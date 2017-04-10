---
title: "Azure IoT Hub デバイス ツインのプロパティの使用 (Node) | Microsoft Docs"
description: "Azure IoT Hub デバイス ツインを使用してデバイスを構成する方法。 Azure IoT SDK for Node.js を使用して、シミュレートされたデバイス アプリと、デバイス ツインを使用してデバイスの構成を変更するサービス アプリを実装します。"
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: d0bcec50-26e6-40f0-8096-733b2f3071ec
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: a243e4f64b6cd0bf7b0776e938150a352d424ad1
ms.openlocfilehash: 397dffe8ec93ced9196bce8fcc12a058c6876bd4


---
# <a name="use-desired-properties-to-configure-devices-node"></a>必要なプロパティを使用してデバイスを構成する (Node)
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

このチュートリアルの最後には、次の 2 つの Node.js コンソール アプリが完成します。

* **SimulateDeviceConfiguration.js**: 必要な構成の更新を待機し、シミュレートされた構成の更新プロセスの状態を報告する、シミュレートされたデバイス アプリです。
* **SetDesiredConfigurationAndQuery.js**: Node.js バックエンド アプリで、デバイス上に目的の構成を設定し、構成更新プロセスをクエリします。

> [!NOTE]
> デバイス アプリケーションとバックエンド アプリの両方の作成に利用できる Azure IoT SDK に関する情報は、「[Azure IoT SDKs (Azure IoT SDK)][lnk-hub-sdks]」の記事で取り上げています。
> 
> 

このチュートリアルを完了するには、以下が必要です。

* Node.js バージョン 0.10.x 以降。
* アクティブな Azure アカウント。 (アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成できます)。

[デバイス ツインの概要][lnk-twin-tutorial]に関するチュートリアルを行った場合は、既に IoT ハブと、**myDeviceId** というデバイス ID があるため、「[シミュレートされたデバイス アプリを作成する][lnk-how-to-configure-createapp]」セクションに進んでください。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-the-simulated-device-app"></a>シミュレートされたデバイスのアプリを作成する
このセクションでは、ハブに **myDeviceId** として接続し、必要な構成の更新を待機して、シミュレートされた構成の更新プロセスの情報を報告する、Node.js コンソール アプリを作成します。

1. **simulatedeviceconfiguration** という名前の新しい空のフォルダーを作成します。 **simulatedeviceconfiguration** フォルダー内に、コマンド プロンプトで次のコマンドを使用して新しい package.json ファイルを作成します。 次の既定値をすべてそのまま使用します。
   
    ```
    npm init
    ```
2. コマンド プロンプトで、**simulatedeviceconfiguration** フォルダーに移動し、次のコマンドを実行して、**azure-iot-device** と **azure-iot-device-mqtt** パッケージをインストールします。
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. テキスト エディターを使用して、**simulatedeviceconfiguration** フォルダーに新しい **SimulateDeviceConfiguration.js** ファイルを作成します。
4. **SimulateDeviceConfiguration.js** ファイルに次のコードを追加し、**{device connection string}** プレースホルダーを、**myDeviceId** のデバイス IDの作成時にコピーしたデバイス接続文字列で置き換えます。
   
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
            if (err) {
                console.error('could not open IotHub client');
            } else {
                client.getTwin(function(err, twin) {
                    if (err) {
                        console.error('could not get twin');
                    } else {
                        console.log('retrieved device twin');
                        twin.properties.reported.telemetryConfig = {
                            configId: "0",
                            sendFrequency: "24h"
                        }
                        twin.on('properties.desired', function(desiredChange) {
                            console.log("received change: "+JSON.stringify(desiredChange));
                            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
                            if (desiredChange.telemetryConfig &&desiredChange.telemetryConfig.configId !== currentTelemetryConfig.configId) {
                                initConfigChange(twin);
                            }
                        });
                    }
                });
            }
        });
   
    **Client** オブジェクトに、デバイスからデバイス ツインとやりとりするのに必要なすべてのメソッドが表示されます。 前のコードでは、**Client** オブジェクトを初期化した後、**myDeviceId** のデバイス ツインを取得して、必要なプロパティの更新のハンドラーをアタッチします。 ハンドラーは、configIds を比較して実際に構成の変更リクエストがあることを確認してから、構成の変更を開始するメソッドを呼び出します。
   
    わかりやすくするため、前のコードでは初期構成にハードコーディングされた既定値を使用しています。 実際のアプリでは、おそらくローカル ストレージから構成を読み込みます。
   
   > [!IMPORTANT]
   > 目的のプロパティの変更イベントは常にデバイスの接続時に発行されるため、何らかの操作を行う前に、目的のプロパティに実際に変更点があることをかならずご確認ください。
   > 
   > 
