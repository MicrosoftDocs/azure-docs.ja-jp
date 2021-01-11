---
title: クイック スタート:パブリック ロード バランサーを作成する - Azure CLI
titleSuffix: Azure Load Balancer
description: このクイックスタートでは、Azure CLI を使用してパブリック ロード バランサーを作成する方法について説明します
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
Customer intent: I want to create a load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/23/2020
ms.author: allensu
ms.custom: mvc, devx-track-javascript, devx-track-azurecli
ms.openlocfilehash: b437bfa205833594c9e76c6f0d8ff1923f51f117
ms.sourcegitcommit: e2b36c60a53904ecf3b99b3f1d36be00fbde24fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2020
ms.locfileid: "88762710"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-cli"></a>クイック スタート:Azure CLI を使用して VM の負荷を分散するパブリック ロード バランサーを作成する

Azure CLI を使用してパブリック ロード バランサーと 3 つの仮想マシンを作成することにより、Azure Load Balancer の使用を開始します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- ローカルにインストールされた Azure CLI または Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

CLI をローカルにインストールして使用する場合、このクイックスタートでは Azure CLI バージョン 2.0.28 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) を使用して、次のようにリソース グループを作成します。

* 名前は **myResourceGroupLB** にします。 
* 場所は **eastus** にします。

```azurecli-interactive
  az group create \
    --name myResourceGroupLB \
    --location eastus
```
---

# <a name="standard-sku"></a>[**Standard SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>運用環境のワークロードには、Standard SKU ロード バランサーをお勧めします。 SKU の詳細については、「 **[Azure Load Balancer の SKU](skus.md)** 」を参照してください。

## <a name="configure-virtual-network"></a>仮想ネットワークを構成する

VM をデプロイしてロード バランサーをテストする前に、サポート用の仮想ネットワーク リソースを作成します。

### <a name="create-a-virtual-network"></a>仮想ネットワークの作成

[az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt) を使用して、次のように仮想ネットワークを作成します。

* 名前は **myVNet** にします。
* アドレス プレフィックスは **10.1.0.0/16** にします。
* サブネットの名前は **myBackendSubnet** にします。
* サブネット プレフィックスは **10.1.0.0/24** にします。
* リソース グループは **myResourceGroupLB** にします。
* 場所は **eastus** にします。

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupLB \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

Standard ロード バランサーの場合、バックエンドが扱う VM には、ネットワーク セキュリティ グループに属しているネットワーク インターフェイスが必要です。 

[az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) を使用して、次のようにネットワーク セキュリティ グループを作成します。

* 名前は **myNSG** にします。
* リソース グループは **myResourceGroupLB** にします。

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupLB \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>ネットワーク セキュリティ グループ規則を作成する

[az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) を使用して、次のようにネットワーク セキュリティ グループの規則を作成します。

* 名前は **myNSGRuleHTTP** にします。
* ネットワーク セキュリティ グループは、前の手順で作成した **myNSG** にします。
* リソース グループは **myResourceGroupLB** にします。
* プロトコルは **(*)** にします。
* 方向は **Inbound** にします。
* 送信元は **(*)** にします。
* 送信先は **(*)** にします。
* 送信先ポートは**ポート 80** にします。
* アクセスは **Allow** にします。
* 優先度は **200** にします。

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupLB \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

### <a name="create-network-interfaces-for-the-virtual-machines"></a>仮想マシンのネットワーク インターフェイスを作成する

[az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) を使用して、次のように 3 つのネットワーク インターフェイスを作成します。

#### <a name="vm1"></a>VM1

* 名前は **myNicVM1** にします。
* リソース グループは **myResourceGroupLB** にします。
* 仮想ネットワークは **myVNet** にします。
* サブネットは **myBackendSubnet** にします。
* ネットワーク セキュリティ グループは **myNSG** にします。

