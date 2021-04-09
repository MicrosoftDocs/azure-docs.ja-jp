---
title: 機械学習パイプラインで Apache Spark を使用する (プレビュー)
titleSuffix: Azure Machine Learning
description: データ操作に Apache Spark を使用するには、Azure Synapse Analytics ワークスペースを Azure 機械学習パイプラインにリンクします。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: how-to, synapse-azureml
ms.openlocfilehash: 1dc4e0b70b0d39d01bada26992eb2213c1e855c5
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2021
ms.locfileid: "102455061"
---
# <a name="how-to-use-apache-spark-powered-by-azure-synapse-analytics-in-your-machine-learning-pipeline-preview"></a>機械学習パイプライン内で (Azure Synapse Analytics で実行される) Apache Spark を使用する方法 (プレビュー)

この記事では、Azure Synapse Analytics によって実行される Apache Spark プールを、Azure Machine Learning パイプラインのデータ準備ステップのコンピューティング先として使用する方法について説明します。 データの準備やトレーニングなどの特定の手順に適したコンピューティング リソースを 1 つのパイプラインで使用する方法について説明します。 Spark ステップ用のデータの準備方法と、次の手順に渡す方法について説明します。 

## <a name="prerequisites"></a>前提条件

* すべてのパイプライン リソースを保持するための [Azure Machine Learning ワークスペース](how-to-manage-workspace.md)を作成します。

* [開発環境を構成](how-to-configure-environment.md)して Azure Machine Learning SDK をインストールするか、SDK が既にインストールされている [Azure Machine Learning コンピューティング インスタンス](concept-compute-instance.md)を使用します。

* Azure Synapse Analytics ワークスペースと Apache Spark プールを作成します (「[クイックスタート: Synapse Studio を使用してサーバーレス Apache Spark プールを作成する](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)」を参照してください)。 

## <a name="link-your-azure-machine-learning-workspace-and-azure-synapse-analytics-workspace"></a>Azure Machine Learning ワークスペースと Azure Synapse Analytics ワークスペースをリンクする 

Apache Spark プールは、Azure Synapse Analytics ワークスペースで作成して管理します。 Apache Spark プールを Azure Machine Learning ワークスペースと統合するには、[Azure Synapse Analytics ワークスペースにリンク](how-to-link-synapse-ml-workspaces.md)する必要があります。 

**[リンクされたサービス]** ページを使用して、Azure Machine Learning スタジオ UI 経由で Apache Spark プールをアタッチできます。 この操作は **[コンピューティング]** ページで **[コンピューティングをアタッチする]** オプションを使用して行うこともできます。

また、SDK (下記を参照) または ARM テンプレート (次の「[ARM テンプレートの例](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)」を参照) を使用して Apache Spark プールをアタッチすることもできます。 

コマンド ラインを使用して、ARM テンプレートに従い、リンクされたサービスを追加し、次のコードを使用して Apache Spark プールをアタッチできます。

```bash
az deployment group create --name --resource-group <rg_name> --template-file "azuredeploy.json" --parameters @"azuredeploy.parameters.json"
```

> [!Important]
> Azure Synapse Analytics ワークスペースに正常にリンクするには、Azure Synapse Analytics ワークスペース リソースに所有者ロールがある必要があります。 Azure portal でご自身のアクセス権を確認してください。
> リンクされたサービスには、ユーザーによって作成されるときにシステム割り当て ID (SAI) が付与されます。 このリンク サービス SAI に Synapse Studio からの "Synapse Apache Spark 管理者" ロールを割り当てて、これが Spark ジョブを送信できるようにする必要があります (「[Synapse Studio で Synapse RBAC ロールの割り当てを管理する方法](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md)」を参照してください)。 また、Azure Machine Learning ワークスペースのユーザーに、リソース管理の Azure portal からの "共同作成者" ロールを付与する必要があります。

