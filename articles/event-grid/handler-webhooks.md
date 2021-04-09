---
title: Azure Event Grid イベントに対するイベント ハンドラーとしての Webhook
description: Azure Event Grid イベントのイベント ハンドラーとして Webhook を使用する方法について説明します。 Azure Automation Runbook と Logic Apps は、Webhook を介してイベント ハンドラーとしてサポートされます。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: aa20c75789a18b93f787134dffe165e60ff1ab6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91875803"
---
# <a name="webhooks-automation-runbooks-logic-apps-as-event-handlers-for-azure-event-grid-events"></a>Azure Event Grid イベントに対するイベント ハンドラーとしての Webhook、Automation Runbook、Logic Apps
イベント ハンドラーは、イベントの送信先となる場所です。 ハンドラーは、さらにいくつかのアクションを行ってイベントを処理します。 一部の Azure サービスは、イベントを処理するように自動的に構成されます。 イベント処理のための Webhook を使うこともできます。 Webhook は、イベント処理用に Azure でホストする必要はありません。 Event Grid では、HTTPS Webhook エンドポイントのみがサポートされています。

> [!NOTE]
> - Azure Automation Runbook と Logic Apps は、Webhook を介してイベント ハンドラーとしてサポートされます。 
> - Azure 関数をイベント ハンドラーとして構成するための **エンドポイントの種類** として **Webhook** を使用できますが、エンドポイントの種類としては **Azure 関数** を使用してください。 詳細については、[イベント ハンドラーとしての Azure 関数](handler-functions.md)に関する記事を参照してください。

## <a name="webhooks"></a>Webhooks
イベント ハンドラーとしての Webhook の使用の概要と例については、次の記事を参照してください。 

|タイトル  |説明  |
|---------|---------|
| クイック スタート: [Azure CLI](custom-event-quickstart.md)、[PowerShell](custom-event-quickstart-powershell.md)、および[ポータル](custom-event-quickstart-portal.md)を使用してカスタム イベントを作成およびルーティングする。 | カスタム イベントを Webhook に送信する方法を示します。 |
| クイック スタート: [Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)、[PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json)、および[ポータル](blob-event-quickstart-portal.md)を使用して、Blob Storage イベントをカスタム Web エンドポイントにルーティングする。 | Blob Storage イベントを Webhook に送信する方法を示します。 |
| [クイック スタート: コンテナー レジストリ イベントを送信する](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Azure CLI を使って Container Registry イベントを送信する方法を示します。 |
| [概要: HTTP エンドポイントへのイベントを受け取る](receive-events.md) | イベント サブスクリプションからイベントを受信する HTTP エンドポイントを検証した後、イベントを逆シリアル化する方法について説明します。 |


## <a name="azure-automation"></a>Azure Automation
Azure Automation Runbook を使用して、イベントを処理できます。 自動化された Runbook を使用したイベントの処理は、Webhook を介してサポートされています。 Runbook 用の Webhook を作成した後、Webhook ハンドラーを使用します。 例については、次のチュートリアルを参照してください。 

|タイトル  |説明  |
|---------|---------|
|[チュートリアル:Azure Automation と Event Grid および Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |イベントを送信する仮想マシンを作成します。 このイベントは、仮想マシンをタグ付けする Automation Runbook をトリガーし、Microsoft Teams チャネルに送信されるメッセージをトリガーします。 |


## <a name="logic-apps"></a>Logic Apps
**Logic Apps** を使用して、Event Grid イベントを処理するビジネス プロセスを実装します。 このシナリオでは、Webhook を明示的に作成しません。 Event Grid からのイベントを処理するようにロジック アプリを構成すると、Webhook が自動的に作成されます。 例については次のチュートリアルを参照してください。 

|タイトル  |説明  |
|---------|---------|
| [チュートリアル:Azure Event Grid と Logic Apps で仮想マシンの変更を監視する](monitor-virtual-machine-changes-event-grid-logic-app.md) | ロジック アプリは仮想マシンへの変更を監視し、それらの変更に関する電子メールを送信します。 |
| [チュートリアル:Logic Apps を使用して Azure IoT Hub イベントに関する電子メール通知を送信する](publish-iot-hub-events-to-logic-apps.md) | ロジック アプリは、IoT ハブにデバイスが追加されるたびに、通知の電子メールを送信します。 |
| [チュートリアル:Azure Functions と Azure Logic Apps を使用して、Azure Event Grid 経由で受信した Azure Service Bus イベントに応答する](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid は、Service Bus トピックから関数アプリとロジック アプリにメッセージを送信します。 |

## <a name="rest-example-for-put"></a>REST の例 (PUT 用)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "WebHook",
            "properties": 
            {
                "endpointUrl": "<WEB HOOK URL>",
                "maxEventsPerBatch": 1,
                "preferredBatchSizeInKilobytes": 64
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>次のステップ
サポートされているイベント ハンドラーの一覧については、「[イベント ハンドラー](event-handlers.md)」を参照してください。 
