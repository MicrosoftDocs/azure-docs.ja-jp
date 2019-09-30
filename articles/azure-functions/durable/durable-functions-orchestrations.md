---
title: 持続的オーケストレーション - Azure Functions
description: Azure Durable Functions のオーケストレーション機能の概要。
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 09/08/2019
ms.author: azfuncdf
ms.openlocfilehash: 82c4a27ac2491e668c1d99e2a14b870e82ec5665
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935420"
---
# <a name="durable-orchestrations"></a>持続的オーケストレーション

Durable Functions は [Azure Functions](../functions-overview.md) の拡張機能です。 "*オーケストレーター関数*" を使用すると、関数アプリ内の他の持続的関数の実行を調整できます。 オーケストレーター関数には次のような特性があります。

* オーケストレーター関数では、手続き型コードを使用して関数のワークフローが定義されています。 宣言型スキーマまたはデザイナーは必要ありません。
* オーケストレーター関数では、他の持続的関数を同期および非同期のどちらでも呼び出すことができます。 呼び出された関数からの出力を、ローカル変数に確実に保存できます。
* オーケストレーター関数は持続性と信頼性を備えています。 関数で "await" または "yield" が実行されると、実行の進行状況に自動的にチェックポイントが設定されます。 プロセスがリサイクルされるときや VM が再起動されるときに、ローカルの状態が失われることがありません。
* オーケストレーター関数は長時間実行される可能性があります。 "*オーケストレーション インスタンス*" の全体的な継続時間は、秒、日、月の単位になる場合、またはいつまでも終了しない場合があります。

この記事では、オーケストレーター関数の概要と、オーケストレーター関数によってアプリ開発のさまざまな課題を解決する方法について説明します。 Durable Functions アプリで使用可能な関数の種類をまだよく理解していない場合は、最初に [Durable Functions の種類](durable-functions-types-features-overview.md)に関する記事をご覧ください。

## <a name="orchestration-identity"></a>オーケストレーション ID

オーケストレーションの各 "*インスタンス*" にはインスタンス ID あります ("*インスタンス ID*" とも呼ばれます)。 既定では、各インスタンス ID は自動生成される GUID です。 ただし、ユーザーが生成した文字列値をインスタンス ID にすることもできます。 各オーケストレーション インスタンス ID は、[タスク ハブ](durable-functions-task-hubs.md)内で一意である必要があります。

インスタンス ID に関するいくつかのルールを次に示します。

* インスタンス ID は、1 から 256 文字にする必要があります。
* インスタンス ID の先頭文字を `@` にすることはできません。
* インスタンス ID に、`/`、`\`、`#`、`?` の各文字を含めることはできません。
* インスタンス ID に、制御文字を含めることはできません。

> [!NOTE]
> 一般に、可能な限り自動生成されたインスタンス ID を使用することをお勧めします。 ユーザー生成のインスタンス ID は、オーケストレーション インスタンスと、外部アプリケーション固有のエンティティ (注文書やドキュメントなど) との間に一対一のマッピングが存在するシナリオのためのものです。

