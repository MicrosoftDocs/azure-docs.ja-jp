---
title: AzCopy v10 を使用して Azure Blob Storage をコピー先またはコピー元としてデータを転送する | Microsoft Docs
description: この記事には、コンテナーの作成、ファイルのコピー、ローカル ファイル システムとコンテナー間のディレクトリの同期に役立つ一連の AzCopy サンプル コマンドが含まれています。
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: fea9e79986e45127ad4918ed62bd8bf8dc782133
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67125801"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>AzCopy と Blob Storage でデータを転送する

AzCopy は、ストレージ アカウント間のデータ コピーに利用できるコマンドライン ユーティリティです。 この記事には、Blob Storage で使用するサンプル コマンドが含まれています。

## <a name="get-started"></a>作業開始

AzCopy のダウンロード方法と、ストレージ サービスに認証資格情報を与える方法については、[AzCopy の作業開始](storage-use-azcopy-v10.md)に関するページをご覧ください。

> [!NOTE]
> この記事の例では、`AzCopy login` コマンドを使用して ID を認証したと想定しています。 AzCopy では次に、Blob Storage のデータへのアクセスを承認するために、Azure AD アカウントが使用されます。
>
> SAS トークンを使用して BLOB データへのアクセスを承認する場合、各 AzCopy コマンドのリソース URL の先頭にそのトークンを追加できます。
>
> (例: `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>"`)。

## <a name="create-a-container"></a>コンテナーを作成する

AzCopy `make` コマンドを使用し、コンテナーを作成できます。 このセクションの例では、`mycontainer` という名前のコンテナーが作成されます。

|    |     |
|--------|-----------|
| **構文** | `azcopy make "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>` |
| **例** | `azcopy make "https://mystorageaccount.blob.core.windows.net/mycontainer"` |
| **例** (階層型名前空間) | `azcopy make "https://mystorageaccount.dfs.core.windows.net/mycontainer"` |

## <a name="upload-files"></a>ファイルをアップロードする

AzCopy `copy` コマンドを使用して、ローカル コンピューターからファイルやディレクトリをアップロードすることができます。

このセクションには、次の例が含まれています。

> [!div class="checklist"]
> * ファイルをアップロードする
> * ディレクトリをアップロードする
> * ワイルドカード文字を使用してファイルをアップロードする

> [!NOTE]
> AzCopy では、ファイルの md5 ハッシュ コードが自動的に計算されたり、保存されたりすることはありません。 AzCopy にそれを行わせる場合、各コピー コマンドの先頭に `--put-md5` フラグを追加します。 そうすることで、BLOB がダウンロードされたとき、AzCopy では、ダウンロードするデータの MD5 ハッシュが計算され、BLOB の `Content-md5` プロパティに格納されている MD5 ハッシュが計算されたハッシュに一致するかどうかが検証されます。

### <a name="upload-a-file"></a>ファイルをアップロードする

|    |     |
|--------|-----------|
| **構文** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>"` |
| **例** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |
| **例** (階層型名前空間) | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt"` |

> [!NOTE]
> 既定では、AzCopy はデータをブロック BLOB にアップロードします。 追加 BLOB またはページ BLOB としてファイルをアップロードするには、`--blob-type=[BlockBlob|PageBlob|AppendBlob]` フラグを使用します。

### <a name="upload-a-directory"></a>ディレクトリをアップロードする

この例では、ディレクトリ (とそのディレクトリ内のすべてのファイル) が BLOB コンテナーにコピーされます。 その結果、同じ名前でコンテナーにディレクトリが生成されます。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **例** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **例** (階層型名前空間) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer" --recursive` |

コンテナー内のディレクトリにコピーするには、コマンド文字列でそのディレクトリの名前を指定します。

|    |     |
|--------|-----------|
| **例** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory" --recursive` |
| **例** (階層型名前空間) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory" --recursive` |

コンテナーに存在しないディレクトリの名前を指定すると、AzCopy は、その名前で新しいディレクトリを作成します。

### <a name="upload-the-contents-of-a-directory"></a>ディレクトリの内容をアップロードする

ワイルドカード記号 (*) を使用することで、ディレクトリ自体をコピーせずにディレクトリの内容をアップロードできます。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy "<local-directory-path>\*" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>` |
| **例** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory"` |
| **例** (階層型名前空間) | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory"` |

> [!NOTE]
> すべてのサブディレクトリのファイルをアップロードするには、`--recursive` フラグを追加します。

## <a name="download-files"></a>ファイルをダウンロードする

AzCopy `copy` コマンドを使用して、BLOB、ディレクトリ、コンテナーをローカル コンピューターにダウンロードできます。

このセクションには、次の例が含まれています。

> [!div class="checklist"]
> * ファイルをダウンロードする
> * ディレクトリをダウンロードする
> * ワイルドカード文字を使用してファイルをダウンロードする

> [!NOTE]
> BLOB の `Content-md5` プロパティ値にハッシュが含まれる場合、AzCopy では、ダウンロードするデータの MD5 ハッシュが計算され、BLOB の `Content-md5` プロパティに格納されている MD5 ハッシュが計算されたハッシュに一致するかどうかが検証されます。 これらの値が一致しない場合、`--check-md5=NoCheck` または `--check-md5=LogOnly` をコピー コマンドに追加してこの動作をオーバーライドしない限り、ダウンロードは失敗します。

### <a name="download-a-file"></a>ファイルをダウンロードする

|    |     |
|--------|-----------|
| **構文** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>" "<local-file-path>"` |
| **例** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |
| **例** (階層型名前空間) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>ディレクトリをダウンロードする

