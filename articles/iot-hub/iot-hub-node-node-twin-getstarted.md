---
title: Azure IoT Hub デバイス ツインの使用 (Node) | Microsoft Docs
description: Azure IoT Hub デバイス ツインを使用してタグを追加し、IoT Hub クエリを使用する方法。 Azure IoT SDK for Node.js を使用して、シミュレートされたデバイス アプリと、タグを追加して IoT Hub クエリを実行するサービス アプリを実装します。
author: fsautomata
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: elioda
ms.custom: mqtt, devx-track-js
ms.openlocfilehash: 65ced3812072bd2650fc36bbb7a7b0f3f75e0def
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91336787"
---
# <a name="get-started-with-device-twins-nodejs"></a>デバイス ツインの使用 (Node.js)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

このチュートリアルの最後には、次の 2 つの Node.js コンソール アプリが完成します。

* **AddTagsAndQuery.js**: Node.js バックエンド アプリで、タグを追加してデバイス ツインのクエリを実行します。

* **TwinSimulatedDevice.js**: 以前作成したデバイス ID を使用して IoT ハブに接続するデバイスをシミュレートする Node.js アプリで、接続の状態を報告します。

> [!NOTE]
> デバイス アプリとバックエンド アプリの両方をビルドするために使用できる Azure IoT SDK については、記事「[Azure IoT SDK](iot-hub-devguide-sdks.md)」を参照してください。
>

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。

* Node.js バージョン 10.0.x 以降。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 (アカウントがない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を数分で作成できます)。

* ポート 8883 がファイアウォールで開放されていることを確認してください。 この記事のデバイス サンプルでは、ポート 8883 を介して通信する MQTT プロトコルを使用しています。 このポートは、企業や教育用のネットワーク環境によってはブロックされている場合があります。 この問題の詳細と対処方法については、「[IoT Hub への接続 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)」を参照してください。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT ハブに新しいデバイスを登録する

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>IoT ハブ接続文字列を取得する

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>デバイス アプリを作成する

このセクションでは、**myDeviceId** に関連付けられたデバイス ツインに場所のメタデータを追加する Node.js コンソール アプリを作成します。 その後、米国にあるデバイスで、携帯ネットワーク接続を報告しているものを選択して、IoT Hub に格納されているデバイス ツインに対してクエリを実行します。

1. **addtagsandqueryapp** という名前の新しい空のフォルダーを作成します。 **addtagsandqueryapp** フォルダー内に、コマンド プロンプトで次のコマンドを使用して新しい package.json ファイルを作成します。 `--yes` パラメーターではすべての既定値を使用できます。

    ```cmd/sh
    npm init --yes
    ```

2. コマンド プロンプトで、**addtagsandqueryapp** フォルダーに移動し、次のコマンドを実行して、**azure-iothub** パッケージをインストールします。

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. テキスト エディターを使用して、**addtagsandqueryapp** フォルダーに新しい **AddTagsAndQuery.js** ファイルを作成します。

