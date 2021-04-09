---
title: Event Grid イベントを IoTHub に転送する - Azure Event Grid IoT Edge | Microsoft Docs
description: Event Grid イベントを IoTHub に転送する
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 36dc7d098892fb2be7c2ba3d75de7c7adef1a4f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171552"
---
# <a name="tutorial-forward-events-to-iothub"></a>チュートリアル: イベントを IoTHub に転送する

この記事では、ルートを使用して Event Grid イベントを他の IoT Edge モジュール、IoTHub に転送するために必要な手順をすべて説明します。 このようなことが必要になる理由としては、以下が考えられます。

* edgeHub のルーティングを使用して既存の投資を引き続き使用する
* デバイスのすべてのイベントを IoT Hub のみを介してルーティングする方が好ましい

このチュートリアルを完了するには、以下の概念を理解している必要があります。

- [Event Grid の概念](concepts.md)
- [IoT Edge ハブ](../../iot-edge/module-composition.md) 

## <a name="prerequisites"></a>前提条件 
このチュートリアルを完了するには、以下が必要になります。

* **Azure サブスクリプション**: まだお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free)を作成してください。 
* **Azure IoT Hub および IoT Edge デバイス**: まだお持ちでない場合は、[Linux](../../iot-edge/quickstart-linux.md) または [Windows](../../iot-edge/quickstart.md) デバイスのクイック スタートの手順に従ってください。

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-topic"></a>トピックの作成

イベントの発行元として、イベント グリッド トピックを作成する必要があります。 このトピックは、後で発行元からイベントを送信する際に宛先となるエンド ポイントを参照します。

1. 以下の内容を含む topic4.json を作成します。 ペイロードの詳細については、[API のドキュメント](api.md)を参照してください。

   ```json
    {
          "name": "sampleTopic4",
          "properties": {
            "inputschema": "eventGridSchema"
          }
    }
    ```
1. 次のコマンドを実行して、トピックを作成します。 HTTP 状態コード 200 OK が返される必要があります。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

1. 次のコマンドを実行して、トピックが正常に作成されたことを確認します。 HTTP 状態コード 200 OK が返される必要があります。

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

   サンプル出力:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4",
            "name": "sampleTopic4",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-event-subscription"></a>イベント サブスクリプションの作成

サブスクライバーは、トピックに発行されたイベントの受信登録ができます。 イベントを受信するには、関心のあるトピックに関する Event Grid サブスクリプションを作成する必要があります。

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. 次の内容を含む subscription4.json を作成します。 ペイロードの詳細については、[API のドキュメント](api.md)を参照してください。

   ```json
    {
          "properties": {
                "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
    }
   ```

   >[!NOTE]
   > `endpointType` は、サブスクライバーが `edgeHub` であることを指定します。 `outputName` は出力を指定するものであり、edgeHub のこのサブスクリプションに一致するイベントは、Event Grid モジュールによってここにルーティングされます。 たとえば、上のサブスクリプションに一致するイベントは、`/messages/modules/eventgridmodule/outputs/sampleSub4` に書き込まれます。
2. 次のコマンドを実行して、サブスクリプションを作成します。 HTTP 状態コード 200 OK が返される必要があります。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```
3. 次のコマンドを実行して、サブスクリプションが正常に作成されたことを確認します。 HTTP 状態コード 200 OK が返される必要があります。

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```

    サンプル出力:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4/eventSubscriptions/sampleSubscription4",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription4",
          "properties": {
            "Topic": "sampleTopic4",
            "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
        }
    ```

## <a name="set-up-an-edge-hub-route"></a>エッジ ハブ ルートの設定

エッジ ハブのルートを次のように更新して、イベント サブスクリプションのイベントが IoTHub に転送されるようにします。

1. [Azure ポータル](https://ms.portal.azure.com)
1. **[IoT Hub]** に移動します。
1. メニューの **[IoT Edge]** を選択します
1. デバイスの一覧で、ターゲット デバイスの ID を選択します。
1. **[Set Modules] \(モジュールの設定)** を選択します。
1. **[次へ]** を選択して、ルートのセクションに進みます。
1. ルートのところに、新しいルートを追加します

  ```sh
  "fromEventGridToIoTHub":"FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
  ```

  たとえば、次のように入力します。

  ```json
  {
      "routes": {
        "fromEventGridToIoTHub": "FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
      }
  }
  ```

   >[!NOTE]
   > 上のルートは、このサブスクリプションに一致するすべてのイベントを IoT ハブに転送します。 [Edge ハブ ルーティング](../../iot-edge/module-composition.md)機能を使用すると、さらにフィルター処理を行い、Event Grid イベントを他の IoT Edge モジュールにルーティングすることもできます。

## <a name="setup-iot-hub-route"></a>IoT Hub ルートの設定

[IoT Hub のルーティングに関するチュートリアル](../../iot-hub/tutorial-routing.md)を参考に IoT ハブからのルートを設定し、Event Grid モジュールから転送されたイベントを表示できるようにします。 チュートリアルが複雑にならないように、クエリには `true` を使用します。  



## <a name="publish-an-event"></a>イベントを発行する

1. 次の内容を含む event4.json を作成します。 ペイロードの詳細については、[API のドキュメント](api.md)を参照してください。

    ```json
        [
          {
            "id": "eventId-iothub-1",
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

1. 次のコマンドを実行して、イベントを発行します。

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>イベントの配信を確認する

イベントを表示する手順については、IoT Hub の[ルーティングに関するチュートリアル](../../iot-hub/tutorial-routing.md)を参照してください。

## <a name="cleanup-resources"></a>リソースをクリーンアップする

* 次のコマンドを実行して、エッジのトピックとそのすべてのサブスクリプションを削除します。

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```
* クラウドで IoTHub のルーティングを設定する際に作成したリソースも、すべて削除します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、イベント グリッド トピックとエッジ ハブ サブスクリプションを作成し、イベントを発行しました。 これでエッジ ハブへの転送の基本的な手順が理解できたので、次に以下の記事を参照してください。

* Azure Event Grid on IoT Edge の使用に伴う問題のトラブルシューティングについては、[トラブルシューティング ガイド](troubleshoot.md)を参照してください。
* イベントをパーティション分割するには、[エッジ ハブ](../../iot-edge/module-composition.md) ルート フィルターを使用します
* [Linux](persist-state-linux.md) または [Windows](persist-state-windows.md) で Event Grid モジュールの永続化を設定します
* [ドキュメント](configure-client-auth.md)に従って、クライアント認証を構成します
* この[チュートリアル](forward-events-event-grid-cloud.md)に従って、クラウド内の Azure Event Grid にイベントを転送します
* [Edge でのトピックとサブスクリプションの監視](monitor-topics-subscriptions.md)