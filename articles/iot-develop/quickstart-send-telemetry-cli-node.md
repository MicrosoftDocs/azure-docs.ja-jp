---
title: Azure IoT Hub にデバイス テレメトリを送信するクイックスタート (Node.js)
description: このクイックスタートでは、Node.js 用 Azure IoT Hub デバイス SDK を使用して、デバイスから IoT ハブにテレメトリを送信します。
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: node
ms.topic: quickstart
ms.date: 01/11/2021
ms.openlocfilehash: 97fcff4706d6da968c93426f85569fed9af95aac
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102197811"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-nodejs"></a>クイックスタート: デバイスから IoT ハブにテレメトリを送信する (Node.js)

**適用対象**: [デバイス アプリケーション開発](about-iot-develop.md#device-application-development)

このクイックスタートでは、基本的な IoT デバイス アプリケーション開発のワークフローについて説明します。 Azure CLI を使用して、Azure IoT ハブとシミュレートされたデバイスを作成した後、Azure IoT Node.js SDK を使用してデバイスにアクセスし、テレメトリをハブに送信します。

## <a name="prerequisites"></a>前提条件
- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料でアカウントを 1 つ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)作成してください。
- Azure CLI。 このクイックスタートのすべてのコマンドは、ブラウザーで実行されるインタラクティブ CLI シェルである Azure Cloud Shell を使用して実行できます。 Cloud Shell を使用する場合は、何もインストールする必要はありません。 CLI をローカルで使用する場合、このクイックスタートでは、Azure CLI バージョン 2.0.76 以降が必要です。 バージョンを確認するには、az --version を実行します。 インストールまたはアップグレードする必要には、「[Azure CLI のインストール]( /cli/azure/install-azure-cli)」をご覧ください。
- [Node.js 10 以上](https://nodejs.org)。 Azure Cloud Shell を使用している場合は、インストールされている Node.js のバージョンを更新しないでください。 Azure Cloud Shell には、最新の Node.js バージョンが既に含まれています。

    開発マシン上の Node.js の現在のバージョンを、次のコマンドを使って確認します。

    ```cmd/sh
        node --version
    ```

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-nodejs-sdk-to-send-messages"></a>Node.js SDK を使用してメッセージを送信する
このセクションでは、Node.js SDK を使用して、シミュレートされたデバイスから IoT ハブにメッセージを送信します。 

1. 新しいターミナル ウィンドウを開きます。 このターミナルを使用して Node.js SDK をインストールし、Node.js のサンプル コードを操作します。 これで 2 つのターミナルを開いたことになります。1 つは Node.js を操作するために今開いたターミナル、もう 1 つは、前のセクションで Azure CLI コマンドを入力するために使用した CLI シェルです。 

1. [Azure IoT Node.js SDK デバイス サンプル](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)をローカル コンピューターにコピーします。

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. *azure-iot-sdk-node/device/samples* フォルダーに移動します。

    ```console
    cd azure-iot-sdk-node/device/samples
    ```
1. Azure IoT Node.js SDK および必要な依存関係をインストールします。

    ```console
    npm install
    ```
    このコマンドを実行すると、*package.json* ファイルに指定されている適切な依存関係が device/samples ディレクトリにインストールされます。

1. デバイスの接続文字列を `DEVICE_CONNECTION_STRING` という環境変数として設定します。 使用する文字列値は、前のセクションで、シミュレートされた Node.js デバイスを作成した後に取得した文字列です。 

    **Windows (cmd)**

    ```console
    set DEVICE_CONNECTION_STRING=<your connection string here>
    ```

    > [!NOTE]
    > Windows CMD では、接続文字列を囲む引用符は入力しません。

    **Linux (bash)**

    ```bash
    export DEVICE_CONNECTION_STRING="<your connection string here>"
    ```

1. 開いている CLI シェルで、[az iot hub monitor-events](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-monitor-events) コマンドを実行し、シミュレートされた IoT デバイス上のイベントの監視を開始します。  イベント メッセージは、到着した時点でターミナルに出力されます。

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. Node.js ターミナルで、インストールされているサンプルファイル *simple_sample_device.js* のコードを実行します。 このコードは、シミュレートされた IoT デバイスにアクセスして、IoT ハブにメッセージを送信します。

    Node.js サンプルをターミナルから実行するには、次のようにします。
    ```console
    node ./simple_sample_device.js
    ```

    サンプルの Node.js コードは、必要に応じて JavaScript IDE から実行することができます。
    ```javascript
    'use strict';

    const Protocol = require('azure-iot-device-mqtt').Mqtt;
    // Uncomment one of these transports and then change it in fromConnectionString to test other transports
    // const Protocol = require('azure-iot-device-amqp').AmqpWs;
    // const Protocol = require('azure-iot-device-http').Http;
    // const Protocol = require('azure-iot-device-amqp').Amqp;
    // const Protocol = require('azure-iot-device-mqtt').MqttWs;
    const Client = require('azure-iot-device').Client;
    const Message = require('azure-iot-device').Message;

    // String containing Hostname, Device Id & Device Key in the following formats:
    //  "HostName=<iothub_host_name>;DeviceId=<device_id>;SharedAccessKey=<device_key>"
    const deviceConnectionString = process.env.DEVICE_CONNECTION_STRING;
    let sendInterval;

    function disconnectHandler () {
    clearInterval(sendInterval);
    client.open().catch((err) => {
        console.error(err.message);
    });
    }

    // The AMQP and HTTP transports have the notion of completing, rejecting or abandoning the message.
    // For example, this is only functional in AMQP and HTTP:
    // client.complete(msg, printResultFor('completed'));
    // If using MQTT calls to complete, reject, or abandon are no-ops.
    // When completing a message, the service that sent the C2D message is notified that the message has been processed.
    // When rejecting a message, the service that sent the C2D message is notified that the message won't be processed by the device. the method to use is client.reject(msg, callback).
    // When abandoning the message, IoT Hub will immediately try to resend it. The method to use is client.abandon(msg, callback).
    // MQTT is simpler: it accepts the message by default, and doesn't support rejecting or abandoning a message.
    function messageHandler (msg) {
    console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
    client.complete(msg, printResultFor('completed'));
    }

    function generateMessage () {
    const windSpeed = 10 + (Math.random() * 4); // range: [10, 14]
    const temperature = 20 + (Math.random() * 10); // range: [20, 30]
    const humidity = 60 + (Math.random() * 20); // range: [60, 80]
    const data = JSON.stringify({ deviceId: 'myFirstDevice', windSpeed: windSpeed, temperature: temperature, humidity: humidity });
    const message = new Message(data);
    message.properties.add('temperatureAlert', (temperature > 28) ? 'true' : 'false');
    return message;
    }

    function errorCallback (err) {
    console.error(err.message);
    }

    function connectCallback () {
    console.log('Client connected');
    // Create a message and send it to the IoT Hub every two seconds
    sendInterval = setInterval(() => {
        const message = generateMessage();
        console.log('Sending message: ' + message.getData());
        client.sendEvent(message, printResultFor('send'));
    }, 2000);

    }

    // fromConnectionString must specify a transport constructor, coming from any transport package.
    let client = Client.fromConnectionString(deviceConnectionString, Protocol);

    client.on('connect', connectCallback);
    client.on('error', errorCallback);
    client.on('disconnect', disconnectHandler);
    client.on('message', messageHandler);

    client.open()
    .catch(err => {
    console.error('Could not connect: ' + err.message);
    });

    // Helper function to print results in the console
    function printResultFor(op) {
    return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
    };
    }
    ```

シミュレートされたテレメトリ メッセージを Node.js コードがデバイスから IoT ハブに送信すると、イベントを監視している CLI シェルにそのメッセージが表示されます。

```output
event:
  component: ''
  interface: ''
  module: ''
  origin: <your device name>
  payload: '{"deviceId":"myFirstDevice","windSpeed":11.853592092144627,"temperature":22.62484121157508,"humidity":66.17960805575937}'
```

デバイスが安全に接続され、Azure IoT Hub にテレメトリを送信するようになりました。

## <a name="clean-up-resources"></a>リソースをクリーンアップする
このクイックスタートで作成した Azure リソースが不要になった場合は、Azure CLI を使用して削除できます。

> [!IMPORTANT]
> リソース グループを削除すると、元に戻すことができません。 リソース グループとそこに含まれるすべてのリソースは完全に削除されます。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。 

名前でリソース グループを削除するには、以下の手順を実行します。
1. [az group delete](/cli/azure/group#az-group-delete) コマンドを実行します。 このコマンドにより、作成したリソース グループ、IoT Hub、デバイスの登録が削除されます。

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. [az group list](/cli/azure/group#az-group-list) コマンドを実行して、リソース グループが削除されていることを確認します。  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、クラウドに対してデバイスを安全に接続し、device-to-cloud テレメトリを送信するための Azure IoT アプリケーションの基本的なワークフローについて説明しました。 Azure CLI を使用して、IoT ハブとシミュレートされたデバイスを作成した後、Azure IoT Node.js SDK を使用してデバイスにアクセスし、テレメトリをハブに送信します。 

次のステップとして、アプリケーション サンプルを通じて Azure IoT Node.js SDK を詳しく見てみましょう。

- [その他の Node.js サンプル](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples): このディレクトリには、IoT Hub のシナリオを紹介する多数のサンプルが Node.js SDK リポジトリから取り上げられています。