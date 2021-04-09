---
title: AzCopy v10 を使用して Azure Files をコピー先またはコピー元としてデータを転送する | Microsoft Docs
description: AzCopy とファイル ストレージでデータを転送します。 AzCopy は、ストレージ アカウントとの間で BLOB またはファイルをコピーするためのコマンドライン ツールです。 Azure Files で AzCopy を使用します。
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 2cca8a93330e5ddd965d27532895ed1d6702c123
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179648"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>AzCopy とファイル ストレージでデータを転送する 

AzCopy は、ストレージ アカウント間でのファイル コピーに利用できるコマンドライン ユーティリティです。 この記事には、Azure Files で使用するサンプル コマンドが含まれています。

始める前に、[AzCopy を使ってみる](storage-use-azcopy-v10.md)の記事を参照して、AzCopy をダウンロードし、ツールについて理解してください。

> [!TIP]
> この記事の例では、単一引用符 ('') でパス引数を囲みます。 Windows コマンド シェル (cmd.exe) を除き、すべてのコマンド シェルで単一引用符を使用します。 Windows コマンド シェル (cmd.exe) を使用している場合は、単一引用符 ('') ではなく、二重引用符 ("") でパス引数を囲みます。

## <a name="create-file-shares"></a>ファイル共有の作成

[azcopy make](storage-ref-azcopy-make.md) コマンドを使用し、ファイル共有を作成できます。 このセクションの例では、`myfileshare` という名前のファイル共有を作成します。

|    |     |
|--------|-----------|
| **構文** | `azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>'` |
| **例** | `azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

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
> |ファイルと共にアクセス制御リスト (ACL) をコピーします。|**--preserve-smb-permissions**=\[true\|false\]|
> |ファイルと共に SMB プロパティ情報をコピーします。|**--preserve-smb-info**=\[true\|false\]|
> 
> 完全な一覧については、「[オプション](storage-ref-azcopy-copy.md#options)」を参照してください。

> [!NOTE]
> AzCopy では、ファイルの md5 ハッシュ コードが自動的に計算されたり、保存されたりすることはありません。 AzCopy にそれを行わせる場合、各コピー コマンドの先頭に `--put-md5` フラグを追加します。 そうすることで、ファイルがダウンロードされたとき、AzCopy では、ダウンロードするデータの MD5 ハッシュが計算され、ファイルの `Content-md5` プロパティに格納されている MD5 ハッシュが計算されたハッシュに一致するかどうかが検証されます。

### <a name="upload-a-file"></a>ファイルをアップロードする

|    |     |
|--------|-----------|
| **構文** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name><SAS-token>'` |
| **例** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

ファイル パスまたはファイル名の任意の場所で、ワイルドカード記号 (*) を使用してファイルをアップロードすることもできます。 例: `'C:\myDirectory\*.txt'`、または `C:\my*\*.txt`。

### <a name="upload-a-directory"></a>ディレクトリをアップロードする

この例では、ディレクトリ (とそのディレクトリ内のすべてのファイル) がファイル共有にコピーされます。 その結果、同じ名前でファイル共有にディレクトリが生成されます。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

ファイル共有内のディレクトリにコピーするには、コマンド文字列でそのディレクトリの名前を指定します。

|    |     |
|--------|-----------|
| **例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

ファイル共有に存在しないディレクトリの名前を指定すると、AzCopy は、その名前で新しいディレクトリを作成します。

### <a name="upload-the-contents-of-a-directory"></a>ディレクトリの内容をアップロードする

ワイルドカード記号 (*) を使用することで、ディレクトリ自体をコピーせずにディレクトリの内容をアップロードできます。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>` |
| **例** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> すべてのサブディレクトリのファイルをアップロードするには、`--recursive` フラグを追加します。

### <a name="upload-specific-files"></a>特定のファイルをアップロードする

完全なファイル名、ワイルドカード文字 (*) を使用した部分的な名前、または日付と時刻を使用して、特定のファイルをアップロードすることができます。

#### <a name="specify-multiple-complete-file-names"></a>複数の完全なファイル名を指定する

[azcopy copy](storage-ref-azcopy-copy.md) コマンドを `--include-path` オプションと共に使用します。 セミコロン (`;`) を使用して、個々のファイル名を区切ります。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-path <semicolon-separated-file-list>` |
| **例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'` |

