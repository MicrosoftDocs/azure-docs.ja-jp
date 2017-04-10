---
title: "インデクサー操作 (Azure Search サービス REST API: 2015-02-28-Preview) | Microsoft Docs"
description: "インデクサー操作 (Azure Search サービス REST API: 2015-02-28-Preview)"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 42b5f85c-8304-4bc7-8e1e-a9c76b8ca25a
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 11/01/2016
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: c98251147bca323d31213a102f607e995b37e0ec
ms.openlocfilehash: 801a9d0e92a248d2e9843f13cfce74b948cf0d4b


---
# <a name="indexer-operations-azure-search-service-rest-api-2015-02-28-preview"></a>インデクサー操作 (Azure Search サービス REST API: 2015-02-28-Preview)
> [!NOTE]
> この記事では、[2015-02-28-Preview REST API](search-api-2015-02-28-preview.md) のインデクサーについて説明します。 この API バージョンにより、ドキュメント抽出機能を備えた Azure BLOB ストレージ インデクサーと Azure Table Storage インデクサーのプレビュー バージョンが追加されます。また、その他の面でも改善されています。 この API は、一般公開 (GA) インデクサーもサポートします。これには、Azure SQL Database、Azure VM の SQL Server、および Azure DocumentDB のインデクサーが含まれます。
> 
> 

## <a name="overview"></a>Overview
Azure Search は一部の共通データ ソースと直接統合できます。データにインデックスを作成するためにコードを記述する必要はありません。 これをセットアップするために、Azure Search API を呼び出し、**インデクサー**と**データ ソース**を作成し、管理できます。 

**インデクサー** は、データ ソースと検索対象のインデックスをつなげるリソースです。 インデクサーは次のように使用されます。 

* 1 回限りのデータのコピーを実行して、インデックスを作成する。
* スケジュールに従ってデータ ソースの変更とインデックスを同期する。 スケジュールは、インデクサーの定義の一部です。
* 必要に応じて呼び出し、インデックスを更新する。 

