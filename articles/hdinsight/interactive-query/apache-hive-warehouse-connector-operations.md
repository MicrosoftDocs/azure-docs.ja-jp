---
title: Azure HDInsight の Hive Warehouse Connector でサポートされる Apache Spark の操作
description: Azure HDInsight での Hive Warehouse Connector のさまざまな機能について説明します。
author: nis-goel
ms.author: nisgoel
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/22/2020
ms.openlocfilehash: 1799aff8bff96d404ddcbefbf58a5f5014cdba6a
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871590"
---
# <a name="apache-spark-operations-supported-by-hive-warehouse-connector-in-azure-hdinsight"></a>Azure HDInsight の Hive Warehouse Connector でサポートされる Apache Spark の操作

この記事では、Hive Warehouse Connector (HWC) でサポートされる Spark ベースの操作について説明します。 次に示すすべての例は、Apache Spark シェルを通じて実行されます。

## <a name="prerequisite"></a>前提条件

[Hive Warehouse Connector の設定](./apache-hive-warehouse-connector.md#hive-warehouse-connector-setup)ステップを完了します。

## <a name="getting-started"></a>作業の開始

spark-shell セッションを開始するには、次の手順を実行します。

1. [ssh コマンド](../hdinsight-hadoop-linux-use-ssh-unix.md)を使用して Apache Spark クラスターに接続します。 次のコマンドを編集して CLUSTERNAME をクラスターの名前に置き換えてから、そのコマンドを入力します。

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. SSH セッションから次のコマンドを実行して、`hive-warehouse-connector-assembly` のバージョンを確認します。

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

1. 上で特定した `hive-warehouse-connector-assembly` のバージョンを使用して、次のコードを編集します。 次のコマンドを実行して、spark シェルを起動します。

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. spark-shell の起動後、次のコマンドを使用して Hive Warehouse Connector インスタンスを開始できます。

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

## <a name="creating-spark-dataframes-using-hive-queries"></a>Hive クエリを使用した Spark DataFrame の作成

HWC ライブラリを使用したすべてのクエリの結果は、DataFrame として返されます。 次の例では、基本的な Hive クエリを作成する方法を示します。

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

クエリの結果は Spark DataFrame です。これは、MLIB や SparkSQL のような Spark ライブラリと共に使用できます。

## <a name="writing-out-spark-dataframes-to-hive-tables"></a>Spark DataFrame から Hive テーブルへの書き出し

Spark では、Hive によって管理される ACID テーブルへの書き込みはネイティブにサポートされていません。 ただし、HWC を使用すると、どの DataFrame も Hive テーブルに書き出すことができます。 次の例では、この機能の動作を確認できます。

1. 次のコマンドを使用し、`sampletable_colorado` というテーブルを作成してその列を指定します。

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. テーブル `hivesampletable` をフィルターします (列 `state` = `Colorado`)。 この Hive クエリでは、`write` 関数を使用して Hive テーブル `sampletable_colorado` に保存されている Spark データフレームが返されます。

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format("com.hortonworks.spark.sql.hive.llap.HiveWarehouseConnector").mode("append").option("table","sampletable_colorado").save()
    ```

1. 次のコマンドを使用して結果を表示します。

    ```scala
    hive.table("sampletable_colorado").show()
    ```
    
    :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png" alt-text="Hive Warehouse Connector の Hive テーブルの表示" border="true":::


## <a name="structured-streaming-writes"></a>構造化ストリーミングの書き込み

Hive Warehouse Connector を使用すると、Spark ストリーミングを使って Hive テーブルにデータを書き込むことができます。

> [!IMPORTANT]
> 構造化ストリーミングの書き込みは、ESP が有効になっている Spark 4.0 クラスターではサポートされていません。

localhost ポート 9999 の Spark ストリームから Hive テーブルにデータを取り込むには (Hive Warehouse Connector 経由)、次の手順のようにします。

1. 開いている Spark シェルから、次のコマンドを使用して Spark ストリームを開始します。

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. 次の手順を実行して、作成した Spark ストリームのためのデータを生成します。
    1. 同じ Spark クラスターで2番目の SSH セッションを開きます。
    1. コマンド プロンプトで、「`nc -lk 9999`」と入力します。 このコマンドでは、netcat ユーティリティを使用して、コマンド ラインから指定のポートにデータを送信します。

1. 最初の SSH セッションに戻り、ストリーミング データを保持する新しい Hive テーブルを作成します。 spark-shell で、次のコマンドを入力します。

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. その後、次のコマンドを使用して、新しく作成したテーブルにストリーミング データを書き込みます。

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format("com.hortonworks.spark.sql.hive.llap.streaming.HiveStreamingDataSource").option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > 現在、`metastoreUri` および `database` オプションは、Apache Spark の既知の問題のため、手動で設定する必要があります。 この問題の詳細については、[SPARK-25460](https://issues.apache.org/jira/browse/SPARK-25460) を参照してください。

1. 2番目の SSH セッションに戻り、次の値を入力します。

    ```bash
    foo
    HiveSpark
    bar
    ```

1. 最初の SSH セッションに戻り、この簡単なアクティビティに注目します。 次のコマンドを使用して、データを表示します。

    ```scala
    hive.table("stream_table").show()
    ```

2番目の SSH セッションで、**Ctrl + C** を使用して netcat を停止します。 最初の SSH セッションで、`:q` を使用して spark-shell を終了します。

## <a name="next-steps"></a>次のステップ

* [Apache Spark および Apache Hive との HWC の統合](./apache-hive-warehouse-connector.md)
* [HDInsight での対話型クエリの使用](./apache-interactive-query-get-started.md)
* [HWC と Apache Zeppelin の統合](./apache-hive-warehouse-connector-zeppelin.md)
