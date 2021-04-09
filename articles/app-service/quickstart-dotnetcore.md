---
title: クイック スタート:C# ASP.NET Core アプリを作成する
description: 初めての ASP.NET Core アプリをデプロイして、Azure App Service で Web アプリを実行する方法を確認します。
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 11/23/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-dotnetcore-uiex
ms.openlocfilehash: 77e0768a617ef79ab8510f88bfdcd41d5647f9bf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101701639"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>クイック スタート:Azure に ASP.NET Core Web アプリを作成する

::: zone pivot="platform-windows"  

このクイック スタートでは、初めての ASP.NET Core Web アプリを作成し、[Azure App Service](overview.md) にデプロイする方法について説明します。 App Service は .NET 5.0 のアプリをサポートします。

完了すると、App Service ホスティング プランと、デプロイされた Web アプリケーションを含む App Service とで構成される、Azure リソース グループを作成できます。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/dotnet/)。
- **[ASP.NET および Web の開発]** ワークロードと共に <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> をインストールする。

  Visual Studio 2019 を既にインストールしている場合:

  - **[ヘルプ]**  >  **[更新プログラムの確認]** の順に選択して、Visual Studio に最新の更新プログラムをインストールします。 最新の更新プログラムには、.NET 5.0 SDK が含まれています。
  - **[ツール]**  >  **[ツールと機能を取得]** の順に選択し、ワークロードを追加します。


## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core Web アプリケーションの作成

次の手順に従って、Visual Studio で ASP.NET Core Web アプリを作成します。

# <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

1. Visual Studio を開き、 **[新しいプロジェクトの作成]** を選択します。

