---
title: Android マップにシンボル レイヤーを追加する | Microsoft Azure Maps
description: マップにマーカーを追加する方法について説明します。 データ ソースのポイントベースのデータを含むシンボル レイヤーを、Azure Maps Android SDK を使用して追加する例を参照してください。
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: edb758469a06dcb7914025ea449b9d952e939533
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097212"
---
# <a name="add-a-symbol-layer-android-sdk"></a>シンボル レイヤーを追加する (Android SDK)

この記事では、Azure Maps Android SDK を使用して、マップ上にデータ ソースからのポイント データをシンボル レイヤーとしてレンダリングする方法を示します。 シンボル レイヤーは、ポイントを画像とテキストとしてマップ上にレンダリングします。

> [!TIP]
> シンボル レイヤーでは、既定ではデータ ソース内のすべてのジオメトリの座標がレンダリングされます。 ポイント ジオメトリ フィーチャーのみがレンダリングされるようにレイヤーを制限するには、レイヤーの `filter` オプションを `eq(geometryType(), "Point")` に設定します。 MultiPoint フィーチャーも含める場合は、レイヤーの `filter` オプションを `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` に設定します。

## <a name="prerequisites"></a>前提条件

[クイックスタート: Android アプリの作成](quick-android-map.md)に関する記事の手順を必ず完了してください。 この記事のコード ブロックは、マップ `onReady` イベント ハンドラーに挿入できます。

## <a name="add-a-symbol-layer"></a>シンボル レイヤーを追加する

マップにシンボル レイヤーを追加する前に、いくつかの手順を行う必要があります。 まず、データ ソースを作成してマップに追加します。 シンボル レイヤーを追加します。 次に、データ ソースからデータを取得するために、データ ソースをシンボル レイヤーに渡します。 最後に、データをデータ ソースに追加して、レンダリングするものが存在するようにします。

次のコードは、マップが読み込まれた後に、そこに追加する必要がある内容を示しています。 このサンプルでは、シンボル レイヤーを使用してマップ上に 1 つのポイントがレンダリングされます。

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point and add it to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source);

