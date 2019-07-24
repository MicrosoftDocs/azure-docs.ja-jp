---
title: Azure Functions の Durable Functions 拡張機能の関数の種類と機能
description: Azure Functions の Durable Functions オーケストレーションでの関数間の情報交換をサポートする、関数とロールの種類について説明します。
services: functions
author: jeffhollan
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 76b6f013333113d5a24b744bc962d36b1c0e21b3
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57455736"
---
# <a name="durable-functions-types-and-features-azure-functions"></a>Durable Functions の種類と機能 (Azure Functions)

Durable Functions は [Azure Functions](../functions-overview.md) の拡張機能です。 Durable Functions は、関数実行のステートフル オーケストレーションに使用できます。 永続関数 (durable function) とはさまざまな Azure 関数で構成されるソリューションです。 Functions は永続関数のオーケストレーションでさまざまな役割を果たすことができます。 

この記事では、Durable Functions オーケストレーションで使用できる関数の種類の概要を示します。 この記事には、関数の接続に使用できる一般的なパターンもいくつか含まれています。 Durable Functions がアプリ開発における課題の解決にどのように役立つかについて説明します。

![永続関数の種類を示す図][1]  

## <a name="types-of-durable-functions"></a>Durable Functions の種類

Azure Functions では、アクティビティ、オーケストレーター、およびクライアントという 3 種類の永続関数を使用できます。

### <a name="activity-functions"></a>アクティビティ関数

アクティビティ関数は、永続関数オーケストレーションの基本作業単位です。 アクティビティ関数は、プロセスでオーケストレーションされる関数とタスクです。 たとえば、注文を処理する永続関数を作成する場合があります。 このタスクには、在庫の確認、顧客への課金、および出荷の手配が含まれます。 それぞれのタスクがアクティビティ関数です。 

