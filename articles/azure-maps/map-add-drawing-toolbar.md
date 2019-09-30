---
title: Azure Maps に描画ツール バーを追加する | Microsoft Docs
description: Azure Maps Web SDK を使用してマップに描画ツールバーを追加する方法
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 6bc754c9a4f333da85e57c5ad9780da8df93e895
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309739"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>描画ツールのツールバーをマップに追加する

この記事では、描画ツール モジュールを使用する方法、およびマップ上に描画ツールバーを表示する方法について説明します。 [DrawingToolbar](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) コントロールでは、マップ上に描画ツールバーが追加されます。 描画ツールを 1 つのみ、およびすべて使用してマップを作成する方法と、描画マネージャーで描画図形のレンダリングをカスタマイズする方法について説明します。

## <a name="add-drawing-toolbar"></a>描画ツール バーの追加

次のコードでは、描画マネージャーのインスタンスが作成され、ツールバーがマップ上に表示されます。

```Javascript
//Create an instance of the drawing manager and display the drawing toolbar.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'dark'
        })
    });
```

上記の機能の完全な実行コード サンプルを以下に示します。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="描画ツール バーの追加" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>描画ツールの追加</a>」 Pen を参照してください。
</iframe>


## <a name="limit-displayed-toolbar-options"></a>表示するツールバー オプションの制限

次のコードでは、描画マネージャーのインスタンスが作成され、多角形描画ツールだけを含むツールバーがマップ上に表示されます。 

```Javascript
//Create an instance of the drawing manager and display the drawing toolbar with polygon drawing tool.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'light',
            buttons: ["draw-polygon"]
        })
    });
```

上記の機能の完全な実行コード サンプルを以下に示します。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="多角形描画ツールの追加" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>多角形描画ツールの追加</a>」 Pen を参照してください。
</iframe>


## <a name="change-drawing-rendering-style"></a>描画のレンダリング スタイルの変更

次のコードでは、描画マネージャーからレンダリング レイヤーを取得し、それらのオプションを変更することで、描画のレンダリング スタイルを変更します。 この場合、点は青色のマーカー アイコンでレンダリングされ、線は赤色の 4 ピクセル幅になります。多角形の塗りつぶしの色は緑色に、枠線の色はオレンジ色になります。

```Javascript
var layers = drawingManager.getLayers();
    layers.pointLayer.setOptions({
        iconOptions: {
            image: 'marker-blue'
        }
    });
    layers.lineLayer.setOptions({
        strokeColor: 'red',
        strokeWidth: 4
    });
    layers.polygonLayer.setOptions({
        fillColor: 'green'
    });
    layers.polygonOutlineLayer.setOptions({
        strokeColor: 'orange'
    });
```

上記の機能の完全な実行コード サンプルを以下に示します。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="描画のレンダリング スタイルの変更" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>描画のレンダリング スタイルの変更</a>」 Pen を参照してください。
</iframe>


## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [描画ツール バー](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

> [!div class="nextstepaction"]
> [描画マネージャー](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)