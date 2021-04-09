---
title: Android マップでマップ イベントを処理する | Microsoft Azure Maps
description: ユーザーがマップとやりとりするときに発生するイベントについて学習します。 サポートされているすべてのマップ イベントの一覧を表示します。 Azure Maps Android SDK を使用してイベントを処理する方法について説明します。
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 86d1b9ec8a507a5cfaa5502efcb239bceabca665
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097348"
---
# <a name="interact-with-the-map-android-sdk"></a>マップを操作する (Android SDK)

この記事では、マップ イベント マネージャーを使用する方法について説明します。

## <a name="interact-with-the-map"></a>マップの操作

マップでは、`events` プロパティを使用してすべてのイベントが管理されます。 次の表に、サポートされているすべてのマップ イベントを一覧表示します。

| Event                  | イベント ハンドラーの形式 | [説明] |
|------------------------|----------------------|-------------|
| `OnCameraIdle`         | `()`                 | <p>マップが "アイドル" 状態になる前にレンダリングされた最後のフレームの後に発生します。<ul><li>カメラの切り替えが進行中ではありません。</li><li>現在要求されているすべてのタイルが読み込まれました。</li><li>すべてのフェード/切り替えアニメーションが完了しました。</li></ul></p> |
| `OnCameraMove`         | `()`                 | ユーザーの操作またはメソッドの結果として、ビュー間でのアニメーション化された遷移中に繰り返し発生します。 |
| `OnCameraMoveCanceled` | `()`                 | カメラに対する移動要求が取り消されたときに発生します。 |
| `OnCameraMoveStarted`  | `(int reason)`       | ユーザーの操作またはメソッドの結果として、マップでビュー間の遷移が開始される直前に発生します。 イベント リスナーの `reason` 引数は、カメラの移動がどのように開始されたかに関する詳細を示す整数値を返します。 考えられる理由を次のリストに示します。<ul><li>1: ジェスチャ</li><li>2: 開発者のアニメーション</li><li>3: API アニメーション</li></ul>   |
| `OnClick`              | `(double lat, double lon)` | マップが、マップ上の同じ位置で押されて解放されたときに発生します。 |
| `OnFeatureClick`       | `(List<Feature>)`    | マップが、フィーチャー上の同じ位置で押されて解放されたときに発生します。  |
| `OnLayerAdded` | `(Layer layer)` | レイヤーがマップに追加されたときに発生します。 |
| `OnLayerRemoved` | `(Layer layer)` | レイヤーがマップから削除されたときに発生します。 |
| `OnLoaded` | `()` | 必要なリソースがすべてダウンロードされ、マップの最初の視覚的に完全なレンダリングが行われた直後に発生します。 |
| `OnLongClick`          | `(double lat, double lon)` | マップが、マップ上の同じ位置で押され、一瞬そのまま保持されてから、解放されたときに発生します。 |
| `OnLongFeatureClick `  | `(List<Feature>)`    | マップが、フィーチャー上の同じ位置で押され、一瞬そのまま保持されてから、解放されたときに発生します。 |
| `OnReady`              | `(AzureMap map)`     | マップが最初に読み込まれたとき、またはアプリの向きが変更されて、必要最小限のマップ リソースが読み込まれ、プログラムを使用してマップを操作できる状態になったときに発生します。 |
| `OnSourceAdded` | `(Source source)` | `DataSource` または `VectorTileSource` がマップに追加されたときに発生します。 |
| `OnSourceRemoved` | `(Source source)` | `DataSource` または `VectorTileSource` がマップから削除されたときに発生します。 |
| `OnStyleChange` | `()` | マップのスタイルが読み込まれたか、変更されたときに発生します。 |

次のコードは、`OnClick`、`OnFeatureClick`、および `OnCameraMove` の各イベントをマップに追加する方法を示しています。

::: zone pivot="programming-language-java-android"

```java
map.events.add((OnClick) (lat, lon) -> {
    //Map clicked.
});

map.events.add((OnFeatureClick) (features) -> {
    //Feature clicked.
});

map.events.add((OnCameraMove) () -> {
    //Map camera moved.
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.events.add(OnClick { lat: Double, lon: Double -> 
    //Map clicked.
})

map.events.add(OnFeatureClick { features: List<Feature?>? -> 
    //Feature clicked.
})

map.events.add(OnCameraMove {
    //Map camera moved.
})
```

::: zone-end

詳細については、「[マップ内を移動する](how-to-use-android-map-control-library.md#navigating-the-map)」ドキュメントを参照して、マップおよびトリガー イベントの操作方法を確認してください。

## <a name="scope-feature-events-to-layer"></a>フィーチャー イベントのスコープをレイヤーに設定する

`OnFeatureClick` または `OnLongFeatureClick` の各イベントをマップに追加する場合、レイヤー インスタンスまたはレイヤー ID を 2 番目のパラメーターとして渡すことができます。 レイヤー ID が渡されると、イベントは、そのレイヤーでイベントが発生した場合にのみ発生します。 レイヤーにスコープ設定されたイベントは、シンボル、バブル、線、および多角形の各レイヤーでサポートされています。

::: zone pivot="programming-language-java-android"

```java
//Create a data source.
DataSource source = new DataSource();
map.sources.add(source);

//Add data to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a layer and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);

//Add a feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnFeatureClick) (features) -> {
    //One or more features clicked.
}, layer);

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnLongFeatureClick) (features) -> {
    //One or more features long clicked.
}, layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source.
val source = DataSource()
map.sources.add(source)

//Add data to the data source.
source.add(Point.fromLngLat(0, 0))

//Create a layer and add it to the map.
val layer = BubbleLayer(source)
map.layers.add(layer)

//Add a feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add(
    OnFeatureClick { features: List<Feature?>? -> 
        //One or more features clicked.
    },
    layer
)

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add(
    OnLongFeatureClick { features: List<Feature?>? -> 
         //One or more features long clicked.
    },
    layer
)
```

::: zone-end

## <a name="next-steps"></a>次のステップ

完全なコードの例については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [マップ内を移動する](how-to-use-android-map-control-library.md#navigating-the-map)

> [!div class="nextstepaction"]
> [シンボル レイヤーを追加する](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [バブル レイヤーを追加する](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [線レイヤーを追加する](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [多角形レイヤーを追加する](how-to-add-shapes-to-android-map.md)