5. `client.open()` の呼び出しの前に次のメソッドを追加します。
   
        var initConfigChange = function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.pendingConfig = twin.properties.desired.telemetryConfig;
            currentTelemetryConfig.status = "Pending";
   
            var patch = {
            telemetryConfig: currentTelemetryConfig
            };
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.log('Could not report properties');
                } else {
                    console.log('Reported pending config change: ' + JSON.stringify(patch));
                    setTimeout(function() {completeConfigChange(twin);}, 60000);
                }
            });
        }
   
        var completeConfigChange =  function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.configId = currentTelemetryConfig.pendingConfig.configId;
            currentTelemetryConfig.sendFrequency = currentTelemetryConfig.pendingConfig.sendFrequency;
            currentTelemetryConfig.status = "Success";
            delete currentTelemetryConfig.pendingConfig;
   
            var patch = {
                telemetryConfig: currentTelemetryConfig
            };
            patch.telemetryConfig.pendingConfig = null;
   
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.error('Error reporting properties: ' + err);
                } else {
                    console.log('Reported completed config change: ' + JSON.stringify(patch));
                }
            });
        };
   
    **initConfigChange** メソッドは、構成の更新要求を使用してローカル デバイス ツイン オブジェクトの報告されるプロパティを更新し、状態を **保留中**に設定し、サービス上のデバイス ツインを更新します。 デバイス ツインを正常に更新できたら、**completeConfigChange** を実行して停止する、長期間実行していたプロセスをシミュレートします。 このメソッドは、ローカル デバイス ツインの報告されるプロパティを更新し、状態を **Success (成功)** に設定して、**pendingConfig** オブジェクトを削除します。 その後、サービス上のデバイス ツインを更新します。
   
    帯域幅を節約するため、報告されたプロパティの更新は、ドキュメント全体を置き換えるのではなく、変更するプロパティのみを指定して行われます (上のコードでは **patch**)。
   
   > [!NOTE]
   > このチュートリアルでは、同時実行の構成の更新動作はシミュレートしません。 一部の構成更新プロセスでは、更新の実行中に対象の構成に変更を加えることができますが、場合によってはキューに入れる必要がある場合や、エラー条件で拒否されることがあります。 お使いの構成プロセスに必要な動作を考慮し、構成の変更を開始する前に適切なロジックを追加するようにしてください。
   > 
   > 
6. デバイス アプリを実行する:
   
        node SimulateDeviceConfiguration.js
   
    `retrieved device twin` というメッセージが表示されます。 そのままアプリを実行します。

## <a name="create-the-service-app"></a>サービス アプリケーションを作成する
このセクションでは、新しいテレメトリの構成オブジェクトを使って、**myDeviceId** に関連付けられたデバイス ツインの "*必要なプロパティ*" を更新する、Node.js コンソール アプリを作成します。 その後このアプリでは、IoT ハブに格納されているデバイス ツインにクエリを実行し、デバイスの必要な構成と報告される構成の違いを示します。

1. **setdesiredandqueryapp** という名前の新しい空のフォルダーを作成します。 **setdesiredandqueryapp** フォルダー内に、コマンド プロンプトで次のコマンドを使用して新しい package.json ファイルを作成します。 次の既定値をすべてそのまま使用します。
   
    ```
    npm init
    ```
2. コマンド プロンプトで、**setdesiredandqueryapp** フォルダーに移動し、次のコマンドを実行して、**azure-iothub** パッケージをインストールします。
   
    ```
    npm install azure-iothub node-uuid --save
    ```
