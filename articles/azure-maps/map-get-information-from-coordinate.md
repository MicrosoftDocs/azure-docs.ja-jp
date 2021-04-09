---
title: マップの座標に関する情報を表示する | Microsoft Azure Maps
description: ユーザーが座標を選択したときにマップ上の住所に関する情報を表示する方法について説明します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 31e4004379340912051204786da592fe33a5bd63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92890753"
---
# <a name="get-information-from-a-coordinate"></a>座標から情報を取得する

この記事では、クリックしたポップアップの場所のアドレスを表示するように、逆アドレスの検索を行う方法を示しています。

逆アドレスの検索を行うには、2 つの方法があります。 1 つ目の方法は、サービス モジュールを介して [Azure Maps Reverse Address Search API](/rest/api/maps/search/getsearchaddressreverse) にクエリを行う方法です。 もう 1 つの方法は、[Fetch API](https://fetch.spec.whatwg.org/) を使用して [Azure Maps Reverse Address Search API](/rest/api/maps/search/getsearchaddressreverse) に要求を行ってアドレスを検索する方法です。 以下で両方の方法を調べることができます。

## <a name="make-a-reverse-search-request-via-service-module"></a>サービス モジュールを使用して逆方向の検索要求を行う

<iframe height='500' scrolling='no' title='座標から情報を取得する (サービス モジュール)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上で、Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による<a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>座標から情報を取得する (サービス モジュール)</a> ペンを表示します。
</iframe>

上記のコードでは、最初のブロックでマップ オブジェクトが構築され、アクセス トークンを使用するための認証メカニズムが設定されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

アクセス トークンを使用して Azure Maps に対する HTTP 要求を認証するために、2 番目のコード ブロックで `TokenCredential` が作成されます。 その後、`TokenCredential` が `atlas.service.MapsURL.newPipeline()` に渡され、[パイプライン](/javascript/api/azure-maps-rest/atlas.service.pipeline) インスタンスが作成されます。 `searchURL` は、Azure Maps の [Search](/rest/api/maps/search) 操作の URL を表します。

3 番目のコード ブロックでは、マウス カーソルのスタイルがポインターに更新されて、[popup](/javascript/api/azure-maps-control/atlas.popup#open) オブジェクトが作成されます。 手順については、[マップへのポップアップの追加](./map-add-popup.md)に関する記事を参照してください。

4 番目のコード ブロックでは、マウス クリックの[イベント リスナー](/javascript/api/azure-maps-control/atlas.map#events)が追加されます。 トリガーされると、クリックされたポイントの座標を使用して検索クエリが作成されます。 次に、[getSearchAddressReverse](/javascript/api/azure-maps-rest/atlas.service.searchurl#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-) メソッドを使用して、座標のアドレスについて [Get Search Address Reverse API](/rest/api/maps/search/getsearchaddressreverse) に対するクエリが実行されます。 次に、応答から、`geojson.getFeatures()` メソッドを使用してGeoJSON フィーチャー コレクションが抽出されます。

5 番目のコード ブロックでは、クリックされた座標位置の応答アドレスを表示する HTML ポップアップ コンテンツが設定されます。

カーソルの変更、ポップアップ オブジェクト、およびクリック イベントは、いずれもマップの[ロード イベント リスナー](/javascript/api/azure-maps-control/atlas.map#events)で作成されます。 このコード構造により、座標情報を取得する前にマップ全体が確実に読み込まれます。

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Fetch API　を使用して逆方向の検索要求を行う

マップをクリックし、Fetch を使用してその場所に対する逆ジオコーディング要求を行います。

<iframe height='500' scrolling='no' title='座標から情報を取得する' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>ペンを参照してください <a href='https://codepen.io'>CodePen</a> の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) によって<a href='https://codepen.io/azuremaps/pen/ddXzoB/'>座標の情報を取得</a>します。
</iframe>

上記のコードでは、最初のコード ブロックでマップ オブジェクトが構築され、アクセス トークンを使用するための認証メカニズムが設定されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

2 番目のコード ブロックでは、マウス カーソルのスタイルがポインターに更新されます。 これによって [popup](/javascript/api/azure-maps-control/atlas.popup#open) オブジェクトがインスタンス化されます。 手順については、[マップへのポップアップの追加](./map-add-popup.md)に関する記事を参照してください。

3 つ目のコード ブロックでは、マウス クリック用のイベント リスナーが追加されます。 マウスをクリックすると、[Fetch API](https://fetch.spec.whatwg.org/) を使用して、クリックした座標アドレスを見つけるために [Azure Maps Reverse Address Search API](/rest/api/maps/search/getsearchaddressreverse) に対してクエリが実行されます。 応答が成功すると、クリックされた場所のアドレスが収集されます。 これにより、popup クラスの [setOptions](/javascript/api/azure-maps-control/atlas.popup#setoptions-popupoptions-) 関数を使用して、ポップアップのコンテンツと位置が定義されます。

カーソルの変更、ポップアップ オブジェクト、およびクリック イベントは、いずれもマップの[ロード イベント リスナー](/javascript/api/azure-maps-control/atlas.map#events)で作成されます。 このコード構造により、座標情報を取得する前にマップ全体が確実に読み込まれます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [検索サービスを使用するためのベスト プラクティス](how-to-use-best-practices-for-search.md)

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Popup](/javascript/api/azure-maps-control/atlas.popup)

完全なコードの例については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [A から B までのルートを表示する ](./map-route.md)

> [!div class="nextstepaction"]
> [トラフィックを表示する](./map-show-traffic.md)