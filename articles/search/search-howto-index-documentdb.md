---
title: "Azure Search 用の Cosmos DB データ ソースのインデックス作成 | Microsoft Docs"
description: "この記事では、Cosmos DB をデータ ソースとする Azure Search インデクサーの作成方法について説明します。"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 
ms.service: search
ms.devlang: rest-api
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: search
ms.date: 08/10/2017
ms.author: eugenesh
ms.translationtype: HT
ms.sourcegitcommit: 760543dc3880cb0dbe14070055b528b94cffd36b
ms.openlocfilehash: 2f1791393b1e59721cc5a1030927cd00d74a5f13
ms.contentlocale: ja-jp
ms.lasthandoff: 08/10/2017

---
# <a name="connecting-cosmos-db-with-azure-search-using-indexers"></a>インデクサーを使用した Cosmos DB と Azure Search の接続

Cosmos DB データの優れた検索エクスペリエンスを実装する必要がある場合、Azure Search インデクサーを使用すると、Azure Search インデックスにデータをプルできます。 この記事では、インデックス作成のインフラストラクチャを保守するコードを記述することなく Azure Cosmos DB と Azure Search を統合する方法を説明します。

Cosmos DB のインデクサーをセットアップするには、[Azure Search サービス](search-create-service-portal.md)、インデックス、データ ソース、そして最後に、インデクサーが必要です。 これらのオブジェクトは、[ポータル](search-import-data-portal.md)、[.NET SDK](/dotnet/api/microsoft.azure.search)、または .NET 以外のすべての言語用の [REST API](/rest/api/searchservice/) を使用して作成します。 

ポータルを選択した場合は、[データのインポート ウィザード](search-import-data-portal.md)に従って、これらすべてのリソースを作成します。

> [!NOTE]
> Cosmos DB とは、次世代の DocumentDB です。 製品名は変更されていますが、構文は以前と同じです。 このインデクサーの記事で指示されているように、引き続き `documentdb` を指定してください。 

> [!TIP]
> **データのインポート** ウィザードを Cosmos DB ダッシュボードから起動して、そのデータ ソースのインデックス作成を簡略化できます。 左側のナビゲーションで、**[コレクション]** > **[Add Azure Search (Azure Search の追加)]** に移動します。

<a name="Concepts"></a>
## <a name="azure-search-indexer-concepts"></a>Azure Search インデクサーの概念
Azure Search では、データ ソース (Cosmos DB を含む) とそのデータ ソースに対して動作するインデクサーの作成および管理をサポートしています。

**データ ソース**は、インデックスを作成するデータ、資格情報、およびデータの変更を識別するためのポリシー (コレクション内の変更または削除されたドキュメントなど) を指定します。 データ ソースは、複数のインデクサーから使用できるように、独立したリソースとして定義します。

**インデクサー** は、データ ソースからターゲットの検索インデックスにデータが流れるしくみを説明します。 インデクサーは次の目的で使用します。

* 1 回限りのデータのコピーを実行して、インデックスを作成する。
* スケジュールに従ってデータ ソースの変更とインデックスを同期する。 スケジュールは、インデクサーの定義の一部です。
* 必要に応じてインデックスのオンデマンド更新を呼び出す。

<a name="CreateDataSource"></a>
## <a name="step-1-create-a-data-source"></a>手順 1: データ ソースを作成する
データ ソースを作成するには、POST 要求を発行します。

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": { "name": "myDocDbCollectionId", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

要求の本文には、次のフィールドを含むデータ ソースの定義が含まれている必要があります。

* **name**: Cosmos DB データベースを表す名前を選択します。
* **type**: は `documentdb` である必要があります。
* **credentials**:
  
  * **connectionString**: 必須。 次の形式で Azure Cosmos DB データベースへの接続情報を指定します。`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`
