---
title: Azure Cosmos DB の MongoDB 用 API を使用したグローバル分散のチュートリアル
description: Azure Cosmos DB の MongoDB 用 API を使用してグローバル分散を設定する方法について説明します。
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 5ae5923253575fc3dea6b90b599b9fa3d79a85b8
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54041375"
---
# <a name="set-up-global-distributed-database-using-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API を使用してグローバル分散型データベースを設定する

この記事では、Azure portal を使用してグローバル分散型データベースを設定し、Azure Cosmos DB の MongoDB 用 API を使用してそれに接続する方法を説明します。

この記事に含まれるタスクは次のとおりです。 

> [!div class="checklist"]
> * Azure Portal を使用してグローバル分散を構成する
> * [Azure Cosmos DB の MongoDB 用 API](mongodb-introduction.md) を使用してグローバル分散を構成する

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="verifying-your-regional-setup"></a>リージョン設定を確認する 
Cosmos DB の MongoDB 用 API を使用してグローバル構成を確認する簡単な方法として、Mongo シェルから *isMaster()* コマンドを実行する方法があります。

Mongo シェルから次のコマンドを実行します。

   ```
      db.isMaster()
   ```
   
結果の例:

   ```JSON
      {
         "_t": "IsMasterResponse",
         "ok": 1,
         "ismaster": true,
         "maxMessageSizeBytes": 4194304,
         "maxWriteBatchSize": 1000,
         "minWireVersion": 0,
         "maxWireVersion": 2,
         "tags": {
            "region": "South India"
         },
         "hosts": [
            "vishi-api-for-mongodb-southcentralus.documents.azure.com:10255",
            "vishi-api-for-mongodb-westeurope.documents.azure.com:10255",
            "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
         ],
         "setName": "globaldb",
         "setVersion": 1,
         "primary": "vishi-api-for-mongodb-southindia.documents.azure.com:10255",
         "me": "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
      }
   ```

## <a name="connecting-to-a-preferred-region"></a>優先リージョンに接続する 

Azure Cosmos DB の MongoDB 用 API を使用すると、グローバル分散型データベースのコレクションの読み取り設定を指定することができます。 待機時間の短い読み取りとグローバルな高可用性の両方を得るために、コレクションの読み取り設定には *Nearest* を設定することをお勧めします。 *Nearest* 読み取り設定は、最寄りのリージョンから読み取るように構成します。

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Nearest));
```

プライマリ読み取り/書き込みリージョンとディザスター リカバリー (DR) 用のセカンダリ リージョンを使用するアプリケーションでは、コレクションの読み取り設定を *SecondaryPreferred* に設定することをお勧めします。 *SecondaryPreferred* 読み取り設定は、プライマリ リージョンが利用できない場合、セカンダリ リージョンから読み取るように構成します。

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.SecondaryPreferred));
```

最後に、読み取りリージョンを手動で指定する場合について説明します。 リージョン タグは、読み取り設定で設定できます。

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
var tag = new Tag("region", "Southeast Asia");
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Secondary, new[] { new TagSet(new[] { tag }) }));
```

このチュートリアルはこれで終わりです。 [Azure Cosmos DB の一貫性レベル](consistency-levels.md)に関する記事を読んで、グローバルにレプリケートされたアカウントの整合性を管理する方法について確認できます。 また、Azure Cosmos DB におけるグローバル データベース レプリケーションの動作の詳細については、[Azure Cosmos DB を使用したデータのグローバル分散](distribute-data-globally.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次の手順を行いました。

> [!div class="checklist"]
> * Azure Portal を使用してグローバル分散を構成する
> * Cosmos DB の MongoDB 用 API を使用してグローバル分散を構成する

これで次のチュートリアルに進むことができます。Azure Cosmos DB ローカル エミュレーターを使用してローカルで開発する方法について学びます。

> [!div class="nextstepaction"]
> [Azure Cosmos DB Emulator を使用したローカルでの開発](local-emulator.md)
