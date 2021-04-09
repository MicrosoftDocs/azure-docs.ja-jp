---
title: Azure Event Grid イベントに対するイベント ハンドラーとしての Service Bus のキューとトピック
description: Azure Event Grid のイベントに対するイベント ハンドラーとして Service Bus のキューとトピックを使用する方法を説明します。
ms.topic: conceptual
ms.date: 09/03/2020
ms.openlocfilehash: 12b72420e3475b46a4cd61ce5032b478af740dde
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97399861"
---
# <a name="service-bus-queues-and-topics-as-event-handlers-for-azure-event-grid-events"></a>Azure Event Grid イベントに対するイベント ハンドラーとしての Service Bus のキューとトピック
イベント ハンドラーは、イベントの送信先となる場所です。 ハンドラーは、さらにいくつかのアクションを行ってイベントを処理します。 一部の Azure サービスは、イベントを処理するように自動的に構成されます。**Azure Service Bus** はその 1 つです。 

Event Grid からのイベントに対するハンドラーとして、Service Bus のキューまたはトピックを使用できます。 

## <a name="service-bus-queues"></a>Service Bus キュー
エンタープライズ アプリケーションでのバッファー処理または音声コマンドのシナリオで使用するために、Event Grid のイベントを Service Bus キューに直接ルーティングすることができます。

Azure portal で、イベント サブスクリプションを作成するときに、エンドポイントの種類として **[Service Bus キュー]** を選択し、 **[エンドポイントの選択]** をクリックして Service Bus キューを選択します。

### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>CLI を使って Service Bus キューハンドラーを追加する

Azure CLI の場合、次の例でイベント グリッド トピックを Service Bus キューにサブスクライブして接続します。

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

## <a name="service-bus-topics"></a>Service Bus トピック

Event Grid のイベントを Service Bus トピックに直接ルーティングして、Service Bus トピックやコマンドと制御メッセージング シナリオで Azure システム イベントを処理することができます。

Azure portal で、イベント サブスクリプションを作成するときに、エンドポイントの種類として **[Service Bus トピック]** を選択し、 **[エンドポイントの選択]** をクリックして Service Bus トピックを選択します。

### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>CLI を使って Service Bus トピック ハンドラーを追加する

Azure CLI の場合、次の例では、イベント グリッド トピックをサブスクライブし、Service Bus トピックに接続します。

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebustopic \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/topics/topic1
```

[!INCLUDE [event-grid-message-headers](../../includes/event-grid-message-headers.md)]

ブローカー メッセージとして Service Bus のキューまたはトピックにイベントを送信する場合、ブローカー メッセージの `messageid` は内部システム ID です。

メッセージの内部システム ID は、イベントの再配信間で保持されるので、Service Bus のエンティティで **重複検出** をオンにすることで、配信の重複を回避できます。 Service Bus エンティティでの重複検出の期間を、イベントの有効期限 (TTL) または最大再試行期間のいずれか長い方に設定することをお勧めします。

## <a name="rest-examples-for-put"></a>REST の例 (PUT 用)

### <a name="service-bus-queue"></a>Service Bus キュー

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-queue---delivery-with-managed-identity"></a>Service Bus キュー - マネージド ID を使用した配信

```json
{
    "properties": {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "ServiceBusQueue",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic"></a>Service Bus トピック

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusTopic",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic---delivery-with-managed-identity"></a>Service Bus トピック - マネージド ID を使用した配信

```json
{
    "properties": 
    {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "ServiceBusTopic",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>次のステップ
サポートされているイベント ハンドラーの一覧については、「[イベント ハンドラー](event-handlers.md)」を参照してください。 
