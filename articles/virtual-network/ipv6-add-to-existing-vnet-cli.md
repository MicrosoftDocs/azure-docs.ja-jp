---
title: Azure 仮想ネットワーク内の IPv4 アプリケーションに IPv6 を追加する - Azure CLI
titlesuffix: Azure Virtual Network
description: この記事では、Azure CLI を使用して Azure 仮想ネットワーク内の既存のアプリケーションに IPv6 アドレスをデプロイする方法について示します。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 0ba8e34c1fb219d86086e73203acf65c2351c340
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708350"
---
# <a name="add-ipv6-to-an-ipv4-application-in-azure-virtual-network---azure-cli"></a>Azure 仮想ネットワーク内の IPv4 アプリケーションに IPv6 を追加する - Azure CLI

この記事では、Standard Load Balancer に対して Azure 仮想ネットワーク内の IPv4 パブリック IP アドレスを使用しているアプリケーションに、Azure CLI を使用して IPv6 アドレスを追加する方法を示します。 インプレース アップグレードには、仮想ネットワークとサブネット、IPv4 + IPV6 フロントエンド構成の Standard Load Balancer、IPv4 + IPv6 構成の NIC を含む VM、ネットワーク セキュリティ グループ、パブリック IP が含まれます。


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

代わりに Azure CLI をローカルにインストールして使用する場合は、このクイック スタートには Azure CLI バージョン 2.0.28 以降を使用する必要があります。 インストールされているバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードについては、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」をご覧ください。

## <a name="prerequisites"></a>前提条件

この記事では、[クイック スタート: Standard Load Balancer を作成する - Azure CLI](../load-balancer/quickstart-load-balancer-standard-public-cli.md)」の説明に従って Standard Load Balancer をデプロイ済みであることを前提としています。

## <a name="create-ipv6-addresses"></a>IPv6 アドレスを作成する

Standard Load Balancer に対して [az network public-ip create](/cli/azure/network/public-ip) を使用してパブリック IPv6 アドレスを作成します。 次の例では、*PublicIP_v6* という名前の IPv6 パブリック IP アドレスを *myResourceGroupSLB* リソース グループに作成します。

```azurecli
  
az network public-ip create \
--name PublicIP_v6 \
--resource-group MyResourceGroupSLB \
--location EastUS \
--sku Standard \
--allocation-method static \
--version IPv6
```

## <a name="configure-ipv6-load-balancer-frontend"></a>IPv6 ロード バランサー フロントエンドを構成する

次のように [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create) を使用して、新しい IPv6 IP アドレスが割り当てられたロード バランサーを構成します。

```azurecli
az network lb frontend-ip create \
--lb-name myLoadBalancer \
--name dsLbFrontEnd_v6 \
--resource-group MyResourceGroupSLB \
--public-ip-address PublicIP_v6
```

## <a name="configure-ipv6-load-balancer-backend-pool"></a>IPv6 ロード バランサー バックエンド プールを構成する

次のように、[az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create) を使用して、IPv6 アドレスが割り当てられた NIC のバックエンド プールを作成します。

```azurecli
az network lb address-pool create \
--lb-name myLoadBalancer \
--name dsLbBackEndPool_v6 \
--resource-group MyResourceGroupSLB
```

## <a name="configure-ipv6-load-balancer-rules"></a>IPv6 ロード バランサー規則を構成する

[az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create) を使用して IPv6 ロード バランサー規則を作成します。

```azurecli
az network lb rule create \
--lb-name myLoadBalancer \
--name dsLBrule_v6 \
--resource-group MyResourceGroupSLB \
--frontend-ip-name dsLbFrontEnd_v6 \
--protocol Tcp \
--frontend-port 80 \
--backend-port 80 \
--backend-pool-name dsLbBackEndPool_v6
```

## <a name="add-ipv6-address-ranges"></a>IPv6 アドレス範囲の追加

次のように、ロード バランサーをホストする仮想ネットワークとサブネットに IPv6 アドレス範囲を追加します。

```azurecli
az network vnet update \
--name myVnet  `
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/16"  "ace:cab:deca::/48"

az network vnet subnet update \
--vnet-name myVnet \
--name mySubnet \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/24"  "ace:cab:deca:deed::/64"  
```

## <a name="add-ipv6-configuration-to-nics"></a>NIC に IPv6 構成を追加する

次のように [az network nic ip-config create](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create) を使用して、IPv6 アドレスが割り当てられた VM NIC を構成します。

```azurecli
az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name myNicVM1 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC2 \
--nic-name myNicVM2 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer

az network nic ip-config create \
--name dsIp6Config_NIC3 \
--nic-name myNicVM3 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer

```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Azure portal で IPv6 デュアル スタック仮想ネットワークを表示する
次のようにして、Azure portal で IPv6 デュアル スタック仮想ネットワークを表示することができます。
1. ポータルの検索バーに、「*myVnet*」と入力します。
2. 検索結果に **myVnet** が表示されたら、それを選択します。 これにより、*myVNet* という名前のデュアル スタック仮想ネットワークの **[概要]** ページが起動します。 デュアル スタック仮想ネットワークには、*mySubnet* という名前のデュアル スタック サブネットにある、IPv4 と IPv6 の両方の構成を持つ 3 つの NIC が表示されます。

  ![Azure の IPv6 デュアル スタック仮想ネットワーク](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)


## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドを使用して、リソース グループ、VM、およびすべての関連リソースを削除できます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>次のステップ

この記事では、IPv4 フロントエンド IP 構成を持つ既存の Standard Load Balancer をデュアル スタック (IPv4 および IPv6) 構成に更新しました。 また、バックエンド プール内の VM の NIC に IPv6 構成も追加しました。 Azure 仮想ネットワークでの IPv6 サポートの詳細については、[Azure Virtual Network の IPv6 の概要](ipv6-overview.md)に関するページを参照してください
