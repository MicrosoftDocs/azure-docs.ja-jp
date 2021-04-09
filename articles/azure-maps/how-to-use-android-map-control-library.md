---
title: Android マップ コントロールの概要 | Microsoft Azure Maps
description: Azure Maps Android SDK について理解を深めます。 Android Studio でプロジェクトを作成する方法、SDK をインストールする方法、および対話型のマップを作成する方法を確認します。
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 5888a5f34ef65fc1015b6e73af1d03368a8329b2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102098521"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Azure Maps Android SDK の概要

Azure Maps Android SDK は、Android 用のベクター マップ ライブラリです。 この記事では、Azure Maps Android SDK のインストールおよびマップの読み込みのプロセスを説明します。

## <a name="prerequisites"></a>前提条件

必ず、[クイック スタート:Android アプリの作成](quick-android-map.md)に関するドキュメントの手順を完了してください。

## <a name="localizing-the-map"></a>マップのローカライズ

Azure Maps Android SDK には、マップの言語と地域ビューを設定するための 3 つの異なる方法が用意されています。 次のコードは、言語をフランス語 ("fr-FR") に、地域ビューを "Auto" に設定する方法を示しています。

最初のオプションでは、静的な `setLanguage` メソッドと `setView` メソッドをグローバルに使用して、言語とビューの地域情報を `AzureMaps` クラスに渡します。 これにより、アプリに読み込まれたすべての Azure Maps コントロールに対して、既定の言語と地域ビューが設定されます。

::: zone pivot="programming-language-java-android"

```java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Alternatively use Azure Active Directory authenticate.
    //AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");

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

        //Alternatively use Azure Active Directory authenticate.
        //AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");
    
        //Set the language to be used by Azure Maps.
        AzureMaps.setLanguage("fr-FR");
    
        //Set the regional view to be used by Azure Maps.
        AzureMaps.setView("Auto");
    }
}
```

::: zone-end

2 つ目のオプションでは、言語とビュー情報をマップ コントロール XML に渡します。

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="Auto"
    />
```

3 つ目のオプションでは、マップの `setStyle` メソッドを使用して、マップの言語と地域ビューをプログラムで設定します。 これは、マップの言語と地域ビューを変更するときにいつでも実行できます。

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
mapControl.onReady(OnReady { map: AzureMap ->
    map.setStyle(
        language("fr-FR"),
        view("Auto")
    )
})
```

::: zone-end

言語が "fr-FR" に設定され、地域ビューが "Auto" に設定されている Azure Maps の例を次に示します。

![Azure Maps、ラベルをフランス語で表示するマップ イメージ](media/how-to-use-android-map-control-library/android-localization.png)

サポートされている言語と地域ビューの完全な一覧については、[こちら](supported-languages.md)をご覧ください。

## <a name="navigating-the-map"></a>マップ内を移動する

マップをズーム、パン、回転、およびピッチ調整する方法は複数存在します。 以下では、マップ内を移動するときのさまざまな方法をすべて詳しく説明します。

**マップをズームする**

* 2 本の指でマップをタッチし、指でつまむようにすると縮小し、指を広げると拡大します。
* マップをダブルタップし、1 レベル分拡大します。
* 2 本の指でダブル タップし、マップを 1 レベル分縮小します。
* 2 回タップします。2 回目のタップで指をマップに置いたままにして、上にドラッグすると拡大し、下にドラッグすると縮小します。

**マップをパンする**

* マップをタッチし、任意の方向にドラッグします。

**マップを回転する**

* 2 本の指でマップをタッチし、回転します。

**マップのピッチを調整する**

* 2 本の指でマップをタッチして、上または下にドラッグします。

## <a name="azure-government-cloud-support"></a>Azure Government クラウドのサポート

Azure Maps Android SDK では、Azure Government クラウドがサポートされています。 Azure Maps Android SDK には、同じ Maven リポジトリからアクセスします。 Azure Maps プラットフォームの Azure Government cloud バージョンに接続するには、次のタスクを実行する必要があります。

Azure Maps 認証の詳細が指定されているのと同じ場所で、次のコード行を追加して、Azure Maps Government クラウド ドメインを使用するようにマップに指示します。

::: zone pivot="programming-language-java-android"

```java
AzureMaps.setDomain("atlas.azure.us");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
AzureMaps.setDomain("atlas.azure.us")
```

::: zone-end

マップとサービスを認証するときは、Azure Government クラウド プラットフォームからの Azure Maps 認証の詳細を必ず使用してください。

## <a name="next-steps"></a>次のステップ

マップにオーバーレイ データを追加する方法を確認します。

> [!div class="nextstepaction"]
> [Azure Maps での認証の管理](how-to-manage-authentication.md)

> [!div class="nextstepaction"]
> [Android マップでのマップ スタイルの変更](set-android-map-styles.md)

> [!div class="nextstepaction"]
> [シンボル レイヤーを追加する](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [線レイヤーを追加する](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [多角形レイヤーを追加する](how-to-add-shapes-to-android-map.md)
