---
title: Azure IoT Hub デバイス管理の開始 (.NET/.NET) | Microsoft Docs
description: Azure IoT Hub デバイス管理を使用してリモート デバイスの再起動を開始する方法。 .NET 用 Azure IoT device SDK を使用して、ダイレクト メソッドを含むシミュレートされたデバイス アプリを実装し、Azure IoT service SDK for .NET を使用して、ダイレクト メソッドを呼び出すサービス アプリを実装します。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: 5760c574e64a3b3b4a1df12092cff44374790a90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89018401"
---
# <a name="get-started-with-device-management-net"></a>デバイス管理の開始 (.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

このチュートリアルでは、次の操作方法について説明します。

* Azure Portal を使用して IoT Hub を作成し、IoT Hub でデバイス ID を作成します。

* デバイスを再起動するダイレクト メソッドを含む、シミュレション済みデバイス アプリを作成します。 ダイレクト メソッドは、クラウドから呼び出されます。

* シミュレートされたデバイス アプリで再起動ダイレクト メソッドを IoT Hub 経由で呼び出す .NET コンソール アプリを作成します。

このチュートリアルの最後には、次の 2 つの .NET コンソール アプリが完成します。

* **SimulateManagedDevice**。 このアプリでは、作成済みのデバイス ID を使用して IoT Hub に接続し、再起動ダイレクト メソッドを受信し、物理的な再起動をシミュレートし、最後の再起動時間を報告します。

* **TriggerReboot**。 このアプリでは、シミュレート対象デバイス アプリでダイレクト メソッドを呼び出し、応答を表示し、更新された報告されるプロパティを表示します。

## <a name="prerequisites"></a>前提条件

