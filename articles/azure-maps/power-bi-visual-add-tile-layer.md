---
title: Azure Maps Power BI ビジュアルにタイル レイヤーを追加する | Microsoft Azure Maps
description: この記事では、Power BI 用の Microsoft Azure Maps ビジュアルでタイル レイヤーを使用する方法について説明します。
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 6ab99f32932f39d5ad140b7a16d16ceae30fff54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92896210"
---
# <a name="add-a-tile-layer"></a>タイル レイヤーを追加する

タイル レイヤー機能を使用すると、参照レイヤー機能と同様に、追加のデータを地図にオーバーレイして、より多くのコンテキストを提供できます。 タイル レイヤーを使用すると、Azure Maps のベース地図タイルの上に画像をスーパーインポーズできます。 これは、ドローンからの画像や数百万行のデータなど、大規模または複雑なデータセットをオーバーレイするための優れた方法です。

> [!div class="mx-imgBorder"]
> ![Azure Maps からの現在の赤外線気象データを示すタイル レイヤーの上にバブル レイヤーを表示した地図](media/power-bi-visual/radar-tile-layer-with-bubbles.png)

タイル レイヤーでは、サーバーからタイルが読み込まれます。 これらの画像は、事前にレンダリングすることも、動的にレンダリングすることもできます。 事前にレンダリングされた画像は、タイル レイヤーが認識する名前付け規則を使用して、サーバー上の他の画像と同様に保存されます。 動的にレンダリングされた画像は、ほぼリアルタイムで画像を読み込むためのサービスを使用します。 タイル レイヤーは、地図上で大規模なデータセットを視覚化する場合に適しています。 画像からタイル レイヤーを生成できるだけでなく、ベクター データもタイル レイヤーとしてレンダリングできます。

タイル サービスが使用できる領域の境界ボックスとズーム範囲を設定として渡すことによって、タイルが要求される領域を制限できます。 これにより、ビジュアルとタイル サービスの両方のパフォーマンスが向上します。 次に示すのは、 **[タイル レイヤー]** セクションの **[Format]\(フォーマット\)** ウィンドウで使用できるすべての設定の概要です。

| 設定        | 説明   |
|----------------|---------------|
| url            | タイル サービスを指す書式設定された URL。  |
| [Tile size] (タイル サイズ)      | タイルの幅と高さの両方の大きさを指定する整数値。   |
| [North bound] (北の境界)    | タイルが使用可能な境界ボックスの北緯。 |
| [South bound] (南の境界)    | タイルが使用可能な境界ボックスの南緯。 |
| [East bound] (東の境界)     | タイルが使用可能な境界ボックスの東経。  |
| [West bound] (西の境界)     | タイルが使用可能な境界ボックスの西経。   |
| 透明性   | タイル レイヤーの透明度。   |
| [Is TMS] (TMS である)         | タイル レイヤーの Y 座標軸を反転する仕様である Tile Map Service。 |
| [Min zoom] (最小ズーム)       | タイルで使用可能な最小ズーム レベル。 |
| Max zoom\(最大ズーム\)       | タイルで使用可能な最大ズーム レベル。  |
| Layer position\(レイヤーの位置\) | レイヤーの位置をその他の地図レイヤーに応じて相対的に指定します。 |

## <a name="tile-url-formatting"></a>[Tile URL formatting] (タイル URL の書式設定)

Azure Maps ビジュアルでは、次の 3 種類のタイル サービスの名前付け規則がサポートされています。

-   **X、Y、ズーム表記** - X はタイル グリッド内のタイルの列の位置、Y はそのタイルの行の位置、ズーム表記はズーム レベルに基づく値です。
-   **Quadkey 表記** - x、y、ズーム情報を組み合わせて、1 つの文字列値にします。 この文字列値は、1 つのタイルの一意識別子になります。
-   **境界ボックス** -  境界ボックス座標形式 `{west},{south},{east},{north}` を使用して画像を指定します。 この形式は、[Web マッピング サービス (WMS)](https://www.opengeospatial.org/standards/wms) で一般的に使用されています。

タイル URL は、タイル URL テンプレートの HTTPS URL であり、次のパラメーターを使用します。

-   `{x}` - タイルの X 位置。 `{y}` と `{z}` も必要です。
-   `{y}` - タイルの Y 位置。 `{x}` と `{z}` も必要です。
-   `{z}` - タイルのズーム レベル。 `{x}` と `{y}` も必要です。
-   `{quadkey}` - Bing Maps タイル システムの名前付け規則に基づくタイルの `quadkey` 識別子。
-   `{bbox-epsg-3857}` - EPSG 3857 空間参照系の `{west},{south},{east},{north}` 形式の境界ボックス文字列。

例として、Azure Maps の[気象レーダー タイル サービス](/rest/api/maps/renderv2/getmaptilepreview)の書式設定済みタイル URL を次に示します。 `[subscription-key]` は、Azure Maps のサブスクリプション キーのプレースホルダーです。

> `https://atlas.microsoft.com/map/tile?zoom={z}&x={x}&y={y}&tilesetId=microsoft.weather.radar.main&api-version=2.0&subscription-key=[subscription-key]`

Azure Maps のタイル システムの詳細については、「[ズーム レベルとタイル グリッド](zoom-levels-and-tile-grid.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

地図にコンテキストをさらに追加する:

> [!div class="nextstepaction"]
> [リアルタイム トラフィックを表示する](power-bi-visual-show-real-time-traffic.md)