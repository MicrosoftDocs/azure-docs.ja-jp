---
title: Azure Maps を使った検索結果の表示 | Microsoft Docs
description: Azure Maps を使って検索要求を実行し、Javascript マップに結果を表示する方法
author: jingjing-z
ms.author: jinzh
ms.date: 3/7/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 61eb42def53d44b947eba60e861fd12a37b52455
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68476811"
---
# <a name="show-search-results-on-the-map"></a>マップに検索結果を表示する

この記事では、目的地を検索し、地図上に検索結果を表示する方法について説明します。

目的地の検索には、2 つ方法があります。 1 つは、サービス モジュールを使用して検索要求を行う方法です。 もう 1 つの方法は、[Fetch API](https://fetch.spec.whatwg.org/) から [Azure Maps Fuzzy search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) に対して検索要求を行うという方法です。 以下で両方の方法を説明します。

## <a name="make-a-search-request-via-service-module"></a>サービス モジュールを使用して検索要求を行う

<iframe height='500' scrolling='no' title='マップに検索結果を表示する (サービス モジュール)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/zLdYEB/'>Show search results on a map (Service Module)</a>」Pen を表示します。
</iframe>

上記のコードでは、最初のコード ブロックはマップ オブジェクトが構築されて、サブスクリプション キーを使用するための認証メカニズムが設定されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

サブスクリプション キーを使用して Azure Maps に対する HTTP 要求を認証するために、2 ブロック目のコードで `SubscriptionKeyCredentialPolicy` が作成されます。 `atlas.service.MapsURL.newPipeline()` は、`SubscriptionKeyCredential` ポリシーを取り込んで、[パイプライン](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) インスタンスを作成します。 `searchURL` は、Azure Maps の [Search](https://docs.microsoft.com/rest/api/maps/search) 操作の URL を表します。

2 つ目のコード ブロックでは、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) クラスを使用してデータ ソース オブジェクトが作成され、検索結果がそこに追加されます。 [シンボル レイヤー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)は、テキストまたはアイコンを使用して、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) にラップされたポイントベースのデータをシンボルとしてマップにレンダリングします。  次にシンボル レイヤーが作成され、データ ソースがシンボル レイヤーに追加され、それがマップに追加されます。

4 番目のコード ブロックでは、[サーチ モジュール](how-to-use-services-module.md)で [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) メソッドが使用されます。 それにより、[Get Search Fuzzy rest API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) を介して自由形式のテキスト検索を実行して、POI を検索できます。 Get Search Fuzzy API は、どの組み合わせのあいまい入力も処理できます。 次に、`geojson.getFeatures()` メソッドを使用して応答から GeoJSON のフィーチャー コレクションが抽出されて、データ ソースに追加されます。それにより、シンボル レイヤーを介してマップ上でデータが自動的にレンダリングされます。

最後のコード ブロックでは、Map の [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) プロパティを使用してマップのカメラ境界が調整されます。

マップの読み込みが完了した後に結果が表示されるように、マップの準備完了[イベント リスナー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)内に検索要求、データ ソース、シンボル レイヤー、およびカメラ境界が作成および設定されます。


## <a name="make-a-search-request-via-fetch-api"></a>Fetch API を使用して検索要求を行う

<iframe height='500' scrolling='no' title='マップに検索結果を表示する' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Show search results on a map</a>」Pen を表示します。
</iframe>

上記のコードでは、最初のコード ブロックではマップ オブジェクトが構築されて、サブスクリプション キーを使用するための認証メカニズムが設定されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

2 番目のコード ブロックでは、検索要求を行う URL が作成されます。 また、検索結果の境界とピンを格納するための 2 つの配列も作成されます。

3 番目のコード ブロックでは、[Fetch API](https://fetch.spec.whatwg.org/) を使用して、POI を検索するための要求が [Azure Maps Fuzzy search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) に対して行われます。 あいまい検索 API は、あいまい入力の任意の組み合わせを処理できます。 次に、検索応答の処理と解析を行い、結果のピンを searchPins 配列に追加します。

4 つ目のコード ブロックでは、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) クラスを使用してデータ ソース オブジェクトが作成され、検索結果がそこに追加されます。 [シンボル レイヤー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)は、テキストまたはアイコンを使用して、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) にラップされたポイントベースのデータをシンボルとしてマップにレンダリングします。 次にシンボル レイヤーが作成され、データ ソースがシンボル レイヤーに追加され、それがマップに追加されます。

最後のコード ブロックでは、結果の配列を使用して [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) オブジェクトが作成され、Map の [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) を使用してマップのカメラ境界が調整されます。 次に、結果のピンがレンダリングされます。

マップの読み込みが完了した後に結果が表示されるように、マップの[イベント リスナー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)内で検索要求、データ ソース、シンボル レイヤー、およびカメラ境界が設定されます。

## <a name="next-steps"></a>次の手順

あいまい検索の詳細については、**こちら**を参照してください。

> [!div class="nextstepaction"]
> [Azure Maps Fuzzy Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

完全なコードの例については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [座標から情報を取得する](./map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [A から B までのルートを表示する ](./map-route.md)