```azurecli-interactive

  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>VM2

* 名前は **myNicVM2** にします。
* リソース グループは **myResourceGroupLB** にします。
* 仮想ネットワークは **myVNet** にします。
* サブネットは **myBackendSubnet** にします。

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm3"></a>VM3

* 名前は **myNicVM3** にします。
* リソース グループは **myResourceGroupLB** にします。
* 仮想ネットワークは **myVNet** にします。
* サブネットは **myBackendSubnet** にします。
* ネットワーク セキュリティ グループは **myNSG** にします。

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM3 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

## <a name="create-backend-servers"></a>バックエンド サーバーの作成

このセクションでは、以下を作成します。

* サーバー構成のための **cloud-init.txt** という名前のクラウド構成ファイル。
* ロード バランサーのバックエンド サーバーとして使用する 3 つの仮想マシン。

### <a name="create-cloud-init-configuration-file"></a>cloud-init 構成ファイルを作成する

cloud-init 構成ファイルを使用して、NGINX をインストールし、Linux 仮想マシンで "Hello World" Node.js アプリを実行します。 

現在のシェルで、cloud-init.txt という名前のファイルを作成します。 以下の構成をコピーしてシェルに貼り付けます。 cloud-init ファイル全体を、特に最初の行に注意して正確にコピーしてください。

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
### <a name="create-virtual-machines"></a>仮想マシンを作成する

[az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) を使用して、次のように仮想マシンを作成します。

#### <a name="vm1"></a>VM1
* 名前は **myVM1** にします。
* リソース グループは **myResourceGroupLB** にします。
* ネットワーク インターフェイス **myNicVM1** に接続します。
* 仮想マシン イメージは **UbuntuLTS** にします。
* 構成ファイルは、前の手順で作成した **cloud-init.txt** にします。
* ゾーンは **Zone 1** にします。

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 1 \
    --no-wait
    
```
#### <a name="vm2"></a>VM2
* 名前は **myVM2** にします。
* リソース グループは **myResourceGroupLB** にします。
* ネットワーク インターフェイス **myNicVM2** に接続します。
* 仮想マシン イメージは **UbuntuLTS** にします。
* 構成ファイルは、前の手順で作成した **cloud-init.txt** にします。
* ゾーンは **Zone 2** にします。

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 2 \
    --no-wait
```

#### <a name="vm3"></a>VM3
* 名前は **myVM3** にします。
* リソース グループは **myResourceGroupLB** にします。
* ネットワーク インターフェイス **myNicVM3** に接続します。
* 仮想マシン イメージは **UbuntuLTS** にします。
* 構成ファイルは、前の手順で作成した **cloud-init.txt** にします。
* ゾーンは **Zone 3** にします。

```azurecli-interactive
   az vm create \
    --resource-group myResourceGroupLB \
    --name myVM3 \
    --nics myNicVM3 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 3 \
    --no-wait
```
VM がデプロイされるまでに、数分かかる場合があります。

## <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成

インターネット上の Web アプリにアクセスするには、ロード バランサーのパブリック IP アドレスが必要です。 

[az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) を使用して、以下のことを行います。

* **myPublicIP** という名前の Standard ゾーン冗長パブリック IP アドレスを作成します。
* **myResourceGroupLB** 内に作成します。

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --sku Standard
```

ゾーン 1 にゾーン冗長パブリック IP アドレスを作成するには、次のようにします。

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --sku Standard \
    --zone 1
