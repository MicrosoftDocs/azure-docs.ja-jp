---
title: .NET Azure Functions で依存関係の挿入を使用する
description: .NET 関数にサービスを登録して使用するために、依存関係の挿入を使用する方法について学習します
author: ggailey777
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 01/27/2021
ms.author: glenga
ms.reviewer: jehollan
ms.openlocfilehash: 66e2cd22f4bcb95be65d6d04345dcac622436a04
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98955090"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>.NET Azure Functions で依存関係の挿入を使用する

Azure Functions では、依存関係の挿入 (DI) ソフトウェア デザイン パターンがサポートされています。これは、クラスと依存関係の間で[制御の反転 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) を実現するための技術です。

- Azure Functions の依存関係挿入は、.NET Core の依存関係挿入機能を基盤としています。 [.NET Core 依存関係挿入](/aspnet/core/fundamentals/dependency-injection)について理解しておくことをお勧めします。 依存関係のオーバーライド方法と、従量課金プランで Azure Functions により構成値を読み取る方法に違いがあります。

- 依存関係の挿入のサポートは、Azure Functions 2.x から開始されます。

## <a name="prerequisites"></a>前提条件

依存関係の挿入を使用する前に、次の NuGet パッケージをインストールする必要があります。

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) パッケージ バージョン 1.0.28 以降

