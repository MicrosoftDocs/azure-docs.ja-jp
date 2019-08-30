---
title: Azure Functions を監視する
description: Azure Application Insights を Azure Functions とともに使用して、関数の実行を監視する方法を説明します。
services: functions
author: ggailey777
manager: jeconnoc
keywords: Azure Functions, 機能, イベント処理, Webhook, 動的コンピューティング, サーバーなしのアーキテクチャ
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: glenga
ms.openlocfilehash: 7220c72625697490d9c42dab7e18ca4bf64d3f43
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70085626"
---
# <a name="monitor-azure-functions"></a>Azure Functions を監視する

[Azure Functions](functions-overview.md) には、関数を監視するための [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) とのビルトイン統合機能が用意されています。 この記事では、システムによって生成されたログ ファイルを Azure Functions が Application Insights に送信するように構成する方法を示します。

Application Insights を使用することをお勧めします。これにより、ログ、パフォーマンス、およびエラー データが収集されます。 また、パフォーマンスの異常が自動的に検出されるほか、問題の診断や、関数がどのように使用されているかの理解に役立つ強力な分析ツールも含まれています。 Application Insights は、パフォーマンスやユーザビリティを継続的に向上させるうえで役立つように設計されています。 ローカル関数アプリ プロジェクトの開発中に Application Insights を使用することもできます。 詳細については、「[Application Insights とは何か](../azure-monitor/app/app-insights-overview.md)」を参照してください。

必要な Application Insights インストルメンテーションは Azure Functions に組み込まれているため、必要になるのは、関数アプリを Application Insights リソースに接続するための有効なインストルメンテーション キーだけです。

## <a name="application-insights-pricing-and-limits"></a>Application Insights の価格と制限

Function App との Application Insights 統合は無料でお試しいただくことができます。 1 日に無料で処理できるデータ量には上限があります。 この上限には、テスト中に達する場合もあります。 1 日あたりの上限に近づいた場合は、ポータルと電子メール通知でお知らせします。 これらのアラートに気が付かず、上限に達してしまった場合は、新しいログが Application Insights クエリに表示されません。 不要なトラブルシューティングに時間を費やさずにすむように、上限には気を付けてください。 詳細については、「[Application Insights での価格とデータ ボリュームの管理](../azure-monitor/app/pricing.md)」を参照してください。

## <a name="enable-application-insights-integration"></a>Application Insights との統合を有効にする

関数アプリでデータを Application Insights に送信するには、Application Insights リソースのインストルメンテーション キーについて知っておく必要があります。 キーは、**APPINSIGHTS_INSTRUMENTATIONKEY** という名前のアプリ設定に指定されている必要があります。

### <a name="new-function-app-in-the-portal"></a>ポータルでの新しい関数アプリ

[Azure Portal で関数アプリを作成](functions-create-first-azure-function.md)すると、Application Insights 統合が既定で有効になります。 Application Insights リソースは関数アプリと同じ名前を持ち、同じリージョンまたは最も近いリージョンのどちらかで作成されます。

