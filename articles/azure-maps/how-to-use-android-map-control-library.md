---
title: Android マップ コントロールの概要 | Microsoft Azure Maps
description: Azure Maps Android SDK について理解を深めます。 Android Studio でプロジェクトを作成する方法、SDK をインストールする方法、および対話型のマップを作成する方法を確認します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: fca1b48911ff2af977b8415ad1d9ec1cc2801623
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037764"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Azure Maps Android SDK の概要

Azure Maps Android SDK は、Android 用のベクター マップ ライブラリです。 この記事では、Azure Maps Android SDK のインストールおよびマップの読み込みのプロセスを説明します。

## <a name="prerequisites"></a>前提条件

### <a name="create-an-azure-maps-account"></a>Azure Maps アカウントを作成する

この記事の手順を完了するには、まず、S1 価格レベルで [Azure Maps アカウントを作成](quick-demo-map-app.md#create-an-azure-maps-account)し、アカウントの[主キーを取得](quick-demo-map-app.md#get-the-primary-key-for-your-account)する必要があります。

Azure Maps での認証の詳細については、「[Azure Maps での認証の管理](./how-to-manage-authentication.md)」を参照してください。

### <a name="download-android-studio"></a>Android Studio をダウンロードする

Azure Maps Android SDK をインストールする前に、Android Studio をダウンロードして、空のアクティビティを含むプロジェクトを作成します。 Google から無料で [Android Studio をダウンロードする](https://developer.android.com/studio/)ことができます。 

## <a name="create-a-project-in-android-studio"></a>Android Studio でプロジェクトを作成する

まず、空のアクティビティを含む新しいプロジェクトを作成します。 Android Studio プロジェクトを作成するには、次の手順を完了します。

1. **[Choose your project]\(プロジェクトの選択\)** で、 **[Phone and Tablet]\(電話およびタブレット\)** を選択します。 このフォーム ファクターでアプリケーションが実行されます。
2. **[Phone and Tablet]\(電話およびタブレット\)** タブで、 **[Empty Activity]\(空のアクティビティ\)** 、 **[次へ]** の順に選択します。
3. **[Configure your project]\(プロジェクトの構成\)** で、最小限の SDK として `API 21: Android 5.0.0 (Lollipop)` を選択します。 これは Azure Maps Android SDK でサポートされる最も古いバージョンです。
4. 既定値の `Activity Name` と `Layout Name` を受け入れ、 **[完了]** を選択します。

Android Studio のインストールと新しいプロジェクトの作成の詳細については、[Android Studio のドキュメント](https://developer.android.com/studio/intro/)を参照してください。

![Android Studio でプロジェクトを作成する ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>仮想デバイスを設定する

Android Studio を使用すると、コンピューター上で仮想 Android デバイスを設定できます。 このようにすると、開発中のアプリケーションのテストに役立つ場合があります。 仮想デバイスを設定するには、プロジェクト画面の右上隅にある Android 仮想デバイス (AVD) アイコンを選び、 **[仮想デバイスの作成]** を選択します。 ツールバーで **[ツール]**  >  **[Android]**  >  **[AVD マネージャー]** の順に選択して、AVD マネージャーに移動することもできます。 **[Phones]\(電話\)** カテゴリで **[Nexus 5X]** 、 **[次へ]** の順に選択します。

AVD の設定の詳細については、[Android Studio のドキュメント](https://developer.android.com/studio/run/managing-avds)を参照してください。

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Azure Maps Android SDK をインストールする

アプリケーションのビルドの次の手順は、Azure Maps Android SDK をインストールすることです。 SDK をインストールするには、以下の手順を完了します。

1. 最上位の **build.gradle** ファイルを開き、次のコードを**すべてのプロジェクト**の**リポジトリ** ブロック セクションに追加します。

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. **app/build.gradle** を更新し、以下のコードを追加します。
    
    1. プロジェクトの **minSdkVersion** が API 21 以降であることを確認します。

    2. Android セクションに、次のコードを追加します。

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. 依存関係のブロックを更新し、最新の Azure Maps Android SDK の新しい実装の依存関係の行を追加します。

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.4"
        ```
    
    4. ツール バーの **[ファイル]** に移動し、 **[Sync Project with Gradle Files]\(プロジェクトを Gradle ファイルと同期\)** をクリックします。
3. メイン アクティビティにマップ フラグメントを追加します (res \> layout \> activity\_main.xml)。
    
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

4. **MainActivity.java** ファイルで、以下の操作を行う必要があります。
    
    * Azure Maps SDK の imports を追加する
    * Azure Maps の認証情報を設定する
    * **onCreate** メソッドでマップ コントロールのインスタンスを取得する

    `setSubscriptionKey` または `setAadProperties` メソッドを使用して `AzureMaps` クラスにグローバルに認証情報を設定すると、すべてのビューで認証情報を追加する必要がなくなります。 

    マップ コントロールには、Android の OpenGL ライフサイクルを管理するための独自のライフサイクル メソッドが含まれています。 これらのライフサイクル メソッドは、それが含まれているアクティビティから直接呼び出す必要があります。 アプリでマップ コントロールのライフサイクル メソッドを正しく呼び出すためには、マップ コントロールを含むアクティビティで次のライフサイクル メソッドをオーバーライドする必要があります。 また、それぞれのマップ コントロール メソッドを呼び出す必要があります。 

    * onCreate(Bundle) 
    * onStart() 
    * onResume() 
    * onPause() 
    * onStop() 
    * onDestroy() 
    * onSaveInstanceState(Bundle) 
    * onLowMemory() 

    **MainActivity.java** ファイルを次のように編集します。
    
    ```java
    package com.example.myapplication;

    //For older versions use: import android.support.v7.app.AppCompatActivity; 
    import androidx.appcompat.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {
        
        static {
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
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
        }
    }
    ```

## <a name="import-classes"></a>クラスをインポートする

上記の手順を完了した後、一部のコードについて、Android Studio に警告が表示される場合があります。 これらの警告を解決するには、`MainActivity.java` で参照されているクラスをインポートします。

Alt + Enter (Mac の場合は Option + Return) を選択して、これらのクラスを自動的にインポートできます。

以下の図に示すように、実行ボタンを選択して (Mac の場合は Control + R キーを押す)、アプリケーションをビルドします。

![[実行] をクリックします。](./media/how-to-use-android-map-control-library/run-app.png)

Android Studio でアプリケーションをビルドするには、数秒かかります。 ビルドが完了した後、エミュレートされた Android デバイスでアプリケーションをテストできます。 次のようなマップが表示されます。

<center>

![Android アプリケーションでの Azure Maps](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="localizing-the-map"></a>マップのローカライズ

Azure Maps Android SDK には、マップの言語と地域ビューを設定するための 3 つの異なる方法が用意されています。 次のコードは、言語をフランス語 ("fr-FR") に、地域ビューを "auto" に設定する方法を示しています。 

最初のオプションでは、静的な `setLanguage` メソッドと `setView` メソッドをグローバルに使用して、言語とビューの地域情報を `AzureMaps` クラスに渡します。 これにより、アプリに読み込まれたすべての Azure Maps コントロールに対して、既定の言語と地域ビューが設定されます。

```Java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("auto");
}
```

2 つ目のオプションでは、言語とビュー情報をマップ コントロール XML に渡します。

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

3 つ目のオプションでは、マップの `setStyle` メソッドを使用して、マップの言語と地域ビューをプログラムで設定します。 これは、マップの言語と地域ビューを変更するときにいつでも実行できます。

```Java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

言語が "fr-FR" に設定され、地域ビューが "auto" に設定されている Azure Maps の例を次に示します。

<center>

![Azure Maps、ラベルをフランス語で表示するマップ イメージ](./media/how-to-use-android-map-control-library/android-localization.png)
</center>

サポートされている言語と地域ビューの完全な一覧については、[こちら](supported-languages.md)をご覧ください。

## <a name="navigating-the-map"></a>マップ内を移動する

マップをズーム、パン、回転、およびピッチ調整する方法は複数存在します。 以下では、マップ内を移動するときのさまざまな方法をすべて詳しく説明します。

**マップをズームする**

- 2 本の指でマップをタッチし、指でつまむようにすると縮小し、指を広げると拡大します。
- マップをダブルタップし、1 レベル分拡大します。
- 2本の指でダブルタップし、マップを 1 レベル分縮小します。
- 2 回タップします。2 回目のタップで指をマップに置いたままにして、上にドラッグすると拡大し、下にドラッグすると縮小します。

**マップをパンする**

- マップをタッチし、任意の方向にドラッグします。

**マップを回転する**

- 2 本の指でマップをタッチし、回転します。

**マップのピッチを調整する**

- 2 本の指でマップをタッチして、上または下にドラッグします。

## <a name="next-steps"></a>次のステップ

マップにオーバーレイ データを追加する方法を確認します。

> [!div class="nextstepaction"]
> [Android マップへのシンボル レイヤーの追加](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Android マップへの図形の追加](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Android マップでのマップ スタイルの変更](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)
