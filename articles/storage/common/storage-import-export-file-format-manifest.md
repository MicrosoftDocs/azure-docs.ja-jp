---
title: Azure Import/Export のマニフェスト ファイルの形式 | Microsoft Docs
description: Azure Blob Storage の BLOB とインポート/エクスポート サービスのインポートまたはエクスポート ジョブにおけるドライブ上のファイル間のマッピングを表すドライブ マニフェスト ファイルの形式について説明します。
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: ee53cc3a639a79e1b29ac6cd537bfb04e05b1bca
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2019
ms.locfileid: "55692478"
---
# <a name="azure-importexport-service-manifest-file-format"></a>Azure Import/Export サービスのマニフェスト ファイルの形式
ドライブ マニフェスト ファイルは、Azure Blob Storage の BLOB とインポートまたはエクスポート ジョブを構成するドライブ上のファイル間のマッピングを表します。 インポート操作の場合、マニフェスト ファイルはドライブの準備プロセスの一部として作成され、ドライブが Azure データ センターに送信される前にそのドライブに格納されます。 エクスポート操作の場合、マニフェストは Azure Import/Export サービスによって作成され、ドライブに保存されます。  
  
ドライブ マニフェスト ファイルはインポート ジョブとエクスポート ジョブの両方で、インポートまたはエクスポート ドライブに格納されます。API 操作を介してサービスに送信されることはありません。  
  
ドライブ マニフェスト ファイルの一般的な形式を次に示します。  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveManifest Version="2014-11-01">  
  <Drive>  
    <DriveId>drive-id</DriveId>  
    import-export-credential  
  
    <!-- First Blob List -->  
    <BlobList>  
      <!-- Global properties and metadata that applies to all blobs -->  
      [<MetadataPath Hash="md5-hash">global-metadata-file-path</MetadataPath>]  
      [<PropertiesPath   
        Hash="md5-hash">global-properties-file-path</PropertiesPath>]  
  
      <!-- First Blob -->  
      <Blob>  
        <BlobPath>blob-path-relative-to-account</BlobPath>  
        <FilePath>file-path-relative-to-transfer-disk</FilePath>  
        [<ClientData>client-data</ClientData>]  
        [<Snapshot>snapshot</Snapshot>]  
        <Length>content-length</Length>  
        [<ImportDisposition>import-disposition</ImportDisposition>]  
        page-range-list-or-block-list          
        [<MetadataPath Hash="md5-hash">metadata-file-path</MetadataPath>]  
        [<PropertiesPath Hash="md5-hash">properties-file-path</PropertiesPath>]  
      </Blob>  
  
      <!-- Second Blob -->  
      <Blob>  
      . . .  
      </Blob>  
    </BlobList>  
  
    <!-- Second Blob List -->  
    <BlobList>  
    . . .  
    </BlobList>  
  </Drive>  
</DriveManifest>  
  
import-export-credential ::=   
  <StorageAccountKey>storage-account-key</StorageAccountKey> | <ContainerSas>container-sas</ContainerSas>  
  
page-range-list-or-block-list ::=   
  page-range-list | block-list  
  
page-range-list ::=   
    <PageRangeList>  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       Hash="md5-hash"/>]  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       Hash="md5-hash"/>]  
    </PageRangeList>  
  
block-list ::=  
    <BlockList>  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]  
       Hash="md5-hash"/>]  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]   
       Hash="md5-hash"/>]  
    </BlockList>  

