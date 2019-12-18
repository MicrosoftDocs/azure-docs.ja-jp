---
title: ビッグ データでバッチ予測を実行する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning のバッチ推論を使用して、大量のデータで非同期的に推論を取得する方法について説明します。 すぐに使用できる並列処理機能を利用できるバッチ推論は、ビッグデータ ユースケースに対する高スループットのファイヤー アンド フォーゲット推論に対して最適化されています。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: vaidyas
author: vaidya-s
ms.date: 11/04/2019
ms.custom: Ignite2019
ms.openlocfilehash: 207e8def168227cb419d25c8e98aa15c09c72b2c
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851606"
---
# <a name="run-batch-inference-on-large-amounts-of-data-by-using-azure-machine-learning"></a>Azure Machine Learning を使用して大規模なデータでバッチ推論を実行する
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning を使用して、大量のデータで非同期的および並列的に推論を取得する方法について説明します。 ここで説明するバッチ推論機能は、パブリック プレビュー段階にあります。 この機能では、パフォーマンスおよびスループットに優れた方法で推論が生成され、データが処理されます。 また、すぐに使用できる非同期機能が用意されています。

バッチ推論では、テラバイト規模の運用データで、マシンの大規模クラスターに簡単にオフライン推論をスケーリングできるため、生産性が向上し、コストが最適化されます。

この記事では、次のタスクについて説明します。

> * リモート コンピューティング リソースを作成する。
> * カスタム推論スクリプトを記述する。
> * [機械学習パイプライン](concept-ml-pipelines.md)を作成し、[MNIST](https://publicdataset.azurewebsites.net/dataDetail/mnist/) データセットに基づいて事前トレーニング済みイメージ分類モデルを登録する。 
> * そのモデルを使用して、Azure Blob Storage アカウントで使用できるサンプル イメージに対するバッチ推論を実行する。 

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) をお試しください。

* ガイド付きクイックスタートの場合は、[セットアップのチュートリアル](tutorial-1st-experiment-sdk-setup.md)を完了します (Azure Machine Learning ワークスペースまたはノートブック仮想マシンがまだない場合)。 

* 独自の環境と依存関係を管理するには、独自の環境の構成に関する[攻略ガイド](how-to-configure-environment.md)をご覧ください。 ご自身の環境で `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-contrib-pipeline-steps` を実行して、必要な依存関係をダウンロードします。

## <a name="set-up-machine-learning-resources"></a>機械学習リソースをセットアップする

次の操作により、バッチ推論パイプラインの実行に必要なリソースが設定されます。

- 推論するイメージが含まれる BLOB コンテナーを指すデータストアを作成します。
- データ参照を、バッチ推論パイプラインのステップの入出力として設定します。
- バッチ推論ステップが実行されるようにコンピューティング クラスターを設定します。

### <a name="create-a-datastore-with-sample-images"></a>サンプル イメージでデータストアを作成する

`pipelinedata` という名前のアカウントで、パブリック BLOB コンテナー `sampledata` から MNIST 評価セットを取得します。 このコンテナーを指す `mnist_datastore` という名前のデータストアを作成します。 `register_azure_blob_container` への次の呼び出しで、`overwrite` フラグを `True` に設定すると、以前にその名前で作成されたデータストアが上書きされます。 

お使いの BLOB コンテナーを指すようにこのステップを変更するには、`datastore_name`、`container_name`、および `account_name` に独自の値を指定します。

```python
from azureml.core import Datastore
from azureml.core import Workspace

# Load workspace authorization details from config.json
ws = Workspace.from_config()

mnist_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="mnist_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata",
                      overwrite=True)
```

次に、ワークスペースの既定のデータストアを、出力データストアとして指定します。 それを、推論の出力に使用します。

ワークスペースを作成すると、[Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)  ストレージと [Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)  が既定でワークスペースに関連付けられます。 Azure Files がワークスペースの既定のデータストアですが、Blob Storage をデータストアとして使用することもできます。 詳細については、[Azure Storage のオプション](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)に関する記事を参照してください。

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-inputs-and-outputs"></a>データの入出力を構成する

ここで、次のようなデータ入出力を構成する必要があります。

