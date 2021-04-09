---
title: Azure IoT Hub モジュール ID とモジュール ツイン (.NET) の概要
description: モジュール ID を作成し、IoT SDK を使用して .NET のモジュール ツインを更新する方法を説明します。
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: menchi
ms.custom: amqp, devx-track-csharp
ms.openlocfilehash: daba400b9daadf464c4c125ad266745237e71367
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92142512"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-net"></a>IoT Hub モジュール ID とモジュール ツイン (.NET) の概要

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [モジュール ID とモジュール ツイン](iot-hub-devguide-module-twins.md)は Azure IoT Hub のデバイス ID とデバイス ツインに類似していますが、より細かい粒度で設定できます。 Azure IoT Hub のデバイス ID とデバイス ツインを使用した場合、バックエンド アプリケーションからデバイスを構成し、デバイスの状態を可視化できるのに対し、モジュール ID とモジュール ツインでは、デバイスのコンポーネントごとにこれらの機能を実現できます。 複数のコンポーネントで構成され、この機能をサポートしているデバイス (オペレーティング システム ベースのデバイスやファームウェア デバイスなど) 上では、モジュール ID とモジュール ツインにより、各コンポーネントの構成と状態を分離することができます。

このチュートリアルの最後には、次の 2 つの .NET コンソール アプリが完成します。

* **CreateIdentities**。 このアプリでは、デバイス ID、モジュール ID と、関連付けられたセキュリティ キーが作成されてデバイスおよびモジュール クライアントが接続されます。

* **UpdateModuleTwinReportedProperties**。 このアプリでは、更新されたモジュール ツインによって報告されたプロパティが IoT ハブに送信されます。

> [!NOTE]
> デバイス上で動作するアプリケーションの作成とソリューションのバックエンドで動作するアプリケーションの開発に利用できる各種 Azure IoT SDK については、「[Azure IoT SDK](iot-hub-devguide-sdks.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* 見ることができます。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 アカウントがない場合は、 [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を数分で作成することができます。

## <a name="create-a-hub"></a>ハブを作成する

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>IoT ハブ接続文字列を取得する

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]

## <a name="update-the-module-twin-using-net-device-sdk"></a>.NET デバイス SDK を使用してモジュール ツインを更新する

このセクションでは、モジュール ツインによって報告されるプロパティを更新する、シミュレートされたデバイス上に.NET を作成するコンソール アプリを作成します。

まず、モジュールの接続文字列を取得します。 [Azure portal](https://portal.azure.com/) にサインインします。 ご自身のハブに移動し、 **[IoT デバイス]** を選択します。 **myFirstDevice** を探します。 **[myFirstDevice]** を選択して開き、 **[myFirstModule]** を選択して開きます。 **[モジュール ID の詳細]** で、次の手順で必要な場合に **[接続文字列 (主キー)]** をコピーします。

   ![Azure Portal モジュールの詳細](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-identity-detail.png)

1. Visual Studio で、 **[ファイル]**  >  **[新規作成]**  >  **[プロジェクト]** の順に選択し、ソリューションに新しいプロジェクトを追加します。 [新しいプロジェクトの作成] で、 **[コンソール アプリ (.NET Framework)]** を選択し、 **[次へ]** を選択します。

1. プロジェクトの名前を *UpdateModuleTwinReportedProperties* に設定します。 **[ソリューション]** で、 **[ソリューションに追加]** を選択します。 .NET Framework のバージョンが 4.6.1 以降であることを確認します。

    ![Visual Studio プロジェクトを作成する](./media/iot-hub-csharp-csharp-module-twin-getstarted/configure-update-twins-csharp1.png)

1. **[作成]** を選択してプロジェクトを作成します。

1. Visual Studio で、 **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[ソリューションの NuGet パッケージの管理]** を開きます。 **[参照]** タブを選択します。

1. **Microsoft.Azure.Devices.Client** を検索して選択し、 **[インストール]** を選択します。

    !["Microsoft.Azure.Devices.Client" が選択されて、[インストール] ボタンが強調表示されているスクリーンショット。](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-client-sdk.png)

1. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    ```

1. **Program** クラスに次のフィールドを追加します。 プレースホルダーの値をモジュールの接続文字列に置き換えます。

    ```csharp
    private const string ModuleConnectionString = "<Your module connection string>";
    private static ModuleClient Client = null;
    static void ConnectionStatusChangeHandler(ConnectionStatus status, 
      ConnectionStatusChangeReason reason)
    {
        Console.WriteLine("Connection Status Changed to {0}; the reason is {1}", 
          status, reason);
    }
    ```

1. 次の **OnDesiredPropertyChanged** メソッドを **Program** クラスに追加します。

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
        {
            Console.WriteLine("desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
            Console.WriteLine("Sending current time as reported property");
            TwinCollection reportedProperties = new TwinCollection
            {
                ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
            };

            await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
        }
    ```

1. **Main** メソッドに次の行を追加します。

    ```csharp
    static void Main(string[] args)
    {
        Microsoft.Azure.Devices.Client.TransportType transport = 
          Microsoft.Azure.Devices.Client.TransportType.Amqp;

        try
        {
            Client = 
              ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
            Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

            Console.WriteLine("Retrieving twin");
            var twinTask = Client.GetTwinAsync();
            twinTask.Wait();
            var twin = twinTask.Result;
            Console.WriteLine(JsonConvert.SerializeObject(twin.Properties)); 

            Console.WriteLine("Sending app start time as reported property");
            TwinCollection reportedProperties = new TwinCollection();
            reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;

            Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (AggregateException ex)
        {
            Console.WriteLine("Error in sample: {0}", ex);
        }

        Console.WriteLine("Waiting for Events.  Press enter to exit...");
        Console.ReadLine();
        Client.CloseAsync().Wait();
    }
    ```

    このコード サンプルは、モジュール ツインを取得し、報告されたプロパティを AMQP プロトコルを使って更新する方法を示したものです。 パブリック プレビューでは、AMQP はモジュール ツインの操作用にのみサポートされています。

1. 必要に応じて、これらのステートメントを **Main** メソッドに追加して、モジュールから IoT Hub にイベントを送信できます。 これらの行を `try catch` ブロックの下に配置します。

    ```csharp
    Byte[] bytes = new Byte[2];
    bytes[0] = 0;
    bytes[1] = 1;
    var sendEventsTask = Client.SendEventAsync(new Message(bytes));
    sendEventsTask.Wait();
    Console.WriteLine("Event sent to IoT Hub.");
    ```

## <a name="run-the-apps"></a>アプリの実行

これで、アプリケーションを実行できます。

1. Visual Studio の **ソリューション エクスプローラー** でソリューションを右クリックし、 **[スタートアップ プロジェクトの設定]** を選択します。

1. **[共通プロパティ]** の下の **[スタートアップ プロジェクト]** を選択します。

1. アプリに対するアクションとして、 **[マルチ スタートアップ プロジェクト]** を選択し、 **[起動する]** を選択し、 **[OK]** をクリックして変更を受け入れます。

1. **F5** キーを押してアプリを起動します。

## <a name="next-steps"></a>次のステップ

引き続き IoT Hub の使用方法を確認すると共に、他の IoT のシナリオについて調べるには、次のページを参照してください。

* [デバイス管理の概要](iot-hub-node-node-device-management-get-started.md)

* [IoT Edge の概要](../iot-edge/quickstart-linux.md)