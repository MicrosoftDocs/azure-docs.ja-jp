---
title: インクルード ファイル
description: インクルード ファイル
services: service-bus-messaging, event-hubs
author: spelluru
ms.service: service-bus-messaging, event-hubs
ms.topic: include
ms.date: 12/12/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 9bc641d680d927c44814f6814ebf6a6dde958c9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98901038"
---
Azure Functions を使用すると、事前構築済みのエントリ ポイントを使用して、構成のみのレプリケーション タスクを作成できます。 [Azure Functions の構成ベース レプリケーションのサンプル](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config)では、独自のコードで[事前構築済みのヘルパー](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication)を活用したり、コード全体を処理せずに構成を使用したりする方法について説明しています。

## <a name="create-a-replication-task"></a>レプリケーション タスクを作成する
そのようなレプリケーション タスクを作成するには、最初にプロジェクト フォルダーの下に新しいフォルダーを作成します。 新しいフォルダーの名前は、`europeToAsia` や `telemetry` などの関数の名前です。 名前は、使用されているメッセージング エンティティとの直接的な相関関係がなく、それらを識別するためにのみ使用されます。 同じプロジェクトに多数の関数を作成できます。

次に、フォルダー内に `function.json` ファイルを作成します。 このファイルでは、関数を構成します。 次の内容から始めます。

``` JSON
{
    "configurationSource": "config",
    "bindings" : [
        {
            "direction": "in",
            "name": "input" 
        },
        {
            "direction": "out",
            "name": "output"
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": -1,
        "minimumInterval": "00:00:05",
        "maximumInterval": "00:05:00"
    },
    "disabled": false,
    "scriptFile": "../bin/Azure.Messaging.Replication.dll",
    "entryPoint": "Azure.Messaging.Replication.*"
}
```

このファイルでは、接続するエンティティに応じた次の 3 つの構成ステップを実行する必要があります。

1. [入力方向の構成](#configure-the-input-direction)
2. [出力方向の構成](#configure-the-output-direction)
3. [エントリ ポイントの構成](#configure-the-entry-point)

### <a name="configure-the-input-direction"></a>入力方向の構成

#### <a name="event-hub-input"></a>イベント ハブの入力

イベント ハブからイベントを受信する場合は、"bindings" 内の最上位セクションに、以下を設定する構成情報を追加します。

* **type** - "eventHubTrigger" 型。
* **connection** - イベント ハブ接続文字列のアプリ設定値の名前。 
* **eventHubName** - 接続文字列によって識別される名前空間内におけるイベント ハブの名前。
* **consumerGroup** - コンシューマー グループの名前。 名前はパーセント記号で囲まれているので、アプリ設定値も参照していることに注意してください。

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "eventHubTrigger",
            "connection": "functionname-source-connection",
            "eventHubName": "eventHubA",
            "consumerGroup" : "%functionname-source-consumergroup%",
            "name": "input" 
        }
    ...
```

#### <a name="service-bus-queue-input"></a>Service Bus キューの入力

Service Bus キューからイベントを受信する場合は、"bindings" 内の最上位セクションに、以下を設定する構成情報を追加します。

* **type** - "serviceBusTrigger" 型。
* **connection** - Service Bus 接続文字列のアプリ設定値の名前。
* **queueName** - 接続文字列によって識別される名前空間内における Service Bus キューの名前。

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "serviceBusTrigger",
            "connection": "functionname-source-connection",
            "queueName": "queue-a",
            "name": "input" 
        }
    ...
```

#### <a name="service-bus-topic-input"></a>Service Bus トピックの入力

Service Bus トピックからイベントを受信する場合は、"bindings" 内の最上位セクションに、以下を設定する構成情報を追加します。

* **type** - "serviceBusTrigger" 型。
* **connection** - Service Bus 接続文字列のアプリ設定値の名前。 指定されたスクリプトを使用する場合は、この値を `{FunctionName}-source-connection` にする必要があります。
* **topicName** - 接続文字列によって識別される名前空間内における Service Bus トピックの名前。
* **subscriptionName** - 接続文字列によって識別される名前空間内における、指定されたトピックの Service Bus サブスクリプションの名前。

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "serviceBusTrigger",
            "connection": "functionname-source-connection",
            "topicName": "topic-x",
            "subscriptionName" : "sub-y",
            "name": "input" 
        }
    ...
```

### <a name="configure-the-output-direction"></a>出力方向の構成

#### <a name="event-hub-output"></a>イベント ハブの出力

イベント ハブにイベントを転送する場合は、"bindings" 内の最下位セクションに、以下を設定する構成情報を追加します。

* **type** - "eventHub" 型。
* **connection** - イベント ハブ接続文字列のアプリ設定値の名前。 
* **eventHubName** - 接続文字列によって識別される名前空間内におけるイベント ハブの名前。

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "eventHub",
            "connection": "functionname-target-connection",
            "eventHubName": "eventHubB",
            "name": "output" 
        }
    ...
```

#### <a name="service-bus-queue-output"></a>Service Bus キューの出力

Service Bus キューにイベントを転送する場合は、"bindings" 内の最下位セクションに、以下を設定する構成情報を追加します。

* **type** - "serviceBus" 型。
* **connection** - Service Bus 接続文字列のアプリ設定値の名前。 
* **queueName** - 接続文字列によって識別される名前空間内における Service Bus キューの名前。

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "serviceBus",
            "connection": "functionname-target-connection",
            "queueName": "queue-b",
            "name": "output" 
        }
    ...
