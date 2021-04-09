---
title: Microsoft Azure Maps での Azure Maps Route Service のベスト プラクティス
description: Microsoft Azure Maps から Route Service を使用して車両をルーティングする方法について説明します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8174529def5e3924086e49f36c225f07a4da2648
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99051653"
---
# <a name="best-practices-for-azure-maps-route-service"></a>Azure Maps Route Service のベスト プラクティス

Azure Maps [Route Service](/rest/api/maps/route) の Route Directions と Route Matrix API は、要求された各ルートの推定到着時間 (ETA) を計算するために使用できます。 Route API は、リアルタイムの交通情報や履歴データ (たとえば要求された曜日や時間帯の通常の道路速度) などの要因を考慮します。 API は、時間または距離に基づいて、複数の到着地に対して順番に、または最適化された順序で、利用できる最短または最速のルートを返します。 ユーザーは、歩行者、自転車運転者、商用車両 (トラックなど) に特化したルートや詳細も要求できます。 この記事では、Azure Maps [Route Service](/rest/api/maps/route) を呼び出すためのベスト プラクティスを共有し、次の方法について説明します。

 * Route Directions API と Matrix Routing API から選択する
 * リアルタイムと履歴の交通情報データに基づいて、履歴と予測の移動時間を要求する
 * ルート全体とルートの各区間に関するルートの詳細 (時間、距離など) を要求する
 * 商用車両 (トラックなど) 用のルートを要求する
 * ルートに沿った交通情報 (渋滞や料金情報など) を要求する
 * 1 つ以上の中継地 (ウェイポイント) で構成されるルートを要求する
 * 1 つ以上の中継地からなるルートを最適化して、各中継地 (ウェイポイント) を訪問する最適な順序を取得する
 * サポート ポイントを使用して代替ルートを最適化する。 たとえば、電気自動車の充電ステーションを通過する代替ルートを提供します。
 * Azure Maps Web SDK で [Route Service](/rest/api/maps/route) を使用する

## <a name="prerequisites"></a>前提条件

