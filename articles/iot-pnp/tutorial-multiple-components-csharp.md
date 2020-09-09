---
title: IoT プラグ アンド プレイ プレビューのサンプル C# コンポーネント デバイス コードを IoT Hub に接続する | Microsoft Docs
description: 複数のコンポーネントを使用し、IoT ハブに接続する、IoT プラグ アンド プレイ プレビューのサンプル C# デバイス コードをビルドして実行します。 Azure IoT Explorer ツールを使用して、デバイスからハブに送信された情報を表示します。
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 67b71399332fb29a277381a8c2806dbe7fb31d85
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/04/2020
ms.locfileid: "87552129"
---
# <a name="tutorial-connect-an-iot-plug-and-play-multiple-component-device-application-running-on-windows-to-iot-hub-c"></a>チュートリアル:Windows 上で実行されている IoT プラグ アンド プレイ プレビューの複数コンポーネントのデバイス アプリケーションを IoT Hub に接続する (C#)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

このチュートリアルでは、コンポーネントとルート インターフェイスを使用する IoT プラグ アンド プレイ デバイス アプリケーションのサンプルをビルドし、それをご利用の IoT ハブに接続し、ハブに送信される情報を Azure IoT エクスプローラー ツールを使用して表示する方法を示します。 このサンプル アプリケーションは C# で記述されており、C# 用 Azure IoT device SDK に含まれています。 ソリューション ビルダーは Azure IoT エクスプローラー ツールを使用して、デバイス コードを表示しなくても IoT プラグ アンド プレイ デバイスの機能を理解することができます。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>前提条件

Windows でこのチュートリアルを完了するには、ご利用のローカル Windows 環境に次のソフトウェアをインストールします。

* [Visual Studio (Community、Professional、または Enterprise)](https://visualstudio.microsoft.com/downloads/)。
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/)。

### <a name="azure-iot-explorer"></a>Azure IoT エクスプローラー

このチュートリアルのパート 2 でサンプル デバイスとやり取りするには、**Azure IoT エクスプローラー** ツールを使用します。 ご利用のオペレーティング システム用の [Azure IoT エクスプローラーの最新リリースをダウンロードしてインストール](./howto-use-iot-explorer.md)します。

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

次のコマンドを実行して、ご利用のハブに対する "_IoT ハブ接続文字列_" を取得します。 この接続文字列はメモしておいてください。これは、このチュートリアルの後半で使用します。

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> また、Azure IoT エクスプローラー ツールを使用して、IoT ハブ接続文字列を見つけることもできます。

次のコマンドを実行して、ハブに追加したデバイスの "_デバイス接続文字列_" を取得します。 この接続文字列はメモしておいてください。これは、このチュートリアルの後半で使用します。

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>コードのダウンロード

このチュートリアルでは、Azure IoT Hub Device C# SDK をクローンしてビルドするために使用できる開発環境を準備します。

