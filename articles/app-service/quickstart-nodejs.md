---
title: クイック スタート:Node.js Web アプリを作成する
description: Azure App Service に、初めての Node.js の Hello World を数分でデプロイします。
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-nodejs-uiex
ms.openlocfilehash: 6c6f0543dcfbecd16ba4176272f928ffd0eb54de
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101735116"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Azure で Node.js Web アプリを作成する

::: zone pivot="platform-windows"  

Visual Studio Code を使用して Node.js または Express アプリをローカルに作成し、アプリをクラウドにデプロイすることによって、Azure App Service の使用を開始します。 App Service の Free レベルを使用するため、このクイックスタートを完了するのにコストはかかりません。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension)。
- <a href="https://git-scm.com/" target="_blank">Git をインストールする</a>
- [Node.js および npm](https://nodejs.org)。 `node --version` コマンドを実行して、Node.js がインストールされていることを確認します。
- [Visual Studio Code](https://code.visualstudio.com/)。
- Visual Studio Code 用の [Azure App Service 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)。

## <a name="clone-and-run-a-local-nodejs-application"></a>ローカル Node.js アプリケーションを複製して実行する

1. お使いのローカル コンピューターで、ターミナルを開き、サンプル リポジトリを複製します。

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. 新しいアプリ フォルダーに移動します。

    ```bash
    cd nodejs-docs-hello-world
    ```

1. アプリを起動してローカルでテストします。

    ```bash
    npm start
    ```
    
1. ブラウザーを開き、`http://localhost:1337` に移動します。 ブラウザーに "Hello World!" と表示されます。

1. ターミナルで **Ctrl** + **C** キーを押してサーバーを停止します。

> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-app-to-azure"></a>Azure にアプリケーションをデプロイする

このセクションでは、VS Code と Azure App Service 拡張機能を使用して自分の Node.js アプリを Azure にデプロイします。

1. ターミナルで、*nodejs-docs-hello-world* フォルダーにいることを確認し、次のコマンドを使用して Visual Studio Code を起動します。

    ```bash
    code .
    ```

1. VS Code のアクティビティ バーで Azure のロゴを選択し、**Azure App Service** エクスプローラーを表示します。 **[サインイン: Azure]** を選択して、指示に従います。 (エラーが発生した場合は、以下の「[Azure サインインのトラブルシューティング](#troubleshooting-azure-sign-in)」を参照してください)。サインインすると、エクスプローラーにご使用の Azure サブスクリプションの名前が表示されます。

    ![Azure へのサインイン](media/quickstart-nodejs/sign-in.png)

1. VS Code の **Azure App Service** エクスプローラーで青色の上矢印アイコンを選択して、自分のアプリを Azure にデプロイします。 (**コマンド パレット** (**Ctrl** + **Shift** + **P** キー) から同じコマンドを呼び出すこともできます。「Web アプリにデプロイ」と入力し、 **[Azure App Service: Web アプリにデプロイ]** を選択します)。

    :::image type="content" source="media/quickstart-nodejs/deploy.png" alt-text="選択されている青い矢印アイコンを示している、VS Code での Azure App Service のスクリーンショット。":::
        
1. *nodejs-docs-hello-world* フォルダーを選択します。

1. デプロイ先のオペレーティング システムに基づいて、作成オプションを選択します。

    - Linux: **[Create new Web App]\(新しい Web アプリの作成\)** を選択します
    - Windows: **[Create new Web App]\(新しい Web アプリの作成\)、[詳細設定]** の順に選択します

1. 自分の Web アプリ用にグローバルに一意な名前を入力し、**Enter** キーを押します。 名前は Azure 全体で一意である必要があり、英数字 ("A-Z"、"a-z"、"0-9") およびハイフン ("-") のみを使用する必要があります。

1. Linux を対象とする場合は、メッセージが表示されたら Node.js バージョンを選択します。 **LTS** バージョンが推奨されます。

1. Windows を対象とする場合は、追加のプロンプトに従います。
    1. **[Create a new resource group]\(新しいリソース グループの作成\)** を選択し、リソース グループの名前を入力します (たとえば、`AppServiceQS-rg`)。
    1. オペレーティング システムとして **[Windows]** を選択します。
    1. **[新しい App Service プランの作成]** を選択し、プランの名前 (たとえば、`AppServiceQS-plan`) を入力してから、価格レベルに **[F1 Free]** を選択します。
    1. Application Insights について確認するメッセージが表示されたら、 **[後で確認する]** を選択します。
    1. 近くのリージョン、またはアクセスするリソースの近くのリージョンを選択します。

1. すべてのプロンプトに応答すると、自分のアプリ用に作成される Azure リソースが VS Code の通知ポップアップに表示されます。

    Linux へのデプロイ時にターゲット Linux サーバーで `npm install` を実行するように構成を更新するよう求められたら、 **[はい]** を選択します。

    ![ターゲットの Linux サーバー上で構成を更新するように求めるメッセージ](media/quickstart-nodejs/server-build.png)

1. "**Always deploy the workspace "nodejs-docs-hello-world" to (app name) (ワークスペース "nodejs-docs-hello-world" を常に (アプリ名) にデプロイ)** " というメッセージが表示されたら、 **[はい]** を選択します。 **[はい]** を選択すると、後続のデプロイで同じ App Service Web アプリを自動的に対象とするように VS Code に指示されます。

1. Linux へのデプロイでは、デプロイが完了したら、プロンプトで **[Web サイトの参照]** を選択して、新しくデプロイした Web アプリを表示します。 ブラウザーに "Hello World!" と表示されます。

1. Windows にデプロイする場合は、最初に Web アプリの Node.js バージョン番号を設定する必要があります。

    1. VS Code で、新しいアプリ サービスのノードを展開し、 **[アプリケーション設定]** を右クリックして、 **[新しい設定の追加]** を選択します。

        ![アプリ設定の追加コマンド](media/quickstart-nodejs/add-setting.png)

    1. 設定キーとして「`WEBSITE_NODE_DEFAULT_VERSION`」と入力します。
    1. 設定値として「`10.15.2`」と入力します。
    1. アプリ サービスのノードを右クリックし、 **[再起動]** を選択します

        ![アプリ サービスの再起動コマンド](media/quickstart-nodejs/restart.png)

    1. アプリ サービスのノードをもう一度右クリックし、 **[Web サイトの参照]** を選択します。

> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="troubleshooting-azure-sign-in"></a>Azure サインインのトラブルシューティング

Azure にサインインするときに **"Cannot find subscription with name [subscription ID] (サブスクリプション名 [サブスクリプション ID] が見つかりません)"** というエラーが表示される場合、原因としては、プロキシの内側にいるために、Azure API に到達できないことが考えられます。 ご利用のターミナルで `export` を使用して、自分のプロキシ情報で `HTTP_PROXY` と `HTTPS_PROXY` の環境変数を構成してください。

```bash
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

環境変数を設定しても問題が解決しない場合は、上の **[問題が発生しました]** ボタンを選択してお問い合わせください。

### <a name="update-the-app"></a>アプリの更新

このアプリに対する変更をデプロイするには、VS Code で編集を行い、ファイルを保存した後、同じプロセスを使用して新しいアプリを作成する代わりに既存のものを選択するだけです。

## <a name="viewing-logs"></a>ログの表示

アプリからのログ出力 (`console.log` への呼び出し) は、VS Code の出力ウィンドウで直接表示できます。

1. **Azure App Service** エクスプローラーで、アプリ ノードを右クリックし、 **[Start Streaming Logs]\(ログのストリーム配信を開始する\)** を選択します。

    ![[Start Streaming Logs]\(ログのストリーム配信を開始する\)](media/quickstart-nodejs/view-logs.png)

1. 確認を求められたら、ログを有効にしてアプリケーションを再起動します。 アプリが再起動すると、ログ ストリームへの接続と共に VS Code の出力ウィンドウが開きます。 

    :::image type="content" source="media/quickstart-nodejs/enable-restart.png" alt-text="[はい] ボタンが選択されている、ログを有効にし、アプリケーションを再起動するための Visual Studio Code プロンプトのスクリーンショット。":::

1. 数秒後、ログストリーミング サービスに接続されていることを示すメッセージが出力ウィンドウに表示されます。 ブラウザーでページを更新することにより、より多くの出力アクティビティを生成できます。

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).
    </pre>

> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>次のステップ

おめでとうございます。このクイックスタートを正常に完了できました。

> [!div class="nextstepaction"]
> [チュートリアル:Node.js アプリと MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Node.js アプリの構成](configure-language-nodejs.md)

他の Azure 拡張機能もチェックしてみましょう。

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker ツール](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLI Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

または、[Node Pack for Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) 拡張機能パックをインストールして、これらすべてを入手しましょう。
::: zone-end

::: zone pivot="platform-linux"  
## <a name="prerequisites"></a>前提条件

Azure アカウントをお持ちでない場合は、無料アカウントに[今すぐサインアップ](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension)してください。200 ドルの Azure クレジットを使用してさまざまな組み合わせのサービスをお試しいただけます。

[Node.js と npm](https://nodejs.org/en/download) (Node.js のパッケージ マネージャー) と共に [Visual Studio Code](https://code.visualstudio.com/) がインストールされている必要があります。

また、[Azure App Service 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)もインストールする必要があります。これにより、Azure PaaS (サービスとしてのプラットフォーム) 上で Linux Web アプリを作成、管理、デプロイすることができます。

### <a name="sign-in"></a>サインイン

拡張機能のインストール後、自分の Azure アカウントにログインします。 アクティビティ バーで Azure のロゴを選択し、**Azure App Service** エクスプローラーを表示します。 **[サインイン: Azure]** を選択して、指示に従います。

![Azure にサインイン](./media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>トラブルシューティング

**"サブスクリプション名 <サブスクリプション ID> が見つかりません"** というエラーが表示された場合、原因としては、プロキシの内側にいるために、Azure API に到達できないことが考えられます。 ご利用のターミナルで `export` を使用して、自分のプロキシ情報で `HTTP_PROXY` と `HTTPS_PROXY` の環境変数を構成してください。

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

環境変数を設定しても問題が解決しない場合は、下の **[問題が発生しました]** ボタンを選択してお問い合わせください。

### <a name="prerequisite-check"></a>前提条件のチェック

続行する前に、すべての前提条件がインストールされ、構成されていることを確認してください。

VS Code では、ステータス バーに自分の Azure メール アドレスが、**Azure App Service** エクスプローラーに自分のサブスクリプションが表示されます。

> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>Node.js アプリケーションの作成

次に、クラウドにデプロイできる Node.js アプリケーションを作成します。 このクイックスタートでは、アプリケーション ジェネレーターを使用して、アプリケーションをターミナルからすばやくスキャフォールディングします。

> [!TIP]
> [Node.js チュートリアル](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial)を既に完了している場合は、「[Azure へのデプロイ](#deploy-to-azure)」に進んでかまいません。

### <a name="scaffold-a-new-application-with-the-express-generator"></a>Express ジェネレーターを使用した新しいアプリケーションのスキャフォールディング

[Express](https://www.expressjs.com) は、Node.js アプリケーションの構築と実行に広く使われているフレームワークです。 [Express ジェネレーター](https://expressjs.com/en/starter/generator.html) ツールを使用して新しい Express アプリケーションをスキャフォールディング (作成) できます。 Express ジェネレーターは npm モジュールとして提供されており、npm コマンドライン ツール (`npx`) を使用して直接 (インストールなしで) 実行できます。

```bash
npx express-generator myExpressApp --view pug --git
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

今度は、ブラウザーを開き、`http://localhost:3000` に移動します。次のような画面が表示されます。

![Express アプリケーションの実行](./media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-to-azure"></a>Deploy to Azure (Azure へのデプロイ)

このセクションでは、VS Code と Azure App Service 拡張機能を使用して自分の Node.js アプリをデプロイします。 このクイックスタートで使用するのは最も基本的なデプロイ モデルであり、自分のアプリは zip 圧縮されて、Linux 上の Azure Web アプリにデプロイされます。

### <a name="deploy-using-azure-app-service"></a>Azure App Service を使用したデプロイ

まず、VS Code で自分のアプリケーション フォルダーを開きます。

```bash
code .
```

**Azure App Service** エクスプローラーで青色の上矢印アイコンを選択して、自分のアプリを Azure にデプロイします。

:::image type="content" source="./media/quickstart-nodejs/deploy.png" alt-text="デプロイの青い矢印が選択されている、Visual Studio Code の Azure App Service メニューのスクリーンショット。":::

> [!TIP]
> **コマンド パレット** (Ctrl + Shift + P) からデプロイすることもできます。「deploy to web app」と入力し、**Azure App Service: Deploy to Web App** コマンドを実行します。

1. 現在開いているディレクトリ (`myExpressApp`) を選択します。

1. **[Create new Web App]\(新しい Web アプリの作成\)** を選択します。既定のデプロイ先は App Service on Linux になります。

1. 自分の Web アプリ用にグローバルに一意な名前を入力し、Enter キーを押します。 アプリ名に使用できる有効な文字は "a-z"、"0-9"、"-" です。

1. 該当する **Node.js バージョン** を選択します (LTS を推奨)。

    自分のアプリ用に作成されている Azure リソースが通知チャネルに表示されます。

1. ターゲット サーバーで `npm install` を実行するように構成を更新するよう求められたら、 **[はい]** を選択します。 その後、アプリがデプロイされます。

    :::image type="content" source="./media/quickstart-nodejs/server-build.png" alt-text="[はい] ボタンが選択されている、ターゲット サーバー上の構成を更新するためのプロンプトのスクリーンショット。":::

1. デプロイが開始されると、以後デプロイのターゲットが自動的に同じ App Service Web アプリとなるように自分のワークスペースを更新するよう求められます。 自分の変更が適切なアプリにデプロイされるよう **[はい]** を選択してください。

    :::image type="content" source="./media/quickstart-nodejs/save-configuration.png" alt-text="[はい] ボタンが選択されている、ワークスペースを更新するためのプロンプトのスクリーンショット。":::

> [!TIP]
> 必ず、PORT 環境変数 (`process.env.PORT`) に指定されたポートで自分のアプリケーションがリッスンするようにしてください。

### <a name="browse-the-app-in-azure"></a>Azure でアプリを参照する

デプロイが完了したら、プロンプトで **[Web サイトの参照]** を選択して、新しくデプロイした Web アプリを表示します。

### <a name="troubleshooting"></a>トラブルシューティング

**"このディレクトリまたはページを表示するアクセス許可がありません"** というエラーが表示された場合、おそらくアプリケーションが正常に起動できていません。 次のセクションに進んでログ出力を表示し、エラーを探して修正してください。 ご自身で解決できない場合は、以下の **[問題が発生しました]** ボタンを選択してお問い合わせください。 喜んでお手伝いします。

> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="update-the-app"></a>アプリの更新

同じプロセスを使用し、新しいアプリを作成する代わりに既存のものを選択することで、このアプリに対する変更をデプロイできます。

## <a name="viewing-logs"></a>ログの表示

このセクションでは、実行中の App Service アプリのログを表示 (または "tail") する方法について説明します。 アプリでの `console.log` の呼び出しはすべて、Visual Studio Code の出力ウィンドウに表示されます。

**Azure App Service** エクスプローラーでアプリを探して右クリックし、 **[ストリーミング ログの表示]** を選択します。

ログ ストリームへの接続と共に VS Code の出力ウィンドウが開きます。

![ストリーミング ログの表示](./media/quickstart-nodejs/view-logs.png)

:::image type="content" source="./media/quickstart-nodejs/enable-restart.png" alt-text="[はい] ボタンが選択されている、ファイルのログを有効にし、Web アプリを再起動するための VS Code プロンプトのスクリーンショット。":::

数秒後、ログストリーミング サービスに接続されていることを示すメッセージが表示されます。 数回ページを更新して、さらにアクティビティを表示します。

<pre>
2019-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
2019-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
2019-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
2019-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
</pre>

> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>次のステップ

おめでとうございます。このクイックスタートを正常に完了できました。

次は、他の Azure 拡張機能もチェックしてみましょう。

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker ツール](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLI Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

または、[Node Pack for Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) 拡張機能パックをインストールして、これらすべてを入手しましょう。


::: zone-end
