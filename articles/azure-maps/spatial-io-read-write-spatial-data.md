---
title: 空間データの読み取りと書き込み | Microsoft Azure Maps
description: Azure Maps Web SDK によって提供される、空間 IO モジュールを使用してデータの読み取りと書き込みを行う方法について説明します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 133674e6764e12742f5b238946e943d9b5011cd2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92891331"
---
# <a name="read-and-write-spatial-data"></a>空間データの読み取りと書き込み

次の表は、空間 IO モジュールを使用した読み取り操作と書き込み操作でサポートされている空間ファイル形式を示しています。

| データ形式       | Read | Write |
|-------------------|------|-------|
| GeoJSON           | ✓  |  ✓  |
| GeoRSS            | ✓  |  ✓  |
| GML               | ✓  |  ✓  |
| GPX               | ✓  |  ✓  |
| KML               | ✓  |  ✓  |
| KMZ               | ✓  |  ✓  |
| 空間 CSV       | ✓  |  ✓  |
| Well-Known Text   | ✓  |  ✓  |

以降のセクションでは、空間 IO モジュールを使用して、空間データの読み取りと書き込みを行うためのさまざまなツールの概要を説明します。

## <a name="read-spatial-data"></a>空間データの読み取り

`atlas.io.read` 関数は、空間データを使用して、KML、GPX、GeoRSS、GeoJSON、CSV ファイルなどの一般的な空間データ形式を読み取るために使用される主要な関数です。 この関数は、これらの形式の圧縮バージョン (ZIP ファイルや KMZ ファイル) も読み取ることができます。 KMZ ファイル形式は、画像などのアセットも含むことができる、KML の圧縮されたバージョンです。 また、read 関数は、ファイルを指す URL をこれらのいずれの形式でも受け取ることができます。 URL は CORS が有効になっているエンドポイントでホストされている必要があります。そうでない場合は、読み取りオプションでプロキシ サービスを提供する必要があります。 プロキシ サービスは、CORS が有効になっていないドメイン上のリソースを読み込むために使用されます。 read 関数は、マップに画像アイコンを追加する Promise を返し、データを非同期的に処理して UI スレッドへの影響を最小限に抑えます。

圧縮されたファイル (ZIP または KMZ) を読み取る場合は、最初の有効なファイルに対して解凍とスキャンが行われます。 たとえば、doc.kml やその他の有効な拡張子 (.kml、.xml、geojson、.json、.csv、.tsv、.txt など) を持つファイルです。 次に、KML および GeoRSS ファイルで参照されている画像は、確実にアクセス可能にするためにプリロードされます。 アクセスできない画像データは、代替となるフォールバック画像を読み込むか、スタイルから削除されます。 KMZ ファイルから抽出された画像は、データ URI に変換されます。

read 関数の結果は `SpatialDataSet` オブジェクトです。 このオブジェクトは、GeoJSON FeatureCollection クラスを拡張します。 これは、そのフィーチャーをマップにレンダリングするために、そのままで `DataSource` に簡単に渡されます。 `SpatialDataSet` には、フィーチャー情報だけでなく、次の表に示すように、KML グラウンド オーバーレイ、処理メトリック、およびその他の詳細を含めることもできます。

| プロパティ名 | Type | 説明 | 
|---------------|------|-------------|
| `bbox` | `BoundingBox` | データ セット内のすべてのデータの境界ボックス。 |
| `features` | `Feature[]` | データ セット内の GeoJSON フィーチャー。 |
| `groundOverlays` | `(atlas.layer.ImageLayer | atlas.layers.OgcMapLayer)[]` | KML GroundOverlays の配列。 |
| `icons` | Record&lt;string, string&gt; | アイコン URL のセット。 キー = アイコン名、値 = URL。 |
| properties | any | 空間データ セットのドキュメント レベルで提供されるプロパティ情報。 |
| `stats` | `SpatialDataSetStats` | 空間データ セットのコンテンツと処理時間に関する統計情報。 |
| `type` | `'FeatureCollection'` | 読み取り専用の GeoJSON 型の値。 |

## <a name="examples-of-reading-spatial-data"></a>空間データの読み取りの例

次のコードは、空間データ セットを読み取り、`SimpleDataLayer` クラスを使用してマップ上にそれをレンダリングする方法を示しています。 このコードでは、URL で指し示される GPX ファイルが使用されます。

<br/>

<iframe height='500' scrolling='no' title='単純な空間データの読み込み' src='//codepen.io/azuremaps/embed/yLNXrZx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> で Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/yLNXrZx/'>単純な空間データの読み込み</a>」を参照してください。
</iframe>

