---
title: チュートリアル:GeoJSON データを Azure Maps Android SDK に読み込む | Microsoft Azure Maps
description: GeoJSON データ ファイルを Azure Maps Android マップ SDK に読み込む方法についてのチュートリアル。
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 8300a7c120ce816c8068a88fa69f4f978fa664ca
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034508"
---
# <a name="tutorial-load-geojson-data-into-azure-maps-android-sdk"></a>チュートリアル:GeoJSON データを Azure Maps Android SDK に読み込む

このチュートリアルでは、場所データの GeoJSON ファイルを Azure Maps Android SDK にインポートする手順を説明します。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure Maps を Android アプリケーションに追加する。
> * データ ソースを作成してローカル ファイルまたは Web から GeoJSON ファイルを読み込む。
> * マップにデータを表示する。

## <a name="prerequisites"></a>[前提条件]

1. 「[Quickstart:Android アプリの作成](quick-android-map.md)。 このクイックスタートで使用したコードをこのチュートリアルで拡張します。
2. [目的地サンプル](https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/data/geojson/SamplePoiDataSet.json)の GeoJSON ファイルをダウンロードします。

### <a name="import-geojson-data-from-web-or-assets-folder"></a>GeoJSON データを Web または assets フォルダーからインポートする

ほとんどの GeoJSON ファイルには、`FeatureCollection` 内のすべてのデータがラップされています。 そのため、GeoJSON ファイルを文字列としてアプリケーションに読み込む場合、それらを地物コレクションの静的 `fromJson` メソッドに渡すことで、GeoJSON の `FeatureCollection` オブジェクトに文字列を逆シリアル化してマップに追加することができます。

次の手順は、アプリケーションに GeoJSON ファイルをインポートし、それを GeoJSON の `FeatureCollection` オブジェクトとして逆シリアル化する方法を示しています。

1. 「[Quickstart:Android アプリの作成](quick-android-map.md)。以降の手順は、そのアプリケーションを前提として記述されています。
2. Android Studio のプロジェクト パネルで **app** フォルダーを右クリックし、`New > Folder > Assets Folder` に移動します。
3. [目的地サンプル](https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/data/geojson/SamplePoiDataSet.json)の GeoJSON ファイルを assets フォルダーにドラッグ アンド ドロップします。

::: zone pivot="programming-language-java-android"

4. **Utils.java** という新しいファイルを作成し、そのファイルに以下のコードを追加します。 このコードには、簡単なコールバック メソッドを使用して、アプリケーションの `assets` フォルダーから、または URL を使用して Web から、ファイルを文字列として非同期的にインポートして UI スレッドに返す `importData` という静的メソッドがあります。

    ```java
    //Modify the package name as needed to align with your application.
    package com.example.myapplication;
    
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

5. **MainActivity.java** ファイルに移動し、`mapControl.onReady` イベントのコールバック内に以下のコードを追加します。これは `onCreate` メソッド内にあります。 このコードでは、インポート ユーティリティを使用して、**SamplePoiDataSet.json** ファイルを文字列として読み取り、`FeatureCollection` クラスの静的 `fromJson` メソッドを使用して、それを地物コレクションとして逆シリアル化します。 このコードでは、地物コレクションに含まれているすべてのデータの境界ボックス領域を計算し、それを使用して、データにフォーカスするようにマップのカメラを設定します。

    ```java
    //Create a data source and add it to the map.
    DataSource source = new DataSource();
    map.sources.add(source);
    
    //Import the GeoJSON data and add it to the data source.
    Utils.importData("SamplePoiDataSet.json",
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

                //Padding added to account for pixel size of rendered points.
                padding(20)
            );
        });
    ```

6. GeoJSON データをデータ ソースとして読み込むコードを使用して、そのデータをどのようにマップ上に表示するかを指定する必要があります。 ポイント データには、いくつかの異なるレンダリング レイヤーが存在します。最もよく使用されるレイヤーは、[バブル レイヤー](map-add-bubble-layer-android.md)、[シンボル レイヤー](how-to-add-symbol-to-android-map.md)、[ヒート マップ レイヤー](map-add-heat-map-layer-android.md)です。 `mapControl.onReady` イベントのコールバックで、データをインポートするためのコードの後に、データをバブル レイヤーにレンダリングする次のコードを追加します。

    ```java
    //Create a layer and add it to the map.
    BubbleLayer layer = new BubbleLayer(source);
    map.layers.add(layer);
    ```

::: zone-end

::: zone pivot="programming-language-kotlin"

4. **Utils.kt** という新しいファイルを作成し、そのファイルに以下のコードを追加します。 このコードには、簡単なコールバック メソッドを使用して、アプリケーションの `assets` フォルダーから、または URL を使用して Web から、ファイルを文字列として非同期的にインポートして UI スレッドに返す `importData` という静的メソッドがあります。

    ```kotlin
    //Modify the package name as needed to align with your application.
    package com.example.myapplication;

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

5. **MainActivity.kt** ファイルに移動し、`mapControl.onReady` イベントのコールバック内に以下のコードを追加します。これは `onCreate` メソッド内にあります。 このコードでは、インポート ユーティリティを使用して、**SamplePoiDataSet.json** ファイルを文字列として読み取り、`FeatureCollection` クラスの静的 `fromJson` メソッドを使用して、それを地物コレクションとして逆シリアル化します。 このコードでは、地物コレクションに含まれているすべてのデータの境界ボックス領域を計算し、それを使用して、データにフォーカスするようにマップのカメラを設定します。

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

6. GeoJSON データをデータ ソースとして読み込むコードを使用して、そのデータをどのようにマップ上に表示するかを指定する必要があります。 ポイント データには、いくつかの異なるレンダリング レイヤーが存在します。最もよく使用されるレイヤーは、[バブル レイヤー](map-add-bubble-layer-android.md)、[シンボル レイヤー](how-to-add-symbol-to-android-map.md)、[ヒート マップ レイヤー](map-add-heat-map-layer-android.md)です。 `mapControl.onReady` イベントのコールバックで、データをインポートするためのコードの後に、データをバブル レイヤーにレンダリングする次のコードを追加します。

    ```kotlin
    //Create a layer and add it to the map.
    val layer = new BubbleLayer(source)
    map.layers.add(layer)
    ```

::: zone-end

7. アプリケーションを実行します。 米国にフォーカスされた状態でマップが表示され、GeoJSON ファイル内のそれぞれの場所に円が重ねて表示されます。

    ![GeoJSON ファイル内のデータが表示された米国のマップ](media/tutorial-load-geojson-file-android/android-import-geojson.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次の手順に従って、このチュートリアルのリソースをクリーンアップします。

1. Android Studio を閉じて、作成したアプリケーションを削除します。
2. 外部のデバイスでアプリケーションをテストした場合は、そのデバイスからアプリケーションをアンインストールします。

## <a name="next-steps"></a>次の手順

他のコード例や対話型のコーディング エクスペリエンスについては、以下を参照してください。

> [!div class="nextstepaction"]
> [データドリブンのスタイルの式を使用する](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [フィーチャーの情報を表示する](display-feature-information-android.md)

> [!div class="nextstepaction"]
> [シンボル レイヤーを追加する](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [線レイヤーを追加する](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [多角形レイヤーを追加する](how-to-add-shapes-to-android-map.md)
