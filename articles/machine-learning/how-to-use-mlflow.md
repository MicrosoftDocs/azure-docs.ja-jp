---
title: ML 実験の MLflow Tracking
titleSuffix: Azure Machine Learning
description: Azure Machine Learning を使用して MLflow を設定し、ML モデルからのメトリックと成果物をログに記録し、Web サービスとして ML モデルをデプロイします。
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 06/04/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: a0241864a5eafe8783aea463197f86ff949ea9ed
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87853383"
---
# <a name="track-model-metrics-and-deploy-ml-models-with-mlflow-and-azure-machine-learning-preview"></a>MLflow と Azure Machine Learning を使用してモデル メトリックを追跡し、ML モデルをデプロイする (プレビュー)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、MLflow の追跡 URI とログ API (まとめて [MLflow Tracking](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api) と呼ばれる) を有効にして、MLflow の実験と Azure Machine Learning を接続する方法について説明します。  これを実行することで、以下のことが可能になります。

+ [Azure Machine Learning ワークスペース](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces)で、実験のメトリックと成果物を追跡してログに記録します。 実験に MLflow Tracking を既に使用している場合、トレーニングのメトリックとモデルを保存するための一元化された安全でスケーラブルな場所がワークスペースに用意されています。

+ MLflow の実験を Azure Machine Learning Web サービスとしてデプロイします。 Web サービスとしてデプロイすることで、Azure Machine Learning の監視機能とデータ誤差検出機能を実稼働モデルに適用できます。 

