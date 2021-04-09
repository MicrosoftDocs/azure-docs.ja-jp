---
title: Azure Maps の Web マップ コントロールのスタイルを変更する
description: マップのスタイルとオプションを変更する方法について説明します。 ユーザーがさまざまなスタイルを切り替えることができるように、Azure Maps でマップにスタイル ピッカー コントロールを追加する方法について説明します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 556e265cc0d1aae33823185ec98d23f191ed1694
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97680023"
---
# <a name="change-the-style-of-the-map"></a>マップのスタイルを変更する

マップ コントロールでは、複数の異なるマップ [スタイル オプション](/javascript/api/azure-maps-control/atlas.styleoptions)と[基本マップ スタイル](supported-map-styles.md)がサポートされています。 すべてのスタイルは、マップ コントロールを初期化するときに設定できます。 または、マップ コントロールの `setStyle` 関数を使用してスタイルを設定することもできます。 この記事では、これらのスタイル オプションを使用してマップの外観をカスタマイズする方法について説明します。 また、マップにスタイル ピッカー コントロールを実装する方法についても説明します。 ユーザーは、スタイル ピッカー コントロールを使用して別の基本スタイルに切り替えることができます。

## <a name="set-map-style-options"></a>マップ スタイル オプションを設定する

スタイル オプションは、Web コントロールの初期化時に設定できます。 または、マップ コントロールの `setStyle` 関数を呼び出してスタイル オプションを更新することもできます。 使用できるすべてのスタイル オプションについては、「[StyleOptions](/javascript/api/azure-maps-control/atlas.styleoptions)」を参照してください。

```javascript
//Set the style options when creating the map.
var map = new atlas.Map('map', {
    renderWorldCopies: false,
    showBuildingModels: false,
    showLogo: true,
    showFeedbackLink: true,
    style: 'road'

    //Additional map options.
};

//Update the style options at anytime using `setStyle` function.
map.setStyle({
    renderWorldCopies: true,
    showBuildingModels: true,
    showLogo: false,
    showFeedbackLink: false
});
```

次のツールは、さまざまなスタイル オプションによってマップのレンダリング方法がどのように変わるかを示しています。 3D の建物を表示するには、大都市の近くにズーム インします。

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="マップ スタイル オプション" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> で Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/eYNMjPb'>マップ スタイル オプション</a>」を参照してください。
</iframe>

## <a name="set-a-base-map-style"></a>基本マップ スタイルを設定する

Web SDK で使用できるいずれかの[基本マップ スタイル](supported-map-styles.md)を使用して、マップ コントロールを初期化することもできます。 その後、`setStyle` 関数を使用して、基本スタイルを別のマップ スタイルで更新できます。

### <a name="set-a-base-map-style-on-initialization"></a>初期化時に基本マップ スタイルを設定する

マップ コントロールの基本スタイルは、初期化時に設定できます。 次のコードでは、マップ コントロールの `style` オプションが [`grayscale_dark` 基本マップ スタイル](supported-map-styles.md#grayscale_dark)に設定されています。  

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additional map options
);
```

<br/>

<iframe height='500' scrolling='no' title='マップの読み込み時にスタイルを設定する' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上で、Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による<a href='https://codepen.io/azuremaps/pen/WKOQRq/'>マップの読み込み時にスタイルを設定する</a>ペンを表示します。
</iframe>

### <a name="update-the-base-map-style"></a>基本マップ スタイルを更新する

基本マップ スタイルを更新するには、`setStyle` 関数を使用して `style` オプションを設定し、別の基本マップ スタイルに変更するか、スタイル オプションを追加します。

```javascript
map.setStyle({ style: 'satellite' });
```

次のコードでは、マップ インスタンスが読み込まれた後、[setStyle](/javascript/api/azure-maps-control/atlas.map#setstyle-styleoptions-) 関数を使用して、マップのスタイルが `grayscale_dark` から `satellite` に更新されます。

<br/>

<iframe height='500' scrolling='no' title='スタイルの更新' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上で、Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による<a href='https://codepen.io/azuremaps/pen/yqXYzY/'>スタイルを更新する</a>ペンを表示します。
</iframe>

## <a name="add-the-style-picker-control"></a>スタイル ピッカー コントロールを追加する

スタイル ピッカー コントロールには、ポップアップ パネル付きの使いやすいボタンがあり、エンド ユーザーが基本スタイルを切り替えるために使用できます。

スタイル ピッカーには、`icon` と `list` という 2 つの異なるレイアウト オプションがあります。 また、スタイル ピッカーでは、`light` と `dark` という 2 つの異なるスタイル ピッカー コントロールの `style` オプションを選択できます。 この例では、スタイル ピッカーに `icon` レイアウトが使用され、基本マップ スタイルの選択リストがアイコン形式で表示されています。 スタイル ピッカー コントロールには、基本スタイルのセットとして `["road", "grayscale_light", "grayscale_dark", "night", "road_shaded_relief"]` が含まれています。 スタイル ピッカー コントロールのオプションの詳細については、「[StyleControlOptions](/javascript/api/azure-maps-control/atlas.stylecontroloptions)」を参照してください。

次の画像は、`icon` レイアウトで表示されるスタイル ピッカー コントロールを示しています。

:::image type="content" source="./media/choose-map-style/style-picker-icon-layout.png" alt-text="スタイル選択機能アイコンのレイアウト":::

次の画像は、`list` レイアウトで表示されるスタイル ピッカー コントロールを示しています。

:::image type="content" source="./media/choose-map-style/style-picker-list-layout.png" alt-text="スタイル選択機能の一覧のレイアウト":::

> [!IMPORTANT]
> 既定では、スタイル ピッカー コントロールには Azure Maps の S0 価格レベルで利用できるすべてのスタイルが一覧表示されます。 この一覧に表示されるスタイルの数を減らすには、一覧に表示するスタイルの配列をスタイルの選択ツールの `mapStyle` オプションに渡します。 S1 を使用していて、使用できるすべてのスタイルを表示する場合は、スタイル選択ツールの `mapStyles` オプションを `"all"` に設定します。

次のコードは、既定の `mapStyles` 基本スタイル一覧をオーバーライドする方法を示しています。 この例では、スタイル ピッカー コントロールに表示される基本スタイルを列挙するように `mapStyles` オプションを設定しています。

<br/>

<iframe height='500' scrolling='no' title='スタイル選択機能の追加' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上で、Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による<a href='https://codepen.io/azuremaps/pen/OwgyvG/'>スタイル選択機能を追加する</a>ペンを表示します。
</iframe>

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [StyleOptions](/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol)

> [!div class="nextstepaction"]
> [StyleControlOptions](/javascript/api/azure-maps-control/atlas.stylecontroloptions)

マップに追加できる他のコード サンプルについては、次の記事をご覧ください。

> [!div class="nextstepaction"]
> [マップ コントロールを追加する](map-add-controls.md)

> [!div class="nextstepaction"]
> [シンボル レイヤーを追加する](map-add-pin.md)

> [!div class="nextstepaction"]
> [バブル レイヤーを追加する](map-add-bubble-layer.md)
