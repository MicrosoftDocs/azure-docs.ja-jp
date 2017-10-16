---
title: "AzCopy on Linux で Azure Storage にデータをコピーまたは移動する | Microsoft Docs"
description: "AzCopy on Linux ユーティリティを使用して、BLOB およびファイル コンテンツとの間でデータを移動またはコピーします。 ローカル ファイルから Azure ストレージにデータをコピーする、またはストレージ アカウント内またはその間でデータをコピーします。 Azure Storage にデータを簡単に移行します。"
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
ms.date: 28/9/2017
ms.author: seguler
ms.translationtype: HT
ms.sourcegitcommit: 8ad98f7ef226fa94b75a8fc6b2885e7f0870483c
ms.openlocfilehash: 0b6417b616a9e4e74b5fb8a67e1414ad74e8f258
ms.contentlocale: ja-jp
ms.lasthandoff: 09/29/2017

---
# <a name="transfer-data-with-azcopy-on-linux"></a>AzCopy on Linux を使用したデータの転送
AzCopy on Linux は、最適なパフォーマンスの単純なコマンドを使用して、Microsoft Azure の Blob Storage および File Storage との間でデータをコピーするために設計されたコマンドライン ユーティリティです。 ストレージ アカウント内のあるオブジェクトから別のオブジェクトにデータをコピーしたり、ストレージ アカウント間でコピーしたりすることができます。

ダウンロードできる AzCopy には、2 つのバージョンがあります。 AzCopy on Linux は .NET Core Framework を使って構築されています。その対象プラットフォームは Linux で、POSIX スタイルのコマンド ライン オプションが備わっています。 [AzCopy on Windows](../storage-use-azcopy.md) は .NET Framework を使って構築され、Windows スタイルのコマンド ライン オプションが備わっています。 この記事では AzCopy on Linux について説明します。

## <a name="download-and-install-azcopy"></a>AzCopy のダウンロードとインストール
### <a name="installation-on-linux"></a>Linux へのインストール

この記事には、Ubuntu のさまざまなリリース用のコマンドが含まれています。  `lsb_release -a` コマンドを使用して、配布リリースおよびコードネームをご確認ください。 

