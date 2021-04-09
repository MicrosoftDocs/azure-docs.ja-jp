---
title: AzCopy を使用して Google Cloud Storage から Azure Storage にデータをコピーする | Microsoft Docs
description: AzCopy を使用して Google Cloud Storage から Azure Storage にデータをコピーします。 AzCopy は、ストレージ アカウント間の BLOB またはファイル コピーに利用できるコマンドライン ユーティリティです。
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: c0f030683954ede013f769bf8584e6cf82bab69f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103555166"
---
# <a name="copy-data-from-google-cloud-storage-to-azure-storage-by-using-azcopy-preview"></a>AzCopy を使用して Google Cloud Storage から Azure Storage にデータをコピーする (プレビュー)

AzCopy は、ストレージ アカウント間の BLOB またはファイル コピーに利用できるコマンドライン ユーティリティです。 この記事は、AzCopy を使用して Google Cloud Storage から Azure Blob Storage にオブジェクト、ディレクトリ、バケットをコピーする場合に役立ちます。

> [!IMPORTANT]
> Google Cloud Storage から Azure Storage へのデータのコピーは、現在パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="choose-how-youll-provide-authorization-credentials"></a>認証資格情報の提供方法を選択する

* Azure Storage による承認を行うには、Azure Active Directory (AD) または Shared Access Signature (SAS) トークンを使用します。

* Google Cloud Storage による承認を行うには、サービス アカウント キーを使用します。

### <a name="authorize-with-azure-storage"></a>Azure Storage による認証

AzCopy のダウンロード方法と、ストレージ サービスに認証資格情報を与える方法については、[AzCopy の作業開始](storage-use-azcopy-v10.md)に関するページをご覧ください。

