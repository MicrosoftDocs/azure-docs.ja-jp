---
title: "Azure Search の検索トラフィックの分析 |Microsoft Docs"
description: "Azure Search の検索トラフィックの分析 (Microsoft Azure 上のクラウド ホスト検索サービス) を有効にして、ユーザーとデータに関する洞察のロックを解除します。"
services: search
documentationcenter: 
author: bernitorres
manager: pablocas
editor: 
ms.assetid: b31d79cf-5924-4522-9276-a1bb5d527b13
ms.service: search
ms.devlang: multiple
ms.workload: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/22/2017
ms.author: betorres
translationtype: Human Translation
ms.sourcegitcommit: cdcf121e52eaf6a1fc45194b7a4f5a02e9e5c001
ms.openlocfilehash: f6b354caf37f94906865b5a2f334e2b7a02f9d5b

---

# <a name="enabling-and-using-search-traffic-analytics"></a>検索トラフィックの分析の有効化と使用
検索トラフィックの分析は、使用している検索サービスを表示し、ユーザーとその動作に関する洞察のロックを解除できる Azure Search の機能です。 この機能を有効にすると、検索サービスのデータは選択しているストレージ アカウントにコピーされます。 このデータには、Search サービスのログと集計された運用メトリックが含まれます。これらを処理および操作して、さらに詳しい分析を行うことができます。

## <a name="how-to-enable-search-traffic-analytics"></a>検索トラフィックの分析を有効にする方法
Search サービスと同じリージョンおよびサブスクリプション内にあるストレージ アカウントが必要です。

> [!IMPORTANT]
> このストレージ アカウントには標準料金が適用されます。
>
>

検索トラフィックの分析は、ポータルまたは PowerShell から有効にできます。 有効にすると、5 ～ 10 分以内にデータのストレージ アカウントへのフローが開始され、次の 2 つの BLOB コンテナーに格納されます。

    insights-logs-operationlogs: search traffic logs
    insights-metrics-pt1m: aggregated metrics


