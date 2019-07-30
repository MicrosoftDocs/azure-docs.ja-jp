---
title: Azure Cosmos DB で一貫性を管理する方法について
description: Azure Cosmos DB で一貫性を管理する方法について
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/08/2019
ms.author: mjbrown
ms.openlocfilehash: 2617aba0d790209d83f410ee632ffad43c952d55
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356424"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Azure Cosmos DB で一貫性レベルを管理する

この記事では、Azure Cosmos DB で一貫性レベルを管理する方法について説明します。 読者は、既定の一貫性レベルを構成する方法、既定の一貫性をオーバーライドする方法、セッション トークンを手動を管理する方法、および確率論的有界整合性制約 (PBS) メトリックを確認する方法について学習します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-the-default-consistency-level"></a>既定の一貫性レベルを構成する

[既定の整合性レベル](consistency-levels.md)は、クライアントによって既定で使用される整合性レベルです。 クライアントはいつでもこれをオーバーライドできます。

### <a name="cli"></a>CLI

```bash
# create with a default consistency
az cosmosdb create --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Session

# update an existing account's default consistency
az cosmosdb update --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Eventual
```

### <a name="powershell"></a>PowerShell

この例では、複数の書き込みリージョン (米国東部リージョンと米国西部リージョン) を有効にした、新しい Azure Cosmos アカウントを作成します。 既定の整合性レベルは、*セッション*整合性に設定されています。

```azurepowershell-interactive
$locations = @(@{"locationName"="East US"; "failoverPriority"=0},
             @{"locationName"="West US"; "failoverPriority"=1})

$iprangefilter = ""

$consistencyPolicy = @{"defaultConsistencyLevel"="Session"}

$CosmosDBProperties = @{"databaseAccountOfferType"="Standard";
                        "locations"=$locations;
                        "consistencyPolicy"=$consistencyPolicy;
                        "ipRangeFilter"=$iprangefilter;
                        "enableMultipleWriteLocations"="true"}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
  -ApiVersion "2015-04-08" `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -Name "myCosmosDbAccount" `
  -Properties $CosmosDBProperties
```

### <a name="azure-portal"></a>Azure ポータル

既定の一貫性レベルを表示または変更するには、Azure portal にサインインします。 Azure Cosmos アカウントを見つけて、 **[既定の整合性]** ウィンドウを開きます。 目的の一貫性レベルを新しい既定として選択し、 **[保存]** を選択します。 Azure portal では、音符によるさまざまな一貫性レベルの視覚化も提供しています。 

![Azure portal の一貫性メニュー](./media/how-to-manage-consistency/consistency-settings.png)

## <a name="override-the-default-consistency-level"></a>既定の一貫性レベルを上書きする

クライアントは、サービスによって設定された既定の一貫性レベルを上書きできます。 整合性レベルは要求ごとに設定できます。この設定によって、アカウント レベルで設定される既定の整合性レベルがオーバーライドされます。

### <a id="override-default-consistency-dotnet"></a>.NET SDK V2

```csharp
// Override consistency at the client level
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, ConsistencyLevel.Eventual);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Eventual };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
```

### <a id="override-default-consistency-dotnet-v3"></a>.NET SDK V3

```csharp
// Override consistency at the request level via request options
ItemRequestOptions requestOptions = new ItemRequestOptions { ConsistencyLevel = ConsistencyLevel.Strong };

var response = await client.GetContainer(databaseName, containerName)
    .CreateItemAsync(
        item, 
        new PartitionKey(itemPartitionKey), 
        requestOptions);
```

### <a id="override-default-consistency-java-async"></a>Java Async SDK

```java
// Override consistency at the client level
ConnectionPolicy policy = new ConnectionPolicy();

AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConsistencyLevel(ConsistencyLevel.Eventual)
                .withConnectionPolicy(policy).build();
```

### <a id="override-default-consistency-java-sync"></a>Java Sync SDK

```java
// Override consistency at the client level
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Eventual);
```

