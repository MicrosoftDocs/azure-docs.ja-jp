---
title: Linux または Windows 上で実行されている IoT プラグ アンド プレイ ブリッジのサンプルを IoTハブ に接続する方法 | Microsoft Docs
description: IoT ハブに接続する IoT プラグ アンド プレイ ブリッジを、Linux または Windows 上でビルドして実行します。 Azure IoT Explorer ツールを使用して、デバイスからハブに送信された情報を表示します。
author: usivagna
ms.author: ugans
ms.date: 12/11/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 9bcf256b6144702254bbff4a57e5ff402abaa962
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99834104"
---
# <a name="how-to-connect-an--iot-plug-and-play-bridge-sample-running-on-linux-or-windows-to-iot-hub"></a>Linux または Windows 上で実行されている IoT プラグ アンド プレイ ブリッジのサンプルを IoT Hub に接続する方法

この記事では、IoT プラグ アンド プレイ ブリッジのサンプル環境アダプターをビルドし、それをご利用の IoT ハブに接続し、送信されるテレメトリを Azure IoT エクスプローラー ツールを使用して表示する方法を示します。 IoT プラグ アンド プレイ ブリッジは C で記述されており、C の Azure IoT device SDK が含まれています。このチュートリアルを終了すると、IoT プラグ アンド プレイ ブリッジを実行し、報告されたテレメトリを Azure IoT エクスプローラー内で確認できるようになります。

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-explorer-telemetry.png" alt-text="Iot プラグ アンド プレイ ブリッジから報告されたテレメトリ (湿度、気温) のテーブルが表示された Azure IoT エクスプローラーを示しているスクリーンショット。":::

## <a name="prerequisites"></a>[前提条件]

この記事のサンプルは、Windows または Linux で実行できます。 この攻略ガイドのシェル コマンドは、パス区切り記号を "`\`" とする Windows 規則に従っています。Linux 方式に従う場合は、必ずこれらの区切り記号を "`/`" に置き換えてください。

### <a name="azure-iot-explorer"></a>Azure IoT エクスプローラー

この記事のパート 2 でサンプル デバイスとやり取りするには、**Azure IoT エクスプローラー** ツールを使用します。 ご利用のオペレーティング システム用の [Azure IoT エクスプローラーの最新リリースをダウンロードしてインストール](./howto-use-iot-explorer.md)します。

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

次のコマンドを実行して、ご利用のハブに対する "_IoT Hub 接続文字列_" を取得します。 この接続文字列はメモしておいてください。これは、この記事の後半で使用します。

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

次のコマンドを実行して、ハブに追加したデバイスの "_デバイス接続文字列_" を取得します。 この接続文字列はメモしておいてください。これは、この記事の後半で使用します。

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

## <a name="download-and-run-the-bridge"></a>ブリッジのダウンロードと実行

この記事では、ブリッジを実行する 2 つのオプションを使用します。 次のようにすることができます。

