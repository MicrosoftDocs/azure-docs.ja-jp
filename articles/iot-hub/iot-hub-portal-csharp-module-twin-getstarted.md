---
title: Azure IoT Hub のモジュール ID とモジュール ツイン (ポータルと .NET)
description: モジュール ID を作成し、ポータルと .NET を使用してモジュール ツインを更新する方法について説明します。
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: amqp, devx-track-csharp
ms.openlocfilehash: 0d16d7a6dbf903ae790015c12e3d34e4a2a553d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92139354"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>ポータルと .NET デバイスを使用した Azure IoT Hub モジュール ID とモジュール ツインの概要

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [モジュール ID とモジュール ツイン](iot-hub-devguide-module-twins.md)は Azure IoT Hub のデバイス ID とデバイス ツインに類似していますが、より細かい粒度で設定できます。 Azure IoT Hub のデバイス ID とデバイス ツインを使用した場合、バックエンド アプリケーションからデバイスを構成し、デバイスの状態を可視化できるのに対し、モジュール ID とモジュール ツインでは、デバイスのコンポーネントごとにこれらの機能を実現できます。 複数のコンポーネントで構成され、この機能をサポートしているデバイス (オペレーティング システム ベースのデバイスやファームウェア デバイスなど) 上では、モジュール ID とモジュール ツインにより、各コンポーネントの構成と状態を分離することができます。
>

このチュートリアルでは、次の事項について説明します。

* モジュール ID をポータルで作成する方法。

* .NET デバイス SDK を使用して、モジュール ツインをデバイスから更新する方法。

> [!NOTE]
> デバイス上で動作するアプリケーションとソリューションのバックエンドで動作するアプリケーションの両方をビルドするために使用できる各種 Azure IoT SDK については、「[Azure IoT SDK](iot-hub-devguide-sdks.md)」を参照してください。
>

## <a name="prerequisites"></a>前提条件

* 見ることができます。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 アカウントがない場合は、 [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を数分で作成することができます。

## <a name="create-a-hub"></a>ハブを作成する

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-hub"></a>ハブに新しいデバイスを登録する

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>モジュール ID をポータルで作成する

1 つのデバイス ID 内には、最大 20 個のモジュール ID を作成できます。 ID を追加するには、次の手順を実行します。

1. 前のセクションで作成したデバイスに対して **[モジュール ID の追加]** を選択して、最初のモジュール ID を作成します。

1. *myFirstModule* という名前を入力します。 モジュール ID を保存します。

    ![モジュール ID を追加する](./media/iot-hub-portal-csharp-module-twin-getstarted/add-module-identity.png)

    新しいモジュール ID は画面の下部に表示されます。 それを選択すると、モジュール ID の詳細が表示されます。

    ![モジュール ID の詳細の表示](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

**[接続文字列 - 主キー]** を保存します。 次のセクションでそれを使用して、デバイス上にモジュールを設定します。

## <a name="update-the-module-twin-using-net-device-sdk"></a>.NET デバイス SDK を使用してモジュール ツインを更新する

IoT Hub 内にモジュール ID が正常に作成されたら、 シミュレートされたデバイスから、クラウドへの通信を試してみましょう。 モジュール ID が作成されると、IoT Hub 内でモジュール ツインが暗黙的に作成されます。 このセクションでは、シミュレートされたデバイス上に .NET コンソール アプリを作成し、モジュール ツインによって報告されたプロパティを更新します。

### <a name="create-a-visual-studio-project"></a>Visual Studio プロジェクトを作成する

モジュール ツインの報告されるプロパティを更新するアプリを作成するには、次の手順を実行します。

1. Visual Studio で、 **[新しいプロジェクトの作成]** を選択します。 **[コンソール アプリ (.NET Framework)]** を選択し、 **[次へ]** を選択します。

1. **[新しいプロジェクトの構成]** で、 **[プロジェクト名]** として「*UpdateModuleTwinReportedProperties*」と入力します。 **[作成]** をクリックして続行します。

    ![Visual Studio プロジェクトを構成する](./media/iot-hub-portal-csharp-module-twin-getstarted/configure-twins-project.png)

### <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>最新の Azure IoT Hub .NET デバイス SDK をインストールする

モジュール ID とモジュール ツインはパブリック プレビュー中です。 これらは、IoT Hub プレリリース デバイス版の SDK でのみ使えます。 インストールするには、次の手順に従います。

1. Visual Studio で、 **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[ソリューションの NuGet パッケージの管理]** を開きます。

1. **[参照]** を選択し、 **[プレリリースを含める]** を選択します。 「*Microsoft.Azure.Devices.Client*」を検索します。 最新のバージョンを選択し、インストールします。

    ![Azure IoT Hub .NET service SDK プレビューをインストールする](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

    これで、モジュールのすべての機能を使用できるようになりました。

### <a name="get-your-module-connection-string"></a>モジュールの接続文字列を取得する

コンソール アプリ用のモジュールの接続文字列が必要です。 次の手順に従います。

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. お使いの IoT ハブに移動し、 **[IoT デバイス]** を選択します。 **myFirstDevice** を開くと、**myFirstModule** が正常に作成されていることを確認できます。

1. **[モジュール ID]** の下の **[myFirstModule]** を選択します。 **[モジュール ID の詳細]** で、 **[接続文字列 (主キー)]** をコピーします。

    ![Azure Portal モジュールの詳細](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

### <a name="create-updatemoduletwinreportedproperties-console-app"></a>UpdateModuleTwinReportedProperties コンソール アプリを作成する

アプリを作成するには、次の手順を実行します。

1. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。

  ```csharp
  using Microsoft.Azure.Devices.Client;
  using Microsoft.Azure.Devices.Shared;
  using Newtonsoft.Json;
  ```

2. **Program** クラスに次のフィールドを追加します。 プレースホルダーの値をモジュールの接続文字列に置き換えます。

  ```csharp
  private const string ModuleConnectionString = "<Your module connection string>";
  private static ModuleClient Client = null;
  ```

3. 次の **OnDesiredPropertyChanged** メソッドを **Program** クラスに追加します。

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

4. 最後に、**Main** メソッドを次のコードに置き換えます。

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
  
  **F5** キーを押すことで、このアプリをビルドして実行できます。

このコード サンプルは、モジュール ツインを取得し、報告されたプロパティを AMQP プロトコルを使って更新する方法を示したものです。 パブリック プレビューでは、AMQP はモジュール ツインの操作用にのみサポートされています。

## <a name="next-steps"></a>次のステップ

引き続き IoT Hub の使用方法を確認すると共に、他の IoT のシナリオについて調べるには、次のページを参照してください。

* [.NET バックアップおよび .NET デバイスを使用した IoT Hub モジュール ID とモジュール ツインの概要](iot-hub-csharp-csharp-module-twin-getstarted.md)

* [IoT Edge の概要](../iot-edge/quickstart-linux.md)