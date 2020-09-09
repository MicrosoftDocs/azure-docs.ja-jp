---
title: サポートされているデータ形式の詳細 | Microsoft Azure Maps
description: 空間 IO モジュールで区切られた空間データを解析する方法について説明します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4b7c82e4650c7680709e809d9f563d79f068601f
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2020
ms.locfileid: "87127929"
---
# <a name="supported-data-format-details"></a>サポートされるデータ形式の詳細

この記事では、すべての XML タグと Well-Known Text の geometry 型の読み取りおよび書き込みのサポートについて詳しく説明します。 区切られた空間データを空間 IO モジュールで解析する方法についても詳しく説明します。

## <a name="supported-xml-namespaces"></a>サポートされている XML 名前空間

空間 IO モジュールでは、次の名前空間の XML タグがサポートされています。

| 名前空間プレフィックス | 名前空間 URI   | Notes                                                                    |
|:------------------|:-----------------|:----------------------------------------|
| `atom`           | `http://www.w3.org/2005/Atom`   |                                         |
| `geo`            | `http://www.w3.org/2003/01/geo/wgs84_pos#`  | GeoRSS ファイルでの読み取り専用のサポート。           |
| `georss`         | `http://www.georss.org/georss`  |                                                |
| `geourl`         | `http://geourl.org/rss/module/` | GeoRSS ファイルでの読み取り専用のサポート。                       |
| `gml`            | `http://www.opengis.net/gml`    |                                                        |
| `gpx`            | `http://www.topografix.com/GPX/1/1` |                                                   |
| `gpxx`           | `http://www.garmin.com/xmlschemas/GpxExtensions/v3` | GPX ファイルでの読み取り専用のサポート。 DisplayColor を解析して使用します。 シェイプ メタデータに追加されたその他のすべてのプロパティ。 |
| `gpx_style`      | `http://www.topografix.com/GPX/gpx_style/0/2`      | GPX ファイルでサポートされています。 線の色を使用します。 |
| `gx`             | `http://www.google.com/kml/ext/2.2` |                                                      |
| `kml`            | `http://www.opengis.net/kml/2.2`    |                                                      |
| `rss`            |                                 | 読み取り専用。 GeoRSS は、Atom 形式を使用して書き込みます。              |

## <a name="supported-xml-elements"></a>サポートされている XML 要素

空間 IO モジュールでは、次の XML 要素がサポートされています。 サポートされていない XML タグはすべて、JSON オブジェクトに変換されます。 その後、各タグは、親シェイプまたはレイヤーの `properties` フィールドにプロパティとして追加されます。

### <a name="kml-elements"></a>KML 要素

空間 IO モジュールでは、次の KML 要素がサポートされています。

