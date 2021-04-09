---
title: Azure IoT Hub デバイス ツインの使用 (.NET/.NET) | Microsoft Docs
description: Azure IoT Hub デバイス ツインを使用してタグを追加し、IoT Hub クエリを使用する方法。 Azure IoT device SDK for .NET を使用して、シミュレートされたデバイス アプリを実装し、Azure IoT service SDK for .NET を使用して、タグを追加し、IoT Hub クエリを実行するサービス アプリを実装します。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: 267a69486dc91ef95c0de704346eeb1d1780ef48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89013760"
---
# <a name="get-started-with-device-twins-net"></a>デバイス ツインの使用 (.NET)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

このチュートリアルでは、次の .NET コンソール アプリを作成します。

* **AddTagsAndQuery**。 このバックエンド アプリでは、タグを追加し、デバイス ツインのクエリを行います。

* **ReportConnectivity**。 このデバイス アプリでは、作成済みのデバイス ID を使用して IoT hub に接続するデバイスをシミュレートし、その接続の状態を報告します。

> [!NOTE]
> デバイス アプリとバックエンド アプリの両方をビルドするために使用できる Azure IoT SDK については、記事「[Azure IoT SDK](iot-hub-devguide-sdks.md)」を参照してください。
>

## <a name="prerequisites"></a>前提条件

