---
title: Azure HDInsight 3.6 Apache Storm を HDInsight 4.0 Apache Spark に移行する
description: Apache Storm のワークロードを Spark ストリーミングまたは Spark Structured Streaming に移行するための相違点と移行フロー。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/16/2019
ms.openlocfilehash: e1262a4699bc42cb5b9a4398be2254854c5d5ff2
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86081198"
---
# <a name="migrate-azure-hdinsight-36-apache-storm-to-hdinsight-40-apache-spark"></a>Azure HDInsight 3.6 Apache Storm を HDInsight 4.0 Apache Spark に移行する

このドキュメントでは、HDInsight 3.6 上の Apache Storm のワークロードを HDInsight 4.0 に移行する方法について説明します。 HDInsight 4.0 では Apache Storm クラスターの種類がサポートされていないため、別のストリーミング データ プラットフォームに移行する必要があります。 2 つの適切なオプションとして、Apache Spark ストリーミングと Spark Structured Streaming があります。 このドキュメントでは、これらのプラットフォーム間の違いについて説明し、Apache Storm のワークロードを移行するためのワークフローを推奨します。

## <a name="storm-migration-paths-in-hdinsight"></a>HDInsight での Storm の移行パス

HDInsight 3.6 の Apache Storm から移行する場合、複数のオプションがあります。

* HDInsight 4.0 の Spark ストリーミング
* HDInsight 4.0 の Spark Structured Streaming
* Azure Stream Analytics

このドキュメントでは、Apache Storm から Spark ストリーミングおよび Spark Structured Streaming に移行するためのガイドを提供します。

> [!div class="mx-imgBorder"]
> ![HDInsight Storm の移行パス](./media/migrate-storm-to-spark/storm-migration-path.png)

## <a name="comparison-between-apache-storm-and-spark-streaming-spark-structured-streaming"></a>Apache Storm と Spark ストリーミング、Spark Structured Streaming の比較

Apache Storm では、さまざまなレベルでメッセージの処理が保証されています。 たとえば、基本的な Storm アプリケーションは少なくとも 1 回の処理を保証でき、[Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) は正確に 1 回の処理を保証できます。 Spark ストリーミングと Spark Structured Streaming では、ノード障害が発生しても、すべての入力イベントが厳密に 1 回処理されることが保証されます。 Storm には、各イベントを処理するモデルがあります。また、Trident でマイクロ バッチ モデルを使用することもできます。 Spark ストリーミングと Spark Structured Streaming には、マイクロ バッチ処理モデルが用意されています。

