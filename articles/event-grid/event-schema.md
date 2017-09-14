---
title: "Azure Event Grid イベント スキーマ"
description: "Azure Event Grid のイベントに対して用意されているプロパティについて説明します。"
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 08/15/2017
ms.author: babanisa
ms.translationtype: HT
ms.sourcegitcommit: b6c65c53d96f4adb8719c27ed270e973b5a7ff23
ms.openlocfilehash: 6736c6a60021b51db612f0a596086a9e988d7aef
ms.contentlocale: ja-jp
ms.lasthandoff: 08/17/2017

---

# <a name="event-grid-event-schema"></a>Event Grid イベント スキーマ

この記事では、イベントのプロパティとスキーマについて説明します。 イベントは、5 つの必須文字列プロパティと 1 つの必須**データ** オブジェクトで構成されます。 プロパティは、すべてのイベントに共通であり、発行元を問いません。 **データ** オブジェクトには、発行元に固有のプロパティが含まれます。 システム トピックの場合、これらのプロパティは、リソース プロバイダー (Storage や Event Hubs など) に固有です。

イベントは、Azure Event Grid に配列で送信され、配列には複数のイベント オブジェクトを含めることができます。 1 つのイベントのみがある場合、配列の長さは 1 になります。 
 
## <a name="event-properties"></a>イベントのプロパティ

すべてのイベントには、次に示す最上位レベルのデータが格納されます。

| プロパティ | 型 | 説明 |
| -------- | ---- | ----------- |
| トピック | string | イベント ソースの完全なリソース パス。 このフィールドは書き込み可能ではありません。 |
| subject | string | 発行元が定義したイベントの対象のパス。 |
| eventType | string | このイベント ソース用に登録されたイベントの種類のいずれか。 |
| eventTime | string | プロバイダーの UTC 時刻に基づくイベントの生成時刻。 |
| id | string | イベントの一意識別子。 |
| data | オブジェクト | リソース プロバイダーに固有のイベント データ。 |

## <a name="available-event-sources"></a>使用可能なイベント ソース

次のイベント ソースが、Event Grid 経由で使用するためのイベントを発行します。

* リソース グループ (管理操作)
* Azure サブスクリプション (管理操作)
* Event Hubs
* カスタム トピック

## <a name="azure-subscriptions"></a>Azure サブスクリプション

Azure サブスクリプションは、VM が作成されたりストレージ アカウントが削除されたりしたときに、Azure Resource Manager から管理イベントを出力できるようになりました。

### <a name="available-event-types"></a>使用可能なイベントの種類

- **Microsoft.Resources.ResourceWriteSuccess**: リソースの作成または更新操作が成功したときに発生します。  
- **Microsoft.Resources.ResourceWriteFailure**: リソースの作成または更新操作が失敗したときに発生します。  
- **Microsoft.Resources.ResourceWriteCancel**: リソースの作成または更新操作が取り消されたときに発生します。  
- **Microsoft.Resources.ResourceDeleteSuccess**: リソースの削除操作が失敗したときに発生します。  
- **Microsoft.Resources.ResourceDeleteFailure**: リソースの削除操作が失敗したときに発生します。  
- **Microsoft.Resources.ResourceDeleteCancel**: リソースの削除が取り消されたときに発生します。 これは、テンプレートのデプロイが取り消された場合に発生します。

### <a name="example-event-schema"></a>イベント スキーマの例

```json
[
    {
    "topic":"/subscriptions/{subscription-id}",
    "subject":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "eventType":"Microsoft.Resources.ResourceWriteSuccess",
    "eventTime":"2017-08-16T03:54:38.2696833Z",
    "id":"25b3b0d0-d79b-44d5-9963-440d4e6a9bba",
    "data": {
        "authorization":"{azure_resource_manager_authorizations}",
        "claims":"{azure_resource_manager_claims}",
        "correlationId":"54ef1e39-6a82-44b3-abc1-bdeb6ce4d3c6",
        "httpRequest":"",
        "resourceProvider":"Microsoft.EventGrid",
        "resourceUri":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
        "operationName":"Microsoft.EventGrid/eventSubscriptions/write",
        "status":"Succeeded",
        "subscriptionId":"{subscription-id}",
        "tenantId":"72f988bf-86f1-41af-91ab-2d7cd011db47"
        },
    }
]
```



## <a name="resource-groups"></a>リソース グループ

リソース グループは、VM が作成されたりストレージ アカウントが削除されたりしたときに、Azure Resource Manager から管理イベントを出力できるようになりました。

### <a name="available-event-types"></a>使用可能なイベントの種類

