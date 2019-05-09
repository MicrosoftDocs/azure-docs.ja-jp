---
title: Azure Data Explorer を使用して時系列データを分析する
description: Azure Data Explorer を使用してクラウドで時系列データを分析する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/07/2019
ms.openlocfilehash: 8492f736e64366802b3601f9b5fc8bd1d9b6ea79
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59273075"
---
# <a name="time-series-analysis-in-azure-data-explorer"></a>Azure データ エクスプローラーの時系列分析

Azure データ エクスプローラー (ADX) は、クラウド サービスまたは IoT デバイスからのテレメトリ データの継続的なコレクションを実行します。 このデータは、サービスの正常性の監視、物理的な運用プロセス、および使用状況の傾向などのさまざまな情報について分析できます。 分析は、パターンをその一般的なベースラインのパターンと比較した偏差を調べるために、選択されたメトリックの時系列で実行されます。
ADX には、複数の時系列の作成、操作、および分析のためのネイティブ サポートが含まれています。 このトピックでは、ADX を使用して**何千もの秒単位の時系列**を作成および分析して、ほぼリアルタイムの監視ソリューションとワークフローを可能にする方法について説明します。

## <a name="time-series-creation"></a>時系列の作成

このセクションでは、`make-series` 演算子と、必要に応じて欠損値を使用して、一定間隔の時系列の大きいセットを簡単かつ直感的に作成します。
時系列分析の最初の手順では、パーティション分割し、元のテレメトリ テーブルを時系列のセットに変換します。 テーブルには、通常、タイムスタンプ列、コンテキスト ディメンション、および省略可能なメトリックが含まれています。 ディメンションは、データをパーティション分割するために使用されます。 目標は、一定の時間間隔でパーティションあたり何千もの時系列を作成することです。

入力テーブル *demo_make_series1* には、任意の Web サービス トラフィックの 600 件のレコードが含まれています。 次のコマンドを使用して、10 件のレコードをサンプリングします。

```kusto
demo_make_series1 | take 10 
```

結果のテーブルには、1 つのタイムスタンプ列、3 つのコンテキスト ディメンション列が含まれ、メトリックは含まれていません。

|   |   |   |   |   |
| --- | --- | --- | --- | --- |
|   | TimeStamp | BrowserVer | OsVer | Country |
|   | 2016-08-25 09:12:35.4020000 | Chrome 51.0 | Windows 7 | イギリス |
|   | 2016-08-25 09:12:41.1120000 | Chrome 52.0 | Windows 10 |   |
|   | 2016-08-25 09:12:46.2300000 | Chrome 52.0 | Windows 7 | イギリス |
|   | 2016-08-25 09:12:46.5100000 | Chrome 52.0 | Windows 10 | イギリス |
|   | 2016-08-25 09:12:46.5570000 | Chrome 52.0 | Windows 10 | リトアニア共和国 |
|   | 2016-08-25 09:12:47.0470000 | Chrome 52.0 | Windows 8.1 | インド |
|   | 2016-08-25 09:12:51.3600000 | Chrome 52.0 | Windows 10 | イギリス |
|   | 2016-08-25 09:12:51.6930000 | Chrome 52.0 | Windows 7 | オランダ |
|   | 2016-08-25 09:12:56.4240000 | Chrome 52.0 | Windows 10 | イギリス |
|   | 2016-08-25 09:13:08.7230000 | Chrome 52.0 | Windows 10 | インド |

