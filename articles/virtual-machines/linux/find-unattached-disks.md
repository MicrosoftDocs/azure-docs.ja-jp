---
title: Azure CLI - 接続されていないマネージドおよびアンマネージド ディスクを見つけて削除する
description: Azure CLI を使用して、接続されていない Azure 管理ディスクと非管理対象ディスク (VHD/ページ BLOB) を見つけて削除する方法
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/30/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 0c3e8bb2ff6f3313e851a4253a95a5ad923a8f70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96016218"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks-using-the-azure-cli"></a>Azure CLI を使用し、接続されていない Azure マネージド ディスクおよび非管理対象ディスクを見つけて削除する
Azure で仮想マシン (VM) を削除するとき、既定では、その VM に接続されているディスクはいずれも削除されません。 この機能は、意図せず VM を削除したことによるデータ損失を防ぐのに役立ちます。 VM が削除された後、接続されていないディスクに対する料金の支払いが続きます。 この記事では、接続されていないディスクを見つけて削除し、不要なコストを削減する方法を示します。 


## <a name="managed-disks-find-and-delete-unattached-disks"></a>マネージド ディスク:接続されていないディスクの検索と削除 

次のスクリプトは、**ManagedBy** プロパティの値を調べることにより、接続されていない [マネージド ディスク](../managed-disks-overview.md)を探します。 マネージド ディスクが VM に接続しているとき、**ManagedBy** プロパティには VM のリソース ID が含まれます。 マネージド ディスクが接続されていないとき、**ManagedBy** プロパティは null です。 スクリプトは Azure サブスクリプションのすべてのマネージド ディスクを調べます。 **ManagedBy** プロパティに null が設定されたマネージド ディスクをスクリプトが見つけると、そのディスクは接続されていないと判断します。

>[!IMPORTANT]
>最初は、**deleteUnattachedDisks** 変数を 0 に設定してスクリプトを実行します。 この操作は、接続されていないすべてのマネージド ディスクを検索して表示します。
>
>接続されていないすべてのディスクを確認したら、再びスクリプトを実行します。このときは **deleteUnattachedDisks** 変数を 1 に設定します。 この操作は、接続されていないすべてのマネージド ディスクを削除します。
>

```azurecli
# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
deleteUnattachedDisks=0
unattachedDiskIds=$(az disk list --query '[?managedBy==`null`].[id]' -o tsv)
for id in ${unattachedDiskIds[@]}
do
    if (( $deleteUnattachedDisks == 1 ))
    then

        echo "Deleting unattached Managed Disk with Id: "$id
        az disk delete --ids $id --yes
        echo "Deleted unattached Managed Disk with Id: "$id

    else
        echo $id
    fi
done
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>アンマネージド ディスク:接続されていないディスクの検索と削除 

非管理対象ディスクは、[Azure Storage アカウント](../../storage/common/storage-account-overview.md)で[ページ BLOB](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) として格納された VHD ファイルです。 次のスクリプトは、**LeaseStatus** プロパティの値を調べて、接続されていない非管理対象ディスク (ページ BLOB) を探します。 非管理対象ディスクが VM に接続されているとき、**LeaseStatus** プロパティは **Locked** に設定されています。 非管理対象ディスクが接続されていないとき、**LeaseStatus** プロパティは **Unlocked** に設定されています。 このスクリプトは、Azure サブスクリプションのすべての Azure ストレージ アカウントの非管理対象ディスクを調べます。 **LeaseStatus** プロパティに **Unlocked** が設定された非管理対象ディスクをスクリプトが見つけると、そのディスクが接続されていないと判断します。

>[!IMPORTANT]
>最初は、**deleteUnattachedVHDs** 変数を 0 に設定してスクリプトを実行します。 この操作は、接続されていないすべての非管理対象 VHD を検索して表示します。
>
>接続されていないすべてのディスクを確認したら、再びスクリプトを実行します。このときは **deleteUnattachedVHDs** 変数を 1 に設定します。 この操作は、接続されていないすべての非管理対象 VHD を削除します。
>

```azurecli
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the details of the unattached VHDs
deleteUnattachedVHDs=0
storageAccountIds=$(az storage account list --query [].[id] -o tsv)
for id in ${storageAccountIds[@]}
do
    connectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
    containers=$(az storage container list --connection-string $connectionString --query [].[name] -o tsv)

    for container in ${containers[@]}
    do 
        
        blobs=$(az storage blob list -c $container --connection-string $connectionString --query "[?properties.blobType=='PageBlob' && ends_with(name,'.vhd')].[name]" -o tsv)
        
        for blob in ${blobs[@]}
        do
            leaseStatus=$(az storage blob show -n $blob -c $container --connection-string $connectionString --query "properties.lease.status" -o tsv)
            
            if [ "$leaseStatus" == "unlocked" ]
            then 

                if (( $deleteUnattachedVHDs == 1 ))
                then 

                    echo "Deleting VHD: "$blob" in container: "$container" in storage account: "$id

                    az storage blob delete --delete-snapshots include  -n $blob -c $container --connection-string $connectionString

                    echo "Deleted VHD: "$blob" in container: "$container" in storage account: "$id
                else
                    echo "StorageAccountId: "$id" container: "$container" VHD: "$blob
                fi

            fi
        done
    done
done 
```

## <a name="next-steps"></a>次のステップ

詳しくは、「[ストレージ アカウントの削除](../../storage/common/storage-account-create.md#delete-a-storage-account)」をご覧ください。