3. テキスト エディターを使用して、**addtagsandqueryapp** フォルダーに新しい **SetDesiredAndQuery.js** ファイルを作成します。
4. **SetDesiredAndQuery.js** ファイルに次のコードを追加し、**{iot hub connection string}** プレースホルダーを、ハブの作成時にコピーした IoT Hub 接続文字列で置き換えます。
   
        'use strict';
        var iothub = require('azure-iothub');
        var uuid = require('node-uuid');
        var connectionString = '{iot hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);
   
        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var newConfigId = uuid.v4();
                var newFrequency = process.argv[2] || "5m";
                var patch = {
                    properties: {
                        desired: {
                            telemetryConfig: {
                                configId: newConfigId,
                                sendFrequency: newFrequency
                            }
                        }
                    }
                }
                twin.update(patch, function(err) {
                    if (err) {
                        console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                    } else {
                        console.log(twin.deviceId + ' twin updated successfully');
                    }
                });
                setInterval(queryTwins, 10000);
            }
        });

    **Registry** オブジェクトに、サービスからデバイス ツインとやりとりするのに必要なすべてのメソッドが表示されます。 前のコードでは、**Registry** オブジェクトを初期化した後、**myDeviceId** のデバイス ツインを取得して、新しいテレメトリ構成オブジェクトを使って必要なプロパティを更新します。 その後、10 秒ごとに **queryTwins** 関数を呼び出します。

    > [!IMPORTANT]
    > このアプリケーションでは、例示を目的として 10 秒ごとに IoT Hub にクエリを実行します。 変更を検出するためではなく、あらゆるデバイスのユーザー向けのレポートを生成するためにクエリを使用します。 ソリューションにデバイス イベントのリアルタイム通知が必要な場合は、[デバイスからクラウドへのメッセージ][lnk-d2c]を使用します。
    > 
    >に関するページを参照してください。

1. `registry.getDeviceTwin()` の呼び出し前に次のコードを追加して、**queryTwins** 関数を実装します。
   
        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log();
                    results.forEach(function(twin) {
                        var desiredConfig = twin.properties.desired.telemetryConfig;
                        var reportedConfig = twin.properties.reported.telemetryConfig;
                        console.log("Config report for: " + twin.deviceId);
                        console.log("Desired: ");
                        console.log(JSON.stringify(desiredConfig, null, 2));
                        console.log("Reported: ");
                        console.log(JSON.stringify(reportedConfig, null, 2));
                    });
                }
            });
        };
   
    前のコードでは、IoT ハブに格納されたデバイス ツインにクエリを実行し、必要なテレメトリ構成と報告されるテレメトリ構成を出力します。 すべてのデバイスに関する豊富なレポートを生成する方法については、[IoT Hub のクエリ言語][lnk-query]に関するページを参照してください。
2. **SimulateDeviceConfiguration.js** の実行中に、以下を使ってアプリケーションを実行します。
   
        node SetDesiredAndQuery.js 5m
   
    報告される構成が、**「成功」**から**「保留中」**に変わり、24 時間ではなく 5分の新しいアクティブな送信頻度を使って再度**「成功」**に変わります。
   
   > [!IMPORTANT]
   > デバイスのレポート操作とクエリの結果までには、最大 1 分間の遅延が生じます。 これは、非常に大きな規模でクエリのインフラストラクチャを動作させるためです。 1 つのデバイス ツインの一貫したビューを取得するには、**Registry** クラスで **getDeviceTwin** メソッドを使用します。
   > 
   > 

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、バックエンド アプリから必要な構成を "*必要なプロパティ*" として設定し、その変更を検出してデバイス ツインに "*報告されるプロパティ*" として状態を報告するマルチステップの更新プロセスをシミュレートするための、シミュレートされたデバイス アプリを記述しました。

詳細については、次のリソースをご覧ください。

* [IoT Hub の概要][lnk-iothub-getstarted]に関するチュートリアルでデバイスからテレメトリを送信する。
* 多数のデバイスで操作をスケジュールまたは実行するには、[ジョブのスケジュールとブロードキャスト][lnk-schedule-jobs]に関するチュートリアルを参照してください。
* [ダイレクト メソッドの使用][lnk-methods-tutorial]に関するチュートリアルで、デバイスを対話形式で制御する (ユーザー制御アプリからファンをオンにするなど)。

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-node-node-twin-how-to-configure.md#create-the-simulated-device-app



<!--HONumber=Dec16_HO1-->


