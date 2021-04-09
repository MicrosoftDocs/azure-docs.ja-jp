---
title: azcopy copy| Microsoft Docs
description: この記事では、azcopy copy コマンドに関する参照情報を提供します。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 03/08/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c676b92fd07c6e444aa22f25c48fdb1b1957ca7a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493766"
---
# <a name="azcopy-copy"></a>azcopy copy

ソース データをコピー先の場所にコピーします。

## <a name="synopsis"></a>概要

ソース データをコピー先の場所にコピーします。 サポートされている方向は次のとおりです。

  - ローカル <-> Azure BLOB (SAS または OAuth 認証)
  - ローカル <-> Azure Files (共有/ディレクトリ SAS 認証)
  - ローカル <-> Azure Data Lake Storage Gen 2 (SAS、OAuth、または共有キー認証)
  - Azure BLOB (SAS またはパブリック) -> Azure BLOB (SAS または OAuth 認証)
  - Azure BLOB (SAS またはパブリック) -> Azure Files (SAS)
  - Azure Files (SAS) -> Azure Files (SAS)
  - Azure Files (SAS) -> Azure BLOB (SAS または OAuth 認証)
  - アマゾン ウェブ サービス (AWS) S3 (アクセス キー) -> Azure ブロック BLOB (SAS または OAuth 認証)
  - Google Cloud Storage (サービス アカウント キー) -> Azure ブロック Blob (SAS または OAuth 認証) [プレビュー]

詳細については、この記事の「例」のセクションを参照してください。

## <a name="related-conceptual-articles"></a>関連する概念に関する記事

