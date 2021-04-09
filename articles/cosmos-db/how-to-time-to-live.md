---
title: Azure Cosmos DB で Time to Live を構成および管理する
description: Azure Cosmos DB でコンテナーと項目の Time to Live を構成および管理する方法について説明します
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/11/2020
ms.author: anfeldma
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 2ddba95f9ccc25d536638dbc68c41027d26e71c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341010"
---
# <a name="configure-time-to-live-in-azure-cosmos-db"></a>Azure Cosmos DB で Time to Live を構成する
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB では、コンテナー レベルで Time to Live (TTL) の構成を選択することも、コンテナーでの設定後に項目レベルでそれをオーバーライドすることもできます。 Azure portal または言語固有の SDK を使用して、コンテナーの TTL を構成できます。 項目レベルの TTL オーバーライドは、SDK を使用して構成できます。

> このコンテンツは Azure Cosmos DB トランザクション ストア TTL に関連付けられています。 [Azure Synapse Link](./synapse-link.md) 経由で NoETL HTAP シナリオを可能にする分析ストア TTL をお探しの場合、こちらを[クリック](./analytical-store-introduction.md#analytical-ttl)してください。

## <a name="enable-time-to-live-on-a-container-using-azure-portal"></a>Azure portal を使用してコンテナーの Time to Live を有効にする

有効期限のないコンテナーの Time to Live を有効にするには、次の手順を使用します。 これを有効にすると、項目レベルで TTL をオーバーライドできます。 秒数として 0 以外の値を入力して TTL を設定することもできます。

1. [Azure portal](https://portal.azure.com/) にサインインします。

2. 新しい Azure Cosmos アカウントを作成するか、既存のアカウントを選択します。

3. **[データ エクスプローラー]** ウィンドウを開きます。

4. 既存のコンテナーを選択し、展開して以下の値を変更します。

   * **[Scale & Settings]\(スケールと設定\)** ウィンドウを開きます。
   * **[設定]** で **[Time to Live]** を探します。
   * **[オン (既定値なし)]** または **[オン]** を選択し、TTL 値を設定します
   * **[保存]** をクリックして変更を保存します。

   :::image type="content" source="./media/how-to-time-to-live/how-to-time-to-live-portal.png" alt-text="Azure portal で Time to Live を構成する":::

* DefaultTimeToLive が null の場合、Time to Live はオフになります
* DefaultTimeToLive が -1 の場合、Time to Live の設定はオン (既定値なし) になります
* DefaultTimeToLive が他の整数値 (0 を除く) の場合、Time to Live の設定はオンになります

## <a name="enable-time-to-live-on-a-container-using-azure-cli-or-powershell"></a>Azure CLI または PowerShell を使用してコンテナーの Time to Live を有効にする

コンテナーで TTL を作成または有効にするには、以下を参照してください。

* [Azure CLI を使用して TTL を利用するコンテナーを作成する](manage-with-cli.md#create-a-container-with-ttl)
* [PowerShell を使用して TTL を利用するコンテナーを作成する](manage-with-powershell.md#create-container-unique-key-ttl)

## <a name="enable-time-to-live-on-a-container-using-sdk"></a>SDK を使用してコンテナーの Time to Live を有効にする

### <a name="net-sdk"></a><a id="dotnet-enable-noexpiry"></a> .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

.NET SDK V2 (Microsoft.Azure.DocumentDB)

```csharp
// Create a new container with TTL enabled and without any expiration value
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = -1; //(never expire by default)

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

.NET SDK V3 (Microsoft.Azure.Cosmos)

```csharp
// Create a new container with TTL enabled and without any expiration value
await client.GetDatabase("database").CreateContainerAsync(new ContainerProperties
{
    Id = "container",
    PartitionKeyPath = "/myPartitionKey",
    DefaultTimeToLive = -1 //(never expire by default)
});
```
---

### <a name="java-sdk"></a><a id="java-enable-noexpiry"></a> Java SDK

# <a name="java-sdk-v4"></a>[Java SDK V4](#tab/javav4)

Java SDK V4 (Maven com.azure::azure-cosmos)

```java
CosmosAsyncContainer container;

// Create a new container with TTL enabled and without any expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.setDefaultTimeToLiveInSeconds(-1);
container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

# <a name="java-sdk-v3"></a>[Java SDK V3](#tab/javav3)

Java SDK V3 (Maven com.microsoft.azure::azure-cosmos)

```java
CosmosContainer container;

// Create a new container with TTL enabled and without any expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(-1);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```
---

## <a name="set-time-to-live-on-a-container-using-sdk"></a>SDK を使用してコンテナーの Time to Live を設定する

コンテナーの Time to Live を設定するには、秒単位で期間を示す 0 以外の正の数を指定する必要があります。 構成された TTL 値に基づいて、項目 `_ts` の最終変更タイムスタンプより後のコンテナー内の項目はすべて削除されます。 必要に応じて、`TimeToLivePropertyPath` を設定できます。この場合、システムによって生成される `_ts` プロパティの代わりに別のプロパティが使用され、TTL に基づいて、削除する項目が決定されます。

### <a name="net-sdk"></a><a id="dotnet-enable-withexpiry"></a> .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

.NET SDK V2 (Microsoft.Azure.DocumentDB)

```csharp
// Create a new container with TTL enabled and a 90 day expiration
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24 // expire all documents after 90 days

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition;
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

.NET SDK V3 (Microsoft.Azure.Cosmos)

```csharp
// Create a new container with TTL enabled and a 90 day expiration
await client.GetDatabase("database").CreateContainerAsync(new ContainerProperties
{
    Id = "container",
    PartitionKeyPath = "/myPartitionKey",
    DefaultTimeToLive = 90 * 60 * 60 * 24 // expire all documents after 90 days
});
```
---

### <a name="java-sdk"></a><a id="java-enable-defaultexpiry"></a> Java SDK

# <a name="java-sdk-v4"></a>[Java SDK V4](#tab/javav4)

Java SDK V4 (Maven com.azure::azure-cosmos)

```java
CosmosAsyncContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.setDefaultTimeToLiveInSeconds(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

# <a name="java-sdk-v3"></a>[Java SDK V3](#tab/javav3)

Java SDK V3 (Maven com.microsoft.azure::azure-cosmos)

```java
CosmosContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```
---

### <a name="nodejs-sdk"></a><a id="nodejs-enable-withexpiry"></a>NodeJS SDK

```javascript
const containerDefinition = {
          id: "sample container1",
        };

async function createcontainerWithTTL(db: Database, containerDefinition: ContainerDefinition, collId: any, defaultTtl: number) {
      containerDefinition.id = collId;
      containerDefinition.defaultTtl = defaultTtl;
      await db.containers.create(containerDefinition);
}
```

## <a name="set-time-to-live-on-an-item"></a>項目の Time to Live を設定する

コンテナーに既定の Time to Live を設定するだけでなく、項目の Time to Live を設定することもできます。 項目レベルで Time to Live を設定すると、そのコンテナーに含まれている項目の既定の TTL がオーバーライドされます。

* 項目に対して TTL を設定するには、項目 `_ts` の最終変更タイムスタンプの後に項目が期限切れになるように、期間 (秒単位) を示す 0 以外の正の数を指定する必要があります。

* 項目に TTL フィールドがない場合、既定では、コンテナーに設定された TTL が項目に適用されます。

* TTL がコンテナー レベルで無効になっている場合、TTL がコンテナーで再び有効になるまで、項目の TTL フィールドは無視されます。

### <a name="azure-portal"></a><a id="portal-set-ttl-item"></a>Azure Portal

項目に対して Time to Live を有効にするには、次の手順を使用します。

1. [Azure portal](https://portal.azure.com/) にサインインします。

2. 新しい Azure Cosmos アカウントを作成するか、既存のアカウントを選択します。

3. **[データ エクスプローラー]** ウィンドウを開きます。

4. 既存のコンテナーを選択し、展開して以下の値を変更します。

   * **[Scale & Settings]\(スケールと設定\)** ウィンドウを開きます。
   * **[設定]** で **[Time to Live]** を探します。
   * **[オン (既定値なし)]** または **[オン]** を選択し、TTL 値を設定します。 
   * **[保存]** をクリックして変更を保存します。

5. 次に、Time to Live を設定する項目に移動し、`ttl` プロパティを追加して **[更新]** を選択します。 

   ```json
   {
    "id": "1",
    "_rid": "Jic9ANWdO-EFAAAAAAAAAA==",
    "_self": "dbs/Jic9AA==/colls/Jic9ANWdO-E=/docs/Jic9ANWdO-EFAAAAAAAAAA==/",
    "_etag": "\"0d00b23f-0000-0000-0000-5c7712e80000\"",
    "_attachments": "attachments/",
    "ttl": 10,
    "_ts": 1551307496
   }
   ```

### <a name="net-sdk-any"></a><a id="dotnet-set-ttl-item"></a>.NET SDK (すべて)

```csharp
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }
    [JsonProperty(PropertyName="cid")]
    public string CustomerId { get; set; }
    // used to set expiration policy
    [JsonProperty(PropertyName = "ttl", NullValueHandling = NullValueHandling.Ignore)]
    public int? ttl { get; set; }

    //...
}
// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder
{
    Id = "SO05",
    CustomerId = "CO18009186470",
    ttl = 60 * 60 * 24 * 30;  // Expire sales orders in 30 days
};
```

### <a name="nodejs-sdk"></a><a id="nodejs-set-ttl-item"></a>NodeJS SDK

```javascript
const itemDefinition = {
          id: "doc",
          name: "sample Item",
          key: "value",
          ttl: 2
        };
```

### <a name="java-sdk"></a><a id="java-set-ttl-item"></a> Java SDK

# <a name="java-sdk-v4"></a>[Java SDK V4](#tab/javav4)

Java SDK V4 (Maven com.azure::azure-cosmos)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String getId() {return this.id;}
    public void setId(String new_id) {this.id = new_id;}
    public String getCustomerId() {return this.customerId;}
    public void setCustomerId(String new_cid) {this.customerId = new_cid;}
    public Integer getTtl() {return this.ttl;}
    public void setTtl(Integer new_ttl) {this.ttl = new_ttl;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);

```

# <a name="java-sdk-v3"></a>[Java SDK V3](#tab/javav3)

Java SDK V3 (Maven com.microsoft.azure::azure-cosmos)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String id() {return this.id;}
    public void id(String new_id) {this.id = new_id;}
    public String customerId() {return this.customerId;}
    public void customerId(String new_cid) {this.customerId = new_cid;}
    public Integer ttl() {return this.ttl;}
    public void ttl(Integer new_ttl) {this.ttl = new_ttl;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);

```
---

## <a name="reset-time-to-live"></a>Time to Live をリセットする

項目に対して書き込みまたは更新の操作を実行することにより、項目の Time to Live をリセットできます。 書き込みまたは更新の操作を行うと、`_ts` が現在の時刻に設定され、項目が期限切れになる TTL が再び開始されます。 項目の TTL を変更したい場合は、他のフィールドを更新する場合と同じように、このフィールドを更新できます。

### <a name="net-sdk"></a><a id="dotnet-extend-ttl-item"></a> .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

.NET SDK V2 (Microsoft.Azure.DocumentDB)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = 60 * 30 * 30; // update time to live
response = await client.ReplaceDocumentAsync(readDocument);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

.NET SDK V3 (Microsoft.Azure.Cosmos)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
ItemResponse<SalesOrder> itemResponse = await client.GetContainer("database", "container").ReadItemAsync<SalesOrder>("SO05", new PartitionKey("CO18009186470"));

itemResponse.Resource.ttl = 60 * 30 * 30; // update time to live
await client.GetContainer("database", "container").ReplaceItemAsync(itemResponse.Resource, "SO05");
```
---

### <a name="java-sdk"></a><a id="java-enable-modifyitemexpiry"></a> Java SDK

# <a name="java-sdk-v4"></a>[Java SDK V4](#tab/javav4)

Java SDK V4 (Maven com.azure::azure-cosmos)

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
CosmosAsyncItemResponse<SalesOrder> itemResponse = container.readItem("SO05", new PartitionKey("CO18009186470"), SalesOrder.class)
        .flatMap(readResponse -> {
            SalesOrder salesOrder = readResponse.getItem();
            salesOrder.setTtl(60 * 30 * 30);
            return container.createItem(salesOrder);
}).block();
```

# <a name="java-sdk-v3"></a>[Java SDK V3](#tab/javav3)

SDK V3 (Maven com.microsoft.azure::azure-cosmos)

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
container.getItem("SO05", new PartitionKey("CO18009186470")).read()
        .flatMap(readResponse -> {
            SalesOrder salesOrder = null;
            try {
                salesOrder = readResponse.properties().getObject(SalesOrder.class);
            } catch (Exception err) {

            }
            salesOrder.ttl(60 * 30 * 30);
            return container.createItem(salesOrder);
}).block();
```
---

## <a name="turn-off-time-to-live"></a>Time to Live をオフにする

項目に Time to Live が設定されていて、その項目を期限切れにする必要がなくなった場合は、その項目を取得し、TTL フィールドを削除して、サーバー上の項目を置き換えることができます。 項目から TTL フィールドが削除されると、コンテナーに割り当てられている既定の TTL 値がその項目に適用されます。 項目が期限切れにならないようにし、コンテナーから TTL 値を継承しないようにするには、TTL 値を -1 に設定します。

### <a name="net-sdk"></a><a id="dotnet-turn-off-ttl-item"></a> .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

.NET SDK V2 (Microsoft.Azure.DocumentDB)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = null; // inherit the default TTL of the container

response = await client.ReplaceDocumentAsync(readDocument);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

.NET SDK V3 (Microsoft.Azure.Cosmos)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
ItemResponse<SalesOrder> itemResponse = await client.GetContainer("database", "container").ReadItemAsync<SalesOrder>("SO05", new PartitionKey("CO18009186470"));

itemResponse.Resource.ttl = null; // inherit the default TTL of the container
await client.GetContainer("database", "container").ReplaceItemAsync(itemResponse.Resource, "SO05");
```
---

### <a name="java-sdk"></a><a id="java-enable-itemdefaultexpiry"></a> Java SDK

# <a name="java-sdk-v4"></a>[Java SDK V4](#tab/javav4)

Java SDK V4 (Maven com.azure::azure-cosmos)

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
CosmosAsyncItemResponse<SalesOrder> itemResponse = container.readItem("SO05", new PartitionKey("CO18009186470"), SalesOrder.class)
        .flatMap(readResponse -> {
            SalesOrder salesOrder = readResponse.getItem();
            salesOrder.setTtl(null);
            return container.createItem(salesOrder);
}).block();
```

# <a name="java-sdk-v3"></a>[Java SDK V3](#tab/javav3)

Java SDK V3 (Maven com.microsoft.azure::azure-cosmos)

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
container.getItem("SO05", new PartitionKey("CO18009186470")).read()
        .flatMap(readResponse -> {
            SalesOrder salesOrder = null;
            try {
                salesOrder = readResponse.properties().getObject(SalesOrder.class);
            } catch (Exception err) {

            }
            salesOrder.ttl(null);
            return container.createItem(salesOrder);
}).block();
```
---

## <a name="disable-time-to-live"></a>Time to Live を無効にする

コンテナーの Time to Live を無効にし、バックグラウンド プロセスによる期限切れ項目の確認を停止するには、コンテナーの `DefaultTimeToLive` プロパティを削除する必要があります。 このプロパティの削除は、-1 に設定することとは異なります。 -1 に設定した場合は、コンテナーに追加される新しい項目が無期限になりますが、コンテナーに含まれている特定の項目についてこの値をオーバーライドできます。 コンテナーから TTL プロパティを削除した場合は、以前の既定の TTL が明示的にオーバーライドされていても、項目が期限切れになることはありません。

### <a name="net-sdk"></a><a id="dotnet-disable-ttl"></a> .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

.NET SDK V2 (Microsoft.Azure.DocumentDB)

```csharp
// Get the container, update DefaultTimeToLive to null
DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
// Disable TTL
collection.DefaultTimeToLive = null;
await client.ReplaceDocumentCollectionAsync(collection);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

.NET SDK V3 (Microsoft.Azure.Cosmos)

```csharp
// Get the container, update DefaultTimeToLive to null
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Disable TTL
containerResponse.Resource.DefaultTimeToLive = null;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```
---

### <a name="java-sdk"></a><a id="java-enable-disableexpiry"></a> Java SDK

# <a name="java-sdk-v4"></a>[Java SDK V4](#tab/javav4)

Java SDK V4 (Maven com.azure::azure-cosmos)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
// Disable TTL
containerProperties.setDefaultTimeToLiveInSeconds(null);
// Update container settings
container.replace(containerProperties).block();
```

# <a name="java-sdk-v3"></a>[Java SDK V3](#tab/javav3)

Java SDK V3 (Maven com.microsoft.azure::azure-cosmos)

```java
CosmosContainer container;

// Create a new container with TTL enabled and without any expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
// Disable TTL
containerProperties.defaultTimeToLive(null);
// Update container settings
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```
---

## <a name="next-steps"></a>次のステップ

Time to Live の詳細について、以下の記事で学習します。

* [Time to Live](time-to-live.md)