---
title: Azure Maps Web SDK の画像テンプレート | Microsoft Docs
description: Azure Maps Web SDK で画像テンプレートを使用する方法について説明します。
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: b9b1543ca37c636f4a82ff9ada3dfe212fa9b8d0
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976665"
---
# <a name="how-to-use-image-templates"></a>イメージ テンプレートの使用方法

画像は、Azure Maps Web SDK 内の HTML マーカーとさまざまなレイヤーで使用できます。

 - シンボル レイヤーでは、画像アイコンを使用してマップ上にポイントをレンダリングできます。 線のパスに沿ってシンボルをレンダリングすることもできます。
 - 塗りつぶしパターン画像を使用して多角形レイヤーをレンダリングできます。 
 - HTML マーカーでは、画像やその他の HTML 要素を使用してポイントをレンダリングできます。

レイヤーを使用して良いパフォーマンスを確保するには、レンダリングする前に、このような画像をマップ画像スプライト リソースに読み込む必要があります。 SymbolLayer の [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions) では、既定でいくつかの色の数個のマーカー画像をマップ画像スプライトに事前に読み込みます。 このような同じマーカー画像などは SVG テンプレートとして使用できます。また、カスタム スケールを持つ画像の作成や、お客様の原色と補色に使用できます。 合計で 42 個の画像テンプレートが用意されています。27 個のシンボル アイコンと、15 個の多角形の塗りつぶしパターンです。

画像テンプレートをマップ画像スプライト リソースに追加するには、`map.imageSprite.createFromTemplate` 関数を使用します。 この関数では、最大 5 つのパラメーターを渡すことができます。

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

ここで、`id` は、マップ画像スプライトに追加されるときに画像に割り当てられる一意の識別子です。 レイヤーでこの識別子を使用して、レンダリングする画像リソースを指定します。 `templateName` では、使用する画像テンプレートを指定します。 `color` オプションでは画像の原色を設定し、`secondaryColor` オプションでは画像の補色を設定します。 `scale` オプションでは、画像スプライトに適用する前に画像テンプレートを拡大縮小します。 画像が画像スプライトに適用されると、画像は PNG に変換されます。 上質なレンダリングを確保するには、画像テンプレートをレイヤーで拡大するよりも、画像テンプレートを拡大してからスプライトに追加することをお勧めします。

この関数では、画像を画像スプライトに非同期的に読み込みます。その結果、この関数が完了するまで待機できる Promise が返されます。

次のコードは、組み込みテンプレートのいずれかから画像を作成し、それをシンボル レイヤーと共に使用する方法を示しています。

```javascript
map.imageSprite.createFromTemplate('myTemplatedIcon', 'marker-flat', 'teal', '#fff').then(function () {

    //Add a symbol layer that uses the custom created icon.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'myTemplatedIcon'
        }
    }));
});
```

## <a name="use-an-image-template-with-a-symbol-layer"></a>シンボル レイヤーと共に画像テンプレートを使用する

画像テンプレートをマップ画像スプライトに読み込んだ後は、`iconOptions` の `image` オプションで画像リソース ID を参照することで、シンボル レイヤーにシンボルとしてレンダリングすることができます。

次のサンプルでは、原色が青緑で補色が白の `marker-flat` 画像テンプレートを使用して、シンボル レイヤーをレンダリングします。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="組み込みのアイコン テンプレートを使用したシンボル レイヤー" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/VoQMPp/'>Symbol layer with built-in icon template (組み込みのアイコン テンプレートを使用したシンボル レイヤー)</a>」を参照してください。
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>線のパスに沿って画像テンプレートを使用する

画像テンプレートをマップ画像スプライトに読み込んだ後は、LineString をデータ ソースに追加し、シンボル レイヤーを `lineSpacing` オプションと共に使用し、`iconOptions` の `image` オプションで画像リソースの ID を参照することで、線のパスに沿ってレンダリングすることができます。 

次のサンプルでは、マップ上でピンク色の線をレンダリングし、原色がドジャー ブルーで補色が白の `car` 画像テンプレートを使用して、シンボル レイヤーを使用します。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="組み込みのアイコン テンプレートを使用した線レイヤー" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/KOQvJe/'>Line layer with built-in icon template (組み込みのアイコン テンプレートを使用した線レイヤー)</a>」を参照してください。
</iframe>

> [!TIP]
> 画像テンプレートが上向きの場合は、線と同じ方向を指すようにするには、シンボル レイヤーの `rotation` アイコン オプションを 90 に設定します。

