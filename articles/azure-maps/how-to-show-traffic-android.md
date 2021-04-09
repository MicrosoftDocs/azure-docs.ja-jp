---
title: Android マップに交通情報データを表示する | Microsoft Azure Maps
description: この記事では、Microsoft Azure Maps Android SDK を使用して、マップに交通情報データを表示する方法について説明します。
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 36b3666f12b48468467e76f4c281d58d8018478c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102098538"
---
# <a name="show-traffic-data-on-the-map-android-sdk"></a>マップに交通情報データを表示する (Android SDK)

フロー データとインシデント データは、マップに表示できる 2 種類の交通情報データです。 このガイドでは、両方の種類の交通情報データを表示する方法について説明します。 インシデント データは、工事、道路閉鎖、交通事故といった事柄を示す、点と線に基づくデータで構成されます。 フロー データは、道路上の交通の流れに関するメトリックを示します。

## <a name="prerequisites"></a>前提条件

[クイックスタート: Android アプリの作成](quick-android-map.md)に関する記事の手順を必ず完了してください。 この記事のコード ブロックは、マップ `onReady` イベント ハンドラーに挿入できます。

## <a name="show-traffic-on-the-map"></a>マップ上にトラフィックを表示する

Azure Maps で使用できるトラフィック データには、次の 2 種類があります。

- インシデント データ - 建設、道路の閉鎖、事故などのためのポイント データと行ベースのデータで構成されます。
- フロー データ - 道路のトラフィックのフローに関するメトリックを提供します。 多くの場合、トラフィック フロー データは、道路の色を設定するために使用されます。 その色は、速度制限やその他のメトリックと相対的な、フロー速度が低下するトラフィック量に基づいています。 マップの交通情報 `flow` オプションに渡すことができる値は 4 つあります。

    |Flow 値 | 説明|
    | :-- | :-- |
    | TrafficFlow.NONE | マップに交通情報データを表示しません |
    | TrafficFlow.RELATIVE | 道路の自由流速度を基準にして交通情報データを表示します |
    | TrafficFlow.RELATIVE_DELAY | 予想されている平均的な遅れよりも遅い地域を表示します |
    | TrafficFlow.ABSOLUTE | 道路上の全車両の対地速度を表示します |

次のコードは、トラフィック データをマップに表示する方法を示しています。

::: zone pivot="programming-language-java-android"

```java
//Show traffic on the map using the traffic options.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
)
```

::: zone-end

次のスクリーンショットは、上記のコードによるリアルタイムの交通情報のマップ上へのレンダリングを示しています。

![リアルタイムの交通情報を示すマップ](media/how-to-show-traffic-android/android-show-traffic.png)

## <a name="get-traffic-incident-details"></a>交通情報インシデントの詳細を取得する

交通情報インシデントの詳細は、マップ上にインシデントを表示するために使用される機能のプロパティ内で使用できます。 交通情報インシデントは、Azure Maps 交通情報インシデント ベクター タイル サービスを使用してマップに追加されます。 これらのベクター タイル内のデータの形式は、[こちらに記載されています](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles)。 次のコードは、マップにクリック イベントを追加し、クリックされた交通情報インシデント機能を取得して、詳細の一部を含むトースト メッセージを表示します。

::: zone pivot="programming-language-java-android"

```java
//Show traffic information on the map.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);

//Add a click event to the map.
map.events.add((OnFeatureClick) (features) -> {

    if (features != null && features.size() > 0) {
        Feature incident = features.get(0);

        //Ensure that the clicked feature is an traffic incident feature.
        if (incident.properties() != null && incident.hasProperty("incidentType")) {

            StringBuilder sb = new StringBuilder();
            String incidentType = incident.getStringProperty("incidentType");

            if (incidentType != null) {
                sb.append(incidentType);
            }

            if (sb.length() > 0) {
                sb.append("\n");
            }

            //If the road is closed, find out where it is closed from.
            if ("Road Closed".equals(incidentType)) {
                String from = incident.getStringProperty("from");

                if (from != null) {
                    sb.append(from);
                }
            } else {
                //Get the description of the traffic incident.
                String description = incident.getStringProperty("description");

                if (description != null) {
                    sb.append(description);
                }
            }

            String message = sb.toString();

            if (message.length() > 0) {
                Toast.makeText(this, message, Toast.LENGTH_LONG).show();
            }
        }
    }
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Show traffic information on the map.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
)

//Add a click event to the map.
map.events.add(OnFeatureClick { features: List<Feature>? ->
    if (features != null && features.size > 0) {
        val incident = features[0]

        //Ensure that the clicked feature is an traffic incident feature.
        if (incident.properties() != null && incident.hasProperty("incidentType")) {
            val sb = StringBuilder()
            val incidentType = incident.getStringProperty("incidentType")

            if (incidentType != null) {
                sb.append(incidentType)
            }

            if (sb.length > 0) {
                sb.append("\n")
            }

            //If the road is closed, find out where it is closed from.
            if ("Road Closed" == incidentType) {
                val from = incident.getStringProperty("from")
                if (from != null) {
                    sb.append(from)
                }
            } else { //Get the description of the traffic incident.
                val description = incident.getStringProperty("description")
                if (description != null) {
                    sb.append(description)
                }
            }

            val message = sb.toString()
            if (message.length > 0) {
                Toast.makeText(this, message, Toast.LENGTH_LONG).show()
            }
        }
    }
})
```

::: zone-end

次のスクリーンショットは、上記のコードによるリアルタイムの交通情報のマップ上へのレンダリングを示し、インシデントの詳細を表示するトースト メッセージも表示されています。

![インシデントの詳細を表示するトースト メッセージ付きでリアルタイムの交通情報を示すマップ](media/how-to-show-traffic-android/android-traffic-details.png)

## <a name="next-steps"></a>次のステップ

マップにデータを追加する方法については、次のガイドを参照してください。

> [!div class="nextstepaction"]
> [タイル レイヤーを追加する](how-to-add-tile-layer-android-map.md)
