---
title: Azure IoT Hub と Event Grid | Microsoft Docs
description: Azure Event Grid を使い、IoT Hub で発生したアクションに基づいてプロセスをトリガーします。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
ms.openlocfilehash: 0e0ca8a787145fb40087a2d99be85607404eebfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92152136"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Event Grid を使用し IoT Hub のイベントに対応してアクションをトリガーする

他のサービスにイベント通知を送信して、ダウンストリームのプロセスをトリガーできるように、Azure IoT Hub は Azure Event Grid と統合します。 信頼性が高く、スケーラブルで、安全な方法により、重大なイベントに対応できるよう、IoT Hub のイベントをリッスンするようにビジネス アプリケーションを構成します。  たとえば、新しい IoT デバイスが IoT Hub に登録されるたびに、データベースの更新、作業チケットの作成、メール通知の配信などを実行するよう、アプリケーションを構築します。

[Azure Event Grid](../event-grid/overview.md) は、発行-サブスクライブ モデルを使う、フル マネージドのイベント ルーティング サービスです。 Event Grid は、[Azure Functions](../azure-functions/functions-overview.md) や [Azure Logic Apps](../logic-apps/logic-apps-overview.md) などの Azure s サービスの組み込みサポートを備えており、webhook を使って Azure 以外のサービスにイベント アラートを配信できます。 Event Grid がサポートするイベント ハンドラーの完全な一覧については、「[Azure Event Grid の概要](../event-grid/overview.md)」をご覧ください。

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
| Microsoft.Devices.DeviceTelemetry | デバイス テレメトリのメッセージが IoT Hub に送信されると発行されます |

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



### <a name="device-telemetry-schema"></a>デバイス テレメトリ スキーマ

