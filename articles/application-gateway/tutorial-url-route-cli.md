---
title: チュートリアル - URL に基づいて Web トラフィックをルーティングする - Azure CLI
description: このチュートリアルでは、Azure CLI を使用して、Web トラフィックを URL に基づいてサーバーの特定のスケーラブル プールにルーティングする方法を説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 10/25/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 4f0c93c41a468b62baf1ec50d030f235d36a8dd2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58006475"
---
# <a name="tutorial-route-web-traffic-based-on-the-url-using-the-azure-cli"></a>チュートリアル:Azure CLI を使用して URL に基づいて Web トラフィックをルーティングする

皆さんは、Web トラフィックを管理する IT 管理者として、顧客またはユーザーができるだけ早く必要な情報を取得できるよう支援したいと考えています。 そのエクスペリエンスを最適化する方法の 1 つとして、Web トラフィックをその種類ごとに異なるサーバー リソースにルーティングすることが考えられます。 このチュートリアルでは、アプリケーションの各種トラフィックに対し、Azure CLI を使用して Application Gateway のルーティングをセットアップ、構成する方法について説明します。 このルーティングによって、トラフィックは、URL に基づいて異なるサーバー プールにリダイレクトされます。

![URL ルーティングの例](./media/tutorial-url-route-cli/scenario.png)

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 必要なネットワーク リソースのリソース グループを作成する
> * ネットワーク リソースを作成する
> * アプリケーションから着信するトラフィックのアプリケーション ゲートウェイを作成する
> * 各種トラフィックのサーバー プールとルーティング規則を指定する
> * プールを自動的にスケーリングできるよう各プールのスケール セットを作成する
> * 各種のトラフィックが適切なプールに誘導されることを確認するためのテストを実行する

好みに応じて、[Azure PowerShell](tutorial-url-route-powershell.md) または [Azure portal](create-url-route-portal.md) を使ってこのチュートリアルの手順を実行することもできます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.4 以降を実行する必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="create-a-resource-group"></a>リソース グループの作成

リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 リソース グループは、`az group create` を使用して作成します。

次の例では、*myResourceGroupAG* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>ネットワーク リソースを作成する

`az network vnet create` を使用して、*myVNet* という名前の仮想ネットワークと *myAGSubnet* という名前のサブネットを作成します。 次に、`az network vnet subnet create` を使用して、バックエンド サーバーに必要な *myBackendSubnet* という名前のサブネットを追加します。 `az network public-ip create` を使用して、*myAGPublicIPAddress* という名前のパブリック IP アドレスを作成します。

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24

az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24

az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-the-app-gateway-with-a-url-map"></a>URL マップを含んだアプリ ゲートウェイを作成する

`az network application-gateway create` を使用して、*myAppGateway* という名前のアプリケーション ゲートウェイを作成します。 Azure CLI を使用してアプリケーション ゲートウェイを作成するときは、容量、SKU、HTTP 設定などの構成情報を指定します。 このアプリケーション ゲートウェイを、先ほど作成した *myAGSubnet* と *myAGPublicIPAddress* に割り当てます。

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
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

 アプリケーション ゲートウェイの作成には数分かかる場合があります。 アプリケーション ゲートウェイを作成すると、新たに次の機能が確認できます。


|機能  |説明  |
|---------|---------|
|appGatewayBackendPool     |アプリケーション ゲートウェイには、少なくとも 1 つのバックエンド アドレス プールが必要です。|
|appGatewayBackendHttpSettings     |通信に使用するポート 80 と HTTP プロトコルを指定します。|
|appGatewayHttpListener     |appGatewayBackendPool に関連付けられている既定のリスナー。|
|appGatewayFrontendIP     |myAGPublicIPAddress を appGatewayHttpListener に割り当てます。|
|rule1     |appGatewayHttpListener に関連付けられている既定のルーティング規則。|

### <a name="add-image-and-video-backend-pools-and-a-port"></a>イメージおよびビデオのバックエンド プールとポートの追加

*imagesBackendPool* および *videoBackendPool* という名前のバックエンド プールをアプリケーション ゲートウェイに追加するには、`az network application-gateway address-pool create` を使用します。 プールのフロントエンド ポートは、`az network application-gateway frontend-port create` を使用して追加します。

```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name imagesBackendPool

az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name videoBackendPool

az network application-gateway frontend-port create \
  --port 8080 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name port8080
```

