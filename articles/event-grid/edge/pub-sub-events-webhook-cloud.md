---
title: クラウドでイベントを発行し、サブスクライブする - Azure Event Grid IoT Edge | Microsoft Docs
description: Event Grid on IoT Edge で Webhook を使用して、クラウドでイベントを発行し、サブスクライブします
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 12bcb54f4bfdf17209324febeba380ff7789fc0f
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998987"
---
# <a name="tutorial-publish-subscribe-to-events-in-cloud"></a>チュートリアル:クラウドでイベントを発行してサブスクライブする

この記事では、Event Grid on IoT Edge を使用してイベントを発行およびサブスクライブするために必要なすべての手順について説明します。 このチュートリアルでは、Azure 関数をイベント ハンドラーとして使用します。 その他の送信先の種類については、「[イベント ハンドラー](event-handlers.md)」を参照してください。

先に進む前に、「[Event Grid の概念](concepts.md)」を参照して、Event Grid のトピックとサブスクリプションがなんであるかを理解してください。

## <a name="prerequisites"></a>前提条件 
このチュートリアルを完了するには、以下が必要になります。

* **Azure サブスクリプション**: まだお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free)を作成してください。 
* **Azure IoT Hub および IoT Edge デバイス**: まだお持ちでない場合は、[Linux](../../iot-edge/quickstart-linux.md) または [Windows](../../iot-edge/quickstart.md) デバイスのクイック スタートの手順に従ってください。

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-an-azure-function-in-the-azure-portal"></a>Azure portal で Azure 関数を作成する

Azure 関数を作成するには、[チュートリアル](../../azure-functions/functions-create-first-azure-function.md)で説明されている手順に従います。 

コード スニペットを以下のコードに置き換えます。

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);

    log.LogInformation($"C# HTTP trigger received {data}.");
    return data != null
        ? (ActionResult)new OkResult()
        : new BadRequestObjectResult("Please pass in the request body");
}
```

新しい関数で、右上の **[関数の URL の取得]** を選択し、既定値 ( **[関数キー]** ) を選択して、 **[コピー]** を選択します。 関数 URL は、このチュートリアルの後の方で使用します。

> [!NOTE]
> イベントへの反応と EventGrid イベント トリガーの使用に関するその他のサンプルとチュートリアルについては、[Azure Functions](../../azure-functions/functions-overview.md) のドキュメントを参照してください。

## <a name="create-a-topic"></a>トピックを作成する

イベントの発行元として、イベント グリッド トピックを作成する必要があります。 トピックは、発行元からイベントを送信する際に宛先となるエンド ポイントを参照します。

1. 次の内容を含む topic2.json を作成します。 ペイロードの詳細については、[API のドキュメント](api.md)を参照してください。

    ```json
         {
          "name": "sampleTopic2",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. 次のコマンドを実行して、トピックを作成します。 HTTP 状態コード 200 OK が返される必要があります。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```
1. 次のコマンドを実行して、トピックが正常に作成されたことを確認します。 HTTP 状態コード 200 OK が返される必要があります。

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

   サンプル出力:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2",
            "name": "sampleTopic2",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>イベント サブスクリプションの作成

サブスクライバーは、トピックに発行されたイベントの受信登録ができます。 イベントを受信するには、サブスクライバーは関心のあるトピックに関する Event Grid サブスクリプションを作成する必要があります。

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. 次の内容を含む subscription2.json を作成します。 ペイロードの詳細については、[API のドキュメント](api.md)を参照してください。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

   >[!NOTE]
   > **endpointType** によって、サブスクライバーが Webhook であることが指定されます。  **endpointUrl** によって、サブスクライバーがイベントをリッスンしている URL が指定されます。 この URL は、先ほど設定した Azure Functions のサンプルに対応しています。
2. 次のコマンドを実行して、サブスクリプションを作成します。 HTTP 状態コード 200 OK が返される必要があります。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```
3. 次のコマンドを実行して、サブスクリプションが正常に作成されたことを確認します。 HTTP 状態コード 200 OK が返される必要があります。

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```

    サンプル出力:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2/eventSubscriptions/sampleSubscription2",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription2",
          "properties": {
            "Topic": "sampleTopic2",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>イベントを発行する

1. 次の内容を含む event2.json を作成します。 ペイロードの詳細については、[API のドキュメント](api.md)を参照してください。

    ```json
        [
          {
            "id": "eventId-func-1",
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
1. 次のコマンドを実行して、イベントを発行します

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>イベントの配信を確認する

Azure portal で、関数の **[モニター]** オプションの下に、配信されたイベントを表示できます。

## <a name="cleanup-resources"></a>リソースをクリーンアップする

* 次のコマンドを実行して、トピックとそのすべてのサブスクリプションを削除します

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

* Azure portal で、作成した Azure 関数を削除します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、イベント グリッド トピックとサブスクリプションを作成し、イベントを発行しました。 基本的な手順が理解できたら、次の記事を参照してください。

* Azure Event Grid on IoT Edge の使用に伴う問題のトラブルシューティングについては、[トラブルシューティング ガイド](troubleshoot.md)を参照してください。
* [フィルター](advanced-filtering.md)を使用してサブスクリプションを作成または更新します。
* [Linux](persist-state-linux.md) または [Windows](persist-state-windows.md) で Event Grid モジュールの永続化を設定します
* [ドキュメント](configure-client-auth.md)に従って、クライアント認証を構成します
* この[チュートリアル](forward-events-event-grid-cloud.md)に従って、クラウド内の Azure Event Grid にイベントを転送します
* [エッジでのトピックとサブスクリプションの監視](monitor-topics-subscriptions.md)
