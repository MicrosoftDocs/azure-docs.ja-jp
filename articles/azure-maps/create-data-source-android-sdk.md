---
title: Android マップのデータ ソースを作成する | Microsoft Azure Maps
description: マップのデータ ソースを作成する方法について説明します。 Azure Maps Android SDK で使用されるデータ ソースについて説明します。GeoJSON ソースとベクター タイル。
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: e870134e2ecd431aa3e5c02638120027f0d47df2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102101462"
---
# <a name="create-a-data-source-android-sdk"></a>データ ソースを作成する (Android SDK)

Azure Maps Android SDK によって、データ ソースにデータが格納されます。 データ ソースを使用すると、クエリとレンダリングのデータ操作が最適化されます。 現在、次の 2 種類のデータ ソースがあります。

- **GeoJSON ソース**: 場所の生データを GeoJSON 形式でローカルに管理します。 小規模から中規模のデータ セット (数十万点を超える図形) に適しています。
- **ベクター タイル ソース**: マップのタイル システムに基づいて、現在のマップ ビューのベクター タイルとして書式設定されたデータを読み込みます。 大規模から大規模なデータセット (数百万または数十億点の図形) に最適です。

## <a name="geojson-data-source"></a>GeoJSON データ ソース

Azure Maps では、プライマリ データ モデルの 1 つとして GeoJSON が使用されます。 GeoJSON は、地理空間データを JSON 形式で表現するためのオープンな地理空間標準方法です。 Azure Maps Android SDK で使用できる GeoJSON クラスによって、GeoJSON データを簡単に作成してシリアル化できます。 `DataSource` クラスに GeoJSON データを読み込んで格納し、レイヤーを使用してレンダリングします。 次のコードは、Azure Maps で GeoJSON オブジェクトを作成する方法を示しています。

::: zone pivot="programming-language-java-android"

```java
/*
    Raw GeoJSON feature
    
    {
         "type": "Feature",
         "geometry": {
             "type": "Point",
             "coordinates": [-100, 45]
         },
         "properties": {
             "custom-property": "value"
         }
    }

*/

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45));

//Add a property to the feature.
feature.addStringProperty("custom-property", "value");

//Add the feature to the data source.
source.add(feature);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
/*
    Raw GeoJSON feature
    
    {
         "type": "Feature",
         "geometry": {
             "type": "Point",
             "coordinates": [-100, 45]
         },
         "properties": {
             "custom-property": "value"
         }
    }

*/

//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-100, 45))

//Add a property to the feature.
feature.addStringProperty("custom-property", "value")

//Add the feature to the data source.
source.add(feature)
```

::: zone-end

または、次に示すように、プロパティを JsonObject に先に読み込んでから、作成時にフィーチャーに渡すことができます。

::: zone pivot="programming-language-java-android"

```java
//Create a JsonObject to store properties for the feature.
JsonObject properties = new JsonObject();
properties.addProperty("custom-property", "value");

Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45), properties);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a JsonObject to store properties for the feature.
val properties = JsonObject()
properties.addProperty("custom-property", "value")

val feature = Feature.fromGeometry(Point.fromLngLat(-100, 45), properties)
```

::: zone-end

GeoJSON フィーチャーを作成したら、マップの `sources` プロパティを使用して、マップにデータ ソースを追加できます。 次のコードは、`DataSource` を作成し、マップに追加し、フィーチャーをデータ ソースに追加する方法を示しています。

```javascript
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Add GeoJSON feature to the data source.
source.add(feature);
```

次のコードは、GeoJSON フィーチャー、FeatureCollection、およびジオメトリを作成するためのさまざまな方法を示しています。

::: zone pivot="programming-language-java-android"

