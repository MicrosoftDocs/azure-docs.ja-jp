---
title: Azure Machine Learning データセットを作成する
titleSuffix: Azure Machine Learning
description: 機械学習の実験を実行するために Azure Machine Learning データセットを作成してデータにアクセスする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1, data4ml
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.openlocfilehash: 39973fe8c15364dc214392985cecd8b8bc7834ed
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878207"
---
# <a name="create-azure-machine-learning-datasets"></a>Azure Machine Learning データセットを作成する

この記事では、Azure Machine Learning Python SDK を使用して、Azure Machine Learning データセットを作成し、ローカルまたはリモートの実験のデータにアクセスする方法について説明します。 Azure Machine Learning のデータ アクセス ワークフロー全体におけるデータ セットの位置付けの詳細については、[データへの安全なアクセス](concept-data.md#data-workflow)に関するページを参照してください。

データセットを作成することにより、データ ソースの場所への参照とそのメタデータのコピーを作成します。 データは既存の場所に残るため、追加のストレージ コストは発生せず、データ ソースの整合性が損なわれることはありません。 また、データセットは遅延評価されるので、ワークフローのパフォーマンス向上に役立ちます。 データセットは、データストア、パブリック URL、[Azure Open Dataset](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md) から作成できます。

コードの少ないエクスペリエンスについては、[Azure Machine Learning Studio での Azure Machine Learning データセットの作成](how-to-connect-data-ui.md#create-datasets)に関するページを参照してください。

Azure Machine Learning データセットを使用すると、次のことを実行できます。

* データセットから参照されるデータの 1 つのコピーをストレージに保存する。

* 接続文字列やデータ パスを気にすることなく、モデルのトレーニング中にデータにシームレスにアクセスする。[データセットを使用したトレーニング方法の詳細を確認してください](how-to-train-with-datasets.md)。

* 他のユーザーとデータを共有し、共同作業を行う。

## <a name="prerequisites"></a>前提条件

データセットを作成して操作するには、以下が必要です。

* Azure サブスクリプション。 まだ持っていない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) をお試しください。

* [Azure Machine Learning ワークスペース](how-to-manage-workspace.md)。

* [Azure Machine Learning SDK for Python がインストール済み](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (これには azureml-datasets パッケージが含まれています)。

    * [Azure Machine Learning コンピューティング インスタンス](how-to-create-manage-compute-instance.md)を作成します (これは、統合ノートブックと SDK が既にインストールされている、完全に構成および管理された開発環境です)。

    **OR**

    * ご自分の Jupyter Notebook で作業し、 [こちらの手順](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)に従って SDK をご自身でインストールします。

> [!NOTE]
> 一部の Dataset クラスは、[azureml-dataprep](/python/api/azureml-dataprep/?preserve-view=true&view=azure-ml-py) パッケージに依存しています。これは、64 ビットの Python とのみ互換性があります。 Linux ユーザーの場合、これらのクラスは次のディストリビューションでのみサポートされています。Red Hat Enterprise Linux (7、8)、Ubuntu (14.04、16.04、18.04)、Fedora (27、28)、Debian (8、9)、および CentOS (7) サポートされていないディストリビューションを使用している場合は、[こちらのガイド](/dotnet/core/install/linux)に従って、.NET Core 2.1 をインストールして続行してください。 

## <a name="compute-size-guidance"></a>コンピューティング サイズのガイダンス

データセットを作成するときは、コンピューティング処理能力とメモリ内のデータのサイズを確認します。 ストレージ内のデータのサイズは、データフレーム内のデータのサイズと同じではありません。 たとえば、CSV ファイルのデータはデータフレームで最大 10 倍まで拡張できるため、1 GB の CSV ファイルがデータフレームで 10 GB になる場合があります。 

データが圧縮されている場合はさらに拡張できます。つまり圧縮 parquet 形式の比較的まばらな格納データが 20 GB ある場合、メモリ内で最大 800 GB まで拡張できます。 Parquet ファイルはデータを列形式で格納するため、必要な列が半分のみ場合は、メモリ内に読み込む必要があるのは最大 400 GB のみです。

[Azure Machine Learning でのデータ処理の最適化の詳細について確認してください](concept-optimize-data-processing.md)。

## <a name="dataset-types"></a>データセットの種類

データセットには、ユーザーがトレーニングでそれらを使用する方法に基づいて、FileDataset と TabularDataset の 2 つの種類があります。 どちらの種類も、推定器、AutoML、hyperDrive、パイプラインが含まれる Azure Machine Learning のトレーニング ワークフローで使用できます。 

### <a name="filedataset"></a>FileDataset

[FileDataset](/python/api/azureml-core/azureml.data.file_dataset.filedataset?preserve-view=true&view=azure-ml-py) は、データストアまたはパブリック URL 内の 1 つまたは複数のファイルを参照します。 データがクレンジング済みで、トレーニング実験で使用できる状態になっている場合は、ファイルを FileDataset オブジェクトとしてコンピューティングに[ダウンロードまたはマウント](how-to-train-with-datasets.md#mount-vs-download)できます。 

ソース ファイルはどのような形式でもよく、ディープ ラーニングなどの、より幅広い機械学習シナリオが可能になるため、機械学習のワークフローには FileDataset を使用することをお勧めします。

FileDataset を作成するには、[Python SDK](#create-a-filedataset) または [Azure Machine Learning Studio](how-to-connect-data-ui.md#create-datasets) を使用します。
### <a name="tabulardataset"></a>TabularDataset

[TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py) は、指定されたファイルまたはファイルのリストを解析して、データを表形式で表します。 これにより、データを Pandas または Spark DataFrame に具体化できるため、ノートブックを離れることなく、使い慣れたデータ準備とトレーニングのライブラリを使用することができます。 `TabularDataset` オブジェクトは、.csv ファイル、.tsv ファイル、.parquet ファイル、.jsonl ファイル、[SQL クエリ結果](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?preserve-view=true&view=azure-ml-py#&preserve-view=truefrom-sql-query-query--validate-true--set-column-types-none--query-timeout-30-)から作成できます。

TabularDataset を使用すると、データの列から、またはデータが格納されている任意のパス パターンからタイム スタンプを指定して、時系列特性を有効にすることができます。 この指定により、時間による簡単かつ効率的なフィルター処理が可能になります。 例については、「[NOAA 気象データを使用した表形式の時系列関連の API のデモ](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb)」を参照してください。

TabularDataset は、[Python SDK](#create-a-tabulardataset) または [Azure Machine Learning Studio](how-to-connect-data-ui.md#create-datasets) を使用して作成します。

>[!NOTE]
> 現在、Azure Machine Learning Studio を使用して生成された AutoML ワークフローでは、TabularDataset のみをがポートされています。 

## <a name="access-datasets-in-a-virtual-network"></a>仮想ネットワーク内のデータセットにアクセスする

ワークスペースが仮想ネットワーク内にある場合は、検証をスキップするようにデータセットを構成する必要があります。 仮想ネットワークでデータストアとデータセットを使用する方法の詳細については、[ワークスペースと関連付けられているリソースをセキュリティで保護する](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets)方法に関するページを参照してください。

<a name="datasets-sdk"></a>

## <a name="create-datasets"></a>データセットを作成する

データを Azure Machine Learning からアクセスできるようにするには、[Azure データストア](how-to-access-data.md)またはパブリック Web URL のパスからデータセットを作成する必要があります。 

Python SDK を使用して [Azure データストア](how-to-access-data.md)からデータセットを作成するには:

1. 登録された Azure データストアへの `contributor` または `owner` アクセス権を持っていることを確認します。

2. データストア内のパスを参照してデータセットを作成します。 複数のデータストア内の複数のパスからデータセットを作成できます。 データセットの作成元として使用できるファイルの数やデータ サイズには、厳密な制限はありません。 

> [!NOTE]
> データ パスごとに、いくつかの要求がストレージ サービスに送信され、ファイルまたはフォルダーを指しているかどうかが確認されます。 このオーバーヘッドによって、パフォーマンスが低下したり、エラーが発生したりする可能性があります。 1000 個のファイルを含む 1 つのフォルダーを参照するデータセットは、1 つのデータ パスを参照していると見なされます。 最適なパフォーマンスを得るために、データストア内の 100 未満のパスを参照するデータセットを作成することをお勧めします。

### <a name="create-a-filedataset"></a>FileDataset を作成する

任意の形式のファイルを読み込み、登録されていない FileDataset を作成するには、`FileDatasetFactory` クラスの [`from_files()`](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?preserve-view=true&view=azure-ml-py#&preserve-view=truefrom-files-path--validate-true-) メソッドを使用します。 

ストレージが仮想ネットワークまたはファイアウォールの内側にある場合は、`from_files()` メソッドでパラメーター `validate=False` を設定します。 これにより、最初の検証手順がバイパスされ、セキュリティで保護されたこれらのファイルからデータセットを作成できるようになります。 [仮想ネットワークでデータストアとデータセットを使用する](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets)方法に関する詳細を確認してください。

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```
ワークスペースの実験間でデータセットを再利用および共有するには、[データセットを登録](#register-datasets)します。 

> [!TIP] 
> ローカル ディレクトリからファイルをアップロードし、パブリック プレビュー メソッドの [upload_directory()](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?preserve-view=true&view=azure-ml-py#upload-directory-src-dir--target--pattern-none--overwrite-false--show-progress-true-) を使用して 1 つのメソッドで FileDataset を作成します。 このメソッドは[試験段階](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#stable-vs-experimental)のプレビュー機能であり、いつでも変更される可能性があります。 
> 
>  このメソッドでは基になるストレージにデータがアップロードされるため、ストレージ コストが発生します。 

### <a name="create-a-tabulardataset"></a>TabularDataset を作成する

.csv 形式または .tsv 形式のファイルを読み取り、登録されていない TabularDataset を作成するには、`TabularDatasetFactory` クラスの [`from_delimited_files()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) メソッドを使用します。 複数のファイルから読み取る場合、結果は 1 つの表形式に集計されます。 

ストレージが仮想ネットワークまたはファイアウォールの内側にある場合は、`from_delimited_files()` メソッドでパラメーター `validate=False` を設定します。 これにより、最初の検証手順がバイパスされ、セキュリティで保護されたこれらのファイルからデータセットを作成できるようになります。 [仮想ネットワークでデータストアとデータセット](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets)を使用する方法に関する詳細を確認してください。

次のコードでは、既存のワークスペースと必要なデータストアを名前で取得します。 次に、データストアとファイルの場所を `path` パラメーターに渡して、新しい TabularDataset である `weather_ds` を作成します。

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 file paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]

weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```
### <a name="set-data-schema"></a>データ スキーマを設定する

既定では、TabularDataset を作成するときに、列のデータ型は自動的に推論されます。 推論された型が期待どおりでない場合は、次のコードを使用して列の型を指定することでデータセット スキーマを更新できます。 パラメーター `infer_column_type` は、区切りファイルから作成されたデータセットにのみ適用されます。 [サポートされているデータ型の詳細を確認してください](/python/api/azureml-core/azureml.data.dataset_factory.datatype?preserve-view=true&view=azure-ml-py)。


```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|(インデックス)|PassengerId|Survived|Pclass|名前|Sex|Age|SibSp|Parch|Ticket|Fare|Cabin|Embarked
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund, Mr. Owen Harris|male|22.0|1|0|A/5 21171|7.2500||S
1|2|True|1|Cumings, Mrs. John Bradley (Florence Briggs Th...|female|38.0|1|0|PC 17599|71.2833|C85|C
2|3|True|3|Heikkinen, Miss. Laina|female|26.0|0|0|STON/O2. 3101282|7.9250||S

ワークスペースの実験間でデータセットを再利用および共有するには、[データセットを登録](#register-datasets)します。

## <a name="explore-data"></a>データの探索

データセットを作成して[登録](#register-datasets)したら、モデルのトレーニングの前に、データ探索用のノートブックにそれを読み込むことができます。 データ探索を実行する必要がない場合は、[データセットを使用したトレーニング](how-to-train-with-datasets.md)に関する記事に記載されている、ML 実験の送信用のトレーニング スクリプトでデータセットを使用する方法について参照してください。

FileDatasets の場合は、データセットを **マウント** または **ダウンロード** して、データ探索に通常使用する python ライブラリを適用します。 [マウントとダウンロードの詳細情報](how-to-train-with-datasets.md#mount-vs-download)。

```python
# download the dataset 
dataset.download(target_path='.', overwrite=False) 

# mount dataset to the temp directory at `mounted_path`

import tempfile
mounted_path = tempfile.mkdtemp()
mount_context = dataset.mount(mounted_path)

mount_context.start()
```

TabularDatasets の場合は、[`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) メソッドを使用して、データフレーム内のデータを表示します。 

```python
# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|(インデックス)|PassengerId|Survived|Pclass|名前|Sex|Age|SibSp|Parch|Ticket|Fare|Cabin|Embarked
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund, Mr. Owen Harris|male|22.0|1|0|A/5 21171|7.2500||S
1|2|True|1|Cumings, Mrs. John Bradley (Florence Briggs Th...|female|38.0|1|0|PC 17599|71.2833|C85|C
2|3|True|3|Heikkinen, Miss. Laina|female|26.0|0|0|STON/O2. 3101282|7.9250||S

## <a name="create-a-dataset-from-pandas-dataframe"></a>Pandas データフレーム からデータセットを作成する

メモリ内の Pandas データ フレームから TabularDataset を作成するには、csv などのローカル ファイルにデータを書き込み、そのファイルからデータセットを作成します。 次のコードはこのワークフローを示しています。

```python
# azureml-core of version 1.0.72 or higher is required
# azureml-dataprep[pandas] of version 1.1.34 or higher is required

from azureml.core import Workspace, Dataset
local_path = 'data/prepared.csv'
dataframe.to_csv(local_path)

# upload the local file to a datastore on the cloud

subscription_id = 'xxxxxxxxxxxxxxxxxxxxx'
resource_group = 'xxxxxx'
workspace_name = 'xxxxxxxxxxxxxxxx'

workspace = Workspace(subscription_id, resource_group, workspace_name)

# get the datastore to upload prepared data
datastore = workspace.get_default_datastore()

# upload the local file from src_dir to the target_path in datastore
datastore.upload(src_dir='data', target_path='data')

# create a dataset referencing the cloud location
dataset = Dataset.Tabular.from_delimited_files(path = [(datastore, ('data/prepared.csv'))])
```

> [!TIP]
> 1 つのメソッドで、[`register_spark_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?preserve-view=true&view=azure-ml-py#methods) と [`register_pandas_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?preserve-view=true&view=azure-ml-py#methods) のパブリック プレビュー メソッドを使用して、メモリ内のスパークや Pandas データフレームから TabularDataset を作成して登録します。 これらの登録メソッドは[試験段階の](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#stable-vs-experimental)プレビュー機能であり、いつでも変更される可能性があります。 
> 
>  これらのメソッドでは基になるストレージにデータがアップロードされるため、ストレージ コストが発生します。 

## <a name="register-datasets"></a>データセットを登録する

作成プロセスを完了するには、ワークスペースにデータセットを登録します。 データ セットを他のユーザーと共有したり、ワークスペース内の実験で再利用できるように、ご使用のワークスペースに登録するには、[`register()`](/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-workspace--name--description-none--tags-none--create-new-version-false-) メソッドを使用します。

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-using-azure-resource-manager"></a>Azure Resource Manager を使用してデータセットを作成する

[https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-dataset-create-*](https://github.com/Azure/azure-quickstart-templates/tree/master/) には、データセットの作成に使用できる多数のテンプレートがあります。

これらのテンプレートの使用に関する詳細については、「[Azure Resource Manager テンプレートを使用して Azure Machine Learning のワークスペースを作成します](how-to-create-workspace-template.md)」を参照してください。


## <a name="create-datasets-with-azure-open-datasets"></a>Azure Open Datasets を使用してデータセットを作成する

[Azure オープン データセット](https://azure.microsoft.com/services/open-datasets/)は選別されたパブリック データセットであり、機械学習ソリューションにシナリオ固有の機能を追加してモデルの精度を上げるために使用できます。 データセットには、機械学習モデルのトレーニングと予測ソリューションのエンリッチメントに役立つ天気、国勢調査、祝日、公共の安全、場所に関するパブリック ドメイン データが含まれます。 Open Datasets は Microsoft Azure 上のクラウドにあり、SDK と Studio の両方に含まれています。

[Azure Open Datasets から Azure Machine Learning データセット](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md)を作成する方法を確認してください。 

## <a name="train-with-datasets"></a>データセットを使用したトレーニング

ML モデルのトレーニングのために、機械学習の実験でデータセットを使用します。 [データセットを使ってトレーニングする方法の詳細をご覧ください](how-to-train-with-datasets.md)

## <a name="version-datasets"></a>データセットをバージョン管理する

新しいバージョンを作成すると、新しいデータセットを同じ名前で登録できます。 データセットのバージョンは、実験または将来の複製に対して特定のバージョンのデータセットを適用できるように、データの状態をブックマークする方法です。 [データセットのバージョンの詳細](how-to-version-track-datasets.md)を参照してください。
```Python
# create a TabularDataset from Titanic training data
web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
             'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```

## <a name="next-steps"></a>次のステップ

* [データセットを使ってトレーニングする方法](how-to-train-with-datasets.md)をご覧ください。
* 自動機械学習を使用し、[TabularDataset でトレーニング](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)を行います。
* データセットのトレーニングのその他の例については、[サンプル ノートブック](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)を参照してください。
