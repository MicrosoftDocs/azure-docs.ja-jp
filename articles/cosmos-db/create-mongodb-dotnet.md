---
title: Azure Cosmos DB の MongoDB 用 API と .NET SDK を使用して Web アプリを構築する
description: 記載されている .NET のコード サンプルを使用すると、Azure Cosmos DB の MongoDB 用 API を使用して接続とクエリを行うことができます。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/15/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 65ceca7ee0a6482e489ca768b9b33981d313c3db
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101658031"
---
# <a name="quickstart-build-a-net-web-app-using-azure-cosmos-dbs-api-for-mongodb"></a>クイック スタート:Azure Cosmos DB の MongoDB 用 API を使用して .NET Web アプリを構築する 
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](./mongodb-introduction.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Azure Cosmos DB は、あらゆる規模に対応する、オープン API を備えた Microsoft の高速 NoSQL データベースです。 Cosmos DB の中核であるグローバル配布と水平スケーリングの機能を利用して、ドキュメント、キー/値、グラフの各データベースの作成とクエリをすばやく行うことができます。 

このクイック スタートでは、[Azure Cosmos DB の MongoDB 用 API](mongodb-introduction.md) を使用して Cosmos アカウントを作成する方法を示します。 次に、[MongoDB .NET ドライバー](https://docs.mongodb.com/ecosystem/drivers/csharp/)を使用してタスク リスト Web アプリを構築し、デプロイします。

## <a name="prerequisites-to-run-the-sample-app"></a>サンプル アプリを実行するための前提条件

サンプルを実行するためには、[Visual Studio](https://www.visualstudio.com/downloads/) および有効な Azure CosmosDB アカウントが必要です。

まだ Visual Studio を持っていない場合は、 **[ASP.NET および Web の開発]** ワークロードがセットアップと共にインストールされた状態で [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/) をダウンロードします。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

<a id="create-account"></a>
## <a name="create-a-database-account"></a>データベース アカウントの作成

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

この記事で説明されているサンプルは、MongoDB.Driver バージョン 2.6.1 と互換性があります。

## <a name="clone-the-sample-app"></a>サンプル アプリの複製

最初に、GitHub からサンプル アプリをダウンロードします。 

1. コマンド プロンプトを開いて git-samples という名前の新しいフォルダーを作成し、コマンド プロンプトを閉じます。

    ```bash
    mkdir "C:\git-samples"
    ```

2. git bash などの git ターミナル ウィンドウを開いて、`cd` コマンドを使用して、サンプル アプリをインストールする新しいフォルダーに変更します。

    ```bash
    cd "C:\git-samples"
    ```

3. 次のコマンドを実行して、サンプル レポジトリを複製します。 このコマンドは、コンピューター上にサンプル アプリのコピーを作成します。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started.git
    ```

git を使いたくない場合は、[プロジェクトを ZIP ファイルとしてダウンロード](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started/archive/master.zip)することもできます。

## <a name="review-the-code"></a>コードの確認

この手順は省略可能です。 コード内のデータベース リソースの作成方法に関心がある場合は、次のスニペットを確認できます。 関心がない場合は、「[接続文字列の更新](#update-your-connection-string)」に進んでください。 

次のスニペットはすべて DAL ディレクトリ内の Dal.cs ファイルからのものです。

* クライアントを初期化します。

    ```cs
        MongoClientSettings settings = new MongoClientSettings();
        settings.Server = new MongoServerAddress(host, 10255);
        settings.UseSsl = true;
        settings.SslSettings = new SslSettings();
        settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;

        MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
        MongoIdentityEvidence evidence = new PasswordEvidence(password);

        settings.Credential = new MongoCredential("SCRAM-SHA-1", identity, evidence);

        MongoClient client = new MongoClient(settings);
    ```

* データベースとコレクションを取得します。

    ```cs
    private string dbName = "Tasks";
    private string collectionName = "TasksList";

    var database = client.GetDatabase(dbName);
    var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
    ```

* すべてのドキュメントを取得します。

    ```cs
    collection.Find(new BsonDocument()).ToList();
    ```

タスクを作成して、コレクションに挿入します。

   ```csharp
    public void CreateTask(MyTask task)
    {
        var collection = GetTasksCollectionForEdit();
        try
        {
            collection.InsertOne(task);
        }
        catch (MongoCommandException ex)
        {
            string msg = ex.Message;
        }
    }
   ```
   同様に、[collection.UpdateOne()](https://docs.mongodb.com/stitch/mongodb/actions/collection.updateOne/index.html) メソッドと [collection.DeleteOne()](https://docs.mongodb.com/stitch/mongodb/actions/collection.deleteOne/index.html) メソッドを使用して、ドキュメントの更新と削除を実行できます。 

## <a name="update-your-connection-string"></a>接続文字列を更新する

ここで Azure Portal に戻り、接続文字列情報を取得し、アプリにコピーします。

1. [Azure portal](https://portal.azure.com/) で Cosmos アカウントを使用して、左側のナビゲーションにある **[接続文字列]** をクリックし、 **[読み取り/書き込みキー]** をクリックします。 次の手順では、画面右側のコピー ボタンを使用して、ユーザー名、パスワード、ホストを Dal.cs ファイルにコピーします。

2. **DAL** ディレクトリ内の **Dal.cs** ファイルを開きます。 

3. ポータルの **username** 値をコピーし (コピー ボタンを使用して)、**Dal.cs** ファイル内の **username** の値に設定します。 

4. 次に、ポータルの **host** 値をコピーし、**Dal.cs** ファイル内の **host** の値に設定します。 

5. 最後に、ポータルの **password** 値をコピーし、**Dal.cs** ファイル内の **password** の値に設定します。 

これで、Cosmos DB と通信するために必要なすべての情報でアプリを更新しました。 
    
## <a name="run-the-web-app"></a>Web アプリの実行

1. Visual Studio の **ソリューション エクスプローラー** でプロジェクトを右クリックし、 **[NuGet パッケージの管理]** をクリックします。 

2. NuGet の **[参照]** ボックスに「*MongoDB.Driver*」と入力します。

3. 結果から、**MongoDB.Driver** ライブラリをインストールします。 これにより、MongoDB.Driver パッケージとすべての依存関係がインストールされます。

4. Ctrl + F5 キーを押してアプリケーションを実行します。 ブラウザーにアプリが表示されます。 

5. ブラウザーで、 **[作成]** をクリックし、タスク リスト アプリで、いくつか新しいタスクを作成します。

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Cosmos アカウントを作成し、コレクションを作成して、コンソール アプリを実行する方法を学習しました。 これで、Cosmos データベースに追加のデータをインポートできます。 

> [!div class="nextstepaction"]
> [MongoDB データを Azure Cosmos DB にインポートする](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)