---
title: AzCopy v10 を使用して Amazon S3 バケットから Azure Storage にデータを転送する | Microsoft Docs
description: AzCopy と Amazon S3 バケットでデータを転送する
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 04/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 414573a90e5a7b7de845b65e6e88715a26024f10
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67125783"
---
# <a name="copy-data-from-amazon-s3-buckets-by-using-azcopy"></a>AzCopy を使用して Amazon S3 バケットからデータをコピーする

AzCopy は、ストレージ アカウント間の BLOB またはファイル コピーに利用できるコマンドライン ユーティリティです。 この記事は、AzCopy を使用して Amazon Web Services (AWS) S3 から Azure BLOB ストレージにオブジェクト、ディレクトリ、およびバケットをコピーする場合に役立ちます。

## <a name="choose-how-youll-provide-authorization-credentials"></a>認証資格情報の提供方法を選択する

* Azure Storage による承認を行うには、Azure Active Directory (AD) または Shared Access Signature (SAS) トークンを使用します。

* AWS S3 による承認を行うには、AWS アクセス キーとシークレット アクセス キーを使用します。

### <a name="authorize-with-azure-storage"></a>Azure Storage による認証

AzCopy のダウンロード方法と、ストレージ サービスに認証資格情報を提供する方法については、「[AzCopy を使ってみる](storage-use-azcopy-v10.md)」という記事をご覧ください。

> [!NOTE]
> この記事の例では、`AzCopy login` コマンドを使用して ID を認証したと想定しています。 AzCopy では次に、Blob Storage のデータへのアクセスを承認するために、Azure AD アカウントが使用されます。
>
> SAS トークンを使用して BLOB データへのアクセスを承認する場合、各 AzCopy コマンドのリソース URL の先頭にそのトークンを追加できます。
>
> (例: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`)。

### <a name="authorize-with-aws-s3"></a>AWS S3 による承認

AWS アクセス キーとシークレット アクセス キーを収集して、以下の環境変数を設定します。

| オペレーティング システム | command  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **MacOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>オブジェクト、ディレクトリ、バケットをコピーする

AzCopy では、[URL からブロックの配置](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API が使用されます。そのため、データは AWS S3 とストレージ サーバー間で直接コピーされます。 これらのコピー操作では、コンピューターのネットワーク帯域幅が使用されません。

> [!IMPORTANT]
> 現在、この機能はプレビュー段階にあります。 コピー操作後に S3 バケットからデータを削除する場合は、データを削除する前に、データがストレージ アカウントに適切にコピーされたことを確認します。

### <a name="copy-an-object"></a>オブジェクトをコピーする

|    |     |
|--------|-----------|
| **構文** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>/<object-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>"` |
| **例** | `azcopy cp "https://s3.amazonaws.com/mybucket/myobject" "https://mystorageaccount.blob.core.windows.net/mycontainer/myblob"` |

> [!NOTE]
> この記事の例では、AWS S3 バケットにパス形式の URL を使用します (例: `http://s3.amazonaws.com/<bucket-name>`)。 
>
> また、仮想ホスト形式の URL も使用できます (例: `http://bucket.s3.amazonaws.com`)。 
>
> バケットの仮想ホスティングの詳細については、「バケットの仮想ホスティング」(https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html) ) を参照してください。

### <a name="copy-a-directory"></a>ディレクトリをコピーする

|    |     |
|--------|-----------|
| **構文** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>/<directory-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>" --recursive=true` |
| **例** | `azcopy cp "https://s3.amazonaws.com/mybucket/mydirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory" --recursive=true` |

### <a name="copy-a-bucket"></a>バケットをコピーする

|    |     |
|--------|-----------|
| **構文** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true` |
| **例** | `azcopy cp "https://s3.amazonaws.com/mybucket" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>すべてのリージョン内のすべてのバケットをコピーする

|    |     |
|--------|-----------|
| **構文** | `azcopy cp "https://s3.amazonaws.com/" "https://<storage-account-name>.blob.core.windows.net" --recursive=true` |
| **例** | `azcopy cp "https://s3.amazonaws.com" "https://mystorageaccount.blob.core.windows.net" --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>特定の S3 リージョン内のすべてのバケットをコピーする

|    |     |
|--------|-----------|
| **構文** | `azcopy cp "https://s3-<region-name>.amazonaws.com/" "https://<storage-account-name>.blob.core.windows.net" --recursive=true` |
| **例** | `azcopy cp "https://s3-rds.eu-north-1.amazonaws.com" "https://mystorageaccount.blob.core.windows.net" --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>オブジェクトの名前付け規則の違いに対処する

