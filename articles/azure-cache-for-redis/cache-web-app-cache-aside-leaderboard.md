---
title: チュートリアル:Web アプリを作成する (キャッシュ アサイド) - Azure Cache for Redis
description: キャッシュ アサイド パターンを使用する Web アプリを Azure Cache for Redis で作成する方法について説明します。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: tutorial
ms.custom: devx-track-csharp, mvc
ms.date: 03/30/2018
ms.openlocfilehash: 90e60044e227ea1a18ea032d302b29abda1ea2e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92536846"
---
# <a name="tutorial-create-a-cache-aside-leaderboard-on-aspnet"></a>チュートリアル:ASP.NET でキャッシュ アサイド スコアボードを作成する

このチュートリアルでは、[Azure Cache for Redis 用の ASP.NET のクイックスタート](cache-web-app-howto.md)に関する記事で作成した *ContosoTeamStats* ASP.NET Web アプリを更新して、Azure Cache for Redis で [キャッシュ アサイド パターン](/azure/architecture/patterns/cache-aside)を使用するスコアボードが含まれるようにします。 サンプル アプリケーションでは、チームの統計情報の一覧をデータベースから取得して表示し、Azure Cache for Redis を使用して、キャッシュに対するデータの保存と取得のパフォーマンスを向上させるさまざまな方法を示します。 チュートリアルの最後には、実際にデータベースの読み取りと書き込みを行う Web アプリが完成します。Web アプリは、Azure Cache for Redis を使って最適化され、Azure でホストされます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * データの保存と取得に Azure Cache for Redis を使うことでデータのスループットを高め、データベースの負荷を軽減する。
> * Redis ソート済みセットを使用して上位 5 チームを取得する。
> * Resource Manager テンプレートを使用してアプリケーションの Azure リソースをプロビジョニングする。
> * Visual Studio を使用してアプリケーションを Azure に発行する。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を満たしている必要があります。

