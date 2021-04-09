---
title: WebJobs SDK の使用方法
description: WebJobs SDK 用のコードを書く方法を学びます。 Azure サービスやサード パーティのデータにアクセスするイベント ドリブンのバックグラウンド処理のジョブを作成します。
author: ggailey777
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 063924dccb7d7b95b962b24ecc1af1870a855194
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102617133"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>イベント ドリブンのバックグラウンド処理に Azure WebJobs SDK を使用する方法

この記事は、Azure WebJobs SDK を使用する方法のガイダンスを提供します。 WebJobs をすぐに使い始めたい場合は、「[イベント ドリブンのバックグラウンド処理で Azure WebJobs SDK の使用を開始する](webjobs-sdk-get-started.md)」をご覧ください。 

## <a name="webjobs-sdk-versions"></a>WebJobs SDK のバージョン

WebJobs SDK のバージョン 3.*x* とバージョン 2.*x* には、重要な違いがあります。

* バージョン 3.*x* では、.NET Core のサポートが追加されています。
* バージョン 3.*x* では、WebJobs SDK で必要となるストレージ バインディング拡張機能を明示的にインストールする必要があります。 バージョン 2.*x* では、ストレージのバインドは SDK に含まれていました。
* .NET Core (3.*x*) プロジェクト用の Visual Studio のツールは、.NET Framework (2.*x*) プロジェクト用のツールと異なります。 詳しくは、「[Visual Studio を使用して Web ジョブを開発してデプロイする - Azure App Service](webjobs-dotnet-deploy-vs.md)」をご覧ください。

可能な場合は、バージョン 3.*x* とバージョン 2.*x* 両方の例が提供されています。

> [!NOTE]
> [Azure Functions](../azure-functions/functions-overview.md) は WebJobs SDK でビルドされており、この記事では Azure Functions ドキュメントの一部のトピックへのリンクが提供されています。 Functions と WebJobs SDK の以下の違いに注意してください。
> * Azure Functions バージョン 2.*x* は WebJobs SDK バージョン 3.*x* に対応しており、Azure Functions 1.*x* は WebJobs SDK 2.*x* に対応しています。 ソース コード リポジトリでは、WebJobs SDK の番号付けが使用されています。
> * Azure Functions C# クラス ライブラリのサンプル コードは、WebJobs SDK プロジェクトに `FunctionName` 属性が必要ないということ以外は、WebJobs SDK コードに類似しています。
> * バインドの種類の一部は、HTTP (Webhook) や (HTTP に基づく) Event Grid などの Functions でのみサポートされます。
>
> 詳しくは、[WebJobs SDK と Azure Functions の比較](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs)に関するページをご覧ください。

## <a name="webjobs-host"></a>WebJobs ホスト

このホストは、関数のランタイム コンテナーです。  トリガーをリッスンして関数を呼び出します。 バージョン 3.*x* では、ホストは `IHost` の実装です。 バージョン 2.*x* では、`JobHost` オブジェクトを使用します。 コードでホスト インスタンスを作成し、その動作をカスタマイズするコードを書きます。

これが、直接 WebJobs SDK を使用することと、Azure Functions によって間接的に使用することの主な違いです。 Azure Functions では、サービスがホストを制御するので、コードを書いてホストをカスタマイズすることはできません。 Azure Functions では、host.json ファイルの設定を使用してホスト動作をカスタマイズできます。 これらの設定はコードではなく文字列なので、行うことができるカスタマイズの種類が制限されます。

### <a name="host-connection-strings"></a>ホスト接続文字列

WebJobs SDK では、Azure Storage と Azure Service Bus の接続文字列は、ローカルで実行するときは local.settings.json ファイルで検索され、Azure で実行するときは WebJob の環境で検索されます。 既定で、`AzureWebJobsStorage` という名前のストレージ接続文字列設定が必要です。  

SDK のバージョン 2.*x* を使用すると、これらの接続文字列に独自の名前を使用したり、それらを他の場所に格納したりできます。 次に示すように、[`JobHostConfiguration`] を使用してコードで名前を設定できます。

```cs
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    //// Dashboard logging is deprecated; use Application Insights.
    //var _dashboardConn = ConfigurationManager
    //    .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    //config.DashboardConnectionString = _dashboardConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

> [!NOTE]
> バージョン 3.*x* では、既定の .NET Core 構成 API が使用されるため、接続文字列の名前を変更する API はありません。 [Visual Studio を使用した Web ジョブの開発とデプロイ](webjobs-dotnet-deploy-vs.md)に関する記事を参照してください

### <a name="host-development-settings"></a>ホスト開発設定

ローカル開発をより効率的にするために、開発モードでホストを実行できます。 開発モードで実行しているときに変更されるいくつかの設定を以下に示します。

| プロパティ | 開発設定 |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` でログ出力を最大化します。 |
| `Queues.MaxPollingInterval`  | キューのメソッドがすぐにトリガーされるように、値は低くしてあります。  |
| `Singleton.ListenerLockPeriod` | 素早い反復開発の支援は 15 秒です。 |

開発モードを有効にするプロセスは、SDK のバージョンによって異なります。 

#### <a name="version-3x"></a>バージョン 3.*x*

