---
title: パブリック IP の作成 - Azure CLI
description: Azure CLI を使用してパブリック IP を作成する方法について説明します
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: 2c469324db11d2e65f8eb958e68f77fd77020865
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99491049"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-cli"></a>クイック スタート:Azure CLI を使用してパブリック IP アドレスを作成する

この記事では、Azure CLI を使用してパブリック IP アドレス リソースを作成する方法について説明します。 これを関連付けることができるリソース、Basic SKU と Standard SKU の違い、およびその他の関連情報については、「[パブリック IP アドレス](./public-ip-addresses.md)」を参照してください。  この例では、IPv4 アドレスのみに焦点を当てます。IPv6 アドレスの詳細については、[Azure VNet の IPv6](./ipv6-overview.md) に関するページを参照してください。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0.28 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[az group create](/cli/azure/group#az-group-create) を使用して、**myResourceGroup** という名前のリソース グループを場所 **eastus2** に作成します。

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-public-ip"></a>パブリック IP を作成する

---
# <a name="standard-sku---using-zones"></a>[**Standard SKU - ゾーンの使用**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>次のコマンドは、API バージョン 2020-08-01 以降で動作します。  現在使用されている API バージョンの詳細については、[リソース プロバイダーと種類](../azure-resource-manager/management/resource-providers-and-types.md)に関するページを参照してください。

**myResourceGroup** に **myStandardZRPublicIP** という Standard ゾーン冗長パブリック IP アドレスを作成するには、[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) を使用します。

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardZRPublicIP \
    --sku Standard
    --zone 1 2 3
```
> [!IMPORTANT]
> 2020-08-01 より前の API のバージョンでは、ゾーン冗長 IP アドレスを作成する際、ゾーン パラメーターを指定せずに上記のコマンドを実行します。 
>

**myResourceGroup** で **myStandardZonalPublicIP** という名前の Standard ゾーン ベースのパブリック IP アドレスをゾーン 2 に作成するには、次のコマンドを使用します。

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myStandardZonalPublicIP \
    --sku Standard \
    --zone 2
```

上記のゾーンのオプションは、[Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) が利用できるリージョンでのみ有効な選択であることに注意してください。

# <a name="standard-sku---no-zones"></a>[**Standard SKU - ゾーンなし**](#tab/option-create-public-ip-standard)

>[!NOTE]
>次のコマンドは、API バージョン 2020-08-01 以降で動作します。  現在使用されている API バージョンの詳細については、[リソース プロバイダーと種類](../azure-resource-manager/management/resource-providers-and-types.md)に関するページを参照してください。

[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) を使用して、**myResourceGroup** に **myStandardPublicIP** という名前の Standard パブリック IP アドレスを非ゾーン リソースとして作成します。

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardPublicIP \
    --sku Standard
```
この選択はすべてのリージョンで有効であり、[Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) が利用できないリージョンでの Standard パブリック IP アドレスの既定の選択です。

# <a name="basic-sku"></a>[**Basic SKU**](#tab/option-create-public-ip-basic)

**myResourceGroup** に **myBasicPublicIP** という Basic パブリック IP アドレスを作成するには、[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) を使用します。  Basic パブリック IP には、可用性ゾーンという概念はありません。

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myBasicPublicIP \
    --sku Basic \
    --allocation-method Static
```
時間の経過と共に IP アドレスが変更されても問題ない場合は、allocation-method を "Dynamic" に変更することで **動的** IP の割り当てを選択できます。

---

## <a name="additional-information"></a>関連情報 

上に示した個々の変数の詳細については、[パブリック IP アドレスの管理](./virtual-network-public-ip-address.md#create-a-public-ip-address)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
- [仮想マシンにパブリック IP アドレス](./associate-public-ip-address-vm.md#azure-portal)を関連付ける。
- Azure の[パブリック IP アドレス](./public-ip-addresses.md#public-ip-addresses)について詳しく学習する。
- すべての[パブリック IP アドレスの設定](virtual-network-public-ip-address.md#create-a-public-ip-address)について詳しく学習する。
