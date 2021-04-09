---
title: Azure Durable Functions の関数の型
description: Azure Functions の Durable Functions オーケストレーションでの関数間の情報交換をサポートする、関数とロールの種類について説明します。
author: cgillum
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: azfuncdf
ms.openlocfilehash: 35ef9d8731e169e890f5985ce01215fec5d6e3de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84697709"
---
# <a name="durable-functions-types-and-features"></a>Durable Functions の型と機能

Durable Functions は [Azure Functions](../functions-overview.md) の拡張機能です。 Durable Functions は、関数実行のステートフル オーケストレーションに使用できます。 持続的関数アプリとはさまざまな Azure 関数で構成されるソリューションです。 Functions は永続関数のオーケストレーションでさまざまな役割を果たすことができます。 

現在、Azure Functions には、アクティビティ、オーケストレーター、エンティティ、クライアントという 4 種類の持続的関数があります。 このセクションの残りの部分では、オーケストレーションに関係する関数の種類について詳しく説明します。

## <a name="orchestrator-functions"></a>オーケストレーター関数

オーケストレーター関数は、アクションが実行される方法とアクセスの実行順序を記述します。 オーケストレーター関数は、[Durable Functions のアプリケーション パターン](durable-functions-overview.md#application-patterns)に関するページにあるように、コード (C# または JavaScript) でオーケストレーションを表現します。 1 つのオーケストレーションに、[アクティビティ関数](#activity-functions)、[サブオーケストレーション](durable-functions-orchestrations.md#sub-orchestrations)、[外部イベントの待ち受け](durable-functions-orchestrations.md#external-events)、[HTTP](durable-functions-http-features.md)、[タイマー](durable-functions-orchestrations.md#durable-timers)など、さまざまな種類のアクションを設定できます。 Orchestrator 関数は、[エンティティ関数](#entity-functions)とやりとりすることもできます。

> [!NOTE]
> オーケストレーター関数は通常のコードを使用して記述されますが、コードの記述方法については厳密な要件があります。 具体的には、オーケストレーター関数のコードは *決定的* である必要があります。 これらの決定性の要件に従わないと、オーケストレーター関数が正しく動作しない可能性があります。 これらの要件とその対処方法の詳細については、[コードの制約](durable-functions-code-constraints.md)に関するトピックを参照してください。

オーケストレーター関数とその機能の詳細については、「[永続的オーケストレーション](durable-functions-orchestrations.md)」の記事を参照してください。

## <a name="activity-functions"></a>アクティビティ関数

アクティビティ関数は、永続関数オーケストレーションの基本作業単位です。 アクティビティ関数は、プロセスでオーケストレーションされる関数とタスクです。 たとえば、注文を処理するオーケストレーター関数を作成する場合があります。 このタスクには、在庫の確認、顧客への課金、および出荷の手配が含まれます。 それぞれのタスクが別個のアクティビティ関数になります。 これらのアクティビティ関数は、順次、並列、またはその両方の組み合わせで実行できます。

オーケストレーター関数とは異なり、アクティビティ関数には、その中で実行できる作業の種類について制限がありません。 アクティビティ関数は、ネットワーク呼び出しを行ったり、CPU 負荷の高い操作を実行したりするためによく使用されます。 アクティビティ関数は、オーケストレーターに値を返すこともできます。 Durable Task Framework によって、呼び出された各アクティビティ関数がオーケストレーションの実行中に "*少なくとも 1 回*" 実行されることが保証されます。

> [!NOTE]
> アクティビティ関数は "*少なくとも 1 回*" の実行を保証するのみであるため、可能な限りアクティビティ関数のロジックを "*べき等*" にすることをお勧めします。

アクティビティ関数を定義するには、[アクティビティ トリガー](durable-functions-bindings.md#activity-trigger)を使用します。 .NET 関数は `DurableActivityContext` をパラメーターとして受け取ります。 トリガーを他の JSON シリアル化可能オブジェクトにバインドして、入力を関数に渡すこともできます。 JavaScript では、[`context.bindings` オブジェクト](../functions-reference-node.md#bindings)の `<activity trigger binding name>` プロパティを介して入力にアクセスできます。 アクティビティ関数に渡すことができる値は 1 つだけです。 複数の値を渡すには、タプル、配列、または複合型を使用する必要があります。

> [!NOTE]
> アクティビティ関数は、オーケストレーター関数からのみトリガーできます。

## <a name="entity-functions"></a>エンティティ関数

エンティティ関数では、状態の小さな部分の読み取りと更新のための操作を定義します。 これらのステートフル エンティティは、"*持続エンティティ*" と呼ばれることがよくあります。 オーケストレーター関数と同様、エンティティ関数は特殊なトリガー型である "*エンティティ トリガー*" を含む関数です。 これらは、クライアント関数またはオーケストレーター関数から呼び出すこともできます。 オーケストレーター関数とは異なり、エンティティ関数には特定のコードの制約はありません。 また、エンティティ関数では、制御フローを介して状態を表す暗黙的ではなく、明示的に状態が管理されます。

> [!NOTE]
> エンティティ関数と関連する機能は Durable Functions 2.0 以降でのみ使用できます。

エンティティ関数の詳細については、[持続エンティティ](durable-functions-entities.md)に関する記事を参照してください。

## <a name="client-functions"></a>クライアント関数

オーケストレーター関数は[オーケストレーション トリガー バインディング](durable-functions-bindings.md#orchestration-trigger)によってトリガーされ、エンティティ関数は[エンティティ トリガー バインディング](durable-functions-bindings.md#entity-trigger)によってトリガーされます。 これらのトリガーはどちらも、[タスク ハブ](durable-functions-task-hubs.md)にエンキューされているメッセージに反応することによって機能します。 これらのメッセージを配信する主な方法は、"*クライアント関数*" 内から [オーケストレーター クライアント バインディング](durable-functions-bindings.md#orchestration-client)または [エンティティ クライアント バインディング](durable-functions-bindings.md#entity-client)を使用する方法です。 オーケストレーター以外の関数は、"*クライアント関数*" にすることができます。 たとえば、オーケストレーターは、HTTP によってトリガーされる関数や Azure Event Hub によってトリガーされる関数などからトリガーできます。関数を "*クライアント関数*" にするのは、持続的クライアント出力バインディングの使用です。

> [!NOTE]
> 他の関数タイプとは異なり、オーケストレーター関数とエンティティ関数は、Azure portal のボタンを使用して直接トリガーすることはできません。 Azure portal でオーケストレーターまたはエンティティ関数をテストする場合は、実装の一部としてオーケストレーターまたはエンティティ関数を開始する "*クライアント関数*" を代わりに実行する必要があります。 最も簡単なテスト エクスペリエンスを実現するには、"*手動トリガー*" 関数をお勧めします。

オーケストレーターまたはエンティティ関数をトリガーするだけでなく、*持続的クライアント* バインディングを使用して、実行中のオーケストレーションおよびエンティティと対話できます。 たとえば、オーケストレーションに対してクエリを実行したり、オーケストレーションを終了したりできます。また、オーケストレーションに対してイベントを生成することもできます。 オーケストレーションとエンティティの管理の詳細については、[インスタンスの管理](durable-functions-instance-management.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

開始するには、[C#](durable-functions-create-first-csharp.md) または [JavaScript](quickstart-js-vscode.md) で最初の永続関数を作成してください。

> [!div class="nextstepaction"]
> [Durable Functions オーケストレーションの詳細について読む](durable-functions-orchestrations.md)