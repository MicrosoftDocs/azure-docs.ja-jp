---
title: Azure Event Hubs - クライアント SDK | Microsoft Docs
description: この記事では、Azure Event Hubs 用のクライアント SDK に関する情報を提供します。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 9f4eec603245f1e4ea6fa4d97b843ca6a770f2ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88930878"
---
# <a name="azure-event-hubs---client-sdks"></a>Azure Event Hubs - クライアント SDK
この記事では、Azure Event Hubs でサポートされる SDK について、次の情報を提供します。 

- アプリケーションで使用できるパッケージの場所 
- ソース コード、サンプル、readme、変更ログ、報告された問題を見つけることができ、新しい問題を提起することもできる GitHub の場所 
- クイックスタート チュートリアルへのリンク 

## <a name="client-sdks"></a>クライアント SDK
以下の表では、現在使用できるすべての Azure Event Hubs ランタイム クライアントについて説明します。 ライブラリの中には管理機能が制限されているものがありますが、管理操作専用のライブラリもあります。 これらのライブラリの主な目的は、イベント ハブから **メッセージを送受信する** ことです。

| Language | Package | リファレンス | 
| -------- | ------- | --------------- | 
| . NET Standard (**最新**。.NET Core と .NET Framework の両方をサポート) | [Azure.Messaging.EventHubs](https://www.nuget.org/packages/Azure.Messaging.EventHubs/) |<ul><li>[GitHub の場所](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs)</li><li>[チュートリアル](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
|       | [Azure.Messaging.EventHubs.Processor](https://www.nuget.org/packages/Azure.Messaging.EventHubs.Processor/) | <ul><li>[GitHub の場所](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor)</li><li>[チュートリアル](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
| . NET Standard (**レガシ**。.NET Core と .NET Framework の両方をサポート) | [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | <ul><li>[GitHub の場所](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs)</li><li>[チュートリアル](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> | 
|       | [Microsoft.Azure.EventHubs.Processor](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor) | <ul><li>[GitHub の場所](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.Processor)</li><li>[チュートリアル](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
| . NET Framework (**古い**) | [WindowsAzure.Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) |<ul><li>[チュートリアル](event-hubs-dotnet-framework-getstarted-send.md)</li></ul> |
| Java | [azure-messaging-eventhubs](https://search.maven.org/search?q=a:azure-messaging-eventhubs) | <ul><li>[GitHub の場所](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs)</li><li>[チュートリアル](event-hubs-java-get-started-send.md)</li></ul> |
|      | [azure-eventhubs](https://search.maven.org/search?q=a:azure-eventhubs) **(レガシ)** | <ul><li>[GitHub の場所](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/microsoft-azure-eventhubs)</li><li>[チュートリアル](event-hubs-java-get-started-send.md)</li></ul> |
| Python |  [azure-eventhub](https://pypi.org/project/azure-eventhub/) | <ul><li>[GitHub の場所](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub)</li><li>[チュートリアル](event-hubs-python-get-started-send.md)</li></ul> |
|        | [azure-eventhub-checkpointstoreblob-aio](https://pypi.org/project/azure-eventhub-checkpointstoreblob-aio/) | <ul><li>[GitHub の場所](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio)</li><li>[チュートリアル](event-hubs-python-get-started-send.md)</li></ul> |
| JavaScript | [azure/event-hubs](https://www.npmjs.com/package/@azure/event-hubs) | <ul><li>[GitHub の場所](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs)</li><li>[チュートリアル](event-hubs-node-get-started-send.md)</li></ul> |
|            | [azure/eventhubs-checkpointstore-blob](https://www.npmjs.com/package/@azure/eventhubs-checkpointstore-blob) | <ul><li>[GitHub の場所](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/eventhubs-checkpointstore-blob)</li><li>[チュートリアル](event-hubs-node-get-started-send.md)</li></ul> |
| Go | [azure-event-hubs-go](https://github.com/Azure/azure-event-hubs-go) | <ul><li>[GitHub の場所](https://github.com/Azure/azure-event-hubs-go)</li><li>[チュートリアル](event-hubs-go-get-started-send.md)</li></ul> |
| C | [azure-event-hubs-c](https://github.com/Azure/azure-event-hubs-c) | <ul><li>[GitHub の場所](https://github.com/Azure/azure-event-hubs-c)</li><li>[チュートリアル](event-hubs-c-getstarted-send.md)</li></ul> |

## <a name="management-sdks"></a>Management SDK
現在使用できる管理専用ライブラリをの一覧を次の表に示します。 これらのライブラリは **Event Hubs エンティティの管理** 専用であり、ランタイム操作は含まれていません。

| Language | Package | リファレンス | 
| -------- | ------- | --------------- | 
| .NET Standard | [Microsoft.Azure.Management.EventHub](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) |<ul><li>[GitHub の場所](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.Management.EventHub)</li><li>[チュートリアル](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |


## <a name="next-steps"></a>次のステップ

Event Hubs の詳細については、次のリンク先を参照してください:

* [Event Hubs の概要](./event-hubs-about.md)
* [Event Hub を作成する](event-hubs-create.md)
* [Event Hubs の FAQ](event-hubs-faq.md)
