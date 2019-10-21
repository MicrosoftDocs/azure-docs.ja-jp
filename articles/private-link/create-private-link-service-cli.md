---
title: Azure CLI を使用して Azure プライベート リンク サービスを作成する
description: Azure CLI を使用して Azure プライベート リンク サービスを作成する方法について説明します
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: c67d2cd4e90b2fa61a4d95e89a68c888a6e1fe3f
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273648"
---
# <a name="create-a-private-link-service-using-azure-cli"></a>Azure CLI を使用してプライベート リンク サービスを作成する
この記事では、Azure CLI を使用して Azure 上でプライベート リンク サービスを作成する方法を示します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

代わりに、Azure CLI をローカルにインストールして使用する場合、このクイックスタートでは、最新の Azure CLI バージョンを使用する必要があります。 インストールされているバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードについては、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」をご覧ください。
## <a name="create-a-private-link-service"></a>Private Link サービスを作成する
### <a name="create-a-resource-group"></a>リソース グループの作成

仮想ネットワークを作成するには、その前に、仮想ネットワークをホストするリソース グループを作成する必要があります。 [az group create](/cli/azure/group) を使用して、リソース グループを作成します。 この例では、*myResourceGroup* という名前のリソース グループを場所 *westcentralus* に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```
### <a name="create-a-virtual-network"></a>仮想ネットワークの作成
[az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) を使用して仮想ネットワークを作成します。 この例では、*mySubnet* という名前のサブネットを使って、*myVirtualNetwork* という名前の既定の仮想ネットワークを作成します。

```azurecli-interactive
az network vnet create --resource-group myResourceGroup --name myVirtualNetwork --address-prefix 10.0.0.0/16  
```
### <a name="create-a-subnet"></a>サブネットの作成
[az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) を使って、仮想ネットワーク用のサブネットを作成します。 この例では、*myVirtualNetwork* 仮想ネットワークに *mySubnet* という名前のサブネットを作成します。

```azurecli-interactive
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --address-prefixes 10.0.0.0/24    
```
### <a name="create-a-internal-load-balancer"></a>内部ロード バランサーを作成する 
[az network lb create](/cli/azure/network/lb#az-network-lb-create) を使って、内部ロード バランサーを作成します。 この例では、*myResourceGroup* という名前のリソース グループに、*myILB* という名前の内部ロード バランサーを作成します。 

```azurecli-interactive
az network lb create --resource-group myResourceGroup --name myILB --sku standard --vnet-name MyVirtualNetwork --subnet mySubnet --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool
```

### <a name="create-a-load-balancer-health-probe"></a>ロード バランサーの正常性プローブの作成

正常性プローブは、ネットワーク トラフィックを受信できるように、すべての仮想マシン インスタンスを確認します。 プローブのチェックで失敗した仮想マシン インスタンスは、オンラインに戻り、プローブ チェックにより正常と判定されるまで、ロード バランサーから削除されます。 [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) を使用して正常性プローブを作成し、仮想マシンの正常性を監視します。 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-a-load-balancer-rule"></a>ロード バランサー規則の作成

ロード バランサー規則は、着信トラフィック用のフロントエンド IP 構成と、トラフィックを受信するためのバックエンド IP プールを、必要な発信元ポートと宛先ポートと共に定義します。 [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) を使用してロード バランサー規則 *myHTTPRule* を作成します。この規則では、フロントエンド プール *myFrontEnd* のポート 80 をリッスンし、同じポート 80 を使用して、負荷分散されたネットワーク トラフィックをバックエンド アドレス プール *myBackEndPool* に送信します。 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```
### <a name="create-backend-servers"></a>バックエンド サーバーの作成

