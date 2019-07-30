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
ms.date: 05/28/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: 1dbbb6e7bd88e08520225515c422529dc260e1b2
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377376"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>.NET Azure Functions で依存関係の挿入を使用する

Azure Functions では、依存関係の挿入 (DI) ソフトウェア デザイン パターンがサポートされています。これは、クラスと依存関係の間で[制御の反転 (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) を実現するための技術です。

Azure Functions は、ASP.NET Core の依存関係の挿入機能の上にビルドされます。 Azure Functions アプリで DI 機能を使用する前に、[ASP.NET Core の依存関係の挿入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)のサービス、有効期間、デザイン パターンを把握しておくことをお勧めします。

依存関係の挿入のサポートは、Azure Functions 2.x から開始されます。

## <a name="prerequisites"></a>前提条件

依存関係の挿入を使用する前に、次の NuGet パッケージをインストールする必要があります。

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Microsoft.NET.Sdk.Functions パッケージ](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) バージョン 1.0.28 以降

- 省略可能:[Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) - スタートアップ時に HttpClient を登録する場合にのみ必要

## <a name="register-services"></a>サービスを登録する

サービスを登録するには、構成用のメソッドを作成して、`IFunctionsHostBuilder` インスタンスにコンポーネントを追加することができます。  Azure Functions ホストにより、`IFunctionsHostBuilder` のインスタンスが作成され、メソッドに直接渡されます。

このメソッドを登録するには、起動時に使用される型名を指定する `FunctionsStartup` アセンブリ属性を追加します。 またコードでは、Nuget の [Microsoft.Azure.Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) のプレリリースが参照されます。

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Cosmos;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
            builder.Services.AddSingleton((s) => {
                return new CosmosClient(Environment.GetEnvironmentVariable("COSMOSDB_CONNECTIONSTRING"));
            });
            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

## <a name="use-injected-dependencies"></a>挿入された依存関係を使用する

ASP.NET Core では、コンストラクターの挿入を使用して、依存関係を関数で使用できるようにします。 次のサンプルでは、`IMyService` と `HttpClient` の依存関係が、HTTP によってトリガーされる関数にどのように挿入されるかを示します。

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

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

コンストラクターの挿入の使用は、依存関係の注入を利用する場合には静的関数を使用すべきでないことを意味します。

## <a name="service-lifetimes"></a>サービスの有効期間

Azure Function アプリでは、[ASP.NET の依存関係の挿入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes)と同じサービスの有効期間 (一時、スコープ付き、シングルトン) が提供されます。

関数アプリでは、スコープ付きのサービスの有効期間は、関数の実行の有効期間に一致します。 スコープ付きサービスは毎回作成されます。 実行時のそのサービスに対する後続の要求では、既存のサービス インスタンスが再利用されます。 シングルトン サービスの有効期間はホストの有効期間に一致し、そのインスタンスでの関数の実行全体で再利用されます。

接続とクライアント (たとえば、`SqlConnection`、`CloudBlobClient`、または `HttpClient` のインスタンス) には、シングルトンの有効期間のサービスをお勧めします。

GitHub の[さまざまなサービスの有効期間のサンプル](https://aka.ms/functions/di-sample)を表示するか、ダウンロードします。

## <a name="logging-services"></a>ログ記録サービス

独自のログ記録プロバイダーが必要な場合は、`ILoggerProvider` インスタンスを登録する方法をお勧めします。 Azure Functions によって Application Insights が自動的に追加されます。

> [!WARNING]
> サービス コレクションに `AddApplicationInsightsTelemetry()` を追加しないでください。環境によって提供されるサービスと競合するサービスが登録されます。

## <a name="function-app-provided-services"></a>関数アプリで提供されるサービス

関数ホストは、多くのサービスを登録します。 次のサービスは、お使いのアプリケーションに依存関係として取り込んでも安全です。

|サービスの種類|有効期間|説明|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|シングルトン|実行時の構成|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|シングルトン|ホスト インスタンスの ID を提供する責任がある|

依存関係を取り込みたい他のサービスがある場合は、[問題を作成し、GitHub でそれらを提案してください](https://github.com/azure/azure-functions-host)。

### <a name="overriding-host-services"></a>ホスト サービスのオーバーライド

ホストによって提供されるサービスのオーバーライドは、現在サポートされていません。  オーバーライドしたいサービスがある場合は、[問題を作成し、GitHub でそれらを提案してください](https://github.com/azure/azure-functions-host)。

## <a name="next-steps"></a>次の手順

詳細については、次のリソースを参照してください。

- [関数アプリを監視する方法](functions-monitoring.md)
- [関数のベスト プラクティス](functions-best-practices.md)
