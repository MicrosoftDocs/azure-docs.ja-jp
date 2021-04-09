---
title: Azure IoT Hub を使用してジョブのスケジュールを設定する (.NET/.NET) | Microsoft Docs
description: 複数のデバイスでダイレクト メソッドを呼び出すように Azure IoT Hub ジョブのスケジュールを設定する方法。 Azure IoT device SDK for .NET を使用して、シミュレートされたデバイス アプリと、ジョブを実行するサービス アプリを実装します。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: 0391afe5562238f488fde7e50d914c46a5c32944
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92142451"
---
# <a name="schedule-and-broadcast-jobs-net"></a>ジョブのスケジュールとブロードキャスト (.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub を使用して、数百万のデバイスを更新するジョブのスケジュールと追跡を行います。 ジョブを使用して、次の操作を行います。

* 必要なプロパティを更新する

* タグを更新する

* ダイレクト メソッドを呼び出す

ジョブはこれらのアクションの 1 つをラップし、デバイス ツイン クエリで定義されるデバイスのセットに対して実行状況を追跡します。 たとえば、バックエンド アプリは、ジョブを使用して 10,000 台のデバイスでダイレクト メソッドを呼び出し、デバイスを再起動できます。 デバイス ツイン クエリでデバイスのセットを指定し、ジョブが将来実行されるようにスケジュールを設定します。 各デバイスが再起動のダイレクト メソッドを受信し、実行するたびに、ジョブは進行状況を追跡します。

これらの各機能の詳細については、次の記事をご覧ください。

* デバイス ツインとプロパティ: [デバイス ツインの概要](iot-hub-csharp-csharp-twin-getstarted.md)および[チュートリアル: デバイス ツインのプロパティの使用方法](tutorial-device-twins.md)