作成されている Application Insights リソースを確認するには、それを選択して **[Application Insights]** ウィンドウを展開します。 **[新しいリソース名]** を変更するか、またはデータを格納する [Azure 地理的環境](https://azure.microsoft.com/global-infrastructure/geographies/)内の別の **[場所]** を選択することができます。

![関数アプリの作成時に Application Insights を有効にする](media/functions-monitoring/enable-ai-new-function-app.png)

**[作成]** を選択すると、関数アプリと共に Application Insights リソースが作成され、アプリケーション設定の `APPINSIGHTS_INSTRUMENTATIONKEY` が設定されます。 これで、すべて準備ができました。

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>既存の関数アプリに追加する 

[Azure CLI](functions-create-first-azure-function-azure-cli.md)、[Visual Studio](functions-create-your-first-function-visual-studio.md)、または [Visual Studio Code](functions-create-first-function-vs-code.md) を使用して関数アプリを作成する場合は、Application Insights リソースを作成する必要があります。 その後、関数アプリのアプリケーション設定として、そのリソースからインストルメンテーション キーを追加できます。

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

初期バージョンの関数では組み込みの監視を使用していましたが、これは推奨されなくなりました。 このような関数アプリで Application Insights 統合を有効にする場合は、[組み込みログも無効にする](#disable-built-in-logging)必要があります。  

## <a name="view-telemetry-in-monitor-tab"></a>[監視] タブにテレメトリを表示する

[Application Insights 統合が有効になっている](#enable-application-insights-integration)場合は、 **[監視]** タブでテレメトリ データを表示できます。

1. 関数アプリのページで、Application Insights が構成されてから少なくとも 1 回実行された関数を選択します。 その後、 **[監視]** タブを選択します。

   ![[監視] タブを選択する](media/functions-monitoring/monitor-tab.png)

1. 関数呼び出しの一覧が表示されるまで、 **[更新]** を一定間隔で選択します。

   テレメトリ クライアントではサーバーに送信するデータをバッチ処理するため、一覧が表示されるには最大で 5 分かかる場合があります。 (この遅延は、[Live Metrics Stream](../azure-monitor/app/live-stream.md) には適用されません。 このサービスは、ページの読み込み時に Functions ホストに接続するため、ログがページに直接ストリーム配信されます)。

   ![呼び出しリスト](media/functions-monitoring/monitor-tab-ai-invocations.png)

1. 特定の関数呼び出しのログを表示するには、その呼び出しの **[日付]** 列のリンクを選択します。

   ![呼び出しの詳細のリンク](media/functions-monitoring/invocation-details-link-ai.png)

   その呼び出しのログ出力は、新しいページに表示されます。

   ![呼び出しの詳細](media/functions-monitoring/invocation-details-ai.png)

両方のページに、データを取得する Application Insights Analytics クエリへの **[Application Insights で実行する]** リンクがあることがわかります。

![Application Insights で実行する](media/functions-monitoring/run-in-ai.png)

次のクエリが表示されます。 呼び出しの一覧が過去 30 日間に制限されていることがわかります。 この一覧には最大 20 行 (`where timestamp > ago(30d) | take 20`) 表示されます。 呼び出しの詳細の一覧には、過去 30 日間のデータが、無制限で表示されます。

![Application Insights Analytics 呼び出しの一覧](media/functions-monitoring/ai-analytics-invocation-list.png)

詳細については、後述の「[テレメトリをクエリする](#query-telemetry-data)」を参照してください。

## <a name="view-telemetry-in-application-insights"></a>Application Insights でテレメトリを表示する

Azure portal で関数アプリから Application Insights を開くには、関数アプリの **[概要]** ページに移動します。 **[構成済みの機能]** で **[Application Insights]** を選択します。

![関数アプリの [概要] ページで Application Insights を開く](media/functions-monitoring/ai-link.png)

Application Insights の使用方法については、「[Application Insights のドキュメント](https://docs.microsoft.com/azure/application-insights/)」をご覧ください。 このセクションでは、Application Insights でデータを表示する方法の例をいくつか示します。 Application Insights を既に使い慣れている場合は、[テレメトリ データの構成とカスタマイズの方法に関するセクション](#configure-categories-and-log-levels)に直接進んでかまいません。

![[Application Insights の概要] タブ](media/functions-monitoring/metrics-explorer.png)

Application Insights の次の領域は、関数の動作、パフォーマンス、およびエラーを評価するときに役立ちます。

| Tab | 説明 |
| ---- | ----------- |
| **[障害](../azure-monitor/app/asp-net-exceptions.md)** |  関数の失敗やサーバーの例外に基づいてグラフやアラートを作成します。 **[操作名]** は関数名です。 依存関係に関するカスタム テレメトリを実装している場合を除き、依存関係のエラーは表示されません。 |
| **[パフォーマンス](../azure-monitor/app/performance-counters.md)** | パフォーマンスの問題を分析します。 |
| **サーバー** | サーバーごとのリソース使用率とスループットを表示します。 このデータは、関数が原因で基本リソースの処理が遅延している場合のデバッグで役立つことがあります。 サーバーは、**クラウド ロール インスタンス**と呼ばれます。 |
| **[メトリック](../azure-monitor/app/metrics-explorer.md)** | メトリックに基づいたグラフやアラートを作成します。 メトリックには、関数呼び出しの数、実行時間、成功率が含まれます。 |
| **[ライブ メトリック ストリーム](../azure-monitor/app/live-stream.md)** | 作成されたメトリック データをリアルタイムに表示します。 |

## <a name="query-telemetry-data"></a>テレメトリをクエリする

[Application Insights Analytics](../azure-monitor/app/analytics.md) では、データベース内のテーブルの形式ですべてのテレメトリ データにアクセスできます。 Analytics では、データを抽出、操作、視覚化するためのクエリ言語が用意されています。

![Analytics を選択する](media/functions-monitoring/select-analytics.png)

![分析の例](media/functions-monitoring/analytics-traces.png)

以下は、直近 30 分間の worker あたりの要求数の分布を示すクエリの例です。

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

使用可能なテーブルは、左側の **[スキーマ]** タブに表示されます。 次のテーブルで、関数呼び出しによって生成されたデータを確認できます。

| テーブル | 説明 |
| ----- | ----------- |
| **traces** | ランタイムや関数コードによって作成されたログ。 |
| **requests** | 関数呼び出しごとの要求。 |
| **exceptions** | ランタイムによってスローされた例外。 |
| **customMetrics** | 呼び出しの成功数と失敗数、成功率、時間。 |
| **customEvents** | ランタイムによって追跡されたイベント。たとえば、関数をトリガーする HTTP 要求など。 |
| **performanceCounters** | 関数が実行されているサーバーのパフォーマンスに関する情報。 |

その他のテーブルには、可用性テストと、クライアントとブラウザーのテレメトリがあります。 カスタム テレメトリを実装して、テーブルにデータを追加できます。

各テーブルでは、関数固有のデータの一部が `customDimensions` フィールドに保存されます。  たとえば、次のクエリでは、ログ レベルが `Error` のすべてのトレースが取得されます。

```
traces 
| where customDimensions.LogLevel == "Error"
```

ランタイムにより、`customDimensions.LogLevel` フィールドと `customDimensions.Category` フィールドが提供されます。 関数コードで記述したログにフィールドを追加できます。 この記事の後半にある「[構造化ログ](#structured-logging)」をご覧ください。

## <a name="configure-categories-and-log-levels"></a>カテゴリとログ レベルを構成する

Application Insights はカスタム構成なしで使用できます。 既定の構成ではデータ量が多くなる可能性があります。 Visual Studio Azure サブスクリプションを使っている場合、Application Insights のデータ上限に達する可能性があります。 この記事の後半では、関数から Application Insights に送信するデータを構成し、カスタマイズする方法を説明します。 関数アプリの場合、ログは [host.json] ファイルで構成されます。

### <a name="categories"></a>Categories

Azure Functions のロガーでは、すべてのログに*カテゴリ*があります。 カテゴリは、ランタイム コードや関数コードのどの部分にログが記述されているかを示します。 

Functions のランタイムでは、先頭が "Host" のカテゴリを持つログが作成されます。 バージョン 1.x では、`function started`、`function executed`、および `function completed` のログのカテゴリは `Host.Executor` になります。 バージョン 2.x 以降、これらのログのカテゴリは `Function.<YOUR_FUNCTION_NAME>` になります。

関数コードでログを作成する場合、Functions ランタイムのバージョン 1.x でのカテゴリは `Function` になります。 バージョン 2.x では、カテゴリは `Function.<YOUR_FUNCTION_NAME>.User` になります。

### <a name="log-levels"></a>ログ レベル

Azure Functions ロガーでは、すべてのログに*ログ レベル*も含まれています。 [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) は列挙型で、整数コードは次のような相対的な重要度を示します。

|LogLevel    |コード|
|------------|---|
|Trace       | 0 |
|デバッグ       | 1 |
|Information | 2 |
|警告     | 3 |
|Error       | 4 |
|重大    | 5 |
|なし        | 6 |

ログ レベル `None` については、次のセクションで説明します。 

### <a name="log-configuration-in-hostjson"></a>host.json 内のログの構成

[Host.json] ファイルでは、関数アプリから Application Insights に送信するログの量を設定します。 カテゴリごとに、送信する最小のログ レベルを指定します。 2 つの例があり、1 つ目の例は [Functions バージョン 2.x ランタイム](functions-versions.md#version-2x) (.NET Core) を対象としていて、2 つ目の例はバージョン 1.x ランタイム用です。

### <a name="version-2x"></a>バージョン 2.x

v2.x ランタイムでは、[.NET Core のログ記録フィルター階層](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering)が使用されます。 

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host.Results": "Error",
      "Function": "Error",
      "Host.Aggregator": "Trace"
    }
  }
}
```

### <a name="version-1x"></a>バージョン 1.x

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

この例では、次のルールを設定します。

* カテゴリが `Host.Results` または `Function` のログについては、`Error` 以上のレベルのみを Application Insights に送信する。 `Warning` 以下のレベルのログは無視する。
* カテゴリが `Host.Aggregator` のログについては、すべてのログを Application Insights に送信する。 `Trace` ログ レベルは、一部のロガーで `Verbose` と呼ばれるものと同じですが、[host.json] ファイルでは `Trace` を使用します。
* その他すべてのログについては、`Information` 以上のレベルのみを Application Insights に送信する。

[host.json] 内のカテゴリ値により、先頭の値が同一のすべてのカテゴリについてログを制御する。 [host.json] の `Host` は、`Host.General`、`Host.Executor`、`Host.Results` などのログを制御します。

[host.json] に先頭の文字列が同一のカテゴリが複数含まれる場合は、同一の部分がより長いものが最初に一致する。 `Host.Aggregator` 以外のランタイムからのすべてのログを `Error` レベルで記録し、`Host.Aggregator` のログは `Information` レベルで記録するとします。

### <a name="version-2x"></a>バージョン 2.x 

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host": "Error",
      "Function": "Error",
      "Host.Aggregator": "Information"
    }
  }
}
```

### <a name="version-1x"></a>バージョン 1.x 

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

これらすべてのログは `Information` レベルで書き込まれます。 `Warning` 以上でフィルターすると、このデータは表示されなくなります。

### <a name="category-hostaggregator"></a>カテゴリ Host.Aggregator

これらのログには、[構成可能な](#configure-the-aggregator)期間の関数呼び出しの回数と平均回数が記録されます。 既定の期間は、30 秒か 1,000 回のどちらか早い方です。 

ログは、Application Insights の **customMetrics** テーブルで利用できます。 例としては、実行回数、成功率、時間などがあります。

![customMetrics クエリ](media/functions-monitoring/custom-metrics-query.png)

これらすべてのログは `Information` レベルで書き込まれます。 `Warning` 以上でフィルターすると、このデータは表示されなくなります。

### <a name="other-categories"></a>その他のカテゴリ

すでに示されているカテゴリ以外のカテゴリのすべてのログは、Application Insights の **traces** テーブルで利用できます。

![traces クエリ](media/functions-monitoring/analytics-traces.png)

`Host` で始まるカテゴリのすべてのログは、Functions ランタイムによって書き込まれます。 "Function started" と "Function completed" のログのカテゴリは `Host.Executor` となります。 正常に実行された場合、これらのログは `Information` レベルとなります。 例外は `Error` レベルで記録されます。 ランタイムでは、`Warning` レベルのログも作成されます。たとえば、"queue messages sent to the poison queue" などです。

ご使用の関数コードが書き込んだログのカテゴリは `Function` となり、どのログ レベルにもできます。

## <a name="configure-the-aggregator"></a>アグリゲーターを構成する

前のセクションで述べたように、ランタイムでは期間内の関数実行についてデータが集計されます。 既定の期間は、30 秒か 1,000 回実行のどちらか早い方です。 [host.json] ファイル内でこの設定を構成できます。  次に例を示します。

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>サンプリングを構成する

Application Insights には、負荷がピークのときに、完了した実行に関してテレメトリ データが生成されすぎないようにする[サンプリング](../azure-monitor/app/sampling.md)機能があります。 Application Insights では、受信実行の割合が指定されたしきい値を超えると、受信した実行の一部がランダムに無視され始めます。 1 秒あたりの最大実行数の既定の設定は 20 (バージョン 1.x では 5) です。 [host.json] でサンプリングを構成できます。  次に例を示します。

### <a name="version-2x"></a>バージョン 2.x 

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20
      }
    }
  }
}
```

### <a name="version-1x"></a>バージョン 1.x 

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

> [!NOTE]
> [サンプリング](../azure-monitor/app/sampling.md)は、既定で有効になっています。 データがないと思われる場合は、特定の監視シナリオに合わせてサンプリング設定を調整するだけで済みます。

## <a name="write-logs-in-c-functions"></a>C# 関数でログを書き込む

Application Insights で traces として表示されるログを、ご使用の関数コードで書き込むことができます。

### <a name="ilogger"></a>ILogger

関数では、`TraceWriter` パラメーターではなく [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) パラメーターを使用します。 `TraceWriter` を使って作成されたログは Application Insights に送られますが、`ILogger` では[構造化ログ](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)を記録することができます。

`ILogger` オブジェクトで、`Log<level>` [拡張メソッド (ILogger 上)](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) を呼び出して、ログを作成します。 次のコードでは、カテゴリが "Function" の `Information` ログが書き込まれます。

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>構造化ログ

ログ メッセージで使用するパラメーターは、プレースホルダーの名前ではなく順序によって決定されます。 次のコードがあるとします。

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

同じメッセージ文字列を維持しながらパラメーターの順序を逆にすると、結果のメッセージ テキストではそれらの値が誤った場所に配置されます。

プレースホルダーはこのように処理されるため、構造化ログを実行できます。 Application Insights では、パラメーターの名前と値のペア、およびメッセージ文字列を保存します。 そのため、メッセージ引数はクエリ可能なフィールドになります。

前の例のようなロガー メソッド呼び出しでは、フィールド `customDimensions.prop__rowKey` をクエリできます。 `prop__` プレフィックスを追加して、ランタイムが追加したフィールドと、ご使用の関数コードが追加したフィールドとの間に競合が起こらないようにします。

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

### <a name="custom-metrics-logging"></a>カスタム メトリックのログ記録

C# スクリプト関数では、`ILogger` 上の `LogMetric` 拡張メソッドを使用して、Application Insights でのカスタム メトリックを作成できます。 メソッド呼び出しの例を次に示します。

```csharp
logger.LogMetric("TestMetric", 1234);
```

.NET 用 Application Insights API を使用して `TrackMetric` を呼び出す代わりに、このコードを使用できます。

## <a name="write-logs-in-javascript-functions"></a>JavaScript 関数でログを書き込む

Node.js 関数では、`context.log` を使用してログを書き込みます。 構造化ログは使用できません。

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>カスタム メトリックのログ記録

Functions Runtime の[バージョン 1.x](functions-versions.md#creating-1x-apps) で Node.js 関数を実行すると、`context.log.metric` メソッドを使用して、Application Insights でカスタム メトリックを作成できます。 このメソッドは、バージョン 2.x では現在サポートされていません。 メソッド呼び出しの例を次に示します。

```javascript
context.log.metric("TestMetric", 1234);
```

Application Insights 用 Node.js SDK を使用して `trackMetric` を呼び出す代わりに、このコードを使用できます。

## <a name="log-custom-telemetry-in-c-functions"></a>C# 関数でカスタム テレメトリをログに記録する

[Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) NuGet パッケージを使用して、カスタム テレメトリ データを Application Insights に送信できます。 次の C# の例では、[カスタム テレメトリ API](../azure-monitor/app/api-custom-events-metrics.md) を使用します。 この例は .NET クラス ライブラリ用ですが、Application Insights のコードは C# スクリプト用と同じです。

### <a name="version-2x"></a>バージョン 2.x

バージョン 2.x ランタイムでは、テレメトリを現在の操作と自動的に関連付けるために、Application Insights のより新しい機能が使用されます。 `Id`、`ParentId`、または `Name` フィールドを手動で設定する操作は必要ありません。

```cs
using System;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;

namespace functionapp0915
{
    public class HttpTrigger2
    {
        private readonly TelemetryClient telemetryClient;

        /// Using dependency injection will guarantee that you use the same configuration for telemetry collected automatically and manually.
        public HttpTrigger2(TelemetryConfiguration telemetryConfiguration)
        {
            this.telemetryClient = new TelemetryClient(telemetryConfiguration);
        }

        [FunctionName("HttpTrigger2")]
        public Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = null)]
            HttpRequest req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.Query
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Track an Event
            var evt = new EventTelemetry("Function called");
            evt.Context.User.Id = name;
            this.telemetryClient.TrackEvent(evt);

            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            metric.Context.User.Id = name;
            this.telemetryClient.TrackMetric(metric);

            // Track a Dependency
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            dependency.Context.User.Id = name;
            this.telemetryClient.TrackDependency(dependency);

            return Task.FromResult<IActionResult>(new OkResult());
        }
    }
}
```

### <a name="version-1x"></a>バージョン 1.x

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
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

        private static TelemetryClient telemetryClient = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;
         
            // Track an Event
            var evt = new EventTelemetry("Function called");
            UpdateTelemetryContext(evt.Context, context, name);
            telemetryClient.TrackEvent(evt);
            
            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            UpdateTelemetryContext(metric.Context, context, name);
            telemetryClient.TrackMetric(metric);
            
            // Track a Dependency
            var dependency = new DependencyTelemetry
                {
                    Name = "GET api/planets/1/",
                    Target = "swapi.co",
                    Data = "https://swapi.co/api/planets/1/",
                    Timestamp = start,
                    Duration = DateTime.UtcNow - start,
                    Success = true
                };
            UpdateTelemetryContext(dependency.Context, context, name);
            telemetryClient.TrackDependency(dependency);
        }
        
        // Correlate all telemetry with the current Function invocation
        private static void UpdateTelemetryContext(TelemetryContext context, ExecutionContext functionContext, string userName)
        {
            context.Operation.Id = functionContext.InvocationId.ToString();
            context.Operation.ParentId = functionContext.InvocationId.ToString();
            context.Operation.Name = functionContext.FunctionName;
            context.User.Id = userName;
        }
    }    
}
```

