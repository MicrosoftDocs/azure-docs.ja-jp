---
title: "Azure Cosmos DB: Python と DocumentDB API によるアプリの構築 | Microsoft Docs"
description: "Azure Cosmos DB DocumentDB API への接続とクエリに使用できる Python コード サンプルについて説明します"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 51c11be2-af6d-425f-a86a-39cbfe61da29
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 05/13/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 26694432663feb0f9cd21573e9114007b2357528
ms.contentlocale: ja-jp
ms.lasthandoff: 06/01/2017

---
# <a name="azure-cosmos-db-build-a-documentdb-api-app-with-python-and-the-azure-portal"></a>Azure Cosmos DB: Python と Azure Portal による DocumentDB API アプリの構築

Azure Cosmos DB は、Microsoft のグローバルに分散されたマルチモデル データベース サービスです。 Azure Cosmos DB の中核をなすグローバルな分散と水平方向のスケール機能を利用して、ドキュメント、キー/値、およびグラフ データベースをすばやく作成およびクエリできます。 

このクイック スタートでは、Azure Portal を使用して、Azure Cosmos DB アカウント、ドキュメント データベース、コレクションを作成する方法を説明します。 さらに、[DocumentDB Python API](documentdb-sdk-python.md) に基づいたコンソール アプリを構築して実行します。

## <a name="prerequisites"></a>前提条件

* このサンプルを実行する前に、以下の前提条件を満たしている必要があります。
    * [Visual Studio 2015](http://www.visualstudio.com/) 以降。
    * Python Tools for Visual Studio ( [GitHub](http://microsoft.github.io/PTVS/)から入手できます)。 このチュートリアルでは、Python Tools for VS 2015 を使用します。
    * Python 2.7 ([python.org](https://www.python.org/downloads/release/python-2712/) から入手できます)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>データベース アカウントの作成

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>コレクションの追加

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

github から DocumentDB API アプリを複製し、接続文字列を設定して実行します。 プログラムでデータを処理することが非常に簡単であることがわかります。 

1. git ターミナル ウィンドウ (git bash など) を開き、`cd` を実行して作業ディレクトリに移動します。  

2. 次のコマンドを実行して、サンプル レポジトリを複製します。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-documentdb-python-getting-started.git
    ```  
## <a name="review-the-code"></a>コードの確認

アプリで何が行われているかを簡単に確認してみましょう。 DocumentDBGetStarted.py ファイルを開くと、これらのコード行によって、Azure Cosmos DB リソースが作成されることがわかります。 


* DocumentClient が初期化されます。

    ```python
    # Initialize the Python DocumentDB client
    client = document_client.DocumentClient(config['ENDPOINT'], {'masterKey': config['MASTERKEY']})
    ```

* 新しいデータベースが作成されます。

    ```python
    # Create a database
    db = client.CreateDatabase({ 'id': config['DOCUMENTDB_DATABASE'] })
    ```

* 新しいコレクションが作成されます。

    ```python
    # Create collection options
    options = {
        'offerEnableRUPerMinuteThroughput': True,
        'offerVersion': "V2",
        'offerThroughput': 400
    }

    # Create a collection
    collection = client.CreateCollection(db['_self'], { 'id': config['DOCUMENTDB_COLLECTION'] }, options)
    ```

* いくつかのドキュメントが作成されます。

    ```python
    # Create some documents
    document1 = client.CreateDocument(collection['_self'],
        { 
            'id': 'server1',
            'Web Site': 0,
            'Cloud Service': 0,
            'Virtual Machine': 0,
            'name': 'some' 
        })
    ```

* SQL を使用して、クエリが実行されます

    ```python
    # Query them in SQL
    query = { 'query': 'SELECT * FROM server s' }    
            
    options = {} 
    options['enableCrossPartitionQuery'] = True
    options['maxItemCount'] = 2

    result_iterable = client.QueryDocuments(collection['_self'], query, options)
    results = list(result_iterable);

    print(results)
    ```

## <a name="update-your-connection-string"></a>接続文字列の更新

ここで Azure Portal に戻り、接続文字列情報を取得し、アプリにコピーします。

1. [Azure Portal](http://portal.azure.com/) で、Azure Cosmos DB アカウントの左のナビゲーションから、**[キー]** をクリックし、**[読み取り/書き込みキー]** をクリックします。 次の手順では、画面右側のコピー ボタンを使用して、URI とプライマリ キーを `DocumentDBGetStarted.py` ファイルにコピーします。

    ![Azure Portal の [キー] ブレードでアクセス キーを表示およびコピーする](./media/create-documentdb-dotnet/keys.png)

2. `DocumentDBGetStarted.py` ファイルを開きます。 

3. ポータルから (コピー ボタンを使用して) URI 値をコピーし、`DocumentDBGetStarted.py` の endpoint キーの値に設定します。 

    `config.ENDPOINT : "https://FILLME.documents.azure.com"`

4. 次に、ポータルから PRIMARY KEY 値をコピーし、`DocumentDBGetStarted.py` 内の `config.MASTERKEY` の値に設定します。 これで、Azure Cosmos DB と通信するために必要なすべての情報でアプリを更新しました。 

    `config.MASTERKEY : "FILLME"`
    
## <a name="run-the-app"></a>アプリの実行
1. Visual Studio の**ソリューション エクスプローラー**でプロジェクトを右クリックし、現在の Python 環境を選択して、右クリックします。

2. [Install Python Package] \(Python パッケージのインストール) を選択し、「**pydocumentdb**」と入力します

3. F5 キーを押してアプリケーションを実行します。 ブラウザーにアプリが表示されます。 

これで、データ エクスプローラーに戻って、この新しいデータの表示、クエリ、変更、操作を行うことができます。 

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このアプリの使用を続けない場合は、以下の手順に従い、Azure Portal でこのクイック スタートで作成したすべてのリソースを削除してください。

1. Azure Portal の左側のメニューで、**[リソース グループ]** をクリックし、作成したリソースの名前をクリックします。 
2. リソース グループのページで **[削除]** をクリックし、削除するリソースの名前をテキスト ボックスに入力してから **[削除]** をクリックします。

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Azure Cosmos DB アカウントを作成し、データ エクスプローラーを使用してコレクションを作成し、アプリを実行する方法を説明しました。 これで、Cosmos DB アカウントに追加のデータをインポートできます。 

> [!div class="nextstepaction"]
> [DocumentDB API 用に Azure Cosmos DB にデータをインポートする](import-data.md)



