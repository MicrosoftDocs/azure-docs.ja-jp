---
title: さまざまなソースからのデータ フィードを Metrics Advisor に追加する方法
titleSuffix: Azure Cognitive Services
description: Metrics Advisor にさまざまなデータ フィードを追加する
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: mbullwin
ms.openlocfilehash: c4d1d23da5fd9678cc5b9477ddeed0daf4f5ac36
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96348621"
---
# <a name="add-data-feeds-from-different-data-sources-to-metrics-advisor"></a>さまざまなデータ ソースからデータ フィードを Metrics Advisor に追加する

この記事を使用して、さまざまな種類のデータ ソースを Metrics Advisor に接続するための設定と要件を理解してください。 Metrics Advisor でデータを使用するための主要な概念については、[データをオンボードする](how-tos/onboard-your-data.md)方法を必ずお読みください。 

## <a name="supported-authentication-types"></a>サポートされている認証の種類

| 認証の種類 | 説明 |
| ---------------------|-------------|
|**Basic** | データ ソースにアクセスするための基本パラメーターを指定できる必要があります。 たとえば、接続文字列やキーなどです。 データ フィード管理者は、これらの資格情報を表示できます。 |
| **AzureManagedIdentity** | Azure リソースの[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) は、Azure Active Directory の機能です。 これにより、Azure AD で自動的に管理される ID が Azure サービスに提供されます。 ID を使用して、Azure AD 認証をサポートする任意のサービスに対する認証を行うことができます。|
| **AzureSQLConnectionString**| AzureSQL 接続文字列を **資格情報エンティティ** として Metrics Advisor に保存し、メトリック データをオンボードするたびにそれを直接使用します。 これらの資格情報を表示できるのは資格情報エンティティの管理者だけですが、承認された閲覧者は、資格情報の詳細を知らなくてもデータ フィードを作成できます。 |
| **DataLakeGen2SharedKey**| データ レイク アカウント キーを **資格情報エンティティ** として Metrics Advisor に保存し、メトリック データをオンボードするたびにそれを直接使用します。 これらの資格情報を表示できるのは資格情報エンティティの管理者だけですが、承認された閲覧者は、資格情報の詳細を知らなくてもデータ フィードを作成できます。|
| **サービス プリンシパル**| サービス プリンシパルを **資格情報エンティティ** として Metrics Advisor に保存し、メトリック データをオンボードするたびにそれを直接使用します。 これらの資格情報を表示できるのは資格情報エンティティの管理者だけですが、承認された閲覧者は、資格情報の詳細を知らなくてもデータ フィードを作成できます。|
| **キー コンテナーからのサービス プリンシパル**|キー コンテナー内のサービス プリンシパルを **資格情報エンティティ** として Metrics Advisor に保存し、メトリック データをオンボードするたびにそれを直接使用します。 資格情報を表示できるのは **資格情報エンティティ** の管理者だけですが、詳細な資格情報を知らなくても、閲覧者はデータ フィードを作成できます。 |

## <a name="data-sources-supported-and-corresponding-authentication-types"></a>サポートされているデータ ソースと対応する認証の種類