[MLflow](https://www.mlflow.org) は、機械学習の実験のライフ サイクルを管理するためのオープンソース ライブラリです。 MLFlow Tracking は MLflow のコンポーネントです。これは、実験の環境がローカル コンピューター、リモートのコンピューティング先、仮想マシン、Azure Databricks クラスターのいずれであるかにかかわらず、トレーニング実行のメトリックとモデル成果物をログに記録し、追跡します。 

>[!NOTE]
> オープン ソース ライブラリである MLflow は頻繁に変更されます。 そのため、Azure Machine Learning と MLflow の統合によって利用できるようになる機能はプレビューとして見なす必要があり、Microsoft は完全にサポートしていません。

次の図は、MLflow Tracking を使用して、実験の実行メトリックを追跡し、Azure Machine Learning ワークスペース内にモデル成果物を保存する例を示しています。

![Azure Machine Learning での MLflow](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> このドキュメントの情報は主に、モデルのトレーニング プロセスを監視したいデータ サイエンティストや開発者を対象としています。 Azure Machine Learning からリソース使用状況やイベント (クォータ、トレーニング実行の完了、モデル デプロイの完了など) を監視することに関心がある管理者の方は、「[Azure Machine Learning の監視](monitor-azure-machine-learning.md)」を参照してください。

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>MLflow と Azure Machine Learning のクライアントの比較

 次の表に、Azure Machine Learning を使用できるさまざまなクライアントとそれぞれの機能を示します。

 MLflow Tracking は、メトリックのログ機能と成果物の保存機能を提供します。他の方法では、[Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) を使用している場合にのみこれらの機能を利用できます。


| 機能 | MLflow&nbsp;Tracking & Deployment | Azure Machine Learning Python SDK |  Azure Machine Learning CLI | Azure Machine Learning Studio|
|---|---|---|---|---|
| ワークスペースの管理 |   | ✓ | ✓ | ✓ |
| データ ストアの使用  |   | ✓ | ✓ | |
| メトリックのログ記録      | ✓ | ✓ |   | |
| 成果物のアップロード | ✓ | ✓ |   | |
| メトリックを表示する     | ✓ | ✓ | ✓ | ✓ |
| コンピューティングの管理   |   | ✓ | ✓ | ✓ |
| モデルをデプロイする    | ✓ | ✓ | ✓ | ✓ |
|モデル パフォーマンスを監視する||✓|  |   |
| データの誤差を検出する |   | ✓ |   | ✓ |

## <a name="prerequisites"></a>前提条件

* [MLflow をインストール](https://mlflow.org/docs/latest/quickstart.html)します。
* ローカル コンピューターに [Azure Machine Learning SDK をインストール](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)します。この SDK により、MLflow がワークスペースにアクセスするための接続が提供されます。
* [Azure Machine Learning ワークスペースを作成](how-to-manage-workspace.md)します。

## <a name="track-local-runs"></a>ローカル実行の追跡

Azure Machine Learning で MLflow Tracking を使用すると、ローカル実行からログに記録されたメトリックと成果物を Azure Machine Learning ワークスペースに格納できます。

Jupyter Notebook またはコード エディターでローカルで実行される Azure Machine Learning の実験で MLflow Tracking を使用するには、`azureml-mlflow` パッケージをインストールします。

```shell
pip install azureml-mlflow
```

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
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>リモート実行の追跡

Azure Machine Learning で MLflow Tracking を使用すると、リモート実行からログに記録されたメトリックと成果物を Azure Machine Learning ワークスペースに格納できます。

リモート実行では、GPU 対応仮想マシンや Machine Learning コンピューティング クラスターなど、より強力なコンピューティングでモデルをトレーニングできます。 さまざまなコンピューティング オプションについては、「[モデル トレーニング用のコンピューティング ターゲットを設定する](how-to-set-up-training-targets.md)」をご覧ください。

[`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) クラスを使用して、コンピューティングとトレーニングの実行環境を構成します。 `mlflow` および `azureml-mlflow` pip パッケージを、環境の [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) セクションに含めます。 次に、コンピューティング ターゲットとして、リモート コンピューティングで [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) を構築します。

```Python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = 'my_workspace',
                 name='my_experiment')

mlflow_env = Environment(name='mlflow-env')

cd = CondaDependencies.create(pip_packages=['mlflow', 'azureml-mlflow'])

mlflow_env.python.conda_dependencies = cd

src = ScriptRunConfig(source_directory='./my_script_location', script='my_training_script.py')

src.run_config.target = 'my-remote-compute-compute'
src.run_config.environment = mlflow_env
```

トレーニング スクリプトで、`mlflow` をインポートして MLflow ログ API を使用し、実行のメトリックのログ記録を開始します。

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

このコンピューティングおよびトレーニングの実行構成では、`Experiment.submit('train.py')` メソッドを使用して実行を送信します。 この方法により、MLflow の追跡 URI が自動的に設定され、MLflow からワークスペースにログが送信されます。

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>Azure Databricks の実行を追跡する

Azure Machine Learning で MLflow Tracking を使用すると、Azure Databricks の実行からログに記録されたメトリックと成果物を Azure Machine Learning ワークスペースに格納できます。

MLflow の実験を Azure Databricks で実行するには、[Azure Databricks ワークスペースとクラスター](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)を最初に作成する必要があります。 クラスターでは、PyPi から *azureml-mlflow* ライブラリを必ずインストールして、必要な関数とクラスにクラスターがアクセスできるようにします。

ここから、実験ノートブックをインポートして、Azure Databricks クラスターにアタッチし、実験を実行します。 

### <a name="install-libraries"></a>ライブラリのインストール

クラスターにライブラリをインストールするには、 **[ライブラリ]** タブに移動して、 **[新規インストール]** をクリックします。

 ![Azure Machine Learning での MLflow](./media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

**[パッケージ]** フィールドに azureml-mlflow と入力し、次に [インストール] をクリックします。 必要に応じてこの手順を繰り返して、実験のための他の追加パッケージをクラスターにインストールします。

 ![Azure Machine Learning での MLflow](./media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>ノートブックとワークスペースのセットアップ

クラスターがセットアップされたら、実験ノートブックをインポートして開き、それにクラスターをアタッチします。

実験ノートブックには次のコードが含まれているはずです。 このコードにより、ワークスペースをインスタンス化するための Azure サブスクリプションの詳細が取得されます。 このコードは、既存のリソースグループと Azure Machine Learning ワークスペースがあることを前提としています。そうでない場合は、[それらを作成](how-to-manage-workspace.md)できます。 

```python
import mlflow
import mlflow.azureml
import azureml.mlflow
import azureml.core

from azureml.core import Workspace
from azureml.mlflow import get_portal_url

subscription_id = 'subscription_id'

# Azure Machine Learning resource group NOT the managed resource group
resource_group = 'resource_group_name' 

#Azure Machine Learning workspace name, NOT Azure Databricks workspace
workspace_name = 'workspace_name'  

# Instantiate Azure Machine Learning workspace
ws = Workspace.get(name=workspace_name,
                   subscription_id=subscription_id,
                   resource_group=resource_group)
```

#### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Azure Databricks と Azure Machine Learning ワークスペースに接続する

[Azure portal](https://ms.portal.azure.com) で、Azure Databricks (ADB) ワークスペースを新規または既存の Azure Machine Learning ワークスペースにリンクできます。 これを行うには、ADB ワークスペースに移動し、右下の **[Link Azure Machine Learning workspace]\(Azure Machine Learning ワークスペースをリンク\)** を選択します。 ワークスペースをリンクすると、Azure Machine Learning ワークスペースで実験データを追跡できるようになります。 

### <a name="link-mlflow-tracking-to-your-workspace"></a>ワークスペースへの MLflow 追跡のリンク

ワークスペースをインスタンス化したら、MLflow の追跡 URI を設定します。 これにより、MLflow の追跡を Azure Machine Learning ワークスペースにリンクします。 リンク後、すべての実験は管理対象の Azure Machine Learning 追跡サービスに入れられます。

#### <a name="directly-set-mlflow-tracking-in-your-notebook"></a>ノートブックで MLflow 追跡を直接設定する

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

トレーニング スクリプトで、MLflow をインポートして MLflow のログ API を使用し、実行メトリックのログ記録を開始します。 次の例では、エポック損失メトリックをログに記録します。 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

#### <a name="automate-setting-mlflow-tracking"></a>MLflow 追跡の設定の自動化

クラスター上の後続の実験ノートブック セッションごとに追跡 URI を手動で設定する代わりに、この [Azure Machine Learning 追跡クラスターの Init スクリプト](https://github.com/Azure/MachineLearningNotebooks/blob/3ce779063b000e0670bdd1acc6bc3a4ee707ec13/how-to-use-azureml/azure-databricks/linking/README.md)を使用して、これを自動的に行います。

正しく構成されている場合は、Azure Machine Learning REST API およびすべてのクライアント、MLflow ユーザー インターフェイスからの Azure Databricks または MLflow クライアントを使用することで、自分の MLflow 追跡データを確認することができます。

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>ワークスペースでのメトリックと成果物の表示

MLflow ログ記録のメトリックと成果物は、お使いのワークスペースに保持されます。 それらを随時表示するには、[Azure Machine Learning Studio](https://ml.azure.com) でワークスペースに移動し、ワークスペースで名前によって実験を見つけます。  または、次のコードを実行します。 

```python
run.get_metrics()
ws.get_details()
```

## <a name="deploy-mlflow-models-as-a-web-service"></a>MLflow モデルを Web サービスとしてデプロイする

MLflow の実験を Azure Machine Learning Web サービスとしてデプロイすると、Azure Machine Learning のモデル管理機能とデータ誤差検出機能を活用し、それらを実稼働モデルに適用することができます。

次の図は、MLflow デプロイ API を使用して、フレームワーク (PyTorch、Tensorflow、scikit-learn、ONNX など) にかかわらず既存の MLflow モデルを Azure Machine Learning Web サービスとしてデプロイでき、ワークスペース内で実稼働モデルを管理できることを示しています。

![Azure Machine Learning での MLflow](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)

### <a name="log-your-model"></a>モデルをログに記録する

デプロイする前に、モデルとそのデプロイ用のパスの場所を参照できるように、必ずモデルを保存します。 トレーニング スクリプトには、指定された出力ディレクトリにモデルを保存する、次の [mlflow.sklearn.log_model()](https://www.mlflow.org/docs/latest/python_api/mlflow.sklearn.html) メソッドと同様のコードがあるはずです。 

```python
# change sklearn to pytorch, tensorflow, etc. based on your experiment's framework 
import mlflow.sklearn

# Save the model to the outputs directory for capture
mlflow.sklearn.log_model(regression_model, model_save_path)
```
>[!NOTE]
> `conda_env` パラメーターを含めて、このモデルを実行すべき依存関係と環境のディクショナリ表現を渡します。

### <a name="retrieve-model-from-previous-run"></a>前回の実行からモデルを取得する

実行を取得するには、実行 ID と、モデルが保存された場所の実行履歴のパスが必要です。 

```python
# gets the list of runs for your experiment as an array
experiment_name = 'experiment-with-mlflow'
exp = ws.experiments[experiment_name]
runs = list(exp.get_runs())

# get the run ID and the path in run history
runid = runs[0].id
model_save_path = 'model'
```

### <a name="deploy-the-model"></a>モデルをデプロイする

Azure Machine Learning SDK を使用し、モデルを Web サービスとしてデプロイします。

最初にデプロイ構成を指定します。 Azure Container Instance (ACI) は、迅速な開発テストのデプロイに適した選択肢です。一方、Azure Kubernetes Service (AKS) は、スケーラブルな実稼働のデプロイに適しています。

#### <a name="deploy-to-aci"></a>ACI にデプロイする

[deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) メソッドを使用してデプロイ構成を設定します。 また、Web サービスの追跡に役立つタグや説明を追加することもできます。

```python
from azureml.core.webservice import AciWebservice, Webservice

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

次に、Azure Machine Learning SDK [deploy](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) メソッドを利用してモデルを登録し、デプロイします。 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```
#### <a name="deploy-to-aks"></a>AKS にデプロイする

AKS にデプロイするには、まず AKS クラスターを作成します。 [ComputeTarget.create()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-) メソッドを使用して、AKS クラスターを作成します。 新しいクラスターの作成には 20 分から 25 分かかる場合があります。

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aks-mlflow' 

# Create the cluster
aks_target = ComputeTarget.create(workspace=ws, 
                                  name=aks_name, 
                                  provisioning_configuration=prov_config)

aks_target.wait_for_completion(show_output = True)

print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```
[deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) メソッドを使用してデプロイ構成を設定します。 また、Web サービスの追跡に役立つタグや説明を追加することもできます。

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')


Then, deploy the image by using the Azure Machine Learning SDK [deploy()](Then, register and deploy the model by using the Azure Machine Learning SDK [deploy](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
# Webservice creation using single command
from azureml.core.webservice import AksWebservice, Webservice
(webservice, model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='my-aks', 
                      deployment_config=aks_config, 
                      tags=None, mlflow_home=None, synchronous=True)


webservice.wait_for_deployment()
```

サービスのデプロイには数分かかることがあります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

ログに記録されたメトリックと成果物をワークスペースで使用する予定がない場合、現時点では、それらを個別に削除する機能は提供されていません。 代わりに、ストレージ アカウントとワークスペースを含むリソース グループを削除すれば、課金は発生しません。

1. Azure Portal で、左端にある **[リソース グループ]** を選択します。

   ![Azure portal で削除する](./media/how-to-use-mlflow/delete-resources.png)

1. 作成したリソース グループを一覧から選択します。

1. **[リソース グループの削除]** を選択します。

1. リソース グループ名を入力します。 次に、 **[削除]** を選択します。

## <a name="example-notebooks"></a>サンプルの Notebook

[Azure ML ノートブックでの MLflow](https://aka.ms/azureml-mlflow-examples) は、この記事で提示した概念を示し、さらに詳しく説明します。

## <a name="next-steps"></a>次のステップ
* [モデルを管理します](concept-model-management-and-deployment.md)。
* [データの誤差](how-to-monitor-data-drift.md)について実稼働モデルを監視します。
