---
title: パイプラインを使用して大規模なデータに対してバッチ予測を実行する
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service を使用して大量のデータで非同期にバッチ予測を行う方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens, garye
ms.author: jordane
author: jpe316
ms.date: 07/12/2019
ms.openlocfilehash: 97b66bff0e6413b7774a054a01a0dc73649c9cce
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68963015"
---
# <a name="run-batch-predictions-on-large-data-sets-with-azure-machine-learning-pipelines"></a>Azure Machine Learning パイプラインを使用して大規模なデータ セットに対してバッチ予測を実行する

この記事では、ML パイプラインと Azure Machine Learning service を使用して、大量のデータに対して予測を非同期的に行う方法を学習します。

バッチ予測 (または、バッチ スコアリング) では、非同期アプリケーションの比類のないスループットで、コスト効率のよい推論が提供されます。 テラバイト規模の運用データで推論を実行するように、バッチ予測パイプラインをスケーリングできます。 バッチ予測は、大規模なデータ コレクションに対する高スループットのファイア アンド フォーゲット予測に対して最適化されています。

>[!TIP]
> お使いのシステムで低待機時間の処理が必要な場合 (1 つのドキュメントまたは小さいドキュメント セットをすばやく処理する場合) に、バッチ予測ではなく[リアルタイム スコアリング](how-to-consume-web-service.md)を使用します。

