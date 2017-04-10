---
title: "テーブル ストレージを使用した Web アプリケーション (Node.js) | Microsoft Docs"
description: "Express を使用する Web アプリケーションのチュートリアルを基に、Azure Storage サービスと Azure モジュールを追加するチュートリアル。"
services: cloud-services, storage
documentationcenter: nodejs
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: e90959a2-4cb2-4b19-9bfb-aede15b18b1c
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/08/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 931503f56b32ce9d1b11283dff7224d7e2f015ae
ms.openlocfilehash: 5d7ee2f529b5127ee60ec8b4f5acaa49e75ddf39


---
# <a name="nodejs-web-application-using-storage"></a>ストレージを使用する Node.js Web アプリケーション
## <a name="overview"></a>概要
このチュートリアルでは、Node.js 用の Microsoft Azure クライアント ライブラリを使って、「[Express を使用する Node.js Web アプリケーション]」チュートリアルで作ったアプリケーションを拡張し、データ管理サービスを使えるようにします。 アプリケーションを拡張して、Azure にデプロイできる Web ベースのタスク一覧アプリケーションを作成します。 このタスク一覧では、ユーザーがタスクの取得、新しいタスクの追加、タスクの完了済みのマーク付けを実行できます。

タスク項目は Azure ストレージに格納されます。 Azure ストレージは、フォールト トレランスと可用性に優れた非構造化データ ストレージです。 Azure ストレージには、データを格納してアクセスできるデータ構造がいくつか用意されています。Azure SDK for Node.js に含まれる API または REST API を通じて、そのストレージ サービスを活用できます。 詳しくは、「[Azure でのデータの格納とアクセス]」をご覧ください。

このチュートリアルは、[Node.js Web アプリケーション]および [Express を使用した Node.js][Express を使用する Node.js Web アプリケーション] のチュートリアルを完了していることを前提としています。

学習内容:

* Jade テンプレート エンジンを操作する方法
* Azure データ管理サービスを操作する方法

完成したアプリケーションのスクリーンショットは次のようになります。

