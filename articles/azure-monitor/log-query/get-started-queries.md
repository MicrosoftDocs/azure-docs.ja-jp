---
title: Azure Monitor でログ クエリの使用を開始する | Microsoft Docs
description: この記事では、Azure Monitor でログ クエリの記述を開始するためのチュートリアルを提供します。
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
ms.date: 05/09/2019
ms.author: bwren
ms.openlocfilehash: b03109ee5cdb76247bf3be6fda97e0cf6e434f17
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296088"
---
# <a name="get-started-with-log-queries-in-azure-monitor"></a>Azure Monitor でログ クエリの使用を開始する


> [!NOTE]
> このチュートリアルを完了する前に、「[Azure Monitor Log Analytics の使用を開始する](get-started-portal.md)」を終えておく必要があります。

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

このチュートリアルでは、Azure Monitor でログ クエリを記述する方法について説明します。 以下の方法について説明します。

- クエリの構造の概要
- クエリ結果を並べ替える
- クエリ結果をフィルター処理する
- 時間の範囲を指定する
- 結果に含めるフィールドを選択する
- カスタム フィールドの定義と使用
- 結果の集計とグループ化

Azure portal での Log Analytics の使用に関するチュートリアルについては、[Azure Monitor Log Analytics の使用開始](get-started-portal.md)に関するページを参照してください。<br>
Azure Monitor でのログ クエリの詳細については、[Azure Monitor でのログ クエリの概要](log-query-overview.md)に関するページを参照してください。

## <a name="writing-a-new-query"></a>新しいクエリの記述
クエリは、テーブル名または *search* コマンドから始めることができます。 まずテーブル名から始めます。これは、テーブル名でクエリの明確な範囲が定義され、クエリのパフォーマンスと結果の関連性の両方が向上するためです。

> [!NOTE]
> Azure Monitor によって使用される Kusto クエリ言語では、大文字と小文字が区別されます。 通常、言語のキーワードは小文字で記述されます。 クエリ内でテーブルまたは列の名前を使用する場合は、スキーマ ウィンドウの表示に従って大文字と小文字を正しく指定します。

### <a name="table-based-queries"></a>テーブルベースのクエリ
Azure Monitor では、ログ データはテーブルに編成され、各テーブルは複数の列で構成されます。 Analytics ポータルでは、すべてのテーブルと列は Log Analytics のスキーマ ウィンドウに表示されます。 目的のテーブルを指定子、データの一部を見てみます。

```Kusto
SecurityEvent
| take 10
```

上記のクエリは、順不同で *SecurityEvent* から 10 個の結果を返します。 これは、テーブルの概要を把握し、その構造と内容を理解する場合に非常に一般的な方法です。 それでは、どのように構築されているかを見てみましょう。

* クエリはテーブル名 *SecurityEvent* で始まります。この部分で、クエリの範囲を定義します。
* 複数のコマンドは、パイプ (|) 文字で区切ります。そのため、最初のコマンドの出力は次のコマンドの入力になります。 パイプでつないだ要素は、任意の数を追加できます。
* パイプの後には **take** コマンドがあります。このコマンドは、テーブルから特定の数の任意のレコードを返します。

実際は `| take 10` を追加しなくてもクエリを実行できます。この方法は有効ですが、最大 10,000 件の結果が返される可能性があります。

### <a name="search-queries"></a>検索クエリ
検索クエリはあまり構造化されていないため、一般的に、任意の列に特定の値が含まれるレコードを検索する場合に適しています。

```Kusto
search in (SecurityEvent) "Cryptographic"
| take 10
```

このクエリで、*SecurityEvent* テーブルの "Cryptographic" というフレーズが含まれるレコードが検索されます。 このようなレコードの中から、10 個のレコードが返され、表示されます。 `in (SecurityEvent)` の部分を省略して `search "Cryptographic"` を実行すると、*すべての*テーブルに対して検索が実行されます。この処理には時間がかかり、効率が良くありません。

> [!WARNING]
> 検索クエリは通常、処理する必要のあるデータが増えるため、テーブル ベースのクエリより低速です。 

## <a name="sort-and-top"></a>sort と top
**take** は少数のレコードを取得する場合に便利ですが、結果は順不同で選択され、表示されます。 順序が設定されたビューを取得するには、優先する列で**並べ替え**ます。

```Kusto
SecurityEvent   
| sort by TimeGenerated desc
```

ただし、返される結果が多すぎる場合や、処理に時間もかかる場合があります。 上記のクエリで、SecurityEvent テーブル*全体*が TimeGenerated 列で並べ替えられます。 Analytics ポータルでは、表示できるレコードの上限は 10,000 個に制限されています。 Analytics ポータルでは、表示が 10,000 レコードに制限されます。

最新の 10 レコードのみを取得するには、**top** を使用する方法が最適です。サーバー側でテーブル全体が並べ替えられてから、上位のレコードが返されます。

```Kusto
SecurityEvent
| top 10 by TimeGenerated
```

降順が既定の並べ替え順です。そのため、通常は **desc** の引数を省略します。出力は次のようになります。

![top 10](media/get-started-queries/top10.png)


## <a name="where-filtering-on-a-condition"></a>where: 条件に基づいてフィルター処理する
フィルターは、その名前のとおり、特定の条件に基づいてデータをフィルター処理します。 これは、クエリ結果を関連する情報に制限する最も一般的な方法です。

クエリにフィルターを追加するには、**where** 演算子に続けて 1 つまたは複数の条件を使用します。 たとえば、次のクエリは、_Level_ が _8_ に等しい *SecurityEvent* レコードのみを返します。

```Kusto
SecurityEvent
| where Level == 8
```

フィルター条件を記述する場合は、次の式を使用できます。

