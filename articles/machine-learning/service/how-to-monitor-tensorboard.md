---
title: TensorBoard を使用して実験を視覚化する
titleSuffix: Azure Machine Learning service
description: TensorBoard を起動して実験の実行履歴を視覚化し、ハイパーパラメーターの調整と再トレーニングの候補となる領域を特定します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: maxluk
ms.author: maxluk
ms.date: 06/28/2019
ms.openlocfilehash: fde2b6d1d298e89227951c376d584452fbff2679
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707062"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>TensorBoard と Azure Machine Learning を使用して実験の実行とメトリックを視覚化する

この記事では、メインとなる Azure Machine Learning service SDK に含まれる [`tensorboard` パッケージ](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py)を使用して、TensorBoard で実験の実行とメトリックを表示する方法について説明します。 実験の実行を調査すると、機械学習モデルの調整と再トレーニングをより適切に行うことができます。

[TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview) は、実験の構造とパフォーマンスを調査して把握するための Web アプリケーションスイートです。

Azure Machine Learning の実験で TensorBoard を起動する方法は、実験の種類に応じて異なります。
+ TensorBoard で使用可能なログ ファイルが実験でネイティブに出力される場合 (PyTorch、Chainer、TensorFlow などの実験)、実験の実行履歴から [TensorBoard を直接起動](#direct)できます。 

+ TensorBoard で使用可能なファイルがネイティブで出力されない実験 (Scikit-learn や Azure Machine Learning などの実験) の場合は、[`export_to_tensorboard()` メソッド](#export)を使用して実行履歴を TensorBoard ログとしてエクスポートし、そこから TensorBoard を起動します。 

## <a name="prerequisites"></a>前提条件

* TensorBoard を起動して実験の実行履歴を表示するには、あらかじめ実験でログ記録を有効にして、そのメトリックとパフォーマンスを追跡しておく必要があります。  

* この記事のコードは、次のいずれの環境でも実行できます。 

    * Azure Machine Learning Notebook VM - ダウンロードとインストールが不要

        * [クラウドベースのノートブックによるクイックスタート](quickstart-run-cloud-notebook.md#create-notebook)を完了して、SDK とサンプル リポジトリが事前に読み込まれている専用のノートブック サーバーを作成します。

        * ノートブック サーバー上の samples フォルダーで、**how-to-use-azureml > training-with-deep-learning** の順にディレクトリを移動して、次の完成したノートブックと展開されたノートブックの 2 つを見つけます。
        * export-run-history-to-run-history.ipynb
        * tensorboard.ipynb

    * 独自の Jupyter Notebook サーバー
      * [ワークスペースの作成に関する記事](setup-create-workspace.md)に従って以下を行います
          * `tensorboard` extra を使用して [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) をインストールする
          * ワークスペースと構成ファイル (config.json) を作成する
  
<a name="direct"></a>
## <a name="option-1-directly-view-run-history-in-tensorboard"></a>オプション 1:実行履歴を TensorBoard で直接表示する

このオプションは、PyTorch、Chainer、TensorFlow の実験など、TensorBoard で使用可能なログ ファイルをネイティブに出力する実験に有効です。 対象の実験がこれに当てはまらない場合は、代わりに [`export_to_tensorboard()` メソッド](#export)を使用してください。

次のコード例では、TensorFlow のリポジトリにある [MNIST デモ実験](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py)を、リモートのコンピューティング先である Azure Machine Learning コンピューティングで使用します。 次に、SDK のカスタム [TensorFlow エスティメーター](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)を使用してモデルをトレーニングしてから、この TensorFlow 実験 (つまり、TensorBoard イベント ファイルをネイティブに出力する実験) に対して TensorBoard を起動します。

### <a name="set-experiment-name-and-create-project-folder"></a>実験名を設定してプロジェクト フォルダーを作成する

ここでは、実験に名前を付けて、そのフォルダーを作成します。 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>TensorFlow デモ実験のコードをダウンロードする

TensorFlow のリポジトリには、TensorBoard の広範なインストルメンテーションを備えた MNIST デモが用意されています。 Azure Machine Learning service での使用に際して、このデモのコードを変更することはありません (また、その必要もありません)。 次のコードでは、MNIST コードをダウンロードして、新しく作成した実験用フォルダーに保存します。

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
MNIST のコード ファイル mnist_with_summaries.py 全体に、`tf.summary.scalar()`、`tf.summary.histogram()`、`tf.summary.FileWriter()` などを呼び出す行があることに注目してください。これらのメソッドは、実験の主要なメトリックを実行履歴にグループ化、記録、タグ付けします。 `tf.summary.FileWriter()` は、記録された実験メトリックのデータをシリアル化するため、特に重要です。これにより、TensorBoard は、そのメトリックから視覚化を生成できるようになります。

 ### <a name="configure-experiment"></a>実験を構成する

以下では、実験を構成し、ログ用とデータ用のディレクトリを設定します。 これらのログは Artifact サービスにアップロードされます。このサービスには、後で TensorBoard からアクセスします。

>[!Note]
> この TensorFlow の例では、TensorFlow をローカル コンピューターにインストールする必要があります。 さらに、TensorBoard はローカル コンピューターで実行されるため、TensorBoard モジュール (TensorFlow に付属) にこのノートブックのカーネルからアクセスできることが必要です。

```Python
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment

ws = Workspace.from_config()

# create directories for experiment logs and dataset
logs_dir = os.path.join(os.curdir, "logs")
data_dir = os.path.abspath(os.path.join(os.curdir, "mnist_data"))

if not path.exists(data_dir):
    makedirs(data_dir)

os.environ["TEST_TMPDIR"] = data_dir

# Writing logs to ./logs results in their being uploaded to Artifact Service,
# and thus, made accessible to our TensorBoard instance.
arguments_list = ["--log_dir", logs_dir]

# Create an experiment
exp = Experiment(ws, experiment_name)
```

### <a name="create-a-cluster-for-your-experiment"></a>実験用のクラスターを作成する
この実験用に AmlCompute クラスターを作成しますが、実験はどの環境にも作成できるため、引き続き実験の実行履歴に対して TensorBoard を起動できます。 

```Python
from azureml.core.compute import ComputeTarget, AmlCompute

cluster_name = "cpucluster"

cts = ws.compute_targets
found = False
if cluster_name in cts and cts[cluster_name].type == 'AmlCompute':
   found = True
   print('Found existing compute target.')
   compute_target = cts[cluster_name]
if not found:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

compute_target.wait_for_completion(show_output=True, min_node_count=None)

# use get_status() to get a detailed status for the current cluster. 
# print(compute_target.get_status().serialize())
```

### <a name="submit-run-with-tensorflow-estimator"></a>TensorFlow エスティメーターを使用して実行を送信する

TensorFlow エスティメーターには、コンピューティング先で TensorFlow トレーニング ジョブを起動する簡単な方法が用意されています。 これは、ジェネリック [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) クラスを介して実装されています。これは、任意のフレームワークをサポートするために使用できます。 ジェネリック エスティメーターを使用したモデルのトレーニングの詳細については、[エスティメーターを使用した Azure Machine Learning によるモデルのトレーニング](how-to-train-ml-models.md)に関するページを参照してください。

```Python
from azureml.train.dnn import TensorFlow
script_params = {"--log_dir": "./logs"}

tf_estimator = TensorFlow(source_directory=exp_dir,
                          compute_target=compute_target,
                          entry_script='mnist_with_summaries.py',
                          script_params=script_params)

run = exp.submit(tf_estimator)
```

### <a name="launch-tensorboard"></a>TensorBoard を起動する

TensorBoard は、実行中または実行完了後に起動できます。 以下では、TensorBoard オブジェクト インスタンス `tb` (`run` で読み込まれた実験の実行履歴を取得する) を作成した後、`start()` メソッドを使用して TensorBoard を起動します。 
  
[TensorBoard コンストラクター](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py)は実行の配列を受け取るため、単一要素の配列として渡すように注意してください。

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>オプション 2:履歴をログとしてエクスポートして TensorBoard で表示する

次のコードでは、サンプル実験を設定し、Azure Machine Learning の実行履歴 API を使用してログ プロセスを開始してから、TensorBoard で視覚化に使用できるログに実験の実行履歴をエクスポートします。 

### <a name="set-up-experiment"></a>実験を設定する

次のコードでは、新しい実験を設定し、実行ディレクトリに `root_run` という名前を付けます。 

```python
from azureml.core import Workspace, Experiment
import azuremml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

次に、糖尿病データセット (scikit-learn 付属の組み込みの小さなデータセット) を読み込み、テスト セットとトレーニング セットに分割します。

```Python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
X, y = load_diabetes(return_X_y=True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
x_train, x_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"x":x_train, "y":y_train},        
    "test":{"x":x_test, "y":y_test}
}
```

### <a name="run-experiment-and-log-metrics"></a>実験を実行してメトリックを記録する

次のコードでは、線形回帰モデルをトレーニングし、主要なメトリックであるアルファ係数 `alpha` と平均二乗誤差 `mse` を実行履歴に記録します。

```Python
from tqdm import tqdm
alphas = [.1, .2, .3, .4, .5, .6 , .7]
# try a bunch of alpha values in a Linear Regression (aka Ridge regression) mode
for alpha in tqdm(alphas):
  # create child runs and fit lines for the resulting models
  with root_run.child_run("alpha" + str(alpha)) as run
 
   reg = Ridge(alpha=alpha)
   reg.fit(data["train"]["x"], data["train"]["y"])    
 
   preds = reg.predict(data["test"]["x"])
   mse = mean_squared_error(preds, data["test"]["y"])
   # End train and eval

# log alpha, mean_squared_error and feature names in run history
   root_run.log("alpha", alpha)
   root_run.log("mse", mse)
```

### <a name="export-runs-to-tensorboard"></a>実行を TensorBoard にエクスポートする

SDK の [export_to_tensorboard()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py) メソッドを使用すると、Azure Machine Learning の実験の実行履歴を TensorBoard のログにエクスポートできるため、これらを TensorBoard で表示することができます。  

次のコードでは、現在の作業ディレクトリ内に `logdir` フォルダーを作成します。 このフォルダーが、`root_run` から実験の実行履歴とログをエクスポートし、その実行を完了済みとマークする場所になります。 

```Python
from azureml.tensorboard.export import export_to_tensorboard
import os

logdir = 'exportedTBlogs'
log_path = os.path.join(os.getcwd(), logdir)
try:
    os.stat(log_path)
except os.error:
    os.mkdir(log_path)
print(logdir)

# export run history for the project
export_to_tensorboard(root_run, logdir)

root_run.complete()
```

>[!Note]
 `export_to_tensorboard(run_name, logdir)` のように実行名を指定することで、特定の実行を TensorBoard にエクスポートすることもできます。

TensorBoard を起動および停止する この実験の実行履歴がエクスポートされたら、[start()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#start-start-browser-false-) メソッドを使用して TensorBoard を起動できます。 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

完了したら、必ず TensorBoard オブジェクトの [stop()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#stop--) メソッドを呼び出してください。 そうしないと、ノートブック カーネルをシャットダウンするまで、TensorBoard は実行したままになります。 

```python
tb.stop()
```

## <a name="next-steps"></a>次の手順

この手順では、2 つの実験を作成したほか、それらの実行履歴に対して TensorBoard を起動して調整と再トレーニングの候補となる領域を特定する方法について学習しました。 

* お使いのモデルに満足している場合は、[モデルのデプロイ方法](how-to-deploy-and-where.md)に関する記事に進んでください。 
* [ハイパーパラメーターの調整](how-to-tune-hyperparameters.md)の詳細を確認してください。 
