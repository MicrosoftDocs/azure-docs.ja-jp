---
title: "Azure Machine Learning とは | Microsoft Docs"
description: "Azure Machine Learning 実験およびモデル管理の概要。これは、高度な分析アプリケーションの開発、実験、デプロイをクラウド規模で行うプロフェッショナルなデータ サイエンティスト向けの統合されたエンドツーエンドのデータ サイエンス ソリューションです。"
services: machine-learning
author: haining
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: get-started-article
ms.date: 09/21/2017
ms.openlocfilehash: 3bf9227a7ee432f036c57dd9d1c3807c7a867f3f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-machine-learning"></a>Azure Machine Learning とは

Azure Machine Learning は、統合されたエンドツーエンドのデータ サイエンスおよび高度な分析ソリューションです。 データ サイエンティストは、このソリューションを使用してデータの準備、実験の開発、モデルのデプロイをクラウド規模で行うことができます。

Azure Machine Learning の主要なコンポーネントは次のとおりです。
- Azure Machine Learning Workbench
- Azure Machine Learning 実験サービス
- Azure Machine Learning モデル管理サービス
- Microsoft Machine Learning Library for Apache Spark (MMLSpark ライブラリ)
- Visual Studio Code Tools for AI

これらのアプリケーションとサービスが一体となって、データ サイエンス プロジェクトの開発とデプロイを大幅に加速する助けとなります。 

![Azure Machine Learning の概念](media/overview-what-is-azure-ml/aml-concepts.png)

## <a name="open-source-compatible"></a>オープン ソースとの互換性

Azure Machine Learning では、オープン ソース テクノロジが完全にサポートされます。 以下の機械学習フレームワークなど、数万のオープン ソース Python パッケージを使用できます。