* 見ることができます。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 アカウントがない場合は、 [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を数分で作成することができます。

* ポート 8883 がファイアウォールで開放されていることを確認してください。 この記事のデバイス サンプルでは、ポート 8883 を介して通信する MQTT プロトコルを使用しています。 このポートは、企業や教育用のネットワーク環境によってはブロックされている場合があります。 この問題の詳細と対処方法については、「[IoT Hub への接続 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)」を参照してください。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT ハブに新しいデバイスを登録する

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>IoT ハブ接続文字列を取得する

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>ダイレクト メソッドを使用してデバイスのリモート再起動をトリガーする

このセクションでは、ダイレクト メソッドを使用してデバイスでのリモート再起動を開始する .NET コンソール アプリケーションを C# を使用して作成します。 このアプリは、デバイス ツイン クエリを使用して、そのデバイスの前回の再起動時刻を検出します。

1. Visual Studio で、 **[新しいプロジェクトの作成]** を選択します。

1. **[新しいプロジェクトの作成]** で、 **[コンソール アプリ (.NET Framework)]** プロジェクト テンプレートを探して選択し、 **[次へ]** を選択します。

1. **[新しいプロジェクトの構成]** で、プロジェクトに *TriggerReboot* という名前を設定し、.NET Framework バージョン 4.5.1 以降を選択します。 **［作成］** を選択します

    ![New Visual C# Windows Classic Desktop project](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-configure.png)

1. **ソリューション エクスプローラー** で **TriggerReboot** プロジェクトを右クリックし、 **[NuGet パッケージの管理]** をクリックします。

1. **[参照]** を選択し、**Microsoft.Azure.Devices** を探して選択します。 **[インストール]** を選択し、**Microsoft.Azure.Devices** パッケージをインストールします。

    ![NuGet Package Manager window](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-nuget-devices.png)

   この手順により、パッケージのダウンロードとインストールが実行され、[Azure IoT service SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet パッケージへの参照とその依存関係が追加されます。

1. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```

1. **Program** クラスに次のフィールドを追加します。 `{iot hub connection string}` プレースホルダーの値を、先ほど「[IoT ハブ接続文字列を取得する](#get-the-iot-hub-connection-string)」でコピーした IoT Hub 接続文字列に置き換えます。

   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

1. **Program** クラスに次のメソッドを追加します。  このコードは、再起動中のデバイスのデバイス ツインを取得し、報告されるプロパティを出力します。

   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```

1. **Program** クラスに次のメソッドを追加します。  このコードは、ダイレクト メソッドを使用してデバイスの再起動をトリガーします。

   ```csharp
   public static async Task StartReboot()
   {
       client = ServiceClient.CreateFromConnectionString(connString);
       CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
       method.ResponseTimeout = TimeSpan.FromSeconds(30);

       CloudToDeviceMethodResult result = await 
         client.InvokeDeviceMethodAsync(targetDevice, method);

       Console.WriteLine("Invoked firmware update on device.");
   }
   ```

1. 最後に、**Main** メソッドに次の行を追加します。

   ```csharp
   registryManager = RegistryManager.CreateFromConnectionString(connString);
   StartReboot().Wait();
   QueryTwinRebootReported().Wait();
   Console.WriteLine("Press ENTER to exit.");
   Console.ReadLine();
   ```

1. **[ビルド]**  >  **[ビルドするソリューション]** を選択します。

> [!NOTE]
> このチュートリアルでは、デバイスでレポートされるプロパティ対して 1 つのクエリのみを実行します。 運用環境のコードでは、レポートされるプロパティの変化を検出するために、ポーリング処理をお勧めします。

## <a name="create-a-simulated-device-app"></a>シミュレート対象デバイス アプリの作成

このセクションでは、次の作業を行います。

* クラウドによって呼び出されたダイレクト メソッドに応答する .NET コンソール アプリを作成します。

* シミュレート対象デバイスの再起動をトリガーします。

* 報告されるプロパティを使用して、デバイス ツイン クエリで、デバイスとデバイスの最後の再起動時間を識別できるようにします。

シミュレートされたデバイス アプリを作成するには、次の手順を実行します。

1. Visual Studio で、既に作成した TriggerReboot ソリューションの **[ファイル]**  >  **[新規]**  >  **[プロジェクト]** を選択します。 **[新しいプロジェクトの作成]** で、 **[コンソール アプリ (.NET Framework)]** プロジェクト テンプレートを探して選択し、 **[次へ]** を選択します。

1. **[新しいプロジェクトの構成]** で、プロジェクトに *SimulateManagedDevice* という名前を付け、 **[ソリューション]** で **[ソリューションに追加]** を選択します。 **［作成］** を選択します

    ![プロジェクトに名前を付けてソリューションに追加する](./media/iot-hub-csharp-csharp-device-management-get-started/configure-device-app.png)

1. ソリューション エクスプローラーで新しい **SimulateManagedDevice** プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。

1. **[参照]** を選択し、**Microsoft.Azure.Devices.Client** を探して選択します。 **[インストール]** を選択します。

    ![NuGet Package Manager window Client app](./media/iot-hub-csharp-csharp-device-management-get-started/create-device-nuget-devices-client.png)

   この手順により、パッケージのダウンロードとインストールが実行され、[Azure IoT device SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet パッケージへの参照とその依存関係が追加されます。

1. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

1. **Program** クラスに次のフィールドを追加します。 `{device connection string}` プレースホルダーの値を、先ほど「[IoT ハブに新しいデバイスを登録する](#register-a-new-device-in-the-iot-hub)」でメモしたデバイス接続文字列に置き換えます。

    ```csharp
    static string DeviceConnectionString = "{device connection string}";
    static DeviceClient Client = null;
    ```

1. デバイスにダイレクト メソッドを実装するために以下を追加します。

   ```csharp
   static Task<MethodResponse> onReboot(MethodRequest methodRequest, object userContext)
   {
       // In a production device, you would trigger a reboot 
       //   scheduled to start after this method returns.
       // For this sample, we simulate the reboot by writing to the console
       //   and updating the reported properties.
       try
       {
           Console.WriteLine("Rebooting!");

           // Update device twin with reboot time. 
           TwinCollection reportedProperties, reboot, lastReboot;
           lastReboot = new TwinCollection();
           reboot = new TwinCollection();
           reportedProperties = new TwinCollection();
           lastReboot["lastReboot"] = DateTime.Now;
           reboot["reboot"] = lastReboot;
           reportedProperties["iothubDM"] = reboot;
           Client.UpdateReportedPropertiesAsync(reportedProperties).Wait();
       }
       catch (Exception ex)
       {
           Console.WriteLine();
           Console.WriteLine("Error in sample: {0}", ex.Message);
       }

       string result = @"{""result"":""Reboot started.""}";
       return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
   }
   ```

1. 最後に、IoT ハブへの接続を開いてメソッド リスナーを初期化する次のコードを **Main** メソッドに追加します。

   ```csharp
   try
   {
       Console.WriteLine("Connecting to hub");
       Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
         TransportType.Mqtt);

       // setup callback for "reboot" method
       Client.SetMethodHandlerAsync("reboot", onReboot, null).Wait();
       Console.WriteLine("Waiting for reboot method\n Press enter to exit.");
       Console.ReadLine();

       Console.WriteLine("Exiting...");

       // as a good practice, remove the "reboot" handler
       Client.SetMethodHandlerAsync("reboot", null, null).Wait();
       Client.CloseAsync().Wait();
   }
   catch (Exception ex)
   {
       Console.WriteLine();
       Console.WriteLine("Error in sample: {0}", ex.Message);
   }
   ```

1. ソリューション エクスプローラーで、ソリューションを右クリックし、 **[スタートアップ プロジェクトの設定]** を選択します。

1. **[共通プロパティ]**  >  **[スタートアップ プロジェクト]** で **[シングル スタートアップ プロジェクト]** を選択し、**SimulateManagedDevice** プロジェクトを選択します。 **[OK]** を選択して変更を保存します。

1. **[ビルド]**  >  **[ビルドするソリューション]** を選択します。

> [!NOTE]
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用コードでは、「[一時的な障害の処理](/azure/architecture/best-practices/transient-faults)」で推奨されているように、再試行ポリシー (エクスポネンシャル バックオフなど) を実装してください。

## <a name="run-the-apps"></a>アプリの実行

これで、アプリを実行する準備が整いました。

1. .Net デバイス アプリ **SimulateManagedDevice** を実行するには、ソリューション エクスプローラーで **SimulateManagedDevice** プロジェクトを右クリックし、 **[デバッグ]** を選択し、 **[新しいインスタンスを開始]** を選択します。 アプリで IoT ハブからメソッド呼び出しのリッスンが開始されます。

1. デバイスが接続され、メソッドの呼び出しを待ってから、**TriggerReboot** プロジェクトを右クリックし、 **[デバッグ]** 、 **[新しいインスタンスを開始]** の順に選択します。

   "Rebooting!"\(再起動中\) というメッセージが **SimulatedManagedDevice** コンソールに出力されます。また、デバイスのレポートされたプロパティ (最終再起動時間など) が **TriggerReboot** コンソールに出力されます。

    ![サービスとデバイス アプリの実行](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
