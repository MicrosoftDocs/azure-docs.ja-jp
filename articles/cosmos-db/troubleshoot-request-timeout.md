---
title: Azure Cosmos DB のサービス要求タイムアウト例外をトラブルシューティングする
description: Azure Cosmos DB のサービス要求タイムアウト例外を診断して修正する方法について説明します。
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 5b188021de30561222f098e2b5782bada25d7ce0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94411254"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-timeout-exceptions"></a>Azure Cosmos DB の要求タイムアウト例外の問題を診断してトラブルシューティングする
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB で HTTP 408 要求タイムアウトが返されました。

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順
次の一覧には、要求タイムアウト例外の既知の原因と解決方法が含まれています。

### <a name="check-the-sla"></a>SLA を確認する
[Azure Cosmos DB の監視](monitor-cosmos-db.md)を確認して、番号 408 の例外が Azure Cosmos DB の SLA に違反しているかどうかを確認する必要があります。

#### <a name="solution-1-it-didnt-violate-the-azure-cosmos-db-sla"></a>解決策 1: Azure Cosmos DB の SLA に違反していなかった
このような一時的なエラーが発生した場合、このシナリオはアプリケーションによって処理され、再試行されます。

#### <a name="solution-2-it-did-violate-the-azure-cosmos-db-sla"></a>解決策 2: Azure Cosmos DB の SLA に違反していた
[Azure サポート](https://aka.ms/azure-support)にお問い合わせください。
 
### <a name="hot-partition-key"></a>ホット パーティション キー
Azure Cosmos DB は、プロビジョニングされたスループット全体を物理パーティション間で均等に分散します。 ホット パーティションが存在すると、ある物理パーティション上の 1 つ以上の論理パーティション キーによってその物理パーティションのすべての要求ユニット/秒 (RU/秒) が消費されます。 同時に、他の物理パーティション上の RU/秒は未使用のままになります。 症状としては、消費済み RU/秒の合計は、データベースまたはコンテナー上にプロビジョニングされている全体的な RU/秒よりも少なくなります。 ホット論理パーティション キーに対する要求では、引き続き帯域幅調整 (429) が見られます。 [正規化された RU 消費量メトリック](monitor-normalized-request-units.md) を使用して、ワークロードでホット パーティションが発生しているかどうかを確認します。 

#### <a name="solution"></a>解決方法:
要求のボリュームと記憶域を均等に分散する適切なパーティション キーを選択します。 [パーティション キーの変更](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/)方法を参照してください。

## <a name="next-steps"></a>次のステップ
* Azure Cosmos DB .NET SDK 使用時の問題を[診断してトラブルシューティングする](troubleshoot-dot-net-sdk.md)。
* [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) と [.NET v2](performance-tips.md) のパフォーマンス ガイドラインを確認する。
* Azure Cosmos DB Java v4 SDK 使用時の問題を[診断してトラブルシューティングする](troubleshoot-java-sdk-v4-sql.md)。
* [Java v4 SDK](performance-tips-java-sdk-v4-sql.md) のパフォーマンス ガイドラインを確認する。