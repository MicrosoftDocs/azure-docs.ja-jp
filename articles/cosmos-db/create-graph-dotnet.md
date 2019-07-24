---
title: Gremlin API を使用して Azure Cosmos DB .NET Framework アプリケーションまたは .NET Core アプリケーションを構築する
description: Azure Cosmos DB への接続とデータの照会に使用できる .NET Framework/Core コード サンプルについて説明します
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 01/08/2018
ms.author: lbosq
ms.openlocfilehash: 3f10c8d38d37682e2a949397d9747ec094bdb9af
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58170107"
---
# <a name="quickstart-build-a-net-framework-or-core-application-using-the-azure-cosmos-db-gremlin-api-account"></a>クイック スタート:Azure Cosmos DB Gremlin API アカウントを使用して .NET Framework アプリケーションまたは Core アプリケーションを構築する

> [!div class="op_single_selector"]
> * [Gremlin コンソール](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Azure Cosmos DB は、Microsoft のグローバルに分散されたマルチモデル データベース サービスです。 Azure Cosmos DB の中核をなすグローバル配布と水平方向のスケール機能を活用して、ドキュメント、キー/値、およびグラフ データベースをすばやく作成および照会できます。 

このクイック スタートでは、Azure portal を使用した Azure Cosmos DB [Gremlin API](graph-introduction.md) アカウント、データベース、およびグラフ (コンテナー) の作成方法を説明します。 その後、オープンソース ドライバーの [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) を使用して、コンソール アプリを構築し実行します。  

## <a name="prerequisites"></a>前提条件

まだ Visual Studio 2017 をインストールしていない場合は、**無料**の [Visual Studio 2017 Community エディション](https://www.visualstudio.com/downloads/)をダウンロードして使用できます。 Visual Studio のセットアップ中に、必ず **[Azure の開発]** を有効にしてください。

Visual Studio 2017 がインストール済みである場合は、[Visual Studio 2017 Update 3](https://www.visualstudio.com/en-us/news/releasenotes/vs2017-relnotes) までインストールされていることを確認してください。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>データベース アカウントの作成

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>グラフの追加

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

GitHub から Gremlin API アプリの複製を作成し、接続文字列を設定して実行します。 プログラムでデータを処理することが非常に簡単であることがわかります。 

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-gremlindotnet-getting-started.git
    ```

4. 次に、Visual Studio を開いてソリューション ファイルを開きます。

5. プロジェクト内の NuGet パッケージを復元します。 これには、Gremlin.Net ドライバーと Newtonsoft.Json パッケージを含める必要があります。


6. また、Nuget パッケージ マネージャーまたは [nuget コマンド ライン ユーティリティ](https://docs.microsoft.com/nuget/install-nuget-client-tools)を使用して、Gremlin.Net ドライバーを手動でインストールすることもできます。 

    ```bash
    nuget install Gremlin.Net
    ```

## <a name="review-the-code"></a>コードの確認

この手順は省略可能です。 コード内のデータベース リソースの作成方法に関心がある場合は、次のスニペットを確認できます。 関心がない場合は、「[接続文字列の更新](#update-your-connection-string)」に進んでください。 

次のスニペットはすべて Program.cs ファイルからのものです。

* 上で作成したアカウントに基づいて接続パラメーターを設定します (19 行目)。 

    ```csharp
    private static string hostname = "your-endpoint.gremlin.cosmosdb.azure.com";
    private static int port = 443;
    private static string authKey = "your-authentication-key";
    private static string database = "your-database";
    private static string collection = "your-graph-container";
    ```

* 実行される Gremlin コマンドの一覧は、Dictionary に記述されています (26 行目)。

    ```csharp
    private static Dictionary<string, string> gremlinQueries = new Dictionary<string, string>
    {
        { "Cleanup",        "g.V().drop()" },
        { "AddVertex 1",    "g.addV('person').property('id', 'thomas').property('firstName', 'Thomas').property('age', 44)" },
        { "AddVertex 2",    "g.addV('person').property('id', 'mary').property('firstName', 'Mary').property('lastName', 'Andersen').property('age', 39)" },
        { "AddVertex 3",    "g.addV('person').property('id', 'ben').property('firstName', 'Ben').property('lastName', 'Miller')" },
        { "AddVertex 4",    "g.addV('person').property('id', 'robin').property('firstName', 'Robin').property('lastName', 'Wakefield')" },
        { "AddEdge 1",      "g.V('thomas').addE('knows').to(g.V('mary'))" },
        { "AddEdge 2",      "g.V('thomas').addE('knows').to(g.V('ben'))" },
        { "AddEdge 3",      "g.V('ben').addE('knows').to(g.V('robin'))" },
        { "UpdateVertex",   "g.V('thomas').property('age', 44)" },
        { "CountVertices",  "g.V().count()" },
        { "Filter Range",   "g.V().hasLabel('person').has('age', gt(40))" },
        { "Project",        "g.V().hasLabel('person').values('firstName')" },
        { "Sort",           "g.V().hasLabel('person').order().by('firstName', decr)" },
        { "Traverse",       "g.V('thomas').out('knows').hasLabel('person')" },
        { "Traverse 2x",    "g.V('thomas').out('knows').hasLabel('person').out('knows').hasLabel('person')" },
        { "Loop",           "g.V('thomas').repeat(out()).until(has('id', 'robin')).path()" },
        { "DropEdge",       "g.V('thomas').outE('knows').where(inV().has('id', 'mary')).drop()" },
        { "CountEdges",     "g.E().count()" },
        { "DropVertex",     "g.V('thomas').drop()" },
    };
    ```


* 上で指定したパラメーターを使用して、`GremlinServer` 接続オブジェクトを作成します (52 行目)。

    ```csharp
    var gremlinServer = new GremlinServer(hostname, port, enableSsl: true, 
                                                    username: "/dbs/" + database + "/colls/" + collection, 
                                                    password: authKey);
    ```

* 新しい `GremlinClient` オブジェクトを作成します (56 行目)。

    ```csharp
    var gremlinClient = new GremlinClient(gremlinServer);
    ```

* 非同期タスクを持つ `GremlinClient` オブジェクトを使用して、各 Gremlin クエリを実行します (63 行目)。 これで、上で定義したディクショナリから Gremlin クエリが読み取られます (26 行目)。

    ```csharp
    var results = await gremlinClient.SubmitAsync<dynamic>(query.Value);
    ```

* 結果を取得し、値を読み取ります。これらの値は、Newtonsoft.Json の `JsonSerializer` クラスを使用して、ディクショナリとして書式設定されています。

    ```csharp
    foreach (var result in results)
    {
        // The vertex results are formed as dictionaries with a nested dictionary for their properties
        string output = JsonConvert.SerializeObject(result);
        Console.WriteLine(String.Format("\tResult:\n\t{0}", output));
    }
    ```

## <a name="update-your-connection-string"></a>接続文字列を更新する

ここで Azure Portal に戻り、接続文字列情報を取得し、アプリにコピーします。

1. [Azure portal](https://portal.azure.com/) でグラフ データベース アカウントに移動します。 **[概要]** タブで、2 つのエンドポイントを確認できます。 
 
   **.NET SDK URI** - この値は、Microsoft.Azure.Graphs ライブラリを使用してグラフ アカウントに接続する場合に使用されます。 

   **Gremlin エンドポイント** - この値は、Gremlin.Net ライブラリを使用してグラフ アカウントに接続する場合に使用されます。

    ![エンドポイントのコピー](./media/create-graph-dotnet/endpoint.png)

   このサンプルを実行するには、**[Gremlin エンドポイント]** の値をコピーして最後のポート番号を削除します。つまり、URI は `https://<your cosmos db account name>.gremlin.cosmosdb.azure.com` になります。

2. Program.cs で、19 行目の `hostname` 変数の `your-endpoint` に値を貼り付けます。 

    `"private static string hostname = "<your cosmos db account name>.gremlin.cosmosdb.azure.com";`

    endpoint の値は次のようになります。

    `"private static string hostname = "testgraphacct.gremlin.cosmosdb.azure.com";`

3. 次に、**[キー]** タブに移動してポータルから **[主キー]** の値をコピーし、`authkey` 変数に貼り付けて、21 行目の `"your-authentication-key"` プレースホルダーを置き換えます。 

    `private static string authKey = "your-authentication-key";`

4. 上で作成したデータベースの情報を使用して、22 行目の `database` 変数内にデータベース名を貼り付けます。 

    `private static string database = "your-database";`

5. 同様に、上で作成したコレクションの情報を使用して、23 行目の `collection` 変数内にコンテナー (グラフ名でもあります) を貼り付けます。 

    `private static string collection = "your-collection-or-graph";`

6. Program.cs ファイルを保存します。 

これで、Azure Cosmos DB と通信するために必要なすべての情報でアプリを更新しました。 

## <a name="run-the-console-app"></a>コンソール アプリの実行

Ctrl + F5 キーを押してアプリケーションを実行します。 アプリケーションによって、Gremlin クエリ コマンドと結果の両方がコンソールに出力されます。

   コンソール ウィンドウには、グラフに追加されている頂点と辺が表示されます。 スクリプトが完了したら、Enter キーを押してコンソール ウィンドウを閉じます。

## <a name="browse-using-the-data-explorer"></a>データ エクスプローラーを使用した参照

次に、Azure Portal のデータ エクスプローラーに戻り、新しいグラフ データを参照しクエリできます。

1. データ エクスプローラーで新しいデータベースが [グラフ] ウィンドウに表示されます。 データベースとコンテナーのノードを展開し、**[グラフ]** をクリックします。

2. **[フィルターの適用]** をクリックし、既定のクエリを使用してグラフのすべての頂点を表示します。 サンプル アプリで生成されたデータは、[グラフ] ウィンドウに表示されます。

    グラフは拡大または縮小できます。また、グラフ表示領域の拡大、頂点の追加、表示画面上での頂点の移動が可能です。

    ![Azure Portal のデータ エクスプローラーでのグラフ表示](./media/create-graph-dotnet/graph-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Azure Cosmos DB アカウントを作成し、データ エクスプローラーを使用してグラフを作成し、アプリを実行する方法を説明しました。 これで Gremlin を使用して、さらに複雑なクエリを作成し、強力なグラフ トラバーサル ロジックを実装できます。 

> [!div class="nextstepaction"]
> [Gremlin を使用したクエリ](tutorial-query-graph.md)