- **Microsoft.Resources.ResourceWriteSuccess**: リソースの作成または更新操作が成功したときに発生します。  
- **Microsoft.Resources.ResourceWriteFailure**: リソースの作成または更新操作が失敗したときに発生します。  
- **Microsoft.Resources.ResourceWriteCancel**: リソースの作成または更新操作が取り消されたときに発生します。  
- **Microsoft.Resources.ResourceDeleteSuccess**: リソースの削除操作が失敗したときに発生します。  
- **Microsoft.Resources.ResourceDeleteFailure**: リソースの削除操作が失敗したときに発生します。  
- **Microsoft.Resources.ResourceDeleteCancel**: リソースの削除が取り消されたときに発生します。 これは、テンプレートのデプロイが取り消された場合に発生します。

### <a name="example-event"></a>イベントの例

```json
[
    {
    "topic":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}",
    "subject":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "eventType":"Microsoft.Resources.ResourceWriteSuccess",
    "eventTime":"2017-08-16T03:54:38.2696833Z",
    "id":"25b3b0d0-d79b-44d5-9963-440d4e6a9bba",
    "data": {
        "authorization":"{azure_resource_manager_authorizations}",
        "claims":"{azure_resource_manager_claims}",
        "correlationId":"54ef1e39-6a82-44b3-abc1-bdeb6ce4d3c6",
        "httpRequest":"",
        "resourceProvider":"Microsoft.EventGrid",
        "resourceUri":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
        "operationName":"Microsoft.EventGrid/eventSubscriptions/write",
        "status":"Succeeded",
        "subscriptionId":"{subscription-id}",
        "tenantId":"72f988bf-86f1-41af-91ab-2d7cd011db47"
        },
    }
]
```



## <a name="event-hubs"></a>Event Hubs

Event Hubs のイベントは、現時点では、ファイルがキャプチャ機能を使用してストレージに自動的に送信されたときにのみ出力されます。

### <a name="available-event-types"></a>使用可能なイベントの種類

- **Microsoft.EventHub.CaptureFileCreated**: キャプチャ ファイルが作成されたときに発生します。

### <a name="example-event"></a>イベントの例

このサンプル イベントは、ファイルがキャプチャによって保存されるときに発生する Event Hubs イベントのスキーマを示しています。 

```json
[
    {
        "topic": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{event-hubs-ns}",
        "subject": "eventhubs/eh1",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-07-11T00:55:55.0120485Z",
        "id": "bd440490-a65e-4c97-8298-ef1eb325673c",
        "data": {
            "fileUrl": "https://gridtest1.blob.core.windows.net/acontainer/eventgridtest1/eh1/1/2017/07/11/00/54/54.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 0,
            "eventCount": 0,
            "firstSequenceNumber": -1,
            "lastSequenceNumber": -1,
            "firstEnqueueTime": "0001-01-01T00:00:00",
            "lastEnqueueTime": "0001-01-01T00:00:00"
        },
    }
]

```



## <a name="azure-blob-storage"></a>Azure Blob Storage

プライベート プレビューの Azure Blob Storage は、サインアップで Event Grid と統合します。

### <a name="available-event-types"></a>使用可能なイベントの種類

- **Microsoft.Storage.BlobCreated**: BLOB が作成されたときに発生します。
- **Microsoft.Storage.BlobDeleted**: BLOB が削除されたときに発生します。

### <a name="example-event"></a>イベントの例

このサンプル イベントは、BLOB が作成されたときに発生するストレージ イベントのスキーマを示しています。 

```json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
    "subject": "/blobServices/default/containers/oc2d2817345i200097container/blobs/oc2d2817345i20002296blob",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    }
  }
]
```




## <a name="custom-topics"></a>カスタム トピック

カスタム イベントのデータ ペイロードを、正しい形式の JSON によって定義できます。 最上位レベルのデータには、リソースによって定義される標準的なイベントと同じフィールドを含める必要があります。 カスタム トピックに対してイベントを発行する場合は、ルーティングとフィルタ処理を支援するために、イベントの対象をモデル化することを検討してください。

### <a name="example-event"></a>イベントの例

次の例は、カスタム トピック用のイベントを示しています。
````json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.EventGrid/topics/myeventgridtopic",
    "subject": "/myapp/vehicles/motorcycles",    
    "id": "b68529f3-68cd-4744-baa4-3c0498ec19e2",
    "eventType": "recordInserted",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "data":{
      "make": "Ducati",
      "model": "Monster"
    }
  }
]

````

## <a name="next-steps"></a>次のステップ

* Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Event Grid サブスクリプションの作成の詳細については、[Event Grid サブスクリプション スキーマ](subscription-creation-schema.md)に関する記事を参照してください。

