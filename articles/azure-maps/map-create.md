---
title: Azure Maps を使用してマップを作成する | Microsoft Docs
description: JavaScript マップを作成する方法
author: jingjing-z
ms.author: jinzh
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 222fc5e9083c03ff0d4e31927363c5f517cf32a9
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2019
ms.locfileid: "55699366"
---
# <a name="create-a-map"></a>マップを作成する

この記事では、マップを作成し、マップをアニメーション化する方法を示します。  

## <a name="understand-the-code"></a>コードの理解

マップを構築する方法は 2 つあります。 中心点とズーム レベルを指定してマップのカメラを設定するか、または南西と北東の境界点を指定して、マップのカメラ境界を設定できます。

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>カメラの設定

<iframe height='500' scrolling='no' title='CameraOptions を介してマップを作成する' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) によって、Pen の <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Create a map via `CameraOptions` </a> を表示します。
</iframe>

上記のコードでは、`new atlas.Map()` によって [Map オブジェクト](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)が作成され、中心とズームが設定されます。 中央やズーム レベルなどのマップのプロパティは、[CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) の一部です。

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>カメラ境界の設定

<iframe height='500' scrolling='no' title='CameraBoundsOptions を介してマップを作成する' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) によって、Pen の <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Create a map via `CameraBoundsOptions` </a> を表示します。
</iframe>

上記のコードでは、`new atlas.Map()` を介して[マップ オブジェクト](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)が構築されています。 `CameraBoundsOptions` などの Map プロパティは、Map クラスの [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 関数を介して定義できます。 境界とパディングのプロパティは、`setCamera` を使用して設定します。

### <a name="animate-map-view"></a>マップ ビューをアニメーション化する

<iframe height='500' scrolling='no' title='マップ ビューをアニメーション化する' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/WayvbO/'>Animate Map View</a>」Pen を表示します。
</iframe>

上記のコードの最初のコード ブロックでは、`new atlas.Map()` によって [Map オブジェクト](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)が作成されます。 中央やズーム レベルなどのマップのプロパティは、[CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) の一部です。 `CameraOptions` は、マップコンストラクターで、またはマップ クラスの [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 関数を介して定義できます。 [マップ スタイル](https://docs.microsoft.com/azure/azure-maps/supported-map-styles)は `road` に設定されます。

2 番目のコード ブロックでは、アニメーション マップ関数が作成されます。この関数は、[setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 関数によって、[AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions) を定義して、マップ ビュー内の変化をアニメーション化します。 関数は、[Animate Map] ボタンをクリックしてトリガーされ、クリックするたびにランダムなズーム レベルが生成されます。

## <a name="try-out-the-code"></a>コードを実行する

前述のサンプル コードをご覧ください。 左側の **[JS] タブ**で JavaScript コードを編集し、右側の **[結果] タブ**でマップ ビューの変更を確認することができます。 **[Edit on CodePen]\(CodePen で編集\)** ボタンをクリックして CodePen のコードを編集することもできます。

<a id="relatedReference"></a>

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

ご利用のアプリに機能を追加するには、次のコード例を参照してください。

> [!div class="nextstepaction"]
> [マップ スタイルを選択する](choose-map-style.md)

> [!div class="nextstepaction"]
> [マップ コントロールを追加する](map-add-controls.md)
