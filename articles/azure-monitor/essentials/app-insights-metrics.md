---
title: Azure Application Insights ログベースのメトリック | Microsoft Docs
description: この記事では Azure Application Insights のメトリックについて説明し、サポートされる集計方法およびディメンションを示します。 ログベースのメトリックの詳細情報には、基になる Kusto クエリ ステートメントが含まれています。
author: vgorbenko
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.author: vitalyg
ms.openlocfilehash: bae7980137c1da5e1755450863af4f9029d6ed62
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104583464"
---
# <a name="application-insights-log-based-metrics"></a>Application Insights ログベースのメトリック

Application Insights ログベースのメトリックを使用すると、監視対象アプリの正常性の分析、強力なダッシュボードの作成、アラートの構成を行うことができます。 次の 2 種類のメトリックがあります。

* バック グラウンドの[ログベースのメトリック](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics)は、格納されているイベントから [Kusto クエリ](/azure/kusto/query/)に変換されます。
* [標準メトリック](../app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics)は、事前に集計された時系列として格納されます。

*標準メトリック* は収集中に事前に集計されるため、クエリ時のパフォーマンスが優れています。 このことから、ダッシュボードやリアルタイム アラートで使用することをお勧めします。 *ログベースのメトリック* には、より多くのディメンションがあるため、データ分析やアドホック診断のための優れたオプションとなります。 [名前空間セレクター](./metrics-getting-started.md#create-your-first-metric-chart)を使用して、[メトリックス エクスプローラー](./metrics-getting-started.md)でログベースのメトリックと標準メトリックを切り替えます。

## <a name="interpret-and-use-queries-from-this-article"></a>この記事のクエリを解釈して使用する

この記事ではメトリックについて説明し、サポートされる集計方法とディメンションを示します。 ログベースのメトリックの詳細情報には、基になる Kusto クエリ ステートメントが含まれています。 各クエリは便宜上、時間の粒度、グラフの種類、および場合によっては分割セグメントに既定値を使用します。これにより、Log Analytics でクエリを変更せずに簡単に使用できます。

同じメトリックを[メトリックス エクスプローラー](./metrics-getting-started.md)でプロットすると、既定値は存在せず、クエリはグラフの設定に基づいて動的に調整されます。

- 選択された **時間の範囲** は、選択された時間の範囲のイベントのみを選択する追加の *where timestamp...* 句に変換されます。 たとえば、過去 24 時間のデータを示すグラフの場合、クエリには *| where timestamp > ago (24 h)* が含まれます。

- 選択された **時間の粒度** は最後の *summarize ... by bin(timestamp, [time grain])* 句に変換されます。

- 選択されたすべての **フィルター** ディメンションは、追加の *where* 句に変換されます。

- 選択された **グラフの分割** のディメンションは、追加の集計プロパティに変換されます。 たとえば、*場所* によってグラフを分割し、5 分の時間の粒度を使用してプロットする場合、*summarize* 句は *... by bin(timestamp, 5 m), location* となります。

> [!NOTE]
> Kusto クエリ言語を初めて使用する場合は、まず Kusto ステートメントをコピーし、変更を加えずに Log Analytics クエリ ウィンドウに貼り付けます。 **[Run]\(実行\)** をクリックして、基本的なグラフを表示します。 クエリ言語の構文を理解し始めたら、わずかな変更を行い変更の影響を確認します。 [Log Analytics](../logs/log-analytics-tutorial.md) と [Azure Monitor](../overview.md) の最大限の能力を理解するには、独自のデータを探索することから始めるのが最もよいやり方です。

## <a name="availability-metrics"></a>可用性のメトリック

可用性カテゴリのメトリックを使用すると、世界中の地点から観察された Web アプリケーションの正常性を確認できます。 このカテゴリにあるメトリックの使用を開始するには、[可用性テストを構成します](../app/monitor-web-app-availability.md)。

### <a name="availability-availabilityresultsavailabilitypercentage"></a>可用性 (availabilityResults/availabilityPercentage)
*可用性* メトリックは、問題が検出されなかった Web テストの実行の割合を示します。 可能な最小値は 0 で、これはすべての Web テストの実行が失敗したことを示します。 値 100 は、すべての Web テストの実行が検証条件に合格したことを意味します。

|Unit of measure|サポートされる集計|サポートされるディメンション|
|---|---|---|---|---|---|
|パーセント|Average|実行場所、テスト名|

```Kusto
availabilityResults 
| summarize sum(todouble(success == 1) * 100) / count() by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-test-duration-availabilityresultsduration"></a>可用性テスト継続時間 (availabilityResults/duration)

*可用性テスト継続時間* メトリックは、Web テストの実行にかかった時間を示します。 [複数ステップの Web テスト](../app/availability-multistep.md)の場合、メトリックには、すべてのステップの合計実行時間が反映されます。

|Unit of measure|サポートされる集計|サポートされるディメンション|
|---|---|---|---|---|---|
|ミリ秒|平均、最小、最大|実行場所、テスト名、テスト結果

```Kusto
availabilityResults
| where notempty(duration)
| extend availabilityResult_duration = iif(itemType == 'availabilityResult', duration, todouble(''))
| summarize sum(availabilityResult_duration)/sum(itemCount) by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-tests-availabilityresultscount"></a>可用性テスト (availabilityResults/count)

*可用性テスト* のメトリックには、Azure Monitor によって実行される Web テストの数が反映されます。

|Unit of measure|サポートされる集計|サポートされるディメンション|
|---|---|---|---|---|---|
|Count|Count|実行場所、テスト名、テスト結果|

```Kusto
availabilityResults
| summarize sum(itemCount) by bin(timestamp, 5m)
| render timechart
```

## <a name="browser-metrics"></a>ブラウザー メトリック

ブラウザー メトリックは、実際のエンドユーザーのブラウザーから Application Insights JavaScript SDK によって収集されます。 これらは Web アプリでのユーザー エクスペリエンスに関する優れた分析情報を提供します。 通常、ブラウザー メトリックはサンプリングされません。つまり、サンプリングによって偏る可能性があるサーバー側のメトリックと比較して、使用状況の数値の精度が高くなります。

> [!NOTE]
> ブラウザー メトリックを収集するには、アプリケーションを [Application Insights JavaScript SDK](../app/javascript.md) でインストルメント化する必要があります。

### <a name="browser-page-load-time-browsertimingstotalduration"></a>ブラウザーのページ読み込み時間 (browserTimings/totalDuration)

ユーザーが要求を出してから DOM、スタイル シート、スクリプト、およびイメージが読み込まれるまでの時間。

|Unit of measure|サポートされる集計|事前に集計されたディメンション|
|---|---|---|
|ミリ秒|平均、最小、最大|なし|

```Kusto
browserTimings
| where notempty(totalDuration)
| extend _sum = totalDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="client-processing-time-browsertimingprocessingduration"></a>クライアントの処理時間 (browserTiming/processingDuration)

ドキュメントの最終バイトを受信してから、DOM が読み込まれるまでの時間。 非同期要求がまだ処理されている可能性があります。

|Unit of measure|サポートされる集計|事前に集計されたディメンション|
|---|---|---|
|ミリ秒|平均、最小、最大|なし|

```Kusto
browserTimings
| where notempty(processingDuration)
| extend _sum = processingDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>ページ読み込みのネットワーク接続時間 (browserTimings/networkDuration)

ユーザー要求からネットワーク接続までの時間。 DNS の参照とトランスポートの接続が含まれます。

|Unit of measure|サポートされる集計|事前に集計されたディメンション|
|---|---|---|
|ミリ秒|平均、最小、最大|なし|

```Kusto
browserTimings
| where notempty(networkDuration)
| extend _sum = networkDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>受信側の応答時間 (browserTimings/receiveDuration)

最初のバイトから最後のバイトまで、または切断までの時間。

|Unit of measure|サポートされる集計|事前に集計されたディメンション|
|---|---|---|
|ミリ秒|平均、最小、最大|なし|

```Kusto
browserTimings
| where notempty(receiveDuration)
| extend _sum = receiveDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="send-request-time-browsertimingssendduration"></a>送信要求時間 (browserTimings/sendDuration)

ネットワーク接続から、最初のバイトを受信するまでの時間。

|Unit of measure|サポートされる集計|事前に集計されたディメンション|
|---|---|---|
|ミリ秒|平均、最小、最大|なし|

```Kusto
browserTimings
| where notempty(sendDuration)
| extend _sum = sendDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

## <a name="failure-metrics"></a>エラー メトリック

**エラー** のメトリックには、処理要求、依存関係呼び出し、およびスローされた例外に関する問題が表示されます。

### <a name="browser-exceptions-exceptionsbrowser"></a>ブラウザーの例外 (exceptions/browser)

このメトリックは、ブラウザーで実行されているアプリケーション コードからスローされた例外の数を反映します。 メトリックには、```trackException()``` Application Insights API 呼び出しで追跡される例外のみが含まれます。

|Unit of measure|サポートされる集計|事前に集計されたディメンション|Notes|
|---|---|---|---|
|Count|Count|なし|ログベースのバージョンでは **Sum** 集計を使用します|

```Kusto
exceptions
| where notempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-call-failures-dependenciesfailed"></a>依存関係呼び出しの失敗数 (dependencies/failed)

失敗した依存関係呼び出しの数。

|Unit of measure|サポートされる集計|事前に集計されたディメンション|Notes|
|---|---|---|---|
|Count|Count|なし|ログベースのバージョンでは **Sum** 集計を使用します|

```Kusto
dependencies
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="exceptions-exceptionscount"></a>例外 (exceptions/count)

Application Insights に例外を記録するたびに、SDK の[trackexception () メソッド](../app/api-custom-events-metrics.md#trackexception)が呼び出されます。 例外メトリックは、ログに記録された例外の数を示します。

|Unit of measure|サポートされる集計|事前に集計されたディメンション|Notes|
|---|---|---|---|
|Count|Count|クラウド ロール名、クラウド ロール インスタンス、デバイスの種類|ログベースのバージョンでは **Sum** 集計を使用します|

```Kusto
exceptions
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="failed-requests-requestsfailed"></a>失敗した要求 (requests/failed)

*失敗* としてマークされた追跡されたサーバー要求の数。 既定では、Application Insights SDK は HTTP 応答コード 5xx または 4xx を返した各サーバー要求を、失敗した要求として自動的にマークします。 このロジックをカスタマイズするには、[カスタムのテレメトリ初期化子](../app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)の要求テレメトリ項目の *success* プロパティを変更します。

|Unit of measure|サポートされる集計|事前に集計されたディメンション|Notes|
|---|---|---|---|
|Count|Count|クラウド ロール インスタンス、クラウド ロール名、実際のトラフィックまたは合成トラフィック、要求パフォーマンス、応答コード|ログベースのバージョンでは **Sum** 集計を使用します|

```Kusto
requests
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-exceptions-exceptionsserver"></a>サーバーの例外 (exceptions/server)

このメトリックは、サーバーの例外の数を示します。

|Unit of measure|サポートされる集計|事前に集計されたディメンション|Notes|
|---|---|---|---|
|Count|Count|クラウド ロール名、クラウド ロール インスタンス|ログベースのバージョンでは **Sum** 集計を使用します|

```Kusto
exceptions
| where isempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

## <a name="performance-counters"></a>パフォーマンス カウンター

**パフォーマンス カウンター** カテゴリのメトリックを使用して [、Application Insights](../app/performance-counters.md) によって収集されたシステム パフォーマンス カウンターにアクセスします。

### <a name="available-memory-performancecountersavailablememory"></a>使用可能なメモリ (performanceCounters/availableMemory)

```Kusto
performanceCounters
| where ((category == "Memory" and counter == "Available Bytes") or name == "availableMemory")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="exception-rate-performancecountersexceptionrate"></a>例外レート (performanceCounters/exceptionRate)

```Kusto
performanceCounters
| where ((category == ".NET CLR Exceptions" and counter == "# of Exceps Thrown / sec") or name == "exceptionRate")
| extend performanceCounter_value = iif(itemType == 'performanceCounter',value,todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>HTTP 要求実行時間 (performanceCounters/requestExecutionTime)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Request Execution Time") or name == "requestExecutionTime")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-rate-performancecountersrequestspersecond"></a>HTTP 要求率 (performanceCounters/requestsPerSecond)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests/Sec") or name == "requestsPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>アプリケーション キュー内の HTTP 要求 (performanceCounters/requestsInQueue)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests In Application Queue") or name == "requestsInQueue")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-cpu-performancecountersprocesscpupercentage"></a>プロセス CPU (performanceCounters/processCpuPercentage)

このメトリックは、監視対象のアプリをホストしているプロセスによって消費されるプロセッサの容量の合計を示します。

|Unit of measure|サポートされる集計|サポートされるディメンション|
|---|---|---|
|パーセント|平均、最小、最大|クラウド ロール インスタンス

```Kusto
performanceCounters
| where ((category == "Process" and counter == "% Processor Time Normalized") or name == "processCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>プロセス IO 量 (performanceCounters/processIOBytesPerSecond)

|Unit of measure|サポートされる集計|サポートされるディメンション|
|---|---|---|
|バイト/秒|平均、最小、最大|クラウド ロール インスタンス

```Kusto
performanceCounters
| where ((category == "Process" and counter == "IO Data Bytes/sec") or name == "processIOBytesPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>プロセス プライベート バイト (performanceCounters/processPrivateBytes)

監視対象プロセスによってデータに割り当てられた非共有メモリの量。

|Unit of measure|サポートされる集計|サポートされるディメンション|
|---|---|---|
|バイト|平均、最小、最大|クラウド ロール インスタンス

```Kusto
performanceCounters
| where ((category == "Process" and counter == "Private Bytes") or name == "processPrivateBytes")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>プロセッサ時間 (performanceCounters/processorCpuPercentage)

監視対象のサーバー インスタンスで実行されている *すべての* プロセスによる CPU 使用量。

|Unit of measure|サポートされる集計|サポートされるディメンション|
|---|---|---|
|パーセント|平均、最小、最大|クラウド ロール インスタンス

>[!NOTE]
> プロセッサ時間メトリックは、Azure App Service でホストされているアプリケーションでは使用できません。 App Services でホストされている Web アプリケーションの CPU 使用率を追跡するには、[プロセス CPU](#process-cpu-performancecountersprocesscpupercentage) メトリックを使用します。

```Kusto
performanceCounters
| where ((category == "Processor" and counter == "% Processor Time") or name == "processorCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

## <a name="server-metrics"></a>サーバー メトリック

### <a name="dependency-calls-dependenciescount"></a>依存関係呼び出し (dependencies/count)

このメトリックは、依存関係呼び出しの数に関連しています。

```Kusto
dependencies
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-duration-dependenciesduration"></a>依存関係の期間 (dependencies/duration)

このメトリックは、依存関係呼び出しの期間を示します。

```Kusto
dependencies
| where notempty(duration)
| extend dependency_duration = iif(itemType == 'dependency',duration,todouble(''))
| extend _sum = dependency_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 1m)
| render timechart
```

### <a name="server-requests-requestscount"></a>サーバー要求 (requests/count)

このメトリックは、Web アプリケーションによって受信された受信サーバー要求の数を反映します。

```Kusto
requests
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-response-time-requestsduration"></a>サーバー応答時間 (requests/duration)

このメトリックは、サーバーが受信要求を処理するのにかかった時間を反映します。

```Kusto
requests
| where notempty(duration)
| extend request_duration = iif(itemType == 'request', duration, todouble(''))
| extend _sum = request_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 1m)
| render timechart
```

## <a name="usage-metrics"></a>使用状況のメトリック

### <a name="page-view-load-time-pageviewsduration"></a>ページ ビューの読み込み時間 (pageViews/duration)

このメトリックは、PageView イベントの読み込みにかかった時間を示します。

```Kusto
pageViews
| where notempty(duration)
| extend pageView_duration = iif(itemType == 'pageView', duration, todouble(''))
| extend _sum = pageView_duration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render barchart
```

### <a name="page-views-pageviewscount"></a>ページ ビュー (pageViews/count)

TrackPageView () Application Insights API でログに記録された PageView イベントの数。

```Kusto
pageViews
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="sessions-sessionscount"></a>セッション (sessions/count)

このメトリックは、個別のセッション ID の数を示します。

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(session_Id)
| summarize dcount(session_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="traces-tracescount"></a>トレース (traces/count)

TrackTrace () Application Insights API 呼び出しを使用してログに記録されたトレース ステートメントの数。

```Kusto
traces
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-userscount"></a>ユーザー (users/count)

アプリケーションにアクセスした個別のユーザーの数。 テレメトリ サンプリングとフィルター処理を使用すると、このメトリックの精度が大幅に低下する可能性があります。

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_Id)
| summarize dcount(user_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-authenticated-usersauthenticated"></a>ユーザー、認証済み (users/authenticated)

アプリケーションに認証された個別のユーザーの数。

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_AuthenticatedId)
| summarize dcount(user_AuthenticatedId) by bin(timestamp, 1h)
| render barchart
```