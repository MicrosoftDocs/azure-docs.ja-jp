---
title: Azure App Configuration の動的な構成を ASP.NET Core アプリで使用するためのチュートリアル | Microsoft Docs
description: このチュートリアルでは、ASP.NET Core アプリの構成データを動的に更新する方法を学習します。
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 9eccb4ca505dac312dd22123a3585863c67f3ad7
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359863"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>チュートリアル:ASP.NET Core アプリで動的な構成を使用する

ASP.NET Core には、さまざまなソースから構成データを読み取ることができるプラグ可能な構成システムがあります。 アプリケーションを再起動せずに、その場で変更を処理できます。 ASP.NET Core では、厳密に型指定された .NET クラスへの構成設定のバインドがサポートされています。 さまざまな `IOptions<T>` パターンを使用して、それらをコードに挿入します。 そうしたパターンの 1 つである `IOptionsSnapshot<T>` では、基になるデータが変化したときに、アプリケーションの構成が自動的にリロードされます。 アプリケーションのコントローラーに `IOptionsSnapshot<T>` を挿入すれば、Azure App Configuration に格納されている最新の構成にアクセスすることができます。

ミドルウェアを使って構成設定のセットを動的に更新するよう、App Configuration ASP.NET Core クライアント ライブラリを設定することもできます。 Web アプリで要求の受信が続けられている限り、構成設定は継続的に構成ストアで更新されます。

設定の更新を維持しながら、構成ストアの呼び出しが多くなりすぎないようにするため、キャッシュが各設定に使用されます。 設定のキャッシュされた値の有効期限が切れるまで、構成ストアの値が変更された場合でも、更新操作で値は更新されません。 各要求の既定の有効期間は 30 秒ですが、必要な場合はオーバーライドできます。

このチュートリアルでは、自分が作成するコードに、構成の動的更新を実装する方法について説明します。 これは、クイック スタートで紹介されている Web アプリに基づいています。 先に進む前に、[App Configuration を使用した ASP.NET Core アプリの作成](./quickstart-aspnet-core-app.md)を完了しておいてください。

このチュートリアルの手順は、任意のコード エディターを使用して実行できます。 推奨のエディターは [Visual Studio Code](https://code.visualstudio.com/) です (Windows、macOS、および Linux プラットフォームで使用できます)。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * アプリ構成ストアへの変更に合わせて構成を更新するようにアプリケーションを設定する。
> * アプリケーションのコントローラーに最新の構成を挿入する。

## <a name="prerequisites"></a>前提条件

このチュートリアルを実行するには、[.NET Core SDK](https://dotnet.microsoft.com/download) をインストールします。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>App Configuration からデータを再度読み込む

1. *Program.cs* を開き、`config.AddAzureAppConfiguration()` メソッドを追加して `CreateWebHostBuilder` メソッドを更新します。

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
                               refresh.Register("TestApp:Settings:BackgroundColor")
                                      .Register("TestApp:Settings:FontColor")
                                      .Register("TestApp:Settings:Message")
                           });
                }
            })
            .UseStartup<Startup>();
    ```

    更新操作がトリガーされたときに、構成データをアプリ構成ストアで更新するために使用する設定を指定するには、`ConfigureRefresh` メソッドを使います。 実際に更新操作をトリガーするには、変更が発生したら構成データを更新するように、アプリケーションに対して更新ミドルウェアを構成する必要があります。

2. 新しい `Settings` クラスを定義して実装する *Settings.cs* ファイルを追加します。

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

3. *Startup.cs* を開き、構成データを `Settings` クラスにバインドするように `ConfigureServices` メソッドを更新します。

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

4. `Configure` メソッドを更新してミドルウェアを追加し、ASP.NET Core Web アプリで要求の受信が続けられている間、更新対象に登録された構成設定を更新できるようにします。

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();
        app.UseMvc();
    }
    ```
    
    ミドルウェアでは、`Program.cs` の `AddAzureAppConfiguration` メソッドで指定されている更新の構成を使って、ASP.NET Core Web アプリによって受信された各要求の更新がトリガーされます。 要求ごとに、更新操作がトリガーされ、登録されている構成設定のキャッシュされた値の有効期限が切れているかどうかが、クライアント ライブラリによって確認されます。 キャッシュされた値の有効期限が切れている設定については、アプリ構成ストアで値が更新され、それ以外の値は変更されません。
    
    > [!NOTE]
    > 構成設定の既定のキャッシュ有効期限は 30 秒ですが、`ConfigureRefresh` メソッドへの引数として渡されるオプション初期化子で `SetCacheExpiration` メソッドを呼び出すことにより、オーバーライドできます。

## <a name="use-the-latest-configuration-data"></a>最新の構成データを使用する

1. Controllers ディレクトリにある *HomeController.cs* を開いて、`Microsoft.Extensions.Options` パッケージへの参照を追加します。

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. `HomeController` クラスを更新して、依存関係の挿入を通じて `Settings` を受け取り、その値を利用するようにします。

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
            font-size: @ViewData["FontSize"];
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

        dotnet build

2. ビルドが正常に完了したら、次のコマンドを実行して、Web アプリをローカルで実行します。

        dotnet run

3. ブラウザー ウィンドウを開いて、`http://localhost:5000` (ローカルでホストされた Web アプリの既定の URL) に移動します。

    ![クイック スタートのアプリ (ローカルで起動)](./media/quickstarts/aspnet-core-app-launch-local-before.png)

4. [Azure Portal](https://portal.azure.com) にサインインします。 **[すべてのリソース]** を選択し、クイック スタートで作成したアプリ構成ストア インスタンスを選択します。

5. **[Configuration Explorer]\(構成エクスプローラー)** を選択して次のキーの値を更新します。

    | キー | 値 |
    |---|---|
    | TestApp:Settings:BackgroundColor | green |
    | TestApp:Settings:FontColor | lightGray |
    | TestApp:Settings:Message | Data from Azure App Configuration - now with live updates! |

6. ブラウザー ページを最新の情報に更新して新しい構成設定を確認します。 変更を反映するには、ブラウザー ページの複数の更新が必要な場合があります。

    ![クイック スタートのアプリ (ローカルで最新の情報に更新)](./media/quickstarts/aspnet-core-app-launch-local-after.png)
    
    > [!NOTE]
    > 構成設定は既定の有効期限 30 秒でキャッシュされるので、アプリ構成ストアの設定に対する変更は、キャッシュの有効期限が切れたときにのみ、Web アプリに反映されます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure マネージド サービス ID を追加して、App Configuration へのアクセスを効率化し、アプリの資格情報管理を改善しました。 App Configuration の使用方法の詳細については、Azure CLI のサンプルに進んでください。

> [!div class="nextstepaction"]
> [CLI のサンプル](./cli-samples.md)