- [Microsoft.Extensions.DependencyInjection](https://www.nuget.org/packages/Microsoft.Extensions.DependencyInjection/) (現在のところ、バージョン 3.x 以前のみサポートされています)

## <a name="register-services"></a>サービスを登録する

サービスを登録するには、構成用のメソッドを作成し、`IFunctionsHostBuilder` インスタンスにコンポーネントを追加します。  Azure Functions ホストにより、`IFunctionsHostBuilder` のインスタンスが作成され、メソッドに直接渡されます。

このメソッドを登録するには、起動時に使用される型名を指定する `FunctionsStartup` アセンブリ属性を追加します。

```csharp
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();

            builder.Services.AddSingleton<IMyService>((s) => {
                return new MyService();
            });

            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

この例では、スタートアップ時に `HttpClient` を登録するために必要な [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) パッケージが使用されています。

### <a name="caveats"></a>注意事項

ランタイム前後の一連の登録手順実行によりスタートアップ クラスが処理されます。 そのため、以下の点に注意してください。

- *スタートアップ クラスは設定と登録だけを目的とします。* スタートアップ プロセス中、スタートアップ時に登録されるサービスを使用しないでください。 たとえば、スタートアップ中に登録されているロガーにメッセージを記録しないでください。 登録プロセスのこの時点は、サービスを利用するには早すぎます。 `Configure` メソッドが実行されると、Functions ランタイムは追加の依存関係を引き続き登録します。これがサービスの動作に影響を与える可能性があります。

- *依存関係挿入コンテナーでは、明示的に登録された型のみが保持されます*。 挿入可能な型として利用できる唯一のサービスは `Configure` メソッド内で設定されるサービスです。 結果的に、`BindingContext` や `ExecutionContext` のような Functions 固有の型は設定中に利用できず、また、挿入可能な型として利用できません。

## <a name="use-injected-dependencies"></a>挿入された依存関係を使用する

コンストラクターの挿入は、依存関係を関数で利用できるようにする目的で使用されます。 コンストラクターの挿入を使用するには、挿入されたサービスまたは関数クラスに対して静的クラスを使用しないようにする必要があります。

次のサンプルでは、`IMyService` と `HttpClient` の依存関係が、HTTP によってトリガーされる関数にどのように挿入されるかを示します。

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Net.Http;
using System.Threading.Tasks;

namespace MyNamespace
{
    public class MyHttpTrigger
    {
        private readonly HttpClient _client;
        private readonly IMyService _service;

        public MyHttpTrigger(HttpClient httpClient, IMyService service)
        {
            this._client = httpClient;
            this._service = service;
        }

        [FunctionName("MyHttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            var response = await _client.GetAsync("https://microsoft.com");
            var message = _service.GetMessage();

            return new OkObjectResult("Response from function with injected dependencies.");
        }
    }
}
```

この例では、スタートアップ時に `HttpClient` を登録するために必要な [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) パッケージが使用されています。

## <a name="service-lifetimes"></a>サービスの有効期間

Azure Functions アプリのサービス有効期間は [ASP.NET 依存関係挿入](/aspnet/core/fundamentals/dependency-injection#service-lifetimes)と同じになります。 Functions アプリの場合、各種サービス有効期間が次のように動作します。

- **一時的**:一時的なサービスは、サービスが解決されるたびに作成されます。
- **スコープ付き**:スコープ付きサービスの有効期間は、関数実行の有効期間に一致します。 スコープ付きサービスは、関数の実行ごとに 1 回作成されます。 実行時のそのサービスに対する後続の要求では、既存のサービス インスタンスが再利用されます。
- **シングルトン**:シングルトン サービスの有効期間はホストの有効期間に一致し、そのインスタンスでの関数実行間で再利用されます。 シングルトン サービスの有効期間は、`DocumentClient` インスタンスや `HttpClient` インスタンスなど、接続やクライアントに推奨されます。

GitHub の[さまざまなサービスの有効期間のサンプル](https://github.com/Azure/azure-functions-dotnet-extensions/tree/main/src/samples/DependencyInjection/Scopes)を表示するか、ダウンロードします。

## <a name="logging-services"></a>ログ記録サービス

独自のログ記録プロバイダーが必要な場合は、カスタム型を [`ILoggerProvider`](/dotnet/api/microsoft.extensions.logging.iloggerfactory) のインスタンスとして登録します。これは、[Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) NuGet パッケージを通じて使用できます。

Azure Functions によって Application Insights が自動的に追加されます。

> [!WARNING]
> - 環境によって提供されるサービスと競合するサービスが登録されるため、サービス コレクションに `AddApplicationInsightsTelemetry()` を追加しないでください。
> - 組み込みの Application Insights 機能を使用している場合、独自の `TelemetryConfiguration` または `TelemetryClient` を登録しないでください。 独自の `TelemetryClient` インスタンスを構成する必要がある場合は、[C# 関数でカスタム テレメトリをログに記録する](functions-dotnet-class-library.md?tabs=v2%2Ccmd#log-custom-telemetry-in-c-functions)方法に関するセクションに示されているように、挿入された `TelemetryConfiguration` を使用して作成します。

### <a name="iloggert-and-iloggerfactory"></a>ILogger<T> および ILoggerFactory

ホストでは、`ILogger<T>` サービスと `ILoggerFactory` サービスがコンストラクターに挿入されます。  ただし、既定では、これらの新しいログ フィルターは関数のログから除外されます。  追加のフィルターおよびカテゴリを選択するには、`host.json` ファイルを変更する必要があります。

次の例では、ホストに公開されるログを含む `ILogger<HttpTrigger>` を追加する方法を示します。

```csharp
namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly ILogger<HttpTrigger> _log;

        public HttpTrigger(ILogger<HttpTrigger> log)
        {
            _log = log;
        }

        [FunctionName("HttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req)
        {
            _log.LogInformation("C# HTTP trigger function processed a request.");

            // ...
    }
}
```

次の例の `host.json` ファイルでは、ログ フィルターが追加されます。

```json
{
    "version": "2.0",
    "logging": {
        "applicationInsights": {
            "samplingSettings": {
                "isEnabled": true,
                "excludedTypes": "Request"
            }
        },
        "logLevel": {
            "MyNamespace.HttpTrigger": "Information"
        }
    }
}
```

ログ レベルの詳細については、「[ログ レベルを構成する](configure-monitoring.md#configure-log-levels)」を参照してください。

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

`IConfiguration` インスタンスからカスタムの型に値を抽出できます。 アプリ設定値をカスタムの型にコピーすると、その値が挿入可能になり、サービスのテストが簡単になります。 構成インスタンスに読み込まれる設定は、単純なキーと値のペアである必要があります。

次のクラスについて考えてください。名前がアプリ設定と一致するプロパティが含まれています。

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

また、次のようにカスタム設定を構成する可能性のある `local.settings.json` ファイルもあります。
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

`Startup.Configure` メソッド内から次のコードを使用し、`IConfiguration` インスタンスの値をカスタムの型に抽出することができます。

```csharp
builder.Services.AddOptions<MyOptions>()
    .Configure<IConfiguration>((settings, configuration) =>
    {
        configuration.GetSection("MyOptions").Bind(settings);
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
        _settings = options.Value;
    }
}
```

オプションの使用に関する詳細については、「[ASP.NET Core のオプション パターン](/aspnet/core/fundamentals/configuration/options)」を参照してください。

## <a name="using-aspnet-core-user-secrets"></a>ASP.NET Core ユーザー シークレットの使用

ローカルで開発する場合、ASP.NET Core にはプロジェクトのルートの外部にシークレット情報を格納できる[シークレット マネージャー ツール](/aspnet/core/security/app-secrets#secret-manager)が用意されています。 これにより、シークレットが誤ってソース管理にコミットされる可能性が低くなります。 ASP.NET Core シークレット マネージャーによって作成されたシークレットは、Azure Functions Core Tools (バージョン 3.0.3233 以降) によって自動的に読み取られます。

ユーザー シークレットを使用するように .NET Azure Functions プロジェクトを構成するには、プロジェクトのルートで次のコマンドを実行します。

```bash
dotnet user-secrets init
```

次に、`dotnet user-secrets set` コマンドを使用して、シークレットを作成または更新します。

```bash
dotnet user-secrets set MySecret "my secret value"
```

関数アプリ コードでユーザー シークレット値にアクセスするには、`IConfiguration` または `IOptions` を使用します。

## <a name="customizing-configuration-sources"></a>構成ソースのカスタマイズ

> [!NOTE]
> 構成ソースのカスタマイズは、Azure Functions ホスト バージョン 2.0.14192.0 および 3.0.14191.0 以降で使用できます。

追加の構成ソースを指定するには、関数アプリの `StartUp` クラスの `ConfigureAppConfiguration` メソッドをオーバーライドします。

次のサンプルでは、基本とオプションの環境固有のアプリ設定ファイルから、構成値を追加しています。

```csharp
using System.IO;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
        {
            FunctionsHostBuilderContext context = builder.GetContext();

            builder.ConfigurationBuilder
                .AddJsonFile(Path.Combine(context.ApplicationRootPath, "appsettings.json"), optional: true, reloadOnChange: false)
                .AddJsonFile(Path.Combine(context.ApplicationRootPath, $"appsettings.{context.EnvironmentName}.json"), optional: true, reloadOnChange: false)
                .AddEnvironmentVariables();
        }
    }
}
```

`IFunctionsConfigurationBuilder` の `ConfigurationBuilder` プロパティに構成プロバイダーを追加します。 構成プロバイダーの使用の詳細については、「[ASP.NET Core での構成](/aspnet/core/fundamentals/configuration/#configuration-providers)」を参照してください。

`FunctionsHostBuilderContext` は `IFunctionsConfigurationBuilder.GetContext()` から取得されます。 このコンテキストを使用して現在の環境名を取得し、関数アプリ フォルダー内の構成ファイルの場所を解決します。

既定では、*appsettings.json* などの構成ファイルは、関数アプリの出力フォルダーに自動的にコピーされません。 ファイルがコピーされるようにするため、次のサンプルと一致するように *.csproj* ファイルを更新します。

```xml
<None Update="appsettings.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>      
</None>
<None Update="appsettings.Development.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    <CopyToPublishDirectory>Never</CopyToPublishDirectory>
</None>
```

> [!IMPORTANT]
> 従量課金プランまたは Premium プランで実行されている関数アプリの場合、トリガーで使用される構成値を変更すると、スケーリング エラーが発生する可能性があります。 `FunctionsStartup` クラスによってこれらのプロパティに変更が加えられると、関数アプリのスタートアップ エラーが発生します。

## <a name="next-steps"></a>次のステップ

詳細については、次のリソースを参照してください。

- [関数アプリを監視する方法](functions-monitoring.md)
- [関数のベスト プラクティス](functions-best-practices.md)
