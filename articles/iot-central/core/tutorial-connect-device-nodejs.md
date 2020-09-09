---
title: チュートリアル - 汎用の Node.js クライアント アプリを Azure IoT Central に接続する | Microsoft Docs
description: このチュートリアルでは、デバイス開発者を対象に、Node.js クライアント アプリが実行されているデバイスを Azure IoT Central アプリケーションに接続する方法を説明します。 デバイス機能モデルをインポートしてデバイス テンプレートを作成し、接続されているデバイスを対話的に操作できるビューを追加します。
author: dominicbetts
ms.author: dobett
ms.date: 07/07/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mqtt, devx-track-javascript
ms.openlocfilehash: 92b28b89d181de368ef1e39eb695be2e1fad6c37
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87423524"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-nodejs"></a>チュートリアル:クライアント アプリケーションを作成して Azure IoT Central アプリケーションに接続する (Node.js)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

"*この記事は、ソリューション ビルダーおよびデバイス開発者を対象としています。* "

このチュートリアルでは、デバイス開発者を対象に、Node.js クライアント アプリケーションを Azure IoT Central アプリケーションに接続する方法を説明します。 Node.js アプリケーションは、環境センサー デバイスの動作をシミュレートします。 IoT Central で、サンプル "_デバイス機能モデル_" を使用して "_デバイス テンプレート_" を作成します。 デバイス テンプレートにビューを追加して、オペレーターがデバイスと対話できるようにします。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * デバイス機能モデルをインポートしてデバイス テンプレートを作成する。
> * 既定のビューとカスタム ビューをデバイス テンプレートに追加する。
> * デバイス テンプレートを発行し、Azure IoT Central アプリケーションに実デバイスを追加する。
> * Node.js デバイス コードを作成して実行し、IoT Central アプリケーションへの接続を確認する。
> * デバイスから送信されるシミュレートされたテレメトリを表示する。
> * ビューを使用してデバイス プロパティを管理する。
> * 同期と非同期のコマンドを呼び出してデバイスを制御する。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

