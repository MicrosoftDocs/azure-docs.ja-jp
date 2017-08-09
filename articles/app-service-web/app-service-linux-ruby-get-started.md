---
title: "Azure App Service Web Apps on Linux で Ruby アプリを作成する | Microsoft Docs"
description: "Azure App Service Web Apps on Linux で Ruby アプリを作成する方法について説明します。"
keywords: Azure App Service, Linux, OSS
services: app-service
documentationcenter: 
author: wesmc7777
manager: erikre
editor: 
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/05/2017
ms.author: wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c63bf790a50b894ea1732ae3142e6c1ff9ccbd4c
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---
# <a name="create-a-ruby-app-with-azure-web-app-on-linux---preview"></a>Azure App Service Web Apps on Linux で Ruby アプリを作成する - プレビュー

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

## <a name="overview"></a>概要

このチュートリアルでは、基本的な Ruby on Rails アプリケーションをローカルで作成し、Azure Web App on Linux にデプロイする方法を示します。

## <a name="prerequisites"></a>前提条件

* 開発用コンピューターにインストールされた [Ruby 2.3.3 以降](https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller)
* 開発用コンピューターにインストールされた [Git](https://git-scm.com/downloads)
* [有効な Azure サブスクリプション](https://azure.microsoft.com/pricing/free-trial/)
* このチュートリアルは、Ubuntu 環境のコンテキストで記述されています。 すべてのシステム コマンドは、bash 固有です。


## <a name="create-a-new-local-rails-application"></a>新しいローカルの Rails アプリケーションを作成する

1. 新しいアプリケーション用のディレクトリを作成し、そのディレクトリに変更します。

        mkdir ~/workspace
        cd ~/workspace

2. Ruby を初期化し、`ruby -v` コマンド使用してバージョンを確認します。

    ![Ruby の初期化](./media/app-service-linux-ruby-get-started/ruby-version.png)

3. `gem install rails` コマンドを使用して Rails をインストールします。

    ![Rails のインストール](./media/app-service-linux-ruby-get-started/install-rails.png)

4. 次のコマンドを使用して、**hello world** という名前の Rails アプリケーションを作成します。

    Rails 5.1.0 以降を使用している場合は、次のコマンドに示すように `--skip-yarn` オプションを指定します。

        rails new hello-world --skip-yarn

    5.1.0 より前のバージョンの Rails では、次のコマンドを使用します。

        rails new hello-world 

    ![新しい Hello-world](./media/app-service-linux-ruby-get-started/rails-new-hello-world.png)

    ![新しい Hello-world](./media/app-service-linux-ruby-get-started/rails-new-hello-world-2.png)

    Rails 5.1 以降では、`--skip-yarn` オプションを使用しなかった場合は package.json が作成されます。 そのファイルはデプロイに含める必要がありません。 代替手段として、package.json ファイルを削除するか、ディレクトリの *.gitignore* ファイルに次のように追加します。 

        # Ignore package.json
        /package.json

5. *hello-world* ディレクトリに変更し、サーバーを起動します。

        cd hello-world
        rails server

    ![Hello-world の起動](./media/app-service-linux-ruby-get-started/start-hello-world-server.png)
    
6. Web ブラウザーで `http://localhost:3000` に移動して、ローカルでアプリケーションをテストします。    

    ![Hello-world](./media/app-service-linux-ruby-get-started/hello-world.png)

## <a name="prepare-the-app-for-azure"></a>Azure 向けにアプリを準備する

既定では、Ruby イメージは、`-e production` フラグが指定されたサーバーで実行されます。 この環境では、Azure Web App on Linux でいくつかの設定を行う必要があります。 一部の設定は、コンテナーによって処理されます (`SECRET_KEY_BASE` の設定など)。 既定のルートを構成する必要があります。 構成しなかった場合は、サイトの参照で 404 エラーが表示されます。

既定のルートを構成するには:

1. 編集するために *~/workspace/hello-world/config/routes.rb* を開きます。 次のスクリーンショットに示されている行を追加します。 

        root 'application#hello'

    ![routes.rb](./media/app-service-linux-ruby-get-started/routes-rb.png)


2. 編集するために *~/workspace/hello-world/app/controllers/application_controller.rb* を開きます。 次のスクリーンショットに示されている行を追加します。

        def hello
            render html: "Hello, world from Azure Web App on Linux!"
        end

    ![routes.rb](./media/app-service-linux-ruby-get-started/application-controller-rb.png)


3. これでアプリの構成は完了です。 Web ブラウザーで `http://localhost:3000` に移動して、ルート ランディング ページを確認します。

    ![構成された Hello World](./media/app-service-linux-ruby-get-started/hello-world-configured.png)

## <a name="create-a-ruby-website-on-azure"></a>Azure で Ruby の Web サイトを作成する

1. [Azure ポータル](http://portal.azure.com)に移動し、自分のサブスクリプションでログインします。 次のスクリーンショットに示すように **Web App on Linux** を追加します。

    ![Web App on Linux の作成](./media/app-service-linux-ruby-get-started/top-level-create.png)

2. 次のスクリーンショットのように **[作成] ブレード**が開きます。

    ![[作成] ブレード](./media/app-service-linux-ruby-get-started/create-blade.png)


    1. Web アプリに名前を付けます。
    2. 既存のリソース グループを選択するか、新しいリソース グループを作成します  (「[limitations (制限)](app-service-linux-intro.md)」セクションで、使用可能なリージョンを確認してください)。
    3. 既存の Azure App Service プランを選択するか、新しいプランを作成します  (「[limitations (制限)](app-service-linux-intro.md)」セクションで、App Service プランの注意事項を確認してください)。
    4. コンテナーの構成では **[Ruby 2.3]** 組み込みランタイム スタックを選択します。
    5. Web アプリ用に **[ダッシュボードにピン留めする]** をクリックします。
    6. **[作成]**をクリックします。

3. Web アプリが作成されると、**[概要]** ブレードが表示されます。 新しい Web アプリの **URL** をコピーし、ブラウザーで開きます。 次のスプラッシュ ページが表示されます。

    ![スプラッシュ ページ](./media/app-service-linux-ruby-get-started/splash-page.png)


## <a name="deploy-your-application"></a>アプリケーションをデプロイする

このチュートリアルでは、Git を使用してローカルの Ruby アプリケーションを Azure にデプロイします。

1. 新しい Azure Web サイトでは、Git のデプロイが既に構成されています。 Git のデプロイ URL は、Web アプリ名を指定して次の URL に移動することで見つけられます。

        https://{your web app name}.scm.azurewebsites.net/api/scm/info

    Git の URL は、Web アプリ名に基づく次の形式になります。

        https://{your web app name}.scm.azurewebsites.net/{your web app name}.git

2. 次のコマンドを実行して、ローカル アプリケーションを Azure Web サイトにデプロイします。

        cd ~/workspace/hello-world
        git init
        git remote add azure <Git deployment URL from above>
        git add -A
        git commit -m "Initial deployment commit"
        git push master

    リモート デプロイ操作が正しく報告されていることを確認します。

    ![Web アプリのデプロイ](./media/app-service-linux-ruby-get-started/deployment-success.png)

    リモートがハングしていることを示すエラーが表示された場合、デプロイはまだ進行中である可能性があります。 この場合は、ブラウザーで次の URL に移動します。

        https://{your web app name}.scm.azurewebsites.net/api/deployments

3. デプロイが完了したら、デプロイを有効にするために Web アプリを再起動します。 [Azure ポータル](http://portal.azure.com)で、Web アプリの **[概要]** ブレードに移動します。

    ツールバーの **[再起動]** をクリックします。

    ![Web アプリの再起動](./media/app-service-linux-ruby-get-started/restart-web-app.png)

4. サイトに移動し、更新が反映されていることを確認します。 

    アプリが再起動している間にサイトを参照しようとすると、結果は HTTP 状態コード エラー 503 (サーバー利用不可) になります。 完全に再起動するまで、数分かかる場合があります。

        http://{your web app name}.azurewebsites.net

    ![更新後の Web アプリ](./media/app-service-linux-ruby-get-started/hello-world-updated.png)
    

## <a name="next-steps"></a>次のステップ
Azure App Service Web App on Linux の詳細については、次のリンクを参照してください。 質問や問題を[フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)に投稿することもできます。

* [App Service on Linux での Web Apps の作成](app-service-linux-how-to-create-web-app.md)
* [App Service on Linux 向けにカスタム Docker イメージを使用する方法](app-service-linux-using-custom-docker-image.md)
* [Web Apps on Linux での Node.js 向け PM2 構成の使用](app-service-linux-using-nodejs-pm2.md)
* [Azure App Service Web Apps on Linux での .NET Core の使用](app-service-linux-using-dotnetcore.md)
* [Azure App Service Web Apps on Linux のFAQ](app-service-linux-faq.md)


