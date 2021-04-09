---
title: Azure Cosmos DB の Table API クエリの要求ユニット (RU) 使用量の確認
description: Azure Cosmos コンテナーに対して実行した Table API クエリの要求ユニット (RU) 使用量を確認する方法について説明します。 Azure portal、.NET、Java、Python、Node.js の各言語を使用して、RU 使用量を調べることができます。
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 0c3f2495630358f48900a9cf27cbc0feb4192e1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201307"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-table-api"></a>Azure Cosmos DB Table API で実行された操作の要求ユニット使用量の確認
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Azure Cosmos DB では、多くの API (SQL、MongoDB、Cassandra、Gremlin、Table など) がサポートされています。 各 API には、固有のデータベース操作のセットがあります。 これらの操作の範囲は、単純なポイント読み取り/書き込みから、複雑なクエリにまで及びます。 各データベース操作は、それらの操作の複雑さに基づいて、システム リソースを消費します。

すべてのデータベース操作のコストは Azure Cosmos DB によって正規化され、要求ユニット (RU) によって表されます。 要求の料金は、すべてのデータベース操作で使用される要求ユニットです。 RU は、Azure Cosmos DB によってサポートされるデータベース操作を実行するために必要な CPU、IOPS、メモリなどのシステム リソースを抽象化する、パフォーマンスの通貨と考えることができます。 Azure Cosmos コンテナーの操作にどの API 使用するかに関係なく、コストは RU によって測定されます。 データベース操作が書き込み、ポイント読み取り、またはクエリのいずれの場合でも、コストは常に RU で測定されます。 詳細については、[要求ユニットとその考慮事項](request-units.md)に関する記事を参照してください。

この記事では、Azure Cosmos DB Table API のコンテナーに対して実行された任意の操作の[要求ユニット](request-units.md) (RU) 消費量を確認するさまざまな方法を紹介します。 別の API を使用している場合は、RU の使用量を確認する方法について、[MongoDB の API](find-request-unit-charge-mongodb.md)、[Cassandra API](find-request-unit-charge-cassandra.md)、[Gremlin API](find-request-unit-charge-gremlin.md)、[SQL API](find-request-unit-charge.md) の記事を参照してください。

## <a name="use-the-net-sdk"></a>.NET SDK を使用する

現在のところ、テーブル操作に対する RU 使用量を返す唯一の SDK が [.NET Standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) です。 `TableResult` オブジェクトは、`RequestCharge` プロパティを公開します。このオブジェクトを Azure Cosmos DB の Table API に対して使用すると、この SDK によってプロパティの値が設定されます。

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

詳細については、「[クイック スタート: .NET SDK と Azure Cosmos DB を使用して Table API アプリをビルドする](create-table-dotnet.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

RU 使用量を最適化する方法については、次の記事をご覧ください。

* [Azure Cosmos DB における要求ユニットとスループット](request-units.md)
* [Azure Cosmos DB でのプロビジョニングされたスループット コストを最適化する](optimize-cost-throughput.md)
* [Azure Cosmos DB でのクエリ コストを最適化する](./optimize-cost-reads-writes.md)