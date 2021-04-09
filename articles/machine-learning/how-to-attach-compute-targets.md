---
title: トレーニングと推論のコンピューティング先を設定する
titleSuffix: Azure Machine Learning
description: 機械学習のトレーニングと推論に使用するためにコンピューティング リソース (コンピューティング先) をワークスペースに追加する
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 10/02/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: 9fa6a1758bc2e2a76291efc3bb239c5249a6e21e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103149343"
---
# <a name="set-up-compute-targets-for-model-training-and-deployment"></a>モデルのトレーニングとデプロイのためのコンピューティング ターゲットを設定する

Azure Machine Learning ワークスペースに Azure のコンピューティング リソースをアタッチする方法について説明します。  その後、機械学習タスクで、これらのリソースをトレーニングおよび推論の[コンピューティング先](concept-compute-target.md)として使用できます。

この記事では、以下のコンピューティング リソースを使用するためにワークスペースを設定する方法について説明します。

* ユーザーのローカル コンピューター
* リモート仮想マシン
* Azure HDInsight
* Azure Batch
* Azure Databricks
* Azure Data Lake Analytics
* Azure Container Instances

Azure Machine Learning によって管理されるコンピューティング先を使用するには、以下を参照してください。


* [Azure Machine Learning コンピューティング インスタンス](how-to-create-manage-compute-instance.md)
* [Azure Machine Learning コンピューティング クラスター](how-to-create-attach-compute-cluster.md)
* [Azure Kubernetes Service クラスター](how-to-create-attach-kubernetes.md)

## <a name="prerequisites"></a>前提条件

* Azure Machine Learning ワークスペース。 詳細については、[Azure Machine Learning ワークスペースの作成](how-to-manage-workspace.md)に関するページをご覧ください。

* [Machine Learning サービス向けの Azure CLI 拡張機能](reference-azure-machine-learning-cli.md)、[Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro)、または [Azure Machine Learning Visual Studio Code 拡張機能](tutorial-setup-vscode-extension.md)。

## <a name="limitations"></a>制限事項

* ワークスペースから **同じコンピューティングに対して複数のアタッチメントを同時に作成することは避けてください**。 たとえば、2 つの異なる名前を使用して 1 つの Azure Kubernetes Service クラスターをワークスペースにアタッチすることが該当します。 アタッチを繰り返すたびに、先行する既存のアタッチメントが切断されます。

    TLS またはその他のクラスター構成設定を変更するためなど、コンピューティング先を再アタッチする場合は、まず、既存のアタッチメントを削除する必要があります。

## <a name="whats-a-compute-target"></a>コンピューティング先とは

