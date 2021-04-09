---
title: チュートリアル - 汎用 Azure IoT プラグ アンド プレイのモジュールを接続する | Microsoft Docs
description: チュートリアル - 汎用モジュールでサンプルの C# IoT プラグ アンド プレイ デバイス コードを使用します。
author: ericmitt
ms.author: ericmitt
ms.date: 9/22/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 33eaa1ea928cc0650c91948c70d46daf499f3b4b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99831208"
---
# <a name="tutorial-connect-an-iot-plug-and-play-module-c"></a>チュートリアル:IoT プラグ アンド プレイ モジュールを接続する (C#)

このチュートリアルでは、汎用の IoT プラグ アンド プレイ [モジュール](../iot-hub/iot-hub-devguide-module-twins.md)の接続方法について説明します。

IoT ハブに接続するときにモデル ID を発行し、そのモデル ID によって識別される Digital Twins Definition Language (DTDL) モデルに記述されているプロパティとメソッドを実装するデバイスである場合は、IoT プラグ アンド プレイ デバイスです。 デバイスで DTDL とモデル ID がどのように使用されるかの詳細については、[IoT プラグ アンド プレイの開発者ガイド](./concepts-developer-guide-device.md)を参照してください。 モジュールでは、モデル ID と DTDL モデルを同じ方法で使用します。

このチュートリアルでは、IoT プラグ アンド プレイ モジュールの実装をデモンストレーションするために、次の方法を紹介します。

> [!div class="checklist"]
> * モジュールを含むデバイスを IoT ハブに追加する
> * サーモスタットの C# デバイス サンプルを汎用モジュールに変換する
> * サービス SDK を使用してモジュールを操作する。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Windows でこのチュートリアルを完了するには、ご利用のローカル Windows 環境に次のソフトウェアをインストールします。

