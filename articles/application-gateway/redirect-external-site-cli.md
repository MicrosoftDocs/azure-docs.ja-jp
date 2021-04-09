---
title: CLI を使用した外部トラフィックのリダイレクト ‐ Azure Application Gateway
description: Azure CLI を使用して外部 Web トラフィックを適切なプールにリダイレクトするアプリケーション ゲートウェイを作成する方法について説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 09/24/2020
ms.author: victorh
ms.openlocfilehash: 838c2dc887790bb12b390261d94748595232d8b3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94565859"
---
# <a name="create-an-application-gateway-with-external-redirection-using-the-azure-cli"></a>Azure CLI を使用して外部リダイレクトと共にアプリケーション ゲートウェイを作成する

[アプリケーション ゲートウェイ](overview.md)を作成するときに、Azure CLI を使用して [Web トラフィック リダイレクト](multiple-site-overview.md)を構成できます。 このチュートリアルでは、アプリケーション ゲートウェイに到達した Web トラフィックを外部サイトにリダイレクトするリスナーと規則を構成します。

この記事では、次のことについて説明します。

* ネットワークのセットアップ
* リスナーとリダイレクト規則の作成
* アプリケーション ゲートウェイの作成

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - このチュートリアルには、Azure CLI のバージョン 2.0.4 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-resource-group"></a>リソース グループを作成する

リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 [az group create](/cli/azure/group) を使用してリソース グループを作成します。

次の例では、*myResourceGroupAG* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>ネットワーク リソースを作成する 

[az network vnet create](/cli/azure/network/vnet) を使用して、*myVNet* という名前の仮想ネットワークと *myAGSubnet* という名前のサブネットを作成します。 [az network public-ip create](/cli/azure/network/public-ip) を使用して *myAGPublicIPAddress* という名前のパブリック IP アドレスを作成します。 こうしたリソースは、アプリケーション ゲートウェイとその関連リソースにネットワーク接続を提供するために使用されます。

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-an-application-gateway"></a>アプリケーション ゲートウェイの作成

[az network application-gateway create](/cli/azure/network/application-gateway) を使用して、*myAppGateway* という名前のアプリケーション ゲートウェイを作成することができます。 Azure CLI を使用してアプリケーション ゲートウェイを作成するときは、容量、SKU、HTTP 設定などの構成情報を指定します。 このアプリケーション ゲートウェイを、先ほど作成した *myAGSubnet* と *myPublicIPAddress* に割り当てます。 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 8080 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

アプリケーション ゲートウェイの作成には数分かかる場合があります。 アプリケーション ゲートウェイを作成すると、新たに次の機能が確認できます。

- *appGatewayBackendPool* - アプリケーション ゲートウェイには、少なくとも 1 つのバックエンド アドレス プールが必要です。
- *appGatewayBackendHttpSettings* - 通信に使用するポート 80 と HTTP プロトコルを指定します。
- *appGatewayHttpListener* - *appGatewayBackendPool* に関連付けられている既定のリスナー。
- *appGatewayFrontendIP* -*myAGPublicIPAddress* を *appGatewayHttpListener* に割り当てます。
- *rule1* - *appGatewayHttpListener* に関連付けられている既定のルーティング規則。

### <a name="add-the-redirection-configuration"></a>リダイレクト構成の追加

[az network application-gateway redirect-config create](/cli/azure/network/application-gateway/redirect-config) を使用して、アプリケーション ゲートウェイで *www\.consoto.org* から *www\.contoso.com* のリスナーにトラフィックを送信するリダイレクト構成を追加します。

```azurecli-interactive
az network application-gateway redirect-config create \
  --name myredirect \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Temporary \
  --target-url "https://bing.com"
```

### <a name="add-a-listener-and-routing-rule"></a>リスナーとルーティング規則の追加

リスナーは、アプリケーションがトラフィックを適切にルーティングするために必要です。 [az network application-gateway frontend-port create](/cli/azure/network/application-gateway) によって作成されたフロントエンド ポートと [az network application-gateway http-listener create](/cli/azure/network/application-gateway) を使用して、リスナーを作成します。 規則は、リスナーが受信トラフィックの送信先を判断するために必要です。 [az network application-gateway rule create](/cli/azure/network/application-gateway) を使用して、*redirectRule* という名前の基本の規則を作成します。

```azurecli-interactive
az network application-gateway frontend-port create \
  --port 80 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name redirectPort
az network application-gateway http-listener create \
  --name redirectListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port redirectPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name redirectRule \
  --resource-group myResourceGroupAG \
  --http-listener redirectListener \
  --rule-type Basic \
  --redirect-config myredirect
```

## <a name="test-the-application-gateway"></a>アプリケーション ゲートウェイのテスト

アプリケーション ゲートウェイのパブリック IP アドレスを取得するには、[az network public-ip show](/cli/azure/network/public-ip) を使用できます。 そのパブリック IP アドレスをコピーし、ブラウザーのアドレス バーに貼り付けます。

*bing.com* がブラウザーに表示されます。

## <a name="next-steps"></a>次のステップ

- [Azure CLI を使用して内部リダイレクトと共にアプリケーション ゲートウェイを作成する](redirect-internal-site-cli.md)
