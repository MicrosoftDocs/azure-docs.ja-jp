---
title: "Azure HDInsight における Spark ストリーミングとは | Microsoft Docs"
description: "HDInsight Spark クラスターで Spark ストリーミング アプリケーションを使用する方法を説明します。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: raghavmohan
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: ramoha
ms.openlocfilehash: e80b69fa3e60618dbaccd612114200a941019854
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-spark-streaming"></a>Spark ストリーミングの概要

Spark ストリーミングでは、ノード障害が発生した場合でも、すべての入力イベントが厳密に 1 回確実に処理されるように HDInsight Spark クラスター上でデータ ストリーム処理を実行できます。 Spark Stream とは、Azure Event Hubs、Azure IoT Hub、Kafka、Flume、Twitter、ZeroMQ、生 TCP ソケットを含むさまざまなソースからの、または HDFS ファイルシステムの監視からの入力データを受け取る実行時間の長いジョブです。 1 つのイベント ドリブン プロセスとは異なり、Spark Stream は入力データを 2 秒スライスなどの時間枠にバッチ処理し、マップ、リデュース、結合、抽出操作を使用してデータの各バッチを変換します。 次に、変換されたデータをファイルシステム、データベース、ダッシュボード、およびコンソールに書き出します。

![HDInsight および Spark ストリーミングを使用したストリーム処理](./media/hdinsight-spark-streaming-overview/hdinsight-spark-streaming.png)

Spark ストリーミング アプリケーションは、処理用にそのバッチを送信する前に、イベントの各*マイクロ バッチ*を収集するために 1 秒に満たない時間待機する必要があります。 これに対し、イベント ドリブンのアプリケーションは各イベントをすぐに処理します。 通常、Spark ストリーミングの待機時間は数秒以内です。 マイクロ バッチのアプローチの利点は、データ処理と集計計算の合理化です。

## <a name="introducing-the-dstream"></a>DStream の概要

Spark ストリーミングは、DStream と呼ばれる*分離されたストリーム*を使用して受信データの継続的なストリームを表します。 DStream は Event Hubs または Kafka などの入力ソースから作成するか、変換を他の DStream に適用して作成できます。

DStream には、生イベント データの上に抽象化の層を備えています。 

1 つのイベント、たとえば、接続されたサーモスタットからの温度の読み取りから開始するとします。 このイベントが Spark ストリーミング アプリケーションに到達すると、イベントは確実な方法 (つまり、複数のノードにレプリケートされる) で格納されます。 このフォールト トレランスにより、単一ノードに障害が発生しても、イベントを損失することはありません。 Spark コアは、クラスター内の複数のノードにデータを分散するデータ構造を使用します。ここでは通常、パフォーマンスを最大限高めるため、各ノードがその独自のデータをメモリ内に保持します。 このデータ構造は、*Resilient Distributed Dataset* (RDD) と呼ばれます。

各 RDD は、バッチ間隔と呼ばれるユーザー定義の期間の経過とともに収集されるイベントを表します。 各バッチ間隔の経過に伴って、その間隔のすべてのデータを含む新しい RDD が生成されます。 この継続的な一連の RDD が DStream に収集されます。 たとえば、バッチ間隔が 1 秒の場合、DStream はその 1 秒間に取り込まれるすべてのデータを含む 1 つの RDD を含む 1 つのバッチを毎秒出力します。 DStream の処理時に、温度のイベントがこれらのバッチのいずれかに出現します。 Spark ストリーミング アプリケーションはイベントを含むバッチを処理し、最終的に各 RDD に格納されているデータを処理します。

![温度イベントでのサンプル DStream ](./media/hdinsight-spark-streaming-overview/hdinsight-spark-streaming-example.png)

## <a name="structure-of-a-spark-streaming-application"></a>Spark ストリーミング アプリケーションの構造
Spark ストリーミング アプリケーションは、取り込みソースからデータを受信する実行時間の長いアプリケーションで、変換を適用してデータを処理した後、1 つ以上の変換先にデータをプッシュします。 Spark ストリーミング アプリケーションは、主に 2 つの部分から構成されます。 1 つ目として、データの取得元、データに対して実行する処理、結果の宛先を定義します。 2 つ目に、ユーザーはアプリケーションを無期限に実行して、停止信号を待機します。

たとえば、TCP ソケットを介した 1 行のテキストを受信して、各単語が表示される回数をカウントする単純なアプリケーションがあるとします。

### <a name="define-the-application"></a>アプリケーションの定義

