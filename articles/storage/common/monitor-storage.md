---
title: Azure Storage を監視する | Microsoft Docs
description: Azure Storage のパフォーマンスと可用性を監視する方法について説明します。 Azure Storage のデータを監視します。構成、およびメトリックとログ データの分析について説明します。
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 2cc275028b64c3df06e2c0275bc0f4ac21fa0f6e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020152"
---
# <a name="monitor-azure-storage"></a>Azure Storage を監視する

Azure リソースに依存するクリティカルなアプリケーションとビジネス プロセスがある場合は、それらのリソースの可用性、パフォーマンス、操作を監視する必要があります。 この記事では、Azure Storage によって生成される監視データと、Azure Monitor の機能を使用してこのデータに関するアラートを分析する方法について説明します。

> [!NOTE]
> Azure Monitor の Azure Storage ログはパブリック プレビュー段階にあり、すべてのパブリック クラウド リージョンでプレビュー テスト用に使用できます。 プレビューに登録するには、[こちらのページ](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)を参照してください。 このプレビューでは、BLOB (Azure Data Lake Storage Gen2 を含む)、ファイル、キュー、およびテーブルに対してログが有効になります。 この機能は、Azure Resource Manager デプロイ モデルを使用して作成されたすべてのストレージ アカウントで使用できます。 「[ストレージ アカウントの概要](../common/storage-account-overview.md)」を参照してください。

## <a name="monitor-overview"></a>監視の概要

Azure portal で、各ストレージ リソースの **[概要]** ページには、リソースの要求や時間ごとの課金など、リソースの使用状況の要約が表示されます。 この情報は役立ちますが、使用できる監視データはごくわずかです。 このデータの一部は自動的に収集され、ストレージ リソースを作成するとすぐに、分析に使用できるようになります。 一定の構成によって追加の種類のデータ収集を有効にすることができます。

## <a name="what-is-azure-monitor"></a>Azure Monitor とは
Azure Storage は、Azure のフルスタック監視サービスである [Azure Monitor](../../azure-monitor/overview.md) を使用して監視データを作成します。 Azure Monitor には、Azure リソースと、他のクラウドおよびオンプレミスのリソースを監視するための完全な機能セットが用意されています。 

Azure Monitor の詳細については、「[Azure Monitor を使用した Azure リソースの監視](../../azure-monitor/insights/monitor-azure-resource.md)」をご覧ください。 この記事では、次のサブジェクトについて説明します。

- Azure Monitor とは
- 監視に関連するコスト
- Azure で収集される監視データ
- データ収集の構成
- 監視データの分析とアラート生成のための Azure の標準ツール

以下のセクションでは、この記事に基づき、Azure Storage から収集された特定のデータについて説明します。 例では、Azure ツールを使用してデータ コレクションを構成し、このデータを分析する方法について説明します。

## <a name="monitor-data-from-azure-storage"></a>Azure Storage からデータを監視する

Azure Storage では、他の Azure リソースと同じ種類の監視データが収集されます。これについては、[Azure リソースの監視データ](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data)に関するページをご覧ください。 Azure Storage によって作成されるログおよびメトリックの詳細については、「[Azure Storage 監視データのリファレンス](monitor-storage-reference.md)」をご覧ください。

Azure Monitor のメトリックとログでは、Azure Resource Manager ストレージ アカウントのみがサポートされています。 Azure Monitor では、従来のストレージ アカウントはサポートされていません。 従来のストレージ アカウントでメトリックまたはログを使用する場合、Azure Resource Manager ストレージ アカウントに移行する必要があります。 [Azure Resource Manager への移行](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview)に関するページを参照してください。

必要に応じて、従来のメトリックとログの使用を続けることができます。 実際に、従来のメトリックとログを Azure Monitor のメトリックとログと並行して使用できます。 このサポートは、Azure Storage で従来のメトリックとログのサービスが終了するまで引き続き提供されます。

### <a name="logs-in-azure-monitor-preview"></a>Azure Monitor のログ (プレビュー)

ログ エントリが作成されるのは、サービス エンドポイントに対して行われた要求がある場合に限られます。 たとえば、ストレージ アカウントの BLOB エンドポイントにはアクティビティが存在するが、テーブル エンドポイントまたはキュー エンドポイントには存在しない場合、BLOB サービスに関連したログだけが作成されます。 Azure Storage ログには、ストレージ サービスに対する要求の成功と失敗についての詳細な情報が含まれています。 この情報を使って個々の要求を監視したり、ストレージ サービスに関する問題を診断したりできます。 要求は、ベスト エフォートでログに記録されます。

