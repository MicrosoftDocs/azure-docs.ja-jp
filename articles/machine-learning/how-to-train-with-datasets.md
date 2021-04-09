---
title: Machine learning データセットを使用したトレーニング
titleSuffix: Azure Machine Learning
description: Azure Machine Learning データセットを使用してモデルをトレーニングするために、ローカルまたはリモート コンピューティングで独自のデータを使用できるようにする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: 15bad877be00e143ce6f6956a4e1f23378c275c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102521783"
---
# <a name="train-models-with-azure-machine-learning-datasets"></a>Azure Machine Learning データセットを使用してモデルをトレーニングする 

この記事では、[Azure Machine Learning データセット](/python/api/azureml-core/azureml.core.dataset%28class%29)を使用して、機械学習モデルのトレーニングを行う方法について説明します。  接続文字列やデータ パスを気にすることなく、ローカルまたはリモートのコンピューティング先でデータセットを使用することができます。 

Azure Machine Learning データセットにより、[ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig)、[HyperDrive](/python/api/azureml-train-core/azureml.train.hyperdrive)、[Azure Machine Learning パイプライン](./how-to-create-machine-learning-pipelines.md)などの Azure Machine Learning トレーニング機能とのシームレスな統合が提供されます。

モデル トレーニングに向けてデータを使用できるようにする準備はできていないものの、データ探索用のノートブックにデータを読み込みたい場合は、[データセット内でデータを探索する](how-to-create-register-datasets.md#explore-data)方法に関する記事を参照してください。 

## <a name="prerequisites"></a>前提条件

データセットを作成し、それを使用してトレーニングするには、以下が必要です。

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。

* [Azure Machine Learning ワークスペース](how-to-manage-workspace.md)。

* [Azure Machine Learning SDK for Python がインストール済み](/python/api/overview/azure/ml/install) (1.13.0 以上) (これには `azureml-datasets` パッケージが含まれています)。

> [!Note]
> 一部の Dataset クラスは、[azureml-dataprep](/python/api/azureml-dataprep/) パッケージに依存しています。 Linux ユーザーの場合、これらのクラスは次のディストリビューションでのみサポートされています。Red Hat Enterprise Linux、Ubuntu、Fedora、および CentOS。

## <a name="consume-datasets-in-machine-learning-training-scripts"></a>Machine learning トレーニング スクリプトでデータセットを使用する

データセットとしてまだ登録されていない構造化データがある場合は、TabularDataset を作成し、それをローカルまたはリモートの実験用のトレーニング スクリプトで直接使用します。

この例では、未登録の [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) を作成し、トレーニング用の ScriptRunConfig オブジェクトのスクリプト引数として指定します。 この TabularDataset をワークスペースの他の実験で再利用する場合は、[データセットをワークスペースに登録する方法](how-to-create-register-datasets.md#register-datasets)に関する記事を参照してください。

### <a name="create-a-tabulardataset"></a>TabularDataset を作成する

次のコードでは、Web URL から未登録の TabularDataset を作成します。  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

TabularDataset オブジェクトを使用すると、TabularDataset のデータを pandas または Spark DataFrame に読み込むことができるため、お使いのノートブックを離れることなく、使い慣れたデータ準備とトレーニングのライブラリを操作することができます。

### <a name="access-dataset-in-training-script"></a>トレーニング スクリプトでデータセットにアクセスする

次のコードでは、トレーニングの実行を構成するときに指定するスクリプト引数 `--input-data` を構成します (次のセクションを参照してください)。 表形式のデータセットが引数値として渡されると、Azure ML によってデータセットの ID に解決されます。これを使用すると、トレーニング スクリプトのデータセットにアクセスできます (スクリプトにデータセットの名前や ID をハードコーディングする必要はありません)。 次に、トレーニングの前にデータをさらに調べて準備するために、[`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) メソッドを使用して、そのデータセットを pandas データフレームに読み込みます。

> [!Note]
> 元のデータ ソースに NaN、空の文字列、または空白の値が含まれている場合、`to_pandas_dataframe()` を使用すると、それらの値は *Null* 値として置き換えられます。

準備されたデータをイン メモリの pandas データ フレームから新しいデータセットに読み込む必要がある場合は、そのデータを parquet などのローカル ファイルに書き込み、そのファイルから新しいデータセットを作成します。 [データセットを作成する方法](how-to-create-register-datasets.md)の詳細をご覧ください。

```Python
%%writefile $script_folder/train_titanic.py

import argparse
from azureml.core import Dataset, Run

parser = argparse.ArgumentParser()
parser.add_argument("--input-data", type=str)
args = parser.parse_args()

run = Run.get_context()
ws = run.experiment.workspace

# get the input dataset by ID
dataset = Dataset.get_by_id(ws, id=args.input_data)

# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-training-run"></a>トレーニングの実行を構成する

[ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrun) オブジェクトは、トレーニング実行を構成および送信するために使用されます。

このコードでは、以下を指定する ScriptRunConfig オブジェクト `src` を作成します

* 使用するスクリプトのスクリプト ディレクトリ。 このディレクトリ内のすべてのファイルは、実行のためにクラスター ノード内にアップロードされます。
* トレーニング スクリプト *train_titanic.py*。
* スクリプト引数としてのトレーニング用の入力データセット `titanic_ds`。 これがスクリプトに渡されると、Azure ML によってデータセットの対応する ID に解決されます。
* 実行のコンピューティング先。
* 実行の環境。

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=script_folder,
                      script='train_titanic.py',
                      # pass dataset as an input with friendly name 'titanic'
                      arguments=['--input-data', titanic_ds.as_named_input('titanic')],
                      compute_target=compute_target,
                      environment=myenv)
                             
# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)                             
```

## <a name="mount-files-to-remote-compute-targets"></a>リモート コンピューティング先にファイルをマウントする

非構造化データがある場合は、[FileDataset](/python/api/azureml-core/azureml.data.filedataset) を作成し、データ ファイルをマウントまたはダウンロードして、トレーニング用にリモート コンピューティング先でそれを使用できるようにします。 リモート トレーニング実験用に[マウントまたはダウンロード](#mount-vs-download)を使用する状況について説明します。 

次の例では、FileDataset を作成し、データセットをトレーニング スクリプトに引数として渡すことによって、それをコンピューティング先にマウントします。 

> [!Note]
> カスタム Docker ベース イメージを使用している場合は、データセットのマウントを機能させるための依存関係として、`apt-get install -y fuse` 経由で fuse をインストールする必要があります。 [カスタム ビルド イメージを構築](how-to-deploy-custom-docker-image.md#build-a-custom-base-image)する方法を確認してください。

### <a name="create-a-filedataset"></a>FileDataset を作成する

次の例では、未登録の FileDataset を Web URL から作成します。 他のソースから[データセットを作成する方法](how-to-create-register-datasets.md)の詳細を参照してください。

```Python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
mnist_ds = Dataset.File.from_files(path = web_paths)
```

### <a name="configure-the-training-run"></a>トレーニングの実行を構成する

`ScriptRunConfig` コンストラクターの `arguments` パラメーターを介してマウントする場合は、データセットを引数として渡すことをお勧めします。 そうすることで、引数を介してトレーニング スクリプトのデータ パス (マウント ポイント) を取得します。 これにより、任意のクラウド プラットフォームで、ローカル デバッグとリモート トレーニングに、同じトレーニング スクリプトを使用できるようになります。

次の例では、`arguments` を介して FileDataset を渡す ScriptRunConfig を作成します。 実行を送信すると、`mnist_ds` データセットによって参照されるデータ ファイルがコンピューティング先にマウントされます。

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=script_folder,
                      script='train_mnist.py',
                      # the dataset will be mounted on the remote compute and the mounted path passed as an argument to the script
                      arguments=['--data-folder', mnist_ds.as_mount(), '--regularization', 0.5],
                      compute_target=compute_target,
                      environment=myenv)

# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="retrieve-data-in-your-training-script"></a>トレーニング スクリプトのデータを取得する

次のコードは、スクリプトでデータを取得する方法を示しています。

```Python
%%writefile $script_folder/train_mnist.py

import argparse
import os
import numpy as np
import glob

from utils import load_data

# retrieve the 2 arguments configured through `arguments` in the ScriptRunConfig
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# get the file paths on the compute
X_train_path = glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0]
X_test_path = glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0]
y_train_path = glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0]
y_test = glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0]

