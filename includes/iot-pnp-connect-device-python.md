---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 607c83bd97f8e371896a5a8ac0c9aa05ab34fa2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95511657"
---
このクイックスタートでは、IoT プラグ アンド プレイ デバイス アプリケーションのサンプルをビルドし、それをご利用の IoT ハブに接続し、送信されるテレメトリを Azure IoT エクスプローラー ツールを使用して表示する方法を示します。 このサンプル アプリケーションは Python 用に記述されており、Python 用 Azure IoT Hub デバイス SDK に含まれています。 ソリューション ビルダーは Azure IoT エクスプローラー ツールを使用して、デバイス コードを表示しなくても IoT プラグ アンド プレイ デバイスの機能を理解することができます。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

このクイックスタートを完了するには、お使いの開発用マシン上に Python 3.7 が必要です。 [python.org](https://www.python.org/) から、複数のプラットフォームに対応した最新の推奨バージョンをダウンロードできます。次のコマンドを使用して、ご利用の Python のバージョンを確認できます。  

```cmd/sh
python --version
```

ローカルの Python 環境で、次のようにパッケージをインストールします。

```cmd/sh
pip install azure-iot-device
```

Python SDK IoT リポジトリをクローンし、**master** をチェックアウトします。

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="run-the-sample-device"></a>サンプル デバイスを実行する

*azure-iot-sdk-python\azure-iot-device\samples\pnp* フォルダーには、IoT プラグ アンド プレイ デバイスのサンプル コードが含まれています。 このクイックスタートでは、*simple_thermostat.py* ファイルを使用します。 このサンプル コードでは、IoT プラグ アンド プレイ互換デバイスを実装し、Azure IoT Python デバイス クライアント ライブラリを使用します。

テキスト エディターで **simple_thermostat.py** ファイルを開きます。 次の点に注意してください。

1. [サーモスタット](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)を一意に表す単一のデバイス ツイン モデル識別子 (DTMI) を定義しています。 DTMI は、ユーザーに認識されている必要があり、デバイス実装のシナリオによって異なります。 現在のサンプルでは、モデルは、温度の監視に関連付けられたテレメトリ、プロパティ、およびコマンドを持つサーモスタットを表します。

1. コマンド ハンドラーの実装を定義するための関数があります。 これらのハンドラーを記述して、コマンド要求に対してデバイスがどのように応答するかを定義します。

1. コマンド応答を定義するための関数があります。 コマンド応答関数を作成して、IoT ハブに応答を返送します。

1. アプリケーションを終了するための入力キーボード リスナー関数を定義しています。

1. **main** 関数があります。 **main** 関数は、以下のことを行います。

    1. デバイス SDK を使用してデバイス クライアントを作成し、IoT ハブに接続します。

    1. プロパティを更新します。 使用している **サーモスタット** モデルでは、サーモスタット用の 2 つのプロパティとして `targetTemperature` と `maxTempSinceLastReboot` が定義されているため、これを使用することになります。 プロパティは、`device_client` で定義されている `patch_twin_reported_properties` メソッドを使用して更新されます。

    1. **execute_command_listener** 関数を使用して、コマンド要求のリッスンが開始されます。 この関数により、サービスからのコマンドをリッスンするように "リスナー" が設定されます。 リスナーを設定するときに、`method_name`、`user_command_handler`、および `create_user_response_handler` を指定します。
        - `user_command_handler` 関数では、コマンドを受信したときにデバイスによって行われる必要がある内容を定義します。 たとえば、アラームが鳴る場合、このコマンドを受信することの効果は、目を覚ますことです。 これは、呼び出されるコマンドの "効果" と考えてください。
        - `create_user_response_handler` 関数により、コマンドが正常に実行されたときに IoT ハブに送信される応答が作成されます。 たとえば、アラームが鳴る場合、スヌーズを押して応答します。これは、サービスに対するフィードバックです。 これは、サービスに与える返信と考えてください。 この応答はポータルで確認できます。

    1. テレメトリの送信が開始されます。 pnp_methods.py ファイルには、**pnp_send_telemetry** が定義されています。 サンプル コードでは、ループを使用して、8 秒ごとにこの関数を呼び出しています。

    1. ユーザーが **Q** または **q** キーを押したら、すべてのリスナーとタスクを無効にし、ループを終了します。

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

サンプル構成の詳細については、[サンプルの readme](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md) を参照してください。

これでコードを見終えたので、次のコマンドを使用してサンプルを実行します。

```cmd/sh
python simple_thermostat.py
```

次の出力が表示されます。これは、デバイスによってハブにテレメトリ データが送信され、コマンドとプロパティの更新情報を受信する準備ができたことを示します。

```cmd/sh
Listening for command requests and property updates
Press Q to quit
Sending telemetry for temperature
Sent message
```

次の手順を完了するまで、サンプルを実行したままにしておきます。

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Azure IoT エクスプローラーを使用してコードを確認する

デバイス クライアントのサンプルが開始された後、Azure IoT エクスプローラー ツールを使用して、それが動作していることを確認します。

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]
