---
title: Durable Functions のプレビュー機能 - Azure Functions
description: Durable Functions のプレビュー機能について説明します。
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: article
ms.date: 04/23/2019
ms.author: azfuncdf
ms.openlocfilehash: 6e96c4361cf086884bb483e2268b592dac40140b
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606066"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Durable Functions 2.0 プレビュー (Azure Functions)

*Durable Functions* は、サーバーレス環境でステートフル関数を記述できる、[Azure Functions](../functions-overview.md) と [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) の拡張機能です。 この拡張機能は状態、チェックポイント、再起動を管理します。 Durable Functions にまだ慣れていない場合は、[概要ドキュメント](durable-functions-overview.md)をご覧ください。

Durable Functions は Azure Functions の GA (一般提供) 機能ですが、現在パブリック プレビュー段階のサブ機能もいくつか含まれています。 この記事では、新しくリリースされたプレビュー機能について説明し、そのしくみと使い始める方法を詳しく見ていきます。

> [!NOTE]
> これらのプレビュー機能は Durable Functions 2.0 リリースの一部であり、現在それはいくつかの破壊的変更を含む**アルファ品質リリース**です。 Azure Functions Durable 拡張機能パッケージ ビルドは、nuget.org に **2.0.0-alpha** という形式のバージョンで存在します。 これらのビルドは運用環境のワークロードには適しておらず、今後のリリースで破壊的変更が追加される可能性があります。

## <a name="breaking-changes"></a>重大な変更

Durable Functions 2.0 では、破壊的変更がいくつか導入されています。 既存のアプリケーションは、コードを変更しないと Durable Functions 2.0 と互換性がないものと思われます。 このセクションでは、変更の一部を示します。

### <a name="dropping-net-framework-support"></a>.NET Framework のサポートの削除

.NET Framework (したがって Functions 1.0) のサポートは、Durable Functions 2.0 で削除されました。 主な理由は、Windows 以外の共同作成者が macOS や Linux プラットフォームから Durable Functions に対して行った変更を、簡単にビルドしてテストできるようにするためです。 二次的な理由は、最新バージョンの Azure Functions ランタイムへの移行を開発者に奨励することです。

### <a name="hostjson-schema"></a>Host.json スキーマ

