---
title: チュートリアル - Android アプリを移行する | Microsoft Azure Maps
description: Google Maps から Microsoft Azure Maps に Android アプリを移行する方法についてのチュートリアルです。
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 3d160649008199233fa0b676d938470569a27853
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102101496"
---
# <a name="tutorial-migrate-an-android-app-from-google-maps"></a>チュートリアル:Google Maps から Android アプリを移行する

Azure Maps Android SDK の API インターフェイスは、Web SDK と似ています。 これらの SDK のいずれかを使用して開発した場合、同じ概念、ベスト プラクティス、アーキテクチャの多くが適用されます。 このチュートリアルでは、次の内容を学習します。

> [!div class="checklist"]
> * マップを読み込む
> * マップをローカライズする
> * マーカー、ポリライン、およびポリゴンを追加する。
> * タイル レイヤーをオーバーレイする
> * トラフィック データを表示する

すべての例は Java で提供されていますが、Azure Maps Android SDK では Kotlin を使用できます。

Azure Maps による Android SDK を使用した開発の詳細については、[Azure Maps Android SDK のハウツー ガイド](how-to-use-android-map-control-library.md)のページを参照してください。

## <a name="prerequisites"></a>前提条件

1. [Azure portal](https://portal.azure.com) にサインインして Azure Maps アカウントを作成します。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
2. [Azure Maps アカウントを作成します](quick-demo-map-app.md#create-an-azure-maps-account)
3. [プライマリ サブスクリプション キー (主キーまたはサブスクリプション キーとも呼ばれます) を取得します](quick-demo-map-app.md#get-the-primary-key-for-your-account)。 Azure Maps での認証の詳細については、「[Azure Maps での認証の管理](how-to-manage-authentication.md)」を参照してください。

## <a name="load-a-map"></a>マップを読み込む

Google マップを使用する場合も、Azure Maps を使用する場合も、Android アプリでのマップの読み込み手順は同様です。 いずれかの SDK を使うときは、次のことを行う必要があります。

* いずれかのプラットフォームにアクセスするための API キーまたはサブスクリプション キーを取得します。
* アクティビティに XML を追加して、マップのレンダリング先とレイアウト方法を指定します。
* マップ ビューを含むアクティビティから、マップ クラスの対応するメソッドに、すべてのライフサイクル メソッドをオーバーライドします。 特に、次のメソッドをオーバーライドする必要があります。
  * `onCreate(Bundle)`
  * `onStart()`
  * `onResume()`
  * `onPause()`
  * `onStop()`
  * `onDestroy()`
  * `onSaveInstanceState(Bundle)`
  * `onLowMemory()`
* マップの準備ができるまで待ってから、アクセスとプログラムを試みます。

### <a name="before-google-maps"></a>前: Google Maps

Google Maps SDK for Android を使用してマップを表示するには、次の手順のようにします。

1. Google Play サービスがインストールされていることを確認します。
2. Google Maps サービスの依存関係を、モジュールの **gradle.build** ファイルに追加します。

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

3. **google\_maps\_api.xml** ファイルのアプリケーション セクションに、Google Maps API キーを追加します。

    ```xml
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_GOOGLE_MAPS_KEY"/>
    ```

4. メイン アクティビティにマップ フラグメントを追加します。

    ```xml
    <com.google.android.gms.maps.MapView
            android:id="@+id/myMap"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    ```

::: zone pivot="programming-language-java-android"

5. **MainActivity.java** ファイルには、Google マップ SDK をインポートする必要があります。 マップ ビューを含むアクティビティから、マップ クラスの対応するものに、すべてのライフサイクル メソッドを転送します。 `getMapAsync(OnMapReadyCallback)` メソッドを使用して、マップ フラグメントから `MapView` インスタンスを取得します。 `MapView` によって、Maps システムとビューが自動的に初期化されます。 **MainActivity.java** ファイルを次のように編集します。

    ```java
    import com.google.android.gms.maps.GoogleMap;
    import com.google.android.gms.maps.MapView;
    import com.google.android.gms.maps.OnMapReadyCallback;
 
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    
    public class MainActivity extends AppCompatActivity implements OnMapReadyCallback {
    
        MapView mapView;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapView = findViewById(R.id.myMap);
    
            mapView.onCreate(savedInstanceState);
            mapView.getMapAsync(this);
        }
    
        @Override
    
        public void onMapReady(GoogleMap map) {
            //Add your post map load code here.
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapView.onResume();
        }
    
        @Override
        protected void onStart(){
            super.onStart();
            mapView.onStart();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapView.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapView.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapView.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapView.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapView.onSaveInstanceState(outState);
        }
    }
    ```

::: zone-end

::: zone pivot="programming-language-kotlin"

5. **MainActivity.kt** ファイルで、Google マップ SDK をインポートする必要があります。 マップ ビューを含むアクティビティから、マップ クラスの対応するものに、すべてのライフサイクル メソッドを転送します。 `getMapAsync(OnMapReadyCallback)` メソッドを使用して、マップ フラグメントから `MapView` インスタンスを取得します。 `MapView` によって、Maps システムとビューが自動的に初期化されます。 **MainActivity.kt** ファイルを次のように編集します。

    ```kotlin
    import com.google.android.gms.maps.GoogleMap;
    import com.google.android.gms.maps.MapView;
    import com.google.android.gms.maps.OnMapReadyCallback;
 
    import androidx.appcompat.app.AppCompatActivity
    import android.os.Bundle

    class MainActivity : AppCompatActivity() implements OnMapReadyCallback {
    
        var mapView: MapView? = null
    
        override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)
            setContentView(R.layout.activity_main)
    
            mapView = findViewById(R.id.myMap)
    
            mapView?.onCreate(savedInstanceState)
            mapView?.getMapAsync(this)
        }

        public fun onMapReady(GoogleMap map) {
            //Add your post map load code here.
        }
    
        public override fun onStart() {
            super.onStart()
            mapView?.onStart()
        }
    
        public override fun onResume() {
            super.onResume()
            mapView?.onResume()
        }
    
        public override fun onPause() {
            mapView?.onPause()
            super.onPause()
        }
    
        public override fun onStop() {
            mapView?.onStop()
            super.onStop()
        }
    
        override fun onLowMemory() {
            mapView?.onLowMemory()
            super.onLowMemory()
        }
    
        override fun onDestroy() {
            mapView?.onDestroy()
            super.onDestroy()
        }
    
        override fun onSaveInstanceState(outState: Bundle) {
            super.onSaveInstanceState(outState)
            mapView?.onSaveInstanceState(outState)
        }
    }
    ```

::: zone-end

アプリケーションを実行すると、マップ コントロールが次の画像のように読み込まれます。

![シンプルな Google Maps](media/migrate-google-maps-android-app/simple-google-maps.png)

### <a name="after-azure-maps"></a>後: Azure Maps

Azure Maps SDK for Android を使用してマップを表示するには、次の手順のようにします。

1. 最上位の **build.gradle** ファイルを開き、次のコードを **すべてのプロジェクト** のブロック セクションに追加します。

    ```gradel
    maven {
        url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. **app/build.gradle** を更新し、以下のコードを追加します。

    1. プロジェクトの **minSdkVersion** が API 21 以降であることを確認します。

    2. Android セクションに、次のコードを追加します。

        ```gradel
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```

    3. 依存関係ブロックを更新します。 最新の Azure Maps Android SDK の新しい実装の依存関係の行を追加します。

        ```gradel
        implementation "com.microsoft.azure.maps:mapcontrol:0.7"
        ```

        > [!Note]
        > バージョン番号を "0+" に設定して、コードが常に最新バージョンを指すようにすることができます。

    4. ツール バーの **[ファイル]** に移動し、 **[Sync Project with Gradle Files]\(プロジェクトを Gradle ファイルと同期\)** をクリックします。

3. メイン アクティビティにマップ フラグメントを追加します (resources pwd\> layout \> activity\_main.xml)。

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
            />
    </FrameLayout>
    ```

::: zone pivot="programming-language-java-android"

4. **MainActivity.java** ファイルで、以下の操作を行う必要があります。

    * Azure Maps SDK をインポートする
    * Azure Maps の認証情報を設定する
    * **onCreate** メソッドでマップ コントロールのインスタンスを取得する

     `AzureMaps` クラスで `setSubscriptionKey` メソッドまたは `setAadProperties` メソッドを使用して認証情報を設定します。 このグローバルな更新により、認証情報が確実にすべてのビューに追加されます。

    マップ コントロールには、Android の OpenGL ライフサイクルを管理するための独自のライフサイクル メソッドが含まれています。 これらのメソッドは、それが含まれているアクティビティから直接呼び出す必要があります。 マップ コントロールのライフサイクル メソッドを正しく呼び出すためには、マップ コントロールを含むアクティビティで次のライフサイクル メソッドをオーバーライドする必要があります。 それぞれのマップ コントロール メソッドを呼び出してください。

    * `onCreate(Bundle)`
    * `onStart()`
    * `onResume()`
    * `onPause()`
    * `onStop()`
    * `onDestroy()`
    * `onSaveInstanceState(Bundle)`
    * `onLowMemory()`

    **MainActivity.java** ファイルを次のように編集します。

    ```java
    package com.example.myapplication;
    
    import androidx.appcompat.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;
    
    public class MainActivity extends AppCompatActivity {
    
    static {
        AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");

        //Alternatively use Azure Active Directory authenticate.
        //AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");
    }

    MapControl mapControl;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mapControl = findViewById(R.id.mapcontrol);

        mapControl.onCreate(savedInstanceState);

        //Wait until the map resources are ready.
        mapControl.onReady(map -> {
            //Add your post map load code here.

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
    }}
    ```

::: zone-end

::: zone pivot="programming-language-kotlin"

4. **MainActivity.kt** ファイルで、以下の操作を行う必要があります。

    * Azure Maps SDK をインポートする
    * Azure Maps の認証情報を設定する
    * **onCreate** メソッドでマップ コントロールのインスタンスを取得する

     `AzureMaps` クラスで `setSubscriptionKey` メソッドまたは `setAadProperties` メソッドを使用して認証情報を設定します。 このグローバルな更新により、認証情報が確実にすべてのビューに追加されます。

    マップ コントロールには、Android の OpenGL ライフサイクルを管理するための独自のライフサイクル メソッドが含まれています。 これらのメソッドは、それが含まれているアクティビティから直接呼び出す必要があります。 マップ コントロールのライフサイクル メソッドを正しく呼び出すためには、マップ コントロールを含むアクティビティで次のライフサイクル メソッドをオーバーライドする必要があります。 それぞれのマップ コントロール メソッドを呼び出してください。

    * `onCreate(Bundle)`
    * `onStart()`
    * `onResume()`
    * `onPause()`
    * `onStop()`
    * `onDestroy()`
    * `onSaveInstanceState(Bundle)`
    * `onLowMemory()`

    **MainActivity.kt** ファイルを次のように編集します。

    ```kotlin
    package com.example.myapplication;

    import androidx.appcompat.app.AppCompatActivity
    import android.os.Bundle
    import com.microsoft.azure.maps.mapcontrol.AzureMap
    import com.microsoft.azure.maps.mapcontrol.AzureMaps
    import com.microsoft.azure.maps.mapcontrol.MapControl
    import com.microsoft.azure.maps.mapcontrol.events.OnReady
    
    class MainActivity : AppCompatActivity() {
    
        companion object {
            init {
                AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
    
                //Alternatively use Azure Active Directory authenticate.
                //AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");
            }
        }
    
        var mapControl: MapControl? = null
    
        override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)
            setContentView(R.layout.activity_main)
    
            mapControl = findViewById(R.id.mapcontrol)
    
            mapControl?.onCreate(savedInstanceState)
    
            //Wait until the map resources are ready.
            mapControl?.onReady(OnReady { map: AzureMap -> })
        }
    
        public override fun onStart() {
            super.onStart()
            mapControl?.onStart()
        }
    
        public override fun onResume() {
            super.onResume()
            mapControl?.onResume()
        }
    
        public override fun onPause() {
            mapControl?.onPause()
            super.onPause()
        }
    
        public override fun onStop() {
            mapControl?.onStop()
            super.onStop()
        }
    
        override fun onLowMemory() {
            mapControl?.onLowMemory()
            super.onLowMemory()
        }
    
        override fun onDestroy() {
            mapControl?.onDestroy()
            super.onDestroy()
        }
    
        override fun onSaveInstanceState(outState: Bundle) {
            super.onSaveInstanceState(outState)
            mapControl?.onSaveInstanceState(outState)
        }
    }
    ```

::: zone-end

アプリケーションを実行すると、マップ コントロールが次の画像のように読み込まれます。

![シンプルな Azure Maps](media/migrate-google-maps-android-app/simple-azure-maps.png)

Azure Maps コントロールではさらに縮小がサポートされていて、より多くのワールド ビューが提供されることに注意してください。

> [!TIP]
> Windows マシンで Android エミュレーターを使用している場合、OpenGL およびソフトウェア アクセラレータのグラフィックス レンダリングとの競合が原因で、マップがレンダリングされないことがあります。 一部のユーザーは、次の方法でこの問題が解決しています。 AVD マネージャーを開き、編集する仮想デバイスを選択します。 **[Verify Configuration]\(構成の確認\)** パネルを下方向へスクロールします。 **[Emulated Performance]\(エミュレートされたパフォーマンス\)** セクションで、 **[Graphics]\(グラフィックス\)** オプションを **[Hardware]\(ハードウェア\)** に設定します。

## <a name="localizing-the-map"></a>マップのローカライズ

対象ユーザーが複数の国または地域に分散している場合や、使用されている言語が異なる場合は、ローカライズが重要になります。

### <a name="before-google-maps"></a>前: Google Maps

マップの言語を設定するには、`onCreate` メソッドに次のコードを追加します。 このコードは、マップのコンテキスト ビューを設定する前に追加する必要があります。 "fr" 言語コードによって、言語はフランス語に限定されています。

::: zone pivot="programming-language-java-android"

```java
String languageToLoad = "fr";
Locale locale = new Locale(languageToLoad);
Locale.setDefault(locale);

Configuration config = new Configuration();
config.locale = locale;

getBaseContext().getResources().updateConfiguration(config,
        getBaseContext().getResources().getDisplayMetrics());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val languageToLoad = "fr"
val locale = Locale(languageToLoad)
Locale.setDefault(locale)

val config = Configuration()
config.locale = locale

baseContext.resources.updateConfiguration(
    config,
    baseContext.resources.displayMetrics
)
```

::: zone-end

言語が "fr" に設定されている Google Maps の例を以下に示します。

![Google Maps のローカライズ](media/migrate-google-maps-android-app/google-maps-localization.png)

### <a name="after-azure-maps"></a>後: Azure Maps

Azure Maps には、マップの言語と地域ビューを設定するための 3 つの異なる方法が用意されています。 1 つ目は、言語と地域ビューの情報を `AzureMaps` クラスに渡す方法です。 この方法では、静的な `setLanguage` メソッドと `setView` メソッドをグローバルに使用します。 つまり、アプリに読み込まれたすべての Azure Maps コントロールに対して、既定の言語と地域のビューが設定されます。 この例では、"fr-FR" 言語コードを使用してフランス語を設定しています。

::: zone pivot="programming-language-java-android"

```java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("Auto");
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
companion object {
    init {
            //Set your Azure Maps Key.
        AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");
    
        //Set the language to be used by Azure Maps.
        AzureMaps.setLanguage("fr-FR");
    
        //Set the regional view to be used by Azure Maps.
        AzureMaps.setView("Auto");
    }
}
```

::: zone-end

2 つ目は、言語とビュー情報をマップ コントロール XML コードに渡す方法です。

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="Auto"
    />
```

3 つ目は、マップの `setStyle` メソッドを使用して、言語と地域のマップ ビューをプログラムで設定する方法です。 この方法では、コードが実行されるたびに言語と地域のビューが更新されます。

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    map.setStyle(
        language("fr-FR"),
        view("Auto")
    );
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    map.setStyle(
        language("fr-FR"),
        view("Auto")
    )
}
```

::: zone-end

以下に、言語が "fr-FR" に設定されている Azure Maps の例を示します。

![Azure Maps のローカライズ](media/migrate-google-maps-android-app/azure-maps-localization.png)

[サポートされている言語](supported-languages.md)の完全な一覧を確認してください。

## <a name="setting-the-map-view"></a>マップ ビューの設定

Azure Maps および Google Maps の両方の動的マップは、適切なメソッドを呼び出すことによって、プログラムで新しい地理的な場所に移動できます。 マップに衛星航空映像を表示し、特定の座標位置をマップの中心に設定して、ズーム レベルを変更してみましょう。 この例では、緯度は 35.0272、経度は -111.0225、ズーム レベルは 15 です。

### <a name="before-google-maps"></a>前: Google Maps

Google マップのマップ コントロールのカメラは、`moveCamera` メソッドを使用してプログラムで移動できます。 `moveCamera` メソッドを使用すると、マップの中心とズーム レベルを指定できます。 表示するマップの種類は、`setMapType` メソッドで変更します。

::: zone pivot="programming-language-java-android"

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
public override fun onMapReady(googleMap: GoogleMap) {
    mapView = googleMap

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(LatLng(35.0272, -111.0225), 15))
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE)
}
```

::: zone-end

![Google Maps の設定ビュー](media/migrate-google-maps-android-app/google-maps-set-view.png)

> [!NOTE]
> Google Maps では、ディメンションが 256 ピクセルのタイルが使用されますが、Azure Maps では、より大きな 512 ピクセルのタイルが使用されます。 これにより、Azure Maps で Google Maps と同じマップ領域を読み込むために必要なネットワーク要求の数が減ります。 Google マップのマップと同じ可視領域を得るためには、Azure Maps の使用時に Google マップで利用されるズーム レベルを 1 ずつ減算する必要があります。

### <a name="after-azure-maps"></a>後: Azure Maps

前に説明したように、Azure Maps で同じ表示可能領域を実現するには、Google マップで使用されているズーム レベルを 1 だけ小さくします。 このケースでは、ズーム レベル 14 を使用します。

初期マップ ビューは、マップ コントロールの XML 属性で設定できます。

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_cameraLat="35.0272"
    app:mapcontrol_cameraLng="-111.0225"
    app:mapcontrol_zoom="14"
    app:mapcontrol_style="satellite"
    />
```

マップ ビューは、マップの `setCamera` メソッドと `setStyle` メソッドを使用してプログラムできます。

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(Point.fromLngLat(-111.0225, 35.0272)), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    //Set the camera of the map.
    map.setCamera(center(Point.fromLngLat(-111.0225, 35.0272)), zoom(14))

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE))
}
```

::: zone-end

![Azure Maps の設定ビュー](media/migrate-google-maps-android-app/azure-maps-set-view.png)

**その他のリソース:**

* [サポートされているマップ スタイル](supported-map-styles.md)

## <a name="adding-a-marker"></a>マーカーの追加

マップ上の画像を使用してポイント データをレンダリングすることがよくあります。 これらの画像は、マーカー、画鋲、ピン、またはシンボルと呼ばれます。 次の例では、ポイント データをマップ上のマーカーとして表示します (緯度: 51.5、経度: -0.2)。

### <a name="before-google-maps"></a>前: Google Maps

Google Maps では、マップの `addMarker` メソッドを使用してマーカーを追加します。

::: zone pivot="programming-language-java-android"

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
public override fun onMapReady(googleMap: GoogleMap) {
    mapView = googleMap

    mapView.addMarker(MarkerOptions().position(LatLng(47.64, -122.33)))
}
```

