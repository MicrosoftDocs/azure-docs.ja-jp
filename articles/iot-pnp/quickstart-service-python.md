---
title: Azure IoT ソリューションに接続されている IoT プラグ アンド プレイ プレビュー デバイスとやり取りする (Python) | Microsoft Docs
description: Python を使用して、ご利用の Azure IoT ソリューションに接続されている IoT プラグ アンド プレイ プレビュー デバイスと接続してやり取りします。
author: elhorton
ms.author: elhorton
ms.date: 7/13/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: e3f00bb601cce17721c5247941588be1c2de788d
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352735"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-python"></a>クイック スタート:ご利用のソリューションに接続されている IoT プラグ アンド プレイ プレビュー デバイスとやり取りする (Python)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

IoT プラグ アンド プレイ プレビューを使用すると、基礎となるデバイスの実装に関する知識がなくてもデバイスのモデルとやり取りできるため、IoT が簡略化されます。 このクイックスタートでは、Python を使用して、ご利用のソリューションに接続されている IoT プラグ アンド プレイ デバイスに接続して制御する方法について説明します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>前提条件

このクイックスタートを完了するには、お使いの開発用マシン上に Python 3.7 が必要です。 [python.org](https://www.python.org/) から、複数のプラットフォームに対応した最新の推奨バージョンをダウンロードできます。次のコマンドを使用して、ご利用の Python のバージョンを確認できます。  

```cmd/sh
python --version
```

次のコマンドを実行して、[Python サービス SDK プレビュー パッケージ](https://pypi.org/project/azure-iot-hub/2.2.1rc0/)をインストールします。

```cmd/sh
pip3 install azure-iot-hub==2.2.1rc0
```

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

次のコマンドを実行して、ご利用のハブに対する _IoT ハブ接続文字列_を取得します。 この接続文字列はメモしてください。このクイックスタートの後半で使用します。

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

次のコマンドを実行して、ハブに追加したデバイスの "_デバイス接続文字列_" を取得します。 この接続文字列はメモしてください。このクイックスタートの後半で使用します。

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output
```

## <a name="run-the-sample-device"></a>サンプル デバイスを実行する

このクイックスタートでは、IoT プラグ アンド プレイ デバイスとして、Python で記述されたサンプルのサーモスタット デバイスを使用します。 サンプル デバイスを実行するには、次のようにします。

1. 任意のフォルダーでターミナル ウィンドウを開きます。 次のコマンドを実行して、[Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python) GitHub リポジトリをこの場所に複製します。

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. このターミナル ウィンドウは、**デバイス** ターミナルとして使用されます。 複製したリポジトリのフォルダーに移動し、 */azure-iot-sdk-python/azure-iot-device/samples/pnp* フォルダーに移動します。

1. _デバイス接続文字列_ を構成します。

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. 次のコマンドを使用して、サンプルのサーモスタット デバイスを実行します。

    ```cmd/sh
    python pnp_thermostat.py
    ```

1. デバイスが情報を送信し、自身がオンラインであると報告したことを示すメッセージが表示されます。 これらのメッセージは、デバイスによってハブへのテレメトリ データの送信が開始され、コマンドとプロパティの更新情報を受信する準備ができたことを示します。 このターミナルは閉じないでください。これは、サービスのサンプルが動作していることを確認するために必要になります。

## <a name="run-the-sample-solution"></a>サンプル ソリューションを実行する

このクイックスタートでは、Python のサンプルの IoT ソリューションを使用して、先ほど設定したサンプル デバイスとやり取りします。

1. **サービス** ターミナルとして使用する別のターミナル ウィンドウを開きます。 サービス SDK はプレビュー段階であるため、[Python SDK のプレビュー ブランチ](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh)からサンプルを複製する必要があります。

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python -b pnp-preview-refresh
    ```

1. この複製したリポジトリ ブランチのフォルダーに移動し、 */azure-iot-sdk-python/azure-iot-hub/samples* フォルダーに移動します。

1. デバイス ID と "_IoT Hub 接続文字列_" 用に環境変数を構成します。

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIOTHubConnectionString>
    set IOTHUB_DEVICE_ID=<Your device ID>
    ```

1. samples フォルダーには、`pnp` プレフィックスを持つ 4 つのサンプル ファイルがあります。 これらのサンプルは、IoT プラグ アンド プレイ デバイスとやり取りするために、各 API をどのように使用するかを示しています。

### <a name="get-digital-twin"></a>デジタル ツインを取得する

このサンプルを実行するには、**サービス** ターミナルで次のコマンドを使用します。

```cmd/sh
python pnp_get_digital_twin_sample.py
```

出力には、デバイスのデジタル ツインが示され、そのモデル ID が出力されます。

```cmd/sh
{'$dtId': 'mySimpleThermostat', '$metadata': {'$model': 'dtmi:com:example:Thermostat;1'}}
Model Id: dtmi:com:example:Thermostat;1
```

次のスニペットは、*pnp_get_digital_twin_sample.py* からのサンプル コードを示しています。

```python
    # Get digital twin and retrieve the modelId from it
    digital_twin = iothub_digital_twin_manager.get_digital_twin(device_id)
    if digital_twin:
        print(digital_twin)
        print("Model Id: " + digital_twin["$metadata"]["$model"])
    else:
        print("No digital_twin found")
```

### <a name="update-a-digital-twin"></a>デジタル ツインを更新する

このサンプルは、"*パッチ*" を使用して、デバイスのデジタル ツインを介してプロパティを更新する方法を示しています。 *pnp_update_digital_twin_sample.py* からの次のスニペットは、パッチを作成する方法を示しています。

```python
# If you already have a component thermostat1:
# patch = [{"op": "replace", "path": "/thermostat1/targetTemperature", "value": 42}]
patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
iothub_digital_twin_manager.update_digital_twin(device_id, patch)
print("Patch has been succesfully applied")
```

このサンプルを実行するには、**サービス** ターミナルで次のコマンドを使用します。

```cmd/sh
python pnp_update_digital_twin_sample.py
```

以下の出力を示す**デバイス** ターミナルで更新が適用されていることを確認できます。

```cmd/sh
the data in the desired properties patch was: {'targetTemperature': 42, '$version': 2}
previous values
42
```

**サービス** ターミナルで、パッチが成功したことを確認します。

```cmd/sh
Patch has been successfully applied
```

### <a name="invoke-a-command"></a>コマンドを呼び出す

コマンドを呼び出すには、サンプル *pnp_invoke_command_sample.py* を実行します。 このサンプルは、単純なサーモスタット デバイスでコマンドを呼び出す方法を示しています。 このサンプルを実行する前に、**サービス** ターミナルで `IOTHUB_COMMAND_NAME` および `IOTHUB_COMMAND_PAYLOAD` 環境変数を設定します。

```cmd/sh
set IOTHUB_COMMAND_NAME="getMaxMinReport" # this is the relevant command for the thermostat sample
set IOTHUB_COMMAND_PAYLOAD="hello world" # this payload doesn't matter for this sample
```

**サービス** ターミナルで、次のコマンドを使用してこのサンプルを実行します。
  
```cmd/sh
python pnp_invoke_command_sample.py
```

**サービス** ターミナルで、デバイスからの確認メッセージが表示されます。

```cmd/sh
{"tempReport": {"avgTemp": 34.5, "endTime": "13/07/2020 16:03:38", "maxTemp": 49, "minTemp": 11, "startTime": "13/07/2020 16:02:18"}}
```

**デバイス** ターミナルで、デバイスがコマンドを受け取ったことを確認します。

```cmd/sh
Command request received with payload
hello world
Will return the max, min and average temperature from the specified time hello to the current time
Done generating
{"tempReport": {"avgTemp": 34.2, "endTime": "09/07/2020 09:58:11", "maxTemp": 49, "minTemp": 10, "startTime": "09/07/2020 09:56:51"}}
Sent message
```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、IoT プラグ アンド プレイ デバイスを IoT ソリューションに接続する方法を学習しました。 IoT プラグ アンド プレイ デバイス モデルの詳細については、以下を参照してください。

> [!div class="nextstepaction"]
> [IoT プラグ アンド プレイ プレビュー モデリング開発者ガイド](concepts-developer-guide.md)
