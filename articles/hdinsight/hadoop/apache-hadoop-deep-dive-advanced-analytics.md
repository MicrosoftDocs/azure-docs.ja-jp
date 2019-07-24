---
title: 詳細情報 - 高度な分析 - Azure HDInsight
description: 高度な分析において、どのようにアルゴリズムを使用してビッグ データを処理するのかについて説明します。
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: 893273fc325eaf878814c07eb095c67f5ea3c3f1
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/22/2019
ms.locfileid: "56675060"
---
# <a name="deep-dive---advanced-analytics"></a>詳細情報 - 高度な分析

## <a name="what-is-advanced-analytics-for-hdinsight"></a>HDInsight の高度な分析とは

HDInsight を使用すると、大量の構造化されたデータ、構造化されていないデータ、動きの速いデータから、価値のある分析情報を得ることができます。 高度な分析では、スケーラビリティの高いアーキテクチャ、統計モデルと機械学習モデル、インテリジェント ダッシュボードを使用することで、示唆に富んだ分析情報を提供します。 機械学習、あるいは *"予測分析"* は、データ内のリレーションシップを特定して学習するアルゴリズムを使用し、予測を行ってユーザーを決断へと導きます。

## <a name="advanced-analytics-process"></a>高度な分析のプロセス

![Process](./media/apache-hadoop-deep-dive-advanced-analytics/process.png)

ビジネス上の問題を特定し、データの収集と処理を開始したら、予測する課題を象徴するモデルを作成する必要があります。 モデルは 1 つ以上の機械学習アルゴリズムを使用して、お客様のビジネス上のニーズに最適な予測タイプを作成します。  ご利用のデータはテストや評価に使用する分を残して、その大部分をモデルのトレーニングに使用する必要があります。 

使用するモデルを作成、読み込み、テスト、評価したら、次はそのモデルをデプロイして、お客様の質問に対する回答を開始するようにします。 最後にモデルのパフォーマンスを監視して、必要に応じて調整します。 

## <a name="common-types-of-algorithms"></a>アルゴリズムの一般的なタイプ

高度な分析ソリューションは、機械学習アルゴリズムのセットを提供します。 アルゴリズムのカテゴリと、関連する一般的なビジネス ユース ケースの概要を次に示します。

![機械学習のユース ケース](./media/apache-hadoop-deep-dive-advanced-analytics/ml-use-cases.png)

最適なアルゴリズムを選択するとともに、トレーニング用のデータを与える必要があるかどうかについても検討する必要があります。 機械学習アルゴリズムは次のように分類されます。

* 教師あり - 結果を得られるようになるには、ラベル付けされたデータのセットでアルゴリズムをトレーニングする必要があります
* 半教師あり - トレーニングの初期段階では使用できなかったトレーナーによるインタラクティブ クエリを使用して、別のターゲットによってアルゴリズムを強化できます
* 教師なし - アルゴリズムはトレーニング データを必要としません 
* 強化 - アルゴリズムはソフトウェア エージェントを使用して、特定のコンテキスト内での (ロボット工学でよく使用される) 理想的な動作を決定します


| アルゴリズムのカテゴリ| 用途 | 学習タイプ | アルゴリズム |
| --- | --- | --- | -- |
| 分類 | ユーザーまたはモノをグループに分類 | 教師あり | デシジョン ツリー、ロジスティック回帰、ニューラル ネットワーク |
| クラスタリング | 例のセットを同種のグループに分割 | 教師なし | K-means クラスタリング |
| パターンの検出 | データ内の頻繁な関連付けを特定 | 教師なし | 相関ルール |
| 回帰 | 数値の結果を予測 | 教師あり | 線形回帰、ニューラル ネットワーク |
| 強化 | ロボット向けの最適な動作を決定 | 強化 | モンテカルロ シミュレーション、DeepMind |

## <a name="machine-learning-on-hdinsight"></a>HDInsight での機械学習

HDInsight には、次の高度な分析ワークフロー向けの、いくつかの機械学習オプションがあります。

* Machine Learning と Apache Spark
* R および ML Services
* Azure Machine Learning と Apache Hive
* Apache Spark とディープ ラーニング

### <a name="machine-learning-and-apache-spark"></a>Machine Learning と Apache Spark


