---
title: "Azure で Node.js とMongoDB Web アプリを構築する | Microsoft Docs"
description: "MongoDB 接続文字列を使用して Cosmos DB データベースに接続する Node.js アプリを Azure で動作させる方法について説明します。"
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 05/04/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: f7479260c7c2e10f242b6d8e77170d4abe8634ac
ms.openlocfilehash: 14e399c06b21d9cabab28bdb195b02eb422f2d00
ms.contentlocale: ja-jp
ms.lasthandoff: 06/21/2017

---
# <a name="build-a-nodejs-and-mongodb-web-app-in-azure"></a>Azure で Node.js とMongoDB Web アプリを構築する

Azure Web Apps では、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供しています。 このチュートリアルでは、Azure で Node.js Web アプリを作成し、MongoDB データベースに接続する方法について説明します。 完了すると、MEAN アプリケーション (MongoDB、Express、AngularJS、および Node.js) が [Azure App Service](app-service-web-overview.md) で実行されます。 単純化するために、サンプル アプリケーションでは [MEAN.js Web フレームワーク](http://meanjs.org/)を使用します。

![Azure App Service で実行されている MEAN.js アプリ](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

ここでは、次の内容について学習します。

> [!div class="checklist"]
> * Azure で MongoDB データベースを作成する
> * Node.js アプリを MongoDB に接続する
> * Azure にアプリケーションをデプロイする
> * データ モデルを更新し、アプリを再デプロイする
> * Azure から診断ログをストリーミングする
> * Azure ポータルでアプリを管理する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

1. [Git をインストールする](https://git-scm.com/)
1. [Node.js および NPM をインストールする](https://nodejs.org/)
1. [Gulp.js をインストールします](http://gulpjs.com/) ([MEAN.js](http://meanjs.org/docs/0.5.x/#getting-started) で必要です)
1. [MongoDB Community Edition をインストールして実行する](https://docs.mongodb.com/manual/administration/install-community/) 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このトピックでは、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

## <a name="test-local-mongodb"></a>ローカル MongoDB をテストする

ターミナル ウィンドウを開き、`cd` コマンドで MongoDB インストールの `bin` ディレクトリに移動します。 このチュートリアルでは、ターミナル ウィンドウを使ってすべてのコマンドを実行します。

ターミナルで `mongo` を実行して、ローカルの MongoDB サーバーに接続します。

```bash
mongo
```

接続に成功した場合、MongoDB データベースは既に稼働しています。 成功しない場合は、「[Install MongoDB Community Edition (MongoDB Community Edition をインストールする)](https://docs.mongodb.com/manual/administration/install-community/)」の手順に従って、ローカルの MongoDB データベースが開始されていることを確認します。 多くの場合、MongoDB はインストールされていますが、`mongod` を実行して起動する必要があります。 

MongoDB データベースのテストが完了したら、ターミナルで `Ctrl+C` キーを押します。 

## <a name="create-local-nodejs-app"></a>ローカル Node.js アプリを作成する

この手順では、ローカル Node.js プロジェクトを設定します。

### <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

ターミナル ウィンドウから、`cd` コマンドで作業ディレクトリに移動します。  

次のコマンドを実行して、サンプル レポジトリを複製します。 

```bash
git clone https://github.com/Azure-Samples/meanjs.git
```

このサンプル レポジトリには、[MEAN.js レポジトリ](https://github.com/meanjs/mean)のコピーが含まれています。 このコピーは App Service で実行するために変更されています (詳細については、MEAN.js リポジトリの [README ファイル](https://github.com/Azure-Samples/meanjs/blob/master/README.md)をご覧ください)。

### <a name="run-the-application"></a>アプリケーションの実行

次のコマンドを実行して、必要なパッケージをインストールし、アプリケーションを起動します。

```bash
cd meanjs
npm install
npm start
```

アプリが完全に読み込まれると、次のようなメッセージが表示されます。

```
--
MEAN.JS - Development Environment

Environment:     development
Server:          http://0.0.0.0:3000
Database:        mongodb://localhost/mean-dev
App version:     0.5.0
MEAN.JS version: 0.5.0
--
```

ブラウザーで http://localhost:3000 にアクセスします。 上部のメニューの **[サインアップ]** をクリックし、テスト ユーザーを作成します。 

MEAN.js サンプル アプリケーションでは、ユーザー データをデータベースに格納します。 ユーザーを作成してサインインすると、アプリがローカルの MongoDB データベースにデータを書き込みます。

![MongoDB に正常に接続されている MEAN.js](./media/app-service-web-tutorial-nodejs-mongodb-app/mongodb-connect-success.png)

**[管理者] > [Manage Articles]\(記事の管理\)** を選択して、いくつかの記事を追加します。

任意のタイミングで Node.js を停止するには、ターミナルで `Ctrl+C` キーを押します。 

## <a name="create-production-mongodb"></a>運用 MongoDB を作成する

この手順では、Azure で MongoDB データベースを作成します。 アプリを Azure にデプロイすると、このクラウド データベースがアプリで使用されます。

このチュートリアルでは、MongoDB に [Azure Cosmos DB](/azure/documentdb/) を使用します。 Cosmos DB は MongoDB のクライアント接続をサポートします。

### <a name="log-in-to-azure"></a>Azure へのログイン

Azure CLI 2.0 を使用して、Azure でアプリをホストするために必要なリソースを作成します。 [az login](/cli/azure/#login) コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。

```azurecli-interactive
az login
```   

### <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#create) コマンドでリソース グループを作成します。

[!INCLUDE [Resource group intro](../../includes/resource-group.md)]

次の例は、西ヨーロッパ リージョンにリソース グループを作成します。

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

使用できる場所の一覧を表示するには、[az appservice list-locations](/cli/azure/appservice#list-locations) Azure CLI コマンドを使用します。 

### <a name="create-a-cosmos-db-account"></a>Cosmos DB アカウントを作成する

[az cosmosdb create](/cli/azure/cosmosdb#create) コマンドを使用して Cosmos DB アカウントを作成します。

次のコマンドで、*\<cosmosdb_name>* プレースホルダーを一意の Cosmos DB 名に置き換えます。 この名前は、Cosmos DB エンドポイント (`https://<cosmosdb_name>.documents.azure.com/`) の一部として使用されるため、Azure のすべての Cosmos DB アカウントで一意である必要があります。 この名前に含めることができるのは英小文字、数字、およびハイフン (-) 文字のみで、文字数は 3 ～ 50 文字にする必要があります。

```azurecli-interactive
az cosmosdb create \
    --name <cosmosdb_name> \
    --resource-group myResourceGroup \
    --kind MongoDB
```

*--kind MongoDB* パラメーターにより、MongoDB のクライアント接続が有効になります。

Cosmos DB アカウントが作成されると、Azure CLI によって次の例のような情報が表示されます。

```json
{
  "consistencyPolicy":
  {
    "defaultConsistencyLevel": "Session",
    "maxIntervalInSeconds": 5,
    "maxStalenessPrefix": 100
  },
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb_name>.documents.azure.com:443/",
  "failoverPolicies": 
  ...
  < Output truncated for readability >
}
```

## <a name="connect-app-to-production-mongodb"></a>アプリを運用 MongoDB に接続する

この手順では、MongoDB 接続文字列を使用して、MEAN.js サンプル アプリケーションを、先ほど作成した Cosmos DB データベースに接続します。 

### <a name="retrieve-the-database-key"></a>データベース キーの取得

Cosmos DB データベースに接続するには、データベース キーが必要です。 [az cosmosdb list-keys](/cli/azure/cosmosdb#list-keys) コマンドを使用して、プライマリ キーを取得します。

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb_name> --resource-group myResourceGroup
```

Azure CLI によって次の例のような情報が表示されます。

```json
{
  "primaryMasterKey": "RS4CmUwzGRASJPMoc0kiEvdnKmxyRILC9BWisAYh3Hq4zBYKr0XQiSE4pqx3UchBeO4QRCzUt1i7w0rOkitoJw==",
  "primaryReadonlyMasterKey": "HvitsjIYz8TwRmIuPEUAALRwqgKOzJUjW22wPL2U8zoMVhGvregBkBk9LdMTxqBgDETSq7obbwZtdeFY7hElTg==",
  "secondaryMasterKey": "Lu9aeZTiXU4PjuuyGBbvS1N9IRG3oegIrIh95U6VOstf9bJiiIpw3IfwSUgQWSEYM3VeEyrhHJ4rn3Ci0vuFqA==",
  "secondaryReadonlyMasterKey": "LpsCicpVZqHRy7qbMgrzbRKjbYCwCKPQRl0QpgReAOxMcggTvxJFA94fTi0oQ7xtxpftTJcXkjTirQ0pT7QFrQ=="
}
```

`primaryMasterKey` の値をコピーします。 この情報は、次の手順に必要です。

<a name="devconfig"></a>
### <a name="configure-the-connection-string-in-your-nodejs-application"></a>Node.js アプリケーションでの接続文字列の構成

MEAN.js レポジトリの _config/env/production.js_ を開きます。

`db` オブジェクトで、`uri` の値を更新します。

* 2 つの *\<cosmosdb_name>* プレースホルダーを Cosmos DB データベース名に置き換えます。
* *\<primary_master_key>* プレースホルダーを前の手順でコピーしたキーに置き換えます。

`db` オブジェクトのコードを示します。

```javascript
db: {
  uri: 'mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false',
  ...
},
```

[Cosmos DB では SSL が必須](../cosmos-db/connect-mongodb-account.md#connection-string-requirements)なので、`ssl=true` オプションは必須です。 

変更を保存します。

### <a name="test-the-application-in-production-mode"></a>運用モードでのアプリケーションのテスト 

次のコマンドを実行して、運用環境用のスクリプトを小さくしてバンドルします。 このプロセスにより、運用環境に必要なファイルが生成されます。

```bash
gulp prod
```

次のコマンドを実行して、_config/env/production.js_ に構成した接続文字列を使用します。

```bash
NODE_ENV=production node server.js
```

`NODE_ENV=production` により、運用環境で実行するよう Node.js に指示する環境変数を設定します。  `node server.js` により、リポジトリのルートにある `server.js` を使用して Node.js サーバーを起動します。 このようにして、Node.js アプリケーションを Azure に読み込みます。 

アプリが読み込まれたら、運用環境で実行されていることを確認します。

```
--
MEAN.JS

Environment:     production
Server:          http://0.0.0.0:8443
Database:        mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false
App version:     0.5.0
MEAN.JS version: 0.5.0
```

ブラウザーで http://localhost:8443 にアクセスします。 上部のメニューの **[サインアップ]** をクリックし、テスト ユーザーを作成します。 ユーザーの作成とサインインに成功すると、アプリが Azure の Cosmos DB データベースにデータを書き込みます。 

ターミナルで、`Ctrl+C` キーを押して Node.js を停止します。 

## <a name="deploy-app-to-azure"></a>アプリを Azure にデプロイする

この手順では、MongoDB に接続している Node.js アプリケーションを Azure App Service にデプロイします。

### <a name="create-an-app-service-plan"></a>App Service プランを作成する

[az appservice plan create](/cli/azure/appservice/plan#create) コマンドで、App Service プランを作成します。 

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

次の例では、**Free** 価格レベルを使用して、_myAppServicePlan_ という名前の App Service プランを作成します。

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

App Service プランが作成されると、Azure CLI によって、次の例のような情報が表示されます。

```json 
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
```

### <a name="create-a-web-app"></a>Web アプリを作成する

[az webapp create](/cli/azure/webapp#create) コマンドを使って、`myAppServicePlan` App Service プランに Web アプリを作成します。 

Web アプリにより、コードをデプロイするためのホスト領域が取得され、デプロイされたアプリケーションを表示するための URL が提供されます。 Web アプリを作成するには  を使用します。 

次のコマンドで、*\<app_name>* プレースホルダーを一意のアプリ名に置き換えます。 この名前は、Web アプリの既定の URL の一部として使用されるため、Azure App Service のすべてのアプリで一意である必要があります。 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

Web アプリが作成されると、Azure CLI によって次の例のような情報が表示されます。 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  ...
  < Output has been truncated for readability >
}
```

### <a name="configure-an-environment-variable"></a>環境変数の構成

チュートリアルの前半で、_config/env/production.js_ にデータベース接続文字列をハードコードしました。 セキュリティのベスト プラクティスに従って、この機密データを Git リポジトリに保存しないようにする必要があります。 Azure で実行されるアプリでは、環境変数を使用できます。

App Service では、[az webapp config appsettings update](/cli/azure/webapp/config/appsettings#update) コマンドを使用して、環境変数を "_アプリ設定_" として設定します。 

次の例では、Azure Web アプリの `MONGODB_URI` アプリ設定を構成します。 *\<app_name>*、*\<cosmosdb_name>*、および *\<primary_master_key>* プレースホルダーを置き換えます。

```azurecli-interactive
az webapp config appsettings update \
    --name <app_name> \
    --resource-group myResourceGroup \
    --settings MONGODB_URI="mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true"
```

Node.js コードでは、任意の環境変数にアクセスする場合と同じように、`process.env.MONGODB_URI` を使用して、このアプリ設定にアクセスします。 

ここで、次のコマンドを使用して、_config/env/production.js_ の変更を元に戻します。

```bash
git checkout -- .
```

_config/env/production.js_ をもう一度開きます。 既定の MEAN.js アプリは、作成した `MONGODB_URI` 環境変数を使用するように既に構成されています。

```javascript
db: {
  uri: ... || process.env.MONGODB_URI || ...,
  ...
},
```

### <a name="configure-local-git-deployment"></a>ローカル Git デプロイの構成 

[az webapp deployment user set](/cli/azure/webapp/deployment/user#set) コマンドを使用して、デプロイ用の資格情報を作成します。

アプリケーションを Azure App Service にデプロイするには、FTP、ローカル Git、GitHub、Visual Studio Team Services、BitBucket など、さまざまな方法があります。 FTP とローカル Git の場合、デプロイを認証するには、サーバー上で構成されたデプロイ ユーザーが必要です。 このデプロイ ユーザーはアカウント レベルであり、Azure サブスクリプション アカウントとは異なります。 このデプロイ ユーザーは、1 回だけ構成する必要があります。

次のコマンドで、*\<user-name>* と *\<password>* を新しいユーザー名とパスワードで置き換えます。 ユーザー名は一意である必要があります。 パスワードは長さが 8 文字以上で、文字、数字、記号のうち 2 つを含む必要があります。 ` 'Conflict'. Details: 409` エラーが発生した場合は、ユーザー名を変更します。 ` 'Bad Request'. Details: 400` エラーが発生した場合は、より強力なパスワードを使用します。

```azurecli-interactive
az appservice web deployment user set --user-name <username> --password <password>
```

後でアプリをデプロイするときに使用するため、ユーザー名とパスワードを記録しておきます。

[az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source#config-local-git) コマンドを使用して、Azure Web アプリへのローカル Git アクセスを構成します。 

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group myResourceGroup
```

デプロイ ユーザーが構成されると、Azure CLI によって、次の形式の Azure Web アプリのデプロイ URL が表示されます。

```bash 
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git 
``` 

次の手順で使用するため、ターミナルからの出力をコピーしておきます。 

### <a name="push-to-azure-from-git"></a>Git から Azure へのプッシュ

ローカル Git リポジトリに Azure リモートを追加します。 

```bash
git remote add azure <paste_copied_url_here> 
```

Node.js アプリケーションをデプロイするために、Azure リモートにプッシュします。 デプロイ ユーザーの作成時に指定したパスワードを入力するように求めるメッセージが表示されます。 

```bash
git push azure master
```

デプロイ中、Azure App Service は進行状況について Git と通信します。

```bash
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
``` 

デプロイ プロセスにより、`npm install` の後、[Gulp](http://gulpjs.com/) が実行されます。 App Service では、デプロイ時に Gulp および Grunt タスクが実行されません。そのため、このサンプル リポジトリには、それを有効にする追加の 2 つのファイルがルート ディレクトリにあります。 

- _.deployment_ - このファイルは、カスタム デプロイ スクリプトとして `bash deploy.sh`を実行するよう App Service に指示します。
- _deploy.sh_- カスタム デプロイ スクリプト。 ファイルを確認すると、`npm install` と `bower install` の後に `gulp prod` が実行されることがわかります。 

この方法を使用して、Git ベースのデプロイに任意の手順を追加できます。 任意の時点で Azure Web アプリを再起動しても、これらの自動タスクが App Service によって再び実行されることはありません。

### <a name="browse-to-the-azure-web-app"></a>Azure Web アプリを参照する 

Web ブラウザーを使用して、デプロイされた Web アプリを参照します。 

```bash 
http://<app_name>.azurewebsites.net 
``` 

上部のメニューの **[サインアップ]** をクリックし、ダミー ユーザーを作成します。 

操作が成功し、作成したユーザーにアプリが自動的にサインインすれば、Azure の MEAN.js アプリは MongoDB (Cosmos DB) データベースに接続しています。 

![Azure App Service で実行されている MEAN.js アプリ](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

**[管理者] > [Manage Articles]\(記事の管理\)** を選択して、いくつかの記事を追加します。 

**お疲れさまでした。** データ主導型の Node.js アプリが Azure App Service で実行されています。

## <a name="update-data-model-and-redeploy"></a>データ モデルを更新し、再デプロイする

この手順では、`article` データ モデルを変更し、変更内容を Azure に発行します。

### <a name="update-the-data-model"></a>データ モデルを更新する

_modules/articles/server/models/article.server.model.js_を開きます。

`ArticleSchema` に `comment` という `String` 型を追加します。 完了すると、スキーマ コードは次のようになります。

```javascript
var ArticleSchema = new Schema({
  ...,
  user: {
    type: Schema.ObjectId,
    ref: 'User'
  },
  comment: {
    type: String,
    default: '',
    trim: true
  }
});
```

### <a name="update-the-articles-code"></a>記事コードを更新する

`comment` を使用するように、`articles` コードの残りの部分を更新します。

5 つのファイルを変更する必要があります (1 つのサーバー コントローラーと 4 つのクライアント ビュー)。 

_modules/articles/server/controllers/articles.server.controller.js_ を開きます。

`update` 関数に `article.comment` の割り当てを追加します。 完成した `update` 関数のコードを次に示します。

```javascript
exports.update = function (req, res) {
  var article = req.article;

  article.title = req.body.title;
  article.content = req.body.content;
  article.comment = req.body.comment;

  ...
};
```

_modules/articles/client/views/view-article.client.view.html_ を開きます。

`</section>` 終了タグのすぐ上に、`comment` と残りの記事データを表示する次の行を追加します。

```HTML
<p class="lead" ng-bind="vm.article.comment"></p>
```

_modules/articles/client/views/list-articles.client.view.html_ を開きます。

`</a>` 終了タグのすぐ上に、`comment` と残りの記事データを表示する次の行を追加します。

```HTML
<p class="list-group-item-text" ng-bind="article.comment"></p>
```

_modules/articles/client/views/admin/list-articles.client.view.html_ を開きます。

`<div class="list-group">` 要素内の `</a>` 終了タグのすぐ上に、`comment` と残りの記事データを表示する次の行を追加します。

```HTML
<p class="list-group-item-text" data-ng-bind="article.comment"></p>
```

_modules/articles/client/views/admin/form-article.client.view.html_ を開きます。

次のような送信ボタンを含む `<div class="form-group">` 要素を探します。

```HTML
<div class="form-group">
  <button type="submit" class="btn btn-default">{{vm.article._id ? 'Update' : 'Create'}}</button>
</div>
```

このタグのすぐ上に、もう 1 つ `<div class="form-group">` 要素を追加して、ユーザーが `comment` フィールドを編集できるようにします。 新しい要素は次のようになります。

```HTML
<div class="form-group">
  <label class="control-label" for="comment">Comment</label>
  <textarea name="comment" data-ng-model="vm.article.comment" id="comment" class="form-control" cols="30" rows="10" placeholder="Comment"></textarea>
</div>
```

### <a name="test-your-changes-locally"></a>変更をローカルでテストする

すべての変更を保存します。

再度運用モードで変更をテストします。

```bash
gulp prod
NODE_ENV=production node server.js
```

> [!NOTE]
> _config/env/production.js_ が元に戻っていることに注意してください。`MONGODB_URI` 環境変数は、ローカル コンピューターではなく、Azure Web アプリにのみ設定されます。 構成ファイルを確認すると、運用構成が既定でローカルの MongoDB データベースを使用するようになっていることがわかります。 そのため、コードの変更をローカルでテストする際に、運用データを操作することはありません。

ブラウザーで `http://localhost:8443` にアクセスし、サインインしていることを確認します。

**[管理者] > [Manage Articles]\(記事の管理\)** を選択し、**+** ボタンを選択して記事を追加します。

新しい `Comment` テキスト ボックスが表示されます。

![記事に追加されたコメント フィールド](./media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field.png)

ターミナルで、`Ctrl+C` キーを押して Node.js を停止します。 

### <a name="publish-changes-to-azure"></a>Azure に変更を発行する

変更を Git にコミットし、コード変更を Azure にプッシュします。

```bash
git commit -am "added article comment"
git push azure master
```

`git push` が完了したら、Azure Web アプリに移動し、新機能を試します。

![Azure に発行されたモデルとデータベースの変更](media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field-published.png)

以前に追加した記事は引き続き表示されます。 Cosmos DB の既存のデータは失われません。 また、データ スキーマは更新され、既存のデータはそのまま残ります。

## <a name="stream-diagnostic-logs"></a>診断ログをストリーミングする 

Azure App Service で Node.js アプリケーションを実行している場合、コンソール ログをターミナルにパイプできます。 このようにすると、アプリケーション エラーのデバッグに役立つ同じ診断メッセージを取得できます。

ログのストリーミングを開始するには、[az webapp log tail](/cli/azure/webapp/log#tail) コマンドを使用します。

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
``` 

ログのストリーミングが開始されたら、ブラウザーで Azure Web アプリを最新の情報に更新して、Web トラフィックを取得します。 ターミナルにパイプされたコンソール ログが表示されます。

`Ctrl+C` キーを押して、任意のタイミングでログのストリーミングを停止します。 

## <a name="manage-your-azure-web-app"></a>Azure Web アプリを管理する

[Azure Portal](https://portal.azure.com) に移動し、作成した Web アプリを表示します。

左側のメニューで **[App Services (App Services)]** をクリックした後、Azure Web アプリの名前をクリックします。

![Azure Web アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-nodejs-mongodb-app/access-portal.png)

既定では、ポータルは Web アプリの **[概要]** ページを表示します。 このページでは、アプリの動作状態を見ることができます。 ここでは、参照、停止、開始、再開、削除のような基本的な管理タスクも行うことができます。 ページの左側にあるタブは、開くことができるさまざまな構成ページを示しています。

![Azure Portal の [App Service] ページ](./media/app-service-web-tutorial-nodejs-mongodb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>次のステップ

ここで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure で MongoDB データベースを作成する
> * Node.js アプリを MongoDB に接続する
> * Azure にアプリケーションをデプロイする
> * データ モデルを更新し、アプリを再デプロイする
> * Azure からターミナルにログをストリーミングする
> * Azure ポータルでアプリを管理する

次のチュートリアルに進み、カスタム DNS 名を Web アプリにマップする方法を学習してください。

> [!div class="nextstepaction"] 
> [既存のカスタム DNS 名を Azure Web Apps にマップする](app-service-web-tutorial-custom-domain.md)

