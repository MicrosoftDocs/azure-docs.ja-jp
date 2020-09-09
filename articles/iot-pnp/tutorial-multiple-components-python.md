---
title: IoT プラグ アンド プレイ プレビューのサンプル Python コンポーネント デバイス コードを IoT Hub に接続する | Microsoft Docs
description: 複数のコンポーネントを使用し、IoT ハブに接続する IoT プラグ アンド プレイ プレビューのサンプル Python デバイス コードをビルドして実行します。 Azure IoT Explorer ツールを使用して、デバイスからハブに送信された情報を表示します。
author: ericmitt
ms.author: ericmitt
ms.date: 7/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 571f0e0ceff0adfbf1814abc627fcab6b23acbe1
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87905858"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-preview-multiple-component-device-application-to-iot-hub-python"></a>チュートリアル:IoT プラグ アンド プレイ プレビューの複数コンポーネントのデバイス アプリケーションのサンプルを IoT Hub に接続する (Python)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

このチュートリアルでは、コンポーネントとルート インターフェイスを使用する IoT プラグ アンド プレイ デバイス アプリケーションのサンプルをビルドし、それをご利用の IoT ハブに接続し、ハブに送信される情報を Azure IoT エクスプローラー ツールを使用して表示する方法を示します。 このサンプル アプリケーションは Python で記述されており、Python 用 Azure IoT device SDK に含まれています。 ソリューション ビルダーは Azure IoT エクスプローラー ツールを使用して、デバイス コードを表示しなくても IoT プラグ アンド プレイ デバイスの機能を理解することができます。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、開発用コンピューターに Python 3.7 が必要です。 [python.org](https://www.python.org/) から、複数のプラットフォームに対応した最新の推奨バージョンをダウンロードできます。次のコマンドを使用して、ご利用の Python のバージョンを確認することができます。  

```cmd/sh
python --version
```

[python.org](https://www.python.org/) から、複数のプラットフォームに対応した最新の推奨バージョンをダウンロードできます。

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

## <a name="set-up-your-environment"></a>環境の設定方法

このパッケージは、パブリック プレビューの更新用の PIP として公開されます。 パッケージのバージョンは、最新であるか `2.1.4` 以上である必要があります

ローカルの python 環境で、次のようにファイルをインストールします。

```cmd/sh
pip install azure-iot-device
```

Python SDK IoT リポジトリをクローンし、**pnp-preview-refresh** を確認します。

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="review-the-code"></a>コードの確認

このサンプルにより、IoT プラグ アンド プレイの温度コントローラー デバイスが実装されます。 このサンプルによって実装されるモデルにより、[複数のコンポーネント](concepts-components.md)が使用されます。 [温度デバイスの Digital Twins Definition Language (DTDL) モデル ファイル](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)により、デバイスによって実装されるテレメトリ、プロパティ、およびコマンドが定義されます。

*azure-iot-sdk-python\azure-iot-device\samples\pnp* フォルダーには、IoT プラグ アンド プレイ デバイスのサンプル コードが含まれています。 温度コントローラー サンプルのファイルは次のとおりです。

- pnp_temp_controller_with_thermostats.py
- pnp_helper_preview_refresh.py

温度コントローラーには、温度コントローラー DTDL モデルに基づく、複数のコンポーネントと 1 つのルート インターフェイスがあります。

任意のエディターで *pnp_temp_controller_with_thermostats.py* ファイルを開きます。 このファイルのコードによって、次のことが行われます。

1. ヘルパー メソッドにアクセスするために `pnp_helper_preview_refresh.py` がインポートされます。

2. DTDL モデルで定義されている、2 つの異なるインターフェイスを一意に表す 2 つの Digital Twin Model Identifier (DTMI) が定義されます。 実際の温度コントローラー内のコンポーネントによって、これら 2 つのインターフェイスが実装される必要があります。 これら 2 つのインターフェイスは、中央リポジトリで既に公開されています。 これらの DTMI は、ユーザーに認識されている必要があり、デバイス実装のシナリオによって異なります。 現在のサンプルの場合、これら 2 つのインターフェイスは以下を表します。

  - サーモスタット
  - Azure によって作成されたデバイス情報。

3. 実装されているデバイスの DTMI `model_id` が定義されます。 DTMI はユーザー定義であり、DTMI モデル ファイル内の DTMI と一致している必要があります。

4. DTDL ファイル内のコンポーネントに指定された名前が定義されます。 2 つのサーモスタット (DTDL 内) と 1 つのデバイス情報コンポーネントがあります。 `serial_number` という定数は、ルート インターフェイスでも定義されています。 デバイスの `serial_number` を変更することはできません。

5. コマンド ハンドラーの実装が定義されます。 これらにより、コマンド要求を受信したときのデバイスの動作が定義されます。

6. コマンド応答を作成するための関数が定義されます。 これらにより、コマンド要求に対して、デバイスによってどのように応答されるかが定義されます。 コマンドによりカスタム応答を IoT ハブに戻す必要がある場合は、コマンドの応答関数を作成します。 コマンドの応答関数が指定されていない場合は、一般的な応答が送信されます。 このサンプルでは、**getMaxMinReport** コマンドにのみカスタム応答があります。

7. このデバイスからテレメトリを送信するための関数が定義されます。 サーモスタットとルート インターフェイスの両方によってテレメトリが送信されます。 テレメトリを送信したコンポーネントを識別できるように、この関数によって、省略可能なコンポーネント名のパラメーターが受け取られます。

8. コマンド要求のリスナーが定義されます。

9. 必要なプロパティ更新のリスナーが定義されます。

10. 次のような `main` 関数があります。

    1. デバイス SDK を使用してデバイス クライアントを作成し、IoT ハブに接続します。 IoT ハブによりデバイスが IoT プラグ アンド プレイ デバイスとして識別できるように、デバイスによって `model_id` が送信されます。

    1. ヘルパー ファイルの `create_reported_properties` 関数を使用して、プロパティを作成します。 コンポーネント名、およびプロパティをキー値のペアとして、この関数に渡します。

    1. `patch_twin_reported_properties` を呼び出すことによって、そのコンポーネントの読み取り可能なプロパティを更新します。

    1. `execute_command_listener` 関数を使用するコマンド要求のリッスンを開始します。 この関数により、サービスからのコマンド要求のリスナーが設定されます。 リスナーを設定するときに、`method_name`、`user_command_handler`、および省略可能な `create_user_response_handler` をパラメーターとして指定します。
        - `method_name` によってコマンド要求が定義されます。 このサンプルでは、モデルによって、コマンドの **reboot**、および **getMaxMinReport** が定義されます。
        - `user_command_handler` 関数により、コマンドを受信したときにデバイスによって行われる必要がある内容が定義されます。
        - `create_user_response_handler` 関数により、コマンドが正常に実行されたときに IoT ハブに送信される応答が作成されます。 この応答はポータルで確認できます。 この関数が指定されていない場合は、一般的な応答がサービスに送信されます。

    1. `execute_property_listener` を使用して、プロパティの更新をリッスンします。

    1. `send_telemetry` を使用するテレメトリの送信が開始されます。 サンプル コードにより、3 つのテレメトリの送信関数を呼び出すためにループが使用されます。 それぞれが 8 秒ごとに呼び出されます。

    1. すべてのリスナーとタスクを無効にし、**Q** または **q** キーが押されたときにループを終了します。

コードを確認したので、先ほどメモしたデバイス接続文字列を格納するために **IOTHUB_DEVICE_CONNECTION_STRING** という環境変数を作成します。次のコマンドを使用して、サンプルを実行します。

```cmd/sh
python pnp_temp_controller_with_thermostats.py
```

サンプル デバイスによって、数秒ごとにテレメトリ メッセージが IoT ハブに送信されます。

次の出力が表示されます。これは、デバイスによってハブにテレメトリ データが送信され、コマンドとプロパティの更新情報を受信する準備ができたことを示します。

![デバイス確認メッセージ](media/tutorial-multiple-components-python/multiple-component.png)

次の手順を完了するまで、サンプルを実行したままにしておきます。

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Azure IoT エクスプローラーを使用してコードを確認する

デバイス クライアントのサンプルが開始された後、Azure IoT エクスプローラー ツールを使用して、それが動作していることを確認します。

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、コンポーネントを使用する IoT プラグ アンド プレイ デバイスを IoT ハブに接続する方法を学習しました。 IoT プラグ アンド プレイ デバイス モデルの詳細については、以下を参照してください。

> [!div class="nextstepaction"]
> [IoT プラグ アンド プレイ プレビュー モデリング開発者ガイド](concepts-developer-guide.md)