# load train and test set into numpy arrays
X_train = load_data(X_train_path, False) / 255.0
X_test = load_data(X_test_path, False) / 255.0
y_train = load_data(y_train_path, True).reshape(-1)
y_test = load_data(y_test, True).reshape(-1)
```

## <a name="mount-vs-download"></a>マウントまたはダウンロード

任意の形式のファイルをダウンロードしたりマウントしたりすることは、Azure BLOB ストレージ、Azure Files、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL Database、および Azure Database for PostgreSQL から作成されたデータセットに対してサポートされています。 

データセットを **マウント** する場合は、データセットによって参照されているファイルをディレクトリ (マウント ポイント) に接続し、コンピューティング先で使用できるようにします。 Azure Machine Learning コンピューティング、仮想マシン、HDInsight など、Linux ベースのコンピューティングでは、マウントがサポートされています。 

データセットを **ダウンロード** するとき、データセットによって参照されるすべてのファイルが、コンピューティング先にダウンロードされます。 すべてのコンピューティングの種類でダウンロードがサポートされています。 

データセットによって参照されるファイルのすべてがスクリプトで処理され、コンピューティング ディスクが完全なデータセットに収まる場合は、ダウンロードによって、ストレージ サービスからのデータ ストリーミングのオーバーヘッドを回避することをお勧めします。 データ サイズがコンピューティング ディスクのサイズを超えると、ダウンロードできません。 このシナリオでは、処理時にスクリプトで使用されるデータ ファイルのみが読み込まれるため、マウントすることをお勧めします。

次のコードを実行すると、`dataset` が `mounted_path` の一時ディレクトリにマウントされます

```python
import tempfile
mounted_path = tempfile.mkdtemp()

