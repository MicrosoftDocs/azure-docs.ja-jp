---
title: Azure Monitor ログ クエリの高度な集計 | Microsoft Docs
description: Azure Monitor ログ クエリで使用できる、より高度な集計オプションの一部について説明します。
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
ms.openlocfilehash: 37eb8ca3c25268dd7923087439a8fbf0fd1f168b
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2019
ms.locfileid: "56269911"
---
# <a name="advanced-aggregations-in-azure-monitor-log-queries"></a>Azure Monitor ログ クエリの高度な集計

> [!NOTE]
> このレッスンを完了する前に、[Azure Monitor クエリでの集計](./aggregations.md)に関するレッスンを完了しておく必要があります。

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

この記事では、Azure Monitor クエリで使用できる、より高度な集計オプションの一部について説明します。

## <a name="generating-lists-and-sets"></a>リストとセットの生成
`makelist` を使用して、特定の列にある値の順序でデータをピボットできます。 たとえば、マシンで行われる最も一般的な注文イベントを調査できます。 基本的に言って、各マシンの EventID の順番でデータをピボットできます。 

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```
|Computer|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085,704,704,701] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

`makelist` は、データが渡された順序でリストを生成します。 イベントを一番前のものから最新のものに並べ替えるには、order ステートメントで `desc` の代わりに `asc` を使用します。 

また、個別の値だけの一覧を作成するのも便利です。 これは _セット_ と呼ばれ、次のように `makeset` で生成できます。

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```
|Computer|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

`makelist` と同様に、`makeset` も順序付けされたデータを処理し、渡される行の順序に基づいて配列を生成します。

## <a name="expanding-lists"></a>リストの展開
`makelist` または `makeset` の逆の操作は `mvexpand` です。これは、値のリストを別々の行に展開します。 JSON と配列の両方で、任意の数の動的な列に展開できます。 たとえば、過去 1 時間にハートビートを送信したコンピューターからのデータを送信するソリューションの *Heartbeat*ソリューション テーブルを確認できます。

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```

| Computer | 解決方法 | 
|--------------|----------------------|
| computer1 | "security"、"updates"、"changeTracking" |
| computer2 | "security"、"updates" |
| computer3 | "antiMalware"、"changeTracking" |
| ... | ... | ... |

`mvexpand` を使用すると、コンマ区切りリストではなく、次のようにそれぞれの値が個別の行に表示されます。

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
```

| Computer | 解決方法 | 
|--------------|----------------------|
| computer1 | "security" |
| computer1 | "updates" |
| computer1 | "changeTracking" |
| computer2 | "security" |
| computer2 | "updates" |
| computer3 | "antiMalware" |
| computer3 | "changeTracking" |
| ... | ... | ... |


次に、もう一度 `makelist` を使用して、項目をまとめてグループ化すると、今度はソリューションごとにコンピューターのリストを表示できます。

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```
|解決方法 | list_Computer |
|--------------|----------------------|
| "security" | ["computer1", "computer2"] |
| "updates" | ["computer1", "computer2"] |
| "changeTracking" | ["computer1", "computer3"] |
| "antiMalware" | ["computer3"] |
| ... | ... |

## <a name="handling-missing-bins"></a>不足しているビンの処理
`mvexpand` の便利な適用方法の 1 つは、不足しているビンの既定値を埋める必要がある場合です。たとえば、ハートビートを調べて、特定のマシンのアップタイムを確認しているとします。 また、_Category_ 列に示されているハートビートのソースを確認する必要もあるとします。 通常は、次のように単純な summarize ステートメントを使用することでしょう。

```Kusto
Heartbeat
| where TimeGenerated > ago(12h)
| summarize count() by Category, bin(TimeGenerated, 1h)
```
| Category | TimeGenerated | count_ |
|--------------|----------------------|--------|
| 直接エージェント | 2017-06-06T17:00:00Z | 15 |
| 直接エージェント | 2017-06-06T18:00:00Z | 60 |
| 直接エージェント | 2017-06-06T20:00:00Z | 55 |
| 直接エージェント | 2017-06-06T21:00:00Z | 57 |
| 直接エージェント | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |

これらの結果には、"2017-06-06T19:00:00Z" に関連付けられたバケットはありません。これは、その時間のハートビート データがないためです。 `make-series` 関数を使用して、空のバケットに既定値を割り当てます。 これにより、カテゴリごとに 1 行が生成され、各行には追加の配列の列が 2 つ設けられます。1 つは値、1 つは合致する時間バケットです。

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

| Category | count_ | TimeGenerated |
|---|---|---|
| 直接エージェント | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.0000000Z","2017-06-06T18:00:00.0000000Z","2017-06-06T19:00:00.0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:00.0000000Z",...] |
| ... | ... | ... |

*count_* 配列の 3 番目の要素は予想どおり 0 であり、一致するタイムスタンプ "2017-06-06T19：00：00.0000000Z" が _TimeGenerated_  配列にあります。 しかし、この配列形式は読み取りが困難です。 `mvexpand` を使用して配列を展開し、`summarize` で生成されたものと同じ形式を出力します。

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
| mvexpand TimeGenerated, count_
| project Category, TimeGenerated, count_
```
| Category | TimeGenerated | count_ |
|--------------|----------------------|--------|
| 直接エージェント | 2017-06-06T17:00:00Z | 15 |
| 直接エージェント | 2017-06-06T18:00:00Z | 60 |
| 直接エージェント | 2017-06-06T19:00:00Z | 0 |
| 直接エージェント | 2017-06-06T20:00:00Z | 55 |
| 直接エージェント | 2017-06-06T21:00:00Z | 57 |
| 直接エージェント | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |



## <a name="narrowing-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>結果を要素セットに絞り込む: `let`、`makeset`、`toscalar`、`in`
一般的なシナリオでは、基準のセットに基づいて特定のエンティティの名前を選択し、異なるデータ セットをフィルターして、そのエンティティのセットに絞り込みます。 たとえば、不足している更新プログラムがあることが認識されているコンピューターを見つけ、これらのコンピューターが呼び出した IP を特定することができます。


```Kusto
let ComputersNeedingUpdate = toscalar(
    Update
    | summarize makeset(Computer)
    | project set_Computer
);
WindowsFirewall
| where Computer in (ComputersNeedingUpdate)
```

## <a name="next-steps"></a>次の手順

Azure Monitor ログ データと共に [Kusto クエリ言語](/azure/kusto/query/)を使用することに関するその他のレッスンを参照してください。

- [文字列操作](string-operations.md)
- [日付と時刻の操作](datetime-operations.md)
- [集計関数](aggregations.md)
- [高度な集計](advanced-aggregations.md)
- [JSON とデータ構造](json-data-structures.md)
- [結合](joins.md)
- [グラフ](charts.md)