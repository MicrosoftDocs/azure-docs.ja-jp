---
title: azureml-datasets を使用したトレーニング
titleSuffix: Azure Machine Learning
description: トレーニングでデータセットを使用する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: b20612756050ae2e9d39f59d049b8c097e3b8010
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88651218"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>Azure Machine Learning でデータセットを使用してトレーニングする
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、トレーニング実験で [Azure Machine Learning データセット](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py)を使用する方法について説明します。  接続文字列やデータ パスを気にすることなく、ローカルまたはリモートのコンピューティング先でデータセットを使用することができます。

Azure Machine Learning データセットにより、[ScriptRun](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py)、[Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py)、[HyperDrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py)、[Azure Machine Learning パイプライン](how-to-create-your-first-pipeline.md)などの Azure Machine Learning トレーニング製品とのシームレスな統合が提供されます。

## <a name="prerequisites"></a>前提条件

データセットを作成し、それを使用してトレーニングするには、以下が必要です。

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。

* [Azure Machine Learning ワークスペース](how-to-manage-workspace.md)。

* [Azure Machine Learning SDK for Python がインストール済み](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) (これには azureml-datasets パッケージが含まれています)。

> [!Note]
> 一部の Dataset クラスは、[azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) パッケージに依存しています。 Linux ユーザーの場合、これらのクラスは次のディストリビューションでのみサポートされています。Red Hat Enterprise Linux、Ubuntu、Fedora、および CentOS。

## <a name="access-and-explore-input-datasets"></a>入力データセットへのアクセスと探索

ワークスペースの実験のトレーニング スクリプトから既存の TabularDataset にアクセスし、そのデータセットを pandas データフレームに読み込んで、ローカル環境でさらに調べることができます。

次のコードでは、[`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) クラスの [`get_context()`]() メソッドを使用して、トレーニング スクリプトの既存の入力 TabularDataset (`titanic`) にアクセスします。 次に、トレーニングの前にデータをさらに調べて準備するために、[`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) メソッドを使用して、そのデータセットを pandas データフレームに読み込みます。

> [!Note]
> 元のデータ ソースに NaN、空の文字列、または空白の値が含まれている場合、to_pandas_dataframe() を使用すると、それらの値は *Null* 値として置き換えられます。 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic']

# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

準備されたデータをイン メモリの pandas データ フレームから新しいデータセットに読み込む必要がある場合は、そのデータを parquet などのローカル ファイルに書き込み、そのファイルから新しいデータセットを作成します。 データストアのローカル ファイルまたはローカル パスからデータセットを作成することもできます。 [データセットを作成する方法](how-to-create-register-datasets.md)の詳細をご覧ください。

## <a name="use-datasets-directly-in-training-scripts"></a>トレーニング スクリプトでデータセットを直接使用する

データセットとしてまだ登録されていない構造化データがある場合は、TabularDataset を作成し、それをローカルまたはリモートの実験用のトレーニング スクリプトで直接使用します。

