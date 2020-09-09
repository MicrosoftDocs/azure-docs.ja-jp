---
title: Android マップに交通情報データを表示する | Microsoft Azure Maps
description: この記事では、Microsoft Azure Maps Android SDK を使用して、マップに交通情報データを表示する方法について説明します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8b07b520dec4e9149c6748777a119b68e56c65e9
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2020
ms.locfileid: "87126399"
---
# <a name="show-traffic-data-on-the-map-using-azure-maps-android-sdk"></a>Azure Maps Android SDK を使用してマップに交通情報データを表示する

フロー データとインシデント データは、マップに表示できる 2 種類の交通情報データです。 このガイドでは、両方の種類の交通情報データを表示する方法について説明します。 インシデント データは、工事、道路閉鎖、交通事故といった事柄を示す、点と線に基づくデータで構成されます。 フロー データは、道路上の交通の流れに関するメトリックを示します。

## <a name="prerequisites"></a>前提条件

マップに交通情報を表示するには、事前に [Azure アカウントを作成](quick-demo-map-app.md#create-an-azure-maps-account)し、[サブスクリプション キーを取得](quick-demo-map-app.md#get-the-primary-key-for-your-account)しておく必要があります。 次に、[Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) をインストールしてマップを読み込む必要があります。

## <a name="incidents-traffic-data"></a>インシデント交通情報データ

`setTraffic` と `incidents` を呼び出すために次のライブラリをインポートする必要があります。

```java
import static com.microsoft.com.azure.maps.mapcontrol.options.TrafficOptions.incidents;
```

 次のコード スニペットは、交通情報データをマップに表示する方法を示しています。 `incidents` メソッドにブール値を渡し、それを `setTraffic` メソッドに渡します。 

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map - > {
        map.setTraffic(incidents(true));
    }
}
```

## <a name="flow-traffic-data"></a>フロー交通情報データ

まず、`setTraffic` と `flow` を呼び出すために、次のライブラリをインポートする必要があります。

```java
import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
```

次のコード スニペットを使用して、交通の流れのデータを設定します。 前のセクションのコードと同様に、`flow` メソッドの戻り値を `setTraffic` メソッドに渡します。 `flow` に渡せる値は 4 つあり、各値が `flow` をトリガーしてそれぞれの値が返されます。 次に、`flow` の戻り値が引数として `setTraffic` に渡されます。 これらの 4 つの値については、次の表を参照してください。

|Flow 値 | 説明|
| :-- | :-- |
| TrafficFlow.NONE | マップに交通情報データを表示しません |
| TrafficFlow.RELATIVE | 道路の自由流速度を基準にして交通情報データを表示します |
| TrafficFlow.RELATIVE_DELAY | 予想されている平均的な遅れよりも遅い地域を表示します |
| TrafficFlow.ABSOLUTE | 道路上の全車両の対地速度を表示します |

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map -> {
        map.setTraffic(flow(TrafficFlow.RELATIVE)));
    }
}
```

## <a name="show-incident-traffic-data-by-clicking-a-feature"></a>機能をクリックしてインシデント交通情報データを表示する

特定の機能のインシデントを取得するには、次のコードを使用できます。 機能をクリックすると、コード ロジックによって、インシデントの発生についてチェックが行われ、インシデントに関するメッセージが作成されます。 画面の下部に、詳細を含むメッセージが表示されます。

1. まず、**res > layout > activity_main.xml** を以下に示されているように編集する必要があります。 `mapcontrol_centerLat`、`mapcontrol_centerLng`、および `mapcontrol_zoom` は、目的の値に置き換えることができます。 この場合、ズーム レベルは 0 から 22 の値になることに注意してください。 ズーム レベルが 0 の場合は、世界全体が 1 つのタイルに収まります。

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
           app:mapcontrol_centerLat="47.6050"
           app:mapcontrol_centerLng="-122.3344"
           app:mapcontrol_zoom="12"
           />

   </FrameLayout>
   ```

2. **MainActivity.java** ファイルに次のコードを追加します。 パッケージは既定で含まれているので、常に一番上にパッケージを配置するようにしてください。

   ```java
   package <yourpackagename>;
   import androidx.appcompat.app.AppCompatActivity;

   import android.os.Bundle;
   import android.widget.Toast;

   import com.microsoft.azure.maps.mapcontrol.AzureMaps;
   import com.microsoft.azure.maps.mapcontrol.MapControl;
   import com.mapbox.geojson.Feature;
   import com.microsoft.azure.maps.mapcontrol.events.OnFeatureClick;

   import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.incidents;

   public class MainActivity extends AppCompatActivity {

       static {
           AzureMaps.setSubscriptionKey("Your Azure Maps Subscription Key");
       }

       MapControl mapControl;

       @Override
       protected void onCreate(Bundle savedInstanceState) {
           super.onCreate(savedInstanceState);
           setContentView(R.layout.activity_main);

           mapControl = findViewById(R.id.mapcontrol);

           mapControl.onCreate(savedInstanceState);

           //Wait until the map resources are ready.
           mapControl.getMapAsync(map -> {

               map.setTraffic(flow(TrafficFlow.RELATIVE));
               map.setTraffic(incidents(true));

               map.events.add((OnFeatureClick) (features) -> {

                   if (features != null && features.size() > 0) {
                       Feature incident = features.get(0);
                       if (incident.properties() != null) {


                           StringBuilder sb = new StringBuilder();
                           String incidentType = incident.getStringProperty("incidentType");
                           if (incidentType != null) {
                               sb.append(incidentType);
                           }
                           if (sb.length() > 0) sb.append("\n");
                           if ("Road Closed".equals(incidentType)) {
                               sb.append(incident.getStringProperty("from"));
                           } else {
                               String description = incident.getStringProperty("description");
                               if (description != null) {
                                   for (String word : description.split(" ")) {
                                       if (word.length() > 0) {
                                           sb.append(word.substring(0, 1).toUpperCase());
                                           if (word.length() > 1) {
                                               sb.append(word.substring(1));
                                           }
                                           sb.append(" ");
                                       }
                                   }
                               }
                           }
                           String message = sb.toString();

                           if (message.length() > 0) {
                               Toast.makeText(this,message,Toast.LENGTH_LONG).show();
                           }
                       }
                   }
               });
           });
       }

       @Override
       public void onResume() {
           super.onResume();
           mapControl.onResume();
       }

       @Override
       protected void onStart(){
           super.onStart();
           mapControl.onStart();
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

3. アプリケーションに上記のコードを組み込むと、機能をクリックして交通インシデントの詳細を表示できるようになります。 **activity_main.xml** ファイルで使用した緯度、経度、およびズーム レベルの値に応じて、次の図のような結果が表示されます。

   <center>

   ![マップ上のインシデント交通情報](./media/how-to-show-traffic-android/android-traffic.png)

   </center>

## <a name="next-steps"></a>次のステップ

マップにデータを追加する方法については、次のガイドを参照してください。

> [!div class="nextstepaction"]
> [シンボル レイヤーを追加する](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [タイル レイヤーを追加する](how-to-add-tile-layer-android-map.md)

> [!div class="nextstepaction"]
> [Android マップに図形を追加する](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [フィーチャーの情報を表示する](display-feature-information-android.md)
