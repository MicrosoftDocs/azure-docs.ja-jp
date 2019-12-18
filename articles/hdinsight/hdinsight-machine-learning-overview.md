---
title: 機械学習の概要 - Azure HDInsight
description: Azure HDInsight 内のクラスターのためのビッグ データの機械学習オプションの概要。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 4a1e621570f3bf5fdb78a2fd7b29ce0d1b51187f
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951787"
---
# <a name="machine-learning-on-hdinsight"></a>HDInsight での機械学習

HDInsight ではビッグ データでの機械学習が可能であるため、大量 (数ペタバイト、場合によっては数エクサバイト) の構造化、非構造化、および高速移動データから貴重な洞察を得る機能が提供されます。 HDInsight には、SparkML と Apache Spark MLlib、R、Apache Hive、および Microsoft Cognitive Toolkit という複数の機械学習オプションが存在します。

## <a name="sparkml-and-mllib"></a>SparkML と MLlib

[HDInsight Spark](spark/apache-spark-overview.md) は、Azure でホストされる [Apache Spark](https://spark.apache.org/) のサービスであり、ビッグ データ分析を向上させるためのメモリ内処理をサポートする、統合されたオープンソースの並列データ処理フレームワークです。 Spark 処理エンジンは、高速かつ簡単に高度な分析を行うことができるように作成されています。 Spark のメモリ内の分散計算機能により、Machine Learning とグラフ計算に使用される反復的なアルゴリズムに対して、Spark は適切な選択肢となります。 この分散環境にアルゴリズム モデリング機能を提供するスケーラブルな機械学習ライブラリとして、MLlib と SparkML の 2 つが存在します。 MLlib には、RDD 上に構築されたオリジナルの API が含まれています。 SparkML は、ML パイプラインを構成するために DataFrames 上に構築されたより高レベルの API を提供する新しいパッケージです。 SparkML は、まだ MLlib のすべての機能をサポートしているわけではありませんが、MLlib は Spark の標準の機械学習ライブラリと置き換えられています。

Apache Spark 用の Microsoft Machine Learning ライブラリは [MMLSpark](https://github.com/Azure/mmlspark) です。 このライブラリは、Spark 上でのデータ サイエンティストの生産性を高め、実験の速度を向上させ、さらに非常に大規模なデータセットに対してディープ ラーニングを含む最先端の機械学習手法を活用するように設計されています。 文字列のインデックス作成、機械学習アルゴリズムによって予測されるレイアウトへのデータの強制的な移行、特徴ベクトルのアセンブルなどのスケーラブルな ML モデルを構築する場合、MMLSpark は SparkML の低レベルの API の上に 1 つのレイヤーを提供します。 MMLSpark ライブラリはこれらのタスクや、PySpark でモデルを構築するためのその他の一般的なタスクを簡略化します。

## <a name="r"></a>R

[R](https://www.r-project.org/) は現在、世界で最も普及している統計プログラミング言語です。 これは、ユーザー数が 250 万を超え、さらに増加しているコミュニティを抱えるオープンソースのデータ視覚化ツールです。 その増大するユーザー ベースと 8,000 を超える提供パッケージにより、R は、機械学習が必要な多くの企業にとって有望な選択肢です。 大量のデータセットとモデルで使用されるように準備された ML Services を使用して HDInsight クラスターを作成できます。 この機能は、データ サイエンティストや統計学者に、クラスターのセットアップやメンテナンスのオーバーヘッドなしで HDInsight を通してオンデマンドで拡張できる使い慣れた R インターフェイスを提供します。

![R Server での予測のためのトレーニング](./media/hdinsight-machine-learning-overview/training-for-prediction.png)

クラスターのエッジ ノードは、クラスターへの接続と R スクリプトの実行に便利な場所です。  ScaleR の Hadoop Map Reduce または Spark コンピューティングコンテキストを使用して、クラスターのノード間で R スクリプトを実行することもできます。

Spark を使用した HDInsight 上の ML Services により、Spark コンピューティング コンテキストを使用して、クラスターのノード間でトレーニングを並列化できます。 必要に応じて、使用可能なすべてのコアを並列に使用して、エッジ ノード上で直接 R スクリプトを実行できます。 あるいは、クラスター内のすべてのノードにわたって分散された処理を開始するために、エッジ ノードからコードを実行できます。 Spark を使用した HDInsight 上の ML Services では、必要に応じて、オープン ソースの R パッケージからの関数を並列化することも可能です。

## <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning と Apache Hive

Azure Machine Learning には、予測分析をモデル化するためのツールと、予測モデルをすぐに使用できる web サービスとして、デプロイするための完全管理型のサービスが用意されています。 Azure Machine Learning は、予測モデルの作成、テスト、操作可能化、および管理のために使用できる、クラウド内の完全な予測分析ソリューションです。 大規模なアルゴリズム ライブラリの中から選択し、モデルを構築するための Web ベースのスタジオを使用して、ご利用のモデルを簡単に Web サービスとしてデプロイできます。

![Microsoft Azure 機械学習の概要](./media/hdinsight-machine-learning-overview/azure-machine-learning.png)

[Hive クエリ](../machine-learning/team-data-science-process/create-features-hive.md)を使用して、HDInsight Hadoop クラスター内のデータの特徴を作成します。 *特徴エンジニアリング*は、学習プロセスを容易にする生データの特徴を作成することによって、学習アルゴリズムの予測能力を向上させようとします。 Azure Machine Learning Studio (クラシック) から HiveQL クエリを実行し、[データのインポート モジュール](../machine-learning/studio/import-data.md)を使用して、Hive で処理され、Blob Storage に格納されているデータにアクセスできます。

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

[ディープ ラーニング](https://www.microsoft.com/en-us/research/group/dltc/)は、人間の脳の生物学的プロセスから着想を得た、ニューラル ネットワークを使用する機械学習の 1 分野です。 多くの研究者がディープ ラーニングを、人工知能を拡張するための将来性のあるアプローチとして見ています。 ディープ ラーニングの例には、音声言語翻訳機、画像認識システム、および機械推論があります。

ディープ ラーニングにおける独自の機能の進歩に役立つように、Microsoft は無料で、使いやすい、オープンソースの [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) を開発しました。 このツールキットは、ディープ ラーニングを大規模に展開する必要がある世界中の企業や、最新のアルゴリズムと手法に関心がある学生によって、さまざまな Microsoft 製品で使用されています。

## <a name="see-also"></a>関連項目

### <a name="scenarios"></a>シナリオ

* [Apache Spark と Machine Learning:HDInsight で Spark を使用して、HVAC データを使用して建物の温度を分析する](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark と Machine Learning:HDInsight で Spark を使用して食品の検査結果を予測する](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Apache Mahout で映画のレコメンデーションを生成する](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Apache Hive と Azure Machine Learning](../machine-learning/team-data-science-process/create-features-hive.md)
* [Apache Hive と Azure Machine Learning の詳細](../machine-learning/team-data-science-process/hive-walkthrough.md)
* [HDInsight での Apache Spark を使用した機械学習](../machine-learning/team-data-science-process/spark-overview.md)

### <a name="deep-learning-resources"></a>ディープ ラーニングのリソース

* [Azure HDInsight Spark クラスターで Microsoft Cognitive Toolkit ディープ ラーニング モデルを使用する](spark/apache-spark-microsoft-cognitive-toolkit.md)
* [分散型深層学習用に Azure HDInsight Spark で Caffe を使用する](spark/apache-spark-deep-learning-caffe.md)
* [Data Science Virtual Machine (DSVM) でのディープ ラーニングおよび AI フレームワーク](../machine-learning/data-science-virtual-machine/dsvm-deep-learning-ai-frameworks.md)
