---
title: マップにコントロールを追加する | Microsoft Azure Maps
description: Microsoft Azure Maps でズーム コントロール、ピッチ コントロール、回転コントロール、およびスタイルの選択ツールをマップに追加する方法。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: bbcfe4935843a00bc5274273e79f4ffc72dc25ed
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051057"
---
# <a name="add-controls-to-a-map"></a>マップにコントロールを追加する

この記事では、マップにコントロールを追加する方法について説明します。 すべてのコントロールと[スタイル選択機能](./choose-map-style.md)を備えたマップを作成する方法についても説明します。

## <a name="add-zoom-control"></a>ズーム コントロールを追加する

ズーム コントロールは、マップを拡大または縮小するためのボタンを追加します。次のコード サンプルでは、[ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) クラスのインスタンスを作成して、マップの右下隅に追加します。

```javascript
//Construct a zoom control and add it to the map.
map.controls.add(new atlas.control.ZoomControl(), {
    position: 'bottom-right'
});
```

上記の機能の完全な実行コード サンプルを以下に示します。

<br/>

<iframe height='500' scrolling='no' title='ズーム コントロールの追加' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/WKOQyN/'>Adding a zoom control (ズーム コントロールの追加)</a>」を参照してください。
</iframe>

## <a name="add-pitch-control"></a>ピッチ コントロールを追加する

ピッチ コントロールでは、水平線を基準としてマップを傾けるためのボタンを追加します。 次のコード サンプルでは、[PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) クラスのインスタンスを作成します。 PitchControl がマップの右上隅に追加されます。

```javascript
//Construct a pitch control and add it to the map.
map.controls.add(new atlas.control.PitchControl(), {
    position: 'top-right'
});
```

上記の機能の完全な実行コード サンプルを以下に示します。

<br/>

<iframe height='500' scrolling='no' title='ピッチ コントロールの追加' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/xJrwaP/'>Adding a pitch control (ピッチ コントロールの追加)</a>」を参照してください。
</iframe>

## <a name="add-compass-control"></a>コンパス コントロールを追加する

コンパス コントロールは、マップを回転するためのボタンを追加します。 次のコード サンプルでは、[CompassControl](/javascript/api/azure-maps-control/atlas.control.compasscontrol) クラスのインスタンスを作成し、マップの左下隅に追加します。

```javascript
//Construct a compass control and add it to the map.
map.controls.add(new atlas.control.CompassControl(), {
    position: 'bottom-left'
});
```

上記の機能の完全な実行コード サンプルを以下に示します。

<br/>

<iframe height='500' scrolling='no' title='回転コントロールの追加' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/GBEoRb/'>Adding a rotate control (回転コントロールの追加)</a>」を参照してください。
</iframe>

## <a name="a-map-with-all-controls"></a>すべてのコントロールを含むマップ

開発を簡略化するため、複数のコントロールを 1 つの配列に格納し、すべてを一度にマップに追加して、マップの同じ領域に配置することができます。 以下ではこの方法を使用して、標準のナビゲーション コントロールをマップに追加しています。

```javascript
map.controls.add([
    new atlas.control.ZoomControl(),
    new atlas.control.CompassControl(),
    new atlas.control.PitchControl(),
    new atlas.control.StyleControl()
], {
    position: "top-right"
});
```

次のコード サンプルでは、ズーム コントロール、コンパス コントロール、ピッチ コントロール、およびスタイルの選択ツール コントロールをマップの右上隅に追加しています。 どのように自動的にスタックされるかに注目してください。 スクリプト内のコントロール オブジェクトの順序により、マップに表示される順序が決まります。 マップ上のコントロールの順序を変更するために、配列内でコントロールの順序を変更できます。

<br/>

<iframe height='500' scrolling='no' title='すべてのコントロールを含むマップ' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/qyjbOM/'>A map with all the controls (すべてのコントロールを含むマップ)</a>」を参照してください。
</iframe>

スタイルの選択ツール コントロールは、[StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) クラスによって定義されます。 スタイルの選択ツール コントロールの使用方法の詳細については、[マップ スタイルの選択](choose-map-style.md)に関する記事を参照してください。

## <a name="customize-controls"></a>コントロールをカスタマイズする

コントロールをカスタマイズするためのさまざまなオプションをテストするツールを次に示します。

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="ナビゲーション コントロールのオプション" src="//codepen.io/azuremaps/embed/LwBZMx/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/LwBZMx/'>Navigation control options (ナビゲーション コントロールのオプション)</a>」Pen を表示します。
</iframe>

カスタマイズされたナビゲーション コントロールを作成する場合は、`atlas.Control` クラスから拡張するクラスを作成するか、HTML 要素を作成してマップの div の上に配置します。 この UI コントロールで、マップを移動するためのマップの `setCamera` 関数を呼び出します。 

## <a name="next-steps"></a>次のステップ

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [コンパス コントロール](/javascript/api/azure-maps-control/atlas.control.compasscontrol)

> [!div class="nextstepaction"]
> [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) 

> [!div class="nextstepaction"]
> [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) 

> [!div class="nextstepaction"]
> [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) 

完全なコードについては、次の記事を参照してください。

> [!div class="nextstepaction"]
> [ピンを追加する](./map-add-pin.md)

> [!div class="nextstepaction"]
> [ポップアップを追加する](./map-add-popup.md)

> [!div class="nextstepaction"]
> [線レイヤーを追加する](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [多角形レイヤーを追加する](map-add-shape.md)

> [!div class="nextstepaction"]
> [バブル レイヤーを追加する](map-add-bubble-layer.md)