#### <a name="log-authenticated-requests"></a>認証済み要求をログに記録する

 次のタイプの認証済み要求が記録されます。

- 成功した要求
- 失敗した要求 (タイムアウト、スロットル、ネットワーク、承認などに関する各種エラー)
- Shared Access Signature (SAS) または OAuth を使用した要求 (失敗した要求と成功した要求を含む)
- 分析データ ( **$logs** コンテナーの従来のログ データと、 **$metric** テーブルのクラス メトリック データ) に対する要求

ストレージ サービスそのものによる要求 (ログの作成や削除など) は記録されません。 ログに記録されるデータの完全な一覧については、[ストレージのログに記録された操作とステータス メッセージ](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)に関するページと、[ストレージのログの形式](monitor-storage-reference.md)に関するページをご覧ください。

#### <a name="log-anonymous-requests"></a>匿名要求をログに記録する

 次のタイプの匿名要求が記録されます。

- 成功した要求
- サーバー エラー
- クライアントとサーバーの両方のタイムアウト エラー
- エラー コード 304 (変更されていません) で失敗した GET 要求

その他の失敗した匿名要求は一切記録されません。 ログに記録されるデータの完全な一覧については、[ストレージのログに記録された操作とステータス メッセージ](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)に関するページと、[ストレージのログの形式](monitor-storage-reference.md)に関するページをご覧ください。

## <a name="configuration"></a>構成

プラットフォーム メトリックとアクティビティ ログは自動的に収集されますが、リソース ログを収集したり、それらを Azure Monitor の外部に転送したりするには、診断設定を作成する必要があります。 Azure portal、Azure CLI、または PowerShell を使用して診断設定を作成するプロセスについては、「[Azure でプラットフォーム ログとメトリックを収集するための診断設定を作成する](../../azure-monitor/platform/diagnostic-settings.md)」をご覧ください。

診断設定を作成するときに、BLOB、キュー、テーブル、ファイルなど、ログを有効にするストレージの種類を選択します。 Data Lake Storage Gen2 はストレージの種類として表示されません。 これは、Data Lake Storage Gen2 が BLOB ストレージで使用できる機能のセットであるためです。 

Azure portal で診断設定を作成する場合は、一覧からリソースを選択できます。 PowerShell または Azure CLI を使用する場合は、ストレージの種類のリソース ID を使用する必要があります。 ストレージ アカウントの **[プロパティ]** ページを開くと、Azure portal 上でリソース ID を確認できます。

また、ログを収集する操作のカテゴリを指定する必要もあります。 Azure Storage のカテゴリを次の表に示します。

| カテゴリ | 説明 |
|:---|:---|
| StorageRead | オブジェクトに対する読み取り操作。 |
| StorageWrite | オブジェクトに対する書き込み操作。 |
| StorageDelete | オブジェクトに対する削除操作。 |

## <a name="analyze-metric-data"></a>メトリック データを分析する

メトリックス エクスプローラーを使用して、他の Azure サービスのメトリックと共に Azure Storage のメトリックを分析できます。 メトリックス エクスプローラーを開くには、 **[Azure Monitor]** メニューの **[メトリック]** を選択します。 このツールの使用方法の詳細については、「[Azure メトリックス エクスプローラーの概要](../../azure-monitor/platform/metrics-getting-started.md)」をご覧ください。 

次の例は、アカウント レベルで**トランザクション**を表示する方法を示しています。

![Azure Portal でのメトリック アクセスのスクリーンショット](./media/monitor-storage/access-metrics-portal.png)

ディメンションをサポートするメトリックについては、目的のディメンション値でメトリックをフィルター処理できます。 次の例は、 **[API 名]** ディメンションの値を選択することで特定の操作に対するアカウント レベルの**トランザクション**を表示する方法を示しています。

![Azure Portal におけるディメンションでのメトリック アクセスのスクリーンショット](./media/monitor-storage/access-metrics-portal-with-dimension.png)

