---
title: ブロック BLOB でポイントインタイム リストアを有効にして管理する (プレビュー)
titleSuffix: Azure Storage
description: ポイントインタイム リストア (プレビュー) を使用して、ブロック BLOB を以前の時点の状態に復元する方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/11/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 6948d4d786e918e5f3e32e6bdf2f7e23940f6815
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445442"
---
# <a name="enable-and-manage-point-in-time-restore-for-block-blobs-preview"></a>ブロック BLOB でポイントインタイム リストアを有効にして管理する (プレビュー)

ポイントインタイム リストア (プレビュー) を使用して、ブロック BLOB を以前の時点の状態に復元することができます。 この記事では、PowerShell を使用してストレージ アカウントでポイントインタイム リストアを有効にする方法について説明します。 また、PowerShell を使用して復元操作を実行する方法についても説明します。

このプレビューの詳細情報および登録方法については、「[ブロック BLOB のポイントインタイム リストア (プレビュー)](point-in-time-restore-overview.md)」を参照してください。

> [!CAUTION]
> ポイントインタイム リストアでは、ブロック BLOB に対する復元操作のみがサポートされます。 コンテナーに対する操作は復元できません。 ポイントインタイム リストアのプレビュー中に [Delete Container](/rest/api/storageservices/delete-container) 操作を呼び出してストレージ アカウントからコンテナーを削除した場合、そのコンテナーは復元操作を使って復元できません。 プレビュー中は、コンテナーを削除するのではなく、復元する可能性がある場合は個々の BLOB を削除してください。

> [!IMPORTANT]
> ポイントインタイム リストアのプレビューは、非運用環境での使用のみを意図しています。 運用環境のサービス レベル契約(SLA) は現在使用できません。

## <a name="install-the-preview-module"></a>プレビュー モジュールをインストールする

PowerShell を使用して Azure のポイントインタイム リストアを構成するには、まず、Az.Storage プレビュー モジュールのバージョン 1.14.1-preview 以降をインストールします。 最新のプレビュー バージョンを使用することをお勧めしますが、ポイントインタイム リストアはバージョン 1.14.1-preview 以降でサポートされています。 Az. Storage モジュールの他のバージョンをすべて削除します。