```

#### <a name="service-bus-topic-output"></a>Service Bus トピックの出力

Service Bus トピックにイベントを転送する場合は、"bindings" 内の最下位セクションに、以下を設定する構成情報を追加します。

* **type** - "serviceBus" 型。
* **connection** - Service Bus 接続文字列のアプリ設定値の名前。 
* **topicName** - 接続文字列によって識別される名前空間内における Service Bus トピックの名前。

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "serviceBus",
            "connection": "functionname-target-connection",
            "topicName": "topic-b",
            "name": "output" 
        }
    ...
```

### <a name="configure-the-entry-point"></a>エントリ ポイントの構成

エントリ ポイントの構成では、標準のレプリケーション タスクのうち 1 つを選択します。 `Azure.Messaging.Replication` プロジェクトを変更する場合は、ここでタスクを追加して参照することもできます。 次に例を示します。

```JSON
    ...
    "scriptFile": "../dotnet/bin/Azure.Messaging.Replication.dll",
    "entryPoint": "Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub"
    ...
```

次の表は、ソースとターゲットの組み合わせに対する正しい値を示しています。

| source      | 移行先      | エントリ ポイント 
|-------------|-------------|------------------------------------------------------------------------
| イベント ハブ   | イベント ハブ   | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub`
| イベント ハブ   | Service Bus | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToServiceBus`
| Service Bus | イベント ハブ   | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub`
| Service Bus | Service Bus | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToServiceBus`

### <a name="retry-policy"></a>再試行ポリシー

再試行ポリシーを構成するには、[再試行に関する Azure Functions ドキュメント](../articles/azure-functions/functions-bindings-error-pages.md)を参照してください。 このリポジトリ内のプロジェクト全体で選択されたポリシー設定によって、データ損失を回避するために、5 秒から 5 分の再試行間隔で再試行回数が無限であるエクスポネンシャル バックオフ戦略が構成されます。

Service Bus については、「[トリガーの回復性に加えて再試行サポートを使用する](../articles/azure-functions/functions-bindings-error-pages.md#using-retry-support-on-top-of-trigger-resilience)」セクションを参照して、トリガーの相互作用と、キューに対して定義されている最大配信回数を理解してください。

### <a name="build-deploy-and-configure"></a>構築、デプロイ、および構成

構成に焦点を当てる場合でも、タスクではデプロイ可能なアプリケーションを構築し、特定のエンドポイントに接続するために必要なすべての情報が含まれるように Azure Functions ホストを構成する必要があります。 

これについては、再利用可能なスクリプトと共に、[Azure Functions の構成ベース レプリケーションのサンプル](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config)で説明されています。