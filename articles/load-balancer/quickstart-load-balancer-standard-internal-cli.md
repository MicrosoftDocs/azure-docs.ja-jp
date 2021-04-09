---
title: クイック スタート:内部ロード バランサーを作成する - Azure CLI
titleSuffix: Azure Load Balancer
description: このクイックスタートでは、Azure CLI を使用して内部ロード バランサーを作成する方法について説明します。
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internal traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/19/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 10ac477bed97d2a48344aa8ef9b570d2b6203345
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2021
ms.locfileid: "101702625"
---
# <a name="quickstart-create-an-internal-load-balancer-by-using-the-azure-cli"></a>クイックスタート: Azure CLI を使用した内部ロード バランサーの作成

Azure CLI を使用して内部ロード バランサーと 3 つの仮想マシンを作成することにより、Azure Load Balancer の使用を開始します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

このクイックスタートには、Azure CLI のバージョン 2.0.28 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

>[!NOTE]
>Azure Load Balancer Standard は、運用環境のワークロードに推奨される選択肢です。 この記事には、Azure Load Balancer Standard および Azure Load Balancer Basic に関する情報が含まれています。 SKU の詳細については、「[Azure Load Balancer の SKU](skus.md)」を参照してください。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[az group create](/cli/azure/group#az_group_create) を使用して、リソース グループを作成します。 リソース グループに **CreateIntLBQS-rg** という名前を付け、場所を **eastus** として指定します。

```azurecli-interactive
  az group create \
    --name CreateIntLBQS-rg \
    --location eastus

```

## <a name="azure-load-balancer-standard"></a>Azure Load Balancer Standard

このセクションでは、仮想マシンの負荷分散を行うロード バランサーを作成します。 内部ロード バランサーを作成すると、仮想ネットワークがロード バランサー用のネットワークとして構成されます。 次の図は、このクイックスタートで作成されるリソースを示したものです。

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal.png" alt-text="クイックスタートで作成される標準的なロード バランサー リソース。" border="false":::

### <a name="configure-the-virtual-network"></a>仮想ネットワークの構成

VM をデプロイしてロード バランサーをデプロイする前に、サポート用の仮想ネットワーク リソースを作成します。

#### <a name="create-a-virtual-network"></a>仮想ネットワークの作成

[az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) を使用して、仮想ネットワークを作成します。 次の指定を行います。

* 名前は **myVNet** にします
* アドレス プレフィックスは **10.1.0.0/16** にします
* サブネットの名前は **myBackendSubnet** にします
* サブネット プレフィックスは **10.1.0.0/24** にします
* 作成先のリソース グループは **CreateIntLBQS-rg** とします
* 場所は **eastus** にします

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

#### <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成

[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) を使用して、Azure Bastion ホストのパブリック IP アドレスを作成します。 次の指定を行います。

* **myBastionIP** という名前の Standard ゾーン冗長パブリック IP アドレスを作成します
* 作成先のリソース グループは **CreateIntLBQS-rg** とします

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionIP \
    --sku Standard
```
#### <a name="create-an-azure-bastion-subnet"></a>Azure Bastion サブネットを作成する

[az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) を使用して、サブネットを作成します。 次の指定を行います。

* 名前は **AzureBastionSubnet** にします
* アドレス プレフィックスは **10.1.1.0/24** にします
* 仮想ネットワークは **myVNet** にします
* 作成先のリソース グループは **CreateIntLBQS-rg** とします

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg  \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

#### <a name="create-an-azure-bastion-host"></a>Azure Bastion ホストを作成する

[az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) を使用して、ホストを作成します。 次の指定を行います。

* 名前は **myBastionHost** にします
* 作成先のリソース グループは **CreateIntLBQS-rg** とします
* パブリック IP **myBastionIP** に関連付けます
* 仮想ネットワーク **myVNet** に関連付けます
* 場所は **eastus** にします

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Azure Bastion ホストがデプロイされるまでに数分かかる場合があります。

#### <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

Standard ロード バランサーの場合、VM には、ネットワーク セキュリティ グループに属しているネットワーク インターフェイスが必要です。 [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) を使用して、ネットワーク セキュリティ グループを作成します。 次の指定を行います。

* 名前は **myNSG** にします
* 作成先のリソース グループは **CreateIntLBQS-rg** とします

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

#### <a name="create-a-network-security-group-rule"></a>ネットワーク セキュリティ グループ規則を作成する

[az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) を使用して、ネットワーク セキュリティ グループの規則を作成します。 次の指定を行います。

* 名前は **myNSGRuleHTTP** にします
* ネットワーク セキュリティ グループは、前の手順で作成した **myNSG** にします
* 作成先のリソース グループは **CreateIntLBQS-rg** とします
* プロトコルは **(*)** にします
* 方向は **Inbound** にします
* 送信元は **(*)** にします
* 送信先は **(*)** にします
* 送信先ポートは **ポート 80** にします
* アクセスは **Allow** にします
* 優先度は **200** にします

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
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

### <a name="create-back-end-servers"></a>バックエンド サーバーを作成する

このセクションでは、以下を作成します。

* 仮想マシンに使用する 3 つのネットワーク インターフェイス。
* ロード バランサーのサーバーとして使用する 3 つの仮想マシン。

#### <a name="create-network-interfaces-for-the-virtual-machines"></a>仮想マシンのネットワーク インターフェイスを作成する

[az network nic create](/cli/azure/network/nic#az-network-nic-create) を使用して、3 つのネットワーク インターフェイスを作成します。 次の指定を行います。

* それぞれ名前は **myNicVM1**、**myNicVM2**、**myNicVM3** とします
* 作成先のリソース グループは **CreateIntLBQS-rg** とします
* 仮想ネットワークは **myVNet** にします
* サブネットは **myBackendSubnet** にします
* ネットワーク セキュリティ グループは **myNSG** にします

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreateIntLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

#### <a name="create-the-virtual-machines"></a>仮想マシンの作成

[az vm create](/cli/azure/vm#az-vm-create) で、仮想マシンを作成します。 次の指定を行います。

* それぞれの名前は **myVM1**、**myVM2**、**myVM3** とします
* 作成先のリソース グループは **CreateIntLBQS-rg** とします
* ネットワーク インターフェイス **myNicVM1**、**myNicVM2**、および **myNicVM3** に接続します
* 仮想マシン イメージは **win2019datacenter** にします
* **ゾーン 1**、**ゾーン 2**、および **ゾーン 3** 内です

```azurecli-interactive
  array=(1 2 3)
  for n in "${array[@]}"
  do
    az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM$n \
    --nics myNicVM$n \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone $n \
    --no-wait
  done
```

VM がデプロイされるまでに、数分かかる場合があります。

### <a name="create-the-load-balancer"></a>ロード バランサーを作成する

このセクションでは、ロード バランサーの以下のコンポーネントを作成および構成する方法について説明します。

* ロード バランサーの着信ネットワーク トラフィックを受け取る IP プール。
* 1 つ目のプールから負荷分散されたネットワーク トラフィックが送信される 2 つ目の IP プール。
* VM インスタンスの正常性を判断する正常性プローブ。
* VM に対するトラフィックの分散方法を定義するロード バランサー規則。

#### <a name="create-the-load-balancer-resource"></a>ロード バランサーのリソースを作成する

[az network lb create](/cli/azure/network/lb#az-network-lb-create) を使用して、パブリック ロード バランサーを作成します。 次の指定を行います。

* 名前は **myLoadBalancer** にします
* プールの名前は **myFrontEnd** にします
* プールの名前は **myBackEndPool** にします
* 仮想ネットワーク **myVNet** に関連付けます
* サブネット **myBackendSubnet** に関連付けます

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet myBackendSubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool
```

#### <a name="create-the-health-probe"></a>正常性プローブを作成する

正常性プローブは、すべての仮想マシン インスタンスを調べて、ネットワーク トラ	フィックを送信できるかどうかを確認します。 プローブ チェックが失敗した仮想マシンは、ロード バランサーから削除されます。 障害が解決されると、仮想マシンがロード バランサーに再び追加されます。

正常性プローブは、[az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create) で作成します。 次の指定を行います。

* 仮想マシンの正常性を監視します
* 名前は **myHealthProbe** にします
* プロトコルは **TCP** にします
* **ポート 80** を監視します

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

#### <a name="create-a-load-balancer-rule"></a>ロード バランサー規則の作成

ロード バランサー規則は、以下のものを定義します。

* 着信トラフィック用の IP 構成。
* トラフィックを受信するための IP プール。
* 必要な発信元ポートと宛先ポート。 

ロード バランサー規則は、[az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) で作成します。 次の指定を行います。

* 名前は **myHTTPRule** にします
* プール **myFrontEnd** で **ポート 80** をリッスンします
* **ポート 80** を使用して、負荷分散されたネットワーク トラフィックをアドレス プール **myBackEndPool** に送信します 
* 正常性プローブ **myHealthProbe** を使用します
* プロトコルは **TCP** にします
* アイドル タイムアウトは **15 分** とします
* TCP リセットを有効にします

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15 \
    --enable-tcp-reset true
```

#### <a name="add-vms-to-the-load-balancer-pool"></a>VM をロード バランサー プールに追加する

[az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add) を使用して、仮想マシンをバックエンド プールに追加します。 次の指定を行います。

* アドレス プールは **myBackEndPool** にします
* 作成先のリソース グループは **CreateIntLBQS-rg** とします
* ネットワーク インターフェイス **myNicVM1**、**myNicVM2**、および **myNicVM3** に関連付けます
* ロード バランサー **myLoadBalancer** に関連付けます

```azurecli-interactive
  array=(VM1 VM2 VM3)
  for vm in "${array[@]}"
  do
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNic$vm \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
  done

```

## <a name="azure-load-balancer-basic"></a>Azure Load Balancer Basic

このセクションでは、仮想マシンの負荷分散を行うロード バランサーを作成します。 内部ロード バランサーを作成すると、仮想ネットワークがロード バランサー用のネットワークとして構成されます。 次の図は、このクイックスタートで作成されるリソースを示したものです。

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal-basic.png" alt-text="クイックスタートで作成される基本的なロード バランサー リソース。" border="false":::

### <a name="configure-the-virtual-network"></a>仮想ネットワークの構成

VM をデプロイしてロード バランサーをデプロイする前に、サポート用の仮想ネットワーク リソースを作成します。

#### <a name="create-a-virtual-network"></a>仮想ネットワークの作成

[az network vnet create](/cli/azure/network/vnet#az-network-vnet-createt) を使用して、仮想ネットワークを作成します。 次の指定を行います。

* 名前は **myVNet** にします
* アドレス プレフィックスは **10.1.0.0/16** にします
* サブネットの名前は **myBackendSubnet** にします
* サブネット プレフィックスは **10.1.0.0/24** にします
* 作成先のリソース グループは **CreateIntLBQS-rg** とします
* 場所は **eastus** にします

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

#### <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成

[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) を使用して、Azure Bastion ホストのパブリック IP アドレスを作成します。 次の指定を行います。

* **myBastionIP** という名前の Standard ゾーン冗長パブリック IP アドレスを作成します
* 作成先のリソース グループは **CreateIntLBQS-rg** とします

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
#### <a name="create-an-azure-bastion-subnet"></a>Azure Bastion サブネットを作成する

[az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) を使用して、サブネットを作成します。 次の指定を行います。

* 名前は **AzureBastionSubnet** にします
* アドレス プレフィックスは **10.1.1.0/24** にします
* 仮想ネットワークは **myVNet** にします
* 作成先のリソース グループは **CreateIntLBQS-rg** とします

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

#### <a name="create-an-azure-bastion-host"></a>Azure Bastion ホストを作成する

[az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) を使用して、ホストを作成します。 次の指定を行います。

* 名前は **myBastionHost** にします
* 作成先のリソース グループは **CreateIntLBQS-rg** とします
* パブリック IP **myBastionIP** に関連付けます
* 仮想ネットワーク **myVNet** に関連付けます
* 場所は **eastus** にします

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Azure Bastion ホストがデプロイされるまでに数分かかる場合があります。

#### <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

Standard ロード バランサーの場合、VM には、ネットワーク セキュリティ グループに属しているネットワーク インターフェイスが必要です。 [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) を使用して、ネットワーク セキュリティ グループを作成します。 次の指定を行います。

* 名前は **myNSG** にします
* 作成先のリソース グループは **CreateIntLBQS-rg** とします

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

#### <a name="create-a-network-security-group-rule"></a>ネットワーク セキュリティ グループ規則を作成する

[az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) を使用して、ネットワーク セキュリティ グループの規則を作成します。 次の指定を行います。

* 名前は **myNSGRuleHTTP** にします
* ネットワーク セキュリティ グループは、前の手順で作成した **myNSG** にします
* 作成先のリソース グループは **CreateIntLBQS-rg** とします
* プロトコルは **(*)** にします
* 方向は **Inbound** にします
* 送信元は **(*)** にします
* 送信先は **(*)** にします
* 送信先ポートは **ポート 80** にします
* アクセスは **Allow** にします
* 優先度は **200** にします

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
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

### <a name="create-back-end-servers"></a>バックエンド サーバーを作成する

このセクションでは、以下を作成します。

* 仮想マシンに使用する 3 つのネットワーク インターフェイス。
* 仮想マシンの可用性セット。
* ロード バランサーのサーバーとして使用する 3 つの仮想マシン。

#### <a name="create-network-interfaces-for-the-virtual-machines"></a>仮想マシンのネットワーク インターフェイスを作成する

[az network nic create](/cli/azure/network/nic#az-network-nic-create) を使用して、3 つのネットワーク インターフェイスを作成します。 次の指定を行います。

* それぞれ名前は **myNicVM1**、**myNicVM2**、**myNicVM3** とします
* 作成先のリソース グループは **CreateIntLBQS-rg** とします
* 仮想ネットワークは **myVNet** にします
* サブネットは **myBackendSubnet** にします
* ネットワーク セキュリティ グループは **myNSG** にします

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreateIntLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

#### <a name="create-the-availability-set-for-the-virtual-machines"></a>仮想マシンの可用性セットを作成する

[az vm availability-set create](/cli/azure/vm/availability-set#az-vm-availability-set-create) で、可用性セットを作成します。 次の指定を行います。

* 名前は **myAvailabilitySet** にします
* 作成先のリソース グループは **CreateIntLBQS-rg** とします
* 場所は **eastus** にします

```azurecli-interactive
  az vm availability-set create \
    --name myAvailabilitySet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

#### <a name="create-the-virtual-machines"></a>仮想マシンの作成

[az vm create](/cli/azure/vm#az-vm-create) で、仮想マシンを作成します。 次の指定を行います。

* それぞれの名前は **myVM1**、**myVM2**、**myVM3** とします
* 作成先のリソース グループは **CreateIntLBQS-rg** とします
* ネットワーク インターフェイス **myNicVM1**、**myNicVM2**、および **myNicVM3** に接続します
* 仮想マシン イメージは **win2019datacenter** にします
* **myAvailabilitySet** 内です


```azurecli-interactive
  array=(1 2 3)
  for n in "${array[@]}"
  do
    az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM$n \
    --nics myNicVM$n \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvailabilitySet \
    --no-wait
  done
```
VM がデプロイされるまでに、数分かかる場合があります。

### <a name="create-the-load-balancer"></a>ロード バランサーを作成する

このセクションでは、ロード バランサーの以下のコンポーネントを作成および構成する方法について説明します。

* ロード バランサーの着信ネットワーク トラフィックを受け取る IP プール。
* 1 つ目のプールから負荷分散されたネットワーク トラフィックが送信される 2 つ目の IP プール。
* VM インスタンスの正常性を判断する正常性プローブ。
* VM に対するトラフィックの分散方法を定義するロード バランサー規則。

#### <a name="create-the-load-balancer-resource"></a>ロード バランサーのリソースを作成する

[az network lb create](/cli/azure/network/lb#az-network-lb-create) を使用して、パブリック ロード バランサーを作成します。 次の指定を行います。

* 名前は **myLoadBalancer** にします
* プールの名前は **myFrontEnd** にします
* プールの名前は **myBackEndPool** にします
* 仮想ネットワーク **myVNet** に関連付けます
* サブネット **myBackendSubnet** に関連付けます

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Basic \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool
```

#### <a name="create-the-health-probe"></a>正常性プローブを作成する

正常性プローブは、すべての仮想マシン インスタンスを調べて、ネットワーク トラ	フィックを送信できるかどうかを確認します。 プローブ チェックが失敗した仮想マシンは、ロード バランサーから削除されます。 障害が解決されると、仮想マシンがロード バランサーに再び追加されます。

正常性プローブは、[az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create) で作成します。 次の指定を行います。

* 仮想マシンの正常性を監視します
* 名前は **myHealthProbe** にします
* プロトコルは **TCP** にします
* **ポート 80** を監視します

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

#### <a name="create-a-load-balancer-rule"></a>ロード バランサー規則の作成

ロード バランサー規則は、以下のものを定義します。

* 着信トラフィック用の IP 構成。
* トラフィックを受信するための IP プール。
* 必要な発信元ポートと宛先ポート。 

ロード バランサー規則は、[az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) で作成します。 次の指定を行います。

* 名前は **myHTTPRule** にします
* プール **myFrontEnd** で **ポート 80** をリッスンします
* **ポート 80** を使用して、負荷分散されたネットワーク トラフィックをアドレス プール **myBackEndPool** に送信します 
* 正常性プローブ **myHealthProbe** を使用します
* プロトコルは **TCP** にします
* アイドル タイムアウトは **15 分** とします

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15 
```
#### <a name="add-vms-to-the-load-balancer-pool"></a>VM をロード バランサー プールに追加する

[az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add) を使用して、仮想マシンをバックエンド プールに追加します。 次の指定を行います。

* アドレス プールは **myBackEndPool** にします
* 作成先のリソース グループは **CreateIntLBQS-rg** とします
* ネットワーク インターフェイス **myNicVM1**、**myNicVM2**、および **myNicVM3** に関連付けます
* ロード バランサー **myLoadBalancer** に関連付けます

```azurecli-interactive
  array=(VM1 VM2 VM3)
  for vm in "${array[@]}"
  do
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNic$vm \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
  done

```

## <a name="test-the-load-balancer"></a>ロード バランサーをテストする

[az network nic create](/cli/azure/network/nic#az-network-nic-create) を使用して、ネットワーク インターフェイスを作成します。 次の指定を行います。

* 名前は **myNicTestVM** にします
* 作成先のリソース グループは **CreateIntLBQS-rg** とします
* 仮想ネットワークは **myVNet** にします
* サブネットは **myBackendSubnet** にします
* ネットワーク セキュリティ グループは **myNSG** にします

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
[az vm create](/cli/azure/vm#az-vm-create) を使用して、仮想マシンを作成します。 次の指定を行います。

* 名前は **myTestVM** にします
* 作成先のリソース グループは **CreateIntLBQS-rg** とします
* ネットワーク インターフェイス **myNicTestVM** に接続します
* 仮想マシン イメージは **win2019datacenter** にします

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username azureuser \
    --no-wait
```
仮想マシンがデプロイされるまでに、数分かかる場合があります。

## <a name="install-iis"></a>IIS のインストール

仮想マシンに IIS をインストールし、既定の Web サイトをコンピューター名に設定するには、[az vm extension set](/cli/azure/vm/extension#az_vm_extension_set) を使用します。

```azurecli-interactive
  array=(myVM1 myVM2 myVM3)
    for vm in "${array[@]}"
    do
     az vm extension set \
       --publisher Microsoft.Compute \
       --version 1.8 \
       --name CustomScriptExtension \
       --vm-name $vm \
       --resource-group CreateIntLBQS-rg \
       --settings '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
  done

```

### <a name="test"></a>テスト

1. Azure portal に[サインイン](https://portal.azure.com)します。

2. **[概要]** ページで、ロード バランサーのプライベート IP アドレスを見つけます。 左側のメニューで、 **[すべてのサービス]**  >  **[すべてのリソース]**  >  **[myLoadBalancer]** を選択します。

3. **myLoadBalancer** の概要で、 **[プライベート IP アドレス]** の横にあるアドレスをコピーしておきます。

4. 左側のメニューで、 **[すべてのサービス]**  >  **[すべてのリソース]** を選択します。 リソースの一覧の **[CreateIntLBQS-rg]** リソース グループで、 **[myTestVM]** を選択します。

5. **[概要]** ページで、 **[接続]**  >  **[Bastion]** を選択します。

6. VM の作成時に入力したユーザー名とパスワードを入力します。

7. **myTestVM** で **Internet Explorer** を開きます。

8. 前の手順の IP アドレスをブラウザーのアドレス バーに入力します。 IIS Web サーバーの既定のページがブラウザーに表示されます。

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="ブラウザーのアドレス バーに表示される IP アドレスのスクリーンショット。" border="true":::
   
ロード バランサーが 3 つの VM すべてにトラフィックを分散していることを確認するために、各 VM の IIS Web サーバーの既定のページをカスタマイズできます。 次に、クライアント マシンで Web ブラウザーを手動で最新の情報に更新します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソースが不要になったら、[az group delete](/cli/azure/group#az-group-delete) コマンドを使用して、リソース グループ、ロード バランサー、およびすべての関連リソースを削除します。

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
```

## <a name="next-steps"></a>次のステップ

Azure Load Balancer の概要をご覧ください。
> [!div class="nextstepaction"]
> [Azure Load Balancer の概要](load-balancer-overview.md)