Azure Storage でサポートされるディメンションの完全な一覧については、「[メトリックのディメンション](monitor-storage-reference.md#metrics-dimensions)」をご覧ください。

Azure Storage のすべてのメトリックは、次の名前空間にあります。

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/blobServices
- Microsoft.Storage/storageAccounts/fileServices
- Microsoft.Storage/storageAccounts/queueServices
- Microsoft.Storage/storageAccounts/tableServices

(Azure Storage を含む) すべての Azure Monitor サポート メトリックの一覧については、「[Azure Monitor のサポートされるメトリック](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)」をご覧ください。


### <a name="access-metrics"></a>メトリックにアクセスする

> [!TIP]
> Azure CLI または .NET の例を表示するには、ここに示した対応するタブを選択します。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>メトリック定義の一覧表示

使用しているストレージ アカウントのメトリック定義、または BLOB、ファイル、テーブル、キュー サービスなどの個々のストレージ サービスを一覧表示できます。 [Get-AzMetricDefinition](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricdefinition?view=azps-3.3.0) コマンドレットを使用します。

この例では、`<resource-ID>` プレースホルダーをストレージ アカウント全体のリソース ID に置き換えるか、BLOB、ファイル、テーブル、キュー サービスなどの個々のストレージ サービスのリソース ID に置き換えます。 これらのリソース ID は、Azure portal 上のストレージ アカウントの **[プロパティ]** ページで確認できます。

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="read-metric-values"></a>メトリック値の読み取り

ストレージ アカウントのアカウント レベルのメトリック値、または BLOB、ファイル、テーブル、キュー サービスなどの個々のストレージ サービスのメトリック値を読み取ることができます。 [Get-AzMetric](https://docs.microsoft.com/powershell/module/Az.Monitor/Get-AzMetric?view=azps-3.3.0) コマンドレットを使用します。

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>アカウント レベルのメトリック定義を一覧表示する

使用しているストレージ アカウントのメトリック定義、または BLOB、ファイル、テーブル、キュー サービスなどの個々のストレージ サービスを一覧表示できます。 [az monitor metrics list-definitions](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list-definitions) コマンドを使用します。
 
この例では、`<resource-ID>` プレースホルダーをストレージ アカウント全体のリソース ID に置き換えるか、BLOB、ファイル、テーブル、キュー サービスなどの個々のストレージ サービスのリソース ID に置き換えます。 これらのリソース ID は、Azure portal 上のストレージ アカウントの **[プロパティ]** ページで確認できます。

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>アカウント レベルのメトリック値を読み取る

使用しているストレージ アカウントのメトリック値、または BLOB、ファイル、テーブル、キュー サービスなどの個々のストレージ サービスのメトリック値を読み取ることができます。 [az monitor metrics list](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) コマンドを使用します。

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/dotnet)

Azure Monitor には、メトリックの定義と値を読み取るための [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) が用意されています。 [サンプル コード](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/)では、さまざまなパラメーターで SDK を使用する方法を示します。 ストレージ メトリックスについては `0.18.0-preview` 以降のバージョンを使用する必要があります。
 
これらの例では、`<resource-ID>` プレースホルダーをストレージ アカウント全体のリソース ID に置き換えるか、BLOB、ファイル、テーブル、キュー サービスなどの個々のストレージ サービスのリソース ID に置き換えます。 これらのリソース ID は、Azure portal 上のストレージ アカウントの **[プロパティ]** ページで確認できます。

`<subscription-ID>` 変数をご自身のサブスクリプションの ID に置き換えます。 `<tenant-ID>`、`<application-ID>`、および `<AccessKey>` の値を取得する方法のガイダンスについては、「[リソースにアクセスできる Azure AD アプリケーションとサービス プリンシパルをポータルで作成する](https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/)」をご覧ください。 

#### <a name="list-the-account-level-metric-definition"></a>アカウント レベルのメトリック定義を一覧表示する

次の例は、アカウント レベルでメトリック定義を一覧表示する方法を示しています。

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";


        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            // Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

#### <a name="read-account-level-metric-values"></a>アカウント レベルのメトリック値を読み取る

次の例は、アカウント レベルで `UsedCapacity` データを読み取る方法を示しています。

```csharp
    public static async Task ReadStorageMetricValue()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metricnames: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            // Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

#### <a name="read-multidimensional-metric-values"></a>多次元メトリック値を読み取る

多次元メトリックの場合、特定のディメンション値に対するメトリック データを読み取る必要がある場合は、メタデータ フィルターを定義する必要があります。

次の例では、多次元をサポートするメトリックについてのメトリック データを読み取る方法を示します。

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "<subscription-ID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metricnames: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

---

## <a name="analyze-log-data"></a>ログ データの分析

リソース ログには、ストレージ アカウント内の BLOB として、イベント データとして、またはログ分析クエリを使用してアクセスできます。

これらのログに表示されるフィールドの詳細なリファレンスについては、「[Azure Storage 監視データのリファレンス](monitor-storage-reference.md)」をご覧ください。

> [!NOTE]
> Azure Monitor の Azure Storage ログはパブリック プレビュー段階にあり、すべてのパブリック クラウド リージョンでプレビュー テスト用に使用できます。 プレビューに登録するには、[こちらのページ](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)を参照してください。 このプレビューでは、汎用 v1 と汎用 v2 ストレージ アカウントの BLOB (Azure Data Lake Storage Gen2 を含む)、ファイル、キュー、テーブル、Premium ストレージ アカウントに対してログが有効になります。 クラシック ストレージ アカウントはサポートされていません。

### <a name="access-logs-in-a-storage-account"></a>ストレージ アカウントのログにアクセスする

ログは、ターゲット ストレージ アカウントのコンテナーに格納された BLOB として表示されます。 データは、行区切りの JSON ペイロードとして収集され、1 つの BLOB 内に格納されます。 BLOB のこの名前は次の名前付け規則に従っています。

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/blobServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

次に例を示します。

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="access-logs-in-an-event-hub"></a>イベント ハブのログにアクセスする

イベント ハブに送信されたログはファイルとして保存されませんが、イベント ハブでログ情報が受信されたことを確認することはできます。 Azure portal でイベント ハブに移動し、**受信メッセージ**の数がゼロより大きいことを確認します。 

![監査ログ](media/monitor-storage/event-hub-log.png)

イベント ハブに送信されるログ データのアクセスと読み取りには、セキュリティ情報とイベント管理のツールと監視ツールを使用します。 詳細については、[イベント ハブに送信された監視データを処理する方法](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)に関するページを参照してください。

### <a name="access-logs-in-a-log-analytics-workspace"></a>Log Analytics ワークスペースのログにアクセスする

Azure Monitor ログ クエリを使用して、Log Analytics ワークスペースに送信されたログにアクセスできます。

詳細については、「[Azure Monitor で Log Analytics の使用を開始する](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)」を参照してください。

データは以下のテーブルに格納されます。

| テーブル | 説明 |
|:---|:---|
|StorageBlobLogs | BLOB ストレージ内のアクティビティを説明するログ。 |
|StorageFileLogs | ファイル共有内のアクティビティを説明するログ。 |
|StorageQueueLogs | キュー内のアクティビティを説明するログ。|
|StorageTableLogs| テーブル内のアクティビティを説明するログ。|

Data Lake Storage Gen2 のログは、専用テーブルに表示されません。 この理由は Data Lake Storage Gen2 がサービスではないためです。 これは、BLOB ストレージ アカウントで有効にできる機能のセットです。 これらの機能を有効にしている場合、ログは StorageBlobLogs テーブルに引き続き表示されます。 

### <a name="azure-storage-log-analytics-queries-in-azure-monitor"></a>Azure Monitor での Azure Storage Log Analytics のクエリ

**[ログ検索]** バーに入力して Azure Storage アカウントの監視に利用できるクエリを紹介します。 これらのクエリは[新しい言語](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)で使用できます。

これらのクエリを使用すると、Azure Storage アカウントの監視に役立ちます。

* 過去 3 日間に発生した 10 件の最も一般的なエラーの一覧を表示します。

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```
* 過去 3 日間で最も多く発生したエラーの原因となった上位 10 件の操作を一覧表示します。

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```
* 過去 3 日間でエンドツーエンドの待機時間が最も長かった上位 10 個の操作を一覧表示します。

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```
* 過去 3 日間にサーバー側の調整エラーの原因となったすべての操作を一覧表示します。

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```
* 過去 3 日間の匿名アクセスを含むすべての要求を一覧表示します。

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```
* 過去 3 日間に使用された操作の円グラフを作成します。
    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc 
    | render piechart
    ```
## <a name="faq"></a>よく寄せられる質問

**Azure Storage はマネージド ディスクまたはアンマネージド ディスクのメトリックをサポートしますか。**

いいえ。 Azure Compute はディスク上のメトリックをサポートします。 詳細については、[マネージド ディスクと非管理ディスクのディスクあたりのメトリック](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

- Azure Storage によって作成されるログおよびメトリックのリファレンスについては、「[Azure Storage 監視データのリファレンス](monitor-storage-reference.md)」をご覧ください。
- Azure リソースの監視の詳細については、「[Azure Monitor で Azure リソースを管理する](../../azure-monitor/insights/monitor-azure-resource.md)」をご覧ください。
- メトリックの移行の詳細については、「[Azure Storage メトリックの移行](./storage-metrics-migration.md)」をご覧ください。
