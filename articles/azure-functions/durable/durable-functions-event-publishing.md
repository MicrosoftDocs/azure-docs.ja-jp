---
title: Azure Event Grid への Durable Functions の発行 (プレビュー)
description: Durable Functions の Azure Event Grid 自動発行を構成する方法を説明します。
ms.topic: conceptual
ms.date: 04/25/2020
ms.openlocfilehash: 44df100a5c794abf918a09dea0f94d30ddf916d3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102175959"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Azure Event Grid への Durable Functions の発行 (プレビュー)

この記事では、オーケストレーション ライフサイクル イベント (作成、完了、失敗など) をカスタムの [Azure Event Grid トピック](../../event-grid/overview.md)に発行するように Durable Functions を設定する方法を示します。

この機能が役立つシナリオを次にいくつか示します。

* **ブルー/グリーン デプロイなどの DevOps シナリオ**: [並行デプロイ戦略](durable-functions-versioning.md#side-by-side-deployments)を実装する前に、タスクが実行されているかどうかを知ることができます。

* **高度な監視と診断のサポート**: Azure SQL Database や Azure Cosmos DB など、クエリ用に最適化された外部ストアで、オーケストレーションの状態情報を追跡できます。

* **実行時間の長いバック グラウンド アクティビティ**: 実行時間の長いバック グラウンド アクティビティに Durable Functions を使用する場合、この機能は現在の状態を知るのに役立ちます。

## <a name="prerequisites"></a>前提条件

* [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) を Durable Functions プロジェクトにインストールします。
* [Azure ストレージ エミュレーター](../../storage/common/storage-use-emulator.md) (Windows のみ) をインストールするか、既存の Azure Storage アカウントを使用します。
* [Azure CLI](/cli/azure/) をインストールするか、[Azure Cloud Shell](../../cloud-shell/overview.md) を使用します

## <a name="create-a-custom-event-grid-topic"></a>カスタムの Event Grid トピックの作成

Durable Functions からイベントを送信するための Event Grid トピックを作成します。 次の手順は、Azure CLI を使用してトピックを作成する方法を示しています。 トピックの作成は、[PowerShell を使用](../../event-grid/custom-event-quickstart-powershell.md)するか、[Azure portal を使用](../../event-grid/custom-event-quickstart-portal.md)しても行うことができます。

### <a name="create-a-resource-group"></a>リソース グループを作成する

`az group create` コマンドでリソース グループを作成します。 現時点では、Azure Event Grid ではすべてのリージョンをサポートしているわけではありません。 サポートされるリージョンについては、[Azure Event Grid の概要](../../event-grid/overview.md)に関する記事を参照してください。

```azurecli
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>カスタム トピックの作成

Event Grid のトピックは、イベントの送信先となるユーザー定義のエンドポイントになります。 `<topic_name>` は、トピックの一意の名前に置き換えてください。 トピック名は、DNS エントリになるため、一意である必要があります。

```azurecli
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>エンドポイントとキーの取得

トピックのエンドポイントを取得します。 `<topic_name>` を選択した名前で置き換えます。

```azurecli
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

トピック キーを取得します。 `<topic_name>` を選択した名前で置き換えます。

```azurecli
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

これで、トピックにイベントを送信できます。

## <a name="configure-event-grid-publishing"></a>Event Grid の発行の構成

Durable Functions プロジェクトで、`host.json` ファイルを検索します。

### <a name="durable-functions-1x"></a>Durable Functions 1.x

`eventGridTopicEndpoint` と `eventGridKeySettingName` を `durableTask` プロパティに追加します。

```json
{
  "durableTask": {
    "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName": "EventGridKey"
  }
}
```

### <a name="durable-functions-2x"></a>Durable Functions 2.x

ファイルの `durableTask` プロパティに `notifications` セクションを追加し、`<topic_name>` を自分で選択した名前に置換します。 `durableTask` または `extensions` プロパティが存在しない場合、次の例のように作成します。

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "notifications": {
        "eventGrid": {
          "topicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
          "keySettingName": "EventGridKey"
        }
      }
    }
  }
}
```

使用できる Azure Event Grid の構成プロパティについては、[host.json のドキュメント](../functions-host-json.md#durabletask)を参照してください。 `host.json` ファイルを構成すると、関数アプリから Event Grid トピックにライフサイクル イベントが送信されます。 ローカルと Azure のどちらで関数アプリを実行しても、この処理が開始されます。

Function App と `local.settings.json` で、トピック キーのアプリ設定を設定します。 次の JSON は、ローカル デバッグ用の `local.settings.json` のサンプルです。 `<topic_key>` はトピック キーで置き換えます。  

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "UseDevelopmentStorage=true",
        "AzureWebJobsDashboard": "UseDevelopmentStorage=true",
        "EventGridKey": "<topic_key>"
    }
}
```

[ストレージ エミュレーター](../../storage/common/storage-use-emulator.md) (Windows のみ) を使用している場合は、それが動作していることを確認します。 実行する前に `AzureStorageEmulator.exe clear all` コマンドを実行することをお勧めします。

既存の Azure Storage アカウントを使用している場合は、`local.settings.json` の `UseDevelopmentStorage=true` をその接続文字列に置き換えます。

## <a name="create-functions-that-listen-for-events"></a>イベントをリッスンする関数の作成

