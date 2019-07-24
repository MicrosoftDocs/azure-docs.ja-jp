---
title: Azure Cosmos DB のコンテナーを照会する
description: Azure Cosmos DB のコンテナーを照会する方法について説明します
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 2ea228a1db204170f947b5fe71f1865a4620b0f4
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57549036"
---
# <a name="query-an-azure-cosmos-container"></a>Azure Cosmos コンテナーを照会する

この記事では、Azure Cosmos DB のコンテナー (コレクション、グラフ、またはテーブル) のクエリを実行する方法について説明します。

## <a name="in-partition-query"></a>インパーティション クエリ

コンテナーのデータのクエリを実行するとき、クエリでパーティション キー フィルターを指定すると、Azure Cosmos DB ではクエリが自動的に処理されます。 クエリは、フィルターで指定されているパーティション キーの値に対応するパーティションにルーティングされます。 たとえば、次のクエリは、パーティション キー値 `XMS-0001` に対応するすべてのドキュメントが保持されている `DeviceId` パーティションにルーティングされます。

```csharp
// Query using partition key into a class called, DeviceReading
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```

## <a name="cross-partition-query"></a>クロスパーティション クエリ

次のクエリにはパーティション キー (`DeviceId`) にフィルターがないので、クエリはすべてのパーティションにファンアウトされて、パーティションのインデックスに対して実行されます。 複数のパーティションでクエリを実行するには、`EnableCrossPartitionQuery` を true に設定します (または、REST API では `x-ms-documentdb-query-enablecrosspartition`)。

EnableCrossPartitionQuery プロパティは、ブール値を受け取ります。 true に設定すると、クエリにパーティション キーが存在しない場合でも、Azure Cosmos DB によってパーティション全体にクエリが展開されます。 この展開動作は、個々のクエリをすべてのパーティションに発行することによって行われます。 クエリの結果を読み取るためには、クライアント アプリケーションが FeedResponse から結果を取り込んで ContinuationToken プロパティを調べる必要があります。 すべての結果を読み取るには、ContinuationToken が null になるまでデータを反復処理してください。 

```csharp
// Query across partition keys into a class called, DeviceReading
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Azure Cosmos DB では、SQL を使用することで、コンテナーに対する集計関数 (COUNT、MIN、MAX、AVG) がサポートされています。 コンテナーに対する集計関数は、SDK バージョン 1.12.0 以降でサポートされます。 クエリには、1 つの集計演算子と、プロジェクション内の 1 つの値を含める必要があります。

## <a name="parallel-cross-partition-query"></a>並列クロスパーティション クエリ

Azure Cosmos DB SDK 1.9.0 以降では、並列クエリ実行オプションがサポートされています。 並列パーティション間クエリを使用すれば、複数のパーティションにまたがるクエリでも、少ない待ち時間で実行できます。 たとえば、次のクエリはパーティション全体で並列に実行されるように構成されています。

```csharp
// Cross-partition Order By Query with parallel execution
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),  
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```

次のパラメーターを調整することで、並列クエリの実行を管理できます。

- **MaxDegreeOfParallelism**:コンテナーのパーティションに対する同時ネットワーク接続数の上限を設定します。 このプロパティを -1 に設定した場合、SDK によって並列処理次数が管理されます。  `MaxDegreeOfParallelism`  を指定しないか、または 0 (既定値) に設定すると、コンテナーのパーティションへのネットワーク接続は 1 つのみになります。

- **MaxBufferedItemCount**:クエリの待ち時間とクライアント側のメモリ使用率のバランスを調整します。 このオプションを省略するか -1 に設定した場合、並列クエリの実行中にバッファリングされる項目の数は SDK によって管理されます。

コレクションが同じ状態の場合、並列クエリでは順次実行と同じ順序で結果が返されます。 並べ替え演算子 (ORDER BY、TOP) を含むクロスパーティション クエリを実行すると、Azure Cosmos DB SDK から複数のパーティションに並列にクエリが発行されます。 部分的に並べ替えられた結果がクライアント側でマージされて、グローバルに並べ替えられた結果が作成されます。

## <a name="next-steps"></a>次の手順

Azure Cosmos DB でのパーティション分割については、次の記事を参照してください。

- [Azure Cosmos DB でのパーティション分割](partitioning-overview.md)
- [Azure Cosmos DB での合成パーティション キー](synthetic-partition-keys.md)
