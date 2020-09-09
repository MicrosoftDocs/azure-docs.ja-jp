---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 03/17/2020
ms.custom: references_regions
ms.openlocfilehash: adc621f932462422202d9f16fd539f5ecc7c3d8e
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87336900"
---
## <a name="prepare-an-iot-hub"></a>IoT ハブを準備する

この記事のステップを完了するには、ご利用の Azure サブスクリプション内に Azure IoT ハブが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

Azure CLI をローカルで使用している場合は、最初に `az login` を使用して Azure サブスクリプションにサインインします。 Azure Cloud Shell でこれらのコマンドを実行している場合は、自動的にサインインされます。

Azure CLI をローカルで使用している場合、`az` のバージョンは **2.8.0** 以降である必要があります。Azure Cloud Shell では最新バージョンが使用されています。 マシンにインストールされているバージョンを確認するには、`az --version` コマンドを使用します。

次のコマンドを実行して、Microsoft Azure IoT Extension for Azure CLI をお使いのインスタンスに追加します。

```azurecli-interactive
az extension add --name azure-iot
```

使用する IoT ハブがまだない場合は、次のコマンドを実行して、サブスクリプション内にリソース グループと Free レベルの IoT ハブを作成します。 `<YourIoTHubName>` を任意のハブ名に置き換えます。

```azurecli-interactive
az group create --name my-pnp-resourcegroup \
    --location centralus
az iot hub create --name <YourIoTHubName> \
    --resource-group my-pnp-resourcegroup --sku F1
```

> [!NOTE]
> 現在、IoT プラグ アンド プレイは、米国中部、北ヨーロッパ、東日本の各リージョンで作成された IoT ハブで利用できます。 IoT プラグ アンド プレイ サポートは、Basic レベルの IoT ハブには含まれていません。

次のコマンドを実行して、ご利用の IoT ハブに新しいデバイス ID を作成します。 `<YourIoTHubName>` と `<YourDeviceID>` のプレースホルダーを、任意の "_IoT ハブ名_" と "_デバイス ID_" に置き換えます。

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```
