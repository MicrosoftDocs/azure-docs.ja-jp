---
title: Azure Cosmos コンテナー用のグローバルに分散されたトランザクション ストレージと分析ストレージ
description: Azure Cosmos コンテナー用のトランザクション ストレージと分析ストレージの概要と、その構成オプションについて説明します。
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.reviewer: sngun
ms.openlocfilehash: 27ca2102ee95273fbedd1a870e57d2ae3318e879
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703624"
---
# <a name="globally-distributed-transactional-and-analytical-storage-for-azure-cosmos-containers"></a>Azure Cosmos コンテナー用のグローバルに分散されたトランザクション ストレージと分析ストレージ

Azure Cosmos コンテナーは、2 つのストレージ エンジン (トランザクション ストレージ エンジンと更新可能な分析ストレージ エンジン) によって内部的にサポートされます。 どちらのストレージ エンジンも、迅速な更新のためにログ構造化および書き込み最適化がなされています。 ただし、それぞれのエンコード方法は異なります。

* **トランザクション ストレージ エンジン** – トランザクションの読み取りとクエリの高速化のために行指向形式でエンコードされています。

* **分析ストレージ エンジン** - 分析のクエリとスキャンの高速化のために列形式でエンコードされています。

![ストレージ エンジンと Azure Cosmos DB API のマッピング](./media/globally-distributed-transactional-analytical-storage/storage-engines-api-mapping.png)

トランザクション ストレージ エンジンはローカル SSD によってサポートされますが、分析ストレージは低コストのクラスター外 SSD ストレージに格納されます。 次の表は、トランザクション ストレージと分析ストレージの主な違いを示しています。


|機能  |トランザクション ストレージ  |分析ストレージ |
|---------|---------|---------|
|Azure Cosmos コンテナーあたりの最大ストレージ容量 |   無制限      |    無制限     |
|論理パーティション キーあたりの最大ストレージ容量   |   10 GB      |   無制限      |
|ストレージのエンコード  |   行指向 (内部形式を使用)。   |   列指向 (Apache Parquet 形式を使用)。 |
|ストレージの場所 |   ローカル/クラスター内 SSD によってサポートされるレプリケートされたストレージ。 |  低コストのリモート/クラスター外 SSD によってサポートされるレプリケートされたストレージ。       |
|持続性  |    99.99999 (7 から 9 秒)     |  99.99999 (7 から 9 秒)       |
|データにアクセスする API  |   SQL、MongoDB、Cassandra、Gremlin、Tables、Etcd。       | Apache Spark         |
|リテンション期間 (有効期限 (TTL))   |  ポリシーに基づき、`DefaultTimeToLive` プロパティを使用して Azure Cosmos コンテナー上に構成されます。       |   ポリシーに基づき、`ColumnStoreTimeToLive` プロパティを使用して Azure Cosmos コンテナー上に構成されます。      |
|GB あたりの料金    |   $0.25/GB      |  $0.02/GB       |
|ストレージ トランザクションの料金    | プロビジョニングされたスループットは、100 RU/秒あたり $0.008 で 1 時間ごとに課金されます。        |  使用量ベースのスループットは、1 万書き込みトランザクションにつき $0.05、1 万読み取りトランザクションにつき $0.004 で課金されます。       |

## <a name="benefits-of-transactional-and-analytical-storage"></a>トランザクション ストレージと分析ストレージの利点

### <a name="no-etl-operations"></a>ETL 操作が不要

従来の分析パイプラインは、コンピューティング層とストレージ層の間の ETL (抽出、変換、読み込み) 操作を必要とするステージが複数あり、複雑になっています。 そのため、データ処理アーキテクチャが複雑になります。 これは、ストレージとコンピューティングのステージが複数あることによってコストが上昇し、ストレージとコンピューティングのさまざまなステージ間で大量のデータが転送されるために待機時間が長くなることを意味します。  

