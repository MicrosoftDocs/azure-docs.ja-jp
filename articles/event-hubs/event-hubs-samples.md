---
title: "Azure Event Hubs アーカイブ | Microsoft Docs"
description: "Azure Event Hubs サンプル"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2017
ms.author: sethm
ms.openlocfilehash: ae9fbd97a1747d8f14c561f247a0973bb11fd039
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="event-hubs-samples"></a>Event Hubs サンプル 

一連の Azure Event Hubs サンプルは、[Azure Event Hubs](/azure/event-hubs/) の主な機能を示しています。 この記事では、使用可能なサンプルを分類して説明しています。また、各サンプルへのリンクも含まれています。

この記事の執筆時には、Event Hubs サンプルは複数の異なる場所に配置されています。

- [MSDN デベロッパー コード サンプル](https://code.msdn.microsoft.com/site/search?query=event%20hubs&f%5B0%5D.Value=event%20hubs&f%5B0%5D.Type=SearchText&ac=5)
- [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples)

.NET Framework のバージョンの詳細については、「[フレームワークとターゲット](/dotnet/articles/standard/frameworks)」を参照してください。

サンプルは今後さらに追加される予定です。頻繁にここに戻り更新情報を確認してください。

## <a name="net-standard"></a>.NET Standard

次のサンプルは、[.NET Standard ライブラリ](/dotnet/articles/standard/library)の [Event Hub クライアント](https://github.com/Azure/azure-event-hubs-dotnet/blob/master/readme.md)を使用して、イベントを送受信する方法を示しています。

### <a name="send-events"></a>送信イベント 

[送信開始](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender)のサンプルは、イベントをイベント ハブに送信する .NET Core コンソール アプリケーションを記述する方法を示しています。

### <a name="receive-events"></a>受信イベント 

[イベント プロセッサ ホストでの受信開始](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver)のサンプルは、イベント プロセッサ ホストを使用して、イベント ハブからメッセージを受け取る .NET Core コンソール アプリケーションです。

## <a name="net-framework"></a>.NET Framework   

このサンプルは、[.NET Framework ライブラリ](/dotnet/framework/index)を対象とする、Azure Event Hubs のその他のさまざまな機能を示しています。
 
### <a name="notify-users-of-events-received"></a>ユーザーへの受信イベントの通知

[AppToNotifyUsers](https://github.com/Azure-Samples/event-hubs-dotnet-user-notifications) サンプルは、センサーまたはその他のシステムから受信したデータをユーザーに通知します。

### <a name="get-started-with-event-hubs"></a>Event Hubs の使用 

[Event Hubs 使用開始](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097)のサンプルは、イベント ハブの作成方法、イベント ハブにイベントを送信する方法、[イベント プロセッサ ホスト](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/)を使用したイベントの利用方法など、Event Hubs の基本機能を示しています。

### <a name="scale-out-event-processing"></a>イベント処理のスケールアウト 

[イベント処理のスケール アウト](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3)のサンプルは、[イベント プロセッサ ホスト](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/)を使用して、Event Hubs ストリーム消費のワークロードを分散する方法を示しています。 **EventProcessor** と **EventProcessorFactory** オブジェクトを実装して、イベント ストリームを管理する方法を示します。 

###  <a name="pull-data-from-sql-into-an-event-hub"></a>イベント ハブへの SQL データのプル

[SQL データのプル](https://github.com/Azure-Samples/event-hubs-dotnet-import-from-sql)のサンプルは、SQL テーブルからデータをプルし、それをイベント ハブにプッシュしたうえで、ダウン ストリーム分析アプリケーションの入力として使用する方法を示しています。

### <a name="pull-web-data-into-an-event-hub"></a>イベント ハブへの Web データのプル 

[Web からのデータ インポート](https://github.com/Azure-Samples/event-hubs-dotnet-importfromweb) サンプルは、パブリック フィード (定時運航データのトラフィック情報フィードなど) からデータをプルし、それをイベント ハブにプッシュする方法を示しています。

## <a name="next-steps"></a>次のステップ

.NET Framework バージョンの詳細については、次のリンク先を参照してください。

- [フレームワークとターゲット](/dotnet/articles/standard/frameworks)
- [.NET Framework 4.6 と 4.5](/dotnet/framework/index)

Event Hubs の詳細については、次の記事を参照してください。

- [Event Hubs の概要](event-hubs-what-is-event-hubs.md)
- [イベント ハブの作成](event-hubs-create.md)
- [Event Hubs の FAQ](event-hubs-faq.md)