| 要素名         | Read    | Write   | Notes                                                                                                                      |
|----------------------|---------|---------|----------------------------------------------------------------------------------------------------------------------------|
| `address`            | 部分的 | はい     | オブジェクトは解析されますが、シェイプの配置には使用されません。                                                                    |
| `AddressDetails`     | 部分的 | no      | オブジェクトは解析されますが、シェイプの配置には使用されません。                                                                    |
| `atom:author`        | はい     | はい     |                                                                                                                            |
| `atom:link`          | はい     | はい     |                                                                                                                            |
| `atom:name`          | はい     | はい     |                                                                                                                            |
| `BalloonStyle`       | 部分的 | 部分的 | `displayMode` はサポートされていません。 `PopupTemplate` に変換されます。 書き込むには、書き込むフィーチャーのプロパティとして `popupTemplate` プロパティを追加します。 |
| `begin`              | はい     | はい     |                                                                                                                            |
| `color`              | はい     | はい     | `#AABBGGRR` と `#BBGGRR` が含まれます。 CSS の色の文字列に解析されます                                                           |
| `colorMode`          | はい     | no      |                                                                                                                            |
| `coordinates`        | はい     | はい     |                                                                                                                            |
| `Data`               | はい     | はい     |                                                                                                                            |
| `description`        | はい     | はい     |                                                                                                                            |
| `displayName`        | はい     | はい     |                                                                                                                            |
| `Document`           | はい     | はい     |                                                                                                                            |
| `drawOrder`          | 部分的 | no      | グランド オーバーレイを読み取り、それらを並べ替えるために使用されます。 
| `east`               | はい     | はい     |                                                                                                                            |
| `end`                | はい     | はい     |                                                                                                                            |
| `ExtendedData`       | はい     | はい     | 型指定されていない `Data`、`SimpleData` または `Schema`、およびフォーム `$[dataName]` のエンティティの置換がサポートされています。                      |
| `extrude`            | 部分的 | 部分的 | 多角形でのみサポートされます。 高さが異なる多角形を持つ MultiGeometry は、個々のフィーチャーに分割されます。 線のスタイルはサポートされていません。 高度が 0 の多角形は、平面の多角形としてレンダリングされます。 読み取り時に、外部リングの最初の座標の高度が、その多角形の height プロパティとして追加されます。 その後、最初の座標の高度は、マップに多角形をレンダリングするために使用されます。 |
| `fill`               | はい     | はい     |                                                                                                                            |
| `Folder`             | はい     | はい     |                                                                                                                            |
| `GroundOverlay`      | はい     | はい     | `color` はサポートされていません                                                                                                   |
| `heading`            | 部分的 | no      | 解析されますが、`SimpleDataLayer` でレンダリングされません。 データがシェイプのプロパティに格納されている場合にのみ書き込みます。                 |
| `hotSpot`            | はい     | 部分的 | データがシェイプのプロパティに格納されている場合にのみ書き込みます。 単位は "ピクセル" としてのみ出力されます。                         |
| `href`               | はい     | はい     |                                                                                                                            |
| `Icon`               | 部分的 | 部分的 | 解析されますが、`SimpleDataLayer` でレンダリングされません。 URI データが含まれている場合は、シェイプのアイコン プロパティのみを書き込みます。 サポートされるのは `href` のみです。 |
| `IconStyle`          | 部分的 | 部分的 | `icon`、`heading`、`colorMode`、`hotspots` の値は解析されますが、`SimpleDataLayer` ではレンダリングされません         |
| `innerBoundaryIs`    | はい     | はい     |                                                                                                                            |
| `kml`                | はい     | はい     |                                                                                                                            |
| `LabelStyle`         | no      | no      |                                                                                                                            |
| `LatLonBox`          | はい     | はい     |                                                                                                                            |
| `gx:LatLonQuad`      | はい     | はい     |                                                                                                                            |
| `LinearRing`         | はい     | はい     |                                                                                                                            |
| `LineString`         | はい     | はい     |                                                                                                                            |
| `LineStyle`          | はい     | はい     | `colorMode` はサポートされていません。                                                                                         |
| `Link`               | はい     | no      | ネットワーク リンクでは、`href` プロパティのみがサポートされています。                                                                   |
| `MultiGeometry`      | 部分的 | 部分的 | 読み取り時に個々のフィーチャーに分割される場合があります。                                                                     |
| `name`               | はい     | はい     |                                                                                                                            |
| `NetworkLink`        | はい     | no      | リンクは、ドキュメントと同じドメインにある必要があります。                                                                  |
| `NetworkLinkControl` | no      | no      |                                                                                                                            |
| `north`              | はい     | はい     |                                                                                                                            |
| `open`               | はい     | はい     |                                                                                                                            |
| `outerBoundaryIs`    | はい     | はい     |                                                                                                                            |
| `outline`            | はい     | はい     |                                                                                                                            |
| `overlayXY`          | no      | no      |                                                                                                                            |
| `Pair`               | 部分的 | no      | `StyleMap` では `normal` スタイルのみがサポートされています。 `highlight` はサポートされていません。                                   |
| `phoneNumber`        | はい     | はい     |                                                                                                                            |
| `PhotoOverlay`       | no      | no      |                                                                                                                            |
| `Placemark`          | はい     | はい     |                                                                                                                            |
| `Point`              | はい     | はい     |                                                                                                                            |
| `Polygon`            | はい     | はい     |                                                                                                                            |
| `PolyStyle`          | はい     | はい     |                                                                                                                            |
| `Region`             | 部分的 | 部分的 | `LatLongBox` は、ドキュメント レベルでサポートされています。                                                                      |
| `rotation`           | no      | no      |                                                                                                                            |
| `rotationXY`         | no      | no      |                                                                                                                            |
| `scale`              | no      | no      |                                                                                                                            |
| `Schema`             | はい     | はい     |                                                                                                                            |
| `SchemaData`         | はい     | はい     |                                                                                                                            |
| `schemaUrl`          | 部分的 | はい     | KMZ に含まれていない外部ドキュメントからのスタイルの読み込みはサポートされていません。                             |
| `ScreenOverlay`      | no      | no      |                                                                                                                            |
| `screenXY`           | no      | no      |                                                                                                                            |
| `SimpleData`         | はい     | はい     |                                                                                                                            |
| `SimpleField`        | はい     | はい     |                                                                                                                            |
| `size`               | no      | no      |                                                                                                                            |
| `Snippet`            | 部分的 | 部分的 | `maxLines` 属性は無視されます。                                                                                  |
| `south`              | はい     | はい     |                                                                                                                            |
| `Style`              | はい     | はい     |                                                                                                                            |
| `StyleMap`           | 部分的 | no      | `StyleMap` では標準スタイルのみがサポートされています。                                                                        |
| `styleUrl`           | 部分的 | はい     | 外部スタイルの URL はサポートされていません。                                                                         |
| `text`               | はい     | はい     | `$[geDirections]` の置換はサポートされていません                                                                          |
| `textColor`          | はい     | はい     |                                                                                                                            |
| `TimeSpan`           | はい     | はい     |                                                                                                                            |
| `TimeStamp`          | はい     | はい     |                                                                                                                            |
| `value`              | はい     | はい     |                                                                                                                            |
| `viewRefreshMode`    | 部分的 | no      |  WMS サービスをポイントしている場合、グラウンド オーバーレイでは `onStop` のみがサポートされます。 `BBOX=[bboxWest],[bboxSouth],[bboxEast],[bboxNorth]` を URL に追加し、マップの移動に合わせて更新します。  |
| `visibility`         | はい     | はい     |                                                                                                                            |
| `west`               | はい     | はい     |                                                                                                                            |
| `when`               | はい     | はい     |                                                                                                                            |
| `width`              | はい     | はい     |                                                                                                                            |