この例では、登録されていない [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) を作成し、それをトレーニング用の `estimator` オブジェクトへの直接入力として使用します。 この TabularDataset をワークスペースの他の実験で再利用する場合は、[データセットをワークスペースに登録する方法](how-to-create-register-datasets.md#register-datasets)に関する記事を参照してください。

### <a name="create-a-tabulardataset"></a>TabularDataset を作成する

次のコードでは、Web URL から未登録の TabularDataset を作成します。  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

TabularDataset オブジェクトを使用すると、TabularDataset のデータを pandas または Spark DataFrame に読み込むことができるため、お使いのノートブックを離れることなく、使い慣れたデータ準備とトレーニングのライブラリを操作することができます。 この機能を利用するには、「[入力データセットへのアクセスと探索](#access-and-explore-input-datasets)」を参照してください。

### <a name="configure-the-estimator"></a>エスティメーターを構成する

実験実行の送信には、[エスティメーター](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) オブジェクトが使用されます。 Azure Machine Learning には、一般的な機械学習フレームワーク用に事前に構成されたエスティメーターと、汎用的なエスティメーターがあります。

このコードでは、以下を指定して `est` という汎用エスティメーター オブジェクトを作成しています。

* 使用するスクリプトのスクリプト ディレクトリ。 このディレクトリ内のすべてのファイルは、実行のためにクラスター ノード内にアップロードされます。
* トレーニング スクリプト *train_titanic.py*。
* トレーニングの入力データセット `titanic_ds`。 `as_named_input()` は、トレーニング スクリプトで割り当てられた名前 `titanic` によって入力データセットを参照できるようにするために必要です。 
* 実験のコンピューティング先。
* 実験の環境定義。

```Python
est = Estimator(source_directory=script_folder,
                entry_script='train_titanic.py',
                # pass dataset object as an input with name 'titanic'
                inputs=[titanic_ds.as_named_input('titanic')],
                compute_target=compute_target,
                environment_definition= conda_env)

# Submit the estimator as part of your experiment run
experiment_run = experiment.submit(est)
experiment_run.wait_for_completion(show_output=True)
```

## <a name="mount-files-to-remote-compute-targets"></a>リモート コンピューティング先にファイルをマウントする

非構造化データがある場合は、[FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py) を作成し、データ ファイルをマウントまたはダウンロードして、トレーニング用にリモート コンピューティング先でそれを使用できるようにします。 リモート トレーニング実験用に[マウントまたはダウンロード](#mount-vs-download)を使用する状況について説明します。 

次の例では、FileDataset を作成し、データセットをトレーニング用の推定器の引数として渡すことによって、それをコンピューティング先にマウントします。 

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

### <a name="configure-the-estimator"></a>エスティメーターを構成する

マウント時に引数としてデータセットを渡すことをお勧めします。 エスティメーターで `inputs` パラメーターを使用してデータセットを渡すほか、`script_params` を使用してデータセットを渡し、引数を使用してトレーニング スクリプト内でデータ パス (マウント ポイント) を取得することもできます。 これにより、任意のクラウド プラットフォームで、ローカル デバッグとリモート トレーニングに、同じトレーニング スクリプトを使用できるようになります。

scikit-learn 実験の実行の送信には、[SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) エスティメーター オブジェクトが使用されます。 実行を送信すると、`mnist` データセットによって参照されるデータ ファイルがコンピューティング先にマウントされます。 SKlearn エスティメーターによるトレーニングの詳細については[こちら](how-to-train-scikit-learn.md)を参照してください。

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    # mount the dataset on the remote compute and pass the mounted path as an argument to the training script
    '--data-folder': mnist_ds.as_named_input('mnist').as_mount(),
    '--regularization': 0.5
}

est = SKLearn(source_directory=script_folder,
              script_params=script_params,
              compute_target=compute_target,
              environment_definition=env,
              entry_script='train_mnist.py')

# Run the experiment
run = experiment.submit(est)
run.wait_for_completion(show_output=True)
```

### <a name="retrieve-the-data-in-your-training-script"></a>トレーニング スクリプトでデータを取得する

次のコードは、スクリプトでデータを取得する方法を示しています。

```Python
%%writefile $script_folder/train_mnist.py

import argparse
import os
import numpy as np
import glob

from utils import load_data

# retrieve the 2 arguments configured through script_params in estimator
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

データセットを**マウント**する場合は、データセットによって参照されているファイルをディレクトリ (マウント ポイント) に接続し、コンピューティング先で使用できるようにします。 Azure Machine Learning コンピューティング、仮想マシン、HDInsight など、Linux ベースのコンピューティングでは、マウントがサポートされています。 

データセットを**ダウンロード**するとき、データセットによって参照されるすべてのファイルが、コンピューティング先にダウンロードされます。 すべてのコンピューティングの種類でダウンロードがサポートされています。 

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

## <a name="access-datasets-in-your-script"></a>スクリプトでデータセットにアクセスする

登録されたデータセットは、Azure Machine Learning コンピューティングなどのコンピューティング クラスター上で、ローカルまたはリモートでアクセスできます。 実験で登録済みデータセットにアクセスするには、次のコードを使用して、ワークスペースと登録済みデータセットに名前でアクセスします。 既定では、`Dataset` クラスの [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) メソッドからは、ワークスペースに登録されているデータセットの最新バージョンが返されます。

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

## <a name="accessing-source-code-during-training"></a>トレーニング中のソース コードへのアクセス

Azure BLOB ストレージは、スループット速度が Azure ファイル共有よりも高く、並列で開始される多数のジョブに対応します。 このため、ソース コード ファイルの転送については、BLOB ストレージを使用するように実行を構成することをお勧めします。

次のコード例では、実行構成で、ソース コード転送に使用する BLOB データストアを指定します。

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="notebook-examples"></a>ノートブックの例

[データセット ノートブック](https://aka.ms/dataset-tutorial)では、この記事の概念を示し、さらに詳しく説明します。

## <a name="next-steps"></a>次のステップ

* TabularDatasets を使用して[機械学習モデルを自動的にトレーニングする](how-to-auto-train-remote.md)。

* FileDatasets を使用して[画像分類モデルをトレーニングする](https://aka.ms/filedataset-samplenotebook)。

* [パイプラインを使用してデータセットをトレーニングする](how-to-create-your-first-pipeline.md)。
