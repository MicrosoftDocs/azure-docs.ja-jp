---
title: Azure PowerShell を使用して近接配置グループを作成する
description: Azure PowerShell を使用した近接配置グループを作成して使用する方法について説明します。
services: virtual-machines
ms.service: virtual-machines
ms.subservice: proximity-placement-groups
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 3/8/2021
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 26921b3d102032cb36f47c3be7a79c2b596a1d0c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102503676"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-powershell"></a>Azure PowerShell を使用して近接通信配置グループに VM をデプロイする


各 VM をできるだけ近くに配置して、可能性のある最も短い待ち時間を実現するには、それらを[近接通信配置グループ](../co-location.md#proximity-placement-groups)内にデプロイするようにしてください。

近接通信配置グループは、Azure コンピューティング リソースが互いに物理的に近くに配置されるようにするために使用される論理的なグループ化です。 近接通信配置グループは、短い待ち時間が要件であるワークロードに役立ちます。


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


## <a name="create-a-vm"></a>VM の作成

[New-AzVM](/powershell/module/az.compute/new-azvm) を使用して VM を作成するときに近接通信配置グループ ID を参照する目的で、`-ProximityPlacementGroup $ppg.Id` を使用し、近接通信配置グループで VM を作成します。

```azurepowershell-interactive
$vmName = "myVM"

New-AzVm `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -OpenPorts 3389 `
  -ProximityPlacementGroup $ppg.Id
```

[Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) を使用し、近接通信配置グループの VM を表示できます。

```azurepowershell-interactive
Get-AzProximityPlacementGroup -ResourceId $ppg.Id |
    Format-Table -Property VirtualMachines -Wrap
```

### <a name="move-an-existing-vm-into-a-proximity-placement-group"></a>既存の VM を近接通信配置グループ内に移動する

既存の VM を近接通信配置グループに追加することもできます。 最初に VM を停止して割り当てを解除し、その後で VM を更新して再起動する必要があります。

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```

### <a name="move-an-existing-vm-out-of-a-proximity-placement-group"></a>既存の VM を近接通信配置グループ外に移動する

近接通信配置グループから VM を削除するには、まず VM を停止して割り当てを解除し、その後で VM を更新して再起動する必要があります。

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
$vm.ProximityPlacementGroup = ""
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName 
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```


## <a name="availability-sets"></a>可用性セット
近接通信配置グループ内に可用性セットを作成することもできます。 同じ `-ProximityPlacementGroup` パラメーターを [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) コマンドレットで使用すると、可用性セットが作成されます。可用性セットで作成された VM はすべて、同じ近接通信配置グループでも作成されます。

近接通信配置グループに既存の可用性セットを追加または削除するには、最初に可用性セット内のすべての VM を停止する必要があります。 

### <a name="move-an-existing-availability-set-into-a-proximity-placement-group"></a>既存の可用性セットを近接通信配置グループ内に移動する

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
Update-AzAvailabilitySet -AvailabilitySet $avSet -ProximityPlacementGroupId $ppg.Id
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

### <a name="move-an-existing-availability-set-out-of-a-proximity-placement-group"></a>既存の可用性セットを近接通信配置グループ外に移動する

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$avSet.ProximityPlacementGroup = ""
Update-AzAvailabilitySet -AvailabilitySet $avSet 
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

## <a name="scale-sets"></a>スケール セット

近接通信配置グループ内にスケール セットを作成することもできます。 同じ `-ProximityPlacementGroup` パラメーターを [New-AzVmss](/powershell/module/az.compute/new-azvmss) で使用すると、スケール セットが作成されます。すべてのインスタンスが同じ近接通信配置グループで作成されます。


近接通信配置グループに既存のスケール セットを追加または削除するには、最初にスケール セットを停止する必要があります。 

### <a name="move-an-existing-scale-set-into-a-proximity-placement-group"></a>既存のスケール セットを近接通信配置グループ内に移動する

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
Update-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

### <a name="move-an-existing-scale-set-out-of-a-proximity-placement-group"></a>既存のスケール セットを近接通信配置グループ外に移動する

```azurepowershell-interactive
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
$vmss.ProximityPlacementGroup = ""
Update-AzVmss -VirtualMachineScaleSet $vmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName  
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

## <a name="next-steps"></a>次のステップ

[Azure CLI](../linux/proximity-placement-groups.md) を使用して近接通信配置グループを作成することもできます。