---
title: .NET Core アプリでの機能フラグの使用に関するチュートリアル |Microsoft Docs
description: このチュートリアルでは、.NET Core アプリで機能フラグを実装する方法について説明します。
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 99559c0c77c3e4b29badec1c0be2d741df1f0621
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798375"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>チュートリアル:ASP.NET Core アプリ内で機能フラグを使用する

.NET Core 機能管理ライブラリでは、.NET または ASP.NET Core アプリケーションで機能フラグを実装するための慣用的なサポートが提供されます。 これらのライブラリにより、機能フラグを宣言的にコードに追加できるため、それらに対するすべての `if` ステートメントを手動で記述する必要がなくなります。

機能管理ライブラリは、バックグラウンドで機能フラグのライフサイクルも管理します。 たとえば、ライブラリは、フラグの状態を更新およびキャッシュしたり、要求の呼び出し中にフラグの状態が不変であることを保証したりします。 さらに、ASP.NET Core ライブラリには、MVC コントローラー アクション、ビュー、ルート、およびミドルウェアを含む、すぐに使用できる統合が用意されています。

「[クイック スタート: ASP.NET Core アプリに機能フラグを追加する](./quickstart-feature-flag-aspnet-core.md)」では、ASP.NET Core アプリケーションに機能フラグを追加する複数の方法が示されています。 このチュートリアルでは、それらの方法についてより詳細に説明します。 完全なリファレンスについては、[ASP.NET Core の機能管理に関するドキュメント](https://go.microsoft.com/fwlink/?linkid=2091410)をご覧ください。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * アプリケーションの主要部分に機能フラグを追加して、機能の可用性を制御します。
> * 機能フラグを管理するのに App Configuration を使用する場合は、それと統合します。

## <a name="set-up-feature-management"></a>機能管理を設定する

.NET Core 機能マネージャー `IFeatureManager` は、フレームワークのネイティブ構成システムから機能フラグを取得します。 その結果、.NET Core がサポートする任意の構成ソース (ローカルの *appsettings.json* ファイルや環境変数など) を使用して、アプリケーションの機能フラグを定義できます。 `IFeatureManager` は、.NET Core の依存関係の挿入に依存します。 機能管理サービスは、標準の規則を使用して登録できます。

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
}
```

機能マネージャーは、既定では .NET Core 構成データの `"FeatureManagement"` セクションから機能フラグを取得します。 次の例では、代わりに `"MyFeatureFlags"` という別のセクションから読み取るように機能マネージャーに指示しています。

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement(options =>
        {
                options.UseConfiguration(Configuration.GetSection("MyFeatureFlags"));
        });
    }
}
```

機能フラグでフィルターを使用する場合は、追加のライブラリを含めて、それを登録する必要があります。 次の例は、`PercentageFilter` という組み込み機能フィルターを使用する方法を示しています。

```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

機能フラグはアプリケーションの外部で保持し、別々に管理することをお勧めします。 そうすることで、フラグの状態をいつでも変更して、アプリケーションにその変更をすぐに反映させることができます。 App Configuration は、専用のポータル UI を介してすべての機能フラグを一元的に整理および制御するための場所を提供します。 また、App Configuration は、その .NET Core クライアント ライブラリを介してお使いのアプリケーションに直接フラグを配信します。

App Configuration に ASP.NET Core アプリケーションを接続する最も簡単な方法は、構成プロバイダー `Microsoft.Azure.AppConfiguration.AspNetCore` を使用することです。 NuGet パッケージを使用するには次の手順に従います。

1. *Program.cs* ファイルを開いて、次のコードを追加します。

   ```csharp
   using Microsoft.Extensions.Configuration.AzureAppConfiguration;

   public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
       WebHost.CreateDefaultBuilder(args)
              .ConfigureAppConfiguration((hostingContext, config) => {
                  var settings = config.Build();
                  config.AddAzureAppConfiguration(options => {
                      options.Connect(settings["ConnectionStrings:AppConfig"])
                             .UseFeatureFlags();
                   });
              })
              .UseStartup<Startup>();
   ```

2. *Startup.cs* を開き、`Configure` メソッドを更新してミドルウェアを追加し、ASP.NET Core Web アプリで要求の受信が続けられている間、定期的に機能フラグの値を更新できるようにします。

   ```csharp
   public void Configure(IApplicationBuilder app, IHostingEnvironment env)
   {
       app.UseAzureAppConfiguration();
       app.UseMvc();
   }
   ```

機能フラグの値は、時間の経過とともに変化することが予想されます。 既定では、機能フラグの値は 30 秒間キャッシュされるため、ミドルウェアで要求が受け取られるときにトリガーされる更新操作では、キャッシュされた値の有効期限が切れるまで、値は更新されません。 次のコードは、`options.UseFeatureFlags()` 呼び出しでキャッシュの有効期間またはポーリング間隔を 5 分に変更する方法を示しています。

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.CacheExpirationTime = TimeSpan.FromMinutes(5);
           });
});
```

