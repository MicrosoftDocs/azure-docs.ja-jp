---
title: Azure Storage Analytics メトリックを有効にして管理する (クラシック) | Microsoft Docs
description: Azure Storage Analytics メトリックを有効化、編集、表示する方法について説明します。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 784929e50d25a07ae92cf388be5ac14f6fa820a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99221458"
---
# <a name="enable-and-manage-azure-storage-analytics-metrics-classic"></a>Azure Storage Analytics メトリックを有効にして管理する (クラシック)

[Azure Storage Analytics](storage-analytics.md) では、すべてのストレージ サービスについて、BLOB、キュー、テーブルのメトリックが提供されます。 [Azure portal](https://portal.azure.com) を使用すると、アカウントに対して記録するメトリックと、そのメトリック データを視覚的に表現するグラフを構成できます。 この記事では、メトリックを有効にして管理する方法を示します。 ログを有効にする方法については、「[Azure Storage Analytics ログを有効にして管理する (クラシック)](manage-storage-analytics-logs.md)」を参照してください。

[Azure Monitor for Storage](../../azure-monitor/insights/storage-insights-overview.md) (プレビュー) を確認することをお勧めします。 これは、Azure Storage サービスのパフォーマンス、容量、可用性の統合されたビューが提供されることで、Azure Storage アカウントを包括的に監視できる Azure Monitor の機能です。 何も有効にしたり構成したりする必要はありません。これらのメトリックは、あらかじめ定義されている対話型のグラフやその他の含まれている視覚エフェクトからすぐに表示できます。

> [!NOTE]
> Azure Portal で監視データを調査するとコストがかかります。 詳細については、「[Storage Analytics](storage-analytics.md)」を参照してください。
>
> Premium パフォーマンス ブロック BLOB のストレージ アカウントでは、Storage Analytics メトリックはサポートされません。 Premium パフォーマンス ブロック BLOB のストレージ アカウントでメトリックを表示する場合は、[Azure Monitor の Azure Storage メトリック](../blobs/monitor-blob-storage.md)を使用することを検討してください。
>
> Storage Analytics や他のツールを使用した Azure Storage 関連の問題の特定、診断、トラブルシューティングに関する詳しいガイドについては、「[Microsoft Azure Storage の監視、診断、およびトラブルシューティング](storage-monitoring-diagnosing-troubleshooting.md)」をご覧ください。
>

<a id="Enable-metrics"></a>

## <a name="enable-metrics"></a>メトリックの有効化

### <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com) で **[ストレージ アカウント]** を選択し、目的のストレージ アカウントの名前を選択して、アカウント ダッシュボードを開きます。

2. メニュー ブレードの **[監視 (クラシック)]** セクションで **[診断設定 (クラシック)]** を選択します。
   
   ![[監視 (クラシック)] セクションの [Diagnostic settings (classic)]\(診断設定 (クラシック)\) オプションが強調表示されているスクリーンショット。](./media/manage-storage-analytics-metrics/storage-enable-metrics-00.png)

3. 監視する **サービス** ごとにメトリック データの **型** を選択し、データの **保持ポリシー** を選択します。 **[ステータス]** を **[オフ]** に設定して、監視を無効にすることもできます。

   > [!div class="mx-imgBorder"]
   > ![Azure Portal でログを構成する。](./media/manage-storage-analytics-logs/enable-diagnostics.png) 

   データ保持ポリシーを設定するには、 **[保有期間 (日)]** スライダーを動かすか、データを保有する日数 (1 ～ 365) を入力します。 新しいストレージ アカウントの既定値は 7 日間です。 保有ポリシーを設定しない場合は、「0」(ゼロ) を入力します。 保有ポリシーがない場合、監視データを削除する責任はユーザーが負います。

   > [!WARNING]
   > メトリックは、アカウントにデータとして格納されます。 メトリック データは時間の経過と共にアカウントに蓄積されるため、ストレージのコストが増加するおそれがあります。 メトリック データが必要なのが短期間のみである場合は、データ保持ポリシーを変更することでコストを削減できます。 古いメトリック データ (保持ポリシーよりも古いデータ) はシステムによって削除されます。 アカウントのメトリック データを保持する必要がある期間に基づいて、保持ポリシーを設定することをお勧めします。 詳細については、「[Billing on storage metrics](storage-analytics-metrics.md#billing-on-storage-metrics)」 (ストレージ メトリックへの課金) を参照してください。
   >

4. 監視の構成が完了したら、 **[保存]** を選択します。

既定のメトリック セットは、 **[概要]** ブレードと **[メトリック (クラシック)]** ブレードのグラフに表示されます。 サービスのメトリックを有効にした場合、データがグラフに表示されるまでに最大で 1 時間かかる場合があります。 メトリック グラフで **[編集]** を選択すると、グラフに表示するメトリックを構成できます。

メトリックの収集とログ記録を無効にするには、 **[ステータス]** を **[オフ]** に設定します。

> [!NOTE]
> Azure Storage では[テーブル ストレージ](storage-introduction.md#table-storage)を使用して、ストレージ アカウントのメトリックが保存されます。メトリックが保存されるのはアカウントのテーブルです。 詳細については、以下を参照してください。 「[メトリックの保存](storage-analytics-metrics.md#how-metrics-are-stored)」を参照してください。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Windows PowerShell コマンド ウィンドウを開きます。

2. `Connect-AzAccount` コマンドを使用して Azure サブスクリプションにサインインし、画面上の指示に従います。

   ```powershell
   Connect-AzAccount
   ```

3. 自分の ID が複数のサブスクリプションに関連付けられている場合は、アクティブなサブスクリプションを設定します。

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   `<subscription-id>` プレースホルダーの値をサブスクリプションの ID に置き換えます。

5. 使用するストレージ アカウントを定義するストレージ アカウント コンテキストを取得します。

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * `<resource-group-name>` プレースホルダーの値を、リソース グループの名前に置き換えます。

   * `<storage-account-name>` プレースホルダーの値は、実際のストレージ アカウントの名前に置き換えます。 

6. ローカル コンピューターで PowerShell を使用して、ストレージ アカウントのストレージ メトリックを構成できます。 現在の設定を変更するには、Azure PowerShell コマンドレット **Set-AzStorageServiceMetricsProperty** を使用します。 

   次のコマンドは、保持期間を 5 日間に設定して、ストレージ アカウントの BLOB サービスの分単位メトリックをオンにします。

   ```powershell
   Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $ctx
   ```   

   このコマンドレットは、次のパラメーターを使用します。  

   - **ServiceType**:指定可能な値は、**Blob**、**Queue**、**Table**、**File** です。
   - **MetricsType**:指定可能な値は **Hour** と **Minute** です。  
   - **MetricsLevel**:次のいずれかの値になります。
      - **なし**: 監視しません。
      - **サービス**:受信と送信、空き時間情報、遅延時間、成功のパーセンテージなどのメトリックを収集して、BLOB、テーブル、キュー、ファイルのサービスごとに集計します。
      - **ServiceAndApi**:サービス メトリックに加えて、Azure Storage サービス API のストレージ操作ごとに同じメトリックを収集します。

   次のコマンドは、既定のストレージ アカウントの BLOB サービスの現在の時間単位メトリック レベルとリテンション日数を取得します。  

   ```powershell
   Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
   ```  

   Azure サブスクリプションを処理するように Azure PowerShell コマンドレットを構成する方法と、使用する既定のストレージ アカウントを選択する方法については、[Azure PowerShell のインストールと構成](/powershell/azure/)に関するページをご覧ください。  

### <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_EnableDiagnosticLogs":::

.NET 言語を使用してストレージ メトリックを構成する方法について詳しくは、[.NET 用の Azure Storage クライアント ライブラリ](/dotnet/api/overview/azure/storage)に関する記事をご覧ください。  

REST API を使用してストレージ メトリックを構成する方法については、「[Storage Analytics の有効化と構成](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics)」をご覧ください。 

### <a name="net-v11"></a>[.NET v11](#tab/dotnet11)  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

.NET 言語を使用してストレージ メトリックを構成する方法について詳しくは、[.NET 用の Azure Storage クライアント ライブラリ](/dotnet/api/overview/azure/storage)に関する記事をご覧ください。  

REST API を使用してストレージ メトリックを構成する方法については、「[Storage Analytics の有効化と構成](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics)」をご覧ください。 

---

<a id="view-metrics"></a>

## <a name="view-metrics-in-a-chart"></a>メトリックをグラフで表示する

ストレージ アカウントを監視するように Storage Analytics メトリックを構成すると、ストレージ アカウントのよく知られたテーブルのセットにメトリックが記録されます。 [Azure portal](https://portal.azure.com) では、時間単位のメトリックを表示するようにグラフを構成できます。

メトリック グラフに表示するストレージ メトリックを選択するには、次の手順に従います。

1. 最初に Azure Portal でストレージのメトリック グラフを表示します。 グラフは、**ストレージ アカウント ブレード** と **[メトリック (クラシック)]** ブレードに表示されます。

   この例では、**ストレージ アカウント ブレード** に表示される次のグラフを使用します。

   ![Azure Portal でのグラフの選択](./media/manage-storage-analytics-metrics/stg-customize-chart-00.png)

2. グラフを編集するために、グラフ内の任意の場所をクリックします。

3. 次に、グラフに表示するメトリックの **時間範囲** と、表示するメトリックの **サービス** (BLOB、キュー、テーブル、ファイル) を選択します。 ここでは、Blob service の過去 1 週間のメトリックが表示されるように選択しました。

   ![[グラフの編集] ブレードでの時間の範囲とサービスの選択](./media/manage-storage-analytics-metrics/storage-edit-metric-time-range.png)

4. グラフに表示されている必要な個別の **メトリック** を選択し、 **[OK]** をクリックします。

   ![[グラフの編集] ブレードでの個別のメトリックの選択](./media/manage-storage-analytics-metrics/storage-edit-metric-selections.png)

グラフの設定は、ストレージ アカウントの監視データの収集、集計、または保存には影響しません。

#### <a name="metrics-availability-in-charts"></a>グラフのメトリックの可用性

使用可能なメトリックの一覧は、ドロップダウン リストで選択したサービスと、編集しているグラフの単位の種類に基づいて変わります。 たとえば、*PercentNetworkError*、*PercentThrottlingError* などの割合メトリックを選択できるのは、単位の割合が表示されているグラフを編集している場合だけです。

![Azure Portal の要求エラーの割合グラフ](./media/manage-storage-analytics-metrics/stg-customize-chart-04.png)

#### <a name="metrics-resolution"></a>メトリック解像度

**[診断]** で選択したメトリックによって、アカウントで利用可能なメトリックの解像度が決まります。

* **集計**: 受信/送信、空き時間情報、遅延時間、成功のパーセンテージなどのメトリックを監視します。 このメトリックは、BLOB、テーブル、ファイル、およびキューのサービスごとに集計されます。
* **API ごと**: さらに細かな解像度が、サービス レベル集計のほか、個別のストレージ操作で使用できるメトリックと共に示されます。

## <a name="download-metrics-to-archive-or-analyze-locally"></a>メトリックをダウンロードしてローカルでアーカイブまたは分析する

長期間ストレージのメトリックをダウンロードし、それらをローカルで分析する場合、ツールを使用するか、コードを記述し、テーブルを読み込む必要があります。 ストレージ アカウントにすべてのテーブルを一覧表示した場合、このテーブルは表示されない場合がありますが、名前で直接アクセスできます。 多くのストレージ閲覧ツールは、これらのテーブルを認識し、テーブルを直接表示できます。 利用できるツールの一覧については、[Azure Storage クライアント ツール](./storage-explorers.md)に関するページをご覧ください。

|メトリック|テーブル名|メモ| 
|-|-|-|  
|時間単位のメトリック|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|2013 年 8 月 15 日より前のバージョンでは、これらのテーブルは以下のように呼ばれていました。<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> ファイル サービスのメトリックは、バージョン 2015 年 4 月 5 日以降で利用できます。|  
|分単位のメトリック|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|PowerShell を使用するか、プログラミングによってのみ有効にできます。<br /><br /> ファイル サービスのメトリックは、バージョン 2015 年 4 月 5 日以降で利用できます。|  
|容量|$MetricsCapacityBlob|BLOB サービスのみ。|  

これらのテーブルのスキーマの完全な詳細については、[Storage Analytics メトリックのテーブル スキーマ](/rest/api/storageservices/storage-analytics-metrics-table-schema)に関するページをご覧ください。 次のサンプル行は、一部の利用できる列のみを示していますが、ストレージ メトリックがこれらのメトリックを保存するしくみについて、重要な特徴をいくつか説明しています。  

|パーティション キー|行キー|Timestamp|TotalRequests|TotalBillableRequests|TotalIngress|TotalEgress|可用性|AverageE2ELatency|AverageServerLatency|PercentSuccess| 
|-|-|-|-|-|-|-|-|-|-|-|  
|20140522T1100|user;All|2014-05-22T11:01:16.7650250Z|7|7|4003|46801|100|104.4286|6.857143|100|  
|20140522T1100|user;QueryEntities|2014-05-22T11:01:16.7640250Z|5|5|2694|45951|100|143.8|7.8|100|  
|20140522T1100|user;QueryEntity|2014-05-22T11:01:16.7650250Z|1|1|538|633|100|3|3|100|  
|20140522T1100|user;UpdateEntity|2014-05-22T11:01:16.7650250Z|1|1|771|217|100|9|6|100|  

この例の分単位メトリック データでは、パーティション キーは分単位解決の時間を使用しています。 行キーは、行に格納されている情報の種類を識別します。 情報はアクセスの種類と要求の種類で構成されます。  

-   アクセス タイプは **user** と **system** のいずれかになります。**user** はストレージ サービスに対するすべてのユーザー要求を意味し、**system** は Storage Analytics により行われる要求を意味します。  
-   要求タイプは、概要行となる **all** か、**QueryEntity** や **UpdateEntity** など特定の API のいずれかになります。  

このサンプル データは、1 分間 (午前 11 時から始まる) のすべてのレコードを示します。つまり、**QueryEntities** 要求の数に **QueryEntity** 要求の数と **UpdateEntity** 要求の数を足すと 7 になります。 この合計が **user:All** 行に表示されます。 同様に、((143.8 * 5) + 3 + 9)/7 を計算し、端末間の平均待機時間 104.4286 を **user:All** 行で導くことができます。  

## <a name="view-metrics-data-programmatically"></a>メトリック データをプログラムで表示する

次の一覧は、一定の分単位範囲で分単位メトリックにアクセスして結果をコンソール ウィンドウに表示するサンプル C# コードを示しています。 コード サンプルでは、Azure Storage クライアント ライブラリのバージョン 4.x 以降が使用されていますが、これに含まれている **CloudAnalyticsClient** クラスにより、Storage のメトリック テーブルにアクセスするプロセスが簡素化されます。 

> [!NOTE]
> **CloudAnalyticsClient** クラスは、.NET 用 Azure Blob Storage クライアント ライブラリ v12 には含まれていません。 **2023 年 8 月 31 日** をもって、Storage Analytics メトリック ("*クラシック メトリック*" とも呼ばれます) は廃止されます。 詳細については、[公式告知](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/)を参照してください。 クラシック メトリックを使用している場合は、その日より前に Azure Monitor のメトリックに移行することをお勧めします。 

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)  
{  
 // Convert the dates to the format used in the PartitionKey.  
 var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  
 var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  

 var services = Enum.GetValues(typeof(StorageService));  
 foreach (StorageService service in services)  
 {  
     Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);  
     var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);  
     var t = analyticsClient.GetMinuteMetricsTable(service);  
     var opContext = new OperationContext();  
     var query =  
             from entity in metricsQuery  
             // Note, you can't filter using the entity properties Time, AccessType, or TransactionType  
             // because they are calculated fields in the MetricsEntity class.  
             // The PartitionKey identifies the DataTime of the metrics.  
             where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0   
             select entity;  

     // Filter on "user" transactions after fetching the metrics from Azure Table storage.  
     // (StartsWith is not supported using LINQ with Azure Table storage.)  
     var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));  
     var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();  
     Console.WriteLine(resultString);  
 }  
}  

