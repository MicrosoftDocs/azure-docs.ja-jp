---
title: AzCopy v10 を使用して Azure Blob Storage をコピー先またはコピー元としてデータを転送する | Microsoft Docs
description: この記事には、コンテナーの作成、ファイルのコピー、ローカル ファイル システムとコンテナー間のディレクトリの同期に役立つ一連の AzCopy サンプル コマンドが含まれています。
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: f4bf3974cd561626c280dc65aa5fc78d0c9a159b
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056501"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>AzCopy と Blob Storage でデータを転送する

AzCopy は、ストレージ アカウント間のデータ コピーに利用できるコマンドライン ユーティリティです。 この記事には、Blob Storage で使用するサンプル コマンドが含まれています。

> [!TIP]
> この記事の例では、単一引用符 ('') でパス引数を囲みます。 Windows コマンド シェル (cmd.exe) を除き、すべてのコマンド シェルで単一引用符を使用します。 Windows コマンド シェル (cmd.exe) を使用している場合は、単一引用符 ('') ではなく、二重引用符 ("") でパス引数を囲みます。

## <a name="get-started"></a>はじめに

AzCopy のダウンロード方法と、ストレージ サービスに認証資格情報を与える方法については、[AzCopy の作業開始](storage-use-azcopy-v10.md)に関するページをご覧ください。

