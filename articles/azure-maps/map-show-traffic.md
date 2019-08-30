---
title: Azure Maps でのトラフィックの表示 | Microsoft Docs
description: Azure Maps Web SDK でトラフィック データを表示する方法。
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b765f6f35cecfdd3183b4432954fcfb2548de046
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69874873"
---
# <a name="show-traffic-on-the-map"></a>マップ上にトラフィックを表示する

Azure Maps で使用できるトラフィック データには、次の 2 種類があります。

- インシデント データ - 建設、道路の閉鎖、事故などのためのポイント データと行ベースのデータで構成されます。
- フロー データ - 道路のトラフィックのフローに関するメトリックを提供します。 多くの場合、トラフィック フロー データは、速度制限やその他のいくつかのメトリックと相対的な、フロー速度が低下するトラフィック量に基づいて道路の色を設定するために使用されます。 Azure Maps のトラフィック フロー データには、次の 3 種類の測定メトリックがあります。
    - `relative` - 道路のフリーフロー速度に対して相対的です。
    - `absolute` - 道路のすべての車両の絶対速度です。
    - `relative-delay` - 予想される平均遅延時間よりも遅い地域が表示されます。

次のコードは、トラフィック データをマップに表示する方法を示しています。

```javascript
//Show traffic on the map using the traffic options.
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

上記の機能の完全な実行コード サンプルを以下に示します。

<br/>

<iframe height='500' scrolling='no' title='マップ上にトラフィックを表示する' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Show traffic on a map</a>」Pen を表示します。
</iframe>

## <a name="traffic-overlay-options"></a>トラフィック オーバーレイのオプション

次のツールを使用すると、さまざまなトラフィック オーバーレイ設定に切り替えて、表示がどのように変化するかを確認できます。 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="トラフィック オーバーレイのオプション" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen の<a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>トラフィック オーバーレイのオプション</a>を参照してください。
</iframe>

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [TrafficOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions)

ユーザー エクスペリエンスを強化します。

> [!div class="nextstepaction"]
> [操作をマウス イベントにマップする](map-events.md)

> [!div class="nextstepaction"]
> [アクセス可能なマップをビルド](map-accessibility.md)

> [!div class="nextstepaction"]
> [コード サンプル ページ](https://aka.ms/AzureMapsSamples)
