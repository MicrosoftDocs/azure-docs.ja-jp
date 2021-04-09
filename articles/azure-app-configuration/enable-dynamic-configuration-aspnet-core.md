---
title: チュートリアル:ASP.NET Core で App Configuration の動的な構成を有効にする
titleSuffix: Azure App Configuration
description: このチュートリアルでは、ASP.NET Core アプリの構成データを動的に更新する方法を学習します。
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/1/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 083bd56b2b211d11206a277bf31eea797b37cdb9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99979931"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>チュートリアル:ASP.NET Core アプリで動的な構成を使用する

ASP.NET Core には、さまざまなソースから構成データを読み取ることができるプラグ可能な構成システムがあります。 アプリケーションを再起動せずに、動的に変更を処理できます。 ASP.NET Core では、厳密に型指定された .NET クラスへの構成設定のバインドがサポートされています。 さまざまな `IOptions<T>` パターンを使用して、それらをコードに挿入します。 そうしたパターンの 1 つである `IOptionsSnapshot<T>` では、基になるデータが変化したときに、アプリケーションの構成が自動的にリロードされます。 アプリケーションのコントローラーに `IOptionsSnapshot<T>` を挿入すれば、Azure App Configuration に格納されている最新の構成にアクセスすることができます。

ミドルウェアを使って構成設定のセットを動的に更新するよう、App Configuration ASP.NET Core クライアント ライブラリを設定することもできます。 Web アプリが要求を受け取るたびに構成設定が構成ストアで更新されます。

App Configuration では、構成ストアへの呼び出しが多くなりすぎないようにするため、個々の設定が自動的にキャッシュされます。 キャッシュされた設定の値は、構成ストアで変更されたとしても、その有効期限が切れるまで、設定の更新操作は先延ばしされます。 キャッシュの既定の有効期間は 30 秒です。 この有効期間は、必要に応じてオーバーライドできます。

このチュートリアルでは、自分が作成するコードに、構成の動的更新を実装する方法について説明します。 これは、クイック スタートで紹介されている Web アプリに基づいています。 先に進む前に、[App Configuration を使用した ASP.NET Core アプリの作成](./quickstart-aspnet-core-app.md)を完了しておいてください。

このチュートリアルの手順は、任意のコード エディターを使用して実行できます。 推奨のエディターは [Visual Studio Code](https://code.visualstudio.com/) です (Windows、macOS、および Linux プラットフォームで使用できます)。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * App Configuration ストアへの変更に合わせて構成を更新するようにアプリケーションを設定する。
> * アプリケーションのコントローラーに最新の構成を挿入する。

## <a name="prerequisites"></a>前提条件

このチュートリアルを実行するには、[.NET Core SDK](https://dotnet.microsoft.com/download) をインストールします。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

先に進む前に、[App Configuration を使用した ASP.NET Core アプリの作成](./quickstart-aspnet-core-app.md)を完了しておいてください。

## <a name="add-a-sentinel-key"></a>センチネル キーを追加する

"*センチネル キー*" は、構成が変更されたときの通知に使用される特殊なキーです。 センチネル キーの変化をアプリで監視してください。 変更を検出したら、すべての構成の値を更新します。 すべてのキーの変化を監視した場合よりも、このアプローチの方が、アプリから App Configuration に対して行う全体的な要求の数が少なくなります。

1. Azure portal で **[構成エクスプローラー] > [作成] > [キー値]** の順に選択します。
1. **[キー]** に「*TestApp:Settings:Sentinel*」と入力します。 **[値]** には、「1」と入力します。 **[ラベル]** と **[コンテンツの種類]** は空にしておきます。
1. **[適用]** を選択します。

> [!NOTE]
> センチネル キーを使用していない場合は、監視するすべてのキーを手動で登録する必要があります。

## <a name="reload-data-from-app-configuration"></a>App Configuration からデータを再度読み込む

1. 次のコマンドを実行して、`Microsoft.Azure.AppConfiguration.AspNetCore` NuGet パッケージへの参照を追加します。

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. *Program.cs* を開き、`config.AddAzureAppConfiguration()` メソッドを追加して `CreateWebHostBuilder` メソッドを更新します。

   #### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(settings["ConnectionStrings:AppConfig"])
                               .ConfigureRefresh(refresh =>
                                    {
                                        refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                               .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                    });
                    });
                })
            .UseStartup<Startup>());
    ```   
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(settings["ConnectionStrings:AppConfig"])
                               .ConfigureRefresh(refresh =>
                                    {
                                        refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                               .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                    });
                    });
                })
            .UseStartup<Startup>());
    ```
    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .ConfigureRefresh(refresh =>
                                {
                                    refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                           .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                });
                });
            })
            .UseStartup<Startup>();
    ```
    ---

    更新操作がトリガーされたときに、構成データを App Configuration ストアで更新するために使用する設定を指定するには、`ConfigureRefresh` メソッドを使います。 `Register` メソッドの `refreshAll` パラメーターは、センチネル キーが変化した場合に、すべての構成値を更新する必要があることを示します。

    また、`SetCacheExpiration` メソッドでは、キャッシュの有効期間として 5 分を指定し、既定値である 30 秒をオーバーライドしています。 これによって、App Configuration への要求数が減少します。

    > [!NOTE]
    > テスト目的で、キャッシュの有効期間をもっと短くすることもできます。

    実際に更新操作をトリガーするには、変更が生じたときに構成データを更新するようにアプリケーションの更新ミドルウェアを構成する必要があります。 この点については、後続の手順で説明します。

2. 新しい `Settings` クラスを定義および実装する *Settings.cs* ファイルを Controllers ディレクトリに追加します。 名前空間を自分のプロジェクト名に置き換えます。 

    ```csharp
    namespace TestAppConfig
    {
        public class Settings
        {
            public string BackgroundColor { get; set; }
            public long FontSize { get; set; }
            public string FontColor { get; set; }
            public string Message { get; set; }
        }
    }
    ```

3. *Startup.cs* を開き、`ConfigureServices` メソッドの `IServiceCollection.Configure<T>` を使用して、構成データを `Settings` クラスにバインドします。

    #### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
        services.AddAzureAppConfiguration();
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
        services.AddAzureAppConfiguration();
    }
    ```
    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
        services.AddAzureAppConfiguration();
    }
    ```
    ---
    > [!Tip]
    > 構成値を読み取る際のオプション パターンの詳細については、「[ASP.NET Core のオプション パターン](/aspnet/core/fundamentals/configuration/options)」を参照してください。

4. `Configure` メソッドに `UseAzureAppConfiguration` ミドルウェアを追加して更新し、ASP.NET Core Web アプリで要求の受信が続けられている間、更新用に登録された構成設定を更新できるようにします。


    #### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }

            // Add the following line:
            app.UseAzureAppConfiguration();

            app.UseHttpsRedirection();
            
            app.UseStaticFiles();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }

            // Add the following line:
            app.UseAzureAppConfiguration();

            app.UseHttpsRedirection();
            
            app.UseStaticFiles();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        app.UseMvc();
    }
    ```
    ---
    
    ミドルウェアでは、`Program.cs` の `AddAzureAppConfiguration` メソッドで指定されている更新の構成を使って、ASP.NET Core Web アプリによって受信された各要求の更新がトリガーされます。 要求ごとに、更新操作がトリガーされ、登録されている構成設定のキャッシュされた値の有効期間が切れているかどうかが、クライアント ライブラリによって確認されます。 有効期間が過ぎていれば更新されます。

    > [!NOTE]
    > 構成を確実に更新するには、ミドルウェアをできるだけ早く要求パイプラインに追加して、アプリケーション内の別のミドルウェアによって短絡されないようにします。

