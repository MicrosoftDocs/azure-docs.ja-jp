---
title: Azure Maps のマップ スタイル機能 | Microsoft Docs
description: Android SDK の機能に関連した Azure Maps スタイルについて説明します。
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 5bcc73c63e7c9d804d01df98551aa51b81d98d07
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844850"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Azure Maps Android SDK を使用したマップ スタイルの設定

この記事では、Azure Maps Android SDK を使用してマップ スタイルを設定する 2 つの方法を示します。 Azure Maps は、6 つの異なるマップ スタイルから選択できます。 サポートされているマップ スタイルの詳細については、「[Azure Maps でのサポートされているマップ スタイル](./supported-map-styles.md)」を参照してください。


## <a name="prerequisites"></a>前提条件

この記事のプロセスを完了するには、[Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) をインストールしてマップを読み込む必要があります。


## <a name="set-map-style-in-the-layout"></a>レイアウトでのマップ スタイルの設定

マップ スタイルは、アクティビティ クラスのレイアウト ファイルで設定できます。 **res > layout > activity_main.xml** を編集すると、次のようになります。

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
        app:mapcontrol_centerLat="47.602806"
        app:mapcontrol_centerLng="-122.329330"
        app:mapcontrol_zoom="12"
        app:mapcontrol_style="grayscale_dark"
        />

</FrameLayout>
```

`mapcontrol_style`上記の属性はマップ スタイルを **grayscale_dark** に設定します。 

<center>

![style-grayscale_dark](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>アクティビティ クラスでのマップ スタイルの設定

マップ スタイルはアクティビティ クラスで設定できます。 次のコード スニペットを **onCreate()** method of your `MainActivity.java` クラスにコピーします。 これによりマップ スタイルは **satellite_road_labels** に設定されます。

```Java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![style-satellite-road-labels](./media/set-android-map-styles/satellite-road-labels.png)</center>