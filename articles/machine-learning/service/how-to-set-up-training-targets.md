---
title: Azure Machine Learning サービスでモデルのトレーニング用のコンピューティング ターゲットを設定する |Microsoft Docs
description: 機械学習モデルのトレーニングに使用するトレーニング環境 (コンピューティング ターゲット) を選択し、構成する方法について説明します。 Azure Machine Learning サービスを使用すると、トレーニング環境を簡単に切り替えることができます。 ローカルでトレーニングを開始し、スケール アウトする必要がある場合は、クラウド ベースのコンピューティング ターゲットに切り替えます。
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: larryfr
manager: cgronlun
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: 7eacc475145dac61db1717f1860e22cedd022262
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2018
ms.locfileid: "51231449"
---
# <a name="select-and-use-a-compute-target-to-train-your-model"></a>コンピューティング ターゲットを選択して使用し、モデルをトレーニングする

Azure Machine Learning サービスでは、さまざまな環境でモデルをトレーニングできます。 __コンピューティング ターゲット__ と呼ばれるこれらの環境はローカルまたはクラウドにできます。 このドキュメントでは、サポートされているコンピューティング ターゲットとその使用方法について学びます。

コンピューティング ターゲットは、トレーニング スクリプトを実行または Web サービスとしてモデルがデプロイされたときにモデルをホストするリソースです。 コンピューティング ターゲットは、Azure Machine Learning SDK または CLI を使用して作成および管理できます。 別のプロセス (たとえば、Azure Portal または Azure CLI) で作成されたコンピューティング ターゲットがある場合、これを Azure Machine Learning サービス ワークスペースに接続して使用できます。

マシン上でのローカル実行から始め、GPU や Azure Batch AI を備えたリモート データ サイエンス仮想マシンなどの別の環境にスケールアップおよびスケールアウトできます。 

>[!NOTE]
> この記事のコードは、Azure Machine Learning SDK バージョン 0.168 を使用してテストされました 

## <a name="supported-compute-targets"></a>サポートされているコンピューティング ターゲット

Azure Machine Learning サービスは、次のコンピューティング ターゲットをサポートしています。