### <a name="georss-elements"></a>GeoRSS 要素

空間 IO モジュールでは、次の GeoRSS 要素がサポートされています。

| 要素名             | Read    | Write | Notes                                                                                          |
|--------------------------|---------|-------|------------------------------------------------------------------------------------------------|
| `atom:author`            | はい     | はい   |                                                                                                |
| `atom:category`          | はい     | はい   |                                                                                                |
| `atom:content`           | はい     | はい   |                                                                                                |
| `atom:contributor`       | はい     | はい   |                                                                                                |
| `atom:email`             | はい     | はい   |                                                                                                |
| `atom:entry`             | はい     | はい   |                                                                                                |
| `atom:feed`              | はい     | はい   |                                                                                                |
| `atom:icon`              | はい     | はい   |                                                                                                |
| `atom:id`                | はい     | はい   |                                                                                                |
| `atom:link`              | はい     | はい   |                                                                                                |
| `atom:logo`              | はい     | はい   |                                                                                                |
| `atom:name`              | はい     | はい   |                                                                                                |
| `atom:published`         | はい     | はい   |                                                                                                |
| `atom:rights`            | はい     | はい   |                                                                                                |
| `atom:source`            | はい     | はい   |                                                                                                |
| `atom:subtitle`          | はい     | はい   |                                                                                                |
| `atom:summary`           | はい     | はい   |                                                                                                |
| `atom:title`             | はい     | はい   |                                                                                                |
| `atom:updated`           | はい     | はい   |                                                                                                |
| `atom:uri`               | はい     | はい   |                                                                                                |
| `geo:lat`                | はい     | no    | `georss:point` として書き込まれます。                                                                   |
| `geo:lon`                | はい     | no    | `georss:point` として書き込まれます。                                                                   |
| `geo:long`               | はい     | no    | `georss:point` として書き込まれます。                                                                   |
| `georss:box`             | はい     | no    | 多角形として読み取り、"四角形" の `subType` プロパティを指定します                                |
| `georss:circle`          | はい     | はい   |                                                                                                |
| `georss:elev`            | はい     | はい   |                                                                                                |
| `georss:featurename`     | はい     | はい   |                                                                                                |
| `georss:featuretypetag`  | はい     | はい   |                                                                                                |
| `georss:floor`           | はい     | はい   |                                                                                                |
| `georss:line`            | はい     | はい   |                                                                                                |
| `georss:point`           | はい     | はい   |                                                                                                |
| `georss:polygon`         | はい     | はい   |                                                                                                |
| `georss:radius`          | はい     | はい   |                                                                                                |
| `georss:relationshiptag` | はい     | はい   |                                                                                                |
| `georss:where`           | はい     | はい   |                                                                                                |
| `geourl:latitude`        | はい     | no    | `georss:point` として書き込まれます。                                                                   |
| `geourl:longitude`       | はい     | no    | `georss:point` として書き込まれます。                                                                   |
| `position`               | はい     | no    | 一部の XML フィードでは、`georss:where` タグでラップするのではなく、位置タグを使用して GML をラップします。 このタグを読み取りますが、`georss:where` タグを使用して書き込みます。 |
| `rss`                    | はい     | no    | GeoRSS は ATOM 形式で記述されています。                                                                 |
| `rss:author`             | はい     | 部分的 | `atom:author` として書き込まれます。                                                                 |
| `rss:category`           | はい     | 部分的 | `atom:category` として書き込まれます。                                                               |
| `rss:channel`            | はい     | no    |                                                                                                |
| `rss:cloud`              | はい     | no    |                                                                                                |
| `rss:comments`           | はい     | no    |                                                                                                |
| `rss:copyright`          | はい     | 部分的 | シェイプに `rights` `properties` プロパティがまだない場合は、`atom:rights` として書き込まれます。       |
| `rss:description`        | はい     | 部分的 | シェイプに `content` `properties` プロパティがまだない場合は、`atom:content` として書き込まれます。      |
| `rss:docs`               | はい     | no    |                                                                                                |
| `rss:enclosure`          | はい     | no    |                                                                                                |
| `rss:generator`          | はい     | no    |                                                                                                |
| `rss:guid`               | はい     | 部分的 | シェイプに `id` `properties` プロパティがまだない場合は、`atom:id` として書き込まれます。         |
| `rss:image`              | はい     | 部分的 | シェイプに `logo` `properties` プロパティがまだない場合は、`atom:logo` として書き込まれます。      |
| `rss:item`               | はい     | 部分的 | `atom:entry` として書き込まれます。                                                                  |
| `rss:language`           | はい     | no    |                                                                                                |
| `rss:lastBuildDate`      | はい     | 部分的 | シェイプに `updated` `properties` プロパティがまだない場合は、`atom:updated` として書き込まれます。     |
| `rss:link`               | はい     | 部分的 | `atom:link` として書き込まれます。                                                                   |
| `rss:managingEditor`     | はい     | 部分的 | `atom:contributor` として書き込まれます。                                                            |
| `rss:pubDate`            | はい     | 部分的 | シェイプに `published` `properties` プロパティがまだない場合は、`atom:published` として書き込まれます。  |
| `rss:rating`             | はい     | no    |                                                                                                |
| `rss:skipDays`           | はい     | no    |                                                                                                |
| `rss:skipHours`          | はい     | no    |                                                                                                |
| `rss:source`             | はい     | 部分的 | `atom:link` を含む `atom:source` として書き込まれます。                                       |
| `rss:textInput`          | はい     | no    |                                                                                                |
| `rss:title`              | はい     | 部分的 | `atom:title` として書き込まれます。                                                                  |
| `rss:ttl`                | はい     | no    |                                                                                                |
| `rss:webMaster`          | はい     | no    |                                                                                                |

