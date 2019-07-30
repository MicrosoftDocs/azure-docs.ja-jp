---
title: Azure Cosmos DB:Python と SQL API によるアプリの構築
description: Azure Cosmos DB SQL API への接続とクエリに使用できる Python コード サンプルについて説明します
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: d85b156e9c6d213b6f65fe738f5d22c8cce022ee
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68001195"
---
# <a name="azure-cosmos-db-build-a-python-application-using-azure-cosmos-db-sql-api-account"></a>Azure Cosmos DB:Azure Cosmos DB SQL API アカウントを使用して Python アプリケーションを構築する

> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Azure Cosmos DB、Microsoft のグローバルに配布されるマルチモデル データベース サービスです。 ドキュメント、キー/値、ワイド カラム、およびグラフ データベースをすばやく作成し、クエリを実行できます。 そのすべての操作に、Azure Cosmos DB が備える分散とスケールは利益をもたらします。

このクイック スタートでは、Azure portal を使用して、Azure Cosmos DB [SQL API](sql-api-introduction.md) アカウント、ドキュメント データベース、コンテナーを作成する方法を説明します。 その後、[SQL API](sql-api-sdk-python.md) 用の Python SDK で構築されたコンソール アプリを構築して実行します。 このクイック スタートでは、[Python SDK](https://pypi.org/project/azure-cosmos) のバージョン 3.0 を使用します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="prerequisites"></a>前提条件

* [Python 3.6](https://www.python.org/downloads/) (`python` 実行可能ファイルが `PATH` に設定されていること)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Visual Studio Code 用の Python 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview)

## <a name="create-a-database-account"></a>データベース アカウントの作成

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>コンテナーの追加

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>サンプル データの追加

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>データのクエリ

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

では、GitHub から SQL API アプリを複製し、接続文字列を設定して実行しましょう。

1. コマンド プロンプトを開いて git-samples という名前の新しいフォルダーを作成し、コマンド プロンプトを閉じます。

    ```cmd
    md "git-samples"
    ```
   bash プロンプトを使用している場合は、代わりに次のコマンドを使用する必要があります。

   ```bash
   mkdir "git-samples"
   ```

2. git bash などの git ターミナル ウィンドウを開いて、`cd` コマンドを使用して、サンプル アプリをインストールする新しいフォルダーに変更します。

    ```bash
    cd "git-samples"
    ```

3. 次のコマンドを実行して、サンプル レポジトリを複製します。 このコマンドは、コンピューター上にサンプル アプリのコピーを作成します。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-python-getting-started.git
    ```  

## <a name="update-your-connection-string"></a>接続文字列を更新する

ここで Azure Portal に戻り、接続文字列情報を取得し、アプリにコピーします。

1. [Azure portal](https://portal.azure.com/) で Azure Cosmos アカウントにアクセスし、左側のナビゲーションにある **[キー]** をクリックします。 次の手順では、画面の右側にあるコピー ボタンを使用して、 **[URI]** と **[主キー]** を `CosmosGetStarted.py` ファイルにコピーします。

    ![Azure Portal の [キー] ブレードでアクセス キーを表示およびコピーする](./media/create-sql-api-dotnet/keys.png)

2. Visual Studio Code で \git-samples\azure-cosmos-db-python-getting-started 内の `CosmosGetStarted.py` ファイルを開きます。

3. ポータルから (コピー ボタンを使用して) **URI** 値をコピーし、それを ``CosmosGetStarted.py`` 内の **endpoint** キーの値にします。 

    `'ENDPOINT': 'https://FILLME.documents.azure.com',`

4. 次に、ポータルから **PRIMARY KEY** 値をコピーし、それを ``CosmosGetStarted.py`` 内の **config.PRIMARYKEY** の値にします。 これで、Azure Cosmos DB と通信するために必要なすべての情報でアプリを更新しました。 

    `'PRIMARYKEY': 'FILLME',`

5. ``CosmosGetStarted.py`` ファイルを保存します。

## <a name="review-the-code"></a>コードの確認

この手順は省略可能です。 コードで作成されたデータベース リソースについて確認するか、または省略して「[接続文字列を更新する](#update-your-connection-string)」に進んでください。

以前のバージョンの Python SDK に精通している場合は、"コレクション" や "ドキュメント" という用語をよく目にしたことに注意してください。 Azure Cosmos DB は複数の API モデルをサポートしているため、Python SDK のバージョン 3.0 以上では、コレクション、グラフ、またはテーブルを示すために一般的な用語である "コンテナー" を、またコンテナーのコンテンツを示すために "項目" を使用します。

次のスニペットはすべて、`CosmosGetStarted.py` ファイルからのものです。

* CosmosClient が初期化されます。 「[接続文字列を更新する](#update-your-connection-string)」セクションの説明に従って "Endpoint" と "master key" の値を更新します。 

    ```python
    # Initialize the Cosmos client
    client = cosmos_client.CosmosClient(url_connection=config['ENDPOINT'], auth={'masterKey': config['MASTERKEY']})
    ```

* 新しいデータベースが作成されます。

    ```python
    # Create a database
    db = client.CreateDatabase({ 'id': config['DATABASE'] })
    ```

* 新しいコレクションが作成されます。

    ```python
    # Create collection options
    options = {
        'offerThroughput': 400
    }

    # Create a container
    container = client.CreateContainer(db['_self'], container_definition, options)
    ```

* いくつかの項目がコンテナーに追加されます。

    ```python
    # Create and add some items to the container
    item1 = client.CreateItem(container['_self'], {
        'serverId': 'server1',
        'Web Site': 0,
        'Cloud Service': 0,
        'Virtual Machine': 0,
        'message': 'Hello World from Server 1!'
        }
    )

    item2 = client.CreateItem(container['_self'], {
        'serverId': 'server2',
        'Web Site': 1,
        'Cloud Service': 0,
        'Virtual Machine': 0,
        'message': 'Hello World from Server 2!'
        }
    )
    ```

* SQL を使用して、クエリが実行されます

    ```python
    query = {'query': 'SELECT * FROM server s'}

    options = {}
    options['enableCrossPartitionQuery'] = True
    options['maxItemCount'] = 2

    result_iterable = client.QueryItems(container['_self'], query, options)
    for item in iter(result_iterable):
        print(item['message'])
    ```
   
## <a name="run-the-app"></a>アプリの実行

1. Visual Studio Code で、 **[ビュー]** > **[コマンド パレット]** の順に選択します。 

2. プロンプトで「**Python: Select Interpreter**」と入力し、使用する Python のバージョンを選択します。

    Visual Studio Code のフッターが、選択されているインタープリターを示すように更新されます。 

3. **[表示]**  >  **[統合ターミナル]** の順に選択して、Visual Studio Code 統合ターミナルを開きます。

4. 統合ターミナル ウィンドウで、azure-cosmos-db-python-getting-started フォルダー内にいることを確認します。 そうでない場合は、次のコマンドを実行して、サンプル フォルダーに切り替えます。 

    ```cmd
    cd "\git-samples\azure-cosmos-db-python-getting-started"`
    ```

5. 次のコマンドを実行して、azure-cosmos パッケージをインストールします。 

    ```python
    pip3 install azure-cosmos
    ```

    azure-cosmos をインストールしようとしたときにアクセスが拒否されたことを示すエラーが表示される場合は、[管理者として VS Code を実行する](https://stackoverflow.com/questions/37700536/visual-studio-code-terminal-how-to-run-a-command-with-administrator-rights)必要があります。

6. 次のコマンドを実行してサンプルを実行し、新しいドキュメントを作成して Azure Cosmos dB に保存します。

    ```python
    python CosmosGetStarted.py
    ```

7. 新しい項目が作成および保存されたことを確認するには、Azure Portal で **[データ エクスプローラー]** を選択し、 **[coll]** 、 **[ドキュメント]** の順に展開して **[server1]** ドキュメントを選択します。 server1 ドキュメントの内容は、統合ターミナル ウィンドウで返される内容と一致します。 

    ![Azure Portal で新しいドキュメントを表示する](./media/create-sql-api-python/azure-cosmos-db-confirm-documents.png)

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>次の手順

このクイックスタートでは、Azure Cosmos アカウントを作成し、データ エクスプローラーを使用してコンテナーを作成し、アプリを実行する方法を説明しました。 これで、Cosmos DB アカウントに追加のデータをインポートできます。 

> [!div class="nextstepaction"]
> [SQL API 用に Azure Cosmos DB にデータをインポートする](import-data.md)


