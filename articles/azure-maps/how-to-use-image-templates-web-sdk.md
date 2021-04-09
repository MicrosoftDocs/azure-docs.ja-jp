---
title: Azure Maps Web SDK の画像テンプレート | Microsoft Azure Maps
description: Azure Maps Web SDK を使用して、マップに画像アイコンと塗りつぶしパターンの多角形を追加する方法について学習します。 使用可能なイメージと塗りつぶしパターンのテンプレートを表示します。
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 5f455a1132a0f63e1ba3eb5d2a57a1f9bfa9a867
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895683"
---
# <a name="how-to-use-image-templates"></a>イメージ テンプレートの使用方法

画像は、Azure Maps Web SDK 内の HTML マーカーおよび のさまざまなレイヤーで使用できます。

 - シンボル レイヤーでは、画像アイコンを使用してマップ上にポイントをレンダリングできます。 線のパスに沿ってシンボルをレンダリングすることもできます。
 - 塗りつぶしパターン画像を使用して多角形レイヤーをレンダリングできます。 
 - HTML マーカーでは、画像やその他の HTML 要素を使用してポイントをレンダリングできます。

レイヤーを使用して良いパフォーマンスを確保するには、レンダリングする前に、画像をマップ画像スプライト リソースに読み込みます。 SymbolLayer の [IconOptions](/javascript/api/azure-maps-control/atlas.iconoptions) では、既定でいくつかの色の数個のマーカー画像をマップ画像スプライトに事前に読み込みます。 これらのマーカー画像などは、SVG テンプレートとして使用できます。 カスタム スケールを使用して画像を作成するために使用したり、顧客の第一および第二の色として使用したりすることができます。 合計で 42 の画像テンプレートが用意されています。27 個の記号アイコンと 15 個の多角形の塗りつぶしパターン。

画像テンプレートをマップ画像スプライト リソースに追加するには、`map.imageSprite.createFromTemplate` 関数を使用します。 この関数では、最大 5 つのパラメーターを渡すことができます。

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

`id` は、作成する一意の識別子です。 `id` は、マップ画像スプライトに追加されるときに、画像に割り当てられます。 レイヤーでこの識別子を使用して、レンダリングする画像リソースを指定します。 `templateName` では、使用する画像テンプレートを指定します。 `color` オプションでは画像の原色を設定し、`secondaryColor` オプションでは画像の補色を設定します。 `scale` オプションでは、画像スプライトに適用する前に画像テンプレートを拡大縮小します。 画像が画像スプライトに適用されると、これは PNG に変換されます。 上質なレンダリングを確保するには、画像テンプレートをレイヤーで拡大するよりも、拡大してからスプライトに追加することをお勧めします。

この関数では、画像が画像スプライトに非同期的に読み込まれます。 したがって、この関数は、それが完了するまで待機できる Promise を返します。

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

次のサンプルでは、第一の色が青緑で第二の色が白の `marker-flat` 画像テンプレートを使用して、シンボル レイヤーをレンダリングします。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="組み込みのアイコン テンプレートを使用したシンボル レイヤー" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/VoQMPp/'>Symbol layer with built-in icon template (組み込みのアイコン テンプレートを使用したシンボル レイヤー)</a>」を参照してください。
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>線のパスに沿って画像テンプレートを使用する

画像テンプレートをマップ画像スプライトに読み込んだ後は、LineString をデータ ソースに追加し、シンボル レイヤーを `lineSpacing` オプションと共に使用し、`iconOptions` の `image` オプションで画像リソースの ID を参照することで、線のパスに沿ってレンダリングすることができます。 

次のサンプルでは、マップ上でピンク色の線をレンダリングし、第一の色がドジャー ブルーで第二の色が白の `car` 画像テンプレートを使用して、シンボル レイヤーを使用します。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="組み込みのアイコン テンプレートを使用した線レイヤー" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/KOQvJe/'>Line layer with built-in icon template (組み込みのアイコン テンプレートを使用した線レイヤー)</a>」を参照してください。
</iframe>

> [!TIP]
> 画像テンプレートが上向きの場合は、線と同じ方向を指すようにするには、シンボル レイヤーの `rotation` アイコン オプションを 90 に設定します。

## <a name="use-an-image-template-with-a-polygon-layer"></a>多角形レイヤーで画像テンプレートを使用する

