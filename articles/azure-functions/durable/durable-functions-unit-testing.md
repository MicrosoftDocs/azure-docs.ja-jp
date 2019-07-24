---
title: Azure Durable Functions の単体テスト
description: Durable Functions の単体テストを行う方法について説明します。
services: functions
author: kadimitr
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: kadimitr
ms.openlocfilehash: 69cf91f1448e36353f83de7a271abb3b53858bb0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58094077"
---
# <a name="durable-functions-unit-testing"></a>Durable Functions の単体テスト

単体テストは、最新ソフトウェアの開発プラクティスの重要な部分です。 単体テストでは、ビジネス ロジックの動作を確認して、今後、認識していない破壊的変更を組み入れることがないように保護します。 Durable Functions は容易に複雑になってしまうので、単体テストを導入することで、破壊的変更を回避できます。 以降のセクションでは、オーケストレーション クライアント、オーケストレーター、およびアクティビティ関数という 3 つの関数タイプを単体テストする方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事の例では、次の概念とフレームワークに関する知識が必要です。

* 単体テスト

* Durable Functions

* [xUnit](https://xunit.github.io/) - テスト フレームワーク

* [moq](https://github.com/moq/moq4) - モックの作成のフレームワーク

## <a name="base-classes-for-mocking"></a>モックの作成の基底クラス

モックの作成は、Durable Functions の以下の 3 つの抽象クラスを介してサポートされます。

* [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html)

* [DurableOrchestrationContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContextBase.html)

* [DurableActivityContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContextBase.html)

これらのクラスは、オーケストレーション クライアント、オーケストレーター、アクティビティのメソッドを定義する [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html)、[DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html)、[DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) の基底クラスです。 単体テストでビジネス ロジックを検証できるように、モックは、基底クラスのメソッドの予期される動作を設定します。 オーケストレーション クライアントとオーケストレーターにおけるビジネス ロジックの単体テストには、以下の 2 段階のワークフローがあります。

1. オーケストレーション クライアントとオーケストレーターの署名を定義するときに、具体的な実装ではなく、基底クラスを使用します。
2. 単体テストでは、基底クラスの動作をモックして、ビジネス ロジックを検証します。

オーケストレーション クライアントのバインディングとオーケストレーター トリガーのバインディングを使用する関数のテストについては、以降の説明で詳細を確認できます。

## <a name="unit-testing-trigger-functions"></a>トリガー関数の単体テスト

このセクションでは、新しいオーケストレーションを開始するための以下の HTTP トリガー関数のロジックを、単体テストで検証します。

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

単体テスト タスクでは、応答のペイロードで指定された `Retry-After` の値を検証します。 そのため、単体テストでは、予測可能な動作を確認するために、一部の [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html) メソッドをモックします。

最初に、基底クラスのモックが必要になります ([DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html))。 モックは、[DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html)を実装する新しいクラスすることが可能です。 ただし、[moq](https://github.com/moq/moq4) などのモック作成のフレームワークを使用すると、プロセスが簡単になります。

```csharp
    // Mock DurableOrchestrationClientBase
    var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();
```

その後、`StartNewAsync` メソッドは、既知のインスタンス ID を返すようにモックされます。

```csharp
    // Mock StartNewAsync method
    durableOrchestrationClientBaseMock.
        Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
        ReturnsAsync(instanceId);
```

次の `CreateCheckStatusResponse` は、常に空の HTTP 200 の応答が返るようにモックされます。

```csharp
    // Mock CreateCheckStatusResponse method
    durableOrchestrationClientBaseMock
        .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId))
        .Returns(new HttpResponseMessage
        {
            StatusCode = HttpStatusCode.OK,
            Content = new StringContent(string.Empty),
            Headers =
            {
                RetryAfter = new RetryConditionHeaderValue(TimeSpan.FromSeconds(10))
            }
        });
```

`TraceWriter` もモックされます。

```csharp
    // Mock TraceWriter
    var traceWriterMock = new Mock<TraceWriter>(TraceLevel.Info);

```  

ここで、`Run` メソッドが、以下のように単体テストから呼び出されます。

```csharp
    // Call Orchestration trigger function
    var result = await HttpStart.Run(
        new HttpRequestMessage()
        {
            Content = new StringContent("{}", Encoding.UTF8, "application/json"),
            RequestUri = new Uri("http://localhost:7071/orchestrators/E1_HelloSequence"),
        },
        durableOrchestrationClientBaseMock.Object,
        functionName,
        traceWriterMock.Object);
 ```

 最後のステップでは、予期される値と出力を比較します。

```csharp
    // Validate that output is not null
    Assert.NotNull(result.Headers.RetryAfter);

    // Validate output's Retry-After header value
    Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

すべてのステップを組み合わせると、単体テストは次のようなコードになります。

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>オーケストレーター関数の単体テスト

オーケストレーターの関数は多くの場合、より多くのビジネス ロジックを含んでいるため、単体テストはより興味深いものになります。

このセクションでは、単体テストで `E1_HelloSequence` オーケストレーター関数の出力を検証します。

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

単体テストのコードは、モックの作成から始まります。

```csharp
    var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
```

その後、アクティビティ メソッドの呼び出しがモックされます。

```csharp
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

次に、単体テストは、`HelloSequence.Run` メソッドを呼び出します。

```csharp
    var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

そして最後に、出力が検証されます。

```csharp
    Assert.Equal(3, result.Count);
    Assert.Equal("Hello Tokyo!", result[0]);
    Assert.Equal("Hello Seattle!", result[1]);
    Assert.Equal("Hello London!", result[2]);
```

すべてのステップを組み合わせると、単体テストは次のようなコードになります。

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>アクティビティ関数の単体テスト

アクティビティ関数は、持続性のない関数と同様に単体テストを行うことができます。

このセクションでは、単体テストで次のように `E1_SayHello` アクティビティ関数の動作を検証します。

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

また、単体テストでは、出力の形式を確認します。 単体テストでは、パラメーターの型を直接使用するか、[DurableActivityContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContextBase.html) クラスをモックできます。

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [xUnit の詳細を確認する](https://xunit.github.io/docs/getting-started-dotnet-core)
> 
> [moq の詳細を確認する](https://github.com/Moq/moq4/wiki/Quickstart)
