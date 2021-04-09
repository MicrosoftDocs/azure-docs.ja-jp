---
title: 大きいパーティション キーを使用して Azure Cosmos コンテナーを作成する
description: Azure portal と各種の SDK を使って大きいパーティション キーを持つコンテナーを Azure Cosmos DB で作成する方法について説明します。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/28/2019
ms.author: mjbrown
ms.custom: devx-track-csharp
ms.openlocfilehash: 4ad26d63ca06f5a46a4a1f77d329d04896e96c52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339299"
---
# <a name="create-containers-with-large-partition-key"></a>大きいパーティション キーを持つコンテナーを作成する
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB では、データの水平方向のスケーリングを実現するために、ハッシュ ベースのパーティション構成が使われます。 2019 年 5 月 3 日より前に作成されたすべての Azure Cosmos コンテナーでは、パーティション キーの最初の 100 バイトに基づいてハッシュを計算するハッシュ関数が使われています。 複数のパーティション キーで最初の 100 バイトが同じ場合、それらの論理パーティションはサービスによって同じ論理パーティションと見なされます。 これにより、パーティション サイズ クォータが正しくなくなったり、一意のインデックスが複数のパーティション キーに適用されるといった問題が発生する可能性があります。 この問題を解決するため、大きいパーティション キーが導入されました。 Azure Cosmos DB では、値が最大 2 KB の大きいパーティション キーがサポートされるようになっています。

大きいパーティション キーは、ハッシュ関数の拡張バージョンの機能を使ってサポートされ、最大 2 KB の大きいパーティション キーから一意のハッシュを生成できます。 このハッシュ バージョンは、パーティション キーのサイズに関係なく、パーティション キーのカーディナリティが高いシナリオにも推奨されます。 パーティション キーのカーディナリティは、一意の論理パーティションの数として定義されます (たとえば、コンテナー内の論理パーティションの数が約 30000)。 この記事では、Azure portal とさまざまな SDK を使って、大きいパーティション キーを持つコンテナーを作成する方法について説明します。

## <a name="create-a-large-partition-key-azure-portal"></a>大きいパーティション キーを作成する (Azure portal)

Azure portal を使って新しいコンテナーを作成するときに、大きいパーティション キーを作成するには、 **[My partition key is larger than 100-bytes]\(100 バイトを超えるパーティション キー\)** オプションをオンにします。 大きいパーティション キーを使う必要がない場合、または 1.18 より前の SDK バージョンでアプリケーションを実行する場合は、チェック ボックスをオフにします。

:::image type="content" source="./media/large-partition-keys/large-partition-key-with-portal.png" alt-text="Azure portal を使って大きいパーティション キーを作成する":::

## <a name="create-a-large-partition-key-powershell"></a>大きいパーティション キーを作成する (PowerShell)

大きいパーティション キーのサポートを使用してコンテナーを作成するには、次を参照してください。

* [大きいパーティション キー サイズで Azure Cosmos コンテナーを作成する](manage-with-powershell.md#create-container-big-pk)

## <a name="create-a-large-partition-key-net-sdk"></a>大きいパーティション キーを作成する (.Net SDK)

.NET SDK を使って大きいパーティション キーを持つコンテナーを作成するには、`PartitionKeyDefinitionVersion.V2` プロパティを指定します。 次の例では、PartitionKeyDefinition オブジェクト内で Version プロパティを指定し、それを PartitionKeyDefinitionVersion.V2 に設定する方法を示します。

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
await database.CreateContainerAsync(
    new ContainerProperties(collectionName, $"/longpartitionkey")
    {
        PartitionKeyDefinitionVersion = PartitionKeyDefinitionVersion.V2,
    })
```

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
DocumentCollection collection = await newClient.CreateDocumentCollectionAsync(
database,
     new DocumentCollection
        {
           Id = Guid.NewGuid().ToString(),
           PartitionKey = new PartitionKeyDefinition
           {
             Paths = new Collection<string> {"/longpartitionkey" },
             Version = PartitionKeyDefinitionVersion.V2
           }
         },
      new RequestOptions { OfferThroughput = 400 });
```
---

## <a name="supported-sdk-versions"></a>サポートされている SDK バージョン

大きいパーティション キーは、次の最小バージョンの SDK でサポートされています。

|SDK の種類  | 最小バージョン   |
|---------|---------|
|.NET     |    1.18     |
|Java Sync     |   2.4.0      |
|Java Async   |  2.5.0        |
| REST API | `x-ms-version` 要求ヘッダーを使用する `2017-05-03` より後のバージョン。|
| Resource Manager テンプレート | バージョン 2 (`partitionKey` オブジェクト内の `"version":2` プロパティを使用)。 |

現在、Power BI と Azure Logic Apps 内に大きなパーティション キーがあるコンテナーは使用できません。 これらのアプリケーションから、大きなパーティション キーなしでコンテナーを使用できます。

## <a name="next-steps"></a>次のステップ

* [Azure Cosmos DB でのパーティション分割](partitioning-overview.md)
* [Azure Cosmos DB の要求ユニット](request-units.md)
* [コンテナーとデータベースのスループットのプロビジョニング](set-throughput.md)
* [Azure Cosmos アカウントの使用](./account-databases-containers-items.md)