```java
//GeoJSON Point Geometry
Point point = Point.fromLngLat(LONGITUDE, LATITUDE);

//GeoJSON Point Geometry
LineString linestring = LineString.fromLngLats(PointList);

//GeoJSON Polygon Geometry
Polygon polygon = Polygon.fromLngLats(listOfPointList);
 
Polygon polygonFromOuterInner = Polygon.fromOuterInner(outerLineStringObject,innerLineStringObject);

//GeoJSON MultiPoint Geometry
MultiPoint multiPoint = MultiPoint.fromLngLats(PointList);

//GeoJSON MultiLineString Geometry
MultiLineString multiLineStringFromLngLat = MultiLineString.fromLngLats(listOfPointList);

MultiLineString multiLineString = MultiLineString.fromLineString(singleLineString);

//GeoJSON MultiPolygon Geometry
MultiPolygon multiPolygon = MultiPolygon.fromLngLats(listOflistOfPointList);

MultiPolygon multiPolygonFromPolygon = MultiPolygon.fromPolygon(polygon);

MultiPolygon multiPolygonFromPolygons = MultiPolygon.fromPolygons(PolygonList);

//GeoJSON Feature
Feature pointFeature = Feature.fromGeometry(Point.fromLngLat(LONGITUDE, LATITUDE));
 
//GeoJSON FeatureCollection 
FeatureCollection featureCollectionFromSingleFeature = FeatureCollection.fromFeature(pointFeature);
 
FeatureCollection featureCollection = FeatureCollection.fromFeatures(listOfFeatures);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//GeoJSON Point Geometry
val point = Point.fromLngLat(LONGITUDE, LATITUDE)

//GeoJSON Point Geometry
val linestring = LineString.fromLngLats(PointList)

//GeoJSON Polygon Geometry
val polygon = Polygon.fromLngLats(listOfPointList)

val polygonFromOuterInner = Polygon.fromOuterInner(outerLineStringObject, innerLineStringObject)

//GeoJSON MultiPoint Geometry
val multiPoint = MultiPoint.fromLngLats(PointList)

//GeoJSON MultiLineString Geometry
val multiLineStringFromLngLat = MultiLineString.fromLngLats(listOfPointList)

val multiLineString = MultiLineString.fromLineString(singleLineString)

//GeoJSON MultiPolygon Geometry
val multiPolygon = MultiPolygon.fromLngLats(listOflistOfPointList)

val multiPolygonFromPolygon = MultiPolygon.fromPolygon(polygon)

val multiPolygonFromPolygons = MultiPolygon.fromPolygons(PolygonList)

//GeoJSON Feature
val pointFeature = Feature.fromGeometry(Point.fromLngLat(LONGITUDE, LATITUDE))

//GeoJSON FeatureCollection 
val featureCollectionFromSingleFeature = FeatureCollection.fromFeature(pointFeature)

val featureCollection = FeatureCollection.fromFeatures(listOfFeatures)
```

::: zone-end

### <a name="serialize-and-deserialize-geojson"></a>GeoJSON のシリアル化と逆シリアル化を行う

フィーチャー コレクション、フィーチャー、およびジオメトリ クラスにはすべてに、静的メソッドである `fromJson()` と `toJson()` があり、シリアル化に役立ちます。 `fromJson()` メソッドを通して渡される書式設定された有効な JSON 文字列によって、ジオメトリ オブジェクトが作成されます。 この `fromJson()` メソッドは、Gson またはその他のシリアル化/逆シリアル化戦略を使用できることも意味します。 次のコードは、文字列化された GeoJSON フィーチャーを取得し、それを Feature クラスに逆シリアル化した後、GeoJSON 文字列にシリアル化する方法を示しています。

::: zone pivot="programming-language-java-android"

