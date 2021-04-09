---
title: Azure Cosmos DB で Java を使用してグラフ データベースをビルドする
description: Gremlin を使用した Azure Cosmos DB 内のグラフ データへの接続とクエリに使用できる Java コード サンプルについて説明します。
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: java
ms.topic: quickstart
ms.date: 03/26/2019
ms.author: chrande
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 5a793eb69b344e361679759e46db47c78885831e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93360957"
---
# <a name="quickstart-build-a-graph-database-with-the-java-sdk-and-the-azure-cosmos-db-gremlin-api"></a>クイック スタート:Java SDK と Azure Cosmos DB Gremlin API を使ってグラフ データベースを作成する
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

> [!div class="op_single_selector"]
> * [Gremlin コンソール](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

このクイックスタートでは、Azure portal から Azure Cosmos DB の Gremlin (グラフ) API アカウントを作成、管理し、GitHub からクローンした Java アプリを使用してデータを追加します。 Azure Cosmos DB は、マルチモデル データベース サービスです。グローバルな分散と水平方向のスケーリング機能を備えたドキュメント データベースやテーブル データベース、キーと値のデータベース、グラフ データベースをすばやく作成し、クエリを実行することができます。

## <a name="prerequisites"></a>前提条件
- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 
- [Java Development Kit (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk)。 JDK のインストール先フォルダーを指すように `JAVA_HOME` 環境変数を設定してください。
- [Maven バイナリ アーカイブ](https://maven.apache.org/download.cgi)。 
- [Git](https://www.git-scm.com/downloads). 

## <a name="create-a-database-account"></a>データベース アカウントの作成

グラフ データベースを作成するには、Azure Cosmos DB を含んだ Gremlin (グラフ) データベース アカウントを事前に作成しておく必要があります。

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>グラフの追加

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

次は、コードを使った作業に移りましょう。 GitHub から Gremlin API アプリの複製を作成し、接続文字列を設定して実行します。 プログラムでデータを処理することが非常に簡単であることがわかります。  

1. コマンド プロンプトを開いて git-samples という名前の新しいフォルダーを作成し、コマンド プロンプトを閉じます。

    ```bash
    md "C:\git-samples"
    ```

2. git bash などの git ターミナル ウィンドウを開き、`cd` コマンドを使用して、サンプル アプリをインストールするフォルダーに変更します。  

    ```bash
    cd "C:\git-samples"
    ```

3. 次のコマンドを実行して、サンプル レポジトリを複製します。 このコマンドは、コンピューター上にサンプル アプリのコピーを作成します。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-java-getting-started.git
    ```

## <a name="review-the-code"></a>コードの確認

この手順は省略可能です。 コード内のデータベース リソースの作成方法に関心がある場合は、次のスニペットを確認できます。 関心がない場合は、「[接続文字列の更新](#update-your-connection-information)」に進んでください。

以降のスニペットはすべて、*C:\git-samples\azure-cosmos-db-graph-java-getting-started\src\GetStarted\Program.java* ファイルから取得されます。

この Java コンソール アプリは、OSS [Apache TinkerPop](https://tinkerpop.apache.org/) ドライバーで [Gremlin API](graph-introduction.md) データベースを使用します。 

- Gremlin `Client` は、*C:\git-samples\azure-cosmos-db-graph-java-getting-started\src\remote.yaml* ファイルの構成から初期化されます。

    ```java
    cluster = Cluster.build(new File("src/remote.yaml")).create();
    ...
    client = cluster.connect();
    ```

- Gremlin の一連の手順は、`client.submit` メソッドを使用して実行されます。

    ```java
    ResultSet results = client.submit(gremlin);

    CompletableFuture<List<Result>> completableFutureResults = results.all();
    List<Result> resultList = completableFutureResults.get();

    for (Result result : resultList) {
        System.out.println(result.toString());
    }
    ```

## <a name="update-your-connection-information"></a>接続情報の更新

ここで Azure Portal に戻り、接続情報を取得して、アプリにコピーします。 これらの設定により、アプリはホストされているデータベースと通信できるようになります。

1. [Azure portal](https://portal.azure.com/) の Azure Cosmos DB アカウントで、 **[キー]** を選択します。 

    URI の値の最初の部分をコピーします。

    :::image type="content" source="./media/create-graph-java/copy-access-key-azure-portal.png" alt-text="Azure portal の [キー] ページでアクセス キーを表示およびコピーする":::

2. *src/remote.yaml* ファイルを開き、`hosts: [$name$.graphs.azure.com]` の `$name$` に一意の ID 値を貼り付けます。

    *remote.yaml* の 1 行目は次のようになります。 

    `hosts: [test-graph.graphs.azure.com]`

3. `endpoint` 値の `graphs` を `gremlin.cosmosdb` に変更します。 (グラフ データベース アカウントを 2017 年 12 月 20 日より前に作成した場合は、エンドポイント値に変更を行わず、次の手順に進んでください。)

    endpoint の値は次のようになります。

    `"endpoint": "https://testgraphacct.gremlin.cosmosdb.azure.com:443/"`

4. Azure Portal でコピー ボタンを使って PRIMARY KEY をコピーし、`password: $masterKey$` の `$masterKey$` に貼り付けます。

    *remote.yaml* の 4 行目は次のようになります。 

    `password: 2Ggkr662ifxz2Mg==`

5. *remote.yaml* の 3 行目

    `username: /dbs/$database$/colls/$collection$`

    to 

    `username: /dbs/sample-database/colls/sample-graph`

    サンプルのデータベースまたはグラフに一意の名前を使用した場合は、適宜値を更新してください。

6. *remote.yaml* ファイルを保存します。

## <a name="run-the-console-app"></a>コンソール アプリの実行

1. git ターミナル ウィンドウで、azure-cosmos-db-graph-java-getting-started フォルダーに `cd` で移動します。

    ```git
    cd "C:\git-samples\azure-cosmos-db-graph-java-getting-started"
    ```

2. git ターミナル ウィンドウで、次のコマンドを実行して 必要な Java パッケージをインストールします。

   ```git
   mvn package
   ```

3. git ターミナル ウィンドウで、次のコマンドを実行して Java アプリケーションを起動します。
    
    ```git
    mvn exec:java -D exec.mainClass=GetStarted.Program
    ```

    グラフに追加される頂点がターミナル ウィンドウに表示されます。 
    
    タイムアウト エラーが発生した場合は、[[Update your connection information]\(接続情報の更新\)](#update-your-connection-information) で接続情報が適切に更新されていることを確認し、最後のコマンドを再試行してください。 
    
    プログラムが停止したら、Enter キーを選択し、インターネット ブラウザーで Azure portal に切り替えます。 

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>サンプル データの確認と追加

今度はデータ エクスプローラーに戻って、グラフに追加された頂点を確認し、さらにデータ ポイントを追加してみましょう。

1. Azure portal の Azure Cosmos DB アカウントで **[データ エクスプローラー]** を選択し、**sample-graph** を展開して **[グラフ]** 、 **[フィルターの適用]** の順に選択します。 

   :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-expanded.png" alt-text="A P I から選択された [グラフ] と、[フィルターの適用] オプションを示すスクリーンショット。":::

2. **[結果]** リストを見ると、新しいユーザーがグラフに追加されていることがわかります。 **ben** を選択すると、ユーザーが robin に接続されていることがわかります。 ドラッグ アンド ドロップで頂点を移動したり、マウスのホイールを回して拡大および縮小したり、双方向矢印でグラフのサイズを大きくしたりできます。 

   :::image type="content" source="./media/create-graph-java/azure-cosmosdb-graph-explorer-new.png" alt-text="Azure portal のデータ エクスプローラーにおけるグラフの新しい頂点":::

3. 新しいユーザーを何人か追加してみます。 グラフにデータを追加するには、 **[New Vertex]\(新しい頂点\)** を選択します。

   :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-new-vertex.png" alt-text="[New Vertex]\(新しい頂点\) ペインを示すスクリーンショット。ここで値を入力できます。":::

4. ラベル ボックスに「*person*」と入力します。

5. **[プロパティの追加]** を選択して、次の各プロパティを追加します。 グラフ内の person ごとに一意のプロパティを作成できることに注目してください。 必須のキーは id のみです。

    key|value|Notes
    ----|----|----
    id|ashley|頂点の一意の識別子。 id を指定しなかった場合は、自動的に生成されます。
    gender|female| 
    tech | java | 

    > [!NOTE]
    > このクイック スタートでは、パーティション分割されていないコレクションを作成します。 ただし、コレクションの作成段階でパーティション キーを指定することによって、パーティション分割されたコレクションを作成した場合は、新たに作成する各頂点のキーとして、パーティション キーを追加する必要があります。 

6. **[OK]** を選択します。 画面サイズを大きくしないと、画面下部の **[OK]** が見えない場合があります。

7. もう一度 **[New Vertex]\(新しい頂点\)** を選択して、新しいユーザーを追加します。 

8. 「*person*」というラベルを入力します。

9. **[プロパティの追加]** を選択し、次の各プロパティを追加します。

    key|value|Notes
    ----|----|----
    id|rakesh|頂点の一意の識別子。 id を指定しなかった場合は、自動的に生成されます。
    gender|male| 
    school|MIT| 

10. **[OK]** を選択します。 

11. 既定の `g.V()` フィルターで **[フィルターの適用]** ボタンを選択して、グラフ内のすべての値を表示します。 すると、 **[結果]** リストにすべてのユーザーが表示されます。 

    追加したデータが多くなってきたら、フィルターを使って結果を制限することができます。 既定では、データ エクスプローラーは `g.V()` を使ってグラフのすべての頂点を取得します。 `g.V().count()` などの他の[グラフ クエリ](tutorial-query-graph.md)に変更して、グラフ内のすべての頂点の数を JSON 形式で取得できます。 フィルターを変更した場合、フィルターを `g.V()` に戻して **[フィルターの適用]** を選択し、もう一度すべての結果を表示します。

12. これで rakesh と ashley を接続できる状態になりました。 **[結果]** リストで **[ashley]** が選択されていることを確認し、右下の **[Targets]\(ターゲット\)** の横にある :::image type="content" source="./media/create-graph-java/edit-pencil-button.png" alt-text="[Change the target of a vertex in a graph]\(グラフ内の頂点のターゲットを変更します\)"::: を選択します。 ウィンドウの幅を広げないとボタンが見えない場合があります。

    :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-edit-target.png" alt-text="グラフ内の頂点のターゲットを変更する - Azure CosmosDB":::

13. **[Target]\(ターゲット\)** ボックスに「*rakesh*」と入力し、 **[Edge label]\(辺ラベル\)** ボックスに「*knows*」と入力し、チェック ボックスを選択します。

    :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-set-target.png" alt-text="データ エクスプローラーで接続を追加する - Azure CosmosDB":::

14. 結果リストから **[rakesh]** を選択すると、ashley と rakesh が接続されていることがわかります。 

    :::image type="content" source="./media/create-graph-java/azure-cosmosdb-graph-explorer.png" alt-text="データ エクスプローラーに接続された 2 つの頂点 - Azure CosmosDB":::

以上で、このチュートリアルのリソース作成部分は完了です。 引き続き、グラフへの頂点の追加、既存の頂点の変更、またはクエリの変更を行うことができます。 次に、Azure Cosmos DB が提供するメトリックを確認し、リソースをクリーンアップします。 

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure Cosmos DB アカウントを作成し、データ エクスプローラーを使用してグラフを作成し、グラフにデータを追加する Java アプリを実行する方法を説明しました。 これで Gremlin を使用して、さらに複雑なクエリを作成し、強力なグラフ トラバーサル ロジックを実装できます。 

> [!div class="nextstepaction"]
> [Gremlin を使用したクエリ](tutorial-query-graph.md)

