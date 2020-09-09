---
title: クイック スタート:C# ASP.NET Core アプリを作成する
description: 初めての ASP.NET Core アプリをデプロイして、Azure App Service で Web アプリを実行する方法を確認します。
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 5604153d31a2828bcefbeccbd2f4fea428e878b5
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961535"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>クイック スタート:Azure に ASP.NET Core Web アプリを作成する

::: zone pivot="platform-windows"  

このクイック スタートでは、初めての ASP.NET Core Web アプリを作成し、[Azure App Service](overview.md) にデプロイする方法について説明します。 

完了すると、App Service ホスティング プランと、デプロイされた Web アプリケーションを含む App Service とで構成される、Azure リソース グループを作成できます。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/dotnet/)。
- このクイック スタートでは、Windows 上の App Service にアプリをデプロイします。 _Linux_ 上の App Service にデプロイするには、[App Service での .NET Core Web アプリの作成](./quickstart-dotnetcore.md)に関するページをご覧ください。
- **[ASP.NET および Web の開発]** ワークロードと共に <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> をインストールする。

  Visual Studio 2019 を既にインストールしている場合:

  - **[ヘルプ]**  >  **[更新プログラムの確認]** の順に選択して、Visual Studio に最新の更新プログラムをインストールします。
  - **[ツール]**  >  **[ツールと機能を取得]** の順に選択し、ワークロードを追加します。


## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core Web アプリケーションの作成

次の手順に従って、Visual Studio で ASP.NET Core Web アプリを作成します。

1. Visual Studio を開き、 **[新しいプロジェクトの作成]** を選択します。

