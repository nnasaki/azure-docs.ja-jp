---
title: Azure IoT Edge Java チュートリアル | Microsoft Docs
description: このチュートリアルでは、Java コードで IoT Edge モジュールを作成し、Edge デバイスに展開する方法について説明します。
services: iot-edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 9c4ad2275339b3aeb21258041a96840c76732687
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2018
ms.locfileid: "47423463"
---
# <a name="tutorial-develop-a-java-iot-edge-module-and-deploy-to-your-simulated-device"></a>チュートリアル: Java IoT Edge モジュールを開発して、シミュレートされたデバイスに展開する

Azure IoT Edge モジュールを使用して、ビジネス ロジックを実装するコードを IoT Edge デバイスに直接展開できます。 このチュートリアルでは、センサー データをフィルター処理する IoT Edge モジュールを作成および展開する方法について説明します。 [Windows][lnk-tutorial1-win] または [Linux][lnk-tutorial1-lin] のシミュレートされたデバイスに Azure IoT Edge を展開するクイック スタートで作成した、シミュレートされた IoT Edge デバイスを使用します。 このチュートリアルでは、以下の内容を学習します。    

> [!div class="checklist"]
> * Visual Studio Code を使用して、Azure IoT Edge maven テンプレート パッケージと Azure IoT Java デバイス SDK に基づく IoT Edge Java モジュールを作成する。
> * Visual Studio Code と Docker を使用して Docker イメージを作成し、レジストリに発行する。
> * モジュールを IoT Edge デバイスに展開する。
> * 生成されたデータを表示する。


このチュートリアルで作成する IoT Edge モジュールは、デバイスによって生成される温度データをフィルター処理します。 これは、指定されたしきい値を温度が上回っているときにのみ、メッセージを上流に送信します。 エッジでのこの種の分析は、クラウドに送信および保存されるデータ量を削減するうえで役に立ちます。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>前提条件

Azure IoT Edge デバイス:

* [Linux](quickstart-linux.md) のクイック スタートに記載された手順に従って開発マシンまたは仮想マシンをエッジ デバイスとして使用できます。
* IoT Edge 用 Java モジュールは、Windows デバイスをサポートしていません。

クラウド リソース:

* Azure の Free レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。 

開発リソース:

