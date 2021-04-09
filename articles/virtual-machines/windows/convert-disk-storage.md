---
title: Azure PowerShell を使用して異なる種類のディスク間でマネージド ディスク ストレージを変換する
description: Azure PowerShell を使用して、異なる種類のディスク間で Azure マネージド ディスクを変換する方法。
author: roygara
ms.service: virtual-machines
ms.subservice: disks
ms.topic: how-to
ms.date: 02/13/2021
ms.author: albecker
ms.openlocfilehash: 658dfbb654920c0dd90c4b4caaac7a5ca5962c81
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104607298"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>マネージド ディスクのストレージの種類を更新する

Azure マネージド ディスクには、ディスクの種類が 4 つあります。Azure Ultra Disk、Premium SSD、Standard SSD、Standard HDD です。 パフォーマンスのニーズに合わせて、Premium SSD、Standard SSD、および Standard HDD を切り替えることができます。 まだ Ultra Disk との切り替えはできません。新しいディスクをデプロイする必要があります。

この機能は、アンマネージド ディスクではサポートされていません。 ただし、ディスクの種類を切り替えられるようにするために、簡単に[アンマネージド ディスクをマネージド ディスクに変換](convert-unmanaged-to-managed-disks.md)できます。



## <a name="before-you-begin"></a>始める前に

* 変換作業には仮想マシン (VM) の再起動が必要なので、既に設定されているメンテナンス期間中にディスク ストレージの移行をスケジュールするようにします。
* お使いのディスクがアンマネージド ディスクの場合、最初に[マネージド ディスクに変換する](convert-unmanaged-to-managed-disks.md)と、ストレージ オプションを切り替えることができます。

## <a name="switch-all-managed-disks-of-a-vm-between-from-one-account-to-another"></a>VM のすべてのマネージド ディスクをある特定のアカウントから別のものに切り替える

この例では、VM のすべてのディスクを Premium Storage に変換する方法を示します。 ただし、この例で $storageType 変数を変更することで、VM のディスクの種類を Standard SSD または Standard HDD に変換できます。 Premium マネージド ディスクを使用するには、Premium Storage に対応している [VM のサイズ](../sizes.md)を使用している必要があります。 この例は、Premium ストレージに対応するサイズへの切り替えも行います。

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from Standard to Premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to Premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
        $disk | Update-AzDisk
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Standard と Premium の間で個別のマネージド ディスクを切り替える

開発/テスト ワークロードでは、コストを削減するために Standard ディスクと Premium ディスクを混在させたい場合があります。 パフォーマンスを上げる必要があるディスクだけをアップグレードするように選択できます。 この例では、1 つの VM ディスクを Standard から Premium ストレージに変換する方法について説明します。 ただし、この例で $storageType 変数を変更することで、VM のディスクの種類を Standard SSD または Standard HDD に変換できます。 Premium マネージド ディスクを使用するには、Premium Storage に対応している [VM のサイズ](../sizes.md)を使用している必要があります。 この例では、Premium ストレージに対応するサイズに切り替える方法も示しています。

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="switch-managed-disks-from-one-disk-type-to-another"></a>マネージド ディスクをある特定のディスクの種類から別のものに切り替える

次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。
2. **仮想マシン** の一覧から VM を選択します。
3. VM が停止していない場合、VM の **[概要]** ウィンドウの一番上で **[停止]** を選択し、VM が停止するまで待ちます。
4. VM のウィンドウで、メニューから **[ディスク]** を選択します。
5. 変換するディスクを選択します。
6. メニューから **[構成]** を選択します。
7. **[アカウントの種類]** を、元のディスクの種類から目的のディスクの種類に変更します。
8. **[保存]** を選択し、ディスク ウィンドウを閉じます。

ディスクの種類の変換は瞬時に行われます。 変換後、VM を起動できます。

## <a name="next-steps"></a>次のステップ

[スナップショット](snapshot-copy-managed-disk.md)を使用して、VM の読み取り専用コピーを取得します。
