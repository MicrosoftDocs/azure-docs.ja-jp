---
title: Durable Functions でのバージョン管理 - Azure
description: Azure Functions の Durable Functions 拡張機能にバージョン管理を実装する方法を説明します。
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 87cbb94dbab241630dc7585bdf4314d858d5b4da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "74232759"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Durable Functions (Azure Functions) でのバージョン管理

アプリケーションの存続期間中に関数が追加、削除、変更されることは避けられません。 [Durable Functions](durable-functions-overview.md) では、以前は不可能だった方法で関数を連結できます。この連結は、バージョン管理への対応方法に影響します。

## <a name="how-to-handle-breaking-changes"></a>重大な変更に対応する方法

注意すべき重大な変更の例がいくつかあります。 この記事では、最も一般的なものについて説明します。 それらすべての背後にある主なテーマは、関数コードの変更によって新規と既存両方の関数オーケストレーションが影響を受けることです。

### <a name="changing-activity-or-entity-function-signatures"></a>アクティビティ関数シグネチャまたはエンティティ関数シグネチャの変更

シグネチャの変更とは、関数の名前、入力、または出力の変更のことを指します。 アクティビティ関数またはエンティティ関数にこの種の変更を行うと、それに依存するオーケストレーター関数が中断される可能性があります。 この変更に対応してオーケストレーター関数を更新すると、処理中の既存のインスタンスが中断される可能性があります。

例として、次のようなオーケストレーター関数があるとします。

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

この単純な関数は、**Foo** の結果を取得して **Bar** に渡します。 広範な結果値をサポートするために、**Foo** の戻り値を、`bool` から `int` に変更する必要があるとします。 結果は次のようになります。

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> 前述の C# の例は Durable Functions 2.x をターゲットにしています。 Durable Functions 1.x の場合、`IDurableOrchestrationContext` の代わりに `DurableOrchestrationContext` を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

この変更は、オーケストレーター関数の新しいインスタンスではすべて正しく機能しますが、処理中のインスタンスでは中断を発生させます。 たとえば、オーケストレーション インスタンスで `Foo` という名前の関数を呼び出し、ブール値を返し、その後チェックポイントを返す場合を考えます。 シグネチャの変更がこの時点でデプロイされると、チェックポイントが付いたインスタンスは、`context.CallActivityAsync<int>("Foo")` の呼び出しを再開して再生すると、直ちに失敗します。 この失敗が起きるのは、履歴テーブル内の結果が `bool` であるにも関わらず、新しいコードで `int` への逆シリアル化が試行されるためです。

この例は、シグネチャの変更によって既存のインスタンスが中断するさまざまな原因の 1 つにすぎません。 一般に、オーケストレーターで関数の呼び出し方を変更する必要がある場合、その変更に問題がある可能性があります。

### <a name="changing-orchestrator-logic"></a>オーケストレーターのロジックの変更

その他のバージョン管理の問題は、処理中のインスタンスの再生ロジックをわかりにくくするようなオーケストレーター関数コードの変更が原因となっています。

次のオーケストレーター関数について考えてみます。

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

ここでは、別の関数呼び出しを追加するという一見無害な変更を行うとします。

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    if (result)
    {
        await context.CallActivityAsync("SendNotification");
    }

    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> 前述の C# の例は Durable Functions 2.x をターゲットにしています。 Durable Functions 1.x の場合、`IDurableOrchestrationContext` の代わりに `DurableOrchestrationContext` を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

この変更では、**Foo** と **Bar** の間に **SendNotification** への新しい関数呼び出しを追加します。 シグネチャの変更はありません。 **Bar** の呼び出しから既存のインスタンスを再開すると、問題が発生します。 再生中に、元の **Foo** への呼び出しから `true` が返されると、オーケストレーターの再生で実行履歴にない **SendNotification** が呼び出されます。 その結果、**Bar** の呼び出しの実行が想定されるときに **SendNotification** の呼び出しが発生したことで、Durable Task フレームワークが `NonDeterministicOrchestrationException` で失敗します。 `CreateTimer`、`WaitForExternalEvent` などの "持続性のある" API の呼び出しを追加すると、同じ種類の問題が発生する可能性があります。