* [Visual Studio Code](https://code.visualstudio.com/)。 
* Visual Studio Code 向け [Java 拡張機能パック](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)。
* Visual Studio Code 用の [Azure IoT Edge 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。 
* [Java SE Development Kit 10](http://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK インストールを指すように [`JAVA_HOME` 環境変数を設定](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/)します)。
* [Maven](https://maven.apache.org/)
* [Docker CE](https://docs.docker.com/install/)
   * Windows デバイス上で開発している場合は、Docker が [Linux コンテナーを使用するように構成](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)されていることを確認してください。 


## <a name="create-a-container-registry"></a>コンテナー レジストリの作成
このチュートリアルでは、VS Code 用の Azure IoT Edge 拡張機能を使用してモジュールをビルドし、ファイルから**コンテナー イメージ**を作成します。 その後、このイメージを**レジストリ**にプッシュし、格納および管理します。 最後に、レジストリからイメージを展開し、IoT Edge デバイスで実行します。  

このチュートリアルでは、Docker と互換性のある任意のレジストリをご利用いただけます。 クラウドで使用できる 2 つの一般的な Docker レジストリ サービスは、[Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) と [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) です。 このチュートリアルでは、Azure Container Registry を使用します。 

1. [Azure Portal](https://portal.azure.com) で、**[リソースの作成]** > **[コンテナー]** > **[Azure Container Registry]** の順に選択します。
2. レジストリに名前を付けて、サブスクリプション、リソース グループを選択し、SKU を **[Basic]** に設定します。 
3. **作成**を選択します。
4. コンテナー レジストリが作成されたら、その場所を参照し、**[アクセス キー]** を選択します。 
5. **[管理者ユーザー]** を **[有効]** に切り替えます。
6. **ログイン サーバー**、**ユーザー名**、および**パスワード**の値をコピーします。 これらの値は、後ほどこのチュートリアルで、レジストリに Docker イメージを発行するときと、Azure IoT Edge ランタイムにレジストリの資格情報を追加するときに使用します。 

## <a name="create-an-iot-edge-module-project"></a>IoT Edge モジュール プロジェクトを作成する
以下の手順では、Visual Studio Code と Azure IoT Edge 拡張機能を使用して、Azure IoT Edge maven テンプレート パッケージと Azure IoT Java デバイス SDK に基づく IoT Edge モジュール プロジェクトを作成します。

### <a name="create-a-new-solution"></a>新しいソリューションの作成

独自のコードでカスタマイズできる Java ソリューション テンプレートを作成します。 

1. Visual Studio Code で、**[表示]** > **[コマンド パレット]** を選択して、VS Code コマンド パレットを開きます。 

2. コマンド パレットで、**Azure: Sign in** コマンドを入力して実行し、指示に従って Azure アカウントにサインインします。 既にサインインしている場合、この手順は省略できます。

3. コマンド パレットで、**Azure IoT Edge: New IoT Edge solution** コマンドを入力して実行します。 コマンド パレットで、次の情報を指定してソリューションを作成します。 

   1. ソリューションの作成先フォルダーを選択します。 
   2. ソリューションの名前を指定するか、既定の **EdgeSolution** をそのまま使用します。
   3. モジュール テンプレートとして **Java Module** を選択します。 
   4. groupId の値を指定するか、既定値の **com.edgemodule** をそのまま使用します。
   5. 既定のモジュール名を **JavaModule** に置き換えます。 
   6. 前のセクションで作成した Azure Container Registry を、最初のモジュールのイメージ リポジトリとして指定します。 **localhost:5000** を、コピーしたログイン サーバーの値に置き換えます。 最終的には、\<registry name\>.azurecr.io/javamodule のような文字列になります。


Java モジュールを初めて作成する場合は、maven パッケージのダウンロードに数分かかる場合があります。 その後、VS Code ウィンドウによって、IoT Edge ソリューション ワークスペースが読み込まれます。 ソリューション ワークスペースには、最上位の 5 つのコンポーネントが含まれています。 このチュートリアルでは、**\.vscode** フォルダーまたは **\.gitignore** ファイルは編集しません。 **modules** フォルダーには、モジュール用の Java コードと、モジュールをコンテナー イメージとして構築するための Dockerfiles が含まれています。 **\.env** ファイルには、コンテナー レジストリの資格情報が格納されています。 **deployment.template.json** ファイルには、デバイスにモジュールをデプロイするために IoT Edge ランタイムが使用する情報が含まれています。 

ソリューションの作成時にコンテナー レジストリを指定せず、既定値の localhost:5000 のままにすると、\.env ファイルは作成されません。 

### <a name="add-your-registry-credentials"></a>レジストリ資格情報を追加する

コンテナー レジストリの資格情報は、環境ファイルに格納され、IoT Edge ランタイムと共有されます。 ランタイムでご自身のプライベート イメージを IoT Edge デバイスにプルするとき、ランタイムではこれらの資格情報が必要になります。 

1. VS Code エクスプローラーで、.env ファイルを開きます。 
2. ご自身の Azure コンテナー レジストリからコピーした**ユーザー名**と**パスワード**の値を使用して、フィールドを更新します。 
3. このファイルを保存します。 

### <a name="update-the-module-with-custom-code"></a>カスタム コードでモジュールを更新する

1. VS Code エクスプローラーで、**[modules]** > **[JavaModule]** > **[src]** > **[main]** > **[java]** > **[com]** > **[edgemodule]** > **[App.java]** の順に開きます。

5. 新しい参照先クラスをインポートするための次のコードをファイルの先頭に追加します。

    ```java
    import java.io.StringReader;
    import java.util.concurrent.atomic.AtomicLong;
    import java.util.HashMap;
    import java.util.Map;
    
    import javax.json.Json;
    import javax.json.JsonObject;
    import javax.json.JsonReader;
    
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Pair;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Property;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.TwinPropertyCallBack;
    ```

5. **App** クラスに次の定義を追加します。 この変数により、データが IoT Hub に送信される基準値が設定されます。データは、測定温度がこの値を超えると送信されます。 

    ```java
    private static final String TEMP_THRESHOLD = "TemperatureThreshold";
    private static AtomicLong tempThreshold = new AtomicLong(25);
    ```

7. **MessageCallbackMqtt** の実行メソッドを次のコードに置き換えます。 このメソッドは、モジュールが IoT Edge ハブから MQTT メッセージを受け取るたびに呼び出されます。 これにより、モジュール ツインで設定されているしきい値を下回る温度を報告するメッセージは除外されます。

    ```java
        private int counter = 0;
       @Override
        public IotHubMessageResult execute(Message msg, Object context) {
            this.counter += 1;
 
            String msgString = new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET);
            System.out.println(
                   String.format("Received message %d: %s",
                            this.counter, msgString));
            if (context instanceof ModuleClient) {
                try (JsonReader jsonReader = Json.createReader(new StringReader(msgString))) {
                    final JsonObject msgObject = jsonReader.readObject();
                    double temperature = msgObject.getJsonObject("machine").getJsonNumber("temperature").doubleValue();
                    long threshold = App.tempThreshold.get();
                    if (temperature >= threshold) {
                        ModuleClient client = (ModuleClient) context;
                        System.out.println(
                            String.format("Temperature above threshold %d. Sending message: %s",
                            threshold, msgString));
                        client.sendEventAsync(msg, eventCallback, msg, App.OUTPUT_NAME);
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            return IotHubMessageResult.COMPLETE;
        }
    ```

8. **App** クラスに次の 2 つの静的内部クラスを追加します。 これらのクラスは、モジュール ツインから対象プロパティの更新を受け取り、それに合わせて **tempThreshold** 変数を更新します。 すべてのモジュールに独自のモジュール ツインがあり、これにより、モジュール内で実行されているコードをクラウドから直接構成できます。

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
 
    protected static class OnProperty implements TwinPropertyCallBack {
        @Override
        public void TwinPropertyCallBack(Property property, Object context) {
            if (!property.getIsReported()) {
                if (property.getKey().equals(App.TEMP_THRESHOLD)) {
                    try {
                        long threshold = Math.round((double) property.getValue());
                        App.tempThreshold.set(threshold);
                    } catch (Exception e) {
                        System.out.println("Faile to set TemperatureThread with exception");
                        e.printStackTrace();
                    }
                }
            }
        }
    }
    ```

9. **client.open()** の後にある **main** メソッドに、モジュール ツインの更新をサブスクライブするための次の行を追加します。

    ```java
    client.startTwin(new DeviceTwinStatusCallBack(), null, new OnProperty(), null);
    Map<Property, Pair<TwinPropertyCallBack, Object>> onDesiredPropertyChange = new HashMap<Property, Pair<TwinPropertyCallBack, Object>>() {
        {
            put(new Property(App.TEMP_THRESHOLD, null), new Pair<TwinPropertyCallBack, Object>(new OnProperty(), null));
        }
    };
    client.subscribeToTwinDesiredProperties(onDesiredPropertyChange);
    client.getTwin();
    ```

11. このファイルを保存します。

## <a name="build-your-iot-edge-solution"></a>IoT Edge ソリューションのビルド

前のセクションでは、IoT Edge ソリューションを作成し、**JavaModule** にコードを追加しました。これにより、許容されるしきい値をマシンの温度が下回ることを報告するメッセージが除外されます。 次は、ソリューションをコンテナー イメージとしてビルドして、それをコンテナー レジストリにプッシュする必要があります。 

1. Visual Studio Code 統合ターミナルで次のコマンドを入力して、Docker にサインインします。 これで、必要なモジュール イメージを Azure Container Registry にプッシュすることができます。
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   最初のセクションで Azure Container Registry からコピーしたユーザー名、パスワード、ログイン サーバーを使用します。 または、Azure portal でご自身のレジストリの **[アクセス キー]** セクションからこれらの値を取得することもできます。

2. VS Code エクスプローラーで、IoT Edge ソリューション ワークスペースの deployment.template.json ファイルを開きます。 このファイルは、**tempSensor** と **JavaModule** の 2 つのモジュールを配置するように **$edgeAgent** に指示します。 **JavaModule.image** 値は、Linux amd64 バージョンのイメージに設定されます。 IoT Edge デバイスのアーキテクチャが arm32v7 の場合は、イメージのバージョンを **arm32v7** に変更します。 

   テンプレートで、モジュール名が既定の **SampleModule** という名前ではなく、IoT Edge ソリューションの作成時に変更した正しい名前になっていることを確認します。

   配置マニフェストの詳細については、[IoT Edge モジュールを使用、構成、再利用する方法の確認](module-composition.md)に関するページをご覧ください。

3. Docker レジストリの資格情報は、deployment.template.json ファイルの **registryCredentials** セクションに格納されています。 実際のユーザー名とパスワードの組み合わせは、.env ファイル (Git では無視されます) に格納されます。  

4. **JavaModule** モジュール ツインを配置マニフェストに追加します。 次の JSON コンテンツを **moduleContent** セクションの下部、**$edgeHub** モジュール ツインの後に挿入します。 
    ```json
        "JavaModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

4. このファイルを保存します。

5. VS Code エクスプローラーで、deployment.template.json ファイルを右クリックし、**[Build and Push IoT Edge solution]\(IoT Edge ソリューションのビルドとプッシュ\)** を選択します。 

ソリューションのビルドを指示すると、最初に Visual Studio Code によって配置テンプレートの情報が読み取られて、**config** という名前の新しいフォルダーに deployment.json ファイルが生成されます。次に、`docker build` と `docker push` の 2 つのコマンドが統合ターミナルで実行されます。 これらの 2 つのコマンドによって、コードがビルドされ、Java アプリがコンテナー化されたうえで、ソリューションを初期化したときに指定したコンテナー レジストリにコードがプッシュされます。 

タグを含む完全なコンテナー イメージ アドレスは、VS Code 統合ターミナルで確認できます。 イメージ アドレスは、\<リポジトリ\>:\<バージョン\>-\<プラットフォーム\> の形式で、module.json ファイルの情報から作成されます。 このチュートリアルでは、registryname.azurecr.io/javamodule:0.0.1-amd64 になります。

## <a name="deploy-and-run-the-solution"></a>ソリューションの配置と実行

IoT Edge デバイスの設定に使用したクイック スタートの記事では、Azure portal を使用してモジュールをデプロイしました。 Visual Studio Code 用の Azure IoT Toolkit 拡張機能を使用して、モジュールをデプロイすることもできます。 シナリオ用の配置マニフェストである **deployment.json** ファイルは、既に用意されています。 ここで行う必要があるのは、デプロイを受け取るデバイスの選択だけです。

1. VS Code コマンド パレットで、**[Azure IoT Hub: Select IoT Hub]\(Azure IoT Hub: IoT ハブの選択\)** を実行します。 

2. 構成する IoT Edge デバイスが含まれているサブスクリプションと IoT ハブを選択します。 

3. VS Code エクスプローラーで、**[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを展開します。 

4. IoT Edge デバイスの名前を右クリックして、**[Create Deployment for Single Device]\(単一デバイスのデプロイの作成\)** を選択します。 

   ![単一デバイスのデプロイを作成する](./media/tutorial-java-module/create-deployment.png)

5. **config**フォルダーで **deployment.json** ファイルを選択し、**[Select Edge Deployment Manifest]\(Edge 配置マニフェストの選択\)** をクリックします。 deployment.template.json ファイルは使用しないでください。 

6. 更新ボタンをクリックします。 新しい **JavaModule** が、**TempSensor** モジュール、**$edgeAgent** および **$edgeHub** と一緒に実行されていることが表示されます。  

## <a name="view-generated-data"></a>生成されたデータを表示する

1. IoT Hub に到着するデータを監視するには、省略記号 (**...**) を選択し、**[Start Monitoring D2C Messages]\(D2C メッセージの監視を開始\)** を選択します。
2. 特定のデバイスの D2C のメッセージを監視するには、一覧でデバイスを右クリックし、**[Start Monitoring D2C Messages]\(D2C メッセージの監視を開始\)** を選択します。
3. データの監視を停止するには、コマンド パレットで **Azure IoT Hub: Stop monitoring D2C message** コマンドを実行します。 
4. モジュール ツインを表示または更新するには、一覧でモジュールを右クリックし、**[Edit module twin]\(モジュール ツインの編集\)** を選択します。 モジュール ツインを更新するには、ツインの JSON ファイルを保存し、編集領域を右クリックして、**[Update Module Twin]\(モジュール ツインの更新\)** を選択します。
5. Docker のログを表示するために、[Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) for VS Code をインストールします。 ローカルで実行されているモジュールを Docker エクスプローラーで確認できます。 コンテキスト メニューで、**[ログを表示する]** を選択し、統合ターミナルで表示します。
 
## <a name="clean-up-resources"></a>リソースのクリーンアップ 

次の推奨記事に進む場合は、作成したリソースおよび構成を維持して、再利用することができます。 また、同じ IoT Edge デバイスをテスト デバイスとして使用し続けることもできます。 

それ以外の場合は、課金されないようにするために、ローカル構成と、この記事で作成した Azure リソースを削除してもかまいません。 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>次の手順

このチュートリアルでは、IoT Edge デバイスによって生成された生データをフィルター処理するコードを含む IoT Edge モジュールを作成しました。 引き続き次のチュートリアルに進み、Azure IoT Edge が、エッジでデータをビジネス上の分析情報に変えるうえで、どのように役立つかを確認できます。

> [!div class="nextstepaction"]
> [SQL Server データベースを使用してエッジでデータを格納する](tutorial-store-data-sql-server.md)

<!-- Links -->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
