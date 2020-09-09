---
title: IoT プラグ アンド プレイ プレビューのサンプル Python デバイス コードを Azure IoT Hub に接続する | Microsoft Docs
description: Python を使用して、IoT ハブと接続する IoT プラグ アンド プレイ プレビューのサンプル デバイス コードをビルドして実行します。 Azure IoT Explorer ツールを使用して、デバイスからハブに送信された情報を表示します。
author: ericmitt
ms.author: ericmitt
ms.date: 7/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 84ef7ff18c294097da20640c1de237b41900cb40
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352698"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-python"></a>クイック スタート:サンプルの IoT プラグ アンド プレイ プレビュー デバイス アプリケーションを IoT Hub に接続する (Python)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

このクイックスタートでは、IoT プラグ アンド プレイ デバイス アプリケーションのサンプルをビルドし、それをご利用の IoT ハブに接続し、送信されるテレメトリを Azure IoT エクスプローラー ツールを使用して表示する方法を示します。 このサンプル アプリケーションは Python 用に記述されており、Python 用 Azure IoT Hub デバイス SDK に含まれています。 ソリューション ビルダーは Azure IoT エクスプローラー ツールを使用して、デバイス コードを表示しなくても IoT プラグ アンド プレイ デバイスの機能を理解することができます。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>前提条件

このクイックスタートを完了するには、お使いの開発用マシン上に Python 3.7 が必要です。 [python.org](https://www.python.org/) から、複数のプラットフォームに対応した最新の推奨バージョンをダウンロードできます。次のコマンドを使用して、ご利用の Python のバージョンを確認できます。  

```cmd/sh
python --version
```

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

## <a name="set-up-your-environment"></a>環境の設定方法

このパッケージは、パブリック プレビューの更新用の PIP として公開されます。 パッケージのバージョンは、最新であるか `2.1.4` である必要があります

ローカルの Python 環境で、次のようにファイルをインストールします。

```cmd/sh
pip install azure-iot-device
```

Python SDK IoT リポジトリをクローンし、**master** をチェックアウトします。

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="run-the-sample-device"></a>サンプル デバイスを実行する

*azure-iot-sdk-python\azure-iot-device\samples\pnp* フォルダーには、IoT プラグ アンド プレイ デバイスのサンプル コードが含まれています。 このクイックスタートでは、*pnp_thermostat.py* ファイルを使用します。 このサンプル コードでは、IoT プラグ アンド プレイ互換デバイスを実装し、Azure IoT Python デバイス クライアント ライブラリを使用します。

先ほどメモしたデバイス接続文字列を格納するために、**IOTHUB_DEVICE_CONNECTION_STRING** という環境変数を作成します。

テキスト エディターで **pnp_thermostat.py** ファイルを開きます。 次の点に注意してください。

1. [サーモスタット](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)を一意に表す単一のデバイス ツイン モデル識別子 (DTMI) を定義しています。 DTMI は、ユーザーに認識されている必要があり、デバイス実装のシナリオによって異なります。 現在のサンプルでは、モデルは、温度の監視に関連付けられたテレメトリ、プロパティ、およびコマンドを持つサーモスタットを表します。

1. コマンド ハンドラーの実装を定義するための関数があります。 これらのハンドラーを記述して、コマンド要求に対してデバイスがどのように応答するかを定義します。

1. コマンド応答を定義するための関数があります。 コマンド応答関数を作成して、IoT ハブに応答を返送します。

1. アプリケーションを終了するための入力キーボード リスナー関数を定義しています。

1. **main** 関数があります。 **main** 関数は、以下のことを行います。

    1. デバイス SDK を使用してデバイス クライアントを作成し、IoT ハブに接続します。

    1. プロパティを更新します。 使用している**サーモスタット** モデルでは、サーモスタット用の 2 つのプロパティとして `targetTemperature` と `maxTempSinceLastReboot` が定義されているため、これを使用することになります。 プロパティは、`device_client` で定義されている `patch_twin_reported_properties` メソッドを使用して更新されます。

    1. **execute_command_listener** 関数を使用して、コマンド要求のリッスンが開始されます。 この関数により、サービスからのコマンドをリッスンするように "リスナー" が設定されます。 リスナーを設定するときに、`method_name`、`user_command_handler`、および `create_user_response_handler` を指定します。 
        - `user_command_handler` 関数では、コマンドを受信したときにデバイスによって行われる必要がある内容を定義します。 たとえば、アラームが鳴る場合、このコマンドを受信することの効果は、目を覚ますことです。 これは、呼び出されるコマンドの "効果" と考えてください。
        - `create_user_response_handler` 関数により、コマンドが正常に実行されたときに IoT ハブに送信される応答が作成されます。 たとえば、アラームが鳴る場合、スヌーズを押して応答します。これは、サービスに対するフィードバックです。 これは、サービスに与える返信と考えてください。 この応答はポータルで確認できます。

    1. テレメトリの送信が開始されます。 pnp_methods.py ファイルには、**pnp_send_telemetry** が定義されています。 サンプル コードでは、ループを使用して、8 秒ごとにこの関数を呼び出しています。

    1. ユーザーが **Q** または **q** キーを押したら、すべてのリスナーとタスクを無効にし、ループを終了します。

これでコードを見終えたので、次のコマンドを使用してサンプルを実行します。

```cmd/sh
python pnp_thermostat.py
```

次の出力が表示されます。これは、デバイスによってハブにテレメトリ データが送信され、コマンドとプロパティの更新情報を受信する準備ができたことを示します。

```cmd/sh
Connecting using Connection String HostName=<your hub name>.azure-devices.net;DeviceId=<your device id>;SharedAccessKey=<your device shared access key>
Listening for command requests and property updates
Press Q to quit
Sending telemetry for temperature
Sent message
```

次の手順を完了するまで、サンプルを実行したままにしておきます。

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Azure IoT エクスプローラーを使用してコードを確認する

デバイス クライアントのサンプルが開始された後、Azure IoT エクスプローラー ツールを使用して、それが動作していることを確認します。

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、IoT プラグ アンド プレイ デバイスを IoT ハブに接続する方法を学習しました。 ご利用の IoT プラグ アンド プレイ デバイスとやりとりするソリューションを構築する方法の詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [ご利用のソリューションに接続されている IoT プラグ アンド プレイ プレビュー デバイスとやり取りする](quickstart-service-python.md)
