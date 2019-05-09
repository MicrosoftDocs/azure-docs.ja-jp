---
title: Azure Import/Export のメタデータとプロパティ ファイルの形式 | Microsoft Docs
description: インポートまたはエクスポート ジョブの一部である 1 つまたは複数の BLOB のメタデータとプロパティを指定する方法について説明します。
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 2066d4a2ed6db97285d92d15e14dbd21629dbdfa
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55457005"
---
# <a name="azure-importexport-service-metadata-and-properties-file-format"></a>Azure Import/Export サービスのメタデータとプロパティ ファイルの形式
インポートまたはエクスポート ジョブの一部として 1 つ以上の BLOB のメタデータとプロパティを指定できます。 インポート ジョブの一部として作成されている BLOB のメタデータまたはプロパティを設定するには、インポートするデータを含むハード ドライブでメタデータまたはプロパティ ファイルを提供します。 エクスポート ジョブの場合、メタデータとプロパティは返されるハード ドライブに含まれるメタデータまたはプロパティ ファイルに書き込まれます。  
  
## <a name="metadata-file-format"></a>メタデータ ファイルの形式  
メタデータ ファイルの形式は、次のとおりです。  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
[<metadata-name-1>metadata-value-1</metadata-name-1>]  
[<metadata-name-2>metadata-value-2</metadata-name-2>]  
. . .  
</Metadata>  
```
  
|XML 要素|type|説明|  
|-----------------|----------|-----------------|  
|`Metadata`|ルート要素|メタデータ ファイルのルート要素です。|  
|`metadata-name`|String|省略可能。 XML 要素は、BLOB のメタデータの名前を指定し、その値はメタデータの設定の値を指定します。|  
  
## <a name="properties-file-format"></a>プロパティ ファイルの形式  
メタデータ ファイルの形式は、次のとおりです。  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
[<Last-Modified>date-time-value</Last-Modified>]  
[<Etag>etag</Etag>]  
[<Content-Length>size-in-bytes<Content-Length>]  
[<Content-Type>content-type</Content-Type>]  
[<Content-MD5>content-md5</Content-MD5>]  
[<Content-Encoding>content-encoding</Content-Encoding>]  
[<Content-Language>content-language</Content-Language>]  
[<Cache-Control>cache-control</Cache-Control>]  
</Properties>  
```
  
|XML 要素|type|説明|  
|-----------------|----------|-----------------|  
|`Properties`|ルート要素|プロパティ ファイルのルート要素。|  
|`Last-Modified`|String|省略可能。 BLOB の最終更新時刻。 エクスポート ジョブの場合のみ。|  
|`Etag`|String|省略可能。 BLOB の ETag 値。 エクスポート ジョブの場合のみ。|  
|`Content-Length`|String|省略可能。 BLOB のサイズ (単位: バイト)。 エクスポート ジョブの場合のみ。|  
|`Content-Type`|String|省略可能。 BLOB のコンテンツの種類。|  
|`Content-MD5`|String|省略可能。 BLOB の MD5 ハッシュ。|  
|`Content-Encoding`|String|省略可能。 BLOB コンテンツのエンコーディング。|  
|`Content-Language`|String|省略可能。 BLOB コンテンツの言語。|  
|`Cache-Control`|String|省略可能。 BLOB のキャッシュ制御文字列。|  

## <a name="next-steps"></a>次の手順

BLOB のメタデータとプロパティの設定の詳細な規則については、「[Set Blob Properties](/rest/api/storageservices/set-blob-properties)」 (BLOB プロパティの設定)、「[Set Blob Metadata](/rest/api/storageservices/set-blob-metadata)」 (BLOB メタデータの設定)、「[Setting and Retrieving Properties and Metadata for Blob Resources](/rest/api/storageservices/setting-and-retrieving-properties-and-metadata-for-blob-resources)」 (BLOB リソースのプロパティとメタデータの設定と取得) をご覧ください。
