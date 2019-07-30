---
title: Azure Monitor ログ クエリの例 | Microsoft Docs
description: Kusto クエリ言語を使用する Azure Monitor でのログ クエリの例です。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/03/2018
ms.author: bwren
ms.openlocfilehash: d50a680ed2b054f87a9cf36e761bd16d79677fb3
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304695"
---
# <a name="azure-monitor-log-query-examples"></a>Azure Monitor ログ クエリの例
この記事には、[Kusto クエリ言語](/azure/kusto/query/)を使用して Azure Monitor から複数の種類のログ データを取得する、多様な[クエリ](log-query-overview.md)の例が含まれています。 データを統合し、分析するためにさまざまな方法が用いられています。そのため、これらのサンプルを使用して、独自の要件に使用できる戦略を識別することができます。  

これらのサンプルで使用されるさまざまなキーワードの詳細については、[Kusto 言語リファレンス](https://docs.microsoft.com/azure/kusto/query/)を参照してください。 Azure Monitor を使用するのが初めてであれば、[クエリの作成に関するレッスン](get-started-queries.md)を行ってください。

## <a name="events"></a>events

### <a name="search-application-level-events-described-as-cryptographic"></a>"Cryptographic" として説明されているアプリケーション レベルのイベントの検索
次の例は、**Events** テーブルから **EventLog** が _Application_ で、**RenderedDescription** に _cryptographic_ が含まれているレコードを検索します。 過去 24 時間のレコードが含まれます。

```Kusto
Event
| where EventLog == "Application" 
| where TimeGenerated > ago(24h) 
| where RenderedDescription contains "cryptographic"
```

### <a name="search-events-related-to-unmarshaling"></a>マーシャリングの解除に関連するイベントの検索
テーブル **Event** と **SecurityEvents** から _unmarshaling_ が言及されているレコードを検索します。

```Kusto
search in (Event, SecurityEvent) "unmarshaling"
```

## <a name="heartbeat"></a>Heartbeat

### <a name="chart-a-week-over-week-view-of-the-number-of-computers-sending-data"></a>データを送信しているコンピューター数を週ごとに比較できるグラフの作成

次の例は、各週のハートビートを送信した個別のコンピューター数をグラフ化します。

```Kusto
Heartbeat
| where TimeGenerated >= startofweek(ago(21d))
| summarize dcount(Computer) by endofweek(TimeGenerated) | render barchart kind=default
```

### <a name="find-stale-computers"></a>古いコンピューターの検索

次の例は、過去 1 日間にアクティブだったものの、過去 1 時間にハートビートを送信しなかったコンピューターを検索します。

```Kusto
Heartbeat
| where TimeGenerated > ago(1d)
| summarize LastHeartbeat = max(TimeGenerated) by Computer
| where isnotempty(Computer)
| where LastHeartbeat < ago(1h)
```

### <a name="get-the-latest-heartbeat-record-per-computer-ip"></a>コンピューター IP ごとの直近のハートビート レコードの取得

次の例は、コンピューター IP ごとに直近のハート ビート レコードを返します。
```Kusto
Heartbeat
| summarize arg_max(TimeGenerated, *) by ComputerIP
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>保護の状態レコードとハートビート レコードのマッチング

次の例は、コンピューターと時間の両方が一致する、関連する保護の状態レコードとハートビート レコードを検索します。
時間フィールドは、秒単位で四捨五入されることに注意してください。 これを行うために、ランタイム bin 計算 (`round_time=bin(TimeGenerated, 1m)`) を使用しました。

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```

### <a name="server-availability-rate"></a>サーバーの可用性率

ハートビート レコードに基づいてサーバーの可用性率を計算します。 可用性は、"1 時間あたり 1 つ以上のハートビート" として定義されます。
したがって、サーバーが 100 時間中 98 時間使用可能な場合、可用性率は 98% です。

```Kusto
let start_time=startofday(datetime("2018-03-01"));
let end_time=now();
Heartbeat
| where TimeGenerated > start_time and TimeGenerated < end_time
| summarize heartbeat_per_hour=count() by bin_at(TimeGenerated, 1h, start_time), Computer
| extend available_per_hour=iff(heartbeat_per_hour>0, true, false)
| summarize total_available_hours=countif(available_per_hour==true) by Computer 
| extend total_number_of_buckets=round((end_time-start_time)/1h)+1
| extend availability_rate=total_available_hours*100/total_number_of_buckets
```


## <a name="multiple-data-types"></a>複数のデータ型

### <a name="chart-the-record-count-per-table"></a>テーブルごとのレコード数のグラフ化
次の例は、過去 5 時間のすべてのテーブルのすべてのレコードを収集し、各テーブルのレコード数をカウントします。 結果は、時間グラフで表示されます。

```Kusto
union withsource=sourceTable *
| where TimeGenerated > ago(5h) 
| summarize count() by bin(TimeGenerated,10m), sourceTable
| render timechart
```

### <a name="count-all-logs-collected-over-the-last-hour-by-type"></a>種類ごとに過去 1 時間に収集されたすべてのログのカウント
次の例は、過去 1 時間で記録されたすべてのログを検索し、**Type** ごとに各テーブルのレコードをカウントします。 結果は棒グラフで表示されます。

```Kusto
search *
| where TimeGenerated > ago(1h) 
| summarize CountOfRecords = count() by Type
| render barchart
```

## <a name="azurediagnostics"></a>AzureDiagnostics

### <a name="count-azure-diagnostics-records-per-category"></a>カテゴリごとの Azure Diagnostics レコードのカウント
次の例は、一意のカテゴリごとの Azure Diagnostics レコードをカウントします。

```Kusto
AzureDiagnostics 
| where TimeGenerated > ago(1d)
| summarize count() by Category
```

### <a name="get-a-random-record-for-each-unique-category"></a>一意の各カテゴリのランダム レコードの取得
次の例は、一意の各カテゴリの Azure Diagnostics レコードをランダムに 1 つ取得します。

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize any(*) by Category
```

### <a name="get-the-latest-record-per-category"></a>カテゴリごとの直近のレコードの取得
次の例は、一意の各カテゴリの直近の Azure Diagnostics レコードを取得します。

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize arg_max(TimeGenerated, *) by Category
```

## <a name="network-monitoring"></a>ネットワーク監視

### <a name="computers-with-unhealthy-latency"></a>異常な待機時間のコンピューター
次の例は、待機時間が異常な個別のコンピューターの一覧を作成します。

```Kusto
NetworkMonitoring 
| where LatencyHealthState <> "Healthy" 
| where Computer != "" 
| distinct Computer
```

## <a name="performance"></a>パフォーマンス

### <a name="join-computer-perf-records-to-correlate-memory-and-cpu"></a>メモリと CPU を関連付けるためにコンピューターのパフォーマンス レコードを結合する
次の例は、特定のコンピューターの **perf** レコードを関連付け、平均 CPU と最大メモリの 2 つの時間グラフを作成します。

```Kusto
let StartTime = now()-5d;
let EndTime = now()-4d;
Perf
| where CounterName == "% Processor Time"  
| where TimeGenerated > StartTime and TimeGenerated < EndTime
and TimeGenerated < EndTime
| project TimeGenerated, Computer, cpu=CounterValue 
| join kind= inner (
   Perf
    | where CounterName == "% Used Memory"  
    | where TimeGenerated > StartTime and TimeGenerated < EndTime
    | project TimeGenerated , Computer, mem=CounterValue 
) on TimeGenerated, Computer
| summarize avgCpu=avg(cpu), maxMem=max(mem) by TimeGenerated bin=30m  
| render timechart
```

### <a name="perf-cpu-utilization-graph-per-computer"></a>コンピューターごとのパフォーマンス CPU 使用率のグラフ
次の例は、_Contoso_ で起動するコンピューターの CPU 使用率を計算し、グラフ化します。

```Kusto
Perf
| where TimeGenerated > ago(4h)
| where Computer startswith "Contoso" 
| where CounterName == @"% Processor Time"
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 15m) 
| render timechart
```

## <a name="protection-status"></a>保護の状態

### <a name="computers-with-non-reporting-protection-status-duration"></a>保護の状態を報告しなかった期間があるコンピューター
次の例は、_Not Reporting_ の保護の状態があったコンピューターと、その状態であった期間の一覧を表示します。

```Kusto
ProtectionStatus
| where ProtectionStatus == "Not Reporting"
| summarize count(), startNotReporting = min(TimeGenerated), endNotReporting = max(TimeGenerated) by Computer, ProtectionStatusDetails
| join ProtectionStatus on Computer
| summarize lastReporting = max(TimeGenerated), startNotReporting = any(startNotReporting), endNotReporting = any(endNotReporting) by Computer
| extend durationNotReporting = endNotReporting - startNotReporting
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>保護の状態レコードとハートビート レコードのマッチング
次の例は、コンピューターと時間の両方が一致する、関連する保護の状態レコードとハートビート レコードを検索します。
時間フィールドは、**bin** を使用して秒単位で四捨五入します。

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```


## <a name="security-records"></a>セキュリティ レコード

### <a name="count-security-events-by-activity-id"></a>アクティビティ ID ごとのセキュリティ イベントのカウント


次の例は、**Activity** 列(\<ID\>-\<Name\>) の固定された構造に依存します。
これは、**Activity** 値を解析して 2 つの新しい列にし、それぞれの **activityID** の発生回数をカウントします。

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| project Activity 
| parse Activity with activityID " - " activityDesc
| summarize count() by activityID
```

### <a name="count-security-events-related-to-permissions"></a>アクセス許可に関連するセキュリティ イベントのカウント
次の例は、**Activity** 列に用語 _Permissions_ が完全に含まれる **securityEvent** レコードの数を表示します。 クエリは、過去 30 分以内に作成されたレコードに適用されます。

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m)
| summarize EventCount = countif(Activity has "Permissions")
```

### <a name="find-accounts-that-failed-to-log-in-from-computers-with-a-security-detection"></a>セキュリティ検出を使用したコンピューターからのログインに失敗したアカウントの検索
次の例は、セキュリティ検出を識別した、コンピューターからのログインに失敗したアカウントを検索し、カウントします。

```Kusto
let detections = toscalar(SecurityDetection
| summarize makeset(Computer));
SecurityEvent
| where Computer in (detections) and EventID == 4624
| summarize count() by Account
```

### <a name="is-my-security-data-available"></a>個人用セキュリティ データを使用できるかどうか
データ探索を開始すると、大抵の場合、データの可用性チェックが開始されます。 次の例は、過去 30 分間の **SecurityEvent** レコードの数を表示します。

```Kusto
SecurityEvent 
| where TimeGenerated  > ago(30m) 
| count
```

### <a name="parse-activity-name-and-id"></a>アクティビティ名と ID の解析
以下の 2 つの例は、**Activity** 列(\<ID\>-\<Name\>) の固定された構造に依存します。 最初の例では、2 つの新しい列 **activityID** と **activityDesc** に値を割り当てるために、**parse** 演算子を使用します。

```Kusto
SecurityEvent
| take 100
| project Activity 
| parse Activity with activityID " - " activityDesc
```

次の例では、別個の値の配列を作成するために **split** 演算子を使用します。
```Kusto
SecurityEvent
| take 100
| project Activity 
| extend activityArr=split(Activity, " - ") 
| project Activity , activityArr, activityId=activityArr[0]
```

### <a name="explicit-credentials-processes"></a>明示的な資格情報プロセス
次の例は、過去 1 週間に明示的な資格情報を使用したプロセスの円グラフを表示します。

```Kusto
SecurityEvent
| where TimeGenerated > ago(7d)
// filter by id 4648 ("A logon was attempted using explicit credentials")
| where EventID == 4648
| summarize count() by Process
| render piechart 
```

### <a name="top-running-processes"></a>上位実行プロセス

次の例は、過去 3 日間で最も使用された 5 つのプロセスのアクティビティのタイム ラインを表示します。

```Kusto
// Find all processes that started in the last three days. ID 4688: A new process has been created.
let RunProcesses = 
    SecurityEvent
    | where TimeGenerated > ago(3d)
    | where EventID == "4688";
// Find the 5 processes that were run the most
let Top5Processes =
RunProcesses
| summarize count() by Process
| top 5 by count_;
// Create a time chart of these 5 processes - hour by hour
RunProcesses 
| where Process in (Top5Processes) 
| summarize count() by bin (TimeGenerated, 1h), Process
| render timechart
```


### <a name="find-repeating-failed-login-attempts-by-the-same-account-from-different-ips"></a>異なる IP アドレスから同じアカウントで繰り返し失敗したログイン試行の検索

次の例は、過去 6 時間で 5 つ以上の異なる IP アドレスから同じアカウントで失敗したログイン試行を検索します。

```Kusto
SecurityEvent 
| where AccountType == "User" and EventID == 4625 and TimeGenerated > ago(6h) 
| summarize IPCount = dcount(IpAddress), makeset(IpAddress)  by Account
| where IPCount > 5
| sort by IPCount desc
```

### <a name="find-user-accounts-that-failed-to-log-in"></a>ログオンに失敗したユーザー アカウントの検索 
次の例は、ログに複数の過去 1 日間に 5 回以上ログインに失敗したユーザー アカウントを識別します。また、最後にログインしようとした時間を表示します。

```Kusto
let timeframe = 1d;
SecurityEvent
| where TimeGenerated > ago(1d)
| where AccountType == 'User' and EventID == 4625 // 4625 - failed log in
| summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
| where failed_login_attempts > 5
| project-away Account1
```

**join** と **let** ステートメントを使用して、同じ疑わしいアカウントが後から正常にログインできたかどうかも確認できます。

```Kusto
let timeframe = 1d;
let suspicious_users = 
    SecurityEvent
    | where TimeGenerated > ago(timeframe)
    | where AccountType == 'User' and EventID == 4625 // 4625 - failed login
    | summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
    | where failed_login_attempts > 5
    | project-away Account1;
let suspicious_users_that_later_logged_in = 
    suspicious_users 
    | join kind=innerunique (
        SecurityEvent
        | where TimeGenerated > ago(timeframe)
        | where AccountType == 'User' and EventID == 4624 // 4624 - successful login,
        | summarize latest_successful_login=arg_max(TimeGenerated, Account) by Account
    ) on Account
    | extend was_login_after_failures = iif(latest_successful_login>latest_failed_login, 1, 0)
    | where was_login_after_failures == 1
;
suspicious_users_that_later_logged_in
```

## <a name="usage"></a>使用法

### <a name="calculate-the-average-size-of-perf-usage-reports-per-computer"></a>コンピューターごとのパフォーマンス使用量レポートの平均サイズの計算

次の例は、過去 3 時間のコンピューターごとのパフォーマンス使用量レポートの平均サイズを計算します。
結果は、棒グラフで表示されます。
```Kusto
Usage 
| where TimeGenerated > ago(3h)
| where DataType == "Perf" 
| where QuantityUnit == "MBytes" 
| summarize avg(Quantity) by Computer
| sort by avg_Quantity desc nulls last
| render barchart
```

### <a name="timechart-latency-percentiles-50-and-95"></a>時間グラフ待機時間のパーセンタイル 50 および 95

次の例は、過去 24 時間に時間単位でレポートされた **avgLatency** の 50 および 95 パーセンタイルを計算し、グラフ化します。

```Kusto
Usage
| where TimeGenerated > ago(24h)
| summarize percentiles(AvgLatencyInSeconds, 50, 95) by bin(TimeGenerated, 1h) 
| render timechart
```

### <a name="usage-of-specific-computers-today"></a>当日の特定のコンピューターの使用状況
次の例は、コンピューター名に文字列 _ContosoFile_ が含まれているコンピューターの過去 1 日間の **Usage** を取得します。 結果は、**TimeGenerated** で並べ替えられます。

```Kusto
Usage
| where TimeGenerated > ago(1d)
| where  Computer contains "ContosoFile" 
| sort by TimeGenerated desc nulls last
```

## <a name="updates"></a>更新プログラム

### <a name="computers-still-missing-updates"></a>まだ更新プログラムを適用していないコンピューター
この例は、数日前に 1 つ以上の重要な更新プログラムを適用していなかったコンピューター、およびまだ適用していないコンピューターの一覧を表示します。

```Kusto
let ComputersMissingUpdates3DaysAgo = Update
| where TimeGenerated between (ago(3d)..ago(2d))
| where  Classification == "Critical Updates" and UpdateState != "Not needed" and UpdateState != "NotNeeded"
| summarize makeset(Computer);

Update
| where TimeGenerated > ago(1d)
| where  Classification == "Critical Updates" and UpdateState != "Not needed" and UpdateState != "NotNeeded"
| where Computer in (ComputersMissingUpdates3DaysAgo)
| summarize UniqueUpdatesCount = dcount(Product) by Computer, OSType
```


## <a name="next-steps"></a>次の手順

- 言語の詳細については、[Kusto 言語リファレンス](/azure/kusto/query)を参照してください。
- [Azure Monitor でのログ クエリの記述に関するレッスン](get-started-queries.md)を行います。
