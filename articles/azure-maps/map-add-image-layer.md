---
title: Azure Maps にイメージ レイヤーを追加する | Microsoft Docs
description: Azure Maps Web SDK にイメージ レイヤーを追加する方法。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 6c43ccaee473eca701d15a5a83f84814d65c6b7c
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976162"
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

次のコードでは、[1922 年のニュージャージー州ニューアークの地図](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg)の画像がマップにオーバーレイされています。 画像の URL と四隅の座標を `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]` 形式で渡すことによって、[ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) が作成されています。

```javascript
//Create an image layer and add it to the map.
map.layers.add(new atlas.layer.ImageLayer({
    url: 'newark_nj_1922.jpg',
    coordinates: [
        [-74.22655, 40.773941], //Top Left Corner
        [-74.12544, 40.773941], //Top Right Corner
        [-74.12544, 40.712216], //Bottom Right Corner
        [-74.22655, 40.712216]  //Bottom Left Corner
    ]
}));
```

上記の機能の完全な実行コード サンプルを以下に示します。

<br/>

<iframe height='500' scrolling='no' title='単純なイメージ レイヤー' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/eQodRo/'>Simple Image Layer</a>」Pen を表示します。
</iframe>

## <a name="import-a-kml-ground-overlay"></a>KML グラウンド オーバーレイをインポートする

このサンプルは、KML グラウンド オーバーレイ情報を画像レイヤーとしてマップにオーバーレイする方法を示しています。 KML グラウンド オーバーレイでは、東西南北の座標と反時計回りの回転が提供されます。一方、イメージ レイヤーには、画像の四隅の座標を指定する必要があります。 このサンプルの KML グラウンド オーバーレイは、シャルトル大聖堂のもので、その出典は [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml) です。

次のコードでは、[ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) クラスの静的関数 `getCoordinatesFromEdges` を使用して、KML グラウンド オーバーレイから得られる東西南北の情報と回転情報から画像の四隅が計算されています。

<br/>

<iframe height='500' scrolling='no' title='イメージ レイヤーとしての KML グラウンド オーバーレイ' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/EOJgpj/'>KML Ground Overlay as Image Layer</a>」Pen を表示します。
</iframe>

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