## <a name="use-the-latest-configuration-data"></a>最新の構成データを使用する

1. Controllers ディレクトリにある *HomeController.cs* を開いて、`Microsoft.Extensions.Options` パッケージへの参照を追加します。

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. `HomeController` クラスを更新して、依存関係の挿入を通じて `Settings` を受け取り、その値を利用するようにします。

 #### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger, IOptionsSnapshot<Settings> settings)
        {
            _logger = logger;
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }

        // ...
    }
```
#### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger, IOptionsSnapshot<Settings> settings)
        {
            _logger = logger;
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }

        // ...
    }
```
#### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        public HomeController(IOptionsSnapshot<Settings> settings)
        {
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }
    }
```
---



3. Views の Home ディレクトリにある *Index.cshtml* を開いて、内容を次のスクリプトに置き換えます。

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @ViewData["BackgroundColor"]
        }
        h1 {
            color: @ViewData["FontColor"];
            font-size: @ViewData["FontSize"]px;
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@ViewData["Message"]</h1>
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>アプリをビルドしてローカルで実行する

1. .NET Core CLI を使用してアプリケーションをビルドするには、コマンド シェルで次のコマンドを実行します。

    ```console
        dotnet build
    ```

1. ビルドが正常に完了したら、次のコマンドを実行して、Web アプリをローカルで実行します。

    ```console
        dotnet run
    ```

1. ブラウザー ウィンドウを開いて、`dotnet run` 出力に表示される URL に移動します。

    ![クイックスタート アプリをローカルで起動する](./media/quickstarts/aspnet-core-app-launch-local-before.png)

1. [Azure portal](https://portal.azure.com) にサインインします。 **[すべてのリソース]** を選択し、クイック スタートで作成した App Configuration ストア インスタンスを選択します。

1. **[Configuration Explorer]\(構成エクスプローラー)** を選択して次のキーの値を更新します。

    | Key | 値 |
    |---|---|
    | TestApp:Settings:BackgroundColor | green |
    | TestApp:Settings:FontColor | lightGray |
    | TestApp:Settings:Message | Data from Azure App Configuration - now with live updates! |
    | TestApp:Settings:Sentinel | 2 |

1. ブラウザー ページを最新の情報に更新して新しい構成設定を確認します。 変更を反映するために複数回の更新が必要になる場合があります。または、自動更新頻度を 5 分未満に変更します。 

    ![更新されたクイックスタート アプリをローカルで起動する](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、App Configuration から動的に構成設定を更新できるように ASP.NET Core Web アプリを設定しました。 App Configuration へのアクセスを効率化する Azure マネージド ID を使用する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [マネージド ID の統合](./howto-integrate-azure-managed-service-identity.md)
