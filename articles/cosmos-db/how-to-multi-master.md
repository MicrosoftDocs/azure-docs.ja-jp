---
title: Azure Cosmos DB でマルチマスターを構成する方法
description: Azure Cosmos DB 内のアプリケーションでマルチマスターを構成する方法について説明します
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 2/12/2019
ms.author: mjbrown
ms.openlocfilehash: 84c8e2921602bb653c0b1ef0adffd3d89e91bd78
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312142"
---
# <a name="how-to-configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Azure Cosmos DB を使用するアプリケーションでマルチマスターを構成する方法

アプリケーションでマルチマスター機能を使用するには、マルチリージョンの書き込みを有効にし、マルチホーム機能を構成する必要があります。 マルチホーム機能は、アプリケーションがデプロイされている現在のリージョンを設定することによって構成します。

## <a id="netv2"></a>.NET SDK v2

アプリケーションでマルチマスターを有効にするには、`UseMultipleWriteLocations` を true に設定し、`SetCurrentLocation` をアプリケーションがデプロイされ、Azure Cosmos DB がレプリケートされているリージョンに構成します。

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a id="netv3"></a>.NET SDK v3 (プレビュー)

アプリケーションでマルチマスターを有効にするには、`UseCurrentRegion` をアプリケーションがデプロイされ、Cosmos DB がレプリケートされているリージョンに構成します。

```csharp
CosmosConfiguration config = new CosmosConfiguration("endpoint", "key");
config.UseCurrentRegion("West US");
CosmosClient client = new CosmosClient(config);
```

## <a id="java"></a>Java Async SDK

アプリケーションでマルチマスターを有効にするには、`policy.setUsingMultipleWriteLocations(true)` を true に設定し、`policy.setPreferredLocations` をアプリケーションがデプロイされ、Cosmos DB がレプリケートされているリージョンに構成します。

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

## <a id="javascript"></a>Node.js、JavaScript、TypeScript SDK

アプリケーションでマルチマスターを有効にするには、`connectionPolicy.UseMultipleWriteLocations` を true に設定し、`connectionPolicy.PreferredLocations` をアプリケーションがデプロイされ、Cosmos DB がレプリケートされているリージョンに構成します。

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

## <a id="python"></a>Python SDK

アプリケーションでマルチマスターを有効にするには、`connection_policy.UseMultipleWriteLocations` を true に設定し、`connection_policy.PreferredLocations` をアプリケーションがデプロイされ、Cosmos DB がレプリケートされているリージョンに構成します。

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>次の手順

Azure Cosmos DB でのマルチマスター、グローバル配布、および一貫性について学びます。 次の記事を参照してください。

* [Azure Cosmos DB で一貫性を管理するためにセッション トークンを利用する](how-to-manage-consistency.md#utilize-session-tokens)

* [Azure Cosmos DB の競合の種類と解決ポリシー](conflict-resolution-policies.md)

* [Azure Cosmos DB での高可用性](high-availability.md)

* [Azure Cosmos DB での適切な一貫性レベルの選択](consistency-levels-choosing.md)

* [Azure Cosmos DB の一貫性、可用性、パフォーマンスのトレードオフ](consistency-levels-tradeoffs.md)
