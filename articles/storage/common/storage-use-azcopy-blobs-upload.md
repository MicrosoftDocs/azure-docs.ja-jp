---
title: AzCopy v10 を使用して Azure BLOB ストレージにファイルをアップロードする | Microsoft Docs
description: この記事には、Azure BLOB ストレージにファイルをアップロードするために役立つ AzCopy コマンドの例のコレクションが含まれています。
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/11/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: ec88a3c740ceda7ccf352f8f32f94e2cd52d0988
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97358760"
---
# <a name="upload-files-to-azure-blob-storage-by-using-azcopy-v10"></a>AzCopy v10 を使用して Azure BLOB ストレージにファイルをアップロードする

AzCopy v10 コマンド ライン ユーティリティを使用して、BLOB ストレージにファイルやディレクトリをアップロードできます。 

BLOB のダウンロード、BLOB ストレージとの同期、アカウント間での BLOB のコピーなどの他の種類のタスクの例を確認するには、この記事の「[次のステップ](#next-steps)」のセクションに示されているリンクを参照してください。

## <a name="get-started"></a>はじめに

AzCopy のダウンロード方法と、ストレージ サービスに認証資格情報を与える方法については、[AzCopy の作業開始](storage-use-azcopy-v10.md)に関するページをご覧ください。

> [!NOTE] 
> この記事の例では、Azure Active Directory (Azure AD) を使用して認証資格情報を指定していることを前提としています。
>
> SAS トークンを使用して BLOB データへのアクセスを承認する場合、各 AzCopy コマンドのリソース URL の先頭にそのトークンを追加できます。 (例: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`)。

## <a name="create-a-container"></a>コンテナーを作成する

[azcopy make](storage-ref-azcopy-make.md) コマンドを使用し、コンテナーを作成できます。

> [!TIP]
> この例では、パス引数を単一引用符 ('') で囲んでいます。 Windows コマンド シェル (cmd.exe) を除き、すべてのコマンド シェルで単一引用符を使用します。 Windows コマンド シェル (cmd.exe) を使用している場合は、単一引用符 ('') ではなく、二重引用符 ("") でパス引数を囲みます。

|    |     |
|--------|-----------|
| **構文** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **例** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **例** (階層型名前空間) | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

詳細なリファレンス ドキュメントについては、「[azcopy make](storage-ref-azcopy-make.md)」を参照してください。

## <a name="upload-a-file"></a>ファイルをアップロードする

[azcopy copy](storage-ref-azcopy-copy.md) コマンドを使用してファイルをアップロードします。

> [!TIP]
> この例では、パス引数を単一引用符 ('') で囲んでいます。 Windows コマンド シェル (cmd.exe) を除き、すべてのコマンド シェルで単一引用符を使用します。 Windows コマンド シェル (cmd.exe) を使用している場合は、単一引用符 ('') ではなく、二重引用符 ("") でパス引数を囲みます。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **例** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **例** (階層型名前空間) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

ファイル パスまたはファイル名の任意の場所で、ワイルドカード記号 (*) を使用してファイルをアップロードすることもできます。 例: `'C:\myDirectory\*.txt'`、または `C:\my*\*.txt`。

## <a name="upload-a-directory"></a>ディレクトリをアップロードする

[azcopy copy](storage-ref-azcopy-copy.md) コマンドを使用してディレクトリをアップロードします。 

この例では、ディレクトリ (とそのディレクトリ内のすべてのファイル) が BLOB コンテナーにコピーされます。 その結果、同じ名前でコンテナーにディレクトリが生成されます。

> [!TIP]
> この例では、パス引数を単一引用符 ('') で囲んでいます。 Windows コマンド シェル (cmd.exe) を除き、すべてのコマンド シェルで単一引用符を使用します。 Windows コマンド シェル (cmd.exe) を使用している場合は、単一引用符 ('') ではなく、二重引用符 ("") でパス引数を囲みます。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **例** (階層型名前空間) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

コンテナー内のディレクトリにコピーするには、コマンド文字列でそのディレクトリの名前を指定します。

|    |     |
|--------|-----------|
| **例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **例** (階層型名前空間) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

コンテナー内に存在しないディレクトリの名前を指定した場合は、AzCopy によってその名前で新しいディレクトリが作成されます。

## <a name="upload-directory-contents"></a>ディレクトリの内容をアップロードする

[azcopy copy](storage-ref-azcopy-copy.md) コマンドを使用してディレクトリの内容をアップロードします。 格納しているディレクトリ自体をコピーせずに内容をアップロードするには、ワイルドカード記号 (*) を使用します。

> [!TIP]
> この例では、パス引数を単一引用符 ('') で囲んでいます。 Windows コマンド シェル (cmd.exe) を除き、すべてのコマンド シェルで単一引用符を使用します。 Windows コマンド シェル (cmd.exe) を使用している場合は、単一引用符 ('') ではなく、二重引用符 ("") でパス引数を囲みます。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **例** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **例** (階層型名前空間) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |


すべてのサブディレクトリ内のファイルをアップロードするには、`--recursive` フラグを追加します。

## <a name="upload-specific-files"></a>特定のファイルをアップロードする

完全なファイル名、ワイルドカード文字 (*) を使用した部分的な名前、または日付と時刻を使用して、特定のファイルをアップロードすることができます。

> [!TIP]
> これらの例では、パス引数を単一引用符 ('') で囲んでいます。 Windows コマンド シェル (cmd.exe) を除き、すべてのコマンド シェルで単一引用符を使用します。 Windows コマンド シェル (cmd.exe) を使用している場合は、単一引用符 ('') ではなく、二重引用符 ("") でパス引数を囲みます。

### <a name="specify-multiple-complete-file-names"></a>複数の完全なファイル名を指定する

[azcopy copy](storage-ref-azcopy-copy.md) コマンドを `--include-path` オプションと共に使用します。 セミコロン (`;`) を使用して、個々のファイル名を区切ります。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **例** (階層型名前空間) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

この例では、AzCopy によって `C:\myDirectory\photos` ディレクトリと `C:\myDirectory\documents\myFile.txt` ファイルが転送されます。 `C:\myDirectory\photos` ディレクトリ内のすべてのファイルを転送するには、`--recursive` オプションを含めます。

`--exclude-path` オプションを使用してファイルを除外することもできます。 詳細については、「[azcopy copy](storage-ref-azcopy-copy.md)」リファレンス ドキュメントを参照してください。

### <a name="use-wildcard-characters"></a>ワイルドカード文字を使用する

[azcopy copy](storage-ref-azcopy-copy.md) コマンドを `--include-pattern` オプションと共に使用します。 ワイルドカード文字を含む名前の一部を指定します。 セミコロン (`;`) で名前を区切ります。 

|    |     |
|--------|-----------|
| **構文** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **例** (階層型名前空間) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

`--exclude-pattern` オプションを使用してファイルを除外することもできます。 詳細については、「[azcopy copy](storage-ref-azcopy-copy.md)」リファレンス ドキュメントを参照してください。

`--include-pattern` オプションと `--exclude-pattern` オプションは、パスではなくファイル名にのみ適用されます。  ディレクトリ ツリーに存在するテキスト ファイルをすべてコピーする場合は、`–recursive` オプションを使用してディレクトリ ツリー全体を取得し、次に `–include-pattern` を使用して `*.txt` を指定し、すべてのテキスト ファイルを取得します。

### <a name="upload-files-that-were-modified-before-or-after-a-date-and-time"></a>ある日時の前後に変更されたファイルをアップロードする 

[azcopy copy](storage-ref-azcopy-copy.md) コマンドを `--include-before` または `--include-after` オプションと共に使用します。 日付と時刻を ISO-8601 形式で指定します (例: `2020-08-19T15:04:00Z`)。 

次の例では、指定した日付以降に変更されたファイルをアップロードします。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **例** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **例** (階層型名前空間) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory'   --include-after '2020-08-19T15:04:00Z'` |

詳細なリファレンスについては、[azcopy copy](storage-ref-azcopy-copy.md) に関するリファレンス ドキュメントを参照してください。

## <a name="upload-with-index-tags"></a>インデックス タグを使用してアップロードする

ファイルをアップロードし、[BLOB インデックス タグ (プレビュー)](../blobs/storage-manage-find-blobs.md) をターゲット BLOB に追加することができます。  

Azure AD 承認を使用している場合は、セキュリティ プリンシパルに[ストレージ Blob データ所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)ロールが割り当てられているか、カスタム Azure ロールを使用して `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [Azure リソース プロバイダーの操作](../../role-based-access-control/resource-provider-operations.md#microsoftstorage)に対するアクセス許可が付与されている必要があります。 Shared Access Signature (SAS) トークンを使用している場合、そのトークンは、`t` SAS アクセス許可を使用して BLOB のタグへのアクセスを提供する必要があります。

タグを追加するには、URL でエンコードされたキーと値のペアと共に `--blob-tags` オプションを使用します。 たとえば、キー `my tag` と値 `my tag value` を追加するには、宛先パラメーターに `--blob-tags='my%20tag=my%20tag%20value'` を追加します。 

複数のインデックス タグを区切るには、アンパサンド (`&`) を使用します。  たとえば、キー `my second tag` と値 `my second tag value` を追加する場合、完全なオプション文字列は `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` になります。

`--blob-tags` オプションを使用する方法の例を次に示します。

> [!TIP]
> この例では、パス引数を単一引用符 ('') で囲んでいます。 Windows コマンド シェル (cmd.exe) を除き、すべてのコマンド シェルで単一引用符を使用します。 Windows コマンド シェル (cmd.exe) を使用している場合は、単一引用符 ('') ではなく、二重引用符 ("") でパス引数を囲みます。

|    |     |
|--------|-----------|
| **ファイルをアップロードする** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **ディレクトリをアップロードする** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`|
| **ディレクトリの内容をアップロードする** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |

> [!NOTE]
> ソースのディレクトリを指定すると、コピー先にコピーされたすべての BLOB のタグは、コマンドで指定したものと同じになります。

## <a name="upload-with-optional-flags"></a>オプションのフラグを使用してアップロードする

オプションのフラグを使用して、アップロード操作を調整できます。 以下にいくつか例を示します。

|シナリオ|フラグ|
|---|---|
|追加 BLOB またはページ BLOB としてファイルをアップロードします。|**--blob-type**=\[BlockBlob\|PageBlob\|AppendBlob\]|
|特定のアクセス層 (アーカイブ層など) にアップロードします。|**--block-blob-tier**=\[None\|Hot\|Cool\|Archive\]|

完全な一覧については、「[オプション](storage-ref-azcopy-copy.md#options)」を参照してください。

## <a name="next-steps"></a>次のステップ

他の例については、次の記事を参照してください。

- [例:ダウンロード](storage-use-azcopy-blobs-download.md)」をご覧ください
- [例:アカウント間のコピー](storage-use-azcopy-blobs-copy.md)
- [例:同期](storage-use-azcopy-blobs-synchronize.md)
- [例:Amazon S3 バケット](storage-use-azcopy-s3.md)
- [例:Azure Files](storage-use-azcopy-files.md)
- [チュートリアル:AzCopy を使用したオンプレミス データのクラウド ストレージへの移行](storage-use-azcopy-migrate-on-premises-data.md)
- [AzCopy の構成、最適化、トラブルシューティング](storage-use-azcopy-configure.md)