---
title: .NET Azure Functions で依存関係の挿入を使用する
description: .NET 関数にサービスを登録して使用するために、依存関係の挿入を使用する方法について学習します
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure 関数、関数、サーバーレス アーキテクチャ
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: 09bcce6daf519c7d5e99c7c120064f5c8bb92475
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996874"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>.NET Azure Functions で依存関係の挿入を使用する

Azure Functions では、依存関係の挿入 (DI) ソフトウェア デザイン パターンがサポートされています。これは、クラスと依存関係の間で[制御の反転 (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) を実現するための技術です。

- Azure Functions の依存関係挿入は、.NET Core の依存関係挿入機能を基盤としています。 [.NET Core 依存関係挿入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)について理解していることが推奨されます。 ただし、依存関係のオーバーライド方法と、従量課金プランで Azure Functions により構成値を読み取る方法に違いがあります。

- 依存関係の挿入のサポートは、Azure Functions 2.x から開始されます。

## <a name="prerequisites"></a>前提条件

依存関係の挿入を使用する前に、次の NuGet パッケージをインストールする必要があります。

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Microsoft.NET.Sdk.Functions パッケージ](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) バージョン 1.0.28 以降

## <a name="register-services"></a>サービスを登録する

サービスを登録するには、構成用のメソッドを作成し、`IFunctionsHostBuilder` インスタンスにコンポーネントを追加します。  Azure Functions ホストにより、`IFunctionsHostBuilder` のインスタンスが作成され、メソッドに直接渡されます。

このメソッドを登録するには、起動時に使用される型名を指定する `FunctionsStartup` アセンブリ属性を追加します。

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();

            builder.Services.AddSingleton((s) => {
                return new MyService();
            });

            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

### <a name="caveats"></a>注意事項

ランタイム前後の一連の登録手順実行によりスタートアップ クラスが処理されます。 そのため、以下の点に注意してください。

- *スタートアップ クラスは設定と登録だけを目的とします。* スタートアップ プロセス中、スタートアップ時に登録されるサービスを使用しないでください。 たとえば、スタートアップ中に登録されているロガーにメッセージを記録しないでください。 登録プロセスのこの時点は、サービスを利用するには早すぎます。 `Configure` メソッドが実行されると、Functions ランタイムは追加の依存関係を引き続き登録します。これがサービスの動作に影響を与える可能性があります。

- *依存関係挿入コンテナーでは、明示的に登録された型のみが保持されます*。 挿入可能な型として利用できる唯一のサービスは `Configure` メソッド内で設定されるサービスです。 結果的に、`BindingContext` や `ExecutionContext` のような Functions 固有の型は設定中に利用できず、また、挿入可能な型として利用できません。

## <a name="use-injected-dependencies"></a>挿入された依存関係を使用する

コンストラクターの挿入は、依存関係を関数で利用できるようにする目的で使用されます。 コンストラクターの挿入を使用するには、静的クラスを使用しないことが必須となります。

次のサンプルでは、`IMyService` と `HttpClient` の依存関係が、HTTP によってトリガーされる関数にどのように挿入されるかを示します。 この例では、スタートアップ時に `HttpClient` を登録するために必要な [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) パッケージが使用されています。

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, IHttpClientFactory httpClientFactory)
        {
            _service = service;
            _client = httpClientFactory.CreateClient();
        }

        [FunctionName("GetPosts")]
        public async Task<IActionResult> Get(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "posts")] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            var res = await _client.GetAsync("https://microsoft.com");
            await _service.AddResponse(res);

            return new OkResult();
        }
    }
}
```

## <a name="service-lifetimes"></a>サービスの有効期間

Azure Functions アプリのサービス有効期間は [ASP.NET 依存関係挿入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes)と同じになります。 Functions アプリの場合、各種サービス有効期間が次のように動作します。

- **一時的**:一時的なサービスは、サービスが要求されるたびに作成されます。
- **スコープ付き**:スコープ付きサービスの有効期間は、関数実行の有効期間に一致します。 スコープ付きサービスは毎回作成されます。 実行時のそのサービスに対する後続の要求では、既存のサービス インスタンスが再利用されます。
- **シングルトン**:シングルトン サービスの有効期間はホストの有効期間に一致し、そのインスタンスでの関数実行間で再利用されます。 シングルトン サービスの有効期間は、`SqlConnection` インスタンスや `HttpClient` インスタンスなど、接続やクライアントに推奨されます。

GitHub の[さまざまなサービスの有効期間のサンプル](https://aka.ms/functions/di-sample)を表示するか、ダウンロードします。

## <a name="logging-services"></a>ログ記録サービス

独自のログ記録プロバイダーが必要であれば、カスタムの型を `ILoggerProvider` インスタンスとして登録します。 Azure Functions によって Application Insights が自動的に追加されます。

> [!WARNING]
> - サービス コレクションに `AddApplicationInsightsTelemetry()` を追加しないでください。環境によって提供されるサービスと競合するサービスが登録されます。
> - 組み込みの Application Insights 機能を使用している場合、独自の `TelemetryConfiguration` または `TelemetryClient` を登録しないでください。

## <a name="function-app-provided-services"></a>関数アプリで提供されるサービス

関数ホストは、多くのサービスを登録します。 次のサービスは、お使いのアプリケーションに依存関係として取り込んでも安全です。

|サービスの種類|有効期間|説明|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|シングルトン|実行時の構成|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|シングルトン|ホスト インスタンスの ID を提供する責任がある|

依存関係を取り込みたい他のサービスがある場合は、[問題を作成し、GitHub でそれらを提案してください](https://github.com/azure/azure-functions-host)。

### <a name="overriding-host-services"></a>ホスト サービスのオーバーライド

ホストによって提供されるサービスのオーバーライドは、現在サポートされていません。  オーバーライドしたいサービスがある場合は、[問題を作成し、GitHub でそれらを提案してください](https://github.com/azure/azure-functions-host)。

## <a name="working-with-options-and-settings"></a>オプションと設定の使用

[アプリ設定](./functions-how-to-use-azure-function-app-settings.md#settings)に定義されている値は `IConfiguration` インスタンスで利用できます。それにより、スタートアップ クラスのアプリ設定値を読み取ることができます。

`IConfiguration` インスタンスからカスタムの型に値を抽出できます。 アプリ設定値をカスタムの型にコピーすると、その値が挿入可能になり、サービスのテストが簡単になります。 次のクラスについて考えてください。名前がアプリ設定と一致するプロパティが含まれています。

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

`Startup.Configure` メソッド内から次のコードを使用し、`IConfiguration` インスタンスの値をカスタムの型に抽出することができます。

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.Bind(settings);
                                           });
```

`Bind` を呼び出すと、プロパティ名が一致する値が構成からカスタム インスタンスにコピーされます。 これで IoC コンテナーで options インスタンスを関数に挿入できるようになりました。

options オブジェクトは、汎用 `IOptions` インターフェイスのインスタンスとして関数に挿入されます。 `Value` プロパティを使用し、構成で見つかった値にアクセスします。

```csharp
using System;
using Microsoft.Extensions.Options;

public class HttpTrigger
{
    private readonly MyOptions _settings;

    public HttpTrigger(IOptions<MyOptions> options)
    {
        _service = service;
        _settings = options.Value;
    }
}
```

オプションの使用に関する詳細については、「[ASP.NET Core のオプション パターン](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)」を参照してください。

## <a name="next-steps"></a>次の手順

詳細については、次のリソースを参照してください。

- [関数アプリを監視する方法](functions-monitoring.md)
- [関数のベスト プラクティス](functions-best-practices.md)
