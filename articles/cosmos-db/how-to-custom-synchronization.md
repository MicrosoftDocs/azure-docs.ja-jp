---
title: Azure Cosmos DB でカスタム同期を実装して可用性とパフォーマンスの向上のために最適化する方法
description: Azure Cosmos DB でカスタム同期を実装して可用性とパフォーマンスの向上のために最適化する方法について説明します
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 2/12/2019
ms.author: mjbrown
ms.openlocfilehash: 43cb73784806358bccb9758be2923d3df5e9badd
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2019
ms.locfileid: "56414883"
---
# <a name="how-to-implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>カスタム同期を実装して可用性とパフォーマンスの向上のために最適化する方法

Azure Cosmos DB には 5 つの明確に定義された一貫性レベルが用意されており、一貫性、パフォーマンス、および可用性のトレードオフのバランスをとるために選択できます。 一貫性を厳密にすることで、Azure Cosmos アカウントを利用できるすべてのリージョンでデータが同期的にレプリケートされ、永続的に維持されます。 この構成では、最高レベルの持続性を達成できますが、パフォーマンスと可用性は犠牲になります。 可用性を犠牲にせずにアプリケーションのニーズに合わせてデータの持続性を制御/緩和する場合は、アプリケーション レイヤーでカスタム同期を採用して、必要なレベルの持続性を実現できます。

以下の図は、カスタム同期モデルを視覚的に示したものです。

![カスタム同期](./media/how-to-custom-synchronization/custom-synchronization.png)

このシナリオでは、Azure Cosmos コンテナーは複数の大陸と複数のリージョンにまたがってグローバルにレプリケートされます。 このシナリオですべてのリージョンに厳密な一貫性を使用すると、パフォーマンスに影響があります。 書き込みの待機時間を犠牲にせずにより高いレベルのデータ持続性を確保するには、アプリケーションで同じセッション トークンを共有する 2 つのクライアントを使用します。

1 つ目のクライアントは、ローカル リージョン (たとえば米国西部) にデータを書き込むことができます。 2 つ目のクライアント (たとえば米国東部) は、同期を確実にするために使用される読み取りクライアントです。 書き込み応答から次の読み取りにセッション トークンを流すことで、読み取りによって米国東部への書き込みの同期が確実に実行されるようになります。 Azure Cosmos DB では、元の書き込みリージョンが停止した場合でも、書き込みが少なくとも 1 つのリージョンで認識され、リージョンの停止に耐えることが保証されます。 このシナリオでは、すべての書き込みが米国東部と同期されているため、すべてのリージョンに厳密な一貫性を採用するための待機時間が短縮されます。 書き込みがすべてのリージョンで発生するマルチマスター シナリオでは、このモデルを拡張して複数のリージョンに並列で同期することができます。

## <a name="configure-the-clients"></a>クライアントを構成する

以下のサンプルは、この目的のために 2 つのクライアントをインスタンス化するデータ アクセス層を示します。

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

## <a name="implement-custom-synchronization"></a>カスタム同期を実装する

クライアントが初期化されると、次のようにアプリケーションでローカル リージョン (米国西部) への書き込みを実行し、米国東部への書き込みを強制的に同期できます。

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

このモデルは、複数のリージョンに並列で同期するように拡張できます。

## <a name="next-steps"></a>次の手順

Azure Cosmos DB のグローバル配布と一貫性の詳細については、以下の記事を参照してください。

* [Azure Cosmos DB での適切な一貫性レベルの選択](consistency-levels-choosing.md)

* [Azure Cosmos DB の一貫性、可用性、パフォーマンスのトレードオフ](consistency-levels-tradeoffs.md)

* [Azure Cosmos DB で一貫性を管理する方法](how-to-manage-consistency.md)

* [Azure Cosmos DB でのパーティション分割とデータ配布](partition-data.md)