```java
//Take a stringified GeoJSON object.
String GeoJSON_STRING = "{"
    + "      \"type\": \"Feature\","            
    + "      \"geometry\": {"
    + "            \"type\": \"Point\""
    + "            \"coordinates\": [-100, 45]"
    + "      },"
    + "      \"properties\": {"
    + "            \"custom-property\": \"value\""
    + "      },"
    + "}";

//Deserialize the JSON string into a feature.
Feature feature = Feature.fromJson(GeoJSON_STRING);
 
//Serialize a feature collection to a string.
String featureString = feature.toJson();
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Take a stringified GeoJSON object.
val GeoJSON_STRING = ("{"
        + "      \"type\": \"Feature\","
        + "      \"geometry\": {"
        + "            \"type\": \"Point\""
        + "            \"coordinates\": [-100, 45]"
        + "      },"
        + "      \"properties\": {"
        + "            \"custom-property\": \"value\""
        + "      },"
        + "}")

//Deserialize the JSON string into a feature.
val feature = Feature.fromJson(GeoJSON_STRING)

//Serialize a feature collection to a string.
val featureString = feature.toJson()
```

::: zone-end

### <a name="import-geojson-data-from-web-or-assets-folder"></a>GeoJSON データを Web または assets フォルダーからインポートする

ほとんどの GeoJSON ファイルには、FeatureCollection が含まれています。 GeoJSON ファイルを文字列として読み取り、`FeatureCollection.fromJson` メソッドを使用して逆シリアル化します。

次のコードは、Web またはローカルの assets フォルダーからデータを文字列としてインポートし、コールバック関数を介して UI スレッドに返すための再利用可能なクラスです。

::: zone pivot="programming-language-java-android"

