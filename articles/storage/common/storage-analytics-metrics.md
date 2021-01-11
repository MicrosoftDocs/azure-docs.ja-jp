---
title: Azure Storage Analytics のメトリック (クラシック)
description: Azure Storage で Storage Analytics メトリックを使用する方法について説明します。 トランザクション メトリックと容量メトリック、メトリックの格納方法、メトリックの有効化などについて説明します。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 2f3fa755f61d398ce7f0965fba86262c3e3ec863
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021155"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Azure Storage Analytics のメトリック (クラシック)

Azure Storage では、Storage Analytics を利用し、ストレージ サービスへの要求に関して集計されたトランザクション統計情報と容量データを含むメトリックが格納されます。 トランザクションは API 操作レベルとストレージ サービス レベルで報告されます。 容量はストレージ サービス レベルで報告されます。 メトリック データを使用して次のことを行うことができます。
- ストレージ サービスの使用状況の分析。
- ストレージ サービスに対して行われた要求の問題の診断。
- サービスを使用するアプリケーションのパフォーマンスの向上。

 新しいストレージ アカウントでは、Storage Analytics Metrics が既定で有効になっています。 メトリックは [Azure portal](https://portal.azure.com/) で構成できます。 詳細については、「[Azure portal でのストレージ アカウントの監視](/azure/storage/storage-monitor-storage-account)」を参照してください。 また、プログラムから REST API またはクライアント ライブラリを使用して有効にすることもできます。 サービスごとに Storage Analytics を有効にするには、サービス プロパティの設定操作を行います。  

> [!NOTE]
> Storage Analytics メトリックは、Azure Blob ストレージ、Azure Queue ストレージ、Azure Table ストレージ、Azure Files でお使いいただけます。
> Storage Analytics メトリックはクラシック メトリックになりました。 Microsoft では、Storage Analytics メトリックの代わりに、[Azure Monitor のストレージ メトリック](monitor-storage.md)の使用をお勧めしています。

## <a name="transaction-metrics"></a>トランザクション メトリック  
 各ストレージ サービスと要求された API 操作について、受信と送信、可用性、エラー、分類された要求のパーセンテージを含む信頼性の高いデータのセットが 1 時間または 1 分間隔で記録されます。 トランザクションの詳細の一覧については、[Storage Analytics メトリックのテーブル スキーマ](/rest/api/storageservices/storage-analytics-metrics-table-schema)に関するページをご覧ください。  

 トランザクション データは、サービス レベルと API 操作レベルで記録されます。 サービス レベルでは、サービスに対して要求が行われなかった場合でも、すべての要求された API 操作を要約した統計情報が 1 時間ごとにテーブル エンティティに書き込まれます。 API 操作レベルでは、統計情報は、その時間内に操作が要求された場合にのみエンティティに書き込まれます。  

 たとえば、Blob service で **GetBlob** 操作を実行する場合、Storage Analytics メトリックでは、その要求がログに記録され、Blob service と **GetBlob** 操作の集計データに要求が含められます。 時間内に **GetBlob** 操作が要求されなかった場合、その操作に対してエンティティが *$MetricsTransactionsBlob* に書き込まれることはありません。  

 トランザクション メトリックは、ユーザー要求と Storage Analytics 自体によって発行された要求に対して記録されます。 たとえば、Storage Analytics からのログとテーブル エンティティの書き込み要求は記録されます。

## <a name="capacity-metrics"></a>容量メトリック  

> [!NOTE]
>  現在、容量メトリックは、Blob service に対してのみご利用になれます。

 容量データはストレージ アカウントの Blob service に対して毎日記録され、2 つのテーブル エンティティが書き込まれます。 一方のエンティティは、ユーザー データの統計情報です。もう一方のエンティティは、Storage Analytics によって使用される `$logs` BLOB コンテナーに関する統計情報です。 *$MetricsCapacityBlob* テーブルには、次の統計情報が含まれています。  

- **Capacity**:ストレージ アカウントの Blob service によって使われているストレージの量 (バイト単位)。  
- **ContainerCount**:ストレージ アカウントの Blob service 内のコンテナーの数。  
- **ObjectCount**:ストレージ アカウントの Blob service 内のコミット済みとコミット前のブロック BLOB またはページ BLOB の数。  

  容量メトリックの詳細については、[Storage Analytics メトリックのテーブル スキーマ](/rest/api/storageservices/storage-analytics-metrics-table-schema)に関するページをご覧ください。  

## <a name="how-metrics-are-stored"></a>メトリックの保存  

 各ストレージ サービスのすべてのメトリック データは、そのサービスに予約された 3 つのテーブルに格納されます。 1 つはトランザクション情報用、1 つは分単位のトランザクション情報用、1 つは容量情報用のテーブルです。 トランザクション情報と分単位のトランザクション情報は、要求データと応答データから構成されます。 容量情報は、ストレージ使用量データから構成されます。 ストレージ アカウントの Blob service に関する時間単位のメトリック、分単位のメトリック、容量は、次の表に示す名前のテーブルで利用できます。  

|メトリック レベル|テーブル名|サポートされているバージョン|  
|-------------------|-----------------|----------------------------|  
|時間単位のメトリック、1 次拠点|-   $MetricsTransactionsBlob<br />-   $MetricsTransactionsTable<br />-   $MetricsTransactionsQueue|2013 年 8 月 15 日より前のバージョンのみ。 これらの名前はまだサポートされていますが、以下のテーブルを使用するように切り替えることをお勧めします。|  
|時間単位のメトリック、1 次拠点|-   $MetricsHourPrimaryTransactionsBlob<br />-   $MetricsHourPrimaryTransactionsTable<br />-   $MetricsHourPrimaryTransactionsQueue<br />-   $MetricsHourPrimaryTransactionsFile|すべてのバージョン。 ファイル サービス メトリックのサポートは、バージョン 2015 年 4 月 5 日以降でのみ使用可能です。|  
|分単位のメトリック、1 次拠点|-   $MetricsMinutePrimaryTransactionsBlob<br />-   $MetricsMinutePrimaryTransactionsTable<br />-   $MetricsMinutePrimaryTransactionsQueue<br />-   $MetricsMinutePrimaryTransactionsFile|すべてのバージョン。 ファイル サービス メトリックのサポートは、バージョン 2015 年 4 月 5 日以降でのみ使用可能です。|  
|時間単位のメトリック、2 次拠点|-   $MetricsHourSecondaryTransactionsBlob<br />-   $MetricsHourSecondaryTransactionsTable<br />-   $MetricsHourSecondaryTransactionsQueue|すべてのバージョン。 読み取りアクセスの地理冗長レプリケーションを有効にする必要があります。|  
|分単位のメトリック、2 次拠点|-   $MetricsMinuteSecondaryTransactionsBlob<br />-   $MetricsMinuteSecondaryTransactionsTable<br />-   $MetricsMinuteSecondaryTransactionsQueue|すべてのバージョン。 読み取りアクセスの地理冗長レプリケーションを有効にする必要があります。|  
|容量 (Blob service のみ)|$MetricsCapacityBlob|すべてのバージョン。|  

 これらのテーブルは、Storage Analytics がストレージ サービス エンドポイントに対して有効化されたときに自動的に作成されます。 これらのテーブルには、ストレージ アカウントの名前空間を介してアクセスします (例: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`)。 メトリック テーブルは、操作の一覧には表示されません。また、テーブル名を使用して直接アクセスする必要があります。

## <a name="enable-metrics-by-using-the-azure-portal"></a>Azure portal を使用してメトリックを有効にします。
[Azure Portal](https://portal.azure.com) でメトリックを有効にするには、次の手順に従います。

1. ストレージ アカウントに移動します。
1. [メニュー] ウィンドウで **[診断設定 (クラシック)]** を選択します。
1. **[状態]** が **[オン]** に設定されていることを確認します。
1. 監視するサービスのメトリックを選択します。
1. リテンション ポリシーを指定して、メトリックとログ データを保持する期間を示します。
1. **[保存]** を選択します。

[Azure portal](https://portal.azure.com) では現在のところ、ストレージ アカウントで分単位のメトリックを構成できません。 PowerShell を使用するか、プログラミングによって分単位のメトリックを有効にする必要があります。

## <a name="enable-storage-metrics-by-using-powershell"></a>PowerShell を使用してストレージ メトリックを有効にする  
ローカル マシンで PowerShell を使用し、Azure PowerShell コマンドレット **Get-AzStorageServiceMetricsProperty** で現在の設定を取得するという方法でストレージ アカウントのストレージ メトリックを構成できます。 現在の設定を変更するには、コマンドレット **Set-AzStorageServiceMetricsProperty** を使用します。  

ストレージ メトリックを制御するコマンドレットでは次のパラメーターが使用されます。  

* **ServiceType**:指定可能な値は、**Blob**、**Queue**、**Table**、**File** です。
* **MetricsType**:指定可能な値は **Hour** と **Minute** です。  
* **MetricsLevel**:次のいずれかの値になります。
   * **なし**: 監視しません。
   * **サービス**:受信と送信、空き時間情報、遅延時間、成功のパーセンテージなどのメトリックを収集して、BLOB、テーブル、キュー、ファイルのサービスごとに集計します。
   * **ServiceAndApi**:サービス メトリックに加えて、Azure Storage サービス API のストレージ操作ごとに同じメトリックを収集します。

たとえば、次のコマンドは、ストレージ アカウントの BLOB サービスの分単位メトリックを 5 日間に設定された保持期間でオンにします。 

> [!NOTE]
> このコマンドは、`Connect-AzAccount` コマンドを使用して Azure サブスクリプションにサインインしていることを想定しています。

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"

Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $storageAccount.Context
```  

* `<resource-group-name>` プレースホルダーの値を、リソース グループの名前に置き換えます。      
* `<storage-account-name>` プレースホルダーの値は、実際のストレージ アカウントの名前に置き換えます。



次のコマンドは、既定のストレージ アカウントの BLOB サービスの現在の時間単位メトリック レベルとリテンション日数を取得します。  

```powershell
Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
```  

Azure サブスクリプションを処理するように Azure PowerShell コマンドレットを構成する方法と、使用する既定のストレージ アカウントを選択する方法については、[Azure PowerShell のインストールと構成](https://azure.microsoft.com/documentation/articles/install-configure-powershell/)に関するページをご覧ください。  

## <a name="enable-storage-metrics-programmatically"></a>プログラムを利用してストレージ メトリックを有効にする  
Azure portal または Azure PowerShell コマンドレットを使用してストレージ メトリックを制御する方法に加え、いずれかの Azure Storage API を使用することもできます。 たとえば、.NET 言語を使用している場合は、Azure Storage クライアント ライブラリを使用できます。  

クラス **CloudBlobClient**、**CloudQueueClient**、**CloudTableClient**、および **CloudFileClient** はすべて、**SetServiceProperties** や **SetServicePropertiesAsync** のような、**ServiceProperties** オブジェクトをパラメーターとして使用するメソッドが用意されています。 **ServiceProperties** オブジェクトを使用して、ストレージ メトリックを構成することができます。 たとえば、次の C# スニペットには、時間単位のキュー メトリックに対して、メトリック レベルとリテンション日数を変更する方法が示されています。  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

.NET 言語を使用してストレージ メトリックを構成する方法について詳しくは、[.NET 用の Azure Storage クライアント ライブラリ](https://msdn.microsoft.com/library/azure/mt347887.aspx)に関する記事をご覧ください。  

REST API を使用してストレージ メトリックを構成する方法については、「[Storage Analytics の有効化と構成](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics)」をご覧ください。  

##  <a name="view-storage-metrics"></a>ストレージ メトリックを表示する  
ストレージ アカウントを監視するように Storage Analytics メトリックを構成すると、ストレージ アカウントのよく知られたテーブルのセットにメトリックが記録されます。 [Azure Portal](https://portal.azure.com) では、時間単位のメトリックを表示するようにグラフを構成できます。

1. [Azure portal](https://portal.azure.com) でストレージ アカウントに移動します。
1. メトリックを表示するサービスの [メニュー] ウィンドウで **[メトリック (クラシック)]** を選択します。
1. 構成するグラフを選択します。
1. **[グラフの編集]** ウィンドウで、 **[時間の範囲]** 、 **[グラフの種類]** 、およびグラフに表示するメトリックを選択します。

Azure portal のストレージ アカウントの [メニュー] ウィンドウにある **[監視 (クラシック)]** セクションで[警告ルール](#metrics-alerts)を構成できます。 たとえば、特定のメトリックが特定の値に到達したら電子メール アラートを送信して通知できます。

長期間ストレージのメトリックをダウンロードし、それらをローカルで分析する場合、ツールを使用するか、コードを記述し、テーブルを読み込む必要があります。 分析のために分単位メトリックをダウンロードする必要があります。 ストレージ アカウントにすべてのテーブルを一覧表示した場合、このテーブルは表示されない場合がありますが、名前で直接アクセスできます。 多くのストレージ閲覧ツールは、これらのテーブルを認識し、テーブルを直接表示できます。 利用できるツールの一覧については、[Azure Storage クライアント ツール](/azure/storage/storage-explorers)に関するページをご覧ください。

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

## <a name="metrics-alerts"></a>メトリック アラート
ストレージ サービスの動作の重要な変更が自動的に通知されるように、[Azure Portal](https://portal.azure.com) でアラートを設定することをご検討ください。 Storage Explorer ツールを使ってこのメトリック データを区切り形式でダウンロードすると、Microsoft Excel を使ってデータを分析できます。 利用できる Storage Explorer ツールの一覧については、[Azure Storage クライアント ツール](/azure/storage/storage-explorers)に関するページをご覧ください。 アラートの構成は、ストレージ アカウント メニュー ウィンドウの **[監視 (クラシック)]** からアクセスできる **[アラート (クラシック)]** ウィンドウで行うことができます。

> [!IMPORTANT]
> ストレージ イベントが発生してから、対応する時間または分単位のメトリック データが記録されるまでに、遅延が生じる場合があります。 分単位のメトリックの場合は、数分間のデータが一度に書き込まれることがあります。 この問題によって、前の数分間のトランザクションが現在の分のトランザクションに集計される可能性があります。 この問題が発生したとき、アラート サービスが構成されているアラート間隔のすべての利用可能なメトリック データを取得できず、アラートが予期せず発生する場合があります。
>

## <a name="access-metrics-data-programmatically"></a>メトリック データにプログラムでアクセスする  
次の一覧は、一定の分単位範囲で分単位メトリックにアクセスして結果をコンソール ウィンドウに表示するサンプル C# コードを示しています。 コード サンプルでは、Azure Storage クライアント ライブラリのバージョン 4.x 以降が使用されていますが、これに含まれている **CloudAnalyticsClient** クラスにより、Storage のメトリック テーブルにアクセスするプロセスが簡素化されます。  

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

## <a name="billing-on-storage-metrics"></a>ストレージ メトリックの課金
メトリックのテーブル エンティティを作成する要求を記述すると、すべての Azure Storage 操作に適用される標準料金で課金されます。  

クライアントによってメトリック データの読み取りまたは削除が要求された場合も、標準料金で課金されます。 データ保持ポリシーを構成した場合、Azure Storage が古いメトリック データを削除するときには課金されません。 分析データを削除する場合は、削除操作に対してアカウントに請求が届きます。  

メトリック テーブルで使用される容量も課金対象です。 次の情報を利用し、メトリック データの保存に使用される容量を見積もることができます。  

-   あるサービスがすべてのサービスのすべての API を毎時間利用する場合、サービス レベルと API レベルの概要を有効にしていれば、約 148 KB のデータがメトリック トランザクション テーブルに毎時間保存されます。  
-   あるサービスがそのサービスのすべての API を毎時間利用する場合、サービス レベルの概要だけを有効にしているときは、約 12 KB のデータがメトリック トランザクション テーブルに毎時間保存されます。  
-   ログを選択している場合は、BLOB の容量テーブルに、毎日 2 行が追加されます。 このシナリオでは、このテーブルのサイズは、毎日、最大約 300 バイト増えることになります。

## <a name="next-steps"></a>次のステップ
* [ストレージ アカウントを監視する](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Storage Analytics メトリックのテーブル スキーマ](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [Storage Analytics によって記録される操作やステータス メッセージ](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Storage Analytics のログ記録](storage-analytics-logging.md)
