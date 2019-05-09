---
title: Azure Cosmos DB の MongoDB 用 API でのインデックス作成
description: Azure Cosmos DB の MongoDB 用 API のインデックス作成機能の概要を示します。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.openlocfilehash: de037316efa50dd25ea04c370fa0e5878fb52ba1
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54040406"
---
# <a name="indexing-using-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API を使用するインデックス作成

Azure Cosmos DB の MongoDB 用 API では、Cosmos DB の自動インデックス管理機能を利用します。 したがって、ユーザーは Cosmos DB の既定のインデックス作成ポリシーにアクセスすることができます。 そのため、ユーザーによってインデックスが定義されなかった場合や、いずれのインデックスも削除されなかった場合、すべてのフィールドは、コレクションに挿入された時点で、インデックスが自動的に作成されます。 ほとんどのシナリオでは、アカウントに設定されている既定のインデックス作成ポリシーの使用をお勧めします。

## <a name="dropping-the-default-indexes"></a>既定のインデックスの削除

コレクション ```coll``` に使用される既定のインデックスを削除するには、次のコマンドを使用します。

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

## <a name="creating-compound-indexes"></a>複合インデックスの作成

複合インデックスは、ドキュメントの複数のフィールドに対する参照を保持します。 論理上は、フィールドごとに異なる複数のインデックスを作成することに相当します。 Cosmos DB のインデックス作成手法から得られる最適化の利点を活かすために、単一 (非一意性) の複合インデックスを作成するのではなく、複数の個別インデックスを作成するようお勧めします。

## <a name="creating-unique-indexes"></a>一意なインデックスの作成

[一意なインデックス](unique-keys.md)を使用すると、インデックス付けされたフィールドに関して、同じ値を含んだドキュメントが複数存在することを確実に回避できます。 
>[!important] 
> 現在、一意なインデックスを作成できるのは、コレクションが空である (ドキュメントが含まれていない) ときに限られます。 

次のコマンドは、フィールド "student_id" に対して一意なインデックスを作成しています。

```JavaScript
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1 }, {unique:true} ) 
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

シャード コレクションの場合、MongoDB の動作上、一意なインデックスを作成するためには、シャード (パーティション) キーを指定する必要があります。 つまり、シャード コレクション上の一意なインデックスはすべて、いずれかのフィールドがパーティション キーである複合インデックスとなります。

以下のコマンドでは、student_id フィールドと university フィールドに対する一意なインデックスを持ったシャード コレクション```coll``` (シャード キーは ```university```) を作成します。

```JavaScript
globaldb:PRIMARY> db.runCommand({shardCollection: db.coll._fullName, key: { university: "hashed"}});
{
        "_t" : "ShardCollectionResponse",
        "ok" : 1,
        "collectionsharded" : "test.coll"
}
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1, "university" : 1 }, {unique:true})
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 3,
        "numIndexesAfter" : 4
}
```

上の例で、仮に ```"university":1``` 句を省略した場合、エラーが発生して次のメッセージが返されます。

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

## <a name="ttl-indexes"></a>TTL インデックス

特定のコレクションにおいてドキュメントの有効期限を有効にするには、["TTL インデックス" (Time to Live インデックス)](../cosmos-db/time-to-live.md) を作成する必要があります。 TTL インデックスは、"expireAfterSeconds" を値として持つ _ts フィールドに対するインデックスです。
 
例:
```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

前出のコマンドでは、過去 10 秒間、変更されなかったドキュメントが ```db.coll``` コレクションからすべて削除されます。 
 
> [!NOTE]
> **_ts** は Cosmos DB に固有のフィールドであり、MongoDB クライアントからアクセスすることはできません。 予約された (システム) プロパティであり、ドキュメントの最終変更日時のタイムスタンプを含んでいます。
>

## <a name="migrating-collections-with-indexes"></a>インデックスを伴うコレクションの移行

現在、一意なインデックスを作成できるのは、コレクションにドキュメントが含まれていないときに限られます。 広く使われている MongoDB 移行ツールはいずれも、データのインポート後に一意なインデックスを作成しようと試みます。 この問題を回避するためには、移行ツールに委ねることはせずに、対応するコレクションと一意なインデックスをユーザーが手動で作成することをお勧めします (```mongorestore``` の場合、この動作を実現するには、コマンド ラインで --noIndexRestore フラグを使用します)。

## <a name="next-steps"></a>次の手順
* [Azure Cosmos DB のインデックス作成](../cosmos-db/index-policy.md)
* [Time to Live を使用して Azure Cosmos DB のデータの有効期限が自動的に切れるようにする](../cosmos-db/time-to-live.md)
