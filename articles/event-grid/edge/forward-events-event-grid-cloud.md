---
title: エッジ イベントを Event Grid クラウドに転送する - Azure Event Grid IoT Edge | Microsoft Docs
description: エッジ イベントを Event Grid クラウドに転送します
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: bfe150a45c70bc5bed18f8e929c9567905cd38f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171603"
---
# <a name="tutorial-forward-events-to-event-grid-cloud"></a>チュートリアル: イベントを Event Grid クラウドに転送する

この記事では、Azure クラウドの Event Grid にエッジ イベントを転送するために必要なすべての手順について説明します。 このようなことが必要になる理由としては、以下が考えられます。

* クラウドのエッジ イベントに対応します。
* クラウドの Event Grid にイベントを転送し、Azure Event Hubs または Azure Storage キューを使用して、クラウドで処理する前にイベントをバッファーに格納します。

 このチュートリアルを最後まで読むには、[エッジ](concepts.md)および [Azure](../concepts.md) での Event Grid の概念を理解している必要があります。 その他の送信先の種類については、「[イベント ハンドラー](event-handlers.md)」を参照してください。 

## <a name="prerequisites"></a>前提条件 
このチュートリアルを完了するには、以下が必要になります。

* **Azure サブスクリプション**: まだお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free)を作成してください。 
* **Azure IoT Hub および IoT Edge デバイス**: まだお持ちでない場合は、[Linux](../../iot-edge/quickstart-linux.md) または [Windows](../../iot-edge/quickstart.md) デバイスのクイック スタートの手順に従ってください。

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)] 
## <a name="create-event-grid-topic-and-subscription-in-cloud"></a>クラウドで Event Grid トピックとサブスクリプションを作成する

[こちらのチュートリアル](../custom-event-quickstart-portal.md)に従って、Event Grid トピックとサブスクリプションをクラウドに作成します。 チュートリアルの後半で使用するので、新しく作成したトピックの `topicURL`、`sasKey`、`topicName` を記録しておきます。

たとえば、米国西部で `testegcloudtopic` という名前のトピックを作成した場合、値は次のようになります。

* **TopicUrl**: `https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events`
* **TopicName**: `testegcloudtopic`
* **SasKey**: トピックの **AccessKey** でわかります。 **key1** を使用します。

## <a name="create-event-grid-topic-at-the-edge"></a>エッジで Event Grid トピックを作成する

1. 次の内容で topic3.json を作成します。 ペイロードの詳細については、[API のドキュメント](api.md)を参照してください。

    ```json
        {
          "name": "sampleTopic3",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. 次のコマンドを実行して、トピックを作成します。 HTTP 状態コード 200 OK が返される必要があります。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```
1. 次のコマンドを実行して、トピックが正常に作成されたことを確認します。 HTTP 状態コード 200 OK が返される必要があります。

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

   サンプル出力:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3",
            "name": "sampleTopic3",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```
  
## <a name="create-event-grid-subscription-at-the-edge"></a>エッジで Event Grid サブスクリプションを作成する

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. 次の内容で subscription3.json を作成します。 ペイロードの詳細については、[API のドキュメント](api.md)を参照してください。

   ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                        "endpointUrl": "<your-event-grid-cloud-topic-endpoint-url>?api-version=2018-01-01",
                        "sasKey": "<your-event-grid-topic-saskey>",
                        "topicName": null
              }
            }
          }
    }
   ```

   >[!NOTE]
   > **endpointUrl** では、クラウドでの Event Grid トピックの URL を指定します。 **sasKey** では、Event Grid クラウド トピックのキーを参照します。 **topicName** の値は、Event Grid へのすべての送信イベントにスタンプするために使用されます。 これは、Event Grid ドメイン トピックに投稿するときに役立ちます。 Event Grid ドメイン トピックについて詳しくは、[イベント ドメイン](../event-domains.md)に関するページをご覧ください

    たとえば、次のように入力します。
  
    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                 "sasKey": "<your-event-grid-topic-saskey>",
                 "topicName": null
              }
            }
          }
        }
    ```

2. 次のコマンドを実行して、サブスクリプションを作成します。 HTTP 状態コード 200 OK が返される必要があります。

     ```sh
     curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
     ```

3. 次のコマンドを実行して、サブスクリプションが正常に作成されたことを確認します。 HTTP 状態コード 200 OK が返される必要があります。

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
    ```

    サンプル出力:

    ```json
         {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3/eventSubscriptions/sampleSubscription3",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription3",
          "properties": {
            "Topic": "sampleTopic3",
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                "sasKey": "<your-event-grid-topic-saskey>",
                "topicName": null
              }
            }
          }
        }
    ```

## <a name="publish-an-event-at-the-edge"></a>エッジでイベントを発行する

1. 次の内容で event3.json を作成します。 ペイロードについて詳しくは、[API のドキュメント](api.md)をご覧ください。

    ```json
        [
          {
            "id": "eventId-egcloud-0",
            "eventType": "recordInserted",
            "subject": "myapp/vehicles/motorcycles",
            "eventTime": "2019-07-28T21:03:07+00:00",
            "dataVersion": "1.0",
            "data": {
              "make": "Ducati",
              "model": "Monster"
            }
          }
        ]
    ```

1. 次のコマンドを実行します。

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview
    ```

## <a name="verify-edge-event-in-cloud"></a>クラウドでエッジ イベントを確認する

クラウド トピックによって配信されたイベントの表示については、[チュートリアル](../custom-event-quickstart-portal.md)をご覧ください。

## <a name="cleanup-resources"></a>リソースをクリーンアップする

* 次のコマンドを実行して、トピックとそのすべてのサブスクリプションを削除します

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

* クラウド (Azure Event Grid) で作成されたトピックとサブスクリプションも削除します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、エッジでイベントを発行し、Azure クラウドの Event Grid に転送しました。 これで、クラウドの Event Grid に転送する基本的な手順がわかりました。

* Azure Event Grid on IoT Edge の使用に伴う問題のトラブルシューティングについては、[トラブルシューティング ガイド](troubleshoot.md)を参照してください。
* この[チュートリアル](forward-events-iothub.md)に従って、イベントを IoTHub に転送します
* この[チュートリアル](pub-sub-events-webhook-cloud.md)に従って、イベントをクラウドの Webhook に転送します
* [Edge でのトピックとサブスクリプションの監視](monitor-topics-subscriptions.md)
