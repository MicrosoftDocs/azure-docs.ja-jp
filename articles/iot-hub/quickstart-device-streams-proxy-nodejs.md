---
title: SSH または RDP 用の Azure IoT Hub デバイス ストリームの Node.js クイック スタート (プレビュー) | Microsoft Docs
description: このクイック スタートでは、IoT Hub デバイス ストリームを介した SSH シナリオまたは RDP シナリオを有効にするためにプロキシとして機能するサンプル Node.js アプリケーションを実行します。
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: a459473e04f9cbf3b11b75f3b9dbea2732455084
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59005437"
---
# <a name="quickstart-sshrdp-over-iot-hub-device-streams-using-nodejs-proxy-application-preview"></a>クイック スタート:Node.js プロキシ アプリケーションを使用した IoT Hub デバイス ストリーム経由の SSH または RDP (プレビュー)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub は現在、[プレビュー機能](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)としてデバイス ストリームをサポートしています。

[IoT Hub デバイス ストリーム](./iot-hub-device-streams-overview.md)を使用すると、サービス アプリケーションとデバイス アプリケーションが、安全でファイアウォールに対応した方法で通信できます。 このクイック スタート ガイドでは、SSH および RDP トラフィックをデバイス ストリームを介してデバイスに送信できるようにするためのサービス側での Node.js プロキシ アプリケーションの実行について説明します。 設定の概要については、[こちら](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp)を参照してください。 パブリック プレビュー中、Node.js SDK ではサービス側のデバイス ストリームのみがサポートされます。 そのため、このクイック スタート ガイドでは、サービスローカルのプロキシを実行する手順についてのみ説明しています。 対応するサービスローカルのプロキシも実行する必要があり、それについては [C クイック スタート](./quickstart-device-streams-proxy-c.md)または [C# クイック スタート](./quickstart-device-streams-proxy-csharp.md)のガイドに記載されています。

まず、SSH (ポート 22 を使用) の設定について説明します。 その後、RDP (ポート 3389 を使用) の設定を変更する方法を説明します。 デバイス ストリームはアプリケーションやプロトコルに依存しないため、(通常は通信ポートを変更することによって) 同じサンプルを他の種類のクライアント/サーバー アプリケーション トラフィックに対応するように変更できます。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

デバイス ストリームのプレビューは現在、次のリージョンで作成された IoT Hub に対してのみサポートされています。

  - **米国中部**
  - **米国中部 EUAP**

このクイック スタートのサービスローカルのアプリケーションを実行するには、開発用マシンに Node.js v4.x.x 以降が必要です。

複数のプラットフォームに対応する Node.js を [nodejs.org](https://nodejs.org) からダウンロードできます。

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

## <a name="ssh-to-a-device-via-device-streams"></a>デバイス ストリームを介したデバイスへの SSH 接続

### <a name="run-the-device-local-proxy"></a>デバイスローカルのプロキシの実行

前述のように、IoT Hub Node.js SDK ではサービス側のデバイス ストリームのみがサポートされます。 デバイスローカルのアプリケーションについては、[C クイック スタート](./quickstart-device-streams-proxy-c.md)または [C# クイック スタート](./quickstart-device-streams-proxy-csharp.md)のガイドに記載されている対応するデバイス プロキシ プログラムを使用してください。 次の手順に進む前に、デバイスローカルのプロキシが実行されていることを確認します。

### <a name="run-the-service-local-proxy"></a>サービスローカルのプロキシの実行

[デバイスローカルのプロキシ](#run-the-device-local-proxy)が実行されているとして、Node.js で記述されたサービスローカルのプロキシを実行するには、次の手順に従います。

- サービス資格情報、SSH デーモンが実行されているターゲット デバイスの ID、およびデバイスで実行されているプロキシのポート番号を、環境変数として指定します。
  ```
  # In Linux
  export IOTHUB_CONNECTION_STRING="<provide_your_service_connection_string>"
  export STREAMING_TARGET_DEVICE="MyDevice"
  export PROXY_PORT=2222

  # In Windows
  SET IOTHUB_CONNECTION_STRING=<provide_your_service_connection_string>
  SET STREAMING_TARGET_DEVICE=MyDevice
  SET PROXY_PORT=2222
  ```
  実際のデバイス ID と接続文字列に合わせて上記の値を変更します。

- 解凍したプロジェクト フォルダーの `Quickstarts/device-streams-service` に移動し、サービスローカルのプロキシを実行します。
  ```
  cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

  # Install the preview service SDK, and other dependencies
  npm install azure-iothub@streams-preview
  npm install

  # Run the service-local proxy application
  node proxy.js
  ```

### <a name="ssh-to-your-device-via-device-streams"></a>デバイス ストリームを介したデバイスへの SSH 接続

Linux では、ターミナルで `ssh $USER@localhost -p 2222` を使用して SSH を実行します。 Windows では、任意の SSH クライアント (PuTTY など) を使用します。

SSH セッションが確立された後のサービスローカルのコンソール出力 (サービスローカルのプロキシはポート 2222 をリッスンします):![代替テキスト](./media/quickstart-device-streams-proxy-nodejs/service-console-output.PNG "SSH ターミナルの出力")

SSH クライアント プログラムのコンソール出力 (SSH クライアントは、サービスローカルのプロキシがリッスンしているポート 22 に接続することで、SSH デーモンと通信します):![代替テキスト](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.PNG "SSH クライアントの出力")

### <a name="rdp-to-your-device-via-device-streams"></a>デバイス ストリームを介したデバイスへの RDP 接続

次に、RDP クライアント プログラムを使用し、ポート 2222 (これは、前に選択した任意の使用可能なポートです) でサービス プロキシに接続します。

> [!NOTE]
> デバイス プロキシが RDP 用に正しく構成され、RDP ポートが 3389 で構成されていることを確認してください。

![代替テキスト](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.PNG "RDP クライアントがサービスローカルのプロキシに接続します。")

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>次の手順

このクイック スタートでは、IoT ハブの設定、デバイスの登録、およびサービス プロキシ プログラムのデプロイによる IoT デバイスへの RDP および SSH 接続の有効化を行いました。 RDP および SSH トラフィックは、IoT Hub へのデバイス ストリームを介してトンネリングされます。 これにより、デバイスへの直接接続の必要性がなくなります。

以下のリンクを使用して、デバイス ストリームについてさらに詳しく学習します。

> [!div class="nextstepaction"]
> [デバイス ストリームの概要](./iot-hub-device-streams-overview.md)