* **container**:
  
  * **name**: 必須。 インデックスを作成する Cosmos DB コレクションの ID を指定します。
  * **query**: 省略可能。 任意の JSON ドキュメントを、Azure Search がインデックスを作成できるフラット スキーマにフラット化するクエリを指定できます。
* **dataChangeDetectionPolicy**: 推奨。 「[変更されたドキュメントのインデックス作成](#DataChangeDetectionPolicy)」セクションを参照してください。
* **dataDeletionDetectionPolicy**: 省略可能。 「[削除されたドキュメントのインデックス作成](#DataDeletionDetectionPolicy)」セクションを参照してください。

### <a name="using-queries-to-shape-indexed-data"></a>インデックス作成するデータの処理にクエリを活用する
Cosmos DB クエリを指定すると、ネストされたプロパティや配列の平坦化、JSON プロパティのプロジェクション、インデックスを作成するデータのフィルター処理を行うことができます。 

ドキュメントのサンプル:

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "documentdb", "search"]
    }

フィルター処理クエリ:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

平坦化クエリ:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
プロジェクション クエリ:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


配列を平坦化するクエリ:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts

<a name="CreateIndex"></a>
## <a name="step-2-create-an-index"></a>手順 2: インデックスを作成する
ターゲットの Azure Search インデックスがまだない場合は、インデックスを作成します。 インデックスを作成するには、[Azure Portal UI](search-create-index-portal.md)、[Create Index REST API](/rest/api/searchservice/create-index) または [Index クラス](/dotnet/api/microsoft.azure.search.models.index)を使用します。

次の例では、ID と説明のフィールドを持つインデックスを作成します。

    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

ターゲット インデックスのスキーマがソース JSON ドキュメントのスキーマまたはカスタムのクエリ プロジェクションの出力と互換性があることを確認します。

> [!NOTE]
> パーティション分割されたコレクションでは、Cosmos DB の `_rid` プロパティが既定のドキュメント キーになります。これは、Azure Search では `rid` という名前に変更されます。 また、Cosmos DB の `_rid` 値には、Azure Search キーでは無効な文字が含まれています。 そのため、`_rid` 値は Base64 でエンコードされます。
> 
> 

### <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>JSON データ型と Azure Search データ型間のマッピング
| JSON データ型 | 互換性のあるターゲット インデックス フィールドの型 |
| --- | --- |
| ブール値 |Edm.Boolean、Edm.String |
| 整数などの数値 |Edm.Int32、Edm.Int64、Edm.String |
| 浮動小数点などの数値 |Edm.Double、Edm.String |
| String |Edm.String |
| プリミティブ型の配列。例: ["a"、"b"、"c"] |Collection(Edm.String) |
| 日付などの文字列 |Edm.DateTimeOffset、Edm.String |
| GeoJSON オブジェクト。例: { "type": "Point", "coordinates": [ long, lat ] } |Edm.GeographyPoint |
| その他の JSON オブジェクト |該当なし |

<a name="CreateIndexer"></a>
## <a name="step-3-create-an-indexer"></a>手順 3: インデクサーを作成する

インデックスとデータ ソースを作成したら、インデクサーを作成できます。

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mydocdbindexer",
      "dataSourceName" : "mydocdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

このインデクサーは 2 時間ごとに実行されます (スケジュールの間隔が "PT2H" に設定されています)。 インデクサーを 30 分ごとに実行するには、間隔を "PT30M" に設定します。 サポートされている最短の間隔は 5 分です。 スケジュールは省略可能です。省略した場合、インデクサーは作成時に一度だけ実行されます。 ただし、いつでもオンデマンドでインデクサーを実行できます。   