::: zone-end

![Google Maps のマーカー](media/migrate-google-maps-android-app/google-maps-marker.png)

### <a name="after-azure-maps"></a>後: Azure Maps

Azure Maps で、ポイント データをマップにレンダリングします。それには、まずデータ ソースにデータを追加します。 その後、そのデータ ソースをシンボル レイヤーに接続します。 データ ソースにより、マップ コントロールでの空間データの管理が最適化されます。 シンボル レイヤーにより、画像やテキストを使用してポイント データをレンダリングする方法が指定されます。

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource source = new DataSource();
    map.sources.add(source);

    //Create a point feature and add it to the data source.
    source.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Create a symbol layer and add it to the map.
    map.layers.add(new SymbolLayer(source));
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    //Create a data source and add it to the map.
    val source = new DataSource()
    map.sources.add(source)

    //Create a point feature and add it to the data source.
    source.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)))

    //Create a symbol layer and add it to the map.
    map.layers.add(SymbolLayer(source))
}
```

::: zone-end

![Azure Maps のマーカー](media/migrate-google-maps-android-app/azure-maps-marker.png)

## <a name="adding-a-custom-marker"></a>カスタム マーカーの追加

カスタム イメージを使用すると、マップ上のポイントを表すことができます。 以下の例のマップでは、カスタム画像を使用してマップ上のポイントを表示します。 ポイントの緯度は 51.5 で、経度は -0.2 です。 アンカーは、マーカーの位置をオフセットして、画鋲アイコンのポイントがマップ上の正しい位置に揃うようにします。

![黄色の画鋲のイメージ](media/migrate-google-maps-web-app/yellow-pushpin.png)<br/>
yellow-pushpin.png

どちらの例でも、上のイメージはアプリ リソースの drawable フォルダーに追加されます。

### <a name="before-google-maps"></a>前: Google Maps

Google Maps では、カスタム イメージをマーカーに使用できます。 マーカーの `icon` オプションを使用して、カスタム イメージを読み込みます。 イメージのポイントを座標に合わせるには、`anchor` オプションを使用します。 アンカーは、画像の大きさに対する相対値です。 このケースでは、幅が 0.2 単位で、高さが 1 単位です。

::: zone pivot="programming-language-java-android"

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33))
    .icon(BitmapDescriptorFactory.fromResource(R.drawable.yellow-pushpin))
    .anchor(0.2f, 1f));
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
public override fun onMapReady(googleMap: GoogleMap) {
    mapView = googleMap;

    mapView.addMarker(MarkerOptions().position(LatLng(47.64, -122.33))
    .icon(BitmapDescriptorFactory.fromResource(R.drawable.yellow-pushpin))
    .anchor(0.2f, 1f))
}
```

