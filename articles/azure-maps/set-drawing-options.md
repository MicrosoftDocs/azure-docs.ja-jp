---
title: Azure Maps で描画オプションを設定する | Microsoft Docs
description: Azure Maps Web SDK を使用して描画オプション データを設定する方法
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: d85525274db7818737b62ad4e9ea2026b8aef3b2
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309727"
---
# <a name="set-drawing-options"></a>描画オプションを設定する

この記事では、[描画マネージャー](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-)のさまざまなオプションによってユーザー エクスペリエンスがどのように変わるかを示します。 インスタンス化の間に描画マネージャーのオプションを指定することも、**drawingManager.setOptions()** 関数を使用してオプションを設定することもできます。


## <a name="set-drawing-mode"></a>描画モードを設定する

次のコードでは、描画マネージャーのインスタンスを作成して、描画モード (**mode**) オプションを設定します。 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

次のコードは、描画マネージャーの描画モードを設定する方法の、実行できる完全な例です。 多角形の描画を開始するには、マップをクリックします。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="多角形を描画する" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>Draw a polygon</a>」Pen を参照してください。
</iframe>


## <a name="set-interaction-type"></a>操作の種類を設定する

次のコードでは、描画マネージャーが従う必要のある描画操作の種類を設定します。 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

次に示すのは、マウスの左ボタンを押したままドラッグすることでマップ上に自由に描画できる機能を実装するコード サンプルです。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="フリーハンドの描画" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>Free-hand drawing</a>」Pen を参照してください。
</iframe>


## <a name="customizing-drawing-options"></a>描画オプションをカスタマイズする

前の例では、描画マネージャーをインスタンス化するときに描画オプションをカスタマイズする方法を示しました。 **drawingManager.setOptions()** 関数を使用して、描画マネージャーのオプションを設定することもできます。 次に示すのは、setOptions 関数を使用した描画マネージャーのすべてのオプションのカスタマイズをテストするツールです。

<br/>

<iframe height="685" title="描画マネージャーをカスタマイズする" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>Get shape data</a>」Pen を参照してください。
</iframe>


## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [描画マネージャー](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [描画ツール バー](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
