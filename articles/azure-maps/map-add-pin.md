---
title: Azure Maps にシンボル レイヤーを追加する | Microsoft Docs
description: JavaScript マップにシンボルを追加する方法
author: rbrundritt
ms.author: richbrun
ms.date: 12/2/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 2580f1177bf9e6e3a92934f88a5d8ab51894e8d9
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2019
ms.locfileid: "59269488"
---
# <a name="add-a-symbol-layer-to-a-map"></a>マップにシンボル レイヤーを追加する

この記事では、データ ソースからのポイント データをマップ上にシンボル レイヤーとしてレンダリングする方法について説明します。 シンボル レイヤーは WebGL を使用してレンダリングされ、HTML マーカーよりはるかに多くのデータ ポイントをサポートしますが、従来の CSS と HTML 要素のスタイルはサポートしていません。  

> [!TIP]
> シンボル レイヤーでは、既定ではデータ ソース内のすべてのジオメトリの座標がレンダリングされます。 ポイント ジオメトリ フィーチャーのみがレンダリングされるようにレイヤーを制限するには、レイヤーの `filter` プロパティを `['==', ['geometry-type'], 'Point']` に設定します。または、MultiPoint フィーチャーも含める場合は、`['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` に設定します。

## <a name="add-a-symbol-layer"></a>シンボル レイヤーを追加する

<iframe height='500' scrolling='no' title='ピンの場所の切り替え' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>Switch pin location</a>」Pen を表示します。
</iframe>

上記のコードの最初のブロックでは、マップ オブジェクトが作成されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

2 番目のコード ブロックでは、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) クラスを使用して、データ ソース オブジェクトが作成されます。 [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) ジオメトリを含む [フィーチャー] は、更新を容易にするために [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) クラスによってラップされた後、データ ソースに追加されます。

3 番目のコード ブロックでは、[イベント リスナー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)が作成され、マウス クリック時にシェイプ クラスの [setCoordinates](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) メソッドを使用して、ポイントの座標が更新されます。

[シンボル レイヤー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)は、テキストまたはアイコンを使用して、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) にラップされたポイントベースのデータをシンボルとしてマップにレンダリングします。  データ ソース、クリック イベント リスナー、およびシンボル レイヤーが作成され、`ready` [イベント リスナー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)関数内でマップに追加されるため、マップが読み込まれてアクセス可能な状態になった後に、ポイントが表示されます。

> [!TIP]
> 既定では、パフォーマンスのために、重複するシンボルがシンボル レイヤーによって非表示になり、シンボルのレンダリングが最適化されます。 非表示のシンボルを拡大すると、表示されるようになります。 この機能を無効にして、すべてのシンボルを常にレンダリングするには、`iconOptions` オプションの `allowOverlap` プロパティを `true` に設定します。

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>シンボル レイヤーにカスタム アイコンを追加する

シンボル レイヤーは WebGL を使用してレンダリングされます。 このため、アイコンの画像などのすべてのリソースを WebGL コンテキストに読み込む必要があります。 このサンプルでは、カスタム アイコンをマップ リソースに追加した後、それを使用してカスタム シンボルとポイント データをマップ上にレンダリングする方法を示します。 シンボル レイヤーの `textField` プロパティに式を指定する必要があります。 このケースでは、温度プロパティをレンダリングします。ただし、このプロパティは数値なので、文字列に変換する必要があります。 加えて、そこに "°F" を追加したいと思います。 `['concat', ['to-string', ['get', 'temperature']], '°F']` という式を使えば、それを実現できます。 

<br/>

<iframe height='500' scrolling='no' title='カスタム シンボル画像アイコン' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>Custom Symbol Image Icon (カスタム シンボル画像アイコン)</a>」Pen を表示します。
</iframe>

## <a name="customize-a-symbol-layer"></a>シンボル レイヤーをカスタマイズする 

シンボル レイヤーには、利用できるさまざまなスタイル設定オプションがあります。 さまざまなスタイル設定オプションをテストするためのツールを次に示します。

<br/>

<iframe height='700' scrolling='no' title='シンボル レイヤーのオプション' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/PxVXje/'>Symbol Layer Options (シンボル レイヤーのオプション)</a>」Pen を表示します。
</iframe>

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TexTOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

マップに追加できる他のコード サンプルについては、次の記事をご覧ください。

> [!div class="nextstepaction"]
> [ポップアップを追加する](map-add-popup.md)

> [!div class="nextstepaction"]
> [データドリブンのスタイルの式を使用する](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [図形を追加する](map-add-shape.md)

> [!div class="nextstepaction"]
> [バブル レイヤーを追加する](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [HTML マーカーを追加する](map-add-bubble-layer.md)
