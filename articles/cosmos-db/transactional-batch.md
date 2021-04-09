---
title: .NET SDK を使用した Azure Cosmos DB でのトランザクション バッチ操作
description: Azure Cosmos DB .NET SDK で TransactionalBatch を使用して、成功または失敗のいずれかになるポイント操作のグループを実行する方法について説明します。
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 9f6692db2da3722507136a468d1dcbdc2985e73f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97347559"
---
# <a name="transactional-batch-operations-in-azure-cosmos-db-using-the-net-sdk"></a>.NET SDK を使用した Azure Cosmos DB でのトランザクション バッチ操作
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

トランザクション バッチでは、コンテナー内の同じパーティション キーと共に成功または失敗のいずれかになる必要があるポイント操作のグループを記述します。 .NET SDK では、この操作のバッチを定義するために `TransactionalBatch` クラスを使用します。 すべての操作がトランザクション バッチ操作内で記述されている順序で成功した場合、トランザクションはコミットされます。 ただし、いずれかの操作が失敗した場合、トランザクション全体がロールバックされます。

## <a name="whats-a-transaction-in-azure-cosmos-db"></a>Azure Cosmos DB のトランザクションとは

一般的なデータベースにおけるトランザクションは、作業の単一の論理ユニットとして実行される一連の操作として定義されます。 各トランザクションによって、ACID (原子性、一貫性、分離性、持続性) プロパティの保証が提供されます。

* **原子性** は、トランザクション内で実行されるすべての操作が単一の単位として扱われることを保証します。その結果は、そのすべてがコミットされるか、まったくコミットされないかのどちらかになります。
* **一貫性** は、トランザクションにまたがってデータが常に有効な状態にあることを保証します。
* **分離性** は、2 つのトランザクションが互いに干渉しないことを保証します。多くの商用システムは、アプリケーション ニーズに基づいて使用できる複数の分離性レベルを提供します。
* **持続性** は、データベース内でコミットされたすべての変更が常に保持されることを保証します。
Azure Cosmos DB では、同じ[論理パーティション キー](partitioning-overview.md)内の操作に対して、[スナップショット分離が有効な ACID に完全に準拠するトランザクション](database-transactions-optimistic-concurrency.md)をサポートします。

## <a name="transactional-batch-operations-vs-stored-procedures"></a>トランザクション バッチ操作とストアド プロシージャの比較

Azure Cosmos DB では現在、ストアド プロシージャをサポートしています。これにより、操作のトランザクション スコープも提供されます。 ただし、トランザクション バッチ操作には次のような利点があります。

* **言語オプション** - トランザクション バッチは、既に使用している SDK と言語でサポートされていますが、ストアド プロシージャは JavaScript で記述される必要があります。
* **コードのバージョン管理** - アプリケーション コードのバージョン管理と CI/CD パイプラインにオンボードすることは、ストアド プロシージャの更新を調整し、適切なタイミングでロールオーバーが発生するように設定することよりもはるかに自然です。 また、変更のロールバックも容易になります。
* **パフォーマンス** - ストアド プロシージャの実行と比較すると、同等の操作における待機時間が最大で 30% 短縮されました。
* **コンテンツのシリアル化** - トランザクション バッチ内の各操作で、そのペイロードのカスタム シリアル化オプションを利用できます。

## <a name="how-to-create-a-transactional-batch-operation"></a>トランザクション バッチ操作を作成する方法

トランザクション バッチ操作を作成するときは、コンテナー インスタンスから開始し、`CreateTransactionalBatch` を呼び出します。

```csharp
string partitionKey = "The Family";
ParentClass parent = new ParentClass(){ Id = "The Parent", PartitionKey = partitionKey, Name = "John", Age = 30 };
ChildClass child = new ChildClass(){ Id = "The Child", ParentId = parent.Id, PartitionKey = partitionKey };
TransactionalBatch batch = container.CreateTransactionalBatch(new PartitionKey(parent.PartitionKey)) 
  .CreateItem<ParentClass>(parent)
  .CreateItem<ChildClass>(child);
```

次に、バッチで `ExecuteAsync` を呼び出す必要があります。

```csharp
TransactionalBatchResponse batchResponse = await batch.ExecuteAsync();
```

応答が受信されたら、成功したかどうかを確認し、その結果を抽出する必要があります。

```csharp
using (batchResponse)
{
  if (batchResponse.IsSuccessStatusCode)
  {
    TransactionalBatchOperationResult<ParentClass> parentResult = batchResponse.GetOperationResultAtIndex<ParentClass>(0);
    ParentClass parentClassResult = parentResult.Resource;
    TransactionalBatchOperationResult<ChildClass> childResult = batchResponse.GetOperationResultAtIndex<ChildClass>(1);
    ChildClass childClassResult = childResult.Resource;
  }
}
```

エラーが発生した場合は、失敗した操作に対応するエラーの状態コードが表示されます。 その他すべての操作では 424 状態コード (依存関係の失敗) が表示されます。 次の例では、既に存在する項目の作成を試行しているため、操作が失敗します (409 HttpStatusCode.Conflict)。 状態コードにより、トランザクション エラーの原因を特定できます。

```csharp
// Parent's birthday!
parent.Age = 31;
// Naming two children with the same name, should abort the transaction
ChildClass anotherChild = new ChildClass(){ Id = "The Child", ParentId = parent.Id, PartitionKey = partitionKey };
TransactionalBatchResponse failedBatchResponse = await container.CreateTransactionalBatch(new PartitionKey(partitionKey))
  .ReplaceItem<ParentClass>(parent.Id, parent)
  .CreateItem<ChildClass>(anotherChild)
  .ExecuteAsync();

using (failedBatchResponse)
{
  if (!failedBatchResponse.IsSuccessStatusCode)
  {
    TransactionalBatchOperationResult<ParentClass> parentResult = failedBatchResponse.GetOperationResultAtIndex<ParentClass>(0);
    // parentResult.StatusCode is 424
    TransactionalBatchOperationResult<ChildClass> childResult = failedBatchResponse.GetOperationResultAtIndex<ChildClass>(1);
    // childResult.StatusCode is 409
  }
}
```

## <a name="how-are-transactional-batch-operations-executed"></a>トランザクション バッチ操作が実行される方法

`ExecuteAsync` メソッドが呼び出されると、`TransactionalBatch` オブジェクト内のすべての操作がグループ化されて 1 つのペイロードにシリアル化され、Azure Cosmos DB サービスに 1 つの要求として送信されます。

サービスによって、要求が受信され、トランザクション スコープ内のすべての操作が実行されて、同じシリアル化プロトコルを使用して応答が返されます。 この応答は成功または失敗のいずれかで、操作ごとの個々の操作の応答を提供します。

SDK によって、結果を確認するための応答が公開され、必要に応じて、内部操作の各結果が抽出されます。

## <a name="limitations"></a>制限事項

現在、2 つの既知の制限があります。

* Azure Cosmos DB の要求サイズ制限によって、`TransactionalBatch` ペイロードのサイズが 2 MB を超えないよう制限されます。最大実行時間は 5 秒です。
* パフォーマンスが期待どおりかつ SLA 内であるように、現在は `TransactionalBatch` ごとに 100 回の操作に制限されています。

## <a name="next-steps"></a>次のステップ

* [TransactionalBatch でできること](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/TransactionalBatch)について、さらに学習する

* Cosmos DB .NET SDK を使用するその他の方法について、[サンプル](sql-api-dotnet-v3sdk-samples.md)を確認する
