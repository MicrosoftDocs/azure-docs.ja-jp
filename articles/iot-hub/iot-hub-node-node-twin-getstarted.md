---
title: "Azure IoT Hub デバイス ツインの使用 (Node) | Microsoft Docs"
description: "Azure IoT Hub デバイス ツインを使用してタグを追加し、IoT Hub クエリを使用する方法。 Azure IoT SDK for Node.js を使用して、シミュレートされたデバイス アプリと、タグを追加して IoT Hub クエリを実行するサービス アプリを実装します。"
services: iot-hub
documentationcenter: node
author: fsautomata
manager: timlt
editor: 
ms.assetid: 314c88e4-cce1-441c-b75a-d2e08e39ae7d
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: a243e4f64b6cd0bf7b0776e938150a352d424ad1
ms.openlocfilehash: 527aed57517f04d1b0fdcad5feac5488123b89c7


---
# <a name="get-started-with-device-twins-node"></a>デバイス ツインの使用 (Node)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

このチュートリアルの最後には、次の 2 つの Node.js コンソール アプリが完成します。

* **AddTagsAndQuery.js**: Node.js バックエンド アプリで、タグを追加してデバイス ツインのクエリを実行します。
* **TwinSimulatedDevice.js**: 以前作成したデバイス ID を使用して IoT Hub に接続するデバイスをシミュレートする Node.js アプリで、接続の状態を報告します。

> [!NOTE]
> デバイス アプリとバックエンド アプリの両方の作成に利用できる Azure IoT SDK に関する情報は、「[Azure IoT SDKs (Azure IoT SDK)][lnk-hub-sdks]」の記事で取り上げています。
> 
> 

このチュートリアルを完了するには、以下が必要です。

* Node.js バージョン 0.10.x 以降。
* アクティブな Azure アカウント。 (アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成できます)。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-the-service-app"></a>サービス アプリケーションを作成する
このセクションでは、**myDeviceId** に関連付けられたデバイス ツインに場所のメタデータを追加する Node.js コンソール アプリを作成します。 その後、米国にあるデバイスで、携帯ネットワーク接続を報告しているものを選択して、IoT ハブに格納されているデバイス ツインに対してクエリを実行します。

1. **addtagsandqueryapp** という名前の新しい空のフォルダーを作成します。 **addtagsandqueryapp** フォルダー内に、コマンド プロンプトで次のコマンドを使用して新しい package.json ファイルを作成します。 次の既定値をすべてそのまま使用します。
   
    ```
    npm init
    ```
2. コマンド プロンプトで、**addtagsandqueryapp** フォルダーに移動し、次のコマンドを実行して、**azure-iothub** パッケージをインストールします。
   
    ```
    npm install azure-iothub --save
    ```
3. テキスト エディターを使用して、**addtagsandqueryapp** フォルダーに新しい **AddTagsAndQuery.js** ファイルを作成します。
4. **AddTagsAndQuery.js** ファイルに次のコードを追加し、**{iot hub connection string}** プレースホルダーを、ハブの作成時にコピーした IoT Hub 接続文字列で置き換えます。
   
        'use strict';
        var iothub = require('azure-iothub');
        var connectionString = '{iot hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);
   
        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var patch = {
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                      }
                    }
                };
   
                twin.update(patch, function(err) {
                  if (err) {
                    console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                  } else {
                    console.log(twin.deviceId + ' twin updated successfully');
                    queryTwins();
                  }
                });
            }
        });
   
    **Registry** オブジェクトに、サービスからデバイス ツインとやりとりするのに必要なすべてのメソッドが表示されます。 前のコードでは、まず **Registry** オブジェクトを初期化し、**myDeviceId** のデバイス ツインを取得して、最後にタグを目的の位置情報で更新します。
   
    タグの更新後、**queryTwins** 関数を呼び出します。
5. **AddTagsAndQuery.js** の末尾に次のコードを追加して、**queryTwins** 関数を実装します。
   
        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
   
            query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43 using cellular network: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
        };
   
    前のコードは 2 つのクエリを実行します。1 つ目では、**Redmond43** 工場にあるデバイスのデバイス ツインのみを選択し、2 つ目のクエリで携帯ネットワーク経由で接続しているデバイスのみを選択するよう絞り込みます。
   
    前のコードでは、**query** オブジェクトの作成時に返されるドキュメントの最大数を指定します。 **query** オブジェクトには、**nextAsTwin** メソッドを複数回呼び出してすべての結果を取得する際に使用できる **hasMoreResults** のブール型プロパティが含まれます。 **next** というメソッドは、集計クエリの結果など、デバイス ツインの結果ではない場合に使用できます。
