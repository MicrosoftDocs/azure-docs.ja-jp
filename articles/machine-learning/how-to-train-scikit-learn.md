---
title: scikit-learn 機械学習モデルをトレーニングする
titleSuffix: Azure Machine Learning
description: Azure Machine Learning でエラスティック クラウド コンピューティング リソースを使用して、scikit-learn のトレーニング ジョブをスケールアウトする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 807174fdbede2e4631b3ca1df7220904038da4c8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102518298"
---
# <a name="train-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Azure Machine Learning を使用して scikit-learn モデルを大規模にトレーニングする

この記事では、Azure Machine Learning を使用して scikit-learn トレーニング スクリプトを実行する方法について説明します。

この記事のサンプル スクリプトを使用し、アイリスの花の画像を分類して、scikit-learn の[アイリス データセット](https://archive.ics.uci.edu/ml/datasets/iris)に基づく機械学習モデルを構築します。

scikit-learn の機械学習モデルを一からトレーニングする場合でも、既存のモデルをクラウドに持ち込む場合でも、Azure Machine Learning のエラスティック クラウド コンピューティング リソースを使用して、オープンソースのトレーニング ジョブをスケールアウトできます。 Azure Machine Learning を使用して、運用レベルのモデルをビルド、デプロイ、バージョン管理、および監視することができます。

## <a name="prerequisites"></a>前提条件

このコードは、次の環境のいずれかで実行してください。
 - Azure Machine Learning コンピューティング インスタンス - ダウンロードやインストールは必要なし

    - 「[チュートリアル: 環境とワークスペースを設定する](tutorial-1st-experiment-sdk-setup.md)」を完了して、SDK とサンプル リポジトリが事前に読み込まれた専用のノートブック サーバーを作成します。
    - ノートブック サーバー上の samples トレーニング用フォルダーで、**how-to-use-azureml > ml-frameworks > scikit-learn > train-hyperparameter-tune-deploy-with-sklearn** フォルダーの順に選択してこのディレクトリに移動し、完了済みで展開済みのノートブックを見つけます。

 - 独自の Jupyter Notebook サーバー

    - [Azure Machine Learning SDK (1.13.0 以降) をインストールします](/python/api/overview/azure/ml/install)。
    - [ワークスペース構成ファイルを作成します](how-to-configure-environment.md#workspace)。

## <a name="set-up-the-experiment"></a>実験を設定する

このセクションでは、必要な Python パッケージを読み込み、ワークスペースを初期化し、トレーニング環境を定義し、トレーニング スクリプトを準備することで、トレーニング実験を設定します。

### <a name="initialize-a-workspace"></a>ワークスペースを初期化する

[Azure Machine Learning ワークスペース](concept-workspace.md)は、サービス用の最上位のリソースです。 作成されるすべての成果物を操作できる一元的な場所が用意されています。 Python SDK では、[`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) オブジェクトを作成することでワークスペースの成果物にアクセスできます。

[前提条件のセクション](#prerequisites)で作成した `config.json` ファイルからワークスペース オブジェクトを作成します。

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="prepare-scripts"></a>スクリプトを準備する

このチュートリアルでは既に、トレーニング スクリプトの **train_iris.py** が [ここ](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/train-hyperparameter-tune-deploy-with-sklearn/train_iris.py)に用意されています。 実際には、コードを変更しなくても、あらゆるカスタム トレーニング スクリプトをそのまま Azure ML で実行できるはずです。

メモ:
- 提供されているトレーニング スクリプトは、スクリプト内の `Run` オブジェクトを使用して Azure ML 実行にいくつかのメトリックをログ記録する方法を示しています。
- 指定されたトレーニング スクリプトでは、`iris = datasets.load_iris()` 関数のサンプル データを使用します。  独自のデータを使用してデータにアクセスするには、[データセットを使用したトレーニング方法](how-to-train-with-datasets.md)を参照して、トレーニング中にデータを使用できるようにしてください。

### <a name="define-your-environment"></a>環境を定義する

トレーニング スクリプトの依存関係をカプセル化する Azure ML [環境](concept-environments.md)を定義するには、カスタム環境を定義するか、Azure ML のキュレーションされた環境を使用します。

#### <a name="use-a-curated-environment"></a>選別された環境を使用する
独自のイメージを作成しない場合には、必要に応じて、事前に構築され、キュレーションされた環境が Azure ML によって提供されます。 詳細については、[ここ](resource-curated-environments.md)を参照してください。
キュレーションが行われた環境を使用する場合は、代わりに次のコマンドを実行できます。

```python
from azureml.core import Environment

sklearn_env = Environment.get(workspace=ws, name='AzureML-Tutorial')
```

#### <a name="create-a-custom-environment"></a>カスタム環境を作成する

独自のカスタム環境を作成することもできます。 YAML ファイルで conda の依存関係を定義します。この例では、ファイルの名前は `conda_dependencies.yml` です。

```yaml
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

sklearn_env = Environment.from_conda_specification(name='sklearn-env', file_path='conda_dependencies.yml')
```

環境の作成と使用の詳細については、「[Azure Machine Learning でソフトウェア環境を作成して使用する](how-to-use-environments.md)」をご覧ください。

## <a name="configure-and-submit-your-training-run"></a>トレーニングの実行を構成して送信する

### <a name="create-a-scriptrunconfig"></a>ScriptRunConfig を作成する
ScriptRunConfig オブジェクトを作成して、トレーニング スクリプト、使用する環境、実行するコンピューティング先など、トレーニング ジョブの構成の詳細を指定します。
`arguments` パラメーターで指定されている場合、トレーニング スクリプトへの引数はすべてコマンド ラインを使用して渡されます。

次のコードは、ローカル コンピューターで実行するジョブを送信するように ScriptRunConfig オブジェクトを構成します。

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='.',
                      script='train_iris.py',
                      arguments=['--kernel', 'linear', '--penalty', 1.0],
                      environment=sklearn_env)
```

代わりにリモート クラスターでジョブを実行する場合は、目的のコンピューティング先を ScriptRunConfig の `compute_target` パラメーターに指定できます。

```python
from azureml.core import ScriptRunConfig

compute_target = ws.compute_targets['<my-cluster-name>']
src = ScriptRunConfig(source_directory='.',
                      script='train_iris.py',
                      arguments=['--kernel', 'linear', '--penalty', 1.0],
                      compute_target=compute_target,
                      environment=sklearn_env)
```

### <a name="submit-your-run"></a>実行を送信する
```python
from azureml.core import Experiment

run = Experiment(ws,'Tutorial-TrainIRIS').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning では、ソース ディレクトリ全体をコピーすることで、トレーニング スクリプトが実行されます。 アップロードしたくない機密データがある場合は、[.ignore ファイル](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots)を使用するか、ソース ディレクトリに含めないようにします。 代わりに、Azure ML [データセット](how-to-train-with-datasets.md)を使用してデータにアクセスします。

### <a name="what-happens-during-run-execution"></a>実行実施中の動作
実行は、以下の段階を経て実施されます。

- **準備**:Docker イメージは、定義されている環境に従って作成されます。 イメージはワークスペースのコンテナー レジストリにアップロードされ、後で実行するためにキャッシュされます。 ログは実行履歴にもストリーミングされ、進行状況を監視するために表示することができます。 代わりに、キュレーションされた環境が指定されている場合は、そのキュレーションされた環境を補足するキャッシュ済みのイメージが使用されます。

- **拡大縮小**:Batch AI クラスターでの実行に現在使用可能な数より多くのノードが必要な場合、クラスターはスケールアップを試みます。

- **[実行中]** : スクリプト フォルダー内のすべてのスクリプトがコンピューティング先にアップロードされ、データ ストアがマウントまたはコピーされて、`script` が実行されます。 stdout からの出力と **./logs** フォルダーが実行履歴にストリーミングされるので、実行の監視のために使用できます。

- **後処理**:実行の **./outputs** フォルダーが実行履歴にコピーされます。

## <a name="save-and-register-the-model"></a>モデルを保存して登録する

モデルのトレーニングが終わったら、それをワークスペースに保存して登録できます。 モデルの登録を使用すると、モデルをワークスペースに格納し、バージョン管理して、[モデルの管理とデプロイ](concept-model-management-and-deployment.md)を簡単にすることができます。

モデルを保存するには、トレーニング スクリプト train_iris.py に次のコードを追加します。 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

次のコードでワークスペースにモデルを登録します。 パラメーター `model_framework`、`model_framework_version`、および `resource_configuration` を指定することによって、コードなしのモデル デプロイが使用可能になります。 コードなしのモデル デプロイを使用すると、登録済みのモデルからモデルを Web サービスとして直接デプロイできるようになり、[`ResourceConfiguration`](/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration) オブジェクトによって、Web サービスのコンピューティング リソースが定義されます。

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

先ほど登録したモデルは、Azure ML に登録されている他のモデルとまったく同じ方法でデプロイできます。 デプロイ方法にはモデルの登録に関するセクションが含まれていますが、登録済みのモデルが既にあるため、デプロイのために[コンピューティング先の作成](how-to-deploy-and-where.md#choose-a-compute-target)に直接スキップできます。

### <a name="preview-no-code-model-deployment"></a>(プレビュー) コードなしのモデル デプロイ

従来のデプロイ ルートの代わりに、scikit-learn でコードなしのデプロイ機能 (プレビュー) を使用することもできます。 コードなしのモデル デプロイは、すべての組み込みの scikit-learn モデルの種類に対してサポートされています。 `model_framework`、`model_framework_version`、および `resource_configuration` パラメーターを使用して前に示したようにモデルを登録することにより、単純に [`deploy()`](/python/api/azureml-core/azureml.core.model%28class%29#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) 静的関数を使用してモデルをデプロイできます。

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
