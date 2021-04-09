---
title: Azure IoT Hub モジュール ID とモジュール ツイン (Node.js) の開始
description: モジュール ID を作成し、IoT SDK for Node.js を使用して .NET のモジュール ツインを更新する方法を説明します。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/26/2018
ms.custom: amqp, devx-track-js
ms.openlocfilehash: 9de3f45a9d62a9d131583c133440ac8a311a468d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95993250"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-nodejs"></a>IoT Hub モジュール ID とモジュール ツイン (Node.js) の概要

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [モジュール ID とモジュール ツイン](iot-hub-devguide-module-twins.md)は Azure IoT Hub のデバイス ID とデバイス ツインに類似していますが、より細かい粒度で設定できます。 Azure IoT Hub のデバイス ID とデバイス ツインを使用した場合、バックエンド アプリケーションからデバイスを構成し、デバイスの状態を可視化できるのに対し、モジュール ID とモジュール ツインでは、デバイスの各コンポーネントごとにこれらの機能を実現できます。 複数のコンポーネントで構成され、この機能をサポートしているデバイス (オペレーティング システム ベースのデバイスやファームウェア デバイスなど) であれば、各コンポーネントの状態を可視化し、個別に構成することができます。

このチュートリアルを完了すると、次の 2 つの Node.js アプリが完成します。

* **CreateIdentities** は、デバイスの ID、モジュール ID と、関連づけられたセキュリティ キーを作成してデバイスおよびモジュール クライアントと接続します。

* **UpdateModuleTwinReportedProperties** は、更新されたモジュール ツインによって報告されたプロパティを IoT Hub に送信します。

