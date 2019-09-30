---
title: azureml-datasets でデータセットを作成してデータにアクセスする
titleSuffix: Azure Machine Learning
description: さまざまなソースからデータセットを作成し、ワークスペースにデータセットを登録する方法に関する説明
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 08/22/2019
ms.openlocfilehash: 6c3a8d62bd6b3650f834540bd7bb13027792b091
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076984"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Azure Machine Learning でデータセット (プレビュー) を作成してアクセスする

この記事では、Azure Machine Learning データセット (プレビュー) を作成する方法と、ローカルまたはリモートの実験からデータにアクセスする方法について説明します。

Azure Machine Learning データセットを使用すると、次のことを実行できます。 

* データセットから参照される**データの 1 つのコピーをストレージに保存する**。 

* 接続文字列やデータ パスを気にすることなく、**モデルのトレーニング中にデータに簡単にアクセスする**。

* 他のユーザーと**データ共有と共同作業を行う**。

## <a name="prerequisites"></a>前提条件

データセットを作成して操作するには、以下が必要です。

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。

* [Azure Machine Learning ワークスペース](how-to-manage-workspace.md)

* [Azure Machine Learning SDK for Python がインストール済み](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) (これには azureml-datasets パッケージが含まれています)。

> [!Note]
> 一部の Dataset クラス (プレビュー) は [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) パッケージに依存しています。 Linux ユーザーの場合、これらのクラスは次のディストリビューションでのみサポートされています。Red Hat Enterprise Linux、Ubuntu、Fedora、および CentOS。

## <a name="dataset-types"></a>データセットの種類

データセットは、ユーザーがトレーニングでそれらを使用する方法に基づいて、2 種類に分類されます。 

* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) は、指定されたファイルまたはファイルのリストを解析して、データを表形式で表します。 これにより、データを pandas の DataFrame で具体化することができます。 `TabularDataset` オブジェクトは、csv ファイル、tsv ファイル、parquet ファイル、SQL クエリの結果などから作成できます。完全な一覧については、こちらの[ドキュメント](https://aka.ms/tabulardataset-api-reference)を参照してください。

* [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) は、データストアまたはパブリック URL 内の 1 つまたは複数のファイルを参照します。 これにより、ファイルをダウンロードしたり、コンピューターにマウントしたりできます。 任意の形式のファイルを使用できます。これにより、ディープ ラーニングなどの幅広い機械学習シナリオに対応します。

今後の API の変更点の詳細については、[こちら](https://aka.ms/tabular-dataset)を参照してください。

## <a name="create-datasets"></a>データセットを作成する 

データセットを作成することにより、データ ソースの場所への参照とそのメタデータのコピーを作成します。 データは既存の場所に残るので、追加のストレージ コストは発生しません。

データを Azure Machine Learning からアクセスできるようにするには、[Azure データストア](how-to-access-data.md)またはパブリック Web URL のパスからデータセットを作成する必要があります。

[Azure データストア](how-to-access-data.md)からデータセットを作成するには、以下のようにします。

* 登録された Azure データストアへの `contributor` または `owner` アクセス権を持っていることを確認します。

* データストア内のパスを参照してデータセットを作成します。

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()

# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)
```

### <a name="create-tabulardatasets"></a>TabularDataset を作成する

TabularDatasets を作成するには、SDK を使用するか、またはワークスペースのランディング ページ (プレビュー) を使用します。 timestamp は、データの列から、またはデータが格納されているパス パターンの列から指定できます。これにより、時間による簡単かつ効率的なフィルター処理が可能になります。 

#### <a name="using-the-sdk"></a>SDK を使用する 

csv 形式または tsv 形式のファイルを読み取り、登録されていない TabularDataset を作成するには、`TabularDatasetFactory` クラスの [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---partition-format-none-) メソッドを使用します。 複数のファイルから読み取る場合、結果は 1 つの表形式に集計されます。

```Python
# create a TabularDataset from multiple paths in datastore
datastore_paths = [
                  (datastore, 'weather/2018/11.csv'),
                  (datastore, 'weather/2018/12.csv'),
                  (datastore, 'weather/2019/*.csv')
                 ]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)

# create a TabularDataset from a delimited file behind a public web url
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |PassengerId|Survived|Pclass|名前|Sex|Age|SibSp|Parch|Ticket|Fare|Cabin|Embarked
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|0|3|Braund, Mr. Owen Harris|male|22.0|1|0|A/5 21171|7.2500||S
1|2|1|1|Cumings, Mrs. John Bradley (Florence Briggs Th...|female|38.0|1|0|PC 17599|71.2833|C85|C
2|3|1|3|Heikkinen, Miss. Laina|female|26.0|0|0|STON/O2. 3101282|7.9250||S

時間によるフィルター処理を簡単かつ効率的にするには、`TabularDataset` クラスの [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) メソッドを使用します。 その他の例と詳細については、[こちら](http://aka.ms/azureml-tsd-notebook)を参照してください。 

```Python
# create a TabularDataset with timeseries trait
datastore_paths = [(datastore, 'weather/*/*/*/data.parquet')]

# get a coarse timestamp column from the path pattern
dataset = Dataset.Tabular.from_parquet_files(path=datastore_path, partition_format='weather/{coarse_time:yyy/MM/dd}/data.parquet')

# set coarse timestamp to the virtual column created, and fine grain timestamp from a column in the data
dataset = dataset.with_timestamp_columns(fine_grain_timestamp='datetime', coarse_grain_timestamp='coarse_time')

# filter with timeseries trait specific methods 
data_slice = dataset.time_before(datetime(2019, 1, 1))
data_slice = dataset.time_after(datetime(2019, 1, 1))
data_slice = dataset.time_between(datetime(2019, 1, 1), datetime(2019, 2, 1)) 
data_slice = dataset.time_recent(timedelta(weeks=1, days=1))                  
```

#### <a name="using-the-workspace-landing-page"></a>ワークスペース ランディング ページの使用 

Web エクスペリエンスを使用してデータセットを作成するには、[ワークスペース ランディング ページ](https://ml.azure.com)にサインインします。 現在、ワークスペース ランディング ページでは、TabularDatasets の作成のみがサポートされています。

次のアニメーションは、ワークスペース ランディング ページでデータセットを作成する方法を示しています。 

まず、左側のウィンドウの **[アセット]** セクションで **[データセット]** を選択します。 次に、 **[+ データセットの作成]** を選択して、ご利用のデータセットのソースを選択します。これは、ローカル ファイル、データストア、パブリック Web URL のいずれからでも選択できます。 **[設定とプレビュー]** フォームおよび **[スキーマ]** フォームはフファイルの種類に基づいてインテリジェントに設定されます。 **[次へ]** を選択してそれらを確認するか、または作成前にデータセットをさらに構成します。 **[完了]** を選択して、データセットの作成を完了します。 

![UI を使用してデータセットを作成する](media/how-to-create-register-datasets/create-dataset-ui.gif)

### <a name="create-filedatasets"></a>FileDataset を作成する

任意の形式のファイルを読み取り、登録されていない FileDataset を作成するには、`FileDatasetFactory` クラスの [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) メソッドを使います。

```Python
# create a FileDataset from multiple paths in datastore
datastore_paths = [
                  (datastore, 'animals/dog/1.jpg'),
                  (datastore, 'animals/dog/2.jpg'),
                  (datastore, 'animals/dog/*.jpg')
                 ]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = [
            'https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
            'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz'
           ]          
mnist_ds = Dataset.File.from_files(path=web_paths)
```

## <a name="register-datasets"></a>データセットを登録する

作成プロセスを完了するには、ワークスペースにデータセットを登録します。

他のユーザーと共有したり、さまざまな実験で再利用できるように、ご使用のワークスペースにデータセットを登録するには、[`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) メソッドを使います。

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data')
```

>[!Note]
> ワークスペース ランディング ページを介して作成されたデータセットは、自動的にワークスペースに登録されます。 

## <a name="version-datasets"></a>データセットをバージョン管理する

新しいバージョンを作成すると、新しいデータセットを同じ名前で登録できます。 データセットのバージョンは、データの状態をブックマークする方法なので、実験または将来の複製に対して特定のバージョンのデータセットを適用できます。 バージョン管理を検討する一般的なシナリオは次のとおりです。 
* 再トレーニングのために新しいデータを使用できるとき。
* 異なるデータ準備または特徴エンジニアリング アプローチを適用するとき。

```Python
# create a TabularDataset from Titanic training data
web_paths = [
            'https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
            'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv'
           ]          
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```


## <a name="access-your-data-during-training"></a>トレーニング中にデータにアクセスする

登録されたデータセットは、ローカル、リモート、コンピューティング クラスター (Azure Machine Learning コンピューティングなど) でアクセスできます。 実験で登録済みデータセットにアクセスするには、次のコードを使用してワークスペースと登録済みデータセットを名前で取得します。 `Dataset` クラスの [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) メソッドからは、既定では、ワークスペースに登録されているデータセットの最新バージョンが返されます。

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

## <a name="next-steps"></a>次の手順

* 自動機械学習を使用し、[TabularDataset でトレーニング](https://aka.ms/automl-dataset)を行います。
* データセットでのトレーニングの他の例については、[サンプル ノートブック](https://aka.ms/dataset-tutorial)を参照してください。
