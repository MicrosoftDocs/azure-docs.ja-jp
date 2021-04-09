---
title: Azure CLI を使用して VHD のスナップショットを作成する
description: バックアップまたは問題のトラブルシューティングに使うために、Azure で VHD のコピーを作成する方法について説明します。
author: roygara
manager: twooley
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/11/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: d041f864c6c8cd3ae9c522d79447d71c86f9ac04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98875606"
---
# <a name="create-a-snapshot-using-the-portal-or-azure-cli"></a>ポータルまたは Azure CLI を使用してスナップショットを作成する

バックアップ、または VM の問題を解決するために、OS ディスクまたはデータ ディスクのスナップショットを作成します。 スナップショットは、VHD の完全な読み取り専用コピーです。 

## <a name="use-azure-cli"></a>Azure CLI の使用 

次の例では、[Cloud Shell](https://shell.azure.com/bash) を使用するか、または Azure CLI をインストールする必要があります。

次の手順は、**az snapshot create** コマンドと **--source-disk** パラメターを使用してスナップショットを取得する方法を示しています。 次の例では、*myResourceGroup* リソース グループに *myVM* という名前の VM があるものとします。

[az vm show](/cli/azure/vm#az-vm-show) を使用して、ディスク ID を取得します。

```azurecli-interactive
osDiskId=$(az vm show \
   -g myResourceGroup \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

[az snapshot create](/cli/azure/snapshot#az-snapshot-create) を使用して、*osDisk-backup* という名前のスナップショットを取得します。

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> スナップショットをゾーン回復性のあるストレージに格納する場合は、[可用性ゾーン](../../availability-zones/az-overview.md)をサポートするリージョンにストレージを作成し、 **--sku Standard_ZRS** パラメーターを含める必要があります。

[az snapshot list](/cli/azure/snapshot#az-snapshot-list) を使用して、スナップショットの一覧を表示できます。

```azurecli-interactive
az snapshot list \
   -g myResourceGroup \
   - table
```

## <a name="use-azure-portal"></a>Azure Portal の使用 

1. [Azure portal](https://portal.azure.com) にサインインします。
2. 左上の **[リソースの作成]** をクリックし、 **[スナップショット]** を探します。 検索結果から **[スナップショット]** を選択します。
3. **[スナップショット]** ブレードで **[作成]** をクリックします。
4. スナップショットの **[名前]** を入力します。
5. 既存のリソース グループを選択するか、新しいリソース グループの名前を入力します。 
7. **[ソース ディスク]** で、スナップショットを作成するマネージド ディスクを選びます。
8. スナップショットの保存に使う **[アカウントの種類]** を選びます。 高パフォーマンスの SSD に保存する必要がある場合を除き、 **[Standard HDD]** を使用します。
9. **Create** をクリックしてください。


## <a name="next-steps"></a>次のステップ

 スナップショットからマネージド ディスクを作成し、その新しいマネージド ディスクを OS ディスクとして接続することで、スナップショットから仮想マシンを作成します。 詳細については、[スナップショットからの仮想マシンの作成に関する記事のスクリプトを参照してください](/previous-versions/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot?toc=%2fcli%2fmodule%2ftoc.json)。