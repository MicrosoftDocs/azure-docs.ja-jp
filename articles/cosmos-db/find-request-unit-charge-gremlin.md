---
title: Azure Cosmos DB の Gremlin API クエリの要求ユニット (RU) 使用量を確認する
description: Azure Cosmos コンテナーに対して実行した Gremlin クエリの要求ユニット (RU) 使用量を確認する方法について説明します。 Azure portal、.NET、Java ドライバーを使用して、RU 使用量を確認できます。
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 295ed3eb2312a5f614a4214b5b627f5657fba1eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201339"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-gremlin-api"></a>Azure Cosmos DB Gremlin API で実行された操作の要求ユニット使用量を確認する
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Azure Cosmos DB では、多くの API (SQL、MongoDB、Cassandra、Gremlin、Table など) がサポートされています。 各 API には、固有のデータベース操作のセットがあります。 これらの操作の範囲は、単純なポイント読み取り/書き込みから、複雑なクエリにまで及びます。 各データベース操作は、それらの操作の複雑さに基づいて、システム リソースを消費します。

すべてのデータベース操作のコストは Azure Cosmos DB によって正規化され、要求ユニット (RU) によって表されます。 要求の料金は、すべてのデータベース操作で使用される要求ユニットです。 RU は、Azure Cosmos DB によってサポートされるデータベース操作を実行するために必要な CPU、IOPS、メモリなどのシステム リソースを抽象化する、パフォーマンスの通貨と考えることができます。 Azure Cosmos コンテナーの操作にどの API 使用するかに関係なく、コストは RU によって測定されます。 データベース操作が書き込み、ポイント読み取り、またはクエリのいずれの場合でも、コストは常に RU で測定されます。 詳細については、[要求ユニットとその考慮事項](request-units.md)に関する記事を参照してください。

この記事では、Azure Cosmos DB Gremlin API でコンテナーに対して実行された任意の操作の[要求ユニット](request-units.md) (RU) 消費量を確認できるさまざまな方法を紹介します。 異なる API を使用している場合は、[MongoDB 用 API](find-request-unit-charge-mongodb.md)、[Cassandra API](find-request-unit-charge-cassandra.md)、[SQL API](find-request-unit-charge.md)、[Table API](find-request-unit-charge-table.md) に関する記事を参照して RU 消費量を確認してください。

Gremlin API から返されるヘッダーは、Gremlin .NET と Java SDK で現在公開されているカスタムの状態属性にマップされます。 要求の使用量は、`x-ms-request-charge` キーで得られます。 Gremlin API を使用するとき、Azure Cosmos コンテナーに対する操作の RU 消費量をいくつかの方法で確認できます。

## <a name="use-the-azure-portal"></a>Azure ポータルの使用

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos アカウントを作成](create-graph-gremlin-console.md#create-a-database-account)してデータを取り込むか、既にデータが存在する既存のアカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウに進み、操作の対象となるコンテナーを選択します。

1. 有効なクエリを入力してから、 **[Gremlin クエリの実行]** を選択します。

1. **[Query Stats]\(クエリの統計\)** を選択すると、実行した要求の実際の使用量が表示されます。

:::image type="content" source="./media/find-request-unit-charge/portal-gremlin-query.png" alt-text="Azure portal で Gremlin クエリ要求の使用量を取得するスクリーンショット":::

## <a name="use-the-net-sdk-driver"></a>.NET SDK ドライバーを使用する

[Gremlin.NET SDK](https://www.nuget.org/packages/Gremlin.Net/) を使用するとき、`ResultSet<>` オブジェクトの `StatusAttributes` プロパティで状態属性が得られます。

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

詳細については、「[クイック スタート: Azure Cosmos DB Gremlin API アカウントを使用して .NET Framework アプリケーションまたは Core アプリケーションを構築する](create-graph-dotnet.md)」を参照してください。

## <a name="use-the-java-sdk-driver"></a>Java SDK ドライバーを使用する

[Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver) を使用するとき、`ResultSet` オブジェクトで `statusAttributes()` メソッドを呼び出すことで状態属性を取得できます。

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

詳細については、「[クイック スタート: Azure Cosmos DB で Java SDK を使用してグラフ データベースを作成する](create-graph-java.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

RU 使用量を最適化する方法については、次の記事をご覧ください。

* [Azure Cosmos DB における要求ユニットとスループット](request-units.md)
* [Azure Cosmos DB でのプロビジョニングされたスループット コストを最適化する](optimize-cost-throughput.md)
* [Azure Cosmos DB でのクエリ コストを最適化する](./optimize-cost-reads-writes.md)