---
title: Azure Maps にイメージ レイヤーを追加する | Microsoft Docs
description: JavaScript マップにイメージ レイヤーを追加する方法
author: rbrundritt
ms.author: richbrun
ms.date: 12/3/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 5396fefca3a60dea7a503f8b4e84cc575753ea30
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/11/2019
ms.locfileid: "54229117"
---
# <a name="add-an-image-layer-to-a-map"></a>イメージ レイヤーをマップに追加する

この記事では、マップ上の固定された一連の座標に画像をオーバーレイする方法について説明します。 マップへの画像のオーバーレイは、さまざまなシナリオで行われます。 以下に、マップによくオーバーレイされる画像の種類をいくつか挙げます。

* ドローンからキャプチャされた画像。
* ビルのフロアプラン。
* 歴史などの目的に特化したマップ画像。
* 仕事の現場のブループリント。
* 気象レーダーの画像。

> [!TIP]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) は、マップに画像をオーバーレイする手軽で簡単な方法です。 ただし、画像が大きいと、それを読み込むブラウザーに負荷がかかることがあります。 その場合は、お客様の画像をタイルに分割したうえで、[TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) としてマップに読み込むことを検討してください。

## <a name="add-an-image-layer"></a>イメージ レイヤーを追加する

このサンプルは、[1922 年のニュージャージー州ニューアークの地図](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg)の画像をマップにオーバーレイする方法を示しています。

<br/>

<iframe height='500' scrolling='no' title='単純なイメージ レイヤー' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/eQodRo/'>Simple Image Layer</a>」Pen を表示します。
</iframe>

上記のコードの最初のコード ブロックでは、マップ オブジェクトが作成されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

2 つ目のコード ブロックでは、画像の URL と四隅の座標を `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]` 形式で渡すことによって [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) を作成しています。

## <a name="import-a-kml-ground-overlay"></a>KML グラウンド オーバーレイをインポートする

このサンプルは、KML グラウンド オーバーレイ情報を画像レイヤーとしてマップにオーバーレイする方法を示しています。 KML グラウンド オーバーレイは、東西南北の座標と反時計回りの回転を提供します。一方、イメージ レイヤーには、画像の四隅の座標を指定する必要があります。 このサンプルの KML グラウンド オーバーレイは、シャルトル大聖堂のもので、その出典は [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml) です。

<br/>

<iframe height='500' scrolling='no' title='イメージ レイヤーとしての KML グラウンド オーバーレイ' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/EOJgpj/'>KML Ground Overlay as Image Layer</a>」Pen を表示します。
</iframe>

上のコードでは、[ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) クラスの静的関数 `getCoordinatesFromEdges` を使用して、KML グラウンド オーバーレイから得られる東西南北の情報と回転情報から画像の四隅を計算しています。


## <a name="customize-an-image-layer"></a>イメージ レイヤーをカスタマイズする

イメージ レイヤーには、さまざまなスタイル設定オプションがあります。 次のツールでそれらをお試しください。

<br/>

<iframe height='700' scrolling='no' title='イメージ レイヤーのオプション' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/RqOGzx/'>Image Layer Options</a>」Pen を表示します。
</iframe>

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest)

マップに追加できる他のコード サンプルについては、次の記事をご覧ください。

> [!div class="nextstepaction"]
> [タイル レイヤーを追加する](./map-add-tile-layer.md)