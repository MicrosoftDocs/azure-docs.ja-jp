---
title: Durable Functions を WebJobs として実行する方法 - Azure
description: WebJobs SDK を使用して、Durable Function を WebJobs で実行するようにコーディングし、構成する方法について説明します。
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 826e475eb71563b52d687903aeac4ec936e267f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009502"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Durable Functions を WebJobs として実行する方法

既定で、Durable Functions ではオーケストレーションをホストするために Azure Functions ランタイムが使用されます。 ただし、特定のシナリオでは、イベントをリッスンするコードに対してより細かな制御が必要となる場合があります。 この記事では、WebJobs SDK を使用してオーケストレーションを実装する方法を示します。 Functions と WebJobs のより詳細な比較については、「[Functions と WebJobs の比較](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs)」を参照してください。

[Azure Functions](../functions-overview.md) と [Durable Functions](durable-functions-overview.md) 拡張機能は、[WebJobs SDK](../../app-service/webjobs-sdk-how-to.md) に基づいて構築されています。 WebJobs SDK のジョブ ホストは、Azure Functions 内のランタイムです。 Azure Functions では不可能な方法で動作を制御する必要がある場合は、WebJobs SDK を使用して Durable Functions を独自に開発し、実行することができます。

WebJobs SDK のバージョン 3.x では、このホストは `IHost` の実装であり、バージョン 2.x では `JobHost` オブジェクトを使用します。

