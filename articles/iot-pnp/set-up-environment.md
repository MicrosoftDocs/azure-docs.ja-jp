---
title: クイックスタート - IoT プラグ アンド プレイに必要な IoT リソースを設定する | Microsoft Docs
description: クイックスタート - IoT プラグ アンド プレイのクイックスタートとチュートリアルで使用する IoT ハブと Device Provisioning Service インスタンスを作成します。
author: dominicbetts
ms.author: dobett
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5446f8481439dabffe67f9b00b5f65b1da495746
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742655"
---
# <a name="quickstart-set-up-your-environment-for-the-iot-plug-and-play-quickstarts-and-tutorials"></a>クイックスタート: IoT プラグ アンド プレイのクイックスタートとチュートリアル用に環境を設定する

IoT プラグ アンド プレイのクイックスタートとチュートリアルのいずれかを完了する前に、Azure サブスクリプションで IoT ハブと Device Provisioning Service (DPS) を構成する必要があります。 また、サンプル アプリケーションと Azure IoT エクスプローラー ツールで使用されるモデル ファイルのローカル コピーも必要になります。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

Azure CLI をローカルにインストールする必要がないようにするために、Azure Cloud Shell を使用してクラウド サービスを設定できます。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resources"></a>リソースの作成

リソース用の Azure リソース グループを作成します。

```azurecli-interactive
az group create --name my-pnp-resourcegroup --location centralus
```

IoT Hub を作成します。 次のコマンドの場合、作成する IoT ハブの名前の例として、`my-pnp-hub` という名前を使用します。 ご利用の IoT ハブには、`my-pnp-hub` の代わりに使用する一意の名前を選んでください。

```azurecli-interactive
az iot hub create --name my-pnp-hub --resource-group my-pnp-resourcegroup --sku F1 --partition-count 2
```

DPS インスタンスを作成します。 次のコマンドの場合、作成する DPS インスタンスの名前の例として、`my-pnp-dps` という名前を使用します。 ご利用の DPS インスタンスには、`my-pnp-dps` の代わりに使用する一意の名前を選んでください。

```azurecli-interactive
az iot dps create --name my-pnp-dps --resource-group my-pnp-resourcegroup
```

DPS インスタンスを IoT ハブにリンクするには、次のコマンドを使用します。 `my-pnp-dps` と `my-pnp-hub` は、先ほど選んだ一意の名前に置き換えます。

```azurecli-interactive
hubConnectionString=$(az iot hub connection-string show -n my-pnp-hub --key primary --query connectionString -o tsv)
az iot dps linked-hub create --dps-name my-pnp-dps --resource-group my-pnp-resourcegroup --location centralus --connection-string $hubConnectionString
```

## <a name="retrieve-the-settings"></a>設定を取得する

一部のクイックスタートとチュートリアルで、IoT ハブに接続文字列を使用します。 Azure IoT エクスプローラー ツールを設定するときにも、接続文字列が必要です。 接続文字列を取得し、ここでメモしておきます。 `my-pnp-hub` は、IoT ハブ用に選んだ一意の名前に置き換えます。

```azurecli-interactive
az iot hub connection-string show -n my-pnp-hub --key primary --query connectionString
```

ほとんどのクイックスタートとチュートリアルで、DPS 構成の "*ID スコープ*" を使用します。 ID スコープを取得し、ここでメモしておきます。 `my-pnp-dps` は、DPS インスタンス用に選んだ一意の名前に置き換えます。

```azurecli-interactive
az iot dps show --name my-pnp-dps --query properties.idScope
```

すべてのクイックスタートとチュートリアルで、DPS デバイス登録を使用します。 次のコマンドを使用して、DPS インスタンスに "*個々のデバイス登録*" の `my-pnp-device` を作成します。 `my-pnp-dps` は、DPS インスタンス用に選んだ一意の名前に置き換えます。 クイックスタートとチュートリアルで使用するために、登録 ID とプライマリ キーの値をメモしておきます。

```azurecli-interactive
az iot dps enrollment create --attestation-type symmetrickey --dps-name my-pnp-dps --resource-group my-pnp-resourcegroup --enrollment-id my-pnp-device --device-id my-pnp-device --query '{registrationID:registrationId,primaryKey:attestation.symmetricKey.primaryKey}'
```

## <a name="create-environment-variables"></a>環境変数を作成する

5 つの環境変数を作成し、Device Provisioning Service (DPS) を使用して IoT ハブに接続するようにクイックスタートとチュートリアルのサンプルを構成します。