次のコード デモでは、KML (KMZ) を読み取って、マップに読み込む方法を示します。 KML には、グラウンド オーバーレイを `ImageLyaer` または `OgcMapLayer` の形式で含めることができます。 これらのオーバーレイは、フィーチャーとは別にマップに追加する必要があります。 さらに、データ セットにカスタム アイコンが含まれている場合は、それらのアイコンをマップ リソースに読み込んでから、フィーチャーを読み込む必要があります。

<br/>

<iframe height='500' scrolling='no' title='マップに KML を読み込む' src='//codepen.io/azuremaps/embed/XWbgwxX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> で Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/XWbgwxX/'>マップに KML を読み込む</a>」を参照してください。
</iframe>

必要に応じて、CORS が有効になっていない可能性のあるクロス ドメイン アセットにアクセスするためのプロキシ サービスを提供することができます。 read 関数は、最初に CORS を使用して別のドメイン上のファイルにアクセスしようとします。 別のドメイン上のいずれかのリソースへの CORS を使用したアクセスが最初に失敗した後、プロキシ サービスが提供されている場合に限り、追加のファイルを要求します。 read 関数は、提供されたプロキシ URL の末尾にファイル URL を追加します。 次のコード スニペットは、read 関数にプロキシ サービスを渡す方法を示しています。

```javascript
//Read a file from a URL or pass in a raw data as a string.
atlas.io.read('https://nonCorsDomain.example.com/mySuperCoolData.xml', {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(async r => {
    if (r) {
        // Some code goes here . . .
    }
});

```

次のデモでは、区切りファイルを読み取り、それをマップにレンダリングする方法を示しています。 このケースでは、コードで空間データ列を含む CSV ファイルが使用されています。

<br/>

<iframe height='500' scrolling='no' title='区切りファイルの追加' src='//codepen.io/azuremaps/embed/ExjXBEb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> で Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/ExjXBEb/'>区切りファイルの追加</a>」を参照してください。
</iframe>

## <a name="write-spatial-data"></a>空間データの書き込み

空間 IO モジュールには、主に 2 つの write 関数があります。 `atlas.io.write` 関数が文字列を生成するのに対し、`atlas.io.writeCompressed` 関数は圧縮された ZIP ファイルを生成します。 圧縮された ZIP ファイルには、空間データを含むテキストベースのファイルが含まれます。 これらの関数はどちらも、データをファイルに追加する Promise を返します。 また、これらはどちらも、`SpatialDataSet`、`DataSource`、`ImageLayer`、`OgcMapLayer`、フィーチャー コレクション、フィーチャー、ジオメトリのうちいずれか、またはこれらのデータ型の任意の組み合わせの配列を書き込むことができます。 いずれかの関数を使用して書き込むときに、必要なファイル形式を指定できます。 ファイル形式が指定されていない場合、データは KML として書き込まれます。

次のツールでは、`atlas.io.write` 関数で使用できるほとんどの書き込みオプションのデモが行えます。

<br/>

<iframe height='700' scrolling='no' title='空間データ書き込みオプション' src='//codepen.io/azuremaps/embed/YzXxXPG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> で Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/YzXxXPG/'>空間データ書き込みオプション</a>」を参照してください。
</iframe>

## <a name="example-of-writing-spatial-data"></a>空間データの書き込みの例

次のサンプルでは、マップ上に空間ファイルをドラッグ アンド ドロップして読み込むことができます。 マップから GeoJSON データをエクスポートして、それをサポートされているいずれかの空間データ形式で文字列または圧縮ファイルとして書き込むことができます。

<br/>

<iframe height='700' scrolling='no' title='マップ上に空間ファイルをドラッグ アンド ドロップする' src='//codepen.io/azuremaps/embed/zYGdGoO/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> で Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/zYGdGoO/'>マップ上に空間ファイルをドラッグ アンド ドロップする</a>」を参照してください。
</iframe>

必要に応じて、CORS が有効になっていない可能性のあるクロス ドメイン アセットにアクセスするためのプロキシ サービスを提供することができます。 次のコード スニペットは、プロキシ サービスを組み込むことができることを示しています。

```javascript
atlas.io.read(data, {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(
    //Success
    function(r) {
        //some code goes here ...
    }
);
```

## <a name="read-and-write-well-known-text-wkt"></a>Well-Known Text (WKT) の読み取りと書き込み

