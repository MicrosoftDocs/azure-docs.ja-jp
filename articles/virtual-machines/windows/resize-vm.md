---
title: Azure で Windows VM のサイズを変更する
description: Azure 仮想マシンに使用する VM のサイズを変更します。
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 01/13/2020
ms.author: cynthn
ms.openlocfilehash: f7f41c0fb2bf949dd47491761fde8025a2d35155
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560652"
---
# <a name="resize-a-windows-vm"></a>Windows VM のサイズ変更

この記事では、VM を別の [VM サイズ](../sizes.md)に移行する方法について説明します。

仮想マシン (VM) を作成した後、VM のサイズを変更することで、VM をスケールアップまたはスケールダウンできます。 場合によっては、先に VM の割り当てを解除する必要があります。 これは、現在 VM をホストしているハードウェア クラスターで新しいサイズを使用できない場合に発生する可能性があります。

VM が Premium Storage を使用している場合、Premium Storage のサポートを受けるには、**s** バージョンのサイズを選択する必要があります。 たとえば、Standard_E4_v3 ではなく Standard_E4 **s** _v3 を選択します。

## <a name="use-the-portal"></a>ポータルの使用

1. [Azure Portal](https://portal.azure.com)を開きます。
1. 仮想マシンのページを開きます。
1. 左側のメニューで **[サイズ]** を選択します。
1. 利用可能なサイズの一覧から新しいサイズを選択し、 **[サイズ変更]** をクリックします。


仮想マシンが現在実行されている場合、そのサイズを変更すると、再起動されます。 仮想マシンを停止すると、追加のサイズが表示される場合があります。

## <a name="use-powershell-to-resize-a-vm-not-in-an-availability-set"></a>PowerShell を使用して可用性セット内にない VM のサイズを変更する

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

## <a name="use-powershell-to-resize-a-vm-in-an-availability-set"></a>PowerShell を使用して可用性セット内の VM のサイズを変更する

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
$availabilitySetName = "<availabilitySetName>"
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $availabilitySetName
$virtualMachines = $as.VirtualMachinesReferences |  Get-AzResource | Get-AzVM
$virtualMachines |  Stop-AzVM -Force -NoWait  
```

可用性セット内の VM のサイズを変更して、再起動します。
   
```powershell
$availabilitySetName = "<availabilitySetName>"
$newSize = "<newVmSize>"
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $availabilitySetName
$virtualMachines = $as.VirtualMachinesReferences |  Get-AzResource | Get-AzVM
$virtualMachines | Foreach-Object { $_.HardwareProfile.VmSize = $newSize }
$virtualMachines | Update-AzVM
$virtualMachines | Start-AzVM
```

## <a name="next-steps"></a>次のステップ

スケーラビリティを高めるには、複数の VM インスタンスを実行してスケール アウトします。詳細については、[仮想マシン スケール セットでのマシンの自動スケール](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md)に関するページを参照してください。