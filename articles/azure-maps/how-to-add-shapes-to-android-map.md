---
title: Azure Maps での Android マップへの図形の追加 |Microsoft Docs
description: Azure Maps Android SDK を使用してマップに図形を追加する方法
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: c8e3f16b939d05a4c055fba3a9f1ab5e47e54070
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976592"
---
# <a name="add-a-shape-to-a-map-using-azure-maps-android-sdk"></a>Azure Maps Android SDK を使用したマップへの図形の追加

この記事では、Azure Maps Android SDK を使用してマップに図形をレンダリングする方法を示します。

## <a name="prerequisites"></a>前提条件

この記事のプロセスを完了するには、[Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) をインストールしてマップを読み込む必要があります。


## <a name="add-a-line-to-the-map"></a>マップへの線の追加

**線レイヤー**を使用してマップに線を追加できます。マップ上に線を追加するには、次の手順に従います。

1. **res > layout > activity_main.xml** を編集すると、次のようになります。

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
            app:mapcontrol_centerLat="40.743270"
            app:mapcontrol_centerLng="-74.004420"
            app:mapcontrol_zoom="12"
            />
    
    </FrameLayout>
    ```

2. 次のコード スニペットを `MainActivity.java` クラスの **onCreate()** メソッドにコピーします。

    ```Java
    mapControl.onReady(map -> {

        //Create a data source and add it to the map.
        DataSource dataSource = new DataSource();
        map.sources.add(dataSource);
    
        //Create a list of points.
        List<Point> points = Arrays.asList(
            Point.fromLngLat(-73.972340, 40.743270),
            Point.fromLngLat(-74.004420, 40.756800));
    
        //Create a LineString feature and add it to the data source.
        dataSource.add(LineString.fromLngLats(points));
    
        //Create a line layer and add it to the map.
        map.layers.add(new LineLayer(dataSource,
            strokeColor("blue"),
            strokeWidth(5f)));
    });

    ```
    
    上記のコード スニペットは、まず **onReady()** コールバック メソッドを使用して Azure Maps コントロール インスタンスを取得します。 次にこれは **DataSource** クラスを使用してデータ ソース オブジェクトを作成し、それをマップに追加します。 次にこれは**ポイント** オブジェクトの一覧を作成します。 **LineString** がポイントの一覧から作成され、データ ソースに追加されます。 **線レイヤー**は、マップ上にデータ ソースでラップされた線オブジェクトをレンダリングします。 次に線レイヤーが作成され、データ ソースがそれに追加されます。

    上記のコード スニペットを追加したら、`MainActivity.java` は次のようになります。
    
    ```Java
    package com.example.myapplication;

    import android.app.Activity;
    import android.os.Bundle;
    import com.mapbox.geojson.LineString;
    import com.mapbox.geojson.Point;
    import android.support.v7.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.layer.LineLayer;
    import com.microsoft.azure.maps.mapcontrol.options.LineLayerOptions;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;
    import java.util.Arrays;
    import java.util.List;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import static com.microsoft.azure.maps.mapcontrol.options.LineLayerOptions.strokeColor;
    import static com.microsoft.azure.maps.mapcontrol.options.LineLayerOptions.strokeWidth;
        
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
            
                //Create a list of points.
                List<Point> points = Arrays.asList(
                    Point.fromLngLat(-73.972340, 40.743270),
                    Point.fromLngLat(-74.004420, 40.756800));
            
                //Create a LineString feature and add it to the data source.
                dataSource.add(LineString.fromLngLats(points));
            
                //Create a line layer and add it to the map.
                map.layers.add(new LineLayer(dataSource,
                    strokeColor("blue"),
                    strokeWidth(5f)));
            });    
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

これでアプリケーションを実行すると、次に示すようにマップ上に線が表示されます。

<center>

![Android マップの線](./media/how-to-add-shapes-to-android-map/android-map-line.png)</center>


## <a name="add-a-polygon-to-the-map"></a>マップに多角形を追加する

**多角形レイヤー**では、マップに多角形の領域をレンダリングできます。 マップ上に多角形を追加するには、次の手順に従います。

