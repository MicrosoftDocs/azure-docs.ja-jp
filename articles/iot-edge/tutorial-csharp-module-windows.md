---
title: 'チュートリアル: Windows 用の C# モジュールを開発する - Azure IoT Edge| Microsoft Docs'
description: このチュートリアルでは、C# コードで IoT Edge モジュールを作成して、Windows IoT Edge デバイスにデプロイする方法について説明します。
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: c872b10d7819fb95d614664ed32831f410349760
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122904"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>チュートリアル:Windows デバイス用の C# IoT Edge モジュールを開発する

Visual Studio を使用して C# コードを開発し、Azure IoT Edge を実行している Windows デバイスにデプロイします。 

Azure IoT Edge モジュールを使用して、ビジネス ロジックを実装するコードを IoT Edge デバイスに直接展開できます。 このチュートリアルでは、センサー データをフィルター処理する IoT Edge モジュールを作成および展開する方法について説明します。 このチュートリアルでは、以下の内容を学習します。    

> [!div class="checklist"]
> * Visual Studio を使用して C# SDK に基づく IoT Edge モジュールを作成する。
> * Visual Studio と Docker を使用して Docker イメージを作成し、レジストリに発行する。
> * モジュールを IoT Edge デバイスに展開する。
> * 生成されたデータを表示する。

このチュートリアルで作成する IoT Edge モジュールは、デバイスによって生成される温度データをフィルター処理します。 これは、指定されたしきい値を温度が上回っているときにのみ、メッセージを上流に送信します。 エッジでのこの種の分析は、クラウドに送信および保存されるデータ量を削減するうえで役に立ちます。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>ソリューション スコープ

このチュートリアルでは、**Visual Studio 2019** を使用して **C#** でモジュールを開発し、それを **Windows デバイス**にデプロイする方法について説明します。 Linux デバイス用のモジュールを開発する場合は、「[Linux デバイス用の C# IoT Edge モジュールを開発する](tutorial-csharp-module.md)」を参照してください。 

次の表を使用し、C モジュールを開発して Windows にデプロイする際のオプションをご確認ください。 

