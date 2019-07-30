---
title: Azure Functions 1.x の host.json のリファレンス
description: Azure Functions の v1 ランタイムの host.json ファイルのリファレンス ドキュメント。
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: glenga
ms.openlocfilehash: 44bc5a245d1bcbc8ff53991af4193ef86f7cd704
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "62107076"
---
# <a name="hostjson-reference-for-azure-functions-1x"></a>Azure Functions 1.x の host.json のリファレンス

> [!div class="op_single_selector" title1="使用している Azure Functions ランタイムのバージョンを選択します。 "]
> * [Version 1](functions-host-json-v1.md)
> * [Version 2](functions-host-json.md)

*host.json* メタデータ ファイルには、関数アプリのすべての関数に影響するグローバル構成オプションが含まれています。 この記事では、v1 ランタイムで使用できる設定の一覧を紹介します。 JSON スキーマは、 http://json.schemastore.org/host にあります。

> [!NOTE]
> この記事は、Azure Functions 1.x を対象としています。  Functions 2.x の host.json のリファレンスについては、「[host.json reference for Azure Functions 2.x (Azure Functions 2.x の host.json のリファレンス)](functions-host-json.md)」を参照してください。

関数アプリの他の構成オプションは、[アプリの設定](functions-app-settings.md)で管理されます。

