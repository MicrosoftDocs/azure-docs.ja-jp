---
title: クイック スタート:Java を使用して Azure IoT Hub に利用統計情報を送信する
description: このクイック スタートでは、2 つのサンプル Java アプリケーションを実行して、IoT ハブにシミュレートされた利用統計情報を送信し、クラウドで処理するために IoT ハブから利用統計情報を読み取ります。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc, seo-java-august2019, seo-java-september2019
ms.date: 06/21/2019
ms.openlocfilehash: cb115b8658850fc85f93fc7a9508a82ecee920d8
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166439"
---
# <a name="quickstart-send-telemetry-to-an-azure-iot-hub-and-read-it-with-a-java-application"></a>クイック スタート:Azure IoT Hub にテレメトリを送信して Java アプリケーションで読み取る

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

このクイックスタートでは、Azure IoT Hub にテレメトリを送信して Java アプリケーションで読み取る方法について説明します。 IoT Hub は、保管や処理のために IoT デバイスから大量の利用統計情報をクラウドに取り込むことを可能にする Azure サービスです。 このクイック スタートでは、シミュレートされたデバイス アプリケーションから、IoT ハブを経由して、処理のためのバックエンド アプリケーションに、利用統計情報を送信します。

このクイック スタートでは、あらかじめ作成されている 2 つの Java アプリケーションを使います。1 つは利用統計情報を送信し、もう 1 つはハブから利用統計情報を読み取ります。 これら 2 つのアプリケーションを実行する前に、IoT ハブを作成し、デバイスをハブに登録します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

このクイック スタートで実行する 2 つのサンプル アプリケーションは、Java を使って書かれています。 開発用マシン上に Java SE 8 が必要です。

