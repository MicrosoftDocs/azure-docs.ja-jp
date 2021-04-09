---
title: ブロック BLOB データに対してポイントインタイム リストアを実行する
titleSuffix: Azure Storage
description: ポイントインタイム リストアを使用して、ブロック BLOB セットを特定の時点で以前の状態に復元する方法について学習します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/29/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: b62e341d35a4ff7fd5a7ddd6d9f19b138aaf0aa9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99071649"
---
# <a name="perform-a-point-in-time-restore-on-block-blob-data"></a>ブロック BLOB データに対してポイントインタイム リストアを実行する

ポイントインタイム リストアを使用して、ブロック BLOB の 1 つまたは複数のセットを以前の状態に復元することができます。 この記事では、ストレージ アカウントでポイントインタイム リストアを有効にする方法と、復元操作を実行する方法について説明します。

ポイントインタイム リストアの詳細については、「[ブロック BLOB のポイントインタイム リストア](point-in-time-restore-overview.md)」を参照してください。

> [!CAUTION]
> ポイントインタイム リストアでは、ブロック BLOB に対する復元操作のみがサポートされます。 コンテナーに対する操作は復元できません。 [Delete Container](/rest/api/storageservices/delete-container) 操作を呼び出してストレージ アカウントからコンテナーを削除した場合、そのコンテナーは復元操作を使って復元できません。 後で復元が必要になる可能性がある場合は、コンテナー全体を削除するのではなく、個々の BLOB を削除してください。 また、誤って削除されるのを防ぐために、コンテナーと BLOB の論理的な削除を有効にすることをお勧めします。 詳細については、「[コンテナーの論理的な削除 (プレビュー)](soft-delete-container-overview.md)」および「[BLOB の論理的な削除](soft-delete-blob-overview.md)」を参照してください。

## <a name="enable-and-configure-point-in-time-restore"></a>ポイントインタイム リストアを有効にして構成する

ポイントインタイム リストアを有効にして構成する前に、ストレージ アカウントに対してその前提条件 (論理的な削除、変更フィード、および BLOB のバージョン管理) を有効にします。 これらの各機能を有効にする方法の詳細については、次の記事を参照してください。

