---
title: Azure Monitor の Azure Storage メトリック | Microsoft Docs
description: Azure Monitor から提供される新しいメトリックについて説明します。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/05/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: c2f6847a286a9c106fc094e9f0aa315d6b1f337d
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257097"
---
# <a name="azure-storage-metrics-in-azure-monitor"></a>Azure Monitor の Azure Storage メトリック

Azure Storage のメトリックを使用して、使用傾向の分析や要求のトレース、ストレージ アカウントの問題の診断を行うことができます。

Azure Monitor には、さまざまな Azure サービスで監視を実施するための統合ユーザー インターフェイスが用意されています。 詳細については、[Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md) に関するページをご覧ください。 Azure Storage は、Azure Monitor プラットフォームにメトリック データを送信することで、Azure Monitor を統合します。

## <a name="access-metrics"></a>メトリックにアクセスする

Azure Monitor では、複数の方法でメトリックにアクセスできます。 たとえば、[Azure portal](https://portal.azure.com)、Azure Monitor API シリーズ (REST および .NET) のほか、Event Hubs などの分析ソリューションからアクセスできます。 詳細については、[Azure Monitor のメトリック](../../monitoring-and-diagnostics/monitoring-overview-metrics.md)に関するページをご覧ください。

メトリックは既定で有効になっており、過去 93 日間のデータにアクセスできます。 データを長期にわたって保持する必要がある場合は、メトリック データを Azure ストレージ アカウントにアーカイブできます。 これは、Azure Monitor の[診断設定](../../azure-monitor/platform/resource-logs-overview.md)で構成されます。

### <a name="access-metrics-in-the-azure-portal"></a>Azure Portal でメトリックにアクセスする

Azure Portal ではメトリックを時間経過に沿って監視できます。 次の例は、アカウント レベルで **Transactions** を表示する方法の例を示しています。

![Azure Portal でのメトリック アクセスのスクリーンショット](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal.png)

ディメンションをサポートするメトリックについては、目的のディメンション値でメトリックをフィルター処理できます。 次の例は、 **[API 名]** ディメンションの値を選択することで特定の操作に対するアカウント レベルの**トランザクション**を表示する方法を示しています。

![Azure Portal におけるディメンションでのメトリック アクセスのスクリーンショット](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal-with-dimension.png)

### <a name="access-metrics-with-the-rest-api"></a>REST API でメトリックにアクセスする

Azure Monitor には、メトリックの定義と値を読み取るための [REST API](/rest/api/monitor/) が用意されています。 このセクションでは、ストレージ メトリックを読み取る方法について説明します。 リソース ID は、すべての REST API で使用されます。 詳細については、Azure Storage サービスのリソース ID に関するページを参照してください。

次の例は、コマンド ラインで [ArmClient](https://github.com/projectkudu/ARMClient) を使用して、REST API でテストを簡略化する方法を示しています。

#### <a name="list-account-level-metric-definition-with-the-rest-api"></a>REST API でアカウント レベルのメトリック定義を一覧表示する

次の例は、アカウント レベルでメトリック定義を一覧表示する方法を示しています。

```
# Login to Azure and enter your credentials when prompted.
> armclient login

> armclient GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions?api-version=2018-01-01

```

BLOB、テーブル、ファイル、またはキューのメトリック定義を一覧表示するには、API でサービスごとに異なるリソース ID を指定する必要があります。

応答には、JSON 形式のメトリック定義が含まれています。

```Json
{
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions/UsedCapacity",
      "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
      "category": "Capacity",
      "name": {
        "value": "UsedCapacity",
        "localizedValue": "Used capacity"
      },
      "isDimensionRequired": false,
      "unit": "Bytes",
      "primaryAggregationType": "Average",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D"
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D"
        }
      ]
    },
    ... next metric definition
  ]
}

```

#### <a name="read-account-level-metric-values-with-the-rest-api"></a>REST API でアカウント レベルのメトリック値を読み取る

次の例は、アカウント レベルでメトリック データを読み取る方法を示しています。

```
> armclient GET "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metrics?metricnames=Availability&api-version=2018-01-01&aggregation=Average&interval=PT1H"

```

上の例で、BLOB、テーブル、ファイル、またはキューのメトリック値を読み取るには、API でサービスごとに異なるリソース ID を指定する必要があります。

次の応答には、JSON 形式のメトリック値が含まれています。

```Json
{
  "cost": 0,
  "timespan": "2017-09-07T17:27:41Z/2017-09-07T18:27:41Z",
  "interval": "PT1H",
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/Microsoft.Insights/metrics/Availability",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Availability",
        "localizedValue": "Availability"
      },
      "unit": "Percent",
      "timeseries": [
        {
          "metadatavalues": [],
          "data": [
            {
              "timeStamp": "2017-09-07T17:27:00Z",
              "average": 100.0
            }
          ]
        }
      ]
    }
  ]
}

```

### <a name="access-metrics-with-the-net-sdk"></a>.NET SDK でメトリックにアクセスする

Azure Monitor には、メトリックの定義と値を読み取るための [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) が用意されています。 [サンプル コード](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/)では、さまざまなパラメーターで SDK を使用する方法を示します。 ストレージ メトリックスについては `0.18.0-preview` 以降のバージョンを使用する必要があります。 リソース ID は .NET SDK で使用されます。 詳細については、Azure Storage サービスのリソース ID に関するページを参照してください。

次の例では、Azure Monitor .NET SDK を使用してストレージ メトリックスを読み取る方法を示します。

#### <a name="list-account-level-metric-definition-with-the-net-sdk"></a>.NET SDK を使用してアカウント レベルのメトリック定義を一覧表示する

次の例は、アカウント レベルでメトリック定義を一覧表示する方法を示しています。

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        // Using metrics in Azure Monitor is currently free. However, if you use additional solutions ingesting metrics data, you may be billed by these solutions. For example, you are billed by Azure Storage if you archive metrics data to an Azure Storage account. Or you are billed by Operation Management Suite (OMS) if you stream metrics data to OMS for advanced analysis.
        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            //Enumrate metric definition:
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

BLOB、テーブル、ファイル、またはキューのメトリック定義を一覧表示するには、API でサービスごとに異なるリソース ID を指定する必要があります。

#### <a name="read-metric-values-with-the-net-sdk"></a>.NET SDK を使用してメトリックの値を読み取る

次の例は、アカウント レベルで `UsedCapacity` データを読み取る方法を示しています。

```csharp
    public static async Task ReadStorageMetricValue()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

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

上の例で、BLOB、テーブル、ファイル、またはキューのメトリック値を読み取るには、API でサービスごとに異なるリソース ID を指定する必要があります。

#### <a name="read-multi-dimensional-metric-values-with-the-net-sdk"></a>.NET SDK を使用して多次元メトリック値を読み取る

多次元メトリックの場合、特定のディメンション値に対するメトリック データを読み取る必要がある場合は、メタ データ フィルターを定義する必要があります。

次の例では、多次元をサポートするメトリックについてのメトリック データを読み取る方法を示します。

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

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

## <a name="understanding-resource-id-for-services-in-azure-storage"></a>Azure Storage サービスのリソース ID について

リソース ID は、Azure のリソースの一意識別子です。 Azure Monitor REST API を使用して、メトリック定義または値を読み取る場合は、操作するリソースのリソース ID を使用する必要があります。 リソース ID テンプレートの形式は次のとおりです。

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Storage は、Azure Monitor を使用して、ストレージ アカウント レベルおよびサービス レベルの両方でメトリックを提供します。 たとえば、Blob Storage についてのみメトリックを取得できます。 レベルごとに独自のリソース ID があり、リソース ID を使用すると、その ID のレベルについてのみメトリックが取得されます。

### <a name="resource-id-for-a-storage-account"></a>ストレージ アカウントのリソース ID

次の形式は、ストレージ アカウントのリソース ID を指定します。

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
```

### <a name="resource-id-for-the-storage-services"></a>ストレージ サービスのリソース ID

次の形式は、各ストレージ サービスのリソース ID を指定します。

* Blob service のリソース ID
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default
```
* Table service のリソース ID
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default
```
* Queue サービスのリソース ID
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default
```
* File サービスのリソース ID
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default
```

### <a name="resource-id-in-azure-monitor-rest-api"></a>Azure Monitor REST API のリソース ID

次の例は、Azure Monitor REST API を呼び出すときに使用されるパターンを示しています。

```
GET {resourceId}/providers/microsoft.insights/metrics?{parameters}
```

## <a name="capacity-metrics"></a>容量メトリック
容量メトリックの値は 1 時間ごとに Azure Monitor に送信され、 毎日更新されます。 時間グレインは、メトリック値が提供される時間間隔を定義します。 すべての容量メトリックに対して、1 時間 (PT1H) の時間グレインがサポートされます。

Azure Storage は、Azure Monitor で次の容量メトリックを提供します。

### <a name="account-level"></a>アカウント レベル

| メトリックの名前 | 説明 |
| ------------------- | ----------------- |
| UsedCapacity | ストレージ アカウントによって使用されているストレージの量。 Standard ストレージ アカウントについては、Blob、Table、File、および Queue で使用される容量の合計です。 Premium ストレージ アカウントと BLOB ストレージ アカウントについては、BlobCapacity と同じです。 <br/><br/> 単位:Bytes <br/> 集計の種類:平均 <br/> 値の例:1024 |

### <a name="blob-storage"></a>BLOB ストレージ

| メトリックの名前 | 説明 |
| ------------------- | ----------------- |
| BlobCapacity | ストレージ アカウントで使用されている Blob Storage の合計。 <br/><br/> 単位:Bytes <br/> 集計の種類:平均 <br/> 値の例:1024 <br/> 寸法: **BlobType**、および **BlobTier** ([定義](#metrics-dimensions)) |
| BlobCount    | ストレージ アカウントに格納されている BLOB オブジェクトの数。 <br/><br/> 単位:Count <br/> 集計の種類:平均 <br/> 値の例:1024 <br/> 寸法: **BlobType**、および **BlobTier** ([定義](#metrics-dimensions)) |
| ContainerCount    | ストレージ アカウントのコンテナーの数。 <br/><br/> 単位:Count <br/> 集計の種類:平均 <br/> 値の例:1024 |
| IndexCapacity     | ADLS Gen2 階層構造のインデックスで使用される記憶域の量 <br/><br/> 単位:Bytes <br/> 集計の種類:平均 <br/> 値の例:1024 |

### <a name="table-storage"></a>テーブル ストレージ

| メトリックの名前 | 説明 |
| ------------------- | ----------------- |
| TableCapacity | ストレージ アカウントによって使用されている Table Storage の量。 <br/><br/> 単位:Bytes <br/> 集計の種類:平均 <br/> 値の例:1024 |
| TableCount   | ストレージ アカウントのテーブルの数。 <br/><br/> 単位:Count <br/> 集計の種類:平均 <br/> 値の例:1024 |
| TableEntityCount | ストレージ アカウントのテーブル エンティティの数。 <br/><br/> 単位:Count <br/> 集計の種類:平均 <br/> 値の例:1024 |

### <a name="queue-storage"></a>ストレージ

| メトリックの名前 | 説明 |
| ------------------- | ----------------- |
| QueueCapacity | ストレージ アカウントによって使用されている Queue ストレージの量。 <br/><br/> 単位:Bytes <br/> 集計の種類:平均 <br/> 値の例:1024 |
| QueueCount   | ストレージ アカウントのキューの数。 <br/><br/> 単位:Count <br/> 集計の種類:平均 <br/> 値の例:1024 |
| QueueMessageCount | ストレージ アカウントの期限が切れていないキュー メッセージの数。 <br/><br/>単位:Count <br/> 集計の種類:平均 <br/> 値の例:1024 |

### <a name="file-storage"></a>File Storage

| メトリックの名前 | 説明 |
| ------------------- | ----------------- |
| FileCapacity | ストレージ アカウントによって使用されている File ストレージの量。 <br/><br/> 単位:Bytes <br/> 集計の種類:平均 <br/> 値の例:1024 |
| FileCount   | ストレージ アカウントのファイルの数。 <br/><br/> 単位:Count <br/> 集計の種類:平均 <br/> 値の例:1024 |
| FileShareCount | ストレージ アカウントのファイル共有の数。 <br/><br/> 単位:Count <br/> 集計の種類:平均 <br/> 値の例:1024 |

## <a name="transaction-metrics"></a>トランザクション メトリック

トランザクション メトリックは、ストレージ アカウントへの要求ごとに、Azure Storage から Azure Monitor に出力されます。 ストレージ アカウントにアクティビティがない場合、その間はトランザクション メトリックのデータは存在しません。 すべてのトランザクション メトリックを、アカウント レベルとサービス レベルの両方 (Blob Storage、Table Storage、Azure Files、および Queue ストレージ) で使用することができます。 時間グレインは、メトリック値が提供される時間間隔を定義します。 すべてのトランザクション メトリックに対してサポートされている時間グレインは PT1H と PT1M です。

Azure Storage は、Azure Monitor で次のトランザクション メトリックを提供します。

| メトリックの名前 | 説明 |
| ------------------- | ----------------- |
| トランザクション | ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 <br/><br/> 単位:Count <br/> 集計の種類:合計 <br/> 適用可能なディメンション:ResponseType、GeoType、ApiName、Authentication ([定義](#metrics-dimensions))<br/> 値の例:1024 |
| イングレス | イングレス データの量。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。 <br/><br/> 単位:Bytes <br/> 集計の種類:合計 <br/> 適用可能なディメンション:GeoType、ApiName、Authentication ([定義](#metrics-dimensions)) <br/> 値の例:1024 |
| エグレス | エグレス データの量。 この値には、外部クライアントから Azure Storage へのエグレスおよび Azure 内でのエグレスが含まれます。 したがって、この値には、課金対象のエグレスが反映されません。 <br/><br/> 単位:Bytes <br/> 集計の種類:合計 <br/> 適用可能なディメンション:GeoType、ApiName、Authentication ([定義](#metrics-dimensions)) <br/> 値の例:1024 |
| SuccessServerLatency | Azure Storage による成功した要求の平均処理時間。 この値には、SuccessE2ELatency で指定されているネットワーク待機時間は含まれません。 <br/><br/> 単位:ミリ秒 <br/> 集計の種類:平均 <br/> 適用可能なディメンション:GeoType、ApiName、Authentication ([定義](#metrics-dimensions)) <br/> 値の例:1024 |
| SuccessE2ELatency | ストレージ サービスまたは指定された API 操作に対して行われた成功した要求の平均エンド ツー エンド待機時間。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。 <br/><br/> 単位:ミリ秒 <br/> 集計の種類:平均 <br/> 適用可能なディメンション:GeoType、ApiName、Authentication ([定義](#metrics-dimensions)) <br/> 値の例:1024 |
| 可用性 | ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、合計課金対象要求数の値を取得し、それを該当する要求の数 (予期しないエラーになった要求を含む) で割ることによって、計算されます。 予期しないエラーすべてが、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。 <br/><br/> 単位:Percent <br/> 集計の種類:平均 <br/> 適用可能なディメンション:GeoType、ApiName、Authentication ([定義](#metrics-dimensions)) <br/> 値の例:99.99 |

## <a name="metrics-dimensions"></a>メトリックのディメンション

Azure Storage では、Azure Monitor の次のメトリック ディメンションがサポートされます。

| ディメンション名 | 説明 |
| ------------------- | ----------------- |
| **BlobType** | BLOB メトリックの BLOB の種類のみ。 サポートされる値は、**BlockBlob**、**PageBlob**、**Azure Data Lake Storage** です。 BlockBlob には Append Blob が含まれます。 |
| **BlobTier** | Azure Storage からはさまざまなアクセス層が提供され、最もコスト効果の高い方法で BLOB オブジェクト データを格納できます。 詳細については、[Azure Storage の BLOB 層](../blobs/storage-blob-storage-tiers.md)に関する記事を参照してください。 サポートされる値は次のとおりです。 <br/> <li>**Hot**:ホット層</li> <li>**Cool**:クール層</li> <li>**アーカイブ**: アーカイブ層</li> <li>**Premium**:ブロック BLOB 用 Premium 層</li> <li>**P4/P6/P10/P15/P20/P30/P40/P50/P60**:Premium ページ BLOB 用の層の種類</li> <li>**Standard**:Standard ページ BLOB 用の層の種類</li> <li>**Untiered**:汎用 v1 ストレージ アカウントの層の種類</li> |
| **GeoType** | プライマリ クラスターまたはセカンダリ クラスターからのトランザクション。 使用できる値には、**Primary** と **Secondary** が含まれています。 セカンダリ テナントからオブジェクトを読み取るときに、読み取りアクセス geo 冗長ストレージ (RA-GRS) に適用されます。 |
| **ResponseType** | トランザクション応答の種類。 次の値をご利用いただけます。 <br/><br/> <li>**ServerOtherError**:記述されていない、その他すべてのサーバー側エラー </li> <li>**ServerBusyError**:HTTP 503 ステータス コードを返した認証済み要求。 </li> <li>**ServerTimeoutError**:HTTP 500 ステータス コードを返した、タイムアウトした認証済み要求。 タイムアウトは、サーバー エラーが原因で発生しました。 </li> <li>**AuthorizationError**:データの不正アクセスまたは承認エラーが原因で失敗した認証済み要求。 </li> <li>**NetworkError**:ネットワーク エラーが原因で失敗した認証済み要求。 クライアントがタイムアウト期限が切れる前に途中で接続を終了したときによく発生します。 </li> <li>**ClientThrottlingError**:クライアント側の調整エラー。 </li> <li>**ClientTimeoutError**:HTTP 500 ステータス コードを返した、タイムアウトした認証済み要求。 クライアントのネットワーク タイムアウトまたは要求タイムアウトが、ストレージ サービスで予期される値よりも低く設定されている場合、これは予期されるタイムアウトです。 それ以外の場合は、ServerTimeoutError としてレポートされます。 </li> <li>**ClientOtherError**:記述されていない、その他すべてのクライアント側エラー。 </li> <li>**成功**:成功した要求</li> <li> **SuccessWithThrottling**: 最初に試みたときは SMB クライアントが調整されたが再試行後に成功したときの、成功した要求。</li> |
| **ApiName** | 操作の名前。 例: <br/> <li>**CreateContainer**</li> <li>**DeleteBlob**</li> <li>**GetBlob**</li> すべての操作名については、こちらの[ドキュメント](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)を参照してください。 |
| **認証** | トランザクションで使用される認証の種類。 次の値をご利用いただけます。 <br/> <li>**AccountKey**:トランザクションは、ストレージ アカウント キーを使って認証されます。</li> <li>**SAS**:トランザクションは、Shared Access Signature を使って認証されます。</li> <li>**OAuth**:トランザクションは、OAuth アクセス トークンを使って認証されます。</li> <li>**Anonymous**:トランザクションは匿名で要求されます。 プリフライト要求は含まれません。</li> <li>**AnonymousPreflight**:トランザクションは、プリフライト要求です。</li> |

メトリック サポート ディメンションの場合、対応するメトリック値を表示するには、ディメンション値を指定する必要があります。 たとえば、成功した応答の **Transaction** 値を確認する場合は、**ResponseType** ディメンション を **Success** でフィルター処理する必要があります。 また、ブロック BLOB の **BlobCount** 値を確認する場合は、**BlobType** ディメンションを **BlockBlob** でフィルター処理する必要があります。

## <a name="service-continuity-of-legacy-metrics"></a>従来のメトリックのサービス継続性

Azure Monitor 管理メトリックと並行して従来のメトリックを利用できます。 サポートは、Azure Storage が従来のメトリックのサービスを終了するまで引き続き提供されます。

## <a name="faq"></a>FAQ

**新しいメトリックは、従来のストレージ アカウントをサポートしていますか?**

いいえ。Azure Monitor の新しいメトリックは、Azure Resource Manager ストレージ アカウントのみサポートしています。 ストレージ アカウントに対してメトリックを使用する場合、Azure Resource Manager ストレージ アカウントに移行する必要があります。 [Azure Resource Manager への移行](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview)に関するページを参照してください。

**Azure Storage はマネージド ディスクまたはアンマネージド ディスクのメトリックをサポートしますか。**

いいえ、Azure Compute はディスク上のメトリックをサポートします。 詳しくは、[この記事](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/)をご覧ください。

**新しいメトリックを使用して従来のメトリックをマップして移行する方法**

従来のメトリックと新しいメトリック間の詳細なマッピングについては、「[Azure Storage メトリックの移行](./storage-metrics-migration.md)」を参照してください。

## <a name="next-steps"></a>次の手順

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