## <a name="feature-flag-declaration"></a>機能フラグの宣言

各機能フラグには 2 つの部分があります。1 つは名前で、もう 1 つは、機能の状態が "*オン*" である (つまり、その値が `True` である) かどうかの評価に使用される 1 つ以上のフィルターの一覧です。 フィルターは、機能をオンにするタイミングに関するユース ケースを定義します。

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
* `FeatureC` は、`Parameters` プロパティとともに `Percentage` という名前のフィルターを指定します。 `Percentage` は、構成可能なフィルターです。 この例で、`Percentage` は、`FeatureC` フラグが "*オン*" になる確率を 50% に指定しています。

## <a name="feature-flag-references"></a>機能フラグの参照

コードで機能フラグを容易に参照できるように、それらを `enum` 変数として定義する必要があります。

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-checks"></a>機能フラグのチェック

機能管理の基本的なパターンでは、最初に機能フラグが "*オン*" に設定されているかどうかをチェックします。 そうなっている場合は、機能マネージャーが、機能に含まれているアクションを実行します。 例:

```csharp
IFeatureManager featureManager;
...
if (featureManager.IsEnabled(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>依存関係の挿入

ASP.NET Core MVC では、依存関係の挿入を介して機能マネージャー `IFeatureManager` にアクセスできます。

```csharp
public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

## <a name="controller-actions"></a>コントローラー アクション

MVC コントローラーでは、`FeatureGate` 属性を使用して、コントローラー クラス全体を有効にするか、特定のアクションを有効にするかを制御できます。 次の `HomeController` コントローラーでは、コントローラー クラスに含まれるアクションを実行するには、`FeatureA` が "*オン*" になっている必要があります。

```csharp
[FeatureGate(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

次の `Index` アクションを実行するには、`FeatureA` が "*オン*" になっている必要があります。

```csharp
[FeatureGate(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

制御する機能フラグが "*オフ*" であるために MVC コントローラーまたはアクションがブロックされている場合、登録されている `IDisabledFeaturesHandler` インターフェイスが呼び出されます。 既定の `IDisabledFeaturesHandler` インターフェイスは、応答本文なしで 404 状態コードをクライアントに返します。

## <a name="mvc-views"></a>MVC ビュー

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

MVC フィルターは、機能フラグの状態に基づいてアクティブになるように設定できます。 以下のコードは、`SomeMvcFilter` という名前の MVC フィルターを追加します。 このフィルターは、`FeatureA` が有効になっている場合のみ、MVC パイプライン内でトリガーされます。

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<SomeMvcFilter>(nameof(MyFeatureFlags.FeatureA));
    });
}
```

## <a name="routes"></a>Routes

機能フラグを使用して、ルートを動的に公開することができます。 以下のコードは、`FeatureA` が有効になっている場合にのみ、`Beta` を既定のコントローラーとして設定するルートを追加します。

```csharp
app.UseMvc(routes => {
    routes.MapRouteForFeature(nameof(MyFeatureFlags.FeatureA), "betaDefault", "{controller=Beta}/{action=Index}/{id?}");
});
```

## <a name="middleware"></a>ミドルウェア

機能フラグを使用して、アプリケーションの分岐とミドルウェアを条件付きで追加することもできます。 以下のコードは、`FeatureA` が有効になっている場合にのみ、要求パイプラインにミドルウェア コンポーネントを挿入します。

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
```

以下のコードは、機能フラグに基づいてアプリケーション全体を分岐させる、より汎用的な機能を構築します。

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、`Microsoft.FeatureManagement` ライブラリを使用して ASP.NET Core アプリケーションで機能フラグを実装する方法を説明しました。 ASP.NET Core と App Configuration での機能管理サポートの詳細については、次のリソースをご覧ください。

* [ASP.NET Core の機能フラグのサンプル コード](/azure/azure-app-configuration/quickstart-feature-flag-aspnet-core)
* [Microsoft.FeatureManagement のドキュメント](https://docs.microsoft.com/dotnet/api/microsoft.featuremanagement)
* [機能フラグを管理する](./manage-feature-flags.md)
