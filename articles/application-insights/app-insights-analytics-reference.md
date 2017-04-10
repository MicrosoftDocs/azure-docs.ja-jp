---
title: "Azure Application Insights の Analytics のリファレンス | Microsoft Docs"
description: "Application Insights の強力な検索ツールである Analytics の正規表現のリファレンス。 "
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: eea324de-d5e5-4064-9933-beb3a97b350b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/09/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: b850264ef2b89ad1679ae1e956a58cc849e63c84
ms.lasthandoff: 03/25/2017


---
# <a name="reference-for-analytics"></a>Analytics のリファレンス
[Analytics](app-insights-analytics.md) は、[Application Insights](app-insights-overview.md) の強力な検索機能です。 ここでは、Analytics のクエリ言語について説明します。

追加の情報源:

* 参考資料の多くを、Analytics で入力しながら使用できます。 クエリを入力し始めると、そのクエリの入力候補が表示されます。
* [チュートリアル ページ](app-insights-analytics-tour.md)では、言語機能の概要が順を追って説明されています。
* [SQL ユーザーのチート シート](https://aka.ms/sql-analytics)では、最も一般的な言語の対応付けを確認できます。
* [シミュレーション データで Analytics を試す](https://analytics.applicationinsights.io/demo) (ご使用のアプリからまだ Application Insights にデータが送信されていない場合)。
 

## <a name="index"></a>Index
**Let** [let](#let-clause) | [materialize](#materialize) 

**クエリと演算子** [as](#as-operator) | [count](#count-operator) | [datatable](#datatable-operator) | [distinct](#distinct-operator) | [evaluate](#evaluate-operator) | [extend](#extend-operator) | [find](#find-operator) | [getschema](#getschema-operator) | [join](#join-operator) | [limit](#limit-operator) | [make-series](#make-series-operator) | [mvexpand](#mvexpand-operator) | [parse](#parse-operator) | [project](#project-operator) | [project-away](#project-away-operator) | [range](#range-operator) | [reduce](#reduce-operator) | [render ディレクティブ](#render-directive) | [restrict clause](#restrict-clause) | [sample](#sample-operator) | [sample-distinct](#sample-distinct-operator) | [sort](#sort-operator) | [summarize](#summarize-operator) | [table](#table-operator) | [take](#take-operator) | [top](#top-operator) | [top-nested](#top-nested-operator) | [union](#union-operator) | [where](#where-operator) 

**集計** [any](#any) | [argmax](#argmax) | [argmin](#argmin) | [avg](#avg) | [buildschema](#buildschema) | [count](#count) | [countif](#countif) | [dcount](#dcount) | [dcountif](#dcountif) | [makelist](#makelist) | [makeset](#makeset) | [max](#max) | [min](#min) | [percentile](#percentile) | [percentiles](#percentiles) | [percentilesw](#percentilesw) | [percentilew](#percentilew) | [stdev](#stdev) | [sum](#sum) | [variance](#variance)

**スカラー** [ブール型リテラル](#boolean-literals) | [ブール演算子](#boolean-operators) | [キャスト](#casts) | [スカラーの比較](#scalar-comparisons) | [gettype](#gettype) | [hash](#hash) | [iff](#iff) | [isnotnull](#isnotnull) | [isnull](#isnull) | [notnull](#notnull) | [toscalar](#toscalar)

**数値** [算術演算子](#arithmetic-operators) | [数値リテラル](#numeric-literals) | [abs](#abs) | [bin](#bin) | [exp](#exp) | [floor](#floor) | [gamma](#gamma) | [log](#log) | [rand](#rand) | [sqrt](#sqrt) | [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

**日付と時刻** [日付と時刻の式](#date-and-time-expressions) | [日付と時刻のリテラル](#date-and-time-literals) | [ago](#ago) | [datepart](#datepart) | [dayofmonth](#dayofmonth) | [dayofweek](#dayofweek) | [dayofyear](#dayofyear) | [endofday](#endofday) | [endofmonth](#endofmonth) | [endofweek](#endofweek) | [endofyear](#endofyear) | [getmonth](#getmonth) | [getyear](#getyear) | [now](#now) | [startofday](#startofday) | [startofmonth](#startofmonth) | [startofweek](#startofweek) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan) | [weekofyear](#weekofyear)

**文字列** [GUIDs](#guids) | [難読化された文字列リテラル](#obfuscated-string-literals) | [文字列リテラル](#string-literals) | [文字列の比較](#string-comparisons) | [countof](#countof) | [extract](#extract) | [in, !in](#in) | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty)| [parseurl](#parseurl) | [replace](#replace) | [split](#split) | [strcat](#strcat) | [strlen](#strlen) | [substring](#substring) | [tolower](#tolower) | [toupper](#toupper)

**配列、オブジェクト、動的** [配列とオブジェクトのリテラル](#array-and-object-literals) | [動的オブジェクトの関数](#dynamic-object-functions) | [let 句の動的オブジェクト](#dynamic-objects-in-let-clauses) | [JSON パス式](#json-path-expressions) | [名前](#names) | [arraylength](#arraylength) | [extractjson](#extractjson) | [in、!in](#in) | [parsejson](#parsejson) | [range](#range) | [todynamic](#todynamic) | [treepath](#treepath)

## <a name="let"></a>let
### <a name="let-clause"></a>let 句
**表形式の let - テーブルに名前を付ける**

    let recentReqs = requests | where timestamp > ago(3d); 
    recentReqs | count

**スカラーの let - 値に名前を付ける**

    let interval = 3d; 
    requests | where timestamp > ago(interval)

**ラムダの let - 関数に名前を付ける**

    let Recent = 
       (interval:timespan) { requests | where timestamp > ago(interval) };
    Recent(3h) | count

    let us_date = (t:datetime)
    {
      strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ", 
      bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
    };
    requests 
    | summarize count() by bin(timestamp, 1h) 
    | project count_, pacificTime=us_date(timestamp-8h)

let 句は、[名前](#names)を表形式の結果、スカラー値、または関数にバインドします。 この句はクエリのプレフィックスであり、バインドのスコープはそのクエリです (let では、セッションの後の方で使用するものに名前を付けることはできません)。

**構文**

    let name = scalar_constant_expression ; query

    let name = query ; query

    let name = (parameterName : type [, ...]) { plain_query }; query

    let name = (parameterName : type [, ...]) { scalar_expression }; query

* *type:* `bool`、`int`、`long`、`double`、`string`、`timespan`、`datetime`、`guid`、[`dynamic`](#dynamic-type)
* *plain_query:* 先頭に let 句が置かれていないクエリ。

**例**

    let rows = (n:long) { range steps from 1 to n step 1 };
    rows(10) | ...

テーブルの結果をスカラーに変換してクエリで使用:

```
let topCities =  toscalar ( // convert single column to value
   requests
   | summarize count() by client_City 
   | top 4 by count_ 
   | summarize makeset(client_City)) ;
requests
| where client_City in (topCities) 
| summarize count() by client_City;
```

### <a name="materialize"></a>materialize

let 句の結果がダウンストリームで 2 回以上使用される場合のパフォーマンスを改善するには、materialize() を使用します。 materialize() は、クエリの実行時に表形式の let 句の結果をキャッシュして、同じクエリが 2 回以上実行されないようにします。

**構文**

    materialize(expression)

**引数**

* `expresion`: クエリの実行中に評価およびキャッシュされる表形式の式。

**ヒント**

* materialize は、join/union のオペランドに 1 回実行できる共通のサブクエリがある場合に使用します (下の例を参照)。
* また、join/union の分岐が必要な場合にも役立ちます。
* materialize を使用できるのは、let ステートメントでキャッシュ結果に名前を付けている場合のみです。
* materialize には、5 GB のキャッシュ サイズの制限があります。 この制限はクラスター ノードごとに適用され、すべてのクエリに共通です。

**例: self-join**


```AIQL
let totalPagesPerDay = pageViews
| summarize by name, Day = startofday(timestamp)
| summarize count() by Day;
let materializedScope = pageViews
| summarize by name, Day = startofday(timestamp);
let cachedResult = materialize(materializedScope);
cachedResult
| project name, Day1 = Day
| join kind = inner
(
    cachedResult
    | project name, Day2 = Day
)
on name
| where Day2 > Day1
| summarize count() by Day1, Day2
| join kind = inner
    totalPagesPerDay
on $left.Day1 == $right.Day
| project Day1, Day2, Percentage = count_*100.0/count_1
```

キャッシュされていないバージョンでは、結果 `scope` が 2 回使用されます。

```AIQL
let totalPagesPerDay = pageViews
| summarize by name, Day = startofday(timestamp)
| summarize count() by Day;
let scope = pageViews
| summarize by name, Day = startofday(timestamp);
scope      // First use of this table.
| project name, Day1 = Day
| join kind = inner
(
    scope  // Second use can cause evaluation twice.
    | project name, Day2 = Day
)
on name
| where Day2 > Day1
| summarize count() by Day1, Day2
| join kind = inner
    totalPagesPerDay
on $left.Day1 == $right.Day
| project Day1, Day2, Percentage = count_*100.0/count_1
```

## <a name="queries-and-operators"></a>クエリと演算子
テレメトリに対するクエリは、ソース ストリームの参照と、それに続くフィルターのパイプラインで構成されます。 次に例を示します。

```AIQL
requests // The request table starts this pipeline.
| where client_City == "London" // filter the records
   and timestamp > ago(3d)
| count 
```

パイプ文字`|`が先頭に配置された各フィルターは、いくつかのパラメーターが設定される*演算子*のインスタンスです。 この演算子への入力は、前のパイプラインの結果であるテーブルです。 ほとんどの場合、パラメーターは入力の列に対する[スカラー式](#scalars)ですが、 まれに、入力列の名前である場合や、2 つ目のテーブルである場合もあります。 列と行が 1 つずつしかなくても、クエリの結果は常にテーブルです。

クエリは、単一の改行を含めることができますが、空白行で終了します。 クエリでは、 `//` と行末の間に注釈を含めることができます。

クエリの前には、クエリ内で使用できるスカラー、テーブル、関数を定義する [let 句](#let-clause)を 1 つまたは複数配置することができます。

```AIQL

    let interval = 3d ;
    let city = "London" ;
    let req = (city:string) {
      requests
      | where client_City == city and timestamp > ago(interval) };
    req(city) | count
```

> `T` が使用されています。
> 
> 

### <a name="as-operator"></a>as 演算子

表形式の入力式に名前を一時的にバインドします。

**構文**

    T | as name

**引数**

* *name:* テーブルの一時的な名前

**メモ**

* この名前を後から部分式で使用する場合は、*as* の代わりに [let](#let-clause) を使用します。
* [union](#union-operator)、[find](#find-operator)、または [search](#search-operator) の結果と同じになるようにテーブルの名前を指定するには、*as* を使用します。

**例**

```AIQL
range x from 1 to 10 step 1 | as T1
| union withsource=TableName (requests | take 10 | as T2)
```

### <a name="count-operator"></a>count 演算子
`count` 演算子は、入力レコード セットのレコード (行) の数を返します。

**構文**

    T | count

**引数**

* *T*: カウントするレコードが含まれる表形式のデータ。

**戻り値**

この関数は、1 つのレコードと `long`型の列を含むテーブルを返します。 唯一のセルの値は、 *T*内のレコードの数です。 

**例**

```AIQL
requests | count
```

### <a name="datatable-operator"></a>datatable 演算子

テーブルをインライン形式で指定します。 スキーマと値は、クエリ自体で定義します。

この演算子にパイプラインの入力はないことに注意してください。

**構文**

    datatable ( ColumnName1 : ColumnType1 , ...) [ScalarValue1, ...]

* *ColumnName* 列の名前。
* *ColumnType* [データ型](#scalars)。 
* *ScalarValue* 適切な型の値。 値の数は、列の数の倍数である必要があります。 

**戻り値**

指定された値が含まれるテーブル。

**例**

```AIQL
datatable (Date:datetime, Event:string)
    [datetime(1910-06-11), "Born",
     datetime(1930-01-01), "Enters Ecole Navale",
     datetime(1953-01-01), "Published first book",
     datetime(1997-06-25), "Died"]
| where strlen(Event) > 4
```

### <a name="distinct-operator"></a>distinct 演算子

値の組み合わせ (重複は除外されます) からなる行のセットが含まれるテーブルを返します。 必要に応じて、演算前にその対象を列のサブセットに限定します。

**構文**

    T | distinct *              // All columns
    T | distinct Column1, ...   // Columns to project

**例**

```AIQL
datatable (Supplier: string, Fruit: string, Price:int) 
["Contoso", "Grapes", 22,
"Fabrikam", "Apples", 14,
"Contoso", "Apples", 15,
"Fabrikam", "Grapes", 22]
| distinct Fruit, Price 
```


|Fruit|料金|
|---|---|
|Grapes|22|
|Apples|14|
|Apples|15|


### <a name="evaluate-operator"></a>evaluate 演算子
`evaluate` は、クエリに特殊なアルゴリズムを追加できるようにする拡張メカニズムです。

`evaluate` は、クエリ パイプラインの最後の演算子にする必要があります (`render` は例外)。 関数本体では使用できません。

[evaluate autocluster](#evaluate-autocluster) | [evaluate basket](#evaluate-basket) | [evaluate diffpatterns](#evaluate-diffpatterns) | [evaluate extractcolumns](#evaluate-extractcolumns)

#### <a name="evaluate-autocluster"></a>evaluate autocluster
     T | evaluate autocluster()

AutoCluster は、データの個別の属性 (ディメンション) の一般的なパターンを見つけ出し、元のクエリの結果を (100 行であっても 100k 行であっても) 少数のパターンにまとめます。 AutoCluster はエラー (例外、クラッシュなど) を分析するために開発されましたが、フィルター処理された任意のデータ セットの操作にも使用できます。 

**構文**

    T | evaluate autocluster( arguments )

**戻り値**

AutoCluster は、通常はパターンの小さなセットを返します。これらのパターンは、複数の個別の属性にわたって共通する値を持つデータの一部をキャプチャします。 各パターンは、結果内の行によって表されます。 

最初の 2 つの列は、元のクエリからパターンによってキャプチャされた行の数と割合です。 残りの列は、元のクエリにあったもので、それらの値は列の具体的な値か、変数値を意味する "*" です。 

パターンは互いに素になっていないことに注意してください。重複する可能性があり、通常は元のすべての行が含まれてはいません。 一部の行は、どのパターンにも当てはまらない場合があります。

**ヒント**

* 入力パイプで `where` と `project` を使用して、関心があるデータだけに絞り込みます。
* 関心がある行が見つかったら、 `where` フィルターに特定の値を追加して、より詳しく調べることができます。

**引数 (すべて省略可能)**

* `output=all | values | minimal` 
  
    結果の形式です。 Count および Percent 列は、常に結果に表示されます。 
  
  * `all` - 入力のすべての列が出力されます。
  * `values` - 結果で "*" だけを持つ列を除外します。
  * `minimal` - 元のクエリとすべての行が同一である列も除外します。 
* `min_percent=`*double* (既定値: 1)
  
    生成される行をカバーする最小の割合。
  
    例: `T | evaluate autocluster("min_percent=5.5")`
* `num_seeds=` *int* (既定値: 25) 
  
    シード数によって、アルゴリズムの最初のローカル検索ポイント数が決まります。 データの構造によっては、シード数を増やすと検索領域が増えて、結果の数が増えたり品質が高くなったりしますが、クエリが遅くなるトレードオフがあります。 num_seeds 引数は大きすぎても小さすぎても結果が減少し、5 より小さくしてもパフォーマンスの向上はほとんど得られず、50 より大きくしても新しいパターンが生成されることはほとんどありません。
  
    例: `T | evaluate autocluster("num_seeds=50")`
* `size_weight=` *0<double<1*+ (既定値: 0.5)
  
    一般性 (高カバレッジ) と情報性 (多くの共有値) とのバランスをある程度制御できるようになります。 通常、size_weight を増やすと、パターン数が減り、各パターンはより大きい割合をカバーするようになります。 通常、size_weight を減らすと、より多くの共有値を持つ、より具体的なパターンが生成され、より小さい割合がカバーされます。 内部的な式は、正規化された一般スコアと情報スコアとの加重幾何平均です。size_weight と 1-size_weight が重みになります。 
  
    例: `T | evaluate autocluster("size_weight=0.8")`
* `weight_column=` *column_name*
  
    入力の各行に、指定された重みを適用します (既定では、各行の重みは "1" です)。weight 列の一般的な使用方法は、既に各行に埋め込まれているデータのサンプリングまたはバケット/集計を考慮することです。
  
    例: `T | evaluate autocluster("weight_column=sample_Count")` 

#### <a name="evaluate-basket"></a>evaluate basket
     T | evaluate basket()

basket は、データの個々の属性 (ディメンション) のすべての頻出パターンを検出し、元のクエリの頻度しきい値を超えたすべての頻出パターンを返します。 データのすべての頻出パターンが検出されることが保証されていますが、多項式近似の実行時間は保証されていません。 クエリの実行時間は行数に比例しますが、場合によっては列 (ディメンション) 数の指数関数になることもあります。 basket は、元はバスケット分析データ マイニング向けに開発された Apriori アルゴリズムに基づいています。 

**戻り値**

指定された分数 (既定値は 0.05) よりも多いイベントに表れるすべてのパターン。

**引数 (すべて省略可能)**

* `threshold=` *0.015<double<1* (既定値: 0.05) 
  
    頻出と見なされる行の最小率を設定します (より少ない率のパターンは返されません)。
  
    例: `T | evaluate basket("threshold=0.02")`
* `weight_column=` *column_name*
  
    入力の各行に、指定された重みを適用します (既定では、各行の重みは "1" です)。weight 列の一般的な使用方法は、既に各行に埋め込まれているデータのサンプリングまたはバケット/集計を考慮することです。
  
    例: T | evaluate basket("weight_column=sample_Count")
* `max_dims=` *1<int* (既定値: 5)
  
    バスケットごとの非相関ディメンションの最大数を設定します。既定では、クエリの実行時間を短くするために制限されています。
* `output=minimize` | `all` 
  
    結果の形式です。 Count および Percent 列は、常に結果に表示されます。
  
  * `minimize` - 結果で "*" だけを持つ列を除外します。
  * `all` - 入力のすべての列が出力されます。

#### <a name="evaluate-diffpatterns"></a>evaluate diffpatterns
     requests | evaluate diffpatterns("split=success")

diffpatterns は、同じ構造の 2 つのデータ セットを比較し、2 つのデータ セット間の違いを特徴付ける個々の属性 (ディメンション) のパターンを発見します。 diffpatterns は、エラーの分析を支援する (たとえば、所定の期間内のエラーと非エラーを比較する) ために開発されましたが、同じ構造の 2 つの任意のデータ セット間の差異も見つけることができます。 

**構文**

`T | evaluate diffpatterns("split=` *BinaryColumn* `" [, arguments] )`

**戻り値**

diffpatterns は、2 つのセット内の異なる部分のデータをキャプチャするパターン (つまり、1 つ目のデータ セットの大きな割合の行と 2 つ目のデータ セットの小さな割合の行をキャプチャするパターン) の、通常は小さいセットを返します。 各パターンは、結果内の行によって表されます。

最初の 4 つの列は、各セットのパターンによってキャプチャされた、元のクエリからの行の数と割合であり、5 番目の列は 2 つのセット間の差異 (絶対パーセント ポイント) です。 残りの列は、元のクエリにあったもので、それらの値は列の具体的な値か、変数値を意味する * です。 

パターンは個別ではないことに注意してください。重複する可能性があり、通常は元のすべての行が含まれてはいません。 一部の行は、どのパターンにも当てはまらない場合があります。

**ヒント**

* 入力パイプで where と project を使用して、関心があるデータだけに絞り込みます。
* 関心がある行が見つかったら、where フィルターに特定の値を追加して、より詳しく調べることができます。

**引数**

* `split=` *column name* (必須)
  
    この列は、正確に 2 つの値を持つ必要があります。 必要に応じて、そのような列を作成します。
  
    `requests | extend fault = toint(resultCode) >= 500` <br/>
    `| evaluate diffpatterns("split=fault")`
* `target=` *string*
  
    対象データ セットで、より割合が高いパターンだけを探すようにアルゴリズムに指示します。対象は、split 列の 2 つの値のうちのいずれかである必要があります。
  
    `requests | evaluate diffpatterns("split=success", "target=false")`
* `threshold=` *0.015<double<1* (既定値: 0.05) 
  
    2 つのセット間の最小限のパターン (割合) の差を設定します。
  
    `requests | evaluate diffpatterns("split=success", "threshold=0.04")`
* `output=minimize | all`
  
    結果の形式です。 Count および Percent 列は、常に結果に表示されます。 
  
  * `minimize` - 結果で "*" だけを持つ列を除外します。
  * `all` - 入力のすべての列が出力されます。
* `weight_column=` *column_name*
  
    入力の各行に、指定された重みを適用します (既定では、各行の重みは "1" です)。 weight 列の一般的な使用方法は、既に各行に埋め込まれているデータのサンプリングまたはバケット/集計を考慮することです。
  
    `requests | evaluate autocluster("weight_column=itemCount")`

#### <a name="evaluate-extractcolumns"></a>evaluate extractcolumns
     exceptions | take 1000 | evaluate extractcolumns("details=json") 

extractcolumns は、テーブルに複数の単純な列を追加するために使用されます。それらの列は、種類に基づいて、(半) 構造化された列から動的に抽出されます。 現在、サポートされているのは json 列だけです。json の dynamic と string の両方のシリアル化に対応しています。

* `max_columns=` *int* (既定値: 10) 
  
    新たに追加される列の数は動的であり、非常に多くなることもあるため (実際には、すべての json レコードの個別のキーの数)、制限する必要があります。 新しい列は、頻度に基づいて降順に並べ替えられ、最大で max_columns の数までテーブルに追加されます。
  
    `T | evaluate extractcolumns("json_column_name=json", "max_columns=30")`
* `min_percent=` *double* (既定値: 10.0) 
  
    新しい列を制限するもう 1 つの方法です。頻度が min_percent より低い列を無視します。
  
    `T | evaluate extractcolumns("json_column_name=json", "min_percent=60")`
* `add_prefix=` *bool* (既定値: true) 
  
    true の場合、抽出された列の名前にプレフィックスとして複合列の名前が追加されます。
* `prefix_delimiter=` *string* (既定値: "_") 
  
    add_prefix=true の場合、このパラメーターは新しい列の名前を連結するために使用される区切り記号を定義します。
  
    `T | evaluate extractcolumns("json_column_name=json",` <br/>
    `"add_prefix=true", "prefix_delimiter=@")`
* `keep_original=` *bool* (既定値: false) 
  
    true の場合、元の (json) 列は出力テーブル内に保持されます。
* `output=query | table` 
  
    結果の形式です。 
  
  * `table` - 出力は、受け取ったテーブルと同じテーブルです。ただし、指定した入力列はなく、入力列から抽出された新しい列が追加されています。
  * `query` - 出力は、結果をテーブルとして取得するために作成するクエリを表す文字列です。 

### <a name="extend-operator"></a>extend 演算子
     T | extend duration = stopTime - startTime

テーブルに 1 つ以上の計算列を追加します。 

**構文**

    T | extend ColumnName = Expression [, ...]

**引数**

* *T:* 入力テーブル。
* *ColumnName:* 追加する列の名前。 [名前](#names)は、大文字と小文字が区別されます。また、名前には、アルファベット、数字、または "_" 文字を使用できます。 キーワードまたは名前を他の文字で囲む場合は、`['...']` または `["..."]` を使います。
* *Expression:* 既存の列に対する計算。

**戻り値**

指定の列が追加された、入力テーブルのコピー。

**ヒント**

* 複数の列を削除するか複数の列の名前を変更する場合は、 [`project`](#project-operator) を代わりに使います。
* 長い式で使う短い名前を取得するためだけに `extend` を使うのは避けてください。 `...| extend x = anonymous_user_id_from_client | ... func(x) ...` 
  
    テーブルのネイティブ列のインデックスは既に作成されていますが、新しい名前により、インデックスが作成されていない追加の列が定義されるため、クエリの実行速度が低下する可能性があります。

**例**

```AIQL
traces
| extend
    Age = now() - timestamp
```

### <a name="find-operator"></a>find 演算子

    find in (Table1, Table2, Table3) where id=="a string"
    find in (Table1, Table2, Table3) where id=="a string" project column1, column2
    find in (Table1, Table2, Table3) where * has "a string"
    find in (Table1, Table2, Table3) where appName in ("string 1", "string 2", "string 3")

テーブルのセットで述語が一致する行を検索します。

**構文**

    find in (Table1, ...) 
    where Predicate 
    [project Column1, ...]

**引数**

* *Table1* テーブル名またはクエリ。 let-defined テーブルを指定できますが、関数は指定できません。 テーブル名は、クエリよりもパフォーマンスに優れています。
* *Predicate* 指定したテーブルのすべての行について評価されるブール式。
 * 文字列の比較では、列名の代わりに "*" を使用できます。
* *Column1* `project` オプションを使用すると、出力に常に表示する列を指定できます。 

**結果**

出力テーブルに既定で含まれるものを次に示します。

* `source_` - 各行のソース テーブルのインジケーター。 この列に表示される名前を指定する場合は、各テーブル式の最後で [as](#as-operator) を使用します。
* 述語で明示的に示されている列
* すべての入力テーブルに共通の空ではない列。
* `pack_` - 他の列からのデータを含むプロパティ バッグ。

この形式は、入力データまたは述語の変更によって変わることがあります。 固定された列セットを指定するには、`project` を使用します。

**例**

すべての要求と例外を取得します。ただし、可用性テストとロボットからの要求と例外は除きます。

```AIQL

    find in (requests, exceptions) where isempty(operation_SyntheticSource)
```

UK からのすべての要求と例外を見つけます。ただし、可用性テストとロボットからの要求と例外は除きます。

```AIQL

    let requk = requests
    | where client_CountryOrRegion == "United Kingdom";
    let exuk = exceptions
    | where client_CountryOrRegion == "United Kingdom";
    find in (requk, exuk) where isempty(operation_SyntheticSource)
```

"test" という用語を含むフィールドがある最新の製品利用統計情報を見つけます。

```AIQL

    find in (traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions) 
    where * has 'test' 
    | top 100 by timestamp desc
```

**パフォーマンスに関するヒント**

* 時間ベースの用語を `where` 述語に追加します。
* クエリをインラインに記述するのではなく、`let` 句を使用します。

### <a name="getschema-operator"></a>getschema 演算子

   T | getschema
   
入力テーブルの列名とタイプを表示するテーブルを生成します。

```AIQL
requests
| project appId, appName, customDimensions, duration, iKey, itemCount, success, timestamp 
| getschema 
```

![getschema の結果](./media/app-insights-analytics-reference/getschema.png)

### <a name="join-operator"></a>join 演算子
    Table1 | join (Table2) on CommonColumn

指定された列の値を照合して 2 つのテーブルの行をマージします。

**構文**

    Table1 | join [kind=Kind] (Table2) on CommonColumn [, ...]

**引数**

* *Table1* - 結合の "左側"。
* *Table2* - 結合の "右側"。 テーブルを出力する入れ子のクエリ式にすることができます。
* *CommonColumn* - 2 つのテーブルで同じ名前を持つ列。
* *Kind* - 2 つのテーブルの行を照合する方法を指定します。

**戻り値**

次の要素が含まれるテーブル:

* 照合キーを含め、2 つのテーブルそれぞれのすべての列に対応する列。 名前が競合している場合は、右側の列の名前が自動的に変更されます。
* 入力テーブル間でのすべての一致に対応する行。 片方のテーブルから選択された、もう一方のテーブルに含まれる 1 つの行とすべての `on` フィールドの値が同じである行です。 
* `Kind` が未指定または `= innerunique`
  
    左側の 1 つの行のみが `on` キーの各値に対して照合されます。 出力には、この行の一致それぞれに対応する行と、右側の行が含まれます。
* `kind=inner`
  
     出力には、左右の一致行のすべての組み合わせに対応する行が含まれます。
* `kind=leftouter` (あるいは `kind=rightouter` または `kind=fullouter`)
  
     内部の一致のほかに、左側と右側のどちらか一方または両方のすべての行に対応する行が含まれます (一致するものがない場合も)。 その場合、一致しない出力セルには null が含まれます。
* `kind=leftanti`
  
     右側との一致が存在しない、左側のすべてのレコードを返します。 結果のテーブルには、左側の列のみが含まれます。 
* `kind=leftsemi` (または `leftantisemi`)

    右側のテーブルに一致がある (または、ない) 場合に、左側のテーブルから行を返します。 結果には、右側からのデータは含まれません。


**ヒント**

結果のテーブルには 64 MB という制限があります。

パフォーマンスを最大限高めるためのヒントを示します。

* 入力テーブルの行と列の数を減らすには、`join` の前に `where` と `project` を使います。 
* 一方のテーブルがもう一方よりも常に小さい場合は、それを結合の左側 (パイプされる側) として使います。
* 一致した場合に結合する列は、同じ名前を持つ必要があります。 いずれかのテーブルの列の名前を変更する必要がある場合は、project 演算子を使います。



**例**

アクティビティの開始と終了がマークされているエントリが含まれるログから、延長されたアクティビティを取得します。 

```AIQL
    let Events = MyLogTable | where type=="Event" ;
    Events
    | where Name == "Start"
    | project Name, City, ActivityId, StartTime=timestamp
    | join (Events
           | where Name == "Stop"
           | project StopTime=timestamp, ActivityId)
        on ActivityId
    | project City, ActivityId, StartTime, StopTime, Duration, StopTime, StartTime

```


### <a name="limit-operator"></a>limit 演算子
     T | limit 5

指定の数を上限に、入力テーブルから行を返します。 どのレコードが返されるかはわかりません (特定のレコードを返すには、[`top`](#top-operator)を使います)。

**エイリアス** `take`

**構文**

    T | limit NumberOfRows


**ヒント**

`Take` は、インタラクティブに操作しているときに結果のサンプルを見るうえで、シンプルかつ効率的な手段です。 ただし、特定の行を生成したり、特定の順序で生成したりすることはできません。

`take`を使用していなくても、クライアントに返される行の数には暗黙的な制限があります。 この制限を引き上げるには、 `notruncation` クライアント要求オプションを使います。

### <a name="make-series-operator"></a>make-series 演算子

集計を実行します。 [summarize](#summarize-operator) とは異なり、出力はグループごとに 1 行になります。 結果の列では、各グループの値が配列にまとめられます。 

**構文**

    T | 
    make-series [Column =] Aggregation default = DefaultValue [, ...] 
    on AxisColumn in range(start, stop, step) 
    by [Column =] GroupExpression [, ...]


**引数**

* *Column:* 結果列の省略可能な名前。 既定値は式から派生した名前です。
* *DefaultValue:* AxisColumn と GroupExpression の特定の値を持つ行がない場合、結果では、配列の対応する要素に DefaultValue が割り当てられます。 
* *Aggregation:* [集計関数](#aggregations)を使用する数値式。 
* *AxisColumn:* 系列が順序付けられている列。 タイムラインと見なすことができますが、任意の数値タイプを指定できます。
*start、stop、step:* すべての行について、AxisColumn の値のリストを定義します。 その他すべての結果集計列には、同じ長さの配列が含まれます。 
* *GroupExpression:* 列に対する式です。個別の値のセットを示します。 GroupExpression の各値が 1 行で出力されます。 通常は、限られた値のセットが既に指定されている列名です。 

**ヒント**

結果の配列は、対応する集計操作と同じ方法で Analytics のグラフに表示されます。

**例**

```AIQL
requests
| make-series sum(itemCount) default=0, avg(duration) default=0
  on timestamp in range (ago(7d), now(), 1d)
  by client_City
```

![make-series の結果](./media/app-insights-analytics-reference/make-series.png)

### <a name="mvexpand-operator"></a>mvexpand 演算子
    T | mvexpand listColumn 

動的に型指定された (JSON) セルからリストを展開して、エントリごとに別の行になるようにします。 展開された行内のその他すべてのセルは複製されます 

(逆の処理を実行する [`summarize makelist`](#summarize-operator) も参照してください)。

**例**

次のような入力テーブルを想定します。

| A:int | B:string | D:dynamic |
| --- | --- | --- |
| 1 |"hello" |{"key":"value"} |
| 2 |"world" |[0,1,"k","v"] |

    mvexpand D

結果は次のとおりです。

| A:int | B:string | D:dynamic |
| --- | --- | --- |
| 1 |"hello" |{"key":"value"} |
| 2 |"world" |0 |
| 2 |"world" |1 |
| 2 |"world" |"k" |
| 2 |"world" |"v" |

**構文**

    T | mvexpand  [bagexpansion=(bag | array)] ColumnName [limit Rowlimit]

    T | mvexpand  [bagexpansion=(bag | array)] [Name =] ArrayExpression [to typeof(Typename)] [limit Rowlimit]

**引数**

* *ColumnName:* 結果では、指定された列内の配列が複数の行に展開されます。 
* *ArrayExpression:* 配列を生成する式。 この形式を使用した場合は、新しい列が追加され、既存の列は保持されます。
* *Name:* 新しい列の名前。
* *Typename:* 展開された式を特定の型にキャストします。
* *RowLimit:* 元の各行から生成される行の最大数。 既定値は 128 です。

**戻り値**

指定された列の配列か、配列の式の各値に対応する複数の行。

展開された列は常に動的な型を持ちます。 値を計算または集計する場合は、`todatetime()` や `toint()` などのキャストを使います。

2 つのモードのプロパティ バッグの展開がサポートされています。

* `bagexpansion=bag`: プロパティ バッグは、単一エントリのプロパティ バッグに展開されます。 これが既定の展開です。
* `bagexpansion=array`: プロパティ バッグは 2 要素 (`[`*key*`,`*value*`]`) の配列構造に展開されるため、キーと値への一貫したアクセスが可能です (プロパティ名での個別のカウントの集計など)。

**例**

    exceptions | take 1
    | mvexpand details[0]

例外のレコードを詳細フィールドの項目ごとに行に分割します。

### <a name="parse-operator"></a>parse 演算子
    T | parse "I got 2 socks for my birthday when I was 63 years old"
    with * "got" counter:long " " present "for" * "was" year:long *


    T | parse kind=relaxed
          "I got no socks for my birthday when I was 63 years old"
    with * "got" counter:long " " present "for" * "was" year:long *

    T |  parse kind=regex "I got socks for my 63rd birthday"
    with "(I|She) got " present " for .*?" year:long *

文字列から値を抽出します。 単純な照合または正規表現の照合を使用できます。

**構文**

    T | parse [kind=regex|relaxed] SourceText
        with [Match | Column [: Type [*]] ]  ...

**引数**

* `T`: 入力テーブル。
* `kind`: 
  * `simple` (既定値): `Match` の文字列はプレーン文字列です。
  * `relaxed`: テキストが列の型として解析されない場合、その列は null に設定され、解析が続行されます。 
  * `regex`: `Match` の文字列は正規表現です。
* `Text`: 文字列に評価されるか、文字列に変換できる列またはその他の式。
* *Match:* 文字列の次の部分を照合し、破棄します。
* *Column:* 文字列の次の部分をこの列に割り当てます。 この列が存在しない場合は作成されます。
* *Type:* 文字列の次の部分を指定の型 (int、date、double など) として解析します。 

**戻り値**

列のリストに応じて展開された入力テーブル。

`with` 句内の要素は、ソース テキストに対して順番に照合されます。 この要素ごとに、ソース テキストのチャンクが処理されます。 

* リテラル文字列または正規表現は、照合カーソルをその一致の長さごとに移動します。
* 正規表現の解析の場合、正規表現で最小化演算子 "?" を使用すると、できるだけ短時間で次の一致に移動できます。
* 型を持つ列名は、指定した型としてテキストを解析します。 kind=relaxed の場合を除き、解析に失敗すると、パターン全体の照合が無効になります。
* 型を持たない列名または "string" 型の列名では、最小限の文字をコピーして、次の一致に移動します。
* "*" は、最小限の文字をスキップして、次の一致に移動します。"*" は、パターンの先頭と末尾、文字列以外の型の後、または文字列の一致の間に使用できます。

解析パターンに含まれるすべての要素は正確に一致する必要があります。一致しない場合は、結果が生成されません。 この規則の例外として、kind=relaxed の場合に、型指定された変数の解析に失敗すると、解析の残り部分が続行されます。

**例**

*Simple の場合:*

```AIQL

// Test without reading a table:
 range x from 1 to 1 step 1 
 | parse "I got 2 socks for my birthday when I was 63 years old" 
    with 
     *   // skip until next match
     "got" 
     counter: long // read a number
     " " // separate fields
     present // copy string up to next match
     "for" 
     *  // skip until next match
     "was" 
     year:long // parse number
     *  // skip rest of string
```

| ○ | counter | present | Year |
| --- | --- | --- | --- |
| 1 |2 |socks |63 |

*Relaxed の場合:*

型指定されたすべての列について正確な一致を含む入力の場合、制限の緩い解析では、単純な解析と同じ結果が生成されます。 ただし、型指定された列の 1 つが正しく解析されない場合、制限の緩い解析では、残りのパターンの処理が続行されます。その一方で、単純な解析は停止し、結果の生成に失敗します。

```AIQL

// Test without reading a table:
 range x from 1 to 1 step 1 
 | parse kind="relaxed"
        "I got several socks for my birthday when I was 63 years old" 
    with 
     *   // skip until next match
     "got" 
     counter: long // read a number
     " " // separate fields
     present // copy string up to next match
     "for" 
     *  // skip until next match
     "was" 
     year:long // parse number
     *  // skip rest of string
```


| ○ | present | Year |
| --- | --- | --- |
| 1 |socks |63 |

*Regex の場合:*

```AIQL

// Run a test without reading a table:
range x from 1 to 1 step 1 
// Test string:
| extend s = "Event: NotifySliceRelease (resourceName=Scheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 07:31, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 06:20:00 ) }" 
// Parse it:
| parse kind=regex s 
  with ".*?=" resource 
       ", total.*?sliceNumber=" slice:long *
       "lockTime=" lock
       ",.*?releaseTime=" release 
       ",.*?previousLockTime=" previous:date 
       @".*\)" *
| project-away x, s
```

| resource | slice | lock | release | previous |
| --- | --- | --- | --- | --- |
| Scheduler |16 |02/17/2016 07:31:00 |02/17/2016 08:41 |2016-02-17T06:20:00Z |

### <a name="project-operator"></a>project 演算子
    T | project cost=price*quantity, price

含める列、名前を変更する列、または削除する列を選択し、新しい計算列を挿入します。 結果の列の順序は、引数の順序によって指定されます。 引数で指定された列のみが結果に含まれ、入力内のそれ以外の列は削除されます  (逆の処理を実行する `extend`も組み合わせて使います)。

**構文**

    T | project ColumnName [= Expression] [, ...]

**引数**

* *T:* 入力テーブル。
* *ColumnName:* 出力に表示される列の名前。 *Expression*を指定しない場合は、この名前の列が入力に存在する必要があります。 [名前](#names)は、大文字と小文字が区別されます。また、名前には、アルファベット、数字、または "_" 文字を使用できます。 キーワードまたは名前を他の文字で囲む場合は、`['...']` または `["..."]` を使います。
* *Expression:* 入力列を参照する、省略可能なスカラー式。 
  
    入力内の既存の列と同じ名前を持つ新しい計算列を返すことは、問題ありません。

**戻り値**

引数で指定された列と、入力テーブルと同数の行が含まれるテーブル。

**例**

次の例は、 `project` 演算子を使って実行できる何種類かの操作を示しています。 入力テーブル `T` には、`int` 型の列が 3 つあります (`A`、`B`、`C`)。 

```AIQL
T
| project
    X=C,               // Rename column C to X
    A=2*B,             // Calculate a new column A from the old B
    C=strcat("-",tostring(C)), // Calculate a new column C from the old C
    B=2*B,              // Calculate a new column B from the old B
    ['where'] = client_City // rename, using a keyword as a column name
```

### <a name="project-away-operator"></a>project-away 演算子
    T | project-away column1, column2, ...

指定された列を除外します。 結果には、指定した列以外のすべての入力列が含まれます。

### <a name="range-operator"></a>range 演算子
    range LastWeek from ago(7d) to now() step 1d

値が含まれる 1 列のテーブルを生成します。 パイプラインの入力はないことに注目してください。 

| LastWeek |
| --- |
| 2015-12-05 09:10:04.627 |
| 2015-12-06 09:10:04.627 |
| ... |
| 2015-12-12 09:10:04.627 |

**構文**

    range ColumnName from Start to Stop step Step

**引数**

* *ColumnName:* 出力テーブル内の 1 つの列の名前。
* *Start:* 出力の最小値。
* *Stop:* 出力で生成される最高値 (*step* でこの値をステップオーバーする場合は、限度内の最高値)。
* *Step:* 2 つの連続する値の差異。 

この引数は、数値、日付、または期間の値である必要があります。 テーブルの列を参照することはできません (入力テーブルに基づいて範囲を計算する場合は、[range *関数*](#range)を使います。場合によっては、[mvexpand 演算子](#mvexpand-operator)も組み合わせて使います)。 

**戻り値**

*ColumnName* という 1 つの列を持つテーブル。その列の値は、*Start*、*Start* + *Step* というように続き、*Stop* までとなります。

**例**  

```AIQL
range Steps from 1 to 8 step 3
```

`Steps` という 1 つの列を持つテーブルです。その列の型は `long`、値は `1`、`4`、`7` です。

**例**

    range LastWeek from bin(ago(7d),1d) to now() step 1d

過去 7 日間の深夜のテーブルです。 bin (floor) 関数により、各時刻が 1 日の開始時刻になっています。

**例**  

```AIQL
range timestamp from ago(4h) to now() step 1m
| join kind=fullouter
  (traces
      | where timestamp > ago(4h)
      | summarize Count=count() by bin(timestamp, 1m)
  ) on timestamp
| project Count=iff(isnull(Count), 0, Count), timestamp
| render timechart  
```

`range` 演算子を使って小規模でアドホックなディメンション テーブルを作成する方法を示しています。さらに、このテーブルは、ソース データに値がない場合にゼロを取り込むために使用されています。

### <a name="reduce-operator"></a>reduce 演算子
    exceptions | reduce by outerMessage

類似したレコードのグループ化を試みます。 この演算子は、グループごとに、そのグループを最も適切に表していると思われる `Pattern` と、そのグループ内のレコードの `Count` を出力します。

![](./media/app-insights-analytics-reference/reduce.png)

**構文**

    T | reduce by  ColumnName [ with threshold=Threshold ]

**引数**

* *ColumnName:* 調べる列。 文字列型である必要があります。
* <seg>
  *Threshold:* 範囲 {0..1} 内の値。</seg> 既定値は 0.001 です。 入力のサイズが大きい場合は、しきい値を小さくする必要があります。 

**戻り値**

`Pattern` と `Count` の 2 つの列。 多くの場合、Pattern は列の完全な値になります。 一部のケースでは、共通するタームを識別し、変数の部分を '*' に置き換えることもできます。

たとえば、 `reduce by city` の結果には次のものが含まれます。 

| パターン | Count |
| --- | --- |
| San * |5182 |
| Saint * |2846 |
| Moscow |3726 |
| \* -on- \* |2730 |
| パリ |27163 |

### <a name="render-directive"></a>render ディレクティブ
    T | render [ table | timechart  | barchart | piechart ]

render では、テーブルの表示方法をプレゼンテーション層に指示します。 パイプの最後の要素である必要があります。 特定のプレゼンテーション メソッドでクエリを保存でき、ディスプレイのコントロールの代わりに使用できる便利な方法です。

### <a name="restrict-clause"></a>restrict 句
以下に示す演算子で使用可能なテーブル名のセットを指定します。 次に例を示します。

    let e1 = requests | project name, client_City;
    let e2 =  requests | project name, success;
    // Exclude predefined tables from the union:
    restrict access to (e1, e2);
    union * |  take 10 

### <a name="sample-operator"></a>sample 演算子

入力テーブルから、一様に分布したランダムな行を返します。


**構文**

    T | sample NumerOfRows

* *NumberOfRows* サンプルとして返される行の数。

**ヒント**

一様に分布したサンプルが必要ない場合は、`Take` を使用します。


### <a name="sample-distinct-operator"></a>sample-distinct 演算子

要求された列について、指定された個数までの重複なしの値が含まれる 1 つの列を返します。 現時点では、適切に分布したサンプルは返されません。

**構文**

    T | sample-distinct NumberOfValues of ColumnName

* *NumberOfValues* 必要とする、テーブルの長さ。
* *ColumnName* 必要とする列。

**ヒント**

sample-distinct を let ステートメントに配置し、後で in 演算子を使用してフィルター処理して、母集団をサンプリングする場合に便利なことがあります (例をご覧ください)。
 
ただのサンプルではなく上位値が必要な場合は、top-hitters 演算子を使用できます。

(特定の列の値ではなく) データ行をサンプリングする場合は、「[sample 演算子](#sample-operator)」をご覧ください。

**例**

summarize 演算がクエリの制限を超えないことがわかっている場合に、母集団をサンプリングしてさらに計算を行います。

```AIQL
let sampleops = toscalar(requests | sample-distinct 10 of OperationName);
requests | where OperationName in (sampleops) | summarize total=count() by OperationName
```
### <a name="search-operator"></a>search 演算子

複数のテーブルと列内の文字列を検索します。

**構文**

    search [kind=case_sensitive] [in (TableName, ...)] SearchToken

    T | search [kind=case_sensitive] SearchToken

    search [kind=case_sensitive] [in (TableName, ...)] SearchPredicate

    T | search [kind=case_sensitive] SearchPredicate

任意のテーブルの任意の列で、指定されたトークン文字列の出現箇所を検索します。
 
* *TableName:* グローバルに (要求、例外など)、または [let 句](#let-clause)によって定義されているテーブルの名前。 r* などのワイルドカードを使用できます。
* *SearchToken:* トークン文字列。単語全体と一致する必要があります。 末尾にワイルドカードを使用できます。 "Amster*" は "Amsterdam" に一致しますが、"Amster" には一致しません。
* *SearchPredicate:* テーブル内の列に対するブール式。 列名で "*" をワイルドカードとして使用できます。

**例**

```AIQL
search "Amster*"  //All columns, all tables

search name has "home"  // one column

search * has "home"     // all columns

search in (requests, exceptions) "Amster*"  // two tables

requests | search "Amster*"

requests | search name has "home"

```




### <a name="sort-operator"></a>sort 演算子
    T | sort by country asc, price desc

入力テーブルの行を 1 つ以上の列の順序で並べ替えます。

**エイリアス** `order`

**構文**

    T  | sort by Column [ asc | desc ] [ , ... ]

**引数**

* *T:* 並べ替える入力テーブル。
* *Column:* 並べ替えに使用する *T* の列。 値の型は、数値、日付、時刻、または文字列にする必要があります。
* `asc` : 昇順で (小さい値から大きい値へ) 並べ替えます。 既定値は `desc`で、降順 (大きい値から小さい値へ) です。

**例**

```AIQL
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc
```
特定の `ActivityId`を持つ Traces テーブルのすべての行が、タイムスタンプの順で並べ替えられます。

### <a name="summarize-operator"></a>summarize 演算子
入力テーブルの内容を集計したテーブルを生成します。

    requests
    | summarize count(), avg(duration), makeset(client_City) 
      by client_CountryOrRegion

数、平均要求期間、各国の都市のセットを示すテーブルです。 出力には国ごとの行が含まれます。 出力列には、数、平均期間、都市、国が示されます。 他のすべての入力列は無視されます。

    T | summarize count() by price_range=bin(price, 10.0)

各間隔 ([0,10.0]、[10.0,20.0] など) で価格を持つ項目の数を示すテーブル。 この例では、数の列と価格範囲の列があります。 他のすべての入力列は無視されます。

**構文**

    T | summarize
         [  [ Column = ] Aggregation [ , ... ] ]
         [ by
            [ Column = ] GroupExpression [ , ... ] ]

**引数**

* *Column:* 結果列の省略可能な名前。 既定値は式から派生した名前です。 [名前](#names)は、大文字と小文字が区別されます。また、名前には、アルファベット、数字、または "_" 文字を使用できます。 キーワードまたは名前を他の文字で囲む場合は、`['...']` または `["..."]` を使います。
* *Aggregation:* 引数として列名を指定して、`count()`や`avg()`などの集計関数を呼び出します。 「 [集計](#aggregations)」を参照してください。
* *GroupExpression:* 列に対する式です。個別の値のセットを示します。 通常は、限られた値のセットが既に指定されている列名か、引数として数値列または時間列が指定されている `bin()` になります。 

`bin()` を使用せずに数値式または時間式を指定した場合、Analytics は自動的に `1h` (時間の場合) または `1.0` (数値の場合) の間隔でその式を適用します。

*GroupExpression* を指定しない場合は、テーブル全体が単一の出力行にまとめられます。

**戻り値**

入力列は、`by` 式の同じ値を持つグループにまとめられます。 次に、指定された集計関数によってグループごとに計算が行われ、各グループに対応する行が生成されます。 結果には、`by` 列のほか、計算された各集計に対応する 1 つ以上の列も含まれます (一部の集計関数は複数の列を返します)。

結果には、 `by` 値の個別の組み合わせと同数の行が含まれます。 数値の範囲をまとめる場合は、 `bin()` を使って範囲を不連続値に減らしてください。

> [!NOTE]
> 集計式とグループ化式の両方に任意の式を指定できますが、単純な列名を使用するか、 `bin()` を数値列に適用する方がより効率的です。

### <a name="table-operator"></a>table 演算子

    table('pageViews')

テーブルの名前を引数の文字列で指定します。

**構文**

    table(tableName)

**引数**

* *tableName:* 文字列。 テーブルの名前として、静的な値か let 句の結果のいずれかを指定できます。

**例**

    table('requests');


    let size = (tableName: string) {
        table(tableName) | summarize sum(itemCount)
    };
    size('pageViews');



### <a name="take-operator"></a>take 演算子
[limit](#limit-operator)

### <a name="top-operator"></a>top 演算子
    T | top 5 by Name desc nulls first

指定された列で並べ替えられた、先頭の *N* 個のレコードを返します。

**構文**

    T | top NumberOfRows by Sort_expression [ asc | desc ] [nulls first|nulls last] [, ... ]

**引数**

* *NumberOfRows:* 返される *T* の行の数。
* *Sort_expression:* 行の並べ替えに使用する式。 通常は単なる列名です。 複数の sort_expression を指定できます。
* `asc` または `desc` (既定値) は、実際には選択が範囲の "下限" と "上限" のどちらから行われるかを制御します。
* `nulls first` または `nulls last` は null 値が表示される場所を制御します。 `First` の既定値は、 `asc`、`last` の既定値は `desc` です。

**ヒント**

`top 5 by name` は、表面的には `sort by name | take 5` と同等です。 しかし、こちらの方が処理速度が早く、常に並べ替えられた結果を返します。`take` にはこのような保証がありません。

### <a name="top-nested-operator"></a>top-nested 演算子
    requests
    | top-nested 5 of name by count()
    , top-nested 3 of performanceBucket by count()
    , top-nested 3 of client_CountryOrRegion by count()
    | render barchart

各レベルが前のレベルからのドリルダウンである、階層型の結果を生成します。 これは、"上位 5 件のリクエストは何か。そのそれぞれについて、上位 3 つのパフォーマンス バケットはどのようなものか。さらにそのそれぞれについて、リクエストの出された上位 3 つの国はどこか" というような質問に答える場合に便利です。

**構文**

   T | top-nested N of COLUMN by AGGREGATION [, ...]

**引数**

* N:int - 返す行または次のレベルに渡す行の数。 3 レベルのクエリ (N が 5、3、3) では、行の合計数は 45 になります。
* COLUMN - 集計のためにグループ化する列。 
* AGGREGATION - 行の各グループに適用する [集計関数](#aggregations) 。 これらの集計の結果により、表示される上位のグループが決まります。

### <a name="union-operator"></a>union 演算子
     Table1 | union Table2, Table3

複数のテーブルを受け取り、それらすべてのテーブルの行を返します。 

**構文**

    T | union [ kind= inner | outer ] [ withsource = ColumnName ] Table2 [ , ...]  

    union [ kind= inner | outer ] [ withsource = ColumnName ] Table1, Table2 [ , ...]  

**引数**

* *Table1*, *Table2* ...
  * テーブルの名前 ( `requests`など)、または [let 句](#let-clause)で定義されたテーブルの名前
  * クエリ式 ( `(requests | where success=="True")`
  * ワイルドカードで指定されたテーブルのセット。 たとえば、 `e*` は、"exceptions" テーブルと共に、前に示した let 句で定義された、名前が "e" で始まるすべてのテーブルの和集合を形成します。
* `kind`: 
  * `inner` - 結果には、すべての入力テーブルに共通する列のサブセットが含まれます。
  * `outer` - 結果には、入力のいずれかに存在するすべての列が含まれます。 入力行で定義されていなかったセルは `null`に設定されます。
* `withsource=`*ColumnName:* これを指定した場合は、各行の基になっているソース テーブルを示す値が格納された列 (名前は *ColumnName*) が出力に含まれます。 この列に表示される名前を指定する場合は、各テーブル式の最後で [as](#as-operator) を使用します。

**戻り値**

すべての入力テーブルに存在する行と同数の行と、入力に存在する一意の列名と同数の列を含むテーブル。

行の順序は保証されていません。


**例**

過去 12 時間で `exceptions` イベントまたは `traces` イベントを発生させた個別のユーザーの数。 結果の "SourceTable" 列は "exceptions" または "traces" を指します。

```AIQL
    
    union withsource=SourceTable kind=outer exceptions, traces
    | where timestamp > ago(12h)
    | summarize dcount(user_Id) by SourceTable
```

より効率的なこのバージョンでも同じ結果が生成されます。 和集合を作成する前に各テーブルをフィルター処理します。

```AIQL
    exceptions
    | where timestamp > ago(24h) | as exceptions
    | union withsource=SourceTable kind=outer (requests | where timestamp > ago(12h) | as traces)
    | summarize dcount(user_Id) by SourceTable 
```

ソース列に表示する名前を指定するには、[as](#as-operator) を使用します。

#### <a name="forcing-an-order-of-results"></a>結果の順序を強制する

和集合では、結果の行で特定の順序が保証されるものではありません。
クエリを実行するたびに同じ順序にするには、各入力テーブルにタグ列を付加します。

    let r1 = (traces | count | extend tag = 'r1');
    let r2 = (requests | count| extend tag = 'r2');
    let r3 = (pageViews | count | extend tag = 'r3');
    r1 | union r2,r3 | sort by tag

#### <a name="see-also"></a>関連項目

代わりに [join 演算子](#join-operator)を検討してください。

### <a name="where-operator"></a>where 演算子
     requests | where resultCode=="200"

述語の条件を満たす行のサブセットにテーブルをフィルター処理します。

**エイリアス** `filter`

**構文**

    T | where Predicate
    T | where * has Term

**引数**

* *T* : フィルター処理するレコードが含まれる表形式の入力。
* *Predicate:* *T* の列に対する `boolean` [式](#boolean)。*T* 内の各行について評価されます。
* *Term* - 列内の単語全体と一致する必要がある文字列。

**戻り値**

*Predicate* が `true` である *T* 内の行。

**ヒント**

パフォーマンスを最大限高めるためのヒントを示します。

* **シンプルな比較を使う** ("定数" とは、テーブルに対する定数です。 ("定数" とは、テーブルに対する定数です。`now()` と `ago()` は適切で、[`let` 句](#let-clause)を使って割り当てられるスカラー値も同様です)。
  
    たとえば、`where floor(Timestamp, 1d) == ago(1d)` よりも `where Timestamp >= ago(1d)` の方が適切です。
* **最もシンプルな語句を先頭に配置する**: `and` で連結された複数の句がある場合は、関係する列が 1 つしかない句を先頭に配置します。 そのため、 `Timestamp > ago(1d) and OpId == EventId` の方が、逆の順序にするよりも適切です。

**例**

```AIQL
traces
| where Timestamp > ago(1h)
    and Source == "Kuskus"
    and ActivityId == SubActivityIt 
```

同じ値の 2 列を持ち、"Kuskus" というソースから取得された、1 時間以内のレコードです。 

2 つの列の比較を最後に配置していることに注目してください。これは、インデックスを使用できず、スキャンを強制するためです。



## <a name="aggregations"></a>集計
集計とは、 [summarize 演算](#summarize-operator)で作成されたグループの値を結合するための関数です。 たとえば、次のクエリでは、dcount() が集計関数です。

    requests | summarize dcount(name) by success

### <a name="any"></a>任意
    any(Expression)

ランダムにグループの 1 つの行を選択し、指定された式の値を返します。

これは、いくつかの列 ("エラー テキスト" 列など) に同じような値が多数含まれており、複合グループ キーの一意の値ごとに 1 回、その列をサンプリングする場合などに便利です。 

**例**  

```

traces 
| where timestamp > now(-15min)  
| summarize count(), any(message) by operation_Name 
| top 10 by count_level desc 
```

<a name="argmin"></a>
<a name="argmax"></a>

### <a name="argmin-argmax"></a>argmin、argmax
    argmin(ExprToMinimize, * | ExprToReturn  [ , ... ] )
    argmax(ExprToMaximize, * | ExprToReturn  [ , ... ] ) 

グループ内で最小化/最大化 (*ExprToMaximize*) する行を検索し、*ExprToReturn* の値を返します (`*` の場合は、行全体を返します)。

**ヒント**: パススルーされた列の名前は自動的に変更されます。 正しい名前を使用していることを確認するには、別の演算子に結果をパイプする前に `take 5` を使用して結果を調べます。

**例**

要求名ごとに、最長要求が発生した時間を表示する場合は、次のように指定します。

    requests | summarize argmax(duration, timestamp) by name

タイムスタンプだけでなく、最長要求の詳細をすべて表示する場合は、次のように指定します。

    requests | summarize argmax(duration, *) by name


タイムスタンプと他のデータと共に、各メトリックの最小値を検索する場合は、次のように指定します。

    metrics 
    | summarize minValue=argmin(value, *) 
      by name


![](./media/app-insights-analytics-reference/argmin.png)

### <a name="avg"></a>avg
    avg(Expression)

グループ全体の *式* の平均を計算します。

### <a name="buildschema"></a>buildschema
    buildschema(DynamicExpression)

*DynamicExpression*のすべての値を許可する最小スキーマを返します。 

パラメーターの列型は `dynamic` (配列またはプロパティ バッグ) である必要があります。 

**例**

    exceptions | summarize buildschema(details)

結果:

    { "indexer":
     {"id":"string",
       "parsedStack":
       { "indexer": 
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

数値インデックスを使用する必要がある場所をマークするために `indexer` が使用されていることに注目してください。 このスキーマでは、以下のように、いくつかの有効なパスがあります (この例のインデックスが範囲内にあると仮定した場合)。

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)

**例**

入力列に次の 3 つの動的値があるとします。

|  |
| --- |
| `{"x":1, "y":3.5}` |
| `{"x":"somevalue", "z":[1, 2, 3]}` |
| `{"y":{"w":"zzz"}, "t":["aa", "bb"], "z":["foo"]}` |

結果のスキーマは次のようになります。

    {
      "x":["int", "string"],
      "y":["double", {"w": "string"}],
      "z":{"indexer": ["int", "string"]},
      "t":{"indexer": "string"}
    }

スキーマの内容は以下のとおりです。

* ルート オブジェクトは、4 つのプロパティ (x、y、z、t) を含むコンテナーです。
* "x" プロパティは "int" 型または "string" 型のいずれかになります。
* "y" プロパティは "double" 型、または "string" 型の "w" プロパティを含む別のコンテナーになります。
* ``indexer`` キーワードは、"z" と "t" が配列であることを示します。
* "z" 配列の各項目は int または string のいずれかです。
* "t" は string の配列です。
* すべてのプロパティは暗黙的に省略可能で、配列は空である可能性があります。

##### <a name="schema-model"></a>スキーマ モデル
返されるスキーマの構文は次のとおりです。

    Container ::= '{' Named-type* '}';
    Named-type ::= (name | '"indexer"') ':' Type;
    Type ::= Primitive-type | Union-type | Container;
    Union-type ::= '[' Type* ']';
    Primitive-type ::= "int" | "string" | ...;

これらは TypeScript 型の注釈のサブセットと同等であり、動的値としてエンコードされます。 TypeScript のスキーマ例を以下に示します。

    var someobject:
    {
      x?: (number | string),
      y?: (number | { w?: string}),
      z?: { [n:number] : (int | string)},
      t?: { [n:number]: string }
    }


### <a name="count"></a>count
    count([ Predicate ])

*Predicate* が `true` と評価された行の数を返します。 *Predicate* が指定されていない場合は、グループ内のレコードの合計数を返します。 

**パフォーマンス ヒント**: `where filter | summarize count()` の代わりに `summarize count(filter)` を使用します。

> [!NOTE]
> 要求、例外、またはその他の発生したイベントの数を検索する場合は、count() を使用しないでください。 [サンプリング](app-insights-sampling.md) の実行中、Application Insights に保持されるデータ ポイントの数は、元のイベントの数より少なくなります。 代わりに `summarize sum(itemCount)...`を使用してください。 itemCount プロパティには、保持されている各データ ポイントで表される元のイベントの数が反映されます。
> 
> 

### <a name="countif"></a>countif
    countif(Predicate)

*Predicate* が `true` と評価された行の数を返します。

**パフォーマンス ヒント**: `where filter | summarize count()` の代わりに `summarize countif(filter)` を使用します。

> [!NOTE]
> 要求、例外、またはその他の発生したイベントの数を検索する場合は、countif() を使用しないでください。 [サンプリング](app-insights-sampling.md) の実行中のデータ ポイントの数は、実際のイベントの数より少なくなります。 代わりに `summarize sum(itemCount)...`を使用してください。 itemCount プロパティには、保持されている各データ ポイントで表される元のイベントの数が反映されます。
> 
> 

### <a name="dcount"></a>dcount
    dcount( Expression [ ,  Accuracy ])

グループ内にある*式*の個別の値の概数を返します。 (個別の値のリストを表示するには、[`makeset`](#makeset) を使用します)。

*Accuracy*を指定した場合は、速度と精度のバランスが制御されます。

* `0` = 精度は最も低くなりますが、計算速度は最高になります。
* `1` = 既定値です。精度と計算時間のバランスをとります。エラー率は約 0.8% です。
* `2` = 精度は最も高くなりますが、計算速度は最低になります。エラー率は約 0.4% です。

**例**

    pageViews 
    | summarize cities=dcount(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/dcount.png)

### <a name="dcountif"></a>dcountif
    dcountif( Expression, Predicate [ ,  Accuracy ])

*Predicate* が true であるグループ内にある行の*式*の個別の値の概数を返します  (個別の値のリストを表示するには、[`makeset`](#makeset) を使用します)。

*Accuracy*を指定した場合は、速度と精度のバランスが制御されます。

* `0` = 精度は最も低くなりますが、計算速度は最高になります。
* `1` = 既定値です。精度と計算時間のバランスをとります。エラー率は約 0.8% です。
* `2` = 精度は最も高くなりますが、計算速度は最低になります。エラー率は約 0.4% です。

**例**

    pageViews 
    | summarize cities=dcountif(client_City, client_City startswith "St") 
      by client_CountryOrRegion


### <a name="makelist"></a>makelist
    makelist(Expr [ ,  MaxListSize ] )

グループ内にある*式*のすべての値の `dynamic` (JSON) 配列を返します。 

* *MaxListSize* は、返される要素の最大数に対する整数制限です (省略可能、既定値は *128*)。

### <a name="makeset"></a>makeset
    makeset(Expression [ , MaxSetSize ] )

グループ内にある*式*で使用される個別の値セットの `dynamic` (JSON) 配列を返します  (ヒント: 単に個別の値をカウントする場合は、[`dcount`](#dcount) を使用します)。

* *MaxSetSize* は、返される要素の最大数に対する整数制限です (省略可能、既定値は *128*)。

**例**

    pageViews 
    | summarize cities=makeset(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/makeset.png)

逆の処理を実行する [`mvexpand` 演算子](#mvexpand-operator) も参照してください。

### <a name="max-min"></a>max、min
    max(Expr)

*式*の最大値を計算します。

    min(Expr)

*式*の最小値を計算します。

**ヒント**: これで最小値または最大値 (最高価格または最低価格など) をそれぞれ単独で計算できます。 ただし、行の他の列 (最低価格を提示するサプライヤーの名前など) が必要な場合は、 [argmin または argmax](#argmin-argmax)を使用します。

<a name="percentile"></a>
<a name="percentiles"></a>
<a name="percentilew"></a>
<a name="percentilesw"></a>

### <a name="percentile-percentiles-percentilew-percentilesw"></a>percentile、percentiles、percentilew、percentilesw
    percentile(Expression, Percentile)

グループ内にある指定されたパーセンタイルの *式* の推定値を返します。 精度は、パーセンタイル リージョンの人口密度によって異なります。

    percentiles(Expression, Percentile1 [ , Percentile2 ...] )

`percentile()`に似ていますが、複数のパーセンタイル値を計算します (各パーセンタイルを個別に計算するより高速)。

    percentilew(Expression, WeightExpression, Percentile)

重み付けされたパーセンタイルです。 事前に集計したデータに対して使用します。  `WeightExpression` は、集計済みの各行で表される元の行の数を指定する整数です。

    percentilesw(Expression, WeightExpression, Percentile1, [, Percentile2 ...])

`percentilew()`に似ていますが、複数のパーセンタイル値を計算します。

**例**

以下の場合、 `duration` の値は、要求名ごとに計算され、サンプル セットの 95% より大きく、サンプル セットの 5% より小さくなります。

    request 
    | summarize percentile(duration, 95)
      by name

"by..." を省略すると、テーブル全体に対して計算が行われます。

異なる要求名のいくつかのパーセンタイルを同時に計算する場合は以下のようになります。

    requests 
    | summarize 
        percentiles(duration, 5, 20, 50, 80, 95) 
      by name

![](./media/app-insights-analytics-reference/percentiles.png)

結果には、/Events/Index 要求の場合、要求の 5% の応答が 2.44 秒未満、50% の応答が 3.52 秒、5% が 6.85 秒より遅いことが示されています。

複数の統計値を計算する場合は次のようになります。

    requests 
    | summarize 
        count(), 
        avg(Duration),
        percentiles(Duration, 5, 50, 95)
      by name

#### <a name="weighted-percentiles"></a>重み付きパーセンタイル
重み付きパーセンタイルの関数は、データが事前に集計されている場合に使用します。 

たとえば、アプリケーションで 1 秒間に数千回の操作が行われており、それらの待機時間を知りたいとします。 単純な方法としては、各操作に対して Application Insights の要求またはカスタム イベントを生成することが考えられます。 この方法では、アダプティブ サンプリングにより緩和されるものの、大量のトラフィックが生成されます。 そこで、より良い解決策として、データを Application Insights に送信する前にアプリケーション内で集計するコードを作成してみましょう。 概要が定期的に集計されて送信されるため、データ レートが 1 秒あたり数ポイント程度に抑えられます。

このコードでは、待機時間の測定値 (ミリ秒単位) のストリームを受け入れます。 次に例を示します。

     { 15, 12, 2, 21, 2, 5, 35, 7, 12, 22, 1, 15, 18, 12, 26, 7 }

測定値は、 `{ 10, 20, 30, 40, 50, 100 }`

定期的に、バケットごとに対して一連の TrackEvent 呼び出しを作成し、各呼び出しにカスタムの測定値を含めます。 

    foreach (var latency in bins.Keys)
    { telemetry.TrackEvent("latency", null, 
         new Dictionary<string, double>
         ({"latency", latency}, {"opCount", bins[latency]}}); }

Analytics では、次のようなイベントのグループがあるとします。

| `opCount` | `latency` | 意味 |
| --- | --- | --- |
| 8 |10 |= 10 ミリ秒のビン含まれる操作は 8 件 |
| 6 |20 |= 20 ミリ秒のビン含まれる操作は 6 件 |
| 3 |30 |= 30 ミリ秒のビン含まれる操作は 3 件 |
| 1 |40 |= 40 ミリ秒のビン含まれる操作は 1 件 |

元のイベント待機時間の正確な分布図を得るには、 `percentilesw`を使用します。

    customEvents | summarize percentilesw(latency, opCount, 20, 50, 80)

結果は、元の測定値のセットに対して単純な `percentiles` を使用した場合と同じになります。

> [!NOTE]
> 重み付きパーセンタイルは[サンプリングされたデータ](app-insights-sampling.md)には適していません。この場合、サンプリングされた各行は、ビンではなく元の行のランダム サンプルを表すからです。 サンプリングされたデータには、単純なパーセンタイル関数が適しています。
> 
> 

#### <a name="estimation-error-in-percentiles"></a>パーセンタイル単位の推定エラー
パーセンタイル集計では、 [T-Digest](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf)を使用して概算値を算出できます。 

重要なポイントをいくつか以下に示します。 

* 推定エラーの限度は、要求されたパーセンタイルの値によって異なります。 [0..100] の範囲の両端にあるパーセンタイル 0 と 100 が分布値の正確な最小値および最大値になっているのが最適な精度です。 精度はスケールの中央に向かって徐々に低下します。 中央値が最低で、1% が上限となります。 
* エラー限度は、値ではなく、ランクで観測されます。 たとえば、percentile(X, 50) の場合は Xm の値が返されます。 推定では、X の値の 49% 以上 51% 以下が Xm 未満になることが保証されます。 Xm と X の実際の中央値との差には、理論上の制限はありません。

### <a name="stdev"></a>stdev
     stdev(Expr)

グループに対する *式* の標準偏差を返します。

### <a name="variance"></a>variance
    variance(Expr)

グループに対する *式* の分散を返します。

### <a name="sum"></a>sum
    sum(Expr)

グループに対する *式* の合計を返します。                      

## <a name="scalars"></a>スカラー
[キャスト](#casts) | [比較](#scalar-comparisons)
<br/>
[gettype](#gettype) | [hash](#hash) | [iff](#iff) |  [isnull](#isnull) | [isnotnull](#isnotnull) | [notnull](#notnull) | [toscalar](#toscalar)

サポートされている型は次のとおりです。

| 型 | その他の名前 | 同等の .NET 型 |
| --- | --- | --- |
| `bool` |`boolean` |`System.Boolean` |
| `datetime` |`date` |`System.DateTime` |
| `dynamic` | |`System.Object` |
| `guid` |`uuid`、`uniqueid` |`System.Guid` |
| `int` | |`System.Int32` |
| `long` | |`System.Int64` |
| `double` |`real` |`System.Double` |
| `string` | |`System.String` |
| `timespan` |`time` |`System.TimeSpan` |

### <a name="casts"></a>キャスト
ある型から別の型にキャストできます。 一般に、変換が理にかなっている場合はうまく動作します。

    todouble(10), todouble("10.6")
    toint(10.6) == 11
    floor(10.6) == 10
    toint("200")
    todatetime("2016-04-28 13:02")
    totimespan("1.5d"), totimespan("1.12:00:00")
    toguid("00000000-0000-0000-0000-000000000000")
    tostring(42.5)
    todynamic("{a:10, b:20}")

文字列を特定の型に変換できるかどうかを調べます。

    iff(notnull(todouble(customDimensions.myValue)),
       ..., ...)

### <a name="scalar-comparisons"></a>スカラーの比較
|  |  |
| --- | --- |
| `<` |小さい |
| `<=` |小さいまたは等しい |
| `>` |大きい |
| `>=` |大きいまたは等しい |
| `<>` |等しくない |
| `!=` |等しくない |
| `in` |右オペランドは (動的) 配列で、左オペランドがその要素のいずれかに等しい |
| `!in` |右オペランドは (動的) 配列で、左オペランドがその要素のいずれとも等しくない |

### <a name="gettype"></a>gettype
**戻り値**

単一の引数の基になるストレージ型を表す文字列。 これは、種類が `dynamic` の値がある場合に特に便利です。この場合、`gettype()` は値がどのようにエンコードされているかを示します。

**例**

|  |  |
| --- | --- |
| `gettype("a")` |`"string" ` |
| `gettype(111)` |`"long" ` |
| `gettype(1==1)` |`"int8"` |
| `gettype(now())` |`"datetime" ` |
| `gettype(1s)` |`"timespan" ` |
| `gettype(parsejson('1'))` |`"int" ` |
| `gettype(parsejson(' "abc" '))` |`"string" ` |
| `gettype(parsejson(' {"abc":1} '))` |`"dictionary"` |
| `gettype(parsejson(' [1, 2, 3] '))` |`"array"` |
| `gettype(123.45)` |`"real" ` |
| `gettype(guid(12e8b78d-55b4-46ae-b068-26d7a0080254))` |`"guid"` |
| `gettype(parsejson(''))` |`"null"` |
| `gettype(1.2)==real` |`true` |

### <a name="hash"></a>hash
**構文**

    hash(source [, mod])

**引数**

* *source*: ハッシュの計算の基となるソース スカラー。
* *mod*: ハッシュの結果に適用される剰余値。

**戻り値**

指定したスカラーの xxhash (long) 値。特定の mod 値が指定されている場合は、その剰余。

**例**

```
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```
### <a name="iff"></a>iff
`iff()` 関数は、最初の引数 (述語) を評価し、述語が `true` であるか `false` であるかに応じて、2 番目または 3 番目の引数の値を返します。 2 番目と 3 番目の引数は、同じ型である必要があります。

**構文**

    iff(predicate, ifTrue, ifFalse)


**引数**

* *predicate:* `boolean` 値に評価される式。
* *ifTrue:* *predicate* が `true` と評価された場合に、評価されてその値が関数から返される式。
* *ifFalse:* *predicate* が `false` と評価された場合に、評価されてその値が関数から返される式。

**戻り値**

この関数は、*predicate* が `true` と評価された場合に *ifTrue* の値を返し、それ以外の場合に *ifFalse* の値を返します。

**例**

```
iff(floor(timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```

<a name="isnull"/></a>
<a name="isnotnull"/></a>
<a name="notnull"/></a>

### <a name="isnull-isnotnull-notnull"></a>isnull、isnotnull、notnull
    isnull(parsejson("")) == true

1 つの引数を受け取り、null かどうかを判定します。

**構文**

    isnull([value])


    isnotnull([value])


    notnull([value])  // alias for isnotnull

**戻り値**

値が null かどうかに応じて、True または false を返します。

| ○ | isnull(x) |
| --- | --- |
| "" |false |
| "x" |false |
| parsejson("") |true |
| parsejson("[]") |false |
| parsejson("{}") |false |

**例**

    T | where isnotnull(PossiblyNull) | count

上記の結果を得る方法はほかにもあることに注意してください。

    T | summarize count(PossiblyNull)

### <a name="toscalar"></a>toscalar
クエリまたは式を評価し、結果を単一の値として返します。 この関数は段階的な計算に便利です。たとえば、イベントの総数を計算した後で、それをベースラインとして使用する場合などです。

**構文**

    toscalar(query)
    toscalar(scalar)

**戻り値**

評価された引数。 引数がテーブルの場合は、最初の行の最初の列を返します (引数に列と行がそれぞれ 1 つだけ含まれるように調整することをお勧めします)。

**例**

```AIQL

    // Get the count of requests 5 days ago:
    let baseline = toscalar(requests  
        | where floor(timestamp, 1d) == floor(ago(5d),1d) | count);
    // List the counts relative to that baseline:
    requests | summarize daycount = count() by floor(timestamp, 1d)  
    | extend relative = daycount - baseline
```




### <a name="boolean-literals"></a>ブール型リテラル
    true == 1
    false == 0
    gettype(true) == "int8"
    typeof(bool) == typeof(int8)

### <a name="boolean-operators"></a>ブール演算子
    and 
    or 

### <a name="convert-to-boolean"></a>ブール値への変換

値 "true" または "false" を含む文字列 `aStringBoolean` がある場合、次のようにしてブール値に変換できます。

    booleanResult = aStringBoolean =~ "true"



## <a name="numbers"></a>数値
[abs](#abs) | [bin](#bin) | [exp](#exp) | [floor](#floor) | [gamma](#gamma) |[log](#log) | [rand](#rand) | [range](#range) | [sqrt](#sqrt) 
| [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

### <a name="numeric-literals"></a>数値リテラル
|  |  |
| --- | --- |
| `42` |`long` |
| `42.0` |`real` |

### <a name="arithmetic-operators"></a>算術演算子
|  |  |
| --- | --- |
| + |Add |
| - |減算 |
| * |乗算 |
| / |/ (除算) |
| % |剰余 |
| `<` |小さい |
| `<=` |小さいまたは等しい |
| `>` |大きい |
| `>=` |大きいまたは等しい |
| `<>` |等しくない |
| `!=` |等しくない |

### <a name="abs"></a>abs
**構文**

    abs(x)

**引数**

* x - 整数、実数、または期間

**戻り値**

    iff(x>0, x, -x)

<a name="bin"></a><a name="floor"></a>

### <a name="bin-floor"></a>bin、floor
値を切り捨てて、指定された bin サイズの倍数である整数にします。 [`summarize by`](#summarize-operator) クエリでよく使用されます。 値が分散している場合に、特定の値ごとの小さなセットにグループ化されます。

エイリアス `floor`。

**構文**

     bin(value, roundTo)
     floor(value, roundTo)

**引数**

* *value:* 数値、日付、または期間。 
* *roundTo:* "bin のサイズ"。 *value*を分割する数値、日付、または期間です。 

**戻り値**

*value* 未満で、*roundTo* の最も近い倍数。  

    (toint(value/roundTo)) * roundTo

**例**

| 式 | 結果 |
| --- | --- |
| `bin(4.5, 1)` |`4.0` |
| `bin(time(16d), 7d)` |`14d` |
| `bin(datetime(1953-04-15 22:25:07), 1d)` |`datetime(1953-04-15)` |

次の式は、バケットのサイズを 1 秒として、各期間のヒストグラムを計算します。

```AIQL

    T | summarize Hits=count() by bin(Duration, 1s)
```

### <a name="exp"></a>exp
    exp(v)   // e raised to the power v
    exp2(v)  // 2 raised to the power v
    exp10(v) // 10 raised to the power v


### <a name="floor"></a>floor
[`bin()`](#bin)のエイリアス。

### <a name="gamma"></a>gamma
[gamma 関数](https://en.wikipedia.org/wiki/Gamma_function)

**構文**

    gamma(x)

**引数**

* *x:* 実数

正の整数の場合、`gamma(x) == (x-1)!` のように指定します。例: `gamma(5) == 4 * 3 * 2 * 1`。

[loggamma](#loggamma)も参照してください。

### <a name="log"></a>log
    log(v)    // Natural logarithm of v
    log2(v)   // Logarithm base 2 of v
    log10(v)  // Logarithm base 10 of v


`v` は 0 を超える実数である必要があります。 それ以外の場合、null が返されます。

### <a name="loggamma"></a>loggamma
[関数](#gamma)の絶対値の自然対数。

**構文**

    loggamma(x)

**引数**

* *x:* 実数

### <a name="rand"></a>rand
乱数ジェネレーター。

* `rand()` - 0.0 ～ 1.0 の範囲の実数
* `rand(n)` - 0 ～ n-1 の範囲の整数

### <a name="sqrt"></a>sqrt
平方根関数。  

**構文**

    sqrt(x)

**引数**

* *x:* 0 以上の実数。

**戻り値**

* `sqrt(x) * sqrt(x) == x`
* 引数が負であるか、`real` 値に変換できない場合は `null`。 

### <a name="toint"></a>toint
    toint(100)        // cast from long
    toint(20.7) == 20 // nearest int below double
    toint(20.4) == 20 // nearest int below double
    toint("  123  ")  // parse string
    toint(a[0])       // cast from dynamic
    toint(b.c)        // cast from dynamic


### <a name="todouble"></a>todouble
    todouble(20) == 20.0 // conversion from long or int
    todouble(" 12.34 ")  // parse string
    todouble(a[0])       // cast from dynamic
    todouble(b.c)        // cast from dynamic


### <a name="tolong"></a>tolong
    tolong(20.7) == 20 // conversion from double
    tolong(20.4) == 20 // conversion from double
    tolong("  123  ")  // parse string
    tolong(a[0])       // cast from dynamic
    tolong(b.c)        // cast from dynamic


## <a name="date-and-time"></a>日付と時刻
[ago](#ago) | [dayofmonth](#dayofmonth) | [dayofweek](#dayofweek) |  [dayofyear](#dayofyear) |[datepart](#datepart) | [endofday](#endofday) | [endofmonth](#endofmonth) | [endofweek](#endofweek) | [endofyear](#endofyear) | [getmonth](#getmonth)|  [getyear](#getyear) | [now](#now) | [startofday](#startofday) | [startofmonth](#startofmonth) | [startofweek](#startofweek) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan) | [weekofyear](#weekofyear)

### <a name="date-and-time-literals"></a>日付と時刻のリテラル
|  |  |
| --- | --- |
| **datetime** | |
| `datetime("2015-12-31 23:59:59.9")`<br/>`datetime("2015-12-31")` |時刻は常に UTC 形式です。 日付を省略すると、今日の時刻になります。 |
| `now()` |現在の時刻。 |
| `now(`-*timespan*`)` |`now()-`*timespan* |
| `ago(`*timespan*`)` |`now()-`*timespan* |
| **timespan** | |
| `2d` |2 日 |
| `1.5h` |1.5 時間 |
| `30m` |30 分 |
| `10s` |10 秒 |
| `0.1s` |0.1 秒 |
| `100ms` |100 ミリ秒 |
| `10microsecond` | |
| `1tick` |100 ナノ秒 |
| `time("15 seconds")` | |
| `time("2")` |2 日 |
| `time("0.12:34:56.7")` |`0d+12h+34m+56.7s` |

### <a name="date-and-time-expressions"></a>日付と時刻の式
| 式 | 結果 |
| --- | --- |
| `datetime("2015-01-02") - datetime("2015-01-01")` |`1d` |
| `datetime("2015-01-01") + 1d` |`datetime("2015-01-02")` |
| `datetime("2015-01-01") - 1d` |`datetime("2014-12-31")` |
| `2h * 24` |`2d` |
| `2d` / `2h` |`24` |
| `datetime("2015-04-15T22:33") % 1d` |`timespan("22:33")` |
| `bin(datetime("2015-04-15T22:33"), 1d)` |`datetime("2015-04-15T00:00")` |
|  | |
| `<` |小さい |
| `<=` |小さいまたは等しい |
| `>` |大きい |
| `>=` |大きいまたは等しい |
| `<>` |等しくない |
| `!=` |等しくない |

### <a name="ago"></a>ago
現在の UTC 時刻から指定された期間を減算します。 `now()` と同様、この関数はステートメント内で複数回使用することができます。また、参照される UTC 時刻はすべてのインスタンスで同じになります。

**構文**

    ago(a_timespan)

**引数**

* *a_timespan*: 現在の UTC 時刻 (`now()`) から減算する期間。

**戻り値**

    now() - a_timespan

**例**

過去 1 時間以内のタイムスタンプを持つすべての行は、次のようになります。

```AIQL

    T | where timestamp > ago(1h)
```

### <a name="datepart"></a>datepart
    datepart("Day", datetime(2015-12-14)) == 14

日付の指定された部分を整数として抽出します。

**構文**

    datepart(part, datetime)

**引数**

* `part:String` - {"Year", "Month", "Day", "Hour", "Minute", "Second", "Millisecond", "Microsecond", "Nanosecond"}
* `datetime`

**戻り値**

指定された部分を表す長整数型の値。

### <a name="dayofmonth"></a>dayofmonth
    dayofmonth(datetime("2016-05-15")) == 15 

月初から数えた日の序数。

**構文**

    dayofmonth(a_date)

**引数**

* `a_date`: `datetime`。

### <a name="dayofweek"></a>dayofweek
    dayofweek(datetime("2015-12-14")) == 1d  // Monday

前の日曜日からの日数を、 `timespan`として示す整数。

**構文**

    dayofweek(a_date)

**引数**

* `a_date`: `datetime`。

**戻り値**

前の日曜日の午前 0 時からの `timespan`。日数を示す整数に切り捨てられます。

**例**

```AIQL
dayofweek(1947-11-29 10:00:05)  // time(6.00:00:00), indicating Saturday
dayofweek(1970-05-11)           // time(1.00:00:00), indicating Monday
```

### <a name="dayofyear"></a>dayofyear
    dayofyear(datetime("2016-05-31")) == 152 
    dayofyear(datetime("2016-01-01")) == 1 

年初から数えた日の序数。

**構文**

    dayofyear(a_date)

**引数**

* `a_date`: `datetime`。

<a name="endofday"></a><a name="endofweek"></a><a name="endofmonth"></a><a name="endofyear"></a>

### <a name="endofday-endofweek-endofmonth-endofyear"></a>endofday、endofweek、endofmonth、endofyear
    dt = datetime("2016-05-23 12:34")

    endofday(dt) == 2016-05-23T23:59:59.999
    endofweek(dt) == 2016-05-28T23:59:59.999 // Saturday
    endofmonth(dt) == 2016-05-31T23:59:59.999 
    endofyear(dt) == 2016-12-31T23:59:59.999 


### <a name="getmonth"></a>getmonth
datetime から月 (1 ～ 12) を取得します。

**例**

    ... | extend month = getmonth(datetime(2015-10-12))

    --> month == 10

### <a name="getyear"></a>getyear
datetime から年を取得します。

**例**

    ... | extend year = getyear(datetime(2015-10-12))

    --> year == 2015

### <a name="now"></a>now
    now()
    now(-2d)

現在の UTC 時刻。オプションで、オフセットの期間を指定できます。 この関数はステートメント内で複数回使用することができます。また、参照される時刻はすべてのインスタンスで同じになります。

**構文**

    now([offset])

**引数**

* *offset:* 現在の UTC 時刻に加算される `timespan`。 既定値: 0。

**戻り値**

`datetime`型の現在の UTC 時刻。

    now() + offset

**例**

述語によって特定されるイベント以降の期間を判断します。

```AIQL
T | where ... | extend Elapsed=now() - timestamp
```

<a name="startofday"></a><a name="startofweek"></a><a name="startofmonth"></a><a name="startofyear"></a>

### <a name="startofday-startofweek-startofmonth-startofyear"></a>startofday、startofweek、startofmonth、startofyear
    date=datetime("2016-05-23 12:34:56")

    startofday(date) == datetime("2016-05-23")
    startofweek(date) == datetime("2016-05-22") // Sunday
    startofmonth(date) == datetime("2016-05-01")
    startofyear(date) == datetime("2016-01-01")



### <a name="todatetime"></a>todatetime
エイリアス `datetime()`。

     todatetime("2016-03-28")
     todatetime("03/28/2016")
     todatetime("2016-03-28 14:34:00")
     todatetime("03/28/2016 2:34pm")
     todatetime("2016-03-28T14:34.5Z")
     todatetime(a[0]) 
     todatetime(b.c) 

文字列が有効な日付であるかどうかを確認します。

     iff(notnull(todatetime(customDimensions.myDate)),
         ..., ...)


### <a name="totimespan"></a>totimespan
エイリアス `timespan()`。

    totimespan("21d")
    totimespan("21h")
    totimespan(request.duration)

### <a name="weekofyear"></a>weekofyear
    weekofyear(datetime("2016-05-14")) == 21
    weekofyear(datetime("2016-01-03")) == 1
    weekofyear(datetime("2016-12-31")) == 53

整数の結果は、ISO 8601 標準での週数を表します。 週の最初の曜日は日曜日で、年の最初の週はその年の最初の木曜日を含む週です (したがって、年の最後の数日に次の年の第 1 週の数日が含まれるか、年の最初の数日に前の年の第 52 週と第 53 週の数日が含まれる可能性があります)。

## <a name="string"></a>String
[countof](#countof) | [extract](#extract) | [extractjson](#extractjson)  | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty) | [parseurl](#parseurl) | [replace](#replace) | [split](#split) | [strcat](#strcat) | [strlen](#strlen) | [substring](#substring) | [tolower](#tolower) | [tostring](#tostring) | [toupper](#toupper)

### <a name="string-literals"></a>文字列リテラル
規則は JavaScript の場合と同様です。

文字列は、単一引用符または二重引用符で囲むことがあります。 

`\t` (タブ)、`\n` (改行)、文字列を囲む引用符などの文字をエスケープするには、バックスラッシュ (`\`) を使用します。

* `'this is a "string" literal in single \' quotes'`
* `"this is a 'string' literal in double \" quotes"`
* `@"C:\backslash\not\escaped\with @ prefix"`

### <a name="obfuscated-string-literals"></a>難読化された文字列リテラル
難読化された文字列リテラルとは、文字列の出力時 (たとえば、トレース時) に Analytics によってわかりにくくされる文字列のことです。 難読化プロセスでは、難読化されるすべての文字がアスタリスク (`*`) 記号に置き換えられます。

難読化された文字列リテラルを形成するには、前に `h` または "H" を付加します。 次に例を示します。

```
h'hello'
h@'world' 
h"hello"
```

### <a name="string-comparisons"></a>文字列の比較
| 演算子 | Description | 大文字と小文字の区別 | 実際の例 |
| --- | --- | --- | --- |
| `==` |等しい |はい |`"aBc" == "aBc"` |
| `<>` `!=` |等しくない |はい |`"abc" <> "ABC"` |
| `=~` |等しい |いいえ |`"abc" =~ "ABC"` <br/>`boolAsString =~ "true"` |
| `!~` |等しくない |いいえ |`"aBc" !~ "xyz"` |
| `has` |右辺 (RHS) が左辺 (LHS) に 1 つの単語として含まれる |いいえ |`"North America" has "america"` |
| `!has` |RHS が LHS に完全な単語として含まれない |いいえ |`"North America" !has "amer"` |
| `hasprefix` |RHS が LHS に単語のプレフィックスとして含まれる |いいえ |`"North America" hasprefix "ame"` |
| `!hasprefix` |RHS が LHS にどの単語のプレフィックスとしても含まれない |いいえ |`"North America" !hasprefix "mer"` |
| `hassuffix` |RHS が LHS に単語のサフィックスとして含まれる |いいえ |`"North America" hassuffix "rth"` |
| `!hassuffix` |RHS が LHS にどの単語のサフィックスとしても含まれない |いいえ |`"North America" !hassuffix "mer"` |
| `contains` |RHS が LHS の部分文字列として出現する |いいえ |`"FabriKam" contains "BRik"` |
| `!contains` |RHS が LHS 内に出現しない |いいえ |`"Fabrikam" !contains "xyz"` |
| `containscs` |RHS が LHS の部分文字列として出現する |はい |`"FabriKam" contains "Kam"` |
| `!containscs` |RHS が LHS 内に出現しない |はい |`"Fabrikam" !contains "Kam"` |
| `startswith` |RHS が LHS の先頭の部分文字列である |いいえ |`"Fabrikam" startswith "fab"` |
| `!startswith` |RHS が LHS の先頭の部分文字列ではない |いいえ |`"Fabrikam" !startswith "abr"` |
| `endswith` |RHS が LHS の末尾の部分文字列である |いいえ |`"Fabrikam" endswith "kam"` |
| `!endswith` |RHS が LHS の末尾の部分文字列ではない |いいえ |`"Fabrikam" !endswith "ka"` |
| `matches regex` |LHS には RHS との一致が含まれている |はい |`"Fabrikam" matches regex "b.*k"` |
| [`in`](#in) |要素のいずれかに等しい |はい |`"abc" in ("123", "345", "abc")` |
| [`!in`](#in) |要素のいずれとも等しくない |はい |`"bc" !in ("123", "345", "abc")` |

語彙全体があるかどうかをテストする場合は、`has` または `in` を使用します。語彙全体とは、非英数字またはフィールドの先頭か末尾によって囲まれた、記号または英数字から成る単語を意味します。 `has` は、`contains`、`startswith`、または `endswith` より速く動作します。 以下のクエリでは、最初の方が処理速度は速くなります。

    EventLog | where continent has "North" | count;
    EventLog | where continent contains "nor" | count





### <a name="countof"></a>countof
    countof("The cat sat on the mat", "at") == 3
    countof("The cat sat on the mat", @"\b.at\b", "regex") == 3

文字列内の部分文字列の出現回数をカウントします。 プレーン文字列一致では、重なり合う可能性があります。正規表現では、重なり合いません。

**構文**

    countof(text, search [, kind])

**引数**

* *text:* 文字列。
* *search:* *text* 内で照合するプレーン文字列または正規表現。
* *kind:* `"normal"|"regex"`。既定では `normal`。 

**戻り値**

コンテナー内で検索文字列が一致する回数。 プレーン文字列一致では、重なり合う可能性があります。正規表現では、重なり合いません。

**例**

|  |  |
| --- | --- |
| `countof("aaa", "a")` |3 |
| `countof("aaaa", "aa")` |3 (2 ではありません) |
| `countof("ababa", "ab", "normal")` |2 |
| `countof("ababa", "aba")` |2 |
| `countof("ababa", "aba", "regex")` |1 |
| `countof("abcabc", "a.c", "regex")` |2 |

### <a name="extract"></a>extract
    extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"

テキスト文字列から [正規表現](#regular-expressions) との一致を取得します。 必要に応じて、抽出された部分文字列を、指定された型に変換することもできます。

**構文**

    extract(regex, captureGroup, text [, typeLiteral])

**引数**

* *regex:*[正規表現](#regular-expressions)。
* *captureGroup:* 抽出するキャプチャ グループを示す、正の `int` 定数。 0 は一致全体、1 は正規表現の最初のかっこで囲まれた部分と一致した値、2 以上は後続のかっこを示します。
* *text:*  `string` 。
* *typeLiteral:* オプションの type リテラル (例: `typeof(long)`)。 指定した場合、抽出された部分文字列はこの型に変換されます。 

**戻り値**

*regex* が *text* 内で一致を見つけた場合: 指定されたキャプチャ グループ *captureGroup* に対応する部分文字列。オプションで、*typeLiteral* に変換できます。

一致がないか、型変換が失敗した場合: `null`。 

**例**

サンプル文字列 `Trace` で `Duration` の定義を検索します。 一致は `real` に変換された後、時間定数 (`1s`) で乗算されて、`Duration` は `timespan` 型になります。 この例では、123.45 秒と等しくなります。

```AIQL
...
| extend Trace="A=1, B=2, Duration=123.45, ..."
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s) 
```

次の例は、 `substring(Text, 2, 4)`に相当します。

```AIQL
extract("^.{2,2}(.{4,4})", 1, Text)
```

<a name="notempty"></a>
<a name="isnotempty"></a>
<a name="isempty"></a>



### <a name="isempty-isnotempty-notempty"></a>isempty、isnotempty、notempty
    isempty("") == true

引数が空の文字列または null である場合は True です。
[isnull](#isnull)も参照してください。

**構文**

    isempty([value])


    isnotempty([value])


    notempty([value]) // alias of isnotempty

**戻り値**

引数が空の文字列または null であるかどうかを示します。

| ○ | isempty(x) |
| --- | --- |
| "" |true |
| "x" |false |
| parsejson("") |true |
| parsejson("[]") |false |
| parsejson("{}") |false |

**例**

    T | where isempty(fieldName) | count


### <a name="parseurl"></a>parseurl
URL を各部分に分割します。

**構文**

    parseurl(urlstring)

**引数**

* *urlstring:* URL。

**戻り値**

文字列として各部分を含むオブジェクト。

**例**

    parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")

    {
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
    }

### <a name="replace"></a>replace
正規表現のすべての一致を別の文字列に置き換えます。

**構文**

    replace(regex, rewrite, text)

**引数**

* *regex:* [text](https://github.com/google/re2/wiki/Syntax) の検索に使用する*正規表現*。 複数のキャプチャ グループをかっこ内に含めることができます。 
* *rewrite:* *matchingRegex* によるすべての一致を置き換える正規表現。 完全一致を参照する場合は `\0`、最初のキャプチャ グループの場合は `\1`、後続のキャプチャ グループの場合は `\2` などを使用します。
* *text:* 文字列。

**戻り値**

*regex* のすべての一致を *rewrite* の評価で置き換えた後の *text*。 一致が重なり合うことはありません。

**例**

次のステートメントを実行します。

```AIQL
range x from 1 to 5 step 1
| extend str=strcat('Number is ', tostring(x))
| extend replaced=replace(@'is (\d+)', @'was: \1', str)
```

結果は次のとおりです。

| ○ | str | replaced |
| --- | --- | --- |
| 1 |Number is 1.000000 |Number was: 1.000000 |
| 2 |Number is 2.000000 |Number was: 2.000000 |
| 3 |Number is 3.000000 |Number was: 3.000000 |
| 4 |Number is 4.000000 |Number was: 4.000000 |
| 5 |Number is 5.000000 |Number was: 5.000000 |

### <a name="split"></a>split
    split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]

指定された区切り記号に従って、指定された文字列を分割し、部分文字列が含まれている文字列配列を返します。 必要に応じて、特定の部分文字列が存在すれば、それを返すこともできます。

**構文**

    split(source, delimiter [, requestedIndex])

**引数**

* *source*: 指定された区切り記号に従って分割されるソース文字列。
* *delimiter*: ソース文字列を分割するために使用される区切り記号。
* *requestedIndex*: 0 から始まるインデックス `int` (省略可能)。 指定した場合、返される文字列配列には、要求した部分文字列が含まれます (存在する場合)。 

**戻り値**

指定された区切り記号によって分割された、指定されたソース文字列の部分文字列が含まれている文字列配列。

**例**

```
split("aa_bb", "_")           // ["aa","bb"]
split("aaa_bbb_ccc", "_", 1)  // ["bbb"]
split("", "_")                // [""]
split("a__b")                 // ["a","","b"]
split("aabbcc", "bb")         // ["aa","cc"]
```




### <a name="strcat"></a>strcat
    strcat("hello", " ", "world")

1 ～ 16 個の引数を連結します。引数は文字列である必要があります。

### <a name="strlen"></a>strlen
    strlen("hello") == 5

文字列の長さ。

### <a name="substring"></a>substring
    substring("abcdefg", 1, 2) == "bc"

指定されたソース文字列の指定されたインデックス位置から部分文字列を抽出します。 必要に応じて、要求する部分文字列の長さを指定できます。

**構文**

    substring(source, startingIndex [, length])

**引数**

* *source:* 部分文字列の取得元となるソース文字列。
* *startingIndex:* 要求する部分文字列の、0 から始まる開始文字位置。
* *length:* 要求する部分文字列の文字数を指定するために使用できる省略可能なパラメーター。 

**戻り値**

指定した文字列の部分文字列。 部分文字列は、startingIndex の (0 から始まる) 文字位置から始まり、文字列の末尾か、指定した文字数になるまで続きます。

**例**

```
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
```

### <a name="tolower"></a>tolower
    tolower("HELLO") == "hello"

文字列を小文字に変換します。

### <a name="toupper"></a>toupper
    toupper("hello") == "HELLO"

文字列を大文字に変換します。

### <a name="guids"></a>GUID
    guid(00000000-1111-2222-3333-055567f333de)


## <a name="arrays-objects-and-dynamic"></a>配列、オブジェクト、動的
[リテラル](#dynamic-literals) | [キャスト](#casting-dynamic-objects) | [演算子](#operators) | [let 句](#dynamic-objects-in-let-clauses)
<br/>
[arraylength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [range](#range) | [treepath](#treepath) | [todynamic](#todynamic) | [zip](#zip)

Application Insights の例外に対するクエリの結果を次に示します。 `details` の値は配列です。

![](./media/app-insights-analytics-reference/310.png)

**インデックス作成:** JavaScript と同様に、配列やオブジェクトのインデックスを作成できます。

    exceptions | take 1
    | extend 
        line = details[0].parsedStack[0].line,
        stackdepth = arraylength(details[0].parsedStack)

* ただし、 `arraylength` やその他の Analytics 関数 (".length" 以外) を使用してください。

**キャスト:** 場合によっては、オブジェクトから抽出する要素をキャストする必要があります。これは、オブジェクトの型が一様ではないためです。 たとえば、`summarize...to` には次のように特定の型が必要です。

    exceptions 
    | summarize count() 
      by toint(details[0].parsedStack[0].line)

    exceptions
    | summarize count()
      by tostring(details[0].parsedStack[0].assembly)

**リテラル:** 明示的な配列またはプロパティ バッグ オブジェクトを作成するには、次のように、JSON 文字列として記述し、キャストします。

    todynamic('[{"x":"1", "y":"32"}, {"x":"6", "y":"44"}]')


**mvexpand:** オブジェクトのプロパティを個々の行に分解するには、次のように、mvexpand を使用します。

    exceptions | take 1
    | mvexpand details[0].parsedStack[0]


![](./media/app-insights-analytics-reference/410.png)

**treepath:** 複合オブジェクト内のすべてのパスを検索するには、次のようにします。

    exceptions | take 1 | project timestamp, details
    | extend path = treepath(details)
    | mvexpand path


![](./media/app-insights-analytics-reference/420.png)

**buildschema:** テーブル内の式のすべての値を受け入れる最小限のスキーマを見つけるには、次のようにします。

    exceptions | summarize buildschema(details)

結果:

    { "indexer":
     {"id":"string",
       "parsedStack":
       { "indexer":
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

数値インデックスを使用する必要がある場所をマークするために `indexer` が使用されていることに注目してください。 このスキーマでは、以下のように、いくつかの有効なパスがあります (この例のインデックスが範囲内にあると仮定した場合)。

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)



### <a name="array-and-object-literals"></a>配列とオブジェクトのリテラル
動的リテラルを作成するには、次のように、JSON 文字列引数を指定した `parsejson` (エイリアスは `todynamic`) を使用します。

* `parsejson('[43, 21, 65]')` - 数値の配列
* `parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')`
* `parsejson('21')` - 数値を示す、動的な型の単一の値
* `parsejson('"21"')` - 文字列を示す、動的な型の単一の値

JavaScript とは異なり、JSON では文字列の前後で二重引用符 (`"`) の使用が必須であることに注意してください。 そのため、一般的に、JSON 形式でエンコードされた文字列リテラルを単一引用符 (`'`) で囲む方がより簡単です。

この例では、動的な値を作成した後、そのフィールドを使用します。

```

T
| extend person = parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')
| extend n = person.name, add = person.address.street
```


### <a name="dynamic-object-functions"></a>動的オブジェクトの関数
|  |  |
| --- | --- |
| [*value* `in` *array*](#in) |*array* に *value* が含まれています。 |
| [*value* `!in` *array*](#in) |*array* に *value* が含まれていません。 |
| [`arraylength(`array`)`](#arraylength) |配列でない場合は null。 |
| [`extractjson(`path,object`)`](#extractjson) |オブジェクトに移動するためのパスを使用します。 |
| [`parsejson(`source`)`](#parsejson) |JSON 文字列を動的オブジェクトに変換します。 |
| [`range(`from,to,step`)`](#range) |値の配列。 |
| [`mvexpand` listColumn](#mvexpand-operator) |リスト内の指定されたセルの各値に対して、行を複製します。 |
| [`summarize buildschema(`column`)`](#buildschema) |列の内容から型スキーマを推測します。 |
| [`summarize makelist(`column`)` ](#makelist) |行のグループをフラット化し、列の値を配列に格納します。 |
| [`summarize makeset(`column`)`](#makeset) |行のグループをフラット化し、列の値を重複しないように配列に格納します。 |

### <a name="dynamic-objects-in-let-clauses"></a>let 句の動的オブジェクト
[let 句](#let-clause)には動的な値が文字列として格納されるため、以下の 2 つの句は同等で、どちらも使用前に `parsejson` (または`todynamic` ) が必要です。

    let list1 = '{"a" : "somevalue"}';
    let list2 = parsejson('{"a" : "somevalue"}');

    T | project parsejson(list1).a, parsejson(list2).a


### <a name="in"></a>という名前で、
    value in (listExpression)
    value !in (listExpression)

値に等しい項目がリストにあるかどうかを判断します。 大文字と小文字が区別されます。値は文字列です。

**引数**

* `value`: スカラー式。
* `listExpression`...: スカラー式のリスト、またはリストに評価される式。 

入れ子になった配列は、1 つのリストにフラット化されます。たとえば、`where x in (dynamic([1,[2,3]]))` は `where x in (1,2,3)` になります。  

**例**

```AIQL
    requests | where client_City in ("London", "Paris", "Rome")
```

```AIQL
let cities = dynamic(['Dublin','Redmond','Amsterdam']);
requests | where client_City in (cities) 
|  summarize count() by client_City
```

計算されるリスト:

```AIQL
let topCities =  toscalar ( // convert single column to value
   requests
   | summarize count() by client_City 
   | top 4 by count_ 
   | summarize makeset(client_City)) ;
requests
| where client_City in (topCities) 
| summarize count() by client_City;
```

リストの式として関数呼び出しを使用:

```AIQL
let topCities =  (n:int) {toscalar (
   requests
   | summarize count() by client_City 
   | top n by count_ 
   | summarize makeset(client_City)) };
requests
| where client_City in (topCities(3)) 
| summarize count() by client_City;
```
 

### <a name="arraylength"></a>arraylength
動的配列内の要素数。

**構文**

    arraylength(array)

**引数**

* *array:* `dynamic` 値。

**戻り値**

*array* 内の要素数。*array* が配列ではない場合は `null`。

**例**

```
arraylength(parsejson('[1, 2, 3, "four"]')) == 4
arraylength(parsejson('[8]')) == 1
arraylength(parsejson('[{}]')) == 1
arraylength(parsejson('[]')) == 0
arraylength(parsejson('{}')) == null
arraylength(parsejson('21')) == null
```



### <a name="extractjson"></a>extractjson
    extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))

パス式を使用している JSON テキストから、指定された要素を取得します。 必要に応じて、抽出した文字列を特定の型に変換することもできます。

**構文**

```

    string extractjson(jsonPath, dataSource) 
    resulttype extractjson(jsonPath, dataSource, typeof(resulttype))
```


**戻り値**

この関数は、有効な JSON 文字列が含まれている dataSource への JsonPath クエリを実行します。オプションで、その値を 3 番目の引数に基づいて他の型に変換することもできます。

**例**

次のように、角かっこ ([]) 表記とドット表記は同等です。

    ... | extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) | ...

    ... | extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) | ...



**パフォーマンスに関するヒント**

* `extractjson()`
* 代わりに、 [extract](#extract) による正規表現の一致を使用することを検討してください。 こちらの方が実行速度が非常に速く、JSON がテンプレートから生成される場合に効率的です。
* JSON から複数の値を抽出する必要がある場合は、 `parsejson()` を使用してください。
* 列の型が動的になるように宣言することによって、取り込み時に JSON が解析されるようにすることを検討してください。

### <a name="json-path-expressions"></a>JSON パス式
|  |  |
| --- | --- |
| `$` |ルート オブジェクト |
| `@` |現在のオブジェクト |
| `[0]` |配列インデックス |
| `.` または `[0]` |子 |

*(現在、ワイルドカード、再帰、共用体、およびスライスは実装していません。)*

### <a name="parsejson"></a>parsejson
`string` を [JSON 値](http://json.org/)として解釈し、値を `dynamic` として返します。 JSON 複合オブジェクトの複数の要素を抽出する必要がある場合は、 `extractjson()` を使用する方が適しています。

**構文**

    parsejson(json)

**引数**

* *json:* JSON ドキュメント。

**戻り値**

*json* によって指定された、`dynamic` 型のオブジェクト。

**例**

次の例では、`context_custom_metrics` が `string` である場合、次のようになります。 

```
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

その後、次のフラグメントがオブジェクトの `duration` スロットの値を取得し、そこから 2 つのスロット `duration.value` および  `duration.min` (それぞれ `118.0` と `110.0`) を取得します。

```AIQL
T
| ...
| extend d=parsejson(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```



### <a name="range"></a>range
`range()` 関数 (`range` 演算子と混同しないようにしてください) は、一連の等間隔の値を保持する動的配列を生成します。

**構文**

    range(start, stop, step)

**引数**

* *start:* 結果として生成される配列内の最初の要素の値。 
* *stop:* 結果として生成される配列内の最後の要素の値。または、生成される配列内の最後の要素より大きく、*start* からの *step* の倍数である整数に含まれる最小値。
* *step:* 配列の連続する 2 つの要素の差異。

**例**

次の例は、 `[1, 4, 7]`を返します。

```AIQL
range(1, 8, 3)
```

次の例は、2015 年のすべての日を保持する配列を返します。

```AIQL

    range(datetime(2015-01-01), datetime(2015-12-31), 1d)
```

### <a name="todynamic"></a>todynamic
    todynamic('{"a":"a1", "b":["b1", "b2"]}')

文字列を動的な値に変換します。

### <a name="treepath"></a>treepath
    treepath(dynamic_object)

動的オブジェクトのリーフを識別するパス式をすべて列挙します。 

**戻り値**

パス式の配列。

**例**

    treepath(parsejson('{"a":"b", "c":123}')) 
    =>       ["['a']","['c']"]
    treepath(parsejson('{"prop1":[1,2,3,4], "prop2":"value2"}'))
    =>       ["['prop1']","['prop1'][0]","['prop2']"]
    treepath(parsejson('{"listProperty":[100,200,300,"abcde",{"x":"y"}]}'))
    =>       ["['listProperty']","['listProperty'][0]","['listProperty'][0]['x']"]

"[0]" は配列の存在を示しますが、特定のパスで使用されるインデックスを指定しないことに注意してください。

### <a name="zip"></a>zip
    zip(list1, list2, ...)

リストのセットをタプルの 1 つのリストに結合します。

* `list1...`: 値のリスト

**例**

    zip(parsejson('[1,3,5]'), parsejson('[2,4,6]'))
    => [ [1,2], [3,4], [5,6] ]


    zip(parsejson('[1,3,5]'), parsejson('[2,4]'))
    => [ [1,2], [3,4], [5,null] ]


### <a name="names"></a>名前
名前の最大長は 1024 文字です。 大文字と小文字が区別され、アルファベット、数字、およびアンダースコア (`_`) を含めることができます。 

名前として他の文字を含める場合やキーワードを使用する場合は、その名前を [' ... '] または [" ... "] で囲みます。 次に例を示します。

```AIQL

    requests | 
    summarize  ["distinct urls"] = dcount(name) // non-alphanumerics
    by  ['where'] = client_City, // using a keyword as a name
        ['outcome!'] = success // non-alphanumerics
```


|  |  |
| --- | --- |
| ['path\\file\n\'x\''] |文字をエスケープする場合は \ を使用します。 |
| ["d-e.=/f#\n"] | |
| [@'path\file'] |エスケープなし - \ はリテラルです。 |
| [@"\now & then\"] | |
| [where] |名前として言語キーワードを使用します。 |

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]


