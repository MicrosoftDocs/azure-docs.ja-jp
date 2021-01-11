---
title: IoT プラグ アンド プレイ プレビューのサンプル C デバイス コードを IoT Hub に接続する | Microsoft Docs
description: IoT ハブと接続する IoT プラグ アンド プレイ プレビューのサンプル デバイス コードを Linux および Windows 上でビルドして実行します。 Azure IoT Explorer ツールを使用して、デバイスからハブに送信された情報を表示します。
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 65b35c30d796f733e91c71e569634b61f9f7c52b
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88959291"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-or-windows-to-iot-hub-c"></a>クイック スタート:Linux または Windows 上で実行されているサンプルの IoT プラグ アンド プレイ プレビュー デバイス アプリケーションを IoT Hub に接続する (C)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

このクイックスタートでは、IoT プラグ アンド プレイ デバイス アプリケーションのサンプルをビルドし、それをご利用の IoT ハブに接続し、送信されるテレメトリを Azure IoT エクスプローラー ツールを使用して表示する方法を示します。 このサンプル アプリケーションは C で記述され、C 対応の Azure IoT device SDK に含められています。ソリューション ビルダーは、Azure IoT エクスプローラー ツールを使用すれば、デバイス コードを表示しなくても IoT プラグ アンド プレイ デバイスの機能を理解することができます。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>前提条件

このクイックスタートは、Linux または Windows で実行できます。 このクイックスタートのシェル コマンドは、パス区切り記号を "`/`" とする Linux 規則に従っています。Windows 方式に従う場合は、これらの区切り記号を "`\`" に置き換えてください。

前提条件は、次のように、オペレーティング システムによって異なります。

### <a name="linux"></a>Linux

このクイックスタートは、Ubuntu Linux を使用していることを前提としています。 このクイックスタートの手順は、Ubuntu 18.04 を使用してテストされました。

このクイックスタートを Linux で完了するには、ご利用のローカルの Linux 環境に以下のソフトウェアをインストールする必要があります。

`apt-get` コマンドを使用して、**GCC**、**Git**、**cmake**、および必要なすべての依存関係をインストールします。

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

`cmake` のバージョンが **2.8.12** より大きく、**GCC** のバージョンが **4.4.7** より大きいことを確認します。

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows

Windows でこのクイックスタートを完了するには、ご利用のローカル Windows 環境に以下のソフトウェアをインストールします。

* [Visual Studio (Community、Professional、または Enterprise)](https://visualstudio.microsoft.com/downloads/)。Visual Studio を[インストール](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2019)するときに、 **[C++ によるデスクトップ開発]** ワークロードを必ず含めてください。
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/)。

### <a name="azure-iot-explorer"></a>Azure IoT エクスプローラー

このクイックスタートのパート 2 でサンプル デバイスとやり取りするには、**Azure IoT エクスプローラー** ツールを使用します。 ご利用のオペレーティング システム用の [Azure IoT エクスプローラーの最新リリースをダウンロードしてインストール](./howto-use-iot-explorer.md)します。

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

次のコマンドを実行して、ご利用のハブに対する "_IoT ハブ接続文字列_" を取得します。 この接続文字列はメモしておいてください。このクイックスタートで後ほど使用します。

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> また、Azure IoT エクスプローラー ツールを使用して、IoT ハブ接続文字列を見つけることもできます。

次のコマンドを実行して、ハブに追加したデバイスの "_デバイス接続文字列_" を取得します。 この接続文字列はメモしておいてください。このクイックスタートで後ほど使用します。

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>コードのダウンロード

このクイックスタートでは、Azure IoT Hub Device C SDK をクローンしてビルドするために使用できる開発環境を準備します。

