---
title: Azure IoT Hub デバイス管理の開始 (.NET/.NET) | Microsoft Docs
description: Azure IoT Hub デバイス管理を使用してリモート デバイスの再起動を開始する方法。 .NET 用 Azure IoT device SDK を使用して、ダイレクト メソッドを含むシミュレートされたデバイス アプリを実装し、Azure IoT service SDK for .NET を使用して、ダイレクト メソッドを呼び出すサービス アプリを実装します。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 09/15/2017
ms.author: robinsh
ms.openlocfilehash: 556c10cc5ec5e528857a120dadb16c2a10202ed3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59788963"
---
# <a name="get-started-with-device-management-netnet"></a>デバイス管理の開始 (.NET/.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

このチュートリアルでは、次の操作方法について説明します。

* Azure Portal を使用して IoT Hub を作成し、IoT Hub 内にデバイス ID を作成します。

* デバイスを再起動するダイレクト メソッドを含む、シミュレション済みデバイス アプリを作成します。 ダイレクト メソッドは、クラウドから呼び出されます。

* シミュレートされたデバイス アプリで再起動ダイレクト メソッドを IoT Hub 経由で呼び出す .NET コンソール アプリを作成します。

このチュートリアルの最後には、次の 2 つの .NET コンソール アプリが完成します。

* **SimulateManagedDevice**: IoT Hub を、作成済みのデバイス ID に接続し、再起動ダイレクト メソッドを受信し、物理的な再起動をシミュレートし、最後の再起動時間を報告します。

* **TriggerReboot**: シミュレート対象デバイス アプリでダイレクト メソッドを呼び出し、応答を表示し、更新された報告されるプロパティを表示します。

このチュートリアルを完了するには、以下が必要です。

* Visual Studio 2017。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 (アカウントがない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を数分で作成できます)。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>IoT ハブに対する接続文字列を取得する

