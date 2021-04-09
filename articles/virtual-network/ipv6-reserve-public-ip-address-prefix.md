---
title: Azure 仮想ネットワーク内でパブリック IPv6 アドレスとアドレス範囲を予約する
titlesuffix: Azure Virtual Network
description: Azure 仮想ネットワーク内でパブリック IPv6 アドレスとアドレス範囲を予約する方法について説明します。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: ac79e1eb5c4f7448dc17804cd8aac3cba582497e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88509945"
---
# <a name="reserve-public-ipv6-address-prefix"></a>パブリック IPv6 アドレスのプレフィックスを予約する
Azure Virtual Network (VNet) の IPv6 により、仮想ネットワークやインターネットで IPv6 接続や IPv4 接続を使用するアプリケーションを Azure 内でホストできます。 個々の IPv6 アドレスを予約することに加え、Azure IPv6 アドレスの連続する範囲 (IP プレフィックスと呼ばれます) を予約して使用することもできます。 この記事では、Azure PowerShell と CLI を使用して IPv6 パブリック IP アドレスおよびアドレス範囲を作成する方法について説明します。


## <a name="create-a-single-reserved-ipv6-public-ip"></a>単一の予約された IPv6 パブリック IP を作成する

### <a name="using-azure-powershell"></a>Azure PowerShell の使用

次のように、Azure PowerShell で [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) を使用して、単一の予約された (静的) IPv6 パブリック IP アドレスを作成することができます。

```azurepowershell
 $myOwnIPv6Address = New-AzPublicIpAddress `
 -name PIPv6_WestUS `
 -ResourceGroup MyRG `
 -Location "West US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6
 ```

### <a name="using-azure-cli"></a>Azure CLI の使用

 次のように、Azure CLI で [az network public-ip create](/cli/azure/network/public-ip) を使用して、単一の予約された (静的) IPv6 パブリック IP アドレスを作成することができます。

```azurecli
 az network public-ip create \
 --name dsPublicIP_v6 \
 --resource-group UpgradeInPlace_CLI_RG1 \
 --location WestUS \
 --sku Standard  \
 --allocation-method static  \
 --version IPv6
```

## <a name="create-a-reserved-ipv6-prefix-range"></a>予約された IPv6 プレフィックス (範囲) を作成する

IPv6 プレフィックスを予約するには、IPv4 プレフィックスの作成に使用したのと同じコマンドに IPv6 の IP アドレス ファミリを追加します。 次のコマンドは、サイズ/125 (8 個の IPv6 アドレス) のプレフィックスが作成されます。

### <a name="using-azure-powershell"></a>Azure PowerShell の使用

次のように、Azure CLI で [az network public-ip create](/powershell/module/az.network/new-azpublicipprefix) を使用して、パブリック IPv6 アドレスを作成することができます。
```azurepowershell
 $myOwnIPv6Prefix = New-AzPublicIpPrefix `
 -name IPv6PrefixWestUS `
 -ResourceGroupName MyRG `
 -Location "West US" `
 -Sku Standard `
 -IpAddressVersion IPv6 `
 -PrefixLength 125
```

### <a name="using-azure-cli"></a>Azure CLI の使用

次に示すように、Azure CLI を使用してパブリック IPv6 アドレスを作成することができます。

```azurecli
az network public-ip prefix create \
--name IPv6PrefixWestUS \
--resource-group MyRG \
--location WestUS \
--version IPv6 \
--length 125
```

## <a name="allocate-a-public-ip-address-from-a-reserved-ipv6-prefix"></a>予約された IPv6 プレフィックスからパブリック IP アドレスを割り当てる

### <a name="using-azure-powershell"></a>Azure PowerShell の使用

 予約されたプレフィックスから静的 IPv6 パブリック IP を作成するには、Azure PowerShell を使用してパブリック IP を作成するときに引数 `-PublicIpPrefix` を追加します。 次の例は、プレフィックスが作成され、 *$myOwnIPv6Prefix* という名前の PowerShell 変数に格納されていることを前提としています。

```azurepowershell
 $MyIPv6PublicIPFromMyReservedPrefix = New-AzPublicIpAddress \
 -name PIPv6_fromPrefix `
 -ResourceGroup DsStdLb04 `
 -Location "West Central US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6 `
 -PublicIpPrefix $myOwnIPv6Prefix
```

### <a name="using-azure-cli"></a>Azure CLI の使用

次の例は、プレフィックスが作成され、次の名前の CLI 変数に格納されていることを前提としています。*IPv6PrefixWestUS*。

```azurecli
az network public-ip create \
--name dsPublicIP_v6 \
--resource-group UpgradeInPlace_CLI_RG1 \
--location WestUS \
--sku Standard \
--allocation-method static \
--version IPv6 \
--public-ip-prefix  IPv6PrefixWestUS
```

## <a name="next-steps"></a>次のステップ
- [IPv6 アドレスのプレフィックス](ipv6-public-ip-address-prefix.md)の詳細について学習する。
- [IPv6 アドレス](ipv6-overview.md)の詳細について学習します。
