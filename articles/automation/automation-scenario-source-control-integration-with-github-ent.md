---
title: Azure Automation ソース管理と GitHub Enterprise の統合
description: GitHub Enterprise との統合を構成して Automation Runbook のソース管理を実現する方法の詳細について説明します。
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 09/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d987e7d1e47e2e88c2f615e1d1bb5446c575ef47
ms.sourcegitcommit: 4edf9354a00bb63082c3b844b979165b64f46286
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2018
ms.locfileid: "48785200"
---
# <a name="azure-automation-scenario---automation-source-control-integration-with-github-enterprise"></a>Azure Automation のシナリオ - Automation ソース管理と GitHub Enterprise の統合

現在、Automation ではソース管理の統合がサポートされており、Automation アカウントの Runbook を GitHub のソース管理リポジトリに関連付けることができます。 しかし、DevOps のプラクティスをサポートするために [GitHub Enterprise](https://enterprise.github.com/home) をデプロイしている顧客は、ビジネス プロセスとサービス管理業務を自動化するために開発した Runbook のライフサイクルの管理にも GitHub Enterprise を使用したいと考えています。

このシナリオでは、データ センター内に、Azure Resource Manager モジュールと Git ツールがインストールされ Hybrid Runbook Worker として構成済みの Windows コンピューターがあります。 ハイブリッド worker コンピューターには、ローカル Git リポジトリの複製があります。 Hybrid Worker で Runbook が実行されると、Git ディレクトリが同期されて、Runbook ファイルの内容が Automation アカウントにインポートされます。

この記事では、Azure Automation 環境でこの構成をセットアップする方法について説明します。 まず、セキュリティ資格情報を使用して、Automation、このシナリオをサポートするために必要な Runbook、およびデータ センター内の Hybrid Runbook Worker のデプロイを構成して、Runbook を実行し GitHub Enterprise リポジトリにアクセスして、Runbook を Automation アカウントと同期します。

## <a name="getting-the-scenario"></a>シナリオの取得

このシナリオは、Azure Portal の [Runbook ギャラリー](automation-runbook-gallery.md)から直接インポート、または [PowerShell ギャラリー](https://www.powershellgallery.com)からダウンロードできる 2 つの PowerShell Runbook で構成されています。

### <a name="runbooks"></a>Runbooks

Runbook | 説明|
--------|------------|
Export-RunAsCertificateToHybridWorker | この Runbook は、ハイブリッド worker の Runbook を Azure で認証して Runbook を Automation アカウントにインポートできるように、Automation アカウントからハイブリッド worker に RunAs 証明書をエクスポートします。|
Sync-LocalGitFolderToAutomationAccount | この Runbook は、ハイブリッド コンピューター上のローカル Git フォルダーを同期してから、Runbook ファイル (*.ps1) を Automation アカウントにインポートします。|

### <a name="credentials"></a>資格情報

資格情報 | 説明|
-----------|------------|
GitHRWCredential | ハイブリッド worker へのアクセス許可を持つユーザーのユーザー名とパスワードを格納する、このシナリオで作成する資格情報資産です。|

## <a name="installing-and-configuring-this-scenario"></a>このシナリオのインストールと構成

### <a name="prerequisites"></a>前提条件

1. Sync-LocalGitFolderToAutomationAccount Runbook で、[Azure 実行アカウント](automation-sec-configure-azure-runas-account.md)を使って認証します。

2. Azure Automation ソリューションが有効化および構成された Log Analytics ワークスペースも必要です。 このシナリオのインストールと構成に使用する Automation アカウントに関連付けられた OMS ワークスペースがない場合は、Hybrid Runbook Worker から **New-OnPremiseHybridWorker.ps1** スクリプトを実行するとこのワークスペースが自動で作成および構成されます。

    > [!NOTE]
    > Automation と Log Analytics との統合は、現在、**オーストラリア南東部**、**米国東部 2**、**東南アジア**、**西ヨーロッパ**のリージョンでのみサポートされています。

3. 専用の Hybrid Runbook Worker として機能するコンピューター。このコンピューターでは GitHub ソフトウェアをホストし、Runbook ファイル (*runbook*.ps1) をファイル システム上のソース ディレクトリに保持して、GitHub と Automation アカウント間で同期します。

### <a name="import-and-publish-the-runbooks"></a>Runbook をインポートして発行する

Azure Portal で Automation アカウントの Runbook ギャラリーから "*Export-RunAsCertificateToHybridWorker*" Runbook と "*Sync-LocalGitFolderToAutomationAccount*" Runbook をインポートするには、[Runbook ギャラリーからの Runbook のインポート](automation-runbook-gallery.md#to-import-a-runbook-from-the-runbook-gallery-with-the-azure-portal)に関するページの手順に従ってください。 Runbook が Automation アカウントに正常にインポートされたら、Runbook を発行します。

### <a name="deploy-and-configure-hybrid-runbook-worker"></a>Hybrid Runbook Worker をデプロイして構成する

データ センターに Hybrid Runbook Worker をデプロイしていない場合は、Azure Automation の Hybrid Runbook Worker の [Windows](automation-windows-hrw-install.md#automated-deployment) または [Linux](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker) に対するインストールと構成の自動化に関するセクションで要件を確認し、このセクションの手順に従って自動インストール手順を実行する必要があります。 Hybrid Worker をコンピューターに正常にインストールしたら、次の手順を実行して、このシナリオをサポートする構成を完了します。

1. ローカル管理者の権限を持つアカウントを使用して Hybrid Runbook Worker ロールをホスティングするコンピューターにサインオンし、Git Runbook ファイルを保存するディレクトリを作成します。 このディレクトリに内部 Git リポジトリを複製します。
1. 実行アカウントをまだ作成していないか、この目的のために専用のアカウントを新たに作成する場合は、Azure Portal で Automation アカウントに移動し、お使いの Automation アカウントを選択して、ハイブリッド worker へのアクセス許可を持つユーザーのユーザー名とパスワードが含まれる[資格情報資産](automation-credentials.md)を作成します。
1. Automation アカウントから、**Export-RunAsCertificateToHybridWorker** [Runbook を編集](automation-edit-textual-runbook.md)して、*$Password* 変数の値を強力なパスワードに変更します。  値を変更したら、**[発行]** をクリックしてドラフト バージョンの Runbook を発行します。
1. **Export-RunAsCertificateToHybridWorker** Runbook を開始し、**[Runbook の開始]** ブレードの **[実行設定]** オプションで **[Hybrid Worker]** オプションを選択します。また、ドロップダウン リストで、このシナリオ用にあらかじめ作成した Hybrid Worker グループを選択します。

    これによって、証明書がハイブリッド worker にエクスポートされ、Azure リソースの管理 (このシナリオの場合は Automation アカウントへの Runbook のインポート) のために、ハイブリッド worker の Runbook が実行者接続を使用して Azure で認証できるようになります。

1. Automation アカウントから、あらかじめ作成したハイブリッド worker グループを選び、ハイブリッド worker グループの [RunAs アカウントを指定](automation-hrw-run-runbooks.md#runas-account)して、作成した資格情報資産を選びます。 これにより、Sync Runbook で Git コマンドを実行できるようになります。 
1. **Sync-LocalGitFolderToAutomationAccount** Runbook を開始し、以下の必要な入力パラメーター値を入力します。**[Runbook の開始]** ブレードで、**[実行設定]** オプションの **[Hybrid Worker]** オプションを選択し、ドロップダウン リストで、このシナリオ用にあらかじめ作成した Hybrid Worker グループを選択します。

   * *ResourceGroup* - Automation アカウントと関連付けられたリソース グループの名前
   * *AutomationAccountName* - Automation アカウントの名前
   * *GitPath* - Git で最新の変更内容の取得先に設定する Hybrid Runbook Worker 上のローカル フォルダーまたはファイル

    これにより、ハイブリッド worker コンピューター上のローカル Git フォルダーが同期され、ソース ディレクトリから Automation アカウントに .ps1 ファイルがインポートされます。

1. Automation アカウントで **[Runbook]** ブレードからこの Runbook を選択し、**[ジョブ]** タイルを選択して、Runbook についてジョブ概要の詳細情報を表示します。 **[All logs] \(すべてのログ)** タイルを選択して詳細なログ ストリームを確認し、正常に完了したことを確認します。

## <a name="next-steps"></a>次の手順

* Runbook の種類とそれらの利点や制限事項の詳細については、「 [Azure Automation の Runbook の種類](automation-runbook-types.md)
* PowerShell スクリプトのサポート機能の詳細については、「 [Native PowerShell Script Support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
