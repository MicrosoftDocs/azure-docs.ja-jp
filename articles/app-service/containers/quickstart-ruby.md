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
ms.date: 07/11/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: f9f142543140be3348bf7cd94894cc9e88278368
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849476"
---
# <a name="create-a-ruby-on-rails-app-in-app-service-on-linux"></a>App Service on Linux で Ruby on Rails アプリを作成する

[App Service on Linux](app-service-linux-intro.md) は、Linux オペレーティング システムを使用する、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供します。 このチュートリアルでは、[Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) を使用して Azure App Service on Linux に Ruby on Rails アプリをデプロイする方法を示します。

> [!NOTE]
> この時点で、Ruby 開発スタックは Ruby on Rails のみをサポートしています。 別のプラットフォーム (Sinatra など) を使用するか、[サポートされていない Ruby バージョン](app-service-linux-intro.md)を使用する場合は、[カスタム コンテナーで実行](quickstart-docker-go.md)する必要があります。

![Hello-world](./media/quickstart-ruby/hello-world-configured.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

* <a href="https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller" target="_blank">Ruby 2.6 以降をインストールする</a>
* <a href="https://git-scm.com/" target="_blank">Git をインストールする</a>

## <a name="download-the-sample"></a>サンプルのダウンロード

ターミナル ウィンドウで、次のコマンドを実行して、サンプル アプリのリポジトリをローカル コンピューターに複製します。

```bash
git clone https://github.com/Azure-Samples/ruby-docs-hello-world
```

## <a name="run-the-application-locally"></a>ローカルでアプリケーションを実行する

アプリケーションをローカルで実行すると、アプリケーションを Azure にデプロイするとどう表示されるかを把握できます。 ターミナル ウィンドウを開き、`hello-world` ディレクトリに変更し、`rails server` コマンドを使用してサーバーを起動します。

最初の手順では、必要な gem をインストールします。 このサンプルには `Gemfile` が含まれているので、次のコマンドを実行するだけです。

```bash
bundle install
```

gem がインストールされたら、bundler を使用してアプリを起動します。

```bash
bundle exec rails server
```

Web ブラウザーで `http://localhost:3000` に移動して、ローカルでアプリケーションをテストします。

![構成された Hello World](./media/quickstart-ruby/hello-world-updated.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Web アプリを作成する

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

アプリを参照して、新たに作成された、組み込みイメージを使用する Web アプリを確認します。 _&lt;app_name>_ は、Web アプリの名前に置き換えます。

```bash
http://<app_name>.azurewebsites.net
```

新しい Web アプリは次のようになります。

![スプラッシュ ページ](./media/quickstart-ruby/splash-page.png)

## <a name="deploy-your-application"></a>アプリケーションをデプロイする

次のコマンドを実行して、ローカル アプリケーションを Azure Web アプリにデプロイします。

```bash
git remote add azure <Git deployment URL from above>
git push azure master
```

リモート デプロイ操作が正しく報告されていることを確認します。 これらのコマンドは、次のテキストに似た出力を生成します。

```bash
remote: Using turbolinks 5.2.0
remote: Using uglifier 4.1.20
remote: Using web-console 3.7.0
remote: Bundle complete! 18 Gemfile dependencies, 78 gems now installed.
remote: Bundled gems are installed into `/tmp/bundle`
remote: Zipping up bundle contents
remote: .......
remote: ~/site/repository
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app-name>.scm.azurewebsites.net/<app-name>.git
   a6e73a2..ae34be9  master -> master
```

デプロイが完了したら、Web アプリが再起動するまで約 10 秒待ってから、Web アプリに移動して結果を確認します。

```bash
http://<app-name>.azurewebsites.net
```

![更新後の Web アプリ](./media/quickstart-ruby/hello-world-configured.png)

> [!NOTE]
> アプリの再起動中に、ブラウザーまたは `Error 503 Server unavailable` の既定のページで、HTTP 状態コード `Hey, Ruby developers!` が表示される場合があります。 アプリが完全に再起動するまで、数分かかる場合があります。
>

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [チュートリアル:Ruby on Rails と Postgres](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Ruby アプリを構成する](configure-language-ruby.md)
