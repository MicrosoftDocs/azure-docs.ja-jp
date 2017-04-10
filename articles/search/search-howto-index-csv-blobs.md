---
title: "Azure Search BLOB インデクサーを使用した CSV BLOB のインデックス作成 | Microsoft Docs"
description: "Azure Search を使用して CSV BLOB のインデックスを作成する方法について説明します。"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: ed3c9cff-1946-4af2-a05a-5e0b3d61eb25
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 12/15/2016
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 82c9d68bc3f1650648fac0597f4a329ef053b109


---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>Azure Search BLOB インデクサーを使用した CSV BLOB のインデックス作成
既定では、 [Azure Search BLOB インデクサー](search-howto-indexing-azure-blob-storage.md) は区切りテキスト BLOB を 1 つのテキスト チャンクとして解析します。 ただし、CSV データを含む BLOB では、BLOB の各行を個別のドキュメントとして処理することがよくあります。 たとえば、次のような区切りテキストがあるとします。 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

この場合、区切りテキストを解析して、"id"、"datePublished"、"tags" の各フィールドがそれぞれ含まれた 2 つのドキュメントにすることができます。

この記事では、Azure Search BLOB インデクサーを使用して CSV BLOB を解析する方法について説明します。 

> [!IMPORTANT]
> 現在この機能はプレビュー版です。 バージョン **2015-02-28-Preview** を使用した REST API でのみ利用できます。 プレビュー版の API は、テストと評価を目的としたものです。運用環境での使用は避けてください。 
> 
> 

## <a name="setting-up-csv-indexing"></a>CSV インデックス作成の設定
CSV BLOB のインデックスを作成するには、 `delimitedText` 解析モードを使用してインデクサーの定義を作成または更新します。  

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

インデクサー作成 API の詳細については、「 [インデクサーの作成](search-api-indexers-2015-02-28-preview.md#create-indexer)」をご覧ください。

`firstLineContainsHeaders` は、各 BLOB の最初の (空白以外の) 行にヘッダーが含まれていることを示します。
BLOB に最初のヘッダー行が含まれていない場合は、インデクサーの構成でヘッダーを指定する必要があります。 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

現在サポートされているのは、UTF-8 エンコードだけです。 また、区切り文字としてコンマ文字 ( `','` ) だけがサポートされています。 他のエンコードまたは区切り文字のサポートが必要な場合は、 [UserVoice サイト](https://feedback.azure.com/forums/263029-azure-search)でお知らせください。

> [!IMPORTANT]
> 区切りテキスト解析モードを使用すると、Azure Search ではデータ ソース内のすべての BLOB が CSV になると見なされます。 CSV と CSV 以外の BLOB が混在するデータ ソースをサポートする必要がある場合は、 [UserVoice サイト](https://feedback.azure.com/forums/263029-azure-search)でお知らせください。
> 
> 

## <a name="request-examples"></a>要求例
すべてをまとめた完全なペイロードの例を以下に示します。 

データソース: 

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

インデクサー:

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-search-better"></a>Azure Search の品質向上にご協力ください
ご希望の機能や品質向上のアイデアがありましたら、 [UserVoice サイト](https://feedback.azure.com/forums/263029-azure-search/)にぜひお寄せください。




<!--HONumber=Nov16_HO3-->


