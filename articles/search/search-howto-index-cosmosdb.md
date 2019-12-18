---
title: Azure Cosmos DB データを検索する
titleSuffix: Azure Cognitive Search
description: Azure Cosmos DB のデータ ソースをクロールし、Azure Cognitive Search のフルテキスト検索可能なインデックスにデータを取り込みます。 インデクサーにより、選択したデータ ソース (Azure Cosmos DB など) のデータ インジェストが自動化されます。
author: mgottein
manager: nitinme
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 616e5dc5ac6416d2efe1d9338b99c2b400fe572a
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977116"
---
# <a name="how-to-index-cosmos-db-data-using-an-indexer-in-azure-cognitive-search"></a>Azure Cognitive Search でインデクサーを使用して Cosmos DB データのインデックスを作成する方法 

> [!IMPORTANT] 
> SQL API は一般提供されています。
> MongoDB API、Gremlin API、Cassandra API のサポートは現在、パブリック プレビューの段階です。 プレビュー段階の機能はサービス レベル アグリーメントなしで提供しています。運用環境のワークロードに使用することはお勧めできません。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。 プレビュー機能に対するアクセスの要求は、[こちらのフォーム](https://aka.ms/azure-cognitive-search/indexer-preview)に必要事項を入力して行うことができます。 プレビュー機能は [REST API バージョン 2019-05-06-Preview](search-api-preview.md) で提供しています。 現時点でポータルによるサポートは一部のみにとどまります。また、.NET SDK によるサポートはありません。

> [!WARNING]
> Azure Cognitive Search でサポートされるのは、[インデックス作成ポリシー](https://docs.microsoft.com/azure/cosmos-db/index-policy)が [[常に]](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode) に設定されている Cosmos DB コレクションだけです。 [遅延] インデックス作成ポリシーを使用したコレクションのインデックス作成はお勧めできず、データが失われることがあります。 インデックス作成が無効になっているコレクションはサポートされません。

この記事では、コンテンツを抽出して Azure Cognitive Search で検索できるようにするために Azure Cosmos DB [インデクサー](search-indexer-overview.md)を構成する方法を説明します。 このワークフローでは、Azure Cognitive Search インデックスを作成して、Azure Cosmos DB から抽出された既存のテキストと共に読み込みます。 

用語が混乱を招く可能性があるため、[Azure Cosmos DB のインデックス作成](https://docs.microsoft.com/azure/cosmos-db/index-overview)と [Azure Cognitive Search のインデックス作成](search-what-is-an-index.md)はそれぞれのサービスに固有の異なる操作であることに注意してください。 Azure Cognitive Search のインデックス作成を開始するには、Azure Cosmos DB データベースが既に存在していて、データが格納されている必要があります。

Azure Cognitive Search の Cosmos DB インデクサーでは、アクセスのためのプロトコルがさまざまに異なる [Azure Cosmos DB 項目](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items)をクロールできます。

+ [SQL API](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference) (一般提供開始済み) については、[ポータル](#cosmos-indexer-portal)、[REST API](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)、または [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet) を利用できます。

+ [MongoDB API (プレビュー)](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction) と [Gremlin API (プレビュー)](https://docs.microsoft.com/azure/cosmos-db/graph-introduction) については、[ポータル](#cosmos-indexer-portal)を使用する方法と、インデクサー作成のための[インデクサーの作成 (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer) 呼び出しで [REST API バージョン 2019-05-06-Preview](search-api-preview.md) を使用する方法のいずれかを利用できます。

+ [Cassandra API (プレビュー)](https://docs.microsoft.com/azure/cosmos-db/cassandra-introduction) については、[インデクサーの作成 (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer) 呼び出しで [REST API バージョン 2019-05-06-Preview](search-api-preview.md) を使用する方法のみ利用できます。


> [!Note]
> Azure Cognitive Search で Table API をサポートしてほしいという方は、[こちら](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab)のユーザーの意見に賛成の票を投じてください。
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>ポータルの使用

> [!Note]
> ポータルでは現在、SQL API と MongoDB API (プレビュー) をサポートしています。

Azure Cosmos DB 項目のインデックスを作成する最も簡単な方法は、[Azure portal](https://portal.azure.com/) 内のウィザードを使用することです。 コンテナー内のデータをサンプリングしてメタデータを解析することにより、Azure Cognitive Search の[**データのインポート**](search-import-data-portal.md)ウィザードでは、既定のインデックスを作成し、ソース フィールドをターゲット インデックス フィールドにマップし、1 回の操作でインデックスを読み込むことができます。 ソース データのサイズと複雑さによっては、数分で運用可能なフルテキスト検索インデックスを作成できます。

待機時間を短くし、帯域幅の課金を回避するために、Azure Cognitive Search と Azure Cosmos DB の両方に同じリージョンまたは場所を使用することをお勧めします。

### <a name="1---prepare-source-data"></a>1 - ソース データを準備する

Cosmos DB アカウント、SQL API、MongoDB API (プレビュー)、または Gremlin API (プレビュー) にマップされた Azure Cosmos DB データベース、およびデータベース内のコンテンツが必要です。

Cosmos DB データベースにデータが含まれることを確認してください。 [データのインポート ウィザード](search-import-data-portal.md)では、メタデータが読み取られ、インデックス スキーマを推論するためにデータのサンプリングが実行されますが、Cosmos DB からもデータが読み込まれます。 このデータが見つからない場合、ウィザードは "データ ソースからインデックス スキーマを削除するときにエラーが発生しました:データソース 'emptycollection' がデータを返さなかったため、プロトタイプ インデックスを構築できませんでした" というエラーで停止します。

### <a name="2---start-import-data-wizard"></a>2 - データのインポート ウィザードを開始する

Azure Cognitive Search サービス ページのコマンド バーから[ウィザードを開始](search-import-data-portal.md)できます。このほか、Cosmos DB SQL API に接続する場合には、Cosmos DB アカウントの左側のナビゲーション ペインにある **[設定]** セクションで **[Add Azure Cognitive Search]\(Azure Cognitive Search の追加\)** をクリックする方法も利用できます。

   ![ポータルの [データのインポート] コマンド](./media/search-import-data-portal/import-data-cmd2.png "データのインポート ウィザードを開始する")

### <a name="3---set-the-data-source"></a>3 - データ ソースを設定する

**データソース**のページでは、ソースが次のように指定された **Cosmos DB** になっている必要があります。

+ **[名前]** は データ ソースの名前です。 作成されたら、その他のワークロード用に選択できます。

+ **[Cosmos DB アカウント]** は、`AccountEndpoint` と `AccountKey` を含む、Cosmos DB からのプライマリまたはセカンダリの接続文字列である必要があります。 MongoDB コレクションの場合には、接続文字列末尾に **ApiKind=MongoDb** を追加します。この文字列と接続文字列の間にはセミコロンを挿入してください。 Gremlin API と Cassandra API の場合には、[REST API](#cosmosdb-indexer-rest) 向けの手順を使用します。

+ **[データベース]** は、アカウントからの既存のデータベースです。 

+ **[コレクション]** はドキュメントのコンテナーです。 インポートが成功するためには、ドキュメントが存在している必要があります。 

+ **[クエリ]** は、すべてのドキュメントが必要な場合は空にすることができます。それ以外の場合は、ドキュメントのサブセットを選択するクエリを入力できます。 **[クエリ]** は SQL API の場合にのみ利用できます。

   ![Cosmos DB データ ソースの定義](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Cosmos DB データ ソースの定義")

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4 - ウィザードの [コンテンツのエンリッチ] ページをスキップする

コグニティブ スキル (またはエンリッチメント) の追加は、インポート要件ではありません。 特別に [AI エンリッチメントを追加する](cognitive-search-concept-intro.md)必要がない場合は、この手順を省略してください。

手順をスキップするには、ページの下部にある [次へ] と [スキップ] の青いボタンをクリックします。

### <a name="5---set-index-attributes"></a>5 - インデックスの属性を設定する

**[インデックス]** ページには、フィールドとデータ型およびインデックスの属性を設定するための一連のチェック ボックスが一覧表示されます。 このウィザードで、メタデータに基づいてソース データをサンプリングすることで、フィールドの一覧を生成できます。 

属性列の上部にあるチェック ボックスをクリックすることで、属性を一括選択できます。 クライアント アプリに返してフル検索処理の対象にする必要があるすべてのフィールドで、 **[取得可能]** と **[検索可能]** を選択します。 整数はフルテキスト検索もあいまい検索もできないことに注意してください (数値は逐語的に評価され、多くの場合フィルターで役立ちます)。

詳細については、[インデックス属性](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib)と[言語アナライザー](https://docs.microsoft.com/rest/api/searchservice/language-support)の説明を参照してください。 

時間をかけて選択内容を確認します。 ウィザードを実行すると、物理データ構造が作成されて、すべてのオブジェクトを削除して再作成しない限り、これらのフィールドを編集することはできません。

   ![Cosmos DB インデックスの定義](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Cosmos DB インデックスの定義")

### <a name="6---create-indexer"></a>6 - インデクサーを作成する

完全に指定すると、ウィザードによって検索サービスに 3 つの異なるオブジェクトが作成されます。 データ ソース オブジェクトとインデックス オブジェクトは、Azure Cognitive Search サービスに名前付きリソースとして保存されます。 最後のステップでは、インデクサー オブジェクトが作成されます。 インデクサーに名前を付けると、インデクサーはスタンドアロン リソースとして存在することができ、同じウィザードのシーケンスで作成されるインデックス オブジェクトやデータ ソース オブジェクトとは独立して、スケジュールを設定したり管理したりできます。

インデクサーに慣れていないユーザーのために説明すると、"*インデクサー*" とは Azure Cognitive Search のリソースであり、外部データ ソースで検索可能なコンテンツのクロールを行います。 **データのインポート** ウィザードで出力されるインデクサーでは、Cosmos DB データ ソースがクロールされ、検索可能なコンテンツが抽出されて、Azure Cognitive Search のインデックスにインポートされます。

次のスクリーンショットは、既定のインデクサー構成を示しています。 インデクサーを 1 回だけ実行する場合は、 **[1 度]** に切り替えることができます。 **[送信]** をクリックしてウィザードを実行し、すべてのオブジェクトを作成します。 インデックスの作成はすぐに開始されます。

   ![Cosmos DB インデクサーの定義](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Cosmos DB インデクサーの定義")

ポータルのページでデータのインポートを監視することができます。 進行状況の通知では、インデックス作成の状態と、アップロードされたドキュメントの数が示されます。 

インデックスの作成が完了したら、[Search エクスプローラー](search-explorer.md)を使用してインデックスのクエリを実行できます。

> [!NOTE]
> 期待どおりのデータが表示されない場合は、その他のフィールドにその他の属性を設定する必要があります。 今作成したインデックスとインデクサーを削除し、もう一度ウィザードの手順に従って、手順 5 でインデックス属性の選択内容を変更してください。 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>REST API の使用

REST API を使用して、Azure Cognitive Search のすべてのインデクサーに共通する 3 部構成のワークフロー (データ ソースを作成し、インデックスを作成して、インデクサーを作成する) で、Azure Cosmos DB データのインデックスを作成できます。 Cosmos DB からのデータ抽出は、インデクサーの作成要求を送信した時点で発生します。 この要求が完了すると、クエリ可能なインデックスが作成されます。 

> [!NOTE]
> Cosmos DB Gremlin API または Cosmos DB Cassandra API からデータのインデックスを作成するためには、まず[こちらのフォーム](https://aka.ms/azure-cognitive-search/indexer-preview)に必要事項を入力し、限定プレビュー機能に対するアクセスを要求する必要があります。 要求が処理されると、[REST API バージョン 2019-05-06-Preview](search-api-preview.md) を使ってデータ ソースを作成する手順の案内が届きます。

この記事の前の方で、[Azure Cosmos DB のインデックス作成](https://docs.microsoft.com/azure/cosmos-db/index-overview)と [Azure Cognitive Search のインデックス作成](search-what-is-an-index.md)が異なる操作であると説明しました。 Cosmos DB のインデックス作成では、既定ですべてのドキュメントのインデックスが自動的に作成されます (Cassandra API の場合を除きます)。 インデックスの自動作成を無効にすると、ドキュメント ID を使用したクエリまたは自己リンクでのみドキュメントにアクセスできるようになります。 Azure Cognitive Search のインデックス作成では、Azure Cognitive Search によってインデックスが作成されるコレクションで、Cosmos DB の自動インデックス作成が有効になっている必要があります。 Cosmos DB Cassandra API インデクサーのプレビューにサインアップした場合には、Cosmos DB のインデックス作成の設定手順に関する案内が届きます。

> [!WARNING]
> Azure Cosmos DB とは、次世代の DocumentDB です。 以前の API バージョン **2017-11-11** では、`documentdb` 構文を使用できました。 つまり、データ ソースの種類を `cosmosdb` または `documentdb` として指定することができました。 API バージョン **2019-05-06** 以降では、この記事で説明するように、Azure Cognitive Search API とポータルの両方で `cosmosdb` 構文のみがサポートされるようになりました。 つまり、Cosmos DB エンドポイントに接続する場合場合、データ ソースの種類は `cosmosdb` でなければなりません。

### <a name="1---assemble-inputs-for-the-request"></a>1 - 要求に対する入力をアセンブルする

要求ごとに、Azure Cognitive Search サービス名と管理者キーを (POST ヘッダーに) 指定し、BLOB ストレージのストレージ アカウント名とキーを指定する必要があります。 [Postman](search-get-started-postman.md) を使用して、Azure Cognitive Search に HTTP 要求を送信できます。

次の 4 つの値をメモ帳にコピーして、要求に貼り付けることができるようにします。

+ Azure Cognitive Search サービス名
+ Azure Cognitive Search の管理者キー
+ Cosmos DB の接続文字列

これらの値は、ポータルで調べることができます。

1. Azure Cognitive Search のポータル ページで、[概要] ページから Search サービスの URL をコピーします。

2. 左側のナビゲーション ウィンドウで **[キー]** をクリックし、プライマリまたはセカンダリのいずれかのキー (どちらも働きは同じです) をコピーします。

3. Cosmos ストレージ アカウントのポータル ページに切り替えます。 左側のナビゲーション ウィンドウで、 **[設定]** の **[キー]** をクリックします。 このページには、1 つの URI、2 組の接続文字列、および 2 組のキーが表示されます。 接続文字列のいずれかをメモ帳にコピーします。

### <a name="2---create-a-data-source"></a>2 - データ ソースを作成する

**データ ソース**は、インデックスを作成するデータ、資格情報、およびデータの変更を識別するためのポリシー (コレクション内の変更または削除されたドキュメントなど) を指定します。 データ ソースは、複数のインデクサーから使用できるように、独立したリソースとして定義します。

データ ソースを作成するには、POST 要求を作成します。

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
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
|**type**| 必須。 `cosmosdb`である必要があります。 |
|**credentials** | 必須。 Cosmos DB の接続文字列でなければなりません。<br/>SQL コレクションでは、接続文字列の形式は `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>` です<br/><br/>MongoDB コレクションの場合は、**ApiKind=MongoDb** を接続文字列に追加します。<br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/><br/>Gremlin グラフと Cassandra テーブルの場合には、[インデクサーの限定プレビュー](https://aka.ms/azure-cognitive-search/indexer-preview)にサインアップして、プレビュー機能に対するアクセス権と、資格情報の形式に関する情報を入手してください。<br/><br/>エンドポイント URL では、ポート番号の使用を避けてください。 ポート番号を含めると、Azure Cognitive Search では、Azure Cosmos DB データベースのインデックスを作成できなくなります。|
| **container** | 次の要素が含まれます。 <br/>**name**:必須。 インデックスを作成するデータベース コレクションの ID を指定します。<br/>**query**: 省略可能。 任意の JSON ドキュメントを、Azure Cognitive Search がインデックスを作成できるフラット スキーマにフラット化するクエリを指定できます。<br/>MongoDB API、Gremlin API、Cassandra API では現在、クエリがサポートされていません。 |
| **dataChangeDetectionPolicy** | 推奨。 「[変更されたドキュメントのインデックス作成](#DataChangeDetectionPolicy)」セクションを参照してください。|
|**dataDeletionDetectionPolicy** | 省略可能。 「[削除されたドキュメントのインデックス作成](#DataDeletionDetectionPolicy)」セクションを参照してください。|

### <a name="using-queries-to-shape-indexed-data"></a>インデックス作成するデータの処理にクエリを活用する
SQL クエリを指定すると、ネストされたプロパティや配列のフラット化、JSON プロパティのプロジェクション、インデックスを作成するデータのフィルター処理を行うことができます。 

> [!WARNING]
> **MongoDB API**、**Gremlin API**、**Cassandra API**については、カスタム クエリがサポートされていません。`container.query` パラメーターは null を設定するか、省略する必要があります。 カスタム クエリを使用する必要がある場合は、[ユーザーの声](https://feedback.azure.com/forums/263029-azure-search) Web サイトでお知らせください。

ドキュメントのサンプル:

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "cosmosdb", "search"]
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

まだない場合は、[ターゲットの Azure Cognitive Search インデックスを作成](/rest/api/searchservice/create-index)します。 次の例では、ID と説明のフィールドを持つインデックスを作成します。

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
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
> パーティション分割されたコレクションの場合、既定のドキュメント キーは Azure Cosmos DB の `_rid` プロパティですが、フィールド名の先頭にはアンダースコア文字を使用できないため、この名前は Azure Cognitive Search によって `rid` に自動的に変更されます。 また、Azure Cosmos DB の `_rid` 値には、Azure Cognitive Search キーでは無効な文字が含まれています。 そのため、`_rid` 値は Base64 でエンコードされます。
> 
> MongoDB コレクションの場合、`_id` プロパティ名は、Azure Cognitive Search によって `id` に自動的に変更されます。  

### <a name="mapping-between-json-data-types-and-azure-cognitive-search-data-types"></a>JSON データ型と Azure Cognitive Search データ型間のマッピング
| JSON データ型 | 互換性のあるターゲット インデックス フィールドの型 |
| --- | --- |
| Bool |Edm.Boolean、Edm.String |
| 整数などの数値 |Edm.Int32、Edm.Int64、Edm.String |
| 浮動小数点などの数値 |Edm.Double、Edm.String |
| string |Edm.String |
| プリミティブ型の配列。例: ["a"、"b"、"c"] |Collection(Edm.String) |
| 日付などの文字列 |Edm.DateTimeOffset、Edm.String |
| GeoJSON オブジェクト。例: { "type": "Point", "coordinates": [long, lat] } |Edm.GeographyPoint |
| その他の JSON オブジェクト |該当なし |

### <a name="4---configure-and-run-the-indexer"></a>4 - インデクサーを構成して実行する

インデックスとデータ ソースを作成したら、インデクサーを作成できます。

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

このインデクサーは 2 時間ごとに実行されます (スケジュールの間隔が "PT2H" に設定されています)。 インデクサーを 30 分ごとに実行するには、間隔を "PT30M" に設定します。 サポートされている最短の間隔は 5 分です。 スケジュールは省略可能です。省略した場合、インデクサーは作成時に一度だけ実行されます。 ただし、いつでもオンデマンドでインデクサーを実行できます。   

インデクサー作成 API の詳細については、「 [インデクサーの作成](https://docs.microsoft.com/rest/api/searchservice/create-indexer)」をご覧ください。

インデクサーのスケジュールの定義の詳細については、[Azure Cognitive Search のインデクサーのスケジュールを設定する方法](search-howto-schedule-indexers.md)に関する記事を参照してください。

## <a name="use-net"></a>.NET の使用

一般提供されている .NET SDK では、一般提供されている REST API による完全なパリティを保持しています。 前の REST API セクションを確認し、概念、ワークフロー、要件を理解することをお勧めします。 その後は、次の .NET API リファレンス ドキュメントを参照して、マネージド コードで JSON インデクサーを実装できます。

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>変更されたドキュメントのインデックス作成

データ変更の検出ポリシーの目的は、変更されたデータ項目を効率的に識別することです。 現在、唯一サポートされているポリシーは、次に示すように、Azure Cosmos DB によって指定される `_ts` (タイムスタンプ) プロパティを使用した [`HighWaterMarkChangeDetectionPolicy`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.highwatermarkchangedetectionpolicy) です。

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

インデクサーの優れたパフォーマンスを確保するのには、このポリシーを使用することが推奨されます。 

カスタム クエリを使用している場合、`_ts` プロパティはクエリによって投影されていることを確認します。

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>増分の進行状況とカスタム クエリ

インデックスの作成中に増分の進行状況を利用すると、一時的な障害や実行の制限時間によってインデクサーの実行が中断された場合、次の実行時に、最初からコレクション全体のインデックスを再作成するのではなく、中断したところからインデックスを作成することができます。 大規模なコレクションのインデックスを作成する場合、この機能は特に重要です。 

カスタム クエリを使用するときに増分の進行状況を有効にするには、クエリを使用して `_ts` 列で結果を並べ替えておきます。 こうすることで Azure Cognitive Search が使用する定期的なチェックポイントが作成され、エラーが発生した場合に増分の進行状況を利用できます。   

ただし状況によっては、クエリに `ORDER BY [collection alias]._ts` 句が含まれる場合でも、クエリ結果が `_ts` で並べ替えられることを Azure Cognitive Search で推論されない可能性があります。 `assumeOrderByHighWaterMarkColumn` 構成プロパティを使用して結果が並べ替えられるように Azure Cognitive Search に指示することもできます。 このヒントを指定するには、次のようにインデクサーを作成または更新します。 

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

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCosmosDbCollectionId" },
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

お疲れさまでした。 インデクサーを使用して、Azure Cosmos DB を Azure Cognitive Search と統合する方法についての説明は以上で終了です。

* Azure Cosmos DB の詳細については、[Azure Cosmos DB サービスのページ](https://azure.microsoft.com/services/cosmos-db/)を参照してください。
* Azure Cognitive Search について詳しくは、[Search サービス ページ](https://azure.microsoft.com/services/search/)をご覧ください。
