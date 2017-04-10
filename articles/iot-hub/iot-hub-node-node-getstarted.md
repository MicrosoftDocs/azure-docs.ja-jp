---
title: "Azure IoT Hub の使用 (Node) | Microsoft Docs"
description: "Azure IoT SDK for Node.js を使用して Azure IoT Hub デバイスでデバイスからクラウドへのメッセージを送信する方法。 メッセージを送信するシミュレーション対象デバイス アプリ、ID レジストリにデバイスを登録するサービス アプリ、およびデバイスからクラウドへのメッセージを IoT Hub から読み取るサービス アプリを作成します。"
services: iot-hub
documentationcenter: nodejs
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 56618522-9a31-42c6-94bf-55e2233b39ac
ms.service: iot-hub
ms.devlang: javascript
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/16/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: e5cfa4c0a6964c3722ecaa6727336f5497565885
ms.lasthandoff: 03/17/2017


---
# <a name="connect-your-simulated-device-to-your-iot-hub-using-node"></a>Node を使用してシミュレーション対象デバイスを IoT Hub に接続する
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

このチュートリアルの最後には、次の&3; つの Node.js コンソール アプリが完成します。

* **CreateDeviceIdentity.js**。デバイス ID と関連付けられているセキュリティ キーを作成し、シミュレーション対象デバイス アプリを接続します。
* **ReadDeviceToCloudMessages.js**。シミュレーション対象デバイス アプリから送信されたテレメトリを表示します。
* **SimulatedDevice.js**。以前に作成したデバイス ID で IoT ハブに接続し、MQTT プロトコルを使用して&1; 秒ごとにテレメトリ メッセージを送信します。

> [!NOTE]
> デバイス上で動作するアプリケーションの作成とソリューションのバックエンドで動作するアプリケーションの開発に利用できる各種 Azure IoT SDK については、[Azure IoT SDK][lnk-hub-sdks] に関する記事を参照してください。
> 
> 

このチュートリアルを完了するには、以下が必要です。

* Node.js バージョン 0.10.x 以降。
* アクティブな Azure アカウント。 (アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成できます)。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

IoT Hub の作成は以上です。 以降の作業に必要な IoT Hub ホスト名と IoT Hub 接続文字列が得られました。

## <a name="create-a-device-identity"></a>デバイス ID の作成
このセクションでは、IoT ハブの ID レジストリにデバイス ID を作成する Node.js コンソール アプリケーションを作成します。 デバイスは、ID レジストリに登録されている場合のみ、IoT ハブに接続することができます。 詳細については、[IoT Hub 開発者ガイド][lnk-devguide-identity]の **ID レジストリ**に関するセクションをご覧ください。 このコンソール アプリケーションを実行すると、デバイスからクラウドへのメッセージを IoT Hub に送信するときにそのデバイスを識別する一意の ID とキーが生成されます。

1. **createdeviceidentity**という名前の新しい空のフォルダーを作成します。 コマンド プロンプトで次のコマンドを使用して、**createdeviceidentity** フォルダー内に package.json ファイルを作成します。 次の既定値をすべてそのまま使用します。
   
    ```
    npm init
    ```
2. コマンド プロンプトで、**createdeviceidentity** フォルダーに移動し、次のコマンドを実行して **azure-iothub** Service SDK パッケージをインストールします。
   
    ```
    npm install azure-iothub --save
    ```
3. テキスト エディターを使用して、**createdeviceidentity** フォルダー内に **CreateDeviceIdentity.js** ファイルを作成します。
4. **CreateDeviceIdentity.js** ファイルの先頭に、次の `require` ステートメントを追加します。
   
    ```
    'use strict';
   
    var iothub = require('azure-iothub');
    ```
5. **CreateDeviceIdentity.js** ファイルに次のコードを追加し、プレースホルダーの値を、前のセクションで作成したハブの IoT Hub 接続文字列に置き換えます。 
   
    ```
    var connectionString = '{iothub connection string}';
   
    var registry = iothub.Registry.fromConnectionString(connectionString);
    ```
6. 次のコードを追加して、IoT Hub で ID レジストリにデバイスの定義を作成します。 このコードは、デバイス ID が ID レジストリに存在しない場合にはデバイスを作成し、存在している場合には既存のデバイスのキーを返します。
   
    ```
    var device = new iothub.Device(null);
    device.deviceId = 'myFirstNodeDevice';
    registry.create(device, function(err, deviceInfo, res) {
      if (err) {
        registry.get(device.deviceId, printDeviceInfo);
      }
      if (deviceInfo) {
        printDeviceInfo(err, deviceInfo, res)
      }
    });
   
    function printDeviceInfo(err, deviceInfo, res) {
      if (deviceInfo) {
        console.log('Device ID: ' + deviceInfo.deviceId);
        console.log('Device key: ' + deviceInfo.authentication.symmetricKey.primaryKey);
      }
    }
    ```
