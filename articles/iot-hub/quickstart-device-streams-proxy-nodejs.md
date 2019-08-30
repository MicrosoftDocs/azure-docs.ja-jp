---
title: SSH および RDP 用の Azure IoT Hub デバイス ストリームの Node.js クイックスタート (プレビュー) | Microsoft Docs
description: このクイックスタートでは、IoT Hub デバイス ストリームを介した SSH および RDP シナリオを有効にするためにプロキシとして機能するサンプル Node.js アプリケーションを実行します。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 83339273d9161c3947df191d10e788980db39b28
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2019
ms.locfileid: "67446029"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-nodejs-proxy-application-preview"></a>クイック スタート:Node.js プロキシ アプリケーションを使用して IoT Hub デバイス ストリーム経由で SSH および RDP を有効にする (プレビュー)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub は現在、[プレビュー機能](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)としてデバイス ストリームをサポートしています。

[IoT Hub デバイス ストリーム](./iot-hub-device-streams-overview.md)を使用すると、サービス アプリケーションとデバイス アプリケーションが、安全でファイアウォールに対応した方法で通信できます。 

このクイックスタートでは、デバイス ストリームを介して Secure Shell (SSH) およびリモート デスクトップ プロトコル (RDP) トラフィックをデバイスに送信できるようにするためのサービス側での Node.js プロキシ アプリケーションの実行について説明します。 設定の概要については、[ローカル プロキシのサンプル](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp)に関するページを参照してください。 

