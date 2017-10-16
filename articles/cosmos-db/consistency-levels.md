---
title: "Azure Cosmos DB の一貫性レベル | Microsoft Docs"
description: "Azure Cosmos DB には、結果的な一貫性、可用性、待機時間のトレードオフを調整できる 5 つの一貫性レベルがあります。"
keywords: "eventual 一貫性, azure cosmos db, azure, Microsoft azure"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: cgronlun
documentationcenter: 
ms.assetid: 3fe51cfa-a889-4a4a-b320-16bf871fe74c
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: mimig
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a1ebec2285982c70aa9dc49950769fe18e2e2d0d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tunable-data-consistency-levels-in-azure-cosmos-db"></a>Azure Cosmos DB の調整可能なデータの一貫性レベル
Azure Cosmos DB は、すべてのデータ モデルについて、最初からグローバル分散を念頭に置いて設計されています。 その設計により、予測可能な待機時間の短縮の保証、可用性が 99.99% の SLA、明確に定義された複数の緩やかな一貫性モデルが提供されます。 現在、Azure Cosmos DB では、厳密、有界整合性制約、セッション、最終的の 5 つの整合性レベルが用意されています。 

分散データベースが一般的に提供している **Strong** と **Eventual の一貫性**モデルに加え、Azure Cosmos DB では、注意深く体系化、運用化された一貫性モデルがさらに 3 つ提供されます。現実のユース ケースに対するその有用性は Azure Cosmos DB によって検証されています。 これらは、**Bounded Staleness**、**Session**、および **Consistent Prefix** 一貫性レベルです。 これら 5 つの一貫性レベルが揃っているため、一貫性、可用性、待機時間の最適なトレードオフを根拠を持って検討することができます。 

## <a name="distributed-databases-and-consistency"></a>分散データベースと一貫性
分散型の商用データベースは、2 つのカテゴリに分けることができます。1 つは整合性に関して明確に定義された証明可能な選択肢が用意されていないデータベース、もう 1 つはプログラム面で大きく異なる 2 つの選択肢 (厳密な整合性と結果整合性) が存在するデータベースです。 

前者はレプリケーションのプロトコルが細かく、アプリケーション開発者にとって負担となります。また、整合性、可用性、待ち時間、スループットのトレードオフを検討するという難しい作業が発生します。 後者は、まったく異なる 2 つの選択肢から 1 つを選ばなければならないという点が大きな重圧となります。 整合性モデルに関する調査や提案はこれまでに幾度となく実施されており、対象となったモデルの種類も 50 以上にのぼりますが、分散データベースのコミュニティでは依然として、厳密な整合性と結果整合性を超える整合性レベルの商品化には至っていません。 Cosmos DB により開発者は、一貫性の程度に応じて明確に定義された 5 種類の一貫性モデルの中から選択することができます。具体的には、"厳密"、"有界整合性制約"、"[セッション](http://dl.acm.org/citation.cfm?id=383631)"、"一貫性のあるプレフィックス"、"結果的" の 5 つが用意されています。 

![Azure Cosmos DB では、明確に定義された (緩やかな) 整合性モデルがいくつも用意されており、そこから 1 つを選択できます](./media/consistency-levels/five-consistency-levels.png)

以下の表は、それぞれの整合性レベルで提供される具体的な保証の内容を示したものです。
 
**整合性レベルと保証内容**

| 整合性レベル | 保証内容 |
| --- | --- |
| Strong | 線形化可能性 |
| Bounded Staleness | 一貫性のあるプレフィックス。 読み取りが、書き込みからプレフィックス k または間隔 t ごとに実行される |
| Session   | 一貫性のあるプレフィックス。 単調読み取り、単調書き込み、書き込み後の読み取り、読み取り後の書き込み |
| 一貫性のあるプレフィックス | 返される更新が、それ以外の全更新の一部のプレフィックスとなる (ギャップなし) |
| Eventual  | 読み取りは順不同 |

