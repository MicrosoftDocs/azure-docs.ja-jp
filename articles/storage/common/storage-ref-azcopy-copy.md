---
title: azcopy copy| Microsoft Docs
description: この記事では、azcopy copy コマンドに関する参照情報を提供します。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c15d188e333bea5e74fa65d2bbdf38ae7fadc246
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70196745"
---
# <a name="azcopy-copy"></a>azcopy copy

ソース データをコピー先の場所にコピーします。

## <a name="synopsis"></a>概要

サポートされている方向は次のとおりです。

- ローカル <-> Azure BLOB (SAS または OAuth 認証)
- ローカル <-> Azure ファイル (共有/ディレクトリ SAS 認証)
- ローカル <-> ADLS Gen 2 (SAS、OAuth、または SharedKey 認証)
- Azure BLOB (SAS またはパブリック) <-> Azure BLOB (SAS または OAuth 認証)
- Azure ファイル (SAS) <-> Azure ブロック BLOB (SAS または OAuth 認証)
- AWS S3 (アクセス キー) <-> Azure ブロック BLOB (SAS または OAuth 認証)

詳細については、例を参照してください。

### <a name="advanced"></a>詳細

AzCopy は、ファイルの拡張子またはコンテンツ (拡張子が指定されていない場合) に基づいて、ローカル ディスクからアップロードするときにファイルのコンテンツの種類を自動的に検出します。

組み込みのルックアップ テーブルは小さいですが、Unix では、次の 1 つ以上の名前において使用可能な場合は、ローカル システムの mime.types ファイルによって拡張されます。

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

Windows では、MIME の種類はレジストリから抽出されます。 この機能は、フラグを使用して無効にできます。 フラグに関するセクションを参照してください。

> [!IMPORTANT]
> コマンド ラインを使用して環境変数を設定した場合、その変数はコマンド ラインの履歴で読み取ることができます。 資格情報を含む変数をコマンド ラインの履歴から消去することを検討してください。 変数が履歴に表示されないようにするために、ユーザーに資格情報の入力を要求し、環境変数を設定するためのスクリプトを使用できます。

```azcopy
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>例

OAuth 認証を使用して 1 つのファイルをアップロードします。

AzCopy にまだログインしていない場合は、次のコマンドを実行する前に `azcopy login` コマンドを使用してください。

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

上記と同じですが、今度はファイル コンテンツの MD5 ハッシュも計算し、BLOB の Content-MD5 プロパティとして保存します。

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

SAS を使用して 1 つのファイルをアップロードします。

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

パイプを使用して SAS で 1 つのファイルをアップロードします (ブロック BLOB のみ):

```azcopy
cat "/path/to/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

SAS を使用してディレクトリ全体をアップロードします。

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

or

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --put-md5
```

ワイルドカードを使用して SAS でファイルのセットをアップロードします。

```azcopy
azcopy cp "/path/*foo/*bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

ワイルドカードを使用して SAS でファイルとディレクトリをアップロードします。

```azcopy
azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

OAuth 認証を使用して 1 つのファイルをダウンロードします。

AzCopy にまだログインしていない場合は、次のコマンドを実行する前に `azcopy login` コマンドを使用してください。

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"
```

SAS を使用して 1 つのファイルをダウンロードします。

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "/path/to/file.txt"
```

パイプを使用して SAS で 1 つのファイルをダウンロードします (ブロック BLOB のみ):

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" > "/path/to/file.txt"
```

SAS を使用してディレクトリ全体をダウンロードします。

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive=true
```

ワイルドカードを使用して SAS でファイルのセットをダウンロードします。

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/foo*?[SAS]" "/path/to/dir"
```

ワイルドカードを使用して SAS でファイルとディレクトリをダウンロードします。

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/foo*?[SAS]" "/path/to/dir" --recursive=true
```

SAS を使用する 1 つの BLOB を SAS を使用する別の BLOB にコピーします。

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

SAS を使用する 1 つの BLOB を OAuth トークンを使用する別の BLOB にコピーします。

AzCopy にまだログインしていない場合は、次のコマンドを実行する前に `azcopy login` コマンドを使用してください。 OAuth トークンは、コピー先ストレージ アカウントにアクセスするために使用されます。

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"
```

SAS を使用する BLOB 仮想ディレクトリから SAS を使用する別の BLOB 仮想ディレクトリにディレクトリ全体をコピーします。

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

SAS を使用する BLOB アカウントから SAS を使用する別の BLOB アカウントにアカウント データ全体をコピーします。

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net?[SAS]" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true
```

アクセス キーを使用する S3 から SAS を使用する BLOB に 1 つのオブジェクトをコピーします。

S3 ソースの環境変数 AWS_ACCESS_KEY_ID と AWS_SECRET_ACCESS_KEY を設定します。

```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

アクセス キーを使用する S3 から SAS を使用する BLOB 仮想ディレクトリにディレクトリ全体をコピーします。

