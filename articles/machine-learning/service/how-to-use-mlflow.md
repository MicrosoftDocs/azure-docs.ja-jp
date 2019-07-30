---
title: Azure Machine Learning service での MLflow の使用
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service で MLflow ライブラリを使用して、メトリックと成果物をログに記録する方法について説明します。
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 06/10/2019
ms.custom: seodec18
ms.openlocfilehash: d0bc4620d0c55d6e94a3d99c39ab405dab2743e5
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461646"
---
# <a name="use-mlflow-with-azure-machine-learning-service-preview"></a>Azure Machine Learning service での MLflow の使用 (プレビュー)

この記事では、Azure Machine Learning service で MLflow の追跡 URI とログ API (MLflow Tracking と総称) を使用して、[Azure Machine Learning service ワークスペース](https://docs.microsoft.com/azure/machine-learning/service/concept-azure-machine-learning-architecture#workspace)で実験のメトリックと成果物を追跡し、ログに記録する方法について説明します。 実験に MLflow Tracking を既に使用している場合、トレーニングのメトリックとモデルを保存するための一元化された安全でスケーラブルな場所がワークスペースに用意されています。

[MLflow](https://www.mlflow.org) は、機械学習の実験のライフ サイクルを管理するためのオープンソース ライブラリです。 [MLFlow Tracking](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api) は MLflow のコンポーネントです。実験がローカル、仮想マシン、リモート コンピューティング クラスターのいずれで実行されているかに関わらず、トレーニング実行のメトリックとモデル成果物をログに記録し、追跡します。
![Azure Machine Learning での MLflow の図](media/how-to-use-mlflow/mlflow-diagram.png)

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>MLflow と Azure Machine Learning のクライアントの比較

 次の表に、Azure Machine Learning service を使用できるさまざまなクライアントとそれぞれの機能を示します。

 MLflow Tracking は、メトリックのログ機能と成果物の保存機能を提供します。他の方法では、[Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) を使用している場合にのみこれらの機能を利用できます。

| | MLflow Tracking | Azure Machine Learning <br> Python SDK |  Azure Machine Learning <br> CLI | Azure ポータル|
|-|-|-|-|-|
| ワークスペースの管理 |   | ✓ |  ✓ | ✓  |
| データ ストアの使用  |   | ✓ |  ✓ |    |
| メトリックのログ記録      | ✓ | ✓ |    |    |
| 成果物のアップロード | ✓ | ✓ |    |    |
| メトリックを表示する     | ✓ | ✓ | ✓  | ✓ |
| コンピューティングの管理   |   | ✓ | ✓  | ✓ |
| モデルをデプロイする    |   | ✓ |   ✓ | ✓ |

## <a name="prerequisites"></a>前提条件

* [MLflow をインストール](https://mlflow.org/docs/latest/quickstart.html)します。
* [ローカル コンピューターに Azure Machine Learning Python SDK をインストールし、Azure Machine Learning ワークスペースを作成](setup-create-workspace.md#sdk)します。 この SDK により、MLflow がワークスペースにアクセスするための接続が提供されます。

## <a name="track-local-runs"></a>ローカル実行の追跡

Jupyter Notebook またはコード エディターでローカルで実行される Azure Machine Learning の実験で MLflow Tracking を使用するには、`azureml-contrib-run` パッケージをインストールします。

```shell
pip install azureml-contrib-run
```

>[!NOTE]
>azureml.contrib 名前空間は、サービスの改善に伴って頻繁に変更されます。 そのため、この名前空間内のものはすべて Microsoft によって完全にサポートされているものではなく、プレビューとして見なされる必要があります。

MLflow の追跡 URI にアクセスし、ワークスペースを構成するには、`mlflow` および [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) クラスをインポートします。

次のコードでは、`get_mlflow_tracking_uri()` メソッドによって、ワークスペース `ws` に一意の追跡 URI アドレスを割り当て、`set_tracking_uri()` によって、MLflow の追跡 URI がそのアドレスを参照するよう設定します。

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>追跡 URI の有効期間は最大 1 時間以内です。 一定のアイドル時間の経過後にスクリプトを再起動した場合は、get_mlflow_tracking_uri API を使用して新しい URI を取得してください。

`set_experiment()` で MLflow の実験名を設定し、`start_run()` でトレーニング実行を開始します。 次に、`log_metric()` を使用して MLflow ログ API をアクティブにし、トレーニング実行のメトリックのログ記録を開始します。

```Python
experiment_name = "experiment_with_mlflow"
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>リモート実行の追跡

リモート実行では、GPU 対応仮想マシンや Machine Learning コンピューティング クラスターなど、より強力なコンピューティングでモデルをトレーニングできます。 さまざまなコンピューティング オプションについては、「[モデル トレーニング用のコンピューティング ターゲットを設定する](how-to-set-up-training-targets.md)」をご覧ください。

[`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) クラスを使用して、コンピューティングとトレーニングの実行環境を構成します。 `mlflow` および `azure-contrib-run` pip パッケージを、環境の [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) セクションに含めます。 次に、コンピューティング ターゲットとして、リモート コンピューティングで [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) を構築します。

```Python

from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = "my_workspace",
                 name = "my_experiment")

mlflow_env = Environment(name="mlflow-env")

cd = CondaDependencies.create(pip_packages = ["mlflow", "azureml-contrib-run"])

mlflow_env.python.conda_dependencies=cd

src = ScriptRunConfig(source_directory="./my_script_location", script="my_training_script.py")

src.run_config.target = "my-remote-compute-compute"
src.run_config.environment = mlflow_env
```

トレーニング スクリプトで、`mlflow` をインポートして MLflow ログ API を使用し、実行のメトリックのログ記録を開始します。

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric("example", 1.23)
```

このコンピューティングおよびトレーニングの実行構成では、`Experiment.submit("train.py")` メソッドを使用して実行を送信します。 これにより、MLflow の追跡 URI が自動的に設定され、MLflow からワークスペースにログが送信されます。

```Python
run = exp.submit(src)
```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>ワークスペースでのメトリックと成果物の表示

MLflow のログのメトリックと成果物は、[Azure portal](https://portal.azure.com) のワークスペースに保持されます。 これらを表示するには、ワークスペースに移動し、実験を名前で検索します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

ログに記録されたメトリックと成果物をワークスペースで使用する予定がない場合、現時点では、それらを個別に削除する機能は提供されていません。 代わりに、ストレージ アカウントとワークスペースを含むリソース グループを削除すれば、課金は発生しません。

1. Azure Portal で、左端にある **[リソース グループ]** を選択します。

   ![Azure portal で削除する](media/how-to-use-mlflow/delete-resources.png)

1. 作成したリソース グループを一覧から選択します。

1. **[リソース グループの削除]** を選択します。

1. リソース グループ名を入力します。 次に、 **[削除]** を選択します。

## <a name="example-notebooks"></a>サンプルの Notebook

[Azure ML での MLflow ノートブック](https://aka.ms/azureml-mlflow-examples)では、この記事の概念を示しています。

## <a name="next-steps"></a>次の手順

* [モデルをデプロイする方法](how-to-deploy-and-where.md)
