---
title: Azure Maps を使用してポップアップを追加する | Microsoft Docs
description: JavaScript マップにポップアップを追加する方法
author: jingjing-z
ms.author: jinzh
ms.date: 11/09/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: a6c8a8aa954379036ce566a205b8cb4e97952727
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2018
ms.locfileid: "52887845"
---
# <a name="add-a-popup-to-the-map"></a>マップにポップアップを追加する

この記事では、マップ上のポイントにポップアップを追加する方法について説明します。

## <a name="understand-the-code"></a>コードの理解

<a id="addAPopup"></a>

<iframe height='500' scrolling='no' title='Azure Maps を使用してポップアップを追加する' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> で Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) により <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Azure Maps を使用してポップアップを追加する</a> Pen の例です。
</iframe>

上記のコードの最初のコード ブロックでは、マップ オブジェクトが作成されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。 ポップアップ内に表示する HTML コンテンツも作成します。

2 番目のコード ブロックでは、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) クラスを使用して、データ ソース オブジェクトが作成されます。 ポイントは、[Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) クラスの [Feature](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) です。 名前プロパティと説明プロパティを含むポイント オブジェクトが作成されて、データ ソースに追加されます。

[シンボル レイヤー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)は、テキストまたはアイコンを使用して、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) にラップされたポイントベースのデータをシンボルとしてマップにレンダリングします。  シンボル レイヤーは、3 番目のコード ブロックで作成されます。 データ ソースがシンボル レイヤーに追加され、それがマップに追加されます。

コードの 4 つ目のブロックでは、`new atlas.Popup()` 経由で[ポップアップ オブジェクト](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)が作成されます。 position や pixelOffset などのポップアップのプロパティは、[PopupOptions](/javascript/api/azure-maps-control/atlas.popupoptions) の一部です。 PopupOptions は、ポップアップ コンストラクターで、またはポップアップ クラスの [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) 関数を介して定義できます。 その後、シンボル レイヤーの `mouseover` イベント リスナーが作成されます。

最後のコード ブロックでは、`mouseover` イベント リスナーによってトリガーされる関数が作成されます。 ポップアップの内容とプロパティを設定して、ポップアップ オブジェクトをマップに追加します。

## <a name="reusing-a-popup-with-multiple-points"></a>複数のポイントでポップアップを再利用する

多数のポイントがあり、一度に 1 つのポップアップのみを表示する場合は、ポイント フィーチャーごとにポップアップを作成するのではなく、ポップアップを 1 つ作成して再利用することをお勧めします。 こうすることで、アプリケーションによって作成される DOM 要素数が大幅に削減され、パフォーマンスが向上します。 このサンプルでは 3 つのポイント フィーチャーを作成します。 そのいずれかをクリックすると、そのポイント フィーチャーのコンテンツを含むポップアップが表示されます。

<br/>

<iframe height='500' scrolling='no' title='複数のピンでポップアップを再利用する' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/rQbjvK/'>Reusing Popup with Multiple Pins</a>」Pen を表示します。
</iframe>

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

完全なコードのサンプルについては、次の優れた記事をご覧ください。

> [!div class="nextstepaction"]
> [シンボル レイヤーを追加する](./map-add-pin.md)

> [!div class="nextstepaction"]
> [HTML マーカーを追加する](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [シェイプを追加する](./map-add-shape.md)