host.json の一部の設定は、[local.settings.json](functions-run-local.md#local-settings-file) ファイルでローカルに実行するときにのみ使用されます。

## <a name="sample-hostjson-file"></a>サンプル host.json ファイル

次のサンプル *host.json* ファイルには、すべての使用できるオプションが指定されています。


```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    },
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    },
    "id": "9f4ea53c5136457d883d685e57164f08",
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    },
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    },
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    },
    "watchDirectories": [ "Shared" ],
}
```

この記事の次のセクションでは、最上位レベルの各プロパティについて説明します。 特記がない場合は、いずれも省略可能です。

## <a name="aggregator"></a>aggregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

[!INCLUDE [applicationInsights](../../includes/functions-host-json-applicationinsights.md)]

## <a name="durabletask"></a>durableTask

[!INCLUDE [durabletask](../../includes/functions-host-json-durabletask.md)]

## <a name="eventhub"></a>eventHub

[Event Hub トリガーとバインディング](functions-bindings-event-hubs.md)の構成設定。

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="functions"></a>functions

ジョブのホストが実行される関数の一覧。 空の配列は、すべての関数を実行することを示します。 [ローカルで実行する](functions-run-local.md)場合にのみ使用します。 Azure の関数アプリでは、この設定を使用する代わりに、「[Azure Functions で関数を無効にする方法](disable-function.md)」の手順に従って、特定の関数を無効にする必要があります。

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

すべての関数のタイムアウト期間を示します。 サーバーレス従量課金プランの有効な範囲は 1 秒から 10 分であり、既定値は 5 分です。 App Service プランでは、全体的な制限はなく、既定値はランタイムのバージョンによって異なります。

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

[ホストの正常性監視](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor)を行うための構成設定です。

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|プロパティ  |既定値 | 説明 |
|---------|---------|---------| 
|enabled|true|機能が有効かどうかを指定します。 | 
|healthCheckInterval|10 秒|定期的なバック グラウンドでの正常性チェックの間隔。 | 
|healthCheckWindow|2 分|`healthCheckThreshold` 設定と組み合わせて使用するスライド時間枠。| 
|healthCheckThreshold|6|正常性チェックの最大失敗回数。この回数を超えると、ホスト リサイクルが開始されます。| 
|counterThreshold|0.80|パフォーマンス カウンターが異常とみなされるしきい値。| 

## <a name="http"></a>http

[http トリガーとバインディング](functions-bindings-http-webhook.md)の構成設定。

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="id"></a>id

*バージョン 1.x のみ。*

ジョブ ホストの一意の ID。 ダッシュを削除した小文字の GUID を指定できます。 ローカルで実行しているときに必要です。 Azure で実行するときは、ID 値を設定しないことをお勧めします。 `id` を省略すると、ID は Azure で自動的に生成されます。 

ストレージ アカウントを複数の関数アプリで共有している場合は、各関数アプリの `id` がそれぞれ異なることを確認してください。 `id` プロパティは省略することができます。あるいは、各関数アプリの `id` を手動でそれぞれ異なる値に設定することもできます。 1 つの関数アプリが複数のインスタンスにスケール アウトする場合、タイマー インスタンスが 1 しか存在しないようにするために、タイマー トリガーではストレージ ロックが使用されます。 2 つの関数アプリが同じ `id` を共有していて、それぞれタイマー トリガーを使用している場合は、1 つのタイマーのみが実行されます。

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>logger

[ILogger object](functions-monitoring.md#write-logs-in-c-functions) から出力されたログまたは [context.log](functions-monitoring.md#write-logs-in-javascript-functions) ログのフィルターを制御します。

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

|プロパティ  |既定値 | 説明 |
|---------|---------|---------| 
|categoryFilter|該当なし|カテゴリ別のフィルターを指定します| 
|defaultLevel|Information|`categoryLevels` 配列に指定されていないカテゴリの場合、このレベル以上のログを Application Insights に送信します。| 
|categoryLevels|該当なし|各カテゴリの Application Insights に送信される最小ログ レベルを指定するカテゴリの配列。 ここで指定されるカテゴリは、同じ値で始まるすべてのカテゴリを制御し、長い値の方が優先されます。 前述のサンプル *host.json* ファイルでは、`Information` レベルの "Host.Aggregator" で始まるすべてのカテゴリ。 `Error` レベルのログである、"Host.Executor" など "Host" で始まるその他すべてのカテゴリ。| 

## <a name="queues"></a>queues

[Storage キュー トリガーとバインディング](functions-bindings-storage-queue.md)の構成設定。

```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|プロパティ  |既定値 | 説明 |
|---------|---------|---------| 
|maxPollingInterval|60000|キューのポーリングの最大間隔 (ミリ秒)。| 
|visibilityTimeout|0|メッセージの処理が失敗したときの再試行間隔。| 
|batchSize|16|Functions ランタイムが同時に取得して並列で処理するキュー メッセージの数。 処理中のメッセージの数が `newBatchThreshold` まで減少すると、ランタイムは は別のバッチを取得し、そのメッセージの処理を開始します。 そのため、1 つの関数につき同時に処理されるメッセージの最大数は、`batchSize` に `newBatchThreshold` を加えた値です。 この制限は、キューによってトリガーされる各関数に個別に適用されます。 <br><br>1 つのキューで受信した複数のメッセージの並列実行を回避したい場合は、`batchSize` を 1 に設定します。 ただし、この設定では、関数アプリが単一の仮想マシン (VM) で実行されている限り、コンカレンシーは実現しません。 この関数アプリを複数の VM にスケール アウトすると、各 VM では、キューによってトリガーされる関数ごとに 1 つのインスタンスを実行できます。<br><br>最大の `batchSize` は 32 です。 | 
|maxDequeueCount|5|有害キューに移動する前に、メッセージの処理を試行する回数。| 
|newBatchThreshold|batchSize/2|同時に処理されているメッセージの数がこの数まで減少すると、ランタイムは別のバッチを取得します。| 

## <a name="servicebus"></a>serviceBus

[Service Bus トリガーとバインディング](functions-bindings-service-bus.md)の構成設定。

```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|プロパティ  |既定値 | 説明 |
|---------|---------|---------| 
|maxConcurrentCalls|16|メッセージ ポンプが開始する必要があるコールバックの同時呼び出しの最大数 既定では、Functions ランタイムは、複数のメッセージを同時に処理します。 一度に 1 つのキューまたはトピックのメッセージのみを処理するようにランタイムに指示するには、`maxConcurrentCalls` を 1 に設定します。 | 
|prefetchCount|該当なし|基になる MessageReceiver に使用される既定の PrefetchCount。| 
|autoRenewTimeout|00:05:00|メッセージ ロックが自動的に更新される最大間隔。| 

## <a name="singleton"></a>singleton

シングルトン ロック動作の構成設定。 詳細については、「[GitHub issue about singleton support](https://github.com/Azure/azure-webjobs-sdk-script/issues/912)」(シングルトンのサポートに関する GitHub の問題) を参照してください。

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|プロパティ  |既定値 | 説明 |
|---------|---------|---------| 
|lockPeriod|00:00:15|関数レベルのロックの取得期間。 ロックの自動更新。| 
|listenerLockPeriod|00:01:00|リスナーのロックの取得期間。| 
|listenerLockRecoveryPollingInterval|00:01:00|スタートアップ時にリスナーのロックを獲得できなかった場合に、リスナーのロックの回復に使用される時間間隔。| 
|lockAcquisitionTimeout|00:01:00|ランタイムがロックの獲得を試行する最長時間。| 
|lockAcquisitionPollingInterval|該当なし|ロックの獲得の試行間隔。| 

## <a name="tracing"></a>tracing

*バージョン 1.x*

`TraceWriter` オブジェクトを使用して作成するログの構成設定。 [C# のログの記録](functions-reference-csharp.md#logging)と [Node.js のログの記録](functions-reference-node.md#writing-trace-output-to-the-console)に関するページを参照してください。

```json
{
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    }
}
```

|プロパティ  |既定値 | 説明 |
|---------|---------|---------| 
|consoleLevel|info|コンソール ログのトレース レベル。 オプションは、`off`、`error`、`warning`、`info`、および `verbose` です。|
|fileLoggingMode|debugOnly|ファイルのログ記録のトレース レベル。 オプションは、`never`、`always`、`debugOnly` です。| 

## <a name="watchdirectories"></a>watchDirectories

変更を監視する[共有コード ディレクトリ](functions-reference-csharp.md#watched-directories)のセット。  これらのディレクトリ内のコードを変更した場合に、関数によって変更を選択するようにします。

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [host.json ファイルを更新する方法について説明します](functions-reference.md#fileupdate)。

> [!div class="nextstepaction"]
> [環境変数のグローバル設定を参照してください。](functions-app-settings.md)
