---
title: ビッグ データでバッチ予測を実行する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning の ParallelRunStep を使用して、大量のデータで非同期的に推論を取得する方法について説明します。 すぐに使用できる並列処理機能を利用できる ParallelRunStep は、ビッグデータ ユース ケースに対する高スループットのファイヤー アンド フォーゲット推論に最適化されています。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: jmartens, larryfr
ms.author: tracych
author: tracychms
ms.date: 08/14/2020
ms.custom: Build2020, devx-track-python
ms.openlocfilehash: dddb332498f41437eba77d75c38218c58b8c8379
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88507116"
---
# <a name="run-batch-inference-on-large-amounts-of-data-by-using-azure-machine-learning"></a>Azure Machine Learning を使用して大規模なデータでバッチ推論を実行する
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、Azure Machine Learning モデルを並列で実行し、大量のデータを迅速に評価する方法について説明します。 

大規模なデータセットの推論や複雑なモデルを使用した推論には、時間がかかることがあります。 `ParallelRunStep` クラスを使用すると、処理を並列で実行できるため、全体的な結果が得られるまでの時間を短縮できる可能性があります。 単一の評価を非常に高速に実行できるとしても、多くのシナリオ (オブジェクト検出、ビデオ処理、自然言語処理など) では多数の評価が実行されます。 

`ParallelRunStep` を使用すると、大量のマシン クラスターにバッチ推論を簡単にスケーリングできます。 このようなクラスターではテラバイト単位の構造化または非構造化データを処理できるため、生産性の向上とコストの最適化を実現できます。

この記事では、次のタスクについて説明します。

> 1. 機械学習リソースを設定する。
> 1. バッチ推論のデータの入出力を構成する。
> 1. [MNIST](https://publicdataset.azurewebsites.net/dataDetail/mnist/) データセットに基づいて事前トレーニング済みイメージ分類モデルを準備する。 
> 1.  ご自身の推論スクリプトを記述する。
> 1. ParallelRunStep を含む[機械学習パイプライン](concept-ml-pipelines.md)を作成し、MNIST テスト イメージに対してバッチ推論を実行する。 
> 1. 新しいデータ入力とパラメーターを使用して、バッチ推論の実行を再送信する。 
> 1. 結果を表示します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) をお試しください。

* ガイド付きクイックスタートの場合は、[設定のチュートリアル](tutorial-1st-experiment-sdk-setup.md)を完了します (Azure Machine Learning ワークスペースがまだない場合)。 

* 実際の環境と依存関係を管理するには、お使いのローカル環境の構成に関する[攻略ガイド](how-to-configure-environment.md)をご覧ください。

## <a name="set-up-machine-learning-resources"></a>機械学習リソースをセットアップする

次の操作により、バッチ推論パイプラインの実行に必要な機械学習リソースが設定されます。

- ワークスペースに接続する。
- コンピューティング リソースの作成または既存のもののアタッチを行う。

### <a name="configure-workspace"></a>ワークスペースの構成

既存のワークスペースからワークスペース オブジェクトを作成します。 `Workspace.from_config()` により、config.json ファイルが読み取られ、詳細情報が ws という名前のオブジェクトに読み込まれます。

```python
from azureml.core import Workspace

ws = Workspace.from_config()
```

> [!IMPORTANT]
> このコード スニペットでは、ワークスペースの構成が現在のディレクトリまたはその親に保存されていることを想定しています。 ワークスペースの作成方法について詳しくは、[Azure Machine Learning ワークスペースを作成し、管理する](how-to-manage-workspace.md)方法に関するページを参照してください。 構成をファイルに保存する方法について詳しくは、「[ワークスペース構成ファイルを作成する](how-to-configure-environment.md#workspace)」を参照してください。

### <a name="create-a-compute-target"></a>コンピューティング ターゲットを作成する

Azure Machine Learning での "*コンピューティング*" (または "*コンピューティング先*") とは、機械学習パイプラインで計算ステップを実行するマシンまたはクラスターのことです。 次のコードを実行して、CPU に基づいた [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) ターゲットを作成します。

```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpucluster")
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

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

## <a name="configure-inputs-and-output"></a>入出力を構成する

### <a name="create-a-datastore-with-sample-images"></a>サンプル イメージでデータストアを作成する

`pipelinedata` という名前のアカウントで、パブリック BLOB コンテナー `sampledata` から MNIST 評価セットを取得します。 このコンテナーを指す `mnist_datastore` という名前のデータストアを作成します。 `register_azure_blob_container` への次の呼び出しで、`overwrite` フラグを `True` に設定すると、以前にその名前で作成されたデータストアが上書きされます。 

お使いの BLOB コンテナーを指すようにこのステップを変更するには、`datastore_name`、`container_name`、および `account_name` に独自の値を指定します。

```python
from azureml.core import Datastore
from azureml.core import Workspace

mnist_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="mnist_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata",
                      overwrite=True)
