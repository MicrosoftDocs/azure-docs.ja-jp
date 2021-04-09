---
title: クイックスタート - Azure IoT Hub からのデバイスの制御に関するクイックスタート (Android) | Microsoft Docs
description: このクイック スタートでは、2 つのサンプル Java アプリケーションを実行します。 1 つは、ハブに接続されたデバイスをリモートで制御できるサービス アプリケーションです。 もう 1 つのアプリケーションは、リモートで制御可能な、ハブに接続された物理デバイスまたはシミュレートされたデバイス上で実行されます。
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- devx-track-java
- devx-track-azurecli
ms.date: 06/21/2019
ms.author: wesmc
ms.openlocfilehash: 361317729ecf868465d498cf3fcedd45d8c979db
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102201007"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-android"></a>クイック スタート:IoT Hub に接続されたデバイスを制御する (Android)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

このクイックスタートでは、ダイレクト メソッドを使って、Azure IoT Hub に接続されているシミュレートされたデバイスを制御します。 IoT Hub は、クラウドから IoT デバイスを管理し、大量のデバイス テレメトリを格納または処理のためにクラウドに取り込むことができるようにする Azure サービスです。 ダイレクト メソッドを使うと、IoT Hub に接続されたデバイスの動作をリモートで変更できます。 このクイックスタートでは、2 つのアプリケーションを使用します。バックエンド サービス アプリケーションから呼び出されたダイレクト メソッドに応答するシミュレートされたデバイスのアプリケーションと、Android デバイスのダイレクト メソッドを呼び出すサービス アプリケーションです。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

