---
title: チュートリアル:CLI を使用した URL パスベースのリダイレクト
titleSuffix: Azure Application Gateway
description: このチュートリアルでは、Azure CLI を使用して、URL パスベースでトラフィックがリダイレクトされるアプリケーション ゲートウェイを作成する方法について説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/05/2021
ms.author: victorh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: aa247d868e19831b3e6f1c3f68e605fb209a0664
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102218317"
---
# <a name="tutorial-create-an-application-gateway-with-url-path-based-redirection-using-the-azure-cli"></a>チュートリアル:Azure CLI を使用して URL パスベースのリダイレクトのあるアプリケーション ゲートウェイを作成する

[アプリケーション ゲートウェイ](./overview.md)を作成するときに、Azure CLI を使用して [URL パス ベースのルーティング規則](tutorial-url-route-cli.md)を構成できます。 このチュートリアルでは、[仮想マシン スケール セット](../virtual-machine-scale-sets/overview.md)を使用してバックエンド プールを作成します。 次に、Web トラフィックが適切なバックエンド プールにリダイレクトされるようにする URL ルーティング規則を作成します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * ネットワークのセットアップ
> * アプリケーション ゲートウェイの作成
> * リスナーとルーティング規則の追加
> * バックエンド プールの仮想マシン スケール セットの作成

次の例は、ポート 8080 と 8081 の両方から送信されてきており、同じバックエンド プールに転送されているサイト トラフィックを示します。

![URL ルーティングの例](./media/tutorial-url-redirect-cli/scenario.png)

必要に応じて、[Azure PowerShell](tutorial-url-redirect-powershell.md) を使ってこのチュートリアルの手順を完了できます。

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

[az network vnet create](/cli/azure/network/vnet) を使用して、*myVNet* という名前の仮想ネットワークと *myAGSubnet* という名前のサブネットを作成します。 次に、[az network vnet subnet create](/cli/azure/network/vnet/subnet) を使用して、バックエンド サーバーに必要な *myBackendSubnet* という名前のサブネットを追加できます。 [az network public-ip create](/cli/azure/network/public-ip) を使用して *myAGPublicIPAddress* という名前のパブリック IP アドレスを作成します。

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
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-an-application-gateway"></a>アプリケーション ゲートウェイを作成する

[az network application-gateway create](/cli/azure/network/application-gateway) を使用して、myAppGateway という名前のアプリケーション ゲートウェイを作成します。 Azure CLI を使用してアプリケーション ゲートウェイを作成するときは、容量、SKU、HTTP 設定などの構成情報を指定します。 このアプリケーション ゲートウェイを、先ほど作成した *myAGSubnet* と *myPublicIPAddress* に割り当てます。

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
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


### <a name="add-backend-pools-and-ports"></a>バックエンド プールとポートの追加

*imagesBackendPool* および *videoBackendPool* という名前のバックエンド アドレス プールをアプリケーション ゲートウェイに追加するには、[az network application-gateway address-pool create](/cli/azure/network/application-gateway/address-pool) を使用します。 プールのフロントエンド ポートは、[az network application-gateway frontend-port create](/cli/azure/network/application-gateway/frontend-port) を使用して追加します。 

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
  --name bport

az network application-gateway frontend-port create \
  --port 8081 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name rport
```

## <a name="add-listeners-and-rules"></a>リスナーと規則の追加

### <a name="add-listeners"></a>リスナーの追加

[az network application-gateway http-listener create](/cli/azure/network/application-gateway/http-listener) を使用して、トラフィックのルーティングに必要な *backendListener* と *redirectedListener* という名前のバックエンド リスナーを追加します。


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port bport \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway

az network application-gateway http-listener create \
  --name redirectedListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port rport \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-the-default-url-path-map"></a>既定の URL パス マップを追加する

URL パス マップにより、特定の URL が特定のバックエンド プールに確実にルーティングされます。 [az network application-gateway url-path-map create](/cli/azure/network/application-gateway/url-path-map) と [az network application-gateway url-path-map rule create](/cli/azure/network/application-gateway/url-path-map/rule) を使用して、*imagePathRule* および *videoPathRule* という名前の URI パス マップを作成します。

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name urlpathmap \
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
  --path-map-name urlpathmap \
  --paths /video/* \
  --address-pool videoBackendPool
```

