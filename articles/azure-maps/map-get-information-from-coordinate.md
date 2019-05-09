---
title: Azure Maps の座標に関する情報を表示する | Microsoft Docs
description: ユーザーが座標を選択したときにマップ上の住所に関する情報を表示する方法
author: jingjing-z
ms.author: jinzh
ms.date: 3/7/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 50f906a9d8a0dc19f5eb47bef4cb68f4703f020f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59256058"
---
# <a name="get-information-from-a-coordinate"></a>座標から情報を取得する

この記事では、クリックしたポップアップの場所のアドレスを表示するように、逆アドレスの検索を行う方法を示しています。

逆アドレスの検索を行うには、2 つの方法があります。 1 つ目の方法は、サービス モジュールを介して [Azure Maps Reverse Address Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) にクエリを行う方法です。 もう 1 つの方法は、[Fetch API](https://fetch.spec.whatwg.org/) を使用して [Azure Maps Reverse Address Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) に要求を行ってアドレスを検索する方法です。 以下で両方の方法を調べることができます。

## <a name="make-a-reverse-search-request-via-service-module"></a>サービス モジュールを使用して逆方向の検索要求を行う

<iframe height='500' scrolling='no' title='座標から情報を取得する (サービス モジュール)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上で、Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による<a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>座標から情報を取得する (サービス モジュール)</a> ペンを表示します。
</iframe>

上記のコードでは、最初のコード ブロックでマップ オブジェクトが構築されて、サブスクリプション キーを使用するための認証メカニズムが設定されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

サブスクリプション キーを使用して Azure Maps に対する HTTP 要求を認証するために、2 ブロック目のコードで `SubscriptionKeyCredentialPolicy` が作成されます。 `atlas.service.MapsURL.newPipeline()` は、`SubscriptionKeyCredential` ポリシーを取り込んで、[パイプライン](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-iot-typescript-latest) インスタンスを作成します。 `searchURL` は、Azure Maps の [Search](https://docs.microsoft.com/rest/api/maps/search) 操作の URL を表します。

3 番目のコード ブロックでは、マウス カーソルのスタイルがポインターに更新されて、[popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) オブジェクトが作成されます。 手順については、[マップへのポップアップの追加](./map-add-popup.md)に関する記事を参照してください。

4 番目のコード ブロックでは、マウス クリックの[イベント リスナー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)が追加されます。 トリガーされると、クリックされたポイントの座標を使用して検索クエリが作成されます。 次に、サービスのモジュールの [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-) メソッドを使用して、座標のアドレスについて [Get Search Address Reverse API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) に対するクエリが実行されます。 次に、`geojson.getFeatures()` メソッドを使用して応答から GeoJSON フィーチャー コレクションが抽出されます。

5 番目のコード ブロックでは、クリックされた座標位置の応答アドレスを表示する HTML ポップアップ コンテンツが設定されます。

座標情報を取得する前にマップの読み込みが完全に行われるように、カーソル、ポップアップ オブジェクト、およびクリック イベントの変更はすべてマップの[ロード イベント リスナー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)で作成されます。

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Fetch API　を使用して逆方向の検索要求を行う

マップをクリックし、Fetch を使用してその場所に対する逆ジオコーディング要求を行います。

<iframe height='500' scrolling='no' title='座標から情報を取得する' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>ペンを参照してください <a href='https://codepen.io'>CodePen</a> の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) によって<a href='https://codepen.io/azuremaps/pen/ddXzoB/'>座標の情報を取得</a>します。
</iframe>

上記のコードでは、最初のコード ブロックでマップ オブジェクトが構築され、サブスクリプション キーを使用するための認証メカニズムが設定されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

2 つ目のコード ブロックでは、マウス カーソルのスタイルがポインターと [popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) オブジェクトに更新されます。 手順については、[マップへのポップアップの追加](./map-add-popup.md)に関する記事を参照してください。

3 つ目のコード ブロックでは、マウス クリック用のイベント リスナーが追加されます。 マウスをクリックすると、[Fetch API](https://fetch.spec.whatwg.org/) を使用して、クリックした座標アドレスを見つけるために [Azure Maps Reverse Address Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) に対してクエリが実行されます。 適切に応答するために、ポップアップ クラスの [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) 関数を使用して、クリックされた場所の住所を収集し、ポップアップのコンテンツと位置が定義されます。

座標情報を取得する前にマップの読み込みが完全に行われるように、カーソル、ポップアップ オブジェクト、およびクリック イベントの変更はすべてマップの[ロード イベント リスナー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)で作成されます。

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

完全なコードの例については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [A から B までのルートを表示する ](./map-route.md)

> [!div class="nextstepaction"]
> [トラフィックを表示する](./map-show-traffic.md)
