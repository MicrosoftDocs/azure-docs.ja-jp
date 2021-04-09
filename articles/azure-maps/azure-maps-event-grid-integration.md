---
title: Event Grid を使用して Azure Maps イベントに反応する
description: Geofence に関連する Azure Maps イベントに対処する方法について調べます。 マップ イベントをリッスンする方法および Event Grid を使用してイベントをイベント ハンドラーに再ルーティングする方法について確認します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/16/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 44b2cab814d4e51c4eb0c3ce3322b898f5b26414
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92889768"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Event Grid を使用して Azure Maps イベントに反応する

Azure Maps は Azure Event Grid と統合されているため、ユーザーは他のサービスにイベント通知を送信して、ダウンストリームのプロセスをトリガーすることができます。 この記事の目的は、ご利用のビジネス アプリケーションを構成して Azure Maps イベントをリッスンできるようにすることです。 これにより、ユーザーは信頼性が高く、スケーラブルで安全な方法で重要なイベントに対応することができます。 たとえば、ユーザーは、デバイスがジオフェンスに入るたびに、データベースの更新、チケットの作成、電子メール通知の配信を実行するアプリケーションを構築できます。

Azure Event Grid は、発行 - サブスクライブ モデルを使用する、フル マネージドのイベント ルーティング サービスです。 Event Grid には、[Azure Functions](../azure-functions/functions-overview.md) や [Azure Logic Apps](../azure-functions/functions-overview.md) のような Azure サービスのサポートが組み込まれています。 また、Webhook を使用することで、Azure 以外のサービスにイベント アラートを配信することもできます。 Event Grid がサポートするイベント ハンドラーの完全な一覧については、「[Azure Event Grid の概要](../event-grid/overview.md)」をご覧ください。


![Azure Event Grid の機能モデル](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Azure Maps のイベントの種類

Event Grid は、[イベント サブスクリプション](../event-grid/concepts.md#event-subscriptions)を使って、イベント メッセージをサブスクライバーにルーティングします。 Azure Maps アカウントから出力されるイベントの種類は次のとおりです。 

| イベントの種類 | 説明 |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | 受信した座標が指定されたジオフェンスの外部から内部に移動したときに発生します |
| Microsoft.Maps.GeofenceExited | 受信した座標が指定されたジオフェンスの内部から外部に移動したときに発生します |
| Microsoft.Maps.GeofenceResult | 状態に関係なく、ジオフェンシング クエリから結果が返されるたびに発生します |

## <a name="event-schema"></a>イベント スキーマ

次の例では、GeofenceResult のスキーマを示します。

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

## <a name="tips-for-consuming-events"></a>イベントの使用に関するヒント

Azure Maps ジオフェンス イベントを処理するアプリケーションでは、いくつかの推奨される手法に従う必要があります。

* 同じイベント ハンドラーにイベントをルーティングするように、複数のサブスクリプションを構成します。 イベントが特定のソースからのものであると想定しないことが重要です。 常にメッセージ トピックをチェックして、メッセージが予期されたソースからのものであることを確認します。
* 応答ヘッダーの `X-Correlation-id` フィールドを使用して、オブジェクトに関する情報が最新かどうかを確認します。 メッセージは、順不同で、または遅延の後に、到着する場合があります。
* モード パラメーターを `EnterAndExit` に設定して Geofence API 内の GET または POST 要求を呼び出すと、前回の Geofence API 呼び出しから状態が変化したジオフェンス内のジオメトリごとに、Enter または Exit イベントが生成されます。

## <a name="next-steps"></a>次のステップ

ジオフェンシングを使用して建設現場での操作を制御する方法について詳しくは、以下をご覧ください。

> [!div class="nextstepaction"] 
> [Azure Maps を使用してジオフェンスを設定する](tutorial-geofence.md)