```java
import android.content.Context;
import android.os.Handler;
import android.os.Looper;
import android.webkit.URLUtil;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.URL;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

import javax.net.ssl.HttpsURLConnection;

public class Utils {

    interface SimpleCallback {
        void notify(String result);
    }

    /**
     * Imports data from a web url or asset file name and returns it to a callback.
     * @param urlOrFileName A web url or asset file name that points to data to load.
     * @param context The context of the app.
     * @param callback The callback function to return the data to.
     */
    public static void importData(String urlOrFileName, Context context, SimpleCallback callback){
        importData(urlOrFileName, context, callback, null);
    }
    
    /**
     * Imports data from a web url or asset file name and returns it to a callback.
     * @param urlOrFileName A web url or asset file name that points to data to load.
     * @param context The context of the app.
     * @param callback The callback function to return the data to.
     * @param error A callback function to return errors to.
     */
    public static void importData(String urlOrFileName, Context context, SimpleCallback callback, SimpleCallback error){
        if(urlOrFileName != null && callback != null) {
            ExecutorService executor = Executors.newSingleThreadExecutor();
            Handler handler = new Handler(Looper.getMainLooper());

            executor.execute(() -> {
                String data = null;

                try {

                    if(URLUtil.isNetworkUrl(urlOrFileName)){
                        data = importFromWeb(urlOrFileName);
                    } else {
                        //Assume file is in assets folder.
                        data = importFromAssets(context, urlOrFileName);
                    }

                    final String result = data;

                    handler.post(() -> {
                        //Ensure the resulting data string is not null or empty.
                        if (result != null && !result.isEmpty()) {
                            callback.notify(result);
                        } else {
                            error.notify("No data imported.");
                        }
                    });
                } catch(Exception e) {
                    if(error != null){
                        error.notify(e.getMessage());
                    }
                }
            });
        }
    }

    /**
     * Imports data from an assets file as a string.
     * @param context The context of the app.
     * @param fileName The asset file name.
     * @return
     * @throws IOException
     */
    private static String importFromAssets(Context context, String fileName) throws IOException {
        InputStream stream = null;

        try {
            stream = context.getAssets().open(fileName);

            if(stream != null) {
                return readStreamAsString(stream);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // Close Stream and disconnect HTTPS connection.
            if (stream != null) {
                stream.close();
            }
        }

        return null;
    }

    /**
     * Imports data from the web as a string.
     * @param url URL to the data.
     * @return
     * @throws IOException
     */
    private static String importFromWeb(String url) throws IOException {
        InputStream stream = null;
        HttpsURLConnection connection = null;
        String result = null;

        try {
            connection = (HttpsURLConnection) new URL(url).openConnection();

            //For this use case, set HTTP method to GET.
            connection.setRequestMethod("GET");

            //Open communications link (network traffic occurs here).
            connection.connect();

            int responseCode = connection.getResponseCode();
            if (responseCode != HttpsURLConnection.HTTP_OK) {
                throw new IOException("HTTP error code: " + responseCode);
            }

            //Retrieve the response body as an InputStream.
            stream = connection.getInputStream();

            if (stream != null) {
                return readStreamAsString(stream);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // Close Stream and disconnect HTTPS connection.
            if (stream != null) {
                stream.close();
            }
            if (connection != null) {
                connection.disconnect();
            }
        }

        return result;
    }

    /**
     * Reads an input stream as a string.
     * @param stream Stream to convert.
     * @return
     * @throws IOException
     */
    private static String readStreamAsString(InputStream stream) throws IOException {
        //Convert the contents of an InputStream to a String.
        BufferedReader in = new BufferedReader(new InputStreamReader(stream, "UTF-8"));

        String inputLine;
        StringBuffer response = new StringBuffer();

        while ((inputLine = in.readLine()) != null) {
            response.append(inputLine);
        }

        in.close();

        return response.toString();
    }
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
import android.content.Context
import android.os.Handler
import android.os.Looper
import android.webkit.URLUtil
import java.net.URL
import java.util.concurrent.ExecutorService
import java.util.concurrent.Executors

class Utils {
    companion object {

        /**
            * Imports data from a web url or asset file name and returns it to a callback.
            * @param urlOrFileName A web url or asset file name that points to data to load.
            * @param context The context of the app.
            * @param callback The callback function to return the data to.
            */
        fun importData(urlOrFileName: String?, context: Context, callback: (String?) -> Unit) {
            importData(urlOrFileName, context, callback, null)
        }

        /**
            * Imports data from a web url or asset file name and returns it to a callback.
            * @param urlOrFileName A web url or asset file name that points to data to load.
            * @param context The context of the app.
            * @param callback The callback function to return the data to.
            * @param error A callback function to return errors to.
            */
        public fun importData(urlOrFileName: String?, context: Context, callback: (String?) -> Unit, error: ((String?) -> Unit)?) {
            if (urlOrFileName != null && callback != null) {
                val executor: ExecutorService = Executors.newSingleThreadExecutor()
                val handler = Handler(Looper.getMainLooper())
                executor.execute {
                    var data: String? = null
                    
                    try {
                        data = if (URLUtil.isNetworkUrl(urlOrFileName)) {
                            URL(urlOrFileName).readText()
                        } else { //Assume file is in assets folder.
                            context.assets.open(urlOrFileName).bufferedReader().use{
                                it.readText()
                            }
                        }

                        handler.post {
                            //Ensure the resulting data string is not null or empty.
                            if (data != null && !data.isEmpty()) {
                                callback(data)
                            } else {
                                error!!("No data imported.")
                            }
                        }
                    } catch (e: Exception) {
                        error!!(e.message)
                    }
                }
            }
        }
    }
}
```

::: zone-end

次のコードは、このユーティリティを使用して、GeoJSON データを文字列としてインポートし、コールバックを介して UI スレッドに返す方法を示しています。 コールバックでは、文字列データを GeoJSON フィーチャー コレクションにシリアル化してデータ ソースに追加できます。 必要に応じて、データに焦点を当てるようにマップのカメラを更新します。

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource dataSource = new DataSource();
map.sources.add(dataSource);

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        dataSource.add(fc);

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
DataSource source = new DataSource();
map.sources.add(source);

//Import the GeoJSON data and add it to the data source.
Utils.importData("SamplePoiDataSet.json", this) { 
    result: String? ->
        //Parse the data as a GeoJSON Feature Collection.
            val fc = FeatureCollection.fromJson(result!!)

        //Add the feature collection to the data source.
        source.add(fc)

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        val bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),

            //Padding added to account for pixel size of rendered points.
            padding(20)
        )
    }