::: zone-end

![Google Maps のカスタム マーカー](media/migrate-google-maps-android-app/google-maps-custom-marker.png)

### <a name="after-azure-maps"></a>後: Azure Maps

Azure Maps のシンボル レイヤーでもカスタム画像がサポートされますが、最初に画像をマップ リソースに読み込んで、一意の ID を割り当てる必要があります。 その後、シンボル レイヤーでこの ID を参照する必要があります。 `iconOffset` オプションを使用して、イメージ上の正しいポイントに合わせるために、シンボルをオフセットします。 アイコンのオフセットは、ピクセル単位です。 既定では、オフセットの基準はイメージの下端中央になりますが、このオフセット値は `iconAnchor` オプションを使用して調整できます。 この例では、`iconAnchor` オプションを `"center"` に設定しています。 アイコン オフセットを使用して、画像を右に 5 ピクセル、上に 15 ピクセルだけ移動して、画鋲の画像の位置に合わせています。

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource source = new DataSource();
    map.sources.add(source);

    //Create a point feature and add it to the data source.
    source.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Load the custom image icon into the map resources.
    map.images.add("my-yellow-pin", R.drawable.yellow_pushpin);

    //Create a symbol that uses the custom image icon and add it to the map.
    map.layers.add(new SymbolLayer(source,
        iconImage("my-yellow-pin"),
        iconAnchor(AnchorType.CENTER),
        iconOffset(new Float[]{5f, -15f})));
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    //Create a data source and add it to the map.
    val source = DataSource()
    map.sources.add(source)

    //Create a point feature and add it to the data source.
    source.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)))

    //Load the custom image icon into the map resources.
    map.images.add("my-yellow-pin", R.drawable.yellow_pushpin)

    //Create a symbol that uses the custom image icon and add it to the map.
    map.layers.add(SymbolLayer(source,
        iconImage("my-yellow-pin"),
        iconAnchor(AnchorType.CENTER),
        iconOffset(arrayOf(0f, -1.5f))))
}
```

::: zone-end

![Azure Maps のカスタム マーカー](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)

## <a name="adding-a-polyline"></a>ポリラインの追加

ポリラインは、マップ上の線またはパスを表すために使用されます。 次の例では、マップ上に破線のポリラインを作成する方法を示します。

### <a name="before-google-maps"></a>前: Google Maps

Google マップでは、`PolylineOptions` クラスを使用してポリラインをレンダリングします。 ポリラインをマップに追加するには、`addPolyline` メソッドを使用します。 ストロークの色は、`color` オプションを使用して設定します。 ストロークの幅は、`width` オプションを使用して設定します。 ストロークの破線の配列は、`pattern` オプションを使用して追加します。

::: zone pivot="programming-language-java-android"

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polyline.
    PolylineOptions lineOptions = new PolylineOptions()
        .add(new LatLng(46, -123))
        .add(new LatLng(49, -122))
        .add(new LatLng(46, -121))
        .color(Color.RED)
        .width(10f)
        .pattern(Arrays.<PatternItem>asList(
                new Dash(30f), new Gap(30f)));

    //Add the polyline to the map.
    Polyline polyline = mapView.addPolyline(lineOptions);
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
public override fun onMapReady(googleMap: GoogleMap) {
    mapView = googleMap

    //Create the options for the polyline.
    val lineOptions = new PolylineOptions()
        .add(new LatLng(46, -123))
        .add(new LatLng(49, -122))
        .add(new LatLng(46, -121))
        .color(Color.RED)
        .width(10f)
        .pattern(Arrays.<PatternItem>asList(
                new Dash(30f), new Gap(30f)))

    //Add the polyline to the map.
    val polyline = mapView.addPolyline(lineOptions)
}
```

