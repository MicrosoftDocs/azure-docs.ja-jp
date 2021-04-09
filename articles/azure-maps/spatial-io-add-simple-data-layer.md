---
title: シンプルなデータ レイヤーの追加 | Microsoft Azure Maps
description: Azure Maps Web SDK によって提供される、空間 IO モジュールを使用してシンプルなデータ レイヤーを追加する方法について説明します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 90f3cb0ae44be176d3ae248988d098039c140c3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92896159"
---
# <a name="add-a-simple-data-layer"></a>シンプルなデータ レイヤーの追加

空間 IO モジュールでは、`SimpleDataLayer` クラスが提供されます。 このクラスを使用すると、スタイル設定されたフィーチャーをマップ上に簡単にレンダリングできます。 また、スタイル プロパティがあるデータ セットや、複数の geometry 型が混在するデータ セットをレンダリングすることもできます。 シンプルなデータ レイヤーでは、複数のレンダリング レイヤーをラップし、スタイル式を使用することにより、この機能を実現しています。 スタイル式は、これらのラップされたレイヤー内のフィーチャーの共通スタイル プロパティを検索します。 `atlas.io.read` 関数と `atlas.io.write` 関数は、これらのプロパティを使用して、サポートされているファイル形式にスタイルを読み書きします。 サポートされているファイル形式にプロパティを追加すると、ファイルをさまざまな目的で使用できます。 たとえば、ファイルを使用して、スタイル設定されたフィーチャーをマップに表示することができます。

`SimpleDataLayer` には、フィーチャーのスタイル設定に加えて、ポップアップ テンプレートを使用した組み込みのポップアップ フィーチャーも用意されています。 フィーチャーがクリックされると、ポップアップが表示されます。 必要に応じて、既定のポップアップ フィーチャーを無効にすることができます。 このレイヤーでは、クラスター化されたデータもサポートされます。 クラスターがクリックされると、マップでそのクラスターが拡大され、個々のポイントとサブクラスターに展開されます。

`SimpleDataLayer` クラスは、多くの geometry 型とフィーチャーに適用される多くのスタイルを持つ大規模なデータ セットで使用されることを想定しています。 このクラスを使用すると、スタイル式を含む 6 つのレイヤーのオーバーヘッドが追加されます。 そのため、コア レンダリング レイヤーを使用する方が効率的な場合もあります。 たとえば、コア レイヤーを使用して、フィーチャーに 2 つの geometry 型といくつかのスタイルをレンダリングする場合などです。

## <a name="use-a-simple-data-layer"></a>シンプルなデータ レイヤーを使用する

`SimpleDataLayer` クラスは、他のレンダリング レイヤーを使用する場合と同じように使用されます。 次のコードは、マップ内でのシンプルなデータ レイヤーの使用方法を示しています。

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Add a simple data layer for rendering data.
var layer = new atlas.layer.SimpleDataLayer(datasource);
map.layers.add(layer);
```

データ ソースにフィーチャーを追加します。 すると、シンプルなデータ レイヤーによって、フィーチャーの最適なレンダリング方法が割り出されます。 個々のフィーチャーのスタイルは、フィーチャーでプロパティとして設定できます。 次のコードは、`color` プロパティが `red` に設定された GeoJSON ポイント フィーチャーを示しています。 

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [0, 0]
    },
    "properties": {
        "color": "red"
    }
}
```

次のコードは、シンプルなデータ レイヤーを使用して上記のポイント フィーチャーをレンダリングします。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="シンプルなデータ レイヤーの使用" src="//codepen.io/azuremaps/embed/zYGzpQV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true"> <a href='https://codepen.io'>CodePen</a> で Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/zYGzpQV/'>シンプルなデータ レイヤーの使用</a>」を参照してください。
</iframe>

シンプルなデータ レイヤーの真の力は、次のような場合に発揮されます。

- データ ソースに複数の異なる種類のフィーチャーがある場合、または
- データ セット内のフィーチャーに複数のスタイル プロパティが個別に設定されている場合、または
- データ セットに含まれているものが正確にわからない場合

