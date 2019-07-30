---
title: Azure Cosmos DB で Time to Live を構成および管理する方法を学習する
description: Azure Cosmos DB で Time to Live を構成および管理する方法を学習する
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 618e7e19b20f361aa0a8c668e9621a29db43772d
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797749"
---
# <a name="configure-time-to-live-in-azure-cosmos-db"></a>Azure Cosmos DB で Time to Live を構成する

Azure Cosmos DB では、コンテナー レベルで Time to Live (TTL) の構成を選択することも、コンテナーでの設定後に項目レベルでそれをオーバーライドすることもできます。 Azure portal または言語固有の SDK を使用して、コンテナーの TTL を構成できます。 項目レベルの TTL オーバーライドは、SDK を使用して構成できます。

## <a name="enable-time-to-live-on-a-container-using-azure-portal"></a>Azure portal を使用してコンテナーの Time to Live を有効にする

有効期限のないコンテナーの Time to Live を有効にするには、次の手順を使用します。 これを有効にすると、項目レベルで TTL をオーバーライドできます。 秒数として 0 以外の値を入力して TTL を設定することもできます。

1. [Azure Portal](https://portal.azure.com/) にサインインします。

2. 新しい Azure Cosmos アカウントを作成するか、既存のアカウントを選択します。

3. **[データ エクスプローラー]** ウィンドウを開きます。

4. 既存のコンテナーを選択し、展開して以下の値を変更します。

   * **[Scale & Settings]\(スケールと設定\)** ウィンドウを開きます。
   * **[設定]** で **[Time to Live]** を探します。
   * **[オン (既定値なし)]** または **[オン]** を選択し、TTL 値を設定します
   * **[保存]** をクリックして変更を保存します。

   ![Azure portal で Time to Live を構成する](./media/how-to-time-to-live/how-to-time-to-live-portal.png)


- DefaultTimeToLive が null の場合、Time to Live はオフになります
- DefaultTimeToLive が -1 の場合、Time to Live の設定はオン (既定値なし) になります
- DefaultTimeToLive が他の整数値 (0 を除く) の場合、Time to Live の設定はオンになります

## <a name="enable-time-to-live-on-a-container-using-sdk"></a>SDK を使用してコンテナーの Time to Live を有効にする

### <a id="dotnet-enable-noexpiry"></a>.NET SDK

```csharp
// Create a new collection with TTL enabled and without any expiration value
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = -1; //(never expire by default)

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition,
    new RequestOptions { OfferThroughput = 20000 });
```

## <a name="set-time-to-live-on-a-container-using-sdk"></a>SDK を使用してコンテナーの Time to Live を設定する

### <a id="dotnet-enable-withexpiry"></a>.NET SDK

コンテナーの Time to Live を設定するには、秒単位で期間を示す 0 以外の正の数を指定する必要があります。 構成された TTL 値に基づいて、項目 `_ts` の最終変更タイムスタンプより後のコンテナー内の項目はすべて削除されます。

```csharp
// Create a new collection with TTL enabled and a 90 day expiration
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition,
    new RequestOptions { OfferThroughput = 20000 });
```

### <a id="nodejs-enable-withexpiry"></a>NodeJS SDK

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

### <a id="portal-set-ttl-item"></a>Azure Portal

項目に対して Time to Live を有効にするには、次の手順を使用します。

1. [Azure Portal](https://portal.azure.com/) にサインインします。

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

### <a id="dotnet-set-ttl-item"></a>.NET SDK

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

### <a id="nodejs-set-ttl-item"></a>NodeJS SDK

```javascript
const itemDefinition = {
          id: "doc",
          name: "sample Item",
          key: "value", 
          ttl: 2
        };
```


## <a name="reset-time-to-live"></a>Time to Live をリセットする

項目に対して書き込みまたは更新の操作を実行することにより、項目の Time to Live をリセットできます。 書き込みまたは更新の操作を行うと、`_ts` が現在の時刻に設定され、項目が期限切れになる TTL が再び開始されます。 項目の TTL を変更したい場合は、他のフィールドを更新する場合と同じように、このフィールドを更新できます。

### <a id="dotnet-extend-ttl-item"></a>.NET SDK

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

## <a name="turn-off-time-to-live"></a>Time to Live をオフにする

項目に Time to Live が設定されていて、その項目を期限切れにする必要がなくなった場合は、その項目を取得し、TTL フィールドを削除して、サーバー上の項目を置き換えることができます。 項目から TTL フィールドが削除されると、コンテナーに割り当てられている既定の TTL 値がその項目に適用されます。 項目が期限切れにならないようにし、コンテナーから TTL 値を継承しないようにするには、TTL 値を -1 に設定します。

### <a id="dotnet-turn-off-ttl-item"></a>.NET SDK

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = null; // inherit the default TTL of the collection

response = await client.ReplaceDocumentAsync(readDocument);
```

## <a name="disable-time-to-live"></a>Time to Live を無効にする

コンテナーの Time to Live を無効にし、バックグラウンド プロセスによる期限切れ項目の確認を停止するには、コンテナーの `DefaultTimeToLive` プロパティを削除する必要があります。 このプロパティの削除は、-1 に設定することとは異なります。 -1 に設定した場合は、コンテナーに追加される新しい項目が無期限になりますが、コンテナーに含まれている特定の項目についてこの値をオーバーライドできます。 コンテナーから TTL プロパティを削除した場合は、以前の既定の TTL が明示的にオーバーライドされていても、項目が期限切れになることはありません。

### <a id="dotnet-disable-ttl"></a>.NET SDK

```csharp
// Get the collection, update DefaultTimeToLive to null
DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
// Disable TTL
collection.DefaultTimeToLive = null;
await client.ReplaceDocumentCollectionAsync(collection);
```

## <a name="next-steps"></a>次の手順

Time to Live の詳細について、以下の記事で学習します。

* [Time to Live](time-to-live.md)
