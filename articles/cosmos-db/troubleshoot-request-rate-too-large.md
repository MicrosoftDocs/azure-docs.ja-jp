---
title: Azure Cosmos DB の要求率が大きすぎる例外をトラブルシューティングする
description: 要求率が大きすぎる例外を診断して修正する方法について説明します。
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: c3a692a8df93931bd8fd7982e69fe2f9118444c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94411339"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-rate-too-large-exceptions"></a>Azure Cosmos DB の要求率が大きすぎる例外を診断してトラブルシューティングする
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

"要求率が大きすぎる" というメッセージまたはエラー コード 429 は、要求が調整中であることを示しています。

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順
次のセクションには、要求が多すぎる問題の原因と解決方法が含まれています。

### <a name="check-the-metrics"></a>メトリックを確認する
例外 429 の数を確認するには、[Azure Cosmos DB の監視](monitor-cosmos-db.md)に関するページを参照してください。

#### <a name="cause"></a>原因:
使用されたスループット (要求ユニット/秒) が、[プロビジョニングされたスループット](set-throughput.md)を超えました。 SDK は、指定された再試行ポリシーに基づいて要求を自動的に再試行します。 このエラーが頻繁に発生する場合、コレクションでスループットを増やすことを検討してください。 ポータルのメトリックを調べて、429 エラーが発生しているかどうかを確認します。 パーティション キーを調べて、[ストレージおよび要求ボリュームが均等に分散されていること](partitioning-overview.md)を確認します。

#### <a name="solution"></a>解決方法:
1. [ポータルまたは SDK](set-throughput.md) を使用して、プロビジョニングされているスループットを大きくします。
1. データベースまたはコンテナーを[自動スケーリング](provision-throughput-autoscale.md)に切り替えます。

## <a name="next-steps"></a>次のステップ
* Azure Cosmos DB .NET SDK 使用時の問題を[診断してトラブルシューティングする](troubleshoot-dot-net-sdk.md)。
* [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) と [.NET v2](performance-tips.md) のパフォーマンス ガイドラインを確認する。
* Azure Cosmos DB Java v4 SDK 使用時の問題を[診断してトラブルシューティングする](troubleshoot-java-sdk-v4-sql.md)。
* [Java v4 SDK](performance-tips-java-sdk-v4-sql.md) のパフォーマンス ガイドラインを確認する。