## <a name="use-an-image-template-with-a-polygon-layer"></a>多角形レイヤーで画像テンプレートを使用する

画像テンプレートをマップ画像スプライトに読み込んだ後は、レイヤーの `fillPattern` オプションで画像リソース ID を参照することで、多角形レイヤーに塗りつぶしパターンとしてレンダリングすることができます。

次のサンプルでは、原色が赤で補色が透明の `dot` 画像テンプレートを使用して、多角形レイヤーをレンダリングします。  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="組み込みのアイコン テンプレートを使用して多角形を塗りつぶす" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/WVMEmz/'>Fill polygon with built-in icon template (組み込みのアイコン テンプレートを使用してポリゴンを塗りつぶす)</a>」を参照してください。
</iframe>

> [!TIP]
> 塗りつぶしパターンの補色を設定すると、基のマップでプライマリ パターンが提供されることを確認しやすくなります。 

## <a name="use-an-image-template-with-an-html-marker"></a>HTML マーカーと共に画像テンプレートを使用する

画像テンプレートは、`altas.getImageTemplate` 関数を使用して取得できます。また、HTML マーカーのコンテンツとして使用できます。 このテンプレートは、マーカーの `htmlContent` オプションに渡してから、`color`、`secondaryColor`、および `text` オプションを使用してカスタマイズすることができます。

次のサンプルでは、原色が赤、補色がピンク、テキスト値が "00" の`marker-arrow` テンプレートを使用します。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="組み込みのアイコン テンプレートを使用した HTML マーカー" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/EqQvzq/'>HTML Marker with built-in icon template (組み込みのアイコン テンプレートを使用した HTML マーカー)</a>」を参照してください。
</iframe>

## <a name="create-custom-reusable-templates"></a>再利用可能なカスタム テンプレートを作成する

アプリケーションで同じアイコンと異なるアイコンを使用する場合、またはさらに画像テンプレートを追加するモジュールを作成する場合、`atlas` 名前空間上で次の静的関数を使用して、Azure Maps Web SDK からこれらのアイコンを簡単に追加および取得できます。

| EnableAdfsAuthentication | 戻り値の型 | 説明 | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | カスタム SVG 画像テンプレートを atlas 名前空間に追加します。 |
|  `getImageTemplate(templateName: string, scale?: number)`| string | 名前を指定して SVG テンプレートを取得します。 |
| `getAllImageTemplateNames()` | string[] |  名前を指定して SVG テンプレートを取得します。 |

SVG 画像テンプレートでは、次のプレースホルダー値がサポートされています。

| プレースホルダー | 説明 |
|-|-|
| `{color}` | 原色。 | 
| `{secondaryColor}` | 補色。 | 
| `{scale}` | SVG 画像は、マップ画像スプライトに追加されると png 画像に変換されます。 このプレースホルダーを使用すると、テンプレートがきれいにレンダリングされるように、変換する前にテンプレートを拡大縮小できます。 | 
| `{text}` | HTML マーカーと共に使用する場合にテキストを表示する位置。 |

次の例は、SVG テンプレートを使用して、再利用可能なアイコン テンプレートとして Azure Maps Web SDK に追加する方法を示しています。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="カスタム アイコン テンプレートを atlas 名前空間に追加する" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/NQyvEX/'>Add custom icon template to atlas namespace (カスタム アイコン テンプレートを atlas 名前空間に追加する)</a>」を参照してください。
</iframe>

## <a name="list-of-image-templates"></a>画像テンプレートの一覧

次の表は、Azure Maps Web SDK 内で現在使用できるすべての画像テンプレートの一覧です。各画像の上にテンプレート名を示します。 既定では、原色は青であり、補色は白です。 白の背景で補色を見やすくするために、次の画像では補色が黒に設定されています。

**シンボル アイコン テンプレート**