* ダイレクト メソッド: [IoT Hub 開発者ガイド - ダイレクト メソッド](iot-hub-devguide-direct-methods.md)および[チュートリアル: ダイレクト メソッドの使用](quickstart-control-device-dotnet.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

このチュートリアルでは、次の操作方法について説明します。

* バックエンド アプリから呼び出し可能な **LockDoor** と呼ばれるダイレクト メソッドを実装するデバイス アプリを作成します。

* 複数のデバイスで **LockDoor** ダイレクト メソッドを呼び出すジョブを作成するバックエンド アプリを作成します。 もう 1 つのジョブは、必要なプロパティの更新を複数のデバイスに送信します。

このチュートリアルの最後には、次の 2 つの .NET (C#) コンソール アプリが完成します。

* **SimulateDeviceMethods**。 このアプリは IoT ハブに接続し、**LockDoor** ダイレクト メソッドを実装します。

* **ScheduleJob**。 このアプリはジョブを使用して **LockDoor** ダイレクト メソッドを呼び出し、複数のデバイスでデバイス ツインの必要なプロパティを更新します。

## <a name="prerequisites"></a>前提条件

* 見ることができます。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 アカウントがない場合は、 [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を数分で作成することができます。

* ポート 8883 がファイアウォールで開放されていることを確認してください。 この記事のデバイス サンプルでは、ポート 8883 を介して通信する MQTT プロトコルを使用しています。 このポートは、企業や教育用のネットワーク環境によってはブロックされている場合があります。 この問題の詳細と対処方法については、「[IoT Hub への接続 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)」を参照してください。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT ハブに新しいデバイスを登録する

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>シミュレート対象デバイス アプリの作成

このセクションでは、ソリューション バックエンドによって呼び出されたダイレクト メソッドに応答する .NET コンソール アプリを作成します。

1. Visual Studio で、 **[新しいプロジェクトの作成]** を選択し、 **[コンソール アプリ (.NET Framework)]** プロジェクト テンプレートを選択します。 **[次へ]** をクリックして続行します。

1. **[新しいプロジェクトの構成]** で、プロジェクトに *SimulateDeviceMethods* という名前を付け、 **[作成]** を選択します。

    ![SimulateDeviceMethods プロジェクトの構成](./media/iot-hub-csharp-csharp-schedule-jobs/configure-device-app.png)

1. ソリューション エクスプローラーで **[SimulateDeviceMethods]** プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。

1. **[NuGet パッケージ マネージャー]** で **[参照]** を選択し、**Microsoft.Azure.Devices.Client** を検索して選択します。 **[インストール]** を選択します。

    ![NuGet Package Manager window Client app](./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png)

    この手順により、パッケージのダウンロードとインストールが実行され、[Azure IoT device SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet パッケージへの参照とその依存関係が追加されます。

1. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. **Program** クラスに次のフィールドを追加します。 プレースホルダーの値は、前のセクションで記したデバイス接続文字列に置き換えてください。

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;
    ```

1. 次のコードを追加して、デバイスにダイレクト メソッドを実装します。

    ```csharp
    static Task<MethodResponse> LockDoor(MethodRequest methodRequest, object userContext)
    {
        Console.WriteLine();
        Console.WriteLine("Locking Door!");
        Console.WriteLine("\nReturning response for method {0}", methodRequest.Name);

        string result = "'Door was locked.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```

1. 次のメソッドを追加して、デバイスにデバイス ツイン リスナーを実装します。

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

1. 最後に、IoT ハブへの接続を開いてメソッド リスナーを初期化する次のコードを **Main** メソッドに追加します。

    ```csharp
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
          TransportType.Mqtt);

        Client.SetMethodHandlerAsync("LockDoor", LockDoor, null);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null);

        Console.WriteLine("Waiting for direct method call and device twin update\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        Client.SetMethodHandlerAsync("LockDoor", null, null);
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

1. 作業内容を保存し、ソリューションを構築します。

> [!NOTE]
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードには、「[一時的な障害の処理](/azure/architecture/best-practices/transient-faults)」で推奨されているように、再試行ポリシー (接続の再試行など) を実装する必要があります。
>

## <a name="get-the-iot-hub-connection-string"></a>IoT ハブ接続文字列を取得する

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>ダイレクト メソッドを呼び出し、デバイス ツインの更新を送信するジョブのスケジュール

このセクションでは、ジョブを使用して **LockDoor** ダイレクト メソッドを呼び出し、複数のデバイスに必要なプロパティの更新を送信する .NET コンソール アプリ (C# を使用) を作成します。

1. Visual Studio で、 **[ファイル]**  >  **[新規]**  >  **[プロジェクト]** の順に選択します。 **[新しいプロジェクトの作成]** で、 **[コンソール アプリ (.NET Framework)]** を選択してから、 **[次へ]** を選択します。

1. **[新しいプロジェクトの構成]** で、プロジェクトに *ScheduleJob* という名前を付けます。 **[ソリューション]** で、 **[ソリューションに追加]** を選択し、 **[作成]** を選択します。

    ![ScheduleJob プロジェクトに名前を付けて構成する](./media/iot-hub-csharp-csharp-schedule-jobs/config-schedule-job-app.png)

1. ソリューション エクスプローラーで **[ScheduleJob]** プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。

1. **[NuGet パッケージ マネージャー]** で **[参照]** を選択し、**Microsoft.Azure.Devices** を検索して選択してから、 **[インストール]** を選択します。

   この手順により、パッケージのダウンロードとインストールが実行され、[Azure IoT service SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet パッケージへの参照とその依存関係が追加されます。

1. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。

    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

1. 既定のステートメント内に存在しない場合は、次の `using` ステートメントを追加します。

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

1. **Program** クラスに次のフィールドを追加します。 プレースホルダーを前述の「[IoT ハブ接続文字列を取得する](#get-the-iot-hub-connection-string)」でコピーした IoT Hub 接続文字列とデバイスの名前に置き換えます。

    ```csharp
    static JobClient jobClient;
    static string connString = "<yourIotHubConnectionString>";
    static string deviceId = "<yourDeviceId>";
    ```

1. **Program** クラスに次のメソッドを追加します。

    ```csharp
    public static async Task MonitorJob(string jobId)
    {
        JobResponse result;
        do
        {
            result = await jobClient.GetJobAsync(jobId);
            Console.WriteLine("Job Status : " + result.Status.ToString());
            Thread.Sleep(2000);
        } while ((result.Status != JobStatus.Completed) && 
          (result.Status != JobStatus.Failed));
    }
    ```

1. **Program** クラスに次のメソッドを追加します。

    ```csharp
    public static async Task StartMethodJob(string jobId)
    {
        CloudToDeviceMethod directMethod = 
          new CloudToDeviceMethod("LockDoor", TimeSpan.FromSeconds(5), 
          TimeSpan.FromSeconds(5));

        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            $"DeviceId IN ['{deviceId}']",
            directMethod,
            DateTime.UtcNow,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Method Job");
    }
    ```

1. 別のメソッドを **Program** クラスに追加します。

    ```csharp
    public static async Task StartTwinUpdateJob(string jobId)
    {
        Twin twin = new Twin(deviceId);
        twin.Tags = new TwinCollection();
        twin.Tags["Building"] = "43";
        twin.Tags["Floor"] = "3";
        twin.ETag = "*";

        twin.Properties.Desired["LocationUpdate"] = DateTime.UtcNow;

        JobResponse createJobResponse = jobClient.ScheduleTwinUpdateAsync(
            jobId,
            $"DeviceId IN ['{deviceId}']", 
            twin, 
            DateTime.UtcNow, 
            (long)TimeSpan.FromMinutes(2).TotalSeconds).Result;

        Console.WriteLine("Started Twin Update Job");
    }
    ```

    > [!NOTE]
    > クエリ構文の詳細については、[IoT Hub クエリ言語](./iot-hub-devguide-query-language.md)に関するページを参照してください。
    >

1. 最後に、**Main** メソッドに次の行を追加します。

    ```csharp
    Console.WriteLine("Press ENTER to start running jobs.");
    Console.ReadLine();

    jobClient = JobClient.CreateFromConnectionString(connString);

    string methodJobId = Guid.NewGuid().ToString();

    StartMethodJob(methodJobId);
    MonitorJob(methodJobId).Wait();
    Console.WriteLine("Press ENTER to run the next job.");
    Console.ReadLine();

    string twinUpdateJobId = Guid.NewGuid().ToString();

    StartTwinUpdateJob(twinUpdateJobId);
    MonitorJob(twinUpdateJobId).Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```

1. 作業内容を保存し、ソリューションを構築します。

## <a name="run-the-apps"></a>アプリの実行

これで、アプリを実行する準備が整いました。

1. Visual Studio ソリューション エクスプローラーでソリューションを右クリックし、 **[スタートアップ プロジェクトの設定]** を選択します。

1. **[共通プロパティ]**  >  **[スタートアップ プロジェクト]** の順に選択し、 **[マルチ スタートアップ プロジェクト]** を選択します。

1. `SimulateDeviceMethods` が一覧の一番上にあり、その後に `ScheduleJob` があることを確認します。 両方のアクションを **[開始]** に設定し、 **[OK]** を選択します。

1. **[開始]** をクリックしてプロジェクトを実行するか、 **[デバッグ]** メニューに移動し、 **[デバッグの開始]** をクリックします。

   デバイスとバックエンド アプリの両方からの出力が表示されます。

    ![アプリを実行して、ジョブをスケジュールします。](./media/iot-hub-csharp-csharp-schedule-jobs/schedule-jobs-console-results.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、ジョブを使用して、デバイスへのダイレクト メソッドと、デバイス ツインのプロパティの更新をスケジュールしました。

* ファームウェアのリモートでのワイヤレス更新など、IoT Hub とデバイス管理パターンの作業を続けるには、[ファームウェア更新の実行方法に関するチュートリアル](tutorial-firmware-update.md)を参照してください。

* Azure IoT Edge を使用したエッジ デバイスへの AI のデプロイについては、[IoT Edge の使用](../iot-edge/quickstart-linux.md)に関するページを参照してください。