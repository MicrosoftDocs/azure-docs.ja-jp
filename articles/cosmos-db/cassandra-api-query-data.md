---
title: チュートリアル:Azure Cosmos DB の Cassandra API アカウントに対するデータのクエリを実行する
description: このチュートリアルでは、Java アプリケーションを使用して、Azure Cosmos DB の Cassandra API アカウントに対するユーザー データのクエリを実行する方法について説明します。
ms.service: cosmos-db
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: 28bb69dc21702a7c98755dc07389ea4147848f7a
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2020
ms.locfileid: "82853039"
---
# <a name="tutorial-query-data-from-a-cassandra-api-account-in-azure-cosmos-db"></a>チュートリアル:Azure Cosmos DB の Cassandra API アカウントに対するデータのクエリを実行する

開発者は、キー/値ペアを使用するアプリケーションを持っていることがあります。 Azure Cosmos DB の Cassandra API アカウントを使用して、キー/値データを格納してクエリを実行できます。 このチュートリアルでは、Java アプリケーションを使用して、Azure Cosmos DB の Cassandra API アカウントに対するユーザー データのクエリを実行する方法について説明します。 この Java アプリケーションでは、[Java ドライバー](https://github.com/datastax/java-driver)を使用して、ユーザー ID、ユーザー名、ユーザー所在地などのユーザー データのクエリを実行します。 

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * Cassandra テーブルのデータのクエリを実行する
> * アプリを実行する

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

* この記事では、マルチパートのチュートリアルの一部です｡ 開始する前に、必ず前の手順を実行して Cassandra API アカウント、キースペース、テーブルを作成し、[サンプル データをテーブルに読み込んで](cassandra-api-load-data.md)ください。 

## <a name="query-data"></a>クエリ データ

以下の手順を使用して、Cassandra API アカウントのデータに対するクエリを実行します。

1. `src\main\java\com\azure\cosmosdb\cassandra` フォルダーにある `UserRepository.java` ファイルを開きます。 次のコード ブロックを末尾に追加します。 このコードは、次の 3 つの方法を提供します。 

   * データベース内のすべてのユーザーを照会する
   * ユーザー ID でフィルター処理された特定のユーザーを照会する
   * テーブルを削除する

   ```java
   /**
   * Select all rows from user table
   */
   public void selectAllUsers() {

     final String query = "SELECT * FROM uprofile.user";
     List<Row> rows = session.execute(query).all();

     for (Row row : rows) {
        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
     }
   }

   /**
   * Select a row from user table
   *
   * @param id user_id
   */
   public void selectUser(int id) {
      final String query = "SELECT * FROM uprofile.user where user_id = 3";
      Row row = session.execute(query).one();

      LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
   }

   /**
   * Delete user table.
   */
   public void deleteTable() {
     final String query = "DROP TABLE IF EXISTS uprofile.user";
     session.execute(query);
   }
   ```

2. `src\main\java\com\azure\cosmosdb\cassandra` フォルダーにある `UserProfile.java` ファイルを開きます。 このクラスには、createKeyspace および createTable (以前に定義したデータの挿入メソッド) を呼び出す main メソッドが含まれています。 すべてのユーザーまたは特定のユーザーに対してクエリを実行する次のコードを末尾に追加します。

   ```java
   LOGGER.info("Select all users");
   repository.selectAllUsers();

   LOGGER.info("Select a user by id (3)");
   repository.selectUser(3);

   LOGGER.info("Delete the users profile table");
   repository.deleteTable();
   ```

## <a name="run-the-java-app"></a>Java アプリを実行する
1. コマンド プロンプトまたはターミナル ウィンドウを開きます。 次のコード ブロックを貼り付けます。 

   このコードは、プロジェクトを作成したフォルダー パスにディレクトリを変更 (cd) します。 次に、`mvn clean install` コマンドを実行して、ターゲット フォルダー内に `cosmosdb-cassandra-examples.jar` ファイルを生成します。 最後に、Java アプリケーションを実行します。

   ```bash
   cd "cassandra-demo"
   
   mvn clean install
   
   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
   ```

2. 次に Azure portal で**データ エクスプローラー**を開き、ユーザー テーブルが削除されていることを確認します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、リソース グループ、Azure Cosmos アカウント、およびすべての関連リソースを削除できます。 これを行うには、仮想マシン用のリソース グループを選択し、 **[削除]** を選択した後、削除するリソース グループの名前を確認します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Cosmos DB の Cassandra API アカウントからデータのクエリを実行する方法を学びました。 次の記事に進むことができます。

> [!div class="nextstepaction"]
> [データを Cassandra API アカウントに移行する](cassandra-import-data.md)


