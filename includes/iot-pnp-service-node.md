---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 7ab17f0d34ba7682778120e11aab562e106b5df7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95487812"
---
IoT プラグ アンド プレイを使用すると、基盤となるデバイスの実装に関する知識がなくてもデバイスの機能とやり取りできるので、IoT が簡略化されます。 このクイックスタートでは、Node.js を使用して、ご利用のソリューションに接続されている IoT プラグ アンド プレイ デバイスに接続して制御する方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

このクイックスタートを完了するには、開発用マシンに Node.js が必要です。 [nodejs.org](https://nodejs.org) から、複数のプラットフォームに対応した最新の推奨バージョンをダウンロードできます。

開発コンピューターに現在インストールされている Node.js のバージョンは、次のコマンドを使って確認できます。

```cmd/sh
node --version
```

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>サンプル コードを使用して SDK リポジトリをクローンする

[Node SDK リポジトリ](https://github.com/Azure/azure-iot-sdk-node)にあるサンプルをクローンします。 任意のフォルダーでターミナル ウィンドウを開きます。 次のコマンドを実行して、[Node.js 用 Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-node) の GitHub リポジトリをクローンします。

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="run-the-sample-device"></a>サンプル デバイスを実行する

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

サンプル構成の詳細については、[サンプルの readme](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/pnp/readme.md) を参照してください。

このクイックスタートでは、Node.js に IoT プラグ アンド プレイ デバイスとして記述されたサンプルのサーモスタット デバイスを使用します。 サンプル デバイスを実行するには、次のようにします。

1. ターミナル ウィンドウを開いて、GitHub からクローンした Microsoft Azure IoT SDK for Node.js リポジトリが格納されているローカル フォルダーに移動します。

1. このターミナル ウィンドウは、**デバイス** ターミナルとして使用されます。 クローンしたリポジトリのフォルダーに移動し、 */azure-iot-sdk-node/device/samples/pnp* フォルダーに移動します。 次のコマンドを実行して、すべての依存関係をインストールします。

    ```cmd/sh
    npm install
    ```

1. 次のコマンドを使用して、サンプルのサーモスタット デバイスを実行します。

    ```cmd/sh
    node simple_thermostat.js
    ```

1. デバイスが情報を送信し、自身がオンラインであると報告したことを示すメッセージが表示されます。 これらのメッセージは、デバイスによってハブへのテレメトリ データの送信が開始され、コマンドとプロパティの更新情報を受信する準備ができたことを示します。 このターミナルは閉じないでください。これは、サービスのサンプルが動作していることを確認するために必要になります。

## <a name="run-the-sample-solution"></a>サンプル ソリューションを実行する

「[IoT プラグ アンド プレイのクイックスタートとチュートリアル用の環境の設定](../articles/iot-pnp/set-up-environment.md)」では、IoT ハブとデバイスに接続するようにサンプルを構成するための 2 つの環境変数を作成しました。

* **IOTHUB_CONNECTION_STRING**: 先ほどメモした IoT ハブ接続文字列。
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"`。

このクイックスタートでは、Node.js 内の IoT ソリューションのサンプルを使用して、先ほど設定したサンプル デバイスとやり取りします。

1. **サービス** ターミナルとして使用する別のターミナル ウィンドウを開きます。

1. クローンした Node SDK リポジトリで、 */azure-iot-sdk-node/service/samples/javascript* フォルダーに移動します。 次のコマンドを実行して、すべての依存関係をインストールします。

    ```cmd/sh
    npm install
    ```

### <a name="read-a-property"></a>プロパティを読み取る

1. **デバイス** ターミナルでサンプルのサーモスタット デバイスを実行したときに、そのオンライン状態を示す次のメッセージが表示されました。

    ```cmd/sh
    properties have been reported for component
    sending telemetry message 0...
    ```

1. "**サービス**" ターミナルにアクセスし、次のコマンドを使用して、デバイス情報を読み取るためのサンプルを実行します。

    ```cmd/sh
    node twin.js
    ```

1. **サービス** ターミナル出力の、デバイス ツインの応答に注目してください。 デバイスのモデル ID と関連プロパティが報告されているのがわかります。

    ```json
    Model Id: dtmi:com:example:Thermostat;1
    {
      "deviceId": "my-pnp-device",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "Njc3MDMxNDcy",
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
      "version": 4,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2020-10-05T11:35:19.4574755Z"
          },
          "$version": 1
        },
        "reported": {
          "maxTempSinceLastReboot": 31.343640523762232,
          "serialNumber": "123abc",
          "$metadata": {
            "$lastUpdated": "2020-10-05T11:35:23.7339042Z",
            "maxTempSinceLastReboot": {
              "$lastUpdated": "2020-10-05T11:35:23.7339042Z"
            },
            "serialNumber": {
              "$lastUpdated": "2020-10-05T11:35:23.7339042Z"
            }
          },
          "$version": 3
        }
      },
      "capabilities": {
        "iotEdge": false
      },
      "tags": {}
    }
    ```

1. 次のスニペットは、デバイス ツインのモデル ID を取得する、*twin.js* のコードを示しています。

    ```javascript
    var registry = Registry.fromConnectionString(connectionString);
    registry.getTwin(deviceId, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log('Model Id: ' + twin.modelId);
        //...
      }
      //...
    }
    ```

このシナリオでは、`Model Id: dtmi:com:example:Thermostat;1`が出力されます。

> [!NOTE]
> これらのサービス サンプルでは、**IoT Hub サービス クライアント** からの **Registry** クラスを使用します。 デジタル ツイン API を含む API の詳細については、[サービス開発者ガイド](../articles/iot-pnp/concepts-developer-guide-service.md)を参照してください。

### <a name="update-a-writable-property"></a>書き込み可能なプロパティを更新する

1. コード エディターで、*twin.js* ファイルを開きます。

1. サンプル コードを見てみましょう。ここには、デバイス ツインを更新する 2 つの方法が示されています。 最初の方法を使用するには、`twinPatch` 変数を次のように変更します。

    ```javascript
    var twinPatch = {
      tags: {
        city: "Redmond"
      },
      properties: {
        desired: {
          targetTemperature: 42
        }
      }
    };
    ```

    `targetTemperature` プロパティは、サーモスタット デバイス モデルの書き込み可能なプロパティとして定義されています。

1. **サービス** ターミナルで、次のコマンドを使用して、プロパティを更新するためのサンプルを実行します。

    ```cmd/sh
    node twin.js
    ```

1. **デバイス** ターミナルで、デバイスによって更新情報が受信されていることを確認します。

    ```cmd/sh
    The following properties will be updated for the default component:
    {
      targetTemperature: {
        value: 42,
        ac: 200,
        ad: 'Successfully executed patch for targetTemperature',
        av: 2
      }
    }
    updated the property
    ```

1. **サービス** ターミナルで、次のコマンドを実行して、プロパティが更新されていることを確認します。

    ```cmd/sh
    node twin.js
    ```

1. **サービス** ターミナル出力の `reported` プロパティ セクションに、更新されたターゲット温度がレポートされているのがわかります。 デバイスでの更新が完了するまで、しばらく時間がかかる場合があります。 デバイスによってプロパティの更新が処理されるまで、この手順は繰り返します。

    ```json
    "reported": {
      //...
      "targetTemperature": {
        "value": 42,
        "ac": 200,
        "ad": "Successfully executed patch for targetTemperature",
        "av": 4
      },
      //...
    }
    ```

### <a name="invoke-a-command"></a>コマンドを呼び出す

1. *device_method.js* ファイルを開き、コードを確認します。

1. "**サービス**" ターミナルにアクセスします。 次のコマンドを使用して、コマンドを呼び出すためのサンプルを実行します。

    ```cmd/sh
    set IOTHUB_METHOD_NAME=getMaxMinReport
    set IOTHUB_METHOD_PAYLOAD=commandpayload
    node device_method.js
    ```

1. **サービス** ターミナル出力に、次の確認が表示されます。

    ```cmd/sh
    getMaxMinReport on my-pnp-device:
    {
      "status": 200,
      "payload": {
        "maxTemp": 23.460596940801928,
        "minTemp": 23.460596940801928,
        "avgTemp": 23.460596940801928,
        "endTime": "2020-10-05T12:48:08.562Z",
        "startTime": "2020-10-05T12:47:54.450Z"
      }
    }
    ```

1. **デバイス** ターミナルで、コマンドが確認されていることがわかります。

    ```cmd/sh
    MaxMinReport commandpayload
    Response to method 'getMaxMinReport' sent successfully.
    ```
