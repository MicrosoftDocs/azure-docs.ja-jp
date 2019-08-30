---
title: Azure Maps での Android マップへのタイル レイヤーの追加 |Microsoft Docs
description: Azure Maps Android SDK を使用してマップにタイル レイヤーを追加する方法
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 5d5f50a38db95f6e62bdd8c51aefd5957041e682
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68886454"
---
# <a name="add-a-tile-layer-to-a-map-using-the-azure-maps-android-sdk"></a>Azure Maps Android SDK を使用してマップにタイル レイヤーを追加する

この記事では、Azure Maps Android SDK を使用してマップにタイル レイヤーをレンダリングする方法を示します。 タイル レイヤーを使用すると、Azure Maps ベース マップ タイルの上に画像を重ねることができます。 Azure Maps のタイル システムの詳細については、[ズーム レベルとタイル グリッド](zoom-levels-and-tile-grid.md)のドキュメントを参照してください。

タイル レイヤーでは、サーバーからタイルが読み込まれます。 これらの画像は、タイル レイヤーが認識できる名前付け規則を使用するか、画像をその場で生成する動的サービスを使用して、サーバー上の他の画像と同様に事前にレンダリングし、保存することができます。 Azure Maps の TileLayer クラスでは、3 種類のタイル サービスの名前付け規則がサポートされています。 

* X、Y、ズーム表記 - ズーム レベルに基づいた、タイル グリッド内のタイルの列 (x)、行 (x) の位置です。
* quadkey 表記 - x、y、ズーム情報を組み合わせて、タイルの一意の識別子である 1 つの文字列値にします。
* 境界ボックス - 境界ボックス座標を使用して、[Web マッピング サービス (WMS)](https://www.opengeospatial.org/standards/wms) で一般的に使用されている `{west},{south},{east},{north}` の形式で画像を指定することができます。

> [!TIP]
> TileLayer は、マップ上で大規模なデータ セットを視覚化する場合に適しています。 画像からタイル レイヤーを生成できるだけでなく、ベクター データもタイル レイヤーとしてレンダリングできます。 ベクター データをタイル レイヤーとしてレンダリングすることで、マップ コントロールでは、タイルが表すベクター データよりもはるかにファイル サイズの小さいタイルを読み込むだけで済みます。 この手法は、何百万行ものデータをマップにレンダリングする必要がある場合によく使われています。

タイル レイヤーに渡されるタイル URL は、TileJSON リソースへの http/https URL、または次のパラメーターを使用するタイル URL テンプレートにする必要があります。 

* `{x}` - タイルの X 位置。 `{y}` と `{z}` も必要です。
* `{y}` - タイルの Y 位置。 `{x}` と `{z}` も必要です。
* `{z}` - タイルのズーム レベル。 `{x}` と `{y}` も必要です。
* `{quadkey}` - Bing Maps タイル システムの名前付け規則に基づくタイルの quadkey 識別子。
* `{bbox-epsg-3857}` - EPSG 3857 空間参照系の `{west},{south},{east},{north}` 形式の境界ボックス文字列。
* `{subdomain}` - プレースホルダー。サブドメインの値が指定されると、ここに追加されます。

## <a name="prerequisites"></a>前提条件

この記事のプロセスを完了するには、[Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) をインストールしてマップを読み込む必要があります。


## <a name="add-a-tile-layer-to-the-map"></a>マップにタイル レイヤーを追加する

 このサンプルは、x、y、ズーム タイル システムを使用するタイルのセットを指すタイル レイヤーを作成する方法を示しています。 このタイル レイヤーのソースは、[アイオワ州立大学の Iowa Environmental Mesonet](https://mesonet.agron.iastate.edu/ogc/) の気象レーダー オーバーレイです。 

次の手順に従って、マップにタイル レイヤーを追加できます。

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
            app:mapcontrol_centerLat="40.75"
            app:mapcontrol_centerLng="-99.47"
            app:mapcontrol_zoom="3"
            />
    
    </FrameLayout>
    ```

2. 次のコード スニペットを `MainActivity.java` クラスの **onCreate()** メソッドにコピーします。

    ```Java
    mapControl.onReady(map -> {
        //Add a tile layer to the map, below the map labels.
        map.layers.add(new TileLayer(
            tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
            opacity(0.8f),
            tileSize(256)
        ), "labels");
    });
    ```
    
    上記のコード スニペットは、まず **onReady()** コールバック メソッドを使用して Azure Maps コントロール インスタンスを取得します。 次に、`TileLayer` オブジェクトを作成し、書式設定された **xyz** タイル URL を `tileUrl` オプションに渡します。 レイヤーの不透明度は `0.8` に設定されます。使用されているタイル サービスのタイルは 256 ピクセルのタイルであるため、この情報が `tileSize` オプションに渡されます。 タイル レイヤーは、次にマップ レイヤー マネージャーに渡されます。

    上記のコード スニペットを追加したら、`MainActivity.java` は次のようになります。
    
    ```Java
    package com.example.myapplication;

    import android.app.Activity;
    import android.os.Bundle;
    import android.support.v7.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.layer.TileLayer;
    import java.util.Arrays;
    import java.util.List;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import static com.microsoft.azure.maps.mapcontrol.options.TileLayerOptions.tileSize;
    import static com.microsoft.azure.maps.mapcontrol.options.TileLayerOptions.tileUrl;
        
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

                //Add a tile layer to the map, below the map labels.
                map.layers.add(new TileLayer(
                    tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
                    opacity(0.8f),
                    tileSize(256)
                ), "labels");
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

![Android マップの線](./media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)</center>

## <a name="next-steps"></a>次の手順

マップのスタイルを設定する方法の詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [Android マップでのマップ スタイルの変更](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)