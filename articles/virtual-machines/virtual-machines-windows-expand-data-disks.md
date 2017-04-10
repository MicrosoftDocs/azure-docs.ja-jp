---
title: "Azure で Windows VM に接続されているデータ ディスクを拡張する | Microsoft Docs"
description: "PowerShell を使用して Windows 仮想マシンに接続されているデータ ディスクのサイズを拡張します。"
services: virtual-machines-windows
documentationcenter: na
author: cynthn
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/02/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: f7816f6e2120c02d970de640b397a1d2d570e35d
ms.lasthandoff: 03/04/2017


---

# <a name="increase-the-size-of-a-data-disk-attached-to-a-windows-vm"></a>Windows VM に接続されているデータ ディスクのサイズを増やす

仮想マシンに接続されているデータ ディスクのサイズを増やす必要がある場合は、PowerShell を使用してサイズを増やすことができます。 Azure VM の設定でデータ ディスクのサイズを増やした後、VM 内で新しいディスク領域を割り当てる必要があります。


## <a name="use-powershell-to-increase-the-size-of-a-managed-data-disk"></a>Powershell を使用して管理対象データ ディスクのサイズを増やす

管理対象データ ディスクのサイズを増やすには、次の PowerShell コマンドレットを使用します。

|                                                                    |                                                            |
|--------------------------------------------------------------------|------------------------------------------------------------|
| [Get-AzureRMReseourceGroup](/powershell/Get-AzureRMReseourceGroup) | [Get-AzureRMVM](/powershell/getazurermvm)                  |
| [Stop-AzureRMVM](/powershell/stop-azurermvm)                       | [Set-AzureRmVMDataDisk](/powershell/Set-AzureRmVMDataDisk) |
| [Update-AzureRmVM](/powershell/update-azurermvm)                   | [Start-AzureRmVM](/powershell/start-azurermvm)             |
<br>

次のスクリプトは、VM 情報の取得、データ ディスクの選択、および新しいサイズの指定を行う方法を示しています。

```powershell
# Select resource group
     
    $rg = Get-AzureRMReseourceGroup | Out-GridView `
        -Title "Select the resource group" `
        -PassThru
     
    $rgName = $rg.ResourceGroupName

# Select the VM
     
    $vm = Get-AzureRMVM -ResourceGroupName $rgName `
        | Out-GridView `
            -Title "Select a VM" `
             -PassThru

# Select data disk
     
    $disk = $vm.dataDiskNames | Out-GridView `
        -Title "Select a data disk" `
        -PassThru
    
# Specify a larger size for the data disk 
       
    $size =  Read-Host `
        -Prompt "New size in GB"

# Stop and Deallocate VM prior to resizing data disk
     
    $vm | Stop-AzureRMVM -Force

# Set the new disk size
    
    Set-AzureRmVMDataDisk -VM $vm -Name "$disk" -DiskSizeInGB $size

# View the new size of the data disk(s)
    
    $vm.StorageProfile.DataDisks

# Update the configuration in Azure
    
    Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Start the VM
    
    Start-AzureRmVM -ResourceGroupName $rgName -VMName $vm.name

```

## <a name="use-powershell-to-increase-the-size-of-an-unmanaged-data-disk"></a>Powershell を使用して非管理対象データ ディスクのサイズを増やす

ストレージ アカウント内の非管理対象データ ディスクのサイズを増やすには、次の PowerShell コマンドレットを使用します。

|                                                                    |                                                            |
|--------------------------------------------------------------------|------------------------------------------------------------|
| [Get-AzureRMStorageAccount](/powershell/Get-AzureRMStorageAccount) | [Get-AzureRMVM](/powershell/getazurermvm)                  |
| [Stop-AzureRMVM](/powershell/stop-azurermvm)                       | [Set-AzureRmVMDataDisk](/powershell/Set-AzureRmVMDataDisk) |
| [Update-AzureRmVM](/powershell/update-azurermvm)                   | [Start-AzureRmVM](/powershell/start-azurermvm)             |

<br>

次のスクリプトは、VM とストレージ アカウントの情報の取得、データ ディスクの選択、および新しいサイズの指定を行う方法を示しています。

```powershell

# Select Azure Storage Account
     
    $storageAccount =
        Get-AzureRMStorageAccount | Out-GridView `
            -Title "Select Azure Storage Account" `
            -PassThru
     
    $rgName = $storageAccount.ResourceGroupName

# Select the VM
     
    $vm = Get-AzureRMVM `
    -ResourceGroupName $rgName | Out-GridView `
            -Title "Select a VM …" `
            -PassThru

# Select Data Disk to resize
     
    $disk =
        $vm.DataDiskNames | Out-GridView `
            -Title "Select a data disk to resize" `
            -PassThru
     
    
# Specify a larger data disk size 
       
    $size =  Read-Host `
        -Prompt "New size in GB"

# Stop and Deallocate VM prior to resizing data disk
     
    $vm | Stop-AzureRMVM -Force

# Set the new disk size

    Set-AzureRmVMDataDisk -VM $vm -Name "$disk" `
        -DiskSizeInGB $size

# Update the configuration in Azure
    
    Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Start the VM
    Start-AzureRmVM -ResourceGroupName $rgName `
    -VMName $vm.name
    
```

## <a name="allocate-the-unallocated-disk-space"></a>未割り当てのディスク領域を割り当てる 

ドライブを大きくしたら、VM 内から新しい未割り当て領域を割り当てる必要があります。 領域を割り当てるには、VM に接続し、ディスク管理 (diskmgmt.msc) を使用できます。 VM での証明書の作成時に WinRM を有効にした場合は、リモート PowerShell を使用してディスクを初期化できます。 また、カスタム スクリプト拡張機能を使用することができます。 

```powershell
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzureRmVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```
        
スクリプト ファイルには、ドライブの割り当てをディスクの最大サイズに増やすための次のようなコードを含めることができます。

```powershell
$driveLetter= "F"

$MaxSize = (Get-PartitionSupportedSize -DriveLetter $driveLetter).sizeMax

Resize-Partition -DriveLetter $driveLetter -Size $MaxSize
```

## <a name="next-steps"></a>次のステップ
- [ディスクと VHD](../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) に関する説明を参照します。