### <a id="override-default-consistency-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Eventual
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a id="override-default-consistency-python"></a>Python SDK

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Eventual)
```

## <a name="utilize-session-tokens"></a>セッション トークンを利用する

Azure Cosmos DB の整合性レベルの 1 つとして、"*セッション*" の整合性があります。 これは、Cosmos アカウントに既定で適用されるレベルです。 "*セッション*" の整合性で作業している場合、クライアントでは設定した整合性レベルが維持されるように、各読み取り/クエリ要求で内部的にセッション トークンが使用されます。

セッション トークンを手動で管理するには、応答からセッション トークンを取得し、それらを要求ごとに設定します。 セッション トークンを手動で管理する必要がない場合は、これらのサンプルを使用する必要はありません。 SDK では、セッション トークンが自動的に追跡されます。 セッション トークンを手動で設定しなかった場合、既定では、SDK によって直近のセッション トークンが使用されます。

### <a id="utilize-session-tokens-dotnet"></a>.NET SDK V2

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

### <a id="utilize-session-tokens-dotnet-v3"></a>.NET SDK V3

```csharp
Container container = client.GetContainer(databaseName, collectionName);
ItemResponse<SalesOrder> response = await container.CreateItemAsync<SalesOrder>(salesOrder);
string sessionToken = response.Headers.Session;

ItemRequestOptions options = new ItemRequestOptions();
options.SessionToken = sessionToken;
ItemResponse<SalesOrder> response = await container.ReadItemAsync<SalesOrder>(salesOrder.Id, new PartitionKey(salesOrder.PartitionKey), options);
```

### <a id="utilize-session-tokens-java-async"></a>Java Async SDK

```java
// Get session token from response
RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey(document.get("mypk")));
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
readObservable.single()           // we know there will be one response
  .subscribe(
      documentResourceResponse -> {
          System.out.println(documentResourceResponse.getSessionToken());
      },
      error -> {
          System.err.println("an error happened: " + error.getMessage());
      });

// Resume the session by setting the session token on RequestOptions
RequestOptions options = new RequestOptions();
requestOptions.setSessionToken(sessionToken);
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
```

### <a id="utilize-session-tokens-java-sync"></a>Java Sync SDK

```java
// Get session token from response
ResourceResponse<Document> response = client.readDocument(documentLink, null);
String sessionToken = response.getSessionToken();

// Resume the session by setting the session token on the RequestOptions
RequestOptions options = new RequestOptions();
options.setSessionToken(sessionToken);
ResourceResponse<Document> response = client.readDocument(documentLink, options);
```

### <a id="utilize-session-tokens-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Get session token from response
const { headers, item } = await container.items.create({ id: "meaningful-id" });
const sessionToken = headers["x-ms-session-token"];

// Immediately or later, you can use that sessionToken from the header to resume that session.
const { body } = await item.read({ sessionToken });
```

### <a id="utilize-session-tokens-python"></a>Python SDK

```python
// Get the session token from the last response headers
item = client.ReadItem(item_link)
session_token = client.last_response_headers["x-ms-session-token"]

// Resume the session by setting the session token on the options for the request
options = {
    "sessionToken": session_token
}
item = client.ReadItem(doc_link, options)
```

## <a name="monitor-probabilistically-bounded-staleness-pbs-metric"></a>確率的有界整合性制約 (PBS) メトリックを監視する

最終的な整合性は、どのくらい最終的でしょうか。 平均的なケースでは、バージョン履歴と時刻に関して、古さの限度を提示できます。 [**確率的有界整合性制約 (PBS)** ](https://pbs.cs.berkeley.edu/) メトリックは、古さの可能性を定量化し、それをメトリックとして表示します。 PBS メトリックを表示するには、Azure portal で Azure Cosmos アカウントに移動します。 **[メトリック]** ウィンドウを開き、 **[整合性]** タブを選択します。**Probability of strongly consistent reads based on your workload (see PBS) (ワークロードに基づいた、強固な一貫性がある読み取りの確率 (PBS を参照))** という名前のグラフを確認します。

![Azure portal の PBS グラフ](./media/how-to-manage-consistency/pbs-metric.png)


## <a name="next-steps"></a>次の手順

データの競合を管理する方法について学習するか、Azure Cosmos DB に関する次の主要概念に進んでください。 次の記事を参照してください。

* [Azure Cosmos DB の整合性レベル](consistency-levels.md)
* [リージョン間の競合の管理](how-to-manage-conflicts.md)
* [パーティション分割とデータ分散](partition-data.md)
* [最新の分散データベース システム設計における整合性のトレードオフ](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
* [高可用性](high-availability.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
