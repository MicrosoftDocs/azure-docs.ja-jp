---
title: Durable Functions での永続的オーケストレーション - Azure
description: Azure Functions の Durable Functions 拡張機能を使用して永続的オーケストレーションを実装する方法について説明します。
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: c4adffd457338ffebfd1c9c7727023f82088dc57
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2019
ms.locfileid: "59607740"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Durable Functions での永続的オーケストレーション (Azure Functions)

"*永続的オーケストレーション*" は、終了しないオーケストレーター関数です。 これは、アグリゲーター、無限ループを必要とする任意のシナリオに対して、[Durable Functions](durable-functions-overview.md) を使用する必要があるときに便利です。

## <a name="orchestration-history"></a>オーケストレーションの履歴

[チェックポイント処理および再生](durable-functions-checkpointing-and-replay.md)に関するページで説明したように、Durable Task Framework では、各関数オーケストレーションの履歴が追跡されます。 この履歴は、オーケストレーター関数が新しい作業をスケジュールする限り、拡大し続けます。 オーケストレーター関数が無限ループに入り、作業を継続的にスケジュールすると、この履歴は巨大になり、パフォーマンスが大幅に低下する可能性があります。 "*永続的オーケストレーション*" の概念は、無限ループを必要とするアプリケーションのこうした問題を軽減することを目的としています。

## <a name="resetting-and-restarting"></a>リセットと再開

オーケストレーター関数は、無限ループを使用するのではなく、[ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) メソッドを呼び出すことで状態をリセットします。 このメソッドでは、JSON でシリアル化できる 1 つのパラメーターが使用され、このパラメーターが、次のオーケストレーター関数生成に対する新しい入力になります。

`ContinueAsNew` が呼び出されると、インスタンスは、終了前に、自身に対してメッセージをエンキューします。 メッセージは、新しい入力値でインスタンスを再開します。 同じインスタンス ID が保持されますが、オーケストレーター関数の履歴は効果的に切り詰められます。

> [!NOTE]
> Durable Task Framework では同じインスタンス ID が維持されますが、内部的には、`ContinueAsNew` によってリセットされるオーケストレーター関数に対して、新しい "*実行 ID*" が作成されます。 一般的に、この実行 ID は外部に公開されませんが、オーケストレーション実行のデバッグのタイミングを確認するときに役に立つ場合があります。

## <a name="periodic-work-example"></a>定期的な作業の例

永続的オーケストレーションのユース ケースの 1 つが、定期的な作業を無期限に実行する必要があるコードです。

### <a name="c"></a>C#

```csharp
[FunctionName("Periodic_Cleanup_Loop")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    await context.CallActivityAsync("DoCleanup");

    // sleep for one hour between cleanups
    DateTime nextCleanup = context.CurrentUtcDateTime.AddHours(1);
    await context.CreateTimer(nextCleanup, CancellationToken.None);

    context.ContinueAsNew(null);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x のみ)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("DoCleanup");

    // sleep for one hour between cleanups
    const nextCleanup = moment.utc(context.df.currentUtcDateTime).add(1, "h");
    yield context.df.createTimer(nextCleanup);

    context.df.continueAsNew(undefined);
});
```

この例と、タイマーによってトリガーされる関数の違いは、クリーンアップ トリガーのタイミングです。この例のタイミングはスケジュールに基づいていません。 たとえば、1 時間ごとに関数を実行する CRON スケジュールでは、1:00、2:00、3:00 といったタイミングで関数が実行され、重複の問題が発生する可能性があります。 この例では、クリーンアップ所要時間が 30 分の場合は、1:00、2:30、4:00 にスケジュールされるため、重複することはありません。

## <a name="exit-from-an-eternal-orchestration"></a>永続的オーケストレーションの終了

オーケストレーター関数は、最終的に完了する必要がある場合は、`ContinueAsNew` を "*呼び出さない*" でください。呼び出さなければ、関数は終了します。

オーケストレーター関数が無限ループに入っている場合、これを停止する必要がある場合は、[TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) メソッドを使用します。 詳しくは、[インスタンス管理](durable-functions-instance-management.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [単一のオーケストレーションを実装する方法を確認する](durable-functions-singletons.md)
