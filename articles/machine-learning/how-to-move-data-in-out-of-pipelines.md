---
title: ML パイプラインでのデータの移動
titleSuffix: Azure Machine Learning
description: Azure Machine Learning パイプラインでのデータの入出力について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 08/20/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4, devx-track-python
ms.openlocfilehash: 1b6b5af2e6533c13165ae8253813a52b2c7ad261
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2020
ms.locfileid: "88756964"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>ML パイプラインのステップ間でのデータの移動 (Python)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、Azure Machine Learning パイプラインのステップ間でデータをインポート、変換、および移動するためのコードを示します。 Azure Machine Learning でデータがどのように動作するかの概要については、[Azure ストレージ サービスのデータへのアクセス](how-to-access-data.md)に関する記事を参照してください。 Azure Machine Learning パイプラインの利点と構造については、「[Azure Machine Learning パイプラインとは](concept-ml-pipelines.md)」を参照してください。

この記事では、次の操作方法について説明します。

- 既存のデータに `Dataset` オブジェクトを使用する
- ステップ内でデータにアクセスする
- トレーニングや検証のサブセットなど、`Dataset` データをサブセットに分割する
- 次のパイプライン ステップにデータを転送する `OutputFileDatasetConfig` オブジェクトを作成する
- パイプライン ステップへの入力として `OutputFileDatasetConfig` オブジェクトを使用する
- 永続化する `OutputFileDatasetConfig` から新しい `Dataset` オブジェクトを作成する

> [!NOTE]
>`OutputFileDatasetConfig` クラスと `OutputTabularDatasetConfig` クラスは試験段階のプレビュー機能であり、いつでも変更される可能性があります。
>
>詳細については、「https://aka.ms/azuremlexperimental」を参照してください。

## <a name="prerequisites"></a>前提条件

必要なものは次のとおりです。

- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) をお試しください。

