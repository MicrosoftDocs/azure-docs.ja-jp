---
title: "ディープ ラーニング用の Microsoft Cognitive Toolkit と Azure HDInsight Spark | Microsoft Docs"
description: "Azure HDInsight Spark クラスターで Spark Python API を使用して、トレーニング済みの Microsoft Cognitive Toolkit ディープ ラーニング モデルをデータセットに適用する方法について説明します。"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 5364a8fabe3ca5d0fdf9aabe296fef085587604e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Azure HDInsight Spark クラスターで Microsoft Cognitive Toolkit ディープ ラーニング モデルを使用する

この記事では、次の手順を実行します。

1. Microsoft Cognitive Toolkit を Azure HDInsight Spark クラスターにインストールするカスタム スクリプトを実行する。

2. Jupyter Notebook をSpark クラスターにアップロードし、トレーニング済みの Microsoft Cognitive Toolkit ディープ ラーニング モデルを [Spark Python API (PySpark)](https://spark.apache.org/docs/0.9.0/python-programming-guide.html) を使用して Azure Blob Storage アカウント内のファイルに適用する方法を確認する。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 このチュートリアルを開始する前に、Azure サブスクリプションが必要です。 「[無料の Azure アカウントを今すぐ作成しましょう](https://azure.microsoft.com/free)」をご覧ください。

* **Azure HDInsight Spark クラスター**. この記事では、Spark 2.0 クラスターを作成します。 手順については、[Azure HDInsight での Apache Spark クラスターの作成](hdinsight-apache-spark-jupyter-spark-sql.md)に関するページを参照してください。

## <a name="how-does-this-solution-flow"></a>このソリューションの流れ

このソリューションは、この記事と、このチュートリアルの一部としてアップロードする Jupyter Notebook に分割されます。 この記事では、次の手順を完了します。

* HDInsight Spark クラスターでスクリプト アクションを実行して、Microsoft Cognitive Toolkit と Python のパッケージをインストールします。
* HDInsight Spark クラスターに対してソリューションを実行する Jupyter Notebook をアップロードします。

次の残りの手順は、Jupyter Notebook に関する記事で説明します。

- サンプル イメージを Spark Resiliant Distributed Dataset (RDD) に読み込む
   - モジュールを読み込んでプリセットを定義する
   - データセットを Spark クラスターにローカルにダウンロードする
   - データセットを RDD に変換する
- トレーニング済みの Cognitive Toolkit モデルを使用してイメージをスコア付けする
   - トレーニング済みの Cognitive Toolkit モデルを Spark クラスターにダウンロードする
   - ワーカー ノードで使用される関数を定義する
   - ワーカー ノードでイメージをスコア付けする
   - モデルの精度を評価する


## <a name="install-microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit をインストールする

スクリプト アクションを使用して Spark クラスターに Microsoft Cognitive Toolkit をインストールします。 スクリプト アクションは、既定で使用できないクラスターにコンポーネントをインストールするためにカスタム スクリプトを使用します。 カスタム スクリプトは、Azure Portal から使用するか、HDInsight .NET SDK または Azure PowerShell によって使用できます。 スクリプトを使用して、ツールキットのインストールをクラスター作成の一環として、またはクラスターの稼働後に実行することもできます。 

この記事では、クラスターの作成後にポータルを使用してツールキットをインストールします。 カスタム スクリプトを実行する他の方法については、[スクリプト アクションを使用した HDInsight クラスターのカスタマイズ](hdinsight-hadoop-customize-cluster-linux.md)に関するページを参照してください。

### <a name="using-the-azure-portal"></a>Azure Portal の使用

Azure Portal を使用してスクリプト アクションを実行する方法については、[スクリプト アクションを使用した HDInsight クラスターのカスタマイズ](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)に関するページを参照してください。 Microsoft Cognitive Toolkit をインストールするには、次の入力を行ってください。

* スクリプト アクション名の値を指定します。

* **[バッシュ スクリプト URI]** には次を入力します。`https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`

* ヘッド ノードとワーカー ノードでのみスクリプトを実行し、他のすべてのチェック ボックスはオフにします。

* **Create** をクリックしてください。

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>Jupyter Notebook を Azure HDInsight Spark クラスターにアップロードする

Azure HDInsight Spark クラスターで Microsoft Cognitive Toolkit を使用するには、Jupyter Notebook **CNTK_model_scoring_on_Spark_walkthrough.ipynb** を Azure HDInsight Spark クラスターに読み込む必要があります。 このノートブックは GitHub の [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration) で入手できます。

1. GitHub リポジトリ [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration) を複製します。 複製の手順については、「[Cloning a repository (リポジトリの複製)](https://help.github.com/articles/cloning-a-repository/)」を参照してください。

2. Azure Portal から、既にプロビジョニングしている Spark クラスターのブレードを開き、**[クラスター ダッシュボード]** をクリックし、**[Jupyter Notebook]** をクリックします。

    Jupyter Notebook は URL `https://<clustername>.azurehdinsight.net/jupyter/` に移動して起動することもできます。 \<clustername> を、使用する HDInsight クラスターの名前に置き換えます。

3. Jupyter Notebook で、右上隅にある **[アップロード]** をクリックした後、GitHub リポジトリを複製した場所に移動します。

    ![Jupyter Notebook を Azure HDInsight Spark クラスターにアップロードする](./media/hdinsight-apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "Jupyter Notebook を Azure HDInsight Spark クラスターにアップロードする")

4. もう 1 度 **[アップロード]** をクリックします。

5. ノートブックのアップロード後、ノートブックの名前をクリックします。データ セットの読み込みとこのチュートリアルの実行方法については、ノートブック自体の指示に従います。

## <a name="see-also"></a>関連項目
* [概要: Azure HDInsight での Apache Spark](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>シナリオ
* [Spark と BI: HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](hdinsight-apache-spark-use-bi-tools.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して HVAC データを基に建物の温度を分析する](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark ストリーミング: リアルタイム ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-eventhub-streaming.md)
* [Website log analysis using Spark in HDInsight (HDInsight での Spark を使用した Web サイト ログ分析)](hdinsight-apache-spark-custom-library-website-log-analysis.md)
* [HDInsight での Spark を使用した Application Insight テレメトリ データ分析](hdinsight-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>アプリケーションの作成と実行
* [Scala を使用してスタンドアロン アプリケーションを作成する](hdinsight-apache-spark-create-standalone-application.md)
* [Livy を使用して Spark クラスターでジョブをリモートで実行する](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>ツールと拡張機能
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark Scala アプリケーションを作成し、送信する](hdinsight-apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark アプリケーションをリモートでデバッグする](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](hdinsight-apache-spark-zeppelin-notebook.md)
* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](hdinsight-apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](hdinsight-apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md
