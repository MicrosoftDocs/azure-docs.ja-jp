---
title: "Azure Portal でのストレージ メトリックの有効化 | Microsoft Docs"
description: "BLOB、Queue、Table、および File サービスに対するストレージ メトリックを有効にする方法"
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 2fb5b229-f099-4334-92be-4e0e7dd257d7
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/03/2017
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 3c3c7924655f5b932be2a3d947a4df786fc0630d
ms.lasthandoff: 03/06/2017


---
# <a name="enabling-storage-metrics-and-viewing-metrics-data"></a>ストレージ メトリックの有効化とメトリック データの表示
[!INCLUDE [storage-selector-portal-enable-and-view-metrics](../../includes/storage-selector-portal-enable-and-view-metrics.md)]

## <a name="overview"></a>概要
新しいストレージ アカウントを作成すると、ストレージ メトリックが既定で有効になります。 [Azure クラシック ポータル](https://manage.windowsazure.com)または Windows PowerShell を使用して監視を構成できます。また、ストレージ API を使用したプログラムで監視を構成することもできます。

ストレージ メトリックを有効にするとき、データのリテンション期間を選択する必要があります。この期間により、ストレージ サービスがメトリックを保有する期間が決まり、メトリックを保存するために必要な領域に対して課金されます。 一般的には、分単位メトリックには時間単位メトリックより短いリテンション期間を使用してください。分単位メトリックにはかなりの追加領域が必要になるためです。 データを分析し、保存するメトリックをダウンロードし、オフライン分析やレポートを行うために十分な時間が確保されるようにリテンション期間を選択してください。 ストレージ アカウントからメトリック データをダウンロードした場合にも課金されることにご注意ください。

## <a name="how-to-enable-storage-metrics-using-the-azure-classic-portal"></a>Azure クラシック ポータルを使用してストレージ メトリックを有効にする方法
[Azure クラシック ポータル](https://manage.windowsazure.com)で、ストレージ アカウントの構成ページを使用し、ストレージ メトリックを制御します。 監視の場合、BLOB、テーブル、キューごとにレベルとリテンション期間 (日数) を設定できます。 いずれの場合も、レベルは次のいずれかになります。

* [オフ] — メトリックは収集されません。
* [最低限] - ストレージ メトリックは、BLOB サービス、Table サービス、Queue サービスに対して集計される、受信/送信、可用性、遅延時間、成功のパーセンテージなど、一連の基本的なメトリックを収集します。
* [詳細] - ストレージ メトリックは、サービス レベル メトリックに加えて、ストレージ API 操作ごとに同じメトリックを含む、メトリックの完全セットを収集します。 詳細メトリックにより、アプリケーションの操作中に発生する問題を詳しく分析できます。

Azure クラシック ポータルでは、現在のところ、ストレージ アカウントで分単位メトリックを構成できません。PowerShell かプログラミングを利用して分単位メトリックを有効にする必要があります。

## <a name="how-to-enable-storage-metrics-using-powershell"></a>PowerShell を使用してストレージ メトリックを有効にする方法
ローカル マシンの PowerShell を使用して、ストレージ アカウントのストレージ メトリックを構成できます。Azure PowerShell コマンドレット Get-AzureStorageServiceMetricsProperty を使って現在の設定を取得し、コマンドレット Set-AzureStorageServiceMetricsProperty を使って現在の設定を変更します。

ストレージ メトリックを制御するコマンドレットでは次のパラメーターが使用されます。

* MetricsType 指定可能な値は Hour と Minute です。
* ServiceType: 指定可能な値は、Blob、Queue、Table です。
* MetricsLevel: 指定可能な値は None (Azure クラシック ポータルの [オフ] と同じ)、Service (Azure クラシック ポータルの [最小] と同じ)、ServiceAndApi (Azure クラシック ポータルの [詳細] と同じ) です。

たとえば、次のコマンドは、既定のストレージ アカウントの BLOB サービスの分単位メトリックを&5; 日間に設定されたリテンション期間でオンにします。

```powershell
Set-AzureStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5
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
ストレージ アカウントを監視するようにストレージ メトリックを構成すると、ストレージ アカウントのよく知られた一連のテーブルにメトリックが記録されます。 Azure クラシック ポータルのストレージ アカウントの [監視] ページを使って、グラフで利用できるようになった時間単位メトリックを表示できます。 Azure クラシック ポータルのこのページでは、以下の操作を実行できます。

* グラフに示すメトリックを選択します (利用できるメトリックは、[構成] ページのサービスに詳細または最低限監視を選択したかどうかによって異なります)。
* グラフに表示するメトリックの時間範囲を選択します。
* メトリックを示すために使用するスケールとして絶対スケールと相対スケールのいずれかを選択します。
* 特定のメトリックが特定の値に到達したら通知するように電子メール アラートを構成します。

長期間ストレージのメトリックをダウンロードし、それらをローカルで分析する場合、ツールを使用するか、コードを記述し、テーブルを読み込む必要があります。 分析のために分単位メトリックをダウンロードする必要があります。 ストレージ アカウントにすべてのテーブルを一覧表示した場合、このテーブルは表示されない場合がありますが、名前で直接アクセスできます。 多くのサードパーティ製ストレージ閲覧ツールはこれらのテーブルを認識するため、直接表示できます (利用できるツールの一覧については、 [Microsoft Azure ストレージ エクスプローラー](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) に関するブログ記事をご覧ください)。

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

ストレージ メトリックがストレージ サービスの動作の重要な変化を自動的に通知するように、Azure クラシック ポータルの [監視] ページでアラートを設定するようにしてください。ストレージ エクスプローラー ツールを使用してこのメトリック データを区切り形式でダウンロードすると、Microsoft Excel でデータを分析できます。 使用できるストレージ エクスプローラー ツールの一覧については、[Microsoft Azure ストレージ エクスプローラー](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx)に関するブログ記事をご覧ください。

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

## <a name="next-steps"></a>次のステップ:
[Storage Analytics ログの有効化とログ データへのアクセス](https://msdn.microsoft.com/library/dn782840.aspx)
