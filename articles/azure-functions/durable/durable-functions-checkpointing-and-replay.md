---
title: Durable Functions でのチェックポイントと再生 - Azure
description: Azure Functions の Durable Functions 拡張機能でチェックポイントと再生がどのように動作するかを説明します。
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 79cb276f121c351a9954994038d9d826819edf5d
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087450"
---
# <a name="checkpoints-and-replay-in-durable-functions-azure-functions"></a>Durable Functions でのチェックポイントと再生 - (Azure Functions)

Durable Functions のキー属性の 1 つが**信頼性の高い実行**です。 オーケストレーター関数とアクティビティ関数は、データ センター内の異なる VM で実行されている可能性があり、こうした VM や基盤となるネットワーク インフラストラクチャは 100% 信頼できるわけではありません。

それにもかかわらず、Durable Functions では、オーケストレーションの信頼性の高い実行が保証されます。 これは、ストレージ キューを使用して関数呼び出しを促進するか、実行履歴をストレージ テーブルに定期的にチェックポイントすることで実現します ([イベント ソーシング](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)と呼ばれるクラウド デザイン パターンを使用)。 この履歴は、オーケストレーターのメモリ内状態が自動的にリビルドされるように、後で再生することができます。

## <a name="orchestration-history"></a>オーケストレーションの履歴

次のオーケストレーター関数があるとします。