* **IOTHUB_DEVICE_SECURITY_TYPE**: 値 `DPS`。
* **IOTHUB_DEVICE_DPS_ID_SCOPE**: 先ほどメモした DPS ID スコープ。
* **IOTHUB_DEVICE_DPS_DEVICE_ID**: 値 `my-pnp-device`。
* **IOTHUB_DEVICE_DPS_DEVICE_KEY**: 先ほどメモした登録プライマリ キー。
* **IOTHUB_DEVICE_DPS_ENDPOINT**: 値 `global.azure-devices-provisioning.net`

サービス サンプルには、接続先のハブとデバイスを特定するための次の環境変数が必要です。

* **IOTHUB_CONNECTION_STRING**: 先ほどメモした IoT ハブの接続文字列。
* **IOTHUB_DEVICE_ID**: `my-pnp-device`。

たとえば、Linux の Bash シェルでは次のようになります。

```bash
export IOTHUB_DEVICE_SECURITY_TYPE="DPS"
export IOTHUB_DEVICE_DPS_ID_SCOPE="<Your ID scope>"
export IOTHUB_DEVICE_DPS_DEVICE_ID="my-pnp-device"
export IOTHUB_DEVICE_DPS_DEVICE_KEY="<Your enrolment primary key>"
export IOTHUB_DEVICE_DPS_ENDPOINT="global.azure-devices-provisioning.net"
export IOTHUB_CONNECTION_STRING="<Your IoT hub connection string>"
export IOTHUB_DEVICE_ID="my-pnp-device"
```

たとえば、Windows のコマンド ラインでは次のようになります。

```cmd
set IOTHUB_DEVICE_SECURITY_TYPE=DPS
set IOTHUB_DEVICE_DPS_ID_SCOPE=<Your ID scope>
set IOTHUB_DEVICE_DPS_DEVICE_ID=my-pnp-device
set IOTHUB_DEVICE_DPS_DEVICE_KEY=<Your enrolment primary key>
set IOTHUB_DEVICE_DPS_ENDPOINT=global.azure-devices-provisioning.net
set IOTHUB_CONNECTION_STRING=<Your IoT hub connection string>
set IOTHUB_DEVICE_ID=my-pnp-device
```

## <a name="download-the-model-files"></a>モデル ファイルをダウンロードする

クイックスタートとチュートリアルで、温度コントローラーおよびサーモスタット デバイスにサンプル モデル ファイルを使用します。 サンプル モデル ファイルをダウンロードするには、次のようにします。

1. ローカル コンピューターに "*モデル*" という名前のフォルダーを作成します。

1. [TemperatureController.json](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/TemperatureController.json) を右クリックして、JSON ファイルを "*モデル*" フォルダーに保存します。

1. [Thermostat.json](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/Thermostat.json) を右クリックして、JSON ファイルを "*モデル*" フォルダーに保存します。

## <a name="install-the-azure-iot-explorer"></a>Azure IoT Explorer をインストールする

クイックスタートとチュートリアルで **Azure IoT エクスプローラー** ツールを使用します。 [Azure IoT エクスプローラーのリリース情報](https://github.com/Azure/azure-iot-explorer/releases)に移動し、最新のリリースのアセットの一覧を展開します。 ご利用のオペレーティング システム用の最新バージョンのアプリケーションをダウンロードしてインストールします。

ツールを初めて実行するときは、IoT ハブの接続文字列を入力するように求められます。 先ほどメモした接続文字列を使用します。

先ほどダウンロードしたモデル ファイルを使用するようにツールを構成します。 ツールのホーム ページで、 **[IoT プラグ アンド プレイの設定]** を選択し、 **[+ 追加] > [ローカル フォルダー]** の順に選択します。 先ほど作成した *models* フォルダーを選択します。 その後、 **[保存]** を選択して設定を保存します。

詳細については、「[Azure IoT エクスプローラーをインストールして使用する](howto-use-iot-explorer.md)」を参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

すべての IoT プラグ アンド プレイのクイックスタートとチュートリアルで、IoT ハブと DPS インスタンスを使用することができます。そのため、この記事の手順を行う必要があるのは 1 回だけです。 完了したら、次のコマンドを使用してサブスクリプションからそれらを削除できます。

```azurecli-interactive
az group delete --name my-pnp-resourcegroup
```

## <a name="next-steps"></a>次の手順

環境を設定したので、次のようなクイックスタートまたはチュートリアルのいずれかを試すことができます。

> [!div class="nextstepaction"]
> [サンプルの IoT プラグ アンド プレイ デバイス アプリケーションを IoT Hub に接続する](quickstart-connect-device.md)
