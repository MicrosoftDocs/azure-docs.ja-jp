---
title: "クラシック デプロイメント モデルから Azure Managed Disks に&1; つの VM を移行する | Microsoft Docs"
description: "Resource Manager デプロイメント モデルで、1 つの Azure VM をクラシック デプロイメント モデルから Managed Disks に移行します。"
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
ms.date: 02/05/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 2c9877f84873c825f96b62b492f49d1733e6c64e
ms.openlocfilehash: 42d9e68c3c18d04c02ab818d84a653ece811fc52
ms.lasthandoff: 03/15/2017


---

# <a name="migrate-a-single-azure-vm-from-classic-to-managed-disks"></a>1 つの Azure VM をクラシック デプロイメント モデルから Managed Disks に移行する 


このセクションでは、Resource Manager デプロイメント モデルで、既存の Azure VM をクラシック デプロイメント モデルから [Managed Disks](../storage/storage-managed-disks-overview.md) する方法を説明します。


## <a name="plan-for-the-migration-to-managed-disks"></a>Managed Disks への移行の計画

このセクションでは、VM とディスクの種類に関する最適な決定を行います。


### <a name="location"></a>Location (場所)

Azure Managed Disks を使用できる場所を選びます。 Premium Managed Disks に移行する場合は、移行先に予定しているリージョンで Premium Storage が使用可能であることも確認します。 使用できる場所に関する最新情報については、[リージョン別の Azure サービス](https://azure.microsoft.com/regions/#services)に関するページをご覧ください。

### <a name="vm-sizes"></a>VM サイズ

Premium Managed Disks に移行する場合は、VM が配置されているリージョンで利用できる Premium Storage 対応サイズに合うように VM のサイズを更新する必要があります。 Premium Storage で対応できる VM サイズをご確認ください。 Azure VM のサイズの仕様は、「 [仮想マシンのサイズ](virtual-machines-windows-sizes.md)」に記載されています。
Premium Storage で動作する仮想マシンのパフォーマンス特性を確認し、ワークロードに最適な VM を選択してください。 ディスク トラフィックが流れるのに十分な帯域幅が VM で利用できることを確認します。

### <a name="disk-sizes"></a>ディスク サイズ

**Premium Managed Disks**

VM で使える Premium 管理ディスクには&3; 種類あり、それぞれに特定の IOPS とスループットの制限があります。 VM の Premium ディスクの種類を選ぶ場合は、容量、パフォーマンス、スケーラビリティ、最大負荷に関するアプリケーションのニーズに基づいて、これらの制限を考慮してください。

| Premium ディスクの種類  | P10               | P20               | P30               |
|---------------------|-------------------|-------------------|-------------------|
| ディスク サイズ           | 128 GB            | 512 GB            | 1024 GB (1 TB)    |
| ディスクあたりの IOPS       | 500               | 2300              | 5000              |
| ディスクあたりのスループット | 100 MB/秒 | 150 MB/秒 | 200 MB/秒 |

**Standard Managed Disks**

VM で使用できる Standard Managed Disks は&5; 種類あります。 それぞれ容量は異なりますが、IOPS とスループットの制限は同じです。 アプリケーションの容量のニーズに基づいて Standard Managed Disks の種類を選択してください。

| Standard ディスクの種類  | S4               | S6               | S10              | S20              | S30              |
|---------------------|------------------|------------------|------------------|------------------|------------------|
| ディスク サイズ           | 30 GB            | 64 GB            | 128 GB           | 512 GB           | 1024 GB (1 TB)   |
| ディスクあたりの IOPS       | 500              | 500              | 500              | 500              | 500              |
| ディスクあたりのスループット | 60 MB/秒 | 60 MB/秒 | 60 MB/秒 | 60 MB/秒 | 60 MB/秒 |

### <a name="disk-caching-policy"></a>ディスク キャッシュ ポリシー 

**Premium Managed Disks**

既定では、ディスクのキャッシュ ポリシーは、すべてのPremium データ ディスクに対して「*読み取り専用*」、VM にアタッチされた Premium オペレーティング システム ディスクに対して「*読み取り/書き込み*」です。 アプリケーションの IO パフォーマンスを最適化するには、この構成をお勧めします。 書き込み量の多いディスクや書き込み専用のディスク (SQL Server ログ ファイルなど) の場合は、ディスク キャッシュを無効にすることで、アプリケーションのパフォーマンスを向上できる場合があります。

### <a name="pricing"></a>価格

[Managed Disks の価格](https://azure.microsoft.com/en-us/pricing/details/managed-disks/)をご確認ください。 Premium Managed Disks の価格は、Premium 非管理対象ディスクと同じです。 一方、Standard 管理ディスクの価格は、Standard 非管理ディスクとは異なります。


## <a name="checklist"></a>チェック リスト

1.  Premium 管理ディスクに移行する場合は、移行先のリージョンでそのディスクが使用できることを確認してください。

2.  使用する新しい VM シリーズを決定します。 Premium 管理ディスクに移行する場合は、Premium Storage に対応したものにする必要があります。

3.  使用する正確な VM のサイズを、移行先のリージョンで使用可能なものから選択します。 VM サイズは、所有するデータ ディスクの数がサポートされるように十分な大きさにする必要があります。 たとえば、データ ディスクが&4; つある場合、VM には&2; つ以上のコアが必要です。 さらに、処理能力、メモリ、ネットワーク帯域幅のニーズについても検討します。

4.  現在の VM の詳細 (ディスクの一覧や対応する VHD BLOB など) を手元に用意します。

ダウンタイムに備えてアプリケーションを準備します。 移行を問題なく実行するには、現在のシステムですべての処理を停止する必要があります。 そうすることで初めて、新しいプラットフォームに移行できる安定した状態になります。 ダウンタイム時間は、移行するディスクのデータ量によって異なります。


## <a name="migrate-the-vm"></a>VM の移行

ダウンタイムに備えてアプリケーションを準備します。 移行を問題なく実行するには、現在のシステムですべての処理を停止する必要があります。 そうすることで初めて、新しいプラットフォームに移行できる安定した状態になります。 ダウンタイム時間は、移行するディスクのデータ量によって異なります。


1.  最初に、共通のパラメーターを設定します。

    ```powershell
    $resourceGroupName = 'yourResourceGroupName'
    
    $location = 'your location' 
    
    $virtualNetworkName = 'yourExistingVirtualNetworkName'
    
    $virtualMachineName = 'yourVMName'
    
    $virtualMachineSize = 'Standard_DS3'
    
    $adminUserName = "youradminusername"
    
    $adminPassword = "yourpassword" | ConvertTo-SecureString -AsPlainText -Force
    
    $imageName = 'yourImageName'
    
    $osVhdUri = 'https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd'
    
    $dataVhdUri = 'https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk1.vhd'
    
    $dataDiskName = 'dataDisk1'
    ```

2.  VHD を使用してクラシック VM から管理 OS ディスクを作成します。

    $osVhdUri パラメーターには、OS VHD の完全な URI を指定してください。 また、移行先のディスクの種類 (Premium または Standard) に合わせて、**-AccountType** で「**PremiumLRS**」または「**StandardLRS**」と入力します。

    ```powershell
    $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk (New-AzureRmDiskConfig '
    -AccountType PremiumLRS -Location $location -CreateOption Import -SourceUri $osVhdUri) '
    -ResourceGroupName $resourceGroupName
    ```

3.  新しい VM に OS ディスクを接続します。

    ```powershell
    $VirtualMachine = New-AzureRmVMConfig -VMName $virtualMachineName -VMSize $virtualMachineSize
    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -ManagedDiskId $osDisk.Id '
    -StorageAccountType PremiumLRS -DiskSizeInGB 128 -CreateOption Attach -Windows
    ```

4.  データ VHD ファイルから管理データ ディスクを作成し、新しい VM に追加します。

    ```powershell
    $dataDisk1 = New-AzureRmDisk -DiskName $dataDiskName -Disk (New-AzureRmDiskConfig '
    -AccountType PremiumLRS -Location $location -CreationDataCreateOption Import '
    -SourceUri $dataVhdUri ) -ResourceGroupName $resourceGroupName
    
    $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name $dataDiskName '
    -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1
    ```

5.  パブリック IP、仮想ネットワーク、NIC を設定して新しい VM を作成します。

    ```powershell
    $publicIp = New-AzureRmPublicIpAddress -Name ($VirtualMachineName.ToLower()+'_ip') '
    -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
    
    $vnet = Get-AzureRmVirtualNetwork -Name $virtualNetworkName -ResourceGroupName $resourceGroupName
    
    $nic = New-AzureRmNetworkInterface -Name ($VirtualMachineName.ToLower()+'_nic') '
    -ResourceGroupName $resourceGroupName -Location $location -SubnetId $vnet.Subnets[0].Id '
    -PublicIpAddressId $publicIp.Id
    
    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nic.Id
    
    New-AzureRmVM -VM $VirtualMachine -ResourceGroupName $resourceGroupName -Location $location
    ```

> [!NOTE]
>このガイドで説明されていないアプリケーションをサポートするには、追加の手順が必要になる場合があります。
>
>

## <a name="next-steps"></a>次のステップ

- 仮想マシンへの接続 手順については、「 [Windows が実行されている Azure 仮想マシンに接続してログオンする方法](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。


