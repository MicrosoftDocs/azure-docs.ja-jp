---
title: Event Grid ソースとしての Azure Maps
description: Azure Event Grid で Azure Maps のイベント用に提供されているプロパティとスキーマについて説明します
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 88cf0c8274d685a45862bc7b7884b5e4a686c22d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100363681"
---
# <a name="azure-maps-as-an-event-grid-source"></a>Event Grid ソースとしての Azure Maps

この記事では、Azure Maps のイベントのプロパティとスキーマについて説明します。 イベント スキーマの概要については、「[Azure Event Grid イベント スキーマ](./event-schema.md)」を参照してください。 また、Azure Maps をイベント ソースとして使用するためのクイック スタートとチュートリアルの一覧も示されています。

## <a name="available-event-types"></a>使用可能なイベントの種類

Azure Maps アカウントから出力されるイベントの種類は次のとおりです。

| イベントの種類 | 説明 |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | 受信した座標が指定されたジオフェンスの外部から内部に移動したときに発生します |
| Microsoft.Maps.GeofenceExited | 受信した座標が指定されたジオフェンスの内部から外部に移動したときに発生します |
| Microsoft.Maps.GeofenceResult | 状態に関係なく、ジオフェンシング クエリから結果が返されるたびに発生します |

## <a name="example-events"></a>イベントの例

# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)
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

# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)
次の例では、**GeofenceEntered** イベントのスキーマを示します

```JSON
{   
   "id":"7f8446e2-1ac7-4234-8425-303726ea3981", 
   "source":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
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
   "type":"Microsoft.Maps.GeofenceEntered", 
   "time":"2018-11-08T00:54:17.6408601Z", 
   "specversion":"1.0" 
}
```

次の例では、**GeofenceResult** のスキーマを示します 

```JSON
{   
   "id":"451675de-a67d-4929-876c-5c2bf0b2c000", 
   "source":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
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
   "type":"Microsoft.Maps.GeofenceResult", 
   "time":"2018-11-08T00:52:08.0954283Z", 
   "specversion":"1.0" 
}
```
---

## <a name="event-properties"></a>イベントのプロパティ

# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)
イベントのトップレベルのデータを次に示します。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| `topic` | string | イベント ソースの完全なリソース パス。 このフィールドは書き込み可能ではありません。 この値は Event Grid によって指定されます。 |
| `subject` | string | 発行元が定義したイベントの対象のパス。 |
| `eventType` | string | このイベント ソース用に登録されたイベントの種類のいずれか。 |
| `eventTime` | string | プロバイダーの UTC 時刻に基づくイベントの生成時刻。 |
| `id` | string | イベントの一意識別子。 |
| `data` | object | ジオフェンシングのイベント データ。 |
| `dataVersion` | string | データ オブジェクトのスキーマ バージョン。 スキーマ バージョンは発行元によって定義されます。 |
| `metadataVersion` | string | イベント メタデータのスキーマ バージョン。 最上位プロパティのスキーマは Event Grid によって定義されます。 この値は Event Grid によって指定されます。 |

# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)
イベントのトップレベルのデータを次に示します。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| `source` | string | イベント ソースの完全なリソース パス。 このフィールドは書き込み可能ではありません。 この値は Event Grid によって指定されます。 |
| `subject` | string | 発行元が定義したイベントの対象のパス。 |
| `type` | string | このイベント ソース用に登録されたイベントの種類のいずれか。 |
| `time` | string | プロバイダーの UTC 時刻に基づくイベントの生成時刻。 |
| `id` | string | イベントの一意識別子。 |
| `data` | object | ジオフェンシングのイベント データ。 |
| `specversion` | string | CloudEvents スキーマ仕様バージョン。 |

---

データ オブジェクトには、次のプロパティがあります。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| `apiCategory` | string | イベントの API カテゴリ。 |
| `apiName` | string | イベントの API 名。 |
| `issues` | object | 処理中に発生した問題の一覧を示します。 何らかの問題が返された場合、応答ではジオメトリが返されません。 |
| `responseCode` | number | HTTP 応答コード |
| `geometries` | object | 座標が含まれるフェンス ジオメトリ、または位置の周りに searchBuffer が重なっているフェンス ジオメトリの一覧を示します。 |

