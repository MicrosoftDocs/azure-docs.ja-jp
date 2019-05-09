---
title: Linux での Ruby Web アプリの作成 - Azure App Service | Microsoft Docs
description: App Service on Linux で Ruby on Rails アプリを作成する方法について説明します。
keywords: Azure App Service、Linux、OSS、Ruby、Rails
services: app-service
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 29126171a2d808153c7578d911e0725641ec39ff
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2019
ms.locfileid: "59545147"
---
# <a name="create-a-ruby-on-rails-app-in-app-service-on-linux"></a>App Service on Linux で Ruby on Rails アプリを作成する

[Azure App Service on Linux](app-service-linux-intro.md) は、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供します。 このクイックスタートでは、Web App on Linux として Azure にデプロイできる基本的な [Ruby on Rails](https://rubyonrails.org/) アプリケーションの作成方法を示します。

> [!NOTE]
> この時点で、Ruby 開発スタックは Ruby on Rails のみをサポートしています。 別のプラットフォーム (Sinatra など) を使用するか、[サポートされていない Ruby バージョン](app-service-linux-intro.md)を使用する場合は、[カスタム コンテナーで実行](quickstart-docker-go.md)する必要があります。

![Hello-world](./media/quickstart-ruby/hello-world-updated.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

* <a href="https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller" target="_blank">Ruby 2.3 以降のインストール</a>
* <a href="https://git-scm.com/" target="_blank">Git をインストールする</a>

## <a name="download-the-sample"></a>サンプルのダウンロード

ターミナル ウィンドウで、次のコマンドを実行して、サンプル アプリのリポジトリをローカル コンピューターに複製します。

```bash
git clone https://github.com/Azure-Samples/ruby-docs-hello-world
```

## <a name="run-the-application-locally"></a>ローカルでアプリケーションを実行する

アプリケーションをローカルで実行すると、アプリケーションを Azure にデプロイするとどう表示されるかを把握できます。 ターミナル ウィンドウを開き、`hello-world` ディレクトリに変更し、`rails server` コマンドを使用してサーバーを起動します。

最初の手順では、必要な gem をインストールします。 `Gemfile` がサンプルに含まれているため、インストールする gem を指定する必要はありません。 これには bundler を使用します。

```bash
bundle install
```

gem がインストールされたら、bundler を使用してアプリを起動します。

```bash
bundle exec rails server
```

Web ブラウザーで `http://localhost:3000` に移動して、ローカルでアプリケーションをテストします。

![構成された Hello World](./media/quickstart-ruby/hello-world-configured.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Web アプリを作成する

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

サイトを参照して、新たに作成された、組み込みイメージを使用する Web アプリを確認します。 _&lt;app_name>_ は、Web アプリの名前に置き換えます。

```bash
http://<app_name>.azurewebsites.net
```

新しい Web アプリは次のようになります。

![スプラッシュ ページ](./media/quickstart-ruby/splash-page.png)

## <a name="deploy-your-application"></a>アプリケーションをデプロイする

次のコマンドを実行して、ローカル アプリケーションを Azure Web サイトにデプロイします。

```bash
git remote add azure <Git deployment URL from above>
git add -A
git commit -m "Initial deployment commit"
git push azure master
```

リモート デプロイ操作が正しく報告されていることを確認します。 これらのコマンドは、次のテキストに似た出力を生成します。

```bash
remote: Using sass-rails 5.0.6
remote: Updating files in vendor/cache
remote: Bundle gems are installed into ./vendor/bundle
remote: Updating files in vendor/cache
remote: ~site/repository
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<your web app name>.scm.azurewebsites.net/<your web app name>.git
  579ccb....2ca5f31  master -> master
myuser@ubuntu1234:~workspace/<app name>$
```

デプロイが完了したら、次に示すように [`az webapp restart`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-restart) コマンドを使用して Web アプリを再起動してデプロイが有効になるようにします。

```azurecli-interactive
az webapp restart --name <app name> --resource-group myResourceGroup
```

サイトに移動して結果を確認します。

```bash
http://<app name>.azurewebsites.net
```

![更新後の Web アプリ](./media/quickstart-ruby/hello-world-updated.png)

> [!NOTE]
> アプリの再起動中にサイトを参照しようとすると、結果は HTTP 状態コード `Error 503 Server unavailable` になります。 完全に再起動するまで、数分かかる場合があります。
>

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [チュートリアル:Ruby on Rails と Postgres](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Ruby アプリを構成する](configure-language-ruby.md)