## <a name="create-or-retrieve-the-link-between-your-azure-synapse-analytics-workspace-and-your-azure-machine-learning-workspace"></a>Azure Synapse Analytics ワークスペースと Azure Machine Learning ワークスペースの間のリンクを作成または取得する

次のようなコードを使用して、ワークスペース内のリンクされたサービスを取得できます。

```python
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

ws = Workspace.from_config()

for service in LinkedService.list(ws) : 
    print(f"Service: {service}")

# Retrieve a known linked service
linked_service = LinkedService.get(ws, 'synapselink1')
```

まず、`Workspace.from_config()` により、`config.json` 内の構成を使用して Azure Machine Learning ワークスペースにアクセスします (「[チュートリアル: 個人の開発環境で Azure Machine Learning の使用を開始する](tutorial-1st-experiment-sdk-setup-local.md)」を参照してください)。 次に、このコードによって、ワークスペースで使用可能なリンクされたサービスがすべて出力されます。 最後に、`LinkedService.get()` によって、`'synapselink1'` という名前のリンクされたサービスが取得されます。 

## <a name="attach-your-apache-spark-pool-as-a-compute-target-for-azure-machine-learning"></a>Azure Machine Learning のコンピューティング先として Apache Spark プールをアタッチする

Apache Spark プールを使用して機械学習パイプラインのステップを実行するには、次のコードに示すように、パイプライン ステップの `ComputeTarget` としてこれをアタッチする必要があります。

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(
        linked_service = linked_service,
        type="SynapseSpark",
        pool_name="spark01") # This name comes from your Synapse workspace

synapse_compute=ComputeTarget.attach(
        workspace=ws,
        name='link1-spark01',
        attach_configuration=attach_config)

synapse_compute.wait_for_completion()
```

最初のステップは、`SynapseCompute` を構成することです。 `linked_service` 引数は、前の手順で作成または取得した `LinkedService` オブジェクトです。 `type` 引数は `SynapseSpark` である必要があります。 `SynapseCompute.attach_configuration()` の `pool_name` 引数は、Azure Synapse Analytics ワークスペースの既存のプールのそれと一致している必要があります。 Azure Synapse Analytics ワークスペースで Apache Spark プールを作成する方法の詳細については、「[クイックスタート: Synapse Studio を使用してサーバーレス Apache Spark プールを作成する](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)」を参照してください。 `attach_config` の型は `ComputeTargetAttachConfiguration` です。

構成が作成されたら、`Workspace`、`ComputeTargetAttachConfiguration`、および機械学習ワークスペース内のコンピューティングの参照に使用する名前を渡して、機械学習 `ComputeTarget` を作成します。 `ComputeTarget.attach()` の呼び出しは非同期であるため、このサンプルは、呼び出しが完了するまでブロック状態になります。

## <a name="create-a-synapsesparkstep-that-uses-the-linked-apache-spark-pool"></a>リンクされた Apache Spark プールを使用する `SynapseSparkStep` を作成する

データは `DatasetConsumptionConfig` オブジェクトを使用して機械学習パイプラインに送られます。このオブジェクトは表形式のデータまたはファイルのセットを保持できます。 データは多くの場合、ワークスペースのデータストア内にある BLOB ストレージ内のファイルから取得されます。 次のコードは、機械学習パイプラインの入力を作成するための一般的なコードを示しています。

```python
from azureml.core import Dataset

datastore = ws.get_default_datastore()
file_name = 'Titanic.csv'

titanic_tabular_dataset = Dataset.Tabular.from_delimited_files(path=[(datastore, file_name)])
step1_input1 = titanic_tabular_dataset.as_named_input("tabular_input")

