---
title: Azure Event Grid の Azure Maps 用のイベント スキーマ
description: Azure Event Grid で Azure Maps のイベント用に提供されているプロパティとスキーマについて説明します
services: event-grid
author: walsehgal
ms.service: event-grid
ms.topic: reference
ms.date: 02/08/2019
ms.author: v-musehg
ms.openlocfilehash: 74a3674e632f8dc3f0755bc2ad48376708c7966f
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2019
ms.locfileid: "56007660"
---
# <a name="azure-event-grid-event-schema-for-azure-maps"></a>Azure Maps 用の Azure Event Grid イベント スキーマ

この記事では、Azure Maps のイベントのプロパティとスキーマについて説明します。 イベント スキーマの概要については、「[Azure Event Grid イベント スキーマ](https://docs.microsoft.com/azure/event-grid/event-schema)」を参照してください。

## <a name="available-event-types"></a>使用可能なイベントの種類

Azure Maps アカウントから出力されるイベントの種類は次のとおりです。

| イベントの種類 | 説明 |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | 受信した座標が指定されたジオフェンスの外部から内部に移動したときに発生します |
| Microsoft.Maps.GeofenceExited | 受信した座標が指定されたジオフェンスの内部から外部に移動したときに発生します |
| Microsoft.Maps.GeofenceResult | 状態に関係なく、ジオフェンシング クエリから結果が返されるたびに発生します |

## <a name="event-examples"></a>イベントの例

次の例では、**GeofenceEntered** イベントのスキーマを示します

```JSON
{   
   "id":"7f8446e2-1ac7-4234-8425-303726ea3981", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":-999.0, 
            "nearestLat":47.618786, 
            "nearestLon":-122.132151 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceEntered", 
   "eventTime":"2018-11-08T00:54:17.6408601Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

次の例では、**GeofenceResult** のスキーマを示します 

```JSON
{   
   "id":"451675de-a67d-4929-876c-5c2bf0b2c000", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"1", 
            "distance":999.0, 
            "nearestLat":47.609833, 
            "nearestLon":-122.148274 
         }, 
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":999.0, 
            "nearestLat":47.621954, 
            "nearestLon":-122.131841 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceResult", 
   "eventTime":"2018-11-08T00:52:08.0954283Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

## <a name="event-properties"></a>イベントのプロパティ

イベントのトップレベルのデータを次に示します。

| プロパティ | type | 説明 |
| -------- | ---- | ----------- |
| topic | 文字列 | イベント ソースの完全なリソース パス。 このフィールドは書き込み可能ではありません。 この値は Event Grid によって指定されます。 |
| subject | 文字列 | 発行元が定義したイベントの対象のパス。 |
| eventType | 文字列 | このイベント ソース用に登録されたイベントの種類のいずれか。 |
| eventTime | 文字列 | プロバイダーの UTC 時刻に基づくイベントの生成時刻。 |
| id | 文字列 | イベントの一意識別子。 |
| data | オブジェクト | ジオフェンシングのイベント データ。 |
| dataVersion | 文字列 | データ オブジェクトのスキーマ バージョン。 スキーマ バージョンは発行元によって定義されます。 |
| metadataVersion | 文字列 | イベント メタデータのスキーマ バージョン。 最上位プロパティのスキーマは Event Grid によって定義されます。 この値は Event Grid によって指定されます。 |

データ オブジェクトには、次のプロパティがあります。

| プロパティ | type | 説明 |
| -------- | ---- | ----------- |
| apiCategory | 文字列 | イベントの API カテゴリ。 |
| apiName | 文字列 | イベントの API 名。 |
| issues | オブジェクト | 処理中に発生した問題の一覧を示します。 何らかの問題が返された場合、応答ではジオメトリが返されません。 |
| responseCode | number | HTTP 応答コード |
| geometries | オブジェクト | 座標が含まれるフェンス ジオメトリ、または位置の周りに searchBuffer が重なっているフェンス ジオメトリの一覧を示します。 |

Maps API でエラーが発生すると、エラー オブジェクトが返されます。 エラー オブジェクトには、次のプロパティがあります。

| プロパティ | type | 説明 |
| -------- | ---- | ----------- |
| error | ErrorDetails |Maps API でエラーが発生すると、このオブジェクトが返されます  |

Maps API でエラーが発生すると、ErrorDetails オブジェクトが返されます。 ErrorDetails オブジェクトには、次のプロパティがあります。

| プロパティ | type | 説明 |
| -------- | ---- | ----------- |
| code | 文字列 | HTTP 状態コード。 |
| message | 文字列 | ある場合、人間が判読できるエラーの説明。 |
| innererror | InnerError | ある場合、エラーに関するサービス固有の情報が含まれるオブジェクト。 |

InnerError は、エラーに関するサービス固有の情報が含まれるオブジェクトです。 InnerError オブジェクトには、次のプロパティがあります。 

| プロパティ | type | 説明 |
| -------- | ---- | ----------- |
| code | 文字列 | エラー メッセージ。 |

geometries オブジェクト。要求のユーザー時刻を基準にして有効期限が切れているジオフェンスのジオメトリ ID の一覧です。 geometries オブジェクトには、次のプロパティを含むジオメトリ項目があります。 

| プロパティ | type | 説明 |
|:-------- |:---- |:----------- |
| deviceid | 文字列 | デバイスの ID。 |
| distance | 文字列 | <p>座標からジオフェンスの最も近い境界までの距離。 正の値は、座標がジオフェンスの外部にあることを意味します。 座標がジオフェンスの外部にあっても、最も近いジオフェンスの境界から searchBuffer の値より大きく離れている場合、値は 999 になります。 負の値は、座標がジオフェンスの内部にあることを意味します。 座標がポリゴンの内部にあっても、最も近いジオフェンスの境界から searchBuffer の値より大きく離れている場合、値は -999 になります。 値 999 は、座標がジオフェンスの外部にあることが十分に確実であることを意味します。 値 -999 は、座標がジオフェンスの内部にあることが十分に確実であることを意味します。<p> |
| geometryid |文字列 | ジオフェンスのジオメトリを示す一意 ID。 |
| nearestlat | number | ジオメトリの最も近いポイントの緯度。 |
| nearestlon | number | ジオメトリの最も近いポイントの経度。 |
| udId | 文字列 | ジオフェンスをアップロードしたときに、ユーザーのアップロード サービスから返される一意 ID。 Geofencing POST API には含まれません。 |

データ オブジェクトには、次のプロパティがあります。

| プロパティ | type | 説明 |
| -------- | ---- | ----------- |
| expiredGeofenceGeometryId | string[] | 要求でのユーザー時刻を基準にして期限切れになっているジオフェンスのジオメトリ ID の一覧。 |
| geometries | geometries[] |座標が含まれるフェンス ジオメトリ、または位置の周りに searchBuffer が重なっているフェンス ジオメトリの一覧を示します。 |
| invalidPeriodGeofenceGeometryId | string[]  | 要求でのユーザー時刻を基準にして無効期間内になっているジオフェンスのジオメトリ ID の一覧。 |
| isEventPublished | ブール値 | Azure Maps イベント サブスクライバーに対して少なくとも 1 つのイベントが発行されている場合は true、Azure Maps イベント サブスクライバーに対してイベントが発行されていない場合は false。 |

## <a name="next-steps"></a>次の手順

* Azure Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Azure Event Grid サブスクリプションの作成の詳細については、[Event Grid サブスクリプション スキーマ](subscription-creation-schema.md)に関する記事を参照してください。