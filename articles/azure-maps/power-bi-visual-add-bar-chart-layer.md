---
title: Azure Maps Power BI ビジュアルに横棒グラフ レイヤーを追加する | Microsoft Azure Maps
description: この記事では、Power BI 用の Microsoft Azure Maps ビジュアルで横棒グラフ レイヤーを使用する方法について説明します。
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 136676b46df6e32f98dca99fccba19d4aa369dbe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92896278"
---
# <a name="add-a-bar-chart-layer"></a>横棒グラフ レイヤーを追加する

**横棒グラフ レイヤー** は、マップ上で場所データを 3D 横棒や円柱として視覚化できるようにすることで、データを次のディメンションに移動させるのに役立ちます。 バブル レイヤーと同様に、横棒グラフ レイヤーでは、色と相対的高さを使用して 2 つのメトリックを同時に視覚化することが容易にできます。 棒の高さを設定するには、 **[フィールド]** ペインの **[サイズ]** バケットにメジャーを追加する必要があります。 メジャーが指定されていない場合は、 **[棒の形状]** オプションに応じて、高さのない棒は、平らな正方形または円として表示されます。

> [!div class="mx-imgBorder"]
> ![横棒グラフ レイヤーを使用してポイント データを表示しているマップ](media/power-bi-visual/bar-chart-layer-styled.png)

ユーザーは、マップを傾けたり回転させたりして、さまざまな観点からデータを表示することができます。 マップを傾けるには、次のいずれかの方法を使用します。

-   **[Format]\(形式\)** ペインの **[マップの設定]** で **[ナビゲーション コントロール]** オプションをオンにします。 これにより、マップを傾けるボタンが追加されます。
-   マウスの右ボタンを押しながら、マウスを上または下にドラッグします。
-   タッチスクリーンを使用して 2 本の指でマップをタッチして、上または下にドラッグします。
-   マップにフォーカスがある状態で、**Shift** キーを押しながら、**上方向** キーまたは **下方向** キーを押します。

マップを回転するには、次のいずれかの方法を使用します。

-   **[Format]\(形式\)** ペインの **[マップの設定]** で **[ナビゲーション コントロール]** オプションをオンにします。 これにより、マップを回転するボタンが追加されます。
-   マウスの右ボタンを押しながら、マウスを左または右にドラッグします。
-   タッチスクリーンを使用して 2 本の指でマップをタッチし、回転します。
-   マップにフォーカスがある状態で、**Shift** キーを押しながら、**左方向** キーまたは **右方向** キーを押します。

**[横棒グラフ レイヤー]** セクションで使用できる **[Format]\(形式\)** ペインのすべての設定を次に示します。

| 設定              | 説明      |
|----------------------|------------------|
| 棒の形状            | 3D 横棒の形状。<br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• ボックス – 横棒が四角形のボックスとしてレンダリングされます。<br/>&nbsp;&nbsp;&nbsp;&nbsp;• 円柱 – 横棒が円柱としてレンダリングされます。 |
| [高さ]               | 各横棒の高さ。 フィールドが **[フィールド]** ペインの **[サイズ]** バケットに渡されると、横棒がこの高さの値を基準にして拡大縮小されます。 |
| Scale height on zoom\(ズーム時に高さを調整する\) | 横棒の高さをズーム レベルに応じて相対的に調整するかどうかを指定します。 |
| 幅                | 各横棒の幅。  |
| Scale width on zoom\(ズーム時に幅を調整する\)  | 横棒の幅をズーム レベルに応じて相対的に調整するかどうかを指定します。  |
| 塗りつぶしの色           | 各横棒の色。 このオプションは、フィールドが **[フィールド]** ペインの **[凡例]** バケットに渡されると非表示になり、別の **[データの色]** セクションが **[Format]\(形式\)** ペインに表示されます。 |
| 透明性         | 各横棒の透明度。 |
| Min zoom\(最小ズーム\)             | タイルで使用可能な最小ズーム レベル。 |
| Max zoom\(最大ズーム\)             | タイルで使用可能な最大ズーム レベル。 |
| Layer position\(レイヤーの位置\)       | 他のマップ レイヤーに対するレイヤーの位置を指定します。 |

> [!NOTE]
> 横棒の幅が小さく、 **[Scale width on zoom]\(ズーム時に幅を調整する\)** オプションが無効になっている場合、レンダリングされた幅のサイズが 1 ピクセル以下になってしまうため、棒は大きく拡大したときに消えてしまう可能性があります。 ただし、 **[Scale width on zoom]\(ズーム時に幅を調整する\)** オプションが有効になっている場合、ズーム レベルが変更されると、追加の計算が実行されるため、大規模なデータ セットのパフォーマンスに影響する可能性があります。

## <a name="next-steps"></a>次のステップ

マップにコンテキストをさらに追加する:

> [!div class="nextstepaction"]
> [参照レイヤーを追加する](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [タイル レイヤーを追加する](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [リアルタイム トラフィックを表示する](power-bi-visual-show-real-time-traffic.md)

ビジュアルをカスタマイズする:

> [!div class="nextstepaction"]
> [Power BI における色の書式設定に関するヒントとコツ](/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [視覚エフェクトのタイトル、背景、および凡例をカスタマイズする](/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)