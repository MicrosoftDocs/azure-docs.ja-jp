---
title: パラメーター化 URL を使用してカスタム ビューを共有する - Azure Time Series Insights | Microsoft Docs
description: パラメーター化 URL を作成して、カスタマイズしたエクスプローラー ビューを Azure Time Series Insights で簡単に共有できるようにする方法について説明します。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: 9bf857a66643b1e95ea2559601761a7217babad4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91665329"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>パラメーター化 URL を使用してカスタム ビューを共有する

Azure Time Series Insights Explorer のカスタム ビューを共有するために、プログラムからカスタム ビューのパラメーター化 URL を作成することができます。

Azure Time Series Insights Explorer では、そのエクスペリエンスのビューを URL から直接指定する URL クエリ パラメーターがサポートされています。 たとえば、ターゲット環境や検索述語、必要な期間を URL だけで指定することができます。 そのカスタマイズした URL をユーザーが選択すると、Azure Time Series Insights ポータル内の対応するアセットにインターフェイスから直接アクセスすることができます。 データ アクセス ポリシーが適用されます。

> [!TIP]
>
> * 無料の [Azure Time Series Insights のデモ](https://insights.timeseries.azure.com/samples)をご覧ください。
> * 付属の [Azure Time Series Insights Explorer](./time-series-insights-explorer.md) のドキュメントを参照してください。

## <a name="environment-id"></a>環境 ID

`environmentId=<guid>` パラメーターは、ターゲット環境 ID を指定します。 これはデータ アクセス FQDN の構成要素であり、Azure portal では、環境の概要の右上隅に表示されます。 `env.timeseries.azure.com` の前に存在するすべての文字列が該当します。

たとえば、環境 ID パラメーターは `?environmentId=10000000-0000-0000-0000-100000000108` のようになっています

## <a name="time"></a>Time

パラメーター化 URL では、絶対時刻または相対時刻の値を指定できます。

### <a name="absolute-time-values"></a>絶対時刻値

絶対時刻値には、`from=<integer>` パラメーターと `to=<integer>` パラメーターを使用します。

* `from=<integer>` には、検索範囲の開始時刻を JavaScript のミリ秒表現で指定します。
* `to=<integer>` には、検索範囲の終了時刻を JavaScript のミリ秒表現で指定します。

> [!TIP]
> 日付を JavaScript のミリ秒に簡単に変換するには、[Epoch & Unix Timestamp Converter](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html) を試してください。

### <a name="relative-time-values"></a>相対時刻値

相対時刻値には、`relativeMillis=<value>` を使用します。*value* は、API から受け取った直近のタイムスタンプを基準とする JavaScript のミリ秒表現で指定します。

たとえば「`&relativeMillis=3600000`」を指定した場合、直近 60 分のデータが表示されます。

指定できる値は、Azure Time Series Insights Explorer の **[quick time]\(クイック タイム\)** メニューに対応し、次のような値です。

* `1800000` (直近 30 分)
* `3600000` (直近 60 分)
* `10800000` (直近 3 時間)
* `21600000` (直近 6 時間)
* `43200000` (直近 12 時間)
* `86400000` (直近 24 時間)
* `604800000` (直近 7 日)
* `2592000000` (直近 30 時間)

### <a name="optional-parameters"></a>省略可能なパラメーター

`timeSeriesDefinitions=<collection of term objects>` パラメーターは、Azure Time Series Insights ビューに表示される述語項を指定します。

| パラメーター | URL 項目 | 説明 |
| --- | --- | --- |
| **name** | `\<string>` | "*期間*" の名前。 |
| **splitBy** | `\<string>` | "*分割基準*" となる列名。 |
| **measureName** | `\<string>` | "*メジャー*" の列名。 |
| **predicate** | `\<string>` | サーバー側フィルター処理の *where* 句。 |
| **useSum** | `true` | メジャーの合計を使用することを指定する省略可能なパラメーター。 |

> [!NOTE]
> 選択された **useSum** メジャーが `Events` である場合、既定で count が選択されます。  
> `Events` が選択されていない場合、既定で average が選択されます。 |

* `multiChartStack=<true/false>` キー/値ペアでは、グラフの積み重ねが有効になります。
* `multiChartSameScale=<true/false>` キー/値ペアでは、オプションのパラメーター内の期間全体で同じ Y 軸のスケールが有効になります。  
* `timeBucketUnit=<Unit>&timeBucketSize=<integer>` を使用すると、間隔スライダーを調整して、グラフの集計表示を細かく、または滑らかにできます。  
* `timezoneOffset=<integer>` パラメーターを使用すると、UTC のオフセットとして表示されるグラフのタイムゾーンを設定できます。

| ペア | 説明 |
| --- | --- |
| `multiChartStack=false` | `true` は既定で有効なので、積み重ねるには `false` を渡します。 |
| `multiChartStack=false&multiChartSameScale=true` | 期間全体で同じ Y 軸のスケールを使用するには、積み重ねを有効にする必要があります。  既定では `false` なので、`true` を渡してこの機能を有効にします。 |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | 単位は `days`、`hours`、`minutes`、`seconds`、`milliseconds` です。  単位は常に大文字にします。 </br> **timeBucketSize** に目的の整数を渡して、単位数を定義します。  |
| `timezoneOffset=-<integer>` | この整数は常にミリ秒単位です。 |

> [!NOTE]
> **timeBucketUnit** 値は、最大 7 日に平滑化できます。
> **timezoneOffset** 値は UTC でも現地時刻でもありません。

### <a name="examples"></a>例

URL パラメーターとして Azure Time Series Insights 環境に時系列定義を追加するには、以下を追加します。

```URL parameter
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

以下の時系列定義の例を使用します。

* 環境 ID
* 直近 60 分のデータ
* オプション パラメーターを構成する用語 (**F1PressureID**、**F2TempStation**、**F3VibrationPL**)

この場合、ビューのパラメーター化 URL は、次のように構築することができます。

```URL
https://insights.timeseries.azure.com/classic/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

[![Azure Time Series Insights Explorer のパラメーター化 URL](media/parameterized-url/share-parameterized-url.png)](media/parameterized-url/share-parameterized-url.png#lightbox)

> [!TIP]
> Explorer ライブで上記の [URL の使用](https://insights.timeseries.azure.com/classic/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[%7B%22name%22:%22F1PressureId%22,%22splitBy%22:%22Id%22,%22measureName%22:%22Pressure%22,%22predicate%22:%22%27Factory1%27%22%7D,%7B%22name%22:%22F2TempStation%22,%22splitBy%22:%22Station%22,%22measureName%22:%22Temperature%22,%22predicate%22:%22%27Factory2%27%22%7D,%7B%22name%22:%22F3VibrationPL%22,%22splitBy%22:%22ProductionLine%22,%22measureName%22:%22Vibration%22,%22predicate%22:%22%27Factory3%27%22%7D]
)例についてご覧ください。

上の URL は、パラメーター化された Azure Time Series Insights Explorer ビューを記述し、表示しています。

* パラメーター化された述語です。

  [![Azure Time Series Insights Explorer のパラメーター化された述語。](media/parameterized-url/share-parameterized-url-predicates.png)](media/parameterized-url/share-parameterized-url-predicates.png#lightbox)

* 共有された完全なグラフ ビュー。

  [![共有された完全なグラフ ビュー。](media/parameterized-url/share-parameterized-url-full-chart.png)](media/parameterized-url/share-parameterized-url-full-chart.png#lightbox)

## <a name="next-steps"></a>次のステップ

* [C# を使用してデータのクエリを行う](time-series-insights-query-data-csharp.md)方法を学習します。

* [Azure Time Series Insights Explorer](./time-series-insights-explorer.md) について学習します。