### <a name="a-using-the-portal"></a>A. ポータルの使用
[Azure Portal](http://portal.azure.com)で Azure Search サービスを開きます。 [設定] には、[検索トラフィックの分析] オプションが表示されます。

![][1]

[状態] を **[オン]**に変更し、使用する Azure Storage アカウントを選択して、コピーするデータ (ログ、メトリック、または両方) を選択します。 ログとメトリックをコピーすることをお勧めします。
データのリテンション期間ポリシーを 1 日から 365 日の間で設定できます。 データを無期限に保持する場合は、リテンション期間 (日数) を 0 に設定します。

![][2]

### <a name="b-using-powershell"></a>B. PowerShell の使用
まず、最新の [Azure PowerShell コマンドレット](https://github.com/Azure/azure-powershell/releases) がインストールされていることを確認します。

次に、検索サービスとストレージ アカウントのリソース ID を取得します。 リソース ID は、ポータルで [設定]、[プロパティ]、[リソース ID] の順にクリックして確認できます。

![][3]

```PowerShell
Login-AzureRmAccount
$SearchServiceResourceId = "Your Search service resource id"
$StorageAccountResourceId = "Your Storage account resource id"
Set-AzureRmDiagnosticSetting -ResourceId $SearchServiceResourceId StorageAccountId $StorageAccountResourceId -Enabled $true
```

## <a name="understanding-the-data"></a>データの説明
データは、JSON 形式で Azure Storage BLOB に格納されます。

1 時間ごと、コンテナーごとに、1 つの BLOB があります。

例のパス: `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2015/m=12/d=25/h=01/m=00/name=PT1H.json`

### <a name="logs"></a>ログ
ログ BLOB には、検索サービスのトラフィック ログが含まれています。
各 BLOB には、ログ オブジェクトの配列を含む、 **レコード** と呼ばれるルート オブジェクトが&1; つあります。
各 BLOB には、同じ時間帯に行われたすべての操作に関するレコードが含まれます。

#### <a name="log-schema"></a>ログのスキーマ
| 名前 | 型 | 例 | メモ |
| --- | --- | --- | --- |
| time |datetime |"2015-12-07T00:00:43.6872559Z" |操作のタイムスタンプ |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |使用している ResourceId |
| operationName |string |"Query.Search" |操作の名前 |
| operationVersion |string |"2016-09-01" |使用されている API バージョン |
| カテゴリ |string |"OperationLogs" |定数 |
| resultType |string |"Success" |使用可能な値: Success または Failure |
| resultSignature |int |200 |HTTP の結果コード |
| durationMS |int |50 |操作時間 (ミリ秒) |
| プロパティ |オブジェクト |次の表を参照 |操作固有データを含むオブジェクト |

#### <a name="properties-schema"></a>プロパティのスキーマ
| 名前 | 型 | 例 | メモ |
| --- | --- | --- | --- |
| Description |string |"GET /indexes('content')/docs" |操作のエンドポイント |
| クエリ |string |"?search=AzureSearch&$count=true&api-version=2016-09-01" |クエリ パラメーター |
| ドキュメント |int |42 |処理されたドキュメント数 |
| IndexName |string |"testindex" |操作に関連付けられているインデックスの名前 |

### <a name="metrics"></a>メトリック
メトリック BLOB には、検索サービスの集計値が含まれます。
各ファイルには、メトリック オブジェクトの配列を含む、 **レコード** と呼ばれるルート オブジェクトが&1; つあります。 このルート オブジェクトには、データが使用可能であった毎分のメトリックが含まれます。

使用可能なメトリック:

* SearchLatency: 検索サービスが検索クエリを処理するために必要とした時間。分単位で集計。
* SearchQueriesPerSecond:&1; 秒間に受信した検索クエリの数。分単位で集計。
* ThrottledSearchQueriesPercentage: スロットルされた検索クエリの割合。分単位で集計。

> [!IMPORTANT]
> スロットルは、送信されたクエリが多すぎて、サービスのプロビジョニングされているリソースの容量が不足したときに発生します。 サービスへのレプリカの追加を検討してください。
>
>

#### <a name="metrics-schema"></a>メトリックのスキーマ
| 名前 | 型 | 例 | メモ |
| --- | --- | --- | --- |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |使用しているリソース ID |
| metricName |string |"Latency" |メトリックの名前 |
| time |datetime |"2015-12-07T00:00:43.6872559Z" |操作のタイムスタンプ |
| average |int |64 |メトリックの時間間隔内の生のサンプルの平均値 |
| minimum |int |37 |メトリックの時間間隔内の生のサンプルの最小値 |
| maximum |int |78 |メトリックの時間間隔内の生のサンプルの最大値 |
| total |int |258 |メトリックの時間間隔内の生のサンプルの合計値 |
| count |int |4 |メトリックの生成に使用される生のサンプル数 |
| timegrain |string |"PT1M" |ISO 8601 でのメトリックの時間グレイン |

すべてのメトリックは、1 分間隔で報告されます。 各メトリックは、1 分あたりの最小値、最大値、および平均値を公開します。

SearchQueriesPerSecond メトリックの場合、最小値は、該当する&1; 分間に登録された秒あたりの検索クエリ数のうち最小の値になります。 最大値も同様です。 平均値は、1 分間にわたって集計されます。
1 分間のうち、1 秒は非常に負荷が高く、続く 58 秒は平均的な負荷で、最後の 1 秒はクエリが 1 つだけというシナリオを考えてみましょう。この場合、最初の 1 秒の値が SearchQueriesPerSecond の最大値、最後の １ 秒の値が最小値となります。

ThrottledSearchQueriesPercentage の場合は、最小値、最大値、平均値、および合計値はすべて同じ値になります。これは、スロットルされた検索クエリの割合であり、1 分間の検索クエリの合計数に基づきます。

## <a name="analyzing-your-data"></a>データの分析
データは使用しているストレージ アカウントにあり、自分の環境に最も適した方法で、このデータを検索することをお勧めします。

最初に、 [Power BI](https://powerbi.microsoft.com) を使用して、データを検索および視覚化することをお勧めします。 Azure Storage アカウントに容易に接続し、データの分析を迅速に開始できます。

#### <a name="power-bi-online"></a>Power BI Online
[Power BI コンテンツ パック](https://app.powerbi.com/getdata/services/azure-search): データが自動的に表示され、検索サービスに関する情報を視覚化できる Power BI ダッシュボードと Power BI レポートのセットを作成します。 [コンテンツ パックのヘルプ ページ](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-search/)をご覧ください。

![][4]

#### <a name="power-bi-desktop"></a>Power BI Desktop
[Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop): データを調査し、データの視覚化を作成します。 次のセクションで、基本的なクエリを参照してください。

1. 新しい PowerBI Desktop レポートを開きます。

2. [データの取得]、[詳細] の順に選択します

    ![][5]

3. Microsoft Azure Blob Storage を選択して接続します。

    ![][6]

4. 使用しているストレージ アカウントの名前とアカウント キーを入力します。

5. "insight-logs-operationlogs" と "insights-metrics-pt1m" を選択して、[編集] をクリックします。

6. クエリ エディターが開いたら、左側で "insight-logs-operationlogs" が選択されていることを確認します。 ここで、[表示]、[詳細エディター] の順に選択して、詳細エディターを開きます。

    ![][7]
    
7. 最初の&2; つの行を保持し、残りの部分を次のクエリに置き換えます。

   ~~~~
   > # "insights-logs-operationlogs" = Source{[Name="insights-logs-operationlogs"]}[Data],
   > # "Sorted Rows" = Table.Sort(#"insights-logs-operationlogs",{{"Date modified", Order.Descending}}),
   > # "Kept First Rows" = Table.FirstN(#"Sorted Rows",744),
   > # "Removed Columns" = Table.RemoveColumns(#"Kept First Rows",{"Name", "Extension", "Date accessed", "Date modified", "Date created", "Attributes", "Folder Path"}),
   > # "Parsed JSON" = Table.TransformColumns(#"Removed Columns",{},Json.Document),
   > # "Expanded Content" = Table.ExpandRecordColumn(#"Parsed JSON", "Content", {"records"}, {"records"}),
   > # "Expanded records" = Table.ExpandListColumn(#"Expanded Content", "records"),
   > # "Expanded records1" = Table.ExpandRecordColumn(#"Expanded records", "records", {"time", "resourceId", "operationName", "operationVersion", "category", "resultType", "resultSignature", "durationMS", "properties"}, {"time", "resourceId", "operationName", "operationVersion", "category", "resultType", "resultSignature", "durationMS", "properties"}),
   > # "Expanded properties" = Table.ExpandRecordColumn(#"Expanded records1", "properties", {"Description", "Query", "IndexName", "Documents"}, {"Description", "Query", "IndexName", "Documents"}),
   > # "Renamed Columns" = Table.RenameColumns(#"Expanded properties",{{"time", "Datetime"}, {"resourceId", "ResourceId"}, {"operationName", "OperationName"}, {"operationVersion", "OperationVersion"}, {"category", "Category"}, {"resultType", "ResultType"}, {"resultSignature", "ResultSignature"}, {"durationMS", "Duration"}}),
   > # "Added Custom2" = Table.AddColumn(#"Renamed Columns", "QueryParameters", each Uri.Parts("http://tmp" & [Query])),
   > # "Expanded QueryParameters" = Table.ExpandRecordColumn(#"Added Custom2", "QueryParameters", {"Query"}, {"Query.1"}),
   > # "Expanded Query.1" = Table.ExpandRecordColumn(#"Expanded QueryParameters", "Query.1", {"search", "$skip", "$top", "$count", "api-version", "searchMode", "$filter"}, {"search", "$skip", "$top", "$count", "api-version", "searchMode", "$filter"}),
   > # "Removed Columns1" = Table.RemoveColumns(#"Expanded Query.1",{"OperationVersion"}),
   > # "Changed Type" = Table.TransformColumnTypes(#"Removed Columns1",{{"Datetime", type datetimezone}, {"ResourceId", type text}, {"OperationName", type text}, {"Category", type text}, {"ResultType", type text}, {"ResultSignature", type text}, {"Duration", Int64.Type}, {"Description", type text}, {"Query", type text}, {"IndexName", type text}, {"Documents", Int64.Type}, {"search", type text}, {"$skip", Int64.Type}, {"$top", Int64.Type}, {"$count", type logical}, {"api-version", type text}, {"searchMode", type text}, {"$filter", type text}}),
   > # "Inserted Date" = Table.AddColumn(#"Changed Type", "Date", each DateTime.Date([Datetime]), type date),
   > # "Duplicated Column" = Table.DuplicateColumn(#"Inserted Date", "ResourceId", "Copy of ResourceId"),
   > # "Split Column by Delimiter" = Table.SplitColumn(#"Duplicated Column","Copy of ResourceId",Splitter.SplitTextByEachDelimiter({"/"}, null, true),{"Copy of ResourceId.1", "Copy of ResourceId.2"}),
   > # "Changed Type1" = Table.TransformColumnTypes(#"Split Column by Delimiter",{{"Copy of ResourceId.1", type text}, {"Copy of ResourceId.2", type text}}),
   > # "Removed Columns2" = Table.RemoveColumns(#"Changed Type1",{"Copy of ResourceId.1"}),
   > # "Renamed Columns1" = Table.RenameColumns(#"Removed Columns2",{{"Copy of ResourceId.2", "ServiceName"}}),
   > # "Lowercased Text" = Table.TransformColumns(#"Renamed Columns1",{{"ServiceName", Text.Lower}}),
   > # "Added Custom" = Table.AddColumn(#"Lowercased Text", "DaysFromToday", each Duration.Days(DateTimeZone.UtcNow() - [Datetime])),
   > # "Changed Type2" = Table.TransformColumnTypes(#"Added Custom",{{"DaysFromToday", Int64.Type}})
   > in
   >
   > # "Changed Type2"
   >
   ~~~~

8. [Done] をクリックします。

9. ここで、クエリの左側にある "insights-metrics-pt1m" を選択し、再度 [詳細エディター] を開きます。 最初の&2; つの行を保持し、残りの部分を次のクエリに置き換えます。

   ~~~~
   > # "insights-metrics-pt1m1" = Source{[Name="insights-metrics-pt1m"]}[Data],
   > # "Sorted Rows" = Table.Sort(#"insights-metrics-pt1m1",{{"Date modified", Order.Descending}}),
   > # "Kept First Rows" = Table.FirstN(#"Sorted Rows",744),
   > # "Removed Columns" = Table.RemoveColumns(#"Kept First Rows",{"Name", "Extension", "Date accessed", "Date modified", "Date created", "Attributes", "Folder Path"}),
   > # "Parsed JSON" = Table.TransformColumns(#"Removed Columns",{},Json.Document),
   > # "Expanded Content" = Table.ExpandRecordColumn(#"Parsed JSON", "Content", {"records"}, {"records"}),
   > # "Expanded records" = Table.ExpandListColumn(#"Expanded Content", "records"),
   > # "Expanded records1" = Table.ExpandRecordColumn(#"Expanded records", "records", {"resourceId", "metricName", "time", "average", "minimum", "maximum", "total", "count", "timeGrain"}, {"resourceId", "metricName", "time", "average", "minimum", "maximum", "total", "count", "timeGrain"}),
   > # "Filtered Rows" = Table.SelectRows(#"Expanded records1", each ([metricName] = "Latency")),
   > # "Removed Columns1" = Table.RemoveColumns(#"Filtered Rows",{"timeGrain"}),
   > # "Renamed Columns" = Table.RenameColumns(#"Removed Columns1",{{"time", "Datetime"}, {"resourceId", "ResourceId"}, {"metricName", "MetricName"}, {"average", "Average"}, {"minimum", "Minimum"}, {"maximum", "Maximum"}, {"total", "Total"}, {"count", "Count"}}),
   > # "Changed Type" = Table.TransformColumnTypes(#"Renamed Columns",{{"ResourceId", type text}, {"MetricName", type text}, {"Datetime", type datetimezone}, {"Average", type number}, {"Minimum", Int64.Type}, {"Maximum", Int64.Type}, {"Total", Int64.Type}, {"Count", Int64.Type}}),
   > Rounding = Table.TransformColumns(#"Changed Type",{{"Average", each Number.Round(_, 2)}}),
   >
   > # "Changed Type1" = Table.TransformColumnTypes(Rounding,{{"Average", type number}}),
   > # "Inserted Date" = Table.AddColumn(#"Changed Type1", "Date", each DateTime.Date([Datetime]), type date)
   > in
   >
   > # "Inserted Date"
   >
   ~~~~

10. [完了] をクリックし、[ホーム] タブで [閉じて適用] を選択します。

11. これで過去 30 日間のデータを使用する準備ができました。 今すぐ[視覚化](https://powerbi.microsoft.com/en-us/documentation/powerbi-desktop-report-view/)を作成しましょう。

## <a name="next-steps"></a>次のステップ
検索構文とクエリ パラメーターについて詳細に学習します。 詳細については、「 [ドキュメントの検索 (Azure Search Service REST API)](https://msdn.microsoft.com/library/azure/dn798927.aspx) 」を参照してください。

レポートの作成についての詳細。 「[Power BI Desktop の概要](https://powerbi.microsoft.com/en-us/documentation/powerbi-desktop-getting-started/)」をご覧ください。

<!--Image references-->

[1]: ./media/search-traffic-analytics/SettingsBlade.png
[2]: ./media/search-traffic-analytics/DiagnosticsBlade.png
[3]: ./media/search-traffic-analytics/ResourceId.png
[4]: ./media/search-traffic-analytics/Dashboard.png
[5]: ./media/search-traffic-analytics/GetData.png
[6]: ./media/search-traffic-analytics/BlobStorage.png
[7]: ./media/search-traffic-analytics/QueryEditor.png



<!--HONumber=Jan17_HO4-->


