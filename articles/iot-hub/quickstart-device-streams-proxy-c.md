---
title: SSH または RDP 用の Azure IoT Hub デバイス ストリームの C クイック スタート (プレビュー) | Microsoft Docs
description: このクイック スタートでは、IoT Hub デバイス ストリームを介した SSH シナリオまたは RDP シナリオを有効にするためにプロキシとして機能するサンプル C アプリケーションを実行します。
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 1468268e407eeac6196c8e8e4db0fc5a52ca09c7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59501571"
---
# <a name="quickstart-sshrdp-over-iot-hub-device-streams-using-c-proxy-application-preview"></a>クイック スタート:C プロキシ アプリケーションを使用した IoT Hub デバイス ストリーム経由の SSH または RDP (プレビュー)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub は現在、[プレビュー機能](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)としてデバイス ストリームをサポートしています。

[IoT Hub デバイス ストリーム](./iot-hub-device-streams-overview.md)を使用すると、サービス アプリケーションとデバイス アプリケーションが、安全でファイアウォールに対応した方法で通信できます。 設定の概要については、[このページ](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp)を参照してください。

このドキュメントでは、デバイス ストリームを通じて SSH トラフィック (ポート 22 を使用) をトンネリングするための設定について説明します。 RDP トラフィック用の設定も同様ですが、簡単な構成変更が必要です。 デバイス ストリームはアプリケーションやプロトコルに依存しないため、このクイック スタートは、(通信ポートを変更することによって) 他の種類のアプリケーション トラフィックに対応するように変更できます。

## <a name="how-it-works"></a>動作のしくみ

