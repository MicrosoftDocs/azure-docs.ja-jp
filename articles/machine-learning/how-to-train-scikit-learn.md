---
title: scikit-learn 機械学習モデルをトレーニングする
titleSuffix: Azure Machine Learning
description: Azure Machine Learning で scikit-learn トレーニング スクリプトを実行する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.date: 07/24/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 4221ed6a927d0c589407dc38b5371ad8a65d2174
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88054393"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Azure Machine Learning を使用して Scikit-learn モデルを大規模に構築する
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、Azure Machine Learning を使用して scikit-learn トレーニング スクリプトを実行する方法について説明します。

この記事のサンプル スクリプトを使用し、アイリスの花の画像を分類して、scikit-learn の[アイリス データセット](https://archive.ics.uci.edu/ml/datasets/iris)に基づく機械学習モデルを構築します。

scikit-learn の機械学習モデルを一からトレーニングする場合でも、既存のモデルをクラウドに持ち込む場合でも、Azure Machine Learning のエラスティック クラウド コンピューティング リソースを使用して、オープンソースのトレーニング ジョブをスケールアウトできます。 Azure Machine Learning を使用して、運用レベルのモデルをビルド、デプロイ、バージョン管理、および監視することができます。

## <a name="prerequisites"></a>前提条件

このコードは、次の環境のいずれかで実行してください。
 - Azure Machine Learning コンピューティング インスタンス - ダウンロードやインストールは必要なし

    - 「[チュートリアル: 環境とワークスペースを設定する](tutorial-1st-experiment-sdk-setup.md)」を完了して、SDK とサンプル リポジトリが事前に読み込まれた専用のノートブック サーバーを作成します。
    - Notebook サーバー上の samples トレーニング用フォルダーで、**how-to-use-azureml > ml-frameworks > scikit-learn > training > train-hyperparameter-tune-deploy-with-sklearn** の順に選択してこのディレクトリに移動し、完了済みで展開済みのノートブックを見つけます。

 - 独自の Jupyter Notebook サーバー

    - [Azure Machine Learning SDK をインストールします](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。
    - [ワークスペース構成ファイルを作成します](how-to-configure-environment.md#workspace)。

## <a name="set-up-the-experiment"></a>実験を設定する

このセクションでは、必要な Python パッケージを読み込み、ワークスペースを初期化し、実験を作成し、トレーニング データとトレーニング スクリプトをアップロードすることで、トレーニング実験を設定します。

### <a name="initialize-a-workspace"></a>ワークスペースを初期化する

[Azure Machine Learning ワークスペース](concept-workspace.md)は、サービス用の最上位のリソースです。 作成されるすべての成果物を操作できる一元的な場所が用意されています。 Python SDK では、[`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) オブジェクトを作成することでワークスペースの成果物にアクセスできます。

[前提条件のセクション](#prerequisites)で作成した `config.json` ファイルからワークスペース オブジェクトを作成します。

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```


### <a name="prepare-scripts"></a>スクリプトを準備する

このチュートリアルでは既に、トレーニング スクリプトの **train_iris.py** が[ここ](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train_iris.py)に用意されています。 実際には、コードを変更しなくても、あらゆるカスタム トレーニング スクリプトをそのまま Azure ML で実行できるはずです。

メモ:
- 提供されているトレーニング スクリプトは、スクリプト内の `Run` オブジェクトを使用して Azure ML 実行にいくつかのメトリックをログ記録する方法を示しています。
- 指定されたトレーニング スクリプトでは、`iris = datasets.load_iris()` 関数のサンプル データを使用します。  独自のデータについては、[データセットやスクリプトのアップロード](how-to-train-keras.md#data-upload)などの手順を使用して、トレーニング中にデータを使用できるようにすることが必要になる場合があります。

### <a name="define-your-environment"></a>環境を定義する

#### <a name="create-a-custom-environment"></a>カスタム環境を作成する

Conda 環境 (sklearn-env.yml) を作成します。
ノートブックから Conda 環境を作成する場合は、セルの最上部に ```%%writefile sklearn-env.yml``` という行を追加できます。

```yaml
name: sklearn-training-env
dependencies:
  - python=3.6.2
  - scikit-learn
  - numpy
  - pip:
    - azureml-defaults
```

この Conda 環境仕様から Azure ML 環境を作成します。 環境は、実行時に Docker コンテナーにパッケージ化されます。
```python
from azureml.core import Environment

myenv = Environment.from_conda_specification(name = "myenv", file_path = "sklearn-env.yml")
myenv.docker.enabled = True
```

#### <a name="use-a-curated-environment"></a>選別された環境を使用する
独自のイメージを作成しない場合には、Azure ML に、事前に構築とキュレーションが行われたコンテナー環境が用意されています。 詳細については、[ここ](resource-curated-environments.md)を参照してください。
キュレーションが行われた環境を使用する場合は、代わりに次のコマンドを実行できます。

```python
env = Environment.get(workspace=ws, name="AzureML-Tutorial")
```

### <a name="create-a-scriptrunconfig"></a>ScriptRunConfig を作成する

この ScriptRunConfig は、ローカルのコンピューティング ターゲットで実行するジョブを送信します。

```python
from azureml.core import ScriptRunConfig

sklearnconfig = ScriptRunConfig(source_directory='.', script='train_iris.py')
sklearnconfig.run_config.environment = myenv
```

リモート クラスターに送信する場合は、run_config.target を目的のコンピューティング ターゲットに変更できます。

### <a name="submit-your-run"></a>実行を送信する
```python
from azureml.core import Experiment

run = Experiment(ws,'train-sklearn').submit(config=sklearnconfig)
run.wait_for_completion(show_output=True)

```

> [!WARNING]
> Azure Machine Learning では、ソース ディレクトリ全体をコピーすることで、トレーニング スクリプトが実行されます。 アップロードしたくない機密データがある場合は、[.ignore ファイル](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots)を使用するか、ソース ディレクトリに含めないようにします。 代わりに、[データストア](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py)を使用してデータにアクセスしてください。

Python 環境のカスタマイズの詳細については、[トレーニングとデプロイのための環境の作成と管理](how-to-use-environments.md)に関するページを参照してください。 

## <a name="what-happens-during-run-execution"></a>実行実施中の動作
実行は、以下の段階を経て実施されます。

- **準備**:docker イメージは TensorFlow エスティメーターに従って作成されます。 イメージはワークスペースのコンテナー レジストリにアップロードされ、後で実行するためにキャッシュされます。 ログは実行履歴にもストリーミングされ、進行状況を監視するために表示することができます。

- **拡大縮小**:Batch AI クラスターでの実行に現在使用可能な数より多くのノードが必要な場合、クラスターはスケールアップを試みます。

- **[実行中]** : script フォルダー内のすべてのスクリプトがコンピューティング先にアップロードされ、データ ストアがマウントまたはコピーされ、entry_script が実行されます。 stdout および ./logs フォルダーの出力は実行履歴にストリーミングされ、実行を監視するために使用できます。

- **後処理**:実行の ./outputs フォルダーが実行履歴にコピーされます。

## <a name="save-and-register-the-model"></a>モデルを保存して登録する

モデルのトレーニングが終わったら、それをワークスペースに保存して登録できます。 モデルの登録を使用すると、モデルをワークスペースに格納し、バージョン管理して、[モデルの管理とデプロイ](concept-model-management-and-deployment.md)を簡単にすることができます。

モデルを保存するには、トレーニング スクリプト train_iris.py に次のコードを追加します。 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

次のコードでワークスペースにモデルを登録します。 パラメーター `model_framework`、`model_framework_version`、および `resource_configuration` を指定することによって、コードなしのモデル デプロイが使用可能になります。 コードなしのモデル デプロイを使用すると、登録済みのモデルからモデルを Web サービスとして直接デプロイできるようになり、[`ResourceConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration?view=azure-ml-py) オブジェクトによって、Web サービスのコンピューティング リソースが定義されます。

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='sklearn-iris', 
                           model_path='outputs/model.joblib',
                           model_framework=Model.Framework.SCIKITLEARN,
                           model_framework_version='0.19.1',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

## <a name="deployment"></a>デプロイ

今登録したモデルは、どのエスティメーターをトレーニングに使用したかには関係なく、Azure Machine Learning の他のすべての登録済みのモデルとまったく同じ方法でデプロイできます。 デプロイ方法にはモデルの登録に関するセクションが含まれていますが、登録済みのモデルが既にあるため、デプロイのために[コンピューティング先の作成](how-to-deploy-and-where.md#choose-a-compute-target)に直接スキップできます。

### <a name="preview-no-code-model-deployment"></a>(プレビュー) コードなしのモデル デプロイ

従来のデプロイ ルートの代わりに、scikit-learn でコードなしのデプロイ機能 (プレビュー) を使用することもできます。 コードなしのモデル デプロイは、すべての組み込みの scikit-learn モデルの種類に対してサポートされています。 `model_framework`、`model_framework_version`、および `resource_configuration` パラメーターを使用して前に示したようにモデルを登録することにより、単純に [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) 静的関数を使用してモデルをデプロイできます。

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

注:事前に構築された scikit-learn 推論コンテナーには、次の依存関係が含まれています。

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

完全な[方法](how-to-deploy-and-where.md)では、Azure Machine Learning を使用したデプロイがさらに詳細に説明されています。


## <a name="next-steps"></a>次のステップ

この記事では、scikit-learn モデルをトレーニングして登録した後、デプロイ オプションについて学習しました。 Azure Machine Learning の詳細については、以下の他の記事をご覧ください。

* [トレーニング中に実行メトリクスを追跡する](how-to-track-experiments.md)
* [ハイパーパラメーターを調整する](how-to-tune-hyperparameters.md)
* [Azure での分散型ディープ ラーニング トレーニングの参照アーキテクチャ](/azure/architecture/reference-architectures/ai/training-deep-learning)
