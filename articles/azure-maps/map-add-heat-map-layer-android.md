---
title: Android マップにヒート マップ レイヤーを追加する | Microsoft Azure Maps
description: ヒート マップを作成する方法について説明します。 Azure MapsAndroid SDK を使用してヒート マップ レイヤーをマップに追加する方法について説明します。 ヒート マップ レイヤーをカスタマイズする方法について説明します。
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: fce2c2d007f92c43e763826f9345f773324e885e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102100187"
---
# <a name="add-a-heat-map-layer-android-sdk"></a>ヒート マップ レイヤーを追加する (Android SDK)

点密度マップとも呼ばれるヒート マップは、データ視覚化の 1 つの種類です。 一定の範囲の色を使用して、データの密度を表すために使用され、マップ上にデータの "ホット スポット" を示します。 ヒート マップは、多数の点によってデータセットをレンダリングする優れた方法です。 

数万個の点を記号としてレンダリングすると、マップのほとんどの領域が覆われてしまう場合があります。 この場合、多くの記号が重なり合う傾向にあります。 より適正にデータを理解することは、難しくなります。 ただし、これと同じデータセットをヒート マップとして視覚化すると、データの各点の密度と相対密度を簡単に把握できます。

ヒート マップは、次のようなさまざまなシナリオで使用できます。

- **気温データ**: 2 つのデータ ポイント間の気温の近似値が提供されます。
- **ノイズ センサーのデータ**: センサーがある場所のノイズの強度が示されるだけでなく、離れた場所での散逸に対する分析情報も提供できます。 個々の場所のノイズ レベルは高くないかもしれません。 複数のセンサーのノイズ感知領域が重なる場合は、この重なる領域においてノイズ レベルがより高くなる可能性があります。 そのため、ヒート マップ上では重なった領域が可視化されます。
- **GPS トレース**: 速度を、重み付けされたハイト マップとして含めます。ここで、各データ ポイントの強度は速度に基づいています。 たとえば、この機能により、車両のスピードが速められた場所を確認する方法が提供されます。

> [!TIP]
> ヒート マップ レイヤーでは、既定ではデータ ソース内のすべてのジオメトリの座標がレンダリングされます。 ポイント ジオメトリ フィーチャーのみがレンダリングされるようにレイヤーを制限するには、レイヤーの `filter` オプションを `eq(geometryType(), "Point")` に設定します。 MultiPoint フィーチャーも含める場合は、レイヤーの `filter` オプションを `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` に設定します。

</br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player?format=ny]

## <a name="prerequisites"></a>前提条件

[クイックスタート: Android アプリの作成](quick-android-map.md)に関する記事の手順を必ず完了してください。 この記事のコード ブロックは、マップの `onReady` イベント ハンドラーに挿入できます。

## <a name="add-a-heat-map-layer"></a>ヒート マップ レイヤーを追加する

ポイントのデータ ソースをヒート マップとしてレンダリングするには、ご自分のデータ ソースを `HeatMapLayer` クラスのインスタンスに渡し、それをマップに追加します。

