---
title: Azure CLI を使用して異なる種類のディスク間でマネージド ディスク ストレージを変換する
description: Azure CLI を使用して、異なる種類のディスク間で Azure マネージド ディスクを変換する方法。
author: roygara
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 02/13/2021
ms.author: albecker
ms.subservice: disks
ms.openlocfilehash: 35ba681ae35e60d7cd275c9e649b2463d2dc30a0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558459"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Azure マネージド ディスク ストレージを Standard から Premium に、または Premium から Standard に変換する

Azure マネージド ディスクには、ディスクの種類が 4 つあります。Azure Ultra Disk、Premium SSD、Standard SSD、Standard HDD です。 パフォーマンスのニーズに合わせて、Premium SSD、Standard SSD、および Standard HDD を切り替えることができます。 まだ Ultra Disk との切り替えはできません。新しいディスクをデプロイする必要があります。

この機能は、アンマネージド ディスクではサポートされていません。 ただし、ディスクの種類を切り替えられるようにするために、簡単に[アンマネージド ディスクをマネージド ディスクに変換](convert-unmanaged-to-managed-disks.md)できます。

この記事では、Azure CLI を使用して、あるディスクの種類から別のものへ、マネージド ディスクを変換する方法について説明します。 ツールをインストールまたはアップグレードする必要には、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」をご覧ください。

## <a name="before-you-begin"></a>開始する前に

* ディスク変換は仮想マシン (VM) の再起動を伴うので、既に設定されているメンテナンス期間中にディスク ストレージの移行をスケジュールしてください。
* アンマネージド ディスクの場合、最初に[マネージド ディスクに変換する](convert-unmanaged-to-managed-disks.md)と、ストレージ オプションを切り替えることができます。


## <a name="switch-all-managed-disks-of-a-vm-between-from-one-account-to-another"></a>VM のすべてのマネージド ディスクをある特定のアカウントから別のものに切り替える

この例では、VM のすべてのディスクを Premium Storage に変換する方法を示します。 ただし、この例の sku 変数を変更すると、VM のディスクの種類を Standard SSD または Standard HDD に変換できます。 Premium マネージド ディスクを使用するには、Premium Storage に対応している [VM のサイズ](../sizes.md)を使用している必要があることに注意してください。 この例は、Premium ストレージに対応するサイズへの切り替えも行います。

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
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
## <a name="switch-individual-managed-disks-from-one-disk-type-to-another"></a>個々のマネージド ディスクをある特定のディスクの種類から別のものに切り替える

開発/テスト ワークロードでは、コストを削減するために Standard ディスクと Premium ディスクを混在させたい場合があります。 パフォーマンスを上げる必要があるディスクだけをアップグレードするように選択できます。 この例では、1 つの VM ディスクを Standard から Premium ストレージに変換する方法について説明します。 ただし、この例の sku 変数を変更すると、VM のディスクの種類を Standard SSD または Standard HDD に変換できます。 Premium マネージド ディスクを使用するには、Premium Storage に対応している [VM のサイズ](../sizes.md)を使用している必要があります。 この例は、Premium ストレージに対応するサイズへの切り替えも行います。

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
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

ディスクの種類は一瞬で更新されます。 変換後、VM を再起動できます。

## <a name="next-steps"></a>次のステップ

[スナップショット](snapshot-copy-managed-disk.md)を使用して、VM の読み取り専用コピーを作成します。