### <a name="gml-elements"></a>GML 要素

空間 IO モジュールでは、次の GML 要素がサポートされています。 

| 要素名            | Read | Write | Notes                                                                                  |
|-------------------------|------|-------|----------------------------------------------------------------------------------------|
| `gml:coordinates`       | はい  | no    | `gml:posList` として書き込まれます。                                                              |
| `gml:curveMember`       | はい  | no    |                                                                                        |
| `gml:curveMembers`      | はい  | no    |                                                                                        |
| `gml:Box`               | はい  | no    | `gml:Envelope` として書き込まれます。                                                             |
| `gml:description`       | はい  | はい   |                                                                                        |
| `gml:Envelope`          | はい  | はい   |                                                                                        |
| `gml:exterior`          | はい  | はい   |                                                                                        |
| `gml:Feature`           | はい  | no    | シェイプとして書き込まれます。                                                                    |
| `gml:FeatureCollection` | はい  | no    | ジオメトリ コレクションとして書き込まれます。                                                      |
| `gml:featureMember`     | はい  | no    | ジオメトリ コレクションとして書き込まれます。                                                      |
| `gml:geometry`          | はい  | no    | シェイプとして書き込まれます。                                                                    |
| `gml:geometryMember`    | はい  | はい   |                                                                                        |
| `gml:geometryMembers`   | はい  | はい   |                                                                                        |
| `gml:identifier`        | はい  | はい   |                                                                                        |
| `gml:innerBoundaryIs`   | はい  | no    | `gml.interior` を使用して書き込まれます。                                                          |
| `gml:interior`          | はい  | はい   |                                                                                        |
| `gml:LinearRing`        | はい  | はい   |                                                                                        |
| `gml:LineString`        | はい  | はい   |                                                                                        |
| `gml:lineStringMember`  | はい  | はい   |                                                                                        |
| `gml:lineStringMembers` | はい  | no    |                                                                                        |
| `gml:MultiCurve`        | はい  | no    | `gml:LineString` メンバーのみを読み取ります。 `gml.MultiLineString` として書き込まれます                  |
| `gml:MultiGeometry`     | 部分的  | 部分的   | FeatureCollection としてのみ読み取ります。                                              |
| `gml:MultiLineString`   | はい  | はい   |                                                                                        |
| `gml:MultiPoint`        | はい  | はい   |                                                                                        |
| `gml:MultiPolygon`      | はい  | はい   |                                                                                        |
| `gml:MultiSurface`      | はい  | no    | `gml:Polygon` メンバーのみを読み取ります。 `gml.MultiPolygon` として書き込まれます                        |
| `gml:name`              | はい  | はい   |                                                                                        |
| `gml:outerBoundaryIs`   | はい  | no    | `gml.exterior` を使用して書き込まれます。                                                          |
| `gml:Point`             | はい  | はい   |                                                                                        |
| `gml:pointMember`       | はい  | はい   |                                                                                        |
| `gml:pointMembers`      | はい  | no    |                                                                                        |
| `gml:Polygon`           | はい  | はい   |                                                                                        |
| `gml:polygonMember`     | はい  | はい   |                                                                                        |
| `gml:polygonMembers`    | はい  | no    |                                                                                        |
| `gml:pos`               | はい  | はい   |                                                                                        |
| `gml:posList`           | はい  | はい   |                                                                                        |
| `gml:surfaceMember`     | はい  | はい   |                                                                                        |