アクティビティ関数には、その中で実行できる作業の種類について制限がありません。 アクティビティ関数は、[Durable Functions でサポートされている言語](durable-functions-overview.md#language-support)で記述できます。 持続的タスク フレームワークによって、呼び出された各アクティビティ関数が、オーケストレーションの間、少なくとも 1 回実行されます。

アクティビティ関数を始動させるには、[アクティビティ トリガー](durable-functions-bindings.md#activity-triggers)を使用します。 .NET 関数はパラメーターとして [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) を受け取ります。 トリガーを他のオブジェクトにバインドし、入力を関数に渡すこともできます。 JavaScript では、[`context.bindings` オブジェクト](../functions-reference-node.md#bindings)の `<activity trigger binding name>` プロパティを介して入力にアクセスできます。

アクティビティ関数はオーケストレーターに値を返すこともできます。 アクティビティ関数から多数の値を送信したり返したりする場合は、[タプルや配列](durable-functions-bindings.md#passing-multiple-parameters)を使用できます。 アクティビティ関数は、オーケストレーション インスタンスからしかトリガーできません。 アクティビティ関数と別の関数 (HTTP でトリガーした関数など) が一部のコードを共有することはできますが、各関数に与えられるトリガーは 1 つに限られます。

詳細と例については、「[アクティビティ関数](durable-functions-bindings.md#activity-triggers)」を参照してください。

### <a name="orchestrator-functions"></a>オーケストレーター関数

オーケストレーター関数は、アクションが実行される方法とアクセスの実行順序を記述します。 オーケストレーター関数は、「[Durable Functions のパターンおよび技術的概念](durable-functions-concepts.md)」にあるように、コード (C# または JavaScript) でオーケストレーションを記述します。 1 つのオーケストレーションに、[アクティビティ関数](#activity-functions)、[サブオーケストレーション](#sub-orchestrations)、[外部イベントの待ち受け](#external-events)、[タイマー](#durable-timers)など、さまざまな種類のアクションを設定できます。 

オーケストレーター関数は[オーケストレーション トリガー](durable-functions-bindings.md#orchestration-triggers)によって始動させる必要があります。

オーケストレーターは[オーケストレーター クライアント](#client-functions)によって起動されます。 オーケストレーターはソース (HTTP、キュー、イベント ストリーム) からトリガーすることができます。 オーケストレーションの各インスタンスにはインスタンス ID が与えられます。 インスタンス ID の生成には自動生成 (推奨) とユーザー生成があります。 インストール ID は、オーケストレーションの[インスタンス管理](durable-functions-instance-management.md)に利用できます。

詳細と例については、「[オーケストレーション トリガー](durable-functions-bindings.md#orchestration-triggers)」を参照してください。

### <a name="client-functions"></a>クライアント関数

クライアント関数は、オーケストレーションの新しいインスタンスを作成するトリガー関数です。 クライアント関数は、Durable Functions オーケストレーションのインスタンス作成のエントリ ポイントです。 クライアント関数はソース (HTTP、キュー、イベント ストリーム) からトリガーすることができます。 クライアント関数は、アプリでサポートされている言語で記述できます。 

クライアント機能には[オーケストレーション クライアント](durable-functions-bindings.md#orchestration-client) バインディングも設定できます。 クライアント関数では、オーケストレーション クライアント バインディングを使用して持続的オーケストレーションを作成し、管理できます。 

クライアント関数の最も基本的な例は、オーケストレーター関数を開始して状態確認応答を返す、HTTP によってトリガーされる関数です。 例については、「[HTTP API URL の検出](durable-functions-http-api.md#http-api-url-discovery)」を参照してください。

詳細と例については、「[オーケストレーション クライアント](durable-functions-bindings.md#orchestration-client)」を参照してください。

## <a name="features-and-patterns"></a>機能とパターン

次のセクションでは、Durable Functions の種類の機能とパターンについて説明します。

### <a name="sub-orchestrations"></a>サブオーケストレーション

オーケストレーター関数はアクティビティ関数を呼び出すだけでなく、別のオーケストレーター関数を呼び出すこともできます。 たとえば、オーケストレーター関数のライブラリから大規模なオーケストレーションを作成できます。 つまり、オーケストレーター関数の複数のインスタンスを並列で実行できます。

詳細と例については、「[サブオーケストレーション](durable-functions-sub-orchestrations.md)」を参照してください。

### <a name="durable-timers"></a>持続的タイマー

[Durable Functions](durable-functions-overview.md) には、遅延を実装したり非同期アクションでタイムアウトを設定したりするためにオーケストレーター関数で使用できる、"*持続的タイマー*" が用意されています。 オーケストレーター関数の持続的タイマーは、`Thread.Sleep` および `Task.Delay` (C#)、または `setTimeout()` および `setInterval()` (JavaScript) の代わりに使用します。

詳細と例については、「[持続的タイマー](durable-functions-timers.md)」を参照してください。

### <a name="external-events"></a>外部イベント

オーケストレーター関数は、オーケストレーション インスタンスを更新するために、外部イベントを待ち受けできます。 Durable Functions のこの機能は、多くの場合、人による操作または他の外部コールバックを処理するときに便利です。

詳細と例については、「[外部イベント](durable-functions-external-events.md)」を参照してください。

### <a name="error-handling"></a>エラー処理

Durable Functions オーケストレーションを実行するには、コードを使用します。 プログラミング言語のエラー処理機能を使用できます。 `try`/`catch` などのパターンがオーケストレーションで機能します。 

Durable Functions には再試行ポリシーも組み込まれています。 アクションは、例外が発生したときに自動的にアクティビティを遅らせたり、再試行したりできます。 再試行を使用すると、オーケストレーションを破棄することなく、一時的な例外を処理できます。

詳細と例については、「[エラー処理](durable-functions-error-handling.md)」を参照してください。

### <a name="cross-function-app-communication"></a>関数アプリ間の通信

持続的オーケストレーションは 1 つの関数アプリという状況で実行されますが、パターンを使用して、さまざまな関数アプリ間でオーケストレーションを調整することができます。 アプリ間通信は HTTP で行われることがありますが、各アクティビティに持続的フレームワークを使用するということは、2 つのアプリ間で持続的プロセスを維持できることを意味します。

次の例は、C# と JavaScript の関数アプリ間オーケストレーションを示しています。 それぞれの例で、1 つのアクティビティが外部オーケストレーションを開始します。 もう 1 つのアクティビティが状態を取得して返します。 オーケストレーターは状態が `Complete` になるのを待ってから続行します。

関数アプリ間オーケストレーションのいくつかの例を次に示します。

#### <a name="c"></a>C#

```csharp
[FunctionName("OrchestratorA")]
public static async Task RunRemoteOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    // Do some work...

    // Call a remote orchestration.
    string statusUrl = await context.CallActivityAsync<string>(
        "StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete.
    while (true)
    {
        bool isComplete = await context.CallActivityAsync<bool>("CheckIsComplete", statusUrl);
        if (isComplete)
        {
            break;
        }

        await context.CreateTimer(context.CurrentUtcDateTime.AddMinutes(1), CancellationToken.None);
    }

    // B is done. Now, go do more work...
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

#### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x のみ)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    // Do some work...

    // Call a remote orchestration.
    const statusUrl = yield context.df.callActivity("StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete.
    while (true) {
        const isComplete = yield context.df.callActivity("CheckIsComplete", statusUrl);
        if (isComplete) {
            break;
        }

        const waitTime = moment(context.df.currentUtcDateTime).add(1, "m").toDate();
        yield context.df.createTimer(waitTime);
    }

    // B is done. Now, go do more work...
});
```

```javascript
const request = require("request-promise-native");

module.exports = async function(context, orchestratorName) {
    const options = {
        method: "POST",
        uri: `https://appB.azurewebsites.net/orchestrations/${orchestratorName}`,
        body: ""
    };

    const statusUrl = await request(options);
    return statusUrl;
};
```

```javascript
const request = require("request-promise-native");

module.exports = async function(context, statusUrl) {
    const options = {
        method: "GET",
        uri: statusUrl,
        resolveWithFullResponse: true,
    };

    const response = await request(options);
    // 200 = Complete, 202 = Running
    return response.statusCode === 200;
};
```

## <a name="next-steps"></a>次の手順

開始するには、[C#](durable-functions-create-first-csharp.md) または [JavaScript](quickstart-js-vscode.md) で最初の永続関数を作成してください。

> [!div class="nextstepaction"]
> [Durable Functions の詳細について読む](durable-functions-bindings.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png