* このチュートリアルは、[Azure Cache for Redis 用の ASP.NET のクイックスタート](cache-web-app-howto.md)に関する記事の続きです。 クイックスタートを完了していない場合は、先に完了してください。
* 次のワークロードを使って、[Visual Studio 2019](https://www.visualstudio.com/downloads/) をインストールします。
    * ASP.NET および Web の開発
    * Azure 開発
    * SQL Server Express LocalDB または [SQL Server 2017 Express エディション](https://www.microsoft.com/sql-server/sql-server-editions-express)を使用する .NET デスクトップ開発

## <a name="add-a-leaderboard-to-the-project"></a>スコアボードをプロジェクトに追加する

チュートリアルのこのセクションでは、架空のチームの勝利、敗北、および引き分けの統計情報を報告するスコアボードを含む *ContosoTeamStats* プロジェクトを構成します。

### <a name="add-the-entity-framework-to-the-project"></a>Entity Framework をプロジェクトに追加する

1. Visual Studio で、[Azure Cache for Redis 用の ASP.NET のクイックスタート](cache-web-app-howto.md)に関する記事で作成した *ContosoTeamStats* ソリューションを開きます。
2. **[ツール]、[NuGet パッケージ マネージャー]、[パッケージ マネージャー コンソール]** の順にクリックします。
3. **[パッケージ マネージャー コンソール]** ウィンドウで次のコマンドを実行して、EntityFramework をインストールします。

    ```powershell
    Install-Package EntityFramework
    ```

このパッケージの詳細については、[EntityFramework](https://www.nuget.org/packages/EntityFramework/) に関する NuGet ページを参照してください。

### <a name="add-the-team-model"></a>チーム モデルを追加する

1. **ソリューション エクスプローラー** で **Models** フォルダーを右クリックし、 **[追加]** 、 **[クラス]** の順に選択します。

1. クラス名に「 `Team` 」と入力し、 **[追加]** をクリックします。

    ![Add model class](./media/cache-web-app-cache-aside-leaderboard/cache-model-add-class-dialog.png)

1. *Team.cs* ファイルの先頭にある `using` ステートメントを次の `using` ステートメントに置き換えます。

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Data.Entity.SqlServer;
    ```

1. `Team` クラスの定義を次のコード スニペットに置き換えます。このコード スニペットでは、更新された `Team` クラスの定義に加え、その他の Entity Framework の一部のヘルパー クラスの定義が含まれています。 このチュートリアルでは、Entity Framework によるコード ファースト アプローチを使用しています。 このアプローチにより、Entity Framework は、コードからデータベースを作成できます。 このチュートリアルで使用されている Entity Framework の Code First 手法の詳細については、「[新しいデータベースの Code First](/ef/ef6/modeling/code-first/workflows/new-database)」を参照してください。

    ```csharp
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

1. **ソリューション エクスプローラー** で、**Web.config** をダブルクリックして開きます。

    ![web.config](./media/cache-web-app-cache-aside-leaderboard/cache-web-config.png)

1. 次の `connectionStrings` セクションを `configuration` セクション内に追加します。 接続文字列の名前は、Entity Framework のデータベース コンテキスト クラスの名前と一致する `TeamContext` にする必要があります。

    この接続文字列は、[前提条件](#prerequisites)が満たされ、Visual Studio 2019 と共にインストールされる *.NET デスクトップ開発* ワークロードの一部である SQL Server Express LocalDB がインストールされていることを前提としています。

    ```xml
    <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    次の例は、`configuration` セクションの `configSections` の後に続く新しい `connectionStrings` セクションを示しています。

    ```xml
    <configuration>
        <configSections>
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
        </configSections>
        <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
        </connectionStrings>
        ...
    ```

### <a name="add-the-teamscontroller-and-views"></a>TeamsController とビューを追加する

1. Visual Studio でプロジェクトをビルドします。 

1. **ソリューション エクスプローラー** で **Controllers** フォルダーを右クリックし、 **[追加]** 、 **[コントローラー]** の順に選択します。

1. **[Entity Framework を使用した、ビューがある MVC 5 コントローラー]** を選択し、 **[追加]** をクリックします。 **[追加]** をクリックした後にエラーが発生する場合は、先にプロジェクトをビルド済みであることを確認してください。

    ![Add controller class](./media/cache-web-app-cache-aside-leaderboard/cache-add-controller-class.png)

1. **[モデル クラス]** ボックスの一覧から **[Team (ContosoTeamStats.Models)]** を選択します。 **[データ コンテキスト クラス]** ボックスの一覧から **[TeamContext (ContosoTeamStats.Models)]** を選択します。 **[コントローラー名]** ボックスに「`TeamsController`」と入力します (自動的に入力されなかった場合)。 **[追加]** をクリックしてコントローラー クラスを作成し、既定のビューを追加します。

    ![Configure controller](./media/cache-web-app-cache-aside-leaderboard/cache-configure-controller.png)

1. **ソリューション エクスプローラー** で **Global.asax** を展開し、**Global.asax.cs** をダブルクリックして開きます。

    ![Global.asax.cs](./media/cache-web-app-cache-aside-leaderboard/cache-global-asax.png)

1. ファイルの上部に次の 2 つの `using` ステートメントを、他の `using` ステートメントに続けて追加します。

    ```csharp
    using System.Data.Entity;
    using ContosoTeamStats.Models;
    ```

1. `Application_Start` メソッドの最後に次のコード行を追加します。

    ```csharp
    Database.SetInitializer<TeamContext>(new TeamInitializer());
    ```

1. **ソリューション エクスプローラー** で `App_Start` を展開し、`RouteConfig.cs` をダブルクリックします。

    ![RouteConfig.cs](./media/cache-web-app-cache-aside-leaderboard/cache-RouteConfig-cs.png)

1. `RegisterRoutes` メソッドで、`Default` ルートの `controller = "Home"` を、次の例に示すように `controller = "Teams"` に置き換えます。

    ```csharp
    routes.MapRoute(
        name: "Default",
        url: "{controller}/{action}/{id}",
        defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
    );
    ```

### <a name="configure-the-layout-view"></a>レイアウト ビューを構成する

1. **ソリューション エクスプローラー** で **Views** フォルダー、**Shared** フォルダーを順に展開し、 **_Layout.cshtml** をダブルクリックします。 

    ![_Layout.cshtml](./media/cache-web-app-cache-aside-leaderboard/cache-layout-cshtml.png)

1. 次の例のように `title` 要素の内容を変更し、`My ASP.NET Application` を `Contoso Team Stats` に置き換えます。

    ```html
    <title>@ViewBag.Title - Contoso Team Stats</title>
    ```

1. `body` セクションで、*Contoso Team Stats* 用の次の新しい `Html.ActionLink` ステートメントを *Azure Cache for Redis Test* へのリンクのすぐ下に追加します。

    ```csharp
    @Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`
    ```

    ![コードの変更](./media/cache-web-app-cache-aside-leaderboard/cache-layout-cshtml-code.png)

1. **Ctrl + F5** キーを押してアプリケーションをビルドし、実行します。 このバージョンのアプリケーションは、データベースから直接結果を読み取ります。 **[Entity Framework を使用した、ビューがある MVC 5 コントローラー]** スキャフォールディングによってアプリケーションに自動的に追加された **[新規作成]** 、 **[編集]** 、 **[詳細]** 、 **[削除]** の各アクションに注目してください。 次のセクションでは、Azure Cache for Redis を追加してデータへのアクセスを効率化し、アプリケーションに機能を追加します。

    ![Starter application](./media/cache-web-app-cache-aside-leaderboard/cache-starter-application.png)

## <a name="configure-the-app-for-azure-cache-for-redis"></a>アプリケーションを Azure Cache for Redis 用に構成する

このセクションでは、[StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) キャッシュ クライアントを使用して、Azure Cache for Redis インスタンスで Contoso チームの統計情報を格納したり、取得したりするようにサンプル アプリケーションを構成します。

### <a name="add-a-cache-connection-to-the-teams-controller"></a>Teams Controller へのキャッシュ接続を追加する

*StackExchange.Redis* クライアント ライブラリ パッケージのインストールは、クイックスタートで既に実行しています。 ローカルに使用される *CacheConnection* アプリ設定の構成と App Service への発行も、既に行っています。 同じクライアント ライブラリと *CacheConnection* 情報を、*TeamsController* でも使用します。

1. **ソリューション エクスプローラー** で **Controllers** フォルダーを展開し、**TeamsController.cs** をダブルクリックして開きます。

    ![Teams controller](./media/cache-web-app-cache-aside-leaderboard/cache-teamscontroller.png)

1. 次の 2 つの `using` ステートメントを **TeamsController.cs** に追加します。

    ```csharp
    using System.Configuration;
    using StackExchange.Redis;
    ```

1. 次の 2 つのプロパティを `TeamsController` クラスに追加します。

    ```csharp
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

### <a name="update-the-teamscontroller-to-read-from-the-cache-or-the-database"></a>TeamsController クラスをキャッシュまたはデータベースから読み取るように更新する

このサンプルでは、データベースまたはキャッシュから、チームの統計情報を取得できます。 チームの統計情報は、シリアル化された `List<Team>`としてキャッシュに格納されるほか、Redis のデータ型を使用してソート済みセットとして格納されます。 ソート済みセットから項目を取得するときは、一部または全部の項目を対象にできるほか、特定の項目を照会することもできます。 このサンプルでは、勝利数によるランキングの上位 5 チームをソート済みセットに対して照会します。

Azure Cache for Redis を使用するためにチームの統計情報を複数の形式でキャッシュに格納する必要はありません。 このチュートリアルでは、データをキャッシュする際に使用できる各種の方法とデータ型を例示するために、複数の形式を使用しています。

1. `TeamsController.cs` ファイルの他の `using` ステートメントの先頭に、次の `using` ステートメントを追加します。

    ```csharp
    using System.Diagnostics;
    using Newtonsoft.Json;
    ```

1. 現在の `public ActionResult Index()` メソッドの実装を次の実装に置き換えます。

    ```csharp
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

    ```csharp
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

    ```csharp
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

    ```csharp
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
    ```csharp
    List<Team> GetFromDB()
    {
        ViewBag.msg += "Results read from DB. ";
        var results = from t in db.Teams
            orderby t.Wins descending
            select t;

        return results.ToList<Team>();
    }
    ```

    `GetFromList` メソッドは、シリアル化された `List<Team>` としてチームの統計情報をキャッシュから読み取ります。 統計情報がキャッシュに存在しない場合は、キャッシュ ミスが発生します。 キャッシュ ミスの場合、チームの統計情報はデータベースから読み取られ、次の要求のためにキャッシュに格納されます。 このサンプルでは、JSON.NET のシリアル化を使用して、.NET オブジェクトのキャッシュへのシリアル化とキャッシュからのシリアル化を行います。 詳細については、[Azure Cache for Redis における .NET オブジェクトの使用方法](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache)に関するセクションを参照してください。

    ```csharp
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

    ```csharp
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

    ```csharp
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

このサンプルの過程で生成されたスキャフォールディング コードには、チームの追加、編集、削除を行うメソッドが含まれています。 キャッシュ内のデータは、チームが追加、編集、削除されるたびに古くなります。 このセクションでは、キャッシュされたチームを消去するようにこれら 3 つのメソッドを変更して、キャッシュが更新されるようにします。

1. `TeamsController` クラスの `Create(Team team)` メソッドに移動します。 次の例のように、`ClearCachedTeams` メソッドの呼び出しを追加します。

    ```csharp
    // POST: Teams/Create
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see https://go.microsoft.com/fwlink/?LinkId=317598.
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

2. `TeamsController` クラスの `Edit(Team team)` メソッドに移動します。 次の例のように、`ClearCachedTeams` メソッドの呼び出しを追加します。

    ```csharp
    // POST: Teams/Edit/5
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see https://go.microsoft.com/fwlink/?LinkId=317598.
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

3. `TeamsController` クラスの `DeleteConfirmed(int id)` メソッドに移動します。 次の例のように、`ClearCachedTeams` メソッドの呼び出しを追加します。

    ```csharp
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

### <a name="add-caching-methods-to-the-teams-index-view"></a>キャッシュ メソッドをチームのインデックス ビューに追加する

1. **ソリューション エクスプローラー** で、**Views** フォルダー、**Teams** フォルダーの順に展開し、**Index.cshtml** をダブルクリックします。

    ![Index.cshtml](./media/cache-web-app-cache-aside-leaderboard/cache-views-teams-index-cshtml.png)

1. ファイルの上部付近から次の段落要素を探します。

    ![Action table](./media/cache-web-app-cache-aside-leaderboard/cache-teams-index-table.png)

    このリンクは、新しいチームを作成します。 段落要素を以下の表に置き換えます。 この表には、新しいチームの作成、新しいシーズンのゲーム プレイ、キャッシュの消去、各種形式でのキャッシュからのチーム取得、データベースからのチームの取得、新しいサンプル データでのデータベースの再構築を行うためのアクション リンクが含まれています。

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

    ![ステータス メッセージ](./media/cache-web-app-cache-aside-leaderboard/cache-status-message.png)

1. **F6** キーを押して、プロジェクトをビルドします。

## <a name="run-the-app-locally"></a>アプリをローカルで実行する

コンピューターでアプリケーションをローカルに実行して、チームをサポートするために追加されている機能を確認します。

このテストでは、アプリケーションとデータベースの両方がローカルに実行されます。 ただし、Azure Cache for Redis は、Azure でリモートでホストされます。 そのため、キャッシュのパフォーマンスは、データベースよりもわずかに低くなる可能性があります。 パフォーマンスを最大限に引き出すには、クライアント アプリケーションと Azure Cache for Redis インスタンスを同じ場所に置いてください。 次のセクションで、すべてのリソースを Azure にデプロイして、キャッシュの使用によるパフォーマンスの向上を確認します。

アプリをローカルに実行するには:

1. **Ctrl キーを押しながら F5 キーを押して** アプリケーションを実行します。

    ![ローカルで実行中のアプリ](./media/cache-web-app-cache-aside-leaderboard/cache-local-application.png)

1. ビューに追加された新しい各メソッドをテストします。 これらのテストでは、キャッシュはリモートであるため、データベースのパフォーマンスのほうがキャッシュよりもわずかに優れているはずです。

## <a name="publish-and-run-in-azure"></a>Azure に発行して実行する

### <a name="provision-a-database-for-the-app"></a>アプリのデータベースをプロビジョニングする

このセクションでは、Azure でホスト中に使用されるアプリ用に SQL Database に新しいデータベースをプロビジョニングします。

1. [Azure Portal](https://portal.azure.com/) で、左上隅にある **[リソースの作成]** をクリックします。

1. **[新規]** ページで、 **[データベース]**  >  **[SQL Database]** をクリックします。

1. 新しい SQL Database で、次の設定を使用します。

   | 設定       | 推奨値 | 説明 |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **データベース名** | *ContosoTeamsDatabase* | 有効なデータベース名については、「[Database Identifiers (データベース識別子)](/sql/relational-databases/databases/database-identifiers)」を参照してください。 |
   | **サブスクリプション** | *該当するサブスクリプション*  | キャッシュの作成と App Service でのホストを行うために使用したのと同じサブスクリプションを選択します。 |
   | **リソース グループ**  | *TestResourceGroup* | **[既存のものを使用]** をクリックし、キャッシュと App Service に配置したものと同じリソース グループを使用します。 |
   | **ソースの選択** | **空のデータベース** | 空のデータベースから始めてください。 |

1. **[サーバー]** で、 **[必要な設定の構成]**  >  **[サーバーの新規作成]** をクリックし、次の情報を指定した後、 **[選択]** ボタンをクリックします。

   | 設定       | 推奨値 | 説明 |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **サーバー名** | グローバルに一意の名前 | 有効なサーバー名については、[名前付け規則と制限](/azure/architecture/best-practices/resource-naming)に関するページを参照してください。 |
   | **サーバー管理者ログイン** | 有効な名前 | 有効なログイン名については、「[Database Identifiers (データベース識別子)](/sql/relational-databases/databases/database-identifiers)」を参照してください。 |
   | **パスワード** | 有効なパスワード | パスワードには 8 文字以上が使用され、大文字、小文字、数字、英数字以外の文字のうち、3 つのカテゴリの文字が含まれている必要があります。 |
   | **場所** | *米国東部* | キャッシュと App Service を作成したのと同じリージョンを選択します。 |

1. **[ダッシュボードにピン留めする]** をクリックした後、 **[作成]** をクリックして、新しいデータベースとサーバーを作成します。

1. 新しいデータベースが作成されたら、 **[データベース接続文字列の表示]** を選択し、**ADO.NET** の接続文字列をコピーします。

    ![接続文字列の表示](./media/cache-web-app-cache-aside-leaderboard/cache-show-connection-strings.png)

1. Azure Portal で App Service に移動し、 **[アプリケーションの設定]** をクリックし、[接続文字列] セクションの **[新しい接続文字列の追加]** をクリックします。

1. Entity Framework データベースのコンテキスト クラスと一致する *TeamContext* という名前の新しい接続文字列を追加します。 新しいデータベースの接続文字列を値として貼り付けます。 接続文字列内の次のプレース ホルダーを置き換えたことを確認し、 **[保存]** をクリックします。

    | プレースホルダー | 推奨値 |
    | --- | --- |
    | *{your_username}* | 先ほど作成したサーバーの **サーバー管理者ログイン** を使用します。 |
    | *{your_password}* | 先ほど作成したサーバーのパスワードを使用します。 |

    ユーザー名とパスワードをアプリケーション設定として追加しても、ユーザー名とパスワードがコードに含まれることはありません。 この方法によって、これらの資格情報を保護できます。

### <a name="publish-the-application-updates-to-azure"></a>アプリケーションの更新を Azure に発行する

チュートリアルのこの手順では、アプリケーションの更新を Azure に発行してクラウドで実行します。

1. Visual Studio で **ContosoTeamStats** プロジェクトを右クリックし、 **[発行]** を選択します。

    ![発行](./media/cache-web-app-cache-aside-leaderboard/cache-publish-app.png)

2. クイックスタートで作成した発行プロファイルと同じものを使用するには、 **[発行]** をクリックします。

3. 発行が完了したら、Visual Studio によって、既定の Web ブラウザーでアプリが起動します。

    ![Cache added](./media/cache-web-app-cache-aside-leaderboard/cache-added-to-application.png)

    次の表は、サンプル アプリケーションの各アクション リンクとその説明を一覧にしたものです。

    | アクション | 説明 |
    | --- | --- |
    | Create New |新しいチームを作成します。 |
    | Play Season |ゲームのシーズンを再生し、チームの統計情報を更新して、キャッシュに格納されている古いチーム データがあれば消去します。 |
    | Clear Cache |チームの統計情報をキャッシュから消去します。 |
    | List from Cache |チームの統計情報をキャッシュから取得します。 キャッシュ ミスが発生した場合は、統計情報をデータベースから読み込んで、次回使用できるようキャッシュに保存します。 |
    | Sorted Set from Cache |ソート済みセットを使用してキャッシュからチームの統計情報を取得します。 キャッシュ ミスが発生した場合は、統計情報をデータベースから読み込み、ソート済みセットを使用してキャッシュに保存します。 |
    | Top 5 Teams from Cache |ソート済みセットを使用してキャッシュから上位 5 チームを取得します。 キャッシュ ミスが発生した場合は、統計情報をデータベースから読み込み、ソート済みセットを使用してキャッシュに保存します。 |
    | Load from DB |チームの統計情報をデータベースから取得します。 |
    | Rebuild DB |データベースを再構築し、サンプル チーム データを再度読み込みます。 |
    | Edit / Details / Delete |チームの編集、詳細表示、削除を実行します。 |

いくつかのアクションをクリックし、各種のソースからデータを取得してみてください。 それぞれの方法で、データベースとキャッシュからデータを取得するのにかかる時間の違いをよく観察してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

チュートリアルのサンプル アプリケーションを使い終わったら、コストとリソースを節約するために Azure リソースを削除しましょう。 すべてのリソースは同じリソース グループに含まれているため、リソース グループを削除するという 1 つの操作で、それらをまとめて削除できます。 このトピックの手順では、*TestResources* という名前のリソース グループを使用しました。

> [!IMPORTANT]
> いったん削除したリソース グループを元に戻すことはできません。リソース グループとそこに存在するすべてのリソースは完全に削除されます。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。 このサンプルをホスティングするリソースを、維持したいリソースが含まれている既存のリソース グループ内に作成した場合は、該当するブレードから各リソースを個別に削除できます。
>

1. [Azure ポータル](https://portal.azure.com) にサインインし、 **[リソース グループ]** をクリックします。
2. リソース グループの名前を **[フィルター項目]** ボックスに入力します。
3. リソース グループの右側にある **[...]** をクリックし、 **[リソース グループの削除]** をクリックします。

    ![削除](./media/cache-web-app-cache-aside-leaderboard/cache-delete-resource-group.png)

4. リソース グループの削除の確認を求めるメッセージが表示されます。 確認のためにリソース グループの名前を入力し、 **[削除]** をクリックします。

    しばらくすると、リソース グループとそこに含まれているすべてのリソースが削除されます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Cache for Redis のスケーリング方法](./cache-how-to-scale.md)