---
title: Azure Cosmos DB の Cassandra API クエリの要求ユニット (RU) 使用量を確認する
description: Azure Cosmos コンテナーに対して実行した Cassandra クエリの要求ユニット (RU) 使用量を確認する方法について説明します。 Azure portal、.NET、および Java ドライバーを使用して、RU 使用率を調べることができます。
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: b504702ad0c74ae9728c0a8b34fa94df26184f70
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200504"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API で実行された操作の要求ユニット使用量の確認
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB では、多くの API (SQL、MongoDB、Cassandra、Gremlin、Table など) がサポートされています。 各 API には、固有のデータベース操作のセットがあります。 これらの操作の範囲は、単純なポイント読み取り/書き込みから、複雑なクエリにまで及びます。 各データベース操作は、それらの操作の複雑さに基づいて、システム リソースを消費します。

すべてのデータベース操作のコストは Azure Cosmos DB によって正規化され、要求ユニット (RU) によって表されます。 要求の料金は、すべてのデータベース操作で使用される要求ユニットです。 RU は、Azure Cosmos DB によってサポートされるデータベース操作を実行するために必要な CPU、IOPS、メモリなどのシステム リソースを抽象化する、パフォーマンスの通貨と考えることができます。 Azure Cosmos コンテナーの操作にどの API 使用するかに関係なく、コストは RU によって測定されます。 データベース操作が書き込み、ポイント読み取り、またはクエリのいずれの場合でも、コストは常に RU で測定されます。 詳細については、[要求ユニットとその考慮事項](request-units.md)に関する記事を参照してください。

この記事では、Azure Cosmos DB Cassandra API のコンテナーに対して実行された任意の操作の[要求ユニット](request-units.md) (RU) 消費量を確認するさまざまな方法を紹介します。 別の API を使用している場合は、RU の使用量を確認する方法について、[MongoDB 用 API](find-request-unit-charge-mongodb.md)、[SQL API](find-request-unit-charge.md)、[Gremlin API](find-request-unit-charge-gremlin.md)、[Table API](find-request-unit-charge-table.md) の記事を参照してください。

Azure Cosmos DB の Cassandra API に対して操作を実行すると、受信ペイロードの `RequestCharge` という名前のフィールドとして RU 使用量が返されます。 RU 使用量はいくつかの方法で取得できます。

## <a name="use-the-net-sdk"></a>.NET SDK を使用する

[.NET SDK](https://www.nuget.org/packages/CassandraCSharpDriver/) を使用するとき、`RowSet` オブジェクトの `Info` プロパティの下で、入ってくるペイロードを取得できます。

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

詳細については、「[クイック スタート: .NET SDK と Azure Cosmos DB を使用して Cassandra アプリを構築する](create-cassandra-dotnet.md)」を参照してください。

## <a name="use-the-java-sdk"></a>Java SDK の使用

[Java SDK](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core) を使用するとき、`ResultSet` オブジェクトで `getExecutionInfo()` メソッドを呼び出すことで、入ってくるペイロードを取得できます。

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

詳細については、「[クイック スタート: Java SDK と Azure Cosmos DB を使用して Cassandra アプリをビルドする](create-cassandra-java.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

RU 使用量を最適化する方法については、次の記事をご覧ください。

* [Azure Cosmos DB における要求ユニットとスループット](request-units.md)
* [Azure Cosmos DB でのプロビジョニングされたスループット コストを最適化する](optimize-cost-throughput.md)
* [Azure Cosmos DB でのクエリ コストを最適化する](./optimize-cost-reads-writes.md)