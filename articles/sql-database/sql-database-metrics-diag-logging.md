---
title: "Azure SQL Database のメトリックと診断のロギング | Microsoft Docs"
description: "Azure SQL Database リソースを構成してリソースの使用状況、接続性、およびクエリ実行の統計情報を保存する方法について説明します。"
services: sql-database
documentationcenter: 
author: vvasic
manager: jhubbard
editor: 
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2017
ms.author: vvasic
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ef73f9036a91d5bac50597d1d96fe134225eef51
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Azure SQL Database のメトリックと診断のロギング 
Azure SQL Database では、監視を容易にするためのメトリックと診断ログを出力することができます。 リソース使用率、ワーカーとセッション、および接続性を次の Azure リソースのいずれかに格納するように Azure SQL Database を構成できます。
- **Azure Storage**: 大量のテレメトリを低価格でアーカイブします
- **Azure イベント ハブ**: Azure SQL Database のテレメトリを、カスタム監視ソリューションまたはホット パイプラインと統合します
- **Azure Log Analytics**: レポート機能、アラート機能、および移行機能を備えた既製の監視ソリューション用です 

    ![アーキテクチャ](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="enable-logging"></a>ログの有効化

メトリックや診断のロギングは既定では有効になっていません。 次のいずれかの方法を使用してメトリックと診断のロギングを有効にして管理できます。
- Azure ポータル
- PowerShell
- Azure CLI
- REST API 
- Resource Manager テンプレート

メトリックと診断のロギングを有効にする際に、選択したデータが収集される Azure リソースを指定する必要があります。 次のオプションを使用できます。
- Log Analytics
- イベント ハブ
- Azure Storage (Azure Storage) 

新しい Azure リソースをプロビジョニングするか、既存のリソースを選択できます。 ストレージ リソースを選択したら、収集するデータを指定する必要があります。 次のオプションを使用できます。

- **[1 分メトリック](sql-database-metrics-diag-logging.md#1-minute-metrics)** - DTU の割合、DTU の上限、CPU の割合、物理データ読み取りの割合、ログ書き込みの割合、ファイアウォール接続による成功/失敗/ブロック、セッションの割合、ワーカーの割合、ストレージ、ストレージの割合、XTP ストレージの割合が含まれます。
- **[QueryStoreRuntimeStatistics](sql-database-metrics-diag-logging.md#query-store-runtime-statistics)** - CPU 使用率、クエリ実行時間など、クエリのランタイム統計に関する情報が含まれます。
- **[QueryStoreWaitStatistics](sql-database-metrics-diag-logging.md#query-store-wait-statistics)** - CPU、ログ、ロック状態など、クエリが何を待機したかを示すクエリ待機統計に関する情報が含まれます。
- **[Errors](sql-database-metrics-diag-logging.md#errors-dataset)** - このデータベースで発生した SQL エラーに関する情報が含まれます。
- **[DatabaseWaitStatistics](sql-database-metrics-diag-logging.md#database-waits-dataset)** - データベースがさまざまな種類の待機に費やした時間に関する情報が含まれます。
- **[Timeouts](sql-database-metrics-diag-logging.md#timeouts-dataset)** - データベースがさまざまな種類の待機に費やした時間に関する情報が含まれます。
- **[Blockings](sql-database-metrics-diag-logging.md#blockings-dataset)** - データベースで発生したブロック イベントに関する情報が含まれます。
- **[SQLInsights](sql-database-metrics-diag-logging.md#intelligent-insights-dataset)** - Intelligent Insights が含まれます。 「[Intelligent Insights](sql-database-intelligent-insights.md)」をご覧ください

イベント ハブまたは AzureStorage アカウントを指定した場合は、リテンション期間ポリシーを指定して、選択した期間より古いデータを削除するように指定できます。 Log Analytics を指定した場合、リテンション期間ポリシーは選択した価格レベルに依存します。 [Log Analytics の価格](https://azure.microsoft.com/pricing/details/log-analytics/)に関するページをご覧ください。 

「[Microsoft Azure のメトリックの概要](../monitoring-and-diagnostics/monitoring-overview-metrics.md)」と [Azure 診断ログの概要](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)に関する記事の両方をご覧になり、ロギングを有効にする方法だけでなく、各種 Azure サービスでサポートされるメトリックとログのカテゴリーについて理解を深めることをお勧めします。

### <a name="azure-portal"></a>Azure ポータル

Azure Portal でメトリックと診断のログ収集を有効にするには、Azure SQL Database またはエラスティック プールのページに移動して、**[診断設定]** をクリックします。

   ![Azure Portal で有効にする](./media/sql-database-metrics-diag-logging/enable-portal.png)

ターゲットとテレメトリを選択して、新規診断設定を作成するか、既存の診断設定を編集します。

   ![診断設定の構成](./media/sql-database-metrics-diag-logging/diagnostics-portal.png)

### <a name="powershell"></a>PowerShell

PowerShell を使用してメトリックと診断のロギングを有効にするには、次のコマンドを使用します。

- ストレージ アカウントへの診断ログの保存を有効にするには、次のコマンドを使用します。

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   ストレージ アカウント ID は、ログの送信先となるストレージ アカウントのリソース ID です。

- Event Hubs への診断ログのストリーミングを有効にするには、次のコマンドを使用します。

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Service Bus 規則 ID はこの形式の文字列です。

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Log Analytics ワークスペースへの診断ログの送信を有効にするには、次のコマンドを使用します。

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- 次のコマンドを使用して、Log Analytics ワークスペースのリソース IDを取得できます。

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

このパラメーターを組み合わせて、複数の出力オプションを有効にできます。

### <a name="azure-cli"></a>Azure CLI

Azure CLI を使用してメトリックと診断のロギングを有効にするには、次のコマンドを使用します。

- ストレージ アカウントへの診断ログの保存を有効にするには、次のコマンドを使用します。

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   ストレージ アカウント ID は、ログの送信先となるストレージ アカウントのリソース ID です。

- Event Hubs への診断ログのストリーミングを有効にするには、次のコマンドを使用します。

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   Service Bus 規則 ID はこの形式の文字列です。

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Log Analytics ワークスペースへの診断ログの送信を有効にするには、次のコマンドを使用します。

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

このパラメーターを組み合わせて、複数の出力オプションを有効にできます。

### <a name="rest-api"></a>REST API

[Azure Monitor REST API を使用して診断設定を変更する](https://msdn.microsoft.com/library/azure/dn931931.aspx)方法をご覧ください。 

### <a name="resource-manager-template"></a>Resource Manager テンプレート

[Resource Manager テンプレートを使用してリソースの作成時に診断設定を有効にする](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md)方法をご覧ください。 

## <a name="stream-into-log-analytics"></a>Log Analytics にストリーミングする 
Azure SQL Database のメトリックと診断のログは、ポータルの組み込みの [Log Analytics への送信] オプションを使用するか、診断設定で Azure PowerShell コマンドレット、Azure CLI、または Azure Monitor REST API を使用して Log Analytics を有効にすることで、Log Analytics にストリーミングできます。

### <a name="installation-overview"></a>インストールの概要

Log Analytics を使用すると、Monitoring Azure SQL Database フリートを簡単に監視できます。 次の 3 つの手順が必要です。

1. Log Analytics リソースを作成する
2. 作成した Log Analytics にメトリックと診断のログを記録するデータベースを構成する
3. Log Analytics のギャラリーから **Azure SQL Analytics** ソリューションをインストールする

### <a name="create-log-analytics-resource"></a>Log Analytics リソースを作成する

1. 左側のメニューで **[新規]** をクリックします。
2. **[監視 + 管理]** をクリックします。
3. **[Log Analytics]** をクリックします。
4. Log Analytics フォームに必要な追加情報 (ワークスペースの名前、サブスクリプション、リソース グループ、場所、価格レベル) を入力します。

   ![Log Analytics](./media/sql-database-metrics-diag-logging/log-analytics.png)

### <a name="configure-databases-to-record-metrics-and-diagnostic-logs"></a>メトリックと診断のログを記録するデータベースを構成する

データベースがメトリックを記録する場所を構成する最も簡単な方法は、Azure Portal を使用する方法です。 Azure Portal で、Azure SQL Database リソースに移動し、**[診断設定]** をクリックします。 

### <a name="install-the-azure-sql-analytics-solution-from-gallery"></a>ギャラリーから Azure SQL Analytics ソリューションをインストールする  

1. Log Analytics リソースが作成され、データがそこにフローするようになったら、Azure SQL Analytics ソリューションをインストールします。 これは、OMS のホームページおよびサイド メニューにある**ソリューション ギャラリー**から行うことができます。 ギャラリーで **Azure SQL Analytics** を探してクリックし、**[追加]** をクリックします。

   ![ソリューションの監視](./media/sql-database-metrics-diag-logging/monitoring-solution.png)

2. OMS のホームページに、**Azure SQL Analytics** と呼ばれる新しいタイルが表示されます。 このタイルを選択すると、Azure SQL Analytics ダッシュボードが開きます。

### <a name="using-azure-sql-analytics-solution"></a>Azure SQL Analytics ソリューションを使用する

Azure SQL Analysis は階層型のダッシュボードで、ユーザーは Azure SQL Database リソースの階層を移動できます。 Azure SQL Analytics ソリューションの使用方法については、[こちら](../log-analytics/log-analytics-azure-sql.md)をクリックしてください。

## <a name="stream-into-azure-event-hub"></a>Azure イベント ハブにストリーミングする

Azure SQL Database のメトリックと診断のログは、ポータルの組み込みの [イベント ハブへのストリーム] オプションを使用するか、診断設定で Azure PowerShell コマンドレット、Azure CLI、または Azure Monitor REST API を使用して Service Bus 規則 ID を有効にすることで、イベント ハブにストリーミングできます。 

### <a name="what-to-do-with-metrics-and-diagnostic-logs-in-event-hub"></a>イベント ハブにおけるメトリックと診断のログの活用方法
選択したデータがイベント ハブにストリーミングされると、高度な監視シナリオが有効になるのに一歩近づきます。 Event Hubs はイベント パイプラインの "玄関" として機能し、Event Hubs に収集されたデータは、任意のリアルタイム分析プロバイダーまたはバッチ処理/ストレージ アダプターを使用して変換および格納できます。 Event Hubs はイベント ストリームの生成とイベントの使用を分離し、イベント コンシューマーが独自のスケジュールでイベントにアクセスできるようにします。 イベント ハブについて詳しくは、次をご覧ください。

- [Azure Event Hubs とは](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Event Hubs の使用](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)


ストリーミング機能を使用する場合、次のような方法があります。

-             "ホットパス" データを PowerBI にストリーミングしてサービスの正常性を表示する - Event Hubs、Stream Analytics、PowerBI を使用して、メトリックや診断データを Azure サービスに関するほぼリアルタイムの洞察に簡単に変換できます。 Event Hubs をセットアップし、Stream Analytics でデータを処理して、PowerBI を出力として使用する方法の概要については、「[Stream Analytics と Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)」をご覧ください。
-             サードパーティ製のロギングおよびテレメトリ ストリームにログをストリーミングする - Event Hubs のストリーミングを使用して、メトリックおよび診断ログが別のサードパーティ製の監視およびログ分析ソリューションに入るように設定できます。 
-             カスタムのテレメトリおよびログ プラットフォームを構築する - カスタム構築されたテレメトリ プラットフォームが既にある場合や構築を検討している場合は、Event Hubs の非常にスケーラブルな発行/サブスクライブの特性により、診断ログを柔軟に取り込むことができます。 [グローバル規模のテレメトリ プラットフォームで Event Hubs を使用する方法に関する Dan Rosanova によるガイド](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)をご覧ください。

## <a name="stream-into-azure-storage"></a>Azure Storage にストリーミングする

Azure SQL Database のメトリックと診断のログは、Azure Portal の組み込みの [ストレージ アカウントへのアーカイブ] オプションを使用するか、診断設定で Azure PowerShell コマンドレット、Azure CLI、または Azure Monitor REST API を使用して Azure Storage を有効にすることで、Azure Storage にストリーミングできます。

### <a name="schema-of-metrics-and-diagnostic-logs-in-the-storage-account"></a>ストレージ アカウントにおけるメトリックおよび診断ログのスキーマ

メトリックおよび診断ログのコレクションをセットアップ後、データの先頭行が利用できるようになると、選択したストレージ アカウントにストレージ コンテナーが作成されます。 これらのBLOB の構造は次のとおりです。

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
    
または、次のようによりシンプルな構造になります。

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

たとえば、1 分メトリックの BLOB 名は次のようになります。

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

エラスティック プールからデータを記録する場合、次のように BLOB 名が少し変わります。

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-azure-storage"></a>Azure Storage からメトリックとログをダウンロードする

[Azure Storage からメトリックとログをダウンロードする方法](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs)に関する記事をご覧ください。

## <a name="metrics-and-logs-available"></a>利用可能なメトリックとログ

### <a name="1-minute-metrics"></a>1 分メトリック

|**リソース**|**メトリック**|
|---|---|
|データベース|DTU の割合、使用中の DTU、DTU の上限、CPU の割合、物理データ読み取りの割合、ログ書き込みの割合、ファイアウォール接続による成功/失敗/ブロック、セッションの割合、ワーカーの割合、ストレージ、ストレージの割合、XTP ストレージの割合、デッドロック |
|エラスティック プール|eDTU の割合、使用中の eDTU、eDTU の上限、CPU の割合、物理データ読み取りの割合、ログ書き込みの割合、セッションの割合、ワーカーの割合、ストレージ、ストレージの割合、ストレージの上限、XTP ストレージの割合 |
|||

### <a name="query-store-runtime-statistics"></a>クエリ ストアのランタイム統計

|プロパティ|Description|
|---|---|
|TenantId|テナント ID。|
|SourceSystem|常に Azure|
|TimeGenerated [UTC]|ログが記録されたときのタイムスタンプ。|
|型|常に AzureDiagnostics|
|ResourceProvider|リソース プロバイダーの名前。 常に MICROSOFT.SQL|
|カテゴリ|カテゴリの名前。 常に QueryStoreRuntimeStatistics|
|OperationName|操作の名前。 常に QueryStoreRuntimeStatisticsEvent|
|リソース|リソースの名前|
|ResourceType|リソースの種類の名前。 常に SERVERS/DATABASES|
|SubscriptionId|データベースが属するサブスクリプション GUID。|
|ResourceGroup|データベースが属するリソース グループの名前。|
|LogicalServerName_s|データベースが属するサーバーの名前。|
|ElasticPoolName_s|データベースが属するエラスティック プールの名前 (存在する場合)。|
|DatabaseName_s|データベースの名前|
|ResourceId|リソース URI|
|query_hash_s|クエリ ハッシュ|
|query_plan_hash_s|クエリ プラン ハッシュ|
|statement_sql_handle_s|ステートメント sql ハンドル|
|interval_start_time_d|1900-1-1 からのティック数での間隔の開始 datetimeoffset。|
|interval_end_time_d|1900-1-1 からのティック数での間隔の終了 datetimeoffset。|
|logical_io_writes_d|論理 IO 書き込みの合計数。|
|max_logical_io_writes_d|実行ごとの論理 IO 書き込みの最大数。|
|physical_io_reads_d|物理 IO 読み取りの合計数。|
|max_physical_io_reads_d|実行ごとの論理 IO 読み取りの最大数。|
|logical_io_reads_d|論理 IO 読み取りの合計数。|
|max_logical_io_reads_d|実行ごとの論理 IO 読み取りの最大数。|
|execution_type_d|実行の種類|
|count_executions_d|クエリの実行回数。|
|cpu_time_d|クエリで使用された合計 CPU 時間 (マイクロ秒単位)。|
|max_cpu_time_d|1 回の実行による最大 CPU 時間コンシューマー (マイクロ秒単位)。|
|dop_d|並列処理の次数の合計。|
|max_dop_d|1 回の実行で使用された並列処理の最大次数。|
|rowcount_d|返された行数の合計。|
|max_rowcount_d|1 回の実行で返された行の最大数。|
|query_max_used_memory_d|サポート技術情報で使用されたメモリの合計量 (KB 単位)。|
|max_query_max_used_memory_d|1 回の実行で使用されたメモリの最大量 (KB 単位)。|
|duration_d|合計実行時間 (マイクロ秒単位)。|
|max_duration_d|1 回の実行の最大実行時間。|
|num_physical_io_reads_d|物理読み取りの合計数。|
|max_num_physical_io_reads_d|実行ごとの物理読み取りの最大数。|
|log_bytes_used_d|使用されたログの合計量 (バイト数)。|
|max_log_bytes_used_d|実行ごとに使用されたログの最大量 (バイト数)。|
|query_id_d|クエリ ストアでのクエリの ID|
|plan_id_d|クエリ ストアでのプランの ID|

[クエリ ストアのランタイム統計データの詳細。](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql)

### <a name="query-store-wait-statistics"></a>クエリ ストアの待機統計

|プロパティ|Description|
|---|---|
|TenantId|テナント ID。|
|SourceSystem|常に Azure|
|TimeGenerated [UTC]|ログが記録されたときのタイムスタンプ。|
|型|常に AzureDiagnostics|
|ResourceProvider|リソース プロバイダーの名前。 常に MICROSOFT.SQL|
|カテゴリ|カテゴリの名前。 常に QueryStoreRuntimeStatistics|
|OperationName|操作の名前。 常に QueryStoreRuntimeStatisticsEvent|
|リソース|リソースの名前|
|ResourceType|リソースの種類の名前。 常に SERVERS/DATABASES|
|SubscriptionId|データベースが属するサブスクリプション GUID。|
|ResourceGroup|データベースが属するリソース グループの名前。|
|LogicalServerName_s|データベースが属するサーバーの名前。|
|ElasticPoolName_s|データベースが属するエラスティック プールの名前 (存在する場合)。|
|DatabaseName_s|データベースの名前|
|ResourceId|リソース URI|
|wait_category_s|待機のカテゴリ。|
|is_parameterizable_s|クエリがパラメーター化可能かどうか。|
|statement_type_s|ステートメントの種類。|
|statement_key_hash_s|ステートメント キー ハッシュ。|
|exec_type_d|実行の種類|
|total_query_wait_time_ms_d|特定の待機カテゴリでのクエリの合計待機時間。|
|max_query_wait_time_ms_d|特定の待機のカテゴリの個々の実行でのクエリの最大待機時間|
|query_param_type_d|0|
|query_hash_s|クエリ ストア内のクエリ ハッシュ。|
|query_plan_hash_s|クエリ ストア内のクエリ プラン ハッシュ|
|statement_sql_handle_s|クエリ ストア内のステートメント ハンドル|
|interval_start_time_d|1900-1-1 からのティック数での間隔の開始 datetimeoffset。|
|interval_end_time_d|1900-1-1 からのティック数での間隔の終了 datetimeoffset。|
|count_executions_d|クエリの実行回数|
|query_id_d|クエリ ストアでのクエリの ID|
|plan_id_d|クエリ ストアでのプランの ID|

クエリ ストアの待機統計データの詳細については、[こちら](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql)をクリックしてください。

### <a name="errors-dataset"></a>エラー データセット

|プロパティ|Description|
|---|---|
|TenantId|テナント ID。|
|SourceSystem|常に Azure|
|TimeGenerated [UTC]|ログが記録されたときのタイムスタンプ。|
|型|常に AzureDiagnostics|
|ResourceProvider|リソース プロバイダーの名前。 常に MICROSOFT.SQL|
|カテゴリ|カテゴリの名前。 常に QueryStoreRuntimeStatistics|
|OperationName|操作の名前。 常に QueryStoreRuntimeStatisticsEvent|
|リソース|リソースの名前|
|ResourceType|リソースの種類の名前。 常に SERVERS/DATABASES|
|SubscriptionId|データベースが属するサブスクリプション GUID。|
|ResourceGroup|データベースが属するリソース グループの名前。|
|LogicalServerName_s|データベースが属するサーバーの名前。|
|ElasticPoolName_s|データベースが属するエラスティック プールの名前 (存在する場合)。|
|DatabaseName_s|データベースの名前|
|ResourceId|リソース URI|
|メッセージ|プレーンテキストでのエラー メッセージ|
|user_defined_b|エラーがユーザー定義ビットかどうか|
|error_number_d|エラー コード|
|Severity|エラーの重大度|
|state_d|エラーの状態|
|query_hash_s|使用可能な場合は、失敗したクエリのクエリ ハッシュ|
|query_plan_hash_s|使用可能な場合は、失敗したクエリのクエリ プラン ハッシュ|

[SQL Server エラー メッセージ](https://msdn.microsoft.com/en-us/library/cc645603.aspx)

### <a name="database-waits-dataset"></a>データベース待機データセット

|プロパティ|Description|
|---|---|
|TenantId|テナント ID。|
|SourceSystem|常に Azure|
|TimeGenerated [UTC]|ログが記録されたときのタイムスタンプ。|
|型|常に AzureDiagnostics|
|ResourceProvider|リソース プロバイダーの名前。 常に MICROSOFT.SQL|
|カテゴリ|カテゴリの名前。 常に QueryStoreRuntimeStatistics|
|OperationName|操作の名前。 常に QueryStoreRuntimeStatisticsEvent|
|リソース|リソースの名前|
|ResourceType|リソースの種類の名前。 常に SERVERS/DATABASES|
|SubscriptionId|データベースが属するサブスクリプション GUID。|
|ResourceGroup|データベースが属するリソース グループの名前。|
|LogicalServerName_s|データベースが属するサーバーの名前。|
|ElasticPoolName_s|データベースが属するエラスティック プールの名前 (存在する場合)。|
|DatabaseName_s|データベースの名前|
|ResourceId|リソース URI|
|wait_type_s|待機の種類の名前|
|start_utc_date_t [UTC]|測定期間の開始時刻。|
|end_utc_date_t [UTC]|測定期間の終了時刻。|
|delta_max_wait_time_ms_d|実行ごとの最大待機時間|
|delta_signal_wait_time_ms_d|合計シグナルの待機時間|
|delta_wait_time_ms_d|期間内の合計待機時間|
|delta_waiting_tasks_count_d|待機中のタスク数|

データベース待機統計の詳細については、[こちら](https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)をクリックしてください。

### <a name="timeouts-dataset"></a>タイムアウト データセット

|プロパティ|Description|
|---|---|
|TenantId|テナント ID。|
|SourceSystem|常に Azure|
|TimeGenerated [UTC]|ログが記録されたときのタイムスタンプ。|
|型|常に AzureDiagnostics|
|ResourceProvider|リソース プロバイダーの名前。 常に MICROSOFT.SQL|
|カテゴリ|カテゴリの名前。 常に QueryStoreRuntimeStatistics|
|OperationName|操作の名前。 常に QueryStoreRuntimeStatisticsEvent|
|リソース|リソースの名前|
|ResourceType|リソースの種類の名前。 常に SERVERS/DATABASES|
|SubscriptionId|データベースが属するサブスクリプション GUID。|
|ResourceGroup|データベースが属するリソース グループの名前。|
|LogicalServerName_s|データベースが属するサーバーの名前。|
|ElasticPoolName_s|データベースが属するエラスティック プールの名前 (存在する場合)。|
|DatabaseName_s|データベースの名前|
|ResourceId|リソース URI|
|error_state_d|エラー状態コード|
|query_hash_s|クエリ ハッシュ (使用可能な場合)|
|query_plan_hash_s|クエリ プラン ハッシュ (使用可能な場合)|

### <a name="blockings-dataset"></a>ブロックしているデータセット

|プロパティ|Description|
|---|---|
|TenantId|テナント ID。|
|SourceSystem|常に Azure|
|TimeGenerated [UTC]|ログが記録されたときのタイムスタンプ。|
|型|常に AzureDiagnostics|
|ResourceProvider|リソース プロバイダーの名前。 常に MICROSOFT.SQL|
|カテゴリ|カテゴリの名前。 常に QueryStoreRuntimeStatistics|
|OperationName|操作の名前。 常に QueryStoreRuntimeStatisticsEvent|
|リソース|リソースの名前|
|ResourceType|リソースの種類の名前。 常に SERVERS/DATABASES|
|SubscriptionId|データベースが属するサブスクリプション GUID。|
|ResourceGroup|データベースが属するリソース グループの名前。|
|LogicalServerName_s|データベースが属するサーバーの名前。|
|ElasticPoolName_s|データベースが属するエラスティック プールの名前 (存在する場合)。|
|DatabaseName_s|データベースの名前|
|ResourceId|リソース URI|
|lock_mode_s|クエリで使用されるロック モード|
|resource_owner_type_s|ロックの所有者。|
|blocked_process_filtered_s|ブロックされているプロセスのレポート XML。|
|duration_d|ロック期間 (ミリ秒単位)。|

### <a name="intelligent-insights-dataset"></a>Intelligent Insights データセット
Intelligent Insights ログ形式の詳細については、[こちら](sql-database-intelligent-insights-use-diagnostics-log.md)をクリックしてください

## <a name="next-steps"></a>次のステップ

- 「[Microsoft Azure のメトリックの概要](../monitoring-and-diagnostics/monitoring-overview-metrics.md)」と [Azure 診断ログの概要](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)に関する記事の両方をご覧になり、ロギングを有効にする方法だけでなく、各種 Azure サービスでサポートされるメトリックとログのカテゴリーについて理解を深めます。
- Event Hubs については次の記事をご覧ください。
   - [Azure Event Hubs とは](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Event Hubs の使用](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- [Azure Storage からメトリックとログをダウンロードする方法](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs)に関する記事をご覧ください。