以下の手順では、[機械学習パイプライン](concept-ml-pipelines.md)を作成して、事前トレーニング済みのコンピューター ビジョン モデル ([Inception-V3](https://arxiv.org/abs/1512.00567)) を登録します。 次に、その事前トレーニング済みモデルを使用して、Azure Blob Storage アカウントで使用可能なイメージに対するバッチ スコアリングを行います。 スコアリングに使用されるこれらのイメージは、[ImageNet](http://image-net.org/) データセットからのラベル付けされていないイメージです。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning service](https://aka.ms/AMLFree) をお試しください。

- Azure Machine Learning SDK をインストールするための開発環境を構成します。 詳しくは、「[Azure Machine Learning のための開発環境を構成する](how-to-configure-environment.md)」をご覧ください。

- すべてのパイプライン リソースを保持する Azure Machine Learning ワークスペースを作成します。 次のコードを使用できます。他のオプションについては、「[ワークスペース構成ファイルを作成する](how-to-configure-environment.md#workspace)」をご覧ください。

  ```python
  from azureml.core import Workspace
  ws = Workspace.create(name = '<workspace-name>',
                        subscription_id = '<subscription-id>',
                        resource_group = '<resource-group>',
                        location = '<workspace_region>',
                        exist_ok = True
                        )
  ```

## <a name="set-up-machine-learning-resources"></a>機械学習リソースをセットアップする

次の手順では、パイプラインの実行に必要なリソースを設定します。

- 事前トレーニング済みモデル、入力ラベル、スコア付け対象のイメージ (これは既に設定されています) が既に存在するデータストアにアクセスします。
- 出力を格納するようにデータストアを設定します。
- 前のデータストア内のデータを指すように  `DataReference`  オブジェクトを構成します。
- パイプラインのステップが実行されるコンピューティング マシンまたはクラスターを設定します。

### <a name="access-the-datastores"></a>データストアにアクセスする

最初に、モデル、ラベル、イメージが格納されているデータストアにアクセスします。

ImageNet 評価セットからのイメージを保持している *pipelinedata* アカウントの *sampledata* という名前のパブリック BLOB コンテナーを使用します。 このパブリック コンテナーのデータストアの名前は *images_datastore* です。 このデータストアを自分のワークスペースに登録します。

```python
from azureml.core import Datastore

account_name = "pipelinedata"
datastore_name = "images_datastore"
container_name = "sampledata"

batchscore_blob = Datastore.register_azure_blob_container(ws,
                                                          datastore_name=datastore_name,
                                                          container_name=container_name,
                                                          account_name=account_name,
                                                          overwrite=True)
```

次に、出力に既定のデータストアを使用するように設定します。

ワークスペースを作成すると、[Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)  ストレージと [Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)  が既定でワークスペースに関連付けられます。 Azure Files がワークスペースの既定のデータストアですが、Blob Storage をデータストアとして使用することもできます。 詳細については、[Azure Storage のオプション](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)に関する記事を参照してください。

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-references"></a>データ参照を構成する

ここでは、パイプラインのステップへの入力として、パイプライン内のデータを参照します。

パイプライン内のデータ ソースは [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) オブジェクトによって表されます。  `DataReference`  オブジェクトでは、データストアに存在するデータまたはデータストアからアクセス可能なデータが指し示されています。 入力イメージに使用されるディレクトリ、事前トレーニング済みモデルが格納されているディレクトリ、ラベル用のディレクトリ、出力ディレクトリのそれぞれに対して、`DataReference`  オブジェクトが必要です。

```python
from azureml.data.data_reference import DataReference

input_images = DataReference(datastore=batchscore_blob,
                             data_reference_name="input_images",
                             path_on_datastore="batchscoring/images",
                             mode="download")

model_dir = DataReference(datastore=batchscore_blob,
                          data_reference_name="input_model",
                          path_on_datastore="batchscoring/models",
                          mode="download")

label_dir = DataReference(datastore=batchscore_blob,
                          data_reference_name="input_labels",
                          path_on_datastore="batchscoring/labels",
                          mode="download")

output_dir = PipelineData(name="scores",
                          datastore=def_data_store,
                          output_path_on_compute="batchscoring/results")
```

### <a name="set-up-compute-target"></a>コンピューティング ターゲットを設定する

Azure Machine Learning での "*コンピューティング*" (または "*コンピューティング先*") とは、機械学習パイプラインで計算ステップを実行するマシンまたはクラスターのことです。 たとえば、`Azure Machine Learning compute` を作成できます。

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget

compute_name = "gpucluster"
compute_min_nodes = 0
compute_max_nodes = 4
vm_size = "STANDARD_NC6"

if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target. just use it. ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(
        vm_size=vm_size,  # NC6 is GPU-enabled
        vm_priority='lowpriority',  # optional
        min_nodes=compute_min_nodes,
        max_nodes=compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws,
                                          compute_name,
                                          provisioning_config)

    compute_target.wait_for_completion(
        show_output=True,
        min_node_count=None,
        timeout_in_minutes=20)
```

## <a name="prepare-the-model"></a>モデルを準備する

事前トレーニング済みモデルを使用するにはその前に、モデルをダウンロードして、ワークスペースに登録する必要があります。

### <a name="download-the-pretrained-model"></a>事前トレーニング済みモデルをダウンロードする

事前トレーニング済みのコンピューター ビジョン モデル (InceptionV3) を、<http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz> からダウンロードします。 その後、`models` サブフォルダーに抽出します。

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url = "http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

### <a name="register-the-model"></a>モデルを登録する

モデルを登録する方法を次に示します。

```python
import shutil
from azureml.core.model import Model

# register downloaded model
model = Model.register(
    model_path="models/inception_v3.ckpt",
    model_name="inception",  # This is the name of the registered model
    tags={'pretrained': "inception"},
    description="Imagenet trained tensorflow inception",
    workspace=ws)
```

## <a name="write-your-scoring-script"></a>スコアリング スクリプトを記述する

>[!Warning]
>次のコードは、[サンプルのノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/pipeline-batch-scoring.ipynb)で使用される [batch_score.py](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/batch_scoring.py) に含まれる内容のサンプルに過ぎません。 ご自身のシナリオに合わせて、独自のスコアリング スクリプトを作成する必要があります。

`batch_score.py` スクリプトでは、入力イメージが *dataset_path* で、事前トレーニング済みモデルが *model_dir* でそれぞれ受け取られて、*results-label.txt* が *output_dir* に出力されます。

```python
# Snippets from a sample scoring script
# Refer to the accompanying batch-scoring Notebook
# https://github.com/Azure/MachineLearningNotebooks/blob/master/pipeline/pipeline-batch-scoring.ipynb
# for the implementation script

# Get labels
def get_class_label_dict(label_file):
  label = []
  proto_as_ascii_lines = tf.gfile.GFile(label_file).readlines()
  for l in proto_as_ascii_lines:
    label.append(l.rstrip())
  return label

class DataIterator:
  # Definition of the DataIterator here

def main(_):
    # Refer to batch-scoring Notebook for implementation.
    label_file_name = os.path.join(args.label_dir, "labels.txt")
    label_dict = get_class_label_dict(label_file_name)
    classes_num = len(label_dict)
    test_feeder = DataIterator(data_dir=args.dataset_path)
    total_size = len(test_feeder.labels)

    # get model from model registry
    model_path = Model.get_model_path(args.model_name)
    with tf.Session() as sess:
        test_images = test_feeder.input_pipeline(batch_size=args.batch_size)
        with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
            input_images = tf.placeholder(tf.float32, [args.batch_size, image_size, image_size, num_channel])
            logits, _ = inception_v3.inception_v3(input_images,
                                                        num_classes=classes_num,
                                                        is_training=False)
            probabilities = tf.argmax(logits, 1)

        sess.run(tf.global_variables_initializer())
        sess.run(tf.local_variables_initializer())
        coord = tf.train.Coordinator()
        threads = tf.train.start_queue_runners(sess=sess, coord=coord)
        saver = tf.train.Saver()
        saver.restore(sess, model_path)
        out_filename = os.path.join(args.output_dir, "result-labels.txt")

        # copy the file to artifacts
        shutil.copy(out_filename, "./outputs/")
```

## <a name="build-and-run-the-batch-scoring-pipeline"></a>バッチ スコアリング パイプラインを構築して実行する

### <a name="prepare-the-run-environment"></a>実行環境を準備する

スクリプトに対する Conda の依存関係を指定します。 このオブジェクトは後で、パイプラインのステップを作成するときに必要です。

```python
from azureml.core.runconfig import DEFAULT_GPU_IMAGE
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

cd = CondaDependencies.create(
    pip_packages=["tensorflow-gpu==1.10.0", "azureml-defaults"])

# Runconfig
amlcompute_run_config = RunConfiguration(conda_dependencies=cd)
amlcompute_run_config.environment.docker.enabled = True
amlcompute_run_config.environment.docker.gpu_support = True
amlcompute_run_config.environment.docker.base_image = DEFAULT_GPU_IMAGE
amlcompute_run_config.environment.spark.precache_packages = False
```

### <a name="specify-the-parameter-for-your-pipeline"></a>パイプラインのパラメーターを指定する

既定の値の  [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py)  オブジェクトを使用して、パイプライン パラメーターを作成します。

```python
from azureml.pipeline.core.graph import PipelineParameter
batch_size_param = PipelineParameter(
    name="param_batch_size",
    default_value=20)
```

### <a name="create-the-pipeline-step"></a>パイプラインのステップを作成する

スクリプト、環境構成、およびパラメーターを使用して、パイプラインのステップを作成します。 スクリプトの実行の対象として既にワークスペースに関連付けたコンピューティング ターゲットを指定します。 [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) を使用してパイプラインのステップを作成します。

```python
from azureml.pipeline.steps import PythonScriptStep
inception_model_name = "inception_v3.ckpt"

batch_score_step = PythonScriptStep(
    name="batch_scoring",
    script_name="batch_score.py",
    arguments=["--dataset_path", input_images,
               "--model_name", "inception",
               "--label_dir", label_dir,
               "--output_dir", output_dir,
               "--batch_size", batch_size_param],
    compute_target=compute_target,
    inputs=[input_images, label_dir],
    outputs=[output_dir],
    runconfig=amlcompute_run_config,
    source_directory=scripts_folder
```

### <a name="run-the-pipeline"></a>パイプラインを実行する

パイプラインを実行し、生成された出力を調べます。 出力には、各入力イメージに対応するスコアが含まれます。

```python
import pandas as pd
from azureml.pipeline.core import Pipeline

# Run the pipeline
pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(
    pipeline, pipeline_params={"param_batch_size": 20})

# Wait for the run to finish (this might take several minutes)
pipeline_run.wait_for_completion(show_output=True)

# Download and review the output
step_run = list(pipeline_run.get_children())[0]
step_run.download_file("./outputs/result-labels.txt")

df = pd.read_csv("result-labels.txt", delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
df.head()
```

## <a name="publish-the-pipeline"></a>パイプラインを発行する

実行の結果に満足したら、後で異なる入力値で実行できるように、パイプラインを発行します。 パイプラインを発行するときに、REST エンドポイントが提供されます。 このエンドポイントは、[PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) を使用して既に組み込まれている一連のパラメーターを使用したパイプラインの呼び出しを受け入れます。

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring",
    description="Batch scoring using Inception v3 model",
    version="1.0")
```

## <a name="rerun-the-pipeline-by-using-the-rest-endpoint"></a>REST エンドポイントを使用してパイプラインを再実行する

パイプラインを実行するには、「[AzureCliAuthentication class (AzureCliAuthentication クラス)](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py)」で説明されているように、Azure Active Directory 認証ヘッダー トークンが必要です。

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.pipeline.core import PublishedPipeline

rest_endpoint = published_pipeline.endpoint
# specify batch size when running the pipeline
response = requests.post(rest_endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})

# Monitor the run
published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)

RunDetails(published_pipeline_run).show()
```

## <a name="next-steps"></a>次の手順

エンドツーエンドでこの動作を確認するには、[GitHub](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines) のバッチ スコアリング ノートブックを試すか、[Azure アーキテクチャ センター](/azure/architecture/reference-architectures/ai/batch-scoring-python)にアクセスしてサンプル ソリューション アーキテクチャを確認してください。
