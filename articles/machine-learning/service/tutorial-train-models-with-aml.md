---
title: イメージの分類チュートリアル:モデルをトレーニングする
titleSuffix: Azure Machine Learning
description: Azure Machine Learning を使用し、Python Jupyter ノートブックで scikit-learn を使用して画像の分類モデルをトレーニングする方法について説明します。 このチュートリアルは、2 部構成のシリーズのパート 1 です。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: ca0f64fe67865e18c47009779cf8bd307a21c961
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978732"
---
# <a name="tutorial-train-image-classification-models-with-mnist-data-and-scikit-learn-using-azure-machine-learning"></a>チュートリアル:Azure Machine Learning で MNIST データと scikit-learn を使用して画像の分類モデルをトレーニングする
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

このチュートリアルでは、機械学習モデルのトレーニングをリモートのコンピューティング リソース上で行います。 Python Jupyter Notebook 内の Azure Machine Learning に関するトレーニングとデプロイのワークフローを使用します。  それからノートブックをテンプレートとして使用し、独自のデータで独自の機械学習モデルをトレーニングできます。 このチュートリアルは、**2 部構成のチュートリアル シリーズのパート 1 です**。  

このチュートリアルでは、Azure Machine Learning で [MNIST](http://yann.lecun.com/exdb/mnist/) データセットや [scikit-learn](https://scikit-learn.org) を使用して、単純なロジスティック回帰をトレーニングします。 MNIST は、70,000 ものグレースケールのイメージから成る、人気のあるデータセットです。 各イメージは、0 から 9 までの数値を表す 28 x 28 ピクセルの手書き数字です。 多クラス分類子を作成して、特定のイメージが表す数字を識別することが目標です。

次の操作の実行方法を確認してください。

> [!div class="checklist"]
> * 開発環境を設定する。
> * データにアクセスして検査する。
> * リモート クラスターで単純なロジスティック回帰モデルをローカルでトレーニングします。
> * トレーニングの結果をレビューし、最適なモデルを登録する。

[このチュートリアルのパート 2](tutorial-deploy-models-with-aml.md) では、モデルを選択してデプロイする方法を学習します。

Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。

>[!NOTE]
> この記事のコードは、[Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) バージョン 1.0.65 を使用してテストされています。

## <a name="prerequisites"></a>前提条件

* 「[チュートリアル: 初めての Azure ML 実験の作成を開始する](tutorial-1st-experiment-sdk-setup.md)」を完了することで、以下の操作を行います。
    * ワークスペースの作成
    * チュートリアル ノートブックをワークスペース内のフォルダーに複製します。
    * クラウドベースのノートブック VM を作成します。

* 複製した **tutorials** フォルダーで、**img-classification-part1-training.ipynb** ノートブックを開きます。 


チュートリアルと付随する **utils.py** ファイルは、独自の[ローカル環境](how-to-configure-environment.md#local)で使用する場合、[GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) から入手することもできます。 `pip install azureml-sdk[notebooks] azureml-opendatasets matplotlib` を実行して、このチュートリアルの依存関係をインストールします。

> [!Important]
> 以降この記事には、ノートブックと同じ内容が記載されています。  
>
> コードを実行しながら読み進めたい方は、ここで Jupyter Notebook に切り替えてください。 
> ノートブックで単一のコード セルを実行するには、そのコード セルをクリックして **Shift + Enter** キーを押します。 または、上部のツール バーから **[すべて実行]** を選択して、ノートブック全体を実行します。

## <a name="start"></a>開発環境を設定する

Python Notebook で、開発作業に関するすべての設定を行うことができます。 設定の一環として次のことを行います。

* Python パッケージをインポートする。
* ローカル コンピューターがリモート リソースと通信できるように、ワークスペースに接続する。
* 実験を作成して、すべての実行を追跡する。
* リモート コンピューティング ターゲットを作成して、トレーニングに使用する。

### <a name="import-packages"></a>パッケージをインポートする

このセッションで必要な Python パッケージをインポートします。 さらに、Azure Machine Learning の SDK のバージョンを表示します。

```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt

import azureml.core
from azureml.core import Workspace

# check core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="connect-to-a-workspace"></a>ワークスペースに接続する

既存のワークスペースからワークスペース オブジェクトを作成します。 `Workspace.from_config()` により、**config.json** ファイルが読み取られ、詳細情報が `ws` という名前のオブジェクトに読み込まれます。

```python
# load workspace configuration from the config.json file in the current folder.
ws = Workspace.from_config()
print(ws.name, ws.location, ws.resource_group, sep='\t')
```

### <a name="create-an-experiment"></a>実験の作成

実験を作成して、ワークスペース内の実行を追跡します。 1 つのワークスペースで複数の実験を保持できます。

```python
from azureml.core import Experiment
experiment_name = 'sklearn-mnist'

exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-or-attach-an-existing-compute-target"></a>コンピューティング先の作成または既存のコンピューティング先のアタッチ

マネージド サービスである Azure Machine Learning コンピューティングを使用することにより、データ サイエンティストは Azure 仮想マシンのクラスター上で機械学習モデルをトレーニングできます。 たとえば、GPU がサポートされている VM などです。 このチュートリアルでは、トレーニング環境として Azure Machine Learning コンピューティングを作成します。 以下のコードでは、まだワークスペース内にコンピューティング クラスターがない場合、それらが作成されます。

 **コンピューティング先の作成には約 5 分かかります。** ワークスペース内にコンピューティング リソースが既にある場合は、それが使用され、作成プロセスはスキップされます。

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

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
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,
                                                                min_nodes=compute_min_nodes,
                                                                max_nodes=compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # can poll for a minimum number of nodes and for a specific timeout.
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

これで、クラウドでモデルをトレーニングするために必要なパッケージとコンピューティング リソースが揃いました。

## <a name="explore-data"></a>データを調査する

モデルをトレーニングする前に、トレーニングに使用するデータを解釈する必要があります。 このセクションでは、次の方法について説明します。

* MNIST データセットをダウンロードする。
* いくつかのサンプル イメージを表示する。

### <a name="download-the-mnist-dataset"></a>MNIST データセットのダウンロード

Azure Open Datasets を使用して、未加工の MNIST データ ファイルを取得します。 [Azure オープン データセット](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets)は選別されたパブリック データセットであり、機械学習ソリューションにシナリオ固有の機能を追加してモデルの精度を上げるために使用できます。 各データセットには、異なる方法でデータを取得するための対応するクラスがあります (ここでは `MNIST`)。

このコードを使用すると、データが `FileDataset` オブジェクトとして取得されます (これは `Dataset` のサブクラスです)。 `FileDataset` によって、データストアまたはパブリック URL 内の 1 つまたは複数の任意の形式のファイルが参照されます。 クラスには、データ ソースの場所への参照を作成することで、お使いのコンピューティングにファイルをダウンロードまたはマウントする機能が用意されています。 さらに、トレーニング中に簡単に取得できるように、データセットをワークスペースに登録します。

SDK でのデータセットとその使用方法の詳細については、[方法](how-to-create-register-datasets.md)のページを参照してください。

```python
from azureml.core import Dataset
from azureml.opendatasets import MNIST

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok=True)

mnist_file_dataset = MNIST.get_file_dataset()
mnist_file_dataset.download(data_folder, overwrite=True)

mnist_file_dataset = mnist_file_dataset.register(workspace=ws,
                                                 name='mnist_opendataset',
                                                 description='training and test dataset',
                                                 create_new_version=True)
```

### <a name="display-some-sample-images"></a>複数のサンプル イメージの表示

圧縮されたファイルを `numpy` 配列内に読み込みます。 それから `matplotlib` を使用して、ラベルがあるデータセットから 30 個のランダムなイメージをプロットします。 この手順には、`util.py` ファイルに含まれている `load_data` 関数が必要です。 このファイルは、サンプル フォルダーに含まれています。 このノートブックと同じフォルダーに配置されていることを確認します。 `load_data` 関数は、圧縮ファイルを numpy 配列に解析するだけのものです。

```python
# make sure utils.py is in the same directory as this code
from utils import load_data

# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data(os.path.join(data_folder, "train-images-idx3-ubyte.gz"), False) / 255.0
X_test = load_data(os.path.join(data_folder, "t10k-images-idx3-ubyte.gz"), False) / 255.0
y_train = load_data(os.path.join(data_folder, "train-labels-idx1-ubyte.gz"), True).reshape(-1)
y_test = load_data(os.path.join(data_folder, "t10k-labels-idx1-ubyte.gz"), True).reshape(-1)

# now let's show some randomly chosen images from the traininng set.
count = 0
sample_size = 30
plt.figure(figsize=(16, 6))
for i in np.random.permutation(X_train.shape[0])[:sample_size]:
    count = count + 1
    plt.subplot(1, sample_size, count)
    plt.axhline('')
    plt.axvline('')
    plt.text(x=10, y=-10, s=y_train[i], fontsize=18)
    plt.imshow(X_train[i].reshape(28, 28), cmap=plt.cm.Greys)
plt.show()
```

ランダムなイメージのサンプルは、以下のように表示されます。

![ランダムなイメージのサンプル](./media/tutorial-train-models-with-aml/digits.png)

これで、これらのイメージの概観が頭に浮かんだり、予測結果を想定したりできるようになりました。

## <a name="train-on-a-remote-cluster"></a>リモート クラスターでのトレーニング

このタスクでは、以前に設定したリモート トレーニング クラスターにジョブを送信します。  ジョブを送信するには、次のようにします。
* ディレクトリを作成する
* トレーニング スクリプトを作成する
* エスティメータ オブジェクトを作成する
* ジョブを送信する

### <a name="create-a-directory"></a>ディレクトリを作成する

必要なコードをコンピューターからリモート リソースに配信するためのディレクトリを作成します。

```python
script_folder = os.path.join(os.getcwd(), "sklearn-mnist")
os.makedirs(script_folder, exist_ok=True)
```

### <a name="create-a-training-script"></a>トレーニング スクリプトを作成する

ジョブをクラスターに送信するには、まずトレーニング スクリプトを作成します。 次のコードを実行して、作成したばかりのディレクトリ内に `train.py` と呼ばれるトレーニング スクリプトを作成します。

```python
%%writefile $script_folder/train.py

import argparse
import os
import numpy as np
import glob

from sklearn.linear_model import LogisticRegression
from sklearn.externals import joblib

from azureml.core import Run
from utils import load_data

# let user feed in 2 parameters, the dataset to mount or download, and the regularization rate of the logistic regression model
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# load train and test set into numpy arrays
# note we scale the pixel intensity values to 0-1 (by dividing it with 255.0) so the model can converge faster.
X_train = load_data(glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0], False) / 255.0
X_test = load_data(glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0], False) / 255.0
y_train = load_data(glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0], True).reshape(-1)
y_test = load_data(glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0], True).reshape(-1)
print(X_train.shape, y_train.shape, X_test.shape, y_test.shape, sep = '\n')

# get hold of the current run
run = Run.get_context()

print('Train a logistic regression model with regularization rate of', args.reg)
clf = LogisticRegression(C=1.0/args.reg, solver="liblinear", multi_class="auto", random_state=42)
clf.fit(X_train, y_train)

print('Predict the test set')
y_hat = clf.predict(X_test)

# calculate accuracy on the prediction
acc = np.average(y_hat == y_test)
print('Accuracy is', acc)

run.log('regularization rate', np.float(args.reg))
run.log('accuracy', np.float(acc))

os.makedirs('outputs', exist_ok=True)
# note file saved in the outputs folder is automatically uploaded into experiment record
joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')
```

以下の、スクリプトがデータを取得してモデルを保存する方法に注目してください。

+ トレーニング スクリプトで引数が読み取られ、データが含まれるディレクトリが検出されます。 後でジョブを送信する際に、次のように、引数にデータストアを指定します。```parser.add_argument('--data-folder', type=str, dest='data_folder', help='data directory mounting point')```

+ トレーニング スクリプトでは、**outputs** という名前のディレクトリにモデルが保存されます。 このディレクトリ内に書き込まれたものはすべてワークスペース内に自動的にアップロードされます。 チュートリアルの後半で、このディレクトリからモデルにアクセスします。 `joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')`

+ このトレーニング スクリプトでは、データセットを正しく読み込むためにファイル `utils.py` が必要です。 次のコードでは、`utils.py` を `script_folder` にコピーして、リモート リソース上でトレーニング スクリプトと共にファイルにアクセスできるようにします。

  ```python
  import shutil
  shutil.copy('utils.py', script_folder)
  ```

### <a name="create-an-estimator"></a>推定を作成する

実行の送信には、[SKLearn 推定](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)オブジェクトが使用されます。 後で示すコードを実行し、次のものを定義することにより、推定を作成します。

* 推定オブジェクトの名前 `est`。
* スクリプトが含まれるディレクトリ。 このディレクトリ内のすべてのファイルは、実行のためにクラスター ノード内にアップロードされます。
* コンピューティング ターゲット。 ここでは、作成した Azure Machine Learning コンピューティング クラスターを使用します。
* トレーニング スクリプトの名前 **train.py**。
* トレーニング スクリプトからの必須パラメーター。

このチュートリアルでは、このターゲットは AmlCompute です。 スクリプト フォルダー内のすべてのファイルは、実行のためにクラスター ノード内にアップロードされます。 データセットを使用するために **data_folder** が設定されます。 まず、トレーニングに必要な依存関係を指定する environment オブジェクトを作成します。 

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

env = Environment('my_env')
cd = CondaDependencies.create(pip_packages=['azureml-sdk','scikit-learn','azureml-dataprep[pandas,fuse]>=1.1.14'])
env.python.conda_dependencies = cd
```

さらに、次のコードで推定を作成します。

```python
from azureml.train.sklearn import SKLearn

script_params = {
    '--data-folder': mnist_file_dataset.as_named_input('mnist_opendataset').as_mount(),
    '--regularization': 0.5
}

est = SKLearn(source_directory=script_folder,
              script_params=script_params,
              compute_target=compute_target,
              environment_definition=env, 
              entry_script='train.py')
```

### <a name="submit-the-job-to-the-cluster"></a>ジョブをクラスターに送信する

推定オブジェクトを送信して、実験を実行します。

```python
run = exp.submit(config=est)
run
```

呼び出しは非同期なので、ジョブが開始されると即時に**準備中**または**実行**状態が返されます。

## <a name="monitor-a-remote-run"></a>リモート実行を監視する

初回の実行は合計で**約 10 分**かかります。 しかし、その後の実行では、スクリプトの依存関係が変わらなければ、同じイメージが再利用されます。 したがって、コンテナーの起動時間が大幅に短縮されます。

待っている間に次のことが行われます。

- **イメージの作成**:推定で指定されている Python 環境と一致する Docker イメージが作成されます。 このイメージがワークスペースにアップロードされます。 イメージの作成とアップロードには**約 5 分**かかります。

  その後の実行のためにコンテナーがキャッシュに入れられるので、この段階は Python 環境ごとに 1 回行われます。 イメージの作成中に、ログが実行履歴にストリーミングされます。 これらのログを使用して、イメージの作成の進行状況を監視できます。

- **拡大縮小**:リモート クラスターで、現在使用可能なノードよりも多くのノードを実行する必要がある場合、自動的にノードが追加されます。 通常、拡大縮小には**約 5 分**かかります。

- **Running**: この段階では、必要なスクリプトとファイルがコンピューティング先に送信されます。 その後、データストアがマウントまたはコピーされます。 そして、**entry_script** が実行されます。 ジョブの実行中に、**stdout** と **./logs** ディレクトリが実行履歴にストリーミングされます。 これらのログを使用して、実行の進行状況を監視できます。

- **後処理**:この実行の **./outputs** ディレクトリがワークスペース内の実行履歴に上書きコピーされ、これらの結果にアクセスできるようになります。

複数の方法で、実行中のジョブの進行状況を確認できます。 このチュートリアルでは、Jupyter ウィジェットと `wait_for_completion` メソッドを使用します。

### <a name="jupyter-widget"></a>Jupyter ウィジェット

[Jupyter ウィジェット](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)を使用して、実行の進行状況を監視します。 実行の送信と同様に、このウィジェットも非同期です。また、ジョブが完了するまで、10 秒から 15 秒ごとにライブ更新が提供されます。

```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

トレーニングの最後には、ウィジェットは次のようになります。

![Notebook のウィジェット](./media/tutorial-train-models-with-aml/widget.png)

実行を取り消す必要がある場合は、[これらの手順](https://aka.ms/aml-docs-cancel-run)に従います。

### <a name="get-log-results-upon-completion"></a>完了時にログの結果を取得する

モデルのトレーニングと監視は、バック グラウンドで行われます。 モデルのトレーニングが完了するまで待ってから、さらにコードを実行します。 `wait_for_completion` を使用して、モデルのトレーニングが完了する時点を表示します。

```python
run.wait_for_completion(show_output=False)  # specify True for a verbose log
```

### <a name="display-run-results"></a>実行結果を表示する

これで、リモート クラスター上のモデルのトレーニングが完了しました。 次のようにして、モデルの精度を取得します。

```python
print(run.get_metrics())
```

出力は、リモート モデルの精度が 0.9204 であることを示しています。

`{'regularization rate': 0.8, 'accuracy': 0.9204}`

次のチュートリアルで、このモデルについてさらに詳しく探索できます。

## <a name="register-model"></a>モデルの登録

トレーニング スクリプトの最後のステップでは、`outputs/sklearn_mnist_model.pkl` ファイルが、ジョブの実行場所のクラスターの VM 内の `outputs` という名前のディレクトリ内に書き込まれました。 `outputs` は特別なディレクトリで、このディレクトリ内のすべてのコンテンツがワークスペースに自動的にアップロードされます。 このコンテンツは、ワークスペースの実験内の実行レコード内に表示されます。 したがって、モデル ファイルはワークスペースでも使用できるようになっています。

この実行に関連付けられているファイルを表示できます。

```python
print(run.get_file_names())
```

ワークスペース内でモデルを登録して、後で自分や他のコラボレーターがこのモデルのクエリ、検査、デプロイを行えるようにします。

```python
# register model
model = run.register_model(model_name='sklearn_mnist',
                           model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep='\t')
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Azure Machine Learning コンピューティング クラスターだけを削除することもできます。 ただし、自動スケーリングが有効になっており、クラスターの最小値は 0 です。 そのため、この特定のリソースが使用中でないときの追加のコンピューティング料金はかかりません。

```python
# Optionally, delete the Azure Machine Learning Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>次の手順

この Azure Machine Learning のチュートリアルでは、Python を使用して次の作業を行いました。

> [!div class="checklist"]
> * 開発環境を設定する。
> * データにアクセスして検査する。
> * 人気のある scikit-learn 機械学習ライブラリを使用して、リモート クラスターで複数のモデルをトレーニングする
> * トレーニングの詳細情報をレビューし、最適なモデルを登録する。

チュートリアル シリーズの次のパートの説明に従って、この登録済みのモデルをデプロイする準備ができました。

> [!div class="nextstepaction"]
> [チュートリアル 2 - モデルをデプロイする](tutorial-deploy-models-with-aml.md)
