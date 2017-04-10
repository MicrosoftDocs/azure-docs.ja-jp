---
title: "Azure IoT Hub を使用したデバイス ファームウェアの更新 (Node) | Microsoft Docs"
description: "Azure IoT Hub でデバイス管理を使用してデバイス ファームウェアの更新を開始する方法。 Azure IoT SDK for Node.js を使用して、シミュレートされたデバイス アプリと、ファームウェアの更新をトリガーするサービス アプリを実装します。"
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/06/2017
ms.author: juanpere
translationtype: Human Translation
ms.sourcegitcommit: 4ba60cee8848079935111ed3de480081a4aa58f6
ms.openlocfilehash: 30a707ec15d592c8a10905e13a75ea2f6e52cccc
ms.lasthandoff: 02/06/2017


---
# <a name="use-device-management-to-initiate-a-device-firmware-update-nodenode"></a>デバイス管理を使用してデバイス ファームウェアの更新を開始する (Node/Node)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

## <a name="introduction"></a>はじめに
[デバイス管理の開始][lnk-dm-getstarted]に関するチュートリアルでは、[デバイス ツイン][lnk-devtwin]と[ダイレクト メソッド][lnk-c2dmethod] プリミティブを使用してリモートでデバイスを再起動する方法を説明しました。 このチュートリアルでは、同じ IoT Hub プリミティブを使用して、エンド ツー エンドでシミュレートされたファームウェア更新を実行する方法を示します。  このパターンは、Intel Edison デバイス サンプルのファームウェア更新プログラムの実装で使用されます。

このチュートリアルでは、次の操作方法について説明します。

* シミュレート対象デバイス アプリで firmwareUpdate ダイレクト メソッドを IoT Hub 経由で呼び出す Node.js コンソール アプリを作成します。
* **firmwareUpdate** ダイレクト メソッドを実装するシミュレート対象デバイス アプリを作成します。 このメソッドは、ファームウェア イメージをダウンロードするまで待機し、ファームウェア イメージをダウンロードし、最後にファームウェア イメージを適用する、多段階のプロセスを開始します。 更新の各段階中、デバイスは、報告されるプロパティを使用して進捗状況を報告します。

このチュートリアルの最後には、次の&2; つの Node.js コンソール アプリが完成します。

**dmpatterns_fwupdate_service.js**: シミュレート対象デバイス アプリでダイレクト メソッドを呼び出し、応答を表示して、更新された報告されるプロパティを定期的に表示します (500 ミリ秒ごと)。

**dmpatterns_fwupdate_device.js**: IoT Hub とすでに作成したデバイス ID をつなげ、farmwareUpdate ダイレクト メソッドを受信し、イメージのダウンロードの待機、新しいイメージのダウンロード、イメージの適用というマルチステートメント プロセスを経由してファームウェアの更新をシミュレーションします。

このチュートリアルを完了するには、以下が必要です。

* Node.js バージョン 0.12.x 以降。 <br/>  「[Prepare your development environment (開発環境を準備する)][lnk-dev-setup]」では、このチュートリアルのために Node.js を Windows または Linux にインストールする方法が説明されています。
* アクティブな Azure アカウント。 (アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成できます)。

「[デバイス管理の開始](iot-hub-node-node-device-management-get-started.md)」の記事を参照して、IoT Hub を作成し、IoT Hub 接続文字列を取得します。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>ダイレクト メソッドを使用して、デバイス上でリモート ファームウェア更新を開始する
このセクションでは、デバイスでファームウェアのリモート更新を開始する Node.js コンソール アプリケーションを作成します。 アプリケーションは、ダイレクト メソッドを使用して更新を開始し、デバイス ツイン クエリを使用してアクティブなファームウェア更新の状態を定期的に取得します。

1. **triggerfwupdateondevice** という名前の空のフォルダーを作成します。  コマンド プロンプトで次のコマンドを使用して、**triggerfwupdateondevice** フォルダー内に新しい package.json ファイルを作成します。  次の既定値をすべてそのまま使用します。
   
    ```
    npm init
    ```
2. コマンド プロンプトで、**triggerfwupdateondevice** フォルダーに移動し、次のコマンドを実行して、**azure-iot-hub** Device SDK パッケージと **azure-iot-device-mqtt** Device SDK パッケージをインストールします。
   
    ```
    npm install azure-iot-hub --save
    ```
3. テキスト エディターを使用して、**triggerfwupdateondevice** フォルダーに **dmpatterns_getstarted_service.js** ファイルを作成します。
4. **dmpatterns_getstarted_service.js** ファイルの先頭に、次の 'require' ステートメントを追加します。
   
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```
5. 次の変数宣言を追加して、プレース ホルダーの値を置き換えます。
   
    ```
    var connectionString = '{device_connectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToUpdate = 'myDeviceId';
    ```
6. 次の関数を追加して、firmwareUpdate の Reported puropathy の値を見つけて表示します。
   
    ```
    var queryTwinFWUpdateReported = function() {
        registry.getTwin(deviceToUpdate, function(err, twin){
            if (err) {
              console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
            } else {
              console.log((JSON.stringify(twin.properties.reported.iothubDM.firmwareUpdate)) + "\n");
            }
        });
    };
    ```
7. 次の関数を追加して、firmwareUpdate メソッドを呼び出してターゲット デバイスを再起動します。
   
    ```
    var startFirmwareUpdateDevice = function() {
      var params = {
          fwPackageUri: 'https://secureurl'
      };
   
      var methodName = "firmwareUpdate";
      var payloadData =  JSON.stringify(params);
   
      var methodParams = {
        methodName: methodName,
        payload: payloadData,
        timeoutInSeconds: 30
      };
   
      client.invokeDeviceMethod(deviceToUpdate, methodParams, function(err, result) {
        if (err) {
          console.error('Could not start the firmware update on the device: ' + err.message)
        } 
      });
    };
    ```
8. 最後に、コードに次の関数を追加して、ファームウェア更新シーケンスを開始し、報告されるプロパティの定期的な表示を開始します。
   
    ```
    startFirmwareUpdateDevice();
    setInterval(queryTwinFWUpdateReported, 500);
    ```
9. **dmpatterns_fwupdate_service.js** ファイルを保存して閉じます。

[!INCLUDE [iot-hub-device-firmware-update](../../includes/iot-hub-device-firmware-update.md)]

## <a name="run-the-apps"></a>アプリの実行
これで、アプリを実行する準備が整いました。

1. コマンド プロンプトで、**manageddevice** フォルダーに移動し、次のコマンドを実行して再起動のダイレクト メソッドのリッスンを開始します。
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. コマンド プロンプトで、**triggerfwupdateondevice** フォルダーに移動し、次のコマンドを実行してデバイス ツインのリモート再起動とクエリをトリガーして最後の再起動時刻を検索します。
   
    ```
    node dmpatterns_fwupdate_service.js
    ```
3. ダイレクト メソッドに対するデバイスの応答がコンソールに表示されます。

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、ダイレクト メソッドを使用してデバイス上でリモートのファームウェア更新を開始し、報告されるプロパティを使用してファームウェア更新の進捗状況を確認しました。

IoT ソリューションの拡張と複数のデバイスでのメソッドの呼び出しをスケジュールする方法については、「[ジョブのスケジュールとブロードキャスト][lnk-tutorial-jobs]」チュートリアルを参照してください。

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