::: zone-end

![Google Maps のポリライン](media/migrate-google-maps-android-app/google-maps-polyline.png)

### <a name="after-azure-maps"></a>後: Azure Maps

Azure Maps では、ポリラインが `LineString` または `MultiLineString` オブジェクトと呼ばれます。 これらのオブジェクトをデータ ソースに追加し、線レイヤーを使用してレンダリングします。 ストロークの幅は、`strokeWidth` オプションを使用して設定します。 ストロークの破線の配列は、`strokeDashArray` オプションを使用して追加します。

Azure Maps Web SDK におけるストロークの幅と破線の配列は、Google マップ サービスと同じく "ピクセル" 単位です。 どちらも、同じ値を渡せば同じ結果が得られます。

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource source = new DataSource();
    map.sources.add(source);

    //Create an array of points.
    List<Point> points = Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46));

    //Create a LineString feature and add it to the data source.
    source.add(Feature.fromGeometry(LineString.fromLngLats(points)));

    //Create a line layer and add it to the map.
    map.layers.add(new LineLayer(source,
        strokeColor("red"),
        strokeWidth(4f),
        strokeDashArray(new Float[]{3f, 3f})));
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    //Create a data source and add it to the map.
    val source = DataSource()
    map.sources.add(source)

    //Create an array of points.
    val points = Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46))

    //Create a LineString feature and add it to the data source.
    source.add(Feature.fromGeometry(LineString.fromLngLats(points)))

    //Create a line layer and add it to the map.
    map.layers.add(LineLayer(source,
        strokeColor("red"),
        strokeWidth(4f),
        strokeDashArray(new Float[]{3f, 3f})))
}
```

::: zone-end

![Azure Maps のポリライン](media/migrate-google-maps-android-app/azure-maps-polyline.png)

## <a name="adding-a-polygon"></a>多角形の追加

多角形は、マップ上の領域を表すために使用されます。 次の例では、多角形の作成方法を紹介します。 この多角形は、マップの中心座標に基づいて三角形を形成します。

### <a name="before-google-maps"></a>前: Google Maps

Google マップでは、`PolygonOptions` クラスを使用して多角形をレンダリングします。 多角形をマップに追加するには、`addPolygon` メソッドを使用します。 塗りつぶしとストロークの色は、それぞれ `fillColor` オプションと `strokeColor` オプションを使用して設定します。 ストロークの幅は、`strokeWidth` オプションを使用して設定します。

::: zone pivot="programming-language-java-android"

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polygon.
    PolygonOptions polygonOptions = new PolygonOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .add(new LatLng(46, -123))  //Close the polygon.
            .fillColor(Color.argb(128, 0, 128, 0))
            .strokeColor(Color.RED)
            .strokeWidth(5f);

    //Add the polygon to the map.
    Polygon polygon = mapView.addPolygon(polygonOptions);
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
public override fun onMapReady(googleMap: GoogleMap) {
    mapView = googleMap;

    //Create the options for the polygon.
    val polygonOptions = PolygonOptions()
        .add(new LatLng(46, -123))
        .add(new LatLng(49, -122))
        .add(new LatLng(46, -121))
        .add(new LatLng(46, -123))  //Close the polygon.
        .fillColor(Color.argb(128, 0, 128, 0))
        .strokeColor(Color.RED)
        .strokeWidth(5f)

    //valAdd the polygon to the map.
    Polygon polygon = mapView.addPolygon(polygonOptions)
}
```

