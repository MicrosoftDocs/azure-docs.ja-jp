---
title: 強化学習モデルをトレーニングしてデプロイします (プレビュー)。
titleSuffix: Azure Machine Learning
description: Azure Machine Learning の強化学習 (プレビュー) を使用して、Pong をプレイするように RL エージェントをトレーニングする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: peterlu
author: peterclu
ms.date: 05/05/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q2
ms.openlocfilehash: 4c03016d003978b3c56361595bec7c559205574b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102520882"
---
# <a name="reinforcement-learning-preview-with-azure-machine-learning"></a>Azure Machine Learning での強化学習 (プレビュー)



> [!NOTE]
> Azure Machine Learning の強化学習は、現在はプレビュー機能です。 現時点では、Ray および RLlib フレームワークのみがサポートされています。

この記事では、ビデオ ゲームの Pong をプレイするように強化学習 (RL) エージェントをトレーニングする方法について説明します。 オープンソースの Python ライブラリ [Ray RLlib](https://ray.readthedocs.io/en/master/rllib.html) と Azure Machine Learning を使用して、分散 RL の複雑さを管理します。

この記事では、次の方法について説明します。
> [!div class="checklist"]
> * 実験を設定する
> * ヘッド ノードとワーカー ノードを定義する
> * RL 推定器を作成する
> * 実験を送信して実行を開始する
> * 結果の表示

この記事は、Azure Machine Learning ノートブックの [GitHub リポジトリ](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/reinforcement-learning/README.md)にある [RLlib Pong の例](https://aka.ms/azureml-rl-pong)が基になっています。

## <a name="prerequisites"></a>前提条件

このコードは、これらの環境のいずれかで実行してください。 最も高速な起動エクスペリエンスのため、Azure Machine Learning コンピューティング インスタンスを試すことをお勧めします。 Azure Machine Learning コンピューティング インスタンスで強化サンプルのノートブックをすばやく複製して実行できます。

 - Azure Machine Learning コンピューティング インスタンス

     - サンプルのノートブックを複製する方法を学習します ([環境とワークスペースの設定に関するチュートリアル](tutorial-1st-experiment-sdk-setup.md)を参照)。
         - **tutorials** ではなく **how-to-use-azureml** フォルダーを複製します
     - `/how-to-use-azureml/reinforcement-learning/setup/devenv_setup.ipynb` にある仮想ネットワーク セットアップ ノートブックを実行して、分散強化学習に使用されるネットワーク ポートを開きます。
     - サンプル ノートブック `/how-to-use-azureml/reinforcement-learning/atari-on-distributed-compute/pong_rllib.ipynb` を実行します
 
 - 独自の Jupyter Notebook サーバー

    - [Azure Machine Learning SDK](/python/api/overview/azure/ml/install) をインストールします。
    - [Azure Machine Learning RL SDK](/python/api/azureml-contrib-reinforcementlearning/) をインストールします: `pip install --upgrade azureml-contrib-reinforcementlearning`
    - [ワークスペース構成ファイル](how-to-configure-environment.md#workspace)を作成します。
    - 仮想ネットワークを実行して、分散強化学習に使用されるネットワーク ポートを開きます。


## <a name="how-to-train-a-pong-playing-agent"></a>Pong プレイ エージェントをトレーニングする方法

強化学習 (RL) は、実行することによって学習する機械学習のアプローチです。 他の機械学習手法では、入力データを受動的に取得し、その中のパターンを見つけることによって学習が行われますが、RL では、**トレーニング エージェント** を使用して、能動的に決定を行い、その結果から学習します。

このトレーニング エージェントは、**シミュレートされた環境** で Pong のプレイを学習します。 トレーニング エージェントは、パドルの上移動、下移動、または静止を、ゲームのすべてのフレームで決定します。 エージェントは、ゲームの状態 (画面の RGB 画像) を調べて決定を行います。

RL は、**報酬** を使用して、決定が成功したかどうかをエージェントに伝えます。 この例では、エージェントは、ポイントを獲得するとプラスの報酬を受け取り、相手にポイントが入るとマイナスの報酬を受け取ります。 多くの回数繰り返すと、トレーニング エージェントは、現在の状態に基づいて、予想される将来の報酬の合計が最適になるアクションを選択することを学習します。 RL でこの最適化を実行するには、**ディープ ニューラル ネットワーク** (DNN) を使用するのが一般的です。 

トレーニングは、エージェントがトレーニング エポックで平均報酬スコア 18 に達すると終了します。 これは、エージェントが、最大 21 回の対戦において平均 18 ポイント以上で対戦相手に勝ったことを意味します。

DNN のシミュレーションと再トレーニングを反復するプロセスは計算の負荷が高く、大量のデータが必要になります。 RL ジョブのパフォーマンスを向上させる方法の 1 つは、複数のトレーニング エージェントが同時に動作して学習できるように、**処理を並列化する** ことです。 ただし、分散 RL 環境の管理は複雑な作業になる可能性があります。

Azure Machine Learning には、これらの複雑さを管理して RL ワークロードをスケールアウトするためのフレームワークが用意されています。

## <a name="set-up-the-environment"></a>環境をセットアップする

次を実行して、ローカル RL 環境を設定します。
1. 必要な Python パッケージを読み込む
1. ワークスペースを初期化する
1. 実験の作成
1. 構成済みの仮想ネットワークを指定する。

### <a name="import-libraries"></a>ライブラリのインポート

この例の残りの部分を実行するために必要な Python パッケージをインポートします。

```python
# Azure ML Core imports
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
from azureml.core.runconfig import EnvironmentDefinition
from azureml.widgets import RunDetails
from azureml.tensorboard import Tensorboard

# Azure ML Reinforcement Learning imports
from azureml.contrib.train.rl import ReinforcementLearningEstimator, Ray
from azureml.contrib.train.rl import WorkerConfiguration
```

### <a name="initialize-a-workspace"></a>ワークスペースを初期化する

[前提条件セクション](#prerequisites)で作成した `config.json` ファイルから[ワークスペース](concept-workspace.md) オブジェクトを初期化します。 このコードを Azure Machine Learning コンピューティング インスタンスで実行している場合、構成ファイルは既に作成されています。

```Python
ws = Workspace.from_config()
```

### <a name="create-a-reinforcement-learning-experiment"></a>強化学習の実験を作成する

強化学習の実行を追跡するための[実験](/python/api/azureml-core/azureml.core.experiment.experiment)を作成します。 Azure Machine Learning では、実験は、実行ログ、履歴、出力などを整理するための、関連する試行の論理的なコレクションです。

```python
experiment_name='rllib-pong-multi-node'

exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="specify-a-virtual-network"></a>仮想ネットワークを指定する

複数のコンピューティング先を使用する RL ジョブの場合、ワーカー ノードとヘッド ノードが相互に通信できるようにポートが開かれている仮想ネットワークを指定する必要があります。

仮想ネットワークはどのリソース グループに作成してもかまいませんが、ワークスペースと同じリージョンに存在する必要があります。 仮想ネットワークの設定の詳細については、前提条件セクションで示されているワークスペース セットアップ ノートブックを参照してください。 ここでは、リソース グループ内で仮想ネットワークの名前を指定します。

```python
vnet = 'your_vnet'
```

## <a name="define-head-and-worker-compute-targets"></a>ヘッドとワーカーのコンピューティング先を定義する

この例では、Ray のヘッド ノードとワーカー ノードに対して個別のコンピューティング先を使用します。 これらの設定を使用すると、自分のワークロードに応じて、コンピューティング リソースをスケールアップおよびスケールダウンできます。 ニーズに応じて、ノードの数と各ノードのサイズを設定します。

### <a name="head-computing-target"></a>ヘッド コンピューティング先

GPU が搭載されたヘッド クラスターを使用して、ディープ ラーニングのパフォーマンスを向上させることができます。 ヘッド ノードでは、エージェントが決定を行うために使用するニューラル ネットワークがトレーニングされます。 また、ヘッド ノードでは、ワーカー ノードからデータ ポイントが収集されて、ニューラル ネットワークがトレーニングされます。

ヘッド コンピューティングでは、1 つの [`STANDARD_NC6` 仮想マシン](../virtual-machines/nc-series.md) (VM) が使用されます。 これには、作業を分散させるために 6 つの仮想 CPU があります。


```python
from azureml.core.compute import AmlCompute, ComputeTarget

# choose a name for the Ray head cluster
head_compute_name = 'head-gpu'
head_compute_min_nodes = 0
head_compute_max_nodes = 2

# This example uses GPU VM. For using CPU VM, set SKU to STANDARD_D2_V2
head_vm_size = 'STANDARD_NC6'

if head_compute_name in ws.compute_targets:
    head_compute_target = ws.compute_targets[head_compute_name]
    if head_compute_target and type(head_compute_target) is AmlCompute:
        print(f'found head compute target. just use it {head_compute_name}')
else:
    print('creating a new head compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = head_vm_size,
                                                                min_nodes = head_compute_min_nodes, 
                                                                max_nodes = head_compute_max_nodes,
                                                                vnet_resourcegroup_name = ws.resource_group,
                                                                vnet_name = vnet_name,
                                                                subnet_name = 'default')

    # create the cluster
    head_compute_target = ComputeTarget.create(ws, head_compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    head_compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(head_compute_target.get_status().serialize())
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="worker-computing-cluster"></a>ワーカー コンピューティング クラスター

この例では、ワーカー コンピューティング先に 4 つの [`STANDARD_D2_V2` VM](../virtual-machines/nc-series.md) を使用します。 各ワーカー ノードでは 2 つの CPU を使用でき、全部で 8 つの CPU を使用できます。

ワーカー ノードではディープ ラーニングは実行されないため、GPU は必要ありません。 ワーカーでは、ゲームのシミュレーションが実行されて、データが収集されます。

```python
# choose a name for your Ray worker cluster
worker_compute_name = 'worker-cpu'
worker_compute_min_nodes = 0 
worker_compute_max_nodes = 4

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
worker_vm_size = 'STANDARD_D2_V2'

# Create the compute target if it hasn't been created already
if worker_compute_name in ws.compute_targets:
    worker_compute_target = ws.compute_targets[worker_compute_name]
    if worker_compute_target and type(worker_compute_target) is AmlCompute:
        print(f'found worker compute target. just use it {worker_compute_name}')
else:
    print('creating a new worker compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = worker_vm_size,
                                                                min_nodes = worker_compute_min_nodes, 
                                                                max_nodes = worker_compute_max_nodes,
                                                                vnet_resourcegroup_name = ws.resource_group,
                                                                vnet_name = vnet_name,
                                                                subnet_name = 'default')

    # create the cluster
    worker_compute_target = ComputeTarget.create(ws, worker_compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    worker_compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(worker_compute_target.get_status().serialize())
```

## <a name="create-a-reinforcement-learning-estimator"></a>強化学習の推定器を作成する
[ReinforcementLearningEstimator](/python/api/azureml-contrib-reinforcementlearning/azureml.contrib.train.rl.reinforcementlearningestimator) を使用して、Azure Machine Learning にトレーニング ジョブを送信します。

Azure Machine Learning では、推定器クラスを使用して、実行の構成情報がカプセル化されます。 これにより、スクリプトの実行の構成方法を指定できます。 

### <a name="define-a-worker-configuration"></a>ワーカーの構成を定義する

WorkerConfiguration オブジェクトでは、エントリ スクリプトを実行するワーカー クラスターを初期化する方法が、Azure Machine Learning に指示されます。

```python
# Pip packages we will use for both head and worker
pip_packages=["ray[rllib]==0.8.3"] # Latest version of Ray has fixes for isses related to object transfers

# Specify the Ray worker configuration
worker_conf = WorkerConfiguration(
    
    # Azure ML compute cluster to run Ray workers
    compute_target=worker_compute_target, 
    
    # Number of worker nodes
    node_count=4,
    
    # GPU
    use_gpu=False, 
    
    # PIP packages to use
    pip_packages=pip_packages
)
```

### <a name="define-script-parameters"></a>スクリプトのパラメーターを定義する

エントリ スクリプト `pong_rllib.py` は、トレーニング ジョブの実行方法を定義するパラメーターのリストを受け取ります。 これらのパラメーターをカプセル化のレイヤーとしての推定器を通して渡すことで、スクリプトのパラメーターと実行の構成を相互に独立して簡単に変更できます。

適切な `num_workers` を指定すると、並列処理を最大限に活用できます。 ワーカーの数を、使用可能な CPU の数と同じに設定します。 この例では、次の計算を使用できます。

ヘッド ノードは、6 つの仮想 CPUを備えた [Standard_NC6](../virtual-machines/nc-series.md) です。 ワーカー クラスターは、それぞれが 2 つの CPU を備える 4 つの [Standard_D2_V2 VM](../cloud-services/cloud-services-sizes-specs.md#dv2-series) であり、CPU は合計で 8 つです。 ただし、1 つの CPU はヘッド ノード ロール専用にする必要があるため、ワーカーの CPU 数から 1 つ減らす必要があります。

6 CPU + 8 CPU - 1 ヘッド CPU = 13 同時ワーカー。 Azure Machine Learning では、ヘッド クラスターとワーカー クラスターを使用して、コンピューティング リソースが区別されます。 ただし、Ray ではヘッドとワーカーは区別されず、すべての CPU はワーカー スレッドとして使用できます。


```python
training_algorithm = "IMPALA"
rl_environment = "PongNoFrameskip-v4"

# Training script parameters
script_params = {
    
    # Training algorithm, IMPALA in this case
    "--run": training_algorithm,
    
    # Environment, Pong in this case
    "--env": rl_environment,
    
    # Add additional single quotes at the both ends of string values as we have spaces in the 
    # string parameters, outermost quotes are not passed to scripts as they are not actually part of string
    # Number of GPUs
    # Number of ray workers
    "--config": '\'{"num_gpus": 1, "num_workers": 13}\'',
    
    # Target episode reward mean to stop the training
    # Total training time in seconds
    "--stop": '\'{"episode_reward_mean": 18, "time_total_s": 3600}\'',
}
```

### <a name="define-the-reinforcement-learning-estimator"></a>強化学習の推定器を定義する

パラメーター リストとワーカー構成オブジェクトを使用して、推定器を作成します。

```python
# RL estimator
rl_estimator = ReinforcementLearningEstimator(
    
    # Location of source files
    source_directory='files',
    
    # Python script file
    entry_script="pong_rllib.py",
    
    # Parameters to pass to the script file
    # Defined above.
    script_params=script_params,
    
    # The Azure ML compute target set up for Ray head nodes
    compute_target=head_compute_target,
    
    # Pip packages
    pip_packages=pip_packages,
    
    # GPU usage
    use_gpu=True,
    
    # RL framework. Currently must be Ray.
    rl_framework=Ray(),
    
    # Ray worker configuration defined above.
    worker_configuration=worker_conf,
    
    # How long to wait for whole cluster to start
    cluster_coordination_timeout_seconds=3600,
    
    # Maximum time for the whole Ray job to run
    # This will cut off the run after an hour
    max_run_duration_seconds=3600,
    
    # Allow the docker container Ray runs in to make full use
    # of the shared memory available from the host OS.
    shm_size=24*1024*1024*1024
)
```

### <a name="entry-script"></a>エントリ スクリプト。

[エントリ スクリプト](https://aka.ms/azure-rl-pong-script) `pong_rllib.py` では、[OpenAI Gym 環境](https://github.com/openai/gym/) `PongNoFrameSkip-v4` を使用して、ニューラル ネットワークがトレーニングされます。 OpenAI Gym は、従来の Atari ゲームで強化学習アルゴリズムをテストするための、標準化されたインターフェイスです。

この例では、[IMPALA](https://arxiv.org/abs/1802.01561) (Importance Weighted Actor-Learner Architecture: 重要度が重み付けされたアクター ラーナー アーキテクチャ) と呼ばれるトレーニング アルゴリズムを使用します。 IMPALA では、速度や安定性を犠牲にすることなく、個々の学習アクターが並列化されて、多数のコンピューティング ノードにスケーリングできます。

[Ray Tune](https://ray.readthedocs.io/en/latest/tune.html) によって、IMPALA のワーカー タスクが調整されます。

```python
import ray
import ray.tune as tune
from ray.rllib import train

import os
import sys

from azureml.core import Run
from utils import callbacks

DEFAULT_RAY_ADDRESS = 'localhost:6379'

if __name__ == "__main__":

    # Parse arguments
    train_parser = train.create_parser()

    args = train_parser.parse_args()
    print("Algorithm config:", args.config)

    if args.ray_address is None:
        args.ray_address = DEFAULT_RAY_ADDRESS

    ray.init(address=args.ray_address)

    tune.run(run_or_experiment=args.run,
             config={
                 "env": args.env,
                 "num_gpus": args.config["num_gpus"],
                 "num_workers": args.config["num_workers"],
                 "callbacks": {"on_train_result": callbacks.on_train_result},
                 "sample_batch_size": 50,
                 "train_batch_size": 1000,
                 "num_sgd_iter": 2,
                 "num_data_loader_buffers": 2,
                 "model": {
                    "dim": 42
                 },
             },
             stop=args.stop,
             local_dir='./logs')
```

### <a name="logging-callback-function"></a>ログ記録コールバック関数


エントリ スクリプトでは、ユーティリティ関数を使用して[カスタム RLlib コールバック関数](https://docs.ray.io/en/latest/rllib-training.html#callbacks-and-custom-metrics)を定義し、Azure Machine Learning ワークスペースにメトリックを記録します。 これらのメトリックを表示する方法については、「[結果を監視および表示する](#monitor-and-view-results)」セクションを参照してください。

```python
'''RLlib callbacks module:
    Common callback methods to be passed to RLlib trainer.
'''
from azureml.core import Run

def on_train_result(info):
    '''Callback on train result to record metrics returned by trainer.
    '''
    run = Run.get_context()
    run.log(
        name='episode_reward_mean',
        value=info["result"]["episode_reward_mean"])
    run.log(
        name='episodes_total',
        value=info["result"]["episodes_total"])
```

## <a name="submit-a-run"></a>実行を送信する

[Run](/python/api/azureml-core/azureml.core.run%28class%29) では、進行中のジョブまたは完了したジョブの実行履歴が処理されます。 

```python
run = exp.submit(config=rl_estimator)
```
> [!NOTE]
> 実行の完了までには、最大で 30 から 45 分かかることがあります。

## <a name="monitor-and-view-results"></a>結果を監視および表示する

Azure Machine Learning の Jupyter ウィジェットを使用して、実行の状態をリアルタイムで確認します。 このウィジェットには、2 つの子実行が表示されます。1 つはヘッドのもので、もう 1 つはワーカーのものです。 

```python
from azureml.widgets import RunDetails

RunDetails(run).show()
run.wait_for_completion()
```

1. ウィジェットが読み込まれるまで待ちます。
1. 実行の一覧でヘッドの実行を選択します。

スタジオで実行の追加情報を表示するには、 **[Click here to see the run in Azure Machine Learning studio]\(Azure Machine Learning スタジオで実行を表示するには、ここをクリックしてください\)** を選択します。 この情報には、実行の進行中または完了後にアクセスできます。

![実行の詳細を示す折れ線グラフのウィジェット](./media/how-to-use-reinforcement-learning/pong-run-details-widget.png)

**episode_reward_mean** プロットは、トレーニング エポックあたりに獲得した平均ポイント数を示します。 最初、トレーニング エージェントはプレイが下手で、1 ポイントも獲得できずに対戦に負けています (-21 という episode_reward_mean で示されています)。 繰り返しが 100 回になる前に、トレーニング エージェントは、平均 18 ポイントでコンピューターの相手を破ることを学習しました。

子実行のログを参照すると、評価結果が driver_log.txt ファイルに記録されていることがわかります。 [実行] ページでこれらのメトリックが利用可能になるまで、数分待つことが必要な場合があります。

これで、複数のコンピューティング リソースを構成し、コンピューターの対戦相手に対して Pong を非常にうまくプレイするように強化学習エージェントをトレーニングする方法を学習しました。

## <a name="next-steps"></a>次のステップ

この記事では、IMPALA 学習エージェントを使用して強化学習エージェントをトレーニングする方法について学習しました。 その他の例については、[Azure Machine Learning 強化学習の GitHub リポジトリ](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/reinforcement-learning/README.md)を参照してください。