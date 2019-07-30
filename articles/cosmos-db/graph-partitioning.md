---
title: Azure Cosmos DB Gremlin API でのデータのパーティション分割
description: Azure Cosmos DB でパーティション分割されたグラフを使用する方法について説明します。 この記事では、パーティション分割されたグラフの要件とベスト プラクティスについても説明します。
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 4c8761d82c8a735ac9c4bff2e5ac0107b2a57fe0
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537542"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Azure Cosmos DB でのパーティション分割されたグラフの使用

Azure Cosmos DB の Gremlin API の主な特長の 1 つとして、水平スケーリングによって大規模なグラフを処理する機能があります。 コンテナーは、ストレージとスループットに応じて個別にスケーリングできます。 グラフ データを格納するために自動的にスケーリングできるコンテナーを Azure Cosmos DB で作成できます。 データは、指定された**パーティション キー**に基づいて自動的に分散されます。

コンテナーに格納されるサイズが 10 GB を超えることが予想される場合、または 1 秒間に 10,000 を超える数の要求ユニット (RU) を割り当てたい場合は、**パーティション分割が必要です**。 [Azure Cosmos DB のパーティション分割メカニズム](partition-data.md)と同じ一般原則が、以下で説明するグラフ固有の最適化でも適用されます。

![グラフのパーティション分割](./media/graph-partitioning/graph-partitioning.png)

## <a name="graph-partitioning-mechanism"></a>グラフのパーティション分割メカニズム

以下のガイドラインでは、Azure Cosmos DB のパーティション分割戦略がどのように機能するかについて説明します。

- **頂点とエッジの両方が、JSON ドキュメントとして格納されます**。

- **頂点にはパーティション キーが必須です**。 このキーによって、ハッシュ アルゴリズムを通して頂点が格納されるパーティションが決定されます。 パーティション キーのプロパティ名は新しいコンテナーの作成時に定義され、`/partitioning-key-name` という形式になります。

- **エッジはソースの頂点と共に格納されます**。 つまり、頂点ごとに、そのパーティション キーによって、頂点がその送信エッジと共に格納される場所が定義されます。 この最適化は、グラフ クエリで `out()` カーディナリティを使用する際にクロス パーティション クエリを回避するために行われます。

- **エッジには、それらが指す頂点への参照が含まれます**。 すべてのエッジは、それらが指している頂点のパーティション キーおよび ID と共に格納されます。 この計算により、`out()` 方向のすべてのクエリは、ブラインド クロスパーティション クエリではなく、常にスコープが設定されたパーティション分割されたクエリになります。 

- **グラフ クエリではパーティション キーを指定する必要があります**。 Azure Cosmos DB で行方向のパーティション分割を最大限に活用するには、単一の頂点を選択するときに、可能な限り、パーティション キーを指定します。 パーティション分割されたグラフにおいて 1 つまたは複数の頂点を選択するためのクエリを次に示します。

    - `/id` および `/label` は、Gremlin API でのコンテナーに対するパーティション キーとしてサポートされていません。


    - ID によって頂点を選択してから、 **`.has()` ステップを使用してパーティション キー プロパティを指定する**: 
    
        ```java
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - **パーティション キー値と ID が含まれている組を指定**して頂点を選択する: 
    
        ```java
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - **パーティション キー値と ID の組の配列**を指定する:
    
        ```java
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - 一連の頂点とその ID を選択し、**パーティション キー値のリストを指定する**: 
    
        ```java
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

    - クエリの先頭で**パーティション戦略**を使用し、Gremlin クエリの残りの部分のスコープにパーティションを指定する: 
    
        ```java
        g.withStrategies(PartitionStrategy.build().partitionKey('partitionKey').readPartitions('partitionKey_value').create()).V()
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>パーティション分割されたグラフを使用する場合のベスト プラクティス

無制限のコンテナーでパーティション分割されたグラフを使用するときは、次のガイドラインを使用してパフォーマンスとスケーラビリティを確保します。

- **頂点に対してクエリを実行するときは常にパーティション キー値を指定します**。 パフォーマンスを実現するには、既知のパーティションから頂点を取得します。 エッジにはターゲット頂点への参照 ID とパーティション キーが含まれているので、以降のすべての隣接操作のスコープは常にパーティションになります。

- **エッジに対してクエリを実行する場合は、可能な限り、送信方向を使用します**。 前述のように、エッジは、送信方向のそのソース頂点と一緒に格納されます。 したがって、このパターンを念頭においてデータとクエリを設計すると、クロス パーティション クエリを使用する可能性が最小限に抑えられます。 これに対して、`in()` クエリは常にコストのかかるファンアウト クエリになります。

- **パーティション間でデータを均等に分散するパーティション キーを選択します**。 この決定は、ソリューションのデータ モデルに大きく依存します。 適切なパーティション キーの作成の詳細については、「[Azure Cosmos DB でのパーティション分割とスケーリング](partition-data.md)」を参照してください。

- **パーティションの境界内でデータを取得するようにクエリを最適化します**。 最適なパーティション分割方法を決める場合は、クエリ パターンに合わせます。 1 つのパーティションからデータを取得するクエリでは、最大限のパフォーマンスを実現できます。

## <a name="next-steps"></a>次の手順

次に、以下の記事をお読みください。

* [Azure Cosmos DB でのパーティション分割とスケーリング](partition-data.md)について説明します。
* [Gremlin API での Gremlin のサポート](gremlin-support.md)について説明します。
* [Gremlin API の概要](graph-introduction.md)について説明します。