* [Visual Studio (Community、Professional、または Enterprise)](https://visualstudio.microsoft.com/downloads/)。
* [Git](https://git-scm.com/download/).

Azure IoT エクスプローラー ツールを使用して、**my-module-device** という新しいデバイスを IoT ハブに追加します。

**my-module-device** に、**my-module** というモジュールを追加します。

1. Azure IoT エクスプローラー ツールで、**my-module-device** デバイスに移動します。

1. **[モジュール ID]** を選択してから、 **[+ 追加]** を選択します。

1. モジュール ID 名として「**my-module**」と入力し、 **[保存]** を選択します。

1. モジュール ID の一覧で **[my-module]** を選択します。 次に、プライマリ接続文字列をコピーします。 このチュートリアルでのちほど、このモジュール接続文字列が必要になります。

1. **[モジュール ツイン]** タブを選択し、必要なプロパティや報告されるプロパティがないことに注意します。

    ```json
    {
      "deviceId": "my-module-device",
      "moduleId": "my-module",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "NjgzMzQ1MzQ1",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00Z",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00Z",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "modelId": "",
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "0001-01-01T00:00:00Z"
          },
          "$version": 1
          },
          "reported": {
            "$metadata": {
              "$lastUpdated": "0001-01-01T00:00:00Z"
          },
          "$version": 1
        }
      }
    }
    ```

## <a name="download-the-code"></a>コードのダウンロード

まだそうしていない場合は、Azure IoT Hub Device C# SDK の GitHub リポジトリをローカル コンピューターにクローンします。

任意のフォルダーでコマンド プロンプトを開きます。 次のコマンドを実行して、[Azure IoT C# サンプル](https://github.com/Azure-Samples/azure-iot-samples-csharp)の GitHub リポジトリをこの場所にクローンします。

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="prepare-the-project"></a>プロジェクトを準備する

サンプル プロジェクトを開いて準備するには:

1. Visual Studio 2019 で、*azure-iot-sdk-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* プロジェクト ファイルを開きます。

1. Visual Studio で、 **[プロジェクト] > [Thermostat のプロパティ] > [デバッグ]** の順に移動します。 プロジェクトに次の環境変数を追加します。

    | 名前 | 値 |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | connectionString |
    | IOTHUB_MODULE_CONNECTION_STRING | 以前に書き留めたモジュール接続文字列 |

    サンプル構成の詳細については、[サンプルの readme](https://github.com/Azure-Samples/azure-iot-samples-csharp/blob/master/iot-hub/Samples/device/PnpDeviceSamples/readme.md) を参照してください。

## <a name="modify-the-code"></a>コードを変更する

デバイスではなくモジュールとして動作するようにコードを変更するには:

1. Visual Studio で *Parameter.cs* を開き、**PrimaryConnectionString** 変数を設定する行を次のように変更します。

    ```csharp
    public string PrimaryConnectionString { get; set; } = Environment.GetEnvironmentVariable("IOTHUB_MODULE_CONNECTION_STRING");
    ```

1. Visual Studio で *Program.cs* を開き、`DeviceClient` クラスの 7 つのインスタンスを `ModuleClient` クラスに置き換えます。

    > [!TIP]
    > Visual Studio の検索と置換の機能を、 **[大文字と小文字を区別する]** と **[単語単位]** を有効にして使用し、`DeviceClient` を `ModuleClient` に置き換えます。

1. Visual Studio で *Thermostat.cs* を開き、下記のように `DeviceClient` クラスの両方のインスタンスを `ModuleClient` クラスに置き換えます。

1. 変更したファイルに対する変更を保存します。

コードの実行後、Azure IoT エクスプローラーを使用して更新されたモジュール ツインを表示すると、モデル ID とモジュールから報告されたプロパティで更新されたデバイス ツインが表示されます。

```json
{
  "deviceId": "my-module-device",
  "moduleId": "my-mod",
  "etag": "AAAAAAAAAAE=",
  "deviceEtag": "NjgzMzQ1MzQ1",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Connected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "modelId": "dtmi:com:example:Thermostat;1",
  "version": 3,
  "properties": {
    "desired": {
      "$metadata": {
        "$lastUpdated": "0001-01-01T00:00:00Z"
      },
      "$version": 1
    },
    "reported": {
      "maxTempSinceLastReboot": 5,
      "$metadata": {
        "$lastUpdated": "2020-09-28T08:53:45.9956637Z",
        "maxTempSinceLastReboot": {
          "$lastUpdated": "2020-09-28T08:53:45.9956637Z"
        }
      },
      "$version": 2
    }
  }
}
```

## <a name="interact-with-a-device-module"></a>デバイス モジュールを操作する

サービスの SDK を使用すると、接続されている IoT プラグ アンド プレイ デバイスとモジュールのモデル ID を取得できます。 サービスの SDK を使用して、書き込み可能なプロパティを設定し、コマンドを呼び出すことができます。

1. Visual Studio の別のインスタンスで、*azure-iot-sdk-csharp\iot-hub\Samples\service\PnpServiceSamples\Thermostat\Thermostat.csproj* プロジェクトを開きます。

1. Visual Studio で、 **[プロジェクト] > [Thermostat のプロパティ] > [デバッグ]** の順に移動します。 プロジェクトに次の環境変数を追加します。

    | 名前 | 値 |
    | ---- | ----- |
    | IOTHUB_DEVICE_ID | my-module-device |
    | IOTHUB_CONNECTION_STRING | [環境の設定](set-up-environment.md)の完了時に書き留めておいた値 |

    > [!TIP]
    > Azure IoT エクスプローラー ツールで、IoT ハブ接続文字列を見つけることもできます。

1. *Program.cs* ファイルを開き、コマンドを呼び出す行を次のように変更します。

    ```csharp
    CloudToDeviceMethodResult result = await s_serviceClient.InvokeDeviceMethodAsync(s_deviceId, "my-module", commandInvocation);
    ```

1. *Program.cs* ファイルで、デバイス ツインを取得する行を次のように変更します。

    ```csharp
    Twin twin = await s_registryManager.GetTwinAsync(s_deviceId, "my-module");
    ```

1. モジュール クライアント サンプルがまだ実行中であることを確認してから、このサービス サンプルを実行します。 サービス サンプルからの出力は、デバイス ツインからのモデル ID とコマンド呼び出しを示しています。

    ```cmd
    [09/28/2020 10:52:55]dbug: Thermostat.Program[0]
      Initialize the service client.
    [09/28/2020 10:52:55]dbug: Thermostat.Program[0]
      Get Twin model Id and Update Twin
    [09/28/2020 10:52:59]dbug: Thermostat.Program[0]
      Model Id of this Twin is: dtmi:com:example:Thermostat;1
    [09/28/2020 10:52:59]dbug: Thermostat.Program[0]
      Invoke a command
    [09/28/2020 10:53:00]dbug: Thermostat.Program[0]
      Command getMaxMinReport invocation result status is: 200
    ```

    モジュール クライアントからの出力には、コマンド ハンドラーの応答が示されています。

    ```cmd
    [09/28/2020 10:53:00]dbug: Thermostat.ThermostatSample[0]
      Command: Received - Generating max, min and avg temperature report since 28/09/2020 10:52:55.
    [09/28/2020 10:53:00]dbug: Thermostat.ThermostatSample[0]
      Command: MaxMinReport since 28/09/2020 10:52:55: maxTemp=25.4, minTemp=25.4, avgTemp=25.4, startTime=28/09/2020 10:52:56, endTime=28/09/2020 10:52:56
    ```

## <a name="convert-to-an-iot-edge-module"></a>IoT Edge モジュールに変換する

このサンプルを IoT プラグ アンド プレイ IoT Edge モジュールとして機能するように変換するには、アプリケーションをコンテナー化する必要があります。 コードにさらに変更を加える必要はありません。 接続文字列の環境変数は、スタートアップ時に IoT Edge ランタイムによって挿入されます。 詳細については、「[Visual Studio 2019 を使用して Azure IoT Edge 用のモジュールを開発してデバッグする](../iot-edge/how-to-visual-studio-develop-module.md)」を参照してください。

コンテナー化されたモジュールをデプロイする方法については、以下を参照してください。

* [Ubuntu 仮想マシン上で Azure IoT Edge を実行する](../iot-edge/how-to-install-iot-edge-ubuntuvm.md)。
* [Debian ベースの Linux システムに Azure IoT Edge ランタイムをインストールする](../iot-edge/how-to-install-iot-edge.md)。

Azure IoT エクスプローラー ツールを使用して、以下を参照できます。

* モジュール ツイン内にある IoT Edge デバイスのモデル ID。
* IoT Edge デバイスからのテレメトリ。
* IoT プラグ アンド プレイ通知をトリガーする、IoT Edge モジュール ツイン プロパティの更新。
* IoT プラグ アンド プレイ コマンドに対する IoT Edge モジュールの反応。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [iot-pnp-clean-resources](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、モジュールを使用する IoT プラグ アンド プレイ デバイスを IoT ハブに接続する方法を学習しました。 IoT プラグ アンド プレイ デバイス モデルの詳細については、以下を参照してください。

> [!div class="nextstepaction"]
> [IoT プラグ アンド プレイ モデリング開発者ガイド](./concepts-developer-guide-device.md)