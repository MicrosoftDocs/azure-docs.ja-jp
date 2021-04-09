---
title: Azure Maps Power BI ビジュアルのレイヤーの概要 | Microsoft Azure Maps
description: この記事では、Power BI 用の Microsoft Azure Maps ビジュアルで利用できるさまざまなレイヤーについて学習します。
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: a5c7296a0e7b7f6ea33c1f4a669675efd90f9e9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86261448"
---
# <a name="understanding-layers-in-the-azure-maps-power-bi-visual"></a>Azure Maps Power BI ビジュアルのレイヤーの概要

Azure Maps ビジュアルには、2 種類のレイヤーが用意されています。 最初の種類は、ビジュアルの **フィールド** ウィンドウに渡されるデータのレンダリングに焦点を当てており、次のレイヤーで構成されています。これらのデータ レンダリング レイヤーを呼び出しましょう。

:::row:::
    :::column span="":::
        **バブル レイヤー**

        ポイントが拡大縮小される円としてマップ上にレンダリングされます。

        ![マップ上のバブル レイヤー](media/power-bi-visual/bubble-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **横棒グラフ レイヤー**

        ポイントを 3D 横棒としてマップ上にレンダリングします。
        
        ![マップ上の横棒グラフ レイヤー](media/power-bi-visual/bar-chart-layer-thumb.png)
    :::column-end:::
:::row-end:::

2 番目の種類のレイヤーでは、追加の外部データのソースを接続してより多くのコンテキストが提供され、次のレイヤーで構成されています。

:::row:::
    :::column span="":::
        **参照レイヤー**

        アップロードされた GeoJSON ファイルをマップの上にオーバーレイします。

        ![マップ上の参照レイヤー](media/power-bi-visual/reference-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **タイル レイヤー**

        カスタム タイル レイヤーをマップの上にオーバーレイします。
        
        ![マップ上のタイル レイヤー](media/power-bi-visual/tile-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **トラフィック レイヤー**

        リアルタイムのトラフィック情報をマップ上にオーバーレイします。
        
        ![マップ上のトラフィック レイヤー](media/power-bi-visual/traffic-layer-thumb.png)
    :::column-end:::
:::row-end:::

すべてのデータ レンダリング レイヤーと **タイル レイヤー** には、最小と最大のズーム レベルのオプションがあります。これは、レイヤーを表示するズーム レベルの範囲を指定するために使用されます。 これにより、1 種類のレンダリング レイヤーが 1 つのズーム レベルで使用され、別のズーム レベルで別のレンダリング レイヤーに切り替えることができます。

これらのレイヤーには、マップ内の他のレイヤーに合わせて配置するオプションもあります。 複数のデータ レンダリング レイヤーを使用する場合、マップに追加された順序によって、 **[Layer position]\(レイヤーの位置\)** の値が同じであるときの相対的な階層化の順序が決まります。

## <a name="general-layer-settings"></a>標準レイヤーの設定

**書式** ウィンドウの標準レイヤー セクションは、**フィールド** ウィンドウ (バブルレイヤー、横棒グラフ) の Power BI データセットに接続されているレイヤーに適用される一般的な設定です。

| 設定     | 説明   |
|-------------|---------------|
| 未選択の透明度 | 1 つまたは複数の図形が選択されている場合の選択されていない図形の透明度。  |
| ゼロを表示              | サイズの値がゼロのポイントを、最小半径を使用してマップに表示するかどうかを指定します。 |
| 負数を表示          | 負のサイズ値の絶対値をプロットするかどうかを指定します。   |
| 最小データ値          | 拡大縮小される入力データの最小値。 外れ値をクリッピングする場合に適しています。  |
| 最大データ値          | 拡大縮小される入力データの最大値。 外れ値をクリッピングする場合に適しています。  |

## <a name="next-steps"></a>次のステップ

マップにデータを表示する方法を変更します。

> [!div class="nextstepaction"]
> [バブル レイヤーを追加する](power-bi-visual-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [横棒グラフ レイヤーを追加する](power-bi-visual-add-bar-chart-layer.md)

マップにコンテキストをさらに追加する:

> [!div class="nextstepaction"]
> [参照レイヤーを追加する](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [タイル レイヤーを追加する](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [リアルタイム トラフィックを表示する](power-bi-visual-show-real-time-traffic.md)
