---
title: Azure Event Grid イベントのイベント ハンドラーとして Azure の関数を使用する
description: Azure Functions 内で作成されてホストされる関数を、Event Grid イベントのイベント ハンドラーとして使用する方法について説明します。
ms.topic: conceptual
ms.date: 03/15/2021
ms.openlocfilehash: f547b09fe7e62eb3fa9e02bd17298a936350f871
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103496543"
---
# <a name="use-a-function-as-an-event-handler-for-event-grid-events"></a>Event Grid イベントのイベント ハンドラーとして関数を使用する

イベント ハンドラーは、イベントの送信先となる場所です。 ハンドラーでは、イベントを処理するアクションが実行されます。 一部の Azure サービスは、イベントを処理するように自動的に構成されます。**Azure Functions** はその 1 つです。 


Azure の関数をイベントのハンドラーとして使用するには、次のいずれかの方法に従います。 

-   [Event Grid トリガー](../azure-functions/functions-bindings-event-grid-trigger.md)を使用する。  **エンドポイントのタイプ** として **Azure 関数** を指定します。 さらに、関数アプリと、イベントを処理する関数を指定します。 
-   [HTTP トリガー](../azure-functions/functions-bindings-http-webhook.md)を使用する。  **エンドポイントのタイプ** として **Webhook** を指定します。 さらに、イベントを処理する関数の URL を指定します。 

お勧めするのは最初の方法 (Event Grid トリガー) です。この方法には、2 番目の方法と比較して次の利点があります。
-   Event Grid では、Event Grid トリガーが自動的に検証されます。 汎用 HTTP トリガーの場合は、[検証応答](webhook-event-delivery.md)を開発者自身が実装する必要があります。
-   Event Grid は、Event Grid イベントによってトリガーされる関数にイベントを配信する速度を、その関数がイベントを処理できるとみなされた速度に基づいて自動的に調整します。 この速度調整機能により、関数のイベント処理速度の経時的な変化が原因で関数がイベントを処理できなくなるために生じる配信エラーを防止できます。 スループットを高めて効率を向上させるには、イベント サブスクリプションでのバッチ処理を有効にします。 詳細については、「[バッチ処理を有効にする](#enable-batching)」をご覧ください。

    > [!NOTE]
    > 現時点では、**CloudEvents** スキーマでイベントが配信される場合に、関数アプリに対して Event Grid トリガーを使用できません。 代わりに、HTTP トリガーを使用してください。

## <a name="tutorials"></a>チュートリアル

|タイトル  |説明  |
|---------|---------|
| [クイック スタート: 関数を使用したイベントの処理](custom-event-to-function.md) | 処理するカスタム イベントを関数に送信します。 |
| [チュートリアル: Event Grid を使用して、アップロードされたイメージのサイズ変更を自動化する](resize-images-on-storage-blob-upload-event.md) | ユーザーは、Web アプリを使ってストレージ アカウントにイメージをアップロードします。 ストレージ BLOB が作成されると、Event Grid によって Function App にイベントが送信され、アップロードされたイメージのサイズが変更されます。 |
| [チュートリアル: ビッグ データをデータ ウェアハウスにストリーミングする](event-grid-event-hubs-integration.md) | Event Hubs によってキャプチャ ファイルが作成されると、Event Grid が Function App にイベントを送信します。 アプリは Capture ファイルを取得し、データ ウェアハウスにデータを移行します。 |
| [チュートリアル:Azure Service Bus の Azure Event Grid への統合の例](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid では、Service Bus トピックからのメッセージが、Function App とロジック アプリに送信されます。 |

## <a name="rest-example-for-put"></a>REST の例 (PUT 用)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "AzureFunction",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Web/sites/<FUNCTION APP NAME>/functions/<FUNCTION NAME>",
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 6400
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="enable-batching"></a>バッチ処理を有効にする
スループットを高めるには、サブスクリプションでバッチ処理を有効にします。 Azure portal を使用する場合は、サブスクリプションの作成時または作成後に、バッチごとの最大イベント数と優先バッチ サイズ (KB 単位) を設定できます。 

バッチ設定の構成には、Azure portal、PowerShell、CLI、または Resource Manager テンプレートを使用できます。 

### <a name="azure-portal"></a>Azure portal
UI でサブスクリプションを作成するときに、 **[イベント サブスクリプションの作成]** ページの **[高度な機能]** タブで、 **[バッチごとの最大イベント数]** と **[優先バッチ サイズ (KB 単位)]** の値を設定します。 
    
:::image type="content" source="./media/custom-event-to-function/enable-batching.png" alt-text="サブスクリプション作成時にバッチ処理を有効にする":::

既存のサブスクリプションについては、 **[Event Grid トピック]** ページの **[機能]** タブでこれらの値を更新できます。 

:::image type="content" source="./media/custom-event-to-function/features-batch-settings.png" alt-text="作成後にバッチ処理を有効にする":::

### <a name="azure-resource-manager-template"></a>Azure Resource Manager テンプレート
Azure Resource Manager テンプレートで **maxEventsPerBatch** と **preferredBatchSizeInKilobytes** を設定できます。 詳細については、[Microsoft.EventGrid eventSubscriptions テンプレートのリファレンス](/azure/templates/microsoft.eventgrid/eventsubscriptions)をご覧ください。

### <a name="azure-cli"></a>Azure CLI
コマンド [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create&preserve-view=true) または [az eventgrid event-subscription update](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_update&preserve-view=true) でパラメーター `--max-events-per-batch` または `--preferred-batch-size-in-kilobytes` を使用して、バッチ関連の設定を構成できます。

### <a name="azure-powershell"></a>Azure PowerShell
コマンドレット [New-AzEventGridSubscription](/powershell/module/az.eventgrid/new-azeventgridsubscription) または [Update-AzEventGridSubscription](/powershell/module/az.eventgrid/update-azeventgridsubscription) でパラメーター `-MaxEventsPerBatch` または `-PreferredBatchSizeInKiloBytes` を使用して、バッチ関連の設定を構成できます。

> [!NOTE]
> Event Grid トリガーを使用すると、Event Grid サービスではターゲットの Azure 関数のクライアント シークレットがフェッチされ、それを使用して Azure 関数にイベントが配信されます。 Azure Active Directory アプリケーションを使用して Azure 関数を保護する場合、一般的な Web hook アプローチを実行して、HTTP トリガーを使用する必要があります。

## <a name="next-steps"></a>次のステップ
サポートされているイベント ハンドラーの一覧については、「[イベント ハンドラー](event-handlers.md)」を参照してください。
