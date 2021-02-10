---
title: チュートリアル:独自のデータを使用する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 入門シリーズの第 4 部では、リモート トレーニングの実行で独自のデータを使用する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: tracking-python
ms.openlocfilehash: c08d9ee6704203d0634d7a1b90a57de9c6a99d31
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2021
ms.locfileid: "98622797"
---
# <a name="tutorial-use-your-own-data-part-4-of-4"></a>チュートリアル:独自のデータを使用する (4 部構成中の第 4 部)

このチュートリアルでは、独自のデータをアップロードし、それを使用することで、Azure Machine Learning で機械学習モデルをトレーニングする方法について説明します。

このチュートリアルは、"*4 部構成のチュートリアル シリーズのパート 4*" であり、Azure Machine Learning の基礎を学習し、Azure でジョブベースの機械学習タスクを実行します。 このチュートリアルは次のパートで完了した作業を基にしています。このシリーズの[第 1 部: 設定](tutorial-1st-experiment-sdk-setup-local.md)、[第 2 部:"Hello World" の実行](tutorial-1st-experiment-hello-world.md)、[第 3 部: モデルのトレーニング](tutorial-1st-experiment-sdk-train.md)に関するページを参照してください。

[第 3 部: モデルのトレーニング](tutorial-1st-experiment-sdk-train.md)では、PyTorch API 内の組み込みの `torchvision.datasets.CIFAR10` メソッドを使用してデータをダウンロードしました。 ただし、多くの場合、リモート トレーニングの実行では独自のデータを使用することになります。 この記事では、Azure Machine Learning で独自のデータを操作するのに使用できるワークフローについて説明します。

このチュートリアルでは、次のことを行いました。

> [!div class="checklist"]
> * ローカル ディレクトリ内のデータを使用するようにトレーニング スクリプトを構成する。
> * トレーニング スクリプトをローカルでテストする。
> * Azure にデータをアップロードする。
> * コントロール スクリプトを作成する。
> * 新しい Azure Machine Learning の概念を理解する (パラメーター、データセット、データストアの受け渡し)。
> * トレーニング スクリプトを送信して実行する。
> * クラウドでのコード出力を表示する。

## <a name="prerequisites"></a>前提条件

* このシリーズの[第 3 部](tutorial-1st-experiment-sdk-train.md)を完了している。

## <a name="adjust-the-training-script"></a>トレーニング スクリプトを調整する

既に、Azure Machine Learning で実行するトレーニング スクリプト (tutorial/src/train.py) が用意され、モデルのパフォーマンスを監視できるようになっています。 引数を導入してトレーニング スクリプトをパラメーター化しましょう。 引数を使用すると、さまざまなハイパーパラメーターを簡単に比較できます。

現在、トレーニング スクリプトは実行のたびに CIFAR10 データセットをダウンロードするように設定されています。 次の Python コードは、ディレクトリからデータを読み取るように調整されています。

>[!NOTE] 
> `argparse` を使用すると、スクリプトがパラメーター化されます。

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/code/pytorch-cifar10-your-data/train.py":::

### <a name="understanding-the-code-changes"></a>コードの変更について

`train.py` のコードでは、`argparse` ライブラリを使用して `data_path`、`learning_rate`、および `momentum` を設定しています。

```python
# .... other code
parser = argparse.ArgumentParser()
parser.add_argument('--data_path', type=str, help='Path to the training data')
parser.add_argument('--learning_rate', type=float, default=0.001, help='Learning rate for SGD')
parser.add_argument('--momentum', type=float, default=0.9, help='Momentum for SGD')
args = parser.parse_args()
# ... other code
```

また、`train.py` スクリプトを調整して、ユーザー定義のパラメーターを使用できるようにオプティマイザーを更新しました。

