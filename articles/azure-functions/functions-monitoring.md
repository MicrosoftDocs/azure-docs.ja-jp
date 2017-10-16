---
title: "Azure Functions を監視する"
description: "Azure Application Insights を Azure Functions とともに使用して、関数の実行を監視する方法を説明します。"
services: functions
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, 機能, イベント処理, Webhook, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/15/2017
ms.author: tdykstra
ms.openlocfilehash: 355cb2cef52b5dfecddae228d0cc24a069d3b695
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-azure-functions"></a>Azure Functions を監視する

## <a name="overview"></a>概要 

[Azure Functions](functions-overview.md) には、関数を監視するための [Azure Application Insights](../application-insights/app-insights-overview.md) とのビルトイン統合機能が用意されています。 この記事では、Functions がテレメトリ データを Application Insights に送信するように構成する方法を示します。

![Application Insights メトリックス エクスプローラー](media/functions-monitoring/metrics-explorer.png)

Functions には、Application Insights を使用しないビルトイン監視機能もあります。 Application Insights ではより多くのデータや優れたデータ分析方法が提供されるため、Application Insights の使用をお勧めします。 ビルトイン監視機能については、[この記事の最後のセクション](#monitoring-without-application-insights)をご覧ください。

## <a name="enable-application-insights-integration"></a>Application Insights との統合を有効にする

関数アプリでデータを Application Insights に送信するには、Application Insights インスタンスのインストルメンテーション キーについて知っておく必要があります。 [Azure Portal](https://portal.azure.com) でその接続を行うには、次の 2 つの方法があります。

* [関数アプリの作成時に、接続された Application Insights インスタンスを作成する](#new-function-app)。
* [Application Insights インスタンスを既存の関数アプリに接続する](#existing-function-app)。
 
### <a name="new-function-app"></a>新しい関数アプリ

次のようにして、Function App の **[作成]** ページで Application Insights を有効にします。

1. **[Application Insights]** スイッチを **[オン]** に設定します。

2. **Application Insights の場所**を選択します。

   ![関数アプリの作成時に Application Insights を有効にする](media/functions-monitoring/enable-ai-new-function-app.png)

### <a name="existing-function-app"></a>既存の関数アプリ

次のようにして、インストルメンテーション キーを取得し、関数アプリに保存します。

1. Application Insights インスタンスを作成します。 アプリケーションの種類を **[全般]** に設定します。

   ![Application Insights インスタンスを作成して [全般] に指定する](media/functions-monitoring/ai-general.png)

2. Application Insights インスタンスの **[要点]** ページからインストルメンテーション キーをコピーします。 表示されているキー値の末尾にカーソルを合わせて、**[コピーするにはクリックします]** ボタンを表示します。

   ![Application Insights のインストルメンテーション キーをコピーする](media/functions-monitoring/copy-ai-key.png)

1. 関数アプリの **[アプリケーションの設定]** ページで、APPINSIGHTS_INSTRUMENTATIONKEY という名前の[アプリ設定を追加し](functions-how-to-use-azure-function-app-settings.md#settings)、インストルメンテーション キーを貼り付けます。

   ![インストルメンテーション キーをアプリ設定に追加する](media/functions-monitoring/add-ai-key.png)

1. [ **Save**] をクリックします。

## <a name="view-telemetry-data"></a>テレメトリ データを表示する

ポータルで関数アプリから Application Insights に移動するには、関数アプリの **[概要]** ページで **[Application Insights]** リンクを選びます。

Application Insights の使用方法については、「[Application Insights のドキュメント](https://docs.microsoft.com/azure/application-insights/)」をご覧ください。 このセクションでは、Application Insights でデータを表示する方法の例をいくつか示します。 Application Insights をすでに使い慣れている場合は、[テレメトリ データの構成とカスタマイズに関するセクション](#configure-categories-and-log-levels)に直接進んでかまいません。

[メトリックス エクスプローラー](../application-insights/app-insights-metrics-explorer.md)では、グラフを作成したり、関数呼び出しの数、実行時間、成功率などのメトリックに基づいて警告を表示したりできます。

![メトリックス エクスプローラー](media/functions-monitoring/metrics-explorer.png)

[[失敗]](../application-insights/app-insights-asp-net-exceptions.md) タブでは、グラフを作成したり、関数の失敗やサーバーの例外に基づいて警告を表示したりできます。 **[操作名]** は関数名です。 依存関係に関する[カスタム テレメトリ](#custom-telemetry-in-c-functions)を実装している場合を除き、依存関係のエラーは表示されません。

![エラー](media/functions-monitoring/failures.png)

[[パフォーマンス]](../application-insights/app-insights-performance-counters.md) タブでは、パフォーマンスの問題を分析できます。

![パフォーマンス](media/functions-monitoring/performance.png)

**[サーバー]** タブには、リソース使用率とサーバーあたりのスループットが表示されます。 このデータは、関数が原因で基本リソースの処理が遅延している場合のデバッグで役立つことがあります。 サーバーは、*クラウド ロール インスタンス*と呼ばれます。 

![サーバー](media/functions-monitoring/servers.png)

[[Live Metrics Stream]](../application-insights/app-insights-live-stream.md) タブには、リアルタイムで作成されたメトリック データが表示されます。

![ライブ ストリーム](media/functions-monitoring/live-stream.png)

## <a name="query-telemetry-data"></a>テレメトリをクエリする

[Application Insights Analytics](../application-insights/app-insights-analytics.md) では、データベース内のテーブルの形式ですべてのテレメトリ データにアクセスできます。 Analytics では、データを抽出、操作するためのクエリ言語が用意されています。

![Analytics を選択する](media/functions-monitoring/select-analytics.png)

![分析の例](media/functions-monitoring/analytics-traces.png)

クエリの例を次に示します。 ここでは、直近 30 分間の worker あたりの要求数の分布を示します。

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

使用可能なテーブルは、左側のペインの **[スキーマ]** タブに表示されます。 次のテーブルで、関数呼び出しによって生成されたデータを確認できます。

* **traces** - ランタイムや関数コードによって作成されたログ。
* **requests** - 関数呼び出しごとの要求。
* **exceptions** - ランタイムによってスローされた例外。
* **customMetrics** - 呼び出しの成功数と失敗数、成功率、時間。
* **customEvents** - ランタイムによって追跡されたイベント。たとえば、関数をトリガーする HTTP 要求など。
* **performanceCounters** - 関数が実行されているサーバーのパフォーマンスに関する情報。

その他のテーブルには、可用性テストと、クライアントとブラウザーのテレメトリがあります。 カスタム テレメトリを実装して、テーブルにデータを追加できます。

各テーブルでは、関数固有のデータの一部が `customDimensions` フィールドに保存されます。  たとえば、次のクエリでは、ログ レベルが `Error` のすべてのトレースが取得されます。

```
traces 
| where customDimensions.LogLevel == "Error"
```

ランタイムにより、`customDimensions.LogLevel` と `customDimensions.Category` が提供されます。 関数コードで記述したログにフィールドを追加できます。 この記事の後半にある「[構造化ログ](#structured-logging)」をご覧ください。

## <a name="configure-categories-and-log-levels"></a>カテゴリとログ レベルを構成する

Application Insights はカスタム構成なしで使用できますが、既定の構成ではデータ量が多くなる可能性があります。 Visual Studio Azure サブスクリプションを使用している場合、App Insights のデータ上限に達する可能性があります。 この記事の残りの部分では、関数から Application Insights に送信するデータを構成し、カスタマイズする方法を説明します。

### <a name="categories"></a>カテゴリ

Azure Functions のロガーでは、すべてのログに*カテゴリ*があります。 カテゴリは、ランタイム コードや関数コードのどの部分にログが記述されているかを示します。 

Functions のランタイムでは、先頭が "Host" のカテゴリを持つログが作成されます。 たとえば、"Function started"、"Function executed"、"Function completed" のログのカテゴリは "Host.Executor" になります。 

ご使用の関数コードでログを記述する場合、カテゴリは "Function" になります。

### <a name="log-levels"></a>ログ レベル

Azure Functions ロガーでは、すべてのログに*ログ レベル*も含まれています。 [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel#Microsoft_Extensions_Logging_LogLevel) は列挙型で、整数コードは次のような相対的な重要度を示します。

|LogLevel    |コード|
|------------|---|
|Trace       | 0 |
|デバッグ       | 1 |
|情報 | 2 |
|警告     | 3 |
|エラー       | 4 |
|重大    | 5 |
|なし        | 6 |

ログ レベル `None` については、次のセクションで説明します。 

### <a name="configure-logging-in-hostjson"></a>Host.json でログを構成する

*Host.json* ファイルでは、関数アプリから Application Insights に送信するログの量を設定します。 カテゴリごとに、送信する最小のログ レベルを指定します。 次に例を示します。

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

この例では、次のルールを設定します。

1. カテゴリが "Host.Results" または "Function" のログの場合は、`Error` 以上のレベルのみを Application Insights に送信する。 `Information` 以下のレベルのログは無視する。
2. カテゴリが "Host" のログの場合は、 `Information` 以上のレベルのみを Application Insights に送信する。 `Debug` 以下のレベルのログは無視する。
3. その他すべてのログについては、`Information` 以上のレベルのみを Application Insights に送信する。

*host.json* 内のカテゴリ値により、先頭の値が同一のすべてのカテゴリについてログを制御する。 たとえば、*host.json* 内の "Host" は、"Host.General"、"Host.Executor"、"Host.Results" などのログを制御します。

*host.json* に先頭の文字列が同一のカテゴリが複数含まれる場合は、同一の部分がより長いものが最初に一致する。 たとえば、"Host.Aggregator" 以外のランタイムからのすべてを `Error` レベルで記録し、"Host.Aggregator" のログを `Information` レベルで記録するとします。

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

あるカテゴリのすべてのログを抑制するには、ログ レベル `None` を使用します。 そのカテゴリのログは書き込まれず、その上のログ レベルはありません。

次のセクションでは、ランタイムが作成するログの主なカテゴリについて説明します。 

### <a name="category-hostresults"></a>カテゴリ Host.Results

これらのログは、Application Insights では "requests" として示されます。 それらは、関数の成功または失敗を示します。

![要求回数グラフ](media/functions-monitoring/requests-chart.png)

これらすべてのログは `Information` レベルで書き込まれるため、`Warning` 以上でフィルターすると、このデータは表示されなくなります。

### <a name="category-hostaggregator"></a>カテゴリ Host.Aggregator

これらのログには、[構成可能な](#configure-the-aggregator)期間の関数呼び出しの回数と平均回数が記録されます。 既定の期間は、30 秒か 1,000 回のどちらか早い方です。 

これらのログは、Application Insights では "customMetrics" として示されます。 例としては、実行回数、成功率、時間などがあります。

![customMetrics クエリ](media/functions-monitoring/custom-metrics-query.png)

これらすべてのログは `Information` レベルで書き込まれるため、`Warning` 以上でフィルターすると、このデータは表示されなくなります。

### <a name="other-categories"></a>その他のカテゴリ

すでに示されているカテゴリ以外のカテゴリのすべてのログは、Application Insights では "traces" として示されます。

![traces クエリ](media/functions-monitoring/analytics-traces.png)

"Host" で始まるカテゴリのすべてのログは、Functions ランタイムによって書き込まれます。 "Function started" と "Function completed" のログのカテゴリは "Host.Executor" となります。 正常に実行された場合、これらのログは `Information` レベルとなり、例外は `Error` レベルで記録されます。 ランタイムでは、`Warning` レベルのログも作成されます。たとえば、"queue messages sent to the poison queue" などです。

ご使用の関数コードが書き込んだログのカテゴリは "Function" となり、どのログ レベルにもできます。

## <a name="configure-the-aggregator"></a>アグリゲーターを構成する

前のセクションで述べたように、ランタイムでは期間内の関数実行についてデータが集計されます。 既定の期間は、30 秒か 1,000 回実行のどちらか早い方です。 *host.json* ファイル内でこの設定を構成できます。  次に例を示します。

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>サンプリングを構成する

Application Insights には、負荷がピークのときにテレメトリ データが生成されすぎないようにする[サンプリング](../application-insights/app-insights-sampling.md)機能が備わっています。 Application Insights では、テレメトリ項目の数が特定の割合を超えると、受信した項目の一部がランダムに無視され始めます。 *host.json* でサンプリングを構成できます。  次に例を示します。

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```

## <a name="write-logs-in-c-functions"></a>C# 関数でログを書き込む

Application Insights で traces として表示されるログを、ご使用の関数コードで書き込むことができます。

### <a name="ilogger"></a>ILogger

関数では、`TraceWriter` パラメーターではなく [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) パラメーターを使用します。 `TraceWriter` を使って作成されたログは Application Insights に送られますが、`ILogger` では[構造化ログ](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)を記録することができます。

`ILogger` オブジェクトで、`Log<level>` [ILogger 上の拡張メソッド](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loggerextensions#Methods_)を呼び出し、ログを作成します。 たとえば、次のコードでは、カテゴリが "Function" の `Information` ログが書き込まれます。

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>構造化ログ

ログ メッセージで使用するパラメーターは、プレースホルダーの名前ではなく順序によって決定されます。 たとえば、次のコードがあるとします。

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

同じメッセージ文字列を維持しながらパラメーターの順序を逆にすると、結果のメッセージ テキストではそれらの値が誤った場所に配置されます。

プレースホルダーはこのように処理されるため、構造化ログを実行できます。 Application Insights では、メッセージ文字列だけでなく、パラメーターの名前と値のペアも保存します。 そのため、メッセージ引数はクエリ可能なフィールドになります。

たとえば、前の例のようなロガー メソッド呼び出しでは、フィールド `customDimensions.prop__rowKey` をクエリできます。 プレフィックスを追加して、ランタイムが追加したフィールドと、ご使用の関数コードが追加したフィールドとの間に競合が起こらないようにします。

フィールド `customDimensions.prop__{OriginalFormat}` を参照することで、元のメッセージ文字列をクエリすることもできます。  

`customDimensions` データの JSON 表現の例を次に示します。

```json
{
  customDimensions: {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="logging-custom-metrics"></a>カスタム メトリックのログ記録  

C# スクリプト関数では、`ILogger` 上の `LogMetric` 拡張メソッドを使用して、Application Insights でのカスタム メトリックを作成できます。 メソッド呼び出しの例を次に示します。

```csharp
logger.LogMetric("TestMetric", 1234); 
```

[.NET 用 Application Insights API ](#custom-telemetry-in-c-functions) を使用して `TrackMetric` を呼び出す代わりに、このコードを使用できます。

## <a name="write-logs-in-javascript-functions"></a>JavaScript 関数でログを書き込む

Node.js 関数では、`context.log` を使用してログを書き込みます。 構造化ログは使用できません。

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="logging-custom-metrics"></a>カスタム メトリックのログ記録  

Node.js 関数では、`context.log.metric` メソッドを使用して、Application Insights でカスタム メトリックを作成できます。 メソッド呼び出しの例を次に示します。

```javascript
context.log.metric("TestMetric", 1234); 
```

[Application Insights 用 Node.js SDK ](#custom-telemetry-in-javascript-functions) を使用して `trackMetric` を呼び出す代わりに、このコードを使用できます。

## <a name="custom-telemetry-in-c-functions"></a>C# 関数でのカスタム テレメトリ

[Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) NuGet パッケージを使用して、カスタム テレメトリ データを Application Insights に送信できます。

[カスタム テレメトリ API](../application-insights/app-insights-api-custom-events-metrics.md) を使用する C# コードの例を次に示します。 この例は .NET クラス ライブラリ用ですが、Application Insights のコードは C# スクリプト用と同じです。

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetry = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;

            telemetry.Context.Operation.Id = context.InvocationId.ToString();
            telemetry.Context.Operation.Name = "cs-http";
            if (!String.IsNullOrEmpty(name))
            {
                telemetry.Context.User.Id = name;
            }
            telemetry.TrackEvent("Function called");
            telemetry.TrackMetric("Test Metric", DateTime.Now.Millisecond);
            telemetry.TrackDependency("Test Dependency", 
                "swapi.co/api/planets/1/", 
                start, DateTime.UtcNow - start, true);

            return name == null
                ? req.CreateResponse(HttpStatusCode.BadRequest, 
                    "Please pass a name on the query string or in the request body")
                : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
        }
    }
}
```

関数呼び出しの要求が重複するため、`TrackRequest` や `StartOperation<RequestTelemetry>` を呼び出さないでください。  Functions ランタイムでは、要求が自動的に追跡されます。

関数が起動されるたびに、呼び出し ID に `telemetry.Context.Operation.Id` が設定されます。 これにより、指定された関数呼び出しのすべてのテレメトリ項目を関連付けることができます。

```cs
telemetry.Context.Operation.Id = context.InvocationId.ToString();
```

## <a name="custom-telemetry-in-javascript-functions"></a>JavaScript 関数でのカスタム テレメトリ

[Application Insights Node.js SDK](https://www.npmjs.com/package/applicationinsights) は、現在はベータ版です。 カスタム テレメトリを Application Insights に送信するサンプル コードをいくつか次に示します。

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    client.trackEvent({name: "my custom event", tagOverrides:{"ai.operation.id": context.invocationId}, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackTrace({message: "trace message", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:{"ai.operation.id": context.invocationId}});

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

`tagOverrides` パラメーターにより、関数の呼び出し ID に `operation_Id` を設定します。 この設定により、指定された関数呼び出しの自動生成されたカスタム テレメトリをすべて関連付けることができます。

## <a name="known-issues"></a>既知の問題

### <a name="dependencies"></a>依存関係

依存関係は自動的には表示されませんが、依存関係を表示するようにカスタム コードを記述することができます。 [C# カスタム テレメトリ セクション](#custom-telemetry-in-c-functions)にあるサンプル コードで方法を示します。 このサンプル コードでは、次のような Application Insights の*アプリケーション マップ*が作成されます。

![アプリケーション マップ](media/functions-monitoring/app-map.png)

### <a name="report-issues"></a>レポートに関する問題

Functions での Application Insights 統合に関する問題をレポートしたり、提案や要求を行ったりするには、[GitHub で問題を作成します](https://github.com/Azure/Azure-Functions/issues/new)。

## <a name="monitoring-without-application-insights"></a>Application Insights を使用しない監視

関数の監視には Application Insights の使用をお勧めします。Application Insights ではより多くのデータと優れたデータ分析方法が提供されるためです。 ただし、テレメトリ データやログ データは Azure Portal の Function App のページで確認することもできます。 

関数の **[監視]** タブを選択すると、関数実行のリストが表示されます。 関数実行を選択して、時間、入力データ、エラー、および関連するログ ファイルを確認します。

> [!IMPORTANT]
> Azure Functions で[従量課金ホスティング プラン](functions-overview.md#pricing)を使用する場合、Function App の **[監視]** タイルにはデータが表示されません。 これは、プラットフォームがコンピューティング インスタンスを動的にスケーリングおよび管理するためです。 これらのメトリックは、従量課金プランでは意味がありません。

### <a name="real-time-monitoring"></a>リアルタイム監視

リアルタイム監視を行うには、関数の **[監視]** タブで **[ライブ イベント ストリーム]** をクリックします。ライブ イベント ストリームは、ブラウザー内の新しいタブにグラフで表示されます

> [!NOTE]
> データの取り込みに失敗する可能性がある、既知の問題があります。 イベント ストリーム データを正しく取り込むために、ライブ イベント ストリームが表示されているブラウザー タブを閉じて、**[ライブ イベント ストリーム]** を再度クリックする必要があります。 

これらの統計はリアルタイムですが、実行データの実際のグラフには 10 秒程度の遅延がある場合があります。

### <a name="monitor-log-files-from-a-command-line"></a>コマンド ラインからログ ファイルを監視する

Azure コマンド ライン インターフェイス (CLI) 1.0 または PowerShell を使用して、ローカル ワークステーションのコマンド ライン セッションにログ ファイルをストリーミングできます。

### <a name="monitor-function-app-log-files-with-the-azure-cli-10"></a>Azure CLI 1.0 で関数アプリのログ ファイルを監視する

開始するには、[Azure CLI 1.0 をインストールして](../cli-install-nodejs.md)、[Azure にサインインします](../xplat-cli-connect.md)。

次のコマンドを使用して、クラシック サービス管理モードを有効にし、ご使用のサブスクリプションを選択して、ログ ファイルをストリーミングします。

```
azure config mode asm
azure account list
azure account set <subscriptionNameOrId>
azure site log tail -v <function app name>
```

### <a name="monitor-function-app-log-files-with-powershell"></a>PowerShell で関数アプリのログ ファイルを監視する

開始するには、[Azure PowerShell をインストールおよび構成](/powershell/azure/overview)します。

次のコマンドを使用して、ご使用の Azure アカウントを追加し、ご使用のサブスクリプションを選択して、ログ ファイルをストリーミングします。

```
PS C:\> Add-AzureAccount
PS C:\> Get-AzureSubscription
PS C:\> Get-AzureSubscription -SubscriptionName "MyFunctionAppSubscription" | Select-AzureSubscription
PS C:\> Get-AzureWebSiteLog -Name MyFunctionApp -Tail
```

詳細については、[Web アプリのログをストリーミングする方法](../app-service/web-sites-enable-diagnostic-log.md#streamlogs)に関するページをご覧ください。 

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Application Insights の詳細を知る](https://docs.microsoft.com/azure/application-insights/)

> [!div class="nextstepaction"]
> [Functions で使用されるログ記録フレームワークの詳細を知る](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)
