---
title: Azure Cosmos DB での改ページ
description: ページングの概念と継続トークンについて説明します
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/15/2021
ms.openlocfilehash: d47a7b9e2229ffbd747b1ff7b9491ce99e9b190f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103492236"
---
# <a name="pagination-in-azure-cosmos-db"></a>Azure Cosmos DB での改ページ
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB では、クエリ結果が複数のページにわたる場合があります。 このドキュメントでは、Azure Cosmos DB のクエリ エンジンがクエリ結果を複数のページに分割するかどうかを決定するために使用する条件について説明します。 複数ページにまたがるクエリ結果を管理するために、オプションで継続トークンを使用できます。

## <a name="understanding-query-executions"></a>クエリ実行について

クエリ結果が複数のページに分割される場合があります。 各ページの結果は、別個のクエリ実行によって生成されます。 クエリ結果を 1 回の実行で返すことができない場合、Azure Cosmos DB では自動的に結果が複数のページに分割されます。

`MaxItemCount` を設定すると、1 回のクエリで返される項目の最大数を指定できます。 要求ごとに `MaxItemCount` を指定して、その数以下の項目を返すようにクエリ エンジンに指示します。 クエリ実行ごとの結果の数を制限しない場合は、`MaxItemCount` を `-1` に設定できます。

また、他の理由でクエリ エンジンによってクエリ結果を複数ページに分割することが必要になる場合もあります。 これには以下が含まれます。

- コンテナーが調整されていて、さらに多くのクエリ結果を返すために使用できる RU がなかった。
- クエリ実行の応答が大きすぎた。
- クエリ実行の時間が長すぎた。
- 追加の実行によってクエリ エンジンから結果を返す方が効率的だった。

クエリ実行ごとに返される項目の数は、常に `MaxItemCount` 以下になります。 ただし、クエリで返すことのできる結果の数が他の条件によって制限されている場合があります。 同じクエリを複数回実行した場合、ページ数が一定になるとは限りません。 たとえば、クエリが調整されるとページごとに返せる結果の数が減る可能性があります。この場合、クエリのページの数が増えます。 時には、クエリから返される結果のページが空の場合もあります。

## <a name="handling-multiple-pages-of-results"></a>複数にわたる結果のページの処理

正確なクエリ結果を確実に得るためには、すべてのページを処理する必要があります。 追加のページが存在しなくなるまで、クエリの実行を続行する必要があります。

次に、複数ページにわたるクエリの結果を処理する例を示します。

- [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L280)
- [Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L162-L176)
- [Node.js SDK](https://github.com/Azure/azure-sdk-for-js/blob/83fcc44a23ad771128d6e0f49043656b3d1df990/sdk/cosmosdb/cosmos/samples/IndexManagement.ts#L128-L140)
- [Python SDK](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/examples.py#L89)

## <a name="continuation-tokens"></a>継続トークン

.NET SDK および Java SDK では、クエリの進行状況のブックマークとして、継続トークンを必要に応じて使用できます。 Azure Cosmos DB のクエリ実行は、サーバー側ではステートレスであり、継続トークンを利用していつでも再開できます。 継続トークンは、Node.js SDK ではサポートされていません。 Python SDK では、単一パーティション クエリでサポートされています。PK は、クエリ自体に含めるには十分でないため、options オブジェクトで指定する必要があります。

継続トークンを使用する例を次に示します。

- [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/Queries/Program.cs#L699-L734)
- [Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L216)
- [Python SDK](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/test/test_query.py#L533)

クエリから継続トークンが返される場合、追加のクエリ結果が存在します。

Azure Cosmos DB の REST API では、`x-ms-continuation` ヘッダーを使用して継続トークンを管理できます。 .NET や Java SDK でクエリを実行する場合と同様に、`x-ms-continuation` 応答ヘッダーが空でない場合は、クエリに追加の結果があることを意味します。

同じ SDK バージョンを使用している限り、継続トークンは期限切れになりません。 必要に応じて、[継続トークンのサイズを制限する](/dotnet/api/microsoft.azure.documents.client.feedoptions.responsecontinuationtokenlimitinkb#Microsoft_Azure_Documents_Client_FeedOptions_ResponseContinuationTokenLimitInKb)ことができます。 コンテナー内のデータ量や物理パーティション数に関係なく、クエリから返される継続トークンは 1 つです。

[GROUP BY](sql-query-group-by.md) や [DISTINCT](sql-query-keywords.md#distinct) を含むクエリに対しては、継続トークンを使用できません。これは、これらのクエリでは、大量の状態を格納する必要があるためです。 `DISTINCT` を含むクエリに対しては、`ORDER BY` をクエリに追加すると、継続トークンを使用できます。

`DISTINCT` を含み、継続トークンが使用できるクエリの例を次に示します。

```sql
SELECT DISTINCT VALUE c.name
FROM c
ORDER BY c.name
```

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の概要](introduction.md)
- [Azure Cosmos DB .NET のサンプル](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [ORDER BY 句](sql-query-order-by.md)
