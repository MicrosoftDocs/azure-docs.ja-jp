---
title: Durable Functions の関数の種類と機能の概要 - Azure
description: Durable Functions オーケストレーションの一環として関数間の情報交換を可能にする関数とロールの種類について説明します。
services: functions
author: jeffhollan
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 07/04/2018
ms.author: azfuncdf
ms.openlocfilehash: 265314ebf2568bd586934d371e1e6c1d74e0b9bb
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2018
ms.locfileid: "52637017"
---
# <a name="overview-of-function-types-and-features-for-durable-functions-azure-functions"></a>Durable Functions の関数の種類と機能の概要 (Azure Functions)

Azure Durable Functions は、関数実行のステートフル オーケストレーションを提供します。 Durable Functions はさまざまな Azure Functions から構成されるソリューションです。 それぞれの関数がオーケストレーションの一部として異なる役割を果たすことができます。 次のドキュメントでは、Durable Functions オーケストレーションに含まれる関数の種類の概要を示します。 関数を組み合わせるときの一般的なパターンもいくつか含まれています。  今すぐ開始するには、[C#](durable-functions-create-first-csharp.md) または [JavaScript](quickstart-js-vscode.md) で最初の永続関数を作成してください。

![Durable Functions の種類][1]  

## <a name="types-of-functions"></a>関数の種類

### <a name="activity-functions"></a>アクティビティ関数

アクティビティ関数は、Durable Functions オーケストレーションの基本作業単位です。  アクティビティ関数は、プロセスでオーケストレーションされる関数とタスクです。  たとえば、注文を処理する、つまり、在庫を確認し、顧客に代金を請求し、出荷を手配する Durable Functions を作成できます。  そのようなタスクはいずれもアクティビティ関数です。  アクティビティ関数には、その中でできる作業の種類について制限がありません。  Azure Functions でサポートされているあらゆる言語で記述できます。  持続的タスク フレームワークによって、呼び出された各アクティビティ関数が、オーケストレーションの間、少なくとも 1 回実行されます。

アクティビティ関数は[アクティビティ トリガー](durable-functions-bindings.md#activity-triggers)によって始動させる必要があります。  この関数はパラメーターとして [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) を受け取ります。 トリガーを他のオブジェクトにバインドし、入力を関数に渡すこともできます。  アクティビティ関数はオーケストレーターに値を返すこともできます。  アクティビティ関数からたくさんの値を送信したり、返したりする場合、[タプルや配列](durable-functions-bindings.md#passing-multiple-parameters)を活用できます。  アクティビティ関数は、オーケストレーション インスタンスからのみトリガーさせることができます。  一部のコードはアクティビティ関数と別の関数 (HTTP でトリガーした関数など) の間で共有できますが、各関数に与えられるトリガーは 1 つに限られます。

詳細とサンプルは、[Durable Functions のバインディングに関するこちらの記事](durable-functions-bindings.md#activity-triggers)にあります。

### <a name="orchestrator-functions"></a>オーケストレーター関数

オーケストレーター関数は、Durable Functions の中核です。  オーケストレーター関数は、アクションが実行される方法と順序を表現します。  オーケストレーター関数は、「[Durable Functions の概要](durable-functions-overview.md)」にあるように、コード (C# または JavaScript) でオーケストレーションを表現します。  1 つのオーケストレーションに、[アクティビティ関数](#activity-functions)、[サブオーケストレーション](#sub-orchestrations)、[外部イベントの待ち受け](#external-events)、[タイマー](#durable-timers)など、さまざまな種類のアクションを設定できます。  

オーケストレーター関数は[オーケストレーション トリガー](durable-functions-bindings.md#orchestration-triggers)によって始動させる必要があります。

オーケストレーターは[オーケストレーター クライアント](#client-functions)によって開始されます。オーケストレーター クライアント自体はあらゆるソース (HTTP、キュー、イベント ストリーム) からトリガーされます。  オーケストレーションの各インスタンスにはインスタンス ID が与えられます。この ID の生成には自動生成 (推奨) とユーザー生成があります。  この ID はオーケストレーションの[インスタンス管理](durable-functions-instance-management.md)に利用できます。

詳細とサンプルは、[Durable Functions のバインディングに関するこちらの記事](durable-functions-bindings.md#orchestration-triggers)にあります。

### <a name="client-functions"></a>クライアント関数

クライアント関数はトリガーされる関数であり、オーケストレーションの新しいインスタンスを作成します。  持続的オーケストレーションのインスタンス作成のエントリ ポイントです。  クライアント関数はあらゆるトリガー (HTTP、キュー、イベント ストリームなど) で始動させることができます。また、アプリでサポートされているあらゆる言語で記述できます。  トリガーに加え、クライアント関数には[オーケストレーション クライアント バインディング](durable-functions-bindings.md#orchestration-client)を設定できます。これにより、クライアント関数で持続的オーケストレーションを作成し、管理できます。  クライアント関数の最も基本的な例は、[このサンプル](durable-functions-http-api.md#http-api-url-discovery)のように、オーケストレーター関数を開始し、状態確認応答を返す HTTP トリガーの関数です。

詳細とサンプルは、[Durable Functions のバインディングに関するこちらの記事](durable-functions-bindings.md#orchestration-client)にあります。

## <a name="features-and-patterns"></a>機能とパターン

### <a name="sub-orchestrations"></a>サブオーケストレーション

オーケストレーター関数はアクティビティ関数を呼び出すだけでなく、別のオーケストレーター関数を呼び出すことができます。 たとえば、オーケストレーター関数のライブラリから大規模なオーケストレーションを作成できます。 またはオーケストレーター関数の複数のインスタンスを並列で実行できます。

詳細とサンプルは、[サブオーケストレーションに関するこちらの記事](durable-functions-sub-orchestrations.md)にあります。

### <a name="durable-timers"></a>持続的タイマー

[Durable Functions](durable-functions-overview.md) には、遅延を実装したり、非同期アクションでタイムアウトを設定したりできるように、オーケストレーター関数で使用する "*持続的タイマー*" が用意されています。 持続的タイマーは、`Thread.Sleep` および `Task.Delay` (C#)、または `setTimeout()` および `setInterval()` (JavaScript) の代わりに、オーケストレーター関数で使用されます。

持続的タイマーの詳細とサンプルは、[持続的タイマーに関するこちらの記事](durable-functions-timers.md)にあります。

### <a name="external-events"></a>外部イベント

オーケストレーター関数は、オーケストレーション インスタンスを更新するために、外部イベントを待ち受けできます。 Durable Functions のこの機能は、多くの場合、人による操作または他の外部コールバックを処理するときに便利です。

詳細とサンプルは、[外部イベントに関するこちらの記事](durable-functions-external-events.md)にあります。

### <a name="error-handling"></a>エラー処理

Durable Function のオーケストレーションはコードで実装され、プログラミング言語のエラー処理機能を使用できます。  つまり、"try/catch" のようなパターンがオーケストレーションで機能します。  Durable Functions には再試行ポリシーがいくつか組み込まれています。  例外時、アクションは自動的にアクティビティを遅らせたり、再試行したりできます。  再試行では、オーケストレーションを破棄することなく、一時的な例外を処理できます。

詳細とサンプルは、[エラー処理に関するこちらの記事](durable-functions-error-handling.md)にあります。

### <a name="cross-function-app-communication"></a>関数アプリ間の通信

持続的オーケストレーションは一般的に 1 つの関数アプリ内で存続しますが、さまざまな関数アプリ間でオーケストレーションを調整できるパターンがあります。  アプリ間通信は HTTP で行われることがありますが、各アクティビティに持続的フレームワークを使用するということは、2 つのアプリ間で持続的プロセスを維持できることを意味します。

C# の関数アプリ間オーケストレーションの例を下に示します。  1 つのアクティビティが外部オーケストレーションを開始します。 別のアクティビティが状態を取得して返します。  オーケストレーターは状態の完了を待ってから続行します。

```csharp
[FunctionName("OrchestratorA")]
public static async Task RunRemoteOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    // Do some work...

    // Call a remote orchestration
    string statusUrl = await context.CallActivityAsync<string>(
        "StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete
    while (true)
    {
        bool isComplete = await context.CallActivityAsync<bool>("CheckIsComplete", statusUrl);
        if (isComplete)
        {
            break;
        }

        await context.CreateTimer(context.CurrentUtcDateTime.AddMinutes(1), CancellationToken.None);
    }

    // B is done. Now go do more work...
}

[FunctionName("StartRemoteOrchestration")]
public static async Task<string> StartRemoteOrchestration([ActivityTrigger] string orchestratorName)
{
    using (var response = await HttpClient.PostAsync(
        $"https://appB.azurewebsites.net/orchestrations/{orchestratorName}",
        new StringContent("")))
    {
        string statusUrl = await response.Content.ReadAsAsync<string>();
        return statusUrl;
    }
}

[FunctionName("CheckIsComplete")]
public static async Task<bool> CheckIsComplete([ActivityTrigger] string statusUrl)
{
    using (var response = await HttpClient.GetAsync(statusUrl))
    {
        // 200 = Complete, 202 = Running
        return response.StatusCode == HttpStatusCode.OK;
    }
}
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [続けて Durable Functions のドキュメントを読む](durable-functions-bindings.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png