ほとんどの[インスタンス管理操作](durable-functions-instance-management.md)で、オーケストレーション インスタンス ID は必須のパラメーターです。 また、トラブルシューティングや分析を目的とする Application Insights での[オーケストレーション追跡データによる検索](durable-functions-diagnostics.md#application-insights)などの診断にも重要です。 このため、生成されたインスタンス ID は、後で簡単に参照できる外部の場所 (データベースやアプリケーション ログなど) に保存することをお勧めします。

## <a name="reliability"></a>信頼性

オーケストレーター関数は、[イベント ソーシング](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)設計パターンを使用して、この関数の実行状態を確実に管理します。 Durable Task Framework では、オーケストレーションの現在の状態を直接格納する代わりに、追加専用ストアを使用して、関数オーケストレーションによって実行されるすべてのアクションが記録されます。 追加専用ストアには、実行時のすべての状態の "ダンプ" に比べて、多くのメリットがあります。 メリットには、パフォーマンス、スケーラビリティ、および応答性の向上が含まれます。 トランザクション データの最終的な一貫性、完全な監査証跡、および監査履歴も取得できます。 監査証跡では、信頼性の高い補正アクションがサポートされます。

Durable Functions では、イベント ソーシングが透過的に使用されます。 バックグラウンドで、オーケストレーター関数内の `await` (C#) または `yield` (JavaScript) 演算子によって、オーケストレーター スレッドのコントロールが Durable Task Framework ディスパッチャーに戻されます。 次に、このディスパッチャーは、オーケストレーター関数がスケジュールした新しいアクション (1 つ以上の子関数を呼び出す、永続タイマーをスケジュールする、など) をストレージにコミットします。 この透過的なコミット アクションが、オーケストレーション インスタンスの実行履歴に追加されます。 この履歴はストレージ テーブルに格納されます。 次に、このコミット アクションはキューにメッセージを追加して実際の作業をスケジュールします。 この時点では、メモリからオーケストレーター関数をアンロードできます。

オーケストレーション関数にさらに処理すべき作業 (応答メッセージの受信や永続タイマーの終了など) が与えられると、オーケストレーターが起動し、関数全体が始めから再実行され、ローカルの状態が再構築されます。 このリプレイ中にコードが関数を呼び出そうとする (または他の非同期作業を行おうとする) と、Durable Task Framework によって現在のオーケストレーションの実行履歴が確認されます。 [アクティビティ関数](durable-functions-types-features-overview.md#activity-functions)が既に実行され、いくつかの結果が生成されていることがわかった場合は、関数の結果をリプレイし、オーケストレーター コードの実行を続行します。 リプレイは、関数コードが完了するまで、または新しい非同期作業がスケジュールされるまで続行されます。

> [!NOTE]
> リプレイ パターンが正常かつ確実に動作するためには、オーケストレーター関数のコードが "*決定論的*" である必要があります。 オーケストレーター関数のコードの制限について詳しくは、[オーケストレーター関数のコードの制約](durable-functions-code-constraints.md)に関するトピックをご覧ください。

> [!NOTE]
> オーケストレーター関数によってログ メッセージを出力される場合、リプレイ動作によって重複するログ メッセージが生成される可能性があります。 この動作が発生する理由とそれを回避する方法の詳細については、「[ログの記録](durable-functions-diagnostics.md#logging)」トピックをご覧ください。

## <a name="orchestration-history"></a>オーケストレーションの履歴

Durable Task Framework のイベント ソーシング動作は、記述したオーケストレーター関数のコードと密接に結び付けられています。 次の C# のオーケストレーター関数のような、アクティビティ チェーン オーケストレーター関数があるものとします。

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

JavaScript でコーディングしている場合、アクティビティ チェーン オーケストレーター関数は次のコード例のようになります。

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

Durable Task Framework では、`await` (C#) または `yield` (JavaScript) ステートメントごとに、関数の実行状態のチェックポイントが、持続的なストレージ バックエンド (通常は Azure Table Storage) に記録されます。 この状態は、"*オーケストレーションの履歴*" と呼ばれます。

### <a name="history-table"></a>履歴テーブル

一般的に、Durable Task Framework は、チェックポイントごとに次の処理を実行します。

1. 実行履歴を Azure Storage テーブルに保存します。
2. オーケストレーターが起動する必要がある関数のメッセージをエンキューします。
3. オーケストレーター自体のメッセージ &mdash; たとえば、持続的タイマー メッセージをエンキューします。

チェックポイントが完了したら、さらに多くの作業が必要になるまで、オーケストレーター関数をメモリから自由に削除できるようになります。

> [!NOTE]
> Azure Storage では、Table Storage へのデータ保存とキューへのデータ保存の間で、トランザクションが保証されません。 エラーを処理するために、Durable Functions ストレージ プロバイダーでは "*最終的な一貫性*" パターンが使用されます。 こうしたパターンにより、チェックポイントの途中でクラッシュまたは接続の切断が発生しても、データが失われることはありません。

完了すると、Azure Table Storage の上記の関数の履歴は次の表のようになります (わかりやすくするために一部省略されています)。

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

`await` (C#) または `yield` (JavaScript) から関数が再実行されるたびに、Durable Task Framework は、オーケストレーター関数をゼロから再実行し、 再実行のたびに、実行履歴が調べられて、現在の非同期操作が実行されているかどうかが確認されます。  操作が実行されている場合、フレームワークはその操作の出力をすぐに再生し、次の `await` (C#) または `yield` (JavaScript) に移動します。 履歴全体がリプレイされるまで、このプロセスが続けられます。 現在の履歴がリプレイされると、ローカル変数が以前の値に復元されます。

## <a name="features-and-patterns"></a>機能とパターン

次のセクションでは、オーケストレーター関数の機能とパターンについて説明します。

### <a name="sub-orchestrations"></a>サブオーケストレーション

オーケストレーター関数では、アクティビティ関数を呼び出すことができますが、別のオーケストレーター関数を呼び出すこともできます。 たとえば、オーケストレーター関数のライブラリから大規模なオーケストレーションを作成できます。 つまり、オーケストレーター関数の複数のインスタンスを並列で実行できます。

詳細と例については、[サブオーケストレーション](durable-functions-sub-orchestrations.md)に関する記事をご覧ください。

### <a name="durable-timers"></a>持続的タイマー

オーケストレーションでは、*持続的タイマー*をスケジュールして、遅延を実装したり、非同期アクションに対するタイムアウト処理を設定したりすることができます。 オーケストレーター関数の持続的タイマーは、`Thread.Sleep` および `Task.Delay` (C#)、または `setTimeout()` および `setInterval()` (JavaScript) の代わりに使用します。

詳細と例については、[持続的タイマー](durable-functions-timers.md)に関する記事をご覧ください。

### <a name="external-events"></a>外部イベント

オーケストレーター関数は、オーケストレーション インスタンスを更新するために、外部イベントを待ち受けできます。 Durable Functions のこの機能は、多くの場合、人による操作または他の外部コールバックを処理するときに便利です。

詳細と例については、[外部イベント](durable-functions-external-events.md)に関する記事をご覧ください。

### <a name="error-handling"></a>エラー処理

オーケストレーター関数では、プログラミング言語のエラー処理機能を使用できます。 オーケストレーションのコードでは、`try`/`catch` のような既存のパターンがサポートされています。

オーケストレーター関数では、呼び出されるアクティビティまたはサブオーケストレーター関数に再試行ポリシーを追加することもできます。 アクティビティまたはサブオーケストレーター関数が例外で失敗した場合、指定した再試行ポリシーによって自動的に遅延し、指定した回数まで実行を再試行できます。

> [!NOTE]
> オーケストレーター関数でハンドルされない例外が発生した場合、オーケストレーションのインスタンスは `Failed` 状態で完了します。 失敗したオーケストレーションのインスタンスを再試行することはできません。

詳細と例については、[エラー処理](durable-functions-error-handling.md)に関する記事をご覧ください。

### <a name="critical-sections"></a>クリティカル セクション

オーケストレーションのインスタンスはシングルスレッドであるため、オーケストレーションの "*内部*" の競合状態について考慮する必要はありません。 ただし、オーケストレーションから外部システムとやりとりする場合、競合状態が発生する可能性があります。 外部システムと相互作用するときの競合状態を軽減するため、オーケストレーター関数では .NET の `LockAsync` メソッドを使用して "*クリティカル セクション*" を定義できます。

次のサンプル コードで示すオーケストレーター関数では、クリティカル セクションが定義されています。 クリティカル セクションに入るには、`LockAsync` メソッドを使用します。 このメソッドでは、持続的にロック状態を管理する[持続エンティティ](durable-functions-entities.md)への 1 つ以上の参照を渡す必要があります。 クリティカル セクション内のコードを実行できるこのオーケストレーションのインスタンスは、一度に 1 つだけです。

```csharp
[FunctionName("Synchronize")]
public static async Task Synchronize(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var lockId = new EntityId("LockEntity", "MyLockIdentifier");
    using (await context.LockAsync(lockId))
    {
        // critical section - only one orchestration can enter at a time
    }
}
```

`LockAsync` では、持続的ロックが取得されて、破棄されたときにクリティカル セクションが終了する `IDisposable` が返されます。 この `IDisposable` の結果を `using` ブロックと共に使用して、クリティカル セクションの構文表現を取得できます。 オーケストレーター関数がクリティカル セクションに入ると、1 つのインスタンスだけがそのコード ブロックを実行できます。 他のインスタンスがそのクリティカル セクションに入ろうとしても、前のインスタンスがそのクリティカル セクションを終了するまでブロックされます。

クリティカル セクションの機能は、持続性エンティティに対する変更を調整する場合にも役立ちます。 クリティカル セクションについて詳しくは、[持続性エンティティの "エンティティ調整"](durable-functions-entities.md#entity-coordination) に関するトピックをご覧ください。

> [!NOTE]
> クリティカル セクションは Durable Functions 2.0 以降で使用できます。 現在、この機能が実装されているのは .NET オーケストレーションだけです。

### <a name="calling-http-endpoints"></a>HTTP エンドポイントの呼び出し

[オーケストレーター関数のコードの制約](durable-functions-code-constraints.md)に関する記事で説明されているように、オーケストレーター関数は I/O を行うことを許可されていません。 この制限に対する一般的な回避策は、I/O を行う必要があるすべてのコードをアクティビティ関数内にラップすることです。 外部システムとのやりとりが頻繁に行われるオーケストレーションでは、アクティビティ関数を使用して、HTTP の呼び出しを行い、その結果をオーケストレーションに返します。

この一般的なパターンを簡略化するため、オーケストレーター関数では、.NET の `CallHttpAsync` メソッドを使用して、HTTP API を直接呼び出すことができます。 基本的な要求/応答パターンのサポートに加えて、`CallHttpAsync` では、一般的な非同期 HTTP 202 ポーリング パターンの自動処理がサポートされており、[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) を使用した外部サービスによる認証もサポートされています。

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

詳細と例については、[HTTP 機能](durable-functions-http-features.md)に関する記事をご覧ください。

> [!NOTE]
> オーケストレーター関数からの HTTP エンドポイントの直接呼び出しは、Durable Functions 2.0 以降で使用できます。 現在、この機能が実装されているのは .NET オーケストレーションだけです。

### <a name="passing-multiple-parameters"></a>複数のパラメーターを渡す

アクティビティ関数に複数のパラメーターを直接渡すことはできません。 オブジェクトの配列を渡すか、.NET の [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) オブジェクトを使用することをお勧めします。

次の例では、[C# 7](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7#tuples) に追加された [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) の新機能を使用しています。

```csharp
[FunctionName("GetCourseRecommendations")]
public static async Task<object> RunOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string major = "ComputerScience";
    int universityYear = context.GetInput<int>();

    object courseRecommendations = await context.CallActivityAsync<object>(
        "CourseRecommendations",
        (major, universityYear));
    return courseRecommendations;
}

[FunctionName("CourseRecommendations")]
public static async Task<object> Mapper([ActivityTrigger] DurableActivityContext inputs)
{
    // parse input for student's major and year in university
    (string Major, int UniversityYear) studentInfo = inputs.GetInput<(string, int)>();

    // retrieve and return course recommendations by major and university year
    return new
    {
        major = studentInfo.Major,
        universityYear = studentInfo.UniversityYear,
        recommendedCourses = new []
        {
            "Introduction to .NET Programming",
            "Introduction to Linux",
            "Becoming an Entrepreneur"
        }
    };
}
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [オーケストレーター コードの制約](durable-functions-code-constraints.md)
