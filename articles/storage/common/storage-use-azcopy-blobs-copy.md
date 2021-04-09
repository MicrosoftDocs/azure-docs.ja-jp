---
title: AzCopy v10 を使用して Azure ストレージ アカウント間で BLOB をコピーする | Microsoft Docs
description: この記事には、ストレージ アカウント間で BLOB をコピーするのに役立つ AzCopy サンプル コマンドのコレクションが含まれています。
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 524e3f70f9588cfae2c739722fc1a44e683f9a7f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97617291"
---
# <a name="copy-blobs-between-azure-storage-accounts-by-using-azcopy-v10"></a>AzCopy v10 を使用して Azure ストレージ アカウント間で BLOB をコピーする

AzCopy v10 コマンドライン ユーティリティを使用して、ストレージ アカウント間で BLOB、ディレクトリ、コンテナーをコピーできます。 

ファイルのアップロード、BLOB のダウンロード、および BLOB ストレージとの同期など、他の種類のタスクの例については、この記事の「[次のステップ](#next-steps)」セクションに記載されているリンクを参照してください。

AzCopy では、[サーバー間](/rest/api/storageservices/put-block-from-url) [API](/rest/api/storageservices/put-page-from-url) が使用されます。そのため、データはストレージ サーバー間で直接コピーされます。 これらのコピー操作では、コンピューターのネットワーク帯域幅が使用されません。

AzCopy のダウンロード方法と、ストレージ サービスに認証資格情報を提供する方法については、「[AzCopy を使ってみる](storage-use-azcopy-v10.md)」を参照してください。 

## <a name="guidelines"></a>ガイドライン

AzCopy コマンドには次のガイドラインを適用します。 

- クライアントは、ソース ストレージ アカウントと宛先ストレージ アカウントの両方へのネットワーク アクセスを持っている必要があります。 各ストレージ アカウントのネットワーク設定を構成する方法の詳細については、「[Azure Storage ファイアウォールおよび仮想ネットワークを構成する](storage-network-security.md?toc=/azure/storage/blobs/toc.json)」を参照してください。

- 各ソース URL に SAS トークンを追加します。 

  Azure Active Directory (Azure AD) を使用して認証資格情報を提供する場合、SAS トークンを省略できるのは宛先 URL だけです。 宛先アカウントに適切なロールが設定されていることを確認します。 「[オプション 1: Azure Active Directory を使用する](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory)」を参照してください。 

  この記事の例では、Azure AD を使用して ID を認証していることを前提としているため、例では接続先 URL の SAS トークンが省略されています。

-  Premium ブロック BLOB ストレージ アカウントにコピーする場合は、`s2s-preserve-access-tier` を `false` に設定することで (例: `--s2s-preserve-access-tier=false`)、コピー操作から BLOB のアクセス層を除外します。 Premium ブロック BLOB ストレージ アカウントでは、アクセス層はサポートされません。 

- 階層型名前空間を持つアカウントとの間でコピーを行う場合は、URL 構文で `dfs.core.windows.net` ではなく `blob.core.windows.net` を使用します。 [Data Lake Storage のマルチプロトコル アクセス](../blobs/data-lake-storage-multi-protocol-access.md)では、`blob.core.windows.net` を使用できます。これは、アカウントからアカウントへのコピー シナリオでサポートされている唯一の構文です。 

- `AZCOPY_CONCURRENCY_VALUE` 環境変数の値を設定することによって、コピー操作のスループットを上げることができます。 詳しくは、「[スループットを最適化する](storage-use-azcopy-configure.md#optimize-throughput)」をご覧ください。 

- ソース BLOB にインデックス タグがあり、それらのタグを保持する必要がある場合は、コピー先 BLOB に再適用する必要があります。 インデックス タグを設定する方法の詳細については、この記事の[インデックス タグがある BLOB を別のストレージ アカウントにコピーする](#copy-between-accounts-and-add-index-tags)方法に関するセクションを参照してください。

## <a name="copy-a-blob"></a>BLOB をコピーする

[azcopy copy](storage-ref-azcopy-copy.md) コマンドを使用して、BLOB を別のストレージ アカウントにコピーします。 

> [!TIP]
> この例では、パス引数を単一引用符 ('') で囲んでいます。 Windows コマンド シェル (cmd.exe) を除き、すべてのコマンド シェルで単一引用符を使用します。 Windows コマンド シェル (cmd.exe) を使用している場合は、単一引用符 ('') ではなく、二重引用符 ("") でパス引数を囲みます。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

コピー操作は同期しているため、コマンドが返されると、それはすべてのファイルがコピーされていることを示します。 

## <a name="copy-a-directory"></a>ディレクトリをコピーする

[azcopy copy](storage-ref-azcopy-copy.md) コマンドを使用して、ディレクトリを別のストレージ アカウントにコピーします。 

> [!TIP]
> この例では、パス引数を単一引用符 ('') で囲んでいます。 Windows コマンド シェル (cmd.exe) を除き、すべてのコマンド シェルで単一引用符を使用します。 Windows コマンド シェル (cmd.exe) を使用している場合は、単一引用符 ('') ではなく、二重引用符 ("") でパス引数を囲みます。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

コピー操作は同期しているため、コマンドが返されると、それはすべてのファイルがコピーされていることを示します。

## <a name="copy-a-container"></a>コンテナーをコピーする

[azcopy copy](storage-ref-azcopy-copy.md) コマンドを使用して、コンテナーを別のストレージ アカウントにコピーします。

> [!TIP]
> この例では、パス引数を単一引用符 ('') で囲んでいます。 Windows コマンド シェル (cmd.exe) を除き、すべてのコマンド シェルで単一引用符を使用します。 Windows コマンド シェル (cmd.exe) を使用している場合は、単一引用符 ('') ではなく、二重引用符 ("") でパス引数を囲みます。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

コピー操作は同期しているため、コマンドが返されると、それはすべてのファイルがコピーされていることを示します。

## <a name="copy-containers-directories-and-blobs"></a>コンテナー、ディレクトリ、BLOB のコピー

[azcopy copy](storage-ref-azcopy-copy.md) コマンドを使用して、すべてのコンテナー、ディレクトリ、BLOB を別のストレージ アカウントにコピーします。

> [!TIP]
> この例では、パス引数を単一引用符 ('') で囲んでいます。 Windows コマンド シェル (cmd.exe) を除き、すべてのコマンド シェルで単一引用符を使用します。 Windows コマンド シェル (cmd.exe) を使用している場合は、単一引用符 ('') ではなく、二重引用符 ("") でパス引数を囲みます。

|    |     |
|--------|-----------|
| **構文** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

コピー操作は同期しているため、コマンドが返されると、それはすべてのファイルがコピーされていることを示します。

<a id="copy-between-accounts-and-add-index-tags"></a>

## <a name="copy-blobs-and-add-index-tags"></a>BLOB をコピーしてインデックス タグを追加する

BLOB を別のストレージ アカウントにコピーし、[BLOB インデックス タグ (プレビュー)](../blobs/storage-manage-find-blobs.md) を対象の BLOB に追加します。

Azure AD 承認を使用している場合は、セキュリティ プリンシパルに[ストレージ Blob データ所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)ロールが割り当てられているか、カスタム Azure ロールを使用して `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [Azure リソース プロバイダーの操作](../../role-based-access-control/resource-provider-operations.md#microsoftstorage)に対するアクセス許可が付与されている必要があります。 Shared Access Signature (SAS) トークンを使用している場合、そのトークンは、`t` SAS アクセス許可を使用して BLOB のタグへのアクセスを提供する必要があります。

タグを追加するには、URL でエンコードされたキーと値のペアと共に `--blob-tags` オプションを使用します。 

たとえば、キー `my tag` と値 `my tag value` を追加するには、宛先パラメーターに `--blob-tags='my%20tag=my%20tag%20value'` を追加します。 

複数のインデックス タグを区切るには、アンパサンド (`&`) を使用します。  たとえば、キー `my second tag` と値 `my second tag value` を追加する場合、完全なオプション文字列は `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` になります。

`--blob-tags` オプションを使用する方法の例を次に示します。

> [!TIP]
> これらの例では、パス引数を単一引用符 ('') で囲んでいます。 Windows コマンド シェル (cmd.exe) を除き、すべてのコマンド シェルで単一引用符を使用します。 Windows コマンド シェル (cmd.exe) を使用している場合は、単一引用符 ('') ではなく、二重引用符 ("") でパス引数を囲みます。

|    |     |
|--------|-----------|
| **Blob** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **ディレクトリ** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **コンテナー** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **アカウント** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |

コピー操作は同期しているため、コマンドが返されると、それはすべてのファイルがコピーされていることを示します。

> [!NOTE]
> ソースのディレクトリ、コンテナー、またはアカウントを指定すると、コピー先にコピーされたすべての BLOB のタグは、コマンドで指定したものと同じになります。

## <a name="copy-with-optional-flags"></a>オプションのフラグを使用してコピーする

オプションのフラグを使用して、コピー操作を調整できます。 以下にいくつか例を示します。

|シナリオ|フラグ|
|---|---|
|ブロック、ページ、または追加 BLOB として BLOB をコピーします。|**--blob-type**=\[BlockBlob\|PageBlob\|AppendBlob\]|
|特定のアクセス層 (アーカイブ層など) にコピーします。|**--block-blob-tier**=\[None\|Hot\|Cool\|Archive\]|
|自動的にファイルを圧縮解除します。|**--decompress**=\[gzip\|deflate\]|

完全な一覧については、「[オプション](storage-ref-azcopy-copy.md#options)」を参照してください。 

## <a name="next-steps"></a>次のステップ

他の例については、次の記事を参照してください。

- [例:アップロード](storage-use-azcopy-blobs-upload.md)
- [例:ダウンロード](storage-use-azcopy-blobs-download.md)」をご覧ください
- [例:同期](storage-use-azcopy-blobs-synchronize.md)
- [例:Amazon S3 バケット](storage-use-azcopy-s3.md)
- [例:Azure Files](storage-use-azcopy-files.md)
- [チュートリアル:AzCopy を使用したオンプレミス データのクラウド ストレージへの移行](storage-use-azcopy-migrate-on-premises-data.md)
- [AzCopy の構成、最適化、トラブルシューティング](storage-use-azcopy-configure.md)