---
title: Azure Cosmos DB の整合性レベル
description: Azure Cosmos DB には、結果的な一貫性、可用性、待機時間のトレードオフを調整できる 5 つの一貫性レベルがあります。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: 836d36cc6f220bb544e0c7723506c624c5f9fc39
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407302"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Azure Cosmos DB の整合性レベル

分散型データベースでは高可用性もしくは待機時間の短縮、またはその両方がレプリケーションに依存するため、読み取りの整合性、可用性、待機時間、スループットとの間に基本的なトレードオフが生じます。 市販のほとんどの分散型データベースでは、開発者は 2 つの極端な整合性モデル (*厳密な*整合性と*最終的な*整合性) のどちらかを選ぶことを求められます。  [線形化可能性](https://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf)、つまり厳密な整合性モデルは、データ プログラミングの標準基準です。 しかし、(安定状態での) 待機時間が長く、(障害発生時の) 可用性が低いという代償があります。 その一方で、最終的な整合性は、可用性が高くパフォーマンスに優れていますが、アプリケーションのプログラミングが難しくなります。 

Azure Cosmos DB では、2 つの両極端ではなく、幅広い選択肢を利用できるデータ整合性に対応しています。 厳密な整合性と最終的な整合性は、整合性の選択範囲の両端にありますが、その間には多くの整合性の選択肢があります。 開発者は、これらの整合性オプションを使用して、最適な選択を行い、高可用性とパフォーマンスに関して詳細なトレードオフを決定できます。 

Azure Cosmos DB では、開発者は、整合性の程度に応じて明確に定義された 5 種類の整合性モデルから選択することができます。 最も厳密なものからより緩やかなものに向かって、*厳密*、*有界整合性制約*、*セッション*、*一貫性のあるプレフィックス*、*最終的*の各整合性モデルがあります。 これらのモデルは適切に定義されていて直感的であり、具体的な実際のシナリオに使用することができます。 どのモデルにも[可用性とパフォーマンスのトレードオフ](consistency-levels-tradeoffs.md)があり、それぞれ SLA によって支えられています。 次の図では、さまざまな整合性レベルの範囲内での位置づけを示します。

![範囲としての整合性](./media/consistency-levels/five-consistency-levels.png)

読み取りと書き込みが行われるリージョン、Azure Cosmos アカウントに関連付けられているリージョン数、またはアカウントの構成に使用されている書き込みリージョンが 1 つか複数かには関係なく、Azure Cosmos アカウントの整合性レベルはリージョンに依存せず、すべての操作で保証されています。

## <a name="scope-of-the-read-consistency"></a>読み取り整合性のスコープ

読み取り整合性は、パーティションキー範囲または論理パーティション内をスコープとする 1 つの読み取り操作に適用されます。 読み取り操作はリモート クライアントまたはストアド プロシージャが発行できます。

## <a name="configure-the-default-consistency-level"></a>既定の一貫性レベルを構成する

Azure Cosmos アカウントの既定の整合性レベルはいつでも構成できます。 自分のアカウントに構成されている既定の整合性レベルは、そのアカウントのすべての Azure Cosmos データベースおよびコンテナーに適用されます。 コンテナーまたはデータベースに対して発行されるすべての読み取りとクエリでは、指定された整合性レベルが既定で使用されます。 詳しくは、[既定の一貫性レベルを構成する](how-to-manage-consistency.md#configure-the-default-consistency-level)方法をご覧ください。

## <a name="guarantees-associated-with-consistency-levels"></a>整合性レベルに関連付けられた保証

Azure Cosmos DB によって提供される包括的 SLA では、読み取り要求の 100 パーセントが、選択した任意の整合性レベルについて整合性の保証を満たすことが保証されます。 整合性レベルに関連付けられている整合性の保証がすべて満たされていれば、読み取り要求は整合性の SLA を満たします。 [TLA+ 仕様言語](https://lamport.azurewebsites.net/tla/tla.html)を使用した Azure Cosmos DB での 5 つの整合性レベルの正確な定義は、[azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla) GitHub リポジトリで提供されています。 

5 つの整合性レベルのセマンティクスを以下で説明します。

- **Strong**:厳密な整合性では、[線形化可能性](https://aphyr.com/posts/313-strong-consistency-models)の保証が提供されます。 読み取りでは、アイテムの最後にコミットされたバージョンが返ることが保証されています。 クライアントが、コミットされていない書き込みや部分的な書き込みを見ることは決してありません。 ユーザーが最新のコミット済みの書き込みを読み取ることが常に保証されます。

- **Bounded Staleness**:読み取りでは、整合性のあるプレフィックスの優先が保証されます。 読み取りが書き込みよりも遅れますが、最大でアイテムの *"K"* 個のバージョン (更新) の分、あるいは期間 *"T"* の分だけです。 つまり、有界整合性制約を選択する場合、"整合性制約" は 2 つの方法で構成できます。 

  * アイテムのバージョンの数 (*K*)
  * 書き込みに対して読み取りが遅れる期間 (*T*) 

  有界整合性制約では、"整合性制約期間" 内を除いてトータルなグローバル順序が実現されます。 リージョン内では、整合性制約期間内外の両方でモノトニックな読み取り保証が実現されます。 厳密な整合性にも、有界整合性制約で提供されるのと同じセマンティクスがあります。 整合性制約期間は 0 です。 有界整合性制約は、遅延線形化可能性とも呼ばれます。 書き込みを受け入れるリージョン内でクライアントが読み取り操作を実行するとき、有界整合性制約整合性で提供される保証は、厳密な整合性の場合と同じです。

- **Session**:読み取りでは、整合性のあるプレフィックス (単一 "ライター" セッションの場合)、単調読み取り、単調書き込み、自己書き込みの読み取り、読み取り後の書き込みの優先が保証されます。 セッション整合性のスコープはクライアント セッションです。

- **整合性のあるプレフィックス**:返される更新には、それ以外の全更新の一部のプレフィックスが含まれます (ギャップなし)。 一貫性のあるプレフィックスの一貫性レベルでは、読み取りの際、書き込みを順序どおりに参照することが保証されます。

- **Eventual**:読み取りの順序の保証はありません。 さらに書き込みがない場合、レプリカが最終的に収束します。

## <a name="consistency-levels-explained-through-baseball"></a>野球で説明する整合性レベル

例として野球の試合のシナリオを見てみましょう。 野球の試合の点数を表す一連の書き込みを想像してみてください。 イニングごとのスコアは、「[Replicated data consistency through baseball](https://www.microsoft.com/en-us/research/wp-content/uploads/2011/10/ConsistencyAndBaseballReport.pdf)」(レプリケート データの整合性を野球にたとえると) という論文で説明されています。 この仮定の野球の試合は、現在 7 回の表と裏の間です。 いわゆるセブンイニング ストレッチです。 以下に示すように、ビジターが 2 対 5 でリードされています。

| | **1** | **2** | **3** | **4** | **5** | **6** | **7** | **8** | **9** | **計** |
| - | - | - | - | - | - | - | - | - | - | - |
| **ビジター** | 0 | 0 | 1 | 0 | 1 | 0 | 0 |  |  | 2 |
| **ホーム** | 1 | 0 | 1 | 1 | 0 | 2 |  |  |  | 5 |

Azure Cosmos コンテナーに、ビジター チームとホーム チームの合計得点が格納されます。 試合の進行中に、さまざまな読み取り保証を使用すると、クライアントは異なる点数を読み取ることになります。 次の表に、5 つの整合性保証それぞれを使用してビジターとホームの点数を読み取ったときに、返される可能性がある点数の一覧を示します。 最初に表示されているのはビジターの点数です。 さまざまな戻り値がコンマで区切って示されています。

| **整合性レベル** | **スコア (ビジター、ホーム)** |
| - | - |
| **厳密** | 2-5 |
| **有界整合性制約** | 最大で 1 イニング遅れの点数:2-3、2-4、2-5 |
| **セッション** | <ul><li>書き込み側:2-5</li><li> 書き込み側以外:0-0、0-1、0-2、0-3、0-4、0-5、1-0、1-1、1-2、1-3、1-4、1-5、2-0、2-1、2-2、2-3、2-4、2-5</li><li>1-3 を読み取った後:1-3、1-4、1-5、2-3、2-4、2-5</li> |
| **一貫性のあるプレフィックス** | 0-0、0-1、1-1、1-2、1-3、2-3、2-4、2-5 |
| **最終的** | 0-0、0-1、0-2、0-3、0-4、0-5、1-0、1-1、1-2、1-3、1-4、1-5、2-0、2-1、2-2、2-3、2-4、2-5 |

## <a name="additional-reading"></a>その他の情報

整合性の概念について詳しくは、以下の記事をご覧ください。

- 「[High-level TLA+ specifications for the five consistency levels offered by Azure Cosmos DB (Azure Cosmos DB によって提供される 5 つの整合性レベルのための高レベル TLA+ 仕様)](https://github.com/Azure/azure-cosmos-tla)」
- [レプリケート データの整合性を野球にたとえると (Doug Terry によるビデオ)](https://www.youtube.com/watch?v=gluIh8zd26I)
- [レプリケート データの整合性を野球にたとえると (Doug Terry によるホワイトペーパー)](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [弱一貫性レプリケート データのためのセッション保証](https://dl.acm.org/citation.cfm?id=383631)
- [先進的な分散データベース システム設計における整合性のトレードオフ:CAP 以外の考慮事項について](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- 「[Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums (現実的なパーシャル クォーラムのための Probabilistic Bounded Staleness (PBS))](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)」
- 「[Eventually Consistent - Revisited (最終的な整合性 - 改訂版)](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)」

## <a name="next-steps"></a>次の手順

Azure Cosmos DB の整合性レベルの概念の詳細については、以下の記事をご覧ください。

* [アプリケーションのための適切な整合性レベルを選択する](consistency-levels-choosing.md)
* [Azure Cosmos DB API における整合性レベル](consistency-levels-across-apis.md)
* [さまざまな整合性レベルでの可用性およびパフォーマンスのトレードオフ](consistency-levels-tradeoffs.md)
* [既定の一貫性レベルを構成する](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [既定の一貫性レベルをオーバーライドする](how-to-manage-consistency.md#override-the-default-consistency-level)

