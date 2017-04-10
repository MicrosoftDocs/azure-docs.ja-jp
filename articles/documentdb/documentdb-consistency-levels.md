---
title: "Azure DocumentDB の一貫性レベル | Microsoft Docs"
description: "DocumentDB には、結果的な一貫性、可用性、待機時間のトレードオフを調整できる 4 つの一貫性レベルがあります。"
keywords: "eventual 一貫性, documentdb, azure, Microsoft azure"
services: documentdb
author: syamkmsft
manager: jhubbard
editor: cgronlun
documentationcenter: 
ms.assetid: 3fe51cfa-a889-4a4a-b320-16bf871fe74c
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: syamk
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: af8e53d7cc074bf669b967014223ee88476d1686
ms.lasthandoff: 03/29/2017


---
# <a name="tunable-data-consistency-levels-in-documentdb"></a>DocumentDB の調整可能なデータの整合性レベル
Azure DocumentDB は、最初からグローバル分散を念頭に置いて設計されています。 その設計により、予測可能な待機時間の短縮の保証、可用性が 99.99% の SLA、明確に定義された複数の緩やかな一貫性モデルが提供されます。 DocumentDB では現在、Strong、Bounded-Staleness、Session、Eventual の 4 つの一貫性レベルが用意されています。 その他の NoSQL データベースが一般的に提供している **Strong** と **Eventual の一貫性**モデルに加え、DocumentDB では、注意深く体系化、運用化された一貫性モデルがさらに 2 つ提供されます (**Bounded Staleness** モデルと **Session** モデル)。現実のユース ケースに対するその有用性は DocumentDB によって検証されています。 これら 4 つの一貫性レベルが揃っているため、一貫性、可用性、待機時間の最適なトレードオフを根拠を持って検討することができます。 

## <a name="scope-of-consistency"></a>一貫性のスコープ
一貫性の粒度の対象は、単一のユーザー要求です。 書き込み要求は、挿入、置換、アップサート、または削除のトランザクション (関連付けられたプリトリガーまたはポストトリガーの実行を伴うもの、伴わないもの) に対応している場合があります。 また、パーティション内の複数のドキュメントに対して動作する JavaScript ストアド プロシージャのトランザクション実行に対応している場合もあります。 書き込みと同様に、読み取り/クエリ トランザクションの対象も単一のユーザー要求です。 ユーザーは複数のパーティションにまたがって大規模な結果セットを読み込む必要がありますが、各読み取りトランザクションは単一のページを対象とし、単一のパーティション内から提供されます。

