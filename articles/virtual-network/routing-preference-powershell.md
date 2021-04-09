---
title: パブリック IP アドレスのルーティング優先設定を構成する - Azure PowerShell
titlesuffix: Azure Virtual Network
description: Azure PowerShell を使用してパブリック IP アドレスのルーティング優先設定を構成する方法を説明します。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: 5fac791247dbb785e087ac21f3fd5532064c6c8a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101667884"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-azure-powershell"></a>Azure PowerShell を使用してパブリック IP アドレスのルーティング優先設定を構成する

この記事では、Azure PowerShell を使用して、パブリック IP アドレスに対して ISP ネットワーク ( **[インターネット]** オプション) によるルーティング優先設定を構成する方法について説明します。 パブリック IP アドレスを作成した後は、インターネットでの受信および送信トラフィック用に、次の Azure リソースと関連付けることができます。

* 仮想マシン
* 仮想マシン スケール セット
* Azure Kubernetes Service (AKS)
* インターネットに接続するロード バランサー
* Application Gateway
* Azure Firewall

既定では、パブリック IP アドレスに対するルーティング優先設定は、すべての Azure サービスについて Microsoft グローバル ネットワークに設定され、任意の Azure サービスと関連付けることができます。

Azure サブスクリプションをお持ちでない場合は、ここで[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
PowerShell をローカルにインストールして使用することを選択する場合、この記事では Azure PowerShell モジュール バージョン 6.9.0 以降が必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="create-a-resource-group"></a>リソース グループを作成する

[New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup) を使用して Azure リソース グループを作成します。 この例では、*eastus* の場所内に *myResourceGroup* という名前のリソース グループを作成します。

```azurepowershell
$rg = New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-public-ip-with-internet-routing-preference"></a>インターネット ルーティング優先設定を使用してパブリック IP アドレスを作成する

次のコマンドを実行すると、"*米国東部*" Azure リージョンにルーティング優先設定の種類が "*インターネット*" の新しいパブリック IP アドレスが作成されます。

```azurepowershell
$iptagtype="RoutingPreference"
$tagName = "Internet"
$ipTag = New-AzPublicIpTag -IpTagType $iptagtype -Tag $tagName 
# attach the tag
$publicIp = New-AzPublicIpAddress  `
-Name "MyPublicIP" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location `
-IpTag $ipTag `
-AllocationMethod Static `
-Sku Standard `
-IpAddressVersion IPv4
```

上で作成したパブリック IP アドレスを、[Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の仮想マシンに関連付けることができます。 チュートリアル ページ「[仮想マシンへのパブリック IP アドレスの関連付け](associate-public-ip-address-vm.md#azure-cli)」の CLI セクションを使用して、パブリック IP アドレスを VM に関連付けます。 上で作成したパブリック IP アドレスを、ロード バランサーの **フロントエンド** 構成に割り当てることで、[Azure Load Balancer](../load-balancer/load-balancer-overview.md) と関連付けることもできます。 このパブリック IP アドレスは、負荷分散された仮想 IP アドレス (VIP) として機能します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドを使用して、リソース グループ、VM、およびすべての関連リソースを削除できます。

```azurepowershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

- [パブリック IP アドレスでのルーティング優先設定](routing-preference-overview.md)について詳しく学習する。
- [Azure PowerShell を使用して VM 用にルーティング優先設定を構成する](configure-routing-preference-virtual-machine-powershell.md)。
