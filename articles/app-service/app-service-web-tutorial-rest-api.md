---
title: CORS を使用して RESTful API をホストする - Azure App Service | Microsoft Docs
description: Azure App Service で CORS サポートを使用して RESTful API をホストする方法について説明します。
services: app-service\api
documentationcenter: dotnet
author: cephalin
manager: cfowler
editor: ''
ms.assetid: a820e400-06af-4852-8627-12b3db4a8e70
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/21/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: b8c1130a45f60b9caaacd365cd1c256f50ed7675
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53629570"
---
# <a name="tutorial-host-a-restful-api-with-cors-in-azure-app-service"></a>チュートリアル:Azure App Service で CORS を使用して RESTful API をホストする

[Azure App Service](overview.md) では、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供しています。 さらに、App Service には、RESTful API 用の[クロスオリジン リソース共有 (CORS)](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing) の組み込みサポートがあります。 このチュートリアルでは、CORS サポートを使用して ASP.NET Core API アプリを App Service にデプロイする方法について説明します。 コマンドライン ツールを使用してアプリを構成し、Git を使用してアプリをデプロイします。 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure CLI を使用して App Service リソースを作成する
> * Git を使用して RESTful API を Azure にデプロイする
> * App Service の CORS サポートを有効にする

このチュートリアルの手順は、macOS、Linux、Windows で実行できます。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* [Git をインストールします](https://git-scm.com/)。
* [.NET Core のインストール](https://www.microsoft.com/net/core/)。

## <a name="create-local-aspnet-core-app"></a>ローカル ASP.NET Core アプリの作成

この手順では、ローカル ASP.NET Core プロジェクトを設定します。 App Service では、他の言語で記述された API についても同じワークフローがサポートされます。

### <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

ターミナル ウィンドウから、`cd` コマンドで作業ディレクトリに移動します。  

次のコマンドを実行して、サンプル レポジトリを複製します。 

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
```

このリポジトリには、「[Swagger を使用する ASP.NET Core Web API のヘルプ ページ](/aspnet/core/tutorials/web-api-help-pages-using-swagger?tabs=visual-studio)」というチュートリアルに基づいて作成されたアプリが含まれています。 ここでは、Swagger ジェネレーターを使用して [Swagger UI](https://swagger.io/swagger-ui/) と Swagger JSON エンドポイントが提供されます。

### <a name="run-the-application"></a>アプリケーションの実行

次のコマンドを実行して、必要なパッケージをインストールし、データベースの移行を実行し、アプリケーションを起動します。

```bash
cd dotnet-core-api
dotnet restore
dotnet run
```

ブラウザーで `http://localhost:5000/swagger` に移動して、Swagger UI を起動します。

![ローカルで実行される ASP.NET Core API](./media/app-service-web-tutorial-rest-api/local-run.png)

`http://localhost:5000/api/todo` に移動して、ToDo JSON 項目の一覧を確認します。

`http://localhost:5000` に移動して、ブラウザー アプリを起動します。 後で、ブラウザー アプリで App Service のリモート API を参照して、CORS 機能をテストします。 ブラウザー アプリのコードは、リポジトリの _wwwroot_ ディレクトリにあります。

任意のタイミングで ASP.NET Core を停止するには、ターミナルで `Ctrl+C` キーを押します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>アプリを Azure にデプロイする

この手順では、SQL Database に接続された .NET Core アプリケーションを App Service にデプロイします。

### <a name="configure-local-git-deployment"></a>ローカル Git デプロイを構成する

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-a-resource-group"></a>リソース グループの作成

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service プランを作成する

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>Web アプリを作成する

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)] 

### <a name="push-to-azure-from-git"></a>Git から Azure へのプッシュ

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 98, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (92/92), done.
Writing objects: 100% (98/98), 524.98 KiB | 5.58 MiB/s, done.
Total 98 (delta 8), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: .
remote: Updating submodules.
remote: Preparing deployment for commit id '0c497633b8'.
remote: Generating deployment script.
remote: Project file path: ./DotNetCoreSqlDb.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-azure-app"></a>Azure アプリの参照

ブラウザーで `http://<app_name>.azurewebsites.net/swagger` に移動して、Swagger UI を起動します。

![Azure App Service で実行される ASP.NET Core API](./media/app-service-web-tutorial-rest-api/azure-run.png)

`http://<app_name>.azurewebsites.net/swagger/v1/swagger.json` に移動して、デプロイされた API の _swagger.json_ を確認します。

`http://<app_name>.azurewebsites.net/api/todo` に移動して、デプロイされた API が機能していることを確認します。

