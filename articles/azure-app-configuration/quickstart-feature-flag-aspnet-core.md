---
title: ASP.NET Core に機能フラグを追加するためのクイックスタート
description: ASP.NET Core アプリに機能フラグを追加し、Azure App Configuration を使用して管理します。
author: lisaguthrie
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 01/14/2020
ms.author: lcozzens
ms.openlocfilehash: 12b66dc173a8d3f93f97fb369ce03533299a65d7
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88235266"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>クイック スタート:ASP.NET Core アプリに機能フラグを追加する

このクイックスタートでは、Azure App Configuration を使用して、ASP.NET Core アプリケーションにエンド ツー エンドの機能管理の実装を作成します。 App Configuration サービスを使用し、すべての機能フラグを 1 か所に格納し、その状態を制御します。 

.NET Core 機能管理ライブラリは、包括的な機能フラグのサポートにより、フレームワークを拡張します。 これらのライブラリは、.NET Core 構成システム上に構築されます。 また、.NET Core 構成プロバイダーを介して、App Configuration とシームレスに統合されます。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download)。

## <a name="create-an-app-configuration-store"></a>App Configuration ストアを作成する

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. **[機能マネージャー]**  >  **[+追加]** を選択して、`Beta` という機能フラグを追加します。

    > [!div class="mx-imgBorder"]
    > ![Beta という名前の機能フラグを有効にする](media/add-beta-feature-flag.png)

    現時点では `label` を空欄にしておいてください。 **[適用]** を選択して新しい機能フラグを保存します。

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core Web アプリケーションの作成

