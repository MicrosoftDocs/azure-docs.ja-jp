---
title: .NET Core アプリでの機能フラグの使用に関するチュートリアル |Microsoft Docs
description: このチュートリアルでは、.NET Core アプリで機能フラグを実装する方法について説明します。
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/17/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 327bc687c466a30d4f92810e48dc08f822f752ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101726429"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>チュートリアル:ASP.NET Core アプリ内で機能フラグを使用する

.NET Core 機能管理ライブラリでは、.NET または ASP.NET Core アプリケーションで機能フラグを実装するための慣用的なサポートが提供されます。 これらのライブラリにより、機能フラグを宣言的にコードに追加できるため、`if` ステートメントで機能を有効にしたり無効にしたりするコードを手動で記述する必要がなくなります。

機能管理ライブラリは、バックグラウンドで機能フラグのライフサイクルも管理します。 たとえば、ライブラリは、フラグの状態を更新およびキャッシュしたり、要求の呼び出し中にフラグの状態が不変であることを保証したりします。 さらに、ASP.NET Core ライブラリには、MVC コントローラー アクション、ビュー、ルート、およびミドルウェアを含む、すぐに使用できる統合が用意されています。

「[クイックスタート: ASP.NET Core アプリに機能フラグを追加する](./quickstart-feature-flag-aspnet-core.md)」では、ASP.NET Core アプリケーションで機能フラグを使用する方法の簡単な例を紹介しています。 それに加えて、このチュートリアルでは、機能管理ライブラリのセットアップ オプションや機能を紹介しています。 クイックスタートで作成したサンプル アプリを使用して、このチュートリアルのサンプル コードを試してみてください。 

ASP.NET Core 機能管理 API のリファレンス ドキュメントについては、「[Microsoft.FeatureManagement 名前空間](/dotnet/api/microsoft.featuremanagement)」を参照してください。

このチュートリアルでは、次の内容を学習します。

> [!div class="checklist"]
> * アプリケーションの主要部分に機能フラグを追加して、機能の可用性を制御します。
> * 機能フラグを管理するのに App Configuration を使用する場合は、それと統合します。

## <a name="set-up-feature-management"></a>機能管理を設定する

.NET Core 機能マネージャーにアクセスするには、`Microsoft.FeatureManagement.AspNetCore` NuGet パッケージへの参照をアプリに追加する必要があります。

.NET Core 機能マネージャーの構成は、フレームワークのネイティブ構成システムから行います。 その結果、.NET Core がサポートする任意の構成ソース (ローカルの *appsettings.json* ファイルや環境変数など) を使用して、アプリケーションの機能フラグ設定を定義できます。

機能マネージャーは、既定では .NET Core 構成データの `"FeatureManagement"` セクションから機能フラグの構成を取得します。 既定の構成の場所を使用するには、**Startup** クラスの **ConfigureServices** メソッドに渡された **IServiceCollection** の [AddFeatureManagement](/dotnet/api/microsoft.featuremanagement.servicecollectionextensions.addfeaturemanagement) メソッドを呼び出します。


```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement();
    }
}
```

その機能管理構成を別の構成セクションから取得するように指定するには、目的のセクションの名前を [Configuration.GetSection](/dotnet/api/microsoft.web.administration.configuration.getsection) に渡して呼び出します。 次の例では、代わりに `"MyFeatureFlags"` という別のセクションから読み取るように機能マネージャーに指示しています。

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement(Configuration.GetSection("MyFeatureFlags"));
    }
}
```


機能フラグにフィルターを使用する場合は、[Microsoft.FeatureManagement.FeatureFilters](/dotnet/api/microsoft.featuremanagement.featurefilters) 名前空間をインクルードし、[AddFeatureFilters](/dotnet/api/microsoft.featuremanagement.ifeaturemanagementbuilder.addfeaturefilter) の呼び出しを追加して、メソッドのジェネリック型として使用するフィルターの型名を指定する必要があります。 機能フィルターを使用して動的に機能を有効化または無効化する方法について詳しくは、「[対象ユーザーに対する機能の段階的なロールアウトを有効にする](./howto-targetingfilter-aspnet-core.md)」を参照してください。

次の例は、`PercentageFilter` という組み込み機能フィルターを使用する方法を示しています。



```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

機能フラグはアプリケーションにハード コーディングするのではなく、アプリケーションの外部で保持し、別々に管理することをお勧めします。 そうすることで、フラグの状態をいつでも変更して、アプリケーションにその変更をすぐに反映させることができます。 Azure App Configuration サービスには、すべての機能フラグを管理できる専用のポータル UI が用意されています。 また、Azure App Configuration サービスは、その .NET Core クライアント ライブラリを介して直接、お使いのアプリケーションに機能フラグを配信します。