任意のディレクトリでコマンド プロンプトを開きます。 次のコマンドを実行して、[Azure IoT C# SDK およびライブラリ](https://github.com/Azure/azure-iot-sdk-csharp)の GitHub リポジトリをこの場所にクローンします。

```cmd
git clone https://github.com/Azure/azure-iot-sdk-csharp.git
```

## <a name="build-the-code"></a>コードのビルド

Visual Studio 2019 で **azureiot.sln** ソリューション ファイルを開き、**TemperatureController** プロジェクトをスタートアップ プロジェクトとして設定します。 **ソリューション エクスプローラー**では、このプロジェクト ファイルを **iothub > device > samples** で見つけることができます。

これで、Visual Studio でサンプルをビルドし、デバッグ モードで実行できるようになりました。

## <a name="run-the-device-sample"></a>デバイス サンプルを実行する

先ほどメモしたデバイス接続文字列を格納するために、**IOTHUB_DEVICE_CONNECTION_STRING** という環境変数を作成します。

Windows 上の Visual Studio でコードの実行をトレースするには、program.cs ファイルの `main` 関数にブレーク ポイントを追加します。

これで、デバイスはコマンドとプロパティの更新情報を受信する準備ができ、ハブへのテレメトリ データの送信が開始されました。 次の手順を完了するまで、サンプルを実行したままにしておきます。

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Azure IoT エクスプローラーを使用してコードを確認する

デバイス クライアントのサンプルが開始された後、Azure IoT エクスプローラー ツールを使用して、それが動作していることを確認します。

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>コードの確認

このサンプルにより、IoT プラグ アンド プレイの温度コントローラー デバイスが実装されます。 このサンプルによって実装されるモデルにより、[複数のコンポーネント](concepts-components.md)が使用されます。 [温度デバイスの Digital Twins Definition Language (DTDL) モデル ファイル](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)により、デバイスによって実装されるテレメトリ、プロパティ、およびコマンドが定義されます。

デバイス コードにより、標準の `CreateFromConnectionString` メソッドが使用され、IoT ハブに接続されます。 デバイスにより、接続要求で実装される DTDL モデルのモデル ID が送信されます。 モデル ID を送信するデバイスは、IoT プラグ アンド プレイ デバイスです。

```csharp
private static void InitializeDeviceClientAsync()
{
  var options = new ClientOptions
  {
      ModelId = ModelId,
  };
  s_deviceClient = DeviceClient.CreateFromConnectionString(s_deviceConnectionString, TransportType.Mqtt, options);
  s_deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
  {
      s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
  });
}
```

モデル ID は、次のスニペットに示すように、コードに格納されます。

```csharp
private const string ModelId = "dtmi:com:example:Thermostat;1";
```

デバイスが IoT ハブに接続された後、コードによってコマンド ハンドラーが登録されます。 `reboot` コマンドは、ルート インターフェイスで定義されています。 `getMaxMinReport` コマンドは、次の 2 つのサーモスタット コンポーネントのそれぞれに定義されています。

```csharp
await s_deviceClient.SetMethodHandlerAsync("reboot", HandleRebootCommandAsync, s_deviceClient);
await s_deviceClient.SetMethodHandlerAsync("thermostat1*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat1);
await s_deviceClient.SetMethodHandlerAsync("thermostat2*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat2);
```

2 つのサーモスタット コンポーネントには、必要なプロパティの更新用の個別のハンドラーがあります。

```csharp
s_desiredPropertyUpdateCallbacks.Add(Thermostat1, TargetTemperatureUpdateCallbackAsync);
s_desiredPropertyUpdateCallbacks.Add(Thermostat2, TargetTemperatureUpdateCallbackAsync);
```

このサンプル コードによって、各サーモスタット コンポーネントからテレメトリが送信されます。

```csharp
await SendTemperatureAsync(Thermostat1);
await SendTemperatureAsync(Thermostat2);
```

`SendTemperature` メソッドによって、各コンポーネントのメッセージを作成するために `PnpHhelper` クラスが使用されます。

```csharp
Message msg = PnpHelper.CreateIothubMessageUtf8(telemetryName, JsonConvert.SerializeObject(currentTemperature), componentName);
```

`PnpHelper` クラスには、複数コンポーネントのモデルで使用できるその他のサンプル メソッドが含まれています。

2 つのサーモスタット コンポーネントからのテレメトリとプロパティを表示するには、Azure IoT エクスプローラー ツールを使用します。

:::image type="content" source="media/tutorial-multiple-components-csharp/multiple-component.png" alt-text="Azure IoT エクスプローラーの複数コンポーネントのデバイス":::

また、Azure IoT エクスプローラー ツールを使用して、2 つのサーモスタット コンポーネントのいずれか、またはルート インターフェイスでコマンドを呼び出すこともできます。

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、コンポーネントを使用する IoT プラグ アンド プレイ デバイスを IoT ハブに接続する方法を学習しました。 IoT プラグ アンド プレイ デバイス モデルの詳細については、以下を参照してください。

> [!div class="nextstepaction"]
> [IoT プラグ アンド プレイ プレビュー モデリング開発者ガイド](concepts-developer-guide.md)
