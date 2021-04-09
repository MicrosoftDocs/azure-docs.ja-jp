---
title: REST API - Azure Event Grid IoT Edge | Microsoft Docs
description: Event Grid on IoT Edge の REST API。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 414487d460d897eff787b11915db560706b29eb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171756"
---
# <a name="rest-api"></a>REST API
この記事では、Azure Event Grid on IoT Edge の REST API について説明します

## <a name="common-api-behavior"></a>一般的な API の動作

### <a name="base-url"></a>ベース URL
Event Grid on IoT Edge には、HTTP (ポート 5888) と HTTPS (ポート 4438) を介して公開されている次の API があります。

* HTTP のベース URL: http://eventgridmodule:5888
* HTTPS のベース URL: https://eventgridmodule:4438

### <a name="request-query-string"></a>クエリ文字列の要求
すべての API 要求で、以下のクエリ文字列パラメーターが必要です。

```?api-version=2019-01-01-preview```

### <a name="request-content-type"></a>コンテンツ タイプの要求
すべての API 要求で、**Content-Type** が必要です。

**EventGridSchema** または **CustomSchema** の場合、Content-Type の値は次のいずれかです。

```Content-Type: application/json```

```Content-Type: application/json; charset=utf-8```

構造化モードの **CloudEventSchemaV1_0** の場合、Content-Type の値は次のいずれかです。

```Content-Type: application/cloudevents+json```
    
```Content-Type: application/cloudevents+json; charset=utf-8```
    
```Content-Type: application/cloudevents-batch+json```
    
```Content-Type: application/cloudevents-batch+json; charset=utf-8```

バイナリ モードの **CloudEventSchemaV1_0** の場合は、[ドキュメント](https://github.com/cloudevents/spec/blob/master/http-protocol-binding.md)で詳細を確認してください。

### <a name="error-response"></a>エラー応答
すべての API は、以下のペイロードでエラーを返します。

```json
{
    "error":
    {
        "code": "<HTTP STATUS CODE>",
        "details": 
        {
            "code": "<Detailed Error Code>",
            "message": "..."
        }
    }
}
```

## <a name="manage-topics"></a>トピックの管理

### <a name="put-topic-create--update"></a>トピックのプット (作成または更新)

**要求**: ``` PUT /topics/<topic_name>?api-version=2019-01-01-preview ```

**ペイロード**:

```json
    {
        "name": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "properties":
        {
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // optional
        }
    }
```

**応答**: HTTP 200

**ペイロード**:

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<get_request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // populated with EventGridSchema if not explicitly specified in PUT request
    }
}
```

### <a name="get-topic"></a>トピックの取得

**要求**: ``` GET /topics/<topic_name>?api-version=2019-01-01-preview ```

**応答**: HTTP 200

**ペイロード**:
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
    }
}
```

### <a name="get-all-topics"></a>すべてのトピックの取得

**要求**: ``` GET /topics?api-version=2019-01-01-preview ```

**応答**: HTTP 200

**ペイロード**:
```json
[
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    },
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    }
]
```

### <a name="delete-topic"></a>トピックの削除

**要求**: ``` DELETE /topics/<topic_name>?api-version=2019-01-01-preview ```

**応答**: HTTP 200、空のペイロード

## <a name="manage-event-subscriptions"></a>イベント サブスクリプションの管理
このセクションのサンプルでは、`EndpointType=Webhook;` を使用しています。 `EndpointType=EdgeHub / EndpointType=EventGrid` の json サンプルは、次のセクションにあります。 

### <a name="put-event-subscription-create--update"></a>イベント サブスクリプションのプット (作成または更新)