> [!NOTE]
> この記事の例では、`AzCopy login` コマンドを使用して ID を認証したと想定しています。 AzCopy では次に、Blob Storage のデータへのアクセスを承認するために、Azure AD アカウントが使用されます。
>
> SAS トークンを使用して BLOB データへのアクセスを承認する場合、各 AzCopy コマンドのリソース URL の先頭にそのトークンを追加できます。
>
> (例: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`)。

## <a name="create-a-container"></a>コンテナーを作成する

[azcopy make](storage-ref-azcopy-make.md) コマンドを使用し、コンテナーを作成できます。 このセクションの例では、`mycontainer` という名前のコンテナーが作成されます。

|    |     |
|--------|-----------|
| **構文** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **例** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **例** (階層型名前空間) | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

詳細なリファレンス ドキュメントについては、「[azcopy make](storage-ref-azcopy-make.md)」を参照してください。

## <a name="upload-files"></a>ファイルをアップロードする

[azcopy copy](storage-ref-azcopy-copy.md) コマンドを使用して、ローカル コンピューターからファイルやディレクトリをアップロードできます。

このセクションには、次の例が含まれています。

> [!div class="checklist"]
> * ファイルをアップロードする
> * ディレクトリをアップロードする
> * ディレクトリの内容をアップロードする 
> * 特定のファイルをアップロードする

> [!TIP]
> オプションのフラグを使用して、アップロード操作を調整できます。 以下にいくつか例を示します。
>
> |シナリオ|フラグ|
> |---|---|
> |追加 BLOB またはページ BLOB としてファイルをアップロードします。|**--blob-type**=\[BlockBlob\|PageBlob\|AppendBlob\]|
> |特定のアクセス層 (アーカイブ層など) にアップロードします。|**--block-blob-tier**=\[None\|Hot\|Cool\|Archive\]|
> 
> 完全な一覧については、「[オプション](storage-ref-azcopy-copy.md#options)」を参照してください。

### <a name="upload-a-file"></a>ファイルをアップロードする

|    |     |
|--------|-----------|
| **構文** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **例** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **例** (階層型名前空間) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

ファイル パスまたはファイル名の任意の場所で、ワイルドカード記号 (*) を使用してファイルをアップロードすることもできます。 例: `'C:\myDirectory\*.txt'`、または `C:\my*\*.txt`。

### <a name="upload-a-directory"></a>ディレクトリをアップロードする

この例では、ディレクトリ (とそのディレクトリ内のすべてのファイル) が BLOB コンテナーにコピーされます。 その結果、同じ名前でコンテナーにディレクトリが生成されます。

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

コンテナーに存在しないディレクトリの名前を指定すると、AzCopy は、その名前で新しいディレクトリを作成します。

### <a name="upload-the-contents-of-a-directory"></a>ディレクトリの内容をアップロードする

ワイルドカード記号 (*) を使用することで、ディレクトリ自体をコピーせずにディレクトリの内容をアップロードできます。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **例** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **例** (階層型名前空間) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |

> [!NOTE]
> すべてのサブディレクトリのファイルをアップロードするには、`--recursive` フラグを追加します。

### <a name="upload-specific-files"></a>特定のファイルをアップロードする

完全なファイル名、ワイルドカード文字 (*) を使用した部分的な名前、または日付と時刻を使用して、特定のファイルをアップロードすることができます。

#### <a name="specify-multiple-complete-file-names"></a>複数の完全なファイル名を指定する

[azcopy copy](storage-ref-azcopy-copy.md) コマンドを `--include-path` オプションと共に使用します。 セミコロン (`;`) を使用して、個々のファイル名を区切ります。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **例** (階層型名前空間) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

この例では、AzCopy によって `C:\myDirectory\photos` ディレクトリと `C:\myDirectory\documents\myFile.txt` ファイルが転送されます。 `C:\myDirectory\photos` ディレクトリ内のすべてのファイルを転送するには、`--recursive` オプションを含める必要があります。

`--exclude-path` オプションを使用してファイルを除外することもできます。 詳細については、「[azcopy copy](storage-ref-azcopy-copy.md)」リファレンス ドキュメントを参照してください。

#### <a name="use-wildcard-characters"></a>ワイルドカード文字を使用する

[azcopy copy](storage-ref-azcopy-copy.md) コマンドを `--include-pattern` オプションと共に使用します。 ワイルドカード文字を含む名前の一部を指定します。 セミコロン (`;`) で名前を区切ります。 

|    |     |
|--------|-----------|
| **構文** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **例** (階層型名前空間) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

`--exclude-pattern` オプションを使用してファイルを除外することもできます。 詳細については、「[azcopy copy](storage-ref-azcopy-copy.md)」リファレンス ドキュメントを参照してください。

`--include-pattern` オプションと `--exclude-pattern` オプションは、パスではなくファイル名にのみ適用されます。  ディレクトリ ツリーに存在するテキスト ファイルをすべてコピーする場合は、`–recursive` オプションを使用してディレクトリ ツリー全体を取得し、次に `–include-pattern` を使用して `*.txt` を指定し、すべてのテキスト ファイルを取得します。

#### <a name="upload-files-that-were-modified-after-a-date-and-time"></a>ある日時の後に変更されたファイルをアップロードする 

[azcopy copy](storage-ref-azcopy-copy.md) コマンドを `--include-after` オプションと共に使用します。 日付と時刻を ISO-8601 形式で指定します (例: `2020-08-19T15:04:00Z`)。 

|    |     |
|--------|-----------|
| **構文** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **例** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **例** (階層型名前空間) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory'   --include-after '2020-08-19T15:04:00Z'` |

詳細なリファレンスについては、[azcopy copy](storage-ref-azcopy-copy.md) に関するリファレンス ドキュメントを参照してください。

## <a name="download-files"></a>ファイルのダウンロード

[azcopy copy](storage-ref-azcopy-copy.md) コマンドを使用して、BLOB、ディレクトリ、コンテナーをローカル コンピューターにダウンロードできます。

このセクションには、次の例が含まれています。

> [!div class="checklist"]
> * ファイルをダウンロードする
> * ディレクトリをダウンロードする
> * ディレクトリの内容をダウンロードする
> * 特定のファイルをダウンロードする

> [!TIP]
> オプションのフラグを使用して、ダウンロード操作を調整できます。 以下にいくつか例を示します。
>
> |シナリオ|フラグ|
> |---|---|
> |自動的にファイルを圧縮解除します。|**--decompress**|
> |コピーに関連するログ エントリの詳細レベルを指定します。|**--log-level**=\[WARNING\|ERROR\|INFO\|NONE\]|
> |コピー先で競合するファイルと BLOB を上書きするかどうか、どのように上書きするのかを指定します。|**--overwrite**=\[true\|false\|ifSourceNewer\|prompt\]|
> 
> 完全な一覧については、[オプション](storage-ref-azcopy-copy.md#options)を参照してください。

> [!NOTE]
> BLOB の `Content-md5` プロパティ値にハッシュが含まれる場合、AzCopy では、ダウンロードするデータの MD5 ハッシュが計算され、BLOB の `Content-md5` プロパティに格納されている MD5 ハッシュが計算されたハッシュに一致するかどうかが検証されます。 これらの値が一致しない場合、`--check-md5=NoCheck` または `--check-md5=LogOnly` をコピー コマンドに追加してこの動作をオーバーライドしない限り、ダウンロードは失敗します。

### <a name="download-a-file"></a>ファイルをダウンロードする

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **例** (階層型名前空間) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>ディレクトリをダウンロードする

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **例** (階層型名前空間) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

この例を実行すると、ダウンロードしたファイルがすべて含まれる `C:\myDirectory\myBlobDirectory` という名前のディレクトリが生成されます。

### <a name="download-the-contents-of-a-directory"></a>ディレクトリの内容をダウンロードする

ワイルドカード記号 (*) を使用することで、ディレクトリ自体をコピーせずにディレクトリの内容をダウンロードできます。

> [!NOTE]
> 現在のところ、このシナリオは、階層型名前空間のないアカウントでのみサポートされています。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

> [!NOTE]
> すべてのサブディレクトリのファイルをダウンロードするには、`--recursive` フラグを追加します。

### <a name="download-specific-files"></a>特定のファイルをダウンロードする

完全なファイル名、ワイルドカード文字 (*) を使用した部分的な名前、または日付と時刻を使用して、特定のファイルをダウンロードすることができます。 

#### <a name="specify-multiple-complete-file-names"></a>複数の完全なファイル名を指定する

[azcopy copy](storage-ref-azcopy-copy.md) コマンドを `--include-path` オプションと共に使用します。 セミコロン (`;`) を使用して、個々のファイル名を区切ります。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **例** (階層型名前空間) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

この例では、AzCopy によって `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` ディレクトリと `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` ファイルが転送されます。 `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` ディレクトリ内のすべてのファイルを転送するには、`--recursive` オプションを含める必要があります。

`--exclude-path` オプションを使用してファイルを除外することもできます。 詳細については、「[azcopy copy](storage-ref-azcopy-copy.md)」リファレンス ドキュメントを参照してください。

#### <a name="use-wildcard-characters"></a>ワイルドカード文字を使用する

[azcopy copy](storage-ref-azcopy-copy.md) コマンドを `--include-pattern` オプションと共に使用します。 ワイルドカード文字を含む名前の一部を指定します。 セミコロン (`;`) で名前を区切ります。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **例** (階層型名前空間) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

`--exclude-pattern` オプションを使用してファイルを除外することもできます。 詳細については、「[azcopy copy](storage-ref-azcopy-copy.md)」リファレンス ドキュメントを参照してください。

`--include-pattern` オプションと `--exclude-pattern` オプションは、パスではなくファイル名にのみ適用されます。  ディレクトリ ツリーに存在するテキスト ファイルをすべてコピーする場合は、`–recursive` オプションを使用してディレクトリ ツリー全体を取得し、次に `–include-pattern` を使用して `*.txt` を指定し、すべてのテキスト ファイルを取得します。

#### <a name="download-files-that-were-modified-after-a-date-and-time"></a>ある日時の後に変更されたファイルをダウンロードする 

[azcopy copy](storage-ref-azcopy-copy.md) コマンドを `--include-after` オプションと共に使用します。 日付と時刻を ISO-8601 形式で指定します (例: `2020-08-19T15:04:00Z`)。 

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>/*' '<local-directory-path>' --include-after <Date-Time-in-ISO-8601-format>` |
| **例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **例** (階層型名前空間) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |

詳細なリファレンスについては、[azcopy copy](storage-ref-azcopy-copy.md) に関するリファレンス ドキュメントを参照してください。

## <a name="copy-blobs-between-storage-accounts"></a>ストレージ アカウント間で BLOB をコピーする

AzCopy を使用し、BLOB を他のストレージ アカウントにコピーできます。 コピー操作は同期しているため、コマンドが返されると、それはすべてのファイルがコピーされていることを示します。 

AzCopy では、[サーバー間](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) [API](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url) が使用されます。そのため、データはストレージ サーバー間で直接コピーされます。 これらのコピー操作では、コンピューターのネットワーク帯域幅が使用されません。 `AZCOPY_CONCURRENCY_VALUE` 環境変数の値を設定することによって、これらの操作のスループットを上げることができます。 詳しくは、「[スループットを最適化する](storage-use-azcopy-configure.md#optimize-throughput)」をご覧ください。

> [!NOTE]
> 現在のリリースでは、このシナリオに以下の制限があります。
>
> - 各ソース URL の末尾に SAS トークンを追加する必要があります。 Azure Active Directory (AD) を使用して認証資格情報を提供する場合、SAS トークンを省略できるのは宛先 URL だけです。 宛先アカウントに適切なロールが設定されていることを確認します。 「[オプション 1: Azure Active Directory を使用する](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory)」を参照してください。
>-  Premium ブロック BLOB ストレージ アカウントでは、アクセス層はサポートされません。 `s2s-preserve-access-tier` を `false` に設定することで (例: `--s2s-preserve-access-tier=false`)、コピー操作から BLOB のアクセス層を除外します。

このセクションには、次の例が含まれています。

> [!div class="checklist"]
> * 別のストレージ アカウントに BLOB をコピーする
> * 別のストレージ アカウントにディレクトリをコピーする
> * 別のストレージ アカウントにコンテナーをコピーする
> * すべてのコンテナー、ディレクトリ、ファイルを別のストレージ アカウントにコピーする

これらの例は、階層型名前空間があるアカウントでも機能します。 [Data Lake Storage のマルチプロトコル アクセス](../blobs/data-lake-storage-multi-protocol-access.md)では、これらのアカウントで同じ URL 構文 (`blob.core.windows.net`) を使用できます。

> [!TIP]
> オプションのフラグを使用して、コピー操作を調整できます。 以下にいくつか例を示します。
>
> |シナリオ|フラグ|
> |---|---|
> |ブロック、ページ、または追加 BLOB として BLOB をコピーします。|**--blob-type**=\[BlockBlob\|PageBlob\|AppendBlob\]|
> |特定のアクセス層 (アーカイブ層など) にコピーします。|**--block-blob-tier**=\[None\|Hot\|Cool\|Archive\]|
> |自動的にファイルを圧縮解除します。|**--decompress**=\[gzip\|deflate\]|
> 
> 完全な一覧については、[オプション](storage-ref-azcopy-copy.md#options)を参照してください。

### <a name="copy-a-blob-to-another-storage-account"></a>別のストレージ アカウントに BLOB をコピーする

階層型名前空間があるアカウントに同じ URL 構文 (`blob.core.windows.net`) を使用します。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **例** (階層型名前空間) | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

### <a name="copy-a-directory-to-another-storage-account"></a>別のストレージ アカウントにディレクトリをコピーする

階層型名前空間があるアカウントに同じ URL 構文 (`blob.core.windows.net`) を使用します。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **例** (階層型名前空間)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-container-to-another-storage-account"></a>別のストレージ アカウントにコンテナーをコピーする

階層型名前空間があるアカウントに同じ URL 構文 (`blob.core.windows.net`) を使用します。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **例** (階層型名前空間)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>すべてのコンテナー、ディレクトリ、BLOB を別のストレージ アカウントにコピーする

階層型名前空間があるアカウントに同じ URL 構文 (`blob.core.windows.net`) を使用します。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |
| **例** (階層型名前空間)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

## <a name="synchronize-files"></a>ファイルを同期する

ローカル ファイル システムのコンテンツを BLOB コンテナーに同期できます。 また、コンテナーと仮想ディレクトリを相互に同期することもできます。 同期は一方向です。 言い換えると、2 つのエンドポイントのいずれかを同期元として、いずれかを同期先として選択します。 同期にもサーバー間 API が使用されます。 このセクションに示されている例は、階層型名前空間があるアカウントでも機能します。 

> [!NOTE]
> 現行版の AzCopy では、他のコピー元とコピー先の間では同期されません (例:ファイル ストレージまたはアマゾン ウェブ サービス (AWS) S3 バケット)。

[sync](storage-ref-azcopy-sync.md) コマンドでは、ファイル名と最後に変更されたタイムスタンプが比較されます。 省略可能な `--delete-destination` フラグの値を `true` または `prompt` に設定すると、コピー元のディレクトリにファイルがもう存在しなくなると、コピー先のディレクトリからそれらのファイルが削除されます。

`--delete-destination` フラグを `true` に設定すると、AzCopy では、プロンプトを与えずにファイルが削除されます。 AzCopy でファイルが削除される前にプロンプトを表示する場合、`--delete-destination` フラグを `prompt` に設定します。

> [!NOTE]
> 誤削除を防ぐために、`--delete-destination=prompt|true` フラグを使用する前に[論理的な削除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)機能を有効にしてください。

> [!TIP]
> オプションのフラグを使用して、同期操作を調整できます。 以下にいくつか例を示します。
>
> |シナリオ|フラグ|
> |---|---|
> |ダウンロードの際に MD5 ハッシュを検証する厳密さを指定します。|**--check-md5**=\[NoCheck\|LogOnly\|FailIfDifferent\|FailIfDifferentOrMissing\]|
> |パターンに基づいてファイルを除外します。|**--exclude-path**|
> |同期に関連するログ エントリの詳細レベルを指定します。|**--log-level**=\[WARNING\|ERROR\|INFO\|NONE\]|
> 
> 完全な一覧については、[オプション](storage-ref-azcopy-sync.md#options)を参照してください。

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>ローカル ファイル システムへの変更を使用してコンテナーを更新する

この場合、コンテナーが宛先であり、ローカル ファイル システムがソースです。 

|    |     |
|--------|-----------|
| **構文** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **例** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>コンテナーへの変更を使用してローカル ファイル システムを更新する

この場合、ローカル ファイル システムが宛先であり、コンテナーがソースです。

|    |     |
|--------|-----------|
| **構文** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **例** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

### <a name="update-a-container-with-changes-in-another-container"></a>別のコンテナーへの変更を使用してコンテナーを更新する

このコマンドに表示される最初のコンテナーがソースです。 2 つ目が宛先です。

|    |     |
|--------|-----------|
| **構文** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **例** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>別のファイル共有内のディレクトリへの変更を使用してディレクトリを更新する

このコマンドに表示される最初のディレクトリがソースです。 2 つ目が宛先です。

|    |     |
|--------|-----------|
| **構文** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **例** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="next-steps"></a>次のステップ

以下の記事にサンプルがあります。

- [AzCopy を使ってみる](storage-use-azcopy-v10.md)

- [チュートリアル:AzCopy を使用したオンプレミス データのクラウド ストレージへの移行](storage-use-azcopy-migrate-on-premises-data.md)

- [AzCopy とファイル ストレージでデータを転送する](storage-use-azcopy-files.md)

- [AzCopy と Amazon S3 バケットでデータを転送する](storage-use-azcopy-s3.md)

- [AzCopy の構成、最適化、トラブルシューティング](storage-use-azcopy-configure.md)