#### <a name="additional-notes"></a>追加情報

- メンバー要素では、子要素内に埋もれている可能性のあるジオメトリが検索されます。 この検索操作が必要になるのは、GML から拡張される XML 形式の多くでは、ジオメトリをメンバー要素の直接の子として配置できない場合があるからです。
- `srsName` は、WGS84 座標と次のコード ([EPSG:4326](https://epsg.io/4326)) および Web メルカトル ([EPSG:3857](https://epsg.io/3857)) またはその代替コードのいずれかに対しては部分的にサポートされています。 その他の座標系は、WGS84 として現状のまま解析されます。
- XML フィードを読み取るときに指定しない限り、軸の順序は XML フィード内のヒントに基づいて決定されます。 "緯度、経度" 軸の順序に対して優先順位が設定されます。
- GML ファイルへの書き込み時にプロパティに対してカスタム GML 名前空間を指定しない限り、追加のプロパティ情報は追加されません。

### <a name="gpx-elements"></a>GPX 要素

空間 IO モジュールでは、次の GPX 要素がサポートされています。

| 要素名             | Read    | Write   | Notes                                                                                       |
|--------------------------|---------|---------|---------------------------------------------------------------------------------------------|
| `gpx:ageofdgpsdata`      | はい     | はい     |                                                                                             |
| `gpx:author`             | はい     | はい     |                                                                                             |
| `gpx:bounds`             | はい     | はい     | 読み取り時に LocationRect に変換されます。                                                    |
| `gpx:cmt`                | はい     | はい     |                                                                                             |
| `gpx:copyright`          | はい     | はい     |                                                                                             |
| `gpx:desc`               | はい     | はい     | 他の XML 形式に合わせて読み取りを行うときに、description プロパティにコピーされます。               |
| `gpx:dgpsid`             | はい     | はい     |                                                                                             |
| `gpx:ele`                | はい     | はい     |                                                                                             |
| `gpx:extensions`         | 部分的 | 部分的 | 読み取り時に、スタイル情報が抽出されます。 その他のすべての拡張機能は、単純な JSON オブジェクトにフラット化されます。 シェイプのスタイル情報のみが書き込まれます。 |
| `gpx:geoidheight`        | はい     | はい     |                                                                                             |
| `gpx:gpx`                | はい     | はい     |                                                                                             |
| `gpx:hdop`               | はい     | はい     |                                                                                             |
| `gpx:link`               | はい     | はい     |                                                                                             |
| `gpx:magvar`             | はい     | はい     |                                                                                             |
| `gpx:metadata`           | はい     | はい     |                                                                                             |
| `gpx:name`               | はい     | はい     |                                                                                             |
| `gpx:pdop`               | はい     | はい     |                                                                                             |
| `gpx:rte`                | はい     | はい     |                                                                                             |
| `gpx:rtept`              | はい     | はい     |                                                                                             |
| `gpx:sat`                | はい     | はい     |                                                                                             |
| `gpx:src`                | はい     | はい     |                                                                                             |
| `gpx:sym`                | はい     | はい     | 値はキャプチャされますが、プッシュピン アイコンの変更には使用されません。                               |
| `gpx:text`               | はい     | はい     |                                                                                             |
| `gpx:time`               | はい     | はい     |                                                                                             |
| `gpx:trk`                | はい     | はい     |                                                                                             |
| `gpx:trkpt`              | はい     | はい     |                                                                                             |
| `gpx:trkseg`             | はい     | はい     |                                                                                             |
| `gpx:type`               | はい     | はい     |                                                                                             |
| `gpx:vdop`               | はい     | はい     |                                                                                             |
| `gpx:wpt`                | はい     | はい     |                                                                                             |
| `gpx_style:color`        | はい     | はい     |                                                                                             |
| `gpx_style:line`         | 部分的 | 部分的 | `color`、`opacity`、`width`、`lineCap` がサポートされています。                                           |
| `gpx_style:opacity`      | はい     | はい     |                                                                                             |
| `gpx_style:width`        | はい     | はい     |                                                                                             |
| `gpxx:DisplayColor`      | はい     | no      | シェイプの色を指定するために使用されます。 書き込み時には、`gpx_style:line` の色が代わりに使用されます。  |
| `gpxx:RouteExtension`    | 部分的 | no      | すべてのプロパティが `properties` に読み込まれます。 `DisplayColor` のみが使用されます。                     |
| `gpxx:TrackExtension`    | 部分的 | no      | すべてのプロパティが `properties` に読み込まれます。 `DisplayColor` のみが使用されます。                     |
| `gpxx:WaypointExtension` | 部分的 | no      | すべてのプロパティが `properties` に読み込まれます。 `DisplayColor` のみが使用されます。                     |
| `gpx:keywords`           | はい     | はい     |                                                                                             |
| `gpx:fix`                | はい     | はい     |                                                                                             |

#### <a name="additional-notes"></a>追加情報

書き込み時に、

- MultiPoint が個々のウェイポイントに分割されます。
- Polygon と MultiPolygon がトラックとして書き込まれます。 
  
## <a name="supported-well-known-text-geometry-types"></a>サポートされている Well-Known Text の geometry 型

| geometry 型 | Read | Write |
|--------------|:----:|:-----:|
| POINT | x | x |
| POINT Z | x | x | 
| POINT M | x | x<sup>[2]</sup> |
| POINT ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| LINESTRING | x | x |
| LINESTRING Z | x | x | 
| LINESTRING M | x | x<sup>[2]</sup> |
| LINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| POLYGON | x | x |
| POLYGON Z | x | x |
| POLYGON M | x | x<sup>[2]</sup> |
| POLYGON ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTIPOINT | x | x |
| MULTIPOINT Z | x | x | 
| MULTIPOINT M | x | x<sup>[2]</sup> |
| POMULTIPOINTINT ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTILINESTRING | x | x |
| MULTILINESTRING Z | x | x | 
| MULTILINESTRING M | x | x<sup>[2]</sup> |
| MULTILINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTIPOLYGON | x | x |
| MULTIPOLYGON Z | x | x | 
| MULTIPOLYGON M | x | x<sup>[2]</sup> |
| MULTIPOLYGON ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| GEOMETRYCOLLECTION | x | x |
| GEOMETRYCOLLECTION Z | x | x | 
| GEOMETRYCOLLECTION M | x | x<sup>[2]</sup> | 
| GEOMETRYCOLLECTION ZM | x<sup>[1]</sup><sup>[2]</sup> | x | 

\[1\] Z パラメーターのみがキャプチャされ、Position 値の 3 番目の値として追加されます。

\[2\] M パラメーターはキャプチャされません。

## <a name="delimited-spatial-data-support"></a>区切られた空間データのサポート

コンマ区切り値 (CSV) ファイルなどの区切られた空間データには、多くの場合、空間データを含む列があります。 たとえば、緯度と経度の情報を含む列がある場合があります。 Well-Known Text 形式では、空間ジオメトリ データを含む列が存在する可能性があります。

### <a name="spatial-data-column-detection"></a>空間データ列の検出

空間データを含む区切りファイルを読み取るときに、ヘッダーが分析され、場所フィールドが含まれている列が特定されます。 ヘッダーに型情報が含まれている場合は、セルの値を適切な型にキャストするために使用されます。 ヘッダーが指定されていない場合、最初の行が分析され、ヘッダーを生成するために使用されます。 最初の行を分析するときに、大文字と小文字を区別しない方法で、列名を次の名前と照合するためのチェックが実行されます。 ファイルに 2 つ以上の名前が存在する場合は、名前の順序が優先順位となります。

#### <a name="latitude"></a>Latitude

- `latitude`
- `lat`
- `latdd`
- `lat_dd`
- `latitude83`
- `latdecdeg`
- `y`
- `ycenter`
- `point-y`

#### <a name="longitude"></a>Longitude

- `longitude`
- `lon`
- `lng`
- `long`
- `longdd`
- `long_dd`
- `longitude83`
- `longdecdeg`
- `x`
- `xcenter`
- `point-x`

#### <a name="elevation"></a>Elevation

- `elevation`
- `elv`
- `altitude`
- `alt`
- `z`

#### <a name="geography"></a>[地理的な場所]

データの最初の行は、Well-Known Text 形式の文字列のためにスキャンされます。 

### <a name="delimited-data-column-types"></a>区切られたデータ列の型

ヘッダー行をスキャンすると、列名に含まれるすべての型情報が抽出され、その列のセルをキャストするために使用されます。 型の値を持つ列名"ColumnName (typeName)" の例を次に示します。 次の型名がサポートされています (大文字と小文字は区別されません)。

#### <a name="numbers"></a>数値

- edm.int64
- INT
- long
- edm.double
- float
- double
- number

#### <a name="booleans"></a>ブール値

- edm.boolean
- [bool]
- boolean

#### <a name="dates"></a>日付

- edm.datetime
- date
- DATETIME

#### <a name="geography"></a>[地理的な場所]

- edm.geography
- geography

#### <a name="strings"></a>文字列

- edm.string
- varchar
- text
- string

ヘッダーから型情報を抽出できず、読み取り時に動的な入力オプションが有効になっている場合は、各セルが個別に分析され、キャストに最適なデータ型が特定されます。

## <a name="next-steps"></a>次のステップ

マップに追加できる他のコード サンプルについては、次の記事をご覧ください。

> [!div class="nextstepaction"]
> [空間データの読み取りと書き込み](spatial-io-read-write-spatial-data.md)