```

::: zone-end

## <a name="vector-tile-source"></a>ベクター タイル ソース

ベクター タイル ソースには、ベクター タイル レイヤーにアクセスする方法が記述されています。 ベクター タイル ソースをインスタンス化するには、`VectorTileSource` クラスを使用します。 ベクター タイル レイヤーはタイル レイヤーに似ていますが、同じではありません。 タイル レイヤーはラスター イメージです。 ベクター タイル レイヤーは、**PBF** 形式の圧縮ファイルです。 この圧縮ファイルには、ベクター マップ データと 1 つ以上のレイヤーが含まれています。 このファイルは、各レイヤーのスタイルに基づいて、クライアントでレンダリングおよびスタイル設定できます。 ベクター タイルのデータには、ポイント、線、および多角形の形式で地理的特徴が含まれています。 ベクター タイル レイヤーの使用には、ラスター タイル レイヤーよりも優れている点がいくつかあります。

- ベクター タイルのファイル サイズは、通常、同等のラスター タイルよりはるかに小さくなります。 そのため、使用される帯域幅が少なくなります。 つまり、待機時間の短縮、より高速なマップ、ユーザー エクスペリエンスの向上を意味します。
- ベクター タイルはクライアント上でレンダリングされるため、表示されているデバイスの解像度に適応できます。 結果として、レンダリングされるマップはより明確に定義され、鮮明なラベルが表示されます。
- ベクター マップ内のデータのスタイルを変更しても、クライアントに新しいスタイルを適用できるので、データを再度ダウンロードする必要はありません。 これに対し、ラスター タイル レイヤーのスタイルを変更するには、通常、タイルをサーバーから読み込み、新しいスタイルを適用する必要があります。
- データはベクター形式で配信されるので、データを準備するために必要なサーバー側の処理が少なくなります。 つまり、新しいデータをより速く使用できるようになります。

Azure Maps は、オープン スタンダードである [Mapbox Vector Tile 仕様](https://github.com/mapbox/vector-tile-spec)に準拠しています。 Azure Maps では、プラットフォームの一部として次のベクター タイル サービスが提供されます。

- Road tiles [ドキュメント](/rest/api/maps/renderv2/getmaptilepreview) | [データ形式の詳細](https://developer.tomtom.com/maps-api/maps-api-documentation-vector/tile)
- Traffic incidents [ドキュメント](/rest/api/maps/traffic/gettrafficincidenttile) | [データ形式の詳細](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles)
- Traffic flow [ドキュメント](/rest/api/maps/traffic/gettrafficflowtile) | [データ形式の詳細](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-flow/vector-flow-tiles)
- Azure Maps の作成者は、[Get Tile Render V2](/rest/api/maps/renderv2/getmaptilepreview) を使用して、カスタム ベクター タイルを作成およびアクセスすることもできます

> [!TIP]
> Web SDK を使用して Azure Maps の Render Service からベクターまたはラスター イメージのタイルを使用する場合、`atlas.microsoft.com` をプレースホルダー `azmapsdomain.invalid` に置き換えることができます。 このプレースホルダーは、マップによって使用されるのと同じドメインに置き換えられ、同じ認証の詳細も自動的に追加されます。 これにより、Azure Active Directory 認証を使用するときに、Render Service での認証が大幅に簡単になります。

マップにベクター タイル ソースからのデータを表示するには、ソースをいずれかのデータ レンダリング レイヤーに接続します。 ベクター ソースを使用するすべてのレイヤーでオプションの `sourceLayer` 値を指定する必要があります。 次のコードでは、Azure Maps のトラフィック フロー ベクター タイル サービスをベクター タイル ソースとして読み込み、その後、線レイヤーを使用してマップに表示します。 このベクター タイル ソースには、ソース レイヤーに "Traffic flow" という 1 つのデータ セットがあります。 このデータ セット内の行データには、`traffic_level` というプロパティがあります。これは、色を選択して線のサイズを拡大縮小するために、このコードで使用されます。

::: zone pivot="programming-language-java-android"

```java
//Formatted URL to the traffic flow vector tiles, with the maps subscription key appended to it.
String trafficFlowUrl = "https://azmapsdomain.invalid/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}";

