---
title: "Azure IoT Hub デバイス ツインのプロパティの使用 (.NET/.NET) | Microsoft Docs"
description: "Azure IoT Hub デバイス ツインを使用してデバイスを構成する方法。 Azure IoT device SDK for .NET を使用して、シミュレートされたデバイス アプリを実装し、Azure IoT service SDK for .NET を使用して、デバイス ツインを使用するデバイス構成を変更するサービス アプリを実装します。"
services: iot-hub
documentationcenter: .net
author: dsk-2015
manager: timlt
editor: 
ms.assetid: 3c627476-f982-43c9-bd17-e0698c5d236d
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2017
ms.author: dkshir
ms.openlocfilehash: 679cda28bf3ce9fb207fe3693a3453b355f1de15
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="use-desired-properties-to-configure-devices"></a>必要なプロパティを使用してデバイスを構成する
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

このチュートリアルの最後には、次の 2 つの .NET コンソール アプリが完成します。

* **SimulateDeviceConfiguration**: 必要な構成の更新を待機し、シミュレートされた構成の更新プロセスの状態を報告する、シミュレートされたデバイス アプリです。
* **SetDesiredConfigurationAndQuery**: デバイス上に必要な構成を設定し、構成更新プロセスをクエリする、バックエンド アプリです。

> [!NOTE]
> デバイス アプリケーションとバックエンド アプリの両方の作成に利用できる Azure IoT SDK に関する情報は、「[Azure IoT SDKs (Azure IoT SDK)][lnk-hub-sdks]」の記事で取り上げています。
> 
> 

このチュートリアルを完了するには、以下が必要です。

* Visual Studio 2015 または Visual Studio 2017。
* アクティブな Azure アカウント。 アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成することができます。

[デバイス ツインの概要][lnk-twin-tutorial]に関するチュートリアルを行った場合は、既に IoT Hub と、**myDeviceId** というデバイス ID があります。 その場合は、「[シミュレートされたデバイス アプリを作成する]」[lnk-how-to-configure-createapp]セクションをスキップできます。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

<a id="#create-the-simulated-device-app"></a>
## <a name="create-the-simulated-device-app"></a>シミュレートされたデバイスのアプリを作成する
このセクションでは、**myDeviceId** としてハブに接続し、必要な構成の更新を待機して、シミュレートされた構成の更新プロセスの情報を報告する、.NET コンソール アプリを作成します。