[HDInsight Spark](../spark/apache-spark-overview.md) は、Azure でホストされる [Apache Spark](https://spark.apache.org/) のサービスであり、メモリ内処理によりビッグ データ分析を向上させる、オープン ソースの統合された並列データ処理フレームワークです。 Spark 処理エンジンは、高速かつ簡単に高度な分析を行うことができるように作成されています。 Spark のメモリ内の分散計算機能により、Machine Learning とグラフ計算に使用される反復的なアルゴリズムに対して、Spark は適切な選択肢となります。 


分散環境にアルゴリズム モデリング機能を提供するスケーラブルな機械学習ライブラリが 3 つあります。

* [**MLlib** ](https://spark.apache.org/docs/latest/ml-guide.html) - MLlib には Spark RDD 上に構築されたオリジナルの API が含まれています。
* [**SparkML** ](https://spark.apache.org/docs/1.2.2/ml-guide.html) - SparkML は、ML パイプラインを構築するために Spark DataFrame 上に構築された高度な API を提供する、比較的新しいパッケージです。
* [**MMLSpark** ](https://github.com/Azure/mmlspark) - Microsoft Machine Learning library for Apache Spark (MMLSpark) は、データ サイエンティストの Spark 上での生産性を高めること、実験速度を向上させること、およびディープ ラーニングなどの非常に大規模なデータセットに対して、機械学習の最先端の手法を利用できることを目標に設計されています。 MMLSpark ライブラリは、PySpark でモデルを構築するための一般的なモデリング タスクを簡略化します。 

### <a name="r-and-ml-services"></a>R および ML Services

大量のデータセットとモデルを扱えるよう準備された [ML Services](../r-server/r-server-overview.md) を使用して、HDInsight の一部として HDInsight クラスターを作成できます。 この新しい機能により、データ サイエンティストや統計学の専門家は、使い慣れた R インターフェイスを使用して、HDInsight を介してオンデマンドでスケーリングすることができ、クラスターのセットアップとメンテナンスのオーバーヘッドがなくなります。

### <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning と Apache Hive

[Azure Machine Learning Studio](https://studio.azureml.net/) は、モデルの予測分析ツールを提供するだけでなく、すぐに使用できる Web サービスとして予測モデルをデプロイするための、フル マネージド サービスを提供します。 また、Azure Machine Learning は、クラウドで予測分析の完全なソリューションを作成するためのツールを提供し、予測モデルを迅速に作成、テスト、運用化して管理します。 大規模なアルゴリズム ライブラリの中から選択し、モデルを構築するための Web ベースのスタジオを使用して、ご利用のモデルを簡単に Web サービスとしてデプロイできます。

### <a name="apache-spark-and-deep-learning"></a>Apache Spark とディープ ラーニング

[ディープ ラーニング](https://www.microsoft.com/research/group/dltc/)とは、*ディープ ニューラル ネットワーク* (DNN) を使用する機械学習の分野であり、人間の脳の生物学的プロセスから着想を得たものです。 多くの研究者がディープ ラーニングを人工知能のための有効なアプローチであると考えています。 ディープ ラーニングの例をいくつか挙げると、音声言語翻訳ツール、画像認識システム、機械推定などがあります。 ディープ ラーニングでの独自の作業を効率化できるよう、Microsoft は無料で使いやすいオープンソースの [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) を開発しました。 このツールキットは、さまざまな Microsoft 製品や、ディープ ラーニングを大規模に展開する必要がある世界規模の会社、あるいは最新のアルゴリズムと手法に興味がある学生によって、広範囲に使用されています。 

## <a name="scenario---score-images-to-identify-patterns-in-urban-development"></a>シナリオ - 画像をスコア付けして都市開発におけるパターンを特定する

HDInsight を使用した、高度な分析の機械学習パイプラインの例を見てみましょう。

このシナリオでは、ディープ ラーニング フレームワークである Microsoft の Cognitive Toolkit (CNTK) 内で生成される DNN がどのように運用化され、Azure Blob Storage アカウントに格納されている大量の画像コレクションを HDInsight Spark クラスターの PySpark を使用してスコア付けするのかを見ていきます。 このアプローチは、一般的な DNN のユース ケースや航空画像の分類に適用され、都市開発における最近のパターンを特定するために使用できます。  事前トレーニング済みの画像分類モデルを使用します。 このモデルは [CIFAR-10 データセット](https://www.cs.toronto.edu/~kriz/cifar.html)で事前トレーニングされており、10,000 枚の非公開画像に適用されています。

この高度な分析のシナリオでは、次の 3 つの主要なタスクがあります。

1. Apache Spark 2.1.0 ディストリビューションを使用して、Azure HDInsight Hadoop クラスターを作成する。 
2. Microsoft Cognitive Toolkit を Azure HDInsight Spark クラスターのすべてのノードにインストールするカスタム スクリプトを実行する。 
3. 事前構築済みの Jupyter Notebook を、ご利用の HDInsight Spark クラスターにアップロードし、トレーニング済みの Microsoft Cognitive Toolkit ディープ ラーニング モデルを Spark Python API (PySpark) を使用して Azure Blob Storage アカウント内のファイルに適用する。 

この例では、Alex Krizhevsky、Vinod Nair、Geoffrey Hinton らによって編集と配布が行なわれている CIFAR-10 の画像セットを使用します。 CIFAR-10 のデータセットには、相互排他的な 10 個のクラスに属している 32×32 のカラー画像が 60,000 枚含まれています。

![イメージ](./media/apache-hadoop-deep-dive-advanced-analytics/ml-images.png)

このデータセットの詳細については、Alex Krizhevsky の『[Learning Multiple Layers of Features from Tiny Images (小さな画像から特徴を学習するマルチ レイヤー)](https://www.cs.toronto.edu/~kriz/learning-features-2009-TR.pdf)』をご覧ください。

このデータセットは、50,000 枚の画像のトレーニング セットと 10,000 枚の画像のテスト セットに分割されています。 最初のセットは、Cognitive Toolkit の GitHub リポジトリにある[こちらのチュートリアル](https://github.com/Microsoft/CNTK/tree/master/Examples/Image/Classification/ResNet)に従い、Microsoft Cognitive Toolkit を使用して 20 層の畳み込み Deep Residual Network (ResNet) モデルをトレーニングするために使用されています。 残りの 10,000 枚の画像は、モデルの精度をテストするために使用されています。 ここで分散コンピューティングが関与します。画像の前処理とスコア付けのタスクは、高度に並列化することが可能です。 使用可能な保存されたトレーニング済みモデルとともに、次のものを使用します。

* PySpark を使用して、画像とトレーニング済みモデルをクラスターのワーカー ノードに分散します。
* Python を使用して、HDInsight Spark クラスターの各ノード上で画像を前処理します。
* Cognitive Toolkit を使用して、モデルを読み込み、前処理された画像を各ノード上でスコア付けします。
* Jupyter Notebooks を使用して、PySpark スクリプトを実行して結果を集計し、[Matplotlib](https://matplotlib.org/) を使用してそのモデルのパフォーマンスを視覚化します。

10,000 枚の画像の前処理とスコア付け全体でかかる時間は、4 つのワーカー ノードを持つ 1 つのクラスターで 1 分未満です。 このモデルは、最大 9,100 枚 (91%) の画像のラベルを正確に予測します。 最もよく見られた分類エラーが混同行列から分かります。 たとえば、犬を猫としたり猫を犬として誤ってラベル付けしたりすることが、他のラベル ペアよりも頻繁に発生するということが混同行列で示されます。

![結果](./media/apache-hadoop-deep-dive-advanced-analytics/ml-results.png)

### <a name="try-it-out"></a>実際に使ってみてください

[こちらのチュートリアル](../spark/apache-spark-microsoft-cognitive-toolkit.md)に従って、このソリューションを最初から最後まで (HDInsight Spark クラスターのセットアップ、Cognitive Toolkit のインストール、10,000 枚の CIFAR 画像をスコア付けする Jupyter Notebook の実行まで) 実装してみてください。

## <a name="next-steps"></a>次の手順

Apache Hive と Azure Machine Learning

* [Apache Hive と Azure Machine Learning の詳細](../../machine-learning/team-data-science-process/hive-walkthrough.md)
* [1 TB データセットでの Azure HDInsight Hadoop クラスターの使用](../../machine-learning/team-data-science-process/hive-criteo-walkthrough.md)

Apache Spark と MLLib

* [HDInsight での Apache Spark を使用した機械学習](../../machine-learning/team-data-science-process/spark-overview.md)
* [Apache Spark と Machine Learning:HDInsight で Apache Spark を使用して、HVAC データを使用して建物の温度を分析する](../spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark と Machine Learning:HDInsight で Apache Spark を使用して食品の検査結果を予測する](../spark/apache-spark-machine-learning-mllib-ipython.md)

ディープ ラーニング、Cognitive Toolkit など

* [Azure データ サイエンス仮想マシン](../../machine-learning/data-science-virtual-machine/overview.md)
* [Azure HDInsight での H2O.ai の導入](https://azure.microsoft.com/blog/introducing-h2o-ai-with-on-azure-hdinsight-to-bring-the-most-robust-ai-platform-for-enterprises/)