関数呼び出しの要求が重複するため、`TrackRequest` や `StartOperation<RequestTelemetry>` を呼び出さないでください。  Functions ランタイムでは、要求が自動的に追跡されます。

`telemetryClient.Context.Operation.Id` は設定しないでください。 このグローバル設定により、多くの関数が同時に実行されていると、正しくない相関関係が発生します。 代わりに、新しいテレメトリ インスタンス (`DependencyTelemetry`、`EventTelemetry`) を作成し、その `Context` プロパティを変更してください。 その後、テレメトリ インスタンスを、`TelemetryClient` (`TrackDependency()`、`TrackEvent()`) の対応する `Track` メソッドに渡します。 このメソッドにより、現在の関数呼び出しについて、テレメトリが必ず正しい相関関係の詳細を持つようになります。

## <a name="log-custom-telemetry-in-javascript-functions"></a>JavaScript 関数でカスタム テレメトリをログに記録する

次は、[Application Insights Node.js SDK](https://github.com/microsoft/applicationinsights-node.js) でカスタム テレメトリを送信するサンプル コード スニペットです。

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

    context.done();
};
```

`tagOverrides` パラメーターにより、関数の呼び出し ID に `operation_Id` を設定します。 この設定により、特定の関数呼び出しについての自動生成されたテレメトリとカスタム テレメトリをすべて関連付けることができます。

## <a name="dependencies"></a>依存関係

Functions v2 は、HTTP 要求、ServiceBus、SQL の依存関係を自動的に収集します。

依存関係を表示するようにカスタム コードを記述することができます。 たとえば、[C# カスタム テレメトリ セクション](#log-custom-telemetry-in-c-functions)にあるサンプル コードを参照してください。 このサンプル コードでは、次のイメージのような Application Insights の*アプリケーション マップ*が作成されます。

![アプリケーション マップ](./media/functions-monitoring/app-map.png)

## <a name="report-issues"></a>レポートに関する問題

Functions での Application Insights 統合に関する問題をレポートしたり、提案や要求を行ったりするには、[GitHub で問題を作成します](https://github.com/Azure/Azure-Functions/issues/new)。

## <a name="streaming-logs"></a>ストリーミング ログ

アプリケーションの開発中、Azure 内での実行時にログに書き込まれている内容がほぼリアルタイムで必要になることがよくあります。

関数実行によって生成されているログ ファイルのストリームを表示する方法は 2 つあります。

* **組み込みのログ ストリーミング**: App Service プラットフォームでは、アプリケーション ログ ファイルのストリームを表示できます。 これは、[ローカル開発](functions-develop-local.md)中に関数をデバッグするときや、ポータル内の **[テスト]** タブを使用するときに見られる出力と同等です。 すべてのログベース情報が表示されます。 詳細については、[Azure App Service の Web アプリの診断ログの有効化](../app-service/troubleshoot-diagnostic-logs.md#streamlogs)に関するページをご覧ください。 このストリーミング方法でサポートされるインスタンスは 1 つだけです。従量課金プランの Linux 上で実行されているアプリでは、この方法を使用できません。

* **Live Metrics Stream**: 関数アプリが [Application Insights に接続されている](#enable-application-insights-integration)場合、[Live Metrics Stream](../azure-monitor/app/live-stream.md) を使用して Azure portal 内でログ データやその他のメトリックをほぼリアルタイムで表示できます。 この方法は、複数のインスタンス上または従量課金プランの Linux 上で実行されている関数を監視する場合に使用します。 このメソッドでは、[サンプリングされたデータ](#configure-sampling)が使用されます。

ログ ストリームは、ポータル内とほとんどのローカル開発環境内の両方で表示できます。 

### <a name="portal"></a>ポータル

ポータル上では、両方の種類のログ ストリームを表示できます。

#### <a name="built-in-log-streaming"></a>組み込みのログ ストリーミング

ポータルでストリーミング ログを表示するには、関数アプリで **[プラットフォーム機能]** タブを選択します。 次に、 **[監視]** の **[ログ ストリーミング]** を選択します。

![ポータルでストリーミング ログを有効にする](./media/functions-monitoring/enable-streaming-logs-portal.png)

これにより、アプリがログ ストリーミング サービスに接続され、ウィンドウにアプリケーション ログが表示されます。 **[アプリケーション ログ]** と **[Web サーバー ログ]** を切り替えることができます。  

![ポータルでストリーミング ログを表示する](./media/functions-monitoring/streaming-logs-window.png)

#### <a name="live-metrics-stream"></a>ライブ メトリック ストリーム

アプリの Live Metrics Stream を表示するには、関数アプリの **[概要]** タブを選択します。 Application Insights 有効にすると、 **[構成済みの機能]** の下に **[Application Insights]** リンクが表示されます。 このリンクをクリックすると、アプリの Application Insights ページに移動します。

Application Insights で、 **[Live Metrics Stream]** を選択します。 [サンプリングされたログ エントリ](#configure-sampling)が、 **[Sample Telemetry]\(サンプル テレメトリ\)** の下に表示されます。

![ポータル上で Live Metrics Stream を表示する](./media/functions-monitoring/live-metrics-stream.png) 

### <a name="visual-studio-code"></a>Visual Studio Code

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

### <a name="core-tools"></a>Core Tools

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

### <a name="azure-cli"></a>Azure CLI

[Azure CLI](/cli/azure/install-azure-cli) を使用して、ストリーミング ログを有効にできます。 次のコマンドを使用してサインインし、サブスクリプションを選択し、ログ ファイルをストリーミングします。

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

### <a name="azure-powershell"></a>Azure PowerShell

[Azure PowerShell](/powershell/azure/overview) を使用して、ストリーミング ログを有効にすることができます。 PowerShell の場合は、次のコマンドを使用して Azure アカウントを追加し、サブスクリプションを選択して、ログ ファイルをストリーミングします。

```powershell
Add-AzAccount
Get-AzSubscription
Get-AzSubscription -SubscriptionName "<subscription name>" | Select-AzSubscription
Get-AzWebSiteLog -Name <FUNCTION_APP_NAME> -Tail
```

## <a name="disable-built-in-logging"></a>組み込みログを無効にする

Application Insights を有効にする場合は、Azure Storage を使用する組み込みログを無効にします。 組み込みログは軽量のワークロードには便利ですが、高負荷の実稼働環境での使用には向きません。 実稼働環境の監視には、Application Insights をお勧めします。 組み込みログを実稼働環境で使用すると、Azure Storage での調整のためにログ レコードが不完全になる場合があります。

組み込みログを無効にするには、`AzureWebJobsDashboard` アプリ設定を削除します。 Azure Portal でアプリ設定を削除する方法については、[関数アプリの管理方法](functions-how-to-use-azure-function-app-settings.md#settings)に関するページで「**アプリケーションの設定**」セクションを参照してください。 アプリ設定を削除する前に、同じ関数アプリの既存の関数によって、Azure Storage のトリガーまたはバインドにその設定が使用されていないことを確認してください。

## <a name="next-steps"></a>次の手順

詳細については、次のリソースを参照してください。

* [Application Insights](/azure/application-insights/)
* [ASP.NET Core のログ記録](/aspnet/core/fundamentals/logging/)

[host.json]: functions-host-json.md
