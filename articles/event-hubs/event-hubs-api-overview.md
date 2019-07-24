---
title: Azure Event Hubs API の概要 | Microsoft Docs
description: 使用可能な Azure Event Hubs API の概要
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 3f221a0c-182d-4e39-9f3d-3a3c16c5c6ed
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/02/2018
ms.author: shvija
ms.openlocfilehash: 80566b0246179064d2a479b8c9bf3c79a2a93aac
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59681286"
---
# <a name="available-event-hubs-apis"></a>使用可能な Event Hubs API

この記事では、Event Hubs リソースの管理に使うことができる API クライアントのセットについて説明します。

## <a name="runtime-apis"></a>ランタイム API

次のセクションで、現在使用できるすべての Azure Event Hubs ランタイム クライアントについて説明します。 ライブラリの中には管理機能が制限されているものがありますが、管理操作専用の[ライブラリ](#management-apis)もあります。 これらのライブラリの主な目的は、イベント ハブからメッセージを送受信することです。

各ランタイム ライブラリの現在の状態の詳細については、[追加情報](#additional-information)を参照してください。

| 言語/プラットフォーム | クライアント パッケージ | EventProcessorHost パッケージ | リポジトリ |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) | [GitHub](https://github.com/azure/azure-event-hubs-dotnet) |
| .NET Framework | [NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) | 該当なし |
| Java | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) | [GitHub](https://github.com/Azure/azure-event-hubs-java) |
| ノード | [NPM](https://www.npmjs.com/package/azure-event-hubs) | 該当なし | [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) |
| C | 該当なし | 該当なし | [GitHub](https://github.com/Azure/azure-event-hubs-c) |

### <a name="additional-information"></a>追加情報

#### <a name="net"></a>.NET

.NET エコシステムには複数のランタイムがあるため、Event Hubs に対して複数の .NET ライブラリがあります。 .NET Standard ライブラリは、.NET Core または .NET Framework のいずれかを使用して実行できますが、.NET Framework ライブラリについては、.NET Framework 環境でのみ実行できます。 .NET Framework バージョンの詳細については、「[フレームワークのバージョン](https://docs.microsoft.com/dotnet/articles/standard/frameworks)」を参照してください。

#### <a name="node"></a>ノード

[Node.js ライブラリ](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs)は現在プレビュー段階で、Microsoft 従業員と外部の共同作成者によってサイド プロジェクトとして管理されています。 ソース コードなど、お寄せいただいた投稿はすべて確認いたします。

## <a name="management-apis"></a>管理 API

現在使用できる管理専用ライブラリをの一覧を次の表に示します。 ここで示すライブラリは Event Hubs エンティティの管理専用で、ランタイム操作は含まれていません。

| 言語/プラットフォーム | 管理パッケージ | リポジトリ |
| --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) | [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/ResourceManagement/EventHub) |

## <a name="next-steps"></a>次の手順
Event Hubs の詳細については、次のリンク先を参照してください:

* [Event Hubs の概要](event-hubs-what-is-event-hubs.md)
* [イベント ハブの作成](event-hubs-create.md)
* [Event Hubs の FAQ](event-hubs-faq.md)
