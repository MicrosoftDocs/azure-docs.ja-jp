---
title: Azure CLI を使用してゾーン冗長 VM の負荷を分散する
titlesuffix: Azure Load Balancer
description: ゾーン冗長フロントエンドを使用したパブリック Standard Load Balancer を Azure CLI で作成する方法について説明します
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/09/2018
ms.author: allensu
ms.openlocfilehash: 6a22ac9a2727c537d98e692e67076637fe8cc457
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274328"
---
#  <a name="load-balance-vms-across-all-availability-zones-using-azure-cli"></a>Azure CLI を使用してすべての可用性ゾーン間で VM の負荷を分散する

この記事では、複数の DNS レコードに依存せずにゾーン冗長性を実現するために、ゾーン冗長フロントエンドを使用するパブリック [Standard Load Balancer](https://aka.ms/azureloadbalancerstandard) を作成する手順について説明します。 単一のフロントエンド IP アドレスが自動的にゾーン冗長になります。  単一の IP アドレスを持つロード バランサーにゾーン冗長フロントエンドを使用すると、すべての可用性ゾーン全体でリージョン内の仮想ネットワークの任意の VM に到達できるようになります。 可用性ゾーンは、データセンター全体に及ぶ珍しい障害や損失からアプリとデータを保護するために使用します。

可用性ゾーンと Standard Load Balancer の使用方法の詳細については、「[Standard Load Balancer と可用性ゾーン](load-balancer-standard-availability-zones.md)」を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.17 以降を実行していることが要件です。  バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。 

> [!NOTE]
> 可用性ゾーンのサポートは、Azure リソース、リージョン、および VM サイズ ファミリを選択するために使用できます。 使用を開始する方法、および可用性ゾーンを試行する場合にどの Azure リソース、リージョン、および VM サイズ ファミリを使用できるかの詳細については、「[Overview of Availability Zones (可用性ゾーンの概要)](https://docs.microsoft.com/azure/availability-zones/az-overview)」を参照してください。 サポートについては、[StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) でアクセスするか、または [Azure サポート チケットを開く](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json)ことができます。  

## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#az-group-create) を使用して、リソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

次の例では、*myResourceGroupSLB* という名前のリソース グループを *westeurope* の場所に作成します。

```azurecli-interactive
az group create \
--name myResourceGroupSLB \
--location westeurope
```

## <a name="create-a-zone-redundant-public-ip-standard"></a>ゾーン冗長パブリック IP Standard を作成する
インターネット上のアプリにアクセスするには、ロード バランサーのパブリック IP アドレスが必要です。 ゾーン冗長フロントエンドは、リージョン内のすべての可用性ゾーンによって同時に提供されます。 [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) を使用して、ゾーン冗長パブリック IP アドレスを作成します。 Standard パブリック IP アドレスを作成した場合は既定でゾーン冗長です。

次の例では、*myPublicIP* という名前のゾーン冗長パブリック IP アドレスを *myResourceGroupLoadBalancer* リソース グループに作成します。

```azurecli-interactive
az network public-ip create \
--resource-group myResourceGroupSLB \
--name myPublicIP \
--sku Standard
```

## <a name="create-azure-standard-load-balancer"></a>Azure Standard Load Balancer を作成する
このセクションでは、ロード バランサーの以下のコンポーネントを作成および構成する方法について説明します。
- ロード バランサーの着信ネットワーク トラフィックを受け取るフロントエンド IP プール。
- フロントエンド プールから負荷分散されたネットワーク トラフィックが送信されるバックエンド IP プール。
- バックエンド VM インスタンスの正常性を判断する正常性プローブ。
- VM に対するトラフィックの分散方法を定義するロード バランサー規則。

### <a name="create-the-load-balancer"></a>ロード バランサーを作成する
Standard ロード バランサーは、[az network lb create](/cli/azure/network/lb#az-network-lb-create) で作成します。 次の例では、*myLoadBalancer* という名前のロード バランサーを作成し、*myPublicIP* アドレスをフロントエンド IP 構成に割り当てます。

```azurecli-interactive
az network lb create \
--resource-group myResourceGroupSLB \
--name myLoadBalancer \
--public-ip-address myPublicIP \
--frontend-ip-name myFrontEnd \
--backend-pool-name myBackEndPool \
--sku Standard
```

## <a name="create-health-probe-on-port-80"></a>ポート 80 で正常性プローブを作成する

正常性プローブは、すべての仮想マシン インスタンスを確認し、ネットワーク トラフィックを送信できるかどうかを確認します。 プローブのチェックで失敗した仮想マシン インスタンスは、オンラインに戻り、プローブ チェックにより正常と判定されるまで、ロード バランサーから削除されます。 az network lb probe create を使用して正常性プローブを作成し、仮想マシンの正常性を監視します。 TCP 正常性プローブを作成するには、[az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create) を使用します。 次の例では、*myHealthProbe* という名前の正常性プローブを作成します。

```azurecli-interactive
az network lb probe create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myHealthProbe \
--protocol tcp \
--port 80
```

## <a name="create-load-balancer-rule-for-port-80"></a>ポート 80 のロード バランサー規則を作成する
ロード バランサー規則は、着信トラフィック用のフロントエンド IP 構成と、トラフィックを受信するためのバックエンド IP プールを、必要な発信元ポートと宛先ポートと共に定義します。 [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) を使用してロード バランサー規則 *myLoadBalancerRuleWeb* を作成します。この規則では、フロントエンド プール *myFrontEndPool* のポート 80 をリッスンし、同じポート 80 を使用して、負荷分散されたネットワーク トラフィックをバックエンド アドレス プール *myBackEndPool* に送信します。

```azurecli-interactive
az network lb rule create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myLoadBalancerRuleWeb \
--protocol tcp \
--frontend-port 80 \
--backend-port 80 \
--frontend-ip-name myFrontEnd \
--backend-pool-name myBackEndPool \
--probe-name myHealthProbe
```

## <a name="configure-virtual-network"></a>仮想ネットワークを構成する
いくつかの VM をデプロイしてロード バランサーをテストする前に、サポート用の仮想ネットワーク リソースを作成します。

### <a name="create-a-virtual-network"></a>仮想ネットワークの作成

[az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) を使用して、myResourceGroup に *mySubnet* という名前のサブネットがある *myVnet* という名前の仮想ネットワークを作成します。


```azurecli-interactive
az network vnet create \
--resource-group myResourceGroupSLB \
--location westeurope \
--name myVnet \
--subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

*myNetworkSecurityGroup* という名前のネットワーク セキュリティ グループを作成して、[az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) で仮想ネットワークへの受信接続を定義します。

```azurecli-interactive
az network nsg create \
--resource-group myResourceGroupSLB \
--name myNetworkSecurityGroup
```

[az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) でポート 80 の *myNetworkSecurityGroupRule* という名前のネットワーク セキュリティ グループ規則を作成します。

```azurecli-interactive
az network nsg rule create \
--resource-group myResourceGroupSLB \
--nsg-name myNetworkSecurityGroup \
--name myNetworkSecurityGroupRule \
--protocol tcp \
--direction inbound \
--source-address-prefix '*' \
--source-port-range '*' \
--destination-address-prefix '*' \
--destination-port-range 80 \
--access allow \
--priority 200
```
### <a name="create-nics"></a>NIC の作成
[az network nic create](/cli/azure/network/nic#az-network-nic-create)で 3 つの仮想 NIC を作成し、パブリック IP アドレスとネットワーク セキュリティ グループに関連付けます。 以下の例では、6 つの仮想 NIC を作成します (以降の手順では、アプリ用に作成する VM ごとに仮想 NIC を 1 つ)。 いつでも追加の仮想 NIC と VM を作成してロード バランサーに追加することができます。

```azurecli-interactive
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupSLB \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```
## <a name="create-backend-servers"></a>バックエンド サーバーの作成
この例では、ゾーン 1、ゾーン 2、およびゾーン 3 に配置された 3 つの仮想マシンを作成して、ロード バランサーのバックエンド サーバーとして使用します。 ロード バランサーが正常に作成されたことを確認するには、仮想マシンに NGINX もインストールします。

### <a name="create-cloud-init-config"></a>cloud-init 構成を作成する

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

### <a name="create-the-zonal-virtual-machines"></a>ゾーン仮想マシンを作成する
[az vm create](/cli/azure/vm#az-vm-create) を使用してゾーン 1、ゾーン 2、ゾーン 3 に VM を作成します。 次の例では、各ゾーンに VM を作成し、SSH キーが存在しない場合は生成します。

*westeurope* の場所の各ゾーン (ゾーン 1、ゾーン 2、ゾーン 3) に VM を作成します。

```azurecli-interactive
for i in `seq 1 3`; do
  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --zone $i \
    --custom-data cloud-init.txt
done
```
## <a name="test-the-load-balancer"></a>ロード バランサーをテストする

[az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) を使用して、ロード バランサーのパブリック IP アドレスを取得します。 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
このパブリック IP アドレスを Web ブラウザーに入力できます。 ロード バランサーがトラフィックの分散を開始する前に、VM の準備が整うまで数分かかることを忘れないでください。 次の例のように、アプリが表示され、ロード バランサーによって負荷分散されたトラフィックの宛先となった VM のホスト名が表示されます。

![実行中の Node.js アプリ](./media/load-balancer-standard-public-zone-redundant-cli/running-nodejs-app.png)

アプリケーションを実行している 3 つの可用性ゾーンすべての VM 間でトラフィックをロード バランサーが分散していることを確認するには、特定のゾーンの VM を停止し、ブラウザーを最新の情報に更新します。

## <a name="next-steps"></a>次の手順
- [Standard Load Balancer](./load-balancer-standard-overview.md) の詳細を確認する



