---
title: マップにシンボル レイヤーを追加する | Microsoft Azure Maps
description: テキストやアイコンなど、カスタマイズしたシンボルをマップに追加する方法について説明します。 この目的で Azure Maps Web SDK のデータ ソースとシンボル レイヤーを使用する方法について説明します。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: c5434406af1f912c1c89123baa344dd3f9c7ff91
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92891057"
---
# <a name="add-a-symbol-layer-to-a-map"></a>マップにシンボル レイヤーを追加する

データ ソースにシンボルを接続し、それを使用して特定のポイントにアイコンやテキストをレンダリングします。 

シンボル レイヤーは WebGL を使用してレンダリングされます。 シンボル レイヤーを使用して、マップ上の多数のポイントをレンダリングします。 HTML マーカーに比べて、シンボル レイヤーでは、多数のポイント データが優れたパフォーマンスでマップ上にレンダリングされます。 ただし、シンボル レイヤーはスタイル設定用の従来の CSS および HTML 要素をサポートしていません。  

> [!TIP]
> シンボル レイヤーでは、既定ではデータ ソース内のすべてのジオメトリの座標がレンダリングされます。 ポイント ジオメトリ フィーチャーのみがレンダリングされるようにレイヤーを制限するには、レイヤーの `filter` プロパティを `['==', ['geometry-type'], 'Point']` に設定します。MultiPoint フィーチャーも含める場合は、`['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` に設定します。

シンボル レイヤーで使用されるカスタム イメージは、マップ イメージ スプライト マネージャーによって読み込まれます。 次のイメージ形式がサポートされます。

- JPEG
- PNG
- SVG
- BMP
- GIF (アニメーションなし)

## <a name="add-a-symbol-layer"></a>シンボル レイヤーを追加する

マップにシンボル レイヤーを追加する前に、いくつかの手順を行う必要があります。 まず、データ ソースを作成してマップに追加します。 シンボル レイヤーを追加します。 次に、データ ソースからデータを取得するために、データ ソースをシンボル レイヤーに渡します。 最後に、データをデータ ソースに追加して、レンダリングするものが存在するようにします。 

次のコードは、マップが読み込まれた後に、そこに追加する必要がある内容を示しています。 このサンプルでは、シンボル レイヤーを使用してマップ上に 1 つのポイントがレンダリングされます。 

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a symbol layer to render icons and/or text at points on the map.
var layer = new atlas.layer.SymbolLayer(dataSource);

//Add the layer to the map.
map.layers.add(layer);