Maps API でエラーが発生すると、エラー オブジェクトが返されます。 エラー オブジェクトには、次のプロパティがあります。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| `error` | ErrorDetails |Maps API でエラーが発生すると、このオブジェクトが返されます  |

Maps API でエラーが発生すると、ErrorDetails オブジェクトが返されます。 ErrorDetails オブジェクトには、次のプロパティがあります。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| `code` | string | HTTP 状態コード。 |
| `message` | string | ある場合、人間が判読できるエラーの説明。 |
| `innererror` | InnerError | ある場合、エラーに関するサービス固有の情報が含まれるオブジェクト。 |

InnerError は、エラーに関するサービス固有の情報が含まれるオブジェクトです。 InnerError オブジェクトには、次のプロパティがあります。 

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| `code` | string | エラー メッセージ。 |

geometries オブジェクト。要求のユーザー時刻を基準にして有効期限が切れているジオフェンスのジオメトリ ID の一覧です。 geometries オブジェクトには、次のプロパティを含むジオメトリ項目があります。 

| プロパティ | Type | 説明 |
|:-------- |:---- |:----------- |
| `deviceid` | string | デバイスの ID。 |
| `distance` | string | <p>座標からジオフェンスの最も近い境界までの距離。 正の値は、座標がジオフェンスの外部にあることを意味します。 座標がジオフェンスの外部にあっても、最も近いジオフェンスの境界から searchBuffer の値より大きく離れている場合、値は 999 になります。 負の値は、座標がジオフェンスの内部にあることを意味します。 座標がポリゴンの内部にあっても、最も近いジオフェンスの境界から searchBuffer の値より大きく離れている場合、値は -999 になります。 値 999 は、座標がジオフェンスの外部にあることが十分に確実であることを意味します。 値 -999 は、座標がジオフェンスの内部にあることが十分に確実であることを意味します。<p> |
| `geometryid` |string | ジオフェンスのジオメトリを示す一意 ID。 |
| `nearestlat` | number | ジオメトリの最も近いポイントの緯度。 |
| `nearestlon` | number | ジオメトリの最も近いポイントの経度。 |
| `udId` | string | ジオフェンスをアップロードしたときに、ユーザーのアップロード サービスから返される一意 ID。 Geofencing POST API には含まれません。 |

データ オブジェクトには、次のプロパティがあります。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| `expiredGeofenceGeometryId` | string[] | 要求でのユーザー時刻を基準にして期限切れになっているジオフェンスのジオメトリ ID の一覧。 |
| `geometries` | geometries[] |座標が含まれるフェンス ジオメトリ、または位置の周りに searchBuffer が重なっているフェンス ジオメトリの一覧を示します。 |
| `invalidPeriodGeofenceGeometryId` | string[]  | 要求でのユーザー時刻を基準にして無効期間内になっているジオフェンスのジオメトリ ID の一覧。 |
| `isEventPublished` | boolean | Azure Maps イベント サブスクライバーに対して少なくとも 1 つのイベントが発行されている場合は true、Azure Maps イベント サブスクライバーに対してイベントが発行されていない場合は false。 |

## <a name="tutorials-and-how-tos"></a>チュートリアルと方法
|タイトル  |説明  |
|---------|---------|
| [Event Grid を使用して Azure Maps イベントに反応する](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Azure Maps と Event Grid の統合の概要。 |
| [チュートリアル:ジオフェンスを設定する](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | このチュートリアルでは、Azure Maps を使用してジオフェンスを設定するための基本的な手順について説明します。 Azure Event Grid を使用してジオフェンスの結果をストリーミングし、ジオフェンスの結果に基づいて通知を設定します。 |

## <a name="next-steps"></a>次のステップ

* Azure Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Azure Event Grid サブスクリプションの作成の詳細については、[Event Grid サブスクリプション スキーマ](subscription-creation-schema.md)に関する記事を参照してください。
