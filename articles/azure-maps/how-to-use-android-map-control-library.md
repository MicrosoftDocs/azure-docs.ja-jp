---
title: Azure Maps で Android マップ コントロールを使用する方法 | Microsoft Docs
description: Azure Maps の Android マップ コントロール。
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 15706addbe6b7f6310223978130158c792a47c89
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010669"
---
# <a name="how-to-use-the-azure-maps-android-sdk"></a>Azure Maps Android SDK を使用する方法

Azure Maps Android SDK は、Android 用のベクター マップ ライブラリです。 この記事では、Azure Maps Android SDK のインストール、マップの読み込み、マップでのピンの配置のプロセスについて説明します。

## <a name="prerequisites"></a>前提条件

### <a name="create-an-azure-maps-account"></a>Azure Maps アカウントを作成する

この記事の手順を完了するには、まず、S1 価格レベルで [Azure Maps アカウントを作成する](how-to-manage-account-keys.md)必要があります。

### <a name="download-android-studio"></a>Android Studio をダウンロードする

Azure Maps Android SDK をインストールするには、Android Studio をダウンロードし、空のアクティビティを含むプロジェクトを作成する必要があります。 Google から無料で [Android Studio をダウンロードする](https://developer.android.com/studio/)ことができます。 

## <a name="create-a-project-in-android-studio"></a>Android Studio でプロジェクトを作成する

まず、空のアクティビティを含む新しいプロジェクトを作成する必要があります。 Android Studio プロジェクトを作成するには、次の手順を完了します。

1. **[Choose your project]\(プロジェクトの選択\)** で、**[Phone and Tablet]\(電話およびタブレット\)** を選択します。 このフォーム ファクターでアプリケーションが実行されます。
2. **[Phone and Tablet]\(電話およびタブレット\)** タブで、**[Empty Activity]\(空のアクティビティ\)**、**[次へ]** の順に選択します。
3. **[Configure your project]\(プロジェクトの構成\)** で、最小限の SDK として `API 21: Android 5.0.0 (Lollipop)` を選択します。 これは Azure Maps Android SDK でサポートされる最も古いバージョンです。
4. 既定値の `Activity Name` と `Layout Name` を受け入れ、**[完了]** を選択します。

Android Studio のインストールと新しいプロジェクトの作成の詳細については、[Android Studio のドキュメント](https://developer.android.com/studio/intro/)を参照してください。

![プロジェクトの作成](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>仮想デバイスを設定する

Android Studio を使用すると、コンピューター上で仮想 Android デバイスを設定できます。 このようにすると、開発中のアプリケーションのテストに役立つ場合があります。 仮想デバイスを設定するには、プロジェクト画面の右上隅にある Android 仮想デバイス (AVD) アイコンを選び、**[仮想デバイスの作成]** を選択します。 ツールバーで **[ツール]** > **[Android]** > **[AVD マネージャー]** の順に選択して、AVD マネージャーに移動することもできます。 **[Phones]\(電話\)** カテゴリで **[Nexus 5X]**、**[次へ]** の順に選択します。

AVD の設定の詳細については、[Android Studio のドキュメント](https://developer.android.com/studio/run/managing-avds)を参照してください。

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Azure Maps Android SDK をインストールする

アプリケーションのビルドの次の手順は、Azure Maps Android SDK をインストールすることです。 SDK をインストールするには、以下の手順を完了します。

1. **build.gradle** ファイルの**リポジトリ** ブロックである **all projects** に以下のコードを追加します。

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. **app/build.gradle** を更新し、以下のコードを追加します。

    1. Android ブロックに以下のコードを追加します。

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    2. 依存関係ブロックを更新し、以下のコードを追加します。

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.1"
        ```

3. **AndroidManifest.xml** ファイルに以下の XML を追加して、アクセス許可を設定します。

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <manifest>
        ...
        <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
        ...
    </manifest>
    ```

4. 次のような XML になるように、**res** > **layout** > **activity_main.xml** を編集します。
    
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
            app:mapcontrol_cameraTargetLat="47.64"
            app:mapcontrol_cameraTargetLng="-122.33"
            app:mapcontrol_cameraZoom="12"
            />

    </FrameLayout>
    ```

5. **MainActivity.java** を編集して、マップ ビュー アクティビティ クラスを作成します。 編集後は、以下のクラスのようになります。

    ```java
    package com.example.myapplication;

    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {
        
        static {
            AzureMaps.setSubscriptionKey("{subscription-key}");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);

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

## <a name="import-classes"></a>クラスをインポートする

上記の手順を完了した後、一部のコードについて、Android Studio に警告が表示される場合があります。 これらの警告を解決するには、`MainActivity.java` で参照されているクラスをインポートします。

Alt + Enter (Mac の場合は Option + Return) を選択して、これらのクラスを自動的にインポートできます。

以下の図に示すように、実行ボタンを選択して (Mac の場合は Control + R キーを押す)、アプリケーションをビルドします。

![[実行] をクリックします。](./media/how-to-use-android-map-control-library/run-app.png)

Android Studio でアプリケーションをビルドするには、数秒かかります。 ビルドが完了した後、エミュレートされた Android デバイスでアプリケーションをテストできます。 次のようなマップが表示されます。

![Android のマップ](./media/how-to-use-android-map-control-library/android-map.png)

## <a name="add-a-marker-to-the-map"></a>マップにマーカーを追加する

マップにマーカーを追加するには、`MainActivity.java` に `mapView.getMapAsync()` 関数を追加します。 最終的な `MainActivity.java` コードは、次のようになります。

```java
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
            AzureMaps.setSubscriptionKey("{subscription-key}");
        }

    MapControl mapControl;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mapControl = findViewById(R.id.mapcontrol);

        mapControl.onCreate(savedInstanceState);

        mapControl.getMapAsync(map -> {
            DataSource dataSource = new DataSource();
            dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

            SymbolLayer symbolLayer = new SymbolLayer(dataSource);
            symbolLayer.setOptions(iconImage("my-icon"));

            map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
            map.sources.add(dataSource);
            map.layers.add(symbolLayer);
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

アプリケーションをもう一度実行します。 次のように、マップにマーカーが表示されます。

![Android のマップ ピン](./media/how-to-use-android-map-control-library/android-map-pin.png)