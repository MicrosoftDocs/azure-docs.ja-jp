---
title: "Redis Cache で Web アプリを作成する方法 | Microsoft Docs"
description: "Redis Cache で Web アプリを作成する方法について説明します。"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 454e23d7-a99b-4e6e-8dd7-156451d2da7c
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: hero-article
ms.date: 03/27/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 02e30f7fcbe0782528460b542a75f1d11c7286a1
ms.lasthandoff: 03/28/2017


---
# <a name="how-to-create-a-web-app-with-redis-cache"></a>Redis Cache で Web アプリを作成する方法
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.JS](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

このチュートリアルでは、Visual Studio 2017 を使用して ASP.NET Web アプリケーションを作成し Azure App Service の Web アプリにデプロイする方法を紹介します。 サンプル アプリケーションでは、チームの一連の統計情報をデータベースから取得して表示します。また Azure Redis Cache を使用して、キャッシュにデータを保存したりキャッシュからデータを取得したりするための各種の方法を紹介しています。 チュートリアルの最後には、実際にデータベースの読み取りと書き込みを行う Web アプリが完成します。Web アプリは、Azure Redis Cache を使って最適化され、Azure でホストされます。

学習内容:

* ASP.NET MVC 5 Web アプリケーションを Visual Studio で作成する方法。
* Entity Framework を使用してデータベースのデータにアクセスする方法。
* データの保存と取得に Azure Redis Cache を使うことでデータのスループットを高め、データベースの負荷を軽減する方法。
* Redis ソート済みセットを使用して上位 5 チームを取得する方法。
* Resource Manager テンプレートを使用してアプリケーションの Azure リソースをプロビジョニングする方法。
* Visual Studio を使用してアプリケーションを Azure に発行する方法

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、次の前提条件を満たしている必要があります。

* [Azure アカウント](#azure-account)
* [Visual Studio 2017 と Azure SDK for .NET](#visual-studio-2017-with-the-azure-sdk-for-net)

### <a name="azure-account"></a>Azure アカウント
このチュートリアルを完了するには、Azure アカウントが必要です。 そのための方法は次のとおりです。

* [無料で Azure アカウントを開きます](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero)。 Azure の有料サービスを試用できるクレジットが提供されます。 このクレジットを使い切ってもアカウントは維持されるため、無料の Azure サービスと機能をご利用になれます。
* [Visual Studio サブスクライバーの特典を有効にします](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero)。 MSDN サブスクリプションにより、有料の Azure サービスを利用できるクレジットが毎月与えられます。

### <a name="visual-studio-2017-with-the-azure-sdk-for-net"></a>Visual Studio 2017 と Azure SDK for .NET
このチュートリアルは、Visual Studio 2017 と [Azure SDK for .NET](https://www.visualstudio.com/news/releasenotes/vs2017-relnotes#azuretools) 向けに書かれています。 Azure SDK 2.9.5 は、Visual Studio インストーラーに付属しています。

Visual Studio 2015 を使用している場合は、[Azure SDK for .NET](../dotnet-sdk.md) 2.8.2 以降でチュートリアルを実行できます。 [最新の Azure SDK for Visual Studio 2015 はここからダウンロードしてください](http://go.microsoft.com/fwlink/?linkid=518003)。 Visual Studio をまだ持っていない場合は、SDK と共に自動的にインストールされます。 一部の画面は、このチュートリアルに例示されている画像と異なる場合があります。

Visual Studio 2013 を持っている場合は、 [最新の Azure SDK for Visual Studio 2013 をダウンロード](http://go.microsoft.com/fwlink/?LinkID=324322)できます。 一部の画面は、このチュートリアルに例示されている画像と異なる場合があります。

## <a name="create-the-visual-studio-project"></a>Visual Studio プロジェクトの作成
1. Visual Studio を開き、**[ファイル]**、**[新規作成]**、**[プロジェクト]** の順にクリックします。
2. **[テンプレート]** リストの **[Visual C#]** ノードを展開し、**[クラウド]** を選択して **[ASP.NET Web アプリケーション]** をクリックします。 **[.NET Framework 4.5.2]** 以降が選択されていることを確認します。  **[名前]** ボックスに「**ContosoTeamStats**」と入力し、**[OK]** をクリックします。
   
    ![Create project][cache-create-project]
3. プロジェクトの種類として、 **[MVC]** を選択します。 

    **[認証]** の設定で **[認証なし]** が指定されていることを確認します。 Visual Studio のバージョンによっては、既定値が他の値に設定されている場合があります。 これを変更するには、**[認証の変更]** をクリックし、**[認証なし]** を選択します。

    Visual Studio 2015 で実行している場合は、**[クラウドにホストする]** チェック ボックスをオフにします。 このチュートリアルの以降の手順では、[Azure リソースをプロビジョニング](#provision-the-azure-resources)し、[アプリケーションを Azure に発行](#publish-the-application-to-azure)します。 Visual Studio から **[クラウド内のホスト]** チェック ボックスをオンにした状態で App Service Web アプリをプロビジョニングする例については、 [ASP.NET と Visual Studio を使用した Azure App Service での Web アプリの使用](../app-service-web/web-sites-dotnet-get-started.md)に関する記事を参照してください。
   
    ![Select project template][cache-select-template]
4. **[OK]** をクリックしてプロジェクトを作成します。

## <a name="create-the-aspnet-mvc-application"></a>ASP.NET MVC アプリケーションの作成
このセクションでは、データベースからチームの統計情報を読み取って表示する基本的なアプリケーションを作成します。

* [Entity Framework NuGet パッケージの追加](#add-the-entity-framework-nuget-package)
* [モデルの追加](#add-the-model)
* [コントローラーの追加](#add-the-controller)
* [ビューの構成](#configure-the-views)

### <a name="add-the-entity-framework-nuget-package"></a>Entity Framework NuGet パッケージの追加

1. **[ツール]** メニューで **[NuGet パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。
2. [`Package Manager Console`] ウィンドウで、次のコマンドを実行します。
    
    ```
    Install-Package EntityFramework
    ```

このパッケージの詳細については、[EntityFramework](https://www.nuget.org/packages/EntityFramework/) に関する NuGet ページを参照してください。

### <a name="add-the-model"></a>モデルの追加
1. **ソリューション エクスプローラー**で **Models** フォルダーを右クリックし、**[追加]**、**[クラス]** の順に選択します。 
   
    ![Add model][cache-model-add-class]
2. クラス名に「 `Team` 」と入力し、 **[追加]**をクリックします。
   
    ![Add model class][cache-model-add-class-dialog]
3. `Team.cs` ファイルの先頭にある `using` ステートメントを次の `using` ステートメントに置き換えます。

    ```c#
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Data.Entity.SqlServer;
    ```


1. `Team` クラスの定義を次のコード スニペットに置き換えます。このコード スニペットでは、更新された `Team` クラスの定義に加え、その他の Entity Framework の一部のヘルパー クラスの定義が含まれています。 このチュートリアルで使用されている Entity Framework の Code First 手法の詳細については、「[新しいデータベースの Code First](https://msdn.microsoft.com/data/jj193542)」を参照してください。

    ```c#
    public class Team
    {
        public int ID { get; set; }
        public string Name { get; set; }
        public int Wins { get; set; }
        public int Losses { get; set; }
        public int Ties { get; set; }
    
        static public void PlayGames(IEnumerable<Team> teams)
        {
            // Simple random generation of statistics.
            Random r = new Random();
    
            foreach (var t in teams)
            {
                t.Wins = r.Next(33);
                t.Losses = r.Next(33);
                t.Ties = r.Next(0, 5);
            }
        }
    }
    
    public class TeamContext : DbContext
    {
        public TeamContext()
            : base("TeamContext")
        {
        }
    
        public DbSet<Team> Teams { get; set; }
    }
    
    public class TeamInitializer : CreateDatabaseIfNotExists<TeamContext>
    {
        protected override void Seed(TeamContext context)
        {
            var teams = new List<Team>
            {
                new Team{Name="Adventure Works Cycles"},
                new Team{Name="Alpine Ski House"},
                new Team{Name="Blue Yonder Airlines"},
                new Team{Name="Coho Vineyard"},
                new Team{Name="Contoso, Ltd."},
                new Team{Name="Fabrikam, Inc."},
                new Team{Name="Lucerne Publishing"},
                new Team{Name="Northwind Traders"},
                new Team{Name="Consolidated Messenger"},
                new Team{Name="Fourth Coffee"},
                new Team{Name="Graphic Design Institute"},
                new Team{Name="Nod Publishers"}
            };
    
            Team.PlayGames(teams);
    
            teams.ForEach(t => context.Teams.Add(t));
            context.SaveChanges();
        }
    }
    
    public class TeamConfiguration : DbConfiguration
    {
        public TeamConfiguration()
        {
            SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
        }
    }
    ```


1. **ソリューション エクスプローラー**で、**web.config** をダブルクリックして開きます。
   
    ![web.config][cache-web-config]
2. 次の `connectionStrings` セクションを追加します。 接続文字列の名前は、Entity Framework のデータベース コンテキスト クラス ( `TeamContext`) の名前と一致している必要があります。

    ```xml
    <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\v11.0;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    次の例に示すように、`configSections` の後に続けて、新しい `connectionStrings` セクションを追加できます。

    ```xml
    <configuration>
      <configSections>
        <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
      </configSections>
      <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\v11.0;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
      </connectionStrings>
      ...
      ```

### <a name="add-the-controller"></a>コントローラーの追加
1. **F6** キーを押して、プロジェクトをビルドします。 
2. **ソリューション エクスプローラー**で **Controllers** フォルダーを右クリックし、**[追加]**、**[コントローラー]** の順に選択します。
   
    ![コントローラーの追加][cache-add-controller]
3. **[Entity Framework を使用した、ビューがある MVC 5 コントローラー]** を選択し、**[追加]** をクリックします。 **[追加]**をクリックした後にエラーが発生する場合は、先にプロジェクトをビルド済みであることを確認してください。
   
    ![Add controller class][cache-add-controller-class]
4. **[モデル クラス]** ボックスの一覧から **[Team (ContosoTeamStats.Models)]** を選択します。 **[データ コンテキスト クラス]** ボックスの一覧から **[TeamContext (ContosoTeamStats.Models)]** を選択します。 **[コントローラー名]** ボックスに「`TeamsController`」と入力します (自動的に入力されなかった場合)。 **[追加]** をクリックしてコントローラー クラスを作成し、既定のビューを追加します。
   
    ![Configure controller][cache-configure-controller]
5. **ソリューション エクスプローラー**で **Global.asax** を展開し、**Global.asax.cs** をダブルクリックして開きます。
   
    ![Global.asax.cs][cache-global-asax]
6. ファイルの上部に次の 2 つの `using` ステートメントを、他の `using` ステートメントに続けて追加します。

    ```c#
    using System.Data.Entity;
    using ContosoTeamStats.Models;
    ```


1. `Application_Start` メソッドの最後に次のコード行を追加します。

    ```c#
    Database.SetInitializer<TeamContext>(new TeamInitializer());
    ```


1. **ソリューション エクスプローラー**で `App_Start` を展開し、`RouteConfig.cs` をダブルクリックします。
   
    ![RouteConfig.cs][cache-RouteConfig-cs]
2. `RegisterRoutes` メソッドに含まれる次のコードの `controller = "Home"` を、次の例に示すように `controller = "Teams"` に置き換えます。

    ```c#
    routes.MapRoute(
        name: "Default",
        url: "{controller}/{action}/{id}",
        defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
    );
    ```


### <a name="configure-the-views"></a>ビューの構成
1. **ソリューション エクスプローラー**で **Views** フォルダー、**Shared** フォルダーを順に展開し、**_Layout.cshtml** をダブルクリックします。 
   
    ![_Layout.cshtml][cache-layout-cshtml]
2. 次の例のように `title` 要素の内容を変更し、`My ASP.NET Application` を `Contoso Team Stats` に置き換えます。

    ```html
    <title>@ViewBag.Title - Contoso Team Stats</title>
    ```


1. `body` セクションで、最初の `Html.ActionLink` ステートメントを更新します。`Application name` は `Contoso Team Stats` に、`Home` は `Teams` に置き換えてください。
   
   * 変更前: `@Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })`
   * 変更後: `@Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`
     
     ![コードの変更][cache-layout-cshtml-code]
2. **Ctrl + F5** キーを押してアプリケーションをビルドし、実行します。 このバージョンのアプリケーションは、データベースから直接結果を読み取ります。 **[Entity Framework を使用した、ビューがある MVC 5 コントローラー]** スキャフォールディングによってアプリケーションに自動的に追加された **[新規作成]**、**[編集]**、**[詳細]**、**[削除]** の各アクションに注目してください。 次のセクションでは、Redis Cache を追加してデータへのアクセスを効率化し、アプリケーションに機能を追加します。

![Starter application][cache-starter-application]

## <a name="configure-the-application-to-use-redis-cache"></a>Redis Cache を使用するようにアプリケーションを構成する
このセクションでは、 [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) キャッシュ クライアントを使用して、Azure Redis Cache インスタンスで Contoso チームの統計情報を格納したり、取得したりするようにサンプル アプリケーションを構成します。

* [StackExchange.Redis を使用するようにアプリケーションを構成する](#configure-the-application-to-use-stackexchangeredis)
* [キャッシュまたはデータベースから結果を返すように TeamsController クラスを更新する](#update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database)
* [キャッシュと連動するように Create、Edit、Delete の各メソッドを更新する](#update-the-create-edit-and-delete-methods-to-work-with-the-cache)
* [キャッシュと連動するように Teams Index ビューを更新する](#update-the-teams-index-view-to-work-with-the-cache)

### <a name="configure-the-application-to-use-stackexchangeredis"></a>StackExchange.Redis を使用するようにアプリケーションを構成する
1. Visual Studio で StackExchange.Redis NuGet パッケージを使用してクライアント アプリケーションを構成するには、**[ツール]** メニューで **[NuGet パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。
2. [`Package Manager Console`] ウィンドウで、次のコマンドを実行します。
    
    ```
    Install-Package StackExchange.Redis
    ```
   
    クライアント アプリケーションから StackExchange.Redis Cache クライアントを使用して Azure Redis Cache にアクセスするために必要なアセンブリ参照が NuGet パッケージによってダウンロードされ追加されます。 `StackExchange.Redis` クライアント ライブラリの厳密な名前を持つバージョンを使用する場合は、`StackExchange.Redis.StrongName` パッケージをインストールします。
3. **ソリューション エクスプローラー**で **Controllers** フォルダーを展開し、**TeamsController.cs** をダブルクリックして開きます。
   
    ![Teams controller][cache-teamscontroller]
4. 次の 2 つの `using` ステートメントを **TeamsController.cs** に追加します。

    ```c#   
    using System.Configuration;
    using StackExchange.Redis;
    ```

5. 次の 2 つのプロパティを `TeamsController` クラスに追加します。

    ```c#   
    // Redis Connection string info
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
        return ConnectionMultiplexer.Connect(cacheConnection);
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

6. コンピューター上に `WebAppPlusCacheAppSecrets.config` という名前のファイルを作成し、サンプル アプリケーションのソース コードでチェックインされない場所に配置します (どこかにチェックインすることを決めた場合)。 この例では、`AppSettingsSecrets.config` ファイルを `C:\AppSecrets\WebAppPlusCacheAppSecrets.config` に配置しています。
   
    `WebAppPlusCacheAppSecrets.config` ファイルを編集し、次の内容を追加します。 この情報は、アプリケーションをローカルで実行する場合に、Azure Redis Cache インスタンスへの接続に使用されます。 このチュートリアルの後半で、Azure Redis Cache インスタンスをプロビジョニングし、キャッシュの名前とパスワードを更新します。 Azure にデプロイした場合、キャッシュ接続情報は、このファイルからではなく Web アプリのアプリケーション設定から取得されます。したがってサンプル アプリケーションをローカルで実行することを予定していない場合は、このファイルの作成およびこのファイルを参照する後続の手順を省略してかまいません。 `WebAppPlusCacheAppSecrets.config` は、アプリケーションと一緒に Azure にデプロイするものではないため、アプリケーションをローカルで実行する場合以外は必要ありません。

    ```xml
    <appSettings>
      <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
    </appSettings>
    ```


1. **ソリューション エクスプローラー**で、**web.config** をダブルクリックして開きます。
   
    ![web.config][cache-web-config]
2. 次の `file` 属性を `appSettings` 要素に追加します。 異なるファイル名または場所を使用した場合は、この例の値を実際の値で置き換えてください。
   
   * 変更前: `<appSettings>`
   * 変更後: ` <appSettings file="C:\AppSecrets\WebAppPlusCacheAppSecrets.config">`
     
   `<appSettings>` 要素内のマークアップは、ASP.NET ランタイムによって外部ファイルの内容と結合されます。 指定したファイルが見つからない場合、このファイル属性は無視されます。 このアプリケーションのソース コードにシークレット (キャッシュへの接続文字列) は含まれていません。 Web アプリを Azure にデプロイするときに、`WebAppPlusCacheAppSecrests.config` ファイルはデプロイされません (それが目的です)。 Azure には、これらのシークレットを指定する方法がいくつかあります。このチュートリアルでは、以降の手順で [Azure リソースをプロビジョニング](#provision-the-azure-resources)するときに自動的にシークレットが構成されます。 Azure におけるシークレットの扱いの詳細については、「[Best practices for deploying passwords and other sensitive data to ASP.NET and Azure App Service (ASP.NET および Azure App Service にパスワードや機密データをデプロイするためのベスト プラクティス)](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure)」を参照してください。

### <a name="update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database"></a>キャッシュまたはデータベースから結果を返すように TeamsController クラスを更新する
このサンプルでは、データベースまたはキャッシュから、チームの統計情報を取得できます。 チームの統計情報は、シリアル化された `List<Team>`としてキャッシュに格納されるほか、Redis のデータ型を使用してソート済みセットとして格納されます。 ソート済みセットから項目を取得するときは、一部または全部の項目を対象にできるほか、特定の項目を照会することもできます。 このサンプルでは、勝利数のランキングで上位 5 チームをソート済みセットに対して照会します。

> [!NOTE]
> Azure Redis Cache を使用するためにチームの統計情報を複数の形式でキャッシュに格納する必要はありません。 このチュートリアルでは、データをキャッシュする際に使用できる各種の方法とデータ型を例示するために、複数の形式を使用しています。
> 
> 

1. `TeamsController.cs` ファイルの他の `using` ステートメントの先頭に、次の `using` ステートメントを追加します。

    ```c#   
    using System.Diagnostics;
    using Newtonsoft.Json;
    ```

2. 現在の `public ActionResult Index()` メソッドの実装を次の実装に置き換えます。

    ```c#
    // GET: Teams
    public ActionResult Index(string actionType, string resultType)
    {
        List<Team> teams = null;

        switch(actionType)
        {
            case "playGames": // Play a new season of games.
                PlayGames();
                break;

            case "clearCache": // Clear the results from the cache.
                ClearCachedTeams();
                break;

            case "rebuildDB": // Rebuild the database with sample data.
                RebuildDB();
                break;
        }

        // Measure the time it takes to retrieve the results.
        Stopwatch sw = Stopwatch.StartNew();

        switch(resultType)
        {
            case "teamsSortedSet": // Retrieve teams from sorted set.
                teams = GetFromSortedSet();
                break;

            case "teamsSortedSetTop5": // Retrieve the top 5 teams from the sorted set.
                teams = GetFromSortedSetTop5();
                break;

            case "teamsList": // Retrieve teams from the cached List<Team>.
                teams = GetFromList();
                break;

            case "fromDB": // Retrieve results from the database.
            default:
                teams = GetFromDB();
                break;
        }

        sw.Stop();
        double ms = sw.ElapsedTicks / (Stopwatch.Frequency / (1000.0));

        // Add the elapsed time of the operation to the ViewBag.msg.
        ViewBag.msg += " MS: " + ms.ToString();

        return View(teams);
    }
    ```


1. 以下の 3 つのメソッドを `TeamsController` クラスに追加して、前のコード スニペットで追加した switch ステートメントにある 3 種類のアクション (`playGames`、`clearCache`、`rebuildDB`) を実装します。
   
    `PlayGames` メソッドは、あるシーズンのゲームをシミュレートすることでチームの統計情報を更新し、結果をデータベースに保存して、古くなったデータをキャッシュから消去します。

    ```c#
    void PlayGames()
    {
        ViewBag.msg += "Updating team statistics. ";
        // Play a "season" of games.
        var teams = from t in db.Teams
                    select t;

        Team.PlayGames(teams);

        db.SaveChanges();

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    `RebuildDB` メソッドは、既定で存在する一連のチームでデータベースを再初期化してその統計情報を生成し、古くなったデータをキャッシュから消去します。

    ```c#
    void RebuildDB()
    {
        ViewBag.msg += "Rebuilding DB. ";
        // Delete and re-initialize the database with sample data.
        db.Database.Delete();
        db.Database.Initialize(true);

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    `ClearCachedTeams` メソッドは、キャッシュされているチームの統計情報をキャッシュから削除します。

    ```c#
    void ClearCachedTeams()
    {
        IDatabase cache = Connection.GetDatabase();
        cache.KeyDelete("teamsList");
        cache.KeyDelete("teamsSortedSet");
        ViewBag.msg += "Team data removed from cache. ";
    } 
    ```


1. キャッシュやデータベースからチームの統計情報を取得する各種の方法を実装するために、以下の 4 つのメソッドを `TeamsController` クラスに追加します。 いずれのメソッドも戻り値は `List<Team>` で、それがビューに表示されます。
   
    `GetFromDB` メソッドは、データベースからチームの統計情報を読み取ります。
   
    ```c#
    List<Team> GetFromDB()
    {
        ViewBag.msg += "Results read from DB. ";
        var results = from t in db.Teams
            orderby t.Wins descending
            select t; 

        return results.ToList<Team>();
    }
    ```

    `GetFromList` メソッドは、シリアル化された `List<Team>` としてチームの統計情報をキャッシュから読み取ります。 キャッシュ ミスが発生した場合は、データベースからチームの統計情報を読み取り、次回使用できるようキャッシュに格納します。 このサンプルでキャッシュとの間でやり取りされる .NET オブジェクトのシリアル化には、JSON.NET シリアル化を使用しています。 詳細については、 [Azure Redis Cache における .NET オブジェクトの使用方法](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache)に関するセクションを参照してください。

    ```c#
    List<Team> GetFromList()
    {
        List<Team> teams = null;

        IDatabase cache = Connection.GetDatabase();
        string serializedTeams = cache.StringGet("teamsList");
        if (!String.IsNullOrEmpty(serializedTeams))
        {
            teams = JsonConvert.DeserializeObject<List<Team>>(serializedTeams);

            ViewBag.msg += "List read from cache. ";
        }
        else
        {
            ViewBag.msg += "Teams list cache miss. ";
            // Get from database and store in cache
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            cache.StringSet("teamsList", JsonConvert.SerializeObject(teams));
        }
        return teams;
    }
    ```

    `GetFromSortedSet` メソッドは、キャッシュされたソート済みセットからチームの統計情報を読み取ります。 キャッシュ ミスが発生した場合は、データベースからチームの統計情報を読み取り、ソート済みセットとしてキャッシュに格納します。

    ```c#
    List<Team> GetFromSortedSet()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();
        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", order: Order.Descending);
        if (teamsSortedSet.Count() > 0)
        {
            ViewBag.msg += "Reading sorted set from cache. ";
            teams = new List<Team>();
            foreach (var t in teamsSortedSet)
            {
                Team tt = JsonConvert.DeserializeObject<Team>(t.Element);
                teams.Add(tt);
            }
        }
        else
        {
            ViewBag.msg += "Teams sorted set cache miss. ";

            // Read from DB
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            foreach (var t in teams)
            {
                Console.WriteLine("Adding to sorted set: {0} - {1}", t.Name, t.Wins);
                cache.SortedSetAdd("teamsSortedSet", JsonConvert.SerializeObject(t), t.Wins);
            }
        }
        return teams;
    }
    ```

    `GetFromSortedSetTop5` メソッドは、キャッシュされたソート済みセットから上位 5 チームを読み取ります。 最初に、 `teamsSortedSet` キーがキャッシュに存在するかどうかをチェックします。 このキーが存在しない場合は、 `GetFromSortedSet` メソッドが呼び出され、チームの統計情報を読み取り、キャッシュに格納します。 次に、キャッシュされたソート済みセットから上位 5 チームを照会して返します。

    ```c#
    List<Team> GetFromSortedSetTop5()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();

        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        if(teamsSortedSet.Count() == 0)
        {
            // Load the entire sorted set into the cache.
            GetFromSortedSet();

            // Retrieve the top 5 teams.
            teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        }

        ViewBag.msg += "Retrieving top 5 teams from cache. ";
        // Get the top 5 teams from the sorted set
        teams = new List<Team>();
        foreach (var team in teamsSortedSet)
        {
            teams.Add(JsonConvert.DeserializeObject<Team>(team.Element));
        }
        return teams;
    }
    ```

### <a name="update-the-create-edit-and-delete-methods-to-work-with-the-cache"></a>キャッシュと連動するように Create、Edit、Delete の各メソッドを更新する
このサンプルの過程で生成されたスキャフォールディング コードには、チームの追加、編集、削除を行うメソッドが含まれています。 キャッシュ内のデータは、チームが追加、編集、削除されるたびに古くなります。 このセクションでは、キャッシュされたチームを消去することによってキャッシュとデータベースの同期状態を維持するように、この 3 つのメソッドに変更を加えます。

1. `TeamsController` クラスの `Create(Team team)` メソッドに移動します。 次の例のように、 `ClearCachedTeams` メソッドの呼び出しを追加します。

    ```c#
    // POST: Teams/Create
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Create([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Teams.Add(team);
            db.SaveChanges();
            // When a team is added, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }

        return View(team);
    }
    ```


1. `TeamsController` クラスの `Edit(Team team)` メソッドに移動します。 次の例のように、 `ClearCachedTeams` メソッドの呼び出しを追加します。

    ```c#
    // POST: Teams/Edit/5
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Edit([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Entry(team).State = EntityState.Modified;
            db.SaveChanges();
            // When a team is edited, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }
        return View(team);
    }
    ```


1. `TeamsController` クラスの `DeleteConfirmed(int id)` メソッドに移動します。 次の例のように、 `ClearCachedTeams` メソッドの呼び出しを追加します。

    ```c#
    // POST: Teams/Delete/5
    [HttpPost, ActionName("Delete")]
    [ValidateAntiForgeryToken]
    public ActionResult DeleteConfirmed(int id)
    {
        Team team = db.Teams.Find(id);
        db.Teams.Remove(team);
        db.SaveChanges();
        // When a team is deleted, the cache is out of date.
        // Clear the cached teams.
        ClearCachedTeams();
        return RedirectToAction("Index");
    }
    ```


### <a name="update-the-teams-index-view-to-work-with-the-cache"></a>キャッシュと連動するように Teams Index ビューを更新する
1. **ソリューション エクスプローラー**で、**Views** フォルダー、**Teams** フォルダーの順に展開し、**Index.cshtml** をダブルクリックします。
   
    ![Index.cshtml][cache-views-teams-index-cshtml]
2. ファイルの上部付近から次の段落要素を探します。
   
    ![Action table][cache-teams-index-table]
   
    これは、新しいチームを作成するためのリンクです。 段落要素を以下の表に置き換えます。 この表には、新しいチームの作成、新しいシーズンのゲーム プレイ、キャッシュの消去、各種形式でのキャッシュからのチーム取得、データベースからのチームの取得、新しいサンプル データでのデータベースの再構築を行うためのアクション リンクが含まれています。

    ```html
    <table class="table">
        <tr>
            <td>
                @Html.ActionLink("Create New", "Create")
            </td>
            <td>
                @Html.ActionLink("Play Season", "Index", new { actionType = "playGames" })
            </td>
            <td>
                @Html.ActionLink("Clear Cache", "Index", new { actionType = "clearCache" })
            </td>
            <td>
                @Html.ActionLink("List from Cache", "Index", new { resultType = "teamsList" })
            </td>
            <td>
                @Html.ActionLink("Sorted Set from Cache", "Index", new { resultType = "teamsSortedSet" })
            </td>
            <td>
                @Html.ActionLink("Top 5 Teams from Cache", "Index", new { resultType = "teamsSortedSetTop5" })
            </td>
            <td>
                @Html.ActionLink("Load from DB", "Index", new { resultType = "fromDB" })
            </td>
            <td>
                @Html.ActionLink("Rebuild DB", "Index", new { actionType = "rebuildDB" })
            </td>
        </tr>    
    </table>
    ```


1. **Index.cshtml** ファイルの一番下までスクロールし、次の `tr` 要素を追加します。これが、ファイル内の最後のテーブルの最終行となります。
   
    ```html
    <tr><td colspan="5">@ViewBag.Msg</td></tr>
    ```
   
    この行は、`ViewBag.Msg` の値を表示します。これには、現在の操作に関する状態レポートが含まれています。 `ViewBag.Msg` は、前の手順のいずれかのアクション リンクをクリックしたときに設定されます。   
   
    ![Status message][cache-status-message]
2. **F6** キーを押して、プロジェクトをビルドします。

## <a name="provision-the-azure-resources"></a>Azure リソースのプロビジョニング
Azure でアプリケーションをホストするにはまず、アプリケーションに必要な Azure サービスをプロビジョニングする必要があります。 このチュートリアルのサンプル アプリケーションでは、次の Azure サービスを使用します。

* Azure Redis Cache
* App Service Web Apps
* SQL Database

新しいリソース グループまたは既存のリソース グループにこれらのサービスをデプロイするには、次の **[Deploy to Azure (Azure へのデプロイ)]** ボタンをクリックします。

[![Deploy to Azure (Azure へのデプロイ)][deploybutton]](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-redis-cache-sql-database%2Fazuredeploy.json)

この **[Deploy to Azure (Azure へのデプロイ)]** ボタンは、[Web アプリと Redis Cache と SQL Database を作成する](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) [Azure クイック スタート](https://github.com/Azure/azure-quickstart-templates) テンプレートを使用して、これらのサービスをプロビジョニングし、SQL Database の接続文字列を設定して、Azure Redis Cache 接続文字列のアプリケーション設定を行います。

> [!NOTE]
> Azure アカウントがない場合は、数分で [無料の Azure アカウントを作成](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero) できます。
> 
> 

**[Deploy to Azure (Azure へのデプロイ)]** ボタンをクリックすると、Azure ポータルが開き、テンプレートによって記述されたリソースの作成プロセスが開始されます。

![Azure へのデプロイ][cache-deploy-to-azure-step-1]

1. **[基本]** セクションで、使用する Azure サブスクリプションを選択し、既存のリソース グループを選択するか新しいリソース グループを作成して、リソース グループの場所を指定します。
2. **[設定]** セクションで、**管理者のログイン** (**admin** は使用しないでください)、**管理者のログイン パスワード**、**データベース名**を指定します。 その他のパラメーターは、Free App Service ホスティング プランを使用するように構成し、Free レベルに付属しない SQL Database と Azure Redis Cache については、コストを低く抑えるように設定しています。

    ![Azure へのデプロイ][cache-deploy-to-azure-step-2]

3. 目的の設定を構成した後、ページの末尾までスクロールし、使用条件を読んで、**[上記の使用条件に同意する]** チェック ボックスをオンにします。
4. リソースのプロビジョニングを開始するには、**[購入]** をクリックします。

デプロイの進行状況を表示するには、通知アイコンをクリックし、 **[デプロイが開始されました]**をクリックします。

![[デプロイが開始されました]][cache-deployment-started]

デプロイのステータスは、 **[Microsoft.Template]** ブレードで確認できます。

![[Deploy to Azure (Azure へのデプロイ)]][cache-deploy-to-azure-step-3]

プロビジョニングが完了したら、Visual Studio から Azure にアプリケーションを発行できます。

> [!NOTE]
> プロビジョニング処理中になんらかのエラーが発生した場合は、 **[Microsoft.Template]** ブレードに表示されます。 一般に、サブスクリプションあたりの Free App Service ホスティング プランが多すぎたり SQL Server が多すぎたりすることが原因でエラーが発生します。 エラーを解決し、**[Microsoft.Template]** ブレードの **[再デプロイ]** またはこのチュートリアルの **[Deploy to Azure (Azure へのデプロイ)]** ボタンをクリックして処理を再開してください。
> 
> 

## <a name="publish-the-application-to-azure"></a>Azure へのアプリケーションの発行
この手順ではアプリケーションを Azure に発行し、クラウドで実行します。

1. Visual Studio で **ContosoTeamStats** プロジェクトを右クリックし、**[発行]** を選択します。
   
    ![[発行]][cache-publish-app]
2. **[Microsoft Azure App Service]** をクリックし、**[既存のものを選択]** を選択して、**[発行]** をクリックします。
   
    ![[発行]][cache-publish-to-app-service]
3. Azure リソースを作成するときに使用したサブスクリプションを選択し、そのリソースが含まれているリソース グループを展開して、目的の Web アプリを選択します。 **[Deploy to Azure (Azure へのデプロイ)]** ボタンを使用した場合、Web アプリの名前は **webSite** から始まります。
   
    ![Select Web App][cache-select-web-app]
4. **[OK]** をクリックして発行プロセスを開始します。 しばらくすると発行プロセスが完了し、ブラウザーが起動してサンプル アプリケーションが実行されます。 検証時または発行時に DNS エラーが発生した場合で、かつアプリケーションに必要な Azure リソースのプロビジョニング プロセスが完了した直後である場合は、しばらく待ってからもう一度実行してください。
   
    ![Cache added][cache-added-to-application]

次の表は、サンプル アプリケーションの各アクション リンクとその説明を一覧にしたものです。

| アクション | 説明 |
| --- | --- |
| 新規作成 |新しいチームを作成します。 |
| Play Season |ゲームのシーズンを再生し、チームの統計情報を更新して、キャッシュに格納されている古いチーム データがあれば消去します。 |
| Clear Cache |チームの統計情報をキャッシュから消去します。 |
| List from Cache |チームの統計情報をキャッシュから取得します。 キャッシュ ミスが発生した場合は、統計情報をデータベースから読み込んで、次回使用できるようキャッシュに保存します。 |
| Sorted Set from Cache |ソート済みセットを使用してキャッシュからチームの統計情報を取得します。 キャッシュ ミスが発生した場合は、統計情報をデータベースから読み込み、ソート済みセットを使用してキャッシュに保存します。 |
| Top 5 Teams from Cache |ソート済みセットを使用してキャッシュから上位 5 チームを取得します。 キャッシュ ミスが発生した場合は、統計情報をデータベースから読み込み、ソート済みセットを使用してキャッシュに保存します。 |
| Load from DB |チームの統計情報をデータベースから取得します。 |
| Rebuild DB |データベースを再構築し、サンプル チーム データを再度読み込みます。 |
| Edit / Details / Delete |チームの編集、詳細表示、削除を実行します。 |

いくつかのアクションをクリックし、各種のソースからデータを取得してみてください。 それぞれの方法で、データベースやキャッシュからデータを取得するのにかかる時間の違いをよく観察してください。

## <a name="delete-the-resources-when-you-are-finished-with-the-application"></a>アプリケーションを使い終わったときにリソースを削除する
チュートリアルのサンプル アプリケーションを使い終わったら、コストとリソースを節約するために Azure リソースを削除しましょう。 「 **Azure リソースのプロビジョニング** 」セクションの [[Deploy to Azure (Azure へのデプロイ)]](#provision-the-azure-resources) ボタンを使用し、すべてのリソースが同じリソース グループに属している場合は、リソース グループを削除することにより、それらを 1 回の操作でまとめて削除できます。

1. [Azure ポータル](https://portal.azure.com) にサインインし、 **[リソース グループ]**をクリックします。
2. リソース グループの名前を **[フィルター項目]** ボックスに入力します。
3. リソース グループの右側にある **[...]** をクリックします。
4. [ **削除**] をクリックします。
   
    ![削除][cache-delete-resource-group]
5. リソース グループの名前を入力して **[削除]**をクリックします。
   
    ![削除の確定][cache-delete-confirm]

しばらくすると、リソース グループとそこに含まれているすべてのリソースが削除されます。

> [!IMPORTANT]
> いったん削除したリソース グループを元に戻すことはできません。リソース グループとそこに存在するすべてのリソースは完全に削除されるので注意が必要です。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。 このサンプルのホストとなるリソースを既存のリソース グループ内に作成した場合、個々のブレードから個別に各リソースを削除することができます。
> 
> 

## <a name="run-the-sample-application-on-your-local-machine"></a>サンプル アプリケーションをローカル コンピューターで実行する
アプリケーションをローカル コンピューターから実行するには、データのキャッシュ先となる Azure Redis Cache インスタンスが必要となります。 

* 前セクションで説明した手順に従ってアプリケーションを Azure に発行した場合は、その過程でプロビジョニングした Azure Redis Cache インスタンスを使用できます。
* 既存の Azure Redis Cache インスタンスが他にも存在する場合、そのインスタンスを使用してこのサンプルをローカルで実行できます。
* Azure Redis Cache インスタンスを作成する必要がある場合は、「 [キャッシュの作成](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)」の手順に従ってください。

使用するキャッシュを選択または作成したら、Azure Portal でそのキャッシュの保存先を参照し、キャッシュの[ホスト名](cache-configure.md#properties)と[アクセス キー](cache-configure.md#access-keys)を取得します。 手順については、「 [Redis Cache の設定の構成](cache-configure.md#configure-redis-cache-settings)」を参照してください。

1. このチュートリアルの「[Redis Cache を使用するようにアプリケーションを構成する](#configure-the-application-to-use-redis-cache)」の手順で作成した `WebAppPlusCacheAppSecrets.config` ファイルを任意のエディターで開きます。
2. `value` 属性の `MyCache.redis.cache.windows.net` を実際のキャッシュの[ホスト名](cache-configure.md#properties)に置き換え、そのキャッシュの[プライマリ キーまたはセカンダリ キー](cache-configure.md#access-keys)をパスワードとして指定します。

    ```xml
    <appSettings>
      <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
    </appSettings>
    ```


1. **Ctrl キーを押しながら F5 キーを押して** アプリケーションを実行します。

> [!NOTE]
> アプリケーション (データベースを含む) がローカルで実行されているのに対し、Redis Cache は Azure でホストされているので、キャッシュの方がデータベースよりもパフォーマンスが低いように見える場合があります。 パフォーマンスを最大限に引き出すには、クライアント アプリケーションと Azure Redis Cache インスタンスを同じ場所に置いてください。 
> 
> 

## <a name="next-steps"></a>次のステップ
* [ASP.NET](http://asp.net/) サイトの「[Getting Started with ASP.NET MVC 5 (ASP.NET MVC 5 の基本)](http://www.asp.net/mvc/overview/getting-started/introduction/getting-started)」でさらに学習します。
* App Service で ASP.NET Web アプリを作成するその他の例については、[HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect の[デモ](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/)の「[Create and deploy an ASP.NET web app in Azure App Service (Azure App Service で ASP.NET Web アプリを作成およびデプロイする)](https://github.com/Microsoft/HealthClinic.biz/wiki/Create-and-deploy-an-ASP.NET-web-app-in-Azure-App-Service)」を参照してください。
  * HealthClinic.biz のデモに関連する他のクイック スタートについては、「 [Azure Developer Tools Quickstarts (Azure 開発者ツールのクイック スタート)](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts)」を参照してください。
* このチュートリアルで使用されている Entity Framework の Code First 手法について、「 [新しいデータベースの Code First](https://msdn.microsoft.com/data/jj193542) 」を参照して学習します。
* [Azure App Service の Web アプリ](../app-service-web/app-service-web-overview.md)について学習します。
* Azure ポータルでキャッシュを [監視](cache-how-to-monitor.md) する方法について学習します。
* Azure Redis Cache Premium の機能について詳しく調べます。
  
  * [How to configure persistence for a Premium Azure Redis Cache (Premium Azure Redis Cache の永続性の構成方法)](cache-how-to-premium-persistence.md)
  * [How to configure clustering for a Premium Azure Redis Cache (Premium Azure Redis Cache のクラスタリングの構成方法)](cache-how-to-premium-clustering.md)
  * [Premium Azure Redis Cache の Virtual Network のサポートを構成する方法](cache-how-to-premium-vnet.md)
  * Premium キャッシュのサイズ、スループット、帯域幅の詳細については、「 [Azure Redis Cache の FAQ](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) 」を参照してください。

<!-- IMAGES -->
[cache-starter-application]: ./media/cache-web-app-howto/cache-starter-application.png
[cache-added-to-application]: ./media/cache-web-app-howto/cache-added-to-application.png
[cache-create-project]: ./media/cache-web-app-howto/cache-create-project.png
[cache-select-template]: ./media/cache-web-app-howto/cache-select-template.png
[cache-model-add-class]: ./media/cache-web-app-howto/cache-model-add-class.png
[cache-model-add-class-dialog]: ./media/cache-web-app-howto/cache-model-add-class-dialog.png
[cache-add-controller]: ./media/cache-web-app-howto/cache-add-controller.png
[cache-add-controller-class]: ./media/cache-web-app-howto/cache-add-controller-class.png
[cache-configure-controller]: ./media/cache-web-app-howto/cache-configure-controller.png
[cache-global-asax]: ./media/cache-web-app-howto/cache-global-asax.png
[cache-RouteConfig-cs]: ./media/cache-web-app-howto/cache-RouteConfig-cs.png
[cache-layout-cshtml]: ./media/cache-web-app-howto/cache-layout-cshtml.png
[cache-layout-cshtml-code]: ./media/cache-web-app-howto/cache-layout-cshtml-code.png
[redis-cache-manage-nuget-menu]: ./media/cache-web-app-howto/redis-cache-manage-nuget-menu.png
[redis-cache-stack-exchange-nuget]: ./media/cache-web-app-howto/redis-cache-stack-exchange-nuget.png
[cache-teamscontroller]: ./media/cache-web-app-howto/cache-teamscontroller.png
[cache-web-config]: ./media/cache-web-app-howto/cache-web-config.png
[cache-views-teams-index-cshtml]: ./media/cache-web-app-howto/cache-views-teams-index-cshtml.png
[cache-teams-index-table]: ./media/cache-web-app-howto/cache-teams-index-table.png
[cache-status-message]: ./media/cache-web-app-howto/cache-status-message.png
[deploybutton]: ./media/cache-web-app-howto/deploybutton.png
[cache-deploy-to-azure-step-1]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-1.png
[cache-deploy-to-azure-step-2]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-2.png
[cache-deploy-to-azure-step-3]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-3.png
[cache-deployment-started]: ./media/cache-web-app-howto/cache-deployment-started.png
[cache-publish-app]: ./media/cache-web-app-howto/cache-publish-app.png
[cache-publish-to-app-service]: ./media/cache-web-app-howto/cache-publish-to-app-service.png
[cache-select-web-app]: ./media/cache-web-app-howto/cache-select-web-app.png
[cache-publish]: ./media/cache-web-app-howto/cache-publish.png
[cache-delete-resource-group]: ./media/cache-web-app-howto/cache-delete-resource-group.png
[cache-delete-confirm]: ./media/cache-web-app-howto/cache-delete-confirm.png


