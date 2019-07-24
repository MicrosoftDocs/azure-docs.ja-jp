---
title: Azure Cosmos DB の MongoDB 用 API と Python SDK を使用して Flask Web アプリを構築する
description: Azure Cosmos DB の MongoDB 用 API を使用して接続とクエリを行うことができる Python Flask のコード サンプルについて説明します。
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: python
ms.topic: quickstart
ms.date: 12/26/2018
ms.openlocfilehash: 35159a1630c7f1268119cd55cc6e0ac99dfd7b5f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58119209"
---
# <a name="quickstart-build-a-python-app-using-azure-cosmos-dbs-api-for-mongodb"></a>クイック スタート:Azure Cosmos DB の MongoDB 用 API を使用して Python アプリをビルドする

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Azure Cosmos DB は、Microsoft のグローバルに配布されるマルチモデル データベース サービスです。 Cosmos DB の中核であるグローバル配布と水平スケーリングの機能を利用して、ドキュメント、キー/値、グラフの各データベースの作成とクエリをすばやく行うことができます。

このクイック スタート ガイドでは、次の [Flask の例](https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample)を使用して、[Azure Cosmos DB Emulator](local-emulator.md) と Azure Cosmos DB の MongoDB 用 API で簡単な To-Do Flask アプリを構築する方法を示します。

## <a name="prerequisites"></a>前提条件

- [Azure Cosmos DB Emulator](local-emulator.md) をダウンロードします。 エミュレーターは現在、Windows でのみサポートされています。 このサンプルでは、Azure のプロダクション キーと共にサンプルを使用する方法を示します。この操作は任意のプラットフォームで実行できます。

- Visual Studio Code をまだインストールしていない場合は、お使いのプラットフォーム (Windows、Mac、Linux) 用の [VS Code](https://code.visualstudio.com/Download) をすばやくインストールできます。

- 一般的な Python 拡張機能のいずれかをインストールして Python 言語サポートを必ず追加してください。
  1. 拡張機能を選びます。
  2. 「`ext install`」と入力して、拡張機能をコマンド パレット `Ctrl+Shift+P` にインストールします。

     このドキュメントの例では、Don Jayamanne の人気のある機能豊富な [Python 拡張機能](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python)を使用します。

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

次のスニペットはすべて app.py ファイルのものであり、ローカル Azure Cosmos DB Emulator の接続文字列を使用しています。 パスワードを次のように分割して、スラッシュに対応する必要があります。そうしないと、スラッシュを解析できません。

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

* アプリケーションの作成

    ```Python
    app = Flask(__name__)
    title = "TODO with Flask"
    heading = "ToDo Reminder"
    ```
    
## <a name="run-the-web-app"></a>Web アプリの実行

1. Azure Cosmos DB Emulator が実行されていることを確認します。

2. ターミナル ウィンドウを開き、`cd` を実行してアプリが保存されているディレクトリに移動します。

3. Mac を使用している場合は、Flask アプリの環境変数を `set FLASK_APP=app.py` または `export FLASK_APP=app.py` に設定します。

4. `flask run` を使用してアプリを実行し、[http://127.0.0.1:5000/](http://127.0.0.1:5000/) を参照します。

5. タスクを追加および削除し、それらがコレクションで追加および変更されたことを確認します。

## <a name="create-a-database-account"></a>データベース アカウントの作成

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>接続文字列を更新する

動作している Cosmos アカウントに対してコードをテストする場合は、Azure portal に移動してアカウントを作成し、接続文字列の情報を取得します。 これをアプリにコピーします。

1. [Azure portal](https://portal.azure.com/) の Cosmos アカウントで、左側のナビゲーションの **[接続文字列]** をクリックし、**[読み取り/書き込みキー]** をクリックします。 次の手順では、画面右側のコピー ボタンを使用して、ユーザー名、パスワード、ホストを Dal.cs ファイルにコピーします。

2. ルート ディレクトリ内の **app.py** ファイルを開きます。

3. (コピー ボタンを使用して) ポータルから**ユーザー名**の値をコピーし、**app.py** ファイル内の **name** の値に設定します。

4. 次に、ポータルから**接続文字列**の値をコピーし、それを **app.py** ファイル内の MongoClient の値に設定します。

5. 最後に、ポータルから**パスワード**の値をコピーし、**app.py** ファイル内の **password** の値に設定します。

これで、Cosmos DB と通信するために必要なすべての情報でアプリを更新しました。 これまでと同じ方法で実行できます。

## <a name="deploy-to-azure"></a>Deploy to Azure (Azure へのデプロイ)

このアプリをデプロイするには、Azure で新しい Web アプリを作成し、この GitHub リポジトリのフォークで継続的配置を有効にします。 この[チュートリアル](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment)に従って、Azure で GitHub による継続的配置を設定します。

Azure にデプロイする場合は、アプリケーション キーを削除し、以下のセクションがコメント アウトされていないことを確認する必要があります。

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

アプリケーション設定に MONGOURL、MONGO_PASSWORD、MONGO_USERNAME を追加する必要があります。 この[チュートリアル](https://docs.microsoft.com/azure/app-service-web/web-sites-configure#application-settings)に従って、Azure Web Apps のアプリケーション設定の詳細を確認できます。

このリポジトリのフォークを作成しない場合は、下の [Azure へのデプロイ] をクリックすることもできます。 Azure に移動し、Cosmos DB アカウント情報でアプリケーション設定を設定する必要があります。

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="https://azuredeploy.net/deploybutton.png" alt="Click to Deploy to Azure">
</a>

> [!NOTE]
> GitHub またはその他のソース管理オプションでコードを格納することを計画している場合は、必ずコードから接続文字列を削除してください。 代わりに、Web アプリのアプリケーション設定で設定することができます。

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Cosmos アカウントを作成して、Flask アプリを実行する方法を学習しました。 これで、Cosmos データベースに追加のデータをインポートできます。 

> [!div class="nextstepaction"]
> [MongoDB データを Azure Cosmos DB にインポートする](mongodb-migrate.md)