|    |     |
|--------|-----------|
| **構文** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>" "<local-directory-path>" --recursive` |
| **例** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |
| **例** (階層型名前空間) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |

この例を実行すると、ダウンロードしたファイルがすべて含まれる `C:\myDirectory\myBlobDirectory` という名前のディレクトリが生成されます。

### <a name="download-the-contents-of-a-directory"></a>ディレクトリの内容をダウンロードする

ワイルドカード記号 (*) を使用することで、ディレクトリ自体をコピーせずにディレクトリの内容をダウンロードできます。

> [!NOTE]
> 現在のところ、このシナリオは、階層型名前空間のないアカウントでのみサポートされています。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"` |
| **例** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*" "C:\myDirectory"` |

> [!NOTE]
> すべてのサブディレクトリのファイルをダウンロードするには、`--recursive` フラグを追加します。

## <a name="copy-blobs-between-storage-accounts"></a>ストレージ アカウント間で BLOB をコピーする

AzCopy を使用し、BLOB を他のストレージ アカウントにコピーできます。 コピー操作は同期しているため、コマンドが返されると、それはすべてのファイルがコピーされていることを示します。

> [!NOTE]
> 現在のところ、このシナリオは、階層型名前空間のないアカウントでのみサポートされています。

AzCopy では、[URL からブロックの配置](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API が使用されます。そのため、データはストレージ サーバー間で直接コピーされます。 これらのコピー操作では、コンピューターのネットワーク帯域幅が使用されません。

このセクションには、次の例が含まれています。

> [!div class="checklist"]
> * 別のストレージ アカウントに BLOB をコピーする
> * 別のストレージ アカウントにディレクトリをコピーする
> * 別のストレージ アカウントにコンテナーをコピーする
> * すべてのコンテナー、ディレクトリ、ファイルを別のストレージ アカウントにコピーする

### <a name="copy-a-blob-to-another-storage-account"></a>別のストレージ アカウントに BLOB をコピーする

|    |     |
|--------|-----------|
| **構文** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>"` |
| **例** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |

### <a name="copy-a-directory-to-another-storage-account"></a>別のストレージ アカウントにディレクトリをコピーする

|    |     |
|--------|-----------|
| **構文** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>" --recursive` |
| **例** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myBlobDirectory" --recursive` |

### <a name="copy-a-containers-to-another-storage-account"></a>別のストレージ アカウントにコンテナーをコピーする

|    |     |
|--------|-----------|
| **構文** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **例** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-all-containers-directories-and-files-to-another-storage-account"></a>すべてのコンテナー、ディレクトリ、ファイルを別のストレージ アカウントにコピーする

|    |     |
|--------|-----------|
| **構文** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/" "https://<destination-storage-account-name>.blob.core.windows.net/" --recursive"` |
| **例** | `azcopy cp "https://mysourceaccount.blob.core.windows.net" "https://mydestinationaccount.blob.core.windows.net" --recursive` |

## <a name="synchronize-files"></a>ファイルを同期する

ローカル ファイル システムのコンテンツを BLOB コンテナーに同期できます。 同期は一方向です。 言い換えると、2 つのエンドポイントのいずれかを同期元として、いずれかを同期先として選択します。

> [!NOTE]
> 現在のところ、このシナリオは、階層型名前空間のないアカウントでのみサポートされています。 現行版の AzCopy では、他のコピー元とコピー先の間では同期されません (例:ファイル ストレージまたはアマゾン ウェブ サービス (AWS) S3 バケット)。

`sync` コマンドでは、ファイル名と最後に変更されたタイムスタンプが比較されます。 省略可能な `--delete-destination` フラグの値を `true` または `prompt` に設定すると、コピー元のディレクトリにファイルがもう存在しなくなると、コピー先のディレクトリからそれらのファイルが削除されます。

`--delete-destination` フラグを `true` に設定すると、AzCopy では、プロンプトを与えずにファイルが削除されます。 AzCopy でファイルが削除される前にプロンプトを表示する場合、`--delete-destination` フラグを `prompt` に設定します。

> [!NOTE]
> 誤削除を防ぐために、`--delete-destination=prompt|true` フラグを使用する前に[論理的な削除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)機能を有効にしてください。

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>ローカル ファイル システムへの変更を使用してコンテナーを更新する

この場合、コンテナーが宛先であり、ローカル ファイル システムがソースです。

|    |     |
|--------|-----------|
| **構文** | `azcopy sync "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **例** | `azcopy sync "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>コンテナーへの変更を使用してローカル ファイル システムを更新する

この場合、ローカル ファイル システムが宛先であり、コンテナーがソースです。

|    |     |
|--------|-----------|
| **構文** | `azcopy sync "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" "C:\myDirectory" --recursive` |
| **例** | `azcopy sync "https://mystorageaccount.blob.core.windows.net/mycontainer" "C:\myDirectory" --recursive` |
|

## <a name="next-steps"></a>次の手順

以下の記事にサンプルがあります。

- [AzCopy を使ってみる](storage-use-azcopy-v10.md)

- [チュートリアル:AzCopy を使用したオンプレミス データのクラウド ストレージへの移行](storage-use-azcopy-migrate-on-premises-data.md)

- [AzCopy とファイル ストレージでデータを転送する](storage-use-azcopy-files.md)

- [AzCopy と Amazon S3 バケットでデータを転送する](storage-use-azcopy-s3.md)

- [AzCopy の構成、最適化、トラブルシューティング](storage-use-azcopy-configure.md)