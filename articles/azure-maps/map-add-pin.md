---
title: Azure Maps にシンボル レイヤーを追加する | Microsoft Docs
description: Azure Maps Web SDK にシンボルを追加する方法について説明します。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 10f6a7ef92bfd6558ed93e7fb40df9e48e1b92f5
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976171"
---
# <a name="add-a-symbol-layer-to-a-map"></a>マップにシンボル レイヤーを追加する

シンボルは、データ ソースに接続し、特定のポイントにアイコンやテキストをレンダリングするために使用できます。 シンボル レイヤーは WebGL を使用してレンダリングされ、マップにポイントの大きな集合をレンダリングするために使用できます。 このレイヤーでは、HTML マーカーを使用した場合よりも優れたパフォーマンスで、はるかに多くのポイント データをマップにレンダリングできます。 ただし、シンボル レイヤーはスタイル設定用の従来の CSS および HTML 要素をサポートしていません。  

> [!TIP]
> シンボル レイヤーでは、既定ではデータ ソース内のすべてのジオメトリの座標がレンダリングされます。 ポイント ジオメトリ フィーチャーのみがレンダリングされるようにレイヤーを制限するには、レイヤーの `filter` プロパティを `['==', ['geometry-type'], 'Point']` に設定します。または、MultiPoint フィーチャーも含める場合は、`['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` に設定します。

## <a name="add-a-symbol-layer"></a>シンボル レイヤーを追加する

マップにシンボル レイヤーを追加してデータをレンダリングするには、最初にデータ ソースを作成してマップに追加する必要があります。 その後、シンボル レイヤーを作成して、データの取得元のデータ ソースに渡すことができます。 最後に、データをデータ ソースに追加して、レンダリングするものが存在するようにする必要があります。 次のコードは、シンボル レイヤーを使用してマップ上に 1 つのポイントをレンダリングするために、読み込まれた後にマップに追加する必要があるコードを示しています。 

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
- `atlas.Shape` クラスは、GeoJSON ジオメトリと、ジオメトリに関連するメタデータを含むプロパティのセットで構成されるという点で、GeoJSON 機能に似ています。 GeoJSON オブジェクトがデータ ソースに追加されると、レイヤーで簡単にレンダリングできますが、その GeoJSON オブジェクトの座標プロパティが更新された場合、JSON オブジェクトには更新をトリガーするメカニズムがないため、データ ソースとマップは変更されません。 Shape クラスには、含まれているデータを更新するための関数が用意されています。また、変更が行われると、データ ソースとマップは自動的に通知され、更新されます。 

次のコード サンプルは、GeoJSON Point ジオメトリを作成し、`atlas.Shape` クラスに渡すことで、更新を容易にしています。 シンボルを最初にレンダリングするのにマップの中心が使用されます。 マップにクリック イベントが追加され、それが実行されると、マウスをクリックした位置の座標が、マップ上のシンボルの場所を更新するシェイプの `setCoordinates` 関数で使用されます。

<br/>

<iframe height='500' scrolling='no' title='ピンの場所の切り替え' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>Switch pin location</a>」Pen を表示します。
</iframe>

> [!TIP]
> 既定では、パフォーマンスのために、重複するシンボルがシンボル レイヤーによって非表示になり、シンボルのレンダリングが最適化されます。 非表示のシンボルを拡大すると、表示されるようになります。 この機能を無効にして、すべてのシンボルを常にレンダリングするには、`iconOptions` オプションの `allowOverlap` プロパティを `true` に設定します。

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>シンボル レイヤーにカスタム アイコンを追加する

シンボル レイヤーは WebGL を使用してレンダリングされます。 このため、アイコンの画像などのすべてのリソースを WebGL コンテキストに読み込む必要があります。 このサンプルでは、カスタム アイコンをマップ リソースに追加した後、それを使用してカスタム シンボルとポイント データをマップ上にレンダリングする方法を示します。 シンボル レイヤーの `textField` プロパティに式を指定する必要があります。 このケースでは、温度プロパティをレンダリングします。ただし、このプロパティは数値なので、文字列に変換する必要があります。 加えて、そこに "°F" を追加したいと思います。 `['concat', ['to-string', ['get', 'temperature']], '°F']` という式を使えば、それを実現できます。 

<br/>

<iframe height='500' scrolling='no' title='カスタム シンボル画像アイコン' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>Custom Symbol Image Icon (カスタム シンボル画像アイコン)</a>」Pen を表示します。
</iframe>

> [!TIP]
> Azure Maps Web SDK には、シンボル レイヤーで使用できるカスタマイズ可能なイメージ テンプレートがいくつか用意されています。 詳細については、「[イメージ テンプレートの使用方法](how-to-use-image-templates-web-sdk.md)」のドキュメントを参照してください。

## <a name="customize-a-symbol-layer"></a>シンボル レイヤーをカスタマイズする 

シンボル レイヤーには、利用できるさまざまなスタイル設定オプションがあります。 さまざまなスタイル設定オプションをテストするためのツールを次に示します。

<br/>

<iframe height='700' scrolling='no' title='シンボル レイヤーのオプション' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/PxVXje/'>Symbol Layer Options (シンボル レイヤーのオプション)</a>」Pen を表示します。
</iframe>

> [!TIP]
> シンボル レイヤーを使用してテキストをレンダリングするだけの場合は、アイコンのオプションの `image` プロパティを `'none'` に設定することによって、アイコンを非表示にすることができます。

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TextOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

マップに追加できる他のコード サンプルについては、次の記事をご覧ください。

> [!div class="nextstepaction"]
> [データ ソースを作成する](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [ポップアップを追加する](map-add-popup.md)

> [!div class="nextstepaction"]
> [データドリブンのスタイルの式を使用する](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [イメージ テンプレートの使用方法](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [線レイヤーを追加する](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [多角形レイヤーを追加する](map-add-shape.md)

> [!div class="nextstepaction"]
> [バブル レイヤーを追加する](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [HTML マーカーを追加する](map-add-bubble-layer.md)
