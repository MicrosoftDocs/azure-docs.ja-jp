---
title: チュートリアル - Azure IoT Edge を使用して Windows 用の C# モジュールを開発する
description: このチュートリアルでは、C# コードで IoT Edge モジュールを作成し、IoT Edge を実行している Windows デバイスにそれらを展開する方法について説明します。
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/03/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, amqp, devx-track-csharp
ms.openlocfilehash: 4e01b1ca9a3858ff31ad9b5da1d1159209c44330
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103464065"
---
# <a name="tutorial-develop-c-iot-edge-modules-using-windows-containers"></a>チュートリアル: Windows コンテナーを使用して C# IoT Edge モジュールを開発する

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

この記事では、Visual Studio を使用して C# コードを開発し、Azure IoT Edge を実行している Windows デバイスに展開する方法について説明します。

>[!NOTE]
>IoT Edge 1.1 LTS は、Windows コンテナーをサポートする最後のリリース チャネルです。 バージョン 1.2 以降では、Windows コンテナーはサポートされません。 Windows デバイスで IoT Edge を実行するには、[IoT Edge for Linux on Windows](iot-edge-for-linux-on-windows.md) の使用またはこちらへの移行を検討してください。

Azure IoT Edge モジュールを使用して、ビジネス ロジックを実装するコードを IoT Edge デバイスに直接展開できます。 このチュートリアルでは、センサー データをフィルター処理する IoT Edge モジュールを作成および展開する方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * Visual Studio を使用して C# SDK に基づく IoT Edge モジュールを作成する。
> * Visual Studio と Docker を使用して Docker イメージを作成し、レジストリに発行する。
> * モジュールを IoT Edge デバイスに展開する。
> * 生成されたデータを表示する。

このチュートリアルで作成する IoT Edge モジュールは、デバイスによって生成される温度データをフィルター処理します。 温度が指定されたしきい値を超えた場合にのみ、モジュールからアップストリームにメッセージが送信されます。 エッジでのこの種の分析は、クラウドに送信および保存されるデータ量を削減するうえで役に立ちます。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、Visual Studio 2019 を使用して C# でモジュールを開発し、それを Windows デバイスに展開する方法について説明します。 Linux コンテナーを使用してモジュールを開発する場合は、代わりに [Linux コンテナーを使用した C# IoT Edge モジュールの開発](tutorial-csharp-module.md)に関する記事を参照してください。

Windows コンテナーを使用して C# モジュールを開発してデプロイする際のオプションについては、次の表を参照してください。

