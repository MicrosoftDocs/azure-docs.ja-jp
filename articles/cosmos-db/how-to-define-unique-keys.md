---
title: Azure Cosmos コンテナーの一意のキーを定義する
description: Azure portal、PowerShell、.NET、Java、およびその他のさまざまな SDK を使用して、Azure Cosmos コンテナーの一意のキーを定義する方法について説明します。
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 12/02/2019
ms.author: thweiss
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: 55fc5222c1c245c56ba0a26caa816c5c845147c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93336624"
---
# <a name="define-unique-keys-for-an-azure-cosmos-container"></a>Azure Cosmos コンテナーの一意のキーを定義する
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

この記事では、Azure Cosmos コンテナーを作成するときに、[一意のキー](unique-keys.md) を定義するさまざまな方法を示します。 現在、Azure portal を使用するか、Sdk のいずれかでこの操作を実行することになります。

## <a name="use-the-azure-portal"></a>Azure ポータルの使用

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos アカウントを作成する](create-sql-api-dotnet.md#create-account)か、既存のアカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて、操作の対象となるコンテナーを選択します。

1. **[新しいコンテナー]** をクリックします。

1. **[コンテナーの追加]** ダイアログで、 **[+ Add unique key]** (+ 一意のキーの追加) をクリックして一意のキー エントリを追加します。

1. 一意キー制約のパスを入力します

1. 必要な場合は、 **[+ Add unique key]** (+ 一意のキーの追加) をクリックして、一意のキー エントリを追加します

    :::image type="content" source="./media/how-to-define-unique-keys/unique-keys-portal.png" alt-text="Azure portal での一意キー制約エントリのスクリーンショット":::

## <a name="use-powershell"></a>PowerShell の使用

一意のキーを持つコンテナーを作成する方法については、[一意のキーと TTL を使用した Azure Cosmos コンテナーの作成](manage-with-powershell.md#create-container-unique-key-ttl)に関するページを参照してください。

## <a name="use-the-net-sdk"></a>.NET SDK を使用する

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

[.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) を使用して新しいコンテナーを作成するときに、`UniqueKeyPolicy` オブジェクトを使用して一意キー制約を定義できます。

```csharp
client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("database"), new DocumentCollection
{
    Id = "container",
    PartitionKey = new PartitionKeyDefinition { Paths = new Collection<string>(new List<string> { "/myPartitionKey" }) },
    UniqueKeyPolicy = new UniqueKeyPolicy
    {
        UniqueKeys = new Collection<UniqueKey>(new List<UniqueKey>
        {
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/firstName", "/lastName", "/emailAddress" }) },
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/address/zipCode" }) }
        })
    }
});
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[.NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) を使用して新しいコンテナーを作成するときに、SDK の fluent API を使用して、簡潔で読みやすい方法で一意のキーを宣言します。

```csharp
await client.GetDatabase("database").DefineContainer(name: "container", partitionKeyPath: "/myPartitionKey")
    .WithUniqueKey()
        .Path("/firstName")
        .Path("/lastName")
        .Path("/emailAddress")
    .Attach()
    .WithUniqueKey()
        .Path("/address/zipCode")
    .Attach()
    .CreateIfNotExistsAsync();
```
---

## <a name="use-the-java-sdk"></a>Java SDK の使用

[Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) を使用して新しいコンテナーを作成するときに、`UniqueKeyPolicy` オブジェクトを使用して一意キー制約を定義できます。

```java
// create a new DocumentCollection object
DocumentCollection container = new DocumentCollection();
container.setId("container");

// create array of strings and populate them with the unique key paths
Collection<String> uniqueKey1Paths = new ArrayList<String>();
uniqueKey1Paths.add("/firstName");
uniqueKey1Paths.add("/lastName");
uniqueKey1Paths.add("/emailAddress");
Collection<String> uniqueKey2Paths = new ArrayList<String>();
uniqueKey2Paths.add("/address/zipCode");

// create UniqueKey objects and set their paths
UniqueKey uniqueKey1 = new UniqueKey();
UniqueKey uniqueKey2 = new UniqueKey();
uniqueKey1.setPaths(uniqueKey1Paths);
uniqueKey2.setPaths(uniqueKey2Paths);

// create a new UniqueKeyPolicy object and set its unique keys
UniqueKeyPolicy uniqueKeyPolicy = new UniqueKeyPolicy();
Collection<UniqueKey> uniqueKeys = new ArrayList<UniqueKey>();
uniqueKeys.add(uniqueKey1);
uniqueKeys.add(uniqueKey2);
uniqueKeyPolicy.setUniqueKeys(uniqueKeys);

// set the unique key policy
container.setUniqueKeyPolicy(uniqueKeyPolicy);

// create the container
client.createCollection(String.format("/dbs/%s", "database"), container, null);
```

## <a name="use-the-nodejs-sdk"></a>Node.js SDK の使用

[Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) を使用して新しいコンテナーを作成するときに、`UniqueKeyPolicy` オブジェクトを使用して一意キー制約を定義できます。

```javascript
client.database('database').containers.create({
    id: 'container',
    uniqueKeyPolicy: {
        uniqueKeys: [
            { paths: ['/firstName', '/lastName', '/emailAddress'] },
            { paths: ['/address/zipCode'] }
        ]
    }
});
```

## <a name="use-the-python-sdk"></a>Python SDK の使用

[Python SDK](https://pypi.org/project/azure-cosmos/) を使用して新しいコンテナーを作成するときに、パラメーターとして渡されるディクショナリの一部として一意キー制約を指定できます。

```python
client.CreateContainer('dbs/' + config['DATABASE'], {
    'id': 'container',
    'uniqueKeyPolicy': {
        'uniqueKeys': [
            {'paths': ['/firstName', '/lastName', '/emailAddress']},
            {'paths': ['/address/zipCode']}
        ]
    }
})
```

## <a name="next-steps"></a>次のステップ

- [パーティション分割](partitioning-overview.md)の詳細を学習する
- [インデックス作成の動作方法](index-overview.md)の詳細を確認する
