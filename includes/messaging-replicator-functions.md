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
ms.openlocfilehash: 1ce983ee739a4a124a93c7913f092b23dfec3cbd
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/27/2021
ms.locfileid: "98901039"
---
## <a name="what-is-a-replication-task"></a>レプリケーション タスクとは

ソースからのイベントは、レプリケーション タスクによって受信されて、ターゲットに転送されます。
ほとんどのレプリケーション タスクで、イベントは変更されずに転送され、変更されるとしても、ソースとターゲットのプロトコルが異なる場合のメタデータ構造間のマッピング程度です。 

レプリケーション タスクは一般にステートレスであり、タスクの順次実行または並列実行の間に状態や他の副作用が共有されないことを意味します。 これは、バッチ処理とチェーン化にも当てはまり、どちらもストリームの既存の状態の上に実装できます。 

これにより、レプリケーション タスクは、一般にステートフルであり [Azure Stream Analytics](../articles/stream-analytics/stream-analytics-introduction.md) のような分析フレームワークとサービスのドメインである集計タスクとは異なるものになります。

## <a name="replication-applications-and-tasks-in-azure-functions"></a>Azure Functions でのレプリケーション アプリケーションとタスク

Azure Functions でのレプリケーション タスクは、構成されているソースから 1 つ以上の入力メッセージを取得する[トリガー](../articles/azure-functions/functions-triggers-bindings.md)と、ソースからコピーされたメッセージを構成されているターゲットに転送する[出力バインド](../articles/azure-functions/functions-triggers-bindings.md#binding-direction)を使用して実装されます。 

| トリガー  | 出力 |
|----------|--------|
| [Azure Event Hubs トリガー](../articles/azure-functions/functions-bindings-event-hubs-trigger.md?tabs=csharp) | [Azure Event Hubs 出力バインド](../articles/azure-functions/functions-bindings-event-hubs-output.md?tabs=csharp) |
| [Azure Service Bus トリガー](../articles/azure-functions/functions-bindings-service-bus-trigger.md?tabs=csharp) | [Azure Service Bus 出力バインド](../articles/azure-functions/functions-bindings-service-bus-output.md?tabs=csharp)
| [Azure IoT Hub トリガー](../articles/azure-functions/functions-bindings-event-iot-trigger.md?tabs=csharp) | [Azure IoT Hub 出力バインド](../articles/azure-functions/functions-bindings-event-iot-output.md?tabs=csharp)
| [Azure Event Grid トリガー](../articles/azure-functions/functions-bindings-event-grid-trigger.md?tabs=csharp) | [Azure Event Grid 出力バインド](../articles/azure-functions/functions-bindings-event-grid-output.md?tabs=csharp)
| [Azure Queue Storage トリガー](../articles/azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp) | [Azure Queue Storage 出力バインド](../articles/azure-functions/functions-bindings-storage-queue-output.md?tabs=csharp)
| [Apache Kafka トリガー](https://github.com/azure/azure-functions-kafka-extension) | [Apache Kafka 出力バインド](https://github.com/azure/azure-functions-kafka-extension)
| [RabbitMQ トリガー](https://github.com/azure/azure-functions-rabbitmq-extension) | [RabbitMQ 出力バインド](https://github.com/azure/azure-functions-rabbitmq-extension) 
| | [Azure Notification Hubs 出力バインド](../articles/azure-functions/functions-bindings-notification-hubs.md)
||[Azure SignalR Service 出力バインド](../articles/azure-functions/functions-bindings-signalr-service-output.md?tabs=csharp)
||[Twilio SendGrid 出力バインド](../articles/azure-functions/functions-bindings-sendgrid.md?tabs=csharp)

レプリケーション タスクは、他の Azure Functions アプリケーションと同じデプロイ方法を使用して、レプリケーションアプリケーションにデプロイされます。 同じアプリケーションに複数のタスクを構成できます。 

Azure Functions Premium を使用すると、複数のレプリケーション アプリケーションで基になる同じリソース プールを共有できます。これは App Service プランと呼ばれます。 つまり、たとえば、.NET で記述されたレプリケーション タスクを、Java で記述されたレプリケーション タスクと、簡単に併置することができます。 これは、Java でのみ使用できる Apache Camel などの特定のライブラリを利用する必要がある場合で、他のレプリケーション タスクには異なる言語とランタイムを使用するのが一般的には好ましくても、特定の統合パスにはこれが最適なオプションである場合に、重要になります。 

可能な場合は常に、イベントやメッセージを個別に配信するトリガーよりバッチ指向のトリガーを優先する必要があり、Azure Functions の[パラメーター バインド式](../articles/azure-functions/functions-bindings-expressions-patterns.md)に依存するのではなく、イベントまたはメッセージの完全な構造を常に取得する必要があります。

関数には、接続しているソースとターゲットのペアを反映した名前を付ける必要があり、アプリケーション構成ファイル内の接続文字列または他の構成要素への参照を、その名前の前に付ける必要があります。 

### <a name="data-and-metadata-mapping"></a>データとメタデータのマッピング

入力トリガーと出力バインドのペアを決定した後は、トリガーと出力の種類が同じでない限り、異なるイベントまたはメッセージの種類の間で何らかのマッピングを実行する必要があります。

Event Hubs と Service Bus 間でメッセージをコピーする単純なレプリケーション タスクの場合は、独自のコードを記述する必要はなく、レプリケーションのサンプルと共に[提供されているユーティリティ ライブラリ](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication)を利用することができます。

### <a name="retry-policy"></a>再試行ポリシー

レプリケーション関数のいずれかの側で可用性イベントが発生したときにデータが失われないようにするには、堅牢な再試行ポリシーを構成する必要があります。 再試行ポリシーを構成するには、[再試行に関する Azure Functions ドキュメント](../articles/azure-functions/functions-bindings-error-pages.md)を参照してください。 

[サンプル リポジトリ](https://github.com/Azure-Samples/azure-messaging-replication-dotnet)内のプロジェクト例で選択されているポリシー設定により、データ損失を回避するために、5 秒から 15 分の再試行間隔で再試行回数が無限であるエクスポネンシャル バックオフ戦略が構成されます。 

Service Bus については、「[トリガーの回復性に加えて再試行サポートを使用する](../articles/azure-functions/functions-bindings-error-pages.md#using-retry-support-on-top-of-trigger-resilience)」セクションを参照して、トリガーの相互作用と、キューに対して定義されている最大配信回数を理解してください。

### <a name="setting-up-a-replication-application-host"></a>レプリケーション アプリケーション ホストの設定

レプリケーション アプリケーションは、1 つまたは複数のレプリケーション タスクのための実行ホストです。 

それは、従量課金プランまたは Azure Functions Premium プラン (推奨) で実行するように構成されている Azure Functions アプリケーションです。 すべてのレプリケーション アプリケーションは、[システムまたはユーザーによって割り当てられたマネージド ID](../articles/app-service/overview-managed-identity.md) で実行する必要があります。 

リンクされた Azure Resource Manager (ARM) テンプレートにより、以下のものを使用してレプリケーション アプリケーションが作成および構成されます。

* レプリケーションの進行状況とログを追跡するための Azure ストレージ アカウント。
* システムによって割り当てられたマネージド ID。 
* 監視のための Azure Monitoring と Application Insights の統合。

Azure 仮想ネットワーク (VNet) にバインドされた Event Hubs にアクセスする必要があるレプリケーション アプリケーションの場合は、Azure Functions Premium プランを使用し、同じ VNet にアタッチするように構成する必要があります。これは、使用可能なオプションの 1 つでもあります。


|       | デプロイ | 視覚化する  
|-------|------------------|--------------|---------------|
| **Azure Functions 従量課金プラン** | [![Azure へのデプロイ](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2FAconsumption%2Fazuredeploy.json)|[![視覚化する](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fconsumption%2Fazuredeploy.json)
| **Azure Functions の Premium プラン** |[![Azure へのデプロイ](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json) | [![視覚化する](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json)
| **VNet を使用する Azure Functions Premium プラン** | [![Azure へのデプロイ](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json)|[![視覚化する](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json)


### <a name="examples"></a>例

[サンプル リポジトリ](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/)には、Event Hubs 間または Service Bus エンティティ間でイベントをコピーするレプリケーション タスクの例がいくつか含まれています。

Event Hubs 間でイベントをコピーする場合は、イベント ハブ トリガーとイベント ハブ出力バインドを使用します。

```csharp
[FunctionName("telemetry")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task Telemetry(
    [EventHubTrigger("telemetry", ConsumerGroup = "$USER_FUNCTIONS_APP_NAME.telemetry", Connection = "telemetry-source-connection")] EventData[] input,
    [EventHub("telemetry-copy", Connection = "telemetry-target-connection")] EventHubClient outputClient,
    ILogger log)
{
    return EventHubReplicationTasks.ForwardToEventHub(input, outputClient, log);
}
```

Service Bus エンティティ間でメッセージをコピーする場合は、Service Bus のトリガーと出力バインドを使用します。

```csharp
[FunctionName("jobs-transfer")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task JobsTransfer(
    [ServiceBusTrigger("jobs-transfer", Connection = "jobs-transfer-source-connection")] Message[] input,
    [ServiceBus("jobs", Connection = "jobs-target-connection")] IAsyncCollector<Message> output,
    ILogger log)
{
    return ServiceBusReplicationTasks.ForwardToServiceBus(input, output, log);
}
```

ヘルパー メソッドを使用すると、Event Hubs と Service Bus の間でレプリケーションを簡単に行うことができます。

| source      | 移行先      | エントリ ポイント 
|-------------|-------------|------------------------------------------------------------------------
| イベント ハブ   | イベント ハブ   | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub`
| イベント ハブ   | Service Bus | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToServiceBus`
| Service Bus | イベント ハブ   | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub`
| Service Bus | Service Bus | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToServiceBus`


### <a name="monitoring"></a>監視

レプリケーション アプリを監視する方法については、Azure Functions のドキュメントの[監視に関するセクション](../articles/azure-functions/configure-monitoring.md)を参照してください。

レプリケーション タスクの監視に特に役に立つビジュアル ツールは、Application Insights の[アプリケーション マップ](../articles/azure-monitor/app/app-map.md)です。これは、キャプチャされた監視情報から自動的に生成され、レプリケーション タスクによるソースとターゲットの転送の信頼性とパフォーマンスを調べることができます。

すぐに診断情報を得るには、ログの詳細を低遅延で視覚化できる [Live Metrics](../articles/azure-monitor/app/live-stream.md) ポータル ツールを使用します。

## <a name="next-steps"></a>次のステップ

* [Azure Functions のデプロイ](../articles/azure-functions/functions-deployment-technologies.md)
* [Azure Functions の診断](../articles/azure-functions/functions-diagnostics.md)
* [Azure Functions のネットワーク オプション](../articles/azure-functions/functions-networking-options.md)
* [Azure Application Insights](../articles/azure-monitor/app/app-insights-overview.md)