[!INCLUDE [iot-hub-find-include-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT ハブに新しいデバイスを登録する

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>ダイレクト メソッドを使用してデバイスのリモート再起動をトリガーする

このセクションでは、ダイレクト メソッドを使用してデバイスでのリモート再起動を開始する .NET コンソール アプリケーションを作成します (C# を使用します)。 このアプリは、デバイス ツイン クエリを使用して、そのデバイスの前回の再起動時刻を検出します。

1. Visual Studio で、**[Console App (.NET Framework)]** プロジェクト テンプレートを使用し、Visual C# Windows クラシック デスクトップ プロジェクトを新しいソリューションに追加します。 .NET Framework のバージョンが 4.5.1 以降であることを確認します。 プロジェクトに **TriggerReboot**という名前を付けます。

    ![New Visual C# Windows Classic Desktop project](./media/iot-hub-csharp-csharp-device-management-get-started/createserviceapp.png)

2. ソリューション エクスプローラーで **TriggerReboot** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。

3. **[NuGet パッケージ マネージャー]** ウィンドウで **[参照]** を選択し、**Microsoft.Azure.Devices** を検索します。**[インストール]** を選択して **Microsoft.Azure.Devices** パッケージをインストールし、使用条件に同意します。 この手順により、パッケージのダウンロードとインストールが実行され、[Azure IoT service SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet パッケージへの参照とその依存関係が追加されます。

    ![NuGet Package Manager window](./media/iot-hub-csharp-csharp-device-management-get-started/servicesdknuget.png)

4. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。
   
   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```
        
5. **Program** クラスに次のフィールドを追加します。 プレースホルダーの値は、「IoT Hub の作成」セクションで作成した IoT Hub の接続文字列に置き換えてください。 
   
   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

6. **Program** クラスに次のメソッドを追加します。  このコードは、再起動中のデバイスのデバイス ツインを取得し、報告されるプロパティを出力します。
   
   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```
        
7. **Program** クラスに次のメソッドを追加します。  このコードは、ダイレクト メソッドを使用してデバイスの再起動をトリガーします。

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

7. 最後に、**Main** メソッドに次の行を追加します。
   
   ```csharp
   registryManager = RegistryManager.CreateFromConnectionString(connString);
   StartReboot().Wait();
   QueryTwinRebootReported().Wait();
   Console.WriteLine("Press ENTER to exit.");
   Console.ReadLine();
   ```

8. ソリューションをビルドします。

> [!NOTE]
> このチュートリアルでは、デバイスでレポートされるプロパティ対して 1 つのクエリのみを実行します。 運用環境のコードでは、レポートされるプロパティの変化を検出するために、ポーリング処理をお勧めします。

## <a name="create-a-simulated-device-app"></a>シミュレート対象デバイス アプリの作成

このセクションでは、以下の手順を実行します。

* クラウドによって呼び出されたダイレクト メソッドに応答する .NET コンソール アプリを作成します。

* シミュレート対象デバイスの再起動をトリガーします。

* 報告されるプロパティを使用して、デバイス ツイン クエリで、デバイスとデバイスの最後の再起動時間を識別できるようにします。

1. Visual Studio で、 **[コンソール アプリケーション]** プロジェクト テンプレートを使用し、Visual C# Windows クラシック デスクトップ プロジェクトを現在のソリューションに追加します。 プロジェクトに **SimulateManagedDevice** という名前を付けます。
   
    ![New Visual C# Windows Classic device app](./media/iot-hub-csharp-csharp-device-management-get-started/createdeviceapp.png)
    
2. ソリューション エクスプローラーで **SimulateManagedDevice** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。

3. **[NuGet パッケージ マネージャー]** ウィンドウで **[参照]** を選択し、**Microsoft.Azure.Devices.Client** を検索します。 **[インストール]** を選択して、**Microsoft.Azure.Devices.Client** パッケージをインストールし、使用条件に同意します。 この手順により、パッケージのダウンロードとインストールが実行され、[Azure IoT device SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet パッケージへの参照とその依存関係が追加されます。
   
    ![NuGet Package Manager window Client app](./media/iot-hub-csharp-csharp-device-management-get-started/clientsdknuget.png)
    
4. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。
   
    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

5. **Program** クラスに次のフィールドを追加します。 プレースホルダーの値は、前のセクションで記したデバイス接続文字列に置き換えてください。

    ```csharp
    static string DeviceConnectionString = 
      "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```
6. デバイスにダイレクト メソッドを実装するために以下を追加します。

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

       string result = "'Reboot started.'";
       return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
   }
   ```

7. 最後に、IoT ハブへの接続を開いてメソッド リスナーを初期化する次のコードを **Main** メソッドに追加します。

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
        
8. Visual Studio のソリューション エクスプローラーでソリューションを右クリックし、**[スタートアップ プロジェクトの設定]** をクリックします。**[シングル スタートアップ プロジェクト]** を選択し、ドロップダウン メニューから **SimulateManagedDevice** プロジェクトを選択します。 ソリューションをビルドします。       

> [!NOTE]
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードでは、「[一時的な障害の処理](/azure/architecture/best-practices/transient-faults)」の記事で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。

## <a name="run-the-apps"></a>アプリの実行

これで、アプリを実行する準備が整いました。

1. .NET デバイス アプリ **SimulateManagedDevice** を実行するには、 **SimulateManagedDevice** プロジェクトを右クリックし、**[デバッグ]**、**[新しいインスタンスを開始]** の順に選択します。 次のように、IoT ハブからのメソッド呼び出しのリッスンが開始するはずです。 

2. デバイスが接続され、メソッドの呼び出しのために待機しています .NET **TriggerReboot** アプリを実行して、シミュレート対象デバイスのアプリで reboot メソッドを呼び出します。 そのためには、**TriggerReboot** プロジェクトを右クリックし、**[デバッグ]**、**[新しいインスタンスを開始]** を選択します。 "Rebooting!"\(再起動中\) というメッセージが **SimulatedManagedDevice** コンソールに出力されます。また、デバイスのレポートされたプロパティ (最終再起動時間など) が **TriggerReboot** コンソールに出力されます。
   
    ![サービスとデバイス アプリの実行](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
