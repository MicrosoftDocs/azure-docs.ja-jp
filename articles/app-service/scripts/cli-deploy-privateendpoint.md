---
title: CLI:Azure CLI を使用して Web アプリ用のプライベート エンドポイントをデプロイする
description: Azure CLI を使用して Web アプリ用のプライベート エンドポイントをデプロイする方法について説明します
author: ericgre
ms.assetid: a56faf72-7237-41e7-85ce-da8346f2bcaa
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/06/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: acebc75b579b13ebb2cfad0e18057245781165ad
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175279"
---
# <a name="create-an-app-service-app-and-deploy-private-endpoint-using-azure-cli"></a>Azure CLI を使用した App Service アプリの作成とプライベート エンドポイントのデプロイ

このサンプル スクリプトでは、App Service でアプリを関連リソースと共に作成し、プライベート エンドポイントをデプロイします。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - このチュートリアルには、Azure CLI のバージョン 2.0.28 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-resource-group"></a>リソース グループを作成する

リソースを作成する前に、Web アプリをホストするリソース グループ、仮想ネットワーク、その他のネットワーク コンポーネントを作成する必要があります。 [az group create](/cli/azure/group) を使用して、リソース グループを作成します。 この例では、*francecentral* の場所に *myResourceGroup* という名前のリソース グループを作成します。

```azurecli-interactive
az group create --name myResourceGroup --location francecentral 
```

## <a name="create-an-app-service-plan"></a>App Service プランを作成する

Web アプリをホストするための App Service プランを作成する必要があります。
[az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) を使用して App Service プランを作成します。
この例では、*P1V2* SKU で 1 つのワーカーのみを持つ *myAppServicePlan* という名前の App Service プランを *francecentral* の場所に作成します。 

```azurecli-interactive
az appservice plan create \
--name myAppServicePlan \
--resource-group myResourceGroup \
--location francecentral \
--sku P1V2 \
--number-of-workers 1
```

## <a name="create-a-web-app"></a>Web アプリの作成

これで App Service プランが作成されたので、Web アプリをデプロイできます。
[az appservice plan create](/cli/azure/webapp#az-webapp-create) を使用して Web アプリを作成します。
この例では、*myAppServicePlan* という名前のプランに *mySiteName* という名前の Web アプリを作成します

```azurecli-interactive
az webapp create \
--name mySiteName \
--resource-group myResourceGroup \
--plan myAppServicePlan
```

## <a name="create-a-vnet"></a>VNet を作成する

[az network vnet create](/cli/azure/network/vnet) を使用して仮想ネットワークを作成します。 この例では、*mySubnet* という名前のサブネットを使って、*myVNet* という名前の既定の仮想ネットワークを作成します。

```azurecli-interactive
az network vnet create \
--name myVNet \
--resource-group myResourceGroup \
--location francecentral \
--address-prefixes 10.8.0.0/16 \
--subnet-name mySubnet \
--subnet-prefixes 10.8.100.0/24
```

## <a name="configure-the-subnet"></a>サブネットを構成する 

サブネットを更新して、プライベート エンドポイント ネットワーク ポリシーを無効にする必要があります。 [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) を使用して  *mySubnet*  という名前のサブネット構成を更新します。

```azurecli-interactive
az network vnet subnet update \
--name mySubnet \
--resource-group myResourceGroup \
--vnet-name myVNet \
--disable-private-endpoint-network-policies true
```

## <a name="create-the-private-endpoint"></a>プライベート エンドポイントを作成する

[az network private-endpoint create](/cli/azure/network/private-endpoint) を使用して、Web アプリ用のプライベート エンドポイントを作成します。 この例では、リソース ID が /subscriptions/SubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/myWebApp、Web アプリの種類のグループ パラメーターが *sites* である Web アプリに対して、*myConnectionName* という名前の接続でサブネット *mySubnet* 内の VNet *myVNet* に *myPrivateEndpoint* という名前のプライベート エンドポイントを作成します。 

```azurecli-interactive
az network private-endpoint create \
--name myPrivateEndpoint \
--resource-group myResourceGroup \
--vnet-name myVNet \
--subnet mySubnet \
--connection-name myConnectionName \
--private-connection-resource-id /subscriptions/SubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/myWebApp \
--group-id sites
```

## <a name="configure-the-private-zone"></a>プライベート ゾーンを構成する

最後に、Web アプリの DNS 名を解決するために、VNet にリンクされた *privatelink.azurewebsites.net* という名前のプライベート DNS ゾーンを作成する必要があります。


```azurecli-interactive
az network private-dns zone create \
--name privatelink.azurewebsites.net \
--resource-group myResourceGroup

az network private-dns link vnet create \
--name myDNSLink \
--resource-group myResourceGroup \
--registration-enabled false \
--virtual-network myVNet \
--zone-name privatelink.azurewebsites.net

az network private-endpoint dns-zone-group create \
--name myZoneGroup \
--resource-group myResourceGroup \
--endpoint-name myPrivateEndpoint \
--private-dns-zone privatelink.azurewebsites.net \
--zone-name privatelink.azurewebsites.net
```






[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]


## <a name="next-steps"></a>次のステップ

- Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。
- その他の App Service の CLI サンプル スクリプトは、[Azure App Service のドキュメント](../samples-cli.md)のページにあります。
