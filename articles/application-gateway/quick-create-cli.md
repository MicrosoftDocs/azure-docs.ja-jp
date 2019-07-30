---
title: クイック スタート - Azure Application Gateway による Web トラフィックのルーティング - Azure CLI | Microsoft Docs
description: Azure CLI を使用して、Web トラフィックをバックエンド プール内の仮想マシンにルーティングする Azure Application Gateway を作成する方法を説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 1/8/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 56b655b07314d5ebc2d0cb47389988f1a89e6a56
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304345"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>クイック スタート:Azure Application Gateway による Web トラフィックの転送 - Azure CLI

このクイック スタートでは、Azure CLI を使用して、アプリケーション ゲートウェイを作成する方法を示します。  アプリケーション ゲートウェイを作成してから、それをテストして正しく動作していることを確認します。 Azure Application Gateway では、ポートにリスナーを割り当て、ルールを作成し、バックエンド プールにリソースを追加することによって、お客様のアプリケーション Web トラフィックを特定のリソースに転送します。 わかりやすくするために、この記事では、パブリック フロントエンド IP、このアプリケーション ゲートウェイで単一サイトをホストするための基本リスナー、バックエンド プールに使用される 2 つの仮想マシン、および基本要求ルーティング規則を使用する簡単な設定を使用します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>前提条件

### <a name="azure-cli"></a>Azure CLI

CLI をローカルにインストールして使用する場合は、Azure CLI バージョン 2.0.4 以降を実行してください。 バージョンを確認するには、**az --version** を実行します。 インストールまたはアップグレードについては、「[Azure CLI のインストール]( /cli/azure/install-azure-cli)」を参照してください。

### <a name="resource-group"></a>Resource group

Azure で、関連するリソースをリソース グループに割り当てます。 [az group create](/cli/azure/group#az-group-create) を使用してリソース グループを作成します。 

次の例では、*myResourceGroupAG* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

### <a name="required-network-resources"></a>必要なネットワーク リソース 

お客様が作成するリソースの間で Azure による通信が行われるには、仮想ネットワークが必要です。  アプリケーション ゲートウェイ サブネットには、アプリケーション ゲートウェイのみを含めることができます。 その他のリソースは許可されません。  Application Gateway 用に新しいサブネットを作成するか、既存のサブネットを使用することができます。 2 つのサブネットを作成します。1 つはアプリケーション ゲートウェイ用で、もう 1 つはバックエンド サーバー用です。 ユース ケースに従って、Application Gateway のフロントエンド IP を [パブリック] または [プライベート] に設定できます。 この例では、パブリック フロントエンド IP を選択します。

仮想ネットワークとサブネットを作成するには、[az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) を使用します。 パブリック IP アドレスを作成するには、[az network public-ip create](/cli/azure/network/public-ip) を実行します。

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

### <a name="backend-servers"></a>バックエンド サーバー

バックエンドは、NIC、仮想マシン スケール セット、パブリック IP、内部 IP、完全修飾ドメイン名 (FQDN)、および Azure App Service などのマルチテナント バックエンドで構成できます。 この例では、Azure によってアプリケーション ゲートウェイのバックエンド サーバーとして使用される 2 つの仮想マシンを作成します。 また、仮想マシンに IIS をインストールして、Azure によってアプリケーション ゲートウェイが正常に作成されたことを確認します。

#### <a name="create-two-virtual-machines"></a>2 つの仮想マシンの作成

アプリケーション ゲートウェイが正常に作成されたことを確認するには、仮想マシンに [NGINX Web サーバー](https://docs.nginx.com/nginx/)をインストールします。 cloud-init 構成ファイルを使って、NGINX をインストールし、Linux 仮想マシンで "Hello World" Node.js アプリを実行することができます。 cloud-init の詳細については、「[Azure での仮想マシンに対する cloud-init のサポート](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init)」を参照してください。

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

[az network nic create](/cli/azure/network/nic#az-network-nic-create) で、ネットワーク インターフェイスを作成します。 仮想マシンを作成するには、[az vm create](/cli/azure/vm#az-vm-create) を使用します。

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

[az network application-gateway create](/cli/azure/network/application-gateway) を使用して、アプリケーション ゲートウェイを作成します。 Azure CLI を使用してアプリケーション ゲートウェイを作成するときは、容量、SKU、HTTP 設定などの構成情報を指定します。 すると、Azure により、ネットワーク インターフェイスのプライベート IP アドレスが、アプリケーション ゲートウェイのバックエンド プールにサーバーとして追加されます。

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
- **appGatewayHttpListener**: **[リスナー] ページ**にあります。 これは、**appGatewayBackendPool** に関連付けられている既定のリスナーを指定します。
- **appGatewayFrontendIP**: **[フロントエンド IP 構成]** ページにあります。 これは、*myAGPublicIPAddress* を **appGatewayHttpListener** に割り当てます。
- **rule1**: **[ルール]** ページにあります。 **appGatewayHttpListener** に関連付けられている既定のルーティング規則を指定します。

## <a name="test-the-application-gateway"></a>アプリケーション ゲートウェイのテスト

Azure はアプリケーション ゲートウェイを作成するために NGINX Web サーバーを必要としませんが、このクイック スタートでは、Azure によってアプリケーション ゲートウェイが正常に作成されたかどうかを確認するために、NGINX をインストールしました。 新しいアプリケーション ゲートウェイのパブリック IP アドレスを取得するには、[az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) を使用します。 

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

そのパブリック IP アドレスをコピーし、ブラウザーのアドレス バーに貼り付けます。
    
![アプリケーション ゲートウェイのテスト](./media/quick-create-cli/application-gateway-nginxtest.png)

ブラウザーを更新すると、2 番目の VM の名前が表示されるはずです。 応答が有効であれば、アプリケーション ゲートウェイが正常に作成され、バックエンドと正常に接続できることが保証されます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

アプリケーション ゲートウェイと共に作成したリソースが不要になったら、[az group delete](/cli/azure/group#az-group-delete) コマンドを使用してリソース グループを削除します。 リソース グループを削除することで、アプリケーション ゲートウェイとそのすべての関連リソースも削除します。

```azurecli-interactive 
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure CLI を使用してアプリケーション ゲートウェイで Web トラフィックを管理する](./tutorial-manage-web-traffic-cli.md)

