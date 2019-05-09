---
title: Azure マネージド ディスク ストレージを Standard から Premium に、または Premium から Standard に変換する | Microsoft Docs
description: Azure CLI を使用し、Azure マネージド ディスク ストレージを Standard から Premium に、または Premium から Standard に変換する方法。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 18730f662f36000e1efc826c35bebde79dbf0e79
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58013618"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Azure マネージド ディスク ストレージを Standard から Premium に、または Premium から Standard に変換する

Azure マネージド ディスクには[ディスクの種類](disks-types.md)が 4 つあります。Azure Ultra Disk Storage、Premium SSD、Standard SSD、Standard HDD です。 パフォーマンス ニーズに合わせ、Premium SSD、Standard SSD、Standard HDD を簡単に切り替えることができます。ダウンタイムはほとんどありません。 この機能は、アンマネージド ディスクや Ultra Disk Storage ではサポートされていません。 ただし、ディスクの種類を簡単に切り替えることができるように、簡単に[アンマネージド ディスクをマネージド ディスクに変換](convert-unmanaged-to-managed-disks.md)できます。

この記事では、Azure CLI を使用し、Azure マネージド ディスクを Standard から Premium に、または Premium から Standard に変換する方法について説明します。 ツールをインストールまたはアップグレードする必要には、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」をご覧ください。

## <a name="before-you-begin"></a>開始する前に

* ディスク変換は仮想マシン (VM) の再起動を伴うので、既に設定されているメンテナンス期間中にディスク ストレージの移行をスケジュールしてください。
* アンマネージド ディスクの場合、最初に[マネージド ディスクに変換する](convert-unmanaged-to-managed-disks.md)と、ストレージ オプションを切り替えることができます。


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>VM のすべてのマネージド ディスクを Premium と Standard の間で切り替える

この例では、VM のすべてのマネージド ディスクを Standard ストレージから Premium ストレージに、または Premium ストレージから Standard ストレージに変換する方法について説明します。 Premium マネージド ディスクを使用するには、Premium Storage に対応している [VM のサイズ](sizes.md)を使用している必要があります。 この例は、Premium ストレージに対応するサイズへの切り替えも行います。

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the SKU of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the SKU of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Standard と Premium の間で個別のマネージド ディスクを切り替える

開発/テスト ワークロードでは、コストを削減するために Standard ディスクと Premium ディスクを混在させたい場合があります。 パフォーマンスを上げる必要があるディスクだけをアップグレードするように選択できます。 この例では、1 つの VM ディスクを Standard ストレージから Premium ストレージに、または Premium ストレージから Standard ストレージに変換する方法について説明します。 Premium マネージド ディスクを使用するには、Premium Storage に対応している [VM のサイズ](sizes.md)を使用している必要があります。 この例は、Premium ストレージに対応するサイズへの切り替えも行います。

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --ids $vmId --size $size

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Standard HDD と Standard SSD の間でマネージド ディスクを切り替える

この例では、1 つの VM ディスクを Standard HDD から Standard SSD に、または Standard SSD から Standard HDD に変換する方法について説明します。

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Choose between Standard_LRS and StandardSSD_LRS based on your scenario
sku='StandardSSD_LRS'

#Get the parent VM ID 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the disk type
az vm deallocate --ids $vmId 

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>Azure portal で Standard と Premium の間でマネージド ディスクを切り替える

次の手順に従います。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. **仮想マシン**の一覧から VM を選択します。
3. VM が停止していない場合、VM の **[概要]** ウィンドウの一番上で **[停止]** を選択し、VM が停止するまで待ちます。
4. VM のウィンドウで、メニューから **[ディスク]** を選択します。
5. 変換するディスクを選択します。
6. メニューから **[構成]** を選択します。
7. **[アカウントの種類]** を **Standard HDD** から **Premium SSD** に、または **Premium SSD** から **Standard HDD** に変更します。
8. **[保存]** を選択し、ディスク ウィンドウを閉じます。

ディスクの種類は一瞬で更新されます。 変換後、VM を再起動できます。

## <a name="next-steps"></a>次の手順

[スナップショット](snapshot-copy-managed-disk.md)を使用して、VM の読み取り専用コピーを作成します。
