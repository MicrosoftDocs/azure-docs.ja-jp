---
title: Node.js アプリの構成
description: Azure App Service のネイティブ Windows インスタンス、または事前に作成した Linux コンテナーで Node.js アプリを構成する方法について学習します。 この記事では、最も一般的な構成タスクを紹介しています。
ms.custom: devx-track-js, devx-track-azurecli
ms.devlang: nodejs
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 6a6f782768db12c2ce75f5cf1e66100222f24446
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101095204"
---
# <a name="configure-a-nodejs-app-for-azure-app-service"></a>Azure App Service 向けの Node.js アプリを構成する

Node.js アプリは、必要なすべての NPM 依存関係と共にデプロイする必要があります。 App Service 展開エンジンは、[Git リポジトリ](deploy-local-git.md)をデプロイするとき、またはビルド自動化が有効になっている [Zip パッケージ](deploy-zip.md)をデプロイするときに、`npm install --production` を自動的に実行します。 ただし、[FTP/S](deploy-ftp.md) を使用してファイルをデプロイする場合、必要なパッケージを手動でアップロードする必要があります。

このガイドでは、App Service にデプロイする Node.js 開発者向けに主要な概念と手順を説明します。 Azure App Service を使用したことがない場合は、まず、[Node.js クイック スタート](quickstart-nodejs.md)と [MongoDB を使った Node.js のチュートリアル](tutorial-nodejs-mongodb-app.md)に従ってください。

## <a name="show-nodejs-version"></a>Node.js のバージョンを表示する

::: zone pivot="platform-windows"  

現在の Node.js バージョンを表示するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp config appsettings list --name <app-name> --resource-group <resource-group-name> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION'].value"
```

サポートされているすべての Node.js バージョンを表示するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp list-runtimes | grep node
```

::: zone-end

::: zone pivot="platform-linux"

現在の Node.js バージョンを表示するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

サポートされているすべての Node.js バージョンを表示するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

::: zone-end

## <a name="set-nodejs-version"></a>Node.js のバージョンを設定する

::: zone pivot="platform-windows"  