| C# | Visual Studio Code | Visual Studio 2017/2019 | 
| -- | ------------------ | ------------------ |
| **Windows AMD64 開発** | ![VS Code で WinAMD64 用の C# モジュールを開発する](./media/tutorial-c-module/green-check.png) | ![Visual Studio で WinAMD64 用の C# モジュールを開発する](./media/tutorial-c-module/green-check.png) |
| **Windows AMD64 デバッグ** |   | ![Visual Studio で WinAMD64 用の C# モジュールをデバッグする](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、開発環境を設定するための、前のチュートリアル「[Windows デバイス用の IoT Edge モジュールを開発する](tutorial-develop-for-windows.md)」を完了している必要があります。 そのチュートリアルを完了すると、既に以下の前提条件が満たされます。 

* Azure の Free レベルまたは Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。
* [Azure IoT Edge を実行している Windows デバイス](quickstart.md)
* コンテナー レジストリ ([Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) など)。
* [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) 拡張機能が構成された [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio)。
* Windows コンテナーを実行するように構成された [Docker Desktop](https://docs.docker.com/docker-for-windows/install/)。

> [!TIP]
> Visual Studio 2017 (バージョン 15.7 以降) を使用している場合は、Visual Studio Marketplace から VS 2017 用の [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) をダウンロードしてインストールしてください

## <a name="create-a-module-project"></a>モジュール プロジェクトを作成する

以下の手順では、Visual Studio と Azure IoT Edge Tools 拡張機能を使用して、IoT Edge モジュール プロジェクトを作成します。 プロジェクト テンプレートが作成されたら、新しいコードを追加して、報告されたプロパティに基づいてモジュールがメッセージをフィルター処理するようにします。 

### <a name="create-a-new-project"></a>新しいプロジェクトを作成する

Azure IoT Edge Tools は、Visual Studio でサポートされているすべての IoT Edge モジュール言語のプロジェクト テンプレートを提供します。 これらのテンプレートは、作業モジュールをデプロイして IoT Edge をテストするために必要なすべてのファイルとコードを含んでいます。または、独自のビジネス ロジックを使用してテンプレートをカスタマイズするための開始点を提供します。 

1. Visual Studio 2019 を起動し、 **[新しいプロジェクトの作成]** を選択します。

2. 新しいプロジェクトのウィンドウで、 **[IoT Edge]** プロジェクトを検索し、 **[Azure IoT Edge (Windows amd64)]** プロジェクトを選択します。 **[次へ]** をクリックします。 

   ![新しい Azure IoT Edge プロジェクトを作成する](./media/tutorial-csharp-module-windows/new-project.png)

3. [新しいプロジェクトの構成] ウィンドウで、プロジェクトとソリューションを、**CSharpTutorialApp** のようなわかりやすい名前に変更します。 **[作成]** をクリックしてプロジェクトを作成します。 

   ![新しい Azure IoT Edge プロジェクトを構成する](./media/tutorial-csharp-module-windows/configure-project.png)

4. IoT Edge アプリケーションとモジュールのウィンドウで、次の値を使用してプロジェクトを構成します。 

   | フィールド | 値 |
   | ----- | ----- |
   | テンプレートの選択 | **[C# モジュール]** を選択します。 | 
   | モジュール プロジェクト名 | ご自身のモジュールに **CSharpModule** と名前を付けます。 | 
   | Docker イメージ リポジトリ | イメージ リポジトリには、コンテナー レジストリの名前とコンテナー イメージの名前が含まれます。 コンテナー イメージは、モジュール プロジェクト名の値から事前に入力されています。 **localhost:5000** を、Azure コンテナー レジストリのログイン サーバーの値に置き換えます。 Azure portal で、コンテナー レジストリの概要ページからログイン サーバーを取得できます。 <br><br> 最終的なイメージ リポジトリは、\<registry name\>.azurecr.io/csharpmodule のようになります。 |

   ![ターゲット デバイス、モジュールの種類、コンテナー レジストリ用にプロジェクトを構成する](./media/tutorial-csharp-module-windows/add-application-and-module.png)

5. **[OK]** を選択して変更を適用します。 

### <a name="add-your-registry-credentials"></a>レジストリ資格情報を追加する

配置マニフェストは、コンテナー レジストリの資格情報を IoT Edge ランタイムと共有します。 ランタイムでご自身のプライベート イメージを IoT Edge デバイスにプルするとき、ランタイムではこれらの資格情報が必要になります。 該当する Azure コンテナー レジストリの **[アクセス キー]** セクションの資格情報を使用します。 

1. Visual Studio ソリューション エクスプローラーで、**deployment.template.json** ファイルを開きます。 

2. $edgeAgent の必要なプロパティで、**registryCredentials** プロパティを見つけます。 

3. 次の形式に従って、自分の資格情報でプロパティを更新します。 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

4. deployment.template.json ファイルを保存します。 

### <a name="update-the-module-with-custom-code"></a>カスタム コードでモジュールを更新する

既定のモジュール コードは、入力キュー上のメッセージを受け取り、出力キューを介してそれらを渡します。 モジュールがメッセージを IoT Hub に転送する前に、エッジでそれらを処理できるように、追加のコードを追加してみましょう。 メッセージごとに温度データを分析し、温度が特定のしきい値を超えた場合にのみ IoT Hub にメッセージを送信するように、モジュールを更新します。 

1. Visual Studio で、 **[CSharpModule]**  >  **[Program.cs]** の順に開きます。

2. **[CSharpModule]** 名前空間の上部で、後で使用する型として 3 つの **using** ステートメントを追加します。

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. カウンター変数の後に、**Program** クラスに **temperatureThreshold** 変数を追加します。 temperatureThreshold 変数は、測定温度の値を設定します。この値を超えると、IoT Hub にデータが送信されます。 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. 変数の宣言の後に、**MessageBody**、**Machine**、**Ambient** の各クラスを **Program** クラスに追加します。 これらのクラスは、受信メッセージの本文に対して予期されるスキーマを定義します。

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

5. **Init** メソッドを見つけます。 このメソッドは、**ModuleClient** オブジェクトを作成して構成します。これにより、モジュールはローカルの Azure IoT Edge ランタイムに接続し、メッセージを送受信することができます。 コードはまた、**input1** エンドポイントを介して IoT Edge ハブからメッセージを受信するためのコールバックを登録します。

   Init メソッド全体を次のコードに置き換えます。
   
   ```csharp
   static async Task Init()
   {
       AmqpTransportSettings amqpSetting = new AmqpTransportSettings(TransportType.Amqp_Tcp_Only);
       ITransportSettings[] settings = { amqpSetting };

       // Open a connection to the Edge runtime
       ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
       await ioTHubModuleClient.OpenAsync();
       Console.WriteLine("IoT Hub module client initialized.");

       // Read the TemperatureThreshold value from the module twin's desired properties
       var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
       await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

       // Attach a callback for updates to the module twin's desired properties.
       await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

       // Register a callback for messages that are received by the module.
       await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
   }
   ```
   
   この更新された Init メソッドは引き続き、ModuleClient で IoT Edge ランタイムへの接続を設定しますが、新しい機能も追加します。 モジュール ツインの必要なプロパティを読み取って、**temperatureThreshold** 値を取得します。 次に、モジュール ツインの必要なプロパティへの今後の更新をリッスンするコールバックを作成します。 このコールバックにより、モジュール ツインでの温度しきい値をリモートで更新することができ、変更はモジュールに組み込まれます。 

   更新された Init メソッドは、既存の **SetInputMessageHandlerAsync** メソッドも変更します。 サンプル コードで、*input1* の着信メッセージは *PipeMessage* 関数によって処理されますが、それを、次の手順で作成する *FilterMessages* 関数を使用するように変更します。 

6. 新しい **onDesiredPropertiesUpdate** メソッドを **Program** クラスに追加します。 このメソッドは、モジュール ツインから対象プロパティの更新を受け取り、それに合わせて **temperatureThreshold** 変数を更新します。 すべてのモジュールに独自のモジュール ツインがあり、これにより、モジュール内で実行されているコードをクラウドから直接構成できます。

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

7. サンプルの **PipeMessage** メソッドを削除して、新しい **FilterMessages** メソッドに置き換えます。 このメソッドは、モジュールが IoT Edge ハブからメッセージを受け取るたびに呼び出されます。 これにより、モジュール ツインで設定されているしきい値を下回る温度を報告するメッセージは除外されます。 また、**MessageType** プロパティを、値が **Alert** に設定されたメッセージに追加します。 

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

8. Program.cs ファイルを保存します。

9. IoT Edge ソリューション内の **deployment.template.json** ファイルを開きます。 このファイルは、IoT Edge エージェントにどのモジュール (この場合は **SimulatedTemperatureSensor** および **CSharpModule**) をデプロイするかを指示し、IoT Edge ハブにそれらの間でメッセージをルーティングする方法を指示します。

10. **CSharpModule** モジュール ツインを配置マニフェストに追加します。 次の JSON コンテンツを **modulesContent** セクションの下部、 **$edgeHub** モジュール ツインの後に挿入します。 

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![モジュール ツインをデプロイ テンプレートに追加する](./media/tutorial-csharp-module-windows/module-twin.png)

11. deployment.template.json ファイルを保存します。


## <a name="build-and-push-your-module"></a>モジュールをビルドしてプッシュする

前のセクションでは、IoT Edge ソリューションを作成し、**CSharpModule** にコードを追加しました。これにより、許容されるしきい値をマシンの温度が下回ることを報告するメッセージが除外されます。 次は、ソリューションをコンテナー イメージとしてビルドして、それをコンテナー レジストリにプッシュする必要があります。 

1. 次のコマンドを使用して、開発マシンで Docker にサインインします。 Azure コンテナー レジストリのユーザー名、パスワード、ログイン サーバーを使用します。 これらの値は、Azure portal でご自身のレジストリの **[アクセス キー]** セクションから取得できます。

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   `--password-stdin` の使用を推奨するセキュリティ警告が表示される場合があります。 このベスト プラクティスは、運用環境のシナリオを対象に推奨されていますが、それはこのチュートリアルの範囲外になります。 詳細については、[docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) のリファレンスをご覧ください。

2. Visual Studio のソリューション エクスプローラーで、ビルドするプロジェクト名を右クリックします。 既定の名前は **AzureIotEdgeApp1** です。そして、Windows モジュールをビルドするので、拡張子は **Windows.Amd64** になります。 

3. **[IoT Edge モジュールをビルドしてプッシュする]\(Build and Push IoT Edge Modules\)** を選択します。 

   ビルドおよびプッシュ コマンドは、3 つの操作を開始します。 最初に、デプロイ テンプレートと他のソリューション ファイルの情報からビルドされた完全な配置マニフェストを保持する、**config** という新しいフォルダーをソリューション内に作成します。 次に、`docker build` を実行して、お使いのターゲット アーキテクチャ用の適切な Dockerfile に基づいてコンテナー イメージをビルドします。 そして、`docker push` を実行して、イメージ リポジトリをコンテナー レジストリにプッシュします。 

## <a name="deploy-modules-to-device"></a>モジュールをデバイスにデプロイする

IoT Edge デバイスにモジュール プロジェクトをデプロイするには、Visual Studio Cloud Explorer と Azure IoT Edge Tools 拡張機能を使用します。 このシナリオ用の配置マニフェストである **deployment.json** ファイルは、config フォルダーに既に用意されています。 ここで行う必要があるのは、デプロイを受け取るデバイスの選択だけです。

お使いの IoT Edge デバイスが稼働していることを確認します。 

1. Visual Studio Cloud Explorer でリソースを展開して、IoT デバイスの一覧を表示します。 

2. デプロイを受け取る IoT Edge デバイスの名前を右クリックします。 

3. **[デプロイの作成]** を選択します。

4. ファイル エクスプローラーで、ソリューションの config フォルダー内にある **deployment.windows-amd64** ファイルを選択します。 

5. クラウド エクスプローラーを更新して、デプロイされたモジュールがデバイスの下に一覧表示されていることを確認します。 

## <a name="view-generated-data"></a>生成されたデータを表示する

IoT Edge デバイスに配置マニフェストを適用すると、デバイス上の IoT Edge ランタイムが新しいデプロイ情報を収集し、そこで実行を開始します。 デバイス上で動作しているモジュールのうち、配置マニフェストに含まれていないものはすべて停止されます。 デバイスから不足しているモジュールがあればすべて開始されます。 

IoT Edge Tools 拡張機能を使用すると、IoT Hub に到着したメッセージを表示することができます。 

1. Visual Studio Cloud Explorer で、お使いの IoT Edge デバイスの名前を選択します。 

2. **[操作]** の一覧で、 **[Start Monitoring Built-in Event Endpoint]\(組み込みイベント エンドポイントの監視を開始する\)** を選択します。 

3. 自分の IoT ハブに到着するメッセージを表示します。 メッセージが到着するまでにはしばらくかかる可能性があります。CModule コードに対して行った変更は、マシンの温度が 25 度に達するまで待ってから、メッセージを送信するからです。 また、その温度しきい値に達するどのメッセージにも、メッセージ型 **Alert** が追加されます。 

   ![IoT Hub に到着するメッセージを表示する](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>モジュール ツインを編集する

CSharpModule モジュール ツインを使用して、温度しきい値を 25 度に設定しました。 モジュール ツインを使用することで、モジュール コードを更新することなく機能を変更できます。

1. Visual Studio で、**deployment.windows-amd64.json** ファイルを開きます。 (deployment.template ファイルではありません。 ソリューション エクスプローラーの config ファイルに配置マニフェストが表示されない場合は、エクスプローラーのツールバーで **[すべてのファイルを表示]** アイコンを選択します。)

2. CSharpModule ツインを見つけ、**temperatureThreshold** パラメーターの値を、報告された最新の温度より 5 度から 10 度高い新規の温度に変更します。 

3. **deployment.windows-amd64.json** ファイルを保存します。

4. もう一度デプロイ手順に従って、更新された配置マニフェストをお使いのデバイスに適用します。 

5. 到着する device-to-cloud メッセージを監視します。 新しい温度しきい値に達するまでメッセージが停止するのを確認できるはずです。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ 

次の推奨記事に進む場合は、作成したリソースおよび構成を維持して、再利用することができます。 また、同じ IoT Edge デバイスをテスト デバイスとして使用し続けることもできます。 

そうでない場合は、課金されないようにするために、ローカル構成と、この記事で使用した Azure リソースを削除できます。 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、IoT Edge デバイスによって生成された生データをフィルター処理するコードを含む IoT Edge モジュールを作成しました。 独自のモジュールをビルドする準備ができたら、[独自の IoT Edge モジュールの開発](module-development.md)または [Visual Studio を使用したモジュールの開発](how-to-visual-studio-develop-module.md)に関する詳細について確認することができます。 次のチュートリアルに進み、Azure Cloud Services をデプロイしてエッジでデータを処理および分析するうえで、Azure IoT Edge がどのように役立つかを確認することができます。

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
