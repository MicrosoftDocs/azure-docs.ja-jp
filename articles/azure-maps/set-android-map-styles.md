---
title: Android マップでマップ スタイルを設定する | Microsoft Azure Maps
description: マップのスタイルを設定する 2 つの方法について説明します。 スタイルを調整するため、レイアウト ファイルまたはアクティビティ クラスで Azure Maps Android SDK を使用する方法について説明します。
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: aef8fbacf8302fb5dd4b5fe28afc615c6bf56090
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102100986"
---
# <a name="set-map-style-android-sdk"></a>マップ スタイルを設定する (Android SDK)

この記事では、Azure Maps Android SDK を使用してマップ スタイルを設定する 2 つの方法を示します。 Azure Maps は、6 つの異なるマップ スタイルから選択できます。 サポートされているマップ スタイルの詳細については、「[Azure Maps でのサポートされているマップ スタイル](supported-map-styles.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

必ず、[クイック スタート:Android アプリの作成](quick-android-map.md)に関するドキュメントの手順を完了してください。

## <a name="set-map-style-in-the-layout"></a>レイアウトでのマップ スタイルの設定

マップ スタイルは、マップ コントロールの追加時に、アクティビティ クラスのレイアウト ファイルで設定できます。 次のコードは、中央の場所、ズーム レベル、およびマップ スタイルを設定します。

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/mapcontrol"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_centerLat="47.602806"
    app:mapcontrol_centerLng="-122.329330"
    app:mapcontrol_zoom="12"
    app:mapcontrol_style="grayscale_dark"
    />
```

次のスクリーンショットは、上記のコードで、グレースケールの濃色スタイルを使用した道路地図を示しています。

![グレースケールの濃い道路地図のスタイルを使用したマップ](media/set-android-map-styles/android-grayscale-dark.png)

## <a name="set-map-style-in-code"></a>コードでマップ スタイルを設定する

マップ スタイルは、マップの `setStyle` メソッドを使用して、プログラムによってコード内で設定できます。 次のコードでは、マップ `setCamera` メソッドを使用して中心位置とズーム レベルを設定し、マップ スタイルを `SATELLITE_ROAD_LABELS` に設定しています。

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {

    //Set the camera of the map.
    map.setCamera(center(Point.fromLngLat(-122.33, 47.64)), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE_ROAD_LABELS));
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    //Set the camera of the map.
    map.setCamera(center(Point.fromLngLat(-122.33, 47.64)), zoom(14))

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE_ROAD_LABELS))
}
```

::: zone-end

次のスクリーンショットは、上記のコードで、衛星からの道路ラベル スタイルを使用したマップを示しています。

![衛星からの道路ラベル スタイルのマップ](media/set-android-map-styles/android-satellite-road-labels.png)

## <a name="setting-the-map-camera"></a>マップ カメラの設定

マップ カメラは、マップのどの部分がマップ内に表示されるかを制御します。 カメラは、コード内でプログラムを使用してレイアウト内に配置できます。 コードで設定する場合は、マップの位置を設定する主な方法として、中心とズームを使用する方法と境界ボックスを渡す方法の 2 つがあります。 次のコードは、`center` と `zoom` を使用する場合に、省略可能なすべてのカメラ オプションを設定する方法を示しています。

::: zone pivot="programming-language-java-android"

```java
//Set the camera of the map using center and zoom.
map.setCamera(
    center(Point.fromLngLat(-122.33, 47.64)), 

    //The zoom level. Typically a value between 0 and 22.
    zoom(14),

    //The amount of tilt in degrees the map where 0 is looking straight down.
    pitch(45),

    //Direction the top of the map is pointing in degrees. 0 = North, 90 = East, 180 = South, 270 = West
    bearing(90),

    //The minimum zoom level the map will zoom-out to when animating from one location to another on the map.
    minZoom(10),
    
    //The maximium zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Set the camera of the map using center and zoom.
map.setCamera(
    center(Point.fromLngLat(-122.33, 47.64)), 

    //The zoom level. Typically a value between 0 and 22.
    zoom(14),

    //The amount of tilt in degrees the map where 0 is looking straight down.
    pitch(45),

    //Direction the top of the map is pointing in degrees. 0 = North, 90 = East, 180 = South, 270 = West
    bearing(90),

    //The minimum zoom level the map will zoom-out to when animating from one location to another on the map.
    minZoom(10),
    
    //The maximium zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
)
```

::: zone-end

多くの場合、一連のデータに対してマップにフォーカスすることをお勧めします。 `MapMath.fromData` メソッドを使用して特徴から境界ボックスを計算でき、その境界ボックスをマップ カメラの `bounds` オプションに渡すことができます。 境界ボックスに基づいてマップ ビューを設定するときには、バブルまたは記号としてレンダリングされているポイントのピクセル サイズに相当する `padding` 値を指定すると多くの場合役に立ちます。 次のコードは、境界ボックスを使用してカメラの位置を設定するときに、省略可能なすべてのカメラ オプションを設定する方法を示しています。

::: zone pivot="programming-language-java-android"

```java
//Set the camera of the map using a bounding box.
map.setCamera(
    //The area to focus the map on.
    bounds(BoundingBox.fromLngLats(
        //West
        -122.4594,

        //South
        47.4333,
        
        //East
        -122.21866,
        
        //North
        47.75758
    )),

    //Amount of pixel buffer around the bounding box to provide extra space around the bounding box.
    padding(20),

    //The maximium zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Set the camera of the map using a bounding box.
map.setCamera(
    //The area to focus the map on.
    bounds(BoundingBox.fromLngLats(
        //West
        -122.4594,

        //South
        47.4333,
        
        //East
        -122.21866,
        
        //North
        47.75758
    )),

    //Amount of pixel buffer around the bounding box to provide extra space around the bounding box.
    padding(20),

    //The maximium zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
)
```

::: zone-end

境界ボックスの縦横比はマップの縦横比とは異なる場合があることに注意してください。そのため、マップには、多くの場合、境界ボックス領域全体が表示されますが、垂直方向と水平方向の一方のみがいっぱいになっていることがよくあります。

## <a name="next-steps"></a>次のステップ

マップに追加できる他のコード サンプルについては、次の記事をご覧ください。

> [!div class="nextstepaction"]
> [シンボル レイヤーを追加する](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [バブル レイヤーを追加する](map-add-bubble-layer-android.md)