* 見ることができます。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 アカウントがない場合は、 [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を数分で作成することができます。

* ポート 8883 がファイアウォールで開放されていることを確認してください。 この記事のデバイス サンプルでは、ポート 8883 を介して通信する MQTT プロトコルを使用しています。 このポートは、企業や教育用のネットワーク環境によってはブロックされている場合があります。 この問題の詳細と対処方法については、「[IoT Hub への接続 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)」を参照してください。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT ハブに新しいデバイスを登録する

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>IoT ハブ接続文字列を取得する

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>デバイス アプリを作成する

このセクションでは、**myDeviceId** に関連付けられたデバイス ツインに場所のメタデータを追加する .NET コンソール アプリを C# を使用して作成します。 その後、米国にあるデバイスで、携帯ネットワーク接続を報告しているものを選択して、IoT Hub に格納されているデバイス ツインに対してクエリを実行します。

1. Visual Studio で、 **[新しいプロジェクトの作成]** を選択します。 **[新しいプロジェクトの作成]** で、 **[コンソール アプリ (.NET Framework)]** を選択してから、 **[次へ]** を選択します。

1. **[新しいプロジェクトの構成]** で、プロジェクトに **AddTagsAndQuery** という名前を付けます。

    ![AddTagsAndQuery プロジェクトを構成する](./media/iot-hub-csharp-csharp-twin-getstarted/config-addtagsandquery-app.png)

1. ソリューション エクスプローラーで **AddTagsAndQuery** プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。

1. **[参照]** を選択し、**Microsoft.Azure.Devices** を探して選択します。 **[インストール]** を選択します。

    ![NuGet Package Manager window](./media/iot-hub-csharp-csharp-twin-getstarted/nuget-package-addtagsandquery-app.png)

   この手順により、パッケージのダウンロードとインストールが実行され、[Azure IoT service SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet パッケージへの参照とその依存関係が追加されます。

1. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。

    ```csharp  
    using Microsoft.Azure.Devices;
    ```

1. **Program** クラスに次のフィールドを追加します。 `{iot hub connection string}` を、「[IoT ハブ接続文字列を取得する](#get-the-iot-hub-connection-string)」でコピーしておいた IoT ハブ接続文字列に置き換えてください。

    ```csharp  
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

1. **Program** クラスに次のメソッドを追加します。

    ```csharp  
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

        var query = registryManager.CreateQuery(
          "SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
        var twinsInRedmond43 = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43: {0}", 
          string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));

        query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
        var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43 using cellular network: {0}", 
          string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
    }
    ```

    **RegistryManager** クラスに、サービスからデバイス ツインとやりとりするのに必要なすべてのメソッドが表示されます。 前のコードでは、まず **registryManager** オブジェクトを初期化し、**myDeviceId** のデバイス ツインを取得して、最後にタグを目的の位置情報で更新します。

    更新後、2 つのクエリを実行します。1 番目のクエリでは、**Redmond43** 工場にあるデバイスのデバイス ツインのみを選択し、2 番目のクエリでは携帯ネットワーク経由で接続しているデバイスのみを選択します。

    前のコードでは、**query** オブジェクトの作成時に、返されるドキュメントの最大数を指定します。 **query** オブジェクトには、**GetNextAsTwinAsync** メソッドを複数回呼び出してすべての結果を取得する際に使用できる **HasMoreResults** のブール型プロパティが含まれます。 **GetNextAsJson** というメソッドは、集計クエリの結果など、デバイス ツインではない結果に使用できます。

1. 最後に、**Main** メソッドに次の行を追加します。

    ```csharp  
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddTagsAndQuery().Wait();
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

1. **AddTagsAndQuery** プロジェクトを右クリックし、 **[デバッグ]** を選択してから、 **[新しいインスタンスを開始]** を選択して、このアプリケーションを実行します。 **Redmond43** にあるすべてのデバイスを照会するクエリの結果には、1 件のデバイスが表示され、携帯ネットワークを使用するデバイスに絞り込んだ結果には 0 件のデバイスが表示されます。

    ![ウィンドウに表示されたクエリの結果](./media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png)

次のセクションでは、接続情報を報告し、前のセクションのクエリの結果を変更するデバイス アプリを作成します。

## <a name="create-the-device-app"></a>デバイス アプリを作成する

このセクションでは、**myDeviceId** としてハブに接続し、報告されるプロパティに携帯ネットワークを使用しているという情報を含めるよう更新する .NET コンソール アプリを作成します。

1. Visual Studio で、 **[ファイル]**  >  **[新規]**  >  **[プロジェクト]** の順に選択します。 **[新しいプロジェクトの作成]** で、 **[コンソール アプリ (.NET Framework)]** を選択してから、 **[次へ]** を選択します。

1. **[新しいプロジェクトの構成]** で、プロジェクトに **ReportConnectivity** という名前を付けます。 **[ソリューション]** で、 **[ソリューションに追加]** を選択し、 **[作成]** を選択します。

1. ソリューション エクスプローラーで **ReportConnectivity** プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。

1. **[参照]** を選択し、**Microsoft.Azure.Devices.Client** を探して選択します。 **[インストール]** を選択します。

   この手順により、パッケージのダウンロードとインストールが実行され、[Azure IoT device SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet パッケージへの参照とその依存関係が追加されます。

1. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。

    ```csharp  
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. **Program** クラスに次のフィールドを追加します。 `{device connection string}` を、「[IoT ハブに新しいデバイスを登録する](#register-a-new-device-in-the-iot-hub)」で書き留めたデバイス接続文字列に置き換えます。

    ```csharp  
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

1. **Program** クラスに次のメソッドを追加します。

    ```csharp
    public static async void InitClient()
    {
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
              TransportType.Mqtt);
            Console.WriteLine("Retrieving twin");
            await Client.GetTwinAsync();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

    **Client** オブジェクトに、デバイスからデバイス ツインとやりとりするのに必要なすべてのメソッドが表示されます。 上記のコードでは、**クライアント** オブジェクトが初期化された後、**myDeviceId** のデバイス ツインが取得されます。

1. **Program** クラスに次のメソッドを追加します。

    ```csharp  
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
    ```

   上記のコードでは、**myDeviceId** の報告されるプロパティが接続情報で更新されます。

1. 最後に、**Main** メソッドに次の行を追加します。

    ```csharp
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
    ```

1. ソリューション エクスプローラーでソリューションを右クリックし、 **[スタートアップ プロジェクトの設定]** を選択します。

1. **[共通プロパティ]**  >  **[スタートアップ プロジェクト]** で、 **[マルチ スタートアップ プロジェクト]** を選択します。 **ReportConnectivity** では、 **[アクション]** として **[開始]** を選択します。 **[OK]** を選択して変更を保存します。  

1. **ReportConnectivity** プロジェクトを右クリックし、 **[デバッグ]** を選択してから、 **[新しいインスタンスを開始]** を選択して、このアプリを実行します。 アプリでツインの情報が取得された後、接続性が "**_報告されるプロパティ_**" として送信される様子が見られます。

    ![デバイス アプリを実行して接続性を報告](./media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png)

   デバイスの接続情報が報告された後、両方のクエリで表示されるようになります。

1. **AddTagsAndQuery** プロジェクトを右クリックし、 **[デバッグ]**  >  **[新しいインスタンスを開始]** を選択して、クエリを再実行します。 今回は、**myDeviceId** が両方のクエリ結果に表示されるはずです。

    ![デバイスの接続性が正常に報告される](./media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Portal で新しい IoT Hub を構成し、IoT Hub の ID レジストリにデバイス ID を作成しました。 バックエンド アプリからデバイスのメタデータをタグとして追加し、シミュレート対象デバイス アプリでデバイス ツインのデバイスの接続情報を報告するよう記述しました。 さらに、SQL に似た IoT Hub クエリ言語を使用してこの情報を照会する方法も学習しました。

次のリソースで詳細を確認できます。

* デバイスからテレメトリを送信する方法を確認するには、[デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-dotnet.md)に関するチュートリアルをご覧ください。

* デバイス ツインの必要なプロパティを使用してデバイスを構成する方法を確認するには、[必要なプロパティを使用したデバイスの構成](tutorial-device-twins.md)に関するチュートリアルをご覧ください。

* ユーザー制御アプリからファンをオンにするなど、デバイスを対話形式で制御する方法を確認するには、[ダイレクト メソッドの使用](quickstart-control-device-dotnet.md)に関するチュートリアルをご覧ください。
