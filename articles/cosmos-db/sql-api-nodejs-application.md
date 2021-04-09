---
title: 'チュートリアル: Azure Cosmos DB の JavaScript SDK を使用して Node.js Web アプリを構築し SQL API データを管理する'
description: この Node.js チュートリアルでは、Microsoft Azure Cosmos DB を使用して、Microsoft Azure App Service の Web Apps 機能にホストされた Node.js Express Web アプリケーションからデータを格納する方法やデータにアクセスする方法について説明します。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: sngun
ms.custom: devx-track-js
ms.openlocfilehash: 49cf54bda985f7d97b2db6a3ada7859aee829cff
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359542"
---
# <a name="tutorial-build-a-nodejs-web-app-using-the-javascript-sdk-to-manage-a-sql-api-account-in-azure-cosmos-db"></a>チュートリアル:JavaScript SDK を使用して Node.js Web アプリを構築して Azure Cosmos DB の SQL API アカウントを管理する 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](./create-sql-api-python.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

お客様は開発者として、NoSQL ドキュメント データが使用されるアプリケーションをお持ちかもしれません。 Azure Cosmos DB の SQL API アカウントを使用して、このドキュメント データを格納し、それにアクセスできます。 この Node.js チュートリアルでは、Microsoft Azure App Service の Web Apps 機能にホストされている Node.js Express アプリケーションを使用して、Azure Cosmos DB の SQL API アカウントからデータを格納する方法やデータにアクセスする方法を示します。 このチュートリアルでは、タスクの作成、取得、および完了を行える Web ベースのアプリケーション (Todo アプリ) を構築します。 タスクは、JSON ドキュメントとして Azure Cosmos DB に保存するものとします。 

このチュートリアルでは、Azure portal を使用して Azure Cosmos DB の SQL API アカウントを作成する方法を示します。 その後、Node.js SDK に基づく Web アプリを構築および実行して、データベースとコンテナーを作成し、コンテナーに項目を追加します。 このチュートリアルでは、JavaScript SDK のバージョン 3.0 を使用します。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * Azure Cosmos DB アカウントを作成する
> * 新しい Node.js アプリケーションを作成する
> * アプリケーションを Azure Cosmos DB に接続する
> * アプリケーションを実行し、Azure にデプロイする

## <a name="prerequisites"></a><a name="_Toc395783176"></a>前提条件

