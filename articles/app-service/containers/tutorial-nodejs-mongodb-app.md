---
title: Linux 上の Node.js (MEAN.js) と MongoDB - Azure App Service | Microsoft Docs
description: Cosmos DB データベースに接続された Node.js アプリを、MongoDB 接続文字列を使用して Azure App Service on Linux で動作させる方法について説明します。 このチュートリアルでは MEAN.js を使用します。
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 3a5f6b5b1f66542a534c9016c5d9d60a1273975f
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2019
ms.locfileid: "59544803"
---
# <a name="build-a-nodejs-and-mongodb-app-in-azure-app-service-on-linux"></a>Azure App Service on Linux で Node.js および MongoDB のアプリを構築する

> [!NOTE]
> この記事では、Linux 上の App Service にアプリをデプロイします。 _Windows_ 上の App Service にデプロイするには、[Azure での Node.js と MongoDB のアプリの構築](../app-service-web-tutorial-nodejs-mongodb-app.md)に関するページを参照してください。
>

[App Service on Linux](app-service-linux-intro.md) は、Linux オペレーティング システムを使用する、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供します。 このチュートリアルでは、Node.js アプリを作成して MongoDB データベースにローカルで接続し、Azure Cosmos DB の MongoDB 用 API でデータベースにデプロイする方法を示します。 完了すると、MEAN アプリケーション (MongoDB、Express、AngularJS、および Node.js) が App Service on Linux で実行されます。 単純化するために、サンプル アプリケーションでは [MEAN.js Web フレームワーク](https://meanjs.org/)を使用します。

![Azure App Service で実行されている MEAN.js アプリ](./media/tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure Cosmos DB の MongoDB 用 API を使用してデータベースを作成する
> * Node.js アプリを MongoDB に接続する
> * Azure にアプリケーションをデプロイする
> * データ モデルを更新し、アプリを再デプロイする
> * Azure から診断ログをストリーミングする
> * Azure Portal でアプリを管理する

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

1. [Git をインストールする](https://git-scm.com/)
2. [Node.js v6.0 以上と NPM をインストールする](https://nodejs.org/)
3. [Gulp.js をインストールします](https://gulpjs.com/) ([MEAN.js](https://meanjs.org/docs/0.5.x/#getting-started) で必要です)
4. [MongoDB Community Edition をインストールして実行する](https://docs.mongodb.com/manual/administration/install-community/)

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

config.domain の警告は無視します。 アプリが完全に読み込まれると、次のようなメッセージが表示されます。

```txt
--
MEAN.JS - Development Environment

Environment:     development
Server:          http://0.0.0.0:3000
Database:        mongodb://localhost/mean-dev
App version:     0.5.0
MEAN.JS version: 0.5.0
--
```

ブラウザーで `http://localhost:3000` にアクセスします。 上部のメニューの **[サインアップ]** をクリックし、テスト ユーザーを作成します。 

MEAN.js サンプル アプリケーションでは、ユーザー データをデータベースに格納します。 ユーザーを作成してサインインすると、アプリがローカルの MongoDB データベースにデータを書き込みます。

![MongoDB に正常に接続されている MEAN.js](./media/tutorial-nodejs-mongodb-app/mongodb-connect-success.png)

**[管理者] > [Manage Articles]\(記事の管理\)** を選択して、いくつかの記事を追加します。

任意のタイミングで Node.js を停止するには、ターミナルで `Ctrl+C` キーを押します。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-production-mongodb"></a>運用 MongoDB を作成する

この手順では、Azure Cosmos DB の MongoDB 用 API を使用してデータベース アカウントを作成します。 アプリを Azure にデプロイすると、このクラウド データベースがアプリで使用されます。

### <a name="create-a-resource-group"></a>リソース グループの作成

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-a-cosmos-db-account"></a>Cosmos DB アカウントを作成する

Cloud Shell で、[`az cosmosdb create`](/cli/azure/cosmosdb?view=azure-cli-latest#az-cosmosdb-create) コマンドを使用して Cosmos DB アカウントを作成します。

次のコマンドで、*\<cosmosdb-name>* プレースホルダーを一意の Cosmos DB 名に置き換えます。 この名前は、Cosmos DB エンドポイント (`https://<cosmosdb-name>.documents.azure.com/`) の一部として使用されるため、Azure のすべての Cosmos DB アカウントで一意である必要があります。 この名前に含めることができるのは英小文字、数字、およびハイフン (-) 文字のみで、文字数は 3 ～ 50 文字にする必要があります。

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
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
  "documentEndpoint": "https://<cosmosdb-name>.documents.azure.com:443/",
  "failoverPolicies":
  ...
  < Output truncated for readability >
}
```

## <a name="connect-app-to-production-configured-with-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API を使用して構成されたアプリを運用データベースに接続する

この手順では、MongoDB 接続文字列を使用して、MEAN.js サンプル アプリケーションを、先ほど作成した Cosmos DB データベースに接続します。

### <a name="retrieve-the-database-key"></a>データベース キーの取得

Cosmos DB データベースに接続するには、データベース キーが必要です。 Cloud Shell で、[`az cosmosdb list-keys`](/cli/azure/cosmosdb?view=azure-cli-latest#az-cosmosdb-list-keys) コマンドを使用して主キーを取得します。

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb-name> --resource-group myResourceGroup
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

ローカルの MEAN.js リポジトリの "_config/env/_" フォルダーに、"_local-production.js_" という名前のファイルを作成します。 "_.gitignore_" は、リポジトリからこのファイルを保持するように構成されます。

ここに次のコードをコピーします。 2 つの *\<cosmosdb-name>* プレースホルダーを Cosmos DB データベース名で置き換え、*\<primary-master-key>* プレースホルダーを前の手順でコピーしたキーで置き換えます。

```javascript
module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb-name>:<primary-master-key>@<cosmosdb-name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false'
  }
};
```

[Cosmos DB では SSL が必須](../../cosmos-db/connect-mongodb-account.md#connection-string-requirements)なので、`ssl=true` オプションは必須です。

変更を保存します。

### <a name="test-the-application-in-production-mode"></a>運用モードでのアプリケーションのテスト

ローカル ターミナル ウィンドウで次のコマンドを実行して、運用環境用のスクリプトを小さくしてバンドルします。 このプロセスにより、運用環境に必要なファイルが生成されます。

```bash
gulp prod
```

ローカル ターミナル ウィンドウで次のコマンドを実行して、_config/env/local-production.js_ で構成した接続文字列を使用します。 証明書エラーと config.domain の警告は無視します。

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
Database:        mongodb://<cosmosdb-name>:<primary-master-key>@<cosmosdb-name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false
App version:     0.5.0
MEAN.JS version: 0.5.0
```

ブラウザーで `http://localhost:8443` にアクセスします。 上部のメニューの **[サインアップ]** をクリックし、テスト ユーザーを作成します。 ユーザーの作成とサインインに成功すると、アプリが Azure の Cosmos DB データベースにデータを書き込みます。

ターミナルで、`Ctrl+C` キーを押して Node.js を停止します。

## <a name="deploy-app-to-azure"></a>アプリを Azure にデプロイする

この手順では、Node.js アプリケーションを Azure App Service にデプロイします。

### <a name="configure-local-git-deployment"></a>ローカル Git デプロイを構成する

[!INCLUDE [Configure a deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service プランを作成する

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

<a name="create"></a>

### <a name="create-a-web-app"></a>Web アプリを作成する

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-nodejs-linux-no-h.md)] 

### <a name="configure-an-environment-variable"></a>環境変数の構成

既定では、MEAN.js プロジェクトは _config/env/local-production.js_ を Git リポジトリ外で保持します。 したがって、Azure アプリでは、アプリ設定を使用して MongoDB 接続文字列を定義します。

アプリ設定を設定するには、Cloud Shell で [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) コマンドを使用します。

次の例では、Azure アプリの `MONGODB_URI` アプリ設定を構成します。 *\<app-name>*、*\<cosmosdb-name>*、および *\<primary-master-key>* プレースホルダーを置き換えます。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings MONGODB_URI="mongodb://<cosmosdb-name>:<primary-master-key>@<cosmosdb-name>.documents.azure.com:10250/mean?ssl=true"
```

Node.js コードでは、任意の環境変数にアクセスする場合と同じように、`process.env.MONGODB_URI` を使用して、この[アプリ設定にアクセス](configure-language-nodejs.md#access-environment-variables)します。

ローカル MEAN.js リポジトリで、運用環境固有の構成を含む _config/env/production.js_ を開きます (_config/env/local-production.js_ ではありません)。 既定の MEAN.js アプリは、作成した `MONGODB_URI` 環境変数を使用するように既に構成されています。

```javascript
db: {
  uri: ... || process.env.MONGODB_URI || ...,
  ...
},
```

### <a name="push-to-azure-from-git"></a>Git から Azure へのプッシュ

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

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
To https://<app-name>.scm.azurewebsites.net/<app-name>.git
 * [new branch]      master -> master
```

デプロイ プロセスにより、`npm install` の後、[Gulp](https://gulpjs.com/) が実行されます。 App Service では、デプロイ時に Gulp および Grunt タスクが実行されません。そのため、このサンプル リポジトリには、それを有効にする追加の 2 つのファイルがルート ディレクトリにあります。

- _.deployment_ - このファイルは、カスタム デプロイ スクリプトとして `bash deploy.sh`を実行するよう App Service に指示します。
- _deploy.sh_- カスタム デプロイ スクリプト。 ファイルを確認すると、`npm install` と `bower install` の後に `gulp prod` が実行されることがわかります。

この方法を使用して、Git ベースのデプロイに任意の手順を追加できます。 任意の時点で Azure アプリを再起動しても、これらの自動タスクが App Service によって再び実行されることはありません。 詳細については、「[Run Grunt/Bower/Gulp](configure-language-nodejs.md#run-gruntbowergulp)」(Grunt/Bower/Gulp を実行する) を参照してください。

### <a name="browse-to-the-azure-app"></a>Azure アプリを参照する

Web ブラウザーを使用して、デプロイされたアプリを参照します。

```bash
http://<app-name>.azurewebsites.net
```

上部のメニューの **[サインアップ]** をクリックし、ダミー ユーザーを作成します。

操作が成功し、作成されたユーザーにアプリが自動的にサインインすれば、Azure の MEAN.js アプリは Azure Cosmos DB の MongoDB 用 API に接続されています。

![Azure App Service で実行されている MEAN.js アプリ](./media/tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

**[管理者] > [Manage Articles]\(記事の管理\)** を選択して、いくつかの記事を追加します。

**お疲れさまでした。** データ主導型の Node.js アプリが Azure App Service on Linux で実行されています。

## <a name="update-data-model-and-redeploy"></a>データ モデルを更新し、再デプロイする

この手順では、`article` データ モデルを変更し、変更内容を Azure に発行します。

### <a name="update-the-data-model"></a>データ モデルを更新する

ローカル MEAN.js リポジトリで、"_modules/articles/server/models/article.server.model.js_" を開きます。

`ArticleSchema` に `comment` という `String` 型を追加します。 完了すると、スキーマ コードは次のようになります。

```javascript
let ArticleSchema = new Schema({
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
  let article = req.article;

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

ローカル ターミナル ウィンドウで、変更を運用モードでもう一度テストします。

```bash
gulp prod
NODE_ENV=production node server.js
```

ブラウザーで `http://localhost:8443` にアクセスし、サインインしていることを確認します。

**[管理者] > [Manage Articles]\(記事の管理\)** を選択し、**+** ボタンを選択して記事を追加します。

新しい `Comment` テキスト ボックスが表示されます。

![記事に追加されたコメント フィールド](./media/tutorial-nodejs-mongodb-app/added-comment-field.png)

ターミナルで、`Ctrl+C` キーを押して Node.js を停止します。

### <a name="publish-changes-to-azure"></a>Azure に変更を発行する

変更を Git にコミットし、コード変更を Azure にプッシュします。

```bash
git commit -am "added article comment"
git push azure master
```

`git push` が完了したら、Azure アプリに移動し、新機能を試します。

![Azure に発行されたモデルとデータベースの変更](media/tutorial-nodejs-mongodb-app/added-comment-field-published.png)

以前に追加した記事は引き続き表示されます。 Cosmos DB の既存のデータは失われません。 また、データ スキーマは更新され、既存のデータはそのまま残ります。

## <a name="stream-diagnostic-logs"></a>診断ログをストリーミングする

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-your-azure-app"></a>Azure アプリを管理する

[Azure portal](https://portal.azure.com) に移動し、お客様が作成したアプリを表示します。

左側のメニューで **[App Services]** をクリックしてから、お客様の Azure アプリの名前をクリックします。

![Azure アプリへのポータル ナビゲーション](./media/tutorial-nodejs-mongodb-app/access-portal.png)

既定では、ポータルにはアプリの **[概要]** ページが表示されます。 このページでは、アプリの動作状態を見ることができます。 ここでは、参照、停止、開始、再開、削除のような基本的な管理タスクも行うことができます。 ページの左側にあるタブは、開くことができるさまざまな構成ページを示しています。

![Azure Portal の [App Service] ページ](./media/tutorial-nodejs-mongodb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>次の手順

ここで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure Cosmos DB の MongoDB 用 API を使用してデータベースを作成する
> * Node.js アプリをデータベースに接続する
> * Azure にアプリケーションをデプロイする
> * データ モデルを更新し、アプリを再デプロイする
> * Azure からターミナルにログをストリーミングする
> * Azure Portal でアプリを管理する

次のチュートリアルに進んで、カスタム DNS 名をアプリにマップする方法を確認してください。

> [!div class="nextstepaction"]
> [チュートリアル:カスタム DNS 名をアプリにマップする](../app-service-web-tutorial-custom-domain.md)

または、他のリソースを参照してください。

> [!div class="nextstepaction"]
> [Node.js アプリの構成](configure-language-nodejs.md)