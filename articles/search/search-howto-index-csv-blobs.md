---
title: CSV BLOB を検索する
titleSuffix: Azure Cognitive Search
description: delimitedText 解析モードを使用して Azure BLOB ストレージから CSV を抽出し、インポートします。
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.openlocfilehash: 14846761535a77f28adbd0147d244817cb799d86
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935842"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Azure Cognitive Search で delimitedText 解析モードと BLOB インデクサーを使用して CSV BLOB のインデックスを作成する方法

既定では、[Azure Cognitive Search BLOB インデクサー](search-howto-indexing-azure-blob-storage.md) は区切りテキスト BLOB を 1 つのテキスト チャンクとして解析します。 ただし、CSV データを含む BLOB では、BLOB の各行を個別のドキュメントとして処理することがよくあります。 たとえば、次のような区切りテキストを解析し、それぞれが "id"、"datePublished"、"tags" フィールドを含む 2 つのドキュメントに格納するような場合です。 

```text
id, datePublished, tags
1, 2016-01-12, "azure-search,azure,cloud"
2, 2016-07-07, "cloud,mobile"
```

この記事では、`delimitedText` 解析モードを設定し、Azure Cognitive Search BLOB インデクサーを使用して CSV BLOB を解析する方法について説明します。 

> [!NOTE]
> 1 つの Azure BLOB から複数の検索ドキュメントを出力するには、[一対多のインデックス作成](search-howto-index-one-to-many-blobs.md)に関するページにあるインデクサー構成の推奨事項に従ってください。

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

## <a name="help-us-make-azure-cognitive-search-better"></a>Azure Cognitive Search の品質向上にご協力ください
ご希望の機能や品質向上のアイデアがありましたら、[UserVoice](https://feedback.azure.com/forums/263029-azure-search/) までお寄せください。
