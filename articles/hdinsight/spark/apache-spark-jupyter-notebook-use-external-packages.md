---
title: Spark 上で Jupyter でのカスタム Maven パッケージを使用する - Azure HDInsight
description: HDInsight Spark クラスター内の Jupyter Notebook でカスタム Maven パッケージを使用するための構成手順を説明します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: 3ddfdfdfe10d5b6ea7c2d5cd99d325564163c0dd
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866014"
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>HDInsight の Apache Spark クラスターの Jupyter Notebook で外部のパッケージを使用する

HDInsight 上の Apache Spark クラスター内の [Jupyter Notebook](https://jupyter.org/) を、そのクラスターに標準では含まれていない、コミュニティから提供された外部の Apache **maven** パッケージを使用するように構成する方法について説明します。

利用できるすべてのパッケージは、 [Maven リポジトリ](https://search.maven.org/) で検索できます。 公開されているパッケージの一覧を他のソースから入手してもかまいません。 たとえば、コミュニティから提供されている全パッケージの一覧を [Spark Packages](https://spark-packages.org/)で入手できます。

この記事では、Jupyter Notebook で [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) パッケージを使用する方法について説明します。

## <a name="prerequisites"></a>前提条件

* HDInsight での Apache Spark クラスター。 手順については、「 [Create Apache Spark clusters in Azure HDInsight (Azure HDInsight での Apache Spark クラスターの作成)](apache-spark-jupyter-spark-sql.md)」を参照してください。

* HDInsight の Spark での Jupyter Notebook の使用方法を熟知していること。 詳細については、[HDInsight の Apache Spark を使用したデータの読み込みとクエリの実行](./apache-spark-load-data-run-query.md)に関するページを参照してください。

* クラスターのプライマリ ストレージの [URI スキーム](../hdinsight-hadoop-linux-information.md#URI-and-scheme)。 Azure Storage では `wasb://`、Azure Data Lake Storage Gen2 では `abfs://`、Azure Data Lake Storage Gen1 では `adl://` です。 Azure Storage または Data Lake Storage Gen2 で安全な転送が有効になっている場合、URI はそれぞれ `wasbs://` または `abfss://` になります。[安全な転送](../../storage/common/storage-require-secure-transfer.md)に関するページも参照してください。

## <a name="use-external-packages-with-jupyter-notebooks"></a>Jupyter Notebook で外部のパッケージを使用する

1. `https://CLUSTERNAME.azurehdinsight.net/jupyter` に移動します。`CLUSTERNAME` はご自身の Spark クラスターの名前です。

1. 新しい Notebook を作成します。 **[新規]** を選択した後、**[Spark]** を選択します。

    :::image type="content" source="./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png " alt-text="新しい Spark Jupyter Notebook を作成する" border="true":::

1. Untitled.pynb という名前の新しい Notebook が作成されて開かれます。 上部のノートブック名を選択し、わかりやすい名前を入力します。

    :::image type="content" source="./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png " alt-text="Notebook の名前を指定する" border="true":::

1. 外部のパッケージを使用するようにノートブックを構成するには、`%%configure` マジックを使用します。 外部のパッケージを使用するノートブックでは必ず、最初のコード セルで `%%configure` マジックを呼び出すようにしてください。 そうすることでセッションが開始される前に、指定のパッケージを使用するようにカーネルが構成されます。

    >[!IMPORTANT]  
    >最初のセルでカーネルを構成しなかった場合、`-f` パラメーターを指定して `%%configure` を使用できますが、その場合セッションが最初からやり直しとなり、すべての進捗が失われます。

    | HDInsight のバージョン | コマンド |
    |-------------------|---------|
    | HDInsight 3.5 および HDInsight 3.6 | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.11:1.5.0" }}`|
    |HDInsight 3.3 および HDInsight 3.4 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|

1. 上のスニペットには、Maven Central Repository における外部パッケージの maven コーディネートを指定します。 このスニペットの `com.databricks:spark-csv_2.11:1.5.0` は、 **spark-csv** パッケージの maven コーディネートです。 パッケージのコーディネートは、以下の方法で構築します。

    a. Maven リポジトリから目的のパッケージを探します。 この記事では [spark-csv](https://mvnrepository.com/artifact/com.databricks/spark-csv) を使用します。

    b. リポジトリで **GroupId**、**ArtifactId**、**Version** の値を確認します。 収集した値が、クラスターに一致することを確認します。 この例では、Scala 2.11 と Spark 1.5.0 パッケージを使用していますが、クラスター内の Scala または Spark のバージョンに応じて別のバージョンを選択しなければならないことがあります。 クラスター上の Scala のバージョンを確認するには、Spark Jupyter カーネルまたは Spark 送信に対して `scala.util.Properties.versionString` を実行します。 クラスター上の Spark のバージョンを確認するには、Jupyter Notebook に対して `sc.version` を実行します。

    :::image type="content" source="./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png " alt-text="Jupyter Notebook で外部のパッケージを使用する" border="true":::

    c. 3 つの値をコロン ( **:** ) で区切って連結します。

    ```scala
    com.databricks:spark-csv_2.11:1.5.0
    ```

1. `%%configure` マジックのコード セルを実行します。 これで、指定したパッケージを使用するように基になる Livy セッションが構成されます。 これでノートブック内の後続セルで、指定したパッケージを使用できるようになります (以下の例を参照)。

    ```scala
    val df = spark.read.format("com.databricks.spark.csv").
    option("header", "true").
    option("inferSchema", "true").
    load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

    HDInsight 3.4 以下の場合、次のスニペットをご利用ください。

    ```scala
    val df = sqlContext.read.format("com.databricks.spark.csv").
    option("header", "true").
    option("inferSchema", "true").
    load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

1. 前の手順で作成したデータフレームからのデータは、以下のスニペットで表示できます。

    ```scala
    df.show()
   
    df.select("Time").count()
    ```

## <a name="see-also"></a><a name="seealso"></a>関連項目

* [概要: Azure HDInsight での Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>シナリオ

* [Apache Spark と BI:HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](apache-spark-use-bi-tools.md)
* [Apache Spark と Machine Learning: HDInsight で Spark を使用して、HVAC データを使用して建物の温度を分析する](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark と Machine Learning:HDInsight で Spark を使用して食品の検査結果を予測する](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight 上での Apache Spark を使用した Web サイト ログ分析](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>アプリケーションの作成と実行

* [Scala を使用してスタンドアロン アプリケーションを作成する](apache-spark-create-standalone-application.md)
* [Apache Livy を使用して Apache Spark クラスターでジョブをリモートから実行する](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>ツールと拡張機能

* [HDInsight Linux の Apache Spark クラスターの Jupyter Notebook で外部の Python パッケージを使用する](apache-spark-python-package-installation.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark Scala アプリケーションを作成し、送信する](apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Apache Spark アプリケーションをリモートでデバッグする](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight 上の Apache Spark クラスターで Apache Zeppelin Notebook を使用する](apache-spark-zeppelin-notebook.md)
* [HDInsight 用の Apache Spark クラスター内の Jupyter Notebook で使用可能なカーネル](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>リソースの管理

* [Azure HDInsight での Apache Spark クラスターのリソースの管理](apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](apache-spark-job-debugging.md)