|コンピューティング ターゲット| GPU アクセラレーション | 自動化されたハイパーパラメーターのチューニング | 自動化されたモデル選択 | パイプラインで使用できます|
|----|:----:|:----:|:----:|:----:|
|[ローカル コンピューター](#local)| その可能性はあります | &nbsp; | ✓ | &nbsp; |
|[データ サイエンス仮想マシン (DSVM)](#dsvm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Batch AI](#batch)| ✓ | ✓ | ✓ | ✓ |
|[Azure Databricks](#databricks)| &nbsp; | &nbsp; | &nbsp; | ✓[*](#pipeline-only) |
|[Azure Data Lake Analytics](#adla)| &nbsp; | &nbsp; | &nbsp; | ✓[*](#pipeline-only) |
|[Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |

> [!IMPORTANT]
> <a id="pipeline-only"></a>* パイプラインで使用できるのは、Azure Databricks と Azure Data Lake Analytics __のみ__ です。 パイプラインの詳細については、「[パイプラインと Azure Machine Learning](concept-ml-pipelines.md)」のドキュメントを参照してください。

__[Azure Container Instances (ACI)](#aci)__ はモデルのトレーニングにも使用できます。 安価で簡単に作成および操作できるサーバーレス クラウド サービスです。 ACI は、GPU アクセラレーション、自動化されたハイパーパラメーターのチューニング、自動化されたモデル選択をサポートしません。 また、パイプラインでは使用できません。

コンピューティング ターゲット間の主な違いは次のとおりです。
* __GPU アクセラレーション__: Data Science Virtual Machine と Azure Batch AI で GPU を利用できます。 インストールされているハードウェア、ドライバー、およびフレームワークに応じてローカル コンピューターの GPU にアクセスできます。
* __自動化されたハイパーパラメーターのチューニング__: Azure Machine Learning の自動化されたハイパーパラメーターの最適化を使用すると、お使いのモデルに最適なハイパーパラメーターを検出できます。
* __自動化されたモデル選択__: Azure Machine Learning サービスは、モデルを構築する際にアルゴリズムとハイパーパラメーターの選択をインテリジェントに推奨できます。 自動化されたモデル選択を使用すると、高品質なモデルに到達する際、手動でさまざまな組み合わせを試みるよりも速く到達できます。 詳細については、「[チュートリアル: Azure Automated Machine Learning によるの分類モデルの自動トレーニング](tutorial-auto-train-models.md)」のドキュメントを参照してください。
* __パイプライン__: Azure Machine Learning サービスでは、トレーニングやパイプラインへのデプロイなどのさまざまなタスクを組み合わせることができます。 パイプラインは並列または連続で実行できます。また、信頼できる自動化メカニズムを提供します。 詳細については、「[Azure Machine Learning サービスによる機械学習パイプラインの構築](concept-ml-pipelines.md)」のドキュメントを参照してください。

Azure Machine Learning SDK、Azure CLI、または Azure Portal を使用してコンピューティング ターゲットを作成できます。 既存のコンピューティング ターゲットをワークスペースに追加 (接続) することによって既存のコンピューティング ターゲットを使用することもできます。

> [!IMPORTANT]
> 既存の Azure コンテナー インスタンスはワークスペースに接続できません。 代わりに、新しいインスタンスを作成する必要があります。
>
> ワークスペース内に Azure HDInsight、Azure Databricks、または Azure Data Lake Store を作成することはできません。 代わりに、リソースを作成してから、それをワークスペースに接続する必要があります。

## <a name="workflow"></a>ワークフロー

Azure Machine Learning でモデルを開発およびデプロイするワークフローは次の手順に従います。

1. Python で機械学習トレーニング スクリプトを開発します。
1. コンピューティング ターゲットを作成して構成するか、既存のコンピューティング ターゲットを接続します。
1. コンピューティング ターゲットにトレーニング スクリプトを送信します。
1. 結果を検査して最適なモデルを見つけます。
1. モデル レジストリにモデルを登録します。
1. モデルをデプロイします。

> [!IMPORTANT]
> トレーニング スクリプトは特定のコンピューティング ターゲットに関連付けられていません。 最初ローカル コンピューターでトレーニングし、トレーニング スクリプトを書き直すことなくコンピューティング ターゲットを切り替えることができます。

あるコンピューティング ターゲットを別のコンピューティング ターゲットに切り替えるには、[実行構成](concept-azure-machine-learning-architecture.md#run-configuration)を作成する必要があります。 実行構成は、コンピューティング ターゲットでスクリプトを実行する方法を定義します。

## <a name="training-scripts"></a>トレーニング スクリプト

トレーニングの実行を開始すると、トレーニング スクリプトを含むディレクトリ全体が送信されます。 スナップショットが作成され、コンピューティング ターゲットに送信されます。 詳細については、[スナップショット](concept-azure-machine-learning-architecture.md#snapshot)を参照してください。

## <a id="local"></a>ローカル コンピューター

ローカルでのトレーニングするとき、SDK を使用してトレーニング操作を送信します。 ユーザー管理環境またはシステム管理環境を使用してトレーニングできます。

### <a name="user-managed-environment"></a>ユーザー管理環境

ユーザー管理環境では、スクリプトを実行する Python 環境で必要なすべてのパッケージが使用できることを確認する必要があります。 次のコード スニペットは、ユーザー管理環境でのトレーニング構成例です。

```python
from azureml.core.runconfig import RunConfiguration

# Editing a run configuration property on-fly.
run_config_user_managed = RunConfiguration()

run_config_user_managed.environment.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
#run_config.environment.python.interpreter_path = '/home/ninghai/miniconda3/envs/sdk2/bin/python'
```

ユーザーが管理する環境でのトレーニングを示す Jupyter Notebook については、[https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local/02.train-on-local.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local/02.train-on-local.ipynb) を参照してください。
  
### <a name="system-managed-environment"></a>システム管理環境

システム管理環境は、conda を使用して依存関係を管理します。 conda は、依存関係のリストを含む `conda_dependencies.yml` という名前のファイルを作成します。 その後、新しい conda 環境の構築をシステムに指示し、そこでスクリプトを実行できます。 システム管理環境は、`conda_dependencies.yml` ファイルが変更されていない限り、後で再利用できます。 

新しい環境の初期セットアップは、必要な依存関係のサイズに応じて完了までに数分かかります。 次のコード スニペットは、scikit に依存するシステム管理環境を作成します。

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config_system_managed = RunConfiguration()

run_config_system_managed.environment.python.user_managed_dependencies = False
run_config_system_managed.auto_prepare_environment = True

# Specify conda dependencies with scikit-learn

run_config_system_managed.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

システムが管理する環境でのトレーニングを示す Jupyter Notebook については、[https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local/02.train-on-local.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local/02.train-on-local.ipynb) を参照してください。

## <a id="dsvm"></a>Data Science Virtual Machine

モデルのトレーニングに必要な計算リソースまたは GPU リソースがローカル コンピューターにない場合があります。 このような状況では、トレーニング プロセスをスケール アップまたはスケール アウトできます。そのためには。Data Science Virtual Machine (DSVM) などコンピューティング ターゲットを追加します。

> [!WARNING]
> Azure Machine Learning は、Ubuntu を実行する仮想マシンのみサポートします。 仮想マシンを作成または既存の仮想マシンを選択するとき、Ubuntu を使用するマシンを選択する必要があります。

次の手順では、SDK を使用してトレーニング ターゲットとしてデータ サイエンス仮想マシン (DSVM) を構成します。

1. 仮想マシンの作成または接続
    
    * 新しい DSVM を作成するには、まず同じ名前の DSVM があるかどうかを確認し、ない場合には新しい VM を作成します。
    
        ```python
        from azureml.core.compute import DsvmCompute
        from azureml.core.compute_target import ComputeTargetException

        compute_target_name = 'mydsvm'

        try:
            dsvm_compute = DsvmCompute(workspace = ws, name = compute_target_name)
            print('found existing:', dsvm_compute.name)
        except ComputeTargetException:
            print('creating new.')
            dsvm_config = DsvmCompute.provisioning_configuration(vm_size = "Standard_D2_v2")
            dsvm_compute = DsvmCompute.create(ws, name = compute_target_name, provisioning_configuration = dsvm_config)
            dsvm_compute.wait_for_completion(show_output = True)
        ```
    * コンピューティング ターゲットとして既存の仮想マシンを接続するには、仮想マシンの完全修飾ドメイン名、ログイン名、およびパスワードを入力する必要があります。  例では、```<fqdn>``` を VM のパブリックの完全修飾ドメイン名、つまりパブリック IP アドレスに置き換えます。 ```<username>``` と ```<password>``` を SSH ユーザーと VM のパスワードに置き換えます。

        ```python
        from azureml.core.compute import RemoteCompute

        dsvm_compute = RemoteCompute.attach(ws,
                                        name="attach-dsvm",
                                        username='<username>',
                                        address="<fqdn>",
                                        ssh_port=22,
                                        password="<password>")

        dsvm_compute.wait_for_completion(show_output=True)
    
   DSVM インスタンスを作成するのに約 5 分かかります。

1. DSVMコンピューティングターゲットを作成します。 ここではDocker とconda を使用して、DSVM 上のトレーニング環境を作成および構成します。:

    ```python
    from azureml.core.runconfig import RunConfiguration
    from azureml.core.conda_dependencies import CondaDependencies

    # Load the "cpu-dsvm.runconfig" file (created by the above attach operation) in memory
    run_config = RunConfiguration(framework = "python")

    # Set compute target to the Linux DSVM
    run_config.target = compute_target_name

    # Use Docker in the remote VM
    run_config.environment.docker.enabled = True

    # Use CPU base image
    # If you want to use GPU in DSVM, you must also use GPU base Docker image azureml.core.runconfig.DEFAULT_GPU_IMAGE
    run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
    print('Base Docker image is:', run_config.environment.docker.base_image)

    # Ask system to provision a new one based on the conda_dependencies.yml file
    run_config.environment.python.user_managed_dependencies = False

    # Prepare the Docker and conda environment automatically when used the first time.
    run_config.prepare_environment = True

    # specify CondaDependencies obj
    run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])

    ```

1. 完了したときに計算リソースを削除するには、次のコードを使用します。

    ```python
    dsvm_compute.delete()
    ```

Data Science Virtual Machine でのトレーニングを示す Jupyter Notebook については、[https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/04.train-on-remote-vm/04.train-on-remote-vm.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/04.train-on-remote-vm/04.train-on-remote-vm.ipynb) を参照してください。

## <a id="batch"></a>Azure Batch AI

モデルのトレーニングに時間がかかる場合は、Azure Batch AI を使用して、クラウドの計算リソースのクラスター間にトレーニングを分散できます。 Batch AI を構成して GPU リソースを有効にすることも可能です。

次の例では名前で既存の Batch AI クラスターを検索します。 クラスターは、見つからない場合には作成されます。

```python
from azureml.core.compute import BatchAiCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
batchai_cluster_name = os.environ.get("BATCHAI_CLUSTER_NAME", ws.name + "gpu")
cluster_min_nodes = os.environ.get("BATCHAI_CLUSTER_MIN_NODES", 1)
cluster_max_nodes = os.environ.get("BATCHAI_CLUSTER_MAX_NODES", 3)
vm_size = os.environ.get("BATCHAI_CLUSTER_SKU", "STANDARD_NC6")
autoscale_enabled = os.environ.get("BATCHAI_CLUSTER_AUTOSCALE_ENABLED", True)


if batchai_cluster_name in ws.compute_targets():
    compute_target = ws.compute_targets()[batchai_cluster_name]
    if compute_target and type(compute_target) is BatchAiCompute:
        print('found compute target. just use it. ' + batchai_cluster_name)
else:
    print('creating a new compute target...')
    provisioning_config = BatchAiCompute.provisioning_configuration(vm_size = vm_size, # NC6 is GPU-enabled
                                                                vm_priority = 'lowpriority', # optional
                                                                autoscale_enabled = autoscale_enabled,
                                                                cluster_min_nodes = cluster_min_nodes, 
                                                                cluster_max_nodes = cluster_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, batchai_cluster_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current BatchAI cluster status, use the 'status' property    
    print(compute_target.status.serialize())
```

コンピューティング ターゲットとして既存の Batch AI クラスターに接続するには、Azure のリソース ID を入力する必要があります。 Azure portal からリソース ID を取得するには、次の手順を使用します。
1. **[すべてのサービス]** で `Batch AI` サービスを検索します
1. クラスターが属しているワークスペース名をクリックします
1. クラスターを選択します
1. **[プロパティ]** をクリックします
1. **ID** をコピーします

次の例では、SDK を使用してクラスターをワークスペースに接続します。 この例では、`<name>` を計算の名前に置き換えます。 この名前は、クラスターの名前と一致させる必要はありません。 `<resource-id>` を上述の Azure リソース ID に置き換えます。

```python
from azureml.core.compute import BatchAiCompute
BatchAiCompute.attach(workspace=ws,
                      name=<name>,
                      resource_id=<resource-id>)
```

次の Azure CLI コマンドを使用して Batch AI クラスターとジョブの状態をチェックすることもできます。

- クラスターの状態を確認します。 `az batchai cluster list` を使用して実行しているノードの数を確認できます。
- ジョブの状態を確認します。 `az batchai job list` を使用して実行しているジョブの数を確認できます。

Batch AI クラスターの作成には約 5 分かかります。

Batch AI クラスターでのトレーニングを示す Jupyter Notebook については、[https://github.com/Azure/MachineLearningNotebooks/blob/master/training/03.train-hyperparameter-tune-deploy-with-tensorflow/03.train-hyperparameter-tune-deploy-with-tensorflow.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/training/03.train-hyperparameter-tune-deploy-with-tensorflow/03.train-hyperparameter-tune-deploy-with-tensorflow.ipynb) を参照してください。

## <a name='aci'></a>Azure コンテナー インスタンス (ACI)

Azure Container Instances は、高速起動し、仮想マシンの管理をユーザーが行う必要がない分離されたコンテナーです。 Azure Machine Learning サービスは、westus、eastus、westeurope、northeurope、westus2、および southeastasia リージョンで使用可能な Linux コンテナーを使用します。 詳細については、「[利用可能なリージョン](https://docs.microsoft.com/azure/container-instances/container-instances-quotas#region-availability)」をご覧ください。 

次の例では、SDK を使用して ACI のコンピューティング ターゲットを作成し、モデルのトレーニングに使用する方法を示します。 

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

# create a new runconfig object
run_config = RunConfiguration()

# signal that you want to use ACI to run script.
run_config.target = "containerinstance"

# ACI container group is only supported in certain regions, which can be different than the region the Workspace is in.
run_config.container_instance.region = 'eastus'

# set the ACI CPU and Memory 
run_config.container_instance.cpu_cores = 1
run_config.container_instance.memory_gb = 2

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE

# use conda_dependencies.yml to create a conda environment in the Docker image
run_config.environment.python.user_managed_dependencies = False

# auto-prepare the Docker image when used for the first time (if it is not already prepared)
run_config.auto_prepare_environment = True

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

ACI のコンピューティング ターゲットの作成には、数秒から数分 かかります。

Azure コンテナー インスタンスでのトレーニングを示す Jupyter Notebook については、[https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/03.train-on-aci/03.train-on-aci.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/03.train-on-aci/03.train-on-aci.ipynb) を参照してください。

## <a id="databricks"></a>Azure Databricks

Azure Databricks は、Azure クラウド内の Apache Spark ベースの環境です。 これは、Azure Machine Learning パイプラインでモデルをトレーニングする際に、コンピューティング ターゲットとして使用できます。

> [!IMPORTANT]
> Azure Databricks コンピューティング ターゲットは、Machine Learning パイプラインでのみ使用できます。
>
> モデルのトレーニングに使用する前に、Azure Databricks ワークスペースを作成する必要があります。 これらのリソースを作成するには、[Azure Databricks での Spark ジョブの実行](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)に関するドキュメントを参照してください。

コンピューティング ターゲットとして Azure Databricks に接続するには、Azure Machine Learning SDK を使用し、次の情報を提供する必要があります。

* __コンピューティング名__: このコンピューティング リソースに割り当てる名前。
* __リソース ID__: Azure Databricks ワークスペースのリソース ID。 次のテキストは、この値の形式の例です。

    ```text
    /subscriptions/<your_subscription>/resourceGroups/<resource-group-name>/providers/Microsoft.Databricks/workspaces/<databricks-workspace-name>
    ```

    > [!TIP]
    > リソース ID を取得するには、次の Azure CLI コマンドを使います。 `<databricks-ws>` を Databricks ワークスペースの名前に置き換えます。
    > ```azurecli-interactive
    > az resource list --name <databricks-ws> --query [].id
    > ```

* __アクセス トークン__: Azure Databricks に対する認証に使用するアクセス トークン。 アクセス トークンを生成するには、[認証](https://docs.azuredatabricks.net/api/latest/authentication.html)に関するドキュメントを参照してください。

次のコードは、コンピューティング ターゲットとして Azure Databricks に接続する方法を示しています。

```python
databricks_compute_name = os.environ.get("AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_resource_id = os.environ.get("AML_DATABRICKS_RESOURCE_ID", "<databricks_resource_id>")
databricks_access_token = os.environ.get("AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_resource_id {}'.format(databricks_resource_id))
    print('databricks_access_token {}'.format(databricks_access_token))
    databricks_compute = DatabricksCompute.attach(
             workspace=ws,
             name=databricks_compute_name,
             resource_id=databricks_resource_id,
             access_token=databricks_access_token
         )
    
    databricks_compute.wait_for_completion(True)
```

## <a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics は、Azure クラウド内のビッグ データ分析プラットフォームです。 これは、Azure Machine Learning パイプラインでモデルをトレーニングする際に、コンピューティング ターゲットとして使用できます。

> [!IMPORTANT]
> Azure Data Lake Analytics コンピューティング ターゲットは、Machine Learning パイプラインでのみ使用できます。
>
> モデルのトレーニングに使用する前に、Azure Data Lake Analytics ワークスペースを作成する必要があります。 このリソースを作成するには、「[Azure Data Lake Analytics の使用を開始する](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal)」を参照してください。

コンピューティング ターゲットとして Data Lake Analytics に接続するには、Azure Machine Learning SDK を使用し、次の情報を提供する必要があります。

* __コンピューティング名__: このコンピューティング リソースに割り当てる名前。
* __リソース ID__: Data Lake Analytics アカウントのリソース ID。 次のテキストは、この値の形式の例です。

    ```text
    /subscriptions/<your_subscription>/resourceGroups/<resource-group-name>/providers/Microsoft.DataLakeAnalytics/accounts/<datalakeanalytics-name>
    ```

    > [!TIP]
    > リソース ID を取得するには、次の Azure CLI コマンドを使います。 `<datalakeanalytics>` を Data Lake Analytics アカウントの名前に置き換えます。
    > ```azurecli-interactive
    > az resource list --name <datalakeanalytics> --query [].id
    > ```

次のコードは、コンピューティング ターゲットとして Data Lake Analytics に接続する方法を示しています。

```python
adla_compute_name = os.environ.get("AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_id = os.environ.get("AML_ADLA_RESOURCE_ID", "<adla_resource_id>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_id))
    adla_compute = AdlaCompute.attach(
             workspace=ws,
             name=adla_compute_name,
             resource_id=adla_resource_id
         )
    
    adla_compute.wait_for_completion(True)
```

> [!TIP]
> Azure Machine Learning パイプラインは、Data Lake Analytics アカウントの既定のデータ ストアに格納されたデータのみ使用できます。 使用する必要があるデータが既定以外のストアにある場合は、[`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) を使用して、トレーニングの前にデータをコピーできます。

## <a id="hdinsight"></a>HDInsight クラスターの接続 

HDInsight は、ビッグ データ分析のための一般的なプラットフォームです。 モデルのトレーニングに使用できる Apache Spark を提供します。

> [!IMPORTANT]
> モデルのトレーニングに使用する前に、HDInsight クラスターを作成する必要があります。 HDInsight クラスターで Spark を作成するには、「[HDInsight で Spark クラスターを作成する」](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql)のドキュメントを参照してください。
>
> クラスターを作成するとき、SSH ユーザー名とパスワードを指定する必要があります。 コンピューティング ターゲットとして HDInsight を使用するときに必要になるので、これらの値をメモしておいてください。
>
> クラスターが作成されると、その完全修飾ドメイン名 (FQDN) は `<clustername>.azurehdinsight.net` になります。ここで、`<clustername>` はクラスターに付けた名前です。 コンピューティング ターゲットとしてこれを使用するには、このアドレス (またはクラスターのパブリック IP アドレス) が必要です。

コンピューティング ターゲットとして HDInsight を構成するには、HDInsight クラスターの完全修飾ドメイン名、クラスターのログイン名、およびパスワードを入力する必要があります。 次の例では、SDK を使用してクラスターをワークスペースに接続します。 この例では、`<fqdn>` をクラスターのパブリックの完全修飾ドメイン名またはパブリック IP アドレスに置き換えます。 `<username>` および `<password>` を SSH ユーザーおよびクラスターのパスワードに置き換えます。

> [!NOTE]
> クラスターの FQDN を検索するには、Azure Portal にアクセスし、HDInsight クラスターを選択します。 __[概要]__ セクションで、FQDN は __URL__ エントリの一部です。 値の先頭から `https://` を削除します。
>
> ![URL エントリが強調表示されている HDInsight クラスターの概要のスクリーン ショット](./media/how-to-set-up-training-targets/hdinsight-overview.png)

```python
from azureml.core.compute import HDInsightCompute

try:
    # Attaches a HDInsight cluster as a compute target.
    HDInsightCompute.attach(ws,name = "myhdi",
                            address = "<fqdn>",
                            username = "<username>",
                            password = "<password>")
except UserErrorException as e:
    print("Caught = {}".format(e.message))
    print("Compute config already attached.")

# Configure HDInsight run
# load the runconfig object from the "myhdi.runconfig" file generated by the attach operaton above.
run_config = RunConfiguration.load(project_object = project, run_config_name = 'myhdi')

# ask system to prepare the conda environment automatically when used for the first time
run_config.auto_prepare_environment = True
```

## <a name="submit-training-run"></a>トレーニングの実行の送信

トレーニングの実行を送信するには、2 つの方法があります。

* `ScriptRunConfig` オブジェクトを送信する。
* `Pipeline` オブジェクトを送信する。

> [!IMPORTANT]
> Azure Databricks および Azure Datalake Analytics のコンピューティング ターゲットは、パイプラインでのみ使用できます。
> ローカルのコンピューティング ターゲットは、パイプラインでは使用できません。

### <a name="submit-using-scriptrunconfig"></a>`ScriptRunConfig` を使用して送信する

`ScriptRunConfig` を使用してトレーニングの実行を送信するコード パターンは、コンピューティング ターゲットに関係なく同じです。

* コンピューティング ターゲットの実行構成を使用して `ScriptRunConfig` オブジェクトを作成します。
* 実行を送信します。
* 実行が完了するのを待ちます。

次の例では、このドキュメントで作成済みのシステム管理のローカル コンピューティング ターゲットの構成を使用します。

```python
src = ScriptRunConfig(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
run = exp.submit(src)
run.wait_for_completion(show_output = True)
```

Spark on HDInsight でのトレーニングを示す Jupyter Notebook については、[https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/05.train-in-spark/05.train-in-spark.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/05.train-in-spark/05.train-in-spark.ipynb) を参照してください。

### <a name="submit-using-a-pipeline"></a>パイプラインを使用して送信する

パイプラインを使用してトレーニングの実行を送信するコード パターンは、コンピューティング ターゲットに関係なく同じです。

* コンピューティング リソースに対するパイプラインに 1 つのステップを追加します。
* パイプラインを使用して実行を送信します。
* 実行が完了するのを待ちます。

次の例では、このドキュメントで作成済みの Azure Databricks コンピューティング ターゲットを使用します。

```python
dbStep = DatabricksStep(
    name="databricksmodule",
    inputs=[step_1_input],
    outputs=[step_1_output],
    num_workers=1,
    notebook_path=notebook_path,
    notebook_params={'myparam': 'testparam'},
    run_name='demo run name',
    databricks_compute=databricks_compute,
    allow_reuse=False
)
# list of steps to run
steps = [dbStep]
pipeline = Pipeline(workspace=ws, steps=steps)
pipeline_run = Experiment(ws, 'Demo_experiment').submit(pipeline)
pipeline_run.wait_for_completion()
```

機械学習パイプラインの詳細については、「[パイプラインと Azure Machine Learning](concept-ml-pipelines.md)」のドキュメントを参照してください。

パイプラインを使用してトレーニングを示す Jupyter Notebook の例については、[https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline) を参照してください。

## <a name="view-and-set-up-compute-using-the-azure-portal"></a>Azure Portal を使用した計算の表示および設定

Azure Portal からワークスペースに関連付けられたコンピューティング ターゲットを表示できます。 一覧を取得するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) にアクセスし、ワークスペースに移動します。
2. __[アプリケーション]__ セクションの __[計算]__ リンクをクリックします。

    ![[計算] タブを表示する](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a name="create-a-compute-target"></a>コンピューティング ターゲットを作成する

上記の手順に従ってコンピューティング ターゲットの一覧を表示し、次の手順に従ってコンピューティング ターゲットを作成します。

1. __+__ 記号をクリックし、コンピューティング ターゲットを追加します。

    ![計算の追加 ](./media/how-to-set-up-training-targets/add-compute-target.png)

1. コンピューティング ターゲットの名前を入力します。
1. __トレーニング__ に接続する計算の種類を選択します。 

    > [!IMPORTANT]
    > Azure portal を使用してすべてのコンピューティングの種類を作成できるわけではありません。 現在トレーニング用に作成できる種類は次のとおりです。
    > 
    > * 仮想マシン
    > * Batch AI

1. __[新規作成]__ を選択し、必要なフォームに入力します。 
1. __[作成]__
1. 一覧からコンピューティング ターゲットを選択することによって、状態作成操作を表示できます。

    ![計算一覧の表示](./media/how-to-set-up-training-targets/View_list.png) コンピューティング ターゲットの詳細が表示されます。
    ![詳細の表示](./media/how-to-set-up-training-targets/vm_view.PNG)
1. 上述のように、これらのターゲットに対して実行を送信できるようになりました。

### <a name="reuse-existing-compute-in-your-workspace"></a>ワークスペース内の既存の計算の再利用

上記の手順に従ってコンピューティング ターゲットの一覧を表示し、次の手順に従ってコンピューティング ターゲットを再利用します。

1. **+** 記号をクリックし、コンピューティング ターゲットを追加します。
2. コンピューティング ターゲットの名前を入力します。
3. トレーニング用に接続する計算の種類を選択します。

    > [!IMPORTANT]
    > ポータルを使用してすべてのコンピューティングの種類を接続できるわけではありません。
    > 現在トレーニング用に接続できる種類は次のとおりです。
    > 
    > * 仮想マシン
    > * Batch AI

1. [既存を使用] を選択します。
    - Batch AI クラスターに接続するときにドロップダウン リストからコンピューティング ターゲットを選択し、Batch AI ワークスペースおよび Batch AI クラスターを選択し、**[作成]** をクリックします。
    - 仮想マシンに接続するときに IP アドレス、ユーザー名/パスワードの組み合わせ、秘密/公開キー、およびポートを入力し、[作成] をクリックします。

    > [!NOTE]
    > パスワードよりも安全な SSH キーを使用することをお勧めします。 パスワードはブルート フォース攻撃に対して脆弱ですが、SSH キーは暗号署名を使用しています。 Azure Virtual Machines で使用するための SSH キーを作成する方法の詳細については、次のドキュメントを参照してください。
    >
    > * [Linux または macOS で SSH キーを作成して使用する]( https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Windows で SSH キーを作成して使用する]( https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

5. プロビジョニング状態のステータスを表示するには、一覧からコンピューティング ターゲットを選択します。
6. これらのターゲットに対して実行を送信できるようになりました。

## <a name="examples"></a>例
次の Notebook は、この記事の概念を示しています。
* [01.getting-started/02.train-on-local/02.train-on-local.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local)
* [01.getting-started/04.train-on-remote-vm/04.train-on-remote-vm.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/04.train-on-remote-vm)
* [01.getting-started/03.train-on-aci/03.train-on-aci.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/03.train-on-aci)
* [01.getting-started/05.train-in-spark/05.train-in-spark.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/05.train-in-spark)
* [tutorials/01.train-models.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/01.train-models.ipynb)

これらの Notebook を取得するには: [!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>次の手順

* [Azure Machine Learning SDK リファレンス](https://aka.ms/aml-sdk)
* [チュートリアル: モデルのトレーニング](tutorial-train-models-with-aml.md)
* [モデルをデプロイする場所](how-to-deploy-and-where.md)
* [Azure Machine Learning サービスで機械学習パイプラインの構築](concept-ml-pipelines.md)
