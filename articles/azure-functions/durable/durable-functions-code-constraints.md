---
title: 持続的オーケストレーター コードの制約 - Azure Functions
description: Azure Durable Functions のオーケストレーション関数の再生とコードの制約。
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 63db8375379144b2ede78d9e7010a350b3f69b12
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101726412"
---
# <a name="orchestrator-function-code-constraints"></a>オーケストレーター関数コードの制約

Durable Functions は、ステートフル アプリの構築を可能にする、[Azure Functions](../functions-overview.md) の拡張機能です。 [オーケストレーター関数](durable-functions-orchestrations.md)を使用すると、関数アプリ内の他の持続的関数の実行を調整できます。 オーケストレーター関数はステートフルで信頼性が高く、場合によっては実行時間が長いことがあります。

## <a name="orchestrator-code-constraints"></a>オーケストレーター コードの制約

オーケストレーター関数では、[イベント ソーシング](/azure/architecture/patterns/event-sourcing)を使用して信頼性の高い実行を確保し、ローカル変数の状態を維持します。 オーケストレーター コードの[再生動作](durable-functions-orchestrations.md#reliability)によって、オーケストレーター関数に記述できるコードの種類に制約が生まれます。 たとえば、オーケストレーター関数は *決定論的* である必要があります。オーケストレーター関数は複数回再生されますが、毎回同じ結果を出す必要があります。

### <a name="using-deterministic-apis"></a>決定論的な API の使用

このセクションでは、コードが決定論的であることを確保するための簡単なガイドラインを示します。

オーケストレーター関数は、ターゲット言語で任意の API を呼び出すことができます。 ただし、オーケストレーター関数では決定論的な API のみを呼び出すことが重要です。 "*決定論的な API*" とは、呼び出しのタイミングや頻度に関係なく、同じ入力に対して常に同じ値を返す API です。

次の表に、決定論的 "*ではない*" ために回避する必要がある API の例を示します。 これらの制限は、オーケストレーター関数にのみ適用されます。 その他の関数の種類には、このような制限はありません。

| API のカテゴリ | 理由 | 回避策 |
| ------------ | ------ | ---------- |
| 日付と時刻  | 現在の日付または時刻を返す API は、再生ごとに異なる値が返されるため、非決定論的です。 | .NET の [CurrentUtcDateTime](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationcontext.currentutcdatetime) プロパティ、JavaScript の `currentUtcDateTime` API、または Python の `current_utc_datetime` API を使用します。これらは再生時に安全です。 |
| GUID と UUID  | ランダムな GUID または UUID を返す API は、再生のたびに異なる値が生成されるため、非決定論的です。 | ランダムな GUID を安全に生成するには、[NewGuid](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationcontext.newguid) (.NET)、`newGuid` (JavaScript)、`new_guid` (Python) を使用します。 |
| ランダムな数値 | ランダムな数値を返す API は、再生のたびに異なる値が生成されるため、非決定論的です。 | アクティビティ関数を使用してオーケストレーションに乱数を返します。 アクティビティ関数の戻り値は、常に安全に再生できます。 |
| バインド | 入力および出力のバインドでは、通常、I/O が実行され、非決定論的です。 [オーケストレーション クライアント](durable-functions-bindings.md#orchestration-client)および[エンティティ クライアント](durable-functions-bindings.md#entity-client)のバインドも、オーケストレーター関数で直接使用することはできません。 | クライアント関数またはアクティビティ関数内では、入力および出力のバインドを使用します。 |
| ネットワーク | ネットワーク呼び出しには外部システムが関わるため、非決定論的です。 | ネットワーク呼び出しを行うには、アクティビティ関数を使用します。 オーケストレーター関数から HTTP 呼び出しを行う必要がある場合は、[持続的 HTTP API](durable-functions-http-features.md#consuming-http-apis) を使用できます。 |
| ブロック API | .Net の `Thread.Sleep` などのブロック API や他の類似する API は、オーケストレーター関数のパフォーマンスやスケールに関する問題を発生させる可能性があるため、回避する必要があります。 これにより、Azure Functions 従量課金プランで不要な実行時間の料金が発生することもあります。 | 使用可能な場合は、ブロック API に代わる手段を使用してください。 たとえば、`CreateTimer` を使用してオーケストレーションの実行に遅延を組み込みます。 [持続的タイマー](durable-functions-timers.md)の遅延は、オーケストレーター関数の実行時間にカウントされません。 |
| 非同期 API | `IDurableOrchestrationContext` API、JavaScript の `context.df` API、または Python の `context` API を使用する場合以外は、オーケストレーター コードで非同期操作を開始しないでください。 たとえば、.NET では `Task.Run`、`Task.Delay`、および `HttpClient.SendAsync` を、JavaScript では `setTimeout` および `setInterval` を使用できません。 Durable Task Framework では、1 つのスレッドでオーケストレーター コードが実行されます。 他の非同期 API によって呼び出される可能性のある他のスレッドとは対話できません。 | オーケストレーター関数によって実行されるのは、持続的な非同期呼び出しのみです。 その他の非同期 API の呼び出しは、アクティビティ関数から実行する必要があります。 |
| 非同期 JavaScript 関数 | 非同期関数が決定論的であることが node.js ランタイムで確保されないため、JavaScript オーケストレーター関数を `async` として宣言することはできません。 | JavaScript オーケストレーター関数は、同期ジェネレーター関数として宣言します。 |
| Python コルーチン | Python オーケストレーター関数をコルーチンとして宣言することはできません。つまり、 `async` キーワードを使用した宣言です。これは、コルーチン セマンティクスは Durable Functions の再生モデルとは連携しないためです。 | Python オーケストレーター関数をジェネレーターとして宣言します。つまり、`context` API は `await`ではなく `yield` を使用することを想定しています。   |
| スレッド API | Durable Task Framework では、1 つのスレッドでオーケストレーター コードが実行され、他のスレッドと対話することはできません。 オーケストレーションの実行に新しいスレッドを導入すると、非決定論的な実行またはデッドロックが発生する可能性あります。 | ほとんどの場合、オーケストレーター関数ではスレッド API を使用できません。 たとえば、.NET では `ConfigureAwait(continueOnCapturedContext: false)` を使用しないようにします。これで、オーケストレーター関数の元の `SynchronizationContext` でタスクの継続が実行されます。 そのような API が必要な場合は、その使用をアクティビティ関数のみに制限します。 |
| 静的変数 | 非定数の静的変数は、時間の経過と共に値が変化し、結果として非決定論的なランタイム動作が生じる可能性があるため、オーケストレーター関数では使用しないでください。 | アクティビティ関数には定数を使用するか、静的変数の使用を制限します。 |
| 環境変数 | オーケストレーター関数では環境変数を使用しないでください。 これらの値は時間の経過と共に変化し、結果として非決定論的なランタイム動作が生じる可能性があります。 | 環境変数は、クライアント関数またはアクティビティ関数内からのみ参照する必要があります。 |
| 無限ループ | オーケストレーター関数の無限ループが発生しなようにしてください。 Durable Task Framework では、オーケストレーション関数の進行状況に応じて実行履歴が保存されるため、無限ループが発生すると、オーケストレーター インスタンスによってメモリが不足する可能性があります。 | 無限ループ シナリオの場合、.NET では `ContinueAsNew`、JavaScript では `continueAsNew`、Python では `continue_as_new` などの API を使用して関数の実行を再開し、以前の実行履歴を破棄してください。 |

最初はこれらの制約が困難に思えますが、実際には簡単に従うことができます。

Durable Task Framework は上記の規則の違反を検出しようと試みます。 違反が検出されると、フレームワークは、**NonDeterministicOrchestrationException** 例外をスローします。 ただし、この検出動作ではすべての違反をキャッチできないため、これに頼りすぎないようにしてください。

## <a name="versioning"></a>バージョン管理

持続的なオーケストレーションは、日、月、年、さらには[永続的に](durable-functions-eternal-orchestrations.md)継続して実行できます。 Durable Functions アプリに対して、未完了のオーケストレーションに影響を与えるコードの更新が加えられた場合、オーケストレーションの再生動作が中断されることがあります。 そのため、コードを更新するときは慎重に計画することが重要です。 コードをバージョン管理する方法の詳細については、[バージョン管理に関する記事](durable-functions-versioning.md)を参照してください。

## <a name="durable-tasks"></a>持続的なタスク

> [!NOTE]
> このセクションでは、Durable Task Framework の内部実装について詳しく説明します。 この情報は、Durable Functions を使用するうえで必ずしも必要とは限りませんが、 再生動作を理解するうえでは役に立ちます。

オーケストレーター関数で安全に待つことができるタスクは、"*持続的なタスク*" と呼ばれることがあります。 これらのタスクは、Durable Task Framework によって作成および管理されます。 例として、.NET オーケストレーター関数の **CallActivityAsync**、**WaitForExternalEvent**、**CreateTimer** によって返されるタスクが挙げられます。

このような持続的なタスクは、.NET の `TaskCompletionSource` オブジェクトの一覧によって内部的に管理されます。 再生時に、これらのタスクはオーケストレーター コードの実行の一部として作成されます。 これらは、対応する履歴イベントがディスパッチャーによって列挙されると完了します。

これらのタスクは、すべての履歴が再生されるまで、1 つのスレッドを使用して同期的に実行されます。 持続的なタスクで、履歴の再生の終了までに完了しなかったものについては、適切なアクションが実行されます。たとえば、アクティビティ関数を呼び出すために、メッセージがエンキューされることがあります。

このセクションのランタイム動作の説明を参照すると、オーケストレーター関数では、非持続的なタスクに `await` や `yield` を使用できない理由がわかります。 理由として、ディスパッチャー スレッドではタスクの完了を待機できないことと、そのタスクによるコールバックによってオーケストレーター関数が追跡状態でなくなる可能性があることの 2 点が挙げられます。 これらの違反を検出できるように、いくつかのランタイム チェックが行われます。

Durable Task Framework がオーケストレーター関数を実行する方法の詳細については、[GitHub の持続的なタスクのソース コード](https://github.com/Azure/durabletask)を確認してください。 特に、[TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) と [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs) を参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [サブオーケストレーションを呼び出す方法](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [バージョン管理の方法](durable-functions-versioning.md)
