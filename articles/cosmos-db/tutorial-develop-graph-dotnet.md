---
title: "Azure Cosmos DB: .NET での Graph API を使用した開発 | Microsoft Docs"
description: ".NET を使用した Azure Cosmos DB の DocumentDB API による開発方法について"
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: cc8df0be-672b-493e-95a4-26dd52632261
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: denlee
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 94909fd1db426267eb60e5d7f4d753de82ca0377
ms.contentlocale: ja-jp
ms.lasthandoff: 05/31/2017

---
# <a name="azure-cosmos-db-develop-with-the-graph-api-in-net"></a>Azure Cosmos DB: .NET での Graph API を使用した開発
Azure Cosmos DB は、Microsoft のグローバルに分散されたマルチモデル データベース サービスです。 Azure Cosmos DB の中核をなすグローバルな分散と水平方向のスケール機能を利用して、ドキュメント、キー/値、およびグラフ データベースをすばやく作成およびクエリできます。 

このチュートリアルでは、Azure Portal を使用した Azure Cosmos DB アカウントの作成方法とグラフ データベースおよびコンテナーの作成方法を説明します。 その後、アプリケーションで、[Graph API](graph-sdk-dotnet.md) (プレビュー) を使用して 4 名のユーザーからなる単純なソーシャル ネットワークを作成し、Gremlin を使用してグラフのトラバーサルとクエリを行います。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * Azure Cosmos DB アカウントを作成する 
> * グラフ データベースとコンテナーを作成する
> * 頂点とエッジを .NET オブジェクトにシリアル化する
> * 頂点とエッジを追加する
> * Gremlin を使用してグラフのクエリを行う

## <a name="graphs-in-azure-cosmos-db"></a>Azure Cosmos DB のグラフ
Azure Cosmos DB では、[Microsoft.Azure.Graphs](graph-sdk-dotnet.md) ライブラリを使用して、グラフの作成、更新、およびクエリを行うことができます。 Microsoft.Azure.Graph ライブラリでは、`DocumentClient` クラス上で、Gremlin のクエリを実行する 1 つの拡張メソッド `CreateGremlinQuery<T>` が提供されます。

Gremlin は、書き込み操作 (DML)、クエリ操作、およびトラバーサル操作をサポートする関数型プログラミング言語です。 この記事では、Gremlin の使用を開始できるようにいくつかの例を示します。 Azure Cosmos DB で使用できる Gremlin の機能のチュートリアルについて詳しくは、[Gremlin のクエリ](gremlin-support.md)をご覧ください。 

## <a name="prerequisites"></a>前提条件
以下のものがそろっていることを確認してください。

