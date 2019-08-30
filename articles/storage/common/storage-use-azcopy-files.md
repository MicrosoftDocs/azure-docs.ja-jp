---
title: AzCopy v10 を使用して Azure Files をコピー先またはコピー元としてデータを転送する | Microsoft Docs
description: AzCopy とファイル ストレージでデータを転送します。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 0c350776323c1b2949285a7ebe6a7c2778ae4dc4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648753"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>AzCopy とファイル ストレージでデータを転送する 

AzCopy は、ストレージ アカウント間の BLOB またはファイル コピーに利用できるコマンドライン ユーティリティです。 この記事には、Azure Files で使用するサンプル コマンドが含まれています。

始める前に、[AzCopy を使ってみる](storage-use-azcopy-v10.md)の記事を参照して、AzCopy をダウンロードし、ツールについて理解してください。

## <a name="create-file-shares"></a>ファイル共有の作成

AzCopy `make` コマンドを使用し、ファイル共有を作成できます。 このセクションの例では、`myfileshare` という名前のファイル共有を作成します。

|    |     |
|--------|-----------|
| **構文** | `azcopy make "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>"` |
| **例** | `azcopy make "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

## <a name="upload-files"></a>ファイルをアップロードする

AzCopy `copy` コマンドを使用して、ローカル コンピューターからファイルやディレクトリをアップロードすることができます。

このセクションには、次の例が含まれています。

> [!div class="checklist"]
> * ファイルをアップロードする
> * ディレクトリをアップロードする
> * ワイルドカード文字を使用してファイルをアップロードする

> [!NOTE]
> AzCopy では、ファイルの md5 ハッシュ コードが自動的に計算されたり、保存されたりすることはありません。 AzCopy にそれを行わせる場合、各コピー コマンドの先頭に `--put-md5` フラグを追加します。 そうすることで、ファイルがダウンロードされたとき、AzCopy では、ダウンロードするデータの MD5 ハッシュが計算され、ファイルの `Content-md5` プロパティに格納されている MD5 ハッシュが計算されたハッシュに一致するかどうかが検証されます。

### <a name="upload-a-file"></a>ファイルをアップロードする

|    |     |
|--------|-----------|
| **構文** | `azcopy copy "<local-file-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>"` |
| **例** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

### <a name="upload-a-directory"></a>ディレクトリをアップロードする

この例では、ディレクトリ (とそのディレクトリ内のすべてのファイル) がファイル共有にコピーされます。 その結果、同じ名前でファイル共有にディレクトリが生成されます。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>" --recursive` |
| **例** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

ファイル共有内のディレクトリにコピーするには、コマンド文字列でそのディレクトリの名前を指定します。

|    |     |
|--------|-----------|
| **例** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

ファイル共有に存在しないディレクトリの名前を指定すると、AzCopy は、その名前で新しいディレクトリを作成します。

### <a name="upload-the-contents-of-a-directory"></a>ディレクトリの内容をアップロードする

ワイルドカード記号 (*) を使用することで、ディレクトリ自体をコピーせずにディレクトリの内容をアップロードできます。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy "<local-directory-path>/*" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>` |
| **例** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> すべてのサブディレクトリのファイルをアップロードするには、`--recursive` フラグを追加します。

## <a name="download-files"></a>ファイルをダウンロードする

AzCopy `copy` コマンドを使用して、ファイル、ディレクトリ、ファイル共有をローカル コンピューターにダウンロードできます。

このセクションには、次の例が含まれています。

> [!div class="checklist"]
> * ファイルをダウンロードする
> * ディレクトリをダウンロードする
> * ワイルドカード文字を使用してファイルをダウンロードする

> [!NOTE]
> ファイルの `Content-md5` プロパティ値にハッシュが含まれる場合、AzCopy では、ダウンロードするデータの MD5 ハッシュが計算され、ファイルの `Content-md5` プロパティに格納されている MD5 ハッシュが計算されたハッシュに一致するかどうかが検証されます。 これらの値が一致しない場合、`--check-md5=NoCheck` または `--check-md5=LogOnly` をコピー コマンドに追加してこの動作を上書きしない限り、ダウンロードは失敗します。

### <a name="download-a-file"></a>ファイルをダウンロードする

|    |     |
|--------|-----------|
| **構文** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>" "<local-file-path>"` |
| **例** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>ディレクトリをダウンロードする

|    |     |
|--------|-----------|
| **構文** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>" "<local-directory-path>" --recursive` |
| **例** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"  --recursive` |

この例では結果的に、ダウンロードしたファイルがすべて含まれる `C:\myDirectory\myFileShareDirectory` という名前のディレクトリが生成されます。

### <a name="download-the-contents-of-a-directory"></a>ディレクトリの内容をダウンロードする

ワイルドカード記号 (*) を使用することで、ディレクトリ自体をコピーせずにディレクトリの内容をダウンロードできます。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>" "<local-directory-path>/"` |
| **例** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"` |

> [!NOTE]
> すべてのサブディレクトリのファイルをダウンロードするには、`--recursive` フラグを追加します。

## <a name="next-steps"></a>次の手順

以下の記事にサンプルがあります。

- [AzCopy を使ってみる](storage-use-azcopy-v10.md)

- [AzCopy と BLOB ストレージでデータを転送する](storage-use-azcopy-blobs.md)

- [AzCopy と Amazon S3 バケットでデータを転送する](storage-use-azcopy-s3.md)

- [AzCopy の構成、最適化、トラブルシューティング](storage-use-azcopy-configure.md)