---
title: "Azure IoT Hub デバイス ツインの使用 (.NET/.NET) | Microsoft Docs"
description: "Azure IoT Hub デバイス ツインを使用してタグを追加し、IoT Hub クエリを使用する方法。 Azure IoT device SDK for .NET を使用して、シミュレートされたデバイス アプリを実装し、Azure IoT service SDK for .NET を使用して、タグを追加し、IoT Hub クエリを実行するサービス アプリを実装します。"
services: iot-hub
documentationcenter: node
author: dsk-2015
manager: timlt
editor: 
ms.assetid: f7e23b6e-bfde-4fba-a6ec-dbb0f0e005f4
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/15/2017
ms.author: dkshir
ms.openlocfilehash: 6073d594117e69676b753a1e3af25fffa3583a2b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-device-twins-netnet"></a>デバイス ツインの概要 (.NET/.NET)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

このチュートリアルの最後には、次の .NET コンソール アプリが完成します。

* **CreateDeviceIdentity**: .NET アプリ。デバイス ID と関連付けられているセキュリティ キーを作成し、シミュレーション対象デバイス アプリを接続します。
* **AddTagsAndQuery**: .NET バックエンド アプリ。タグを追加してデバイス ツインのクエリを実行します。
* **ReportConnectivity**: .NET デバイス アプリ。以前作成したデバイス ID を使用して IoT hub に接続するデバイスをシミュレートし、接続の状態を報告します。

> [!NOTE]
> デバイス アプリケーションとバックエンド アプリの両方の作成に利用できる Azure IoT SDK に関する情報は、「[Azure IoT SDKs (Azure IoT SDK)][lnk-hub-sdks]」の記事で取り上げています。
> 
> 

このチュートリアルを完了するには、以下が必要です。

* Visual Studio 2015 または Visual Studio 2017。
* アクティブな Azure アカウント。 (アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成できます)。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

代わりにプログラムを使用してデバイス ID を作成する場合は、[.NET を使用したシミュレーション対象デバイスの IoT hub への接続][lnk-device-identity-csharp]に関する記事の対応するセクションをご覧ください。

## <a name="create-the-service-app"></a>サービス アプリケーションを作成する
このセクションでは、**myDeviceId** に関連付けられたデバイス ツインに場所のメタデータを追加する .NET コンソール アプリを (C# を使用) 作成します。 その後、米国にあるデバイスで、携帯ネットワーク接続を報告しているものを選択して、IoT Hub に格納されているデバイス ツインに対してクエリを実行します。

1. Visual Studio で、 **[コンソール アプリケーション]** プロジェクト テンプレートを使用し、Visual C# Windows クラシック デスクトップ プロジェクトを現在のソリューションに追加します。 プロジェクトに **AddTagsAndQuery** という名前を付けます。
   
    ![New Visual C# Windows Classic Desktop project][img-createapp]
1. ソリューション エクスプローラーで **AddTagsAndQuery** プロジェクトを右クリックし、**[NuGet パッケージの管理...]** をクリックします。
1. **[NuGet パッケージ マネージャー]** ウィンドウで **[参照]** を選択し、**microsoft.azure.devices** を検索します。 **[インストール]** を選択して、**Microsoft.Azure.Devices** パッケージをインストールし、使用条件に同意します。 この手順により、パッケージのダウンロードとインストールが実行され、[Azure IoT service SDK][lnk-nuget-service-sdk] NuGet パッケージへの参照とその依存関係が追加されます。
   
    ![NuGet Package Manager window][img-servicenuget]
1. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。
   
        using Microsoft.Azure.Devices;
1. **Program** クラスに次のフィールドを追加します。 プレースホルダーの値は、前のセクションで作成したハブの IoT Hub 接続文字列に置き換えてください。
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
1. **Program** クラスに次のメソッドを追加します。
   
        public static async Task AddTagsAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch =
                @"{
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                        }
                    }
                }";
            await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);
   
            var query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            var twinsInRedmond43 = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43: {0}", string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));
   
            query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43 using cellular network: {0}", string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
        }
   
    **RegistryManager** クラスに、サービスからデバイス ツインとやりとりするのに必要なすべてのメソッドが表示されます。 前のコードでは、まず **registryManager** オブジェクトを初期化し、**myDeviceId** のデバイス ツインを取得して、最後にタグを目的の位置情報で更新します。
   
    更新後、2 つのクエリを実行します。1 番目のクエリでは、**Redmond43** 工場にあるデバイスのデバイス ツインのみを選択し、2 番目のクエリでは携帯ネットワーク経由で接続しているデバイスのみを選択します。
   
    前のコードでは、**query** オブジェクトの作成時に返されるドキュメントの最大数を指定します。 **query** オブジェクトには、**GetNextAsTwinAsync** メソッドを複数回呼び出してすべての結果を取得する際に使用できる **HasMoreResults** のブール型プロパティが含まれます。 **GetNextAsJson** というメソッドは、集計クエリの結果など、デバイス ツインではない結果に使用できます。
1. 最後に、 **Main** メソッドに次の行を追加します。
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddTagsAndQuery().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

1. ソリューション エクスプローラーで、**[スタートアップ プロジェクトの設定...]** を開き、**AddTagsAndQuery** プロジェクトの **[アクション]** が **[開始]** になっていることを確認します。 ソリューションをビルドします。
1. **AddTagsAndQuery** プロジェクトを右クリックし、**[デバッグ]** を選択してから、**[新しいインスタンスを開始]** を選択して、このアプリケーションを実行します。 **Redmond43** にあるすべてのデバイスを照会するクエリの結果には、1 件のデバイスが表示され、携帯ネットワークを使用するデバイスに絞り込んだ結果には 0 件のデバイスが表示されます。
   
    ![ウィンドウに表示されたクエリの結果][img-addtagapp]

