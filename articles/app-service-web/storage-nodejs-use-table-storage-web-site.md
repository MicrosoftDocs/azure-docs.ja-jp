---
title: "Azure Table サービスを使用する Node.js Web アプリ"
description: "このチュートリアルでは、Azure Table サービスを使用して、Azure App Service Web Apps でホストされる Node.js アプリケーションのデータを格納する方法を説明します。"
tags: azure-portal
services: app-service\web, storage
documentationcenter: nodejs
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 029e6f46-f586-4309-adbf-71c7b8d537d4
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 3f0183c2c88819dfc67886676fd8c69b246537b6
ms.lasthandoff: 03/21/2017


---
# <a name="nodejs-web-app-using-the-azure-table-service"></a>Azure Table サービスを使用する Node.js Web アプリ
## <a name="overview"></a>概要
このチュートリアルでは、Azure データ管理で提供される Table サービスを使って、[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps でホストされる[node] アプリケーションのデータを格納し、データにアクセスする方法を説明します。 このチュートリアルは、ノードおよび [Git]を使用した経験があることを前提としています。

学習内容:

* npm (ノード パッケージ マネージャー) を使用してノード モジュールをインストールする方法
* Azure Table サービスを使用する方法
* Azure CLI を使用して Web アプリを作成する方法

このチュートリアルでは、タスクを作成、取得、完了する機能を備えた、単純な Web ベースの "To DO リスト" アプリケーションを作成します。 タスクは Table サービスに格納されます。

完成したアプリケーションを次に示します。

![空のタスク一覧が表示されている Web ページ][node-table-finished]

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、「[Azure App Service アプリケーションの作成](https://azure.microsoft.com/try/app-service/)」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 

## <a name="prerequisites"></a>前提条件
この記事の手順を実行する前に、次のソフトウェアがインストールされていることを確認してください。

* [node] Version 0.10.24 以上
* [Git]

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成
Azure ストレージ アカウントを作成します。 アプリでは、このアカウントを使用して To Do リストの項目を保存します。

1. [Azure ポータル](https://portal.azure.com/)にログインします。
2. ポータルの左下にある **[新規]** アイコンをクリックし、**[データ + ストレージ]** > **[ストレージ]** の順にクリックします。 ストレージ アカウントに一意の名前を付け、アカウントの新しい[リソース グループ](../azure-resource-manager/resource-group-overview.md)を作成します。
   
      ![New ボタン](./media/storage-nodejs-use-table-storage-web-site/configure-storage.png)
   
    ストレージ アカウントが作成されると、**[通知]** ボタンに緑色の **[成功]** という文字が点滅し、ストレージ アカウントのブレードが開いて、作成した新しいリソース グループに属していることが示されます。
3. ストレージ アカウントのブレードで、**[設定]** > **[キー]** の順にクリックします。 プライマリ アクセス キーをクリップボードにコピーします。
   
    ![アクセス キー][portal-storage-access-keys]

## <a name="install-modules-and-generate-scaffolding"></a>モジュールのインストールとスキャフォールディングの生成
ここでは、新しい Node アプリケーションを作成し、npm を使用してモジュール パッケージを追加します。 このアプリケーションでは、[Express] モジュールと [Azure] モジュールを使います。 Express モジュールは node の Model View Controller フレームワークを提供し、Azure モジュールは Table サービスへの接続を提供します。

### <a name="install-express-and-generate-scaffolding"></a>express のインストールとスキャフォールディングの生成
1. コマンド ラインで、 **tasklist** という名前の新しいディレクトリを作成し、そのディレクトリに移動します。  
2. 次のコマンドを入力して、Express モジュールをインストールします。
   
        npm install express-generator@4.2.0 -g
   
    オペレーティング システムによっては、コマンドの前に 「sudo」を入力することが必要な場合があります。
   
        sudo npm install express-generator@4.2.0 -g
   
    出力は次の例のようになります。
   
        express-generator@4.2.0 /usr/local/lib/node_modules/express-generator
        ├── mkdirp@0.3.5
        └── commander@1.3.2 (keypress@0.1.0)
   
   > [!NOTE]
   > "-g" パラメーターにより、モジュールがグローバルにインストールされます。 これにより、追加のパス情報を入力しなくても、 **express** を使用して Web アプリのスキャフォールディングを生成できます。
   > 
   > 
3. アプリケーションのスキャフォールディングを作成するには、 **express** コマンドを入力します。
   
        express
   
    このコマンドの出力は次の例のようになります。
   
           create : .
           create : ./package.json
           create : ./app.js
           create : ./public
           create : ./public/images
           create : ./routes
           create : ./routes/index.js
           create : ./routes/users.js
           create : ./public/stylesheets
           create : ./public/stylesheets/style.css
           create : ./views
           create : ./views/index.jade
           create : ./views/layout.jade
           create : ./views/error.jade
           create : ./public/javascripts
           create : ./bin
           create : ./bin/www
   
           install dependencies:
             $ cd . && npm install
   
           run the app:
             $ DEBUG=my-application ./bin/www
   
    **tasklist** ディレクトリに、複数の新しいディレクトリとファイルが作成されています。

### <a name="install-additional-modules"></a>追加モジュールをインストールする
**express** によって作成されるファイルの&1; つに **package.json** があります。 このファイルには、モジュールの依存関係のリストが含まれます。 後でアプリケーションを App Service Web Apps にデプロイするときに、このファイルによって Azure にインストールする必要があるモジュールが特定されます。

コマンド ラインで次のコマンドを入力して、 **package.json** ファイルに記述されたモジュールをインストールします。 場合によっては、"sudo" を使用する必要があります。

    npm install

このコマンドの出力は次の例のようになります。

    debug@0.7.4 node_modules\debug

    cookie-parser@1.0.1 node_modules\cookie-parser
    ├── cookie-signature@1.0.3
    └── cookie@0.1.0

    [...]


次に、次のコマンドを入力して、[azure]、[node-uuid]、[nconf]、[async] の各モジュールをインストールします。

    npm install azure-storage node-uuid async nconf --save

**--save** フラグにより、これらのモジュールのエントリが **package.json** ファイルに追加されます。

このコマンドの出力は次の例のようになります。

    async@0.9.0 node_modules\async

    node-uuid@1.4.1 node_modules\node-uuid

    nconf@0.6.9 node_modules\nconf
    ├── ini@1.2.1
    ├── async@0.2.9
    └── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.10)

    [...]


## <a name="create-the-application"></a>アプリケーションを作成する
これで、アプリケーションを作成する準備ができました。

### <a name="create-a-model"></a>モデルの作成
*"モデル"* は、アプリケーションでデータを表すオブジェクトです。 このアプリケーションの場合、To Do リストの項目を表す Task オブジェクトが唯一のモデルです。 Task には次のフィールドがあります。

* PartitionKey
* RowKey
* name (文字列)
* category (文字列)
* completed (ブール値)

**PartitionKey** と **RowKey** は、Table サービスでテーブル キーとして使われます。 詳細については、「 [Table サービス データ モデルについて](https://msdn.microsoft.com/library/azure/dd179338.aspx)」を参照してください。

1. **tasklist** ディレクトリ内に、**models** という名前の新しいディレクトリを作ります。
2. **models** ディレクトリ内に、**task.js** という名前の新しいファイルを作ります。 このファイルには、アプリケーションで作成されるタスクのモデルが格納されます。
3. **task.js** ファイルの先頭に、必要なライブラリを参照する次のコードを追加します。
   
        var azure = require('azure-storage');
          var uuid = require('node-uuid');
        var entityGen = azure.TableUtilities.entityGenerator;
4. Task オブジェクトを定義し、エクスポートする次のコードを追加します。 このオブジェクトは、テーブルへの接続を処理します。
   
          module.exports = Task;
   
        function Task(storageClient, tableName, partitionKey) {
          this.storageClient = storageClient;
          this.tableName = tableName;
          this.partitionKey = partitionKey;
          this.storageClient.createTableIfNotExists(tableName, function tableCreated(error) {
            if(error) {
              throw error;
            }
          });
        };
5. Task オブジェクトの追加のメソッドを定義する次のコードを追加します。このメソッドによって、テーブルに格納されたデータを操作できます。
   
        Task.prototype = {
          find: function(query, callback) {
            self = this;
            self.storageClient.queryEntities(this.tableName, query, null, function entitiesQueried(error, result) {
              if(error) {
                callback(error);
              } else {
                callback(null, result.entries);
              }
            });
          },
   
          addItem: function(item, callback) {
            self = this;
            // use entityGenerator to set types
            // NOTE: RowKey must be a string type, even though
            // it contains a GUID in this example.
            var itemDescriptor = {
              PartitionKey: entityGen.String(self.partitionKey),
              RowKey: entityGen.String(uuid()),
              name: entityGen.String(item.name),
              category: entityGen.String(item.category),
              completed: entityGen.Boolean(false)
            };
            self.storageClient.insertEntity(self.tableName, itemDescriptor, function entityInserted(error) {
              if(error){  
                callback(error);
              }
              callback(null);
            });
          },
   
          updateItem: function(rKey, callback) {
            self = this;
            self.storageClient.retrieveEntity(self.tableName, self.partitionKey, rKey, function entityQueried(error, entity) {
              if(error) {
                callback(error);
              }
              entity.completed._ = true;
              self.storageClient.updateEntity(self.tableName, entity, function entityUpdated(error) {
                if(error) {
                  callback(error);
                }
                callback(null);
              });
            });
          }
        }
6. **task.js** ファイルを保存して閉じます。

### <a name="create-a-controller"></a>コントローラーの作成
*コント ローラー* は、HTTP 要求を処理し、HTML 応答を表示します。

1. **tasklist/routes** ディレクトリに **tasklist.js** という名前の新しいファイルを作り、テキスト エディターで開きます。
2. 次のコードを **tasklist.js** に追加します。 これによって、**tasklist.js** で使われる azure モジュールと async モジュールが読み込まれます。 また、**TaskList** 関数が定義されます。先ほど定義した **Task** オブジェクトのインスタンスがこの関数に渡されます。
   
        var azure = require('azure-storage');
        var async = require('async');
   
        module.exports = TaskList;
3. **TaskList** オブジェクトを定義します。
   
        function TaskList(task) {
          this.task = task;
        }
4. **TaskList**に次のメソッドを追加します。
   
        TaskList.prototype = {
          showTasks: function(req, res) {
            self = this;
            var query = new azure.TableQuery()
              .where('completed eq ?', false);
            self.task.find(query, function itemsFound(error, items) {
              res.render('index',{title: 'My ToDo List ', tasks: items});
            });
          },
   
          addTask: function(req,res) {
            var self = this;
            var item = req.body.item;
            self.task.addItem(item, function itemAdded(error) {
              if(error) {
                throw error;
              }
              res.redirect('/');
            });
          },
   
          completeTask: function(req,res) {
            var self = this;
            var completedTasks = Object.keys(req.body);
            async.forEach(completedTasks, function taskIterator(completedTask, callback) {
              self.task.updateItem(completedTask, function itemsUpdated(error) {
                if(error){
                  callback(error);
                } else {
                  callback(null);
                }
              });
            }, function goHome(error){
              if(error) {
                throw error;
              } else {
               res.redirect('/');
              }
            });
          }
        }

### <a name="modify-appjs"></a>app.js の変更
1. **tasklist** ディレクトリの **app.js** ファイルを開きます。 このファイルは、先ほど **express** コマンドを実行することによって作成されたものです。
2. ファイルの先頭に次のコードを追加します。このコードは、azure モジュールを読み込み、テーブル名とパーティション キーを設定し、この例で使用するストレージ資格情報を設定します。
   
        var azure = require('azure-storage');
        var nconf = require('nconf');
        nconf.env()
             .file({ file: 'config.json', search: true });
        var tableName = nconf.get("TABLE_NAME");
        var partitionKey = nconf.get("PARTITION_KEY");
        var accountName = nconf.get("STORAGE_NAME");
        var accountKey = nconf.get("STORAGE_KEY");
   
   > [!NOTE]
   > nconf は、環境変数または後で作成する **config.json** ファイルから構成値を読み込みます。
   > 
   > 
3. app.js ファイル内で、次の行が表示されるまで下へスクロールします。
   
        app.use('/', routes);
        app.use('/users', users);
   
    これらの行を下のコードに置き換えます。 これにより、ストレージ アカウントへの接続を使って、<strong>Task</strong> のインスタンスが初期化されます。 このインスタンスが <strong>TaskList</strong> に渡されます。TaskList はこれを使用して Table サービスとやり取りします。
   
        var TaskList = require('./routes/tasklist');
        var Task = require('./models/task');
        var task = new Task(azure.createTableService(accountName, accountKey), tableName, partitionKey);
        var taskList = new TaskList(task);
   
        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));
4. **app.js** ファイルを保存します。

### <a name="modify-the-index-view"></a>index ビューの変更
1. テキスト エディターで **tasklist/views/index.jade** ファイルを開きます。
2. ファイルの内容全体を次のコードに置き換えます。 これにより、既存のタスクを表示するビューが定義されます。このビューには、新しいタスクを追加し、既存のタスクを完了済みとしてマークするためのフォームが含まれます。
   
        extends layout
   
        block content
          h1= title
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
                    td #{task.name._}
                    td #{task.category._}
                    - var day   = task.Timestamp._.getDate();
                    - var month = task.Timestamp._.getMonth() + 1;
                    - var year  = task.Timestamp._.getFullYear();
                    td #{month + "/" + day + "/" + year}
                    td
                      input(type="checkbox", name="#{task.RowKey._}", value="#{!task.completed._}", checked=task.completed._)
            button.btn(type="submit") Update tasks
          hr
          form.well(action="/addtask", method="post")
            label Item Name:
            input(name="item[name]", type="textbox")
            label Item Category:
            input(name="item[category]", type="textbox")
            br
            button.btn(type="submit") Add item
3. **index.jade** ファイルを保存して閉じます。

### <a name="modify-the-global-layout"></a>グローバル レイアウトの変更
**views** ディレクトリの **layout.jade** ファイルは、他の **.jade** ファイルのグローバル テンプレートです。 この手順では、[Twitter Bootstrap](https://github.com/twbs/bootstrap) を使うようにこのファイルを変更します。Twitter Bootstrap は、見栄えのよい Web アプリを簡単にデザインできるツールキットです。

[Twitter Bootstrap](http://getbootstrap.com/)のファイルをダウンロードして展開します。 Bootstrap の **css** フォルダーから **bootstrap.min.css** ファイルをアプリケーションの **public/stylesheets** ディレクトリにコピーします。

**views** フォルダーの **layout.jade** を開き、内容全体を次のコードに置き換えます。

    doctype html
    html
      head
        title= title
        link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
        link(rel='stylesheet', href='/stylesheets/style.css')
      body.app
        nav.navbar.navbar-default
          div.navbar-header
          a.navbar-brand(href='/') My Tasks
        block content

### <a name="create-a-config-file"></a>config ファイルの作成
アプリケーションをローカルで実行するために、Azure Storage の資格情報を config ファイルに追加します。 次の JSON が含まれた **config.json** という名前のファイルを作成します。

    {
        "STORAGE_NAME": "<storage account name>",
        "STORAGE_KEY": "<storage access key>",
        "PARTITION_KEY": "mytasks",
        "TABLE_NAME": "tasks"
    }

**<storage account name>** を以前に作成したストレージ アカウントの名前に置き換え、**<storage access key>** をストレージ アカウントのプライマリ アクセス キーに置き換えます。 次に例を示します。

    {
        "STORAGE_NAME": "nodejsappstorage",
        "STORAGE_KEY": "KG0oDd..."
        "PARTITION_KEY": "mytasks",
        "TABLE_NAME": "tasks"
    }

次のように、 **tasklist** ディレクトリより *"1 つ上位のディレクトリ レベル"* にこのファイルを保存します。

    parent/
      |-- config.json
      |-- tasklist/

これは、公開される可能性のあるソース コントロールに config ファイルがチェックインされないようにするためです。 アプリを Azure にデプロイするときは、config ファイルではなく環境変数を使用します。

## <a name="run-the-application-locally"></a>ローカルでアプリケーションを実行する
ローカル コンピューターでアプリケーションをテストするには、次の手順を実行します。

1. コマンド ラインで、 **tasklist** ディレクトリに移動します。
2. 次のコマンドを使用して、ローカルでアプリケーションを起動します。
   
        npm start
3. Web ブラウザーを開いて、http://127.0.0.1:3000 にアクセスします。
   
    次の例のような Web ページが表示されます。
   
    ![空のタスク一覧が表示されている Web ページ][node-table-finished]
4. To Do リストの新しい項目を作成するには、名前とカテゴリを入力し、 **[Add Item]**をクリックします。 
5. タスクを完了としてマークするには、**[Complete]** をクリックし、**[Update Tasks]** をクリックします。
   
    ![タスク一覧の新しいアイテムの画像][node-table-list-items]

アプリケーションがローカルで実行されていても、データは Azure Table サービスに保存されています。

## <a name="deploy-your-application-to-azure"></a>Azure へのアプリケーションのデプロイ
このセクションの手順では、Azure コマンド ライン ツールを使用して App Service で新しい Web アプリを作成し、Git を使用してアプリケーションをデプロイします。 これらの手順を実行するには、Azure サブスクリプションが必要です。

> [!NOTE]
> これらの手順は、 [Azure ポータル](https://portal.azure.com/)を使用して実行することもできます。 「 [Azure App Service での Node.js Web アプリの構築とデプロイ]」をご覧ください。
> 
> これが初めて作成した Web アプリの場合、Azure ポータルを使用してこのアプリケーションをデプロイする必要があります。
> 
> 

作業を始めるには、コマンド ラインで次のコマンドを入力して、 [Azure CLI] をインストールします。

    npm install azure-cli -g

### <a name="import-publishing-settings"></a>発行の設定をインポートする
この手順では、サブスクリプションに関する情報が含まれたファイルをダウンロードします。

1. 次のコマンドを入力します。
   
        azure login
   
    このコマンドにより、ブラウザーが起動し、ダウンロード ページに移動します。 ログインを求められた場合は、Azure サブスクリプションに関連付けられたアカウントを使用してログインします。
   
    <!-- ![The download page][download-publishing-settings] -->
   
    ファイルのダウンロードが自動的に開始されます。ダウンロードが開始されない場合は、ページの先頭にあるリンクをクリックして、手動でファイルをダウンロードできます。 ファイルを保存し、ファイル パスを書き留めます。
2. 次のコマンドを入力して設定をインポートします。
   
        azure account import <path-to-file>
   
    そのためには、前の手順でダウンロードした発行設定ファイルのパスとファイル名を指定します。
3. 設定をインポートしたら、不要になった発行設定ファイルを削除してください。 このファイルはもう不要であり、Azure サブスクリプションに関する機密情報が含まれているためです。

### <a name="create-an-app-service-web-app"></a>App Service Web アプリの作成
1. コマンド ラインで、 **tasklist** ディレクトリに移動します。
2. 次のコマンドを使用して、新しい Web アプリを作成します。
   
        azure site create --git
   
    Web アプリの名前と場所の入力を求められます。 一意の名前を指定し、Azure ストレージ アカウントと同じ地理的な場所を選択します。
   
    `--git` パラメーターにより、Azure にこの Web アプリの Git リポジトリが作成されます。 何も存在しない場合は、現在のディレクトリで Git リポジトリが初期化され、アプリケーションを Azure に発行するときに使う "azure" という名前の [Git リモート]が追加されます。 最後に、**web.config** ファイルが作成されます。このファイルには、ノード アプリケーションをホストするために Azure で使われる設定が含まれます。 `--git` パラメーターを省略した場合でも、ディレクトリに Git リポジトリが含まれていれば、"azure" リモートが作成されます。
   
    このコマンドが完了すると、次のような出力が表示されます。 **Website created at** で始まる行には、Web アプリの URL が含まれています。
   
        info:   Executing command site create
        help:   Need a site name
        Name: TableTasklist
        info:   Using location southcentraluswebspace
        info:   Executing `git init`
        info:   Creating default .gitignore file
        info:   Creating a new web site
        info:   Created web site at  tabletasklist.azurewebsites.net
        info:   Initializing repository
        info:   Repository initialized
        info:   Executing `git remote add azure https://username@tabletasklist.azurewebsites.net/TableTasklist.git`
        info:   site create command OK
   
   > [!NOTE]
   > これがサブスクリプションの最初の App Service Web アプリである場合、Azure ポータルを使用して Web アプリを作成するよう指示するメッセージが表示されます。 詳細は、「 [Azure App Service での Node.js Web アプリの構築とデプロイ]」を参照してください。
   > 
   > 

### <a name="set-environment-variables"></a>環境変数の設定
この手順では、Azure の Web アプリの構成に環境変数を追加します。
コマンド ラインで次のように入力します。

    azure site appsetting add
        STORAGE_NAME=<storage account name>;STORAGE_KEY=<storage access key>;PARTITION_KEY=mytasks;TABLE_NAME=tasks


**<storage account name>** を以前に作成したストレージ アカウントの名前に置き換え、**<storage access key>** をストレージ アカウントのプライマリ アクセス キーに置き換えます  (以前に作成した config.json ファイルと同じ値を使用します)。

環境変数は、 [Azure ポータル](https://portal.azure.com/)で設定することもできます。

1. **[参照]** > **[Web Apps]** > Web アプリ名の順にクリックして、Web アプリのブレードを開きます。
2. Web アプリのブレードで、**[すべての設定]** > **[アプリケーションの設定]** の順にクリックします。
   
     <!-- ![Top Menu](./media/storage-nodejs-use-table-storage-web-site/PollsCommonWebSiteTopMenu.png) -->
3. **[アプリ設定]** セクションまで下にスクロールし、キーと値のペアを追加します。
   
     ![[アプリ設定]](./media/storage-nodejs-use-table-storage-web-site/storage-tasks-appsettings.png)
4. **[保存]**をクリックします。

### <a name="publish-the-application"></a>アプリケーションの発行
アプリを発行するには、コード ファイルを Git にコミットし、azure/master にプッシュします。

1. デプロイメント資格情報を設定します。
   
        azure site deployment user set <name> <password>
2. アプリケーション ファイルを追加し、コミットします。
   
        git add .
        git commit -m "adding files"
3. コミットを App Service の Web アプリにプッシュします。
   
        git push azure master
   
    **master** をターゲット分岐として使用します。 デプロイの最後に、次の例のようなステートメントが表示されます。
   
        To https://username@tabletasklist.azurewebsites.net/TableTasklist.git
          * [new branch]      master -> master
4. プッシュ操作が完了したら、先ほど `azure create site` コマンドから返された Web アプリの URL を参照してアプリケーションを表示します。

## <a name="next-steps"></a>次のステップ
この記事の手順では、Table サービスを使って情報を格納する方法を説明しましたが、[MongoDB](https://mlab.com/azure/) を使うこともできます。 

## <a name="additional-resources"></a>その他のリソース
[Azure CLI]

## <a name="whats-changed"></a>変更内容
* Websites から App Service への変更ガイドについては、「 [Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- URLs -->

[Azure App Service での Node.js Web アプリの構築とデプロイ]: web-sites-nodejs-develop-deploy-mac.md
[Azure Developer Center]: /develop/nodejs/

[node]: http://nodejs.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[for free]: http://windowsazure.com
[Git リモート]: http://git-scm.com/docs/git-remote

[Azure CLI]:../cli-install-nodejs.md

[azure]: https://github.com/Azure/azure-sdk-for-node
[node-uuid]: https://www.npmjs.com/package/node-uuid
[nconf]: https://www.npmjs.com/package/nconf
[async]: https://www.npmjs.com/package/async

[Azure Portal]: https://portal.azure.com

[Create and deploy a Node.js application to an Azure Web Site]: web-sites-nodejs-develop-deploy-mac.md

<!-- Image References -->

[node-table-finished]: ./media/storage-nodejs-use-table-storage-web-site/table_todo_empty.png
[node-table-list-items]: ./media/storage-nodejs-use-table-storage-web-site/table_todo_list.png
[download-publishing-settings]: ./media/storage-nodejs-use-table-storage-web-site/azure-account-download-cli.png
[portal-new]: ./media/storage-nodejs-use-table-storage-web-site/plus-new.png
[portal-storage-account]: ./media/storage-nodejs-use-table-storage-web-site/new-storage.png
[portal-quick-create-storage]: ./media/storage-nodejs-use-table-storage-web-site/quick-storage.png
[portal-storage-access-keys]: ./media/storage-nodejs-use-table-storage-web-site/manage-access-keys.png
[go-to-dashboard]: ./media/storage-nodejs-use-table-storage-web-site/go_to_dashboard.png
[web-configure]: ./media/storage-nodejs-use-table-storage-web-site/sql-task-configure.png
[app-settings-save]: ./media/storage-nodejs-use-table-storage-web-site/savebutton.png
[app-settings]: ./media/storage-nodejs-use-table-storage-web-site/storage-tasks-appsettings.png