```

次に、ワークスペースの既定のデータストアを、出力データストアとして指定します。 それを、推論の出力に使用します。

ワークスペースを作成すると、[Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)  ストレージと [Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)  が既定でワークスペースに関連付けられます。 Azure Files がワークスペースの既定のデータストアですが、BLOB ストレージをデータストアとして使用することもできます。 詳細については、[Azure Storage のオプション](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)に関する記事を参照してください。

```python
def_data_store = ws.get_default_datastore()
```

### <a name="create-the-data-inputs"></a>データ入力を作成する

バッチ推論の入力は、並列処理のためにパーティション分割するデータです。 バッチ推論パイプラインは、[`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) を通じてデータ入力を受け入れます。

`Dataset` は Azure Machine Learning でデータを探索、変換、および管理するためのものです。 [`TabularDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) と [`FileDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py) の 2 種類があります。 この例では、入力として `FileDataset` を使用します。 `FileDataset` により、お使いのコンピューターにファイルをダウンロードしたり、マウントしたりできます。 データセットを作成することにより、データ ソースの場所への参照を作成します。 データセットにサブセット変換を適用した場合は、それらの変換もデータセットに格納されます。 データは既存の場所に残るので、追加のストレージ コストは発生しません。

Azure Machine Learning データセットの詳細については、[データセットの作成とアクセス (プレビュー)](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) に関するページをご覧ください。

```python
from azureml.core.dataset import Dataset

path_on_datastore = mnist_blob.path('mnist/')
input_mnist_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)
```

バッチ推論パイプラインの実行時に動的データ入力を使用する場合は、`Dataset` 入力を [`PipelineParameter`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) として定義できます。 バッチ推論パイプラインの実行を再送信するたびに、入力データセットを指定できます。

```python
from azureml.data.dataset_consumption_config import DatasetConsumptionConfig
from azureml.pipeline.core import PipelineParameter

pipeline_param = PipelineParameter(name="mnist_param", default_value=input_mnist_ds)
input_mnist_ds_consumption = DatasetConsumptionConfig("minist_param_config", pipeline_param).as_mount()
```

### <a name="create-the-output"></a>出力を作成する

パイプラインのステップ間での中間データの転送には、[`PipelineData`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) オブジェクトが使用されます。 この例では、これを推論の出力に使用します。

```python
from azureml.pipeline.core import Pipeline, PipelineData

output_dir = PipelineData(name="inferences", datastore=def_data_store)
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

次に、モデルをお使いのワークスペースに登録して、ご自身のコンピューティング リソースで使用できるようにします。

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
- `init()`:この関数は、後で推論するためのコストのかかる準備、または一般的な準備を行うときに使用します。 たとえば、これを使って、モデルをグローバル オブジェクトに読み込みます。 この関数は、プロセスの開始時に 1 回だけ呼び出されます。
-  `run(mini_batch)`:この関数は、`mini_batch` インスタンスごとに実行されます。
    -  `mini_batch`: `ParallelRunStep` は run メソッドを呼び出して、そのメソッドに、リストまたは Pandas `DataFrame` のいずれかを引数として渡します。 mini_batch のエントリはそれぞれ、ファイル パス (入力が `FileDataset` の場合) または Pandas `DataFrame` (入力が `TabularDataset` の場合) になります。
    -  `response`: run() メソッドは、Pandas `DataFrame` または配列を返します。 append_row output_action の場合、これらの返される要素は、共通の出力ファイルに追加されます。 summary_only の場合、要素のコンテンツは無視されます。 すべての出力アクションについて、返される出力要素はそれぞれ、入力ミニバッチ内で成功した 1 つの入力要素の実行を示します。 入力を実行出力結果にマップできるだけの十分なデータが実行結果に含まれていることを確認してください。 実行の出力は出力ファイルに書き込まれますが、順序どおりの書き込みは保証されません。出力でいずれかのキーを使って、入力にマップする必要があります。

```python
%%writefile digit_identification.py
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

