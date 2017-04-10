---
title: "Azure IoT Hub デバイス管理の開始 (Node) | Microsoft Docs"
description: "IoT Hub デバイス管理を使用してリモート デバイスの再起動を開始する方法。 Azure IoT SDK for Node.js を使用して、ダイレクト メソッドを含むシミュレートされたデバイス アプリと、ダイレクト メソッドを呼び出すサービス アプリを実装します。"
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: e044006d-ffd6-469b-bc63-c182ad066e31
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: juanpere
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: ecc6f4a1a8cbb07d9f610e8f6fb5ca66b7532513
ms.lasthandoff: 03/07/2017


---
# <a name="get-started-with-device-management-node"></a>デバイス管理の開始 (Node)
## <a name="introduction"></a>はじめに
IoT クラウド アプリケーションは、Azure IoT Hub のプリミティブ (デバイス ツインとダイレクト メソッド) を使用して、デバイス上でデバイス管理アクションをリモートで開始して監視できます。 この記事では、IoT クラウド アプリケーションとデバイスが、IoT Hub を使用したデバイスの再起動をリモートで開始して監視するためにどのように連携するかについて説明し、それらの操作を実行するためのコードを示します。

クラウドベースのバックエンド アプリから、デバイスのデバイス管理操作をリモートで起動して監視するには、[デバイス ツイン][lnk-devtwin]や[ダイレクト メソッド][lnk-c2dmethod]などの Azure IoT Hub プリミティブを使用します。 このチュートリアルでは、バックエンド アプリケーションとデバイスをどのように連携させると、IoT Hub からデバイスの再起動をリモートで開始して監視できるかについて示します。

ダイレクト メソッドを使用して、クラウド内のバックエンド アプリケーションからデバイス管理操作 (再起動、出荷時の設定に戻す、ファームウェアの更新など) を開始します。 デバイスは次の操作を担当します。

* IoT Hub から送信されたメソッド要求の処理。
* デバイスでの対応するデバイス固有の操作の開始。
* 報告されるプロパティを介した IoT Hub への状態更新の提供。

クラウドでバックエンド アプリを使用してデバイス ツインのクエリを実行することで、デバイス管理操作の進行状況を報告できます。

このチュートリアルでは、次の操作方法について説明します。

* Azure Portal を使用して IoT Hub を作成し、IoT Hub 内にデバイス ID を作成します。
* デバイスを再起動するダイレクト メソッドを含む、シミュレション済みデバイス アプリを作成します。 ダイレクト メソッドは、クラウドから呼び出されます。
* シミュレートされたデバイス アプリで再起動ダイレクト メソッドを IoT Hub 経由で呼び出す .NET コンソール アプリを作成します。

このチュートリアルの最後には、次の&2; つの Node.js コンソール アプリが完成します。

**dmpatterns_getstarted_device.js**: IoT Hub を、作成済みのデバイス ID に接続し、再起動ダイレクト メソッドを受信し、物理的な再起動をシミュレートし、最後の再起動時間を報告します。

**dmpatterns_getstarted_service.js**: シミュレートされたデバイス アプリでダイレクト メソッドを呼び出し、応答を表示し、更新された報告されるプロパティを表示します。

このチュートリアルを完了するには、以下が必要です。

* Node.js バージョン 0.12.x 以降。 <br/>  「[Prepare your development environment (開発環境を準備する)][lnk-dev-setup]」では、このチュートリアルのために Node.js を Windows または Linux にインストールする方法が説明されています。
* アクティブな Azure アカウント。 (アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成できます)。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>シミュレーション対象デバイス アプリの作成
このセクションでは、次の作業を行います。

* クラウドによって呼び出されたダイレクト メソッドに応答する Node.js コンソール アプリを作成します。
* シミュレート対象デバイスの再起動をトリガーします。
* 報告されるプロパティを使用して、デバイス ツイン クエリで、デバイスとデバイスの最後の再起動時間を識別できるようにします。

1. **manageddevice** という名前の空のフォルダーを作成します。  コマンド プロンプトで次のコマンドを使用して、**manageddevice** フォルダー内に新しい package.json ファイルを作成します。  次の既定値をすべてそのまま使用します。
   
    ```
    npm init
    ```