### <a name="c"></a>C#

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    var outputs = new List<string>();

    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x のみ)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const output = [];
    output.push(yield context.df.callActivity("E1_SayHello", "Tokyo"));
    output.push(yield context.df.callActivity("E1_SayHello", "Seattle"));
    output.push(yield context.df.callActivity("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return output;
});
```

Durable Task Framework は、`await` (C#) または `yield` (JavaScript) ステートメントごとに、関数の実行状態を Table Storage にチェックポイントします。 この状態は、"*オーケストレーションの履歴*" と呼ばれます。

## <a name="history-table"></a>履歴テーブル

一般的に、Durable Task Framework は、チェックポイントごとに次の処理を実行します。

1. 実行履歴を Azure Storage テーブルに保存します。
2. オーケストレーターが起動する必要がある関数のメッセージをエンキューします。
3. オーケストレーター自体のメッセージ &mdash; たとえば、持続的タイマー メッセージをエンキューします。

チェックポイントが完了したら、さらに多くの作業が必要になるまで、オーケストレーター関数をメモリから自由に削除できるようになります。

> [!NOTE]
> Azure Storage では、Table Storage へのデータ保存とキューへのデータ保存の間で、トランザクションが保証されません。 エラーを処理するために、Durable Functions ストレージ プロバイダーでは "*最終的な一貫性*" パターンが使用されます。 こうしたパターンにより、チェックポイントの途中でクラッシュまたは接続の切断が発生しても、データが失われることはありません。

完了すると、Azure Table Storage の上記の関数の履歴は次のようになります (わかりやすくするために一部省略されています)。

| PartitionKey (InstanceId)                     | EventType             | Timestamp               | 入力 | 名前             | 結果                                                    | Status |
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     |
| eaee885b | ExecutionStarted      | 2017-05-05T18:45:28.852Z | null  | E1_HelloSequence |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:32.670Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:32.670Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.232Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.201Z |       |                  | """Hello Tokyo!"""                                        |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.435Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.435Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.763Z |       |                  | """Hello Seattle!"""                                      |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.857Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.919Z |       |                  | """Hello London!"""                                       |                     |
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044Z |       |                  | "[""Hello Tokyo!"",""Hello Seattle!"",""Hello London!""]" | 完了           |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     |

列の値に関する注意点:

* **PartitionKey**:オーケストレーションのインスタンス ID が含まれます。
* **EventType**:イベントの種類を表します。 次のいずれかの種類です。
  * **OrchestrationStarted**:オーケストレーター関数が await から再実行されました。または初回実行中です。 `Timestamp` 列は、[CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) API の決定論的な値を設定するときに使用されます。
  * **ExecutionStarted**:オーケストレーター関数は初めて実行を開始しました。 このイベントには、`Input` 列の関数入力も含まれています。
  * **TaskScheduled**:アクティビティ関数がスケジュールされました。 アクティビティ関数の名前は `Name` 列でキャプチャされます。
  * **TaskCompleted**:アクティビティ関数が完了しました。 関数の結果は `Result` 列に含まれます。
  * **TimerCreated**:持続的タイマーが作成されました。 `FireAt` 列には、タイマーが期限切れになるスケジュールされた UTC 時間が含まれます。
  * **TimerFired**:持続的タイマーが開始されました。
  * **EventRaised**:外部イベントがオーケストレーション インスタンスに送信されました。 `Name` 列は、イベントの名前をキャプチャし、`Input` 列は、イベントのペイロードをキャプチャします。
  * **OrchestratorCompleted**:オーケストレーター関数が待機状態になりました。
  * **ContinueAsNew**:オーケストレーター関数が完了し、新しい状態で再実行されました。 `Result` 列には、再起動されたインスタンスで入力として使用される値が含まれます。
  * **ExecutionCompleted**:オーケストレーター関数が実行され、完了 (または失敗) しました。 関数またはエラーの詳細の出力は `Result` 列に格納されます。
* **Timestamp**:履歴イベントの UTC タイムスタンプ。
* **Name**:呼び出された関数の名前。
* **入力**:関数の JSON 形式の入力。
* **Result**:関数の出力、つまり、戻り値。

> [!WARNING]
> これはデバッグ ツールとしては便利ですが、このテーブルにあまり依存しないようにしてください。 Durable Functions 拡張機能の刷新に伴って変更される可能性があります。

`await` (C#) または `yield` (JavaScript) から関数が再実行されるたびに、Durable Task Framework は、オーケストレーター関数をゼロから再実行し、 再実行のたびに、実行履歴を調べて、現在の非同期操作が実行されているかどうかを確認します。  操作が実行されている場合、フレームワークはその操作の出力をすぐに再生し、次の `await` (C#) または `yield` (JavaScript) に移動します。 このプロセスは、履歴全体が再生されるまで継続的に実行され、全体が再生された時点で、オーケストレーター関数のすべてのローカル変数が以前の値に復元されます。

## <a name="orchestrator-code-constraints"></a>オーケストレーター コードの制約

この再生動作は、オーケストレーター関数に記述できるコードの種類に制約を設けます。

* オーケストレーター コードは**決定論的**である必要があります。 複数回再生され、毎回結果が同じでなければなりません。 たとえば、現在の日付/時刻の取得、乱数の取得、ランダム GUID の生成、またはリモート エンドポイントへの呼び出しを行うための直接呼び出しは行わないでください。

  オーケストレーター コードが現在の日付/時刻を取得する必要がある場合は、安全に再生できる [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) (.NET) または `currentUtcDateTime` (JavaScript) API を使用する必要があります。

  オーケストレーター コードで、ランダムな GUID を生成する必要がある場合、安全に再生できる [NewGuid](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_NewGuid) (.NET) API を使用するか、またはこの例のように、アクティビティ関数 (JavaScript) に GUID の生成を委任する必要があります。

  ```javascript
  const uuid = require("uuid/v1");

  module.exports = async function(context) {
    return uuid();
  }
  ```

  非決定論的な操作は、アクティビティ関数で行う必要があります。 これには、他の入力または出力バインドとのすべての対話が含まれ、 これにより、最初の実行時、および実行履歴への保存時に、非決定論的なすべての値が生成されます。 以降の実行では、保存された値が自動的に使用されます。

* オーケストレーター コードは**非ブロッキング**である必要があります。 これは、たとえば `Thread.Sleep` (.NET) または同等の API に対して I/O および呼び出しがないことを意味します。

  オーケストレーターに遅延が必要な場合は、[CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) (.NET) または `createTimer` (JavaScript) API を使用できます。

* オーケストレーター コードで**非同期操作を開始しないでください** ([DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) API または `context.df` オブジェクトの APIを使用する場合を除く)。 たとえば、.NET では `Task.Run`、`Task.Delay`、または `HttpClient.SendAsync` を開始せず、JavaScript では `setTimeout()` および `setInterval()` を開始しません。 Durable Task Framework は、オーケストレーター コードを 1 つのスレッドで実行しており、他の非同期 API でスケジュールされている他のスレッドとは対話できません。 これが発生すると、`InvalidOperationException` 例外がスローされます。

* オーケストレーター コードでは**無限ループが発生しないようにしてください**。 Durable Task Framework では、オーケストレーション関数の進行状況に応じて実行履歴が保存されるため、無限ループが発生すると、オーケストレーター インスタンスによってメモリが不足する可能性があります。 無限ループ シナリオでは、[ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) (.NET) または `continueAsNew` (JavaScript) などの API を使用して、関数の実行を再開して、前の実行履歴を破棄してください。

* JavaScript オーケストレーター関数は、`async` にできません。 これらは、同期ジェネレーター関数として宣言する必要があります。

これらの制約は一見大変なように感じますが、実際のところ、それほど苦労せずに従うことができます。 Durable Task Frameworkは上記の規則違反の検出を試み、`NonDeterministicOrchestrationException` をスローします。 ただし、この検出動作はベスト エフォートであるため、これに頼りすぎないようにしてください。

> [!NOTE]
> こうした規則はすべて、`orchestrationTrigger` バインドでトリガーされる関数にのみ適用されます。 `activityTrigger` バインドでトリガーされるアクティビティ関数と、`orchestrationClient` バインドを使用する関数には、こうした制限はありません。

## <a name="durable-tasks-net"></a>持続的なタスク (.NET)

> [!NOTE]
> このセクションでは、Durable Task Framework の内部実装について詳しく説明します。 この情報は、Durable Functions を使用するうえで必ずしも必要とは限りませんが、 再生動作を理解するうえでは役に立ちます。

オーケストレーター関数で安全に待つことができるタスクが、"*持続的なタスク*" と呼ばれることがあります。 こうしたタスクは Durable Task Framework で作成され管理されます。 たとえば、`CallActivityAsync`、`WaitForExternalEvent`、`CreateTimer` によって返されるタスクです。

このような "*持続的なタスク*" は、`TaskCompletionSource` オブジェクトの一覧を使用して内部的に管理されます。 再生中、こうしたタスクは、オーケストレーター コード実行の一部として作成され、対応する履歴イベントをディスパッチャーが列挙すると完了します。 この処理は、すべての履歴が再生されるまで、1 つのスレッドを使用して、すべて同期的に実行されます。 持続的なタスクで、履歴の再生の終了までに完了しなかったものについては、適切なアクションが実行されます。たとえば、アクティビティ関数を呼び出すために、メッセージがエンキューされることがあります。

ここで説明した実行の動作は、オーケストレーター関数コードが持続的なタスク以外を `await` しない理由を理解するうえで役立ちます。ディスパッチャー スレッドはそれが完了するのを待たず、そのタスクによるコールバックによって、オーケストレーター関数が追跡状態でなくなる可能性があります。 一部のランタイム チェックは、これを回避しようとします。

Durable Task Framework がオーケストレーター関数を実行する方法に関する詳細情報が必要な場合は、[GitHub の持続的なタスクのソース コード](https://github.com/Azure/durabletask)を確認することをお勧めします。 特に、[TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) と [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs) を参照してください

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [インスタンス管理について確認する](durable-functions-instance-management.md)
