---
title: Azure Stream Analytics を使用した高頻度取引のシミュレーション
description: 線形回帰モデルのトレーニングとスコア付けを Azure Stream Analytics ジョブで実行する方法。
services: stream-analytics
author: zhongc
ms.author: zhongc
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 33a7b27d065fc0383e4693053f7bfb6d56e2d33b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53087969"
---
# <a name="high-frequency-trading-simulation-with-stream-analytics"></a>Stream Analytics による高頻度取引のシミュレーション
Azure Stream Analytics では、SQL 言語に JavaScript のユーザー定義関数 (UDF) とユーザー定義集計 (UDA) を組み合わせることで高度な分析を行うことができます。 高度な分析には、オンライン機械学習のトレーニングやスコアリングのほか、ステートフル プロセス シミュレーションが含まれます。 この記事では、高頻度取引を例に、Azure Stream Analytics ジョブで線形回帰を実行し、継続的にトレーニングとスコア付けを行う方法について説明します。

## <a name="high-frequency-trading"></a>高頻度取引
高頻度取引の論理フローで大切なことは、次の点です。
1. 証券取引所からリアルタイムの気配値を取得する。
2. 気配値に基づく予測モデルを構築し、ユーザーが値動きを予測できるようにする。
3. 適切に値動きを予測して買い注文や売り注文を出し、利益を上げる。 

結果として、次のものが必要になります。
* 気配値のリアルタイム フィード。
* リアルタイムの気配値に基づいて機能する予測モデル。
* 取引アルゴリズムの利益/損失をデモンストレーションする取引シミュレーション。