バージョン 3.*x* では、標準の ASP.NET Core API が使用されます。 [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) インスタンスで [`UseEnvironment`](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) メソッドを呼び出します。 次の例のように、`development` という名前の文字列を渡します。

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.UseEnvironment("development");
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>バージョン 2.*x*

`JobHostConfiguration` クラスには、開発モードを有効にする `UseDevelopmentSettings` メソッドがあります。  次の例では、開発設定を使用する方法を示します。 ローカル環境で実行したときは `config.IsDevelopment` が `true` を返すようにするには、`AzureWebJobsEnv` という名前で値が `Development` のローカル環境変数を設定します。

```cs
static void Main()
{
    config = new JobHostConfiguration();

    if (config.IsDevelopment)
    {
        config.UseDevelopmentSettings();
    }

    var host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="managing-concurrent-connections-version-2x"></a><a name="jobhost-servicepointmanager-settings"></a>コンカレント接続の管理 (バージョン 2.*x*)

バージョン 3.*x* では、接続制限は既定で無限接続に設定されます。 何らかの理由でこの制限を変更する必要がある場合は、[`WinHttpHandler`](/dotnet/api/system.net.http.winhttphandler) クラス の [`MaxConnectionsPerServer`](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) プロパティを使用できます。

バージョン 2.*x* では、[ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) API を使用して、ホストへのコンカレント接続の数を制御します。 バージョン 2.*x* では、WebJobs ホストを開始する前に、この値を既定値の 2 から増加してください。

`HttpClient` を使用することで関数から送信するすべての HTTP 要求は、`ServicePointManager` を通過します。 `DefaultConnectionLimit` で設定されている値に達した後は、`ServicePointManager` では送信する前に要求をキューに格納するようになります。 たとえば、`DefaultConnectionLimit` が 2 に設定されていて、コードが 1,000 個の HTTP 要求を行ったとします。 最初、OS への送信が許可されるのは 2 個の要求のみです。 その他の 998 個は、余裕ができるまでキューされたままになります。 つまり、要求を行ったように見えても、OS によって宛先サーバーに要求が送信されなかったため、`HttpClient` がタイムアウトする可能性があります。 従って、ローカルの `HttpClient` は、要求を完了するのに 10 秒間かかっているのに、サービスはどの要求も 200 ミリ秒で返しているというような、あまり意味をなさないように見える動作が目撃されることがあります。 

ASP.NET アプリケーションの既定値は `Int32.MaxValue` であり、これは Basic またはそれ以降の App Service プランで実行されている WebJobs で適切に動作します。 WebJobs は通常、Always On の設定を必要としており、これは Basic またはそれ以降の App Service プランでのみサポートされています。

Web ジョブが Free または Shared App Service プランで実行されている場合、アプリケーションは、現在 [300 の接続制限](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits)を持つ App Service サンドボックスにより制限されています。 `ServicePointManager` の設定がバインドなしの接続制限の場合、サンドボックスの接続がしきい値に達し、サイトがシャット ダウンする可能性が高くなります。 その場合は、`DefaultConnectionLimit`の設定を 50 または 100 のようにより低いものにすることで、これを防ぎつつ十分なスループットを可能にします。

あらゆる HTTP 要求が行われる前に、この設定を構成する必要があります。 このため、WebJobs ホストでは設定を自動的に調整しないでください。 ホストが開始する前に HTTP 要求が発生する可能性があり、予期しない動作を招くことがあります。 最善の方法は、次に示すように、`JobHost` を初期化する前に `Main` メソッドですぐに値を設定することです。

```csharp
static void Main(string[] args)
{
    // Set this immediately so that it's used by all requests.
    ServicePointManager.DefaultConnectionLimit = Int32.MaxValue;

    var host = new JobHost();
    host.RunAndBlock();
}
```

## <a name="triggers"></a>トリガー

関数は、パブリック メソッドである必要があり、1 つのトリガー属性または [`NoAutomaticTrigger`](#manual-triggers) 属性を必要とします。

### <a name="automatic-triggers"></a>自動トリガー

自動トリガーは、イベントに応答して関数を呼び出します。 Azure Queue Storage に追加されるメッセージによってトリガーされる次の例のような関数について考えます。 それは、Azure Blob Storage から BLOB を読み取ることで応答します。

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

`QueueTrigger` 属性は、キュー メッセージが `myqueue-items` キューに出現するたびに関数を呼び出すようランタイムに通知します。 `Blob` 属性は、キュー メッセージを使用して *sample-workitems* コンテナー内の BLOB を読み取るようランタイムに通知します。 `samples-workitems` コンテナー内の BLOB 項目の名前は、キュー トリガーからバインド式 (`{queueTrigger}`) として直接取得されます。

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

### <a name="manual-triggers"></a>手動トリガー

関数を手動でトリガーするには、次に示すように、`NoAutomaticTrigger` 属性を使用します。

```cs
[NoAutomaticTrigger]
public static void CreateQueueMessage(
ILogger logger,
string value,
[Queue("outputqueue")] out string message)
{
    message = value;
    logger.LogInformation("Creating queue message: ", message);
}
```

関数を手動でトリガーするプロセスは、SDK のバージョンによって異なります。

#### <a name="version-3x"></a>バージョン 3.*x*

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage();
    });
    var host = builder.Build();
    using (host)
    {
        var jobHost = host.Services.GetService(typeof(IJobHost)) as JobHost;
        var inputs = new Dictionary<string, object>
        {
            { "value", "Hello world!" }
        };

        await host.StartAsync();
        await jobHost.CallAsync("CreateQueueMessage", inputs);
        await host.StopAsync();
    }
}
```

