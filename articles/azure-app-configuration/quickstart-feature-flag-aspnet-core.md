---
title: ASP.NET Core に機能フラグを追加するためのクイックスタート
description: ASP.NET Core アプリに機能フラグを追加し、Azure App Configuration を使用して管理します。
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 09/28/2020
ms.author: alkemper
ms.openlocfilehash: 8ca4a6e78ab3c6ccf492869bb68c5296ae91de21
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99806072"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>クイック スタート:ASP.NET Core アプリに機能フラグを追加する

このクイックスタートでは、Azure App Configuration を使用して、ASP.NET Core アプリに機能管理のエンド ツー エンドの実装を作成します。 App Configuration サービスを使用して、すべての機能フラグを 1 か所に保存し、その状態を制御します。 

.NET Core 機能管理ライブラリは、包括的な機能フラグのサポートにより、フレームワークを拡張します。 これらのライブラリは、.NET Core 構成システム上に構築されます。 また、.NET Core 構成プロバイダーを介して、App Configuration とシームレスに統合されます。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/dotnet)
* [.NET Core SDK](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>App Configuration ストアを作成する

[!INCLUDE[Azure App Configuration resource creation steps](../../includes/azure-app-configuration-create.md)]

8. **[操作]**  >  **[機能マネージャー]**  >  **[追加]** の順に選択して、*Beta* という機能フラグを追加します。

    > [!div class="mx-imgBorder"]
    > ![Beta という名前の機能フラグを有効にする](media/add-beta-feature-flag.png)

    ここでは、 **[ラベル]** は空欄のままにしておきます。 **[適用]** を選択して新しい機能フラグを保存します。

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core Web アプリケーションの作成

[.NET Core コマンド ライン インターフェイス (CLI)](/dotnet/core/tools) を使用して、新しい ASP.NET Core MVC プロジェクトを作成します。 Visual Studio ではなく .NET Core CLI を使用する利点は、.NET Core CLI は Windows、macOS、および Linux プラットフォームで使用できるという点です。

次のコマンドを実行して、新しい *TestFeatureFlags* フォルダーに ASP.NET Core MVC プロジェクトを作成します。

```dotnetcli
dotnet new mvc --no-https --output TestFeatureFlags
```

[!INCLUDE[Add Secret Manager support to an ASP.NET Core project](../../includes/azure-app-configuration-add-secret-manager.md)]

## <a name="connect-to-an-app-configuration-store"></a>App Configuration ストアに接続する

1. 次のコマンドを実行して、[Microsoft.Azure.AppConfiguration.AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) および [Microsoft.FeatureManagement.AspNetCore](https://www.nuget.org/packages/Microsoft.FeatureManagement.AspNetCore) NuGet パッケージをインストールします。

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

    ```dotnetcli
    dotnet add package Microsoft.FeatureManagement.AspNetCore
    ```

1. *.csproj* ファイルと同じディレクトリで次のコマンドを実行します。 このコマンドでは、シークレット マネージャーを使用して、`ConnectionStrings:AppConfig` という名前のシークレットを格納します。これには、App Configuration ストアの接続文字列が格納されます。 `<your_connection_string>` プレースホルダーを App Configuration ストアの接続文字列に置き換えます。 接続文字列は、Azure portal の **[アクセス キー]** で確認できます。

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig "<your_connection_string>"
    ```

    シークレット マネージャーは、Web アプリをローカルにテストするためだけに使用されます。 [Azure App Service](https://azure.microsoft.com/services/app-service/web) にアプリをデプロイした場合、シークレット マネージャーの代わりに App Service の **[接続文字列]** アプリケーション設定を使用して、接続文字列を格納します。

    このシークレットには、.NET Core 構成 API を使用してアクセスします。 構成名の中のコロン (`:`) は、サポートされているすべてのプラットフォーム上の構成 API で機能します。 詳細については、「[構成キーと値](/aspnet/core/fundamentals/configuration#configuration-keys-and-values)」をご覧ください。

1. *Program.cs* で、`AddAzureAppConfiguration` メソッドを呼び出して、App Configuration を使用するように `CreateWebHostBuilder` メソッドを更新します。

    > [!IMPORTANT]
    > `CreateHostBuilder` は、.NET Core 3.x の `CreateWebHostBuilder` に代わるものです。 お使いの環境に応じて適切な構文を選択します。

     #### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    var connection = settings.GetConnectionString("AppConfig");
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(connection).UseFeatureFlags());
                }).UseStartup<Startup>());
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    var connection = settings.GetConnectionString("AppConfig");
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(connection).UseFeatureFlags());
                }).UseStartup<Startup>());
    ```

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration(config =>
               {
                   var settings = config.Build();
                   var connection = settings.GetConnectionString("AppConfig");
                   config.AddAzureAppConfiguration(options =>
                       options.Connect(connection).UseFeatureFlags());
               }).UseStartup<Startup>();
    ```

    ---

    上記の変更により、[App Configuration の構成プロバイダー](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration)が .NET Core 構成 API に登録されました。

1. *Startup.cs* で、.NET Core 機能マネージャーへの参照を追加します。

    ```csharp
    using Microsoft.FeatureManagement;
    ```

1. `AddFeatureManagement` メソッドを呼び出して機能フラグ サポートを追加するように、`Startup.ConfigureServices` メソッドを更新します。 必要に応じて、`AddFeatureFilter<FilterType>()` を呼び出すことで、機能フラグで使用される任意のフィルターを含めることができます。

     #### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

    ```csharp    
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllersWithViews();
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
    ```

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc()
            .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
        services.AddFeatureManagement();
    }
    ```

    ---

1. 次のコードを使用して、*MyFeatureFlags.cs* ファイルをルート プロジェクト ディレクトリに追加します。

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

1. 次のコードを使用して、*BetaController.cs* ファイルを *Controllers* ディレクトリに追加します。

    ```csharp
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.FeatureManagement;
    using Microsoft.FeatureManagement.Mvc;

    namespace TestFeatureFlags.Controllers
    {
        public class BetaController: Controller
        {
            private readonly IFeatureManager _featureManager;

            public BetaController(IFeatureManagerSnapshot featureManager) =>
                _featureManager = featureManager;

            [FeatureGate(MyFeatureFlags.Beta)]
            public IActionResult Index() => View();
        }
    }
    ```

1. *Views/_ViewImports.cshtml* で、`@addTagHelper` ディレクティブを使用して機能マネージャーのタグ ヘルパーを登録します。

    ```cshtml
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

    上記のコードでは、`<feature>` タグ ヘルパーをプロジェクトの *.cshtml* ファイルで使用できます。

1. *Views*\\*Shared* ディレクトリの *_Layout.cshtml* を開きます。 `<body>` > `<header>` の下の `<nav>` バー コードを見つけます。 次の強調表示されている行に示すように、*Home* と *Privacy* navbar 項目の間に新しい `<feature>` タグを挿入します。

    :::code language="html" source="../../includes/azure-app-configuration-navbar.md" range="15-38" highlight="14-18":::

1. *Views/Beta* ディレクトリと、次のマークアップを含む *Index.cshtml* ファイルを作成します。

    ```cshtml
    @{
        ViewData["Title"] = "Beta Home Page";
    }

    <h1>This is the beta website.</h1>
    ```

## <a name="build-and-run-the-app-locally"></a>アプリをビルドしてローカルで実行する

1. .NET Core CLI を使用してアプリケーションをビルドするには、コマンド シェルで次のコマンドを実行します。

    ```dotnetcli
    dotnet build
    ```

1. ビルドが正常に完了したら、次のコマンドを実行して、Web アプリをローカルで実行します。

    ```dotnetcli
    dotnet run
    ```

1. ブラウザー ウィンドウを開いて、`http://localhost:5000` (ローカルでホストされた Web アプリの既定の URL) に移動します。 Azure Cloud Shell で作業している場合は、 **[Web プレビュー]** 、 **[構成]** の順に選択します。 確認を求められたら、ポート 5000 を選択します。

    ![[Web プレビュー] ボタンを見つける](./media/quickstarts/cloud-shell-web-preview.png)

    ブラウザーに、次の画像のようなページが表示されます。

    :::image type="content" source="media/quickstarts/aspnet-core-feature-flag-local-before.png" alt-text="変更前のローカル クイックスタート アプリ" border="true":::

1. [Azure portal](https://portal.azure.com) にサインインします。 **[すべてのリソース]** を選択し、クイック スタートで作成した App Configuration ストア インスタンスを選択します。

1. **[機能マネージャー]** を選択します。 

1. **[有効]** でチェック ボックスをオンにして、*Beta* フラグを有効にします。

1. コマンド シェルに戻ります。 <kbd>Ctrl + C</kbd> キーを押して、実行中の `dotnet` プロセスをキャンセルします。 `dotnet run` を使用してアプリを再起動します。

1. ブラウザー ページを最新の情報に更新して新しい構成設定を確認します。

    :::image type="content" source="media/quickstarts/aspnet-core-feature-flag-local-after.png" alt-text="変更後のローカル クイックスタート アプリ" border="true":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE[Azure App Configuration cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、新しい App Configuration ストアを作成し、この構成ストアを使用して、[機能管理ライブラリ](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration)を介して ASP.NET Core Web アプリの機能を管理しました。

* [機能管理](./concept-feature-management.md)の詳細を確認します。
* [機能フラグを管理](./manage-feature-flags.md)します。
* [ASP.NET Core アプリ内で機能フラグを使用](./use-feature-flags-dotnet-core.md)します。
* [ASP.NET Core アプリで動的な構成を使用する](./enable-dynamic-configuration-aspnet-core.md)