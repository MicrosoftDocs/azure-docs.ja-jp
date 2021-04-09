---
title: 既存の Azure Load Balancer で仮想マシン スケール セットを構成する - Azure PowerShell
description: 既存の Azure Load Balancer で仮想マシン スケール セットを構成する方法について説明します。
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/26/2020
ms.openlocfilehash: 919883025075ca38bcef6c23ac744528e7bd5502
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88510064"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-azure-powershell"></a>Azure PowerShell を使用して、既存の Azure Load Balancer で仮想マシン スケール セットを構成する

この記事では、既存の Azure Load Balancer で仮想マシン スケール セットを構成する方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。
- 仮想マシン スケール セットがデプロイされるサブスクリプション内の、既存の標準 SKU ロード バランサー。
- 仮想マシン スケール セット用の Azure Virtual Network。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure-cli"></a>Azure CLI へのサインイン

Azure にサインインします。

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>既存のロード バランサーで仮想マシン スケール セットをデプロイする

かっこ内の値を、構成内のリソースの名前に置き換えます。

```azurepowershell-interactive

$rsg = <resource-group>
$loc = <location>
$vms = <vm-scale-set-name>
$vnt = <virtual-network>
$sub = <subnet-name>
$lbn = <load-balancer-name>
$pol = <upgrade-policy-mode>

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol

```

以下の例では、仮想マシン スケール セットを次の情報でデプロイします。

- 仮想マシン スケール セットの名前 **myVMSS**
- Azure Load Balancer の名前 **myLoadBalancer**
- ロード バランサー のバックエンド プールの名前 **myBackendPool**
- Azure Virtual Network の名前 **myVnet**
- サブネットの名前 **mySubnet**
- リソースグループの名前 **myResourceGroup**

```azurepowershell-interactive

$rsg = "myResourceGroup"
$loc = "East US 2"
$vms = "myVMSS"
$vnt = "myVnet"
$sub = "mySubnet"
$pol = "Automatic"
$lbn = "myLoadBalancer"

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol
```
> [!NOTE]
> スケール セットが作成された後、ロード バランサーの正常性プローブに使用される負荷分散ルールのバックエンド ポートを変更することはできません。 ポートを変更するには、Azure 仮想マシン スケール セットを更新し、ポートを更新してから正常性プローブを再度構成することで、正常性プローブを削除できます。

## <a name="next-steps"></a>次のステップ

この記事では、既存の Azure Load Balancer で仮想マシン スケール セットをデプロイしました。  仮想マシン スケール セットとロードバランサーの詳細については、以下を参照してください。

- [Azure Load Balancer の概要](load-balancer-overview.md)
- [仮想マシン スケール セットとは](../virtual-machine-scale-sets/overview.md)
