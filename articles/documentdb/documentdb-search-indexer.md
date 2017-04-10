---
title: "インデクサーを使用した DocumentDB と Azure Search の接続 | Microsoft Docs"
description: "この記事では、Azure Search インデクサーとデータ ソースとして DocumentDB を使用する方法を説明します。"
services: documentdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: fdef3d1d-b814-4161-bdb8-e47d29da596f
ms.service: documentdb
ms.devlang: rest-api
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 01/10/2017
ms.author: mimig
redirect_url: https://docs.microsoft.com/azure/search/search-howto-index-documentdb
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 9a5416b1c26d1e8eaecec0ada79d357f32ca5ab1
ms.openlocfilehash: c318d7133e26ec3a39d6fc97b0693b44d742d456
ms.lasthandoff: 01/12/2017


---
# <a name="connecting-documentdb-with-azure-search-using-indexers"></a>インデクサーを使用した DocumentDB と Azure Search の接続
DocumentDB データの優れた検索機能を実装する場合は、DocumentDB の Azure Search インデクサーをご使用ください。 この記事では、インデックス作成のインフラストラクチャを保守するコードを記述することなく Azure DocumentDB と Azure Search を統合する方法を説明します。

このセットアップを行うには、[Azure Search アカウントをセットアップ](../search/search-create-service-portal.md)し (標準の検索のアップグレードは不要です)、[Azure Search REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) を呼び出して DocumentDB **データ ソース**とそのデータ ソースの**インデクサー**を作成する必要があります。

