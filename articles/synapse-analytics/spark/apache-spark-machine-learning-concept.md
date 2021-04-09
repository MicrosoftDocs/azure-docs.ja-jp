---
title: Apache Spark による機械学習
description: この記事では、Azure Synapse Analytics で Apache Spark を介して使用可能な、機械学習とデータ サイエンスの機能の概念を概説します。
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 11/13/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.openlocfilehash: 0485f697b9360b0f2dfe94fdf07629978b5127c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98116927"
---
# <a name="machine-learning-with-apache-spark"></a>Apache Spark による機械学習

Azure Synapse Analytics の Apache Spark では、ビッグ データを使用した機械学習が可能であるため、大量の構造化データ、非構造化データ、および高速移動データから貴重な分析情報を得る機能が提供されます。 

このセクションには、探索的データ分析、特徴エンジニアリング、モデル トレーニング、モデル スコアリング、デプロイなど、機械学習ワークフローの概要とチュートリアルが含まれます。  

## <a name="synapse-runtime"></a>Synapse ランタイム 
Synapse ランタイムは、データ サイエンスおよび機械学習用に最適化されている、キュレーションされた環境です。 Synapse ランタイムでは、Azure Machine Learning SDK 内にさまざまな一般的オープンソースのライブラリと ビルドが既定で用意されています。 Synapse ランタイムには、PyTorch、Scikit-learn、XGBoost などの多くの外部ライブラリも含まれています。

使用可能なライブラリおよび関連するバージョンの詳細については、公開済みの[Azure Synapse Analytics ランタイム](../spark/apache-spark-version-support.md)を参照してください。

## <a name="exploratory-data-analysis"></a>探索的データ分析
Azure Synapse Analytics で Apache Spark を使用する場合は、データの視覚化に役立つさまざまな組み込みオプションが用意されています。たとえば、Synapse ノートブック グラフのオプション、Seaborn や Matplotlib などの一般的なオープンソース ライブラリへのアクセス、Synapse SQL および Power BI との統合などです。

データの視覚化とデータ分析のオプションの詳細については、[Azure Synapse Notebook を使用してデータを視覚化する](../spark/apache-spark-data-visualization.md)方法に関する記事を参照してください。

## <a name="feature-engineering"></a>機能エンジニアリング
既定では、Synapse ランタイムに、特徴エンジニアリングに一般的に使用されるライブラリのセットが含まれています。 大規模なデータセットの場合は、特徴エンジニアリングに Spark SQL、MLlib、および Koalas を使用できます。 より小規模のデータセットの場合は、Numpy、Pandas、Scikit-learn などのサードパーティ製ライブラリにも、これらのシナリオに便利な方法が用意されています。

## <a name="train-models"></a>モデルをトレーニングする
Azure Synapse Analytics で Azure Spark を使用して機械学習モデルをトレーニングする場合は、いくつかのオプションがあります。つまり、Apache Spark MLlib、Azure Machine Learning、およびその他のさまざまなオープンソース ライブラリです。 

機械学習の機能の詳細については、[Azure Synapse Analytics でモデルをトレーニングする](../spark/apache-spark-machine-learning-training.md)方法に関する記事を参照してください。