### <a name="add-a-backend-listener"></a>バックエンド リスナーの追加

`az network application-gateway http-listener create` を使用して、トラフィックのルーティングに必要な *backendListener* という名前のバックエンド リスナーを追加します。


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port port8080 \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-a-url-path-map"></a>URL パス マップの追加

URL パス マップにより、特定の URL が特定のバックエンド プールに確実にルーティングされます。 `az network application-gateway url-path-map create` および `az network application-gateway url-path-map rule create` を使用して、*imagePathRule* および *videoPathRule* という名前の URL パス マップを作成します。

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name myPathMap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --address-pool imagesBackendPool \
  --default-address-pool appGatewayBackendPool \
  --default-http-settings appGatewayBackendHttpSettings \
  --http-settings appGatewayBackendHttpSettings \
  --rule-name imagePathRule

az network application-gateway url-path-map rule create \
  --gateway-name myAppGateway \
  --name videoPathRule \
  --resource-group myResourceGroupAG \
  --path-map-name myPathMap \
  --paths /video/* \
  --address-pool videoBackendPool
```

### <a name="add-a-routing-rule"></a>ルーティングの規則を追加する

ルーティング規則は、URL マップを、作成したリスナーに関連付けます。 `az network application-gateway rule create` を使用して、*rule2* という名前の規則を追加します。

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name rule2 \
  --resource-group myResourceGroupAG \
  --http-listener backendListener \
  --rule-type PathBasedRouting \
  --url-path-map myPathMap \
  --address-pool appGatewayBackendPool
```

## <a name="create-vm-scale-sets"></a>VM スケール セットを作成する

このチュートリアルでは、作成した 3 つのバックエンド プールをサポートする 3 つの仮想マシン スケール セットを作成します。 *myvmss1*、*myvmss2*、および *myvmss3* という名前のスケール セットを作成します。 各スケール セットには、NGINX をインストールする 2 つの仮想マシン インスタンスが含まれています。

```azurecli-interactive
for i in `seq 1 3`; do

  if [ $i -eq 1 ]
  then
    poolName="appGatewayBackendPool" 
  fi

  if [ $i -eq 2 ]
  then
    poolName="imagesBackendPool"
  fi

  if [ $i -eq 3 ]
  then
    poolName="videoBackendPool"
  fi

  az vmss create \
    --name myvmss$i \
    --resource-group myResourceGroupAG \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password Azure123456! \
    --instance-count 2 \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --vm-sku Standard_DS2 \
    --upgrade-policy-mode Automatic \
    --app-gateway myAppGateway \
    --backend-pool-name $poolName
done
```

### <a name="install-nginx"></a>NGINX のインストール

```azurecli-interactive
for i in `seq 1 3`; do
  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"], "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="test-the-application-gateway"></a>アプリケーション ゲートウェイのテスト

アプリケーション ゲートウェイのパブリック IP アドレスを取得するには、az network public-ip show を使用します。 そのパブリック IP アドレスをコピーし、ブラウザーのアドレス バーに貼り付けます。 たとえば、`http://40.121.222.19`、`http://40.121.222.19:8080/images/test.htm`、`http://40.121.222.19:8080/video/test.htm` などです。

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![アプリケーション ゲートウェイでのベース URL のテスト](./media/tutorial-url-route-cli/application-gateway-nginx.png)

URL を http://&lt;ip-address&gt;:8080/images/test.html に変更します。&lt;ip-address&gt; は使用している IP アドレスに置き換えてください。次の例のように表示されます。

![アプリケーション ゲートウェイでのイメージ URL のテスト](./media/tutorial-url-route-cli/application-gateway-nginx-images.png)

URL を http://&lt;ip-address&gt;:8080/video/test.html に変更します。&lt;ip-address&gt; は使用している IP アドレスに置き換えてください。次の例のように表示されます。

![アプリケーション ゲートウェイでのビデオ URL のテスト](./media/tutorial-url-route-cli/application-gateway-nginx-video.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、リソース グループ、アプリケーション ゲートウェイ、およびすべての関連リソースを削除します。

```azurecli-interactive
az group delete --name myResourceGroupAG --location eastus
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [URL パスベースのリダイレクトのあるアプリケーション ゲートウェイを作成する](./tutorial-url-redirect-cli.md)