::: zone-end

![Google Maps の多角形](media/migrate-google-maps-android-app/google-maps-polygon.png)

### <a name="after-azure-maps"></a>後: Azure Maps

Azure Maps では、`Polygon` および `MultiPolygon` オブジェクトをデータ ソースに追加し、レイヤーを使用してマップ上にレンダリングします。 多角形の領域は、多角形レイヤーを使用してレンダリングします。 多角形の枠線は、線レイヤーを使用してレンダリングします。 ストロークの色と幅は、`strokeColor` オプションと `strokeWidth` オプションを使用して設定します。

Azure Maps Web SDK におけるストロークの幅と破線の配列は、Google マップに合わせて "ピクセル" 単位となっています。 どちらも、同じ値を渡せば同じ結果が得られます。

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource source = new DataSource();
    map.sources.add(source);

    //Create an array of point arrays to create polygon rings.
    List<List<Point>> rings = Arrays.asList(Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46),
        Point.fromLngLat(-123, 46)));

    //Create a Polygon feature and add it to the data source.
    source.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));

    //Add a polygon layer for rendering the polygon area.
    map.layers.add(new PolygonLayer(source,
        fillColor("green"),
        fillOpacity(0.5f)));

    //Add a line layer for rendering the polygon outline.
    map.layers.add(new LineLayer(source,
        strokeColor("red"),
        strokeWidth(2f)));
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    //Create a data source and add it to the map.
    val source = DataSource()
    map.sources.add(source)

    //Create an array of point arrays to create polygon rings.
    val rings = Arrays.asList(Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46),
        Point.fromLngLat(-123, 46)))

    //Create a Polygon feature and add it to the data source.
    source.add(Feature.fromGeometry(Polygon.fromLngLats(rings)))

    //Add a polygon layer for rendering the polygon area.
    map.layers.add(PolygonLayer(source,
        fillColor("green"),
        fillOpacity(0.5f)))

    //Add a line layer for rendering the polygon outline.
    map.layers.add(LineLayer(source,
        strokeColor("red"),
        strokeWidth(2f)))
}
```

::: zone-end

![Azure Maps の多角形](media/migrate-google-maps-android-app/azure-maps-polygon.png)

## <a name="overlay-a-tile-layer"></a>タイル レイヤーをオーバーレイする

 マップ タイル システムに合わせて、より小さなタイル表示された画像に分割されたレイヤー画像をオーバーレイするには、タイル レイヤーを使用します。 このアプローチは、レイヤー イメージや大きなデータ セットをオーバーレイする一般的な方法です。 タイル レイヤーは、Google Maps ではイメージ オーバーレイと呼ばれます。

次の例では、アイオワ州立大学の Iowa Environmental Mesonet の気象レーダー タイル レイヤーをオーバーレイします。 タイルのサイズは 256 ピクセルです。

### <a name="before-google-maps"></a>前: Google Maps

Google マップでは、タイル レイヤーをマップの上にオーバーレイできます。 `TileOverlayOptions` クラスを使用します。 タイル レイヤーをマップに追加するには、`addTileLayer` メソッドを使用します。 タイルを半透明にするには、`transparency` オプションを 0.2 つまり 20% の透明度に設定します。

::: zone pivot="programming-language-java-android"

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the tile layer.
    TileOverlayOptions tileLayer = new TileOverlayOptions()
        .tileProvider(new UrlTileProvider(256, 256) {
            @Override
            public URL getTileUrl(int x, int y, int zoom) {

                try {
                    //Define the URL pattern for the tile images.
                    return new URL(String.format("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/%d/%d/%d.png", zoom, x, y));
                }catch (MalformedURLException e) {
                    throw new AssertionError(e);
                }
            }
        }).transparency(0.2f);

    //Add the tile layer to the map.
    mapView.addTileOverlay(tileLayer);
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
public override fun onMapReady(googleMap: GoogleMap) {
    mapView = googleMap
    //Create the options for the tile layer.
    val tileLayer: TileOverlayOptions = TileOverlayOptions()
        .tileProvider(object : UrlTileProvider(256, 256) {
            fun getTileUrl(x: Int, y: Int, zoom: Int): URL? {
                return try { //Define the URL pattern for the tile images.
                    URL(
                        String.format(
                            "https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/%d/%d/%d.png",
                            zoom,
                            x,
                            y
                        )
                    )
                } catch (e: MalformedURLException) {
                    throw AssertionError(e)
                }
            }
        }).transparency(0.2f)
    //Add the tile layer to the map.
    mapView.addTileOverlay(tileLayer)
}
```

