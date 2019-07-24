---
title: Azure IoT Hub デバイス ストリーム Node.js クイック スタート (プレビュー) | Microsoft Docs
description: このクイック スタートでは、デバイス ストリームを介して IoT デバイスと通信する Node.js サービス側アプリケーションを実行します。
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 4b546b91634e153fa0074adfb863596a1bf36242
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59006441"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>クイック スタート:IoT Hub デバイス ストリームを介して Node.js でデバイス アプリケーションと通信する (プレビュー)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Microsoft Azure IoT Hub は現在、[プレビュー機能](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)としてデバイス ストリームをサポートしています。

[IoT Hub デバイス ストリーム](./iot-hub-device-streams-overview.md)を使用すると、サービス アプリケーションとデバイス アプリケーションが、安全でファイアウォールに対応した方法で通信できます。 パブリック プレビュー中、Node.js SDK ではサービス側のデバイス ストリームのみがサポートされます。 そのため、このクイック スタートでは、サービス側アプリケーションを実行する手順についてのみ説明しています。 対応するデバイス側アプリケーションも実行する必要があり、それについては [C クイック スタート](./quickstart-device-streams-echo-c.md)または [C# クイック スタート](./quickstart-device-streams-echo-csharp.md)のガイドに記載されています。

このクイック スタートのサービス側 Node.js アプリケーションには、以下の機能があります。

* IoT デバイスへのデバイス ストリームを作成します。

* コマンド ラインから入力を読み込み、それをデバイス アプリケーションに送信します。デバイス アプリケーションは、それをエコーバックします。

コードは、デバイス ストリームの開始プロセスと、それを使用してデータを送受信する方法を示しています。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

デバイス ストリームのプレビューは現在、次のリージョンで作成された IoT Hub に対してのみサポートされています。

  - **米国中部**
  - **米国中部 EUAP**

このクイック スタートのサービス側アプリケーションを実行するには、開発用マシンに Node.js v4.x.x 以降が必要です。

複数のプラットフォームに対応する Node.js を [Node.js.org](https://nodejs.org) からダウンロードできます。

開発コンピューターに現在インストールされている Node.js のバージョンは、次のコマンドを使って確認できます。

```
node --version
```

次のコマンドを実行して、Microsoft Azure IoT Extension for Azure CLI を Cloud Shell インスタンスに追加します。 IoT Hub、IoT Edge、IoT Device Provisioning Service (DPS) 固有のコマンドが Azure CLI に追加されます。

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

まだ行っていない場合は、 https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip からサンプル Node.js プロジェクトをダウンロードし、ZIP アーカイブを抽出します。


## <a name="create-an-iot-hub"></a>IoT Hub の作成

前出の[デバイスから IoT ハブへの利用統計情報の送信に関するクイック スタート](quickstart-send-telemetry-node.md)を完了した場合は、この手順を省略できます。

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]


## <a name="register-a-device"></a>デバイスの登録

前出の[デバイスから IoT ハブへの利用統計情報の送信に関するクイック スタート](quickstart-send-telemetry-node.md)を完了した場合は、この手順を省略できます。

デバイスを IoT ハブに接続するには、あらかじめ IoT ハブに登録しておく必要があります。 このクイック スタートでは、Azure Cloud Shell を使用して、シミュレートされたデバイスを登録します。

1. Azure Cloud Shell で次のコマンドを実行してデバイス ID を作成します。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

   **MyDevice**: これは、登録済みデバイスに付けられた名前です。 示されているように、MyDevice を使用します。 デバイスに別の名前を選択した場合は、この記事全体でその名前を使用する必要があります。また、サンプル アプリケーションを実行する前に、アプリケーション内のデバイス名を更新してください。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. また、バックエンド アプリケーションが IoT ハブに接続してメッセージを取得できるようにするには、"_サービス接続文字列_" が必要です。 次のコマンドを実行すると、IoT ハブのサービス接続文字列が取得されます。

    **YourIoTHubName**:このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    次のような戻り値をメモしておきます。

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`


## <a name="communicate-between-device-and-service-via-device-streams"></a>デバイス ストリームを介したデバイスとサービスの間の通信

### <a name="run-the-device-side-application"></a>デバイス側アプリケーションの実行

前述のように、IoT Hub Node.js SDK ではサービス側のデバイス ストリームのみがサポートされます。 デバイス側アプリケーションについては、[C クイック スタート](./quickstart-device-streams-echo-c.md)または [C# クイック スタート](./quickstart-device-streams-echo-csharp.md)のガイドに記載されている対応するデバイス プログラムを使用してください。 次の手順に進む前に、デバイス側アプリケーションが実行されていることを確認します。


### <a name="run-the-service-side-application"></a>サービス側アプリケーションの実行

デバイス側アプリケーションが実行されているとして、Node.js でサービス側アプリケーションを実行するには、次の手順に従います。

- サービス資格情報とデバイス ID を環境変数として指定します。
  ```
  # In Linux
  export IOTHUB_CONNECTION_STRING="<provide_your_service_connection_string>"
  export STREAMING_TARGET_DEVICE="MyDevice"

  # In Windows
  SET IOTHUB_CONNECTION_STRING=<provide_your_service_connection_string>
  SET STREAMING_TARGET_DEVICE=MyDevice
  ```
  `MyDevice` を、デバイス用に選択したデバイス ID に変更します。

- 解凍したプロジェクト フォルダーの `Quickstarts/device-streams-service` に移動し、ノードを使用してサンプルを実行します。
  ```
  cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service
  
  # Install the preview service SDK, and other dependencies
  npm install azure-iothub@streams-preview
  npm install

  node echo.js
  ```

最後の手順の終わりに、サービス側プログラムはデバイスへのストリームを開始し、確立後はストリームを介してサービスに文字列バッファーを送信します。 このサンプルでは、​​サービス側プログラムは単にターミナルの stdin を読み取り、それをデバイスに送信します。その後、デバイスはそれをエコーバックします。 これによって、2 つのアプリケーション間の双方向通信が成功したことが示されます。

サービス側のコンソール出力: ![代替テキスト](./media/quickstart-device-streams-echo-nodejs/service-console-output.PNG "サービス側のコンソール出力")


その後、もう一度 Enter キーを押すと、プログラムを終了することができます。


## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]


## <a name="next-steps"></a>次の手順

このクイック スタートでは、IoT ハブの設定、デバイスの登録、デバイス側とサービス側のアプリケーション間のデバイス ストリームの確立、そのストリームを使用したアプリケーション間のデータのやり取りを行いました。

以下のリンクを使用して、デバイス ストリームについてさらに詳しく学習します。

> [!div class="nextstepaction"]
> [デバイス ストリームの概要](./iot-hub-device-streams-overview.md)