Cosmos DB アカウントには、既定の整合性レベルを構成できます (さらに、後から特定の読み取り要求について既定の整合性を無視することもできます)。 内部的には、複数のリージョンにまたがる可能性があるパーティション セット内のデータに対して、既定の整合性レベルが適用されます。 約 73% のテナントが Session 一貫性を使用し、20% が Bounded Staleness を使用しています。 顧客の約 3% が、アプリケーション用に特定の一貫性の設定を選択する前に、さまざまな一貫性レベルを試していることがわかっています。 また、テナントのわずか 2% が、要求ベースで一貫性レベルをオーバーライドしていることもわかっています。 

Cosmos DB では、Session、Consistent Prefix、Eventual Consistency での読み取りは、Strong または Bounded Staleness 一貫性での読み取りより 2 倍節約できます。 Cosmos DB は、一貫性の保証に加え、可用性、スループット、待機時間を含む、業界最高レベルの包括的な 99.99% の SLA を誇ります。 サービス テレメトリで継続的に動作し、一貫性の違反を公表する[線形化可能性チェッカー](http://dl.acm.org/citation.cfm?id=1806634)を採用しています。 Bounded Staleness では、違反がないか監視して k および t 制約に報告します。 また、5 つの穏やかな一貫性レベルすべてについて、[確率論的 Bounded Staleness メトリック](http://dl.acm.org/citation.cfm?id=2212359)をユーザーに直接報告します。  

## <a name="scope-of-consistency"></a>一貫性のスコープ
一貫性の粒度の対象は、単一のユーザー要求です。 書き込み要求は、挿入、置換、アップサート、または削除のトランザクションに対応付けられます。 書き込みと同様に、読み取り/クエリ トランザクションの対象も単一のユーザー要求です。 ユーザーは複数のパーティションにまたがって大規模な結果セットを読み込む必要がありますが、各読み取りトランザクションは単一のページを対象とし、単一のパーティション内から提供されます。

## <a name="consistency-levels"></a>一貫性レベル
データベース アカウントには既定の一貫性レベルを構成できます。既定の一貫性レベルは、対象となる Cosmos DB アカウント下のすべてのコレクション (およびデータベース) に適用されます。 既定では、ユーザー定義リソースに対して発行されたすべての読み取りとクエリに、データベース アカウントに指定された既定の一貫性レベルが使用されます。 サポートされている各 API の特定の読み取り/クエリ要求の一貫性レベルを緩和することもできます。 このセクションに示されているように、Azure Cosmos DB のレプリケーション プロトコルによって 5 種類の一貫性レベルがサポートされており、特定の一貫性の保証とパフォーマンスの間の明確なトレードオフを実現しています。

**Strong**: 

* Strong 一貫性では、[線形化可能性](https://aphyr.com/posts/313-strong-consistency-models)が保証されます。ここでは読み取りに対して最新バージョンのアイテムを返すことが保証されています。 
* レプリカのマジョリティ クォーラムによってコミットされた後でのみ、書き込み結果が見える状態になることが保証されます。 書き込みは、プライマリとセカンダリのクォーラムとの両方によって同期的かつ永続的にコミットされるか、または中止されるか、のどちらかとなります。 読み取りは常に、マジョリティ リード クォーラムによって確認応答されます。未コミットの書き込みや部分的な書き込みがクライアントから見えることはありません。読み取りの内容は常に、確認応答が返された最新の書き込み結果であることが保証されます。 
* Strong 一貫性を使用するように構成された Azure Cosmos DB アカウントでは、複数の Azure リージョンをその Azure Cosmos DB アカウントに関連付けることができません。 
* "厳密な" 整合性を使用した場合の (消費される [要求ユニット](request-units.md) に関する) 読み取り操作のコストは "セッション" 整合性や "最終的" 整合性よりも高いものの、有界整合性制約と同じです。

**Bounded Staleness**: 

* Bounded Staleness 一貫性では、最大でアイテムの *K* 個のバージョンまたはプレフィックスあるいは期間 *t* の分だけ、読み取りが書き込みに対し遅れることが保証されます。 
* このため、Bounded Staleness を選択する場合、"staleness" は、アイテムのバージョンの数 *K* (書き込みに対する読み取りの遅れ) と、期間 *t* の 2 つの方法で構成できます。 
* Bounded Staleness では、"staleness 期間" 内を除いてトータルなグローバル順序が実現されます。 リージョン内では、"staleness 期間" 内外の両方でモノトニックな読み取り保証が実現されます。 
* Bounded Staleness では、Session または Eventual の一貫性よりも強力な一貫性が保証されます。 グローバルに分散されるアプリケーションについては、強力な一貫性だけでなく、99.99% の可用性と短い待機時間も求められるシナリオで Bounded Staleness を使用することをお勧めします。 
* Bounded Staleness 一貫性が構成された Azure Cosmos DB アカウントでは、任意の数の Azure リージョンをその Azure Cosmos DB アカウントと関連付けることができます。 
* "有界整合性制約" を使用した場合の (消費される RU に関する) 読み取り操作のコストは "セッション" 整合性や "最終的" 整合性よりも高いものの、"厳密な" 整合性と同じです。

**Session**: 

* Strong と Bounded Staleness の一貫性レベルで得られるグローバルな一貫性モデルとは異なり、Session 一貫性はクライアント セッションを対象とします。 
* Session 一貫性は、デバイスまたはユーザーのセッションが関係するすべてのシナリオに最適です。これは、モノトニックな読み取り、モノトニックな書き込み、自己の書き込みの読み取り (RYW) が保証されるためです。 
* Session 一貫性では、セッションの予測可能な一貫性が確保されます。また、書き込みと読み取りの待機時間が最も短い一方で、読み取りスループットは最大です。 
* Session 一貫性が構成された Azure Cosmos DB アカウントでは、任意の数の Azure リージョンをその Azure Cosmos DB アカウントと関連付けることができます。 
* Session 一貫性レベルを使用した場合の (消費される RU に関する) 読み取り操作のコストは Strong と Bounded Staleness より低く、Eventual 一貫性より高くなります。

<a id="consistent-prefix"></a>
**一貫性のあるプレフィックス**: 

* 一貫性のあるプレフィックスでは、長期間書き込みがない場合にグループ内のレプリカがやがて収束することが保証されます。 
* また、読み取りでは決して順不同の書き込みが確認されないことが保証されます。 書き込みが `A, B, C` の順で実行された場合、クライアントでは `A`、`A,B`、または `A,B,C` が確認されますが、`A,C` または `B,A,C` などの順不同が確認されることはありません。
* 一貫性のあるプレフィックスが構成された Azure Cosmos DB アカウントでは、任意の数の Azure リージョンをその Azure Cosmos DB アカウントと関連付けることができます。 

**Eventual**: 

* Eventual 一貫性では、長期間書き込みがない場合にグループ内のレプリカがやがて収束することが保証されます。 
* Eventual 一貫性は最も弱い形態の一貫性であり、クライアントは、過去に認識した値よりも古い値を取得する可能性があります。
* Eventual の一貫性は、読み取りの一貫性という点は最も弱いですが、読み取りと書き込みの待機時間は最短となります。
* Eventual 一貫性が構成された Azure Cosmos DB アカウントでは、任意の数の Azure リージョンをその Azure Cosmos DB アカウントと関連付けることができます。 
* Eventual 一貫性レベルを使用した場合の (消費される RU に関する) 読み取り操作のコストは、すべての Azure Cosmos DB 一貫性レベルの中で最も低くなります。

## <a name="configuring-the-default-consistency-level"></a>既定の一貫性レベルの構成
1. [Azure Portal](https://portal.azure.com/) で、ジャンプバーの **[Azure Cosmos DB]** をクリックします。
2. **[Azure Cosmos DB]** ブレードで、変更するデータベース アカウントを選択します。
3. アカウントのブレードで、 **[既定の整合性]**をクリックします。
4. **[既定の一貫性]** ブレードで、新しい一貫性レベルを選択し、**[保存]** をクリックします。
   
    ![[設定] アイコンと [既定の整合性] エントリが強調表示されたスクリーン ショット](./media/consistency-levels/database-consistency-level-1.png)

## <a name="consistency-levels-for-queries"></a>クエリの一貫性レベル
既定では、ユーザー定義リソースに関するクエリの一貫性レベルは、読み取りの一貫性レベルと同じです。 既定では、Cosmos DB コンテナーに対するアイテムの挿入、置換、削除のたびに、インデックスが同期的に更新されます。 これにより、クエリには、アイテムの読み取りと同じ一貫性レベルが保証されます。 Azure Cosmos DB では書き込みが最適化されており、持続的書き込み、同期インデックス メンテナンス、クエリの一貫性の確保がサポートされていますが、特定のコレクションのインデックスが遅れて更新されるように構成することもできます。 インデックスの遅延作成は、書き込みのパフォーマンスを飛躍的に高める効果があり、ワークロードで主に読み取りの負荷が激しい状況での一括インジェストに最適です。  

| インデックス作成モード | 読み取り | クエリ |
| --- | --- | --- |
| 一貫性 (既定) |Strong、Bounded Staleness、Session、Consistent Prefix、Eventual の中から選択 |Strong、Bounded Staleness、Session、Eventual の中から選択 |
| 遅延 |Strong、Bounded Staleness、Session、Consistent Prefix、Eventual の中から選択 |Eventual |
| なし |Strong、Bounded Staleness、Session、Consistent Prefix、Eventual の中から選択 |適用不可 |

読み取り要求と同様に、各 API の特定のクエリ要求の一貫性レベルを引き下げることができます。

## <a name="next-steps"></a>次のステップ
一貫性レベルとトレードオフの詳細については、以下の資料を参照してください。

* Doug Terry 著: Replicated Data Consistency Explained Through Baseball (レプリケート データの一貫性を野球にたとえると) (ビデオ)   
  [https://www.youtube.com/watch?v=gluIh8zd26I](https://www.youtube.com/watch?v=gluIh8zd26I)
* Doug Terry 著: Replicated Data Consistency Explained Through Baseball (レプリケート データの一貫性を野球にたとえると)   
  [http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf)
* Doug Terry 著: Session Guarantees for Weakly Consistent Replicated Data (弱一貫性レプリケート データのためのセッション保証)   
  [http://dl.acm.org/citation.cfm?id=383631](http://dl.acm.org/citation.cfm?id=383631)
* Daniel Abadi 著: Consistency Tradeoffs in Modern Distributed Database Systems Design: CAP is only part of the story (先進的な分散データベース システム設計における一貫性のトレードオフ: CAP 以外の考慮事項について)   
  [http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html](http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html)
* Peter Bailis、Shivaram Venkataraman、Michael J. Franklin、Joseph M.   
  [http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
* Werner Vogels 著: Eventual Consistent - Revisited (結果的な一貫性 - 改訂版)    
  [http://allthingsdistributed.com/2008/12/eventually_consistent.html](http://allthingsdistributed.com/2008/12/eventually_consistent.html)
* Moni Naor、Avishai Wool 著: The Load, Capacity, and Availability of Quorum Systems (クォーラム システムの負荷、容量、および可用性), SIAM Journal on Computing, v.27 n.2, p.423-447, April 1998
  [http://epubs.siam.org/doi/abs/10.1137/S0097539795281232](http://epubs.siam.org/doi/abs/10.1137/S0097539795281232)
* Sebastian Burckhardt、Chris Dern、Macanal Musuvathi、Roy Tan 著: Line-up: a complete and automatic linearizability checker, (Line-up: 完全自動線形化可能性検査器), Proceedings of the 2010 ACM SIGPLAN conference on Programming language design and implementation, June 05-10, 2010, Toronto, Ontario, Canada [doi>10.1145/1806596.1806634] [http://dl.acm.org/citation.cfm?id=1806634](http://dl.acm.org/citation.cfm?id=1806634)
* Peter Bailis、Shivaram Venkataraman、Michael J. Franklin、Joseph M. Hellerstein、Ion Stoica 著: Probabilistically bounded staleness for practical partial quorums (現実的なパーシャル クォーラムのための Probabilistic Bounded Staleness), Proceedings of the VLDB Endowment, v.5 n.8, p.776-787, April 2012 [http://dl.acm.org/citation.cfm?id=2212359](http://dl.acm.org/citation.cfm?id=2212359)
