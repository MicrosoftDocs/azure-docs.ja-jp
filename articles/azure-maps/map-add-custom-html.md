---
title: Azure Maps に HTML マーカーを追加する | Microsoft Docs
description: JavaScript マップに HTML マーカーを追加する方法
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 1c812a77429e13ea39b2f4946043c13e10aaf097
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2019
ms.locfileid: "55993744"
---
# <a name="add-html-markers-to-the-map"></a>マップに HTML マーカーを追加する

この記事では、イメージ ファイルなどのカスタム HTML をマップに HTML マーカーとして追加する方法について説明します。

> [!NOTE]
> HTML マーカーはデータ ソースに結び付けられません。 代わりに、位置情報がマーカーに直接追加され、マーカーはマップの `markers` プロパティ ([HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest)) に追加されます。

> [!IMPORTANT]
> レンダリングに WebGL を使用する Azure Maps Web コントロールのほとんどのレイヤーとは異なり、HTML マーカーはレンダリングに従来の DOM 要素を使用します。 そのため、ページに追加される HTML マーカーが多くなるほど、DOM 要素数も多くなります。 数百個の HTML マーカーを追加すると、パフォーマンスが低下する可能性があります。 大規模なデータ セットの場合は、データをクラスター化するか、Symbol または Bubble レイヤーを使用することを検討してください。

## <a name="add-an-html-marker"></a>HTML マーカーを追加する

HtmlMarker クラスには既定のスタイルがあります。 マーカーをカスタマイズするには、マーカーの色とテキストのオプションを設定します。 HtmlMarker クラスの既定のスタイルは、色とテキストのプレースホルダーがある SVG テンプレートです。 簡単にカスタマイズするには、HtmlMarker オプションの色とテキストのプロパティを設定します。 

<br/>

<iframe height='500' scrolling='no' title='マップに HTML マーカーを追加する' src='//codepen.io/azuremaps/embed/MVoeVw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/MVoeVw/'>Add an HTML Marker to a map</a>」Pen を表示します。
</iframe>

上記のコードの最初のコード ブロックでは、マップ オブジェクトが作成されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

2 番目のコード ブロックでは、[Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) クラスの [markers](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#markers) プロパティを使用して、マップに [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest) が追加されます。 HtmlMarker は[イベント リスナー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)関数内でマップに追加されるため、マップが完全に読み込まれた後に、HtmlMarker が表示されます。

## <a name="create-svg-templated-html-marker"></a>SVG テンプレート化された HTML マーカーを作成する

HTML マーカーの既定の `htmlContent` は、プレースホルダー `{color}` と `{text}` を含む SVG テンプレートです。 カスタムの SVG 文字列を作成し、このような同じプレースホルダーを SVG に追加して、マーカーの `color` オプションと `text` オプションの設定によって SVG 内のこれらのプレースホルダーが更新されるようにすることができます。

<br/>

<iframe height='500' scrolling='no' title='カスタム SVG テンプレートを使用した HTML マーカー' src='//codepen.io/azuremaps/embed/LXqMWx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/LXqMWx/'>HTML Marker with Custom SVG Template</a>」Pen を表示します。
</iframe>

## <a name="add-a-css-styled-html-marker"></a>CSS スタイルの HTML マーカーを追加する

HTML マーカーの利点の 1 つは、CSS を使用して達成できる優れたカスタマイズが多数あることです。 このサンプルで、HtmlMarker のコンテンツは、場所とパルスにドロップされるアニメーション ピンを作成する HTML と CSS で構成されます。

<br/>

<iframe height='500' scrolling='no' title='HTML DataSource' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/qJVgMx/'>HTML DataSource</a>」Pen を表示します。
</iframe>

## <a name="draggable-html-markers"></a>ドラッグ可能な HTML マーカー

このサンプルは、HTML マーカーをドラッグ可能にする方法を示しています。 HTML マーカーでは、`drag`、`dragstart`、および `dragend` イベントがサポートされます。

<br/>

<iframe height='500' scrolling='no' title='ドラッグ可能な HTML マーカー' src='//codepen.io/azuremaps/embed/wQZoEV/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/wQZoEV/'>Draggable HTML Marker</a>」Pen を表示します。
</iframe>

## <a name="add-mouse-events-to-html-markers"></a>マウス イベントを HTML マーカーに追加する

これらのサンプルは、マウスおよびドラッグ イベントを HTML マーカーに追加する方法を示しています。

<br/>

<iframe height='500' scrolling='no' title='マウス イベントを HTML マーカーに追加する' src='//codepen.io/azuremaps/embed/RqOKRz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/RqOKRz/'>Adding Mouse Events to HTML Markers</a>」Pen を表示します。
</iframe>

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest)

マップに追加するコード例の詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [シンボル レイヤーを追加する](./map-add-pin.md)

> [!div class="nextstepaction"]
> [バブル レイヤーを追加する](./map-add-bubble-layer.md)