- [Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)、または [Azure Machine Learning Studio](https://ml.azure.com/) へのアクセス。

- Azure Machine Learning ワークスペース。
  
  [Azure Machine Learning ワークスペースを作成](how-to-manage-workspace.md)するか、Python SDK を介して既存のワークスペースを使用します。 `Workspace` および `Datastore` クラスをインポートし、関数 `from_config()` を使用してファイル `config.json` からサブスクリプション情報を読み込みます。 この関数により、既定で現在のディレクトリ内の JSON ファイルが検索されますが、`from_config(path="your/file/path")` を使用してパス パラメーターを指定してファイルを指すこともできます。

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- 一部の既存のデータ。 この記事では、[Azure Blob コンテナー](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)の使用方法について簡単に説明します。

- 省略可能:既存の機械学習パイプライン ([Azure Machine Learning SDK を使用した機械学習パイプラインの作成および実行](how-to-create-your-first-pipeline.md)に関する記事に記載されているものなど)。

## <a name="use-dataset-objects-for-pre-existing-data"></a>既存のデータに `Dataset` オブジェクトを使用する 

パイプラインにデータを取り込む方法としては、[データセット](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) オブジェクトを使用することをお勧めします。 `Dataset` オブジェクトは、ワークスペース全体で使用できる永続データを表します。

`Dataset` オブジェクトを作成および登録する方法は多数あります。 表形式データセットは、1つまたは複数のファイルで使用できる区切られたデータ用です。 ファイル データセットは、バイナリ データ (画像など) または解析するデータ用です。 `Dataset` オブジェクトを作成する最も簡単なプログラム的方法は、ワークスペース ストレージまたはパブリック URL で既存の Blob を使用することです。

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

cats_dogs_dataset = Dataset.File.from_files(
    paths='https://download.microsoft.com/download/3/E/1/3E1C3F21-ECDB-4869-8368-6DEBA77B919F/kagglecatsanddogs_3367a.zip',
    archive_options=ArchiveOptions(archive_type=ArchiveType.ZIP, entry_glob='**/*.jpg')
)
```

各種のオプションとさまざまなソースからのデータセットの作成、それらの登録と Azure Machine Learning UI での確認、データ サイズがコンピューティング能力とどのように相互作用するかの理解、およびそれらのバージョン管理についての詳細は、「[Azure Machine Learning データセットを作成する](how-to-create-register-datasets.md)」を参照してください。 

### <a name="pass-datasets-to-your-script"></a>データセットをスクリプトに渡す

データセットのパスをスクリプトに渡すには、`Dataset` オブジェクトの `as_named_input()` メソッドを使用します。 生成された `DatasetConsumptionConfig` オブジェクトを引数としてスクリプトに渡すか、またはパイプライン スクリプトへの `inputs` 引数を使用して、`Run.get_context().input_datasets[]` を使ってデータセットを取得することができます。

名前付き入力を作成したら、アクセス モード (`as_mount()` または `as_download()`) を選択できます。 スクリプトがデータセット内のすべてのファイルを処理し、コンピューティング リソースのディスクがデータセットに対して十分な大きさである場合は、ダウンロード アクセス モードを選択することをお勧めします。 ダウンロード アクセス モードを使用すると、実行時のデータ ストリーミングのオーバーヘッドを回避できます。 スクリプトがデータセットのサブセットにアクセスする場合、またはそれがコンピューティングに対して大きすぎる場合は、マウント アクセス モードを使用します。 詳細については、「[マウントとダウンロード](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets#mount-vs-download)」をご覧ください

データセットをパイプライン ステップに渡すには、次の手順を実行します。

1. `TabularDataset.as_named_inputs()` または `FileDataset.as_named_input()` (末尾に ’s’ はありません) を使用して `DatasetConsumptionConfig` オブジェクトを作成します
1. `as_mount()` または `as_download()` を使用して、アクセス モードを設定します
1. `arguments` または `inputs` 引数のいずれかを使用して、データセットをパイプライン ステップに渡します

次のスニペットは、`PythonScriptStep` コンストラクター内でこれらのステップを組み合わせた一般的なパターンを示しています。 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_inputs('iris').as_mount()]
)
```

また、`random_split()` や `take_sample()` などのメソッドを使用して、複数の入力を作成したり、パイプライン ステップに渡されるデータの量を減らしたりすることもできます。

```python
seed = 42 # PRNG seed
smaller_dataset = iris_dataset.take_sample(0.1, seed=seed) # 10%
train, test = smaller_dataset.random_split(percentage=0.8, seed=seed)

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[train.as_named_inputs('train').as_download(), test.as_named_inputs('test').as_download()]
)
```

### <a name="access-datasets-within-your-script"></a>スクリプト内でデータセットにアクセスする

パイプライン ステップ スクリプトへの名前付き入力は、`Run` オブジェクト内でディクショナリとして使用できます。 `Run.get_context()` を使用してアクティブな `Run` オブジェクトを取得し、`input_datasets` を使用して名前付き入力のディクショナリを取得します。 `inputs` 引数ではなく `arguments` 引数を使用して `DatasetConsumptionConfig` オブジェクトを渡した場合は、`ArgParser` コードを使用してデータにアクセスします。 次のスニペットでは、両方の手法が示されています。

```python
# In pipeline definition script:
# Code for demonstration only: It would be very confusing to split datasets between `arguments` and `inputs`
train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    arguments=['--training-folder', train.as_named_inputs('train').as_download()]
    inputs=[test.as_named_inputs('test').as_download()]
)

# In pipeline script
parser = argparse.ArgumentParser()
parser.add_argument('--training-folder', type=str, dest='train_folder', help='training data folder mounting point')
args = parser.parse_args()
training_data_folder = args.train_folder

testing_data_folder = Run.get_context().input_datasets['test']
```

渡される値は、データセット ファイルのパスになります。

登録済みの `Dataset` に直接アクセスすることもできます。 登録済みのデータセットは永続的であり、ワークスペース全体で共有されるため、それらを直接取得できます。

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

## <a name="use-outputfiledatasetconfig-for-intermediate-data"></a>中間データに `OutputFileDatasetConfig` を使用する

`Dataset` オブジェクトは永続データを表しますが、[`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py) オブジェクトはパイプライン ステップから出力される一時的なデータ**および**永続出力データに使用できます。 

 `OutputFileDatasetConfig` オブジェクトの既定の動作では、ワークスペースの既定のデータストアに書き込みます。 `arguments` パラメーターを使用して `OutputFileDatasetConfig` オブジェクトを `PythonScriptStep` に渡します。

```python
from azureml.data import OutputFileDatasetConfig
dataprep_output = OutputFileDatasetConfig()
input_dataset = Dataset.get_by_name(workspace, 'raw_data')

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=[input_dataset.as_named_input('raw_data').as_mount(), dataprep_output]
    )
```

実行の終わりに `OutputFileDatasetConfig` オブジェクトのコンテンツをアップロードすることを選択できます。 その場合、`OutputFileDatasetConfig` オブジェクトと共に `as_upload()` 関数を使用し、アップロード先で既存のファイルを上書きするかどうかを指定します。 

```python
#get blob datastore already registered with the workspace
blob_store= ws.datastores['my_blob_store']
OutputFileDatasetConfig(name="clean_data", destination=blob_store).as_upload(overwrite=False)
```

### <a name="use-outputfiledatasetconfig-as-outputs-of-a-training-step"></a>トレーニング ステップの出力として `OutputFileDatasetConfig` を使用する

パイプラインの `PythonScriptStep` 内で、プログラムの引数を使用して、使用可能な出力パスを取得できます。 このステップが最初のものであり、出力データを初期化する場合は、指定されたパスにディレクトリを作成する必要があります。 その後、`OutputFileDatasetConfig` に含める任意のファイルを書き込むことができます。

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

### <a name="read-outputfiledatasetconfig-as-inputs-to-non-initial-steps"></a>最初以外のステップへの入力として `OutputFileDatasetConfig` を読み取る

最初のパイプライン ステップで `OutputFileDatasetConfig` パスにデータが書き込まれ、それがその最初のステップの出力になった場合、それを後のステップへの入力として使用できます。 

次のコードで、 

* `step1_output_data` は、PythonScriptStep の出力である `step1` が、アップロード アクセス モードで、ADLS Gen 2 データストア `my_adlsgen2` に書き込まれることを示します。 ADLS Gen 2 データストアにデータを書き戻すには、[ロールのアクセス許可を設定する](how-to-access-data.md#azure-data-lake-storage-generation-2)方法を参照してください。 

* `step1` が完了し、`step1_output_data` によって示される書き込み先に出力が書き込まれると、手順 2 で入力として `step1_output_data` を使用する準備ができます。 

```python
# get adls gen 2 datastore already registered with the workspace
datastore = workspace.datastores['my_adlsgen2']
step1_output_data = OutputFileDatasetConfig(name="processed_data", destination=datastore).as_upload()

step1 = PythonScriptStep(
    name="generate_data",
    script_name="step1.py",
    runconfig = aml_run_config,
    arguments = ["--output_path", step1_output_data]
)

step2 = PythonScriptStep(
    name="read_pipeline_data",
    script_name="step2.py",
    compute_target=compute,
    runconfig = aml_run_config,
    arguments = ["--pd", step1_output_data.as_input]

)

pipeline = Pipeline(workspace=ws, steps=[step1, step2])
```

## <a name="register-outputfiledatasetconfig-objects-for-reuse"></a>再利用のために `OutputFileDatasetConfig` オブジェクトを登録する

実験期間より長く `OutputFileDatasetConfig` を利用できるようにする場合、ワークスペースにそれを登録し、実験間で共有し、再利用します。

```python
step1_output_ds = step1_output_data.register_on_complete(name='processed_data', 
                                                         description = 'files from step1`)
```

## <a name="next-steps"></a>次のステップ

* [Azure Machine Learning データセットを作成する](how-to-create-register-datasets.md)
* [Azure Machine Learning SDK で機械学習パイプラインを作成して管理する](how-to-create-your-first-pipeline.md)