private static string MetricsString(MetricsEntity entity, OperationContext opContext)  
{  
 var entityProperties = entity.WriteEntity(opContext);  
 var entityString =  
         string.Format("Time: {0}, ", entity.Time) +  
         string.Format("AccessType: {0}, ", entity.AccessType) +  
         string.Format("TransactionType: {0}, ", entity.TransactionType) +  
         string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));  
 return entityString;  
}  
```  

<a id="add-metrics-to-dashboard"></a>

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>ポータル ダッシュボードへのメトリック グラフの追加

ストレージ アカウントの Azure Storage メトリック グラフを、ポータルのダッシュボードに追加できます。

1. [Azure Portal](https://portal.azure.com) にダッシュボードが表示されている間に、 **[ダッシュボードの編集]** をクリックします。
1. **[タイル ギャラリー]** で **[タイルの検索方法]**  >  **[種類]** の順に選択します。
1. **[種類]**  >  **[ストレージ アカウント]** の順に選択します。
1. **[リソース]** で、ダッシュボードに追加するメトリックのストレージ アカウントを選択します。
1. **[カテゴリ]**  >  **[監視]** の順に選択します。
1. グラフのタイルを、メトリックを表示したいダッシュボードにドラッグ アンド ドロップします。 ダッシュボードに表示するすべてのメトリックについて、この操作を繰り返します。 次の図では、"BLOB - 要求の合計" グラフが例として強調表示されますが、すべてのグラフをダッシュボードに配置することができます。

   ![Azure Portal のタイル ギャラリー](./media/manage-storage-analytics-metrics/storage-customize-dashboard.png)
1. グラフの追加が完了したら、ダッシュボードの上部にある **[カスタマイズ完了]** を選択します。

グラフをダッシュボードに追加したら、「メトリック グラフのカスタマイズ」の説明に従って、そのグラフをさらにカスタマイズすることができます。

## <a name="next-steps"></a>次のステップ

* Storage Analytics の詳細については、Storage Analytics の「[Storage Analytics](storage-analytics.md)」を参照してください。
* [Storage Analytics ログを構成する](manage-storage-analytics-logs.md)。
* メトリック スキーマに関する詳細を確認する。 「[Storage Analytics メトリックのテーブル スキーマ](/rest/api/storageservices/storage-analytics-metrics-table-schema)」を参照してください。