---
title: 仮想マシン スケール セットの近接配置グループのプレビュー
description: Azure の Windows 仮想マシンに近接通信配置グループを作成して使用する方法について説明します。
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 07/01/2019
ms.reviewer: zivr
ms.custom: mimckitt
ms.openlocfilehash: a3a7b50c82b3deffa7526bfcfe799972c3b02762
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98877868"
---
# <a name="preview-creating-and-using-proximity-placement-groups-using-powershell"></a>プレビュー:PowerShell を使用した近接配置グループの作成と使用

VM を可能な限り近くに置き、待ち時間を可能な限り短くするためには、[近接通信配置グループ](../virtual-machines/co-location.md#proximity-placement-groups)内にスケール セットをデプロイする必要があります。

近接通信配置グループは、Azure コンピューティング リソースが互いに物理的に近くに配置されるようにするために使用される論理的なグループ化です。 近接通信配置グループは、短い待ち時間が要件であるワークロードに役立ちます。

> [!IMPORTANT]
> 近接通信配置グループは現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。
>
> 近接通信配置グループは、プレビュー期間中、**東日本**、**オーストラリア東部**、**インド中部** の各リージョンでは使用できません。


## <a name="create-a-proximity-placement-group"></a>近接通信配置グループの作成
[New-AzProximityPlacementGroup](/powershell/module/az.compute/new-azproximityplacementgroup) コマンドレットを使用し、近接通信配置グループを作成します。 

```azurepowershell-interactive
$resourceGroup = "myPPGResourceGroup"
$location = "East US"
$ppgName = "myPPG"
New-AzResourceGroup -Name $resourceGroup -Location $location
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard
```

## <a name="list-proximity-placement-groups"></a>近接通信配置グループを一覧表示する

[Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) コマンドレットを使用し、近接通信配置グループをすべて一覧表示できます。

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-scale-set"></a>スケール セットを作成する

[New-AzVMSS](/powershell/module/az.compute/new-azvmss) を使用してスケール セットを作成するときに近接通信配置グループ ID を参照する目的で、`-ProximityPlacementGroup $ppg.Id` を使用し、近接通信配置グループでスケールを作成します。

```azurepowershell-interactive
$scalesetName = "myVM"

New-AzVmss `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -VMScaleSetName $scalesetName `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ProximityPlacementGroup $ppg.Id
```

[Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) を使用して、近接通信配置グループ内のインスタンスを表示できます。

```azurepowershell-interactive
  Get-AzProximityPlacementGroup `
   -ResourceId $ppg.Id | Format-Table `
   -Wrap `
   -Property VirtualMachineScaleSets
```

## <a name="next-steps"></a>次のステップ

[Azure CLI](../virtual-machines/linux/proximity-placement-groups.md) を使用して近接通信配置グループを作成することもできます。
