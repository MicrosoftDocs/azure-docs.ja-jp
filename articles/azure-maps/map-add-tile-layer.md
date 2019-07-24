---
title: Azure Maps にタイル レイヤーを追加する | Microsoft Docs
description: JavaScript マップにタイル レイヤーを追加する方法
author: rbrundritt
ms.author: richbrun
ms.date: 12/3/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 3a773c24993d229f20df698113ff7535fea634ca
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58170039"
---
# <a name="add-a-tile-layer-to-a-map"></a>マップにタイル レイヤーを追加する

この記事では、タイル レイヤーをマップに重ねて配置する方法について説明します。 タイル レイヤーを使用すると、Azure Maps ベース マップ タイルの上に画像を重ねることができます。 Azure Maps のタイル システムの詳細については、[ズーム レベルとタイル グリッド](zoom-levels-and-tile-grid.md)のドキュメントを参照してください。

タイル レイヤーでは、サーバーからタイルが読み込まれます。 これらの画像は、タイル レイヤーが認識できる名前付け規則を使用するか、画像をその場で生成する動的サービスを使用して、サーバー上の他の画像と同様に事前にレンダリングし、保存することができます。 Azure Maps の [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) クラスでは、3 種類のタイル サービスの名前付け規則がサポートされています。 

* X、Y、ズーム表記 - ズーム レベルに基づいた、タイル グリッド内のタイルの列 (x)、行 (x) の位置です。
* quadkey 表記 - x、y、ズーム情報を組み合わせて、タイルの一意の識別子である 1 つの文字列値にします。
* 境界ボックス - 境界ボックス座標を使用して、[Web マッピング サービス (WMS)](https://www.opengeospatial.org/standards/wms) で一般的に使用されている `{west},{south},{east},{north}` の形式で画像を指定することができます。

> [!TIP]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) は、マップ上で大規模なデータ セットを視覚化する場合に適しています。 画像からタイル レイヤーを生成できるだけでなく、ベクター データもタイル レイヤーとしてレンダリングできます。 ベクター データをタイル レイヤーとしてレンダリングすることで、マップ コントロールでは、タイルが表すベクター データよりもはるかにファイル サイズの小さいタイルを読み込むだけで済みます。 この手法は、何百万行ものデータをマップにレンダリングする必要がある場合によく使われています。

タイル レイヤーに渡されるタイル URL は、TileJSON リソースへの http/https URL、または次のパラメーターを使用するタイル URL テンプレートにする必要があります。 

* `{x}` - タイルの X 位置。 `{y}` と `{z}` も必要です。
* `{y}` - タイルの Y 位置。 `{x}` と `{z}` も必要です。
* `{z}` - タイルのズーム レベル。 `{x}` と `{y}` も必要です。
* `{quadkey}` - Bing Maps タイル システムの名前付け規則に基づくタイルの quadkey 識別子。
* `{bbox-epsg-3857}` - EPSG 3857 空間参照系の `{west},{south},{east},{north}` 形式の境界ボックス文字列。
* `{subdomain}` - プレースホルダー。サブドメインの値が指定されると、ここに追加されます。

## <a name="add-a-tile-layer"></a>タイル レイヤーを追加する

 このサンプルは、x、y、ズーム タイル システムを使用するタイルのセットを指すタイル レイヤーを作成する方法を示しています。 このタイル レイヤーのソースは、[アイオワ州立大学の Iowa Environmental Mesonet](https://mesonet.agron.iastate.edu/ogc/) の気象レーダー オーバーレイです。

<br/>

<iframe height='500' scrolling='no' title='X、Y、Z を使用するタイル レイヤー' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/BGEQjG/'>Tile Layer using X, Y, and Z</a>」Pen を表示します。
</iframe>

上記のコードの最初のコード ブロックでは、マップ オブジェクトが作成されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

2 番目のコード ブロックでは、タイル サービスを指す形式の URL、タイル サイズ、および半透明にする不透明度を渡して [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) を作成しています。 また、タイル レイヤーをマップに追加すると、`labels` レイヤーの下に追加されるので、ラベルが明瞭に見えます。

## <a name="customize-a-tile-layer"></a>タイル レイヤーをカスタマイズする

多数のスタイル オプションがあるのはタイル レイヤーのみです。 それらを試すツールを次に紹介します。

<br/>

<iframe height='700' scrolling='no' title='タイル レイヤーのオプション' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/xQeRWX/'>Tile Layer Options</a>」Pen を表示します。
</iframe>

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

マップに追加できる他のコード サンプルについては、次の記事をご覧ください。

> [!div class="nextstepaction"]
> [イメージ レイヤーを追加する](./map-add-image-layer.md)