## <a name="add-cors-functionality"></a>CORS 機能の追加

次に、API について App Service の組み込み CORS サポートを有効にします。

### <a name="test-cors-in-sample-app"></a>サンプル アプリで CORS をテストする

ローカル リポジトリで、_wwwroot/index.html_ を開きます。

行 51 で `apiEndpoint` 変数を、デプロイされた API の URL (`http://<app_name>.azurewebsites.net`) に設定します。 _\<app_name>_ は、App Service のアプリ名に置き換えます。

ローカル ターミナル ウィンドウでサンプル アプリをもう一度実行します。

```bash
dotnet run
```

`http://localhost:5000` でブラウザー アプリに移動します。 ブラウザーで開発者ツール ウィンドウを開き (Windows 用の Chrome では `Ctrl` + `Shift` + `i`)、**[Console]\(コンソール\)** タブを確認します。`No 'Access-Control-Allow-Origin' header is present on the requested resource` というエラー メッセージが表示されています。

![ブラウザー クライアントでの CORS エラー](./media/app-service-web-tutorial-rest-api/cors-error.png)

ブラウザー アプリ (`http://localhost:5000`) とリモート リソース (`http://<app_name>.azurewebsites.net`) の間でドメインが一致しておらず、App Service の API が `Access-Control-Allow-Origin` ヘッダーを送信していないため、ブラウザー アプリでのクロスドメイン コンテンツの読み込みが、ブラウザーによって妨げられています。

運用環境のブラウザー アプリでは localhost URL ではなくパブリック URL が使用されます。しかし、localhost URL に対して CORS を有効にする方法は、パブリック URL の場合と同じです。

### <a name="enable-cors"></a>CORS を有効にする 

Cloud Shell で [`az resource update`](/cli/azure/resource#az-resource-update) コマンドを使用して、クライアントの URL に対して CORS を有効にします。 _&lt;appname>_ プレースホルダーを置き換えます。

```azurecli-interactive
az resource update --name web --resource-group myResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<app_name> --set properties.cors.allowedOrigins="['http://localhost:5000']" --api-version 2015-06-01
```

`properties.cors.allowedOrigins` (`"['URL1','URL2',...]"`) で 2 つ以上のクライアント URL を設定できます。 また、`"['*']"` を使用して、すべてのクライアント URL を有効にすることもできます。

> [!NOTE]
> Cookie や認証トークンなどの資格情報をアプリで送信する必要がある場合、ブラウザーは、応答に `ACCESS-CONTROL-ALLOW-CREDENTIALS` ヘッダーを必要とします。 App Service でこれを有効にする場合は、CORS の構成で `properties.cors.supportCredentials` を `true` に設定してください。`allowedOrigins` に `'*'` が含まれているときに、これを有効にすることはできません。

### <a name="test-cors-again"></a>CORS をもう一度テストする

`http://localhost:5000` でブラウザー アプリを更新します。 **[Console]\(コンソール\)** ウィンドウのエラー メッセージは消えており、デプロイされた API からのデータを確認して操作できます。 これで、ローカルで実行されているブラウザー アプリへの CORS がリモート API でサポートされました。 

![ブラウザー クライアントでの CORS の成功](./media/app-service-web-tutorial-rest-api/cors-success.png)

おめでとうございます。CORS サポートを使用して Azure App Service の API が実行されています。

## <a name="app-service-cors-vs-your-cors"></a>App Service CORS と独自の CORS

App Service CORS ではなく独自の CORS ユーティリティを使用して、柔軟性を高めることができます。 たとえば、異なるルートまたはメソッドに対して、別の許可されるオリジンを指定したい場合があります。 App Service CORS では、すべての API ルートとメソッドに 1 セットの許可されるオリジンを指定できます。そのため、独自の CORS コードを使用する必要があります。 ASP.NET Core でこれを実行する方法については、「[Enabling Cross-Origin Requests (CORS) (クロスオリジン要求の有効化 (CORS))](/aspnet/core/security/cors)」を参照してください。

> [!NOTE]
> App Service CORS と独自の CORS コードを一緒に使用しないでください。 一緒に使用すると、App Service CORS が優先され、独自の CORS コードは機能しません。
>
>

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>次の手順

ここで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure CLI を使用して App Service リソースを作成する
> * Git を使用して RESTful API を Azure にデプロイする
> * App Service の CORS サポートを有効にする

次のチュートリアルに進み、ユーザーを認証および承認する方法を学習してください。

> [!div class="nextstepaction"]
> [チュートリアル: エンドツーエンドでのユーザーの認証と承認](app-service-web-tutorial-auth-aad.md)