```

## <a name="create-standard-load-balancer"></a>Standard ロード バランサーを作成する

このセクションでは、ロード バランサーの以下のコンポーネントを作成および構成する方法について説明します。

  * ロード バランサーの着信ネットワーク トラフィックを受け取るフロントエンド IP プール。
  * フロントエンド プールから負荷分散されたネットワーク トラフィックが送信されるバックエンド IP プール。
  * バックエンド VM インスタンスの正常性を判断する正常性プローブ。
  * VM に対するトラフィックの分散方法を定義するロード バランサー規則。

### <a name="create-the-load-balancer-resource"></a>ロード バランサーのリソースを作成する

[az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create) を使用して、次のようにパブリック ロード バランサーを作成します。

* 名前は **myLoadBalancer** にします。
* フロントエンド プールの名前は **myFrontEnd** にします。
* バックエンド プールの名前は **myBackEndPool** にします。
* 前の手順で作成したパブリック IP アドレス **myPublicIP** に関連付けます。 

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupLB \
    --name myLoadBalancer \
    --sku Standard \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>正常性プローブを作成する

正常性プローブは、すべての仮想マシン インスタンスを調べて、ネットワーク トラ	フィックを送信できるかどうかを確認します。 

プローブ チェックが失敗した仮想マシンは、ロード バランサーから削除されます。 障害が解決されると、仮想マシンがロード バランサーに再び追加されます。

[az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create) を使用して、次のように正常性プローブを作成します。

* 仮想マシンの正常性を監視します。
* 名前は **myHealthProbe** にします。
* プロトコルは **TCP** にします。
* **ポート 80** を監視します。

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>ロード バランサー規則を作成する

ロード バランサー規則は、以下のものを定義します。

* 着信トラフィック用のフロントエンド IP 構成。
* トラフィックを受信するためのバックエンド IP プール。
* 必要な発信元ポートと宛先ポート。 

[az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create) を使用して、次のようにロード バランサー規則を作成します。

* 名前は **myHTTPRule** にします
* フロントエンド プール **myFrontEnd** で**ポート 80** をリッスンします。
* **ポート 80** を使用して、負荷分散されたネットワーク トラフィックをバックエンド アドレス プール **myBackEndPool** に送信します。 
* 正常性プローブ **myHealthProbe** を使用します。
* プロトコルは **TCP** にします。
* フロントエンド IP アドレスを使用して、アウトバウンドの送信元ネットワーク アドレス変換 (SNAT) を有効にします。

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --disable-outbound-snat true 
```
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>仮想マシンをロード バランサー バックエンド プールに追加する

