---
title: パブリック IP を作成する - Azure PowerShell
description: Azure PowerShell を使用してパブリック IP を作成する方法について説明します
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
ms.openlocfilehash: f81e76e32bc0a50b945c54cdfcac0575540484bc
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102502435"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-powershell"></a>クイック スタート:Azure PowerShell を使用してパブリック IP アドレスを作成する

この記事では、Azure PowerShell を使用してパブリック IP アドレス リソースを作成する方法について説明します。 これを関連付けることができるリソース、Basic SKU と Standard SKU の違い、およびその他の関連情報については、「[パブリック IP アドレス](./public-ip-addresses.md)」を参照してください。  この例では、IPv4 アドレスのみに焦点を当てます。IPv6 アドレスの詳細については、[Azure VNet の IPv6](./ipv6-overview.md) に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

- ローカルにインストールされた Azure PowerShell または Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell をインストールしてローカルで使用する場合、この記事では Azure PowerShell モジュール バージョン 5.4.1 以降が必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用して **myResourceGroup** という名前のリソース グループを **eastus2** に作成します。

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'

New-AzResourceGroup -Name $rg -Location $loc
```
## <a name="create-public-ip"></a>パブリック IP を作成する

---
# <a name="standard-sku---using-zones"></a>[**Standard SKU - ゾーンの使用**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>次のコマンドは、Az.Network モジュール バージョン 4.5.0 以降で動作します。  現在使用されている Powershell モジュールの詳細については、[PowerShellGet のドキュメント](/powershell/module/powershellget/)を参照してください。

[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) を使用して、**myResourceGroup** に **myStandardZRPublicIP** という名前の Standard ゾーン冗長パブリック IP アドレスを作成します。

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardZRPublicIP'
$sku = 'Standard'
$alloc = 'Static'
$zone = 1,2,3

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku -zone $zone
```
> [!IMPORTANT]
> 4\.5.0 より前の Az.Network モジュールに対しては、ゾーン パラメーターを指定せずに上記のコマンドを実行して、ゾーン冗長 IP アドレスを作成します。 
>

**myResourceGroup** で **myStandardZonalPublicIP** という名前の Standard ゾーン ベースのパブリック IP アドレスをゾーン 2 に作成するには、次のコマンドを使用します。

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardZonalPublicIP'
$sku = 'Standard'
$alloc = 'Static'
$zone = 2

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku -zone $zone
```

上記のゾーンのオプションは、[Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) が利用できるリージョンでのみ有効な選択であることに注意してください。

# <a name="standard-sku---no-zones"></a>[**Standard SKU - ゾーンなし**](#tab/option-create-public-ip-standard)

>[!NOTE]
>次のコマンドは、Az.Network モジュール バージョン 4.5.0 以降で動作します。  現在使用されている Powershell モジュールの詳細については、[PowerShellGet のドキュメント](/powershell/module/powershellget/)を参照してください。

[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) を使用して、**myResourceGroup** に **myStandardPublicIP** という名前の Standard パブリック IP アドレスを非ゾーン リソースとして作成します。

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardPublicIP'
$sku = 'Standard'
$alloc = 'Static'

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku
```

この選択はすべてのリージョンで有効であり、[Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) が利用できないリージョンでの Standard パブリック IP アドレスの既定の選択です。

# <a name="basic-sku"></a>[**Basic SKU**](#tab/option-create-public-ip-basic)

[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) を使用して、**myResourceGroup** に **myBasicPublicIP** という名前の Basic 静的パブリック IP アドレスを作成します。  Basic パブリック IP には、可用性ゾーンという概念はありません。

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myBasicPublicIP'
$sku = 'Basic'
$alloc = 'Static'

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku
```
時間の経過と共に IP アドレスが変更されても問題ない場合は、AllocationMethod を "Dynamic" に変更することで **動的** IP の割り当てを選択できます。

---

## <a name="additional-information"></a>関連情報 

上に示した個々の変数の詳細については、[パブリック IP アドレスの管理](./virtual-network-public-ip-address.md#create-a-public-ip-address)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
- [仮想マシンにパブリック IP アドレス](./associate-public-ip-address-vm.md#azure-portal)を関連付ける
- Azure の[パブリック IP アドレス](./public-ip-addresses.md#public-ip-addresses)について詳しく学習する。
- すべての[パブリック IP アドレスの設定](virtual-network-public-ip-address.md#create-a-public-ip-address)について詳しく学習する。