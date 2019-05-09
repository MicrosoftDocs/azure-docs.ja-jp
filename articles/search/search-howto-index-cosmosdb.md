---
title: Azure Cosmos DB データ ソースのインデックス作成 - Azure Search
description: Azure Cosmos DB のデータ ソースをクロールし、Azure Search のフルテキスト検索可能なインデックスにデータを取り込みます。 インデクサーにより、選択したデータ ソース (Azure Cosmos DB など) のデータ インジェストが自動化されます。
ms.date: 02/28/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 019945c48342238a1caa7611bdff6d06fd1e2bd9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58883396"
---
# <a name="how-to-index-cosmos-db-using-an-azure-search-indexer"></a>Azure Search インデクサーを使用して Cosmos DB のインデックスを作成する方法

この記事では、コンテンツを抽出して Azure Search で検索できるようにするために Azure Comos DB [インデクサー](search-indexer-overview.md)を構成する方法を説明します。 このワークフローでは、Azure Search インデックスを作成して、Azure Cosmos DB から抽出された既存のテキストとともに読み込みます。 

用語が混乱を招く可能性があるため、[Azure Cosmos DB のインデックス作成](https://docs.microsoft.com/azure/cosmos-db/index-overview)と [Azure Search のインデックス作成](search-what-is-an-index.md)はそれぞれのサービスに固有の異なる操作であることに注意してください。 Azure Search のインデックス作成を開始するには、Azure Cosmos DB データベースが既に存在していて、データが格納されている必要があります。

[ポータル](#cosmos-indexer-portal)、REST API、または .NET SDK を使用して、Cosmos コンテンツのインデックスを作成できます。 Azure Search の Cosmos DB インデクサーでは、以下のプロトコルを使用してアクセスする [Azure Cosmos 項目](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items)をクロールできます。

* [SQL API](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference) 
* [MongoDB API](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction) (この API の Azure Search のサポートはパブリック プレビュー段階です)  

> [!Note]
> ユーザーの声に、その他の API サポートについての既存の項目があります。 Azure Search でサポートされていることを確認したい Cosmos API([Table API](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab)、[Graph API](https://feedback.azure.com/forums/263029-azure-search/suggestions/13285011-add-graph-databases-to-your-data-sources-eg-neo4)、[Apache Cassandra API](https://feedback.azure.com/forums/263029-azure-search/suggestions/32857525-indexer-crawler-for-apache-cassandra-api-in-azu)) を投票していただくことができます。
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>ポータルの使用

Azure Cosmos 項目のインデックスを作成する最も簡単な方法は、[Azure portal](https://portal.azure.com/) のウィザードを使用することです。 コンテナー内のデータをサンプリングしてメタデータを解析することにより、Azure Search の[**データのインポート**](search-import-data-portal.md)ウィザードでは、既定のインデックスを作成し、ソース フィールドをターゲット インデックス フィールドにマップし、1 回の操作でインデックスを読み込むことができます。 ソース データのサイズと複雑さによっては、数分で運用可能なフルテキスト検索インデックスを作成できます。

Azure Search と Azure Cosmos DB の両方で、可能であれば同じリージョンで、同じ Azure サブスクリプションを使用することをお勧めします。

### <a name="1---prepare-source-data"></a>1 - ソース データを準備する

Cosmos アカウント、SQL API または MongoDB API にマップされた Azure Cosmos データベース、および JSON ドキュメントのコンテナーが必要です。 

Cosmos DB データベースにデータが含まれることを確認してください。 [データのインポート ウィザード](search-import-data-portal.md)では、メタデータが読み取られ、インデックス スキーマを推論するためにデータのサンプリングが実行されますが、Cosmos DB からもデータが読み込まれます。 このデータが見つからない場合、ウィザードは "データ ソースからインデックス スキーマを削除するときにエラーが発生しました:データソース 'emptycollection' がデータを返さなかったため、プロトタイプ インデックスを構築できませんでした" というエラーで停止します。

### <a name="2---start-import-data-wizard"></a>2 - データのインポート ウィザードを開始する

Azure Search サービス ページのコマンド バーから、またはストレージ アカウントの左側のナビゲーション ウィンドウの **[設定]** セクションで **[Azure Search の追加]** をクリックして、[ウィザードを開始する](search-import-data-portal.md)ことができます。

   ![ポータルの [データのインポート] コマンド](./media/search-import-data-portal/import-data-cmd2.png "データのインポート ウィザードを起動する")

### <a name="3---set-the-data-source"></a>3 - データ ソースを設定する

> [!NOTE] 
> 現時点では、Azure portal または .NET SDK を使用して、**MongoDB** データ ソースを作成または編集することはできません。 ただし、ポータルでは MongoDB のインデクサーの実行履歴を監視**できます**。

**データソース**のページでは、ソースが次のように指定された **Cosmos DB** になっている必要があります。

+ **[名前]** は データ ソースの名前です。 作成されたら、その他のワークロード用に選択できます。

+ **[Cosmos DB アカウント]** は、`AccountEndpoint` と `AccountKey` を含む、Cosmos DB からのプライマリまたはセカンダリの接続文字列である必要があります。 アカウントは、データを SQL API または Mongo DB API のどちらとしてキャストするかを決定します

+ **[データベース]** は、アカウントからの既存のデータベースです。 

+ **[コレクション]** はドキュメントのコンテナーです。 インポートが成功するためには、ドキュメントが存在している必要があります。 

+ **[クエリ]** は、すべてのドキュメントが必要な場合は空にすることができます。それ以外の場合は、ドキュメントのサブセットを選択するクエリを入力できます。 

   ![Cosmos DB データ ソースの定義](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Cosmos DB データ ソースの定義")

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 - ウィザードの [認知検索を追加します] ページをスキップする

ドキュメントをインポートするときは、コグニティブ スキルを追加する必要はありません。 インデックス作成パイプラインに [Cognitive Services APIs と変換を含める](cognitive-search-concept-intro.md)特定のニーズがない限り、このステップをスキップする必要があります。

このステップをスキップするには、先に次のページに移動します。

   ![コグニティブ検索用の次のページ ボタン](media/search-get-started-portal/next-button-add-cog-search.png)

そのページから、インデックスのカスタマイズに進むことができます。

   ![コグニティブ スキル手順のスキップ](media/search-get-started-portal/skip-cog-skill-step.png)

### <a name="5---set-index-attributes"></a>5 - インデックスの属性を設定する

**[インデックス]** ページには、フィールドとデータ型およびインデックスの属性を設定するための一連のチェック ボックスが一覧表示されます。 このウィザードで、メタデータに基づいてソース データをサンプリングすることで、フィールドの一覧を生成できます。 

属性列の上部にあるチェック ボックスをクリックすることで、属性を一括選択できます。 クライアント アプリに返してフル検索処理の対象にする必要があるすべてのフィールドで、**[取得可能]** と **[検索可能]** を選択します。 整数はフルテキスト検索もあいまい検索もできないことに注意してください (数値は逐語的に評価され、多くの場合フィルターで役立ちます)。

詳細については、[インデックス属性](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib)と[言語アナライザー](https://docs.microsoft.com/rest/api/searchservice/language-support)の説明を参照してください。 

時間をかけて選択内容を確認します。 ウィザードを実行すると、物理データ構造が作成されて、すべてのオブジェクトを削除して再作成しない限り、これらのフィールドを編集することはできません。

   ![Cosmos DB インデックスの定義](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Cosmos DB インデックスの定義")

### <a name="6---create-indexer"></a>6 - インデクサーを作成する

完全に指定すると、ウィザードによって検索サービスに 3 つの異なるオブジェクトが作成されます。 データ ソース オブジェクトとインデックス オブジェクトは、Azure Search サービスに名前付きリソースとして保存されます。 最後のステップでは、インデクサー オブジェクトが作成されます。 インデクサーに名前を付けると、インデクサーはスタンドアロン リソースとして存在することができ、同じウィザードのシーケンスで作成されるインデックス オブジェクトやデータ ソース オブジェクトとは独立して、スケジュールを設定したり管理したりできます。

インデクサーに慣れていないユーザーのために説明すると、"*インデクサー*" とは Azure Search のリソースであり、外部データ ソースで検索可能なコンテンツのクロールを行います。 **データのインポート** ウィザードで出力されるインデクサーでは、Cosmos DB データ ソースがクロールされ、検索可能なコンテンツが抽出されて、Azure Search のインデックスにインポートされます。

次のスクリーンショットは、既定のインデクサー構成を示しています。 インデクサーを 1 回だけ実行する場合は、**[1 度]** に切り替えることができます。 **[送信]** をクリックしてウィザードを実行し、すべてのオブジェクトを作成します。 インデックスの作成はすぐに開始されます。

   ![Cosmos DB インデクサーの定義](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Cosmos DB インデクサーの定義")

ポータルのページでデータのインポートを監視することができます。 進行状況の通知では、インデックス作成の状態と、アップロードされたドキュメントの数が示されます。 

インデックスの作成が完了したら、[Search エクスプローラー](search-explorer.md)を使用してインデックスのクエリを実行できます。

> [!NOTE]
> 期待どおりのデータが表示されない場合は、その他のフィールドにその他の属性を設定する必要があります。 今作成したインデックスとインデクサーを削除し、もう一度ウィザードの手順に従って、手順 5 でインデックス属性の選択内容を変更してください。 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>REST API の使用

REST API を使用して、Azure Search のすべてのインデクサーに共通する 3 部構成のワークフロー (データ ソースを作成し、インデックスを作成して、インデクサーを作成する) で、Azure Comos DB データのインデックスを作成できます。 Cosmos ストレージからのデータ抽出は、インデクサーの作成要求を送信するときに行われます。 この要求が完了すると、クエリ可能なインデックスが作成されます。 

MongoDB を評価する場合は、REST API を使用してデータ ソースを作成する必要があります。

ご自分の Cosmos DB アカウントで、コレクションですべてのドキュメントのインデックスを自動的に作成するかどうかを選択できます。 既定では、すべてのドキュメントのインデックスが自動的に作成されますが、自動インデックス作成を無効にすることができます。 自動インデックス作成が無効になっている場合、自己リンクまたはドキュメント ID を使用したクエリでのみドキュメントにアクセスできます。 Azure Search では、Azure Search によってインデックスが作成されるコレクションで、Cosmos DB の自動インデックス作成が有効化される必要があります。 

> [!NOTE]
> Azure Cosmos DB とは、次世代の DocumentDB です。 製品名が変更されていますが、下位互換性のために Azure Search インデクサーの `documentdb` 公文は Azure Search API とポータル ページの両方に依然として存在します。 インデクサーを構成するときには、この記事の説明に従って必ず `documentdb` 構文を指定します。


### <a name="1---assemble-inputs-for-the-request"></a>1 - 要求に対する入力をアセンブルする

要求ごとに、Azure Search サービス名と管理者キーを (POST ヘッダーに) 指定し、BLOB ストレージのストレージ アカウント名とキーを指定する必要があります。 [Postman](search-fiddler.md) を使用して、Azure Search に HTTP 要求を送信できます。

次の 4 つの値をメモ帳にコピーして、要求に貼り付けることができるようにします。

+ Azure Search サービス名
+ Azure Search 管理者キー
+ Cosmos DB の接続文字列

これらの値はポータルで調べることができます。

1. Azure Search のポータル ページで、[概要] ページから Search サービスの URL をコピーします。

2. 左側のナビゲーション ウィンドウで **[キー]** をクリックし、プライマリまたはセカンダリのいずれかのキー (どちらも働きは同じです) をコピーします。

3. Cosmos ストレージ アカウントのポータル ページに切り替えます。 左側のナビゲーション ウィンドウで、**[設定]** の **[キー]** をクリックします。 このページには、1 つの URI、2 組の接続文字列、および 2 組のキーが表示されます。 接続文字列のいずれかをメモ帳にコピーします。

### <a name="2---create-a-data-source"></a>2 - データ ソースを作成する

**データ ソース**は、インデックスを作成するデータ、資格情報、およびデータの変更を識別するためのポリシー (コレクション内の変更または削除されたドキュメントなど) を指定します。 データ ソースは、複数のインデクサーから使用できるように、独立したリソースとして定義します。

データ ソースを作成するには、POST 要求を作成します。

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

要求の本文には、次のフィールドを含むデータ ソースの定義が含まれている必要があります。

| フィールド   | 説明 |
|---------|-------------|
| **name** | 必須。 データ ソース オブジェクトを表す名前を選択します。 |
|**type**| 必須。 `documentdb`である必要があります。 |
|**credentials** | 必須。 Cosmos DB の接続文字列でなければなりません。<br/>SQL コレクションでは、接続文字列の形式は `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>` です<br/>MongoDB コレクションの場合は、**ApiKind=MongoDb** を接続文字列に追加します。<br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/>エンドポイント URL では、ポート番号の使用を避けてください。 ポート番号を含めると、Azure Search では、Azure Cosmos DB データベースのインデックスを作成できなくなります。|
| **container** | 次の要素が含まれます。 <br/>**name**:必須。 インデックスを作成するデータベース コレクションの ID を指定します。<br/>**query**: 省略可能。 任意の JSON ドキュメントを、Azure Search がインデックスを作成できるフラット スキーマにフラット化するクエリを指定できます。<br/>MongoDB コレクションの場合、クエリはサポートされません。 |
| **dataChangeDetectionPolicy** | 推奨。 「[変更されたドキュメントのインデックス作成](#DataChangeDetectionPolicy)」セクションを参照してください。|
|**dataDeletionDetectionPolicy** | 省略可能。 「[削除されたドキュメントのインデックス作成](#DataDeletionDetectionPolicy)」セクションを参照してください。|

### <a name="using-queries-to-shape-indexed-data"></a>インデックス作成するデータの処理にクエリを活用する
SQL クエリを指定すると、ネストされたプロパティや配列のフラット化、JSON プロパティのプロジェクション、インデックスを作成するデータのフィルター処理を行うことができます。 

> [!WARNING]
> **MongoDB** コレクションではカスタム クエリはサポートされていません。`container.query` パラメーターは null に設定するか、省略する必要があります。 カスタム クエリを使用する必要がある場合は、[ユーザーの声](https://feedback.azure.com/forums/263029-azure-search) Web サイトでお知らせください。

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


### <a name="3---create-a-target-search-index"></a>3 - ターゲット検索インデックスを作成する 

まだない場合は、[ターゲットの Azure Search インデックスを作成します](/rest/api/searchservice/create-index)。 次の例では、ID と説明のフィールドを持つインデックスを作成します。

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
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
> パーティション分割されたコレクションの場合、既定のドキュメント キーは Azure Cosmos DB の `_rid` プロパティですが、フィールド名の先頭にはアンダースコア文字を使用できないため、この名前は Azure Search によって `rid` に自動的に変更されます。 また、Azure Cosmos DB の `_rid` 値には、Azure Search キーでは無効な文字が含まれています。 そのため、`_rid` 値は Base64 でエンコードされます。
> 
> MongoDB コレクションの場合、`_id` プロパティ名は、Azure Search によって `doc_id` に自動的に変更されます。  

### <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>JSON データ型と Azure Search データ型間のマッピング
| JSON データ型 | 互換性のあるターゲット インデックス フィールドの型 |
| --- | --- |
| Bool |Edm.Boolean、Edm.String |
| 整数などの数値 |Edm.Int32、Edm.Int64、Edm.String |
| 浮動小数点などの数値 |Edm.Double、Edm.String |
| String |Edm.String |
| プリミティブ型の配列。例: ["a"、"b"、"c"] |Collection(Edm.String) |
| 日付などの文字列 |Edm.DateTimeOffset、Edm.String |
| GeoJSON オブジェクト。例: { "type": "Point", "coordinates": [long, lat] } |Edm.GeographyPoint |
| その他の JSON オブジェクト |該当なし |

### <a name="4---configure-and-run-the-indexer"></a>4 - インデクサーを構成して実行する

インデックスとデータ ソースを作成したら、インデクサーを作成できます。

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
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

## <a name="use-net"></a>.NET の使用

.NET SDK は REST API と完全に同等です。 前の REST API セクションを確認し、概念、ワークフロー、要件を理解することをお勧めします。 その後は、次の .NET API リファレンス ドキュメントを参照して、マネージド コードで JSON インデクサーを実装できます。

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>変更されたドキュメントのインデックス作成

データ変更の検出ポリシーの目的は、変更されたデータ項目を効率的に識別することです。 現在、唯一サポートされているポリシーは、次に示すように、Azure Cosmos DB によって指定される `_ts` (タイムスタンプ) プロパティを使用した `High Water Mark` ポリシーです。

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

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
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

## <a name="watch-this-video"></a>次の動画をご覧ください

若干古いこの 7 分のビデオでは、Azure Cosmos DB のプログラム マネージャーである Andrew Liu が、Azure Search インデックスを Azure Cosmos DB コンテナーに追加する方法を紹介しています。 ビデオに表示されるポータル ページは古くなっていますが、情報はまだ古くなっていません。

>[!VIDEO https://www.youtube.com/embed/OyoYu1Wzk4w]

## <a name="NextSteps"></a>次のステップ

お疲れさまでした。 インデクサーを使用して、Azure Cosmos DB を Azure Search と統合する方法についての説明は以上で終了です。

* Azure Cosmos DB の詳細については、[Azure Cosmos DB サービスのページ](https://azure.microsoft.com/services/cosmos-db/)を参照してください。
* Azure Search について詳しくは、[Search サービス ページ](https://azure.microsoft.com/services/search/)をご覧ください。
