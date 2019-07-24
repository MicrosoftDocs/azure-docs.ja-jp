---
title: SQL Database を使用して ASP.NET アプリをビルドする - Azure App Service | Microsoft Docs
description: C# ASP.NET アプリを SQL Server データベースと共に Azure にデプロイする方法について説明します。
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 03c584f1-a93c-4e3d-ac1b-c82b50c75d3e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 59b390776a5e23c08d8793125747742cee4e473e
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487025"
---
# <a name="tutorial-build-an-aspnet-app-in-azure-with-sql-database"></a>チュートリアル:SQL Database を使用して Azure に ASP.NET アプリを作成する

[Azure App Service](overview.md) では、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供しています。 このチュートリアルでは、App Service でデータ主導の ASP.NET アプリをデプロイし、それを [Azure SQL Database](../sql-database/sql-database-technical-overview.md) に接続する方法について説明します。 これが完了すると、ASP.NET アプリは Azure 内で実行され、SQL Database に接続された状態になります。

![Azure App Service に発行された ASP.NET アプリケーション](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure で SQL データベースを作成する
> * ASP.NET アプリを SQL Database に接続する
> * Azure にアプリケーションをデプロイする
> * データ モデルを更新し、アプリを再デプロイする
> * Azure からターミナルにログをストリーミングする
> * Azure Portal でアプリを管理する

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

**ASP.NET および Web 開発**のワークロードと共に、<a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017</a> をインストールする。

既に Visual Studio をインストールしている場合は、**[ツール]** > **[Get Tools and Features]\(ツールと機能の取得\)** の順にクリックして、Visual Studio 内でワークロードを追加します。

## <a name="download-the-sample"></a>サンプルのダウンロード

- [サンプル プロジェクトをダウンロードします](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/archive/master.zip)。
- *dotnet-sqldb-tutorial-master.zip* ファイルを抽出 (解凍) します。

このサンプル プロジェクトには、[Entity Framework Code First](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) を使用した基本的な [ASP.NET MVC](https://www.asp.net/mvc) "作成、読み取り、更新、削除" (CRUD) アプリが含まれています。

### <a name="run-the-app"></a>アプリの実行

Visual Studio で *dotnet-sqldb-tutorial-master/DotNetAppSqlDb.sln* ファイルを開きます。 

デバッグせずにアプリを実行するには、`Ctrl+F5` キーを押します。 アプリが既定のブラウザーに表示されます。 **[新規作成]** リンクを選択し、いくつかの *To Do* アイテムを作成します。 

![[新しい ASP.NET プロジェクト] ダイアログ ボックス](media/app-service-web-tutorial-dotnet-sqldatabase/local-app-in-browser.png)

**[編集]**、**[詳細]**、**[削除]** リンクをテストします。

アプリはデータベース コンテキストを使用してデータベースに接続します。 このサンプルでは、データベース コンテキストは `MyDbConnection` という接続文字列を使用します。 この接続文字列は *Web.config* ファイルで設定され、*Models/MyDatabaseContext.cs* ファイルで参照されます。 この接続文字列名は、このチュートリアルの後半で Azure アプリを Azure SQL Database に接続するために使用します。 

## <a name="publish-to-azure-with-sql-database"></a>SQL Database を使用して Azure に発行する

**ソリューション エクスプローラー**で **DotNetAppSqlDb** プロジェクトを右クリックし、**[発行]** を選択します。

![ソリューション エクスプローラーから発行する](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

**[Microsoft Azure App Service]** が選択されていることを確認し、**[発行]** をクリックします。

![プロジェクトの概要ページから発行する](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-to-app-service.png)

発行すると **[App Service の作成]** ダイアログ ボックスが表示されます。このダイアログ ボックスで、Azure で ASP.NET アプリを実行するために必要なすべての Azure リソースを作成できます。

### <a name="sign-in-to-azure"></a>Azure へのサインイン

**[App Service の作成]** ダイアログ ボックスで、**[アカウントの追加]** をクリックし、Azure サブスクリプションにサインインします。 既に Microsoft アカウントにサインインしている場合は、アカウントが Azure サブスクリプションを保持していることを確認します。 サインインしている Microsoft アカウントが Azure サブスクリプションを備えていない場合は、正しいアカウントをクリックして追加します。 

> [!NOTE]
> 既にサインインしている場合は、まだ **[作成]** を選択しないでください。
>
>
   
![Azure へのサインイン](./media/app-service-web-tutorial-dotnet-sqldatabase/sign-in-azure.png)

### <a name="configure-the-web-app-name"></a>Web アプリ名を構成する

生成された Web アプリ名をそのまま使用するか、別の一意の名前に変更することができます (有効な文字は `a-z`、`0-9`、および `-` です)。 この Web アプリ名は、アプリの既定の URL の一部として使用されます (既定の URL は `<app_name>.azurewebsites.net` で、`<app_name>` が Web アプリ名です)。 この Web アプリ名は、Azure のすべてのアプリで一意である必要があります。 

![[App Service の作成] ダイアログ](media/app-service-web-tutorial-dotnet-sqldatabase/wan.png)

### <a name="create-a-resource-group"></a>リソース グループの作成

[!INCLUDE [resource-group](../../includes/resource-group.md)]

**[リソース グループ]** の横にある **[新規]** をクリックします。

![[リソース グループ] の横にある [新規] をクリックする](media/app-service-web-tutorial-dotnet-sqldatabase/new_rg2.png)

リソース グループに **myResourceGroup** という名前を付けます。

### <a name="create-an-app-service-plan"></a>App Service プランを作成する

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

**[App Service プラン]** の横にある **[新規]** をクリックします。 

**[App Service プランの構成]** ダイアログ ボックスで、新しい App Service プランを次の設定で構成します。

![Create App Service plan](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-app-service-plan.png)

| Setting  | 推奨値 | BLOB の詳細 |
| ----------------- | ------------ | ----|
|**App Service プラン**| myAppServicePlan | [App Service プラン](../app-service/overview-hosting-plans.md) |
|**場所**| 西ヨーロッパ | [Azure リージョン](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) |
|**サイズ**| 無料 | [価格レベル](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)|

### <a name="create-a-sql-server-instance"></a>SQL Server インスタンスを作成する

データベースを作成する前に、[Azure SQL Database 論理サーバー](../sql-database/sql-database-features.md)が必要です。 論理サーバーには、ひとまとめにして管理される一連のデータベースが含まれています。

**[SQL Database の作成]** をクリックします。

![SQL Database の作成](media/app-service-web-tutorial-dotnet-sqldatabase/web-app-name.png)

**[SQL Database の構成]** ダイアログで、**[SQL Server]** の隣にある **[New (新規)]** をクリックします。 

一意のサーバー名が生成されます。 この名前は論理サーバーの既定の URL (`<server_name>.database.windows.net`) の一部として使用されます。 この名前は、Azure のすべての論理サーバー インスタンスで一意である必要があります。 サーバー名は変更できますが、このチュートリアルでは生成された名前をそのまま使用します。

管理者のユーザー名とパスワードを追加します。 パスワードの複雑さの要件については、「[パスワード ポリシー](/sql/relational-databases/security/password-policy)」をご覧ください。

このユーザー名とパスワードを覚えておいてください。 後で、論理サーバー インスタンスを管理するために使用します。

> [!IMPORTANT]
> (Visual Studio および App Service で) 接続文字列のパスワードがマスクされていても、それがどこかに保持されているのは事実であり、アプリの攻撃対象領域が増えることになります。 App Service では、[マネージド サービス ID](overview-managed-identity.md) を使用して、コードやアプリの構成にシークレットを保持する必要性をなくすことで、このリスクを排除できます。 詳細については、「[次のステップ](#next-steps)」を参照してください。

![SQL Server インスタンスを作成する](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-server.png)

Click **OK**. **[SQL Database の構成]** ダイアログはまだ閉じないでください。

### <a name="create-a-sql-database"></a>SQL Database の作成

**[SQL Database の構成]** ダイアログで、次の操作を行います。 

* 生成された既定の **[データベース名]** をそのまま使用します。
* **[接続文字列名]** に「*MyDbConnection*」と入力します。 この名前は、*Models/MyDatabaseContext.cs* で参照されている接続文字列と一致する必要があります。
* **[OK]** を選択します。

![SQL Database を構成する](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database.png)

構成したリソースが **[App Service の作成]** ダイアログに表示されます。 **Create** をクリックしてください。 

![作成したリソース](media/app-service-web-tutorial-dotnet-sqldatabase/app_svc_plan_done.png)

ウィザードで Azure リソースの作成が完了すると、ASP.NET アプリが Azure に発行されます。 既定のブラウザーが、デプロイされたアプリの URL を参照した状態で起動します。 

いくつかの To Do アイテムを追加します。

![Azure アプリに発行された ASP.NET アプリケーション](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

お疲れさまでした。 データ主導の ASP.NET アプリケーションを Azure App Services でライブ実行することができました。

## <a name="access-the-sql-database-locally"></a>SQL データベースにローカルでアクセスする

Visual Studio では、**SQL Server オブジェクト エクスプローラー**を使用して、新しい SQL データベースの確認と管理を簡単に行うことができます。

### <a name="create-a-database-connection"></a>データベース接続を作成する

**[表示]** メニューの **[SQL Server オブジェクト エクスプローラー]** を選択します。

**SQL Server オブジェクト エクスプローラー**の上部で、**[SQL Server の追加]** ボタンをクリックします。

### <a name="configure-the-database-connection"></a>データベース接続を構成する

**[接続]** ダイアログで、**Azure** ノードを展開します。 Azure 上のすべての SQL Database が一覧表示されます。

前に作成した SQL データベースを選択します。 前に作成した接続が、一番下に自動的に入力されます。

前に作成したデータベース管理者のパスワードを入力し、**[接続]** をクリックします。

![Visual Studio からデータベース接続を構成する](./media/app-service-web-tutorial-dotnet-sqldatabase/connect-to-sql-database.png)

### <a name="allow-client-connection-from-your-computer"></a>コンピューターからのクライアント接続を許可する

**[新しいファイアウォール規則の作成]** ダイアログが開かれています。 既定では、SQL データベース インスタンスはお使いの Azure アプリなどの Azure サービスからの接続のみを許可します。 データベースに接続するには、SQL データベース インスタンスでファイアウォール規則を作成します。 ファイアウォール規則で、ローカル コンピューターのパブリック IP アドレスを許可します。

ダイアログには、既にコンピューターのパブリック IP アドレスが入力されています。

**クライアント IP を追加**するための項目が選択されていることを確認し、**[OK]** をクリックします。 

![SQL データベース インスタンスのファイアウォールを設定する](./media/app-service-web-tutorial-dotnet-sqldatabase/sql-set-firewall.png)

Visual Studio で SQL データベース インスタンスのファイアウォール設定の作成が完了すると、**SQL Server オブジェクト エクスプローラー**に接続が表示されます。

ここでは、クエリの実行やビューとストアド プロシージャの作成など、最も一般的なデータベース操作を実行できます。 

接続を展開し、**[データベース]**、 > **[&lt;お使いのデータベース>]**、 > **[テーブル]** の順に選択します。 `Todoes` テーブルを右クリックし、**[データの表示]** を選択します。 

![SQL Database オブジェクトを確認する](./media/app-service-web-tutorial-dotnet-sqldatabase/explore-sql-database.png)

## <a name="update-app-with-code-first-migrations"></a>Code First Migrations を使用してアプリを更新する

Visual Studio の使い慣れたツールを使用して、Azure でデータベースとアプリを更新できます。 この手順では、Entity Framework の Code First Migrations を使用して、データベース スキーマに変更を加え、Azure に発行します。

Entity Framework Code First Migrations の使用方法の詳細については、「[Getting Started with Entity Framework 6 Code First using MVC 5 (MVC 5 を使用した Entity Framework 6 Code First の概要)](https://docs.microsoft.com/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application)」をご覧ください。

### <a name="update-your-data-model"></a>データ モデルを更新する

コード エディターで _Models\Todo.cs_ を開きます。 `ToDo` クラスに次のプロパティを追加します。

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>Code First Migrations をローカルで実行する

いくつかのコマンドを実行して、ローカル データベースを更新します。 

**[ツール]** メニューで、**[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** の順にクリックします。

[パッケージ マネージャー コンソール] ウィンドウで、Code First Migrations を有効にします。

```powershell
Enable-Migrations
```

移行を追加します。

```powershell
Add-Migration AddProperty
```

ローカル データベースを更新します。

```powershell
Update-Database
```

`Ctrl+F5` キーを押してアプリを実行します。 編集、詳細、作成のリンクをテストします。

エラーが発生せずにアプリケーションが読み込まれたら、Code First Migrations は成功です。 ただし、ページはまだ変わっていないように見えます。これは、この新しいプロパティがまだアプリケーション ロジックで使用されていないためです。 

### <a name="use-the-new-property"></a>新しいプロパティを使用する

`Done` プロパティを使用するために、コードにいくつかの変更を加えます。 このチュートリアルでは、わかりやすくするために `Index` ビューと `Create` ビューのみを変更して、実際のプロパティを確認します。

_Controllers\TodosController.cs_ を開きます。

52 行目にある `Create()` メソッドを探し、`Bind` 属性内のプロパティの一覧に `Done` を追加します。 完了すると、`Create()` メソッドのシグネチャは次のコードのようになります。

```csharp
public ActionResult Create([Bind(Include = "Description,CreatedDate,Done")] Todo todo)
```

_Views\Todos\Create.cshtml_ を開きます。

Razor コードでは、`model.Description` を使用する `<div class="form-group">` 要素、`model.CreatedDate` を使用する別の `<div class="form-group">` 要素が表示されます。 これら 2 つの要素の直後に、次のように、`model.Done` を使用する別の `<div class="form-group">` 要素を追加します。

```csharp
<div class="form-group">
    @Html.LabelFor(model => model.Done, htmlAttributes: new { @class = "control-label col-md-2" })
    <div class="col-md-10">
        <div class="checkbox">
            @Html.EditorFor(model => model.Done)
            @Html.ValidationMessageFor(model => model.Done, "", new { @class = "text-danger" })
        </div>
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

`Html.ActionLink()` ヘルパー メソッドを含む `<td>` 要素を探します。 この `<td>` の _上_ に、次の Razor コードで別の `<td>` 要素を追加します。

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

これだけで、`Index` ビューと `Create` ビューの変更を確認できます。 

`Ctrl+F5` キーを押してアプリを実行します。

これで、To Do 項目を追加し、**[完了]** チェック ボックスをオンにすることができるようになります。 そうすると、完了済みの項目としてホームページに表示されます。 `Edit` ビューを変更していないため、`Edit` ビューには `Done` フィールドが表示されないことに注意してください。

### <a name="enable-code-first-migrations-in-azure"></a>Azure で Code First Migrations を有効にする

データベースの移行を含むコードの変更に成功したので、Azure アプリに発行し、SQL Database も Code First Migrations を使用して更新します。

前と同じように、プロジェクトを右クリックし、**[発行]** を選択します。

**[構成]** をクリックして発行設定を開きます。

![発行の設定を開く](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-settings.png)

ウィザードで **[次へ]** をクリックします。

SQL Database の接続文字列が **[MyDatabaseContext (MyDbConnection)]** に入力されていることを確認します。 ドロップダウン リストから **myToDoAppDb** データベースを選択する必要があります。 

**[Code First Migrations を実行する (アプリケーション開始時に実行)]** チェック ボックスをオンにし、**[保存]** をクリックします。

![Azure アプリで Code First Migrations を有効にする](./media/app-service-web-tutorial-dotnet-sqldatabase/enable-migrations.png)

### <a name="publish-your-changes"></a>変更を発行する

Azure アプリで Code First Migrations を有効にしたので、コードの変更を発行します。

発行ページで **[発行]** をクリックします。

再度、To Do 項目を追加してみてください。その後、**[完了]** を選択すると、完了済みの項目としてホームページに表示されます。

![Code First Migration の手順後の Azure アプリ](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

既存のすべての To Do 項目がまだ表示されています。 ASP.NET アプリケーションを再発行しても、SQL データベースの既存のデータは消失しません。 また、Code First Migrations によって変更されるのはデータ スキーマのみであり、既存のデータはそのまま残されます。


## <a name="stream-application-logs"></a>アプリケーション ログをストリーミングする

Azure アプリから Visual Studio に、トレース メッセージを直接ストリーム配信することができます。

_Controllers\TodosController.cs_ を開きます。

各アクションが `Trace.WriteLine()` メソッドで開始されます。 このコードは、Azure アプリにトレース メッセージを追加する方法を示すために追加されています。

### <a name="open-server-explorer"></a>サーバー エクスプローラーを開く

**[表示]** メニューで、**[サーバー エクスプローラー]** を選択します。 **サーバー エクスプローラー**で、Azure アプリのログ記録を構成できます。 

### <a name="enable-log-streaming"></a>ログ ストリーミングを有効にする

**サーバー エクスプローラー**で、**[Azure]** > **[App Service]** の順に展開します。

最初に Azure アプリを作成したときに作成した **myResourceGroup** リソース グループを展開します。

Azure アプリを右クリックし、**[ストリーミング ログの表示]** を選択します。

![ログ ストリーミングを有効にする](./media/app-service-web-tutorial-dotnet-sqldatabase/stream-logs.png)

ログが **[出力]** ウィンドウにストリーミングされるようになりました。 

![出力ウィンドウでのログ ストリーミング](./media/app-service-web-tutorial-dotnet-sqldatabase/log-streaming-pane.png)

しかし、トレース メッセージはまだ表示されません。 最初に **[ストリーミング ログの表示]** を選択する際、Azure アプリによってトレース レベルが `Error` に設定されるためです。このレベルでは、エラー イベントのみが (`Trace.TraceError()` メソッドによって) 記録されます。

### <a name="change-trace-levels"></a>トレース レベルを変更する

トレース レベルを変更して別のトレース メッセージを出力するには、**サーバー エクスプローラー**に戻ります。

Azure アプリを再度右クリックし、**[表示の設定]** を選択します。

**[Application Logging (File System) (アプリケーションのログ記録 (ファイル システム))]** ボックスの一覧の **[詳細]** をクリックします。 **[Save]** をクリックします。

![トレース レベルを [詳細] に変更する](./media/app-service-web-tutorial-dotnet-sqldatabase/trace-level-verbose.png)

> [!TIP]
> 異なるトレース レベルを試しながら、各レベルでどのような種類のメッセージが表示されるかを確認することができます。 たとえば、**[情報]** レベルを指定した場合、`Trace.TraceInformation()`、`Trace.TraceWarning()`、`Trace.TraceError()` で作成されたすべてのログが表示されますが、`Trace.WriteLine()` で作成されたログは除外されます。
>
>

ブラウザーでアプリにもう一度アクセスし (*http://&lt;アプリ名>.azurewebsites.net*)、Azure の To Do リスト アプリケーションの周囲をクリックしてみます。 Visual Studio の **[出力]** ウィンドウにトレース メッセージがストリーミングされます。

```console
Application: 2017-04-06T23:30:41  PID[8132] Verbose     GET /Todos/Index
Application: 2017-04-06T23:30:43  PID[8132] Verbose     GET /Todos/Create
Application: 2017-04-06T23:30:53  PID[8132] Verbose     POST /Todos/Create
Application: 2017-04-06T23:30:54  PID[8132] Verbose     GET /Todos/Index
```



### <a name="stop-log-streaming"></a>ログ ストリーミングを停止する

ログ ストリーミング サービスを停止するには、**[出力]** ウィンドウの **[監視の停止]** ボタンをクリックします。

![ログ ストリーミングを停止する](./media/app-service-web-tutorial-dotnet-sqldatabase/stop-streaming.png)

## <a name="manage-your-azure-app"></a>Azure アプリを管理する

[Azure portal](https://portal.azure.com) に移動し、お客様が作成したアプリを表示します。 



左側のメニューで **[App Service]** をクリックし、Azure アプリの名前をクリックします。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-dotnet-sqldatabase/access-portal.png)

アプリのページが表示されます。 

既定では、ポータルは **[概要]** ページを表示します。 このページでは、アプリの動作状態を見ることができます。 ここでは、参照、停止、開始、再開、削除のような基本的な管理タスクも行うことができます。 ページの左側にあるタブは、開くことができるさまざまな構成ページを示しています。 

![Azure Portal の [App Service] ページ](./media/app-service-web-tutorial-dotnet-sqldatabase/web-app-blade.png)

[!INCLUDE [Clean up section](../../includes/clean-up-section-portal-web-app.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Azure で SQL データベースを作成する
> * ASP.NET アプリを SQL Database に接続する
> * Azure にアプリケーションをデプロイする
> * データ モデルを更新し、アプリを再デプロイする
> * Azure からターミナルにログをストリーミングする
> * Azure Portal でアプリを管理する

次のチュートリアルに進んで、接続 Azure SQL Database のセキュリティを簡単に改善する方法を学んでください。

> [!div class="nextstepaction"]
> [Azure リソースのマネージド ID を使用して SQL データベースに安全にアクセスする](app-service-web-tutorial-connect-msi.md)
