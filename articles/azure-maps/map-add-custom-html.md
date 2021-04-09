---
title: マップに HTML マーカーを追加する | Microsoft Azure Maps
description: マップに HTML マーカーを追加する方法について説明します。 Azure Maps Web SDK を使用してマーカーをカスタマイズし、ポップアップとマウス イベントをマーカーに追加する方法について説明します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 1c4367e2a649f4e239e2dab374afc4fb867e517b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92891195"
---
# <a name="add-html-markers-to-the-map"></a>マップに HTML マーカーを追加する

この記事では、イメージ ファイルなどのカスタム HTML をマップに HTML マーカーとして追加する方法について説明します。

> [!NOTE]
> HTML マーカーはデータ ソースに結び付けられません。 代わりに、位置情報がマーカーに直接追加され、マーカーはマップの `markers` プロパティ ([HtmlMarkerManager](/javascript/api/azure-maps-control/atlas.htmlmarkermanager)) に追加されます。

> [!IMPORTANT]
> レンダリングに WebGL を使用する Azure Maps Web コントロールのほとんどのレイヤーとは異なり、HTML マーカーはレンダリングに従来の DOM 要素を使用します。 そのため、ページに追加される HTML マーカーが多くなるほど、DOM 要素数も多くなります。 数百個の HTML マーカーを追加すると、パフォーマンスが低下する可能性があります。 大規模なデータ セットの場合は、データをクラスター化するか、Symbol または Bubble レイヤーを使用することを検討してください。

## <a name="add-an-html-marker"></a>HTML マーカーを追加する

[HtmlMarker](/javascript/api/azure-maps-control/atlas.htmlmarker) クラスには既定のスタイルがあります。 マーカーをカスタマイズするには、マーカーの色とテキストのオプションを設定します。 HTML マーカー クラスの既定のスタイルは、`{color}` と `{text}` のプレースホルダーがある SVG テンプレートです。 簡単にカスタマイズするには、HTML マーカー オプションの色とテキストのプロパティを設定します。 

次のコードでは、HTML マーカーを作成し、color プロパティを "DodgerBlue" に、text プロパティを "10" に設定しています。 ポップアップがマーカーにアタッチされ、ポップアップの表示を切り替えるために `click` イベントが使用されます。

```javascript
//Create an HTML marker and add it to the map.
var marker = new atlas.HtmlMarker({
    color: 'DodgerBlue',
    text: '10',
    position: [0, 0],
    popup: new atlas.Popup({
        content: '<div style="padding:10px">Hello World</div>',
        pixelOffset: [0, -30]
    })
});

map.markers.add(marker);

//Add a click event to toggle the popup.
map.events.add('click',marker, () => {
    marker.togglePopup();
});
```

上記の機能の完全な実行コード サンプルを以下に示します。

<br/>

<iframe height='500' scrolling='no' title='マップに HTML マーカーを追加する' src='//codepen.io/azuremaps/embed/MVoeVw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/MVoeVw/'>Add an HTML Marker to a map</a>」Pen を表示します。
</iframe>

## <a name="create-svg-templated-html-marker"></a>SVG テンプレート化された HTML マーカーを作成する

HTML マーカーの既定の `htmlContent` は、プレースホルダー `{color}` と `{text}` を含む SVG テンプレートです。 カスタムの SVG 文字列を作成し、このような同じプレースホルダーを SVG に追加して、マーカーの `color` オプションと `text` オプションの設定によって SVG 内のこれらのプレースホルダーが更新されるようにすることができます。

<br/>

<iframe height='500' scrolling='no' title='カスタム SVG テンプレートを使用した HTML マーカー' src='//codepen.io/azuremaps/embed/LXqMWx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/LXqMWx/'>HTML Marker with Custom SVG Template</a>」Pen を表示します。
</iframe>

> [!TIP]
> Azure Maps Web SDK には、HTML マーカーと共に使用できるいくつかの SVG イメージ テンプレートが用意されています。 詳細については、[画像テンプレートの使用方法](how-to-use-image-templates-web-sdk.md)のドキュメントを参照してください。

## <a name="add-a-css-styled-html-marker"></a>CSS スタイルの HTML マーカーを追加する

HTML マーカーの利点の 1 つは、CSS を使用して達成できる優れたカスタマイズが多数あることです。 このサンプルで、HtmlMarker のコンテンツは、場所とパルスにドロップされるアニメーション ピンを作成する HTML と CSS で構成されます。

<br/>

<iframe height='500' scrolling='no' title='HTML DataSource' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/qJVgMx/'>HTML DataSource</a>」Pen を表示します。
</iframe>

## <a name="draggable-html-markers"></a>ドラッグ可能な HTML マーカー

このサンプルは、HTML マーカーをドラッグ可能にする方法を示しています。 HTML マーカーでは、`drag`、`dragstart`、および `dragend` イベントがサポートされます。

<br/>

<iframe height='500' scrolling='no' title='ドラッグ可能な HTML マーカー' src='//codepen.io/azuremaps/embed/wQZoEV/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/wQZoEV/'>Draggable HTML Marker</a>」Pen を表示します。
</iframe>

## <a name="add-mouse-events-to-html-markers"></a>マウス イベントを HTML マーカーに追加する

これらのサンプルは、マウスおよびドラッグ イベントを HTML マーカーに追加する方法を示しています。

<br/>

<iframe height='500' scrolling='no' title='マウス イベントを HTML マーカーに追加する' src='//codepen.io/azuremaps/embed/RqOKRz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/RqOKRz/'>Adding Mouse Events to HTML Markers</a>」Pen を表示します。
</iframe>

## <a name="next-steps"></a>次のステップ

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [HtmlMarker](/javascript/api/azure-maps-control/atlas.htmlmarker)

> [!div class="nextstepaction"]
> [HtmlMarkerOptions](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

> [!div class="nextstepaction"]
> [HtmlMarkerManager](/javascript/api/azure-maps-control/atlas.htmlmarkermanager)

マップに追加するコード例の詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [画像テンプレートの使用方法](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [シンボル レイヤーを追加する](./map-add-pin.md)

> [!div class="nextstepaction"]
> [バブル レイヤーを追加する](./map-add-bubble-layer.md)