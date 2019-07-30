---
title: 'NoSQL チュートリアル: Azure Cosmos DB Java SDK 用 SQL API'
description: Azure Cosmos DB 用 SQL API を使用してオンライン データベースと Java コンソール アプリケーションを作成する NoSQL チュートリアル。 Azure SQL は、JSON 用の NoSQL データベースです。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 12/22/2018
ms.author: sngun
ms.openlocfilehash: 55bdcf9847f2194f269b92aed830a66c79c4d337
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67985661"
---
# <a name="nosql-tutorial-build-a-sql-api-java-console-application"></a>NoSQL チュートリアル: SQL API Java コンソール アプリケーションの作成

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Azure Cosmos DB Java SDK 用 SQL API の NoSQL チュートリアルへようこそ。 このチュートリアルに従うことで、Azure Cosmos DB リソースを作成し、クエリするコンソール アプリケーションを準備することができます。

説明内容は次のとおりです。

* Azure Cosmos DB アカウントを作成し、アカウントに接続する
* Visual Studio ソリューションを構成する
* オンライン データベースを作成する
* コレクションを作成する
* JSON ドキュメントを作成する
* コレクションをクエリする
* JSON ドキュメントを作成する
* コレクションをクエリする
* ドキュメントを置換する
* ドキュメントを削除する
* データベースを削除する

それでは始めましょう。

