---
title: 内部 Basic Load Balancer を作成する - Azure CLI
titlesuffix: Azure Load Balancer
description: Azure CLI を使用して内部ロード バランサーを作成する方法について説明します
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/27/2018
ms.author: allensu
ms.openlocfilehash: e38cc4e6da574e0c3be490cf5d9cd929624343e5
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275449"
---
# <a name="create-an-internal-load-balancer-to-load-balance-vms-using-azure-cli"></a>Azure CLI を使用して VM の負荷を分散する内部ロード バランサーを作成する

この記事では、VM の負荷を分散する内部ロード バランサーを作成する方法について説明します。 ロード バランサーをテストするには、Web アプリの負荷を分散するために、Ubuntu サーバーを実行する 2 つの仮想マシン (VM) をデプロイします。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.28 以降のバージョンを実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](https://docs.microsoft.com/cli/azure/group) を使用して、リソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

次の例では、*myResourceGroupILB* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive
  az group create \
    --name myResourceGroupILB \
    --location eastus
```
## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

[az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) を使用して、*myVnet* という名前の仮想ネットワークを作成します。*myResourceGroup* に、*mySubnet* という名前のサブネットを含めます。

```azurecli-interactive
  az network vnet create \
    --name myVnet \
    --resource-group myResourceGroupILB \
    --location eastus \
    --subnet-name mySubnet
```
## <a name="create-basic-load-balancer"></a>Basic Load Balancer を作成する

このセクションでは、ロード バランサーの以下のコンポーネントを作成および構成する方法について説明します。
  - ロード バランサーの着信ネットワーク トラフィックを受け取るフロントエンド IP 構成。
  - フロントエンド プールから負荷分散されたネットワーク トラフィックが送信されるバックエンド IP プール。
  - バックエンド VM インスタンスの正常性を判断する正常性プローブ。
  - VM に対するトラフィックの分散方法を定義するロード バランサー規則。

### <a name="create-the-load-balancer"></a>ロード バランサーを作成する

[az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) を使用して、**myLoadBalancer** という名前の内部ロード バランサーを作成します。これには、**myFrontEnd** という名前のフロントエンド IP 構成と、プライベート IP アドレス **10.0.0.7 に関連付けられている **myBackEndPool** という名前のバックエンド プールを含めます。

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupILB \
    --name myLoadBalancer \
    --frontend-ip-name myFrontEnd \
    --private-ip-address 10.0.0.7 \
    --backend-pool-name myBackEndPool \
    --vnet-name myVnet \
    --subnet mySubnet      
  ```
### <a name="create-the-health-probe"></a>正常性プローブを作成する

正常性プローブは、ネットワーク トラフィックを受信できるように、すべての仮想マシン インスタンスを確認します。 プローブのチェックで失敗した仮想マシン インスタンスは、オンラインに戻り、プローブ チェックにより正常と判定されるまで、ロード バランサーから削除されます。 [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) を使用して正常性プローブを作成し、仮想マシンの正常性を監視します。 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupILB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>ロード バランサー規則を作成する

ロード バランサー規則は、着信トラフィック用のフロントエンド IP 構成と、トラフィックを受信するためのバックエンド IP プールを、必要な発信元ポートと宛先ポートと共に定義します。 [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) を使用してロード バランサー規則 *myHTTPRule* を作成します。この規則では、フロントエンド プール *myFrontEnd* のポート 80 をリッスンし、同じポート 80 を使用して、負荷分散されたネットワーク トラフィックをバックエンド アドレス プール *myBackEndPool* に送信します。 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupILB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```

## <a name="create-servers-for-the-backend-address-pool"></a>バックエンド アドレス プール用のサーバーを作成する

いくつかの VM をデプロイしてロード バランサーをテストする前に、サポート用の仮想ネットワーク リソースを作成します。

### <a name="create-nics"></a>NIC の作成

[az network nic create](/cli/azure/network/nic#az-network-nic-create) を使用して 2 つのネットワーク インターフェイスを作成し、それらをプライベート IP アドレスに関連付けます。 

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupILB \
    --name myNic$i \
    --vnet-name myVnet \
    --subnet mySubnet \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
done
```

## <a name="create-backend-servers"></a>バックエンド サーバーの作成

この例では、ロード バランサーのバックエンド サーバーとして使用する 2 つの仮想マシンを作成します。 ロード バランサーが正常に作成されたことを確認するには、仮想マシンに NGINX もインストールします。

### <a name="create-an-availability-set"></a>可用性セットを作成する

[az vm availabilityset create](/cli/azure/network/nic) を使用して、可用性セットを作成します。

 ```azurecli-interactive
  az vm availability-set create \
    --resource-group myResourceGroupILB \
    --name myAvailabilitySet
```

### <a name="create-two-virtual-machines"></a>2 つの仮想マシンの作成

cloud-init 構成ファイルを使って、NGINX をインストールし、Linux 仮想マシンで "Hello World" Node.js アプリを実行することができます。 現在のシェルで、cloud-init.txt という名前のファイルを作成し、次の構成をコピーして、そのシェルに貼り付けます。 cloud-init ファイル全体 (特に最初の行) を、確実かつ正確にコピーしてください。

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
 
[az vm create](/cli/azure/vm#az-vm-create) で、仮想マシンを作成します。

 ```azurecli-interactive
for i in `seq 1 2`; do
  az vm create \
    --resource-group myResourceGroupILB \
    --name myVM$i \
    --availability-set myAvailabilitySet \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
    done
```
VM がデプロイされるまでに、数分かかる場合があります。

### <a name="create-a-vm-for-testing-the-load-balancer"></a>ロード バランサーをテストするために VM を作成する

ロード バランサーをテストするには、仮想マシン *myVMTest* を作成し、それを *myNic3* に関連付けます。

```azurecli-interactive
 az vm create \
    --resource-group myResourceGroupILB \
    --name myVMTest \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword123456!
```

## <a name="test-the-internal-load-balancer"></a>内部ロード バランサーをテストする

ロード バランサーをテストするには、まず、ロード バランサーのプライベート IP アドレスを取得する必要があります。 次に、仮想マシン myVMTest にサインインし、Web ブラウザーのアドレス バーにプライベート IP アドレスを入力します。

ロード バランサーのプライベート IP アドレスを取得するには、[az network lb show](/cli/azure/network/public-ip) を使用します。 プライベート IP アドレスをコピーし、仮想マシン *myVMTest* の Web ブラウザーのアドレス バーに貼り付けます。

```azurecli-interactive
  az network lb show \
    --name myLoadBalancer \
    --resource-group myResourceGroupILB
``` 
![ロード バランサーをテストする](./media/load-balancer-get-started-ilb-arm-cli/load-balancer-test.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、[az group delete](/cli/azure/group#az-group-delete) コマンドを使用して、リソース グループ、ロード バランサー、およびすべての関連リソースを削除できます。

```azurecli-interactive 
  az group delete --name myResourceGroupILB
```


## <a name="next-steps"></a>次の手順
この記事では、内部 Basic Load Balancer を作成し、それに VM をアタッチして、ロード バランサー トラフィック規則と正常性プローブを構成してから、ロード バランサーをテストしました。 ロード バランサーとその関連リソースの詳細を確認するには、ハウツー記事に進みます。
