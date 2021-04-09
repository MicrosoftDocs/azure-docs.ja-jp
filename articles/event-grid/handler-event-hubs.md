---
title: Azure Event Grid イベントに対するイベント ハンドラーとしてのイベント ハブ
description: Azure Event Grid イベントのイベント ハンドラーとしてイベント ハブを使用する方法について説明します。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 446fef6df65f59206519e282c74d59c2ed1bfa9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005633"
---
# <a name="event-hub-as-an-event-handler-for-azure-event-grid-events"></a>Azure Event Grid イベントに対するイベント ハンドラーとしてのイベント ハブ
イベント ハンドラーは、イベントの送信先となる場所です。 ハンドラーでは、イベントを処理するアクションが実行されます。 一部の Azure サービスは、イベントを処理するように自動的に構成されます。**Azure Event Hubs** はその 1 つです。 

Event Grid からのイベントが速すぎて、ソリューションで処理しきれない場合は、**Event Hubs** を使用します。 イベントがイベント ハブに格納されたら、アプリケーションでは独自のスケジュールでイベント ハブからイベントを取得して処理できます。 イベント処理をスケーリングして、受信イベントを処理することができます。

## <a name="tutorials"></a>チュートリアル
次の例を参照してください。 

|タイトル  |説明  |
|---------|---------|
| [クイック スタート: Azure CLI を使用してカスタム イベントを Azure Event Hubs にルーティングする](custom-event-to-eventhub.md) | アプリケーションで処理するためにカスタム イベントをイベント ハブに送信します。 |
| [Resource Manager テンプレート:Event Grid カスタム トピックを作成し、イベント ハブにイベントを送信する](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| カスタム トピックのサブスクリプションを作成する Resource Manager テンプレート。 Azure Event Hubs にイベントを送信します。 |

[!INCLUDE [event-grid-message-headers](../../includes/event-grid-message-headers.md)]


## <a name="rest-examples-for-put"></a>REST の例 (PUT 用)


### <a name="event-hub"></a>イベント ハブ

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "EventHub",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="event-hub---delivery-with-managed-identity"></a>イベント ハブ - マネージド ID を使用した配信

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
                "endpointType": "EventHub",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>次のステップ
サポートされているイベント ハンドラーの一覧については、「[イベント ハンドラー](event-handlers.md)」を参照してください。 
