---
title: "Azure App Service 用の Node.js Web アプリの開発 | Microsoft Docs"
description: "Node.js アプリケーションを Azure App Service の Web アプリにデプロイする方法を説明します。"
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: fb2b90c8-02b6-4700-929b-5de9a35d67cc
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: get-started-article
ms.date: 12/16/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: 3405fa7bf960ff1ba4b9f789e5e89f3f75fe96d4
ms.lasthandoff: 03/01/2017


---
# <a name="develop-nodejs-web-apps-for-azure-app-service"></a>Azure App Service 用の Node.js Web アプリの開発
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

このチュートリアルでは、簡単な [Node.js] アプリケーションを作成し、cmd.exe や bash などのコマンド ライン環境を使用して [Azure App Service] にデプロイする方法について説明します。 このチュートリアルの手順は、Node.js を実行できる任意のオペレーティング システムで使用できます。

[!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

<a name="prereq"></a>

## <a name="cli-versions-to-complete-the-task"></a>タスクを完了するための CLI バージョン

次のいずれかの CLI バージョンを使用してタスクを完了できます。

- [Azure CLI 1.0](app-service-web-nodejs-get-started-cli-nodejs.md) - クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用の CLI
- [Azure CLI 2.0](app-service-web-nodejs-get-started.md) - Resource Manager デプロイ モデル用の次世代 CLI

## <a name="prerequisites"></a>前提条件
* [Node.js]
* [Bower]
* [Yeoman]
* [Git]
* [Azure CLI 2.0 プレビュー](/cli/azure/install-az-cli2)
* Microsoft Azure アカウント。 アカウントを持っていない場合は、[無料試用版にサインアップ]するか [Visual Studio サブスクライバー特典を有効]にしてください。

> [!NOTE]
> Azure アカウントがなくても、[App Service を試用](https://azure.microsoft.com/try/app-service/)できます。 スターター アプリを作成し、最大&1; 時間使用できます。クレジット カードも契約も不要です。
> 
> 

## <a name="create-and-configure-a-simple-nodejs-app-for-azure"></a>Azure の単純な Node.js アプリの作成と構成
1. 任意のコマンド ライン ターミナルを開いて、 [Express generator for Yeoman]をインストールします。
   
        npm install -g generator-express

2. `CD` で作業ディレクトリに移動し、次の構文を使用して、express アプリを生成します。
   
        yo express
   
    プロンプトで次のオプションを選択します。  
   
    `? Would you like to create a new directory for your project?` **Yes**  
    `? Enter directory name` **{appname}**  
    `? Select a version to install:` **MVC**  
    `? Select a view engine to use:` **Jade**  
    `? Select a css preprocessor to use (Sass Requires Ruby):` **None**  
    `? Select a database to use:` **None**  
    `? Select a build tool to use:` **Grunt**

3. `CD` で新しいアプリのルート ディレクトリに移動し、アプリを起動して開発環境で動作することを確認します。
   
        npm start
   
    ブラウザーで <http://localhost:3000> に移動し、Express のホーム ページが表示されることを確認します。 アプリが適切に実行されることを確認したら、 `Ctrl-C` を使用してアプリを停止します。

6. アプリケーションのルートから ./config/config.js ファイルを開き、運用環境のポートを `process.env.port` に変更します。`config` オブジェクトの `production` プロパティは次の例のようになります。
   
        production: {
            root: rootPath,
            app: {
                name: 'express1'
            },
            port: process.env.port,
        }
   
    > [!NOTE] 
    > 既定では、Azure App Service は、`production` 環境変数 (`process.env.NODE_ENV="production"`) を使用して Node.js アプリケーションを実行します。
    > そのため、ここでの構成により、Azure の Node.js アプリは iisnode がリッスンする既定のポートで Web 要求に応答できるようになります。
    >
    >

7. ./package.json を開き、[必要な Node.js バージョンを指定する](#version)ために `engines` プロパティを追加します。
   
        "engines": {
            "node": "6.9.1"
        }, 

8. 変更内容を保存し、アプリケーションのルートにある Git リポジトリを初期化して、コードをコミットします。
   
        git add .
        git add -f config
        git commit -m "{your commit message}"

## <a name="deploy-your-nodejs-app-to-azure"></a>Azure への Node.js アプリのデプロイ

1. Azure にログインします ([Azure CLI 2.0 プレビュー](#prereq)が必要です)。
   
        az login
   
    画面の指示に従い、Azure サブスクリプションのある Microsoft アカウントを使用してブラウザーでログイン操作を進めます。

3. App Service のデプロイ ユーザーを設定します。 後で、これらの資格情報を使用してコードをデプロイします。
   
        az appservice web deployment user set --user-name <username> --password <password>

3. 新しい[リソース グループ](../azure-resource-manager/resource-group-overview.md)を作成します。 この node.js チュートリアルでは、実際にその内容を把握している必要はありません。

        az group create --location "<location>" --name my-nodejs-app-group

    `<location>` に使用できる値を確認するには、CLI コマンド `az appservice list-locations` を使用してください。

3. 新しい "Free" [App Service プラン](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)を作成します。 この node.js チュートリアルでは、このプランの Web アプリに対しては課金されないことを把握しておくだけでかまいません。

        az appservice plan create --name my-nodejs-appservice-plan --resource-group my-nodejs-app-group --sku FREE

4. `<app_name>` に一意の名前を指定して新しい Web アプリを作成します。

        az appservice web create --name <app_name> --resource-group my-nodejs-app-group --plan my-nodejs-appservice-plan

5. 次のコマンドを使用して、新しい Web アプリのローカル Git デプロイを構成します。

        az appservice web source-control config-local-git --name <app_name> --resource-group my-nodejs-app-group

    次のような JSON 出力が表示されます。これは、リモート Git リポジトリが構成されていることを意味します。

        {
        "url": "https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git"
        }

6. この URL を、ローカル リポジトリの Git リモートとして JSON に追加します (Git リモートは、わかりやすくするために `azure` という名前にしています)。

        git remote add azure https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git
   
7. サンプル コードを `azure` Git リモートにデプロイします。 入力を求められたら、前に構成したデプロイ資格情報を使用します。

        git push azure master
   
    Express ジェネレーターによって .gitignore ファイルがあらかじめ提供されているため、`git push` が node_modules/ ディレクトリのアップロードを試みることによる帯域幅の消費は発生しません。

9. 最後に、ブラウザーでライブ Azure アプリを起動します。
   
        az appservice web browse --name <app_name> --resource-group my-nodejs-app-group
   
    これで対象の Node.js Web アプリが Azure App Service でライブ実行されていることを確認できます。
   
    ![Example of browsing to the deployed application.][deployed-express-app]

## <a name="update-your-nodejs-web-app"></a>Node.js Web アプリの更新
App Service で実行されている Node.js Web アプリを更新するには、最初にデプロイしたときと同様に、`git add`、`git commit`、`git push` を実行します。

## <a name="how-app-service-deploys-your-nodejs-app"></a>App Service における Node.js アプリのデプロイ
Azure App Service では、Node.js アプリを実行するために [iisnode] が使用されます。 Node.js アプリの開発とデプロイは、Azure CLI 2.0 プレビューと Kudu エンジン (Git デプロイ) を組み合わせることでコマンド ラインから効率的に行うことができます。 

* iisnode.yml ファイルをルート ディレクトリに作成し、それを使用して iisnode のプロパティをカスタマイズすることができます。 構成可能なすべての設定に関するドキュメントは、[こちら](https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/iisnode.yml)にあります。
* `git push azure master` では、次のデプロイ タスクが Kudu によって自動化されます。
  
  * package.json がリポジトリのルートに存在する場合、`npm install --production` を実行します。
  * package.json 内の起動スクリプト (server.js、app.js など) を参照する iisnode 用の Web.config を生成します。
  * Node-Inspector でアプリをデバッグできるよう Web.config をカスタマイズします。

## <a name="use-a-nodejs-framework"></a>Node.js フレームワークの使用
[Sails.js][SAILSJS] や [MEAN.js][MEANJS] などの広く使われている Node.js フレームワークを使用して開発したアプリは、App Service にデプロイすることができます。 広く使われている Node.js フレームワークには、それぞれ独自のくせがあり、パッケージの依存関係も絶えず更新されます。 しかし App Service では、stdout と stderr のログが利用できるようになっているため、アプリで起こっていることを正確に把握し、それに応じた変更を加えることができます。 詳細については、「 [iisnode から stdout ログと stderr ログを取得する](#iisnodelog)」を参照してください。

以下のチュートリアルには、App Service における特定のフレームワークの使用方法が解説されています。

* [Deploy a Sails.js web app to Azure App Service (Sails.js Web アプリを Azure App Service にデプロイする)]
* [Azure App Service での Socket.IO を使用する Node.js チャット アプリケーションの構築]
* [Azure App Service Web Apps で io.js を使用する方法]

<a name="version"></a>

## <a name="use-a-specific-nodejs-engine"></a>特定の Node.js エンジンの使用
一般的なワークフローでは、通常 package.json で行うように、特定の Node.js エンジンの使用を App Service に指示します。
次に例を示します。

    "engines": {
        "node": "6.9.1"
    }, 

Kudu デプロイ エンジンは、使用する Node.js エンジンを次の順序で決定します。

* まず iisnode.yml を参照し、`nodeProcessCommandLine` が指定されているかどうかを確認します。 指定されている場合はそれを使用します。
* 次に package.json を参照し、`engines` オブジェクトに `"node": "..."` が指定されているかどうかを確認します。 指定されている場合はそれを使用します。
* 特に指定されていない場合、既定の Node.js バージョンを選択します。

Azure App Service でサポートされているすべての Node.js/NPM バージョンの更新された一覧については、アプリに応じて次の URL にアクセスします。

    https://<app_name>.scm.azurewebsites.net/api/diagnostics/runtime

> [!NOTE]
> 必要な Node.js エンジンを明示的に定義することをお勧めします。 既定の Node.js バージョンは変更できます。既定の Node.js バージョンがアプリに適していないことが理由で Azure Web アプリでエラーが発生する場合があります。
> 
> 

<a name="iisnodelog"></a>

## <a name="get-stdout-and-stderr-logs-from-iisnode"></a>iisnode から stdout ログと stderr ログを取得する
iisnode ログを読み取るには、次の手順を実行します。

> [!NOTE]
> 次の手順を完了した後も、エラーが発生するまでログ ファイルが生成されない場合があります。
> 
> 

1. Azure CLI 2.0 プレビューに用意されている iisnode.yml ファイルを開きます。
2. 次の&2; つのパラメーターを設定します。 
   
        loggingEnabled: true
        logDirectory: iisnode
   
    この&2; つのパラメーターを組み合わせることで、stdout と stderror の出力先が D:\home\site\wwwroot\**iisnode** ディレクトリとなるよう、App Service 内の iisnode に伝えられます。
3. 次の Git コマンドで変更内容を保存し、Azure にプッシュします。
   
        git add .
        git commit -m "{your commit message}"
        git push azure master
   
    これで iisnode の構成は完了です。 以下、これらのログにアクセスする方法を説明します。
4. ブラウザーでアプリの Kudu デバッグ コンソールにアクセスします。URL は次のとおりです。
   
        https://{appname}.scm.azurewebsites.net/DebugConsole 
   
    この URL は、Web アプリの URL とは異なるもので、"*.scm.*"  が DNS 名に追加されています。 この追加部分を URL から省くと、404 エラーが表示されます。
5. D:\home\site\wwwroot\iisnode に移動します。
   
    ![Navigating to the location of the iisnode log files.][iislog-kudu-console-find]
6. 確認するログの **[編集]** アイコンをクリックします。 必要に応じて **[ダウンロード]** または **[削除]** をクリックすることもできます。
   
    ![Opening an iisnode log file.][iislog-kudu-console-open]
   
    これでログを参照できるようになりました。ログを基に App Service のデプロイをデバッグすることができます。
   
    ![Examining an iisnode log file.][iislog-kudu-console-read]

## <a name="debug-your-app-with-node-inspector"></a>Node-Inspector によるアプリのデバッグ
Node.js アプリのデバッグに Node-Inspector を使用している場合、実稼働の App Service アプリに Node-Inspector を使用できます。 Node-Inspector は、App Service の iisnode のインストールでプレインストールされます。 また、デプロイに Git を使用している場合、Kudu によって自動生成された Web.config には既に、Node-Inspector を有効にするうえで必要な構成がすべて含まれています。

Node-Inspector を有効にするには、次の手順を実行します。

1. リポジトリのルートにある iisnode.yml を開いて、次のパラメーターを指定します。 
   
        debuggingEnabled: true
        debuggerExtensionDll: iisnode-inspector.dll
2. 次の Git コマンドで変更内容を保存し、Azure にプッシュします。
   
        git add .
        git commit -m "{your commit message}"
        git push azure master
3. 後は、package.json 内の起動スクリプトで指定されたアプリの起動ファイルに移動するだけです。URL の最後に /debug を付けてください。 たとえば、次のように入力します。
   
        http://{appname}.azurewebsites.net/server.js/debug
   
    または、
   
        http://{appname}.azurewebsites.net/app.js/debug

## <a name="more-resources"></a>その他のリソース
* [Azure アプリケーションでの Node.js のバージョンの指定](../nodejs-specify-node-version-azure-apps.md)
* [Azure での Node.js アプリケーションのベスト プラクティスとトラブルシューティング ガイド](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)
* [Azure App Service で Node.js Web アプリをデバッグする方法](web-sites-nodejs-debug.md)
* [Azure アプリケーションでの Node.js モジュールの使用](../nodejs-use-node-modules-azure-apps.md)
* [Azure App Service Web Apps: Node.js](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx)
* [Node.js デベロッパー センター](/develop/nodejs/)
* [Azure App Service での Web アプリの使用](app-service-web-get-started.md)
* [優れた Kudu デバッグ コンソールの詳細]

<!-- URL List -->

[Azure App Service]: ../app-service/app-service-value-prop-what-is.md
[Visual Studio サブスクライバー特典を有効]: http://go.microsoft.com/fwlink/?LinkId=623901
[Bower]: http://bower.io/
[Azure App Service での Socket.IO を使用する Node.js チャット アプリケーションの構築]: ./web-sites-nodejs-chat-app-socketio.md
[Deploy a Sails.js web app to Azure App Service (Sails.js Web アプリを Azure App Service にデプロイする)]: ./app-service-web-nodejs-sails.md
[優れた Kudu デバッグ コンソールの詳細]: /documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[Express generator for Yeoman]: https://github.com/petecoop/generator-express
[Git]: http://www.git-scm.com/downloads
[Azure App Service Web Apps で io.js を使用する方法]: ./web-sites-nodejs-iojs.md
[iisnode]: https://github.com/tjanczuk/iisnode/wiki
[MEANJS]: http://meanjs.org/
[Node.js]: http://nodejs.org
[SAILSJS]: http://sailsjs.org/
[無料試用版にサインアップ]: http://go.microsoft.com/fwlink/?LinkId=623901
[web app]: ./app-service-web-overview.md
[Yeoman]: http://yeoman.io/

<!-- IMG List -->

[deployed-express-app]: ./media/app-service-web-nodejs-get-started/deployed-express-app.png
[iislog-kudu-console-find]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-navigate.png
[iislog-kudu-console-open]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-open.png
[iislog-kudu-console-read]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-read.png

