---
title: Azure Maps の描画マネージャーから図形データを取得する | Microsoft Docs
description: Azure Maps Web SDK を使用して図形データを取得する方法
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: e7706fba1efad1bd0ce7110e129dcf113689af9a
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309725"
---
# <a name="get-shape-data"></a>図形データを取得する

この記事では、[描画マネージャー](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#getsource--)の **drawingManager.getSource()** 関数を使用して、マップ上に描画された図形データを取得する方法について説明します。 さまざまなシナリオで、描画された図形の geojson データを抽出し、他の場所で使用することが必要になる場合があります。  


## <a name="get-data-from-drawn-shape"></a>描画された図形からデータを取得する

次の関数では、描画された図形のソース データが取得されて、画面に出力されます。 

```Javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

次に示すのは実行できる完全なコード サンプルであり、図形を描画して機能をテスできます。

<br/>

<iframe height="686" title="図形データを取得する" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>Get shape data</a>」Pen を参照してください。
</iframe>


## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [描画マネージャー](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [描画ツール バー](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
