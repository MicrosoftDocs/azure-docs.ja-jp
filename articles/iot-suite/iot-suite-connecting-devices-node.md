---
title: "Node.js を使用してデバイスを接続する | Microsoft Docs"
description: "Node.js で記述されたアプリケーションを使用して、デバイスを Azure IoT Suite 構成済みリモート監視ソリューションに接続する方法について説明します。"
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: fc50a33f-9fb9-42d7-b1b8-eb5cff19335e
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 144dcd5277673024c7a71808f32c6a7b6491559a
ms.openlocfilehash: 6ccea0eeb594070eed6341d3f5ec80db0efdf8f2
ms.lasthandoff: 02/27/2017


---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-nodejs"></a>デバイスをリモート監視構成済みソリューションに接続する (Node.js)
[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="create-a-nodejs-sample-solution"></a>node.js のサンプル ソリューションを作成する

開発コンピューターに Node.js のバージョン 0.10.x 以降がインストールされていることを確認します。 バージョンを確認するには、コマンド ラインで `node --version` を実行します。

1. 開発用コンピューターに **RemoteMonitoring** という名前のフォルダーを作成します。 コマンドライン環境内でこのフォルダーに移動します。

1. 次のコマンドを実行して、サンプル アプリケーションを完成させるために必要なパッケージをダウンロードしてインストールします。

    ```
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. **RemoteMonitoring** フォルダーで、**remote_monitoring.js** という名前のファイルを作成します。 このファイルをテキスト エディターで開きます。

1. **remote_monitoring.js** ファイルに次の `require` ステートメントを追加します。

    ```nodejs
    'use strict';

    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

1. `require` ステートメントの後に次の変数宣言を追加します。 リモート監視ソリューション ダッシュボードで、プレースホルダー [Device Id] と [Device Key] の値を、書き留めておいたデバイス用の値に置き換えます。 ソリューション ダッシュボードの IoT Hub ホスト名を使用して、[IoTHub Name] を置き換えます。 たとえば、IoT Hub ホスト名が **contoso.azure-devices.net** である場合は、[IoTHub Name] を **contoso** に置き換えます。

    ```nodejs
    var connectionString = 'HostName=[IoTHub Name].azure-devices.net;DeviceId=[Device Id];SharedAccessKey=[Device Key]';
    var deviceId = ConnectionString.parse(connectionString).DeviceId;
    ```

1. 基本のテレメトリ データをいくつか定義するために、次の変数を追加します。

    ```nodejs
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    ```

1. 操作結果を出力するために、次のヘルパー関数を追加します。

    ```nodejs
    function printErrorFor(op) {
        return function printError(err) {
            if (err) console.log(op + ' error: ' + err.toString());
        };
    }
    ```

1. テレメトリの値をランダム化するために使用する次のヘルパー関数を追加します。

    ```nodejs
    function generateRandomIncrement() {
        return ((Math.random() * 2) - 1);
    }
    ```

1. 起動時にデバイスから送信する **DeviceInfo** オブジェクトについて、次の定義を追加します。

    ```nodejs
    var deviceMetaData = {
        'ObjectType': 'DeviceInfo',
        'IsSimulatedDevice': 0,
        'Version': '1.0',
        'DeviceProperties': {
            'DeviceID': deviceId,
            'HubEnabledState': 1
        }
    };
    ```

1. デバイス ツインの報告される値について、次の定義を追加します。 この定義には、デバイスでサポートされるダイレクト メソッドの説明が含まれます。

    ```nodejs
    var reportedProperties = {
        "Device": {
            "DeviceState": "normal",
            "Location": {
                "Latitude": 47.642877,
                "Longitude": -122.125497
            }
        },
        "Config": {
            "TemperatureMeanValue": 56.7,
            "TelemetryInterval": 45
        },
        "System": {
            "Manufacturer": "Contoso Inc.",
            "FirmwareVersion": "2.22",
            "InstalledRAM": "8 MB",
            "ModelNumber": "DB-14",
            "Platform": "Plat 9.75",
            "Processor": "i3-9",
            "SerialNumber": "SER99"
        },
        "Location": {
            "Latitude": 47.642877,
            "Longitude": -122.125497
        },
        "SupportedMethods": {
            "Reboot": "Reboot the device",
            "InitiateFirmwareUpdate--FwPackageURI-string": "Updates device Firmware. Use parameter FwPackageURI to specifiy the URI of the firmware file"
        },
    }
    ```

1. **Reboot** ダイレクト メソッドの呼び出しを処理する次の関数を追加します。

    ```nodejs
    function onReboot(request, response) {
        // Implement actual logic here.
        console.log('Simulated reboot...');

        // Complete the response
        response.send(200, "Rebooting device", function(err) {
            if(!!err) {
                console.error('An error ocurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```

1. **InitiateFirmwareUpdate** ダイレクト メソッドの呼び出しを処理する次の関数を追加します。 このダイレクト メソッドは、パラメーターを使用して、ダウンロードするファームウェア イメージの場所を指定し、デバイスでファームウェアの更新を非同期に開始します。

    ```nodejs
    function onInitiateFirmwareUpdate(request, response) {
        console.log('Simulated firmware update initiated, using: ' + request.payload.FwPackageURI);

        // Complete the response
        response.send(200, "Firmware update initiated", function(err) {
            if(!!err) {
                console.error('An error ocurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });

        // Add logic here to perform the firmware update asynchronously
    }
    ```

1. クライアント インスタンスを作成するために次のコードを追加します。

    ```nodejs
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

1. 次の処理を行うために、以下のコードを追加します。

    * 接続を開きます。
    * **DeviceInfo** オブジェクトを送信します。
    * 必要なプロパティのハンドラーを設定します。
    * 報告されたプロパティを送信します。
    * ダイレクト メソッドのハンドラーを登録します。
    * テレメトリの送信を開始します。

    ```nodejs
    client.open(function (err) {
        if (err) {
            printErrorFor('open')(err);
        } else {
            console.log('Sending device metadata:\n' + JSON.stringify(deviceMetaData));
            client.sendEvent(new Message(JSON.stringify(deviceMetaData)), printErrorFor('send metadata'));

            // Create device twin
            client.getTwin(function(err, twin) {
                if (err) {
                    console.error('Could not get device twin');
                } else {
                    console.log('Device twin created');

                    twin.on('properties.desired', function(delta) {
                        console.log('Received new desired properties:');
                        console.log(JSON.stringify(delta));
                    });

                    // Send reported properties
                    twin.properties.reported.update(reportedProperties, function(err) {
                        if (err) throw err;
                        console.log('twin state reported');
                    });

                    // Register handlers for direct methods
                    client.onDeviceMethod('Reboot', onReboot);
                    client.onDeviceMethod('InitiateFirmwareUpdate', onInitiateFirmwareUpdate);
                }
            });

            // Start sending telemetry
            var sendInterval = setInterval(function () {
                temperature += generateRandomIncrement();
                externalTemperature += generateRandomIncrement();
                humidity += generateRandomIncrement();

                var data = JSON.stringify({
                    'DeviceID': deviceId,
                    'Temperature': temperature,
                    'Humidity': humidity,
                    'ExternalTemperature': externalTemperature
                });

                console.log('Sending device event data:\n' + data);
                client.sendEvent(new Message(data), printErrorFor('send event'));
            }, 5000);

            client.on('error', function (err) {
                printErrorFor('client')(err);
                if (sendInterval) clearInterval(sendInterval);
                client.close(printErrorFor('client.close'));
            });
        }
    });
    ```

1. 変更を **remote_monitoring.js** ファイルに保存します。

1. コマンド プロンプトで次のコマンドを実行して、サンプル アプリケーションを起動します。
   
    ```
    node remote_monitoring.js
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-github-repo]: https://github.com/azure/azure-iot-sdk-node
[lnk-github-prepare]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

