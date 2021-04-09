---
title: Azure Cosmos DB の MongoDB 用 API でマルチドキュメント トランザクションを使用する
description: Azure Cosmos DB の MongoDB 4.0 用 API で、固定コレクションに対してマルチドキュメント トランザクション (全か無かのセマンティック) を実行できるサンプル Mongo シェル アプリを作成する方法について説明します。
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: gahllevy
ms.openlocfilehash: f319db76c8aee5a2a35ff8ca9670c42089350ede
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101692472"
---
# <a name="use-multi-document-transactions-in-azure-cosmos-db-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API でマルチドキュメント トランザクションを使用する
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

この記事では、サーバー バージョン 4.0 で Azure Cosmos DB の MongoDB 用 API の固定コレクションに対してマルチドキュメント トランザクションを実行する Mongo シェル アプリを作成します。

## <a name="what-are-multi-document-transactions"></a>マルチドキュメント トランザクションとは

Azure Cosmos DB の MongoDB 用 API では、1 つのドキュメントに対する操作はアトミックです。 マルチドキュメント トランザクションを使用すると、アプリケーションで複数のドキュメントにまたがるアトミック操作を実行できます。 操作に対する "全か無か" のセマンティクスがもたらされます。 コミット時には、トランザクション内で行われた変更は保持され、トランザクションが失敗した場合はトランザクション内のすべての変更が破棄されます。

マルチドキュメント トランザクションでは **ACID** セマンティクスに従います。

* 原子性: すべての操作が 1 つとして扱われます。
* 一貫性: コミットされたデータは有効です。
* 分離性: 他の操作から分離されています。
* 持続性: トランザクション データはクライアントから通知されたときに保持されます。

## <a name="requirements"></a>要件

API バージョン 4.0 では、シャード化されていないコレクション内でマルチドキュメント トランザクションがサポートされています。 コレクションをまたいだマルチドキュメント トランザクションやシャード化されたコレクションにおけるマルチドキュメント トランザクションは、4.0 ではサポートされません。 トランザクションのタイムアウトは 5 秒に固定されています。

ワイヤ プロトコル バージョン 4.0 以上をサポートするすべてのドライバーで、Azure Cosmos DB の MongoDB 用 API がサポートされます。

## <a name="run-multi-document-transactions-in-mongodb-shell"></a>MongoDB シェルでマルチドキュメント トランザクションを実行する
> [!Note]
> MongoDB Compass に埋め込まれた MongoSH ベータ版 (シェル) では、この例は機能しません。

1. コマンド プロンプトを開き、Mongo シェル バージョン 4.0 以降がインストールされているディレクトリに移動します。

   ```powershell
   cd <path_to_mongo_shell_>
   ```

2. Mongo シェル スクリプト *connect_friends.js* を作成し、次の内容を追加します。

   ```javascript
   // insert data into friends collection
   db.getMongo().getDB("users").friends.insert({name:"Tom"})
   db.getMongo().getDB("users").friends.insert({name:"Mike"})
   // start transaction
   var session = db.getMongo().startSession();
   var friendsCollection = session.getDatabase("users").friends;
   session.startTransaction();
   // operations in transaction
   try {
       friendsCollection.updateOne({ name: "Tom" }, { $set: { friendOf: "Mike" } } );
       friendsCollection.updateOne({ name: "Mike" }, { $set: { friendOf: "Tom" } } );
   } catch (error) {
       // abort transaction on error
       session.abortTransaction();
       throw error;
   }

    // commit transaction
    session.commitTransaction();

    ```

3. 次のコマンドを実行して、マルチドキュメント トランザクションを実行します。 ホスト、ポート、ユーザー、およびキーは Azure portal にあります。

   ```powershell
   mongo "<HOST>:<PORT>" -u "<USER>" -p "KEY" --ssl connect_friends.js
   ```

## <a name="next-steps"></a>次のステップ

[Azure Cosmos DB の MongoDB 4.0 用 API](mongodb-feature-support-40.md) の新機能を確認する
