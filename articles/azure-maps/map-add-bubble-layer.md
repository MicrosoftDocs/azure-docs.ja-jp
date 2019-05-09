---
title: Azure Maps にバブル レイヤーを追加する | Microsoft Docs
description: JavaScript マップにバブル レイヤーを追加する方法
author: rbrundritt
ms.author: richbrun
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: f2c4c6b8655d5efb993a2dedf536000ac94328c2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59281490"
---
# <a name="add-a-bubble-layer-to-a-map"></a>マップにバブル レイヤーを追加する

この記事では、データ ソースからのポイント データをマップ上のバブル レイヤーにレンダリングする方法について説明します。 バブル レイヤーは、決まったピクセル半径を持つ円としてポイントをマップ上にレンダリングするものです。 

> [!TIP]
> バブル レイヤーの既定では、データ ソース内のすべてのジオメトリの座標がレンダリングされます。 ポイント ジオメトリ フィーチャーのみがレンダリングされるようにレイヤーを制限するには、レイヤーの `filter` プロパティを `['==', ['geometry-type'], 'Point']` に設定します。または、MultiPoint フィーチャーも含める場合は、`['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` に設定します。

## <a name="add-a-bubble-layer"></a>バブル レイヤーを追加する

<iframe height='500' scrolling='no' title='BubbleLayer DataSource' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer DataSource</a>」Pen を表示します。
</iframe>

上記のコードの最初のコード ブロックでは、マップ オブジェクトが作成されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

2 つ目のコード ブロックでは、[Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) オブジェクトの配列を定義して [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) オブジェクトに追加しています。

[バブル レイヤー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)によって、[データ ソース](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)にラップされたポイントベースのデータが円としてマップにレンダリングされます。 最後のコード ブロックでは、バブル レイヤーが作成され、マップに追加されます。 [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions)で、バブル レイヤーのプロパティをご覧ください。

マップが完全に読み込まれた後で円を表示するために、[イベント リスナー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)関数内で、Point オブジェクトの配列、データ ソース、バブル レイヤーを作成してマップに追加しています。

## <a name="show-labels-with-a-bubble-layer"></a>バブル レイヤーでラベルを表示する

<iframe height='500' scrolling='no' title='MultiLayer DataSource' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/rqbQXy/'>MultiLayer DataSource</a>」Pen を表示します。
</iframe>

上記のコードでは、マップ上のデータを視覚化し、ラベル付けする方法を示します。 上記のコードの最初のブロックでは、マップ オブジェクトが作成されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

2 番目のコード ブロックでは、[Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) オブジェクトが作成されます。 次に、[データ ソース](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) クラスを使用して、データ ソース オブジェクトが作成され、そのデータ ソースにポイントが追加されます。

[バブル レイヤー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)によって、[データ ソース](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)にラップされたポイントベースのデータが円としてマップにレンダリングされます。 3 番目のコード ブロックでは、バブル レイヤーが作成され、マップに追加されます。 [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions)で、バブル レイヤーのプロパティをご覧ください。

[シンボル レイヤー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)は、テキストまたはアイコンを使用して、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) にラップされたポイントベースのデータをシンボルとしてマップにレンダリングします。 最後のコード ブロックでは、シンボル レイヤーが作成され、マップに追加されて、バブルのテキスト ラベルがレンダリングされます。 シンボル レイヤーのプロパティについては、[SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions) に関する記事をご覧ください。

[イベント リスナー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)関数内でデータ ソースとレイヤーが作成されてマップに追加されるため、データは、マップが完全に読み込まれた後で表示されます。

## <a name="customize-a-bubble-layer"></a>バブル レイヤーをカスタマイズする

バブル レイヤーにはごくわずかのスタイル オプションしかありません。 次のツールでそれらをお試しください。

<br/>

<iframe height='700' scrolling='no' title='バブル レイヤーのオプション' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/eQxbGm/'>Bubble Layer Options</a>」Pen を表示します。
</iframe>

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

マップに追加できる他のコード サンプルについては、次の記事をご覧ください。

> [!div class="nextstepaction"]
> [シンボル レイヤーを追加する](map-add-pin.md)

> [!div class="nextstepaction"]
> [データドリブンのスタイルの式を使用する](data-driven-style-expressions-web-sdk.md)