# Example only: it wouldn't make sense to duplicate input data, especially one as tabular and the other as files
titanic_file_dataset = Dataset.File.from_files(path=[(datastore, file_name)])
step1_input2 = titanic_file_dataset.as_named_input("file_input").as_hdfs()
```

上記のコードは、ファイル `Titanic.csv` が BLOB ストレージにあることが前提となっています。 このコードでは、ファイルを `TabularDataset` として、および `FileDataset` として読み取る方法を示しています。 このコードはデモ目的でのみ提供されています。なぜなら、テーブルに含まれるリソースと、単なるファイルの両方として、入力を複製したり単一のデータソースを解釈したりすると、ややこしくなるためです。

> [!Important]
> `FileDataset` を入力として使用するには、`azureml-core` のバージョンが `1.20.0` 以上である必要があります。 `Environment` クラスを使用してこれを指定する方法については、以下で説明します。

ステップが完了したら、次のようなコードを使用して出力データを格納することができます。

```python
from azureml.data import HDFSOutputDatasetConfig
step1_output = HDFSOutputDatasetConfig(destination=(datastore,"test")).register_on_complete(name="registered_dataset")
```

この場合、データは `test` という名前のファイルの `datastore` に格納され、機械学習ワークスペース内では `registered_dataset` という名前の `Dataset` として使用できます。

データに加えて、パイプライン ステップには、ステップごとの Python の依存関係がある場合があります。 個々の `SynapseSparkStep` オブジェクトでは、正確な Azure Synapse Apache Spark 構成を指定することもできます。 これを次のコードに示します。これは `azureml-core` パッケージのバージョンが `1.20.0` 以上である必要があることを示します。 (前述のように、`FileDataset` を入力として使用するには、`azureml-core` の要件が必要です。)

```python
from azureml.core.environment import Environment
from azureml.pipeline.steps import SynapseSparkStep

env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core>=1.20.0")

step_1 = SynapseSparkStep(name = 'synapse-spark',
                          file = 'dataprep.py',
                          source_directory="./code", 
                          inputs=[step1_input1, step1_input2],
                          outputs=[step1_output],
                          arguments = ["--tabular_input", step1_input1, 
                                       "--file_input", step1_input2,
                                       "--output_dir", step1_output],
                          compute_target = 'link1-spark01',
                          driver_memory = "7g",
                          driver_cores = 4,
                          executor_memory = "7g",
                          executor_cores = 2,
                          num_executors = 1,
                          environment = env)
```

上記のコードでは、Azure 機械学習パイプラインの 1 つのステップが指定されています。 このステップの `environment` では、特定の `azureml-core` バージョンを指定し、必要に応じて他の conda または pip の依存関係を追加できます。

`SynapseSparkStep` では、ローカル コンピューターからサブディレクトリ `./code` が zip され、アップロードされます。 このディレクトリはコンピューティング サーバー上に再作成され、このステップによって、そのディレクトリからファイル `dataprep.py` が実行されます。 そのステップの `inputs` および `outputs` は、前に説明した `step1_input1`、`step1_input2`、および `step1_output` オブジェクトです。 `dataprep.py` スクリプト内でこれらの値にアクセスする最も簡単な方法は、これらを `arguments` という名前に関連付けることです。

`SynapseSparkStep` コンストラクターに対する次の引数セットにより、Apache Spark が制御されます。 `compute_target` は、以前にコンピューティング先としてアタッチされた `'link1-spark01'` です。 その他のパラメーターでは、使用するメモリとコアが指定されます。

サンプル ノートブックでは、`dataprep.py` に対して次のコードを使用します。

```python
import os
import sys
import azureml.core
from pyspark.sql import SparkSession
from azureml.core import Run, Dataset

print(azureml.core.VERSION)
print(os.environ)

import argparse
parser = argparse.ArgumentParser()
parser.add_argument("--tabular_input")
parser.add_argument("--file_input")
parser.add_argument("--output_dir")
args = parser.parse_args()

# use dataset sdk to read tabular dataset
run_context = Run.get_context()
dataset = Dataset.get_by_id(run_context.experiment.workspace,id=args.tabular_input)
sdf = dataset.to_spark_dataframe()
sdf.show()

