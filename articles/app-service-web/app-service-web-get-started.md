---
title: "初めての Web アプリを Azure に&5; 分でデプロイする | Microsoft Docs"
description: "サンプル アプリをデプロイして、App Service での Web アプリの実行がいかに簡単であるかを説明します。 実際の開発を速やかに開始し、すぐに成果を確認できます。"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 65c9bdd9-8763-4c56-8e15-f790992e951e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/04/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: 7cf4f7c5e0d3e4b51c98fb7a98bbf9de95c9fd7b
ms.lasthandoff: 03/01/2017


---
# <a name="deploy-your-first-web-app-to-azure-in-five-minutes"></a>初めての Web アプリを Azure に&5; 分でデプロイする
このチュートリアルでは、初めての Web アプリを [Azure App Service](../app-service/app-service-value-prop-what-is.md)にデプロイします。
App Service を使用すると、Web アプリ、[モバイル アプリ バックエンド](/documentation/learning-paths/appservice-mobileapps/)、および [API アプリ](../app-service-api/app-service-api-apps-why-best-platform.md)を作成できます。

このチュートリアルの内容は次のとおりです。 

* Azure App Service で Web アプリを作成する。
* サンプル コードをデプロイする (ASP.NET、PHP、Node.js、Java、Python の中から選択する)。
* 運用環境でライブ実行されているコードを確認する。
* [Git コミットをプッシュする](https://git-scm.com/docs/git-push)ときと同じ方法で Web アプリを更新する。

[!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

## <a name="cli-versions-to-complete-the-task"></a>タスクを完了するための CLI バージョン

次のいずれかの CLI バージョンを使用してタスクを完了できます。

- [Azure CLI 1.0](app-service-web-get-started-cli-nodejs.md) - クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用の CLI
- [Azure CLI 2.0](app-service-web-get-started.md) - Resource Manager デプロイ モデル用の次世代 CLI

## <a name="prerequisites"></a>前提条件
* [Git](http://www.git-scm.com/downloads)。
* [Azure CLI 2.0 プレビュー](/cli/azure/install-az-cli2)。
* Microsoft Azure アカウント。 アカウントを持っていない場合は、[無料試用版にサインアップ](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)するか [Visual Studio サブスクライバー特典を有効](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)にしてください。

> [!NOTE]
> Azure アカウントがなくても、[App Service を試用](https://azure.microsoft.com/try/app-service/)できます。 スターター アプリを作成し、最大&1; 時間使用できます。クレジット カードも契約も不要です。
> 
> 

## <a name="deploy-a-web-app"></a>Web アプリをデプロイする
Azure App Service に Web アプリをデプロイしましょう。

1. 新しい Windows コマンド プロンプト、PowerShell ウィンドウ、Linux のシェル、または OS X ターミナルを開きます。 `git --version` と `az --version` を実行し、Git と Azure CLI がコンピューターにインストールされていることを確認します。
   
    ![Test installation of CLI tools for your first web app in Azure](./media/app-service-web-get-started/1-test-tools-2.0.png)
   
    ツールをインストールしていない場合は、「 [前提条件](#Prerequisites) 」のダウンロード リンクを参照してください。

2. 次のようにして、Azure にログインします。
   
        az login
   
    ヘルプ メッセージに従って、ログイン プロセスを続行します。
   
    ![Log in to Azure to create your first web app](./media/app-service-web-get-started/3-azure-login-2.0.png)

3. App Service のデプロイ ユーザーを設定します。 後で、これらの資格情報を使用してコードをデプロイします。
   
        az appservice web deployment user set --user-name <username> --password <password>

3. 新しい[リソース グループ](../azure-resource-manager/resource-group-overview.md)を作成します。 この最初の App Service のチュートリアルでは、実際にその内容を把握している必要はありません。

        az group create --location "<location>" --name my-first-app-group

    `<location>` に使用できる値を確認するには、CLI コマンド `az appservice list-locations` を使用してください。

3. 新しい "Free" [App Service プラン](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)を作成します。 この最初の App Service のチュートリアルでは、このプランの Web アプリに対しては課金されないことを把握しておくだけでかまいません。

        az appservice plan create --name my-free-appservice-plan --resource-group my-first-app-group --sku FREE

4. `<app_name>` に一意の名前を指定して新しい Web アプリを作成します。

        az appservice web create --name <app_name> --resource-group my-first-app-group --plan my-free-appservice-plan

4. 次に、デプロイするサンプル コードを取得します。 作業ディレクトリに移動 (`CD`) し、次のようにサンプル アプリを複製します。
   
        cd <working_directory>
        git clone <github_sample_url>
   
    *&lt;github_sample_url>* には、好みのフレームワークに応じて次の URL のいずれかを使用します。
   
   * HTML + CSS + JS: [https://github.com/Azure-Samples/app-service-web-html-get-started.git](https://github.com/Azure-Samples/app-service-web-html-get-started.git)
   * ASP.NET: [https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git](https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git)
   * PHP (CodeIgniter): [https://github.com/Azure-Samples/app-service-web-php-get-started.git](https://github.com/Azure-Samples/app-service-web-php-get-started.git)
   * Node.js (Express): [https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git](https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git)
   * Java: [https://github.com/Azure-Samples/app-service-web-java-get-started.git](https://github.com/Azure-Samples/app-service-web-java-get-started.git)
   * Python (Django): [https://github.com/Azure-Samples/app-service-web-python-get-started.git](https://github.com/Azure-Samples/app-service-web-python-get-started.git)

    ![Clone the app sample code for your first web app in Azure](./media/app-service-web-get-started/2-clone-sample.png)
   
5. サンプル アプリのリポジトリに移動します。 For example:
   
        cd app-service-web-html-get-started

5. 次のコマンドを使用して、App Service Web アプリのローカル Git デプロイを構成します。

        az appservice web source-control config-local-git --name <app_name> --resource-group my-first-app-group

    次のような JSON 出力が表示されます。これは、リモート Git リポジトリが構成されていることを意味します。

        {
        "url": "https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git"
        }

6. この URL を、ローカル リポジトリの Git リモートとして JSON に追加します (Git リモートは、わかりやすくするために `azure` という名前にしています)。

        git remote add azure https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git
   
7. サンプル コードを `azure` Git リモートにデプロイします。 入力を求められたら、前に構成したデプロイ資格情報を使用します。
   
        git push azure master
   
    ![Push code to your first web app in Azure](./media/app-service-web-get-started/5-push-code.png)
   
    いずれかの言語フレームワークを使用した場合は、別の出力が表示されます。 `git push` を実行すると、Azure にコードが配置されるだけでなく、デプロイ エンジンのデプロイ タスクがトリガーされます。 プロジェクト (リポジトリ) のルートに package.json (Node.js) ファイルまたは requirements.txt (Python) ファイルがある場合、または ASP.NET プロジェクトに packages.config ファイルがある場合は、デプロイメント スクリプトによって必要なパッケージが復元されます。 また、 [Composer 拡張機能を有効にして](web-sites-php-mysql-deploy-use-git.md#composer) 、PHP アプリで composer.json ファイルを自動的に処理することもできます。

これで、Azure App Service にアプリがデプロイされました。

## <a name="see-your-app-running-live"></a>アプリがライブ実行されるのを確認する

Azure でアプリがライブ実行されるのを確認するには、次のコマンドを実行します。

    az appservice web browse --name <app_name> --resource-group my-first-app-group

## <a name="make-updates-to-your-app"></a>アプリを更新する

Git を使用してプロジェクト (リポジトリ) のルートからプッシュして、いつでもライブ サイトを更新することができるようになりました。 これは、初めてコードをデプロイしたときと同様に行います。 たとえば、ローカルでテストした新しい変更をプッシュする場合は、プロジェクト (リポジトリ) のルートから次のコマンドを実行するだけで済みます。

    git add .
    git commit -m "<your_message>"
    git push azure master

## <a name="next-steps"></a>次のステップ

ご使用の言語フレームワークの推奨される開発とデプロイの手順を確認します。

* [.NET](web-sites-dotnet-get-started.md)
* [PHP](app-service-web-php-get-started.md)
* [Node.JS](app-service-web-nodejs-get-started.md)
* [Python](web-sites-python-ptvs-django-mysql.md)
* [Java](web-sites-java-get-started.md)

または、最初の Web アプリを活用します。 次に例を示します。

* [Azure にコードをデプロイする他の方法](web-sites-deploy.md)を試してみます。 たとえば、GitHub リポジトリのいずれかからデプロイする場合、**[デプロイ オプション]** の **[ローカル Git リポジトリ]** ではなく、**[GitHub]** を選択します。
* Azure アプリを次のレベルに進めます。 ユーザーを認証します。 必要に応じてスケールを変更したり、 パフォーマンスのアラートを設定したりできます。 いずれも、数回のクリックで実現できます。 「[初めての Web アプリに機能を追加する](app-service-web-get-started-2.md)」を参照してください。


