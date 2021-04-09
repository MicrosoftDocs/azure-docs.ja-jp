---
title: Android マップにイメージ レイヤーを追加する | Microsoft Azure Maps
description: マップに画像を追加する方法について説明します。 Azure Maps Android SDK を使用し、固定の座標セット上でイメージ レイヤーやオーバーレイ画像をカスタマイズする方法について説明します。
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: e1d99297c0357039606149bdf7e5a526258fc7c5
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054142"
---
# <a name="add-an-image-layer-to-a-map-android-sdk"></a>マップにイメージ レイヤーを追加する (Android SDK)

この記事では、固定された一連の座標に画像をオーバーレイする方法について説明します。 以下、マップでオーバーレイできるさまざまな種類の画像のうち、いくつか例を挙げます。

* ドローンからキャプチャされた画像
* ビルのフロアプラン
* 歴史などの目的に特化したマップ画像
* 仕事の現場のブループリント

> [!TIP]
> イメージ レイヤーは、マップに画像をオーバーレイする簡単な方法です。 大きな画像の場合、大量のメモリを消費し、パフォーマンスの問題を引き起こすおそれがあることにご注意ください。 その場合は、お客様の画像をタイルに分割したうえで、タイル レイヤーとしてマップに読み込むことを検討してください。

## <a name="add-an-image-layer"></a>イメージ レイヤーを追加する

次のコードでは、[1922 年のニュージャージー州ニューアークの地図](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg)の画像がマップにオーバーレイされています。 このイメージはプロジェクトの `drawable` フォルダーに追加されます。 イメージ レイヤーは、形式 `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]` で四隅に画像と座標を設定することで作成されます。 多くの場合、`label` レイヤーの下にイメージ レイヤーを追加することが推奨されます。

::: zone pivot="programming-language-java-android"

```java
//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(
        new Position[] {
            new Position(-74.22655, 40.773941), //Top Left Corner
            new Position(-74.12544, 40.773941), //Top Right Corner
            new Position(-74.12544, 40.712216), //Bottom Right Corner
            new Position(-74.22655, 40.712216)  //Bottom Left Corner
        }
    ),
    setImage(R.drawable.newark_nj_1922)
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(
        arrayOf<Position>(
            Position(-74.22655, 40.773941),  //Top Left Corner
            Position(-74.12544, 40.773941),  //Top Right Corner
            Position(-74.12544, 40.712216),  //Bottom Right Corner
            Position(-74.22655, 40.712216)   //Bottom Left Corner
        )
    ),
    setImage(R.drawable.newark_nj_1922)
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

あるいは、オンライン上でホストされている画像の URL を指定できます。 ただし、行動計画上許されるなら、プロジェクトの `drawable` フォルダーに画像を追加してください。画像がローカルで利用可能になり、ダウンロードする必要がなくなるため、読み込みが速くなります。

::: zone pivot="programming-language-java-android"

```java
//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(
        new Position[] {
            new Position(-74.22655, 40.773941), //Top Left Corner
            new Position(-74.12544, 40.773941), //Top Right Corner
            new Position(-74.12544, 40.712216), //Bottom Right Corner
            new Position(-74.22655, 40.712216)  //Bottom Left Corner
        }
    ),
    setUrl("https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg")
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(
        arrayOf<Position>(
            Position(-74.22655, 40.773941),  //Top Left Corner
            Position(-74.12544, 40.773941),  //Top Right Corner
            Position(-74.12544, 40.712216),  //Bottom Right Corner
            Position(-74.22655, 40.712216) //Bottom Left Corner
        )
    ),
    setUrl("https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg")
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

次のスクリーンショットからは、ニュージャージー州ニューアークの 1922 年の地図を確認できます。イメージ レイヤーでオーバーレイされています。

![ニュージャージー州ニューアークの 1922 年の地図。イメージ レイヤーでオーバーレイされています](media/map-add-image-layer-android/android-image-layer.gif)

## <a name="import-a-kml-file-as-ground-overlay"></a>KML ファイルをグラウンド オーバーレイとしてインポートする

このサンプルからは、KML グラウンド オーバーレイ情報を画像レイヤーとしてマップに追加する方法がわかります。 KML グランド オーバーレイからは、東西南北の座標が反時計回りで与えられます。 しかしながら、画像レイヤーでは、画像の四隅の座標が求められます。 このサンプルの KML グラウンド オーバーレイはシャルトル大聖堂のものであり、その出典は [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml) です。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<kml xmlns="http://www.opengis.net/kml/2.2" xmlns:gx="http://www.google.com/kml/ext/2.2" xmlns:kml="http://www.opengis.net/kml/2.2" xmlns:atom="http://www.w3.org/2005/Atom">
<GroundOverlay>
    <name>Map of Chartres cathedral</name>
    <Icon>
        <href>https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png</href>
        <viewBoundScale>0.75</viewBoundScale>
    </Icon>
    <LatLonBox>
        <north>48.44820923628113</north>
        <south>48.44737203258976</south>
        <east>1.488833825534365</east>
        <west>1.486788581643038</west>
        <rotation>46.44067597839695</rotation>
    </LatLonBox>
</GroundOverlay>
</kml>
```

このコードでは、`ImageLayer` クラスの静的 `getCoordinatesFromEdges` メソッドが使用されています。 このメソッドでは、KML グラウンド オーバーレイの東西南北情報と回転情報を利用し、画像の四隅が計算されます。

::: zone pivot="programming-language-java-android"

```java
//Calculate the corner coordinates of the ground overlay.
Position[] corners = ImageLayer.getCoordinatesFromEdges(
    //North, south, east, west
    48.44820923628113, 48.44737203258976, 1.488833825534365, 1.486788581643038,

    //KML rotations are counter-clockwise, subtract from 360 to make them clockwise.
    360 - 46.44067597839695
);

//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(corners),
    setUrl("https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png")
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Calculate the corner coordinates of the ground overlay.
val corners: Array<Position> =
    ImageLayer.getCoordinatesFromEdges( //North, south, east, west
        48.44820923628113,
        48.44737203258976,
        1.488833825534365,
        1.486788581643038,  //KML rotations are counter-clockwise, subtract from 360 to make them clockwise.
        360 - 46.44067597839695
    )

//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(corners),
    setUrl("https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png")
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

次のスクリーンショットからは、イメージ レイヤーでオーバーレイされた KML グラウンド オーバーレイの地図を確認できます。

![KML グラウンド オーバーレイの地図。イメージ レイヤーでオーバーレイされています](media/map-add-image-layer-android/android-ground-overlay.jpg)

> [!TIP]
> イメージ レイヤー クラスの `getPixels` メソッドと `getPositions` メソッドを使用することで、配置されたイメージ レイヤーの地理的座標とローカル画像のピクセル座標の間で変換を行います。

## <a name="next-steps"></a>次のステップ

地図上で画像をオーバーレイする各種方法の詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [タイル レイヤーを追加する](how-to-add-tile-layer-android-map.md)