デバイス テレメトリ メッセージは、メッセージの [システム プロパティ](iot-hub-devguide-routing-query-syntax.md#system-properties)で contentType が **application/json** に設定され、contentEncoding が **UTF-8** に設定された有効な JSON 形式である必要があります。 これらのプロパティでは、どちらも大文字と小文字が区別されません。 コンテンツのエンコードが設定されていない場合、IoT Hub では Base 64 エンコード形式でメッセージが書き込まれます。

エンドポイントを Event Grid として選択してデバイス テレメトリ イベントを Event Grid に発行する前に、それらをエンリッチすることができます。 詳細については、[メッセージ エンリッチメントの概要](iot-hub-message-enrichments-overview.md)に関するページを参照してください。

次の例に、デバイス テレメトリ イベントのスキーマを示します。

```json
[{  
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {
      "body": {
          "Weather": {
              "Temperature": 900
            },
            "Location": "USA"
        },
        "properties": {
            "Status": "Active"
        },
        "systemProperties": {
          "iothub-content-type": "application/json",
          "iothub-content-encoding": "utf-8",
          "iothub-connection-device-id": "d1",
          "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
          "iothub-connection-auth-generation-id": "123455432199234570",
          "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
          "iothub-message-source": "Telemetry"
        }
  },
  "dataVersion": "",
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


> [!WARNING]
> デバイス作成イベントに関連付けられている *ツイン データ* は既定の構成であるため、新しく作成されたデバイスの実際の `authenticationType` とその他のデバイス プロパティは、それらに依存 *させない* ようにしてください。 新しく作成されたデバイスの `authenticationType` とその他のデバイス プロパティについては、Azure IoT SDK で提供されている Register Manager API を使用してください。

各プロパティの詳しい説明については、「[IoT Hub の Azure Event Grid イベント スキーマ](../event-grid/event-schema-iot-hub.md)」をご覧ください。

## <a name="filter-events"></a>イベントのフィルター処理

IoT Hub イベント サブスクリプションでは、イベントの種類、データ コンテンツ、およびサブジェクト (デバイス名) に基づいてイベントをフィルター処理できます。

Event Grid により、イベントの種類、サブジェクト、およびデータ コンテンツで[フィルター処理](../event-grid/event-filtering.md)できます。 Event Grid サブスクリプションの作成時に、選択した IoT イベントにサブスクライブするように選択できます。 Event Grid のサブジェクト フィルターは、**Begins With** (プレフィックス) と **Ends With** (サフィックス) の一致に基づいて動作します。 フィルターは `AND` 演算子を使用するため、プレフィックスとサフィックスの両方に一致するサブジェクトを持つイベントがサブスクライバーに配信されます。

IoT イベントのサブジェクトには次の形式が使われます。

```json
devices/{deviceId}
```

さらに、Event Grid では、データ コンテンツなどの各イベントの属性でフィルター処理することもできます。 これにより、テレメトリ メッセージの内容に基づいて配信されるイベントを選択することができます。 [高度なフィルター処理](../event-grid/event-filtering.md#advanced-filtering)を参照して、例を確認してください。 テレメトリ メッセージ本文のフィルター処理の場合、メッセージの [システム プロパティ](./iot-hub-devguide-routing-query-syntax.md#system-properties)で contentType を **application/json** に設定し、contentEncoding を **UTF-8** に設定する必要があります。 これらのプロパティでは、どちらも大文字と小文字が区別されません。

DeviceConnected、DeviceDisconnected、DeviceCreated、DeviceDeleted のような非テレメトリ イベントの場合、サブスクリプションの作成時に Event Grid のフィルター処理を使用できます。 テレメトリ イベントの場合、Event Grid でのフィルター処理に加えて、ユーザーは、メッセージ ルーティング クエリ経由で、デバイス ツイン、メッセージのプロパティと本文に対してフィルター処理することもできます。 

Event Grid 経由でテレメトリ イベントをサブスクライブすると、IoT Hub では、Event Grid にデータ ソース型のデバイス メッセージを送信するための既定のメッセージ ルートが作成されます。 メッセージ ルーティングの詳細については、[IoT Hub メッセージ ルーティング](iot-hub-devguide-messages-d2c.md)に関するページを参照してください。 このルートはポータルの [IoT Hub] の [メッセージ ルーティング] に表示されます。 Event Grid までの唯一のルートは、テレメトリ イベントに対して作成される EG サブスクリプションの数に関係なく作成されます。 そのため、フィルターの異なるサブスクリプションがいくつか必要な場合、同じルートでこれらのクエリに OR 演算子を使用できます。 ルートの作成と削除は、Event Grid 経由のテレメトリ イベントのサブスクリプションによって制御されます。 IoT Hub メッセージ ルーティングを利用して Event Grid までのルートを作成したり、削除したりすることはできません。

テレメトリ データが送信される前に、メッセージをフィルター処理するために、[ルーティング クエリ](iot-hub-devguide-routing-query-syntax.md)を更新できます。 ルーティング クエリは本文が JSON である場合にのみ、メッセージ本文に適用できることに注意してください。 また、メッセージの [システム プロパティ](./iot-hub-devguide-routing-query-syntax.md#system-properties)で contentType を **application/json** に設定し、contentEncoding を **UTF-8** に設定する必要もあります。

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>デバイス接続イベントおよびデバイス切断イベントの制限事項

デバイス接続状態イベントを受信するには、デバイスで IoT Hub を使用して "D2C テレメトリ送信" または "C2D メッセージ受信" 操作を実行する必要があります。 しかし、AMQP プロトコルを使用して IoT Hub に接続するデバイスについては、"C2D メッセージ受信" 操作を実行することが推奨されます。そうしないと、接続状態の通知が数分遅延することがあります。 デバイスが MQTT プロトコルを使用している場合、IoT Hub は C2D リンクを開いたままにします。 AMQP では、Receive Async API (IoT Hub C# SDK) または[デバイス クライアント (AMQP)](iot-hub-amqp-support.md#device-client) を呼び出すことによって、C2D リンクを開くことができます。

テレメトリを送信する場合は、D2C リンクが開いています。 

デバイスの接続状態が頻繁に変化する場合、つまりデバイスが頻繁に接続されたり切断されたりする場合は、1 つ 1 つの接続状態が送信されるのではなく、接続状態が変化し続ける間、定期的なスナップショットで取得される最新の接続状態が公開されます。 異なるシーケンス番号で同じ接続状態イベントを受信する場合も、異なる接続状態イベントを受信する場合も、デバイスの接続状態に変化が生じたことを意味します。

## <a name="tips-for-consuming-events"></a>イベントの使用に関するヒント

IoT Hub イベントを処理するアプリケーションは、以下の推奨される手法に従う必要があります。

* 同じイベント ハンドラーにイベントをルーティングするように複数のサブスクリプションを構成できるので、イベントが特定のソースからであると想定しないでください。 常にメッセージ トピックをチェックし、予期される IoT Hub からものであることを確認してください。

* 受信するすべてのイベントが予期する種類であると想定してはいけません。 メッセージを処理する前に、常に eventType をチェックしてください。

* メッセージは、順不同で、または遅延の後に、到着する場合があります。 etag フィールドを使って、デバイス作成イベントやデバイス削除イベントについて、オブジェクトに関する情報が最新かどうかを確認してください。

## <a name="next-steps"></a>次のステップ

* [IoT Hub イベントのチュートリアルを試します](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [デバイス接続イベントおよびデバイス切断イベントの順序を設定する方法を確認します](iot-hub-how-to-order-connection-state-events.md)

* [Event Grid の詳細を確認します](../event-grid/overview.md)

* [IoT Hub のイベントとメッセージのルーティングの違いを比較します](iot-hub-event-grid-routing-comparison.md)

* [IoT テレメトリ イベントを使用して、Azure Maps を使用して IoT 空間分析を実装する方法について学びます](../azure-maps/tutorial-iot-hub-maps.md)