この記事の手順を実行する前に、以下のリソースがあることを確認してください。

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js][Node.js] バージョン 6.10 以降。
* [Express ジェネレーター](https://www.expressjs.com/starter/generator.html) (`npm install express-generator -g` によって Express をインストールできます)
* ローカル ワークステーションに [Git][Git] をインストールします。

## <a name="create-an-azure-cosmos-db-account"></a><a name="_Toc395637761"></a>Azure Cosmos DB アカウントを作成する
まず最初に、Azure Cosmos DB アカウントを作成します。 アカウントが既にある場合、またはこのチュートリアルで Azure Cosmos DB Emulator を使用する場合は、「[手順 2: 新しい Node.js アプリケーションを作成する](#_Toc395783178)」に進むことができます。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [cosmos-db-keys](../../includes/cosmos-db-keys.md)]

## <a name="create-a-new-nodejs-application"></a><a name="_Toc395783178"></a>新しい Node.js アプリケーションを作成する
次に、Express フレームワークを使用した基本的な Hello World Node.js プロジェクトの作成について学習しましょう。

1. Node.js のコマンド プロンプトなどのお好きなターミナルを開きます。

1. 新しいアプリケーションを保存するディレクトリに移動します。

1. Express ジェネレーターを使用し、 **todo** という名前の新しいアプリケーションを作成します。

   ```bash
   express todo
   ```

1. **todo** ディレクトリを開き、依存関係をインストールします。

   ```bash
   cd todo
   npm install
   ```

1. 新しいアプリケーションを実行します。

   ```bash
   npm start
   ```

1. ブラウザーで `http://localhost:3000` に移動すると、新しいアプリケーションを表示できます。
   
   :::image type="content" source="./media/sql-api-nodejs-application/cosmos-db-node-js-express.png" alt-text="Node.js について学習する - ブラウザー ウィンドウでの Hello World アプリケーションのスクリーンショット":::

   ターミナル ウィンドウで Ctrl キーを押しながら C キーを押してアプリケーションを停止し、 **[y]** を選択してバッチ ジョブを終了します。

## <a name="install-the-required-modules"></a><a name="_Toc395783179"></a>必須のモジュールをインストールする

**package.json** ファイルは、プロジェクトのルートに作成されるファイルの 1 つです。 このファイルには、Node.js アプリケーションで必要な追加モジュールのリストが含まれます。 このファイルは、このアプリケーションを Azure にデプロイするときに、アプリケーションをサポートするために Azure にインストールする必要があるモジュールを判断するために使用されます。 このチュートリアルのために、さらに 2 つのパッケージをインストールします。

1. npm を使用して **\@azure/cosmos** モジュールをインストールします。 

   ```bash
   npm install @azure/cosmos
   ```

## <a name="connect-the-nodejs-application-to-azure-cosmos-db"></a><a name="_Toc395783180"></a>Node.js アプリケーションを Azure Cosmos DB に接続する
最初の設定と構成が済んだら、Todo アプリケーションが Azure Cosmos DB と通信を行うために必要なコードを作成します。

### <a name="create-the-model"></a>モデルを作成する
1. お客様のプロジェクト ディレクトリのルートに、**models** という名前の新しいディレクトリを作成します。  

2. **models** ディレクトリ内に、**taskDao.js** という名前の新しいファイルを作成します。 このファイルには、データベースとコンテナーの作成に必要なコードを含めます。 また、Azure Cosmos DB 内のタスクの読み取り、更新、作成、および検索を行うメソッドも定義します。 

3. 次のコードを **taskDao.js** ファイルにコピーします。

   ```javascript
    // @ts-check
    const CosmosClient = require('@azure/cosmos').CosmosClient
    const debug = require('debug')('todo:taskDao')

    // For simplicity we'll set a constant partition key
    const partitionKey = undefined
    class TaskDao {
      /**
       * Manages reading, adding, and updating Tasks in Cosmos DB
       * @param {CosmosClient} cosmosClient
       * @param {string} databaseId
       * @param {string} containerId
       */
      constructor(cosmosClient, databaseId, containerId) {
        this.client = cosmosClient
        this.databaseId = databaseId
        this.collectionId = containerId

        this.database = null
        this.container = null
      }

      async init() {
        debug('Setting up the database...')
        const dbResponse = await this.client.databases.createIfNotExists({
          id: this.databaseId
        })
        this.database = dbResponse.database
        debug('Setting up the database...done!')
        debug('Setting up the container...')
        const coResponse = await this.database.containers.createIfNotExists({
          id: this.collectionId
        })
        this.container = coResponse.container
        debug('Setting up the container...done!')
      }

      async find(querySpec) {
        debug('Querying for items from the database')
        if (!this.container) {
          throw new Error('Collection is not initialized.')
        }
        const { resources } = await this.container.items.query(querySpec).fetchAll()
        return resources
      }

      async addItem(item) {
        debug('Adding an item to the database')
        item.date = Date.now()
        item.completed = false
        const { resource: doc } = await this.container.items.create(item)
        return doc
      }

      async updateItem(itemId) {
        debug('Update an item in the database')
        const doc = await this.getItem(itemId)
        doc.completed = true

        const { resource: replaced } = await this.container
          .item(itemId, partitionKey)
          .replace(doc)
        return replaced
      }

      async getItem(itemId) {
        debug('Getting an item from the database')
        const { resource } = await this.container.item(itemId, partitionKey).read()
        return resource
      }
    }

    module.exports = TaskDao
   ```
4. **taskDao.js** ファイルを保存して閉じます。  

### <a name="create-the-controller"></a>コントローラーの作成

1. プロジェクトの **routes** ディレクトリ内に、**tasklist.js** という名前の新しいファイルを作成します。  

2. 次のコードを **tasklist.js** に追加します。 このコードによって、**tasklist.js** で使用される CosmosClient および async モジュールが読み込まれます。 また、**TaskList** クラスが定義されます。先ほど定義した **TaskDao** オブジェクトのインスタンスとして、このクラスが渡されます。
   
   ```javascript
    const TaskDao = require("../models/TaskDao");
    
    class TaskList {
      /**
       * Handles the various APIs for displaying and managing tasks
       * @param {TaskDao} taskDao
       */
      constructor(taskDao) {
        this.taskDao = taskDao;
      }
      async showTasks(req, res) {
        const querySpec = {
          query: "SELECT * FROM root r WHERE r.completed=@completed",
          parameters: [
            {
              name: "@completed",
              value: false
            }
          ]
        };

        const items = await this.taskDao.find(querySpec);
        res.render("index", {
          title: "My ToDo List ",
          tasks: items
        });
      }

      async addTask(req, res) {
        const item = req.body;

        await this.taskDao.addItem(item);
        res.redirect("/");
      }

      async completeTask(req, res) {
        const completedTasks = Object.keys(req.body);
        const tasks = [];

        completedTasks.forEach(task => {
          tasks.push(this.taskDao.updateItem(task));
        });

        await Promise.all(tasks);

        res.redirect("/");
      }
    }

    module.exports = TaskList;
   ```

3. **tasklist.js** ファイルを保存して閉じます。

### <a name="add-configjs"></a>config.js の追加

1. プロジェクト ディレクトリのルートに、**config.js** という名前の新しいファイルを作成します。 

2. 次のコードを **config.js** ファイルに追加します。 このコードにより、アプリケーションに必要な値と構成設定が定義されます。
   
   ```javascript
   const config = {};

   config.host = process.env.HOST || "[the endpoint URI of your Azure Cosmos DB account]";
   config.authKey =
     process.env.AUTH_KEY || "[the PRIMARY KEY value of your Azure Cosmos DB account";
   config.databaseId = "ToDoList";
   config.containerId = "Items";

   if (config.host.includes("https://localhost:")) {
     console.log("Local environment detected");
     console.log("WARNING: Disabled checking of self-signed certs. Do not have this code in production.");
     process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
     console.log(`Go to http://localhost:${process.env.PORT || '3000'} to try the sample.`);
   }

   module.exports = config;
   ```

3. [Azure portal](https://portal.azure.com) にあるお客様の Azure Cosmos DB アカウントの [キー] ページで見つかる値を使用して、**config.js** ファイルの HOST および AUTH_KEY の値を更新します。 

4. **config.js** ファイルを保存して閉じます。

### <a name="modify-appjs"></a>app.js の変更

1. プロジェクト ディレクトリ内の **app.js** ファイルを開きます。 これは、先ほどの Express Web アプリケーション作成時に作成されたファイルです。  

2. 次のコードを **app.js** ファイルに追加します。 このコードにより、使用される構成ファイルが定義され、以降のセクションで使用するいくつかの変数に値が読み込まれます。 
   
   ```javascript
    const CosmosClient = require('@azure/cosmos').CosmosClient
    const config = require('./config')
    const TaskList = require('./routes/tasklist')
    const TaskDao = require('./models/taskDao')

    const express = require('express')
    const path = require('path')
    const logger = require('morgan')
    const cookieParser = require('cookie-parser')
    const bodyParser = require('body-parser')

    const app = express()

    // view engine setup
    app.set('views', path.join(__dirname, 'views'))
    app.set('view engine', 'jade')

    // uncomment after placing your favicon in /public
    //app.use(favicon(path.join(__dirname, 'public', 'favicon.ico')));
    app.use(logger('dev'))
    app.use(bodyParser.json())
    app.use(bodyParser.urlencoded({ extended: false }))
    app.use(cookieParser())
    app.use(express.static(path.join(__dirname, 'public')))

    //Todo App:
    const cosmosClient = new CosmosClient({
      endpoint: config.host,
      key: config.authKey
    })
    const taskDao = new TaskDao(cosmosClient, config.databaseId, config.containerId)
    const taskList = new TaskList(taskDao)
    taskDao
      .init(err => {
        console.error(err)
      })
      .catch(err => {
        console.error(err)
        console.error(
          'Shutting down because there was an error settinig up the database.'
        )
        process.exit(1)
      })

    app.get('/', (req, res, next) => taskList.showTasks(req, res).catch(next))
    app.post('/addtask', (req, res, next) => taskList.addTask(req, res).catch(next))
    app.post('/completetask', (req, res, next) =>
      taskList.completeTask(req, res).catch(next)
    )
    app.set('view engine', 'jade')

    // catch 404 and forward to error handler
    app.use(function(req, res, next) {
      const err = new Error('Not Found')
      err.status = 404
      next(err)
    })

    // error handler
    app.use(function(err, req, res, next) {
      // set locals, only providing error in development
      res.locals.message = err.message
      res.locals.error = req.app.get('env') === 'development' ? err : {}

      // render the error page
      res.status(err.status || 500)
      res.render('error')
    })

    module.exports = app
   ```

3. 最後に、**app.js** ファイルを保存して閉じます。

## <a name="build-a-user-interface"></a><a name="_Toc395783181"></a>ユーザー インターフェイスを構築する

次に、ユーザーがアプリケーションと対話できるように、ユーザー インターフェイスを構築しましょう。 前のセクションで作成した Express アプリケーションは、ビュー エンジンとして **Jade** を使用します。

1. **views** ディレクトリ内の **layout.jade** ファイルは、他の **.jade** ファイルのグローバル テンプレートとして使われます。 この手順では、Web サイトのデザインに使用されるツールキットである Twitter Bootstrap を使用するために、これを変更します。  

2. **views** フォルダーにある **layout.jade** ファイルを開き、その内容を次のコードで置き換えます。

   ```html
   doctype html
   html
     head
       title= title
       link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
       link(rel='stylesheet', href='/stylesheets/style.css')
     body
       nav.navbar.navbar-inverse.navbar-fixed-top
         div.navbar-header
           a.navbar-brand(href='#') My Tasks
       block content
       script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
       script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')
   ```

    このコードでは、アプリケーション用に一定の HTML をレンダリングするよう **Jade** エンジンに指示するほか、コンテンツ ページ用のレイアウトを指定できる **content** という名前の **block** を作成します。 **layout.jade** ファイルを保存して閉じます。

3. アプリケーションによって使用されるビューである **index.jade** ファイルを開き、そのファイルの中身を次のコードに置き換えます。

   ```html
   extends layout
   block content
        h1 #{title}
        br
        
        form(action="/completetask", method="post")
         table.table.table-striped.table-bordered
            tr
              td Name
              td Category
              td Date
              td Complete
            if (typeof tasks === "undefined")
              tr
                td
            else
              each task in tasks
                tr
                  td #{task.name}
                  td #{task.category}
                  - var date  = new Date(task.date);
                  - var day   = date.getDate();
                  - var month = date.getMonth() + 1;
                  - var year  = date.getFullYear();
                  td #{month + "/" + day + "/" + year}
                  td
                   if(task.completed) 
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
                   else
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
          button.btn.btn-primary(type="submit") Update tasks
        hr
        form.well(action="/addtask", method="post")
          label Item Name:
          input(name="name", type="textbox")
          label Item Category:
          input(name="category", type="textbox")
          br
          button.btn(type="submit") Add item
   ```

これはレイアウトを拡張するためのコードであり、前述の **layout.jade** ファイル内の **content** プレースホルダーの内容を指定します。 このレイアウトでは、2 つの HTML フォームを作成しました。

1 つ目のフォームには、データ用のテーブルと、コントローラーの **/completeTask** メソッドに対するポストによって項目を更新できるボタンが含まれます。
    
2 つ目のフォームには、2 つの入力フィールドと、コントローラーの **/addtask** メソッドに対するポストによって新しい項目を作成できるボタンが含まれます。 アプリケーションの動作に必要なものは、これですべてです。

## <a name="run-your-application-locally"></a><a name="_Toc395783181"></a>アプリケーションをローカルで実行する

アプリケーションをビルドしたら、お客様は次の手順を使用して、それをローカルで実行できます。  

1. お客様のローカル コンピューター上でアプリケーションをテストするには、ターミナルで `npm start` を実行してお客様のアプリケーションを起動し、`http://localhost:3000` ブラウザー ページを更新します。 これで、ページは次のスクリーンショットのように表示されているはずです。
   
    :::image type="content" source="./media/sql-api-nodejs-application/cosmos-db-node-js-localhost.png" alt-text="ブラウザー ウィンドウでの MyTodo List アプリケーションのスクリーンショット":::

    > [!TIP]
    > layout.jade ファイルまたは index.jade ファイルのインデントについてのエラーが表示される場合は、両ファイルの最初の 2 行がスペースなしで左揃えになっていることを確認してください。 最初の 2 行の前にスペースがある場合は、それらを削除し、両方のファイルを保存してから、ブラウザー ウィンドウを更新します。 

2. [Item]、[Item Name]、および [Category] フィールドを使用して、新しいタスクを入力し、 **[Add Item]** を選択します。 これらのプロパティに基づいて、Azure Cosmos DB にドキュメントが作成されます。 

3. ページが更新され、ToDo リストに新しく作成された項目が表示されます。
   
    :::image type="content" source="./media/sql-api-nodejs-application/cosmos-db-node-js-added-task.png" alt-text="ToDo リストに新しい項目があるアプリケーションのスクリーンショット":::

4. タスクを完了するには、[Complete] 列のチェック ボックスをオンにし、 **[Update tasks]** を選択します。 既に作成したドキュメントが更新され、ビューから削除されます。

5. アプリケーションを停止するには、ターミナル ウィンドウで Ctrl キーを押しながら C キーを押し、 **[Y]** を選択してバッジ ジョブを終了します。

## <a name="deploy-your-application-to-web-apps"></a><a name="_Toc395783182"></a>アプリケーションを Web Apps にデプロイする

お客様のアプリケーションがローカルで成功した後は、次の手順を使用して、それを Azure にデプロイできます。

1. お客様の Web Apps アプリケーションの Git リポジトリをまだ有効にしていない場合は、有効にします。

2. お客様の Web Apps アプリケーションを Git リモートとして追加します。
   
   ```bash
   git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
   ```

3. アプリケーションをリモートにプッシュすることによって、アプリケーションをデプロイします。
   
   ```bash
   git push azure main
   ```

4. 数秒後に、Web アプリケーションが公開され、ブラウザーで起動されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

これらのリソースが必要なくなったら、リソース グループ、Azure Cosmos DB アカウント、およびすべての関連リソースを削除できます。 そうするには、Azure Cosmos DB アカウントのためにお客様が使用したリソース グループを選択し、 **[削除]** を選択した後、削除するリソース グループの名前を確認します。

## <a name="next-steps"></a><a name="_Toc395637775"></a>次のステップ

> [!div class="nextstepaction"]
> [Xamarin と Azure Cosmos DB を使用したモバイル アプリケーションの構築](mobile-apps-with-xamarin.md)


[Node.js]: https://nodejs.org/
[Git]: https://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-todo-app