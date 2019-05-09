---
title: Azure Import/Export のインポート ジョブ用のハード ドライブを準備するためのサンプル ワークフロー | Microsoft Docs
description: Azure Import/Export サービスでインポート ジョブ用のドライブを準備するプロセスの手順について説明します。
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 04/07/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 42da285fbb55df43959506996bcde9cf547c2a22
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57439267"
---
# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>インポート ジョブ用のハード ドライブを準備するためのサンプル ワークフロー

この記事では、インポート ジョブ用のドライブを準備するプロセスの手順について説明します。

## <a name="sample-data"></a>サンプル データ

この例では、`mystorageaccount` という名前の Azure Storage アカウントに次のデータをインポートします。

|Location|説明|データ サイズ|
|--------------|-----------------|-----|
|H:\Video\ |一連のビデオ|12 TB|
|H:\Photo\ |一連の写真|30 GB|
|K:\Temp\FavoriteMovie.ISO|Blu-Ray™ ディスク イメージ|25 GB|
|\\\bigshare\john\music\ |ネットワーク共有上の一連の音楽ファイル|10 GB|

## <a name="storage-account-destinations"></a>ストレージ アカウント内のインポート先

インポート ジョブでは、ストレージ アカウント内の次のインポート先にデータをインポートします。

|ソース|インポート先の仮想ディレクトリまたは BLOB|
|------------|-------------------------------------------|
|H:\Video\ |video/|
|H:\Photo\ |photo/|
|K:\Temp\FavoriteMovie.ISO|favorite/FavoriteMovies.ISO|
|\\\bigshare\john\music\ |music|

このマッピングにより、`H:\Video\Drama\GreatMovie.mov` ファイルが `https://mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov` BLOB にインポートされます。

## <a name="determine-hard-drive-requirements"></a>ハード ドライブの要件を決定する

次に、必要なハード ドライブの数を決定するために、データのサイズを計算します。

`12TB + 30GB + 25GB + 10GB = 12TB + 65GB`

この例では、8 TB のハード ドライブが 2 つあれば十分です。 ただし、ソース ディレクトリ `H:\Video` には 12 TB のデータがありますが、1 つのハード ドライブの容量は 8 TB しかないため、**driveset.csv** ファイルに次のように指定します。

```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
X,Format,SilentMode,Encrypt,
Y,Format,SilentMode,Encrypt,
```
ツールにより、最適化された方法で 2 つのハード ドライブにデータが分配されます。

## <a name="attach-drives-and-configure-the-job"></a>ドライブを接続してジョブを構成する
両方のディスクをコンピューターに接続し、ボリュームを作成します。 次に、**dataset.csv** ファイルを作成します。
```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
H:\Video\,video/,BlockBlob,rename,None,H:\mydirectory\properties.xml
H:\Photo\,photo/,BlockBlob,rename,None,H:\mydirectory\properties.xml
K:\Temp\FavoriteVideo.ISO,favorite/FavoriteVideo.ISO,BlockBlob,rename,None,H:\mydirectory\properties.xml
\\myshare\john\music\,music/,BlockBlob,rename,None,H:\mydirectory\properties.xml
```

さらに、すべてのファイルの次のメタデータを設定できます。

* **UploadMethod:** Microsoft Azure Import/Export サービス
* **DataSetName:** SampleData
* **CreationDate:** 10/1/2013

インポートするファイルのメタデータを設定するには、次の内容が含まれたテキスト ファイル `c:\WAImportExport\SampleMetadata.txt` を作成します。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Metadata>
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>
    <DataSetName>SampleData</DataSetName>
    <CreationDate>10/1/2013</CreationDate>
</Metadata>
```

`FavoriteMovie.ISO` BLOB の一部のプロパティを設定することもできます。

* **Content-Type:** application/octet-stream
* **Content-MD5:** Q2hlY2sgSW50ZWdyaXR5IQ==
* **Cache-Control:** no-cache

これらのプロパティを設定するには、テキスト ファイル `c:\WAImportExport\SampleProperties.txt` を作成します。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Properties>
    <Content-Type>application/octet-stream</Content-Type>
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>
    <Cache-Control>no-cache</Cache-Control>
</Properties>
```

## <a name="run-the-azure-importexport-tool-waimportexportexe"></a>Azure Import/Export ツール (WAImportExport.exe) を実行する

これで、Azure Import/Export ツールを実行して 2 つのハード ドライブを準備できます。

**最初のセッション:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

データをさらに追加する必要がある場合は、別のデータセット ファイル (初期データセットと同じ形式) を作成します。

**2 番目のセッション:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

コピー セッションが完了したら、コピー用コンピューターから 2 つのドライブを切断し、適切な Azure データ センターに送付できます。 Azure Portal でインポート ジョブを作成するときに、`<FirstDriveSerialNumber>.xml` と `<SecondDriveSerialNumber>.xml` の 2 つのジャーナル ファイルをアップロードします。

## <a name="next-steps"></a>次の手順

* [インポート ジョブ用のハード ドライブを準備する](../storage-import-export-tool-preparing-hard-drives-import.md)
* [頻繁に使用するコマンドのクイック リファレンス](../storage-import-export-tool-quick-reference.md)