```

## <a name="manifest-xml-elements-and-attributes"></a>マニフェストの XML 要素と属性

データ要素とドライブのマニフェスト XML 形式の属性は、次の表で指定されます。  
  
|XML 要素|type|説明|  
|-----------------|----------|-----------------|  
|`DriveManifest`|ルート要素|マニフェスト ファイルのルート要素です。 ファイル内の他のすべての要素は、この要素の下に表示されます。|  
|`Version`|属性、String|マニフェスト ファイルのバージョンです。|  
|`Drive`|入れ子になった XML 要素|各ドライブのマニフェストが含まれています。|  
|`DriveId`|String|ドライブの一意のドライブ識別子です。 ドライブ識別子は、そのドライブのシリアル番号を照会して確認します。 ドライブのシリアル番号は通常、ドライブの外側にも印刷されています。 `DriveID` 要素は、マニフェスト ファイルで `BlobList` 要素の前に表示される必要があります。|  
|`StorageAccountKey`|String|`ContainerSas` が指定されていない場合に限り、インポート ジョブで必須となります。 ジョブに関連した Azure Storage アカウントのアカウント キーです。<br /><br /> この要素は、エクスポート操作ではマニフェストから省略されます。|  
|`ContainerSas`|String|`StorageAccountKey` が指定されていない場合に限り、インポート ジョブで必須となります。 ジョブに関連付けられている BLOB にアクセスするためのコンテナー SAS です。 形式については「[Put Job](/rest/api/storageimportexport/jobs)」をご覧ください。この要素は、エクスポート操作ではマニフェストから省略されます。|  
|`ClientCreator`|String|XML ファイルを作成したクライアントを指定します。 この値は Import/Export サービスによって解釈されることはありません。|  
|`BlobList`|入れ子になった XML 要素|インポート ジョブまたはエクスポート ジョブの 一部である BLOB の一覧が含まれています。 BLOB 一覧内の各 BLOB は、同じメタデータとプロパティを共有します。|  
|`BlobList/MetadataPath`|String|省略可能。 インポート操作の BLOB 一覧で、BLOB に設定される既定のメタデータを含むディスク上のファイルの相対パスを指定します。 このメタデータは、必要に応じて BLOB ごとにオーバーライドできます。<br /><br /> この要素は、エクスポート操作ではマニフェストから省略されます。|  
|`BlobList/MetadataPath/@Hash`|属性、String|メタデータ ファイルの Base16 エンコード MD5 ハッシュ値を指定します。|  
|`BlobList/PropertiesPath`|String|省略可能。 インポート操作の BLOB の一覧で、BLOB に設定される既定のプロパティを含むディスク上のファイルの相対パスを指定します。 このプロパティは、必要に応じて BLOB ごとにオーバーライドできます。<br /><br /> この要素は、エクスポート操作ではマニフェストから省略されます。|  
|`BlobList/PropertiesPath/@Hash`|属性、String|プロパティ ファイルの Base16 エンコード MD5 ハッシュ値を指定します。|  
|`Blob`|入れ子になった XML 要素|各 BLOB 一覧の各 BLOB についての情報が含まれます。|  
|`Blob/BlobPath`|String|コンテナー名で始まる、BLOB への相対 URI。 BLOB がルート コンテナー内にある場合は、`$root` で始まる必要があります。|  
|`Blob/FilePath`|String|ドライブ上のファイルへの相対パスを指定します。 エクスポート ジョブの場合、可能であれば BLOB のパスはファイルのパスに使用します。*例:* `pictures/bob/wild/desert.jpg` は `\pictures\bob\wild\desert.jpg` にエクスポートされます。 ただし NTFS 名の制限により、BLOB は BLOB のパスとは異なるパスのファイルにエクスポートされる場合があります。|  
|`Blob/ClientData`|String|省略可能。 お客様からのコメントが含まれます。 この値は Import/Export サービスによって解釈されることはありません。|  
|`Blob/Snapshot`|DateTime|エクスポート ジョブの場合は省略可能です。 エクスポートされた BLOB スナップショットの識別子を指定します。|  
|`Blob/Length`|整数|BLOB の合計長さを指定します (単位: バイト)。 値は、ブロック BLOB では最大 200 GB、ページ BLOB では最大 1 TB にできます。 ページ BLOB ではこの値を 512 の倍数にする必要があります。|  
|`Blob/ImportDisposition`|String|インポート ジョブでは省略可能。エクスポート ジョブでは省略。 これは、Import/Export サービスで同じ名前の BLOB が既に存在する場合のインポート ジョブのケースをどのように処理するべきか指定します。 インポート マニフェストでこの値を省略した場合、既定値は `rename` になります。<br /><br /> この要素の値は次のとおりです。<br /><br /> -   `no-overwrite`:コピー先 BLOB が同じ名前で既に存在する場合、インポート操作ではこのファイルのインポートをスキップします。<br />-   `overwrite`:既存のコピー先 BLOB は、新しくインポートしたファイルで完全に上書きされます。<br />-   `rename`:新しい BLOB は、変更された名前でアップロードされます。<br /><br /> 名前変更の規則は次のとおりです。<br /><br /> -   BLOB 名にドットが含まれない場合、新しい名前は、`(2)` を元の BLOB 名に追加することで生成されます。この新しい名前も既存の BLOB 名と競合する場合、`(3)` の代わりに `(2)` が追加されるというように続きます。<br />-   BLOB 名にドットが含まれる場合、最後のドットより後の部分は、拡張子名と見なされます。 上記の手順と同様に、`(2)` は最後のドットの前に挿入され、新しい名前を生成します。それでも新しい名前が既存の BLOB 名と競合する場合は、`(3)`、`(4)`、など競合しない名前が見つかるまで続きます。<br /><br /> 次に例をいくつか示します。<br /><br /> BLOB `BlobNameWithoutDot` は次のように名前が変更します。<br /><br /> `BlobNameWithoutDot (2)  // if BlobNameWithoutDot exists`<br /><br /> `BlobNameWithoutDot (3)  // if both BlobNameWithoutDot and BlobNameWithoutDot (2) exist`<br /><br /> BLOB `Seattle.jpg` は次のように名前が変更します。<br /><br /> `Seattle (2).jpg  // if Seattle.jpg exists`<br /><br /> `Seattle (3).jpg  // if both Seattle.jpg and Seattle (2).jpg exist`|  
|`PageRangeList`|入れ子になった XML 要素|ページ BLOB では必須です。<br /><br /> インポート操作の場合は、インポートするファイルのバイト範囲一覧を指定します。 各ページ範囲は、ページ範囲とリージョンの MD5 ハッシュを表すソース ファイルのオフセットと長さによって表されます。 ページ範囲の `Hash` 属性は必須です。 サービスでは、BLOB 内のデータのハッシュが、ページ範囲から計算された MD5 ハッシュと一致することを確認します。 ページ範囲を使用して合計サイズ最大 1 TB のインポート用ファイルを表すことができます。 すべてのページ範囲はオフセット別に順序を指定する必要があり、重複は許可されません。<br /><br /> エクスポート操作の場合は、ドライブにエクスポートされた BLOB の一連のバイト範囲を指定します。<br /><br /> ページ範囲とともに BLOB またはファイルのサブ範囲のみ対応する場合があります。  任意のページ範囲で対応できないファイルの残りの部分が推測され、その内容は定義されない場合があります。|  
|`PageRange`|XML 要素|ページ範囲を表します。|  
|`PageRange/@Offset`|属性、整数|転送ファイルと BLOB で指定したページ範囲が開始するオフセットを指定します。 この値は 512 の倍数にする必要があります。|  
|`PageRange/@Length`|属性、整数|ページ範囲の長さを指定します。 この値は 512 の倍数で、4 MB 以下にする必要があります。|  
|`PageRange/@Hash`|属性、String|ページ範囲の Base16 エンコード MD5 ハッシュ値を指定します。|  
|`BlockList`|入れ子になった XML 要素|名前付きブロックとブロック BLOB では必須です。<br /><br /> インポート操作の場合、ブロック一覧は、Azure Storage にインポートされる一連のブロックを指定します。 エクスポート操作の場合、ブロック一覧はエクスポートされたファイルで各ブロックが格納されている場所を指定します。 各ブロックは、ファイルとブロック長のオフセットで表します。各ブロックはさらにブロック ID 属性別に名前が付けられ、ブロックの MD5 ハッシュが含まれます。 最大 50,000 個のブロックを使用して BLOB を表すことができます。  すべてのブロックはオフセット別に順序を指定し、ファイルの完全範囲に対応する必要があります。*つまり*、ブロック間に空白がないようにする必要があります。 BLOB が 64 MB 以下である場合は、各ブロックのブロック ID はすべて存在しないか、すべて存在するかのいずれかにする必要があります。 ブロック ID は、Base64 でエンコードされた文字列にする必要があります。 ブロック ID の要件についての詳細は、「[Put Block](/rest/api/storageservices/put-block)」をご覧ください。|  
|`Block`|XML 要素|ブロックを表します。|  
|`Block/@Offset`|属性、整数|指定されたブロックの開始位置のオフセットを指定します。|  
|`Block/@Length`|属性、整数|ブロックのバイト数を指定します。この値は 4 MB 以下である必要があります。|  
|`Block/@Id`|属性、String|ブロックのブロック ID を表す文字列を指定します。|  
|`Block/@Hash`|属性、String|ブロックの Base16 エンコード MD5 ハッシュを指定します。|  
|`Blob/MetadataPath`|String|省略可能。 メタデータ ファイルの相対パスを指定します。 インポート中に、コピー先 BLOB のメタデータが設定されます。 エクスポート操作中、BLOB のメタデータは、ドライブ上のメタデータ ファイルに格納されます。|  
|`Blob/MetadataPath/@Hash`|属性、String|BLOB のメタデータ ファイルの Base16 エンコード MD5 ハッシュを指定します。|  
|`Blob/PropertiesPath`|String|省略可能。 プロパティ ファイルの相対パスを指定します。 インポート中に、コピー先 BLOB のプロパティが設定されます。 エクスポート操作中、BLOB のプロパティは、ドライブ上のプロパティ ファイルに格納されます。|  
|`Blob/PropertiesPath/@Hash`|属性、String|BLOB のプロパティ ファイルの Base16 エンコード MD5 ハッシュを指定します。|  
  
## <a name="next-steps"></a>次の手順
 
* [ストレージの Import/Export REST API](/rest/api/storageimportexport/)
