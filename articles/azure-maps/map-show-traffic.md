---
title: マップ上にトラフィックを表示する | Microsoft Azure Maps
description: トラフィック データをマップに追加する方法について確認します。 フロー データについて学習し、Azure Maps Web SDK を使用して、インシデント データとフロー データをマップに追加する方法について確認します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: b6fcf39e05c4649503f0b7a80aadaaa16df24674
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98599564"
---
# <a name="show-traffic-on-the-map"></a>マップ上にトラフィックを表示する

Azure Maps で使用できるトラフィック データには、次の 2 種類があります。

- インシデント データ - 建設、道路の閉鎖、事故などのためのポイント データと行ベースのデータで構成されます。
- フロー データ - 道路のトラフィックのフローに関するメトリックを提供します。 多くの場合、トラフィック フロー データは、道路の色を設定するために使用されます。 その色は、速度制限やその他のメトリックと相対的な、フロー速度が低下するトラフィック量に基づいています。 マップの交通情報 `flow` オプションに渡すことができる値は 4 つあります。

    |Flow 値 | 説明|
    | :-- | :-- |
    | `none` | マップに交通情報データを表示しません |
    | `relative` | 道路の自由流速度を基準にして交通情報データを表示します |
    | `relative-delay` | 予想されている平均的な遅れよりも遅い地域を表示します |
    | `absolute` | 道路上の全車両の対地速度を表示します |

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

<iframe height='500' scrolling='no' title='マップ上にトラフィックを表示する' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Show traffic on a map</a>」Pen を表示します。
</iframe>

## <a name="traffic-overlay-options"></a>トラフィック オーバーレイのオプション

次のツールを使用すると、さまざまなトラフィック オーバーレイ設定に切り替えて、表示がどのように変化するかを確認できます。 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="トラフィック オーバーレイのオプション" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen の<a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>トラフィック オーバーレイのオプション</a>を参照してください。
</iframe>


## <a name="add-traffic-controls"></a>トラフィック コントロールを追加する

マップに追加できるトラフィック コントロールは 2 種類あります。 最初のコントロールである `TrafficControl` は、トラフィックのオンとオフの切り替えに使用できるトグル ボタンを追加します。 このコントロールのオプションを使用すると、トラフィックを表示するときに使用するトラフィック設定を指定できます。 このコントロールは既定では、相対トラフィック フローとインシデント データを表示しますが、必要に応じて絶対トラフィック フローを表示し、インシデントを表示しないように変更できます。 2 番目のコントロールである `TrafficLegendControl` は、トラフィック フローの凡例をマップに追加し、ユーザーはこれに基づいて、カラー コードによる道路の強調表示の意味を理解できます。 このコントロールは、マップにトラフィック フロー データが表示されているときにのみマップ上に表示され、それ以外のときは表示されません。

次のコードは、トラフィック コントロールをマップに追加する方法を示しています。

```JavaScript
//Att the traffic control toogle button to the top right corner of the map.
map.controls.add(new atlas.control.TrafficControl(), { position: 'top-right' });

//Att the traffic legend control to the bottom left corner of the map.
map.controls.add(new atlas.control.TrafficLegendControl(), { position: 'bottom-left' });
```

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="トラフィック コントロール" src="https://codepen.io/azuremaps/embed/ZEWaeLJ?height500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> での Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen <a href='https://codepen.io/azuremaps/pen/ZEWaeLJ'>トラフィック コントロール</a>を参照してください。
</iframe>


## <a name="next-steps"></a>次のステップ

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [TrafficOptions](/javascript/api/azure-maps-control/atlas.trafficoptions)

ユーザー エクスペリエンスを強化します。

> [!div class="nextstepaction"]
> [操作をマウス イベントにマップする](map-events.md)

> [!div class="nextstepaction"]
> [アクセス可能なマップをビルド](map-accessibility.md)

> [!div class="nextstepaction"]
> [コード サンプル ページ](https://aka.ms/AzureMapsSamples)