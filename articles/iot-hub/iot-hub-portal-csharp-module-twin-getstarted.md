---
title: Azure IoT Hub モジュール ID とモジュール ツイン (ポータルと .NET) の概要 | Microsoft Docs
description: モジュール ID を作成し、ポータルと .NET を使用してモジュール ツインを更新する方法について説明します。
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/26/2018
ms.openlocfilehash: ce71c64aff66ea94282a82c1f1b1ee564e74f192
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68403894"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>ポータルと .NET デバイスを使用した Azure IoT Hub モジュール ID とモジュール ツインの概要

> [!NOTE]
> [モジュール ID とモジュール ツイン](iot-hub-devguide-module-twins.md)は Azure IoT Hub のデバイス ID とデバイス ツインに類似していますが、より細かい粒度で設定できます。 Azure IoT Hub のデバイス ID とデバイス ツインを使用した場合、バックエンド アプリケーションからデバイスを構成し、デバイスの状態を可視化できるのに対し、モジュール ID とモジュール ツインでは、デバイスの各コンポーネントごとにこれらの機能を実現できます。 複数のコンポーネントで構成され、この機能をサポートしているデバイス (オペレーティング システム ベースのデバイスやファームウェア デバイスなど) であれば、各コンポーネントの状態を可視化し、個別に構成することができます。
>

このチュートリアルでは、次の事項について説明します。

1. モジュール ID をポータルで作成する方法。

2. .NET デバイス SDK を使用して、モジュール ツインをデバイスから更新する方法。

> [!NOTE]
> デバイス上で動作するアプリケーションの作成とソリューションのバックエンドで動作するアプリケーションの開発に利用できる各種 Azure IoT SDK については、「[Azure IoT SDK](iot-hub-devguide-sdks.md)」を参照してください。
>

このチュートリアルを完了するには、以下が必要です。

* 見ることができます。
* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 (アカウントがない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を数分で作成できます)。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT ハブに新しいデバイスを登録する

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>モジュール ID をポータルで作成する

1 つのデバイス ID 内には、最大 20 個のモジュール ID を作成できます。 上部の **[Add Module Identity]\(モジュール ID の追加\)** ボタンクリックして、最初のモジュール ID を **myFirstModule** という名前で作成します。

  ![[デバイスの詳細]](./media/iot-hub-portal-csharp-module-twin-getstarted/create-module-id.png)

保存し、作成したモジュール ID をクリックします。 モジュール ID の詳細が表示されます。 [Connection string - Primary Key]\(接続文字列 – 主キー\) の値をコピーします。 これは、次のセクション (デバイス上でモジュールを設定するセクション) で使用します。

  ![[デバイスの詳細]](./media/iot-hub-portal-csharp-module-twin-getstarted/module-details.png)

## <a name="update-the-module-twin-using-net-device-sdk"></a>.NET デバイス SDK を使用してモジュール ツインを更新する

IoT Hub 内にモジュール ID が正常に作成されたら、 シミュレートされたデバイスから、クラウドへの通信を試してみましょう。 モジュール ID が作成されると、IoT Hub 内でモジュール ツインが暗黙的に作成されます。 このセクションでは、シミュレートされたデバイス上に .NET コンソール アプリを作成し、モジュール ツインによって報告されたプロパティを更新します。

## <a name="create-a-visual-studio-project"></a>Visual Studio プロジェクトを作成する

Visual Studio で、 **[Console App (.NET Framework)]** プロジェクト テンプレートを使用し、Visual C# Windows クラシック デスクトップ プロジェクトを既存のソリューションに追加します。 .NET Framework のバージョンが 4.6.1 以降であることを確認します。 プロジェクトの名前を **UpdateModuleTwinReportedProperties** に設定します。

  ![Visual Studio プロジェクトを作成する](./media/iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.png)

## <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>最新の Azure IoT Hub .NET デバイス SDK をインストールする

モジュール ID とモジュール ツインはパブリック プレビュー中です。 これらは、IoT Hub プレリリース デバイス版の SDK でのみ使えます。 Visual Studio で、[ツール] > [Nuget パッケージ マネージャー] > [ソリューションの Nuget パッケージの管理] の順に選択します。 Microsoft.Azure.Devices.Client を検索します。 [プレリリースを含める] チェック ボックスをオンにしてください。 最新のバージョンを選択し、インストールします。 これで、モジュールのすべての機能を使用できるようになりました。

  ![Azure IoT Hub .NET service SDK V1.16.0-preview-005 をインストールする](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

## <a name="get-your-module-connection-string"></a>モジュールの接続文字列を取得する

[Azure ポータル](https://portal.azure.com/)にサインインします。 IoT Hub に移動し、[IoT デバイス] をクリックします。 myFirstDevice を検索して開くと、myFirstModule が正常に作成されていることを確認できます。 モジュールの接続文字列をコピーします。 これは、次の手順で必要になります。

  ![Azure Portal モジュールの詳細](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-detail.png)

## <a name="create-updatemoduletwinreportedproperties-console-app"></a>UpdateModuleTwinReportedProperties コンソール アプリを作成する

**Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。

```csharp
using Microsoft.Azure.Devices.Client;
using Microsoft.Azure.Devices.Shared;
```

**Program** クラスに次のフィールドを追加します。 プレースホルダーの値をモジュールの接続文字列に置き換えます。

```csharp
private const string ModuleConnectionString = "<Your module connection string>";
private static ModuleClient Client = null;
```

次の **OnDesiredPropertyChanged** メソッドを **Program** クラスに追加します。

```csharp
private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
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

最後に、**Main** メソッドに次の行を追加します。

```csharp
static void Main(string[] args)
{
    Microsoft.Azure.Devices.Client.TransportType transport = Microsoft.Azure.Devices.Client.TransportType.Amqp;

    try
    {
        Client = ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
        Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

        Console.WriteLine("Retrieving twin");
        var twinTask = Client.GetTwinAsync();
        twinTask.Wait();
        var twin = twinTask.Result;
        Console.WriteLine(JsonConvert.SerializeObject(twin));

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
    Console.ReadKey();
    Client.CloseAsync().Wait();
}

private static void ConnectionStatusChangeHandler(ConnectionStatus status, ConnectionStatusChangeReason reason)
{
    Console.WriteLine($"Status {status} changed: {reason}");
}
```

このコード サンプルは、モジュール ツインを取得し、報告されたプロパティを AMQP プロトコルを使って更新する方法を示したものです。 パブリック プレビューでは、AMQP はモジュール ツインの操作用にのみサポートされています。

## <a name="run-the-apps"></a>アプリの実行

これで、アプリを実行する準備が整いました。 Visual Studio のソリューション エクスプローラーでソリューションを右クリックし、 **[スタートアップ プロジェクトの設定]** をクリックします。 コンソール アプリの動作として、 **[マルチ スタートアップ プロジェクト]** を選択し、 **[起動する]** を選択します。 次に、F5 キーを押して、両方のアプリケーションを実行します。

## <a name="next-steps"></a>次の手順

引き続き IoT Hub の使用方法を確認すると共に、他の IoT のシナリオについて調べるには、次のページを参照してください。

* [.NET バックアップおよび .NET デバイスを使用した IoT Hub モジュール ID とモジュール ツインの概要](iot-hub-csharp-csharp-module-twin-getstarted.md)

* [IoT Edge の概要](../iot-edge/tutorial-simulate-device-linux.md)
