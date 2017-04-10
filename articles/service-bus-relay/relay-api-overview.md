---
title: "Azure リレー API の概要 | Microsoft Docs"
description: "使用可能な Azure リレー API の概要"
services: event-hubs
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: fdaa1d2b-bd80-4e75-abb9-0c3d0773af2d
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: edc730aa383b07dc308da8a160203faf3636208b
ms.lasthandoff: 03/27/2017

---

# <a name="available-relay-apis"></a>使用可能なリレー API

## <a name="runtime-apis"></a>ランタイム API

現在使用できるリレー ランタイム クライアントの一覧を次に示します。

各ランタイム ライブラリの状態の詳細については、[追加情報](#additional-information)を参照してください。

| 言語/プラットフォーム | 使用可能な機能 | クライアント パッケージ | リポジトリ |
| --- | --- | --- | --- |
| .NET Standard | Hybrid Connections | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET Framework | WCF リレー | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | 該当なし |
| ノード | Hybrid Connections | [hyco-ws](https://www.npmjs.com/package/hyco-ws)<br/>[hyco-websocket](https://www.npmjs.com/package/hyco-websocket) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>追加情報

#### <a name="net"></a>.NET
.NET エコシステムには複数のランタイムがあるため、Event Hubs に対して複数の .NET ライブラリがあります。 .NET Standard ライブラリは、.NET Core または .NET Framework のいずれかを使用して実行できますが、.NET Framework ライブラリについては、.NET Framework 環境でのみ実行できます。 .NET Framework の詳細については、「[フレームワークのバージョン](https://docs.microsoft.com/dotnet/articles/standard/frameworks#framework-versions)」を参照してください。

## <a name="next-steps"></a>次のステップ
Azure リレーの詳細については、次のリンク先を参照してください。
* [What is Azure Relay? (Azure Relay とは)](relay-what-is-it.md)
* [Relay に関する FAQ](relay-faq.md)
