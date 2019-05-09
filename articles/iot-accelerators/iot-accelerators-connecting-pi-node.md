---
title: Node.js でリモート監視するために Raspberry Pi をプロビジョニング - Azure | Microsoft Docs
description: Node.js で記述されたアプリケーションを使用して、Raspberry Pi デバイスをリモート監視ソリューション アクセラレータに接続する方法について説明します。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 01/24/2018
ms.author: dobett
ms.openlocfilehash: 20d50ac4ac4a1919077ebe67bb529e2dc5abf187
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58109748"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-nodejs"></a>Raspberry Pi デバイスをリモート監視ソリューション アクセラレータに接続する (Node.js)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

このチュートリアルでは、実デバイスをリモート監視ソリューション アクセラレータに接続する方法を示します。 このチュートリアルでは、リソースの制約が少ない環境に適したオプションである Node.js を使用します。

デバイスをシミュレートする場合は、「[新しいシミュレートされたデバイスの作成とテスト](iot-accelerators-remote-monitoring-create-simulated-device.md)」を参照してください。

### <a name="required-hardware"></a>必要なハードウェア

Raspberry Pi でコマンド ラインにリモート接続するためのデスクトップ コンピューター。

[Raspberry Pi 3 用 Microsoft IoT スタート キット](https://azure.microsoft.com/develop/iot/starter-kits/)または同等のコンポーネント。 このチュートリアルでは、キット内の次のものを使用します。

- Raspberry Pi 3
- microSD カード (NOOBS をインストール済み)
- USB ミニ ケーブル
- イーサネット ケーブル

### <a name="required-desktop-software"></a>必要なデスクトップ ソフトウェア

Raspberry Pi でコマンド ラインにリモートでアクセスするための SSH クライアントがデスクトップ コンピューターに必要です。

- Windows には SSH クライアントは含まれていません。 [PuTTY](https://www.putty.org/) を使用することをお勧めします。
- ほとんどの Linux ディストリビューションと Mac OS には、コマンド ライン SSH ユーティリティが含まれています。 詳細については、「[SSH Using Linux or Mac OS (Linux または Mac OS を使用した SSH 接続)](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md)」を参照してください。

### <a name="required-raspberry-pi-software"></a>必要な Raspberry Pi ソフトウェア

まだインストールしていない場合は、Raspberry Pi に Node.js 4.0.0 以上をインストールします。 次の手順では、Node.js v6 を Raspberry Pi にインストールする方法を示します。

1. `ssh` を使用して Raspberry Pi に接続します。 詳細については、[Raspberry Pi の Web サイト](https://www.raspberrypi.org/)の [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) のセクションを参照してください。

1. 次のコマンドを使用して Raspberry Pi を更新します。

    ```sh
    sudo apt-get update
    ```

1. 次のコマンドを使用して、既存の Node.js のインストールを Raspberry Pi から削除します。

    ```sh
    sudo apt-get remove nodered -y
    sudo apt-get remove nodejs nodejs-legacy -y
    sudo apt-get remove npm  -y
    ```

1. 次のコマンドを使用して、Node.js v6 を Raspberry Pi にダウンロードしてインストールします。

    ```sh
    curl -sL https://deb.nodesource.com/setup_6.x | sudo bash -
    sudo apt-get install nodejs npm
    ```

1. 次のコマンドを使用して、Node.js v6.11.4 が正常にインストールされたことを確認します。

    ```sh
    node --version
    ```

## <a name="create-a-nodejs-solution"></a>Node.js ソリューションを作成する

Raspberry Pi への `ssh` 接続を使用して、次の手順を実行します。

1. `remotemonitoring` という名前のフォルダーを Raspberry Pi のホーム フォルダーで作成します。 コマンド ラインでこのフォルダーに移動します。

    ```sh
    cd ~
    mkdir remotemonitoring
    cd remotemonitoring
    ```

1. サンプル アプリケーションを完成させるために必要なパッケージをダウンロードしてインストールするには、次のコマンドを実行します。

    ```sh
    npm install async azure-iot-device azure-iot-device-mqtt
    ```

1. `remotemonitoring` フォルダーで、**remote_monitoring.js** という名前のファイルを作成します。 このファイルをテキスト エディターで開きます。 Raspberry Pi では、`nano` または `vi` テキスト エディターを使用できます。

1. **remote_monitoring.js** ファイルに次の `require` ステートメントを追加します。

    ```javascript
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var async = require('async');
    ```

1. `require` ステートメントの後に次の変数宣言を追加します。 プレースホルダー値 `{device connection string}` を、リモート監視ソリューションでプロビジョニングしたデバイス用の値に置き換えます。

    ```javascript
    var connectionString = '{device connection string}';
    ```

1. 基本のテレメトリをいくつか定義するために、次の変数を追加します。

    ```javascript
    var temperature = 50;
    var temperatureUnit = 'F';
    var humidity = 50;
    var humidityUnit = '%';
    var pressure = 55;
    var pressureUnit = 'psig';
    ```

1. プロパティ値を定義するには、次の変数を追加します。

    ```javascript
    var schema = "real-chiller;v1";
    var deviceType = "RealChiller";
    var deviceFirmware = "1.0.0";
    var deviceFirmwareUpdateStatus = "";
    var deviceLocation = "Building 44";
    var deviceLatitude = 47.638928;
    var deviceLongitude = -122.13476;
    var deviceOnline = true;
    ```

1. ソリューションに送信する、報告対象プロパティを定義するには、次の変数を追加します。 これらのプロパティには、Web UI に表示するメタデータが含まれます。

    ```javascript
    var reportedProperties = {
      "SupportedMethods": "Reboot,FirmwareUpdate,EmergencyValveRelease,IncreasePressure",
      "Telemetry": {
        [schema]: ""
      },
      "Type": deviceType,
      "Firmware": deviceFirmware,
      "FirmwareUpdateStatus": deviceFirmwareUpdateStatus,
      "Location": deviceLocation,
      "Latitude": deviceLatitude,
      "Longitude": deviceLongitude,
      "Online": deviceOnline
    }
    ```

1. 操作結果を出力するには、次のヘルパー関数を追加します。

    ```javascript
    function printErrorFor(op) {
        return function printError(err) {
            if (err) console.log(op + ' error: ' + err.toString());
        };
    }
    ```

1. テレメトリの値をランダム化するために使用する次のヘルパー関数を追加します。

     ```javascript
     function generateRandomIncrement() {
         return ((Math.random() * 2) - 1);
     }
     ```

1. ソリューションからのダイレクト メソッドの呼び出しを処理するため、次の汎用関数を追加します。 この関数は、呼び出されたダイレクト メソッドに関する情報を表示しますが、このサンプルではデバイスを変更しません。 ソリューションは、次のようにデバイスで動作するダイレクト メソッドを使用します。

     ```javascript
     function onDirectMethod(request, response) {
       // Implement logic asynchronously here.
       console.log('Simulated ' + request.methodName);

       // Complete the response
       response.send(200, request.methodName + ' was called on the device', function (err) {
         if (err) console.error('Error sending method response :\n' + err.toString());
         else console.log('200 Response to method \'' + request.methodName + '\' sent successfully.');
       });
     }
     ```

1. ソリューションからの **FirmwareUpdate** ダイレクト メソッドの呼び出しを処理するために、次の関数を追加します。 この関数は、ダイレクト メソッドのペイロードで渡されたパラメーターを検証し、ファームウェア更新シミュレーションを非同期に実行します。

     ```javascript
     function onFirmwareUpdate(request, response) {
       // Get the requested firmware version from the JSON request body
       var firmwareVersion = request.payload.Firmware;
       var firmwareUri = request.payload.FirmwareUri;
      
       // Ensure we got a firmware values
       if (!firmwareVersion || !firmwareUri) {
         response.send(400, 'Missing firmware value', function(err) {
           if (err) console.error('Error sending method response :\n' + err.toString());
           else console.log('400 Response to method \'' + request.methodName + '\' sent successfully.');
         });
       } else {
         // Respond the cloud app for the device method
         response.send(200, 'Firmware update started.', function(err) {
           if (err) console.error('Error sending method response :\n' + err.toString());
           else {
             console.log('200 Response to method \'' + request.methodName + '\' sent successfully.');

             // Run the simulated firmware update flow
             runFirmwareUpdateFlow(firmwareVersion, firmwareUri);
           }
         });
       }
     }
     ```

1. ソリューションに進行状況を報告する実行時間の長いファームウェア更新フローをシミュレートするために、次の関数を追加します。

     ```javascript
     // Simulated firmwareUpdate flow
     function runFirmwareUpdateFlow(firmwareVersion, firmwareUri) {
       console.log('Simulating firmware update flow...');
       console.log('> Firmware version passed: ' + firmwareVersion);
       console.log('> Firmware URI passed: ' + firmwareUri);
       async.waterfall([
         function (callback) {
           console.log("Image downloading from " + firmwareUri);
           var patch = {
             FirmwareUpdateStatus: 'Downloading image..'
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(10000, callback);
         },
         function (callback) {
           console.log("Downloaded, applying firmware " + firmwareVersion);
           deviceOnline = false;
           var patch = {
             FirmwareUpdateStatus: 'Applying firmware..',
             Online: false
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(8000, callback);
         },
         function (callback) {
           console.log("Rebooting");
           var patch = {
             FirmwareUpdateStatus: 'Rebooting..'
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(10000, callback);
         },
         function (callback) {
           console.log("Firmware updated to " + firmwareVersion);
           deviceOnline = true;
           var patch = {
             FirmwareUpdateStatus: 'Firmware updated',
             Online: true,
             Firmware: firmwareVersion
           };
           reportUpdateThroughTwin(patch, callback);
           callback(null);
         }
       ], function(err) {
         if (err) {
           console.error('Error in simulated firmware update flow: ' + err.message);
         } else {
           console.log("Completed simulated firmware update flow");
         }
       });

       // Helper function to update the twin reported properties.
       function reportUpdateThroughTwin(patch, callback) {
         console.log("Sending...");
         console.log(JSON.stringify(patch, null, 2));
         client.getTwin(function(err, twin) {
           if (!err) {
             twin.properties.reported.update(patch, function(err) {
               if (err) callback(err);
             });      
           } else {
             if (err) callback(err);
           }
         });
       }

       function sleep(milliseconds, callback) {
         console.log("Simulate a delay (milleseconds): " + milliseconds);
         setTimeout(function () {
           callback(null);
         }, milliseconds);
       }
     }
     ```

1. ソリューションにテレメトリを送信する次のコードを追加します。 クライアント アプリでは、次のようにメッセージ スキーマを識別するためのプロパティをメッセージに追加します。

     ```javascript
     function sendTelemetry(data, schema) {
       if (deviceOnline) {
         var d = new Date();
         var payload = JSON.stringify(data);
         var message = new Message(payload);
         message.properties.add('iothub-creation-time-utc', d.toISOString());
         message.properties.add('iothub-message-schema', schema);

         console.log('Sending device message data:\n' + payload);
         client.sendEvent(message, printErrorFor('send event'));
       } else {
         console.log('Offline, not sending telemetry');
       }
     }
     ```

1. クライアント インスタンスを作成するために次のコードを追加します。

     ```javascript
     var client = Client.fromConnectionString(connectionString, Protocol);
     ```

1. 次の処理を行うために、以下のコードを追加します。

    * 接続を開きます。
    * 必要なプロパティのハンドラーを設定します。
    * 報告されたプロパティを送信します。
    * ダイレクト メソッドのハンドラーを登録します。 このサンプルでは、ファームウェア更新ダイレクト メソッドに別のハンドラーを使用しています。
    * テレメトリの送信を開始します。

      ```javascript
      client.open(function (err) {
      if (err) {
        printErrorFor('open')(err);
      } else {
        // Create device Twin
        client.getTwin(function (err, twin) {
          if (err) {
            console.error('Could not get device twin');
          } else {
            console.log('Device twin created');

            twin.on('properties.desired', function (delta) {
              // Handle desired properties set by solution
              console.log('Received new desired properties:');
              console.log(JSON.stringify(delta));
            });

            // Send reported properties
            twin.properties.reported.update(reportedProperties, function (err) {
              if (err) throw err;
              console.log('Twin state reported');
            });

            // Register handlers for all the method names we are interested in.
            // Consider separate handlers for each method.
            client.onDeviceMethod('Reboot', onDirectMethod);
            client.onDeviceMethod('FirmwareUpdate', onFirmwareUpdate);
            client.onDeviceMethod('EmergencyValveRelease', onDirectMethod);
            client.onDeviceMethod('IncreasePressure', onDirectMethod);
          }
        });

        // Start sending telemetry
        var sendDeviceTelemetry = setInterval(function () {
          temperature += generateRandomIncrement();
          pressure += generateRandomIncrement();
          humidity += generateRandomIncrement();
          var data = {
            'temperature': temperature,
            'temperature_unit': temperatureUnit,
            'humidity': humidity,
            'humidity_unit': humidityUnit,
            'pressure': pressure,
            'pressure_unit': pressureUnit
          };
          sendTelemetry(data, schema)
        }, 5000);

        client.on('error', function (err) {
          printErrorFor('client')(err);
          if (sendTemperatureInterval) clearInterval(sendTemperatureInterval);
          if (sendHumidityInterval) clearInterval(sendHumidityInterval);
          if (sendPressureInterval) clearInterval(sendPressureInterval);
          client.close(printErrorFor('client.close'));
        });
      }
      });
      ```

1. 変更を **remote_monitoring.js** ファイルに保存します。

1. Raspberry Pi 上のコマンド プロンプトで次のコマンドを実行して、サンプル アプリケーションを起動します。

     ```sh
     node remote_monitoring.js
     ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