* アクティブな Azure アカウント。 お持ちでない場合は、 [無料アカウント](https://azure.microsoft.com/free/)にサインアップしてください。 
    * また、このチュートリアルには、[Azure DocumentDB Emulator](local-emulator.md) を使用することもできます。
* [Visual Studio](http://www.visualstudio.com/)。

## <a name="create-database-account"></a>データベース アカウントを作成する

まず最初に、Azure Portal で Azure Cosmos DB アカウントを作成します。  

> [!TIP]
> * 既に Azure Cosmos DB アカウントをお持ちですか。 その場合は、「[Visual Studio ソリューションをセットアップする](#SetupVS)」に進みます。
> * 既に Azure DocumentDB アカウントをお持ちでしたか。 この場合、そのアカウントが Azure Cosmos DB アカウントになります。「[Visual Studio ソリューションをセットアップする](#SetupVS)」に進んでください。  
> * Azure Cosmos DB Emulator を使用する場合は、[Azure Cosmos DB Emulator](local-emulator.md) に関する記事に記載されている手順に従って、エミュレーターをセットアップし、「[Visual Studio ソリューションをセットアップする](#SetupVS)」に進んでください。 
>
> 

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a id="SetupVS"></a>Visual Studio ソリューションをセットアップする
1. コンピューターで **Visual Studio** を開きます。
2. **[ファイル]** メニューで、**[新規]**、**[プロジェクト]** の順に選択します。
3. **[新しいプロジェクト]** ダイアログで、**[テンプレート]** / **[Visual C#]** / **[コンソール アプリ (.NET Framework)]** の順に選択し、プロジェクトの名前を指定して、**[OK]** をクリックします。
4. **ソリューション エクスプローラー**で、Visual Studio ソリューションの下にある新しいコンソール アプリケーションを右クリックし、**[NuGet パッケージの管理]** をクリックします。
5. **[NuGet]** タブで **[参照]** をクリックし、検索ボックスに「**Microsoft.Azure.Graphs**」と入力して、**[Include prerelease versions (プレリリース版を含める)]** をオンにします。
6. 結果で **Microsoft.Azure.Graphs** を探し、**[インストール]** をクリックします。
   
   ソリューションの変更の確認に関するメッセージが表示されたら、**[OK]** をクリックします。 ライセンスの同意に関するメッセージが表示されたら、**[同意する]** をクリックします。
   
    `Microsoft.Azure.Graphs` ライブラリには、Gremlin の操作を実行するために 1 つの拡張メソッド `CreateGremlinQuery<T>` が用意されています。 Gremlin は、書き込み操作 (DML)、クエリ操作、およびトラバーサル操作をサポートする関数型プログラミング言語です。 この記事では、Gremlin の使用を開始できるようにいくつかの例を示します。 [Gremlin のクエリ](gremlin-support.md)に関する記事には、Azure Cosmos DB での Gremlin 機能の詳しいチュートリアルが含まれます。

## <a id="add-references"></a>アプリに接続する

これら 2 つの定数とユーザーの *client* 変数をアプリケーションに追加します。 

```csharp
string endpoint = ConfigurationManager.AppSettings["Endpoint"]; 
string authKey = ConfigurationManager.AppSettings["AuthKey"]; 
``` 
次に、[Azure Portal](https://portal.azure.com) に戻り、エンドポイント URL とプライマリ キーを取得します。 エンドポイント URL とプライマリ キーは、アプリケーションが接続先を認識し、Azure Cosmos DB がアプリケーションの接続を信頼するために必要です。 

Azure Portal で Azure Cosmos DB アカウントに移動し、**[キー]** をクリックしてから **[読み取り/書き込みキー]** をクリックします。 

ポータルの URI をコピーして、上の endpoint プロパティの `Endpoint` に貼り付けます。 次に、ポータルのプライマリ キーをコピーし、上の `AuthKey` プロパティに貼り付けます。 

![C# アプリケーションを作成するためにチュートリアルで使用される Azure ポータルのスクリーン ショット。 Azure Cosmos DB ナビゲーションで [キー] ボタンが強調表示された Azure Cosmos DB アカウントと、[キー] ブレードで強調表示された URI と PRIMARY KEY の値を示します[キー] 
 
## <a id="instantiate"></a>DocumentClient をインスタンス化する 
次は、**DocumentClient** の新しいインスタンスを作成します。  

```csharp 
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey); 
``` 

## <a id="create-database"></a>データベースを作成する 

ここで、Azure Cosmos DB [データベース](documentdb-resources.md#databases)を作成します。これには、[DocumentDB .NET SDK](documentdb-sdk-dotnet.md)に含まれる **DocumentClient** クラスの [CreateDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) メソッドまたは [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) メソッドを使用します。  

```csharp 
Database database = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "graphdb" }); 
``` 
 
## <a name="create-a-graph"></a>グラフを作成する 

次に、グラフ コンテナーを作成します。これには、**DocumentClient** クラスの [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) メソッドまたは [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) メソッドを使用します。 コレクションは、グラフ エンティティのコンテナーです。 

```csharp 
DocumentCollection graph = await client.CreateDocumentCollectionIfNotExistsAsync( 
    UriFactory.CreateDatabaseUri("graphdb"), 
    new DocumentCollection { Id = "graphcollz" }, 
    new RequestOptions { OfferThroughput = 1000 }); 
``` 

## <a id="serializing"></a>頂点とエッジを .NET オブジェクトにシリアル化する
Azure Cosmos DB では、頂点、エッジ、プロパティの JSON スキーマを定義するために [GraphSON ワイヤ形式](gremlin-support.md) が使用されます。 Azure Cosmos DB .NET SDK には、依存関係として JSON.NET が含まれます。これを使用して、コードで使用できる .NET オブジェクトに GraphSON をシリアル化/逆シリアル化することができます。

例として、4 名のユーザーからなる単純なソーシャル ネットワークを使用しましょう。 どのようにして `Person` 頂点を作成し、その間に `Knows` リレーションシップを追加してから、グラフのクエリとトラバーサルを行って、"友人の友人" リレーションシップを検索するかを説明します。 

`Microsoft.Azure.Graphs.Elements` 名前空間によって、適切に定義された .NET オブジェクトに GraphSON 応答を逆シリアル化するために、`Vertex`、`Edge`、`Property`、`VertexProperty` の各クラスが提供されます。

## <a name="run-gremlin-using-creategremlinquery"></a>CreateGremlinQuery を使用して Gremlin を実行する
SQL と同じく Gremlin では、読み取り、書き込み、クエリの操作がサポートされます。 たとえば、次のスニペットは、頂点とエッジを作成し、`CreateGremlinQuery<T>` を使用してサンプル クエリを実行し、あらに `ExecuteNextAsync` と HasMoreResults を使用して結果を非同期で反復処理しています。

```cs
Dictionary<string, string> gremlinQueries = new Dictionary<string, string>
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
    { "Traverse",       "g.V('thomas').outE('knows').inV().hasLabel('person')" },
    { "Traverse 2x",    "g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')" },
    { "Loop",           "g.V('thomas').repeat(out()).until(has('id', 'robin')).path()" },
    { "DropEdge",       "g.V('thomas').outE('knows').where(inV().has('id', 'mary')).drop()" },
    { "CountEdges",     "g.E().count()" },
    { "DropVertex",     "g.V('thomas').drop()" },
};

foreach (KeyValuePair<string, string> gremlinQuery in gremlinQueries)
{
    Console.WriteLine($"Running {gremlinQuery.Key}: {gremlinQuery.Value}");

    // The CreateGremlinQuery method extensions allow you to execute Gremlin queries and iterate
    // results asychronously
    IDocumentQuery<dynamic> query = client.CreateGremlinQuery<dynamic>(graph, gremlinQuery.Value);
    while (query.HasMoreResults)
    {
        foreach (dynamic result in await query.ExecuteNextAsync())
        {
            Console.WriteLine($"\t {JsonConvert.SerializeObject(result)}");
        }
    }

    Console.WriteLine();
}
```

## <a name="add-vertices-and-edges"></a>頂点とエッジを追加する

ここでは前のセクションの Gremlin ステートメントについて詳しく説明します。 まず、Gremlin の `addV` メソッドを使用して頂点を追加します。 たとえば、次のスニペットでは、名、姓、年齢のプロパティを含む、タイプが "Person" の "Thomas Andersen" という頂点が作成されます。

```cs
// Create a vertex
IDocumentQuery<Vertex> createVertexQuery = client.CreateGremlinQuery<Vertex>(
    graphCollection, 
    "g.addV('person').property('firstName', 'Thomas')");

while (createVertexQuery.HasMoreResults)
{
    Vertex thomas = (await create.ExecuteNextAsync<Vertex>()).First();
}
```

次に、Gremlin の `addE` メソッドを使用して、これらの頂点の間にエッジを作成します。 

```cs
// Add a "knows" edge
IDocumentQuery<Edge> createEdgeQuery = client.CreateGremlinQuery<Edge>(
    graphCollection, 
    "g.V('thomas').addE('knows').to(g.V('mary'))");

while (create.HasMoreResults)
{
    Edge thomasKnowsMaryEdge = (await create.ExecuteNextAsync<Edge>()).First();
}
```

Gremlin の`properties` ステップ を使用すると既存の頂点を更新できます。 `HasMoreResults` と `ExecuteNextAsync` を介してクエリを実行する呼び出しについてはここでは説明せず、例の後半部分で説明します。

```cs
// Update a vertex
client.CreateGremlinQuery<Vertex>(
    graphCollection, 
    "g.V('thomas').property('age', 45)");
```

エッジや頂点を削除するには、Gremlin の `drop` ステップを使用します。 次のスニペットでは、頂点とエッジを削除しています。 頂点を削除すると、関連するエッジが連鎖的に削除されることに注意してください。

```cs
// Drop an edge
client.CreateGremlinQuery(graphCollection, "g.E('thomasKnowsRobin').drop()");

// Drop a vertex
client.CreateGremlinQuery(graphCollection, "g.V('robin').drop()");
```

## <a name="query-the-graph"></a>グラフのクエリを行う

クエリやトラバーサルも Gremlin を使用して行うことができます。 たとえば、次のスニペットはグラフ内の頂点の数をカウントする方法を示しています。

```cs
// Run a query to count vertices
IDocumentQuery<int> countQuery = client.CreateGremlinQuery<int>(graphCollection, "g.V().count()");
```
Gremlin の `has` ステップと `hasLabel` ステップを使用してフィルターを実行できます。また、`and`、`or`、`not` を使用してそれらを組み合わせて複雑なフィルターを作成できます。

```cs
// Run a query with filter
IDocumentQuery<Vertex> personsByAge = client.CreateGremlinQuery<Vertex>(
  graphCollection, 
  "g.V().hasLabel('person').has('age', gt(40))");
```

`values` ステップを使用してクエリ結果の特定のプロパティを投影できます。

```cs
// Run a query with projection
IDocumentQuery<string> firstNames = client.CreateGremlinQuery<string>(
  graphCollection, 
  $"g.V().hasLabel('person').values('firstName')");
```

ここまでは、どのデータベースでも使用できるクエリ操作のみを説明しました。 関連するエッジと頂点にナビゲートする必要がある場合、グラフではトラバーサル操作が高速かつ効率的です。 Thomas の友人をすべて探しましょう。 これには、 Gremlin の `outE` ステップを使用して Thomas のエッジ (外側) すべてを検索してから、Gremlin の `inV` ステップを使用してそれらのエッジの頂点 (内側) をトラバーサルします。

```cs
// Run a traversal (find friends of Thomas)
IDocumentQuery<Vertex> friendsOfThomas = client.CreateGremlinQuery<Vertex>(
  graphCollection,
  "g.V('thomas').outE('knows').inV().hasLabel('person')");
```

次のクエリは、`outE` と `inV` を 2 回呼び出し、2 つのホップを実行して Thomas の "友人の友人" をすべて検索します。 

```cs
// Run a traversal (find friends of friends of Thomas)
IDocumentQuery<Vertex> friendsOfFriendsOfThomas = client.CreateGremlinQuery<Vertex>(
  graphCollection,
  "g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')");
```

Gremlin を使用すると、さらに複雑なクエリを作成したり、強力なグラフ トラバーサル ロジックを実装したりできます。これには、フィルター式の組み合わせ、`loop` ステップを使用したループの実行、`choose` ステップを使用した条件付きナビゲーションの実装などが含まれます。 他にどのようなことができるかについて詳しくは、[Gremlin のサポート](gremlin-support.md)に関する記事をご覧ください。

これで、この Azure Cosmos DB チュートリアルは終了です。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このアプリの使用を続けない場合は、以下の手順に従い、このチュートリアルで作成したすべてのリソースを Azure Portal で削除してください。  

1. Azure Portal の左側のメニューで、**[リソース グループ]** をクリックしてから、作成したリソースの名前をクリックします。 
2. リソース グループのページで **[削除]** をクリックし、削除するリソースの名前をテキスト ボックスに入力してから **[削除]** をクリックします。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次の手順を行いました。

> [!div class="checklist"]
> * Azure Cosmos DB アカウントの作成 
> * グラフ データベースとコンテナーの作成
> * 頂点とエッジの .NET オブジェクトへのシリアル化
> * 頂点とエッジの追加
> * Gremlin を使用したグラフのクエリ

これで Gremlin を使用して、さらに複雑なクエリを作成し、強力なグラフ トラバーサル ロジックを実装できます。 

> [!div class="nextstepaction"]
> [Gremlin を使用したクエリ](tutorial-query-graph.md)