> [!NOTE] 
> この記事の例では、Azure Active Directory (Azure AD) を使用して認証資格情報を指定していることを前提としています。
>
> SAS トークンを使用して BLOB データへのアクセスを承認する場合、各 AzCopy コマンドのリソース URL の先頭にそのトークンを追加できます。 (例: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`)。

### <a name="authorize-with-google-cloud-storage"></a>Google Cloud Storage を使用して承認する

Google Cloud Storage による承認を行うには、サービス アカウント キーを使用します。 サービス アカウント キーの作成方法の詳細については、「[サービス アカウント キーの作成と管理](https://cloud.google.com/iam/docs/creating-managing-service-account-keys)」を参照してください。

サービス キーを取得したら、`GOOGLE_APPLICATION_CREDENTIALS` 環境変数をサービス アカウント キー ファイルへの絶対パスに設定します。

| オペレーティング システム | コマンド  |
|--------|-----------|
| **Windows** | `set GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **Linux** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **macOS** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |

## <a name="copy-objects-directories-and-buckets"></a>オブジェクト、ディレクトリ、バケットをコピーする

AzCopy では、[Put Block From URL](/rest/api/storageservices/put-block-from-url) API が使用されます。そのため、データは Google Cloud Storage とストレージ サーバーの間で直接コピーされます。 これらのコピー操作では、コンピューターのネットワーク帯域幅が使用されません。

> [!TIP]
> このセクションの例では、単一引用符 ('') でパス引数を囲みます。 Windows コマンド シェル (cmd.exe) を除き、すべてのコマンド シェルで単一引用符を使用します。 Windows コマンド シェル (cmd.exe) を使用している場合は、単一引用符 ('') ではなく、二重引用符 ("") でパス引数を囲みます。

 これらの例は、階層型名前空間があるアカウントでも機能します。 [Data Lake Storage のマルチプロトコル アクセス](../blobs/data-lake-storage-multi-protocol-access.md)では、これらのアカウントで同じ URL 構文 (`blob.core.windows.net`) を使用できます。 

### <a name="copy-an-object"></a>オブジェクトをコピーする

階層型名前空間があるアカウントに同じ URL 構文 (`blob.core.windows.net`) を使用します。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'` |
| **例** | `azcopy copy 'https://storage.cloud.google.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |
| **例** (階層型名前空間) | `azcopy copy 'https://storage.cloud.google.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |


### <a name="copy-a-directory"></a>ディレクトリをコピーする

階層型名前空間があるアカウントに同じ URL 構文 (`blob.core.windows.net`) を使用します。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **例** | `azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **例** (階層型名前空間)| `azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

> [!NOTE]
> この例では、`--recursive` フラグを追加して、すべてのサブディレクトリ内のファイルをコピーします。

### <a name="copy-the-contents-of-a-directory"></a>ディレクトリの内容をコピーする

ワイルドカード記号 (*) を使用することで、ディレクトリ自体をコピーせずにディレクトリの内容をコピーできます。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>/*' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **例** | `azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **例** (階層型名前空間)| `azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-a-cloud-storage-bucket"></a>クラウド ストレージ バケットをコピーする

階層型名前空間があるアカウントに同じ URL 構文 (`blob.core.windows.net`) を使用します。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://storage.cloud.google.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **例** | `azcopy copy 'https://storage.cloud.google.com/mybucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **例** (階層型名前空間)| `azcopy copy 'https://storage.cloud.google.com/mybucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |

### <a name="copy-all-buckets-in-a-google-cloud-project"></a>Google Cloud プロジェクト内のすべてのバケットをコピーする 

最初に、`GOOGLE_CLOUD_PROJECT` を Google Cloud プロジェクトのプロジェクト ID に設定します。

階層型名前空間があるアカウントに同じ URL 構文 (`blob.core.windows.net`) を使用します。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://storage.cloud.google.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **例** | `azcopy copy 'https://storage.cloud.google.com/' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **例** (階層型名前空間)| `azcopy copy 'https://storage.cloud.google.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |

### <a name="copy-a-subset-of-buckets-in-a-google-cloud-project"></a>Google Cloud プロジェクト内のバケットのサブセットをコピーする 

最初に、`GOOGLE_CLOUD_PROJECT` を Google Cloud プロジェクトのプロジェクト ID に設定します。

バケット名にワイルドカード記号 (*) を使用して、バケットのサブセットをコピーします。 階層型名前空間があるアカウントに同じ URL 構文 (`blob.core.windows.net`) を使用します。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://storage.cloud.google.com/<bucket*name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **例** | `azcopy copy 'https://storage.cloud.google.com/my*bucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **例** (階層型名前空間)| `azcopy copy 'https://storage.cloud.google.com/my*bucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |

## <a name="handle-differences-in-bucket-naming-rules"></a>バケットの名前付け規則の違いに対処する

Google Cloud Storage では、Azure BLOB コンテナーと比べると異なるバケット名の名前付け規則があります。 詳しくは、[こちら](https://cloud.google.com/storage/docs/naming-buckets)をご覧ください。 バケットのグループを Azure ストレージ アカウントにコピーする場合は、名前付けの違いのためにコピー操作が失敗することがあります。

AzCopy は、発生する可能性のある最も一般的な 3 つの問題を処理します。ピリオドが含まれているバケットと、連続するハイフンが含まれているバケッと、アンダースコアが含まれているバケットです。 Google Cloud Storage バケットの名前にはピリオドと連続するハイフンを含めることができますが、Azure 内のコンテナーには含めることができません。 AzCopy では、期間はハイフンに置き換えられ、連続するハイフンは、連続するハイフンの数を表す数に置き換えられます (たとえば、`my----bucket` という名前のバケットは `my-4-bucket` になります)。 バケット名にアンダースコア (`_`) が含まれている場合、AzCopy を実行するとアンダースコアがハイフンに置き換えられます (たとえば、`my_bucket` という名前のバケットは `my-bucket` になります。 

## <a name="handle-differences-in-object-naming-rules"></a>オブジェクトの名前付け規則の違いに対処する

Google Cloud Storage では、Azure BLOB コンテナーと比べると異なるオブジェクト名の名前付け規則があります。 詳しくは、[こちら](https://cloud.google.com/storage/docs/naming-objects)をご覧ください。

Azure Storage では、オブジェクト名 (または仮想ディレクトリ パス内の任意のセグメント) が連続するドットで終わることは許可されていません (たとえば `my-bucket...`)。 末尾のドットは、コピー操作の実行時にトリミングされます。 

## <a name="handle-differences-in-object-metadata"></a>オブジェクトのメタデータの違いに対処する

Google Cloud Storage と Azure では、オブジェクト キーの名前にさまざまな文字セットを使用できます。 Google Cloud Storage のメタデータについては、[こちら](https://cloud.google.com/storage/docs/metadata)を参照してください。 Azure 側では、BLOB オブジェクト キーは [C# 識別子](/dotnet/csharp/language-reference/)の名前付け規則に従います。

AzCopy の `copy` コマンドの一部として、オプションの `s2s-handle-invalid-metadata` フラグの値を指定することができます。これは、ファイルのメタデータに互換性のないキー名が含まれているファイルを処理する方法を指定します。 次の表は、それぞれのフラグ値の説明です。

| フラグ値 | 説明  |
|--------|-----------|
| **ExcludeIfInvalid** | (既定のオプション) メタデータは転送されたオブジェクトに含まれていません。 AzCopy は警告を記録します。 |
| **FailIfInvalid** | オブジェクトはコピーされません。 AzCopy はエラーを記録し、そのエラーを、転送の概要に表示される失敗の数に含めます。  |
| **RenameIfInvalid**  | AzCopy は無効なメタデータ キーを解決し、解決されたメタデータのキーと値のペアを使用してオブジェクトを Azure にコピーします。 AzCopy でオブジェクト キーの名前を変更するために実行する正確な手順については、以下の「[AzCopy でオブジェクト キーの名前を変更する方法](#rename-logic)」のセクションを参照してください。 AzCopy でキーの名前を変更できない場合、オブジェクトはコピーされません。 |

<a id="rename-logic"></a>

### <a name="how-azcopy-renames-object-keys"></a>AzCopy でオブジェクト キーの名前を変更する方法

AzCopy では次の手順が実行されます。

1. 無効な文字を '_' に置き換えます。

2. 文字列 `rename_` を新しい有効なキーの先頭に追加します。

   このキーは、元のメタデータ **値** を保存するために使用されます。

3. 文字列 `rename_key_` を新しい有効なキーの先頭に追加します。
   このキーは、元のメタデータの無効な **キー** を保存するために使用されます。
   メタデータ キーは BLOB ストレージ サービスに値として保持されるため、このキーを使用して、Azure 側でメタデータを試して復元することができます。

## <a name="next-steps"></a>次のステップ

以下の記事にサンプルがあります。

- [AzCopy を使ってみる](storage-use-azcopy-v10.md)

- [データの転送](storage-use-azcopy-v10.md#transfer-data)

- [AzCopy の構成、最適化、トラブルシューティング](storage-use-azcopy-configure.md)
