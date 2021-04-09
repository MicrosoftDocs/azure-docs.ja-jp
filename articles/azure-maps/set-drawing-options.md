---
title: 描画ツール モジュール | Microsoft Azure Maps
description: この記事では、Microsoft Azure Maps Web SDK を使用して描画オプション データを設定する方法について学習します
author: anastasia-ms
ms.author: v-stharr
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 95a04d763fa5982181cc1c797bce969d9857ae4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92890634"
---
# <a name="use-the-drawing-tools-module"></a>描画ツール モジュールを使用する

Azure Maps の Web SDK は、*描画ツール モジュール* を提供しています。 このモジュールを使用すると、マウスやタッチ スクリーンなどの入力デバイスを使用して、マップ上で簡単に図形の描画や編集ができます。 このモジュールのコア クラスは、[描画マネージャー](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#setoptions-drawingmanageroptions-)です。 描画マネージャーにより、マップ上で図形を描画したり編集したりするために必要なすべての機能が提供されます。 これは直接使用することができ、カスタム ツール バーの UI に統合されています。 組み込みの[描画ツール バー](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar) クラスを使用することもできます。 

## <a name="loading-the-drawing-tools-module-in-a-webpage"></a>Web ページへの描画ツール モジュールの読み込み

1. 新しい HTML ファイルを作成し、[通常のマップを実装します](./how-to-use-map-control.md)。
2. Azure Maps の描画ツール モジュールを読み込みます。 次の 2 つの方法のいずれかで読み込みます。
    - グローバルにホストされている Azure Maps サービス モジュールの Azure Content Delivery Network のバージョンを使用します。 ファイルの `<head>` 要素に JavaScript および CSS スタイルシートへの参照を追加します。

        ```html
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
        ```

    - または、[azure-maps-drawing-tools](https://www.npmjs.com/package/azure-maps-drawing-tools) npm パッケージを使用して、Azure Maps Web SDK ソース コード用の描画ツール モジュールをローカルに読み込み、アプリを使用してそれをホストできます。 このパッケージには TypeScript 定義も含まれています。 次のコマンドを実行します。
    
        > **npm install azure-maps-drawing-tools**
    
        次に、ファイルの `<head>` 要素に JavaScript および CSS スタイルシートへの参照を追加します。

         ```html
        <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.css" type="text/css" />
        <script src="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.js"></script>
         ```

## <a name="use-the-drawing-manager-directly"></a>描画マネージャーを直接使用する

描画ツール モジュールがアプリケーションに読み込まれたら、[描画マネージャー](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#setoptions-drawingmanageroptions-)を使用して、描画機能と編集機能を有効にすることができます。 インスタンス化の間に描画マネージャーのオプションを指定することも、`drawingManager.setOptions()` 関数を使用することもできます。

### <a name="set-the-drawing-mode"></a>描画モードを設定する

次のコードでは、描画マネージャーのインスタンスを作成して、描画モード (**mode**) オプションを設定します。 

```javascript
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


### <a name="set-the-interaction-type"></a>操作の種類を設定する

描画マネージャーでは、図形を描画するためにマップを対話式に操作する 3 つの異なる方法がサポートされています。

* `click` - マウスまたはタッチでクリックしたときに座標が追加されます。
* `freehand ` - マウスまたはタッチでマップ上にドラッグしたときに座標が追加されます。 
* `hybrid` - マウスまたはタッチでクリックまたはドラッグしたときに座標が追加されます。

次のコードでは、多角形描画モードが有効になり、描画マネージャーが `freehand` に従うという描画操作の種類が設定されます。 

```javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

 このコード サンプルでは、マップに多角形を描画する機能を実装します。 マウスの左ボタンを押したまま、自由にドラッグするだけです。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="フリーハンドの描画" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>Free-hand drawing</a>」Pen を参照してください。
</iframe>


### <a name="customizing-drawing-options"></a>描画オプションをカスタマイズする

前の例では、描画マネージャーをインスタンス化するときに描画オプションをカスタマイズする方法を示しました。 `drawingManager.setOptions()` 関数を使用して、描画マネージャーのオプションを設定することもできます。 次に示すのは、setOptions 関数を使用した描画マネージャーのすべてのオプションのカスタマイズをテストするツールです。

<br/>

<iframe height="685" title="描画マネージャーをカスタマイズする" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>Get shape data</a>」Pen を参照してください。
</iframe>


## <a name="next-steps"></a>次のステップ

描画ツール モジュールのその他の機能の使用方法を確認します。

> [!div class="nextstepaction"]
> [描画ツールバーを追加する](map-add-drawing-toolbar.md)

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
> [描画マネージャー](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)

> [!div class="nextstepaction"]
> [描画ツール バー](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)