推論スクリプトと同じディレクトリに他のファイルまたはフォルダーがある場合、現在の作業ディレクトリを特定することでそれらを参照することができます。

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

## <a name="build-and-run-the-pipeline-containing-parallelrunstep"></a>ParallelRunStep を含むパイプラインをビルドして実行する

これで必要となるデータ入力、モデル、出力、ご自身の推論スクリプトがすべて揃いました。 ParallelRunStep を含むバッチ推論パイプラインをビルドしましょう。

### <a name="prepare-the-environment"></a>環境の準備

最初に、お使いのスクリプトに対する依存関係を指定します。 これにより、pip パッケージをインストールするだけでなく、環境を構成することもできます。

pip パッケージの一覧には、必ず **azureml-core** と **azureml-dataset-runtime[pandas, fuse]** を含めてください。 カスタム Docker イメージ (user_managed_dependencies=True) を使用する場合は、Conda がインストールされている必要もあります。

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

batch_conda_deps = CondaDependencies.create(pip_packages=["tensorflow==1.15.2", "pillow", 
                                                          "azureml-core", "azureml-dataset-runtime[pandas, fuse]"])

batch_env = Environment(name="batch_environment")
batch_env.python.conda_dependencies = batch_conda_deps
batch_env.docker.enabled = True
batch_env.docker.base_image = DEFAULT_GPU_IMAGE
```

### <a name="specify-the-parameters-using-parallelrunconfig"></a>ParallelRunConfig を使用してパラメーターを指定する

`ParallelRunConfig` は、Azure Machine Learning パイプライン内にある `ParallelRunStep` インスタンスの主要な構成です。 これは、お使いのスクリプトをラップし、必要なパラメーターを構成するときに使用します。たとえば、次のようなエントリです。
- `entry_script`:複数のノードで並列で実行されるローカル ファイル パスとしてのユーザー スクリプト。 `source_directory` が存在する場合は、相対パスを使用します。 それ以外の場合は、マシンでアクセス可能な任意のパスを使用します。
- `mini_batch_size`:1 つの `run()` 呼び出しに渡されたミニバッチのサイズ (省略可能。既定値は、`FileDataset` の場合は `10` ファイルで、`TabularDataset` の場合は `1MB` です。)
    - `FileDataset` の場合、これはファイル数を示し、最小値は `1` です。 複数のファイルを 1 つのミニバッチに結合できます。
    - `TabularDataset` の場合は、データのサイズです。 サンプル値は、`1024`、`1024KB`、`10MB`、および `1GB` です。 推奨値は `1MB` です。 `TabularDataset` のミニバッチは、ファイル境界を超えません。 たとえば、さまざまなサイズの .csv ファイルがある場合、ファイルの最小サイズは 100 KB で、最大サイズは 10 MB です。 `mini_batch_size = 1MB` を設定すると、1 MB より小さいファイルは 1 つのミニバッチとして処理されます。 1 MB を超えるファイルは、複数のミニバッチに分割されます。
- `error_threshold`:処理中に無視する必要のあるエラーの数。`TabularDataset` の場合はレコード エラー数、`FileDataset` の場合はファイル エラー数を示します。 入力全体に対するエラーの数がこの値を超えると、ジョブは中止されます。 エラーのしきい値は入力全体を対象としています。`run()` メソッドに送信された個々のミニバッチを対象にしているものではありません。 範囲は `[-1, int.max]` です。 `-1` 部分は、処理中にすべてのエラーを無視することを示します。
- `output_action`:次のいずれかの値が、出力がどのように編成されるかを示しています。
    - `summary_only`:ユーザー スクリプトによって出力が格納されます。 `ParallelRunStep` では、エラーしきい値の計算にのみ出力が使用されます。
    - `append_row`:すべての入力について、出力フォルダーにファイルが 1 つだけ作成され、行で区切られたすべての出力が追加されます。
- `append_row_file_name`:append_row output_action の出力ファイル名をカスタマイズします (省略可能。既定値は `parallel_run_step.txt` です)。
- `source_directory`:コンピューティング ターゲットで実行されるすべてのファイルを含むフォルダーへのパス (省略可能)。
- `compute_target`:サポートされるのは `AmlCompute` のみです。
- `node_count`:ユーザー スクリプトの実行に使用されるコンピューティング ノードの数。
- `process_count_per_node`:ノードあたりのプロセスの数。 ベスト プラクティスとして、ノードあたりの GPU または CPU の数に設定します (省略可能。既定値は `1` です)。
- `environment`:Python 環境定義。 既存の Python 環境が使用されるように、または一時的な環境が設定されるように構成できます。 定義で、必要なアプリケーションの依存関係を設定することもできます (省略可能)。
- `logging_level`:ログの詳細。 値は詳細度が低い順に `WARNING`、`INFO`、`DEBUG` です。 (省略可能。既定値は `INFO` です)
- `run_invocation_timeout`:`run()` メソッド呼び出しのタイムアウト (秒単位)。 (省略可能、既定値は `60` です)
- `run_max_try`:ミニバッチに対する `run()` の最大試行回数。 例外がスローされた場合、`run()` は失敗します。`run_invocation_timeout` に到達した場合は何も返されません (省略可能。既定値は `3` です)。 

`mini_batch_size`、`node_count`、`process_count_per_node`、`logging_level`、`run_invocation_timeout`、`run_max_try` を `PipelineParameter` として指定すると、パイプラインの実行を再送信するときに、パラメーターの値を微調整できます。 この例では、`mini_batch_size` と `Process_count_per_node` に `PipelineParameter` を使用し、後で実行を再送信するときに、これらの値を変更します。 

この例では、前に説明した `digit_identification.py` スクリプトを使用していることを前提としています。 独自のスクリプトを使用する場合は、`source_directory` および `entry_script` パラメーターを適宜変更します。

```python
from azureml.pipeline.core import PipelineParameter
from azureml.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    source_directory='.',
    entry_script="digit_identification.py",
    mini_batch_size=PipelineParameter(name="batch_size_param", default_value="5"),
    error_threshold=10,
    output_action="append_row",
    append_row_file_name="mnist_outputs.txt",
    environment=batch_env,
    compute_target=compute_target,
    process_count_per_node=PipelineParameter(name="process_count_param", default_value=2),
    node_count=2)
