---
title: AzCopy v10 を使用して Azure BLOB ストレージから BLOB をダウンロードする | Microsoft Docs
description: この記事には、Azure BLOB ストレージから BLOB をダウンロードするために役立つ AzCopy コマンドの例のコレクションが含まれています。
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/11/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 20da05399eed4cb9c5a4b69a82b0b1e799997751
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880120"
---
# <a name="download-blobs-from-azure-blob-storage-by-using-azcopy-v10"></a>AzCopy v10 を使用して Azure BLOB ストレージから BLOB をダウンロードする

AzCopy v10 コマンド ライン ユーティリティを使用して、BLOB ストレージから BLOB やディレクトリをダウンロードできます。 

ファイルのアップロード、BLOB ストレージとの同期、アカウント間での BLOB のコピーなどの他の種類のタスクの例を確認するには、この記事の「[次のステップ](#next-steps)」のセクションに示されているリンクを参照してください。

## <a name="get-started"></a>はじめに

AzCopy のダウンロード方法と、ストレージ サービスに認証資格情報を与える方法については、[AzCopy の作業開始](storage-use-azcopy-v10.md)に関するページをご覧ください。

> [!NOTE] 
> この記事の例では、Azure Active Directory (Azure AD) を使用して認証資格情報を指定していることを前提としています。
>
> SAS トークンを使用して BLOB データへのアクセスを承認する場合、各 AzCopy コマンドのリソース URL の先頭にそのトークンを追加できます。 (例: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`)。

## <a name="download-a-blob"></a>BLOB をダウンロードする

[azcopy copy](storage-ref-azcopy-copy.md) コマンドを使用して BLOB をダウンロードします。

> [!TIP]
> この例では、パス引数を単一引用符 ('') で囲んでいます。 Windows コマンド シェル (cmd.exe) を除き、すべてのコマンド シェルで単一引用符を使用します。 Windows コマンド シェル (cmd.exe) を使用している場合は、単一引用符 ('') ではなく、二重引用符 ("") でパス引数を囲みます。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **例** (階層型名前空間) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

> [!NOTE]
> BLOB の `Content-md5` プロパティ値にハッシュが含まれる場合、AzCopy では、ダウンロードするデータの MD5 ハッシュが計算され、BLOB の `Content-md5` プロパティに格納されている MD5 ハッシュが計算されたハッシュに一致するかどうかが検証されます。 これらの値が一致しない場合、`--check-md5=NoCheck` または `--check-md5=LogOnly` をコピー コマンドに追加してこの動作をオーバーライドしない限り、ダウンロードは失敗します。

## <a name="download-a-directory"></a>ディレクトリをダウンロードする

[azcopy copy](storage-ref-azcopy-copy.md) コマンドを使用してディレクトリをダウンロードします。

> [!TIP]
> この例では、パス引数を単一引用符 ('') で囲んでいます。 Windows コマンド シェル (cmd.exe) を除き、すべてのコマンド シェルで単一引用符を使用します。 Windows コマンド シェル (cmd.exe) を使用している場合は、単一引用符 ('') ではなく、二重引用符 ("") でパス引数を囲みます。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **例** (階層型名前空間) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

この例では、ダウンロードされたすべての BLOB を含む `C:\myDirectory\myBlobDirectory` という名前のディレクトリが生成されます。

## <a name="download-directory-contents"></a>ディレクトリの内容をダウンロードする

ワイルドカード記号 (*) を使用することで、ディレクトリ自体をコピーせずにディレクトリの内容をダウンロードできます。

> [!TIP]
> この例では、パス引数を単一引用符 ('') で囲んでいます。 Windows コマンド シェル (cmd.exe) を除き、すべてのコマンド シェルで単一引用符を使用します。 Windows コマンド シェル (cmd.exe) を使用している場合は、単一引用符 ('') ではなく、二重引用符 ("") でパス引数を囲みます。

> [!NOTE]
> 現在のところ、このシナリオは、階層型名前空間のないアカウントでのみサポートされています。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

すべてのサブディレクトリ内のファイルをダウンロードするには、`--recursive` フラグを追加します。

## <a name="download-specific-blobs"></a>特定の BLOB をダウンロードする

完全なファイル名かワイルドカード文字 (*) による部分的な名前を使用して、または日付と時刻を使用して、特定の BLOB をダウンロードできます。 

> [!TIP]
> これらの例では、パス引数を単一引用符 ('') で囲んでいます。 Windows コマンド シェル (cmd.exe) を除き、すべてのコマンド シェルで単一引用符を使用します。 Windows コマンド シェル (cmd.exe) を使用している場合は、単一引用符 ('') ではなく、二重引用符 ("") でパス引数を囲みます。

#### <a name="specify-multiple-complete-blob-names"></a>複数の完全な BLOB 名を指定する

[azcopy copy](storage-ref-azcopy-copy.md) コマンドを `--include-path` オプションと共に使用します。 個々の BLOB 名は、セミコロン (`;`) を使用して区切ります。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **例** (階層型名前空間) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

この例では、AzCopy によって `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` ディレクトリと `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` ファイルが転送されます。 `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` ディレクトリ内のすべての BLOB を転送するには、`--recursive` オプションを含めます。

`--exclude-path` オプションを使用して BLOB を除外することもできます。 詳細については、「[azcopy copy](storage-ref-azcopy-copy.md)」リファレンス ドキュメントを参照してください。

#### <a name="use-wildcard-characters"></a>ワイルドカード文字を使用する

[azcopy copy](storage-ref-azcopy-copy.md) コマンドを `--include-pattern` オプションと共に使用します。 ワイルドカード文字を含む名前の一部を指定します。 セミコロン (`;`) で名前を区切ります。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **例** (階層型名前空間) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

`--exclude-pattern` オプションを使用して BLOB を除外することもできます。 詳細については、「[azcopy copy](storage-ref-azcopy-copy.md)」リファレンス ドキュメントを参照してください。

`--include-pattern` および `--exclude-pattern` オプションは BLOB 名にのみ適用され、パスには適用されません。  ディレクトリ ツリー内に存在するすべてのテキスト ファイル (BLOB) をコピーする場合は、`–recursive` オプションを使用してディレクトリ ツリー全体を取得した後、`–include-pattern` を使用し、`*.txt` を指定してすべてのテキスト ファイルを取得します。

#### <a name="download-blobs-that-were-modified-before-or-after-a-date-and-time"></a>ある日付と時刻の前後に変更された BLOB をダウンロードする 

[azcopy copy](storage-ref-azcopy-copy.md) コマンドを `--include-before` または `--include-after` オプションと共に使用します。 日付と時刻を ISO-8601 形式で指定します (例: `2020-08-19T15:04:00Z`)。 

次の例では、指定した日付以降に変更されたファイルをダウンロードします。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>/*' '<local-directory-path>' --include-after <Date-Time-in-ISO-8601-format>` |
| **例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **例** (階層型名前空間) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |

詳細なリファレンスについては、[azcopy copy](storage-ref-azcopy-copy.md) に関するリファレンス ドキュメントを参照してください。

#### <a name="download-previous-versions-of-a-blob"></a>前のバージョンの BLOB をダウンロードする

[BLOB のバージョン管理](../blobs/versioning-enable.md)を有効にした場合、BLOB の以前のバージョンを 1 つでも複数でもダウンロードできます。 

まず、[バージョン ID](../blobs/versioning-overview.md) の一覧が含まれるテキスト ファイルを作成します。 各バージョン ID は別個の行に表示する必要があります。 次に例を示します。 

```
2020-08-17T05:50:34.2199403Z
2020-08-17T05:50:34.5041365Z
2020-08-17T05:50:36.7607103Z
```

次に、[azcopy copy](storage-ref-azcopy-copy.md) コマンドを `--list-of-versions` オプションと共に使用します。 バージョンの一覧を含むテキスト ファイルの場所を指定します (例: `D:\\list-of-versions.txt`)。  

#### <a name="download-a-blob-snapshot"></a>BLOB のスナップショットをダウンロードする

BLOB スナップショットの **DateTime** 値を参照することによって、[BLOB スナップショット](../blobs/snapshots-overview.md)をダウンロードできます。 

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>?sharesnapshot=<DateTime-of-snapshot>' '<local-file-path>'` |
| **例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'` |
| **例** (階層型名前空間) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'` |

> [!NOTE]
> SAS トークンを使用して BLOB データへのアクセスを認可している場合は、SAS トークンの後にスナップショット **DateTime** を追加します。 (例: `'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z'`)。

## <a name="download-with-optional-flags"></a>オプションのフラグを使用してダウンロードする

オプションのフラグを使用して、ダウンロード操作を調整できます。 以下にいくつか例を示します。

|シナリオ|フラグ|
|---|---|
|自動的にファイルを圧縮解除します。|**--decompress**|
|コピーに関連するログ エントリの詳細レベルを指定します。|**--log-level**=\[WARNING\|ERROR\|INFO\|NONE\]|
|コピー先で競合するファイルと BLOB を上書きするかどうか、どのように上書きするのかを指定します。|**--overwrite**=\[true\|false\|ifSourceNewer\|prompt\]|

完全な一覧については、[オプション](storage-ref-azcopy-copy.md#options)を参照してください。

## <a name="next-steps"></a>次のステップ

他の例については、次の記事を参照してください。

- [例:アップロード](storage-use-azcopy-blobs-upload.md)
- [例:アカウント間でのコピー](storage-use-azcopy-blobs-copy.md)
- [例:同期](storage-use-azcopy-blobs-synchronize.md)
- [例:Amazon S3 バケット](storage-use-azcopy-s3.md)
- [例:Azure Files](storage-use-azcopy-files.md)
- [チュートリアル:AzCopy を使用したオンプレミス データのクラウド ストレージへの移行](storage-use-azcopy-migrate-on-premises-data.md)
- [AzCopy の構成、最適化、トラブルシューティング](storage-use-azcopy-configure.md)