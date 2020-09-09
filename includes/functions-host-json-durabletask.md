---
title: インクルード ファイル
description: インクルード ファイル
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 6e253604c57d73c2a89ccfa5cff7efe9e572d11d
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89094348"
---
[Durable Functions](../articles/azure-functions/durable-functions-overview.md) の構成設定。

### <a name="durable-functions-1x"></a>Durable Functions 1.x

```json
{
  "durableTask": {
    "hubName": "MyTaskHub",
    "controlQueueBatchSize": 32,
    "partitionCount": 4,
    "controlQueueVisibilityTimeout": "00:05:00",
    "workItemQueueVisibilityTimeout": "00:05:00",
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "maxQueuePollingInterval": "00:00:30",
    "azureStorageConnectionStringName": "AzureWebJobsStorage",
    "trackingStoreConnectionStringName": "TrackingStorage",
    "trackingStoreNamePrefix": "DurableTask",
    "traceInputsAndOutputs": false,
    "logReplayEvents": false,
    "eventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName":  "EventGridKey",
    "eventGridPublishRetryCount": 3,
    "eventGridPublishRetryInterval": "00:00:30",
    "eventGridPublishEventTypes": ["Started", "Completed", "Failed", "Terminated"]
  }
}
```

### <a name="durable-functions-2x"></a><a name="durable-functions-2-0-host-json"></a>Durable Functions 2.x

```json
{
 "extensions": {
  "durableTask": {
    "hubName": "MyTaskHub",
    "storageProvider": {
      "connectionStringName": "AzureWebJobsStorage",
      "controlQueueBatchSize": 32,
      "controlQueueBufferThreshold": 256,
      "controlQueueVisibilityTimeout": "00:05:00",
      "maxQueuePollingInterval": "00:00:30",
      "partitionCount": 4,
      "trackingStoreConnectionStringName": "TrackingStorage",
      "trackingStoreNamePrefix": "DurableTask",
      "useLegacyPartitionManagement": true,
      "workItemQueueVisibilityTimeout": "00:05:00",
    },
    "tracing": {
      "traceInputsAndOutputs": false,
      "traceReplayEvents": false,
    },
    "notifications": {
      "eventGrid": {
        "topicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
        "keySettingName": "EventGridKey",
        "publishRetryCount": 3,
        "publishRetryInterval": "00:00:30",
        "publishEventTypes": [
          "Started",
          "Pending",
          "Failed",
          "Terminated"
        ]
      }
    },
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "extendedSessionsEnabled": false,
    "extendedSessionIdleTimeoutInSeconds": 30,
    "useAppLease": true,
    "useGracefulShutdown": false
  }
 }
}

```

タスク ハブの名前は、先頭文字をアルファベットとする必要があります。また、使用できるのはアルファベットと数値だけです。 指定しない場合、関数アプリの既定のタスク ハブ名は **DurableFunctionsHub** です。 詳細については、[タスク ハブ](../articles/azure-functions/durable-functions-task-hubs.md)に関するページをご覧ください。