メトリックがないため、構築できるのは、次のクエリを使用して OS ごとにパーティション分割された、トラフィック数そのものを表す時系列のセットのみです。

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| render timechart 
```

- [`make-series`](/azure/kusto/query/make-seriesoperator) 演算子を使用して、次の 3 つの時系列のセットを作成します。
    - `num=count()`: トラフィックの時系列
    - `range(min_t, max_t, 1h)`: 時系列は時間範囲 (テーブル レコードの最も古いタイムスタンプと最も新しいタイムスタンプ) 内の 1 時間のビンに作成されます
    - `default=0`: 一定間隔の時系列を作成するために、欠測ビンを埋める方法を指定します。 または、変化に対して [`series_fill_const()`](/azure/kusto/query/series-fill-constfunction)、[`series_fill_forward()`](/azure/kusto/query/series-fill-forwardfunction)、[`series_fill_backward()`](/azure/kusto/query/series-fill-backwardfunction)、および [`series_fill_linear()`](/azure/kusto/query/series-fill-linearfunction) を使用します
    - `byOsVer`:  OS ごとのパーティション分割
- 実際の時系列データの構造は、各時間ビンごとに集計された値の数値配列です。 視覚化には `render timechart` を使用します。

上の表には、3 つのパーティションがあります。 個別の時系列を作成できます:次のグラフでは、OS のバージョンごとに Windows 10 (赤)、7 (青)、8.1 (緑):

![時系列のパーティション](media/time-series-analysis/time-series-partition.png)

## <a name="time-series-analysis-functions"></a>時系列の分析関数

このセクションでは、一般的な時系列処理関数を実行します。
時系列が作成された後は、ADX が、増加を続ける関数の一覧をサポートして、それらの処理と分析を行います。これについては[時系列のドキュメント](/azure/kusto/query/machine-learning-and-tsa)を参照してください。 時系列の処理と分析用の、いくつかの代表的な関数について説明します。

### <a name="filtering"></a>フィルター処理

フィルター処理は信号処理の一般的な方法であり、時系列処理タスク (ノイズの多い信号の円滑化や変化検出など) で有効です。
- 次の 2 つの一般的なフィルター処理関数があります。
    - [`series_fir()`](/azure/kusto/query/series-firfunction):FIR フィルターを適用します。 変化検出のための時系列の移動平均と微分の単純な計算に使用します。
    - [`series_iir()`](/azure/kusto/query/series-iirfunction):IIR フィルターを適用します。 指数平滑法と累積合計に使用します。
- サイズ 5 のビンの新しい移動平均系列 (名前は *ma_num*) をクエリに追加することで、時系列セットに `Extend` を実行します。

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| render timechart
```

![時系列のフィルター処理](media/time-series-analysis/time-series-filtering.png)

### <a name="regression-analysis"></a>回帰分析

ADX は、時系列の傾向を予測するために、セグメント化された線形回帰分析をサポートしています。
- [series_fit_line()](/azure/kusto/query/series-fit-linefunction) を使用して、一般的な傾向の検出のために最適な直線を時系列に合わせます。
- [series_fit_2lines()](/azure/kusto/query/series-fit-2linesfunction) を使用して、ベースラインに対して相対的な傾向変化を検出します。監視シナリオで役立ちます。

時系列のクエリでの `series_fit_line()` 関数と `series_fit_2lines()` 関数の例を、次に示します。

```kusto
demo_series2
| extend series_fit_2lines(y), series_fit_line(y)
| render linechart with(xcolumn=x)
```

![時系列回帰](media/time-series-analysis/time-series-regression.png)

- 青: 元の時系列
- 緑: 適合する直線
- 赤: 2 つの適合する直線

> [!NOTE]
> この関数により、ジャンプ (レベルの変化) ポイントが正確に検出されました。

### <a name="seasonality-detection"></a>季節性の検出

多くのメトリックは、季節的な (定期的な) パターンに従います。 クラウド サービスのユーザー トラフィックには、営業日の昼頃が最も多く夜間と週末が最も少ないという、日単位と週単位のパターンが含まれています。 IoT センサーは定期的な間隔で測定を行います。 温度、気圧、湿度などの物理学的測定が季節的な動作を示すもこともあります。

次の例では、Web サービス (2 時間のビン) の 1 か月間のトラフィックに季節性の検出を適用します。

```kusto
demo_series3
| render timechart 
```

![時系列の季節性](media/time-series-analysis/time-series-seasonality.png)

- [series_periods_detect()](/azure/kusto/query/series-periods-detectfunction) を使用して、時系列の期間を自動的に検出します。 
- メトリックに個別の特定の期間が含まれていることがわかっていて、それらが存在することを確認したい場合は、[series_periods_validate()](/azure/kusto/query/series-periods-validatefunction) を使用します。

> [!NOTE]
> これは、個別の特定の期間が存在しない場合は異常です