Azure portal を使用して、ご使用の Durable Functions アプリによって発行されたイベントをリッスンする別の関数アプリを作成します。 Event Grid トピックと同じリージョンに配置することをお勧めします。

### <a name="create-an-event-grid-trigger-function"></a>イベント グリッド トリガー関数の作成

1. 関数アプリで、 **[関数]** を選択し、 **[+ 追加]** を選択します。 

   :::image type="content" source="./media/durable-functions-event-publishing/function-add-function.png" alt-text="Azure portal で関数を追加します。" border="true":::

1. 「**Event Grid**」を検索し、 **[Azure Event Grid trigger]\(Azure Event Grid トリガー\)** テンプレートを選択します。 

    :::image type="content" source="./media/durable-functions-event-publishing/function-select-event-grid-trigger.png" alt-text="Azure portal で Event Grid トリガー テンプレートを選択します。" border="true":::

1. 新しいトリガーに名前を付け、 **[関数の作成]** を選択します。

    :::image type="content" source="./media/durable-functions-event-publishing/function-name-event-grid-trigger.png" alt-text="Azure portal で Event Grid トリガーの名前を付けます。" border="true":::


    次のコードを含む関数が作成されます。

    # <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

    ```csharp
    #r "Newtonsoft.Json"
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    using Microsoft.Extensions.Logging;

    public static void Run(JObject eventGridEvent, ILogger log)
    {
        log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
    }
    ```

   # <a name="javascript"></a>[JavaScript](#tab/javascript)

   ```javascript
   module.exports = async function(context, eventGridEvent) {
       context.log(typeof eventGridEvent);
       context.log(eventGridEvent);
   }
   ```

---

### <a name="add-an-event-grid-subscription"></a>Event Grid のサブスクリプションを追加する

ここでは、作成した Event Grid トピックの Event Grid サブスクリプションを追加できます。 詳しくは、「[Azure Event Grid の概念](../../event-grid/concepts.md)」をご覧ください。

1. 新しい関数で、 **[統合]** を選択し、 **[イベント グリッド トリガー (eventGridEvent)]** を選択します。 

    :::image type="content" source="./media/durable-functions-event-publishing/eventgrid-trigger-link.png" alt-text="[イベント グリッド トリガー] リンクの選択。" border="true":::

1. **[Create Event Grid Description]\(Event Grid の説明の作成\)** を選択します。

    :::image type="content" source="./media/durable-functions-event-publishing/create-event-grid-subscription.png" alt-text="Event Grid のサブスクリプションを作成します。" border="true":::

1. イベント サブスクリプションに名前を付け、トピックの種類として **[Event Grid トピック]** を選択します。 

1. サブスクリプションを選択します。 Event Grid トピック用に作成したリソース グループとリソースを選択します。 

1. **［作成］** を選択します

    :::image type="content" source="./media/durable-functions-event-publishing/event-grid-subscription-details.png" alt-text="Event Grid のサブスクリプションを作成します。" border="true":::

これで、ライフサイクル イベントを受信する準備が整いました。

## <a name="run-durable-functions-app-to-send-the-events"></a>Durable Functions アプリを実行してイベントを送信する

前に構成した Durable Functions プロジェクトで、ご使用のローカル コンピューターでデバッグを開始し、オーケストレーションを開始します。 アプリによって、Durable Functions ライフサイクル イベントが Event Grid に発行されます。 Azure portal でログをチェックして、作成したリスナー関数が Event Grid によってトリガーされることを確認します。

```
2019-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2019-04-20T09:28:21.104 [Info] {
    "id": "054fe385-c017-4ce3-b38a-052ac970c39d",
    "subject": "durable/orchestrator/Running",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Running"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2019-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2019-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2019-04-20T09:28:37.098 [Info] {
    "id": "8cf17246-fa9c-4dad-b32a-5a868104f17b",
    "subject": "durable/orchestrator/Completed",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Completed"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2019-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>イベント スキーマ

次の一覧では、ライフサイクル イベントのスキーマについて説明します。

* **`id`** :Event Grid イベントの一意識別子。
* **`subject`** :イベントの件名へのパス。 `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` は`Running`、`Completed`、`Failed`、`Terminated` になります。  
* **`data`** :Durable Functions 固有のパラメーター。
  * **`hubName`** :[TaskHub](durable-functions-task-hubs.md) の名前。
  * **`functionName`** :オーケストレーター関数の名前。
  * **`instanceId`** :Durable Functions のインスタンス ID。
  * **`reason`** :追跡イベントに関連付けられている追加のデータ。 詳しくは、「[Durable Functions における診断 (Azure Functions)](durable-functions-diagnostics.md)」をご覧ください
  * **`runtimeStatus`** :オーケストレーションのランタイム状態。 実行中、完了、失敗、取り消し済みです。
* **`eventType`** : "orchestratorEvent"
* **`eventTime`** :イベント時間 (UTC)。
* **`dataVersion`** :ライフサイクル イベント スキーマのバージョン。
* **`metadataVersion`** :メタデータのバージョン。
* **`topic`** :イベント グリッド トピック リソース。

## <a name="how-to-test-locally"></a>ローカルでテストする方法

ローカルでテストするには、「[Azure Functions の Event Grid トリガーのローカル デバッグ](../functions-debug-event-grid-trigger-local.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Durable Functions でのインスタンスの管理について](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Durable Functions でのバージョン管理について](durable-functions-versioning.md)
