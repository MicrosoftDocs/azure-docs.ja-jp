---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 6b861baea93c2c57b8f66ebac928a7e4fd3adfa8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95487807"
---
IoT プラグ アンド プレイを使用すると、基礎となるデバイスの実装に関する知識がなくてもデバイスのモデルを操作できるため、IoT が簡略化されます。 このクイックスタートでは、Python を使用して、ご利用のソリューションに接続されている IoT プラグ アンド プレイ デバイスに接続して制御する方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

このクイックスタートを完了するには、お使いの開発用マシン上に Python 3.7 が必要です。 [python.org](https://www.python.org/) から、複数のプラットフォームに対応した最新の推奨バージョンをダウンロードできます。次のコマンドを使用して、ご利用の Python のバージョンを確認できます。  

```cmd/sh
python --version
```

**azure-iot-device** パッケージは PIP として公開されています。

ローカルの Python 環境で、次のようにパッケージをインストールします。

```cmd/sh
pip install azure-iot-device
```

次のコマンドを実行して、**azure-iot-hub** パッケージをインストールします。

```cmd/sh
pip install azure-iot-hub
```

## <a name="run-the-sample-device"></a>サンプル デバイスを実行する

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

サンプル構成の詳細については、[サンプルの readme](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md) を参照してください。

このクイックスタートでは、IoT プラグ アンド プレイ デバイスとして、Python で記述されたサンプルのサーモスタット デバイスを使用します。 サンプル デバイスを実行するには、次のようにします。

1. 任意のフォルダーでターミナル ウィンドウを開きます。 次のコマンドを実行して、[Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python) GitHub リポジトリをこの場所に複製します。

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. このターミナル ウィンドウは、**デバイス** ターミナルとして使用されます。 複製したリポジトリのフォルダーに移動し、 */azure-iot-sdk-python/azure-iot-device/samples/pnp* フォルダーに移動します。

1. 次のコマンドを使用して、サンプルのサーモスタット デバイスを実行します。

    ```cmd/sh
    python simple_thermostat.py
    ```

1. デバイスが情報を送信し、自身がオンラインであると報告したことを示すメッセージが表示されます。 これらのメッセージは、デバイスによってハブへのテレメトリ データの送信が開始され、コマンドとプロパティの更新情報を受信する準備ができたことを示します。 このターミナルは閉じないでください。これは、サービスのサンプルが動作していることを確認するために必要になります。

## <a name="run-the-sample-solution"></a>サンプル ソリューションを実行する

このクイックスタートでは、Python のサンプルの IoT ソリューションを使用して、先ほど設定したサンプル デバイスとやり取りします。

1. **サービス** ターミナルとして使用する別のターミナル ウィンドウを開きます。

1. 複製された Python SDK リポジトリの */azure-iot-sdk-python/azure-iot-hub/samples* フォルダーに移動します。

1. *registry_manager_pnp_sample.py* ファイルを開き、コードを確認します。 このサンプルは、**IoTHubRegistryManager** クラスを使用して IoT プラグ アンド プレイ デバイスと対話する方法を示しています。

> [!NOTE]
> これらのサービス サンプルでは、**IoT Hub サービス クライアント** からの **IoTHubRegistryManager** クラスを使用します。 デジタル ツイン API を含む API の詳細については、[サービス開発者ガイド](../articles/iot-pnp/concepts-developer-guide-service.md)を参照してください。

### <a name="get-the-device-twin"></a>デバイス ツインを取得する

「[IoT プラグ アンド プレイのクイックスタートとチュートリアル用の環境の設定](../articles/iot-pnp/set-up-environment.md)」では、IoT ハブとデバイスに接続するようにサンプルを構成するための 2 つの環境変数を作成しました。

* **IOTHUB_CONNECTION_STRING**: 先ほどメモした IoT ハブ接続文字列。
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"`。

このサンプルを実行するには、**サービス** ターミナルで次のコマンドを使用します。

```cmd/sh
set IOTHUB_METHOD_NAME="getMaxMinReport"
set IOTHUB_METHOD_PAYLOAD="hello world"
python registry_manager_pnp_sample.py
```

> [!NOTE]
> このサンプルを Linux 上で実行する場合は、`set` の代わりに `export` を使用してください。

出力には、デバイス ツインが示され、そのモデル ID が出力されます。

```cmd/sh
The Model ID for this device is:
dtmi:com:example:Thermostat;1
```

次のスニペットは、*registry_manager_pnp_sample.py* からのサンプル コードを示しています。

```python
    # Create IoTHubRegistryManager
    iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

    # Get device twin
    twin = iothub_registry_manager.get_twin(device_id)
    print("The device twin is: ")
    print("")
    print(twin)
    print("")

    # Print the device's model ID
    additional_props = twin.additional_properties
    if "modelId" in additional_props:
        print("The Model ID for this device is:")
        print(additional_props["modelId"])
        print("")
```

### <a name="update-a-device-twin"></a>デバイス ツインを更新する

このサンプルは、デバイスの `targetTemperature` 書き込み可能プロパティを更新する方法を示しています。

```python
    # Update twin
    twin_patch = Twin()
    twin_patch.properties = TwinProperties(
        desired={"targetTemperature": 42}
    )  # this is relevant for the thermostat device sample
    updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
    print("The twin patch has been successfully applied")
    print("")
```

以下の出力を示す **デバイス** ターミナルで更新が適用されていることを確認できます。

```cmd/sh
the data in the desired properties patch was: {'targetTemperature': 42, '$version': 2}
```

**サービス** ターミナルで、パッチが成功したことを確認します。

```cmd/sh
The twin patch has been successfully applied
```

### <a name="invoke-a-command"></a>コマンドを呼び出す

サンプルでは、次に、コマンドを呼び出します。

**サービス** ターミナルで、デバイスからの確認メッセージが表示されます。

```cmd/sh
The device method has been successfully invoked
```

**デバイス** ターミナルで、デバイスがコマンドを受け取ったことを確認します。

```cmd/sh
Command request received with payload
hello world
Will return the max, min and average temperature from the specified time hello to the current time
Done generating
{"tempReport": {"avgTemp": 34.2, "endTime": "09/07/2020 09:58:11", "maxTemp": 49, "minTemp": 10, "startTime": "09/07/2020 09:56:51"}}
```