インデクサー作成 API の詳細については、「 [インデクサーの作成](https://docs.microsoft.com/rest/api/searchservice/create-indexer)」をご覧ください。

<a id="RunIndexer"></a>
### <a name="running-indexer-on-demand"></a>オンデマンドでインデクサーを実行する
スケジュールに従って定期的に実行されるだけでなく、オンデマンドでインデクサーを呼び出すこともできます。

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2016-09-01
    api-key: [Search service admin key]

> [!NOTE]
> API の実行で正しい応答があった場合、インデクサーの呼び出しはスケジュール済みですが、実際の処理は非同期的に実行されます。 

ポータル、または次に説明する Get Indexer Status API を使用すると、インデクサーの状態を監視できます。 

<a name="GetIndexerStatus"></a>
### <a name="getting-indexer-status"></a>インデクサーの状態を取得する
インデクサーの現在の状態と実行履歴を取得できます。

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2016-09-01
    api-key: [Search service admin key]

応答には、全体的なインデクサーの状態、最後の (または実行中の) インデクサー呼び出し、およびインデクサー呼び出しの最近の履歴が含まれています。

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

実行履歴には、最近完了した 50 件の実行内容が含まれ、時系列の逆の順に並べられます (そのため、最近の実行内容が応答の最初に表示されます)。

<a name="DataChangeDetectionPolicy"></a>
## <a name="indexing-changed-documents"></a>変更されたドキュメントのインデックス作成
データ変更の検出ポリシーの目的は、変更されたデータ項目を効率的に識別することです。 現在、唯一サポートされているポリシーは、次に示すように、Cosmos DB によって指定される `_ts` (タイムスタンプ) プロパティを使用した `High Water Mark` ポリシーです。

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

インデクサーの優れたパフォーマンスを確保するのには、このポリシーを使用することが推奨されます。 

カスタム クエリを使用している場合、`_ts` プロパティはクエリによって投影されていることを確認します。

<a name="IncrementalProgress"></a>
### <a name="incremental-progress-and-custom-queries"></a>増分の進行状況とカスタム クエリ
インデックスの作成中に増分の進行状況を利用すると、一時的な障害や実行の制限時間によってインデクサーの実行が中断された場合、次の実行時に、最初からコレクション全体のインデックスを再作成するのではなく、中断したところからインデックスを作成することができます。 大規模なコレクションのインデックスを作成する場合、この機能は特に重要です。 

カスタム クエリを使用するときに増分の進行状況を有効にするには、クエリを使用して `_ts` 列で結果を並べ替えておきます。 こうすることで Azure Search が使用する定期的なチェックポイントが作成され、エラーが発生した場合に増分の進行状況を利用できます。   

ただし状況によっては、クエリに `ORDER BY [collection alias]._ts` 句が含まれる場合でも、クエリ結果が `_ts` で並べ替えられることを Azure Search で推論されない可能性があります。 `assumeOrderByHighWaterMarkColumn` 構成プロパティを使用して結果が並べ替えられるように Azure Search に指示することもできます。 このヒントを指定するには、次のようにインデクサーを作成または更新します。 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>
## <a name="indexing-deleted-documents"></a>削除されたドキュメントのインデックス作成
コレクションから行が削除されると、通常、検索インデックスからも同様に行を削除する必要があります。 データ削除の検出ポリシーの目的は、削除されたデータ項目を効率的に識別することです。 現在、唯一サポートされているポリシーは、`Soft Delete` ポリシー (削除されると何らかのフラグでマークされる) のみです。このポリシーは、次のように指定します。

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

カスタム クエリを使用している場合、`softDeleteColumnName` から参照されているプロパティはクエリによって投影されていることを確認します。

次の例では、ソフト削除ポリシーとともにデータ ソースを作成します。

    POST https://[Search service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": { "name": "myDocDbCollectionId" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

## <a name="NextSteps"></a>次のステップ
ご利用ありがとうございます。 Cosmos DB のインデクサーを使用して、Azure Cosmos DB を Azure Search と統合する方法についての説明は以上で終了です。

* Azure Cosmos DB の詳細については、[Cosmos DB のサービス ページ](https://azure.microsoft.com/services/documentdb/)をご覧ください。
* Azure Search の詳細については、 [Search サービス ページ](https://azure.microsoft.com/services/search/)をご覧ください。