この例では、AzCopy によって `C:\myDirectory\photos` ディレクトリと `C:\myDirectory\documents\myFile.txt` ファイルが転送されます。 `C:\myDirectory\photos` ディレクトリ内のすべてのファイルを転送するには、`--recursive` オプションを含める必要があります。

`--exclude-path` オプションを使用してファイルを除外することもできます。 詳細については、「[azcopy copy](storage-ref-azcopy-copy.md)」リファレンス ドキュメントを参照してください。

#### <a name="use-wildcard-characters"></a>ワイルドカード文字を使用する

[azcopy copy](storage-ref-azcopy-copy.md) コマンドを `--include-pattern` オプションと共に使用します。 ワイルドカード文字を含む名前の一部を指定します。 セミコロン (`;`) で名前を区切ります。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'` |

`--exclude-pattern` オプションを使用してファイルを除外することもできます。 詳細については、「[azcopy copy](storage-ref-azcopy-copy.md)」リファレンス ドキュメントを参照してください。

`--include-pattern` オプションと `--exclude-pattern` オプションは、パスではなくファイル名にのみ適用されます。  ディレクトリ ツリーに存在するテキスト ファイルをすべてコピーする場合は、`--recursive` オプションを使用してディレクトリ ツリー全体を取得し、次に `--include-pattern` を使用して `*.txt` を指定し、すべてのテキスト ファイルを取得します。

#### <a name="upload-files-that-were-modified-after-a-date-and-time"></a>ある日時の後に変更されたファイルをアップロードする 

[azcopy copy](storage-ref-azcopy-copy.md) コマンドを `--include-after` オプションと共に使用します。 日時を ISO 8601 形式で指定します (例: `2020-08-19T15:04:00Z`)。 

|    |     |
|--------|-----------|
| **構文** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **例** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-after '2020-08-19T15:04:00Z'` |

詳細なリファレンスについては、[azcopy copy](storage-ref-azcopy-copy.md) リファレンス ドキュメントを参照してください。

## <a name="download-files"></a>ファイルのダウンロード

[azcopy copy](storage-ref-azcopy-copy.md) コマンドを使用して、ファイル、ディレクトリ、ファイル共有をローカル コンピューターにダウンロードできます。

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
> |ファイルと共にアクセス制御リスト (ACL) をコピーします。|**--preserve-smb-permissions**=\[true\|false\]|
> |ファイルと共に SMB プロパティ情報をコピーします。|**--preserve-smb-info**=\[true\|false\]|
> |自動的にファイルを圧縮解除します。|**--decompress**|
> 
> 完全な一覧については、「[オプション](storage-ref-azcopy-copy.md#options)」を参照してください。

> [!NOTE]
> ファイルの `Content-md5` プロパティ値にハッシュが含まれる場合、AzCopy では、ダウンロードするデータの MD5 ハッシュが計算され、ファイルの `Content-md5` プロパティに格納されている MD5 ハッシュが計算されたハッシュに一致するかどうかが検証されます。 これらの値が一致しない場合、`--check-md5=NoCheck` または `--check-md5=LogOnly` をコピー コマンドに追加してこの動作をオーバーライドしない限り、ダウンロードは失敗します。

### <a name="download-a-file"></a>ファイルをダウンロードする

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' '<local-file-path>'` |
| **例** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>ディレクトリをダウンロードする

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' '<local-directory-path>' --recursive` |
| **例** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive` |

この例を実行すると、ダウンロードしたファイルがすべて含まれる `C:\myDirectory\myFileShareDirectory` という名前のディレクトリが生成されます。

### <a name="download-the-contents-of-a-directory"></a>ディレクトリの内容をダウンロードする

ワイルドカード記号 (*) を使用することで、ディレクトリ自体をコピーせずにディレクトリの内容をダウンロードできます。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*<SAS-token>' '<local-directory-path>/'` |
| **例** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'` |

> [!NOTE]
> すべてのサブディレクトリのファイルをダウンロードするには、`--recursive` フラグを追加します。

### <a name="download-specific-files"></a>特定のファイルをダウンロードする

完全なファイル名、ワイルドカード文字 (*) を使用した部分的な名前、または日付と時刻を使用して、特定のファイルをダウンロードすることができます。

#### <a name="specify-multiple-complete-file-names"></a>複数の完全なファイル名を指定する

[azcopy copy](storage-ref-azcopy-copy.md) コマンドを `--include-path` オプションと共に使用します。 セミコロン (`;`) を使用して、個々のファイル名を区切ります。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **例** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |

