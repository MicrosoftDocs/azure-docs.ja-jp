---
title: Azure Import/Export を使ったプロパティとメタデータの設定 | Microsoft Docs
description: Azure Import/Export ツールを実行してドライブを準備する際に、目的の BLOB で設定するプロパティとメタデータを指定する方法について説明します。
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 3e2796d943fbc4a4ce99576de74b2fc5c77bcca9
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55459453"
---
# <a name="setting-properties-and-metadata-during-the-import-process"></a>インポート処理中にプロパティとメタデータを設定する

Microsoft Azure Import/Export ツールを実行してドライブを準備する際に、目的の BLOB で設定するプロパティとメタデータを指定できます。 次の手順に従います。

1.  BLOB のプロパティを設定するには、プロパティの名前と値を指定するテキスト ファイルをローカル コンピューターで作成します。
2.  BLOB のメタデータを設定するには、メタデータの名前と値を指定するテキスト ファイルをローカル コンピューターで作成します。
3.  `PrepImport` 操作の一部として、これらのファイルの一方または両方の完全なパスを Azure Import/Export ツールに渡します。

> [!NOTE]
>  コピー セッションの一部としてプロパティまたはメタデータ ファイルを指定すると、それらのプロパティやメタデータは、そのコピー セッションの一部としてインポートされるすべての BLOB に対して設定されます。 インポート中の一部の BLOB のプロパティまたはメタデータの別のセットを指定する場合は、異なるプロパティまたはメタデータ ファイルを使用して別のコピー セッションを作成する必要があります。

## <a name="specify-blob-properties-in-a-text-file"></a>テキスト ファイルに BLOB のプロパティを指定する

BLOB のプロパティを指定するには、ローカル テキスト ファイルを作成し、要素としてプロパティ名を指定し、値としてプロパティ値を指定する XML を含めます。 一部のプロパティ値を指定する例を次に示します。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Properties>
    <Content-Type>application/octet-stream</Content-Type>
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>
    <Cache-Control>no-cache</Cache-Control>
</Properties>
```

ファイルをローカルの場所 (`C:\WAImportExport\ImportProperties.txt` など) に保存します。

## <a name="specify-blob-metadata-in-a-text-file"></a>テキスト ファイルに BLOB のメタデータを指定する

同様に、BLOB のメタデータを指定するには、要素としてメタデータ名を指定し、値としてメタデータ値を指定するローカル テキスト ファイルを作成します。 一部のメタデータ値を指定する例を次に示します。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Metadata>
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>
    <DataSetName>SampleData</DataSetName>
    <CreationDate>10/1/2013</CreationDate>
</Metadata>
```

ファイルをローカルの場所 (`C:\WAImportExport\ImportMetadata.txt` など) に保存します。

## <a name="add-the-path-to-properties-and-metadata-files-in-datasetcsv"></a>dataset.csv にプロパティとメタデータ ファイルのパスを追加する

```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
H:\Video\,https://mystorageaccount.blob.core.windows.net/video/,BlockBlob,rename,None,H:\mydirectory\properties.xml
H:\Photo\,https://mystorageaccount.blob.core.windows.net/photo/,BlockBlob,rename,None,H:\mydirectory\properties.xml
K:\Temp\FavoriteVideo.ISO,https://mystorageaccount.blob.core.windows.net/favorite/FavoriteVideo.ISO,BlockBlob,rename,None,H:\mydirectory\properties.xml
\\myshare\john\music\,https://mystorageaccount.blob.core.windows.net/music/,BlockBlob,rename,None,H:\mydirectory\properties.xml
```

## <a name="next-steps"></a>次の手順

* [Import/Export サービスのメタデータとプロパティ ファイルの形式](../storage-import-export-file-format-metadata-and-properties.md)
