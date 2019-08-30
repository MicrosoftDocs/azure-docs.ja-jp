---
title: PowerShell を使用して Azure VM の OS ディスクを交換する | Microsoft Docs
description: PowerShell を使用して Azure 仮想マシンで使用されるオペレーティング システムのディスクを変更します。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: b213db38dade06e5015227494fa77b2f465ba1d9
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70089057"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>PowerShell を使用して Azure VM で使用される OS ディスクを変更する

既存の VM があり、そのディスクをバックアップ ディスクまたは別の OS ディスクに交換する場合、Azure PowerShell を使用して OS ディスクを交換できます。 VM を削除して再作成する必要はありません。 別のリソース グループ内のマネージド ディスクでも、まだ使用されていない場合に限り使用することができます。

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

この場合は、VM を停止するか、割り当てを解除する必要があります。その後は、マネージド ディスクのリソース ID を別のマネージド ディスクのリソース ID に置き換えることができます。

VM のサイズとストレージの種類が、接続するディスクと互換性があることを確認します。 たとえば、使用するディスクが Premium Storage 内にある場合、VM は Premium Storage (DS シリーズのサイズなど) に対応している必要があります。 

[Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk) を使用して、リソース グループ内のディスクの一覧を取得する

```azurepowershell-interactive
Get-AzDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
使用するディスクの名前がわかっている場合は、VM の OS ディスクとしてその名前を設定します。 この例では、*myVM* という名前の VM を停止するか割り当てを解除し、*newDisk* という名前のディスクを新しい OS ディスクとして割り当てます。 
 
```azurepowershell-interactive 
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name 

# Update the VM with the new OS disk
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm 

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup

```

**次のステップ**

ディスクのコピーを作成する方法については、「[スナップショットの作成](snapshot-copy-managed-disk.md)」を参照してください。