アプリケーション ロジックを定義するには、次の 4 つの手順を行います。

1. StreamingContext を作成します。
2. StreamingContext から DStream を作成します。
3. 変換を DStream に適用します。
4. 結果を出力します。

この定義は静的で、アプリケーションを実行するまでデータは処理されません。

#### <a name="create-a-streamingcontext"></a>StreamingContext の作成
クラスターを指す SparkContext から StreamingContext を作成します。 StreamingContext を作成する際に、バッチのサイズを秒で指定します。例:

    val ssc = new StreamingContext(spark, Seconds(1))

#### <a name="create-a-dstream"></a>DStream の作成
作成した StreamingContext インスタンスを使用して、入力ソース用の入力 DStream を作成します。 ここでは、HDInsight クラスターに接続されている既定の記憶域内の新しいファイルの出現に注意します。

    val lines = ssc.textFileStream("/uploads/2017/01/")

#### <a name="apply-transformations"></a>変換の適用
変換を DStream に適用して、処理を実装します。 アプリケーションはファイルから一度に 1 行のテキストを受信し、各行をいくつかの語に分割した後、map-reduce パターンを使用して各単語が出現する回数をカウントします。

    val words = lines.flatMap(_.split(" "))
    val pairs = words.map(word => (word, 1))
    val wordCounts = pairs.reduceByKey(_ + _)

#### <a name="output-results"></a>結果の出力
出力操作を適用して、変換結果を変換先システムにプッシュします。 ここでは、コンソール出力の計算の各実行の結果を示しています。

    wordCounts.print()

### <a name="run-the-application"></a>アプリケーションの実行
ストリーミング アプリケーションを起動し、強制終了シグナルを受信するまで実行します。

    ssc.start()            
    ssc.awaitTermination()

Spark Stream API とサポートされるイベント ソース、変換、出力操作について詳しくは、「[Spark ストリーミングのプログラミング ガイド](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)」をご覧ください。

次のサンプル アプリケーションは自己完結型なので、[Jupyter Notebook](hdinsight-apache-spark-jupyter-notebook-kernels.md) で実行できます。 例では、カウンターの値と 5 秒おきに現在の時刻 (ミリ秒) を出力するクラス DummySource にモック データ ソースを作成します。 バッチ間隔が 30 秒の新しい StreamingContext オブジェクトを作成します。 これは、バッチが作成されるたびに、生成された RDD を調べて Spark DataFrame に変換し、DataFrame から一時テーブルを作成します。

    class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

        /** Start the thread that simulates receiving data */
        def onStart() {
            new Thread("Dummy Source") { override def run() { receive() } }.start()
        }

        def onStop() {  }

        /** Periodically generate a random number from 0 to 9, and the timestamp */
        private def receive() {
            var counter = 0  
            while(!isStopped()) {
            store(Iterator((counter, System.currentTimeMillis)))
            counter += 1
            Thread.sleep(5000)
            }
        }
    }

    // A batch is created every 30 seconds
    val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

    // Set the active SQLContext so that we can access it statically within the foreachRDD
    org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

    // Create the stream
    val stream = ssc.receiverStream(new DummySource())

    // Process RDDs in the batch
    stream.foreachRDD { rdd =>

        // Access the SQLContext and create a table called demo_numbers we can query
        val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
        _sqlContext.createDataFrame(rdd).toDF("value", "time")
            .registerTempTable("demo_numbers")
    } 

    // Start the stream processing
    ssc.start()

次に、DataFrame に定期的にクエリを実行して、バッチ内に現在のデータ セットを表示できます。 この場合は、次の SQL クエリを使用します。

    %%sql
    SELECT * FROM demo_numbers

結果の出力は次のようになります。

| value | time |
| --- | --- |
|10 | 1497314465256 |
|11 | 1497314470272 |
|12 | 1497314475289 |
|13 | 1497314480310 |
|14 | 1497314485327 |
|15 | 1497314490346 |

DummySource は 5 秒ごとに 1 つの値を作成し、30 秒ごとに 1 つのバッチを出力するため、値は 6 つになります。

## <a name="sliding-windows"></a>スライディング ウィンドウ
一定の期間にわたって DStream で集計計算を実行する場合 (最後の 2 秒間の平均温度を取得する場合など)、Spark ストリーミングに付属のスライディング ウィンドウ操作を使用できます。 スライディング ウィンドウは、期間 (ウィンドウの長さ) と、ウィンドウのコンテンツが評価される間隔 (スライド間隔) を持ちます。

