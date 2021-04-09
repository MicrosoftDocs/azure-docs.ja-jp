---
title: マップに検索結果を表示する | Microsoft Azure Maps
description: この記事では、Microsoft Azure Maps Web SDK を使用して検索要求を実行し、結果を地図上に表示する方法について説明します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 647a8fc25f27ef7f441ed7459ecd543d4f35581e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895275"
---
# <a name="show-search-results-on-the-map"></a>マップに検索結果を表示する

この記事では、目的地を検索し、地図上に検索結果を表示する方法について説明します。

目的地の検索には、2 つ方法があります。 1 つは、サービス モジュールを使用して検索要求を行う方法です。 もう 1 つの方法は、[Fetch API](https://fetch.spec.whatwg.org/) から [Azure Maps Fuzzy search API](/rest/api/maps/search/getsearchfuzzy) に対して検索要求を行うという方法です。 以下で両方の方法を説明します。

## <a name="make-a-search-request-via-service-module"></a>サービス モジュールを使用して検索要求を行う

<iframe height='500' scrolling='no' title='マップに検索結果を表示する (サービス モジュール)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/zLdYEB/'>Show search results on a map (Service Module)</a>」Pen を表示します。
</iframe>

上記のコードでは、最初のブロックでマップ オブジェクトが構築され、アクセス トークンを使用するための認証メカニズムが設定されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

アクセス トークンを使用して Azure Maps に対する HTTP 要求を認証するために、2 ブロック目のコードで `TokenCredential` が作成されます。 その後、`TokenCredential` が `atlas.service.MapsURL.newPipeline()` に渡され、[パイプライン](/javascript/api/azure-maps-rest/atlas.service.pipeline) インスタンスが作成されます。 `searchURL` は、Azure Maps の [Search](/rest/api/maps/search) 操作の URL を表します。

2 つ目のコード ブロックでは、[DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) クラスを使用してデータ ソース オブジェクトが作成され、検索結果がそこに追加されます。 [シンボル レイヤー](/javascript/api/azure-maps-control/atlas.layer.symbollayer)は、テキストまたはアイコンを使用して、[DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) にラップされたポイントベースのデータをシンボルとしてマップにレンダリングします。  シンボル レイヤーが作成されます。 データ ソースがシンボル レイヤーに追加され、それがマップに追加されます。

4 番目のコード ブロックでは、[サーチ モジュール](how-to-use-services-module.md)で [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) メソッドが使用されます。 それにより、[Get Search Fuzzy rest API](/rest/api/maps/search/getsearchfuzzy) を介して自由形式のテキスト検索を実行して、POI を検索できます。 Get requests to the Search Fuzzy API では、どの組み合わせのあいまい入力も処理できます。 次に、`geojson.getFeatures()` メソッドを使用して応答から GeoJSON のフィーチャー コレクションが抽出されて、データ ソースに追加されます。それにより、シンボル レイヤーを介してマップ上でデータが自動的にレンダリングされます。

最後のコード ブロックでは、マップの [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) プロパティを使用してマップのカメラ境界が調整されます。

検索要求、データ ソース、シンボル レイヤー、カメラの境界は、マップの[イベント リスナー](/javascript/api/azure-maps-control/atlas.map#events)内にあります。 必ず、マップが完全に読み込まれた後に、結果が表示されるようにします。


## <a name="make-a-search-request-via-fetch-api"></a>Fetch API を使用して検索要求を行う

<iframe height='500' scrolling='no' title='マップに検索結果を表示する' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Show search results on a map</a>」Pen を表示します。
</iframe>

上記のコードの最初のコード ブロックでは、マップ オブジェクトが作成されます。 アクセス トークンを使用する認証メカニズムを設定します。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

2 番目のコード ブロックでは、検索要求を行う URL が作成されます。 また、検索結果の境界とピンを格納するための 2 つの配列も作成されます。

3 番目のコード ブロックでは、[Fetch API](https://fetch.spec.whatwg.org/) が使用されます。 [Fetch API](https://fetch.spec.whatwg.org/) は、 [Azure Maps Fuzzy search API](/rest/api/maps/search/getsearchfuzzy) への要求を行って目的地を検索するために使用されます。 あいまい検索 API は、あいまい入力の任意の組み合わせを処理できます。 次に、検索応答の処理と解析を行い、結果のピンを searchPins 配列に追加します。

4 番目のコード ブロックでは、[DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) クラスを使用して、データ ソース オブジェクトが作成されます。 コードでは、ソース オブジェクトに検索結果が追加されます。 [シンボル レイヤー](/javascript/api/azure-maps-control/atlas.layer.symbollayer)は、テキストまたはアイコンを使用して、[DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) にラップされたポイントベースのデータをシンボルとしてマップにレンダリングします。 シンボル レイヤーが作成されます。 データ ソースがシンボル レイヤーに追加され、それがマップに追加されます。

最後のコード ブロックでは、[BoundingBox](/javascript/api/azure-maps-control/atlas.data.boundingbox) オブジェクトが作成されます。 結果の配列が使用され、その後、マップの [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) を使用してマップに対するカメラ境界が調整されます。 次に、結果のピンがレンダリングされます。

必ずマップの読み込みが完了した後に結果が表示されるように、マップの[イベント リスナー](/javascript/api/azure-maps-control/atlas.map#events)内で検索要求、データ ソース、シンボル レイヤー、およびカメラ境界が設定されます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [検索サービスを使用するためのベスト プラクティス](how-to-use-best-practices-for-search.md)

あいまい検索の詳細については、**こちら** を参照してください。

> [!div class="nextstepaction"]
> [Azure Maps Fuzzy Search API](/rest/api/maps/search/getsearchfuzzy)

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

完全なコードの例については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [座標から情報を取得する](map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [A から B までのルートを表示する ](map-route.md)