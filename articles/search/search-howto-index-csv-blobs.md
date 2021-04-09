---
title: CSV BLOB を検索する
titleSuffix: Azure Cognitive Search
description: delimitedText 解析モードを使用して Azure BLOB ストレージから CSV を抽出し、インポートします。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: d9633031ca8358ab0498c2e806b22e6c4ddd3eab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99430481"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Azure Cognitive Search で delimitedText 解析モードと BLOB インデクサーを使用して CSV BLOB のインデックスを作成する方法

Azure Cognitive Search の [BLOB インデクサー](search-howto-indexing-azure-blob-storage.md)には、CSV の各行を個別の検索ドキュメントとして扱う CSV ファイルの `delimitedText` 解析モードが用意されています。 たとえば、次のコンマ区切りのテキストを指定すると、`delimitedText` の結果、検索インデックスに 2 つのドキュメントが生成されます。 

```text
id, datePublished, tags
1, 2016-01-12, "azure-search,azure,cloud"
2, 2016-07-07, "cloud,mobile"
```

`delimitedText` 解析モードがないと、CSV ファイルの内容全体が 1 つの検索ドキュメントとして扱われます。

1 つの BLOB から複数の検索ドキュメントを作成する場合は、必ず「[BLOB のインデックス作成して複数の検索ドキュメントを生成する](search-howto-index-one-to-many-blobs.md)」を参照し、ドキュメント キーの割り当てがどのように機能するかを理解してください。 BLOB インデクサーには、新しい各ドキュメントを一意に定義する値を検索または生成する機能があります。 具体的には、BLOB がより小さい部分に解析されるときに生成される一時的な `AzureSearch_DocumentKey` を作成できます。この値は、インデックス内の検索ドキュメントのキーとして使用されます。

## <a name="setting-up-csv-indexing"></a>CSV インデックス作成の設定

CSV BLOB のインデックスを作成するには、[インデクサーの作成](/rest/api/searchservice/create-indexer)要求で `delimitedText` 解析モードを使用してインデクサーの定義を作成または更新します。

```http
{
  "name" : "my-csv-indexer",
  ... other indexer properties
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
}
```

`firstLineContainsHeaders` は、各 BLOB の最初の (空白以外の) 行にヘッダーが含まれていることを示します。
BLOB に最初のヘッダー行が含まれていない場合は、インデクサーの構成でヘッダーを指定する必要があります。 

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 
```

`delimitedTextDelimiter` 構成設定を使用して区切り記号をカスタマイズできます。 次に例を示します。

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }
```

> [!NOTE]
> 現在サポートされているのは、UTF-8 エンコードだけです。 他のエンコードのサポートが必要な場合は、[UserVoice](https://feedback.azure.com/forums/263029-azure-search) で投票してください。

> [!IMPORTANT]
> 区切りテキスト解析モードを使用すると、Azure Cognitive Search ではデータ ソース内のすべての BLOB が CSV になると見なされます。 CSV と CSV 以外の BLOB が混在するデータ ソースをサポートする必要がある場合は、[UserVoice](https://feedback.azure.com/forums/263029-azure-search) で投票してください。
>

## <a name="request-examples"></a>要求例

すべてをまとめた完全なペイロードの例を以下に示します。 

データソース: 

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
    "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
}   
```

インデクサー:

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "name" : "my-csv-indexer",
  "dataSourceName" : "my-blob-datasource",
  "targetIndexName" : "my-target-index",
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
}
```


