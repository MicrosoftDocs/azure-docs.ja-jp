---
title: PowerShell を使って Azure 内の Windows VM のサイズを変更する | Microsoft Docs
description: Resource Manager デプロイ モデルで作成された Windows 仮想マシンのサイズを Azure Powershell を使用して変更します。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 057ff274-6dad-415e-891c-58f8eea9ed78
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: cynthn
ms.openlocfilehash: f54ff738199d433308a8eaba6a643861c57b4abb
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58540688"
---
# <a name="resize-a-windows-vm"></a>Windows VM のサイズ変更

この記事では、Azure Powershell を使用して VM を別の [VM サイズ](sizes.md)に移行する方法について説明します。

仮想マシン (VM) を作成した後、VM のサイズを変更することで、VM をスケールアップまたはスケールダウンできます。 場合によっては、先に VM の割り当てを解除する必要があります。 これは、現在 VM をホストしているハードウェア クラスターで新しいサイズを使用できない場合に発生する可能性があります。

VM が Premium Storage を使用している場合、Premium Storage のサポートを受けるには、**s** バージョンのサイズを選択する必要があります。 たとえば、Standard_E4_v3 ではなく Standard_E4**s**_v3 を選択します。

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>可用性セット内にない Windows VM のサイズ変更

変数をいくつか設定します。 値は実際の値に変更してください。

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

VM がホストされているハードウェア クラスターで使用できる VM のサイズを一覧表示します。 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

目的のサイズが表示される場合は、次のコマンドを実行して VM のサイズを変更します。 目的のサイズが表示されない場合は、手順 3. に進みます。
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

目的のサイズが表示されない場合は、次のコマンドを実行して VM の割り当てを解除します。その後、VM のサイズを変更して、再起動します。 **\<newVMsize>** を目的のサイズに置き換えます。
   
```powershell
Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> VM の割り当てを解除すると、VM に割り当てられているすべての動的 IP アドレスが解放されます。 OS とデータ ディスクには影響しません。 
> 
> 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>可用性セット内の Windows VM のサイズ変更

現在 VM をホストしているハードウェア クラスターで可用性セット内の VM の新しいサイズを使用できない場合、可用性セット内のすべての VM の割り当てを解除して、VM のサイズを変更する必要があります。 また、1 つの VM のサイズを変更した後、可用性セット内の他の VM のサイズを更新する必要がある場合もあります。 可用性セット内の VM のサイズを変更するには、次の手順を実行します。

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

VM がホストされているハードウェア クラスターで使用できる VM のサイズを一覧表示します。 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

目的のサイズが表示される場合は、次のコマンドを実行して VM のサイズを変更します。 表示されない場合は、次のセクションに進みます。
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```
    
目的のサイズが表示されない場合は、次の手順を続行して、可用性セット内のすべての VM の割り当てを解除し、VM のサイズを変更してから再起動します。

可用性セット内のすべての VM を停止します。
   
```powershell
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 
```

可用性セット内の VM のサイズを変更して、再起動します。
   
```powershell
$newSize = "<newVmSize>"
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
  foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    $vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
    $vm.HardwareProfile.VmSize = $newSize
    Update-AzVM -ResourceGroupName $resourceGroup -VM $vm
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
    }
```

## <a name="next-steps"></a>次の手順

スケーラビリティを高めるには、複数の VM インスタンスを実行してスケール アウトします。詳細については、[仮想マシン スケール セットでのマシンの自動スケール](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md)に関するページを参照してください。