**要求**: ``` PUT /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**ペイロード**:
```json
{
    "name": "<subscription_name>", // optional, inferred from URL. If specified must match URL subscription_name
    "properties":
    {
        "topicName": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0", // optional
        "retryPolicy":  //optional
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "persistencePolicy": "true",
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // optional
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

**応答**: HTTP 200

**ペイロード**:

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```


### <a name="get-event-subscription"></a>イベント サブスクリプションの取得

**要求**: ``` GET /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**応答**: HTTP 200

**ペイロード**:
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

### <a name="get-event-subscriptions"></a>複数のイベント サブスクリプションの取得

**要求**: ``` GET /topics/<topic_name>/eventSubscriptions?api-version=2019-01-01-preview ```

**応答**: HTTP 200

**ペイロード**:
```json
[
    {
        // same event-subscription json as that returned from Get-EventSubscription above
    },
    {
    },
    ...
]
```

### <a name="delete-event-subscription"></a>イベント サブスクリプションの削除

**要求**: ``` DELETE /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**応答**: HTTP 200、ペイロードなし


## <a name="publish-events-api"></a>イベント発行 API

### <a name="send-batch-of-events-in-event-grid-schema"></a>イベントのバッチの送信 (Event Grid スキーマで)

**要求**: ``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        "id": "<user-defined-event-id>",
        "topic": "<topic_name>",
        "subject": "",
        "eventType": "",
        "eventTime": ""
        "dataVersion": "",
        "metadataVersion": "1",
        "data": 
            ...
    }
]
```

**応答**: HTTP 200、空のペイロード


**ペイロードのフィールドの説明**
- ```Id``` は必須です。 呼び出し元によって設定される任意の文字列値を指定できます。 Event Grid では、このフィールドに対する重複検出やセマンティクスの強制は行われません。
- ```Topic``` は省略可能ですが、指定する場合は、要求 URL の topic_name と一致する必要があります
- ```Subject``` は必須です。任意の文字列値を指定できます
- ```EventType``` は必須です。任意の文字列値を指定できます
- ```EventTime``` は必須です。検証は行われませんが、適切な DateTime である必要があります。
- ```DataVersion``` は必須です。
- ```MetadataVersion``` は省略可能です。指定する場合は、値が ```"1"``` の文字列である必要があります
- ```Data``` は省略可能です。任意の JSON トークン (number、string、boolean、array、object) を指定できます

### <a name="send-batch-of-events-in-custom-schema"></a>イベントのバッチの送信 (カスタム スキーマで)

**要求**: ``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        ...
    }
]
```

**応答**: HTTP 200、空のペイロード


**ペイロードの制限**
- イベントの配列である必要があります。
- 各配列エントリは、JSON オブジェクトである必要があります。
- 他の制約はありません (ペイロードのサイズを除く)。

## <a name="examples"></a>例

### <a name="set-up-topic-with-eventgrid-schema"></a>EventGrid スキーマを使用したトピックの設定
**eventgridschema** で、イベントの発行を要求するトピックを設定します。

```json
    {
        "name": "myeventgridtopic",
        "properties":
        {
            "inputSchema": "EventGridSchema"
        }
    }
```

### <a name="set-up-topic-with-custom-schema"></a>カスタム スキーマを使用したトピックの設定
`customschema` で、イベントの発行を要求するトピックを設定します。

```json
    {
        "name": "mycustomschematopic",
        "properties":
        {
            "inputSchema": "CustomSchema"
        }
    }
```

### <a name="set-up-topic-with-cloud-event-schema"></a>クラウド イベント スキーマを使用したトピックの設定
`cloudeventschema` で、イベントの発行を要求するトピックを設定します。

```json
    {
        "name": "mycloudeventschematopic",
        "properties":
        {
            "inputSchema": "CloudEventSchemaV1_0"
        }
    }
```

### <a name="set-up-webhook-as-destination-events-to-be-delivered-in-eventgridschema"></a>WebHook を宛先とする設定 (イベントは eventgridschema で配信)
他のモジュール (HTTP エンドポイントをホストするもの) や、ネットワークまたはインターネット上にあって任意の HTTP アドレスを指定可能なエンドポイントにイベントを送信する場合には、この宛先の種類を使用します。

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "eventDeliverySchema": "eventgridschema",
            }
        }
    }
}
```

`endpointUrl` 属性に対する制限:
- null 以外である必要があります。
- 絶対 URL である必要があります。
- EventGridModule 設定で outbound__webhook__httpsOnly が true に設定されている場合は、HTTPS のみである必要があります。
- outbound__webhook__httpsOnly が false に設定されている場合は、HTTP または HTTPS にすることができます。

`eventDeliverySchema` プロパティに対する制限:
- サブスクライブしているトピックの入力スキーマと一致する必要があります。
- null にすることができます。 既定では、トピックの入力スキーマになります。

### <a name="set-up-iot-edge-as-destination"></a>IoT Edge を宛先とする設定

