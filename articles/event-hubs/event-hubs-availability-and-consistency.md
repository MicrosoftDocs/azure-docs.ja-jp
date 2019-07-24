---
title: 可用性と一貫性 - Azure Event Hubs | Microsoft Docs
description: パーティションを使用して Azure Event Hubs で最大限の可用性と一貫性を実現する方法
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 8f3637a1-bbd7-481e-be49-b3adf9510ba1
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: e5cad797b633d43bcc9ead657a60fca8aa6679bb
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090722"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Event Hubs における可用性と一貫性

## <a name="overview"></a>概要
Azure Event Hubs は[パーティション分割モデル](event-hubs-features.md#partitions)を使用して、単一のイベント ハブで可用性と並列処理を向上させます。 たとえば、イベント ハブに 4 つのパーティションがあり、そのうちの 1 つが、負荷分散操作によってあるサーバーから別のサーバーに移動されても、残りの 3 つのパーティションで送受信ができます。 また、パーティションが増えると、より多くのリーダーがデータを同時に処理できるため、合計スループットが向上します。 分散システムでのパーティション分割と順序付けの意味合いを理解することは、ソリューション設計の重要な側面です。

順序付けと可用性のトレードオフを理解するには、[CAP 定理](https://en.wikipedia.org/wiki/CAP_theorem)をご覧ください。これはブリュワーの定理とも呼ばれています。 この定理によると、一貫性、可用性、パーティション トレランスの中からの選択が考察されています。 ネットワークごとにパーティション分割されたシステムの場合、常に一貫性と可用性のトレードオフがあることが示されています。

ブリュワー定理によると、一貫性と可用性は次のように定義されています。
* パーティション トレランス - パーティション障害が発生した場合でも、データ処理システムがデータ処理を継続する能力。
* 可用性 - 障害の発生しないノードが適切な応答を適切な時間内に (エラーやタイムアウトなく) 返すこと。
* 一貫性 - 指定のクライアントで読み込みを実行したときに、必ず最新の書き込みデータが返ってくること。

## <a name="partition-tolerance"></a>パーティション トレランス
Event Hubs は、パーティション分割されたデータ モデルの上に構築されます。 Event Hub のパーティションの数はセットアップ時に構成できますが、後でこの値を変更することはできません。 Event Hubs でパーティションを使用する必要があるため、アプリケーションの可用性と一貫性について決定を行う必要があります。

## <a name="availability"></a>可用性
Event Hubs の使用を開始する最も簡単な方法は、既定の動作を使用することです。 新しい **[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)** オブジェクトを作成し、**[Send](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync?view=azure-dotnet#Microsoft_Azure_EventHubs_EventHubClient_SendAsync_Microsoft_Azure_EventHubs_EventData_)** メソッドを使用すると、イベントはイベント ハブのパーティション間に自動的に分散されます。 この動作により、アップ タイムを最大にすることができます。

最大のアップ タイムを必要とするユース ケースでは、このモデルが適しています。

## <a name="consistency"></a>整合性
シナリオによっては、イベントの順序付けが重要になる場合があります。 たとえば、バックエンド システムで、delete コマンドの前に update コマンドを処理したいとします。 この場合、イベントにパーティション キーを設定するか、`PartitionSender` オブジェクトを使用して特定のパーティションに対してのみイベントを送信できます。 これにより、これらのイベントがパーティションから読み取られる際に、読み取られる順番が保証されます。

この構成では、送信先の特定のパーティションが使用できない場合は、エラー応答が受信される点に注意してください。 比較のポイントとして、1 つのパーティションにアフィニティがない場合、Event Hubs サービスは次の利用可能なパーティションにイベントを送信します。

順番を保証しつつ、アップ タイムも最大化するためのソリューションの 1 つは、イベント処理アプリケーションの一部としてイベントを集計することです。 これを実現する最も簡単な方法は、カスタムのシーケンス番号のプロパティをイベントにスタンプすることです。 次に例を示します。

```csharp
// Get the latest sequence number from your application
var sequenceNumber = GetNextSequenceNumber();
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set a custom sequence number property
data.Properties.Add("SequenceNumber", sequenceNumber);
// Send single message async
await eventHubClient.SendAsync(data);
```

この例では、イベント ハブ内の利用可能なパーティションの 1 つにイベントを送信し、アプリケーションの対応するシーケンス番号を設定します。 このソリューションでは処理アプリケーションで状態を保持する必要がありますが、送信者には、使用できる可能性の高いエンドポイントが提示されます。

## <a name="next-steps"></a>次の手順
Event Hubs の詳細については、次のリンク先を参照してください:

* [Event Hubs サービスの概要](event-hubs-what-is-event-hubs.md)
* [イベント ハブの作成](event-hubs-create.md)
