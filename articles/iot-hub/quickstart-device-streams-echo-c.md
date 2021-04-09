---
title: クイックスタート - Azure IoT Hub デバイス ストリームを使用して C でデバイス アプリと通信する
description: このクイックスタートでは、デバイス ストリームを介して IoT デバイスと通信する C デバイス側アプリケーションを実行します。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: fd179bf17f481ae84f768d619c6ddc0b9afab280
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102202554"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>クイック スタート:IoT Hub デバイス ストリームを介して C でデバイス アプリケーションと通信する (プレビュー)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Azure IoT Hub は現在、[プレビュー機能](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)としてデバイス ストリームをサポートしています。

[IoT Hub デバイス ストリーム](iot-hub-device-streams-overview.md)を使用すると、サービス アプリケーションとデバイス アプリケーションが、安全でファイアウォールに対応した方法で通信できます。 パブリック プレビュー中、C SDK ではデバイス側のみのデバイス ストリームがサポートされます。 そのため、このクイックスタートでは、デバイス側アプリケーションを実行する手順のみについて説明しています。 対応するサービス側アプリケーションを実行するには、次の記事を参照してください。

* [IoT Hub デバイス ストリームを介して C# でデバイス アプリと通信する](./quickstart-device-streams-echo-csharp.md)

* [IoT Hub デバイス ストリームを介して Node.js でデバイス アプリと通信する](./quickstart-device-streams-echo-nodejs.md)方法に関するページ

このクイック スタートのデバイス側 C アプリケーションには、以下の機能があります。

* IoT デバイスへのデバイス ストリームを確立します。

* サービス側アプリケーションから送信されたデータを受信し、それをエコーバックします。

コードは、デバイス ストリームの開始プロセスと、それを使用してデータを送受信する方法を示しています。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

次の前提条件を満たす必要があります。

* **C++ によるデスクトップ開発** ワークロードを有効にした [Visual Studio 2019](https://www.visualstudio.com/vs/) をインストールします。

* 最新バージョンの [Git](https://git-scm.com/download/) をインストールします。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

デバイス ストリームのプレビューは現在、以下のリージョンで作成された IoT ハブに対してのみサポートされています。

  * 米国中部
  * 米国中部 EUAP
  * 北ヨーロッパ
  * 東南アジア

## <a name="prepare-the-development-environment"></a>開発環境の準備

このクイックスタートでは、[C 用 Azure IoT device SDK](iot-hub-device-sdk-c-intro.md) を使用します。[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) を GitHub から複製してビルドするために使用される開発環境を準備します。 GitHub 上の SDK には、このクイックスタートで使用されるサンプル コードが含まれています。

   > [!NOTE]
   > この手順を開始する前に、Visual Studio が **C++ によるデスクトップ開発** ワークロードと共にインストールされていることを確認します。

1. ダウンロード ページの説明に従って、[CMake ビルド システム](https://cmake.org/download/)をインストールします。

1. コマンド プロンプトまたは Git Bash シェルを開きます。 次のコマンドを実行して、[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) の GitHub リポジトリを複製します。

    ```cmd/sh
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    この操作には数分かかるはずです。

1. git リポジトリのルート ディレクトリに *cmake* サブディレクトリを作成し、そのフォルダーに移動します。 *azure-iot-sdk-c* ディレクトリから次のコマンドを実行します。

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

1. *cmake* ディレクトリから次のコマンドを実行して、開発クライアント プラットフォームに固有の SDK のバージョンをビルドします。

   * Linux の場合:

      ```bash
      cmake ..
      make -j
      ```

   * Windows では、[Visual Studio の開発者コマンド プロンプト](/dotnet/framework/tools/developer-command-prompt-for-vs)を開きます。 使用している Visual Studio のバージョンに対応するコマンドを実行します。 このクイックスタートでは、Visual Studio 2019 を使用します。 これらのコマンドを実行すると、シミュレートされたデバイスの Visual Studio ソリューションが *cmake* ディレクトリに作成されます。

      ```cmd
      rem For VS2015
      cmake .. -G "Visual Studio 14 2015"

      rem Or for VS2017
      cmake .. -G "Visual Studio 15 2017"

      rem Or for VS2019
      cmake .. -G "Visual Studio 16 2019"

      rem Then build the project
      cmake --build . -- /m /p:Configuration=Release
      ```

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>デバイスの登録

デバイスを IoT ハブに接続するには、あらかじめ IoT ハブに登録しておく必要があります。 このセクションでは、[IoT 拡張機能](/cli/azure/ext/azure-iot/iot)と共に Azure Cloud Shell を使用して、シミュレートされたデバイスを登録します。

1. Cloud Shell で次のコマンドを実行してデバイス ID を作成します。

   > [!NOTE]
   > * *YourIoTHubName* プレースホルダーを、IoT ハブ用に選択した名前に置き換えます。
   > * 登録しているデバイスの名前については、示されているように、*MyDevice* を使用することをお勧めします。 デバイスに別の名前を選択した場合は、この記事全体でその名前を使用し、サンプル アプリケーションを実行する前に、アプリケーション内のデバイス名を更新します。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. 登録したデバイスの "*デバイス接続文字列*" を取得するには、Cloud Shell で次のコマンドを実行します。

   > [!NOTE]
   > *YourIoTHubName* プレースホルダーを、IoT ハブ用に選択した名前に置き換えます。

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    このクイックスタートの後の方で使用できるように、返されたデバイス接続文字列を書き留めておきます。 次の例のようになります。

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>デバイス ストリームを介したデバイスとサービスの間の通信

このセクションでは、デバイス側アプリケーションとサービス側アプリケーションの両方を実行し、両者間で通信します。

### <a name="run-the-device-side-application"></a>デバイス側アプリケーションの実行

デバイス側アプリケーションを実行するには、次の手順に従います。

1. `iothub_client/samples/iothub_client_c2d_streaming_sample` フォルダー内の **iothub_client_c2d_streaming_sample.c** ソース ファイルを編集してデバイスの資格情報を指定し、デバイスの接続文字列を追加します。

   ```C
   /* Paste in your iothub connection string  */
   static const char* connectionString = "{DeviceConnectionString}";
   ```

1. 以下のコマンドを使用して、コードをコンパイルします。

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to the cmake folder at the root of repo
   cmake --build . -- /m /p:Configuration=Release
   ```

1. コンパイルされたプログラムを実行します。

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   ./iothub_client_c2d_streaming_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_sample\Release
   iothub_client_c2d_streaming_sample.exe
   ```

### <a name="run-the-service-side-application"></a>サービス側アプリケーションの実行

前述のように、IoT Hub C SDK ではデバイス側のみのデバイス ストリームがサポートされています。 対応するサービス側アプリケーションをビルドして実行するには、次のいずれかのクイックスタートの手順に従ってください。

* [IoT Hub デバイス ストリームを介して C# でデバイス アプリと通信する](./quickstart-device-streams-echo-csharp.md)

* [IoT Hub デバイス ストリームを介して Node.js でデバイス アプリと通信する](./quickstart-device-streams-echo-nodejs.md)方法に関するページ

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、IoT ハブの設定、デバイスの登録、デバイス上の C アプリケーションとサービス側の別のアプリケーション間のデバイス ストリームの確立、そのストリームを使用したアプリケーション間のデータのやり取りを行いました。

デバイス ストリームについて詳しく学習するには、次を参照してください。

> [!div class="nextstepaction"]
> [デバイス ストリームの概要](./iot-hub-device-streams-overview.md)