Azure Machine Learning では、さまざまなリソースまたは環境でご利用のモデルをトレーニングでき、それらを総称して [__コンピューティング先__](concept-azure-machine-learning-architecture.md#compute-targets)と呼びます。 コンピューティング先は、ローカル マシンでも、Azure Machine Learning コンピューティング、Azure HDInsight、リモート仮想マシンなどのクラウド リソースでもかまいません。  コンピューティング先は、[モデル デプロイの場所と方法](how-to-deploy-and-where.md)に関するページで説明されているように、モデル デプロイにも使用します。


## <a name="local-computer"></a><a id="local"></a>ローカル コンピューター

**トレーニング** のためにローカル コンピューターを使用する場合は、コンピューティング先を作成する必要はありません。  ローカル コンピューターから[トレーニング実行を送信する](how-to-set-up-training-targets.md)だけで十分です。

ローカル コンピューターを **推論** に使用する場合は、Docker がインストールされている必要があります。 デプロイを実行するには、[LocalWebservice.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.local.localwebservice#deploy-configuration-port-none-) を使用して、Web サービスが使用するポートを定義します。 次に、「[Azure Machine Learning を使用してモデルをデプロイする](how-to-deploy-and-where.md)」の説明に従って通常のデプロイ プロセスを使用します。

## <a name="remote-virtual-machines"></a><a id="vm"></a>リモート仮想マシン

Azure Machine Learning では、Azure 仮想マシンのアタッチもサポートされています。 VM は、Azure Data Science Virtual Machine (DSVM) である必要があります。 この VM は、Azure での事前構成済みのデータ サイエンスおよび AI 開発環境です。 その VM では、完全なライフサイクルの機械学習開発用に精選されたツールとフレームワークが提供されます。 Azure Machine Learning での DSVM の使用方法について詳しくは、[開発環境の構成](./how-to-configure-environment.md#dsvm)に関する記事をご覧ください。

1. **作成**:モデルのトレーニングに使用する DSVM を事前に作成します。 このリソースの作成については、「[Linux (Ubuntu) データ サイエンス仮想マシンのプロビジョニング](./data-science-virtual-machine/dsvm-ubuntu-intro.md)」をご覧ください。

    > [!WARNING]
    > Azure Machine Learning では、**Ubuntu** を実行する仮想マシンのみがサポートされます。 VM を作成するとき、または既存の VM を選択するときは、Ubuntu を使用する VM を選択する必要があります。
    > 
    > さらに Azure Machine Learning では、仮想マシンに __パブリック IP アドレス__ が必要です。

1. **アタッチする**:コンピューティング ターゲットとして既存の仮想マシンを接続するには、仮想マシンのリソース ID、ユーザー名、およびパスワードを入力する必要があります。 VM のリソース ID は、次の文字列形式を使用して、サブスクリプション ID、リソース グループ名、VM 名から作成できます: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   または、[Azure Machine Learning Studio を使用して](how-to-create-attach-compute-studio.md#attached-compute)、DSVM をワークスペースにアタッチすることもできます。

    > [!WARNING]
    > ワークスペースから同じ DSVM に対して複数のアタッチメントを同時に作成することは避けてください。 アタッチを繰り返すたびに、先行する既存のアタッチメントが切断されます。

1. **構成する**:DSVM コンピューティング先用の実行構成を作成します。 Docker と conda は、DSVM でトレーニング環境を作成および構成するために使用されます。

   ```python
   from azureml.core import ScriptRunConfig
   from azureml.core.environment import Environment
   from azureml.core.conda_dependencies import CondaDependencies
   
   # Create environment
   myenv = Environment(name="myenv")
   
   # Specify the conda dependencies
   myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
   
   # If no base image is explicitly specified the default CPU image "azureml.core.runconfig.DEFAULT_CPU_IMAGE" will be used
   # To use GPU in DSVM, you should specify the default GPU base Docker image or another GPU-enabled image:
   # myenv.docker.enabled = True
   # myenv.docker.base_image = azureml.core.runconfig.DEFAULT_GPU_IMAGE
   
   # Configure the run configuration with the Linux DSVM as the compute target and the environment defined above
   src = ScriptRunConfig(source_directory=".", script="train.py", compute_target=compute, environment=myenv) 
   ```

## <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight は、ビッグ データ分析のための一般的なプラットフォームです。 そのプラットフォームでは、モデルのトレーニングに使用できる Apache Spark が提供されます。

1. **作成**:モデルのトレーニングに使用する HDInsight クラスターを、事前に作成します。 HDInsight クラスターで Spark を作成するには、[HDInsight での Spark クラスターの作成](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)に関する記事をご覧ください。 

    > [!WARNING]
    > Azure Machine Learning では、HDInsight クラスターに __パブリック IP アドレス__ が必要です。

    クラスターを作成するとき、SSH ユーザー名とパスワードを指定する必要があります。 コンピューティング先として HDInsight を使用するときに必要になるので、これらの値をメモしておいてください。
    
    クラスターが作成された後、ホスト名 \<clustername>-ssh.azurehdinsight.net でそれに接続します。\<clustername> はクラスターに指定した名前です。 

1. **アタッチする**:コンピューティング先として HDInsight クラスターをアタッチするには、HDInsight クラスターのリソース ID、ユーザー名、およびパスワードを指定する必要があります。 HDInsight クラスターのリソース ID は、次の文字列形式を使用して、サブスクリプション ID、リソース グループ名、HDInsight クラスター名から作成できます: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`

    ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase

    attach_config = HDInsightCompute.attach_configuration(resource_id='<resource_id>',
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   または、[Azure Machine Learning Studio を使用して](how-to-create-attach-compute-studio.md#attached-compute)、HDInsight クラスターをワークスペースにアタッチすることもできます。

    > [!WARNING]
    > ワークスペースから同じ HDInsight に対して複数のアタッチメントを同時に作成することは避けてください。 アタッチを繰り返すたびに、先行する既存のアタッチメントが切断されます。

1. **構成する**:HDI コンピューティング先用の実行構成を作成します。 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


コンピューティングをアタッチし、実行を構成したので、次のステップでは[トレーニング実行を送信](how-to-set-up-training-targets.md)します。

## <a name="azure-batch"></a><a id="azbatch"></a>Azure Batch 

Azure Batch は、大規模な並列コンピューティングやハイパフォーマンス コンピューティング (HPC) のアプリケーションをクラウドで効率的に実行するために使用されます。 AzureBatchStep を Azure Machine Learning Pipeline で使用して、マシンの Azure Batch プールにジョブを送信できます。

コンピューティング ターゲットとして Azure Batch に接続するには、Azure Machine Learning SDK を使用し、次の情報を提供する必要があります。

-    **Azure Batch のコンピューティング名**:ワークスペース内のコンピューティングに使用されるフレンドリ名
-    **Azure Batch アカウント名**:Azure Batch アカウントの名前
-    **リソース グループ**:Azure Batch アカウントを含むリソース グループ。

次のコードは、コンピューティング ターゲットとして Azure Batch に接続する方法を示しています。

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

> [!WARNING]
> ワークスペースから同じ Azure Batch に対して複数のアタッチメントを同時に作成することは避けてください。 アタッチを繰り返すたびに、先行する既存のアタッチメントが切断されます。

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

Azure Databricks は、Azure クラウド内の Apache Spark ベースの環境です。 これは、Azure Machine Learning パイプラインでコンピューティング先として使用できます。

使用する前に、Azure Databricks ワークスペースを作成します。 ワークスペース リソースを作成するには、[Azure Databricks での Spark ジョブの実行](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)に関するドキュメントを参照してください。

コンピューティング先として Azure Databricks をアタッチするには、次の情報を指定します。

* __Databricks コンピューティング名__:このコンピューティング リソースに割り当てる名前。
* __Databricks ワークスペース名__:Azure Databricks ワークスペースの名前。
* __Databricks アクセス トークン__:Azure Databricks に対する認証に使用するアクセス トークン。 アクセス トークンを生成するには、[認証](/azure/databricks/dev-tools/api/latest/authentication)に関するドキュメントを参照してください。

次のコードでは、Azure Machine Learning SDK を使用してコンピューティング先として Azure Databricks をアタッチする方法を示します (__Databricks ワークスペースは、AML ワークスペースと同じサブスクリプション内に存在する必要があります__)。

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

詳細な例については、GitHub の[サンプル ノートブック](https://aka.ms/pl-databricks)を参照してください。

> [!WARNING]
> ワークスペースから同じ Azure Databricks に対して複数のアタッチメントを同時に作成することは避けてください。 アタッチを繰り返すたびに、先行する既存のアタッチメントが切断されます。

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics は、Azure クラウド内のビッグ データ分析プラットフォームです。 これは、Azure Machine Learning パイプラインでコンピューティング先として使用できます。

使用する前に、Azure Data Lake Analytics アカウントを作成します。 このリソースを作成するには、「[Azure Data Lake Analytics の使用を開始する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)」を参照してください。

コンピューティング ターゲットとして Data Lake Analytics に接続するには、Azure Machine Learning SDK を使用し、次の情報を提供する必要があります。

* __コンピューティング名__:このコンピューティング リソースに割り当てる名前。
* __リソース グループ__:Data Lake Analytics アカウントを含むリソース グループ。
* __アカウント名__:Data Lake Analytics アカウント名です。

次のコードは、コンピューティング ターゲットとして Data Lake Analytics に接続する方法を示しています。

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

詳細な例については、GitHub の[サンプル ノートブック](https://aka.ms/pl-adla)を参照してください。

> [!WARNING]
> ワークスペースから同じ ADLA に対して複数のアタッチメントを同時に作成することは避けてください。 アタッチを繰り返すたびに、先行する既存のアタッチメントが切断されます。

> [!TIP]
> Azure Machine Learning パイプラインは、Data Lake Analytics アカウントの既定のデータ ストアに格納されたデータのみ使用できます。 使用する必要があるデータが既定以外のストアにある場合は、[`DataTransferStep`](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep) を使用して、トレーニングの前にデータをコピーできます。

## <a name="azure-container-instance"></a><a id="aci"></a>Azure Container Instances

Azure Container Instances (ACI) は、モデルのデプロイ時に動的に作成されます。 他の方法では、ACI を作成したり、ワークスペースにアタッチしたりすることはできません。 詳細については、[Azure Container Instances へのモデルのデプロイ](how-to-deploy-azure-container-instance.md)に関するページを参照してください。

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Azure Kubernetes Service (AKS) を Azure Machine Learning と組み合わせて使用すると、さまざまな構成オプションが使用できます。 詳細については、[Azure Kubernetes Service を作成してアタッチする方法](how-to-create-attach-kubernetes.md)に関するページを参照してください。


## <a name="notebook-examples"></a>ノートブックの例

さまざまなコンピューティング先を使用したトレーニングの例については、以下のノートブックをご覧ください。
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>次のステップ

* コンピューティング リソースを使用して、[トレーニング実行を構成して送信します](how-to-set-up-training-targets.md)。
* [チュートリアル:モデルのトレーニング](tutorial-train-models-with-aml.md)に関する記事では、マネージド コンピューティング先を使用してモデルをトレーニングします。
* より優れたモデルを構築するために、[ハイパーパラメーター](how-to-tune-hyperparameters.md)を効率的に調整する方法を学習します。
* モデルのトレーニングが済んだら、[モデルをデプロイする方法と場所](how-to-deploy-and-where.md)を確認します。
* [Azure Machine Learning と Azure Virtual Network を使用する](./how-to-network-security-overview.md)