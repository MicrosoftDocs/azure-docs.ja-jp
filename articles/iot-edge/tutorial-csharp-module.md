---
title: 'チュートリアル: Linux 用の C# モジュールを開発する - Azure IoT Edge| Microsoft Docs'
description: このチュートリアルでは、C# コードを使って IoT Edge モジュールを作成し、Linux IoT Edge デバイスにデプロイする方法について説明します。
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 7714d065d8ac449d10d9b022005e7799f8280bda
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104449"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-linux-devices"></a>チュートリアル:Linux デバイス用の C# IoT Edge モジュールを開発する

Visual Studio Code を使用して C# コードを開発し、Azure IoT Edge が動作している Linux デバイスにデプロイします。 

Azure IoT Edge モジュールを使用して、ビジネス ロジックを実装するコードを IoT Edge デバイスに直接展開できます。 このチュートリアルでは、センサー データをフィルター処理する IoT Edge モジュールを作成および展開する方法について説明します。 [Windows](quickstart.md) または [Linux](quickstart-linux.md) のシミュレートされたデバイスに Azure IoT Edge をデプロイするクイック スタートで作成した、シミュレートされた IoT Edge デバイスを使用します。 このチュートリアルでは、以下の内容を学習します。    

> [!div class="checklist"]
> * Visual Studio Code を使用して .NET Core 2.1 SDK に基づく IoT Edge モジュールを作成する。
> * Visual Studio Code と Docker を使用して Docker イメージを作成し、レジストリに発行する。
> * モジュールを IoT Edge デバイスに展開する。
> * 生成されたデータを表示する。

このチュートリアルで作成する IoT Edge モジュールは、デバイスによって生成される温度データをフィルター処理します。 これは、指定されたしきい値を温度が上回っているときにのみ、メッセージを上流に送信します。 エッジでのこの種の分析は、クラウドに送信および保存されるデータ量を削減するうえで役に立ちます。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>ソリューション スコープ

このチュートリアルでは、**Visual Studio Code** を使用して **C#** でモジュールを開発し、それを **Linux デバイス**にデプロイする方法について説明します。 Windows デバイス用のモジュールを開発する場合は、「[Windows デバイス用の C# IoT Edge モジュールを開発する](tutorial-csharp-module-windows.md)」を参照してください。

次の表を使用し、C# モジュールを開発して Linux にデプロイする際のオプションをご確認ください。 

