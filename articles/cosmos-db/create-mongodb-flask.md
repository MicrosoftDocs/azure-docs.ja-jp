---
title: Azure Cosmos DB の MongoDB 用 API を使用して Python Flask Web アプリを作成する
description: Azure Cosmos DB の MongoDB 用 API を使用して接続とクエリを行うことができる Python Flask のコード サンプルについて説明します。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: python
ms.topic: quickstart
ms.date: 12/26/2018
ms.custom: devx-track-python
ms.openlocfilehash: 1ed15182e700f125072c69ba9bcf60caf4da5c88
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87873043"
---
# <a name="quickstart-build-a-python-app-using-azure-cosmos-dbs-api-for-mongodb"></a>クイック スタート:Azure Cosmos DB の MongoDB 用 API を使用して Python アプリをビルドする

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

このクイックスタートでは、Azure Cosmos DB for Mongo DB API アカウントまたは Azure Cosmos DB Emulator を使用して、GitHub からクローンした Python Flask To-Do Web アプリを実行します。 Azure Cosmos DB は、マルチモデル データベース サービスです。グローバルな分散と水平方向のスケーリング機能を備えたドキュメント データベースやテーブル データベース、キーと値のデータベース、グラフ データベースをすばやく作成し、クエリを実行することができます。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 または、Azure サブスクリプションなしで、[Azure Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)こともできます。 または、[Azure Cosmos DB Emulator](local-emulator.md) を使用することもできます。 
- [Python 3.6 以降](https://www.python.org/downloads/)
- [Visual Studio Code](https://code.visualstudio.com/Download) と [Python 拡張機能](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python)。

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

それでは、GitHub から Flask-MongoDB アプリを複製し、接続文字列を設定して実行しましょう。 プログラムでデータを処理することが非常に簡単であることがわかります。

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
    git clone https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample.git
    ```
3. 次のコマンドを実行して python モジュールをインストールします。

    ```bash 
    pip install -r .\requirements.txt
    ```
4. Visual Studio Code でフォルダーを開きます。

## <a name="review-the-code"></a>コードの確認

この手順は省略可能です。 コード内のデータベース リソースの作成方法に関心がある場合は、次のスニペットを確認できます。 それ以外の場合は、「[Web アプリの実行](#run-the-web-app)」に進んでください。 

次のスニペットはすべて *app.py* ファイルのものであり、ローカル Azure Cosmos DB Emulator の接続文字列を使用しています。 パスワードを次のように分割して、スラッシュに対応する必要があります。そうしないと、スラッシュを解析できません。

* MongoDB クライアントを初期化し、データベースを取得して認証します。

    ```python
    client = MongoClient("mongodb://127.0.0.1:10250/?ssl=true") #host uri
    db = client.test    #Select the database
    db.authenticate(name="localhost",password='C2y6yDjf5' + r'/R' + '+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw' + r'/Jw==')
    ```

* コレクションを取得するか、まだ存在しない場合は作成します。

    ```python
    todos = db.todo #Select the collection
    ```

* アプリを作成する

    ```Python
    app = Flask(__name__)
    title = "TODO with Flask"
    heading = "ToDo Reminder"
    ```
    
## <a name="run-the-web-app"></a>Web アプリの実行

1. Azure Cosmos DB Emulator が実行されていることを確認します。

2. ターミナル ウィンドウを開き、`cd` を実行してアプリが保存されているディレクトリに移動します。

3. 次に、Mac を使用している場合は、`set FLASK_APP=app.py`、PowerShell エディターの `$env:FLASK_APP = app.py`、または `export FLASK_APP=app.py` を使用して Flask アプリの環境変数を設定します。 

4. `flask run` を使用してアプリを実行し、*http:\//127.0.0.1:5000/* を参照します。

5. タスクを追加および削除し、それらがコレクションで追加および変更されたことを確認します。

## <a name="create-a-database-account"></a>データベース アカウントの作成

ライブ Azure Cosmos DB アカウントに対してコードをテストする場合は、Azure portal に移動してアカウントを作成します。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>接続文字列を更新する

ライブ Azure Cosmos DB アカウントに対してコードをテストするために、接続文字列情報を取得します。 これをアプリにコピーします。

1. Azure portal の Azure Cosmos DB アカウントで、左のナビゲーションの **[接続文字列]** を選択してから **[読み取り/書き込みキー]** を選択します。 ユーザー名、接続文字列、パスワードは、画面の右側にあるコピー ボタンを使用してコピーしてください。 

2. ルート ディレクトリ内の *app.py* ファイルを開きます。

3. (コピー ボタンを使用して) ポータルから**ユーザー名**の値をコピーし、*app.py* ファイル内の **name** の値に設定します。

4. 次に、ポータルから**接続文字列**の値をコピーし、それを *app.py* ファイル内の **MongoClient** の値に設定します。

5. 最後に、ポータルから**パスワード**の値をコピーし、*app.py* ファイル内の **password** の値に設定します。

これで、Azure Cosmos DB と通信するために必要なすべての情報でアプリを更新しました。 これまでと同じ方法で実行できます。

## <a name="deploy-to-azure"></a>Deploy to Azure (Azure へのデプロイ)

このアプリをデプロイするには、Azure で新しい Web アプリを作成し、この GitHub リポジトリのフォークで継続的配置を有効にします。 この[チュートリアル](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment)に従って、Azure で GitHub による継続的配置を設定します。

Azure にデプロイする場合は、アプリケーション キーを削除し、以下のセクションがコメント アウトされていないことを確認する必要があります。

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

アプリケーション設定に MONGOURL、MONGO_PASSWORD、MONGO_USERNAME を追加する必要があります。 この[チュートリアル](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings)に従って、Azure Web Apps のアプリケーション設定の詳細を確認できます。

このリポジトリのフォークを作成しない場合は、下の **[Azure へのデプロイ]** をクリックすることもできます。 Azure に移動し、Azure Cosmos DB アカウント情報でアプリケーション設定を設定する必要があります。

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="https://azuredeploy.net/deploybutton.png" alt="Click to Deploy to Azure">
</a>

> [!NOTE]
> GitHub またはその他のソース管理オプションでコードを格納することを計画している場合は、必ずコードから接続文字列を削除してください。 代わりに、Web アプリのアプリケーション設定で設定することができます。

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure Cosmos DB for Mongo DB API アカウントを作成し、Azure Cosmos DB Emulator を使用して、GitHub からクローンした Python Flask To-Do Web アプリを実行する方法について説明しました。 これで、Azure Cosmos DB アカウントに追加のデータをインポートできるようになりました。 

> [!div class="nextstepaction"]
> [MongoDB データを Azure Cosmos DB にインポートする](mongodb-migrate.md)
