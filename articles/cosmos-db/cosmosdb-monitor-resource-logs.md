---
title: Azure の診断設定を使用して Azure Cosmos DB データを監視する
description: Azure の診断設定を使用して、Azure Cosmos DB に格納されているデータのパフォーマンスと可用性を監視する方法について説明します
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 01/06/2021
ms.author: sngun
ms.openlocfilehash: 1e551fc12da5e25ba54df5a6a38a49b76f7c376e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102181875"
---
# <a name="monitor-azure-cosmos-db-data-by-using-diagnostic-settings-in-azure"></a>Azure の診断設定を使用して Azure Cosmos DB データを監視する
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure の診断設定は、リソース ログの収集に使用されます。 Azure のリソース ログは、リソースによって出力され、そのリソースの操作に関する豊富なデータを提供します。 これらのログは、要求ごとにキャプチャされ、"データ プレーン ログ" とも呼ばれます。 データ プレーン操作の例としては、delete、insert、readFeed などがあります。 これらのログの内容は、リソースの種類によって異なります。

プラットフォーム メトリックとアクティビティ ログは自動的に収集されますが、リソース ログを収集したり、それらを Azure Monitor の外部に転送したりするには、診断設定を作成する必要があります。 次の手順に従って、Azure Cosmos アカウントの診断設定を有効にすることができます。