- [scikit-learn](http://scikit-learn.org/)
- [TensorFlow](https://www.tensorflow.org/)
- [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/)
- [Spark ML](https://spark.apache.org/docs/2.1.1/ml-pipeline.html)

Docker コンテナーや Spark クラスターなどの管理された環境で実験を実行することができます。 また、[Azure の GPU に対応する仮想マシン](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu)などの高度なハードウェアを使用して、実行を高速化することもできます。

Azure Machine Learning は、次のオープン ソース テクノロジの上に構築されています。

- [Jupyter Notebook](http://jupyter.org/)
- [Apache Spark](https://spark.apache.org/)
- [Docker](https://www.docker.com/)
- [Kubernetes](https://kubernetes.io/)
- [Python](https://www.python.org/)
- [Conda](https://conda.io/docs/)

これには、[Microsoft Machine Learning Library for Apache Spark](https://github.com/Azure/mmlspark) や Cognitive Toolkit など、Microsoft のオープン ソース テクノロジも含まれています。

さらに、大規模な問題を解決するために Microsoft が設計および開発した、最新の実証済みの機械学習テクノロジの恩恵を受けることもできます。 これらは、次のような多くの Microsoft 製品で実際に使用されています。

- Windows
- Bing
- Xbox
- Office
- SQL Server

Azure Machine Learning に含まれている Microsoft の機械学習テクノロジの一部を次に示します。

- [PROSE](https://microsoft.github.io/prose/) (PROgram Synthesis using Examples)
- [microsoftml](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package)
- [revoscalepy](https://docs.microsoft.com/r-server/python-reference/revoscalepy/revoscalepy-package)

## <a name="azure-machine-learning-workbench"></a>Azure Machine Learning Workbench
Azure Machine Learning Workbench は、Windows と macOS の両方でサポートされるデスクトップ アプリケーションとコマンドライン ツールです。 これを使用して、データ サイエンスのライフ サイクル全体で機械学習ソリューションを管理することができます。

- データの取り込みと準備
- モデルの開発と実験の管理
- さまざまなターゲット環境でのモデルのデプロイ

Azure Machine Learning Workbench によって提供される主な機能を次に示します。

- データ変換ロジックを例で学習できるデータ準備ツール。
- UX および Python コードを通じてアクセス可能なデータ ソース抽象化。
- 視覚的に構築されたデータ準備パッケージを呼び出すための Python SDK。
- 組み込みの Jupyter Notebook サービスとクライアント UX。
- 実行履歴ビューによる実験の監視と管理。
- Azure Active Directory を介して共有とコラボレーションを可能にするロールベースのアクセス制御。
- 実行ごとの自動プロジェクト スナップショット作成、およびネイティブ Git 統合による明示的なバージョン管理。 
- 一般的な Python IDE との統合。

詳細については、次の記事を参照してください。
- [データ準備ユーザー ガイド](data-prep-user-guide.md)
- [Azure Machine Learning で Git を使用する](using-git-ml-project.md)
- [Azure Machine Learning で Jupyter Notebook を使用する](how-to-use-jupyter-notebooks.md)
- ローミングと共有
- [実行履歴ガイド](how-to-use-run-history-model-metrics.md)
- [IDE 統合](how-to-configure-your-IDE.md)

## <a name="azure-machine-learning-experimentation-service"></a>Azure Machine Learning 実験サービス
実験サービスでは、機械学習実験の実行が処理されます。 また、プロジェクト管理、Git 統合、アクセス制御、ローミング、共有により、Workbench もサポートされます。 

ユーザーは、簡単な構成を通じて、さまざまなコンピューティング環境オプションで実験を実行できます。

- ローカル ネイティブ
- ローカル Docker コンテナー
- リモート VM 上の Docker コンテナー
- Azure のスケールアウト Spark クラスター

実験サービスでは、スクリプトを分離して実行して再現性のある結果を得られるようにするために仮想環境が構築されます。 実行履歴情報が記録され、その履歴が視覚的に表示されます。 ユーザーは、実行した実験の中から最良のモデルを簡単に選択することができます。 

詳細については、[実験の実行の構成](experiment-execution-configuration.md)に関するページを参照してください。

## <a name="azure-machine-learning-model-management-service"></a>Azure Machine Learning モデル管理サービス

モデル管理サービスは、データ サイエンティストと DevOps チームがさまざまな環境に予測モデルをデプロイすることを可能にします。 モデルのバージョンと系列は、トレーニングの実行からデプロイまで追跡されます。 モデルは、クラウドに保存、登録、管理されます。 

単純な CLI コマンドを使用して、モデル、スコア付けスクリプト、依存関係を Docker イメージにコンテナー化できます。 これらのイメージは、Azure (Azure Container Registry) でホストされる独自の Docker レジストリに登録されます。 これらは、次のターゲットを確実にデプロイすることができます。

- ローカル マシン
- オンプレミスのサーバー
- クラウド
- IoT エッジ デバイス

Azure Container Service (ACS) で動作する Kubernetes は、クラウドのスケールアウト デプロイに使用されます。 視覚的な分析のために、AppInsights でモデル実行テレメトリがキャプチャされます。 

モデル管理サービスの詳細については、[モデル管理の概要](model-management-overview.md)に関するページを参照してください。


## <a name="microsoft-machine-learning-library-for-apache-spark"></a>Microsoft Machine Learning Library for Apache Spark

[MMLSpark](https://github.com/Azure/mmlspark) (Microsoft Machine Learning Library for Apache Spark) は、Apache Spark 向けのディープ ラーニングおよびデータ サイエンス ツールを提供するオープン ソースの Spark パッケージです。 このパッケージによって、[Spark Machine Learning Pipelines](https://spark.apache.org/docs/2.1.1/ml-pipeline.html) が [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) および [OpenCV](http://opencv.org/) ライブラリと統合されます。 これを使用することで、大きな画像およびテキスト データセットに対する強力かつスケーラブルな予測モデルと分析モデルをすぐに作成できます。 いくつかの特徴を次に示します。

- HDFS から Spark DataFrame に画像を簡単に取り込む
- OpenCV からの変換を使用して画像データを前処理する
- Microsoft Cognitive Toolkit を使用して、事前にトレーニングされたディープ ニューラル ネットを使用して画像の特徴を生成する 
- Keras の事前にトレーニングされた双方向 LSTM を使用して、医療エンティティを抽出する
- Azure 上の N シリーズ GPU VM で DNN ベースの画像分類モデルをトレーニングする
- 単一のトランスフォーマーを介して SparkML のプリミティブ上で使いやすい API を使用して、自由形式のテキスト データの特徴を生成する
- 暗黙的なデータの特徴の生成を介して分類モデルと回帰モデルをトレーニングする
- インスタンスごとのメトリックなどの豊富な評価メトリックを計算する

詳細については、[Azure Machine Learning での MMLSpark の使用](how-to-use-mmlspark.md)に関するページを参照してください。

## <a name="visual-studio-code-tools-for-ai"></a>Visual Studio Code Tools for AI
Visual Studio Code Tools for AI は、ディープ ラーニングおよび AI ソリューションを構築、テスト、デプロイするための Visual Studio Code の拡張機能です。 このツールには、以下を含む Azure Machine Learning との多くの統合ポイントが用意されています。
- 実行履歴ビューには、トレーニングの実行のパフォーマンスと記録されたメトリックが表示されます。
- ギャラリー ビューでは、ユーザーは、Microsoft Cognitive Toolkit、TensorFlow、その他の多数のディープ ラーニング フレームワークを使用して、新しいプロジェクトを参照およびブートストラップできます。 
- エクスプローラー ビューでは、実行するスクリプトのコンピューティング ターゲットを選択できます。
 

## <a name="what-are-the-machine-learning-options-from-microsoft"></a>Microsoft の機械学習オプション
Azure には、Azure Machine Learning に加えて、機械学習モデルを構築、デプロイ、管理するためのさまざまなオプションが用意されています。 
* SQL Server の Microsoft Machine Learning サービス
* Microsoft Machine Learning Server
* データ サイエンス仮想マシン
* HDInsight の Spark MLLib
* Batch AI トレーニング サービス
* Microsoft Cognitive Toolkit
* Microsoft Cognitive Services


### <a name="microsoft-machine-learning-services-in-sql-server"></a>SQL Server の Microsoft Machine Learning サービス
[Microsoft Machine Learning サービス](https://docs.microsoft.com/sql/advanced-analytics/r/r-services)を使用すると、R または Python を使用して機械学習モデルを実行、トレーニング、デプロイできます。 オンプレミスおよび SQL Server データベースにあるデータを使用できます。 

Microsoft Machine Learning サービスは、モデルをオンプレミスまたは Microsoft SQL Server 内でトレーニングまたはデプロイする必要がある場合に使用します。 Machine Learning サービスで構築されたモデルは、Azure Machine Learning モデル管理を使用してデプロイできます。 

### <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server 
[Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone) は、R プロセスと Python プロセスの並列および分散ワークロードをホストして管理するためのエンタープライズ サーバーです。 Microsoft Machine Learning Server は、Linux、Windows、Hadoop、Apache Spark で動作します。 さらに、[HDInsight](https://azure.microsoft.com/services/hdinsight/r-server/) でも利用できます。 Microsoft Machine Learning Server は、[Microsoft Machine Learning パッケージ](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package)を使用して構築されたソリューションの実行エンジンを提供し、次のシナリオをサポートしてオープン ソースの R と Python を拡張します。

- 高パフォーマンスの分析
- 統計分析
- 機械学習
- 非常に大規模なデータセット

サーバーと共にインストールされる独自のパッケージによって付加価値機能が提供されます。 開発用には、[R Tools for Visual Studio](https://www.visualstudio.com/vs/rtvs/) や [Python Tools for Visual Studio](https://www.visualstudio.com/vs/python/) などの IDE を使用することができます。

Microsoft Machine Learning Server は、次の操作を行う必要がある場合に使用します。

- R と Python で構築されたモデルを作成してサーバーにデプロイする
- R および Python トレーニングを Hadoop または Spark クラスターに大規模に配布する

### <a name="data-science-virtual-machine"></a>データ サイエンス仮想マシン
[データ サイエンス仮想マシン (DSVM)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) とは、データ サイエンス専用に構築された Microsoft の Azure クラウド上にあるカスタマイズされた VM イメージです。 多くのよく使われるデータ サイエンス ツールや他のツールが事前にインストールおよび構成されており、高度な分析のためのインテリジェントなアプリケーションの構築をすぐに始めることができます。 Windows Server と Linux で使用できます。 Windows エディションの DSVM は、Windows Server 2016 と Windows Server 2012 で提供しています。 Linux エディションの DSVM は、Ubuntu 16.04 LTS と OpenLogic 7.2 CentOS ベースの Linux ディストリビューションで提供しています。 

データ サイエンス仮想マシンは、単一のノードでジョブを実行またはホストする必要がある場合や、 1 台のマシンで処理をリモートでスケールアップする必要がある場合に使用します。 データ サイエンス仮想マシンは、Azure Machine Learning 実験と Azure Machine Learning モデル管理の両方のターゲットとしてサポートされています。 

### <a name="spark-mllib-in-hdinsight"></a>HDInsight の Spark MLLib
[HDInsight の Spark MLLib](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-ipython-notebook-machine-learning) を使用すると、ビッグ データに対して実行する Spark ジョブの一部としてモデルを作成できます。 Spark を使用すると、データを簡単に変換して準備し、単一のジョブでモデル作成をスケールアウトできます。 Spark MLLib で作成されたモデルは、Azure Machine Learning モデル管理を使用してデプロイ、管理、監視できます。 トレーニングの実行は、Azure Machine Learning 実験を使用してディスパッチおよび管理できます。 Spark は、Machine Learning Workbench で作成したデータ準備ジョブをスケールアウトするためにも使用できます。 

Spark は、データ処理をスケールアウトし、データ パイプラインの一部としてモデルを作成する必要がある場合に使用します。 Spark ジョブは、Scala、Java、Python、または R で作成できます。 

### <a name="batch-ai-training"></a>Batch AI トレーニング 
[Azure Batch AI トレーニング](https://aka.ms/batchaitraining)は、任意のフレームワークを使用して AI モデルと並行して実験を行い、クラスター化された GPU でそれらを大規模にトレーニングするのに役立ちます。 ジョブの要件と実行する構成を記述すると、残りの処理が自動的に実行されます。 

Batch AI トレーニングを使用すると、次のようなフレームワークを使用して、クラスター化された GPU にディープ ラーニング ジョブをスケールアウトできます。

- Cognitive Toolkit
- Caffe
- Chainer
- TensorFlow

Azure Machine Learning モデル管理を使用すると、Batch AI トレーニングのモデルをデプロイ、管理、監視できます。  Batch AI トレーニングは、将来 Azure Machine Learning 実験と統合される予定です。 

### <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit
[Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) は、有向グラフの計算ステップとしてニューラル ネットワークを記述する、統合されたディープ ラーニング ツールキットです。 この有向グラフでは、リーフ ノードは入力値またはネットワーク パラメーターを表し、他のノードはその入力に対するマトリックス演算を表します。 Cognitive Toolkit を使用すると、フィード転送 DNN、たたみ込みネット (CNN)、再帰型ネットワーク (RNN/LSTM) などの一般的なモデル タイプを簡単に実現し、組み合わせることができます。 また、複数の GPU とサーバーの間での自動的な差別化および並列化を使用して、確率的勾配降下法 (SGD、誤差逆伝播) 学習を実装できます。

Cognitive Toolkit は、ディープ ラーニングを使用してモデルを構築する場合に使用します。  Cognitive Toolkit は、上記のいずれのサービスでも使用できます。

### <a name="microsoft-cognitive-services"></a>Microsoft Cognitive Services
Microsoft Cognitive Services は、自然なコミュニケーション方法が使用されるアプリの構築を可能にする 30 個の API のセットです。 これらの API を使用すると、数行のコードを書くだけで、見る、聞く、話す、理解する、ニーズを解釈する、などの動作が可能なアプリを作成できます。 次のようなインテリジェントな機能をアプリに簡単に追加できます。 

- 感情とセンチメントの検出
- 視覚と音声認識
- 言語の理解
- 知識と検索

Microsoft Cognitive Services は、デバイスとプラットフォームを越えてアプリを開発するために使用できます。 API は、継続的に品質改善され、設定も簡単です。 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>次のステップ
* [Azure Machine Learning をインストールおよび作成する](quickstart-installation.md)
