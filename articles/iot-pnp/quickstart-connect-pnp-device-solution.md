---
title: Azure IoT ソリューションに接続されている IoT プラグ アンド プレイ プレビュー デバイスとやりとりする | Microsoft Docs
description: Node.js を使用して、ご利用の Azure IoT ソリューションに接続されている IoT プラグ アンド プレイ プレビュー デバイスと接続してやりとりします。
author: miagdp
ms.author: miag
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: a082e4b7896b317bf2b28971d3693bada95a3445
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806544"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution"></a>クイック スタート:ご利用のソリューションに接続されている IoT プラグ アンド プレイ プレビュー デバイスとやりとりする

IoT プラグ アンド プレイ プレビューを使用すると、基礎となるデバイスの実装に関する知識がなくてもデバイスの機能とやりとりできるので、IoT が簡略化されます。 このクイックスタートでは、Node.js を使用して、ご利用のソリューションに接続されている IoT プラグ アンド プレイ デバイスに接続して制御する方法について説明します。

## <a name="prerequisites"></a>前提条件

Node.js をダウンロードし、[nodejs.org](https://nodejs.org) からインストールします。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>IoT ハブを準備する

また、このクイックスタートを完了するには、ご利用の Azure サブスクリプション内に Azure IoT ハブが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

> [!NOTE]
> パブリック プレビュー中、IoT プラグ アンド プレイ機能は、**米国中部**、**北ヨーロッパ**、および**東日本**の各リージョンで作成された IoT ハブでのみご利用いただけます。

Azure CLI 用の Microsoft Azure IoT 拡張機能を追加します。

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

次のコマンドを実行して、ご利用の IoT ハブに新しいデバイス ID を作成します。 **YourIoTHubName** と **YourDevice** を実際に使用する名前に置き換えます。 IoT Hub をお持ちではない場合は、[次の手順](../iot-hub/iot-hub-create-using-cli.md)に従って作成します。

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id [YourDevice]
```

次のコマンドを実行して、登録したばかりのデバイス用の_デバイス接続文字列_を取得します。

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id [YourDevice] --output table
```

次のコマンドを実行して、ご利用の IoT ハブに対する _IoT ハブ接続文字列_を取得します。

```azurecli-interactive
az iot hub show-connection-string --hub-name [YourIoTHubName] --output table
```

## <a name="connect-your-device"></a>ご利用のデバイスを接続する

このクイックスタートでは、Node.js 内に IoT プラグ アンド プレイ デバイスとして記述された環境センサーのサンプルを使用します。 次の手順では、デバイスをインストールして実行する方法を示します。

1. GitHub リポジトリを複製する:

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. ターミナルで、複製されたリポジトリのルート フォルダーに移動し、さらに **/azure-iot-samples-node/digital-twins/Quickstarts/Device** フォルダーに移動してから、次のコマンドを実行してすべての依存関係をインストールします。

    ```cmd/sh
    npm install
    ```

1. _デバイス接続文字列_を構成します。

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<your device connection string>
    ```

1. 次のコマンドを実行して、サンプルを実行します。

    ```cmd/sh
    node sample_device.js
    ```

1. テレメトリとそのプロパティがデバイスから送信されたことを示すメッセージが表示されます。 これで、デバイスでコマンドとプロパティの更新情報を受け取る準備ができました。 このターミナルは閉じないでください。後でサービスのサンプルも動作していることを確認するために必要になります。

## <a name="build-the-solution"></a>ソリューションをビルドします。

このクイックスタートでは、Node.js 内の IoT ソリューションのサンプルを使用してサンプル デバイスとやりとりします。

1. 別のターミナルを開きます。 複製したリポジトリのフォルダーに移動し、さらに **/azure-iot-samples-node/digital-twins/Quickstarts/Service** フォルダーに移動します。 次のコマンドを実行して、すべての依存関係をインストールします。

    ```cmd/sh
    npm install
    ```

1. _ハブ接続文字列_を構成します。

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<your hub connection string>
    ```

### <a name="read-a-property"></a>プロパティを読み取る

1. ターミナルでデバイスに接続すると、次のメッセージが表示されます。

    ```cmd/sh
    reported state property as online
    ```

1. ファイル **get_digital_twin.js** を開きます。 `deviceID` をご利用のデバイス ID に置き換え、ファイルを保存します。

1. サービスのサンプルを実行するために開いたターミナルにアクセスし、次のコマンドを実行します。

    ```cmd/sh
    node get_digital_twin.js
    ```

1. 出力では、_environmentalSensor_ コンポーネントの下に、同じ状態が報告されているのがわかります。

    ```JSON
    reported state property as online
    ```

### <a name="update-a-writable-property"></a>書き込み可能なプロパティを更新する

1. ファイル **update_digital_twin_property.js** を開きます。

1. ファイルの先頭には、大文字のプレースホルダーを使用して定義された一連の定数があります。 **deviceID** を実際のデバイス ID に置き換え、定数を次の値で更新して、ファイルを保存します。

    ```javascript
    const componentName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 60;
    ```

1. サービスのサンプルを実行するために開いたターミナルにアクセスし、次のコマンドを使用してサンプルを実行します。

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. ターミナルには、ご利用のデバイスに関連付けられているデジタル ツイン情報が表示されます。 コンポーネント _environmentalSensor_ を検索し、新しい明度値が 60 になっていることを確認します。

    ```json
        "environmentalSensor": {
        "name": "environmentalSensor",
        "properties": {
          "brightness": {
            "reported": {
              "value": 60,
              "desiredState": {
                "code": 200,
                "version": 14,
                "description": "helpful descriptive text"
              }
            },
            "desired": {
              "value": 60
            }
          },
          "state": {
            "reported": {
              "value": "online"
            }
          }
        }
      }
    ```

1. _デバイス_ ターミナルにアクセスして、デバイスが更新プログラムを受信済みであることを確認します。

    ```cmd/sh
    Received an update for brightness: 60
    updated the property
    ```
2. _サービス_ ターミナルに戻り、次のコマンドをもう一度実行して、プロパティが更新されたことを確認します。
    
    ```cmd/sh
    node get_digital_twin.js
    ```
3. 出力内の environmentalSensor コンポーネントの下を見ると、更新された明度値が報告されていることがわかります。 注: デバイスが更新を完了するまで、しばらく時間がかかる場合があります。 デバイスによってプロパティの更新が実際に処理されるまで、この手順は繰り返すことができます。
    
    ```json
      "brightness": {
        "reported": {
          "value": 60,
          }
       }
    ```

### <a name="invoke-a-command"></a>コマンドを呼び出す

1. ファイル **invoke_command.js** を開きます。

1. ファイルの先頭にある `deviceID` を実際のデバイス ID に置き換えます。 定数を次の値で更新して、ファイルを保存します。

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    ```

1. サービスのサンプルを実行するために開いたターミナルにアクセスします。 次のコマンドを使用して、サンプルを実行します。

    ```cmd/sh
    node invoke_command.js
    ```

1. ターミナルでは、正常に完了すると次のような出力が表示されます。

    ```cmd/sh
    invoking command blink on component environmentalSensor for device test...
    {
      "result": "helpful response text",
      "statusCode": 200,
      "requestId": "33e536d3-14f7-4105-88f3-629b9933851c",
      "_response": "helpful response text"
    }
    ```

1. _デバイス_ ターミナルにアクセスすると、コマンドが確認済みであることがわかります。

    ```cmd/sh
    received command: blink for component: environmentalSensor
    acknowledgement succeeded.
    ```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

引き続き以降の記事に進む場合は、このクイックスタートで使用したリソースを保持することができます。 それ以外の場合は、追加料金が発生するのを防ぐために、このクイックスタート用に作成したリソースは削除できます。

ハブと登録されているデバイスを削除するには、Azure CLI を使用して次の手順を行います。

```azurecli-interactive
az group delete --name <Your group name>
```

ご利用の IoT Hub に登録されたデバイスのみを削除するには、Azure CLI を使用して次の手順を行います。

```azurecli-interactive
az iot hub device-identity delete --hub-name [YourIoTHubName] --device-id [YourDevice]
```

## <a name="next-steps"></a>次の手順

このクイックスタートでは、IoT プラグ アンド プレイ デバイスを IoT ソリューションに接続する方法を学習しました。 ご利用の IoT プラグ アンド プレイ デバイスとやりとりするソリューションを構築する方法の詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [方法: デバイスに接続してやりとりする](howto-develop-solution.md)
