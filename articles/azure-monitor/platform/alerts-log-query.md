---
title: Azure Monitor でのログ アラートのクエリ | Microsoft Docs
description: Azure Monitor の更新プログラムでのログ アラートの効率的なクエリの記述と、既存のクエリを変換するためのプロセスに関する推奨事項を示します。
author: yossi-y
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: bwren
ms.subservice: alerts
ms.openlocfilehash: 429770b7651a93473c03f5e386d8f7b72692c161
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59784383"
---
# <a name="log-alert-queries-in-azure-monitor"></a>Azure Monitor でのログ アラートのクエリ
[Azure Monitor ログに基づくアラート ルール](alerts-unified-log.md)は一定間隔で実行されるため、必ずオーバーヘッドと待機時間を最小限に抑えるように記述する必要があります。 この記事では、ログ アラートの効率的なクエリの記述と、既存のクエリを変換するためのプロセスに関する推奨事項を示します。 

## <a name="types-of-log-queries"></a>ログ クエリの種類
[Azure Monitor のログ クエリ](../log-query/log-query-overview.md)は、テーブルか、[search](/azure/kusto/query/searchoperator) または [union](/azure/kusto/query/unionoperator) 演算子のいずれかで始まります。

たとえば、次のクエリは _SecurityEvent_ テーブルが対象であり、特定のイベント ID を検索します。 これは、このクエリが処理する必要のある唯一のテーブルです。

``` Kusto
SecurityEvent | where EventID == 4624 
```

`search` または `union` で始まるクエリでは、1 つのテーブル内の列間や、複数のテーブル間でも検索を行うことができます。 次の例は、_Memory_ という用語を検索するための複数の方法を示しています。

```Kusto
search "Memory"
search * | where == "Memory"
search ObjectName: "Memory"
search ObjectName == "Memory"
union * | where ObjectName == "Memory"
```

`search` と `union` は、データ モデル全体で用語を検索するデータの探索時には便利ですが、複数のテーブルをスキャンする必要があるため、テーブルを使用するよりも効率が低下します。 アラート ルールのクエリは一定間隔で実行されるため、結果として過剰なオーバーヘッドが生じてアラートに待機時間が追加されます。 このオーバーヘッドのため、Azure のログ アラートのクエリは、常に明確な範囲を定義するテーブルで始める必要があります。これにより、クエリのパフォーマンスと結果の関連性の両方が向上します。

## <a name="unsupported-queries"></a>サポートされていないクエリ
2019 年 1 月 11 日以降、`search` または `union`演算子を使用するログ アラート ルールの作成または変更は Azure portal でサポートされません。 アラート ルールでこれらの演算子を使用すると、エラー メッセージが返されます。 既存のアラート ルールと Log Analytics API を使用して作成および編集したアラート ルールは、この変更による影響は受けません。 効率を向上させるためには、この種のクエリを使用するアラート ルールを変更することを検討してください。  

リソース間のクエリは `union` を使用し、これによりクエリの範囲が特定のリソースに制限されるため、[リソース間のクエリ](../log-query/cross-workspace-query.md)を使用するログ アラート ルールはこの変更による影響は受けません。 これは、使用できない `union *` に相当するものではありません。  次の例は、ログ アラート ルールで有効です。

```Kusto
union 
app('Contoso-app1').requests, 
app('Contoso-app2').requests, 
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
>ログ アラートでの[リソース間のクエリ](../log-query/cross-workspace-query.md)は、新しい [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) でサポートされています。 [従来の Log Alerts API](alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) から切り替えていない場合、Azure Monitor では既定で、[従来の Log Analytics Alert API](api-alerts.md) を使用して Azure portal から新しいログ アラート ルールが作成されます。 切り替えた後は、Azure portal での新しいアラート ルールに対して新しい API が既定になり、リソース間のクエリのログ アラート ルールを作成できます。 切り替えを行わなくても、[scheduledQueryRules API 用の ARM テンプレート](alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template)を使用することで、[リソース間のクエリ](../log-query/cross-workspace-query.md)のログ アラート ルールを作成できます。ただし、このアラート ルールは、[scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) では管理できますが、Azure portal では管理できません。

## <a name="examples"></a>例
次の例には、`search` と `union` を使用するログ クエリが含まれていて、アラート ルールで使用するためにこれらのクエリを変更する際に使用できる手順を示しています。

### <a name="example-1"></a>例 1
`search` を使用してパフォーマンス情報を取得する次のクエリを使用して、ログ アラート ルールを作成するとします。 

``` Kusto
search * | where Type == 'Perf' and CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```
  

このクエリを変更するには、まず次のクエリを使用して、プロパティが属しているテーブルを特定します。

``` Kusto
search * | where CounterName == '% Free Space'
| summarize by $table
```
 

このクエリの結果として、_CounterName_ プロパティが _Perf_ テーブルから取得されたことが示されます。 

この結果を使用して、アラート ルールに使用する次のクエリを作成することができます。

``` Kusto
Perf 
| where CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```


### <a name="example-2"></a>例 2
`search` を使用してパフォーマンス情報を取得する次のクエリを使用して、ログ アラート ルールを作成するとします。 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"  
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
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
| count 
```
 

### <a name="example-3"></a>例 3

`search` と `union` の両方を使用してパフォーマンス情報を取得する次のクエリを使用して、ログ アラート ルールを作成するとします。 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| where Computer !in ((union * | where CounterName == "% Processor Utility" | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer|  count  
```
 

このクエリを変更するには、まず次のクエリを使用して、クエリの最初の部分のプロパティが属しているテーブルを特定します。 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| summarize by $table 
```

このクエリの結果として、これらのすべてのプロパティが _Perf_ テーブルから取得されたことが示されます。 

ここで、`withsource` コマンドで `union` を使用して、各行の基になっているソース テーブルを特定します。

``` Kusto
union withsource=table * | where CounterName == "% Processor Utility" 
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
| count 
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
)  
on Hour 
| count 
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

 
結果は、結合の右側にあるプロパティが Heartbeat テーブルに属していることを示しています。 

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
)  
on Hour 
| count 
```

## <a name="next-steps"></a>次の手順
- Azure Monitor での[ログ アラート](alerts-log.md)について学習します。
- [ログ クエリ](../log-query/log-query-overview.md)について学習します。

