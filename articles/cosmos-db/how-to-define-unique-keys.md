---
title: Azure Cosmos コンテナーの一意のキーを定義する
description: Azure Cosmos コンテナーの一意のキーを定義する方法について説明する
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: thweiss
ms.openlocfilehash: 313dd07c2b8eeb5684310b57d74053d3cbc1b5e1
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356387"
---
# <a name="define-unique-keys-for-an-azure-cosmos-container"></a>Azure Cosmos コンテナーの一意のキーを定義する

この記事では、Azure Cosmos コンテナーを作成するときに、[一意のキー](unique-keys.md) を定義するさまざまな方法を示します。 現在、Azure portal を使用するか、Sdk のいずれかでこの操作を実行することになります。

## <a name="use-the-azure-portal"></a>Azure ポータルの使用

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos アカウントを作成する](create-sql-api-dotnet.md#create-account)か、既存のアカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて、操作の対象となるコンテナーを選択します。

1. **[新しいコンテナー]** をクリックします。

1. **[コンテナーの追加]** ダイアログで、 **[+ Add unique key]** (+ 一意のキーの追加) をクリックして一意のキー エントリを追加します。

1. 一意キー制約のパスを入力します

1. 必要な場合は、 **[+ Add unique key]** (+ 一意のキーの追加) をクリックして、一意のキー エントリを追加します

![Azure portal での一意キー制約エントリのスクリーンショット](./media/how-to-define-unique-keys/unique-keys-portal.png)

## <a name="use-the-net-sdk-v2"></a>.NET SDK V2 の使用

[.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) を使用して新しいコンテナーを作成するときに、`UniqueKeyPolicy` オブジェクトを使用して一意キー制約を定義できます。

```csharp
client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("database"), new DocumentCollection
{
    Id = "container",
    UniqueKeyPolicy = new UniqueKeyPolicy
    {
        UniqueKeys = new Collection<UniqueKey>(new List<UniqueKey>
        {
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/firstName", "/lastName", "emailAddress" }) },
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/address/zipCode" }) }
        })
    }
});
```

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

## <a name="next-steps"></a>次の手順

- [パーティション分割](partition-data.md)の詳細を学習する
- [インデックス作成の動作方法](index-overview.md)の詳細を確認する