* **カスタム アプリケーション** テンプレートを使用して作成された Azure IoT Central アプリケーション。 詳細については、[アプリケーションの作成のクイック スタート](quick-deploy-iot-central.md)に関するページをご覧ください。 アプリケーションは、2020 年 7 月 14 日以降に作成されている必要があります。
* [Node.js](https://nodejs.org/) バージョン 10.0.0 以降がインストールされた開発用マシン。 バージョンを確認するには、コマンド ラインで `node --version` を実行できます。 このチュートリアルの手順は、Windows コマンド プロンプトから **node** コマンドを実行することを前提としています。 ただし、他の多くのオペレーティング システムで Node.js を使用できます。

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-nodejs-application"></a>Node.js アプリケーションの作成

以降の手順では、アプリケーションに追加した実デバイスに接続する Node.js クライアント アプリケーションの作成方法を説明します。 この Node.js アプリケーションは、実デバイスの動作をシミュレートします。

1. コマンドライン環境で、先ほど作成した `environmental-sensor` フォルダーに移動します。

1. Node.js プロジェクトを初期化し、必要な依存関係をインストールするために、次のコマンドを実行します。`npm init` を実行する際は、既定のオプションをすべてそのまま使用してください。

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. `environmental-sensor` フォルダーに、**environmentalSensor.js** という名前のファイルを作成します。

1. **environmentalSensor.js** ファイルの先頭に、次の `require` ステートメントを追加します。

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var SymmetricKeySecurityClient = require('azure-iot-security-symmetric-key').SymmetricKeySecurityClient;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

1. このファイルに次の変数宣言を追加します。

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var idScope = '{your Scope ID}';
    var registrationId = '{your Device ID}';
    var symmetricKey = '{your Primary Key}';
    var provisioningSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;

    var targetTemperature = 0;
    var ledOn = true;
    ```

    `{your Scope ID}`、`{your Device ID}`、`{your Primary Key}` の各プレースホルダーを、前に書き留めた値で更新します。 このサンプルでは、`targetTemperature` を 0 に初期化します。デバイスから現在の読み取りを取得したり、デバイス ツインから値を取得したりできます。

1. シミュレートされたテレメトリを Azure IoT Central アプリケーションに送信するために、次の関数をファイルに追加します。

    ```javascript
    // Send simulated device telemetry.
    function sendTelemetry() {
      var temp = targetTemperature + (Math.random() * 15);
      var humid = 70 + (Math.random() * 10);
      var data = JSON.stringify({
        temp: temp,
        humid: humid,
        });
      var message = new Message(data);
      hubClient.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

    テレメトリ項目の名前 (`temp` と `humid`) は、デバイス テンプレートで使用されている名前と一致している必要があります。

1. デバイス ツインのプロパティを Azure IoT Central アプリケーションに送信するには、ファイルに次の関数を追加します。

    ```javascript
    // Send device twin reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

    IoT Central では、デバイス ツインを使用して、デバイスと IoT Central アプリケーションの間でプロパティ値を同期します。 デバイスのプロパティ値では、デバイス ツインによってレポートされるプロパティが使用されます。 書き込み可能なプロパティでは、デバイス ツインによってレポートされるプロパティと必要とされるプロパティの両方が使用されます。

1. デバイス側で対応する書き込み可能なプロパティを定義して処理するために、次のコードを追加します。 [書き込み可能なプロパティの更新](concepts-telemetry-properties-commands.md#writeable-property-types)に応答してデバイスから送信されるメッセージには、`av` フィールドと `ac` フィールドが含まれている必要があります。 `ad` フィールドは省略可能です。

    ```javascript
    // Add any writeable properties your device supports,
    // mapped to a function that's called when the writeable property
    // is updated in the IoT Central application.
    var writeableProperties = {
      'name': (newValue, callback) => {
          setTimeout(() => {
            callback(newValue, 'completed', 200);
          }, 1000);
      },
      'brightness': (newValue, callback) => {
        setTimeout(() => {
            callback(newValue, 'completed', 200);
        }, 5000);
      }
    };

    // Handle writeable property updates that come from IoT Central via the device twin.
    function handleWriteablePropertyUpdates(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (writeableProperties[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting]}`);
            writeableProperties[setting](desiredChange[setting], (newValue, status, code) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  ad: status,
                  ac: code,
                  av: desiredChange.$version
                }
              }
              sendDeviceProperties(twin, patch);
            });
          }
        }
      });
    }
    ```

    オペレーターが IoT Central アプリケーションで書き込み可能なプロパティを設定すると、アプリケーションではデバイス ツインによって必要とされるプロパティを使用して、デバイスに値を送信します。 次に、デバイスによってデバイス ツインの reported プロパティが使用され、応答が行われます。 IoT Central は、reported プロパティの値を受け取ると、**同期済み**の状態でプロパティ ビューを更新します。

    プロパティの名前 (`name` と `brightness`) は、デバイス テンプレートで使用されている名前と一致している必要があります。

1. IoT Central アプリケーションから送信されたコマンドを処理するために次のコードを追加します。

    ```javascript
    // Setup command handlers
    function setupCommandHandlers(twin) {

      // Handle synchronous LED blink command with request and response payload.
      function onBlink(request, response) {
        console.log('Received synchronous call to blink');
        var responsePayload = {
          status: 'Blinking LED every ' + request.payload  + ' seconds'
        }
        response.send(200, responsePayload, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          } else {
            console.log('Blinking LED every ' + request.payload  + ' seconds');
          }
        });
      }

      // Handle synchronous LED turn on command
      function turnOn(request, response) {
        console.log('Received synchronous call to turn on LED');
        if(!ledOn){
          console.log('Turning on the LED');
          ledOn = true;
        }
        response.send(200, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          }
        });
      }

      // Handle synchronous LED turn off command
      function turnOff(request, response) {
        console.log('Received synchronous call to turn off LED');
        if(ledOn){
          console.log('Turning off the LED');
          ledOn = false;
        }
        response.send(200, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          }
        });
      }

      // Handle asynchronous sensor diagnostics command with response payload.
      function diagnostics(request, response) {
        console.log('Starting asynchronous diagnostics run...');
        response.send(202, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          } else {
            var repetitions = 3;
            var intervalID = setInterval(() => {
              console.log('Generating diagnostics...');
              if (--repetitions === 0) {
                clearInterval(intervalID);
                var properties = {
                  rundiagnostics: {
                    value: 'Diagnostics run complete at ' + new Date().toLocaleString()
                  }
                };
                sendDeviceProperties(twin, properties);
              }
            }, 2000);
          }
        });
      }

      hubClient.onDeviceMethod('blink', onBlink);
      hubClient.onDeviceMethod('turnon', turnOn);
      hubClient.onDeviceMethod('turnoff', turnOff);
      hubClient.onDeviceMethod('rundiagnostics', diagnostics);
    }
    ```

    コマンドの名前 (`blink`、`turnon`、`turnoff`、`rundiagnostics`) は、デバイス テンプレートで使用されている名前と一致している必要があります。

    現在、IoT Central では、デバイス機能モデルで定義されている応答スキーマを使用しません。 同期コマンドの場合、応答ペイロードは任意の有効な JSON にすることができます。 非同期コマンドの場合、デバイスはすぐに 202 応答を返し、その後、作業が完了したときに reported プロパティの更新を返します。 reported プロパティの更新の形式は次のとおりです。

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    オペレーターは、コマンド履歴で応答ペイロードを表示できます。

1. Azure IoT Central への接続を完了し、クライアント コードの関数をフックする次のコードを追加します。

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Send telemetry to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            var properties = {
              state: 'true',
              processorArchitecture: 'ARM',
              swVersion: '1.0.0'
            };
            sendDeviceProperties(twin, properties);

            handleWriteablePropertyUpdates(twin);

            setupCommandHandlers(twin);
          }
        });
      }
    };

    // Start the device (register and connect to Azure IoT Central).
    provisioningClient.register((err, result) => {
      if (err) {
        console.log('Error registering device: ' + err);
      } else {
        console.log('Registration succeeded');
        console.log('Assigned hub=' + result.assignedHub);
        console.log('DeviceId=' + result.deviceId);
        var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
        hubClient = Client.fromConnectionString(connectionString, iotHubTransport);

        hubClient.open(connectCallback);
      }
    });
    ```

## <a name="run-your-nodejs-application"></a>Node.js アプリケーションを実行する

デバイス クライアント アプリケーションを起動するには、コマンドライン環境で次のコマンドを実行します。

```cmd/sh
node environmentalSensor.js
```

デバイスが Azure IoT Central アプリケーションに接続して、テレメトリを送信し始めたことがわかります。

![クライアント アプリケーションを実行する](media/tutorial-connect-device-nodejs/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

コマンドとプロパティの更新に対してデバイスがどのように応答するかを確認できます。

![クライアント アプリケーションを観察する](media/tutorial-connect-device-nodejs/run-application-2.png)

## <a name="view-raw-data"></a>生データを表示する

[!INCLUDE [iot-central-monitor-environmental-sensor-raw-data](../../../includes/iot-central-monitor-environmental-sensor-raw-data.md)]

## <a name="next-steps"></a>次のステップ

デバイス開発者として、Node.js を使用してデバイスを作成する方法の基本を学習しました。推奨される次の手順は以下のとおりです。

* デバイス コードを実装する際のデバイス テンプレートの役割について詳しくは、「[デバイス テンプレートとは](./concepts-device-templates.md)」をご覧ください。
* IoT Central にデバイスを登録する方法と IoT Central でデバイスの接続を保護する方法の詳細については、「[Azure IoT Central に接続する](./concepts-get-connected.md)」を参照してください。

一連の IoT Central チュートリアルを続行し、IoT Central ソリューションの構築方法の詳細を確認するには、次のページを参照してください。

> [!div class="nextstepaction"]
> [ゲートウェイ デバイス テンプレートを作成する](./tutorial-define-gateway-device-type.md)
