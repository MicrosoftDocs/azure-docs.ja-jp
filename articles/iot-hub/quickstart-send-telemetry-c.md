---
title: クイックスタート - Azure IoT Hub への利用統計情報の送信に関するクイックスタート (C) | Microsoft Docs
description: このクイック スタートでは、2 つのサンプル C アプリケーションを実行して、IoT ハブにシミュレートされた利用統計情報を送信し、クラウドで処理するために IoT ハブから利用統計情報を読み取ります。
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
- devx-track-azurecli
ms.date: 04/10/2019
ms.author: wesmc
ms.openlocfilehash: aad28c704ba8c46f4c2d0111e545dc4d2381250d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102201942"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-c"></a>クイック スタート:デバイスから IoT ハブに利用統計情報を送信してバックエンド アプリケーションで読み取る (C)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub は、保管や処理のために IoT デバイスから大量のテレメトリをクラウドに取り込むことを可能にする Azure サービスです。 このクイック スタートでは、シミュレートされたデバイス アプリケーションから、IoT Hub を経由して、処理のためのバックエンド アプリケーションに、テレメトリを送信します。

クイック スタートでは、[C 対応の Azure IoT device SDK ](iot-hub-device-sdk-c-intro.md) から C サンプル アプリケーションを使用して、IoT ハブに利用統計情報を送信します。 Azure IoT device SDK は、移植性と広範なプラットフォーム互換性のために、[ANSI C (C99)](https://wikipedia.org/wiki/C99) で作成されています。 サンプル コードを実行する前に、IoT ハブを作成して、そのハブを使ってシミュレートされたデバイスを登録します。

この記事は Windows 用に書かれていますが、Linux 上でも、このクイックスタートを完了できます。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

* ["C++ によるデスクトップ開発"](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) ワークロードを有効にした [Visual Studio 2019](https://www.visualstudio.com/vs/) をインストールします。

* 最新バージョンの [Git](https://git-scm.com/download/) をインストールします。

* ポート 8883 がファイアウォールで開放されていることを確認してください。 このクイックスタートのデバイス サンプルでは、ポート 8883 を介して通信する MQTT プロトコルを使用しています。 このポートは、企業や教育用のネットワーク環境によってはブロックされている場合があります。 この問題の詳細と対処方法については、「[IoT Hub への接続 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)」を参照してください。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prepare-the-development-environment"></a>開発環境の準備

このクイック スタートでは、[C 対応の Azure IoT device SDK](iot-hub-device-sdk-c-intro.md) を使用します。 

以下の環境にこれらのパッケージとライブラリをインストールして、SDK を使用できます。

* **Linux**: CPU アーキテクチャ amd64、arm64、armhf、および i386 を使用して、Ubuntu 16.04 および 18.04 用に apt-get パッケージが利用可能です。 詳細については、「[Using apt-get to create a C device client project on Ubuntu](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/ubuntu_apt-get_sample_setup.md)」(apt-get を使用して Ubuntu 上に C デバイス クライアント プロジェクトを作成する) を参照してください。

* **mbed**: mbed プラットフォーム上にデバイス アプリケーションを作成している開発者向けに、ライブラリとサンプルを公開しています。これを利用して、Azure IoT Hub の使用をすぐに開始できます。 詳細については、[mbed ライブラリの使用](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#mbed)に関するページを参照してください。

* **Arduino**: Arduino で開発している場合、Arduino IDE ライブラリ マネージャーで利用可能な Azure IoT ライブラリを活用できます。 詳細については、[Arduino 対応の Azure IoT Hub ライブラリ](https://github.com/azure/azure-iot-arduino)に関するページを参照してください。

* **iOS**: IoT Hub Device SDK は、Mac および iOS デバイス開発の CocoaPods として利用可能です。 詳細については、[Microsoft Azure IoT の iOS サンプル](https://cocoapods.org/pods/AzureIoTHubClient)に関するページを参照してください。

ただし、このクイック スタートでは、クローンに使用される開発環境を準備して、GitHub から [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) をビルドします。 GitHub 上の SDK には、このクイック スタートで使用されるサンプル コードが含まれます。

1. [CMake ビルド システム](https://cmake.org/download/)をダウンロードします。

    `CMake` のインストールを開始する **前に**、Visual Studio の前提条件 (Visual Studio と "C++ によるデスクトップ開発" ワークロード) が マシンにインストールされていることが重要です。 前提条件を満たし、ダウンロードを検証したら、CMake ビルド システムをインストールします。

2. SDK の[最新リリース](https://github.com/Azure/azure-iot-sdk-c/releases/latest)のタグ名を見つけます。

3. コマンド プロンプトまたは Git Bash シェルを開きます。 次のコマンドを実行して、最新リリースの [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub リポジトリを複製します。 `-b` パラメーターの値として、前の手順で見つけたタグを使用します。

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    この操作は、完了するまでに数分かかります。

4. git リポジトリのルート ディレクトリに `cmake` サブディレクトリを作成し、そのフォルダーに移動します。 `azure-iot-sdk-c` ディレクトリから次のコマンドを実行します。

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. 次のコマンドを実行して、開発クライアント プラットフォームに固有の SDK のバージョンをビルドします。 シミュレートされたデバイスの Visual Studio ソリューションが `cmake` ディレクトリに生成されます。

    ```cmd
    cmake ..
    ```

    `cmake` で C++ コンパイラが見つからない場合は、上記のコマンドの実行中にビルド エラーが発生している可能性があります。 これが発生した場合は、[Visual Studio コマンド プロンプト](/dotnet/framework/tools/developer-command-prompt-for-vs)でこのコマンドを実行してください。 

    ビルドが成功すると、最後のいくつかの出力行は次のようになります。

    ```cmd/sh
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>デバイスの登録

デバイスを IoT Hub に接続するには、あらかじめ IoT Hub に登録しておく必要があります。 このセクションでは、[IoT 拡張機能](/cli/azure/ext/azure-iot/iot)と共に Azure Cloud Shell を使用して、シミュレートされたデバイスを登録します。

1. Azure Cloud Shell で次のコマンドを実行してデバイス ID を作成します。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT Hub に対して選んだ名前に置き換えてください。

   **MyCDevice**: これは、登録するデバイスの名前です。 示されているように、**MyCDevice** を使用することをお勧めします。 デバイスに別の名前を選択した場合は、この記事全体でその名前を使用する必要があります。また、サンプル アプリケーションを実行する前に、アプリケーション内のデバイス名を更新してください。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyCDevice
    ```

2. Azure Cloud Shell で次のコマンドを実行して、登録したデバイスの "_デバイス接続文字列_" を取得します。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT Hub に対して選んだ名前に置き換えてください。

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyCDevice --output table
    ```

    次のようなデバイス接続文字列をメモしておきます。

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyCDevice;SharedAccessKey={YourSharedAccessKey}`

    この値は、このクイックスタートの後の方で使用します。

## <a name="send-simulated-telemetry"></a>シミュレートされたテレメトリの送信

シミュレートされたデバイス アプリケーションは、IoT ハブ上のデバイス固有のエンドポイントに接続し、シミュレートされた利用統計情報として文字列を送信します。

1. テキスト エディターを使用して、iothub_convenience_sample.c ソース ファイルを開き、利用統計情報を送信するサンプル コードを確認します。 このファイルは、Azure IoT C SDK を複製した作業ディレクトリの下の次の場所にあります。

    ```
    azure-iot-sdk-c\iothub_client\samples\iothub_convenience_sample\iothub_convenience_sample.c
    ```

2. 次の `connectionString` 定数の宣言を探します。

    ```C
    /* Paste in your device connection string  */
    static const char* connectionString = "[device connection string]";
    ```

    `connectionString` 定数の値を、前にメモしたデバイス接続文字列に置き換えます。 その後、**iothub_convenience_sample.c** への変更を保存します。

3. ローカル ターミナル ウィンドウで、Azure IoT C SDK で作成した CMake ディレクトリの *iothub_convenience_sample* プロジェクト ディレクトリに移動します。 作業ディレクトリから次のコマンドを入力します。

    ```cmd/sh
    cd azure-iot-sdk-c/cmake/iothub_client/samples/iothub_convenience_sample
    ```

4. ローカル ターミナル ウィンドウで CMake を実行して、更新した `connectionString` 値を使用してサンプルをビルドします。

    ```cmd/sh
    cmake --build . --target iothub_convenience_sample --config Debug
    ```

5. ローカル ターミナル ウィンドウで次のコマンドを実行して、シミュレートされたデバイス アプリケーションを実行します。

    ```cmd/sh
    Debug\iothub_convenience_sample.exe
    ```

    次のスクリーンショットは、シミュレートされたデバイス アプリケーションが IoT ハブに利用統計情報を送信したときの出力を示しています。

    ![シミュレートされたデバイスを実行する](media/quickstart-send-telemetry-c/simulated-device-app.png)

## <a name="read-the-telemetry-from-your-hub"></a>Hub からテレメトリを読み取る

このセクションでは、[IoT 拡張機能](/cli/azure/ext/azure-iot/iot)と共に Azure Cloud Shell を使用して、シミュレートされたデバイスから送信されるデバイス メッセージを監視します。

1. Azure Cloud Shell を使用して、次のコマンドを実行して接続し、お使いの IoT ハブからのメッセージを読み取ります。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    ![Azure CLI を使用してデバイス メッセージを読み取る](media/quickstart-send-telemetry-c/read-device-to-cloud-messages-app.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、IoT ハブの設定、デバイスの登録、C アプリケーションによるハブへのシミュレートされた利用統計情報の送信、および Azure Cloud Shell によるハブからの利用統計情報の読み取りを行いました。

Azure IoT Hub C SDK による開発の詳細については、引き続き次の攻略ガイドを参照してください。

> [!div class="nextstepaction"]
> [Azure IoT Hub C SDK を使用した開発](iot-hub-devguide-develop-for-constrained-devices.md)