| C# | Visual Studio Code | Visual Studio | 
| -- | ------------------ | ------------- |
| **Linux AMD64** | ![VS Code の LinuxAMD64 用 C# モジュール](./media/tutorial-c-module/green-check.png) | ![Visual Studio の LinuxAMD64 用 C# モジュール](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![VS Code の LinuxARM32 用 C# モジュール](./media/tutorial-c-module/green-check.png) | ![Visual Studio の LinuxARM64 用 C# モジュール](./media/tutorial-c-module/green-check.png) |

>[!NOTE]
>Linux ARM64 デバイスのサポートは、[パブリック プレビュー](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)でご利用いただけます。 詳細については、「[Visual Studio Code で ARM64 IoT Edge モジュールを開発してデバッグする (プレビュー)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、[Linux デバイス用の IoT Edge モジュールの開発](tutorial-develop-for-linux.md)に関する前のチュートリアルを完了して、開発環境を設定する必要があります。 そのチュートリアルを完了すると、次の前提条件は既に満たしています。 

* Azure の Free レベルまたは Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。
* [Azure IoT Edge が動作している Linux デバイス](quickstart-linux.md)。
* コンテナー レジストリ ([Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) など)。
* [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) を使用して構成された [Visual Studio Code](https://code.visualstudio.com/)。
* Linux コンテナーを実行するように構成された [Docker CE](https://docs.docker.com/install/)。

これらのチュートリアルを完了するには、開発マシンでさらに次の前提条件を整えます。 

* [Visual Studio Code 用の C# (OmniSharp を使用) 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)。

## <a name="create-a-module-project"></a>モジュール プロジェクトを作成する

以下の手順では、Visual Studio Code と Azure IoT Tools 拡張機能を使用して、C# 用の IoT Edge モジュール プロジェクトを作成します。 プロジェクト テンプレートの作成後、新しいコードを追加して、レポートされるプロパティに基づいてモジュールがメッセージを除外するようにします。 

### <a name="create-a-new-project"></a>新しいプロジェクトを作成する

独自のコードでカスタマイズできる C# ソリューション テンプレートを作成します。 

1. Visual Studio Code で、 **[表示]**  >  **[コマンド パレット]** を選択して、VS Code コマンド パレットを開きます。 

2. コマンド パレットで、**Azure: Sign in** コマンドを入力して実行し、手順に従って Azure アカウントにサインインします。 既にサインインしている場合、この手順は省略できます。

3. コマンド パレットで、**Azure IoT Edge: New IoT Edge solution** コマンドを入力して実行します。 コマンド パレットに表示されるメッセージに従って、ソリューションを作成します。

   | フィールド | 値 |
   | ----- | ----- |
   | フォルダーの選択 | VS Code によってソリューション ファイルが作成される、開発マシン上の場所を選択します。 |
   | Provide a solution name (ソリューション名の指定) | ソリューションのためにわかりやすい名前を入力するか、既定値の **EdgeSolution** をそのまま使用します。 |
   | Select module template (モジュール テンプレートの選択) | **C# モジュール**を選択します。 |
   | Provide a module name (モジュール名の指定) | ご自身のモジュールに **CSharpModule** と名前を付けます。 |
   | Provide Docker image repository for the module (モジュールの Docker イメージ リポジトリの指定) | イメージ リポジトリには、コンテナー レジストリの名前とコンテナー イメージの名前が含まれます。 前の手順で指定した名前がコンテナー イメージに事前設定されます。 **localhost:5000** を、Azure コンテナー レジストリのログイン サーバーの値に置き換えます。 Azure portal で、コンテナー レジストリの概要ページからログイン サーバーを取得できます。 <br><br>最終的なイメージ リポジトリは、\<registry name\>.azurecr.io/csharpmodule のようになります。 |
 
   ![Docker イメージ リポジトリを指定する](./media/tutorial-csharp-module/repository.png)


### <a name="add-your-registry-credentials"></a>レジストリ資格情報を追加する

コンテナー レジストリの資格情報は、環境ファイルに格納され、IoT Edge ランタイムと共有されます。 ランタイムでご自身のプライベート イメージを IoT Edge デバイスにプルするとき、ランタイムではこれらの資格情報が必要になります。 自分の Azure コンテナー レジストリの **[アクセス キー]** セクションにある資格情報を使用します。 

1. VS Code エクスプローラーで、 **.env** ファイルを開きます。 
2. 自分の Azure コンテナー レジストリの**ユーザー名**と**パスワード**の値を使用して、フィールドを更新します。 
3. このファイルを保存します。 

### <a name="select-your-target-architecture"></a>ターゲット アーキテクチャを選択する

現在、Visual Studio Code では、Linux AMD64 および Linux ARM32v7 デバイス用の C# モジュールを開発できます。 ソリューションごとにターゲットとするアーキテクチャを選択する必要があります。これは、アーキテクチャの種類によって、コンテナーのビルド方法と実行方法が異なるためです。 既定値は Linux AMD64 です。 

1. コマンド パレットを開き、次を検索します: 「**Azure IoT Edge: Set Default Target Platform for Edge Solution (Azure IoT Edge: Edge ソリューションの既定のターゲット プラットフォームの設定)** 」。または、ウィンドウの下部にあるサイド バーで、ショートカット アイコンを選択します。 

2. コマンド パレットで、オプションの一覧からターゲット アーキテクチャを選択します。 このチュートリアルでは、Ubuntu 仮想マシンを IoT Edge デバイスとして使用するため、既定値の **amd64** のままにします。 

### <a name="update-the-module-with-custom-code"></a>カスタム コードでモジュールを更新する

1. VS Code エクスプローラーで、 **[モジュール]**  >  **[CSharpModule]**  >  **[Program.cs]** の順に開きます。

2. **[CSharpModule]** 名前空間の上部で、後で使用する型として 3 つの **using** ステートメントを追加します。

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. **temperatureThreshold** 変数を **Program** クラスに追加します。 この変数により、データが IoT Hub に送信される基準値が設定されます。データは、測定温度がこの値を超えると送信されます。 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. **MessageBody**、**Machine**、**Ambient** の各クラスを **Program** クラスに追加します。 これらのクラスは、受信メッセージの本文に対して予期されるスキーマを定義します。

    ```csharp
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
        public double temperature {get; set;}
        public double pressure {get; set;}         
    }
    class Ambient
    {
        public double temperature {get; set;}
        public int humidity {get; set;}         
    }
    ```

5. **Init** 関数を探します。 この関数では、**ModuleClient** オブジェクトを作成して構成します。これにより、モジュールはローカルの Azure IoT Edge ランタイムに接続して、メッセージを送受信できます。 **ModuleClient** の作成後、コードによって、モジュール ツインの目的のプロパティから **temperatureThreshold** が読み取られ、 IoT Edge ハブから **input1** エンドポイントを介してメッセージを受信するためのコールバックが登録されます。 **SetInputMessageHandlerAsync** メソッドを新しいメソッドで置き換え、対象プロパティの更新のために **SetDesiredPropertyUpdateCallbackAsync** メソッドを追加します。 この変更を行うには、**Init** メソッドの最後の行を次のコードに置き換えます。

    ```csharp
    // Register a callback for messages that are received by the module.
    // await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read the TemperatureThreshold value from the module twin's desired properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);
    
    // Attach a callback for updates to the module twin's desired properties.
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

    // Register a callback for messages that are received by the module.
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

6. **onDesiredPropertiesUpdate** メソッドを **Program** クラスに追加します。 このメソッドは、モジュール ツインから対象プロパティの更新を受け取り、それに合わせて **temperatureThreshold** 変数を更新します。 すべてのモジュールに独自のモジュール ツインがあり、これにより、モジュール内で実行されているコードをクラウドから直接構成できます。

    ```csharp
    static Task OnDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

7. **PipeMessage** メソッドを **FilterMessages** メソッドに置き換えます。 このメソッドは、モジュールが IoT Edge ハブからメッセージを受け取るたびに呼び出されます。 これにより、モジュール ツインで設定されているしきい値を下回る温度を報告するメッセージは除外されます。 また、**MessageType** プロパティを、値が **Alert** に設定されたメッセージに追加します。 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);
        try
        {
            ModuleClient moduleClient = (ModuleClient)userContext;
            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get the message body.
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                using (var filteredMessage = new Message(messageBytes))
                {
                    foreach (KeyValuePair<string, string> prop in message.Properties)
                    {
                        filteredMessage.Properties.Add(prop.Key, prop.Value);
                    }

                    filteredMessage.Properties.Add("MessageType", "Alert");
                    await moduleClient.SendEventAsync("output1", filteredMessage);
                }
            }

            // Indicate that the message treatment is completed.
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed.
            var moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed.
            ModuleClient moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

8. Program.cs ファイルを保存します。

9. VS Code のエクスプローラーで、ご自身の IoT Edge ソリューション ワークスペースの **deployment.template.json** ファイルを開きます。 

10. **CSharpModule** モジュール ツインを配置マニフェストに追加します。 次の JSON コンテンツを **modulesContent** セクションの下部、 **$edgeHub** モジュール ツインの後に挿入します。 

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![モジュール ツインをデプロイ テンプレートに追加する](./media/tutorial-csharp-module/module-twin.png)

11. deployment.template.json ファイルを保存します。


## <a name="build-and-push-your-module"></a>モジュールをビルドしてプッシュする

前のセクションでは、IoT Edge ソリューションを作成したほか、レポートされるマシン温度が許容範囲内である場合にメッセージを除外するコードを CSharpModule に追加しました。 次は、ソリューションをコンテナー イメージとしてビルドして、それをコンテナー レジストリにプッシュする必要があります。

1. **[表示]**  >  **[ターミナル]** を選択して、VS Code 統合ターミナルを開きます。

1. ターミナルで次のコマンドを入力して、Docker にサインインします。 自分の Azure コンテナー レジストリのユーザー名、パスワード、ログイン サーバーを使用してサインインします。 これらの値は、Azure portal でご自身のレジストリの **[アクセス キー]** セクションから取得できます。
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   `--password-stdin` の使用を推奨するセキュリティ警告が表示される場合があります。 このベスト プラクティスは、運用環境のシナリオを対象に推奨されていますが、それはこのチュートリアルの範囲外になります。 詳細については、[docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) のリファレンスを参照してください。

2. VS Code エクスプローラーで、**deployment.template.json** ファイルを右クリックし、 **[Build and Push IoT Edge solution]\(IoT Edge ソリューションのビルドとプッシュ\)** を選択します。

   ビルドおよびプッシュ コマンドでは、3 つの操作を開始します。 最初に、デプロイ テンプレートと他のソリューション ファイルの情報からビルドされた完全な配置マニフェストを保持する、**config** という新しいフォルダーをソリューション内に作成します。 次に、`docker build` を実行して、お使いのターゲット アーキテクチャ用の適切な Dockerfile に基づいてコンテナー イメージをビルドします。 そして、`docker push` を実行して、イメージ リポジトリをコンテナー レジストリにプッシュします。

## <a name="deploy-and-run-the-solution"></a>ソリューションの配置と実行

Visual Studio Code エクスプローラーと Azure IoT Tools 拡張機能を使用して、モジュール プロジェクトを自分の IoT Edge デバイスにデプロイします。 シナリオ用の配置マニフェストである **deployment.json** ファイルは、config フォルダーに既に用意されています。 ここで行う必要があるのは、デプロイを受け取るデバイスの選択だけです。

お使いの IoT Edge デバイスが稼働していることを確認します。

1. Visual Studio Code エクスプローラーで **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを展開して、IoT デバイスの一覧を確認します。

2. IoT Edge デバイスの名前を右クリックして、 **[Create Deployment for Single Device]\(単一デバイスのデプロイの作成\)** を選択します。 

5. **config**フォルダーで **deployment.json** ファイルを選択し、 **[Select Edge Deployment Manifest]\(Edge 配置マニフェストの選択\)** をクリックします。 deployment.template.json ファイルは使用しないでください。 

6. 更新ボタンをクリックします。 新しい **CSharpModule** が、**SimulatedTemperatureSensor** モジュール、 **$edgeAgent** および **$edgeHub** と一緒に実行されていることがわかります。  

## <a name="view-generated-data"></a>生成されたデータを表示する

IoT Edge デバイスに配置マニフェストを適用すると、デバイス上の IoT Edge ランタイムが新しいデプロイ情報を収集し、そこで実行を開始します。 デバイス上で動作しているモジュールのうち、配置マニフェストに含まれていないものはすべて停止されます。 デバイスから不足しているモジュールがあればすべて開始されます。

IoT Edge デバイスのステータスは、Visual Studio Code エクスプローラーの **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを使用して確認できます。 デバイスの詳細を展開すると、デプロイされて実行中のモジュールが一覧表示されます。

1. Visual Studio Code のエクスプローラーで、IoT Edge デバイスの名前を右クリックし、 **[Start Monitoring Built-in Event Endpoint]\(組み込みイベント エンドポイントの監視を開始する\)** を選択します。

2. 自分の IoT ハブに到着するメッセージを表示します。 IoT Edge デバイスは新しいデプロイを受け取って、すべてのモジュールを開始する必要があるため、メッセージの到着にはしばらく時間がかかる場合があります。 その後、CModule コードに対して行った変更は、コンピューターの温度が 25 度に達するまで待ってからメッセージを送信します。 さらに、その温度しきい値に達するすべてのメッセージにメッセージ型 **Alert** が追加されます。 

   ![IoT Hub に到着するメッセージを表示する](./media/tutorial-csharp-module/view-d2c-message.png)
 
## <a name="edit-the-module-twin"></a>モジュール ツインを編集する

配置マニフェストで CSharpModule モジュール ツインを使用して、温度のしきい値を 25 度に設定しました。 モジュール ツインを使用することで、モジュール コードを更新することなく機能を変更できます。

1. Visual Studio Code で、自分の IoT Edge デバイスの詳細を展開し、実行中のモジュールを表示します。 

2. **[CSharpModule]** を右クリックし、 **[モジュール ツインを編集します]** を選択します。 

3. 目的のプロパティの中から **TemperatureThreshold** を見つけます。 その値を、新しい温度 (最後にレポートされた温度より 5 度から 10 度高い温度) に変更します。 

4. モジュール ツイン ファイルを保存します。

5. モジュール ツイン編集ウィンドウ内の任意の場所を右クリックして、 **[Update module twin]\(モジュール ツインの更新\)** を選択します。 

5. 到着する device-to-cloud メッセージを監視します。 新しい温度しきい値に達するまでメッセージが停止するのを確認できるはずです。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ 

次の推奨記事に進む場合は、作成したリソースおよび構成を維持して、再利用することができます。 また、同じ IoT Edge デバイスをテスト デバイスとして使用し続けることもできます。 

そうでない場合は、課金されないようにするために、ローカル構成と、この記事で使用した Azure リソースを削除できます。 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>次の手順

このチュートリアルでは、IoT Edge デバイスによって生成された生データをフィルター処理するコードを含む IoT Edge モジュールを作成しました。 独自のモジュールをビルドする準備ができたら、[独自の IoT Edge モジュールの開発](module-development.md)または [Visual Studio Code を使用してモジュールを開発](how-to-vs-code-develop-module.md)する方法の詳細をご確認ください。 次のチュートリアルに進むと、Azure のクラウド サービスをデプロイしてエッジでデータの処理と分析を行ううえで、Azure IoT Edge がどのように役立つかを確認できます。

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
