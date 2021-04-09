---
title: Apache Spark クラスター構成の最適化 - Azure HDInsight
description: Azure HDInsight でスループットを最大化するように Apache Spark クラスターを構成する方法について説明します。
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: f70e9f242b0f12abf58e72554e7cb6819ce8f7a9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944729"
---
# <a name="cluster-configuration-optimization-for-apache-spark"></a>Apache Spark のクラスター構成の最適化

この記事では、Azure HDInsight で最高のパフォーマンスが得られるように Apache Spark クラスターの構成を最適化する方法について説明します。

## <a name="overview"></a>概要

Spark クラスターのワークロードによっては、既定以外の Spark 構成のほうが結果として Spark ジョブの実行をより最適化することがわかる場合があります。  既定以外のクラスター構成を検証するには、サンプルのワークロードを使用してベンチマーク テストを実行します。

調整可能ないくつかの一般的なパラメーターを、次に示します。

|パラメーター |説明 |
|---|---|
|--num-executors|適切な数の実行プログラムを設定します。|
|--executor-cores|各 Executor のコア数を設定します。 他のプロセスが使用可能なメモリの一部を消費するため、通常は中規模な実行プログラムにしてください。|
|--executor-memory|YARN のヒープ サイズを制御する、各実行プログラムのメモリ サイズを設定します。 実行のオーバーヘッド用のメモリを残しておいてください。|

## <a name="select-the-correct-executor-size"></a>実行プログラムの適切なサイズの選択

実行プログラムの構成を決定する際は、Java ガベージ コレクション (GC) のオーバーヘッドを考慮してください。

* 実行プログラムのサイズを小さくする要因:
    1. GC オーバーヘッドを 10% 未満に保つため、ヒープ サイズを 32 GB より小さくします。
    2. GC オーバーヘッドを 10% 未満に保つため、コア数を減らします。

* 実行プログラムのサイズを大きくする要因:
    1. 実行プログラム間の通信オーバーヘッドを削減します。
    2. 大きいクラスター (実行プログラムが 100 より多い) 上の実行プログラム (N2) の間で開いている接続の数を減らします。
    3. メモリを大量に使用するタスクに合わせて、ヒープ サイズを増やします。
    4. 省略可能:実行プログラムあたりのメモリ オーバーヘッドを減らします。
    5. 省略可能:CPU をオーバーサブスクライブすることで、使用量とコンカレンシーを増やします。

実行プログラムのサイズを選択するときの一般的なルール:

1. 実行プログラムあたり 30 GB から始めて、使用可能なコンピューターのコアを分散します。
2. 大きいクラスター (実行プログラムが 100 より多い) の実行プログラムのコアの数を増やします。
3. 試運転と、GC オーバーヘッドなどの上記の要因の両方に基づいて、サイズを変更します。

同時クエリを実行する場合の考慮事項:

1. 実行プログラムあたり 30 GB と、すべてのコンピューター コアから始めます。
2. CPU をオーバーサブスクライブすることで、複数の並列の Spark アプリケーションを作成します (約 30% の待機時間の改善)。
3. クエリを並列アプリケーション全体に分散します。
4. 試運転と、GC オーバーヘッドなどの上記の要因の両方に基づいて、サイズを変更します。

Ambari を使用してエグゼキュータを構成する方法の詳細については、[Apache Spark の設定の Spark エグゼキュータ](apache-spark-settings.md#configuring-spark-executors)に関する記事を参照してください。

タイムライン ビューを調べることで、外れ値やその他のパフォーマンスの問題に対するクエリのパフォーマンスを監視します。 SQL グラフやジョブの統計なども調べます。 YARN と Spark History サーバーを使用した Spark ジョブのデバッグの詳細については、「[Azure HDInsight で実行される Apache Spark ジョブのデバッグ](apache-spark-job-debugging.md)」を参照してください。 YARN Timeline Server を使う際のヒントについては、[Apache Hadoop YARN アプリケーション ログへのアクセス](../hdinsight-hadoop-access-yarn-app-logs-linux.md)に関する記事を参照してください。

場合によっては、1 つまたは 2、3 の実行プログラムが他よりも遅くなり、タスクの実行にかなり長くかかることがあります。 こうした遅延は大きいクラスター (ノードが 30 より多い) で頻繁に発生します。 この場合は、スケジューラが低速のタスクを補正できるよう、より多くのタスクに操作を分割します。 たとえば、タスクの数を、少なくとも 2 回、アプリケーション内の実行プログラムのコア数と同数にします。 また、`conf: spark.speculation = true` を使用してタスクの予測実行を有効にすることもできます。

## <a name="next-steps"></a>次のステップ

* [Apache Spark のデータ処理を最適化する](optimize-cluster-configuration.md)
* [Apache Spark 用にデータ ストレージを最適化する](optimize-data-storage.md)
* [Apache Spark 用にメモリ使用量を最適化する](optimize-memory-usage.md)
