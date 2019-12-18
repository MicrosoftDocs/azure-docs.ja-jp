---
title: Azure IoT Hub からのデバイスの制御に関するクイック スタート (Python) | Microsoft Docs
description: このクイック スタートでは、2 つのサンプル Python アプリケーションを実行します。 1 つのアプリケーションは、ハブに接続されたデバイスをリモートで制御できるバックエンド アプリケーションです。 もう 1 つのアプリケーションは、ハブに接続されたリモートで制御できるデバイスをシミュレートします。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/21/2019
ms.openlocfilehash: b36e5d88c67a4aabf530aa8d945c17870e9c126b
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892653"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-python"></a>クイック スタート:IoT ハブに接続されたデバイスを制御する (Python)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub は、クラウドから IoT デバイスを管理し、大量のデバイス テレメトリを格納または処理のためにクラウドに取り込むことができるようにする Azure サービスです。 このクイック スタートでは、"*ダイレクト メソッド*" を使って、IoT ハブに接続されているシミュレートされたデバイスを制御します。 ダイレクト メソッドを使うと、IoT ハブに接続されたデバイスの動作をリモートで変更できます。

このクイック スタートでは、あらかじめ作成されている次の 2 つの Python アプリケーションを使います。

* シミュレートされたデバイス アプリケーションは、バックエンド アプリケーションから呼び出されたダイレクト メソッドに応答します。 ダイレクト メソッドの呼び出しを受け取るため、このアプリケーションは IoT ハブ上のデバイス固有のエンドポイントに接続します。

* バックエンド アプリケーションは、シミュレートされたデバイスでダイレクト メソッドを呼び出します。 デバイスでダイレクト メソッドを呼び出すため、このアプリケーションは IoT ハブ上のサービス側エンドポイントに接続します。

