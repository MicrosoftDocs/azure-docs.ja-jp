---
title: 'Azure Cosmos DB チュートリアル: Apache TinkerPops Gremlin コンソールでの作成、クエリ、走査'
description: Azure Cosmos DB Gremlin API を使用して頂点、辺、およびクエリを作成するための Azure Cosmos DB クイック スタート。
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: quickstart
ms.date: 07/23/2019
ms.author: lbosq
ms.openlocfilehash: cd5408e9a40177fb2c8b029673b96994c6928313
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384333"
---
# <a name="quickstart-create-query-and-traverse-an-azure-cosmos-db-graph-database-using-the-gremlin-console"></a>クイック スタート:Gremlin コンソールを使用して Azure Cosmos DB グラフ データベースを作成、クエリ、および走査する

> [!div class="op_single_selector"]
> * [Gremlin コンソール](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Azure Cosmos DB は、Microsoft のグローバルに配布されるマルチモデル データベース サービスです。 Azure Cosmos DB の中核をなすグローバル配布と水平方向のスケール機能を活用して、ドキュメント、キー/値、およびグラフ データベースをすばやく作成および照会できます。 

このクイック スタートでは、Azure portal を使用して Azure Cosmos DB [Gremlin API](graph-introduction.md) アカウント、データベース、グラフ (コンテナー) を作成してから、[Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) で [Gremlin コンソール](https://tinkerpop.apache.org)を使用して Gremlin API データを操作する方法を説明します。 このチュートリアルでは、頂点プロパティを更新しながら頂点と辺を作成およびクエリし、頂点をクエリし、グラフを走査し、頂点を削除します。

![Apache Gremlin コンソールからの Azure Cosmos DB](./media/create-graph-gremlin-console/gremlin-console.png)

Gremlin コンソールは Groovy/Java ベースであり、Linux、Mac、および Windows 上で実行します。 これは [Apache TinkerPop サイト](https://tinkerpop.apache.org/downloads.html)からダウンロードできます。

## <a name="prerequisites"></a>前提条件

このクイック スタートの Azure Cosmos DB アカウントを作成するには、Azure サブスクリプションが必要です。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[Gremlin コンソール](https://tinkerpop.apache.org/)もインストールする必要があります。 バージョン 3.2.5 以降を使用してください。 (Windows で Gremlin コンソールを使用する場合は、[Java ランタイム](https://www.oracle.com/technetwork/java/javase/overview/index.html)をインストールする必要があります。)

## <a name="create-a-database-account"></a>データベース アカウントの作成

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>グラフの追加

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a id="ConnectAppService"></a>App Service への接続
1. Gremlin コンソールを開始する前に、`apache-tinkerpop-gremlin-console-3.2.5/conf` ディレクトリで remote-secure.yaml 構成ファイルを作成または変更します。
2. 次の表の定義に従って、*host*、*port*、*username*、*password*、*connectionPool*、および *serializer* の構成を入力します。

    Setting|推奨値|説明
    ---|---|---
    hosts|[*account-name*.gremlin.cosmos.azure.com]|次のスクリーンショットをご覧ください。 これは、Azure Portal の [概要] ページに表示される [Gremlin URI] の値から末尾の ":443/" を削除して角かっこで囲んだものです。
    port|443|443 に設定します。
    username|*自分のユーザー名*|`/dbs/<db>/colls/<coll>` 形式のリソースです。`<db>` は実際のデータベースの名前、`<coll>` は実際のコレクションの名前になります。
    password|*自分のプライマリ キー*| 以下の 2 つ目のスクリーンショットをご覧ください。 これは自分のプライマリ キーです。Azure Portal の [キー] ページの [プライマリ キー] ボックスから取得できます。 ボックスの左側にあるコピー ボタンを使用して値をコピーしてください。
    connectionPool|{enableSsl: true}|SSL 用の接続プールの設定です。
    serializer|{ className: org.apache.tinkerpop.gremlin.<br>driver.ser.GraphSONMessageSerializerV1d0,<br> config: { serializeResultToString: true }}|この値に設定します。改行 (`\n`) を削除して値を貼り付けてください。

    hosts の値には、 **[概要]** ページから **[Gremlin URI]** の値をコピーします。![Azure Portal の [概要] ページで Gremlin URI の値を表示してコピー](./media/create-graph-gremlin-console/gremlin-uri.png)

    パスワードの値には、 **[キー]** ページから **[主キー]** をコピーします。![Azure portal の [キー] ページでプライマリ キーを表示してコピーする](./media/create-graph-gremlin-console/keys.png)

remote secure.yaml ファイルは、次のようになります。

```
hosts: [your_database_server.gremlin.cosmos.azure.com] 
port: 443
username: /dbs/your_database_account/colls/your_collection
password: your_primary_key
connectionPool: {
  enableSsl: true
}
serializer: { className: org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerV1d0, config: { serializeResultToString: true }}
```

hosts パラメーターの値は、必ず角かっこ ([]) で囲んでください。 

1. ご使用のターミナルで、`bin/gremlin.bat` または `bin/gremlin.sh` を実行して [Gremlin コンソール](https://tinkerpop.apache.org/docs/3.2.5/tutorials/getting-started/)を起動します。
1. ご使用のターミナルで、`:remote connect tinkerpop.server conf/remote-secure.yaml` を実行して目的の App Service に接続します。

    > [!TIP]
    > エラー `No appenders could be found for logger` が発生した場合、手順 2. で説明されているとおり、remote-secure.yaml ファイルの serializer 値を更新したことを確認してください。 

1. 次に、`:remote console` を実行して、すべてのコンソール コマンドをリモート サーバーにリダイレクトします。

   > [!NOTE]
   > `:remote console` コマンドを実行せずに、すべてのコンソール コマンドをリモート サーバーにリダイレクトしたい場合は、コマンドの前に `:>` を付ける必要があります。たとえば、コマンドを `:> g.V().count()` として実行します。 このプレフィックスはコマンドの一部であり、Azure Cosmos DB で Gremlin コンソールを使用するときに重要になります。 このプレフィックスを省略すると、コンソールにコマンドをローカルで、多くの場合はメモリ内のグラフに対して実行するように指示します。 このプレフィックス `:>` を使用すると、コンソールにリモート コマンドを、この場合は Azure Cosmos DB (localhost エミュレーターまたは Azure インスタンスのどちらか) に対して実行するように指示します。

これでセットアップは終了です。 いくつかのコンソール コマンドの実行を開始しましょう。

単純な count() コマンドを試します。 コンソールのプロンプトに次のように入力します。

```
g.V().count()
```

## <a name="create-vertices-and-edges"></a>頂点と辺の作成

まずはじめに、*Thomas*、*Mary Kay*、*Robin*、*Ben*、および *Jack* の 5 つの人物頂点を追加しましょう。

入力 (Thomas):

```
g.addV('person').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44).property('userid', 1)
```

出力:

```
==>[id:796cdccc-2acd-4e58-a324-91d6f6f5ed6d,label:person,type:vertex,properties:[firstName:[[id:f02a749f-b67c-4016-850e-910242d68953,value:Thomas]],lastName:[[id:f5fa3126-8818-4fda-88b0-9bb55145ce5c,value:Andersen]],age:[[id:f6390f9c-e563-433e-acbf-25627628016e,value:44]],userid:[[id:796cdccc-2acd-4e58-a324-91d6f6f5ed6d|userid,value:1]]]]
```
入力 (Mary Kay):

```
g.addV('person').property('firstName', 'Mary Kay').property('lastName', 'Andersen').property('age', 39).property('userid', 2)

```

出力:

```
==>[id:0ac9be25-a476-4a30-8da8-e79f0119ea5e,label:person,type:vertex,properties:[firstName:[[id:ea0604f8-14ee-4513-a48a-1734a1f28dc0,value:Mary Kay]],lastName:[[id:86d3bba5-fd60-4856-9396-c195ef7d7f4b,value:Andersen]],age:[[id:bc81b78d-30c4-4e03-8f40-50f72eb5f6da,value:39]],userid:[[id:0ac9be25-a476-4a30-8da8-e79f0119ea5e|userid,value:2]]]]

```

入力 (Robin):

```
g.addV('person').property('firstName', 'Robin').property('lastName', 'Wakefield').property('userid', 3)
```

出力:

```
==>[id:8dc14d6a-8683-4a54-8d74-7eef1fb43a3e,label:person,type:vertex,properties:[firstName:[[id:ec65f078-7a43-4cbe-bc06-e50f2640dc4e,value:Robin]],lastName:[[id:a3937d07-0e88-45d3-a442-26fcdfb042ce,value:Wakefield]],userid:[[id:8dc14d6a-8683-4a54-8d74-7eef1fb43a3e|userid,value:3]]]]
```

入力 (Ben):

```
g.addV('person').property('firstName', 'Ben').property('lastName', 'Miller').property('userid', 4)

```

出力:

```
==>[id:ee86b670-4d24-4966-9a39-30529284b66f,label:person,type:vertex,properties:[firstName:[[id:a632469b-30fc-4157-840c-b80260871e9a,value:Ben]],lastName:[[id:4a08d307-0719-47c6-84ae-1b0b06630928,value:Miller]],userid:[[id:ee86b670-4d24-4966-9a39-30529284b66f|userid,value:4]]]]
```

入力 (Jack):

```
g.addV('person').property('firstName', 'Jack').property('lastName', 'Connor').property('userid', 5)
```

出力:

```
==>[id:4c835f2a-ea5b-43bb-9b6b-215488ad8469,label:person,type:vertex,properties:[firstName:[[id:4250824e-4b72-417f-af98-8034aa15559f,value:Jack]],lastName:[[id:44c1d5e1-a831-480a-bf94-5167d133549e,value:Connor]],userid:[[id:4c835f2a-ea5b-43bb-9b6b-215488ad8469|userid,value:5]]]]
```


次に、人物間の関係に対し辺を追加してみましょう。

入力 (Thomas -> Mary Kay):

```
g.V().hasLabel('person').has('firstName', 'Thomas').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Mary Kay'))
```

出力:

```
==>[id:c12bf9fb-96a1-4cb7-a3f8-431e196e702f,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:0d1fa428-780c-49a5-bd3a-a68d96391d5c,outV:1ce821c6-aa3d-4170-a0b7-d14d2a4d18c3]
```

入力 (Thomas -> Robin):

```
g.V().hasLabel('person').has('firstName', 'Thomas').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Robin'))
```

出力:

```
==>[id:58319bdd-1d3e-4f17-a106-0ddf18719d15,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:3e324073-ccfc-4ae1-8675-d450858ca116,outV:1ce821c6-aa3d-4170-a0b7-d14d2a4d18c3]
```

入力 (Robin -> Ben):

```
g.V().hasLabel('person').has('firstName', 'Robin').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Ben'))
```

出力:

```
==>[id:889c4d3c-549e-4d35-bc21-a3d1bfa11e00,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:40fd641d-546e-412a-abcc-58fe53891aab,outV:3e324073-ccfc-4ae1-8675-d450858ca116]
```

## <a name="update-a-vertex"></a>頂点の更新

*Thomas* の頂点を *45* の新しい年齢で更新しましょう。

次の内容を入力します。
```
g.V().hasLabel('person').has('firstName', 'Thomas').property('age', 45)
```
出力:

```
==>[id:ae36f938-210e-445a-92df-519f2b64c8ec,label:person,type:vertex,properties:[firstName:[[id:872090b6-6a77-456a-9a55-a59141d4ebc2,value:Thomas]],lastName:[[id:7ee7a39a-a414-4127-89b4-870bc4ef99f3,value:Andersen]],age:[[id:a2a75d5a-ae70-4095-806d-a35abcbfe71d,value:45]]]]
```

## <a name="query-your-graph"></a>グラフのクエリ

次に、グラフに対してさまざまなクエリを実行してみましょう。

最初に、40 歳よりも年齢が上の人物だけを返すフィルターを使用してクエリを試してみましょう。

入力 (フィルター クエリ):

```
g.V().hasLabel('person').has('age', gt(40))
```

出力:

```
==>[id:ae36f938-210e-445a-92df-519f2b64c8ec,label:person,type:vertex,properties:[firstName:[[id:872090b6-6a77-456a-9a55-a59141d4ebc2,value:Thomas]],lastName:[[id:7ee7a39a-a414-4127-89b4-870bc4ef99f3,value:Andersen]],age:[[id:a2a75d5a-ae70-4095-806d-a35abcbfe71d,value:45]]]]
```

次に、40 歳よりも年齢が上の人物の名を表示してみましょう。

入力 (フィルター + プロジェクション クエリ):

```
g.V().hasLabel('person').has('age', gt(40)).values('firstName')
```

出力:

```
==>Thomas
```

## <a name="traverse-your-graph"></a>グラフの走査

Thomas のすべての友人を返すようにグラフを走査してみましょう。

入力 (Thomas の友人):

```
g.V().hasLabel('person').has('firstName', 'Thomas').outE('knows').inV().hasLabel('person')
```

出力: 

```
==>[id:f04bc00b-cb56-46c4-a3bb-a5870c42f7ff,label:person,type:vertex,properties:[firstName:[[id:14feedec-b070-444e-b544-62be15c7167c,value:Mary Kay]],lastName:[[id:107ab421-7208-45d4-b969-bbc54481992a,value:Andersen]],age:[[id:4b08d6e4-58f5-45df-8e69-6b790b692e0a,value:39]]]]
==>[id:91605c63-4988-4b60-9a30-5144719ae326,label:person,type:vertex,properties:[firstName:[[id:f760e0e6-652a-481a-92b0-1767d9bf372e,value:Robin]],lastName:[[id:352a4caa-bad6-47e3-a7dc-90ff342cf870,value:Wakefield]]]]
```

次に、次のレイヤーの頂点を取得しましょう。 Thomas の友人のすべての友人を返すようにグラフを走査します。

入力 (Thomas の友人の友人):

```
g.V().hasLabel('person').has('firstName', 'Thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```
出力:

```
==>[id:a801a0cb-ee85-44ee-a502-271685ef212e,label:person,type:vertex,properties:[firstName:[[id:b9489902-d29a-4673-8c09-c2b3fe7f8b94,value:Ben]],lastName:[[id:e084f933-9a4b-4dbc-8273-f0171265cf1d,value:Miller]]]]
```

## <a name="drop-a-vertex"></a>頂点の削除

次に、グラフ データベースから頂点を削除しましょう。

入力 (Jack 頂点を削除):

```
g.V().hasLabel('person').has('firstName', 'Jack').drop()
```

## <a name="clear-your-graph"></a>グラフのクリア

最後に、すべての頂点と辺のデータベースをクリアしてみましょう。

入力:

```
g.E().drop()
g.V().drop()
```

お疲れさまでした。 この Azure Cosmos DB: Graph API チュートリアルは完了しました。

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Azure Cosmos DB アカウントの作成、データ エクスプローラーを使用したグラフの作成、Gremlin コンソールを使用した頂点と辺の作成およびグラフの走査を行う方法を説明しました。 これで Gremlin を使用して、さらに複雑なクエリを作成し、強力なグラフ トラバーサル ロジックを実装できます。 

> [!div class="nextstepaction"]
> [Gremlin を使用したクエリ](tutorial-query-graph.md)