| C# | Visual&nbsp;Studio&nbsp;Code | Visual Studio 2017&nbsp;および&nbsp;2019 |
| -- | :------------------: | :------------------: |
| Windows AMD64 開発 | ![Visual Studio Code で WinAMD64 用の C# モジュールを開発する](./media/tutorial-c-module/green-check.png) | ![Visual Studio で WinAMD64 用の C# モジュールを開発する](./media/tutorial-c-module/green-check.png) |
| Windows AMD64 デバッグ |   | ![Visual Studio で WinAMD64 用の C# モジュールをデバッグする](./media/tutorial-c-module/green-check.png) |

このチュートリアルを開始する前に、[Windows コンテナーを使用した IoT Edge モジュールの開発](tutorial-develop-for-windows.md)チュートリアルに記載された手順に従って開発環境を設定します。 これを完了すると、環境には次の前提条件が含まれます。

* Azure の Free レベルまたは Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。
* [Azure IoT Edge を実行している Windows デバイス](how-to-install-iot-edge-windows-on-windows.md)。
* コンテナー レジストリ ([Azure Container Registry](../container-registry/index.yml) など)。
* [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) 拡張機能で構成された [Visual Studio 2019](/visualstudio/install/install-visual-studio)。
* Windows コンテナーを実行するように構成された [Docker Desktop](https://docs.docker.com/docker-for-windows/install/)。

> [!TIP]
> Visual Studio 2017 (バージョン 15.7 以降) を使用している場合は、[Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) から Visual Studio 2017 用の Azure IoT Edge Tools をダウンロードしてインストールしてください。

## <a name="create-a-module-project"></a>モジュール プロジェクトを作成する

このセクションでは、Visual Studio と Azure IoT Edge Tools 拡張機能を使用して、IoT Edge モジュール プロジェクトを作成します。 プロジェクト テンプレートを作成したら、レポートされたプロパティに基づいてモジュールがメッセージを除外するように、新しいコードを追加します。

### <a name="create-a-new-project"></a>新しいプロジェクトを作成する

Azure IoT Edge Tools には、Visual Studio でサポートされているすべての IoT Edge モジュール言語のプロジェクト テンプレートが用意されています。 これらのテンプレートには、IoT Edge をテストするための作業モジュールの展開に必要なすべてのファイルとコードが含まれています。 また、独自のビジネス ロジックでテンプレートをカスタマイズする際の出発点として使用することもできます。

1. Visual Studio 2019 を開き、 **[新しいプロジェクトの作成]** を選択します。

1. **[新しいプロジェクトの作成]** ペインで、**IoT Edge** を検索し、結果リストで **[Azure IoT Edge (Windows amd64)]** プロジェクトを選択します。

   ![IoT Edge の [新しいプロジェクトの作成] ペインのスクリーンショット。](./media/tutorial-csharp-module-windows/new-project.png)

1. **[次へ]** を選択します。

    **[新しいプロジェクトの構成]** ペインが開きます。

   ![[新しいプロジェクトの構成] ペインのスクリーンショット。](./media/tutorial-csharp-module-windows/configure-project.png)

1. **[新しいプロジェクトの構成]** ペインで、プロジェクトとソリューションの名前を、**CSharpTutorialApp** のようなわかりやすい名前に変更します。 

1. **[作成]** を選択してプロジェクトを作成します。

   **[モジュールの追加]** ペインが開きます。

   ![プロジェクトを構成するための [モジュールの追加] ペインのスクリーンショット。](./media/tutorial-csharp-module-windows/add-application-and-module.png)

1. **[新しいプロジェクトの構成]** ページで、次を実行します。

   a. 左ペインで、 **[C# Module]\(C# モジュール\)** テンプレートを選択します。  
   b. **[モジュール名]** ボックスに「**CSharpModule**」と入力します。  
   c. **[リポジトリ URL]** ボックスで、**localhost:5000** を、Azure コンテナー レジストリの **ログイン サーバー** の値 (`<registry name>.azurecr.io/csharpmodule` の形式) に置き換えます。

    > [!NOTE]
    > イメージ リポジトリには、コンテナー レジストリの名前とコンテナー イメージの名前が含まれます。 コンテナー イメージは、モジュール プロジェクト名の値から事前に入力されています。  ログイン サーバーは、Azure portal のコンテナー レジストリの概要ページから取得できます。

1. **[追加]** を選択してプロジェクトを作成します。

### <a name="add-your-registry-credentials"></a>レジストリ資格情報を追加する

配置マニフェストは、コンテナー レジストリの資格情報を IoT Edge ランタイムと共有します。 ランタイムでご自身のプライベート イメージを IoT Edge デバイスにプルするとき、ランタイムではこれらの資格情報が必要になります。 該当する Azure コンテナー レジストリの **[アクセス キー]** セクションの資格情報を使用します。

1. Visual Studio ソリューション エクスプローラーで、*deployment.template.json* ファイルを開きます。

1. $edgeAgent の必要なプロパティで、**registryCredentials** プロパティを探します。 このプロパティのレジストリ アドレスには、プロジェクトの作成時に指定した情報が自動入力されます。 ユーザー名とパスワードのフィールドには変数名が含まれています。 次に例を示します。

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. 自分のモジュール ソリューションで環境 (ENV) ファイルを開きます。 このファイルは、既定ではソリューション エクスプローラーで非表示になっているので、表示するために、 **[すべてのファイルを表示]** ボタンを選択することが必要な場合があります。 ENV ファイルには、*deployment.template.json* ファイルで確認したのと同じユーザー名とパスワードの変数が含まれている必要があります。

1. Azure コンテナー レジストリからの **Username** と **Password** の値を追加します。

1. 変更内容を ENV ファイルに保存します。

### <a name="update-the-module-with-custom-code"></a>カスタム コードでモジュールを更新する

既定のモジュール コードでは、入力キューでメッセージを受け取り、出力キューを介してそれらを渡します。 モジュールがメッセージを IoT ハブに転送する前にエッジで処理するように、コードを追加しましょう。 各メッセージの温度データを分析し、温度が特定のしきい値を超えた場合にのみ IoT ハブにメッセージを送信するようにモジュールを更新します。

1. Visual Studio で、 **[CSharpModule]**  >  **[Program.cs]** の順に選択します。

1. **[CSharpModule]** 名前空間の上部で、後で使用する型として 3 つの **using** ステートメントを追加します。

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

1. カウンター変数の後に、**Program** クラスに **temperatureThreshold** 変数を追加します。 temperatureThreshold 変数は、測定温度の値を設定します。この値を超えると、IoT Hub にデータが送信されます。

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

1. 変数の宣言の後に、**MessageBody**、**Machine**、**Ambient** の各クラスを **Program** クラスに追加します。 これらのクラスは、受信メッセージの本文に対して予期されるスキーマを定義します。

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

1. **Init** メソッドを探します。 このメソッドは、**ModuleClient** オブジェクトを作成して構成します。これにより、モジュールはローカルの Azure IoT Edge ランタイムに接続し、メッセージを送受信することができます。 コードはまた、**input1** エンドポイントを介して IoT Edge ハブからメッセージを受信するためのコールバックを登録します。

   Init メソッド全体を次のコードに置き換えます。

   ```csharp
   static async Task Init()
   {
       AmqpTransportSettings amqpSetting = new AmqpTransportSettings(TransportType.Amqp_Tcp_Only);
       ITransportSettings[] settings = { amqpSetting };

       // Open a connection to the Edge runtime.
       ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
       await ioTHubModuleClient.OpenAsync();
       Console.WriteLine("IoT Hub module client initialized.");

       // Read the TemperatureThreshold value from the module twin's desired properties.
       var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
       await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

       // Attach a callback for updates to the module twin's desired properties.
       await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

       // Register a callback for messages that are received by the module.
       await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
   }
   ```

   この更新された Init メソッドは、引き続き ModuleClient で IoT Edge ランタイムへの接続を設定しますが、新しい機能も追加します。 モジュール ツインの必要なプロパティを読み取って、**temperatureThreshold** 値を取得します。 次に、モジュール ツインの必要なプロパティに対する今後の更新をリッスンするコールバックを作成します。 このコールバックにより、モジュール ツインでの温度しきい値をリモートで更新することができ、変更はモジュールに組み込まれます。

   更新された Init メソッドは、既存の **SetInputMessageHandlerAsync** メソッドも変更します。 サンプル コードで、*input1* の着信メッセージは *PipeMessage* 関数によって処理されますが、それを、次の手順で作成する *FilterMessages* 関数を使用するように変更します。

1. 新しい **onDesiredPropertiesUpdate** メソッドを **Program** クラスに追加します。 このメソッドでは、モジュール ツインから必要なプロパティの更新を受け取り、それに合わせて **temperatureThreshold** 変数を更新します。 すべてのモジュールに独自のモジュール ツインがあり、これにより、モジュール内で実行されているコードをクラウドから直接構成できます。

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

1. サンプルの **PipeMessage** メソッドを削除して、新しい **FilterMessages** メソッドに置き換えます。 このメソッドは、モジュールが IoT Edge ハブからメッセージを受け取るたびに呼び出されます。 これにより、モジュール ツインによって設定された温度しきい値を下回る温度をレポートするメッセージが除外されます。 また、値が **Alert** に設定されたメッセージに **MessageType** プロパティが追加されます。

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
                using(var filteredMessage = new Message(messageBytes))
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

1. *Program.cs* ファイルを保存します。

1. IoT Edge ソリューション内の *deployment.template.json* ファイルを開きます。 このファイルでは、展開するモジュールを IoT Edge エージェントに指示し、それらの間でメッセージをルーティングする方法を IoT Edge ハブに指示します。 ここでは、展開するモジュールは **SimulatedTemperatureSensor** と **CSharpModule** です。

1. **CSharpModule** モジュール ツインを配置マニフェストに追加します。 次の JSON コンテンツを `modulesContent` セクションの下部、 **$edgeHub** モジュール ツインの後に挿入します。

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![展開テンプレートに追加されるモジュール ツインを示すスクリーンショット。](./media/tutorial-csharp-module-windows/module-twin.png)

1. *deployment.template.json* ファイルを保存します。

## <a name="build-and-push-your-module"></a>モジュールをビルドしてプッシュする

前のセクションでは、IoT Edge ソリューションを作成し、レポートされたマシンの温度が許容されるしきい値を下回っているメッセージを除外するコードを **CSharpModule** に追加しました。 次は、ソリューションをコンテナー イメージとしてビルドして、それをコンテナー レジストリにプッシュする必要があります。

### <a name="sign-in-to-docker"></a>Docker にサインインする

1. 次のコマンドを使用して、開発マシンで Docker にサインインします。 Azure コンテナー レジストリのユーザー名、パスワード、ログイン サーバーを使用します。 これらの値は、Azure portal でご自身のレジストリの **[アクセス キー]** セクションから取得できます。

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   `--password-stdin` の使用を推奨するセキュリティ警告が表示される場合があります。 これは、運用環境のシナリオではベスト プラクティスとして推奨されていますが、このチュートリアルの範囲外になります。 詳細については、[docker login リファレンス](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)を参照してください。

### <a name="build-and-push"></a>ビルドとプッシュ

1. Visual Studio ソリューション エクスプローラーで、ビルドしたいプロジェクトの名前を右クリックします。 既定の名前は **AzureIotEdgeApp1** です。Windows モジュールをビルドするので、拡張子は **Windows.Amd64** になります。

1. **[IoT Edge モジュールをビルドしてプッシュする]\(Build and Push IoT Edge Modules\)** を選択します。

   ビルドおよびプッシュ コマンドでは、3 つの操作を開始します。
   * まず、*config* という名前の新しいフォルダーをソリューション内に作成します。そこに完全な展開マニフェストが保持されます。 これは、展開テンプレートと他のソリューション ファイルの情報からビルドされます。 
   * 次に、`docker build` を実行して、ターゲット アーキテクチャの適切な Dockerfile に基づいてコンテナー イメージをビルドします。 
   * 最後に、`docker push` を実行して、イメージ リポジトリを自分のコンテナー レジストリにプッシュします。

   このプロセスは、初回は数分かかることがありますが、次回これらのコマンドを実行するときには、それより速くなります。

## <a name="deploy-modules-to-the-device"></a>モジュールをデバイスに展開する

IoT Edge デバイスにモジュール プロジェクトをデプロイするには、Visual Studio Cloud Explorer と Azure IoT Edge Tools 拡張機能を使用します。 お客様のシナリオの展開マニフェストである *deployment.windows-amd64.json* ファイルは、*config* フォルダーに既に用意されています。 ここで行う必要があるのは、デプロイを受け取るデバイスの選択だけです。

お使いの IoT Edge デバイスが稼働していることを確認します。

1. Visual Studio Cloud Explorer で、リソースを展開して IoT デバイスの一覧を表示します。

1. デプロイを受け取る IoT Edge デバイスの名前を右クリックします。

1. **[デプロイの作成]** を選択します。

1. Visual Studio エクスプローラーで、自分のソリューションの *config* フォルダーにある *deployment.windows-amd64.json* ファイルを選択します。

1. Cloud Explorer を最新の情報に更新して、展開されたモジュールが自分のデバイスとして一覧表示されていることを確認します。

## <a name="view-generated-data"></a>生成されたデータを表示する

IoT Edge デバイスに展開マニフェストを適用すると、デバイス上の IoT Edge ランタイムによって新しい展開情報が収集され、そこで実行が開始されます。 デバイスで実行されていても、展開マニフェストに含まれていないモジュールはすべて停止されます。 デバイスから不足しているモジュールはすべて開始されます。

IoT Edge Tools 拡張機能を使用すると、自分の IoT ハブに到着したメッセージを表示できます。

1. Visual Studio Cloud Explorer で、お使いの IoT Edge デバイスの名前を選択します。

1. **[操作]** の一覧で、 **[Start Monitoring Built-in Event Endpoint]\(組み込みイベント エンドポイントの監視を開始する\)** を選択します。

1. 自分の IoT ハブに到着するメッセージを表示します。 IoT Edge デバイスは、新しい展開を受け取り、すべてのモジュールを開始する必要があるため、メッセージが到着するまでにしばらく時間がかかる場合があります。 CSharpModule コードの変更により、マシンの温度が 25 度に達するまで待ってから、メッセージを送信する必要があります。 また、その温度しきい値に達したメッセージには、メッセージの種類として **Alert** が追加されます。

   ![IoT ハブに到着するメッセージを表示する出力ウィンドウのスクリーンショット。](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>モジュール ツインを編集する

CSharpModule モジュール ツインを使用して、温度しきい値を 25 度に設定しました。 モジュール ツインを使用することで、モジュール コードを更新することなく機能を変更できます。

1. Visual Studio で、*deployment.windows-amd64.json* ファイルを開きます。 

   *deployment.template* ファイルは "*開かない*" でください。 ソリューション エクスプローラーで *config* ファイルに展開マニフェストが表示されない場合は、ソリューション エクスプローラーのツール バーで **[すべてのファイルを表示]** アイコンを選択します。

1. CSharpModule ツインを探し、**temperatureThreshold** パラメーターの値を、レポートされた最新の温度より 5 から 10 度高い新しい温度に変更します。

1. *deployment.windows-amd64.json* ファイルを保存します。

1. もう一度展開手順に従って、更新された展開マニフェストをデバイスに適用します。

1. 到着する device-to-cloud メッセージを監視します。 新しい温度しきい値に達するまで、メッセージは停止されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次の推奨記事に進む場合は、このチュートリアルで作成したリソースと構成を保持して再利用できます。 また、同じ IoT Edge デバイスをテスト デバイスとして使用し続けることもできます。

それ以外の場合は、課金されないようにするために、ローカル構成と、ここで使用した Azure リソースを削除できます。

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、IoT Edge デバイスによって生成された生データをフィルター処理するコードを含む IoT Edge モジュールを作成しました。

Azure クラウド サービスをデプロイしてエッジでデータの処理と分析を行う際に、Azure IoT Edge がどのように役立つかを確認するために、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure Functions](tutorial-deploy-function.md)
> [Azure Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Azure Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
