---
title: "Azure 管理ディスクのストレージを Standard から Premium に (または Premium から Standard に) 変換する | Microsoft Docs"
description: "Azure 管理ディスクのストレージを Azure CLI を使用して Standard から Premium に (または Premium から Standard に) 変換する方法。"
services: virtual-machines-linux
documentationcenter: 
author: ramankum
manager: kavithag
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: ramankum
ms.openlocfilehash: 0380b4aaa23b4aaba4c67d05e2d62f3ef41d6a32
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Azure 管理ディスクのストレージを Standard から Premium に (または Premium から Standard に) 変換する

管理ディスクには、[Premium](../../storage/storage-premium-storage.md) (SSD ベース) と [Standard](../../storage/storage-standard-storage.md) (HDD ベース) という 2 つのストレージ オプションがあります。 パフォーマンス上のニーズに基づいて、この 2 つのオプションを最小限のダウンタイムで簡単に切り替えることができます。 この機能は、管理されていないディスクに対して使用することはできません。 ただし、2 つのオプションを簡単に切り替えるために、簡単に[管理ディスクに変換](convert-unmanaged-to-managed-disks.md)できます。

この記事では、管理ディスクを Azure CLI を使用して Standard から Premium に (または Premium から Standard に) 変換する方法を示します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli.md)」を参照してください。 

## <a name="before-you-begin"></a>開始する前に

* 変換作業は VM の再起動を伴うので、既に設定されているメンテナンス期間中に ディスク ストレージの移行をスケジュールしてください。 
* 管理されていないディスクを使用している場合は、まず[管理ディスクに変換](convert-unmanaged-to-managed-disks.md)してから、この記事を参照して 2 つのストレージ オプションを切り替えてください。 


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>VM のすべての管理ディスクを Standard から Premium に (または Premium から Standard に) 変換する

次の例は、VM のすべてのディスクを Standard ストレージから Premium ストレージに切り替える方法を示しています。 Premium Managed Disks を使用するには、Premium Storage に対応している [VM のサイズ](sizes.md)を使用している必要があります。 この例は、Premium ストレージに対応するサイズへの切り替えも行います。

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the sku of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the sku of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>管理ディスクを Standard から Premium に (または Premium から Standard に) 変換する

開発/テスト ワークロードでは、コストを削減するために Standard ディスクと Premium ディスクを混在させたい場合があります。 これは、優れたパフォーマンスを必要とするディスクのみを Premium ストレージにアップグレードすることで実現できます。 次の例は、VM の 1 つのディスクを Standard から Premium に (または Premium から Standard に) 切り替える方法を示しています。 Premium Managed Disks を使用するには、Premium Storage に対応している [VM のサイズ](sizes.md)を使用している必要があります。 この例は、Premium ストレージに対応するサイズへの切り替えも行います。

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --ids $vmId --size $size

# Update the sku
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="next-steps"></a>次のステップ

[スナップショット](snapshot-copy-managed-disk.md)を使用して、VM の読み取り専用コピーを取得します。