4. 次のコードを **AddTagsAndQuery.js** ファイルに追加します。 `{iot hub connection string}` を、「[IoT ハブ接続文字列を取得する](#get-the-iot-hub-connection-string)」でコピーしておいた IoT ハブ接続文字列に置き換えます。

   ``` javascript
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
   ```

    **Registry** オブジェクトに、サービスからデバイス ツインとやりとりするのに必要なすべてのメソッドが表示されます。 前のコードでは、まず **Registry** オブジェクトを初期化し、**myDeviceId** のデバイス ツインを取得して、最後にタグを目的の位置情報で更新します。

    タグの更新後、**queryTwins** 関数を呼び出します。

5. **AddTagsAndQuery.js** の末尾に次のコードを追加して、**queryTwins** 関数を実装します。

   ```javascript
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
   ```

    前のコードは 2 つのクエリを実行します。1 つ目では、**Redmond43** 工場にあるデバイスのデバイス ツインのみを選択し、2 つ目のクエリで携帯ネットワーク経由で接続しているデバイスのみを選択するよう絞り込みます。

    コードで **クエリ** オブジェクトが作成されると、2 番目のパラメーターで返されるドキュメントの最大数が指定されます。 **query** オブジェクトには、**nextAsTwin** メソッドを複数回呼び出してすべての結果を取得する際に使用できる **hasMoreResults** のブール型プロパティが含まれます。 **next** というメソッドは、集計クエリの結果など、デバイス ツインではない結果で使用できます。

6. 以下を使用してアプリケーションを実行します。

    ```cmd/sh
        node AddTagsAndQuery.js
    ```

   **Redmond43** にあるすべてのデバイスを照会するクエリの結果には、1 件のデバイスが表示され、携帯ネットワークを使用するデバイスに絞り込んだ結果には 0 件のデバイスが表示されます。

   ![クエリ結果で 1 つのデバイスを参照してください](media/iot-hub-node-node-twin-getstarted/service1.png)

次のセクションでは、接続情報を報告し、前のセクションのクエリの結果を変更するデバイス アプリを作成します。

## <a name="create-the-device-app"></a>デバイス アプリを作成する

このセクションでは、**myDeviceId** としてハブに接続し、デバイス ツインの報告されるプロパティに携帯ネットワークを使用しているという情報を含めるよう更新する Node.js コンソール アプリを作成します。

1. **reportconnectivity** という名前の新しい空のフォルダーを作成します。 **reportconnectivity** フォルダー内に、コマンド プロンプトで次のコマンドを使用して新しい package.json ファイルを作成します。 `--yes` パラメーターではすべての既定値を使用できます。

    ```cmd/sh
    npm init --yes
    ```

2. **reportconnectivity** フォルダーのコマンド プロンプトで、次のコマンドを実行して、**azure-iot-device** パッケージと **azure-iot-device-mqtt** パッケージをインストールします。

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. テキスト エディターを使用して、**reportconnectivity** フォルダーに新しい **ReportConnectivity.js** ファイルを作成します。

4. 次のコードを **ReportConnectivity.js** ファイルに追加します。 `{device connection string}` を、「[IoT ハブに新しいデバイスを登録する](#register-a-new-device-in-the-iot-hub)」で **myDeviceId** デバイス ID を作成するときにコピーしておいたデバイス接続文字列に置き換えます。

    ```javascript
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
    ```

    **Client** オブジェクトに、デバイスからデバイス ツインとやりとりするのに必要なすべてのメソッドが表示されます。 前のコードでは、**Client** オブジェクトを初期化した後、**myDeviceId** のデバイス ツインを取得して、報告されるプロパティに接続情報を含めるよう更新します。

5. デバイス アプリを実行する

    ```cmd/sh
        node ReportConnectivity.js
    ```

    `twin state reported` というメッセージが表示されます。

6. これで、デバイスが接続情報を報告したため、両方のクエリで表示されるようになります。 **addtagsandqueryapp** フォルダーに戻り、クエリを再度実行します。

    ```cmd/sh
        node AddTagsAndQuery.js
    ```

    今回は、**myDeviceId** が両方のクエリ結果に表示されるはずです。

    ![両方のクエリ結果で myDeviceId を表示します](media/iot-hub-node-node-twin-getstarted/service2.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Portal で新しい IoT Hub を構成し、IoT Hub の ID レジストリにデバイス ID を作成しました。 バックエンド アプリからデバイスのメタデータをタグとして追加し、シミュレート対象デバイス アプリでデバイス ツインのデバイスの接続情報を報告するよう記述しました。 さらに、SQL に似た IoT Hub クエリ言語を使用してこの情報を照会する方法も学習しました。

詳細については、次のリソースをご覧ください。

* [IoT Hub の概要](quickstart-send-telemetry-node.md)に関するチュートリアルでデバイスからテレメトリを送信する。

* デバイス ツインの必要なプロパティを使用してデバイスを構成する: [必要なプロパティを使用してデバイスを構成する](tutorial-device-twins.md)方法のチュートリアル、

* [ダイレクト メソッドの使用](quickstart-control-device-node.md)に関するチュートリアルで、デバイスを対話形式で制御する (ユーザー制御アプリからファンをオンにするなど)。
