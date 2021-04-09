---
title: Azure Event Grid イベントに対するイベント ハンドラーとしてのストレージ キュー
description: Azure Event Grid イベントのイベント ハンドラーとして Azure ストレージ キューを使用する方法について説明します。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 502b44f276253be69362424c9de0fd516d20ad9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91270187"
---
# <a name="storage-queue-as-an-event-handler-for-azure-event-grid-events"></a>Azure Event Grid イベントに対するイベント ハンドラーとしてのストレージ キュー
イベント ハンドラーは、イベントの送信先となる場所です。 ハンドラーは、さらにいくつかのアクションを行ってイベントを処理します。 一部の Azure サービスは、イベントを処理するように自動的に構成されます。**Azure Queue storage** はその 1 つです。 

**Queue storage** を使用すると、プルする必要があるイベントを受信できます。 応答に時間がかかりすぎる長期実行プロセスの場合に、Queue Storage を使用できます。 イベントを Queue Storage に送信することで、アプリでは独自のスケジュールでイベントをプルして処理することができます。

## <a name="tutorials"></a>チュートリアル
イベント ハンドラーとして Queue storage を使用する例については、次のチュートリアルを参照してください。 

|タイトル  |説明  |
|---------|---------|
| [クイック スタート: Azure CLI と Event Grid を使ってカスタム イベントを Azure Queue Storage にルーティングする](custom-event-to-queue-storage.md) | Queue Storage にカスタム イベントを送信する方法について説明します。 |

## <a name="rest-examples-for-put"></a>REST の例 (PUT 用)

### <a name="storage-queue-as-the-event-handler"></a>イベント ハンドラーとしてのストレージ キュー

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "StorageQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>",
                "queueName": "<QUEUE NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="storage-queue-as-the-event-handler---delivery-with-managed-identity"></a>イベント ハンドラーとしてのストレージ キュー - マネージド ID を使用した配信

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
                "endpointType": "StorageQueue",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>",
                    "queueName": "<QUEUE NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="storage-queue-as-a-deadletter-destination"></a>配信不能の宛先としてのストレージ キュー

```json
{
    "name": "",
    "properties": 
    {
        "destination": 
        {
            "endpointType": "StorageQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<DESTINATION STORAGE>",
                "queueName": "queue1"
            }
        },
        "eventDeliverySchema": "EventGridSchema",
        "deadLetterDestination": 
        {
            "endpointType": "StorageBlob",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<DEADLETTER STORAGE>",
                "blobContainerName": "test"
            }
        }
    }
}
```

### <a name="storage-queue-as-a-deadletter-destination---managed-identity"></a>配信不能の宛先としてのストレージ キュー - マネージド ID

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "StorageQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<DESTINATION STORAGE>",
                "queueName": "queue1"
            }
        },
        "eventDeliverySchema": "EventGridSchema",
        "deadLetterWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "deadLetterDestination": 
            {
                "endpointType": "StorageBlob",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<DEADLETTER STORAGE>",
                    "blobContainerName": "test"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>次のステップ
サポートされているイベント ハンドラーの一覧については、「[イベント ハンドラー](event-handlers.md)」を参照してください。 
