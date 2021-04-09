---
title: チュートリアル:Azure Cosmos DB および SQL API を使用した Java Web アプリの作成
description: チュートリアル:この Java Web アプリケーション チュートリアルでは、Azure Cosmos DB および SQL API を使って、Azure Websites でホストされる Java アプリケーションからデータを格納する方法やデータにアクセスする方法について説明します。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 930594d2c1af198aba3dae936cd0ce8fcf91c336
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102214101"
---
# <a name="tutorial-build-a-java-web-application-using-azure-cosmos-db-and-the-sql-api"></a>チュートリアル:Azure Cosmos DB および SQL API を使用した Java Web アプリケーションの作成
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](./create-sql-api-python.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

この Java Web アプリケーション チュートリアルでは、[Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) サービスを使用して、Azure App Service Web Apps でホストされる Java アプリケーションからデータを格納する方法やデータにアクセスする方法について説明します。 この記事では、次のことについて説明します。

* Eclipse で基本的な JavaServer Pages (JSP) アプリケーションを作成する方法。
* [Azure Cosmos DB Java SDK](https://github.com/Azure/azure-documentdb-java) を使って Azure Cosmos DB サービスを操作する方法。

この Java Web アプリケーション チュートリアルでは、次の図に示すように、タスクを作成、取得、完了済みとしてマークできる、Web ベースのタスク管理アプリケーションを作成する方法について説明します。 ToDo リストの各タスクは、JSON ドキュメントとして Azure Cosmos DB に格納されます。

:::image type="content" source="./media/sql-api-java-application/image1.png" alt-text="My ToDo List Java アプリケーション":::

> [!TIP]
> このアプリケーション開発チュートリアルは、Java を使用した経験がある読者を対象としています。 Java や[前提条件となるツール](#Prerequisites)を初めて扱う方は、完全な [todo]https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app) プロジェクトを GitHub からダウンロードし、[この記事の最後にある手順](#GetProject)に従ってプロジェクトをビルドすることをお勧めします。 プロジェクトをビルドした後でこの記事を見直すと、プロジェクトのコンテキストのコードについての洞察を得ることができます。  
>

## <a name="prerequisites-for-this-java-web-application-tutorial"></a><a id="Prerequisites"></a>この Java Web アプリケーション チュートリアルの前提条件

このアプリケーション開発チュートリアルを読み始める前に、次の項目を用意する必要があります。

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Java Development Kit (JDK) 7 以降](/java/azure/jdk/)。
* [Eclipse IDE for Java EE Developers。](https://www.eclipse.org/downloads/packages/release/luna/sr1/eclipse-ide-java-ee-developers)
* [Java ランタイム環境 (Tomcat、Jetty など) が有効な Azure Web サイト。](../app-service/quickstart-java.md)

これらのツールを初めてインストールする場合は、coreservlets.com の「[Tutorial: Installing TomCat7 and Using it with Eclipse](http://www.coreservlets.com/Apache-Tomcat-Tutorial/tomcat-7-with-eclipse.html)」 (チュートリアル: TomCat7 のインストールと Eclipse での使用) の「Quick Start」 (クイック スタート) セクションで、インストール プロセスの手順を参照してください。

## <a name="create-an-azure-cosmos-db-account"></a><a id="CreateDB"></a>Azure Cosmos DB アカウントを作成する

まず最初に、Azure Cosmos DB アカウントを作成します。 アカウントが既にある場合、またはこのチュートリアルで Azure Cosmos DB Emulator を使用する場合は、「[手順 2: Java JSP アプリケーションを作成する](#CreateJSP)」に進むことができます。

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

## <a name="create-the-java-jsp-application"></a><a id="CreateJSP"></a>Java JSP アプリケーションを作成する

JSP アプリケーションを作成するには:

1. 最初に、Java プロジェクトを作成します。 Eclipse を起動し、 **[File]** 、 **[New]** 、 **[Dynamic Web Project]** の順にクリックします。 使用可能なプロジェクトとして **[Dynamic Web Project]\(動的 Web プロジェクト\)** が表示されない場合は、 **[File]\(ファイル\)** 、 **[New]\(新規\)** 、 **[Project]\(プロジェクト\)** の順にクリックし、 **[Web]** を展開して、 **[Dynamic Web Project]\(動的 Web プロジェクト\)** 、 **[Next]\(次へ\)** の順にクリックします。
   
    :::image type="content" source="./media/sql-api-java-application/image10.png" alt-text="JSP Java アプリケーション開発":::

1. **[Project name]** ボックスに、プロジェクト名を入力します。必要に応じて、 **[Target Runtime]** ボックスの一覧で値 (たとえば、Apache Tomcat v7.0) を選択し、 **[Finish]** をクリックします。 ターゲット ランタイムを選択すると、Eclipse でプロジェクトをローカルに実行できます。

1. Eclipse の Project Explorer ビューで、プロジェクトを展開します。 **WebContent** を右クリックし、 **[新規]** 、 **[JSP ファイル]** の順にクリックします。

1. **[New JSP File]** ダイアログ ボックスで、ファイルに **index.jsp** という名前を付けます。 次の図に示すように、親フォルダーは **WebContent** のままにしておきます。 **[Next]** をクリックします。
   
    :::image type="content" source="./media/sql-api-java-application/image11.png" alt-text="新しい JSP ファイルの作成 - Java Web アプリケーションのチュートリアル":::

1. **[Select JSP Template]** ダイアログ ボックスで、このチュートリアルのために **[New JSP File (html)]** を選択し、 **[Finish]** をクリックします。

1. *index.jsp* ファイルが Eclipse で開いたら、**Hello World!** を表示するためのテキストを追加します 既存の `<body>` 要素に追加します。 更新した `<body>` の内容は次のようになります。

   ```html
   <body>
     <% out.println("Hello World!"); %>
   </body>
   ```

1. *index.jsp* ファイルを保存します。

1. 手順 2. でターゲットのランタイムを設定している場合、 **[Project]** をクリックし、次に **[Run]** をクリックして、JSP アプリケーションをローカルで実行できます。

   :::image type="content" source="./media/sql-api-java-application/image12.png" alt-text="Hello World - Java アプリケーションのチュートリアル":::

## <a name="install-the-sql-java-sdk"></a><a id="InstallSDK"></a>SQL Java SDK をインストールする

SQL Java SDK とその依存関係をインストールするには、[Apache Maven](https://maven.apache.org/) を使うのが最も簡単です。 そのためには、次の手順に従ってプロジェクトを Maven プロジェクトに変換する必要があります。

1. Project Explorer でプロジェクトを右クリックして、 **[Configure]** 、 **[Convert to Maven Project]** の順にクリックします。

1. **[Create new POM]** ウィンドウで、既定値を受け入れ、 **[Finish]** をクリックします。

1. **Project Explorer** で、pom.xml ファイルを開きます。

1. **[Dependencies]** タブの **[Dependencies]** ウィンドウで、 **[Add]** をクリックします。

1. **[Select Dependency]** ウィンドウで、次の操作を行います。
   
   * **[Group Id]** ボックスに、「`com.azure`」と入力します。
   * **[Artifact Id]** ボックスに、「`azure-cosmos`」と入力します。
   * **[Version]** ボックスに「`4.11.0`」と入力します。
  
   または、Group ID および Artifact ID の依存関係 XML を直接 *pom.xml* に追加します。

   ```xml
   <dependency>
     <groupId>com.azure</groupId>
     <artifactId>azure-cosmos</artifactId>
     <version>4.11.0</version>
   </dependency>
   ```

1. **[OK]** をクリックすると、Maven によって、SQL Java SDK がインストールされるか、pom.xml ファイルが保存されます。

## <a name="use-the-azure-cosmos-db-service-in-your-java-application"></a><a id="UseService"></a>Java アプリケーションで Azure Cosmos DB サービスを使用する

次に、モデル、ビュー、およびコントローラーを Web アプリに追加してみましょう。

### <a name="add-a-model"></a>モデルを追加する

まず、新しいファイル *TodoItem.java* 内にモデルを定義します。 `TodoItem` クラスで、getter および setter メソッドと共に項目のスキーマを定義します。

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/model/TodoItem.java":::

### <a name="add-the-data-access-objectdao-classes"></a>データ アクセス オブジェクト (DAO) クラスを追加する

ToDo 項目の永続化を Azure Cosmos DB に抽出するためのデータ アクセス オブジェクト (DAO) を作成します。 ToDo 項目をコレクションに保存するために、クライアントはどのデータベースとコレクションが永続化するかを知っている必要があります (自己リンクによって参照されます)。 一般に、データベースへの追加のラウンドト リップを回避するために、可能な場合は、データベースとコレクションをキャッシュすることをお勧めします。

1. Azure Cosmos DB サービスを呼び出すには、新しい `cosmosClient` オブジェクトをインスタンス化する必要があります。 一般に、後続の要求ごとに新しいクライアントを構築するのではなく、`cosmosClient` オブジェクトを再利用することをお勧めします。 クライアントを再利用するには、`cosmosClientFactory` クラス内で定義します。 [手順 1](#CreateDB) で保存した HOST と MASTER_KEY の値を更新します。 HOST 変数と MASTER_KEY をそれぞれ、ご自分の URI とプライマリ キーに置き換えます。 次のコードを使用して、*CosmosClientFactory.java* ファイル内に `CosmosClientFactory` クラスを作成します。

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/CosmosClientFactory.java":::

1. 新しい *TodoDao.java* ファイルを作成し、ToDo 項目を作成、更新、読み取り、削除するための `TodoDao` クラスを追加します。

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/TodoDao.java":::

1. 新しい *MockDao.java* ファイルを作成し、`MockDao` クラスを追加します。このクラスは `TodoDao` クラスを実装して、項目に対して CRUD 操作を実行します。

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/MockDao.java":::

1. 新しい *DocDbDao.java* ファイルを作成し、`DocDbDao` クラスを追加します。 このクラスで TodoItems をコンテナーに永続化するコードを定義し、データベースとコレクションが存在する場合はそれを取得し、存在しない場合は新規に作成します。 この例では、[Gson](https://code.google.com/p/google-gson/) を使用して、TodoItem Plain Old Java Objects (POJO) を JSON ドキュメントにシリアル化および逆シリアル化します。 ToDo 項目をコレクションに保存するために、クライアントはどのデータベースとコレクションが永続化するかを知っている必要があります (自己リンクによって参照されます)。 このクラスで、別の属性 ("ID" など) により、ドキュメントを取得するヘルパー関数も定義します。セルフ リンクは使用しません。 ヘルパー メソッドを使用して、ID により、TodoItem の JSON ドキュメントを取得して POJO に逆シリアル化できます。

   `cosmosClient` クライアント オブジェクトを使って、SQL クエリにより、TodoItems のコレクションまたはリストを取得することもできます。 最後に、リストから TodoItem を削除するための削除メソッドを定義します。 以下のコードは、`DocDbDao` クラスの内容を示しています。

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/DocDbDao.java":::

1. 次に、新しい *TodoDaoFactory.java* ファイルを作成し、新しい DocDbDao オブジェクトを作成する `TodoDaoFactory` クラスを追加します。

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/TodoDaoFactory.java":::

### <a name="add-a-controller"></a>コントローラーを追加する

*TodoItemController* コントローラーをアプリケーションに追加します。 このプロジェクトでは、[Project Lombok](https://projectlombok.org/) を使用して、コンストラクター、getter、setter、ビルダーを生成します。 または、このコードを手動で作成するか、IDE で自動的に生成することもできます。

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/controller/TodoItemController.java":::

### <a name="create-a-servlet"></a>サーブレットを作成する

次に、サーブレットを作成して HTTP 要求をコントローラーにルーティングします。 *ApiServlet.java* ファイルを作成し、その下に次のコードを定義します。

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/ApiServlet.java":::

## <a name="wire-the-rest-of-the-of-java-app-together"></a><a id="Wire"></a>Java アプリの残りの部分を連結する

これで、楽しい作業が終わりました。残っているのは簡単なユーザー インターフェイスの作成と DAO への連結です。

1. ユーザーに表示する Web ユーザー インターフェイスが必要です。 次のコードを使用して、先ほど作成した *index.jsp* を書き換えてみましょう。

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/WebContent/index.jsp":::

1. 最後に、Web ユーザー インターフェイスとサーブレットを結び付けるためにクライアント側の JavaScript を記述します。

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/WebContent/assets/todo.js":::

1. 後はアプリケーションをテストするだけです。 アプリケーションをローカルで実行し、項目の名前とカテゴリを入力して、 **[Add Task]** をクリックして、いくつかの Todo 項目を追加します。 項目が表示されたら、チェックボックスを切り替え、 **[Update Tasks]** をクリックして、その項目が完了しているかどうかを確認できます。

## <a name="deploy-your-java-application-to-azure-web-sites"></a><a id="Deploy"></a>Azure Web Sites に Java アプリケーションをデプロイする

Azure Web Sites での Java アプリケーションのデプロイは簡単です。アプリケーションを WAR ファイルとしてエクスポートし、ソース管理 (例: Git) または FTP を使用してアップロードするだけです。

1. アプリケーションを WAR ファイルとしてエクスポートするには、**Project Explorer** でプロジェクトを右クリックし、 **[Export]\(エクスポート\)** 、 **[WAR File]\(WAR ファイル\)** の順にクリックします。

1. **[WAR Export]** ウィンドウで、次の操作を行います。
   
   * [Web project]\(Web プロジェクト\) ボックスに、「azure-cosmos-java-sample」と入力します。
   * [Destination] ボックスでエクスポート先を選択し、WAR ファイルを保存します。
   * **[完了]** をクリックします。

1. これで WAR ファイルを Azure Web サイトの **webapps** ディレクトリにアップロードできます。 ファイルのアップロード手順については、「[Azure App Service Web Apps への Java アプリケーションの追加](../app-service/quickstart-java.md)」を参照してください。 WAR ファイルを webapps ディレクトリにアップロードすると、ランタイム環境により、ファイルの追加が検出され、ファイルが自動的に読み込まれます。

1. 完成した製品を表示するには、`http://YOUR\_SITE\_NAME.azurewebsites.net/azure-cosmos-java-sample/` に移動し、タスクの追加を開始します。

## <a name="get-the-project-from-github"></a><a id="GetProject"></a>GitHub からのプロジェクトの入手

このチュートリアルのサンプルはすべて、GitHub の [todo](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app) プロジェクトに含まれています。 todo プロジェクトを Eclipse にインポートするには、「 [前提条件](#Prerequisites) 」セクションに記載されているソフトウェアおよびリソースがあることを確認したうえで、以下の手順に従います。

1. [Project Lombok](https://projectlombok.org/)をインストールします。 Lombok は、プロジェクトのコンストラクター、getter、setter の生成に使用します。 lombok.jar ファイルをダウンロードしたら、ファイルをダブルクリックしてコマンド ラインからインストールします。

1. Eclipse が開いている場合は、いったん終了してから再起動して Lombok を読み込みます。

1. Eclipse で、 **[File]** メニューの **[Import]** をクリックします。

1. **[Import]** ウィンドウで、 **[Git]** 、 **[Projects from Git]** 、 **[Next]** の順にクリックします。

1. **[Select Repository Source]** 画面で、 **[Clone URI]** をクリックします。

1. **[Source Git Repository]** 画面で、 **[URI]** ボックスに「 https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app 」と入力し、 **[Next]** をクリックします。

1. **[Branch Selection]** 画面で、 **[main]** が選択されていることを確認し、 **[Next]** をクリックします。

1. **[Local Destination]** 画面で、 **[Browse]** をクリックしてリポジトリをコピーするフォルダーを選択し、 **[Next]** をクリックします。

1. **[Select a wizard to use for importing projects]** 画面で、 **[Import existing projects]** が選択されていることを確認し、 **[Next]** をクリックします。

1. **[Import Projects]** 画面で、**DocumentDB** プロジェクトを選択解除し、 **[Finish]** をクリックします。 DocumentDB プロジェクトには、依存関係として追加される Azure Cosmos DB Java SDK が含まれています。

1. **Project Explorer** で、azure-cosmos-java-sample\src\com.microsoft.azure.cosmos.sample.dao\DocumentClientFactory.java を表示し、[HOST] 値と [MASTER_KEY] 値を Azure Cosmos DB アカウントの URI とプライマリ キーで置き換え、ファイルを保存します。 詳細については、「[手順 1:Azure Cosmos データベース アカウントの作成](#CreateDB)に関する説明を参照してください。

1. **Project Explorer** で、**azure-cosmos-java-sample** を右クリックし、 **[Build Path]\(ビルド パス\)** 、 **[Configure Build Path]\(ビルド パスの構成\)** の順にクリックします。

1. **[Java Build Path]** 画面の右ウィンドウで **[Libraries]** タブを選択し、 **[Add External JARs]** をクリックします。 lombok.jar ファイルの場所を参照し、 **[Open]** 、 **[OK]** の順にクリックします。

1. 手順 12. の手順を使用してもう一度 **[Properties]** ウィンドウを開き、左ウィンドウの **[Targeted Runtimes]** をクリックします。

1. **[Targeted Runtimes]** 画面で、 **[New]** をクリックします。 **[Apache Tomcat v7.0]** を選択し、 **[OK]** をクリックします。

1. 手順 12. の手順を使用してもう一度 **[Properties]** ウィンドウを開き、左ウィンドウの **[Project Facets]** をクリックします。

1. **[Project Facets]** 画面で、 **[Dynamic Web Module]** と **[Java]** を選択し、 **[OK]** をクリックします。

1. 画面の下部の **[Servers]** タブで、 **[Tomcat v7.0 Server at localhost]** を右クリックし、 **[Add and Remove]** をクリックします。

1. **[Add and Remove]\(追加と削除\)** ウィンドウで、**azure-cosmos-java-sample** を **[Configured]\(構成済み\)** ボックスに移動し、 **[Finish]\(完了\)** をクリックします。

1. **[Servers]\(サーバー\)** タブで、 **[Tomcat v7.0 Server at localhost]\(ローカルホストの Tomcat v7.0 サーバー\)** を右クリックし、 **[Restart]\(再起動\)** をクリックします。

1. ブラウザーで `http://localhost:8080/azure-cosmos-java-sample/` に移動し、タスク一覧への追加を開始します。 既定のポート値を変更している場合は、8080 に代えて、使用している値を指定してください。

1. プロジェクトを Azure Web サイトにデプロイする方法については、「[手順 6: Azure Web Sites にアプリケーションをデプロイする](#Deploy)」を参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Cosmos DB に接続する Node.js アプリケーションの構築](sql-api-nodejs-application.md)