6. 以下を使用してアプリケーションを実行します。
   
        node AddTagsAndQuery.js
   
    **Redmond43** にあるすべてのデバイスを照会するクエリの結果には、1 件のデバイスが表示され、携帯ネットワークを使用するデバイスに絞り込んだ結果には 0 件のデバイスが表示されます。
   
    ![][1]

次のセクションでは、接続情報を報告し、前のセクションのクエリの結果を変更するデバイス アプリを作成します。

## <a name="create-the-device-app"></a>サービス アプリを作成する
このセクションでは、**myDeviceId** としてハブに接続し、デバイス ツインの報告されるプロパティに携帯ネットワークを使用しているという情報を含めるよう更新する Node.js コンソール アプリを作成します。

> [!NOTE]
> 現在のところ、デバイス ツインには MQTT プロトコルを使用して IoT Hub に接続するデバイスからのみアクセスできます。 既存のデバイス アプリを変換して MQTT を使用する方法については、[MQTT サポート][lnk-devguide-mqtt]の記事を参照してください。
> 
> 

1. **reportconnectivity** という名前の新しい空のフォルダーを作成します。 **reportconnectivity** フォルダー内に、コマンド プロンプトで次のコマンドを使用して新しい package.json ファイルを作成します。 次の既定値をすべてそのまま使用します。
   
    ```
    npm init
    ```
2. コマンド プロンプトで、**reportconnectivity** フォルダーに移動し、次のコマンドを実行して、**azure-iot-device** と **azure-iot-device-mqtt** パッケージをインストールします。
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. テキスト エディターを使用して、**reportconnectivity** フォルダーに新しい**ReportConnectivity.js** ファイルを作成します。
4. **ReportConnectivity.js** ファイルに次のコードを追加し、**{device connection string}** プレースホルダーを、**myDeviceId** のデバイス IDの作成時にコピーしたデバイス接続文字列で置き換えます。
   
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
   
            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };
   
                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });
   
    **Client** オブジェクトに、デバイスからデバイス ツインとやりとりするのに必要なすべてのメソッドが表示されます。 前のコードでは、**Client** オブジェクトを初期化した後、**myDeviceId** のデバイス ツインを取得して、報告されるプロパティに接続情報を含めるよう更新します。
5. デバイス アプリを実行する
   
        node ReportConnectivity.js
   
    `twin state reported` というメッセージが表示されます。
6. これで、デバイスが接続情報を報告したため、両方のクエリで表示されるようになります。 **addtagsandqueryapp** フォルダーに戻り、クエリを再度実行します。
   
        node AddTagsAndQuery.js
   
    今回は、**myDeviceId** が両方のクエリ結果に表示されるはずです。
   
    ![][3]

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Azure Portal で新しい IoT Hub を構成し、IoT Hub の ID レジストリにデバイス ID を作成しました。 バックエンド アプリからデバイスのメタデータをタグとして追加し、シミュレート対象デバイス アプリでデバイス ツインのデバイスの接続情報を報告するよう記述しました。 さらに、SQL に似た IoT Hub クエリ言語を使用してこの情報を照会する方法も学習しました。

詳細については、次のリソースをご覧ください。

* [IoT Hub の概要][lnk-iothub-getstarted]に関するチュートリアルでデバイスからテレメトリを送信する。
* 「[Use desired properties to configure devices (目的のプロパティを使用してデバイスを構成する)][lnk-twin-how-to-configure]」チュートリアルで、デバイス ツインの必要なプロパティを使用してデバイスを構成する。
* [ダイレクト メソッドの使用][lnk-methods-tutorial]に関するチュートリアルで、デバイスを対話形式で制御する (ユーザー制御アプリからファンをオンにするなど)。

<!-- images -->
[1]: media/iot-hub-node-node-twin-getstarted/service1.png
[3]: media/iot-hub-node-node-twin-getstarted/service2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

[lnk-twin-how-to-configure]: iot-hub-node-node-twin-how-to-configure.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md



<!--HONumber=Dec16_HO1-->


