---
title: Azure Cosmos DB でカスタム同期を実装して可用性とパフォーマンスの向上のために最適化する方法
description: Azure Cosmos DB でカスタム同期を実装して可用性とパフォーマンスの向上のために最適化する方法について説明します。
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: rimman
ms.openlocfilehash: de66149a2ea3e01e62aa8e33ea5a99121a21524f
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67986080"
---
# <a name="implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>カスタム同期を実装して可用性とパフォーマンスの向上のために最適化する

Azure Cosmos DB には [5 つの明確に定義された整合性レベル](consistency-levels.md)が用意されており、整合性、パフォーマンス、および可用性のトレードオフのバランスをとるために選択できます。 厳密な整合性では、Azure Cosmos アカウントを利用できるすべてのリージョンでデータが同期的にレプリケートされ、永続的に維持されやすくなります。 この構成では、最高レベルの持続性を達成できますが、パフォーマンスと可用性は犠牲になります。 アプリケーションで、可用性を犠牲にせずにアプリケーションのニーズに合わせてデータの持続性を制御または緩和する場合は、アプリケーション レイヤーで*カスタム同期*を採用して、必要なレベルの持続性を実現できます。

次の図は、カスタム同期モデルを視覚的に示したものです。

![カスタム同期](./media/how-to-custom-synchronization/custom-synchronization.png)

このシナリオでは、Azure Cosmos コンテナーは複数の大陸の複数のリージョンにまたがってグローバルにレプリケートされます。 このシナリオですべてのリージョンに厳密な一貫性を使用すると、パフォーマンスに影響があります。 書き込みの待ち時間を犠牲にせずにより高いレベルのデータ持続性を確保するには、アプリケーションで同じ[セッション トークン](how-to-manage-consistency.md#utilize-session-tokens)を共有する 2 つのクライアントを使用します。

1 つ目のクライアントは、ローカル リージョン (たとえば米国西部) にデータを書き込むことができます。 2 つ目のクライアント (たとえば米国東部) は、同期を確実にするために使用される読み取りクライアントです。 書き込み応答から次の読み取りにセッション トークンを流すことで、読み取りによって米国東部への書き込みの同期が確実に実行されます。 Azure Cosmos DB は、少なくとも 1 つのリージョンで書き込みが行われることを保証します。 それらの書き込みは、元の書き込みリージョンがダウンした場合でも、リージョンの停止の影響を受けないことが保証されます。 このシナリオでは、すべての書き込みが米国東部と同期されているため、すべてのリージョンに厳密な一貫性を採用するための待機時間が短縮されます。 書き込みがすべてのリージョンで発生するマルチマスター シナリオでは、このモデルを拡張して複数のリージョンに並列で同期することができます。

## <a name="configure-the-clients"></a>クライアントを構成する

次のサンプルは、カスタム同期のために 2 つのクライアントをインスタンス化するデータ アクセス レイヤーを示しています。

### <a name="net-v2-sdk"></a>.Net V2 SDK
```csharp
class MyDataAccessLayer
{
    private DocumentClient writeClient;
    private DocumentClient readClient;

    public async Task InitializeAsync(Uri accountEndpoint, string key)
    {
        ConnectionPolicy writeConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp,
            UseMultipleWriteLocations = true
        };
        writeConnectionPolicy.SetCurrentLocation(LocationNames.WestUS);

        ConnectionPolicy readConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp
        };
        readConnectionPolicy.SetCurrentLocation(LocationNames.EastUS);

        writeClient = new DocumentClient(accountEndpoint, key, writeConnectionPolicy);
        readClient = new DocumentClient(accountEndpoint, key, readConnectionPolicy, ConsistencyLevel.Session);

        await Task.WhenAll(new Task[]
        {
            writeClient.OpenAsync(),
            readClient.OpenAsync()
        });
    }
}
```

### <a name="net-v3-sdk"></a>.Net V3 SDK
```csharp
class MyDataAccessLayer
{
    private CosmosClient writeClient;
    private CosmosClient readClient;

    public void InitializeAsync(string accountEndpoint, string key)
    {
        CosmosClientOptions writeConnectionOptions = new CosmosClientOptions();
        writeConnectionOptions.ConnectionMode = ConnectionMode.Direct;
        writeConnectionOptions.ApplicationRegion = "West US";

        CosmosClientOptions readConnectionOptions = new CosmosClientOptions();
        readConnectionOptions.ConnectionMode = ConnectionMode.Direct;
        readConnectionOptions.ApplicationRegion = "East US";


        writeClient = new CosmosClient(accountEndpoint, key, writeConnectionOptions);
        writeClient = new CosmosClient(accountEndpoint, key, writeConnectionOptions);
    }
}
```

## <a name="implement-custom-synchronization"></a>カスタム同期を実装する

クライアントが初期化された後、次のようにアプリケーションでローカル リージョン (米国西部) への書き込みを実行し、米国東部への書き込みを強制的に同期します。

### <a name="net-v2-sdk"></a>.Net V2 SDK
```csharp
class MyDataAccessLayer
{
    public async Task CreateItem(string containerLink, Document document)
    {
        ResourceResponse<Document> response = await writeClient.CreateDocumentAsync(
            containerLink, document);

        await readClient.ReadDocumentAsync(response.Resource.SelfLink,
            new RequestOptions { SessionToken = response.SessionToken });
    }
}
```

### <a name="net-v3-sdk"></a>.Net V3 SDK
```csharp
class MyDataAccessLayer
{
     public async Task CreateItem(string databaseId, string containerId, dynamic item)
     {
        ItemResponse<dynamic> response = await writeClient.GetContainer("containerId", "databaseId")
            .CreateItemAsync<dynamic>(
                item,
                new PartitionKey("test"));

        await readClient.GetContainer("containerId", "databaseId").ReadItemAsync<dynamic>(
            response.Resource.id,
            new PartitionKey("test"),
            new ItemRequestOptions { SessionToken = response.Headers.Session, ConsistencyLevel = ConsistencyLevel.Session });
    }
}
```

モデルを拡張して、複数のリージョンに並列で同期することができます。

## <a name="next-steps"></a>次の手順

Azure Cosmos DB のグローバル配布と一貫性の詳細については、以下の記事を参照してください。

* [Azure Cosmos DB で適切な一貫性レベルを選択する](consistency-levels-choosing.md)
* [Azure Cosmos DB の一貫性、可用性、パフォーマンスのトレードオフ](consistency-levels-tradeoffs.md)
* [Azure Cosmos DB で一貫性を管理する](how-to-manage-consistency.md)
* [Azure Cosmos DB でのパーティション分割とデータ配布](partition-data.md)