1. **[新しいプロジェクトの作成]** で、 **[ASP.NET Core Web アプリケーション]** を選択し、その選択内容の言語に **[C#]** がリストされていることを確認してから、 **[次へ]** を選択します。

1. **[新しいプロジェクトの構成]** で、ご自分の Web アプリケーション プロジェクトに *myFirstAzureWebApp* という名前を指定し、 **[作成]** を選択します。

   ![Web アプリ プロジェクトを構成する](./media/quickstart-dotnetcore/configure-web-app-project.png)

1. 任意の種類の ASP.NET Core Web アプリを Azure にデプロイできますが、このクイック スタートでは、 **[Web アプリケーション]** テンプレートを選択します。 **[認証]** が **[認証なし]** に設定されていて、他のオプションが選択されていないことを確認してください。 そのうえで **[Create]\(作成\)** を選択します。

   ![新しい ASP.NET Core Web アプリを作成する](./media/quickstart-dotnetcore/create-aspnet-core-web-app.png) 
   
1. Visual Studio のメニューから **[デバッグ]**  >  **[デバッグなしで開始]** を選択して、Web アプリをローカルで実行します。

   ![ローカルで実行されている Web アプリ](./media/quickstart-dotnetcore/web-app-running-locally.png)

## <a name="publish-your-web-app"></a>Web アプリを発行する

Web アプリを発行するには、まず、アプリの発行先となる新しい App Service を作成して構成する必要があります。 

App Service の設定の一環として、次のものを作成します。

- サービスのすべての Azure リソースを格納する新しい[リソース グループ](../azure-resource-manager/management/overview.md#terminology)。
- アプリをホストする Web サーバー ファームの場所、サイズ、および機能を指定する新しい[ホスティング プラン](./overview-hosting-plans.md)。

App Service を作成して Web アプリを発行するには、次の手順に従います。

1. **ソリューション エクスプローラー**で **myFirstAzureWebApp** プロジェクトを右クリックし、 **[発行]** を選択します。 まだ Visual Studio から Azure アカウントにサインインしていない場合は、 **[アカウントの追加]** または **[サインイン]** を選択します。 無料の Azure アカウントを作成することもできます。

1. **[発行先を選択]** ダイアログボックスで **[App Service]** を選択し、 **[新規作成]** を選択して、 **[プロファイルの作成]** を選択します。

   ![発行先の選択](./media/quickstart-dotnetcore/pick-publish-target-vs2019.png)

1. **App Service の [新規作成]** ダイアログボックスで、既定の名前を受け入れるか、新しい名前を入力して、アプリに対してグローバルに一意の**名前**を指定します。 有効な文字は、`a-z`、`A-Z`、`0-9`、および `-` です。 この**名前**は、Web アプリの URL プレフィックスとして `http://<app_name>.azurewebsites.net` の形式で使用されます。

1. **[サブスクリプション]** で、リストされているサブスクリプションを受け入れるか、ドロップダウン リストから新しいサブスクリプションを選択します。

1. **[リソース グループ]** で、 **[新規]** を選択します。 **[新しいリソース グループ名]** に「*myResourceGroup*」と入力し、 **[OK]** を選択します。 

1. **[ホスティング プラン]** で、 **[新規]** を選択します。 

1. **ホスティング プランの [新規作成]** ダイアログで、次の表で指定されている値を使用します。

   | 設定  | 推奨値 | 説明 |
   | -------- | --------------- | ----------- |
   | **ホスティング プラン**  | *myFirstAzureWebAppPlan* | App Service プランの名前です。 |
   | **場所**      | "*西ヨーロッパ*" | Web アプリがホストされているデータ センターです。 |
   | **[サイズ]**          | *Free* | [価格レベル](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)によって、ホスティング機能が決まります。 |
   
   ![新しいホスティング プランを作成する](./media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png)

1. **[Application Insights]** は *[なし]* のままにします。

1. **App Service の [新規作成]** ダイアログ ボックスで **[作成]** をクリックして、Azure リソースの作成を開始します。

   ![新しい App Service を作成する](./media/quickstart-dotnetcore/create-new-app-service-vs2019.png)

1. ウィザードが完了したら、 **[発行]** を選択します。

   ![Web アプリを Azure に発行する](./media/quickstart-dotnetcore/publish-web-app-vs2019.png)

   Visual Studio によって ASP.NET Core Web アプリが Azure に発行され、既定のブラウザーでアプリが起動されます。 

   ![Azure で実行されている発行済みの ASP.NET Web アプリ](./media/quickstart-dotnetcore/web-app-running-live.png)

**お疲れさまでした。** Azure App Service で ASP.NET Core Web アプリをライブ実行することができました。

## <a name="update-the-app-and-redeploy"></a>アプリを更新して再デプロイする

Web アプリを更新して再デプロイするには、次の手順に従います。

1. **ソリューション エクスプローラー**で、対象プロジェクトから **[ページ]**  >  **[Index.cshtml]** の順に開きます。

1. `<div>` タグ全体を次のコードに置き換えます。

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Azure に再デプロイするには、**ソリューション エクスプローラー**で **myFirstAzureWebApp** プロジェクトを右クリックし、 **[発行]** を選択します。

1. **発行**の概要ページで **[発行]** を選択します。

   ![Web アプリに更新を発行する](./media/quickstart-dotnetcore/publish-update-to-web-app-vs2019.png)

発行が完了すると、Visual Studio で Web アプリの URL のブラウザーが起動されます。

![Azure で実行されている更新済みの ASP.NET Web アプリ](./media/quickstart-dotnetcore/updated-web-app-running-live.png)

## <a name="manage-the-azure-app"></a>Azure アプリの管理

Web アプリを管理するには、[Azure portal](https://portal.azure.com) に移動し、 **[App Services]** を検索して選択します。

![[App Services] を選択します](./media/quickstart-dotnetcore/app-services.png)

**[App Services]** ページで、Web アプリの名前を選択します。

![Azure アプリへのポータル ナビゲーション](./media/quickstart-dotnetcore/select-app-service.png)

Web アプリの **[概要]** ページには、参照、停止、開始、再起動、削除などの基本的な管理のためのオプションが含まれています。 左側のメニューは、アプリを構成するためのさらなるページを示しています。

![Azure portal の App Service](./media/quickstart-dotnetcore/web-app-overview-page.png)

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Visual Studio を使用して ASP.NET Core Web アプリを作成し、Azure App Service にデプロイしました。

次の記事に進み、.NET Core アプリを作成して SQL Database に接続する方法を学習してください。

> [!div class="nextstepaction"]
> [ASP.NET Core と SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [ASP.NET Core アプリの構成](configure-language-dotnetcore.md)

::: zone-end  

::: zone pivot="platform-linux"
[App Service on Linux](overview.md#app-service-on-linux) は、Linux オペレーティング システムを使用する、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供します。 このクイックスタートでは、App Service on Linux に [.NET Core](/aspnet/core/) アプリを作成する方法を示します。 [Azure CLI](/cli/azure/get-started-with-azure-cli) を使用してアプリを作成し、Git を使用してアプリに .NET Core コードをデプロイします。

![Azure で実行されるサンプル アプリ](media/quickstart-dotnetcore/dotnet-browse-azure.png)

この記事の手順は、Mac、Windows、または Linux コンピューターを使って実行できます。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、以下が必要です。

* <a href="https://git-scm.com/" target="_blank">Git をインストールする</a>
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">最新の .NET Core 3.1 SDK をインストールする</a>

## <a name="create-the-app-locally"></a>アプリをローカルで作成する

コンピューターのターミナル ウィンドウで、`hellodotnetcore` という名前のディレクトリを作成し、現在のディレクトリをそのディレクトリに変更します。

```bash
mkdir hellodotnetcore
cd hellodotnetcore
```

新しい .NET Core アプリを作成します。

```bash
dotnet new web
```

## <a name="run-the-app-locally"></a>アプリをローカルで実行する

アプリケーションをローカルで実行すると、アプリケーションを Azure にデプロイするとどう表示されるかを把握できます。 

NuGet パッケージを復元し、アプリを実行します。

```bash
dotnet run
```

Web ブラウザーを開き、`http://localhost:5000` のアプリに移動します。

ページに表示されているサンプル アプリの **Hello World** メッセージが表示されます。

![ブラウザーを使用してテストする](media/quickstart-dotnetcore/dotnet-browse-local.png)

ターミナル ウィンドウで **Ctrl + C** キーを押して、Web サーバーを終了します。 .NET Core プロジェクト用の Git リポジトリを初期化します。

```bash
git init
git add .
git commit -m "first commit"
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Web アプリを作成する

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)]

新しく作成されたアプリに移動します。 _&lt;app_name>_ は、自分のアプリの名前に置き換えます。

```bash
https://<app-name>.azurewebsites.net
```

新しいアプリは次のようになります。

![空のアプリ ページ](media/quickstart-dotnetcore/dotnet-browse-created.png)

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure.md)] 

<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'd6b54472f7'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote:
remote: Oryx Version      : 0.2.20200114.13, Commit: 204922f30f8e8d41f5241b8c218425ef89106d1d, ReleaseTagName: 20200114.13
remote: Build Operation ID: |imoMY2y77/s=.40ca2a87_
remote: Repository Commit : d6b54472f7e8e9fd885ffafaa64522e74cf370e1
.
.
.
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;app-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/d6b54472f7e8e9fd885ffafaa64522e74cf370e1/log'
To https://&lt;app-name&gt;.scm.azurewebsites.net:443/&lt;app-name&gt;.git
   d87e6ca..d6b5447  master -> master
</pre>

## <a name="browse-to-the-app"></a>アプリの参照

Web ブラウザーを使用して、デプロイされたアプリケーションを参照します。

```bash
http://<app_name>.azurewebsites.net
```

App Service on Linux で組み込みのイメージを使用して .NET Core サンプル コードが実行されています。

![Azure で実行されるサンプル アプリ](media/quickstart-dotnetcore/dotnet-browse-azure.png)

**お疲れさまでした。** App Service on Linux に初めての .NET Core アプリをデプロイしました。

## <a name="update-and-redeploy-the-code"></a>コードを更新して再デプロイする

ローカル ディレクトリで、_Startup.cs_ ファイルを開きます。 メソッド呼び出し `context.Response.WriteAsync` 内のテキストに小さな変更を加えます。

```csharp
await context.Response.WriteAsync("Hello Azure!");
```

Git で変更をコミットしてから、コード変更を Azure にプッシュします。

```bash
git commit -am "updated output"
git push azure master
```

デプロイが完了したら、「**アプリの参照**」の手順で開いた元のブラウザー ウィンドウに切り替えて、更新をクリックします。

![Azure で実行される更新済みのサンプル アプリ](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)

## <a name="manage-your-new-azure-app"></a>新しい Azure アプリの管理

<a href="https://portal.azure.com" target="_blank">Azure portal</a> に移動し、お客様が作成したアプリを管理します。

左側のメニューで **[App Services]** をクリックしてから、お客様の Azure アプリの名前をクリックします。

![Azure アプリへのポータル ナビゲーション](./media/quickstart-dotnetcore/portal-app-service-list.png)

お客様のアプリの [概要] ページを確認します。 ここでは、参照、停止、開始、再開、削除のような基本的な管理タスクを行うことができます。 

![Azure Portal の [App Service] ページ](media/quickstart-dotnetcore/portal-app-overview.png)

左側のメニューは、アプリを構成するためのさまざまなページを示しています。 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:ASP.NET Core アプリと SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [ASP.NET Core アプリの構成](configure-language-dotnetcore.md)

::: zone-end