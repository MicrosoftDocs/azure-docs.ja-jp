---
title: 描画ツール イベント | Microsoft Azure Maps
description: この記事では、Microsoft Azure Maps Web SDK を使用して、マップに描画ツール バーを追加する方法を学習します
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 6abe0ed88adbdf8263aa27d340fb2fff156d98e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90089345"
---
# <a name="drawing-tool-events"></a>描画ツール イベント

マップで描画ツールを使用するときに、ユーザーがマップに描画した場合に特定のイベントに応答すると便利です。 次の表に、`DrawingManager` クラスがサポートするすべてのイベントを一覧表示します。

| Event | 説明 |
|-------|-------------|
| `drawingchanged` | 図形に任意の座標が追加されたり、それが変更されると発生します。 | 
| `drawingchanging` | 図形のプレビュー座標が表示されると発生します。 たとえば、このイベントは座標をドラッグすると複数回発生します。 | 
| `drawingcomplete` | 図形の描画が終了したり、編集モードが終了すると発生します。 |
| `drawingmodechanged` | 描画モードが変更されると発生します。 新しい描画モードはイベント ハンドラーに渡されます。 |
| `drawingstarted` | ユーザーが図形の描画を開始すると、または図形を編集モードにすると発生します。  |

次のコードでは、描画ツール モジュールのイベントのしくみを示します。 マップ上に図形を描画し、イベントが発生するのを確認できます。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="描画ツールのイベント" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen の<a href='https://codepen.io/azuremaps/pen/dyPMRWo'>描画ツール イベント</a>に関するページを参照してください。
</iframe>

<br/>

## <a name="examples"></a>例

描画ツール イベントを使用する、いくつかの一般的なシナリオを見てみましょう。

### <a name="select-points-in-polygon-area"></a>多角形領域内のポイントを選択する

このコードは、ユーザーによる図形描画のイベントを監視する方法を示します。 この例では、コードは多角形、四角形、および円の図形を監視します。 次に、マップ上のどのデータ ポイントが描画領域内にあるかを判断します。 `drawingcomplete` イベントは、選択ロジックをトリガーするために使用します。 選択ロジックでは、コードはマップ上のすべてのデータ ポイントをループします。 ここでは、描画された図形の領域とポイントに交差があるかどうかを検査します。 この例では、オープンソースの [Turf.js](https://turfjs.org/) ライブラリを使用して、空間上の交差を計算しています。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="描画された多角形領域でデータを選択する" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen の<a href='https://codepen.io/azuremaps/pen/XWJdeja'>描画された多角形領域でのデータの選択</a>に関するページを参照してください。
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>多角形領域で描画および検索する

このコードは、ユーザーが図形の描画を終了した後に、図形の領域内の目的地を検索します。 フレームの右上隅にある [Edit on CodePen]\(CodePen で編集\) をクリックして、コードを変更して実行できます。 `drawingcomplete` イベントは、検索ロジックをトリガーするために使用します。 ユーザーが四角形または多角形を描画すると、ジオメトリ内で検索が実行されます。 円が描画されている場合は、中心位置が使用され目的地が検索されます。 `drawingmodechanged` イベントは、ユーザーがいつ描画モードに切り替えるかを決定するために使用し、このイベントは描画キャンバスをクリアします。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="多角形領域で描画および検索する" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen の<a href='https://codepen.io/azuremaps/pen/eYmZGNv'>多角形領域での描画と検索</a>に関するページを参照してください。
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>測定ツールを作成する

次のコードは、描画イベントを使用して測定ツールを作成する方法を示しています。 `drawingchanging` は、描画中の図形を監視するために使用します。 ユーザーがマウスを動かすと、図形の寸法が計算されます。 `drawingcomplete` イベントは、図形の描画後に、図形の最終計算を実行するために使用します。 `drawingmodechanged` イベントは、ユーザーがいつ描画モードに切り替えるかを決定するために使用します。 また、`drawingmodechanged` イベントは描画キャンバスをクリアし、古い測定情報をクリアします。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="測定ツール" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen の<a href='https://codepen.io/azuremaps/pen/RwNaZXe'>測定ツール</a>に関するページを参照してください。
</iframe>

<br/>

## <a name="next-steps"></a>次のステップ

描画ツール モジュールのその他の機能の使用方法を確認します。

> [!div class="nextstepaction"]
> [図形データを取得する](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [対話式操作の種類とキーボード ショートカット](drawing-tools-interactions-keyboard-shortcuts.md)

サービス モジュールの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [サービス モジュール](how-to-use-services-module.md)

他のコード サンプルについては、次を確認してください。

> [!div class="nextstepaction"]
> [コード サンプル ページ](https://aka.ms/AzureMapsSamples)
