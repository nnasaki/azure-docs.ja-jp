---
title: Azure Application Gateway での無効なゲートウェイによる (502) エラーのトラブルシューティング | Microsoft Docs
description: Application Gateway の 502 エラーに関するトラブルシューティングの方法を説明します
services: application-gateway
documentationcenter: na
author: amitsriva
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 1d431ead-d318-47d8-b3ad-9c69f7e08813
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2017
ms.author: amsriva
ms.openlocfilehash: 4eca6a588d2c95189f0ba995b8db195907e9dc39
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2018
ms.locfileid: "34356037"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Application Gateway での無効なゲートウェイによるエラーのトラブルシューティング

Application Gateway の使用時に表示された無効なゲートウェイによる (502) エラーをトラブルシューティングする方法について説明します。

## <a name="overview"></a>概要

Application Gateway の構成後に発生する可能性があるエラーの 1 つに、"サーバー エラー: 502 - Web サーバーがゲートウェイまたはプロキシ サーバーとして動作しているときに、無効な応答を受信しました。" というエラーがあります。 このエラーが発生する主な理由としては、次のことが考えられます。

* NSG、UDR、またはカスタム DNS が、バックエンド プールのメンバーへのアクセスをブロックしている。
* バックエンド VM または仮想マシン スケール セット インスタンスが[既定の正常性プローブに応答していない](#problems-with-default-health-probe.md)。
* [カスタムの正常性プローブの構成](#problems-with-custom-health-probe.md)が無効または不適切である。
* Azure Application Gateway の[バックエンド プールが構成されていないか、空である](#empty-backendaddresspool)。
* [仮想マシン スケール セット内に正常な](#unhealthy-instances-in-backendaddresspool) VM またはインスタンスがない。
* ユーザー要求で[要求がタイムアウトしたか、接続の問題がある](#request-time-out)。

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>ネットワーク セキュリティ グループ、ユーザー定義ルート、またはカスタム DNS に関する問題

### <a name="cause"></a>原因

NSG、UDR、またはカスタム DNS の存在によってバックエンドへのアクセスがブロックされている場合、Application Gateway インスタンスはバックエンド プールに到達できません。この結果、プローブ障害によって 502 エラーが発生します。 NSG/UDR は、Application Gateway サブネットまたはアプリケーション VM がデプロイされているサブネットのいずれかに存在する可能性があります。 同様に、VNET 内のカスタム DNS の存在は、バックエンド プール メンバーで FQDN が使用されているときに、VNET に対して構成された DNS サーバーのユーザーに正しく解決されない場合は、問題を発生させる可能性があります。

### <a name="solution"></a>解決策

次の手順によって、NSG、UDR、および DNS の構成を検証します。
* Application Gateway サブネットに関連付けられている NSG をチェックします。 バックエンドとの通信がブロックされていないことを確認します。
* Application Gateway サブネットに関連付けられている UDR をチェックします。 UDR がトラフィックをバックエンド サブネットから離れるように方向付けていないことを確認します。たとえば、ネットワーク仮想アプライアンスへのルーティングや、ExpressRoute/VPN 経由で Application Gateway にアドバタイズされる既定のルートをチェックします。

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzureRmVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* VM バックエンドとの有効な NSG と ルートをチェックします。

```powershell
Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzureRmEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* VNet 内のカスタム DNS の存在をチェックします。 DNS は、出力内の VNet プロパティの詳細を調べることでチェックできます。

```json
Get-AzureRmVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
存在する場合は、DNS サーバーがバックエンド プール メンバーの FQDN を正しく解決できることを確認します。

## <a name="problems-with-default-health-probe"></a>既定の正常性プローブに関する問題

### <a name="cause"></a>原因

502 エラーは、既定の正常性プローブがバックエンド VM に到達できないことを示している場合もよくあります。 Application Gateway インスタンスがプロビジョニングされると、BackendHttpSetting のプロパティを使用して BackendAddressPool ごとに既定の正常性プローブが自動的に構成されます。 このプローブの設定にはユーザーの入力は必要ありません。 具体的には、負荷分散規則を構成する際に、BackendHttpSetting と BackendAddressPool の間で関連付けが行われます。 既定のプローブがこれらの関連付けごとに構成されます。Application Gateway は、BackendHttpSetting 要素で指定されたポートで BackendAddressPool 内の各インスタンスに対して定期的な正常性チェック接続を開始します。 次の表は、既定の正常性プローブに関連する値の一覧です。

| プローブのプロパティ | 値 | [説明] |
| --- | --- | --- |
| プローブの URL |http://127.0.0.1/ |URL パス |
| 間隔 |30 |プローブの間隔 (秒) |
| タイムアウト |30 |プローブのタイムアウト (秒) |
| 異常のしきい値 |3 |プローブの再試行回数。 プローブの連続失敗回数が異常のしきい値に達すると、バックエンド サーバーは「ダウン」とマークされます。 |

### <a name="solution"></a>解決策

* 既定のサイトが構成されており、127.0.0.1 でリッスンしていることを確認します。
* BackendHttpSetting で 80 以外のポートが指定されている場合、既定のサイトはポート 80 でリッスンするように構成する必要があります。
* http://127.0.0.1:port の呼び出しで、HTTP 結果コード 200 が返されるようにする必要があります。 30 秒のタイムアウト期間内に返されるようにする必要があります。
* 構成済みのポートを開き、構成済みのポートでの送受信トラフィックをブロックするファイアウォール規則または Azure ネットワーク セキュリティ グループが存在しないようにします。
* FQDN またはパブリック IP と共に Azure クラシック VM またはクラウド サービスを使用する場合、対応する [エンドポイント](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json) を必ず開いてください。
* Azure Resource Manager を介して VM を構成しており、Application Gateway がデプロイされた VNet の外側に VM がある場合、 [ネットワーク セキュリティ グループ](../virtual-network/security-overview.md) は、目的のポートにアクセスできるように構成する必要があります。

## <a name="problems-with-custom-health-probe"></a>カスタムの正常性プローブに関する問題

### <a name="cause"></a>原因

カスタムの正常性プローブを使用すれば、既定のプローブ動作の柔軟性を高めることができます。 カスタム プローブを使用する場合、ユーザーは、プローブの間隔、テスト対象の URL とパス、バックエンド プール インスタンスを "異常" とマークするまでの応答の失敗回数を構成することができます。 次のプロパティが追加されます。

| プローブのプロパティ | [説明] |
| --- | --- |
| Name |プローブの名前。 この名前は、バックエンドの HTTP 設定でプローブを参照するために使用されます。 |
| プロトコル |プローブを送信するために使用するプロトコル。 プローブでは、バックエンドの HTTP 設定で定義されているプロトコルを使用します |
| Host |プローブを送信するホスト名。 Application Gateway でマルチサイトを構成する場合にのみ適用可能です。 これは VM ホスト名とは異なります。 |
| パス |プローブの相対パス。 パスは、先頭が '/' である必要があります。 プローブは、\<protocol\>://\<host\>:\<port\>\<path\> に送信されます。 |
| 間隔 |プローブの間隔 (秒)。 2 つの連続するプローブの時間間隔。 |
| タイムアウト |プローブのタイムアウト (秒)。 このタイムアウト期間内に正常な応答が受信されなかった場合は、プローブが「失敗」とマークされます。 |
| 異常のしきい値 |プローブの再試行回数。 プローブの連続失敗回数が異常のしきい値に達すると、バックエンド サーバーは「ダウン」とマークされます。 |

### <a name="solution"></a>解決策

カスタムの正常性プローブが前の表のとおりに正しく構成されていることを確認します。 前のトラブルシューティングの手順を実行したうえで、次のことも必ず行ってください。

* プローブが [ガイド](application-gateway-create-probe-ps.md)のとおりに正しく指定されていることを確認する。
* Application Gateway を単一のサイトで構成する場合、既定ではホスト名は "127.0.0.1" と指定する必要があります (カスタム プローブで構成する場合は除く)。
* http://\<host\>:\<port\>\<path\> の呼び出しで HTTP 結果コード 200 が返されることを確認する。
* 間隔、タイムアウト、異常のしきい値が許容される範囲内であることを確認する。
* HTTPS ブローブを使用する場合は、バックエンド サーバー自体にフォールバック証明書を構成して、バックエンド サーバーが SNI を必要としないようにしてください。

## <a name="request-time-out"></a>要求のタイムアウト

### <a name="cause"></a>原因

ユーザー要求の受信時に、Application Gateway は構成済みの規則をその要求に適用し、要求をバックエンド プール インスタンスにルーティングします。 Application Gateway は一定時間バックエンド インスタンスからの応答を待ちます。この時間間隔は構成できます。 既定では、この間隔は **30 秒**です。 この間隔の間に Application Gateway がバックエンド アプリケーションから応答を受信しない場合、ユーザー要求の結果として 502 エラーが表示されます。

### <a name="solution"></a>解決策

Application Gateway では、ユーザーは BackendHttpSetting でこの設定を構成し、別のプールに適用できます。 バックエンド プールごとに異なる BackendHttpSetting を構成できるため、複数の要求のタイムアウトを構成できます。

```powershell
    New-AzureRmApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>空の BackendAddressPool

### <a name="cause"></a>原因

バックエンド アドレス プールに構成済みの VM または仮想マシン スケール セットがない場合、Application Gateway は顧客の要求をルーティングできず、無効なゲートウェイによるエラーをスローします。

### <a name="solution"></a>解決策

バックエンド アドレス プールを空でない状態にしてください。 これには、PowerShell、CLI、ポータルのいずれかを使用できます。

```powershell
Get-AzureRmApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

前のコマンドレットから取得した出力に、空でないバックエンド アドレス プールが含まれている必要があります。 次の例では、バックエンド VM の FQDN または IP アドレスが構成された 2 つのプールが返されます。 BackendAddressPool のプロビジョニング状態は "Succeeded" である必要があります。

BackendAddressPoolsText:

```json
[{
    "BackendAddresses": [{
        "ipAddress": "10.0.0.10",
        "ipAddress": "10.0.0.11"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool01",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
}, {
    "BackendAddresses": [{
        "Fqdn": "xyx.cloudapp.net",
        "Fqdn": "abc.cloudapp.net"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool02",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
}]
```

## <a name="unhealthy-instances-in-backendaddresspool"></a>BackendAddressPool の異常なインスタンス

### <a name="cause"></a>原因

BackendAddressPool のインスタンスがすべて異常である場合、Application Gateway にユーザー要求のルーティング先となるバックエンドがありません。 これは、バックエンド インスタンスは正常であるものの、必要なアプリケーションがデプロイされていない場合にも生じることがあります。

### <a name="solution"></a>解決策

インスタンスが正常であり、アプリケーションが正しく構成されていることを確認してください。 バックエンド インスタンスが同じ VNet 内に存在する他の VM からの ping に応答できることをチェックします。 パブリック エンドポイントを構成している場合は、Web アプリケーションに対するブラウザーの要求を処理できるようにします。

## <a name="next-steps"></a>次の手順

前の手順で問題を解決できない場合は、 [サポート チケット](https://azure.microsoft.com/support/options/)を開きます。

