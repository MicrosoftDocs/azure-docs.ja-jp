---
title: Apache Spark を使用して機械学習モデルをトレーニングする
description: Azure Synapse Analytics の Apache Spark を使用して機械学習モデルをトレーニングする
author: midesa
ms.author: midesa
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 09/13/2020
ms.openlocfilehash: 56b9a98eb72b375aacfeb7cb147997028d3d9ba7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98116808"
---
# <a name="train-machine-learning-models"></a>機械学習モデルをトレーニングする
Azure Synapse Analytics の Apache Spark ではビッグ データを使用した機械学習が可能であるため、大量の構造化、非構造化、および高速移動データから貴重な分析情報を得る機能が提供されます。 Azure Synapse Analytics の Azure Spark を使用して機械学習モデルをトレーニングする場合、いくつかのオプションがあります。Apache Spark MLlib、Azure Machine Learning、およびその他のさまざまなオープンソース ライブラリです。 

## <a name="apache-sparkml-and-mllib"></a>Apache SparkML と MLlib
Azure Synapse Analytics の Apache Spark は、Apache Spark を Microsoft がクラウドに実装したものです。 これは、ビッグ データ分析を向上させるためのメモリ内処理をサポートする、統合されたオープンソースの並列データ処理フレームワークを提供します。 Spark 処理エンジンは、高速かつ簡単に高度な分析を行うことができるように作成されています。 Spark のメモリ内の分散計算機能により、Machine Learning とグラフ計算に使用される反復的なアルゴリズムに対して、Spark は適切な選択肢となります。 

この分散環境にアルゴリズム モデリング機能を提供するスケーラブルな機械学習ライブラリとして、MLlib と SparkML の 2 つがあります。 MLlib には、RDD 上に構築されたオリジナルの API が含まれています。 SparkML は、ML パイプラインを構成するために DataFrames 上に構築されたより高レベルの API を提供する新しいパッケージです。 SparkML はまだ MLlib のすべての機能をサポートしていませんが、Spark の標準の機械学習ライブラリとして MLlib を置き換えつつあります。

> [!NOTE]
> 
> SparkML モデルの作成について理解を深めるには、こちらの[チュートリアル](../spark/apache-spark-azure-machine-learning-tutorial.md)に従ってください。

## <a name="popular-libraries"></a>人気の高いライブラリ
Azure Synapse Analytics のどの Apache Spark プールにも、事前に読み込まれる人気の高い機械学習ライブラリのセットが付属しています。 これらのライブラリでは、プログラムまたはプロジェクトに含めることができる再利用可能なコードが提供されます。 既定で含まれている関連する機械学習ライブラリには、次のようなものがあります。
- [Scikit-learn](https://scikit-learn.org/stable/index.html) は、従来の ML アルゴリズム用のとても人気のある単一ノード機械学習ライブラリの 1 つです。 Scikit-learn は、教師ありおよび教師なしの学習アルゴリズムのほとんどをサポートしており、データ マイニングとデータ分析にも使用できます。
  
- [XGBoost](https://xgboost.readthedocs.io/en/latest/) は、デシジョン ツリーとランダム フォレストのトレーニング向けに最適化されたアルゴリズムを含む、人気の高い機械学習ライブラリです。 
  
- [PyTorch](https://pytorch.org/) と [Tensorflow](https://www.tensorflow.org/) は、強力な Python ディープ ラーニング ライブラリです。 Azure Synapse Analytics の Apache Spark プール内で、これらのライブラリを使用して、プール上の Executor の数をゼロに設定することで、単一マシンのモデルを構築できます。 この構成では Apache Spark は機能しませんが、単一マシンのモデルを作成するためのシンプルでコスト効率の高い方法です。

使用可能なライブラリと関連するバージョンの詳細については、公開済みの[Azure Synapse Analytics ランタイム](../spark/apache-spark-version-support.md)を参照してください。

## <a name="mmlspark"></a>MMLSpark
Apache Spark 用の Microsoft Machine Learning ライブラリは [MMLSpark](https://github.com/Azure/mmlspark) です。 このライブラリは、Spark 上でのデータ科学者の生産性を高め、実験の速度を向上させ、さらに大規模なデータセットに対してディープ ラーニングを含む最先端の機械学習手法を活用するように設計されています。 

文字列のインデックス作成、機械学習アルゴリズムによって予測されるレイアウトへのデータの強制的な移行、特徴ベクトルのアセンブルなどのスケーラブルな ML モデルを構築する場合、MMLSpark は SparkML の低レベルの API の上に 1 つのレイヤーを提供します。 MMLSpark ライブラリはこれらのタスクや、PySpark でモデルを構築するためのその他の一般的なタスクを簡略化します。

## <a name="automated-ml-in-azure-machine-learning"></a>Azure Machine Learning の自動 ML 
Azure Machine Learning は、機械学習モデルのトレーニング、デプロイ、自動化、管理、追跡を可能にするクラウドベースの環境です。 Azure Machine Learning の自動 ML は、トレーニング データと構成設定を受け取り、さまざまな特徴量の正規化および標準化の方法、モデル、およびハイパーパラメーター設定の組み合わせを自動的に反復処理し、最適なモデルに到達します。 

Azure Synapse Analytics 内で自動 ML を使用すると、さまざまなサービス間の緊密な統合を活用して、認証とモデル トレーニングを簡略化できます。 

> [!NOTE]
> 
> Azure Machine Learning 自動 ML 実験の作成について理解を深めるには、こちらの[チュートリアル](./spark/../apache-spark-azure-machine-learning-tutorial.md)に従ってください。

## <a name="azure-cognitive-services"></a>Azure Cognitive Services
[Azure Cognitive Services](../../cognitive-services/what-are-cognitive-services.md) は、一般的な問題を解決する機械学習機能を備えています。たとえば、テキストで感情 (センチメント) を分析したり、画像を分析して物や顔を認識したりすることができます。 これらのサービスを使用するために、機械学習やデータ サイエンスに関する特別な知識は必要ありません。 Cognitive Services は、機械学習ソリューションのコンポーネントであるデータ、アルゴリズム、トレーニング済みモデルの一部または全部を提供します。 これらのサービスは、データに関する一般的な知識が前提になりますが、機械学習やデータ サイエンスの経験は不要です。 これらの事前トレーニング済みの Cognitive Services は、Azure Synapse Analytics 内で自動的に利用できます。

## <a name="next-steps"></a>次のステップ
この記事では、Azure Synapse Analytics の Apache Spark プール内で機械学習モデルをトレーニングするためのさまざまなオプションの概要について説明しています。 モデルのトレーニングについて理解を深めるには、下のチュートリアルに従ってください。

- Azure Machine Learning と Azure Synapse Analytics を使用して、自動 ML の実験を実行します:[自動 ML のチュートリアル](../spark/apache-spark-azure-machine-learning-tutorial.md) 
- SparkML 実験を実行します:[Apache SparkML のチュートリアル](../spark/apache-spark-machine-learning-mllib-notebook.md)
- 既定のライブラリを確認します: [Azure Synapse Analytics ランタイム](../spark/apache-spark-version-support.md)