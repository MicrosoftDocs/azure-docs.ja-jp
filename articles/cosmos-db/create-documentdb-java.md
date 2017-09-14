---
title: "Azure Cosmos DB ドキュメント データベースを Java で作成する | Microsoft Docs | Microsoft Docs'"
description: "Azure Cosmos DB DocumentDB API への接続とクエリに使用できる Java コード サンプルについて説明します。"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 89ea62bb-c620-46d5-baa0-eefd9888557c
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: quickstart
ms.date: 08/02/2017
ms.author: mimig
ms.translationtype: HT
ms.sourcegitcommit: b6c65c53d96f4adb8719c27ed270e973b5a7ff23
ms.openlocfilehash: df1a25d703a7b8082bdabb4f7d593cb005d416fe
ms.contentlocale: ja-jp
ms.lasthandoff: 08/17/2017

---
# <a name="azure-cosmos-db-create-a-document-database-using-java-and-the-azure-portal"></a>Azure Cosmos DB: ドキュメント データベースを Java と Azure Portal で作成する

Azure Cosmos DB は、Microsoft のグローバルに分散されたマルチモデル データベース サービスです。 Azure Cosmos DB の中核をなすグローバルな分散と水平方向のスケール機能を利用して、ドキュメント、キー/値、およびグラフ データベースをすばやく作成およびクエリできます。 

このクイックスタートでは、Azure Cosmos DB 用の Azure Portal ツールを使ってドキュメント データベースを作成します。 また、[DocumentDB Java API](documentdb-sdk-java.md) を使ってすばやく Java コンソール アプリを作成する方法も紹介します。 このクイックスタートの手順は、Java を実行できる任意のオペレーティング システムで使用できます。 このクイックスタートを完了すると、UI とプログラムのどちらか好きな方法で、ドキュメント データベース リソースの作成と変更を行うことができるようになります。

## <a name="prerequisites"></a>前提条件

