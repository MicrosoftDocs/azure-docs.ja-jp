---
title: Azure Monitor での効率的なログ クエリの記述 | Microsoft Docs
description: Log Analytics でクエリを記述する方法を学習するためのリソースへの参照。
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
ms.date: 01/17/2019
ms.author: bwren
ms.openlocfilehash: 25d6b582ed4d4e24df3841f4191471296e25abd8
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2019
ms.locfileid: "54436334"
---
# <a name="writing-efficient-log-queries-in-azure-monitor"></a>Azure Monitor での効率的なログ クエリの記述
この記事では、Azure Monitor で効率的なログ クエリを記述するための推奨事項を示します。 これらの方式を使用すると、クエリがすばやく、かつ最小限のオーバーヘッドで実行されるように保証できます。

## <a name="scope-your-query"></a>クエリの範囲を指定する
実際に必要な量を超えるデータをクエリで処理するようにすると、実行時間の長いクエリになることがあり、多くの場合は、効率的に分析するには多すぎるデータが結果に含まれます。 極端な場合は、クエリがタイムアウトになり、失敗することさえあります。

### <a name="specify-your-data-source"></a>データ ソースを指定する
効率的なクエリを記述するための最初の手順は、その範囲を必要なデータ ソースに制限することです。 幅広いテキスト検索 (`search *` など) を実行することより、テーブルを指定することが常に推奨されます。 特定のテーブルにクエリを実行するには、次に示すように、テーブル名を使用してクエリを起動します。

``` Kusto
requests | ...
```

次のようなクエリを使用して、特定のテーブル内の複数の列にまたがる値を検索するには、[search](/azure/kusto/query/searchoperator) を使用できます。

``` Kusto
search in (exceptions) "The server was not found"

search in (exceptions, customEvents) "timeout"
```

次のように複数のテーブルにクエリを実行するには、[union](/azure/kusto/query/unionoperator) を使用します。

``` Kusto
union requests, traces | ...
```

### <a name="specify-a-time-range"></a>時間の範囲を指定する
また、クエリをデータの必要な時間範囲に制限することをお勧めします。 既定では、クエリには過去 24 時間以内に収集されたデータが含まれます。 そのオプションを[時間範囲セレクター](get-started-portal.md#select-a-time-range)で変更するか、または明示的にクエリに追加できます。 時間フィルターは、クエリの残りでその範囲内のデータのみが処理されるように、テーブル名の直後に追加することが最善です。

``` Kusto
requests | where timestamp > ago(1h)

requests | where timestamp between (ago(1h) .. ago(30m))
```
   
### <a name="get-only-the-latest-records"></a>最新のレコードのみを取得する

最新のレコードのみを返すには、次のクエリに示すように、*traces* テーブルに記録された最新の 10 レコードを返す *top* 演算子を使用します。

``` Kusto
traces | top 10 by timestamp
```

   
### <a name="filter-records"></a>レコードをフィルター処理する
特定の条件に一致するログのみを確認するには、次のクエリに示すように、_severityLevel_ 値が 0 より大きいレコードのみを返す *where* 演算子を使用します。

``` Kusto
traces | where severityLevel > 0
```



## <a name="string-comparisons"></a>文字列の比較
[文字列を評価する](/azure/kusto/query/datatypes-string-operators)場合は通常、完全なトークンの検索時には `contains` の代わりに `has` を使用してください。 `has` は、部分文字列を検索する必要がないため、より効率的です。

## <a name="returned-columns"></a>返される列

処理される列のセットを必要な列のみに絞り込むには、[project](/azure/kusto/query/projectoperator) を使用します。

``` Kusto
traces 
| project timestamp, severityLevel, client_City 
| ...
```

[extend](/azure/kusto/query/extendoperator) を使用して値を計算し、独自の列を作成することは可能ですが、通常、テーブル列に対してフィルター処理する方がより効率的です。

たとえば、_operation\_Name_ に対してフィルター処理する下の最初のクエリは、新しい _subscription_ 列を作成し、それに対してフィルター処理する 2 番目のクエリより効率的です。

``` Kusto
customEvents 
| where split(operation_Name, "/")[2] == "acb"

customEvents 
| extend subscription = split(operation_Name, "/")[2] 
| where subscription == "acb"
```

## <a name="using-joins"></a>結合の使用
[join](/azure/kusto/query/joinoperator) 演算子を使用する場合は、行数の少ない方のテーブルをクエリの左側として選択します。


## <a name="next-steps"></a>次の手順
クエリのベスト プラクティスの詳細については、「[クエリのベスト プラクティス](/azure/kusto/query/best-practices)」を参照してください。