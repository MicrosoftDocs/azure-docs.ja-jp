---
title: Azure IoT Hub からのデバイスの制御に関するクイック スタート (Android) | Microsoft Docs
description: このクイック スタートでは、2 つのサンプル Java アプリケーションを実行します。 1 つは、ハブに接続されたデバイスをリモートで制御できるサービス アプリケーションです。 もう 1 つのアプリケーションは、リモートで制御可能な、ハブに接続された物理デバイスまたはシミュレートされたデバイス上で実行されます。
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/15/2019
ms.author: wesmc
ms.openlocfilehash: e3b0c0703cb46087db38121055117b50f97ad03f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59006576"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-android"></a>クイック スタート:IoT ハブに接続されたデバイスを制御する (Android)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub は、IoT デバイスからクラウドに大量の利用統計情報を取り込み、クラウドからデバイスを管理することができる、Azure サービスです。 このクイック スタートでは、"*ダイレクト メソッド*" を使って、IoT ハブに接続されているシミュレートされたデバイスを制御します。 ダイレクト メソッドを使うと、IoT ハブに接続されたデバイスの動作をリモートで変更できます。

このクイック スタートでは、あらかじめ作成されている次の 2 つの Java アプリケーションを使います。

* バックエンド サービス アプリケーションから呼び出されたダイレクト メソッドに応答するシミュレートされたデバイスのアプリケーション。 ダイレクト メソッドの呼び出しを受け取るため、このアプリケーションは IoT ハブ上のデバイス固有のエンドポイントに接続します。

* Android デバイス上でダイレクト メソッドを呼び出すサービス アプリケーション。 デバイスでダイレクト メソッドを呼び出すため、このアプリケーションは IoT ハブ上のサービス側エンドポイントに接続します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

