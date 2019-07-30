---
title: Azure Cosmos DB の MongoDB 用 API と Java SDK を使用してコンソール アプリを構築する
description: Azure Cosmos DB の MongoDB 用 API を使用して接続とクエリを行うことができる Java のコード サンプルについて説明します。
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: java
ms.topic: quickstart
ms.date: 12/26/2018
ms.author: rimman
ms.openlocfilehash: 1e1cfe7f0e3af72de819ed09dcd0a6d190f239ab
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839809"
---
# <a name="quickstart-build-a-web-app-using-azure-cosmos-dbs-api-for-mongodb-and-java-sdk"></a>クイック スタート:Azure Cosmos DB の MongoDB 用 API と Java SDK を使用して Web アプリを構築する

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Azure Cosmos DB、Microsoft のグローバルに配布されるマルチモデル データベース サービスです。 Cosmos DB の中核であるグローバル配布と水平スケーリングの機能を利用して、ドキュメント、キー/値、グラフの各データベースの作成とクエリをすばやく行うことができます。 

このクイック スタートでは、[Azure Cosmos DB の MongoDB 用 API](mongodb-introduction.md) を使用して Cosmos アカウントを作成する方法を示します。 さらに、[MongoDB Java ドライバー](https://docs.mongodb.com/ecosystem/drivers/java/)を使用してコンソール アプリを構築し、デプロイします。 

## <a name="prerequisites"></a>前提条件

このサンプルを実行する前に、以下の前提条件を満たしている必要があります。
* [Azure 用の JDK および Azure Stack JDK バージョン 8 をインストールします](https://aka.ms/azure-jdks)
* Maven (Maven がない場合は、`apt-get install maven` を実行します)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

## <a name="create-a-database-account"></a>データベース アカウントの作成

[!INCLUDE [mongodb-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="add-a-collection"></a>コレクションの追加

新しいデータベースに **db**、新しいコレクションに **coll** という名前を付けます。

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)] 

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

それでは、GitHub からアプリを複製し、接続文字列を設定して実行しましょう。 プログラムでデータを処理することが非常に簡単であることがわかります。 

1. コマンド プロンプトを開いて git-samples という名前の新しいフォルダーを作成し、コマンド プロンプトを閉じます。

    ```bash
    md "C:\git-samples"
    ```

2. git bash などの git ターミナル ウィンドウを開いて、`cd` コマンドを使用して、サンプル アプリをインストールする新しいフォルダーに変更します。

    ```bash
    cd "C:\git-samples"
    ```

3. 次のコマンドを実行して、サンプル レポジトリを複製します。 このコマンドは、コンピューター上にサンプル アプリのコピーを作成します。

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-getting-started.git
    ```

3. 次に、使い慣れたエディターでコードを開きます。 

## <a name="review-the-code"></a>コードの確認

この手順は省略可能です。 コード内のデータベース リソースの作成方法に関心がある場合は、次のスニペットを確認できます。 関心がない場合は、「[接続文字列の更新](#update-your-connection-string)」に進んでください。 

次のスニペットはすべて Program.java ファイルからのものです。

* DocumentClient が初期化されます。

    ```java
    MongoClientURI uri = new MongoClientURI("FILLME");`

    MongoClient mongoClient = new MongoClient(uri);            
    ```

* 新しいデータベースとコレクションが作成されます。

    ```java
    MongoDatabase database = mongoClient.getDatabase("db");

    MongoCollection<Document> collection = database.getCollection("coll");
    ```

* `MongoCollection.insertOne` を使用していくつかのドキュメントが挿入されます。

    ```java
    Document document = new Document("fruit", "apple")
    collection.insertOne(document);
    ```

* `MongoCollection.find` を使用していくつかのクエリが実行されます。

    ```java
    Document queryResult = collection.find(Filters.eq("fruit", "apple")).first();
    System.out.println(queryResult.toJson());       
    ```

## <a name="update-your-connection-string"></a>接続文字列を更新する

ここで Azure Portal に戻り、接続文字列情報を取得し、アプリにコピーします。

1. アカウントから **[クイック スタート]** を選択し、Java を選択して、接続文字列をクリップボードにコピーします。

2. `Program.java` ファイルを開き、MongoClientURI コンストラクターの引数を接続文字列で置き換えます。 これで、Azure Cosmos DB と通信するために必要なすべての情報でアプリを更新しました。 
    
## <a name="run-the-console-app"></a>コンソール アプリの実行

1. ターミナルで `mvn package` を実行し、必要な npm モジュールをインストールします。

2. ターミナルで `mvn exec:java -D exec.mainClass=GetStarted.Program` を実行し、Java アプリケーションを起動します。

これで、[Robomongo](mongodb-robomongo.md) / [Studio 3T](mongodb-mongochef.md) を使用して、この新しいデータをクエリ、変更、および操作できるようになりました。

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Cosmos アカウントを作成し、コレクションを作成して、コンソール アプリを実行する方法を学習しました。 これで、Cosmos データベースに追加のデータをインポートできます。

> [!div class="nextstepaction"]
> [MongoDB データを Azure Cosmos DB にインポートする](mongodb-migrate.md)
