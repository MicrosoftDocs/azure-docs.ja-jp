---
title: チュートリアル - Azure IoT Edge を使用したカスタム Java モジュールのチュートリアル
description: このチュートリアルでは、Java コードで IoT Edge モジュールを作成し、Edge デバイスに展開する方法について説明します。
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom:
- mvc
- mqtt
- devx-track-java
ms.openlocfilehash: 3f24f38db7704557894d866b789890763f9e1316
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103463257"
---
# <a name="tutorial-develop-a-java-iot-edge-module-using-linux-containers"></a>チュートリアル: Linux コンテナーを使用して Java の IoT Edge モジュールを開発する

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge モジュールを使用して、ビジネス ロジックを実装するコードを IoT Edge デバイスに直接展開できます。 このチュートリアルでは、センサー データをフィルター処理する IoT Edge モジュールを作成および展開する方法について説明します。 シミュレートされたデバイスに Azure IoT Edge をデプロイするクイック スタートの記事で作成した、シミュレートされた IoT Edge デバイスを使用します。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * Visual Studio Code を使用して、Azure IoT Edge maven テンプレート パッケージと Azure IoT Java デバイス SDK に基づく IoT Edge Java モジュールを作成する。
> * Visual Studio Code と Docker を使用して Docker イメージを作成し、レジストリに発行する。
> * モジュールを IoT Edge デバイスに展開する。
> * 生成されたデータを表示する。

このチュートリアルで作成する IoT Edge モジュールは、デバイスによって生成される温度データをフィルター処理します。 これは、指定されたしきい値を温度が上回っているときにのみ、メッセージを上流に送信します。 エッジでのこの種の分析は、クラウドに送信および保存されるデータ量を削減するうえで役に立ちます。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、**Visual Studio Code** を使用して **Java** でモジュールを開発し、それを IoT Edge デバイスにデプロイする方法について説明します。 IoT Edge では、Windows コンテナーとして構築された Java モジュールはサポートされていません。

次の表を使用し、Java モジュールを開発してデプロイする際のオプションをご確認ください。

| Java | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Linux AMD64 の Java モジュールに VS Code を使用する](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Linux ARM32 の Java モジュールに VS Code を使用する](./media/tutorial-c-module/green-check.png) |  |

このチュートリアルを開始する前に、前のチュートリアルを完了して、Linux コンテナー開発用の開発環境を設定しておく必要があります。[Linux デバイス用の IoT Edge モジュールを開発する](tutorial-develop-for-linux.md)。 これらのチュートリアルのいずれかを完了すると、次の前提条件が満たされます。

