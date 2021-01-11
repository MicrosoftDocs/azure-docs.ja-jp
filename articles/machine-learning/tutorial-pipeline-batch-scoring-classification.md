---
title: チュートリアル:バッチ スコアリング用の ML パイプライン
titleSuffix: Azure Machine Learning
description: このチュートリアルでは、画像分類モデルでバッチ スコアリングを実行するための機械学習パイプラインを作成します。 Azure Machine Learning を使用すると、インフラストラクチャと自動化ではなく、機械学習に専念できます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: lobrien
ms.author: laobri
ms.reviewer: laobri
ms.date: 03/11/2020
ms.custom: contperfq4, devx-track-python
ms.openlocfilehash: 600b19ffac61f8f7c7336f114c6b52c6bc88b5ad
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2020
ms.locfileid: "89489512"
---
# <a name="tutorial-build-an-azure-machine-learning-pipeline-for-batch-scoring"></a>チュートリアル:バッチ スコアリング用の Azure Machine Learning パイプラインを作成する

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この高度なチュートリアルでは、Azure Machine Learning でパイプラインを作成してバッチ スコアリング ジョブを実行する方法について説明します。 機械学習パイプラインは、速度、移植性、再利用によってワークフローを最適化します。これにより、ユーザーはインフラストラクチャや自動化にではなく、機械学習に専念することができます。 パイプラインを作成して発行したら、REST エンドポイントを構成します。それを使用して、任意のプラットフォームで任意の HTTP ライブラリからパイプラインをトリガーできます。 

