---
title: ASP.NET Core と SQL Database - Azure App Service | Microsoft Docs
description: SQL Database に接続された .NET Core アプリを Azure App Service で動作させる方法について説明します。
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: syntaxc4
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 9a4d4f84626eafdfbc5cc21eef1968a9ed64fcad
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2019
ms.locfileid: "71055617"
---
# <a name="tutorial-build-an-aspnet-core-and-sql-database-app-in-azure-app-service"></a>チュートリアル:Azure App Service での ASP.NET Core および SQL Database アプリの作成

> [!NOTE]
> この記事では、Windows 上の App Service にアプリをデプロイします。 App Service on _Linux_ にデプロイするには、[Azure App Service on Linux での .NET Core および SQL Database のアプリの作成](./containers/tutorial-dotnetcore-sqldb-app.md)に関する記事をご覧ください。
>

[App Service](overview.md) では、Azure の高度にスケーラブルな自己適用型の Web ホスティング サービスを提供しています。 このチュートリアルでは、.NET Core アプリを作成して SQL Database に接続する方法について説明します。 完了すると、.NET Core MVC アプリが App Service で実行されます。

![App Service で実行されるアプリ](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

学習内容は次のとおりです。

> [!div class="checklist"]
> * Azure で SQL データベースを作成する
> * .NET Core アプリを SQL Database に接続する
> * Azure にアプリケーションをデプロイする
> * データ モデルを更新し、アプリを再デプロイする
> * Azure から診断ログをストリーミングする
> * Azure Portal でアプリを管理する

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

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

![SQL Database に正常に接続](./media/app-service-web-tutorial-dotnetcore-sqldb/local-app-in-browser.png)

任意のタイミングで .NET Core を停止するには、ターミナルで `Ctrl+C` キーを押します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-production-sql-database"></a>運用 SQL Database を作成する

この手順では、Azure に SQL Database を作成します。 アプリを Azure にデプロイすると、このクラウド データベースがアプリで使用されます。

SQL Database については、このチュートリアルでは [Azure SQL Database](/azure/sql-database/) を使用します。

### <a name="create-a-resource-group"></a>リソース グループの作成

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>SQL Database 論理サーバーを作成する

Cloud Shell で、[`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create) コマンドを使用して SQL Database 論理サーバーを作成します。

" *\<server_name >* " プレースホルダーを一意の SQL Database 名で置換します。 この名前は、SQL Database エンドポイント (`<server_name>.database.windows.net`) の一部として使用されるため、名前は Azure のすべての論理サーバーで一意である必要があります。 この名前に含めることができるのは英小文字、数字、およびハイフン (-) 文字のみで、文字数は 3 ～ 50 文字にする必要があります。 また、" *\<db_username >* " と "*db_password >\<* " を選択したユーザー名とパスワードで置換します。 


```azurecli-interactive
az sql server create --name <server_name> --resource-group myResourceGroup --location "West Europe" --admin-user <db_username> --admin-password <db_password>
```

SQL Database 論理サーバーが作成されると、Azure CLI によって、次の例のような情報が表示されます。

```json
{
  "administratorLogin": "sqladmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/<server_name>",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "<server_name>",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
```

### <a name="configure-a-server-firewall-rule"></a>サーバーのファイアウォール規則の構成

[`az sql server firewall create`](/cli/azure/sql/server/firewall-rule?view=azure-cli-latest#az-sql-server-firewall-rule-create)コマンドを使用して、[Azure SQL Database のサーバー レベルのファイアウォール規則](../sql-database/sql-database-firewall-configure.md)を作成します。 開始 IP と終了 IP の両方が 0.0.0.0 に設定されている場合、ファイアウォールは他の Azure リソースに対してのみ開かれます。 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server_name> --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> [アプリで使用する送信 IP アドレスのみを使用する](overview-inbound-outbound-ips.md#find-outbound-ips)ことで、ファイアウォール規則による制限をさらに厳しくすることができます。
>

### <a name="create-a-database"></a>データベースを作成する

[`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-create) コマンドで [S0 パフォーマンス レベル](../sql-database/sql-database-service-tiers-dtu.md)のデータベースをサーバーに作成します。

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server_name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>接続文字列を作成する

次の文字列を前に使用した " *\<server_name>* "、" *\<db_username>* "、" *\<db_password>* " で置換します。

```
Server=tcp:<server_name>.database.windows.net,1433;Database=coreDB;User ID=<db_username>;Password=<db_password>;Encrypt=true;Connection Timeout=30;
```

これは .NET Core アプリの接続文字列です。 後で使用するためコピーします。

## <a name="deploy-app-to-azure"></a>アプリを Azure にデプロイする

この手順では、SQL Database に接続された .NET Core アプリケーションを App Service にデプロイします。

### <a name="configure-local-git-deployment"></a>ローカル Git デプロイを構成する

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service プランを作成する

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>Web アプリを作成する

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)] 

### <a name="configure-connection-string"></a>接続文字列を構成する