![Internet Explorer で表示された完成した Web ページ](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

## <a name="setting-storage-credentials-in-webconfig"></a>Web.Config のストレージ資格情報の設定
Azure ストレージにアクセスするには、ストレージ資格情報を渡す必要があります。 そのためには、web.config アプリケーション設定を使用します。
これらの設定は環境変数としてノードに渡され、その後 Azure SDK によって読み取られます。

> [!NOTE]
> ストレージの資格情報は、アプリケーションが Azure にデプロイされた場合にのみ使用されます。 エミュレーターで実行している場合、アプリケーションはストレージ エミュレーターを使用します。
>
>

ストレージ アカウントの資格情報を取得し、web.config 設定に追加するには、次の手順を実行します。

1. Azure PowerShell をまだ開いていない場合は起動します。**[スタート]** メニューから **[すべてのプログラム]、[Azure]** の順に展開し、**[Azure PowerShell]** を右クリックして、**[管理者として実行]** を選びます。
2. アプリケーションが含まれているフォルダーに移動します。 たとえば、C:\\node\\tasklist\\WebRole1 です。
3. Azure Powershell ウィンドウで次のコマンドレットを入力して、ストレージ アカウント情報を取得します。

    ```powershell
    PS C:\node\tasklist\WebRole1> Get-AzureStorageAccounts
    ```

   これにより、ホストされるサービスに関連付けられたストレージ アカウントとアカウント キーのリストが取得されます。

   > [!NOTE]
   > Azure SDK では、サービスを展開するときにストレージ アカウントが作成されるので、以前のガイドでアプリケーションを展開したときから、ストレージ アカウントは既に存在しています。
   >
   >
4. **ServiceDefinition.csdef** ファイルを開きます。このファイルには、アプリケーションを Azure にデプロイするときに使われる環境設定が含まれます。

    ```powershell
    PS C:\node\tasklist> notepad ServiceDefinition.csdef
    ```

5. 次のブロックを **Environment** 要素の下に挿入し、{STORAGE ACCOUNT} と {STORAGE ACCESS KEY} を、デプロイメントに使うストレージ アカウントのアカウント名とプライマリ キーに置き換えます。

  <Variable name="AZURE_STORAGE_ACCOUNT" value="{STORAGE ACCOUNT}" />
  <Variable name="AZURE_STORAGE_ACCESS_KEY" value="{STORAGE ACCESS KEY}" />

   ![web.cloud.config ファイルの内容](./media/storage-nodejs-use-table-storage-cloud-service-app/node37.png)

6. ファイルを保存して、メモ帳を閉じます。

### <a name="install-additional-modules"></a>追加モジュールをインストールする
1. 次のコマンドを入力して、[azure]、[node-uuid]、[nconf]、および [async] モジュールをローカルにインストールし、これらのモジュールのエントリを **package.json** ファイルに保存します。

  ```powershell
  PS C:\node\tasklist\WebRole1> npm install azure-storage node-uuid async nconf --save
  ```

  このコマンドの出力は次のように表示されます。

  ```
  node-uuid@1.4.1 node_modules\node-uuid

  nconf@0.6.9 node_modules\nconf
  ├── ini@1.1.0
  ├── async@0.2.9
  └── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.8)

  azure-storage@0.1.0 node_modules\azure-storage
  ├── extend@1.2.1
  ├── xmlbuilder@0.4.3
  ├── mime@1.2.11
  ├── underscore@1.4.4
  ├── validator@3.1.0
  ├── node-uuid@1.4.1
  ├── xml2js@0.2.7 (sax@0.5.2)
  └── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)
  ```

## <a name="using-the-table-service-in-a-node-application"></a>ノード アプリケーションでのテーブル サービスの使用
ここでは、**express** コマンドで作成された基本的なアプリケーションを、タスクのモデルを格納する **task.js** ファイルを追加することによって拡張します。 また、既存の **app.js** を変更し、このモデルを使う新しい **tasklist.js** ファイルを作成します。

### <a name="create-the-model"></a>モデルの作成
1. **WebRole1** ディレクトリ内に、**models** という名前の新しいディレクトリを作ります。
2. **models** ディレクトリ内に、**task.js** という名前の新しいファイルを作ります。 このファイルには、アプリケーションで作成されるタスクのモデルが格納されます。
3. **task.js** ファイルの先頭に、必要なライブラリを参照する次のコードを追加します。

    ```nodejs
    var azure = require('azure-storage');
    var uuid = require('node-uuid');
    var entityGen = azure.TableUtilities.entityGenerator;
    ```

4. 次に、Task オブジェクトを定義およびエクスポートするコードを追加します。 このオブジェクトは、テーブルへの接続を処理します。

    ```nodejs
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
    ```

5. 次に、Task オブジェクトの追加のメソッドを定義する次のコードを追加します。このメソッドによって、テーブルに格納されたデータを操作できます。

    ```nodejs
    Task.prototype = {
      find: function(query, callback) {
        self = this;
        self.storageClient.queryEntities(query, function entitiesQueried(error, result) {
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
    ```

6. **task.js** ファイルを保存して閉じます。

### <a name="create-the-controller"></a>コントローラーの作成
1. **WebRole1/routes** ディレクトリに **tasklist.js** という名前の新しいファイルを作り、テキスト エディターで開きます。
2. 次のコードを **tasklist.js** に追加します。 これによって、**tasklist.js** で使われる azure モジュールと async モジュールが読み込まれます。 また、**TaskList** 関数が定義されます。先ほど定義した **Task** オブジェクトのインスタンスがこの関数に渡されます。

    ```nodejs
    var azure = require('azure-storage');
    var async = require('async');

    module.exports = TaskList;

    function TaskList(task) {
      this.task = task;
    }
    ```

3. **tasklist.js** ファイルへのコードの追加を続行し、タスクの表示、追加、完了に使われる、**showTasks**、**addTask**、および **completeTasks** の各メソッドを追加します。

    ```nodejs
    TaskList.prototype = {
      showTasks: function(req, res) {
        self = this;
        var query = azure.TableQuery()
          .where('completed eq ?', false);
        self.task.find(query, function itemsFound(error, items) {
          res.render('index',{title: 'My ToDo List ', tasks: items});
        });
      },

      addTask: function(req,res) {
        var self = this
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
    ```

4. **tasklist.js** ファイルを保存します。

### <a name="modify-appjs"></a>app.js の変更
1. **WebRole1** ディレクトリ内の **app.js** ファイルを、テキスト エディターで開きます。
2. ファイルの先頭に次のコードを追加します。このコードは、azure モジュールを読み込み、テーブル名とパーティション キーを設定します。

    ```nodejs
    var azure = require('azure-storage');
    var tableName = 'tasks';
    var partitionKey = 'hometasks';
    ```

3. app.js ファイル内で、次の行が表示されるまで下へスクロールします。

    ```nodejs
    app.use('/', routes);
    app.use('/users', users);
    ```

    これらの行を下のコードに置き換えます。 これにより、ストレージ アカウントへの接続を使って、<strong>Task</strong> のインスタンスが初期化されます。 このインスタンスが <strong>TaskList</strong> に渡されます。TaskList はこれを使用して Table サービスとやり取りします。

    ```nodejs
    var TaskList = require('./routes/tasklist');
    var Task = require('./models/task');
    var task = new Task(azure.createTableService(), tableName, partitionKey);
    var taskList = new TaskList(task);

    app.get('/', taskList.showTasks.bind(taskList));
    app.post('/addtask', taskList.addTask.bind(taskList));
    app.post('/completetask', taskList.completeTask.bind(taskList));
    ```

4. **app.js** ファイルを保存します。

### <a name="modify-the-index-view"></a>index ビューの変更
1. **views** ディレクトリに移動し、テキスト エディターで **index.jade** ファイルを開きます。
2. **index.jade** ファイルの内容を次のコードに置き換えます。 これにより、既存のタスクを表示するビューと、新しいタスクの追加とタスクの完了済みのマーク付けを実行するためのフォームを定義します。

    ```
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
          if tasks != []
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
    ```

3. **index.jade** ファイルを保存して閉じます。

### <a name="modify-the-global-layout"></a>グローバル レイアウトの変更
**views** ディレクトリ内の **layout.jade** ファイルは、他の **.jade** ファイルのグローバル テンプレートとして使われます。 この手順では、[Twitter Bootstrap](https://github.com/twbs/bootstrap) を使うようにこのファイルを変更します。Twitter Bootstrap は、見栄えのよい Web サイトを簡単にデザインできるツールキットです。

1. [Twitter Bootstrap](http://getbootstrap.com/)のファイルをダウンロードして展開します。 **bootstrap\\dist\\css** フォルダーから **bootstrap.min.css** ファイルを tasklist アプリケーションの **public\\stylesheets** ディレクトリにコピーします。
2. **views** フォルダーから、テキスト エディターで **layout.jade** を開き、内容を次の内容に置き換えます。

    doctype html  html    head      title= title      link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')      link(rel='stylesheet', href='/stylesheets/style.css')    body.app      nav.navbar.navbar-default        div.navbar-header          a.navbar-brand(href='/') My Tasks      block content

3. **layout.jade** ファイルを保存します。

### <a name="running-the-application-in-the-emulator"></a>エミュレーターでのアプリケーションの実行
エミュレーターでアプリケーションを開始するには、次のコマンドを実行します。

```powershell
PS C:\node\tasklist\WebRole1> start-azureemulator -launch
```

ブラウザーが開き、次のページが表示されます。

![My Task List というタイトルの Web ページと新しいタスクを追加するためのタスクとフィールドを含むテーブル](./media/storage-nodejs-use-table-storage-cloud-service-app/node44.png)

フォームを使用してアイテムを追加するか、完了済みのマークを付けることにより既存のアイテムを削除します。

## <a name="publishing-the-application-to-azure"></a>Azure にアプリケーションをデプロイする
Windows PowerShell ウィンドウで、次のコマンドレットを呼び出して、ホストされるサービスを Azure に再デプロイします。

```powershell
PS C:\node\tasklist\WebRole1> Publish-AzureServiceProject -name myuniquename -location datacentername -launch
```

**myuniquename** を、このアプリケーションの一意の名前に置き換えます。 **datacentername** を、Azure データ センターの名前で置き換えます (たとえば、**West US**)。

デプロイが完了すると、次のような応答が表示されます。

```
  PS C:\node\tasklist> publish-azureserviceproject -servicename tasklist -location "West US"
  WARNING: Publishing tasklist to Microsoft Azure. This may take several minutes...
  WARNING: 2:18:42 PM - Preparing runtime deployment for service 'tasklist'
  WARNING: 2:18:42 PM - Verifying storage account 'tasklist'...
  WARNING: 2:18:43 PM - Preparing deployment for tasklist with Subscription ID: 65a1016d-0f67-45d2-b838-b8f373d6d52e...
  WARNING: 2:19:01 PM - Connecting...
  WARNING: 2:19:02 PM - Uploading Package to storage service larrystore...
  WARNING: 2:19:40 PM - Upgrading...
  WARNING: 2:22:48 PM - Created Deployment ID: b7134ab29b1249ff84ada2bd157f296a.
  WARNING: 2:22:48 PM - Initializing...
  WARNING: 2:22:49 PM - Instance WebRole1_IN_0 of role WebRole1 is ready.
  WARNING: 2:22:50 PM - Created Website URL: http://tasklist.cloudapp.net/.
```

前と同様に **-launch** オプションを指定してあるので、発行が完了するとブラウザーが開かれ、Azure で実行されているアプリケーションが表示されます。

![My Task List ページを表示しているブラウザー ウィンドウ。 URL から、ページが Azure でホストされていることがわかります](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

## <a name="stopping-and-deleting-your-application"></a>アプリケーションの停止と削除
アプリケーションのデプロイ後に、コストがかかることを避けたり、無料評価版の有効期間中に他のアプリケーションを作成してデプロイしたりするために、アプリケーションを無効にしたい場合があります。

Azure では、消費されたサーバー時間の&1; 時間単位の料金が Web ロール インスタンスに課金されます。
インスタンスが実行されていない場合や停止状態の場合でも、アプリケーションを展開した直後からサーバー時間が消費されます。

次の手順では、アプリケーションの停止と削除の方法を示します。

1. Windows PowerShell ウィンドウで次のコマンドレットを実行し、前のセクションで作成したサービスの展開を停止します。

    ```powershell
    PS C:\node\tasklist\WebRole1> Stop-AzureService
    ```

   サービスの停止には、数分間かかる場合があります。 サービスが停止すると、停止したことを知らせるメッセージが表示されます。

2. サービスを削除するには、次のコマンドレットを呼び出します。

    ```powershell
    PS C:\node\tasklist\WebRole1> Remove-AzureService contosotasklist
    ```

   確認を求めるメッセージが表示されたら、「 **Y** 」と入力して、サービスを削除します。

   サービスの削除には、数分間かかる場合があります。 サービスが削除されると、削除されたことを知らせるメッセージが表示されます。

[Express を使用する Node.js Web アプリケーション]: http://azure.microsoft.com/develop/nodejs/tutorials/web-app-with-express/
[Azure でのデータの格納とアクセス]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[Node.js Web アプリケーション]: http://azure.microsoft.com/develop/nodejs/tutorials/getting-started/





<!--HONumber=Dec16_HO2-->


