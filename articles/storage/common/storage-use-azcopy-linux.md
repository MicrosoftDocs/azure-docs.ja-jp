---
title: AzCopy on Linux で Azure Storage にデータをコピーまたは移動する | Microsoft Docs
description: AzCopy on Linux ユーティリティを使用して、BLOB およびファイル コンテンツとの間でデータを移動またはコピーします。 ローカル ファイルから Azure ストレージにデータをコピーする、またはストレージ アカウント内またはその間でデータをコピーします。 Azure Storage にデータを簡単に移行します。
services: storage
author: seguler
ms.service: storage
ms.topic: article
ms.date: 04/26/2018
ms.author: seguler
ms.subservice: common
ms.openlocfilehash: 0f87645537576f49ee04b823341acf8853798f88
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58882227"
---
# <a name="transfer-data-with-azcopy-on-linux"></a>AzCopy on Linux を使用したデータの転送

AzCopy は、最適なパフォーマンスのためのシンプルなコマンドを使用して Microsoft Azure Blob Storage および File Storage との間でデータをコピーするために設計されたコマンドライン ユーティリティです。 ファイル システムとストレージ アカウント間、またはストレージ アカウント間でデータをコピーできます。  

ダウンロードできる AzCopy には、2 つのバージョンがあります。 AzCopy on Linux の対象プラットフォームは Linux で、POSIX スタイルのコマンドライン オプションが用意されています。 [AzCopy on Windows](../storage-use-azcopy.md) には、Windows スタイルのコマンドライン オプションが用意されています。 この記事では AzCopy on Linux について説明します。 

> [!NOTE]  
> AzCopy 7.2 バージョン以降、.NET Core の依存関係は AzCopy パッケージにパッケージ化されています。 7.2 バージョン以降を使用する場合、前提条件として .NET Core をインストールする必要はなくなりました。

## <a name="download-and-install-azcopy"></a>AzCopy のダウンロードとインストール

### <a name="installation-on-linux"></a>Linux へのインストール

> [!NOTE]
> ディストリビューションに応じて、[.NET Core の前提条件に関する記事](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)に示されている .NET Core 2.1 の依存関係のインストールが必要な場合があります。 
>
> RHEL 7 ディストリビューションでは、ICU と libunwind の依存関係をインストールします: ```yum install -y libunwind icu```

AzCopy on Linux (v7.2 以降) のインストールは簡単で、tar パッケージを抽出し、インストール スクリプトを実行するだけです。 