たとえば、XML データ フィードを解析する場合、フィーチャーの正確なスタイルと geometry 型がわからないことがあります。 次のサンプルは、KML ファイルのフィーチャーをレンダリングすることによって、シンプルなデータ レイヤーの能力を示します。 また、シンプルなデータ レイヤー クラスで提供されているさまざまなオプションのデモも行えます。

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="シンプルなデータ レイヤー オプション" src="//codepen.io/azuremaps/embed/gOpRXgy/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true"> <a href='https://codepen.io'>CodePen</a> で Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/gOpRXgy/'>シンプルなデータ レイヤー オプション</a>」を参照してください。
</iframe>


> [!NOTE]
> このシンプルなデータ層では、[ポップアップ テンプレート](map-add-popup.md#add-popup-templates-to-the-map) クラスを使用して、KML バルーンまたは機能プロパティがテーブルとして表示されます。 既定では、ポップアップでレンダリングされるすべてのコンテンツは、iframe 内にセキュリティ機能としてサンドボックス化されます。 ただし、次のような制限があります。
>
> - すべてのスクリプト、フォーム、ポインター ロック、上部ナビゲーションの機能は無効になります。 リンクは、クリックすると新しいタブで開くことができます。 
> - iframe の `srcdoc` パラメーターをサポートしていない古いブラウザーでは、少量のコンテンツのレンダリングに制限されます。
> 
> ポップアップに読み込まれるデータを信頼しており、潜在的に、ポップアップに読み込まれるこれらのスクリプトがアプリケーションにアクセスできるようにしたい場合は、ポップアップ テンプレートの `sandboxContent` オプションを false に設定することによって、これを無効にすることができます。 

## <a name="default-supported-style-properties"></a>既定でサポートされているスタイル プロパティ

既に説明したように、シンプルなデータ レイヤーでは、コア レンダリング レイヤー (バブル、記号、直線、多角形、押し出しポリゴン) のうちのいくつかがラップされます。 その後、式を使用して、個々のフィーチャーで有効なスタイル プロパティが検索されます。

Azure Maps と GitHub スタイルのプロパティは、サポートされているプロパティ名の 2 つの主なセットです。 さまざまな Azure Maps レイヤー オプションのほとんどのプロパティ名は、シンプルなデータ レイヤー内のフィーチャーのスタイル プロパティとしてサポートされています。 一部のレイヤー オプションには、GitHub で一般的に使用されているスタイル プロパティ名をサポートするために、式が追加されています。 これらのプロパティ名は、[GitHub の GeoJSON マップのサポート](https://help.github.com/en/github/managing-files-in-a-repository/mapping-geojson-files-on-github)で定義されており、プラットフォーム内に格納されてレンダリングされる GeoJSON ファイルをスタイル設定するために使用されます。 GitHub のすべてのスタイル設定プロパティ (`marker-symbol` スタイル設定プロパティを除く) は、シンプルなデータ レイヤーでサポートされています。

あまり一般的でないスタイルのプロパティに閲覧者が遭遇した場合、最も近い Azure Maps スタイルのプロパティに変換されます。 また、シンプルなデータ レイヤーの `getLayers` 関数を使用し、任意のレイヤーのオプションを更新することで、既定のスタイル式をオーバーライドできます。

次のセクションでは、シンプルなデータ レイヤーでサポートされている既定のスタイル プロパティについて詳しく説明します。 サポートされているプロパティ名の順序は、プロパティの優先順位でもあります。 同じレイヤー オプションに 2 つのスタイル プロパティが定義されている場合、リスト内の最初のプロパティの方が優先順位が高くなります。 カラーには任意の CSS3 カラー値 (16 進数、RGB、RGBA、HSL、HSLA、名前付きカラー値) を指定できます。

### <a name="bubble-layer-style-properties"></a>バブル レイヤーのスタイル プロパティ

フィーチャーが `Point` または `MultiPoint` で、フィーチャーにポイントをシンボルとしてレンダリングするためのカスタム アイコンとして使用される `image` プロパティがない場合、そのフィーチャーは `BubbleLayer` でレンダリングされます。

| レイヤー オプション | サポートされているプロパティ名 | 既定値 |
|--------------|----------------------------|---------------|
| `color` | `color`, `marker-color` | `'#1A73AA'` |
| `radius` | `size`<sup>1</sup>, `marker-size`<sup>2</sup>, `scale`<sup>1</sup> | `8` |
| `strokeColor` | `strokeColor`, `stroke` | `'#FFFFFF'` |

\[1\] `size` 値と `scale` 値はスカラー値と見なされ、`8` で乗算されます。

\[2\] GitHub `marker-size` オプションが指定されている場合、半径には次の値が使用されます。

| [マーカー サイズ] | Radius |
|-------------|--------|
| `small`     | `6`    |
| `medium`    | `8`    |
| `large`     | `12`   |

クラスターはバブル レイヤーを使用してもレンダリングされます。 既定では、クラスターの半径は `16` に設定されます。 クラスターの色は、次に定義されているように、クラスター内のポイントの数によって異なります。

| ポイントの数 | Color    |
|-------------|----------|
| &gt;= 100   | `red`    |
| &gt;= 10    | `yellow` |
| &lt; 10     | `green`  |

### <a name="symbol-style-properties"></a>シンボルのスタイル プロパティ

フィーチャーが `Point` または `MultiPoint` で、フィーチャーにポイントをシンボルとしてレンダリングするためのカスタム アイコンとして使用される `image` プロパティがある場合、そのフィーチャーは `SymbolLayer` でレンダリングされます。

| レイヤー オプション | サポートされているプロパティ名 | 既定値 |
|--------------|----------------------------|---------------|
| `image` | `image` | ``none`` |
| `size` | `size`, `marker-size`<sup>1</sup> | `1` |
| `rotation` | `rotation` | `0` |
| `offset` | `offset` | `[0, 0]` |
| `anchor` | `anchor` | `'bottom'` |

\[1\] GitHub `marker-size` オプションが指定されている場合は、アイコン サイズ オプションには次の値が使用されます。

| [マーカー サイズ] | シンボルのサイズ |
|-------------|-------------|
| `small`     | `0.5`       |
| `medium`    | `1`         |
| `large`     | `2`         |

ポイント フィーチャーがクラスターの場合、`point_count_abbreviated` プロパティはテキスト ラベルとしてレンダリングされます。 画像はレンダリングされません。

### <a name="line-style-properties"></a>線のスタイル プロパティ

このフィーチャーが `LineString`、`MultiLineString`、`Polygon`、`MultiPolygon` のいずれかの場合、フィーチャーは `LineLayer` でレンダリングされます。

| レイヤー オプション | サポートされているプロパティ名 | 既定値 |
|--------------|----------------------------|---------------|
| `strokeColor` | `strokeColor`, `stroke` | `'#1E90FF'` |
| `strokeWidth` | `strokeWidth`, `stroke-width`, `stroke-thickness` | `3` |
| `strokeOpacity` | `strokeOpacity`, `stroke-opacity` | `1` |

### <a name="polygon-style-properties"></a>多角形のスタイル プロパティ

フィーチャーが `Polygon` または `MultiPolygon`で、フィーチャーに `height` プロパティがない場合、または `height` プロパティがゼロの場合、このフィーチャーは `PolygonLayer` でレンダリングされます。

| レイヤー オプション | サポートされているプロパティ名 | 既定値 |
|--------------|----------------------------|---------------|
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `fillOpacity` | `fillOpacity`, '`fill-opacity` | `0.5` |

### <a name="extruded-polygon-style-properties"></a>浮き出し多角形のスタイル プロパティ

フィーチャーが `Polygon` または `MultiPolygon`で、0 より大きい値を持つ `height` プロパティがある場合、このフィーチャーは `PolygonExtrusionLayer` でレンダリングされます。

| レイヤー オプション | サポートされているプロパティ名 | 既定値 |
|--------------|----------------------------|---------------|
| `base` | `base` | `0` |
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `height` | `height` | `0` |

## <a name="next-steps"></a>次のステップ

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [SimpleDataLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)

> [!div class="nextstepaction"]
> [SimpleDataLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

マップに追加できる他のコード サンプルについては、次の記事をご覧ください。

> [!div class="nextstepaction"]
> [空間データの読み取りと書き込み](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [OGC マップ レイヤーの追加](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [WFS サービスへの接続](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [コア操作の活用](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [サポートされるデータ形式の詳細](spatial-io-supported-data-format-details.md)