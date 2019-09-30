---
title: Azure IoT Hub (ノード) を使用したクラウドからデバイスへのメッセージ| Microsoft Docs
description: Azure IoT SDK for Node.js を使用して、クラウドからデバイスへのメッセージを Azure IoT Hub からデバイスへ送信する方法。 クラウドからデバイスへのメッセージを受信するためにシミュレートされたデバイス アプリを変更し、クラウドからデバイスへのメッセージを送信するためにバックエンド アプリを変更します。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.openlocfilehash: ba14a6bb9e234a5eae34232fc617f8b04284cd4f
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147458"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-nodejs"></a>IoT Hub を使用したクラウドからデバイスへのメッセージの送信 (Node.js)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub は、何百万ものデバイスとソリューション バックエンドの間に信頼性のある保護された双方向通信を確立するのに役立つ、フル マネージドのサービスです。 [デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-node.md)に関するクイックスタートには、IoT ハブの作成方法、IoT ハブでデバイス ID をプロビジョニングする方法、および device-to-cloud メッセージを送信するシミュレートされたデバイス アプリをコード化する方法が示されています。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

このチュートリアルは、[デバイスから IoT ハブへのテレメトリ送信](quickstart-send-telemetry-node.md)に関する記事に基づいて作成されています。 次の方法について説明します。

* ソリューション バックエンドから IoT Hub を介して単一のデバイスにクラウドからデバイスへのメッセージを送信する。
* クラウドからデバイスへのメッセージをデバイスで受信する。
* ソリューション バックエンドから、IoT Hub からデバイスに送信されたメッセージの配信確認 ("*フィードバック*") を要求する。

cloud-to-device メッセージの詳細については、[IoT Hub 開発者ガイド](iot-hub-devguide-messaging.md)を参照してください。

このチュートリアルの最後には、次の 2 つの Node.js コンソール アプリを実行します。

* **SimulatedDevice**。[デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-node.md)に関するページで作成されたアプリの修正版であり、IoT ハブに接続し、cloud-to-device メッセージを受信します。

* **SendCloudToDeviceMessage**。cloud-to-device メッセージを IoT Hub を介してシミュレートされたデバイス アプリに送信し、その配信確認を受け取ります。

> [!NOTE]
> IoT Hub には、Azure IoT device SDK シリーズを介した多数のデバイス プラットフォームや言語 (C、Java、Python、JavaScript など) に対する SDK サポートがあります。 このチュートリアルのコード (一般的には Azure IoT Hub) にデバイスを接続するための詳しい手順については、 [Azure IoT デベロッパー センター](https://azure.microsoft.com/develop/iot)のページを参照してください。
>

## <a name="prerequisites"></a>前提条件

* Node.js バージョン 10.0.x 以降。 「[Prepare your development environment (開発環境を準備する)](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md)」では、このチュートリアルのために Node.js を Windows または Linux にインストールする方法が説明されています。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 (アカウントがない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial) を数分で作成できます)。

## <a name="receive-messages-in-the-simulated-device-app"></a>シミュレートされたデバイス アプリでメッセージを受信する

このセクションでは、[デバイスから IoT Hub へのテレメトリ送信](quickstart-send-telemetry-node.md)に関するページで作成したシミュレート デバイス アプリを変更して、cloud-to-device メッセージを IoT ハブから受信するようにします。

1. テキスト エディターを使用して **SimulatedDevice.js** ファイルを開きます。 このファイルは、[デバイスから IoT ハブへの利用統計情報の送信](quickstart-send-telemetry-node.md)に関するクイックスタートでダウンロードした Node.js サンプル コードのルート フォルダーの **iot-hub\Quickstarts\simulated-device** フォルダーにあります。

2. IoT Hub から送信されたメッセージを受信するために、デバイス クライアントにハンドラーを登録します。 次のスニペットのように、デバイス クライアントを作成する行の直後に、`client.on` の呼び出しを追加します。

    ```javascript
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);

    client.on('message', function (msg) {
      console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
      client.complete(msg, function (err) {
        if (err) {
          console.error('complete error: ' + err.toString());
        } else {
          console.log('complete sent');
        }
      });
    });
    ```

    この例では、メッセージが処理されたことを IoT Hub に通知するために、デバイスで **complete** 関数が呼び出されます。 MQTT トランスポートを使用している場合は、**complete** の呼び出しは必要なく省略できます。 HTTPS と AMQP の場合は必要です。
  
   > [!NOTE]
   > トランスポートとして AMQP の代わりに HTTPS を使用した場合、**DeviceClient** インスタンスが IoT Hub からのメッセージをチェックする頻度は低くなります (25 分に 1 回未満)。 MQTT、AMQP、および HTTPS のサポートの相違点と、IoT Hub スロットルの詳細については、[IoT Hub 開発者ガイド](iot-hub-devguide-messaging.md)を参照してください。
   >