> [!NOTE]
> デバイス上で動作するアプリケーションの作成とソリューションのバックエンドで動作するアプリケーションの開発に利用できる各種 Azure IoT SDK については、「[Azure IoT SDK](iot-hub-devguide-sdks.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Node.js バージョン 10.0.x 以降。 「[Prepare your development environment (開発環境を準備する)](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md)」では、このチュートリアルのために Node.js を Windows または Linux にインストールする方法が説明されています。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 (アカウントがない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を数分で作成できます)。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>IoT ハブ接続文字列を取得する

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>IoT Hub でデバイス ID とモジュール ID を作成する

このセクションでは、IoT ハブの ID レジストリにデバイス ID とモジュール ID を作成する Node.js アプリを作成します。 IoT ハブに接続するデバイスまたはモジュールは、あらかじめ ID レジストリに登録されている必要があります。 詳しくは、[IoT Hub 開発者ガイド](iot-hub-devguide-identity-registry.md)の "ID レジストリ" に関するセクションをご覧ください このコンソール アプリを実行すると、デバイスとモジュール両方に対して一意のデバイス ID とキーが生成されます。 デバイスとモジュールは、IoT ハブに device-to-cloud メッセージを送信するときにこれらの値を使用して自分自身を識別します。 ID には大文字と小文字の区別があります。

1. コードを保持するためのディレクトリを作成します。

2. そのディレクトリ内で最初に **npm init -y** を実行して、既定値で空の package.json を作成します。 これは、コードのプロジェクト ファイルです。

3. **npm install -S azure-iothub\@modules-preview** を実行して、**node_modules** サブディレクトリ内にサービス SDK をインストールします。

    > [!NOTE]
    > サブディレクトリ名 node_modules では、"ノード ライブラリ" を意味する用語モジュールを使用します。 ここで言う用語は IoT Hub モジュールとは関係ありません。

4. ディレクトリ内に次の .js ファイルを作成します。 これを **add.js** と呼びます。 ハブの接続文字列とハブ名をコピーして貼り付けます。

    ```javascript
    var Registry = require('azure-iothub').Registry;
    var uuid = require('uuid');
    // Copy/paste your connection string and hub name here
    var serviceConnectionString = '<hub connection string from portal>';
    var hubName = '<hub name>.azure-devices.net';
    // Create an instance of the IoTHub registry
    var registry = Registry.fromConnectionString(serviceConnectionString);
    // Insert your device ID and moduleId here.
    var deviceId = 'myFirstDevice';
    var moduleId = 'myFirstModule';
    // Create your device as a SAS authentication device
    var primaryKey = new Buffer(uuid.v4()).toString('base64');
    var secondaryKey = new Buffer(uuid.v4()).toString('base64');
    var deviceDescription = {
      deviceId: deviceId,
      status: 'enabled',
      authentication: {
        type: 'sas',
        symmetricKey: {
          primaryKey: primaryKey,
          secondaryKey: secondaryKey
        }
      }
    };

    // First, create a device identity
    registry.create(deviceDescription, function(err) {
      if (err) {
        console.log('Error creating device identity: ' + err);
        process.exit(1);
      }
      console.log('device connection string = "HostName=' + hubName + ';DeviceId=' + deviceId + ';SharedAccessKey=' + primaryKey + '"');

      // Then add a module to that device
      registry.addModule({ deviceId: deviceId, moduleId: moduleId }, function(err) {
        if (err) {
          console.log('Error creating module identity: ' + err);
          process.exit(1);
        }

        // Finally, retrieve the module details from the hub so we can construct the connection string
        registry.getModule(deviceId, moduleId, function(err, foundModule) {
          if (err) {
            console.log('Error getting module back from hub: ' + err);
            process.exit(1);
          }
          console.log('module connection string = "HostName=' + hubName + ';DeviceId=' + foundModule.deviceId + ';ModuleId='+foundModule.moduleId+';SharedAccessKey=' + foundModule.authentication.symmetricKey.primaryKey + '"');
          process.exit(0);
        });
      });
    });

    ```

このアプリは、ID **myFirstDevice** のデバイス ID と ID **myFirstModule** のモジュール ID をデバイス **myFirstDevice** の下に作成します。 (そのモジュール ID が既に ID レジストリに存在する場合は、単にその既存のモジュール情報を取得します。)続けてその ID のプライマリ キーが表示されます。 シミュレートされたモジュール アプリでこのキーを使用し、IoT ハブに接続します。

ノード add.js を使用してこれを実行します。 これにより、デバイス ID とモジュール ID の各接続文字列を取得できます。

> [!NOTE]
> IoT ハブの ID レジストリには、IoT ハブに対するセキュリティで保護されたアクセスを有効にするためのデバイス ID とモジュール ID のみが格納されます。 ID レジストリには、セキュリティ資格情報として使用されるデバイス ID とキーが格納されます。 ID レジストリには、そのデバイスのアクセスを無効にするために使用できる各デバイスの有効/無効フラグも格納されます。 その他デバイス固有のメタデータをアプリケーションで保存する必要がある場合は、アプリケーション固有のストアを使用する必要があります。 モジュール ID 用の有効/無効フラグはありません。 詳細については、[IoT Hub 開発者ガイド](iot-hub-devguide-identity-registry.md)をご覧ください。

## <a name="update-the-module-twin-using-nodejs-device-sdk"></a>Node.js デバイス SDK を使用してモジュール ツインを更新する

このセクションでは、モジュール ツインによって報告されるプロパティを更新する Node.js アプリをシミュレートされたデバイス上に作成します。

1. **モジュールの接続文字列を取得する** -- [Azure Portal](https://portal.azure.com/) にログインします。 IoT Hub に移動し、[IoT デバイス] をクリックします。 myFirstDevice を検索して開くと、myFirstModule が正常に作成されていることを確認できます。 モジュールの接続文字列をコピーします。 これは、次の手順で必要になります。

   ![Azure Portal モジュールの詳細](./media/iot-hub-node-node-module-twin-getstarted/module-detail.png)

2. 前述の手順で行ったように、デバイス コードのディレクトリを作成し、NPM を使用してこれを初期化して、デバイス SDK (**npm install -S azure-iot-device-amqp\@modules-preview**) をインストールします。

   > [!NOTE]
   > npm install コマンドが遅くなったように感じる場合があります。 パッケージ リポジトリから多数のコードがプルダウンされます。

   > [!NOTE]
   > json の解析中に npm ERR! レジストリ エラーが発生したというエラーが表示されたら、 そのエラーは無視できます。 json の解析中に npm ERR! レジストリ エラーが発生したというエラーが表示されたら、 そのエラーは無視できます。

3. twin.js という名前でファイルを作成します。 モジュールの ID 文字列をコピーして貼り付けます。

    ```javascript
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-amqp').Amqp;
    // Copy/paste your module connection string here.
    var connectionString = '<insert module connection string here>';
    // Create a client using the Amqp protocol.
    var client = Client.fromConnectionString(connectionString, Protocol);
    client.on('error', function (err) {
      console.error(err.message);
    });
    // connect to the hub
    client.open(function(err) {
      if (err) {
        console.error('error connecting to hub: ' + err);
        process.exit(1);
      }
      console.log('client opened');
    // Create device Twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('error getting twin: ' + err);
          process.exit(1);
        }
        // Output the current properties
        console.log('twin contents:');
        console.log(twin.properties);
        // Add a handler for desired property changes
        twin.on('properties.desired', function(delta) {
            console.log('new desired properties received:');
            console.log(JSON.stringify(delta));
        });
        // create a patch to send to the hub
        var patch = {
          updateTime: new Date().toString(),
          firmwareVersion:'1.2.1',
          weather:{
            temperature: 72,
            humidity: 17
          }
        };
        // send the patch
        twin.properties.reported.update(patch, function(err) {
          if (err) throw err;
          console.log('twin state reported');
        });
      });
    });
    ```

4. 次に、コマンド **node twin.js** を使用してこれを実行します。

   ```cmd/sh
   F:\temp\module_twin>node twin.js
   ```

   すると、次のように表示されます。

   ```console
   client opened
   twin contents:
   { reported: { update: [Function: update], '$version': 1 },
     desired: { '$version': 1 } }
   new desired properties received:
   {"$version":1}
   twin state reported
   ```

## <a name="next-steps"></a>次のステップ

引き続き IoT Hub の使用方法を確認すると共に、他の IoT のシナリオについて調べるには、次のページを参照してください。

* [デバイス管理の概要](iot-hub-node-node-device-management-get-started.md)

* [IoT Edge の概要](../iot-edge/quickstart-linux.md)