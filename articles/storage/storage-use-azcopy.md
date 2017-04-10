---
title: "AzCopy を使用して Storage にデータをコピーまたは移動する | Microsoft Docs"
description: "AzCopy ユーティリティを使用して、BLOB、テーブル、およびファイル間でデータを移動またはコピーします。 ローカル ファイルから Azure ストレージにデータをコピーする、またはストレージ アカウント内またはその間でデータをコピーします。 Azure Storage にデータを簡単に移行します。"
services: storage
documentationcenter: 
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: aa155738-7c69-4a83-94f8-b97af4461274
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: seguler
translationtype: Human Translation
ms.sourcegitcommit: e1c4342897fe6cf57e323a82e04e10473b3ece30
ms.openlocfilehash: 777b84d9aa59f773345c1925c3c6467dc64d2d34
ms.lasthandoff: 03/01/2017


---
# <a name="transfer-data-with-the-azcopy-command-line-utility"></a>AzCopy コマンド ライン ユーティリティを使用してデータを転送する
## <a name="overview"></a>概要
AzCopy は、最適なパフォーマンスの単純なコマンドを使用して、Microsoft Azure BLOB、File、Table の記憶域との間でデータをコピーするために設計された Windows コマンドライン ユーティリティです。 ストレージ アカウント内のあるオブジェクトから別のオブジェクトにデータをコピーしたり、ストレージ アカウント間でコピーしたりすることができます。