この例では、TensorFlow で実装された事前トレーニング済みの [Inception-V3](https://arxiv.org/abs/1512.00567) 畳み込みニューラル ネットワーク モデルを使用して、ラベル付けされていない画像を分類します。 [機械学習パイプラインについての詳細情報をご覧ください](concept-ml-pipelines.md)。

このチュートリアルでは、次のタスクを実行します。

> [!div class="checklist"]
> * ワークスペースの構成 
> * サンプル データをダウンロードして保存する
> * データを取り込んで出力するデータセット オブジェクトを作成する
> * モデルをダウンロードして準備し、ワークスペースに登録する
> * コンピューティング先をプロビジョニングし、スコアリング スクリプトを作成する
> * 非同期バッチ スコアリングに `ParallelRunStep` クラスを使用する
> * パイプラインを作成、実行、発行する
> * パイプラインの REST エンドポイントを有効にする

Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。

## <a name="prerequisites"></a>前提条件

* Azure Machine Learning ワークスペースまたはノートブック仮想マシンがまだない場合は、[セットアップのチュートリアルのパート 1](tutorial-1st-experiment-sdk-setup.md) を済ませておいてください。
* セットアップのチュートリアルが完了したら、同じノートブック サーバーを使用して、*tutorials/machine-learning-pipelines-advanced/tutorial-pipeline-batch-scoring-classification.ipynb* ノートブックを開きます。

独自の[ローカル環境](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials)でセットアップのチュートリアルを実行したい場合は、[GitHub](how-to-configure-environment.md#local) 上のチュートリアルを利用できます。 `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-pipeline-steps pandas requests` を実行して必要なパッケージを取得してください。

## <a name="configure-workspace-and-create-a-datastore"></a>ワークスペースを構成してデータストアを作成する

既存の Azure Machine Learning ワークスペースからワークスペース オブジェクトを作成します。

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

> [!IMPORTANT]
> このコード スニペットでは、ワークスペースの構成が現在のディレクトリまたはその親に保存されていることを想定しています。 ワークスペースの作成方法について詳しくは、[Azure Machine Learning ワークスペースを作成し、管理する](how-to-manage-workspace.md)方法に関するページを参照してください。 構成をファイルに保存する方法について詳しくは、「[ワークスペース構成ファイルを作成する](how-to-configure-environment.md#workspace)」を参照してください。

## <a name="create-a-datastore-for-sample-images"></a>サンプル画像のデータストアを作成する

`pipelinedata` アカウントのパブリック BLOB コンテナー `sampledata` から、ImageNet のパブリック評価データ サンプルを入手します。 そのデータに `images_datastore` という名前のワークスペースからアクセスできるよう、`register_azure_blob_container()` を呼び出します。 そのうえで、ワークスペースの既定のデータストアを出力データストアとして設定します。 この出力データストアを使用して、パイプラインにおける出力のスコアリングを実行します。

データへのアクセスの詳細については、[データへのアクセス方法](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#python-sdk)に関するページを参照してください。

```python
from azureml.core.datastore import Datastore

batchscore_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="images_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata", 
                      overwrite=True)

def_data_store = ws.get_default_datastore()
```

## <a name="create-dataset-objects"></a>データセット オブジェクトを作成する

パイプラインを作成する際、ワークスペースのデータストアからデータを読み取るために `Dataset` オブジェクトを、またパイプライン ステップ間で中間データを転送するために `PipelineData` オブジェクトを使用します。

> [!Important]
> このチュートリアルのバッチ スコアリングの例で使用するパイプライン ステップは 1 つだけです。 複数のステップを使用したユースケースでは、標準的なフローにこれらのステップが含まれます。
>
> 1. `Dataset` オブジェクトを "*入力*" に使用して生データを取り込み、なんらかの変換を実行した後、`PipelineData` オブジェクトを "*出力*" します。
>
> 2. 前のステップの `PipelineData` "*出力オブジェクト*" を "*入力オブジェクト*" として使用します。 後続のステップに対して処理を繰り返します。

このシナリオでは、入力画像と分類ラベル (y 検定値) の両方について、データストアのディレクトリに対応する `Dataset` オブジェクトを作成します。 バッチ スコアリングの出力データ用の `PipelineData` オブジェクトも作成します。

```python
from azureml.core.dataset import Dataset
from azureml.pipeline.core import PipelineData

input_images = Dataset.File.from_files((batchscore_blob, "batchscoring/images/"))
label_ds = Dataset.File.from_files((batchscore_blob, "batchscoring/labels/"))
output_dir = PipelineData(name="scores", 
                          datastore=def_data_store, 
                          output_path_on_compute="batchscoring/results")
```

次に、データセットをワークスペースに登録します。

```python

input_images = input_images.register(workspace = ws, name = "input_images")
label_ds = label_ds.register(workspace = ws, name = "label_ds")
```

## <a name="download-and-register-the-model"></a>モデルをダウンロードして登録する

パイプラインでのバッチ スコアリングに使用する事前トレーニング済みの TensorFlow モデルをダウンロードします。 まず、モデルの格納先となるローカル ディレクトリを作成します。 その後、モデルをダウンロードして抽出します。

```python
import os
import tarfile
import urllib.request

if not os.path.isdir("models"):
    os.mkdir("models")
    
response = urllib.request.urlretrieve("http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz", "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall("models")
```

次に、モデルをワークスペースに登録します。パイプライン処理でモデルを簡単に取得できるようになります。 `register()` 静的関数で重要となるのは `model_name` パラメーターです。SDK 全体でモデルを特定する際に使用します。

```python
from azureml.core.model import Model
 
model = Model.register(model_path="models/inception_v3.ckpt",
                       model_name="inception",
                       tags={"pretrained": "inception"},
                       description="Imagenet trained tensorflow inception",
                       workspace=ws)
```

## <a name="create-and-attach-the-remote-compute-target"></a>リモートのコンピューティング先を作成してアタッチする

機械学習パイプラインは、ローカルで実行することができないので、クラウド リソース ("*リモート コンピューティング先*") で実行します。 リモート コンピューティング先は、実験や機械学習のワークフローを実行する再利用可能な仮想コンピューティング環境です。 

次のコードを実行して GPU 対応のコンピューティング先 [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) を作成し、自分のワークスペースにアタッチします。 コンピューティング先の詳細については、[概念に関する記事](https://docs.microsoft.com/azure/machine-learning/concept-compute-target)を参照してください。


```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.exceptions import ComputeTargetException
compute_name = "gpu-cluster"

# checks to see if compute target already exists in workspace, else create it
try:
    compute_target = ComputeTarget(workspace=ws, name=compute_name)
except ComputeTargetException:
    config = AmlCompute.provisioning_configuration(vm_size="STANDARD_NC6",
                                                   vm_priority="lowpriority", 
                                                   min_nodes=0, 
                                                   max_nodes=1)

    compute_target = ComputeTarget.create(workspace=ws, name=compute_name, provisioning_configuration=config)
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

## <a name="write-a-scoring-script"></a>スコアリング スクリプトを作成する

スコアリングを実行するには、`batch_scoring.py` というバッチ スコアリング スクリプトを作成して現在のディレクトリに書き込みます。 このスクリプトは、入力画像を受け取って分類モデルを適用し、結果ファイルに予測を出力します。

`batch_scoring.py` スクリプトは次のパラメーターを受け取ります。これらは後ほど作成する `ParallelRunStep` から渡されます。

- `--model_name`:使用するモデルの名前。
- `--labels_dir`:`labels.txt` ファイルの場所。

パイプライン ステップにパラメーターを渡すために、このパイプラインのインフラストラクチャでは `ArgumentParser` クラスを使用しています。 たとえば、以下のコードでは、最初の引数 `--model_name` に `model_name` というプロパティ識別子が割り当てられています。 `init()` 関数の中では、`Model.get_model_path(args.model_name)` を使用して、このプロパティにアクセスしています。


```python
%%writefile batch_scoring.py

import os
import argparse
import datetime
import time
import tensorflow as tf
from math import ceil
import numpy as np
import shutil
from tensorflow.contrib.slim.python.slim.nets import inception_v3

from azureml.core import Run
from azureml.core.model import Model
from azureml.core.dataset import Dataset

slim = tf.contrib.slim

image_size = 299
num_channel = 3


def get_class_label_dict(labels_dir):
    label = []
    labels_path = os.path.join(labels_dir, 'labels.txt')
    proto_as_ascii_lines = tf.gfile.GFile(labels_path).readlines()
    for l in proto_as_ascii_lines:
        label.append(l.rstrip())
    return label


def init():
    global g_tf_sess, probabilities, label_dict, input_images

    parser = argparse.ArgumentParser(description="Start a tensorflow model serving")
    parser.add_argument('--model_name', dest="model_name", required=True)
    parser.add_argument('--labels_dir', dest="labels_dir", required=True)
    args, _ = parser.parse_known_args()

    label_dict = get_class_label_dict(args.labels_dir)
    classes_num = len(label_dict)

    with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
        input_images = tf.placeholder(tf.float32, [1, image_size, image_size, num_channel])
        logits, _ = inception_v3.inception_v3(input_images,
                                              num_classes=classes_num,
                                              is_training=False)
        probabilities = tf.argmax(logits, 1)

    config = tf.ConfigProto()
    config.gpu_options.allow_growth = True
    g_tf_sess = tf.Session(config=config)
    g_tf_sess.run(tf.global_variables_initializer())
    g_tf_sess.run(tf.local_variables_initializer())

    model_path = Model.get_model_path(args.model_name)
    saver = tf.train.Saver()
    saver.restore(g_tf_sess, model_path)


def file_to_tensor(file_path):
    image_string = tf.read_file(file_path)
    image = tf.image.decode_image(image_string, channels=3)

    image.set_shape([None, None, None])
    image = tf.image.resize_images(image, [image_size, image_size])
    image = tf.divide(tf.subtract(image, [0]), [255])
    image.set_shape([image_size, image_size, num_channel])
    return image


def run(mini_batch):
    result_list = []
    for file_path in mini_batch:
        test_image = file_to_tensor(file_path)
        out = g_tf_sess.run(test_image)
        result = g_tf_sess.run(probabilities, feed_dict={input_images: [out]})
        result_list.append(os.path.basename(file_path) + ": " + label_dict[result[0]])
    return result_list
```

> [!TIP]
> このチュートリアルのパイプラインに含まれるステップは 1 つだけで、出力はファイルに書き込まれます。 複数のステップから成るパイプラインでは、後続のステップに入力する出力データの書き込み先ディレクトリを定義する場合にも `ArgumentParser` を使用します。 `ArgumentParser` の設計パターンを使用して、複数のパイプライン ステップ間でデータを受け渡しする例については、[ノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb)を参照してください。

## <a name="build-the-pipeline"></a>パイプラインを構築する

パイプラインを実行する前に、Python 環境を定義するオブジェクトを作成し、`batch_scoring.py` スクリプトに必要な依存関係を作成します。 主な依存関係として Tensorflow が必要となるほか、ParallelRunStep に必要な `azureml-core` および `azureml-dataprep[fuse]` もインストールします。 さらに Docker および Docker-GPU のサポートを指定します。

```python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.15.2",
                                            "azureml-core", "azureml-dataprep[fuse]"])
env = Environment(name="parallelenv")
env.python.conda_dependencies = cd
env.docker.base_image = DEFAULT_GPU_IMAGE
```

### <a name="create-the-configuration-to-wrap-the-script"></a>スクリプトをラップする構成を作成する

スクリプト、環境構成、およびパラメーターを使用して、パイプラインのステップを作成します。 既にワークスペースに関連付けたコンピューティング ターゲットを指定します。

```python
from azureml.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    environment=env,
    entry_script="batch_scoring.py",
    source_directory=".",
    output_action="append_row",
    mini_batch_size="20",
    error_threshold=1,
    compute_target=compute_target,
    process_count_per_node=2,
    node_count=1
)
```

### <a name="create-the-pipeline-step"></a>パイプラインのステップを作成する

パイプライン ステップとは、パイプラインを実行するために必要なあらゆる要素をカプセル化するオブジェクトであり、以下のようなものがあります。

* 環境と依存関係の設定
* パイプラインを実行するためのコンピューティング リソース
* 入力データと出力データ、カスタム パラメーター
* ステップ中に実行するスクリプトまたは SDK ロジックへの参照

親クラスである [`PipelineStep`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py) は、複数のクラスによって継承されています。 クラスを選択し、特定のフレームワークまたはスタックを使用してステップを作成できます。 この例では `ParallelRunStep` クラスを使用し、カスタム Python スクリプトを使ってステップのロジックを定義します。 スクリプトの引数がステップへの入力またはステップからの出力である場合、引数を "*2 か所*" で定義する必要があります。1 つは `arguments` 配列、"*もう 1 つ*" は `input` または `output` パラメーターです。 

複数のステップが存在するシナリオでは、`outputs` 配列のオブジェクト参照が後続のパイプライン ステップの "*入力*" として使用できるようになります。

```python
from azureml.pipeline.steps import ParallelRunStep
from datetime import datetime

parallel_step_name = "batchscoring-" + datetime.now().strftime("%Y%m%d%H%M")

label_config = label_ds.as_named_input("labels_input")

batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--model_name", "inception",
               "--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
    allow_reuse=False
)
```

さまざまなタイプのステップに使用できるクラスを網羅した一覧については、「[ステップ パッケージ](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py)」を参照してください。

## <a name="submit-the-pipeline"></a>パイプラインを送信する

次に、パイプラインを実行します。 まず、自分のワークスペースの参照および作成したパイプライン ステップを使用して `Pipeline` オブジェクトを作成します。 `steps` パラメーターは、ステップの配列です。 この場合、バッチ スコアリング用のステップは 1 つだけです。 複数のステップが含まれたパイプラインを作成する場合は、この配列内にステップを順に配置します。

次に、`Experiment.submit()` 関数を使用して、実行するパイプラインを送信します。 パイプラインの作成プロセス中は、`wait_for_completion` 関数からログが出力されます。 ログを使用して、最新の進行状況を確認することができます。

> [!IMPORTANT]
> パイプラインの初回実行には約 "*15 分*" かかります。 依存関係をすべてダウンロードする必要があるほか、Docker イメージの作成と Python 環境のプロビジョニングおよび作成が行われます。 パイプラインを再度実行する際は、それらのリソースが作成されるのではなく再利用されるので、時間が大幅に短縮されます。 ただし、パイプラインの総実行時間は、実際のスクリプトのワークロードと、各パイプライン ステップで実行される処理によって異なります。

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline)
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>出力をダウンロードして確認する

次のコードを実行して、`batch_scoring.py` スクリプトから生成された出力ファイルをダウンロードします。 その後、スコアリングの結果を調べます。

```python
import pandas as pd

batch_run = next(pipeline_run.get_children())
batch_output = batch_run.get_output_data("scores")
batch_output.download(local_path="inception_results")

for root, dirs, files in os.walk("inception_results"):
    for file in files:
        if file.endswith("parallel_run_step.txt"):
            result_file = os.path.join(root, file)

df = pd.read_csv(result_file, delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
print("Prediction has ", df.shape[0], " rows")
df.head(10)
```

## <a name="publish-and-run-from-a-rest-endpoint"></a>発行して REST エンドポイントから実行する

次のコードを実行してパイプラインをワークスペースに発行します。 Azure Machine Learning Studio のワークスペースでは、実行の履歴や時間など、パイプラインのメタデータを確認できます。 Studio から手動でパイプラインを実行することもできます。

パイプラインを発行すると、任意のプラットフォーム上の任意の HTTP ライブラリから REST エンドポイントを使用してパイプラインを実行できるようになります。

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

REST エンドポイントからパイプラインを実行するには、OAuth2 ベアラータイプの認証ヘッダーが必要です。 以下の例では説明のために対話型認証を使用していますが、自動化された認証 (ヘッドレス認証) を必要とする運用環境のシナリオではほとんどの場合、[この記事の説明](how-to-setup-authentication.md)にあるようにサービス プリンシパル認証を使用します。

サービス プリンシパル認証では、*Azure Active Directory* に "*アプリの登録*" を作成する必要があります。 まず、クライアント シークレットを生成したうえで、ご自分の機械学習ワークスペースへの "*ロール アクセス*" をサービス プリンシパルに付与します。 認証フローは、[`ServicePrincipalAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py) クラスを使用して管理します。 

[`InteractiveLoginAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.interactiveloginauthentication?view=azure-ml-py) と `ServicePrincipalAuthentication` は、どちらも `AbstractAuthentication` を継承します。 どちらの場合も同じように [`get_authentication_header()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.abstractauthentication?view=azure-ml-py#get-authentication-header--) 関数を使用してヘッダーをフェッチします。

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

REST URL は、発行済みのパイプライン オブジェクトの `endpoint` プロパティから取得します。 REST URL は、Azure Machine Learning Studio のワークスペースでも確認できます。 

このエンドポイントに対する HTTP POST 要求を作成します。 要求には、認証ヘッダーを指定します。 実験の名前が含まれた JSON ペイロード オブジェクトを追加します。

実行をトリガーするための要求を作成します。 応答ディクショナリの `Id` キーにアクセスして実行 ID の値を取得するコードを追加します。

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"process_count_per_node": 6}})
run_id = response.json()["Id"]
```

この実行 ID を使用して、新しい実行の状態を監視します。 新しい実行が完了するまでには、10 分から 15 分、さらに時間がかかります。 

新しい実行は、先ほどチュートリアルの中で実行したパイプラインと同様になります。 出力全体の確認は省略してもかまいません。

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure Machine Learning の他のチュートリアルを実行する予定の場合、このセクションは実行しないでください。

### <a name="stop-the-compute-instance"></a>コンピューティング インスタンスの停止

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>すべてを削除する

作成したリソースを今後使用する予定がない場合は、課金が発生しないように削除します。

1. Azure portal で、左側のメニューにある **[リソース グループ]** を選択します。
1. リソース グループの一覧で、自分が作成したリソース グループを選択します。
1. **[リソース グループの削除]** を選択します。
1. リソース グループ名を入力します。 次に、 **[削除]** を選択します。

リソース グループは保持しつつ、いずれかのワークスペースを削除することもできます。 ワークスペースのプロパティを表示し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

機械学習パイプラインに関するこのチュートリアルでは、以下のタスクを学習しました。

> [!div class="checklist"]
> * リモートの GPU コンピューティング リソース上で実行するための環境の依存関係を使用してパイプラインを作成する。
> * 事前トレーニング済みの TensorFlow モデルを使用してバッチ予測を実行するスコアリング スクリプトを作成する。
> * パイプラインを発行して REST エンドポイントから実行できるようにする。

Machine Learning SDK を使用してパイプラインを作成するその他の例については、[ノートブック リポジトリ](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)を参照してください。