| 式 | 説明 | 例 |
|:---|:---|:---|
| == | 等価性をチェックします<br>(大文字と小文字を区別します) | `Level == 8` |
| =~ | 等価性をチェックします<br>(大文字と小文字は区別されません) | `EventSourceName =~ "microsoft-windows-security-auditing"` |
| !=、<> | 非等価性をチェックします<br>(両方の式は同じです) | `Level != 4` |
| *and*、*or* | 条件の間に必要です| `Level == 16 or CommandLine != ""` |

複数の条件でフィルター処理するには、**and** を使用します。

```Kusto
SecurityEvent
| where Level == 8 and EventID == 4672
```

または、複数の **where** 要素をパイプでつなぎます。

```Kusto
SecurityEvent
| where Level == 8 
| where EventID == 4672
```
    
> [!NOTE]
> 値の型は異なる可能性があるため、正しい型に基づいて比較を実行するために型をキャストする必要があります。 たとえば、SecurityEvent *Level* 列は String 型なので、数値演算子を使用する前に、*int* または *long* などの数値型にキャストする必要があります。`SecurityEvent | where toint(Level) >= 10`

## <a name="specify-a-time-range"></a>時間の範囲を指定する

### <a name="time-picker"></a>時刻の選択ツール
時刻の選択ツールは [実行] ボタンの横にあります。これは、過去 24 時間のレコードのみのクエリを実行していることを示しています。 これは、すべてのクエリに適用される既定の時間範囲です。 過去 1 時間のレコードのみを取得するには、 _[過去 1 時間]_ を選択してクエリを再度実行します。

![時刻の選択](media/get-started-queries/timepicker.png)


### <a name="time-filter-in-query"></a>クエリの時間フィルター
また、時間フィルターをクエリに追加して、独自の時間の範囲を定義することもできます。 テーブル名の直後に時間フィルターを配置することをお勧めします。 

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| where toint(Level) >= 10
```

上記の時間フィルター `ago(30m)` は "30 分前" を意味するので、このクエリで過去 30 分間のレコードのみが返されます。 他の時間単位には、日 (2d)、分 (25m)、秒 (10s) があります。


## <a name="project-and-extend-select-and-compute-columns"></a>プロジェクトと拡張: 列の選択と計算
結果に含める特定の列を選択するには、**project** を使用します。

```Kusto
SecurityEvent 
| top 10 by TimeGenerated 
| project TimeGenerated, Computer, Activity
```

前述の例では、次の出力が生成されます。

![プロジェクトの結果をクエリする](media/get-started-queries/project.png)

また、**project** を使用して列の名前を変更し、新しい列を定義することもできます。 次の例では、project を使用して次の処理を行います。

* 元の列の *Computer* および *TimeGenerated* のみを選択します。
* *Activity* 列の名前を *EventDetails* に変更します。
* *EventCode* という名前の新しい列を作成します。 **substring ()** 関数は、Activity フィールドの先頭の 4 文字のみを取得するために使用されます。


```Kusto
SecurityEvent
| top 10 by TimeGenerated 
| project Computer, TimeGenerated, EventDetails=Activity, EventCode=substring(Activity, 0, 4)
```

**extend** は、元の列をすべて結果セットに保存し、追加の列を定義します。 次のクエリでは、**extend** を使用して、*EventCode* 列が追加されています。 この列は、テーブル結果の最後に表示されない場合があることに注意してください。その場合、この列を表示するには、レコードの詳細を展開する必要があります。

```Kusto
SecurityEvent
| top 10 by TimeGenerated
| extend EventCode=substring(Activity, 0, 4)
```

## <a name="summarize-aggregate-groups-of-rows"></a>summarize: 行のグループを集計する
**summarize** を使用して、1 つまたは複数の列に従ってレコードのグループを特定し、それらのグループに集計を適用します。 **summarize** の最も一般的な用途は、各グループの結果の数を返す *count* です。

次のクエリでは、過去 1 時間のすべての *Perf* レコードを確認し、*ObjectName* でグループ化し、各グループのレコードをカウントします。 
```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName
```

複数の次元でグループを定義することが理にかなっている場合があります。 これらの値の一意の組み合わせで、それぞれ別のグループが定義されます。

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName, CounterName
```

もう 1 つの一般的な用途は、各グループに対して数学的または統計的計算を実行する場合です。 以下の例では、各コンピューターの平均  *CounterValue* を計算します。

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer
```

残念ながら、異なるパフォーマンス カウンターが混在するので、このクエリの結果は意味がありません。 より意味のある結果にするには、*CounterName* と *Computer* の組み合わせごとに別に平均を計算する必要があります。

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer, CounterName
```

### <a name="summarize-by-a-time-column"></a>時間列で要約する
時間列または別の連続する値に基づいて結果をグループ化することもできます。 ただし、これらは一意の値なので、`by TimeGenerated` を要約するだけでは、時間の範囲全体に 1 ミリ秒ごとのグループが作成されます。 

連続する値に基づいてグループを作成するには、**bin** を使用して範囲を管理しやすい単位に分割することをお勧めします。 次のクエリでは、特定のコンピューター上の空きメモリ ( *[利用可能な MB]* ) を測定する *Perf* レコードを分析します。 過去 7 日間の 1 時間ごとの平均値が計算されます。

```Kusto
Perf 
| where TimeGenerated > ago(7d)
| where Computer == "ContosoAzADDS2" 
| where CounterName == "Available MBytes" 
| summarize avg(CounterValue) by bin(TimeGenerated, 1h)
```

出力をよりわかりやすくするには、時系列で空きメモリを示す時間グラフとして表示するように選択します。

![時系列でのメモリのクエリ](media/get-started-queries/chart.png)



## <a name="next-steps"></a>次の手順

- [検索クエリの記述方法](search-queries.md)を参照してください。
