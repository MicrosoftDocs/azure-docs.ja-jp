---
title: IoT プラグ アンド プレイ プレビューのサンプル Node.js デバイス コードを Azure IoT Hub に接続する | Microsoft Docs
description: Node.js を使用して、IoT ハブと接続する IoT プラグ アンド プレイ プレビューのサンプル デバイス コードをビルドして実行します。 Azure IoT Explorer ツールを使用して、デバイスからハブに送信された情報を表示します。
author: ericmitt
ms.author: ericmitt
ms.date: 07/10/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc, devx-track-javascript
ms.openlocfilehash: 00a748c3c372f1980042cff201edec720587a511
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87422557"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-nodejs"></a>クイック スタート:サンプルの IoT プラグ アンド プレイ プレビュー デバイス アプリケーションを IoT Hub に接続する (Node.js)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

このクイックスタートでは、IoT プラグ アンド プレイ デバイス アプリケーションのサンプルをビルドし、それをご利用の IoT ハブに接続し、送信されるテレメトリを Azure IoT エクスプローラー ツールを使用して表示する方法を示します。 このサンプル アプリケーションは Node.js で記述されており、Node.js 用 Azure IoT device SDK に含まれています。 ソリューション ビルダーは Azure IoT エクスプローラー ツールを使用して、デバイス コードを表示しなくても IoT プラグ アンド プレイ デバイスの機能を理解することができます。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>前提条件

このクイックスタートを完了するには、開発用マシンに Node.js が必要です。 [nodejs.org](https://nodejs.org) から、複数のプラットフォームに対応した最新の推奨バージョンをダウンロードできます。

開発コンピューターに現在インストールされている Node.js のバージョンは、次のコマンドを使って確認できます。

```cmd/sh
node --version
```

### <a name="azure-iot-explorer"></a>Azure IoT エクスプローラー

このクイックスタートのパート 2 でサンプル デバイスとやり取りするには、**Azure IoT エクスプローラー** ツールを使用します。 ご利用のオペレーティング システム用の [Azure IoT エクスプローラーの最新リリースをダウンロードしてインストール](./howto-use-iot-explorer.md)します。

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

次のコマンドを実行して、ご利用のハブに対する "_IoT ハブ接続文字列_" を取得します。 この接続文字列はメモしておいてください。このクイックスタートで後ほど使用します。

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> また、Azure IoT エクスプローラー ツールを使用して、IoT ハブ接続文字列を見つけることもできます。

次のコマンドを実行して、ハブに追加したデバイスの "_デバイス接続文字列_" を取得します。 この接続文字列はメモしておいてください。このクイックスタートで後ほど使用します。

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>コードのダウンロード

このクイックスタートでは、Node.js 用 Azure IoT Hub Device SDK をクローンしてビルドするために使用できる開発環境を準備します。

任意のディレクトリでコマンド プロンプトを開きます。 次のコマンドを実行して、[Node.js 用 Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-node) GitHub リポジトリをこの場所にクローンします。

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="install-required-libraries"></a>必要なライブラリをインストールする

デバイス SDK を使用して、含まれているサンプル コードをビルドします。 ビルドしたアプリケーションでは、IoT ハブに接続するデバイスのシミュレーションが行われます。 アプリケーションによりテレメトリとプロパティが送信され、コマンドが受け取られます。

1. ローカル ターミナル ウィンドウで、クローンしたリポジトリのフォルダーに移動し、 */azure-iot-sdk-node/device/samples/pnp* フォルダーに移動します。 次に、次のコマンドを実行して、必要なライブラリをインストールします。

    ```cmd/sh
    npm install
    ```

1. 先ほどメモしたデバイス接続文字列で、環境変数を構成します。

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-sample-device"></a>サンプル デバイスを実行する

_simple_thermostat.js_ ファイルを開きます。 このファイルでは、以下の方法を確認できます。

1. 必要なインターフェイスをインポートする。
1. プロパティ更新ハンドラーとコマンド ハンドラーを記述する。
1. 必要なプロパティの修正プログラムを処理し、テレメトリを送信する。
1. 必要に応じて、Azure Device Provisioning Service (DPS) を使用して、デバイスをプロビジョニングする。

main 関数では、すべてがどのようにまとめられているかを確認できます。

1. 接続文字列からデバイスを作成するか、DPS を使用してデバイスをプロビジョニングします。
1. IoT プラグ アンド プレイのデバイス モデルを指定するには、**modelID** オプションを使用します。
1. コマンド ハンドラーを有効にします。
1. デバイスからハブにテレメトリを送信します。
1. デバイス ツインを取得し、レポートされたプロパティを更新します。
1. 必要なプロパティ更新ハンドラーを有効にします。

サンプル アプリケーションを実行して、IoT ハブにテレメトリを送信する IoT プラグ アンド プレイ デバイスをシミュレートします。 サンプル アプリケーションを実行するには、次のコマンドを使用します。

```cmd\sh
node simple_thermostat.js
```

次の出力が表示されます。これは、デバイスがハブへの利用統計情報の送信を開始し、コマンドとプロパティの更新情報を受信する準備ができたことを示します。

![デバイス確認メッセージ](media/quickstart-connect-device-node/device-confirmation-node.png)

次の手順を完了するまで、サンプルを実行したままにしておきます。

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Azure IoT エクスプローラーを使用してコードを確認する

デバイス クライアントのサンプルが開始された後、Azure IoT エクスプローラー ツールを使用して、それが動作していることを確認します。

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、IoT プラグ アンド プレイ デバイスを IoT ハブに接続する方法を学習しました。 ご利用の IoT プラグ アンド プレイ デバイスとやりとりするソリューションを構築する方法の詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [ご利用のソリューションに接続されている IoT プラグ アンド プレイ プレビュー デバイスとやり取りする](quickstart-service-node.md)
