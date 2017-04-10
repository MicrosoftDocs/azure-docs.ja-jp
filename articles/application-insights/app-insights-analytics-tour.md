---
title: "Azure Application Insights の Analytics のツアー | Microsoft Docs"
description: "Analytics のすべてのメイン クエリの短いサンプル、Application Insights の強力な検索ツール。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: bddf4a6d-ea8d-4607-8531-1fe197cc57ad
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: 132576ca394fe475000449ea58871726c36d078f
ms.lasthandoff: 03/16/2017


---
# <a name="a-tour-of-analytics-in-application-insights"></a>Application Insights の Analytics について
[Analytics](app-insights-analytics.md) は、[Application Insights](app-insights-overview.md) の強力な検索機能です。 ここでは、Analytics のクエリ言語について説明します。

* **[紹介ビデオを見る](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**。
* **[シミュレーション データで Analytics を試す](https://analytics.applicationinsights.io/demo)** (ご使用のアプリからまだ Application Insights にデータが送信されていない場合)。
* **[SQL ユーザーのチート シート](https://aka.ms/sql-analytics)**では、最も一般的な言語の対応付けを確認できます。

作業を開始するための基本的なクエリをいくつか見てみましょう。

## <a name="connect-to-your-application-insights-data"></a>Application Insights のデータに接続する
以下のように、Application Insights でアプリの [概要ブレード](app-insights-dashboards.md) から Analytics を開きます。

![portal.azure.com で Application Insights リソースを開き、[Analytics] をクリックします。](./media/app-insights-analytics-tour/001.png)

## <a name="takeapp-insights-analytics-referencemdtake-operator-show-me-n-rows"></a>[take](app-insights-analytics-reference.md#take-operator): n 個の行を表示する
ユーザーの操作 (通常、Web アプリが受信する HTTP 要求) を記録するデータ ポイントは、 `requests`という名前のテーブルに格納されます。 各行は、アプリの Application Insights SDK から受信したテレメトリ データ ポイントです。

テーブルのいくつかのサンプル行を調べることから始めましょう。

![results](./media/app-insights-analytics-tour/010.png)

> [!NOTE]
> ステートメントの任意の場所にカーソルを置いてから、[検索] をクリックします。 ステートメントは複数の行に分割できますが、1 つのステートメントに複数の空白行を配置しないでください。 空白行は、ウィンドウ内に複数のクエリを収めるのに便利です。
>
>

複数の列を選択してドラッグし、列でグループ化してフィルター処理します。

![結果の右上にある列の選択をクリックする](./media/app-insights-analytics-tour/030.png)

詳細を表示する場合は、以下のように該当する項目を展開します。

![テーブルを選択し、列の構成を使用する](./media/app-insights-analytics-tour/040.png)

> [!NOTE]
> 列の先頭をクリックすると、Web ブラウザーで使用できる結果の順序を変更できます。 ただし、大きな結果セットの場合、ブラウザーにダウンロードされる行の数が制限されることに注意してください。 この並べ替え方法では、実際の最上位項目または最下位項目が表示されない場合があります。 項目を確実に並べ替えるには、`top` または `sort` 演算子を使用します。
>
>

## <a name="topapp-insights-analytics-referencemdtop-operator-and-sortapp-insights-analytics-referencemdsort-operator"></a>[top](app-insights-analytics-reference.md#top-operator) と [sort](app-insights-analytics-reference.md#sort-operator)
`take` は結果の簡単なサンプルを取得するのに便利ですが、テーブルの行は任意の順序で表示されます。 指定した順序で表示するには、(サンプルに対して) `top` を使用するか、(テーブル全体に対して) `sort` を使用します。

特定の列で並べ替えた最初の n 個の行を表示する場合は、以下のように指定します。

```AIQL

    requests | top 10 by timestamp desc
```

* *構文:* ほとんどの演算子に、`by` などのキーワード パラメーターがあります。
* `desc` は降順、`asc` は昇順を意味します。

![](./media/app-insights-analytics-tour/260.png)

`top...` は、`sort ... | take...` と指定するよりも効率的な方法です。 記述例を以下に示します。

```AIQL

    requests | sort by timestamp desc | take 10
```

結果は同じですが、実行時間が少し長くなります。 (`sort` のエイリアスである `order` を記述することもできます。)

テーブル ビューの列ヘッダーを使用して、画面上の結果を並べ替えることもできます。 ただし、言うまでもなく、`take` または `top` を使用してテーブルの一部のみを取得した場合、並べ替えるのは取得したレコードのみとなります。

## <a name="whereapp-insights-analytics-referencemdwhere-operator-filtering-on-a-condition"></a>[where](app-insights-analytics-reference.md#where-operator): 条件に基づいてフィルター処理する

特定の結果コードを返した要求だけを見てみましょう。

```AIQL

    requests
    | where resultCode  == "404"
    | take 10
```

![](./media/app-insights-analytics-tour/250.png)

`where` 演算子はブール式を使用します。 以下に重要なポイントをいくつか示します。

* `and`、`or`: ブール演算子
* `==`、`<>`、`!=`: 等しい、等しくない
* `=~`、`!~`: 等しい、等しくない (大文字と小文字が区別されない文字列の場合)。 文字列比較演算子にはさらに多くの種類があります。

詳細については、 [スカラー式](app-insights-analytics-reference.md#scalars)に関する記述をご覧ください。

### <a name="getting-the-right-type"></a>適切な種類を取得する
失敗した要求を検索する場合は、次のように指定します。

```AIQL

    requests
    | where isnotempty(resultCode) and toint(resultCode) >= 400
```

`resultCode` は文字列型であるため、数値比較では [キャスト](app-insights-analytics-reference.md#casts) する必要があります。

## <a name="time-range"></a>時間範囲

既定では、クエリは過去 24 時間に制限されます。 ただし、この範囲は変更できます。

![](./media/app-insights-analytics-tour/change-time-range.png)

時間範囲をオーバーライドするには、where 句で `timestamp` にメンションするクエリを記述します。 次に例を示します。

```AIQL

    // What were the slowest requests over the past 3 days?
    requests
    | where timestamp > ago(3d)  // Override the time range
    | top 5 by duration
```

時間範囲の機能は、いずれかのソース テーブルの各メンションの後に挿入される 'where' 句に相当します。

`ago(3d)` は "3 日前" を意味します。 他の時間単位には、時間 (`2h`、`2.5h`)、分 (`25m`)、および秒 (`10s`) があります。

その他の例:

```AIQL

    // Last calendar week:
    requests
    | where timestamp > startofweek(now()-7d)
        and timestamp < startofweek(now())
    | top 5 by duration

    // First hour of every day in past seven days:
    requests
    | where timestamp > ago(7d) and timestamp % 1d < 1h
    | top 5 by duration

    // Specific dates:
    requests
    | where timestamp > datetime(2016-11-19) and timestamp < datetime(2016-11-21)
    | top 5 by duration

```

[日付と時刻のリファレンス](app-insights-analytics-reference.md#date-and-time)。


## <a name="projectapp-insights-analytics-referencemdproject-operator-select-rename-and-compute-columns"></a>[project](app-insights-analytics-reference.md#project-operator): 列の選択、名前変更、計算を行う
必要な列だけを選択する場合は、次のように [`project`](app-insights-analytics-reference.md#project-operator) を使用します。

```AIQL

    requests | top 10 by timestamp desc
             | project timestamp, name, resultCode
```

![](./media/app-insights-analytics-tour/240.png)

また、以下のように列の名前を変更し、新しい列を定義することもできます。

```AIQL

    requests
    | top 10 by timestamp desc
    | project  
            name,
            response = resultCode,
            timestamp,
            ['time of day'] = floor(timestamp % 1d, 1s)
```

![result](./media/app-insights-analytics-tour/270.png)

* `['...']` や `["..."]` のように角かっこで囲まれている場合は、[列名](app-insights-analytics-reference.md#names)にスペースまたは記号を含めることができます。
* `%` は一般的なモジュロ演算子です。
* `1d` (数字の 1 の後に "d" が付加されている) は 1 日を意味する期間リテラルです。 期間リテラルにはこの他にも `12h`、`30m`、`10s`、`0.01s` などがあります。
* `floor` (エイリアスは `bin`) は、指定した基準値の最も近い倍数に値を切り捨てます。 したがって、 `floor(aTime, 1s)` の場合、最も近い秒数に時間を切り捨てます。

[式](app-insights-analytics-reference.md#scalars)には、一般的な演算子 (`+` や `-` など) をすべて含めることができ、各種の便利な関数があります。

## <a name="extendapp-insights-analytics-referencemdextend-operator-compute-columns"></a>[extend](app-insights-analytics-reference.md#extend-operator): 列を計算する
単に列を既存の列に追加する場合は、以下のように [`extend`](app-insights-analytics-reference.md#extend-operator)を使用します。

```AIQL

    requests
    | top 10 by timestamp desc
    | extend timeOfDay = floor(timestamp % 1d, 1s)
```

既存の列をすべて保持する場合に、[`extend`](app-insights-analytics-reference.md#extend-operator) を使用すると [`project`](app-insights-analytics-reference.md#project-operator) よりも詳細度が低くなります。

### <a name="convert-to-local-time"></a>現地時刻への変換

タイムスタンプは常に UTC 形式です。 このため、米国太平洋沿岸に在住で冬の場合は、次のようになります。

```AIQL

    requests
    | top 10 by timestamp desc
    | extend localTime = timestamp - 8h
```


## <a name="summarizeapp-insights-analytics-referencemdsummarize-operator-aggregate-groups-of-rows"></a>[summarize](app-insights-analytics-reference.md#summarize-operator): 行のグループをまとめる
`Summarize` を使用すると、行のグループに対して指定の *集計関数* が適用されます。

たとえば、Web アプリが要求に対して応答するのにかかった時間が `duration`フィールドでレポートされます。 すべての要求に対する平均応答時間を見てみましょう。

![](./media/app-insights-analytics-tour/410.png)

この結果は要求名で分けることもできます。

![](./media/app-insights-analytics-tour/420.png)

`Summarize` は、ストリーム中のデータ ポイントを収集して、`by` 句によって等しく評価されるグループに分けます。 `by` 式の各値 (上記の例では各操作名) は、結果のテーブルに 1 列で表示されます。

結果を時間帯でグループ分けすることもできます。

![](./media/app-insights-analytics-tour/430.png)

`bin` 関数 (つまり `floor`) がどのように使われているかに注目してください。 `by timestamp` のみを使用した場合、すべての入力行が小さなグループになります。 時間、数値などの連続するスカラーの場合、連続する範囲を、管理できる数の不連続値に分ける必要があります。 これを行う最も簡単な方法は、`bin` を使用することです (この関数は、切り捨てのための使い慣れた `floor` 関数にすぎません)。

同じ方法で文字列の範囲を減らすことができます。

![](./media/app-insights-analytics-tour/440.png)

`name=` を使用すると、集計式または by 句の中で結果列の名前を設定できます。

## <a name="counting-sampled-data"></a>サンプリングされたデータのカウント
イベントのカウントには、`sum(itemCount)` 集計を使うことをお勧めします。 多くの場合、itemCount==1 であるため、この関数を使用すると単にグループ内の行数がカウントされます。 ただし、[サンプリング](app-insights-sampling.md)を実行中の場合、元のイベントの一部のみが Application Insights のデータ ポイントとして保持されます。そのため、表示される各データ ポイントに複数の `itemCount` イベントが存在します。

たとえば、サンプリングによって元のイベントの 75% が破棄された場合、保持されているレコードでは itemCount == 4 になります。つまり、保持されているすべてのレコードについて、それぞれ 4 つの元のレコードが存在していました。

アダプティブ サンプリングでは、アプリケーションの使用頻度が高い期間に itemCount が増えます。

したがって、itemCount を合計することによって、元のイベント数の正確な見積もりが得られます。

![](./media/app-insights-analytics-tour/510.png)

また、あるグループ内の行数を実際にカウントする必要がある場合のために、 `count()` 集計 (およびカウント操作) もあります。

さまざまな [集計関数](app-insights-analytics-reference.md#aggregations)が用意されています。

## <a name="charting-the-results"></a>結果のグラフ表示
```AIQL

    exceptions
       | summarize count=sum(itemCount)  
         by bin(timestamp, 1h)
```

既定では、結果はテーブルの形で表示されます。

![](./media/app-insights-analytics-tour/225.png)

テーブル ビューよりわかりやすくするために、 以下のように縦棒オプションを使用してグラフビューで結果を見てみることにします。

![グラフをクリックしてから、縦棒グラフを選択して x 軸と y 軸を割り当てる](./media/app-insights-analytics-tour/230.png)

結果を時間で並べ替えていませんが (テーブル表示でわかるように)、グラフには常に正しい順序で日時が表示されることに注目してください。


## <a name="timecharts"></a>時間グラフ
イベント数を時間単位で表示する場合は、次のように指定します。

```AIQL

    requests
      | summarize event_count=sum(itemCount)
        by bin(timestamp, 1h)
```

以下のグラフの表示オプションを選択します。

![時間グラフ](./media/app-insights-analytics-tour/080.png)

## <a name="multiple-series"></a>複数の系列
`summarize` 句の複数の式によって複数の列が作成されます。

`by` 句の複数の式によって複数の行 (値の組み合わせごとに 1 つ) が作成されます。

```AIQL

    requests
    | summarize count_=sum(itemCount), avg(duration)
      by bin(timestamp, 1h), client_StateOrProvince, client_City
    | order by timestamp asc, client_StateOrProvince, client_City
```

![時間および場所別の要求のテーブル](./media/app-insights-analytics-tour/090.png)

### <a name="segment-a-chart-by-dimensions"></a>ディメンションによるグラフの分割
文字列の列と数値の列があるテーブルのグラフを作成する場合、文字列を使用して数値データを個別の系列に分割できます。 文字列の列が複数ある場合は、識別子として使用する列を選択できます。

![分析グラフの分割](./media/app-insights-analytics-tour/100.png)

#### <a name="bounce-rate"></a>バウンス率

識別子として使用するために、ブール値を文字列に変換します。

```AIQL

    // Bounce rate: sessions with only one page view
    requests
    | where notempty(session_Id)
    | where tostring(operation_SyntheticSource) == "" // real users
    | summarize pagesInSession=sum(itemCount), sessionEnd=max(timestamp)
               by session_Id
    | extend isbounce= pagesInSession == 1
    | summarize count()
               by tostring(isbounce), bin (sessionEnd, 1h)
    | render timechart
```

### <a name="display-multiple-metrics"></a>複数のメトリックの表示
複数の数値列があるテーブルのグラフを作成する場合、タイムスタンプに加え、列の任意の組み合わせを表示できます。

![分析グラフの分割](./media/app-insights-analytics-tour/110.png)

複数の数値列を選択するには、**[分割しない]** を選択しておく必要があります。 複数の数値列を表示しながら、文字列の列で分割することはできません。

## <a name="daily-average-cycle"></a>日次平均サイクル
ここでは、使用量が平均的な日にどのように変わるかを見てみます。

1 日の要求数を時間モジュロ (時間単位にビン分割) でカウントする場合は、次のように指定します。

```AIQL

    requests
    | where timestamp > ago(30d)  // Override "Last 24h"
    | where tostring(operation_SyntheticSource) == "" // real users
    | extend hour = bin(timestamp % 1d , 1h)
          + datetime("2016-01-01") // Allow render on line chart
    | summarize event_count=sum(itemCount) by hour
```

![平均的な日における時間単位の折れ線グラフ](./media/app-insights-analytics-tour/120.png)

> [!NOTE]
> 折れ線グラフで表示する場合、現時点では期間を日時に変換する必要があることに注意してください。
>
>

## <a name="compare-multiple-daily-series"></a>複数の日次系列の比較
ここでは、使用量がさまざまな国で時間帯によってどのように変わるのかを見てみます。

```AIQL

     requests  
     | where timestamp > ago(30d)  // Override "Last 24h"
     | where tostring(operation_SyntheticSource) == "" // real users
     | extend hour= floor( timestamp % 1d , 1h)
           + datetime("2001-01-01")
     | summarize event_count=sum(itemCount)
       by hour, client_CountryOrRegion
     | render timechart
```

![client_CountryOrRegion による分割](./media/app-insights-analytics-tour/130.png)

## <a name="plot-a-distribution"></a>分布のプロット
ここでは、長さ単位でセッションの数を見てみます。

```AIQL

    requests
    | where timestamp > ago(30d) // override "Last 24h"
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id
    | extend sessionDuration = max_timestamp - min_timestamp
    | where sessionDuration > 1s and sessionDuration < 3m
    | summarize count() by floor(sessionDuration, 3s)
    | project d = sessionDuration + datetime("2016-01-01"), count_
```

最後の行は、datetime に変換するために必要となります。 現在、グラフの x 軸は、datetime の場合にのみスカラーとして表示されます。

`where` 句では、1 回限りのセッション (sessionDuration==0) を除外し、x 軸の長さを設定します。

![](./media/app-insights-analytics-tour/290.png)

## <a name="percentilesapp-insights-analytics-referencemdpercentiles"></a>[パーセンタイル](app-insights-analytics-reference.md#percentiles)
ここでは、セッションのパーセンタイルごとの期間範囲を見てみます。

上記のクエリを使用しますが、最後の行は次のように置き換えます。

```AIQL

    requests
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id
    | extend sesh = max_timestamp - min_timestamp
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s)
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
```

また、複数の要求ですべてのセッションを含む正しい数値を取得するために、where 句の上限を削除しています。

![result](./media/app-insights-analytics-tour/180.png)

この結果から次のことがわかります。

* セッションの 5% の期間は 3 分 34 秒未満である。
* セッションの 50% が 36 分以上は続かない。
* セッションの 5% は 7 日以上続く。

国別の内訳を表示する場合に必要なのは、次のように両方の summarize 演算子でそれぞれ client_CountryOrRegion 列を指定するだけです。

```AIQL

    requests
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id, client_CountryOrRegion
    | extend sesh = max_timestamp - min_timestamp
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s), client_CountryOrRegion
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
      by client_CountryOrRegion
```

![](./media/app-insights-analytics-tour/190.png)

## <a name="join"></a>Join (結合)
要求や例外も含む、いくつかのテーブルにアクセスできます。

失敗応答が返された要求に関連する例外を検索する場合は、以下のように `session_Id`でテーブルを結合できます。

```AIQL

    requests
    | where toint(resultCode) >= 500
    | join (exceptions) on operation_Id
    | take 30
```


結合を実行する前に必要な列のみを選択する場合は、 `project` を使用することをお勧めします。
その場合、同じ句で、タイムスタンプ列の名前を変更します。

## <a name="letapp-insights-analytics-referencemdlet-clause-assign-a-result-to-a-variable"></a>[let](app-insights-analytics-reference.md#let-clause): 結果を変数に代入する

上記の式の部分を分割する場合は、`let` を使用します。 結果は変わりません。

```AIQL

    let bad_requests =
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
    | join (exceptions) on session_Id
    | take 30
```

> [!Tip] 
> Analytics クライアントでは、クエリの各部分の間に空白行を入れないでください。 必ず、すべて間を空けずに実行してください。
>

`toscalar` を使用して、単一のテーブル セルを値に変換します。

```AIQL
let topCities =  toscalar (
   requests
   | summarize count() by client_City 
   | top n by count_ 
   | summarize makeset(client_City));
requests
| where client_City in (topCities(3)) 
| summarize count() by client_City;
```


### <a name="functions"></a>関数

*Let* を使用して関数を定義します。

```AIQL

    let usdate = (t:datetime)
    {
      strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ",
      bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
    };
    requests  
    | extend PST = usdate(timestamp-8h)
```

## <a name="accessing-nested-objects"></a>入れ子になったオブジェクトへのアクセス
入れ子になったオブジェクトに簡単にアクセスできます。 たとえば、例外ストリームの構造化されたオブジェクトは次のようになります。

![result](./media/app-insights-analytics-tour/520.png)

関心のあるプロパティを選択することで、これをフラット化できます。

```AIQL

    exceptions | take 10
    | extend method1 = tostring(details[0].parsedStack[1].method)
```

[キャスト](app-insights-analytics-reference.md#casts) を使用して適切な型に変換する必要があります。


## <a name="custom-properties-and-measurements"></a>カスタム プロパティと測定値
アプリケーションで[カスタム ディメンション (プロパティ) とカスタム測定値](app-insights-api-custom-events-metrics.md#properties)をイベントにアタッチする場合、これらは `customDimensions` および `customMeasurements` オブジェクトで表示されます。

たとえば、アプリに以下が含まれているものとします。

```C#

    var dimensions = new Dictionary<string, string>
                     {{"p1", "v1"},{"p2", "v2"}};
    var measurements = new Dictionary<string, double>
                     {{"m1", 42.0}, {"m2", 43.2}};
    telemetryClient.TrackEvent("myEvent", dimensions, measurements);
```

Analytics でこれらの値を抽出するには、次のようにします。

```AIQL

    customEvents
    | extend p1 = customDimensions.p1,
      m1 = todouble(customMeasurements.m1) // cast to expected type

```

カスタム ディメンションが特定の種類であるかどうかを確認するには、次のようにします。

```AIQL

    customEvents
    | extend p1 = customDimensions.p1,
      iff(notnull(todouble(customMeasurements.m1)), ...
```

## <a name="dashboards"></a>ダッシュボード
最も重要なグラフとテーブルをすべてまとめておくために、結果をダッシュボードにピン留めできます。

* [Azure 共有ダッシュボード](app-insights-dashboards.md#share-dashboards): ピンのアイコンをクリックします。 この操作を行うには、共有ダッシュボードが必要です。 Azure Portal でダッシュボードを開くか作成し、[共有] をクリックします。
* [Power BI ダッシュボード](app-insights-export-power-bi.md): [エクスポート]、[Power BI Query (Power BI クエリ)] の順にクリックします。 この方法の利点は、さまざまなソースの他の結果と共にクエリを表示できることです。

## <a name="combine-with-imported-data"></a>インポート済みデータと組み合わせる

Analytics レポートはダッシュボードで非常にわかりやすいものの、データをより消化しやすい形式に変換することが必要な場合もあります。 たとえば、認証されたユーザーがテレメトリでエイリアスにより識別されるとします。 結果には、ユーザーの実名を表示する必要があるとします。 このために必要なのは、エイリアスから実際の名前にマップする CSV ファイルです。

データ ファイルをインポートし、標準的なテーブル (要求、例外など) と同じようにそれを使用できます。 それに単独でクエリを実行するか、他のテーブルと結合します。 たとえば、ユーザーマップという名前のテーブルに `realName` および `userId` という列がある場合、これを使用して要求テレメトリで `user_AuthenticatedId` フィールドを変換できます。

```AIQL

    requests
    | where notempty(user_AuthenticatedId)
    | project userId = user_AuthenticatedId
      // get the realName field from the usermap table:
    | join kind=leftouter ( usermap ) on userId
      // count transactions by name:
    | summarize count() by realName
```

テーブルをインポートするには、[スキーマ] ブレードの **[他のデータ ソース]** で、データのサンプルをアップロードして、指示に従って新しいデータ ソースを追加します。 次に、この定義を使用して、テーブルをアップロードできます。

インポート機能は現在プレビュー段階であるため、最初に [他のデータ ソース] に [お問い合わせ] リンクが表示されます。 これを使用してプレビュー プログラムにサインアップすると、リンクは [新しいデータ ソースの追加] ボタンに変わります。


## <a name="tables"></a>テーブル
アプリから受信したテレメトリのストリームは、いくつかのテーブルからアクセスできます。 各テーブルで使用できるプロパティのスキーマが、ウィンドウの左側に表示されます。

### <a name="requests-table"></a>要求テーブル
Web アプリとセグメントに対する HTTP 要求をページ名別にカウントします。

![名前別にセグメント化された要求をカウントします。](./media/app-insights-analytics-tour/analytics-count-requests.png)

最も失敗した要求を検索します。

![名前別にセグメント化された要求をカウントします。](./media/app-insights-analytics-tour/analytics-failed-requests.png)

### <a name="custom-events-table"></a>カスタム イベント テーブル
[TrackEvent()](app-insights-api-custom-events-metrics.md#trackevent) を使用して独自のイベントを送信すると、このテーブルからイベントを読み取ることができます。

アプリ コードに次の行が含まれている例を見てみましょう。

```C#

    telemetry.TrackEvent("Query",
       new Dictionary<string,string> {{"query", sqlCmd}},
       new Dictionary<string,double> {
           {"retry", retryCount},
           {"querytime", totalTime}})
```

これらのイベントの頻度を表示します。

![カスタム イベントの表示率](./media/app-insights-analytics-tour/analytics-custom-events-rate.png)

イベントから測定値とディメンションを抽出します。

![カスタム イベントの表示率](./media/app-insights-analytics-tour/analytics-custom-events-dimensions.png)

### <a name="custom-metrics-table"></a>カスタム メトリック テーブル
[TrackMetric()](app-insights-api-custom-events-metrics.md#trackmetric) を使用して独自のメトリック値を送信すると、**customMetrics** ストリームでその結果がわかります。 次に例を示します。  

![Application Insights Analytics のカスタム メトリック](./media/app-insights-analytics-tour/analytics-custom-metrics.png)

> [!NOTE]
> [メトリックス エクスプローラー](app-insights-metrics-explorer.md)では、任意の種類のテレメトリにアタッチされているすべてのカスタム測定値は、`TrackMetric()` を使用して送信されたメトリックと共にメトリックス ブレードにまとめて表示されます。 一方 Analytics では、カスタム測定値は実行されたテレメトリの種類 (イベントや要求など) にまだアタッチされており、TrackMetric によって送信されたメトリックは独自のストリームに表示されます。
>
>

### <a name="performance-counters-table"></a>パフォーマンス カウンター テーブル
[パフォーマンス カウンター](app-insights-performance-counters.md)は、CPU、メモリ、ネットワーク使用率などの、アプリの基本的なシステム メトリックを表示します。 独自のカスタム カウンターを含む追加のカウンターを送信するように SDK を構成することができます。

**performanceCounters** スキーマは、各パフォーマンス カウンターの `category`、`counter` 名、および `instance` 名を表示します。 カウンター インスタンス名は一部のパフォーマンス カウンターにのみ適用され、通常はカウントが関連付けられているプロセス名を示します。 各アプリケーションのテレメトリでは、そのアプリケーションのカウンターのみが確認できます。 たとえば、使用できるカウンターを表示するには次のようにします。

![Application Insights Analytics のパフォーマンス カウンター](./media/app-insights-analytics-tour/analytics-performance-counters.png)

選択した期間の利用可能なメモリのグラフを取得するには、次のようにします。

![Application Insights Analytics のメモリ タイムチャート](./media/app-insights-analytics-tour/analytics-available-memory.png)

他のテレメトリと同様に、**performanceCounters** にも、アプリを実行しているホスト コンピューターの ID を示す列 `cloud_RoleInstance` があります。 たとえば、異なるコンピューター上でのアプリのパフォーマンスを比較するには、次のようにします。

![Application Insights Analytics でロール インスタンス別にセグメント化されたパフォーマンス](./media/app-insights-analytics-tour/analytics-metrics-role-instance.png)

### <a name="exceptions-table"></a>例外テーブル
[アプリによって報告された例外](app-insights-asp-net-exceptions.md)はこのテーブルで入手できます。

例外発生時にアプリが処理した HTTP 要求を検索するには、operation_Id で結合します。

![operation_Id で例外と要求を結合](./media/app-insights-analytics-tour/analytics-exception-request.png)

### <a name="browser-timings-table"></a>ブラウザー タイミング テーブル
`browserTimings` は、ユーザーのブラウザーが収集したページ読み込みデータを表示します。

これらのメトリックを表示するには、[クライアント側テレメトリのためにアプリをセットアップ](app-insights-javascript.md)します。

スキーマには、[ページ読み込みプロセスのさまざまな段階の長さを示すメトリック](app-insights-javascript.md#page-load-performance)が含まれます。 (ユーザーがページを読む時間の長さは表示されません。)  

さまざまなページの人気度や、ページごとの読み込み時間を表示します。

![Analytics でのページ読み込み時間](./media/app-insights-analytics-tour/analytics-page-load.png)

### <a name="availability-results-table"></a>可用性の結果テーブル
`availabilityResults` は、[Web テスト](app-insights-monitor-web-app-availability.md)の結果を表示します。 各テスト場所からのテストの実行は、それぞれ個別に報告されます。

![Analytics でのページ読み込み時間](./media/app-insights-analytics-tour/analytics-availability.png)

### <a name="dependencies-table"></a>依存関係テーブル
アプリが行うデータベースや REST API への呼び出し、および TrackDependency() への他の呼び出しの結果が含まれています。 また、ブラウザーからの AJAX 呼び出しも含まれています。

ブラウザーからの AJAX 呼び出し:

```AIQL

    dependencies | where client_Type == "Browser"
    | take 10
```

サーバーからの依存関係呼び出し:

```AIQL

    dependencies | where client_Type == "PC"
    | take 10
```

Application Insights エージェントがインストールされていない場合、サーバー側の依存関係の結果は常に `success==False` と表示されます。 ただし、他のデータは正確です。

### <a name="traces-table"></a>トレース テーブル
TrackTrace() を使用してアプリが送信したテレメトリ、または [他のログ記録フレームワーク](app-insights-asp-net-trace-logs.md)が含まれています。

## <a name="video"></a>ビデオ 
 
> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

## <a name="next-steps"></a>次のステップ
* [Analytics 言語リファレンス](app-insights-analytics-reference.md)
* [SQL ユーザーのチート シート](https://aka.ms/sql-analytics)では、最も一般的な言語の対応付けを確認できます。

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

