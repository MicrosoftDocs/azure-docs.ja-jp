---
title: Azure Event Grid への Durable Functions の発行 (プレビュー)
description: Durable Functions の Azure Event Grid 自動発行を構成する方法を説明します。
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: glenga
ms.openlocfilehash: c07a42349fbd81a46b1b7cd9bcad1978f891a6b2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58136363"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Azure Event Grid への Durable Functions の発行 (プレビュー)

この記事では、オーケストレーション ライフサイクル イベント (作成、完了、失敗など) をカスタムの [Azure Event Grid トピック](https://docs.microsoft.com/azure/event-grid/overview)に発行するように Durable Functions を設定する方法を示します。

この機能が役立つシナリオを次にいくつか示します。

* **ブルー/グリーン デプロイなどの DevOps シナリオ**: [並行デプロイ戦略](durable-functions-versioning.md#side-by-side-deployments)を実装する前に、タスクが実行されているかどうかを知ることができます。

* **高度な監視と診断のサポート**: SQL データベースや CosmosDB など、クエリ用に最適化された外部ストアで、オーケストレーションの状態情報を追跡できます。

* **実行時間の長いバック グラウンド アクティビティ**: 実行時間の長いバック グラウンド アクティビティに Durable Functions を使用する場合、この機能は現在の状態を知るのに役立ちます。

## <a name="prerequisites"></a>前提条件

* [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) 1.3.0-rc 以降を Durable Functions プロジェクトにインストールします。
* [Azure ストレージ エミュレーター](https://docs.microsoft.com/azure/storage/common/storage-use-emulator)をインストールします。
* [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) をインストールするか、[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) を使用します

## <a name="create-a-custom-event-grid-topic"></a>カスタムのイベント グリッド トピックの作成

Durable Functions からイベントを送信するためのイベント グリッド トピックを作成します。 次の手順は、Azure CLI を使用してトピックを作成する方法を示しています。 PowerShell または Azure Portal を使用して行う方法については、次の記事をご覧ください。

* [EventGrid Quickstarts: カスタム イベントの作成- PowerShell](https://docs.microsoft.com/azure/event-grid/custom-event-quickstart-powershell)
* [EventGrid Quickstarts: カスタム イベントの作成 - Azure portal](https://docs.microsoft.com/azure/event-grid/custom-event-quickstart-portal)

### <a name="create-a-resource-group"></a>リソース グループの作成

`az group create` コマンドでリソース グループを作成します。 現時点では、Azure Event Grid ではすべてのリージョンをサポートしているわけではありません。 サポートされるリージョンについては、[Azure Event Grid の概要](https://docs.microsoft.com/azure/event-grid/overview)に関する記事を参照してください。

```bash
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>カスタム トピックの作成

イベント グリッドのトピックは、イベントの送信先となるユーザー定義のエンドポイントになります。 `<topic_name>` は、トピックの一意の名前に置き換えてください。 トピック名は、DNS エントリになるため、一意である必要があります。

```bash
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>エンドポイントとキーの取得

トピックのエンドポイントを取得します。 `<topic_name>` を選択した名前で置き換えます。

```bash
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

トピック キーを取得します。 `<topic_name>` を選択した名前で置き換えます。

```bash
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

これで、トピックにイベントを送信できます。

## <a name="configure-azure-event-grid-publishing"></a>Azure Event Grid の発行の構成

Durable Functions プロジェクトで、`host.json` ファイルを検索します。

`eventGridTopicEndpoint` と `eventGridKeySettingName` を `durableTask` プロパティに追加します。

```json
{
    "durableTask": {
        "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "eventGridKeySettingName": "EventGridKey"
    }
}
```

使用できる Azure Event Grid の構成プロパティについては、[host.json のドキュメント](../functions-host-json.md#durabletask)を参照してください。 `host.json` ファイルを構成すると、関数アプリからイベント グリッド トピックにライフサイクル イベントが送信されます。 これは、ローカルと Azure のどちらで関数アプリを実行する場合にも機能します。

Function App と `local.setting.json` で、トピック キーのアプリ設定を設定します。 次の JSON は、ローカル デバッグ用の `local.settings.json` のサンプルです。 `<topic_key>` はトピック キーで置き換えます。  

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

[ストレージ エミュレーター](https://docs.microsoft.com/azure/storage/common/storage-use-emulator)が動作していることを確認します。 実行する前に `AzureStorageEmulator.exe clear all` コマンドを実行することをお勧めします。

## <a name="create-functions-that-listen-for-events"></a>イベントをリッスンする関数の作成

Function App を作成します。 イベント グリッド トピックと同じリージョンに配置することをお勧めします。

### <a name="create-an-event-grid-trigger-function"></a>イベント グリッド トリガー関数の作成

ライフサイクル イベントを受け取る関数を作成します。 **[カスタム関数]** を選択します。

![カスタム関数の作成の選択。](./media/durable-functions-event-publishing/functions-portal.png)

イベント グリッド トリガーを選択し、[`C#`] を選択します。

![イベント グリッド トリガーの選択。](./media/durable-functions-event-publishing/eventgrid-trigger.png)

関数の名前を入力し、[`Create`] を選択します。

![イベント グリッド トリガーの作成。](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

次のコードを含む関数が作成されます。

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

[`Add Event Grid Subscription`] を選択します。 この操作では、作成したイベント グリッド トピックのイベント グリッド サブスクリプションを追加します。 詳しくは、「[Azure Event Grid の概念](https://docs.microsoft.com/azure/event-grid/concepts)」をご覧ください

![[イベント グリッド トリガー] リンクの選択。](./media/durable-functions-event-publishing/eventgrid-trigger-link.png)

**[トピックの種類]** に [`Event Grid Topics`] を選択します。 イベント グリッド トピック用に作成したリソース グループを選択します。 次に、イベント グリッド トピックのインスタンスを選択します。 [`Create`] をクリックします。

![Event Grid のサブスクリプションを作成する。](./media/durable-functions-event-publishing/eventsubscription.png)

これで、ライフサイクル イベントを受信する準備が整いました。

## <a name="create-durable-functions-to-send-the-events"></a>イベントを送信する Durable Functions を作成する

Durable Functions プロジェクトで、ローカル コンピューターでのデバッグを開始します。  次のコードは、Durable Functions のテンプレート コードと同じです。 ローカル コンピューターに `host.json` と `local.settings.json` を既に構成してあります。

```csharp
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace LifeCycleEventSpike
{
    public static class Sample
    {
        [FunctionName("Sample")]
        public static async Task<List<string>> RunOrchestrator(
            [OrchestrationTrigger] DurableOrchestrationContext context)
        {
            var outputs = new List<string>();

            // Replace "hello" with the name of your Durable Activity Function.
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Tokyo"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Seattle"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "London"));

            // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
            return outputs;
        }

        [FunctionName("Sample_Hello")]
        public static string SayHello([ActivityTrigger] string name, ILogger log)
        {
            log.LogInformation($"Saying hello to {name}.");
            return $"Hello {name}!";
        }

        [FunctionName("Sample_HttpStart")]
        public static async Task<HttpResponseMessage> HttpStart(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
            [OrchestrationClient]DurableOrchestrationClient starter,
            ILogger log)
        {
            // Function input comes from the request content.
            string instanceId = await starter.StartNewAsync("Sample", null);
            log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
            return starter.CreateCheckStatusResponse(req, instanceId);
        }
    }
}
```

Postman またはブラウザーで `Sample_HttpStart` を呼び出す場合、Durable Function はライフサイクル イベントの送信を開始します。 通常、ローカル デバッグのエンドポイントは `http://localhost:7071/api/Sample_HttpStart` です。

Azure Portal で作成した関数からのログをご覧ください。

```
2018-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2018-04-20T09:28:21.104 [Info] {
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
    "eventTime": "2018-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2018-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2018-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2018-04-20T09:28:37.098 [Info] {
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
    "eventTime": "2018-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2018-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>イベント スキーマ

次の一覧では、ライフサイクル イベントのスキーマについて説明します。

* **`id`**:イベント グリッド イベントの一意識別子。
* **`subject`**:イベントの件名へのパス。 `durable/orchestrator/{orchestrationRuntimeStatus}` `{orchestrationRuntimeStatus}` は`Running`、`Completed`、`Failed`、`Terminated` になります。  
* **`data`**:Durable Functions 固有のパラメーター。
  * **`hubName`**:[TaskHub](durable-functions-task-hubs.md) の名前。
  * **`functionName`**:オーケストレーター関数の名前。
  * **`instanceId`**:Durable Functions のインスタンス ID。
  * **`reason`**:追跡イベントに関連付けられている追加のデータ。 詳しくは、「[Durable Functions における診断 (Azure Functions)](durable-functions-diagnostics.md)」をご覧ください
  * **`runtimeStatus`**:オーケストレーションのランタイム状態。 実行中、完了、失敗、取り消し済みです。
* **`eventType`**: "orchestratorEvent"
* **`eventTime`**:イベント時間 (UTC)。
* **`dataVersion`**:ライフサイクル イベント スキーマのバージョン。
* **`metadataVersion`**:メタデータのバージョン。
* **`topic`**:イベント グリッド トピック リソース。

## <a name="how-to-test-locally"></a>ローカルでテストする方法

ローカルでテストするには、[ngrok](../functions-bindings-event-grid.md#local-testing-with-ngrok) を使用します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Durable Functions でのインスタンスの管理について](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Durable Functions でのバージョン管理について](durable-functions-versioning.md)
