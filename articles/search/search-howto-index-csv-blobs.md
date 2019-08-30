---
title: Azure Search BLOB インデクサーを使用して CSV BLOB のインデックスを作成する - Azure Search
description: Azure Search インデックスを使用して、Azure Blob Storage 内の BLOB CSV をフルテキスト検索用にクロールします。 インデクサーにより、選択したデータ ソース (Azure Blob Storage など) のデータ インジェストが自動化されます。
ms.date: 05/02/2019
author: mgottein
manager: nitinme
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: b135fd1a0758567a7b504996bf442a913741fe59
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656752"
---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>Azure Search BLOB インデクサーを使用した CSV BLOB のインデックス作成

> [!Note]
> delimitedText 解析モードはプレビュー段階にあり、運用環境での使用は意図していません。 [REST API バージョン 2019-05-06-Preview](search-api-preview.md) でこの機能を提供します。 現時点で .NET SDK のサポートはありません。
>

既定では、 [Azure Search BLOB インデクサー](search-howto-indexing-azure-blob-storage.md) は区切りテキスト BLOB を 1 つのテキスト チャンクとして解析します。 ただし、CSV データを含む BLOB では、BLOB の各行を個別のドキュメントとして処理することがよくあります。 たとえば、次のような区切りテキストを解析し、それぞれが "id"、"datePublished"、"tags" フィールドを含む 2 つのドキュメントに格納するような場合です。 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

この記事では、`delimitedText` 解析モードを設定し、Azure Search BLOB インデクサーを使用して CSV BLOB を解析する方法について説明します。 

> [!NOTE]
> 1 つの Azure BLOB から複数の検索ドキュメントを出力するには、[一対多のインデックス作成](search-howto-index-one-to-many-blobs.md)に関するページにあるインデクサー構成の推奨事項に従ってください。

## <a name="setting-up-csv-indexing"></a>CSV インデックス作成の設定
CSV BLOB のインデックスを作成するには、[インデクサーの作成](https://docs.microsoft.com/rest/api/searchservice/create-indexer)要求で `delimitedText` 解析モードを使用してインデクサーの定義を作成または更新します。

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

`firstLineContainsHeaders` は、各 BLOB の最初の (空白以外の) 行にヘッダーが含まれていることを示します。
BLOB に最初のヘッダー行が含まれていない場合は、インデクサーの構成でヘッダーを指定する必要があります。 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

`delimitedTextDelimiter` 構成設定を使用して区切り記号をカスタマイズできます。 例:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> 現在サポートされているのは、UTF-8 エンコードだけです。 他のエンコードのサポートが必要な場合は、[UserVoice](https://feedback.azure.com/forums/263029-azure-search) で投票してください。

> [!IMPORTANT]
> 区切りテキスト解析モードを使用すると、Azure Search ではデータ ソース内のすべての BLOB が CSV になると見なされます。 CSV と CSV 以外の BLOB が混在するデータ ソースをサポートする必要がある場合は、[UserVoice](https://feedback.azure.com/forums/263029-azure-search) で投票してください。
> 
> 

## <a name="request-examples"></a>要求例
すべてをまとめた完全なペイロードの例を以下に示します。 

データソース: 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

インデクサー:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-search-better"></a>Azure Search の品質向上にご協力ください
ご希望の機能や品質向上のアイデアがありましたら、[UserVoice](https://feedback.azure.com/forums/263029-azure-search/) までお寄せください。