次に示すのは、Az.Storage の [2.0.1-preview](https://www.powershellgallery.com/packages/Az.Storage/2.0.1-preview) モジュールをインストールするコマンドです。

```powershell
Install-Module -Name Az.Storage -RequiredVersion 2.0.1-preview -AllowPrerelease
```

上記のコマンドでは、バージョン 2.2.4.1 以上の PowerShellGet をインストールする必要があります。 現在読み込まれているバージョンを確認するには、次を実行します。
```powershell
Get-Module PowerShellGet
```
Azure PowerShell のインストールの詳細については、[PowerShellGet を使用した Azure PowerShell のインストール](/powershell/azure/install-az-ps)に関するページを参照してください。

## <a name="enable-and-configure-point-in-time-restore"></a>ポイントインタイム リストアを有効にして構成する

ポイントインタイム リストアを有効にして構成する前に、ストレージ アカウントに対してその前提条件 (論理的な削除、変更フィード、および BLOB のバージョン管理) を有効にします。 これらの各機能を有効にする方法の詳細については、次の記事を参照してください。

- [BLOB の論理的な削除を有効にする](soft-delete-enable.md)
- [変更フィードを有効または無効にする](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [BLOB のバージョン管理を有効にして管理する](versioning-enable.md)

PowerShell で Azure のポイントインタイム リストアを構成するには、Enable-AzStorageBlobRestorePolicy コマンドを呼び出します。 次の例では、論理的な削除を有効にし、論理的な削除の保持期間を設定し、変更フィードを有効にした後、ポイントインタイム リストアを有効にします。 この例を実行する前に、Azure portal または Azure Resource Manager テンプレートを使用して、BLOB のバージョン管理も有効にします。

例を実行するときは、必ず山かっこ内の値を実際の値に置き換えてください。

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable soft delete with a retention of 6 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 6

# Enable change feed.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true

# Enable point-in-time restore with a retention period of 5 days.
# The retention period for point-in-time restore must be at least one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 5

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

## <a name="perform-a-restore-operation"></a>復元操作を実行する

復元操作を開始するには、UTC **DateTime** 値として復元ポイントを指定して、**Restore-AzStorageBlobRange** コマンドを呼び出します。 復元する辞書式範囲を指定することも、範囲を省略して、ストレージ アカウントにあるすべてのコンテナー内のすべての BLOB を復元することもできます。 復元操作ごとに最大 10 個の辞書式範囲がサポートされます。 ページ BLOB と追加 BLOB は復元に含まれません。 復元操作が完了するまでに数分かかる場合があります。

復元する BLOB の範囲を指定するときは、次の規則に注意してください。

- 開始範囲と終了範囲に指定するコンテナー パターンには、3 文字以上を含める必要があります。 コンテナー名と BLOB 名を区切るために使用されるスラッシュ (/) は、この最小数にはカウントされません。
- 1 回の復元操作につき最大 10 個の範囲を指定できます。
- ワイルドカード文字はサポートされていません。 それらは、標準文字として扱われます。
- 復元操作に渡される範囲に明示的に指定することで、`$root` および `$web` コンテナー内の BLOB を復元できます。 `$root` および `$web` コンテナーは、明示的に指定されている場合にのみ復元されます。 他のシステム コンテナーは復元できません。

> [!IMPORTANT]
> 復元操作を実行すると、Azure Storage では操作中、復元される範囲内の BLOB に対するデータ操作がブロックされます。 読み取り、書き込み、および削除の各操作がプライマリ ロケーションでブロックされます。 このため、Azure portal でのコンテナーの一覧表示などの操作は、復元操作の実行中に予期したとおりに実行されない場合があります。
>
> ストレージ アカウントが geo レプリケートされている場合は、復元操作中にセカンダリ ロケーションからの読み取り操作を続行できます。

### <a name="restore-all-containers-in-the-account"></a>アカウント内のすべてのコンテナーを復元する

ストレージ アカウント内のすべてのコンテナーと BLOB を復元するには、`-BlobRestoreRange` パラメーターを省略して、**Restore-AzStorageBlobRange** コマンドを呼び出します。 次の例は、ストレージ アカウント内のコンテナーを、現時点から 12 時間前の状態に復元します。

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)
```

### <a name="restore-a-single-range-of-block-blobs"></a>1 つの範囲のブロック BLOB を復元する

1 つの範囲の BLOB を復元するには、**Restore-AzStorageBlobRange** コマンドを呼び出し、`-BlobRestoreRange` パラメーターに対してコンテナーの辞書式範囲と BLOB 名を指定します。 範囲の開始は含まれ、範囲の終了は含まれません。

たとえば、*sample-container* という名前の 1 つのコンテナー内の BLOB を復元するには、*sample-container* で始まり、*sample-container1* で終わる範囲を指定できます。 開始と終了の範囲に指定されたコンテナーが存在している必要はありません。 範囲の終了は除外されるため、ストレージ アカウントに *sample-container1* という名前のコンテナーが含まれていたとしても、*sample-container* という名前のコンテナーだけが復元されます。

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container -EndRange sample-container1
```

復元するコンテナー内の BLOB のサブセットを指定するには、スラッシュ (/) を使用して、コンテナー名を BLOB パターンから分離します。 たとえば、次の範囲では、名前が *d* から *f* までの文字で始まる 1 つのコンテナー内の BLOB が選択されます。

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container/d -EndRange sample-container/g
```

次に、**Restore-AzStorageBlobRange** コマンドに範囲を指定します。 `-TimeToRestore` パラメーターに UTC **DateTime** 値を指定して、復元ポイントを指定します。 次の例は、指定された範囲の BLOB を、現時点から 3 日前の状態に復元します。

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

### <a name="restore-multiple-ranges-of-block-blobs"></a>複数の範囲のブロック BLOB を復元する

複数の範囲のブロック BLOB を復元するには、`-BlobRestoreRange` パラメーターに範囲の配列を指定します。 復元操作ごとに最大 10 個の範囲がサポートされます。 次の例では、*container1* と *container4* の完全な内容を復元する 2 つの範囲を指定しています。

```powershell
# Specify a range that includes the complete contents of container1.
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 -EndRange container2
# Specify a range that includes the complete contents of container4.
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 -EndRange container5

Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-30) `
    -BlobRestoreRange @($range1, $range2)
```

### <a name="restore-block-blobs-asynchronously"></a>ブロック BLOB を非同期に復元する

復元操作を非同期に実行するには、`-AsJob` パラメーターを **AzStorageBlobRange** への呼び出しに追加し、呼び出しの結果を変数に格納します。 **Restore-AzStorageBlobRange** コマンドは、**AzureLongRunningJob** 型のオブジェクトを返します。 このオブジェクトの **State** プロパティをチェックして、復元操作が完了したかどうかを判断できます。 **State** プロパティの値は、**Running** または **Completed** である可能性があります。

次の例は、復元操作を非同期に呼び出す方法を示しています。

```powershell
$job = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-5) `
    -AsJob

# Check the state of the job.
$job.State
```

実行後に復元操作が完了するまで待機するには、次の例に示すように、[Wait-Job](/powershell/module/microsoft.powershell.core/wait-job) コマンドを呼び出します。

```powershell
$job | Wait-Job
```

## <a name="known-issues"></a>既知の問題
- 追加 BLOB が存在する復元のサブセットの場合、復元は失敗します。 ここでは、アカウントに追加 BLOB が存在する場合は、復元を実行しないようにしてください。

## <a name="next-steps"></a>次のステップ

- [ブロック BLOB のポイントインタイム リストア (プレビュー)](point-in-time-restore-overview.md)
- [論理的な削除](soft-delete-overview.md)
- [変更フィード (プレビュー)](storage-blob-change-feed.md)
- [BLOB のバージョン管理 (プレビュー)](versioning-overview.md)