7. **CreateDeviceIdentity.js** ファイルを保存して閉じます。
8. **createdeviceidentity** アプリケーションを実行するには、コマンド プロンプトで createdeviceidentity フォルダーに移動し、次のコマンドを実行します。
   
    ```
    node CreateDeviceIdentity.js 
    ```
9. **デバイス ID** と**デバイス キー**をメモします。 これらの値は、後でデバイスとして IoT Hub に接続するアプリケーションを作成するときに必要になります。

> [!NOTE]
> IoT Hub の ID レジストリには、IoT ハブに対するセキュリティで保護されたアクセスを有効にするためのデバイス ID のみが格納されます。 セキュリティ資格情報として使用するキーとデバイス ID、そして個々のデバイスについてアクセスを無効にすることのできる有効/無効フラグが格納されます。 その他デバイス固有のメタデータをアプリケーションで保存する必要がある場合は、アプリケーション固有のストアを使用する必要があります。 詳細については、[IoT Hub 開発者ガイド][lnk-devguide-identity]をご覧ください。
> 
> 

## <a name="receive-device-to-cloud-messages"></a>デバイスからクラウドへのメッセージの受信
このセクションでは、デバイスからクラウドへのメッセージを IoT Hub から読み込む Node.js コンソール アプリケーションを作成します。 IoT Hub は、デバイスからクラウドへのメッセージを読み取るための、[Event Hubs][lnk-event-hubs-overview] と互換性のあるエンドポイントを公開します。 わかりやすくするために、このチュートリアルで作成するリーダーは基本的なものであり、高スループットのデプロイメントには適していません。 [デバイスからクラウドへのメッセージの処理][lnk-process-d2c-tutorial]に関するチュートリアルでは、デバイスからクラウドへのメッセージを大規模に処理する方法を紹介しています。 「[Event Hubs の使用][lnk-eventhubs-tutorial]」チュートリアルでは、Event Hubs からのメッセージを処理する方法について詳しく説明しています。また、このチュートリアルは IoT Hub のイベント ハブと互換性のあるエンドポイントに当てはまります。

> [!NOTE]
> Event Hub 対応エンドポイントは、常に、デバイスからクラウドへのメッセージを読み取るために AMQP プロトコルを使用します。
> 
> 

1. **readdevicetocloudmessages** という名前の空のフォルダーを作成します。 コマンド プロンプトで次のコマンドを使用して、**readdevicetocloudmessages** フォルダー内に package.json ファイルを作成します。 次の既定値をすべてそのまま使用します。
   
    ```
    npm init
    ```
2. コマンド プロンプトで、**readdevicetocloudmessages** フォルダーに移動し、次のコマンドを実行して **azure-event-hubs** パッケージをインストールします。
   
    ```
    npm install azure-event-hubs --save
    ```
3. テキスト エディターを使用して、**readdevicetocloudmessages** フォルダー内に **ReadDeviceToCloudMessages.js** ファイルを作成します。
4. **ReadDeviceToCloudMessages.js** ファイルの先頭に、次の `require` ステートメントを追加します。
   
    ```
    'use strict';
   
    var EventHubClient = require('azure-event-hubs').Client;
    ```
5. 次の変数宣言を追加し、プレースホルダーの値をハブの IoT Hub 接続文字列に置き換えます。
   
    ```
    var connectionString = '{iothub connection string}';
    ```
6. コンソールに出力する次の&2; つの関数を追加します。
   
    ```
    var printError = function (err) {
      console.log(err.message);
    };
   
    var printMessage = function (message) {
      console.log('Message received: ');
      console.log(JSON.stringify(message.body));
      console.log('');
    };
    ```
7. **EventHubClient** を作成し、IoT ハブへの接続を開き、各パーティションのレシーバーを作成する次のコードを追加します。 受信側が実行開始後、IoT Hub に送信されたメッセージのみを読み込むように、このアプリケーション メソッドは受信側の構築時にフィルターを使用します。 現在のメッセージのセットのみが表示されるため、このフィルターはテスト環境で役に立ちます。 運用環境では、すべてのメッセージがコードによって処理されるようにする必要があります。 詳細については、[IoT Hub のデバイスからクラウドへのメッセージを処理する方法][lnk-process-d2c-tutorial]に関するチュートリアルを参照してください。
   
    ```
    var client = EventHubClient.fromConnectionString(connectionString);
    client.open()
        .then(client.getPartitionIds.bind(client))
        .then(function (partitionIds) {
            return partitionIds.map(function (partitionId) {
                return client.createReceiver('$Default', partitionId, { 'startAfterTime' : Date.now()}).then(function(receiver) {
                    console.log('Created partition receiver: ' + partitionId)
                    receiver.on('errorReceived', printError);
                    receiver.on('message', printMessage);
                });
            });
        })
        .catch(printError);
    ```
