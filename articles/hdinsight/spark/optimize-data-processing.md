---
title: Apache Spark のデータ処理を最適化する - Azure HDInsight
description: Azure HDInsight を使用して Apache Spark でデータを処理する最も効率的な操作を選択する方法について説明します。
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 98457456bc1c8f9fdb08c32fdcd319a3aa9ff14a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944724"
---
# <a name="data-processing-optimization-for-apache-spark"></a>Apache Spark のデータ処理の最適化

この記事では、Azure HDInsight で最高のパフォーマンスが得られるように Apache Spark クラスターの構成を最適化する方法について説明します。

## <a name="overview"></a>概要

結合またはシャッフルで低速のジョブがある場合、その原因は *データ スキュー* である可能性があります。 データ スキューとは、ジョブ データの非対称です。 たとえば、マップ ジョブには 20 秒かかることがあります。 一方、データが結合またはシャッフルされているジョブの実行には何時間もかかります。 データ スキューを修正するには、キー全体をソルティングするか、キーの一部のサブセットのみに *分離したソルト* を使用する必要があります。 分離したソルトを使用する場合は、マップの結合でソルティングしたキーのサブセットを分離するため、さらにフィルター処理する必要があります。 もう 1 つのオプションは、バケット列を導入し、最初にバケットで事前に集計することです。

結合が遅くなるもう 1 つの要因は、結合タイプである可能性があります。 既定では、Spark は `SortMerge` 結合タイプを使用します。 この種類の結合は、大きいデータセットに最適です。 ただし、それ以外の場合は、マージする前に最初にデータの左側と右側を並べ替える必要があるため、計算コストが高くなります。

`Broadcast` 結合は、小さいデータ セット、または結合の一方の側がもう一方よりはるかに小さい場合に最適です。 このタイプの結合は、一方の側をすべての実行プログラムにブロードキャストするため、通常はブロードキャストに多くのメモリが必要です。

`spark.sql.autoBroadcastJoinThreshold` を設定することで構成内の結合タイプを変更することも、DataFrame API (`dataframe.join(broadcast(df2))`) を使用して結合のヒントを設定することもできます。

```scala
// Option 1
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", 1*1024*1024*1024)

// Option 2
val df1 = spark.table("FactTableA")
val df2 = spark.table("dimMP")
df1.join(broadcast(df2), Seq("PK")).
    createOrReplaceTempView("V_JOIN")

sql("SELECT col1, col2 FROM V_JOIN")
```

バケット化したテーブルを使用する場合は、3 つ目の結合タイプ、`Merge` 結合があります。 事前のパーティション分割と事前の並べ替えが適切に行われているデータセットは、`SortMerge` 結合からコストのかかる並べ替えフェーズを省略します。

結合の順序は、より複雑なクエリでは特に重要です。 最も選択的な結合から開始してください。 また、可能な場合は、集計後に行数を増やす結合を移動してください。

デカルト結合の並列処理を管理するには、入れ子になった構造やウィンドウ化を追加することができ、場合によっては Spark ジョブの 1 つまたは複数の手順をスキップすることができます。

## <a name="optimize-job-execution"></a>ジョブ実行の最適化

* たとえばデータを 2 回使用してからキャッシュする場合は、必要に応じてキャッシュします。
* 変数はすべての実行プログラムにブロードキャストします。 変数は 1 回シリアル化されるだけなので、結果として検索が高速になります。
* ドライバーでスレッド プールを使用します。これにより、多数のタスクの操作が高速になります。

パフォーマンスの問題の場合は、実行中のジョブを定期的に監視します。 特定の問題について詳細に知る必要がある場合は、次のパフォーマンスのプロファイル ツールのいずれかを検討してください。

* [Intel PAL ツール](https://github.com/intel-hadoop/PAT)は、CPU、ストレージ、およびネットワーク帯域幅の使用量を監視します。
* [Oracle Java 8 Mission Control](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) は、Spark と実行プログラムのコードをプロファイルします。

Spark 2.x クエリのパフォーマンスに重要なものは、ステージ全体のコード生成に依存する Tungsten エンジンです。 場合によっては、ステージ全体のコード生成を利用できないことがあります。 たとえば、集計式で変更できない型 (`string`) を使用すると、`HashAggregate` ではなく `SortAggregate`が表示されます。 たとえば、パフォーマンスを向上させるために、次を実行してから、コード生成を再度有効にします。

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>次のステップ

* [Apache Spark 用にデータ ストレージを最適化する](optimize-data-storage.md)
* [Apache Spark 用にメモリ使用量を最適化する](optimize-memory-usage.md)
* [Apache Spark のクラスター構成を最適化する](optimize-cluster-configuration.md)
