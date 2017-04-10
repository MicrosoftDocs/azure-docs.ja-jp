---
title: "NoSQL チュートリアル: Azure DocumentDB Java SDK | Microsoft Docs"
description: "DocumentDB Java SDK を使用してオンライン データベースと Java コンソール アプリケーションを作成する NoSQL チュートリアル。 Azure DocumentDB は、JSON 用の NoSQL データベースです。"
keywords: "NoSQL チュートリアル, オンライン データベース, Java コンソール アプリケーション"
services: documentdb
documentationcenter: Java
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: 75a9efa1-7edd-4fed-9882-c0177274cbb2
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: hero-article
ms.date: 01/05/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: da7907ffc515ea2e3040075c93bcd53840cf3ff5
ms.lasthandoff: 03/29/2017


---
# <a name="nosql-tutorial-build-a-documentdb-java-console-application"></a>NoSQL チュートリアル: DocumentDB Java コンソール アプリケーションの作成
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [MongoDB 用 Node.js](documentdb-mongodb-samples.md)
> * [Node.JS](documentdb-nodejs-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 

Azure DocumentDB Java SDK の NoSQL チュートリアルへようこそ。 このチュートリアルに従うことで、DocumentDB リソースを作成し、クエリするコンソール アプリケーションを準備することができます。

説明内容は次のとおりです。

* DocumentDB アカウントを作成して接続する
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

* アクティブな Azure アカウント。 お持ちでない場合は、 [無料アカウント](https://azure.microsoft.com/free/)にサインアップしてください。 また、このチュートリアルには、[Azure DocumentDB Emulator](documentdb-nosql-local-emulator.md) を使用することもできます。
* [Git](https://git-scm.com/downloads)
* [Java Development Kit (JDK) 7 以降](http://www.oracle.com/technetwork/java/javase/downloads/index.html)。
* [Maven](http://maven.apache.org/download.cgi)。

## <a name="step-1-create-a-documentdb-account"></a>手順 1: DocumentDB アカウントを作成する
DocumentDB アカウントを作成しましょう。 使用するアカウントが既にある場合は、「[GitHub プロジェクトを複製する](#GitClone)」に進んでかまいません。 DocumentDB Emulator を使用する場合は、[Azure DocumentDB Emulator](documentdb-nosql-local-emulator.md) に関する記事に記載されている手順に従って、エミュレーターをセットアップし、「[GitHub プロジェクトを複製する](#GitClone)」に進んでください。

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a id="GitClone"></a>手順 2: GitHub プロジェクトを複製する
最初に、[DocumentDB と Java の概要](https://github.com/Azure-Samples/documentdb-java-getting-started)に関するページの GitHub リポジトリを複製します。 たとえば、ローカル ディレクトリで次のコマンドを実行し、サンプル プロジェクトをローカルに取得します。

    git clone git@github.com:Azure-Samples/documentdb-java-getting-started.git

    cd documentdb-java-getting-started

ディレクトリには、プロジェクトの `pom.xml` のほか、Java ソース コードが含まれた `src` フォルダーがあります。このフォルダーには、ドキュメントの作成やコレクション内のデータのクエリなど、Azure DocumentDB での単純な操作の実行方法を示す `Program.java` が含まれています。 `pom.xml` には、[DocumentDB Java SDK on Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb) の依存関係が含まれています。

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-documentdb</artifactId>
        <version>LATEST</version>
    </dependency>

## <a id="Connect"></a>手順 3: DocumentDB アカウントに接続する
次に、[Azure Portal](https://portal.azure.com) に戻り、エンドポイントとプライマリ マスター キーを取得します。 DocumentDB エンドポイントとプライマリ キーは、アプリケーションが接続先を認識し、DocumentDB がアプリケーションの接続を信頼するために必要です。

Azure Portal で DocumentDB アカウントに移動し、 **[キー]**をクリックします。 ポータルから URI をコピーし、Program.java ファイルの `<your endpoint URI>` に貼り付けます。 次に、ポータルからプライマリ キーをコピーし、 `<your key>`に貼り付けます。

    this.client = new DocumentClient(
        "<your endpoint URI>",
        "<your key>"
        , new ConnectionPolicy(),
        ConsistencyLevel.Session);

![Java コンソール アプリケーションを作成するために NoSQL チュートリアルで使用される Azure Portal のスクリーン ショット。 アクティブなハブが強調表示され、[DocumentDB アカウント] ブレードで [キー] ボタンが強調表示され、[キー] ブレードで URI 値、プライマリ キー値、およびセカンダリ キーの値が強調表示されている DocumentDB アカウントを示します][keys]

## <a name="step-4-create-a-database"></a>手順 4: データベースを作成する
DocumentDB [データベース](documentdb-resources.md#databases)は、**DocumentClient** クラスの [createDatabase](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#createDatabase-com.microsoft.azure.documentdb.Database-com.microsoft.azure.documentdb.RequestOptions-) メソッドを使用して作成できます。 データベースは、コレクションに分割された JSON ドキュメント ストレージの論理上のコンテナーです。

    Database database = new Database();
    database.setId("familydb");
    this.client.createDatabase(database, null);

## <a id="CreateColl"></a>手順 5: コレクションを作成する
> [!WARNING]
> **createCollection** は、予約済みのスループットで新しいコレクションを作成します。これによって価格に影響があります。 詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/documentdb/)を参照してください。
> 
> 

[コレクション](documentdb-resources.md#collections)は、**DocumentClient** クラスの [createCollection](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#createCollection-java.lang.String-com.microsoft.azure.documentdb.DocumentCollection-com.microsoft.azure.documentdb.RequestOptions-) メソッドを使用して作成できます。 コレクションには、JSON ドキュメントのほか、関連する JavaScript アプリケーション ロジックが格納されます。


    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId("familycoll");

    // DocumentDB collections can be reserved with throughput specified in request units/second. 
    // Here we create a collection with 400 RU/s.
    RequestOptions requestOptions = new RequestOptions();
    requestOptions.setOfferThroughput(400);

    this.client.createCollection("/dbs/familydb", collectionInfo, requestOptions);

## <a id="CreateDoc"></a>手順 6: JSON ドキュメントを作成する
[ドキュメント](documentdb-resources.md#documents)は、**DocumentClient** クラスの [createDocument](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#createDocument-java.lang.String-java.lang.Object-com.microsoft.azure.documentdb.RequestOptions-boolean-) メソッドを使用して作成できます。 ドキュメントは、ユーザー定義の (ユーザーが自由に定義できる) JSON コンテンツです。 ここで 1 つ以上のドキュメントを挿入できます。 データベースに保存するデータが既にある場合には、DocumentDB の[データ移行ツール](documentdb-import-data.md)を使用して、データをデータベースにインポートできます。

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

![Java コンソール アプリケーションを作成するために NoSQL チュートリアルで使用されるアカウント、オンライン データベース、コレクション、およびドキュメントの間の階層関係を示す図](./media/documentdb-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>手順 7: DocumentDB リソースをクエリする
DocumentDB では、各コレクションに格納された JSON ドキュメントに対するリッチ [クエリ](documentdb-sql-query.md)をサポートしています。  次のサンプル コードは、[queryDocuments](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#queryDocuments-java.lang.String-com.microsoft.azure.documentdb.SqlQuerySpec-com.microsoft.azure.documentdb.FeedOptions-) メソッドが含まれた SQL 構文を使用して DocumentDB のドキュメントを照会する方法を示します。

    FeedResponse<Document> queryResults = this.client.queryDocuments(
        "/dbs/familydb/colls/familycoll",
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", 
        null);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }

## <a id="ReplaceDocument"></a>手順 8: JSON ドキュメントを置換する
DocumentDB では、[replaceDocument](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#replaceDocument-com.microsoft.azure.documentdb.Document-com.microsoft.azure.documentdb.RequestOptions-) メソッドを使用した JSON ドキュメントの更新がサポートされています。

    // Update a property
    andersenFamily.Children[0].Grade = 6;

    this.client.replaceDocument(
        "/dbs/familydb/colls/familycoll/docs/Andersen.1", 
        andersenFamily,
        null);

## <a id="DeleteDocument"></a>手順 9: JSON ドキュメントを削除する
同様に、DocumentDB では、[deleteDocument](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#deleteDocument-java.lang.String-com.microsoft.azure.documentdb.RequestOptions-) メソッドを使用した JSON ドキュメントの削除がサポートされています。  

    this.client.delete("/dbs/familydb/colls/familycoll/docs/Andersen.1", null);

## <a id="DeleteDatabase"></a>手順 10: データベースを削除する
作成したデータベースを削除すると、データベースとすべての子リソース (コレクション、ドキュメントなど) が削除されます。

    this.client.deleteDatabase("/dbs/familydb", null);

## <a id="Run"></a>手順 11: Java コンソール アプリケーションの全体的な実行の流れ
コンソールからアプリケーションを実行するには、最初に Maven を使用してコンパイルを行います。
    
    mvn package

`mvn package` を実行すると、Maven から最新の DocumentDB ライブラリがダウンロードされ、`GetStarted-0.0.1-SNAPSHOT.jar` が生成されます。 次に、次のコードを実行してアプリを実行します。

    mvn exec:java -D exec.mainClass=GetStarted.Program

お疲れさまでした。 この NoSQL チュートリアルを終え、実用的な Java コンソール アプリケーションが完成しました。

## <a name="next-steps"></a>次のステップ
* Java Web アプリケーションのチュートリアルが必要な場合、 [DocumentDB を使用した Java Web アプリケーションの作成](documentdb-java-application.md)に関するページを参照してください。
* [DocumentDB アカウントを監視する](documentdb-monitor-accounts.md)方法について学習します。
* [クエリのプレイ グラウンド](https://www.documentdb.com/sql/demo)でサンプル データセットに対してクエリを実行します。
* プログラミング モデルの詳細については、 [DocumentDB のドキュメントに関するページ](https://azure.microsoft.com/documentation/services/documentdb/)の「開発」セクションを参照してください。

[documentdb-create-account]: documentdb-create-account.md
[keys]: media/documentdb-get-started/nosql-tutorial-keys.png