### <a name="real-time-quote-feed"></a>気配値のリアルタイム フィード
[リアルタイムの買い気配値と売り気配値](https://iextrading.com/developer/docs/#websockets)は、IEX から提供されている socket.io を使用して無料で入手できます。 リアルタイムの気配値を取得して Azure Event Hubs にデータ ソースとしてプッシュする単純なコンソール プログラムを作成することができます。 以下に示したのは、そのプログラムのスケルトン コードです。 簡潔にするため、エラー処理は省略しています。 また、プロジェクトには別途 SocketIoClientDotNet および WindowsAzure.ServiceBus NuGet パッケージを追加する必要があります。

```csharp
using Quobject.SocketIoClientDotNet.Client;
using Microsoft.ServiceBus.Messaging;
var symbols = "msft,fb,amzn,goog";
var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
var socket = IO.Socket("https://ws-api.iextrading.com/1.0/tops");
socket.On(Socket.EVENT_MESSAGE, (message) =>
{
    eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes((string)message)));
});
socket.On(Socket.EVENT_CONNECT, () =>
{
    socket.Emit("subscribe", symbols);
});
```

生成されるイベントの例を次に示します。

```json
{"symbol":"MSFT","marketPercent":0.03246,"bidSize":100,"bidPrice":74.8,"askSize":300,"askPrice":74.83,volume":70572,"lastSalePrice":74.825,"lastSaleSize":100,"lastSaleTime":1506953355123,lastUpdated":1506953357170,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"GOOG","marketPercent":0.04825,"bidSize":114,"bidPrice":870,"askSize":0,"askPrice":0,volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,lastUpdated":1506953357633,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"MSFT","marketPercent":0.03244,"bidSize":100,"bidPrice":74.8,"askSize":100,"askPrice":74.83,volume":70572,"lastSalePrice":74.825,"lastSaleSize":100,"lastSaleTime":1506953355123,lastUpdated":1506953359118,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"FB","marketPercent":0.01211,"bidSize":100,"bidPrice":169.9,"askSize":100,"askPrice":170.67,volume":39042,"lastSalePrice":170.67,"lastSaleSize":100,"lastSaleTime":1506953351912,lastUpdated":1506953359641,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"GOOG","marketPercent":0.04795,"bidSize":100,"bidPrice":959.19,"askSize":0,"askPrice":0,volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,lastUpdated":1506953360949,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"FB","marketPercent":0.0121,"bidSize":100,"bidPrice":169.9,"askSize":100,"askPrice":170.7,volume":39042,"lastSalePrice":170.67,"lastSaleSize":100,"lastSaleTime":1506953351912,lastUpdated":1506953362205,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"GOOG","marketPercent":0.04795,"bidSize":114,"bidPrice":870,"askSize":0,"askPrice":0,volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,lastUpdated":1506953362629,"sector":"softwareservices","securityType":"commonstock"}
```

>[!NOTE]
>イベントのタイムスタンプは **lastUpdated** で、エポック時間で表現されます。

### <a name="predictive-model-for-high-frequency-trading"></a>高頻度取引の予測モデル
デモンストレーションの目的上、ここでは Darryl Shen 氏がその[論文](http://eprints.maths.ox.ac.uk/1895/1/Darryl%20Shen%20%28for%20archive%29.pdf)の中で説明している線形モデルを使用します。

Volume Order Imbalance (VOI) は、現在の買い気配値/売り気配値/売買高と前回のティックの買い気配値/売り気配値/売買高との関数です。 同論文では、VOI と将来の値動きの間には相関があると考えています。 過去の 5 つの VOI 値とそれに続く 10 ティックの価格変動との間で線形モデルを構築しています。 このモデルのトレーニングには、前日のデータと線形回帰が使用されます。 

そのトレーニング済みのモデルを使用して、取引当日における気配値の価格変動をリアルタイムに予測します。 十分に大きな価格変動が予測されると取引が実行されます。 しきい値の設定によっては、1 つの株式に対して数千回の取引が 1 取引日に実行されることもあります。

![Volume Order Imbalance の定義](./media/stream-analytics-high-frequency-trading/volume-order-imbalance-formula.png)

それでは、トレーニングと予測の操作を Azure Stream Analytics ジョブで表してみましょう。

まず、入力を整えます。 エポック時間は、**DATEADD** を使用して datetime に変換します。 クエリのエラーを回避しながらデータ型を強制的に変換するには、**TRY_CAST** を使用します。 日頃から、入力フィールドを適切なデータ型にキャストすることをお勧めします。そうすれば、フィールドの操作や比較で予期しない動作が発生するのを避けることができます。

```SQL
WITH
typeconvertedquotes AS (
    /* convert all input fields to proper types */
    SELECT
        System.Timestamp AS lastUpdated,
        symbol,
        DATEADD(millisecond, CAST(lastSaleTime as bigint), '1970-01-01T00:00:00Z') AS lastSaleTime,
        TRY_CAST(bidSize as bigint) AS bidSize,
        TRY_CAST(bidPrice as float) AS bidPrice,
        TRY_CAST(askSize as bigint) AS askSize,
        TRY_CAST(askPrice as float) AS askPrice,
        TRY_CAST(volume as bigint) AS volume,
        TRY_CAST(lastSaleSize as bigint) AS lastSaleSize,
        TRY_CAST(lastSalePrice as float) AS lastSalePrice
    FROM quotes TIMESTAMP BY DATEADD(millisecond, CAST(lastUpdated as bigint), '1970-01-01T00:00:00Z')
),
timefilteredquotes AS (
    /* filter between 7am and 1pm PST, 14:00 to 20:00 UTC */
    /* clean up invalid data points */
    SELECT * FROM typeconvertedquotes
    WHERE DATEPART(hour, lastUpdated) >= 14 AND DATEPART(hour, lastUpdated) < 20 AND bidSize > 0 AND askSize > 0 AND bidPrice > 0 AND askPrice > 0
),
```

次に、**LAG** 関数を使用して、前回のティックからの値を取得します。 **LIMIT DURATION** の値には、とりあえず 1 時間を指定しています。 前回のティックを調べるためにさかのぼる時間は、気配値の更新頻度から考えても 1 時間を見ておけば十分でしょう。  

```SQL
shiftedquotes AS (
    /* get previous bid/ask price and size in order to calculate VOI */
    SELECT
        symbol,
        (bidPrice + askPrice)/2 AS midPrice,
        bidPrice,
        bidSize,
        askPrice,
        askSize,
        LAG(bidPrice) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS bidPricePrev,
        LAG(bidSize) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS bidSizePrev,
        LAG(askPrice) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS askPricePrev,
        LAG(askSize) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS askSizePrev
    FROM timefilteredquotes
),
```

これで、VOI 値を計算することができます。 万一前回のティックが存在しない場合も想定して、フィルターで Null 値を除外します。

```SQL
currentPriceAndVOI AS (
    /* calculate VOI */
    SELECT
        symbol,
        midPrice,
        (CASE WHEN (bidPrice < bidPricePrev) THEN 0
            ELSE (CASE WHEN (bidPrice = bidPricePrev) THEN (bidSize - bidSizePrev) ELSE bidSize END)
         END) -
        (CASE WHEN (askPrice < askPricePrev) THEN askSize
            ELSE (CASE WHEN (askPrice = askPricePrev) THEN (askSize - askSizePrev) ELSE 0 END)
         END) AS VOI
    FROM shiftedquotes
    WHERE
        bidPrice IS NOT NULL AND
        bidSize IS NOT NULL AND
        askPrice IS NOT NULL AND
        askSize IS NOT NULL AND
        bidPricePrev IS NOT NULL AND
        bidSizePrev IS NOT NULL AND
        askPricePrev IS NOT NULL AND
        askSizePrev IS NOT NULL
),
```

今度は、もう一度 **LAG** を使用して、2 つの連続する VOI 値とそれに続く連続 10 件の中間価格値とから成るシーケンスを作成します。

```SQL
shiftedPriceAndShiftedVOI AS (
    /* get 10 future prices and 2 previous VOIs */
    SELECT
        symbol,
        midPrice AS midPrice10,
        LAG(midPrice, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice9,
        LAG(midPrice, 2) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice8,
        LAG(midPrice, 3) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice7,
        LAG(midPrice, 4) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice6,
        LAG(midPrice, 5) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice5,
        LAG(midPrice, 6) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice4,
        LAG(midPrice, 7) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice3,
        LAG(midPrice, 8) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice2,
        LAG(midPrice, 9) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice1,
        LAG(midPrice, 10) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice,
        LAG(VOI, 10) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI1,
        LAG(VOI, 11) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI2
    FROM currentPriceAndVOI
),
```

そのデータを、2 変数線形モデルの入力として整形します。 ここでも、データが欠落しているイベントをフィルターで除外します。

```SQL
modelInput AS (
    /* create feature vector, x being VOI, y being delta price */
    SELECT
        symbol,
        (midPrice1 + midPrice2 + midPrice3 + midPrice4 + midPrice5 + midPrice6 + midPrice7 + midPrice8 + midPrice9 + midPrice10)/10.0 - midPrice AS y,
        VOI1 AS x1,
        VOI2 AS x2
    FROM shiftedPriceAndShiftedVOI
    WHERE
        midPrice1 IS NOT NULL AND
        midPrice2 IS NOT NULL AND
        midPrice3 IS NOT NULL AND
        midPrice4 IS NOT NULL AND
        midPrice5 IS NOT NULL AND
        midPrice6 IS NOT NULL AND
        midPrice7 IS NOT NULL AND
        midPrice8 IS NOT NULL AND
        midPrice9 IS NOT NULL AND
        midPrice10 IS NOT NULL AND
        midPrice IS NOT NULL AND
        VOI1 IS NOT NULL AND
        VOI2 IS NOT NULL
),
```

Azure Stream Analytics には組み込みの線形回帰関数が存在しないため、**SUM** と **AVG** の集計を使用して線形モデルの係数を計算しています。

![線形回帰の数式](./media/stream-analytics-high-frequency-trading/linear-regression-formula.png)

```SQL
modelagg AS (
    /* get aggregates for linear regression calculation,
     http://faculty.cas.usf.edu/mbrannick/regression/Reg2IV.html */
    SELECT
        symbol,
        SUM(x1 * x1) AS x1x1,
        SUM(x2 * x2) AS x2x2,
        SUM(x1 * y) AS x1y,
        SUM(x2 * y) AS x2y,
        SUM(x1 * x2) AS x1x2,
        AVG(y) AS avgy,
        AVG(x1) AS avgx1,
        AVG(x2) AS avgx2
    FROM modelInput
    GROUP BY symbol, TumblingWindow(hour, 24, -4)
),
modelparambs AS (
    /* calculate b1 and b2 for the linear model */
    SELECT
        symbol,
        (x2x2 * x1y - x1x2 * x2y)/(x1x1 * x2x2 - x1x2 * x1x2) AS b1,
        (x1x1 * x2y - x1x2 * x1y)/(x1x1 * x2x2 - x1x2 * x1x2) AS b2,
        avgy,
        avgx1,
        avgx2
    FROM modelagg
),
model AS (
    /* calculate a for the linear model */
    SELECT
        symbol,
        avgy - b1 * avgx1 - b2 * avgx2 AS a,
        b1,
        b2
    FROM modelparambs
),
```

現在のイベントのスコア付けに前日のモデルを使用するためには、気配値とモデルを結合する必要があります。 ただし、ここでは **JOIN** の代わりに **UNION** を使用してモデルのイベントと気配値のイベントを統合します。 その後、**LAG** を使用してイベントと前日のモデルをペアにし、返される一致が 1 件だけになるようにします。 週末があるので、3 日前までさかのぼって確認する必要があります。 単純に **JOIN** を使用した場合、気配値イベントごとに 3 つのモデルを取得できることになります。

```SQL
shiftedVOI AS (
    /* get two consecutive VOIs */
    SELECT
        symbol,
        midPrice,
        VOI AS VOI1,        
        LAG(VOI, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI2
    FROM currentPriceAndVOI
),
VOIAndModel AS (
    /* combine VOIs and models */
    SELECT
        'voi' AS type,
        symbol,
        midPrice,
        VOI1,
        VOI2,
        0.0 AS a,
        0.0 AS b1,
        0.0 AS b2
    FROM shiftedVOI
    UNION
    SELECT
        'model' AS type,
        symbol,
        0.0 AS midPrice,
        0 AS VOI1,
        0 AS VOI2,
        a,
        b1,
        b2
    FROM model
),
VOIANDModelJoined AS (
    /* match VOIs with the latest model within 3 days (72 hours, to take the weekend into account) */
    SELECT
        symbol,
        midPrice,
        VOI1 as x1,
        VOI2 as x2,
        LAG(a, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS a,
        LAG(b1, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS b1,
        LAG(b2, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS b2
    FROM VOIAndModel
    WHERE type = 'voi'
),
```

これで、予測が可能な状態になりました。しきい値を 0.02 とするモデルに基づいて買い/売りシグナルを生成することができます。 trade 値 10 が買いです。 trade 値 -10 が売りです。

``` SQL
prediction AS (
    /* make prediction if there is a model */
    SELECT
        symbol,
        midPrice,
        a + b1 * x1 + b2 * x2 AS efpc
    FROM VOIANDModelJoined
    WHERE
        a IS NOT NULL AND
        b1 IS NOT NULL AND
        b2 IS NOT NULL AND
        x1 IS NOT NULL AND
        x2 IS NOT NULL
),
tradeSignal AS (
    /* generate buy/sell signals */
    SELECT
        DateAdd(hour, -7, System.Timestamp) AS time,
        symbol,     
        midPrice,
        efpc,
        CASE WHEN (efpc > 0.02) THEN 10 ELSE (CASE WHEN (efpc < -0.02) THEN -10 ELSE 0 END) END AS trade,
        DATETIMEFROMPARTS(DATEPART(year, System.Timestamp), DATEPART(month, System.Timestamp), DATEPART(day, System.Timestamp), 0, 0, 0, 0) as date
    FROM prediction
),
```

### <a name="trading-simulation"></a>取引のシミュレーション
取引シグナルが得られたら、取引戦略の効果を、実際の取引は行わずにテストしたいと思います。 

このテストは、UDA に、ホップ サイズを 1 分とするホッピング ウィンドウを組み合わせて行います。 別途、日付に基づくグループ化と having 句によって、同じ日に属するイベントだけを集計対象としています。 ホッピング ウィンドウが 2 日にまたがる場合は、日付の **GROUP BY** によって前日と当日にグループ分けされます。 当日に終わっているものの、前日にグループ分けされているウィンドウは、**HAVING** 句によって除外されます。

```SQL
simulation AS
(
    /* perform trade simulation for the past 7 hours to cover an entire trading day, and generate output every minute */
    SELECT
        DateAdd(hour, -7, System.Timestamp) AS time,
        symbol,
        date,
        uda.TradeSimulation(tradeSignal) AS s
    FROM tradeSignal
    GROUP BY HoppingWindow(minute, 420, 1), symbol, date
    Having DateDiff(day, date, time) < 1 AND DATEPART(hour, time) < 13
)
```

この JavaScript UDA は、すべてのアキュムレータを `init` 関数で初期化し、ウィンドウに追加されたイベントごとに状態遷移を計算して、ウィンドウの最後にシミュレーション結果を返します。 一般的な取引プロセスは次のとおりです。

- 買いシグナルを受信し、かつ保有株がなければ株式を買います。
- 売りシグナルを受信し、かつ保有株がある場合には株式を売ります。
- 保有株がない場合には空売りします。 

ショート ポジションで、かつ買いシグナルを受信した場合は、信用買いをすることになります。 このシミュレーションでは、特定の株式のホールドまたはショートが 10 株になることはありません。 取引コストは 8 ドル固定です。

```javascript
function main() {
    var TRADE_COST = 8.0;
    var SHARES = 10;
    this.init = function () {
        this.own = false;
        this.pos = 0;
        this.pnl = 0.0;
        this.tradeCosts = 0.0;
        this.buyPrice = 0.0;
        this.sellPrice = 0.0;
        this.buySize = 0;
        this.sellSize = 0;
        this.buyTotal = 0.0;
        this.sellTotal = 0.0;
    }
    this.accumulate = function (tradeSignal, timestamp) {
        if(!this.own && tradeSignal.trade == 10) {
          // Buy to open
          this.own = true;
          this.pos = 1;
          this.buyPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.buySize += SHARES;
          this.buyTotal += SHARES * tradeSignal.midprice;
        } else if(!this.own && tradeSignal.trade == -10) {
          // Sell to open
          this.own = true;
          this.pos = -1
          this.sellPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.sellSize += SHARES;
          this.sellTotal += SHARES * tradeSignal.midprice;
        } else if(this.own && this.pos == 1 && tradeSignal.trade == -10) {
          // Sell to close
          this.own = false;
          this.pos = 0;
          this.sellPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.pnl += (this.sellPrice - this.buyPrice)*SHARES - 2*TRADE_COST;
          this.sellSize += SHARES;
          this.sellTotal += SHARES * tradeSignal.midprice;
          // Sell to open
          this.own = true;
          this.pos = -1;
          this.sellPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.sellSize += SHARES;        
          this.sellTotal += SHARES * tradeSignal.midprice;
        } else if(this.own && this.pos == -1 && tradeSignal.trade == 10) {
          // Buy to close
          this.own = false;
          this.pos = 0;
          this.buyPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.pnl += (this.sellPrice - this.buyPrice)*SHARES - 2*TRADE_COST;
          this.buySize += SHARES;
          this.buyTotal += SHARES * tradeSignal.midprice;
          // Buy to open
          this.own = true;
          this.pos = 1;
          this.buyPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.buySize += SHARES;         
          this.buyTotal += SHARES * tradeSignal.midprice;
        }
    }
    this.computeResult = function () {
        var result = {
            "pnl": this.pnl,
            "buySize": this.buySize,
            "sellSize": this.sellSize,
            "buyTotal": this.buyTotal,
            "sellTotal": this.sellTotal,
            "tradeCost": this.tradeCost
            };
        return result;
    }
}
```

最後に、Power BI ダッシュボードに出力して視覚化します。

```SQL
SELECT * INTO tradeSignalDashboard FROM tradeSignal /* output tradeSignal to PBI */
SELECT
    symbol,
    time,
    date,
    TRY_CAST(s.pnl as float) AS pnl,
    TRY_CAST(s.buySize as bigint) AS buySize,
    TRY_CAST(s.sellSize as bigint) AS sellSize,
    TRY_CAST(s.buyTotal as float) AS buyTotal,
    TRY_CAST(s.sellTotal as float) AS sellTotal
    INTO pnlDashboard
FROM simulation /* output trade simulation to PBI */
```

![取引 Power BI グラフの視覚エフェクト](./media/stream-analytics-high-frequency-trading/trades-power-bi-chart.png)

![PNL Power BI グラフの視覚エフェクト](./media/stream-analytics-high-frequency-trading/pnl-power-bi-chart.png)


## <a name="summary"></a>まとめ
実際の高頻度取引モデルを Azure Stream Analytics で実装するためには、やや複雑なクエリを使用することになります。 組み込みの線形回帰関数が存在しないため、入力変数を 5 つから 2 つに減らしてモデルを単純化する必要があります。 しかし、その気になれば、より次元の高い、洗練されたアルゴリズムを JavaScript UDA として実装することもできます。 

注目すべき点は、JavaScript UDA を除く大半のクエリのテストとデバッグが、[Visual Studio の Azure Stream Analytics ツール](stream-analytics-tools-for-visual-studio-install.md)を使って Visual Studio 内から実行できることです。 筆者が最初のクエリを作成した後、Visual Studio でクエリのテストとデバッグに要した時間は 30 分足らずでした。 

UDA については現在、Visual Studio でデバッグを行うことはできません。 JavaScript コードをステップ実行する機能も含め、その実現に向けて取り組んでいるところです。 また、UDA の中ではフィールド名が小文字になることに注意してください。 クエリのテスト段階では、この動作が明らかになっていませんでした。 ただし、Azure Stream Analytics 互換性レベル 1.1 では、フィールド名の大文字と小文字が維持され、より自然な動作になっています。

Azure Stream Analytics のすべてのユーザーは、Microsoft のサービスを使用して、高度な分析を絶え間なく、ほぼリアルタイムで実行することができます。この記事がそんなユーザーへのインスピレーションとなれば幸いです。 高度な分析のシナリオで、より簡単にクエリを実装する方法に関して、何かご意見があれば、ぜひお寄せください。
