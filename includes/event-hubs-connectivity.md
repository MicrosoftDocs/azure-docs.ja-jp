---
title: インクルード ファイル
description: インクルード ファイル
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 01/21/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 44afd8ea4ef2ab06ec31b7528e9776faebc3b4dc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98689910"
---
### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>ファイアウォールで開く必要があるのはどのポートですか。 
Azure Event Hubs でイベントを送受信するために、次のプロトコルを使用できます。

- Advanced Message Queuing Protocol 1.0 (AMQP)
- TLS を使用したハイパーテキスト転送プロトコル 1.1 (HTTPS)
- Apache Kafka

これらのプロトコルを使用して Azure Event Hubs と通信するために開く必要がある送信ポートについては、次の表を参照してください。 

| Protocol | Port | 詳細 | 
| -------- | ----- | ------- | 
| AMQP | 5671 と 5672 | [AMQP プロトコル ガイド](../articles/service-bus-messaging/service-bus-amqp-protocol-guide.md)に関するページを参照してください | 
| HTTPS | 443 | このポートは、HTTP/REST API と AMQP (WebSocket 経由) で使用されます。 |
| Kafka | 9093 | [Kafka アプリケーションからの Event Hubs の使用](../articles/event-hubs/event-hubs-for-kafka-ecosystem-overview.md)に関するページをご覧ください

HTTPS ポートは、ポート 5671 で AMQP が使用されている場合に送信通信にも必要です。これは、クライアント SDK によって実行されるいくつかの管理操作と Azure Active Directory からのトークンの取得 (使用する場合) が HTTPS 経由で実行されるためです。 

公式の Azure SDK では、通常、Event Hubs に対するイベントの送受信で AMQP プロトコルが使用されます。 WebSocket 経由の AMQP プロトコル オプションは、HTTP API と同様に、ポート TCP 443 で実行されますが、それ以外については通常の AMQP と機能的に同じです。 このオプションでは、HTTPS ポートを共有するためのトレードオフとして、追加のハンドシェイクのラウンドトリップが発生し、若干のオーバーヘッドが生じるため、初期接続の待機時間が長くなります。 このモードが選択されている場合は、通信のためには TCP ポート 443 で十分です。 次のオプションでは、通常の AMQP または AMQP WebSocket モードを選択できます。

| 言語 | オプション   |
| -------- | ----- |
| .NET     | [EventHubConnectionOptions.TransportType](/dotnet/api/azure.messaging.eventhubs.eventhubconnectionoptions.transporttype) プロパティが [EventHubsTransportType.AmqpTcp](/dotnet/api/azure.messaging.eventhubs.eventhubstransporttype) または [EventHubsTransportType.AmqpWebSockets](/dotnet/api/azure.messaging.eventhubs.eventhubstransporttype) |
| Java     | [com.microsoft.azure.eventhubs.EventProcessorClientBuilder.transporttype](/java/api/com.azure.messaging.eventhubs.eventprocessorclientbuilder.transporttype) が [AmqpTransportType.AMQP](/java/api/com.azure.core.amqp.amqptransporttype) または [AmqpTransportType.AMQP_WEB_SOCKETS](/java/api/com.azure.core.amqp.amqptransporttype) |
| ノード  | [EventHubConsumerClientOptions](/javascript/api/@azure/event-hubs/eventhubconsumerclientoptions) に `webSocketOptions` プロパティがある。 |
| Python | [EventHubConsumerClient.transport_type](/python/api/azure-eventhub/azure.eventhub.eventhubconsumerclient) が [TransportType.Amqp](/python/api/azure-eventhub/azure.eventhub.transporttype) または [TransportType.AmqpOverWebSocket](/python/api/azure-eventhub/azure.eventhub.transporttype) |

### <a name="what-ip-addresses-do-i-need-to-allow"></a>どのような IP アドレスを許可する必要がありますか。
Azure を使用している場合、使用している、または使用しようとしているすべての Azure サービスにアクセスするために、企業のファイアウォールまたはプロキシで特定の IP アドレス範囲または URL を許可することが必要になる場合があります。 Event Hubs によって使用される IP アドレスでトラフィックが許可されていることを確認します。 Azure Event Hubs によって使用される IP アドレスについては、「[Azure の IP 範囲とサービス タグ - パブリック クラウド](https://www.microsoft.com/download/details.aspx?id=56519)」を参照してください。

また、名前空間の IP アドレスが許可されていることを確認します。 接続を許可する適切な IP アドレスを検索するには、次の手順を実行します。

1. コマンド プロンプトで、次のコマンドを実行します。 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. `Non-authoritative answer` で返された IP アドレスをメモします。 

名前空間に **ゾーン冗長性** を使用している場合は、次の追加手順を実行する必要があります。 

1. まず、名前空間に対して nslookup を実行します。

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. **non-authoritative answer** セクションの名前をメモします。これは、次のいずれかの形式になります。 

    ```
    <name>-s1.cloudapp.net
    <name>-s2.cloudapp.net
    <name>-s3.cloudapp.net
    ```
3. s1、s2、s3 のサフィックスが付いているそれぞれについて nslookup を実行し、3 つの可用性ゾーンで実行されている 3 つのインスタンスすべての IP アドレスを取得します。 

    > [!NOTE]
    > `nslookup` コマンドによって返された IP アドレスは、静的 IP アドレスではありません。 ただし、基になるデプロイが削除されるか別のクラスターに移動されるまでは変わりません。

### <a name="what-client-ips-are-sending-events-to-or-receiving-events-from-my-namespace"></a>どのクライアント IP によって名前空間からイベントの送信または受信が行われているか
まず、名前空間で [IP フィルター](../articles/event-hubs/event-hubs-ip-filtering.md)を有効にします。 

次に、「[診断ログを有効にする](../articles/event-hubs/event-hubs-diagnostic-logs.md#enable-diagnostic-logs)」の手順に従って、[Event Hubs 仮想ネットワーク接続イベント](../articles/event-hubs/event-hubs-diagnostic-logs.md#event-hubs-virtual-network-connection-event-schema)の診断ログを有効にします。 接続が拒否された IP アドレスが表示されます。

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

> [!IMPORTANT]
> 仮想ネットワーク ログが生成されるのは、名前空間で **特定の IP アドレス** (IP フィルター規則) からのアクセスが許可されている場合のみです。 これらの機能を使用して名前空間へのアクセスを制限せずに、Azure Event Hubs 名前空間に接続しているクライアントの IP アドレスを追跡する仮想ネットワーク ログを取得する場合は、次の回避策を使用できます。IP フィルター処理を有効にし、アドレス指定可能な IPv4 の範囲の合計 (1.0.0.0/1 - 255.0.0.0/1) を追加します。 Azure Event Hubs は IPv6 アドレス範囲をサポートしていません。 

> [!NOTE]
> 現時点では、個々のメッセージまたはイベントのソース IP を特定することはできません。 