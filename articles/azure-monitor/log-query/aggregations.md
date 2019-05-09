---
title: Azure Monitor ログ クエリの集計 | Microsoft Docs
description: データ分析のための便利な手段となる Azure Monitor ログ クエリの集計関数について説明します。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.openlocfilehash: 864e9586082ed95bf17135414ec4b879e3034ace
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2019
ms.locfileid: "56267344"
---
# <a name="aggregations-in-azure-monitor-log-queries"></a>Azure Monitor ログ クエリの集計

> [!NOTE]
> このレッスンを完了する前に、[Analytics ポータルの概要](get-started-portal.md)および[クエリの概要](get-started-queries.md)に関するチュートリアルを完了する必要があります。

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

この記事では、データ分析のための便利な手段となる Azure Monitor ログ クエリの集計関数について説明します。 これらの関数はすべて、入力テーブルの集計結果が含まれるテーブルを生成する `summarize` 演算子で機能します。

## <a name="counts"></a>カウント

### <a name="count"></a>count
任意のフィルターが適用された結果セット内の行数をカウントします。 次の例では、過去 30 分間にわたる _Perf_ テーブル内の行数の合計が返されます。 結果は、*count_* という名前の列で返されます (特定の名前を列に割り当てた場合を除く)。


```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize count()
```

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize num_of_records=count() 
```

時間グラフの視覚化は、時間の経過に沿った傾向を確認するうえで役立ちます。

```Kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

この例の出力には、perf レコード数のトレンドラインが 5 分間隔で示されています。

![カウントの傾向](media/aggregations/count-trend.png)


### <a name="dcount-dcountif"></a>dcount、dcountif
`dcount` と `dcountif` を使用して、特定の列の個別の値をカウントします。 次のクエリでは、過去 1 時間にハートビートを送信した個別のコンピューターの数が評価されます。

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

ハートビートを送信した Linux コンピューターだけをカウントするには、`dcountif` を使用します。

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluating-subgroups"></a>サブグループの評価
ご自身のデータのサブグループでカウントまたはその他の集計を実行するには、`by` キーワードを使用します。 たとえば、ハートビートを送信した個別の Linux コンピューターの数を国ごとにカウントするには、次を使用します。

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry
```

|RemoteIPCountry  | distinct_computers  |
------------------|---------------------|
|米国    | 19                  |
|カナダ           | 3                   |
|アイルランド          | 0                   |
|イギリス   | 0                   |
|オランダ      | 2                   |


ご自身のデータのさらに小さなサブグループを分析するには、追加の列名を `by` セクションに追加します。 たとえば、OSType あたりの個別のコンピューターの数を国ごとにカウントできます。

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```

## <a name="percentiles-and-variance"></a>パーセンタイルと分散
数値を評価する場合、一般的には、`summarize avg(expression)` を使用してその数値を平均します。 平均は、ほんのわずかしか存在しないケースの特性を示す、極端な値の影響を受けます。 この問題に対処するには、`median` や `variance` など、影響を受けにくい関数を使用します。

### <a name="percentile"></a>パーセンタイル
中央値を見つけるには、`percentile` 関数と、パーセンタイルを指定する値を使用します。

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

さまざまなパーセンタイルを指定して、それぞれの集計結果を取得することもできます。

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

これは、複数のコンピューター CPU の中央値が似かよっていても、実際は CPU が中央値付近で安定しているコンピューターもあれば、極端に低い CPU または高い CPU 値が報告される、つまりスパイクが発生しているコンピューターもあります。

### <a name="variance"></a>variance
値の分散を直接評価するには、標準偏差法と分散法を使用します。

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

CPU 使用率の安定性を分析する場合は、stdev と中央値の計算を組み合わせることをお勧めします。

```Kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

Azure Monitor ログ データと共に [Kusto クエリ言語](/azure/kusto/query/)を使用することに関するその他のレッスンを参照してください。

- [文字列操作](string-operations.md)
- [日付と時刻の操作](datetime-operations.md)
- [高度な集計](advanced-aggregations.md)
- [JSON とデータ構造](json-data-structures.md)
- [詳細クエリ書き込み](advanced-query-writing.md)
- [結合](joins.md)
- [グラフ](charts.md)
