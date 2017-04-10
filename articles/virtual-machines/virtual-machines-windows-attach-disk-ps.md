---
title: "Azure の Windows VM に PowerShell を使用してデータ ディスクを接続する | Microsoft Docs"
description: "Resource Manager デプロイメント モデルで、PowerShell を使用して Windows VM に新規または既存のデータ ディスクを接続する方法。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: c0f96dfadb41aa05f922629820f29454d49d847a
ms.openlocfilehash: 0d78c93e90af0fa4c813ee53e4003c1f09442157


---

# <a name="attach-a-data-disk-to-a-windows-vm-using-powershell"></a>PowerShell を使用して Windows VM にデータ ディスクを接続する

この記事では、PowerShell を使用して新しいディスクおよび既存のディスクを Windows 仮想マシンに接続する方法について説明します。 VM で管理対象ディスクを使用している場合は、管理対象のデータ ディスクを追加できます。 同様に、ストレージ アカウントの非管理対象ディスクを使用している VM には、非管理対象データ ディスクを接続できます。

接続する前に、次のヒントを確認してください。
* 仮想マシンのサイズによって、接続できるデータ ディスク数は変わります。 詳細については、「 [仮想マシンのサイズ](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。
* Premium Storage を使用するには、Premium Storage に対応した VM サイズ (DS シリーズや GS シリーズなどの仮想マシン) が必要です。 これらの仮想マシンでは、Premium および Standard のストレージ アカウントのディスクを使用できます。 Premium Storage は特定のリージョンで使用できます。 詳細については、「 [Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。

## <a name="before-you-begin"></a>開始する前に
PowerShell を使用する場合は、AzureRM.Compute PowerShell モジュールの最新バージョンがあることを確認してください。 インストールするには次のコマンドを実行します。

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
詳細については、[Azure PowerShell のバージョン管理に関するページ](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning)をご覧ください。


## <a name="add-an-empty-data-disk-to-a-virtual-machine"></a>空のデータ ディスクを仮想マシンに追加する

この例では、既存の仮想マシンに空のデータ ディスクを追加する方法を示します。

### <a name="using-managed-disks"></a>管理対象ディスクを使用する場合

```powershell
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'West Central US' 
$storageType = 'PremiumLRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location $location -CreateOption Empty -DiskSizeGB 128

$dataDisk1 = New-AzureRmDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzureRmVM -VM $vm -ResourceGroupName $rgName
```

### <a name="using-unmanaged-disks-in-a-storage-account"></a>ストレージ アカウントの非管理対象ディスクを使用する場合

```powershell
    $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    Add-AzureRmVMDataDisk -VM $vm -Name "disk-name" -VhdUri "https://mystore1.blob.core.windows.net/vhds/datadisk1.vhd" -LUN 0 -Caching ReadWrite -DiskSizeinGB 1 -CreateOption Empty
    Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
```


### <a name="initialize-the-disk"></a>ディスクの初期化

空のディスクは、追加した後で初期化する必要があります。 ディスクを初期化するには、VM にログインしてディスクの管理を使用します。 VM を作成したときに WinRM と証明書を有効にした場合は、リモート PowerShell を使用してディスクを初期化できます。 また、カスタム スクリプト拡張機能を使用することができます。 

```powershell
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzureRmVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```
        
スクリプト ファイルには、ディスクを初期化するために次のようなコードを含めることができます。

```powershell
    $disks = Get-Disk | Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { [char]$_ }
    $count = 0
    $labels = "data1","data2"

    foreach ($disk in $disks) {
        $driveLetter = $letters[$count].ToString()
        $disk | 
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] -Confirm:$false -Force
    $count++
    }
```


## <a name="attach-an-existing-data-disk-to-a-vm"></a>VM に既存のデータ ディスクを接続する

既存の VHD を、管理対象データ ディスクとして仮想マシンに接続することもできます。 

### <a name="using-managed-disks"></a>管理対象ディスクを使用する場合

```powershell
$rgName = 'myRG'
$vmName = 'ContosoMdPir3'
$location = 'West Central US' 
$storageType = 'PremiumLRS'
$dataDiskName = $vmName + '_datadisk2'
$dataVhdUri = 'https://mystorageaccount.blob.core.windows.net/vhds/managed_data_disk.vhd' 

$diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location $location -CreateOption Import -SourceUri $dataVhdUri -DiskSizeGB 128

$dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk2.Id -Lun 2

Update-AzureRmVM -VM $vm -ResourceGroupName $rgName
```

## <a name="next-steps"></a>次のステップ

[スナップショット](virtual-machines-windows-snapshot-copy-managed-disk.md)を作成します。



<!--HONumber=Feb17_HO2-->