**RHEL 6 ベースのディストリビューション**: [ダウンロード リンク](https://aka.ms/downloadazcopylinuxrhel6)
```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinuxrhel6
tar -xf azcopy.tar.gz
sudo ./install.sh
```

**その他すべての Linux ディストリビューション**: [ダウンロード リンク](https://aka.ms/downloadazcopylinux64)
```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinux64
tar -xf azcopy.tar.gz
sudo ./install.sh
```

抽出したファイルは、AzCopy on Linux のインストール後、削除してかまいません。 スーパーユーザーの特権がない場合は、抽出したフォルダー内のシェル スクリプト azcopy を使用して `azcopy` を実行することもできます。

### <a name="alternative-installation-on-ubuntu"></a>Ubuntu のバージョンごとのインストール方法

**Ubuntu 14.04**

Microsoft Linux 製品リポジトリの apt ソースを追加し、AzCopy をインストールします。

```bash
sudo echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-trusty-prod/ trusty main" > azure.list
sudo cp ./azure.list /etc/apt/sources.list.d/
sudo apt-key adv --keyserver packages.microsoft.com --recv-keys EB3E94ADBE1229CF
```

```bash
sudo apt-get update
sudo apt-get install azcopy
```

**Ubuntu 16.04**

Microsoft Linux 製品リポジトリの apt ソースを追加し、AzCopy をインストールします。

```bash
echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod/ xenial main" > azure.list
sudo cp ./azure.list /etc/apt/sources.list.d/
sudo apt-key adv --keyserver packages.microsoft.com --recv-keys EB3E94ADBE1229CF
```

```bash
sudo apt-get update
sudo apt-get install azcopy
```

## <a name="writing-your-first-azcopy-command"></a>AzCopy コマンドの基本
AzCopy コマンドの基本構文は次のとおりです。

```azcopy
azcopy --source <source> --destination <destination> [Options]
```

以下の例では、Microsoft Azure の BLOB および File との間でデータをコピーするさまざまなシナリオを紹介しています。 各サンプルで使用されているパラメーターの詳細な説明については、`azcopy --help` メニューを参照してください。

## <a name="blob-download"></a>BLOB:ダウンロード
### <a name="download-single-blob"></a>1 つの BLOB をダウンロードする

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key> 
```

フォルダー `/mnt/myfiles` が存在しない場合、AzCopy によってフォルダーが作成され、この新しいフォルダーに `abc.txt` がダウンロードされます。 

### <a name="download-single-blob-from-secondary-region"></a>セカンダリ リージョンから 1 つの BLOB をダウンロードする

```azcopy
azcopy \
    --source https://myaccount-secondary.blob.core.windows.net/mynewcontainer/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key>
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

## <a name="blob-upload"></a>BLOB:アップロード
### <a name="upload-single-file"></a>1 つのファイルをアップロードする

```azcopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer/abc.txt \
    --dest-key <key>
```

存在しない宛先コンテナーを指定すると、AzCopy によってコンテナーが作成され、そのコンテナーにファイルがアップロードされます。

### <a name="upload-single-file-to-virtual-directory"></a>1 つのファイルを仮想ディレクトリにアップロードする

```azcopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer/vd/abc.txt \
    --dest-key <key>
```

存在しない仮想ディレクトリを指定すると、アップロードされるファイルの BLOB 名に仮想ディレクトリが含められます (*例* `vd/abc.txt` 例: 上記の例の)。

### <a name="redirect-from-stdin"></a>stdin からリダイレクトする

```azcopy
gzip myarchive.tar -c | azcopy \
    --destination https://myaccount.blob.core.windows.net/mycontainer/mydir/myarchive.tar.gz \
    --dest-key <key>
```

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

### <a name="customizing-the-mime-content-type-mapping"></a>MIME コンテンツの種類のマッピングのカスタマイズ
AzCopy は、ファイル拡張子とコンテンツの種類のマッピングが含まれる構成ファイルを使用します。 このマッピングをカスタマイズし、必要に応じて新しいペアを追加できます。 マッピングは ```/usr/lib/azcopy/AzCopyConfig.json``` に配置されます。

## <a name="blob-copy"></a>BLOB:コピー
### <a name="copy-single-blob-within-storage-account"></a>ストレージ アカウント内の 1 つの BLOB をコピーする

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-key <key> \
    --dest-key <key>
```

--sync-copy オプションを指定せずに 1 つの BLOB をコピーすると、[サーバー側でコピー](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx)操作が実行されます。

### <a name="copy-single-blob-across-storage-accounts"></a>複数のストレージ アカウントに 1 つの BLOB をコピーする

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://destaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-key <key1> \
    --dest-key <key2>
```

--sync-copy オプションを指定せずに 1 つの BLOB をコピーすると、[サーバー側でコピー](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx)操作が実行されます。

### <a name="copy-single-blob-from-secondary-region-to-primary-region"></a>1 つの BLOB をセカンダリ リージョンからプライマリ リージョンにコピーする

```azcopy
azcopy \
    --source https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1/abc.txt \
    --destination https://myaccount2.blob.core.windows.net/mynewcontainer2/abc.txt \
    --source-key <key1> \
    --dest-key <key2>
```

地理冗長ストレージに対応する読み取りアクセス権を持っている必要があります。

### <a name="copy-single-blob-and-its-snapshots-across-storage-accounts"></a>複数のストレージ アカウントに 1 つの BLOB とそのスナップショットをコピーする

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1/ \
    --destination https://destaccount.blob.core.windows.net/mycontainer2/ \
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
    --source https://myaccount.file.core.windows.net/myfileshare/myfolder1/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key>
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
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.file.core.windows.net/myfileshare/abc.txt \
    --dest-key <key>
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
ファイル共有をまたがってファイルをコピーすると、[サーバー側でコピー](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx)操作が実行されます。

### <a name="copy-from-file-share-to-blob"></a>ファイル共有から BLOB にコピーする

```azcopy
azcopy \ 
    --source https://myaccount1.file.core.windows.net/myfileshare/ \
    --destination https://myaccount2.blob.core.windows.net/mycontainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
ファイル共有から BLOB にファイルをコピーすると、[サーバー側でコピー](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx)操作が実行されます。

### <a name="copy-from-blob-to-file-share"></a>BLOB からファイル共有にコピーする

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/mycontainer/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
BLOB からファイル共有にファイルをコピーすると、[サーバー側でコピー](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx)操作が実行されます。

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

File Storage から Blob Storage にコピーするときの既定の BLOB タイプはブロック BLOB ですが、ユーザーはオプション `--blob-type page` を指定して宛先 BLOB タイプを変更できます。 使用可能なタイプは `page | block | append` です。

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
    --source https://myaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-sas <SAS1> \
    --dest-sas <SAS2>
```

コンテナー URI に対して SAS を指定することもできます。

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken \
    --destination /mnt/myfiles \
    --recursive
```

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

>[!TIP]
>AzCopy の全パラメーターを一覧表示するには、"azcopy --help" メニューを確認してください。

## <a name="installation-steps-for-azcopy-71-and-earlier-versions"></a>AzCopy 7.1 以前のバージョンのインストール手順

AzCopy on Linux (v7.1 以前のみ) には、.NET Core Framework が必要です。 インストール手順については、[.NET Core のインストール](https://www.microsoft.com/net/core#linuxubuntu)に関するページをご覧ください。

たとえば、Ubuntu 16.10 への .NET Core のインストールから開始します。 最新のインストール ガイドについては、[.NET Core on Linux](https://www.microsoft.com/net/core#linuxubuntu) のインストール ページを参照してください。


```bash
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ yakkety main" > /etc/apt/sources.list.d/dotnetdev.list' 
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
sudo apt-get update
sudo apt-get install dotnet-sdk-2.0.0
```

.NET Core をインストールしたら、AzCopy をダウンロードしてインストールします。

```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopyprlinux
tar -xf azcopy.tar.gz
sudo ./install.sh
```

抽出したファイルは、AzCopy on Linux のインストール後、削除してかまいません。 スーパーユーザーの特権がない場合は、抽出したフォルダー内のシェル スクリプト azcopy を使用して `azcopy` を実行することもできます。

## <a name="known-issues-and-best-practices"></a>既知の問題とベスト プラクティス
### <a name="error-installing-azcopy"></a>AzCopy のインストール中のエラー
AzCopy のインストールで問題が発生した場合は、抽出した `azcopy` フォルダーにある bash スクリプトを使って AzCopy を実行してみてください。

```bash
cd azcopy
./azcopy
```

### <a name="limit-concurrent-writes-while-copying-data"></a>データのコピー中の同時書き込みの制限
AzCopy を使用して BLOB またはファイルをコピーする場合は、コピー中に別のアプリケーションがそのデータを変更している可能性があることに注意してください。 可能な場合は、コピーしているデータがコピー操作中に変更されないようにしてください。 たとえば、Azure 仮想マシンに関連付けられている VHD をコピーしている場合は、その VHD に別のアプリケーションが書き込まないようにします。 これを行う適切な方法は、コピーされるリソースをリースすることです。 または、最初に VHD のスナップショットを作成してからそのスナップショットをコピーします。

コピーしている BLOB またはファイルに対する他のアプリケーションによる書き込みを回避できない場合は、ジョブが終了した時点で、コピー対象のリソースがソース リソースとの間に完全なパリティを保持していない可能性があることを覚えておいてください。

### <a name="running-multiple-azcopy-processes"></a>複数の AzCopy プロセスの実行
異なるジャーナル フォルダーを使用している場合は、単一のクライアント上で複数の AzCopy プロセスを実行できます。 複数の AzCopy プロセスに対して単一のジャーナル フォルダーを使用することはサポートされていません。

最初のプロセス:
```azcopy
azcopy \
    --source /mnt/myfiles1 \
    --destination https://myaccount.blob.core.windows.net/mycontainer/myfiles1 \
    --dest-key <key> \
    --resume "/mnt/myazcopyjournal1"
```

2 番目のプロセス:
```azcopy
azcopy \
    --source /mnt/myfiles2 \
    --destination https://myaccount.blob.core.windows.net/mycontainer/myfiles2 \
    --dest-key <key> \
    --resume "/mnt/myazcopyjournal2"
```

## <a name="next-steps"></a>次の手順
Azure Storage および AzCopy の詳細については、以下のリソースを参照してください。

### <a name="azure-storage-documentation"></a>Azure Storage のドキュメント:
* [Azure ストレージの概要](../storage-introduction.md)
* [ストレージ アカウントの作成](../storage-create-storage-account.md)
* [ストレージ エクスプローラーを使用した BLOB の管理](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs)
* [Azure Storage での Azure CLI の使用](../storage-azure-cli.md)
* [C++ から BLOB ストレージを使用する方法](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Java から BLOB ストレージを使用する方法](../blobs/storage-java-how-to-use-blob-storage.md)
* [Node.js から BLOB ストレージを使用する方法](../blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Python から BLOB ストレージを使用する方法](../blobs/storage-python-how-to-use-blob-storage.md)

### <a name="azure-storage-blog-posts"></a>Azure Storage に関するブログの投稿:
* [Announcing AzCopy on Linux Preview (AzCopy on Linux プレビューの発表)](https://azure.microsoft.com/blog/announcing-azcopy-on-linux-preview/)
* [Azure Storage Data Movement Library プレビューの概要](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
* [AzCopy:Introducing synchronous copy and customized content type (AzCopy: 同期コピーとカスタマイズしたコンテンツの種類の概要)](https://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
* [AzCopy:Announcing General Availability of AzCopy 3.0 plus preview release of AzCopy 4.0 with Table and File support (AzCopy: AzCopy 3.0 の一般公開とテーブルおよびファイルのサポートを伴う AzCopy 4.0 のプレビュー リリースのお知らせ)](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
* [AzCopy:Optimized for Large-Scale Copy Scenarios (AzCopy: 大量のコピーのシナリオ用の最適化)](https://go.microsoft.com/fwlink/?LinkId=507682)
* [AzCopy:Support for read-access geo-redundant storage (AzCopy: geo 冗長ストレージの読み取りアクセスのサポート)](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
* [AzCopy:Transfer data with restartable mode and SAS token (AzCopy: 再起動可能モードまたは SAS トークンを使用したデータの転送)](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
* [AzCopy:Using cross-account Copy Blob (AzCopy: アカウント間での BLOB のコピー)](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
* [AzCopy:Uploading/downloading files for Azure Blobs (AzCopy: Azure BLOB に対するファイルのアップロードおよびダウンロード)](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)
