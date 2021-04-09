---
title: Azure IoT Central ソリューションで位置データを使用する
description: IoT Central アプリケーションに接続されているデバイスから送信された位置データを使用する方法について説明します。 マップ上に位置データをプロットするか、ジオフェンシング規則を作成します。
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: c909fd1438488e3625f3674dd26f959cf6fad79f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98127974"
---
# <a name="use-location-data-in-an-azure-iot-central-solution"></a>Azure IoT Central ソリューションで位置データを使用する

この記事では、IoT Central アプリケーションで位置データを使用する方法について説明します。 デバイスを IoT Central に接続すると、位置データをテレメトリ ストリームとして送信したり、デバイス プロパティを使用して位置データを報告したりできます。

ソリューション ビルダーで位置データを使用して、次のことを行うことができます。

* 報告された位置をマップ上にプロットします。
* テレメトリ位置履歴をマップ上にプロットします。
* 特定の領域に対するデバイスの出入りのタイミングをオペレーターに通知するジオフェンシング規則を作成します。

## <a name="add-location-capabilities-to-a-device-template"></a>デバイス テンプレートに位置機能を追加する

次のスクリーンショットに、デバイス テンプレートを示します。これには位置データを使用するデバイス プロパティおよびテレメトリの種類の例が含まれています。 定義には、**location** セマンティック型と **geolocation** スキーマ型が使用されています。

:::image type="content" source="media/howto-use-location-data/location-device-template.png" alt-text="デバイス テンプレートでの位置プロパティの定義を示すスクリーンショット":::

参考までに、これらの機能の [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) 定義は、次のスニペットのようになります。

```json
{
  "@type": [
    "Property",
    "Location"
  ],
  "displayName": {
    "en": "DeviceLocation"
  },
  "name": "DeviceLocation",
  "schema": "geopoint",
  "writable": false
},
{
  "@type": [
    "Telemetry",
    "Location"
  ],
  "displayName": {
    "en": "Tracking"
  },
  "name": "Tracking",
  "schema": "geopoint"
}
```

> [!NOTE]
> **geopoint** スキーマ型は、[DTDL 仕様](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)に含まれていません。 IoT Central では、現在、下位互換性のために **geopoint** スキーマ型と **location** セマンティック型がサポートされています。

## <a name="send-location-data-from-a-device"></a>デバイスから位置データを送信する

前のセクションに示されている **DeviceLocation** プロパティ用のデータをデバイスから送信する場合、ペイロードは次の JSON スニペットのようになります。

```json
{
  "DeviceLocation": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

前のセクションに示されている **Tracking** テレメトリ用のデータをデバイスから送信する場合、ペイロードは次の JSON スニペットのようになります。

```json
{
  "Tracking": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

## <a name="display-device-location"></a>デバイスの位置を表示する

ご利用の IoT Central アプリケーション内の複数の場所に位置データを表示することができます。 例として、個々のデバイスに関連付けられたビューや、ダッシュボードが挙げられます。

デバイスに関するビューを作成する場合は、マップ上に位置情報をプロットすることも、個々の値を表示することもできます。

:::image type="content" source="media/howto-use-location-data/location-views.png" alt-text="位置データが含まれるビューの例を示したスクリーンショット":::

ダッシュボードにマップ タイルを追加することで、1 台または複数台のデバイスの位置をプロットすることができます。 位置テレメトリを表示するマップ タイルを追加すると、一定期間にわたって位置をプロットできます。 次のスクリーンショットに、過去 30 分間にわたってシミュレートされたデバイスから報告された位置を示します。

:::image type="content" source="media/howto-use-location-data/location-dashboard.png" alt-text="位置データを含むダッシュボードの例を示すスクリーンショット":::

## <a name="create-a-geofencing-rule"></a>ジオフェンシング規則を作成する

位置テレメトリを使用すれば、デバイスが四角形の領域に入ったとき、またはそこから出たときにアラートを生成するジオフェンシング規則を作成できます。 次のスクリーンショットに示す規則では、緯度および経度の値を使用して四角形の領域を定義するために 4 つの条件を使用しています。 デバイスが四角形の領域に移動すると、この規則に従って電子メールが生成されます。

:::image type="content" source="media/howto-use-location-data/geofence-rule.png" alt-text="ジオフェンシング規則の定義を示すスクリーンショット":::

## <a name="next-steps"></a>次の手順

Azure IoT Central アプリケーションでプロパティを使用する方法を習得したら、次は以下を参照してください。

* [Payloads](concepts-telemetry-properties-commands.md)
* [クライアント アプリケーションを作成して Azure IoT Central アプリケーションに接続する](tutorial-connect-device.md)