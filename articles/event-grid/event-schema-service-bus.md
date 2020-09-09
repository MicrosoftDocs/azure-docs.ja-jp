---
title: Event Grid ソースとしての Azure Service Bus
description: Azure Event Grid で Service Bus イベント用に提供されているプロパティについて説明します
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 81293321b3a8fb989023a231c905996b4059bd81
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86121136"
---
# <a name="azure-service-bus-as-an-event-grid-source"></a>Event Grid ソースとしての Azure Service Bus

この記事では、Service Bus イベントのプロパティとスキーマについて説明します。 イベント スキーマの概要については、「[Azure Event Grid イベント スキーマ](event-schema.md)」を参照してください。

## <a name="event-grid-event-schema"></a>Event Grid イベント スキーマ

### <a name="available-event-types"></a>使用可能なイベントの種類

Service Bus から出力されるイベントの種類は次のとおりです。

| イベントの種類 | 説明 |
| ---------- | ----------- |
| Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners | キューまたはサブスクリプションにアクティブなメッセージがあり、リッスンしているレシーバーがない場合に生成されます。 |
| Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener | 配信不能キューにアクティブなメッセージがあり、アクティブなレシーバーがない場合に生成されます。 |

### <a name="example-event"></a>イベントの例

次の例では、リスナー イベントがないアクティブなメッセージのスキーマを示します。

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

配信不能キュー イベントのスキーマも同様です。

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>イベントのプロパティ

イベントのトップレベルのデータを次に示します。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| topic | string | イベント ソースの完全なリソース パス。 このフィールドは書き込み可能ではありません。 この値は Event Grid によって指定されます。 |
| subject | string | 発行元が定義したイベントの対象のパス。 |
| eventType | string | このイベント ソース用に登録されたイベントの種類のいずれか。 |
| eventTime | string | プロバイダーの UTC 時刻に基づくイベントの生成時刻。 |
| id | string | イベントの一意識別子。 |
| data | object | Blob Storage イベントのデータ。 |
| dataVersion | string | データ オブジェクトのスキーマ バージョン。 スキーマ バージョンは発行元によって定義されます。 |
| metadataVersion | string | イベント メタデータのスキーマ バージョン。 最上位プロパティのスキーマは Event Grid によって定義されます。 この値は Event Grid によって指定されます。 |

データ オブジェクトには、次のプロパティがあります。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| namespaceName | string | リソースが存在する Service Bus 名前空間。 |
| requestUri | string | イベントを生成している特定のキューまたはサブスクリプションの URI。 |
| entityType | string | イベントを生成している Service Bus エンティティの種類 (キューまたはサブスクリプション)。 |
| queueName | string | キューをサブスクライブしている場合、アクティブなメッセージのあるキュー。 トピック/サブスクリプションを使っている場合は値 null。 |
| topicName | string | アクティブなメッセージのある Service Bus サブスクリプションが属しているトピック。 キューを使っている場合は値 null。 |
| subscriptionName | string | アクティブなメッセージのある Service Bus サブスクリプション。 キューを使っている場合は値 null。 |

## <a name="tutorials-and-how-tos"></a>チュートリアルと方法
|タイトル  |説明  |
|---------|---------|
| [チュートリアル:Azure Service Bus の Azure Event Grid への統合の例](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid は、Service Bus トピックから関数アプリとロジック アプリにメッセージを送信します。 |
| [Azure Service Bus と Event Grid の統合](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Service Bus と Event Grid の統合の概要です。 |

## <a name="next-steps"></a>次のステップ

* Azure Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Azure Event Grid サブスクリプションの作成の詳細については、[Event Grid サブスクリプション スキーマ](subscription-creation-schema.md)に関する記事を参照してください。
* Service Bus での Azure Event Grid の使用に関して詳しくは、「[Azure Service Bus と Event Grid の統合の概要](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md)」をご覧ください。
* [Functions または Logic Apps で Service Bus イベントを受信](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json)してみてください。