複数のプラットフォーム向けの Java SE Development Kit 8 を「[Azure および Azure Stack の Java 長期サポート](https://docs.microsoft.com/en-us/java/azure/jdk/?view=azure-java-stable)」からダウンロードできます。 JDK 8 のダウンロードを利用するには、「**長期サポート**」の「**Java 8**」を選択します。

開発コンピューターに現在インストールされている Java のバージョンは、次のコマンドを使って確認できます。

```cmd/sh
java -version
```

サンプルをビルドするには、Maven 3 をインストールする必要があります。 複数のプラットフォームに対応する Maven を [Apache Maven](https://maven.apache.org/download.cgi) からダウンロードできます。

開発コンピューターに現在インストールされている Maven のバージョンは、次のコマンドを使って確認できます。

```cmd/sh
mvn --version
```

次のコマンドを実行して、Microsoft Azure IoT Extension for Azure CLI を Cloud Shell インスタンスに追加します。 IoT Hub、IoT Edge、IoT Device Provisioning Service (DPS) 固有のコマンドが Azure CLI に追加されます。

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

[https://github.com/Azure-Samples/azure-iot-samples-java/archive/master.zip](https://github.com/Azure-Samples/azure-iot-samples-java/archive/master.zip ) からサンプル Java プロジェクトをダウンロードし、ZIP アーカイブを抽出します。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>デバイスの登録

デバイスを IoT ハブに接続するには、あらかじめ IoT ハブに登録しておく必要があります。 このクイック スタートでは、Azure Cloud Shell を使用して、シミュレートされたデバイスを登録します。

1. Azure Cloud Shell で次のコマンドを実行してデバイス ID を作成します。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

   **MyJavaDevice**: これは、登録するデバイスの名前です。 示されているように、**MyJavaDevice** を使用することをお勧めします。 デバイスに別の名前を選択した場合は、この記事全体でその名前を使用する必要があります。また、サンプル アプリケーションを実行する前に、アプリケーション内のデバイス名を更新してください。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyJavaDevice
    ```

2. Azure Cloud Shell で次のコマンドを実行して、登録したデバイスの "_デバイス接続文字列_" を取得します。

    **YourIoTHubName**: このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyJavaDevice --output table
    ```

    次のようなデバイス接続文字列をメモしておきます。

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyJavaDevice;SharedAccessKey={YourSharedAccessKey}`

    この値は、このクイックスタートの後の方で使用します。

3. また、バックエンド アプリケーションが IoT ハブに接続してメッセージを取得できるようにするには、IoT ハブの "_Event Hubs 互換エンドポイント_"、"_Event Hubs 互換パス_"、および "_サービス主キー_" も必要です。 次のコマンドは、お使いの IoT ハブに対するこれらの値を取得します。

     **YourIoTHubName**: このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    クイックスタートの後の方で使うので、これら 3 つの値をメモしておきます。

## <a name="send-simulated-telemetry"></a>シミュレートされた利用統計情報の送信

シミュレートされたデバイス アプリケーションは、IoT ハブ上のデバイスに固有のエンドポイントに接続し、シミュレートされた温度と湿度の利用統計情報を送信します。

1. ローカル ターミナル ウィンドウで、サンプルの Java プロジェクトのルート フォルダーに移動します。 **iot-hub\Quickstarts\simulated-device** フォルダーに移動します。

2. 適当なテキスト エディターで **src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java** ファイルを開きます。

    `connString` 変数の値を、前にメモしたデバイス接続文字列に置き換えます。 その後、変更を **SimulatedDevice.java** に保存します。

3. ローカル ターミナル ウィンドウで次のコマンドを実行して、必要なライブラリをインストールし、シミュレートされたデバイス アプリケーションをビルドします。

    ```cmd/sh
    mvn clean package
    ```

4. ローカル ターミナル ウィンドウで次のコマンドを実行して、シミュレートされたデバイス アプリケーションを実行します。

    ```cmd/sh
    java -jar target/simulated-device-1.0.0-with-deps.jar
    ```

    次のスクリーンショットは、シミュレートされたデバイス アプリケーションが IoT ハブに利用統計情報を送信したときの出力を示しています。

    ![デバイスによって対象の IoT ハブに送信された利用統計情報からの出力](media/quickstart-send-telemetry-java/iot-hub-simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>ハブから利用統計情報を読み取る

バックエンド アプリケーションは、IoT ハブ上のサービス側 **Events** エンドポイントに接続します。 このアプリケーションは、シミュレートされたデバイスから送信されたデバイスとクラウドの間のメッセージを受信します。 通常、IoT Hub のバックエンド アプリケーションはクラウド内で実行され、デバイスとクラウドの間のメッセージ受信して処理します。

1. 別のローカル ターミナル ウィンドウで、サンプルの Java プロジェクトのルート フォルダーに移動します。 **iot-hub\Quickstarts\read-d2c-messages** フォルダーに移動します。

2. 適当なテキスト エディターで **src/main/java/com/microsoft/docs/iothub/samples/ReadDeviceToCloudMessages.java** ファイルを開きます。 次の変数を更新し、ご自身の変更をファイルに保存します。

    | 変数 | 値 |
    | -------- | ----------- |
    | `eventHubsCompatibleEndpoint` | 変数の値を、前にメモした Event Hubs 互換エンドポイントに置き換えます。 |
    | `eventHubsCompatiblePath`     | 変数の値を、前にメモした Event Hubs 互換パスに置き換えます。 |
    | `iotHubSasKey`                | 変数の値を、前にメモしたサービス主キーに置き換えます。 |

3. ローカル ターミナル ウィンドウで次のコマンドを実行して、必要なライブラリをインストールし、バックエンド アプリケーションをビルドします。

    ```cmd/sh
    mvn clean package
    ```

4. ローカル ターミナル ウィンドウで次のコマンドを実行して、バックエンド アプリケーションを実行します。

    ```cmd/sh
    java -jar target/read-d2c-messages-1.0.0-with-deps.jar
    ```

    次のスクリーンショットは、シミュレートされたデバイスがハブに送信した利用統計情報をバックエンド アプリケーションが受信したときの出力を示しています。

    ![IoT hub に送信された利用統計情報をバックエンド アプリケーションが受信したときの出力](media/quickstart-send-telemetry-java/iot-hub-read-device-to-cloud.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

このクイックスタートでは、IoT ハブを設定し、デバイスを登録し、Java アプリケーションを使用してシミュレートされた利用統計情報をハブに送信し、簡単なバックエンド アプリケーションを使用してハブから利用統計情報を読み取りました。

バックエンド アプリケーションからシミュレートされたデバイスを制御する方法を学習するには、次のクイック スタートに進んでください。

> [!div class="nextstepaction"]
> [クイック スタート: IoT ハブに接続されたデバイスを制御する](quickstart-control-device-java.md)
