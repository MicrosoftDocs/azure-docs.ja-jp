---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 4308dd2b63b33604af83b360e5c1c0f02a3dec27
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95487817"
---
IoT プラグ アンド プレイを使用すると、基盤となるデバイスの実装に関する知識がなくてもデバイスの機能とやり取りできるので、IoT が簡略化されます。 このクイックスタートでは、C# を使用して、ご利用のソリューションに接続されている IoT プラグ アンド プレイ デバイスに接続して制御する方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Windows 上でこのクイックスタートを実行するには、開発用マシンに次のソフトウェアがインストールされている必要があります。

* [Visual Studio (Community、Professional、または Enterprise)](https://visualstudio.microsoft.com/downloads/)。
* [Git](https://git-scm.com/download/).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>サンプル コードを使用して SDK リポジトリをクローンする

「[クイックスタート: Windows 上で実行されている IoT プラグ アンド プレイのサンプル デバイス アプリケーションを IoT Hub に接続する (C#)](../articles/iot-pnp/quickstart-connect-device.md)」を完了している場合は、リポジトリを既にクローンしています。

Azure IoT Samples for C# GitHub リポジトリからサンプルをクローンします。 任意のフォルダーでコマンド プロンプトを開きます。 次のコマンドを実行して、[Microsoft Azure IoT Samples for .NET](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub リポジトリをクローンします。

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="run-the-sample-device"></a>サンプル デバイスを実行する

このクイックスタートでは、IoT プラグ アンド プレイ デバイスとして、C# で記述されたサンプルのサーモスタット デバイスを使用します。 サンプル デバイスを実行するには、次のようにします。

1. Visual Studio 2019 で、*azure-iot-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* プロジェクト ファイルを開きます。

1. Visual Studio で、 **[プロジェクト] > [Thermostat のプロパティ] > [デバッグ]** の順に移動します。 プロジェクトに次の環境変数を追加します。

    | 名前 | 値 |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | [環境の設定](../articles/iot-pnp/set-up-environment.md)の完了時に書き留めておいた値 |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | my-pnp-device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | [環境の設定](../articles/iot-pnp/set-up-environment.md)の完了時に書き留めておいた値 |

1. これで、Visual Studio でサンプルをビルドし、デバッグ モードで実行できるようになりました。

1. デバイスが情報を送信し、自身がオンラインであると報告したことを示すメッセージが表示されます。 これらのメッセージは、デバイスによってハブへのテレメトリ データの送信が開始され、コマンドとプロパティの更新情報を受信する準備ができたことを示します。 Visual Studio のこのインスタンスは閉じないでください。これは、サービスのサンプルが動作していることを確認するために必要になります。

## <a name="run-the-sample-solution"></a>サンプル ソリューションを実行する

「[IoT プラグ アンド プレイのクイックスタートとチュートリアル用の環境の設定](../articles/iot-pnp/set-up-environment.md)」では、IoT ハブとデバイスに接続するようにサンプルを構成するための 2 つの環境変数を作成しました。

* **IOTHUB_CONNECTION_STRING**: 先ほどメモした IoT ハブ接続文字列。
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"`。

このクイックスタートでは、C# のサンプルの IoT ソリューションを使用して、先ほど設定したサンプル デバイスとやり取りします。

1. Visual Studio の別のインスタンスで、*azure-iot-samples-csharp\iot-hub\Samples\service\PnpServiceSamples\Thermostat\Thermostat.csproj* プロジェクトを開きます。

1. Visual Studio で、 **[プロジェクト] > [Thermostat のプロパティ] > [デバッグ]** の順に移動します。 プロジェクトに次の環境変数を追加します。

    | 名前 | 値 |
    | ---- | ----- |
    | IOTHUB_DEVICE_ID | my-pnp-device |
    | IOTHUB_CONNECTION_STRING | [環境の設定](../articles/iot-pnp/set-up-environment.md)の完了時に書き留めておいた値 |

1. これで、Visual Studio でサンプルをビルドし、デバッグ モードで実行できるようになりました。

### <a name="get-device-twin"></a>デバイス ツインを取得する

次のコード スニペットは、サービス アプリケーションがデバイス ツインを取得する方法を示しています。

```C#
// Get a Twin and retrieves model Id set by Device client
Twin twin = await s_registryManager.GetTwinAsync(s_deviceId);
s_logger.LogDebug($"Model Id of this Twin is: {twin.ModelId}");
```

> [!NOTE]
> このサンプルでは、**IoT Hub サービス クライアント** の **Microsoft.Azure.Devices.Client** 名前空間を使用します。 デジタル ツイン API を含む API の詳細については、[サービス開発者ガイド](../articles/iot-pnp/concepts-developer-guide-service.md)を参照してください。

このコードは、次の出力を生成します。

```cmd
[09/21/2020 11:26:04]dbug: Thermostat.Program[0]
      Model Id of this Twin is: dtmi:com:example:Thermostat;1
```

次のコード スニペットは、"*パッチ*" を使用して、デバイス ツインを介してプロパティを更新する方法を示しています。

```C#
// Update the twin
var twinPatch = new Twin();
twinPatch.Properties.Desired[PropertyName] = PropertyValue;
await s_registryManager.UpdateTwinAsync(s_deviceId, twinPatch, twin.ETag);
```

このコードによって、サービスが `targetTemperature` プロパティを更新するときに、デバイスから次の出力が生成されます。

```cmd
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Property: Received - { "targetTemperature": 60°C }.
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Property: Update - {"targetTemperature": 60°C } is InProgress.

...

[09/21/2020 11:26:17]dbug: Thermostat.ThermostatSample[0]
      Property: Update - {"targetTemperature": 60°C } is Completed.
```

### <a name="invoke-a-command"></a>コマンドを呼び出す

次のコード スニペットは、コマンドを呼び出す方法を示しています。

```csharp
private static async Task InvokeCommandAsync()
{
    var commandInvocation = new CloudToDeviceMethod(CommandName) { ResponseTimeout = TimeSpan.FromSeconds(30) };

    // Set command payload
    string componentCommandPayload = JsonConvert.SerializeObject(s_dateTime);
    commandInvocation.SetPayloadJson(componentCommandPayload);

    CloudToDeviceMethodResult result = await s_serviceClient.InvokeDeviceMethodAsync(s_deviceId, commandInvocation);

    if (result == null)
    {
        throw new Exception($"Command {CommandName} invocation returned null");
    }

    s_logger.LogDebug($"Command {CommandName} invocation result status is: {result.Status}");
}
```

このコードによって、サービスが `getMaxMinReport` コマンドを呼び出すときに、デバイスから次の出力が生成されます。

```cmd
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Command: Received - Generating max, min and avg temperature report since 21/09/2020 11:25:58.
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Command: MaxMinReport since 21/09/2020 11:25:58: maxTemp=32, minTemp=32, avgTemp=32, startTime=21/09/2020 11:25:59, endTime=21/09/2020 11:26:04
```