この例では、AzCopy によって `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` ディレクトリと `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt` ファイルが転送されます。 `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` ディレクトリ内のすべてのファイルを転送するには、`--recursive` オプションを含めます。

`--exclude-path` オプションを使用してファイルを除外することもできます。 詳細については、「[azcopy copy](storage-ref-azcopy-copy.md)」リファレンス ドキュメントを参照してください。

#### <a name="use-wildcard-characters"></a>ワイルドカード文字を使用する

[azcopy copy](storage-ref-azcopy-copy.md) コマンドを `--include-pattern` オプションと共に使用します。 ワイルドカード文字を含む名前の一部を指定します。 セミコロン (`;`) で名前を区切ります。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **例** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

`--exclude-pattern` オプションを使用してファイルを除外することもできます。 詳細については、「[azcopy copy](storage-ref-azcopy-copy.md)」リファレンス ドキュメントを参照してください。

`--include-pattern` オプションと `--exclude-pattern` オプションは、パスではなくファイル名にのみ適用されます。  ディレクトリ ツリーに存在するテキスト ファイルをすべてコピーする場合は、`--recursive` オプションを使用してディレクトリ ツリー全体を取得し、次に `--include-pattern` を使用して `*.txt` を指定し、すべてのテキスト ファイルを取得します。

#### <a name="download-files-that-were-modified-after-a-date-and-time"></a>ある日時の後に変更されたファイルをダウンロードする 

