---
title: マップに描画ツール バーを追加する | Microsoft Azure Maps
description: Azure Maps Web SDK を使用してマップに描画ツールバーを追加する方法
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: b00628ec5a9f41b027bf90b93421f3aa1404e97a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92896397"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>描画ツールのツールバーをマップに追加する

この記事では、描画ツール モジュールを使用する方法、およびマップ上に描画ツールバーを表示する方法について説明します。 [DrawingToolbar](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar) コントロールでは、マップ上に描画ツールバーが追加されます。 描画ツールを 1 つのみ、およびすべて使用してマップを作成する方法と、描画マネージャーで描画図形のレンダリングをカスタマイズする方法について説明します。

## <a name="add-drawing-toolbar"></a>描画ツール バーの追加

次のコードでは、描画マネージャーのインスタンスが作成され、ツールバーがマップ上に表示されます。

```javascript
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

<iframe height="500" style="width: 100%;" scrolling="no" title="描画ツール バーの追加" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>描画ツールの追加</a>」 Pen を参照してください。
</iframe>


## <a name="limit-displayed-toolbar-options"></a>表示するツールバー オプションの制限

次のコードでは、描画マネージャーのインスタンスが作成され、多角形描画ツールだけを含むツールバーがマップ上に表示されます。 

```javascript
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

<iframe height="500" style="width: 100%;" scrolling="no" title="多角形描画ツールの追加" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>多角形描画ツールの追加</a>」 Pen を参照してください。
</iframe>


## <a name="change-drawing-rendering-style"></a>描画のレンダリング スタイルの変更

描画される図形のスタイルは、`drawingManager.getLayers()` 関数を使用し、個々のレイヤーのオプションを設定することによって、描画マネージャーの基になるレイヤーを取得することによりカスタマイズできます。 図形の編集時に座標に表示されるドラッグ ハンドルは HTML マーカーです。 ドラッグ ハンドルのスタイルは、描画マネージャーの `dragHandleStyle` オプションおよび `secondaryDragHandleStyle` オプションに HTML マーカー オプションを渡すことによってカスタマイズできます。  

次のコードでは、描画マネージャーからレンダリング レイヤーを取得し、それらのオプションを変更することで、描画のレンダリング スタイルを変更します。 この場合、ポイントは青いマーカー アイコンで描画されます。 線は、赤色の 4 ピクセル幅になります。 多角形は、緑色で塗りつぶされ、枠線がオレンジ色になります。 次に、ドラッグ ハンドルのスタイルが四角形のアイコンに変更されます。 

```javascript
//Get rendering layers of drawing manager.
var layers = drawingManager.getLayers();

//Change the icon rendered for points.
layers.pointLayer.setOptions({
    iconOptions: {
        image: 'marker-blue'
    }
});

//Change the color and width of lines.
layers.lineLayer.setOptions({
    strokeColor: 'red',
    strokeWidth: 4
});

//Change fill color of polygons.
layers.polygonLayer.setOptions({
    fillColor: 'green'
});

//Change the color of polygon outlines.
layers.polygonOutlineLayer.setOptions({
    strokeColor: 'orange'
});

//Update the style of the drag handles that appear when editting.
drawingManager.setOptions({
    //Primary drag handle that represents coordinates in the shape.
    dragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="15" height="15" viewBox="0 0 15 15" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="15" height="15" style="stroke:black;fill:white;stroke-width:4px;"/></svg>',
        draggable: true
    },

    //Secondary drag hanle that represents mid-point coordinates that users can grab to add new cooridnates in the middle of segments.
    secondaryDragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="10" height="10" viewBox="0 0 10 10" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="10" height="10" style="stroke:white;fill:black;stroke-width:4px;"/></svg>',
        draggable: true
    }
});  
```

上記の機能の完全な実行コード サンプルを以下に示します。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="描画のレンダリング スタイルの変更" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>描画のレンダリング スタイルの変更</a>」 Pen を参照してください。
</iframe>


## <a name="next-steps"></a>次のステップ

描画ツール モジュールのその他の機能の使用方法を確認します。

> [!div class="nextstepaction"]
> [図形データを取得する](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [描画イベントに応答する](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [対話式操作の種類とキーボード ショートカット](drawing-tools-interactions-keyboard-shortcuts.md)

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [描画ツール バー](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)

> [!div class="nextstepaction"]
> [描画マネージャー](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)