> [!NOTE]
> このガイドでは、既に [Azure Storage](https://azure.microsoft.com/services/storage/)を使い慣れている前提で説明します。 また使い慣れていない場合は、「 [Microsoft Azure Storage の概要](storage-introduction.md) 」を参照してください。 最も重要な点は、AzCopy を使用するには、 [ストレージ アカウントを作成する](storage-create-storage-account.md#create-a-storage-account) 必要がある、ということです。
> 
> 

## <a name="download-and-install-azcopy"></a>AzCopy のダウンロードとインストール
### <a name="windows"></a>Windows
[最新バージョンの AzCopy](http://aka.ms/downloadazcopy)をダウンロードします。

### <a name="maclinux"></a>Mac/Linux
AzCopy は、Mac または Linux OS では使用できませんが、 Azure Storage との間でデータをコピーする代替の方法として、Azure CLI があります。 詳細については、「[Azure Storage での Azure CLI の使用](storage-azure-cli.md)」をご覧ください。

## <a name="writing-your-first-azcopy-command"></a>AzCopy コマンドの基本
AzCopy コマンドの基本構文は次のとおりです。

```azcopy
AzCopy /Source:<source> /Dest:<destination> [Options]
```

コマンド ウィンドウを開き、 `AzCopy.exe` 実行可能ファイルが格納されている、コンピューターの AzCopy インストール ディレクトリに移動します。 必要に応じて、AzCopy のインストール先をシステム パスに追加できます。 既定で、AzCopy は `%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy` または `%ProgramFiles%\Microsoft SDKs\Azure\AzCopy` にインストールされます。

以下の例では、Microsoft Azure BLOB、File、Table との間でデータをコピーする多様なシナリオを紹介しています。 各サンプルで使用されているパラメーターの詳細な説明については、「 [AzCopy のパラメーター](#azcopy-parameters) 」を参照してください。

## <a name="blob-download"></a>BLOB: ダウンロード
### <a name="download-single-blob"></a>1 つの BLOB をダウンロードする

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:"abc.txt"
```

フォルダー `C:\myfolder` が存在しない場合、AzCopy によってフォルダーが作成され、この新しいフォルダーに `abc.txt ` がダウンロードされます。

### <a name="download-single-blob-from-secondary-region"></a>セカンダリ リージョンから&1; つの BLOB をダウンロードする

```azcopy
AzCopy /Source:https://myaccount-secondary.blob.core.windows.net/mynewcontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt
```

地理冗長ストレージに対応する読み取りアクセス権を持っている必要があります。

### <a name="download-all-blobs"></a>すべての BLOB をダウンロードする

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /S
```

指定されたコンテナーに以下の BLOB があるとします。  

    abc.txt
    abc1.txt
    abc2.txt
    vd1\a.txt
    vd1\abcd.txt

ダウンロード操作後に、ディレクトリ `C:\myfolder` には以下のファイルがあります。

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\vd1\a.txt
    C:\myfolder\vd1\abcd.txt

オプション `/S`を指定しない場合、BLOB はダウンロードされません。

### <a name="download-blobs-with-specified-prefix"></a>指定したプレフィックスの BLOB をダウンロードする

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:a /S
```

指定されたコンテナーに以下の BLOB があるとします。 プレフィックス `a` で始まるすべての BLOB がダウンロードされます。

    abc.txt
    abc1.txt
    abc2.txt
    xyz.txt
    vd1\a.txt
    vd1\abcd.txt

ダウンロード操作後に、フォルダー `C:\myfolder` には以下のファイルがあります。

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

プレフィックスは仮想ディレクトリに適用され、BLOB 名の最初の部分に付けられます。 前述の例では、仮想ディレクトリは指定されたプレフィックスと一致しないので、ダウンロードされません。 さらに、オプション `\S` が指定されていない場合、AzCopy では BLOB はダウンロードされません。

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>エクスポートしたファイルの最終変更時刻をソース BLOB と同時刻に設定する

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT
```

最終変更時刻に基づいてダウンロード操作の対象から BLOB を除外することもできます。 たとえば、対象ファイルの最終変更時刻以降の BLOB を除外する場合、 `/XN` オプションを追加します。

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN
```

または、対象ファイルの最終変更時刻以前の BLOB を除外する場合、 `/XO` オプションを追加します。

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO
```

## <a name="blob-upload"></a>BLOB: アップロード
### <a name="upload-single-file"></a>1 つのファイルをアップロードする

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:"abc.txt"
```

存在しない宛先コンテナーを指定すると、AzCopy によってコンテナーが作成され、そのコンテナーにファイルがアップロードされます。

### <a name="upload-single-file-to-virtual-directory"></a>1 つのファイルを仮想ディレクトリにアップロードする

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer/vd /DestKey:key /Pattern:abc.txt
```

存在しない仮想ディレクトリを指定すると、アップロードされるファイルの名前に仮想ディレクトリが含められます (*例*: 上記の例の `vd/abc.txt`)。

### <a name="upload-all-files"></a>すべてのファイルをアップロードする

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S
```

オプション `/S` を指定すると、指定したディレクトリの内容が再帰的にアップロードされます。つまり、すべてのサブフォルダーと、それらのサブフォルダーに含まれるファイルも BLOB ストレージにアップロードされます。 たとえば、フォルダー `C:\myfolder` に以下のファイルがあるとします。

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

アップロード操作後に、コンテナーには以下のファイルがあります。

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

オプション `/S`を指定しない場合、AzCopy では再帰的にアップロードされません。 アップロード操作後に、コンテナーには以下のファイルがあります。

    abc.txt
    abc1.txt
    abc2.txt

### <a name="upload-files-matching-specified-pattern"></a>指定したパターンに一致するファイルをアップロードする

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:a* /S
```

フォルダー `C:\myfolder`に以下のファイルがあるとします。

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\xyz.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

アップロード操作後に、コンテナーには以下のファイルがあります。

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

オプション `/S`を指定しない場合、AzCopy では仮想ディレクトリ内にない BLOB のみがアップロードされます。

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>コピー先の BLOB の MIME コンテンツの種類を指定する
既定では、AzCopy はコピー先の BLOB のコンテンツの種類を `application/octet-stream`に設定します。 バージョン 3.1.0 以降は、オプション `/SetContentType:[content-type]`を使用してコンテンツの種類を明示的に指定できます。 この構文は、アップロード操作におけるすべての BLOB のコンテンツの種類を設定します。

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType:video/mp4
```

`/SetContentType` で値を指定しない場合、AzCopy は各 BLOB またはファイルのコンテンツの種類をそのファイルの拡張子に応じて設定します。

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType
```

## <a name="blob-copy"></a>BLOB: コピー
### <a name="copy-single-blob-within-storage-account"></a>ストレージ アカウント内の&1; つの BLOB をコピーする

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt
```

ストレージ アカウント内の&1; つの BLOB をコピーすると、 [サーバー側でコピー](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) 操作が実行されます。

### <a name="copy-single-blob-across-storage-accounts"></a>複数のストレージ アカウントに&1; つの BLOB をコピーする

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
```

複数のストレージ アカウントに&1; つの BLOB をコピーすると、 [サーバー側でコピー](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) 操作が実行されます。

### <a name="copy-single-blob-from-secondary-region-to-primary-region"></a>1 つの BLOB をセカンダリ リージョンからプライマリ リージョンにコピーする

```azcopy
AzCopy /Source:https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 /Dest:https://myaccount2.blob.core.windows.net/mynewcontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
```

地理冗長ストレージに対応する読み取りアクセス権を持っている必要があります。

### <a name="copy-single-blob-and-its-snapshots-across-storage-accounts"></a>複数のストレージ アカウントに&1; つの BLOB とそのスナップショットをコピーする

```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot
```

コピー操作後、ターゲット コンテナーには BLOB とそのスナップショットが含まれます。 前の例の BLOB に&2; つのスナップショットが含まれる場合、コンテナーには以下の BLOB とスナップショットが含まれます。

    abc.txt
    abc (2013-02-25 080757).txt
    abc (2014-02-21 150331).txt

### <a name="synchronously-copy-blobs-across-storage-accounts"></a>複数のストレージ アカウントに複数の BLOB を同期的にコピーする
既定では、AzCopy は&2; つのストレージ エンドポイント間で非同期でデータをコピーします。 そのため、コピー操作は、BLOB のコピー速度に関する SLA のない予備の帯域幅容量を使用してバック グラウンドで実行され、AzCopy はコピーが完了または失敗するまで定期的にコピー状態を確認します。

`/SyncCopy` オプションは、コピー操作が一定の速度になるようにします。 AzCopy は、指定されたソースからローカル メモリにコピーして BLOB をダウンロードし、これを BLOB ストレージのコピー先にアップロードすることで同期コピーを実行します。

```azcopy
AzCopy /Source:https://myaccount1.blob.core.windows.net/myContainer/ /Dest:https://myaccount2.blob.core.windows.net/myContainer/ /SourceKey:key1 /DestKey:key2 /Pattern:ab /SyncCopy
```

`/SyncCopy` は、非同期コピーと比較すると追加の送信コストが発生する可能性があります。送信コストを回避するために、ソース ストレージ アカウントと同じリージョンにある Azure VM でこのオプションを使用することをお勧めします。

## <a name="file-download"></a>ファイル: ダウンロード
### <a name="download-single-file"></a>1 つのファイルをダウンロードする

```azcopy
AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt
```

指定されたソースが Azure ファイル共有の場合は、厳密なファイル名 (*例*: `abc.txt`) を指定して単一ファイルをダウンロードするか、オプション `/S` を指定して共有内の全ファイルを再帰的にダウンロードする必要があります。 ファイル パターンとオプション `/S` の両方を同時に指定しようとすると、エラーになります。

### <a name="download-all-files"></a>すべてのファイルをダウンロードする

```azcopy
AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S
```

空のフォルダーはダウンロードされません。

## <a name="file-upload"></a>ファイル: アップロード
### <a name="upload-single-file"></a>1 つのファイルをアップロードする

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:abc.txt
```

### <a name="upload-all-files"></a>すべてのファイルをアップロードする

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S
```

空のフォルダーはアップロードされません。

### <a name="upload-files-matching-specified-pattern"></a>指定したパターンに一致するファイルをアップロードする

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S
```

## <a name="file-copy"></a>ファイル: コピー
### <a name="copy-across-file-shares"></a>ファイル共有間でコピーする

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S
```
ファイル共有をまたがってファイルをコピーすると、[サーバー側でコピー](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx)操作が実行されます。

### <a name="copy-from-file-share-to-blob"></a>ファイル共有から BLOB にコピーする

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare/ /Dest:https://myaccount2.blob.core.windows.net/mycontainer/ /SourceKey:key1 /DestKey:key2 /S
```
ファイル共有から BLOB にファイルをコピーすると、[サーバー側でコピー](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx)操作が実行されます。


### <a name="copy-from-blob-to-file-share"></a>BLOB からファイル共有にコピーする

```azcopy
AzCopy /Source:https://myaccount1.blob.core.windows.net/mycontainer/ /Dest:https://myaccount2.file.core.windows.net/myfileshare/ /SourceKey:key1 /DestKey:key2 /S
```
BLOB からファイル共有にファイルをコピーすると、[サーバー側でコピー](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx)操作が実行されます。

### <a name="synchronously-copy-files"></a>ファイルを同期的にコピーする
オプション `/SyncCopy` を指定すると、File Storage から File Storage に、File Storage から Blob Storage に、Blob Storage から File Storage にデータを同期的にコピーすることができます。AzCopy で同期的にコピーするには、ソース データをローカル メモリにダウンロードし、それを宛先に再度アップロードします。 標準の送信コストが適用されます。

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy
```

File Storage から Blob Storage にコピーするときの既定の BLOB タイプはブロック BLOB ですが、ユーザーはオプション `/BlobType:page` を指定して宛先 BLOB タイプを変更できます。

`/SyncCopy` は、非同期コピーと比較すると追加の送信コストが発生する可能性があります。送信コストを回避するために、ソース ストレージ アカウントと同じリージョンにある Azure VM でこのオプションを使用することをお勧めします。

## <a name="table-export"></a>テーブル: エクスポート
### <a name="export-table"></a>テーブルをエクスポートする

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key
```

AzCopy で、指定した宛先フォルダーにマニフェスト ファイルを出力します。 このマニフェスト ファイルは、インポート プロセスで、必要なデータ ファイルを特定し、データの検証を実行するために使用されます。 マニフェスト ファイルでは、既定で次の名前付け規則が使用されます。

    <account name>_<table name>_<timestamp>.manifest

オプション `/Manifest:<manifest file name>` を指定してマニフェスト ファイル名を設定することもできます。

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /Manifest:abc.manifest
```

### <a name="split-export-into-multiple-files"></a>複数のファイルにエクスポートを分割する

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/mytable/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100
```

AzCopy では、複数のファイルを区別できるように、分割したデータ ファイル名に *ボリューム インデックス* を含めます。 ボリューム インデックスは、*パーティション キー範囲のインデックス*と*分割されたファイルのインデックス*の&2; つの部分で構成されます。 どちらのインデックスも&0; から始まります。

パーティション キー範囲のインデックスは、ユーザーがオプション `/PKRS`を指定しなかった場合、0 に設定されます。

たとえば、ユーザーがオプション `/SplitSize`を指定した後に、AzCopy によって&2; つのデータ ファイルが生成されたとします。 生成されたデータ ファイルの名前は次のようになります。

    myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
    myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

オプション `/SplitSize` の最小の値は 32 MB です。 指定された宛先が BLOB ストレージの場合、データ ファイルのサイズが BLOB サイズの上限 (200 GB) に達すると、ユーザーがオプション `/SplitSize` を指定しているかどうかにかかわらず、AzCopy によってデータ ファイルが分割されます。

### <a name="export-table-to-json-or-csv-data-file-format"></a>テーブルを JSON または CSV データ ファイル形式にエクスポートする
AzCopy の既定では、テーブルは JSON データ ファイルにエクスポートされます。 オプション `/PayloadFormat:JSON|CSV` を指定すると、テーブルを JSON または CSV 形式でエクスポートできます。

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PayloadFormat:CSV
```

CSV ペイロード形式を指定して AzCopy を実行すると、各データ ファイルのファイル拡張子 `.schema.csv` が付いたスキーマ ファイルも生成されます。

### <a name="export-table-entities-concurrently"></a>複数のテーブル エンティティを同時にエクスポートする

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PKRS:"aa#bb"
```

ユーザーによってオプション `/PKRS`が指定されると、AzCopy はエンティティをエクスポートするための同時操作を開始します。 1 つの操作で&1; つのパーティション キー範囲がエクスポートされます。

同時操作の数もオプション `/NC`によって制御されます。 `/NC` が指定されていない場合でも、AzCopy はコア プロセッサ数を `/NC` の既定値として使用してテーブル エンティティをコピーします。 ユーザーがオプション `/PKRS`を指定した場合、AzCopy は、パーティション キー範囲と明示的または暗黙的に指定された同時操作数の&2; つの値のうち小さい方を使用して、開始する同時操作の数を決定します。 詳細については、コマンド ラインに「 `AzCopy /?:NC` 」と入力してください。

### <a name="export-table-to-blob"></a>テーブルを BLOB にエクスポートする

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2
```

AzCopy は、次の名前付け規則を使用して JSON データ ファイルを BLOB コンテナーに生成します。

    <account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

生成される JSON データ ファイルは、最小のメタデータ用のペイロード形式に準じます。 このペイロード形式の詳細については、「 [テーブル サービス操作のペイロード形式](http://msdn.microsoft.com/library/azure/dn535600.aspx)」を参照してください。

BLOB にテーブルをエクスポートすると、AzCopy は Table エンティティをローカルの一時データ ファイルにダウンロードしてから、そのエンティティを BLOB にアップロードします。 この一時データ ファイルは、既定のパスが “<code>%LocalAppData%\Microsoft\Azure\AzCopy</code>” のジャーナル ファイル フォルダーに格納されます。オプション /Z:[ジャーナル ファイルのフォルダー] を指定してジャーナル ファイル フォルダーの場所を変更することもできます。この場合、一時データ ファイルの場所も変わります。 一時データ ファイルのサイズは、テーブル エンティティのサイズと、オプション /SplitSize で指定したサイズによって決まります。ローカル ディスクの一時データ ファイルは BLOB にアップロードされた後すぐに削除されますが、そのような一時データ ファイルを格納するのに十分なローカル ディスク領域が存在することを事前に確認しておく必要があります。

## <a name="table-import"></a>テーブル: インポート
### <a name="import-table"></a>テーブルをインストールする

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

オプション `/EntityOperation` は、エンティティをテーブルに挿入する方法を示します。 次のいずれかの値になります。

* `InsertOrSkip`: テーブルにエンティティが存在する場合はスキップし、存在しない場合は新しいエンティティを挿入します。
* `InsertOrMerge`: テーブルにエンティティが存在する場合はマージし、存在しない場合は新しいエンティティを挿入します。
* `InsertOrReplace`: テーブルにエンティティが存在する場合は置換し、存在しない場合は新しいエンティティを挿入します。

インポート シナリオでは、オプション `/PKRS` を指定できません。 同時操作を開始するためにオプション `/PKRS` を指定する必要があるエクスポート シナリオとは違って、テーブルをインポートすると、AzCopy の既定で同時操作が開始されます。 開始される同時操作の既定数は、コア プロセッサの数と同じです。ただし、オプション `/NC` を使用して別の同時操作数を指定できます。 詳細については、コマンド ラインに「 `AzCopy /?:NC` 」と入力してください。

AzCopy は、CSV ではなく、JSON のインポートのみをサポートしています。 AzCopy では、ユーザーが作成した JSON およびマニフェスト ファイルからのテーブル インポートはサポートされていません。 これらのファイルは両方とも、AzCopy テーブル エクスポートから取得する必要があります。 エラーが発生しないように、エクスポートされた JSON またはマニフェスト ファイルは変更しないでください。

### <a name="import-entities-to-table-using-blobs"></a>BLOB を使用してエンティティをテーブルにインポートする
BLOB コンテナーに、Azure Table を表す JSON ファイルと、それに付随するマニフェスト ファイルが含まれるとします。

    myaccount_mytable_20140103T112020.manifest
    myaccount_mytable_20140103T112020_0_0_0AF395F1DC42E952.json

次のコマンドを実行して、この BLOB コンテナー内のマニフェスト ファイルを使用し、エンティティをテーブルにインポートすることができます。

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:https://myaccount.table.core.windows.net/mytable /SourceKey:key1 /DestKey:key2 /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:"InsertOrReplace"
```

## <a name="other-azcopy-features"></a>その他の AzCopy 機能
### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>宛先に存在しないデータのみをコピーする
`/XO` パラメーターを指定すると古いソース リソースを、`/XN` パラメーターを指定すると新しいソース リソースをコピー対象から除外できます。 宛先に存在しないソース リソースのみをコピーする場合は、AzCopy コマンドに両方のパラメーターを指定します。

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /XO /XN

    /Source:C:\myfolder /Dest:http://myaccount.file.core.windows.net/myfileshare /DestKey:<destkey> /S /XO /XN

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:http://myaccount.blob.core.windows.net/mycontainer1 /SourceKey:<sourcekey> /DestKey:<destkey> /S /XO /XN

ソースまたは宛先がテーブルの場合、この操作はサポートされていません。

### <a name="use-a-response-file-to-specify-command-line-parameters"></a>コマンド ライン パラメーターを指定するための応答ファイルの使用

```azcopy
AzCopy /@:"C:\responsefiles\copyoperation.txt"
```

応答ファイルには、任意の AzCopy コマンド ライン パラメーターを含めることができます。 AzCopy は、ファイルの内容に直接置換することで、ファイルのパラメーターをコマンド ラインで指定されているかのように処理します。

`copyoperation.txt`という名前のファイルに、以下の行が含まれているとします。 各 AzCopy パラメーターは&1; 行に指定できます。

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

また、別の行に指定することもできます。

    /Source:http://myaccount.blob.core.windows.net/mycontainer
    /Dest:C:\myfolder
    /SourceKey:<sourcekey>
    /S
    /Y

`/sourcekey` パラメーターの場合、次の例のようにパラメーターが&2; 行に分かれていると、AzCopy は失敗します。

    http://myaccount.blob.core.windows.net/mycontainer
     C:\myfolder
    /sourcekey:
    <sourcekey>
    /S
    /Y

### <a name="use-multiple-response-files-to-specify-command-line-parameters"></a>複数の応答ファイルを使用してコマンドライン パラメーターを指定する
`source.txt` という名前の応答ファイルが、ソース コンテナーを次のように指定するとします。

    /Source:http://myaccount.blob.core.windows.net/mycontainer

さらに、 `dest.txt` という名前の応答ファイルは、ファイル システムの宛先フォルダーを次のように指定します。

    /Dest:C:\myfolder

また、 `options.txt` という名前の応答ファイルは、AzCopy のオプションを次のように指定します。

    /S /Y

すべてディレクトリ `C:\responsefiles`に格納されているこれらの応答ファイルを使用して AzCopy を呼び出すには、次のコマンドを使用します。

```azcopy
AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /SourceKey:<sourcekey> /@:"C:\responsefiles\options.txt"   
```

AzCopy は次のように、個々のパラメーターがすべてコマンド ラインで指定されたかのようにこのコマンドを処理します。

```azcopy
AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y
```

### <a name="specify-a-shared-access-signature-sas"></a>共有アクセス署名 (SAS) の指定

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceSAS:SAS1 /DestSAS:SAS2 /Pattern:abc.txt
```

コンテナー URI に対して SAS を指定することもできます。

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken /Dest:C:\myfolder /S
```

### <a name="journal-file-folder"></a>ジャーナル ファイル フォルダー
AzCopy にコマンドが発行されるたびに、AzCopy は既定のフォルダーまたはこのオプションで指定されたフォルダーにジャーナル ファイルが存在するかどうかを確認します。 どちらの場所にもジャーナル ファイルがない場合は、AzCopy は新しい操作であると認識し、新しいジャーナル ファイルを作成します。

既存のジャーナル ファイルがある場合は、AzCopy は入力されたコマンド ラインがジャーナル ファイルのコマンド ラインと一致するかどうかを確認します。 2 つのコマンド ラインが一致する場合、AzCopy は未完了の操作を再開します。 一致しない場合は、ジャーナル ファイルを上書きして新しい操作を開始するか、現在の操作を取り消すかをたずねるメッセージが表示されます。

ジャーナル ファイルに既定の場所を使用する場合:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z
```

前に示したように、オプション `/Z` を省略するか、フォルダー パスなしでオプション `/Z` を指定すると、AzCopy は既定の場所である `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy` にジャーナル ファイルを作成します。 既存のジャーナル ファイルがある場合、このジャーナル ファイルに基づいて AzCopy が操作を再開します。

ジャーナル ファイルにカスタムの場所を指定する場合:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\
```

この例では、既存のジャーナル ファイルがない場合に、これを作成します。 既存のファイルがある場合、AzCopy はこのジャーナル ファイルに基づいて操作を再開します。

AzCopy の操作を再開する場合:

```azcopy
AzCopy /Z:C:\journalfolder\
```

この例では、完了できなかった可能性がある直前の操作を再開します。

### <a name="generate-a-log-file"></a>ログ ファイルの生成

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V
```

冗長ログへのファイル パスを指定せずにオプション `/V` を指定すると、AzCopy は既定の場所である `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy` にログ ファイルを作成します。

それ以外の場合、カスタムの場所にログ ファイルを作成できます。

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log
```

`/V:test/azcopy1.log` のように、オプション `/V` に続けて相対パスを指定すると、現在の作業ディレクトリの `test` という名前のサブフォルダー内に詳細ログが作成されます。

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>開始する同時操作の数の指定
オプション `/NC` を使用して同時コピー操作の数を指定します。 既定では、AzCopy はデータ転送のスループットを向上するために一定数の同時操作を開始します。 Table 操作については、同時操作数と所有するプロセッサ数が同じになります。 BLOB および File 操作については、同時操作数は、所有するプロセッサ数に 8 を乗算した数と同じになります。 低帯域幅のネットワークで AzCopy を実行している場合は、/NC に少ない数を指定することで、リソースの競合で生じる失敗を回避できます。

### <a name="run-azcopy-against-azure-storage-emulator"></a>Azure ストレージ エミュレーターに対して AzCopy を実行する
[Azure ストレージ エミュレーター](storage-use-emulator.md)に対して AzCopy を実行できます。BLOB の例を次に示します。

```azcopy
AzCopy /Source:https://127.0.0.1:10000/myaccount/mycontainer/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S
```

テーブルの例を選択します。

```azcopy
AzCopy /Source:https://127.0.0.1:10002/myaccount/mytable/ /Dest:C:\myfolder /SourceKey:key /SourceType:Table
```

## <a name="azcopy-parameters"></a>AzCopy のパラメーター
ここでは、AzCopy のパラメーターについて説明します。 AzCopy の使用中にコマンド ラインから以下のコマンドのいずれかを入力して、ヘルプを表示することもできます。

* AzCopy に関する詳細なコマンド ライン ヘルプを表示する場合: `AzCopy /?`
* AzCopy パラメーターに関する詳細なヘルプを表示する場合: `AzCopy /?:SourceKey`
* コマンド ラインの例を表示する場合: `AzCopy /?:Samples`

### <a name="sourcesource"></a>/Source:"source"
コピー元となるソース データを指定します。 ソースには、ファイル システムのディレクトリ、BLOB コンテナー、BLOB 仮想ディレクトリ、ストレージ ファイル共有、ストレージ ファイルのディレクトリ、または Azure テーブルを指定できます。

**適用対象:** BLOB、ファイル、テーブル

### <a name="destdestination"></a>/Dest:"destination"
コピー先を指定します。 コピー先には、ファイル システムのディレクトリ、BLOB コンテナー、BLOB 仮想ディレクトリ、ストレージ ファイル共有、ストレージ ファイル ディレクトリ、または Azure テーブルを指定できます。

**適用対象:** BLOB、ファイル、テーブル

### <a name="patternfile-pattern"></a>/Pattern:"file-pattern"
コピーするファイルを示すファイル パターンを指定します。 /Pattern パラメーターの動作は、ソース データの場所と再帰モード オプションの有無で決まります。 再帰モードは、オプション /S で指定されます。

指定されたソースがファイル システムのディレクトリの場合、標準のワイルドカードが有効となり、指定されたファイル パターンがディレクトリ内のファイルと照合されます。 オプション /S が指定されると、AzCopy は、該当するディレクトリの下位にあるすべてのサブフォルダー内の全ファイルについても、指定のパターンと照合します。

指定されたソースが BLOB コンテナーまたは仮想ディレクトリの場合、ワイルドカードは適用されません。 オプション /S が指定されると、AzCopy は指定のファイル パターンを BLOB のプレフィックスとして解釈します。 オプション /S を指定しない場合は、AzCopy はファイル パターンを BLOB 名そのものと照合します。

指定されたソースが Azure ファイル共有の場合は、厳密なファイル名 (abc.txt など) を指定して単一ファイルをコピーするか、オプション /S を指定して共有内の全ファイルを再帰的にコピーする必要があります。 ファイル パターンとオプション /S の両方を同時に指定しようとすると、エラーになります。

AzCopy では、/Source が BLOB コンテナーまたは BLOB 仮想ディレクトリである場合は大文字と小文字を区別する照合が行われ、その他の場合はすべて大文字と小文字を区別しない照合が行われます。

ファイル パターンが指定されていない場合に使用される既定のファイル パターンは、*.* です。 また、Azure Storage 上の場所の場合は空のプレフィックスです。 複数のファイル パターンを指定することはサポートされていません。

**適用対象:** BLOB、ファイル

### <a name="destkeystorage-key"></a>/DestKey:"storage-key"
宛先リソースのストレージのアカウント キーを指定します。

**適用対象:** BLOB、ファイル、テーブル

### <a name="destsassas-token"></a>/DestSAS:"sas-token"
宛先に対して、READ および WRITE アクセス許可を使用する Shared Access Signature (SAS) を指定します (該当する場合)。 特殊なコマンド ライン文字が含まれる可能性があるため、SAS は二重引用符で囲みます。

宛先リソースが BLOB コンテナー、ファイル共有、またはテーブルの場合は、このオプションと直後の SAS トークンを指定するか、または、このオプションを指定せずに宛先 BLOB コンテナー、ファイル共有、またはテーブルの URI の一部として SAS を指定することができます。

ソースと宛先がどちらも BLOB の場合は、宛先 BLOB がソース BLOB と同じストレージ アカウント内に存在している必要があります。

**適用対象:** BLOB、ファイル、テーブル

### <a name="sourcekeystorage-key"></a>/SourceKey:"storage-key"
ソース リソースのストレージのアカウント キーを指定します。

**適用対象:** BLOB、ファイル、テーブル

### <a name="sourcesassas-token"></a>/SourceSAS:"sas-token"
ソースに対して、READ および LIST アクセス許可を使用する Shared Access Signature を指定します (該当する場合)。 特殊なコマンド ライン文字が含まれる可能性があるため、SAS は二重引用符で囲みます。

ソース リソースが BLOB コンテナーで、キーまたは SAS のどちらも指定しない場合、BLOB コンテナーは匿名のアクセスで読み取られます。

ソースがファイル共有またはテーブルの場合は、キーまたは SAS を指定する必要があります。

**適用対象:** BLOB、ファイル、テーブル

### <a name="s"></a>/S
コピー操作の再帰モードを指定します。 再帰モードでは、AzCopy は指定のファイル パターンに一致するすべての BLOB またはファイル (サブフォルダー内を含む) をコピーします。

**適用対象:** BLOB、ファイル

### <a name="blobtypeblock--page--append"></a>/BlobType:"block" | "page" | "append"
宛先 BLOB がブロック BLOB、ページ BLOB、追加 BLOB のどれであるかを指定します。 このオプションは、BLOB をアップロードする場合にのみ適用されます。 それ以外の場合は、エラーが発生します。 宛先が BLOB で、このオプションが指定されていない場合、既定では AzCopy によってブロック BLOB が作成されます。

**適用対象:** BLOB

### <a name="checkmd5"></a>/CheckMD5
ダウンロードするデータの MD5 ハッシュを計算し、BLOB に格納されている MD5 ハッシュまたはファイルの Content-MD5 プロパティが計算したハッシュに一致するかどうかを検証します。 MD5 チェックは既定では無効になっているため、MD5 チェックを実行するにはデータのダウンロード時にこのオプションを指定する必要があります。

Azure ストレージでは、BLOB またはファイルに対して格納される MD5 ハッシュが最新であることは保証されません。 BLOB またはファイルが変更された場合、MD5 の更新はクライアントの責任で行う必要があります。

AzCopy は、Azure BLOB またはファイルをサービスにアップロードした後、常にその Content-MD5 プロパティを設定します。  

**適用対象:** BLOB、ファイル

### <a name="snapshot"></a>/Snapshot
スナップショットを転送するかどうかを示します。 このオプションはソースが BLOB の場合のみに有効です。

転送する BLOB のスナップショットは、blob-name (snapshot-time).extension の形式で名前が変更されます

スナップショットは既定ではコピーされません。

**適用対象:** BLOB

### <a name="vverbose-log-file"></a>/V:[verbose-log-file]
詳細な状態メッセージをログ ファイルに出力します。

既定では、詳細なログ ファイル名は AzCopyVerbose.log で、 `%LocalAppData%\Microsoft\Azure\AzCopy`に出力されます。 このオプションで既存のファイルの場所を指定すると、そのファイルに詳細なログが追加されます。  

**適用対象:** BLOB、ファイル、テーブル

### <a name="zjournal-file-folder"></a>/Z:[journal-file-folder]
操作を再開するジャーナル ファイル フォルダーを指定します。

操作に割り込みが発生した場合、AzCopy は常に再開をサポートします。

このオプションを指定しないか、フォルダー パスなしで指定した場合、AzCopy は既定の場所である %LocalAppData%\Microsoft\Azure\AzCopy にジャーナル ファイルを作成します。

AzCopy にコマンドが発行されるたびに、AzCopy は既定のフォルダーまたはこのオプションで指定されたフォルダーにジャーナル ファイルが存在するかどうかを確認します。 どちらの場所にもジャーナル ファイルがない場合は、AzCopy は新しい操作であると認識し、新しいジャーナル ファイルを作成します。

既存のジャーナル ファイルがある場合は、AzCopy は入力されたコマンド ラインがジャーナル ファイルのコマンド ラインと一致するかどうかを確認します。 2 つのコマンド ラインが一致する場合、AzCopy は未完了の操作を再開します。 一致しない場合は、ジャーナル ファイルを上書きして新しい操作を開始するか、現在の操作を取り消すかをたずねるメッセージが表示されます。

操作が正常に完了すると、ジャーナル ファイルは削除されます。

以前のバージョンの AzCopy で作成されたジャーナル ファイルによる操作の再開はサポートされていません。

**適用対象:** BLOB、ファイル、テーブル

### <a name="parameter-file"></a>/@:"parameter-file"
パラメーターを含むファイルを指定します。 AzCopy は、このファイルのパラメーターを、コマンド ラインで指定された場合と同様に処理します。

応答ファイルでは、複数のパラメーターを単一行に指定することも、各パラメーターをそれぞれの行に指定することもできます。 個々のパラメーターを複数行にまたがって指定することはできません。

応答ファイルには、シンボル # で始まるコメント行を含めることができます。

複数の応答ファイルを指定できます。 ただし、入れ子になった応答ファイルはサポートされません。

**適用対象:** BLOB、ファイル、テーブル

### <a name="y"></a>/Y
AzCopy のすべての確認プロンプトを表示しません。

**適用対象:** BLOB、ファイル、テーブル

### <a name="l"></a>/L
一覧の操作のみ指定します。データはコピーされません。

このオプションを使用すると、AzCopy はこのオプション /L を指定せずにコマンド ラインを実行するためのシミュレーションであると解釈し、コピーされるオブジェクト数をカウントします。同時に /V オプションを指定して詳細ログにコピーされるオブジェクトをチェックできます。

このオプションの動作は、ソース データの場所と、再帰モード オプション /S およびファイル パターン オプション /Pattern の有無によっても左右されます。

AzCopy では、このオプションを使用するときに、このソースの場所の LIST および READ アクセス許可が必要です。

**適用対象:** BLOB、ファイル

### <a name="mt"></a>/MT
ダウンロードしたファイルの最後の変更時刻をソースの BLOB またはファイルと同じ値に設定します。

**適用対象:** BLOB、ファイル

### <a name="xn"></a>/XN
新しいソース リソースを除外します。 ソースが最後に変更された時刻が宛先と同じか、それよりも後である場合、リソースはコピーされません。

**適用対象:** BLOB、ファイル

### <a name="xo"></a>/XO
古いソース リソースを除外します。 ソースが最後に変更された時刻が宛先と同じか、それよりも前である場合、リソースはコピーされません。

**適用対象:** BLOB、ファイル

### <a name="a"></a>/A
Archive 属性が設定済みのファイルのみアップロードします。

**適用対象:** BLOB、ファイル

### <a name="iarashcnetoi"></a>/IA:[RASHCNETOI]
指定の属性のいずれかが設定されているファイルのみアップロードします。

以下の属性が利用可能です。

* R = 読み取り専用ファイル
* A = アーカイブの準備が完了したファイル
* S = システム ファイル
* H = 隠しファイル
* C = 圧縮ファイル
* N = 通常ファイル
* E = 暗号化ファイル
* T = 一時ファイル
* O = オフラインのファイル
* I = インデックス付きでないファイル

**適用対象:** BLOB、ファイル

### <a name="xarashcnetoi"></a>/XA:[RASHCNETOI]
指定の属性のいずれかが設定されているファイルを除外します。

以下の属性が利用可能です。

* R = 読み取り専用ファイル
* A = アーカイブの準備が完了したファイル
* S = システム ファイル
* H = 隠しファイル
* C = 圧縮ファイル
* N = 通常ファイル
* E = 暗号化ファイル
* T = 一時ファイル
* O = オフラインのファイル
* I = インデックス付きでないファイル

**適用対象:** BLOB、ファイル

### <a name="delimiterdelimiter"></a>/Delimiter:"delimiter"
BLOB 名で仮想ディレクトリを区切るために使用する区切り文字を示します。

既定では、AzCopy は区切り文字として / を使用します。 ただし、AzCopy は、一般的な文字である @、#、または % の区切り文字としての使用もサポートします。 この特殊文字のいずれかをコマンド ラインに含める必要がある場合は、ファイル名を二重引用符で囲みます。

このオプションは BLOB のダウンロードのみに適用されます。

**適用対象:** BLOB

### <a name="ncnumber-of-concurrent-operations"></a>/NC:"number-of-concurrent-operations"
同時操作の数を指定します。

AzCopy は既定で、データ転送のスループットを向上するために一定数の同時操作を開始します。 低帯域幅の環境で大量に同時操作を実行すると、接続するネットワークに過剰な負荷がかかり、操作を完了できなくなる場合があります。 実際に利用可能なネットワーク帯域幅に適した同時操作数になるように調整してください。

同時操作の上限は 512 です。

**適用対象:** BLOB、ファイル、テーブル

### <a name="sourcetypeblob--table"></a>/SourceType:"Blob" | "Table"
`source` リソースが、ストレージ エミュレーターで実行されている、ローカル開発環境で利用可能な BLOB であると指定します。

**適用対象:** BLOB、テーブル

### <a name="desttypeblob--table"></a>/DestType:"Blob" | "Table"
`destination` リソースが、ストレージ エミュレーターで実行されている、ローカル開発環境で利用可能な BLOB であると指定します。

**適用対象:** BLOB、テーブル

### <a name="pkrskey1key2key3"></a>/PKRS:"key1#key2#key3#..."
テーブル データを並行してエクスポートできるようにパーティション キーの範囲を分割します。これによって、エクスポート操作の速度が速くなります。

このオプションを指定しない場合、AzCopy はシングル スレッドを使用してテーブル エンティティをエクスポートします。 たとえば、ユーザーが /PKRS:"aa#bb" と指定した場合、AzCopy は&3; つの同時操作を開始します。

以下のとおり、1 つの操作で、3 つのパーティション キー範囲の&1; つがエクスポートされます。

  [first-partition-key, aa)

  [aa, bb)

  [bb, last-partition-key]

**適用対象:** テーブル

### <a name="splitsizefile-size"></a>/SplitSize:"file-size"
エクスポートされるファイルの分割サイズを MB 単位で指定します。許容される最小値は 32 です。

このオプションを指定しない場合、AzCopy はテーブル データを&1; つのファイルにエクスポートします。

テーブル データを BLOB にエクスポートする場合、エクスポートされるファイルのサイズが BLOB サイズの上限である 200 GB に達していると、このオプションが指定されていなくても、エクスポートされるファイルが分割されます。

**適用対象:** テーブル

### <a name="entityoperationinsertorskip--insertormerge--insertorreplace"></a>/EntityOperation:"InsertOrSkip" | "InsertOrMerge" | "InsertOrReplace"
テーブル データのインポート動作を指定します。

* InsertOrSkip - テーブルにエンティティが存在する場合はスキップし、存在しない場合は新しいエンティティを挿入します。
* InsertOrMerge - テーブルにエンティティが存在する場合はマージし、存在しない場合は新しいエンティティを挿入します。
* InsertOrReplace - テーブルにエンティティが存在する場合は置換し、存在しない場合は新しいエンティティを挿入します。

**適用対象:** テーブル

### <a name="manifestmanifest-file"></a>/Manifest:"manifest-file"
テーブルのエクスポートおよびインポート操作のマニフェスト ファイルを指定します。

このオプションはエクスポート操作中のオプションであり、オプションが指定されていない場合、AzCopy は事前定義された名前を使用してマニフェスト ファイルを生成します。

このオプションは、データ ファイルを検索するためのインポート操作中に必要です。

**適用対象:** テーブル

### <a name="synccopy"></a>/SyncCopy
2 つの Azure ストレージ エンドポイント間で BLOB またはファイルを同期的にコピーするかどうかを示します。

既定では、AzCopy はサーバー側の非同期コピーを使用します。 同期コピーを実行する場合は、このオプションを指定します。これにより、BLOB またはファイルはローカル メモリにダウンロードされ、次に Azure Storage にアップロードされます。

このオプションは、ファイルを BLOB ストレージ内またはファイル ストレージ内でコピーする場合や、BLOB ストレージからファイル ストレージ、またはファイル ストレージから BLOB ストレージにコピーする場合に使用できます。

**適用対象:** BLOB、ファイル

### <a name="setcontenttypecontent-type"></a>/SetContentType:"content-type"
コピー先の BLOB またはファイルの MIME コンテンツの種類を指定します。

既定では、AzCopy は BLOB またはファイルのコンテンツの種類を application/octet-stream に設定します。 このオプションで値を明示的に指定することで、すべての BLOB またはファイルのコンテンツの種類を設定できます。

このオプションで値を指定しない場合、AzCopy は各 BLOB またはファイルのコンテンツの種類をそのファイルの拡張子に応じて設定します。

**適用対象:** BLOB、ファイル

### <a name="payloadformatjson--csv"></a>/PayloadFormat:"JSON" | "CSV"
テーブルのエクスポートされたデータ ファイルの形式を指定します。

このオプションが指定されていない場合、AzCopy では既定で JSON 形式でテーブル データ ファイルがエクスポートされます。

**適用対象:** テーブル

## <a name="known-issues-and-best-practices"></a>既知の問題とベスト プラクティス
### <a name="limit-concurrent-writes-while-copying-data"></a>データのコピー中の同時書き込みの制限
AzCopy を使用して BLOB またはファイルをコピーする場合は、コピー中に別のアプリケーションがそのデータを変更している可能性があることに注意してください。 可能な場合は、コピーしているデータがコピー操作中に変更されないようにしてください。 たとえば、Azure 仮想マシンに関連付けられている VHD をコピーしている場合は、その VHD に別のアプリケーションが書き込まないようにします。 これを行う適切な方法は、コピーされるリソースをリースすることです。 または、最初に VHD のスナップショットを作成してからそのスナップショットをコピーします。

コピーしている BLOB またはファイルに対する他のアプリケーションによる書き込みを回避できない場合は、ジョブが終了した時点で、コピー対象のリソースがソース リソースとの間に完全なパリティを保持していない可能性があることを覚えておいてください。

### <a name="run-one-azcopy-instance-on-one-machine"></a>1 つのマシンでは&1; つの AzCopy インスタンスを実行します。
AzCopy はマシン リソースの利用率を最大限に高めてデータ転送を高速化する設計になっています。1 つのマシンで実行する AzCopy インスタンスは&1; つのみとすること、同時実行操作の数を増やす必要がある場合はオプション `/NC` を指定することをお勧めします。 詳細については、コマンド ラインに「 `AzCopy /?:NC` 」と入力してください。

### <a name="enable-fips-compliant-md5-algorithms-for-azcopy-when-you-use-fips-compliant-algorithms-for-encryption-hashing-and-signing"></a>[暗号化、ハッシュ、署名のための FIPS 準拠アルゴリズムを使う] を有効にする場合、AzCopy に対して FIPS 準拠の MD5 アルゴリズムを有効にします。
AzCopy はオブジェクトをコピーするときに、既定で .NET MD5 実装を使用して MD5 を計算しますが、AzCopy で FIPS 準拠の MD5 設定を有効にするために必要ないくつかのセキュリティ要件があります。

プロパティ `AzureStorageUseV1MD5` を含む App.config ファイル `AzCopy.exe.config` を作成し、それを AzCopy.exe と共に取っておくことができます。

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
      <appSettings>
        <add key="AzureStorageUseV1MD5" value="false"/>
      </appSettings>
    </configuration>

“AzureStorageUseV1MD5” プロパティの場合 • True - 既定値。AzCopy は .NET MD5 実装を使用します。
• False – AzCopy は FIPS 準拠の MD5 アルゴリズムを使用します。

FIPS 準拠アルゴリズムは既定で Windows マシンでは無効に設定されます。secpol.msc を Run ウィンドウに入力し、この切り替えを [セキュリティ設定]、[ローカル ポリシー]、[セキュリティのオプション]、[システム暗号化] の順にクリックして確認できます。暗号化、ハッシュ、署名には、FIPS 準拠のアルゴリズムを使用します。

## <a name="next-steps"></a>次のステップ
Azure Storage および AzCopy の詳細については、以下のリソースを参照してください。

### <a name="azure-storage-documentation"></a>Azure Storage のドキュメント:
* [Azure ストレージの概要](storage-introduction.md)
* [.NET から BLOB ストレージを使用する方法](storage-dotnet-how-to-use-blobs.md)
* [Java からファイル ストレージを使用する方法](storage-dotnet-how-to-use-files.md)
* [.NET からテーブル ストレージを使用する方法](storage-dotnet-how-to-use-tables.md)
* [ストレージ アカウントを作成、管理、または削除する方法](storage-create-storage-account.md)

### <a name="azure-storage-blog-posts"></a>Azure Storage に関するブログの投稿:
* [Azure Storage Data Movement Library プレビューの概要](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
* [AzCopy: Introducing synchronous copy and customized content type (AzCopy: 同期コピーとカスタマイズしたコンテンツの種類の概要)](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
* [AzCopy: Announcing General Availability of AzCopy 3.0 plus preview release of AzCopy 4.0 with Table and File support (AzCopy: AzCopy 3.0 の一般公開とテーブルおよびファイルのサポートを伴う AzCopy 4.0 のプレビュー リリースのお知らせ)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
* [AzCopy: Optimized for Large-Scale Copy Scenarios (AzCopy: 大量のコピーのシナリオ用の最適化)](http://go.microsoft.com/fwlink/?LinkId=507682)
* [AzCopy - Support for read-access geo-redundant storage (AzCopy - 地理冗長ストレージの読み取りアクセスのサポート)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
* [AzCopy - Transfer data with re-startable mode and SAS token (AzCopy - 再起動可能モードまたは SAS トークンを使用したデータの転送)](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
* [AzCopy: Using cross-account Copy Blob (AzCopy: アカウント間での BLOB のコピー)](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
* [AzCopy - Uploading/downloading files for Azure Blobs (AzCopy - Azure BLOB に対するファイルのアップロードおよびダウンロード)](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)


