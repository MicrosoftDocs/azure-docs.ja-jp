---
title: Linux での ASP.NET Core と SQL Database - Azure App Service | Microsoft Docs
description: SQL Database に接続された ASP.NET Core アプリを Azure App Service on Linux で動作させる方法について説明します。
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 4837867188721b13b3f4cb64245ae85a1e32fe50
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67656637"
---
# <a name="build-an-aspnet-core-and-sql-database-app-in-azure-app-service-on-linux"></a>Azure App Service on Linux での ASP.NET Core および SQL Database アプリの作成

> [!NOTE]
> この記事では、Linux 上の App Service にアプリをデプロイします。 _Windows_ 上の App Service にデプロイするには、[Azure App Service での .NET Core および SQL Database のアプリの作成](../app-service-web-tutorial-dotnetcore-sqldb.md)に関するページを参照してください。
>

[App Service on Linux](app-service-linux-intro.md) は、Linux オペレーティング システムを使用する、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供します。 このチュートリアルでは、.NET Core アプリを作成し、SQL Database に接続する方法について説明します。 完了すると、.NET Core MVC アプリが App Service on Linux で実行されます。

![App Service on Linux で実行されるアプリ](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure で SQL データベースを作成する
> * .NET Core アプリを SQL Database に接続する
> * Azure にアプリケーションをデプロイする
> * データ モデルを更新し、アプリを再デプロイする
> * Azure から診断ログをストリーミングする
> * Azure Portal でアプリを管理する

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* [Git をインストールする](https://git-scm.com/)
* [.NET Core をインストールする](https://www.microsoft.com/net/core/)

## <a name="create-local-net-core-app"></a>ローカル .NET Core アプリを作成する

この手順では、ローカル .NET Core プロジェクトを設定します。

### <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

ターミナル ウィンドウから、`cd` コマンドで作業ディレクトリに移動します。

次のコマンドを実行してサンプル リポジトリを複製し、ルートに移動します。

```bash
git clone https://github.com/azure-samples/dotnetcore-sqldb-tutorial
cd dotnetcore-sqldb-tutorial
```

サンプル プロジェクトには、[Entity Framework Core](https://docs.microsoft.com/ef/core/) を使用した基本的な CRUD (作成、読み取り、更新、削除) アプリが含まれています。

### <a name="run-the-application"></a>アプリケーションの実行

次のコマンドを実行して、必要なパッケージをインストールし、データベースの移行を実行し、アプリケーションを起動します。

```bash
dotnet restore
dotnet ef database update
dotnet run
```

ブラウザーで `http://localhost:5000` にアクセスします。 **[新規作成]** リンクを選択し、いくつかの _To Do_ アイテムを作成します。

![SQL Database に正常に接続](./media/tutorial-dotnetcore-sqldb-app/local-app-in-browser.png)

任意のタイミングで .NET Core を停止するには、ターミナルで `Ctrl+C` キーを押します。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-production-sql-database"></a>運用 SQL Database を作成する

この手順では、Azure に SQL Database を作成します。 アプリを Azure にデプロイすると、このクラウド データベースがアプリで使用されます。

SQL Database については、このチュートリアルでは [Azure SQL Database](/azure/sql-database/) を使用します。

### <a name="create-a-resource-group"></a>リソース グループの作成

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>SQL Database 論理サーバーを作成する

Cloud Shell で、[`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create) コマンドを使用して SQL Database 論理サーバーを作成します。

*\<server-name>* プレースホルダーを一意の SQL Database 名で置換します。 この名前は、SQL Database エンドポイント (`<server-name>.database.windows.net`) の一部として使用されるため、名前は Azure のすべての論理サーバーで一意である必要があります。 この名前に含めることができるのは英小文字、数字、およびハイフン (-) 文字のみで、文字数は 3 ～ 50 文字にする必要があります。 また、 *\<db-username>* と *\<db-password>* を、選択したユーザー名とパスワードで置換します。 


```azurecli-interactive
az sql server create --name <server-name> --resource-group myResourceGroup --location "West Europe" --admin-user <db-username> --admin-password <db-password>
```

SQL Database 論理サーバーが作成されると、Azure CLI によって、次の例のような情報が表示されます。

```json
{
  "administratorLogin": "sqladmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<server-name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/<server-name>",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "<server-name>",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
```

### <a name="configure-a-server-firewall-rule"></a>サーバーのファイアウォール規則の構成

[`az sql server firewall create`](/cli/azure/sql/server/firewall-rule?view=azure-cli-latest#az-sql-server-firewall-rule-create)コマンドを使用して、[Azure SQL Database のサーバー レベルのファイアウォール規則](../../sql-database/sql-database-firewall-configure.md)を作成します。 開始 IP と終了 IP の両方が 0.0.0.0 に設定されている場合、ファイアウォールは他の Azure リソースに対してのみ開かれます。 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server-name> --name AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

### <a name="create-a-database"></a>データベースを作成する

[`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-create) コマンドで [S0 パフォーマンス レベル](../../sql-database/sql-database-service-tiers-dtu.md)のデータベースをサーバーに作成します。

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server-name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>接続文字列を作成する

次の文字列を、前に使用した *\<server-name>* 、 *\<db-username>* 、 *\<db-password>* で置換します。

```
Server=tcp:<server-name>.database.windows.net,1433;Database=coreDB;User ID=<db-username>;Password=<db-password>;Encrypt=true;Connection Timeout=30;
```

これは .NET Core アプリの接続文字列です。 後で使用するためコピーします。

## <a name="deploy-app-to-azure"></a>アプリを Azure にデプロイする

この手順では、SQL Database に接続された .NET Core アプリケーションを App Service on Linux にデプロイします。

### <a name="configure-local-git-deployment"></a>ローカル Git デプロイを構成する

[!INCLUDE [Configure a deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service プランを作成する

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Web アプリを作成する

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)] 

### <a name="configure-an-environment-variable"></a>環境変数の構成

Azure アプリの接続文字列を設定するには、Cloud Shell で [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) コマンドを使用します。 次のコマンドで、 *\<app-name>* および *\<connection-string>* パラメーターを、先ほど作成した接続文字列で置換します。

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection='<connection-string>' --connection-string-type SQLServer
```

次に、`ASPNETCORE_ENVIRONMENT` アプリ設定を "_Production_" に設定します。 ローカル開発環境では SQLite を使用し、Azure 環境では SQL Database を使用するため、Azure で実行しているかどうかをこの設定で把握できます。

次の例では、Azure アプリの `ASPNETCORE_ENVIRONMENT` アプリ設定を構成します。 *\<app-name>* プレースホルダーを置換します。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings ASPNETCORE_ENVIRONMENT="Production"
```

### <a name="connect-to-sql-database-in-production"></a>運用環境の SQL Database に接続する

ローカル リポジトリで、Startup.cs を開き、次のコードを検索します。

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

前に構成した環境変数を使用する次のコードで置換します。

```csharp
// Use SQL Database if in Azure, otherwise, use SQLite
if(Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlite("Data Source=MvcMovie.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

このコードは、運用環境 (Azure 環境を示します) で実行されていることを検出すると、SQL Database に接続するように構成した接続文字列を使用します。 App Service でアプリ設定にアクセスする方法については、「[Access environment variables (環境変数にアクセスする)](configure-language-dotnetcore.md#access-environment-variables)」を参照してください。

`Database.Migrate()` 呼び出しは、移行の構成に基づいて .NET Core アプリが必要とするデータベースを自動的に作成するため、Azure で実行するときに役立ちます。

変更を保存し、それを Git リポジトリにコミットします。

```bash
git add .
git commit -m "connect to SQLDB in Azure"
```

### <a name="push-to-azure-from-git"></a>Git から Azure へのプッシュ

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

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
To https://<app-name>.scm.azurewebsites.net/<app-name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-azure-app"></a>Azure アプリを参照する

Web ブラウザーを使用して、デプロイされたアプリを参照します。

```bash
http://<app-name>.azurewebsites.net
```

いくつかの To Do アイテムを追加します。

![App Service on Linux で実行されるアプリ](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

**お疲れさまでした。** App Service on Linux でデータ主導型の .NET Core アプリが実行されています。

## <a name="update-locally-and-redeploy"></a>ローカルで更新して再デプロイする

この手順では、データベース スキーマに変更を加えて Azure に発行します。

### <a name="update-your-data-model"></a>データ モデルを更新する

コード エディターで _Models\Todo.cs_ を開きます。 `ToDo` クラスに次のプロパティを追加します。

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>Code First Migrations をローカルで実行する

いくつかのコマンドを実行して、ローカル データベースを更新します。

```bash
dotnet ef migrations add AddProperty
```

ローカル データベースを更新します。

```bash
dotnet ef database update
```

### <a name="use-the-new-property"></a>新しいプロパティを使用する

`Done` プロパティを使用するために、コードにいくつかの変更を加えます。 このチュートリアルでは、わかりやすくするために `Index` ビューと `Create` ビューのみを変更して、実際のプロパティを確認します。

_Controllers\TodosController.cs_ を開きます。

`Create()` メソッドを探し、`Bind` 属性内のプロパティの一覧に `Done` を追加します。 完了すると、`Create()` メソッドのシグネチャは次のコードのようになります。

```csharp
public async Task<IActionResult> Create([Bind("ID,Description,CreatedDate,Done")] Todo todo)
```

_Views\Todos\Create.cshtml_ を開きます。

Razor コードに、`Description` の `<div class="form-group">` 要素と、`CreatedDate` の別の `<div class="form-group">` 要素が表示されます。 この 2 つの要素の直後に、`Done` の別の `<div class="form-group">` 要素を追加します。

```csharp
<div class="form-group">
    <label asp-for="Done" class="col-md-2 control-label"></label>
    <div class="col-md-10">
        <input asp-for="Done" class="form-control" />
        <span asp-validation-for="Done" class="text-danger"></span>
    </div>
</div>
```

_Views\Todos\Index.cshtml_ を開きます。

空の `<th></th>` 要素を探します。 この要素のすぐ上に、次の Razor コードを追加します。

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

`asp-action` タグ ヘルパーを含む `<td>` 要素を探します。 この要素のすぐ上に、次の Razor コードを追加します。

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

これだけで、`Index` ビューと `Create` ビューの変更を確認できます。

### <a name="test-your-changes-locally"></a>変更をローカルでテストする

アプリをローカルで実行します。

```bash
dotnet run
```

ブラウザーで `http://localhost:5000/` にアクセスします。 これで、To Do 項目を追加し、 **[完了]** チェック ボックスをオンにすることができるようになります。 そうすると、完了済みの項目としてホームページに表示されます。 `Edit` ビューを変更していないため、`Edit` ビューには `Done` フィールドが表示されないことに注意してください。

### <a name="publish-changes-to-azure"></a>Azure に変更を発行する

```bash
git add .
git commit -m "added done field"
git push azure master
```

`git push` が完了したら、Azure アプリに移動し、新機能を試します。

![Code First Migration の手順後の Azure アプリ](./media/tutorial-dotnetcore-sqldb-app/this-one-is-done.png)

既存のすべての To Do 項目がまだ表示されています。 .NET Core アプリを再発行しても、SQL Database の既存のデータは消失しません。 また、Entity Framework Core Migrations によって変更されるのはデータ スキーマのみであり、既存のデータはそのまま残されます。

## <a name="stream-diagnostic-logs"></a>診断ログをストリーミングする

サンプル プロジェクトは既に、[Azure における ASP.NET Core のログ記録](https://docs.microsoft.com/aspnet/core/fundamentals/logging#azure-app-service-provider)に関するページのガイダンスに従っています。ただし、次の 2 つの変更を構成に加えています。

- *DotNetCoreSqlDb.csproj* で `Microsoft.Extensions.Logging.AzureAppServices` への参照を追加しています。
- *Startup.cs* で `loggerFactory.AddAzureWebAppDiagnostics()` を呼び出します。

> [!NOTE]
> プロジェクトのログ レベルは、*appsettings.json* で `Information` に設定されています。
>

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

ASP.NET Core のログのカスタマイズの詳細については、「[ASP.NET Core でのログ記録](https://docs.microsoft.com/aspnet/core/fundamentals/logging)」を参照してください。

## <a name="manage-your-azure-app"></a>Azure アプリを管理する

[Azure portal](https://portal.azure.com) に移動し、お客様が作成したアプリを表示します。

左側のメニューで **[App Services]** をクリックしてから、お客様の Azure アプリの名前をクリックします。

![Azure アプリへのポータル ナビゲーション](./media/tutorial-dotnetcore-sqldb-app/access-portal.png)

既定では、ポータルにはアプリの **[概要]** ページが表示されます。 このページでは、アプリの動作状態を見ることができます。 ここでは、参照、停止、開始、再開、削除のような基本的な管理タスクも行うことができます。 ページの左側にあるタブは、開くことができるさまざまな構成ページを示しています。

![Azure Portal の [App Service] ページ](./media/tutorial-dotnetcore-sqldb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>次の手順

ここで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure で SQL データベースを作成する
> * .NET Core アプリを SQL Database に接続する
> * Azure にアプリケーションをデプロイする
> * データ モデルを更新し、アプリを再デプロイする
> * Azure からターミナルにログをストリーミングする
> * Azure Portal でアプリを管理する

次のチュートリアルに進んで、カスタム DNS 名をアプリにマップする方法を確認してください。

> [!div class="nextstepaction"]
> [チュートリアル:カスタム DNS 名をアプリにマップする](../app-service-web-tutorial-custom-domain.md)

または、他のリソースを参照してください。

> [!div class="nextstepaction"]
> [ASP.NET Core アプリの構成](configure-language-dotnetcore.md)
