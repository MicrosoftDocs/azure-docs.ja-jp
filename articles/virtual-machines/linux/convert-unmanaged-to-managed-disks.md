---
title: Azure の Linux 仮想マシンを非管理対象ディスクからマネージド ディスクに変換する - Azure Managed Disks |Microsoft Docs
description: Resource Manager デプロイ モデルで Azure CLI を使用して Linux VM を非管理対象からマネージド ディスクに変換する方法
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 12/15/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 39a42891a73bd64731dd19aa22214a62d913d975
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2019
ms.locfileid: "55700827"
---
# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Linux 仮想マシンを非管理対象ディスクからマネージド ディスクに変換する

非管理対象ディスクを使用する既存の Linux 仮想マシン (VM) を所有している場合、[Azure Managed Disks](../linux/managed-disks-overview.md) を使用するように VM を変換することができます。 このプロセスでは、OS ディスクと接続されたすべてのデータ ディスクの両方を変換します。

この記事では、Azure CLI を使用して VM を変換する方法を説明します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。 

## <a name="before-you-begin"></a>開始する前に
* [Managed Disks への移行に関する FAQ](faq-for-disks.md#migrate-to-managed-disks) を確認します。

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]


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

## <a name="next-steps"></a>次の手順
ストレージのオプションについて詳しくは、「[Azure Managed Disks の概要](../windows/managed-disks-overview.md)」をご覧ください。
