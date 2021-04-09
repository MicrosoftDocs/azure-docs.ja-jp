---
title: Azure Maps Power BI ビジュアルに参照レイヤーを追加する | Microsoft Azure Maps
description: この記事では、Power BI 用の Microsoft Azure Maps ビジュアルで参照レイヤーを使用する方法について学習します。
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: eb6c9fed42f263ca6cfaa1ea975d31cb8f4a75cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86261478"
---
# <a name="add-a-reference-layer"></a>参照レイヤーを追加する

参照レイヤー機能を使用すると、セカンダリ空間データセットをビジュアルにアップロードし、マップ上にオーバーレイして追加のコンテキストを提供できます。 このデータセットは Power BI によってホストされ、`.json` または `.geojson` のファイル拡張子の [GeoJSON ファイル](https://wikipedia.org/wiki/GeoJSON)である必要があります。

**GeoJSON** ファイルを参照レイヤーとして追加するには、**書式** ウィンドウにアクセスし、 **[参照レイヤー]** セクションを展開して、 **[+ Add local file]\(+ ローカル ファイルの追加\)** ボタンを押します。

GeoJSON ファイルが参照レイヤーに追加されると、そのファイルの名前が **[+ Add local file]\(+ ローカル ファイルの追加\)** ボタンの代わりに、 **[X]** と共に表示されます。 **[X]** ボタンを押して、ビジュアルからデータを削除し、Power BI から GeoJSON ファイルを削除します。

次のマップは、人口別に色分けされた [2016 年のコロラドの人口調査標準地域](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Common/data/geojson)を表示しています。

> [!div class="mx-imgBorder"]
> ![参照レイヤーとして人口別に色分けされた、2016 年のコロラドの人口調査標準地域を示すマップ](media/power-bi-visual/reference-layer-CO-census-tract.png)

**[参照レイヤー]** セクションで使用できる **書式** ウィンドウのすべての設定を次に示します。

| 設定              | 説明   |
|----------------------|---------------|
| 参照レイヤーのデータ | マップ内の追加レイヤーとしてビジュアルにアップロードする GeoJSON ファイルのデータ。 **[+ Add local file]\(+ ローカル ファイルの追加\)** ボタンで、ファイルのダイアログが開かれ、ユーザーが `.json` または `.geojson` のファイル拡張子の GeoJSON ファイルを選択するために使用できます。 |

> [!NOTE]
> Azure Maps ビジュアルのこのプレビューでは、参照レイヤーは、マップに最初の 5,000 個の図形機能のみが読み込まれます。 この制限は、今後の更新で緩和されます。

## <a name="styling-data-in-a-reference-layer"></a>参照レイヤーでのデータのスタイル指定

GeoJSON ファイル内の各機能にプロパティを追加して、マップ上でどのようにスタイル指定されるかをカスタマイズできます。 この機能では、Azure Maps Web SDK のシンプルなデータ レイヤー機能を使用します。 詳細については、[サポートされているスタイル プロパティ](spatial-io-add-simple-data-layer.md#default-supported-style-properties)に関するドキュメントを参照してください。 セキュリティ上の理由から、Azure Maps ビジュアル内ではカスタム アイコン イメージはサポートされていません。

表示される色を赤に設定した GeoJSON ポイント機能の例を次に示します。

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": {
        "color": "red"
    }
}
```
## <a name="next-steps"></a>次のステップ

マップにコンテキストをさらに追加します。

> [!div class="nextstepaction"]
> [タイル レイヤーを追加する](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [リアルタイム トラフィックを表示する](power-bi-visual-show-real-time-traffic.md)
