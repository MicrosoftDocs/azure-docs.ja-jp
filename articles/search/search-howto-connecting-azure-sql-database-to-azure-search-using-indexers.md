---
title: Azure SQL Database に接続し、インデクサーを使用してコンテンツのインデックスを作成する - Azure Search
description: Azure Search でのフルテキスト検索用のインデクサーを使用して Azure SQL Database 内でデータをクロールする方法について説明します。 この記事では、接続、インデクサーの構成、およびデータの取り込みについて説明します。
ms.date: 03/01/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 5453bcdd371c0639cb1d3568f05a1768e6204d3d
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2019
ms.locfileid: "57315216"
---
# <a name="connect-to-and-index-azure-sql-database-content-using-azure-search-indexers"></a>Azure SQL Database に接続し、Azure Search インデクサーを使用してコンテンツのインデックスを作成する

[Azure Search インデックス](search-what-is-an-index.md)を照会するには、先に Azure Search インデックスにデータを入力する必要があります。 データが Azure SQL Database に存在する場合は、**Azure SQL Database 用 Azure Search インデクサー** (**Azure SQL インデクサー**) でインデックス作成プロセスを自動化できます。これは、記述するコードと対処するインフラストラクチャが減ることを意味します。

この記事では[インデクサー](search-indexer-overview.md)使用のしくみだけでなく、Azure SQL データベースでのみ使用できる機能 (統合された変更追跡など) についても説明します。 

