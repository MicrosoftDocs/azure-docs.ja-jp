---
title: 'クイック スタート: Azure IoT に利用統計情報を送信する (Node.js)'
description: このクイック スタートでは、2 つのサンプル Node.js アプリケーションを実行して、IoT ハブにシミュレートされた利用統計情報を送信し、クラウドで処理するために IoT ハブから利用統計情報を読み取ります。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019
ms.date: 06/21/2019
ms.openlocfilehash: 859bb580f5fa974eec70c120297f094247fa2a9b
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70967171"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-nodejs"></a>クイック スタート:デバイスから IoT ハブに利用統計情報を送信してバックエンド アプリケーションで読み取る (Node.js)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub は、保管や処理のために IoT デバイスから大量の利用統計情報をクラウドに取り込むことを可能にする Azure サービスです。 このクイック スタートでは、シミュレートされたデバイス アプリケーションから、IoT ハブを経由して、処理のためのバックエンド アプリケーションに、利用統計情報を送信します。

このクイック スタートでは、あらかじめ作成されている 2 つの Node.js アプリケーションを使います。1 つは利用統計情報を送信し、もう 1 つはハブから利用統計情報を読み取ります。 これら 2 つのアプリケーションを実行する前に、IoT ハブを作成し、デバイスをハブに登録します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

このクイック スタートで実行する 2 つのサンプル アプリケーションは、Node.js で書かれています。 開発用コンピューター上に Node.js v10.x.x 以降が必要です。

複数のプラットフォームに対応する Node.js を [nodejs.org](https://nodejs.org) からダウンロードできます。

開発コンピューターに現在インストールされている Node.js のバージョンは、次のコマンドを使って確認できます。

```cmd/sh
node --version
```

次のコマンドを実行して、Microsoft Azure IoT Extension for Azure CLI を Cloud Shell インスタンスに追加します。 IoT Hub、IoT Edge、IoT Device Provisioning Service (DPS) 固有のコマンドが Azure CLI に追加されます。

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

[https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip](https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip ) からサンプル Node.js プロジェクトをダウンロードし、ZIP アーカイブを抽出します。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>デバイスの登録

デバイスを IoT ハブに接続するには、あらかじめ IoT ハブに登録しておく必要があります。 このクイック スタートでは、Azure Cloud Shell を使用して、シミュレートされたデバイスを登録します。

1. Azure Cloud Shell で次のコマンドを実行してデバイス ID を作成します。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

   **MyNodeDevice**: 登録するデバイスの名前。 示されているように、**MyNodeDevice** を使用します。 デバイスに別の名前を選択した場合は、この記事全体でその名前を使用し、サンプル アプリケーションを実行する前に、アプリケーション内のデバイス名を更新する必要があります。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```

1. Azure Cloud Shell で次のコマンドを実行して、登録したデバイスの "_デバイス接続文字列_" を取得します。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    次のようなデバイス接続文字列をメモしておきます。

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    この値は、このクイック スタートの後の方で使います。

1. また、バックエンド アプリケーションが IoT ハブに接続してメッセージを取得できるようにするには、"_サービス接続文字列_" が必要です。 次のコマンドを実行すると、IoT ハブのサービス接続文字列が取得されます。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

    ```azurecli-interactive
    az iot hub show-connection-string --name YourIoTHubName --policy-name service --output table
    ```

    次のようなサービス接続文字列をメモしておきます。

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

    この値は、このクイック スタートの後の方で使います。 サービス接続文字列はデバイス接続文字列とは異なります。

## <a name="send-simulated-telemetry"></a>シミュレートされた利用統計情報の送信

シミュレートされたデバイス アプリケーションは、IoT ハブ上のデバイスに固有のエンドポイントに接続し、シミュレートされた温度と湿度の利用統計情報を送信します。

1. ローカル ターミナル ウィンドウを開いて、サンプルの Node.js プロジェクトのルート フォルダーに移動します。 **iot-hub\Quickstarts\simulated-device** フォルダーに移動します。

1. 適当なテキスト エディターで **SimulatedDevice.js** ファイルを開きます。

    `connectionString` 変数の値を、前にメモしたデバイス接続文字列に置き換えます。 その後、変更を **SimulatedDevice.js** ファイルに保存します。

1. ローカル ターミナル ウィンドウで次のコマンドを実行して、必要なライブラリをインストールし、シミュレートされたデバイス アプリケーションを実行します。

    ```cmd/sh
    npm install
    node SimulatedDevice.js
    ```

    次のスクリーンショットは、シミュレートされたデバイス アプリケーションが IoT ハブに利用統計情報を送信したときの出力を示しています。

    ![シミュレートされたデバイスを実行する](media/quickstart-send-telemetry-node/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>ハブから利用統計情報を読み取る

バックエンド アプリケーションは、IoT ハブ上のサービス側 **Events** エンドポイントに接続します。 このアプリケーションは、シミュレートされたデバイスから送信されたデバイスとクラウドの間のメッセージを受信します。 通常、IoT Hub のバックエンド アプリケーションはクラウド内で実行され、デバイスとクラウドの間のメッセージ受信して処理します。

1. 別のローカル ターミナル ウィンドウを開いて、サンプルの Node.js プロジェクトのルート フォルダーに移動します。 **iot-hub\Quickstarts\read-d2c-messages** フォルダーに移動します。

1. 適当なテキスト エディターで **ReadDeviceToCloudMessages.js** ファイルを開きます。

    `connectionString` 変数の値を、前にメモしたサービス接続文字列に置き換えます。 **ReadDeviceToCloudMessages.js** ファイルに変更を保存します。

1. ローカル ターミナル ウィンドウで次のコマンドを実行して、必要なライブラリをインストールし、バックエンド アプリケーションを実行します。

    ```cmd/sh
    npm install
    node ReadDeviceToCloudMessages.js
    ```

    次のスクリーンショットは、シミュレートされたデバイスがハブに送信した利用統計情報をバックエンド アプリケーションが受信したときの出力を示しています。

    ![バックエンド アプリケーションを実行する](media/quickstart-send-telemetry-node/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

このクイック スタートでは、IoT ハブをセットアップし、デバイスを登録し、Node.js アプリケーションを使ってハブにシミュレートされた利用統計情報を送信し、簡単なバックエンド アプリケーションを使ってハブから利用統計情報を読み取りました。

バックエンド アプリケーションからシミュレートされたデバイスを制御する方法を学習するには、次のクイック スタートに進んでください。

> [!div class="nextstepaction"]
> [クイック スタート: IoT ハブに接続されたデバイスを制御する](quickstart-control-device-node.md)
