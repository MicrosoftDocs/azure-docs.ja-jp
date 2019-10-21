---
title: モデルのトレーニング方法
titleSuffix: Azure Machine Learning
description: Azure Machine Learning を使用してモデルをトレーニングするために使用できるさまざまな方法について説明します。 Estimator では、Scikit-learn、TensorFlow、Keras、PyTorch、Chainer などの広く使われているフレームワークを簡単に操作する方法が提供されます。 Machine Learning パイプラインを使用すると、無人実行のスケジュール設定、異種コンピューティング環境の使用、ワークフローの一部再利用が簡単になります。 また、実行構成では、トレーニング プロセスを実行するコンピューティング先をきめ細かく制御できます。
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: a755fe1607e581cb0a25eb9bd90c2ba223829a46
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350601"
---
# <a name="train-models-with-azure-machine-learning"></a>Azure Machine Learning を使用してモデルをトレーニングする

Azure Machine Learning には、SDK を使用したコード ファースト ソリューションから、自動機械学習やビジュアル インターフェイスなどのコーディングの少ないソリューションまで、モデルをトレーニングするためのいくつかの方法が用意されています。 次の一覧を使用して、ご自身にどのトレーニング方法が適しているかを判断してください。

+ [Azure Machine Learning SDK for Python](#python-sdk):Python SDK には、モデルをトレーニングするためのいくつかの方法が用意されており、それぞれに異なる機能があります。

    | トレーニング方法 | 説明 |
    | ----- | ----- |
    | [実行構成](#run-configuration) | **モデルをトレーニングするための汎用的な方法**は、トレーニング スクリプトと実行構成を使用することです。 実行構成では、モデルのトレーニングに使用されるトレーニング環境を構成するために必要な情報が提供されます。 実行構成、トレーニング スクリプト、コンピューティング先 (トレーニング環境) を取得し、トレーニング ジョブを実行できます。 |
    | [自動機械学習](#automated-machine-learning) | 自動機械学習を使用すると、**データ サイエンスやプログラミングに関する豊富な知識がなくてもモデルをトレーニング**することができます。 データ サイエンスとプログラミングの経験がある人に対しては、アルゴリズムの選択とハイパーパラメーターのチューニングを自動化することによって時間とリソースを節約する手段が提供されます。 自動機械学習を使用する場合、実行構成の定義について心配する必要はありません。 |
    | [Estimator](#estimators) | Estimator クラスを使用すると、**広く使われている機械学習フレームワークに基づいてモデルのトレーニングを簡単に行う**ことができます。 **Scikit-learn**、**PyTorch**、**TensorFlow**、**Chainer** 用の Estimator クラスがあります。 また、専用の Estimator クラスがまだないフレームワークで使用できる汎用 Estimator もあります。 Estimator を使用する場合、実行構成の定義について心配する必要はありません。 |
    | [機械学習パイプライン](#machine-learning-pipeline) | パイプラインは別のトレーニング方法ではなく、**モジュール型の再利用可能な手順を使用してワークフローを定義する方法**であり、トレーニングをワークフローの一部として含めることができます。 機械学習パイプラインでは、自動機械学習、Estimator、および実行構成を使用したモデルのトレーニングがサポートされています。 パイプラインはトレーニングに明示的に重点を置いていないため、パイプラインを使用する理由は、他のトレーニング方法と大きく異なります。 一般に、以下の場合にパイプラインを使用できます。<br>* 実行時間の長いトレーニング ジョブやデータ準備など、**無人プロセスをスケジュール設定する**。<br>* 異種コンピューティング リソースとストレージの場所全体で調整された**複数の手順**を使用する。<br>* 再トレーニングやバッチ スコアリングなどの特定のシナリオ向けにパイプラインを**再利用可能なテンプレート**として使用する。<br>* ワークフローの**データソース、入力、出力を追跡してバージョン管理する**。<br>* ワークフローが、**特定の手順で個別に作業するさまざまなチームによって実装される**。 その後、手順をパイプラインに結合して、ワークフローを実装できます。 |

+ **ビジュアル インターフェイス**:Azure Machine Learning __ビジュアル インターフェイス__では、概念実証を構築するため、またはコーディングの経験がほとんどないないユーザーのための簡単なエントリポイントが機械学習に提供されます。 これにより、ドラッグ アンド ドロップ Web ベース UI を使用してモデルをトレーニングできます。 Python コードを設計の一部として使用したり、コードを記述せずにモデルをトレーニングしたりすることができます。

+ **CLI**:機械学習 CLI には Azure Machine Learning を使用した一般的なタスク用のコマンドが用意されており、多くの場合、**スクリプト作成とタスクの自動化**に使用されます。 たとえば、トレーニング スクリプトまたはパイプラインを作成した後、スケジュールに基づいて、またはトレーニングに使用するデータ ファイルが更新されたときに、CLI を使用してトレーニングの実行を開始することができます。 トレーニング モデルの場合は、トレーニング ジョブを送信するコマンドが用意されています。 実行構成またはパイプラインを使用してジョブを送信できます。

これらの各トレーニング方法では、トレーニング用にさまざまな種類のコンピューティング リソースを使用できます。 これらのリソースをまとめて、[__コンピューティング先__](concept-azure-machine-learning-architecture.md#compute-targets)と呼びます。 コンピューティング先は、ローカル マシンでも、Azure Machine Learning コンピューティング、Azure HDInsight、リモート仮想マシンなどのクラウド リソースでもかまいません。

## <a name="python-sdk"></a>Python SDK

Python 用 Azure Machine Learning SDK では、Azure Machine Learning を使用して、機械学習のワークフローをビルドして実行できます。 対話型の Python セッション、Jupyter Notebook、Visual Studio Code、またはその他の IDE からサービスと対話できます。

* [Azure Machine Learning SDK for Python とは](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [SDK のインストール/更新](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Azure Machine Learning のための開発環境を構成する](how-to-configure-environment.md)

### <a name="run-configuration"></a>実行構成

Azure Machine Learning の汎用トレーニング ジョブは、[RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) を使用して定義できます。 次に、実行構成がトレーニング スクリプトと共に使用されて、コンピューティング先でモデルがトレーニングされます。

ローカル コンピューター用の実行構成から開始し、必要に応じてクラウドベースのコンピューティング先に切り替えることができます。 コンピューティング先を変更する場合は、使用する実行構成のみ変更します。 実行によって、入力、出力、ログなどのトレーニング ジョブに関する情報もログに記録されます。

* [実行構成とは](concept-azure-machine-learning-architecture.md#run-configurations)
* [チュートリアル:](tutorial-1st-experiment-sdk-train.md)最初の ML モデルをトレーニングする
* [例:トレーニング モデルの Jupyter Notebook の例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training)
* [方法: モデル トレーニング用のコンピューティング先を設定して使用する](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>自動化された機械学習

イテレーション、ハイパーパラメーターの設定、特徴付け、その他の設定を定義します。 トレーニング中、Azure Machine Learning では、さまざまなアルゴリズムとパラメーターが並列に試行されます。 トレーニングは、定義した終了基準が満たされると終了します。 Estimator を使用する場合、実行構成の定義について心配する必要はありません。

> [!TIP]
> Python SDK に加えて、[ワークスペース ランディング ページ (プレビュー)](https://ml.azure.com) を通じて自動 ML を使用することもできます。

* [自動機械学習とは](concept-automated-ml.md)
* [チュートリアル:自動機械学習を使用して最初の分類モデルを作成する](tutorial-first-experiment-automated-ml.md)
* [チュートリアル:自動機械学習を使用してタクシー料金を予測する](tutorial-auto-train-models.md)
* [例:自動機械学習の場合の Jupyter Notebook の例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [方法: Python で自動 ML の実験を構成する](how-to-configure-auto-train.md)
* [方法: 時系列予測モデルを自動トレーニングする](how-to-auto-train-forecast.md)
* [方法: Azure Machine Learning のワークスペース ランディング ページ (プレビュー) を使用して自動化された機械学習の実験を作成、参照、およびデプロイする](how-to-create-portal-experiments.md)

### <a name="estimators"></a>Estimator

Estimator を使用すると、広く使われている ML フレームワークを使用してモデルを簡単にトレーニングできます。 **Scikit-learn**、**PyTorch**、**TensorFlow**、または **Chainer** を使用している場合は、トレーニングに Estimator を使用することを検討してください。 また、専用の Estimator クラスがまだないフレームワークで使用できる汎用 Estimator もあります。 Estimator を使用する場合、実行構成の定義について心配する必要はありません。

* [Estimator とは](concept-azure-machine-learning-architecture.md#estimators)
* [チュートリアル:Azure Machine Learning で MNIST データと scikit-learn を使用して画像の分類モデルをトレーニングする](tutorial-train-models-with-aml.md)
* [例:Estimator の使用の Jupyter Notebook の例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)
* [方法: トレーニングでの Estimator の作成](how-to-train-ml-models.md)

### <a name="machine-learning-pipeline"></a>機械学習パイプライン

機械学習パイプラインでは、前述のトレーニング方法 (実行構成、Estimator、自動機械学習) を使用できます。 パイプラインは、ワークフローの作成だけに関するものではないため、モデルのトレーニング以外のものも含まれます。 パイプラインでは、自動機械学習、Estimator、または実行構成を使用してモデルをトレーニングできます。

* [Azure Machine Learning の ML パイプラインとは](concept-ml-pipelines.md)
* [Azure Machine Learning SDK で機械学習パイプラインを作成して管理する](how-to-create-your-first-pipeline.md)
* [チュートリアル:バッチ スコアリングに Azure Machine Learning パイプラインを使用する](tutorial-pipeline-batch-scoring-classification.md)
* [例:機械学習パイプラインの場合の Jupyter Notebook の例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [例:自動機械学習を使用したパイプライン](https://aka.ms/pl-automl)
* [例:Estimator を使用したパイプライン](https://aka.ms/pl-estimator)

## <a name="visual-interface"></a>ビジュアル インターフェイス

ビジュアル インターフェイス (プレビュー) を使用すると、Web ブラウザーでドラッグ アンド ドロップ インターフェイスを使用してモデルをトレーニングできます。

+ [ビジュアル インターフェイスとは](ui-concept-visual-interface.md)
+ [チュートリアル:自動車価格を予測する](ui-tutorial-automobile-price-train-score.md)
+ [回帰:価格を予測する](how-to-ui-sample-regression-predict-automobile-price-basic.md)
+ [分類:信用リスクを予測する](how-to-ui-sample-classification-predict-credit-risk-basic.md)
+ [分類:顧客離れ、強い欲求、アップセルを予測する](how-to-ui-sample-classification-predict-churn.md)

## <a name="cli"></a>CLI

機械学習 CLI は、Azure CLI 用の拡張機能です。 Azure Machine Learning を操作するためのクロスプラットフォーム CLI コマンドが提供されます。 通常は、CLI を使用して、機械学習モデルのトレーニングなどのタスクを自動化します。

* [Azure Machine Learning 用の CLI 拡張機能を使用する](reference-azure-machine-learning-cli.md)
* [Azure 上の MLOps](https://github.com/microsoft/MLOps)

## <a name="next-steps"></a>次の手順

[トレーニング環境をセットアップする](how-to-set-up-training-targets.md)方法を確認します。