[サポートされている Node.js バージョン](#show-nodejs-version)にアプリを設定するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行して、`WEBSITE_NODE_DEFAULT_VERSION` をサポートされているバージョンに設定します。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_NODE_DEFAULT_VERSION="10.15"
```

この設定では、実行時と、App Service のビルド自動化中のパッケージの自動復元時の両方で使用する Node.js のバージョンを指定します。

> [!NOTE]
> プロジェクトの `package.json` で Node.js バージョンを設定する必要があります。 展開エンジンは、サポートされているすべての Node.js バージョンを含む別のプロセスで実行されます。

::: zone-end

::: zone pivot="platform-linux"

[サポートされているすべての Node.js バージョン](#show-nodejs-version)にアプリを設定するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

この設定では、実行時と、Kudu でのパッケージの自動復元中の両方で使用する Node.js のバージョンを指定します。

> [!NOTE]
> プロジェクトの `package.json` で Node.js バージョンを設定する必要があります。 デプロイ エンジンは、サポートされているすべての Node.js バージョンを含む別のコンテナーで実行します。

::: zone-end

## <a name="get-port-number"></a>ポート番号を取得する

Node.js アプリでは、受信要求を受信するために適切なポートがリッスンされる必要があります。

::: zone pivot="platform-windows"  

Windows の App Service では、[IISNode](https://github.com/Azure/iisnode) を使用して Node.js アプリがホストされるため、Node.js アプリでは `process.env.PORT` 変数で指定されたポートがリッスンされる必要があります。 次の例は、単純な簡易アプリで行う方法を示しています。

::: zone-end

::: zone pivot="platform-linux"  

App Service では、Node.js コンテナーに環境変数 `PORT` が設定され、そのポート番号で受信要求がコンテナーに転送されます。 要求を受信するには、アプリで `process.env.PORT` を使用して、そのポートがリッスンされる必要があります。 次の例は、単純な簡易アプリで行う方法を示しています。

::: zone-end

```javascript
const express = require('express')
const app = express()
const port = process.env.PORT || 3000

app.get('/', (req, res) => {
  res.send('Hello World!')
})

app.listen(port, () => {
  console.log(`Example app listening at http://localhost:${port}`)
})
```

::: zone pivot="platform-linux"

## <a name="customize-build-automation"></a>ビルドの自動化のカスタマイズ

ビルドの自動化を有効にして Git または zip パッケージを使用してアプリをデプロイする場合、App Service のビルドの自動化によって、次の手順が実行されます。

1. `PRE_BUILD_SCRIPT_PATH` によって指定された場合、カスタム スクリプトを実行します。
1. フラグを指定せずに `npm install` を実行します。これには、npm `preinstall` スクリプトと `postinstall` スクリプトが含まれ、`devDependencies` のインストールも行われます。
1. *package.json* 内でビルド スクリプトが指定されている場合、`npm run build` を実行します。
1. *package.json* 内で build:azure スクリプトが指定されている場合、`npm run build:azure` を実行します。
1. `POST_BUILD_SCRIPT_PATH` によって指定された場合、カスタム スクリプトを実行します。

> [!NOTE]
> [npm に関するドキュメント](https://docs.npmjs.com/misc/scripts)に説明されているように、`prebuild` および `postbuild` というスクリプトが指定された場合、それぞれ `build`の前と後に実行されます。 `preinstall` および `postinstall` はそれぞれ、`install` の前と後に実行されます。

`PRE_BUILD_COMMAND` および `POST_BUILD_COMMAND` は、既定では空の環境変数です。 ビルド前のコマンドを実行するには、`PRE_BUILD_COMMAND` を定義します。 ビルド後のコマンドを実行するには、`POST_BUILD_COMMAND` を定義します。

次の例では、一連のコマンドに対して 2 つの変数をコンマ区切りで指定しています。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

ビルドの自動化をカスタマイズするためのその他の環境変数については、「[Oryx の構成](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)」を参照してください。

Linux 上で App Service によって Node.js アプリが実行されビルドされる方法に関する詳細については、[Oryx ドキュメントの Node.js アプリが検出されビルドされる方法](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md)に関するページを参照してください。

## <a name="configure-nodejs-server"></a>Node.js サーバーを構成する

Node.js コンテナーには、製造工程マネージャーである [PM2](https://pm2.keymetrics.io/) が付属しています。 PM2、NPM、またはカスタム コマンドで開始するようにアプリを構成できます。

- [カスタム コマンドを実行する](#run-custom-command)
- [npm start を実行する](#run-npm-start)
- [PM2 で実行する](#run-with-pm2)

### <a name="run-custom-command"></a>カスタム コマンドを実行する

App Service は、*run.sh* のような実行可能ファイルなどのカスタム コマンドを使用してアプリを開始できます。たとえば、`npm run start:prod` を実行するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>npm start を実行する

`npm start` を使用してをアプリを開始するには、`start` スクリプトが *package.json* ファイル内にあることを確認するだけです。 次に例を示します。

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
- 次のいずれかの [PM2 ファイル](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file): *process.json* および *ecosystem.config.js*

次の拡張子を持つカスタム スタート ファイルを構成することもできます。

- *.js* ファイル
- *.json*、*. config.js*、*.yaml*、または *.yml* の拡張子を持つ [PM2 ファイル](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file)

カスタム スタート ファイルを追加するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>リモート デバッグ

> [!NOTE]
> リモート デバッグは、現在、プレビュー段階です。

[PM2 で実行](#run-with-pm2)するように構成した場合、*.config.js、*.yml、または *.yaml* を使用して実行したときを除き、[Visual Studio Code](https://code.visualstudio.com/) で Node.js アプリをリモートからデバッグできます。

ほとんどの場合、アプリ用に追加の構成は必要ありません。 アプリが *process.json* ファイル (既定またはカスタム) で実行されている場合、JSON ルートに `script` プロパティが必要です。 次に例を示します。

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

::: zone-end

## <a name="access-environment-variables"></a>環境変数へのアクセス

App Service では、アプリ コードの外部で[アプリ設定を指定](configure-common.md)できます。 その後、標準の Node.js パターンを使用して、それらにアクセスできます。 たとえば、`NODE_ENV` というアプリ設定にアクセスするには、次のコードを使用します。

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Grunt/Bower/Gulp を実行する

既定では、App Service のビルド自動化によって、Node.js アプリが Git またはビルド自動化が有効になっている Zip デプロイを介してデプロイされることが認識されると、`npm install --production` が実行されます。 アプリで、Grunt、Bower、Gulp など、一般的な自動化ツールのいずれかが必要な場合、それを実行する[カスタム デプロイ スクリプト](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)を提供する必要があります。

リポジトリでこれらのツールを実行できるようにするには、*package.json* での依存関係にこれらを追加する必要があります。 次に例を示します。

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

リポジトリのルートには現在、 *.deployment* と *deploy.sh* の 2 つの追加ファイルがあります。

*deploy.sh* を開き、次のような `Deployment` セクションを見つけます。

```bash
##################################################################################################################################
# Deployment
# ----------
```

このセクションは、`npm install --production` の実行で終わります。 `Deployment` セクションの *末尾* に必要なツールの実行に必要なコード セクションを追加します。

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

一般的な Web フレームワークでは、標準のアプリ パターンで `X-Forwarded-*` 情報にアクセスできます。 [Express](https://expressjs.com/) で、[trust proxy](https://expressjs.com/guide/behind-proxies.html) を使用できます。 次に例を示します。

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>診断ログにアクセスする

::: zone pivot="platform-windows"  

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end


::: zone pivot="platform-linux"

## <a name="monitor-with-application-insights"></a>Application Insights での監視

Application Insights を使用すると、コードを変更することなく、アプリケーションのパフォーマンス、例外、使用状況を監視できます。 App Insights エージェントをアタッチするには、ポータルで自分の Web アプリにアクセスし、 **[設定]** で **[Application Insights]** を選択してから、 **[Turn on Application Insights]\(Application Insights を有効にする\)** を選択します。 次に、既存の App Insights リソースを選択するか、新しいものを作成します。 最後に、下部にある **[適用]** を選択します。 PowerShell を使用して Web アプリをインストルメント化するには、[これらの手順](../azure-monitor/app/azure-web-apps.md?tabs=netcore#enabling-through-powershell)を参照してください。

このエージェントは、サーバー側の Node.js アプリケーションを監視します。 クライアント側の JavaScript を監視するには、[JavaScript SDK をプロジェクトに追加します](../azure-monitor/app/javascript.md)。 

詳細については、[Application Insights の拡張機能のリリース ノート](../azure-monitor/app/web-app-extension-release-notes.md)を参照してください。

::: zone-end

## <a name="troubleshooting"></a>トラブルシューティング

動作中の Node.js アプリが App Service で異なる動作をしたり、エラーが発生した場合は、次のことを試してください。

- [ログ ストリームにアクセス](#access-diagnostic-logs)します。
- 実稼働モードでローカルにアプリをテストします。 App Service では、実稼働モードで Node.js アプリが実行されるので、プロジェクトがローカルで実稼働モードで予想どおりに動作することを確認する必要があります。 次に例を示します。
    - *package.json* に応じて、実稼働モードに別々のパッケージ (`dependencies` と `devDependencies`) がインストールされる場合があります。
    - 特定の Web フレームワークでは、実稼働モードで静的ファイルを別にデプロイすることがあります。
    - 特定の Web フレームワークでは、実稼働モードで実行しているときにカスタム スタートアップ スクリプトを使用することがあります。
- 開発モードの App Service でアプリを実行します。 たとえば、[MEAN.js](https://meanjs.org/) で、[`NODE_ENV` アプリ設定を指定する](configure-common.md)ことによって、実行時に開発モードにアプリを設定できます。

::: zone pivot="platform-linux"

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:Node.js アプリと MongoDB](tutorial-nodejs-mongodb-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [App Service Linux の FAQ](faq-app-service-linux.md)

::: zone-end
