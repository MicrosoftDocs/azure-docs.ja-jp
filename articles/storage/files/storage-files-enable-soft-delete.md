---
title: 論理的な削除を有効にする - Azure ファイル共有
description: データを復旧したり誤削除を回避したりできるように、Azure ファイル共有に対して論理的な削除を有効にする方法について説明します。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/01/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 77381700f4257006b50e56ab7ffc037ef99d297c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102218555"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>Azure ファイル共有で論理的な削除を有効にする

Azure Storage では、アプリケーションまたは他のストレージ アカウント ユーザーによってデータが誤って削除されたときに、データをより簡単に復旧できるように、ファイル共有での論理的な削除が提供されています。 論理的な削除の詳細については、[Azure ファイル共有の誤削除を回避する方法](storage-files-prevent-file-share-deletion.md)に関するページをご覧ください。

以下のセクションでは、既存のストレージ アカウントで Azure ファイル共有の論理的な削除を有効にして使用する方法について説明します。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

## <a name="getting-started"></a>作業の開始

1. [Azure Portal](https://portal.azure.com/) にサインインします。
1. ストレージ アカウントに移動し、 **[File service]** の下にある **[論理的な削除]** を選択します。
1. **[File share soft delete]\(ファイル共有の論理的な削除\)** で **[有効]** を選択します。
1. **[File share retention period in days]\(ファイル共有の保有期間の日数\)** を選択し、任意の数を入力します。
1. **[保存]** を選択し、データ保有設定を確認します。

:::image type="content" source="media/storage-how-to-recover-deleted-account/enable-soft-delete-files.png" alt-text="ストレージ アカウントの論理的な削除の設定ペインのスクリーンショット。ファイル共有セクション、有効トグル、保有期間の設定、および保存が強調表示されています。これにより、ストレージ アカウント内のすべてのファイル共有に対して論理的な削除が有効になります。":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

論理的な削除のコマンドレットは、バージョン 2.1.3 以降の [Azure CLI モジュール](/cli/azure/install-azure-cli)で使用できます。

## <a name="getting-started-with-cli"></a>CLI の概要

論理的な削除を有効にするには、ファイル クライアントのサービスのプロパティを更新する必要があります。 次の例では、ストレージ アカウント内のすべてのファイル共有に対して論理的な削除が有効になります。

```azurecli
az storage account file-service-properties update --enable-delete-retention true -n yourStorageaccount -g yourResourceGroup
```

次のコマンドを使用すると、論理的な削除が有効になっているかどうかを確認して、その保有ポリシーを表示できます。

```azurecli
az storage account file-service-properties show -n yourStorageaccount -g yourResourceGroup
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="prerequisite"></a>前提条件

論理的な削除のコマンドレットは、Az.Storage モジュールの 4.8.0 以降のバージョンで使用できます。 

## <a name="getting-started-with-powershell"></a>PowerShell の概要

論理的な削除を有効にするには、ファイル クライアントのサービスのプロパティを更新する必要があります。 次の例では、ストレージ アカウント内のすべてのファイル共有に対して論理的な削除が有効になります。

```azurepowershell-interactive
$rgName = "yourResourceGroupName"
$accountName = "yourStorageAccountName"

Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $true -ShareRetentionDays 7
```

次のコマンドを使用すると、論理的な削除が有効になっているかどうかを確認して、その保有ポリシーを表示できます。

```azurepowershell-interactive
Get-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName
```
---

## <a name="restore-soft-deleted-file-share"></a>論理的に削除されたファイル共有を復元する

# <a name="portal"></a>[ポータル](#tab/azure-portal)

論理的に削除されたファイル共有を復元するには、次のようにします。

1. ストレージ アカウントに移動し、 **[ファイル共有]** を選択します。
1. [ファイル共有] ブレードで、 **[Show deleted shares]\(削除された共有を表示\)** を有効にして、論理的に削除されているすべての共有を表示します。

    これにより、現在 **[削除済み]** の状態になっているすべての共有が表示されます。

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="[状態] 列 ([名前] 列の隣の列) が [削除済み] に設定されている場合、ファイル共有は論理的に削除された状態になっています。また、指定した保有期間が経過すると、完全に削除されます。":::

1. 共有を選択し、 **[削除の取り消し]** を選択すると、共有が復元されます。

    共有の状態が **[アクティブ]** に切り替わるので、共有が復元されたことを確認できます。

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="[状態] 列 ([名前] 列の隣の列) が [アクティブ] に設定されている場合、ファイル共有は復元されています。":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

論理的な削除のコマンドレットは、Azure CLI の 2.1.3 バージョンで使用できます。 論理的に削除されたファイル共有を復元するには、まず共有の `--deleted-version` 値を取得する必要があります。 この値を取得するには、次のコマンドを使用して、ストレージ アカウントの削除されたすべての共有を一覧表示します。

```azurecli
az storage share-rm list --storage-account yourStorageaccount --include-deleted
```

復元する共有を特定したら、次のコマンドでそれを使用して復元できます。

```azurecli
az storage share-rm restore -n deletedshare --deleted-version 01D64EB9886F00C4 -g yourResourceGroup --storage-account yourStorageaccount
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

論理的な削除のコマンドレットは、Az.Storage モジュールの 4.8.0 以降のバージョンで使用できます。 論理的に削除されたファイル共有を復元するには、まず共有の `-DeletedShareVersion` 値を取得する必要があります。 この値を取得するには、次のコマンドを使用して、ストレージ アカウントの削除されたすべての共有を一覧表示します。

```azurepowershell-interactive
Get-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -IncludeDeleted
```

復元する共有を特定したら、次のコマンドでそれを使用して復元できます。

```azurepowershell-interactive
Restore-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -DeletedShareVersion 01D5E2783BDCDA97
```
---

## <a name="disable-soft-delete"></a>論理的な削除の無効化

論理的な削除の使用を停止する場合、またはファイル共有を完全に削除する場合は、次の手順に従います。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. ストレージ アカウントに移動し、 **[設定]** の下にある **[論理的な削除]** を選択します。
1. **[ファイル共有]** の **[Soft delete for file shares]\(ファイル共有の論理的な削除\)** で **[無効]** を選択します。
1. **[保存]** を選択し、データ保有設定を確認します。

    :::image type="content" source="media/storage-how-to-recover-deleted-account/disable-soft-delete-files.png" alt-text="論理的な削除を無効にすると、ストレージ アカウント内のすべてのファイル共有を、必要なときにすぐ完全に削除することができます。":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

論理的な削除のコマンドレットは、Azure CLI の 2.1.3 バージョンで使用できます。 次のコマンドを使用して、ストレージ アカウントで論理的な削除を無効にすることができます。

```azurecli
az storage account file-service-properties update --enable-delete-retention false -n yourStorageaccount -g yourResourceGroup
```
# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

論理的な削除のコマンドレットは、Az.Storage モジュールの 4.8.0 以降のバージョンで使用できます。 次のコマンドを使用して、ストレージ アカウントで論理的な削除を無効にすることができます。

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---

## <a name="next-steps"></a>次のステップ

別の形式のデータ保護と復旧の詳細については、「[Azure Files の共有スナップショットの概要](storage-snapshots-files.md)」記事を参照してください。