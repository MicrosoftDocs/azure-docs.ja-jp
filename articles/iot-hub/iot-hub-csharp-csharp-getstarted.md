---
title: "Azure IoT Hub の使用 (.NET) | Microsoft Docs"
description: "IoT SDK for .NET を使用して Azure IoT Hub にデバイスからクラウドへのメッセージを送信する方法について説明します。 デバイスを登録し、メッセージを送信して、IoT ハブからメッセージを読み取るために、シミュレートされたデバイスとサービス アプリを作成します。"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: f40604ff-8fd6-4969-9e99-8574fbcf036c
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 69296eb9ac2a74a97b632d27733a6a06500b4abd
ms.contentlocale: ja-jp
ms.lasthandoff: 08/09/2017

---
# <a name="connect-your-device-to-your-iot-hub-using-net"></a>.NET を使用してデバイスを IoT ハブに接続する

[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

このチュートリアルの最後には、次の 3 つの .NET コンソール アプリが完成します。

* **CreateDeviceIdentity**。デバイス ID および関連付けられているセキュリティ キーを作成し、デバイス アプリを接続します。
* **ReadDeviceToCloudMessages**。デバイス アプリから送信されたテレメトリを表示します。
* **SimulatedDevice**。以前に作成したデバイス ID で IoT Hub に接続し、MQTT プロトコルを使用して 1 秒ごとにテレメトリ メッセージを送信します。

3 つのアプリを含んだ Visual Studio ソリューションを GitHub からダウンロードまたは複製することができます。

```bash
git clone https://github.com/Azure-Samples/iot-hub-dotnet-simulated-device-client-app.git
```

> [!NOTE]
> デバイス上で動作するアプリケーションの作成とソリューションのバックエンドで動作するアプリケーションの開発に利用できる各種 Azure IoT SDK については、「[Azure IoT SDK][lnk-hub-sdks]」を参照してください。

このチュートリアルを完了するには、以下が必要です。

* Visual Studio 2015 または Visual Studio 2017。
* アクティブな Azure アカウント。 (アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成できます)。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

IoT Hub の作成は以上です。以降の作業に必要なホスト名と IoT Hub 接続文字列が得られました。

<a id="DeviceIdentity_csharp"></a>
[!INCLUDE [iot-hub-get-started-create-device-identity-csharp](../../includes/iot-hub-get-started-create-device-identity-csharp.md)]

<a id="D2C_csharp"></a>
## <a name="receive-device-to-cloud-messages"></a>デバイスからクラウドへのメッセージの受信
このセクションでは、デバイスからクラウドへのメッセージを IoT Hub から読み取る .NET コンソール アプリケーションを作成します。 IoT Hub は、デバイスからクラウドへのメッセージを読み取るための、[Azure Event Hubs][lnk-event-hubs-overview] と互換性のあるエンドポイントを公開します。 わかりやすくするために、このチュートリアルで作成するリーダーは基本的なものであり、高スループットのデプロイメントには適していません。 デバイスからクラウドへのメッセージを大規模に処理する方法については、[デバイスからクラウドへのメッセージの処理][lnk-process-d2c-tutorial]に関するチュートリアルを参照してください。 Event Hubs からのメッセージを処理する方法の詳細については、「[Event Hubs の使用][lnk-eventhubs-tutorial]」のチュートリアルを参照してください。 このチュートリアルは、IoT Hub の Event Hub 対応エンドポイントで応用できます。

> [!NOTE]
> Event Hub 対応エンドポイントは、常に、デバイスからクラウドへのメッセージを読み取るために AMQP プロトコルを使用します。

1. Visual Studio で、**[Console App (.NET Framework)]** プロジェクト テンプレートを使用し、Visual C# Windows クラシック デスクトップ プロジェクトを現在のソリューションに追加します。 .NET Framework のバージョンが 4.5.1 以降であることを確認します。 プロジェクトに **ReadDeviceToCloudMessages**という名前を付けます。

    ![New Visual C# Windows Classic Desktop project][10a]

2. ソリューション エクスプローラーで **ReadDeviceToCloudMessages** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。

3. **[NuGet パッケージ マネージャー]** ウィンドウで **WindowsAzure.ServiceBus** を検索し、**[インストール]** を選択して、使用条件に同意します。 この手順により、[Azure Service Bus][lnk-servicebus-nuget] のダウンロードとインストールが実行され、その参照とすべての依存関係が追加されます。 このパッケージは、アプリケーションが IoT Hub でイベント ハブと互換性のあるエンドポイントに接続できるようにします。

4. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。

    ```csharp
    using Microsoft.ServiceBus.Messaging;
    using System.Threading;
    ```

5. **Program** クラスに次のフィールドを追加します。 プレースホルダーの値は、「IoT Hub の作成」セクションで作成したハブの接続文字列に置き換えてください。

    ```csharp
    static string connectionString = "{iothub connection string}";
    static string iotHubD2cEndpoint = "messages/events";
    static EventHubClient eventHubClient;
    ```

6. **Program** クラスに次のメソッドを追加します。

    ```csharp
    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested) break;
            EventData eventData = await eventHubReceiver.ReceiveAsync();
            if (eventData == null) continue;

            string data = Encoding.UTF8.GetString(eventData.GetBytes());
            Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
        }
    }
    ```

    このメソッドは、**EventHubReceiver** インスタンスを使用して、IoT Hub のすべてのデバイスからクラウドへの受信パーティションからメッセージを受け取ります。 **EventHubReceiver** オブジェクトを作成するときの `DateTime.Now` パラメーターの渡し方に注目してください。これによって、起動後に送信されたメッセージのみを受信するようにしています。 このフィルターは、現在のメッセージのセットを表示できるので、テスト環境で便利です。 運用環境では、すべてのメッセージがコードによって処理されるようにする必要があります。 詳細については、[IoT Hub のデバイスからクラウドへのメッセージを処理する方法][lnk-process-d2c-tutorial]に関するチュートリアルを参照してください。

7. 最後に、 **Main** メソッドに次の行を追加します。

    ```csharp
    Console.WriteLine("Receive messages. Ctrl-C to exit.\n");
    eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);

    var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;

    CancellationTokenSource cts = new CancellationTokenSource();

    System.Console.CancelKeyPress += (s, e) =>
    {
        e.Cancel = true;
        cts.Cancel();
        Console.WriteLine("Exiting...");
    };

    var tasks = new List<Task>();
    foreach (string partition in d2cPartitions)
    {
        tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
    }  
    Task.WaitAll(tasks.ToArray());
    ```

## <a name="create-a-device-app"></a>デバイス アプリの作成

このセクションでは、デバイスからクラウドへのメッセージを IoT ハブに送信するデバイスをシミュレートする .NET コンソール アプリを作成します。

1. Visual Studio で、**[Console App (.NET Framework)]** プロジェクト テンプレートを使用し、Visual C# Windows クラシック デスクトップ プロジェクトを現在のソリューションに追加します。 .NET Framework のバージョンが 4.5.1 以降であることを確認します。 プロジェクトに **SimulatedDevice**という名前を付けます。

    ![New Visual C# Windows Classic Desktop project][10b]

2. ソリューション エクスプローラーで **SimulatedDevice** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。

3. **[NuGet パッケージ マネージャー]** ウィンドウで **[参照]** を選択し、**Microsoft.Azure.Devices.Client** を検索します。その後、**[インストール]** を選択し、**Microsoft.Azure.Devices.Client** パッケージをインストールして使用条件に同意します。 この手順により、パッケージのダウンロードとインストールが実行され、[Azure IoT device SDK NuGet パッケージ][lnk-device-nuget]への参照とその依存関係が追加されます。

4. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Newtonsoft.Json;
    ```

5. **Program** クラスに次のフィールドを追加します。 `{iot hub hostname}` は、「IoT ハブの作成」セクションで取得した IoT ハブ ホスト名に置き換えます。 `{device key}` は、「デバイス ID の作成」セクションで取得したデバイス キーに置き換えます。

    ```csharp
    static DeviceClient deviceClient;
    static string iotHubUri = "{iot hub hostname}";
    static string deviceKey = "{device key}";
    ```

6. **Program** クラスに次のメソッドを追加します。

    ```csharp
    private static async void SendDeviceToCloudMessagesAsync()
    {
        double minTemperature = 20;
        double minHumidity = 60;
        int messageId = 1;
        Random rand = new Random();

        while (true)
        {
            double currentTemperature = minTemperature + rand.NextDouble() * 15;
            double currentHumidity = minHumidity + rand.NextDouble() * 20;

            var telemetryDataPoint = new
            {
                messageId = messageId++,
                deviceId = "myFirstDevice",
                temperature = currentTemperature,
                humidity = currentHumidity
            };
            var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
            var message = new Message(Encoding.ASCII.GetBytes(messageString));
            message.Properties.Add("temperatureAlert", (currentTemperature > 30) ? "true" : "false");

            await deviceClient.SendEventAsync(message);
            Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);

            await Task.Delay(1000);
        }
    }
    ```

    このメソッドは、デバイスからクラウドへの新しいメッセージを 1 秒おきに送信します。 このメッセージには、JSON 形式でシリアル化されたオブジェクトが、デバイス ID、ランダムに生成された番号と共に含まれ、これによって温度センサーと湿度センサーがシミュレートされます。

7. 最後に、 **Main** メソッドに次の行を追加します。

    ```csharp
    Console.WriteLine("Simulated device\n");
    deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey ("myFirstDevice", deviceKey), TransportType.Mqtt);

    SendDeviceToCloudMessagesAsync();
    Console.ReadLine();
    ```

    既定では、.NET Framework アプリの **Create** メソッドは、IoT Hub と通信するために AMQP プロトコルを使用する **DeviceClient** インスタンスを作成します。 MQTT または HTTP プロトコルを使用するには、プロトコルを引数として受け取る、**Create** メソッドのオーバーライドを使用します。 UWP および PCL クライアントは、既定では HTTP プロトコルを使用します。 HTTP プロトコルを使用する場合は、**Microsoft.AspNet.WebApi.Client** NuGet パッケージをプロジェクトに追加して、**System.Net.Http.Formatting** 名前空間を含める必要もあります。

このチュートリアルでは、IoT Hub デバイス アプリを作成する手順について説明します。 Visual Studio 拡張機能である [Connected Service for Azure IoT Hub][lnk-connected-service] を使用して、デバイス アプリに必要なコードを追加することもできます。

> [!NOTE]
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードでは、[一時的な障害処理][lnk-transient-faults]に関する MSDN の記事で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。

## <a name="run-the-apps"></a>アプリの実行

これで、アプリを実行する準備が整いました。

1. Visual Studio のソリューション エクスプローラーでソリューションを右クリックし、 **[スタートアップ プロジェクトの設定]**をクリックします。 **[マルチ スタートアップ プロジェクト]** を選択し、**ReadDeviceToCloudMessages** プロジェクトと **SimulatedDevice** プロジェクトのアクションとして、どちらも **[開始]** を選択します。

    ![Startup project properties][41]

2. **F5** キーを押して両方のアプリケーションを実行します。 デバイス アプリから IoT ハブに送信されたメッセージは、**SimulatedDevice** アプリのコンソール出力に表示されます。 IoT Hub が受信したメッセージは、 **ReadDeviceToCloudMessages** アプリのコンソール出力に表示されます。

    ![Console output from apps][42]

3. [Azure Portal][lnk-portal] の **[使用状況]** タイルには、IoT Hub に送信されたメッセージ数が表示されます。

    ![Azure portal Usage tile][43]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Portal で IoT Hub を構成し、IoT Hub の ID レジストリにデバイス ID を作成しました。 デバイス アプリでデバイスからクラウドへのメッセージを IoT ハブに送信できるようにするために、このデバイス ID を使用しました。 また、IoT Hub で受け取ったメッセージを表示するアプリを作成しました。

引き続き IoT Hub の使用方法を確認すると共に、他の IoT のシナリオについて調べるには、次のページを参照してください。

* [デバイスを接続する][lnk-connect-device]
* [デバイス管理の概要][lnk-device-management]
* [IoT Edge の概要][lnk-iot-edge]

既存の IoT ソリューションを拡張し、デバイスからクラウドへのメッセージを大規模に処理する方法については、[デバイスからクラウドへのメッセージの処理][lnk-process-d2c-tutorial]に関するチュートリアルを参照してください。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

<!-- Images. -->
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png
[10a]: ./media/iot-hub-csharp-csharp-getstarted/create-receive-csharp1.png
[10b]: ./media/iot-hub-csharp-csharp-getstarted/create-device-csharp1.png


<!-- Links -->
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-connected-service]: https://visualstudiogallery.msdn.microsoft.com/e254a3a5-d72e-488e-9bd3-8fee8e0cd1d6
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

