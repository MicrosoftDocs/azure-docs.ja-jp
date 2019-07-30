---
title: 'チュートリアル: Async Java SDK を使って Java アプリを構築し Azure Cosmos DB の SQL API アカウントを管理する'
description: このチュートリアルでは、Async Java アプリケーションを使用して、Azure Cosmos DB の SQL API アカウント内にデータを格納し、そのデータにアクセスする方法を示します。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 12/15/2018
ms.author: sngun
Customer intent: As a developer, I want to build a Java application with the Async Java SDK to access and manage Azure Cosmos DB resources so that customers can utilize the global distribution, elastic scaling, multi-master, and other capabilities offered by Azure Cosmos DB.
ms.openlocfilehash: c466c11cc51ca8d8efcf9ff4e86a9dbd2fd4930c
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67985652"
---
# <a name="tutorial-build-a-java-app-with-the-async-java-sdk-to-manage-data-stored-in-a-sql-api-account"></a>チュートリアル:Async Java SDK を使って Java アプリを構築し SQL API アカウントに格納されているデータを管理する

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.JS](sql-api-nodejs-get-started.md)
> 

開発者は、NoSQL ドキュメント データを使用するアプリケーションを持っていることがあります。 Azure Cosmos DB の SQL API アカウントを使用して、このドキュメント データを格納し、そのデータにアクセスすることができます。 このチュートリアルでは、Async Java SDK を使用して Java アプリケーションを構築し、ドキュメント データを格納し、そのデータにアクセスする方法について説明します。 

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * Azure Cosmos アカウントを作成し、アカウントに接続する
> * ソリューションを構成する
> * コレクションを作成する
> * JSON ドキュメントを作成する
> * コレクションをクエリする

## <a name="prerequisites"></a>前提条件