REST API とやり取りするために要求を送信するには、[Postman](https://www.getpostman.com/)、[Fiddler](http://www.telerik.com/fiddler) など、任意のツールを自由に使用できます。

## <a name="a-idconceptsaazure-search-indexer-concepts"></a><a id="Concepts"></a>Azure Search インデクサーの概念
Azure Search では、データ ソース (DocumentDB を含む) とそのデータ ソースに対して動作するインデクサーの作成および管理をサポートしています。

**データ ソース** により、インデックスを作成する必要があるデータ、データにアクセスするための資格情報、および Azure Search でのデータの変更 (コレクション内のドキュメントの変更や削除など) を効率的に識別するためのポリシーを指定します。 データ ソースは、複数のインデクサーから使用できるように、独立したリソースとして定義します。

**インデクサー** は、データ ソースからターゲットの検索インデックスにデータが流れるしくみを説明します。 ターゲット インデックスとデータ ソースの組み合わせごとにインデクサーを&1; つ作成するように設計する必要があります。 複数のインデクサーから同じインデックスへ書き込むことができますが、1 つのインデクサーから書き込むことができるのは&1; つのインデックスのみです。 インデクサーは次の目的で使用します。

* 1 回限りのデータのコピーを実行して、インデックスを作成する。
* スケジュールに従ってデータ ソースの変更とインデックスを同期する。 スケジュールは、インデクサーの定義の一部です。
* 必要に応じてインデックスのオンデマンド更新を呼び出す。

## <a name="a-idcreatedatasourceastep-1-create-a-data-source"></a><a id="CreateDataSource"></a>手順 1: データ ソースを作成する
HTTP POST 要求を発行して、次の要求ヘッダーを含む新しいデータ ソースを Azure Search サービスに作成します。

    POST https://[Search service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

`api-version` は必須です。 有効な値には `2015-02-28` 以降のバージョンが含まれます。 サポートされているすべての Search API バージョンについては、「 [Azure Search の API バージョン](../search/search-api-versions.md) 」を参照してください。

要求の本文には、次のフィールドを含むデータ ソースの定義が含まれている必要があります。

* **name**: DocumentDB データベースを表す名前を選択します。
* **type**: `documentdb` を使用。
* **credentials**:
  
  * **connectionString**: 必須。 次の形式で Azure DocumentDB データベースへの接続情報を指定します。`AccountEndpoint=<DocumentDB endpoint url>;AccountKey=<DocumentDB auth key>;Database=<DocumentDB database id>`
* **container**:
  
  * **name**: 必須。 インデックスを作成する DocumentDB コレクションの ID を指定します。
  * **query**: 省略可能。 任意の JSON ドキュメントを、Azure Search がインデックスを作成できるフラット スキーマにフラット化するクエリを指定できます。
* **dataChangeDetectionPolicy**: 省略可能。 以下の [データ変更の検出ポリシーに関するセクション](#DataChangeDetectionPolicy) を参照してください。
* **dataDeletionDetectionPolicy**: 省略可能。 以下の [データ削除の検出ポリシーに関するセクション](#DataDeletionDetectionPolicy) を参照してください。

以下の [要求本文の例](#CreateDataSourceExample)を参照してください。

### <a name="a-iddatachangedetectionpolicyacapturing-changed-documents"></a><a id="DataChangeDetectionPolicy"></a>ドキュメントの変更のキャプチャ
データ変更の検出ポリシーの目的は、変更されたデータ項目を効率的に識別することです。 現在、唯一サポートされているポリシーは、次のような、DocumentDB によって指定される `_ts` 最終更新タイムスタンプ プロパティを使用した `High Water Mark` ポリシーです。

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

さらに、クエリのプロジェクションと `WHERE` 句にも `_ts` を追加する必要があります。 次に例を示します。

    SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts >= @HighWaterMark

### <a name="a-iddatadeletiondetectionpolicyacapturing-deleted-documents"></a><a id="DataDeletionDetectionPolicy"></a>ドキュメントの削除のキャプチャ
ソース テーブルから行が削除されると、検索インデックスからも同様に行を削除する必要があります。 データ削除の検出ポリシーの目的は、削除されたデータ項目を効率的に識別することです。 現在、唯一サポートされているポリシーは、`Soft Delete` ポリシー (削除されると何らかのフラグでマークされる) のみです。このポリシーは、次のように指定します。

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

> [!NOTE]
> カスタムのプロジェクションを使用している場合は、SELECT 句に softDeleteColumnName プロパティを含める必要があります。
> 
> 

### <a name="a-idleveagingqueriesaleveraging-queries"></a><a id="LeveagingQueries"></a>クエリの活用
DocumentDB クエリの指定を使用すると、変更されたドキュメントや削除されたドキュメントのキャプチャに加えて、入れ子になったプロパティの平坦化、配列のアンワインド、json プロパティのプロジェクション、インデックス作成するデータのフィルター処理を行うことができます。 インデックスの作成に使用するデータを処理すると、Azure Search Indexer のパフォーマンスを向上させることができます。

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


平坦化のクエリ:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark
    
    
プロジェクションのクエリ:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark


配列をアンワインドするクエリ:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark
    
    
フィルター処理のクエリ:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark


### <a name="a-idcreatedatasourceexamplearequest-body-example"></a><a id="CreateDataSourceExample"></a>要求本文の例
次の例では、カスタム クエリとポリシーのヒントと一緒にデータ ソースを作成します。

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": {
            "name": "myDocDbCollectionId",
            "query": "SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts > @HighWaterMark"
        },
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

### <a name="response"></a>応答
データ ソースが正常に作成された場合は、HTTP 201 Created 応答を受け取ります。

## <a name="a-idcreateindexastep-2-create-an-index"></a><a id="CreateIndex"></a>手順 2: インデックスを作成する
ターゲットの Azure Search インデックスがまだない場合は、インデックスを作成します。 これは、[Azure Portal UI](../search/search-create-index-portal.md) から、または[インデックス作成 API](https://msdn.microsoft.com/library/azure/dn798941.aspx) を使用して行うことができます。

    POST https://[Search service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]


ターゲット インデックスのスキーマがソース JSON ドキュメントのスキーマまたはカスタムのクエリ プロジェクションの出力と互換性があることを確認します。

> [!NOTE]
> パーティション分割されたコレクションでは、DocumentDB の `_rid` プロパティが既定のドキュメント キーになります。これは、Azure Search では `rid` という名前に変更されます。 また、DocumentDB の `_rid` 値には、Azure Search のキーでは無効な文字が含まれています。そのため、`_rid` 値は Base64 でエンコードされます。
> 
> 

### <a name="figure-a-mapping-between-json-data-types-and-azure-search-data-types"></a>図 A: JSON データ型と Azure Search データ型間のマッピング
| JSON データ型 | 互換性のあるターゲット インデックス フィールドの型 |
| --- | --- |
| ブール値 |Edm.Boolean、Edm.String |
| 整数などの数値 |Edm.Int32、Edm.Int64、Edm.String |
| 浮動小数点などの数値 |Edm.Double、Edm.String |
| String |Edm.String |
| プリミティブ型の配列。例: "a"、"b"、"c" |Collection(Edm.String) |
| 日付などの文字列 |Edm.DateTimeOffset、Edm.String |
| GeoJSON オブジェクト。例: { "type": "Point", "coordinates": [ long, lat ] } |Edm.GeographyPoint |
| その他の JSON オブジェクト |該当なし |

### <a name="a-idcreateindexexamplearequest-body-example"></a><a id="CreateIndexExample"></a>要求本文の例
次の例では、ID と説明のフィールドを持つインデックスを作成します。

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

### <a name="response"></a>応答
インデックスが正常に作成された場合、HTTP 201 Created 応答を受け取ります。

## <a name="a-idcreateindexerastep-3-create-an-indexer"></a><a id="CreateIndexer"></a>手順 3: インデクサーを作成する
HTTP POST 要求を次のヘッダーと共に使用して、Azure Search サービス内に新しいインデクサーを作成できます。

    POST https://[Search service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

要求の本文には、次のフィールドを含むインデクサーの定義が含まれている必要があります。

* **name**: 必須。 インデクサーの名前。
* **dataSourceName**: 必須。 既存のデータ ソースの名前。
* **targetIndexName**: 必須。 既存のインデックスの名前。
* **schedule**: 省略可能。 以下の [インデックス作成のスケジュールに関するセクション](#IndexingSchedule) を参照してください。

### <a name="a-idindexingschedulearunning-indexers-on-a-schedule"></a><a id="IndexingSchedule"></a>スケジュールに従ったインデクサーの実行
インデクサーには、必要に応じてスケジュールを指定できます。 スケジュールが存在する場合、インデクサーはスケジュールに従って定期的に実行されます。 スケジュールには次の属性があります。

* **interval**: 必須。 インデクサーが実行される間隔または期間を指定する時間の値。 許可される最短の間隔は 5 分です。最長は 1 日です。 XSD "dayTimeDuration" 値 ([ISO 8601 期間](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)値の制限されたサブセット) として書式設定する必要があります。 使用されるパターンは、`P(nD)(T(nH)(nM))` です。 たとえば、15 分ごとの場合は `PT15M`、2 分ごとの場合は `PT2H` です。
* **startTime**: 必須。 インデクサーの実行を開始する時刻を指定する UTC 日時。

### <a name="a-idcreateindexerexamplearequest-body-example"></a><a id="CreateIndexerExample"></a>要求本文の例
次の例では、UTC 2015 年 1 月 1 日から 1 時間ごとに実行されるスケジュールで `myDocDbDataSource` データ ソースによって参照されるコレクションのデータを `mySearchIndex` インデックスにコピーするインデクサーを作成します。

    {
        "name" : "mysearchindexer",
        "dataSourceName" : "mydocdbdatasource",
        "targetIndexName" : "mysearchindex",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" }
    }

### <a name="response"></a>応答
インデクサーが正常に作成された場合、HTTP 201 Created 応答を受け取ります。

## <a name="a-idrunindexerastep-4-run-an-indexer"></a><a id="RunIndexer"></a>手順 4: インデクサーを実行する
スケジュールに従って定期的に実行されるだけでなく、次の HTTP POST 要求を発行して、オンデマンドでインデクサーを呼び出すこともできます。

    POST https://[Search service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [Search service admin key]

### <a name="response"></a>応答
インデクサーが正常に呼び出された場合、HTTP 202 Accepted 応答を受け取ります。

## <a name="a-namegetindexerstatusastep-5-get-indexer-status"></a><a name="GetIndexerStatus"></a>手順 5: インデクサーの状態を取得する
HTTP GET 要求を発行して、インデクサーの現在の状態と実行の履歴を取得することができます。

    GET https://[Search service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [Search service admin key]

### <a name="response"></a>応答
インデクサーの全体的な正常性状態、インデクサーの最後の呼び出し、および (存在する場合は) インデクサー呼び出しの最近の履歴に関する情報が含まれる応答の本文と共に、HTTP 200 OK 応答が表示されます。

応答は、次のようになります。

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

## <a name="a-namenextstepsanext-steps"></a><a name="NextSteps"></a>次のステップ
ご利用ありがとうございます。 DocumentDB のインデクサーを使用して、Azure DocumentDB を Azure Search と統合する方法についての説明は以上で終了です。

* Azure DocumentDB の詳細については、 [DocumentDB サービス ページ](https://azure.microsoft.com/services/documentdb/)をご覧ください。
* Azure Search の詳細については、 [Search サービス ページ](https://azure.microsoft.com/services/search/)をご覧ください。