> [!IMPORTANT]
> この記事では、バックエンド アプリケーションで Python V1 サービス クライアントを、デバイス アプリケーションで Python V2 デバイス クライアントを使用します。 V1 サービス クライアントは Azure IoT Python SDK GitHub リポジトリの [v1-deprecated branch](https://github.com/Azure/azure-iot-sdk-python/tree/v1-deprecated) にあります。 V1 サービス クライアントの pip パッケージ (*azure-iothub-service-client*) には、開発マシンにインストールされている Python のバージョンなど、プラットフォームごとの厳密な要件があります。 それらの要件は、「**前提条件**」セクションに記載されています。
>

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

次のコマンドを実行して、Microsoft Azure IoT Extension for Azure CLI を Cloud Shell インスタンスに追加します。 IoT Hub、IoT Edge、IoT Device Provisioning Service (DPS) 固有のコマンドが Azure CLI に追加されます。

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

まだ行っていない場合は、 https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip からサンプル Python プロジェクトをダウンロードし、ZIP アーカイブを抽出します。

**Windows の場合**: V1 IoT Hub サービス クライアントの pip パッケージをインストールするには、次の前提条件を満たす必要があります。

* [Python バージョン **3.6.x**](https://www.python.org/downloads/) がインストールされていることを確認します。

* [Microsoft Visual Studio の Visual C++ 再頒布可能パッケージ](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads)がインストールされていることを確認します。

**Windows 以外のプラットフォームの場合**: V1 SDK ドキュメントの「[Python pip パッケージ ディストリビューション表](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md#python-pip-package-distribution-table)」を参照してください。 ご利用のプラットフォーム向けに指定された Python 3.x バージョンおよび関連する要件が開発マシンにインストールされていることを確認してください。 Python 2.7 ではなく Python 3.x をインストールすることにより、このクイックスタートでも使用されている V2 デバイス クライアントでの非同期操作が可能となります。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

前出の[デバイスから IoT ハブへの利用統計情報の送信に関するクイック スタート](quickstart-send-telemetry-python.md)を完了した場合は、この手順を省略できます。

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>デバイスの登録

前出の[デバイスから IoT ハブへの利用統計情報の送信に関するクイック スタート](quickstart-send-telemetry-python.md)を完了した場合は、この手順を省略できます。

デバイスを IoT Hub に接続するには、あらかじめ IoT Hub に登録しておく必要があります。 このクイック スタートでは、Azure Cloud Shell を使用して、シミュレートされたデバイスを登録します。

1. Azure Cloud Shell で次のコマンドを実行してデバイス ID を作成します。

    **YourIoTHubName**: このプレースホルダーは、実際の IoT Hub に対して選んだ名前に置き換えてください。

    **MyPythonDevice**: これは、登録するデバイスの名前です。 示されているように、**MyPythonDevice** を使用することをお勧めします。 デバイスに別の名前を選択した場合は、この記事全体でその名前を使用する必要があります。また、サンプル アプリケーションを実行する前に、アプリケーション内のデバイス名を更新してください。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

2. Azure Cloud Shell で次のコマンドを実行して、登録したデバイスの "_デバイス接続文字列_" を取得します。

    **YourIoTHubName**: このプレースホルダーは、実際の IoT Hub に対して選んだ名前に置き換えてください。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    次のようなデバイス接続文字列をメモしておきます。

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    この値は、このクイック スタートの後の方で使います。

3. また、バックエンド アプリケーションが IoT ハブに接続してメッセージを取得できるようにするには、"_サービス接続文字列_" が必要です。 次のコマンドを実行すると、IoT ハブのサービス接続文字列が取得されます。

    **YourIoTHubName**: このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

    ```azurecli-interactive
    az iot hub show-connection-string \
      --policy-name service \
      --name {YourIoTHubName} \
      --output table
    ```

    次のようなサービス接続文字列をメモしておきます。

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

    この値は、このクイック スタートの後の方で使います。 このサービス接続文字列は、前の手順でメモしたデバイス接続文字列とは異なります。

## <a name="listen-for-direct-method-calls"></a>ダイレクト メソッドの呼び出しをリッスンする

シミュレートされたデバイス アプリケーションは、IoT ハブ上のデバイス固有エンドポイントに接続し、シミュレートされた利用統計情報を送信して、ハブからのダイレクト メソッド呼び出しをリッスンします。 このクイック スタートでは、ハブからのダイレクト メソッド呼び出しは、利用統計情報の送信間隔を変更するようデバイスに指示します。 シミュレートされたデバイスでは、ダイレクト メソッドを実行した後、ハブに受信確認が返送されます。

1. ローカル ターミナル ウィンドウで、サンプルの Python プロジェクトのルート フォルダーに移動します。 次に、**iot-hub\Quickstarts\simulated-device-2** フォルダーに移動します。

1. 適当なテキスト エディターで **SimulatedDevice.py** ファイルを開きます。

    `CONNECTION_STRING` 変数の値を、前にメモしたデバイス接続文字列に置き換えます。 その後、変更を **SimulatedDevice.py** に保存します。

1. ローカル ターミナル ウィンドウで次のコマンドを実行して、シミュレートされたデバイス アプリケーションに必要なライブラリをインストールします。

    ```cmd/sh
    pip install azure-iot-device
    ```

1. ローカル ターミナル ウィンドウで次のコマンドを実行して、シミュレートされたデバイス アプリケーションを実行します。

    ```cmd/sh
    python SimulatedDevice.py
    ```

    次のスクリーンショットは、シミュレートされたデバイス アプリケーションが IoT Hub にテレメトリを送信したときの出力を示しています。

    ![シミュレートされたデバイスを実行する](./media/quickstart-control-device-python/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>ダイレクト メソッドを呼び出す

バックエンド アプリケーションは、IoT ハブ上のサービス側エンドポイントに接続します。 アプリケーションにより、IoT ハブを通してデバイスへのダイレクト メソッド呼び出しが行われた後、受信確認がリッスンされます。 通常、IoT Hub のバックエンド アプリケーションはクラウドで実行されます。

1. 別のローカル ターミナル ウィンドウで、サンプルの Python プロジェクトのルート フォルダーに移動します。 その後、**iot-hub\Quickstarts\back-end-application** フォルダーに移動します。

1. 適当なテキスト エディターで **BackEndApplication.py** ファイルを開きます。

    `CONNECTION_STRING` 変数の値を、前にメモしたサービス接続文字列に置き換えます。 変更を **BackEndApplication.py** に保存します。

1. ローカル ターミナル ウィンドウで次のコマンドを実行して、シミュレートされたデバイス アプリケーションに必要なライブラリをインストールします。

    ```cmd/sh
    pip install azure-iothub-service-client future
    ```

1. ローカル ターミナル ウィンドウで次のコマンドを実行して、バックエンド アプリケーションを実行します。

    ```cmd/sh
    python BackEndApplication.py
    ```

    次のスクリーンショットは、アプリケーションによりデバイスに対してダイレクト メソッド呼び出しが行われ、受信確認が受診されたときの出力を示します。

    ![バックエンド アプリケーションを実行する](./media/quickstart-control-device-python/BackEndApplication.png)

    バックエンド アプリケーションを実行した後、シミュレートされたデバイスを実行しているコンソール ウィンドウにメッセージが表示され、メッセージの送信速度が変わります。

    ![シミュレートされたクライアントでの変更](./media/quickstart-control-device-python/SimulatedDevice-2.png)

    > [!NOTE]
    > *iothub_service_client* のインポートでエラーが発生した場合は、ご利用のプラットフォームに関して「[前提条件](#prerequisites)」で指定された正しいバージョンの Python と、その他関連する成果物がすべてインストールされていることを確認してください。 前提条件を確認したにもかかわらずエラーが発生する場合は、おそらくご利用のプラットフォーム用にサービス クライアントをビルドする必要があります。 ご利用のプラットフォーム用に SDK をビルドする方法については、V1 SDK ドキュメントで [devbox のセットアップ手順](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md)を参照してください。
    >

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

このクイック スタートでは、バックエンド アプリケーションからデバイス上のダイレクト メソッドを呼び出し、シミュレートされたデバイス アプリケーションでダイレクト メソッド呼び出しに応答しました。

デバイスからクラウドへのメッセージをクラウド内の異なる宛先にルーティングする方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [チュートリアル: 処理のために利用統計情報を異なるエンドポイントにルーティングする](tutorial-routing.md)