* Azure の Free レベルまたは Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。
* Azure IoT Edge を実行しているデバイス。 クイックスタートを使用して、[Linux デバイス](quickstart-linux.md)または [Windows デバイス](quickstart.md)を設定できます。
* コンテナー レジストリ ([Azure Container Registry](../container-registry/index.yml) など)。
* [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) を使用して構成された [Visual Studio Code](https://code.visualstudio.com/)。
* Linux コンテナーを実行するように構成された [Docker CE](https://docs.docker.com/install/)。

Java で IoT Edge モジュールを開発するには、開発マシンに次の追加の前提条件をインストールします。

* Visual Studio Code 向け [Java 拡張機能パック](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)。
* [Java SE Development Kit 11](/azure/developer/java/fundamentals/java-jdk-long-term-support) (JDK インストールを指すように [`JAVA_HOME` 環境変数を設定](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/)します)。
* [Maven](https://maven.apache.org/)

   >[!TIP]
   >Java および Maven のインストール プロセスによって、環境変数がシステムに追加されます。 インストールが完了したら、開いている Visual Studio Code ターミナル、Powershell、またはコマンド プロンプトのインスタンスを再起動します。 この手順により、これらのユーティリティがこれ以降、Java コマンドおよび Maven コマンドを認識できるようになります。

## <a name="create-a-module-project"></a>モジュール プロジェクトを作成する

以降の手順では、Azure IoT Edge maven テンプレート パッケージと Azure IoT Java デバイス SDK に基づく IoT Edge モジュール プロジェクトを作成します。 Visual Studio Code と Azure IoT Tools を使用してプロジェクトを作成します。

### <a name="create-a-new-project"></a>新しいプロジェクトを作成する

独自のコードでカスタマイズできる Java ソリューション テンプレートを作成します。

1. Visual Studio Code で、 **[表示]**  >  **[コマンド パレット]** を選択して、VS Code コマンド パレットを開きます。

2. コマンド パレットで、**Azure IoT Edge:New IoT Edge solution** コマンドを入力して実行します。 コマンド パレットに表示されるメッセージに従って、ソリューションを作成します。

   | フィールド | 値 |
   | ----- | ----- |
   | フォルダーの選択 | VS Code によってソリューション ファイルが作成される、開発マシン上の場所を選択します。 |
   | Provide a solution name (ソリューション名の指定) | ソリューションのためにわかりやすい名前を入力するか、既定値の **EdgeSolution** をそのまま使用します。 |
   | Select module template (モジュール テンプレートの選択) | **Java モジュール** を選択します。 |
   | Provide a module name (モジュール名の指定) | モジュールに **JavaModule** という名前を付けます。 |
   | Provide Docker image repository for the module (モジュールの Docker イメージ リポジトリの指定) | イメージ リポジトリには、コンテナー レジストリの名前とコンテナー イメージの名前が含まれます。 前の手順で指定した名前がコンテナー イメージに事前設定されます。 **localhost:5000** を、Azure コンテナー レジストリの **ログイン サーバー** の値に置き換えます。 Azure portal で、コンテナー レジストリの概要ページからログイン サーバーを取得できます。 <br><br>最終的なイメージ リポジトリは、\<registry name\>.azurecr.io/javamodule のようになります。 |
   | Provide value for groupId (groupId の値の指定) | グループ ID の値を入力するか、既定値の **com.edgemodule** をそのまま使用します。 |

   ![Docker イメージ リポジトリを指定する](./media/tutorial-java-module/repository.png)

Java モジュールを初めて作成する場合は、maven パッケージのダウンロードに数分かかる場合があります。 ソリューションの準備が整うと、VS Code ウィンドウによって、IoT Edge ソリューション ワークスペースが読み込まれます。 ソリューション ワークスペースには、最上位の 5 つのコンポーネントが含まれています。

* **modules** フォルダーには、対象のモジュールの Java コードと、対象のモジュールをコンテナー イメージとして構築するための Docker ファイルが含まれています。
* **\.env** ファイルには、コンテナー レジストリの資格情報が格納されています。
* **deployment.template.json** ファイルには、デバイスにモジュールをデプロイするために IoT Edge ランタイムが使用する情報が含まれています。
* **deployment.debug.template.json** ファイルには、モジュールのデバッグ バージョンが含まれています。
* このチュートリアルでは、 **\.vscode** フォルダーまたは **\.gitignore** ファイルは編集しません。

ソリューションの作成時にコンテナー レジストリを指定せず、既定値の localhost:5000 のままにすると、\.env ファイルは作成されません。

### <a name="add-your-registry-credentials"></a>レジストリ資格情報を追加する

コンテナー レジストリの資格情報は、環境ファイルに格納され、IoT Edge ランタイムと共有されます。 ランタイムでご自身のプライベート イメージを IoT Edge デバイスにプルするとき、ランタイムではこれらの資格情報が必要になります。

IoT Edge 拡張機能は、Azure からコンテナー レジストリの資格情報をプルし、それらを環境ファイルに取り込もうとします。 資格情報が既に含まれているかどうかを確認します。 含まれていない場合は、次のようにして追加します。

1. VS Code エクスプローラーで、.env ファイルを開きます。
2. ご自身の Azure コンテナー レジストリからコピーした **ユーザー名** と **パスワード** の値を使用して、フィールドを更新します。
3. このファイルを保存します。

### <a name="select-your-target-architecture"></a>ターゲット アーキテクチャを選択する

現在、Visual Studio Code では、Linux AMD64 および Linux ARM32v7 デバイス用の Java モジュールを開発できます。 ソリューションごとにターゲットとするアーキテクチャを選択する必要があります。これは、アーキテクチャの種類によって、コンテナーのビルド方法と実行方法が異なるためです。 既定値は Linux AMD64 です。

1. コマンド パレットを開き、次を検索します: 「**Azure IoT Edge: Set Default Target Platform for Edge Solution (Azure IoT Edge: Edge ソリューションの既定のターゲット プラットフォームの設定)** 」。または、ウィンドウの下部にあるサイド バーで、ショートカット アイコンを選択します。

2. コマンド パレットで、オプションの一覧からターゲット アーキテクチャを選択します。 このチュートリアルでは、Ubuntu 仮想マシンを IoT Edge デバイスとして使用するため、既定値の **amd64** のままにします。

### <a name="update-the-module-with-custom-code"></a>カスタム コードでモジュールを更新する

1. VS Code エクスプローラーで、 **[modules]**  >  **[JavaModule]**  >  **[src]**  >  **[main]**  >  **[java]**  >  **[com]**  >  **[edgemodule]**  >  **[App.java]** の順に開きます。

2. 新しい参照先クラスをインポートするための次のコードをファイルの先頭に追加します。

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

3. **App** クラスに次の定義を追加します。 この変数は、温度のしきい値を設定します。 測定されたマシンの温度は、この値を超えたときにはじめて IoT Hub にレポートされます。

    ```java
    private static final String TEMP_THRESHOLD = "TemperatureThreshold";
    private static AtomicLong tempThreshold = new AtomicLong(25);
    ```

4. **MessageCallbackMqtt** の実行メソッドを次のコードに置き換えます。 このメソッドは、モジュールが IoT Edge ハブから MQTT メッセージを受け取るたびに呼び出されます。 これにより、モジュール ツインで設定されているしきい値を下回る温度を報告するメッセージは除外されます。

    ```java
    protected static class MessageCallbackMqtt implements MessageCallback {
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
    }
    ```

5. **App** クラスに次の 2 つの静的内部クラスを追加します。 モジュール ツインの desired プロパティが変化すると、これらのクラスによって tempThreshold 変数が更新されます。 すべてのモジュールに独自のモジュール ツインがあり、これにより、モジュール内で実行されているコードをクラウドから直接構成できます。

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

6. **client.open()** の後にある **main** メソッドに、モジュール ツインの更新をサブスクライブするための次の行を追加します。

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

7. App.java ファイルを保存します。

8. VS Code のエクスプローラーで、ご自身の IoT Edge ソリューション ワークスペースの **deployment.template.json** ファイルを開きます。

9. **JavaModule** モジュール ツインを配置マニフェストに追加します。 次の JSON コンテンツを **moduleContent** セクションの下部、 **$edgeHub** モジュール ツインの後に挿入します。

   ```json
     "JavaModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![モジュール ツインをデプロイ テンプレートに追加する](./media/tutorial-java-module/module-twin.png)

10. deployment.template.json ファイルを保存します。

## <a name="build-and-push-your-module"></a>モジュールをビルドしてプッシュする

前のセクションでは、IoT Edge ソリューションを作成し、**JavaModule** にコードを追加しました。これにより、許容される限界をマシンの温度が下回ることをレポートするメッセージが除外されます。 次は、ソリューションをコンテナー イメージとしてビルドして、それをコンテナー レジストリにプッシュします。

1. **[表示]**  >  **[ターミナル]** を選択して、VS Code 統合ターミナルを開きます。

2. ターミナルで次のコマンドを入力して、Docker にサインインします。 自分の Azure コンテナー レジストリのユーザー名、パスワード、ログイン サーバーを使用してサインインします。 これらの値は、Azure portal でご自身のレジストリの **[アクセス キー]** セクションから取得できます。

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   `--password-stdin` の使用を推奨するセキュリティ警告が表示される場合があります。 このベスト プラクティスは、運用環境のシナリオを対象に推奨されていますが、それはこのチュートリアルの範囲外になります。 詳細については、[docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) のリファレンスをご覧ください。

3. VS Code エクスプローラーで、**deployment.template.json** ファイルを右クリックし、 **[Build and Push IoT Edge Solution]\(IoT Edge ソリューションのビルドとプッシュ\)** を選択します。

   ビルドおよびプッシュ コマンドでは、3 つの操作を開始します。 最初に、デプロイ テンプレートと他のソリューション ファイルの情報からビルドされた完全な配置マニフェストを保持する、**config** という新しいフォルダーをソリューション内に作成します。 次に、`docker build` を実行して、お使いのターゲット アーキテクチャ用の適切な Dockerfile に基づいてコンテナー イメージをビルドします。 そして、`docker push` を実行して、イメージ リポジトリをコンテナー レジストリにプッシュします。

   このプロセスは、初回は数分間かかる可能性がありますが、次回これらのコマンドを実行するときは、それより速くなります。

## <a name="deploy-modules-to-device"></a>モジュールをデバイスにデプロイする

Visual Studio Code エクスプローラーと Azure IoT Tools 拡張機能を使用して、モジュール プロジェクトを自分の IoT Edge デバイスにデプロイします。 シナリオ用の配置マニフェストである **deployment.amd64.json** ファイルは、config フォルダーに既に用意されています。 ここで行う必要があるのは、デプロイを受け取るデバイスの選択だけです。

お使いの IoT Edge デバイスが稼働していることを確認します。

1. Visual Studio Code エクスプローラーの **[Azure IoT Hub]** セクションで、 **[デバイス]** を展開して IoT デバイスの一覧を表示します。

2. IoT Edge デバイスの名前を右クリックして、 **[Create Deployment for Single Device]\(単一デバイスのデプロイの作成\)** を選択します。

3. **config** フォルダーで **deployment.amd64.json** ファイルを選択し、 **[Select Edge Deployment Manifest]\(Edge 配置マニフェストの選択\)** をクリックします。 deployment.template.json ファイルは使用しないでください。

4. お使いのデバイスの **[モジュール]** を展開し、デプロイされて実行中のモジュールの一覧を表示します。 更新ボタンをクリックします。 新しい **JavaModule** が、**SimulatedTemperatureSensor** モジュール、 **$edgeAgent** および **$edgeHub** と一緒に実行されていることがわかります。  

    両方のモジュールが開始するまでに数分かかる場合があります。 IoT Edge ランタイムは、新しい配置マニフェストを受け取り、コンテナー ランタイムからモジュール イメージを取得して、それぞれの新しいモジュールを開始する必要があります。

## <a name="view-the-generated-data"></a>生成されたデータを表示する

IoT Edge デバイスに配置マニフェストを適用すると、デバイス上の IoT Edge ランタイムが新しいデプロイ情報を収集し、そこで実行を開始します。 デバイス上で動作しているモジュールのうち、配置マニフェストに含まれていないものはすべて停止されます。 デバイスから不足しているモジュールがあればすべて開始されます。

1. Visual Studio Code のエクスプローラーで、IoT Edge デバイスの名前を右クリックし、 **[Start Monitoring Built-in Event Endpoint]\(組み込みイベント エンドポイントの監視を開始する\)** を選択します。

2. 自分の IoT ハブに到着するメッセージを表示します。 メッセージが到着するまでに時間がかかる場合があります。 IoT Edge デバイスでは、新しいデプロイを受け取って、すべてのモジュールを開始する必要があります。 次に、JavaModule コードに加えられた変更により、マシンの温度が 25 度に達するまで待機してから、メッセージが送信されます。 また、その温度しきい値に達するどのメッセージにも、メッセージ型 **Alert** が追加されます。

## <a name="edit-the-module-twin"></a>モジュール ツインを編集する

配置マニフェストで JavaModule モジュール ツインを使用して、温度のしきい値を 25 度に設定しました。 モジュール ツインを使用することで、モジュール コードを更新することなく機能を変更できます。

1. Visual Studio Code で、自分の IoT Edge デバイスの詳細を展開し、実行中のモジュールを表示します。

2. **[JavaModule]** を右クリックし、 **[Edit module twin]\(モジュール ツインの編集\)** を選択します。

3. 目的のプロパティの中から **TemperatureThreshold** を見つけます。 その値を、新しい温度 (最後にレポートされた温度より 5 度から 10 度高い温度) に変更します。

4. モジュール ツイン ファイルを保存します。

5. モジュール ツイン編集ウィンドウ内の任意の場所を右クリックして、 **[Update module twin]\(モジュール ツインの更新\)** を選択します。

6. 到着する device-to-cloud メッセージを監視します。 新しい温度しきい値に達するまでメッセージが停止するのを確認できるはずです。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次の推奨記事に進む場合は、作成したリソースおよび構成を維持して、再利用することができます。 また、同じ IoT Edge デバイスをテスト デバイスとして使用し続けることもできます。

それ以外の場合は、課金されないようにするために、ローカル構成と、この記事で作成した Azure リソースを削除してもかまいません。

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、お使いの IoT Edge デバイスによって生成された生データをフィルター処理する IoT Edge モジュールを作成しました。

次のチュートリアルに進んで、Azure のクラウド サービスをデプロイしてエッジでデータの処理と分析を行ううえで Azure IoT Edge がどのように役立つかを学習します。

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