次のセクションでは、接続情報を報告し、前のセクションのクエリの結果を変更するデバイス アプリを作成します。

## <a name="create-the-device-app"></a>サービス アプリを作成する
このセクションでは、**myDeviceId** としてハブに接続し、報告されるプロパティに携帯ネットワークを使用しているという情報を含めるよう更新する .NET コンソール アプリを作成します。

1. Visual Studio で、 **[コンソール アプリケーション]** プロジェクト テンプレートを使用し、Visual C# Windows クラシック デスクトップ プロジェクトを現在のソリューションに追加します。 プロジェクトに **ReportConnectivity** という名前をつけます。
   
    ![New Visual C# Windows Classic device app][img-createdeviceapp]
    
1. ソリューション エクスプローラーで **ReportConnectivity** プロジェクトを右クリックし、**[NuGet パッケージの管理...]** をクリックします。
1. **[NuGet パッケージ マネージャー]** ウィンドウで **[参照]** を選択し、**microsoft.azure.devices.client** を検索します。 **[インストール]** を選択して、**Microsoft.Azure.Devices.Client** パッケージをインストールし、使用条件に同意します。 この手順により、パッケージのダウンロードとインストールが実行され、[Azure IoT device SDK][lnk-nuget-client-sdk] NuGet パッケージへの参照とその依存関係が追加されます。
   
    ![NuGet Package Manager window Client app][img-clientnuget]
1. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using Newtonsoft.Json;

1. **Program** クラスに次のフィールドを追加します。 プレースホルダーの値は、前のセクションで記したデバイス接続文字列に置き換えてください。
   
        static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
        static DeviceClient Client = null;

1. **Program** クラスに次のメソッドを追加します。

       public static async void InitClient()
        {
            try
            {
                Console.WriteLine("Connecting to hub");
                Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
                Console.WriteLine("Retrieving twin");
                await Client.GetTwinAsync();
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

    **Client** オブジェクトに、デバイスからデバイス ツインとやりとりするのに必要なすべてのメソッドが表示されます。 上記のコードは**クライアント**オブジェクトを初期化し、次に **myDeviceId** のデバイス ツインを取得します。

1. **Program** クラスに次のメソッドを追加します。
   
        public static async void ReportConnectivity()
        {
            try
            {
                Console.WriteLine("Sending connectivity data as reported property");
                
                TwinCollection reportedProperties, connectivity;
                reportedProperties = new TwinCollection();
                connectivity = new TwinCollection();
                connectivity["type"] = "cellular";
                reportedProperties["connectivity"] = connectivity;
                await Client.UpdateReportedPropertiesAsync(reportedProperties);
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

   上記のコードは、**myDeviceId**の報告対象プロパティを接続情報で更新します。

1. 最後に、 **Main** メソッドに次の行を追加します。
   
       try
       {
            InitClient();
            ReportConnectivity();
       }
       catch (Exception ex)
       {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
       }
       Console.WriteLine("Press Enter to exit.");
       Console.ReadLine();

1. ソリューション エクスプローラーで、**[スタートアップ プロジェクトの設定...]** を開き、**ReportConnectivity** プロジェクトの **[アクション]** が **[開始]** になっていることを確認します。 ソリューションをビルドします。
1. **ReportConnectivity** プロジェクトを右クリックし、**[デバッグ]** を選択してから、**[新しいインスタンスを開始]** を選択して、このアプリケーションを実行します。 ツインの情報を取得し、次に接続性が*報告対象プロパティ*として送信される様子が見られます。
   
    ![デバイス アプリを実行して接続性を報告][img-rundeviceapp]
    
    
1. これで、デバイスが接続情報を報告したため、両方のクエリで表示されるようになります。 .NET **AddTagsAndQuery** アプリを実行して、クエリをもう一度実行します。 今回は、**myDeviceId** が両方のクエリ結果に表示されるはずです。
   
    ![デバイスの接続性が正常に報告される][img-tagappsuccess]

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Azure Portal で新しい IoT Hub を構成し、IoT Hub の ID レジストリにデバイス ID を作成しました。 バックエンド アプリからデバイスのメタデータをタグとして追加し、シミュレート対象デバイス アプリでデバイス ツインのデバイスの接続情報を報告するよう記述しました。 さらに、SQL に似た IoT Hub クエリ言語を使用してこの情報を照会する方法も学習しました。

詳細については、次のリソースをご覧ください。

* [IoT Hub の概要][lnk-iothub-getstarted]に関するチュートリアルでデバイスからテレメトリを送信する。
* 「[目的のプロパティを使用してデバイスを構成する][lnk-twin-how-to-configure]」チュートリアルで、デバイス ツインの必要なプロパティを使用してデバイスを構成する。
* 「[ダイレクト メソッドの使用][lnk-methods-tutorial]」チュートリアルで、デバイスを対話形式で制御する (ユーザー制御アプリからファンをオンにするなど)。

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-twin-getstarted/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-csharp-twin-getstarted/createnetapp.png
[img-addtagapp]: media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-twin-getstarted/createdeviceapp.png
[img-clientnuget]: media/iot-hub-csharp-csharp-twin-getstarted/clientsdknuget.png
[img-rundeviceapp]: media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png
[img-tagappsuccess]: media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/

[lnk-device-identity-csharp]: iot-hub-csharp-csharp-getstarted.md#DeviceIdentity_csharp
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-twin-how-to-configure]: iot-hub-csharp-node-twin-how-to-configure.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

