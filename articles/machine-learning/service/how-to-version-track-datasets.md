---
title: データセットのバージョン管理
titleSuffix: Azure Machine Learning service
description: データセットを最適にバージョン管理する方法と、機械学習パイプラインでバージョン管理がどのように機能するかについて説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: sihhu
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.custom: ''
ms.openlocfilehash: 6940b6ecc231befba908271920e31d4821338baa
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928955"
---
# <a name="version-and-track-datasets-in-experiments"></a>実験でデータセットをバージョン管理して追跡する
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、再現性のために Azure Machine Learning データセットをバージョン管理する方法について説明します。 データセットのバージョン管理は、将来の実験のために特定のバージョンのデータセットを適用できるように、データの状態をブックマークする方法です。

一般的なバージョン管理のシナリオ:

* 再トレーニングのために新しいデータを使用できるとき
* 異なるデータ準備または特徴エンジニアリングのアプローチを適用しようとしているとき

## <a name="prerequisites"></a>前提条件

このチュートリアルには、次のものが必要です。

- [Azure Machine Learning SDK for Python がインストール](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)されていること。 この SDK には、[azureml-datasets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py) パッケージが含まれています。
    
- [Azure Machine Learning ワークスペース](concept-workspace.md)。 次のコードを実行して既存のものを取得するか、[新しいワークスペースを作成](how-to-manage-workspace.md)します。

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- [Azure Machine Learning のデータセット](how-to-create-register-datasets.md)。

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>データセットのバージョンを登録および取得する

データセットを登録することで、複数の実験にわたり、同僚との間でバージョン管理、再利用、共有を行うことができます。 複数のデータセットを同じ名前で登録し、名前とバージョン番号で特定のバージョンを取得することができます。

### <a name="register-a-dataset-version"></a>データセットのバージョンを登録する

次のコードでは、`create_new_version` パラメーターを `True` に設定することによって、新しいバージョンの `titanic_ds` データセットを登録します。 ワークスペースに登録されている既存の `titanic_ds` データセットがない場合、コードは `titanic_ds` という名前で新しいデータセットを作成し、そのバージョンを 1 に設定します。

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```

### <a name="retrieve-a-dataset-by-name"></a>名前でデータセットを取得する

既定では、`Dataset` クラスの [get_by_name()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) メソッドでは、ワークスペースに登録されているデータセットの最新バージョンが返されます。 

次のコードでは、`titanic_ds` データセットのバージョン 1 を取得します。

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>バージョン管理のベスト プラクティス

データセットのバージョンを作成するときには、ワークスペースでデータの余分なコピーを作成*しない*ようにします。 データセットはストレージ サービス内のデータへの参照であるため、あるのはストレージ サービスによって管理される単一のデータ ソースです。

>[!IMPORTANT]
> データセットによって参照されるデータが上書きまたは削除された場合、特定バージョンのデータセットを呼び出しても変更は元に戻され*ません*。

データセットからデータを読み込むときは常に、データセットによって参照された現在のデータ コンテンツが読み込まれます。 データセットの各バージョンが再現可能であることを確認する場合、データセットのバージョンによって参照されるデータ コンテンツは変更しないことをお勧めします。 新しいデータが到着したら、新しいデータ ファイルを別個のデータ フォルダーに保存してから、新しいデータセット バージョンを作成し、その新しいフォルダーのデータを含めます。

次の図とサンプル コードは、データ フォルダーを構成し、それらのフォルダーを参照するデータセット バージョンを作成するための推奨される方法を示しています。

![フォルダー構造](media/how-to-version-datasets/folder-image.png)

```Python
from azureml.core import Dataset

# get the default datastore of the workspace
datastore = workspace.get_default_datastore()

# create & register weather_ds version 1 pointing to all files in the folder of week 27
datastore_path1 = [(datastore, 'Weather/week 27')]
dataset1 = Dataset.File.from_files(path=datastore_path1)
dataset1.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27',
                  create_new_version = True)

# create & register weather_ds version 2 pointing to all files in the folder of week 27 and 28
datastore_path2 = [(datastore, 'Weather/week 27'), (datastore, 'Weather/week 28')]
dataset2 = Dataset.File.from_files(path = datastore_path2)
dataset2.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27, 28',
                  create_new_version = True)

```

<a name="pipeline"></a>

## <a name="version-a-pipeline-output-dataset"></a>パイプライン出力データセットをバージョン管理する

データセットは、Machine Learning の各パイプライン ステップの入力および出力として使用できます。 パイプラインを再実行すると、各パイプライン ステップの出力は新しいデータセット バージョンとして登録されます。

パイプラインを再実行するたびに、Machine Learning パイプラインによって、各ステップの出力が新しいフォルダーに格納されるので、バージョン管理された出力データセットは再現可能となります。

```Python
from azureml.core import Dataset
from azureml.pipeline.steps import PythonScriptStep
from azureml.pipeline.core import Pipeline, PipelineData
from azureml.core. runconfig import CondaDependencies, RunConfiguration

# get input dataset 
input_ds = Dataset.get_by_name(workspace, 'weather_ds')

# register pipeline output as dataset
output_ds = PipelineData('prepared_weather_ds', datastore=datastore).as_dataset()
output_ds = output_ds.register(name='prepared_weather_ds', create_new_version=True)

conda = CondaDependencies.create(
    pip_packages=['azureml-defaults', 'azureml-dataprep[fuse,pandas]'], 
    pin_sdk_version=False)

run_config = RunConfiguration()
run_config.environment.docker.enabled = True
run_config.environment.python.conda_dependencies = conda

# configure pipeline step to use dataset as the input and output
prep_step = PythonScriptStep(script_name="prepare.py",
                             inputs=[input_ds.as_named_input('weather_ds')],
                             outputs=[output_ds],
                             runconfig=run_config,
                             compute_target=compute_target,
                             source_directory=project_folder)
```

<a name="track"></a>

## <a name="track-datasets-in-experiments"></a>実験のデータセットを追跡する

各機械学習の実験ごとに、実験 `Run` オブジェクトを介して、入力として使用されるデータセットを簡単に追跡できます。

次のコードでは、[`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-details--) メソッドを使用して、実験の実行でどの入力データセットが使用されたかを追跡します。

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
input_dataset = inputs[0]['dataset']

# list the files referenced by input_dataset
input_dataset.to_path()
```

また、[Azure Machine Learning Studio](https://ml.azure.com/) を使用して、実験から `input_datasets` を見つけることもできます。 

次の図は、Azure Machine Learning Studio で実験の入力データセットを探す場所を示しています。 この例では、 **[実験]** ペインに移動し、実験 `keras-mnist` の特定の実行について **[プロパティ]** タブを開きます。

![入力データセット](media/how-to-version-datasets/input-datasets.png)

次のコードを使用して、モデルをデータセットに登録します。

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

登録後は、Python または [Azure Machine Learning Studio](https://ml.azure.com/) を使用して、データセットに登録されたモデルのリストを表示できます。 次のビューは、 **[資産]** の下の **[データセット]** ペインからのものです。 データセットを選択して、 **[モデル]** タブを選択し、そのデータセットで登録されたモデルのリストを表示します。 

![入力データセットのモデル](media/how-to-version-datasets/dataset-models.png)

## <a name="next-steps"></a>次の手順

* [データセットを使ってトレーニングする](how-to-train-with-datasets.md)
* [その他のサンプル データセット ノートブック](https://aka.ms/dataset-tutorial)