## <a name="consistency-levels"></a>一貫性レベル
データベース アカウントには既定の一貫性レベルを構成できます。既定の一貫性レベルは、対象となるデータベース アカウント下のすべての (全データベースの) コレクションに適用されます。 既定では、ユーザー定義リソースに対して発行されたすべての読み取りとクエリに、データベース アカウントに指定された既定の一貫性レベルが使用されます。 ただし、[[x-ms-consistency-level]](https://msdn.microsoft.com/library/azure/mt632096.aspx) という要求ヘッダーを指定すれば、特定の読み取り/クエリ要求の一貫性レベルを緩和することもできます。 次に示すように、DocumentDB のレプリケーション プロトコルによって 4 種類の一貫性レベルがサポートされており、特定の一貫性の保証とパフォーマンスの間の明確なトレードオフを実現しています。

![DocumentDB offers multiple, well defined (relaxed) consistency models to choose from][1]

**Strong**: 

* Strong 一貫性では、[線形化可能性](https://aphyr.com/posts/313-strong-consistency-models)が保証されます。ここでは読み取りに対して最新バージョンのドキュメントを返すことが保証されています。 
* レプリカのマジョリティ クォーラムによってコミットされた後でのみ、書き込み結果が見える状態になることが保証されます。 書き込みは、プライマリとセカンダリのクォーラムとの両方によって同期的かつ永続的にコミットされるか、または中止されるか、のどちらかとなります。 読み取りは常に、マジョリティ リード クォーラムによって確認応答されます。未コミットの書き込みや部分的な書き込みがクライアントから見えることはありません。読み取りの内容は常に、確認応答が返された最新の書き込み結果であることが保証されます。 
* Strong 一貫性を使用するように構成された DocumentDB アカウントでは、複数の Azure リージョンをその DocumentDB アカウントに関連付けることができません。 
* "厳密な" 整合性を使用した場合の (消費される [要求ユニット](documentdb-request-units.md) に関する) 読み取り操作のコストは "セッション" 整合性や "最終的" 整合性よりも高いものの、有界整合性制約と同じです。

**Bounded Staleness**: 

* Bounded Staleness 一貫性では、最大でドキュメントの *K* 個のバージョンまたはプレフィックスあるいは期間 *t* の分だけ、読み取りが書き込みに対し遅れることが保証されます。 
* このため、Bounded Staleness を選択する場合、"staleness" は 2 つの方法で構成できます。 
  * ドキュメントのバージョンの数 *K* (書き込みに対する読み取りの遅れ)
  * 期間 *t* 
* Bounded Staleness では、"staleness 期間" 内を除いてトータルなグローバル順序が実現されます。 リージョン内では、"staleness 期間" 内外の両方でモノトニックな読み取り保証が実現されることに注意してください。 
* Bounded Staleness では、Session または Eventual の一貫性よりも強力な一貫性が保証されます。 グローバルに分散されるアプリケーションについては、強力な一貫性だけでなく、99.99% の可用性と短い待機時間も求められるシナリオで Bounded Staleness を使用することをお勧めします。 
* Bounded Staleness 一貫性が構成された DocumentDB アカウントでは、任意の数の Azure リージョンをその DocumentDB アカウントと関連付けることができます。 
* "有界整合性制約" を使用した場合の (消費される RU に関する) 読み取り操作のコストは "セッション" 整合性や "最終的" 整合性よりも高いものの、"厳密な" 整合性と同じです。

**Session**: 

* Strong と Bounded Staleness の一貫性レベルで得られるグローバルな一貫性モデルとは異なり、Session 一貫性はクライアント セッションを対象とします。 
* Session 一貫性は、デバイスまたはユーザーのセッションが関係するすべてのシナリオに最適です。これは、モノトニックな読み取り、モノトニックな書き込み、自己の書き込みの読み取り (RYW) が保証されるためです。 
* Session 一貫性では、セッションの予測可能な一貫性が確保されます。また、書き込みと読み取りの待機時間が最も短い一方で、読み取りスループットは最大です。 
* Session 一貫性が構成された DocumentDB アカウントでは、任意の数の Azure リージョンをその DocumentDB アカウントと関連付けることができます。 
* Session 一貫性レベルを使用した場合の (消費される RU に関する) 読み取り操作のコストは Strong と Bounded Staleness より低く、Eventual 一貫性より高くなります。

**Eventual**: 

* Eventual 一貫性では、長期間書き込みがない場合にグループ内のレプリカがやがて収束することが保証されます。 
* Eventual 一貫性は最も弱い形態の一貫性であり、クライアントは、過去に認識した値よりも古い値を取得する可能性があります。
* Eventual の一貫性は、読み取りの一貫性という点は最も弱いですが、読み取りと書き込みの待機時間は最短となります。
* Eventual 一貫性が構成された DocumentDB アカウントでは、任意の数の Azure リージョンをその DocumentDB アカウントと関連付けることができます。 
* Eventual 一貫性レベルを使用した場合の (消費される RU に関する) 読み取り操作のコストは、すべての DocumentDB 一貫性レベルの中で最も低くなります。

## <a name="consistency-guarantees"></a>一貫性の保証
次の表では、4 つの一貫性レベルに対応するさまざまな一貫性の保証について説明しています。

| 保証 | Strong | Bounded Staleness | Session | Eventual |
| --- | --- | --- | --- | --- |
| **トータルなグローバル順序** |はい |はい ("staleness 期間" 外) |いいえ (部分的な "セッションの" 順序) |なし |
| **一貫性のあるプレフィックスの保証** |はい |あり |あり |はい |
| **モノトニックな読み取り** |あり |はい (リージョン間は staleness 期間外のみ、リージョン内は常時) |いいえ (特定のセッションの間) |なし |
| **モノトニックな書き込み** |はい |あり |あり |はい |
| **自身の書き込みの読み取り** |はい |はい (書き込みリージョン内) | はい |なし |

## <a name="configuring-the-default-consistency-level"></a>既定の一貫性レベルの構成
1. [Azure ポータル](https://portal.azure.com/)で、ジャンプバーの **[DocumentDB (NoSQL)]**をクリックします。
2. **[DocumentDB (NoSQL)]** ブレードで、変更するデータベース アカウントを選択します。
3. アカウントのブレードで、 **[既定の整合性]**をクリックします。
4. **[既定の一貫性]** ブレードで、新しい一貫性レベルを選択し、**[保存]** をクリックします。
   
    ![[設定] アイコンと [既定の整合性] エントリが強調表示されたスクリーン ショット](./media/documentdb-consistency-levels/database-consistency-level-1.png)

> [!NOTE]
> 既定の一貫性レベルの構成は、[Azure DocumentDB Emulator](documentdb-nosql-local-emulator.md) ではサポートされていません。 

## <a name="consistency-levels-for-queries"></a>クエリの一貫性レベル
既定では、ユーザー定義リソースに関するクエリの一貫性レベルは、読み取りの一貫性レベルと同じです。 既定では、コレクションに対するドキュメントの挿入、置換、削除のたびに、インデックスが同期的に更新されます。 これにより、クエリには、ドキュメントの読み取りと同じ一貫性レベルが保証されます。 DocumentDB では書き込みが最適化されており、ドキュメントの持続的書き込み、同期インデックス メンテナンス、クエリの一貫性の確保がサポートされていますが、特定のコレクションのインデックスが遅れて更新されるように構成することもできます。 インデックスの遅延作成は、書き込みのパフォーマンスを飛躍的に高める効果があり、ワークロードで主に読み取りの負荷が激しい状況での一括インジェストに最適です。  

| インデックス作成モード | 読み取り | クエリ |
| --- | --- | --- |
| 一貫性 (既定) |Strong、Bounded Staleness、Session、Eventual の中から選択 |Strong、Bounded Staleness、Session、Eventual の中から選択 |
| 遅延 |Strong、Bounded Staleness、Session、Eventual の中から選択 |Eventual |
| なし |Strong、Bounded Staleness、Session、Eventual の中から選択 |適用不可 |

読み取り要求と同様に、 [x-ms-consistency-level](https://msdn.microsoft.com/library/azure/mt632096.aspx) という要求ヘッダーを指定することによって、特定のクエリ要求の整合性レベルを引き下げることができます。

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

[1]: ./media/documentdb-consistency-levels/consistency-tradeoffs.png

