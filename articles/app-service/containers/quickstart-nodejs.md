---
title: Node.js Web アプリを作成する - Azure App Service
description: Azure App Service に Node.js アプリをデプロイする方法
author: msangapu
ms.author: msangapu
ms.date: 08/12/2019
ms.topic: quickstart
ms.service: app-service
ms.devlang: javascript
ms.openlocfilehash: 8d679a95cc89c2ae7774b7f7b51b9d0aadd89d12
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390972"
---
# <a name="create-a-nodejs-app-in-azure"></a>Azure で Node.js アプリを作成する

Azure App Service では、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供しています。 このクイック スタートでは、Azure App Service に Node.js アプリをデプロイする方法を示します。

## <a name="prerequisites"></a>前提条件

Azure アカウントをお持ちでない場合は、無料アカウントに[今すぐサインアップ](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension)してください。200 ドルの Azure クレジットを使用してさまざまな組み合わせのサービスをお試しいただけます。

[Node.js と npm](https://nodejs.org/en/download) (Node.js のパッケージ マネージャー) と共に [Visual Studio Code](https://code.visualstudio.com/) がインストールされている必要があります。

また、[Azure App Service 拡張機能](vscode:extension/ms-azuretools.vscode-azureappservice)もインストールする必要があります。これにより、Azure PaaS (サービスとしてのプラットフォーム) 上で Linux Web アプリを作成、管理、デプロイすることができます。

### <a name="sign-in"></a>サインイン

拡張機能のインストール後、自分の Azure アカウントにログインします。 アクティビティ バーで Azure のロゴをクリックし、**Azure App Service** エクスプローラーを表示します。 **[Azure にサインイン]** をクリックして、指示に従います。

![Azure にサインイン](./media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>トラブルシューティング

**"サブスクリプション名 <サブスクリプション ID> が見つかりません"** というエラーが表示された場合、原因としては、プロキシの内側にいるために、Azure API に到達できないことが考えられます。 ご利用のターミナルで `export` を使用して、自分のプロキシ情報で `HTTP_PROXY` と `HTTPS_PROXY` の環境変数を構成してください。

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

環境変数を設定しても問題が解決しない場合は、下の **[問題が発生しました]** ボタンをクリックしてお問い合わせください。

### <a name="prerequisite-check"></a>前提条件のチェック

続行する前に、すべての前提条件がインストールされ、構成されていることを確認してください。

VS Code では、ステータス バーに自分の Azure メール アドレスが、**Azure App Service** エクスプローラーに自分のサブスクリプションが表示されます。

> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>Node.js アプリケーションの作成

次に、クラウドにデプロイできる Node.js アプリケーションを作成します。 このクイックスタートでは、アプリケーション ジェネレーターを使用して、アプリケーションをターミナルからすばやくスキャフォールディングします。

> [!TIP]
> [Node.js チュートリアル](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial)を既に完了している場合は、「[Web サイトのデプロイ](#deploy-the-website)」に進んでかまいません。

### <a name="install-the-express-generator"></a>Express ジェネレーターのインストール

[Express](https://www.expressjs.com) は、Node.js アプリケーションの構築と実行に広く使われているフレームワークです。 [Express ジェネレーター](https://expressjs.com/en/starter/generator.html) ツールを使用して新しい Express アプリケーションをスキャフォールディング (作成) できます。 Express ジェネレーターは npm モジュールとして提供されており、npm コマンドライン ツール (`npm`) を使用してインストールされます。

```bash
npm install -g express-generator
```

`-g` スイッチを指定すると、自分のマシンに Express ジェネレーターがグローバルにインストールされて、どこからでも実行できるようになります。

### <a name="scaffold-a-new-application"></a>新しいアプリケーションのスキャフォールディング

次に、下記を実行して、`myExpressApp` という新しい Express アプリケーションをスキャフォールディングします。

```bash
express myExpressApp --view pug --git
```

`--view pug --git` は、[pug](https://pugjs.org/api/getting-started.html) テンプレート エンジン (旧称 `jade`) を使用すること、また `.gitignore` ファイルを作成することをジェネレーターに伝えるパラメーターです。

アプリケーションの依存関係をすべてインストールするために、新しいフォルダーに移動して `npm install` を実行します。

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>アプリケーションの実行

次に、アプリケーションが動作することを確認します。 ターミナルから `npm start` コマンドを使用してアプリケーションを起動し、サーバーを起動します。

```bash
npm start
```

今度は、ブラウザーを開いて [http://localhost:3000](http://localhost:3000) に移動します。そこで、このような画面が表示されるはずです。

![Express アプリケーションの実行](./media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-website"></a>Web サイトのデプロイ

このセクションでは、VS Code と Azure App Service 拡張機能を使用して自分の Node.js Web サイトをデプロイします。 このクイックスタートで使用するのは最も基本的なデプロイ モデルであり、自分のアプリは zip 圧縮されて、Linux 上の Azure Web アプリにデプロイされます。

### <a name="deploy-using-azure-app-service"></a>Azure App Service を使用したデプロイ

まず、VS Code で自分のアプリケーション フォルダーを開きます。

```bash
code .
```

**Azure App Service** エクスプローラーで青色の上矢印アイコンをクリックして、自分のアプリを Azure にデプロイします。

![Web アプリへのデプロイ](./media/quickstart-nodejs/deploy.png)

> [!TIP]
> **コマンド パレット** (Ctrl + Shift + P) で「deploy to web app」と入力し、 **[Azure App Service: Deploy to Web App]** コマンドを実行してデプロイすることもできます。

1. 現在開いているディレクトリ (`myExpressApp`) を選択します。

2. **[Create new Web App]\(新しい Web アプリを作成する\)** を選択します。

3. 自分の Web アプリ用にグローバルに一意な名前を入力し、Enter キーを押します。 アプリ名に使用できる有効な文字は "a-z"、"0-9"、"-" です。

4. 該当する **Node.js バージョン**を選択します (LTS を推奨)。

    自分のアプリ用に作成されている Azure リソースが通知チャネルに表示されます。

ターゲット サーバーで `npm install` を実行するように構成を更新するよう求められたら、 **[はい]** をクリックします。 その後、アプリがデプロイされます。

![構成済みのデプロイ](./media/quickstart-nodejs/server-build.png)

デプロイが開始されると、以後デプロイのターゲットが自動的に同じ App Service Web アプリとなるように自分のワークスペースを更新するよう求められます。 自分の変更が適切なアプリにデプロイされるよう **[はい]** を選択してください。

![構成済みのデプロイ](./media/quickstart-nodejs/save-configuration.png)

> [!TIP]
> 必ず、PORT 環境変数 (`process.env.PORT`) に指定されたポートで自分のアプリケーションがリッスンするようにしてください。

### <a name="browse-the-website"></a>Web サイトの閲覧

デプロイが完了したら、プロンプトの **[Web サイトの参照]** をクリックして、自分が新しくデプロイした Web サイトを表示します。

### <a name="troubleshooting"></a>トラブルシューティング

**"このディレクトリまたはページを表示するアクセス許可がありません"** というエラーが表示された場合、おそらくアプリケーションが正常に起動できていません。 次のセクションに進んでログ出力を表示し、エラーを探して修正してください。 ご自身で解決できない場合は、以下の **[問題が発生しました]** ボタンをクリックしてお問い合わせください。 喜んでお手伝いします。

> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="updating-the-website"></a>Web サイトの更新

同じプロセスを使用し、新しいアプリを作成する代わりに既存のものを選択することで、このアプリに対する変更をデプロイできます。

## <a name="viewing-logs"></a>ログの表示

このセクションでは、稼働中の Web サイトのログ (またはその "末尾") を表示する方法について説明します。 サイトでの `console.log` の呼び出しはすべて、Visual Studio Code の出力ウィンドウに表示されます。

**Azure App Service** エクスプローラーでアプリを探して右クリックし、 **[ストリーミング ログの表示]** を選択します。

確認を求められたら、ログを有効にしてアプリケーションを再起動します。 アプリが再起動すると、ログ ストリームへの接続と共に VS Code の出力ウィンドウが開きます。

![ストリーミング ログの表示](./media/quickstart-nodejs/view-logs.png)

![ログの有効化と再起動](./media/quickstart-nodejs/enable-restart.png)

数秒後、ログストリーミング サービスに接続されていることを示すメッセージが表示されます。

```bash
Connecting to log-streaming service...
2017-12-21 17:33:51.428 INFO  - Container practical-mustache_2 for site practical-mustache initialized successfully.
2017-12-21 17:33:56.500 INFO  - Container logs
```

ブラウザーで数回、ページを最新の情報に更新してログ出力を確認します。

```bash
2017-12-21 17:35:17.774 INFO  - Container logs
2017-12-21T17:35:14.955412230Z GET / 304 141.798 ms - -
2017-12-21T17:35:15.248930479Z GET /stylesheets/style.css 304 3.180 ms - -
2017-12-21T17:35:15.378623115Z GET /favicon.ico 404 53.839 ms - 995
```

> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>次の手順

おめでとうございます。このクイックスタートを正常に完了できました。

次は、他の Azure 拡張機能もチェックしてみましょう。

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker ツール](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLI Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure リソース マネージャー ツール](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

または、[Node Pack for Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) 拡張機能パックをインストールして、これらすべてを入手しましょう。