//Create a point and add it to the data source.
dataSource.add(new atlas.data.Point([0, 0]));
```

マップに追加できるポイント データには、次の 4 種類があります。

- GeoJSON Point ジオメトリ - このオブジェクトには、ポイントの座標だけが含まれ、それ以外は含まれません。 `atlas.data.Point` ヘルパー クラスを使用すると、これらのオブジェクトを簡単に作成できます。
- GeoJSON MultiPoint ジオメトリ - このオブジェクトには、複数のポイントの座標が含まれ、それ以外は含まれません。 `atlas.data.MultiPoint` ヘルパー クラスを使用すると、これらのオブジェクトを簡単に作成できます。
- GeoJSON 機能 - このオブジェクトは、すべての GeoJSON ジオメトリと、ジオメトリに関連するメタデータを含むプロパティのセットで構成されます。 `atlas.data.Feature` ヘルパー クラスを使用すると、これらのオブジェクトを簡単に作成できます。
- `atlas.Shape` クラスは GeoJSON 機能に似ています。 どちらも、GeoJSON ジオメトリとそのジオメトリに関連するメタデータが含まれる一連のプロパティで構成されます。 GeoJSON オブジェクトがデータ ソースに追加されると、レイヤーで簡単にレンダリングできます。 ただし、その GeoJSON オブジェクトの座標プロパティが更新された場合でも、データ ソースとマップは変更されません。 これは、JSON オブジェクトには更新をトリガーするためのメカニズムがないためです。 Shape クラスには、含まれているデータを更新するための関数が用意されています。 変更が行われると、データ ソースとマップに自動的に通知され、更新が実行されます。 

次のコード サンプルは、GeoJSON Point ジオメトリを作成し、`atlas.Shape` クラスに渡すことで、更新を容易にしています。 シンボルをレンダリングするのに、最初はマップの中心が使用されます。 マップにクリック イベントが追加され、そのイベントが発生したときに、マウスの座標が `setCoordinates` 関数で使用されます。 クリック イベント時にマウスの座標が記録されます。 次に、`setCoordinates` によって、マップ上のシンボルの場所が更新されます。

<br/>

<iframe height='500' scrolling='no' title='ピンの場所の切り替え' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>Switch pin location</a>」Pen を表示します。
</iframe>

> [!TIP]
> 既定では、重なっているシンボルがシンボル レイヤーによって非表示になることで、シンボルのレンダリングが最適化されます。 拡大すると、非表示のシンボルが表示されるようになります。 この機能を無効にして、すべてのシンボルを常にレンダリングするには、`iconOptions` オプションの `allowOverlap` プロパティを `true` に設定します。

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>シンボル レイヤーにカスタム アイコンを追加する

シンボル レイヤーは WebGL を使用してレンダリングされます。 このため、アイコンの画像などのすべてのリソースを WebGL コンテキストに読み込む必要があります。 このサンプルでは、マップ リソースにカスタム アイコンを追加する方法を示します。 このアイコンを使用して、ポイント データをカスタム シンボルでマップ上にレンダリングします。 シンボル レイヤーの `textField` プロパティに式を指定する必要があります。 ここでは、気温プロパティをレンダリングします。 気温は数値であるため、文字列に変換する必要があります。 加えて、そこに "°F" を追加したいと思います。 `['concat', ['to-string', ['get', 'temperature']], '°F']` という式を使えば、この連結を実行できます。 

<br/>

<iframe height='500' scrolling='no' title='カスタム シンボル画像アイコン' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>Custom Symbol Image Icon (カスタム シンボル画像アイコン)</a>」Pen を表示します。
</iframe>

> [!TIP]
> Azure Maps Web SDK には、シンボル レイヤーで使用できるカスタマイズ可能な画像テンプレートがいくつか用意されています。 詳細については、[画像テンプレートの使用方法](how-to-use-image-templates-web-sdk.md)のドキュメントを参照してください。

## <a name="customize-a-symbol-layer"></a>シンボル レイヤーをカスタマイズする 

シンボル レイヤーには、利用できるさまざまなスタイル設定オプションがあります。 さまざまなスタイル設定オプションをテストするためのツールを次に示します。

<br/>

<iframe height='700' scrolling='no' title='シンボル レイヤーのオプション' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/PxVXje/'>Symbol Layer Options (シンボル レイヤーのオプション)</a>」Pen を表示します。
</iframe>

> [!TIP]
> シンボル レイヤーを使用してテキストをレンダリングするだけの場合は、アイコンのオプションの `image` プロパティを `'none'` に設定することでアイコンを非表示にすることができます。

## <a name="next-steps"></a>次のステップ

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [SymbolLayer](/javascript/api/azure-maps-control/atlas.layer.symbollayer)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions)

> [!div class="nextstepaction"]
> [IconOptions](/javascript/api/azure-maps-control/atlas.iconoptions)

> [!div class="nextstepaction"]
> [TextOptions](/javascript/api/azure-maps-control/atlas.textoptions)

マップに追加できる他のコード サンプルについては、次の記事をご覧ください。

> [!div class="nextstepaction"]
> [データ ソースを作成する](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [ポップアップを追加する](map-add-popup.md)

> [!div class="nextstepaction"]
> [データドリブンのスタイルの式を使用する](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [画像テンプレートの使用方法](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [線レイヤーを追加する](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [多角形レイヤーを追加する](map-add-shape.md)

> [!div class="nextstepaction"]
> [バブル レイヤーを追加する](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [HTML マーカーを追加する](map-add-bubble-layer.md)