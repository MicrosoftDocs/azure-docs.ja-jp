---
title: 持続的オーケストレーター コードの制約 - Azure Functions
description: Azure Durable Functions のオーケストレーション関数の再生とコードの制約。
author: cgillum
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/18/2019
ms.author: azfuncdf
ms.openlocfilehash: 87851a4879760c76950f765d05de4662ecb04bea
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935540"
---
# <a name="orchestrator-function-code-constraints"></a>オーケストレーター関数コードの制約

Durable Functions は、ステートフル アプリケーションを構築できるようになる [Azure Functions](../functions-overview.md) の拡張機能です。 [オーケストレーター関数](durable-functions-orchestrations.md)を使用すると、関数アプリ内の他の持続的関数の実行を調整できます。 オーケストレーター関数はステートフルで信頼性が高く、場合によっては実行時間が長いことがあります。

## <a name="orchestrator-code-constraints"></a>オーケストレーター コードの制約

オーケストレーター関数では、[イベント ソーシング](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)を使用して信頼性の高い実行を確保し、ローカル変数の状態を維持します。 オーケストレーター コードの[再生動作](durable-functions-orchestrations.md#reliability)によって、オーケストレーター関数に記述できるコードの種類に制約が生まれます。 たとえば、オーケストレーター コードは "*決定論的*" である必要があります。  オーケストレーター関数は複数回再生されます。また、毎回同じ結果が必ず生成されます。

以下のセクションでは、コードが決定論的であることを確保するための簡単なガイドラインを示します。

### <a name="using-deterministic-apis"></a>決定論的な API の使用

オーケストレーター関数では、ターゲット言語で必要な任意の API を自由に呼び出すことができます。 ただし、オーケストレーター関数で "*決定論的*" な API のみを呼び出すことが重要です。 "*決定論的な API*" とは、呼び出しのタイミングや頻度に関係なく、同じ入力に対して同じ値を常に返す API です。

決定論的 "*ではない*" ために回避する必要がある API のいくつかの例を次に示します。 これらの制限は、オーケストレーター関数にのみ適用されます。 その他の関数の種類には、このような制限はありません。

| API のカテゴリ | 理由 | 対処法 |
| ------------ | ------ | ---------- |
| 日付/時刻  | "_現在の_" 日付または時刻を返す API は、再生ごとに異なる値が返されるため、非決定論的です。 | 安全に再生できる [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) (.NET) または `currentUtcDateTime` (JavaScript) API を使用します。 |
| GUID/UUID  | "_ランダムな_" GUID/UUID を返す API は、再生のたびに異なる値が生成されるため、非決定論的です。 | ランダムな GUID を安全に生成するには、[NewGuid](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_NewGuid) (.NET) または `newGuid` (JavaScript) を使用します。 |
| ランダムな数値 | ランダムな数値を返す API は、再生のたびに異なる値が生成されるため、非決定論的です。 | アクティビティ関数を使用してオーケストレーションに乱数を返します。 アクティビティ関数の戻り値は、常に安全に再生できます。 |
| バインド | 入力および出力のバインドでは、通常、I/O が実行され、非決定論的です。 [オーケストレーション クライアント](durable-functions-bindings.md#orchestration-client)と[エンティティ クライアント](durable-functions-bindings.md#entity-client)のバインドをオーケストレーター関数で直接使用することはできません。 | クライアント関数またはアクティビティ関数内では、入力および出力のバインドを使用します。 |
| ネットワーク | ネットワーク呼び出しには外部システムが関わるため、非決定論的です。 | ネットワーク呼び出しを行うには、アクティビティ関数を使用します。 オーケストレーター関数から HTTP 呼び出しを行う必要がある場合は、[持続的 HTTP API](durable-functions-http-features.md#consuming-http-apis) を使用する方法もあります。 |
| ブロック API | `Thread.Sleep` (.Net) などのブロック API や他の似た API によってオーケストレーター関数のパフォーマンスとスケールに関する問題が発生する可能性があるため、回避する必要があります。 Azure Functions 従量課金プランでは、不要な実行時間の料金が発生することもあります。 | オーケストレーションの実行に遅延を組み込む `CreateTimer` など、使用可能な場合はブロック API に代わるものを使用します。 [持続的タイマー](durable-functions-timers.md)の遅延は、オーケストレーター関数の実行時間にカウントされません。 |
| 非同期 API | オーケストレーター コードで**非同期操作を開始しないでください** ([DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) API または `context.df` オブジェクトの APIを使用する場合を除く)。 たとえば、.NET では `Task.Run`、`Task.Delay`、または `HttpClient.SendAsync` を開始せず、JavaScript では `setTimeout()` および `setInterval()` を開始しません。 Durable Task Framework は、オーケストレーター コードを 1 つのスレッドで実行しており、他の非同期 API でスケジュールされている他のスレッドとは対話できません。 | オーケストレーター関数によって実行されるのは、"*持続的な*" 非同期呼び出しのみです。 その他の非同期 API の呼び出しは、アクティビティ関数から実行する必要があります。 |
| 非同期 JavaScript 関数 | JavaScript オーケストレーター関数は、非同期関数が決定論的であることが node.js ランタイムで確保されないため、`async` にすることはできません。 | JavaScript オーケストレーター関数は、同期ジェネレーター関数として宣言する必要があります。 |
| スレッド API | Durable Task Framework では、1 つのスレッドでオーケストレーター コードが実行され、他のスレッドと対話することはできません。 オーケストレーションの実行に新しいスレッドを導入すると、非決定論的な実行またはデッドロックが発生する可能性あります。 | ほとんどの場合、オーケストレーター関数ではスレッド API を使用できません。 必要に応じて、アクティビティ関数に制限する必要があります。 |
| 静的変数 | 非定数の静的変数は、時間の経過と共に値が変化し、結果として非決定論的な実行動作になる可能性があるため、オーケストレーター関数では使用しないことをお勧めします。 | アクティビティ関数には定数を使用するか、静的変数の使用を制限します。 |
| 環境変数 | オーケストレーター関数では環境変数を使用しないでください。 これらの値は時間の経過と共に変化し、結果として非決定論的な実行動作になる可能性があります。 | 環境変数は、クライアント関数またはアクティビティ関数内からのみ参照する必要があります。 |
| 無限ループ | オーケストレーター関数では無限ループが発生しないようにしてください。 Durable Task Framework では、オーケストレーション関数の進行状況に応じて実行履歴が保存されるため、無限ループが発生すると、オーケストレーター インスタンスによってメモリが不足する可能性があります。 | 無限ループ シナリオでは、[ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) (.NET) または `continueAsNew` (JavaScript) などの API を使用して、関数の実行を再開して、前の実行履歴を破棄してください。 |

これらの制約は一見大変なように感じますが、実際のところ、それほど苦労せずに従うことができます。 Durable Task Frameworkは上記の規則違反の検出を試み、`NonDeterministicOrchestrationException` をスローします。 ただし、この検出動作はベスト エフォートであるため、これに頼りすぎないようにしてください。

## <a name="versioning"></a>バージョン管理

持続的なオーケストレーションは、日、月、年、さらには[永続的に](durable-functions-eternal-orchestrations.md)継続して実行することができます。 Durable Functions アプリケーションに対して、まだ完了していないオーケストレーションに影響を与えるコードの更新が加えられた場合、再生動作が中断されることがあります。 そのため、コードを更新するときは慎重に計画することが重要です。 コードをバージョン管理する方法の詳細については、[バージョン管理](durable-functions-versioning.md)に関する記事を参照してください。

## <a name="durable-tasks"></a>持続的なタスク

> [!NOTE]
> このセクションでは、Durable Task Framework の内部実装について詳しく説明します。 この情報は、Durable Functions を使用するうえで必ずしも必要とは限りませんが、 再生動作を理解するうえでは役に立ちます。

オーケストレーター関数で安全に待つことができるタスクが、"*持続的なタスク*" と呼ばれることがあります。 これらのタスクは、Durable Task Framework によって作成および管理されます。 たとえば、.NET オーケストレーター関数で、`CallActivityAsync`、`WaitForExternalEvent`、および `CreateTimer` から返されるタスクです。

このような "*持続的なタスク*" は、.NET の `TaskCompletionSource` オブジェクトの一覧を使用して内部的に管理されます。 再生中、こうしたタスクは、オーケストレーター コード実行の一部として作成され、対応する履歴イベントをディスパッチャーが列挙すると完了します。 この実行は、すべての履歴が再生されるまで、1 つのスレッドを使用して同期的に処理されます。 持続的なタスクで、履歴の再生の終了までに完了しなかったものについては、適切なアクションが実行されます。たとえば、アクティビティ関数を呼び出すために、メッセージがエンキューされることがあります。

ここで説明した実行の動作は、オーケストレーター関数コードが持続的なタスク以外を `await` または `yield` を実行しない理由を理解するうえで役立ちます。ディスパッチャー スレッドはそれが完了するのを待たず、そのタスクによるコールバックによって、オーケストレーター関数が追跡状態でなくなる可能性があります。 これらの違反を検出するために、いくつかのランタイム チェックが行われます。

Durable Task Framework がオーケストレーター関数を実行する方法に関する詳細情報が必要な場合は、[GitHub の持続的なタスクのソース コード](https://github.com/Azure/durabletask)を確認することをお勧めします。 特に、[TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) と [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs) を参照してください

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [サブオーケストレーションを呼び出す方法](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [バージョン管理の方法](durable-functions-versioning.md)