* Android Studio (https://developer.android.com/studio/)。 Android Studio のインストールの詳細については、[Android のインストール](https://developer.android.com/studio/install)に関するページを参照してください。

* この記事のサンプルでは、Android SDK 27 を使用しています。

* 次のコマンドを実行して、Microsoft Azure IoT Extension for Azure CLI を Cloud Shell インスタンスに追加します。 IoT Hub、IoT Edge、IoT Device Provisioning Service (DPS) 固有のコマンドが Azure CLI に追加されます。

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

* このクイック スタートでは、2 つのサンプル アプリケーションが必要です ([Device SDK サンプル Android アプリケーション](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample)と [Service SDK サンプル Android アプリケーション](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/service/AndroidSample))。 どちらのサンプルも、GitHub 上の azure-iot-samples-java リポジトリに含まれています。 [azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java) リポジトリをダウンロードまたは複製してください。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

前出の[デバイスから IoT ハブへの利用統計情報の送信に関するクイック スタート](quickstart-send-telemetry-android.md)を完了した場合は、この手順を省略して、既に作成した IoT ハブを使用できます。

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>デバイスの登録

前出の[デバイスから IoT ハブへの利用統計情報の送信に関するクイック スタート](quickstart-send-telemetry-android.md)を完了した場合は、この手順を省略して、前のクイック スタートで登録したものと同じデバイスを使用します。

デバイスを IoT ハブに接続するには、あらかじめ IoT ハブに登録しておく必要があります。 このクイック スタートでは、Azure Cloud Shell を使用して、シミュレートされたデバイスを登録します。

1. Azure Cloud Shell で次のコマンドを実行してデバイス ID を作成します。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

   **MyAndroidDevice**:この値は、登録済みデバイスに付けられた名前です。 示されているように、MyAndroidDevice を使用します。 デバイスに別の名前を選択した場合は、この記事全体でその名前を使用する必要があります。また、サンプル アプリケーションを実行する前に、アプリケーション内のデバイス名を更新してください。

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name YourIoTHubName --device-id MyAndroidDevice
    ```

2. Azure Cloud Shell で次のコマンドを実行して、登録したデバイスの "_デバイス接続文字列_" を取得します。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name YourIoTHubName \
      --device-id MyAndroidDevice \
      --output table
    ```

    次のようなデバイス接続文字列をメモしておきます。

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    この値は、このクイック スタートの後の方で使います。

## <a name="retrieve-the-service-connection-string"></a>サービス接続文字列を取得する

また、バックエンド サービス アプリケーションが IoT ハブに接続してメソッドを実行したりメッセージを取得したりできるようにするには、"_サービス接続文字列_" が必要です。 次のコマンドを実行すると、IoT ハブのサービス接続文字列が取得されます。

**YourIoTHubName**:このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

```azurecli-interactive
az iot hub show-connection-string --name YourIoTHubName --output table
```

次のようなサービス接続文字列をメモしておきます。

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={YourSharedAccessKey}`

この値は、このクイック スタートの後の方で使います。 サービス接続文字列はデバイス接続文字列とは異なります。

## <a name="listen-for-direct-method-calls"></a>ダイレクト メソッドの呼び出しをリッスンする

このデバイス SDK サンプル アプリケーションは、物理 Android デバイスまたは Android Emulator 上で実行することができます。 このサンプルは、IoT ハブ上のデバイス固有エンドポイントに接続し、シミュレートされた利用統計情報を送信して、ハブからのダイレクト メソッド呼び出しをリッスンします。 このクイック スタートでは、ハブからのダイレクト メソッド呼び出しは、利用統計情報の送信間隔を変更するようデバイスに指示します。 シミュレートされたデバイスは、ダイレクト メソッドを実行した後、ハブに受信確認を返送します。

1. GitHub のサンプル Android プロジェクトを Android Studio で開きます。 このプロジェクトは、複製またはダウンロードした [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java) リポジトリのコピーの次のディレクトリにあります。

        \azure-iot-samples-java\iot-hub\Samples\device\AndroidSample

2. Android Studio でサンプル プロジェクトの *gradle.properties* を開き、**Device_Connection_String** プレースホルダーを、先ほどメモしたご自分のデバイスの接続文字列に置き換えます。

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. Android Studio で、**[File]\(ファイル\)** > **[Sync Project with Gradle Files]\(プロジェクトを Gradle ファイルと同期\)** の順にクリックします。 ビルドが完了したことを確認します。

   > [!NOTE]
   > プロジェクトの同期に失敗した場合、次のいずれかの理由が原因として考えられます。
   >
   > * プロジェクトで参照されている Android Gradle プラグインと Gradle のバージョンが、ご使用の Android Studio のバージョンに対して古い。 [こちらの手順](https://developer.android.com/studio/releases/gradle-plugin)に従って、実際の環境に合った正しいバージョンのプラグインと Gradle を参照、インストールしてください。
   > * Android SDK のライセンス契約に署名していない。 ビルド出力に書かれている手順に従ってライセンス契約に署名し、SDK をダウンロードしてください。


4. ビルドが完了したら、**[Run]\(実行\)** > **[Run 'app']\(<アプリ> の実行\)** をクリックします。 物理 Android デバイスまたは Android Emulator 上で実行されるようにアプリを構成します。 物理デバイスまたはエミュレーター上での Android アプリの実行について詳しくは、「[アプリを実行する](https://developer.android.com/training/basics/firstapp/running-app)」を参照してください。

5. アプリが読み込まれたら、**[Start]\(開始\)** ボタンをクリックして、IoT ハブへの利用統計情報の送信を開始します。

    ![Application](media/quickstart-send-telemetry-android/sample-screenshot.png)

利用統計情報の送信間隔を実行時に更新するためには、サービス SDK サンプルを実行している間、物理デバイスまたはエミュレーター上でこのアプリを実行状態にしておく必要があります。


## <a name="read-the-telemetry-from-your-hub"></a>ハブから利用統計情報を読み取る

このセクションでは、[IoT 拡張機能](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest)と共に Azure Cloud Shell を使用して、Android デバイスから送信されるデバイス メッセージを監視します。

1. Azure Cloud Shell を使用して、次のコマンドを実行して接続し、お使いの IoT ハブからのメッセージを読み取ります。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

    ```azurecli-interactive
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```
    次のスクリーンショットは、Android デバイスから送信された利用統計情報を IoT ハブが受信しているときの出力を示しています。

      ![Azure CLI を使用してデバイス メッセージを読み取る](media/quickstart-send-telemetry-android/read-data.png)

既定では、テレメトリ アプリによって Android デバイスから 5 秒おきに利用統計情報が送信されています。 次のセクションでは、ダイレクト メソッド呼び出しを使用して、Android IoT デバイスの利用統計情報の送信間隔を更新します。


## <a name="call-the-direct-method"></a>ダイレクト メソッドを呼び出す

サービス アプリケーションは、IoT ハブ上のサービス側エンドポイントに接続します。 アプリケーションは、IoT ハブを通してデバイスへのダイレクト メソッド呼び出しを行った後、受信確認をリッスンします。

このアプリは、単独の物理 Android デバイスまたは Android Emulator 上で実行します。

通常、IoT ハブのバックエンド サービス アプリケーションはクラウドで実行されます。クラウドの方が、IoT ハブ上のあらゆるデバイスを制御する、慎重な扱いを要する接続文字列に伴うリスクを容易に軽減できます。 この例では、デモンストレーションの目的に限って、Android アプリとして実行しています。 このクイック スタートの他の言語のバージョンでは、もっとバックエンド サービス アプリケーションにふさわしい例が紹介されています。

1. このサービスの GitHub サンプル Android プロジェクトを Android Studio で開きます。 このプロジェクトは、複製またはダウンロードした [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java) リポジトリのコピーの次のディレクトリにあります。

        \azure-iot-samples-java\iot-hub\Samples\service\AndroidSample

2. Android Studio でサンプル プロジェクトの *gradle.properties* を開き、**ConnectionString** プロパティと **DeviceId** プロパティの値を、先ほどメモしたご自分のサービスの接続文字列と登録済みの Android デバイス ID に更新します。

    ```
    ConnectionString=HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={YourSharedAccessKey}
    DeviceId=MyAndroidDevice
    ```

3. Android Studio で、**[File]\(ファイル\)** > **[Sync Project with Gradle Files]\(プロジェクトを Gradle ファイルと同期\)** の順にクリックします。 ビルドが完了したことを確認します。

   > [!NOTE]
   > プロジェクトの同期に失敗した場合、次のいずれかの理由が原因として考えられます。
   >
   > * プロジェクトで参照されている Android Gradle プラグインと Gradle のバージョンが、ご使用の Android Studio のバージョンに対して古い。 [こちらの手順](https://developer.android.com/studio/releases/gradle-plugin)に従って、実際の環境に合った正しいバージョンのプラグインと Gradle を参照、インストールしてください。
   > * Android SDK のライセンス契約に署名していない。 ビルド出力に書かれている手順に従ってライセンス契約に署名し、SDK をダウンロードしてください。


4. ビルドが完了したら、**[Run]\(実行\)** > **[Run 'app']\(<アプリ> の実行\)** をクリックします。 単独の物理 Android デバイスまたは Android Emulator 上で実行されるようにアプリを構成します。 物理デバイスまたはエミュレーター上での Android アプリの実行について詳しくは、「[アプリを実行する](https://developer.android.com/training/basics/firstapp/running-app)」を参照してください。

5. アプリの読み込み後、**[Set Messaging Interval]\(メッセージ送信間隔の設定\)** の値を **1000** に更新し、**[Invoke]\(呼び出し\)** をクリックします。

    利用統計情報メッセージの送信間隔はミリ秒単位です。 このデバイス サンプルでは、利用統計情報の送信間隔が既定で 5 秒に設定されています。 この変更によって、利用統計情報が 1 秒おきに送信されるように Android IoT デバイスが更新されます。

    ![利用統計情報の送信間隔を入力](media/quickstart-control-device-android/enter-telemetry-interval.png)

6. メソッドが正常に実行されたかどうかを示す受信確認がアプリに届きます。

    ![ダイレクト メソッドの受信確認](media/quickstart-control-device-android/direct-method-ack.png)



## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

このクイック スタートでは、バックエンド アプリケーションからデバイス上のダイレクト メソッドを呼び出し、シミュレートされたデバイス アプリケーションでダイレクト メソッド呼び出しに応答しました。

デバイスからクラウドへのメッセージをクラウド内の異なる宛先にルーティングする方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [チュートリアル: 処理のために利用統計情報を異なるエンドポイントにルーティングする](tutorial-routing.md)
