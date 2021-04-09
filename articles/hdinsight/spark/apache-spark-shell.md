---
title: Azure HDInsight で対話型の Spark シェルを使用する
description: 対話型の Spark シェルは、一度に 1 つずつ Spark コマンドを実行し、結果を表示するための read-execute-print プロセスを提供します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/10/2020
ms.openlocfilehash: 324852a967b5de015a9b1e9b465d4b4703e573cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98929686"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Spark Shell から Apache Spark を実行する

対話型の [Apache Spark](https://spark.apache.org/) シェルは、一度に 1 つずつ Spark コマンドを実行し、結果を表示する REPL (read-execute-print loop) 環境を提供します。 このプロセスは、開発およびデバッグに役立ちます。 Spark は、そのサポートされている言語 (Scala、Python、R) ごとに 1 つのシェルを提供します。

## <a name="run-an-apache-spark-shell"></a>Apache Spark シェルの実行

1. [ssh コマンド](../hdinsight-hadoop-linux-use-ssh-unix.md)を使用してクラスターに接続します。 次のコマンドを編集して CLUSTERNAME をクラスターの名前に置き換えてから、そのコマンドを入力します。

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Spark には、Scala (spark-shell) 向けと Python (pyspark) 向けのシェルが用意されています。 この SSH セッションでは、次の "*いずれか*" のコマンドを入力します。

    ```bash
    spark-shell

    # Optional configurations
    # spark-shell --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4
    ```

    ```bash
    pyspark

    # Optional configurations
    # pyspark --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4
    ```

    オプション構成を使用する場合は、まず [Apache Spark の OutOfMemoryError 例外](./apache-spark-troubleshoot-outofmemory.md)に関するページを確認してください。

1. 基本的なコマンドの例を次に示します。 該当する言語を選択してください。

    ```spark-shell
    val textFile = spark.read.textFile("/example/data/fruits.txt")
    textFile.first()
    textFile.filter(line => line.contains("apple")).show()
    ```

    ```pyspark
    textFile = spark.read.text("/example/data/fruits.txt")
    textFile.first()
    textFile.filter(textFile.value.contains("apple")).show()
    ```

1. CSV ファイルに対してクエリを実行します。 下のコマンドは `spark-shell` と `pyspark` の両方に使用できます。

    ```scala
    spark.read.csv("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv").show()
    ```

1. CSV ファイルに対してクエリを実行し、結果を変数に格納します。

    ```spark-shell
    var data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")
    ```

    ```pyspark
    data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")
    ```

1. 結果を表示します。

    ```spark-shell
    data.show()
    data.select($"BuildingID", $"Country").show(10)
    ```

    ```pyspark
    data.show()
    data.select("BuildingID", "Country").show(10)
    ```

1. 終了

    ```spark-shell
    :q
    ```

    ```pyspark
    exit()
    ```

## <a name="sparksession-and-sparkcontext-instances"></a>SparkSession インスタンスと SparkContext インスタンス

既定では、Spark シェルを実行すると、SparkSession および SparkContext のインスタンスが自動的にインスタンス化されます。

SparkSession インスタンスにアクセスするには、「`spark`」と入力します。 SparkContext インスタンスにアクセスするには、「`sc`」と入力します。

## <a name="important-shell-parameters"></a>重要なシェル パラメーター

Spark シェル コマンド (`spark-shell` または `pyspark`) は、数多くのコマンドライン パラメーターに対応しています。 パラメーターの完全な一覧を表示するには、スイッチ `--help` を使用して Spark シェルを起動します。 これらのパラメーターの一部は `spark-submit` のみに適用される場合があります (これは、Spark シェルによってラップされます)。

| switch | description | 例 |
| --- | --- | --- |
| --master MASTER_URL | マスター URL を指定します。 HDInsight では、この値は常に `yarn` です。 | `--master yarn`|
| --jars JAR_LIST | ドライバーと Executor のクラスパスに含めるローカル jar のコンマ区切りリスト。 HDInsight では、この一覧は Azure Storage または Data Lake Storage の既定のファイルシステムへのパスで構成されます。 | `--jars /path/to/examples.jar` |
| --packages MAVEN_COORDS | ドライバーと Executor のクラスパスに含める jar の maven コーディネートのコンマ区切りリスト。 ローカルの maven リポジトリ、maven セントラル、`--repositories` で指定された追加のリモート リポジトリの順に検索します。 コーディネートの形式は、*groupId*:*artifactId*:*version* です。 | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --py-files LIST | Python のみについて、PYTHONPATH に配置する .zip、.egg、.py ファイルのコンマ区切りリストです。 | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>次のステップ

- 「[Azure HDInsight での Apache Spark の概要](apache-spark-overview.md)」で概要を確認します。
- Spark クラスターや SparkSQL の操作方法について、「[Azure HDInsight での Apache Spark クラスターの作成](apache-spark-jupyter-spark-sql.md)」を確認します。
- Spark を使用してストリーミング データを処理するアプリケーションの作成方法について、[Apache Spark 構造化ストリーミング](apache-spark-streaming-overview.md)に関する記事を参照します。