* [Android Studio と Android SDK 27](https://developer.android.com/studio/)。 詳細については、「[Android Studio のインストール](https://developer.android.com/studio/install)」を参照してください。

* [Git](https://git-scm.com/download/).

* [Device SDK のサンプル Android アプリケーション](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample)。[Azure IoT サンプル (Java)](https://github.com/Azure-Samples/azure-iot-samples-java) に含まれています。

* [Service SDK のサンプル Android アプリケーション](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/service/AndroidSample)。Azure IoT サンプル (Java) に含まれています。

* ファイアウォールでポート 8883 が開放されていること。 このクイックスタートのデバイス サンプルでは、ポート 8883 を介して通信する MQTT プロトコルを使用しています。 このポートは、企業や教育用のネットワーク環境によってはブロックされている場合があります。 この問題の詳細と対処方法については、「[IoT Hub への接続 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)」を参照してください。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT Hub の作成

前出の[デバイスから IoT Hub への利用統計情報の送信に関するクイック スタート](quickstart-send-telemetry-android.md)を完了した場合は、この手順を省略して、既に作成した IoT Hub を使用できます。

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>デバイスの登録

前出の[デバイスから IoT Hub への利用統計情報の送信に関するクイック スタート](quickstart-send-telemetry-android.md)を完了した場合は、この手順を省略して、前のクイック スタートで登録したものと同じデバイスを使用します。

デバイスを IoT Hub に接続するには、あらかじめ IoT Hub に登録しておく必要があります。 このクイック スタートでは、Azure Cloud Shell を使用して、シミュレートされたデバイスを登録します。

1. Azure Cloud Shell で次のコマンドを実行してデバイス ID を作成します。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT Hub に対して選んだ名前に置き換えてください。

   **MyAndroidDevice**: これは、登録するデバイスの名前です。 示されているように、**MyAndroidDevice** を使用することをお勧めします。 デバイスに別の名前を選択した場合は、この記事全体でその名前を使用する必要があります。また、サンプル アプリケーションを実行する前に、アプリケーション内のデバイス名を更新してください。

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Azure Cloud Shell で次のコマンドを実行して、登録したデバイスの "_デバイス接続文字列_" を取得します。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT Hub に対して選んだ名前に置き換えてください。

    ```azurecli-interactive
    az iot hub device-identity connection-string show\
      --hub-name {YourIoTHubName} \
      --device-id MyAndroidDevice \
      --output table
    ```

    次のようなデバイス接続文字列をメモしておきます。

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    この値は、このクイック スタートの後の方で使います。

## <a name="retrieve-the-service-connection-string"></a>サービス接続文字列を取得する

また、バックエンド サービス アプリケーションが IoT Hub に接続してメソッドを実行したりメッセージを取得したりできるようにするには、"_サービス接続文字列_" が必要です。 次のコマンドを実行すると、IoT Hub のサービス接続文字列が取得されます。

**YourIoTHubName**: このプレースホルダーは、実際の IoT Hub に対して選んだ名前に置き換えてください。

```azurecli-interactive
az iot hub connection-string show --policy-name service --name {YourIoTHubName} --output table
```

次のようなサービス接続文字列をメモしておきます。

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

この値は、このクイック スタートの後の方で使います。 このサービス接続文字列は、前の手順でメモしたデバイス接続文字列とは異なります。

## <a name="listen-for-direct-method-calls"></a>ダイレクト メソッドの呼び出しをリッスンする

このクイックスタートでは、どちらのサンプルも、GitHub 上の azure-iot-samples-java リポジトリに含まれています。 [azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java) リポジトリをダウンロードまたは複製してください。

このデバイス SDK サンプル アプリケーションは、物理 Android デバイスまたは Android Emulator 上で実行することができます。 このサンプルは、IoT Hub 上のデバイス固有エンドポイントに接続し、シミュレートされた利用統計情報を送信して、ハブからのダイレクト メソッド呼び出しをリッスンします。 このクイック スタートでは、ハブからのダイレクト メソッド呼び出しは、利用統計情報の送信間隔を変更するようデバイスに指示します。 シミュレートされたデバイスでは、ダイレクト メソッドを実行した後、ハブに受信確認が返送されます。

1. GitHub のサンプル Android プロジェクトを Android Studio で開きます。 このプロジェクトは、複製またはダウンロードした [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java) リポジトリのコピーの次のディレクトリにあります: *\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample*

2. Android Studio でサンプル プロジェクトの *gradle.properties* を開き、**Device_Connection_String** プレースホルダーを、先ほどメモした自分のデバイスの接続文字列に置き換えます。

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. Android Studio で、 **[File]\(ファイル\)**  >  **[Sync Project with Gradle Files]\(プロジェクトを Gradle ファイルと同期\)** の順にクリックします。 ビルドが完了したことを確認します。

   > [!NOTE]
   > プロジェクトの同期に失敗した場合、次のいずれかの理由が原因として考えられます。
   >
   > * プロジェクトで参照されている Android Gradle プラグインと Gradle のバージョンが、ご使用の Android Studio のバージョンに対して古い。 [こちらの手順](https://developer.android.com/studio/releases/gradle-plugin)に従って、実際の環境に合った正しいバージョンのプラグインと Gradle を参照、インストールしてください。
   > * Android SDK のライセンス契約に署名していない。 ビルド出力に書かれている手順に従ってライセンス契約に署名し、SDK をダウンロードしてください。

4. ビルドが完了したら、 **[Run]\(実行\)**  >  **[Run 'app']\(<アプリ> の実行\)** をクリックします。 物理 Android デバイスまたは Android Emulator 上で実行されるようにアプリを構成します。 物理デバイスまたはエミュレーター上での Android アプリの実行について詳しくは、「[アプリを実行する](https://developer.android.com/training/basics/firstapp/running-app)」を参照してください。

5. アプリが読み込まれたら、 **[Start]\(開始\)** ボタンをクリックして、IoT Hub への利用統計情報の送信を開始します。

    ![クライアント デバイスの Android アプリのサンプル スクリーンショット](media/quickstart-control-device-android/sample-screenshot.png)

利用統計情報の送信間隔を実行時に更新するためには、サービス SDK サンプルを実行している間、物理デバイスまたはエミュレーター上でこのアプリを実行状態にしておく必要があります。

## <a name="read-the-telemetry-from-your-hub"></a>Hub からテレメトリを読み取る

このセクションでは、[IoT 拡張機能](/cli/azure/ext/azure-iot/iot)と共に Azure Cloud Shell を使用して、Android デバイスから送信されるメッセージを監視します。

1. Azure Cloud Shell を使用して、次のコマンドを実行して接続し、お使いの IoT Hub からのメッセージを読み取ります。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT Hub に対して選んだ名前に置き換えてください。

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    次のスクリーンショットは、Android デバイスから送信された利用統計情報を IoT Hub が受信しているときの出力を示しています。

      ![Azure CLI を使用してデバイス メッセージを読み取る](media/quickstart-control-device-android/read-data.png)

既定では、テレメトリ アプリによって Android デバイスから 5 秒おきに利用統計情報が送信されています。 次のセクションでは、ダイレクト メソッド呼び出しを使用して、Android IoT デバイスの利用統計情報の送信間隔を更新します。

## <a name="call-the-direct-method"></a>ダイレクト メソッドを呼び出す

サービス アプリケーションは、IoT Hub 上のサービス側エンドポイントに接続します。 アプリケーションにより、IoT Hub を通してデバイスへのダイレクト メソッド呼び出しが行われた後、受信確認がリッスンされます。

このアプリは、単独の物理 Android デバイスまたは Android Emulator 上で実行します。

通常、IoT Hub のバックエンド サービス アプリケーションはクラウドで実行されます。クラウドの方が、IoT Hub 上のあらゆるデバイスを制御する、慎重な扱いを要する接続文字列に伴うリスクを容易に軽減できます。 この例では、デモンストレーションの目的に限って、Android アプリとして実行しています。 このクイックスタートの他の言語のバージョンでは、より典型的なバックエンド サービス アプリケーションに即した例が紹介されています。

1. このサービスの GitHub サンプル Android プロジェクトを Android Studio で開きます。 このプロジェクトは、複製またはダウンロードした [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java) リポジトリのコピーの次のディレクトリにあります: *\azure-iot-samples-java\iot-hub\Samples\service\AndroidSample*

2. Android Studio で、サンプル プロジェクトの *gradle.properties* を開きます。 **ConnectionString** プロパティと **DeviceId** プロパティの値を、先ほどメモしたご自分のサービスの接続文字列と登録済みの Android デバイス ID に更新します。

    ```
    ConnectionString=HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}
    DeviceId=MyAndroidDevice
    ```

3. Android Studio で、 **[File]\(ファイル\)**  >  **[Sync Project with Gradle Files]\(プロジェクトを Gradle ファイルと同期\)** の順にクリックします。 ビルドが完了したことを確認します。

   > [!NOTE]
   > プロジェクトの同期に失敗した場合、次のいずれかの理由が原因として考えられます。
   >
   > * プロジェクトで参照されている Android Gradle プラグインと Gradle のバージョンが、ご使用の Android Studio のバージョンに対して古い。 [こちらの手順](https://developer.android.com/studio/releases/gradle-plugin)に従って、実際の環境に合った正しいバージョンのプラグインと Gradle を参照、インストールしてください。
   > * Android SDK のライセンス契約に署名していない。 ビルド出力に書かれている手順に従ってライセンス契約に署名し、SDK をダウンロードしてください。

4. ビルドが完了したら、 **[Run]\(実行\)**  >  **[Run 'app']\(<アプリ> の実行\)** をクリックします。 単独の物理 Android デバイスまたは Android Emulator 上で実行されるようにアプリを構成します。 物理デバイスまたはエミュレーター上での Android アプリの実行について詳しくは、「[アプリを実行する](https://developer.android.com/training/basics/firstapp/running-app)」を参照してください。

5. アプリの読み込み後、 **[Set Messaging Interval]\(メッセージ送信間隔の設定\)** の値を **1000** に更新し、 **[Invoke]\(呼び出し\)** をクリックします。

    利用統計情報メッセージの送信間隔はミリ秒単位です。 このデバイス サンプルでは、利用統計情報の送信間隔が既定で 5 秒に設定されています。 この変更によって、利用統計情報が 1 秒おきに送信されるように Android IoT デバイスが更新されます。

    ![利用統計情報の送信間隔を入力](media/quickstart-control-device-android/enter-telemetry-interval.png)

6. メソッドが正常に実行されたかどうかを示す受信確認がアプリに届きます。

    ![ダイレクト メソッドの受信確認](media/quickstart-control-device-android/direct-method-ack.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、バックエンド アプリケーションからデバイス上のダイレクト メソッドを呼び出し、シミュレートされたデバイス アプリケーションでダイレクト メソッド呼び出しに応答しました。

デバイスからクラウドへのメッセージをクラウド内の異なる宛先にルーティングする方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [チュートリアル:処理のために利用統計情報を異なるエンドポイントにルーティングする](tutorial-routing.md)