```

### <a name="create-the-parallelrunstep"></a>ParallelRunStep を作成する

スクリプト、環境構成、およびパラメーターを使用して、ParallelRunStep を作成します。 お使いの推論スクリプトの実行の対象としてご自身のワークスペースに関連付けたコンピューティング ターゲットを指定します。 `ParallelRunStep` を使用して、バッチ推論パイプラインのステップを作成します。このステップでは、次のすべてのパラメーターが使用されます。
- `name`:ステップの名前。3 文字以上 32 文字以内で、一意の名前にする必要があります。また、正規表現 ^\[a-z\]([-a-z0-9]*[a-z0-9])?$ を使用できます。
- `parallel_run_config`:`ParallelRunConfig` オブジェクト (前述にて定義)。
- `inputs`:並列処理のためにパーティション分割される 1 つ以上の single 型の Azure Machine Learning データセット。
- `side_inputs`:パーティション分割する必要のないサイド入力として使用される 1 つ以上の参照データまたはデータセット。
- `output`:`PipelineData` オブジェクト (出力ディレクトリに対応)。
- `arguments`:ユーザー スクリプトに渡された引数の一覧。 それらを実際のエントリ スクリプト内で取得するには、unknown_args を使用します (省略可能)。
- `allow_reuse`:同じ設定/入力で実行されたときに、ステップで前の結果を再利用するかどうか。 このパラメーターが `False` の場合、パイプラインの実行中、このステップに対して必ず新しい実行が生成されます (省略可能。既定値は `True` です)。

```python
from azureml.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="predict-digits-mnist",
    parallel_run_config=parallel_run_config,
    inputs=[input_mnist_ds_consumption],
    output=output_dir,
    allow_reuse=True
)
```
### <a name="create-and-run-the-pipeline"></a>パイプラインを作成して実行する

次に、パイプラインを実行します。 まず、ご自身のワークスペース参照、および作成したパイプラインのステップを使用して、[`Pipeline`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) オブジェクトを作成します。 `steps` パラメーターは、ステップの配列です。 この場合、バッチ推論用のステップは 1 つだけです。 複数のステップが含まれたパイプラインを作成する場合は、この配列内にステップを順に配置します。

次に、`Experiment.submit()` 関数を使用して、実行するパイプラインを送信します。

```python
from azureml.pipeline.core import Pipeline
from azureml.core.experiment import Experiment

