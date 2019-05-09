---
title: イベント処理を 1 回のみ伴う Spark Streaming ジョブの作成 - Azure HDInsight
description: ただ 1 回だけイベントを処理するように Spark Streaming を設定する方法。
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 194e6091180fa1dd0eaaf999e970c0248ea99db9
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/20/2018
ms.locfileid: "53651777"
---
# <a name="create-apache-spark-streaming-jobs-with-exactly-once-event-processing"></a>イベント処理を 1 回だけ伴う Apache Spark Streaming ジョブを作成します

システムでの障害発生後にストリーム処理アプリケーションがメッセージの再処理を行う方法はさまざまです。

* 少なくとも 1 回:各メッセージは必ず処理されますが、複数回処理される可能性があります。
* 多くても 1 回:各メッセージは、処理される場合と処理されない場合があります。 メッセージが処理される場合は、1 回だけ処理されます。
* 厳密に 1 回:各メッセージは、必ず 1 回だけ処理されることが保証されます。

この記事では、Spark Streaming が厳密に 1 回だけ処理するように構成する方法について説明します。

## <a name="exactly-once-semantics-with-apache-spark-streaming"></a>Apache Spark Streaming での厳密に 1 回のセマンティクス

まず、問題が発生した後にすべてのシステム障害ポイントが再起動する方法、およびデータの損失を回避する方法を検討します。 Spark Streaming アプリケーションには次のものがあります。

* 入力ソース
* 入力ソースからデータを取得する 1 つ以上のレシーバー プロセス
* データを処理するタスク
* 出力シンク
* 実行時間の長いジョブを管理するドライバー プロセス

厳密に 1 回のセマンティクスでは、障害が発生した場所に関係なく、いずれの時点でもデータが失われず、メッセージの処理が再開可能である必要があります。

### <a name="replayable-sources"></a>再生可能なソース

Spark Streaming アプリケーションがイベントを読み取るソースは、"*再生可能*" である必要があります。 つまり、メッセージを取得した後、メッセージを永続化または処理する前に、システムで障害が発生した場合、ソースは同じメッセージを再び提供する必要があります。

Azure では、Azure Event Hubs と [Apache Kafka](https://kafka.apache.org/) on HDInsight の両方が再生可能なソースを提供します。 再生可能なソースのもう 1 つの例は、[Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html)、Microsoft Azure Storage、Azure Data Lake Storage のようなフォールト トレラントなファイル システムであり、すべてのデータが永久に保持され、いつでもデータ全体を読み込み直すことができます。

### <a name="reliable-receivers"></a>信頼性の高いレシーバー

Spark Streaming では、Event Hubs や Kafka のようなソースは "*信頼性の高いレシーバー*" を備え、各レシーバーはソースの読み取りの進行状況を追跡します。 信頼性の高いレシーバーは、状態をフォールト トレラントなストレージに保持します ([Apache ZooKeeper](https://zookeeper.apache.org/)、または HDFS に書き込まれる Spark Streaming チェックポイント)。 このようなレシーバーで障害が発生して再起動した場合、中断した場所を取得できます。

### <a name="use-the-write-ahead-log"></a>先書きログの使用

Spark Streaming がその使用をサポートしている先書きログでは、受信された各イベントは最初にフォールト トレラント ストレージの Spark のチェックポイント ディレクトリに書き込まれた後、Resilient Distributed Dataset (RDD) に格納されます。 Azure では、フォールト トレラント ストレージは Azure Storage または Azure Data Lake Storage のいずれかによってバックアップされた HDFS です。 Spark Streaming アプリケーションでは、`spark.streaming.receiver.writeAheadLog.enable` 構成設定を `true` に設定することで、すべてのレシーバーに対して先書きログが有効になります。 先書きログは、ドライバーと Executor の両方の障害に対してフォールト トレランスを提供します。

イベント データに対するタスクを実行しているワーカーの場合、各 RDD はレプリケートされて複数のワーカーに分散されるように定義されています。 タスクを実行しているワーカーのクラッシュによりタスクが失敗した場合、タスクはイベント データのレプリカを持つ別のワーカーで再開されるので、イベントは失われます。

### <a name="use-checkpoints-for-drivers"></a>ドライバーに対するチェックポイントの使用

ジョブ ドライバーは再実行可能である必要があります。 Spark Streaming アプリケーションを実行しているドライバーがクラッシュした場合、ドライバーは、実行しているすべてのレシーバー、タスク、およびイベント データを格納する RDD と共に停止します。 この場合、後で再開できるように、ジョブの進行状況を保存できる必要があります。 これは、DStream の有向非巡回グラフ (DAG) をフォールト トレラント ストレージに定期的にチェックポイントすることによって実現されます。 DAG のメタデータには、ストリーミング アプリケーションの作成に使われる構成、アプリケーションを定義する操作、およびキューに登録されたがまだ完了していないすべてのバッチが含まれます。 このメタデータにより、障害が発生したドライバーはチェックポイント情報から再起動できます。 ドライバーは再起動すると、新しいレシーバーを起動し、レシーバー自体が先書きログから RDD にイベント データを復旧します。

チェックポイントは、Spark Streaming において 2 つのステップで有効化されます。 

1. StreamingContext オブジェクトでは、チェックポイントのストレージ パスを構成します。

    ```Scala
    val ssc = new StreamingContext(spark, Seconds(1))
    ssc.checkpoint("/path/to/checkpoints")
    ```

    HDInsight では、これらのチェックポイントをクラスターに接続されている既定のストレージ (Azure Storage または Azure Data Lake Storage) に保存する必要があります。

2. 次に、DStream でチェックポイントの間隔 (秒単位) を指定します。 各間隔で、入力イベントから派生した状態データがストレージに保存されます。 永続化された状態データは、ソース イベントから状態を再構築するときに必要な計算を減らすことができます。

    ```Scala
    val lines = ssc.socketTextStream("hostname", 9999)
    lines.checkpoint(30)
    ssc.start()
    ssc.awaitTermination()
    ```

### <a name="use-idempotent-sinks"></a>べき等シンクの使用

ジョブが結果を書き込む先のシンクは、同じ結果を複数回渡される状況に対処できる必要があります。 シンクは、このような重複する結果を検出し、それらを無視できる必要があります。 "*べき等*" シンクは、状態変化なしに、同じデータで複数回呼び出すことができます。

べき等シンクは、最初にデータ ストアに受信結果が存在するかどうかを確認するロジックを実装することによって、作成できます。 結果が既に存在する場合は、Spark ジョブから見ると書き込みは成功したように見えますが、実際には、データ ストアは重複するデータを無視しています。 結果が存在しない場合は、シンクはストレージに新しい結果を挿入する必要があります。 

たとえば、テーブルにイベントを挿入する Azure SQL Database のストアド プロシージャを使うことができます。 このストアド プロシージャは、最初にキー フィールドでイベントを検索し、一致するイベントが見つからなかった場合にのみ、テーブルにレコードを挿入します。

もう 1 つの例は、Azure Storage Blob や Azure Data Lake Storage のように、パーティション分割されたファイル システムを使う方法です。 この場合は、シンク ロジックでファイルの存在を確認する必要はありません。 イベントを表すファイルが存在する場合は、同じデータで単純に上書きされます。 存在しない場合は、計算されたパスで新しいファイルが作成されます。

## <a name="next-steps"></a>次の手順

* [Apache Spark Streaming の概要](apache-spark-streaming-overview.md)
* [Apache Hadoop YARN で高可用性 Apache Spark Streaming ジョブを作成する](apache-spark-streaming-high-availability.md)