以下のリソースがそろっていることを確認してください。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 お持ちでない場合は、 [無料アカウント](https://azure.microsoft.com/free/)にサインアップしてください。 

* [Git](https://git-scm.com/downloads)。

* [Java Development Kit (JDK) 8 以降](https://aka.ms/azure-jdks)。

* [Maven](https://maven.apache.org/download.cgi)。

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB アカウントを作成する

次の手順を使用して、Azure Cosmos アカウントを作成します。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="GitClone"></a>GitHub リポジトリを複製する

[Azure Cosmos DB と Java の概要](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started)に関する GitHub リポジトリを複製します。 たとえば、ローカル ディレクトリで次のコマンドを実行し、サンプル プロジェクトをローカルで取得します。

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started.git

cd azure-cosmos-db-sql-api-async-java-getting-started
cd azure-cosmosdb-get-started
```

ディレクトリには `pom.xml` ファイルと、`Main.java` などの Java ソース コードを含む `src/main/java/com/microsoft/azure/cosmosdb/sample` フォルダーが含まれています。 プロジェクトには、Azure Cosmos DB の操作 (ドキュメントの作成やコレクション内のデータのクエリなど) に必要なコードが含まれています。 `pom.xml` ファイルには、[Maven の Azure Cosmos DB Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb) への依存関係が含まれています。

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-documentdb</artifactId>
  <version>2.0.0</version>
</dependency>
```

## <a id="Connect"></a>Azure Cosmos アカウントに接続する

次に、[Azure portal](https://portal.azure.com) に戻り、エンドポイントとプライマリ マスター キーを取得します。 Azure Cosmos DB のエンドポイントとプライマリ キーは、アプリケーションが接続先を認識し、Azure Cosmos DB がアプリケーションの接続を信頼するために必要です。 `AccountSettings.java` ファイルは、プライマリ キーと URI の値を保持します。 

Azure ポータルで Azure Cosmos アカウントに移動し、 **[キー]** をクリックします。 ポータルから URI とプライマリ キーをコピーし、`AccountSettings.java` ファイルに貼り付けます。 

```java
public class AccountSettings 
{
  // Replace MASTER_KEY and HOST with values from your Azure Cosmos account.
    
  // <!--[SuppressMessage("Microsoft.Security", "CS002:SecretInNextLine")]-->
  public static String MASTER_KEY = System.getProperty("ACCOUNT_KEY", 
          StringUtils.defaultString(StringUtils.trimToNull(
          System.getenv().get("ACCOUNT_KEY")), "<Fill your Azure Cosmos account key>"));

  public static String HOST = System.getProperty("ACCOUNT_HOST",
           StringUtils.defaultString(StringUtils.trimToNull(
           System.getenv().get("ACCOUNT_HOST")),"<Fill your Azure Cosmos DB URI>"));
}
```

![ポータルからキーを取得するスクリーンショット][keys]

## <a name="initialize-the-client-object"></a>クライアント オブジェクトを初期化する

"AccountSettings.java" ファイルで定義されているホスト URI とプライマリ キー値を使用して、クライアント オブジェクトを初期化します。

```java
client = new AsyncDocumentClient.Builder()
         .withServiceEndpoint(AccountSettings.HOST)
         .withMasterKey(AccountSettings.MASTER_KEY)
         .withConnectionPolicy(ConnectionPolicy.GetDefault())
         .withConsistencyLevel(ConsistencyLevel.Session)
         .build();
```

## <a id="CreateDatabase"></a>データベースを作成する

DocumentClient クラスの `createDatabaseIfNotExists()` メソッドを使用して、Azure Cosmos DB データベースを作成します。 データベースは、コレクションに分割された JSON ドキュメント ストレージの論理上のコンテナーです。

```java
private void createDatabaseIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if database " + databaseName + " exists.");

    String databaseLink = String.format("/dbs/%s", databaseName);

    Observable<ResourceResponse<Database>> databaseReadObs = client.readDatabase(databaseLink, null);

    Observable<ResourceResponse<Database>> databaseExistenceObs = databaseReadObs
                .doOnNext(x -> {System.out.println("database " + databaseName + " already exists.");}).onErrorResumeNext(e -> {
   // if the database doesn't already exists
   // readDatabase() will result in 404 error
   if (e instanceof DocumentClientException) 
   {
       DocumentClientException de = (DocumentClientException) e;
       // if database 
       if (de.getStatusCode() == 404) {
       // if the database doesn't exist, create it.
       System.out.println("database " + databaseName + " doesn't existed," + " creating it...");
       Database dbDefinition = new Database();
       dbDefinition.setId(databaseName);
       return client.createDatabase(dbDefinition, null);
     }
     }

    // some unexpected failure in reading database happened.
    // pass the error up.
    System.err.println("Reading database " + databaseName + " failed.");
        return Observable.error(e);     
    });

   // wait for completion
   databaseExistenceObs.toCompletable().await();

   System.out.println("Checking database " + databaseName + " completed!\n");
}
```

## <a id="CreateColl"></a>コレクションを作成する

DocumentClient クラスの `createDocumentCollectionIfNotExists()` メソッドを使用して、コレクションを作成することができます。 コレクションには、JSON ドキュメントのほか、関連する JavaScript アプリケーション ロジックが格納されます。

> [!WARNING]
> **createCollection** は、予約済みのスループットで新しいコレクションを作成します。これによって価格に影響があります。 詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/cosmos-db/)を参照してください。
> 
> 

```java
private void createDocumentCollectionIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if collection " + collectionName + " exists.");

    // query for a collection with a given id
    // if it exists nothing else to be done
    // if the collection doesn't exist, create it.

    String databaseLink = String.format("/dbs/%s", databaseName);

    // we know there is only single page of result (empty or with a match)
    client.queryCollections(databaseLink, new SqlQuerySpec("SELECT * FROM r where r.id = @id", 
            new SqlParameterCollection(new SqlParameter("@id", collectionName))), null).single() 
            .flatMap(page -> {
            if (page.getResults().isEmpty()) {
             // if there is no matching collection create the collection.
             DocumentCollection collection = new DocumentCollection();
             collection.setId(collectionName);
             System.out.println("Creating collection " + collectionName);

             return client.createCollection(databaseLink, collection, null);
            } 
            else {
              // collection already exists, nothing else to be done.
              System.out.println("Collection " + collectionName + "already exists");
              return Observable.empty();
            }
      }).toCompletable().await();

 System.out.println("Checking collection " + collectionName + " completed!\n");
    }
```

## <a id="CreateDoc"></a>JSON ドキュメントを作成する

DocumentClient クラスの createDocument メソッドを使用して、ドキュメントを作成します。 ドキュメントは、ユーザー定義の (ユーザーが自由に定義できる) JSON コンテンツです。 ここで 1 つ以上のドキュメントを挿入できます。 "src/main/java/com/microsoft/azure/cosmosdb/sample/Families.java" ファイルでは、ファミリ JSON ドキュメントが定義されています。 

```java
public static Family getJohnsonFamilyDocument() {
     Family andersenFamily = new Family();
     andersenFamily.setId("Johnson" + System.currentTimeMillis());
     andersenFamily.setLastName("Johnson");

      Parent parent1 = new Parent();
      parent1.setFirstName("John");

      Parent parent2 = new Parent();
      parent2.setFirstName("Lili");

      return andersenFamily;
    }
```

## <a id="Query"></a>Azure Cosmos DB リソースにクエリを実行する

Azure Cosmos DB では、各コレクションに格納された JSON ドキュメントに対する豊富なクエリがサポートされています。 次のサンプル コードでは、`queryDocuments` メソッドが含まれている SQL 構文を使用して Azure Cosmos DB のドキュメントにクエリを実行する方法を示します。

```java
private void executeSimpleQueryAsyncAndRegisterListenerForResult(CountDownLatch completionLatch) 
{
    // Set some common query options
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setMaxItemCount(100);
    queryOptions.setEnableCrossPartitionQuery(true);

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(collectionLink,
           "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", queryOptions);

    queryObservable.subscribe(queryResultPage -> {
            System.out.println("Got a page of query result with " +
            queryResultPage.getResults().size() + " document(s)"
            + " and request charge of " + queryResultPage.getRequestCharge());
    },
   // terminal error signal
        e -> {
          e.printStackTrace();
          completionLatch.countDown();
        },

   // terminal completion signal
         () -> {
            completionLatch.countDown();
         });
}
```

## <a id="Run"></a>Java コンソール アプリケーションを実行する

コンソールからアプリケーションを実行するには、プロジェクト フォルダーに移動し、Maven を使用してコンパイルします。

```bash
mvn package
```

`mvn package` を実行すると、Maven から最新の Azure Cosmos DB ライブラリがダウンロードされ、`GetStarted-0.0.1-SNAPSHOT.jar` が生成されます。 次に、次のコードを実行してアプリを実行します。

```bash
mvn exec:java -DACCOUNT_HOST=<YOUR_COSMOS_DB_HOSTNAME> -DACCOUNT_KEY= <YOUR_COSMOS_DB_MASTER_KEY>
```

これで、この NoSQL チュートリアルが完了し、実用的な Java コンソール アプリケーションが完成しました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、リソース グループ、Azure Cosmos アカウント、およびすべての関連リソースを削除できます。 これを行うには、仮想マシン用のリソース グループを選択し、 **[削除]** を選択した後、削除するリソース グループの名前を確認します。


## <a name="next-steps"></a>次の手順

このチュートリアルでは、Async Java SDK を使用して Java アプリを構築し、Azure Cosmos DB の SQL API データを管理する方法について説明しました。 次の記事に進むことができます。

> [!div class="nextstepaction"]
> [JavaScript SDK と Azure Cosmos DB を使用して Node.js コンソール アプリを構築する](sql-api-nodejs-get-started.md)

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