Azure アプリの接続文字列を設定するには、Cloud Shell で [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) コマンドを使用します。 次のコマンドで、" *\<app name>* " および " *\<connection_string>* " パラメーターを先ほど作成した接続文字列で置換します。

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection="<connection_string>" --connection-string-type SQLServer
```

ASP.NET Core では、*appsettings.json* で指定される接続文字列のように、標準パターンを使用して、この名前付き接続文字列 (`MyDbConnection`) を使用できます。 この場合、`MyDbConnection` は、*appsettings.json* でも定義されています。 App Service で実行する場合、App Service で定義された接続文字列は、*appsettings.json* で定義された接続文字列よりも優先されます。 コードは、ローカル開発中は *appsettings.json* 値を使用し、同じコードがデプロイ時には App Service 値を使用します。

コード内で接続文字列がどのように参照されるかについては、「[運用環境の SQL Database に接続する](#connect-to-sql-database-in-production)」を参照してください。

### <a name="configure-environment-variable"></a>環境変数を構成する

次に、`ASPNETCORE_ENVIRONMENT` アプリ設定を "_Production_" に設定します。 ローカル開発環境では SQLite を使用し、Azure 環境では SQL Database を使用するため、Azure で実行しているかどうかをこの設定で把握できます。

次の例では、Azure アプリの `ASPNETCORE_ENVIRONMENT` アプリ設定を構成します。 " *\<appname>* " プレースホルダーを置換します。

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings ASPNETCORE_ENVIRONMENT="Production"
```

コード内で環境変数がどのように参照されるかについては、「[運用環境の SQL Database に接続する](#connect-to-sql-database-in-production)」を参照してください。

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
            options.UseSqlite("Data Source=localdatabase.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

このコードは、運用環境 (Azure 環境を示します) で実行されていることを検出すると、SQL Database に接続するように構成した接続文字列を使用します。

`Database.Migrate()` 呼び出しは、移行の構成に基づいて .NET Core アプリが必要とするデータベースを自動的に作成するため、Azure で実行するときに役立ちます。 

> [!IMPORTANT]
> スケールアウトする必要がある運用アプリの場合は、「[運用環境で移行を適用する](/aspnet/core/data/ef-rp/migrations#applying-migrations-in-production)」のベスト プラクティスに従ってください。
> 

変更を保存し、それを Git リポジトリにコミットします。 

```bash
git add .
git commit -m "connect to SQLDB in Azure"
```

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

### <a name="browse-to-the-azure-app"></a>Azure アプリを参照する

Web ブラウザーを使用して、デプロイされたアプリを参照します。

```bash
http://<app_name>.azurewebsites.net
```

いくつかの To Do アイテムを追加します。

![App Service で実行されるアプリ](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

**お疲れさまでした。** App Service でデータ主導型の .NET Core アプリが実行されています。

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

`Create([Bind("ID,Description,CreatedDate")] Todo todo)` メソッドを探し、`Bind` 属性内のプロパティの一覧に `Done` を追加します。 完了すると、`Create()` メソッドのシグネチャは次のコードのようになります。

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

`git push` が完了したら、App Service アプリに移動し、To Do 項目を追加してみてから **[完了]** をオンにします。

![Code First Migration の手順後の Azure アプリ](./media/app-service-web-tutorial-dotnetcore-sqldb/this-one-is-done.png)

既存のすべての To Do 項目がまだ表示されています。 .NET Core アプリを再発行しても、SQL Database 内の既存のデータは消失しません。 また、Entity Framework Core Migrations によって変更されるのはデータ スキーマのみであり、既存のデータはそのまま残されます。

## <a name="stream-diagnostic-logs"></a>診断ログをストリーミングする

Azure App Service で ASP.NET Core アプリが稼動している間、コンソールのログをパイプ処理で Cloud Shell に渡すことができます。 このようにすると、アプリケーション エラーのデバッグに役立つ同じ診断メッセージを取得できます。

サンプル プロジェクトは既に、[Azure における ASP.NET Core のログ記録](https://docs.microsoft.com/aspnet/core/fundamentals/logging#azure-app-service-provider)に関するページのガイダンスに従っています。ただし、次の 2 つの変更を構成に加えています。

- *DotNetCoreSqlDb.csproj* で `Microsoft.Extensions.Logging.AzureAppServices` への参照を追加しています。
- *Program.cs* 内の `loggerFactory.AddAzureWebAppDiagnostics()` を呼び出します。

App Service で ASP.NET Core の[ログ レベル](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-level)を、既定のレベルである `Error` から `Information` に設定するには、Cloud Shell から [`az webapp log config`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-config) コマンドを使用します。

```azurecli-interactive
az webapp log config --name <app_name> --resource-group myResourceGroup --application-logging true --level information
```

> [!NOTE]
> プロジェクトのログ レベルは、*appsettings.json* で、あらかじめ `Information` に設定されています。
> 

ログのストリーミングを開始するには、Cloud Shell で [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-tail) コマンドを使用します。

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
```

ログのストリーミングが開始されたら、ブラウザーで Azure アプリを最新の情報に更新して、Web トラフィックを取得します。 ターミナルにパイプされたコンソール ログが表示されます。 コンソール ログがすぐに表示されない場合は、30 秒以内にもう一度確認します。

任意のタイミングでログのストリーミングを停止するには、`Ctrl` + `C` と入力します。

ASP.NET Core のログのカスタマイズの詳細については、「[ASP.NET Core でのログ記録](https://docs.microsoft.com/aspnet/core/fundamentals/logging)」を参照してください。

## <a name="manage-your-azure-app"></a>Azure アプリを管理する

[Azure portal](https://portal.azure.com) に移動し、お客様が作成したアプリを表示します。

左側のメニューで **[App Services]** をクリックしてから、お客様の Azure アプリの名前をクリックします。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-dotnetcore-sqldb/access-portal.png)

既定では、ポータルにはアプリの **[概要]** ページが表示されます。 このページでは、アプリの動作状態を見ることができます。 ここでは、参照、停止、開始、再開、削除のような基本的な管理タスクも行うことができます。 ページの左側にあるタブは、開くことができるさまざまな構成ページを示しています。

![Azure Portal の [App Service] ページ](./media/app-service-web-tutorial-dotnetcore-sqldb/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

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
> [既存のカスタム DNS 名を Azure App Service にマップする](app-service-web-tutorial-custom-domain.md)