1. **res > layout > activity_main.xml** を編集すると、次のようになります。

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
            app:mapcontrol_centerLat="40.78"
            app:mapcontrol_centerLng="-73.97"
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
    
        //Create a list of point arrays to create polygon rings.
        List<List<Point>> rings = Arrays.asList(Arrays.asList(
            Point.fromLngLat(-73.98235, 40.76799),
            Point.fromLngLat(-73.95785, 40.80044),
            Point.fromLngLat(-73.94928, 40.7968),
            Point.fromLngLat(-73.97317, 40.76437),
            Point.fromLngLat(-73.98235, 40.76799)));
    
        //Create a Polygon feature and add it to the data source.
        dataSource.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));
    
        //Add a polygon layer for rendering the polygon area.
        map.layers.add(new PolygonLayer(dataSource,
            fillColor("red")));
    
        //Add a line layer for rendering the polygon outline.
        map.layers.add(new LineLayer(dataSource,
            strokeColor("blue"),
            strokeWidth(2f)));
    });
    ```

    上記のコード スニペットは、まず **onReady()** コールバック メソッドを使用して Azure Maps コントロール インスタンスを取得します。 次にこれは **DataSource** クラスを使用してデータ ソース オブジェクトを作成し、それをマップに追加します。 **多角形**オブジェクトが**ポイント** オブジェクトの一覧から作成され、データ ソースに追加されます。 **多角形レイヤー**は、マップ上にデータ ソースでラップされたデータをレンダリングします。 次に多角形レイヤーを作成して、多角形領域をレンダリングし、それにデータ ソースを追加します。 **線レイヤー**は、データ ソースにラップされた線オブジェクトをレンダリングします。 コード スニペットの最後の部分では、多角形のアウトラインをレンダリングするための線レイヤーが作成され、それにデータ ソースが追加されます。

    上記のコード スニペットを追加したら、`MainActivity.java` は次のようになります。

    ```Java
    package com.example.myapplication;
    
    import android.app.Activity;
    import android.os.Bundle;
    import java.util.Arrays;
    import android.util.Log;
    import java.util.Collections;
    import android.support.v7.app.AppCompatActivity;
    import com.mapbox.geojson.Point;
    import com.mapbox.geojson.Polygon;
    import com.mapbox.geojson.Feature;
    import com.microsoft.azure.maps.mapcontrol.layer.LineLayer;
    import com.microsoft.azure.maps.mapcontrol.options.LineLayerOptions;
    import com.microsoft.azure.maps.mapcontrol.layer.PolygonLayer;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import static com.microsoft.azure.maps.mapcontrol.options.LineLayerOptions.strokeColor;
    import static com.microsoft.azure.maps.mapcontrol.options.LineLayerOptions.strokeWidth;
    import static com.microsoft.azure.maps.mapcontrol.options.PolygonLayerOptions.fillColor;
    
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
            
                //Create a list of point arrays to create polygon rings.
                List<List<Point>> rings = Arrays.asList(Arrays.asList(
                    Point.fromLngLat(-73.98235, 40.76799),
                    Point.fromLngLat(-73.95785, 40.80044),
                    Point.fromLngLat(-73.94928, 40.7968),
                    Point.fromLngLat(-73.97317, 40.76437),
                    Point.fromLngLat(-73.98235, 40.76799)));
            
                //Create a Polygon feature and add it to the data source.
                dataSource.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));
            
                //Add a polygon layer for rendering the polygon area.
                map.layers.add(new PolygonLayer(dataSource,
                    fillColor("red")));
            
                //Add a line layer for rendering the polygon outline.
                map.layers.add(new LineLayer(dataSource,
                    strokeColor("blue"),
                    strokeWidth(2f)));
            });    
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

これでアプリケーションを実行すると、次に示すようにマップ上に多角形が表示されます。

<center>

![Android マップの多角形](./media/how-to-add-shapes-to-android-map/android-map-polygon.png)</center>


## <a name="next-steps"></a>次の手順

マップにさらにデータを追加するには:

> [!div class="nextstepaction"]
> [シンボル レイヤーを追加する](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [タイル レイヤーを追加する](how-to-add-tile-layer-android-map.md)

> [!div class="nextstepaction"]
> [フィーチャーの情報を表示する](display-feature-information-android.md)