App Configuration に ASP.NET Core アプリケーションを接続する最も簡単な方法は、`Microsoft.Azure.AppConfiguration.AspNetCore` NuGet パッケージに含まれている構成プロバイダーを使用することです。 パッケージへの参照を追加した後、次の手順に従ってこの NuGet パッケージを使用します。

1. *Program.cs* ファイルを開いて、次のコードを追加します。
    > [!IMPORTANT]
    > `CreateHostBuilder` は、.NET Core 3.x の `CreateWebHostBuilder` に代わるものです。 お使いの環境に応じて適切な構文を選択します。

    ### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
                }).UseStartup<Startup>());
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration(config =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
            }).UseStartup<Startup>());
    ```
        
    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                    .ConfigureAppConfiguration(config =>
                    {
                        var settings = config.Build();
                        config.AddAzureAppConfiguration(options =>
                            options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
                    }).UseStartup<Startup>();
    ```
    ---

2. *Startup.cs* を開き、`Configure` および `ConfigureServices` メソッドを更新して、`UseAzureAppConfiguration` と呼ばれる組み込みのミドルウェアを追加します。 このミドルウェアを使用すると、ASP.NET Core Web アプリで要求の受信が続けられている間、定期的に機能フラグの値を更新できます。



    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        app.UseAzureAppConfiguration();
    }
    ```

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddAzureAppConfiguration();
   }
   ```
   
標準的なシナリオでは、アプリケーションのさまざまな機能をデプロイして有効にする過程で、機能フラグの値を定期的に更新します。 既定では、機能フラグの値は 30 秒間キャッシュされるため、ミドルウェアで要求が受け取られるときにトリガーされる更新操作では、キャッシュされた値の有効期限が切れるまで、値は更新されません。 次のコードは、**UseFeatureFlags** 呼び出しの [CacheExpirationInterval](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.featuremanagement.featureflagoptions.cacheexpirationinterval) を選択することによって、キャッシュの有効期間またはポーリング間隔を 5 分に変更する方法を示しています。


    
```csharp
config.AddAzureAppConfiguration(options =>
    options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags(featureFlagOptions => {
        featureFlagOptions.CacheExpirationInterval = TimeSpan.FromMinutes(5);
    }));
});
```


## <a name="feature-flag-declaration"></a>機能フラグの宣言

各機能フラグの宣言には 2 つの部分があります。1 つは名前で、もう 1 つは、機能の状態が "*オン*" である (つまり、その値が `True` である) かどうかの評価に使用される 1 つ以上のフィルターの一覧です。 フィルターは、機能をオンにするタイミングの基準を定義します。

機能フラグに複数のフィルターがある場合は、フィルターのいずれかが、機能を有効にする必要があると判断するまで、フィルター一覧が順番に走査されます。 その時点で、機能フラグが "*オン*" になり、残りのフィルターの結果はすべてスキップされます。 どのフィルターも、機能を有効にする必要があると示していない場合、機能フラグは "*オフ*" になります。

機能マネージャーは、*appsettings.json* を機能フラグの構成ソースとしてサポートしています。 次の例は、JSON ファイルでの機能フラグの設定方法を示しています。

```JSON
"FeatureManagement": {
    "FeatureA": true, // Feature flag set to on
    "FeatureB": false, // Feature flag set to off
    "FeatureC": {
        "EnabledFor": [
            {
                "Name": "Percentage",
                "Parameters": {
                    "Value": 50
                }
            }
        ]
    }
}
```

慣例により、この JSON ドキュメントの `FeatureManagement` セクションが機能フラグの設定に使用されます。 前述の例は、`EnabledFor` プロパティにフィルターが定義されている 3 つの機能フラグを示しています。

* `FeatureA` は "*オン*" です。
* `FeatureB` は "*オフ*" です。
* `FeatureC` は、`Parameters` プロパティとともに `Percentage` という名前のフィルターを指定します。 `Percentage` は、構成可能なフィルターです。 この例で、`Percentage` は、`FeatureC` フラグが "*オン*" になる確率を 50% に指定しています。 機能フィルターの使用に関する攻略ガイドについては、「[機能フィルターを使用して条件付き機能フラグを有効にする](./howto-feature-filters-aspnet-core.md)」を参照してください。




## <a name="use-dependency-injection-to-access-ifeaturemanager"></a>依存関係の挿入を使用して IFeatureManager にアクセスする 

機能フラグの値を手動でチェックするなど一部の操作では、[IFeatureManager](/dotnet/api/microsoft.featuremanagement.ifeaturemanager?view=azure-dotnet-preview) のインスタンスを取得する必要があります。 ASP.NET Core MVC では、依存関係の挿入を介して機能マネージャー `IFeatureManager` にアクセスできます。 以下の例では、コントローラーのコンストラクターのシグネチャに `IFeatureManager` 型の引数が追加されています。 このランタイムは、コンストラクターを呼び出す際に、参照を自動的に解決してインターフェイスを提供します。 最初からコントローラーのコンストラクターに依存関係の挿入のための引数が少なくとも 1 つあるようなアプリケーション テンプレートを使用している場合は (`ILogger` など)、単純に引数として `IFeatureManager` を追加することができます。

### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)
    
```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(ILogger<HomeController> logger, IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(ILogger<HomeController> logger, IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```
    
### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

---

## <a name="feature-flag-references"></a>機能フラグの参照

機能フラグをコードから参照するためには、それらを文字列変数として定義します。

```csharp
public static class MyFeatureFlags
{
    public const string FeatureA = "FeatureA";
    public const string FeatureB = "FeatureB";
    public const string FeatureC = "FeatureC";
}
```

## <a name="feature-flag-checks"></a>機能フラグのチェック

機能フラグが "*オン*" に設定されているかどうかをチェックし、もしそうなっていたら、特定のコード セクションを実行するというのが、機能管理の一般的なパターンです。 次に例を示します。

```csharp
IFeatureManager featureManager;
...
if (await featureManager.IsEnabledAsync(MyFeatureFlags.FeatureA))
{
    // Run the following code
}
```

## <a name="controller-actions"></a>コントローラー アクション

MVC コントローラーでは、`FeatureGate` 属性を使用して、コントローラー クラス全体を有効にするか、特定のアクションを有効にするかを制御できます。 次の `HomeController` コントローラーでは、コントローラー クラスに含まれるアクションを実行するには、`FeatureA` が "*オン*" になっている必要があります。

```csharp
using Microsoft.FeatureManagement.Mvc;

[FeatureGate(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

次の `Index` アクションを実行するには、`FeatureA` が "*オン*" になっている必要があります。

```csharp
using Microsoft.FeatureManagement.Mvc;

[FeatureGate(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

制御する機能フラグが "*オフ*" であるために MVC コントローラーまたはアクションがブロックされている場合、登録されている [IDisabledFeaturesHandler](/dotnet/api/microsoft.featuremanagement.mvc.idisabledfeatureshandler?view=azure-dotnet-preview) インターフェイスが呼び出されます。 既定の `IDisabledFeaturesHandler` インターフェイスは、応答本文なしで 404 状態コードをクライアントに返します。

## <a name="mvc-views"></a>MVC ビュー

*Views* ディレクトリの *_ViewImports.cshtml* を開き、機能マネージャーのタグ ヘルパーを追加します。

```html
@addTagHelper *, Microsoft.FeatureManagement.AspNetCore
```

MVC ビューでは、`<feature>` タグを使用して、機能フラグが有効かどうかに基づいてコンテンツをレンダリングできます。

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

要件が満たされていないときに代替コンテンツを表示するには、`negate` 属性を使用できます。

```html
<feature name="FeatureA" negate="true">
    <p>This will be shown if 'FeatureA' is disabled.</p>
</feature>
```

一覧内のいずれかまたはすべての機能が有効になっている場合は、機能 `<feature>` タグを使用してコンテンツを表示することもできます。

```html
<feature name="FeatureA, FeatureB" requirement="All">
    <p>This can only be seen if 'FeatureA' and 'FeatureB' are enabled.</p>
</feature>
<feature name="FeatureA, FeatureB" requirement="Any">
    <p>This can be seen if 'FeatureA', 'FeatureB', or both are enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>MVC フィルター

MVC フィルターは、機能フラグの状態に基づいてアクティブになるように設定できます。 この機能は、[IAsyncActionFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncactionfilter) を実装するフィルターに限定されます。 以下のコードは、`ThirdPartyActionFilter` という名前の MVC フィルターを追加します。 このフィルターは、`FeatureA` が有効になっている場合のみ、MVC パイプライン内でトリガーされます。  

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<ThirdPartyActionFilter>(MyFeatureFlags.FeatureA);
    });
}
```

## <a name="middleware"></a>ミドルウェア

機能フラグを使用して、アプリケーションの分岐とミドルウェアを条件付きで追加することもできます。 以下のコードは、`FeatureA` が有効になっている場合にのみ、要求パイプラインにミドルウェア コンポーネントを挿入します。

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(MyFeatureFlags.FeatureA);
```

以下のコードは、機能フラグに基づいてアプリケーション全体を分岐させる、より汎用的な機能を構築します。

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、`Microsoft.FeatureManagement` ライブラリを使用して ASP.NET Core アプリケーションで機能フラグを実装する方法を説明しました。 ASP.NET Core と App Configuration での機能管理サポートの詳細については、次のリソースをご覧ください。

* [ASP.NET Core の機能フラグのサンプル コード](./quickstart-feature-flag-aspnet-core.md)
* [Microsoft.FeatureManagement のドキュメント](/dotnet/api/microsoft.featuremanagement)
* [機能フラグを管理する](./manage-feature-flags.md)