画像テンプレートをマップ画像スプライトに読み込んだ後は、レイヤーの `fillPattern` オプションで画像リソース ID を参照することで、多角形レイヤーに塗りつぶしパターンとしてレンダリングすることができます。

次のサンプルでは、原色が赤で補色が透明の `dot` 画像テンプレートを使用して、多角形レイヤーをレンダリングします。  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="組み込みのアイコン テンプレートを使用して多角形を塗りつぶす" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/WVMEmz/'>Fill polygon with built-in icon template (組み込みのアイコン テンプレートを使用してポリゴンを塗りつぶす)</a>」を参照してください。
</iframe>

> [!TIP]
> 塗りつぶしパターンの第二の色を設定すると、基のマップでプライマリ パターンが提供されることを確認しやすくなります。 

## <a name="use-an-image-template-with-an-html-marker"></a>HTML マーカーと共に画像テンプレートを使用する

画像テンプレートは、`altas.getImageTemplate` 関数を使用して取得できます。また、HTML マーカーのコンテンツとして使用できます。 このテンプレートは、マーカーの `htmlContent` オプションに渡してから、`color`、`secondaryColor`、および `text` オプションを使用してカスタマイズすることができます。

次のサンプルでは、第一の色が赤、第二の色がピンク、テキスト値が "00" の`marker-arrow` テンプレートを使用します。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="組み込みのアイコン テンプレートを使用した HTML マーカー" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/EqQvzq/'>HTML Marker with built-in icon template (組み込みのアイコン テンプレートを使用した HTML マーカー)</a>」を参照してください。
</iframe>


> [!TIP]
> 画像テンプレートはマップの外でも使用できます。 getImageTemplate 関数からは `{color}`、`{secondaryColor}`、`{scale}`、`{text}` プレースホルダーのある SVG 文字列が返されます。 これらのプレースホルダーを置換し、有効な SVG 文字列を作成します。 その後、SVG 文字列を HTML DOM に直接追加するか、それをデータ URI に変換し、イメージ タグに挿入できます。 次に例を示します。
> ```JavaScript
> //Retrieve an SVG template and replace the placeholder values.
> var svg = atlas.getImageTemplate('marker').replace(/{color}/, 'red').replace(/{secondaryColor}/, 'white').replace(/{text}/, '').replace(/{scale}/, 1);
>
> //Convert to data URI for use in image tags.
> var dataUri = 'data:image/svg+xml;base64,' + btoa(svg);
> ```

## <a name="create-custom-reusable-templates"></a>再利用可能なカスタム テンプレートを作成する

アプリケーションで同じアイコンと異なるアイコンを使用する場合、またはさらに画像テンプレートを追加するモジュールを作成する場合、Azure Maps Web SDK からこれらのアイコンを簡単に追加および取得できます。 以下の静的関数は `atlas` 名前空間で使用します。

| 名前 | 戻り値の型 | 説明 | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | カスタム SVG 画像テンプレートを atlas 名前空間に追加します。 |
|  `getImageTemplate(templateName: string, scale?: number)`| string | 名前を指定して SVG テンプレートを取得します。 |
| `getAllImageTemplateNames()` | string[] |  名前を指定して SVG テンプレートを取得します。 |

SVG 画像テンプレートでは、次のプレースホルダー値がサポートされています。

| プレースホルダー | 説明 |
|-|-|
| `{color}` | 第一の色。 | 
| `{secondaryColor}` | 補色。 | 
| `{scale}` | SVG 画像は、マップ画像スプライトに追加されると png 画像に変換されます。 このプレースホルダーを使用すると、テンプレートがきれいにレンダリングされるように、変換する前にテンプレートを拡大縮小できます。 | 
| `{text}` | HTML マーカーと共に使用する場合にテキストを表示する位置。 |

次の例は、SVG テンプレートを使用して、再利用可能なアイコン テンプレートとして Azure Maps Web SDK に追加する方法を示しています。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="カスタム アイコン テンプレートを atlas 名前空間に追加する" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/NQyvEX/'>Add custom icon template to atlas namespace (カスタム アイコン テンプレートを atlas 名前空間に追加する)</a>」を参照してください。
</iframe>

## <a name="list-of-image-templates"></a>画像テンプレートの一覧

次の表は、Azure Maps Web SDK 内で現在使用できるすべての画像テンプレートの一覧です。 各画像の上にテンプレート名が示されています。 既定では、第一の色は青であり、第二の色は白です。 白の背景で第二の色を見やすくするために、次の画像では第二の色が黒に設定されています。