- [AzCopy を使ってみる](storage-use-azcopy-v10.md)
- [AzCopy と Blob Storage でデータを転送する](./storage-use-azcopy-v10.md#transfer-data)
- [AzCopy とファイル ストレージでデータを転送する](storage-use-azcopy-files.md)
- [AzCopy の構成、最適化、トラブルシューティング](storage-use-azcopy-configure.md)

## <a name="advanced"></a>詳細設定

AzCopy は、ローカル ディスクからファイルがアップロードされる際に、そのコンテンツの種類を自動的に検出します。 AzCopy は、ファイル拡張子またはコンテンツ (拡張子が指定されていない場合) に基づいてコンテンツの種類を検出します。

組み込みのルックアップ テーブルは小さいですが、Unix では、次の 1 つ以上の名前において使用可能な場合は、ローカル システムの `mime.types` ファイルによって拡張されます。

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

Windows では、MIME の種類はレジストリから抽出されます。 この機能は、フラグを使用して無効にできます。 詳細については、この記事のフラグ のセクションを参照してください。

コマンド ラインを使用して環境変数を設定した場合、その変数はコマンド ラインの履歴で読み取ることができます。 資格情報を含む変数をコマンド ラインの履歴から消去することを検討してください。 変数が履歴に表示されないようにするために、ユーザーに資格情報の入力を要求し、環境変数を設定するためのスクリプトを使用できます。

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>例

OAuth 認証を使用して 1 つのファイルをアップロードします。 AzCopy にまだログインしていない場合は、次のコマンドを実行する前に `azcopy login` コマンドを実行します。

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```
上記と同じですが、今度はファイル コンテンツの MD5 ハッシュも計算し、BLOB の Content-MD5 プロパティとして保存します。

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

SAS トークンを使用して 1 つのファイルをアップロードします。

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

SAS トークンとパイプを使用して 1 つのファイルをアップロードします (ブロック BLOB のみ)。
  
```azcopy
cat "/path/to/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]
```

SAS トークンを使用してディレクトリ全体をアップロードします。
  
```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

or

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive --put-md5
```

SAS トークンとワイルドカード文字 (*) を使用して、一連のファイルをアップロードします。
 
```azcopy
azcopy cp "/path/*foo/*bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

SAS トークンとワイルドカード文字 (*) を使用して、ファイルとディレクトリをアップロードします。

```azcopy
azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

ファイルとディレクトリを Azure Storage アカウントにアップロードし、クエリ文字列でエンコードされたタグを BLOB に設定します。 

- {key = "bla bla", val = "foo"}、{key = "bla bla 2", val = "bar"} の各タグを設定するには、次の構文を使用します。`azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --blob-tags="bla%20bla=foo&bla%20bla%202=bar"`
    
- キーと値は URL でエンコードされ、キーと値のペアはアンパサンド ('&') で区切られます

- BLOB にタグを設定するときに、SAS に追加のアクセス許可 (タグ用に 't') があります。これがないと、サービスから認可エラーが返されます。

OAuth 認証を使用して 1 つのファイルをダウンロードします。 AzCopy にまだログインしていない場合は、次のコマンドを実行する前に `azcopy login` コマンドを実行します。

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"
```

SAS トークンを使用して 1 つのファイルをダウンロードします。

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "/path/to/file.txt"
```

SAS トークンを使用し、次に出力をファイルにパイプして、1 つのファイルをダウンロードします (ブロック BLOB のみ)。
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" > "/path/to/file.txt"
``` 

SAS トークンを使用してディレクトリ全体をダウンロードします。
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive
``` 

URL でのワイルドカード文字 (*) の使用に関する注意事項:

URL でワイルドカード文字を使用するには、2 つの方法のみがサポートされます。 

- URL の最後のスラッシュ (/) の直後に 1 つ使用できます。 このようにワイルドカード文字を使用することで、ディレクトリ内のすべてのファイルが、サブディレクトリに配置されることなく、コピー先に直接コピーされます。 

- URL が、BLOB ではなくコンテナーのみを参照している場合は、コンテナーの名前にワイルドカード文字を使用することもできます。 この方法を使用して、コンテナーのサブセットからファイルを取得できます。 

格納しているディレクトリ自体をコピーせずに、ディレクトリの内容をダウンロードします。
 
```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/folder]/*?[SAS]" "/path/to/dir"
```

ストレージ アカウント全体をダウンロードします。

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/" "/path/to/dir" --recursive
```

コンテナー名にワイルドカード記号 (*) を使用して、ストレージ アカウント内のコンテナーのサブセットをダウンロードします。

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container*name]" "/path/to/dir" --recursive
```

SAS トークンを使用して、1 つの BLOB を別の BLOB にコピーします。

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

SAS トークンと認証トークンを使用して、1 つの BLOB を別の BLOB にコピーします。 ソース アカウント URL の末尾に SAS トークンを使用する必要がありますが、`azcopy login` コマンドを使用して AzCopy にログインする場合、コピー先アカウントでは不要です。 

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"
```

SAS トークンを使用して、1 つの BLOB 仮想ディレクトリを別の BLOB 仮想ディレクトリにコピーします。

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

SAS トークンを使用して、すべての BLOB コンテナー、ディレクトリ、BLOB をストレージ アカウントから別のストレージ アカウントにコピーします。

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net?[SAS]" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

アクセス キーと SAS トークンを使用して、アマゾン ウェブ サービス (AWS) S3 から Blob Storage に 1 つのオブジェクトをコピーします。 最初に、AWS S3 ソースの環境変数 `AWS_ACCESS_KEY_ID` と `AWS_SECRET_ACCESS_KEY` を設定します。
  
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

アクセス キーと SAS トークンを使用して、AWS S3 から Blob Storage にディレクトリ全体をコピーします。 最初に、AWS S3 ソースの環境変数 `AWS_ACCESS_KEY_ID` と `AWS_SECRET_ACCESS_KEY` を設定します。
 
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```
    
  [folder] プレースホルダーをさらによく理解するには、 https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html を参照してください。

アクセス キーと SAS トークンを使用して、アマゾン ウェブ サービス (AWS) から Blob Storage にすべてのバケットをコピーします。 最初に、AWS S3 ソースの環境変数 `AWS_ACCESS_KEY_ID` と `AWS_SECRET_ACCESS_KEY` を設定します。
 
```azcopy
azcopy cp "https://s3.amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

アクセス キーと SAS トークンを使用して、アマゾン ウェブ サービス (AWS) のリージョンから Blob Storage にすべてのバケットをコピーします。 最初に、AWS S3 ソースの環境変数 `AWS_ACCESS_KEY_ID` と `AWS_SECRET_ACCESS_KEY` を設定します。
 
```azcopy
- azcopy cp "https://s3-[region].amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

バケット名にワイルドカード記号 (*) を使用して、バケットのサブセットをコピーします。 前の例と同様に、アクセス キーと SAS トークンが必要になります。 AWS S3 ソースの環境変数 `AWS_ACCESS_KEY_ID` と `AWS_SECRET_ACCESS_KEY` は必ず設定します。

```azcopy
- azcopy cp "https://s3.amazonaws.com/[bucket*name]/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

ファイルとディレクトリを Azure Storage アカウントに転送し、指定されたクエリ文字列でエンコードされたタグを BLOB に設定します。 

- {key = "bla bla", val = "foo"}、{key = "bla bla 2", val = "bar"} の各タグを設定するには、次の構文を使用します。`azcopy cp "https://[account].blob.core.windows.net/[source_container]/[path/to/directory]?[SAS]" "https://[account].blob.core.windows.net/[destination_container]/[path/to/directory]?[SAS]" --blob-tags="bla%20bla=foo&bla%20bla%202=bar"`
        
- キーと値は URL でエンコードされ、キーと値のペアはアンパサンド ('&') で区切られます
    
- BLOB にタグを設定するときに、SAS に追加のアクセス許可 (タグ用に 't') があります。これがないと、サービスから認可エラーが返されます。

サービス アカウント キーと SAS トークンを使用して、Google Cloud Storage から Blob Storage に 1 つのオブジェクトをコピーします。 最初に、Google Cloud Storage ソースの環境変数 GOOGLE_APPLICATION_CREDENTIALS を設定します。
  
```azcopy
azcopy cp "https://storage.cloud.google.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

サービス アカウント キーと SAS トークンを使用して、Google Cloud Storage から Blob Storage にディレクトリ全体をコピーします。 最初に、Google Cloud Storage ソースの環境変数 GOOGLE_APPLICATION_CREDENTIALS を設定します。
 
```azcopy
  - azcopy cp "https://storage.cloud.google.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

サービス アカウント キーと SAS トークンを使用して、Google Cloud Storage から Blob Storage にバケット全体をコピーします。 最初に、Google Cloud Storage ソースの環境変数 GOOGLE_APPLICATION_CREDENTIALS を設定します。

```azcopy 
azcopy cp "https://storage.cloud.google.com/[bucket]" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

サービス アカウント キーと SAS トークンを使用して、Google Cloud Storage から Blob Storage にすべてのバケットをコピーします。 最初に、GCS ソースの環境変数 GOOGLE_APPLICATION_CREDENTIALS と GOOGLE_CLOUD_PROJECT =<プロジェクト ID> を設定します。

```azcopy
  - azcopy cp "https://storage.cloud.google.com/" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

Google Cloud Storage のバケット名にワイルドカード記号 (*) を指定し、サービス アカウント キーと宛先の SAS トークンを使用して、バケットのサブセットをコピーします。 最初に、Google Cloud Storage ソースの環境変数 GOOGLE_APPLICATION_CREDENTIALS と GOOGLE_CLOUD_PROJECT =<プロジェクト ID> を設定します。
 
```azcopy
azcopy cp "https://storage.cloud.google.com/[bucket*name]/" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

## <a name="options"></a>Options

**--backup** アップロード向けに Windows の SeBackupPrivilege を、またはダウンロード向けに SeRestorePrivilege を有効にし、AzCopy ですべてのファイルを表示して読み取り、ファイル システムのアクセス許可に関係なく、あらゆるアクセス許可を復元できるようにします。 AzCopy を実行しているアカウントには、既にこれらのアクセス許可がなくてはなりません (たとえば、管理者権限を持っている、または `Backup Operators` グループのメンバーであるなど)。 このフラグは、アカウントに既に含まれている特権をアクティブ化します。

**--blob-tags** string   BLOB に文字列を設定して、ストレージ アカウントのデータを分類します。

**--blob-type** string  宛先の BLOB の種類を定義します。 これは、BLOB をアップロードする場合と、アカウント間でコピーする場合に使用されます (既定では `Detect`)。 有効な値は、`Detect`、`BlockBlob`、`PageBlob`、および `AppendBlob` です。 アカウント間でコピーする場合、値 `Detect` を使用すると、AzCopy はソース BLOB の種類を使用して、コピー先 BLOB の種類を判断します。 ファイルをアップロードするとき、`Detect` は、ファイル拡張子に基づいて、ファイルが VHD ファイルまたは VHDX ファイルであるかを判断します。 ファイルが VHD ファイルまたは VHDX ファイルの場合、AzCopy はそのファイルをページ BLOB として扱います。 (既定値は "Detect")

**--block-blob-tier** string この BLOB 層を使用してブロック BLOB を Azure Storage にアップロードします。 (既定値は "None")

**--block-size-mb** float Azure Storage にアップロードするとき、および Azure Storage からダウンロードするときに、このブロック サイズ (MiB で指定) を使用します。 既定値は、ファイル サイズに基づいて自動的に計算されます。 小数を使用できます (例:0.25)。

**--cache-control** string cache-control ヘッダーを設定します。 ダウンロード時に返されます。

**--check-length** 転送後にコピー先のファイルの長さを確認します。 コピー元とコピー先の間に不一致がある場合は、転送に失敗のマークが付けられます。 既定値は `true` です。

**--check-md5** string ダウンロード時に MD5 ハッシュをどのくらい厳密に検証する必要があるかを指定します。 ダウンロード時にのみ使用できます。 使用可能なオプション: `NoCheck`、`LogOnly`、`FailIfDifferent`、`FailIfDifferentOrMissing` (既定値は `FailIfDifferent`) (既定値は "FailIfDifferent")

**--content-disposition** string content-disposition ヘッダーを設定します。 ダウンロード時に返されます。

**--content-encoding** string content-encoding ヘッダーを設定します。 ダウンロード時に返されます。

**--content-encoding** string content-language ヘッダーを設定します。 ダウンロード時に返されます。

**--content-type** string ファイルのコンテンツの種類を指定します。 no-guess-mime-type を暗黙的に示します。 ダウンロード時に返されます。

**--decompress** content-encoding が、ファイルが圧縮されていることを示している場合、ダウンロードするときに自動的に圧縮解除します。 サポートされている content-encoding の値は `gzip` と `deflate` です。 ファイル拡張子 `.gz` / `.gzip`、または `.zz` は不要です。ある場合は削除されます。

**--exclude-attributes**  string (Windows のみ) 属性が属性一覧と一致するファイルを除外します。 次に例を示します。A;S;R

**--exclude-blob-type** string 必要に応じて、コンテナーまたはアカウントから BLOB をコピーするときに除外する BLOB の種類 (`BlockBlob` / `PageBlob` / `AppendBlob`) を指定します。 このフラグの使用は、Azure 以外のサービスからサービスにデータをコピーする場合には適用されません。 複数の BLOB は `;` で区切る必要があります。 

**--exclude-path** string コピーするときにこれらのパスを除外します。 このオプションでは、ワイルドカード文字 (*) はサポートされていません。 相対パスのプレフィックスを確認します (例: `myFolder;myFolder/subDirName/file.pdf`)。 アカウント トラバーサルと組み合わせて使用する場合、パスにはコンテナー名は含まれません。

**--exclude-pattern** string コピーするときにこれらのファイルを除外します。 このオプションでは、ワイルドカード文字 (*) がサポートされます。

**--follow-symlinks** ローカル ファイル システムからアップロードするときにシンボリック リンクに従います。

**--force-if-read-only** Windows または Azure Files 上の既存のファイルを上書きするときに、既存のファイルに読み取り専用属性が設定されている場合でも、上書きを強制的に実行します。

**--from-to** string 必要に応じて、コピー元とコピー先の組み合わせを指定します。 例: `LocalBlob`、`BlobLocal`、`LocalBlobFS`

**--help** copy のヘルプを表示します。

**--include-after** string 指定した日付/時刻以降に変更されたファイルのみが含まれます。 値は、ISO8601 形式で指定する必要があります。 タイムゾーンが指定されていない場合、値は AzCopy を実行しているマシンのローカル タイムゾーンにあると見なされます。 たとえば、UTC 時刻の場合は `2020-08-19T15:04:00Z`、ローカル タイムゾーンが深夜 (00:00) の場合は `2020-08-19` になります。 AzCopy 10.5 と同様に、このフラグはフォルダーにではなくファイルにのみ適用されるため、このフラグを `--preserve-smb-info` または `--preserve-smb-permissions` と共に使用した場合、フォルダー プロパティはコピーされません。

 **--include-before** string  指定した日付/時刻以前に変更されたファイルのみが含まれます。 値は、ISO8601 形式で指定する必要があります。 タイムゾーンが指定されていない場合、値は AzCopy を実行しているマシンのローカル タイムゾーンにあると見なされます。 例: UTC 時刻の場合は `2020-08-19T15:04:00Z`、ローカル タイムゾーンが深夜 (00:00) の場合は `2020-08-19` になります。 AzCopy 10.7 以降、このフラグはフォルダーにではなくファイルにのみ適用されるため、このフラグを `--preserve-smb-info` または `--preserve-smb-permissions` と共に使用した場合、フォルダー プロパティはコピーされません。

**--include-attributes** string (Windows のみ) 属性が属性一覧と一致するファイルを含めます。 次に例を示します。A;S;R

**--include-path** string コピーするときにこれらのパスのみを含めます。 このオプションでは、ワイルドカード文字 (*) はサポートされていません。 相対パスのプレフィックスを確認します (例: `myFolder;myFolder/subDirName/file.pdf`)。

**--include-pattern** string コピーするときにこれらのファイルのみを含めます。 このオプションでは、ワイルドカード文字 (*) がサポートされます。 `;` を使用してファイルを区切ります。

**--list-of-versions** string  各バージョン ID が個別の行に一覧表示されているファイルを指定します。 ソースが 1 つの BLOB を指している必要があり、このフラグを使用してファイルで指定されたすべてのバージョン ID がソース BLOB のみに属している必要があるため、これを徹底します。 AzCopy は、指定されたバージョンをコピー先フォルダーにダウンロードします。 詳細については、「[前のバージョンの BLOB をダウンロードする](./storage-use-azcopy-v10.md#transfer-data)」を参照してください。

**--log-level** string ログ ファイルのログの詳細度を定義します。使用できるレベルは次のとおりです。INFO (すべての要求/応答)、WARNING (遅い応答)、ERROR (失敗した要求のみ)、NONE (出力ログなし)。 既定値は `INFO` です。 

**--metadata** string これらのキーと値のペアをメタデータとして Azure Storage にアップロードします。

**--no-guess-mime-type** AzCopy がファイルの拡張子またはコンテンツに基づいてコンテンツの種類を検出しないようにします。

**--overwrite** string このフラグが true に設定されている場合は、コピー先の競合するファイルおよび BLOB を上書きします。 既定値は `true` です。指定できる値は `true`、`false`、`prompt`、および `ifSourceNewer` です。 このフラグが `true` の場合、または肯定的な応答がプロンプトに返された場合、フォルダーをサポートしているコピー先では、競合するフォルダーレベルのプロパティが上書きされます。 (既定値は "true")

**--page-blob-tier** string  この BLOB 層を使用して Azure Storage にページ BLOB をアップロードします。 既定値は `None` です。 (既定値は "None")

**--preserve-last-modified-time** コピー先がファイル システムの場合にのみ使用できます。

**--preserve-owner**  ダウンロードする際、および `--preserve-smb-permissions` を使用している場合にのみ影響があります。 true の場合 (既定)、「Owner and Group」ファイルはダウンロードで保持されます。 false に設定されている場合、`--preserve-smb-permissions` は引き続き ACL を保持しますが、「Owner and Group」は AzCopy を実行しているユーザーに基づいて作成されます (既定値は true)。

**--preserve-smb-info** string 既定で false になっています。 SMB 対応リソース (Windows と Azure Files) 間の SMB プロパティ情報 (最終書き込み時刻、作成時刻、属性ビット) を保持します。 Azure Files によってサポートされる属性ビットのみが転送され、それ以外は無視されます。 ファイルのみのフィルターが指定されている場合を除き、このフラグはファイルとフォルダーの両方に適用されます (例: include-pattern)。 フォルダーの場合は保持されない最終書き込み時刻を除き、フォルダーについて転送される情報はファイルの情報と同じです。

**--preserve-smb-permissions** string 既定では false になっています。 認識されるリソース (Windows と Azure Files) 間で SMB ACL を保持します。 ダウンロードする場合は、`--backup` フラグを使用して、新しい所有者が AzCopy を実行しているユーザーにならないアクセス許可を復元する必要もあります。 ファイルのみのフィルターが指定されている場合を除き、このフラグはファイルとフォルダーの両方に適用されます (例: `include-pattern`)。

**--put-md5** 各ファイルの MD5 ハッシュを作成し、ハッシュを宛先 BLOB またはファイルの Content-MD5 プロパティとして保存します。 (既定では、ハッシュは作成されません)。アップロード時にのみ使用できます。

**--recursive** ローカル ファイル システムからアップロードするときにサブディレクトリを再帰的に検索します。

**--s2s-detect-source-changed** 読み取り中にソース ファイルまたは BLOB が変更されたかどうかを検出します。 対応するチェックがアップロードとダウンロードに対して永続的に有効になっているため、このパラメーターはサービス間のコピーにのみ適用されます。

**--s2s-handle-invalid-metadata** string   無効なメタデータ キーの処理方法を指定します。 使用できるオプションは ExcludeIfInvalid、FailIfInvalid、RenameIfInvalid です。 既定値は `ExcludeIfInvalid` です。 (既定値は "ExcludeIfInvalid")

**--s2s-preserve-access-tier** サービス間のコピー中にアクセス層を保持します。 「[Azure Blob Storage: ホット、クール、アーカイブ アクセス層](../blobs/storage-blob-storage-tiers.md)」を参照して、コピー先ストレージ アカウントでアクセス層の設定がサポートされていることを確認してください。 アクセス層の設定がサポートされていない場合は、s2sPreserveAccessTier=false を使用してアクセス層のコピーをバイパスしてください。 既定値は `true` です。  (既定値は "true")

**--s2s-preserve-properties** サービス間のコピー中にすべてのプロパティを保持します。 AWS S3 と Azure File の非単一ファイル ソースの場合、リスト操作はオブジェクトとファイルの完全なプロパティを返しません。 完全なプロパティを保持するために、AzCopy では、オブジェクトまたはファイルごとに 1 つの追加の要求を送信する必要があります。 (既定値は true)

## <a name="options-inherited-from-parent-commands"></a>親コマンドから継承されるオプション

**--cap-mbps float** 転送速度の上限を設定します (メガビット/秒)。 瞬間的なスループットは、上限と若干異なる場合があります。 このオプションを 0 に設定した場合や省略した場合、スループットは制限されません。

**--output-type** string   コマンドの出力形式。 選択肢には、text、json などがあります。 既定値は `text` です。 (既定値は "text")

**--trusted-microsoft-suffixes** string   Azure Active Directory ログイン トークンを送信できる追加のドメイン サフィックスを指定します。  既定では、 `*.core.windows.net;*.core.chinacloudapi.cn;*.core.cloudapi.de;*.core.usgovcloudapi.net`です。 ここに記載されているすべてが既定値に追加されます。 セキュリティのために、Microsoft Azure のドメインのみをここに入力してください。 複数のエンティティは、セミコロンで区切ります。

## <a name="see-also"></a>関連項目

- [azcopy](storage-ref-azcopy.md)
