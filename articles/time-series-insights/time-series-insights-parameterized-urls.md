---
title: パラメーター化 URL を使用して Azure Time Series Insights のカスタム ビューを共有する | Microsoft Docs
description: この記事では、パラメーター化した URL を Azure Time Series Insights で構築し、カスタマー ビューを簡単に共有できるようにする方法について説明します。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.topic: conceptual
ms.workload: big-data
ms.date: 11/21/2017
ms.custom: seodec18
ms.openlocfilehash: 172e6f53b25a1aeef67afea0c1769e6fcaf497cd
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487874"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>パラメーター化 URL を使用してカスタム ビューを共有する

Time Series Insights エクスプローラーのカスタム ビューを共有するために、プログラムからカスタム ビューのパラメーター化 URL を作成することができます。

Time Series Insights エクスプローラーでは、そのエクスペリエンスのビューを URL から直接指定する URL クエリ パラメーターがサポートされています。  たとえば、ターゲット環境や検索述語、必要な期間を URL だけで指定することができます。 そのカスタマイズした URL をユーザーがクリックすると、Time Series Insights ポータル内の対応するアセットにインターフェイスから直接アクセスすることができます。  データ アクセス ポリシーが適用されます。 

## <a name="environment-id"></a>環境 ID

`environmentId=<guid>` パラメーターは、ターゲット環境 ID を指定します。  これはデータ アクセス FQDN の構成要素であり、Azure Portal では、環境の概要の右上隅に表示されます。  `env.timeseries.azure.com` の前に存在するすべての文字列が該当します。 たとえば、環境 ID パラメーターは `?environmentId=10000000-0000-0000-0000-100000000108` のようになっています

## <a name="time"></a>Time

パラメーター化 URL では、絶対時刻または相対時刻の値を指定できます。

### <a name="absolute-time-values"></a>絶対時刻値

絶対時刻値には、`from=<integer>` パラメーターと `to=<integer>` パラメーターを使用します。 

`from=<integer>` には、検索範囲の開始時刻を JavaScript のミリ秒表現で指定します。

`to=<integer>` には、検索範囲の終了時刻を JavaScript のミリ秒表現で指定します。 

JavaScript における日付のミリ秒表現については、「[Epoch & Unix Timestamp Converter (Epoch & Unix タイムスタンプ コンバーター)](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html)」を参照してください。

### <a name="relative-time-values"></a>相対時刻値

相対時刻値には、`relativeMillis=<value>` を使用します。*value* は、終了時刻の直近データを基準とする時間を JavaScript のミリ秒表現で指定します。

たとえば「`&relativeMillis=3600000`」を指定した場合、直近 60 分のデータが表示されます。

指定できる値は、Time Series Insights エクスプローラーの **[quick time]\(クイック タイム\)** メニューに対応し、次の値が該当します。

- 1800000 (直近 30 分)
- 3600000 (直近 60 分)
- 10800000 (直近 3 時間)
- 21600000 (直近 6 時間)
- 43200000 (直近 12 時間)
- 86400000 (直近 24 時間)
- 604800000 (直近 7 日)
- 2592000000 (直近 30 日)

### <a name="optional-parameters"></a>省略可能なパラメーター

`timeSeriesDefinitions=<collection of term objects>` パラメーターは、Time Series Insights ビューの期間を指定します。

- "name":"<string>"
  - "*期間*" の名前。
- "splitBy":"<string>"
  - "*分割基準*" となる列名。
- "measureName":"<string>"
  - "*メジャー*" の列名。
- "predicate":"<string>"
  - サーバー側フィルター処理の *where* 句。
- "useSum":"true"
  - これは、メジャーの合計を使用することを指定する省略可能なパラメーターです。  選択されたメジャーが "Events" の場合、既定で count が選択されることに注意してください。  "Events" が選択されていない場合、既定で average が選択されます。  

'multiChartStack=<true/false>' パラメーターを使用すると、グラフの積み重ねが有効になります。また、'multiChartSameScale=<true/false>' パラメーターを使用すると、オプションのパラメーター内の期間全体で同じ Y 軸のスケールが有効になります。  

- 'multiChartStack=false'
  - 'True' は既定で有効なので、積み重ねるには 'false' を渡します。
- 'multiChartStack=false&multiChartSameScale=true' 
  - 期間全体で同じ Y 軸のスケールを使用するには、積み重ねを有効にする必要があります。  既定では 'false' なので、'true' を渡してこの機能を有効にします。  
  
`timeBucketUnit=<Unit>&timeBucketSize=<integer>` を使用すると、間隔スライダーを調整して、グラフの集計表示を細かく、または滑らかにできます。  
- `timeBucketUnit=<Unit>&timeBucketSize=<integer>`
  - 単位 = 日、時間、分、秒、ミリ秒。  単位は常に大文字にします。
  - timeBucketSize に目的の整数を渡して、単位数を定義します。  最大 7 日間まで滑らかにすることができます。  
  
`timezoneOffset=<integer>` パラメーターを使用すると、UTC のオフセットとして表示されるグラフのタイムゾーンを設定できます。  
  - `timezoneOffset=-<integer>`
    - この整数は常にミリ秒単位です。  
    - この機能は、ローカル時刻 (ブラウザーの時刻) または UTC を選択できる TSI エクスプローラーで有効にする機能とは少し異なります。  
 
### <a name="examples"></a>例

たとえば URL パラメーターとして時系列の定義を追加するには、以下を使用します。

```https
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

この例の時系列定義を以下に対して使用するとします。 

- 環境 ID
- 直近 60 分のデータ
- オプション パラメーターを構成する期間 (F1PressureID、F2TempStation、F3VibrationPL)
 
この場合、ビューのパラメーター化 URL は、次のように構築することができます。

```https
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

前述の URL によって表されるビューを Time Series Insights エクスプローラーで構築した場合は、次のように表示されます。

![Time Series Insights エクスプローラーの期間設定](media/parameterized-url/url1.png)

完全なビュー (グラフを含む) は次のようになります。

![グラフ ビュー](media/parameterized-url/url2.png)

## <a name="next-steps"></a>次の手順
[C# を使用してデータのクエリを実行する](time-series-insights-query-data-csharp.md)
