---
title: Node.js アプリを構成する - Azure App Service | Microsoft Docs
description: Azure App Service で動作するように Node.js アプリを構成する方法について説明する
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: 43dc76e6d1e1ec2a6167f1d3e3cc7b8780f843db
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551061"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>Azure App Service 向けの Linux Node.js アプリを構成する

Node.js アプリは、必要なすべての NPM 依存関係と共にデプロイする必要があります。 App Service デプロイ エンジン (Kudu) は、[Git リポジトリ](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)をデプロイするとき、またはビルド プロセスがオンになっている [Zip パッケージ](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)をデプロイするときに、`npm install --production` を実行します。 ただし、[FTP/S](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) を使用してファイルをデプロイする場合、必要なパッケージを手動でアップロードする必要があります。

このガイドでは、App Service のビルトイン Linux コンテナーを使用する Node.js 開発者向けに、主要な概念と手順を示します。 Azure App Service を使用したことがない場合は、まず、[Node.js クイック スタート](quickstart-nodejs.md)と [MongoDB を使った Node.js のチュートリアル](tutorial-nodejs-mongodb-app.md)に従ってください。

## <a name="show-nodejs-version"></a>Node.js のバージョンを表示する

現在の Node.js バージョンを表示するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

サポートされているすべての Node.js バージョンを表示するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>Node.js のバージョンを設定する