8. **ReadDeviceToCloudMessages.js** ファイルを保存して閉じます。

## <a name="create-a-simulated-device-app"></a>シミュレーション対象デバイス アプリの作成
このセクションでは、デバイスからクラウドへのメッセージを IoT ハブに送信するデバイスをシミュレートする Node.js コンソール アプリを作成します。

1. **simulateddevice** という名前の空のフォルダーを作成します。 コマンド プロンプトで次のコマンドを使用して、**simulateddevice** フォルダー内に新しい package.json ファイルを作成します。 次の既定値をすべてそのまま使用します。
   
    ```
    npm init
    ```
2. コマンド プロンプトで、**simulateddevice** フォルダーに移動し、次のコマンドを実行して、**azure-iot-device** Device SDK パッケージと **azure-iot-device-mqtt** パッケージをインストールします。
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. テキスト エディターを使用して、**simulateddevice** フォルダーに **SimulatedDevice.js** ファイルを作成します。
4. **SimulatedDevice.js** ファイルの先頭に、次の `require` ステートメントを追加します。
   
    ```
    'use strict';
   
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```
5. **connectionString** 変数を追加し、それを使用して **Client** インスタンスを作成します。 「 **IoT Hub の作成** 」で作成した IoT ハブの名前で *{youriothostname}* を置き換えます。 「 **デバイス ID の作成** 」で生成したデバイス キーの値で *{yourdevicekey}* を置き換えます。
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myFirstNodeDevice;SharedAccessKey={yourdevicekey}';
   
    var client = clientFromConnectionString(connectionString);
    ```
6. 次の関数を追加してアプリケーションからの出力を表示します。
   
    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```
7. コールバックを作成し、**setInterval** 関数を使用して&1; 秒ごとにメッセージを IoT ハブに送信します。
   
    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
   
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
8. IoT Hub への接続を開き、メッセージの送信を開始します。
   
    ```
    client.open(connectCallback);
    ```
9. **SimulatedDevice.js** ファイルを保存して閉じます。

> [!NOTE]
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードでは、[一時的な障害処理][lnk-transient-faults]に関する MSDN の記事で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。
> 
> 

## <a name="run-the-apps"></a>アプリの実行
これで、アプリを実行する準備が整いました。

1. コマンド プロンプトで、**readdevicetocloudmessages** フォルダーに移動し、次のコマンドを実行して IoT Hub の監視を開始します。
   
    ```
    node ReadDeviceToCloudMessages.js 
    ```
   
    ![デバイスからクラウドへのメッセージを監視するための Node.js IoT Hub サービス アプリ][7]
2. コマンド プロンプトで、**simulateddevice** フォルダーに移動し、次のコマンドを実行して IoT Hub へのテレメトリ データの送信を開始します。
   
    ```
    node SimulatedDevice.js
    ```
   
    ![デバイスからクラウドへのメッセージを送信するための Node.js IoT Hub デバイス アプリ][8]
3. [Azure Portal][lnk-portal] の **[使用状況]** タイルには、IoT Hub に送信されたメッセージ数が表示されます。
   
    ![Azure portal Usage tile showing number of messages sent to IoT Hub][43]

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Azure Portal で新しい IoT Hub を構成し、IoT Hub の ID レジストリにデバイス ID を作成しました。 シミュレーション対象デバイス アプリでデバイスからクラウドへのメッセージを IoT Hub に送信できるようにするために、このデバイス ID を使用しました。 また、IoT Hub で受け取ったメッセージを表示するアプリを作成しました。 

引き続き IoT Hub の使用方法を確認すると共に、他の IoT のシナリオについて調べるには、次のページを参照してください。

* [デバイスを接続する][lnk-connect-device]
* [デバイス管理の概要][lnk-device-management]
* [IoT Gateway SDK の概要][lnk-gateway-SDK]

既存の IoT ソリューションを拡張し、デバイスからクラウドへのメッセージを大規模に処理する方法については、[デバイスからクラウドへのメッセージの処理][lnk-process-d2c-tutorial]に関するチュートリアルを参照してください。

<!-- Images. -->
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