## <a name="get-the-iot-hub-connection-string"></a>IoT ハブ接続文字列を取得する

この記事では、[デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-node.md)に関するページで作成した IoT ハブを介して cloud-to-device メッセージを送信するバックエンド サービスを作成します。 cloud-to-device メッセージを送信するサービスには、**サービス接続**のアクセス許可が必要となります。 既定では、どの IoT Hub も、このアクセス許可を付与する **service** という名前の共有アクセス ポリシーがある状態で作成されます。

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>C2D メッセージを送信する

このセクションでは、クラウドからデバイスへのメッセージを、シミュレートされたデバイス アプリに送信する Node.js コンソール アプリを作成します。 [デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-node.md)に関するクイックスタートで追加したデバイスのデバイス ID が必要です。 また、先ほど「[IoT ハブ接続文字列を取得する](#get-the-iot-hub-connection-string)」でコピーしておいた IoT ハブ接続文字列も必要です。

1. 空のフォルダーを **sendcloudtodevicemessage**という名前で作成します。 コマンド プロンプトで次のコマンドを使用して、**sendcloudtodevicemessage** フォルダー内に package.json ファイルを作成します。 次の既定値をすべてそのまま使用します。

    ```shell
    npm init
    ```

2. コマンド プロンプトで、**sendcloudtodevicemessage** フォルダーに移動し、次のコマンドを実行して、**azure-iothub** パッケージをインストールします。

    ```shell
    npm install azure-iothub --save
    ```

3. テキスト エディターを使用して、**sendcloudtodevicemessage** フォルダーに **SendCloudToDeviceMessage.js** ファイルを作成します。

4. **SendCloudToDeviceMessage.js** ファイルの先頭に、次の `require` ステートメントを追加します。

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. 次のコードを **SendCloudToDeviceMessage.js** ファイルに追加します。 プレースホルダー "{iot hub connection string}" と "{device id}" の値は、先ほどメモした IoT ハブ接続文字列とデバイス ID に置き換えてください。

    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. 操作結果をコンソールに出力するための次の関数を追加します。

    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. 配信フィードバック メッセージをコンソールに出力するための次の関数を追加します。

    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. デバイスにメッセージを送信し、クラウドからデバイスへのメッセージが配信されたことの確認応答がデバイスからあったときにフィードバック メッセージを処理するための次のコードを追加します。

    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```

9. **SendCloudToDeviceMessage.js** ファイルを保存して閉じます。

## <a name="run-the-applications"></a>アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

1. **simulated-device** フォルダーからコマンド プロンプトで次のコマンドを実行し、IoT Hub にテレメトリを送信して、クラウドからデバイスへのメッセージを待機します。

    ```shell
    node SimulatedDevice.js
    ```

    ![シミュレーション済みデバイス アプリを実行する](./media/iot-hub-node-node-c2d/receivec2d.png)

2. **sendcloudtodevicemessage** フォルダーからコマンド プロンプトで次のコマンドを実行し、クラウドからデバイスへのメッセージを送信して、配信確認のフィードバックを待機します。

    ```shell
    node SendCloudToDeviceMessage.js
    ```

    ![クラウドからデバイスへのコマンドを送信するアプリを実行する](./media/iot-hub-node-node-c2d/sendc2d.png)

   > [!NOTE]
   > わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードでは、「[一時的な障害の処理](/azure/architecture/best-practices/transient-faults)」の記事で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。
   >

## <a name="next-steps"></a>次の手順

このチュートリアルでは、クラウドからデバイスへのメッセージを送受信する方法を学習しました。

IoT Hub を使用する完全なエンド ツー エンド ソリューションの例については、[Azure IoT リモート監視ソリューション アクセラレータ](https://azure.microsoft.com/documentation/suites/iot-suite/)に関するページをご覧ください。

IoT Hub を使用したソリューションの開発に関する詳細については、[IoT Hub 開発者ガイド](iot-hub-devguide.md)をご覧ください。