# mount dataset onto the mounted_path of a Linux-based compute
mount_context = dataset.mount(mounted_path)

mount_context.start()

import os
print(os.listdir(mounted_path))
print (mounted_path)
```

## <a name="get-datasets-in-machine-learning-scripts"></a>Machine learning スクリプトでデータセットを取得する

登録されたデータセットは、Azure Machine Learning コンピューティングなどのコンピューティング クラスター上で、ローカルまたはリモートでアクセスできます。 複数の実験で登録済みデータセットにアクセスするには、以下のコードを使用してワークスペースにアクセスし、以前送信した実行で使用されたデータセットを取得します。 既定では、`Dataset` クラスの [`get_by_name()`](/python/api/azureml-core/azureml.core.dataset.dataset#get-by-name-workspace--name--version--latest--) メソッドからは、ワークスペースに登録されているデータセットの最新バージョンが返されます。

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## <a name="access-source-code-during-training"></a>トレーニング中のソース コードへのアクセス

Azure BLOB ストレージは、スループット速度が Azure ファイル共有よりも高く、並列で開始される多数のジョブに対応します。 このため、ソース コード ファイルの転送については、BLOB ストレージを使用するように実行を構成することをお勧めします。

次のコード例では、実行構成で、ソース コード転送に使用する BLOB データストアを指定します。

```python 
# workspaceblobstore is the default blob storage
src.run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="notebook-examples"></a>ノートブックの例

+ [データセット ノートブック](https://aka.ms/dataset-tutorial)では、この記事の概念を示し、さらに詳しく説明します。
+ [ML パイプラインでデータセットをパラメーター化する](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-dataset-and-pipelineparameter.ipynb)方法を参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

* **データセットの初期化に失敗しました:マウント ポイントの準備が完了するまで待っていましたがタイムアウトになりました**: 
  * アウトバウンド [ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md)規則がなく、`azureml-sdk>=1.12.0` を使用している場合は、`azureml-dataset-runtime` とその依存関係を更新して、特定のマイナー バージョンの最新版にします。または、それを実行で使用している場合は、修正プログラムを含む最新パッチが適用されるように、お使いの環境を再作成します。 
  * `azureml-sdk<1.12.0` を使用している場合は、最新バージョンにアップグレードします。
  * アウトバウンド NSG 規則がある場合は、サービス タグ `AzureResourceMonitor` のすべてのトラフィックを許可するアウトバウンド規則があることを確認します。

### <a name="overloaded-azurefile-storage"></a>オーバーロードされた AzureFile ストレージ

エラー `Unable to upload project files to working directory in AzureFile because the storage is overloaded` が発生する場合は、次の回避策を適用してください。

データ転送などの他のワークロードにファイル共有を使用している場合は、BLOB を使用して、ファイル共有を実行の送信のために自由に使用できるようにすることをお勧めします。 2 つの異なるワークスペース間でワークロードを分割することもできます。

### <a name="passing-data-as-input"></a>入力としてのデータの引き渡し

*  **TypeError:FileNotFound:そのようなファイルまたはディレクトリはありません**:このエラーは、指定したファイル パスにファイルがない場合に発生します。 ファイルを参照する方法が、コンピューティング先でデータセットをマウントした場所と一致していることを確認する必要があります。 確定的な状態を確保するには、データセットをコンピューティング先にマウントするときに抽象パスを使用することをお勧めします。 たとえば、次のコードでは、コンピューティング先のファイルシステムのルート `/tmp` にデータセットをマウントしています。 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    先頭のスラッシュ "/" を含めない場合は、データセットをマウントする場所を示すために、コンピューティング先の作業ディレクトリをプレフィックスとして付ける必要があります (たとえば、`/mnt/batch/.../tmp/dataset`)。


## <a name="next-steps"></a>次のステップ

* TabularDatasets を使用して[機械学習モデルを自動的にトレーニングする](how-to-auto-train-remote.md)。

* FileDatasets を使用して[画像分類モデルをトレーニングする](https://aka.ms/filedataset-samplenotebook)。

* [パイプラインを使用してデータセットをトレーニングする](./how-to-create-machine-learning-pipelines.md)。