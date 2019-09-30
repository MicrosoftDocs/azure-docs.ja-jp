---
title: PyTorch を使用したディープ ラーニング ニューラル ネットワークのトレーニング
titleSuffix: Azure Machine Learning
description: Azure Machine Learning の PyTorch 推定クラスを使用して、PyTorch トレーニング スクリプトをエンタープライズ規模で実行する方法について説明します。  このサンプル スクリプトは、PyTorch の転移学習チュートリアルに基づいてニワトリと七面鳥の画像を分類し、ディープ ラーニング ニューラル ネットワークを構築します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: c688f5a59a9a6d980f50a726f9da4dc4379ce073
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002568"
---
# <a name="train-pytorch-deep-learning-models-at-scale-with-azure-machine-learning"></a>Azure Machine Learning を使用して PyTorch ディープ ラーニング モデルを大規模にトレーニングする

この記事では、Azure Machine Learning の [PyTorch 推定](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)クラスを使用して、[PyTorch](https://pytorch.org/) トレーニング スクリプトをエンタープライズ規模で実行する方法について説明します。  

この記事のサンプル スクリプトは、PyTorch の転移学習[チュートリアル](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)に基づいてニワトリと七面鳥の画像を分類し、ディープ ラーニング ニューラル ネットワークを構築するために使用されます。 

PyTorch のディープ ラーニング モデルを一からトレーニングする場合でも、既存のモデルをクラウドに持ち込む場合でも、Azure Machine Learning のエラスティック クラウド コンピューティング リソースを使用して、オープンソースのトレーニング ジョブをスケールアウトできます。 Azure Machine Learning を使用して、運用レベルのモデルをビルド、デプロイ、バージョン管理、および監視することができます。 

[ディープ ラーニングと機械学習の比較](concept-deep-learning-vs-machine-learning.md)の詳細を確認してください。

## <a name="prerequisites"></a>前提条件

このコードは、次の環境のいずれかで実行してください。

 - Azure Machine Learning Notebook VM - ダウンロードやインストールは必要なし

    - 「[チュートリアル: 環境とワークスペースを設定する](tutorial-1st-experiment-sdk-setup.md)」を完了して、SDK とサンプル リポジトリが事前に読み込まれた専用のノートブック サーバーを作成します。
    - ノートブック サーバー上のディープ ラーニングの samples フォルダーで、**how-to-use-azureml > training-with-deep-learning > train-hyperparameter-tune-deploy-with-pytorch** の順に移動して、次の完了および展開済みノートブックを見つけます。 
 
 - 独自の Jupyter Notebook サーバー

    - [Azure Machine Learning SDK をインストールします](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。
    - [ワークスペース構成ファイルを作成します](how-to-configure-environment.md#workspace)。
    - [サンプル スクリプト ファイル `pytorch_train.py` をダウンロードする](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
     
    このガイドの完成した [Jupyter Notebook バージョン](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb)は、GitHub サンプル ページにもあります。 このノートブックには、インテリジェントなハイパーパラメーター調整、モデル デプロイ、およびノートブックのウィジェットを示す展開済みセクションが含まれています。

## <a name="set-up-the-experiment"></a>実験を設定する

このセクションでは、必要な Python パッケージを読み込み、ワークスペースを初期化し、実験を作成し、トレーニング データとトレーニング スクリプトをアップロードすることで、トレーニング実験を設定します。

### <a name="import-packages"></a>パッケージをインポートする

まず、必要な Python ライブラリをインポートします。

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
from azureml.train.dnn import PyTorch
```

### <a name="initialize-a-workspace"></a>ワークスペースを初期化する

[Azure Machine Learning ワークスペース](concept-workspace.md)は、サービス用の最上位のリソースです。 作成されるすべての成果物を操作できる一元的な場所が用意されています。 Python SDK では、[`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) オブジェクトを作成することでワークスペースの成果物にアクセスできます。

[前提条件のセクション](#prerequisites)で作成した `config.json` ファイルからワークスペース オブジェクトを作成します。

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>ディープ ラーニングの実験を作成する

トレーニング スクリプトを保存するための実験とフォルダーを作成します。 この例では、"pytorch-birds" という実験を作成します。

```Python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-birds'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>データを取得する

データセットは、七面鳥と鶏のそれぞれについて約 120 のトレーニング画像で構成され、クラスごとに 100 の検証用画像があります。 トレーニング スクリプト `pytorch_train.py` の一部として、データセットをダウンロードして展開します。 画像は [Open Images v5 Dataset](https://storage.googleapis.com/openimages/web/index.html) のサブセットです。

### <a name="prepare-training-scripts"></a>トレーニング スクリプトを準備する

このチュートリアルでは、トレーニング スクリプトの `pytorch_train.py` は既に用意されています。 実際には、任意のカスタム トレーニング スクリプトをそのまま使用し、Azure Machine Learning で実行できます。

Pytorch トレーニング スクリプト `pytorch_train.py` をアップロードします。

```Python
shutil.copy('pytorch_train.py', project_folder)
```

ただし、Azure Machine Learning の追跡機能とメトリック機能を利用したい場合は、トレーニング スクリプト内に少量のコードを追加する必要があります。 メトリックの追跡の例は、`pytorch_train.py` にあります。

## <a name="create-a-compute-target"></a>コンピューティング ターゲットを作成する

PyTorch ジョブを実行するためのコンピューティング先を作成します。 この例では、GPU 対応の Azure Machine Learning コンピューティング クラスターを作成します。

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

コンピューティング先の詳細については、[コンピューティング先の概要](concept-compute-target.md)に関する記事を参照してください。

## <a name="create-a-pytorch-estimator"></a>PyTorch エスティメーターを作成する

[PyTorch エスティメーター](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)には、コンピューティング先で PyTorch トレーニング ジョブを起動する簡単な方法が用意されています。

PyTorch エスティメーターは、ジェネリック [`estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) クラスを介して実装されています。これは、任意のフレームワークをサポートするために使用できます。 ジェネリック エスティメーターを使用したモデルのトレーニングの詳細については、[エスティメーターを使用した Azure Machine Learning によるモデルのトレーニング](how-to-train-ml-models.md)に関するページを参照してください。

トレーニング スクリプトを実行するために追加の pip パッケージまたは conda パッケージが必要な場合は、`pip_packages` および `conda_packages` 引数に名前を渡すことで、パッケージを結果の docker イメージにインストールできます。

```Python
script_params = {
    '--num_epochs': 30,
    '--output_dir': './outputs'
}

estimator = PyTorch(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='pytorch_train.py',
                    use_gpu=True,
                    pip_packages=['pillow==5.4.1'])
```

## <a name="submit-a-run"></a>実行を送信する

[実行オブジェクト](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)には、ジョブの実行中および完了後の実行履歴へのインターフェイスが用意されています。

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

実行は、以下の段階を経て実施されます。

- **準備**:docker イメージは PyTorch エスティメーターに従って作成されます。 イメージはワークスペースのコンテナー レジストリにアップロードされ、後で実行するためにキャッシュされます。 ログは実行履歴にもストリーミングされ、進行状況を監視するために表示することができます。

- **拡大縮小**:Batch AI クラスターでの実行に現在使用可能な数より多くのノードが必要な場合、クラスターはスケールアップを試みます。

- **Running**: script フォルダー内のすべてのスクリプトがコンピューティング先にアップロードされ、データ ストアがマウントまたはコピーされ、entry_script が実行されます。 stdout および ./logs フォルダーの出力は実行履歴にストリーミングされ、実行を監視するために使用できます。

- **後処理**:実行の ./outputs フォルダーが実行履歴にコピーされます。

## <a name="register-or-download-a-model"></a>モデルを登録またはダウンロードする

モデルのトレーニングが終わったら、それをワークスペースに登録できます。 モデルの登録を使用すると、モデルをワークスペースに格納し、バージョン管理して、[モデルの管理とデプロイ](concept-model-management-and-deployment.md)を簡単にすることができます。

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

実行オブジェクトを使用してモデルのローカル コピーをダウンロードすることもできます。 トレーニング スクリプトの `pytorch_train.py` では、PyTorch 保存オブジェクトによってモデルがローカル フォルダー (コンピューティング先に対するローカル) に永続化されます。 実行オブジェクトを使用してコピーをダウンロードすることができます。

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>分散トレーニング

[`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) 推定器は、CPU と GPU のクラスターでの分散トレーニングもサポートしています。 分散 PyTorch ジョブは簡単に実行でき、オーケストレーションの管理は Azure Machine Learning service によって自動的に行われます。

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) は、Uber によって開発された、分散トレーニングのためのオープン ソースの all reduce フレームワークです。 これを利用すると、分散 GPU PyTorch ジョブを簡単に実行できます。

Horovod を使用するには、PyTorch コンストラクターの `distributed_training` パラメーターに [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) オブジェクトを指定します。 このパラメーターによって、トレーニング スクリプトで使用する Horovod ライブラリが確実にインストールされます。


```Python
from azureml.train.dnn import PyTorch

estimator= PyTorch(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True)
```
Horovod および依存関係にあるファイルが自動的にインストールされるので、次のようにトレーニング スクリプト `train.py` にインポートできます。

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>ONNX にエクスポートする

[ONNX Runtime](concept-onnx.md) での推論を最適化するために、トレーニングされた PyTorch モデルを ONNX 形式に変換します。 推論、つまりモデル スコアリングとは、通常は運用環境のデータに基づいて、デプロイしたモデルを使用して予測を行うフェーズです。 例については、[チュートリアル](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb)を参照してください。

## <a name="next-steps"></a>次の手順

この記事では、Azure Machine Learning で PyTorch を使用して、ディープ ラーニング ニューラル ネットワークをトレーニングして登録しました。 モデルをデプロイする方法を学習するには、モデル デプロイの記事に進んでください。

> [!div class="nextstepaction"]
> [モデルをデプロイする方法と場所](how-to-deploy-and-where.md)
* [トレーニング中に実行メトリクスを追跡する](how-to-track-experiments.md)
* [ハイパーパラメーターを調整する](how-to-tune-hyperparameters.md)
* [トレーニング済みモデルをデプロイする](how-to-deploy-and-where.md)
* [Azure での分散型ディープ ラーニング トレーニングの参照アーキテクチャ](/azure/architecture/reference-architectures/ai/training-deep-learning)
