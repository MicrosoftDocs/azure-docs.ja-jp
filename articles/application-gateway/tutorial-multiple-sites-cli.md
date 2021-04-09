---
title: CLI を使用して複数の Web サイトをホストする
titleSuffix: Azure Application Gateway
description: Azure CLI を使用して複数の Web サイトをホストするアプリケーション ゲートウェイを作成する方法について説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 350962aed89d04c5508e7b2c50e8a838cd5a7174
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94566148"
---
# <a name="create-an-application-gateway-that-hosts-multiple-web-sites-using-the-azure-cli"></a>Azure CLI を使用して複数の Web サイトをホストするアプリケーション ゲートウェイを作成する

[アプリケーション ゲートウェイ](overview.md)を作成するときに、Azure CLI を使用して[複数の Web サイトのホスティングを構成](multiple-site-overview.md)できます。 この記事では、仮想マシン スケール セットを使用してバックエンド アドレス プールを定義します。 その後、Web トラフィックがプール内の適切なサーバーに確実に到着するように、所有するドメインに基づいてリスナーと規則を構成します。 この記事では、複数のドメインを所有していることを前提として、*www\.contoso.com* と *www\.fabrikam.com* の例を使用します。

この記事では、次のことについて説明します。

* ネットワークのセットアップ
* アプリケーション ゲートウェイの作成
* バックエンド リスナーの作成
* ルーティング規則の作成
* バックエンド プールを含んだ仮想マシン スケール セットの作成
* ドメインの CNAME レコードの作成

:::image type="content" source="./media/tutorial-multiple-sites-cli/scenario.png" alt-text="複数サイト アプリケーション ゲートウェイ":::

好みに応じて、[Azure PowerShell](tutorial-multiple-sites-powershell.md) を使ってこの手順を実行することもできます。

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

[az network vnet create](/cli/azure/network/vnet) を使用して、仮想ネットワークと *myAGSubnet* という名前のサブネットを作成します。 次に、[az network vnet subnet create](/cli/azure/network/vnet/subnet) を使用して、バックエンド サーバーに必要なサブネットを追加できます。 [az network public-ip create](/cli/azure/network/public-ip) を使用して *myAGPublicIPAddress* という名前のパブリック IP アドレスを作成します。

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

## <a name="create-the-application-gateway"></a>アプリケーション ゲートウェイの作成

[az network application-gateway create](/cli/azure/network/application-gateway#az-network-application-gateway-create) を使用して、アプリケーション ゲートウェイを作成することができます。 Azure CLI を使用してアプリケーション ゲートウェイを作成するときは、容量、SKU、HTTP 設定などの構成情報を指定します。 このアプリケーション ゲートウェイを、先ほど作成した *myAGSubnet* と *myAGPublicIPAddress* に割り当てます。 

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

### <a name="add-the-backend-pools"></a>バックエンド プールの追加

[az network application-gateway address-pool create](/cli/azure/network/application-gateway/address-pool#az-network-application-gateway-address-pool-create) を使用して、バックエンド サーバーを含めるために必要なバックエンド プールを追加します
```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name contosoPool

az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name fabrikamPool
```

### <a name="add-listeners"></a>リスナーの追加

[az network application-gateway http-listener create](/cli/azure/network/application-gateway/http-listener#az-network-application-gateway-http-listener-create) を使用して、トラフィックのルーティングに必要なリスナーを追加します。

>[!NOTE]
> Application Gateway または WAF v2 SKU では、リスナーごとに最大 5 つのホスト名を構成することもできます。ホスト名にワイルドカード文字を使用できます。 詳細については、「[リスナーにおけるワイルドカードのホスト名](multiple-site-overview.md#wildcard-host-names-in-listener-preview)」を参照してください。
>Azure CLI を使用してリスナーで複数のホスト名とワイルドカード文字を使用するには、`--host-name` ではなく `--host-names` を使用する必要があります。 host-names を使用すると、最大 5 つのホスト名をスペースで区切られた値として指定できます。 たとえば、`--host-names "*.contoso.com *.fabrikam.com"` のように指定します。

```azurecli-interactive
az network application-gateway http-listener create \
  --name contosoListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.com

az network application-gateway http-listener create \
  --name fabrikamListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.fabrikam.com   
  ```

### <a name="add-routing-rules"></a>ルーティング規則の追加

規則は、列記されている順序で処理されます。 トラフィックは、特異性に関係なく、最初に一致した規則を使って転送されます。 たとえば、同一のポート上に基本リスナーを使用するルールとマルチサイト リスナーを使用するルールがある場合、マルチサイトのルールを適切に動作させるには、リストでマルチサイト リスナーのルールを基本リスナーのルールよりも先に配置する必要があります。 

この例では、2 つの新しい規則を作成し、アプリケーション ゲートウェイをデプロイしたときに作成された既定の規則を削除します。 [az network application-gateway rule create](/cli/azure/network/application-gateway/rule#az-network-application-gateway-rule-create) を使用して、規則を追加することができます。

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoListener \
  --rule-type Basic \
  --address-pool contosoPool

az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name fabrikamRule \
  --resource-group myResourceGroupAG \
  --http-listener fabrikamListener \
  --rule-type Basic \
  --address-pool fabrikamPool

az network application-gateway rule delete \
  --gateway-name myAppGateway \
  --name rule1 \
  --resource-group myResourceGroupAG
```

## <a name="create-virtual-machine-scale-sets"></a>仮想マシン スケール セットの作成

この例では、アプリケーション ゲートウェイで 3 つのバックエンド プールをサポートする 3 つの仮想マシン スケール セットを作成します。 作成するスケール セットの名前は、*myvmss1*、*myvmss2*、および *myvmss3* です。 各スケール セットには、IIS をインストールする 2 つの仮想マシン インスタンスが含まれています。

```azurecli-interactive
for i in `seq 1 2`; do

  if [ $i -eq 1 ]
  then
    poolName="contosoPool"
  fi

  if [ $i -eq 2 ]
  then
    poolName="fabrikamPool"
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
for i in `seq 1 2`; do

  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'

done
```

## <a name="create-a-cname-record-in-your-domain"></a>ドメインの CNAME レコードの作成

パブリック IP アドレスを使用してアプリケーション ゲートウェイを作成した後は、DNS アドレスを取得し、これを使用してドメインに CNAME レコードを作成できます。 [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) を使用して、アプリケーション ゲートウェイの DNS アドレスを取得できます。 DNSSettings の *fqdn* 値をコピーし、作成した CNAME レコードの値として使用します。 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

アプリケーション ゲートウェイを再起動すると VIP が変更される可能性があるため、A レコードの使用はお勧めしません。

## <a name="test-the-application-gateway"></a>アプリケーション ゲートウェイのテスト

ブラウザーのアドレス バーにドメイン名を入力します。 例: http:\//www.contoso.com

![アプリケーション ゲートウェイの contoso サイトをテストする](./media/tutorial-multiple-sites-cli/application-gateway-nginxtest1.png)

アドレスをもう 1 つのドメインに変更します。次の例のように表示されます。

![アプリケーション ゲートウェイの fabrikam サイトをテストする](./media/tutorial-multiple-sites-cli/application-gateway-nginxtest2.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、リソース グループ、アプリケーション ゲートウェイ、およびすべての関連リソースを削除します。

```azurecli-interactive
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>次のステップ

[URL パスベースのルーティング規則のあるアプリケーション ゲートウェイを作成する](./tutorial-url-route-cli.md)