これらのスライディング ウィンドウは重複が可能です。たとえば、長さが 2 秒で 1 秒ごとにスライドするウィンドウを定義することができます。 つまり、集計計算を実行するたびに、前のウィンドウの最後の 1 秒のデータと、次の 1 秒の新しいデータがウィンドウに含まれます。

![初期ウィンドウと温度イベントの例](./media/hdinsight-spark-streaming-overview/hdinsight-spark-streaming-window-01.png)

![温度イベントを持つスライド後のウィンドウの例](./media/hdinsight-spark-streaming-overview/hdinsight-spark-streaming-window-02.png)

次の例では、DummySource を使用するコードを、1 分間の長さと 1 分間のスライドを持つウィンドウにバッチを収集するように更新します。

    // A batch is created every 30 seconds
    val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

    // Set the active SQLContext so that we can access it statically within the foreachRDD
    org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

    // Create the stream
    val stream = ssc.receiverStream(new DummySource())

    // Process batches in 1 minute windows
    stream.window(org.apache.spark.streaming.Minutes(1)).foreachRDD { rdd =>

        // Access the SQLContext and create a table called demo_numbers we can query
        val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
        _sqlContext.createDataFrame(rdd).toDF("value", "time")
        .registerTempTable("demo_numbers")
    } 

    // Start the stream processing
    ssc.start()

最初の 1 分後、これは、12 エントリ (ウィンドウで収集される 2 つのバッチそれぞれから 6 エントリずつ) になります。

| value | time |
| --- | --- |
| 1 | 1497316294139 |
| 2 | 1497316299158
| 3 | 1497316304178
| 4 | 1497316309204
| 5 | 1497316314224
| 6 | 1497316319243
| 7 | 1497316324260
| 8 | 1497316329278
| 9 | 1497316334293
| 10 | 1497316339314
| 11 | 1497316344339
| 12 | 1497316349361

Spark Streaming API で使用できるスライディング ウィンドウ関数には、window、countByWindow、reduceByWindow、countByValueAndWindow などがあります。 これらの関数について詳しくは、[DStreams での変換](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html#transformations-on-dstreams)に関する記事をご覧ください。

## <a name="checkpointing"></a>チェックポイント機能
回復性とフォールト トレランスを実現するために、Spark ストリーミングはチェックポイント機能に依存して、ノード障害が発生した場合でも中断なくストリーム処理を続行できるようにします。 HDInsight では、Spark は持続性のあるストレージ (Azure Storage or Data Lake Store) にチェックポイントを作成します。 これらのチェックポイントには、ストリーミング アプリケーションに関するメタデータ (構成、アプリケーションが定義した操作、キューに登録済みだが未処理のバッチなど) が格納されます。 In some cases, the checkpoints will also include saving the data in the RDDs to sh場合によっては、チェックポイントには RDD 内のデータも保存されます。これは、Spark が管理する RDD 内に存在するものからのデータの状態のリビルドにかかる時間を削減するためです。

## <a name="deploying-spark-streaming-applications"></a>Spark ストリーミング アプリケーションのデプロイ
通常、Spark ストリーミング アプリケーションをローカルでビルドして、アプリケーションを含む JAR ファイルを HDInsight クラスターに接続された既定のストレージにコピーすることでそれを HDInsight の Spark にデプロイします。 次に、アプリケーションを開始するには、クラスターから利用できる LIVY REST API を使用します。 これは POST 操作で、JAR へのパス、クラスの名前 (その主なメソッドによってストリーミング アプリケーションが定義され実行される)、(省略可能) ジョブのリソース要件 (実行プログラム、メモリ、コアの数など)、およびアプリケーション コードで必要になる構成設定などを指定する JSON ドキュメントが POST の本文に含まれます。

![温度イベントを持つスライド後のウィンドウの例](./media/hdinsight-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

GET 要求を使用して、LIVY エンドポイントに対してすべてのアプリケーションの状態をチェックすることもできます。 最後に、LIVE エンドポイントに対して DELETE 要求を発行することで、実行中のアプリケーションを終了できます。 LIVY API について詳しくは、「[Livy を使用したリモート ジョブ](hdinsight-apache-spark-livy-rest-interface.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* [HDInsight での Apache Spark クラスターの作成](hdinsight-hadoop-create-linux-clusters-portal.md)
* [Spark ストリーミング プログラミング ガイド](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)
* [LIVY を使用した Spark ジョブのリモート起動](hdinsight-apache-spark-livy-rest-interface.md)