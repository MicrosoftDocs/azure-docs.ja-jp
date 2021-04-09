---
title: ログ アラート クエリを最適化する | Microsoft Docs
description: 効率的なアラート クエリを記述するための推奨事項
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: a7d65d7c65dabde3834458a36b50216878f7cf8d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031295"
---
# <a name="optimizing-log-alert-queries"></a>ログ アラート クエリの最適化
この記事では、最適なパフォーマンスを実現するために[ログ アラート](./alerts-unified-log.md) クエリを記述して変換する方法について説明します。 最適化されたクエリを使用すると、頻繁に実行されるアラートの待機時間と負荷が軽減されます。

## <a name="how-to-start-writing-an-alert-log-query"></a>アラート ログ クエリの記述を開始する方法

アラート クエリは、問題を示す[ログ分析のログ データを照会](alerts-log.md#create-a-log-alert-rule-with-the-azure-portal)することから開始します。 何が検出できるかを理解するには、[アラートのクエリ例に関するトピック](../logs/example-queries.md)を使用できます。 また、[独自のクエリの記述を記述する](../logs/log-analytics-tutorial.md)ことから開始することもできます。 

### <a name="queries-that-indicate-the-issue-and-not-the-alert"></a>アラートでなく問題を示すクエリ

アラート フローは、問題を示す結果をアラートに変換するために構築されました。 たとえば、次のようなクエリの場合を考えてみましょう。

``` Kusto
SecurityEvent
| where EventID == 4624
```

ユーザーの意図がアラートを発することの場合は、この種類のイベントが発生すると、アラート ロジックによってクエリに `count` が追加されます。 実行されるクエリは次のようになります。

``` Kusto
SecurityEvent
| where EventID == 4624
| count
```

クエリにアラート ロジックを追加する必要はなく、それを行うと問題を引き起こす可能性さえあります。 上の例では、クエリに `count` を含めると、アラート サービスによって `count` の `count` が行われるため、常に値 1 が返されます。

### <a name="avoid-limit-and-take-operators"></a>`limit` 演算子と `take` 演算子の回避

クエリで `limit` と `take` を使用すると、結果が時間の経過とともに一貫しないため、待機時間とアラートの負荷が増加する可能性があります。 必要な場合にのみ使用することをお勧めします。

## <a name="log-query-constraints"></a>ログ クエリの制約
[Azure Monitor のログ クエリ](../logs/log-query-overview.md)は、テーブルか、[`search`](/azure/kusto/query/searchoperator) 演算子または [`union`](/azure/kusto/query/unionoperator) 演算子のいずれかで始まります。

ログ アラート ルールのクエリは、常に明確な範囲を定義するテーブルで始める必要があります。これにより、クエリのパフォーマンスと結果の関連性の両方が向上します。 アラート ルール内のクエリは頻繁に実行されるので、`search` と `union` を使用すると、複数のテーブルにわたるスキャンが必要になるため、アラートの待機時間が増加するオーバーヘッドが過剰に生じる可能性があります。 また、これらの演算子は、アラート サービスがクエリを最適化する機能を低下させます。

リソース間のクエリが想定される `search` 演算子または `union` 演算子を使用するログ アラート ルールの作成または変更は、サポートされていません。

たとえば、次のアラート クエリは _SecurityEvent_ テーブルが対象であり、特定のイベント ID を検索します。 これは、このクエリが処理する必要のある唯一のテーブルです。

``` Kusto
SecurityEvent
| where EventID == 4624
```

リソース間のクエリは `union` 型を使用し、これによりクエリの範囲が特定のリソースに制限されるため、[リソース間のクエリ](../logs/cross-workspace-query.md)を使用するログ アラート ルールはこの変更による影響は受けません。 次の例は、ログ アラート クエリで有効です。

```Kusto
union
app('Contoso-app1').requests,
app('Contoso-app2').requests,
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
> [リソース間のクエリ](../logs/cross-workspace-query.md)は、新しい [scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules) でサポートされています。 まだ[従来の Log Analytics Alert API](./api-alerts.md) を使用してログ アラートを作成する場合は、切り替えについて[ここ](../alerts/alerts-log-api-switch.md)を参照してください。

## <a name="examples"></a>例
次の例には、`search` と `union` を使用するログ クエリが含まれていて、アラート ルールで使用するためにこれらのクエリを変更する際に使用できる手順を示しています。

### <a name="example-1"></a>例 1
`search` を使用してパフォーマンス情報を取得する次のクエリを使用して、ログ アラート ルールを作成するとします。 

``` Kusto
search *
| where Type == 'Perf' and CounterName == '% Free Space'
| where CounterValue < 30
```

このクエリを変更するには、まず次のクエリを使用して、プロパティが属しているテーブルを特定します。

``` Kusto
search *
| where CounterName == '% Free Space'
| summarize by $table
```

このクエリの結果として、_CounterName_ プロパティが _Perf_ テーブルから取得されたことが示されます。

この結果を使用して、アラート ルールに使用する次のクエリを作成することができます。

``` Kusto
Perf
| where CounterName == '% Free Space'
| where CounterValue < 30
```

### <a name="example-2"></a>例 2
`search` を使用してパフォーマンス情報を取得する次のクエリを使用して、ログ アラート ルールを作成するとします。 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer
| where Avg_Memory_Usage between(90 .. 95)  
```

このクエリを変更するには、まず次のクエリを使用して、プロパティが属しているテーブルを特定します。

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use"
| summarize by $table
```

このクエリの結果として、_ObjectName_ プロパティと _CounterName_ プロパティが _Perf_ テーブルから取得されたことが示されます。

この結果を使用して、アラート ルールに使用する次のクエリを作成することができます。

``` Kusto
Perf
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer
| where Avg_Memory_Usage between(90 .. 95)
```

### <a name="example-3"></a>例 3

`search` と `union` の両方を使用してパフォーマンス情報を取得する次のクエリを使用して、ログ アラート ルールを作成するとします。 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")
| where Computer !in (
    union *
    | where CounterName == "% Processor Utility"
    | summarize by Computer)
| summarize Avg_Idle_Time = avg(CounterValue) by Computer
```

このクエリを変更するには、まず次のクエリを使用して、クエリの最初の部分のプロパティが属しているテーブルを特定します。 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")
| summarize by $table
```

このクエリの結果として、これらのすべてのプロパティが _Perf_ テーブルから取得されたことが示されます。

ここで、`withsource` コマンドで `union` を使用して、各行の基になっているソース テーブルを特定します。

``` Kusto
union withsource=table *
| where CounterName == "% Processor Utility"
| summarize by table
```

このクエリの結果として、これらのプロパティも _Perf_ テーブルから取得されたことが示されます。

これらの結果を使用して、アラート ルールに使用する次のクエリを作成することができます。 

``` Kusto
Perf
| where ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total"
| where Computer !in (
    (Perf
    | where CounterName == "% Processor Utility"
    | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer
``` 

### <a name="example-4"></a>例 4
2 つの `search` クエリの結果を結合する次のクエリを使用して、ログ アラート ルールを作成するとします。

```Kusto
search Type == 'SecurityEvent' and EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    search in (Heartbeat) OSType == 'Windows'
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h)
    | project Hour , Computer
) on Hour
```

このクエリを変更するには、まず次のクエリを使用して、結合の左側にあるプロパティが含まれているテーブルを特定します。 

``` Kusto
search Type == 'SecurityEvent' and EventID == '4625'
| summarize by $table
```

結果は、結合の左側にあるプロパティが _SecurityEvent_ テーブルに属していることを示しています。 

ここで、次のクエリを使用して、結合の右側にあるプロパティが含まれているテーブルを特定します。 
 
``` Kusto
search in (Heartbeat) OSType == 'Windows'
| summarize by $table
```
 
結果は、結合の右側にあるプロパティが _Heartbeat_ テーブルに属していることを示しています。

これらの結果を使用して、アラート ルールに使用する次のクエリを作成することができます。 

``` Kusto
SecurityEvent
| where EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    Heartbeat
    | where OSType == 'Windows'
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h)
    | project Hour , Computer
) on Hour
```

## <a name="next-steps"></a>次のステップ
- Azure Monitor での[ログ アラート](alerts-log.md)について学習します。
- [ログ クエリ](../logs/log-query-overview.md)について学習します。