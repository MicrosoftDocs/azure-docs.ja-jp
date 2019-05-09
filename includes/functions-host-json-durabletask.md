---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d79d1bd5ec244ad4399a02c349e2504516d06ccd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58137635"
---
[Durable Functions](../articles/azure-functions/durable-functions-overview.md) の構成設定。

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
    "eventGridPublishEventTypes": ["Started", "Pending", "Failed", "Terminated"]
  }
}
```

タスク ハブの名前は、先頭文字をアルファベットとする必要があります。また、使用できるのはアルファベットと数値だけです。 指定しない場合、関数アプリの既定のタスク ハブ名は **DurableFunctionsHub** です。 詳細については、[タスク ハブ](../articles/azure-functions/durable-functions-task-hubs.md)に関するページをご覧ください。

|プロパティ  |既定値 | 説明 |
|---------|---------|---------|
|hubName|DurableFunctionsHub|代替[タスク ハブ](../articles/azure-functions/durable-functions-task-hubs.md)名を使用すると、複数の Durable Functions アプリケーションが同じストレージ バックエンドを使用している場合でも、これらのアプリケーションを互いに分離できます。|
|controlQueueBatchSize|32|コントロール キューから一度にプルするメッセージの数。|
|partitionCount |4|コントロール キューのパーティション数。 1 から 16 までの正の整数を使用できます。|
|controlQueueVisibilityTimeout |5 分|デキューされたコントロール キュー メッセージの表示タイムアウト。|
|workItemQueueVisibilityTimeout |5 分|デキューされた作業項目キュー メッセージの表示タイムアウト。|
|maxConcurrentActivityFunctions |現在のマシン上のプロセッサ数の 10 倍|1 つのホスト インスタンスで同時に処理できるアクティビティ関数の最大数。|
|maxConcurrentOrchestratorFunctions |現在のマシン上のプロセッサ数の 10 倍|1 つのホスト インスタンスで同時に処理できるオーケストレーター関数の最大数。|
|maxQueuePollingInterval|30 秒|コントロールおよび作業項目キューの最大ポーリング間隔 (*hh:mm:ss* 形式)。 値が高くなるほどメッセージ処理の待機時間が長くなる可能性があります。 値が低くなるほどストレージ コストが高くなる可能性があります。これは、ストレージ トランザクションが増加するからです。|
|azureStorageConnectionStringName |AzureWebJobsStorage|基になる Azure Storage リソースの管理に使用される Azure Storage 接続文字列を含むアプリ設定の名前。|
|azureStorageConnectionStringName||履歴テーブルとインスタンス テーブルに使用する接続文字列の名前。 指定しない場合、`azureStorageConnectionStringName` 接続が使用されます。|
|trackingStoreNamePrefix||`trackingStoreConnectionStringName` が指定されているときに履歴テーブルとインスタンス テーブルに使用されるプレフィックス。 設定されていない場合、既定のプレフィックス値は `DurableTask` になります。 `trackingStoreConnectionStringName` が指定されていない場合、履歴テーブルとインスタンス テーブルは `hubName` 値をプレフィックスとして使用し、`trackingStoreNamePrefix` の設定はすべて無視されます。|
|traceInputsAndOutputs |false|関数呼び出しの入力と出力をトレースするかどうかを示す値。 関数の実行イベントをトレースした場合の既定の動作では、関数呼び出しのシリアル化された入力および出力のバイト数が記録されます。 この動作により、ログが肥大化することも、機密情報が誤って公開されることもなく、入力および出力に関する最小限の情報が示されます。 このプロパティを true に設定すると、既定の関数ログ記録によって、関数の入力および出力の内容全体がログに記録されます。|
|logReplayEvents|false|オーケストレーションの再生イベントを Application Insights に書き込むかどうかを示す値。|
|eventGridTopicEndpoint ||Azure Event Grid カスタム トピック エンドポイントの URL。 このプロパティが設定されている場合は、オーケストレーションのライフ サイクル通知イベントがこのエンドポイントに発行されます。 このプロパティは、アプリ設定の解決をサポートします。|
|eventGridKeySettingName ||`EventGridTopicEndpoint` での Azure Event Grid カスタム トピックによる認証に使用されるキーを含むアプリ設定の名前。|
|eventGridPublishRetryCount|0|Event Grid トピックへの発行が失敗した場合に再試行する回数。|
|eventGridPublishRetryInterval|5 分|Event Grid の発行を再試行する間隔 (*hh:mm:ss* 形式)。|
|eventGridPublishEventTypes||Event Grid に発行するイベントの種類の一覧。 指定されていない場合は、すべてのイベントの種類が発行されます。 指定できる値は、`Started`、`Completed`、`Failed`、`Terminated` です。|

これらの設定の多くはパフォーマンスの最適化を目的としています。 詳細については、[パフォーマンスとスケール](../articles/azure-functions/durable-functions-perf-and-scale.md)に関するページをご覧ください。