---
title: 'クイック スタート: Node.js での Table API の使用 - Azure Cosmos DB'
description: このクイックスタートでは、Azure Cosmos DB Table API を使用して Azure Portal と Node.js でアプリケーションを作成する方法を示します
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 04/10/2018
ms.author: sngun
ms.openlocfilehash: 57da4fcb6fb1a91477b6c31387ab06205be8138c
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54044656"
---
# <a name="quickstart-build-a-table-api-app-with-nodejs-and-azure-cosmos-db"></a>クイック スタート: Node.js と Azure Cosmos DB での Table API アプリの構築

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](create-table-python.md)
> 

このクイックスタートでは、GitHub から例を複製することで、Node.js と Azure Cosmos DB の [Table API](table-introduction.md) を使用してアプリを構築する方法を示します。 このクイックスタートでは、Web ベースの Azure Portal で Azure Cosmos DB アカウントを作成する方法、およびデータ エクスプローラーを使用してテーブルとエンティティを作成する方法も説明します。

Azure Cosmos DB は、Microsoft のグローバルに配布されるマルチモデル データベース サービスです。 Azure Cosmos DB の中核をなすグローバルな分散と水平方向のスケール機能を利用して、ドキュメント、キー/値、ワイドカラム、およびグラフ データベースをすばやく作成およびクエリできます。 

## <a name="prerequisites"></a>前提条件

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

加えて次の作業を行います。

* [Node.js](https://nodejs.org/en/) バージョン v0.10.29 以降
* [Git](https://git-scm.com/)

## <a name="create-a-database-account"></a>データベース アカウントの作成

> [!IMPORTANT] 
> 新しいテーブル API アカウントを作成して一般公開のテーブル API SDK を操作する必要があります。 プレビュー期間中に作成されたテーブル API アカウントは、一般公開の SDK ではサポートされません。
>

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>テーブルの追加

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>サンプル データの追加

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

GitHub で Table アプリの複製を作成し、接続文字列を設定して実行します。 プログラムでデータを処理することが非常に簡単であることがわかります。 

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
    git clone https://github.com/Azure-Samples/storage-table-node-getting-started.git
    ```

## <a name="update-your-connection-string"></a>接続文字列を更新する

ここで Azure Portal に戻り、接続文字列情報を取得し、アプリにコピーします。 これでアプリが、ホストされているデータベースと通信できます。 

1. [Azure Portal](https://portal.azure.com/) で **[接続文字列]** をクリックします。 

    ![[接続文字列] ウィンドウに表示されている必要な接続文字列情報をコピーします。](./media/create-table-nodejs/connection-string.png)

2. 右側にある [コピー] ボタンを使って、プライマリ接続文字列をコピーします。

3. app.config ファイルを開き、3 行目の connectionString に値を貼り付けます。 

    > [!IMPORTANT]
    > エンドポイントで documents.azure.com を使用している場合は、プレビュー アカウントを持っていることになるため、[新しいテーブル API](#create-a-database-account) アカウントを作成して、一般公開のテーブル API SDK を操作する必要があります。
    >

3. app.config ファイルを保存します。

これで、Azure Cosmos DB と通信するために必要なすべての情報でアプリを更新しました。 

## <a name="run-the-app"></a>アプリの実行

1. git ターミナル ウィンドウで、storage-table-java-getting-started フォルダーに `cd` で移動します。

    ```
    cd "C:\git-samples\storage-table-node-getting-started"
    ```

2. 次のコマンドを実行して、[azure]、[node-uuid]、[nconf]、および [async] モジュールをローカルにインストールし、これらのモジュールのエントリを package.json ファイルに保存します。

   ```
   npm install azure-storage node-uuid async nconf --save
   ```

2. git ターミナル ウィンドウで、次のコマンドを実行して Node アプリケーションを起動します。

    ```
    node ./tableSample.js 
    ```

    コンソール ウィンドウに、Azure Cosmos DB の新しいテーブル データベースに追加されるテーブル データが表示されます。

    これで、データ エクスプローラーに戻って、この新しいデータの表示、クエリ、変更、操作を行うことができます。 

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Azure Cosmos DB アカウントを作成し、データ エクスプローラーを使用してテーブルを作成し、アプリを実行する方法を説明しました。  これで、Table API を使用して、データをクエリできます。  

> [!div class="nextstepaction"]
> [テーブル データを Table API にインポートする](table-import.md)
