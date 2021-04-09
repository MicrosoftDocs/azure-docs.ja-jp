---
title: 仮想マシン スケール セットの管理のための Azure CLI サンプル
description: このサンプルでは、仮想マシン スケール セットにディスクを追加する方法を示します。 ディスクをアップグレードし、仮想マシンを Azure AD 認証に追加できます。
author: mimckitt
ms.author: mimckitt
ms.date: 02/04/2021
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1abdf7ae15753d78ac8728f57e9b0cd5dcd9165e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101672600"
---
# <a name="create-and-manage-virtual-machine-scale-set"></a>仮想マシン スケール セットの作成と管理

これらのサンプル コマンドを使用して、Azure CLI で仮想マシン スケール セットのプロトタイプを作成します。

これらのサンプルコマンドは、次の操作を示しています。

* 仮想マシン スケール セットを作成します。
* 新規または既存のディスクを、スケール セットまたはセットのインスタンスに追加し、アップグレードする。
* スケール セットを Azure Active Directory (Azure AD) 認証に追加する。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="sample-commands"></a>サンプル コマンド

```azurecli
# Create a resource group
az group create --name MyResourceGroup --location eastus

# Create virtual machine scale set
az vmss create --resource-group MyResourceGroup --name myScaleSet --instance-count 2 \
    --image UbuntuLTS --upgrade-policy-mode automatic --admin-username azureuser \
    --generate-ssh-keys

# Attach a new managed disk to your scale set
az vmss disk attach --resource-group MyResourceGroup --vmss-name myScaleSet --size-gb 50
```

新しいデータ ディスクを追加したら、ディスクをフォーマットしてマウントします。 Windows 仮想マシンについては、「[Azure portal を使用して Windows VM にマネージド データ ディスクを接続する](../../virtual-machines/windows/attach-managed-disk-portal.md)」をご覧ください。 Linux 仮想マシンについては、「[Linux VM へのディスクの追加](../../virtual-machines/linux/add-disk.md)」をご覧ください。

```azurecli
# Attach an existing managed disk to a virtual machine instance in your scale set
az vmss disk attach --resource-group MyResourceGroup --disk myDataDisk \
    --vmss-name myScaleSet --instance-id 0

# See the instances in your virtual machine scale set
az vmss list-instances --resource-group MyResourceGroup --name myScaleSet --output table

# See the disks for your virtual machine
az disk list --resource-group MyResourceGroup \
    --query "[*].{Name:name,Gb:diskSizeGb,Tier:accountType}" --output table

# Deallocate the virtual machine
az vmss deallocate --resource-group MyResourceGroup --name myScaleSet --instance-ids 0 

# Resize the disk
az disk update --resource-group MyResourceGroup --name myDataDisk --size-gb 200

# Restart the disk
az vmss restart --resource-group MyResourceGroup --name myScaleSet --instance-ids 0
```

拡張ディスクを使用するには、基になるパーティションを拡張します。 詳細については、「[ディスク パーティションとファイル システムの拡張](../../virtual-machines/linux/expand-disks.md#expand-a-disk-partition-and-filesystem)」をご覧ください。

この例では、データ ディスクのサイズを変更しています。 これと同じ手順を使用して、OS ディスクを更新できます。 Windows 仮想マシンの詳細については、「[仮想マシンの OS ドライブを展開する方法](../../virtual-machines/windows/expand-os-disk.md)」をご覧ください。 Linux 仮想マシンの詳細については、「[Azure CLI を使用して Linux VM の仮想ハード ディスクを拡張する](../../virtual-machines/linux/expand-disks.md)」をご覧ください。

```azurecli
# Enable managed service identity on your scale set. This is required to authenticate and interact with other Azure services using bearer tokens.
az vmss identity assign --resource-group MyResourceGroup --name myScaleSet --role Owner \
    --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup

# Connect to Azure AD authentication
az vmss extension set --resource-group MyResourceGroup --name AADLoginForWindows \
    --publisher Microsoft.Azure.ActiveDirectory --vmss-name myScaleSet

# Upgrade one instance of a scale set virtual machine
az vmss update-instances --resource-group MyResourceGroup --name myScaleSet --instance-ids 0 

# Remove a managed disk from the scale set
az vmss disk detach --resource-group MyResourceGroup --vmss-name myScaleSet --lun 0

# Remove a managed disk from an instance
az vmss disk detach --resource-group MyResourceGroup --vmss-name myScaleSet --instance-id 1 --lun 0

# Delete the pre-existing disk
az disk delete --resource-group MyResourceGroup --disk myDataDisk
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

これらのコマンドの使用後、次のコマンドを実行して、リソース グループと、関連付けられているすべてのリソースを削除します。

```azurecli
az group delete --name MyResourceGroup
```

## <a name="azure-cli-references-used-in-this-article"></a>この記事で使用されている Azure CLI リファレンス

* [az disk delete](/cli/azure/disk#az_disk_delete)
* [az disk list](/cli/azure/disk#az_disk_list)
* [az disk update](/cli/azure/disk#az_disk_update)
* [az group create](/cli/azure/group#az_group_create)
* [az vmss create](/cli/azure/vmss#az_vmss_create)
* [az virtual machine scale set deallocate](/cli/azure/vmss#az_vmss_deallocate)
* [az vmss disk attach](/cli/azure/vmss/disk#az_vmss_disk_attach)
* [az vmss disk detach](/cli/azure/vmss/disk#az_vmss_disk_detach)
* [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set)
* [az vmss identity assign](/cli/azure/vmss/identity#az_vmss_identity_assign)
* [az vmss list-instances](/cli/azure/vmss#az_vmss_list_instances)
* [az vmss restart](/cli/azure/vmss#az_vmss_restart)
* [az vmss update-instances](/cli/azure/vmss#az_vmss_update_instances)