IoT Edge Hub にイベントを送信し、エッジ ハブのルーティング、フィルタリング、および転送サブシステムの対象になるようにするには、この宛先を使用します。

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EdgeHub",
            "properties":
            {
                "outputName": "<eventgridmodule_output_port_name>"
            }
        }
    }
}
```

### <a name="set-up-event-grid-cloud-as-destination"></a>Event Grid クラウドを宛先とする設定

クラウド (Azure) 内の Event Grid にイベントを送信する場合には、この宛先を使用します。 まず、イベントの送信先となるクラウドにユーザー トピックを設定してから、エッジ上にイベント サブスクリプションを作成する必要があります。

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EventGrid",
            "properties":
            {
                "endpointUrl": "<eventgrid_user_topic_url>",
                "sasKey": "<user_topic_sas_key>",
                "topicName": "<new value to populate in forwarded EventGridEvent.Topic>" // if not specified, the Topic field on every event gets nulled out before being sent to Azure Event Grid
            }
        }
    }
}
```

EndpointUrl:
- null 以外である必要があります。
- 絶対 URL である必要があります。
- 要求 URL パスでは、パス `/api/events` を定義する必要があります。
- クエリ文字列内に `api-version=2018-01-01` が必要です。
- EventGridModule 設定で outbound__eventgrid__httpsOnly が true に設定されている場合は (既定では true)、HTTPS のみである必要があります。
- outbound__eventgrid__httpsOnly が false に設定されている場合は、HTTP または HTTPS にすることができます。
- outbound__eventgrid__allowInvalidHostnames が false に設定されている場合は (既定では false)、以下のいずれかのエンドポイントを対象にする必要があります。
   - `eventgrid.azure.net`
   - `eventgrid.azure.us`
   - `eventgrid.azure.cn`

SasKey:
- null 以外である必要があります。

TopicName:
- Subscription.EventDeliverySchema が EventGridSchema に設定されている場合、このフィールドの値は、クラウド内の Event Grid に転送される前に、それぞれのイベントの [トピック] フィールドに配置されます。
- Subscription.EventDeliverySchema が CustomEventSchema に設定されている場合、このプロパティは無視され、カスタム イベント ペイロードが受信されたとおりに転送されます。

## <a name="set-up-event-hubs-as-a-destination"></a>宛先として Event Hubs を設定する

イベント ハブに発行するには、`endpointType` を `eventHub` に設定し、以下を指定します。

* connectionString:共有アクセス ポリシーを介して生成された、対象となる特定のイベント ハブの接続文字列。

    >[!NOTE]
    > 接続文字列はエンティティ固有である必要があります。 名前空間の接続文字列を使用すると機能しません。 エンティティ固有の接続文字列を生成するには、Azure portal で発行する特定のイベント ハブに移動し、 **[Shared access policies]/(共有アクセス ポリシー/)** をクリックして、新しいエンティティ固有の接続文字列を生成します。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-queues-as-a-destination"></a>宛先として Service Bus キューを設定する

Service Bus キューに発行するには、`endpointType` を `serviceBusQueue` に設定し、以下を指定します。

* connectionString:共有アクセス ポリシーを介して生成された、対象となる特定の Service Bus キューの接続文字列。

    >[!NOTE]
    > 接続文字列はエンティティ固有である必要があります。 名前空間の接続文字列を使用すると機能しません。 エンティティ固有の接続文字列を生成するには、Azure portal で発行する特定の Service Bus キューに移動し、 **[Shared access policies]/(共有アクセス ポリシー/)** をクリックして、新しいエンティティ固有の接続文字列を生成します。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-topics-as-a-destination"></a>宛先として Service Bus トピックを設定する

Service Bus トピックに発行するには、`endpointType` を `serviceBusTopic` に設定し、以下を指定します。

* connectionString:共有アクセス ポリシーを介して生成された、対象となる特定の Service Bus トピックの接続文字列。

    >[!NOTE]
    > 接続文字列はエンティティ固有である必要があります。 名前空間の接続文字列を使用すると機能しません。 エンティティ固有の接続文字列を生成するには、Azure portal で発行する特定の Service Bus トピックに移動し、 **[Shared access policies]/(共有アクセス ポリシー/)** をクリックして、新しいエンティティ固有の接続文字列を生成します。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-storage-queues-as-a-destination"></a>宛先としてストレージ キューを設定する

ストレージ キューに発行するには、`endpointType` を `storageQueue` に設定し、以下を指定します。

* queueName:発行先のストレージ キューの名前。
* connectionString:ストレージ キューが存在するストレージ アカウントの接続文字列。

    >[!NOTE]
    > Event Hubs、Service Bus キュー、および Service Bus トピックとは異なり、ストレージ キューに使用される接続文字列はエンティティ固有ではありません。 その代わり、ストレージ アカウントの接続文字列である必要があります。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```