パブリック プレビュー中、Node.js SDK ではサービス側のみでデバイス ストリームがサポートされます。 そのため、このクイックスタートでは、サービスローカルのプロキシ アプリケーションのみを実行する手順について説明しています。 デバイスローカルのプロキシ アプリケーションを実行するには、以下を参照してください。  

   * [C プロキシ アプリケーションを使用して IoT Hub デバイス ストリーム経由で SSH および RDP を有効にする](./quickstart-device-streams-proxy-c.md)
   * [C# プロキシ アプリケーションを使用して IoT Hub デバイス ストリーム経由で SSH および RDP を有効にする](./quickstart-device-streams-proxy-csharp.md)

この記事では、SSH の設定 (ポート 22 を使用) について説明し、次に RDP の設定 (ポート 3389 を使用) を変更する方法について説明します。 デバイス ストリームはアプリケーションやプロトコルに依存しないため、通常は通信ポートを変更することによって、同じサンプルを他の種類のクライアント/サーバー アプリケーション トラフィックに対応するように変更できます。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

* デバイス ストリームのプレビューは現在、以下のリージョンで作成された IoT ハブに対してのみサポートされています。

  * 米国中部
  * 米国中部 EUAP

* このクイックスタートのサービスローカルのアプリケーションを実行するには、開発マシンに Node.js v10.x.x 以降が必要です。
  * 複数のプラットフォームに対応する [Node.js](https://nodejs.org) をダウンロードします。
  * 開発マシン上の Node.js の現在のバージョンを、次のコマンドを使って確認します。

   ```
   node --version
   ```

* 次のコマンドを実行して、Azure IoT Extension for Azure CLI を Cloud Shell インスタンスに追加します。 IoT Hub、IoT Edge、IoT Device Provisioning Service (DPS) 固有のコマンドが Azure CLI に追加されます。

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

* まだ行っていない場合は、[サンプル Node.js プロジェクトをダウンロード](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip)し、ZIP アーカイブを抽出します。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

前出の[デバイスから IoT ハブへの利用統計情報の送信に関するクイック スタート](quickstart-send-telemetry-node.md)を完了した場合は、この手順を省略できます。

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>デバイスの登録

「[クイックスタート: ](quickstart-send-telemetry-node.md)を完了した場合は、この手順を省略できます。

デバイスを IoT ハブに接続するには、あらかじめ IoT ハブに登録しておく必要があります。 このセクションでは、Azure Cloud Shell を使用して、シミュレートされたデバイスを登録します。

1. Cloud Shell で次のコマンドを実行してデバイス ID を作成します。

   > [!NOTE]
   > * *YourIoTHubName* プレースホルダーを、IoT ハブ用に選択した名前に置き換えます。
   > * 示されているように、*MyDevice* を使用します。 これは、登録済みデバイスに付けられた名前です。 デバイスに別の名前を選択した場合は、この記事全体でその名前を使用し、サンプル アプリケーションを実行する前に、アプリケーション内のデバイス名を更新します。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

1. また、バックエンド アプリケーションが IoT ハブに接続してメッセージを取得できるようにするには、"*サービス接続文字列*" も必要です。 次のコマンドを実行すると、自分の IoT ハブの文字列が取得されます。

   > [!NOTE]
   > *YourIoTHubName* プレースホルダーを、IoT ハブ用に選択した名前に置き換えます。

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    このクイックスタートの後の方で使用するために、返された値を書き留めておきます。 次の例のようになります。

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>デバイス ストリームを介したデバイスへの SSH 接続

このセクションでは、SSH トラフィックをトンネリングするためのエンドツーエンド ストリームを確立します。

### <a name="run-the-device-local-proxy-application"></a>デバイスローカルのプロキシ アプリケーションの実行

前述のとおり、IoT Hub Node.js SDK ではサービス側のデバイス ストリームのみがサポートされます。 デバイスローカルのアプリケーションでは、次のクイックスタートのいずれかに記載されているデバイス プロキシ アプリケーションを使用します。

   * [C プロキシ アプリケーションを使用して IoT Hub デバイス ストリーム経由で SSH および RDP を有効にする](./quickstart-device-streams-proxy-c.md)
   * [C# プロキシ アプリケーションを使用して IoT Hub デバイス ストリーム経由で SSH および RDP を有効にする](./quickstart-device-streams-proxy-csharp.md) 

次の手順に進む前に、デバイスローカルのプロキシ アプリケーションが実行されていることを確認します。

### <a name="run-the-service-local-proxy-application"></a>サービスローカルのプロキシ アプリケーションの実行

デバイスローカルのプロキシ アプリケーションを実行しつつ、次の手順に従って、Node.js で記述されたサービスローカルのプロキシ アプリケーションを実行します。

1. 環境変数として、サービス資格情報、SSH デーモンが実行されているターゲット デバイスの ID、およびデバイスで実行されているプロキシのポート番号を指定します。

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

1. 解凍したプロジェクト フォルダーの *Quickstarts/device-streams-service* ディレクトリに移動し、サービスローカルのプロキシ アプリケーションを実行します。

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

SSH セッションが確立された後のサービスローカルのコンソール出力 (サービスローカルのプロキシ アプリケーションはポート 2222 をリッスンします):

![SSH ターミナルの出力](./media/quickstart-device-streams-proxy-nodejs/service-console-output.png)

SSH クライアント アプリケーションのコンソール出力 (SSH クライアントは、サービスローカルのプロキシ アプリケーションがリッスンしているポート 22 に接続することで、SSH デーモンと通信します):

![SSH クライアントの出力](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.png)

### <a name="rdp-to-your-device-via-device-streams"></a>デバイス ストリームを介したデバイスへの RDP 接続

次に、RDP クライアント アプリケーションを使用し、ポート 2222 (前に選択した任意のポート) でサービス プロキシに接続します。

> [!NOTE]
> デバイス プロキシが RDP 用に正しく構成され、RDP ポートが 3389 で構成されていることを確認してください。

![RDP クライアントがサービスローカルのプロキシ アプリケーションに接続する](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>次の手順

このクイックスタートでは、IoT ハブの設定、デバイスの登録、およびサービス プロキシ アプリケーションのデプロイによる IoT デバイスへの RDP および SSH の有効化を行いました。 RDP および SSH トラフィックは、IoT ハブへのデバイス ストリームを介してトンネリングされます。 このプロセスにより、デバイスへの直接接続の必要性がなくなります。

デバイス ストリームについて詳しく学習するには、次を参照してください。

> [!div class="nextstepaction"]
> [デバイス ストリームの概要](./iot-hub-device-streams-overview.md)