//Create a vector tile source and add it to the map.
VectorTileSource source = new VectorTileSource(
    tiles(new String[] { trafficFlowUrl }),
    maxSourceZoom(22)
);
map.sources.add(source);

//Create a layer for traffic flow lines.
LineLayer layer = new LineLayer(source,
    //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer("Traffic flow"),

    //Color the roads based on the traffic_level property.
    strokeColor(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, color(Color.RED)),
            stop(0.33, color(Color.YELLOW)),
            stop(0.66, color(Color.GREEN))
        )
    ),

    //Scale the width of roads based on the traffic_level property.
    strokeWidth(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, 6),
            stop(1,1)
        )
    )
);

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Formatted URL to the traffic flow vector tiles, with the maps subscription key appended to it.
val trafficFlowUrl = "https://azmapsdomain.invalid/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}"

//Create a vector tile source and add it to the map.
val source = VectorTileSource(
    tiles(arrayOf(trafficFlowUrl)),
    maxSourceZoom(22)
)
map.sources.add(source)

//Create a layer for traffic flow lines.
val layer = LineLayer(
    source,  //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer("Traffic flow"),  //Color the roads based on the traffic_level property.
    strokeColor(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, color(Color.RED)),
            stop(0.33, color(Color.YELLOW)),
            stop(0.66, color(Color.GREEN))
        )
    ),  //Scale the width of roads based on the traffic_level property.
    strokeWidth(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, 6),
            stop(1, 1)
        )
    )
)

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(layer, "labels")
```

::: zone-end

![交通流のレベルを示す色分けされた道路を表示しているマップ](media/create-data-source-android-sdk/android-vector-tile-source-line-layer.png)

## <a name="connecting-a-data-source-to-a-layer"></a>レイヤーへのデータ ソースの接続

データは、レンダリング レイヤーを使用してマップ上にレンダリングされます。 1 つのデータ ソースは、1 つ以上のレンダリング レイヤーから参照できます。 次のレンダリング レイヤーでは、データ ソースが必要です。

- [バブル レイヤー](map-add-bubble-layer-android.md) - ポイント データをマップ上で拡大縮小された円としてレンダリングします。
- [シンボル レイヤー](how-to-add-symbol-to-android-map.md) - ポイント データをアイコンやテキストとしてレンダリングします。
- [ヒート マップ レイヤー](map-add-heat-map-layer-android.md) - ポイント データを密度ヒート マップとしてレンダリングします。
- [線レイヤー](android-map-add-line-layer.md) - 線をレンダリングしたり、多角形のアウトラインをレンダリングしたりします。
- [多角形レイヤー](how-to-add-shapes-to-android-map.md) - 純色または画像パターンで多角形の領域を塗りつぶします。

次のコードは、データ ソースを作成し、マップに追加してバブル レイヤーに接続する方法を示しています。 次に、GeoJSON ポイント データをリモートの場所からデータ ソースにインポートします。

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a layer that defines how to render points in the data source and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        dataSource.add(fc);

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

//Create a layer that defines how to render points in the data source and add it to the map.
val layer = BubbleLayer(source)
map.layers.add(layer)

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data", this) { 
    result: String? ->
        //Parse the data as a GeoJSON Feature Collection.
        val fc = FeatureCollection.fromJson(result!!)
    
        //Add the feature collection to the data source.
        dataSource.add(fc)
    
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

これらのデータ ソースに接続せず、レンダリングするためのデータを直接読み込む追加のレンダリング レイヤーがあります。

- [タイル レイヤー](how-to-add-tile-layer-android-map.md) - マップの上にラスター タイル レイヤーを重ねて表示します。

## <a name="one-data-source-with-multiple-layers"></a>複数のレイヤーがある 1 つのデータ ソース

複数のレイヤーを 1 つのデータ ソースに接続できます。 このオプションが役に立つさまざまなシナリオがあります。 たとえば、ユーザーが多角形を描画するシナリオを考えてみます。 ユーザーがポイントをマップに追加するときに、多角形領域をレンダリングして塗りつぶす必要があります。 多角形の輪郭を描画するスタイル指定された線を追加すると、ユーザーが描画するときに多角形のエッジが見やすくなります。 多角形内の個々の位置を簡単に編集するために、各位置の上にピンやマーカーなどのハンドルを追加することもできます。

![1 つのデータ ソースのデータをレンダリングする複数のレイヤーが表示されるマップ](media/create-data-source-android-sdk/multiple-layers-one-datasource.png)

ほとんどのマッピング プラットフォームで、多角形オブジェクト、線オブジェクト、および多角形内の各位置に対するピンが必要になります。 多角形を変更するときは、線とピンを手動で更新する必要がありますが、これはすぐに複雑になる可能性があります。

Azure Maps で必要なものは、次のコードで示すように、データ ソース内の 1 つの多角形だけです。

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a polygon and add it to the data source.
source.add(Polygon.fromLngLats(/* List of points */));

//Create a polygon layer to render the filled in area of the polygon.
PolygonLayer polygonLayer = new PolygonLayer(source,
    fillColor("rgba(255,165,0,0.2)")
);

//Create a line layer for greater control of rendering the outline of the polygon.
LineLayer lineLayer = new LineLayer(source,
    strokeColor("orange"),
    strokeWidth(2f)
);

//Create a bubble layer to render the vertices of the polygon as scaled circles.
BubbleLayer bubbleLayer = new BubbleLayer(source,
    bubbleColor("orange"),
    bubbleRadius(5f),
    bubbleStrokeColor("white"),
    bubbleStrokeWidth(2f)
);

//Add all layers to the map.
map.layers.add(new Layer[] { polygonLayer, lineLayer, bubbleLayer });
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a polygon and add it to the data source.
source.add(Polygon.fromLngLats())

//Create a polygon layer to render the filled in area of the polygon.
val polygonLayer = PolygonLayer(
    source,
    fillColor("rgba(255,165,0,0.2)")
)

//Create a line layer for greater control of rendering the outline of the polygon.
val lineLayer = LineLayer(
    source,
    strokeColor("orange"),
    strokeWidth(2f)
)

//Create a bubble layer to render the vertices of the polygon as scaled circles.
val bubbleLayer = BubbleLayer(
    source,
    bubbleColor("orange"),
    bubbleRadius(5f),
    bubbleStrokeColor("white"),
    bubbleStrokeWidth(2f)
)

//Add all layers to the map.
map.layers.add(arrayOf<Layer>(polygonLayer, lineLayer, bubbleLayer))
```

::: zone-end

> [!TIP]
> `map.layers.add` 関数を使用してマップにレイヤーを追加するときに、既存のレイヤーの ID またはインスタンスを 2 番目のパラメーターとして渡すことができます。 これにより、マップは、追加される新しいレイヤーを既存のレイヤーの下に挿入するように指示されます。 このメソッドは、レイヤー ID を渡すだけでなく、次の値もサポートします。
>
> - `"labels"` - マップ ラベル レイヤーの下に新しいレイヤーを挿入します。
> - `"transit"` - マップの道路および輸送のレイヤーの下に新しいレイヤーを挿入します。

## <a name="next-steps"></a>次のステップ

マップに追加できる他のコード サンプルについては、次の記事をご覧ください。

> [!div class="nextstepaction"]
> [データドリブンのスタイルの式を使用する](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [シンボル レイヤーを追加する](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [バブル レイヤーを追加する](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [線レイヤーを追加する](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [多角形レイヤーを追加する](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [ヒート マップを追加する](map-add-heat-map-layer-android.md)

> [!div class="nextstepaction"]
> [Web SDK コード サンプル](/samples/browse/?products=azure-maps)