[.NET Core コマンド ライン インターフェイス (CLI)](https://docs.microsoft.com/dotnet/core/tools/) を使用して、新しい ASP.NET Core MVC Web アプリ プロジェクトを作成します。 Visual Studio ではなく .NET Core CLI を使用する利点は、.NET Core CLI は Windows、macOS、および Linux プラットフォームで使用できるという点です。

1. プロジェクト用の新規フォルダーを作成します。 このクイック スタートでは、*TestFeatureFlags* という名前を付けます。

1. 新しいフォルダーで次のコマンドを実行して、新しい ASP.NET Core MVC Web アプリ プロジェクトを作成します。

   ```    
   dotnet new mvc --no-https
   ```

## <a name="add-secret-manager"></a>シークレット マネージャーを追加する

シークレット マネージャーを使用するには、 *.csproj* ファイルに `UserSecretsId` 要素を追加します。

1. *.csproj* ファイルを開きます。

1.  ここに示すように、`UserSecretsId` 要素を追加します。 同じ GUID を使用することも、この値を独自の値に置き換えることもできます。

    > [!IMPORTANT]
    > `CreateHostBuilder` により、.NET Core 3.0 の `CreateWebHostBuilder` が置き換えられます。  お使いの環境に応じて適切な構文を選択します。

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

        <PropertyGroup>
            <TargetFramework>netcoreapp2.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>

        <ItemGroup>
            <PackageReference Include="Microsoft.AspNetCore.App" />
            <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
        </ItemGroup>

    </Project>
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

        <PropertyGroup>
            <TargetFramework>netcoreapp3.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>

    </Project>
    ```
    ---

1. *.csproj* ファイルを保存します。

シークレット マネージャー ツールは、開発作業の機密データをプロジェクト ツリーの外部に格納します。 これにより、ソース コード内のアプリ シークレットが偶発的に共有されるのを防止できます。

> [!TIP]
> シークレット マネージャーの詳細については、「[ASP.NET Core での開発におけるアプリ シークレットの安全な保存](https://docs.microsoft.com/aspnet/core/security/app-secrets)」を参照してください。

## <a name="connect-to-an-app-configuration-store"></a>App Configuration ストアに接続する

1. 次のコマンドを実行して、`Microsoft.Azure.AppConfiguration.AspNetCore` パッケージと `Microsoft.FeatureManagement.AspNetCore` NuGet パッケージへの参照を追加します。

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    dotnet add package Microsoft.FeatureManagement.AspNetCore
    ```

1. 次のコマンドを実行して、プロジェクトのパッケージを復元します。

    ```dotnetcli
    dotnet restore
    ```

1. シークレット マネージャーに、**ConnectionStrings:AppConfig** という名前のシークレットを追加します。

    このシークレットには、App Configuration ストアにアクセスするための接続文字列が格納されます。 次のコマンドの `<your_connection_string>` の値を、自分の App Configuration ストアの接続文字列に置き換えます。 プライマリ読み取り専用キーの接続文字列は、Azure portal の **[アクセス キー]** で確認できます。

    このコマンドは、 *.csproj* ファイルと同じディレクトリで実行する必要があります。

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    シークレット マネージャーは、Web アプリをローカルにテストするためだけに使用されます。 たとえば [Azure App Service](https://azure.microsoft.com/services/app-service) にアプリをデプロイするときは、シークレット マネージャーを使用して接続文字列を格納する代わりに、App Service で **[接続文字列]** という名前のアプリケーション設定を使用します。

    このシークレットには、App Configuration API を使用してアクセスできます。 構成名の中のコロン (:) は、サポートされているすべてのプラットフォーム上の App Configuration API で機能します。 [環境別の構成](https://docs.microsoft.com/aspnet/core/fundamentals/configuration)に関するページを参照してください。

1. *Program.cs* で、`config.AddAzureAppConfiguration()` メソッドを呼び出して、App Configuration を使用するように `CreateWebHostBuilder` メソッドを更新します。

    > [!IMPORTANT]
    > `CreateHostBuilder` により、.NET Core 3.0 の `CreateWebHostBuilder` が置き換えられます。  お使いの環境に応じて適切な構文を選択します。

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options => {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                        .UseFeatureFlags();
                });
            })
            .UseStartup<Startup>();
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(options => {
                options.Connect(settings["ConnectionStrings:AppConfig"])
                    .UseFeatureFlags();
            });
        })
        .UseStartup<Startup>());
    ```
    ---

1. *Startup.cs* を開き、.NET Core 機能マネージャーへの参照を追加します。

    ```csharp
    using Microsoft.FeatureManagement;
    ```

1. `services.AddFeatureManagement()` メソッドを呼び出して機能フラグ サポートを追加するように、`ConfigureServices` メソッドを更新します。 必要に応じて、`services.AddFeatureFilter<FilterType>()` を呼び出すことで、機能フラグで使用される任意のフィルターを含めることができます。

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)
    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);        
        services.AddFeatureManagement();
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)
    ```csharp    
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllersWithViews();
        services.AddFeatureManagement();
    }

    ---

1. Update the `Configure` method to add a middleware to allow the feature flag values to be refreshed at a recurring interval while the ASP.NET Core web app continues to receive requests.

    #### [.NET Core 2.x](#tab/core2x)
    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
            }

            app.UseStaticFiles();
            app.UseCookiePolicy();
            app.UseAzureAppConfiguration();
            app.UseMvc(routes =>
            {
                routes.MapRoute(
                    name: "default",
                    template: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)
    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
            }
            app.UseStaticFiles();
            app.UseRouting();
            app.UseAuthorization();
            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
            app.UseAzureAppConfiguration();
    }
    ```
    ---

1. *MyFeatureFlags.cs* ファイルを追加します。

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

1. *Controllers* ディレクトリに *BetaController.cs* を追加します。

    ```csharp
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.FeatureManagement;
    using Microsoft.FeatureManagement.Mvc;

    namespace TestFeatureFlags.Controllers
    {
        public class BetaController: Controller
        {
            private readonly IFeatureManager _featureManager;

            public BetaController(IFeatureManagerSnapshot featureManager)
            {
                _featureManager = featureManager;
            }

            [FeatureGate(MyFeatureFlags.Beta)]
            public IActionResult Index()
            {
                return View();
            }
        }
    }
    ```

1. *Views* ディレクトリの *_ViewImports.cshtml* を開き、機能マネージャーのタグ ヘルパーを追加します。

    ```html
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