この例では、仮想マシンの作成については説明しません。 「[Azure CLI を使用して VM の負荷を分散する内部ロード バランサーを作成する](../load-balancer/load-balancer-get-started-ilb-arm-cli.md#create-servers-for-the-backend-address-pool)」の手順に従って、ロード バランサー用のバックエンド サーバーとして使用される 2 つの仮想マシンを作成できます。 


### <a name="disable-private-link-service-network-policies-on-subnet"></a>サブネット上で Private Link サービス ネットワーク ポリシーを無効にする 
プライベート リンク サービスでは、仮想ネットワーク内の任意のサブネットからの IP が必要になります。 現時点では、これらの IP 上でのネットワーク ポリシーはサポートされていません。  そのため、サブネット上のネットワーク ポリシーを無効にする必要があります。 [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) を使って、サブネットを更新して Private Link サービス ネットワーク ポリシーを無効にします。

```azurecli-interactive
az network vnet subnet update --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --disable-private-link-service-network-policies true 
```
 
## <a name="create-a-private-link-service"></a>Private Link サービスを作成する  
 
[az network private-link-service create](/cli/azure/network/private-link-service#az-network-private-link-service-create) を使って、Standard Load Balancer フロントエンド IP 構成を使用してプライベート リンク サービスを作成します。 この例では、*myResourceGroup* という名前のリソース グループ内で *myLoadBalancer* という名前の Standard Load Balancer を使用して、*myPLS* という名前のプライベート リンク サービスを作成します。 
 
```azurecli-interactive
az network private-link-service create \
--resource-group myResourceGroup \
--name myPLS \
--vnet-name myVirtualNetwork \
--subnet mySubnet \
--lb-name myILB \
--lb-frontend-ip-configs myFrontEnd \
--location westcentralus 
```
作成したら、そのプライベート リンクのサービス ID を書き留めておきます。 このサービスへの接続を要求するために、後で必要になります。  
 
この段階で、プライベート リンク サービスが正常に作成され、トラフィックを受信する準備が整いました。 上記の例は、Azure CLI を使用したプライベート リンク サービスの作成を示すことのみを目的としていることに注意してください。  ロード バランサーのバックエンド プールまたはバックエンド プール上のアプリケーションは、トラフィックをリッスンするようには構成されていません。 エンドツーエンドのトラフィック フローを確認する予定なら、お使いの Standard Load Balancer の背後にアプリケーションを構成することを強くお勧めします。  
 
次に、Azure CLI を使用して、異なる 仮想ネットワーク内でプライベート エンドポイントにこのサービスをマップする方法について説明します。 例ではやはり、プライベート エンドポイントを作成すること、また、Azure CLI を使用して上記で作成したプライベート リンク サービスへ接続することに的を絞っています。 さらに、仮想ネットワーク内に仮想マシンを作成して、プライベート エンドポイントに対してトラフィックを送受信することができます。        
 
## <a name="private-endpoints"></a>プライベート エンドポイント

### <a name="create-the-virtual-network"></a>仮想ネットワークの作成 
 [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) を使って、仮想ネットワークを作成します。 この例では、*myResourcegroup* という名前のリソース グループ内に  *myPEVNet*  という名前の仮想ネットワークを作成します。 
```azurecli-interactive
az network vnet create \
--resource-group myResourceGroup \
--name myPEVnet \
--address-prefix 10.0.0.0/16  
```
### <a name="create-the-subnet"></a>サブネットを作成する 
 [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) を使って、仮想ネットワーク内にサブネットを作成します。 この例では、*myResourcegroup* という名前のリソース グループ内で *myPEVnet* という名前の仮想ネットワークに、 *mySubnet*  という名前のサブネットを作成します。 

```azurecli-interactive 
az network vnet subnet create \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--address-prefixes 10.0.0.0/24 
```   
## <a name="disable-private-endpoint-network-policies-on-subnet"></a>サブネット上でプライベート エンドポイントのネットワーク ポリシーを無効にする 
プライベート エンドポイントは、仮想ネットワークにある任意のサブネット内に作成できます。 現時点では、プライベート エンドポイント上でのネットワーク ポリシーはサポートされていません。  そのため、サブネット上のネットワーク ポリシーを無効にする必要があります。 [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) を使って、サブネットを更新して、プライベート エンドポイント ネットワーク ポリシーを無効にします。 

```azurecli-interactive
az network vnet subnet update \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--disable-private-endpoint-network-policies true 
```
## <a name="create-private-endpoint-and-connect-to-private-link-service"></a>プライベート エンドポイントを作成してプライベート リンクサービスに接続する 
上記で作成したプライベート リンク サービスを使用するためのプライベート エンドポイントを仮想ネットワーク内に作成します。
  
```azurecli-interactive
az network private-endpoint create \
--resource-group myResourceGroup \
--name myPE \
--vnet-name myPEVnet \
--subnet myPESubnet \
--private-connection-resource-id {PLS_resourceURI} \
--connection-name myPEConnectingPLS \
--location westcentralus 
```
プライベート リンク サービスに対して `az network private-link-service show` を使って、*private-connection-resource-id* を取得できます。 ID は次のようになります。   
/subscriptions/subID/resourceGroups/*resourcegroupname*/providers/Microsoft.Network/privateLinkServices/**privatelinkservicename** 
 
## <a name="show-private-link-service-connections"></a>プライベート リンク サービスの接続を表示する 
 
[az network private-link-service show](/cli/azure/network/private-link-service#az-network-private-link-service-show) を使って、ご自身のプライベート リンク サービスでの接続要求を確認します。    
```azurecli-interactive 
az network private-link-service show --resource-group myResourceGroup --name myPLS 
```
## <a name="next-steps"></a>次の手順
- [Azure Private Link サービス](private-link-service-overview.md)についてさらに学習する
 
