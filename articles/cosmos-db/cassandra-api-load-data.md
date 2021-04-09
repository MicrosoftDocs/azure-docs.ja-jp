---
title: 'チュートリアル: Azure Cosmos DB 内の Cassandra API テーブルにサンプル データを読み込む Java アプリ'
description: このチュートリアルでは、Java アプリケーションを使用して Azure Cosmos DB 内の Cassandra API テーブルにサンプル ユーザー データを読み込む方法を示します。
author: kanshiG
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: ad3dfc8c86d22e58377ebfbdb94b2beb004ded56
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93092468"
---
# <a name="tutorial-load-sample-data-into-a-cassandra-api-table-in-azure-cosmos-db"></a>チュートリアル: Azure Cosmos DB 内の Cassandra API テーブルにサンプル データを読み込む
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

開発者は、キー/値ペアを使用するアプリケーションを持っていることがあります。 Azure Cosmos DB の Cassandra API アカウントを使用して、キー/値データを格納して管理できます。 このチュートリアルでは、Java アプリケーションを使用して、Azure Cosmos DB の Cassandra API アカウント内のテーブルにサンプル ユーザー データを読み込む方法を示します。 この Java アプリケーションでは、[Java ドライバー](https://github.com/datastax/java-driver)を使用して、ユーザー ID、ユーザー名、ユーザー所在地などのユーザー データを読み込みます。 

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * Cassandra テーブルへのデータの読み込み
> * アプリを実行する

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

* この記事では、マルチパートのチュートリアルの一部です｡ このドキュメントに取り組むにあたっては、必ず事前に [Cassandra API アカウント、キースペース、およびテーブルを作成](create-cassandra-api-account-java.md)しておいてください。   

## <a name="load-data-into-the-table"></a>テーブルにデータを読み込む

以下の手順を使用して、Cassandra API テーブルにデータを読み込みます。

1. "Src\main\java\com\azure\cosmosdb\cassandra"フォルダーにある "UserRepository.java" ファイルを開いて、user_id、user_name、および user_bcity フィールドをテーブルに挿入するコードを追加します。

   ```java
   /**
   * Insert a row into user table
   *
   * @param id   user_id
   * @param name user_name
   * @param city user_bcity
   */
   public void insertUser(PreparedStatement statement, int id, String name, String city) {
        BoundStatement boundStatement = new BoundStatement(statement);
        session.execute(boundStatement.bind(id, name, city));
   }

   /**
   * Create a PrepareStatement to insert a row to user table
   *
   * @return PreparedStatement
   */
   public PreparedStatement prepareInsertStatement() {
      final String insertStatement = "INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (?,?,?)";
   return session.prepare(insertStatement);
   }
   ```
 
2. "Src\main\java\com\azure\cosmosdb\cassandra"フォルダーにある "UserProfile.java" ファイルを開きます。 このクラスには、以前に定義した createKeyspace と createTable メソッドを呼び出すメイン メソッドが含まれています。 これで、Cassandra API テーブルにサンプル データを挿入する次のコードを追加します。

   ```java
   //Insert rows into user table
   PreparedStatement preparedStatement = repository.prepareInsertStatement();
     repository.insertUser(preparedStatement, 1, "JohnH", "Seattle");
     repository.insertUser(preparedStatement, 2, "EricK", "Spokane");
     repository.insertUser(preparedStatement, 3, "MatthewP", "Tacoma");
     repository.insertUser(preparedStatement, 4, "DavidA", "Renton");
     repository.insertUser(preparedStatement, 5, "PeterS", "Everett");
   ```

## <a name="run-the-app"></a>アプリを実行する

コマンド プロンプトまたはターミナル ウィンドウを開いて、フォルダーのパスをプロジェクトを作成した場所に変更します。 "mvn clean install" コマンドを実行してターゲット フォルダー内に cassandra-examples.jar ファイルを生成し、アプリケーションを実行します。 

```bash
cd "cassandra-demo"

mvn clean install

java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
```

これで､Azure portal でデータ エクスプローラーを開きユーザー情報がテーブルに追加されていることを確認できます｡
    
## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Cosmos DB の Cassandra API アカウントにサンプル データを読み込む方法を学習しました。 次の記事に進むことができます。

> [!div class="nextstepaction"]
> [Cassandra API アカウントからデータを照会する](cassandra-api-query-data.md)
