---
title: Azure Maps を使った道順の表示 | Microsoft Docs
description: 2 つの場所の間の道順を Javascript マップ上に表示する方法
author: jingjing-z
ms.author: jinzh
ms.date: 3/7/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 9e9469e012f2e6b54d9bb84858a9dfc970873b4c
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68476802"
---
# <a name="show-directions-from-a-to-b"></a>A から B までのルートを表示する

この記事では、ルート要求を実行し、マップにルートを表示する方法について説明します。

これには 2 つ方法があります。 最初の方法は、サービス モジュールを介して [Azure Maps Route API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) にクエリを行う方法です。 2 番目の方法は、[Fetch API](https://fetch.spec.whatwg.org/) を使用して、[Azure Maps Route API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) に対して検索要求を行う方法です。 以下で両方の方法を説明します。

## <a name="query-the-route-via-service-module"></a>サービス モジュールを使用してルートをクエリする

<iframe height='500' scrolling='no' title='A から B までのルートをマップに表示する (サービス モジュール)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/RBZbep/'>A から B までのルートをマップに表示する (サービス モジュール)</a>」を表示します。
</iframe>

上記のコードでは、最初のコード ブロックでマップ オブジェクトが構築され、サブスクリプション キーを使用するための認証メカニズムが設定されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

サブスクリプション キーを使用して Azure Maps に対する HTTP 要求を認証するために、2 ブロック目のコードで `SubscriptionKeyCredentialPolicy` が作成されます。 `atlas.service.MapsURL.newPipeline()` は、`SubscriptionKeyCredential` ポリシーを取り込んで、[パイプライン](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) インスタンスを作成します。 `routeURL` は、Azure Maps の [Route](https://docs.microsoft.com/rest/api/maps/route) 操作の URL を表します。

3 つ目のコード ブロックでは、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) オブジェクトが作成され、マップに追加されます。

4 つ目のコード ブロックでは、開始と終了の [points](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) オブジェクトが作成され、それらが dataSource オブジェクトに追加されます。

線は、LineString の [Feature](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) です。 [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) によって、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) にラップされた線オブジェクトがマップ上に線としてレンダリングされます。 4 つ目のコード ブロックでは、線レイヤーが作成され、マップに追加されます。 線レイヤーのプロパティについては、[LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) に関する記事をご覧ください。

[シンボル レイヤー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)は、テキストまたはアイコンを使用して、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) にラップされたポイントベースのデータをシンボルとしてマップにレンダリングします。 5 つ目のコード ブロックでは、シンボル レイヤーが作成され、マップに追加されます。

6 つ目のコード ブロックでは、[サービス モジュール](how-to-use-services-module.md)の一部である Azure Maps ルーティング サービスのクエリが実行されます。 RouteURL の [calculateRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest#methods) メソッドは、開始点と終了点の間のルートを取得するために使用されます。 `geojson.getFeatures()` メソッドを使用して応答から GeoJSON フィーチャー コレクションが抽出され、データ ソースに追加されます。 次に、応答はマップ上にルートとしてレンダリングされます。 マップに線を追加する方法については、[マップへの線の追加](./map-add-shape.md#addALine)に関するページを参照してください。

最後のコード ブロックでは、マップの [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) プロパティを使用してマップの境界が設定されます。

マップの読み込みが完了した後に結果が表示されるように、マップの[イベント リスナー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)内でルート クエリ、データ ソース、シンボル、および線レイヤーとカメラ境界が作成および設定されます。

## <a name="query-the-route-via-fetch-api"></a>Fetch API を介してルートのクエリを実行する

<iframe height='500' scrolling='no' title='A から B までのルートをマップに表示する' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Show directions from A to B on a map</a>」Pen を表示します。
</iframe>

上記のコードでは、最初のコード ブロックでマップ オブジェクトが構築され、サブスクリプション キーを使用するための認証メカニズムが設定されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

2 つ目のコード ブロックでは、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) オブジェクトが作成され、マップに追加されます。

3 つ目のコード ブロックでは、ルートの開始点と終了点が作成され、データ ソースに追加されます。 [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) の使用方法については、[マップにピンを追加する](map-add-pin.md)に関する記事を参照してください。

[LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) によって、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) にラップされた線オブジェクトがマップ上に線としてレンダリングされます。 4 つ目のコード ブロックでは、線レイヤーが作成され、マップに追加されます。 線レイヤーのプロパティについては、[LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) に関するページを参照してください。

[シンボル レイヤー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)は、テキストまたはアイコンを使用して、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) にラップされたポイントベースのデータをシンボルとしてマップにレンダリングします。 5 つ目のコード ブロックでは、シンボル レイヤーが作成され、マップに追加されます。 シンボル レイヤーのプロパティについては、[SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) に関する記事をご覧ください。

次のコード ブロックでは、開始点と終了点から `SouthWest` と `NorthEast` のポイントが作成され、マップの [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) プロパティを使用してマップの境界が設定されます。

最後のコード ブロックでは、[Fetch API](https://fetch.spec.whatwg.org/) を使用して、[Azure Maps Route API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) に対して検索要求を行います。 その後、応答が解析されます。 成功応答の場合は、緯度と経度の情報を使用し、それらのポイントを結んで線の配列が作成されます。 その線データがデータ ソースに追加されて、マップ上にルートがレンダリングされます。 手順については、「[線を追加する](./map-add-shape.md#addALine)」をご覧ください。

マップの読み込みが完了した後に結果が表示されるように、マップの[イベント リスナー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)内でルート クエリ、データ ソース、シンボル、および線レイヤーとカメラ境界が作成および設定されます。

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

完全なコードの例については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [マップ上にトラフィックを表示する](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [マップの連動 – マウス イベント](./map-events.md)
