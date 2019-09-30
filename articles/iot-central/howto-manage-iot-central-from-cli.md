---
title: Azure CLI から IoT Central を管理する | Microsoft Docs
description: Azure CLI から IoT Central を管理する
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 1ec83541c62d93eee91348531797ecdeb8c9fc6e
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70873485"
---
# <a name="manage-iot-central-from-azure-cli"></a>Azure CLI から IoT Central を管理する

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

[Azure IoT Central アプリケーション マネージャー](https://aka.ms/iotcentral) Web サイト上で IoT Central アプリケーションを作成および管理するのではなく、[Azure CLI](/cli/azure/) を使用してアプリケーションを管理できます。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

ローカル コンピューター上で Azure CLI を実行する場合は、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」をご覧ください。 Azure CLI をローカルで実行する場合は、この記事にあるコマンドを試行する前に、**az login** コマンドを使用して Azure にサインインします。

## <a name="create-an-application"></a>アプリケーションの作成

[az iotcentral app create](/cli/azure/iotcentral/app#az-iotcentral-app-create) コマンドを使用して、Azure サブスクリプション内に IoT Central アプリケーションを作成します。 例:

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iotcentral app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku S1 --template "iotc-demo@1.0.0" \
  --display-name "My Custom Display Name"
```

これらのコマンドでは、最初に、米国東部リージョンにアプリケーション用のリソース グループを作成します。 次の表では、**az iotcentral app create** コマンドに使用されるパラメーターを説明しています。

| パラメーター         | 説明 |
| ----------------- | ----------- |
| resource-group    | そのアプリケーションを含むリソース グループ。 サブスクリプションにこのリソース グループが既に存在している必要があります。 |
| location          | このコマンドでは既定で、リソース グループの場所が使用されます。 現在、IoT Central アプリケーションは、**米国東部**、**米国西部**、**北ヨーロッパ**、**西ヨーロッパ**のリージョン、または**オーストラリア**地域で作成できます。 |
| 名前              | Azure portal 内のアプリケーションの名前。 |
| subdomain         | アプリケーションの URL のサブドメイン。 この例では、アプリケーションの URL は https://mysubdomain.azureiotcentral.com です。 |
| sku               | 現在使用できる値は **S1** (Standard レベル) のみです。 「[Azure IoT Central の価格](https://azure.microsoft.com/pricing/details/iot-central/)」を参照してください。 |
| template          | 使用するアプリケーション テンプレート。 詳細については、後の表を参照してください。 |
| display-name      | UI に表示されるアプリケーションの名前。 |

**アプリケーション テンプレート**

| テンプレート名            | 説明 |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | 独自のデバイス テンプレートおよびデバイスにデータを入力するための空のアプリケーションを作成します。 |
| iotc-demo@1.0.0          | Refrigerated Vending Machine 用に既に作成したデバイス テンプレートを含むアプリケーションを作成します。 このテンプレートは、Azure IoT Central の調査を開始するために使用します。 |
| iotc-devkit-sample@1.0.0 | MXChip または Raspberry Pi デバイスを接続するための準備ができたデバイス テンプレートを含むアプリケーションを作成します。 このテンプレートは、これらのいずれかのデバイスで実験しているデバイス開発者が使用します。 |

> [!NOTE]
> 現在、**プレビュー アプリケーション** テンプレートは、**北ヨーロッパ**と**米国中部**のリージョンでのみ利用できます。

## <a name="view-your-applications"></a>アプリケーションを表示する

[az iotcentral app list](/cli/azure/iotcentral/app#az-iotcentral-app-list) コマンドを使用して、IoT Central アプリケーションを一覧表示し、メタデータを表示します。

## <a name="modify-an-application"></a>アプリケーションの変更

[az iotcentral app update](/cli/azure/iotcentral/app#az-iotcentral-app-update) コマンドを使用して、IoT Central アプリケーションのメタデータを更新します。 アプリケーションの表示名を変更する場合の例を次に示します。

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>アプリケーションの削除

[az iotcentral app delete](/cli/azure/iotcentral/app#az-iotcentral-app-delete) コマンドを使用して、IoT Central アプリケーションを削除します。 例:

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>次の手順

ここでは、Azure CLI から Azure IoT Central アプリケーションを管理する方法について説明しました。推奨される次の手順は以下のとおりです。

> [!div class="nextstepaction"]
> [アプリケーションを管理する](howto-administer.md)