Azure Cosmos DB では、ETL 操作を実行するオーバーヘッドがありません。 各 Azure Cosmos コンテナーは、トランザクション ストレージ エンジンと分析ストレージ エンジンの両方によってサポートされており、トランザクション ストレージ エンジンと分析ストレージ エンジン間のデータ転送はデータベース エンジン内で行われるため、ネットワーク ホップがありません。 そのため、従来の分析ソリューションと比べて、待機時間とコストが大幅に削減されます。 また、トランザクション ワークロードと分析ワークロード用に、グローバルに分散された単一のストレージ システムを利用できます。  

### <a name="store-multiple-versions-update-and-query-the-analytical-storage"></a>分析ストレージの複数バージョンを保存、更新、クエリを実行

分析ストレージは完全に更新可能であり、Azure Cosmos コンテナー上で発生したすべてのトランザクション更新の完全なバージョン履歴が保持されます。

トランザクション ストレージに対して行われたすべての更新は、必ず 30 秒以内に分析ストレージから見えるようになります。 

### <a name="globally-distributed-multi-master-analytical-storage"></a>グローバルに分散されたマルチマスター分析ストレージ

Azure Cosmos アカウントが単一のリージョンにスコープ指定されている場合、コンテナー内の (トランザクション ストレージと分析ストレージに) 格納されているデータも単一のリージョンにスコープ指定されます。 一方、Azure Cosmos アカウントがグローバルに分散されている場合は、コンテナーに格納されているデータもグローバルに分散されます。

複数の書き込みリージョンで構成された Azure Cosmos アカウントの場合、コンテナー内の (トランザクション ストレージと分析ストレージの両方) への書き込みは、常にローカル リージョンで実行されるため高速になります。

単一または複数リージョンの Azure Cosmos アカウントの場合、単一の書き込みリージョン (シングルマスター) か複数の書き込みリージョン (マルチマスター) かに関係なく、トランザクションと分析の両方の読み取り/クエリが、指定されたリージョンでローカルに実行されます。

### <a name="performance-isolation-between-transactional-and-analytical-workloads"></a>トランザクション ワークロードと分析ワークロード間のパフォーマンスの分離

指定されたリージョンでは、トランザクション ワークロードはコンテナーのトランザクション/行ストレージに対して動作します。 一方、分析ワークロードは、コンテナーの分析/列ストレージに対して動作します。 2 つのストレージ エンジンは独立して動作し、ワークロード間のパフォーマンスは厳密に分離されます。

トランザクション ワークロードでは、プロビジョニングされたスループット (RU) が消費されます。 トランザクション ワークロードとは異なり、分析ワークロードのスループットは実際の消費量に基づきます。 分析ワークロードでは、オンデマンドでリソースが消費されます。

### <a name="on-demand-snapshots-and-time-travel-analytics"></a>オンデマンドのスナップショットとタイムトラベル分析

コンテナーで `CreateSnapshot (name, timestamp)` コマンドを呼び出すことによって、Azure Cosmos コンテナーの分析ストレージに格納されているデータのスナップショットをいつでも取得できます。 スナップショットは、コンテナー上で行われた更新の履歴では "ブックマーク" という名前が付けられます。

![オンデマンドのスナップショットとタイムトラベル分析](./media/globally-distributed-transactional-analytical-storage/ondemand-analytical-data-snapshots.png)

スナップショットの作成時に、名前だけでなく、更新履歴内のコンテナーの状態を定義するタイムスタンプを指定することもできます。 その後、スナップショット データを Spark に読み込み、クエリを実行できます。

現時点では、コンテナーでいつでもオンデマンドでスナップショットを取得できますが、スケジュールまたはカスタム ポリシーに基づいて自動的にスナップショットを取得する機能はまだサポートされていません。

### <a name="configure-and-tier-data-between-transactional-and-analytical-storage-independently"></a>トランザクション ストレージと分析ストレージ間で個別にデータを構成および階層化する

使用するシナリオに応じて、2 つのストレージ エンジンをそれぞれ個別に有効または無効にできます。 各シナリオの構成は次のとおりです。

