---
title: "Azure IoT Hub のダイレクト メソッド (Node) | Microsoft Docs"
description: "Azure IoT Hub のダイレクト メソッドの使用方法 Azure IoT SDK for Node.js を使用して、ダイレクト メソッドを含むシミュレートされたデバイス アプリと、ダイレクト メソッドを呼び出すサービス アプリを実装します。"
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: ea9c73ca-7778-4e38-a8f1-0bee9d142f04
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2017
ms.author: nberdy
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: c76870c49f5c8e51f521fd4be920d976e4cdbcef
ms.openlocfilehash: 7100856b02935fba7bbf1427d86859ddb7a2b5f3
ms.lasthandoff: 03/02/2017


---
# <a name="use-direct-methods-on-your-iot-device-with-nodejs"></a>Node.js を使用した IoT デバイスでのダイレクト メソッドの使用
[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

このチュートリアルの最後には、次の&2; つの Node.js コンソール アプリが完成します。

* **CallMethodOnDevice.js**。シミュレートされたデバイス アプリでメソッドを呼び出し、応答を表示します。
* **SimulatedDevice.js**。前もって作成してあるデバイス ID を使用して IoT Hub に接続し、クラウドによって呼び出されたメソッドに応答をします。

> [!NOTE]
> デバイス上で動作するアプリケーションの作成とソリューションのバックエンドで動作するアプリケーションの開発に利用できる各種 Azure IoT SDK については、[Azure IoT SDK][lnk-hub-sdks] に関する記事を参照してください。
> 
> 

このチュートリアルを完了するには、以下が必要です。

* Node.js バージョン 0.10.x 以降。
* アクティブな Azure アカウント。 (アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成できます)。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>シミュレーション対象デバイス アプリの作成
このセクションでは、クラウドによって呼び出されたメソッドに応答する Node.js コンソール アプリを作成します。

1. **simulateddevice**という名前の新しい空のフォルダーを作成します。 コマンド プロンプトで次のコマンドを使用して、**simulateddevice** フォルダー内に新しい package.json ファイルを作成します。 次の既定値をすべてそのまま使用します。
   
    ```
    npm init
    ```
2. コマンド プロンプトで、**simulateddevice** フォルダーに移動し、次のコマンドを実行して、**azure-iot-device** Device SDK パッケージと **azure-iot-device-mqtt** パッケージをインストールします。
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. テキスト エディターを使用して、**simulateddevice** フォルダーに新しい **SimulatedDevice.js** ファイルを作成します。
4. **SimulatedDevice.js** ファイルの先頭に、次の `require` ステートメントを追加します。
   
    ```
    'use strict';
   
    var Mqtt = require('azure-iot-device-mqtt').Mqtt;
    var DeviceClient = require('azure-iot-device').Client;
    ```
5. **connectionString** 変数を追加し、それを使用して **DeviceClient** インスタンスを作成します。 **{device connection string}** を "*デバイス ID の作成*" セクションで生成したデバイス接続文字列で置き換えます。
   
    ```
    var connectionString = '{device connection string}';
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);
    ```
6. デバイスにメソッドを実装する次の関数を追加します。
   
    ```
    function onWriteLine(request, response) {
        console.log(request.payload);
   
        response.send(200, 'Input was written to log.', function(err) {
            if(err) {
                console.error('An error ocurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```
7. IoT Hub への接続を開き、メソッド リスナーの初期化を開始します。
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
            client.onDeviceMethod('writeLine', onWriteLine);
        }
    });
    ```
8. **SimulatedDevice.js** ファイルを保存して閉じます。

> [!NOTE]
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードでは、MSDN の記事「[Transient Fault Handling (一時的な障害処理)][lnk-transient-faults]」で推奨されているように、再試行ポリシー (接続の再試行など) を実装することをお勧めします。
> 
> 

## <a name="call-a-method-on-a-device"></a>デバイスでメソッドを呼び出します。
このセクションでは、シミュレートされたデバイス アプリでメソッドを呼び出し、応答を表示する Node.js コンソール アプリを作成します。

1. 「**callmethodondevice**」という名前の新しい空のフォルダーを作成します。 コマンド プロンプトで次のコマンドを使用して、**callmethodondevice** フォルダー内に新しい package.json ファイルを作成します。 次の既定値をすべてそのまま使用します。
   
    ```
    npm init
    ```
2. コマンド プロンプトで、**callmethodondevice** フォルダーに移動し、次のコマンドを実行して、**azure-iothub** パッケージをインストールします。
   
    ```
    npm install azure-iothub --save
    ```
3. テキスト エディターを使用して、**callmethodondevice** フォルダーに **CallMethodOnDevice.js** ファイルを作成します。
4. **CallMethodOnDevice.js** ファイルの先頭に、次の `require` ステートメントを追加します。
   
    ```
    'use strict';
   
    var Client = require('azure-iothub').Client;
    ```
5. 次の変数宣言を追加し、プレースホルダーの値をハブの IoT Hub 接続文字列に置き換えます。
   
    ```
    var connectionString = '{iothub connection string}';
    var methodName = 'writeLine';
    var deviceId = 'myDeviceId';
    ```
6. IoT Hub への接続を開くクライアントを作成します。
   
    ```
    var client = Client.fromConnectionString(connectionString);
    ```
7. デバイスのメソッドを呼び出し、コンソールにデバイスの応答を出力する、次の関数を追加します。
   
    ```
    var methodParams = {
        methodName: methodName,
        payload: 'hello world',
        timeoutInSeconds: 30
    };
   
    client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
        if (err) {
            console.error('Failed to invoke method \'' + methodName + '\': ' + err.message);
        } else {
            console.log(methodName + ' on ' + deviceId + ':');
            console.log(JSON.stringify(result, null, 2));
        }
    });
    ```
8. **CallMethodOnDevice.js** ファイルを保存して閉じます。

## <a name="run-the-apps"></a>アプリの実行
これで、アプリを実行する準備が整いました。

1. コマンド プロンプトで、**simulateddevice** フォルダーに移動し、次のコマンドを実行して、IoT Hub からのメソッドの呼び出しのリッスンを開始します。
   
    ```
    node SimulatedDevice.js
    ```
   
    ![][7]
2. コマンド プロンプトで、**callmethodondevice** フォルダーに移動し、次のコマンドを実行して IoT Hub の監視を開始します。
   
    ```
    node CallMethodOnDevice.js 
    ```
   
    ![][8]
3. メッセージと、デバイスからの応答を表示するメソッドを呼び出したアプリケーションを出力することによって、デバイスの対応を確認できます。
   
    ![][9]

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Azure Portal で新しい IoT Hub を構成し、IoT Hub の ID レジストリにデバイス ID を作成しました。 シミュレーション対象デバイス アプリでクラウドから呼び出したメソッドに対応できるようにするために、このデバイス ID を使用しました。 また、デバイスでメソッドを呼び出し、デバイスからの応答を表示するアプリも作成しました。 

引き続き IoT Hub の使用方法を確認すると共に、他の IoT のシナリオについて調べるには、次のページを参照してください。

* [IoT Hub の使用]
* [複数デバイスで実行されるジョブのスケジュール設定][lnk-devguide-jobs]

IoT ソリューションの拡張と複数のデバイスでのメソッドの呼び出しをスケジュールする方法については、「[ジョブのスケジュールとブロードキャスト][lnk-tutorial-jobs]」チュートリアルを参照してください。

<!-- Images. -->
[7]: ./media/iot-hub-node-node-direct-methods/run-simulated-device.png
[8]: ./media/iot-hub-node-node-direct-methods/run-callmethodondevice.png
[9]: ./media/iot-hub-node-node-direct-methods/methods-output.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[IoT Hub の使用]: iot-hub-node-node-getstarted.md