- [BLOB の論理的な削除を有効にする](./soft-delete-blob-enable.md)
- [変更フィードを有効または無効にする](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [BLOB のバージョン管理を有効にして管理する](versioning-enable.md)

> [!IMPORTANT]
> 論理的な削除、変更フィード、および BLOB のバージョン管理を有効にすると、追加料金が発生する可能性があります。 詳細については、「[BLOB の論理的な削除](soft-delete-blob-overview.md)」、「[Azure Blob Storage の変更フィードのサポート](storage-blob-change-feed.md)」、および「[BLOB のバージョン管理](versioning-overview.md)」を参照してください。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure portal を使用してポイントインタイム リストアを構成するには、次の手順に従います。

1. Azure Portal のストレージ アカウントに移動します。
1. **[設定]** で、 **[データ保護]** を選択します。
1. **[ポイントインタイム リストアを有効にする]** をオンにします。 このオプションを選択すると、BLOB の論理的な削除、バージョン管理、および変更フィードも有効になります。
1. ポイントインタイム リストアの最大復元ポイントを日単位で設定します。 この数は、BLOB の論理的な削除に指定された保有期間よりも 1 日以上短くする必要があります。
1. 変更を保存します。

次の画像は、復元ポイントが 7 日前で、BLOB の論理的な削除の保有期間が 14 日間のポイントインタイム リストア用に構成されたストレージ アカウントを示しています。

:::image type="content" source="media/point-in-time-restore-manage/configure-point-in-time-restore-portal.png" alt-text="Azure portal でポイントインタイム リストアを構成する方法を示すスクリーンショット":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用してポイントインタイム リストアを構成するには、まず、[Az.Storage](https://www.powershellgallery.com/packages/Az.Storage) モジュールのバージョン 2.6.0 以降をインストールします。 その後、[Enable-AzStorageBlobRestorePolicy](/powershell/module/az.storage/enable-azstorageblobrestorepolicy) コマンドを呼び出して、ストレージ アカウントのポイントインタイム リストアを有効にします。

次の例では、論理的な削除を有効にし、論理的な削除の保持期間を設定し、変更フィードとバージョン管理を有効にした後、ポイントインタイム リストアを有効にします。 例を実行するときは、必ず山かっこ内の値を実際の値に置き換えてください。

```powershell
# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable blob soft delete with a retention of 14 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 14

# Enable change feed and versioning.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable point-in-time restore with a retention period of 7 days.
# The retention period for point-in-time restore must be at least
# one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 7

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用してポイントインタイム リストアを構成するには、まず、Azure CLI バージョン 2.2.0 以降をインストールします。 次に、[az storage account blob-service-properties update](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_update) コマンドを呼び出して、ポイントインタイム リストアと、ストレージ アカウントのその他の必須なデータ保護設定を有効にします。

次の例では、論理的な削除を有効にして論理的な削除の保持期間を 14 日に設定し、変更フィードとバージョン管理を有効にし、復元期間が 7 日に設定されているポイントインタイム リストアを有効にします。 例を実行するときは、必ず山かっこ内の値を実際の値に置き換えてください。

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --enable-delete-retention true \
    --delete-retention-days 14 \
    --enable-versioning true \
    --enable-change-feed true \
    --enable-restore-policy true \
    --restore-days 7
```

---

## <a name="choose-a-restore-point"></a>復元ポイントを選択する

復元ポイントは、データが復元される日時です。 Azure Storage では常に、UTC の日付または時刻値が復元ポイントとして使用されます。 ただし、Azure portal を使用すると、復元ポイントをローカル時刻で指定し、その日付または時刻値を UTC の日付または時刻値に変換して復元操作を実行できます。

PowerShell または Azure CLI で復元操作を実行する場合は、復元ポイントを UTC の日付または時刻値として指定する必要があります。 復元ポイントに UTC 時刻値ではなくローカル時刻値を指定したとしても、場合によっては復元操作が期待どおりに動作することがあります。 たとえば、UTC から 5 時間を引いた時刻がローカル時刻の場合、現地時刻の値を指定すると、指定した値より 5 時間早い復元ポイントが生成されます。 その 5 時間の間に復元対象の範囲内のデータに変更が加えられていない場合は、指定した時間値にかかわらず、復元操作で同じ結果が得られます。 予期しない結果を避けるために、復元ポイントには UTC 時刻を指定することをお勧めします。

## <a name="perform-a-restore-operation"></a>復元操作を実行する

ストレージ アカウント内のすべてのコンテナーを復元することも、1 つまたは複数のコンテナー内の BLOB の範囲を復元することもできます。 BLOB の範囲は、辞書式 (つまり、辞書の順) で定義されます。 復元操作ごとに最大 10 個の辞書式範囲がサポートされます。 範囲の開始は含まれ、範囲の終了は含まれません。

開始範囲と終了範囲に指定するコンテナー パターンには、3 文字以上を含める必要があります。 コンテナー名と BLOB 名を区切るために使用されるスラッシュ (/) は、この最小数にはカウントされません。

ワイルドカード文字は、辞書式の範囲ではサポートされていません。 ワイルドカード文字はすべて、標準文字として扱われます。

復元操作に渡される範囲に明示的に指定することで、`$root` および `$web` コンテナー内の BLOB を復元できます。 `$root` および `$web` コンテナーは、明示的に指定されている場合にのみ復元されます。 他のシステム コンテナーは復元できません。

ブロック BLOB のみが復元されます。 ページ BLOB と追加 BLOB は復元操作に含まれません。 追加 BLOB に関する制限の詳細については、「[ブロック BLOB のポイントインタイム リストア](point-in-time-restore-overview.md)」を参照してください。

> [!IMPORTANT]
> 復元操作を実行すると、Azure Storage では操作中、復元される範囲内の BLOB に対するデータ操作がブロックされます。 読み取り、書き込み、および削除の各操作がプライマリ ロケーションでブロックされます。 このため、Azure portal でのコンテナーの一覧表示などの操作は、復元操作の実行中に予期したとおりに実行されない場合があります。
>
> ストレージ アカウントが geo レプリケートされている場合は、復元操作中にセカンダリ ロケーションからの読み取り操作を続行できます。
>
> 一連のデータの復元にかかる時間は、復元期間中に行われた書き込み操作と削除操作の数に基づきます。 たとえば、100 万オブジェクトを持つアカウントで毎日 3,000 オブジェクトが追加され、毎日 1,000 オブジェクトが削除される場合、過去 30 日間のポイントまで復元するのに約 2 時間必要になります。 1 保有期間と過去 90 日間の復元は、この変更率のアカウントには推奨されません。

### <a name="restore-all-containers-in-the-account"></a>アカウント内のすべてのコンテナーを復元する

ストレージ アカウント内のすべてのコンテナーを復元して、特定の時点でそれらを以前の状態に戻すことができます。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure portal を使用してストレージ アカウント内のすべてのコンテナーと BLOB を復元するには、これらの手順に従います。

1. ストレージ アカウントのコンテナーの一覧に移動します。
1. ツールバーで、 **[コンテナーを復元する]** 、 **[すべてを復元する]** の順に選択します。
1. **[すべてのコンテナーを復元する]** ペインで、日付と時刻を指定して復元ポイントを設定します。
1. チェックボックスをオンにして続行することを確認します。
1. **[復元]** を選択して復元操作を開始します。

    :::image type="content" source="media/point-in-time-restore-manage/restore-all-containers-portal.png" alt-text="指定された復元ポイントにすべてのコンテナーを復元する方法を示すスクリーンショット":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用してストレージ アカウント内のすべてのコンテナーと BLOB を復元するには、**Restore-AzStorageBlobRange** コマンドを呼び出して、復元ポイントを UTC の日付または時刻値で指定します。 既定では、**Restore-AzStorageBlobRange** コマンドは非同期的に実行され、**PSBlobRestoreStatus** 型のオブジェクトが返されます。これを使用して、復元操作の状態を確認できます。

次の例では、ストレージ アカウント内のコンテナーを、現時点から 12 時間前の状態に非同期的に復元し、復元操作の一部のプロパティを確認します。

```powershell
# Specify -TimeToRestore as a UTC value
$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).ToUniversalTime().AddHours(-12)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the restore point in UTC time.
$restoreOperation.Parameters.TimeToRestore
```

復元操作を同期的に実行するには、コマンドに **-WaitForComplete** パラメーターを含めます。 **-WaitForComplete** パラメーターが存在する場合、PowerShell により、操作の復元 ID を含むメッセージが示され、復元操作が完了するまで実行時にブロックされます。 復元操作に必要な時間の長さは、復元するデータの量によって異なり、大規模な復元操作が完了するまでに最大 1 時間かかる場合があることに注意してください。

```powershell
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12) -WaitForComplete
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用してストレージ アカウント内のすべてのコンテナーと BLOB を復元するには、[az storage blob restore](/cli/azure/storage/blob#az_storage_blob_restore) コマンドを呼び出して、復元ポイントを UTC の日付または時刻値で指定します。

次の例では、ストレージ アカウント内のすべてのコンテナーを、指定した日付と時刻の 12 時間前の状態に非同期的に復元します。 復元操作の状態を確認するには、次のように [az storage account show](/cli/azure/storage/account#az_storage_account_show) を呼び出します。

```azurecli
az storage blob restore \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --time-to-restore 2021-01-14T06:31:22Z \
    --no-wait
```

復元操作のプロパティを確認するには、[az storage account show](/cli/azure/storage/account#az_storage_account_show) を呼び出し、**blobRestoreStatus** プロパティを展開します。 次の例は、**status** プロパティを確認する方法を示しています。

```azurecli
az storage account show \
    --name <storage-account> \
    --resource-group <resource_group> \ 
    --expand blobRestoreStatus \
    --query blobRestoreStatus.status \
    --output tsv
```

**az storage blob restore** コマンドを同期的に実行し、復元操作が完了するまで実行時にブロックするには、`--no-wait` パラメーターを省略します。

---

### <a name="restore-ranges-of-block-blobs"></a>ブロック BLOB の範囲を復元する

単一のコンテナー内または複数のコンテナー全体の BLOB の 1 つまたは複数の辞書式範囲を復元して、特定の時点でそれらの BLOB を以前の状態に戻すことができます。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure portal を使用して 1 つまたは複数のコンテナー内の BLOB の範囲を復元するには、これらの手順に従います。

1. ストレージ アカウントのコンテナーの一覧に移動します。
1. 復元するコンテナーを選択します。
1. ツールバーで、 **[コンテナーを復元する]** 、 **[選択範囲を復元する]** の順に選択します。
1. **[選択したコンテナーを復元する]** ペインで、日付と時刻を指定して復元ポイントを設定します。
1. 復元する範囲を指定します。 コンテナー名と BLOB プレフィックスを区切るには、スラッシュ (/) を使用します。
1. 既定では、 **[選択したコンテナーを復元する]** ペインにより、コンテナー内のすべての BLOB を含む範囲が指定されます。 コンテナー全体を復元しない場合は、この範囲を削除します。 次の画像に既定の範囲が示されています。

    :::image type="content" source="media/point-in-time-restore-manage/delete-default-blob-range.png" alt-text="カスタム範囲を指定する前に削除する既定の BLOB 範囲を示すスクリーンショット":::

1. チェックボックスをオンにして続行することを確認します。
1. **[復元]** を選択して復元操作を開始します。

次の画像は、一連の範囲に対する復元操作を示しています。

:::image type="content" source="media/point-in-time-restore-manage/restore-multiple-container-ranges-portal.png" alt-text="1 つまたは複数のコンテナー内の BLOB の範囲を復元する方法を示すスクリーンショット":::

画像に示されている復元操作では、次の操作を実行します。

- *container1* の完全な内容を復元します。
- *container2* の *blob1* から *blob5* までの辞書式範囲の BLOB を復元します。 この範囲の場合、*blob1.txt*、*blob11*、*blob100*、*blob2* などの名前を持つ BLOB が復元されます。 範囲の終了は含まれないため、名前が *blob4* で始まる BLOB は復元されますが、名前が *blob5* で始まる BLOB は復元されません。
- *container3* と *container4* 内のすべての BLOB を復元します。 範囲の最後は含まれないため、この範囲の場合、*container5* は復元されません。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

単一の範囲の BLOB を復元するには、**Restore-AzStorageBlobRange** コマンドを呼び出し、`-BlobRestoreRange` パラメーターに対してコンテナーの辞書式範囲と BLOB 名を指定します。 たとえば、*container1* という名前の単一のコンテナー内の BLOB を復元する場合は、*container1* で始まり、*container2* で終わる範囲を指定できます。 開始と終了の範囲に指定されたコンテナーが存在している必要はありません。 範囲の終了は含まれないため、ストレージ アカウントに *container2* という名前のコンテナーが含まれていたとしても、*container1* という名前のコンテナーのみが復元されます。

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
```

復元するコンテナー内の BLOB のサブセットを指定するには、スラッシュ (/) を使用して、コンテナー名と BLOB プレフィックス パターンを区切ります。 たとえば、次の範囲では、名前が *d* から *f* までの文字で始まる 1 つのコンテナー内の BLOB が選択されます。

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1/d `
    -EndRange container1/g
```

次に、**Restore-AzStorageBlobRange** コマンドに範囲を指定します。 `-TimeToRestore` パラメーターに UTC **DateTime** 値を指定して、復元ポイントを指定します。 次の例は、指定された範囲の BLOB を、現時点から 3 日前の状態に復元します。

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

既定では、**Restore-AzStorageBlobRange** コマンドは非同期的に実行されます。 復元操作を非同期的に開始すると、PowerShell に操作のプロパティのテーブルがすぐに表示されます。  

```powershell
Status     RestoreId                            FailureReason Parameters.TimeToRestore     Parameters.BlobRanges
------     ---------                            ------------- ------------------------     ---------------------
InProgress 459c2305-d14a-4394-b02c-48300b368c63               2020-09-15T23:23:07.1490859Z ["container1/d" -> "container1/g"]
```

複数の範囲のブロック BLOB を復元するには、`-BlobRestoreRange` パラメーターに範囲の配列を指定します。 次の例では、*container1* と *container4* の完全な内容を、24 時間前の状態に復元する 2 つの範囲を指定し、その結果を変数に保存します。

```powershell
# Specify a range that includes the complete contents of container1.
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
# Specify a range that includes the complete contents of container4.
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 `
    -EndRange container5

$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-24) `
    -BlobRestoreRange @($range1, $range2)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the blob ranges specified for the operation.
$restoreOperation.Parameters.BlobRanges
```

復元操作を同期的に実行し、完了するまで実行時にブロックするには、コマンドに **-WaitForComplete** パラメーターを含めます。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

BLOB の範囲を復元するには、[az storage blob restore](/cli/azure/storage/blob#az_storage_blob_restore) コマンドを呼び出し、`--blob-range` パラメーターに対してコンテナーの辞書式範囲と BLOB 名を指定します。 複数の範囲を指定するには、個別の範囲ごとに `--blob-range` パラメーターを指定します。

たとえば、*container1* という名前の単一のコンテナー内の BLOB を復元する場合は、*container1* で始まり、*container2* で終わる範囲を指定できます。 開始と終了の範囲に指定されたコンテナーが存在している必要はありません。 範囲の終了は含まれないため、ストレージ アカウントに *container2* という名前のコンテナーが含まれていたとしても、*container1* という名前のコンテナーのみが復元されます。

復元するコンテナー内の BLOB のサブセットを指定するには、スラッシュ (/) を使用して、コンテナー名と BLOB プレフィックス パターンを区切ります。 次の例では、名前が `d` から `f` までの文字で始まるコンテナー内の BLOB の範囲を非同期的に復元します。

```azurecli
az storage blob restore \
    --account-name <storage-account> \
    --time-to-restore 2021-01-14T06:31:22Z \
    --blob-range container1 container2
    --blob-range container3/d container3/g
    --no-wait
```

**az storage blob restore** コマンドを同期的に実行し、復元操作が完了するまで実行時にブロックするには、`--no-wait` パラメーターを省略します。

---

## <a name="next-steps"></a>次の手順

- [ブロック BLOB のポイントインタイム リストア](point-in-time-restore-overview.md)
- [論理的な削除](./soft-delete-blob-overview.md)
- [変更フィード](storage-blob-change-feed.md)
- [BLOB のバージョン管理](versioning-overview.md)