次のコード サンプルでは、過去 1 週間の地震の GeoJSON フィードを読み込んで、ヒート マップとしてレンダリングします。 各データ ポイントは、すべてのズーム レベルで 10 ピクセルの半径でレンダリングされます。 ユーザー エクスペリエンスを向上させるため、ラベルがはっきりと見えるように、ヒート マップはラベル レイヤーの下にあります。 このサンプルのデータは、[USGS Earthquake Hazards Program (USGS 地震ハザード プログラム) ](https://earthquake.usgs.gov/)から引用しています。 このサンプルでは、[データ ソースの作成](create-data-source-android-sdk.md)に関するドキュメントで提供されているデータ インポート ユーティリティのコード ブロックを使用して、Web から GeoJSON データを読み込みます。

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a heat map layer.
HeatMapLayer layer = new HeatMapLayer(source,
  heatmapRadius(10f),
  heatmapOpacity(0.8f)
);

//Add the layer to the map, below the labels.
map.layers.add(layer, "labels");

//Import the geojson data and add it to the data source.
Utils.importData("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        source.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a heat map layer.
val layer = HeatMapLayer(
    source,
    heatmapRadius(10f),
    heatmapOpacity(0.8f)
)

//Add the layer to the map, below the labels.
map.layers.add(layer, "labels")

//Import the geojson data and add it to the data source.
Utils.importData("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson",
    this
) { result: String? ->
    //Parse the data as a GeoJSON Feature Collection.
    val fc = FeatureCollection.fromJson(result!!)

    //Add the feature collection to the data source.
    source.add(fc)

    //Optionally, update the maps camera to focus in on the data.
    //Calculate the bounding box of all the data in the Feature Collection.
    val bbox = MapMath.fromData(fc)

    //Update the maps camera so it is focused on the data.
    map.setCamera(
        bounds(bbox),
        padding(20)
    )
}
```

::: zone-end

次のスクリーンショットは、上のコードを使用してヒート マップが読み込まれているマップを示しています。

![最近の地震のヒート マップ レイヤーが含まれたマップ](media/map-add-heat-map-layer-android/android-heat-map-layer.png)

## <a name="customize-the-heat-map-layer"></a>ヒート マップ レイヤーをカスタマイズする

前の例では、半径と不透明度のオプションを設定してヒート マップをカスタマイズしました。 ヒート マップ レイヤーには、次のようなカスタマイズのためのオプションがいくつか用意されています。

- `heatmapRadius`:各データ ポイントをレンダリングするときのピクセルの半径を定義します。 この半径は、固定数または式として設定できます。 式を使用することにより、ズーム レベルに基づいて半径をスケーリングし、マップ上の一貫した空間領域 (たとえば、5 マイルの半径) を表すことができます。
- `heatmapColor`:ヒート マップの配色方法を指定します。 色のグラデーションは、ヒート マップの一般的な機能です。 `interpolate` 式を利用して、その効果を実現できます。 また、`step` 式を使用してヒート マップを色分けし、密度を、等高線またはレーダー スタイル マップのような範囲に視覚的に分割することもできます。 このようなカラー パレットでは、最小濃度値から最大濃度値の色を定義します。

  ヒート マップの色の値は、`heatmapDensity` 値に対する式として指定します。 "補間" 式のインデックス 0 または "段階" 式の既定の色では、データのない領域の色が定義されます。 この値を使用して、背景色を定義することができます。 多くの場合、この値は透明、または半透明の黒に設定されます。 

  色の式の例を次に示します。

  | 補間色の式 | 段階的な色の式 |
  |--------------------------------|--------------------------|
  | interpolate(<br/>&nbsp;&nbsp;&nbsp;&nbsp;linear(), <br/>&nbsp;&nbsp;&nbsp;&nbsp;heatmapDensity(),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0, color(Color.TRANSPARENT)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0.01, color(Color.MAGENTA)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0.5, color(parseColor("#fb00fb"))),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(1, color(parseColor("#00c3ff")))<br/>)` | step(<br/>&nbsp;&nbsp;&nbsp;&nbsp;heatmapDensity(),<br/>&nbsp;&nbsp;&nbsp;&nbsp;color(Color.TRANSPARENT),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0.01, color(parseColor("#000080"))),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0.25, color(parseColor("#000080"))),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0.5, color(Color.GREEN)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0.5, color(Color.YELLOW)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(1, color(Color.RED))<br/>) |

- `heatmapOpacity`:ヒート マップ レイヤーの不透明度または透明度を指定します。
- `heatmapIntensity`:ヒート マップの全体的な強度を高めるために、各データ ポイントの重みに乗数を適用します。 これにより、データ ポイントの重みの違いが示され、視覚化しやすくなります。
- `heatmapWeight`:既定では、データ ポイントはすべて、重みが 1 となり、均等に重み付けされています。 weight オプションは乗数として機能し、数値または式として設定できます。 数値を重みとして設定した場合、各データ ポイントをマップ上に 2 回配置することに相当します。 たとえば、重みが `2` の場合、密度は 2 倍になります。 weight オプションを数値に設定すると、intensity オプションを使用する場合と同様にヒート マップがレンダリングされます。

  ただし、式を使用する場合は、各データ ポイントの重みを、各データ ポイントのプロパティに基づいて決めることができます。 たとえば、各データ ポイントが地震を表しているとします。 マグニチュード値は、各地震データ ポイントの重要なメトリックです。 地震は常に発生していますが、多くはマグニチュードが低く、体感することはありません。 各データ ポイントに重みを割り当てるには、式にマグニチュード値を使用します。 マグニチュード値を使用して重みを割り当てると、地震の深刻さをヒート マップ内でよりわかりやすく表すことができます。
- `minZoom` および `maxZoom`:レイヤーを表示するズーム レベルの範囲。
- `filter`:ソースからの取得とレイヤーでのレンダリングを制限するために使用するフィルター式。
- `sourceLayer`:レイヤーに接続されているデータ ソースがベクター タイルのソースである場合は、ベクター タイル内のソース レイヤーを指定する必要があります。
- `visible`:レイヤーを表示または非表示にします。