* [Java Development Kit (JDK) 1.7 以降](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
    * Ubuntu で `apt-get install default-jdk` を実行して JDK をインストールします。
    * 必ず、JDK のインストール先フォルダーを指すように JAVA_HOME 環境変数を設定してください。
* [Maven](http://maven.apache.org/) バイナリ アーカイブの[ダウンロード](http://maven.apache.org/download.cgi)と[インストール](http://maven.apache.org/install.html)
    * Ubuntu で `apt-get install maven` を実行して Maven をインストールします。
* [Git](https://www.git-scm.com/)
    * Ubuntu で `sudo apt-get install git` を実行して Git をインストールします。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-a-database-account"></a>How to create a DocumentDB account (DocumentDB アカウントの作成方法)

ドキュメント データベースを作成するには、Azure Cosmos DB を含んだ SQL (DocumentDB) データベース アカウントを事前に作成しておく必要があります。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>コレクションの追加

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>サンプル データの追加

これで、データ エクスプローラーを使用して、新しいコレクションにデータを追加できます。

1. データ エクスプローラーで新しいデータベースが [コレクション] ウィンドウに表示されます。 **[タスク]** データベースを展開し、**[項目]** コレクションを展開して、**[ドキュメント]** をクリックし、**[新しいドキュメント]** をクリックします。 

   ![Azure Portal のデータ エクスプローラーで新しいドキュメントを作成する](./media/create-documentdb-dotnet/azure-cosmosdb-data-explorer-new-document.png)
  
2. ここで、次の構造のドキュメントをコレクションに追加します。

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

3. json を **[ドキュメント]** タブに追加したら、**[保存]** をクリックします。

    ![json データをコピーし、Azure Portal のデータ エクスプローラーで [保存] をクリックします。](./media/create-documentdb-dotnet/azure-cosmosdb-data-explorer-save-document.png)

4.  もう 1 つドキュメントを作成して保存します。`id` プロパティには一意の値を挿入し、その他のプロパティについては適宜変更してください。 Azure Cosmos DB では、データにスキーマを課さないため、新しいドキュメントは必要な任意の構造にすることができます。

     これでデータ エクスプローラーで **[フィルターの編集]** ボタンと **[フィルターの適用]** ボタンをクリックすることにより、クエリを使ってデータを取得することができます。 既定では、データ エクスプローラーにより `SELECT * FROM c` を使用してコレクション内のすべてのドキュメントが取得されますが、[SQL クエリ](documentdb-sql-query.md)を `SELECT * FROM c ORDER BY c._ts DESC` のように変更すると、すべてのドキュメントをそのタイムスタンプの降順で取得できます。 
 
     さらに、データ エクスプローラーを使用して、ストアド プロシージャ、UDF、トリガーを作成し、サーバー側ビジネス ロジックを実行できるほか、スループットをスケールすることもできます。 データ エクスプローラーでは、API で使用可能な、組み込みのプログラムによるデータ アクセスがすべて公開されていますが、Azure Portal でデータに簡単にアクセスできます。

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

次は、コードを使った作業に移りましょう。 GitHub から DocumentDB API アプリを複製し、接続文字列を設定して実行します。 プログラムでデータを処理することが非常に簡単であることがわかります。 

1. git ターミナル ウィンドウ (git bash など) を開き、`CD` を実行して作業ディレクトリに移動します。  

2. 次のコマンドを実行して、サンプル レポジトリを複製します。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-documentdb-java-getting-started.git
    ```

## <a name="review-the-code"></a>コードの確認

アプリで何が行われているかを簡単に確認してみましょう。 \src\GetStarted フォルダーから `Program.java` ファイルを開き、Azure Cosmos DB リソースを作成する以下のコード行を探します。 

* `DocumentClient` が初期化されます。

    ```java
    this.client = new DocumentClient("https://FILLME.documents.azure.com",
            "FILLME", 
            new ConnectionPolicy(),
            ConsistencyLevel.Session);
    ```

* 新しいデータベースが作成されます。

    ```java
    Database database = new Database();
    database.setId(databaseName);
    
    this.client.createDatabase(database, null);
    ```

* 新しいコレクションが作成されます。

    ```java
    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId(collectionName);

    ...

    this.client.createCollection(databaseLink, collectionInfo, requestOptions);
    ```

* いくつかのドキュメントが作成されます。

    ```java
    // Any Java object within your code can be serialized into JSON and written to Azure Cosmos DB
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen");
    // More properties

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    this.client.createDocument(collectionLink, family, new RequestOptions(), true);
    ```

* JSON に対する SQL クエリが実行されます。

    ```java
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setPageSize(-1);
    queryOptions.setEnableCrossPartitionQuery(true);

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    FeedResponse<Document> queryResults = this.client.queryDocuments(
        collectionLink,
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", queryOptions);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }
    ```    

## <a name="update-your-connection-string"></a>接続文字列の更新

ここで Azure Portal に戻り、接続文字列情報を取得し、アプリにコピーします。 これでアプリが、ホストされているデータベースと通信できるようになります。

1. [Azure Portal](http://portal.azure.com/) で、Azure Cosmos DB アカウントの左のナビゲーションから、**[キー]** をクリックし、**[読み取り/書き込みキー]** をクリックします。 次の手順では、画面右側のコピー ボタンを使用して、URI とプライマリ キーを `Program.java` ファイルにコピーします。

    ![Azure Portal の [キー] ブレードでアクセス キーを表示およびコピーする](./media/create-documentdb-dotnet/keys.png)

2. 開いている `Program.java` ファイルに、ポータルから URI 値をコピーし (コピー ボタンを使用して)、`Program.java` の DocumentClient コンストラクターの endpoint の値に設定します。 

    `"https://FILLME.documents.azure.com"`

4. 次に、ポータルからプライマリ キーの値をコピーし、DocumentClient コンストラクターの第 2 パラメーターとなる "FILLME" に貼り付けます。 これで、Azure Cosmos DB と通信するために必要なすべての情報でアプリを更新しました。 
    
## <a name="run-the-app"></a>アプリの実行

1. git ターミナル ウィンドウで、azure-cosmos-db-documentdb-java-getting-started フォルダーに `cd` で移動します。

2. git ターミナル ウィンドウで「`mvn package`」と入力して、必要な Java パッケージをインストールします。

3. git ターミナル ウィンドウで `mvn exec:java -D exec.mainClass=GetStarted.Program` を実行して、Java アプリケーションを起動します。

    ターミナル ウィンドウに、FamilyDB データベースが作成されたことを示す通知が表示され、キーを押して続行するよう促されます。 キーを押してデータベースを作成した後、データ エクスプローラーに切り替えると、FamilyDB データベースが追加されていることがわかります。 キーを繰り返し押して、コレクションとドキュメントを作成した後、クエリを実行します。 プロジェクトが完了すると、アカウントからリソースが削除されます。 

    ![Azure Portal の [キー] ブレードでアクセス キーを表示およびコピーする](./media/create-documentdb-java/console-output.png)


## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このアプリの使用を続けない場合は、以下の手順に従い、Azure Portal でこのクイック スタートで作成したすべてのリソースを削除してください。

1. Azure Portal の左側のメニューで、**[リソース グループ]** をクリックし、作成したリソースの名前をクリックします。 
2. リソース グループのページで **[削除]** をクリックし、削除するリソースの名前をテキスト ボックスに入力してから **[削除]** をクリックします。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、データ エクスプローラーを使って Azure Cosmos DB アカウント、ドキュメント データベース、コレクションを作成する方法について説明しました。また、同じことを行うアプリをプログラムから実行する方法についても説明しました。 これで、Cosmos DB アカウントに追加のデータをインポートできます。 

> [!div class="nextstepaction"]
> [Azure Cosmos DB へのデータのインポート](import-data.md)