1. **[新しいプロジェクトの作成]** で、 **[ASP.NET Core Web アプリケーション]** を選択し、その選択内容の言語に **[C#]** がリストされていることを確認してから、 **[次へ]** を選択します。

1. **[新しいプロジェクトの構成]** で、ご自分の Web アプリケーション プロジェクトに *myFirstAzureWebApp* という名前を指定し、 **[作成]** を選択します。

   ![Web アプリ プロジェクトを構成する](./media/quickstart-dotnetcore/configure-web-app-project.png)

1. 任意の種類の ASP.NET Core Web アプリを Azure にデプロイできますが、このクイック スタートでは、 **[Web アプリケーション]** テンプレートを選択します。 **[認証]** が **[認証なし]** に設定されていて、他のオプションが選択されていないことを確認してください。 そのうえで **[Create]\(作成\)** を選択します。

   ![新しい ASP.NET Core Web アプリを作成する](./media/quickstart-dotnetcore/create-aspnet-core-web-app.png) 
   
1. Visual Studio のメニューから **[デバッグ]**  >  **[デバッグなしで開始]** を選択して、Web アプリをローカルで実行します。

   ![ローカルで実行されている Web アプリ](./media/quickstart-dotnetcore/web-app-running-locally.png)

# <a name="net-50"></a>[.NET 5.0](#tab/net50)

1. Visual Studio を開き、 **[新しいプロジェクトの作成]** を選択します。

1. **[新しいプロジェクトの作成]** で、 **[ASP.NET Core Web アプリケーション]** を選択し、その選択内容の言語に **[C#]** がリストされていることを確認してから、 **[次へ]** を選択します。

1. **[新しいプロジェクトの構成]** で、ご自分の Web アプリケーション プロジェクトに *myFirstAzureWebApp* という名前を指定し、 **[作成]** を選択します。

   ![Web アプリ プロジェクトを構成する](./media/quickstart-dotnetcore/configure-web-app-project.png)

1. .NET 5.0 アプリの場合は、ドロップダウンから **[ASP.NET Core 5.0]** を選択します。

1. 任意の種類の ASP.NET Core Web アプリを Azure にデプロイできますが、このクイックスタートでは、 **[ASP.NET Core Web アプリ]** テンプレートを選択します。 **[認証]** が **[認証なし]** に設定されていて、他のオプションが選択されていないことを確認してください。 そのうえで **[Create]\(作成\)** を選択します。

   ![新しい ASP.NET Core Web アプリを作成する](./media/quickstart-dotnetcore/create-aspnet-core-web-app-5.png) 
   
1. Visual Studio のメニューから **[デバッグ]**  >  **[デバッグなしで開始]** を選択して、Web アプリをローカルで実行します。

   ![ローカルで実行されている Web アプリ](./media/quickstart-dotnetcore/web-app-running-locally.png)

---

## <a name="publish-your-web-app"></a>Web アプリを発行する

Web アプリを発行するには、まず、アプリの発行先となる新しい App Service を作成して構成する必要があります。 

App Service の設定の一環として、次のものを作成します。

- サービスのすべての Azure リソースを格納する新しい[リソース グループ](../azure-resource-manager/management/overview.md#terminology)。
- アプリをホストする Web サーバー ファームの場所、サイズ、および機能を指定する新しい[ホスティング プラン](./overview-hosting-plans.md)。

App Service を作成して Web アプリを発行するには、次の手順に従います。

1. **ソリューション エクスプローラー** で **myFirstAzureWebApp** プロジェクトを右クリックし、 **[発行]** を選択します。 

1. **[発行]** で **[Azure]** を選択し、 **[次へ]** をクリックします。

1. 表示されるオプションは、既に Azure にサインインしているかどうかや、Visual Studio アカウントが Azure アカウントとリンクされているかどうかによって異なります。 **[アカウントの追加]** または **[サインイン]** を選択して Azure サブスクリプションにサインインします。 既にサインインしている場合は、目的のアカウントを選択します。

   ![Azure へのサインイン](./media/quickstart-dotnetcore/sign-in-azure-vs2019.png)

1. **[App Service インスタンス]** の右側の **[+]** をクリックします。

   ![新しい App Service アプリ](./media/quickstart-dotnetcore/publish-new-app-service.png)

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

1. **[名前]** に、有効な文字 (`a-z`、`A-Z`、`0-9`、`-`) のみから成る一意のアプリ名を入力します。 自動的に生成される一意の名前をそのまま使用してもかまいません。 Web アプリの URL は `http://<app-name>.azurewebsites.net` です。`<app-name>` には自分のアプリの名前を指定します。

2. **[作成]** を選択して、Azure リソースを作成します。

   ![アプリのリソースを作成する](./media/quickstart-dotnetcore/web-app-name-vs2019.png)

   ウィザードが完了すると、Azure リソースが作成され、発行する準備が整います。

3. **[完了]** を選択して、ウィザードを終了します。

1. **[発行]** ページで、 **[発行]** をクリックします。 Visual Studio によってアプリのビルド、パッケージ化、および Azure への発行が行われた後、既定のブラウザーでアプリが起動されます。

   ![Azure で実行されている発行済みの ASP.NET Web アプリ](./media/quickstart-dotnetcore/web-app-running-live.png)

**お疲れさまでした。** Azure App Service で ASP.NET Core Web アプリをライブ実行することができました。

## <a name="update-the-app-and-redeploy"></a>アプリを更新して再デプロイする

Web アプリを更新して再デプロイするには、次の手順に従います。

1. **ソリューション エクスプローラー** で、対象プロジェクトから **[ページ]**  >  **[Index.cshtml]** の順に開きます。

1. `<div>` タグ全体を次のコードに置き換えます。

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Azure に再デプロイするには、**ソリューション エクスプローラー** で **myFirstAzureWebApp** プロジェクトを右クリックし、 **[発行]** を選択します。

1. **発行** の概要ページで **[発行]** を選択します。

   <!-- ![Publish update to web app](./media/quickstart-dotnetcore/publish-update-to-web-app-vs2019.png) -->

    発行が完了すると、Visual Studio で Web アプリの URL のブラウザーが起動されます。

    ![Azure で実行されている更新済みの ASP.NET Web アプリ](./media/quickstart-dotnetcore/updated-web-app-running-live.png)

## <a name="manage-the-azure-app"></a>Azure アプリの管理

Web アプリを管理するには、[Azure portal](https://portal.azure.com) に移動し、 **[App Services]** を検索して選択します。

![[App Services] を選択します](./media/quickstart-dotnetcore/app-services.png)

**[App Services]** ページで、Web アプリの名前を選択します。

:::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="サンプル Web アプリが選択されている App Service ページのスクリーンショット。":::

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
[App Service on Linux](overview.md#app-service-on-linux) は、Linux オペレーティング システムを使用する、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供します。 このクイックスタートでは、[.NET Core](/aspnet/core/) アプリを作成し、[Azure CLI](/cli/azure/get-started-with-azure-cli) を使用して Linux でホストされている App Service にデプロイする方法を示します。

![Azure で実行されるサンプル アプリ](media/quickstart-dotnetcore/dotnet-browse-azure.png)

この記事の手順は、Mac、Windows、または Linux コンピューターを使って実行できます。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-initial-environment"></a>初期環境を設定する

# <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

このクイック スタートを完了するには、以下が必要です。

* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">最新の .NET Core 3.1 SDK をインストールします</a>。
* <a href="/cli/azure/install-azure-cli" target="_blank">最新の Azure CLI をインストールします</a>。

# <a name="net-50"></a>[.NET 5.0](#tab/net50)

このクイック スタートを完了するには、以下が必要です。

* <a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank">最新の .NET 5.0 SDK をインストールします</a>。
* <a href="/cli/azure/install-azure-cli" target="_blank">最新の Azure CLI をインストールします</a>。

---

[問題がある場合は、お知らせください。](https://aka.ms/DotNetAppServiceLinuxQuickStart)

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

```bash
dotnet run
```

Web ブラウザーを開き、`http://localhost:5000` のアプリに移動します。

ページに表示されているサンプル アプリの **Hello World** メッセージが表示されます。

![ブラウザーを使用してテストする](media/quickstart-dotnetcore/dotnet-browse-local.png)

[問題がある場合は、お知らせください。](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="sign-into-azure"></a>Azure にサインインする
ターミナル ウィンドウから次のコマンドを使用して Azure にログインします。

```azurecli
az login
```

## <a name="deploy-the-app"></a>アプリケーションのデプロイ

`az webapp up` コマンドを使用して、ローカル フォルダー (*hellodotnetcore*) にコードをデプロイします。

```azurecli
az webapp up --sku F1 --name <app-name>
```

- `az` コマンドが認識されない場合は、「[初期環境を設定する](#set-up-your-initial-environment)」の説明に従って Azure CLI がインストールされていることを確認してください。
- `<app-name>` を Azure 全体で一意の名前で置き換えます ("*有効な文字は、`a-z`、`0-9`、および `-` です*")。 会社名とアプリ識別子を組み合わせて使用すると、適切なパターンになります。
- `--sku F1` 引数を使用すると、Free 価格レベルで Web アプリが作成されます。 この引数を省略するとより高速な Premium レベルが使用されるため、時間単位のコストが発生します。
- 必要に応じて、引数 `--location <location-name>` を含めることができます。ここで、`<location-name>` は利用可能な Azure リージョンです。 [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations) コマンドを実行すると、お使いの Azure アカウントで使用可能なリージョンの一覧を取得できます。

コマンドが完了するまでに数分かかる場合があります。 実行中には、リソース グループ、App Service プラン、およびホスティング アプリの作成、ログ記録の構成、ZIP デプロイの実行に関するメッセージが表示されます。 次に、"http://&lt;app-name&gt;.azurewebsites.net でアプリを起動することができます" という内容のメッセージが表示されます。これは、Azure 上のアプリの URL です。

# <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

![az webapp up コマンドの出力例](./media/quickstart-dotnetcore/az-webapp-up-output-3.1.png)

# <a name="net-50"></a>[.NET 5.0](#tab/net50)

<!-- Deploy the code in your local folder (*hellodotnetcore*) using the `az webapp up` command:

```azurecli
az webapp up --sku B1 --name <app-name> --os-type linux
```

- If the `az` command isn't recognized, be sure you have the Azure CLI installed as described in [Set up your initial environment](#set-up-your-initial-environment).
- Replace `<app-name>` with a name that's unique across all of Azure (*valid characters are `a-z`, `0-9`, and `-`*). A good pattern is to use a combination of your company name and an app identifier.
- The `--sku B1` argument creates the web app in the Basic pricing tier, which incurs an hourly cost. Omit this argument to use a faster premium tier, which costs more.
- You can optionally include the argument `--location <location-name>` where `<location-name>` is an available Azure region. You can retrieve a list of allowable regions for your Azure account by running the [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations) command.

The command may take a few minutes to complete. While running, it provides messages about creating the resource group, the App Service plan and hosting app, configuring logging, then performing ZIP deployment. It then gives the message, "You can launch the app at http://&lt;app-name&gt;.azurewebsites.net", which is the app's URL on Azure. -->

![az webapp up コマンドの出力例](./media/quickstart-dotnetcore/az-webapp-up-output-5.0.png)

---

[問題がある場合は、お知らせください。](https://aka.ms/DotNetAppServiceLinuxQuickStart)

[!include [az webapp up command note](../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>アプリの参照

Web ブラウザーを使用して、デプロイされたアプリケーションを参照します。

```bash
http://<app_name>.azurewebsites.net
```

App Service on Linux で組み込みのイメージを使用して .NET Core サンプル コードが実行されています。

![Azure で実行されるサンプル アプリ](media/quickstart-dotnetcore/dotnet-browse-azure.png)

**お疲れさまでした。** App Service on Linux に初めての .NET Core アプリをデプロイしました。

[問題がある場合は、お知らせください。](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="update-and-redeploy-the-code"></a>コードを更新して再デプロイする

ローカル ディレクトリで、_Startup.cs_ ファイルを開きます。 メソッド呼び出し `context.Response.WriteAsync` 内のテキストに小さな変更を加えます。

```csharp
await context.Response.WriteAsync("Hello Azure!");
```

変更を保存してから、もう一度 `az webapp up` コマンドを使用してアプリを再デプロイします。

```azurecli
az webapp up --os-type linux
```

このコマンドでは、 *.azure/config* ファイルにローカルでキャッシュされている値 (アプリ名、リソース グループ、App Service プランなど) を使用します。

デプロイが完了したら、「**アプリの参照**」の手順で開いた元のブラウザー ウィンドウに切り替えて、更新をクリックします。

![Azure で実行される更新済みのサンプル アプリ](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)

[問題がある場合は、お知らせください。](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="manage-your-new-azure-app"></a>新しい Azure アプリの管理

<a href="https://portal.azure.com" target="_blank">Azure portal</a> に移動し、お客様が作成したアプリを管理します。

左側のメニューで **[App Services]** をクリックしてから、お客様の Azure アプリの名前をクリックします。

:::image type="content" source="./media/quickstart-dotnetcore/portal-app-service-list-up.png" alt-text="サンプル Azure アプリの選択状態を示す App Service ページのスクリーンショット。":::

お客様のアプリの [概要] ページを確認します。 ここでは、参照、停止、開始、再開、削除のような基本的な管理タスクを行うことができます。 

![Azure Portal の [App Service] ページ](media/quickstart-dotnetcore/portal-app-overview-up.png)

左側のメニューは、アプリを構成するためのさまざまなページを示しています。 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

[問題がある場合は、お知らせください。](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:ASP.NET Core アプリと SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [ASP.NET Core アプリの構成](configure-language-dotnetcore.md)

::: zone-end
