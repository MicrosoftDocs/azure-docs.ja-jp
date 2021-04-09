---
title: クイックスタート - Azure IoT Hub デバイス ストリームを使用して Node.js でデバイス アプリと通信する
description: このクイックスタートでは、デバイス ストリームを介して IoT デバイスと通信する Node.js サービス側アプリケーションを実行します。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 335014f032162866e4780bf1294ddcd108b4fd03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624390"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>クイック スタート:IoT Hub デバイス ストリームを介して Node.js でデバイス アプリケーションと通信する (プレビュー)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

このクイックスタートでは、サービス側アプリケーションを実行し、デバイス ストリームを使用して、デバイスとサービスとの間の通信を設定します。 Azure IoT Hub デバイス ストリームを使用すると、サービス アプリケーションとデバイス アプリケーションが、安全でファイアウォールに対応した方法で通信できます。 パブリック プレビュー中、Node.js SDK ではサービス側のデバイス ストリームのみがサポートされます。 そのため、このクイック スタートでは、サービス側アプリケーションを実行する手順についてのみ説明しています。

## <a name="prerequisites"></a>前提条件

* [IoT Hub デバイス ストリームを介して C でデバイス アプリと通信する](./quickstart-device-streams-echo-c.md)クイックスタートまたは [IoT Hub デバイス ストリームを介して C# でデバイス アプリと通信する](./quickstart-device-streams-echo-csharp.md)クイックスタートが完了していること。

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

* [Node.js 10 以上](https://nodejs.org)。

    開発コンピューターに現在インストールされている Node.js のバージョンは、次のコマンドを使って確認できます。

    ```cmd/sh
    node --version
    ```

* [サンプル Node.js プロジェクト](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip)。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

Microsoft Azure IoT Hub は現在、[プレビュー機能](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)としてデバイス ストリームをサポートしています。

> [!IMPORTANT]
> デバイス ストリームのプレビューは現在、次のリージョンで作成された IoT Hub に対してのみサポートされています。
>
> * 米国中部
> * 米国中部 EUAP
> * 北ヨーロッパ
> * 東南アジア

## <a name="create-an-iot-hub"></a>IoT Hub の作成

前出の[デバイスから IoT Hub への利用統計情報の送信に関するクイック スタート](quickstart-send-telemetry-node.md)を完了した場合は、この手順を省略できます。

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>デバイスの登録

前の「[クイック スタート: デバイスから IoT Hub への利用統計情報の送信](quickstart-send-telemetry-node.md)」を完了した場合は、この手順を省略できます。

デバイスを IoT Hub に接続するには、あらかじめ IoT Hub に登録しておく必要があります。 このクイック スタートでは、Azure Cloud Shell を使用して、シミュレートされたデバイスを登録します。

1. Azure Cloud Shell で次のコマンドを実行してデバイス ID を作成します。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT Hub に対して選んだ名前に置き換えてください。

   **MyDevice**: これは、登録するデバイスの名前です。 示されているように、**MyDevice** を使用することをお勧めします。 デバイスに別の名前を選択した場合は、この記事全体でその名前を使用する必要があります。また、サンプル アプリケーションを実行する前に、アプリケーション内のデバイス名を更新してください。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

2. また、バックエンド アプリケーションが IoT ハブに接続してメッセージを取得できるようにするには、"*サービス接続文字列*" が必要です。 次のコマンドを実行すると、IoT ハブのサービス接続文字列が取得されます。

    **YourIoTHubName**: このプレースホルダーは、実際の IoT Hub に対して選んだ名前に置き換えてください。

    ```azurecli-interactive
    az iot hub connection-string show --policy-name service --name {YourIoTHubName} --output table
    ```

    このクイックスタートの後の方で使用できるように、返されたサービス接続文字列を書き留めておきます。 次の例のようになります。

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>デバイス ストリームを介したデバイスとサービスの間の通信

このセクションでは、デバイス側アプリケーションとサービス側アプリケーションの両方を実行し、両者間で通信します。

### <a name="run-the-device-side-application"></a>デバイス側アプリケーションの実行

前述のように、IoT Hub Node.js SDK ではサービス側のデバイス ストリームのみがサポートされます。 デバイス側アプリケーションについては、以下のクイックスタートに記載されている、対応するいずれかのデバイス プログラムを使用してください。

* [IoT Hub デバイス ストリームを介して C でデバイス アプリと通信する](./quickstart-device-streams-echo-c.md)

* [IoT Hub デバイス ストリームを介して C# でデバイス アプリと通信する](./quickstart-device-streams-echo-csharp.md)

次の手順に進む前に、デバイス側アプリケーションが実行されていることを確認します。

### <a name="run-the-service-side-application"></a>サービス側アプリケーションの実行

このクイック スタートのサービス側 Node.js アプリケーションには、以下の機能があります。

* IoT デバイスへのデバイス ストリームを作成します。
* コマンド ラインから入力を読み込み、それをデバイス アプリケーションに送信します。デバイス アプリケーションは、それをエコーバックします。

コードは、デバイス ストリームの開始プロセスと、それを使用してデータを送受信する方法を示しています。

デバイス側アプリケーションが実行されているとして、Node.js でサービス側アプリケーションを実行するには、ローカルのターミナル ウィンドウで次の手順に従います。

* サービス資格情報とデバイス ID を環境変数として指定します。
 
   ```cmd/sh
   # In Linux
   export IOTHUB_CONNECTION_STRING="{ServiceConnectionString}"
   export STREAMING_TARGET_DEVICE="MyDevice"

   # In Windows
   SET IOTHUB_CONNECTION_STRING={ServiceConnectionString}
   SET STREAMING_TARGET_DEVICE=MyDevice
   ```
  
   ServiceConnectionString プレースホルダーを、サービス接続文字列に一致するように変更します。また、**MyDevice** を、これ以外の名前を指定した場合は、デバイス ID に一致するように変更します。

* 解凍したプロジェクト フォルダーの `Quickstarts/device-streams-service` に移動し、ノードを使用してサンプルを実行します。

   ```cmd/sh
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service
    
   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   node echo.js
   ```

最後の手順の終わりに、サービス側プログラムはデバイスへのストリームを開始し、確立後はストリームを介してサービスに文字列バッファーを送信します。 このサンプルでは、​​サービス側プログラムは単にターミナルの `stdin` を読み取り、それをデバイスに送信します。その後、デバイスはそれをエコーバックします。 これによって、2 つのアプリケーション間の双方向通信が成功したことが示されます。

![サービス側のコンソール出力](./media/quickstart-device-streams-echo-nodejs/service-console-output.png)

その後、もう一度 Enter キーを押すと、プログラムを終了することができます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、IoT ハブの設定、デバイスの登録、デバイス側とサービス側のアプリケーション間のデバイス ストリームの確立、そのストリームを使用したアプリケーション間のデータのやり取りを行いました。

以下のリンクを使用して、デバイス ストリームについてさらに詳しく学習します。

> [!div class="nextstepaction"]
> [デバイス ストリームの概要](./iot-hub-device-streams-overview.md)