AWS の S3 では、Azure BLOB コンテナーと比べると異なるバケット名の名前付け規則があります。 詳しくは、[こちら](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules)をご覧ください。 バケットのグループを Azure ストレージ アカウントにコピーする場合は、名前付けの違いのためにコピー操作が失敗することがあります。

AzCopy は、発生する可能性のある最も一般的な 2 つの問題を処理します。期間が含まれているバケットと、連続するハイフンが含まれているバケットです。 AWS S3 バケットの名前には期間と連続するハイフンを含めることができますが、Azure 内のコンテナーには含めることができません。 AzCopy では、期間はハイフンに置き換えられ、連続するハイフンは、連続するハイフンの数を表す数に置き換えられます (たとえば、`my----bucket` という名前のバケットは `my-4-bucket` になります)。 

また、AzCopy でファイルがコピーされるときには、名前の競合がチェックされ、その解決が試みられます。 たとえば、名前が `bucket-name` と `bucket.name` のバケットがある場合、AzCopy は `bucket.name` という名前のバケットをまず `bucket-name` に解決してから、次に `bucket-name-2` に解決します。

## <a name="handle-differences-in-object-metadata"></a>オブジェクトのメタデータの違いに対処する

AWS S3 と Azure では、オブジェクト キーの名前にさまざまな文字セットを使用できます。 AWS S3 で使用する文字の詳細については、[こちら](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys)をご覧ください。 Azure 側では、BLOB オブジェクト キーは [C# 識別子](https://docs.microsoft.com/dotnet/csharp/language-reference/)の名前付け規則に従います。

AzCopy の `copy` コマンドの一部として、オプションの `s2s-invalid-metadata-handle` フラグの値を指定することができます。これは、ファイルのメタデータに互換性のないキー名が含まれているファイルを処理する方法を指定します。 次の表は、それぞれのフラグ値の説明です。

| フラグ値 | 説明  |
|--------|-----------|
| **ExcludeIfInvalid** | (既定のオプション) メタデータは転送されたオブジェクトに含まれていません。 AzCopy は警告を記録します。 |
| **FailIfInvalid** | オブジェクトはコピーされません。 AzCopy はエラーを記録し、そのエラーを、転送の概要に表示される失敗の数に含めます。  |
| **RenameIfInvalid**  | AzCopy は無効なメタデータ キーを解決し、解決されたメタデータのキーと値のペアを使用してオブジェクトを Azure にコピーします。 AzCopy でオブジェクト キーの名前を変更するために実行する正確な手順については、以下の「[AzCopy でオブジェクト キーの名前を変更する方法](#rename-logic)」のセクションを参照してください。 AzCopy でキーの名前を変更できない場合、オブジェクトはコピーされません。 |

<a id="rename-logic" />

### <a name="how-azcopy-renames-object-keys"></a>AzCopy でオブジェクト キーの名前を変更する方法

AzCopy では次の手順が実行されます。

1. 無効な文字を '_' に置き換えます。

2. 文字列 `rename_` を新しい有効なキーの先頭に追加します。

   このキーは、元のメタデータ**値**を保存するために使用されます。

3. 文字列 `rename_key_` を新しい有効なキーの先頭に追加します。
   このキーは、元のメタデータの無効な**キー**を保存するために使用されます。
   メタデータ キーは BLOB ストレージ サービスに値として保持されるため、このキーを使用して、Azure 側でメタデータを試して復元することができます。

## <a name="next-steps"></a>次の手順

以下の記事にサンプルがあります。

- [AzCopy を使ってみる](storage-use-azcopy-v10.md)

- [AzCopy と BLOB ストレージでデータを転送する](storage-use-azcopy-blobs.md)

- [AzCopy とファイル ストレージでデータを転送する](storage-use-azcopy-files.md)

- [AzCopy の構成、最適化、トラブルシューティング](storage-use-azcopy-configure.md)