**インデクサー** はインデックスを定期的に更新する場合に便利です。 インデクサー定義の一部としてインラインスケジュールを設定するか、 [インデクサー実行](#RunIndexer)を利用してオンデマンドで実行できます。 

**データ ソース** はインデックスを作成するデータとデータにアクセスするための資格情報、Azure Search を有効にしてデータの変更 (データベース テーブルの行の変更や削除など) を効率的に特定するためのポリシーを指定します。 データ ソースは複数のインデクサーで使用できるように独立したリソースとして定義します。

現在、次のデータ ソースがサポートされています。

* **Azure SQL Database** と **Azure VM の SQL Server** 該当するチュートリアルについては、[この記事](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)をご覧ください。 
* **Azure DocumentDB**。 該当するチュートリアルについては、 [この記事](search-howto-index-documentdb.md)をご覧ください。 
* 次のドキュメント形式を含む **Azure BLOB Storage**: PDF、Microsoft Office (DOCX/DOC、XSLX/XLS、PPTX/PPT、MSG)、HTML、XML、ZIP、プレーン テキスト ファイル (JSON など)。 該当するチュートリアルについては、[この記事](search-howto-indexing-azure-blob-storage.md)をご覧ください。
* **Azure テーブル ストレージ**。 該当するチュートリアルについては、 [この記事](search-howto-indexing-azure-tables.md)をご覧ください。

将来的にサポートするデータ ソースを増やす予定です。 決定項目に優先順位を付けるために、 [Azure Search フィードバックフォーラム](http://feedback.azure.com/forums/263029-azure-search)でフィードバックをご提供ください。

インデクサーとデータ ソースのリソースに関連する上限については、「 [サービスの制限](search-limits-quotas-capacity.md) 」を参照してください。

## <a name="typical-usage-flow"></a>一般的な使用フロー
特定の `data source` または `indexer` リソースに簡単な HTTP 要求 (POST、GET、PUT、DELETE) を実行することでインデクサーとデータ ソースを作成し、管理できます。

自動インデックス作成の設定は通常、4 つの段階から構成されます。

1. インデックスを作成する必要があるデータを含むデータ ソースを特定します。 Azure Search はお使いのデータ ソースに存在するデータ型の一部に対応していない場合があることにご留意ください。 一覧については、「 [サポートされているデータ型](https://msdn.microsoft.com/library/azure/dn798938.aspx) 」について参照してください。
2. スキーマがお使いのデータ ソースに対応する Azure Search インデックスを作成します。
3. 「 [データ ソースの作成](#CreateDataSource)」の説明に基づき、Azure Search データ ソースを作成します。
4. 「 [インデクサーの作成](#CreateIndexer)」の説明に基づき、Azure Search インデクサーを作成します。

ターゲット インデックスとデータ ソースの組み合わせごとにインデクサーを&1; つ作成するように設計する必要があります。 複数のインデクサーが同じインデックスに書き込みできます。複数のインデクサーに同じデータ ソースを再利用できます。 ただし、インデクサーが&1; 回に利用できるデータ ソースは&1; つだけです。そして、書き込めるインデックスは&1; つだけです。 

インデクサーを作成したら、 [インデクサー状態の取得](#GetIndexerStatus) 操作でその実行状態を取得できます。 また、 [インデクサーの実行](#RunIndexer) 操作を使用すれば、(スケジュールに基づき定期的に実行する代わりに、あるいは定期的な実行に加えて) いつでもインデクサーを実行できます。

<!-- MSDN has 2 art files plus a API topic link list -->


## <a name="create-data-source"></a>データ ソースの作成
Azure Search では、インデクサーでデータ ソースが使用され、ターゲット インデックスのアドホックまたはスケジュールされたデータ更新用の接続情報が提供されます。 HTTP POST 要求を実行し、Azure Search サービス内に新しいデータ ソースを作成できます。

    POST https://[service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

または、PUT を使用し、URI でデータ ソース名を指定できます。 データ ソースが存在しない場合、作成されます。

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]

> [!NOTE]
> 許可されるデータ ソースの最大数は価格レベルによって異なります。 無料サービスの場合、最大 3 つのデータ ソースが許可されます。 Standard サービスの場合、50 のデータ ソースが許可されます。 詳細については、「 [サービスの制限](search-limits-quotas-capacity.md) 」を参照してください。
> 
> 

**要求**

HTTPS はすべてのサービス要求に必要です。 **データ ソースの作成** 要求は POST メソッドと PUT メソッドのいずれかを使用して作成できます。 POST を使用するとき、データ ソースの定義とともに、要求本文にデータ ソースの名前を指定する必要があります。 PUT の場合、名前は URL の一部です。 データ ソースが存在しない場合、作成されます。 既に存在する場合は、新しい定義に更新されます。 

データ ソースの名前は小文字にします。文字または数字で始めます。スラッシュとドットは使用できません。文字数の制限は 128 文字です。 データ ソースの名前は文字または数字で始め、残りの部分には文字、数字、ダッシュを使用できます。ダッシュは連続することができません。 詳細については、「[名前付け規則](https://msdn.microsoft.com/library/azure/dn857353.aspx)」を参照してください。

`api-version` は必須です。 現行バージョンは `2015-02-28`です。

**要求ヘッダー**

次の一覧は、必須と任意の要求ヘッダーについてまとめたものです。 

* `Content-Type`: 必須。 これを `application/json`
* `api-key`: 必須。 `api-key` は Search サービスに対する要求の認証に使用されます。 これはサービスに固有の文字列値です。 **データ ソースの作成要求**には (クエリ キーではなく) 管理者キーに設定された `api-key` ヘッダーを含めます。 

要求 URL を作成するにはサービス名も必要です。 サービス名と `api-key` はどちらも、 [Azure Portal](https://portal.azure.com/)のサービス ダッシュボードから取得できます。 ページのナビゲーション ヘルプについては、「 [ポータルで Search サービスを作成する](search-create-service-portal.md) 」を参照してください。

<a name="CreateDataSourceRequestSyntax"></a>
**要求本文の構文**

要求本文にはデータ ソースの定義が含まれます。この定義には、データ ソースの種類、データを読むための資格情報、定期的にスケジュールされたインデクサーで使用するときに、データ ソースで変更または削除されたデータを効率的に識別するための任意のデータ変更検出/データ削除検出ポリシーが含まれます。 

要求ペイロードを構築するための構文は次のとおりです。 サンプル要求については、このトピックでさらに詳しく取り上げます。

    { 
        "name" : "Required for POST, optional for PUT. The name of the data source",
        "description" : "Optional. Anything you want, or nothing at all",
        "type" : "Required. Must be one of 'azuresql', 'documentdb', 'azureblob', or 'azuretable'",
        "credentials" : { "connectionString" : "Required. Connection string for your data source" },
        "container" : { "name" : "Required. The name of the table, collection, or blob container you wish to index" },
        "dataChangeDetectionPolicy" : { Optional. See below for details }, 
        "dataDeletionDetectionPolicy" : { Optional. See below for details }
    }

要求には次のプロパティが含まれます。 

* `name`: 必須。 データ ソースの名前。 データ ソース名には小文字、数字、ダッシュのみを使用できます。最初と最後の文字をダッシュにすることはできません。文字数の制限は 128 文字です。
* `description`: 任意の説明。 
* `type`: 必須。 サポートされているデータ ソースの種類のいずれかを指定する必要があります。
  * `azuresql` - Azure SQL Database または Azure VM の SQL Server
  * `documentdb` - Azure DocumentDB
  * `azureblob` - Azure Blob ストレージ
  * `azuretable` - Azure テーブル ストレージ
* `credentials`:
  * 必須の `connectionString` プロパティはデータ ソースの接続文字列を指定します。 接続文字列の形式はデータ ソースの種類によって異なります。 
    * Azure SQL の場合、通常の SQL Server 接続文字列です。 Azure ポータルを利用して接続文字列を取得する場合は、 `ADO.NET connection string` オプションを使用します。
    * DocumentDB の場合、接続文字列は `"AccountEndpoint=https://[your account name].documents.azure.com;AccountKey=[your account key];Database=[your database id]"`の形式にする必要があります。 すべての値が必須です。 値は [Azure ポータル](https://portal.azure.com/)にあります。  
    * Azure Blob ストレージおよび Azure テーブル ストレージの場合、これはストレージ アカウント接続文字列です。 形式については、 [こちら](https://azure.microsoft.com/documentation/articles/storage-configure-connection-string/)をご覧ください。 HTTPS エンドポイント プロトコルが必要です。  
* `container`、必須: `name` プロパティと `query` プロパティを使用して、インデックスを作成するデータを指定します。 
  * `name`、必須: 
    * Azure SQL: テーブルまたはビューを指定します。 `[dbo].[mytable]`などのスキーマ修飾名を使用できます。
    * DocumentDB: コレクションを指定します。 
    * Azure Blob ストレージ: ストレージ コンテナーを指定します。
    * Azure テーブル ストレージ: テーブルの名前を指定します。 
  * `query`、省略可能:
    * DocumentDB: Azure Search がインデックスを作成できるフラット スキーマに任意の JSON ドキュメント レイアウトをフラット化するクエリを指定できます。  
    * Azure BLOB ストレージ: BLOB コンテナー内の仮想フォルダーを指定できます。 たとえば、BLOB パス `mycontainer/documents/blob.pdf` については、`documents` を仮想フォルダーとして使用できます。
    * Azure テーブル ストレージ: インポートする行のセットをフィルター処理するクエリを指定できます。
    * Azure SQL: クエリはサポートされていません。 この機能が必要な場合は、 [こちらの提案](https://feedback.azure.com/forums/263029-azure-search/suggestions/9893490-support-user-provided-query-in-sql-indexer)
* 省略可能な `dataChangeDetectionPolicy` プロパティと `dataDeletionDetectionPolicy` プロパティについては、次に説明します。

<a name="DataChangeDetectionPolicies"></a>
**データ変更検出ポリシー**

データ変更の検出ポリシーの目的は、変更されたデータ項目を効率的に識別することです。 サポートされているポリシーは、データ ソースの種類によって異なります。 以下のセクションでは、各ポリシーについて説明します。 

***高基準値変更検出ポリシー*** 

このポリシーは、データ ソースに含まれる列またはプロパティが次の条件を満たす場合に使用します。

* すべての挿入は列の値を指定します。 
* 項目を更新すると、列の値も変更されます。 
* この列の値は変更のたびに増加します。
* 次の `WHERE [High Water Mark Column] > [Current High Water Mark Value]` と同じようなフィルター句を使用するクエリを効率的に実行できます。

たとえば、Azure SQL データ ソースを使用するとき、インデックス付き `rowversion` 列は高基準値ポリシーとともに使用するのに最適な候補となります。 

このポリシーは次のように指定できます。

    { 
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "[a row version or last_updated column name]" 
    } 

DocumentDB データ ソースを使用するとき、DocumentDB が与える `_ts` プロパティを使用する必要があります。 

Azure BLOB の利用時、Azure Search は、BLOB の最後に変更されたタイムスタンプに基づく高基準値変更検出ポリシーを自動的に使用します。このようなポリシーを自分で指定する必要はありません。   

***SQL 統合変更検出ポリシー***

SQL データベースが [変更追跡](https://msdn.microsoft.com/library/bb933875.aspx)をサポートする場合、SQL 統合変更追跡ポリシーの使用が推奨されます。 このポリシーは最も効率的な変更追跡を可能にし、スキーマに明示的な「ソフト削除」列がなくても、Azure Search で削除済み行を識別できます。

統合変更追跡は、次の SQL Server データベース バージョン以降でサポートされます。 

* Azure VM で SQL Server を使用している場合は、SQL Server 2008 R2。
* Azure SQL Database を使用している場合は、Azure SQL Database V12。  

SQL 統合変更追跡ポリシーを使用するときは、個別のデータ削除検出ポリシーを指定しないでください。個別のデータ削除ポリシーには、削除された行を識別するためのサポートが組み込まれています。 

このポリシーはテーブルでのみ使用できます。ビューで使用することはできません。 このポリシーを使用する前に、使用しているテーブルの変更追跡を有効にする必要があります。 指示については、「[変更の追跡の有効化と無効化](https://msdn.microsoft.com/library/bb964713.aspx)」をご覧ください。    

**データ ソースの作成** 要求を作成するとき、SQL 統合変更追跡ポリシーを次のように指定できます。

    { 
        "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
    }

<a name="DataDeletionDetectionPolicies"></a>
**データ削除検出ポリシー**

データ削除の検出ポリシーの目的は、削除されたデータ項目を効率的に識別することです。 現在、唯一サポートされているポリシーは `Soft Delete` ポリシーです。このポリシーでは、削除された項目をデータ ソースの `soft delete` 列またはプロパティに基づいて識別できます。 このポリシーは次のように指定できます。

    { 
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the column that specifies whether a row was deleted", 
        "softDeleteMarkerValue" : "the value that identifies a row as deleted" 
    }

> [!NOTE]
> 文字列、整数、ブール値を持つ列のみがサポートされます。 `softDeleteMarkerValue` として使用される値は、それに対応する列に整数またはブール値が含まれているとしても、文字列にする必要があります。 たとえば、データ ソースに表示される値が 1 の場合、`"1"` を `softDeleteMarkerValue` として使用します。    
> 
> 

<a name="CreateDataSourceRequestExamples"></a>
**要求本文の例**

スケジュールに基づいて実行されるインデクサーでデータ ソースを使用する場合、この例を見れば、変更/削除検出ポリシーの指定方法がわかります。 

    { 
        "name" : "asqldatasource",
        "description" : "a description",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" },
        "dataChangeDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy", "highWaterMarkColumnName" : "RowVersion" }, 
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }

データの&1; 回限りのコピーのためにデータ ソースを使用する場合、ポリシーは省略できます。

    { 
        "name" : "asqldatasource",
        "description" : "anything you want, or nothing at all",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" }
    } 

**応答**

要求成功の場合: 201 作成されました。 

<a name="UpdateDataSource"></a>

## <a name="update-data-source"></a>データ ソースの更新
HTTP PUT 要求を使用して既存のデータ ソースを更新できます。 要求 URI に更新するデータ ソースの名前を指定します。

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

`api-version` は必須です。 現行バージョンは `2015-02-28`です。 [Azure Search API のバージョン](https://msdn.microsoft.com/library/azure/dn864560.aspx)に関するページに、代替バージョンに関する情報を含む詳細があります。

`api-key` は (クエリ キーではなく) 管理者キーにする必要があります。 キーに関する詳細については、「 [Search サービス REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 」の認証セクションを参照してください。 [ポータルで Search サービスを作成する](search-create-service-portal.md) 」には、要求で使用されるサービスの URL とキーのプロパティを取得する方があります。

**要求**

要求本文の構文は [データ ソースの作成要求](#CreateDataSourceRequestSyntax)の場合と同じです。

一部のプロパティは既存のデータ ソースで更新できません。 たとえば、既存のデータ ソースの種類は変更できません。  

既存のデータ ソースの接続文字列を変更しない場合、接続文字列にリテラル `<unchanged>` を指定できます。 これは、データ ソースを更新する必要があるが、機密データであるため、接続文字列に簡単にアクセスできない場合に便利です。

**応答**

要求成功の場合: 新しいデータ ソースが作成された場合は「201 作成されました」で、既存のデータ ソースが更新された場合は「204 コンテンツがありません」。

<a name="ListDataSource"></a>

## <a name="list-data-sources"></a>データ ソースの一覧表示
**データ ソースの一覧表示** 操作は Azure Search サービスのデータ ソースの一覧を返します。 

    GET https://[service name].search.windows.net/datasources?api-version=[api-version]
    api-key: [admin key]

`api-version` は必須です。 現行バージョンは `2015-02-28`です。 

`api-key` は (クエリ キーではなく) 管理者キーにする必要があります。 キーに関する詳細については、「 [Search サービス REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 」の認証セクションを参照してください。 [ポータルで Search サービスを作成する](search-create-service-portal.md) 」には、要求で使用されるサービスの URL とキーのプロパティを取得する方があります。

**応答**

要求成功の場合: 200 OK。

次は応答本文の例です。

    {
      "value" : [
        {
          "name": "datasource1",
          "type": "azuresql",
          ... other data source properties
        }]
    }

フィルター処理で応答を興味のあるプロパティだけに絞り込むことができます。 たとえば、データ ソース名の一覧だけを必要とする場合、OData `$select` クエリ オプションを使用します。

    GET /datasources?api-version=205-02-28&$select=name

この場合は、上記の例の応答は次のように表示されます。 

    {
      "value" : [ { "name": "datasource1" }, ... ]
    }

これは、Search サービスにデータ ソースがたくさんある場合、帯域幅を節約する便利な方法となります。

<a name="GetDataSource"></a>

## <a name="get-data-source"></a>データ ソースの取得
**データ ソースの取得** 操作は Azure Search からデータ ソース定義を取得します。

    GET https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

`api-version` は必須です。 現行バージョンは `2015-02-28`です。 

`api-key` は (クエリ キーではなく) 管理者キーにする必要があります。 キーに関する詳細については、「 [Search サービス REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 」の認証セクションを参照してください。 [ポータルで Search サービスを作成する](search-create-service-portal.md) 」には、要求で使用されるサービスの URL とキーのプロパティを取得する方があります。

**応答**

状態コード: 応答の成功に対して「200 OK」が返されます。

この応答は [データ ソースの作成の要求例](#CreateDataSourceRequestExamples)に似ています。 

    { 
        "name" : "asqldatasource",
        "description" : "a description",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" },
        "dataChangeDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName" : "RowVersion" }, 
        "dataDeletionDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted", 
            "softDeleteMarkerValue" : "true" }
    }

> [!NOTE]
> この API を呼び出すときは `Accept` 要求ヘッダーを `application/json;odata.metadata=none` に設定しないでください。設定すると、応答から `@odata.type` 属性が省略され、さまざまな種類のデータ変更/データ削除検出ポリシーを区別できなくなります。 
> 
> 

<a name="DeleteDataSource"></a>

## <a name="delete-data-source"></a>データ ソースの削除
**データ ソースの削除** 操作は Azure Search サービスからデータ ソースを削除します。

    DELETE https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

> [!NOTE]
> 削除しようとしているデータ ソースをインデクサーが参照する場合でも、削除操作は続行されます。 ただし、そのインデクサーの状態が次の実行でエラーに変わります。  
> 
> 

`api-version` は必須です。 現行バージョンは `2015-02-28`です。 

`api-key` は (クエリ キーではなく) 管理者キーにする必要があります。 キーに関する詳細については、「 [Search サービス REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 」の認証セクションを参照してください。 [ポータルで Search サービスを作成する](search-create-service-portal.md) 」には、要求で使用されるサービスの URL とキーのプロパティを取得する方があります。

**応答**

状態コード: 応答の成功に対して「204 コンテンツがありません」が返されます。

<a name="CreateIndexer"></a>

## <a name="create-indexer"></a>インデクサーの作成
HTTP POST 要求を実行し、Azure Search サービス内に新しいインデクサーを作成できます。

    POST https://[service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

または、PUT を使用し、URI でデータ ソース名を指定できます。 データ ソースが存在しない場合、作成されます。

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]

> [!NOTE]
> 許可されるインデクサーの最大数は価格レベルによって異なります。 無料サービスの場合、最大 3 つのインデクサーが許可されます。 Standard サービスの場合、50 のインデクサーが許可されます。 詳細については、「 [サービスの制限](search-limits-quotas-capacity.md) 」を参照してください。
> 
> 

`api-version` は必須です。 現行バージョンは `2015-02-28`です。 

`api-key` は (クエリ キーではなく) 管理者キーにする必要があります。 キーに関する詳細については、「 [Search サービス REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 」の認証セクションを参照してください。 [ポータルで Search サービスを作成する](search-create-service-portal.md) 」には、要求で使用されるサービスの URL とキーのプロパティを取得する方があります。

<a name="CreateIndexerRequestSyntax"></a>
**要求本文の構文**

要求の本文には、インデックス作成のためのデータ ソースとターゲット インデックスを指定するインデクサー定義と、任意でインデックス作成のスケジュールとパラメーターが含まれます。 

要求ペイロードを構築するための構文は次のとおりです。 サンプル要求については、このトピックでさらに詳しく取り上げます。

    { 
        "name" : "Required for POST, optional for PUT. The name of the indexer",
        "description" : "Optional. Anything you want, or null",
        "dataSourceName" : "Required. The name of an existing data source",
        "targetIndexName" : "Required. The name of an existing index",
        "schedule" : { Optional. See Indexing Schedule below. },
        "parameters" : { Optional. See Indexing Parameters below. },
        "fieldMappings" : { Optional. See Field Mappings below. },
        "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.  
    }

**インデクサーのスケジュール**

インデクサーには、必要に応じてスケジュールを指定できます。 スケジュールが存在する場合、インデクサーはスケジュールに従って定期的に実行されます。 スケジュールには次の属性があります。

* `interval`: 必須。 インデクサーが実行される間隔または期間を指定する時間の値。 許可される最短の間隔は 5 分です。最長は 1 日です。 XSD "dayTimeDuration" 値 ([ISO 8601 期間](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)値の制限されたサブセット) として書式設定する必要があります。 使用されるパターンは、`"P[nD][T[nH][nM]]"` です。 たとえば、15 分ごとの場合は `PT15M`、2 分ごとの場合は `PT2H` です。 
* `startTime`: 必須。 インデクサーの実行を開始する UTC 日時。 

**インデクサーのパラメーター**

インデクサーには任意でパラメーターを指定し、インデクサーの動作を変えることができます。 パラメーターはすべて任意です。  

* `maxFailedItems` : インデックス作成失敗がこの項目数に到達すると、そのインデクサー実行は失敗であるとみなされます。 既定値は 0 です。 失敗した項目に関する情報は [インデクサー状態の取得](#GetIndexerStatus) 操作で返されます。 
* `maxFailedItemsPerBatch` : バッチごとのインデックス作成失敗がこの項目数に到達すると、そのインデクサー実行は失敗であるとみなされます。 既定値は 0 です。
* `base64EncodeKeys`: ドキュメント キーを base&64; でエンコードするかどうかを指定します。 Azure Search では、ドキュメント キーの文字に制約があります。 ただし、ソース データの値には無効な文字を含めることができます。 そのような値にドキュメント キーとしてインデックスを作成する必要がある場合、このフラグを true に設定できます。 既定値は `false`です。
* `batchSize`: パフォーマンスを改善する目的で、データ ソースから読み込まれ、1 つのバッチとしてインデックスが作成されるアイテムの数を指定します。 既定値はデータ ソースの種類によって異なります。Azure SQL と DocumentDB の場合は 1000 であり、Azure Blob Storage の場合は 10 です。

**フィールド マッピング**

フィールド マッピングを使用すると、データ ソース内のフィールド名をターゲット インデックスの別のフィールド名にマッピングできます。 たとえば、ソース テーブルに `_id` というフィールドがあるとします。 Azure Search では下線で始まるフィールド名は許可されません。そのため、このフィールドの名前を変更する必要があります。 次のようにインデクサーの `fieldMappings` プロパティを使用すれば名前を変更できます。 

    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 

複数のフィールド マッピングを指定できます。 

    "fieldMappings" : [ 
        { "sourceFieldName" : "_id", "targetFieldName" : "id" },
        { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" },
     ]

ソースとターゲットの両方のフィールド名で大文字と小文字が区別されます。

<a name="FieldMappingFunctions"></a>
***フィールド マッピング関数***

フィールド マッピングでは、 *マッピング関数*を利用し、ソースのフィールド値を変換することもできます。

このような関数としては現在、`jsonArrayToStringCollection` のみがサポートされています。 JSON 配列として書式設定されている文字列を含むフィールドを解析してターゲット インデックスの Collection(Edm.String) フィールドにします。 特に Azure SQL インデクサーで使用することを意図しています。SQL にはネイティブのコレクション データ型がないためです。 次のように使用できます。 

    "fieldMappings" : [ { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } } ] 

たとえば、ソース フィールドに文字列 `["red", "white", "blue"]` が含まれている場合、`Collection(Edm.String)` 型のターゲット フィールドに `"red"`、`"white"`、`"blue"` の&3; つの値が入力されます。

`targetFieldName` プロパティは任意です。省略した場合、`sourceFieldName` 値が使用されます。 

<a name="CreateIndexerRequestExamples"></a>
**要求本文の例**

次の例では、UTC 2015 年 1 月 1 日から 1 時間ごとに実行されるスケジュールで `ordersds` データ ソースによって参照されるテーブルのデータを `orders` インデックスにコピーするインデクサーを作成します。 インデックス作成に失敗した項目が各バッチで 5 つ以下で、合計で 10 以下あれば、各インデクサーの呼び出しは成功となります。 

    {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
    }

**応答**

要求成功の場合:&201; 作成されました。

<a name="UpdateIndexer"></a>

## <a name="update-indexer"></a>インデクサーの更新
HTTP PUT 要求を使用して既存のインデクサーを更新できます。 要求 URI に更新するインデクサーの名前を指定します。

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

`api-version` は必須です。 現行バージョンは `2015-02-28`です。 

`api-key` は (クエリ キーではなく) 管理者キーにする必要があります。 キーに関する詳細については、「 [Search サービス REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 」の認証セクションを参照してください。 [ポータルで Search サービスを作成する](search-create-service-portal.md) 」には、要求で使用されるサービスの URL とキーのプロパティを取得する方があります。

**要求**

要求本文の構文は [インデクサーの作成要求](#CreateIndexerRequestSyntax)の場合と同じです。

**応答**

要求成功の場合: 新しいインデクサーが作成された場合は「201 作成されました」で、既存のインデクサーが更新された場合は「204 コンテンツがありません」。

<a name="ListIndexers"></a>

## <a name="list-indexers"></a>インデクサーの一覧表示
**インデクサーの一覧表示** 操作は Azure Search サービスのインデクサーの一覧を返します。 

    GET https://[service name].search.windows.net/indexers?api-version=[api-version]
    api-key: [admin key]


`api-version` は必須です。 プレビュー バージョンは `2015-02-28-Preview`です。 [Azure Search バージョン管理](https://msdn.microsoft.com/library/azure/dn864560.aspx) に代替バージョンに関する情報を含む詳細があります。

`api-key` は (クエリ キーではなく) 管理者キーにする必要があります。 キーに関する詳細については、「 [Search サービス REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 」の認証セクションを参照してください。 [ポータルで Search サービスを作成する](search-create-service-portal.md) 」には、要求で使用されるサービスの URL とキーのプロパティを取得する方があります。

**応答**

要求成功の場合: 200 OK。

次は応答本文の例です。

    {
      "value" : [
      {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        ... other indexer properties
      }]
    }

フィルター処理で応答を興味のあるプロパティだけに絞り込むことができます。 たとえば、インデクサー名の一覧だけを必要とする場合、OData `$select` クエリ オプションを使用します。

    GET /indexers?api-version=2014-10-20-Preview&$select=name

この場合は、上記の例の応答は次のように表示されます。 

    {
      "value" : [ { "name": "myindexer" } ]
    }

これは、Search サービスにインデクサーがたくさんある場合、帯域幅を節約する便利な方法となります。

<a name="GetIndexer"></a>

## <a name="get-indexer"></a>インデクサーの取得
**インデクサーの取得** 操作は Azure Search からインデクサーの定義を取得します。

    GET https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

`api-version` は必須です。 プレビュー バージョンは `2015-02-28-Preview`です。 

`api-key` は (クエリ キーではなく) 管理者キーにする必要があります。 キーに関する詳細については、「 [Search サービス REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 」の認証セクションを参照してください。 [ポータルで Search サービスを作成する](search-create-service-portal.md) 」には、要求で使用されるサービスの URL とキーのプロパティを取得する方があります。

**応答**

状態コード: 応答の成功に対して「200 OK」が返されます。

この応答は [インデクサーの作成の要求例](#CreateIndexerRequestExamples)に似ています。 

    {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
    }


<a name="DeleteIndexer"></a>

## <a name="delete-indexer"></a>インデクサーの削除
**インデクサーの削除** 操作は Azure Search サービスからインデクサーを削除します。

    DELETE https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

インデクサーが削除されるとき、その時点で進行中のインデクサー実行は完了しますが、後続の実行はスケジュールされません。 存在しないインデクサーを使用しようとすると、HTTP 状態コードの「404 見つかりません」が表示されます。 

`api-version` は必須です。 プレビュー バージョンは `2015-02-28-Preview`です。 

`api-key` は (クエリ キーではなく) 管理者キーにする必要があります。 キーに関する詳細については、「 [Search サービス REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 」の認証セクションを参照してください。 [ポータルで Search サービスを作成する](search-create-service-portal.md) 」には、要求で使用されるサービスの URL とキーのプロパティを取得する方があります。

**応答**

状態コード: 応答の成功に対して「204 コンテンツがありません」が返されます。

<a name="RunIndexer"></a>

## <a name="run-indexer"></a>インデクサー実行
スケジュールに従って定期的に実行されるだけでなく、 **インデクサーの実行** 操作を実行して、オンデマンドでインデクサーを呼び出すこともできます。 

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [admin key]

`api-version` は必須です。 プレビュー バージョンは `2015-02-28-Preview`です。 

`api-key` は (クエリ キーではなく) 管理者キーにする必要があります。 キーに関する詳細については、「 [Search サービス REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 」の認証セクションを参照してください。 [ポータルで Search サービスを作成する](search-create-service-portal.md) 」には、要求で使用されるサービスの URL とキーのプロパティを取得する方があります。

**応答**

状態コード: 応答の成功に対して「202 受理されました」が返されます。

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>インデクサー状態の取得
**インデクサー状態の取得** 操作は、インデクサーの現在の状態と実行の履歴を取得します。 

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [admin key]


`api-version` は必須です。 プレビュー バージョンは `2015-02-28-Preview`です。 

`api-key` は (クエリ キーではなく) 管理者キーにする必要があります。 キーに関する詳細については、「 [Search サービス REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 」の認証セクションを参照してください。 [ポータルで Search サービスを作成する](search-create-service-portal.md) 」には、要求で使用されるサービスの URL とキーのプロパティを取得する方があります。

**応答**

状態コード: 応答の成功に対して「200 OK」が返されます。

応答本文にはインデクサーの全体的な正常性状態、インデクサーの最後の呼び出し、(存在する場合は) インデクサー呼び出しの最近の履歴に関する情報が含まれます。 

サンプルの応答本文は次のようになります。 

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

**インデクサーの状態**

インデクサーの状態には次の値のいずれかが設定されます。

* `running` は、インデクサーが通常どおり実行されていることを示します。 一部のインデクサー実行が失敗している可能性があります。`lastResult` プロパティも確認しておけば安心です。 
* `error` は、ユーザーの介入なしでは修正できないエラーがインデクサーに発生したことを示します。 たとえば、データ ソースの資格情報の有効期限が切れている、データ ソースまたはターゲット インデックスのスキーマに大幅に変更された、などです。 

**インデクサー実行の結果**

インデクサー実行の結果には、1 回のインデクサー実行に関する情報が含まれています。 最新の結果はインデクサーの状態に `lastResult` プロパティとして表示されます。 最近の結果がその他にも存在する場合、インデクサーの状態の `executionHistory` プロパティとして返されます。 

インデクサー実行の結果には次のプロパティが含まれます。 

* `status`: 実行の状態。 詳細については、下の「 [インデクサー実行の状態](#IndexerExecutionStatus) 」を参照してください。 
* `errorMessage`: 失敗した実行のエラー メッセージ。 
* `startTime`: この実行を開始した UTC 時刻。
* `endTime`: この実行を終了した UTC 時刻。 実行が進行中の場合、この値は設定されません。
* `errors`: 項目レベル エラーの配列 (そのエラーがある場合)。 各エントリには、ドキュメント キー(`key` プロパティ) とエラー メッセージ (`errorMessage` プロパティ) が含まれています。 
* `itemsProcessed`: この実行の間にインデクサーがインデックス作成を試行したデータ ソース項目 (テーブル行など) の数。 
* `itemsFailed`: この実行の間に失敗した項目の数。  
* `initialTrackingState`: 最初のインデクサー実行に対して、あるいは使用されるデータ ソースでデータ変更追跡ポリシーが有効になっていない場合、常に `null`。 データ変更追跡ポリシーが有効になっている場合、後続の実行で、この値はこの実行で処理された最初の (最低) 変更追跡値を示します。 
* `finalTrackingState`: 使用されるデータ ソースでデータ変更追跡ポリシーが有効になっていない場合、常に `null`。 有効になっている場合、この実行で処理された最後の (最高) 変更追跡値を示します。 

<a name="IndexerExecutionStatus"></a>
**インデクサー実行の状態**

インデクサー実行の状態は、1 回のインデクサー実行の状態を記録したものです。 次の値が入ります。

* `success` は、インデクサー実行が正常に完了したことを示します。
* `inProgress` は、インデクサー実行が進行中であることを示します。 
* `transientFailure` は、インデクサー実行が失敗したことを示します。 詳細については、`errorMessage` プロパティを参照してください。 エラーには、それを修正するのに人間の介入が必要なものと不要なものがあります。たとえば、データ ソースとターゲット インデックス間のスキーマの互換性を修正するにはユーザーのアクションが必要ですが、データ ソースが一時的にダウンしている場合はユーザーのアクションは不要です。 スケジュールがあれば、スケジュールに基づいてインデクサーの呼び出しが続行されます。 
* `persistentFailure` は、インデクサーでエラーが発生し、ユーザーの介在を必要していることを示します。 スケジュールされているインデクサーの実行は停止します。 問題を解決した後に、インデクサーのリセット API を使用し、スケジュールされている実行を再開します。 
* `reset` は、インデクサーをリセットする API (下記参照) の呼び出しによりインデクサーがリセットされたことを示します。 

<a name="ResetIndexer"></a>

## <a name="reset-indexer"></a>インデクサーのリセット
**インデクサーのリセット** 操作は、インデクサーに関連付けられている変更追跡の状態をリセットします。 リセットすることで、インデックス再作成を最初から始めたり (データ ソース スキーマが変更された場合など)、インデクサーに関連付けられているデータ ソースのデータ変更追跡ポリシーを変えたりできます。   

    POST https://[service name].search.windows.net/indexers/[indexer name]/reset?api-version=[api-version]
    api-key: [admin key]

`api-version` は必須です。 プレビュー バージョンは `2015-02-28-Preview`です。 

`api-key` は (クエリ キーではなく) 管理者キーにする必要があります。 キーに関する詳細については、「 [Search サービス REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 」の認証セクションを参照してください。 [ポータルで Search サービスを作成する](search-create-service-portal.md) 」には、要求で使用されるサービスの URL とキーのプロパティを取得する方があります。

**応答**

状態コード: 応答の成功に対して「204 コンテンツがありません」が返されます。

## <a name="mapping-between-sql-data-types-and-azure-search-data-types"></a>JSON データ型と Azure Search データ型の間のマッピング
<table style="font-size:12">
<tr>
<td>SQL データ型</td>    
<td>ターゲット インデックス フィールドに許可される型</td>
<td>メモ</td>
</tr>
<tr>
<td>ビット</td>
<td>Edm.Boolean、Edm.String</td>
<td></td>
</tr>
<tr>
<td>int、smallint、tinyint</td>
<td>Edm.Int32、Edm.Int64、Edm.String</td>
<td></td>
</tr>
<tr>
<td>bigint</td>
<td>Edm.Int64、Edm.String</td>
<td></td>
</tr>
<tr>
<td>real、float</td>
<td>Edm.Double、Edm.String</td>
<td></td>
</tr>
<tr>
<td>smallmoney、money<br>小数点<br>数値
</td>
<td>Edm.String</td>
<td>Azure Search では、小数点を Edm.Double に変換できません。精度が失われるためです。
</td>
</tr>
<tr>
<td>char、nchar、varchar、nvarchar</td>
<td>Edm.String<br/>Collection(Edm.String)</td>
<td>文字列の列を Collection(Edm.String) に変換する方法については、本書の[フィールド マッピング関数 ](#FieldMappingFunctions) を参照してください</td>
</tr>
<tr>
<td>smalldatetime、datetime、datetime2、date、datetimeoffset</td>
<td>Edm.DateTimeOffset、Edm.String</td>
<td></td>
</tr>
<tr>
<td>uniqueidentifer</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>地理</td>
<td>Edm.GeographyPoint</td>
<td>型が POINT で SRID が 4326 (既定) の地理インスタンスのみがサポートされます。</td>
</tr>
<tr>
<td>rowversion</td>
<td>該当なし</td>
<td>行バージョン列は検索インデックスに保存できませんが、変更追跡に利用できます。</td>
</tr>
<tr>
<td>time、timespan<br>binary、varbinary、image<br>xml、geometry、CLR 型</td>
<td>該当なし</td>
<td>サポートされていません</td>
</tr>
</table>

## <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>JSON データ型と Azure Search データ型間のマッピング
<table style="font-size:12">
<tr>
<td>JSON データ型</td>    
<td>ターゲット インデックス フィールドに許可される型</td>
<td>メモ</td>
</tr>
<tr>
<td>bool</td>
<td>Edm.Boolean、Edm.String</td>
<td></td>
</tr>
<tr>
<td>整数</td>
<td>Edm.Int32、Edm.Int64、Edm.String</td>
<td></td>
</tr>
<tr>
<td>浮動小数点数</td>
<td>Edm.Double、Edm.String</td>
<td></td>
</tr>
<tr>
<td>string</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>プリミティブ型の配列。例 [ "a"、"b"、"c" ]</td>
<td>Collection(Edm.String)</td>
<td></td>
</tr>
<tr>
<td>日付などの文字列</td>
<td>Edm.DateTimeOffset、Edm.String</td>
<td></td>
</tr>
<tr>
<td>GeoJSON ポイント オブジェクト</td>
<td>Edm.GeographyPoint</td>
<td>GeoJSON ポイントは { "type" : "Point", "coordinates" : [long, lat] } 形式の JSON オブジェクトです。 </td>
</tr>
<tr>
<td>その他の JSON オブジェクト</td>
<td>該当なし</td>
<td>サポートされていません。Azure Search では現在のところ、プリミティブ型と文字列の集合のみをサポートしています。</td>
</tr>
</table>



<!--HONumber=Jan17_HO3-->


