---
title: CLI を使用して OS ディスク間をスワップする
description: CLI を使用して Azure 仮想マシンで使用されるオペレーティング システムのディスクを変更します。
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: adcbc5b0ba0056c79576e8d4c8ded2150b206513
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87371874"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-cli"></a>CLI を使用して Azure VM で使用される OS ディスクを変更する


既存の VM があり、そのディスクをバックアップ ディスクまたは別の OS ディスクに交換する場合、Azure CLI を使用して OS ディスクを交換できます。 VM を削除して再作成する必要はありません。 別のリソース グループ内のマネージド ディスクでも、まだ使用されていない場合に限り使用することができます。

この場合は、VM を停止するか、割り当てを解除する必要があります。その後は、マネージド ディスクのリソース ID を別のマネージド ディスクのリソース ID に置き換えることができます。 

VM のサイズとストレージの種類が、接続するディスクと互換性があることを確認します。 たとえば、使用するディスクが Premium Storage 内にある場合、VM は Premium Storage (DS シリーズのサイズなど) に対応している必要があります。

この記事では、Azure CLI バージョン 2.0.25 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。 


[az disk list](/cli/azure/disk) を使用してリソース グループ内のディスクの一覧を取得します。

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


ディスクを交換する前に、[az vm stop](/cli/azure/vm) を使用して VM を停止するか、割り当てを解除します。

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


`--osdisk` パラメーターに新しいディスクの完全なリソース ID を指定して、[az vm update](/cli/azure/vm#az-vm-update) を使用します。 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/swap/providers/Microsoft.Compute/disks/myDisk 
   ```
   
[az vm start](/cli/azure/vm) を使用して VM を再起動します。

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**次の手順**

ディスクのコピーを作成する方法については、「[スナップショットの作成](snapshot-copy-managed-disk.md)」を参照してください。