1. *Views*\\*Shared* ディレクトリにある *_Layout.cshtml* を開いて、`<body>` > `<header>` の下の `<nav>` バー コードを次のコードに置き換えます。

    ```html
    <nav class="navbar navbar-expand-sm navbar-toggleable-sm navbar-light bg-white border-bottom box-shadow mb-3">
        <div class="container">
            <a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">TestFeatureFlags</a>
            <button class="navbar-toggler" type="button" data-toggle="collapse" data-target=".navbar-collapse" aria-controls="navbarSupportedContent"
            aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
            </button>
            <div class="navbar-collapse collapse d-sm-inline-flex flex-sm-row-reverse">
                <ul class="navbar-nav flex-grow-1">
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="Index">Home</a>
                    </li>
                    <feature name="Beta">
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Beta" asp-action="Index">Beta</a>
                    </li>
                    </feature>
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="Privacy">Privacy</a>
                    </li>
                </ul>
            </div>
        </div>
    </nav>
    ```

1. *Views* の下に *Beta* ディレクトリを作成し、そこに *Index.cshtml* を追加します。

    ```html
    @{
        ViewData["Title"] = "Beta Home Page";
    }

    <h1>
        This is the beta website.
    </h1>
    ```

## <a name="build-and-run-the-app-locally"></a>アプリをビルドしてローカルで実行する

1. .NET Core CLI を使用してアプリケーションをビルドするには、コマンド シェルで次のコマンドを実行します。

    ```
    dotnet build
    ```

1. ビルドが正常に完了したら、次のコマンドを実行して、Web アプリをローカルで実行します。

    ```
    dotnet run
    ```

1. ブラウザー ウィンドウを開いて、`https://localhost:5000` (ローカルでホストされた Web アプリの既定の URL) に移動します。
    Azure Cloud Shell で作業している場合は、 *[Web プレビュー]* 、 *[構成]* の順に選択します。  確認を求められたら、ポート 5000 を選択します。

    ![[Web プレビュー] ボタンを見つける](./media/quickstarts/cloud-shell-web-preview.png)

    ブラウザーに、次の画像のようなページが表示されます。
    ![クイックスタートのアプリ (ローカルで起動)](./media/quickstarts/aspnet-core-feature-flag-local-before.png)

1. [Azure portal](https://portal.azure.com) にサインインします。 **[すべてのリソース]** を選択し、クイック スタートで作成した App Configuration ストア インスタンスを選択します。

1. **[Feature Manager]\(機能マネージャー\)** を選択し、 **[Beta]\(ベータ\)** キーの状態を **[On]\(オン\)** に変更します。

1. コマンド プロンプトに戻り、`Ctrl-C` キーを押して、`dotnet` プロセスの実行をキャンセルします。  `dotnet run` を使用してアプリケーションを再起動します。

1. ブラウザー ページを最新の情報に更新して新しい構成設定を確認します。

    ![クイック スタートのアプリ (ローカルで起動)](./media/quickstarts/aspnet-core-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、新しい App Configuration ストアを作成し、この構成ストアを使用して、[機能管理ライブラリ](https://go.microsoft.com/fwlink/?linkid=2074664)を介して ASP.NET Core Web アプリの機能を管理しました。

- [機能管理](./concept-feature-management.md)の詳細を確認します。
- [機能フラグを管理](./manage-feature-flags.md)します。
- [ASP.NET Core アプリ内で機能フラグを使用](./use-feature-flags-dotnet-core.md)します。
- [ASP.NET Core アプリで動的な構成を使用する](./enable-dynamic-configuration-aspnet-core.md)