#### <a name="version-2x"></a>バージョン 2.*x*

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>入出力バインド

入力バインドは、Azure やサード パーティ サービスからのデータをコードに使用できるようにする宣言方法を提供します。 出力バインドは、データを更新する方法を提供します。 [WebJobs SDK の使用開始](webjobs-sdk-get-started.md)に関するページでは、それぞれの例が示されています。

出力バインドに対してメソッドの戻り値を使用するには、属性をメソッドの戻り値に適用します。 「[Azure 関数の戻り値の使用](../azure-functions/functions-bindings-return-value.md)」の例をご覧ください。

## <a name="binding-types"></a>バインドの種類

バインドの種類をインストールして管理するプロセスは、バージョン 3.*x* またはバージョン 2.*x* のどちらの SDK を使用しているかによって異なります。 特定のバインドの種類用にインストールするパッケージは、Azure Functions のそのバインドの種類の[参照資料](#binding-reference-information)の「パッケージ」セクションで見つけることができます。 例外は (ローカル ファイル システム用の) Files トリガーとバインドで、これは Azure Functions ではサポートされていません。

#### <a name="version-3x"></a>バージョン 3.*x*

バージョン 3.*x`Microsoft.Azure.WebJobs.Extensions.Storage` では、ストレージのバインドは*  パッケージに含まれています。 次に示すように、`ConfigureWebJobs` メソッドで `AddAzureStorage` 拡張メソッドを呼び出します。

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddAzureStorage();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

他のトリガーやバインドの種類を使用するには、それらを含む NuGet パッケージをインストールして、拡張で実装されている `Add<binding>` 拡張メソッドを呼び出します。 たとえば、Azure Cosmos DB バインドを使用する場合は、次のように、`Microsoft.Azure.WebJobs.Extensions.CosmosDB` をインストールして `AddCosmosDB` を呼び出します。

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddCosmosDB();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

コア サービスの一部である Timer トリガーまたは Files バインドを使用するには、それぞれ `AddTimers` 拡張メソッドまたは `AddFiles` 拡張メソッドを呼び出します。

#### <a name="version-2x"></a>バージョン 2.*x*

次のトリガーおよびバインドの種類は、`Microsoft.Azure.WebJobs` パッケージのバージョン 2.*x* に含まれています。

* BLOB ストレージ
* ストレージ
* テーブル ストレージ

その他の種類のトリガーやバインドを使用するには、それらを含む NuGet パッケージをインストールして、`JobHostConfiguration` オブジェクトの `Use<binding>` メソッドを呼び出します。 たとえば、Timer トリガーを使用する場合は、次のように、`Microsoft.Azure.WebJobs.Extensions` をインストールして、`Main` メソッドで `UseTimers` を呼び出します。

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

Files バインドを使用するには、`Microsoft.Azure.WebJobs.Extensions` をインストールして `UseFiles` を呼び出します。

### <a name="executioncontext"></a>ExecutionContext

WebJobs を使用すると、[`ExecutionContext`] にバインドできます。 このバインディングを使用すると、関数シグネチャのパラメーターとして [`ExecutionContext`] にアクセスできます。 たとえば、次のコードはコンテキスト オブジェクトを使用して呼び出し ID にアクセスします。その呼び出し ID を使用すると、特定の関数呼び出しによって生成されたすべてのログを関連付けることができます。  

```cs
public class Functions
{
    public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        ExecutionContext executionContext,
        ILogger logger)
    {
        logger.LogInformation($"{message}\n{executionContext.InvocationId}");
    }
}
```

[`ExecutionContext`] にバインドするプロセスは、SDK のバージョンによって異なります。

#### <a name="version-3x"></a>バージョン 3.*x*

次に示すように、`ConfigureWebJobs` メソッドで `AddExecutionContextBinding` 拡張メソッドを呼び出します。

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddExecutionContextBinding();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>バージョン 2.*x*

先述した `Microsoft.Azure.WebJobs.Extensions` パッケージは、`UseCore` メソッドを呼び出すことによって登録できる特別なバインドの種類も提供しています。 このバインドを使用すると、関数シグネチャで [`ExecutionContext`] パラメーターを定義でき、それは次のように有効化されます。

```cs
class Program
{
    static void Main()
    {
        config = new JobHostConfiguration();
        config.UseCore();
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="binding-configuration"></a>バインド構成

一部のトリガーとバインドの動作を構成することができます。 それらを構成するプロセスは、SDK のバージョンによって異なります。

* **バージョン 3.*x*:** `ConfigureWebJobs` で `Add<Binding>` メソッドを 呼び出すときに、構成を設定します。
* **バージョン 2.*x*:** `JobHost` に渡す構成オブジェクトでプロパティを設定することにより、構成を設定します。

これらのバインド固有の設定は、Azure Functions の [host.json プロジェクト ファイル](../azure-functions/functions-host-json.md)での設定と同等です。

次のバインドを構成できます。

* [Azure CosmosDB トリガー](#azure-cosmosdb-trigger-configuration-version-3x)
* [Event Hubs トリガー](#event-hubs-trigger-configuration-version-3x)
* [Queue ストレージ トリガー](#queue-storage-trigger-configuration)
* [SendGrid バインド](#sendgrid-binding-configuration-version-3x)
* [Service Bus トリガー](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Azure CosmosDB トリガーの構成 (バージョン 3.*x*)

次の例では、Azure Cosmos DB トリガーを構成する方法を示します。

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddCosmosDB(a =>
        {
            a.ConnectionMode = ConnectionMode.Gateway;
            a.Protocol = Protocol.Https;
            a.LeaseOptions.LeasePrefix = "prefix1";

        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

詳しくは、[Azure CosmosDB のバインド](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings)に関する記事をご覧ください。

### <a name="event-hubs-trigger-configuration-version-3x"></a>Event Hubs トリガーの構成 (バージョン 3.*x*)

次の例では、Event Hubs トリガーを構成する方法を示します。

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddEventHubs(a =>
        {
            a.BatchCheckpointFrequency = 5;
            a.EventProcessorOptions.MaxBatchSize = 256;
            a.EventProcessorOptions.PrefetchCount = 512;
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

詳しくは、[Event Hubs のバインド](../azure-functions/functions-bindings-event-hubs.md#host-json)に関する記事をご覧ください。

### <a name="queue-storage-trigger-configuration"></a>Queue Storage トリガーの構成

以下の例では、Queue Storage トリガーを構成する方法を示します。

#### <a name="version-3x"></a>バージョン 3.*x*

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage(a => {
            a.BatchSize = 8;
            a.NewBatchThreshold = 4;
            a.MaxDequeueCount = 4;
            a.MaxPollingInterval = TimeSpan.FromSeconds(15);
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

詳しくは、[Queue Storage のバインド](../azure-functions/functions-bindings-storage-queue-trigger.md#hostjson-properties)に関する記事をご覧ください。

#### <a name="version-2x"></a>バージョン 2.*x*

```cs
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.NewBatchThreshold = 4;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

詳しくは、[host.json v1.x のリファレンス](../azure-functions/functions-host-json-v1.md#queues)をご覧ください。

### <a name="sendgrid-binding-configuration-version-3x"></a>SendGrid バインドの構成 (バージョン 3.*x*)

次の例では、SendGrid 出力バインドを構成する方法を示します。

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddSendGrid(a =>
        {
            a.FromAddress.Email = "samples@functions.com";
            a.FromAddress.Name = "Azure Functions";
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

詳しくは、[SendGrid のバインド](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings)に関する記事をご覧ください。

### <a name="service-bus-trigger-configuration-version-3x"></a>Service Bus トリガーの構成 (バージョン 3.*x*)

次の例では、Service Bus  トリガーを構成する方法を示します。

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddServiceBus(sbOptions =>
        {
            sbOptions.MessageHandlerOptions.AutoComplete = true;
            sbOptions.MessageHandlerOptions.MaxConcurrentCalls = 16;
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

詳しくは、[Service Bus のバインド](../azure-functions/functions-bindings-service-bus-output.md#hostjson-settings)に関する記事をご覧ください。

### <a name="configuration-for-other-bindings"></a>その他のバインドの構成

一部のトリガーとバインドの種類では、独自のカスタム構成の種類が定義されています。 たとえば、次の例に示すように、File トリガーを使用して、監視するルート パスを指定することができます。

#### <a name="version-3x"></a>バージョン 3.*x*

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddFiles(a => a.RootPath = @"c:\data\import");
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>バージョン 2.*x*

```cs
static void Main()
{
    config = new JobHostConfiguration();
    var filesConfig = new FilesConfiguration
    {
        RootPath = @"c:\data\import"
    };
    config.UseFiles(filesConfig);
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

## <a name="binding-expressions"></a>バインド式

属性コンス トラクターのパラメーターでは、さまざまなソースからの値に解決する式を使用することができます。 たとえば、次のコードで `BlobTrigger` 属性のパスは、`filename` という名前の式を作成します。 これが出力バインドに使用されると、`filename` はトリガーする BLOB の名前に解決します。

```cs
public static void CreateThumbnail(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger logger)
{
    logger.Info($"Blob trigger processing: {filename}");
    // ...
}
```

バインド式の詳細については、Azure Functions ドキュメント内の[バインド式とパターン](../azure-functions/functions-bindings-expressions-patterns.md)を参照してください。

### <a name="custom-binding-expressions"></a>カスタム バインド式

キュー名、BLOB 名、コンテナー、テーブル名をハードコーディングではなく、コードに指定する場合もあります。 たとえば、構成ファイルや環境変数で `QueueTrigger` 属性のキュー名を指定するとします。

これは、`NameResolver` オブジェクトを `JobHostConfiguration` オブジェクトに渡すことでできます。 プレースホルダーをトリガーやバインド属性コンストラクターのパラメーターに含めると、これらのプレースホルダーの代わりに使用する実際の値が `NameResolver` コードにより提供されます。 次に示すように、プレースホルダーはパーセント (%) 記号で囲んで示します。

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

このコードを使用すると、`logqueuetest` という名前のキューをテスト環境で使用でき、`logqueueprod` という名前のキューを実稼働環境で使用できます。 キュー名をハードコードする代わりに、`appSettings` コレクションのエントリの名前を指定します。

カスタムの名前を提供しない場合、既定の `NameResolver` が使用されます。 既定値は、アプリの設定や環境変数から値を取得します。

次のように、`NameResolver` クラスでは `appSettings` からキューの名前が取得されます。

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

#### <a name="version-3x"></a>バージョン 3.*x*

リゾルバーは、依存関係インジェクションを使用して構成します。 これらのサンプルには、次の `using` ステートメントが必要です。

```cs
using Microsoft.Extensions.DependencyInjection;
```

次の例のように、[`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) で [`ConfigureServices`] 拡張メソッドを呼び出すことによって、リゾルバーを追加します。

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    var resolver = new CustomNameResolver();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureServices(s => s.AddSingleton<INameResolver>(resolver));
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>バージョン 2.*x*

次に示すように、`NameResolver` クラスを `JobHost` オブジェクトに渡します。

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Azure Functions では、次の例に示すように、アプリの設定から値を取得するために `INameResolver` が実装されます。 WebJobs SDK を直接使用する場合は、お好みのソースからプレースホルダーの置換値を取得するカスタム実装を記述できます。

## <a name="binding-at-runtime"></a>実行時のバインド

`Queue`、`Blob`、`Table` などのバインド属性を使用する前に関数で何らかの処理を行う必要がある場合は、`IBinder` インターフェイスを使用できます。

次の例では、入力キュー メッセージを取得して同じ内容の新しい出力キュー メッセージを作成します。 出力キュー名は、関数本体のコードによって設定されます。

```cs
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessageAsync(new CloudQueueMessage(queueMessage));
}
```

詳細については、Azure Functions ドキュメント内の[実行時のバインド](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime)を参照してください。

## <a name="binding-reference-information"></a>バインド参照情報

Azure Functions のドキュメントでは、各バインドの種類に関する参照情報が提供されています。 各バインド参照記事には、以下の情報が記載されています。 (この例は、Storage キューに基づいています。)

* [パッケージ](../azure-functions/functions-bindings-storage-queue.md)。 WebJobs SDK プロジェクトにバインドのサポートを含めるためにインストールする必要のあるパッケージです。
* [例](../azure-functions/functions-bindings-storage-queue-trigger.md)。 コード サンプルです。 C# クラス ライブラリの例は、WebJobs SDK に適用されます。 `FunctionName` 属性は単に省略します。
* [属性](../azure-functions/functions-bindings-storage-queue-trigger.md#attributes-and-annotations)。 バインドの種類に使用する属性です。
* [構成](../azure-functions/functions-bindings-storage-queue-trigger.md#configuration)。 属性のプロパティとコンストラクターのパラメーターの説明です。
* [使用方法](../azure-functions/functions-bindings-storage-queue-trigger.md#usage)。 どの種類にバインドできるかとバインドの動作方法に関する情報です。 例: ポーリング アルゴリズム、有害キュー処理。
  
バインドの参照記事の一覧については、Azure Functions の[トリガーとバインド](../azure-functions/functions-triggers-bindings.md#supported-bindings)に関する記事の「サポートされるバインディング」をご覧ください。 その一覧では、HTTP、Webhook、Event Grid のバインドは、Azure Functions でのみサポートされており、WebJobs SDK ではサポートされていません。

## <a name="disable-attribute"></a>Disable 属性 

[`Disable`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) 属性は、関数がトリガーされるかどうかを制御します。 

次の例では、アプリ設定 `Disable_TestJob` の値が `1` または `True` (大文字小文字の区別なし) の場合、関数は実行されません。 その場合、ランタイムが *関数 'Functions.TestJob' が無効です* というログ メッセージを作成します。

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Azure portal でアプリ設定の値を変更すると、WebJobs が再起動され、新しい設定が取得されます。

属性は、パラメーター、メソッド、またはクラス レベルで宣言できます。 設定名には、バインド式を含めることもできます。

## <a name="timeout-attribute"></a>Timeout 属性

[`Timeout`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) 属性は、関数が指定した時間内に完了しない場合にキャンセルします。 次の例では、関数は Timeout 属性なしで 1 日間実行されます。 Timeout があると、関数は 15 秒後に取り消されます。

```cs
[Timeout("00:00:15")]
public static async Task TimeoutJob(
    [QueueTrigger("testqueue2")] string message,
    CancellationToken token,
    TextWriter log)
{
    await log.WriteLineAsync("Job starting");
    await Task.Delay(TimeSpan.FromDays(1), token);
    await log.WriteLineAsync("Job completed");
}
```

クラスまたはメソッド レベルで Timeout 属性を適用し、`JobHostConfiguration.FunctionTimeout` を使用してグローバル タイムアウトを指定することができます。 クラス レベルまたはメソッド レベルのタイムアウトは、グローバル タイムアウトをオーバーライドします。

## <a name="singleton-attribute"></a>Singleton 属性

[`Singleton`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) 属性を使用すると、ホスト Web アプリの複数のインスタンスがある場合でも、関数の 1 つのインスタンスのみが実行されます。 これは、[分散ロック](#viewing-lease-blobs)を使用することにより行われます。

次の例では、常に `ProcessImage` 関数の単一のインスタンスのみが実行されます。

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image.
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

一部のトリガーには、コンカレンシー管理の組み込みサポートがあります。

* **QueueTrigger**。 `JobHostConfiguration.Queues.BatchSize` を `1` に設定します。
* **ServiceBusTrigger**。 `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` を `1` に設定します。
* **FileTrigger**。 `FileProcessor.MaxDegreeOfParallelism` を `1` に設定します。

これらの設定を使用すると、単一のインスタンスで、関数がシングルトンとして実行されるようになります。 Web アプリが複数のインスタンスにスケールアウトするとき、関数の単一のインスタンスのみが実行されるようにするには、関数にリスナー レベルのシングルトン ロック (`[Singleton(Mode = SingletonMode.Listener)]`) を適用します。 リスナー ロックは、JobHost の起動時に取得されます。 3 つのスケール アウト インスタンスのすべてが同時に開始すると、1 つのインスタンスのみがロックを取得して、1 つのみがリスナーを開始します。

> [!NOTE]
> SingletonMode.Function の機能の詳細については、この [GitHub リポジトリ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonMode.cs)を参照してください。

### <a name="scope-values"></a>スコープ値

シングルトンで "*スコープ式/値*" を指定することができます。 式/値により、特定スコープでの関数のすべての実行がシリアル化されることが保証されます。 この方法で詳細なロックを実装すると、要件によって指示されたように他の呼び出しをシリアル化すると同時に、関数のある程度の並列処理が可能になります。 たとえば、次のコードでは、スコープ式は、受信メッセージの `Region` 値 にバインドしています。 キューに 3 つのメッセージが含まれ、それぞれ East、East、West リージョンである場合、East リージョンのメッセージは順次実行されますが、West リージョンのメッセージは East のメッセージと並列に実行されます。

```csharp
[Singleton("{Region}")]
public static async Task ProcessWorkItem([QueueTrigger("workitems")] WorkItem workItem)
{
     // Process the work item.
}

public class WorkItem
{
     public int ID { get; set; }
     public string Region { get; set; }
     public int Category { get; set; }
     public string Description { get; set; }
}
```

### <a name="singletonscopehost"></a>SingletonScope.Host

ロックの既定スコープは `SingletonScope.Function` です。つまり、ロック スコープ (BLOB のリース パス) は、関数の完全修飾名に関連付けられています。 複数の関数にわたってロックするには、`SingletonScope.Host` を指定して、同時に実行したくない関数すべてと同じスコープ ID 名を使用します。 次の例では、  `AddItem` または `RemoveItem`の1 つのみのインスタンス が一度に実行されます。

```csharp
[Singleton("ItemsLock", SingletonScope.Host)]
public static void AddItem([QueueTrigger("add-item")] string message)
{
     // Perform the add operation.
}

[Singleton("ItemsLock", SingletonScope.Host)]
public static void RemoveItem([QueueTrigger("remove-item")] string message)
{
     // Perform the remove operation.
}
```

### <a name="viewing-lease-blobs"></a>リース BLOB のビュー

WebJobs SDK は、バックグラウンドで [Azure BLOB リース](../storage/blobs/concurrency-manage.md#pessimistic-concurrency-for-blobs)を使用して、分散ロックを実装します。 Singleton により使用されるリース BLOB は、パス "locks" にある `AzureWebJobsStorage` ストレージ アカウント内の `azure-webjobs-host` コンテナーにあります。 たとえば、先に示した最初の `ProcessImage` の例のリース BLOB パスは、`locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage` です。 どのパスにも JobHost ID が含まれています。この場合は、061851c758f04938a4426aa9ab3869c0 になります。

## <a name="async-functions"></a>Async 関数

Async 関数のコードを書く方法については、[Azure Functions](../azure-functions/functions-dotnet-class-library.md#async) のドキュメントをご覧ください。

## <a name="cancellation-tokens"></a>キャンセル トークン

キャンセル トークンを処理する方法については、Azure Functions ドキュメントの[キャンセル トークンと正常なシャットダウン](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens)を参照してください。

## <a name="multiple-instances"></a>複数インスタンス

Web アプリが複数のインスタンス上で稼働している場合、継続的な Web ジョブは各インスタンスで実行され、トリガーをリッスンして関数の呼び出しを行います。 各種のトリガー バインドは、インスタンス間で協調して作業を効率的に共有するよう設計されているので、より多くのインスタンスにスケール アウトすると、より多くの負荷を処理することができます。

一部のトリガーで二重の処理が発生する可能性がありますが、キューと BLOB ストレージ トリガーでは、関数がキュー メッセージや BLOB を 2 回以上処理することが自動的に防止されます。 詳細については、Azure Functions ドキュメントの[同一入力のための設計](../azure-functions/functions-idempotent.md)に関する記事を参照してください。

タイマー トリガーは、指定された時刻に常に 1 つを超える関数のインスタンスが実行しないように、自動的に タイマーの1 つのインスタンスのみが実行するようにします。

ホスト Web アプリの複数のインスタンスがある場合でも、関数の 1 つのインスタンスのみを実行するには [`Singleton`](#singleton-attribute) 属性を使用できます。

## <a name="filters"></a>フィルター

関数のフィルター (プレビュー) は、独自のロジックで WebJobs 実行パイプラインをカスタマイズする方法を提供します。 フィルターは [ASP.NET Core フィルター](/aspnet/core/mvc/controllers/filters)に似ています。 関数またはクラスに適用される宣言型の属性として実装することができます。 詳細については、[関数フィルター](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters)を参照してください。

## <a name="logging-and-monitoring"></a>ログ記録と監視

ASP.NET 用に開発されたログ記録フレームワークをお勧めします。 使用方法については、[使用の開始](webjobs-sdk-get-started.md)に関する記事をご覧ください。 

### <a name="log-filtering"></a>ログのフィルタリング

`ILogger` インスタンスによって作成されたすべてのログには、関連付けられた `Category` と `Level` があります。 [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel) は列挙型で、整数コードは次のような相対的な重要度を示します。

|LogLevel    |コード|
|------------|---|
|Trace       | 0 |
|デバッグ       | 1 |
|Information | 2 |
|警告     | 3 |
|エラー       | 4 |
|Critical    | 5 |
|なし        | 6 |

各カテゴリを特定の [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel) に個別にフィルター処理できます。 たとえば、BLOB トリガー処理のすべてのログを表示するが、それ以外に関しては `Error` 以降のみを表示するなどが可能です。

#### <a name="version-3x"></a>バージョン 3.*x*

SDK のバージョン 3.*x* は、.NET Core に組み込まれているフィルタリングに依存しています。 `LogCategories` クラスを使用すると、特定の関数、トリガー、またはユーザーのカテゴリを定義することができます。 また、特定のホストの状態 (`Startup` や `Results` など) のフィルターも定義されています。 これにより、ログ記録の出力を微調整できます。 定義されたカテゴリ内で一致するものが見つからない場合、メッセージをフィルターするかどうかを決めるときに、フィルターは `Default` 値に戻ります。

`LogCategories` には、次の using ステートメントが必要です。

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

次の例では、既定で `Warning` レベルのすべてのログをフィルター処理するフィルターを構築します。 `Function` および `results` カテゴリ (バージョン 2.*x* の `Host.Results` と同等) は、`Error` レベルでフィルタリングされます。 フィルターは、現在のカテゴリを、`LogCategories` インスタンス内のすべての登録済みレベルと比較して、最も長い一致を選択します。 つまり、`Host.Triggers` に登録されている `Debug` レベルは、`Host.Triggers.Queue` や `Host.Triggers.Blob` に一致します。 これにより、カテゴリを 1 つずつ追加しなくても、より幅広いカテゴリを制御できます。

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging(logging =>
            {
                logging.SetMinimumLevel(LogLevel.Warning);
                logging.AddFilter("Function", LogLevel.Error);
                logging.AddFilter(LogCategories.CreateFunctionCategory("MySpecificFunctionName"),
                    LogLevel.Debug);
                logging.AddFilter(LogCategories.Results, LogLevel.Error);
                logging.AddFilter("Host.Triggers", LogLevel.Debug);
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>バージョン 2.*x*

SDK のバージョン 2.*x* では、`LogCategoryFilter` クラスを使用してフィルター処理を制御します。 `LogCategoryFilter` には `Default` プロパティがあり、初期値は `Information` です。つまり、`Information`、`Warning`、`Error`、`Critical` レベルのメッセージはすべてログに記録されますが、`Debug` または `Trace` レベルのメッセージは除外されます。

バージョン 3.*x* の `LogCategories` と同様に、`CategoryLevels` プロパティを使用すると特定のカテゴリのログ レベルを指定できるため、ロギング出力を微調整することができます。 `CategoryLevels` ディクショナリ内で一致するものが見つからない場合、メッセージをフィルターするかどうかを決定する際に、フィルターが `Default` 値にフォールバックします。

次の例は、既定値で `Warning` レベルのすべてのログをフィルタリングするフィルターを構築します。 `Function` および `Host.Results` カテゴリは、`Error` レベルでフィルター処理されます。 `LogCategoryFilter` は現在のカテゴリを、登録されているすべての `CategoryLevels` と比較して、最長一致を選択します。 したがって、`Host.Triggers` に登録されている `Debug` レベルは、`Host.Triggers.Queue` または `Host.Triggers.Blob` と一致します。 これにより、カテゴリを 1 つずつ追加しなくても、より幅広いカテゴリを制御できます。

```csharp
var filter = new LogCategoryFilter();
filter.DefaultLevel = LogLevel.Warning;
filter.CategoryLevels[LogCategories.Function] = LogLevel.Error;
filter.CategoryLevels[LogCategories.Results] = LogLevel.Error;
filter.CategoryLevels["Host.Triggers"] = LogLevel.Debug;

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(instrumentationKey, filter.Filter)
    .AddConsole(filter.Filter);
```

### <a name="custom-telemetry-for-application-insights"></a>Application Insights のカスタム テレメトリ

[Application Insights](../azure-monitor/app/app-insights-overview.md) 用のカスタム テレメトリを実装するプロセスは、SDK のバージョンによって異なります。 Application Insights の構成方法については、「[Application Insights ログの追加](webjobs-sdk-get-started.md#add-application-insights-logging)」を参照してください。

#### <a name="version-3x"></a>バージョン 3.*x*

WebJobs SDK のバージョン 3.*x* は、.NET Core 汎用ホストに依存しているため、カスタム テレメトリ ファクトリは提供されなくなりました。 ただし、依存関係インジェクションを使用してカスタム テレメトリをパイプラインに追加できます。 このセクションの例には、次の `using` ステートメントが必要です。

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

次の [`ITelemetryInitializer`] のカスタム実装を使用すると、独自の [`ITelemetry`](/dotnet/api/microsoft.applicationinsights.channel.itelemetry) を既定の [`TelemetryConfiguration`] に追加することができます。

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

ビルダーで [`ConfigureServices`] を呼び出して、カスタム [`ITelemetryInitializer`] をパイプラインに追加します。

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging((context, b) =>
    {
        // Add logging providers.
        b.AddConsole();

        // If this key exists in any config, use it to enable Application Insights.
        string appInsightsKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
        if (!string.IsNullOrEmpty(appInsightsKey))
        {
            // This uses the options callback to explicitly set the instrumentation key.
            b.AddApplicationInsights(o => o.InstrumentationKey = appInsightsKey);
        }
    });
    builder.ConfigureServices(services =>
        {
            services.AddSingleton<ITelemetryInitializer, CustomTelemetryInitializer>();
        });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

[`TelemetryConfiguration`] が構築されるとき、[`ITelemetryInitializer`] のすべての登録されている種類が組み込まれます。 詳しくは、「[カスタムのイベントとメトリックのための Application Insights API](../azure-monitor/app/api-custom-events-metrics.md)」をご覧ください。

バージョン 3.*x* では、ホストが停止するときに [`TelemetryClient`] をフラッシュする必要がなくなりました。 .NET Core 依存関係インジェクション システムが、登録されている `ApplicationInsightsLoggerProvider` を自動的に破棄し、それにより [`TelemetryClient`] がフラッシュされます。

#### <a name="version-2x"></a>バージョン 2.*x*

バージョン 2.*x* では、WebJobs SDK 用に Application Insights プロバイダーによって内部で作成された [`TelemetryClient`] では、[`ServerTelemetryChannel`](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/.publicApi/Microsoft.AI.ServerTelemetryChannel.dll) が使用されます。 Application Insights のエンドポイントが使用できない、または着信要求のスロットリングが行われている場合、このチャンネルが [Web アプリのファイル システムで要求を保存して、後でこれらを再送信](https://apmtips.com/posts/2015-09-03-more-telemetry-channels/)します。

[`TelemetryClient`] は、`ITelemetryClientFactory` を実装するクラスによって作成されます。 既定で、これは [`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/) です。

Application Insights パイプラインの一部を変更する場合、独自の `ITelemetryClientFactory` を提供できます。そして、ホストは提供されたクラスを使用して [`TelemetryClient`] を構築します。 たとえば、次のコードでは `DefaultTelemetryClientFactory` がオーバーライドされて、`ServerTelemetryChannel` のプロパティが変更されます。

```csharp
private class CustomTelemetryClientFactory : DefaultTelemetryClientFactory
{
    public CustomTelemetryClientFactory(string instrumentationKey, Func<string, LogLevel, bool> filter)
        : base(instrumentationKey, new SamplingPercentageEstimatorSettings(), filter)
    {
    }

    protected override ITelemetryChannel CreateTelemetryChannel()
    {
        ServerTelemetryChannel channel = new ServerTelemetryChannel();

        // Change the default from 30 seconds to 15 seconds.
        channel.MaxTelemetryBufferDelay = TimeSpan.FromSeconds(15);

        return channel;
    }
}
```

`SamplingPercentageEstimatorSettings` オブジェクトでは、[アダプティブ サンプリング](../azure-monitor/app/sampling.md)が構成されます。 つまり、特定の大規模なシナリオでは、Application Insights はテレメトリ データの選択されたサブセットをサーバーに送信します。

テレメトリ ファクトリを作成した後、Application Insights のログ プロバイダーにそれを渡します。

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a name="next-steps"></a><a id="nextsteps"></a> 次のステップ

この記事では、WebJobs SDK を操作するための一般的なシナリオの処理方法を示すコード スニペットを提供しました。 完全なサンプルは、[azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk/tree/dev/sample/SampleHost) を参照してください。

[`ExecutionContext`]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[`TelemetryClient`]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[`ConfigureServices`]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
[`ITelemetryInitializer`]: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
[`TelemetryConfiguration`]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
[`JobHostConfiguration`]: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs