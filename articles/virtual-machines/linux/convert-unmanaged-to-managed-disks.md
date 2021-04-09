---
title: Linux VM を非管理対象ディスクからマネージド ディスクに変換する
description: Azure CLI を使用して Linux VM を非管理対象ディスクからマネージド ディスクに変換する方法
author: roygara
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 12/15/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 1f3b62f8c05edffa1b55bf3d8cd24494b1c918bd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558493"
---
# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Linux 仮想マシンを非管理対象ディスクからマネージド ディスクに変換する

非管理対象ディスクを使用する既存の Linux 仮想マシン (VM) を所有している場合、[Azure Managed Disks](../managed-disks-overview.md) を使用するように VM を変換することができます。 このプロセスでは、OS ディスクと接続されたすべてのデータ ディスクの両方を変換します。

この記事では、Azure CLI を使用して VM を変換する方法を説明します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。 

## <a name="before-you-begin"></a>開始する前に
* [Managed Disks への移行に関する FAQ](../faq-for-disks.md#migrate-to-managed-disks) を確認します。

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* 変換前の VM で使用されていた元の VHD とストレージ アカウントは削除されません。 その後も料金が発生します。 これらのアーティファクトに対して課金されないように、変換が完了したことを確認したら、元の VHD BLOB は削除してください。 削除するためにこれらのアタッチされていないディスクを探す必要がある場合は、「[Find and delete unattached Azure managed and unmanaged disks (接続されていない Azure マネージド ディスクおよびアンマネージド ディスクを見つけて削除する)](find-unattached-disks.md)」を参照してください。

## <a name="convert-single-instance-vms"></a>単一インスタンスの VM を変換する
このセクションでは、単一インスタンスの Azure VM を非管理対象ディスクからマネージド ディスクに変換する方法について説明します。 (VM が可用性セットに含まれている場合は次のセクションを参照してください。)このプロセスを使用して、VM を非管理対象の Premium (SSD) ディスクから Premium マネージド ディスクに、または非管理対象の標準 (HDD) ディスクから標準マネージド ディスクに変換できます。

1. [az vm deallocate](/cli/azure/vm) を使用して VM の割り当てを解除します。 次の例では、`myResourceGroup` という名前のリソース グループに含まれる `myVM` という名前の VM の割り当てを解除します。

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. [az vm convert](/cli/azure/vm) を使用して VM をマネージド ディスクに変換します。 次のプロセスでは、OS ディスクとすべてのデータ ディスクを含む `myVM` という名前の VM を変換します。

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. マネージド ディスクに変換した後、[az vm start](/cli/azure/vm) で VM を起動します。 次の例では、`myResourceGroup` という名前のリソース グループ内にある `myVM` という名前の VM を起動します。

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>可用性セットの VM を変換する

マネージド ディスクに変換する VM が可用性セット内にある場合は、最初に可用性セットを管理対象の可用性セットに変換する必要があります。

可用性セットを変換する前に、可用性セット内のすべての VM の割り当てを解除する必要があります。 可用性セット自体を管理対象の可用性セットに変換したあと、すべての VM をマネージド ディスクに変換する計画を立てます。 その後、すべての VM を起動し、通常どおり操作を続行します。

1. [az vm availability-set list](/cli/azure/vm/availability-set) を使用して、可用性セット内のすべての VM を一覧表示します。 次の例では、`myResourceGroup` という名前のリソース グループの `myAvailabilitySet` という名前の可用性セットに含まれるすべての VM の一覧を取得します。

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. [az vm deallocate](/cli/azure/vm) を使用して、すべての VM の割り当てを解除します。 次の例では、`myResourceGroup` という名前のリソース グループに含まれる `myVM` という名前の VM の割り当てを解除します。

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. [az vm availability-set convert](/cli/azure/vm/availability-set) を使用して、可用性セットを変換します。 次の例では、`myResourceGroup` という名前のリソース グループの `myAvailabilitySet` という名前の可用性セットを変換します。

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. [az vm convert](/cli/azure/vm) を使用して、すべての VM をマネージド ディスクに変換します。 次のプロセスでは、OS ディスクとすべてのデータ ディスクを含む `myVM` という名前の VM を変換します。

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. マネージド ディスクに変換した後、[az vm start](/cli/azure/vm) ですべての VM を起動します。 次の例では、`myResourceGroup` という名前のリソース グループの `myVM` という名前の VM を起動します。

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-using-the-azure-portal"></a>Azure portal を使用して変換する

Azure portal を使用して、アンマネージド ディスクをマネージド ディスクに変換することもできます。

1. [Azure portal](https://portal.azure.com) にサインインします。
2. ポータルで VM の一覧から VM を選択します。
3. VM のブレードで、メニューから **[ディスク]** を選択します。
4. **[ディスク]** ブレードの上部で、 **[編集]** を選択します。
5. VM が可用性セット内にある場合は、可用性セットを先に変換する必要があることを示す警告が **[マネージド ディスクへの移行]** ブレードに表示されます。 警告には、可用性セットを変換するためにクリックできるリンクが含まれています。 可用性セットが変換された後、または VM が可用性セットに含まれていない場合は、 **[移行]** をクリックして、ディスクをマネージド ディスクに移行するプロセスを開始します。

VM が停止し、移行の完了後に再起動します。

## <a name="next-steps"></a>次のステップ

ストレージのオプションについて詳しくは、「[Azure Managed Disks の概要](../managed-disks-overview.md)」をご覧ください。
