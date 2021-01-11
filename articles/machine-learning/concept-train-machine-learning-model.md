---
title: モデルを構築し、トレーニングする
titleSuffix: Azure Machine Learning
description: Azure Machine Learning を使用してモデルをトレーニングするために使用できるさまざまな方法について説明します。 Estimator では、Scikit-learn、TensorFlow、Keras、PyTorch、Chainer などの広く使われているフレームワークを簡単に操作する方法が提供されます。 Machine Learning パイプラインを使用すると、無人実行のスケジュール設定、異種コンピューティング環境の使用、ワークフローの一部再利用が簡単になります。 また、実行構成では、トレーニング プロセスを実行するコンピューティング先をきめ細かく制御できます。
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 05/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: 186839425e6ab2fb5430a82650615425bb93d51a
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88651762"
---
# <a name="train-models-with-azure-machine-learning"></a>Azure Machine Learning を使用してモデルをトレーニングする

Azure Machine Learning には、SDK を使用したコード ファースト ソリューションから、自動機械学習やビジュアル デザイナーなどのコーディングの少ないソリューションまで、モデルをトレーニングするためのいくつかの方法が用意されています。 次の一覧を使用して、ご自身にどのトレーニング方法が適しているかを判断してください。

+ [Azure Machine Learning SDK for Python](#python-sdk):Python SDK には、モデルをトレーニングするためのいくつかの方法が用意されており、それぞれに異なる機能があります。

    | トレーニング方法 | 説明 |
    | ----- | ----- |
    | [実行構成](#run-configuration) | **モデルをトレーニングするための汎用的な方法**は、トレーニング スクリプトと実行構成を使用することです。 実行構成では、モデルのトレーニングに使用されるトレーニング環境を構成するために必要な情報が提供されます。 実行構成、トレーニング スクリプト、コンピューティング先 (トレーニング環境) を取得し、トレーニング ジョブを実行できます。 |
    | [自動機械学習](#automated-machine-learning) | 自動機械学習を使用すると、**データ サイエンスやプログラミングに関する豊富な知識がなくてもモデルをトレーニング**することができます。 データ サイエンスとプログラミングの経験がある人に対しては、アルゴリズムの選択とハイパーパラメーターのチューニングを自動化することによって時間とリソースを節約する手段が提供されます。 自動機械学習を使用する場合、実行構成の定義について心配する必要はありません。 |
    | [Estimator](#estimators) | Estimator クラスを使用すると、**広く使われている機械学習フレームワークに基づいてモデルのトレーニングを簡単に行う**ことができます。 **Scikit-learn**、**PyTorch**、**TensorFlow**、**Chainer**、および **Ray RLlib** 用の Estimator クラスがあります。 また、専用の Estimator クラスがまだないフレームワークで使用できる汎用 Estimator もあります。 Estimator を使用する場合、実行構成の定義について心配する必要はありません。 |
    | [機械学習パイプライン](#machine-learning-pipeline) | パイプラインは別のトレーニング方法ではなく、**モジュール型の再利用可能な手順を使用してワークフローを定義する方法**であり、トレーニングをワークフローの一部として含めることができます。 機械学習パイプラインでは、自動機械学習、Estimator、および実行構成を使用したモデルのトレーニングがサポートされています。 パイプラインはトレーニングに明示的に重点を置いていないため、パイプラインを使用する理由は、他のトレーニング方法と大きく異なります。 一般に、以下の場合にパイプラインを使用できます。<br>* 実行時間の長いトレーニング ジョブやデータ準備など、**無人プロセスをスケジュール設定する**。<br>* 異種コンピューティング リソースとストレージの場所全体で調整された**複数の手順**を使用する。<br>* 再トレーニングやバッチ スコアリングなどの特定のシナリオ向けにパイプラインを**再利用可能なテンプレート**として使用する。<br>* ワークフローの**データソース、入力、出力を追跡してバージョン管理する**。<br>* ワークフローが、**特定の手順で個別に作業するさまざまなチームによって実装される**。 その後、手順をパイプラインに結合して、ワークフローを実装できます。 |

+ [Azure Machine Learning SDK for R](#r-sdk):SDK for R では、reticulate パッケージを使用して Azure Machine Learning の Python SDK にバインドします。 これにより、R 環境から Python SDK で実装されているコア オブジェクトとメソッドにアクセスできます。

+ **デザイナー**: Azure Machine Learning デザイナー (プレビュー) では、概念実証を構築するため、またはコーディングの経験がほとんどないないユーザーのための簡単なエントリポイントが機械学習に提供されます。 これにより、ドラッグ アンド ドロップ Web ベース UI を使用してモデルをトレーニングできます。 Python コードを設計の一部として使用したり、コードを記述せずにモデルをトレーニングしたりすることができます。

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
> Python SDK に加えて、[Azure Machine Learning Studio](https://ml.azure.com) を通じて自動 ML を使用することもできます。

* [自動機械学習とは](concept-automated-ml.md)
* [チュートリアル:自動機械学習を使用して最初の分類モデルを作成する](tutorial-first-experiment-automated-ml.md)
* [チュートリアル:自動機械学習を使用してタクシー料金を予測する](tutorial-auto-train-models.md)
* [例:自動機械学習の場合の Jupyter Notebook の例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [方法: Python で自動 ML の実験を構成する](how-to-configure-auto-train.md)
* [方法: 時系列予測モデルを自動トレーニングする](how-to-auto-train-forecast.md)
* [方法: Azure Machine Learning Studio で自動機械学習の実験を作成、調査、およびデプロイする](how-to-use-automated-ml-for-ml-models.md)

### <a name="estimators"></a>Estimator

Estimator を使用すると、広く使われている ML フレームワークを使用してモデルを簡単にトレーニングできます。 **Scikit-learn**、**PyTorch**、**TensorFlow**、**Chainer**、または **Ray RLlib** を使用している場合は、トレーニングに Estimator を使用することを検討してください。 また、専用の Estimator クラスがまだないフレームワークで使用できる汎用 Estimator もあります。 Estimator を使用する場合、実行構成の定義について心配する必要はありません。

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
* [例:Estimator を使用したパイプライン](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-estimatorstep.ipynb)

### <a name="understand-what-happens-when-you-submit-a-training-job"></a>トレーニング ジョブを送信したときの動作を理解する

Azure トレーニング ライフサイクルは次の要素で構成されています。

1. プロジェクト フォルダー内のファイルの解凍。 _.amlignore_ または _.gitignore_ で指定されたファイルは無視されます
1. コンピューティング クラスターのスケール アップ 
1. Dockerfile のビルドまたは計算ノードへのダウンロード 
    1. システムにより、次のハッシュが計算されます。 
        - 基本イメージ 
        - カスタム Docker の手順 (「[カスタム Docker ベース イメージを使用してモデルをデプロイする](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-custom-docker-image)」を参照してください)
        - Conda 定義 YAML ([Azure Machine Learning でのソフトウェア環境の作成と使用](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments)に関するページを参照してください)
    1. システムでは、ワークスペース Azure Container Registry (ACR) の検索で、このハッシュがキーとして使用されます
    1. 見つからない場合、グローバル ACR で一致するものが検索されます
    1. 見つからない場合、システムでは新しいイメージがビルドされます (これはキャッシュされ、ワークスペース ACR に登録されます)
1. 計算ノード上の一時記憶域への圧縮されたプロジェクト ファイルのダウンロード
1. プロジェクト ファイルの解凍
1. `python <entry script> <arguments>` を実行する計算ノード
1. ログ、モデル ファイル、`./outputs` に書き込まれたその他のファイルの、ワークスペースに関連付けられているストレージ アカウントへの保存
1. 一時記憶域の削除など、コンピューティングのスケールダウン 

ローカル コンピューターでのトレーニングを選択した場合 ("ローカル実行として構成")、Docker を使用する必要はありません。 必要に応じて、Docker をローカルで使用できます (例については、[ML パイプラインの構成](https://docs.microsoft.com/azure/machine-learning/how-to-debug-pipelines#configure-ml-pipeline )に関するセクションを参照)。

## <a name="r-sdk"></a>R SDK

R SDK を使用すると、Azure Machine Learning で R 言語を使用できます。 SDK では、reticulate パッケージを使用して Azure Machine Learning の Python SDK にバインドします。 これにより、R 環境から Python SDK で実装されているコア オブジェクトとメソッドにアクセスできます。

詳細については、次の記事を参照してください。

* [チュートリアル:ロジスティック回帰モデルを作成する](tutorial-1st-r-experiment.md)
* [Azure Machine Learning SDK for R リファレンス](https://azure.github.io/azureml-sdk-for-r/index.html)

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning デザイナー

デザイナーを使用すると、Web ブラウザーでドラッグ アンド ドロップ インターフェイスを使用してモデルをトレーニングできます。

+ [デザイナーとは](concept-designer.md)
+ [チュートリアル:自動車価格を予測する](tutorial-designer-automobile-price-train-score.md)
+ [回帰:価格を予測する](how-to-designer-sample-regression-automobile-price-basic.md)
+ [分類:所得を予測する](how-to-designer-sample-classification-predict-income.md)
+ [分類:顧客離れ、強い欲求、アップセルを予測する](how-to-designer-sample-classification-churn.md)
+ [カスタム R スクリプトを使用した分類: フライトの遅延を予測する](how-to-designer-sample-classification-flight-delay.md)
+ [テキスト分類: Wikipedia SP 500 データセット](how-to-designer-sample-text-classification.md)

## <a name="many-models-solution-accelerator"></a>多数モデル ソリューション アクセラレータ

[多数モデル ソリューション アクセラレータ](https://aka.ms/many-models) (プレビュー) は Azure Machine Learning 上に構築されており、数百または数千もの機械学習モデルをトレーニング、操作、管理できます。

たとえば次のシナリオで、__それぞれの事例または個々の対象の__ モデルを構築すると、結果が改善される可能性があります。

* 個々の店舗の売上予測
* 数百基の油井の予測メンテナンス
* 個々のユーザーのエクスペリエンスの調整。

詳細については、GitHub の[多数モデル ソリューション アクセラレータ](https://aka.ms/many-models)に関するページを参照してください。

## <a name="cli"></a>CLI

機械学習 CLI は、Azure CLI 用の拡張機能です。 Azure Machine Learning を操作するためのクロスプラットフォーム CLI コマンドが提供されます。 通常は、CLI を使用して、機械学習モデルのトレーニングなどのタスクを自動化します。

* [Azure Machine Learning 用の CLI 拡張機能を使用する](reference-azure-machine-learning-cli.md)
* [Azure 上の MLOps](https://github.com/microsoft/MLOps)

## <a name="vs-code"></a>VS Code

VS Code 拡張機能を使用して、トレーニング ジョブを実行および管理できます。 詳細については、[VS Code リソース管理の攻略ガイド](how-to-manage-resources-vscode.md#experiments)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

[トレーニング環境をセットアップする](how-to-set-up-training-targets.md)方法を確認します。
