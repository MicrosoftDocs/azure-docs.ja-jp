---
title: トレーニングの実行を構成する
titleSuffix: Azure Machine Learning
description: さまざまなトレーニング環境 (コンピューティング先) で機械学習モデルをトレーニングします。 トレーニング環境を簡単に切り替えることができます。
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: f38fe7d847754247f8c1510527b3ffe026c20be5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102518502"
---
# <a name="configure-and-submit-training-runs"></a>トレーニングの実行を構成して送信する

この記事では、モデルをトレーニングするために Azure Machine Learning の実行を構成して送信する方法について説明します。 コード スニペットでは、トレーニング スクリプトの構成と送信の主要な部分について説明します。  その後、[ノートブックの例](#notebooks)のいずれかを使用して、完全なエンドツーエンドの作業例を見つけます。

トレーニングの場合は、ローカル コンピューター上で開始し、後でクラウドベースのクラスターにスケール アウトするのが一般的です。 Azure Machine Learning では、トレーニング スクリプトを変更しなくても、さまざまなコンピューティング先でスクリプトを実行できます。

必要なのは、**スクリプト実行構成** 内で各コンピューティング先の環境を定義することだけです。  その後、異なるコンピューティング先でトレーニング実験を実行するときは、そのコンピューティングの実行構成を指定します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を試してください
* [Azure Machine Learning SDK for Python](/python/api/overview/azure/ml/install) (1.13.0 以降)
* [Azure Machine Learning ワークスペース](how-to-manage-workspace.md)、`ws`
* コンピューティング先、`my_compute_target`。  [コンピューティング ターゲットを作成する](how-to-create-attach-compute-studio.md) 

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>スクリプト実行構成とは
[ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) は、トレーニングの実行を実験の一部として送信するのに必要な情報を構成するために使用されます。

ScriptRunConfig オブジェクトを使用して、トレーニング実験を送信します。  このオブジェクトには以下のものが含まれます。

* **source_directory**:トレーニング スクリプトが格納されているソース ディレクトリ
* **script**:実行するトレーニング スクリプト
* **compute_target**: 実行が行われるコンピューティング ターゲット
* **環境**: スクリプトを実行する場合に使用する環境
* および、いくつかの構成可能な追加のオプション (詳細については、該当する[リファレンス ドキュメント](/python/api/azureml-core/azureml.core.scriptrunconfig)を参照してください)

## <a name="train-your-model"></a><a id="submit"></a>モデルをトレーニングする

トレーニングの実行を送信するためのコード パターンは、すべての種類のコンピューティング先について同じです。

1. 実行する実験を作成する
1. スクリプトが実行される環境を作成する
1. コンピューティング ターゲットと環境を指定する ScriptRunConfig を作成する
1. 実行の送信
1. 実行が完了するまで待機する

または、次のことができます。

* [ハイパーパラメーターのチューニング](how-to-tune-hyperparameters.md)用の HyperDrive 実行を送信します。
* [VS Code 拡張機能](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model)を介して実験を送信します。

## <a name="create-an-experiment"></a>実験の作成

ご自分のワークスペース内に実験を作成します。

```python
from azureml.core import Experiment

experiment_name = 'my_experiment'
experiment = Experiment(workspace=ws, name=experiment_name)
```

## <a name="select-a-compute-target"></a>コンピューティング ターゲットを選択する

トレーニング スクリプトを実行するコンピューティング ターゲットを選択します。 ScriptRunConfig でコンピューティング ターゲットが指定されていない場合、または `compute_target='local'` である場合は、Azure ML によってスクリプトがローカルで実行されます。 

この記事のコード例では、「前提条件」セクションのコンピューティング ターゲット `my_compute_target` が既に作成されていることを前提としています。

>[!Note]
>Azure Databricks は、モデル トレーニングのコンピューティング先としてサポートされていません。 データ準備およびデプロイのタスクには Azure Databricks を使用できます。 

## <a name="create-an-environment"></a>環境の作成
Azure Machine Learning [環境](concept-environments.md)は、機械学習トレーニングが行われる環境をカプセル化したものです。 そこでは、トレーニングとスコアリングのスクリプトに関連する、Python パッケージ、Docker イメージ、環境変数、およびソフトウェア設定が指定されます。 また、実行時間 (Python、Spark、または Docker) も指定されます。

独自の環境を定義することも、Azure ML のキュレーションされた環境を使用することもできます。 [キュレーションされた環境](./how-to-use-environments.md#use-a-curated-environment)とは、ワークスペース内で既定で使用できる定義済みの環境です。 これらの環境は、キャッシュされた Docker イメージでバックアップされ、実行の準備コストを下げます。 利用可能なキュレーション環境の完全な一覧については、「[Azure Machine Learning のキュレーションされた環境](./resource-curated-environments.md)」を参照してください。

リモート コンピューティング先の場合は、一般的なキュレーション環境のいずれかを使用して開始できます。

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="AzureML-Minimal")
```

環境の詳細については、「[Azure Machine Learning でソフトウェア環境を作成して使用する](how-to-use-environments.md)」をご覧ください。
  
### <a name="local-compute-target"></a><a name="local"></a>ローカル コンピューティング先

コンピューティング先が **ローカル コンピューター** である場合は、スクリプトが実行される Python 環境で、必要なすべてのパッケージが使用できることをユーザー自身で確認する必要があります。  `python.user_managed_dependencies` を使用すると、現在の Python 環境 (または指定したパス上の Python) を使用できます。

```python
from azureml.core import Environment

myenv = Environment("user-managed-env")
myenv.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
# myenv.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
```

## <a name="create-the-script-run-configuration"></a>スクリプトの実行構成を作成する

コンピューティング先 (`my_compute_target`) と環境 (`myenv`) が用意できたので、`project_folder` ディレクトリにあるトレーニング スクリプト (`train.py`) を実行するスクリプト実行構成を作成します。

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='train.py',
                      compute_target=my_compute_target,
                      environment=myenv)

# Set compute target
# Skip this if you are running on your local computer
script_run_config.run_config.target = my_compute_target
```

環境を指定しないと、既定の環境が自動的に作成されます。

トレーニング スクリプトに渡したいコマンドライン引数を用意している場合は、ScriptRunConfig コンストラクターの **`arguments`** パラメーター (`arguments=['--arg1', arg1_val, '--arg2', arg2_val]`など) を使用してそれらを指定することができます。

実行に対する既定の最大許容時間をオーバーライドしたい場合は、 **`max_run_duration_seconds`** パラメーターを使用してそれを行うことができます。 この値よりも時間がかかる場合は、システムによって自動的に実行のキャンセルが試みられます。

### <a name="specify-a-distributed-job-configuration"></a>分散ジョブの構成を指定する
分散トレーニング ジョブを実行する場合は、分散ジョブ固有の構成を **`distributed_job_config`** パラメーターに指定します。 サポートされている構成の種類には、[MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration)、[TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration)、および [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration) があります。 

Horovod、TensorFlow、PyTorch の各分散ジョブの実行の詳細と例については、以下を参照してください。

* [TensorFlow モデルをトレーニングする](./how-to-train-tensorflow.md#distributed-training)
* [PyTorch モデルをトレーニングする](./how-to-train-pytorch.md#distributed-training)

## <a name="submit-the-experiment"></a>実験を送信する

```python
run = experiment.submit(config=src)
run.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> トレーニングの実行を送信すると、トレーニング スクリプトが含まれるディレクトリのスナップショットが作成されて、コンピューティング先に送信されます。 スナップショットは、ご利用のワークスペースにも実験の一部として保存されます。 ファイルを変更して実行を再度送信すると、変更したファイルのみがアップロードされます。
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> スナップショットの詳細については、「[スナップショット](concept-azure-machine-learning-architecture.md#snapshots)」をご覧ください。

> [!IMPORTANT]
> **特殊フォルダー** *outputs* および *logs* の 2 つのフォルダーは、Azure Machine Learning によって特別に扱われます｡ トレーニング中に、ルート ディレクトリを基準にした *outputs* と *logs* というフォルダー (それぞれ `./outputs` と `./logs`) にファイルを書き込んだ場合、それらのファイルは自動的に実行履歴にアップロードされて、実行が完了すると、それらのファイルにアクセスできるようになります。
>
> トレーニング中に成果物 (モデル ファイル、チェックポイント、データ ファイル、プロット画像など) を作成するには、`./outputs` フォルダーにそれら成果物を書き込みます。
>
> 同様に、トレーニング実行のログを `./logs` フォルダーに書き込むこともできます。 Azure Machine Learning の [ TensorBoard 統合](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/tensorboard/export-run-history-to-tensorboard/export-run-history-to-tensorboard.ipynb)を利用するには､このフォルダーに TensorBoard のログを書き込むようにします｡ 実行中に、TensorBoard を起動して、それらログをストリーミングできます｡  過去のどの実行についても､後でログを復元することができます。
>
> たとえば､リモート トレーニングの実行後に *outputs* フォルダーに書き込まれたファイルをローカル コンピューターには､次のコードを使用します。 `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

## <a name="git-tracking-and-integration"></a><a id="gitintegration"></a>Git の追跡と統合

ソース ディレクトリがローカル Git リポジトリであるトレーニング実行を開始すると、リポジトリに関する情報が実行履歴に格納されます。 詳細については、「[Azure Machine Learning との Git 統合](concept-train-model-git-integration.md)」を参照してください。

## <a name="notebook-examples"></a><a name="notebooks"></a>ノートブックの例

さまざまなトレーニング シナリオでの実行の構成例については、次のノートブックを参照してください。
* [さまざまなコンピューティング ターゲット上でのトレーニング](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [ML フレームワークを使用したトレーニング](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="troubleshooting"></a>トラブルシューティング

* **実行が `jwt.exceptions.DecodeError` で失敗する**:正確なエラー メッセージ: `jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()`。 
    
    最新バージョンの azureml-core へのアップグレードを検討してください: `pip install -U azureml-core`。
    
    ローカル実行でこの問題が発生している場合は、実行を開始する環境にインストールされている PyJWT のバージョンを確認してください。 サポートされている PyJWT のバージョンは、2.0.0 より前です。 バージョンが 2.0.0 以降の場合は、環境から PyJWT をアンインストールします。 PyJWT のバージョンを確認し、アンインストールして適切なバージョンをインストールするには、次のようにします。
    1. コマンド シェルを起動し、azureml-core がインストールされている Conda 環境をアクティブにします。
    2. `pip freeze` を入力して `PyJWT`を探します。見つかった場合は、表示されるバージョンは 2.0.0 未満になるはずです。
    3. 表示されているバージョンがサポート対象のバージョンでない場合は、コマンド シェルで `pip uninstall PyJWT` を入力し、確認のために「y」を入力します。
    4. `pip install 'PyJWT<2.0.0'` を使用してインストールする
    
    ユーザーが作成した環境を実行で送信する場合は、その環境で azureml-core の最新バージョンを使用することを検討してください。 azureml-core バージョン 1.18.0 以降では、既に 2.0.0 より前の PyJWT が固定されています。 送信する環境で 1.18.0 より前のバージョンの azureml-core を使用する必要がある場合は、pip 依存関係で 2.0.0 より前の PyJWT を必ず指定してください。


 * **ModuleErrors (モジュール名が指定されていない)** :Azure ML で実験を送信する際に ModuleErrors が発生した場合、トレーニング スクリプトではパッケージがインストールされていることを期待しているのに、それが追加されていません。 パッケージ名を指定すると、Azure ML により、トレーニングの実行に使用される環境にパッケージがインストールされます。

    Estimator を使用して実験を送信する場合は、パッケージのインストール元に基づく Estimator 内の `pip_packages` または `conda_packages` パラメータ―を使って、パッケージ名を指定できます。 また、`conda_dependencies_file` を使用してすべての依存関係を含む yml ファイルを指定したり、`pip_requirements_file` パラメーターを使用して txt ファイル内のすべての pip 要件を一覧表示したりすることも可能です。 Estimator で使用される既定のイメージをオーバーライドする独自の Azure ML 環境オブジェクトがある場合は、Estimator コンストラクターの `environment` パラメーターを使用してその環境を指定できます。
    
    Azure ML によって保守される docker イメージとそのコンテンツは、[AzureML のコンテナー](https://github.com/Azure/AzureML-Containers)内で確認できます。
    フレームワーク固有の依存関係は、それぞれのフレームワークのドキュメントに示されています。
    *  [Chainer](/python/api/azureml-train-core/azureml.train.dnn.chainer#remarks)
    * [PyTorch](/python/api/azureml-train-core/azureml.train.dnn.pytorch#remarks)
    * [TensorFlow](/python/api/azureml-train-core/azureml.train.dnn.tensorflow#remarks)
    *  [SKLearn](/python/api/azureml-train-core/azureml.train.sklearn.sklearn#remarks)
    
    > [!Note]
    > 特定のパッケージが Azure ML によって保守されるイメージと環境に追加できるほど十分に一般的だと考えられる場合は、[AzureML のコンテナー](https://github.com/Azure/AzureML-Containers)に関するページで、GitHub の問題を作成してください。 
 
* **NameError (名前が定義されていない)、AttributeError (オブジェクトに属性がない)** :この例外は、トレーニング スクリプトに起因しているはずです。 Azure portal からログ ファイルを参照して、定義されていない特定の名前または属性エラーに関する詳細情報を取得できます。 SDK から、`run.get_details()` を使用して、エラー メッセージを確認できます。 これにより、実行に対して生成されたすべてのログ ファイルも一覧表示されます。 実行を再送信する前に、トレーニング スクリプトを確認し、エラーを修正してください。 


* **実行または実験の削除**:実験をアーカイブするには、[Experiment.archive](/python/api/azureml-core/azureml.core.experiment%28class%29#archive--) メソッドを使用するか、[Archive experiment]\(アーカイブ実験\) ボタンを介して Azure Machine Learning Studio クライアントの [実験] タブ ビューを使用します。 この操作により、実験はリスト クエリおよびビューから非表示になりますが、削除はされません。

    個々の実験または実行を完全に削除することは現在サポートされていません。 ワークスペース アセットの削除の詳細については、「[Machine Learning service のワークスペース データをエクスポートまたは削除する](how-to-export-delete-data.md)」を参照してください。

* **メトリック ドキュメントが大きすぎる**:Azure Machine Learning には、トレーニングの実行から一度にログに記録できるメトリック オブジェクトのサイズに関する内部制限があります。 リスト値メトリックのログ記録時に "Metric Document is too large (メトリック ドキュメントが大きすぎます)" エラーが発生した場合は、次の例のように、リストを小さいチャンクに分割してみてください。

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    内部的には、Azure ML は同じメトリック名を持つブロックを連続したリストへと連結します。

* **コンピューティング先の起動に時間がかかる**:コンピューティング先の Docker イメージは、Azure Container Registry (ACR) から読み込まれます。 既定では、Azure Machine Learning により、*basic* サービス レベルを使用する ACR が作成されます。 ワークスペースの ACR を standard または premium レベルに変更すると、イメージの構築と読み込みにかかる時間が短縮される場合があります。 詳細については、「[Azure Container Registry サービス階層](../container-registry/container-registry-skus.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* [チュートリアル:モデルのトレーニング](tutorial-train-models-with-aml.md)に関する記事では、マネージド コンピューティング先を使用してモデルをトレーニングします。
* [Scikit-learn](how-to-train-scikit-learn.md)、[TensorFlow](how-to-train-tensorflow.md)、[PyTorch](how-to-train-pytorch.md) など、特定の ML フレームワークを使用してモデルをトレーニングする方法を参照してください。
* より優れたモデルを構築するために、[ハイパーパラメーター](how-to-tune-hyperparameters.md)を効率的に調整する方法を学習します。
* モデルのトレーニングが済んだら、[モデルをデプロイする方法と場所](how-to-deploy-and-where.md)を確認します。
* [ScriptRunConfig クラス](/python/api/azureml-core/azureml.core.scriptrunconfig) SDK リファレンスを表示します。
* [Azure Machine Learning と Azure Virtual Network を使用する](./how-to-network-security-overview.md)
