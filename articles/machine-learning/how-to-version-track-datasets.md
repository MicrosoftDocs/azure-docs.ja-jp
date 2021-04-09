---
title: データセットのバージョン管理
titleSuffix: Azure Machine Learning
description: 機械学習のデータセットをバージョン管理する方法と、機械学習パイプラインでバージョン管理がどのように機能するかについて説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: fde25e4ba75bfb86c9837582d7168f85335836b6
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102520542"
---
# <a name="version-and-track-azure-machine-learning-datasets"></a>Azure Machine Learning データセットをバージョン管理および追跡する

この記事では、再現性のために Azure Machine Learning データセットをバージョン管理する方法について説明します。 データセットのバージョン管理は、将来の実験のために特定のバージョンのデータセットを適用できるように、データの状態をブックマークする方法です。

一般的なバージョン管理のシナリオ:

* 再トレーニングのために新しいデータを使用できるとき
* 異なるデータ準備または特徴エンジニアリングのアプローチを適用しようとしているとき

## <a name="prerequisites"></a>前提条件

このチュートリアルには、次のものが必要です。

- [Azure Machine Learning SDK for Python がインストール](/python/api/overview/azure/ml/install)されていること。 この SDK には、[azureml-datasets](/python/api/azureml-core/azureml.core.dataset) パッケージが含まれています。
    
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

既定では、`Dataset` クラスの [get_by_name()](/python/api/azureml-core/azureml.core.dataset.dataset#get-by-name-workspace--name--version--latest--) メソッドでは、ワークスペースに登録されているデータセットの最新バージョンが返されます。 

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

データセットのバージョンを作成するときには、ワークスペースでデータの余分なコピーを作成 *しない* ようにします。 データセットはストレージ サービス内のデータへの参照であるため、あるのはストレージ サービスによって管理される単一のデータ ソースです。

>[!IMPORTANT]
> データセットによって参照されるデータが上書きまたは削除された場合、特定バージョンのデータセットを呼び出しても変更は元に戻され *ません*。

データセットからデータを読み込むときは常に、データセットによって参照された現在のデータ コンテンツが読み込まれます。 データセットの各バージョンが再現可能であることを確認する場合、データセットのバージョンによって参照されるデータ コンテンツは変更しないことをお勧めします。 新しいデータが到着したら、新しいデータ ファイルを別個のデータ フォルダーに保存してから、新しいデータセット バージョンを作成し、その新しいフォルダーのデータを含めます。

次の図とサンプル コードは、データ フォルダーを構成し、それらのフォルダーを参照するデータセット バージョンを作成するための推奨される方法を示しています。

![フォルダー構造](./media/how-to-version-track-datasets/folder-image.png)

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

## <a name="version-an-ml-pipeline-output-dataset"></a>ML パイプライン出力データセットをバージョン管理する

データセットは、[ML の各パイプライン](concept-ml-pipelines.md) ステップの入力および出力として使用できます。 パイプラインを再実行すると、各パイプライン ステップの出力は新しいデータセット バージョンとして登録されます。

パイプラインを再実行するたびに、ML パイプラインによって、各ステップの出力が新しいフォルダーに格納されます。 この動作により、バージョン管理された出力データセットを再現できます。 パイプラインのデータセットについては[こちら](./how-to-create-machine-learning-pipelines.md#steps)をご覧ください。

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

## <a name="track-data-in-your-experiments"></a>実験のデータを追跡する

Azure Machine Learning を使用すると、入力と出力のデータセットとして実験全体のデータが追跡されます。  

データが **入力データセット** として追跡されるシナリオを次に示します。 

* 実験の実行を送信するときに、`ScriptRunConfig` オブジェクトの `inputs` または `arguments` のいずれかのパラメーターを使用して `DatasetConsumptionConfig` オブジェクトとして。 

* get_by_name() または get_by_id() のようなメソッドがスクリプトで呼び出される場合。 このシナリオでは、ワークスペースに登録したときにデータセットに割り当てられた名前が表示されます。 

データが **出力データセット** として追跡されるシナリオを次に示します。  

* 実験の実行を送信するときに、`outputs` または `arguments` のいずれかのパラメーターを使用して `OutputFileDatasetConfig` オブジェクトを渡す。 `OutputFileDatasetConfig` オブジェクトを使用して、パイプライン ステップ間でデータを保持することもできます。 [ML パイプラインのステップ間でのデータの移動](how-to-move-data-in-out-of-pipelines.md)に関するページを参照してください。
  
* スクリプトにデータセットを登録する。 このシナリオでは、ワークスペースに登録したときにデータセットに割り当てられた名前が表示されます。 次の例では、`training_ds` が表示される名前です。

    ```Python
   training_ds = unregistered_ds.register(workspace = workspace,
                                     name = 'training_ds',
                                     description = 'training data'
                                     )
    ```

* スクリプトに登録されていないデータセットを使用して子の実行を送信します。 これにより、匿名で保存されたデータセットが生成されます。

### <a name="trace-datasets-in-experiment-runs"></a>実験の実行でのデータセットのトレース

Machine Learning の実験ごとに、実験 `Run` オブジェクトを使用して、入力として使用されるデータセットを簡単に追跡できます。

次のコードでは、[`get_details()`](/python/api/azureml-core/azureml.core.run.run#get-details--) メソッドを使用して、実験の実行でどの入力データセットが使用されたかを追跡します。

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
input_dataset = inputs[0]['dataset']

# list the files referenced by input_dataset
input_dataset.to_path()
```

また、[Azure Machine Learning スタジオ]()を使用して、実験から `input_datasets` を見つけることもできます。 

次の図は、Azure Machine Learning Studio で実験の入力データセットを探す場所を示しています。 この例では、 **[実験]** ペインに移動し、実験 `keras-mnist` の特定の実行について **[プロパティ]** タブを開きます。

![入力データセット](./media/how-to-version-track-datasets/input-datasets.png)

次のコードを使用して、モデルをデータセットに登録します。

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

登録後は、Python を使用してデータセットに登録されたモデルのリストを表示できます。[スタジオ](https://ml.azure.com/)に進む方法もあります。

次のビューは、 **[資産]** の下の **[データセット]** ペインからのものです。 データセットを選択して、 **[モデル]** タブを選択し、そのデータセットで登録されたモデルのリストを表示します。 

![入力データセットのモデル](./media/how-to-version-track-datasets/dataset-models.png)

## <a name="next-steps"></a>次のステップ

* [データセットを使ってトレーニングする](how-to-train-with-datasets.md)
* [その他のサンプル データセット ノートブック](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)