[Well-Known Text](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry) (WKT) は、空間ジオメトリをテキストとして表現するための Open Geospatial Consortium (OGC) 標準です。 多くの地理空間システムでは、PostGIS プラグインを使用した Azure SQL や Azure PostgreSQL などの WKT がサポートされています。 ほとんどの OGC 標準と同様に、座標は "x y" 規則に合わせて "経度 緯度" として書式設定されます。 例として、WKT 形式を使用すると、経度 -110 と緯度 45 のポイントは、`POINT(-110 45)` として書き込むことができます。

Well Known Text は、`atlas.io.ogc.WKT.read` 関数を使用して読み取ることができ、`atlas.io.ogc.WKT.write` 関数を使用して書き込むことができます。

## <a name="examples-of-reading-and-writing-well-known-text-wkt"></a>Well-Known Text (WKT) の読み取りと書き込みの例

次のコードは、Well Known Text 文字列 `POINT(-122.34009 47.60995)` を読み取り、それをバブル レイヤーを使用してマップ上にレンダリングする方法を示しています。

<br/>

<iframe height='500' scrolling='no' title='Well-Known Text の読み取り' src='//codepen.io/azuremaps/embed/XWbabLd/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> で Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/XWbabLd/'>Well-Known Text の読み取り</a>」を参照してください。
</iframe>

次のコードは、Well Known Text の読み取りと書き込みを行ったり来たりしてデモを行えます。

<br/>

<iframe height='700' scrolling='no' title='Well-Known Text の読み取りと書き込み' src='//codepen.io/azuremaps/embed/JjdyYav/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> で Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/JjdyYav/'>Well-Known Text の読み取りと書き込み</a>」を参照してください。
</iframe>

## <a name="read-and-write-gml"></a>GML の読み取りと書き込み

GML は、他の XML 仕様の拡張としてよく使用される、空間 XML ファイルの仕様です。 GeoJSON データは、`atlas.io.core.GmlWriter.write` 関数を使用して、GML タグを持つ XML として書き込むことができます。 GML を含む XML は、`atlas.io.core.GmlReader.read` 関数を使用して読み取ることができます。 read 関数には、次の 2 つのオプションがあります。

- `isAxisOrderLonLat` オプション - 座標軸の順序 ("緯度、経度" または "経度、緯度") は、データ セット間で異なる場合があり、常に明確に定義されているわけではありません。 既定では、GML リーダーは座標データを "緯度、経度" として読み取りますが、このオプションを true に設定すると、"経度、緯度" として読み取ります。
- `propertyTypes` オプション - このオプションはキー値の参照テーブルで、ここではキーがデータ セット内のプロパティの名前です。 値は、解析時に値をキャストするオブジェクトの種類です。 サポートされる型の値は `string`、`number`、`boolean`、`date` です。 プロパティが参照テーブルにない場合、または型が定義されていない場合、プロパティは文字列として解析されます。

入力データが XML であるが、データが他のサポートされる空間 XML 形式の 1 つではないことが検出されると、`atlas.io.read` 関数が `atlas.io.core.GmlReader.read` 関数の既定になります。

`GmlReader` は、次のいずれかの SRID を持つ座標を解析します。

- EPSG:4326 (推奨)
- EPSG:4269、EPSG:4283、EPSG:4258、EPSG:4308、EPSG:4230、EPSG:4272、EPSG:4271、EPSG:4267、EPSG:4608、EPSG:4674 (場合によっては、わずかな許容誤差があります)
- EPSG:3857、EPSG:102100、EPSG:3785、EPSG:900913、EPSG:102113、EPSG:41001、EPSG:54004

## <a name="more-resources"></a>その他のリソース

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

[atlas.io 静的関数](/javascript/api/azure-maps-spatial-io/atlas.io)

[SpatialDataSet](/javascript/api/azure-maps-spatial-io/atlas.spatialdataset)

[SpatialDataSetStats](/javascript/api/azure-maps-spatial-io/atlas.spatialdatasetstats)

[GmlReader](/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlreader)

[GmlWriter](/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlwriter)

[atlas.io.ogc.WKT 関数](/javascript/api/azure-maps-spatial-io/atlas.io.ogc.wkt)

[WFS サービスへの接続](spatial-io-connect-wfs-service.md)

[コア操作の活用](spatial-io-core-operations.md)

[サポートされるデータ形式の詳細](spatial-io-supported-data-format-details.md)


## <a name="next-steps"></a>次のステップ

マップに追加できる他のコード サンプルについては、次の記事をご覧ください。

[OGC マップ レイヤーの追加](spatial-io-add-ogc-map-layer.md)