//Add the layer to the map.
map.layers.add(layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a point and add it to the data source.
source.add(Point.fromLngLat(0, 0))

//Create a symbol layer to render icons and/or text at points on the map.
val layer = SymbolLayer(source)

//Add the layer to the map.
map.layers.add(layer)
```

::: zone-end

マップに追加できるポイント データには、次の 3 種類があります。

- GeoJSON Point ジオメトリ - このオブジェクトには、ポイントの座標だけが含まれ、それ以外は含まれません。 `Point.fromLngLat` 静的メソッドを使用すると、これらのオブジェクトを簡単に作成できます。
- GeoJSON MultiPoint ジオメトリ - このオブジェクトには、複数のポイントの座標が含まれ、それ以外は含まれません。 ポイントの配列を `MultiPoint` クラスに渡して、これらのオブジェクトを作成します。
- GeoJSON 機能 - このオブジェクトは、すべての GeoJSON ジオメトリと、ジオメトリに関連するメタデータを含むプロパティのセットで構成されます。

データの作成とマップへの追加の詳細については、「[データ ソースを作成する](create-data-source-android-sdk.md)」のドキュメントをご覧ください。

次のコード サンプルは、GeoJSON Point ジオメトリを作成して GeoJSON フィーチャーに渡し、`title` 値をそのプロパティに追加させます。 `title` プロパティは、マップ上のシンボル アイコンの上にテキストとして表示されます。

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(0, 0));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source, 
    //Get the title property of the feature and display it on the map.
    textField(get("title"))
);

//Add the layer to the map.
map.layers.add(layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(0, 0))

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!")

//Add the feature to the data source.
source.add(feature)

//Create a symbol layer to render icons and/or text at points on the map.
val layer = SymbolLayer(
    source,  //Get the title property of the feature and display it on the map.
    textField(get("title"))
)

//Add the layer to the map.
map.layers.add(layer)
```

::: zone-end

次のスクリーンショットでは、上記のコードにより、シンボル レイヤーでアイコンとテキスト ラベルを使ってポイント フィーチャーがレンダリングされた状態を示しています。

![ポイント フィーチャーのアイコンとテキスト ラベルを表示したシンボル レイヤーを使用してポイントがレンダリングされたマップ](media/how-to-add-symbol-to-android-map/android-map-pin.png)

> [!TIP]
> 既定では、重なっているシンボルがシンボル レイヤーによって非表示になることで、シンボルのレンダリングが最適化されます。 拡大すると、非表示のシンボルが表示されるようになります。 この機能を無効にして、すべてのシンボルを常にレンダリングするには、`iconAllowOverlap` および `textAllowOverlap` オプションを `true` に設定します。

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>シンボル レイヤーにカスタム アイコンを追加する

シンボル レイヤーは WebGL を使用してレンダリングされます。 このため、アイコンの画像などのすべてのリソースを WebGL コンテキストに読み込む必要があります。 このサンプルでは、マップ リソースにカスタム アイコンを追加する方法を示します。 このアイコンを使用して、ポイント データをカスタム シンボルでマップ上にレンダリングします。 シンボル レイヤーの `textField` プロパティに式を指定する必要があります。 ここでは、気温プロパティをレンダリングします。 気温は数値であるため、文字列に変換する必要があります。 加えて、そこに "°F" を追加したいと思います。 `concat(Expression.toString(get("temperature")), literal("°F"))` という式を使えば、この連結を実行できます。

::: zone pivot="programming-language-java-android"

```java
//Load a custom icon image into the image sprite of the map.
map.images.add("my-custom-icon", R.drawable.showers);

//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-73.985708, 40.75773));

//Add a property to the feature.
feature.addNumberProperty("temperature", 64);

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source,
    iconImage("my-custom-icon"),
    iconSize(0.5f),

    //Get the title property of the feature and display it on the map.
    textField(concat(Expression.toString(get("temperature")), literal("°F"))),
    textOffset(new Float[]{0f, -1.5f})
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Load a custom icon image into the image sprite of the map.
map.images.add("my-custom-icon", R.drawable.showers)

//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-73.985708, 40.75773))

//Add a property to the feature.
feature.addNumberProperty("temperature", 64)

//Add the feature to the data source.
source.add(feature)

//Create a symbol layer to render icons and/or text at points on the map.
val layer = SymbolLayer(
    source,
    iconImage("my-custom-icon"),
    iconSize(0.5f),  //Get the title property of the feature and display it on the map.
    textField(concat(Expression.toString(get("temperature")), literal("°F"))),
    textOffset(arrayOf(0f, -1.5f))
)
```

::: zone-end

このサンプルでは、次の画像がアプリのドローアブル フォルダーに読み込まれています。

| ![にわか雨の天気アイコンの画像](media/how-to-add-symbol-to-android-map/showers.png)|
|:-----------------------------------------------------------------------:|
| showers.png                                                  |

次のスクリーンショットでは、上記のコードにより、シンボル レイヤーでカスタム アイコンと書式設定されたテキスト ラベルを使ってポイント フィーチャーがレンダリングされた状態を示しています。

![ポイント フィーチャーのカスタム アイコンと書式設定されたテキスト ラベルを表示したシンボル レイヤーを使用してポイントがレンダリングされたマップ](media/how-to-add-symbol-to-android-map/android-custom-symbol-layer.png)

> [!TIP]
> シンボル レイヤーを使用してテキストをレンダリングするだけの場合は、アイコンのオプションの `iconImage` プロパティを `"none"` に設定することでアイコンを非表示にすることができます。

## <a name="modify-symbol-colors"></a>シンボルの色を変更する

Azure Maps Android SDK には、既定のマーカー アイコンの定義済みの色のバリエーション セットが付属しています。 たとえば、`marker-red` をシンボル レイヤーの `iconImage` オプションに渡して、そのレイヤーにマーカー アイコンの赤色のバージョンを表示することができます。

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
    iconImage("marker-red")
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    iconImage("marker-red")
)
```

::: zone-end

次の表は、使用可能なすべての組み込みのアイコン画像名を示しています。 これらのマーカーはすべて、オーバーライド可能な色リソースからその色をプルします。 さらに、このマーカーのメイン塗りつぶしの色をオーバーライドします。 ただし、これらのマーカーのいずれかの色をオーバーライドすると、そのアイコン画像を使用するすべてのレイヤーにも適用されることに注意してください。

| アイコン画像名 | 色リソース名 |
|-----------------|---------------------|
| `marker-default` | `mapcontrol_marker_default` |
| `marker-black` | `mapcontrol_marker_black` |
| `marker-blue` | `mapcontrol_marker_blue` |
| `marker-darkblue` | `mapcontrol_marker_darkblue` |
| `marker-red` | `mapcontrol_marker_red` |
| `marker-yellow` | `mapcontrol_marker_yellow` |

また、`mapcontrol_marker_border` 色リソース名を使用して、すべてのマーカーの境界線の色をオーバーライドすることもできます。 これらのマーカーの色は、アプリの `colors.xml` ファイルに同じ名前の色を追加することでオーバーライドできます。 たとえば、次の `colors.xml` ファイルの場合、既定のマーカーの色が明るい緑になります。

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="mapcontrol_marker_default">#00FF00</color>
</resources>
```

既定のマーカー ベクター XML を変更した後のバージョンを次に示します。この XML を変更すると、既定のマーカーの追加のカスタム バージョンを作成できます。 変更したバージョンを、アプリの `drawable` フォルダーに追加したり、`map.images.add` を使用してマップ イメージ スプライトに追加したりして、シンボル レイヤーで使うことができます。

```xml
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:width="24.5dp"
    android:height="36.5dp"
    android:viewportWidth="24.5"
    android:viewportHeight="36.5">
    <path
        android:pathData="M12.25,0.25a12.2543,12.2543 0,0 0,-12 12.4937c0,6.4436 6.4879,12.1093 11.059,22.5641 0.5493,1.2563 1.3327,1.2563 1.882,0C17.7621,24.8529 24.25,19.1857 24.25,12.7437A12.2543,12.2543 0,0 0,12.25 0.25Z"
        android:strokeWidth="0.5"
        android:fillColor="@color/mapcontrol_marker_default"
        android:strokeColor="@color/mapcontrol_marker_border"/>
</vector>
```

## <a name="next-steps"></a>次のステップ

マップに追加できる他のコード サンプルについては、次の記事をご覧ください。

> [!div class="nextstepaction"]
> [データ ソースを作成する](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [バブル レイヤーを追加する](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [データドリブンのスタイルの式を使用する](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [フィーチャーの情報を表示する](display-feature-information-android.md)
