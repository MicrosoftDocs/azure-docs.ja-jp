---
title: クイック スタート:CLI を使用して Web トラフィックを転送する
titleSuffix: Azure Application Gateway
description: このクイックスタートでは、Azure CLI を使用して、Web トラフィックをバックエンド プール内の仮想マシンに転送する Azure Application Gateway を作成する方法を説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 01/19/2021
ms.author: victorh
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 1a691d8ab212dd436b0dc9f7aafbc19a406b12b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98601775"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>クイック スタート:Azure Application Gateway による Web トラフィックの転送 - Azure CLI

このクイックスタートでは、Azure CLI を使用してアプリケーション ゲートウェイを作成します。 さらに、それをテストし、正しく動作することを確認します。 

アプリケーション ゲートウェイは、アプリケーション Web トラフィックをバックエンド プール内の特定のリソースに転送します。 リスナーをポートに割り当て、ルールを作成し、リソースをバックエンド プールに追加します。 わかりやすくするために、この記事では、パブリック フロントエンド IP アドレス、アプリケーション ゲートウェイで単一サイトをホストするための基本リスナー、基本要求ルーティング規則、およびバックエンド プール内の 2 つの仮想マシンを使用する簡単な設定を使用します。

また、[Azure PowerShell](quick-create-powershell.md) または [Azure portal](quick-create-portal.md) を使用してこのクイックスタートを完了することもできます。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0.4 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-resource-group"></a>リソース グループの作成

Azure で、関連するリソースをリソース グループに割り当てます。 `az group create` を使用して、リソース グループを作成します。 

次の例では、*myResourceGroupAG* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>ネットワーク リソースを作成する 

お客様が作成するリソースの間で Azure による通信が行われるには、仮想ネットワークが必要です。  アプリケーション ゲートウェイ サブネットには、アプリケーション ゲートウェイのみを含めることができます。 その他のリソースは許可されません。  Application Gateway 用に新しいサブネットを作成するか、既存のサブネットを使用することができます。 この例では 2 つのサブネットを作成します。1 つはアプリケーション ゲートウェイ用で、もう 1 つはバックエンド サーバー用です。 ユース ケースに従って、Application Gateway のフロントエンド IP を [パブリック] または [プライベート] に設定できます。 この例では、パブリック フロントエンド IP アドレスを選択します。

仮想ネットワークとサブネットを作成するには、`az network vnet create` を使用します。 `az network public-ip create` を実行して、パブリック IP アドレスを作成します。

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
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-the-backend-servers"></a>バックエンド サーバーを作成する

バックエンドには、NIC、仮想マシンスケールセット、パブリック IP アドレス、内部 IP アドレス、完全修飾ドメイン名 (FQDN)、および Azure App Service のようなマルチテナント バックエンドを割り当てることができます。 この例では、アプリケーション ゲートウェイのバックエンド サーバーとして使用する 2 つの仮想マシンを作成します。 また、アプリケーション ゲートウェイをテストするために、仮想マシン上に IIS もインストールします。

#### <a name="create-two-virtual-machines"></a>2 つの仮想マシンの作成

アプリケーション ゲートウェイが正常に作成されたことを確認するには、仮想マシンに NGINX Web サーバーをインストールします。 cloud-init 構成ファイルを使って、NGINX をインストールし、Linux 仮想マシンで "Hello World" Node.js アプリを実行することができます。 cloud-init の詳細については、「[Azure での仮想マシンに対する cloud-init のサポート](../virtual-machines/linux/using-cloud-init.md)」を参照してください。

Azure Cloud Shell で、次の構成をコピーして、*cloud-init.txt* という名前のファイルに貼り付けます。 「*editor cloud-init.txt*」と入力してファイルを作成します。

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

`az network nic create` を使用して、ネットワーク インターフェイスを作成します。 仮想マシンを作成するには、`az vm create` を使用します。

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupAG \
    --name myNic$i \
    --vnet-name myVNet \
    --subnet myBackendSubnet
  az vm create \
    --resource-group myResourceGroupAG \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
done
```

## <a name="create-the-application-gateway"></a>アプリケーション ゲートウェイの作成

`az network application-gateway create` を使用して、アプリケーション ゲートウェイを作成します。 Azure CLI を使用してアプリケーション ゲートウェイを作成するときは、容量、SKU、HTTP 設定などの構成情報を指定します。 すると、Azure により、ネットワーク インターフェイスのプライベート IP アドレスが、アプリケーション ゲートウェイのバックエンド プールにサーバーとして追加されます。

```azurecli-interactive
address1=$(az network nic show --name myNic1 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
address2=$(az network nic show --name myNic2 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-cookie-based-affinity Enabled \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$address1" "$address2"
```

Azure によってアプリケーション ゲートウェイが作成されるのに最大 30 分かかる場合があります。 作成後は、 **[アプリケーション ゲートウェイ]** ページの **[設定]** セクションで次の設定を確認できます。

- **appGatewayBackendPool**: **[バックエンド プール]** ページにあります。 これは、必要なバックエンド プールを指定します。
- **appGatewayBackendHttpSettings**: **[HTTP 設定]** ページにあります。 これは、アプリケーション ゲートウェイがポート 80 を使用すること、および通信に HTTP プロトコルを使用することを指定します。
- **appGatewayHttpListener**: **[リスナー] ページ** にあります。 これは、**appGatewayBackendPool** に関連付けられている既定のリスナーを指定します。
- **appGatewayFrontendIP**: **[フロントエンド IP 構成]** ページにあります。 これは、*myAGPublicIPAddress* を **appGatewayHttpListener** に割り当てます。
- **rule1**: **[ルール]** ページにあります。 **appGatewayHttpListener** に関連付けられている既定のルーティング規則を指定します。

## <a name="test-the-application-gateway"></a>アプリケーション ゲートウェイのテスト

Azure はアプリケーション ゲートウェイを作成するために NGINX Web サーバーを必要としませんが、このクイック スタートでは、Azure によってアプリケーション ゲートウェイが正常に作成されたかどうかを確認するために、NGINX をインストールしました。 新しいアプリケーション ゲートウェイのパブリック IP アドレスを取得するには、`az network public-ip show` を使用します。 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

そのパブリック IP アドレスをコピーし、ブラウザーのアドレス バーに貼り付けます。
    
![アプリケーション ゲートウェイのテスト](./media/quick-create-cli/application-gateway-nginxtest.png)

ブラウザーを更新すると、2 番目の VM の名前が表示されるはずです。 これは、アプリケーション ゲートウェイが正常に作成され、バックエンドに正常に接続できることを示しています。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

アプリケーション ゲートウェイと共に作成したリソースが不要になったら、`az group delete` コマンドを使用してリソース グループを削除します。 リソース グループを削除すると、アプリケーション ゲートウェイとそのすべての関連リソースも削除されます。

```azurecli-interactive 
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure CLI を使用してアプリケーション ゲートウェイで Web トラフィックを管理する](./tutorial-manage-web-traffic-cli.md)

