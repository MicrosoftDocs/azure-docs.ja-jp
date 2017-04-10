---
title: "Azure で VHD から管理対象ディスクを作成する | Microsoft Docs"
description: "Resource Manager デプロイメント モデルを使って、現在 Azure Storage アカウント内にある VHD から管理対象ディスクを作成します。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
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
ms.sourcegitcommit: 204fa369dd6db618ec5340317188681b0a2988e3
ms.openlocfilehash: 155ca6e6dfb55b17111762df03f4eb434d6e437d


---

# <a name="create-managed-disks-from-unmanaged-disks-in-a-storage-account"></a>ストレージ アカウント内の非管理対象ディスクから管理対象ディスクを作成する

現在 Azure Storage アカウント内にある既存のデータ ディスクまたは OS ディスクから、管理対象ディスクを作成できます。 また、VM 用の新しいデータ ディスクとして使うことができる空のディスクを作成することもできます。 

## <a name="before-you-begin"></a>開始する前に
PowerShell を使う場合は、AzureRM.Compute PowerShell モジュールの最新バージョンがあることを確認してください。 インストールするには次のコマンドを実行します。

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
詳しくは、[Azure PowerShell のバージョン管理に関するページ](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning)をご覧ください。


## <a name="create-a-managed-disk-from-a-vhd-in-an-azure-storage-account"></a>Azure Storage アカウント内の VHD から管理対象ディスクを作成する

この例では、VHD から管理対象ディスクとしてディスクを作成し、後で使うために **$disk1** パラメーターに割り当てます。 

管理対象ディスクは、**米国西部**にあるリソース グループ **myResourceGroup** に作成されます。 ディスクの名前は **myDisk** で、**mystorageaccount** という名前のストレージ アカウントにアップロードしてある **myDisk.vhd** という名前の VHD ファイルから作成されます。 管理対象ディスクは、Premium ローカル冗長ストレージ (LRS) に作成されます。 管理対象ディスクで使うことができる **-AccountType** オプションは、StandardLRS と PremiumLRS だけです。 

1.  パラメーターを設定します。

    ```powershell
    $rgName = "myResourceGroup"
    $location = "West Central US"
    $diskName = "myDisk"
    $vhdUri = "https://mystorageaccount.blob.core.windows.net/vhds/myDisk.vhd"
    ```

2. データ ディスクを作成します。 
    ```powershell
    $disk1 = New-AzureRmDisk -DiskName $diskName -Disk (New-AzureRmDiskConfig -AccountType PremiumLRS -Location $location -CreateOption Import -SourceUri $vhdUri) -ResourceGroupName $rgName
    ```
    
    

## <a name="create-an-empty-data-disk-as-a-managed-disk"></a>管理対象ディスクとして空のデータ ディスクを作成する

この例では、管理対象ディスクとしてデータ ディスクを作成し、後で使うために **$dataDisk2** パラメーターに割り当てます。 空のデータ ディスクは、実行中の VM に接続された後、VM にログインして diskmgmt.msc を使うか、または [WinRM とスクリプトをリモートで使って](virtual-machines-windows-attach-disk-ps.md#initialize-the-disk)、初期化する必要があります。

空のデータ ディスクは、**米国中西部**にあるリソース グループ **myResourceGroup** に作成されます。 ディスクの名前は **myEmptyDataDisk** です。 空のディスクは、Premium ローカル冗長ストレージ (LRS) に作成されます。 管理対象ディスクで使うことができる **-AccountType** オプションは、StandardLRS と PremiumLRS だけです。

この例のディスク サイズは 128 GB ですが、VM で実行するアプリケーションに必要なサイズを選ぶ必要があります。

1.  パラメーターを設定します。

    ```powershell
    $rgName = "myResourceGroup"
    $location = "West Central US"
    $dataDiskName = "myEmptyDataDisk"
    ```

2. データ ディスクを作成します。
    ```powershell
    $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk (New-AzureRmDiskConfig -AccountType PremiumLRS -Location $location -CreateOption Empty -DiskSizeGB 128) -ResourceGroupName $rgName
    ```
    
## <a name="next-steps"></a>次のステップ    
- 既に VM がある場合は、[データ ディスクを接続](virtual-machines-windows-attach-disk-portal.md)できます。


<!--HONumber=Feb17_HO2-->


