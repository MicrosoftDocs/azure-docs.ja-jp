---
title: Azure HPC Cache ストレージ ターゲットを更新する
description: Azure HPC Cache ストレージ ターゲットを編集する方法
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/02/2020
ms.author: v-erkel
ms.openlocfilehash: f11e12c4f30977514e04b09c7e1c3012eb7888a7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092458"
---
# <a name="edit-storage-targets"></a>ストレージ ターゲットを編集する

キャッシュの**ストレージ ターゲット** ポータル ページから、あるいは Azure CLI を使用し、ストレージ ターゲットを削除または変更できます。

> [!TIP]
> [Azure HPC Cache の管理ビデオ](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)では、Azure portal でストレージ ターゲットを編集する方法を確認できます。

## <a name="remove-a-storage-target"></a>ストレージ ターゲットの削除

### <a name="portal"></a>[ポータル](#tab/azure-portal)

ストレージ ターゲットを削除するには、リストで選択し、 **[削除]** ボタンをクリックします。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

[az hpc-cache storage-target remove](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-remove) を使用し、キャッシュからストレージ ターゲットを削除します。

```azurecli
$ az hpc-cache storage-target remove --resource-group cache-rg --cache-name doc-cache0629 --name blob1

{- Finished ..
  "endTime": "2020-07-09T21:45:06.1631571+00:00",
  "name": "2f95eac1-aded-4860-b19c-3f089531a7ec",
  "startTime": "2020-07-09T21:43:38.5461495+00:00",
  "status": "Succeeded"
}
```

---

この操作により、この Azure HPC Cache システムとのストレージ ターゲットの関連付けが削除されますが、バックエンド ストレージ システムは変更されません。 たとえば、Azure BLOB ストレージ コンテナーを使用した場合、コンテナーとその内容はキャッシュから削除された後も存在します。 コンテナーを別の Azure HPC Cache に追加したり、このキャッシュに再度追加したり、Azure portal で削除したりすることができます。

キャッシュに格納されているファイルの変更は、ストレージ ターゲットが削除される前に、バックエンド ストレージ システムに書き込まれます。 キャッシュ内に多数の変更されたデータがある場合、このプロセスには 1 時間以上かかることがあります。

## <a name="update-storage-targets"></a>ストレージ ターゲットの更新

ストレージ ターゲットを編集して、そのプロパティの一部を変更できます。 ストレージの種類に応じて異なるプロパティを編集できます。

* Blob ストレージ ターゲットの場合は、名前空間のパスを変更できます。

* NFS ストレージ ターゲットの場合は、以下のプロパティを変更できます。

  * 名前空間のパス
  * 使用モデル
  * エクスポート
  * サブディレクトリのエクスポート

ストレージ ターゲットの名前、種類、またはバックエンド ストレージ システム (Blob コンテナーまたは NFS ホスト名/IP アドレス) を編集することはできません。 これらのプロパティを変更する必要がある場合は、ストレージ ターゲットを削除し、新しい値で置換を作成します。

Azure portal で、ストレージ ターゲット名をクリックし、その詳細ページを開くことで編集可能なフィールドを確認できます。 Azure CLI でストレージ ターゲットも変更できます。

![NFS ストレージ ターゲットの編集ページのスクリーンショット](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>NFS ストレージ ターゲットを更新する

NFS ストレージターゲットの場合は、いくつかのプロパティを更新できます。 (編集ページの例については、上記のスクリーンショットを参照してください)。

* **[使用モデル]** - 使用モデルは、キャッシュがデータを保持する方法に影響を及ぼします。 詳細については、「[使用モデルを選択する](hpc-cache-add-storage.md#choose-a-usage-model)」を参照してください。
* **[仮想名前空間のパス]** - クライアントがこのストレージ ターゲットをマウントするために使用するパス。 詳細については、「[集約された名前空間を計画する](hpc-cache-namespace.md)」を参照してください。
* **[NFS エクスポート パス]** - この名前空間のパスに使用するストレージ システムのエクスポート。
* **[サブディレクトリ パス]** - この名前空間のパスに関連付けるサブディレクトリ (エクスポートの下)。 サブディレクトリを指定する必要がない場合は、このフィールドを空白のままにします。

それぞれの名前空間のパスには、エクスポートとサブディレクトリの一意の組み合わせが必要です。 つまり、バックエンド ストレージ システム上のまったく同じディレクトリに対して、2 つの異なるクライアント向けのパスを作成することはできません。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

変更を行った後、 **[OK]** をクリックしてストレージ ターゲットを更新するか、 **[キャンセル]** をクリックして変更を破棄します。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

[az nfs-storage-target](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target) コマンドを使用し、ストレージ ターゲットの使用モデル、仮想名前空間パス、NFS エクスポートまたはサブディレクトリ値を変更します。

* 使用モデルを変更するには、``--nfs3-usage-model`` オプションを使用します。 例: ``--nfs3-usage-model WRITE_WORKLOAD_15``

* 名前空間パス、エクスポート、またはエクスポート サブディレクトリを変更するには、``--junction`` オプションを使用します。

  ``--junction`` パラメーターでは次の値が使用されます。

  * ``namespace-path`` - クライアント側の仮想ファイル パス
  * ``nfs-export`` - クライアント側パスに関連付けるストレージ システム エクスポート
  * ``target-path`` (省略可能) - 必要な場合、エクスポートのサブディレクトリ

  例: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

キャッシュ名、ストレージ ターゲット名、リソース グループは、すべての更新コマンドで必須です。

コマンド例: <!-- having problem testing this -->

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
    --name rivernfs0 --resource-group doc-rg0619 \
    --nfs3-usage-model READ_HEAVY_INFREQ
```

キャッシュが停止しているか、正常な状態ではない場合、キャッシュが正常になってから更新が適用されます。

---

## <a name="update-an-azure-blob-storage-target"></a>Azure Blob ストレージ ターゲットを更新する

Blob ストレージ ターゲットについては、仮想名前空間のパスを変更できます。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

Blob ストレージ ターゲットの詳細ページでは、仮想名前空間のパスを変更できます。

![Blob ストレージ ターゲットの編集ページのスクリーンショット](media/hpc-cache-edit-storage-blob.png)

完了したら、 **[OK]** をクリックしてストレージ ターゲットを更新するか、 **[キャンセル]** をクリックして変更を破棄します。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

[az hpc-cache blob-storage-target update](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update) を使用し、ターゲットの名前空間パスを更新します。

```azurecli
az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --storage-account "/subscriptions/<subscription_ID>/resourceGroups/erinazcli/providers/Microsoft.Storage/storageAccounts/rg"  \
    --container-name "container-name" --virtual-namespace-path "/new-path"
```

キャッシュが停止しているか、正常な状態ではない場合、キャッシュが正常になってから更新が適用されます。

---

## <a name="next-steps"></a>次のステップ

* これらのオプションの詳細については、「[ストレージ ターゲットを追加する](hpc-cache-add-storage.md)」を参照してください。
* 仮想パスの使用に関するその他のヒントについては、「[集約された名前空間を計画する](hpc-cache-namespace.md)」を参照してください。
