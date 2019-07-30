---
title: Estimator を使用して ML モデルをトレーニングする
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service の Estimator クラスを使用して従来の機械学習モデルとディープ ラーニング モデルを単一ノードおよび分散トレーニングする方法を説明します
ms.author: minxia
author: mx-iao
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 04/19/2019
ms.custom: seodec18
ms.openlocfilehash: 689d7dcd57c513479c7bc08a45094670242ef6a5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075028"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Azure Machine Learning で Estimator を使用してモデルをトレーニングする

Azure Machine Learning では、[RunConfiguration オブジェクト](how-to-set-up-training-targets.md#whats-a-run-configuration)と [ScriptRunConfig オブジェクト](how-to-set-up-training-targets.md#submit)を使用して、トレーニング スクリプトを[さまざまなコンピューティング先](how-to-set-up-training-targets.md#compute-targets-for-training)に簡単に送信できます。 このパターンを利用して、高い柔軟性と最大のコントロールを実現できます。

ディープ ラーニング モデルのトレーニングを容易にするために、Azure Machine Learning の Python SDK には代替の高度な抽象化である Estimator クラスが用意されています。このクラスを利用すると、実行構成を簡単に構築できます。 汎用の [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) を作成して使用して、選択した任意のラーニング フレームワーク (scikit-learn など) を使用してトレーニング スクリプトを送信し、任意のコンピューティング先 (ローカル マシン、Azure 内の単一の VM、Azure 内の GPU クラスターなど) で実行することができます。 PyTorch、TensorFlow、Chainer タスクの場合、Azure Machine Learning には、それぞれに [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)、[TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)、[Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) Estimator が用意されており、これらのフレームワークを簡単に使用できます。

## <a name="train-with-an-estimator"></a>Estimator でトレーニングする

[ワークスペース](concept-workspace.md)を作成し、[開発環境](how-to-configure-environment.md)を設定したら、Azure Machine Learning でのモデルのトレーニングは次の手順で構成されます。  
1. [リモートのコンピューティング先](how-to-set-up-training-targets.md)を作成する (ローカル コンピューターをコンピューティング先として使用することもできる点に注意してください)
2. [トレーニング データ](how-to-access-data.md)をデータストアにアップロードする (省略可能)
3. [トレーニング スクリプト](tutorial-train-models-with-aml.md#create-a-training-script)を作成する
4. `Estimator` オブジェクトを作成する
5. ワークスペースの実験オブジェクトに Estimator を送信する

この記事では、手順 4 と 5 を説明します。 手順 1 から 3 については、[モデルのトレーニングに関するチュートリアル](tutorial-train-models-with-aml.md)の例を参照してください。

### <a name="single-node-training"></a>単一ノードのトレーニング

scikit 学習モデルについては、Azure のリモート コンピューティングで実行される単一ノード トレーニングに `Estimator` を使用します。 [コンピューティング ターゲット](how-to-set-up-training-targets.md#amlcompute) オブジェクト `compute_target` および[データストア](how-to-access-data.md) オブジェクト `ds` は、既に作成されている必要があります。

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])
```

このコード スニペットは、`Estimator` コンストラクターに次のパラメーターを指定します。

パラメーター | 説明
--|--
`source_directory`| トレーニング ジョブに必要なコードのすべてが含まれているローカル ディレクトリ。 このフォルダーは、ローカル コンピューターからリモート コンピューティングにコピーされています 
`script_params`| <コマンドライン引数, 値> ペアの形式で、トレーニング スクリプト `entry_script` にコマンドライン引数を指定するディクショナリ。 `script_params` で詳細フラグを指定するには、`<command-line argument, "">` を使用します。
`compute_target`| トレーニング スクリプトの実行に使用するリモートのコンピューティング先 (この例では Azure Machine Learning コンピューティング ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)) クラスター) (AmlCompute クラスターが共通して使用されているコンピューティング先であっても、Azure VM やローカル コンピューターなど、他のコンピューティング先の種類を選択することもできる点に注意してください)。
`entry_script`| リモート コンピューティングで実行するトレーニング スクリプトのファイルパス (`source_directory` を基準にした相対パス)。 このファイル、およびこのファイルと依存関係があるその他のファイルはすべて、このフォルダーに置かれている必要があります
`conda_packages`| トレーニング スクリプトで必要な、conda を使用してインストールする Python パッケージのリスト。  

コンストラクターには `pip_packages` という名前の別のパラメーターもあり、必要な pip パッケージに対して使用します

ご自分の `Estimator` オブジェクトを作成すると、[実験](concept-azure-machine-learning-architecture.md#experiments)オブジェクト `experiment` で `submit` 関数を呼び出すことで、リモート コンピューティングで実行するトレーニング ジョブを送信します。 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **特殊フォルダー** *outputs* および *logs* の 2 つのフォルダーは、Azure Machine Learning によって特別に扱われます｡ トレーニング中に、ルート ディレクトリを基準にした *outputs* と *logs* というフォルダー (それぞれ `./outputs` と `./logs`) にファイルを書き込んだ場合、それらのファイルは自動的に実行履歴にアップロードされて、実行が完了すると、それらのファイルにアクセスできるようになります。
>
> トレーニング中に成果物 (モデル ファイル、チェックポイント、データ ファイル、プロット画像など) を作成するには、`./outputs` フォルダーにそれら成果物を書き込みます。
>
> 同様に、トレーニング実行のログを `./logs` フォルダーに書き込むこともできます。 Azure Machine Learning の [ TensorBoard 統合](https://aka.ms/aml-notebook-tb)を利用するには､このフォルダーに TensorBoard のログを書き込むようにします｡ 実行中に、TensorBoard を起動して、それらログをストリーミングできます｡  過去のどの実行についても､後でログを復元することができます。
>
> たとえば､リモート トレーニングの実行後に *outputs*フォルダーに書き込まれたファイルをローカル コンピューターには､次のコードを使用します。 `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>分散トレーニングとカスタム Docker イメージ

`Estimator` を使用して実行できるトレーニング シナリオは他にも 2 つあります。
* カスタム Docker イメージを使用する
* マルチノード クラスターで分散トレーニングする

次のコードでは、Keras モデルに対して分散トレーニングを実行する方法を示しています。 また、既定の Azure Machine Learning イメージを使わずに、トレーニングに Docker Hub `continuumio/miniconda` のカスタム Docker イメージを指定します。

[コンピューティング ターゲット](how-to-set-up-training-targets.md#amlcompute)オブジェクト`compute_target`は作成済みである必要があります｡ Estimator は次のようにして作成できます。

```Python
from azureml.train.estimator import Estimator

estimator = Estimator(source_directory='./my-keras-proj',
                      compute_target=compute_target,
                      entry_script='train.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_backend='mpi',     
                      conda_packages=['tensorflow', 'keras'],
                      custom_docker_base_image='continuumio/miniconda')
```

上記のコードでは、`Estimator` コンストラクターに対して次の新しいパラメーターを公開しています。

パラメーター | 説明 | 既定値
--|--|--
`custom_docker_base_image`| 使用するイメージの名前。 パブリックな Docker リポジトリ (この場合は Docker Hub) にあるイメージのみを指定します。 プライベートな docker リポジトリにあるイメージを使用するには、コンストラクターの `environment_definition` パラメーターを利用します｡ [例を参照](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)してください。 | `None`
`node_count`| トレーニング ジョブに使用するノードの数。 | `1`
`process_count_per_node`| 各ノードで実行するプロセス (つまり "worker") の数。 この場合は、ノード 1 つあたり `2` 個の GPU を使用します｡| `1`
`distributed_backend`| 分散トレーニングを起動するためのバックエンド。Estimator によって MPI 経由で提供されます。  並列または分散トレーニングを実行するには (例えば `node_count`>1 か `process_count_per_node`>1、またはその両方)､`distributed_backend='mpi'` を設定します。 AML が使用する MPI 実装は [Open MPI](https://www.open-mpi.org/) です｡| `None`

最後に、トレーニング ジョブを送信します。
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="github-tracking-and-integration"></a>GitHub の追跡と統合

ソース ディレクトリがローカル Git リポジトリであるトレーニング実行を開始すると、リポジトリに関する情報が実行履歴に格納されます。 たとえば、リポジトリの現在のコミット ID が履歴の一部としてログに記録されます。

## <a name="examples"></a>例
Estimator パターンの基本がわかるノートブックについては、次のページを参照してください。
* [how-to-use-azureml/training-with-deep-learning/how-to-use-estimator](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

Estimator を使用して scikit-learn モデルをトレーニングするノートブックについては、次のページを参照してください。
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

deep-learning-framework 固有の Estimator を使用してモデルをトレーニングするノートブックについては、次のページを参照してください。
* [how-to-use-azureml/training-with-deep-learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>次の手順

* [トレーニング中に実行メトリクスを追跡する](how-to-track-experiments.md)
* [PyTorch モデルをトレーニングする](how-to-train-pytorch.md)
* [TensorFlow モデルをトレーニングする](how-to-train-tensorflow.md)
* [ハイパーパラメーターを調整する](how-to-tune-hyperparameters.md)
* [トレーニング済みモデルをデプロイする](how-to-deploy-and-where.md)
