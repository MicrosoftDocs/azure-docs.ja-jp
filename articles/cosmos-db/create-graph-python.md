---
title: クイック スタート:Python での Gremlin API の使用 - Azure Cosmos DB
description: このクイック スタートでは、Azure portal および Python で Azure Cosmos DB Gremlin API を使ってコンソール アプリケーションを作成する方法を説明します
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: python
ms.topic: quickstart
ms.date: 03/29/2021
ms.author: chrande
ms.custom: devx-track-python
ms.openlocfilehash: cef397789d5ebcfa95c01e42dac9a80b9e1564e0
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106944"
---
# <a name="quickstart-create-a-graph-database-in-azure-cosmos-db-using-python-and-the-azure-portal"></a>クイック スタート:Python と Azure portal を使用して Azure Cosmos DB にグラフ データベースを作成する
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

> [!div class="op_single_selector"]
> * [Gremlin コンソール](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

このクイックスタートでは、Azure portal から Azure Cosmos DB の Gremlin (グラフ) API アカウントを作成、管理し、GitHub からクローンした Python アプリを使用してデータを追加します。 Azure Cosmos DB は、マルチモデル データベース サービスです。グローバルな分散と水平方向のスケーリング機能を備えたドキュメント データベースやテーブル データベース、キーと値のデータベース、グラフ データベースをすばやく作成し、クエリを実行することができます。

## <a name="prerequisites"></a>前提条件
- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 または、Azure サブスクリプションなしで、[Azure Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)こともできます。
- [Python 3.6 以降](https://www.python.org/downloads/) ([pip](https://pip.pypa.io/en/stable/installing/) パッケージ インストーラーを含む)。
- [Gremlin 用 Python ドライバー](https://github.com/apache/tinkerpop/tree/master/gremlin-python)。
- [Git](https://git-scm.com/downloads).

> [!NOTE]
> このクイックスタートには、2017 年 12 月 20 日以降に作成したグラフ データベース アカウントが必要です。 既存のアカウントは、一般公開に移行された後で Python をサポートするようになります。

## <a name="create-a-database-account"></a>データベース アカウントの作成

グラフ データベースを作成するには、Azure Cosmos DB を含んだ Gremlin (グラフ) データベース アカウントを事前に作成しておく必要があります。

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>グラフの追加

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>サンプル アプリケーションのクローン

次は、コードを使った作業に移りましょう。 GitHub から Gremlin API アプリのクローンを作成し、接続文字列を設定して実行します。 プログラムでデータを処理することが非常に簡単であることがわかります。  

1. コマンド プロンプトを開いて git-samples という名前の新しいフォルダーを作成し、コマンド プロンプトを閉じます。

    ```bash
    mkdir "./git-samples"
    ```

2. git bash などの git ターミナル ウィンドウを開き、`cd` コマンドを使用して、サンプル アプリをインストールするフォルダーに変更します。  

    ```bash
    cd "./git-samples"
    ```

3. 次のコマンドを実行して、サンプル レポジトリをクローンします。 このコマンドは、コンピューター上にサンプル アプリのコピーを作成します。

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-python-getting-started.git
    ```

## <a name="review-the-code"></a>コードの確認

この手順は省略可能です。 コード内のデータベース リソースの作成方法に関心がある場合は、次のスニペットを確認できます。 スニペットはすべて、*C:\git-samples\azure-cosmos-db-graph-python-getting-started\\* フォルダーの *connect.py* ファイルからのものです。 関心がない場合は、「[接続文字列の更新](#update-your-connection-information)」に進んでください。

* Gremlin の `client` は *connect.py* の 155 行目で初期化されています。 必ず `<YOUR_DATABASE>` と `<YOUR_CONTAINER_OR_GRAPH>` を自分のアカウントのデータベース名とグラフ名の値で置き換えてください。

    ```python
    ...
    client = client.Client('wss://<YOUR_ENDPOINT>.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_CONTAINER_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ...
    ```

* *connect.py* ファイルの先頭で、Gremlin の一連のステップが宣言されています。 これらは、`client.submitAsync()` メソッドを使って実行されます。

    ```python
    client.submitAsync(_gremlin_cleanup_graph)
    ```

## <a name="update-your-connection-information"></a>接続情報の更新

ここで Azure Portal に戻り、接続情報を取得して、アプリにコピーします。 これらの設定により、アプリはホストされているデータベースと通信できるようになります。

1. [Azure portal](https://portal.azure.com/) の Azure Cosmos DB アカウントで、 **[キー]** を選択します。 

    URI の値の最初の部分をコピーします。

    :::image type="content" source="./media/create-graph-python/keys.png" alt-text="Azure portal の [キー] ページでアクセス キーを表示およびコピーする":::

2. *connect.py* ファイルを開き、155 行目の `<YOUR_ENDPOINT>` に URI の値を貼り付けます。

    ```python
    client = client.Client('wss://<YOUR_ENDPOINT>.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ```

    クライアント オブジェクトの URI 部分は、次のコードにようになります。

    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/<YOUR_DATABASE>/colls/<YOUR_COLLECTION_OR_GRAPH>", 
        password="<YOUR_PASSWORD>")
    ```

3. `client` オブジェクトの 2 番目のパラメーターを、`<YOUR_DATABASE>` と `<YOUR_COLLECTION_OR_GRAPH>` の文字列に置き換えます。 提案された値を使った場合、パラメーターは次のコードのようになります。

    `username="/dbs/sample-database/colls/sample-graph"`

    `client` オブジェクト全体は、次のコードのようになります。

    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/sample-database/colls/sample-graph", 
        password="<YOUR_PASSWORD>")
    ```

4. **[キー]** ページでコピー ボタンを使って PRIMARY KEY をコピーし、`password=<YOUR_PASSWORD>` パラメーターの `<YOUR_PASSWORD>` に貼り付けます。

    `client` オブジェクトの定義全体は、次のコードのようになります。
    ```python
    client = client.Client('wss://test.gremlin.cosmosdb.azure.com:443/','g', 
        username="/dbs/sample-database/colls/sample-graph", 
        password="asdb13Fadsf14FASc22Ggkr662ifxz2Mg==")
    ```

6. *connect.py* ファイルを保存します。

## <a name="run-the-console-app"></a>コンソール アプリの実行

1. git ターミナル ウィンドウで、azure-cosmos-db-graph-python-getting-started フォルダーに `cd` で移動します。

    ```git
    cd "./git-samples\azure-cosmos-db-graph-python-getting-started"
    ```

2. git ターミナル ウィンドウで、次のコマンドを実行して 必要な Python パッケージをインストールします。

   ```
   pip install -r requirements.txt
   ```

3. git ターミナル ウィンドウで、次のコマンドを実行して Python アプリケーションを起動します。
    
    ```
    python connect.py
    ```

    グラフに追加される頂点と辺がターミナル ウィンドウに表示されます。 
    
    タイムアウト エラーが発生した場合は、[[Update your connection information]\(接続情報の更新\)](#update-your-connection-information) で接続情報が適切に更新されていることを確認し、最後のコマンドを再試行してください。 
    
    プログラムが停止したら、Enter キーを押して、インターネット ブラウザーで Azure Portal に切り替えます。

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>サンプル データの確認と追加

頂点とエッジが挿入されたら、データ エクスプローラーに戻り、グラフに追加された頂点を確認して、データ ポイントを追加することができます。

1. Azure portal の Azure Cosmos DB アカウントで **[データ エクスプローラー]** を選択し、**sample-graph** を展開して **[グラフ]** 、 **[フィルターの適用]** の順に選択します。 

   :::image type="content" source="./media/create-graph-python/azure-cosmosdb-data-explorer-expanded.png" alt-text="A P I から選択された [グラフ] と、[フィルターの適用] オプションを示すスクリーンショット。":::

2. **[結果]** リストを見ると、3 人の新しいユーザーがグラフに追加されていることがわかります。 ドラッグ アンド ドロップで頂点を移動したり、マウスのホイールを回して拡大および縮小したり、双方向矢印でグラフのサイズを大きくしたりできます。 

   :::image type="content" source="./media/create-graph-python/azure-cosmosdb-graph-explorer-new.png" alt-text="Azure portal のデータ エクスプローラーにおけるグラフの新しい頂点":::

3. 新しいユーザーを何人か追加してみます。 グラフにデータを追加するには、 **[New Vertex]\(新しい頂点\)** ボタンを選択します。

   :::image type="content" source="./media/create-graph-python/azure-cosmosdb-data-explorer-new-vertex.png" alt-text="[New Vertex]\(新しい頂点\) ペインを示すスクリーンショット。ここで値を入力できます。":::

4. 「*person*」というラベルを入力します。

5. **[プロパティの追加]** を選択して、次の各プロパティを追加します。 グラフ内の person ごとに一意のプロパティを作成できることに注目してください。 必須のキーは id のみです。

    key|value|Notes
    ----|----|----
    pk|/pk| 
    id|ashley|頂点の一意の識別子。 id を指定しなかった場合は、自動的に生成されます。
    gender|female| 
    tech | java | 

    > [!NOTE]
    > このクイックスタートでは、パーティション分割されていないコレクションを作成します。 ただし、コレクションの作成段階でパーティション キーを指定することによって、パーティション分割されたコレクションを作成した場合は、新たに作成する各頂点のキーとして、パーティション キーを追加する必要があります。 

6. **[OK]** を選択します。 画面サイズを大きくしないと、画面下部の **[OK]** が見えない場合があります。

7. もう一度 **[New Vertex]\(新しい頂点\)** を選択して、新しいユーザーを追加します。 

8. 「*person*」というラベルを入力します。

9. **[プロパティの追加]** を選択し、次の各プロパティを追加します。

    key|value|Notes
    ----|----|----
    pk|/pk| 
    id|rakesh|頂点の一意の識別子。 id を指定しなかった場合は、自動的に生成されます。
    gender|male| 
    school|MIT| 

10. **[OK]** を選択します。 

11. 既定の `g.V()` フィルターで **[フィルターの適用]** ボタンを選択して、グラフ内のすべての値を表示します。 すると、 **[結果]** リストにすべてのユーザーが表示されます。 

    追加したデータが多くなってきたら、フィルターを使って結果を制限することができます。 既定では、データ エクスプローラーは `g.V()` を使ってグラフのすべての頂点を取得します。 `g.V().count()` などの他の[グラフ クエリ](tutorial-query-graph.md)に変更して、グラフ内のすべての頂点の数を JSON 形式で取得できます。 フィルターを変更した場合、フィルターを `g.V()` に戻して **[フィルターの適用]** を選択し、もう一度すべての結果を表示します。

12. これで rakesh と ashley を接続できる状態になりました。 **[結果]** リストで **[ashley]** が選択されていることを確認し、右下の **[Targets]\(ターゲット\)** の横にある編集ボタンを選択します。 ウィンドウの幅を広げないと **[プロパティ]** 領域が見えない場合があります。

    :::image type="content" source="./media/create-graph-python/azure-cosmosdb-data-explorer-edit-target.png" alt-text="グラフ内の頂点のターゲットを変更する":::

13. **[Target]\(ターゲット\)** ボックスに「*rakesh*」と入力し、 **[Edge label]\(辺ラベル\)** ボックスに「*knows*」と入力して、チェック ボックスをオンにします。

    :::image type="content" source="./media/create-graph-python/azure-cosmosdb-data-explorer-set-target.png" alt-text="データ エクスプローラーで ashley と rakesh との間の接続を追加する":::

14. 結果リストから **[rakesh]** を選択すると、ashley と rakesh が接続されていることがわかります。 

    :::image type="content" source="./media/create-graph-python/azure-cosmosdb-graph-explorer.png" alt-text="データ エクスプローラーで接続されている 2 つの頂点":::

以上で、このチュートリアルのリソース作成部分は完了です。 引き続き、グラフへの頂点の追加、既存の頂点の変更、またはクエリの変更を行うことができます。 次に、Azure Cosmos DB が提供するメトリックを確認し、リソースをクリーンアップします。 

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure Cosmos DB アカウントを作成し、データ エクスプローラーを使用してグラフを作成し、グラフにデータを追加する Python アプリを実行する方法を説明しました。 これで Gremlin を使用して、さらに複雑なクエリを作成し、強力なグラフ トラバーサル ロジックを実装できます。 

> [!div class="nextstepaction"]
> [Gremlin を使用したクエリ](tutorial-query-graph.md)