## <a name="prerequisites"></a>前提条件
次のものを用意してください。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 お持ちでない場合は、 [無料アカウント](https://azure.microsoft.com/free/)にサインアップしてください。 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Git](https://git-scm.com/downloads)。
* [Java Development Kit (JDK) 7 以降](https://aka.ms/azure-jdks)。
* [Maven](https://maven.apache.org/download.cgi)。

## <a name="step-1-create-an-azure-cosmos-db-account"></a>手順 1: Azure Cosmos DB アカウントを作成する
それでは、Azure Cosmos DB アカウントを作成してみましょう。 使用するアカウントが既にある場合は、「[GitHub プロジェクトを複製する](#GitClone)」に進んでかまいません。 Azure Cosmos DB Emulator を使用する場合は、[DB Emulator](local-emulator.md) に関する記事に記載されている手順に従ってエミュレーターをセットアップし、「[GitHub プロジェクトを複製する](#GitClone)」に進んでください。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="GitClone"></a>手順 2: GitHub プロジェクトを複製する
最初に、[Azure Cosmos DB と Java の概要](https://github.com/Azure-Samples/documentdb-java-getting-started)に関する GitHub リポジトリを複製します。 たとえば、ローカル ディレクトリで次のコマンドを実行し、サンプル プロジェクトをローカルに取得します。

    git clone git@github.com:Azure-Samples/azure-cosmos-db-documentdb-java-getting-started.git

    cd azure-cosmos-db-documentdb-java-getting-started

ディレクトリには、プロジェクトの `pom.xml` のほか、Java ソース コードが含まれた `src` フォルダーがあります。このフォルダーには、ドキュメントの作成やコレクション内のデータのクエリなど、Azure Cosmos DB での単純な操作の実行方法を示す `Program.java` が含まれています。 `pom.xml` には、[Azure Cosmos DB Java SDK on Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb) の依存関係が含まれています。

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-documentdb</artifactId>
        <version>LATEST</version>
    </dependency>

## <a id="Connect"></a>手順 3: Azure Cosmos DB アカウントに接続する
次に、[Azure portal](https://portal.azure.com) に戻り、エンドポイントとプライマリ マスター キーを取得します。 Azure Cosmos DB のエンドポイントとプライマリ キーは、アプリケーションが接続先を認識し、Azure Cosmos DB がアプリケーションの接続を信頼するために必要です。

Azure Portal で Azure Cosmos DB アカウントに移動し、 **[キー]** をクリックします。 ポータルから URI をコピーし、Program.java ファイルの `https://FILLME.documents.azure.com` に貼り付けます。 次に、ポータルからプライマリ キーをコピーし、 `FILLME`に貼り付けます。

    this.client = new DocumentClient(
        "https://FILLME.documents.azure.com",
        "FILLME"
        , new ConnectionPolicy(),
        ConsistencyLevel.Session);

![Java コンソール アプリケーションを作成するために NoSQL チュートリアルで使用される Azure portal のスクリーンショット。 アクティブなハブが強調表示され、[Azure Cosmos DB account]\(Azure Cosmos DB アカウント\) ブレードで [キー] ボタンが強調表示され、[キー] ブレードで URI 値、プライマリ キー値、およびセカンダリ キーの値が強調表示されている Azure Cosmos DB アカウントを示します][keys]

## <a name="step-4-create-a-database"></a>手順 4: データベースを作成する
Azure Cosmos DB [データベース](databases-containers-items.md#azure-cosmos-databases)は、**DocumentClient** クラスの [createDatabase](/java/api/com.microsoft.azure.documentdb.documentclient.createdatabase) メソッドを使用して作成できます。 データベースは、コレクションに分割された JSON ドキュメント ストレージの論理上のコンテナーです。

    Database database = new Database();
    database.setId("familydb");
    this.client.createDatabase(database, null);

## <a id="CreateColl"></a>手順 5: コレクションの作成
> [!WARNING]
> **createCollection** は、予約済みのスループットで新しいコレクションを作成します。これによって価格に影響があります。 詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/cosmos-db/)を参照してください。
> 
> 

コレクションは、**DocumentClient** クラスの [createCollection](/java/api/com.microsoft.azure.documentdb.documentclient.createcollection) メソッドを使用して作成できます。 コレクションには、JSON ドキュメントのほか、関連する JavaScript アプリケーション ロジックが格納されます。


    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId("familycoll");

    // Azure Cosmos DB collections can be reserved with throughput specified in request units/second. 
    // Here we create a collection with 400 RU/s.
    RequestOptions requestOptions = new RequestOptions();
    requestOptions.setOfferThroughput(400);

    this.client.createCollection("/dbs/familydb", collectionInfo, requestOptions);

## <a id="CreateDoc"></a>手順 6: JSON ドキュメントの作成
ドキュメントは、**DocumentClient** クラスの [createDocument](/java/api/com.microsoft.azure.documentdb.documentclient.createdocument) メソッドを使用して作成できます。 ドキュメントは、ユーザー定義の (ユーザーが自由に定義できる) JSON コンテンツです。 ここで 1 つ以上のドキュメントを挿入できます。 データベースに保存するデータが既にある場合には、Azure Cosmos DB の[データ移行ツール](import-data.md)を使用して、データをデータベースにインポートできます。

    // Insert your Java objects as documents 
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen")

    // More initialization skipped for brevity. You can have nested references
    andersenFamily.setParents(new Parent[] { parent1, parent2 });
    andersenFamily.setDistrict("WA5");
    Address address = new Address();
    address.setCity("Seattle");
    address.setCounty("King");
    address.setState("WA");

    andersenFamily.setAddress(address);
    andersenFamily.setRegistered(true);

    this.client.createDocument("/dbs/familydb/colls/familycoll", family, new RequestOptions(), true);

![Java コンソール アプリケーションを作成するために NoSQL チュートリアルで使用されるアカウント、オンライン データベース、コレクション、およびドキュメントの間の階層関係を示す図](./media/sql-api-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>手順 7: Azure Cosmos DB リソースを照会する
Azure Cosmos DB では、各コレクションに格納された JSON ドキュメントに対する豊富な[クエリ](how-to-sql-query.md)がサポートされています。  次のサンプル コードは、[queryDocuments](/java/api/com.microsoft.azure.documentdb.documentclient.querydocuments) メソッドが含まれている SQL 構文を使用して Azure Cosmos DB のドキュメントを照会する方法を示します。

    FeedResponse<Document> queryResults = this.client.queryDocuments(
        "/dbs/familydb/colls/familycoll",
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", 
        null);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }

## <a id="ReplaceDocument"></a>手順 8: JSON ドキュメントを置換する
Azure Cosmos DB では、[replaceDocument](/java/api/com.microsoft.azure.documentdb.documentclient.replacedocument) メソッドを使用した JSON ドキュメントの更新がサポートされています。

    // Update a property
    andersenFamily.Children[0].Grade = 6;

    this.client.replaceDocument(
        "/dbs/familydb/colls/familycoll/docs/Andersen.1", 
        andersenFamily,
        null);

## <a id="DeleteDocument"></a>手順 9: JSON ドキュメントを削除する
同様に、Azure Cosmos DB では、[deleteDocument](/java/api/com.microsoft.azure.documentdb.documentclient.deletedocument) メソッドを使用した JSON ドキュメントの削除がサポートされています。  

    this.client.delete("/dbs/familydb/colls/familycoll/docs/Andersen.1", null);

## <a id="DeleteDatabase"></a>手順 10: データベースを削除する
作成したデータベースを削除すると、データベースとすべての子リソース (コレクション、ドキュメントなど) が削除されます。

    this.client.deleteDatabase("/dbs/familydb", null);

## <a id="Run"></a>手順 11: Java コンソール アプリケーションの全体的な実行の流れ
コンソールからアプリケーションを実行するには、プロジェクト フォルダーに移動し、Maven を使用してコンパイルします。
    
    mvn package

`mvn package` を実行すると、Maven から最新の Azure Cosmos DB ライブラリがダウンロードされ、`GetStarted-0.0.1-SNAPSHOT.jar` が生成されます。 次に、次のコードを実行してアプリを実行します。

    mvn exec:java -D exec.mainClass=GetStarted.Program

お疲れさまでした。 この NoSQL チュートリアルを終え、実用的な Java コンソール アプリケーションが完成しました。

## <a name="next-steps"></a>次の手順
* Java Web アプリケーションのチュートリアルが必要な場合、 [Azure Cosmos DB を使用した Java Web アプリケーションの作成](sql-api-java-application.md)に関するページを参照してください。
* [Azure Cosmos DB アカウントを監視する](monitor-accounts.md)方法を学習します。
* [クエリのプレイ グラウンド](https://www.documentdb.com/sql/demo)でサンプル データセットに対してクエリを実行します。

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