次のスニペットでは、host.json の新しいスキーマを示します。 注意すべき主な変更点は、新しい `"storageProvider"` セクションと、その下にある `"azureStorage"` セクションです。 この変更は、[代替ストレージ プロバイダー](durable-functions-preview.md#alternate-storage-providers)をサポートするために行われました。

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "azureStorage": {
          "connectionStringName": <string>,
          "controlQueueBatchSize": <int?>,
          "partitionCount": <int?>,
          "controlQueueVisibilityTimeout": <hh:mm:ss?>,
          "workItemQueueVisibilityTimeout": <hh:mm:ss?>,
          "trackingStoreConnectionStringName": <string?>,
          "trackingStoreNamePrefix": <string?>,
          "maxQueuePollingInterval": <hh:mm:ss?>
        }
      },
      "maxConcurrentActivityFunctions": <int?>,
      "maxConcurrentOrchestratorFunctions": <int?>,
      "traceInputAndOutputs": <bool?>,
      "eventGridTopicEndpoint": <string?>,
      "eventGridKeySettingName": <string?>,
      "eventGridPublishRetryCount": <string?>,
      "eventGridPublishRetryInterval": <hh:mm:ss?>,
      "eventGridPublishRetryHttpStatus": <int[]?>,
      "eventgridPublishEventTypes": <string[]?>,
      "customLifeCycleNotificationHelperType"
      "extendedSessionsEnabled": <bool?>,
      "extendedSessionIdleTimeoutInSeconds": <int?>,
      "logReplayEvents": <bool?>
  }
}
```

Durable Functions 2.0 は安定化が続けられており、host.json の `durableTask` セクションはさらに変更されます。 これらの変更について詳しくは、[こちらの GitHub の問題](https://github.com/Azure/azure-functions-durable-extension/issues/641)をご覧ください。

### <a name="public-interface-changes"></a>パブリック インターフェイスの変更

Durable Functions によってサポートされるさまざまな "コンテキスト" オブジェクトには、単体テストでの使用が意図される抽象基本クラスが含まれました。 Durable Functions 2.0 の一部として、これらの抽象基本クラスがインターフェイスに置き換えられました。 具象型を直接使用する関数のコードには影響しません。

次の表では、主な変更を示します。

| 古い型 | 新しい型 |
|----------|----------|
| DurableOrchestrationClientBase | IDurableOrchestrationClient |
| DurableOrchestrationContextBase | IDurableOrchestrationContext |
| DurableActivityContextBase | IDurableActivityContext |

抽象基本クラスに仮想メソッドが含まれていた場合、これらの仮想メソッドは `DurableContextExtensions` で定義される拡張メソッドに置き換えられています。

## <a name="entity-functions"></a>エンティティ関数

エンティティ関数では、"*持続エンティティ*" と呼ばれる小さい状態の読み取りと更新のための操作が定義されています。 オーケストレーター関数と同様、エンティティ関数は特殊なトリガー型である "*エンティティ トリガー*" を含む関数です。 オーケストレーター関数とは異なり、エンティティ関数には特定のコードの制約はありません。 また、エンティティ関数では、制御フローを介して状態を表す暗黙的ではなく、明示的に状態が管理されます。

次のコードは、*Counter* エンティティを定義する簡単なエンティティ関数の例です。 その関数では 3 つの操作 `add`、`subtract`、`reset` が定義されており、いずれでも整数値 `currentValue` が更新されます。

```csharp
[FunctionName("Counter")]
public static async Task Counter(
    [EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();
    int operand = ctx.GetInput<int>();

    switch (ctx.OperationName)
    {
        case "add":
            currentValue += operand;
            break;
        case "subtract":
            currentValue -= operand;
            break;
        case "reset":
            await SendResetNotificationAsync();
            currentValue = 0;
            break;
    }

    ctx.SetState(currentValue);
}
```

エンティティの "*インスタンス*" には、一意の識別子である "*エンティティ ID*" を使用してアクセスします。 エンティティ ID は単に、エンティティのインスタンスを一意に示す文字列のペアです。 構成は次のとおりです。

1. **エンティティ名**: エンティティの種類を示す名前 (例: "Counter")
2. **エンティティ キー**: 同じ名前の他のすべてのエンティティの間でそのエンティティを一意に示す文字列 (例: GUID)

たとえば、"*カウンター*" エンティティ関数は、オンライン ゲームでスコアを保持するために使用される可能性があります。 ゲームの各インスタンスは、`@Counter@Game1`、`@Counter@Game2` のような一意のエンティティ ID を持ちます。

### <a name="comparison-with-virtual-actors"></a>仮想アクターとの比較

持続エンティティの設計には、[アクター モデル](https://en.wikipedia.org/wiki/Actor_model)が大きく影響しています。 アクターについて既に理解している場合、持続エンティティの背後にある概念はよくわかるはずです。 具体的には、持続エンティティは多くの点で[仮想アクター](https://research.microsoft.com/en-us/projects/orleans/)と似ています。

* 持続エンティティは、"*エンティティ ID*" を使用してアドレス指定可能です。
* 持続エンティティの操作は、競合状態を避けるため、一度に 1 つずつ順番に実行されます。
* 持続エンティティは、呼び出されるか、シグナルされると、自動的に作成されます。
* 実行する操作がなくなると、持続エンティティはメモリからサイレントにアンロードされます。

ただし、いくつか重要な違いがあります。

* 持続エンティティは、純粋な関数としてモデル化されます。 この設計は、クラス、プロパティ、メソッドの言語固有のサポートを使用してアクターを表すほとんどのオブジェクト指向フレームワークと異なります。
* 持続エンティティは、"*待ち時間*" より "*持続性*" の方が優先されるので、待ち時間の要件が厳しいアプリケーションには適していない可能性があります。
* エンティティ間で送信されるメッセージは、信頼性の高い方法で順番に配信されます。
* 持続エンティティは、持続性のあるオーケストレーションと組み合わせて使用でき、この記事の後半で説明する分散ロックとして使用できます。
* エンティティでの要求/応答パターンは、オーケストレーションに制限されます。 エンティティ間通信では、元のアクター モデルのように、一方向のメッセージ (別名 "シグナリング") のみが許可されます。 この動作により、分散デッドロックが防止されます。

### <a name="durable-entity-apis"></a>持続エンティティの API

エンティティのサポートには、いくつかの API が含まれます。 たとえば、上で示したように、エンティティ関数を定義するための新しい API があり、エンティティで操作が呼び出されるときの動作が指定されています。 また、クライアントとオーケストレーションに関する既存の API が、エンティティと相互作用するための新しい機能で更新されています。

### <a name="implementing-entity-operations"></a>エンティティの操作の実装

エンティティでの操作の実行では、コンテキスト オブジェクト (.NET では`IDurableEntityContext`) で次のメンバーを呼び出すことができます。

* **OperationName**: 操作の名前を取得します。
* **GetInput\<T>** : 操作に対する入力を取得します。
* **GetState\<T>** : エンティティの現在の状態を取得します。
* **SetState**: エンティティの状態を更新します。
* **SignalEntity**: エンティティに一方向のメッセージを送信します。
* **Self**: エンティティの ID を取得します。
* **Return**: 操作を呼び出したクライアントまたはオーケストレーションに値を返します。
* **IsNewlyConstructed**: 操作の前にエンティティが存在しなかった場合、`true` を返します。
* **DestructOnExit**: 操作が完了したら、エンティティを削除します。

操作はオーケストレーションほど制限が厳しくありません。

* 操作では、同期または非同期の API を使用して、外部の I/O を呼び出すことができます (非同期のもののみを使用することをお勧めします)。
* 操作は決定論的ではない場合があります。 たとえば、`DateTime.UtcNow`、`Guid.NewGuid()`、または `new Random()` を呼び出しても安全です。

### <a name="accessing-entities-from-clients"></a>クライアントからエンティティへのアクセス

持続エンティティは、`orchestrationClient` バインド (.NET では `IDurableOrchestrationClient`) を使用して通常の関数から呼び出すことができます。 以下のメソッドがサポートされています。

* **ReadEntityStateAsync\<T>** : エンティティの状態を読み取ります。
* **SignalEntityAsync**: エンティティに一方向のメッセージを送信し、エンキューされるまで待機します。

これらのメソッドでは、一貫性よりパフォーマンスが優先されています。`ReadEntityStateAsync` は古い値を返すことがあり、`SignalEntityAsync` は操作が完了する前に返ることがあります。 これに対し、オーケストレーションからのエンティティの呼び出しでは、(次に示すように) 整合性が厳密です。

### <a name="accessing-entities-from-orchestrations"></a>オーケストレーションからエンティティへのアクセス

オーケストレーションでは、コンテキスト オブジェクトを使用してエンティティにアクセスできます。 一方向の通信の (ファイア アンド フォーゲット) または双方向の通信 (要求と応答) を選択できます。 それぞれのメソッドは次のとおりです。

* **SignalEntity**: エンティティに一方向のメッセージを送信します。
* **CallEntityAsync**: エンティティにメッセージを送信し、操作が完了したことを示す応答を待ちます。
* **CallEntityAsync\<T>** : エンティティにメッセージを送信し、T 型の結果を含む応答を待ちます。

双方向の通信を使用すると、操作の実行中にスローされた例外も、呼び出し元のオーケストレーションに返送されて、再スローされます。 これに対し、ファイア アンド フォーゲットを使用すると、例外は反映されません。

### <a name="locking-entities-from-orchestrations"></a>オーケストレーションからのエンティティのロック

オーケストレーションでは、エンティティをロックできます。 この機能では、"*クリティカル セクション*" を使用することによって望ましくない競合を防ぐ簡単な方法が提供されます。

コンテキスト オブジェクトでは次のメソッドが提供されます。

* **LockAsync**: 1 つまたは複数のエンティティでロックを取得します。
* **IsLocked**: 現在クリティカル セクションにいる場合は true を返し、それ以外の場合は false を返します。

オーケストレーションが終了すると、クリティカル セクションが終了し、すべてのロックが解放されます。 .NET では、`LockAsync` から破棄されるとクリティカル セクションを終了する `IDisposable` が返され、それを `using` 句と組み合わせて使用して、クリティカル セクションの構文表現を取得できます。

たとえば、2 人のプレーヤーが使用可能かどうかをテストした後、両方をゲームに割り当る必要があるオーケストレーションについて考えます。 このタスクは、次のようにクリティカル セクションを使用して実装できます。

```csharp
[FunctionName("Orchestrator")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext ctx)
{
    EntityId player1 = /* ... */;
    EntityId player2 = /* ... */;

    using (await ctx.LockAsync(player1, player2))
    {
        bool available1 = await ctx.CallEntityAsync<bool>(player1, "is-available");
        bool available2 = await ctx.CallEntityAsync<bool>(player2, "is-available");

        if (available1 && available2)
        {
            Guid gameId = ctx.NewGuid();

            await ctx.CallEntityAsync(player1, "assign-game", gameId);
            await ctx.CallEntityAsync(player2, "assign-game", gameId);
        }
    }
}
```

クリティカル セクション内では両方のプレーヤー エンティティがロックされ、これはクリティカル セクション内から呼び出されるもの以外の操作を実行していないことを意味します。 この動作により、異なるゲームに割り当てられているプレーヤーや、サインオフなど、競合する操作が防止されます。

クリティカル セクションの使用方法には、いくつかの制限が課せられています。 これらの制限は、デッドロックや再入を防ぐために機能します。

* クリティカル セクションを入れ子にすることはできません。
* クリティカル セクションでサブオーケストレーションを作成することはできません。
* クリティカル セクションでは、自分がロックしているエンティティのみを呼び出すことができます。
* クリティカル セクションでは、複数の並列呼び出しを使用して、同じエンティティを呼び出すことはできません。
* クリティカル セクションでは、自分がロックしていないエンティティのみにシグナルを送信できます。

## <a name="alternate-storage-providers"></a>代替ストレージ プロバイダー

現在、Durable Task Framework では、[Azure Storage](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage)、[Azure Service Bus](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus)、[インメモリ エミュレーター](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator)、実験的な [Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis) プロバイダーなど、複数のストレージ プロバイダーがサポートされています。 ただし、これまで、Azure Functions の Durable Task 拡張機能では Azure Storage プロバイダーのみがサポートされていました。 Durable Functions 2.0 以降では、Redis プロバイダーから始めて、代替ストレージ プロバイダーのサポートが追加されています。

> [!NOTE]
> Durable Functions 2.0 では、.NET Standard 2.0 と互換性のあるプロバイダーのみがサポートされます。 これを書いている時点で、Azure Service Bus プロバイダーは .NET Standard 2.0 をサポートしていないため、代替ストレージ プロバイダーとして使用できません。

### <a name="emulator"></a>エミュレーター

[DurableTask.Emulator](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/) プロバイダーは、ローカル テスト シナリオに適している、ローカル メモリの非持続ストレージ プロバイダーです。 次の最小限の **host.json** スキーマを使用して構成できます。

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "emulator": { }
      }
    }
  }
}
```

### <a name="redis-experimental"></a>Redis (実験段階)

[DurableTask.Redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/) プロバイダーでは、すべてのオーケストレーション状態が構成されている Redis クラスターに保持されます。

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "redis": {
          "connectionStringName": <string>,
        }
      }
    }
  }
}
```

`connectionStringName` では、アプリ設定または環境変数の名前を参照する必要があります。 そのアプリ設定または環境変数には、Redis 接続文字列の値が "*サーバー:ポート*" の形式で含まれる必要があります。 たとえば、ローカル Redis クラスターに接続する場合は `localhost:6379` です。

> [!NOTE]
> 現在、Redis プロバイダーは実験段階であり、1 つのノードで実行されている関数アプリのみをサポートします。
