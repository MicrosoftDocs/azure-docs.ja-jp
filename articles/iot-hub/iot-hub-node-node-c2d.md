---
title: "Azure IoT Hub (ノード) を使用したクラウドからデバイスへのメッセージ| Microsoft Docs"
description: "Azure IoT SDK for Node.js を使用して、クラウドからデバイスへのメッセージを Azure IoT Hub からデバイスへ送信する方法。 クラウドからデバイスへのメッセージを受信するためにシミュレートされたデバイス アプリを変更し、クラウドからデバイスへのメッセージを送信するためにバックエンド アプリを変更します。"
services: iot-hub
documentationcenter: nodejs
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 3ca8a78f-ade2-46e8-8a49-d5d599cdf1f1
ms.service: iot-hub
ms.devlang: javascript
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/16/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 2e4220bedcb0091342fd9386669d523d4da04d1c
ms.openlocfilehash: 312e9081c8597f59c32e99d594f2e729410986d8
ms.lasthandoff: 12/16/2016


---
# <a name="send-cloud-to-device-messages-with-iot-hub-node"></a>IoT Hub を使用したクラウドからデバイスへのメッセージの送信 (Node)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>はじめに
Azure IoT Hub は、何百万ものデバイスとソリューション バックエンドの間に信頼性のある保護された双方向通信を確立するのに役立つ、完全に管理されたサービスです。 [IoT Hub の概要]チュートリアルには、IoT ハブの作成方法、IoT ハブでデバイス ID をプロビジョニングする方法、およびデバイスからクラウドへのメッセージを送信するシミュレートされたデバイス アプリをコード化する方法が示されています。

このチュートリアルは、[IoT Hub の概要]に関するページのチュートリアルに基づいて作成されており、 次の方法について説明します。

* ソリューション バックエンドから IoT Hub を介して単一のデバイスにクラウドからデバイスへのメッセージを送信する。
* クラウドからデバイスへのメッセージをデバイスで受信する。
* IoT Hub からデバイスに送信されたメッセージの配信確認 ("*フィードバック*") を、ソリューション バックエンドから要求する。

クラウドからデバイスへのメッセージの詳細については、[IoT Hub 開発者ガイド][IoT Hub developer guide - C2D]を参照してください。

このチュートリアルの最後には、次の&2; つの Node.js コンソール アプリを実行します。

* **SimulatedDevice**。「[IoT Hub の概要]」で作成されたアプリケーションの修正バージョン。これは、IoT Hub に接続し、クラウドからデバイスへのメッセージを受け取ります。
* **SendCloudToDeviceMessage**。クラウドからデバイスへのメッセージを IoT Hub を介してシミュレートされたデバイス アプリに送信し、その配信確認を受け取ります。

> [!NOTE]
> IoT Hub には、Azure IoT device SDK を介した多数のデバイス プラットフォームや言語 (C、Java、Javascript など) に対する SDK サポートがあります。 このチュートリアルのコード (一般的には Azure IoT Hub) にデバイスを接続するための詳しい手順については、 [Azure IoT デベロッパー センター]のページを参照してください。
> 
> 

このチュートリアルを完了するには、以下が必要です。

* Node.js バージョン 0.10.x 以降。
* アクティブな Azure アカウント。 (アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成できます)。

## <a name="receive-messages-in-the-simulated-device-app"></a>シミュレートされたデバイス アプリでメッセージを受信する
このセクションでは、[IoT Hub の概要]に関するページで作成したシミュレートされたデバイス アプリを、クラウドからデバイスへのメッセージを IoT ハブから受信するように変更します。