### <a name="add-redirection-configuration"></a>リダイレクト構成を追加する

[az network application-gateway redirect-config create](/cli/azure/network/application-gateway/redirect-config) を使用して、リスナーのリダイレクトを構成できます。

```azurecli-interactive
az network application-gateway redirect-config create \
  --gateway-name myAppGateway \
  --name redirectConfig \
  --resource-group myResourceGroupAG \
  --type Found \
  --include-path true \
  --include-query-string true \
  --target-listener backendListener
```

### <a name="add-the-redirection-url-path-map"></a>リダイレクト URL パス マップを追加する

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name redirectpathmap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --redirect-config redirectConfig \
  --rule-name redirectPathRule
```

### <a name="add-routing-rules"></a>ルーティング規則の追加

ルーティング規則によって、URL パス マップが作成したリスナーに関連付けられます。 [az network application-gateway rule create](/cli/azure/network/application-gateway/rule) を使用して、*defaultRule* と *redirectedRule* という名前の規則を追加することができます。

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name defaultRule \
  --resource-group myResourceGroupAG \
  --http-listener backendListener \
  --rule-type PathBasedRouting \
  --url-path-map urlpathmap \
  --address-pool appGatewayBackendPool

az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name redirectedRule \
  --resource-group myResourceGroupAG \
  --http-listener redirectedListener \
  --rule-type PathBasedRouting \
  --url-path-map redirectpathmap \
  --address-pool appGatewayBackendPool
```

## <a name="create-virtual-machine-scale-sets"></a>仮想マシン スケール セットの作成

この例では、作成した 3 つのバックエンド プールをサポートする 3 つの仮想マシン スケール セットを作成します。 作成するスケール セットの名前は、*myvmss1*、*myvmss2*、および *myvmss3* です。 各スケール セットには、NGINX をインストールする 2 つの仮想マシン インスタンスが含まれています。

\<azure-user> と \<password> は、選択したユーザー名とパスワードに置き換えます。

```azurecli
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
    --admin-username <azure-user> \
    --admin-password <password> \
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

アプリケーション ゲートウェイのパブリック IP アドレスを取得するには、[az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) を使用します。 そのパブリック IP アドレスをコピーし、ブラウザーのアドレス バーに貼り付けます。 たとえば、`http://40.121.222.19`、`http://40.121.222.19:8080/images/test.htm`、`http://40.121.222.19:8080/video/test.htm`、`http://40.121.222.19:8081/images/test.htm` などです。

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![アプリケーション ゲートウェイでのベース URL のテスト](./media/tutorial-url-redirect-cli/application-gateway-nginx.png)

URL を http://&lt;IP アドレス&gt;:8080/images/test.html に変更し、&lt;IP アドレス&gt; を使用している IP アドレスに置き換えると、次の例のように表示されるはずです。

![アプリケーション ゲートウェイでのイメージ URL のテスト](./media/tutorial-url-redirect-cli/application-gateway-nginx-images.png)

URL を http://&lt;ip-address&gt;:8080/video/test.html に変更します。&lt;ip-address&gt; は使用している IP アドレスに置き換えてください。次の例のように表示されます。

![アプリケーション ゲートウェイでのビデオ URL のテスト](./media/tutorial-url-redirect-cli/application-gateway-nginx-video.png)

URL を http://&lt;ip-address&gt;:8081/images/test.htm に変更します。&lt;ip-address&gt; は使用している IP アドレスに置き換えてください。トラフィックが http://&lt;ip-address&gt;:8080/images の images バックエンド プールにリダイレクトされるのがわかります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、リソース グループ、アプリケーション ゲートウェイ、およびすべての関連リソースを削除します。

```azurecli-interactive
az group delete --name myResourceGroupAG
```
## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [アプリケーション ゲートウェイでできることについてさらに学習する](./overview.md)