---
title: Azure HDInsight での大規模なストリーミング
description: スケーラブルな HDInsight クラスターでデータ ストリーミングを使用する方法。
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 97bc2e585ccce250dd83dc855c3eda5f958186a7
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484066"
---
# <a name="streaming-at-scale-in-hdinsight"></a>HDInsight での大規模なストリーミング

リアルタイム ビッグ データ ソリューションは、動いているデータに作用します。 通常、このデータは到着時に最も高い価値があります。 受信データ ストリームがその瞬間で処理できるよりも大きくなった場合は、リソースを制限する必要がある可能性があります。 または、オンデマンドでノードを追加することによって、HDInsight クラスターをスケール アップし、ストリーミング ソリューションに対応することができます。


ストリーミング アプリケーションでは、1 つまたは複数のデータ ソースが、有用な情報をすべて取りこぼすことなく速やかに取り込む必要があるイベント (場合により 1 秒あたり数百万個) を生成しています。 受信イベントは、[Apache Kafka](kafka/apache-kafka-introduction.md) や [Event Hubs](https://azure.microsoft.com/services/event-hubs/) などのサービスによって、*ストリーム バッファリング* (*イベント キュー*とも呼ばれる) を使用して処理されます。 イベントを収集したら、[Apache Storm](storm/apache-storm-overview.md) または [Apache Spark ストリーミング](spark/apache-spark-streaming-overview.md)などの*ストリーム処理*レイヤー内でリアルタイム分析システムを使用して、データを分析できます。 処理済みのデータは、[Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/) などの長期ストレージ システムに格納し、[Power BI](https://powerbi.microsoft.com)、Tableau、カスタム Web ページなどのビジネス インテリジェンス ダッシュ ボードにリアルタイムで表示できます。


![HDInsight ストリーミング パターン](./media/hdinsight-streaming-at-scale-overview/HDInsight-streaming-patterns.png)

## <a name="apache-kafka"></a>Apache Kafka

Apache Kafka は、高スループットで低待機時間のメッセージ キュー サービスであり、現在オープン ソース ソフトウェア (OSS) の Apache スイートの一部になりました。 Kafka はパブリッシュおよびサブスクライブ メッセージング モデルを使用して、分散され、レプリケートされたクラスターにパーティション分割されたデータのストリームを安全に保存します。 Kafka はスループットの増加に応じて線形的にスケーリングします。

詳細については、「[HDInsight での Apache Kafka の概要](kafka/apache-kafka-introduction.md)」を参照してください。

## <a name="apache-storm"></a>Apache Storm

Apache Storm は、Hadoop を使用して、リアルタイムでデータのストリームを処理するために最適化されたフォールト トレランスに優れたオープン ソースの分散型計算システムです。 受信イベントのデータの中核となる単位は、タプルであり、これは不変のキー/値ペアのセットです。 これらのタプルの無限のシーケンスは、スパウトから入力されるストリームを形成します。 スパウトは Kafka などのストリーミング データ ソースをラップし、タプルを出力します。 ストーム トポロジは、これらのストリームの変換のシーケンスです。

詳細については、「[Azure HDInsight での Apache Storm とは](storm/apache-storm-overview.md)」を参照してください。

## <a name="spark-streaming"></a>Spark Streaming

Spark Streaming は、Spark の拡張機能で、バッチ処理に使用する同じコードを再利用できます。 同じアプリケーションで、バッチと対話型の両方のクエリを組み合わせることができます。 Storm と異なり、Spark Streaming はステートフルな exactly-once 処理セマンティクスを提供します。 [Kafka Direct API](https://spark.apache.org/docs/latest/streaming-kafka-integration.html) と組み合わせて使用すると、すべての Kafka データが Spark Streaming によって正確に 1 回受信され、エンド ツー エンドの exactly-once 保証を実現できます。 Spark Streaming の長所の 1 つは、クラスター内で複数のノードが使用されている場合に、障害のあるノードを迅速に復旧するフォールト トレランス機能です。

詳細については、「[What is Apache Spark Streaming?](hdinsight-spark-streaming-overview.md)」(Apache Spark ストリーミングの概要) を参照してください。

## <a name="scaling-a-cluster"></a>クラスターのスケーリング

作成中にクラスター内のノード数を指定できますが、ワークロードに一致するようにクラスターを拡大、縮小できます。 すべての HDInsight クラスターで、[クラスター内のノード数を変更](hdinsight-administer-use-portal-linux.md#scale-clusters)できます。 すべてのデータが Azure Storage または Data Lake Storage に格納されるため、Spark クラスターはデータの損失なく削除できます。

分離テクノロジには利点があります。 たとえば、Kafka はイベント バッファリング テクノロジであるため、IO 集中型であり、大量の処理能力を必要としません。 比較すると、Spark Streaming などのストリーム プロセッサはコンピューティング集中型であり、より強力な VM を必要とします。 これらのテクノロジを異なるクラスターに分離することによって、VM を最適に利用しながら、それらを独立してスケーリングできます。

### <a name="scale-the-stream-buffering-layer"></a>ストリーム バッファリング レイヤーのスケーリング

ストリーム バッファリング テクノロジの Event Hubs や Kafka は共にパーティションを使用し、コンシューマーがそれらのパーティションから読み取ります。 入力スループットのスケーリングには、パーティションの数をスケール アップする必要があり、パーティションの追加によって、並列処理の増加を実現します。 Event Hubs では、デプロイ後にパーティション数を変更できないため、ターゲット スケールを考慮して開始することが重要です。 Kafka では、Kafka がデータを処理中であっても、[パーティションを追加](https://kafka.apache.org/documentation.html#basic_ops_cluster_expansion)できます。 Kafka は、パーティションを再割り当てするためのツール `kafka-reassign-partitions.sh` を提供しています。 HDInsight は、[パーティション レプリカ再調整ツール](https://github.com/hdinsight/hdinsight-kafka-tools) `rebalance_rackaware.py` を提供しています。 この再調整ツールは、各レプリカが個別の障害ドメインと更新ドメインに置かれるように、`kafka-reassign-partitions.sh` ツールを呼び出し、Kafka ラックを認識させ、フォールト トレランスを強化します。

### <a name="scale-the-stream-processing-layer"></a>ストリーム処理レイヤーのスケーリング

Apache Storm と Spark Streaming は共に、データの処理中でも、クラスターにワーカー ノードを追加する機能をサポートしています。

Storm のスケーリングによって追加された新しいノードを利用するには、クラスター サイズを増やす前に開始していた Storm トポロジを再調整する必要があります。 この再調整は、Storm Web UI またはその CLI を使用して実行できます。 詳細については、[Apache Storm のドキュメント](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)を参照してください。

Apache Spark は、アプリケーションの要件に応じて、その環境を構成するための 3 つの重要なパラメーター (`spark.executor.instances`、`spark.executor.cores`、および `spark.executor.memory`) を使用します。 *executor* は、Spark アプリケーション用に起動されるプロセスです。 executor はワーカー ノードで動作し、アプリケーションのタスクを実行する役割を担います。 それぞれのクラスターで使用される executor の既定の数とサイズは、ワーカー ノードの数とワーカー ノードのサイズに基づいて計算されます。 これらの数は、各クラスター ヘッド ノード上の `spark-defaults.conf` ファイルに格納されます。

これらの 3 つのパラメーターは、クラスター レベルで (クラスター上で実行されるすべてのアプリケーションに対して) 構成でき、個々のアプリケーションに対して指定することもできます。 詳細については、[Apache Spark クラスターのリソースの管理](spark/apache-spark-resource-manager.md)に関するドキュメントを参照してください。

## <a name="next-steps"></a>次の手順

* [Azure HDInsight で Apache Storm トポロジを作成、監視する](storm/apache-storm-quickstart.md)
* [HDInsight での Apache Storm のトポロジ例](storm/apache-storm-example-topology.md)
* [HDInsight での Apache Spark の概要](spark/apache-spark-overview.md)
* [HDInsight での Apache Kafka の開始](kafka/apache-kafka-get-started.md)
