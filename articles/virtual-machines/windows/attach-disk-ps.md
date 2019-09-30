---
title: PowerShell を使用して Azure 内の Windows VM に データ ディスクを接続する | Microsoft Docs
description: Resource Manager デプロイ モデルで、PowerShell を使用して Windows VM に新規または既存のデータ ディスクを接続する方法です。
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 81d0469645281f63527db5cf600f157d8d6b29d7
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70968358"
---
# <a name="attach-a-data-disk-to-a-windows-vm-with-powershell"></a>PowerShell を使用して Windows VM にデータ ディスクを接続する

この記事では、PowerShell を使用して新しいディスクおよび既存のディスクを Windows 仮想マシンに接続する方法について説明します。 

最初に、以下のヒントを確認してください。

* 仮想マシンのサイズによって、接続できるデータ ディスク数は変わります。 詳細については、 [仮想マシンのサイズ](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページをご覧ください。
* Premium SSD を使用するには、[Premium Storage に対応した VM の種類](sizes-memory.md) (DS シリーズや GS シリーズなどの仮想マシン) が必要です。

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-an-empty-data-disk-to-a-virtual-machine"></a>空のデータ ディスクを仮想マシンに追加する

この例では、既存の仮想マシンに空のデータ ディスクを追加する方法を示します。

### <a name="using-managed-disks"></a>マネージド ディスクを使用する場合

```azurepowershell-interactive
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US' 
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="using-managed-disks-in-an-availability-zone"></a>可用性ゾーンでマネージド ディスクを使用する場合

可用性ゾーンにディスクを作成するには、`-Zone` パラメーターを指定して [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig) を使用します。 次の例では、ゾーン *1* にディスクを作成します。

```powershell
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US 2'
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128 -Zone 1
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="initialize-the-disk"></a>ディスクの初期化

空のディスクは、追加した後で初期化する必要があります。 ディスクを初期化するには、VM にサインインしてディスクの管理を使用します。 VM を作成したときに [WinRM](https://docs.microsoft.com/windows/desktop/WinRM/portal) と証明書を有効にした場合は、リモート PowerShell を使用してディスクを初期化できます。 また、カスタム スクリプト拡張機能を使用することができます。

```azurepowershell-interactive
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```

スクリプト ファイルには、たとえば、ディスクを初期化するためのコードを含めることができます。

```azurepowershell-interactive
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

既存のマネージド ディスクをデータ ディスクとして VM にアタッチできます。

```azurepowershell-interactive
$rgName = "myResourceGroup"
$vmName = "myVM"
$location = "East US" 
$dataDiskName = "myDisk"
$disk = Get-AzDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

## <a name="next-steps"></a>次の手順

テンプレートを使用してマネージド ディスクをデプロイすることもできます。 詳細については、「[Azure Resource Manager テンプレートでの管理ディスクの使用](using-managed-disks-template-deployments.md)」または複数のデータ ディスクをデプロイする[クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-multiple-data-disk)を参照してください。
