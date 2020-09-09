---
title: Azure CLI から IoT Central を管理する | Microsoft Docs
description: この記事では、CLI を使用して IoT Central アプリケーションを作成し、管理する方法について説明します。 CLI を使用して、アプリケーションの表示、変更、および削除を行えます。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
ms.custom: devx-track-azurecli
manager: philmea
ms.openlocfilehash: bd87f15ff63edf1da447faf986cad2f9591610dd
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87502972"
---
# <a name="manage-iot-central-from-azure-cli"></a>Azure CLI から IoT Central を管理する

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

[Azure IoT Central アプリケーション マネージャー](https://aka.ms/iotcentral) Web サイト上で IoT Central アプリケーションを作成および管理するのではなく、[Azure CLI](/cli/azure/) を使用してアプリケーションを管理できます。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

ローカル コンピューター上で Azure CLI を実行する場合は、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」をご覧ください。 Azure CLI をローカルで実行する場合は、この記事にあるコマンドを試行する前に、**az login** コマンドを使用して Azure にサインインします。

> [!TIP]
> CLI コマンドを別の Azure サブスクリプションで実行する必要がある場合は、「[アクティブなサブスクリプションを変更する](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#change-the-active-subscription)」を参照してください。

## <a name="install-the-extension"></a>拡張機能をインストールする

この記事のコマンドは、**azure-iot** CLI 拡張機能の一部です。 次のコマンドを実行して拡張機能をインストールします。

```azurecli-interactive
az extension add --name azure-iot
```

## <a name="create-an-application"></a>アプリケーションの作成

[az iot central app create](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-create) コマンドを使用して、Azure サブスクリプション内に IoT Central アプリケーションを作成します。 次に例を示します。

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iot central app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku ST1 --template "iotc-pnp-preview" \
  --display-name "My Custom Display Name"
```

これらのコマンドでは、最初に、米国東部リージョンにアプリケーション用のリソース グループを作成します。 次の表では、**az iot central app create** コマンドに使用されるパラメーターを説明しています。

| パラメーター         | 説明 |
| ----------------- | ----------- |
| resource-group    | そのアプリケーションを含むリソース グループ。 サブスクリプションにこのリソース グループが既に存在している必要があります。 |
| location          | このコマンドでは既定で、リソース グループの場所が使用されます。 現時点では、IoT Central アプリケーションは、**オーストラリア**、**アジア太平洋**、**ヨーロッパ**、または**米国**、**イギリス**、**日本**の地域で作成できます。 |
| name              | Azure portal 内のアプリケーションの名前。 |
| subdomain         | アプリケーションの URL のサブドメイン。 この例では、アプリケーションの URL は `https://mysubdomain.azureiotcentral.com` です。 |
| sku               | 現在、**ST1** または **ST2** のいずれかを使用できます。 「[Azure IoT Central の価格](https://azure.microsoft.com/pricing/details/iot-central/)」を参照してください。 |
| template          | 使用するアプリケーション テンプレート。 詳細については、後の表を参照してください。 |
| display-name      | UI に表示されるアプリケーションの名前。 |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-applications"></a>アプリケーションを表示する

[az iot central app list](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-list) コマンドを使用して、IoT Central アプリケーションを一覧表示し、メタデータを表示します。

## <a name="modify-an-application"></a>アプリケーションの変更

[az iot central app update](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-update) コマンドを使用して、IoT Central アプリケーションのメタデータを更新します。 アプリケーションの表示名を変更する場合の例を次に示します。

```azurecli-interactive
az iot central app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>アプリケーションの削除

[az iot central app delete](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-delete) コマンドを使用して、IoT Central アプリケーションを削除します。 次に例を示します。

```azurecli-interactive
az iot central app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>次のステップ

ここでは、Azure CLI から Azure IoT Central アプリケーションを管理する方法について説明しました。推奨される次の手順は以下のとおりです。

> [!div class="nextstepaction"]
> [アプリケーションを管理する](howto-administer.md)