1. [Azure Portal](https://portal.azure.com) にサインインします。

1. Azure Cosmos アカウントに移動します。 **[診断設定]** ウィンドウを開き、 **[診断設定の追加]** オプションを選択します。

1. **[診断設定]** ウィンドウで、以下の詳細情報をフォームに入力します。 

    * **Name**:作成するログの名前を入力します。

    * **[ストレージ アカウントへのアーカイブ]** 、 **[イベント ハブへのストリーム]** 、または **[Log Analytics への送信]** の方法で、ログを格納できます。

1. 診断設定を作成するときは、収集するログのカテゴリを指定します。 Azure Cosmos DB でサポートされているログのカテゴリと、それらによって収集されるサンプル ログを次に示します。

 * **DataPlaneRequests**:Azure Cosmos DB 内の SQL API アカウントへのバックエンド要求をログに記録するには、このオプションを選択します。 注意すべき重要なプロパティは、`Requestcharge`、`statusCode`、`clientIPaddress`、`partitionID`、`resourceTokenPermissionId`、および `resourceTokenPermissionMode` です。

   ```json
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372", "resourceTokenPermissionId": "perm-prescriber-app","resourceTokenPermissionMode": "all", "resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
   ```
   
   次のクエリを使用して、データ プレーン要求に対応するログを取得します。
  
   ```kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   ```

* **MongoRequests**:Azure Cosmos DB の MongoDB 用 API の要求にサービスを提供するために、ユーザーがフロントエンドから開始した要求をログに記録するには、このオプションを選択します。 このログの種類は、その他の API アカウントでは使用できません。 注意すべき重要なプロパティは、`Requestcharge`、`opCode` です。 診断ログで MongoRequests を有効にする場合は、DataPlaneRequests をオフにしてください。 API に対して行われたすべての要求に対して 1 つのログが表示されます。

    ```json
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```
  
  次のクエリを使用して、MongoDB 要求に対応するログを取得します。
  
  ```kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="MongoRequests"
  ```

* **CassandraRequests**:フロント エンドからユーザーが開始した要求をログに記録して、Cassandra 用の Azure Cosmos DB の API への要求を処理する場合に、このオプションを選択します。 このログの種類は、その他の API アカウントでは使用できません。 注意する重要なプロパティは、`operationName`、`requestCharge`、`piiCommandText` です。 診断ログで CassandraRequests を有効にする場合は、DataPlaneRequests をオフにしてください。 API に対して行われたすべての要求に対して 1 つのログが表示されます。

   ```json
   { "time": "2020-03-30T23:55:10.9579593Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "CassandraRequests", "operationName": "QuerySelect", "properties": {"activityId": "6b33771c-baec-408a-b305-3127c17465b6","opCode": "<empty>","errorCode": "-1","duration": "0.311900","requestCharge": "1.589237","databaseName": "system","collectionName": "local","retryCount": "<empty>","authorizationTokenType": "PrimaryMasterKey","address": "104.42.195.92","piiCommandText": "{"request":"SELECT key from system.local"}","userAgent": """"}}
   ```
   
  次のクエリを使用して、Cassandra 要求に対応するログを取得します。
  
  ```kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="CassandraRequests"
  ```

* **GremlinRequests**:Azure Cosmos DB の Gremlin 用 API の要求にサービスを提供するために、ユーザーがフロントエンドから開始した要求をログするには、このオプションを選択します。 このログの種類は、その他の API アカウントでは使用できません。 注意すべき重要なプロパティは、`operationName` と `requestCharge` です。 診断ログで GremlinRequests を有効にする場合は、必ず DataPlaneRequests をオフにしてください。 API に対して行われたすべての要求に対して 1 つのログが表示されます。

  ```json
  { "time": "2021-01-06T19:36:58.2554534Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "GremlinRequests", "operationName": "eval", "properties": {"activityId": "b16bd876-0e5c-4448-90d1-7f3134c6b5ff", "errorCode": "200", "duration": "9.6036", "requestCharge": "9.059999999999999", "databaseName": "GraphDemoDatabase", "collectionName": "GraphDemoContainer", "authorizationTokenType": "PrimaryMasterKey", "address": "98.225.2.189", "estimatedDelayFromRateLimitingInMilliseconds": "0", "retriedDueToRateLimiting": "False", "region": "Australia East", "requestLength": "266", "responseLength": "364", "userAgent": "<empty>"}}
  ```
  
  次のクエリを使用して、Gremlin 要求に対応するログを取得します。
  
  ```kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="GremlinRequests"
  ```

* **QueryRuntimeStatistics**:実行されたクエリ テキストをログに記録するには、このオプションを選択します。 この種類のログは、SQL API アカウントでのみ使用できます。

    ```json
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* **PartitionKeyStatistics**: パーティション キーの統計をログに記録するには、このオプションを選択します。 これは現在、パーティション キーのストレージ サイズ (KB) で表されます。 この記事にある [Azure の診断クエリを使用した問題のトラブルシューティング](#diagnostic-queries)に関するセクションを参照してください。 たとえば、"PartitionKeyStatistics" を使用するクエリなどです。 ログは、ほとんどのデータ ストレージを占有する最初の 3 つのパーティション キーに対して出力されます。 このログには、サブスクリプション ID、リージョン名、データベース名、コレクション名、パーティション キー、ストレージ サイズ (KB 単位) などのデータが含まれています。

    ```json
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* **PartitionKeyRUConsumption**:このログでは、パーティション キーの RU (秒単位) の合計消費量が報告されます。 現在のところ Azure Cosmos DB では、SQL API アカウントのみについて、およびポイントの読み取りまたは書き込みとストアド プロシージャ操作について、パーティション キーが報告されます。 その他の API と操作の種類はサポートされていません。 その他の API の場合、診断ログ テーブルのパーティション キー列は空になります。 このログには、サブスクリプション ID、リージョン名、データベース名、コレクション名、パーティション キー、操作の種類、要求の使用量などのデータが含まれています。 この記事にある [Azure の診断クエリを使用した問題のトラブルシューティング](#diagnostic-queries)に関するセクションを参照してください。 たとえば、"PartitionKeyRUConsumption" を使用するクエリなどです。 

* **ControlPlaneRequests**:このログには、アカウントの作成、リージョンの追加または削除、アカウントのレプリケーション設定の更新など、コントロール プレーンの操作に関する詳細が含まれています。この種類のログは、SQL (コア)、MongoDB、Gremlin、Cassandra、Table API などのすべての種類の API で使用できます。

* **要求**: Azure Cosmos DB からメトリック データを収集し、診断設定で指定した場所に送信するには、このオプションを選択します。 これは、Azure のメトリックで自動的に収集されるものと同じデータです。 リソース ログを使用してメトリック データを収集し、両方の種類のデータをまとめて分析し、Azure Monitor の外部にメトリック データを送信します。

Azure portal、CLI、または PowerShell を使用して診断設定を作成する方法の詳細については、「[Azure でプラットフォーム ログとメトリックを収集するための診断設定を作成する](../azure-monitor/essentials/diagnostic-settings.md)」を参照してください。


## <a name="troubleshoot-issues-with-diagnostics-queries"></a><a id="diagnostic-queries"></a> 診断クエリに関する問題のトラブルシューティング

1. 実行に 3 ミリ秒より長くかかっている操作のクエリを実行する方法:

   ```Kusto
   AzureDiagnostics 
   | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | summarize count() by clientIpAddress_s, TimeGenerated
   ```

1. 操作を実行中のユーザー エージェントのクエリを実行する方法:

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | summarize count() by OperationName, userAgent_s
   ```

1. 長時間かかる操作のクエリを実行する方法:

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | project TimeGenerated , duration_s 
   | summarize count() by bin(TimeGenerated, 5s)
   | render timechart
   ```
    
1. パーティション キーの統計を取得して、データベース アカウントの上位 3 つのパーティション間のずれを評価する方法:

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
   | project SubscriptionId, regionName_s, databaseName_s, collectionName_s, partitionKey_s, sizeKb_d, ResourceId 
   ```

1. コストの高いクエリについて要求の使用量を取得する方法

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 10.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
   AzureDiagnostics
   | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
   | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

1. RU (秒単位) の多くを使用している操作を見つける方法

    ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(responseLength_s), max(requestLength_s), max(requestCharge_s), count = count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```

1. **DataPlaneRequests** と **QueryRunTimeStatistics** のデータと結合された、消費量が 100 RU/秒を上回るすべてのクエリを取得する方法。

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 100.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
           AzureDiagnostics
           | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
           | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

1. 要求の料金とクエリの実行時間を取得する方法

   ```kusto
   AzureDiagnostics
   | where TimeGenerated >= ago(24hr)
   | where Category == "QueryRuntimeStatistics"
   | join (
   AzureDiagnostics
   | where TimeGenerated >= ago(24hr)
   | where Category == "DataPlaneRequests"
   ) on $left.activityId_g == $right.activityId_g
   | project databasename_s, collectionname_s, OperationName1 , querytext_s,requestCharge_s1, duration_s1, bin(TimeGenerated, 1min)
   ```


1. さまざまな操作の分布を取得する方法

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize count = count()  by OperationName, requestResourceType_s, bin(TimeGenerated, 1h) 
   ```

1. パーティションで消費される最大スループット量

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(requestCharge_s) by bin(TimeGenerated, 1h), partitionId_g
   ```

1. パーティション キーの RU (秒単位) の消費量に関する情報を取得する方法

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s, TimeGenerated 
   | order by TimeGenerated asc 
   ```

1. 特定のパーティション キーについて要求の使用量を取得する方法

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where parse_json(partitionKey_s)[0] == "2" 
   ```

1. 特定の期間に RU (秒単位) の多くが消費された上位のパーティション キーを取得する方法 

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where TimeGenerated >= datetime("11/26/2019, 11:20:00.000 PM") and TimeGenerated <= datetime("11/26/2019, 11:30:00.000 PM") 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s 
   | order by total desc
    ```

1. ストレージ サイズが 8 GB を超えるパーティション キーのログを取得する方法

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics"
   | where todouble(sizeKb_d) > 8000000
   ```

1. 操作、要求の課金、または応答の長さの P99 または P50 レプリケーションの待機時間を取得する方法

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2d)
   | summarize
   percentile(todouble(responseLength_s), 50), percentile(todouble(responseLength_s), 99), max(responseLength_s),
   percentile(todouble(requestCharge_s), 50), percentile(todouble(requestCharge_s), 99), max(requestCharge_s),
   percentile(todouble(duration_s), 50), percentile(todouble(duration_s), 99), max(duration_s),
   count()
   by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```
 
1. ControlPlane ログを取得する方法
 
   必ず[キーベースのメタデータ書き込みアクセスを無効にする方法](audit-control-plane-logs.md#disable-key-based-metadata-write-access)に関する記事の説明に従ってフラグをオンに切り替えてから、Azure PowerShell、Azure CLI、または Azure Resource Manager を使用して操作を実行してください。
 
   ```Kusto  
   AzureDiagnostics 
   | where Category =="ControlPlaneRequests"
   | summarize by OperationName 
   ```

## <a name="next-steps"></a>次のステップ

* [Azure Monitor for Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json)
* [Azure Cosmos DB のメトリックを使用した監視とデバッグ](use-metrics.md)
