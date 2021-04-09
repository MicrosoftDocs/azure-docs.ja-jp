---
title: Android マップにコントロールを追加する | Microsoft Azure Maps
description: Microsoft Azure Maps Android SDK でズーム コントロール、ピッチ コントロール、回転コントロール、およびスタイルの選択ツールをマップに追加する方法。
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 90d037fc02bdc1c4d6fe682386790561c890c1e6
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100221"
---
# <a name="add-controls-to-a-map-android-sdk"></a>マップにコントロールを追加する (Android SDK)

この記事では、マップに UI を追加する方法について説明します。

## <a name="add-zoom-control"></a>ズーム コントロールを追加する

ズーム コントロールは、マップを拡大または縮小するためのボタンを追加します。次のサンプル コードは、`ZoomControl` クラスのインスタンスを作成してマップに追加します。

::: zone pivot="programming-language-java-android"

```java
//Construct a zoom control and add it to the map.
map.controls.add(new ZoomControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a zoom control and add it to the map.
map.controls.add(ZoomControl())
```

::: zone-end

次のスクリーンショットは、マップに読み込まれたズーム コントロールです。

![マップに追加されたズーム コントロール](media/map-add-controls-android/android-zoom-control.jpg)

## <a name="add-pitch-control"></a>ピッチ コントロールを追加する

ピッチ コントロールでは、水平線を基準としてマップを傾けるためのボタンを追加します。 次のサンプル コードは、`PitchControl` クラスのインスタンスを作成してマップに追加します。

::: zone pivot="programming-language-java-android"

```java
//Construct a pitch control and add it to the map.
map.controls.add(new PitchControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a pitch control and add it to the map.
map.controls.add(PitchControl())
```

::: zone-end

次のスクリーンショットは、マップに読み込まれたピッチ コントロールです。

![マップに追加されたピッチ コントロール](media/map-add-controls-android/android-pitch-control.jpg)

## <a name="add-compass-control"></a>コンパス コントロールを追加する

コンパス コントロールは、マップを回転するためのボタンを追加します。 次のサンプル コードは、`CompassControl` クラスのインスタンスを作成してマップに追加します。

::: zone pivot="programming-language-java-android"

```java
//Construct a compass control and add it to the map.
map.controls.add(new CompassControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a compass control and add it to the map.
map.controls.add(CompassControl())
```

::: zone-end

次のスクリーンショットは、マップに読み込まれたコンパス コントロールです。

![マップに追加されたコンパス コントロール](media/map-add-controls-android/android-compass-control.jpg)

## <a name="add-traffic-control"></a>トラフィック コントロールを追加する

トラフィック コントロールによって、マップ上のトラフィック データの可視性を切り替えるボタンが追加されます。 次のサンプル コードは、`TrafficControl` クラスのインスタンスを作成してマップに追加します。

::: zone pivot="programming-language-java-android"

```java
//Construct a traffic control and add it to the map.
map.controls.add(new TrafficControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a traffic control and add it to the map.
map.controls.add(TrafficControl())
```

::: zone-end

次のスクリーンショットは、マップに読み込まれたトラフィック コントロールです。

![マップに追加されたトラフィック コントロール](media/map-add-controls-android/android-traffic-control.jpg)

## <a name="a-map-with-all-controls"></a>すべてのコントロールを含むマップ

開発を簡略化するため、複数のコントロールを 1 つの配列に格納し、すべてを一度にマップに追加して、マップの同じ領域に配置することができます。 以下ではこの方法を使用して、標準のナビゲーション コントロールをマップに追加しています。

::: zone pivot="programming-language-java-android"

```java
map.controls.add(
    new Control[]{
        new ZoomControl(),
        new CompassControl(),
        new PitchControl(),
        new TrafficControl()
    }
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.controls.add(
    arrayOf<Control>(
        ZoomControl(),
        CompassControl(),
        PitchControl(),
        TrafficControl()
    )
)
```

::: zone-end

次のスクリーンショットは、マップに読み込まれたすべてのコントロールを示しています。 マップに追加された順序が表示される順序になることに注意してください。

![マップに追加されたすべてのコントロール](media/map-add-controls-android/android-all-controls.jpg)

## <a name="next-steps"></a>次のステップ

マップに追加できる他のコード サンプルについては、次の記事をご覧ください。

> [!div class="nextstepaction"]
> [シンボル レイヤーを追加する](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [バブル レイヤーを追加する](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [線レイヤーを追加する](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [多角形レイヤーを追加する](how-to-add-shapes-to-android-map.md)