1. Visual Studio で、**コンソール アプリケーション** プロジェクト テンプレートを使用して、新しい Visual C# Windows クラシック デスクトップ プロジェクトを作成します。 プロジェクトには **SimulateDeviceConfiguration** という名前を付けます。
   
    ![New Visual C# Windows Classic device app][img-createdeviceapp]

1. ソリューション エクスプローラーで **SimulateDeviceConfiguration** プロジェクトを右クリックし、**[NuGet パッケージの管理...]** をクリックします。
1. **[NuGet パッケージ マネージャー]** ウィンドウで **[参照]** を選択し、**microsoft.azure.devices.client** を検索します。 **[インストール]** を選択して、**Microsoft.Azure.Devices.Client** パッケージをインストールし、使用条件に同意します。 この手順により、パッケージのダウンロードとインストールが実行され、[Azure IoT device SDK][lnk-nuget-client-sdk] NuGet パッケージへの参照とその依存関係が追加されます。
   
    ![NuGet Package Manager window Client app][img-clientnuget]
1. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using Newtonsoft.Json;

1. **Program** クラスに次のフィールドを追加します。 プレースホルダーの値は、前のセクションで記したデバイス接続文字列に置き換えてください。
   
        static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
        static DeviceClient Client = null;
        static TwinCollection reportedProperties = new TwinCollection();

1. **Program** クラスに次のメソッドを追加します。
 
        public static void InitClient()
        {
            try
            {
                Console.WriteLine("Connecting to hub");
                Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }
    **Client** オブジェクトに、デバイスからデバイス ツインとやりとりするのに必要なすべてのメソッドが表示されます。 上記のコードは**クライアント**オブジェクトを初期化し、次に **myDeviceId** のデバイス ツインを取得します。

1. **Program** クラスに次のメソッドを追加します。 このメソッドでは、ローカル デバイスでテレメトリの初期値を設定してから、デバイス ツインを更新します。

        public static async void InitTelemetry()
        {
            try
            {
                Console.WriteLine("Report initial telemetry config:");
                TwinCollection telemetryConfig = new TwinCollection();
                
                telemetryConfig["configId"] = "0";
                telemetryConfig["sendFrequency"] = "24h";
                reportedProperties["telemetryConfig"] = telemetryConfig;
                Console.WriteLine(JsonConvert.SerializeObject(reportedProperties));

                await Client.UpdateReportedPropertiesAsync(reportedProperties);
            }
            catch (AggregateException ex)
            {
                foreach (Exception exception in ex.InnerExceptions)
                {
                    Console.WriteLine();
                    Console.WriteLine("Error in sample: {0}", exception);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

1. **Program** クラスに次のメソッドを追加します。 これは、デバイス ツインで*必要なプロパティ*の変更を検出するコールバックです。

        private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
        {
            try
            {
                Console.WriteLine("Desired property change:");
                Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

                var currentTelemetryConfig = reportedProperties["telemetryConfig"];
                var desiredTelemetryConfig = desiredProperties["telemetryConfig"];

                if ((desiredTelemetryConfig != null) && (desiredTelemetryConfig["configId"] != currentTelemetryConfig["configId"]))
                {
                    Console.WriteLine("\nInitiating config change");
                    currentTelemetryConfig["status"] = "Pending";
                    currentTelemetryConfig["pendingConfig"] = desiredTelemetryConfig;

                    await Client.UpdateReportedPropertiesAsync(reportedProperties);

                    CompleteConfigChange();
                }
            }
            catch (AggregateException ex)
            {
                foreach (Exception exception in ex.InnerExceptions)
                {
                    Console.WriteLine();
                    Console.WriteLine("Error in sample: {0}", exception);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

    このメソッドでは、構成の更新要求を使用してローカル デバイス ツイン オブジェクトの報告されるプロパティを更新し、状態を **Pending** に設定してから、サービス上のデバイス ツインを更新します。 デバイス ツインが正常に更新されたら、次のポイントで記述されているメソッド `CompleteConfigChange` を呼び出すことで、構成の変更が完了します。

1. **Program** クラスに次のメソッドを追加します。 このメソッドは、デバイスのリセットをシミュレートしてから、ローカルの報告されるプロパティを更新して状態を **Success** に設定し、**pendingConfig** 要素を削除します。 その後、サービス上のデバイス ツインを更新します。 

        public static async void CompleteConfigChange()
        {
            try
            {
                var currentTelemetryConfig = reportedProperties["telemetryConfig"];

                Console.WriteLine("\nSimulating device reset");
                await Task.Delay(30000); 

                Console.WriteLine("\nCompleting config change");
                currentTelemetryConfig["configId"] = currentTelemetryConfig["pendingConfig"]["configId"];
                currentTelemetryConfig["sendFrequency"] = currentTelemetryConfig["pendingConfig"]["sendFrequency"];
                currentTelemetryConfig["status"] = "Success";
                currentTelemetryConfig["pendingConfig"] = null;

                await Client.UpdateReportedPropertiesAsync(reportedProperties);
                Console.WriteLine("Config change complete \nPress any key to exit.");
            }
            catch (AggregateException ex)
            {
                foreach (Exception exception in ex.InnerExceptions)
                {
                    Console.WriteLine();
                    Console.WriteLine("Error in sample: {0}", exception);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

1. 最後に、**Main** メソッドに次の行を追加します。

        try
        {
            InitClient();
            InitTelemetry();

            Console.WriteLine("Wait for desired telemetry...");
            Client.SetDesiredPropertyUpdateCallback(OnDesiredPropertyChanged, null).Wait();
            Console.ReadKey();
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }

   > [!NOTE]
   > このチュートリアルでは、同時実行の構成の更新動作はシミュレートしません。 一部の構成更新プロセスでは、更新の実行中に対象の構成に変更を加えることができますが、場合によってはキューに入れる必要があったり、エラー条件で拒否されたりすることがあります。 お使いの構成プロセスに必要な動作を考慮し、構成の変更を開始する前に適切なロジックを追加するようにしてください。
   > 
   > 
1. ソリューションをビルドし、**F5** キーをクリックして Visual Studio からデバイス アプリを実行します。 出力コンソールには、シミュレートされたデバイスがデバイス ツインを取得中であること、テレメトリをセットアップ中であること、また、必要なプロパティの変更を待機中であることを示すメッセージが表示されます。 そのままアプリを実行します。

## <a name="create-the-service-app"></a>サービス アプリケーションを作成する
このセクションでは、新しいテレメトリの構成オブジェクトを使用して、**myDeviceId** に関連付けられたデバイス ツインの*必要なプロパティ*を更新する、.NET コンソール アプリを作成します。 その後このアプリでは、IoT Hub に格納されているデバイス ツインにクエリを実行し、デバイスの必要な構成と報告される構成の違いを示します。

1. Visual Studio で、 **[コンソール アプリケーション]** プロジェクト テンプレートを使用し、Visual C# Windows クラシック デスクトップ プロジェクトを現在のソリューションに追加します。 プロジェクトに **SetDesiredConfigurationAndQuery** という名前を付けます。
   
    ![New Visual C# Windows Classic Desktop project][img-createapp]
1. ソリューション エクスプローラーで **SetDesiredConfigurationAndQuery** プロジェクトを右クリックし、**[NuGet パッケージの管理...]** をクリックします。
1. **[NuGet パッケージ マネージャー]** ウィンドウで **[参照]** を選択し、**microsoft.azure.devices** を検索します。**[インストール]** を選択して **Microsoft.Azure.Devices** パッケージをインストールし、使用条件に同意します。 この手順により、パッケージのダウンロードとインストールが実行され、[Azure IoT service SDK][lnk-nuget-service-sdk] NuGet パッケージへの参照とその依存関係が追加されます。
   
    ![NuGet Package Manager window][img-servicenuget]
1. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。
   
        using Microsoft.Azure.Devices;
        using System.Threading;
        using Newtonsoft.Json;
1. **Program** クラスに次のフィールドを追加します。 プレースホルダーの値は、前のセクションで作成したハブの IoT Hub 接続文字列に置き換えてください。
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
1. **Program** クラスに次のメソッドを追加します。
   
        static private async Task SetDesiredConfigurationAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch = new {
                    properties = new {
                        desired = new {
                            telemetryConfig = new {
                                configId = Guid.NewGuid().ToString(),
                                sendFrequency = "5m"
                            }
                        }
                    }
                };
   
            await registryManager.UpdateTwinAsync(twin.DeviceId, JsonConvert.SerializeObject(patch), twin.ETag);
            Console.WriteLine("Updated desired configuration");
   
            while (true)
            {
                var query = registryManager.CreateQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'");
                var results = await query.GetNextAsTwinAsync();
                foreach (var result in results)
                {
                    Console.WriteLine("Config report for: {0}", result.DeviceId);
                    Console.WriteLine("Desired telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Desired["telemetryConfig"], Formatting.Indented));
                    Console.WriteLine("Reported telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Reported["telemetryConfig"], Formatting.Indented));
                    Console.WriteLine();
                }
                Thread.Sleep(10000);
            }
        }
   
    **Registry** オブジェクトに、サービスからデバイス ツインとやりとりするのに必要なすべてのメソッドが表示されます。 このコードは、**Registry** オブジェクトを初期化し、**myDeviceId** のデバイス ツインを取得してから、新しいテレメトリ構成オブジェクトを使って必要なプロパティを更新します。
    その後、IoT Hub に格納されたデバイス ツインのクエリを 10 秒ごとに実行して、報告された目的のテレメトリ構成を出力します。 すべてのデバイスに関する豊富なレポートを生成する方法については、[IoT Hub のクエリ言語][lnk-query]に関するページを参照してください。
   
   > [!IMPORTANT]
   > このアプリケーションでは、例示を目的として 10 秒ごとに IoT Hub にクエリを実行します。 変更を検出するためではなく、あらゆるデバイスのユーザー向けのレポートを生成するためにクエリを使用します。 ソリューションにデバイス イベントのリアルタイム通知が必要な場合は、[ツイン通知][lnk-twin-notifications]を使用します。
   > 
   > 
1. 最後に、 **Main** メソッドに次の行を追加します。
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        SetDesiredConfigurationAndQuery();
        Console.WriteLine("Press any key to quit.");
        Console.ReadLine();
1. ソリューション エクスプローラーで、**[スタートアップ プロジェクトの設定...]** を開き、**SetDesiredConfigurationAndQuery** プロジェクトの **[アクション]** が **[開始]** になっていることを確認します。 ソリューションをビルドします。
1. **SimulateDeviceConfiguration** デバイス アプリを実行した状態で、**F5** キーを使用して Visual Studio からサービス アプリを実行します。 報告される構成が、24 時間ではなく 5 分の新しいアクティブな送信頻度で、**Pending** から **Success** に変わります。

 ![正常に構成されたデバイス][img-deviceconfigured]
   
   > [!IMPORTANT]
   > デバイスのレポート操作とクエリの結果までには、最大 1 分間の遅延が生じます。 これは、非常に大きな規模でクエリのインフラストラクチャを動作させるためです。 1 つのデバイス ツインの一貫したビューを取得するには、**Registry** クラスで **getDeviceTwin** メソッドを使用します。
   > 
   > 

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、必要な構成を*必要なプロパティ*としてソリューション バックエンドから設定し、その変更を検出して、報告されるプロパティを通してその状態を報告するマルチステップ更新プロセスをシミュレートするデバイス アプリを記述しました。

詳細については、次のリソースをご覧ください。

* [IoT Hub の概要][lnk-iothub-getstarted]に関するチュートリアルでデバイスからテレメトリを送信する。
* 多数のデバイスで操作をスケジュールまたは実行するには、[ジョブのスケジュールとブロードキャスト][lnk-schedule-jobs]に関するチュートリアルを参照してください。
* [ダイレクト メソッドの使用][lnk-methods-tutorial]に関するチュートリアルで、デバイスを対話形式で制御する (ユーザー制御アプリからファンをオンにするなど)。

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-twin-how-to-configure/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-csharp-twin-how-to-configure/createnetapp.png
[img-deviceconfigured]: media/iot-hub-csharp-csharp-twin-how-to-configure/deviceconfigured.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-twin-how-to-configure/createdeviceapp.png
[img-clientnuget]: media/iot-hub-csharp-csharp-twin-how-to-configure/devicesdknuget.png
[img-deviceconfigured]: media/iot-hub-csharp-csharp-twin-how-to-configure/deviceconfigured.png


<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/1.1.0/

[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-twin-tutorial]: iot-hub-csharp-csharp-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-iothub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-how-to-configure-createapp]: iot-hub-csharp-csharp-twin-how-to-configure.md#create-the-simulated-device-app