| データ ソース | 認証の種類 |
|-------------| ---------------------|
|[**Azure Application Insights**](#appinsights)|  Basic |
|[**Azure Blob Storage (JSON)**](#blob) | Basic<br>ManagedIdentity|
|[**Azure Cosmos DB (SQL)**](#cosmosdb) | Basic |
|[**Azure Data Explorer (Kusto)**](#kusto) | Basic<br>ManagedIdentity|
|[**Azure Data Lake Storage Gen2**](#adl) | Basic<br>DataLakeGen2SharedKey<br>サービス プリンシパル<br>キー コンテナーからのサービス プリンシパル<br> |
|[**Azure SQL Database / SQL Server**](#sql) | Basic<br>ManagedIdentity<br>サービス プリンシパル<br>キー コンテナーからのサービス プリンシパル<br>AzureSQLConnectionString
|[**Azure Table Storage**](#table) | Basic | 
|[**ElasticSearch**](#es) | Basic |
|[**HTTP 要求**](#http) | Basic | 
|[**InfluxDB (InfluxQL)**](#influxdb) | Basic |
|[**MongoDB**](#mongodb) | Basic |
|[**MySQL**](#mysql) | Basic |
|[**PostgreSQL**](#pgsql)| Basic|

**資格情報エンティティ** を作成し、それを使用してデータ ソースへの認証を行います。 以降のセクションでは、"*基本*" 認証に必要なパラメーターを指定します。 

## <a name="span-idappinsightsazure-application-insightsspan"></a><span id="appinsights">Azure Application Insights</span>

* **アプリケーション ID**:これは、Application Insights API を使用する場合に、このアプリケーションを識別するために使用されます。 アプリケーション ID を取得するには、次の手順を実行します。

    1. Application Insights リソースから、 [API アクセス] をクリックします。

    2. Metrics Advisor の **[アプリケーション ID]** フィールドに生成されたアプリケーション ID をコピーします。 
    
    詳細については、[Azure Bot Service のドキュメント](/azure/bot-service/bot-service-resources-app-insights-keys#application-id)を参照してください。

* **API キー**:API キーは、このリソースへアクセスするために、ブラウザーの外側のアプリケーションによって使用されます。 API キーを取得するには、次の手順を実行します。

    1. Application Insights リソースから、 [API アクセス] をクリックします。

    2. [API キーの作成] をクリックします。

    3. 簡単な説明を入力し、[テレメトリを読み取る] をオンにして、[キーの生成] ボタンをクリックします。

    4. API キーを Metrics Advisor の **[API キー]** フィールドにコピーします。

* **Query**: Azure Application Insights ログは、Azure Data Explorer 上に構築されており、Azure Monitor ログ クエリでは、同じ Kusto クエリ言語のバージョンが使用されます。 [Kusto クエリ言語のドキュメント](/azure/data-explorer/kusto/query/)には言語に関するすべての詳細が記載されているため、Application Insights に対するクエリを記述するためのプライマリ リソースとなります。 


## <a name="span-idblobazure-blob-storage-jsonspan"></a><span id="blob">Azure Blob Storage (JSON)</span>

* **接続文字列**:この文字列の取得の詳細については、Azure Blob Storage の [接続文字列](../../storage/common/storage-configure-connection-string.md#configure-a-connection-string-for-an-azure-storage-account)に関する記事を参照してください。

* **コンテナー**:Metrics Advisor では、1 つのコンテナーの下に BLOB ファイルとして格納された時系列データ (タイムスタンプごとに 1 つの BLOB) が想定されています。 これはコンテナー名フィールドです。

* **BLOB テンプレート**:これは BLOB ファイル名のテンプレートです。 (例: `/%Y/%m/X_%Y-%m-%d-%h-%M.json`)。 サポートされているパラメーターは次のとおりです。
  * `%Y` は `yyyy` として書式設定された年です
  * `%m` は `MM` として書式設定された月です
  * `%d` は `dd` として書式設定された日です
  * `%h` は `HH` として書式設定された時間です
  * `%M` は `mm` として書式設定された分です

* **JSON 形式のバージョン**:JSON ファイルのデータ スキーマを定義します。 現在、Metrics Advisor では 2 つのバージョンがサポートされています。
  
  * v1 (既定値)

      メトリック *Name* と *Value* のみが受け入れられます。 次に例を示します。
    
      ``` JSON
      {"count":11, "revenue":1.23}
      ```

  * v2

      メトリック *Dimensions* と *timestamp* も受け入れられます。 次に例を示します。
      
      ``` JSON
      [
        {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
        {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
      ]
      ```

JSON ファイルごとに 1 つのタイムスタンプのみが許可されます。 

## <a name="span-idcosmosdbazure-cosmos-db-sqlspan"></a><span id="cosmosdb">Azure Cosmos DB (SQL)</span>

* **接続文字列**:Azure Cosmos DB にアクセスするための接続文字列。 これは、Cosmos DB リソースの **Keys** にあります。 
* **データベース**:クエリ対象のデータベース。 これは、 **[参照]** ページの **[コンテナー]** セクションにあります。
* **コレクション ID**:クエリ対象のコレクション ID。 これは、 **[参照]** ページの **[コンテナー]** セクションにあります。
* **SQL クエリ**:データを取得して多次元時系列データに編成する SQL クエリ。 クエリでは、変数 `@StartTime` と `@EndTime` を使用できます。 これらは `yyyy-MM-dd HH:mm:ss` に書式設定する必要があります。

    サンプル クエリ:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = @StartTime
    ```
    
    2019/12/12 のデータ スライスのサンプル クエリ:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = '2019-12-12 00:00:00'
    ```

## <a name="span-idkustoazure-data-explorer-kustospan"></a><span id="kusto">Azure Data Explorer (Kusto)</span>

* **接続文字列**:Metrics Advisor では、Azure AD アプリケーション認証を使用して Azure Data Explorer (Kusto) へのアクセスをサポートします。 Azure AD アプリケーションを作成して登録し、Azure Data Explorer データベースへのアクセスを承認する必要があります。 接続文字列を取得するには、[Azure Data Explorer](/azure/data-explorer/provision-azure-ad-app) のドキュメントをご覧ください。

* **Query**: データを取得して多次元時系列データに編成するには、[Kusto クエリ言語](/azure/data-explorer/kusto/query)に関する記事を参照してください。 クエリでは、変数 `@StartTime` と `@EndTime` を使用できます。 これらは `yyyy-MM-dd HH:mm:ss` に書式設定する必要があります。

## <a name="span-idadlazure-data-lake-storage-gen2span"></a><span id="adl">Azure Data Lake Storage Gen2</span>

* **アカウント名**: Azure Data Lake Storage Gen2 のアカウント名。 これは **アクセス キー** の Azure Storage アカウント (Azure Data Lake Storage Gen2) リソースにあります。

* **アカウント キー**:Azure Data Lake Storage Gen2 にアクセスするには、アカウント名を指定してください。 これは **アクセス キー** 設定の Azure Storage アカウント (Azure Data Lake Storage Gen2) リソースにあります。

* **ファイル システム名 (コンテナー)** :Metrics Advisor では、1 つのコンテナーの下に BLOB ファイルとして格納された時系列データ (タイムスタンプごとに 1 つの BLOB) が想定されます。 これはコンテナー名フィールドです。 これは、Azure ストレージ アカウント (Azure Data Lake Storage Gen2) インスタンスにあります。[Blob service] セクションの [コンテナー] をクリックします。

* **ディレクトリ テンプレート**:これは BLOB ファイルのディレクトリ テンプレートです。 例: */%Y/%m/%d*。 サポートされているパラメーターは次のとおりです。
  * `%Y` は `yyyy` として書式設定された年です
  * `%m` は `MM` として書式設定された月です
  * `%d` は `dd` として書式設定された日です
  * `%h` は `HH` として書式設定された時間です
  * `%M` は `mm` として書式設定された分です

* **ファイル テンプレート**:これは BLOB ファイルのファイル テンプレートです。 次に例を示します。*X_%Y-%m-%d-%h-%M.json*。 サポートされているパラメーターは次のとおりです。
  * `%Y` は `yyyy` として書式設定された年です
  * `%m` は `MM` として書式設定された月です
  * `%d` は `dd` として書式設定された日です
  * `%h` は `HH` として書式設定された時間です
  * `%M` は `mm` として書式設定された分です

現在、Metrics Advisor では、次のように JSON ファイルのデータ スキーマがサポートされています。 次に例を示します。

``` JSON
[
  {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
  {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
]
```
<!--
## <span id="eventhubs">Azure Event Hubs</span>

* **Connection String**: This can be found in 'Shared access policies' in your Event Hubs instance. Also for the 'EntityPath', it could be found by clicking into your Event Hubs instance and clicking at 'Event Hubs' in 'Entities' blade. Items that listed can be input as EntityPath. 

* **Consumer Group**: A [consumer group](../../event-hubs/event-hubs-features.md#consumer-groups) is a view (state, position, or offset) of an entire event hub.
Event Hubs use the latest offset of a consumer group to consume (subscribe from) the data from data source. Therefore a dedicated consumer group should be created for one data feed in your Metrics Advisor instance.

* **Timestamp**: Metrics Advisor uses the Event Hubs timestamp as the event timestamp if the user data source does not contain a timestamp field.
The timestamp field must match one of these two formats:

    * "YYYY-MM-DDTHH:MM:SSZ" format;

    * Number of seconds or milliseconds from the epoch of 1970-01-01T00:00:00Z.

    No matter which timestamp field it left aligns to granularity.For example, if timestamp is "2019-01-01T00:03:00Z", granularity is 5 minutes, then Metrics Advisor aligns the timestamp to "2019-01-01T00:00:00Z". If the event timestamp is "2019-01-01T00:10:00Z",  Metrics Advisor uses the timestamp directly without any alignment.
-->
## <a name="span-idsqlazure-sql-database--sql-serverspan"></a><span id="sql">Azure SQL Database | SQL Server</span>

* **接続文字列**:Metrics Advisor では、SQL Server データ ソース用の [ADO.NET スタイルの接続文字列](/dotnet/framework/data/adonet/connection-string-syntax)を受け取ります。

    サンプルの接続文字列を次に示します。

    ```
    Data Source=db-server.database.windows.net:[port];initial catalog=[database];User ID=[username];Password=[password];Connection Timeout=10ms;
    ```

* **Query**: データを取得して多次元時系列データに編成する SQL クエリ。 クエリで変数 `@StartTime` を使用すると、期待されるメトリック値を取得するのに役立ちます。

  * `@StartTime`: `yyyy-MM-dd HH:mm:ss` 形式の datetime

    サンプル クエリ:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = @StartTime
    ```
    
    2019/12/12 のデータ スライスに対して実際に実行されたクエリ:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = '2019-12-12 00:00:00'
    ```

## <a name="span-idtableazure-table-storagespan"></a><span id="table">Azure Table Storage</span>

* **接続文字列**:Azure Table Storage から接続文字列を取得する方法については、[接続文字列の表示とコピー](../../storage/common/storage-account-keys-manage.md?tabs=azure-portal&toc=%2fazure%2fstorage%2ftables%2ftoc.json#view-account-access-keys)に関する記事を参照してください。

* **[テーブル名]** : クエリ対象のテーブルを指定します。 これは、Azure Storage アカウント インスタンスにあります。 **[Table service]** セクションで **[テーブル]** をクリックします。

* **クエリ**: クエリで `@StartTime` を使用できます。 `@StartTime` は、スクリプト内で yyyy-MM-ddTHH:mm:ss 形式の文字列に置き換えられます。

    ``` mssql
    let StartDateTime = datetime(@StartTime); let EndDateTime = StartDateTime + 1d; 
    SampleTable | where Timestamp >= StartDateTime and Timestamp < EndDateTime | project Timestamp, Market, RPM
    ```

## <a name="span-ideselasticsearchspan"></a><span id="es">Elasticsearch</span>

* **ホスト**: Elasticsearch クラスターのマスター ホストを指定します。
* **ポート**: Elasticsearch クラスターのマスター ポートを指定します。
* **Authorization ヘッダー**: Elasticsearch クラスターの Authorization ヘッダー値を指定します。
* **クエリ**: データを取得するクエリを指定します。 プレースホルダー @StartTime がサポートされています (たとえば、2020-06-21T00:00: 00Z のデータが取り込まれると、@StartTime = 2020-06-21T00:00:00 になります)。

## <a name="span-idhttphttp-requestspan"></a><span id="http">HTTP 要求</span>

* **要求 URL**:JSON を返すことができる HTTP URL。 プレースホルダー %Y、%m、%d、%h、%M がサポートされています。%Y = 形式 yyyy の年、%m = MM 形式の月、%d = dd 形式の日、%h = HH 形式の時間、%M = mm 形式の分です。 (例: `http://microsoft.com/ProjectA/%Y/%m/X_%Y-%m-%d-%h-%M`)。
* **HTTP 要求メソッド**:GET または POST を使用します。
* **要求ヘッダー**:基本認証を追加できます。 
* **要求ペイロード**:JSON ペイロードのみがサポートされています。 このペイロードでは、プレースホルダー @StartTime がサポートされています。 応答は、次の JSON 形式である必要があります: [{"timestamp":"2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23}, {"timestamp":"2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}].(例: 2020-06-21T00:00:00Z のデータが取り込まれると、@StartTime = 2020-06-21T00:00:00.0000000+00:00 になります)

## <a name="span-idinfluxdbinfluxdb-influxqlspan"></a><span id="influxdb">InfluxDB (InfluxQL)</span>

* **接続文字列**:InfluxDB にアクセスするための接続文字列。
* **データベース**:クエリ対象のデータベース。
* **Query**: データを取得して、インジェスト用の多次元時系列データに編成するクエリ。
* **ユーザー名**:これは、認証に対しては省略可能です。 
* **パスワード**:これは、認証に対しては省略可能です。 

## <a name="span-idmongodbmongodbspan"></a><span id="mongodb">MongoDB</span>

* **接続文字列**:MongoDB にアクセスするための接続文字列。
* **データベース**:クエリ対象のデータベース。
* **コマンド**:データを取得して、インジェスト用の多次元時系列データに編成するコマンド。

## <a name="span-idmysqlmysqlspan"></a><span id="mysql">MySQL</span>

* **接続文字列**:MySQL DB にアクセスするための接続文字列。
* **Query**: データを取得して、インジェスト用の多次元時系列データに編成するクエリ。

## <a name="span-idpgsqlpostgresqlspan"></a><span id="pgsql">PostgreSQL</span>

* **接続文字列**:PostgreSQL DB にアクセスするための接続文字列。
* **Query**: データを取得して、インジェスト用の多次元時系列データに編成するクエリ。

## <a name="next-steps"></a>次のステップ

* メトリック データがシステムに取り込まれるのを待つ間、[データ フィード構成の管理方法](how-tos/manage-data-feeds.md)についてお読みください。
* メトリック データが取り込まれたら、[メトリックを構成し、検出構成を微調整](how-tos/configure-metrics.md)できます。