## <a name="mitigation-strategies"></a>対応方法

バージョン管理の課題に対応するための方法をいくつか次に示します。

* 何もしない
* 処理中のすべてのインスタンスを停止する
* 並行してデプロイする

### <a name="do-nothing"></a>何もしない

重大な変更に対応する最も簡単な方法は、処理中のオーケストレーション インスタンスを失敗させることです。 新しいインスタンスでは、変更されたコードが正常に実行されます。

このような失敗が問題かどうかは、処理中のインスタンスの重要度によって異なります。 現在開発中で、処理中のインスタンスが重要でない場合は、この方法で十分であることもあります。 ただし、診断パイプラインで例外とエラーに対応する必要があります。 そのような対応を行いたくない場合は、バージョン管理のその他の選択肢を検討してください。

### <a name="stop-all-in-flight-instances"></a>処理中のすべてのインスタンスを停止する

別の選択肢として、処理中のすべてのインスタンスを停止することもできます。 すべてのインスタンスを停止するには、内部の **control-queue** キューと **workitem-queue** キューの内容を消去します。 インスタンスはその場所で永久に停止しますが、ログにエラー メッセージを大量に書き込むことはありません。 このアプローチは迅速なプロトタイプ開発に最適です。

> [!WARNING]
> これらのキューの詳細は時間の経過と共に変わることがあるため、この手法は運用環境のワークロードには使用しないでください。

### <a name="side-by-side-deployments"></a>並行してデプロイする

重大な変更が安全にデプロイされるようにする最も失敗のない方法は、古いバージョンと並行してデプロイすることです。 これを行うには、次のいずれかの手法を使用します。

* 既存の関数のままにして、すべての更新プログラムをまったく新しい関数としてデプロイする。 これは、新しい関数のバージョンの呼び出し元を同じガイドラインに従って更新する必要があるため、複雑になる可能性があります。
* 別のストレージ アカウントを使用して、すべての更新プログラムを新しい関数アプリとしてデプロイする。
* 関数アプリの新しいコピーを、同じストレージ アカウントですが、更新された名前 `taskHub` でデプロイする。 推奨される手法は並行デプロイです。

### <a name="how-to-change-task-hub-name"></a>タスク ハブ名を変更する方法

タスク ハブは、*host.json* ファイルに次のように構成できます。

#### <a name="functions-1x"></a>Functions 1.x

```json
{
    "durableTask": {
        "hubName": "MyTaskHubV2"
    }
}
```

#### <a name="functions-20"></a>Functions 2.0

```json
{
    "extensions": {
        "durableTask": {
            "hubName": "MyTaskHubV2"
        }
    }
}
```

Durable Functions v1.x の既定値は `DurableFunctionsHub` です。 Durable Functions v2.0 以降では、既定のタスク ハブ名は、Azure の関数アプリ名と同じか、Azure の外部で実行されている場合は `TestHubName` になります。

すべての Azure Storage エンティティは、`hubName` 構成値に基づいて名前が付けられます。 タスク ハブに新しい名前を付けることにより、新しいバージョンのアプリケーション用に別個のキューと履歴テーブルを作成できます。 しかし、関数アプリでは、前のタスク ハブ名で作成されたオーケストレーションまたはエンティティのイベント処理は停止されます。

新しいバージョンの関数アプリは新しい[デプロイ スロット](../functions-deployment-slots.md)にデプロイすることをお勧めします。 デプロイ スロットでは、複数のコピーの関数アプリを並行して実行し、そのうちの 1 つのみをアクティブな *運用* スロットとして実行できます。 既存のインフラストラクチャに新しいオーケストレーション ロジックを公開する準備ができている場合、新しいバージョンを運用スロットにスワップするのと同じくらい簡単です。

> [!NOTE]
> この方法は、オーケストレーター関数の HTTP トリガーや Webhook トリガーを使用する場合に最適です。 キューや Event Hubs などの HTTP 以外のトリガーの場合、トリガー定義は、スワップ操作の一環として更新された[アプリ設定を派生する](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings)必要があります。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [パフォーマンスとスケーリングの問題に対応する方法を知る](durable-functions-perf-and-scale.md)