|プロパティ  |Default | 説明 |
|---------|---------|---------|
|hubName|DurableFunctionsHub|代替[タスク ハブ](../articles/azure-functions/durable-functions-task-hubs.md)名を使用すると、複数の Durable Functions アプリケーションが同じストレージ バックエンドを使用している場合でも、これらのアプリケーションを互いに分離できます。|
|controlQueueBatchSize|32|コントロール キューから一度にプルするメッセージの数。|
|controlQueueBufferThreshold|256|一度にメモリにバッファー処理できる制御キュー メッセージの数。その時点で、ディスパッチャーは、追加のメッセージがデキューされるまで待機します。|
|partitionCount |4|コントロール キューのパーティション数。 1 から 16 までの正の整数を使用できます。|
|controlQueueVisibilityTimeout |5 分|デキューされたコントロール キュー メッセージの表示タイムアウト。|
|workItemQueueVisibilityTimeout |5 分|デキューされた作業項目キュー メッセージの表示タイムアウト。|
|maxConcurrentActivityFunctions |現在のマシン上のプロセッサ数の 10 倍|1 つのホスト インスタンスで同時に処理できるアクティビティ関数の最大数。|
|maxConcurrentOrchestratorFunctions |現在のマシン上のプロセッサ数の 10 倍|1 つのホスト インスタンスで同時に処理できるオーケストレーター関数の最大数。|
|maxQueuePollingInterval|30 秒|コントロールおよび作業項目キューの最大ポーリング間隔 (*hh:mm:ss* 形式)。 値が高くなるほどメッセージ処理の待機時間が長くなる可能性があります。 値が低くなるほどストレージ コストが高くなる可能性があります。これは、ストレージ トランザクションが増加するからです。|
|azureStorageConnectionStringName |AzureWebJobsStorage|基になる Azure Storage リソースの管理に使用される Azure Storage 接続文字列を含むアプリ設定の名前。|
|azureStorageConnectionStringName||履歴テーブルとインスタンス テーブルに使用する接続文字列の名前。 指定しない場合は、`connectionStringName` (Durable 2.x) または `azureStorageConnectionStringName` (Durable 1.x) 接続が使用されます。|
|trackingStoreNamePrefix||`trackingStoreConnectionStringName` が指定されているときに履歴テーブルとインスタンス テーブルに使用されるプレフィックス。 設定されていない場合、既定のプレフィックス値は `DurableTask` になります。 `trackingStoreConnectionStringName` が指定されていない場合、履歴テーブルとインスタンス テーブルは `hubName` 値をプレフィックスとして使用し、`trackingStoreNamePrefix` の設定はすべて無視されます。|
|traceInputsAndOutputs |false|関数呼び出しの入力と出力をトレースするかどうかを示す値。 関数の実行イベントをトレースした場合の既定の動作では、関数呼び出しのシリアル化された入力および出力のバイト数が記録されます。 この動作により、ログが肥大化することも、機密情報が誤って公開されることもなく、入力および出力に関する最小限の情報が示されます。 このプロパティを true に設定すると、既定の関数ログ記録によって、関数の入力および出力の内容全体がログに記録されます。|
|logReplayEvents|false|オーケストレーションの再生イベントを Application Insights に書き込むかどうかを示す値。|
|eventGridTopicEndpoint ||Azure Event Grid カスタム トピック エンドポイントの URL。 このプロパティが設定されている場合は、オーケストレーションのライフ サイクル通知イベントがこのエンドポイントに発行されます。 このプロパティは、アプリ設定の解決をサポートします。|
|eventGridKeySettingName ||`EventGridTopicEndpoint` での Azure Event Grid カスタム トピックによる認証に使用されるキーを含むアプリ設定の名前。|
|eventGridPublishRetryCount|0|Event Grid トピックへの発行が失敗した場合に再試行する回数。|
|eventGridPublishRetryInterval|5 分|Event Grid の発行を再試行する間隔 (*hh:mm:ss* 形式)。|
|eventGridPublishEventTypes||Event Grid に発行するイベントの種類の一覧。 指定されていない場合は、すべてのイベントの種類が発行されます。 指定できる値は、`Started`、`Completed`、`Failed`、`Terminated` です。|
|useAppLease|true|`true` に設定すると、アプリはタスク ハブ メッセージを処理する前にアプリレベルの BLOB リースを取得する必要があります。 詳細については、[ディザスター リカバリーと geo ディストリビューション](../articles/azure-functions/durable/durable-functions-disaster-recovery-geo-distribution.md)に関するドキュメントを参照してください。 v2.3.0 以降で利用可能です。
|useLegacyPartitionManagement|true|`false` に設定した場合は、スケールアウト時に関数の実行が重複する可能性を抑えるパーティション管理アルゴリズムを使用します。v2.3.0 以降で利用可能です。 既定値は、将来のリリースで `false` に変更される予定です。|
|useGracefulShutdown|false|(プレビュー) 正常なシャットダウンを有効にして、ホストのシャットダウンでインプロセス関数の実行が失敗する可能性を減らします。|

これらの設定の多くはパフォーマンスの最適化を目的としています。 詳細については、「[パフォーマンスと拡張性](../articles/azure-functions/durable-functions-perf-and-scale.md)」を参照してください。