1. テキスト エディターを使用して SimulatedDevice.js ファイルを開きます。
2. IoT Hub から送信されたメッセージを処理するように **connectCallback** 関数を変更します。 この例では、メッセージが処理されたことを IoT Hub に通知するために、デバイスは必ず **complete** 関数を呼び出します。 変更後の **connectCallback** 関数は次のスニペットのようになります。
   
    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
        client.on('message', function (msg) {
          console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
          client.complete(msg, printResultFor('completed'));
        });
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var windSpeed = 10 + (Math.random() * 4);
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', windSpeed: windSpeed });
            var message = new Message(data);
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```
   
   > [!NOTE]
   > トランスポートとして AMQP の代わりに HTTP/1 を使用した場合、**DeviceClient** インスタンスが IoT Hub からのメッセージをチェックする頻度は低くなります (25 分に 1 回未満)。 MQTT、AMQP、および HTTP のサポートの相違点と、IoT Hub スロットルの詳細については、[IoT Hub 開発者ガイド][IoT Hub developer guide - C2D]を参照してください。
   > 
   > 

## <a name="send-a-cloud-to-device-message"></a>C2D メッセージを送信する
このセクションでは、クラウドからデバイスへのメッセージを、シミュレートされたデバイス アプリに送信する Node.js コンソール アプリを作成します。 [IoT Hub の概要]のチュートリアルで追加したデバイスのデバイス ID が必要です。 また、ハブの IoT Hub 接続文字列も必要です ([Azure Portal] で確認できます)。

1. 空のフォルダーを **sendcloudtodevicemessage**という名前で作成します。 コマンド プロンプトで次のコマンドを使用して、**sendcloudtodevicemessage** フォルダー内に package.json ファイルを作成します。 次の既定値をすべてそのまま使用します。
   
    ```
    npm init
    ```
2. コマンド プロンプトで、**sendcloudtodevicemessage** フォルダーに移動し、次のコマンドを実行して、**azure-iothub** パッケージをインストールします。
   
    ```
    npm install azure-iothub --save
    ```
3. テキスト エディターを使用して、**sendcloudtodevicemessage** フォルダーに **SendCloudToDeviceMessage.js** ファイルを作成します。
4. **SendCloudToDeviceMessage.js** ファイルの先頭に、次の `require` ステートメントを追加します。
   
    ```
    'use strict';
   
    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```
5. 次のコードを **SendCloudToDeviceMessage.js** ファイルに追加します。 IoT Hub 接続文字列のプレースホルダーの値は、[IoT Hub の概要]のチュートリアルで作成したハブの IoT Hub の接続文字列で置き換えてください。 ターゲット デバイスのプレースホルダーを、次のように [IoT Hub の概要]のチュートリアルで追加したデバイスのデバイス ID で置き換えます。
   
    ```
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';
   
    var serviceClient = Client.fromConnectionString(connectionString);
    ```
6. 操作結果をコンソールに出力するための次の関数を追加します。
   
    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```
7. 配信フィードバック メッセージをコンソールに出力するための次の関数を追加します。
   
    ```
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```
8. デバイスにメッセージを送信し、クラウドからデバイスへのメッセージが配信されたことの確認応答がデバイスからあったときにフィードバック メッセージを処理するための次のコードを追加します。
   
    ```
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

1. **simulateddevice** フォルダーからコマンド プロンプトで次のコマンドを実行し、IoT Hub にテレメトリを送信して、クラウドからデバイスへのメッセージを待機します。
   
    ```
    node SimulatedDevice.js 
    ```
   
    ![シミュレーション済みデバイス アプリを実行する][img-simulated-device]
2. **sendcloudtodevicemessage** フォルダーからコマンド プロンプトで次のコマンドを実行し、クラウドからデバイスへのメッセージを送信して、配信確認のフィードバックを待機します。
   
    ```
    node SendCloudToDeviceMessage.js 
    ```
   
    ![クラウドからデバイスへのコマンドを送信するアプリを実行する][img-send-command]
   
   > [!NOTE]
   > わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードでは、MSDN の記事「 [Transient Fault Handling (一時的な障害の処理)]」で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。
   > 
   > 

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、クラウドからデバイスへのメッセージを送受信する方法を学習しました。 

IoT Hub を使用する完全なエンド ツー エンド ソリューションの例については、 [Azure IoT Suite]に関するドキュメントをご覧ください。

IoT Hub を使用したソリューションの開発に関する詳細については、[IoT Hub 開発者ガイド]をご覧ください。

<!-- Images -->
[img-simulated-device]: media/iot-hub-node-node-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-node-node-c2d/sendc2d.png

<!-- Links -->

[IoT Hub の概要]: iot-hub-node-node-getstarted.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[IoT Hub 開発者ガイド]: iot-hub-devguide.md
[Azure IoT デベロッパー センター]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[Transient Fault Handling (一時的な障害の処理)]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure Portal]: https://portal.azure.com
[Azure IoT Suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

