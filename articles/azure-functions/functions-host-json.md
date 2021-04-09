---
title: Azure Functions 2.x の host.json のリファレンス
description: Azure Functions の v2 ランタイムの host.json ファイルのリファレンス ドキュメント。
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: cbedf2212c52d8f1996d3cce0d96d494313ea525
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102608820"
---
# <a name="hostjson-reference-for-azure-functions-2x-and-later"></a>Azure Functions 2.x 以降の host.json のリファレンス 

> [!div class="op_single_selector" title1="使用している Azure Functions ランタイムのバージョンを選択します。 "]
> * [Version 1](functions-host-json-v1.md)
> * [バージョン 2 以降](functions-host-json.md)

*host.json* メタデータ ファイルには、関数アプリのすべての関数に影響するグローバル構成オプションが含まれています。 この記事では、Azure Functions ランタイムのバージョン 2.x 移行で使用できる設定を一覧表示しています。  

> [!NOTE]
> この記事は、Azure Functions 2.x 以降のバージョンを対象としています。  Functions 1.x の host.json のリファレンスについては、「[host.json reference for Azure Functions 1.x (Azure Functions 1.x の host.json のリファレンス)](functions-host-json-v1.md)」を参照してください。

その他の関数アプリの構成オプションは、[アプリ設定](functions-app-settings.md) (デプロイされているアプリの場合) または [local.settings.json](functions-run-local.md#local-settings-file) ファイル (ローカル開発の場合) で管理されます。

バインドに関連する host.json 内の構成は、関数アプリの各関数に均等に適用されます。 

また、アプリケーション設定を使用して、[環境ごとに設定をオーバーライドまたは適用する](#override-hostjson-values)こともできます。

## <a name="sample-hostjson-file"></a>サンプル host.json ファイル

バージョン 2.x 以降用の次のサンプルの *host.json* ファイルには、使用可能なすべてのオプションが指定されています (内部でのみ使用するオプションは除く)。

```json
{
    "version": "2.0",
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "extensions": {
        "blobs": {},
        "cosmosDb": {},
        "durableTask": {},
        "eventHubs": {},
        "http": {},
        "queues": {},
        "sendGrid": {},
        "serviceBus": {}
    },
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
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
    "logging": {
        "fileLoggingMode": "debugOnly",
        "logLevel": {
          "Function.MyFunction": "Information",
          "default": "None"
        },
        "applicationInsights": {
            "samplingSettings": {
              "isEnabled": true,
              "maxTelemetryItemsPerSecond" : 20,
              "evaluationInterval": "01:00:00",
              "initialSamplingPercentage": 100.0, 
              "samplingPercentageIncreaseTimeout" : "00:00:01",
              "samplingPercentageDecreaseTimeout" : "00:00:01",
              "minSamplingPercentage": 0.1,
              "maxSamplingPercentage": 100.0,
              "movingAverageRatio": 1.0,
              "excludedTypes" : "Dependency;Event",
              "includedTypes" : "PageView;Trace"
            },
            "enableLiveMetrics": true,
            "enableDependencyTracking": true,
            "enablePerformanceCountersCollection": true,            
            "httpAutoCollectionOptions": {
                "enableHttpTriggerExtendedInfoCollection": true,
                "enableW3CDistributedTracing": true,
                "enableResponseHeaderInjection": true
            },
            "snapshotConfiguration": {
                "agentEndpoint": null,
                "captureSnapshotMemoryWeight": 0.5,
                "failedRequestLimit": 3,
                "handleUntrackedExceptions": true,
                "isEnabled": true,
                "isEnabledInDeveloperMode": false,
                "isEnabledWhenProfiling": true,
                "isExceptionSnappointsEnabled": false,
                "isLowPrioritySnapshotUploader": true,
                "maximumCollectionPlanSize": 50,
                "maximumSnapshotsRequired": 3,
                "problemCounterResetInterval": "24:00:00",
                "provideAnonymousTelemetry": true,
                "reconnectInterval": "00:15:00",
                "shadowCopyFolder": null,
                "shareUploaderProcess": true,
                "snapshotInLowPriorityThread": true,
                "snapshotsPerDayLimit": 30,
                "snapshotsPerTenMinutesLimit": 1,
                "tempFolder": null,
                "thresholdForSnapshotting": 1,
                "uploaderProxy": null
            }
        }
    },
    "managedDependency": {
        "enabled": true
    },
    "retry": {
      "strategy": "fixedDelay",
      "maxRetryCount": 5,
      "delayInterval": "00:00:05"
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "watchDirectories": [ "Shared", "Test" ],
    "watchFiles": [ "myFile.txt" ]
}
```

この記事の次のセクションでは、最上位レベルの各プロパティについて説明します。 特記がない場合は、いずれも省略可能です。

## <a name="aggregator"></a>aggregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

この設定は [logging](#logging) の子です。

[サンプリング オプション](./configure-monitoring.md#configure-sampling)など、Application Insights のオプションを制御します。

完全な JSON 構造については、前の [サンプル host.json ファイル](#sample-hostjson-file) を参照してください。

> [!NOTE]
> ログ サンプリングが原因で、一部の実行が Application Insights の [モニター] ブレードに表示されない場合があります。 ログ サンプリングを回避するには、`excludedTypes: "Request"` を `samplingSettings` 値に追加します。

| プロパティ | Default | 説明 |
| --------- | --------- | --------- | 
| samplingSettings | 該当なし | 「[applicationInsights.samplingSettings](#applicationinsightssamplingsettings)」を参照してください。 |
| enableLiveMetrics | true | ライブ メトリックの収集を有効にします。 |
| enableDependencyTracking | true | 依存関係の追跡を有効にします。 |
| enablePerformanceCountersCollection | true | Kudu パフォーマンス カウンターの収集を有効にします。 |
| liveMetricsInitializationDelay | 00:00:15 | 内部使用専用です。 |
| httpAutoCollectionOptions | 該当なし | 「[applicationInsights.httpAutoCollectionOptions](#applicationinsightshttpautocollectionoptions)」を参照してください。 |
| snapshotConfiguration | 該当なし | 「[applicationInsights.snapshotConfiguration](#applicationinsightssnapshotconfiguration)」を参照してください。 |

### <a name="applicationinsightssamplingsettings"></a>applicationInsights.samplingSettings

これらの設定の詳細については、「[Application Insights におけるサンプリング](../azure-monitor/app/sampling.md)」を参照してください。 

|プロパティ | Default | 説明 |
| --------- | --------- | --------- | 
| isEnabled | true | サンプリングを有効または無効にします。 | 
| maxTelemetryItemsPerSecond | 20 | 各サーバー ホストで 1 秒あたりにログに記録されるテレメトリ項目の目標数。 アプリを多数のホストで実行する場合、トラフィックの全体的なターゲット レート内に収まるように、この値を削減します。 | 
| evaluationInterval | 01:00:00 | テレメトリの現在のレートを再評価する間隔。 評価は移動平均として実行されます。 急変しやすいテレメトリの場合は、この間隔を短くすることもできます。 |
| initialSamplingPercentage| 100.0 | サンプリング率が動的に変化するサンプリング プロセスの開始時に適用される初期サンプリング率。 デバッグ中はこの値を減らさないでください。 |
| samplingPercentageIncreaseTimeout | 00:00:01 | サンプリング率が変化する場合、このプロパティにより、変化してからどのくらいの時間が経過すると、Application Insights でサンプリング率を上げてキャプチャ データ量を増やすことができるようになるかが決まります。 |
| samplingPercentageDecreaseTimeout | 00:00:01 | サンプリング率が変化する場合、このプロパティにより、変化してからどのくらいの時間が経過すると、Application Insights でサンプリング率を下げてキャプチャ データ量を減らすことができるようになるかが決まります。 |
| minSamplingPercentage | 0.1 | サンプリング率がさまざまであるため、このプロパティにより、許容される最小サンプリング率が決定されます。 |
| maxSamplingPercentage | 100.0 | サンプリング率がさまざまであるため、このプロパティにより、許容される最大サンプリング率が決定されます。 |
| movingAverageRatio | 1.0 | 移動平均の計算で最新値に割り当てられる重み。 1 以下の値を使用します。 小さい値にすると、急変に対する反応が低いアルゴリズムになります。 |
| excludedTypes | null | サンプリングしない型をセミコロンで区切ったリスト。 認識される種類は、`Dependency`、`Event`、`Exception`、`PageView`、`Request`、`Trace` です。 指定された型のすべてのインスタンスが転送されます。指定されていない型はサンプリングされます。 |
| includedTypes | null | サンプリングする型をセミコロンで区切ったリスト。空のリストはすべての型を意味します。 `excludedTypes` にリストされた型は、ここにリストされた型をオーバーライドします。 認識される種類は、`Dependency`、`Event`、`Exception`、`PageView`、`Request`、`Trace` です。 指定された型のインスタンスがサンプリングされます。明示的にも暗黙的にも指定されていない型はサンプリングなしで転送されます。 |

### <a name="applicationinsightshttpautocollectionoptions"></a>applicationInsights.httpAutoCollectionOptions

|プロパティ | Default | 説明 |
| --------- | --------- | --------- | 
| enableHttpTriggerExtendedInfoCollection | true | HTTP トリガーの拡張 HTTP 要求情報 (受信要求の関連付けヘッダー、複数のインストルメンテーション キーのサポート、HTTP メソッド、パス、応答) を有効または無効にします。 |
| enableW3CDistributedTracing | true | W3C 分散トレース プロトコルのサポートを有効または無効にします (さらに、レガシの相関スキーマをオンにします)。 `enableHttpTriggerExtendedInfoCollection` が true の場合、既定で有効になります。 `enableHttpTriggerExtendedInfoCollection` が false の場合、このフラグは、送信要求にのみ適用され、受信要求には適用されません。 |
| enableResponseHeaderInjection | true | 複数のコンポーネントの関連付けヘッダーの応答への挿入を有効または無効にします。 挿入を有効にすると、複数のインストルメンテーション キーを使用する場合に、Application Insights でアプリケーション マップを作成できます。 `enableHttpTriggerExtendedInfoCollection` が true の場合、既定で有効になります。 `enableHttpTriggerExtendedInfoCollection` が false の場合、この設定は適用されません。 |

### <a name="applicationinsightssnapshotconfiguration"></a>applicationInsights.snapshotConfiguration

スナップショットの詳細については、「[.NET アプリでの例外でのデバッグ スナップショット](../azure-monitor/app/snapshot-debugger.md)」および「[Application Insights Snapshot Debugger の有効化やスナップショットの表示に関する問題のトラブルシューティング](../azure-monitor/app/snapshot-debugger-troubleshoot.md)」を参照してください。

|プロパティ | Default | 説明 |
| --------- | --------- | --------- | 
| agentEndpoint | null | Application Insights スナップショット デバッガー サービスに接続するために使用されるエンドポイント。 null の場合、既定のエンドポイントが使用されます。 |
| captureSnapshotMemoryWeight | 0.5 | スナップショットを取得するのに十分なメモリがあるかどうかを確認するときに、現在のプロセス メモリのサイズに割り当てられる重み。 予期される値は、0 より大きい真分数 (0 < CaptureSnapshotMemoryWeight < 1) です。 |
| failedRequestLimit | 3 | テレメトリ プロセッサが無効になるまでに、スナップショットを要求する要求が失敗する回数の制限。|
| handleUntrackedExceptions | true | Application Insights テレメトリで追跡されない例外の追跡を有効または無効にします。 |
| isEnabled | true | スナップショットの収集を有効または無効にします。 | 
| isEnabledInDeveloperMode | false | 開発モードでのスナップショットの収集を有効または無効にします。 |
| isEnabledWhenProfiling | true | Application Insights Profiler で詳細なプロファイル セッションを収集している場合でも、スナップショットの作成を有効または無効にします。 |
| isExceptionSnappointsEnabled | false | 例外のフィルター処理を有効または無効にします。 |
| isLowPrioritySnapshotUploader | true | SnapshotUploader プロセスを通常の優先順位以下で実行するかどうかを決定します。 |
| maximumCollectionPlanSize | 50 | 任意の時点で追跡可能な問題の最大個数 (1 から 9999 の範囲内)。 |
| maximumSnapshotsRequired | 3 | 単一の問題について収集されるスナップショットの最大数 (1 から 999 の範囲内)。 問題は、アプリケーション内の個別の throw ステートメントと見なすことができます。 問題について収集されるスナップショット数がこの値に達すると、問題カウンターがリセットされるまで (`problemCounterResetInterval` を参照)、および `thresholdForSnapshotting` 制限に再度達するまで、その問題のスナップショットは収集されなくなります。 |
| problemCounterResetInterval | 24:00:00 | 問題カウンターをリセットする頻度 (1 分から 7 日の範囲内)。 この間隔に達すると、すべての問題カウントが 0 にリセットされます。 既存の問題について、スナップショットを実行するためのしきい値に達していても、`maximumSnapshotsRequired` で指定された数のスナップショットが生成されていない場合は、アクティブのままです。 |
| provideAnonymousTelemetry | true | 使用状況とエラー テレメトリを匿名で Microsoft に送信するかどうかを決定します。 スナップショット デバッガーに関する問題のトラブルシューティングのサポートを Microsoft に求める場合に、このテレメトリを使用できます。 また、使用状況パターンの監視にも使用されます。 |
| reconnectInterval | 00:15:00 | スナップショット デバッガー エンドポイントに再接続する頻度。 指定可能な範囲は、1 分から 1 日です。 |
| shadowCopyFolder | null | シャドウ コピー バイナリに使用するフォルダーを指定します。 設定しない場合、次の環境変数で指定されたフォルダーがこの順で試行されます: Fabric_Folder_App_Temp、LOCALAPPDATA、APPDATA、TEMP。 |
| shareUploaderProcess | true | true の場合、SnapshotUploader の 1 つのインスタンスだけで、InstrumentationKey を共有する複数のアプリのスナップショットが収集され、アップロードされます。 false に設定すると、SnapshotUploader は、各 (ProcessName, InstrumentationKey) タプルごとに固有になります。 |
| snapshotInLowPriorityThread | true | スナップショットを、IO 優先度の低いスレッドで処理するかどうかを決定します。 スナップショットの作成は高速操作ですが、スナップショットをスナップショット デバッガー サービスにアップロードするには、最初にスナップショットをミニダンプとしてディスクに書き込む必要があります。 これは、SnapshotUploader プロセスで発生します。 この値を true に設定すると、低優先度の IO を使用してミニダンプが書き込まれ、アプリケーションとのリソースの競合は発生しません。 この値を false に設定すると、ミニダンプの作成速度は速くなりますが、アプリケーションの速度は低下します。 |
| snapshotsPerDayLimit | 30 | 1 日 (24 時間内) に許容されるスナップショットの最大数。 この制限は、Application Insights サービス側にも適用されます。 アップロードの頻度は、アプリケーション (つまり、インストルメンテーション キー) ごとに、1 日あたり 50 回に制限されます。 この値は、最終的にアップロード時に拒否される追加のスナップショットが作成されるのを防ぐのに役立ちます。 値を 0 に設定すると、制限が完全に取り除かれます。これは推奨されません。 |
| snapshotsPerTenMinutesLimit | 1 | 10 分間に許容されるスナップショットの最大数。 この値に上限はありませんが、この値はアプリケーションのパフォーマンスに影響を与える可能性があるため、運用ワークロードでこの値を増加する場合は注意する必要があります。 スナップショットの作成は高速ですが、スナップショットのミニダンプの作成とスナップショット デバッガーへのアップロードの操作は非常に遅くなり、アプリケーションとのリソース (CPU と I/O の両方) の競合が発生します。 |
| tempFolder | null | ミニダンプとアップローダー ログ ファイルを書き込むフォルダーを指定します。 設定しない場合、 *%TEMP%\Dumps* が使用されます。 |
| thresholdForSnapshotting | 1 | Application Insights により、スナップショットが要求される前に確認される必要がある例外の回数。 |
| uploaderProxy | null | Snapshot Uploader プロセスで使用されるプロキシ サーバーをオーバーライドします。 アプリケーションがプロキシ サーバーを経由してインターネットに接続する場合、この設定を使用することが必要になる場合があります。 Snapshot Collector はアプリケーションのプロセス内で実行され、同じプロキシ設定を使用します。 しかし、Snapshot Uploader は個別のプロセスとして実行され、プロキシ サーバーを手動で構成することが必要になる場合があります。 この値が null の場合、Snapshot Collector により、System.Net.WebRequest.DefaultWebProxy が調べられ、値が Snapshot Uploader に渡されて、プロキシのアドレスの自動検出が試みられます。 この値が null 以外の場合、Snapshot Uploader では、自動検出は使用されず、ここで指定されたプロキシ サーバーが使用されます。 |

## <a name="blobs"></a>BLOB

構成設定は、[Storage BLOB のトリガーとバインディング](functions-bindings-storage-blob.md#hostjson-settings)に関する記事に記載されています。  

## <a name="cosmosdb"></a>cosmosDb

構成設定は、[Cosmos DB のトリガーとバインディング](functions-bindings-cosmosdb-v2-output.md#host-json)に関する記事に記載されています。

## <a name="customhandler"></a>customHandler

カスタム ハンドラーの構成設定。 詳細については、「[Azure Functions のカスタム ハンドラー](functions-custom-handlers.md#configuration)」を参照してください。

```json
"customHandler": {
  "description": {
    "defaultExecutablePath": "server",
    "workingDirectory": "handler",
    "arguments": [ "--port", "%FUNCTIONS_CUSTOMHANDLER_PORT%" ]
  },
  "enableForwardingHttpRequest": false
}
```

|プロパティ | Default | 説明 |
| --------- | --------- | --------- |
| defaultExecutablePath | N/A | カスタム ハンドラー プロセスとして起動する実行可能ファイル。 カスタム ハンドラーを使用する場合は必須の設定であり、その値は関数アプリのルートを基準とします。 |
| workingDirectory | *関数アプリのルート* | カスタム ハンドラー プロセスを開始する作業ディレクトリ。 これはオプションの設定であり、その値は関数アプリのルートを基準とします。 |
| 引数 | N/A | カスタム ハンドラー プロセスに渡すコマンド ライン引数の配列。 |
| enableForwardingHttpRequest | false | 設定した場合、HTTP トリガーと HTTP 出力だけで構成されているすべての関数には、カスタム ハンドラーの[要求ペイロード](functions-custom-handlers.md#request-payload)ではなく、元の HTTP 要求が転送されます。 |

## <a name="durabletask"></a>durableTask

構成設定は、[Durable Functions のバインディング](durable/durable-functions-bindings.md#host-json)に関する記事に記載されています。

## <a name="eventhub"></a>eventHub

構成設定は、[Event Hub のトリガーとバインディング](functions-bindings-event-hubs.md#host-json)に関する記事に記載されています。 

## <a name="extensions"></a>拡張機能

バインド固有の設定 ([http](#http) や [eventHub](#eventhub) など) をすべて含むオブジェクトを返すプロパティ。

## <a name="extensionbundle"></a>extensionBundle 

拡張機能バンドルを使用すると、互換性のある一連の関数バインド拡張機能を関数アプリに追加できます。 詳細については、「[ローカル開発用の拡張機能バンドル](functions-bindings-register.md#extension-bundles)」を参照してください。

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="functions"></a>functions

ジョブのホストが実行される関数の一覧。 空の配列は、すべての関数を実行することを示します。 [ローカルで実行する](functions-run-local.md)場合にのみ使用します。 Azure の関数アプリでは、この設定を使用する代わりに、「[Azure Functions で関数を無効にする方法](disable-function.md)」の手順に従って、特定の関数を無効にする必要があります。

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

すべての関数のタイムアウト期間を示します。 これは、期間文字列形式に従います。 

| プランの種類 | 既定値 (分) | 最大値 (分) |
| -- | -- | -- |
| 従量課金 | 5 | 10 |
| Premium<sup>1</sup> | 30 | -1 (無制限)<sup>2</sup> |
| 専用 (App Service) | 30 | -1 (無制限)<sup>2</sup> |

<sup>1</sup> Premium プランの実行が保証されるのは 60 分間のみですが、技術的には無制限です。   
<sup>2</sup> `-1` の値は無制限の実行を示しますが、固定の上限を維持することをお勧めします。

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

構成設定は、[HTTP トリガーとバインディング](functions-bindings-http-webhook-output.md#hostjson-settings)に関する記事に記載されています。

## <a name="logging"></a>logging

Application Insights など、関数アプリのログの動作を制御します。

```json
"logging": {
    "fileLoggingMode": "debugOnly",
    "logLevel": {
      "Function.MyFunction": "Information",
      "default": "None"
    },
    "console": {
        ...
    },
    "applicationInsights": {
        ...
    }
}
```

|プロパティ  |Default | 説明 |
|---------|---------|---------|
|fileLoggingMode|debugOnly|どのレベルでファイルのログ記録を有効にするかを定義します。  オプションは、`never`、`always`、`debugOnly` です。 |
|logLevel|該当なし|アプリ内の関数に対するログ カテゴリのフィルター処理を定義するオブジェクト。 この設定により、特定の関数についてログをフィルター処理できます。 詳細については、「[ログ レベルを構成する](configure-monitoring.md#configure-log-levels)」を参照してください。 |
|console|該当なし| [console](#console) ログ記録の設定。 |
|applicationInsights|該当なし| [applicationInsights](#applicationinsights) の設定。 |

## <a name="console"></a>console

この設定は [logging](#logging) の子です。 デバッグ モードでないときのコンソール ログ記録を制御します。

```json
{
    "logging": {
    ...
        "console": {
          "isEnabled": "false"
        },
    ...
    }
}
```

|プロパティ  |Default | 説明 |
|---------|---------|---------| 
|isEnabled|false|コンソール ログ記録を有効または無効にします。| 

## <a name="manageddependency"></a>managedDependency

マネージド依存関係は、現在 PowerShell ベースの関数でのみサポートされている機能です。 この機能を使用すると、サービスによって依存関係を自動的に管理できます。 `enabled` プロパティが `true` に設定されている場合は、`requirements.psd1` ファイルが処理されます。 いずれかのマイナー バージョンがリリースされると、依存関係が更新されます。 詳細については、PowerShell の記事の[マネージド依存関係](functions-reference-powershell.md#dependency-management)に関する記事をご覧ください。

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="queues"></a>queues

構成設定は、[Storage キュー トリガーとバインディング](functions-bindings-storage-queue.md#host-json)に関する記事に記載されています。  

## <a name="retry"></a>retry

アプリ内のすべての実行に対する[再試行ポリシー](./functions-bindings-error-pages.md#retry-policies-preview) オプションを制御します。

```json
{
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 2,
        "delayInterval": "00:00:03"  
    }
}
```

|プロパティ  |Default | 説明 |
|---------|---------|---------| 
|strategy|null|必須。 使用する再試行戦略。 有効な値は `fixedDelay` または `exponentialBackoff`です。|
|maxRetryCount|null|必須。 関数の実行ごとに許可される再試行の最大回数。 `-1` は、無制限に再試行することを意味します。|
|delayInterval|null|`fixedDelay` 戦略で再試行の間に使用される遅延。|
|minimumInterval|null|`exponentialBackoff` 戦略を使用する場合の最小再試行遅延。|
|maximumInterval|null|`exponentialBackoff` 戦略を使用する場合の最大再試行遅延。| 

## <a name="sendgrid"></a>sendGrid

構成設定は、[SendGrid のトリガーとバインディング](functions-bindings-sendgrid.md#host-json)に関する記事に記載されています。

## <a name="servicebus"></a>serviceBus

構成設定は、[Service Bus のトリガーとバインディング](functions-bindings-service-bus-output.md#host-json)に関する記事に記載されています。

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

## <a name="version"></a>version

この値は、host. json のスキーマ バージョンを示します。 v2 ランタイム、またはそれ以降のバージョンを対象とする関数アプリでは、バージョン文字列 `"version": "2.0"` が必要です。 v2 と v3 間での host.json スキーマの変更はありません。

## <a name="watchdirectories"></a>watchDirectories

変更を監視する[共有コード ディレクトリ](functions-reference-csharp.md#watched-directories)のセット。  これらのディレクトリ内のコードを変更した場合に、関数によって変更を選択するようにします。

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="watchfiles"></a>watchFiles

アプリを再起動する必要がある変更について監視されているファイルの 1 つ以上の名前の配列。  これにより、これらのファイル内のコードが変更されたときに、その更新が関数によって取得されることが保証されます。

```json
{
    "watchFiles": [ "myFile.txt" ]
}
```

## <a name="override-hostjson-values"></a>host.json 値をオーバーライドする

host.json ファイル自体を変更せずに、特定の環境用に host.json ファイル内の特定の設定を構成または変更することが必要な場合があります。  特定の host.json 値をオーバーライドするには、等価の値をアプリケーション設定として作成します。 ランタイムは、`AzureFunctionsJobHost__path__to__setting` 形式のアプリケーション設定を検出すると、JSON の `path.to.setting` にある等価の host.json 設定をオーバーライドします。 アプリケーション設定として表現した場合、JSON 階層を示すために使用されるドット (`.`) は 2 つのアンダースコア (`__`) に置き換えられます。 

たとえば、ローカルで実行しているときに Application Insight のサンプリングを無効にするとします。 ローカルの host.json ファイルを変更して Application Insights を無効にした場合、この変更がデプロイ中に運用アプリにプッシュされる可能性があります。 これをより安全に行うには、代わりに `local.settings.json` ファイル内にアプリケーション設定を `"AzureFunctionsJobHost__logging__applicationInsights__samplingSettings__isEnabled":"false"` として作成します。 これは、次の `local.settings.json` ファイルで確認できます。このファイルは発行されません。

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "{storage-account-connection-string}",
        "FUNCTIONS_WORKER_RUNTIME": "{language-runtime}",
        "AzureFunctionsJobHost__logging__applicationInsights__samplingSettings__isEnabled":"false"
    }
}
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [host.json ファイルを更新する方法について説明します](functions-reference.md#fileupdate)。

> [!div class="nextstepaction"]
> [環境変数のグローバル設定を参照してください。](functions-app-settings.md)
