---
title: "Azure Search BLOB インデクサーを使用した JSON BLOB のインデックス作成"
description: "Azure Search BLOB インデクサーを使用した JSON BLOB のインデックス作成"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 57e32e51-9286-46da-9d59-31884650ba99
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 12/15/2016
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: fc2f30569acc49dd383ba230271989eca8a14423
ms.openlocfilehash: de7af5419aa423734ad06b236e0edf61fbb0cad1

---

# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>Azure Search BLOB インデクサーを使用した JSON BLOB のインデックス作成
この記事では、JSON を格納している BLOB から構造化コンテンツを抽出するために Azure Search BLOB インデクサーを構成する方法を説明します。

## <a name="scenarios"></a>シナリオ
[Azure Search BLOB インデクサー](search-howto-indexing-azure-blob-storage.md) は、既定では JSON BLOB を 1 つのテキスト チャンクとして解析します。 多くの場合、JSON ドキュメントの構造はそのままに維持する必要があります。 たとえば、次のような JSON ドキュメントについて考えてみましょう。

    {
        "article" : {
             "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

この場合は、JSON ドキュメントを "text"、"datePublished"、"tags" の各フィールドを持つ Azure Search ドキュメントに解析できます。

また、BLOB に **JSON オブジェクトの配列**が含まれる場合は、配列の各要素を個別の Azure Search ドキュメントにすることができます。 たとえば、次の JSON を含む BLOB を考えてみます。  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

それぞれ "id" と "text" のフィールドを持つ 3 つの独立したドキュメントを Azure Search インデックスに設定できます。

> [!IMPORTANT]
> 現在この機能はプレビュー版です。 バージョン **2015-02-28-Preview** を使用した REST API でのみ利用できます。 プレビュー版の API は、テストと評価を目的としたものです。運用環境での使用は避けてください。
>
>

## <a name="setting-up-json-indexing"></a>JSON インデックス作成の設定
JSON BLOB のインデックスを作成するには、`parsingMode` 構成パラメーターを `json` (1 つのドキュメントとして各 BLOB にインデックスを作成する場合) または `jsonArray` (BLOB に JSON 配列が含まれる場合) に設定します。

    {
      "name" : "my-json-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "json" | "jsonArray" } }
    }

必要に応じて **フィールド マッピング** を使用し、ターゲットの検索インデックスへの設定に使用するソース JSON ドキュメントのプロパティを選択します。  これについては、以下で詳しく説明します。

> [!IMPORTANT]
> `json` または `jsonArray` 解析モードを使用すると、Azure Search ではデータ ソース内のすべての BLOB が JSON になると見なされます。 JSON BLOB と JSON 以外の BLOB が混在するデータ ソースをサポートする必要がある場合は、 [UserVoice のサイト](https://feedback.azure.com/forums/263029-azure-search)でお知らせください。
>
>

## <a name="using-field-mappings-to-build-search-documents"></a>フィールド マッピングを使用して検索ドキュメントを作成する
現在、Azure Search ではプリミティブ データ型、文字列配列、GeoJSON ポイントのみをサポートしているため、任意の JSON ドキュメントに対して直接インデックス作成を行うことはできません。 ただし、 **フィールド マッピング** を使用すると、JSON ドキュメントの一部を選択して、それを検索ドキュメントの最上位レベルのフィールドに "引き上げる" ことができます。 フィールド マッピングの基本については、「 [データ ソースと検索インデックスの橋渡し役としての Azure Search インデクサー フィールド マッピング](search-indexer-field-mappings.md)」をご覧ください。

JSON ドキュメントの例に戻りましょう。

    {
        "article" : {
             "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Edm.String 型の `text` フィールド、Edm.DateTimeOffset 型の `date` フィールド、コレクション (Edm.String) 型の `tags` フィールドを持つ検索インデックスがあるとします。 JSON を必要な形式にマッピングするには、次のフィールド マッピングを使用します。

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

マッピング内のソース フィールド名は、 [JSON ポインター](http://tools.ietf.org/html/rfc6901) の表記を使用して指定されています。 スラッシュから開始して JSON ドキュメントのルートを参照し、その後はスラッシュ区切りのパスで目的のプロパティ (任意の入れ子レベル) まで指定します。

0 から始まるインデックスを使用して個々の配列要素を参照することもできます。 たとえば、上の例から "tags" 配列の最初の要素を選択するには、次のようなフィールド マッピングを使用します。

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> フィールド マッピングのパス内のソース フィールド名が、JSON に存在しないプロパティを参照している場合、そのマッピングはエラーなしでスキップされます。 これは、異なるスキーマを使用したドキュメントをサポートできるようにするためです (一般的なユース ケースです)。 検証機能がないため、フィールド マッピングの仕様を入力するときは、入力ミスをしないように注意する必要があります。
>
>

JSON ドキュメントに単純な最上位レベルのプロパティのみが含まれる場合、フィールド マッピングは必要ありません。 たとえば、次のような JSON の場合、最上位レベルのプロパティ "text"、"datePublished"、"tags" は検索インデックス内の対応する各フィールドに直接マッピングされます。

    {
       "text" : "A hopefully useful article explaining how to parse JSON blobs",
       "datePublished" : "2016-04-13"
       "tags" : [ "search", "storage", "howto" ]    
     }

## <a name="indexing-nested-json-arrays"></a>入れ子になった JSON 配列のインデックス作成
JSON オブジェクトの配列にインデックスを作成するときに、そのドキュメント内のどこかで配列が入れ子になっていたらどうすればよいでしょうか。 `documentRoot` 構成プロパティを使用して、入れ子になった配列が格納されているプロパティを選択できます。 たとえば、次のような BLOB があるとします。

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

次の構成を使用して、"level2" プロパティに格納されている配列のインデックスを作成します。

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }


## <a name="request-examples"></a>要求例
すべてをまとめた完全なペイロード例を次に示します。

データソース:

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

インデクサー:

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }

## <a name="help-us-make-azure-search-better"></a>Azure Search の品質向上にご協力ください
ご希望の機能や品質向上のアイデアがありましたら、 [UserVoice サイト](https://feedback.azure.com/forums/263029-azure-search/)にぜひお寄せください。



<!--HONumber=Nov16_HO3-->


