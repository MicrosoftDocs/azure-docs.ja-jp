---
title: "Azure Event Hubs API の概要 | Microsoft Docs"
description: "使用可能な Azure Event Hubs API の概要"
services: event-hubs
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 3f221a0c-182d-4e39-9f3d-3a3c16c5c6ed
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2017
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: aa7244849f6286e8ef9f9785c133b4c326193c12
ms.openlocfilehash: 5a360462288e5df6e0ede5f11adabba158a9dd57

---

# <a name="available-event-hubs-apis"></a>使用可能な Event Hubs API

## <a name="runtime-apis"></a>ランタイム API

現在使用できる Event Hubs ランタイム クライアントの一覧を次に示します。 ライブラリの中には管理機能が制限されているものがありますが、管理操作専用の[ライブラリ](#management-apis)もあります。 これらのライブラリの主な目的は、Event Hub からメッセージを送受信することです。

各ランタイム ライブラリの現在の状態の詳細については、[追加情報](#additional-information)を参照してください。

| 言語/プラットフォーム | クライアント パッケージ | EventProcessorHost パッケージ | リポジトリ |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) | [GitHub](https://github.com/azure/azure-event-hubs-dotnet) |
| .NET Framework | [NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) | 該当なし |
| Java | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) | [GitHub](https://github.com/Azure/azure-event-hubs-java) |
| ノード | [NPM](https://www.npmjs.com/package/azure-event-hubs) | 該当なし | [GitHub](https://github.com/Azure/azure-event-hubs-node) |
| C | 該当なし | 該当なし | [GitHub](https://github.com/Azure/azure-event-hubs-c) |

### <a name="additional-information"></a>追加情報

#### <a name="net"></a>.NET
.NET エコシステムには複数のランタイムがあるため、Event Hubs に対して複数の .NET ライブラリがあります。 .NET Standard ライブラリは、.NET Core または .NET Framework のいずれかを使用して実行できますが、.NET Framework ライブラリについては、.NET Framework 環境でのみ実行できます。 .NET Framework の詳細については、「[フレームワークのバージョン](https://docs.microsoft.com/dotnet/articles/standard/frameworks#framework-versions)」を参照してください。

#### <a name="node"></a>ノード

Node.js ライブラリは現在プレビュー段階で、Microsoft 従業員と外部の共同作成者によってサイド プロジェクトとして管理されています。 ソース コードなど、お寄せいただいた投稿はすべて確認いたします。

## <a name="management-apis"></a>管理 API

現在使用できる管理専用ライブラリをの一覧を次に示します。 ここで示すライブラリは Event Hubs エンティティの管理専用で、ランタイム操作は含まれていません。

| 言語/プラットフォーム | 管理パッケージ | リポジトリ |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) | [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/ResourceManagement/EventHub) |

## <a name="next-steps"></a>次のステップ
Event Hubs の詳細については、次のリンク先を参照してください:

* [Event Hubs の概要](event-hubs-what-is-event-hubs.md)
* [イベント ハブの作成](event-hubs-create.md)
* [Event Hubs の FAQ](event-hubs-faq.md)


<!--HONumber=Feb17_HO1-->


