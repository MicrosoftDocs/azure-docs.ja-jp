---
title: "Azure Portal でのストレージ メトリックの有効化 | Microsoft Docs"
description: "BLOB、Queue、Table、および File サービスに対するストレージ メトリックを有効にする方法"
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 0407adfc-2a41-4126-922d-b76e90b74563
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/14/2017
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: d7a08bb0f4128eb3915658b41843b35613108a98
ms.openlocfilehash: d544695654ff136ee25856cb4a0309f882057ebb
ms.lasthandoff: 02/22/2017


---
# <a name="enabling-azure-storage-metrics-and-viewing-metrics-data"></a>Azure のストレージ メトリックの有効化とメトリック データの表示
[!INCLUDE [storage-selector-portal-enable-and-view-metrics](../../includes/storage-selector-portal-enable-and-view-metrics.md)]

## <a name="overview"></a>概要
新しいストレージ アカウントを作成すると、ストレージ メトリックが既定で有効になります。 [Azure Portal](https://portal.azure.com) または Windows PowerShell を使って監視を構成できます。または、ストレージ クライアント ライブラリの&1; つを使ってプログラムで監視を有効にすることもできます。

メトリック データのリテンション期間を構成できます。この期間により、ストレージ サービスがメトリックを保有する期間が決まり、メトリックを保存するために必要な領域に対して課金されます。 一般的には、分単位メトリックには時間単位メトリックより短いリテンション期間を使用してください。分単位メトリックにはかなりの追加領域が必要になるためです。 データを分析し、保存するメトリックをダウンロードし、オフライン分析やレポートを行うために十分な時間が確保されるようにリテンション期間を選択してください。 ストレージ アカウントからメトリック データをダウンロードした場合にも課金されることにご注意ください。

## <a name="how-to-enable-metrics-using-the-azure-portal"></a>Azure Portal を使用してメトリックを有効にする方法
[Azure Portal](https://portal.azure.com) でメトリックを有効にするには、次の手順に従います。

1. ストレージ アカウントに移動します。
1. **[メニュー]** ブレードで、**[診断]** を選択します。
1. **[状態]** が **[オン]** に設定されていることを確認します。
1. 監視するサービスのメトリックを選択します。
1. リテンション ポリシーを指定して、メトリックとログ データを保持する期間を示します。
1. [ **保存**] を選択します。

[Azure Portal](https://portal.azure.com) では、現在のところ、ストレージ アカウントで分単位のメトリックを構成できません。分単位のメトリックは PowerShell かプログラミングを使用して有効にする必要があります。

## <a name="how-to-enable-metrics-using-powershell"></a>PowerShell を使用してメトリックを有効にする方法
ローカル マシンの PowerShell を使用して、ストレージ アカウントのストレージ メトリックを構成できます。Azure PowerShell コマンドレット Get-AzureStorageServiceMetricsProperty を使って現在の設定を取得し、コマンドレット Set-AzureStorageServiceMetricsProperty を使って現在の設定を変更します。

ストレージ メトリックを制御するコマンドレットでは次のパラメーターが使用されます。

* MetricsType: 指定可能な値は Hour と Minute です。
* ServiceType: 指定可能な値は、Blob、Queue、Table です。
* MetricsLevel: 指定可能な値は None、Service、ServiceAndApi です。

たとえば、次のコマンドは、既定のストレージ アカウントの BLOB サービスの分単位メトリックを&5; 日間に設定されたリテンション期間でオンにします。

```powershell
Set-AzureStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5`
```

次のコマンドは、既定のストレージ アカウントの BLOB サービスの現在の時間単位メトリック レベルとリテンション日数を取得します。

```powershell
Get-AzureStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob
```

Azure サブスクリプションを処理するように Azure PowerShell コマンドレットを構成する方法と、使用する既定のストレージ アカウントを選択する方法については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azureps-cmdlets-docs)」をご覧ください。

## <a name="how-to-enable-storage-metrics-programmatically"></a>プログラムを利用してストレージ メトリックを有効にする方法
次の C# スニペットは、.NET 用ストレージ クライアント ライブラリを使用して、BLOB サービスのメトリックとログ記録を有効にする方法を示しています。

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

// Create service client for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Enable Storage Analytics logging and set retention policy to 10 days.
ServiceProperties properties = new ServiceProperties();
properties.Logging.LoggingOperations = LoggingOperations.All;
properties.Logging.RetentionDays = 10;
properties.Logging.Version = "1.0";

// Configure service properties for metrics. Both metrics and logging must be set at the same time.
properties.HourMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
properties.HourMetrics.RetentionDays = 10;
properties.HourMetrics.Version = "1.0";

properties.MinuteMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
properties.MinuteMetrics.RetentionDays = 10;
properties.MinuteMetrics.Version = "1.0";

// Set the default service version to be used for anonymous requests.
properties.DefaultServiceVersion = "2015-04-05";

// Set the service properties.
blobClient.SetServiceProperties(properties);
```

## <a name="viewing-storage-metrics"></a>ストレージ メトリックを表示する
ストレージ アカウントを監視するように Storage Analytics メトリックを構成すると、ストレージ アカウントのよく知られたテーブルのセットにメトリックが記録されます。 [Azure Portal](https://portal.azure.com) では、時間単位のメトリックを表示するようにグラフを構成できます。

1. [Azure Portal](https://portal.azure.com) のストレージ アカウントに移動します。
1. メトリックを表示するサービスの **[メニュー]** ブレードで、**[メトリック]** を選択します。
1. 構成するグラフで **[編集]** を選択します。
1. **[グラフの編集]** ブレードで、**[時間の範囲]**、**[グラフの種類]**、およびグラフに表示するメトリックを選択します。
1. **[OK]** を選択します。

長期間ストレージのメトリックをダウンロードしたり、それらをローカルで分析したりする場合は、次のようにしてください。

* これらのテーブルを認識し、表示やダウンロードを行うことができるツールを使用します。
* テーブルの読み取りと格納を行うためのカスタム アプリケーションやスクリプトを記述します。

多くのサード パーティ製ストレージ閲覧ツールは、これらのテーブルを認識し、テーブルを直接表示できます。
利用できるツールの一覧については、「[Azure Storage クライアント ツール](storage-explorers.md)」をご覧ください。

> [!NOTE]
> [Microsoft Azure ストレージ エクスプローラー](http://storageexplorer.com/)のバージョン 0.8.0 以降では、分析メトリック テーブルの表示とダウンロードができます。
> 
> 

分析テーブルにプログラムでアクセスする場合、ストレージ アカウント内のすべてのテーブルを一覧表示すると、分析テーブルは表示されないことに注意してください。 名前を指定して直接アクセスするか、.NET クライアント ライブラリの [CloudAnalyticsClient API](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.analytics.cloudanalyticsclient.aspx) を使用してテーブル名を照会する必要があります。

### <a name="hourly-metrics"></a>時間単位のメトリック
* $MetricsHourPrimaryTransactionsBlob
* $MetricsHourPrimaryTransactionsTable
* $MetricsHourPrimaryTransactionsQueue

### <a name="minute-metrics"></a>分単位のメトリック
* $MetricsMinutePrimaryTransactionsBlob
* $MetricsMinutePrimaryTransactionsTable
* $MetricsMinutePrimaryTransactionsQueue

### <a name="capacity"></a>容量
* $MetricsCapacityBlob

これらのテーブルのスキーマの全詳細については、「 [Storage Analytics Metrics のテーブル スキーマ](https://msdn.microsoft.com/library/azure/hh343264.aspx)」をご覧ください。 下のサンプル行は、一部の利用できる列のみを示していますが、ストレージ メトリックがこれらのメトリックを保存するしくみについて、重要な特徴をいくつか説明しています。

| PartitionKey | RowKey | Timestamp | TotalRequests | TotalBillableRequests | TotalIngress | TotalEgress | 可用性 | AverageE2ELatency | AverageServerLatency | PercentSuccess |
| --- |:---:| ---:| --- | --- | --- | --- | --- | --- | --- | --- |
| 20140522T1100 |user;All |2014-05-22T11:01:16.7650250Z |7 |7 |4003 |46801 |100 |104.4286 |6.857143 |100 |
| 20140522T1100 |user;QueryEntities |2014-05-22T11:01:16.7640250Z |5 |5 |2694 |45951 |100 |143.8 |7.8 |100 |
| 20140522T1100 |user;QueryEntity |2014-05-22T11:01:16.7650250Z |1 |1 |538 |633 |100 |3 |3 |100 |
| 20140522T1100 |user;UpdateEntity |2014-05-22T11:01:16.7650250Z |1 |1 |771 |217 |100 |9 |6 |100 |

この例の分単位メトリック データでは、パーティション キーは分単位解決の時間を使用しています。 行キーは、行に保存され、アクセス タイプと要求タイプという&2; つの情報から構成されるタイプの情報を識別します。

* アクセス タイプは user と system のいずれかになります。user はストレージ サービスに対するすべてのユーザー要求を意味し、system は Storage Analytics により行われる要求を意味します。
* 要求タイプは、概要行となる all か、QueryEntity や UpdateEntity など特定の API のいずれかになります。

上記のサンプル データは、1 つの分単位 (午前 11 時から始まる) に対するすべてのレコードを示します。つまり、QueryEntities 要求の数に QueryEntity 要求の数と UpdateEntity 要求の数を足すと 7 になり、これが user:All 行に表示される合計です。 同様に、((143.8 * 5) + 3 + 9)/7 を計算し、端末間の平均待機時間 104.4286 を user:All 行で導くことができます。

## <a name="metrics-alerts"></a>メトリック アラート
ストレージ サービスの動作の重要な変更がストレージ メトリックによって自動的に通知されるように、[Azure Portal](https://portal.azure.com) でアラートを設定することを検討してください。 ストレージ エクスプローラー ツールを使ってこのメトリック データを区切り形式でダウンロードすると、Microsoft Excel を使ってデータを分析できます。 利用できるストレージ エクスプローラー ツールの一覧については、「[Azure Storage クライアント ツール](storage-explorers.md)」をご覧ください。 アラートの構成は、ストレージ アカウント メニュー ブレードの **[監視]** からアクセスできる **[アラート ルール]** で行うことができます。

> [!IMPORTANT]
> ストレージ イベントが発生してから、対応する時間または分単位のメトリック データが記録されるまでに、遅延が生じる場合があります。 分単位のメトリックの場合は、数分間のデータが一度に書き込まれることがあります。 そのため、前の数分のトランザクションが現在の分のトランザクションに集計される可能性があります。 その場合、アラート サービスが構成されているアラート間隔のすべての利用可能なメトリック データを取得できず、アラートが予期せず発生する場合があります。
>

## <a name="accessing-metrics-data-programmatically"></a>メトリック データにプログラミングでアクセスする
次の一覧は、一定の分単位範囲で分単位メトリックにアクセスして結果をコンソール ウィンドウに表示するサンプル C# コードを示しています。 Azure Storage ライブラリのバージョン 4 を使用しますが、これに入っている CloudAnalyticsClient クラスは、Storage のメトリック テーブルにアクセスするプロセスを簡素化します。

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)
{
    // Convert the dates to the format used in the PartitionKey
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

        // Filter on "user" transactions after fetching the metrics from Table Storage.
        // (StartsWith is not supported using LINQ with Azure table storage)
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

## <a name="what-charges-do-you-incur-when-you-enable-storage-metrics"></a>ストレージ メトリックを有効にした場合に発生する課金
メトリックのテーブル エンティティを作成する要求を記述すると、すべての Azure Storage 操作に適用される標準料金で課金されます。

クライアントがメトリック データの読み取りまたは削除を要求した場合も、標準料金で課金されます。 データ保持ポリシーを構成した場合、Azure Storage が古いメトリック データを削除するときは課金されません。 ただし、分析データを削除する場合は、削除操作に対してアカウントに請求が届きます。

メトリック テーブルで使用される容量も課金対象です。次を利用し、メトリック データの保存に使用される容量を見積もることができます。

* あるサービスがすべてのサービスのすべての API を毎時間利用する場合、サービス レベルと API レベルの概要を両方とも有効にしていれば、約 148 KB のデータがメトリック トランザクション テーブルに毎時間保存されます。
* あるサービスがすべてのサービスのすべての API を毎時間利用する場合、サービス レベルの概要だけを有効にしていれば、約 12 KB のデータがメトリック トランザクション テーブルに毎時間保存されます。
* BLOB の容量テーブルには、毎日、2 つの行が追加されます (ユーザーがログを選択している場合)。つまり、このテーブルのサイズは、毎日、最大約 300 バイト増えることになります。

## <a name="next-steps"></a>次のステップ
[ストレージ ログの有効化とログ データへのアクセス](/rest/api/storageservices/fileservices/Enabling-Storage-Logging-and-Accessing-Log-Data)