- 事前構築済みの実行可能ファイルをダウンロードし、このセクションの説明に従って実行します。
- 次のセクションの説明に従って、ソース コードをダウンロードしてから、[ブリッジをビルドして実行](#build-and-run-the-bridge)します。

ブリッジをダウンロードして実行するには:

1. IoT プラグ アンド プレイの[リリース ページ](https://github.com/Azure/iot-plug-and-play-bridge/releases)にアクセスします。
1. オペレーティング システムの事前構築済み実行可能ファイル (Windows の場合は **pnpbridge_bin.exe**、Linux の場合は **pnpbridge_bin**) をダウンロードします。
1. 環境センサー サンプルの [config.xml](https://raw.githubusercontent.com/Azure/iot-plug-and-play-bridge/master/pnpbridge/src/adapters/samples/environmental_sensor/config.json) 構成ファイル サンプルをダウンロードします。 構成ファイルが実行可能ファイルと同じフォルダーにあることを確認します。
1. *config.json* ファイルを編集します。

    - 以前にメモした _デバイス接続文字列_ である `connection-string` 値を追加します。
    - _デバイス接続文字列_ から共有アクセス キーの値である `symmetric_key` 値を追加します。
    - `root_interface_model_id` 値を `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` に置き換えます。

    これで *config.json* ファイルの最初のセクションは、次のスニペットのようになります。

    ```json
    {
      "$schema": "../../../pnpbridge/src/pnpbridge_config_schema.json",
      "pnp_bridge_connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "HostName=youriothub.azure-devices.net;DeviceId=yourdevice;SharedAccessKey=TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8=",
        "root_interface_model_id": "dtmi:com:example:PnpBridgeEnvironmentalSensor;1",
        "auth_parameters": {
            "auth_type": "symmetric_key",
            "symmetric_key": "TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8="
        },
    ```

1. コマンドライン環境で実行可能ファイルを実行します。 ブリッジは次のような出力を生成します。

    ```output
    c:\temp\temp-bridge>dir
     Volume in drive C is OSDisk
     Volume Serial Number is 38F7-DA4A
    
     Directory of c:\temp\temp-bridge
    
    10/12/2020  12:24    <DIR>          .
    10/12/2020  12:24    <DIR>          ..
    08/12/2020  15:26             1,216 config.json
    10/12/2020  12:21         3,617,280 pnpbridge_bin.exe
                   2 File(s)      3,618,496 bytes
                   2 Dir(s)  12,999,147,520 bytes free
    
    c:\temp\temp-bridge>pnpbridge_bin.exe
    Info:
     -- Press Ctrl+C to stop PnpBridge
    
    Info: Using default configuration location
    Info: Starting Azure PnpBridge
    Info: Pnp Bridge is running as am IoT egde device.
    Info: Pnp Bridge creation succeeded.
    Info: Connection_type is [connection_string]
    Info: Tracing is disabled
    Info: WARNING: SharedAccessKey is included in connection string. Ignoring symmetric_key in config file.
    Info: IoT Edge Device configuration initialized successfully
    Info: Building Pnp Bridge Adapter Manager, Adapters & Components
    Info: Adapter with identity environment-sensor-sample-pnp-adapter does not have any associated global parameters. Proceeding with adapter creation.
    Info: Pnp Adapter with adapter ID environment-sensor-sample-pnp-adapter has been created.
    Info: Pnp Adapter Manager created successfully.
    Info: Pnp components created successfully.
    Info: Pnp components built in model successfully.
    Info: Connected to Azure IoT Hub
    Info: Environmental Sensor: Starting Pnp Component
    Info: IoTHub client call to _SendReportedState succeeded
    Info: Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=state, propertyValue=true
    Info: Pnp components started successfully.
    ```

## <a name="build-and-run-the-bridge"></a>ブリッジをビルドして実行する

実行可能ファイルを自分でビルドする場合は、ソース コードとビルド スクリプトをダウンロードできます。

任意のフォルダーでコマンド プロンプトを開きます。 次のコマンドを実行して、[IoT プラグ アンド プレイ ブリッジ](https://github.com/Azure/iot-plug-and-play-bridge)の GitHub リポジトリをこの場所に複製します。

```cmd
git clone https://github.com/Azure/iot-plug-and-play-bridge.git
```

リポジトリを複製した後、サブモジュールを更新します。 サブモジュールには、Azure IoT SDK for C が含まれます。

```cmd
cd iot-plug-and-play-bridge
git submodule update --init --recursive
```

この操作が完了するまで数分かかることが予想されます。

> [!TIP]
> Git クローン サブ モジュールの更新に失敗する問題が発生した場合、これは Windows ファイル パスに関する既知の問題です。 問題を解決するために、次のコマンドを試すことができます: `git config --system core.longpaths true`

ブリッジをビルドするための前提条件は、オペレーティング システムによって異なります。

### <a name="windows"></a>Windows

Windows で IoT プラグ アンド プレイ ブリッジをビルドするには、次のソフトウェアをインストールします。

* [Visual Studio (Community、Professional、または Enterprise)](https://visualstudio.microsoft.com/downloads/)。Visual Studio を [インストール](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019)するときに、 **[C++ によるデスクトップ開発]** ワークロードを必ず含めてください。
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/)。

### <a name="linux"></a>Linux

この記事は、Ubuntu Linux を使用していることを前提としています。 この記事の手順は、Ubuntu 18.04 を使用してテストされました。

Linux に IoT プラグ アンド プレイ ブリッジをビルドするには、`apt-get` コマンドを使用して、**GCC**、**Git**、**cmake**、および必要なすべての依存関係をインストールします。

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

`cmake` のバージョンが **2.8.12** より大きく、**GCC** のバージョンが **4.4.7** より大きいことを確認します。

```sh
cmake --version
gcc --version
```

### <a name="build-the-iot-plug-and-play-bridge"></a>IoT プラグ アンド プレイ ブリッジの作成

リポジトリ ディレクトリ内の *pnpbridge* フォルダーに移動します。

Windows の場合は、[Visual Studio 用開発者コマンド プロンプト](/dotnet/framework/tools/developer-command-prompt-for-vs)で以下を実行します。

```cmd
cd scripts\windows
build.cmd
```

Linux の場合も同様に以下を実行します。

```bash
cd scripts/linux
./setup.sh
./build.sh
```

>[!TIP]
>Windows 上で、cmake コマンドによって生成されたソリューションを Visual Studio 2019 で開くことができます。 cmake ディレクトリ内の *azure_iot_pnp_bridge.sln* プロジェクト ファイルを開き、*pnpbridge_bin* プロジェクトをソリューションのスタートアップ プロジェクトとして設定します。 これで、Visual Studio でサンプルをビルドし、デバッグ モードで実行できるようになりました。

### <a name="edit-the-configuration-file"></a>構成ファイルを編集する

構成ファイルの詳細については、[IoT プラグ アンド プレイ ブリッジの概念ドキュメント](concepts-iot-pnp-bridge.md)を参照してください。

テキスト エディターで *iot-plug-and-play-bridge\pnpbridge\src\adapters\samples\environmental_sensor\config.json* ファイルを開きます。

- 以前にメモした _デバイス接続文字列_ である `connection-string` 値を追加します。
- _デバイス接続文字列_ から共有アクセス キーの値である `symmetric_key` 値を追加します。
- `root_interface_model_id` 値を `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` に置き換えます。

これで *config.json* ファイルの最初のセクションは、次のスニペットのようになります。

```json
{
  "$schema": "../../../pnpbridge/src/pnpbridge_config_schema.json",
  "pnp_bridge_connection_parameters": {
    "connection_type" : "connection_string",
    "connection_string" : "HostName=youriothub.azure-devices.net;DeviceId=yourdevice;SharedAccessKey=TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8=",
    "root_interface_model_id": "dtmi:com:example:PnpBridgeEnvironmentalSensor;1",
    "auth_parameters": {
        "auth_type": "symmetric_key",
        "symmetric_key": "TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8="
    },
```

### <a name="run-the-iot-plug-and-play-bridge"></a>IoT プラグ アンド プレイ ブリッジを実行する

IoT プラグ アンド プレイ ブリッジ環境センサーのサンプルを開始します。 パラメーターは、前のセクションで編集した `config.json` ファイルへのパスです。

```cmd
REM Windows
cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console
Debug\pnpbridge_bin.exe ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
```

ブリッジは次のような出力を生成します。

```output
c:\temp>cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console

c:\temp\iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console>Debug\pnpbridge_bin.exe ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
Info:
 -- Press Ctrl+C to stop PnpBridge

Info: Using configuration from specified file path: ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
Info: Starting Azure PnpBridge
Info: Pnp Bridge is running as am IoT egde device.
Info: Pnp Bridge creation succeeded.
Info: Connection_type is [connection_string]
Info: Tracing is disabled
Info: WARNING: SharedAccessKey is included in connection string. Ignoring symmetric_key in config file.
Info: IoT Edge Device configuration initialized successfully
Info: Building Pnp Bridge Adapter Manager, Adapters & Components
Info: Adapter with identity environment-sensor-sample-pnp-adapter does not have any associated global parameters. Proceeding with adapter creation.
Info: Pnp Adapter with adapter ID environment-sensor-sample-pnp-adapter has been created.
Info: Pnp Adapter Manager created successfully.
Info: Pnp components created successfully.
Info: Pnp components built in model successfully.
Info: Connected to Azure IoT Hub
Info: Environmental Sensor: Starting Pnp Component
Info: IoTHub client call to _SendReportedState succeeded
Info: Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=state, propertyValue=true
Info: Pnp components started successfully.
Info: IoTHub client call to _SendEventAsync succeeded
Info: PnpBridge_PnpBridgeStateTelemetryCallback called, result=0, telemetry=PnpBridge configuration complete
Info: Processing property update for the device or module twin
Info: Environmental Sensor Adapter:: Successfully delivered telemetry message for <environmentalSensor>
```

次のコマンドを使用して、Linux でブリッジを実行します。

```bash
cd iot-plug-and-play-bridge/pnpbridge/cmake/pnpbridge_x86/src/pnpbridge/samples/console
./pnpbridge_bin ../../../../../../src/adapters/samples/environmental_sensor/config.json
```

## <a name="download-the-model-files"></a>モデル ファイルをダウンロードする

後で Azure IoT エクスプローラーを使用して、IoT ハブへの接続時にデバイスを表示します。 Azure IoT エクスプローラーには、デバイスから送信される **モデル ID** と一致するモデル ファイルのローカル コピーが必要です。 モデル ファイルを使用すると、デバイスによって実装されるテレメトリ、プロパティ、およびコマンドを IoT エクスプローラーに表示できます。

Azure IoT エクスプローラーのモデルをダウンロードするには:

1. ローカル コンピューターに "*モデル*" という名前のフォルダーを作成します。
1. [EnvironmentalSensor.json](https://raw.githubusercontent.com/Azure/iot-plug-and-play-bridge/master/pnpbridge/docs/schemas/EnvironmentalSensor.json) を、前の手順で作成した "*モデル*" フォルダーに保存します。
1. このモデル ファイルをテキスト エディターで開くと、`dtmi:com:example:PnpBridgeEnvironmentalSensor;1` を ID としたコンポーネントがモデルで定義されていることがわかります。 これは、*config.json* ファイルで使用したモデル ID と同じです。

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Azure IoT エクスプローラーを使用してコードを確認する

ブリッジが開始された後、Azure IoT エクスプローラー ツールを使用して、それが動作していることを確認します。 `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` モデルで定義されているテレメトリ、プロパティ、およびコマンドを確認できます。

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>次のステップ

この記事では、IoT プラグ アンド プレイ デバイスを IoT ハブに接続する方法を学習しました。 ご利用の IoT プラグ アンド プレイ デバイスとやりとりするソリューションを構築する方法の詳細については、次を参照してください。

* [IoT プラグ アンド プレイ ブリッジとは](./concepts-iot-pnp-bridge.md)
* [IoT プラグ アンド プレイ ブリッジをビルド、デプロイ、拡張する](howto-build-deploy-extend-pnp-bridge.md)
* [GitHub の IoT プラグ アンド プレイ ブリッジ](https://github.com/Azure/iot-plug-and-play-bridge)
