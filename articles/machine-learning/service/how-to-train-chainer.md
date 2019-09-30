---
title: Chainer を使用したディープ ラーニング ニューラル ネットワークのトレーニング
titleSuffix: Azure Machine Learning
description: Azure Machine Learning の Chainer 推定クラスを使用して、PyTorch トレーニング スクリプトをエンタープライズ規模で実行する方法について説明します。  このサンプル スクリプトでは、numpy 上で実行される Chainer Python ライブラリを使用して、ディープ ラーニング ニューラル ネットワークを構築するために手書きの数字の画像を分類します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: sdgilley
ms.date: 08/02/2019
ms.openlocfilehash: 79c0cadc1b266a6d160cd36fc21dcaf36637a2b1
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076420"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning"></a>Azure Machine Learning を使用して大規模な Chainer モデルをトレーニングし、登録する

この記事では、Azure Machine Learning の [Chainer 推定](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py)クラスを使用して、[Chainer](https://chainer.org/) トレーニング スクリプトをエンタープライズ規模で実行する方法について説明します。 この記事のトレーニング スクリプトの例では、人気の [MNIST データセット](http://yann.lecun.com/exdb/mnist/)を使用し、[numpy](https://www.numpy.org/) を基盤に Chainer Python ライブラリを使用して構築されたディープ ニューラル ネットワーク (DNN) を使用して手書きの数字を分類します。

Chainer のディープ ラーニング モデルを一からトレーニングする場合でも、既存のモデルをクラウドに持ち込む場合でも、Azure Machine Learning のエラスティック クラウド コンピューティング リソースを使用して、オープンソースのトレーニング ジョブをスケールアウトできます。 Azure Machine Learning を使用して、運用レベルのモデルをビルド、デプロイ、バージョン管理、および監視することができます。 

[ディープ ラーニングと機械学習の比較](concept-deep-learning-vs-machine-learning.md)の詳細を確認してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。

## <a name="prerequisites"></a>前提条件

このコードは、次の環境のいずれかで実行してください。

- Azure Machine Learning Notebook VM - ダウンロードやインストールは必要なし

    - 「[チュートリアル: 環境とワークスペースを設定する](tutorial-1st-experiment-sdk-setup.md)」を完了して、SDK とサンプル リポジトリが事前に読み込まれた専用のノートブック サーバーを作成します。
    - Notebook サーバー上の samples deep learning フォルダー内で、**how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer** フォルダーから完了済みのノートブックとファイルを見つけます。  このノートブックには、インテリジェントなハイパーパラメーター調整、モデル デプロイ、およびノートブックのウィジェットを示す展開済みセクションが含まれています。

- 独自の Jupyter Notebook サーバー

    - [Azure Machine Learning SDK をインストールします](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。
    - [ワークスペース構成ファイルを作成します](how-to-configure-environment.md#workspace)。
    - サンプル スクリプト ファイル [chainer_mnist.py](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/chainer_mnist.py) をダウンロードします。
     - GitHub サンプル ページにも、完全な [Jupyter Notebook バージョン](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)があります。 このノートブックには、インテリジェント ハイパーパラメーター チューニング、モデル デプロイ、ノートブック ウィジェットを取り上げた拡張セクションがあります。

## <a name="set-up-the-experiment"></a>実験を設定する

このセクションでは、必要な Python パッケージを読み込み、ワークスペースを初期化し、実験を作成し、トレーニング データとトレーニング スクリプトをアップロードすることで、トレーニング実験を設定します。

### <a name="import-packages"></a>パッケージをインポートする

まず、azureml.core Python ライブラリをインポートし、バージョン番号を表示します。

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>ワークスペースを初期化する

[Azure Machine Learning ワークスペース](concept-workspace.md)は、サービス用の最上位のリソースです。 作成されるすべての成果物を操作できる一元的な場所が用意されています。 Python SDK では、[`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) オブジェクトを作成することでワークスペースの成果物にアクセスできます。

[前提条件のセクション](#prerequisites)で作成した `config.json` ファイルを読み取ることによって、ワークスペース オブジェクトを作成します。

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>プロジェクト ディレクトリを作成する
リモート リソースでアクセスを必要とする、ローカル コンピューターからの必要なコードがすべて含まれるディレクトリを作成します。 これには、トレーニング スクリプトと、トレーニング スクリプトが依存する追加ファイルが含まれます。

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>トレーニング スクリプトを準備する

このチュートリアルでは、トレーニング スクリプトの **chainer_mnist.py** は既に用意されています。 実際には、コードを変更しなくても、あらゆるカスタム トレーニング スクリプトをそのまま Azure ML で実行できるはずです。

Azure ML の追跡およびメトリック機能を利用するには、トレーニング スクリプト内に少量の Azure ML コードを追加します。  トレーニング スクリプト **chainer_mnist.py** には、スクリプト内の `Run` オブジェクトを使用して Azure ML 実行に一部のメトリックをログ記録する方法があります。

指定されたトレーニング スクリプトでは、Chainer の `datasets.mnist.get_mnist` 関数のサンプル データを使用します。  独自のデータについては、[データセットやスクリプトのアップロード](how-to-train-keras.md)などの手順を使用して、トレーニング中にデータを使用できるようにすることが必要になる場合があります。

トレーニング スクリプト **chainer_mnist.py** をプロジェクト ディレクトリにコピーします。

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-a-deep-learning-experiment"></a>ディープ ラーニングの実験を作成する

実験を作成します。 この例では、"chainer-mnist" という実験を作成します。

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>コンピューティング先を作成または取得する

モデルをトレーニングするには[コンピューティング先](concept-compute-target.md)が必要です。 この例では、リモート トレーニング コンピューティング リソースに Azure ML マネージド コンピューティング (AmlCompute) を使用します。

**AmlCompute の作成には約 5 分かかります**。 その名前の AmlCompute がワークスペースに既にある場合、このコードの作成プロセスはスキップされます。  

```Python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# use get_status() to get a detailed status for the current cluster. 
print(compute_target.get_status().serialize())
```

コンピューティング先の詳細については、[コンピューティング先の概要](concept-compute-target.md)に関する記事を参照してください。

## <a name="create-a-chainer-estimator"></a>Chainer エスティメーターを作成する

[Chainer エスティメーター](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py)には、コンピューティング先で Chainer トレーニング ジョブを起動する簡単な方法が用意されています。

Chainer エスティメーターは、ジェネリック [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) クラスを介して実装されています。これは、任意のフレームワークをサポートするために使用できます。 ジェネリック エスティメーターを使用したモデルのトレーニングの詳細については、[エスティメーターを使用した Azure Machine Learning によるモデルのトレーニング](how-to-train-ml-models.md)に関するページを参照してください。

```Python
from azureml.train.dnn import Chainer

script_params = {
    '--epochs': 10,
    '--batchsize': 128,
    '--output_dir': './outputs'
}

estimator = Chainer(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    pip_packages=['numpy', 'pytest'],
                    entry_script='chainer_mnist.py',
                    use_gpu=True)
```

## <a name="submit-a-run"></a>実行を送信する

[実行オブジェクト](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)には、ジョブの実行中および完了後の実行履歴へのインターフェイスが用意されています。

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

実行は、以下の段階を経て実施されます。

- **準備**:docker イメージは Chainer エスティメーターに従って作成されます。 イメージはワークスペースのコンテナー レジストリにアップロードされ、後で実行するためにキャッシュされます。 ログは実行履歴にもストリーミングされ、進行状況を監視するために表示することができます。

- **拡大縮小**:Batch AI クラスターでの実行に現在使用可能な数より多くのノードが必要な場合、クラスターはスケールアップを試みます。

- **Running**: script フォルダー内のすべてのスクリプトがコンピューティング先にアップロードされ、データ ストアがマウントまたはコピーされ、entry_script が実行されます。 stdout および ./logs フォルダーの出力は実行履歴にストリーミングされ、実行を監視するために使用できます。

- **後処理**:実行の ./outputs フォルダーが実行履歴にコピーされます。

## <a name="save-and-register-the-model"></a>モデルを保存して登録する

モデルのトレーニングが終わったら、それをワークスペースに保存して登録できます。 モデルの登録を使用すると、モデルをワークスペースに格納し、バージョン管理して、[モデルの管理とデプロイ](concept-model-management-and-deployment.md)を簡単にすることができます。


モデルのトレーニングが完了したら、次のコードでワークスペースにモデルを登録します。  

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```

> [!TIP]
> モデルが見つからないというエラーが表示された場合は、1 分ほど待ってから、もう一度やり直してください。  場合によっては、トレーニングの実行が終了してから出力ディレクトリでモデルが利用できるようになるまでの間に若干の遅延が発生することがあります。

モデルのローカル コピーをダウンロードすることもできます。 これは、追加のモデル検証作業をローカルで実行するのに役立つ場合があります。 トレーニング スクリプト `chainer_mnist.py` では、セーバー オブジェクトにより、モデルがローカル フォルダー (コンピューティング先に対してローカル) に永続化されます。 実行オブジェクトを使用して、データストアからコピーをダウンロードできます。

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>次の手順

この記事では、Azure Machine Learning 上で Chainer を使用して、ディープ ラーニング ニューラル ネットワークをトレーニングして登録しました。 モデルをデプロイする方法を学習するには、[モデル デプロイ](how-to-deploy-and-where.md)の記事に進んでください。

* [ハイパーパラメーターを調整する](how-to-tune-hyperparameters.md)

* [トレーニング中に実行メトリクスを追跡する](how-to-track-experiments.md)

* [Azure での分散型ディープ ラーニング トレーニングの参照アーキテクチャを表示する](/azure/architecture/reference-architectures/ai/training-deep-learning)
