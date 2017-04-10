---
title: "Azure の Linux VM を非管理対象ディスクから Managed Disks に変換する | Microsoft Docs"
description: "Azure CLI 2.0 を使って非管理対象ディスクから Azure Managed Disks に VM を変換する方法"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 53f5eefd9223fecefa184c612633d7a455fe15bf
ms.lasthandoff: 03/18/2017

---

# <a name="how-to-convert-a-linux-vm-from-unmanaged-disks-to-azure-managed-disks"></a>Linux VM を非管理対象ディスクから Azure Managed Disks に変換す方法

Azure にストレージ アカウントの非管理対象ディスクを使っている既存の Linux Vm があり、その VM で Managed Disks を利用できるようにしたい場合は、VM を変換できます。 このプロセスでは、OS ディスクと接続されたすべてのデータ ディスクの両方を変換します。 変換プロセスでは VM の再起動が必要なので、すでに設定されているメンテナンス期間中に VM の移行をスケジュールしてください。 移行プロセスを元に戻すことはできません。 運用環境で移行を実行する前に、テスト仮想マシンを移行することで移行プロセスをテストしてください。

> [!IMPORTANT]
> 変換中に、VM の割り当てを解除します。 VM は、変換後に起動されたときに、新しい IP アドレスを受け取ります。 固定 IP に依存関係がある場合は、予約済み IP を使ってください。

非管理対象ディスクが [Azure Storage Service Encryption (SSE)](../storage/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) を使って暗号化されたストレージ アカウントに現在存在する場合、またはかつて存在したことがある場合は、非管理対象ディスクを Azure Storage に変換することはできません。 次の手順では、暗号化されたストレージ アカウントにある、またはあった非管理対象ディスクを変換する方法について詳しく説明します。

- [az storage blob copy start](/cli/azure/storage/blob/copy#start) を使用して、Azure Storage Service Encryption が有効にされたことのないストレージ アカウントに仮想ハード ディスク (VHD) をコピーします。
- Managed Disks を使う VM を作成し、[az vm create](/cli/azure/vm#create) での作成時にその VHD ファイルを指定します。
- [az vm disk attach](/cli/azure/vm/disk#attach) を使って、コピーした VHD を Managed Disks で実行中の VM に接続します。

## <a name="convert-vm-to-azure-managed-disks"></a>VM を Azure Managed Disks に変換する
このセクションでは、既存の Azure VM を非管理対象ディスクから Managed Disks に変換する方法について説明します。 このプロセスを使って、Premium (SDD) の非管理対象ディスクから Premium Managed Disks に、または Standard (HDD) の非管理対象ディスクから Standard Managed Disks に変換できます。

> [!IMPORTANT]
> 以下の手順を実行した後、既定の vhds コンテナーにはブロック BLOB が 1 つ残ります。 ファイルの名前は "VMName.xxxxxxx.status" です。 この残っているステータス オブジェクトを削除しないでください。 この問題には今後の作業で対処されます。

1. [az vm deallocate](/cli/azure/vm#deallocate) で VM の割り当てを解除します。 次の例では、`myResourceGroup` という名前のリソース グループに含まれる `myVM` という名前の VM の割り当てを解除します。

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. [az vm convert](/cli/azure/vm#convert) で VM を Managed Disks に変換します。 次の例では、OS ディスクとすべてのデータ ディスクを含む `myVM` という名前の VM を変換します。

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. Managed Disks に変換した後、[az vm start](/cli/azure/vm#start) で VM を起動します。 次の例では、`myResourceGroup` という名前のリソース グループ内にある `myVM` という名前の VM を起動します。

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vm-in-an-availability-set-to-managed-disks"></a>可用性セット内の VM を Managed Disks に変換する

Managed Disks に変換する VM が可用性セット内にある場合は、最初に可用性セットを管理対象の可用性セットに変換する必要があります。

可用性セットを変換する前に、可用性セット内のすべての VM の割り当てを解除する必要があります。 可用性セット自体を管理対象の可用性セットに変換した後、Managed Disks へのすべての VM の変換を計画します。 その後、すべての VM を起動し、通常どおり操作を続行できます。

1. [az vm availability-set list](/cli/azure/vm/availability-set#list) で、可用性セット内のすべての VM の一覧を取得します。 次の例では、`myResourceGroup` という名前のリソース グループの `myAvailabilitySet` という名前の可用性セットに含まれるすべての VM の一覧を取得します。

    ```azurecli
    az vm availability-set show --resource-group myResourceGroup \
        --name myAvailabilitySet --query [virtualMachines[*].id] --output table
    ```

2. [az vm deallocate](/cli/azure/vm#deallocate) ですべての VM の割り当てを解除します。 次の例では、`myResourceGroup` という名前のリソース グループに含まれる `myVM` という名前の VM の割り当てを解除します。

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. [az vm availability-set convert](/cli/azure/vm/availability-set#convert) で、可用性セットを変換します。 次の例では、`myResourceGroup` という名前のリソース グループの `myAvailabilitySet` という名前の可用性セットを変換します。

    ```azurecli
    az vm availability-set convert --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. [az vm convert](/cli/azure/vm#convert) ですべての VM を Managed Disks に変換します。 次の例では、OS ディスクとすべてのデータ ディスクを含む `myVM` という名前の VM を変換します。

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. Managed Disks に変換した後、[az vm start](/cli/azure/vm#start) ですべての VM を起動します。 次の例では、`myResourceGroup` という名前のリソース グループ内にある `myVM` という名前の VM を起動します。

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="next-steps"></a>次のステップ
記憶域のオプションについて詳しくは、「[Azure Managed Disks の概要](../storage/storage-managed-disks-overview.md)」をご覧ください。