AzCopy on Linux では、.NET Core Framework (バージョン 1.1.x) がプラットフォームに存在している必要があります。 [.NET Core](https://www.microsoft.com/net/download/linux) ページのインストール手順を参照してください。

例として、.NET Core を Ubuntu 16.04 にインストールしてみましょう。 最新のインストール ガイドについては、[.NET Core on Linux](https://www.microsoft.com/net/download/linux) のインストール ページを参照してください。


```bash
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-get update
sudo apt-get install dotnet-dev-1.1.4
```

.NET Core をインストールしたら、AzCopy をダウンロードしてインストールします。

```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopyprlinux
tar -xf azcopy.tar.gz
sudo ./install.sh
```

抽出したファイルは、AzCopy on Linux のインストール後、削除してかまいません。 スーパーユーザーの特権がない場合は、抽出したフォルダー内のシェル スクリプト "azcopy" を使用して AzCopy を実行することもできます。 


## <a name="writing-your-first-azcopy-command"></a>AzCopy コマンドの基本
AzCopy コマンドの基本構文は次のとおりです。

```azcopy
azcopy --source <source> --destination <destination> [Options]
```

以下の例では、Microsoft Azure の BLOB および File との間でデータをコピーするさまざまなシナリオを紹介しています。 各サンプルで使用されているパラメーターの詳細な説明については、`azcopy --help` メニューを参照してください。

## <a name="blob-download"></a>BLOB: ダウンロード
### <a name="download-single-blob"></a>1 つの BLOB をダウンロードする

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "abc.txt"
```

フォルダー `/mnt/myfiles` が存在しない場合、AzCopy によってフォルダーが作成され、この新しいフォルダーに `abc.txt ` がダウンロードされます。

### <a name="download-single-blob-from-secondary-region"></a>セカンダリ リージョンから 1 つの BLOB をダウンロードする

```azcopy
azcopy \
    --source https://myaccount-secondary.blob.core.windows.net/mynewcontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "abc.txt"
```

地理冗長ストレージに対応する読み取りアクセス権を持っている必要があります。

### <a name="download-all-blobs"></a>すべての BLOB をダウンロードする

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

指定されたコンテナーに以下の BLOB があるとします。  

```
abc.txt
abc1.txt
abc2.txt
vd1/a.txt
vd1/abcd.txt
```

ダウンロード操作後に、ディレクトリ `/mnt/myfiles` には以下のファイルがあります。

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/vd1/a.txt
/mnt/myfiles/vd1/abcd.txt
```

オプション `--recursive` を指定しない場合、BLOB はダウンロードされません。

### <a name="download-blobs-with-specified-prefix"></a>指定したプレフィックスの BLOB をダウンロードする

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "a" \
    --recursive
```

指定されたコンテナーに以下の BLOB があるとします。 プレフィックス `a` で始まるすべての BLOB がダウンロードされます。

```
abc.txt
abc1.txt
abc2.txt
xyz.txt
vd1\a.txt
vd1\abcd.txt
```

ダウンロード操作後に、フォルダー `/mnt/myfiles` には以下のファイルがあります。

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
```

プレフィックスは仮想ディレクトリに適用され、BLOB 名の最初の部分に付けられます。 前述の例では、仮想ディレクトリは指定されたプレフィックスと一致しないので、BLOB は一切ダウンロードされません。 さらに、オプション `--recursive` が指定されていない場合、AzCopy では BLOB はダウンロードされません。

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>エクスポートしたファイルの最終変更時刻をソース BLOB と同時刻に設定する

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination "/mnt/myfiles" \
    --source-key <key> \
    --preserve-last-modified-time
```

最終変更時刻に基づいてダウンロード操作の対象から BLOB を除外することもできます。 たとえば、対象ファイルの最終変更時刻以降の BLOB を除外する場合、 `--exclude-newer` オプションを追加します。

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-newer
```

または、対象ファイルの最終変更時刻以前の BLOB を除外する場合、 `--exclude-older` オプションを追加します。

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-older
```

## <a name="blob-upload"></a>BLOB: アップロード
### <a name="upload-single-file"></a>1 つのファイルをアップロードする

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --include "abc.txt"
```

存在しない宛先コンテナーを指定すると、AzCopy によってコンテナーが作成され、そのコンテナーにファイルがアップロードされます。

### <a name="upload-single-file-to-virtual-directory"></a>1 つのファイルを仮想ディレクトリにアップロードする

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --include "abc.txt"
```

存在しない仮想ディレクトリを指定すると、アップロードされるファイルの BLOB 名に仮想ディレクトリが含められます (*例* `vd/abc.txt` 例: 上記の例の)。

### <a name="upload-all-files"></a>すべてのファイルをアップロードする

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --recursive
```

オプション `--recursive` を指定すると、指定したディレクトリの内容が再帰的にアップロードされます。つまり、すべてのサブフォルダーと、それらのサブフォルダーに含まれるファイルも BLOB ストレージにアップロードされます。 たとえば、フォルダー `/mnt/myfiles` に以下のファイルがあるとします。

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/subfolder/a.txt
/mnt/myfiles/subfolder/abcd.txt
```

アップロード操作後に、コンテナーには以下のファイルがあります。

```
abc.txt
abc1.txt
abc2.txt
subfolder/a.txt
subfolder/abcd.txt
```

オプション `--recursive` を指定しなかった場合、次の 3 つのファイルだけがアップロードされます。

```
abc.txt
abc1.txt
abc2.txt
```

### <a name="upload-files-matching-specified-pattern"></a>指定したパターンに一致するファイルをアップロードする

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --include "a*" \
    --recursive
```

フォルダー `/mnt/myfiles`に以下のファイルがあるとします。

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/xyz.txt
/mnt/myfiles/subfolder/a.txt
/mnt/myfiles/subfolder/abcd.txt
```

アップロード操作後に、コンテナーには以下のファイルがあります。

```
abc.txt
abc1.txt
abc2.txt
subfolder/a.txt
subfolder/abcd.txt
```

オプション `--recursive` を指定しなかった場合、サブディレクトリ内のファイルはスキップされます。

```
abc.txt
abc1.txt
abc2.txt
```

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>コピー先の BLOB の MIME コンテンツの種類を指定する
既定では、AzCopy はコピー先の BLOB のコンテンツの種類を `application/octet-stream`に設定します。 ただしコンテンツの種類は、オプション `--set-content-type [content-type]` を使用して明示的に指定できます。 この構文は、アップロード操作におけるすべての BLOB のコンテンツの種類を設定します。

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type "video/mp4"
```

オプション `--set-content-type` で値を指定しない場合、AzCopy は各 BLOB またはファイルのコンテンツの種類をそのファイルの拡張子に応じて設定します。

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type
```

## <a name="blob-copy"></a>BLOB: コピー
### <a name="copy-single-blob-within-storage-account"></a>ストレージ アカウント内の 1 つの BLOB をコピーする

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1 \
    --destination https://myaccount.blob.core.windows.net/mycontainer2 \
    --source-key <key> \
    --dest-key <key> \
    --include "abc.txt"
```

--sync-copy オプションを指定せずに 1 つの BLOB をコピーすると、[サーバー側でコピー](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx)操作が実行されます。

### <a name="copy-single-blob-across-storage-accounts"></a>複数のストレージ アカウントに 1 つの BLOB をコピーする

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1 \
    --destination https://destaccount.blob.core.windows.net/mycontainer2 \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt"
```

--sync-copy オプションを指定せずに 1 つの BLOB をコピーすると、[サーバー側でコピー](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx)操作が実行されます。

### <a name="copy-single-blob-from-secondary-region-to-primary-region"></a>1 つの BLOB をセカンダリ リージョンからプライマリ リージョンにコピーする

```azcopy
azcopy \
    --source https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 \
    --destination https://myaccount2.blob.core.windows.net/mynewcontainer2 \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt"
```

地理冗長ストレージに対応する読み取りアクセス権を持っている必要があります。

### <a name="copy-single-blob-and-its-snapshots-across-storage-accounts"></a>複数のストレージ アカウントに 1 つの BLOB とそのスナップショットをコピーする

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1 \
    --destination https://destaccount.blob.core.windows.net/mycontainer2 \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt" \
    --include-snapshot
```

コピー操作後、ターゲット コンテナーには BLOB とそのスナップショットが含まれます。 このコンテナーには、次の BLOB とそのスナップショットが含まれます。

```
abc.txt
abc (2013-02-25 080757).txt
abc (2014-02-21 150331).txt
```

### <a name="synchronously-copy-blobs-across-storage-accounts"></a>複数のストレージ アカウントに複数の BLOB を同期的にコピーする
既定では、AzCopy は 2 つのストレージ エンドポイント間で非同期でデータをコピーします。 そのため、コピー操作は、BLOB のコピー速度に関する SLA のない予備の帯域幅容量を使用してバックグラウンドで実行されます。 

`--sync-copy` オプションは、コピー操作が一定の速度になるようにします。 AzCopy は、指定されたソースからローカル メモリにコピーして BLOB をダウンロードし、これを BLOB ストレージのコピー先にアップロードすることで同期コピーを実行します。

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/myContainer/ \
    --destination https://myaccount2.blob.core.windows.net/myContainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "ab" \
    --sync-copy
```

`--sync-copy` では、非同期コピーと比較すると、追加の送信コストが発生する可能性があります。 この送信コストが発生しないように、このオプションは、ソース ストレージ アカウントと同じリージョンにある Azure VM で使用することをお勧めします。

## <a name="file-download"></a>ファイル: ダウンロード
### <a name="download-single-file"></a>1 つのファイルをダウンロードする

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/myfolder1/ \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "abc.txt"
```

指定されたソースが Azure ファイル共有の場合は、厳密なファイル名 (*例*: `abc.txt`) を指定して単一ファイルをダウンロードするか、オプション `--recursive` を指定して共有内の全ファイルを再帰的にダウンロードする必要があります。 ファイル パターンとオプション `--recursive` の両方を同時に指定しようとすると、エラーになります。

### <a name="download-all-files"></a>すべてのファイルをダウンロードする

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/ \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

空のフォルダーはダウンロードされないことに注意してください。

## <a name="file-upload"></a>ファイル: アップロード
### <a name="upload-single-file"></a>1 つのファイルをアップロードする

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --include abc.txt
```

### <a name="upload-all-files"></a>すべてのファイルをアップロードする

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --recursive
```

空のフォルダーはアップロードされないことに注意してください。

### <a name="upload-files-matching-specified-pattern"></a>指定したパターンに一致するファイルをアップロードする

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --include "ab*" \
    --recursive
```

## <a name="file-copy"></a>ファイル: コピー
### <a name="copy-across-file-shares"></a>ファイル共有間でコピーする

```azcopy
azcopy \
    --source https://myaccount1.file.core.windows.net/myfileshare1/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
ファイル共有をまたがってファイルをコピーすると、[サーバー側でコピー](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx)操作が実行されます。

### <a name="copy-from-file-share-to-blob"></a>ファイル共有から BLOB にコピーする

```azcopy
azcopy \ 
    --source https://myaccount1.file.core.windows.net/myfileshare/ \
    --destination https://myaccount2.blob.core.windows.net/mycontainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
ファイル共有から BLOB にファイルをコピーすると、[サーバー側でコピー](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx)操作が実行されます。

### <a name="copy-from-blob-to-file-share"></a>BLOB からファイル共有にコピーする

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/mycontainer/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
BLOB からファイル共有にファイルをコピーすると、[サーバー側でコピー](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx)操作が実行されます。

### <a name="synchronously-copy-files"></a>ファイルを同期的にコピーする
オプション `--sync-copy` を指定すると、File Storage から File Storage に、File Storage から Blob Storage に、Blob Storage から File Storage にデータを同期的にコピーすることができます。 この操作は、AzCopy がソース データをローカル メモリにダウンロードし、それを宛先に再度アップロードすることによって実行されます。 その場合は、標準的な送信コストが適用されます。

```azcopy
azcopy \
    --source https://myaccount1.file.core.windows.net/myfileshare1/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive \
    --sync-copy
```

File Storage から Blob Storage にコピーするときの既定の BLOB タイプはブロック BLOB ですが、ユーザーはオプション `/BlobType:page` を指定して宛先 BLOB タイプを変更できます。

ただし `--sync-copy` では、非同期コピーと比較すると、追加の送信コストが発生する可能性があります。 この送信コストが発生しないように、このオプションは、ソース ストレージ アカウントと同じリージョンにある Azure VM で使用することをお勧めします。

## <a name="other-azcopy-features"></a>その他の AzCopy 機能
### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>宛先に存在しないデータのみをコピーする
`--exclude-older` パラメーターを指定すると古いソース リソースを、`--exclude-newer` パラメーターを指定すると新しいソース リソースをコピー対象から除外できます。 宛先に存在しないソース リソースのみをコピーする場合は、AzCopy コマンドに両方のパラメーターを指定します。

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --exclude-older --exclude-newer

    --source /mnt/myfiles --destination http://myaccount.file.core.windows.net/myfileshare --dest-key <destkey> --recursive --exclude-older --exclude-newer

    --source http://myaccount.blob.core.windows.net/mycontainer --destination http://myaccount.blob.core.windows.net/mycontainer1 --source-key <sourcekey> --dest-key <destkey> --recursive --exclude-older --exclude-newer

### <a name="use-a-configuration-file-to-specify-command-line-parameters"></a>コマンド ライン パラメーターを指定するための構成ファイルの使用

```azcopy
azcopy --config-file "azcopy-config.ini"
```

構成ファイルには、任意の AzCopy コマンド ライン パラメーターを含めることができます。 AzCopy は、ファイルの内容に直接置換することで、ファイルのパラメーターをコマンド ラインで指定されているかのように処理します。

`copyoperation` という名前の構成ファイルに、以下の行が含まれているとします。 各 AzCopy パラメーターは 1 行に指定できます。

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --quiet

また、別の行に指定することもできます。

    --source http://myaccount.blob.core.windows.net/mycontainer
    --destination /mnt/myfiles
    --source-key<sourcekey>
    --recursive
    --quiet

`--source-key` パラメーターの場合、次の例のようにパラメーターが 2 行に分かれていると、AzCopy は失敗します。

    http://myaccount.blob.core.windows.net/mycontainer
    /mnt/myfiles
    --sourcekey
    <sourcekey>
    --recursive
    --quiet

### <a name="specify-a-shared-access-signature-sas"></a>共有アクセス署名 (SAS) の指定

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1 \
    --destination https://myaccount.blob.core.windows.net/mycontainer2 \
    --source-sas <SAS1> \
    --dest-sas <SAS2> \
    --include abc.txt
```

コンテナー URI に対して SAS を指定することもできます。

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken \
    --destination /mnt/myfiles \
    --recursive
```

現在 AzCopy でサポートされるのは[アカウント SAS](https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1) だけであることに注意してください。

### <a name="journal-file-folder"></a>ジャーナル ファイル フォルダー
AzCopy にコマンドが発行されるたびに、AzCopy は既定のフォルダーまたはこのオプションで指定されたフォルダーにジャーナル ファイルが存在するかどうかを確認します。 どちらの場所にもジャーナル ファイルがない場合は、AzCopy は新しい操作であると認識し、新しいジャーナル ファイルを作成します。

既存のジャーナル ファイルがある場合は、AzCopy は入力されたコマンド ラインがジャーナル ファイルのコマンド ラインと一致するかどうかを確認します。 2 つのコマンド ラインが一致する場合、AzCopy は未完了の操作を再開します。 一致しない場合は、ジャーナル ファイルを上書きして新しい操作を開始するか、現在の操作を取り消すかをたずねるメッセージが表示されます。

ジャーナル ファイルに既定の場所を使用する場合:

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --resume
```

前に示したように、オプション `--resume` を省略するか、フォルダー パスなしでオプション `--resume` を指定すると、AzCopy は既定の場所である `~\Microsoft\Azure\AzCopy` にジャーナル ファイルを作成します。 既存のジャーナル ファイルがある場合、このジャーナル ファイルに基づいて AzCopy が操作を再開します。

ジャーナル ファイルにカスタムの場所を指定する場合:

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key key \
    --resume "/mnt/myjournal"
```

この例では、既存のジャーナル ファイルがない場合に、これを作成します。 既存のファイルがある場合、AzCopy はこのジャーナル ファイルに基づいて操作を再開します。

AzCopy の操作を再開するには、同じコマンドをもう一度実行します。 AzCopy on Linux から確認のメッセージが表示されます。

```azcopy
Incomplete operation with same command line detected at the journal directory "/home/myaccount/Microsoft/Azure/AzCopy", do you want to resume the operation? Choose Yes to resume, choose No to overwrite the journal to start a new operation. (Yes/No)
```

### <a name="output-verbose-logs"></a>詳細ログの出力

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --verbose
```

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>開始する同時操作の数の指定
オプション `--parallel-level` を使用して同時コピー操作の数を指定します。 既定では、AzCopy はデータ転送のスループットを向上するために一定数の同時操作を開始します。 同時実行操作の数は、所有するプロセッサ数の 8 倍になります。 低帯域幅のネットワークで AzCopy を実行している場合は、--parallel-level に少ない数を指定することで、リソースの競合で生じる失敗を回避できます。

[!TIP]
>AzCopy の全パラメーターを一覧表示するには、"azcopy --help" メニューを確認してください。

## <a name="known-issues-and-best-practices"></a>既知の問題とベスト プラクティス
### <a name="error-net-core-is-not-found-in-the-system"></a>エラー: .NET Core がシステムに見つからない
.NET Core がシステムにインストールされていないことを示すエラーが表示される場合、.NET Core のバイナリ `dotnet` の PATH が欠落している可能性があります。

この問題を解決するには、次のコマンドを使用して、該当する .NET Core バイナリをシステム内で検索します。
```bash
sudo find / -name dotnet
```

このコマンドを実行すると、dotnet バイナリのパスが返されます。 

    /opt/rh/rh-dotnetcore11/root/usr/bin/dotnet
    /opt/rh/rh-dotnetcore11/root/usr/lib64/dotnetcore/dotnet
    /opt/rh/rh-dotnetcore11/root/usr/lib64/dotnetcore/shared/Microsoft.NETCore.App/1.1.2/dotnet

次に、そのパスを PATH 変数に追加します。 sudo の場合、secure_path を編集して dotnet バイナリのパスを追加します。
```bash 
sudo visudo
### Append the path found in the preceding example to 'secure_path' variable
```

この例では、secure_path 変数は次のようになります。

```
secure_path = /sbin:/bin:/usr/sbin:/usr/bin:/opt/rh/rh-dotnetcore11/root/usr/bin/
```

現在のユーザーの場合、.bash_profile/.profile を編集して、dotnet バイナリのパスを PATH 変数に追加します。 
```bash
vi ~/.bash_profile
### Append the path found in the preceding example to 'PATH' variable
```

.NET Core が PATH に追加されていることを確認します。
```bash
which dotnet
sudo which dotnet
```

### <a name="error-installing-azcopy"></a>AzCopy のインストール中のエラー
AzCopy のインストールで問題が発生した場合は、抽出した `azcopy` フォルダーにある bash スクリプトを使って AzCopy を実行してみてください。

```bash
cd azcopy
./azcopy
```

### <a name="limit-concurrent-writes-while-copying-data"></a>データのコピー中の同時書き込みの制限
AzCopy を使用して BLOB またはファイルをコピーする場合は、コピー中に別のアプリケーションがそのデータを変更している可能性があることに注意してください。 可能な場合は、コピーしているデータがコピー操作中に変更されないようにしてください。 たとえば、Azure 仮想マシンに関連付けられている VHD をコピーしている場合は、その VHD に別のアプリケーションが書き込まないようにします。 これを行う適切な方法は、コピーされるリソースをリースすることです。 または、最初に VHD のスナップショットを作成してからそのスナップショットをコピーします。

コピーしている BLOB またはファイルに対する他のアプリケーションによる書き込みを回避できない場合は、ジョブが終了した時点で、コピー対象のリソースがソース リソースとの間に完全なパリティを保持していない可能性があることを覚えておいてください。

### <a name="run-one-azcopy-instance-on-one-machine"></a>1 つのマシンでは 1 つの AzCopy インスタンスを実行します。
AzCopy はマシン リソースの利用率を最大限に高めてデータ転送を高速化する設計になっています。1 つのマシンで実行する AzCopy インスタンスは 1 つのみとすること、同時実行操作の数を増やす必要がある場合はオプション `--parallel-level` を指定することをお勧めします。 詳細については、コマンド ラインに「 `AzCopy --help parallel-level` 」と入力してください。

## <a name="next-steps"></a>次のステップ
Azure Storage および AzCopy の詳細については、以下のリソースを参照してください。

### <a name="azure-storage-documentation"></a>Azure Storage のドキュメント:
* [Azure ストレージの概要](../storage-introduction.md)
* [ストレージ アカウントの作成](../storage-create-storage-account.md)
* [ストレージ エクスプローラーを使用した BLOB の管理](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-explorer-blobs)
* [Azure Storage での Azure CLI 2.0 の使用](../storage-azure-cli.md)
* [C++ から BLOB ストレージを使用する方法](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Java から BLOB ストレージを使用する方法](../blobs/storage-java-how-to-use-blob-storage.md)
* [Node.js から BLOB ストレージを使用する方法](../blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Python から BLOB ストレージを使用する方法](../blobs/storage-python-how-to-use-blob-storage.md)

### <a name="azure-storage-blog-posts"></a>Azure Storage に関するブログの投稿:
* [Announcing AzCopy on Linux Preview (AzCopy on Linux プレビューの発表)](https://azure.microsoft.com/en-in/blog/announcing-azcopy-on-linux-preview/)
* [Azure Storage Data Movement Library プレビューの概要](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
* [AzCopy: Introducing synchronous copy and customized content type (AzCopy: 同期コピーとカスタマイズしたコンテンツの種類の概要)](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
* [AzCopy: Announcing General Availability of AzCopy 3.0 plus preview release of AzCopy 4.0 with Table and File support (AzCopy: AzCopy 3.0 の一般公開とテーブルおよびファイルのサポートを伴う AzCopy 4.0 のプレビュー リリースのお知らせ)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
* [AzCopy: Optimized for Large-Scale Copy Scenarios (AzCopy: 大量のコピーのシナリオ用の最適化)](http://go.microsoft.com/fwlink/?LinkId=507682)
* [AzCopy - Support for read-access geo-redundant storage (AzCopy - 地理冗長ストレージの読み取りアクセスのサポート)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
* [AzCopy - Transfer data with restartable mode and SAS token (AzCopy - 再起動可能モードまたは SAS トークンを使用したデータの転送)](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
* [AzCopy: Using cross-account Copy Blob (AzCopy: アカウント間での BLOB のコピー)](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
* [AzCopy - Uploading/downloading files for Azure Blobs (AzCopy - Azure BLOB に対するファイルのアップロードおよびダウンロード)](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)