[サポートされているすべての Node.js バージョン](#show-nodejs-version)にアプリを設定するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

この設定では、実行時と、Kudu でのパッケージの自動復元中の両方で使用する Node.js のバージョンを指定します。

> [!NOTE]
> プロジェクトの `package.json` で Node.js バージョンを設定する必要があります。 デプロイ エンジンは、サポートされているすべての Node.js バージョンを含む別のコンテナーで実行します。

## <a name="configure-nodejs-server"></a>Node.js サーバーを構成する

Node.js コンテナーには、製造工程マネージャーである [PM2](http://pm2.keymetrics.io/) が付属しています。 PM2、NPM、またはカスタム コマンドで開始するようにアプリを構成できます。

- [カスタム コマンドを実行する](#run-custom-command)
- [npm start を実行する](#run-npm-start)
- [PM2 で実行する](#run-with-pm2)

### <a name="run-custom-command"></a>カスタム コマンドを実行する

App Service は、*run.sh* のような実行可能ファイルなどのカスタム コマンドを使用してアプリを開始できます。たとえば、`npm run start:prod` を実行するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>npm start を実行する

`npm start` を使用してをアプリを開始するには、`start` スクリプトが *package.json* ファイル内にあることを確認するだけです。 例: 

```json
{
  ...
  "scripts": {
    "start": "gulp",
    ...
  },
  ...
}
```

プロジェクトでカスタム *package.json* を使用するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>PM2 で実行する

コンテナーは、一般的な Node.js ファイルの 1 つがプロジェクトで見つかった場合、PM2 でアプリを自動的に開始します。

- *bin/www*
- *server.js*
- *app.js*
- *index.js*
- *hostingstart.js*
- 次のいずれかの [PM2 ファイル](http://pm2.keymetrics.io/docs/usage/application-declaration/#process-file): *process.json* および *ecosystem.config.js*

次の拡張子を持つカスタム スタート ファイルを構成することもできます。

- *.js* ファイル
- *.json*、*. config.js*、*.yaml*、または *.yml* の拡張子を持つ [PM2 ファイル](http://pm2.keymetrics.io/docs/usage/application-declaration/#process-file)

カスタム スタート ファイルを追加するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>リモート デバッグ

> [!NOTE]
> リモート デバッグは、現在、プレビュー段階です。

[PM2 で実行](#run-with-pm2)するように構成した場合、*.config.js、*.yml、または *.yaml* を使用して実行したときを除き、[Visual Studio Code](https://code.visualstudio.com/) で Node.js アプリをリモートからデバッグできます。

ほとんどの場合、アプリ用に追加の構成は必要ありません。 アプリが *process.json* ファイル (既定またはカスタム) で実行されている場合、JSON ルートに `script` プロパティが必要です。 例: 

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

リモート デバッグ用に Visual Studio Code を設定するには、[App Service 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)をインストールします。 拡張機能ページの指示に従い、Visual Studio Code で Azure にサインインします。

Azure エクスプローラーで、デバッグするアプリを見つけ、それを右クリックして、**[リモート デバッグを開始する]** を選択します。 **[はい]** をクリックしてアプリに対して有効にします。 App Service により、トンネル プロキシが開始され、デバッガがアタッチされます。 続いて、アプリに対して要求を行って、ブレーク ポイントで中断しているデバッガを確認できます。

デバッグが完了すると、**[切断]** を選択してデバッガを停止します。 求められたら、**[はい]** をクリックしてリモート デバッグを無効にする必要があります。 後で無効にするには、Azure エクスプローラーでもう一度アプリを右クリックし、**[リモート デバッグの無効化]** を選択します。

## <a name="access-environment-variables"></a>環境変数へのアクセス

App Service では、アプリ コードの外部で[アプリ設定を指定](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings)できます。 その後、標準の Node.js パターンを使用して、それらにアクセスできます。 たとえば、`NODE_ENV` というアプリ設定にアクセスするには、次のコードを使用します。

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Grunt/Bower/Gulp を実行する

既定では、Kudu は、Node.js アプリがデプロイされていることを認識すると、`npm install --production` を実行します。 アプリで、Grunt、Bower、Gulp など、一般的な自動化ツールのいずれかが必要な場合、それを実行する[カスタム デプロイ スクリプト](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)を提供する必要があります。

リポジトリでこれらのツールを実行できるようにするには、*package.json* での依存関係にこれらを追加する必要があります。 例: 

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

ローカル ターミナル ウィンドウから、ディレクトリをリポジトリのルートに変更し、次のコマンドを実行します。

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

リポジトリのルートには現在、*.deployment* と *deploy.sh* の 2 つの追加ファイルがあります。

*deploy.sh* を開き、次のような `Deployment` セクションを見つけます。

```bash
##################################################################################################################################
# Deployment
# ----------
```

このセクションは、`npm install --production` の実行で終わります。 `Deployment` セクションの*末尾*に必要なツールの実行に必要なコード セクションを追加します。

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

[MEAN.js サンプルでの例](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)を参照してください。ここでは、デプロイ スクリプトはカスタム `npm install` コマンドも実行します。

### <a name="bower"></a>Bower

このスニペットは `bower install` を実行します。

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

このスニペットは `gulp imagemin` を実行します。

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

このスニペットは `grunt` を実行します。

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="detect-https-session"></a>HTTPS セッションの検出

App Service では、[SSL 終了](https://wikipedia.org/wiki/TLS_termination_proxy)がネットワーク ロード バランサーで発生するため、すべての HTTPS リクエストは暗号化されていない HTTP リクエストとしてアプリに到達します。 ユーザー要求が暗号化されているかどうかをアプリ ロジックが確認する必要がある場合は、`X-Forwarded-Proto` ヘッダーを調べます。

一般的な Web フレームワークでは、標準のアプリ パターンで `X-Forwarded-*` 情報にアクセスできます。 [Express](https://expressjs.com/) で、[trust proxy](http://expressjs.com/guide/behind-proxies.html) を使用できます。 例: 

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>診断ログにアクセスする

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>ブラウザーで SSH セッションを開く

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>トラブルシューティング

動作中の Node.js アプリが App Service で異なる動作をしたり、エラーが発生した場合は、次のことを試してください。

- [ログ ストリームにアクセス](#access-diagnostic-logs)します。
- 実稼働モードでローカルにアプリをテストします。 App Service は、実稼働モードで Node.js アプリを実行するので、プロジェクトがローカルで実稼働モードで予想どおりに動作することを確認する必要があります。 例: 
    - *package.json* に応じて、実稼働モードに別々のパッケージ (`dependencies` と `devDependencies`) がインストールされる場合があります。
    - 特定の Web フレームワークでは、実稼働モードで静的ファイルを別にデプロイすることがあります。
    - 特定の Web フレームワークでは、実稼働モードで実行しているときにカスタム スタートアップ スクリプトを使用することがあります。
- 開発モードの App Service でアプリを実行します。 たとえば、[MEAN.js](http://meanjs.org/) で、[`NODE_ENV` アプリ設定を指定する](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)ことによって、実行時に開発モードにアプリを設定できます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [チュートリアル:Node.js アプリと MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [App Service Linux の FAQ](app-service-linux-faq.md)