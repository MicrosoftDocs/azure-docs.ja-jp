---
title: Azure Functions 1.x の host.json のリファレンス
description: Azure Functions の v1 ランタイムの host.json ファイルのリファレンス ドキュメント。
ms.topic: conceptual
ms.date: 10/19/2018
ms.openlocfilehash: 48dba50b384731befdc7fba7c418e542994cedd9
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102608956"
---
# <a name="hostjson-reference-for-azure-functions-1x"></a>Azure Functions 1.x の host.json のリファレンス

> [!div class="op_single_selector" title1="使用している Azure Functions ランタイムのバージョンを選択します。 "]
> * [Version 1](functions-host-json-v1.md)
> * [Version 2](functions-host-json.md)

*host.json* メタデータ ファイルには、関数アプリのすべての関数に影響するグローバル構成オプションが含まれています。 この記事では、v1 ランタイムで使用できる設定の一覧を紹介します。 JSON スキーマは、 http://json.schemastore.org/host にあります。

> [!NOTE]
> この記事は、Azure Functions 1.x を対象としています。  Functions 2.x 以降の host.json のリファレンスについては、[Azure Functions 2.x の host.json のリファレンス](functions-host-json.md)に関する記事を参照してください。

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
    "documentDB": {
        "connectionMode": "Gateway",
        "protocol": "Https",
        "leaseOptions": {
            "leasePrefix": "prefix"
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
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
    },
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00",
      "autoComplete": true
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

## <a name="documentdb"></a>DocumentDB

[Azure Cosmos DB のトリガーとバインド](functions-bindings-cosmosdb.md)の構成設定。

```json
{
    "documentDB": {
        "connectionMode": "Gateway",
        "protocol": "Https",
        "leaseOptions": {
            "leasePrefix": "prefix1"
        }
    }
}
```

|プロパティ  |Default | 説明 |
|---------|---------|---------|
|GatewayMode|Gateway|Azure Cosmos DB サービスに接続する際に関数で使用される接続モード。 オプションは `Direct` と `Gateway` です|
|Protocol|Https|Azure Cosmos DB サービスに接続する際に関数で使用される接続プロトコル。  両方のモードの説明については[こちら](../cosmos-db/performance-tips.md#networking)を参照してください|
|leasePrefix|該当なし|アプリ内のすべての関数で使用するプレフィックスをリースします。|

## <a name="durabletask"></a>durableTask

[!INCLUDE [durabletask](../../includes/functions-host-json-durabletask.md)]

## <a name="eventhub"></a>eventHub

[Event Hub トリガーとバインディング](functions-bindings-event-hubs.md#functions-1x)の構成設定。

## <a name="functions"></a>functions

ジョブのホストが実行される関数の一覧。 空の配列は、すべての関数を実行することを示します。 [ローカルで実行する](functions-run-local.md)場合にのみ使用します。 Azure の関数アプリでは、この設定を使用する代わりに、「[Azure Functions で関数を無効にする方法](disable-function.md)」の手順に従って、特定の関数を無効にする必要があります。

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

すべての関数のタイムアウト期間を示します。 サーバーレス従量課金プランの有効な範囲は 1 秒から 10 分であり、既定値は 5 分です。 App Service プランに全体的な制限はありません。既定は _null_ です (タイムアウトなしを示します)。

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

|プロパティ  |Default | 説明 |
|---------|---------|---------| 
|enabled|true|機能が有効かどうかを指定します。 | 
|healthCheckInterval|10 秒|定期的なバック グラウンドでの正常性チェックの間隔。 | 
|healthCheckWindow|2 分|`healthCheckThreshold` 設定と組み合わせて使用するスライド時間枠。| 
|healthCheckThreshold|6|正常性チェックの最大失敗回数。この回数を超えると、ホスト リサイクルが開始されます。| 
|counterThreshold|0.80|パフォーマンス カウンターが異常とみなされるしきい値。| 

## <a name="http"></a>http

[http トリガーとバインディング](functions-bindings-http-webhook.md)の構成設定。

```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 200,
        "maxConcurrentRequests": 100,
        "dynamicThrottlesEnabled": true
    }
}
```

|プロパティ  |Default | 説明 |
|---------|---------|---------| 
|dynamicThrottlesEnabled|false|この設定を有効にすると、要求処理パイプラインが、システム パフォーマンス カウンター (接続、スレッド、プロセス、メモリ、CPU など) を定期的にチェックし、カウンターのいずれかが組み込まれた上限閾値 (80%) を超えた場合は、カウンターが正常なレベルに戻るまで要求は 429 "Too Busy" 応答で拒否されます。|
|maxConcurrentRequests|unbounded (`-1`)|並列で実行される HTTP 関数の最大数。 これによりコンカレンシーを制御でき、リソース使用率の管理に役立ちます。 たとえば、多くのシステム リソース (メモリ、CPU、ソケット) を消費する HTTP 関数があった場合、コンカレンシー率が高すぎると問題が発生します。 または、サードパーティのサービスに対して要求を送信する関数があり、その呼び出し速度を制限する必要がある場合です。 このような場合は、調整を適用することができます。|
|maxOutstandingRequests|unbounded (`-1`)|特定の時点で保持される未処理の要求の最大数。 この制限には、キューに格納され、まだ実行が開始されていない要求と、処理中の実行が含まれます。 この制限を超える受信要求は、429 "Too Busy" 応答で拒否されます。 これにより、呼び出し元は時間ベースの再試行戦略を採用でき、要求の最大待機時間の制御にも役立ちます。 この設定は、スクリプト ホストの実行パス内で発生するキューのみを制御します。 ASP.NET 要求キューなどの他のキューは有効なままで、この設定の影響を受けません。|
|routePrefix|api|すべてのルートに適用されるルート プレフィックス。 既定のプレフィックスを削除するには、空の文字列を使用します。 |

## <a name="id"></a>id

ジョブ ホストの一意の ID。 ダッシュを削除した小文字の GUID を指定できます。 ローカルで実行しているときに必要です。 Azure で実行するときは、ID 値を設定しないことをお勧めします。 `id` を省略すると、ID は Azure で自動的に生成されます。 

ストレージ アカウントを複数の関数アプリで共有している場合は、各関数アプリの `id` がそれぞれ異なることを確認してください。 `id` プロパティは省略することができます。または、各関数アプリの `id` を手動でそれぞれ異なる値に設定することもできます。 1 つの関数アプリが複数のインスタンスにスケール アウトする場合、タイマー インスタンスが 1 しか存在しないようにするために、タイマー トリガーではストレージ ロックが使用されます。 2 つの関数アプリが同じ `id` を共有していて、それぞれタイマー トリガーを使用している場合は、1 つのタイマーのみが実行されます。

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>logger

[ILogger](functions-dotnet-class-library.md#ilogger) オブジェクトまたは [context.log](functions-reference-node.md#contextlog-method) によって書き込まれたログのフィルター処理を制御します。

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

|プロパティ  |Default | 説明 |
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

|プロパティ  |Default | 説明 |
|---------|---------|---------| 
|maxPollingInterval|60000|キューのポーリングの最大間隔 (ミリ秒)。| 
|visibilityTimeout|0|メッセージの処理が失敗したときの再試行間隔。| 
|batchSize|16|Functions ランタイムが同時に取得して並列で処理するキュー メッセージの数。 処理中のメッセージの数が `newBatchThreshold` まで減少すると、ランタイムは は別のバッチを取得し、そのメッセージの処理を開始します。 そのため、1 つの関数につき同時に処理されるメッセージの最大数は、`batchSize` に `newBatchThreshold` を加えた値です。 この制限は、キューによってトリガーされる各関数に個別に適用されます。 <br><br>1 つのキューで受信した複数のメッセージの並列実行を回避したい場合は、`batchSize` を 1 に設定します。 ただし、この設定では、関数アプリが単一の仮想マシン (VM) で実行されている限り、コンカレンシーは実現しません。 この関数アプリを複数の VM にスケール アウトすると、各 VM では、キューによってトリガーされる関数ごとに 1 つのインスタンスを実行できます。<br><br>最大の `batchSize` は 32 です。 | 
|maxDequeueCount|5|有害キューに移動する前に、メッセージの処理を試行する回数。| 
|newBatchThreshold|batchSize/2|同時に処理されているメッセージの数がこの数まで減少すると、ランタイムは別のバッチを取得します。| 

## <a name="sendgrid"></a>SendGrid

[SendGrind 出力バインド](functions-bindings-sendgrid.md)の構成設定

```json
{
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
    }
}    
```

|プロパティ  |Default | 説明 |
|---------|---------|---------| 
|from|該当なし|すべての関数の送信者の電子メール アドレス。| 

## <a name="servicebus"></a>serviceBus

[Service Bus トリガーとバインディング](functions-bindings-service-bus.md)の構成設定。

```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00",
      "autoComplete": true
    }
}
```

|プロパティ  |Default | 説明 |
|---------|---------|---------| 
|maxConcurrentCalls|16|メッセージ ポンプが開始する必要があるコールバックの同時呼び出しの最大数 既定では、Functions ランタイムは、複数のメッセージを同時に処理します。 一度に 1 つのキューまたはトピックのメッセージのみを処理するようにランタイムに指示するには、`maxConcurrentCalls` を 1 に設定します。 | 
|prefetchCount|該当なし|基になる MessageReceiver に使用される既定の PrefetchCount。| 
|autoRenewTimeout|00:05:00|メッセージ ロックが自動的に更新される最大間隔。|
|autoComplete|true|true の場合、操作が正常に実行されたときにトリガーがメッセージの処理を自動的に完了します。 false の場合、返す前にメッセージを完了するのは関数の役割です。|

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

|プロパティ  |Default | 説明 |
|---------|---------|---------| 
|lockPeriod|00:00:15|関数レベルのロックの取得期間。 ロックの自動更新。| 
|listenerLockPeriod|00:01:00|リスナーのロックの取得期間。| 
|listenerLockRecoveryPollingInterval|00:01:00|スタートアップ時にリスナーのロックを獲得できなかった場合に、リスナーのロックの回復に使用される時間間隔。| 
|lockAcquisitionTimeout|00:01:00|ランタイムがロックの獲得を試行する最長時間。| 
|lockAcquisitionPollingInterval|該当なし|ロックの獲得の試行間隔。| 

## <a name="tracing"></a>tracing

*バージョン 1.x*

`TraceWriter` オブジェクトを使用して作成するログの構成設定。 詳細については、[C# のログの記録] を参照してください。

```json
{
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    }
}
```

|プロパティ  |Default | 説明 |
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

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [host.json ファイルを更新する方法について説明します](functions-reference.md#fileupdate)。

> [!div class="nextstepaction"]
> [環境変数のグローバル設定を参照してください。](functions-app-settings.md)
