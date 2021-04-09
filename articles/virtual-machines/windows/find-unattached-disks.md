---
title: 接続されていない Azure マネージド ディスクおよび非管理対象ディスクを見つけて削除する
description: Azure PowerShell を使用して、接続されていない Azure 管理ディスクおよび非管理対象ディスク (VHD/ページ BLOB) を見つけて削除する方法。
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 66a54ea74fcc6d8d354f5adbffe214c34b4c20d5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554380"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>接続されていない Azure マネージド ディスクおよび非管理対象ディスクを見つけて削除する

Azure で仮想マシン (VM) を削除するとき、既定では、その VM に接続されているディスクはいずれも削除されません。 この機能は、意図せず VM を削除したことによるデータ損失を防ぐのに役立ちます。 VM が削除された後、接続されていないディスクに対する料金の支払いが続きます。 この記事では、接続されていないディスクを見つけて削除し、不要なコストを削減する方法を示します。

## <a name="managed-disks-find-and-delete-unattached-disks"></a>マネージド ディスク:接続されていないディスクの検索と削除

次のスクリプトは、**ManagedBy** プロパティの値を調べることにより、接続されていない [マネージド ディスク](../managed-disks-overview.md)を探します。 マネージド ディスクが VM に接続しているとき、**ManagedBy** プロパティには VM のリソース ID が含まれます。 マネージド ディスクが接続されていないとき、**ManagedBy** プロパティは null です。 スクリプトは Azure サブスクリプションのすべてのマネージド ディスクを調べます。 **ManagedBy** プロパティに null が設定されたマネージド ディスクをスクリプトが見つけると、そのディスクは接続されていないと判断します。

>[!IMPORTANT]
>最初は、**deleteUnattachedDisks** 変数を 0 に設定してスクリプトを実行します。 この操作は、接続されていないすべてのマネージド ディスクを検索して表示します。
>
>接続されていないすべてのディスクを確認したら、再びスクリプトを実行します。このときは **deleteUnattachedDisks** 変数を 1 に設定します。 この操作は、接続されていないすべてのマネージド ディスクを削除します。

```azurepowershell-interactive
# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
$deleteUnattachedDisks=0
$managedDisks = Get-AzDisk
foreach ($md in $managedDisks) {
    # ManagedBy property stores the Id of the VM to which Managed Disk is attached to
    # If ManagedBy property is $null then it means that the Managed Disk is not attached to a VM
    if($md.ManagedBy -eq $null){
        if($deleteUnattachedDisks -eq 1){
            Write-Host "Deleting unattached Managed Disk with Id: $($md.Id)"
            $md | Remove-AzDisk -Force
            Write-Host "Deleted unattached Managed Disk with Id: $($md.Id) "
        }else{
            $md.Id
        }
    }
 }
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>アンマネージド ディスク:接続されていないディスクの検索と削除

非管理対象ディスクは、[Azure Storage アカウント](../../storage/common/storage-account-overview.md)で[ページ BLOB](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) として格納された VHD ファイルです。 次のスクリプトは、**LeaseStatus** プロパティの値を調べて、接続されていない非管理対象ディスク (ページ BLOB) を探します。 非管理対象ディスクが VM に接続されているとき、**LeaseStatus** プロパティは **Locked** に設定されています。 非管理対象ディスクが接続されていないとき、**LeaseStatus** プロパティは **Unlocked** に設定されています。 このスクリプトは、Azure サブスクリプションのすべての Azure ストレージ アカウントの非管理対象ディスクを調べます。 **LeaseStatus** プロパティに **Unlocked** が設定された非管理対象ディスクをスクリプトが見つけると、そのディスクが接続されていないと判断します。

>[!IMPORTANT]
>最初は、**deleteUnattachedVHDs** 変数を `$false` に設定してスクリプトを実行します。 この操作は、接続されていないすべての非管理対象 VHD を検索して表示します。
>
>接続されていないすべてのディスクを確認したら、再びスクリプトを実行します。このときは **deleteUnattachedVHDs** 変数を `$true` に設定します。 この操作は、接続されていないすべての非管理対象 VHD を削除します。

```azurepowershell-interactive
# Set deleteUnattachedVHDs=$true if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=$false if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=$false
$storageAccounts = Get-AzStorageAccount
foreach($storageAccount in $storageAccounts){
    $storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.StorageAccountName)[0].Value
    $context = New-AzStorageContext -StorageAccountName $storageAccount.StorageAccountName -StorageAccountKey $storageKey
    $containers = Get-AzStorageContainer -Context $context
    foreach($container in $containers){
        $blobs = Get-AzStorageBlob -Container $container.Name -Context $context
        #Fetch all the Page blobs with extension .vhd as only Page blobs can be attached as disk to Azure VMs
        $blobs | Where-Object {$_.BlobType -eq 'PageBlob' -and $_.Name.EndsWith('.vhd')} | ForEach-Object { 
            #If a Page blob is not attached as disk then LeaseStatus will be unlocked
            if($_.ICloudBlob.Properties.LeaseStatus -eq 'Unlocked'){
                    if($deleteUnattachedVHDs){
                        Write-Host "Deleting unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                        $_ | Remove-AzStorageBlob -Force
                        Write-Host "Deleted unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                    }
                    else{
                        $_.ICloudBlob.Uri.AbsoluteUri
                    }
            }
        }
    }
}
```

## <a name="next-steps"></a>次のステップ

詳細については、「[ストレージ アカウントを削除する](../../storage/common/storage-account-create.md#delete-a-storage-account)」および [PowerShell を使用した孤立したディスクの特定](/archive/blogs/ukplatforms/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell)に関するページを参照してください。