```kusto
demo_series3
| project (periods, scores) = series_periods_detect(num, 0., 14d/2h, 2) //to detect the periods in the time series
| mv-expand periods, scores
| extend days=2h*todouble(periods)/1d
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | periods | スコア | days |
|   | 84 | 0.820622786055595 | 7 |
|   | 12 | 0.764601405803502 | 1 |

この関数で、日単位と週単位の季節性が検出されます。 週末は平日と異なるため、日単位のスコアは週単位のスコアより少なくなります。

### <a name="element-wise-functions"></a>要素ごとの関数

時系列では算術演算と論理演算を実行できます。 [series_subtract()](/azure/kusto/query/series-subtractfunction) を使用すると、残差時系列、つまり、元の未加工のメトリックと平滑化されたメトリックの間の差を計算して、残差信号の異常を調べることができます。

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| extend residual_num=series_subtract(num, ma_num) //to calculate residual time series
| where OsVer == "Windows 10"   // filter on Win 10 to visualize a cleaner chart 
| render timechart
```

![時系列の演算](media/time-series-analysis/time-series-operations.png)

- 青: 元の時系列
- 赤: 平滑化された時系列
- 緑: 残差系列

## <a name="time-series-workflow-at-scale"></a>大規模な時系列のワークフロー

次の例は、異常検出のために、何千もの秒単位の時系列でこれらの関数を大規模に実行する方法を示しています。 4 日間にわたる DB サービスの読み取り回数のメトリックの、いくつかのサンプルのテレメトリ レコードを確認するには、次のクエリを実行します。

```kusto
demo_many_series1
| take 4 
```

|   |   |   |   |   |   |
| --- | --- | --- | --- | --- | --- |
|   | TIMESTAMP | Loc | anonOp | DB | DataRead |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | 5117853934049630089 | 262 | 0 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | 5117853934049630089 | 241 | 0 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | -865998331941149874 | 262 | 279862 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | 371921734563783410 | 255 | 0 |

シンプルな統計は次のとおりです。

```kusto
demo_many_series1
| summarize num=count(), min_t=min(TIMESTAMP), max_t=max(TIMESTAMP) 
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | num | min\_t | max\_t |
|   | 2177472 | 2016-09-08 00:00:00.0000000 | 2016-09-11 23:00:00.0000000 |

読み取りメトリックの 1 時間のビンで時系列を構築すると (4 日 * 24 時間の合計 = 96 ポイント)、結果として正常なパターンの変動が発生します。

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h)
| render timechart with(ymin=0) 
```

![大規模な時系列分析](media/time-series-analysis/time-series-at-scale.png)

上記の動作は誤解を招く恐れがあります。1 つの正常な時系列が、異常パターンが含まれる可能性のある何千もの別のインスタンスから集計されるためです。 そのため、インスタンスごとの時系列を作成します。 インスタンスは、Loc (場所)、anonOp (操作)、および DB (特定のマシン) によって定義されます。

時系列はいくつ作成できるでしょうか。

```kusto
demo_many_series1
| summarize by Loc, Op, DB
| count
```

|   |   |
| --- | --- |
|   | Count |
|   | 18339 |

ここで、読み取り回数のメトリックの 18339 の時系列のセットを作成します。 make-series ステートメントに `by` 句を追加し、線形回帰を適用して、最も重要な減少傾向があった上位 2 つの時系列を選択します。

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc 
| render timechart with(title='Service Traffic Outage for 2 instances (out of 18339)')
```

![時系列の上位 2 つ](media/time-series-analysis/time-series-top-2.png)

インスタンスを表示します。

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc
| project Loc, Op, DB, slope 
```

|   |   |   |   |   |
| --- | --- | --- | --- | --- |
|   | Loc | 操作 | DB | 傾き |
|   | Loc 15 | 37 | 1151 | -102743.910227889 |
|   | Loc 13 | 37 | 1249 | -86303.2334644601 |

2 分以内に、ADX は約 20,000 の時系列を分析し、読み取り数が突然低下した 2 つの異常な時系列を検出しました。

これらの高度な機能と ADX の高速なパフォーマンスを組み合わせることで、時系列分析のためのユニークで強力なソリューションが提供されます。
