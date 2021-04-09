---
title: Gremlin API を使用して Azure Cosmos DB Node.js アプリケーションを構築する
description: Azure Cosmos DB への接続とデータの照会に使用できる Node.js コード サンプルについて説明します
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/05/2019
ms.author: chrande
ms.custom: devx-track-js
ms.openlocfilehash: 0b88456bd245f79c38d7829dca76de702f768c06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93360141"
---
# <a name="quickstart-build-a-nodejs-application-by-using-azure-cosmos-db-gremlin-api-account"></a>クイック スタート:Azure Cosmos DB Gremlin API アカウントを使用して Node.js アプリケーションをビルドする
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

> [!div class="op_single_selector"]
> * [Gremlin コンソール](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

このクイックスタートでは、Azure portal から Azure Cosmos DB の Gremlin (グラフ) API アカウントを作成して管理し、GitHub から複製された Node.js アプリを使用してデータを追加します。 Azure Cosmos DB は、マルチモデル データベース サービスです。グローバルな分散と水平方向のスケーリング機能により、ドキュメント データベースやテーブル データベース、キーと値のデータベース、グラフ データベースをすばやく作成し、クエリを実行することができます。

## <a name="prerequisites"></a>前提条件
- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 
- [Node.js 0.10.29+](https://nodejs.org/)。
- [Git](https://git-scm.com/downloads).

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started.git
    ```

3. Visual Studio でソリューション ファイルを開きます。 

## <a name="review-the-code"></a>コードの確認

この手順は省略可能です。 コード内のデータベース リソースの作成方法に関心がある場合は、次のスニペットを確認できます。 関心がない場合は、「[接続文字列の更新](#update-your-connection-string)」に進んでください。 

次のスニペットはすべて *app.js* ファイルからのものです。

このコンソール アプリでは、オープンソースの [Gremlin Node.js](https://www.npmjs.com/package/gremlin) ドライバーが使用されます。

* Gremlin クライアントが作成されます。

    ```javascript
    const authenticator = new Gremlin.driver.auth.PlainTextSaslAuthenticator(
        `/dbs/${config.database}/colls/${config.collection}`, 
        config.primaryKey
    )


    const client = new Gremlin.driver.Client(
        config.endpoint, 
        { 
            authenticator,
            traversalsource : "g",
            rejectUnauthorized : true,
            mimeType : "application/vnd.gremlin-v2.0+json"
        }
    );

    ```

  構成はすべて、*config.js* に含まれています。これは [次のセクション](#update-your-connection-string)で編集します。

* さまざまな Gremlin 操作を実行する一連の関数が定義されています。 その一例を次に示します。

    ```javascript
    function addVertex1()
    {
        console.log('Running Add Vertex1'); 
        return client.submit("g.addV(label).property('id', id).property('firstName', firstName).property('age', age).property('userid', userid).property('pk', 'pk')", {
                label:"person",
                id:"thomas",
                firstName:"Thomas",
                age:44, userid: 1
            }).then(function (result) {
                    console.log("Result: %s\n", JSON.stringify(result));
            });
    }
    ```

* 各関数は、Gremlin クエリ文字列パラメーターが指定された `client.execute` メソッドを実行します。 `g.V().count()` の実行方法の例を次に示します。

    ```javascript
    function countVertices()
    {
        console.log('Running Count');
        return client.submit("g.V().count()", { }).then(function (result) {
            console.log("Result: %s\n", JSON.stringify(result));
        });
    }
    ```

* このファイルの最後で、すべてのメソッドが呼び出されます。 すべてのメソッドは順に実行されます。

    ```javascript
    client.open()
    .then(dropGraph)
    .then(addVertex1)
    .then(addVertex2)
    .then(addEdge)
    .then(countVertices)
    .catch((err) => {
        console.error("Error running query...");
        console.error(err)
    }).then((res) => {
        client.close();
        finish();
    }).catch((err) => 
        console.error("Fatal error:", err)
    );
    ```


## <a name="update-your-connection-string"></a>接続文字列を更新する

1. *config.js* ファイルを開きます。 

2. *config.js* の `config.endpoint` キーに、Azure portal の Cosmos DB アカウントで **[概要]** ページに表示される **[Gremlin エンドポイント]** の値を設定します。 

    `config.endpoint = "https://<your_Gremlin_account_name>.gremlin.cosmosdb.azure.com:443/";`

    :::image type="content" source="./media/create-graph-nodejs/gremlin-uri.png" alt-text="Azure portal の [概要] ページでアクセス キーを表示およびコピーする":::

3. *config.js* で、config.primaryKey の値として、Azure portal の Cosmos DB アカウントの **[キー]** ページから **[プライマリ キー]** の値を入力します。 

    `config.primaryKey = "PRIMARYKEY";`

   :::image type="content" source="./media/create-graph-nodejs/keys.png" alt-text="Azure portal キー ブレード":::

4. データベース名とグラフ (コンテナー) 名を config.database と config.collection の値として入力します。 

たとえば、完成した *config.js* ファイルの内容は次のようになります。

```javascript
var config = {}

// Note that this must include the protocol (HTTPS:// for .NET SDK URI or wss:// for Gremlin Endpoint) and the port number
config.endpoint = "https://testgraphacct.gremlin.cosmosdb.azure.com:443/"; 
config.primaryKey = "Pams6e7LEUS7LJ2Qk0fjZf3eGo65JdMWHmyn65i52w8ozPX2oxY3iP0yu05t9v1WymAHNcMwPIqNAEv3XDFsEg==";
config.database = "graphdb"
config.collection = "Persons"

module.exports = config;
```

## <a name="run-the-console-app"></a>コンソール アプリの実行

1. ターミナル ウィンドウを開き、`cd` コマンドを使用して、プロジェクトに含まれる *package.json* ファイルのインストール ディレクトリに移動します。

2. `npm install` を実行し、`gremlin`など、必要な npm モジュールをインストールします。

3. ターミナルで `node app.js` を実行し、node アプリケーションを起動します。

## <a name="browse-with-data-explorer"></a>データ エクスプローラーで閲覧する

ここで、Azure Portal のデータ エクスプローラーに戻り、新しいグラフ データの表示、クエリ実行、変更、使用を行うことができます。

データ エクスプローラーで新しいデータベースが **[グラフ]** ウィンドウに表示されます。 データベース、コンテナーの順に展開し、 **[グラフ]** を選択します。

**[フィルターの適用]** を選択すると、サンプル アプリで生成されたデータが、 **[グラフ]** タブ内にある隣のウィンドウに表示されます。

試しに `g.V()` に「`.has('firstName', 'Thomas')`」と入力して、フィルターをテストします。 値の大文字と小文字が区別されることに注意してください。

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-your-resources"></a>リソースをクリーンアップする

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>次のステップ

この記事では、Azure Cosmos DB アカウントを作成し、データ エクスプローラーを使用してグラフを作成し、Node.js アプリを実行してグラフにデータを追加する方法を説明しました。 これで Gremlin を使用して、さらに複雑なクエリを作成し、強力なグラフ トラバーサル ロジックを実装できます。 

> [!div class="nextstepaction"]
> [Gremlin を使用したクエリ](tutorial-query-graph.md)
