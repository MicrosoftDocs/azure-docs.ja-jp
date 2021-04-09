---
title: Azure IoT Hub デバイス管理の開始 (Node) | Microsoft Docs
description: IoT Hub デバイス管理を使用してリモート デバイスの再起動を開始する方法。 Azure IoT SDK for Node.js を使用して、ダイレクト メソッドを含むシミュレートされたデバイス アプリと、ダイレクト メソッドを呼び出すサービス アプリを実装します。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: mqtt, devx-track-js
ms.openlocfilehash: cfc0fa45c08f917b2e0b4a0b055e801173a4ba39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91252019"
---
# <a name="get-started-with-device-management-nodejs"></a>デバイス管理の開始 (Node.js)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

このチュートリアルでは、次の操作方法について説明します。

* [Azure portal](https://portal.azure.com) を使用して IoT Hub を作成し、IoT Hub 内にデバイス ID を作成します。

* デバイスを再起動するダイレクト メソッドを含む、シミュレション済みデバイス アプリを作成します。 ダイレクト メソッドは、クラウドから呼び出されます。

* シミュレートされたデバイス アプリで再起動ダイレクト メソッドを IoT ハブ経由で呼び出す Node.js コンソール アプリを作成します。

このチュートリアルの最後には、次の 2 つの Node.js コンソール アプリが完成します。

* **dmpatterns_getstarted_device.js**: IoT Hub を、作成済みのデバイス ID に接続し、再起動ダイレクト メソッドを受信し、物理的な再起動をシミュレートし、最後の再起動時間を報告します。

* **dmpatterns_getstarted_service.js**: シミュレートされたデバイス アプリでダイレクト メソッドを呼び出し、応答を表示し、更新された報告されるプロパティを表示します。

## <a name="prerequisites"></a>前提条件

* Node.js バージョン 10.0.x 以降。 「[Prepare your development environment (開発環境を準備する)](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md)」では、このチュートリアルのために Node.js を Windows または Linux にインストールする方法が説明されています。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 (アカウントがない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を数分で作成できます)。

* ポート 8883 がファイアウォールで開放されていることを確認してください。 この記事のデバイス サンプルでは、ポート 8883 を介して通信する MQTT プロトコルを使用しています。 このポートは、企業や教育用のネットワーク環境によってはブロックされている場合があります。 この問題の詳細と対処方法については、「[IoT Hub への接続 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)」を参照してください。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT ハブに新しいデバイスを登録する

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>シミュレート対象デバイス アプリの作成

このセクションでは、次の作業を行います。

* クラウドによって呼び出されたダイレクト メソッドに応答する Node.js コンソール アプリを作成します。

* シミュレート対象デバイスの再起動をトリガーします。

* 報告されるプロパティを使用して、デバイス ツイン クエリで、デバイスとデバイスの最後の再起動時間を識別できるようにします。

1. **manageddevice** という名前の空のフォルダーを作成します。  コマンド プロンプトで次のコマンドを使用して、**manageddevice** フォルダー内に新しい package.json ファイルを作成します。  次の既定値をすべてそのまま使用します。

    ```cmd/sh
    npm init
    ```

2. コマンド プロンプトで、**manageddevice** フォルダーに移動し、次のコマンドを実行して、**azure-iot-device** Device SDK パッケージと **azure-iot-device-mqtt** パッケージをインストールします。

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. テキスト エディターを使用して、**manageddevice** フォルダーに **dmpatterns_getstarted_device.js** ファイルを作成します。

4. **dmpatterns_getstarted_device.js** ファイルの先頭に、次の 'require' ステートメントを追加します。

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. **connectionString** 変数を追加し、それを使用して **Client** インスタンスを作成します。  `{yourdeviceconnectionstring}` プレースホルダーの値を、先ほど「[IoT ハブに新しいデバイスを登録する](#register-a-new-device-in-the-iot-hub)」でコピーしたデバイス接続文字列に置き換えます。  

    ```javascript
    var connectionString = '{yourdeviceconnectionstring}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. デバイスにダイレクト メソッドを実装する次の関数を追加します。

    ```javascript
    var onReboot = function(request, response) {

        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });

        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };

        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });

        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```

7. IoT Hub への接続を開き、ダイレクト メソッド リスナーを開始します。

    ```javascript
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```

8. **dmpatterns_getstarted_device.js** ファイルを保存して閉じます。

> [!NOTE]
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードでは、「[一時的な障害の処理](/azure/architecture/best-practices/transient-faults)」の記事で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。

## <a name="get-the-iot-hub-connection-string"></a>IoT ハブ接続文字列を取得する

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>ダイレクト メソッドを使用してデバイスのリモート再起動をトリガーする

このセクションでは、ダイレクト メソッドを使用してデバイスでのリモート再起動を開始する Node.js コンソール アプリケーションを作成します。 このアプリは、デバイス ツイン クエリを使用して、そのデバイスの前回の再起動時刻を検出します。

1. **triggerrebootondevice** という名前の空のフォルダーを作成します。 コマンド プロンプトで次のコマンドを使用して、**triggerrebootondevice** フォルダー内に新しい package.json ファイルを作成します。 次の既定値をすべてそのまま使用します。

    ```cmd/sh
    npm init
    ```

2. コマンド プロンプトで、**triggerrebootondevice** フォルダーに移動し、次のコマンドを実行して、**azure-iothub** Device SDK パッケージと **azure-iot-device-mqtt** パッケージをインストールします。

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. テキスト エディターを使用して、**triggerrebootondevice** フォルダー内に **dmpatterns_getstarted_service.js** ファイルを作成します。

4. **dmpatterns_getstarted_service.js** ファイルの先頭に、次の 'require' ステートメントを追加します。

    ```javascript
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. 次の変数宣言を追加し、`{iothubconnectionstring}` プレースホルダーの値を、先ほど「[IoT ハブ接続文字列を取得する](#get-the-iot-hub-connection-string)」でコピーしておいた IoT Hub 接続文字列に置き換えます。

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```

6. ターゲット デバイスを再起動するダイレクトメソッドを呼び出す次の関数を追加します。

    ```javascript
    var startRebootDevice = function(twin) {

        var methodName = "reboot";

        var methodParams = {
            methodName: methodName,
            payload: null,
            timeoutInSeconds: 30
        };

        client.invokeDeviceMethod(deviceToReboot, methodParams, function(err, result) {
            if (err) {
                console.error("Direct method error: "+err.message);
            } else {
                console.log("Successfully invoked the device to reboot.");  
            }
        });
    };
    ```

7. デバイスを照会して最後の再起動時間を取得する次の関数を追加します。

    ```javascript
    var queryTwinLastReboot = function() {

        registry.getTwin(deviceToReboot, function(err, twin){

            if (twin.properties.reported.iothubDM != null)
            {
                if (err) {
                    console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
                } else {
                    var lastRebootTime = twin.properties.reported.iothubDM.reboot.lastReboot;
                    console.log('Last reboot time: ' + JSON.stringify(lastRebootTime, null, 2));
                }
            } else 
                console.log('Waiting for device to report last reboot time.');
        });
    };
    ```

8. 再起動ダイレクト メソッドをトリガーする関数と最後の再起動時間のクエリを実行する関数を呼び出す次のコードを追加します。

    ```javascript
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```

9. **dmpatterns_getstarted_service.js** ファイルを保存して閉じます。

## <a name="run-the-apps"></a>アプリの実行

これで、アプリを実行する準備が整いました。

1. コマンド プロンプトで、**manageddevice** フォルダーに移動し、次のコマンドを実行して再起動のダイレクト メソッドのリッスンを開始します。

    ```cmd/sh
    node dmpatterns_getstarted_device.js
    ```

2. コマンド プロンプトで、**triggerrebootondevice** フォルダーに移動し、次のコマンドを実行してデバイス ツインのリモート再起動とクエリをトリガーして最後の再起動時刻を検索します。

    ```cmd/sh
    node dmpatterns_getstarted_service.js
    ```

3. 再起動ダイレクト メソッドに対するデバイスの応答と再起動の状態がコンソールに表示されます。

   サービスによって送信された再起動ダイレクト メソッドに対するデバイスの応答を次に示します。

   ![manageddevice アプリの出力](./media/iot-hub-node-node-device-management-get-started/device.png)

   再起動をトリガーしてデバイス ツインの前回の再起動時刻をポーリングするサービスを次に示します。

   ![triggerrebootondevice アプリの出力](./media/iot-hub-node-node-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
