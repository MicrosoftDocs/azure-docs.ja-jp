---
title: Apache Cassandra と Azure Cosmos DB の整合性レベル
description: Apache Cassandra と Azure Cosmos DB の整合性レベル。
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 3107610215d5b37c43124ce4129b2eb5437e3b62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97516839"
---
# <a name="apache-cassandra-and-azure-cosmos-db-cassandra-api-consistency-levels"></a>Apache Cassandra と Azure Cosmos DB の Cassandra API の整合性レベル
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Apache Cassandra は、Azure Cosmos DB とは異なり、整合性の保証がネイティブで正確に定義されていません。 代わりに Apache Cassandra には、高可用性、整合性および待機時間とのトレードオフを可能にする、書き込みの整合性レベルと読み取りの整合性レベルがあります。 Azure Cosmos DB の Cassandra API を使用する場合:

* Apache Cassandra の書き込み整合性レベルは、お使いの Azure Cosmos アカウントに構成された既定の整合性レベルにマップされます。 書き込み操作 (CL) の整合性は、要求ごとに変更することはできません。

* Azure Cosmos DB は、Cassandra クライアント ドライバーによって指定された読み取り整合性レベルを、読み取り要求に動的に構成された Azure Cosmos DB 整合性レベルの 1 つに動的にマップします。

## <a name="multi-region-writes-vs-single-region-writes"></a>複数リージョンの書き込み対単一リージョンの書き込み

Apache Cassandra データベースは、既定ではマルチマスター システムであり、読み取りに複数リージョン レプリケーションを使用した単一リージョンの書き込みに、すぐに使用できるオプションは用意されていません。 ただし、Azure Cosmos DB には、単一リージョンか[複数リージョン](how-to-multi-master.md)のどちらかの書き込み構成を使用するターンキー機能が備えられています。 複数のリージョンにわたって単一リージョンの書き込み構成を選択できる利点の 1 つは、リージョン間の競合シナリオを回避することであり、複数のリージョン間で強力な整合性を維持することができます。 

単一リージョンの書き込みでは、強力な整合性を維持しながら、[自動フェールオーバー](high-availability.md#multi-region-accounts-with-a-single-write-region-write-region-outage)を使用してリージョン間で高可用性レベルを維持することもできます。 この構成では、要求ごとに最終的な整合性にダウングレードすることによって、データの局所性を利用して読み取り待機時間を短縮できます。 これらの機能に加えて、Azure Cosmos DB プラットフォームでは、リージョンを選択するときに、[ゾーン冗長性](high-availability.md#availability-zone-support) を有効にする機能も用意されています。 したがって、ネイティブの Apache Cassandra とは異なり、Azure Cosmos DB を使用すると、CAP 定理の[トレードオフの領域](consistency-levels.md#rto)をより詳細に操作できるようになります。

## <a name="mapping-consistency-levels"></a>整合性レベルのマッピング

Azure Cosmos DB プラットフォームには、[CAP 定理](https://en.wikipedia.org/wiki/CAP_theorem)および [PACLC 定理](https://en.wikipedia.org/wiki/PACELC_theorem)によって定義されたレプリケーションとトレードオフに関する、明確に定義されたビジネス ユース ケース指向の 5 つの整合性設定のセットを用意されています。 このアプローチは Apache Cassandra とは大きく異なるため、[ドキュメント](consistency-levels.md)の Azure Cosmos DB の整合性設定を確認して理解するか、この短い[ビデオ](https://www.youtube.com/watch?v=t1--kZjrG-o) ガイドを見て Azure Cosmos DB プラットフォームでの整合性設定を理解することをお勧めします。

次の表は、Cassandra API を使用する場合の Apache Cassandra と Azure Cosmos DB の整合性レベル間での可能性のあるマッピングを示しています。 ここには、単一リージョン、複数リージョンの読み取りと単一リージョンの書き込み、複数リージョンの書き込みの構成が示されています。

> [!NOTE]
> これらは正確なマッピングではありません。 むしろ、Apache Cassandra に最も近い類似物を提供し、右端の列に質的な違いを明確にしてきました。 前述のように、Azure Cosmos DB の[整合性設定](consistency-levels.md)を確認することをお勧めします。 

:::image type="content" source="./media/cassandra-consistency/account.png" alt-text="Cassandra の整合性アカウント レベル マッピング" lightbox="./media/cassandra-consistency/account.png" :::

:::image type="content" source="./media/cassandra-consistency/dynamic.png" alt-text="Cassandra の整合性動的マッピング" lightbox="./media/cassandra-consistency/dynamic.png" :::

Azure Cosmos アカウントが、強固な整合性以外の任意の整合性レベルで構成されている場合、*確率論的有界整合性制約* (PBS) メトリックを調べることによって、ワークロードの厳密な整合性を持つ読み取りをクライアントが取得する確率を調べることができます。 このメトリックは、Azure portal で公開されます。詳しくは、「[確率的有界整合性制約 (PBS) メトリックを監視する](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)」をご覧ください。

確率論的有界整合性制約は、最終的な整合性が、どれほど最終的であるかを示します。 このメトリックでは、Azure Cosmos アカウントで現在構成されている整合性レベルより強力な整合性をどのくらいの頻度で得られるかについての分析情報が提供されます。 つまり、書き込みと読み取りのリージョンの組み合わせに対して厳密な整合性の読み取りを取得する確率 (ミリ秒で測定) を表示できます。

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB でのグローバル配信と整合性レベルについて学習します。

* [グローバル配信の概要](distribute-data-globally.md)
* [整合性レベルの概要](consistency-levels.md)
* [高可用性](high-availability.md)