# use hdfs path to read file dataset
spark= SparkSession.builder.getOrCreate()
sdf = spark.read.option("header", "true").csv(args.file_input)
sdf.show()

sdf.coalesce(1).write\
.option("header", "true")\
.mode("append")\
.csv(args.output_dir)
```

この "データ準備" スクリプトでは実際のデータ変換が行われませんが、データの取得、spark データフレームへの変換、および基本的な Apache Spark 操作の実行方法が示されます。 次の図に示すように、子実行を開き、 **[出力 + ログ]** タブを選択し、`logs/azureml/driver/stdout` ファイルを開くことによって、Azure Machine Learning Studio で出力を確認できます。

:::image type="content" source="media/how-to-use-synapsesparkstep/synapsesparkstep-stdout.png" alt-text="子実行の stdout タブが表示されている Studio のスクリーンショット":::

## <a name="use-the-synapsesparkstep-in-a-pipeline"></a>パイプラインで `SynapseSparkStep` を使用する

パイプライン内のその他のステップには、それら独自の環境があり、これらは手持ちのタスクに適したさまざまなコンピューティング リソース上で実行される場合があります。 サンプル ノートブックでは、小規模な CPU クラスターで "トレーニング ステップ" が実行されます。

```python
from azureml.core.compute import AmlCompute

cpu_cluster_name = "cpucluster"

if cpu_cluster_name in ws.compute_targets:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
else:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', max_nodes=1)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    print('Allocating new CPU compute cluster')

cpu_cluster.wait_for_completion(show_output=True)

step2_input = step1_output.as_input("step2_input").as_download()

step_2 = PythonScriptStep(script_name="train.py",
                          arguments=[step2_input],
                          inputs=[step2_input],
                          compute_target=cpu_cluster_name,
                          source_directory="./code",
                          allow_reuse=False)
```

上記のコードでは、必要に応じて新しいコンピューティング リソースが作成されます。 その後、`step1_output` の結果がトレーニング ステップの入力に変換されます。 `as_download()` オプションは、データがコンピューティング リソースに移動されるため、アクセスが高速になることを意味します。 データが大きすぎてローカルのコンピューティング ハードドライブに収まりきらない場合は、`as_mount()` オプションを使用して、FUSE ファイル システムを介してデータをストリーミングします。 この 2 番目のステップの `compute_target` は `'cpucluster'` で、データ準備ステップで使用した `'link1-spark01'` リソースではありません。 このステップでは、前のステップで使用した `dataprep.py` の代わりに単純なプログラム `train.py` を使用します。 `train.py` の詳細については、サンプル ノートブックで確認できます。

すべてのステップを定義したら、パイプラインを作成して実行できます。 

```python
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[step_1, step_2])
pipeline_run = pipeline.submit('synapse-pipeline', regenerate_outputs=True)
```

上記のコードでは、Azure Synapse Analytics (`step_1`) で実行される Apache Spark プール上のデータ準備ステップと、トレーニング ステップ (`step_2`) で構成されるパイプラインが作成されます。 Azure では、ステップ間のデータの依存関係を調べることによって実行グラフが計算されます。 この場合、`step2_input` が必ず `step1_output` を必要とするという単純な依存関係のみが存在します。

`pipeline.submit` の呼び出しにより、必要に応じて `synapse-pipeline` という実験が作成され、その中で実行が非同期的に開始されます。 パイプライン内の個々のステップは、このメイン実行の子実行として実行され、Studio の[実験] ページで監視および確認できます。

## <a name="next-steps"></a>次のステップ

* [機械学習パイプラインを発行して追跡する](how-to-deploy-pipelines.md) 
* [Azure Machine Learning の監視](monitor-azure-machine-learning.md)
* [Python の Azure Machine Learning パイプラインで自動 ML を使用する](how-to-use-automlstep-in-pipelines.md)
