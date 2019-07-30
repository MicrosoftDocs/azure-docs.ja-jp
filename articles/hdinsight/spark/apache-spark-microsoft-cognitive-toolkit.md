---
title: ディープ ラーニング用の Microsoft Cognitive Toolkit と Azure HDInsight Spark
description: Azure HDInsight Spark クラスターで Spark Python API を使用して、トレーニング済みの Microsoft Cognitive Toolkit ディープ ラーニング モデルをデータセットに適用する方法について説明します。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: hrasheed
ms.openlocfilehash: aaa690b62b44f5f21db0861d99d45734cf210db0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448692"
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Azure HDInsight Spark クラスターで Microsoft Cognitive Toolkit ディープ ラーニング モデルを使用する

この記事では、次の手順を実行します。

1. [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) を Azure HDInsight Spark クラスターにインストールするカスタム スクリプトを実行する。

2. [Jupyter Notebook](https://jupyter.org/) を [Apache Spark](https://spark.apache.org/) クラスターにアップロードし、トレーニング済みの Microsoft Cognitive Toolkit ディープ ラーニング モデルを [Spark Python API (PySpark)](https://spark.apache.org/docs/0.9.0/python-programming-guide.html) を使用して Azure Blob Storage アカウント内のファイルに適用する方法を確認する。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 この記事を開始する前に、Azure サブスクリプションが必要です。 「[無料の Azure アカウントを今すぐ作成しましょう](https://azure.microsoft.com/free)」をご覧ください。

* **Azure HDInsight Spark クラスター**. この記事では、Spark 2.0 クラスターを作成します。 手順については、[Azure HDInsight での Apache Spark クラスターの作成](apache-spark-jupyter-spark-sql.md)に関するページを参照してください。

## <a name="how-does-this-solution-flow"></a>このソリューションの流れ

このソリューションは、この記事と、この記事の一部としてアップロードする Jupyter Notebook に分割されます。 この記事では、次の手順を完了します。

* HDInsight Spark クラスターでスクリプト アクションを実行して、Microsoft Cognitive Toolkit と Python のパッケージをインストールします。
* HDInsight Spark クラスターに対してソリューションを実行する Jupyter Notebook をアップロードします。

次の残りの手順は、Jupyter Notebook に関する記事で説明します。

- サンプル イメージを Spark Resiliant Distributed Dataset (RDD) に読み込む。
   - モジュールを読み込んでプリセットを定義する。
   - データセットを Spark クラスターにローカルにダウンロードする。
   - データセットを RDD に変換する。
- トレーニング済みの Cognitive Toolkit モデルを使用してイメージをスコア付けする。
   - トレーニング済みの Cognitive Toolkit モデルを Spark クラスターにダウンロードする。
   - ワーカー ノードで使用される関数を定義する。
   - ワーカー ノードでイメージをスコア付けする。
   - モデルの精度を評価する。


## <a name="install-microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit をインストールする

スクリプト アクションを使用して Spark クラスターに Microsoft Cognitive Toolkit をインストールします。 スクリプト アクションは、既定で使用できないクラスターにコンポーネントをインストールするためにカスタム スクリプトを使用します。 カスタム スクリプトは、Azure portal から使用するか、HDInsight .NET SDK または Azure PowerShell によって使用できます。 スクリプトを使用して、ツールキットのインストールをクラスター作成の一環として、またはクラスターの稼働後に実行することもできます。 

この記事では、クラスターの作成後にポータルを使用してツールキットをインストールします。 カスタム スクリプトを実行する他の方法については、[スクリプト アクションを使用した HDInsight クラスターのカスタマイズ](../hdinsight-hadoop-customize-cluster-linux.md)に関するページを参照してください。

### <a name="using-the-azure-portal"></a>Azure ポータルの使用

Azure portal を使用してスクリプト アクションを実行する方法については、[スクリプト アクションを使用した HDInsight クラスターのカスタマイズ](../hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)に関するページを参照してください。 Microsoft Cognitive Toolkit をインストールするには、次の入力を行ってください。

* スクリプト アクション名の値を指定します。

* **[バッシュ スクリプト URI]** には次を入力します。`https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`

* ヘッド ノードとワーカー ノードでのみスクリプトを実行し、他のすべてのチェック ボックスはオフにします。

* **Create** をクリックしてください。

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>Jupyter Notebook を Azure HDInsight Spark クラスターにアップロードする

Azure HDInsight Spark クラスターで Microsoft Cognitive Toolkit を使用するには、Jupyter Notebook **CNTK_model_scoring_on_Spark_walkthrough.ipynb** を Azure HDInsight Spark クラスターに読み込む必要があります。 このノートブックは [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration) の GitHub から入手できます。

1. GitHub リポジトリ [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration) を複製します。 複製の手順については、「[Cloning a repository (リポジトリの複製)](https://help.github.com/articles/cloning-a-repository/)」を参照してください。

2. Azure portal から、既にプロビジョニングしている Spark クラスターのブレードを開き、 **[クラスター ダッシュボード]** をクリックし、 **[Jupyter Notebook]** をクリックします。

    Jupyter Notebook は URL `https://<clustername>.azurehdinsight.net/jupyter/` に移動して起動することもできます。 \<clustername> を、使用する HDInsight クラスターの名前に置き換えます。

3. Jupyter Notebook で、右上隅にある **[アップロード]** をクリックした後、GitHub リポジトリを複製した場所に移動します。

    ![Jupyter Notebook を Azure HDInsight Spark クラスターにアップロードする](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "Jupyter Notebook を Azure HDInsight Spark クラスターにアップロードする")

4. もう 1 度 **[アップロード]** をクリックします。

5. ノートブックのアップロード後、ノートブックの名前をクリックします。データセットの読み込みとこの記事の実行方法については、ノートブック自体の指示に従います。

## <a name="see-also"></a>関連項目
* [概要:Azure HDInsight での Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>シナリオ
* [Apache Spark と BI:HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](apache-spark-use-bi-tools.md)
* [Apache Spark と Machine Learning:HDInsight で Spark を使用して、HVAC データを使用して建物の温度を分析する](apache-spark-ipython-notebook-machine-learning.md)
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

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md