[azcopy copy](storage-ref-azcopy-copy.md) コマンドを `--include-after` オプションと共に使用します。 日付と時刻を ISO-8601 形式で指定します (例: `2020-08-19T15:04:00Z`)。 

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>/*<SAS-token>' '<local-directory-path>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **例** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/*?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory' --include-after '2020-08-19T15:04:00Z'` |


詳細なリファレンスについては、[azcopy copy](storage-ref-azcopy-copy.md) リファレンス ドキュメントを参照してください。

#### <a name="download-from-a-share-snapshot"></a>共有スナップショットからのダウンロード

ファイルまたはディレクトリの特定のバージョンをダウンロードするには、共有スナップショットの **DateTime** 値を参照します。 共有スナップショットの詳細については、「[Azure Files の共有スナップショットの概要](../files/storage-snapshots-files.md)」を参照してください。 

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path-or-directory-name><SAS-token>&sharesnapshot=<DateTime-of-snapshot>' '<local-file-or-directory-path>'` |
| **例** (ファイルのダウンロード) | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'` |
| **例** (ディレクトリのダウンロード) | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory'  --recursive`|


## <a name="copy-files-between-storage-accounts"></a>ストレージ アカウント間でファイルをコピーする

AzCopy を使用し、ファイルを他のストレージ アカウントにコピーできます。 コピー操作は同期しているため、コマンドが返されると、それはすべてのファイルがコピーされていることを示します。

AzCopy では、[サーバー間](/rest/api/storageservices/put-block-from-url) [API](/rest/api/storageservices/put-page-from-url) が使用されます。そのため、データはストレージ サーバー間で直接コピーされます。 これらのコピー操作では、コンピューターのネットワーク帯域幅が使用されません。 `AZCOPY_CONCURRENCY_VALUE` 環境変数の値を設定することによって、これらの操作のスループットを上げることができます。 詳しくは、「[スループットを最適化する](storage-use-azcopy-configure.md#optimize-throughput)」をご覧ください。

また、共有スナップショットの **DateTime** 値を参照して、特定のバージョンのファイルをコピーすることもできます。 共有スナップショットの詳細については、「[Azure Files の共有スナップショットの概要](../files/storage-snapshots-files.md)」を参照してください。 

このセクションには、次の例が含まれています。

> [!div class="checklist"]
> * 別のストレージ アカウントにファイルをコピーする
> * 別のストレージ アカウントにディレクトリをコピーする
> * 別のストレージ アカウントにファイル共有をコピーする
> * すべてのファイル共有、ディレクトリ、ファイルを別のストレージ アカウントにコピーする

> [!TIP]
> オプションのフラグを使用して、コピー操作を調整できます。 以下にいくつか例を示します。
>
> |シナリオ|フラグ|
> |---|---|
> |ファイルと共にアクセス制御リスト (ACL) をコピーします。|**--preserve-smb-permissions**=\[true\|false\]|
> |ファイルと共に SMB プロパティ情報をコピーします。|**--preserve-smb-info**=\[true\|false\]|
> 
> 完全な一覧については、「[オプション](storage-ref-azcopy-copy.md#options)」を参照してください。

### <a name="copy-a-file-to-another-storage-account"></a>別のストレージ アカウントにファイルをコピーする

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>'` |
| **例** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |
| **例** (共有スナップショット) | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |

### <a name="copy-a-directory-to-another-storage-account"></a>別のストレージ アカウントにディレクトリをコピーする

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **例** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/myFileShare/myFileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |
| **例** (共有スナップショット) | `azcopy copy 'https://mysourceaccount.file.core.windows.net/myFileShare/myFileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |


### <a name="copy-a-file-share-to-another-storage-account"></a>別のストレージ アカウントにファイル共有をコピーする

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **例** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |
| **例** (共有スナップショット) | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |


### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>すべてのファイル共有、ディレクトリ、ファイルを別のストレージ アカウントにコピーする

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<SAS-token>' --recursive'` |
| **例** | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |
| **例** (共有スナップショット) | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |


## <a name="synchronize-files"></a>ファイルを同期する

ファイル共有の内容を別のファイル共有と同期することができます。 また、ファイル共有内のディレクトリの内容を、別のファイル共有に配置されているディレクトリの内容と同期させることもできます。 同期は一方向です。 言い換えると、2 つのエンドポイントのいずれかを同期元として、いずれかを同期先として選択します。 同期にもサーバー間 API が使用されます。

> [!NOTE]
> 現在のところ、このシナリオは、階層型名前空間のないアカウントでのみサポートされています。 AzCopy の現在のリリースでは、Azure Files と Blob Storage が同期されません。

[sync](storage-ref-azcopy-sync.md) コマンドでは、ファイル名と最後に変更されたタイムスタンプが比較されます。 省略可能な `--delete-destination` フラグの値を `true` または `prompt` に設定すると、コピー元のディレクトリにファイルがもう存在しなくなると、コピー先のディレクトリからそれらのファイルが削除されます。

`--delete-destination` フラグを `true` に設定すると、AzCopy では、プロンプトが表示されずにファイルが削除されます。 AzCopy でファイルが削除される前にプロンプトを表示する場合、`--delete-destination` フラグを `prompt` に設定します。

> [!TIP]
> オプションのフラグを使用して、同期操作を調整できます。 以下にいくつか例を示します。
>
> |シナリオ|フラグ|
> |---|---|
> |ファイルと共にアクセス制御リスト (ACL) をコピーします。|**--preserve-smb-permissions**=\[true\|false\]|
> |ファイルと共に SMB プロパティ情報をコピーします。|**--preserve-smb-info**=\[true\|false\]|
> |パターンに基づいてファイルを除外します。|**--exclude-path**|
> |同期に関連するログ エントリの詳細レベルを指定します。|**--log-level**=\[WARNING\|ERROR\|INFO\|NONE\]|
> 
> 完全な一覧については、「[オプション](storage-ref-azcopy-sync.md#options)」を参照してください。

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>別のファイル共有への変更を使用してファイル共有を更新する

このコマンドに表示される最初のファイル共有がソースです。 2 つ目が宛先です。

|    |     |
|--------|-----------|
| **構文** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **例** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>別のファイル共有内のディレクトリへの変更を使用してディレクトリを更新する

このコマンドに表示される最初のディレクトリがソースです。 2 つ目が宛先です。

|    |     |
|--------|-----------|
| **構文** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' --recursive` |
| **例** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-file-share-to-match-the-contents-of-a-share-snapshot"></a>共有スナップショットの内容と一致するようにファイル共有を更新する

このコマンドに表示される最初のファイル共有がソースです。 URI の末尾に文字列 `&sharesnapshot=` を追加し、その後にスナップショットの **DateTime** 値を追加します。 

|    |     |
|--------|-----------|
| **構文** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>&sharesnapsot<snapshot-ID>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **例** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-03-03T20%3A24%3A13.0000000Z' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

共有スナップショットの詳細については、「[Azure Files の共有スナップショットの概要](../files/storage-snapshots-files.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

以下の記事にサンプルがあります。

- [AzCopy を使ってみる](storage-use-azcopy-v10.md)

- [データの転送](storage-use-azcopy-v10.md#transfer-data)

- [AzCopy の構成、最適化、トラブルシューティング](storage-use-azcopy-configure.md)