pipeline = Pipeline(workspace=ws, steps=[parallelrun_step])
experiment = Experiment(ws, 'digit_identification')
pipeline_run = experiment.submit(pipeline)
```

## <a name="monitor-the-batch-inference-job"></a>バッチ推論ジョブを監視する

バッチ推論ジョブは完了するまでに時間がかかることがあります。 この例では、Jupyter ウィジェットを使用して、進行状況を監視しています。 以下を使用して、ジョブの進行状況を監視することもできます。

* Azure Machine Learning Studio。 
* [`PipelineRun`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.run.pipelinerun?view=azure-ml-py) オブジェクトからのコンソール出力。

```python
from azureml.widgets import RunDetails
RunDetails(pipeline_run).show()

pipeline_run.wait_for_completion(show_output=True)
```

## <a name="resubmit-a-run-with-new-data-inputs-and-parameters"></a>新しいデータ入力とパラメーターを使用して実行を再送信する

入力と複数の構成を `PipelineParameter` として作成したので、まったく新しいパイプラインを作成しなくても、別のデータセット入力を使用してバッチ推論の実行を再送信し、パラメーターを微調整することができます。 同じデータストアを使用しますが、データ入力として使用するのは 1 つのイメージのみです。

```python
path_on_datastore = mnist_blob.path('mnist/0.png')
single_image_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)

pipeline_run_2 = experiment.submit(pipeline, 
                                   pipeline_parameters={"mnist_param": single_image_ds, 
                                                        "batch_size_param": "1",
                                                        "process_count_param": 1}
)

pipeline_run_2.wait_for_completion(show_output=True)
```
## <a name="view-the-results"></a>結果の確認

上記の実行結果は、`PipelineData` オブジェクトに指定した `DataStore` に出力データとして書き込まれます。この場合、これは*推論*と呼ばれます。 結果は既定の BLOB コンテナーに格納されるので、ストレージ アカウントに移動して Storage Explorer を使用して表示できます。ファイル パスは、azureml-blobstore-*GUID*/azureml/*RunId*/*output_dir* です。

また、このデータをダウンロードして結果を確認することもできます。 サンプル コード (最初の 10 行のみ) を次に示します。

```python
import pandas as pd
import tempfile

batch_run = pipeline_run.find_step_run(parallelrun_step.name)[0]
batch_output = batch_run.get_output_data(output_dir.name)

target_dir = tempfile.mkdtemp()
batch_output.download(local_path=target_dir)
result_file = os.path.join(target_dir, batch_output.path_on_datastore, parallel_run_config.append_row_file_name)

df = pd.read_csv(result_file, delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
print("Prediction has ", df.shape[0], " rows")
df.head(10) 
```

## <a name="next-steps"></a>次のステップ

エンド ツー エンドで動作するこのプロセスを確認するには、[バッチ推論のノートブック](https://aka.ms/batch-inference-notebooks)をお試しください。 

ParallelRunStep のデバッグとトラブルシューティングのガイダンスについては、[攻略ガイド](how-to-debug-parallel-run-step.md)を参照してください。

パイプラインのデバッグとトラブルシューティングのガイダンスについては、[攻略ガイド](how-to-debug-pipelines.md)をご覧ください。

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