2. コマンド プロンプトで、**manageddevice** フォルダーに移動し、次のコマンドを実行して、**azure-iot-device** Device SDK パッケージと **azure-iot-device-mqtt** パッケージをインストールします。
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. テキスト エディターを使用して、**manageddevice** フォルダーに **dmpatterns_getstarted_device.js** ファイルを作成します。
4. **dmpatterns_getstarted_device.js** ファイルの先頭に、次の 'require' ステートメントを追加します。
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. **connectionString** 変数を追加し、それを使用して **Client** インスタンスを作成します。  接続文字列を、デバイスの接続文字列に置き換えます。  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. デバイスにダイレクト メソッドを実装する次の関数を追加します。
   
    ```
    var onReboot = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
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
   
    ```
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
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードでは、[一時的な障害処理][lnk-transient-faults]に関する MSDN の記事で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>ダイレクト メソッドを使用してデバイスのリモート再起動をトリガーする
このセクションでは、ダイレクト メソッドを使用してデバイスでのリモート再起動を開始する .NET コンソール アプリケーションを作成します (C# を使用します)。 このアプリは、デバイス ツイン クエリを使用して、そのデバイスの前回の再起動時刻を検出します。

1. **triggerrebootondevice** という名前の空のフォルダーを作成します。  コマンド プロンプトで次のコマンドを使用して、**triggerrebootondevice** フォルダー内に新しい package.json ファイルを作成します。  次の既定値をすべてそのまま使用します。
   
    ```
    npm init
    ```
2. コマンド プロンプトで、**triggerrebootondevice** フォルダーに移動し、次のコマンドを実行して、**azure-iothub** Device SDK パッケージと **azure-iot-device-mqtt** パッケージをインストールします。
   
    ```
    npm install azure-iothub --save
    ```
3. テキスト エディターを使用して、**triggerrebootondevice** フォルダー内に **dmpatterns_getstarted_service.js** ファイルを作成します。
4. **dmpatterns_getstarted_service.js** ファイルの先頭に、次の 'require' ステートメントを追加します。
   
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```
5. 次の変数宣言を追加して、プレース ホルダーの値を置き換えます。
   
    ```
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```
6. ターゲット デバイスを再起動するダイレクトメソッドを呼び出す次の関数を追加します。
   
    ```
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
   
    ```
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
   
    ```
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```
9. **dmpatterns_getstarted_service.js** ファイルを保存して閉じます。

## <a name="run-the-apps"></a>アプリの実行
これで、アプリを実行する準備が整いました。

1. コマンド プロンプトで、**manageddevice** フォルダーに移動し、次のコマンドを実行して再起動のダイレクト メソッドのリッスンを開始します。
   
    ```
    node dmpatterns_getstarted_device.js
    ```
2. コマンド プロンプトで、**triggerrebootondevice** フォルダーに移動し、次のコマンドを実行してデバイス ツインのリモート再起動とクエリをトリガーして最後の再起動時刻を検索します。
   
    ```
    node dmpatterns_getstarted_service.js
    ```
3. ダイレクト メソッドに対するデバイスの応答がコンソールに表示されます。

## <a name="customize-and-extend-the-device-management-actions"></a>デバイス管理操作のカスタマイズと拡張を行う
IoT ソリューションでは、定義された一連のデバイス管理パターンを拡張したり、デバイス ツインと cloud-to-device メソッド プリミティブを使用してカスタム パターンを作成したりできます。 デバイス管理操作の他の例には、出荷時の設定への復帰、ファームウェアの更新、ソフトウェアの更新、電源管理、ネットワークと接続の管理、データの暗号化などがあります。

## <a name="device-maintenance-windows"></a>デバイスのメンテナンス期間
通常、デバイスは、中断やダウンタイムを最小限に抑えることができる時間に操作を実行するように構成します。  デバイスのメンテナンス期間は、デバイスがその構成を更新する必要がある時間を定義する一般的に使用されるパターンです。 バックエンド ソリューションは、デバイス ツインの必要なプロパティを使用して、メンテナンス期間を可能にするデバイスのポリシーを定義してアクティブにすることができます。 デバイスは、メンテナンス期間ポリシーを受信したときに、デバイス ツインの報告されたプロパティを使用してポリシーの状態を報告することができます。 その後、バックエンド アプリケーションは、デバイス ツインのクエリを使用して、デバイスが各ポリシーに対応していることを確認できます。

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、ダイレクト メソッドを使用して、デバイスのリモート再起動をトリガーしました。 報告されるプロパティを使用してデバイスの最後の再起動時間を報告し、デバイス ツインのクエリを実行してクラウドからデバイスの最後の再起動時間を検出しました。

ファームウェアのリモートでのワイヤレス更新などの IoT Hub による他のデバイス管理パターンを確認するには、次の記事を参照してください。

[チュートリアル: ファームウェアを更新する方法][lnk-fwupdate]

IoT ソリューションの拡張と複数のデバイスでのメソッドの呼び出しをスケジュールする方法については、「[ジョブのスケジュールとブロードキャスト][lnk-tutorial-jobs]」チュートリアルを参照してください。

引き続き IoT Hub の使用方法を確認するには、[IoT Gateway SDK の使用][lnk-gateway-SDK]に関する記事を参照してください。

<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