::: zone-end

![Google Maps のタイル レイヤー](media/migrate-google-maps-android-app/google-maps-tile-layer.png)

### <a name="after-azure-maps"></a>後: Azure Maps

他のレイヤーと同じような方法で、タイル レイヤーをマップに追加できます。 x、y、ズーム プレースホルダー(`{x}`、`{y}`、`{z}`) の書式設定された URL はそれぞれ、タイルにアクセスする場所をレイヤーに指示するために使用されます。 また、Azure Maps のタイル レイヤーでは、`{quadkey}`、`{bbox-epsg-3857}`、`{subdomain}` のプレースホルダーがサポートされます。 タイル レイヤーを半透明にするには、不透明度の値 0.8 を使用します。 不透明度と透明度は似ていますが、逆の値を使用します。 2 つのオプション間で変換するには、その値を 1 から減算します。

> [!TIP]
> Azure Maps では、レイヤーを他のレイヤー (基準マップ レイヤーを含む) の下にレンダリングすると便利です。 また、多くの場合、読みやすいようにタイル レイヤーをマップ ラベルの下にレンダリングすることをお勧めします。 `map.layers.add` メソッドでは、2 番目のパラメーターを受け取ります。これは、下に新しいレイヤーを挿入するレイヤーの ID です。 マップ ラベルの下にタイル レイヤーを挿入する場合は、次のコードを使用できます: `map.layers.add(myTileLayer, "labels");`

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    //Add a tile layer to the map, below the map labels.
    map.layers.add(new TileLayer(
        tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
        opacity(0.8f),
        tileSize(256)
    ), "labels");
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    //Add a tile layer to the map, below the map labels.
    map.layers.add(TileLayer(
        tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
        opacity(0.8f),
        tileSize(256)
    ), "labels")
}
```

::: zone-end

![Azure Maps のタイル レイヤー](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)

## <a name="show-traffic"></a>交通情報を表示する

Azure Maps も Google マップも、交通情報データをオーバーレイするためのオプションがあります。

### <a name="before-google-maps"></a>前: Google Maps

Google マップでは、マップの `setTrafficEnabled` メソッドに true を渡すことで、交通量データをマップの上にオーバーレイできます。

::: zone pivot="programming-language-java-android"

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
public override fun onMapReady(googleMap: GoogleMap) {
    mapView = googleMap

    mapView.setTrafficEnabled(true)
}
```

::: zone-end

![Google Maps の交通情報](media/migrate-google-maps-android-app/google-maps-traffic.png)

### <a name="after-azure-maps"></a>後: Azure Maps

Azure Maps では、交通情報を表示するためのさまざまなオプションが提供されます。 道路の閉鎖や事故などの交通事故は、マップ上にアイコンとして表示できます。 交通量と色分けされた道路を、マップにオーバーレイできます。 掲示された速度制限、通常の予想される遅延、または絶対遅延を基準として、色を変更することができます。 Azure Maps の事故データは 1 分ごとに更新され、流量データは 2 分ごとに更新されます。

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE))
}
```

::: zone-end

![Azure Maps の交通情報](media/migrate-google-maps-android-app/azure-maps-traffic.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

クリーンアップすべきリソースはありません。

## <a name="next-steps"></a>次のステップ

Azure Maps の移行について理解を深めます。

> [!div class="nextstepaction"]
> [Android アプリを移行する](migrate-from-google-maps-android-app.md)
