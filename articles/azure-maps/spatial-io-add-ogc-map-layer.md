---
title: Open Geospatial Consortium (OGC) のマップ レイヤーを追加する | Microsoft Azure Maps
description: マップに OGC マップ レイヤーをオーバーレイする方法と、OgcMapLayer クラスのさまざまなオプションを使用する方法について説明します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 7d94a681fa987a4b23dbcda744c2f8516da4437a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92891667"
---
# <a name="add-a-map-layer-from-the-open-geospatial-consortium-ogc"></a>Open Geospatial Consortium (OGC) のマップ レイヤーを追加する

`atlas.layer.OgcMapLayer` クラスを使用すると、Web Map Service (WMS) の画像や Web Map Tile Service (WMTS) の画像をマップにオーバーレイすることができます。 WMS は、ジオリファレンスされたマップ画像をインターネット経由で提供する OGC によって開発された標準プロトコルです。 画像のジオリファレンスとは、画像を地理的な場所と関連付けるプロセスです。 WMTS も OGC によって開発された標準プロトコルです。 これは、事前レンダリングされ、ジオリファレンスされたマップ タイルを提供するように設計されています。

以下のセクションでは、`OgcMapLayer` クラスでサポートされている Web Map Service の機能の概要を説明します。

**Web Map Service (WMS)**

- サポート対象のバージョン: `1.0.0`、`1.1.0`、`1.1.1`、および `1.3.0`
- このサービスでは、`EPSG:3857` のプロジェクション システムをサポートしているか、または再投影を処理する必要があります。
- GetFeatureInfo では、サービスで `EPSG:4326` がサポートされるか、再投影が処理される必要があります。 
- サポートされている操作:

    | 操作 | 説明 |
    | :-- | :-- |
    | GetCapabilities | サポートされている機能を含む、サービスに関するメタデータを取得します |
    | GetMap | 指定された領域のマップ イメージを取得します |
    | GetFeatureInfo | 機能の基になるデータを含む `feature_info` を取得します |

**Web Map Tile Service (WMTS)**

- サポート対象のバージョン: `1.0.0`
- タイルは、`TileWidth == TileHeight` を満たす正方形である必要があります。
- サポート対象の CRS: `EPSG:3857` または `GoogleMapsCompatible` 
- TileMatrix 識別子は、マップのズーム レベルに対応する整数値である必要があります。 Azure マップ上でのズーム レベルは、`"0"` から `"22"` の値です。 つまり、`"00"` はサポートされませんが、`"0"` はサポートされます。
- サポートされている操作:

    | 操作 | 説明 |
    | :-- | :-- |
    | GetCapabilities | サポート対象の操作と機能を取得します |
    | GetTile | 特定のタイルの画像を取得します |

## <a name="overlay-an-ogc-map-layer"></a>OGC マップ レイヤーのオーバーレイ

`url` には、サービスのベース URL を使用することも、サービスの機能を取得するクエリを含む完全な URL を使用することもできます。 指定した詳細に応じ、サービスに最初にアクセスする方法を決定するために、WFS クライアントがいくつかの標準的な URL 形式を試す場合があります。

次のコードは、マップに OGC マップ レイヤーをオーバーレイする方法を示しています。

<br/>

<iframe height='700' scrolling='no' title='OGC マップ レイヤーの例' src='//codepen.io/azuremaps/embed/xxGLZWB/?height=700&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> で Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/xxGLZWB/'>OGC マップ レイヤーの例</a>」を参照してください。
</iframe>

## <a name="ogc-map-layer-options"></a>OGC マップ レイヤーのオプション

次のサンプルでは、さまざまな OGC マップ レイヤー オプションを示しています。 CodePen を編集するには、右上隅にある CodePen のボタンをクリックします。

<br/>

<iframe height='700' scrolling='no' title='OGC マップ レイヤーのオプション' src='//codepen.io/azuremaps/embed/abOyEVQ/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> で Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/abOyEVQ/'>OGC マップ レイヤー オプション</a>」を参照してください。
</iframe>

## <a name="ogc-web-map-service-explorer"></a>OGC Web Map Service エクスプローラー

次のツールは、Web Map Service (WMS) と Web Map Tile Service (WMTS) の画像をレイヤーとしてオーバーレイします。 マップにサービスのどのレイヤーをレンダリングするかを選択することができます。 これらのレイヤーに関連付けられている凡例を表示することもできます。

<br/>

<iframe height='750' style='width: 100%;' scrolling='no' title='OGC Web Map Service エクスプローラー' src='//codepen.io/azuremaps/embed/YzXxYdX/?height=750&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> で Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen 「<a href='https://codepen.io/azuremaps/pen/YzXxYdX/'>OGC Web Map Service エクスプローラー</a>」を参照してください。
</iframe>

プロキ シサービスを使用するためのマップ設定を指定することもできます。 プロキシ サービスを使用すると、CORS が有効になっていないドメインにホストされているリソースを読み込むことができます。

## <a name="next-steps"></a>次のステップ

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [OgcMapLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.ogcmaplayer)

> [!div class="nextstepaction"]
> [OgcMapLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.ogcmaplayeroptions)

次を参照してください。これらの記事には、お使いのマップに追加できるコード サンプルがあります。

> [!div class="nextstepaction"]
> [WFS サービスへの接続](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [コア操作の活用](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [サポートされるデータ形式の詳細](spatial-io-supported-data-format-details.md)