任意のディレクトリでコマンド プロンプトを開きます。 次のコマンドを実行して、[Azure IoT C SDK およびライブラリ](https://github.com/Azure/azure-iot-sdk-c)の GitHub リポジトリをこの場所にクローンします。

```cmd\bash
git clone --depth 1 --recurse-submodules https://github.com/Azure/azure-iot-sdk-c.git
```

この操作が完了するまで数分かかることが予想されます。

## <a name="build-the-code"></a>コードのビルド

デバイス SDK を使用して、含まれているサンプル コードをビルドします。

1. デバイス SDK のルート フォルダーに _cmake_ サブディレクトリを作成し、そのフォルダーに移動します。

    ```cmd\bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. 次のコマンドを実行して、SDK とサンプルをビルドします。

    ```cmd\bash
    cmake ..
    cmake --build .
    ```

> [!TIP]
> Windows では、`cmake` コマンドで生成されたソリューションを Visual Studio 2019 で開くことができます。 _cmake_ ディレクトリの *azure_iot_sdks.sln* プロジェクト ファイルを開き、**pnp_simple_thermostat** プロジェクトをソリューションのスタートアップ プロジェクトとして設定します。 これで、Visual Studio でサンプルをビルドし、デバッグ モードで実行できるようになりました。

## <a name="run-the-device-sample"></a>デバイス サンプルを実行する

IoT ハブにテレメトリを送信する IoT プラグ アンド プレイ デバイスをシミュレートする、SDK のサンプル アプリケーションを実行します。

IoT ハブへの接続に接続文字列を使用するようにサンプルを構成するために、次の 2 つの環境変数を作成します。

- 値が `"connectionString"` の **IOTHUB_DEVICE_SECURITY_TYPE**
- 先ほどメモしたデバイス接続文字列を格納するための **IOTHUB_DEVICE_CONNECTION_STRING**。

_cmake_ フォルダーから、実行可能ファイルを含むフォルダーに移動し、そのファイルを実行します。

```bash
# Bash
cd iothub_client/samples/pnp/pnp_simple_thermostat/
./pnp_simple_thermostat
```

```cmd
REM Windows
cd  iothub_client\samples\pnp\pnp_simple_thermostat\Debug\pnp_simple_thermostat.exe
```

> [!TIP]
> Windows 上の Visual Studio でコードの実行をトレースするには、_pnp_simple_thermostat.c_ ファイルの `main` 関数にブレーク ポイントを追加します。

これで、デバイスはコマンドとプロパティの更新情報を受信する準備が整い、ハブへのテレメトリ データの送信が開始されました。 次の手順を完了するまで、サンプルを実行したままにしておきます。

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Azure IoT エクスプローラーを使用してコードを確認する

デバイス クライアントのサンプルが開始された後、Azure IoT エクスプローラー ツールを使用して、それが動作していることを確認します。

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>コードの確認

このサンプルにより、単純な IoT プラグ アンド プレイのサーモスタット デバイスが実装されます。 このサンプルにより実装されるモデルによって、IoT プラグ アンド プレイ [コンポーネント](concepts-components.md)は使用されません。 [サーモスタット デバイスの DTDL モデル ファイル](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)により、デバイスによって実装されるテレメトリ、プロパティ、およびコマンドが定義されます。

デバイス コードでは、IoT ハブに接続するために標準の関数が使用されます。

```c
deviceHandle = IoTHubDeviceClient_CreateFromConnectionString(connectionString, MQTT_Protocol)
```

デバイスにより、接続要求で実装される DTDL モデルのモデル ID が送信されます。 モデル ID を送信するデバイスは、IoT プラグ アンド プレイ デバイスです。

```c
static const char g_ModelId[] = "dtmi:com:example:Thermostat;1";

...

IoTHubDeviceClient_SetOption(deviceHandle, OPTION_MODEL_ID, modelId)
```

プロパティの更新、コマンドの処理、テレメトリの送信を行うコードは、IoT プラグ アンド プレイ規則を使用しないデバイスのコードと同じです。

IoT ハブから送信されたペイロード内の JSON オブジェクトを解析するために、このコードでは Parson ライブラリが使用されます。

```c
// JSON parser
#include "parson.h"
```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、IoT プラグ アンド プレイ デバイスを IoT ハブに接続する方法を学習しました。 ご利用の IoT プラグ アンド プレイ デバイスとやりとりするソリューションを構築する方法の詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [方法: デバイスに接続してやりとりする](howto-develop-solution.md)