[az network nic ip-config address-pool add](https://docs.microsoft.com/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add) を使用して、次のように仮想マシンをバックエンド プールに追加します。

#### <a name="vm1"></a>VM1
* バックエンド アドレス プールは **myBackEndPool** にします。
* リソース グループは **myResourceGroupLB** にします。
* ネットワーク インターフェイス **myNicVM1** および **ipconfig1** に関連付けます。
* ロード バランサー **myLoadBalancer** に関連付けます。

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* バックエンド アドレス プールは **myBackEndPool** にします。
* リソース グループは **myResourceGroupLB** にします。
* ネットワーク インターフェイス **myNicVM2** および **ipconfig1** に関連付けます。
* ロード バランサー **myLoadBalancer** に関連付けます。

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm3"></a>VM3
* バックエンド アドレス プールは **myBackEndPool** にします。
* リソース グループは **myResourceGroupLB** にします。
* ネットワーク インターフェイス **myNicVM3** および **ipconfig1** に関連付けます。
* ロード バランサー **myLoadBalancer** に関連付けます。

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM3 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

## <a name="create-outbound-rule-configuration"></a>アウトバウンド規則構成の作成
ロードバランサーのアウトバウンド規則では、バックエンド プール内の VM 用に送信 SNAT を構成します。 

アウトバウンド接続の詳細については、「[Azure のアウトバウンド接続](load-balancer-outbound-connections.md)」を参照してください。

### <a name="create-outbound-public-ip-address-or-public-ip-prefix"></a>アウトバウンド パブリック IP アドレスまたはパブリック IP プレフィックスを作成します。

[az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) を使用して、アウトバウンド接続用の 1 つの IP を作成します。  

[az network public-ip prefix create](https://docs.microsoft.com/cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-create) を使用して、アウトバウンド接続用のパブリック IP プレフィックスを作成します。

アウトバウンド NAT とアウトバウンド接続のスケーリングの詳細については、「[複数の IP アドレスでアウトバウンド NAT をスケーリングする](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scale)」を参照してください。

#### <a name="public-ip"></a>パブリック IP

* 名前は **myPublicIPOutbound** にします。
* **myResourceGroupLB** 内に作成します。

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIPOutbound \
    --sku Standard
```

ゾーン 1 にゾーン冗長パブリック IP アドレスを作成するには、次のようにします。

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIPOutbound \
    --sku Standard \
    --zone 1
```
#### <a name="public-ip-prefix"></a>パブリック IP プレフィックス

* 名前は **myPublicIPPrefixOutbound** にします。
* **myResourceGroupLB** 内に作成します。
* プレフィックス長は **28** にします。

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupLB \
    --name myPublicIPPrefixOutbound \
    --length 28
```
ゾーン 1 にゾーン冗長パブリック IP プレフィックスを作成するには、次のようにします。

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupLB \
    --name myPublicIPPrefixOutbound \
    --length 28 \
    --zone 1
```

### <a name="create-outbound-frontend-ip-configuration"></a>アウトバウンド フロントエンド IP 構成を作成する

[az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create) を使用して、次のように新しいフロントエンド IP 構成を作成します。

前の手順での決定に基づいて、パブリック IP またはパブリック IP プレフィックスのコマンドを選択します。

#### <a name="public-ip"></a>パブリック IP

* 名前は **myFrontEndOutbound** にします。
* リソース グループは **myResourceGroupLB** にします。
* パブリック IP アドレス **myPublicIPOutbound** に関連付けます。
* ロード バランサー **myLoadBalancer** に関連付けます。

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myResourceGroupLB \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-address myPublicIPOutbound 
```

#### <a name="public-ip-prefix"></a>パブリック IP プレフィックス

* 名前は **myFrontEndOutbound** にします。
* リソース グループは **myResourceGroupLB** にします。
* パブリック IP プレフィックス **myPublicIPPrefixOutbound** に関連付けます。
* ロード バランサー **myLoadBalancer** に関連付けます。

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myResourceGroupLB \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-prefix myPublicIPPrefixOutbound 
```

### <a name="create-outbound-pool"></a>送信プールを作成する

[az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create) を使用して、次のように新しいアウトバウンド プールを作成します。

* 名前は **myBackEndPoolOutbound** にします。
* リソース グループは **myResourceGroupLB** にします。
* ロード バランサー **myLoadBalancer** に関連付けます。

```azurecli-interactive
  az network lb address-pool create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myBackendPoolOutbound
```
### <a name="create-outbound-rule"></a>アウトバウンド規則の作成

[az network lb outbound-rule create](https://docs.microsoft.com/cli/azure/network/lb/outbound-rule?view=azure-cli-latest#az-network-lb-outbound-rule-create) を使用して、次のようにアウトバウンド バックエンド プールの新しいアウトバウンド規則を作成します。

* 名前は **myOutboundRule** にします。
* リソース グループは **myResourceGroupLB** にします。
* ロード バランサー **myLoadBalancer** に関連付けます
* フロントエンド **myFrontEndOutbound** に関連付けます。
* プロトコルは **All** にします。
* アイドル タイムアウトは **15** にします。
* アウトバウンド ポートは **10000** にします。
* バックエンド プール **myBackEndPoolOutbound** に関連付けます。

```azurecli-interactive
  az network lb outbound-rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myOutboundRule \
    --frontend-ip-configs myFrontEndOutbound \
    --protocol All \
    --idle-timeout 15 \
    --outbound-ports 10000 \
    --address-pool myBackEndPoolOutbound
```
### <a name="add-virtual-machines-to-outbound-pool"></a>仮想マシンをアウトバウンド プールに追加する

[az network nic ip-config address-pool add](https://docs.microsoft.com/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add) を使用して、次のように仮想マシンをアウトバウンド プールに追加します。


#### <a name="vm1"></a>VM1
* バックエンド アドレス プールは **myBackEndPoolOutbound** にします。
* リソース グループは **myResourceGroupLB** にします。
* ネットワーク インターフェイス **myNicVM1** および **ipconfig1** に関連付けます。
* ロード バランサー **myLoadBalancer** に関連付けます。

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPoolOutbound \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* バックエンド アドレス プールは **myBackEndPoolOutbound** にします。
* リソース グループは **myResourceGroupLB** にします。
* ネットワーク インターフェイス **myNicVM2** および **ipconfig1** に関連付けます。
* ロード バランサー **myLoadBalancer** に関連付けます。

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPoolOutbound \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm3"></a>VM3
* バックエンド アドレス プールは **myBackEndPoolOutbound** にします。
* リソース グループは **myResourceGroupLB** にします。
* ネットワーク インターフェイス **myNicVM3** および **ipconfig1** に関連付けます。
* ロード バランサー **myLoadBalancer** に関連付けます。

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPoolOutbound \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM3 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

# <a name="basic-sku"></a>[**Basic SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>運用環境のワークロードには、Standard SKU ロード バランサーをお勧めします。 SKU の詳細については、「 **[Azure Load Balancer の SKU](skus.md)** 」を参照してください。

## <a name="configure-virtual-network"></a>仮想ネットワークを構成する

VM をデプロイしてロード バランサーをテストする前に、サポート用の仮想ネットワーク リソースを作成します。

### <a name="create-a-virtual-network"></a>仮想ネットワークの作成

[az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt) を使用して、次のように仮想ネットワークを作成します。

* 名前は **myVNet** にします。
* アドレス プレフィックスは **10.1.0.0/16** にします。
* サブネットの名前は **myBackendSubnet** にします。
* サブネット プレフィックスは **10.1.0.0/24** にします。
* リソース グループは **myResourceGroupLB** にします。
* 場所は **eastus** にします。

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupLB \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

Standard ロード バランサーの場合、バックエンドが扱う VM には、ネットワーク セキュリティ グループに属しているネットワーク インターフェイスが必要です。 

[az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) を使用して、次のようにネットワーク セキュリティ グループを作成します。

* 名前は **myNSG** にします。
* リソース グループは **myResourceGroupLB** にします。

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupLB \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>ネットワーク セキュリティ グループ規則を作成する

[az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) を使用して、次のようにネットワーク セキュリティ グループの規則を作成します。

* 名前は **myNSGRuleHTTP** にします。
* ネットワーク セキュリティ グループは、前の手順で作成した **myNSG** にします。
* リソース グループは **myResourceGroupLB** にします。
* プロトコルは **(*)** にします。
* 方向は **Inbound** にします。
* 送信元は **(*)** にします。
* 送信先は **(*)** にします。
* 送信先ポートは**ポート 80** にします。
* アクセスは **Allow** にします。
* 優先度は **200** にします。

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupLB \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

### <a name="create-network-interfaces-for-the-virtual-machines"></a>仮想マシンのネットワーク インターフェイスを作成する

[az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) を使用して、次のように 3 つのネットワーク インターフェイスを作成します。

#### <a name="vm1"></a>VM1

* 名前は **myNicVM1** にします。
* リソース グループは **myResourceGroupLB** にします。
* 仮想ネットワークは **myVNet** にします。
* サブネットは **myBackendSubnet** にします。
* ネットワーク セキュリティ グループは **myNSG** にします。

```azurecli-interactive

  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>VM2

* 名前は **myNicVM2** にします。
* リソース グループは **myResourceGroupLB** にします。
* 仮想ネットワークは **myVNet** にします。
* サブネットは **myBackendSubnet** にします。
* ネットワーク セキュリティ グループは **myNSG** にします。

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM2 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm3"></a>VM3

* 名前は **myNicVM3** にします。
* リソース グループは **myResourceGroupLB** にします。
* 仮想ネットワークは **myVNet** にします。
* サブネットは **myBackendSubnet** にします。
* ネットワーク セキュリティ グループは **myNSG** にします。

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM3 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

## <a name="create-backend-servers"></a>バックエンド サーバーの作成

このセクションでは、以下を作成します。

* サーバー構成のための **cloud-init.txt** という名前のクラウド構成ファイル。 
* 仮想マシンの可用性セット
* ロード バランサーのバックエンド サーバーとして使用する 3 つの仮想マシン。


ロード バランサーが正常に作成されたことを確認するには、仮想マシンに NGINX をインストールします。

### <a name="create-cloud-init-configuration-file"></a>cloud-init 構成ファイルを作成する

cloud-init 構成ファイルを使用して、NGINX をインストールし、Linux 仮想マシンで "Hello World" Node.js アプリを実行します。 

現在のシェルで、cloud-init.txt という名前のファイルを作成します。 以下の構成をコピーしてシェルに貼り付けます。 cloud-init ファイル全体を、特に最初の行に注意して正確にコピーしてください。

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
### <a name="create-availability-set-for-virtual-machines"></a>仮想マシンの可用性セットを作成する

[az vm availability-set create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest#az-vm-availability-set-create) を使用して、次のように可用性セットを作成します。

* 名前は **myAvSet** にします。
* リソース グループは **myResourceGroupLB** にします。
* 場所は **eastus** にします。

```azurecli-interactive
  az vm availability-set create \
    --name myAvSet \
    --resource-group myResourceGroupLB \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>仮想マシンを作成する

[az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) を使用して、次のように仮想マシンを作成します。

#### <a name="vm1"></a>VM1
* 名前は **myVM1** にします。
* リソース グループは **myResourceGroupLB** にします。
* ネットワーク インターフェイス **myNicVM1** に接続します。
* 仮想マシン イメージは **UbuntuLTS** にします。
* 構成ファイルは、前の手順で作成した **cloud-init.txt** にします。
* 可用性セットは **myAvSet** にします。

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet \
    --no-wait 
```
#### <a name="vm2"></a>VM2
* 名前は **myVM2** にします。
* リソース グループは **myResourceGroupLB** にします。
* ネットワーク インターフェイス **myNicVM2** に接続します。
* 仮想マシン イメージは **UbuntuLTS** にします。
* 構成ファイルは、前の手順で作成した **cloud-init.txt** にします。
* ゾーンは **Zone 2** にします。

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet  \
    --no-wait
```

#### <a name="vm3"></a>VM3
* 名前は **myVM3** にします。
* リソース グループは **myResourceGroupLB** にします。
* ネットワーク インターフェイス **myNicVM3** に接続します。
* 仮想マシン イメージは **UbuntuLTS** にします。
* 構成ファイルは、前の手順で作成した **cloud-init.txt** にします。
* ゾーンは **Zone 3** にします。

```azurecli-interactive
   az vm create \
    --resource-group myResourceGroupLB \
    --name myVM3 \
    --nics myNicVM3 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet  \
    --no-wait
```
VM がデプロイされるまでに、数分かかる場合があります。


## <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成

インターネット上の Web アプリにアクセスするには、ロード バランサーのパブリック IP アドレスが必要です。 

[az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) を使用して、以下のことを行います。

* **myPublicIP** という名前の Standard ゾーン冗長パブリック IP アドレスを作成します。
* **myResourceGroupLB** 内に作成します。

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --sku Basic
```

## <a name="create-basic-load-balancer"></a>Basic ロード バランサーを作成する

このセクションでは、ロード バランサーの以下のコンポーネントを作成および構成する方法について説明します。

  * ロード バランサーの着信ネットワーク トラフィックを受け取るフロントエンド IP プール。
  * フロントエンド プールから負荷分散されたネットワーク トラフィックが送信されるバックエンド IP プール。
  * バックエンド VM インスタンスの正常性を判断する正常性プローブ。
  * VM に対するトラフィックの分散方法を定義するロード バランサー規則。

### <a name="create-the-load-balancer-resource"></a>ロード バランサーのリソースを作成する

[az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create) を使用して、次のようにパブリック ロード バランサーを作成します。

* 名前は **myLoadBalancer** にします。
* フロントエンド プールの名前は **myFrontEnd** にします。
* バックエンド プールの名前は **myBackEndPool** にします。
* 前の手順で作成したパブリック IP アドレス **myPublicIP** に関連付けます。 

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupLB \
    --name myLoadBalancer \
    --sku Basic \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>正常性プローブを作成する

正常性プローブは、すべての仮想マシン インスタンスを調べて、ネットワーク トラ	フィックを送信できるかどうかを確認します。 

プローブ チェックが失敗した仮想マシンは、ロード バランサーから削除されます。 障害が解決されると、仮想マシンがロード バランサーに再び追加されます。

[az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create) を使用して、次のように正常性プローブを作成します。

* 仮想マシンの正常性を監視します。
* 名前は **myHealthProbe** にします。
* プロトコルは **TCP** にします。
* **ポート 80** を監視します。

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>ロード バランサー規則を作成する

ロード バランサー規則は、以下のものを定義します。

* 着信トラフィック用のフロントエンド IP 構成。
* トラフィックを受信するためのバックエンド IP プール。
* 必要な発信元ポートと宛先ポート。 

[az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create) を使用して、次のようにロード バランサー規則を作成します。

* 名前は **myHTTPRule** にします
* フロントエンド プール **myFrontEnd** で**ポート 80** をリッスンします。
* **ポート 80** を使用して、負荷分散されたネットワーク トラフィックをバックエンド アドレス プール **myBackEndPool** に送信します。 
* 正常性プローブ **myHealthProbe** を使用します。
* プロトコルは **TCP** にします。

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>仮想マシンをロード バランサー バックエンド プールに追加する

[az network nic ip-config address-pool add](https://docs.microsoft.com/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add) を使用して、次のように仮想マシンをバックエンド プールに追加します。


#### <a name="vm1"></a>VM1
* バックエンド アドレス プールは **myBackEndPool** にします。
* リソース グループは **myResourceGroupLB** にします。
* ネットワーク インターフェイス **myNicVM1** および **ipconfig1** に関連付けます。
* ロード バランサー **myLoadBalancer** に関連付けます。

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* バックエンド アドレス プールは **myBackEndPool** にします。
* リソース グループは **myResourceGroupLB** にします。
* ネットワーク インターフェイス **myNicVM2** および **ipconfig1** に関連付けます。
* ロード バランサー **myLoadBalancer** に関連付けます。

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm3"></a>VM3
* バックエンド アドレス プールは **myBackEndPool** にします。
* リソース グループは **myResourceGroupLB** にします。
* ネットワーク インターフェイス **myNicVM3** および **ipconfig1** に関連付けます。
* ロード バランサー **myLoadBalancer** に関連付けます。

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM3 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```
---

## <a name="test-the-load-balancer"></a>ロード バランサーをテストする

ロード バランサーのパブリック IP アドレスを取得するには、[az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show) を使用します。 

そのパブリック IP アドレスをコピーし、ブラウザーのアドレス バーに貼り付けます。

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```
:::image type="content" source="./media/load-balancer-standard-public-cli/running-nodejs-app.png" alt-text="ロード バランサーをテストする" border="true":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、[az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) コマンドを使用して、リソース グループ、ロード バランサー、およびすべての関連リソースを削除します。

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB
```

## <a name="next-steps"></a>次のステップ
このクイックスタートでは、

* Standard またはパブリック ロード バランサーを作成しました
* 仮想マシンを接続しました。 
* ロード バランサーのトラフィック規則と正常性プローブを構成しました。
* ロード バランサーをテストしました。

Azure Load Balancer の詳細については、「[Azure Load Balancer の概要](load-balancer-overview.md)」および「[Load Balancer に関してよく寄せられる質問](load-balancer-faqs.md)」を参照してください。

[Load Balancer と可用性ゾーン](load-balancer-standard-availability-zones.md)について理解を深めます。
