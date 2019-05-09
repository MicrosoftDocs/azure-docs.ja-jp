---
title: Azure IoT Hub と Event Grid | Microsoft Docs
description: Azure Event Grid を使い、IoT Hub で発生したアクションに基づいてプロセスをトリガーします。
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: a2c49a6ba269321d1903565ace3ebaae3f3b917e
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673855"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Event Grid を使用し IoT Hub のイベントに対応してアクションをトリガーする

他のサービスにイベント通知を送信して、ダウンストリームのプロセスをトリガーできるように、Azure IoT Hub は Azure Event Grid と統合します。 信頼性が高く、スケーラブルで、安全な方法により、重大なイベントに対応できるよう、IoT Hub のイベントをリッスンするようにビジネス アプリケーションを構成します。 たとえば、新しい IoT デバイスが IoT Hub に登録されるたびに、データベースの更新、作業チケットの作成、メール通知の配信などを実行するよう、アプリケーションを構築します。 

[Azure Event Grid](../event-grid/overview.md) は、発行-サブスクライブ モデルを使う、フル マネージドのイベント ルーティング サービスです。 Event Grid は、[Azure Functions](../azure-functions/functions-overview.md) や [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md) などの Azure s サービスの組み込みサポートを備えており、webhook を使って Azure 以外のサービスにイベント アラートを配信できます。 Event Grid がサポートするイベント ハンドラーの完全な一覧については、「[Azure Event Grid の概要](../event-grid/overview.md)」をご覧ください。 

![Azure Event Grid のアーキテクチャ](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>リージョン別の提供状況

Event Grid の統合は、Event Grid イベントグリッドがサポートされている地域にある IoT ハブで利用できます。 最新のリージョン一覧については、「[Azure Event Grid の概要](../event-grid/overview.md)」を参照してください。 

## <a name="event-types"></a>イベントの種類

IoT Hub は次のイベントの種類を発行します。 

| イベントの種類 | 説明 |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | デバイスが IoT Hub に登録されると発行されます。 |
| Microsoft.Devices.DeviceDeleted | デバイスが IoT Hub から削除されると発行されます。 |
| Microsoft.Devices.DeviceConnected | デバイスが IoT Hub に接続されると発行されます。 |
| Microsoft.Devices.DeviceDisconnected | デバイスが IoT Hub から切断されると発行されます。 |

各 IoT Hub から発行するイベントを構成するには、Azure Portal または Azure CLI を使います。 例については、チュートリアル「[Logic Apps を使用して Azure IoT Hub イベントに関する電子メール通知を送信する](../event-grid/publish-iot-hub-events-to-logic-apps.md)」をお試しください。

## <a name="event-schema"></a>イベント スキーマ

IoT Hub イベントには、デバイスのライフサイクルの変更に対応するために必要なすべての情報が含まれます。 eventType プロパティが **Microsoft.Devices** で始まることをチェックすることにより、IoT Hub イベントを識別できます。 Event Grid イベント プロパティの使い方について詳しくは、「[Event Grid イベント スキーマ](../event-grid/event-schema.md)」をご覧ください。

### <a name="device-connected-schema"></a>デバイス接続スキーマ

次の例では、デバイス接続イベントのスキーマを示します。 

```json
[{  
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8", 
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceConnected", 
  "eventTime": "2018-06-02T19:17:44.4383997Z", 
  "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID",
  }, 
  "dataVersion": "1", 
  "metadataVersion": "1" 
}]
```

### <a name="device-created-schema"></a>デバイスで作成されたスキーマ

次の例では、デバイス作成スキーマを示します。 

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag":"null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

各プロパティの詳しい説明については、「[IoT Hub の Azure Event Grid イベント スキーマ](../event-grid/event-schema-iot-hub.md)」をご覧ください。

## <a name="filter-events"></a>イベントのフィルター処理

IoT Hub イベント サブスクリプションは、イベントの種類とデバイス名に基づいてイベントをフィルター処理できます。 Event Grid のサブジェクト フィルターは、**Begins With** (プレフィックス) と **Ends With** (サフィックス) の一致に基づいて動作します。 フィルターは `AND` 演算子を使用するため、プレフィックスとサフィックスの両方に一致するサブジェクトを持つイベントがサブスクライバーに配信されます。 

IoT イベントのサブジェクトには次の形式が使われます。

```json
devices/{deviceId}
```
## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>デバイス接続イベントおよびデバイス切断イベントの制限事項

デバイス接続イベントおよびデバイス切断イベントを受信するには、デバイスの D2C リンクまたは C2D リンクを開く必要があります。 デバイスが MQTT プロトコルを使用している場合、IoT Hub は C2D リンクを開いたままにします。 AMQP の場合は、[非同期受信 API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet) を呼び出して C2D リンクを開くことができます。 

テレメトリを送信する場合は、D2C リンクが開いています。 デバイスの接続状態が頻繁に変化する場合、つまりデバイスが頻繁に接続されたり切断されたりする場合、すべての単一の接続状態は送信されず、1 分ごとのスナップショットが作成された接続状態が公開されます。 IoT Hub が停止した場合、停止が解消されるとすぐにデバイスの接続状態が公開されます。 その停止中にデバイスが切断された場合は、デバイス切断イベントが 10 分以内に発行されます。

## <a name="tips-for-consuming-events"></a>イベントの使用に関するヒント

IoT Hub イベントを処理するアプリケーションは、以下の推奨される手法に従う必要があります。

* 同じイベント ハンドラーにイベントをルーティングするように複数のサブスクリプションを構成できるので、イベントが特定のソースからであると想定しないでください。 常にメッセージ トピックをチェックし、予期される IoT Hub からものであることを確認してください。 

* 受信するすべてのイベントが予期する種類であると想定してはいけません。 メッセージを処理する前に、常に eventType をチェックしてください。

* メッセージは、順不同で、または遅延の後に、到着する場合があります。 etag フィールドを使って、オブジェクトに関する情報が最新かどうか確認してください。

## <a name="next-steps"></a>次の手順

* [IoT Hub イベントのチュートリアルを試します](../event-grid/publish-iot-hub-events-to-logic-apps.md)
* [デバイス接続イベントおよびデバイス切断イベントの順序を設定する方法を確認します](iot-hub-how-to-order-connection-state-events.md)
* [Event Grid の詳細を確認します](../event-grid/overview.md)
* [IoT Hub のイベントとメッセージのルーティングの違いを比較します](iot-hub-event-grid-routing-comparison.md)