```python
optimizer = optim.SGD(
    net.parameters(),
    lr=args.learning_rate,     # get learning rate from command-line argument
    momentum=args.momentum,    # get momentum from command-line argument
)
```
> [!div class="nextstepaction"]
> [トレーニング スクリプトを調整しました](?success=adjust-training-script#test-locally) [問題が発生しました](https://www.research.net/r/7C6W7BQ?issue=adjust-training-script)

## <a name="test-the-script-locally"></a><a name="test-locally"></a> スクリプトのローカル テスト

これで、スクリプトは _データ パス_ を引数として受け付けるようになりました。 まず、それをローカルでテストします。 チュートリアル ディレクトリ構造に、`data` という名前のフォルダーを追加します。 ディレクトリ構造は次のようになります。

```txt
tutorial
└──.azureml
|  └──config.json
|  └──pytorch-env.yml
└──data
└──src
|  └──hello.py
|  └──model.py
|  └──train.py
└──01-create-workspace.py
└──02-create-compute.py
└──03-run-hello.py
└──04-run-pytorch.py
```

前のチュートリアルで `train.py` をローカルで実行しなかった場合、`data/` ディレクトリは含まれません。 この場合は、`train.py` スクリプト内で `download=True` を使用して `torchvision.datasets.CIFAR10` メソッドをローカルで実行します。

また、ローカルで実行するには、必ずチュートリアル環境を終了し、新しい conda 環境をアクティブ化してください。

```bash
conda deactivate                # If you are still using the tutorial environment, exit it
```

```bash
conda env create -f .azureml/pytorch-env.yml    # create the new conda environment with updated dependencies
```

```bash
conda activate pytorch-aml-env          # activate new conda environment
```

変更したトレーニング スクリプトをローカルで実行するには、次のように呼び出します。

```bash
python src/train.py --data_path ./data --learning_rate 0.003 --momentum 0.92
```

データへのローカル パスを渡すことによって、CIFAR10 データセットをダウンロードする必要はなくなります。 また、トレーニング スクリプト内にハードコーディングしなくても、_学習率_ および _モーメンタム_ ハイパーパラメーターのさまざまな値を試してみることができます。

> [!div class="nextstepaction"]
> [スクリプトをローカルでテストしました](?success=test-locally#upload) [問題が発生しました](https://www.research.net/r/7C6W7BQ?issue=test-locally)

## <a name="upload-the-data-to-azure"></a><a name="upload"></a> データを Azure にアップロードする

このスクリプトを Azure Machine Learning で実行するために、トレーニング データを Azure で使用できるようにする必要があります。 Azure Machine Learning ワークスペースには、 _既定_ のデータストアが用意されています。 これは、トレーニング データを格納できる Azure Blob Storage アカウントです。

>[!NOTE] 
> Azure Machine Learning を使用すると、自分のデータが格納される他のクラウドベースのデータストアに接続できます。 詳細については、[データストアに関するドキュメント](./concept-data.md)を参照してください。  

`tutorial` ディレクトリに `05-upload-data.py` という名前の新しい Python コントロール スクリプトを作成します。

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/05-upload-data.py":::

`target_path` の値には、CIFAR10 データがアップロードされるデータストア上のパスを指定します。

>[!TIP] 
> Azure Machine Learning を使用してデータをアップロードしながら、[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を使用してアドホック ファイルをアップロードできます。 ETL ツールが必要な場合は、[Azure Data Factory](../data-factory/introduction.md) を使用して、データを Azure に取り込むことができます。

アクティブになった *tutorial1* Conda 環境が表示されているウィンドウで、次の Python ファイルを実行して、データをアップロードします。 (アップロードは 60 秒未満で迅速に行われます)。

```bash
python 05-upload-data.py
```

次の標準的な出力が表示されます。

```txt
Uploading ./data\cifar-10-batches-py\data_batch_2
Uploaded ./data\cifar-10-batches-py\data_batch_2, 4 files out of an estimated total of 9
.
.
Uploading ./data\cifar-10-batches-py\data_batch_5
Uploaded ./data\cifar-10-batches-py\data_batch_5, 9 files out of an estimated total of 9
Uploaded 9 files
```

> [!div class="nextstepaction"]
> [データをアップロードしました](?success=upload-data#control-script) [問題が発生しました](https://www.research.net/r/7C6W7BQ?issue=upload-data)

## <a name="create-a-control-script"></a><a name="control-script"></a> コントロール スクリプトを作成する

前に行ったように、`06-run-pytorch-data.py` という名前の新しい Python コントロール スクリプトを作成します。

```python
# 06-run-pytorch-data.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig
from azureml.core import Dataset

if __name__ == "__main__":
    ws = Workspace.from_config()
    datastore = ws.get_default_datastore()
    dataset = Dataset.File.from_files(path=(datastore, 'datasets/cifar10'))

    experiment = Experiment(workspace=ws, name='day1-experiment-data')

    config = ScriptRunConfig(
        source_directory='./src',
        script='train.py',
        compute_target='cpu-cluster',
        arguments=[
            '--data_path', dataset.as_named_input('input').as_mount(),
            '--learning_rate', 0.003,
            '--momentum', 0.92],
    )
    # set up pytorch environment
    env = Environment.from_conda_specification(
        name='pytorch-env',
        file_path='./.azureml/pytorch-env.yml'
    )
    config.run_config.environment = env

    run = experiment.submit(config)
    aml_url = run.get_portal_url()
    print("Submitted to compute cluster. Click link below")
    print("")
    print(aml_url)
```

### <a name="understand-the-code-changes"></a>コードの変更を理解する

このコントロール スクリプトは、[このシリーズの第 3 部](tutorial-1st-experiment-sdk-train.md)からのものによく似ていますが、次に示す新しい行が追加されています。

:::row:::
   :::column span="":::
      `dataset = Dataset.File.from_files( ... )`
   :::column-end:::
   :::column span="2":::
      [データセット](/python/api/azureml-core/azureml.core.dataset.dataset?preserve-view=true&view=azure-ml-py)は、Azure Blob Storage にアップロードしたデータを参照するために使用します。 データセットは、ご利用のデータの上にある抽象化レイヤーであり、信頼性および信頼度の向上を目的に設計されています。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig(...)`
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) は、`train.py` に渡す引数のリストを含むように変更されています。 `dataset.as_named_input('input').as_mount()` 引数を使用すると、指定したディレクトリがコンピューティング先に _マウント_ されます。
   :::column-end:::
:::row-end:::

> [!div class="nextstepaction"]
> [コントロール スクリプトを作成しました](?success=control-script#submit-to-cloud) [問題が発生しました](https://www.research.net/r/7C6W7BQ?issue=control-script)

## <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit-to-cloud"></a> Azure Machine Learning に実行を送信する

ここで、新しい構成を使用するために実行を再送信します。

```bash
python 06-run-pytorch-data.py
```

このコードにより Azure Machine Learning スタジオの実験に URL が出力されます。 そのリンクに移動すると、実行中の自分のコードを確認できます。

> [!div class="nextstepaction"]
> [実行を再送信しました](?success=submit-to-cloud#inspect-log) [問題が発生しました](https://www.research.net/r/7C6W7BQ?issue=submit-to-cloud)

### <a name="inspect-the-log-file"></a><a name="inspect-log"></a> ログ ファイルの確認

スタジオで、前の URL 出力を選択することによって実験の実行に移動し、 **[出力 + ログ]** を選択します。 `70_driver_log.txt` ファイルを選択します。 次の出力が表示されます。

```txt
Processing 'input'.
Processing dataset FileDataset
{
  "source": [
    "('workspaceblobstore', 'datasets/cifar10')"
  ],
  "definition": [
    "GetDatastoreFiles"
  ],
  "registration": {
    "id": "XXXXX",
    "name": null,
    "version": null,
    "workspace": "Workspace.create(name='XXXX', subscription_id='XXXX', resource_group='X')"
  }
}
Mounting input to /tmp/tmp9kituvp3.
Mounted input to /tmp/tmp9kituvp3 as folder.
Exit __enter__ of DatasetContextManager
Entering Run History Context Manager.
Current directory:  /mnt/batch/tasks/shared/LS_root/jobs/dsvm-aml/azureml/tutorial-session-3_1600171983_763c5381/mounts/workspaceblobstore/azureml/tutorial-session-3_1600171983_763c5381
Preparing to call script [ train.py ] with arguments: ['--data_path', '$input', '--learning_rate', '0.003', '--momentum', '0.92']
After variable expansion, calling script [ train.py ] with arguments: ['--data_path', '/tmp/tmp9kituvp3', '--learning_rate', '0.003', '--momentum', '0.92']

Script type = None
===== DATA =====
DATA PATH: /tmp/tmp9kituvp3
LIST FILES IN DATA PATH...
['cifar-10-batches-py', 'cifar-10-python.tar.gz']
```

注意:

- Azure Machine Learning によって Blob Storage が自動的にコンピューティング クラスターにマウントされました。
- コントロール スクリプトで使用されている ``dataset.as_named_input('input').as_mount()`` は、マウント ポイントに解決されます。

> [!div class="nextstepaction"]
> [ログ ファイルを確認しました](?success=inspect-log#clean-up-resources) [問題が発生しました](https://www.research.net/r/7C6W7BQ?issue=inspect-log)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

リソース グループは保持しつつ、いずれかのワークスペースを削除することもできます。 ワークスペースのプロパティを表示し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、`Datastore` を使用して Azure にデータをアップロードする方法について説明しました。 データストアはワークスペース用のクラウド ストレージとして機能し、データを保持するための永続的で柔軟な場所を提供しました。

コマンドラインを介してデータ パスを受け付けるようにトレーニング スクリプトを変更する方法を説明しました。 `Dataset` を使用することで、ディレクトリをリモート実行にマウントできました。 

モデルを用意できましたので、今度は次のことを学習します。

* [Azure Machine Learning を使用してモデルをデプロイする](how-to-deploy-and-where.md)方法。