- 入力画像が格納されるディレクトリ。
- 事前トレーニング済みモデルが格納されるディレクトリ。
- ラベルを含むディレクトリ。
- 出力用のディレクトリ。

`Dataset` は Azure Machine Learning でデータを探索、変換、および管理するためのクラスです。 このクラスには `TabularDataset` と `FileDataset` の 2 つの型があります。 この例では、`FileDataset` を、バッチ推論パイプラインのステップへの入力として使用します。 

> [!NOTE] 
> 現在、バッチ推論の `FileDataset` サポートは、Azure Blob Storage に制限されています。 

カスタム推論スクリプトで、他のデータセットを参照することもできます。 たとえば、これを使って、イメージにラベルを付けるためにスクリプトでラベルにアクセスするには、`Dataset.register` と `Dataset.get_by_name`を使用します。

Azure Machine Learning データセットの詳細については、[データセットの作成とアクセス (プレビュー)](https://docs.microsoft.com/azure/machine-learning/service/how-to-create-register-datasets) に関するページをご覧ください。

パイプラインのステップ間での中間データの転送には、`PipelineData` オブジェクトが使用されます。 この例では、これを推論の出力に使用します。

```python
from azureml.core.dataset import Dataset

mnist_ds_name = 'mnist_sample_data'

path_on_datastore = mnist_blob.path('mnist/')
input_mnist_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)
registered_mnist_ds = input_mnist_ds.register(ws, mnist_ds_name, create_new_version=True)
named_mnist_ds = registered_mnist_ds.as_named_input(mnist_ds_name)

output_dir = PipelineData(name="inferences", 
                          datastore=def_data_store, 
                          output_path_on_compute="mnist/results")
```

### <a name="set-up-a-compute-target"></a>コンピューティング ターゲットを設定する

Azure Machine Learning での "*コンピューティング*" (または "*コンピューティング先*") とは、機械学習パイプラインで計算ステップを実行するマシンまたはクラスターのことです。 次のコードを実行して、CPU に基づいた [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) ターゲットを作成します。

```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

## <a name="prepare-the-model"></a>モデルを準備する

[事前トレーニング済みイメージ分類モデルをダウンロード](https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz)し、`models` ディレクトリに抽出します。

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url="https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

次に、モデルをお使いのワークスペースに登録して、ご自身のリモート コンピューティング リソースで使用できるようにします。

```python
from azureml.core.model import Model

# Register the downloaded model 
model = Model.register(model_path="models/",
                       model_name="mnist",
                       tags={'pretrained': "mnist"},
                       description="Mnist trained tensorflow model",
                       workspace=ws)
```

## <a name="write-your-inference-script"></a>お使いの推論スクリプトを記述する

>[!Warning]
>次のコードは、[サンプル ノートブック](https://aka.ms/batch-inference-notebooks)で使用されるサンプルに過ぎません。 ご自身のシナリオに合わせて、独自のスクリプトを作成する必要があります。

スクリプトには 2 つの関数が "*必ず必要*" です。
- `init()`:この関数は、後で推論するためのコストのかかる準備、または一般的な準備を行うときに使用します。 たとえば、これを使って、モデルをグローバル オブジェクトに読み込みます。
-  `run(mini_batch)`:この関数は、`mini_batch` インスタンスごとに実行されます。
    -  `mini_batch`:バッチ推論は run メソッドを呼び出して、そのメソッドに、リストまたは Pandas データフレームのいずれかを引数として渡します。 min_batch のエントリはそれぞれ、ファイル パス (入力が FileDataset の場合) または Pandas データフレーム (入力が TabularDataset の場合) になります。
    -  `response`: run() メソッドは、Pandas データフレームまたは配列を返します。 append_row output_action の場合、これらの返される要素は、共通の出力ファイルに追加されます。 summary_only の場合、要素のコンテンツは無視されます。 すべての出力アクションについて、返される出力要素はそれぞれ、入力ミニバッチ内で成功した 1 つの入力要素の推論を示します。 ユーザーは、入力を推論にマップするのに十分なデータが、推論の結果に含まれていることを確認する必要があります。 推論の出力は出力ファイルに書き込まれますが、必ずしも順序どおりであるとは限りません。ユーザーは、出力で何らかのキーを使って、それを入力にマップする必要があります。

```python
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://aka.ms/batch-inference-notebooks)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

### <a name="how-to-access-other-files-in-init-or-run-functions"></a>`init()` 関数または `run()` 関数で他のファイルにアクセスする方法

推論スクリプトと同じディレクトリに他のファイルまたはフォルダーがある場合、現在の作業ディレクトリを特定することでそれらを参照することができます。

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

## <a name="build-and-run-the-batch-inference-pipeline"></a>バッチ推論パイプラインを構築して実行する

パイプラインの構築に必要なものはすべて揃いました。

### <a name="prepare-the-run-environment"></a>実行環境を準備する

最初に、お使いのスクリプトに対する依存関係を指定します。 このオブジェクトは後で、パイプラインのステップを作成するときに使用します。

```python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

batch_conda_deps = CondaDependencies.create(pip_packages=["tensorflow==1.13.1", "pillow"])

batch_env = Environment(name="batch_environment")
batch_env.python.conda_dependencies = batch_conda_deps
batch_env.docker.enabled = True
batch_env.docker.base_image = DEFAULT_GPU_IMAGE
batch_env.spark.precache_packages = False
```

### <a name="specify-the-parameters-for-your-batch-inference-pipeline-step"></a>バッチ推論パイプラインのステップのパラメーターを指定する

`ParallelRunConfig` は、Azure Machine Learning パイプライン内にある、新しく導入されたバッチ推論の `ParallelRunStep` インスタンスの主要な構成です。 これは、お使いのスクリプトをラップし、必要なパラメーターを構成するときに使用します。たとえば、次のようなパラメーターです。
- `entry_script`:複数のノードで並列で実行されるローカル ファイル パスとしてのユーザー スクリプト。 `source_directly` が存在する場合は、相対パスを使用します。 それ以外の場合は、マシンでアクセス可能な任意のパスを使用します。
- `mini_batch_size`:1 つの `run()` 呼び出しに渡されたミニバッチのサイズ (省略可能。既定値は `1` です)。
    - `FileDataset` の場合、これはファイル数を示し、最小値は `1` です。 複数のファイルを 1 つのミニバッチに結合できます。
    - `TabularDataset` の場合は、データのサイズです。 サンプル値は、`1024`、`1024KB`、`10MB`、および `1GB` です。 推奨値は `1MB` です。 `TabularDataset` のミニバッチは、ファイル境界を超えません。 たとえば、さまざまなサイズの .csv ファイルがある場合、ファイルの最小サイズは 100 KB で、最大サイズは 10 MB です。 `mini_batch_size = 1MB` を設定すると、1 MB より小さいファイルは 1 つのミニバッチとして処理されます。 1 MB を超えるファイルは、複数のミニバッチに分割されます。
- `error_threshold`:処理中に無視する必要のあるエラーの数。`TabularDataset` の場合はレコード エラー数、`FileDataset` の場合はファイル エラー数を示します。 入力全体に対するエラーの数がこの値を超えると、ジョブは停止します。 エラーのしきい値は入力全体を対象としています。`run()` メソッドに送信された個々のミニバッチを対象にしているものではありません。 範囲は `[-1, int.max]` です。 `-1` 部分は、処理中にすべてのエラーを無視することを示します。
- `output_action`:次のいずれかの値が、出力がどのように編成されるかを示しています。
    - `summary_only`:ユーザー スクリプトによって出力が格納されます。 `ParallelRunStep` では、エラーしきい値の計算にのみ出力が使用されます。
    - `append_row`:すべての入力ファイルについて、出力フォルダーにファイルが 1 つだけ作成され、行で区切られたすべての出力が追加されます。 ファイル名は parallel_run_step.txt になります。
- `source_directory`:コンピューティング ターゲットで実行されるすべてのファイルを含むフォルダーへのパス (省略可能)。
- `compute_target`:`AmlCompute` だけがサポートされています。
- `node_count`:ユーザー スクリプトの実行に使用されるコンピューティング ノードの数。
- `process_count_per_node`:ノードあたりのプロセスの数。
- `environment`:Python 環境定義。 既存の Python 環境が使用されるように、または実験用の一時的な環境が設定されるように構成できます。 定義で、必要なアプリケーションの依存関係を設定することもできます (省略可能)。
- `logging_level`:ログの詳細。 値は詳細度が低い順に `WARNING`、`INFO`、`DEBUG` です。 既定値は `INFO` です (省略可能)。
- `run_invocation_timeout`:`run()` メソッド呼び出しのタイムアウト (秒単位)。 既定値は `60` です。

```python
from azureml.contrib.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    source_directory=scripts_folder,
    entry_script="digit_identification.py",
    mini_batch_size="5",
    error_threshold=10,
    output_action="append_row",
    environment=batch_env,
    compute_target=compute_target,
    node_count=4)
```

### <a name="create-the-pipeline-step"></a>パイプラインのステップを作成する

スクリプト、環境構成、およびパラメーターを使用して、パイプラインのステップを作成します。 スクリプトの実行の対象としてワークスペースに関連付けたコンピューティング ターゲットを指定します。 `ParallelRunStep` を使用して、バッチ推論パイプラインのステップを作成します。このステップでは、次のすべてのパラメーターが使用されます。
- `name`:ステップの名前。3 文字以上 32 文字以内で、一意の名前にする必要があります。また、正規表現 ^\[a-z\]([-a-z0-9]*[a-z0-9])?$ を使用できます。
- `models`:Azure Machine Learning モデル レジストリに既に登録されている 0 個以上のモデル名。
- `parallel_run_config`:`ParallelRunConfig` オブジェクト (前述にて定義)。
- `inputs`:1 つ以上の single 型 のAzure Machine Learning データセット。
- `output`:`PipelineData` オブジェクト (出力ディレクトリに対応)。
- `arguments`:ユーザー スクリプトに渡された引数の一覧 (省略可能)。
- `allow_reuse`:同じ設定/入力で実行されたときに、ステップで前の結果を再利用するかどうか。 このパラメーターが `False` の場合、パイプラインの実行中、このステップに対して必ず新しい実行が生成されます (省略可能。既定値は `True` です)。

```python
from azureml.contrib.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="batch-mnist",
    models=[model],
    parallel_run_config=parallel_run_config,
    inputs=[named_mnist_ds],
    output=output_dir,
    arguments=[],
    allow_reuse=True
)
```

### <a name="run-the-pipeline"></a>パイプラインを実行する

次に、パイプラインを実行します。 まず、ご自身のワークスペース参照、および作成したパイプラインのステップを使用して、`Pipeline` オブジェクトを作成します。 `steps` パラメーターは、ステップの配列です。 この場合、バッチ スコアリング用のステップは 1 つだけです。 複数のステップが含まれたパイプラインを作成する場合は、この配列内にステップを順に配置します。

次に、`Experiment.submit()` 関数を使用して、実行するパイプラインを送信します。

```python
from azureml.pipeline.core import Pipeline
from azureml.core.experiment import Experiment

pipeline = Pipeline(workspace=ws, steps=[parallelrun_step])
pipeline_run = Experiment(ws, 'digit_identification').submit(pipeline)
```

## <a name="monitor-the-batch-inference-job"></a>バッチ推論ジョブを監視する

バッチ推論ジョブは完了するまでに時間がかかることがあります。 この例では、Jupyter ウィジェットを使用して、進行状況を監視しています。 以下を使用して、ジョブの進行状況を管理することもできます。

* Azure Machine Learning Studio。 
* [`PipelineRun`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.run.pipelinerun?view=azure-ml-py) オブジェクトからのコンソール出力。

```python
from azureml.widgets import RunDetails
RunDetails(pipeline_run).show()

pipeline_run.wait_for_completion(show_output=True)
```

## <a name="next-steps"></a>次の手順

エンド ツー エンドで動作するこのプロセスを確認するには、[バッチ推論のノートブック](https://aka.ms/batch-inference-notebooks)をお試しください。 

ParallelRunStep のデバッグとトラブルシューティングのガイダンスについては、[攻略ガイド](how-to-debug-batch-predictions.md)を参照してください。

パイプラインのデバッグとトラブルシューティングのガイダンスについては、[攻略ガイド](how-to-debug-pipelines.md)をご覧ください。

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