次に示すのは、滑らかな色のグラデーションを作成するために線形補間式が使用されているヒート マップの例です。 データに定義されている `mag` プロパティは、各データ ポイントの重みまたは関連性を設定するために指数補間と共に使用されます。

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapRadius(10f),

    //A linear interpolation is used to create a smooth color gradient based on the heat map density.
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.BLACK)),
            stop(0.25, color(Color.MAGENTA)),
            stop(0.5, color(Color.RED)),
            stop(0.75, color(Color.YELLOW)),
            stop(1, color(Color.WHITE))
        )
    ),

    //Using an exponential interpolation since earthquake magnitudes are on an exponential scale.
    heatmapWeight(
       interpolate(
            exponential(2),
            get("mag"),
            stop(0,0),

            //Any earthquake above a magnitude of 6 will have a weight of 1
            stop(6, 1)
       )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapRadius(10f),

    //A linear interpolation is used to create a smooth color gradient based on the heat map density.
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.BLACK)),
            stop(0.25, color(Color.MAGENTA)),
            stop(0.5, color(Color.RED)),
            stop(0.75, color(Color.YELLOW)),
            stop(1, color(Color.WHITE))
        )
    ),

    //Using an exponential interpolation since earthquake magnitudes are on an exponential scale.
    heatmapWeight(
       interpolate(
            exponential(2),
            get("mag"),
            stop(0,0),

            //Any earthquake above a magnitude of 6 will have a weight of 1
            stop(6, 1)
       )
    )
)
```

::: zone-end

次のスクリーンショットは、前のヒート マップ例と同じデータを使用して、上記のカスタム ヒート マップ レイヤーを示しています。

![最近の地震のカスタム ヒート マップ レイヤーが含まれたマップ](media/map-add-heat-map-layer-android/android-custom-heat-map-layer.png)

## <a name="consistent-zoomable-heat-map"></a>一貫性のあるズーム可能なヒート マップ

既定では、ヒート マップ レイヤーにレンダリングされるデータ ポイントの半径には、すべてのズーム レベルの固定ピクセル半径が含まれます。 マップをズームすると、データがまとめて集計され、ヒート マップ レイヤーの外観が変化します。 次のビデオは、マップをズームするときにピクセル半径が維持されるヒート マップの既定の動作を示しています。

![一貫したピクセル サイズが示されるヒート マップ レイヤーでのマップのズームを示すアニメーション](media/map-add-heat-map-layer-android/android-heat-map-layer-zoom.gif)

`zoom` 式を使用すると、各データ ポイントによってマップの同じ物理領域がカバーされるように、各ズーム レベルの半径をスケーリングできます。 この式により、ヒート マップ レイヤーの外観は、より静的かつ一貫性の高いものになります。 マップの各ズーム レベルは、垂直方向および水平方向のピクセル数がすぐ下のズーム レベルの 2 倍になっています。

ズーム レベルごとに半径が 2 倍になるようにスケーリングすると、すべてのズーム レベルで外観に一貫性のあるヒート マップが作成されます。 このスケーリングを適用するには、次の例に示すように、最小ズーム レベルに設定されたピクセル半径と、最大ズーム レベルでスケーリングされた半径が `2 * Math.pow(2, minZoom - maxZoom)` として計算された、基数 2 の `exponential interpolation` 式で `zoom` を使用します。 マップをズームして、ヒート マップがズーム レベルに応じてどのように変化するかを確認してください。

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
  heatmapRadius(
    interpolate(
      exponential(2),
      zoom(),

      //For zoom level 1 set the radius to 2 pixels.
      stop(1, 2f),

      //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
      stop(19, Math.pow(2, 19 - 1) * 2f)
    )
  ),
  heatmapOpacity(0.75f)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
  heatmapRadius(
    interpolate(
      exponential(2),
      zoom(),

      //For zoom level 1 set the radius to 2 pixels.
      stop(1, 2f),

      //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
      stop(19, Math.pow(2.0, 19 - 1.0) * 2f)
    )
  ),
  heatmapOpacity(0.75f)
)
```

::: zone-end

次のビデオでは、上記のコードが実行されているマップを示します。これは、マップをズームしている間に半径を拡大縮小して、ズーム レベル全体で一貫性のあるヒート マップ レンダリングを作成します。

![一貫した地理空間サイズが示されるヒート マップ レイヤーでのマップのズームを示すアニメーション](media/map-add-heat-map-layer-android/android-consistent-zoomable-heat-map-layer.gif)

## <a name="next-steps"></a>次のステップ

マップに追加するコード例の詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [データ ソースを作成する](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [データドリブンのスタイルの式を使用する](data-driven-style-expressions-android-sdk.md)