Azure SQL データベースだけでなく、Azure Searchでは [Azure Cosmos DB](search-howto-index-cosmosdb.md)、[Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)、[Azure Table Storage](search-howto-indexing-azure-tables.md) 用のインデクサーも用意されています。 その他のデータ ソースについてのサポートを希望する場合は、[Azure Search フィードバック フォーラム](https://feedback.azure.com/forums/263029-azure-search/)でフィードバックをお寄せください。

## <a name="indexers-and-data-sources"></a>インデクサーとデータ ソース

**データ ソース**では、インデックスを作成するデータ、データにアクセスするための資格情報、およびデータの変更 (新しい行、変更された行、削除された行) を効率よく識別するためのポリシーを指定します。 データ ソースは複数のインデクサーで使用できるように独立したリソースとして定義します。

**インデクサー**は、単一のデータ ソースと検索対象のインデックスをつなげるリソースです。 インデクサーは次のように使用されます。

* 1 回限りのデータのコピーを実行して、インデックスを作成する。
* スケジュールに従ってデータ ソースの変更とインデックスを更新する。
* 必要に応じて実行し、インデックスを更新する。

1 つのインデクサーで使用できるのは、1 つのテーブルまたはビューのみですが、複数の検索インデックスを設定する必要がある場合は、複数のインデクサーを作成することができます。 概念について詳しくは、[インデクサー操作の一般的なワークフロー](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations#typical-workflow)に関する記事をご覧ください。

Azure SQL インデクサーのセットアップと構成には次を使用できます。

* [Azure Portal](https://portal.azure.com) のデータのインポート ウィザード
* Azure Search [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)
* Azure Search [REST API](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)

この記事では、REST API を使用して、**インデクサー**と**データソース**を作成します。

## <a name="when-to-use-azure-sql-indexer"></a>Azure SQL インデクサーの使用に適した場合
データに関連する複数の要因により、Azure SQL インデクサーを使用するのが適切な場合と、適切ではない場合があります。 データが次の要件を満たす場合、Azure SQL インデクサーを使用できます。

| 条件 | 詳細 |
|----------|---------|
| データが単一のテーブルまたはビューのものである | データが複数のテーブルに分散している場合は、データの 1 つのビューを作成できます。 ただし、ビューを使用する場合は、SQL Server の統合変更検出を使用して、インデックスを増分変更で更新することはできません。 詳細については、後の「[新しい行、変更された行、削除された行の取得](#CaptureChangedRows)」を参照してください。 |
| データ型に互換性がある | Azure Search インデックスでは、SQL のほとんどの型がサポートされていますが、すべてではありません。 一覧については、「[データ型間のマッピング](#TypeMapping)」を参照してください。 |
| リアルタイムのデータ同期は必要ない | インデクサーがテーブルのインデックスを作成し直すのに、最大で 5 分かかります。 データが頻繁に変更され、秒単位または 1 分以内に変更をインデックスに反映する必要がある場合は、[REST API](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) または [.NET SDK](search-import-data-dotnet.md) を使用して、更新された行を直接プッシュすることをお勧めします。 |
| インデックスの増分作成が可能 | データ セットが大きく、スケジュールに従ってインデクサーを実行する場合は、Azure Search により、新しい、変更された、または削除された行を効率的に特定できます。 非増分のインデックス作成は、オンデマンドでインデックスを作成 (スケジュールされていない) するか、100,000 未満の行のインデックスを作成する場合にのみ使用できます。 詳細については、後の「[新しい行、変更された行、削除された行の取得](#CaptureChangedRows)」を参照してください。 |

> [!NOTE] 
> Azure Search では SQL Server 認証のみがサポートされています。 Azure Active Directory パスワード認証のサポートが必要な場合は、こちらの [UserVoice の提案](https://feedback.azure.com/forums/263029-azure-search/suggestions/33595465-support-azure-active-directory-password-authentica)に投票してください。

## <a name="create-an-azure-sql-indexer"></a>Azure SQL インデクサーの作成

1. データ ソースを作成します。

   ```
    POST https://myservice.search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }
   ```

   [Azure ポータル](https://portal.azure.com)から接続文字列を取得し、`ADO.NET connection string` オプションを使用できます。

2. まだない場合は、ターゲットの Azure Search インデックスを作成します。 インデックスを作成するには、[ポータル](https://portal.azure.com)または[インデックス作成 API](https://docs.microsoft.com/rest/api/searchservice/Create-Index) を使用します。 ターゲット インデックスのスキーマとソース テーブルのスキーマに互換性があることを確認します。「[SQL データ型と Azure Search データ型間のマッピング](#TypeMapping)」を参照してください。

3. 名前を指定し、データ ソースとターゲット インデックスを参照することによって、インデクサーを作成します。

    ```
    POST https://myservice.search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }
    ```

この方法で作成されたインデクサーには、スケジュールがありません。 作成すると自動的に 1 回実行されます。 **インデクサー実行** 要求を使用して、いつでも再び実行できます。

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2017-11-11
    api-key: admin-key

インデクサーの動作の一部 (バッチ サイズ、インデクサーの実行が失敗する前にスキップできるドキュメントの数など) をカスタマイズできます。 詳細については、[インデクサーの作成 API](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer) に関するページをご覧ください。

Azure サービスにデータベースへの接続を許可することが必要な場合があります。 方法については、「 [Azure からの接続](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) 」を参照してください。

インデクサーの状態および実行履歴 (インデックスを作成された項目の数、エラーなど) を監視するには、 **インデクサーの状態** 要求を使用します。

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2017-11-11
    api-key: admin-key

応答は、次のようになります。

    {
        "\@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null
            },
            ... earlier history items
        ]
    }

実行履歴には、最近完了した 50 件の実行内容が含まれ、時系列の逆の順に並べられます (そのため、最近の実行内容が応答の最初に表示されます)。
応答の詳細については、「 [Get Indexer Status (インデクサーの状態の取得)](https://go.microsoft.com/fwlink/p/?LinkId=528198)

## <a name="run-indexers-on-a-schedule"></a>スケジュールに従ったインデクサーの実行
スケジュールに従って定期的に実行するようにインデクサーを調整することもできます。 そのためには、インデクサーを作成または更新するときに、**schedule** プロパティを追加します。 次の例では、インデクサーを更新する PUT 要求を示します。

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2017-11-11
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

**interval** パラメーターは必須です。 interval は、連続する 2 つのインデクサー実行の開始の時間間隔を示します。 許可される最短の間隔は 5 分です。最長は 1 日です。 XSD "dayTimeDuration" 値 ([ISO 8601 期間](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)値の制限されたサブセット) として書式設定する必要があります。 使用されるパターンは、`P(nD)(T(nH)(nM))` です。 たとえば、15 分ごとの場合は `PT15M`、2 時間ごとの場合は `PT2H` です。

省略可能な **startTime** は、スケジュールされた実行を開始するときを示します。 省略すると、現在の UTC 時刻が使用されます。 この時刻は過去でもかまいません。その場合、最初の実行はインデクサーが startTime から継続的に実行されているかのようにスケジュールされます。  

特定のインデクサーを実行できるのは一度に 1 つだけです。 インデクサーの開始予定時間にそのインデクサーが既に実行されている場合、その実行は、次回予定されている時間まで延期されます。

さらに具体的な例を示します。 次のような時間単位のスケジュールを構成したものとします。

    "schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

これは次のように動作します。

1. 最初のインデクサーの実行は、UTC で 2015 年 3 月 1 日午前 12 時前後に開始します 。
2. この実行に 20 分 (または、1 時間未満) かかるものとします。
3. 第 2 の実行は、2015 年 3 月 1 日午前 1 時前後に開始します。
4. この実行に 1 時間以上かかるものとします。たとえば、70 分かかって、午前 2 時 10 分前後に終了します。
5. 今は午前 2 時で、3 番目の実行が開始する時刻です。 ただし、午前 1 時からの 2 番目の実行が 継続中のため、3 番目の実行はスキップされます。 3 番目の実行は午前 3 時に開始します。

**PUT インデクサー** 要求を使用することで、既存のインデクサーのスケジュールを追加、変更、削除できます。

<a name="CaptureChangedRows"></a>

## <a name="capture-new-changed-and-deleted-rows"></a>新しい行、変更された行、削除された行の取得

Azure Search では**増分インデックス作成**を使用することで、インデクサーを実行するたびにテーブルまたはビュー全体のインデックスを再作成する必要を回避できます。 Azure Search は、増分インデックス作成をサポートする 2 つの変更検出ポリシーを備えています。 

### <a name="sql-integrated-change-tracking-policy"></a>SQL 統合変更追跡ポリシー
SQL データベースが [変更追跡](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server)をサポートする場合、 **SQL 統合変更追跡ポリシー**の使用が推奨されます。 これは最も効率的なポリシーです。 また、テーブルに明示的な "論理削除" 列を追加しなくても、Azure Search で削除済み行を特定できます。

#### <a name="requirements"></a>必要条件 

+ データベースのバージョンの要件:
  * Azure VM で SQL Server を使用している場合は、SQL Server 2012 SP3 以降。
  * Azure SQL Database を使用している場合は、Azure SQL Database V12。
+ テーブルのみ (ビューなし)。 
+ データベースでテーブルの[変更の追跡を有効にしている](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server)。 
+ テーブルに複合主キー (複数の列を含む主キー) がない。  

#### <a name="usage"></a>使用法

このポリシーを使用するには、データ ソースを次のように作成または更新します。

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }

SQL 統合変更追跡ポリシーを使用するときは、個別のデータ削除検出ポリシーを指定しないでください。個別のデータ削除ポリシーには、削除された行を識別するためのサポートが組み込まれています。 ただし、削除が "自動的に" 検出されるためには、検索インデックスのドキュメント キーが SQL テーブルの主キーと同じである必要があります。 

> [!NOTE]  
> [TRUNCATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/truncate-table-transact-sql) を使用して SQL テーブルから多数の行を削除するときは、インデクサーを[リセット](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)して変更追跡状態をリセットし、行の削除を取得する必要があります。

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>高基準値変更検出ポリシー

この変更検出ポリシーは、行が最後に更新されたときのバージョンまたは時刻を取得する "高基準" 列に依存します。 ビューを使う場合は、高基準ポリシーを使用する必要があります。 高基準列では、次の要件を満たす必要があります。

#### <a name="requirements"></a>必要条件 

* すべての挿入は列の値を指定します。
* 項目を更新すると、列の値も変更されます。
* この列の値は挿入または更新のたびに増加します。
* 次の WHERE 句および ORDER BY 句のあるクエリを効率的に実行できます。`WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

> [!IMPORTANT] 
> 高基準列には、[rowversion](https://docs.microsoft.com/sql/t-sql/data-types/rowversion-transact-sql) データ型を使用することを強くお勧めします。 その他のデータ型を使用した場合、インデクサー クエリと同時に実行されるトランザクションが存在するところでは、変更の追跡でのすべての変更の捕捉は保証されません。 読み取り専用のレプリカのある構成で **rowversion** を使用する場合、インデクサーはプライマリ レプリカをポイントする必要があります。 プライマリ レプリカを使用できるのは、データ同期のシナリオのみです。

#### <a name="usage"></a>使用法

高基準値ポリシーを使用するには、データ ソースを次のように作成または更新します。

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a rowversion or last_updated column name]"
      }
    }

> [!WARNING]
> ソース テーブルの高基準列にインデックスがない場合、SQL インデクサーが使用するクエリはタイムアウトになる可能性があります。具体的には、多くの行が含まれるテーブルに対してクエリを効率的に実行するには、`ORDER BY [High Water Mark Column]` 句にはインデックスが必要です。
>
>

タイムアウト エラーを発生した場合は、`queryTimeout` インデクサー構成設定を使用すると、クエリのタイムアウトを、既定の 5 分よりも大きい値に設定できます。 たとえば、タイムアウトを 10 分に設定するには、次の構成でインデクサーを作成または更新します。

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

`ORDER BY [High Water Mark Column]` 句を無効にすることもできます。 ただし、これはお勧めしません。インデクサーの実行がエラーで中断された場合、その時点でほぼすべての行の処理が完了していても、そのインデクサーが後で実行されたときに、すべての行を再処理しなければならないためです。 `ORDER BY` 句を無効にするには、インデクサー定義で `disableOrderByHighWaterMarkColumn` 設定を使用します。  

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>論理削除列削除検出ポリシー
ソース テーブルから行が削除されると、おあそらく検索インデックスからも同様に行を削除する必要があります。 SQL 統合変更追跡ポリシーを使用する場合、これは自動的に行われます。 ただし、高基準変更追跡ポリシーでは行は削除されません。 どうすればよいでしょうか。

行がテーブルから物理的に削除されている場合、Azure Search では、存在しなくなったレコードの存在を推論することはできません。  ただし、"論理削除" テクニックを使用すると、行をテーブルから削除せずに論理的に削除できます。 このテクニックでは、列をテーブルまたはビューに追加し、その列を使用して行を削除済みとしてマークします。

論理削除手法を使用する場合は、データ ソースを作成または更新するときに、次のような論理削除ポリシーを指定できます。

    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }

**softDeleteMarkerValue** は、文字列にする必要があります。実際の値の文字列表現を使用してください。 たとえば、削除された行が値 1 でマークされている整数列がある場合は、`"1"` を使用します。 削除された行が BIT 列でブール型の true 値でマークされている場合は、文字列リテラル `True` または `true` を使用します。大文字/小文字は区別されません。

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-and-azure-search-data-types"></a>SQL データ型と Azure Search データ型間のマッピング
| SQL データ型 | ターゲット インデックス フィールドに許可される型 | メモ |
| --- | --- | --- |
| ビット |Edm.Boolean、Edm.String | |
| int、smallint、tinyint |Edm.Int32、Edm.Int64、Edm.String | |
| bigint |Edm.Int64、Edm.String | |
| real、float |Edm.Double、Edm.String | |
| smallmoney、money decimal numeric |Edm.String |Azure Search では、小数点を Edm.Double に変換できません。精度が失われるためです。 |
| char、nchar、varchar、nvarchar |Edm.String<br/>Collection(Edm.String) |SQL 文字列が JSON 文字列配列 `["red", "white", "blue"]` を表している場合、その SQL 文字列を使用して、Collection(Edm.String) フィールドを設定できます |
| smalldatetime、datetime、datetime2、date、datetimeoffset |Edm.DateTimeOffset、Edm.String | |
| uniqueidentifer |Edm.String | |
| 地理 |Edm.GeographyPoint |型が POINT で SRID が 4326 (既定) の地理インスタンスのみがサポートされます。 |
| rowversion |該当なし |行バージョン列は検索インデックスに保存できませんが、変更追跡に利用できます。 |
| time、timespan、binary、varbinary、image、xml、geometry、CLR 型 |該当なし |サポートされていません |

## <a name="configuration-settings"></a>構成設定
SQL インデクサーが公開している構成設定をいくつか次に示します。

| Setting | データ型 | 目的 | 既定値 |
| --- | --- | --- | --- |
| queryTimeout |文字列 |SQL クエリ実行のタイムアウトを設定します |5 分 ("00:05:00") |
| disableOrderByHighWaterMarkColumn |bool |高基準ポリシーが使用する SQL クエリで ORDER BY 句が省略されます。 [高基準値ポリシー](#HighWaterMarkPolicy)に関するセクションをご覧ください |false |

こうした設定は、インデクサー定義の `parameters.configuration` オブジェクトで使用されます。 たとえば、クエリのタイムアウトを 10 分に設定するには、次の構成でインデクサーを作成または更新します。

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

## <a name="faq"></a>FAQ

**Q: Azure 上の IaaS VM で実行される SQL データベースで Azure SQL インデクサーを使用できますか?**

はい。 ただし、Search サービスに対してデータベースへの接続を許可する必要があります。 詳細については、「[Azure VM での Azure Search インデクサーから SQL Server への接続の構成](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)」を参照してください。

**Q: オンプレミスで実行される SQL データベースで Azure SQL インデクサーを使用できますか?**

直接無効にすることはできません。 直接接続は推奨もサポートもされません。これを行うには、インターネット トラフィックに対してデータベースを開く必要があります。 Azure Data Factory などのブリッジ テクノロジを使用してこのシナリオで成功した事例があります。 詳しくは、「[Azure Data Factory を使用して Azure Search インデックスにデータをプッシュする](https://docs.microsoft.com/azure/data-factory/data-factory-azure-search-connector)」を参照してください。

**Q: Azure 上の IaaS で実行される SQL Server 以外のデータベースで Azure SQL インデクサーを使用できますか?**

いいえ。 SQL Server 以外のデータベースではインデクサーをテストしていないので、このシナリオはサポートされません。  

**Q: スケジュールに従って実行される複数のインデクサーを作成できますか?**

はい。 ただし、1 つのノードで一度に実行できるインデクサーは 1 つだけです。 複数のインデクサーを同時に実行する必要がある場合は、複数の検索単位に検索サービスを拡大することを検討してください。

**Q: インデクサーを実行するとクエリのワークロードに影響しますか?**

はい。 インデクサーは検索サービス内のノードの 1 つで実行し、そのノードのリソースは、インデックスの作成、クエリ トラフィックの提供、およびその他の API 要求で共有されます。 大量のインデックス作成とクエリ ワークロードを実行し、503 エラーが高率で発生するか、または応答時間が長くなる場合は、[検索サービスのスケールアップ](search-capacity-planning.md)を検討してください。

**Q: [フェールオーバー クラスター](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview)でデータ ソースとしてセカンダリ レプリカを使用できますか?**

一概には言えません。 テーブルまたはビューのインデックスの完全作成の場合、セカンダリ レプリカを使用できます。 

Azure Search では、増分インデックス作成用に、SQL 統合変更追跡と高基準値という 2 つの変更検出ポリシーがサポートされています。

読み取り専用のレプリカでは、SQL データベースは統合変更追跡に対応しません。 そのため、高基準値ポリシーを使用する必要があります。 

標準推奨事項として、高基準列には、rowversion データ型を使用することをお勧めします。 ただし、rowversion の使用は、読み取り専用のレプリカでサポートされていない、SQL Database の `MIN_ACTIVE_ROWVERSION` 関数に依存します。 そのため、rowversion を使用する場合は、インデクサーはプライマリ レプリカを指す必要があります。

読み取り専用レプリカで rowversion の使用を試みると、次のようなエラーが表示されます。 

    "Using a rowversion column for change tracking is not supported on secondary (read-only) availability replicas. Please update the datasource and specify a connection to the primary availability replica.Current database 'Updateability' property is 'READ_ONLY'".

**Q: 高基準変更追跡に代替の非 rowversion 列を使用することはできますか?**

それはお勧めしません。 信頼性の高いデータ同期を実行できるのは、**rowversion** のみです。 ただし、アプリケーション ロジックによっては、次の条件を満たせば、その信頼性が高まる可能性があります。

+ インデクサーの実行時に、インデックスが作成されるテーブルに未処理のトランザクションがない (すべてのテーブルの更新がスケジュールに従ってバッチ操作として実行され、Azure Search インデクサーのスケジュールがテーブル更新スケジュールと重ならないように設定されているなど)。  

+ 列が実行されないことを防ぐために、インデックスの完全な再作成を定期的に実行している。 