1. [Azure Maps アカウントを作成します](quick-demo-map-app.md#create-an-azure-maps-account)
2. [プライマリ サブスクリプション キー (主キーまたはサブスクリプション キーとも呼ばれます) を取得します](quick-demo-map-app.md#get-the-primary-key-for-your-account)。

Route Service の範囲の詳細については、[ルーティングの対象範囲](routing-coverage.md)に関するページを参照してください。

この記事では、[Postman アプリ](https://www.postman.com/downloads/)を使用して REST 呼び出しを構築しますが、任意の API 開発環境を選択できます。

## <a name="choose-between-route-directions-and-matrix-routing"></a>Route Directions と Matrix Routing から選択する

Route Directions APIは、ルート パスの移動時間と座標を含む指示を返します。 Route Matrix API を使用すると、出発地と到着地で定義される一連のルートの移動時間と距離を計算できます。 指定された出発地のそれぞれに対して、Matrix API は、その出発地から各到着地へのルート指定コスト (移動時間と距離) を計算します。 これらの API では、希望する出発時刻、到着時刻、車両の種類 (自動車やトラックなど) といったパラメーターを指定できます。 これらはすべてリアルタイムまたは予測の交通情報データを適宜使用して、最適なルートを返します。

次のようなシナリオの場合は、Route Directions API の呼び出しを検討してください。

* 2 つ以上の既知の場所間の最短または最速の運転ルートを要求して、配送車両の正確な到着時刻を取得する。
* マップ上のルートを視覚化するために、ルート ジオメトリを含む詳細なルート ガイダンスを要求する
* 顧客の所在地の一覧を基に、各顧客の所在地を訪問して出発地に戻る最短のルートを計算する。 このシナリオは、巡回セールスマン問題として広く知られています。 1 つの要求で最大 150 のウェイポイント (中継地) を渡すことができます。
* 1 回の API 呼び出しだけで Route Directions Batch API にクエリのバッチを送信する。

次のようなシナリオの場合は、Matrix Routing API の呼び出しを検討してください。

* 一連の出発地と到着地の間の移動時間と距離を計算する。 たとえば、12 人の運転手がいる場合、配達する料理をレストランから入手するために、最も近くにいる空き運転手を探す必要があるとします。
* 可能性のあるルートを実際の移動距離または時間で並べ替える。 Matrix API は、出発地と到着地の組み合わせごとに、移動時間と距離のみを返します。
* 移動時間または距離に基づいてクラスター データを作成する。 たとえば、会社に 50 人の従業員がいて、オフィスから 20 分以内の運転時間の場所に住んでいるすべての従業員を検索します。

Route Directions と Matrix API のいくつかの機能を示す比較を次に示します。

| Azure Maps API | 要求内のクエリの最大数 | 回避区域 | トラックと電気自動車のルート指定 | ウェイポイントと巡回セールスマンの最適化 | サポート ポイント |
| :--------------: |  :--------------: |  :--------------: | :--------------: | :--------------: | :--------------: |
| Get Route Directions | 1 | | ✔ | ✔ | |
| Post Route Directions | 1 | ✔ | ✔ | ✔ | ✔ |
| Post Route Directions Batch | 700 | | ✔ | ✔ | |
| Post Route Matrix | 700 | | ✔ | | |

電気自動車のルート指定機能の詳細については、[Python で Azure Notebooks を使用して電気自動車をルート指定する](tutorial-ev-routing.md)方法に関するチュートリアルを参照してください。

## <a name="request-historic-and-real-time-data"></a>履歴データとリアルタイム データを要求する

既定では、Route Service は移動モードが自動車、出発時刻が現在であると想定します。 ルート計算要求で特に指定されていない限り、リアルタイムの交通状態に基づいてルートを返します。 固定時間に依存する交通制限 (「午後 4 時から午後 6 時は左折禁止」など) が収集され、ルート指定エンジンによって考慮されます。 現在のライブ交通情報を無視するルートを明示的に要求しない限り、通行止め (道路工事など) は考慮されます。 現在の交通情報を無視するには、API 要求で `traffic` を `false` に設定します。

ルート計算の **travelTimeInSeconds** 値には、交通情報による遅延が含まれます。 これは、出発時刻が現在に設定されている場合に、現在および履歴の移動時間データを利用して生成されます。 今後、出発時刻が設定されると、API は履歴データに基づく予測の移動時間を返します。

要求に **computeTravelTimeFor=all** パラメーターを含めると、応答の summary 要素には、履歴の交通状態を含む次のような追加のフィールドが表示されます。

| 要素 | 説明|
| :--- | :--- |
| noTrafficTravelTimeInSeconds | 交通状態が原因の (渋滞などによる) 遅延がルートにないものとして計算される推定移動時間 |
| historicTrafficTravelTimeInSeconds | 時間に依存する履歴の交通情報データを使用して計算される推定移動時間 |
| liveTrafficIncidentsTravelTimeInSeconds | リアルタイムの速度データを使用して計算される推定移動時間 |

次のセクションでは、説明したパラメーターを使用して Route API を呼び出す方法を示します。

### <a name="sample-query"></a>サンプル クエリ

以下に示す最初の例では、作成時に、出発時刻が未来に設定されています。

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=51.368752,-0.118332:51.385426,-0.128929&travelMode=car&traffic=true&departAt=2025-03-29T08:00:20&computeTravelTimeFor=all
```

応答には、次のような summary 要素が含まれます。 出発時刻が未来に設定されているため、**trafficDelayInSeconds** 値は 0 になります。 **travelTimeInSeconds** 値は時間に依存する履歴の交通情報データを使用して計算される推定移動時間です。 そのため、この場合の **travelTimeInSeconds** 値は **historicTrafficTravelTimeInSeconds** 値と等しくなります。

```json
"summary": {
    "lengthInMeters": 2131,
    "travelTimeInSeconds": 248,
    "trafficDelayInSeconds": 0,
    "departureTime": "2025-03-29T08:00:20Z",
    "arrivalTime": "2025-03-29T08:04:28Z",
    "noTrafficTravelTimeInSeconds": 225,
    "historicTrafficTravelTimeInSeconds": 248,
    "liveTrafficIncidentsTravelTimeInSeconds": 248
},
```

### <a name="sample-query"></a>サンプル クエリ

次の 2 番目の例では、出発時刻が現在である、リアルタイムのルート指定要求を使用しています。 これは既定値であるため、URL で明示的に指定されていません。

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=47.6422356,-122.1389797:47.6641142,-122.3011268&travelMode=car&traffic=true&computeTravelTimeFor=all
```

応答には、次のような summary が含まれています。 渋滞のため、**trafficDelaysInSeconds** 値は 0 より大きくなっています。 また、**historicTrafficTravelTimeInSeconds** よりも大きくなっています。

```json
"summary": {
    "lengthInMeters": 16637, 
    "travelTimeInSeconds": 2905, 
    "trafficDelayInSeconds": 1604, 
    "departureTime": "2020-02-28T01:00:20+00:00",
    "arrivalTime": "2020-02-28T01:48:45+00:00", 
    "noTrafficTravelTimeInSeconds": 872, 
    "historicTrafficTravelTimeInSeconds": 1976, 
    "liveTrafficIncidentsTravelTimeInSeconds": 2905 
},
```

## <a name="request-route-and-leg-details"></a>ルートと区間の詳細を要求する

既定では、Route Service は座標の配列を返します。 応答には、`points` という名前のリストに、パスを構成する座標が含まれます。 ルートの応答には、ルートの開始からの距離と推定経過時間も含まれます。 これらの値は、ルート全体の平均速度を計算するために使用できます。

次の図は、`points` 要素を示しています。

![points 要素](media/how-to-use-best-practices-for-routing/points-list-is-hidden-img.png)

`point` 要素を展開して、パスの座標の一覧を表示します。

![展開された points 要素](media/how-to-use-best-practices-for-routing/points-list-img.png)

Route Directions API では、**instructionsType** パラメーターを指定して使用できるさまざまな形式の指示がサポートされています。 コンピューター処理を容易にするように指示の書式を設定するには、**instructionsType=coded** を使用します。 指示をテキストとしてユーザーに表示するには、**instructionsType=tagged** を使用します。 また、指示の一部の要素がマークされているテキストとして指示の書式を設定することができ、指示は特殊な書式設定で表示されます。 詳細については、[サポートされている指示の種類の一覧](/rest/api/maps/route/postroutedirections#routeinstructionstype)に関するページを参照してください。

指示が要求されると、応答は `guidance` という名前の新しい要素を返します。 `guidance` 要素には、道案内による道順とターンバイターンの方向と概要の指示という 2 つの情報が格納されます。

![指示の種類](media/how-to-use-best-practices-for-routing/instructions-type-img.png)

`instructions` 要素は道案内による移動の道順が格納され、`instructionGroups` には概要の指示が設定されます。 各指示の概要では、複数の道路をカバーする可能性のある移動セグメントが対象となります。 API は、ルートの区間の詳細を返すことができます。 交通渋滞の座標範囲や、交通の現在の速度などです。

![道案内の指示](media/how-to-use-best-practices-for-routing/instructions-turn-by-turn-img.png)

![概要の指示](media/how-to-use-best-practices-for-routing/instructions-summary-img.png)

## <a name="request-a-route-for-a-commercial-vehicle"></a>商用車両用のルートを要求する

Azure Maps Routing API は、商用車両のルート指定をサポートしていて、商用トラックのルート指定をカバーしています。 API は、指定された制限を考慮します。 車両の高さと重量、積み荷の危険性から生じる制限などです。 たとえば、車両が引火性物質を運搬する場合は、ルート指定エンジンが居住区域近くにある特定のトンネルを回避します。

### <a name="sample-query"></a>サンプル クエリ

以下のサンプル要求では、商用トラックのルートを照会します。 トラックは、クラス 1 の危険廃棄物を運搬しています。

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass1&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

Route API は、トラックの寸法と危険廃棄物に対応する道順を返します。 `guidance` 要素を展開すると、ルート指示を確認できます。

![クラス 1 危険廃棄物を運搬するトラック](media/how-to-use-best-practices-for-routing/truck-with-hazwaste-img.png)

### <a name="sample-query"></a>サンプル クエリ

上記のクラスから USHazmatClass を変更すると、この変更に対応する別のルートが得られます。

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass9&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

以下の応答は、クラス 9 の危険物 (クラス 1 の危険物よりも危険性が低い) を運搬するトラックの場合です。 `guidance` 要素を展開して道順を確認すると、道順が同じではないことがわかります。 クラス 1 の危険物を運搬するトラックには、より多くのルート指示があります。



![クラス 9 危険廃棄物を運搬するトラック](media/how-to-use-best-practices-for-routing/truck-with-hazwaste9-img.png)



## <a name="request-traffic-information-along-a-route"></a>ルートに沿った交通情報を要求する

Azure Maps Route Direction API を使用すると、開発者は要求に `sectionType` パラメーターを含めることで、区間の種類ごとに詳細を要求できます。 たとえば、各交通渋滞区間の速度情報を要求することができます。 要求できるさまざまな詳細については、[sectionType キーの値の一覧](/rest/api/maps/route/getroutedirections#sectiontype)に関するページを参照してください。

### <a name="sample-query"></a>サンプル クエリ

次のクエリでは `sectionType` を `traffic` に設定します。 シアトルからサンディエゴまでの交通情報を含む区間を要求します。

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&sectionType=traffic&query=47.6062,-122.3321:32.7157,-117.1611
```

応答には、指定された座標に沿った交通情報に適した区間が含まれています。

![トラフィック セクション](media/how-to-use-best-practices-for-routing/traffic-section-type-img.png)

このオプションを使用すると、次の図のように、マップをレンダリングするときに区間に色を付けることができます。 

![マップ上でレンダリングされた、色付けされたセクション](media/how-to-use-best-practices-for-routing/show-traffic-sections-img.png)

## <a name="calculate-and-optimize-a-multi-stop-route"></a>複数の中継地があるルートを計算して最適化する

現在 Azure Maps には、2 つの形式のルート最適化があります。

* 要求されたルートの種類に基づく最適化。ウェイポイントの順序は変更されません。 [ここで、サポートされているルートの種類](/rest/api/maps/route/postroutedirections#routetype)を確認できます。

* 巡回セールスマン最適化。各中継地を訪問するための最適な順序を取得するためにウェイポイントの順序を変更します。

複数の中継地があるルート指定の場合、1 つのルート要求で最大 150 のウェイポイントを指定できます。 周遊の場合と同じように、開始と終了の座標位置を同じにすることができます。 ただし、ルートの計算を行うには、少なくとも 1 つの追加ウェイポイントを指定する必要があります。 ウェイポイントは、出発地と到着地の座標の途中でクエリに追加できます。

特定のウェイポイントを訪問する最適な順序を最適化する場合は、**computeBestOrder=true** を指定する必要があります。 このシナリオは、巡回セールスマン最適化問題としても知られています。

### <a name="sample-query"></a>サンプル クエリ

次のクエリでは、`computeBestOrder` パラメーターを `false` に設定して、6 つのウェイポイントに対するパスを要求しています。 これは、`computeBestOrder` パラメーターの既定値でもあります。

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=false&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

応答では、パスの長さが 140,851 m であること、そのパスを移動するのに 9,991 秒かかることが示されます。

![最適化されていない応答](media/how-to-use-best-practices-for-routing/non-optimized-response-img.png)

次の図は、このクエリの結果として得られるパスを示しています。 このパスは、可能なルートのうちの 1 つです。 時間または距離に基づく最適なパスではありません。

![最適化されていない図](media/how-to-use-best-practices-for-routing/non-optimized-image-img.png)



このルートのウェイポイントの順序は次のとおりです。0、1、2、3、4、5、および 6。

### <a name="sample-query"></a>サンプル クエリ

次のクエリでは、上記のサンプルのように、同じ 6 つのウェイポイントのパスを要求しています。 今回は、`computeBestOrder` パラメーターを `true` (巡回セールスマン最適化) に設定します。

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=true&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

応答では、パスの長さが 91,814 m であること、そのパスを移動するのに 7,797 秒かかることが示されます。 API が最適化されたルートを返したため、移動距離と移動時間はどちらも低下します。

![最適化された応答](media/how-to-use-best-practices-for-routing/optimized-response-img.png)

次の図は、このクエリの結果として得られるパスを示しています。

![最適化された図](media/how-to-use-best-practices-for-routing/optimized-image-img.png)

最適なルートのウェイポイントの順序は次のとおりです。0、5、1、2、4、3、および 6。

>[!TIP]
> ルーティング サービスによる最適化されたウェイポイント順序情報では、一連のインデックスを提供します。 これらは、出発地と到着地のインデックスが除外されています。 出発地を考慮するには、これらの値を 1 増やす必要があります。 次に、到着地を末尾に追加して、完全な順序指定済みウェイポイント リストが得られます。

## <a name="calculate-and-bias-alternative-routes-using-supporting-points"></a>サポート ポイントを使用して代替ルートを計算し偏向させる

場合によっては、ルートを再構築して、参照ルートに対して 0 個以上の代替ルートを計算する必要があります。 たとえば、自分の小売店を通過する代替ルートを顧客に表示するとします。 この場合は、サポート ポイントを使用して場所を偏向させる必要があります。 場所を偏向させる手順を次に示します。

1. ルートを現状のまま計算し、ルートの応答からパスを取得します。
2. ルート パスを使用して、ルート パスに沿った、または近くにある目的の場所を検索します。 たとえば、Azure Maps [Point of Interest API](/rest/api/maps/search/getsearchpoi) を使用したり、データベース内にある独自のデータを照会したりすることができます。  
3. ルートの開始からの距離に基づいて場所の順序を指定します。
4. 新しいルート要求のサポート ポイントとして、これらの場所を [Post Route Directions API](/rest/api/maps/route/postroutedirections) に追加します。 サポート ポイントの詳細については、[Post Route Directions API のドキュメント](/rest/api/maps/route/postroutedirections#supportingpoints)を参照してください。 

[Post Route Directions API](/rest/api/maps/route/postroutedirections) を呼び出すと、最小偏差時間または距離制約をサポート ポイントと共に設定できます。 代替ルートを提供する場合は、これらのパラメーターを使用しますが、移動時間を制限することもできます。 これらの制約を使用すると、代替ルートは、指定された時間または距離で出発ポイントからの参照ルートに従います。 言い換えると、その他のルートは、指定された制約に従って参照ルートから逸脱します。

次の図は、時間と距離に対して指定された偏差制限を仕様して代替ルートをレンダリングする例を示しています。

![代替ルート](media/how-to-use-best-practices-for-routing/alternative-routes-img.png)

## <a name="use-the-routing-service-in-a-web-app"></a>Web アプリでルーティング サービスを使用する

Azure Maps の Web SDK は、[サービス モジュール](/javascript/api/azure-maps-rest/)を提供します。 このモジュールは、Web または Node.js アプリケーションから JavaScript または TypeScript を使用して簡単に Azure Maps REST API を使用できるようにするヘルパー ライブラリです。 サービス モジュールは、返されたルートをマップにレンダリングするために使用できます。 このモジュールは、GET 要求と POST 要求で使用する API を自動的に決定します。

## <a name="next-steps"></a>次のステップ

詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Azure Maps Route Service](/rest/api/maps/route)

> [!div class="nextstepaction"]
> [サービス モジュールの使用方法](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [マップにルートを表示する](./map-route.md)

> [!div class="nextstepaction"]
> [Azure Maps NPM パッケージ](https://www.npmjs.com/package/azure-maps-rest  )