次の図は、デバイスローカルおよびサービスローカルのプロキシ プログラムで、SSH クライアントと SSH デーモン プロセスの間のエンドツーエンド接続を可能にする設定を示しています。 パブリック プレビュー中、C SDK ではデバイス側のデバイス ストリームのみがサポートされます。 そのため、このクイック スタートでは、デバイスローカルのプロキシ アプリケーションを実行する手順についてのみ説明しています。 対応するサービスローカルのプロキシ アプリケーションも実行する必要があり、それについては [C# クイック スタート](./quickstart-device-streams-proxy-csharp.md)または [Node.js クイック スタート](./quickstart-device-streams-proxy-nodejs.md)のガイドに記載されています。

![代替テキスト](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg "ローカル プロキシの設定")

1. サービスローカルのプロキシによって IoT ハブへの接続が行われ、ターゲット デバイスへのデバイス ストリームが開始されます。

2. デバイスローカルのプロキシによって、ストリームの開始ハンドシェイクが完了され、サーバー側への IoT Hub のストリーミング エンドポイントを通じてエンドツーエンドのストリーミング トンネルが確立されます。

3. デバイスローカルのプロキシによって、デバイス上のポート 22 をリッスンする SSH デーモン (SSHD) への接続が行われます ([以下](#run-the device-local-proxy-application)で説明するように、これは構成可能です)。

4. サービスローカルのプロキシは、指定されたポート (この場合はポート 2222) をリッスンして、ユーザーからの新しい SSH 接続を待機します ([以下](#run-the-device-local-proxy-application)で説明するように、これも構成可能です)。 ユーザーが SSH クライアントを介して接続すると、トンネルによって SSH アプリケーション トラフィックが SSH クライアントとサーバー プログラムの間で転送されるようになります。

> [!NOTE]
> デバイス ストリームを介して送信される SSH トラフィックは、サービスとデバイスの間で直接送信されるのではなく、IoT Hub のストリーミング エンドポイントを介してトンネリングされます。 このことには、[これらの利点](./iot-hub-device-streams-overview.md#benefits)があります。 さらに、図ではデバイスローカルのプロキシと同じデバイス (またはマシン) 上で実行されている SSH デーモンが示されています。 このクイック スタートでは、SSH デーモンの IP アドレスを指定することで、デバイスローカルのプロキシとデーモンを異なるマシン上で実行することもできます。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

* デバイス ストリームのプレビューは現在、次のリージョンで作成された IoT Hub に対してのみサポートされています。

  * **米国中部**
  * **米国中部 EUAP**

* ["C++ によるデスクトップ開発"](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) ワークロードを有効にした [Visual Studio 2017](https://www.visualstudio.com/vs/) をインストールします。
* 最新バージョンの [Git](https://git-scm.com/download/) をインストールします。
* 次のコマンドを実行して、Microsoft Azure IoT Extension for Azure CLI を Cloud Shell インスタンスに追加します。 IoT Hub、IoT Edge、IoT Device Provisioning Service (DPS) 固有のコマンドが Azure CLI に追加されます。

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>開発環境の準備

このクイック スタートでは、[C 対応の Azure IoT device SDK](iot-hub-device-sdk-c-intro.md) を使用します。[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) を GitHub から複製してビルドするために使用される開発環境を準備します。 GitHub 上の SDK には、このクイック スタートで使用されるサンプル コードが含まれます。 

1. [CMake ビルド システム](https://cmake.org/download/)をダウンロードします。

    `CMake` のインストールを開始する**前に**、Visual Studio の前提条件 (Visual Studio と "C++ によるデスクトップ開発" ワークロード) が マシンにインストールされていることが重要です。 前提条件を満たし、ダウンロードを検証したら、CMake ビルド システムをインストールします。

2. コマンド プロンプトまたは Git Bash シェルを開きます。 次のコマンドを実行して、[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) の GitHub リポジトリを複製します。
    
    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```
    この操作は、完了するまでに数分かかります。

3. git リポジトリのルート ディレクトリに `cmake` サブディレクトリを作成し、そのフォルダーに移動します。 

    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. `cmake` ディレクトリから次のコマンドを実行して、開発クライアント プラットフォームに固有の SDK のバージョンをビルドします。

   * Linux の場合:

      ```bash
      cmake ..
      make -j
      ```

   * Windows では、Visual Studio 2015 または 2017 用の開発者コマンド プロンプトで、次のコマンドを実行します。 シミュレートされたデバイスの Visual Studio ソリューションが `cmake` ディレクトリに生成されます。

      ```cmd
      rem For VS2015
      cmake .. -G "Visual Studio 14 2015"

      rem Or for VS2017
      cmake .. -G "Visual Studio 15 2017"

      rem Then build the project
      cmake --build . -- /m /p:Configuration=Release
      ```

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>デバイスの登録

デバイスを IoT ハブに接続するには、あらかじめ IoT ハブに登録しておく必要があります。 このセクションでは、[IoT 拡張機能](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest)と共に Azure Cloud Shell を使用して、シミュレートされたデバイスを登録します。

1. Azure Cloud Shell で次のコマンドを実行してデバイス ID を作成します。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

   **MyDevice**: これは、登録済みデバイスに付けられた名前です。 示されているように、MyDevice を使用します。 デバイスに別の名前を選択した場合は、この記事全体でその名前を使用する必要があります。また、サンプル アプリケーションを実行する前に、アプリケーション内のデバイス名を更新してください。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Azure Cloud Shell で次のコマンドを実行して、登録したデバイスの "_デバイス接続文字列_" を取得します。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    次の例のような、デバイスの接続文字列をメモしておきます。

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

    この値は、このクイック スタートの後の方で使います。

## <a name="ssh-to-a-device-via-device-streams"></a>デバイス ストリームを介したデバイスへの SSH 接続

### <a name="run-the-device-local-proxy-application"></a>デバイスローカルのプロキシ アプリケーションの実行

1. ソース ファイル `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample/iothub_client_c2d_streaming_proxy_sample.c` を編集して、お客様のデバイスの接続文字列、ターゲット デバイスの IP またはホスト名、SSH ポート 22 を指定します。

   ```C
   /* Paste in the your iothub connection string  */
   static const char* connectionString = "[Connection string of IoT Hub]";
   static const char* localHost = "[IP/Host of your target machine]"; // Address of the local server to connect to.
   static const size_t localPort = 22; // Port of the local server to connect to.
   ```

2. サンプルをコンパイルします。

   ```bash
    # In Linux
    # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
    make -j
   ```

   ```cmd
    rem In Windows
    rem Go to cmake at root of repository
    cmake --build . -- /m /p:Configuration=Release
   ```

3. デバイス上で、コンパイル済みプログラムを実行します。

   ```bash
    # In Linux
    # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
    ./iothub_client_c2d_streaming_proxy_sample
   ```

   ```cmd
    rem In Windows
    rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_proxy_sample\Release
    iothub_client_c2d_streaming_proxy_sample.exe
   ```

### <a name="run-the-service-local-proxy-application"></a>サービスローカルのプロキシ アプリケーションの実行

[前述](#how-it-works)のように、SSH トラフィックをトンネリングするためにエンドツーエンドのストリームを確立するには、両端 (サービスとデバイスの両方) にローカル プロキシが必要です。 パブリック プレビュー中、IoT Hub C SDK ではデバイス側のデバイス ストリームのみがサポートされます。 サービスローカル プロキシを構築して実行するには、[C# クイック スタート](./quickstart-device-streams-proxy-csharp.md)または [Node.js クイック スタート](./quickstart-device-streams-proxy-nodejs.md)に記載されている手順に従ってください。

### <a name="establish-an-ssh-session"></a>SSH セッションの確立

デバイスローカルとサービスローカルの両方のプロキシが実行されていると、お客様の SSH クライアント プログラムを使用して、(SSH デーモンに直接ではなく) ポート 2222 でサービスローカルのプロキシに接続します。

```cmd/sh
ssh <username>@localhost -p 2222
```

この時点で、お客様の資格情報を入力するための SSH ログイン プロンプトが表示されます。

`IP_address:22` の SSH デーモンに接続するデバイスローカルのプロキシのコンソール出力:![代替テキスト](./media/quickstart-device-streams-proxy-c/device-console-output.PNG "デバイスローカルのプロキシの出力")

SSH クライアント プログラムのコンソール出力 (SSH クライアントは、サービスローカルのプロキシがリッスンしているポート 22 に接続することで、SSH デーモンと通信します):![代替テキスト](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png "SSH クライアントの出力")

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>次の手順

このクイック スタートでは、IoT ハブの設定、デバイスの登録、デバイスローカルおよびサービスローカルのプロキシ プログラムのデプロイによる IoT Hub を通じたデバイス ストリームの確立、およびプロキシの使用による SSH トラフィックのトンネリングを行いました。

以下のリンクを使用して、デバイス ストリームについてさらに詳しく学習します。

> [!div class="nextstepaction"]
> [デバイス ストリームの概要](./iot-hub-device-streams-overview.md)