```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

S3 での [folder] の意味については、 https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html を参照してください。 S3 ソースの環境変数 AWS_ACCESS_KEY_ID と AWS_SECRET_ACCESS_KEY を設定します。

アクセス キーを使用する S3 サービスのすべてのバケットを、SAS を使用する BLOB アカウントにコピーします。

S3 ソースの環境変数 AWS_ACCESS_KEY_ID と AWS_SECRET_ACCESS_KEY を設定します。

```azcopy
azcopy cp "https://s3.amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true
```

アクセス キーを使用する S3 リージョンのすべてのバケットを、SAS を使用する BLOB アカウントにコピーします。

```azcopy
azcopy cp "https://s3-[region].amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true
```

S3 ソースの環境変数 AWS_ACCESS_KEY_ID と AWS_SECRET_ACCESS_KEY を設定します。

## <a name="options"></a>オプション

|オプション|説明|
|--|--|
|--blob-type string|コピー先の BLOB の種類を定義します。 これは、BLOB をアップロードする場合と、アカウント間でコピーする場合に使用されます (既定では "None")。|
|--block-blob-tier string|この BLOB 層を使用して Azure Storage にブロック BLOB をアップロードします。 (既定値: "None")|
|--block-size-mb float |Azure Storage にアップロードする場合と、Azure Storage からダウンロードする場合に、このブロック サイズ (MiB で指定) を使用します。 既定値は、ファイル サイズに基づいて自動的に計算されます。 小数を使用できます (例: 0.25)。|
|--cache-control string|Cache-Control ヘッダーを設定します。 ダウンロード時に返されます。|
|--check-md5 string|ダウンロード時に MD5 ハッシュを検証する厳密さを指定します。 ダウンロード時にのみ使用できます。 使用できるオプションは NoCheck、LogOnly、FailIfDifferent、FailIfDifferentOrMissing です。 (既定値は "FailIfDifferent")|
|--content-disposition string|content-disposition ヘッダーを設定します。 ダウンロード時に返されます。|
|--content-encoding string|content-encoding ヘッダーを設定します。 ダウンロード時に返されます。|
|--content-language string|content-language ヘッダーを設定します。 ダウンロード時に返されます。|
|--content-type string |ファイルのコンテンツ タイプを指定します。 no-guess-mime-type を暗黙的に示します。 ダウンロード時に返されます。|
|--exclude string|コピー時にこれらのファイルを除外します。 \* の使用をサポートします。|
|--exclude-blob-type string|必要に応じて、コンテナーまたはアカウントから BLOB をコピーするときに除外する BLOB の種類 (BlockBlob/PageBlob/AppendBlob) を指定します。 このフラグの使用は、Azure 以外のサービスからサービスにデータをコピーする場合には適用されません。 複数の BLOB は ";" で区切る必要があります。|
|--follow-symlinks|ローカル ファイル システムからアップロードするときにシンボリック リンクに従います。|
|--from-to string|必要に応じて、コピー元とコピー先の組み合わせを指定します。 次に例を示します。LocalBlob、BlobLocal、LocalBlobFS。|
|-h, --help|copy コマンドのヘルプ コンテンツを表示します。 |
|--log-level string|ログ ファイルのログの詳細度を定義します。使用できるレベルは次のとおりです。INFO (すべての要求/応答)、WARNING (遅い応答)、ERROR (失敗した要求のみ)、NONE (出力ログなし)。 (既定値は "INFO")。|
|--metadata string|これらのキーと値のペアをメタデータとして Azure Storage にアップロードします。|
|--no-guess-mime-type|AzCopy がファイルの拡張子または内容に基づいてコンテンツ タイプを検出しないようにします。|
|--overwrite|このフラグが true に設定されている場合は、コピー先の競合するファイルまたは BLOB を上書きします。 (既定値は true)。|
|--page-blob-tier string |この BLOB 層を使用して Azure Storage にページ BLOB をアップロードします。 (既定値: "None")。|
|--preserve-last-modified-time|コピー先がファイル システムの場合にのみ使用できます。|
|--put-md5|各ファイルの MD5 ハッシュを作成し、ハッシュをコピー先の BLOB またはファイルの Content-MD5 プロパティとして保存します。 (既定では、ハッシュは作成されません)。アップロード時にのみ使用できます。|
|--recursive|ローカル ファイル システムからアップロードするときにサブディレクトリを再帰的に検索します。|
|--s2s-detect-source-changed|列挙後にソースが変更されたかどうかを確認します。 S2S コピーの場合、ソースはリモート リソースであるため、ソースが変更されたかどうかを検証するには、追加の要求コストが必要です。|
|--s2s-handle-invalid-metadata string |無効なメタデータ キーの処理方法を指定します。 使用できるオプションは ExcludeIfInvalid、FailIfInvalid、RenameIfInvalid です。 (既定値は "ExcludeIfInvalid")。|
|--s2s-preserve-access-tier|サービス間のコピー中にアクセス層を保持します。 「[Azure Blob storage: ホット、クール、およびアーカイブ アクセス層](../blobs/storage-blob-storage-tiers.md)」を参照して、コピー先ストレージ アカウントでアクセス層の設定がサポートされていることを確認してください。 アクセス層の設定がサポートされていない場合は、s2sPreserveAccessTier = false を使用してアクセス層のコピーをバイパスしてください。  (既定値は true)。|
|--s2s-preserve-properties|サービス間のコピー中にすべてのプロパティを保持します。 S3 と Azure ファイルの非単一ファイル ソースの場合、リスト操作はオブジェクトとファイルの完全なプロパティを返しません。完全なプロパティを保持するために、AzCopy では、オブジェクトとファイルごとに 1 つの追加の要求を送信する必要があります。 (既定値は true)。|

## <a name="options-inherited-from-parent-commands"></a>親コマンドから継承されるオプション

|オプション|説明|
|---|---|
|--cap-mbps uint32|転送速度を制限します (メガビット/秒)。 瞬間的なスループットは、上限と若干異なる場合があります。 このオプションを 0 に設定した場合、または省略した場合、スループットは制限されません。|
|--output-type string|コマンドの出力形式。 選択肢には、text、json などがあります。 既定値は "text" です。|

## <a name="see-also"></a>関連項目

- [azcopy](storage-ref-azcopy.md)