**シンボル アイコン テンプレート**

:::row:::
   :::column span="":::
      marker
   :::column-end:::
   :::column span="":::
      marker-thick
   :::column-end:::
   :::column span="":::
      marker-circle
   :::column-end:::
   :::column span="":::
      marker-flat
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![marker アイコン](./media/image-templates/marker.png)
   :::column-end:::
   :::column span="":::
      ![marker-thick アイコン](./media/image-templates/marker-thick.png)
   :::column-end:::
   :::column span="":::
      ![marker-circle アイコン](./media/image-templates/marker-circle.png)
   :::column-end:::
   :::column span="":::
      ![marker-flat アイコン](./media/image-templates/marker-flat.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      marker-square
   :::column-end:::
   :::column span="":::
      marker-square-cluster
   :::column-end:::
   :::column span="":::
      marker-arrow
   :::column-end:::
   :::column span="":::
      marker-ball-pin
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![marker-square アイコン](./media/image-templates/marker-square.png)
   :::column-end:::
   :::column span="":::
      ![marker-square-cluster アイコン](./media/image-templates/marker-square-cluster.png)
   :::column-end:::
   :::column span="":::
      ![marker-arrow アイコン](./media/image-templates/marker-arrow.png)
   :::column-end:::
   :::column span="":::
      ![marker-ball-pin アイコン](./media/image-templates/marker-ball-pin.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      marker-square-rounded
   :::column-end:::
   :::column span="":::
      marker-square-rounded-cluster
   :::column-end:::
   :::column span="":::
      フラグ
   :::column-end:::
   :::column span="":::
      flag-triangle
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![marker-square-rounded アイコン](./media/image-templates/marker-square-rounded.png)
   :::column-end:::
   :::column span="":::
      ![marker-square-rounded-cluster アイコン](./media/image-templates/marker-square-rounded-cluster.png)
   :::column-end:::
   :::column span="":::
      ![flag アイコン](./media/image-templates/flag.png)
   :::column-end:::
   :::column span="":::
      ![flag-triangle アイコン](./media/image-templates/flag-triangle.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      triangle
   :::column-end:::
   :::column span="":::
      triangle-thick
   :::column-end:::
   :::column span="":::
      triangle-arrow-up
   :::column-end:::
   :::column span="":::
      triangle-arrow-left
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![triangle アイコン](./media/image-templates/triangle.png)
   :::column-end:::
   :::column span="":::
      ![triangle-thick アイコン](./media/image-templates/triangle-thick.png)
   :::column-end:::
   :::column span="":::
      ![triangle-arrow-up アイコン](./media/image-templates/triangle-arrow-up.png)
   :::column-end:::
   :::column span="":::
      ![triangle-arrow-left アイコン](./media/image-templates/triangle-arrow-left.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      hexagon
   :::column-end:::
   :::column span="":::
      hexagon-thick
   :::column-end:::
   :::column span="":::
      hexagon-rounded
   :::column-end:::
   :::column span="":::
      hexagon-rounded-thick
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![hexagon アイコン](./media/image-templates/hexagon.png)
   :::column-end:::
   :::column span="":::
      ![hexagon-thick アイコン](./media/image-templates/hexagon-thick.png)
   :::column-end:::
   :::column span="":::
      ![hexagon-rounded アイコン](./media/image-templates/hexagon-rounded.png)
   :::column-end:::
   :::column span="":::
      ![hexagon-rounded-thick アイコン](./media/image-templates/hexagon-rounded-thick.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      pin
   :::column-end:::
   :::column span="":::
      pin-round
   :::column-end:::
   :::column span="":::
      rounded-square
   :::column-end:::
   :::column span="":::
      rounded-square-thick
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![pin アイコン](./media/image-templates/pin.png)
   :::column-end:::
   :::column span="":::
      ![pin-round アイコン](./media/image-templates/pin-round.png)
   :::column-end:::
   :::column span="":::
      ![rounded-square アイコン](./media/image-templates/rounded-square.png)
   :::column-end:::
   :::column span="":::
      ![rounded-square-thick アイコン](./media/image-templates/rounded-square-thick.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      arrow-up
   :::column-end:::
   :::column span="":::
      arrow-up-thin
   :::column-end:::
   :::column span="":::
      car
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![arrow-up アイコン](./media/image-templates/arrow-up.png)
   :::column-end:::
   :::column span="":::
      ![arrow-up-thin アイコン](./media/image-templates/arrow-up-thin.png)
   :::column-end:::
   :::column span="":::
      ![car アイコン](./media/image-templates/car.png)
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::


**多角形塗りつぶしパターン テンプレート**

:::row:::
   :::column span="":::
      checker
   :::column-end:::
   :::column span="":::
      checker-rotated
   :::column-end:::
   :::column span="":::
      circles
   :::column-end:::
   :::column span="":::
      circles-spaced
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![checker アイコン](./media/image-templates/checker.png)
   :::column-end:::
   :::column span="":::
      ![checker-rotated アイコン](./media/image-templates/checker-rotated.png)
   :::column-end:::
   :::column span="":::
      ![circles アイコン](./media/image-templates/circles.png)
   :::column-end:::
   :::column span="":::
      ![circles-spaced アイコン](./media/image-templates/circles-spaced.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      diagonal-lines-up
   :::column-end:::
   :::column span="":::
      diagonal-lines-down
   :::column-end:::
   :::column span="":::
      diagonal-stripes-up
   :::column-end:::
   :::column span="":::
      diagonal-stripes-down
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![diagonal-lines-up アイコン](./media/image-templates/diagonal-lines-up.png)
   :::column-end:::
   :::column span="":::
      ![diagonal-lines-down アイコン](./media/image-templates/diagonal-lines-down.png)
   :::column-end:::
   :::column span="":::
      ![diagonal-stripes-up アイコン](./media/image-templates/diagonal-stripes-up.png)
   :::column-end:::
   :::column span="":::
      ![diagonal-stripes-down アイコン](./media/image-templates/diagonal-stripes-down.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      grid-lines
   :::column-end:::
   :::column span="":::
      rotated-grid-lines
   :::column-end:::
   :::column span="":::
      rotated-grid-stripes
   :::column-end:::
   :::column span="":::
      x-fill
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![grid-lines アイコン](./media/image-templates/grid-lines.png)
   :::column-end:::
   :::column span="":::
      ![rotated-grid-lines アイコン](./media/image-templates/rotated-grid-lines.png)
   :::column-end:::
   :::column span="":::
      ![rotated-grid-stripes アイコン](./media/image-templates/rotated-grid-stripes.png)
   :::column-end:::
   :::column span="":::
      ![x-fill アイコン](./media/image-templates/x-fill.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      zig-zag
   :::column-end:::
   :::column span="":::
      zig-zag-vertical
   :::column-end:::
   :::column span="":::
      dots
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![zig-zag アイコン](./media/image-templates/zig-zag.png)
   :::column-end:::
   :::column span="":::
      ![zig-zag-vertical アイコン](./media/image-templates/zig-zag-vertical.png)
   :::column-end:::
   :::column span="":::
      ![dots アイコン](./media/image-templates/dots.png)
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
<br>

**事前に読み込まれるイメージ アイコン**

マップによって、`marker`、`pin`、`pin-round` テンプレートを使用し、マップ イメージ スプライトに一連のアイコンが事前読み込みされます。 このようなアイコンの名前と色を下の表にまとめています。

| アイコン名 | color | secondaryColor |
|-----------|-------|----------------|
| `marker-black` | `#231f20` | `#ffffff` |
| `marker-blue` | `#1a73aa` | `#ffffff` |
| `marker-darkblue` | `#003963` | `#ffffff` |
| `marker-red` | `#ef4c4c` | `#ffffff` |
| `marker-yellow` | `#f2c851` | `#ffffff` |
| `pin-blue` | `#2072b8` | `#ffffff` |
| `pin-darkblue` | `#003963` | `#ffffff` |
| `pin-red` | `#ef4c4c` | `#ffffff` |
| `pin-round-blue` | `#2072b8` | `#ffffff` |
| `pin-round-darkblue` | `#003963` | `#ffffff` |
| `pin-round-red` | `#ef4c4c` | `#ffffff` |


## <a name="try-it-now-tool"></a>すぐに試せるツール

次のツールを使用すると、さまざまな方法でさまざまな組み込みの画像テンプレートをレンダリングし、原色、補色、およびスケールをカスタマイズできます。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="アイコン テンプレートのオプション" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/NQyaaO/'>Icon template options (アイコン テンプレート オプション)</a>」を参照してください。
</iframe>

## <a name="next-steps"></a>次のステップ

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [ImageSpriteManager](/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [atlas 名前空間](/javascript/api/azure-maps-control/atlas#functions
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