|||||
|:-:|:-:|:-:|:-:|
| marker | marker-thick | marker-circle | marker-flat |
|![marker アイコン](./media/image-templates/marker.png)|![marker-thick アイコン](./media/image-templates/marker-thick.png)|![marker-circle アイコン](./media/image-templates/marker-circle.png)|![marker-flat アイコン](./media/image-templates/marker-flat.png)|
||||
| marker-square | marker-square-cluster | marker-arrow | marker-ball-pin | 
|![marker-square アイコン](./media/image-templates/marker-square.png)|![marker-square-cluster アイコン](./media/image-templates/marker-square-cluster.png)|![marker-arrow アイコン](./media/image-templates/marker-arrow.png)|![marker-ball-pin アイコン](./media/image-templates/marker-ball-pin.png)|
||||
| marker-square-rounded | marker-square-rounded-cluster | フラグ | flag-triangle |
| ![marker-square-rounded アイコン](./media/image-templates/marker-square-rounded.png) | ![marker-square-rounded-cluster アイコン](./media/image-templates/marker-square-rounded-cluster.png) | ![flag アイコン](./media/image-templates/flag.png) | ![flag-triangle アイコン](./media/image-templates/flag-triangle.png) |
||||
| triangle | triangle-thick | triangle-arrow-up | triangle-arrow-left |
| ![triangle アイコン](./media/image-templates/triangle.png) | ![triangle-thick アイコン](./media/image-templates/triangle-thick.png) | ![triangle-arrow-up アイコン](./media/image-templates/triangle-arrow-up.png) | ![triangle-arrow-left アイコン](./media/image-templates/triangle-arrow-left.png) |
||||
| hexagon | hexagon-thick | hexagon-rounded | hexagon-rounded-thick |
| ![hexagon アイコン](./media/image-templates/hexagon.png) | ![hexagon-thick アイコン](./media/image-templates/hexagon-thick.png) | ![hexagon-rounded アイコン](./media/image-templates/hexagon-rounded.png) | ![hexagon-rounded-thick アイコン](./media/image-templates/hexagon-rounded-thick.png) |
||||
| pin | pin-round | rounded-square | rounded-square-thick |
| ![pin アイコン](./media/image-templates/pin.png) | ![pin-round アイコン](./media/image-templates/pin-round.png) | ![rounded-square アイコン](./media/image-templates/rounded-square.png) | ![rounded-square-thick アイコン](./media/image-templates/rounded-square-thick.png) |
||||
| arrow-up | arrow-up-thin | car ||
| ![arrow-up アイコン](./media/image-templates/arrow-up.png) | ![arrow-up-thin アイコン](./media/image-templates/arrow-up-thin.png) | ![car アイコン](./media/image-templates/car.png) | |

**多角形塗りつぶしパターン テンプレート**

|||||
|:-:|:-:|:-:|:-:|
| checker | checker-rotated | circles | circles-spaced |
| ![checker アイコン](./media/image-templates/checker.png) | ![checker-rotated アイコン](./media/image-templates/checker-rotated.png) | ![circles アイコン](./media/image-templates/circles.png) | ![circles-spaced アイコン](./media/image-templates/circles-spaced.png) |
|||||
| diagonal-lines-up | diagonal-lines-down | diagonal-stripes-up | diagonal-stripes-down |
| ![diagonal-lines-up アイコン](./media/image-templates/diagonal-lines-up.png) | ![diagonal-lines-down アイコン](./media/image-templates/diagonal-lines-down.png) | ![diagonal-stripes-up アイコン](./media/image-templates/diagonal-stripes-up.png) | ![diagonal-stripes-down アイコン](./media/image-templates/diagonal-stripes-down.png) |
|||||
| grid-lines | rotated-grid-lines | rotated-grid-stripes | x-fill |
| ![grid-lines アイコン](./media/image-templates/grid-lines.png) | ![rotated-grid-lines アイコン](./media/image-templates/rotated-grid-lines.png) | ![rotated-grid-stripes アイコン](./media/image-templates/rotated-grid-stripes.png) | ![x-fill アイコン](./media/image-templates/x-fill.png) |
|||||
| zig-zag | zig-zag-vertical | dots |  |
| ![zig-zag アイコン](./media/image-templates/zig-zag.png) | ![zig-zag-vertical アイコン](./media/image-templates/zig-zag-vertical.png) | ![dots アイコン](./media/image-templates/dots.png) | |

## <a name="try-it-now-tool"></a>すぐに試せるツール

次のツールを使用すると、さまざまな方法でさまざまな組み込みの画像テンプレートをレンダリングし、原色、補色、およびスケールをカスタマイズできます。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="アイコン テンプレートのオプション" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/NQyaaO/'>Icon template options (アイコン テンプレート オプション)</a>」を参照してください。
</iframe>

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [ImageSpriteManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [atlas 名前空間](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-maps-typescript-latest#functions
)

画像テンプレートを使用できるコード サンプルの詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [シンボル レイヤーを追加する](map-add-pin.md)

> [!div class="nextstepaction"]
> [線レイヤーを追加する](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [多角形レイヤーを追加する](map-add-shape.md)

> [!div class="nextstepaction"]
> [HTML マーカーを追加する](map-add-bubble-layer.md)