|シナリオ |トランザクション ストレージの設定  |分析ストレージの設定 |
|---------|---------|---------|
|分析ワークロードを排他的に実行する (無限のリテンション期間) |  DefaultTimeToLive = 0       |  ColumnStoreTimeToLive = -1       |
|トランザクション ワークロードを排他的に実行する (無限のリテンション期間)  |   DefaultTimeToLive = -1      |  ColumnStoreTimeToLive = 0       |
|トランザクション ワークロードと分析ワークロードの両方を実行する (無限のリテンション期間)   |   DefaultTimeToLive = -1      | ColumnStoreTimeToLive = -1        |
|トランザクション ワークロードと分析ワークロードの両方を実行する (異なるリテンション期間を使用 (ストレージの階層化とも呼ばれます))  |  DefaultTimeToLive = <Value1>       |     ColumnStoreTimeToLive = <Value2>    |

1. **分析ワークロード専用にコンテナーを構成する (無制限のリテンション期間)**

   Azure Cosmos コンテナーは、分析ワークロード専用に構成できます。 この構成には、トランザクション ストレージの料金を支払う必要がないという利点があります。 分析ワークロード専用のコンテナーを使用することを目標としている場合は、Cosmos コンテナーで `DefaultTimeToLive` を 0 に設定してトランザクション ストレージを無効にし、`ColumnStoreTimeToLive` を -1 に設定して無限のリテンション期間を持つ分析ストレージを有効にできます。

   ![無限のリテンション期間を持つ分析ワークロード](./media/globally-distributed-transactional-analytical-storage/analytical-workload-configuration.png)

1. **トランザクション ワークロード専用にコンテナーを構成する (無限のリテンション期間)**

   Azure Cosmos コンテナーは、トランザクション ワークロード専用に構成できます。 コンテナーで `ColumnStoreTimeToLive` を 0 に設定して分析ストレージを無効にし、`DefaultTimeToLive` を -1 に設定して無限のリテンション期間を持つ分析ストレージを有効にできます。

   ![無期限のリテンション期間を持つトランザクション ワークロード](./media/globally-distributed-transactional-analytical-storage/transactional-workload-configuration.png)

1. **トランザクション ワークロードと分析ワークロード用にコンテナーを構成する (無限のリテンション期間)**

   Azure Cosmos コンテナーは、トランザクション ワークロードと分析ワークロード用に構成できます。その際、両者のパフォーマンスを完全に分離することができます。 `ColumnStoreTimeToLive` を -1 に設定して分析ストレージを有効にし、`DefaultTimeToLive ` を -1 に設定して無限のリテンション期間を持つトランザクション ストレージを有効にできます。

   ![無限のリテンション期間を持つトランザクション ワークロードと分析ワークロード](./media/globally-distributed-transactional-analytical-storage/analytical-transactional-configuration-infinite-retention.png)

1. **ストレージの階層化を使用してトランザクション ワークロードと分析ワークロード用のコンテナーを構成する**

   Azure Cosmos コンテナーは、トランザクション ワークロードと分析ワークロード用に構成できます。その際、両者のパフォーマンスを完全に分離し、異なるリテンション期間を持たせることができます。 Azure Cosmos DB では、常に分析ストレージのリテンション期間がトランザクション ストレージのリテンション期間よりも長くなります。

   `DefaultTimeToLive` を <Value 1> に設定して無限のリテンション期間を持つトランザクション ストレージを有効にし、`ColumnStoreTimeToLive` を <Value 2> に設定して分析ストレージを有効にできます。 Azure Cosmos DB では、常に <Value 2> が <Value 1> よりも大きくなります。

   ![ストレージの階層化を使用したトランザクション ワークロードと分析ワークロード](./media/globally-distributed-transactional-analytical-storage/analytical-transactional-configuration-specified-retention.png)

## <a name="next-steps"></a>次の手順

* [Azure Cosmos DB の Time to Live](time-to-live.md)
