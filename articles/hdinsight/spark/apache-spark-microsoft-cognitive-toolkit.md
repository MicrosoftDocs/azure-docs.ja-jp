---
title: Apache Spark を利用した Microsoft Cognitive Toolkit - Azure HDInsight
description: Azure HDInsight Spark クラスターで Spark Python API を使用して、トレーニング済みの Microsoft Cognitive Toolkit ディープ ラーニング モデルをデータセットに適用する方法について説明します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/14/2020
ms.openlocfilehash: 6be70f448cec7cf6db2b8152565ad8102d3a3294
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104868751"
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Azure HDInsight Spark クラスターで Microsoft Cognitive Toolkit ディープ ラーニング モデルを使用する

この記事では、次の手順を実行します。

1. [Microsoft Cognitive Toolkit](/cognitive-toolkit/) を Azure HDInsight Spark クラスターにインストールするカスタム スクリプトを実行する。

2. [Jupyter Notebook](https://jupyter.org/) を [Apache Spark](https://spark.apache.org/) クラスターにアップロードし、トレーニング済みの Microsoft Cognitive Toolkit ディープ ラーニング モデルを [Spark Python API (PySpark)](https://spark.apache.org/docs/latest/api/python/index.html) を使用して Azure Blob Storage アカウント内のファイルに適用する方法を確認する。

## <a name="prerequisites"></a>前提条件

* HDInsight での Apache Spark クラスター。 [Apache Spark クラスターの作成](./apache-spark-jupyter-spark-sql-use-portal.md)に関するページを参照してください。

* HDInsight の Spark での Jupyter Notebook の使用方法を熟知していること。 詳細については、[HDInsight の Apache Spark を使用したデータの読み込みとクエリの実行](./apache-spark-load-data-run-query.md)に関するページを参照してください。

## <a name="how-does-this-solution-flow"></a>このソリューションの流れ

このソリューションは、この記事と、この記事の一部としてアップロードする Jupyter Notebook に分割されます。 この記事では、次の手順を完了します。

* HDInsight Spark クラスターでスクリプト アクションを実行して、Microsoft Cognitive Toolkit と Python のパッケージをインストールします。
* HDInsight Spark クラスターに対してソリューションを実行する Jupyter Notebook をアップロードします。

次の残りの手順は、Jupyter Notebook に関する記事に記載されています。

* サンプル イメージを Spark Resilient Distributed Dataset (RDD) に読み込む。
  * モジュールを読み込んでプリセットを定義する。
  * データセットを Spark クラスターにローカルにダウンロードする。
  * データセットを RDD に変換する。
* トレーニング済みの Cognitive Toolkit モデルを使用してイメージをスコア付けする。
  * トレーニング済みの Cognitive Toolkit モデルを Spark クラスターにダウンロードする。
  * ワーカー ノードで使用される関数を定義する。
  * ワーカー ノードでイメージをスコア付けする。
  * モデルの精度を評価する。

## <a name="install-microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit をインストールする

スクリプト アクションを使用して Spark クラスターに Microsoft Cognitive Toolkit をインストールします。 スクリプト操作では、既定で使用できないコンポーネントをクラスターにインストールするためにカスタム スクリプトを使用します。 カスタム スクリプトは、Azure portal から使用するか、HDInsight .NET SDK または Azure PowerShell によって使用できます。 スクリプトを使用して、ツールキットのインストールをクラスター作成の一環として、またはクラスターの稼働後に実行することもできます。

この記事では、クラスターの作成後にポータルを使用してツールキットをインストールします。 カスタム スクリプトを実行する他の方法については、[スクリプト アクションを使用した HDInsight クラスターのカスタマイズ](../hdinsight-hadoop-customize-cluster-linux.md)に関するページを参照してください。

### <a name="using-the-azure-portal"></a>Azure ポータルの使用

Azure portal を使用してスクリプト アクションを実行する方法については、[スクリプト アクションを使用した HDInsight クラスターのカスタマイズ](../hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation)に関するページを参照してください。 Microsoft Cognitive Toolkit をインストールするには、次の入力を行ってください。 スクリプト操作には、次の値を使用します。

|プロパティ |値 |
|---|---|
|スクリプトの種類|- Custom|
|Name| MCT をインストールする|
|Bash スクリプト URI|`https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`|
|ノードの種類:|ヘッド、ワーカー|
|パラメーター|なし|

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>Jupyter Notebook を Azure HDInsight Spark クラスターにアップロードする

Azure HDInsight Spark クラスターで Microsoft Cognitive Toolkit を使用するには、Jupyter Notebook **CNTK_model_scoring_on_Spark_walkthrough.ipynb** を Azure HDInsight Spark クラスターに読み込む必要があります。 このノートブックは [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration) の GitHub から入手できます。

1. [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration) をダウンロードして解凍します。

1. Web ブラウザーから、`https://CLUSTERNAME.azurehdinsight.net/jupyter` に移動します。ここで、`CLUSTERNAME` はクラスターの名前です。

1. Jupyter Notebook で、右上隅にある **[アップロード]** を選択し、ダウンロードに移動して `CNTK_model_scoring_on_Spark_walkthrough.ipynb` ファイルを選びます。

    :::image type="content" source="./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png " alt-text="Jupyter Notebook を Azure HDInsight Spark クラスターにアップロードする" border="true":::

1. もう一度 **[アップロード]** を選択します。

1. ノートブックのアップロード後、ノートブックの名前をクリックします。データセットの読み込みとこの記事の実行方法については、ノートブック自体の指示に従います。

## <a name="see-also"></a>参照

* [概要:Azure HDInsight での Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>シナリオ

* [Apache Spark と BI:HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](apache-spark-use-bi-tools.md)
* [Apache Spark と Machine Learning: HDInsight で Spark を使用して、HVAC データを使用して建物の温度を分析する](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark と Machine Learning:HDInsight で Spark を使用して食品の検査結果を予測する](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight 上での Apache Spark を使用した Web サイト ログ分析](apache-spark-custom-library-website-log-analysis.md)
* [HDInsight での Apache Spark を使用した Application Insight テレメトリ データ分析](apache-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>アプリケーションの作成と実行

* [Scala を使用してスタンドアロン アプリケーションを作成する](apache-spark-create-standalone-application.md)
* [Apache Livy を使用して Apache Spark クラスターでジョブをリモートから実行する](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>ツールと拡張機能

* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark Scala アプリケーションを作成し、送信する](apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Apache Spark アプリケーションをリモートでデバッグする](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight 上の Apache Spark クラスターで Apache Zeppelin Notebook を使用する](apache-spark-zeppelin-notebook.md)
* [HDInsight 用の Apache Spark クラスター内の Jupyter Notebook で使用可能なカーネル](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>リソースの管理

* [Azure HDInsight での Apache Spark クラスターのリソースの管理](apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](apache-spark-job-debugging.md)
