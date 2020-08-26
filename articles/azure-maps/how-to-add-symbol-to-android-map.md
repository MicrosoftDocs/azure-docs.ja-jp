---
title: Android マップにシンボル レイヤーを追加する | Microsoft Azure Maps
description: マップにマーカーを追加する方法について説明します。 データ ソースのポイントベースのデータを含むシンボル レイヤーを、Azure Maps Android SDK を使用して追加する例を参照してください。
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 99a4f69a814314b233a3129ed31d18ffa1682d8d
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037390"
---
# <a name="add-a-symbol-layer-to-a-map-using-azure-maps-android-sdk"></a>Azure Maps Android SDK を使用してマップにシンボル レイヤーを追加する方法

この記事では、Azure Maps Android SDK を使用して、マップ上にデータ ソースからのポイント データをシンボル レイヤーとしてレンダリングする方法を示します。

## <a name="prerequisites"></a>前提条件

この記事の手順に完全に従うには、[Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) をインストールしてマップを読み込む必要があります。

## <a name="add-a-symbol-layer"></a>シンボル レイヤーを追加する

シンボル レイヤーを使用してマップ上にマーカーを追加するには、次の手順に従います。

1. **res** > **layout** > **activity_main.xml** を編集すると、次のような XML になります。
    
    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <FrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >

        <com.microsoft.azure.maps.mapcontrol.MapControl
            android:id="@+id/mapcontrol"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:mapcontrol_centerLat="47.64"
            app:mapcontrol_centerLng="-122.33"
            app:mapcontrol_zoom="12"
            />

    </FrameLayout>
    ```

2. 次のコード スニペットを **onCreate()** method of your `MainActivity.java` クラスにコピーします。

    ```Java
    mapControl.onReady(map -> {
    
        //Create a data source and add it to the map.
        DataSource dataSource = new DataSource();
        map.sources.add(dataSource);
    
        //Create a point feature and add it to the data source.
        dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
    
        //Add a custom image icon to the map resources.
        map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
    
        //Create a symbol layer and add it to the map.
        map.layers.add(new SymbolLayer(dataSource,
            iconImage("my-icon")));
        });
    
    ```
    
    上記のコード スニペットは、まず **onReady()** コールバック メソッドを使用して Azure Maps コントロール インスタンスを取得します。 次にこれは **DataSource** クラスを使用してデータ ソース オブジェクトを作成し、それをマップに追加します。 次にこれは、ポイント ジオメトリを含む**図形**を追加します。 シンボルのアイコンとして赤いマーカー イメージが設定されます。 **シンボル レイヤー**は、テキストまたはアイコンを使用して、マップ上のシンボルとしてデータ ソースにラップされたポイント ベースのデータをレンダリングします。 次にシンボル レイヤーが作成され、それにレンダリングするためにデータ ソースが渡され、マップのレイヤーに追加されます。
    
    上記のコード スニペットを追加したら、`MainActivity.java` は次のようになります。
    
    ```Java
    package com.example.myapplication;
    
    import android.app.Activity;
    import android.os.Bundle;
    import com.mapbox.geojson.Feature;
    import com.mapbox.geojson.Point;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;
    import static com.microsoft.azure.maps.mapcontrol.options.SymbolLayerOptions.iconImage;
    public class MainActivity extends AppCompatActivity {
        
        static{
                AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
            }
    
        MapControl mapControl;
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapControl = findViewById(R.id.mapcontrol);
    
            mapControl.onCreate(savedInstanceState);
    
            mapControl.onReady(map -> {
    
                //Create a data source and add it to the map.
                DataSource dataSource = new DataSource();
                map.sources.add(dataSource);
            
                //Create a point feature and add it to the data source.
                dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
            
                //Add a custom image icon to the map resources.
                map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
            
                //Create a symbol layer and add it to the map.
                map.layers.add(new SymbolLayer(dataSource,
                    iconImage("my-icon")));
            });
        }
    
        @Override
        public void onStart() {
            super.onStart();
            mapControl.onStart();
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapControl.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapControl.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapControl.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapControl.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapControl.onSaveInstanceState(outState);
        }
    }
    ```
    
この時点では、アプリケーションを実行すると、ここに示すように、マップ上にマーカーが表示されます。

<center>

![Android マップのピン](./media/how-to-add-symbol-to-android-map/android-map-pin.png)</center>

> [!TIP]
> 既定では、重なっているシンボルがシンボル レイヤーによって非表示になることで、シンボルのレンダリングが最適化されます。 拡大すると、非表示のシンボルが表示されるようになります。 この機能を無効にして、すべてのシンボルを常にレンダリングするには、`iconAllowOverlap` オプションを `true` に設定します。

## <a name="next-steps"></a>次のステップ

さらに内容をマップに追加するには、次を参照してください。

> [!div class="nextstepaction"]
> [Android マップへの図形の追加](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [フィーチャーの情報を表示する](display-feature-information-android.md)