### <a name="sparkml-and-mllib"></a>SparkML と MLlib
Spark のメモリ内の分散計算機能により、Machine Learning とグラフ計算に使用される反復的なアルゴリズムに対して、Spark は適切な選択肢となります。 ```spark.ml``` は、ユーザーが機械学習パイプラインを作成して調整できるようにするための、一貫した高レベルの API のセットを提供します。 ```spark.ml``` の詳細については、「[Apache Spark ML プログラミング ガイド](https://spark.apache.org/docs/1.2.2/ml-guide.html)」を参照してください。

### <a name="azure-machine-learning-automated-ml"></a>Azure Machine Learning の自動 ML
[Azure Machine Learning の自動 ML](../../machine-learning/concept-automated-ml.md) (自動機械学習) は、機械学習モデルを開発するプロセスの自動化に役立ちます。 これにより、データ サイエンティスト、アナリスト、開発は、モデルの品質を維持しながら、高いスケール、効率性、生産性で ML モデルを構築することができます。 Azure Machine Learning 自動 ML SDK を実行するためのコンポーネントは、Synapse ランタイムに直接組み込まれています。

### <a name="open-source-libraries"></a>オープンソース ライブラリ
Azure Synapse Analytics のすべての Apache Spark プールには、事前に読み込まれる人気の高い機械学習ライブラリのセットが付属しています。  既定で含まれている関連する機械学習ライブラリには、次のようなものがあります。

- [Scikit-learn](https://scikit-learn.org/stable/index.html) は、従来の ML アルゴリズム用のとても人気のある単一ノード機械学習ライブラリの 1 つです。 Scikit-learn は、教師ありおよび教師なしの学習アルゴリズムのほとんどをサポートしており、データ マイニングとデータ分析にも使用できます。
  
- [XGBoost](https://xgboost.readthedocs.io/en/latest/) は、デシジョン ツリーとランダム フォレストのトレーニング向けに最適化されたアルゴリズムを含む、人気の高い機械学習ライブラリです。 
  
- [PyTorch](https://pytorch.org/) と [Tensorflow](https://www.tensorflow.org/) は、強力な Python ディープ ラーニング ライブラリです。 Azure Synapse Analytics の Apache Spark プール内で、これらのライブラリを使用して、プール上の Executor の数をゼロに設定することで、単一マシンのモデルを構築できます。 この構成では Apache Spark が機能しませんが、単一マシンのモデルを作成するためのシンプルでコスト効率の高い方法です。

## <a name="track-model-development"></a>モデル開発の追跡
[MLFlow](https://www.mlflow.org/) は、機械学習の実験のライフ サイクルを管理するためのオープンソース ライブラリです。 MLflow Tracking は、トレーニング実行のメトリックとモデル成果物のログ記録と追跡を行う、MLflow のコンポーネントです。 Azure Synapse Analytics と Azure Machine Learning を介して MLFlow 追跡を使用する方法の詳細については、[MLFlow の使用方法](../../machine-learning/how-to-use-mlflow.md)に関するこのチュートリアルを参照してください。

## <a name="model-scoring"></a>モデル スコアリング
モデル スコアリング (推論) は、モデルを使用して予測を行うフェーズです。 SparkML または MLLib によるモデル スコアリングの場合は、ネイティブ Spark の方法を活用して、Spark DataFrame で推論を直接実行できます。 その他のオープンソース ライブラリおよびモデルの種類では、Spark UDF を作成して、大規模なデータセットで推論をスケールアウトすることもできます。 小規模なデータセットでは、ライブラリによって提供される、ネイティブのモデル推論方法を使用することもできます。

## <a name="register-and-serve-models"></a>モデルのデプロイと提供
モデルを登録すると、ワークスペース内のモデルに関するメタデータを保存、バージョン管理、および追跡できます。 モデルのトレーニングが完了したら、[Azure Machine Learning モデル レジストリ](../../machine-learning/concept-model-management-and-deployment.md#register-package-and-deploy-models-from-anywhere)にモデルを登録できます。 登録した後は、ONNX モデルを使用して、専用 SQL プールに格納されている[データを強化する](../machine-learning/tutorial-sql-pool-model-scoring-wizard.md)こともできます。

## <a name="next-steps"></a>次のステップ
Azure Synapse Analytics による機械学習を開始するには、次のチュートリアルを必ずご確認ください。
- [Azure Synapse Notebook でデータを分析する](../spark/apache-spark-data-visualization-tutorial.md)

- [自動 ML を使用して機械学習モデルをトレーニングする](../spark/apache-spark-azure-machine-learning-tutorial.md)

- [Apache Spark MLlib で機械学習モデルをトレーニングする](../spark/apache-spark-machine-learning-mllib-notebook.md)