|  |Storm |Spark ストリーミング | Spark Structured Streaming|
|---|---|---|---|
|**イベント処理の保証**|少なくとも 1 回 <br> 厳密に 1 回 (Trident) |[厳密に 1 回](https://spark.apache.org/docs/latest/streaming-programming-guide.html)|[厳密に 1 回](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**処理モデル**|リアルタイム <br> マイクロ バッチ (Trident) |マイクロ バッチ |マイクロ バッチ |
|**イベント時刻のサポート**|[はい](https://storm.apache.org/releases/2.0.0/Windowing.html)|いいえ|[はい](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Languages**|Java など|Scala、Java、Python|Python、R、Scala、Java、SQL|

### <a name="spark-streaming-vs-spark-structured-streaming"></a>Spark ストリーミングと Spark Structured Streaming

Spark Structured Streaming は、Spark ストリーミング (DStreams) に代わるものです。 Structured Streaming が拡張やメンテナンスを継続的に受けるのに対して、DStreams はメンテナンス モードのみになります。 **注: このポイントを強調するためのリンクが必要**。 Structured Streaming には、標準でサポートされているソースやシンクに対して DStreams ほど多くの機能がないため、要件を評価して適切な Spark ストリーム処理オプションを選択してください。

## <a name="streaming-single-event-processing-vs-micro-batch-processing"></a>ストリーミング (単一イベント) 処理とマイクロ バッチ処理

Storm には、各イベントを処理するモデルが用意されています。 つまり、受信したすべてのレコードは、到着するとすぐに処理されます。 Spark ストリーミングのアプリケーションは、イベントの各マイクロ バッチを処理のために送信する前に、そのバッチを収集するために一瞬待機しなければなりません。 これに対し、イベント ドリブンのアプリケーションは各イベントをすぐに処理します。 通常、Spark ストリーミングの待機時間は数秒以内です。 マイクロ バッチのアプローチの利点は、データ処理と集計計算の合理化です。

> [!div class="mx-imgBorder"]
> ![ストリーミングとマイクロ バッチの処理](./media/migrate-storm-to-spark/streaming-and-micro-batch-processing.png)

## <a name="storm-architecture-and-components"></a>Storm のアーキテクチャとコンポーネント

Storm トポロジは、有向非巡回グラフ (DAG) に配置された複数のコンポーネントで構成されます。 データはグラフ内のコンポーネント間を流れます。 各コンポーネントでは 1 つ以上のデータ ストリームを使用し、必要に応じて 1 つ以上のストリームを出力できます。

|コンポーネント |説明 |
|---|---|
|スパウト|データをトポロジに取り込みます。 1 つ以上のストリームをこのトポロジに出力します。|
|ボルト|スパウトまたはその他のボルトから出力されたストリームを使用します。 ボルトは、必要に応じてストリームをトポロジに出力できます。 また、HDFS、Kafka、HBase などの外部サービスまたはストレージへのデータの書き込みも行います。|

> [!div class="mx-imgBorder"]
> ![Storm コンポーネントの相互作用](./media/migrate-storm-to-spark/apache-storm-components.png)

Storm は、次の 3 つのデーモンで構成されています。これにより、Storm クラスターは機能し続けます。

|デーモン |説明 |
|---|---|
|Nimbus|Hadoop の JobTracker と同様に、クラスターへのコードの配布、マシンへのタスクの割り当て、障害の監視を行います。|
|Zookeeper|クラスターの調整に使用されます。|
|Supervisor|マシンに割り当てられた作業をリッスンし、Nimbus からのディレクティブに基づいてワーカー プロセスの起動と停止を行います。 各ワーカー プロセスは、トポロジのサブセットを実行します。 ユーザーのアプリケーション ロジック (スパウトとボルト) はここで実行されます。|

> [!div class="mx-imgBorder"]
> ![nimbus、zookeeper、および supervisor デーモン](./media/migrate-storm-to-spark/nimbus-zookeeper-supervisor.png)

## <a name="spark-streaming-architecture-and-components"></a>Spark ストリーミングのアーキテクチャとコンポーネント

次の手順は、コンポーネントが Spark ストリーミング (DStreams) と Spark Structured Streaming でどのように連携するかをまとめたものです。

* Spark ストリーミングが起動されると、ドライバーは Executor でタスクを起動します。
* Executor は、ストリーミング データ ソースからストリームを受信します。
* Executor は、データ ストリームを受信すると、ストリームをブロックに分割して、メモリに保持します。
* データのブロックは、他の Executor にレプリケートされます。
* その後、処理されたデータは、ターゲット データ ストアに格納されます。

> [!div class="mx-imgBorder"]
> ![Spark ストリーミングの出力へのパス](./media/migrate-storm-to-spark/spark-streaming-to-output.png)

## <a name="spark-streaming-dstream-workflow"></a>Spark ストリーミング (DStream) のワークフロー

各バッチ間隔の経過に伴って、その間隔のすべてのデータを含む新しい RDD が生成されます。 この連続する一連の RDD が DStream に収集されます。 たとえば、バッチ間隔が 1 秒の場合、DStream はその 1 秒間に取り込まれるすべてのデータを含む 1 つの RDD を含む 1 つのバッチを毎秒出力します。 DStream の処理時に、温度のイベントがこれらのバッチのいずれかに出現します。 Spark ストリーミング アプリケーションはイベントを含むバッチを処理し、最終的に各 RDD に格納されているデータを処理します。

> [!div class="mx-imgBorder"]
> ![Spark ストリーミングの処理バッチ](./media/migrate-storm-to-spark/spark-streaming-batches.png)

Spark ストリーミングで使用できるさまざまな変換の詳細については、「[DStreams での変換](https://spark.apache.org/docs/latest/streaming-programming-guide.html#transformations-on-dstreams)」を参照してください。

## <a name="spark-structured-streaming"></a>Spark Structured Streaming

Spark Structured Streaming は、深さが無制限のテーブルとしてデータ ストリームを表します。 テーブルは、新しいデータが到着するにつれて拡大し続けます。 この入力テーブルは実行時間の長いクエリによって継続的に処理され、その結果は出力テーブルに送信されます。

Structured Streaming では、データがシステムに到着すると、直ちに入力テーブルに取り込まれます。 この入力テーブルに対して操作を実行するクエリを (データフレームおよびデータセット API を使用して) 記述します。

クエリの出力により、クエリの結果を含む "*結果テーブル*" が生成されます。 この結果テーブルから、リレーショナル データベースなどの外部データストアのためにデータを取り出すことができます。

入力テーブルのデータが処理されるタイミングは、トリガー間隔によって制御されます。 既定では、トリガー間隔は 0 であるため、Structured Streaming はデータが到着するとすぐに処理しようとします。 これは、実際には、Structured Streaming が前のクエリの処理を完了するとすぐに、新しく受信したデータに対する別の処理を開始することを示します。 ストリーミング データが時間ベースのバッチで処理されるように、トリガーを一定の間隔で実行されるように構成することができます。

> [!div class="mx-imgBorder"]
> ![Structured Streaming のデータの処理](./media/migrate-storm-to-spark/structured-streaming-data-processing.png)

> [!div class="mx-imgBorder"]
> ![Structured Streaming のプログラミング モデル](./media/migrate-storm-to-spark/structured-streaming-model.png)

## <a name="general-migration-flow"></a>一般的な移行フロー

Storm から Spark への推奨される移行フローは、次の初期アーキテクチャを前提としています。

* ストリーミング データ ソースとして Kafka が使用されている
* Kafka と Storm は同じ仮想ネットワークにデプロイされている
* Storm よって処理されたデータは、Azure Storage や Azure Data Lake Storage Gen2 などのデータ シンクに書き込まれる

    > [!div class="mx-imgBorder"]
    > ![想定される現在の環境の図](./media/migrate-storm-to-spark/presumed-current-environment.png)

アプリケーションを Storm からいずれかの Spark ストリーミング API に移行するには、次の手順を実行します。

1. **新しいクラスターをデプロイします。** 新しい HDInsight 4.0 の Spark クラスターを同じ仮想ネットワークにデプロイし、Spark ストリーミングまたは Spark Structured Streaming アプリケーションをそこにデプロイして十分にテストします。

    > [!div class="mx-imgBorder"]
    > ![HDInsight での新しい Spark のデプロイ](./media/migrate-storm-to-spark/new-spark-deployment.png)

1. **以前の Storm クラスターでの使用を停止します。** 既存の Storm で、ストリーミング データ ソースからのデータの使用を停止し、データがターゲット シンクへの書き込みを完了するまで待機します。

    > [!div class="mx-imgBorder"]
    > ![現在のクラスターでの使用を停止する](./media/migrate-storm-to-spark/stop-consuming-current-cluster.png)

1. **新しい Spark クラスターでの使用を開始します。** 新しくデプロイされた HDInsight 4.0 の Spark クラスターからデータのストリーミングを開始します。 この時点で、最新の Kafka オフセットからの使用により、プロセスが引き継がれます。

    > [!div class="mx-imgBorder"]
    > ![新しいクラスターでの使用を開始する](./media/migrate-storm-to-spark/start-consuming-new-cluster.png)

1. **必要に応じて、古いクラスターを削除します。** 切り替えが完了し、正常に動作したら、必要に応じて古い HDInsight 3.6 の Storm クラスターを削除します。

    > [!div class="mx-imgBorder"]
    > ![必要に応じて古い HDInsight クラスターを削除する](./media/migrate-storm-to-spark/remove-old-clusters1.png)

## <a name="next-steps"></a>次のステップ

Storm、Spark ストリーミング、および Spark Structured Streaming の詳細については、次のドキュメントを参照してください。

* [Apache Spark ストリーミングの概要](../spark/apache-spark-streaming-overview.md)
* [Apache Spark Structured Streaming の概要](../spark/apache-spark-structured-streaming-overview.md)