Durable Functions のチェーンのサンプルは、WebJobs SDK 2.x バージョンで提供されています。[Durable Functions リポジトリ](https://github.com/azure/azure-functions-durable-extension/)をダウンロードまたは複製し、*v1* ブランチをチェックアウトして、*samples\\webjobssdk\\chaining* フォルダーに移動してください。

## <a name="prerequisites"></a>前提条件

この記事では、WebJobs SDK、Azure Functions 用の C# クラス ライブラリ開発、および Durable Functions について、読者が基本的な知識を持っていることを前提としています。 これらのトピックの概要については、次のリソースを参照してください。

* [WebJobs SDK の概要](../../app-service/webjobs-sdk-get-started.md)
* [Visual Studio での初めての関数の作成](../functions-create-your-first-function-visual-studio.md)
* [Durable Functions](durable-functions-sequence.md)

この記事の手順を完了するには、次のことが条件となります。

* **Azure 開発** ワークロードと共に [Visual Studio 2019 をインストール](/visualstudio/install/)します。

  Visual Studio は既にあるものの、必要なワークロードがない場合は、 **[ツール]**  >  **[ツールと機能の取得]** の順に選択してワークロードを追加してください。

  (代わりに [Visual Studio Code](https://code.visualstudio.com/) を使うこともできますが、一部の説明は Visual Studio に固有のものです。)

* [Azure Storage Emulator](../../storage/common/storage-use-emulator.md) バージョン 5.2 以降をインストールして実行します。 これは、*App.config* ファイルを Azure Storage の接続文字列で更新することで代用できます。

## <a name="webjobs-sdk-versions"></a>WebJobs SDK のバージョン

この記事では、WebJobs SDK 2.x プロジェクト (Azure Functions バージョン 1.x に相当) の開発方法について説明します。 バージョン 3.x について詳しくは、この記事で後述する「[WebJobs SDK 3.x](#webjobs-sdk-3x)」をご覧ください。

## <a name="create-a-console-app"></a>コンソール アプリを作成する

Durable Functions を WebJobs として実行するには、まず、コンソール アプリを作成する必要があります。 WebJobs SDK プロジェクトは、適切な NuGet パッケージがインストールされたコンソール アプリ プロジェクトです。

Visual Studio の **[新しいプロジェクト]** ダイアログ ボックスで、 **[Windows クラシック デスクトップ]**  >  **[コンソール アプリ (.NET Framework)]** を選択します。 プロジェクト ファイルでは、`TargetFrameworkVersion` が `v4.6.1` になっています。

Visual Studio には、WebJob プロジェクト テンプレートも用意されています。これを使用するには、 **[クラウド]**  >  **[Azure WebJob (.NET Framework)]** を選択します。 このテンプレートでは多数のパッケージがインストールされますが、これらの一部は必要ない場合があります。

## <a name="install-nuget-packages"></a>NuGet パッケージのインストール

必要な NuGet パッケージは、WebJobs SDK、コア バインディング、ログ記録フレームワーク、および Durable Task 拡張機能用のパッケージです。 次に示すのは、これらのパッケージに対応する **パッケージ マネージャー コンソール** コマンドです。バージョン番号は、この記事の作成時点での、最新かつ安定したバージョンの番号です。

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.8.3
```

ログ プロバイダーも必要です。 次に示すのは、Azure Application Insights プロバイダーと `ConfigurationManager` をインストールするコマンドです。 `ConfigurationManager` では、Application Insights のインストルメンテーション キーをアプリ設定から取得できます。

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

次に示すのは、コンソール プロバイダーをインストールするコマンドです。

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>JobHost コード

コンソール アプリを作成し、必要な NuGet パッケージをインストールしたので、Durable Functions を使用する準備ができました。 そのためには、JobHost コードを使用します。

Durable Functions 拡張機能を使用するには、`Main` メソッド内の `JobHostConfiguration` オブジェクトで `UseDurableTask` を呼び出します。

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

`DurableTaskExtension` オブジェクトで設定できるプロパティの一覧については、「[host.json](../functions-host-json.md#durabletask)」をご覧ください。

`Main` メソッドでは、ログ プロバイダーも設定します。 次の例では、コンソールと Application Insights プロバイダーを構成します。

```cs
static void Main(string[] args)
{
    using (var loggerFactory = new LoggerFactory())
    {
        var config = new JobHostConfiguration();

        config.DashboardConnectionString = "";

        var instrumentationKey =
            ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];

        config.LoggerFactory = loggerFactory
            .AddApplicationInsights(instrumentationKey, null)
            .AddConsole();

        config.UseTimers();
        config.UseDurableTask(new DurableTaskExtension
        {
            HubName = "MyTaskHub",
        });
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="functions"></a>関数

WebJobs のコンテキスト内の Durable Functions と、Azure Functions のコンテキスト内の Durable Functions はやや異なります。 コードを記述する際は、この相違点に注意することが重要です。

WebJobs SDK では、次の Azure Functions 機能はサポートされません。

* [FunctionName 属性](#functionname-attribute)
* [HTTP トリガー](#http-trigger)
* [Durable Functions の HTTP 管理 API](#http-management-api)

### <a name="functionname-attribute"></a>FunctionName 属性

WebJobs SDK プロジェクトでは、関数名が関数のメソッド名になります。 `FunctionName` 属性は Azure Functions でしか使用されません。

### <a name="http-trigger"></a>HTTP トリガー

WebJobs SDK には、HTTP トリガーはありません。 サンプル プロジェクトのオーケストレーション クライアントでは、タイマー トリガーが使用されています。

```cs
public static async Task CronJob(
    [TimerTrigger("0 */2 * * * *")] TimerInfo timer,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger logger)
{
  ...
}
```

### <a name="http-management-api"></a>HTTP 管理 API

WebJobs SDK では、HTTP トリガーがないため、[HTTP 管理 API](durable-functions-http-api.md) もありません。

WebJobs SDK プロジェクトでは、HTTP 要求を送信する代わりに、オーケストレーション クライアント オブジェクト上でメソッドを呼び出すことができます。 次に示すのは、HTTP 管理 API で実行できる 3 つのタスクに対応したメソッドです。

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

サンプル プロジェクトのオーケストレーション クライアント関数は、オーケストレーター関数を起動した後、`GetStatusAsync` を 2 秒ごとに呼び出すループに入ります。

```cs
string instanceId = await client.StartNewAsync(nameof(HelloSequence), input: null);
logger.LogInformation($"Started new instance with ID = {instanceId}.");

DurableOrchestrationStatus status;
while (true)
{
    status = await client.GetStatusAsync(instanceId);
    logger.LogInformation($"Status: {status.RuntimeStatus}, Last update: {status.LastUpdatedTime}.");

    if (status.RuntimeStatus == OrchestrationRuntimeStatus.Completed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Failed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Terminated)
    {
        break;
    }

    await Task.Delay(TimeSpan.FromSeconds(2));
}
```

## <a name="run-the-sample"></a>サンプルを実行する

WebJob として実行されるように設定された Durable Functions を取得できたので、これが、Durable Functions をスタンドアロン Azure Functions として実行する場合とどのように異なるのかを理解します。 この時点で、それがサンプル内で動作するのを確認することをお勧めします。

このセクションでは、[サンプル プロジェクト](https://github.com/Azure/azure-functions-durable-extension/tree/v1/samples/webjobssdk/chaining)の実行方法について概説します。 WebJobs SDK プロジェクトをローカルで実行し、それを Azure WebJob にデプロイする方法について詳しくは、「[WebJobs SDK の概要](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob)」をご覧ください。

### <a name="run-locally"></a>ローカルで実行する

1. ストレージ エミュレーターが実行されていることを確認します (「[前提条件](#prerequisites)」をご覧ください)。

1. プロジェクトをローカルで実行するときに Application Insights でログを表示するには:

    a. Application Insights リソースを作成し、それに対するアプリの種類として **[全般]** を使用します。

    b. インストルメンテーション キーを *App.config* ファイルに保存します。

1. プロジェクトを実行します。

### <a name="run-in-azure"></a>Azure で実行する

1. Web アプリとストレージ アカウントを作成します。

1. Web アプリで、`AzureWebJobsStorage` という名前のアプリ設定にストレージ接続文字列を保存します。

1. Application Insights リソースを作成し、それに対するアプリの種類として **[全般]** を使用します。

1. `APPINSIGHTS_INSTRUMENTATIONKEY` という名前のアプリ設定に、インストルメンテーション キーを保存します。

1. WebJob としてデプロイします。

## <a name="webjobs-sdk-3x"></a>WebJobs SDK 3.x

この記事では、WebJobs SDK 2.x プロジェクトの開発方法について説明します。 [WebJobs SDK 3.x](../../app-service/webjobs-sdk-get-started.md) プロジェクトを開発中である場合、このセクションは変更履歴を理解するのに役立ちます。

導入された主な変更は、.NET Framework の代わりに .NET Core を使用することです。 WebJobs SDK 3.x プロジェクトの作成手順は基本的に以前と同じですが、次の点が異なります。

1. .NET Core コンソール アプリを作成します。 Visual Studio の **[新しいプロジェクト]** ダイアログ ボックスで、 **[.NET Core]**  >  **[コンソール アプリ (.NET Core)]** を選択します。 プロジェクト ファイルでは、`TargetFramework` が `netcoreapp2.x` と指定されています。

1. 次のパッケージのリリース版 WebJobs SDK 3.x を選択します。

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. ストレージ接続文字列と Application Insights のインストルメンテーション キーを、.NET Core の構成フレームワークを使用して、*appsettings.json* ファイル内に設定します。 次に例を示します。

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. `Main` メソッドを変更して、これを行います。 次に例を示します。

   ```cs
   static void Main(string[] args)
   {
        var hostBuilder = new HostBuilder()
            .ConfigureWebJobs(config =>
            {
                config.AddAzureStorageCoreServices();
                config.AddAzureStorage();
                config.AddTimers();
                config.AddDurableTask(options =>
                {
                    options.HubName = "MyTaskHub";
                    options.AzureStorageConnectionStringName = "AzureWebJobsStorage";
                });
            })
            .ConfigureLogging((context, logging) =>
            {
                logging.AddConsole();
                logging.AddApplicationInsights(config =>
                {
                    config.InstrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                });
            })
            .UseConsoleLifetime();

        var host = hostBuilder.Build();

        using (host)
        {
            host.Run();
        }
   }
   ```

## <a name="next-steps"></a>次のステップ

WebJobs SDK について詳しくは、「[How to use the WebJobs SDK (WebJobs SDK の使用方法)](../../app-service/webjobs-sdk-how-to.md)」をご覧ください。
