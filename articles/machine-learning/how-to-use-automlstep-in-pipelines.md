---
title: ML パイプラインで自動 ML を使用する
titleSuffix: Azure Machine Learning
description: AutoMLStep では、パイプラインで自動機械学習を使用することができます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
manager: cgronlun
ms.date: 02/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, automl
ms.openlocfilehash: 0de3c9a7cf464f38a1a12d8bc19451fb1158a5ad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102520508"
---
# <a name="use-automated-ml-in-an-azure-machine-learning-pipeline-in-python"></a>Python の Azure Machine Learning パイプラインで自動 ML を使用する


Azure Machine Learning の自動 ML 機能は、考えられる方法をすべて再実装することなく、高パフォーマンス モデルを検出するのに役立ちます。 Azure Machine Learning パイプラインと組み合わせることで、データに最適なアルゴリズムをすばやく検出できるデプロイ可能なワークフローを作成できます。 この記事では、データ準備ステップを自動 ML ステップに効率的に結合する方法を示します。 自動 ML では、パイプラインを使用して MLOps およびモデル ライフサイクルの運用化への道に導きながら、データに最適なアルゴリズムをすばやく検出することができます。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。

* Azure Machine Learning ワークスペース。 [Azure Machine Learning ワークスペースを作成する](how-to-manage-workspace.md)方法に関するページを参照してください。  

* Azure の[自動機械学習](concept-automated-ml.md)機能および[機械学習パイプライン](concept-ml-pipelines.md)機能と SDK についての知識。

## <a name="review-automated-mls-central-classes"></a>自動 ML の中心的なクラスを確認する

パイプラインでの自動 ML は、`AutoMLStep` オブジェクトによって表されます。 `AutoMLStep` クラスは `PipelineStep`のサブクラスです。 `PipelineStep` オブジェクトのグラフでは、`Pipeline` を定義します。

`PipelineStep` にはいくつかのサブクラスがあります。 `AutoMLStep` に加え、この記事では、データ準備用とモデルの登録用の `PythonScriptStep` を示します。

データを ML パイプライン "_に_" 最初に移動する場合は、`Dataset` オブジェクトを使用することをお勧めします。 ステップ "_間_" でデータを移動し、場合によっては実行からデータ出力を保存するために、[`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig) および [`OutputTabularDatasetConfig`](/python/api/azureml-core/azureml.data.output_dataset_config.outputtabulardatasetconfig) オブジェクトを使用することをお勧めします。 `AutoMLStep` で使用するには、`PipelineData` オブジェクトを `PipelineOutputTabularDataset` オブジェクトに変換する必要があります。 詳細については、[ML パイプラインからのデータの入力と出力](how-to-move-data-in-out-of-pipelines.md)に関するページを参照してください。

`AutoMLStep` は `AutoMLConfig` オブジェクトを通じて構成されます。 「[Python で自動 ML の実験を構成する](./how-to-configure-auto-train.md#configure-your-experiment-settings)」で説明されているように、`AutoMLConfig` は柔軟なクラスです。 

`Pipeline` は `Experiment` 内で実行されます。 パイプライン `Run` には、ステップごとに子の `StepRun` があります。 自動 ML `StepRun` の出力は、トレーニング メトリックとパフォーマンスが最も高いモデルです。

具体的には、この記事では分類タスクのシンプルなパイプラインを作成します。 タスクではタイタニック号の生存者を予測しますが、データやタスクについては、ここでは受け渡し以外のことは説明しません。

## <a name="get-started"></a>はじめに

### <a name="retrieve-initial-dataset"></a>初期データセットを取得する

多くの場合、ML ワークフローは、既存のベースライン データで開始されます。 これは、登録済みのデータセットに適したシナリオです。 データセットは、ワークスペース全体で表示され、バージョン管理をサポートし、対話形式で探索することができます。 「[Azure Machine Learning データセットを作成する](how-to-create-register-datasets.md)」で説明されているように、データセットを作成して設定するさまざまな方法があります。 ここでは Python SDK を使用してパイプラインを作成するため、その SDK を使用してベースライン データをダウンロードし、'titanic_ds' という名前で登録します。

```python
from azureml.core import Workspace, Dataset

ws = Workspace.from_config()
if not 'titanic_ds' in ws.datasets.keys() :
    # create a TabularDataset from Titanic training data
    web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
                 'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
    titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

    titanic_ds.register(workspace = ws,
                                     name = 'titanic_ds',
                                     description = 'Titanic baseline data',
                                     create_new_version = True)

titanic_ds = Dataset.get_by_name(ws, 'titanic_ds')
```

このコードでは、まず、**config.json** で定義されている Azure Machine Learning ワークスペースにログインします (説明については、「[ワークスペース構成ファイルを作成する](how-to-configure-environment.md#workspace)」を参照してください)。 `'titanic_ds'` という名前のデータセットがまだ登録されていない場合は、1 つ作成されます。 このコードでは、Web から CSV データをダウンロードし、それらを使用して `TabularDataset` をインスタンス化してから、ワークスペースにデータセットを登録します。 最後に、関数 `Dataset.get_by_name()` で `Dataset` を `titanic_ds` に割り当てます。 

### <a name="configure-your-storage-and-compute-target"></a>ストレージおよびコンピューティング ターゲットを構成する

パイプラインで必要となるその他のリソースはストレージであり、一般には Azure Machine Learning コンピューティング リソースです。 

```python
from azureml.core import Datastore
from azureml.core.compute import AmlCompute, ComputeTarget

datastore = ws.get_default_datastore()

compute_name = 'cpu-cluster'
if not compute_name in ws.compute_targets :
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                min_nodes=0,
                                                                max_nodes=1)
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)

    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # Show the result
    print(compute_target.get_status().serialize())

compute_target = ws.compute_targets[compute_name]
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

データ準備と自動 ML ステップの間の中間データは、ワークスペースの既定のデータストアに格納できるため、`Workspace` オブジェクトで `get_default_datastore()` を呼び出す以上のことを行う必要はありません。 

その後、このコードでは、AML コンピューティング ターゲットの `'cpu-cluster'` が既に存在するかどうかを確認します。 そうでない場合、小さな CPU ベースのコンピューティング ターゲットが必要であることを示します。 自動 ML のディープ ラーニング機能 (たとえば、DNN をサポートするテキストの特徴付け) を使用する予定の場合は、「[GPU 最適化済み仮想マシンのサイズ](../virtual-machines/sizes-gpu.md)」で説明されているように、強力な GPU をサポートするコンピューティングを選択する必要があります。 

このコードでは、ターゲットがプロビジョニングされるまでブロックし、作成されたばかりのコンピューティング ターゲットの詳細をいくつか出力します。 最後に、名前が指定されたコンピューティング ターゲットがワークスペースから取得され、`compute_target` に割り当てられます。 

### <a name="configure-the-training-run"></a>トレーニングの実行を構成する

AutoMLStep を使用すると、その依存関係がジョブの送信時に自動的に構成されます。 ランタイム コンテキストを設定するには、`RunConfiguration` オブジェクトを作成して構成します。 ここでは、コンピューティング先を設定します。

```python
from azureml.core.runconfig import RunConfiguration

aml_run_config = RunConfiguration()
# Use just-specified compute target ("cpu-cluster")
aml_run_config.target = compute_target
```

## <a name="prepare-data-for-automated-machine-learning"></a>自動機械学習用にデータを準備する

### <a name="write-the-data-preparation-code"></a>データ準備コードを書き込む

ベースラインのタイタニック号データセットは、数値とテキスト データが混在したもので構成され、一部の値が欠落しています。 それを自動機械学習用に準備する場合、データ準備パイプライン ステップは次のようになります。

- 欠落しているデータを、ランダム データ、または "不明" に対応するカテゴリを使用して入力する
- カテゴリ データを整数に変換する
- 使用する予定のない列を削除する
- データをトレーニングおよびテスト セットに分割する
- 変換後のデータを `OutputFileDatasetConfig` 出力パスに書き込む

```python
%%writefile dataprep.py
from azureml.core import Run

import pandas as pd 
import numpy as np 
import argparse

RANDOM_SEED=42

def prepare_age(df):
    # Fill in missing Age values from distribution of present Age values 
    mean = df["Age"].mean()
    std = df["Age"].std()
    is_null = df["Age"].isnull().sum()
    # compute enough (== is_null().sum()) random numbers between the mean, std
    rand_age = np.random.randint(mean - std, mean + std, size = is_null)
    # fill NaN values in Age column with random values generated
    age_slice = df["Age"].copy()
    age_slice[np.isnan(age_slice)] = rand_age
    df["Age"] = age_slice
    df["Age"] = df["Age"].astype(int)
    
    # Quantize age into 5 classes
    df['Age_Group'] = pd.qcut(df['Age'],5, labels=False)
    df.drop(['Age'], axis=1, inplace=True)
    return df

def prepare_fare(df):
    df['Fare'].fillna(0, inplace=True)
    df['Fare_Group'] = pd.qcut(df['Fare'],5,labels=False)
    df.drop(['Fare'], axis=1, inplace=True)
    return df 

def prepare_genders(df):
    genders = {"male": 0, "female": 1, "unknown": 2}
    df['Sex'] = df['Sex'].map(genders)
    df['Sex'].fillna(2, inplace=True)
    df['Sex'] = df['Sex'].astype(int)
    return df

def prepare_embarked(df):
    df['Embarked'].replace('', 'U', inplace=True)
    df['Embarked'].fillna('U', inplace=True)
    ports = {"S": 0, "C": 1, "Q": 2, "U": 3}
    df['Embarked'] = df['Embarked'].map(ports)
    return df
    
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()
    
titanic_ds = Run.get_context().input_datasets['titanic_ds']
df = titanic_ds.to_pandas_dataframe().drop(['PassengerId', 'Name', 'Ticket', 'Cabin'], axis=1)
df = prepare_embarked(prepare_genders(prepare_fare(prepare_age(df))))

df.to_csv(os.path.join(args.output_path,"prepped_data.csv"))

print(f"Wrote prepped data to {args.output_path}/prepped_data.csv")
```

上記のコード スニペットは、タイタニック号データのデータ準備の完全な例ですが、最小限のものです。 スニペットは、コードをファイルに出力する Jupyter の "マジック コマンド" で開始されます。 Jupyter Notebook を使用しない場合は、その行を削除し、手動でファイルを作成します。

上記のスニペットのさまざまな `prepare_` 関数では、入力データセット内の関連する列を変更します。 これらの関数は、Pandas の `DataFrame` オブジェクトに変更されたデータで動作します。 いずれの場合も、欠落しているデータは、代表的なランダム データ、または "不明" を示すカテゴリ データを使用して入力されます。 テキストベースのカテゴリ データは整数にマップされます。 不要な列は上書きまたは削除されます。 

コードでは、データ準備関数を定義した後、入力引数を解析します。これは、データの書き込み先のパスです  (これらの値は、次のステップで説明する `OutputFileDatasetConfig` オブジェクトによって決まります)。このコードでは、登録済みの `'titanic_cs'` `Dataset` を取得し、それを Pandas `DataFrame` に変換して、さまざまなデータ準備関数を呼び出します。 

`output_path` はディレクトリであるため、`to_csv()` の呼び出しでファイル名 `prepped_data.csv` を指定します。

### <a name="write-the-data-preparation-pipeline-step-pythonscriptstep"></a>データ準備パイプライン ステップ (`PythonScriptStep`) を書き込む

パイプラインで使用するには、上記で説明したデータ準備コードを `PythonScripStep` オブジェクトに関連付ける必要があります。 CSV 出力が書き込まれる先のパスは、`OutputFileDatasetConfig` オブジェクトによって生成されます。 `ComputeTarget`、`RunConfig`、`'titanic_ds' Dataset` など、事前に準備されたリソースを使用して指定を完了します。

```python
from azureml.data import OutputFileDatasetConfig
from azureml.pipeline.steps import PythonScriptStep

prepped_data_path = OutputFileDatasetConfig(name="output_path")

dataprep_step = PythonScriptStep(
    name="dataprep", 
    script_name="dataprep.py", 
    compute_target=compute_target, 
    runconfig=aml_run_config,
    arguments=["--output_path", prepped_data_path],
    inputs=[titanic_ds.as_named_input('titanic_ds')],
    allow_reuse=True
)
```

`prepped_data_path` オブジェクトは、ディレクトリを指す `OutputFileDatasetConfig` 型です。  `arguments` パラメーターで指定されていることに注意してください。 前のステップを確認すると、データ準備コード内で、引数 `'--output_path'` の値が、CSV ファイルが書き込まれたディレクトリ パスであることがわかります。 

## <a name="train-with-automlstep"></a>AutoMLStep でトレーニングする

自動 ML パイプライン ステップの構成は、`AutoMLConfig` クラスを使用して行われます。 この柔軟なクラスについては、「[Python で自動 ML の実験を構成する](./how-to-configure-auto-train.md)」を参照してください。 データの入力と出力は、ML パイプラインで特に注意が必要な構成の唯一の側面です。 パイプラインでの `AutoMLConfig` の入力と出力については、以下で詳しく説明します。 データだけでなく、ML パイプラインの利点は、さまざまなステップで異なるコンピューティング ターゲットを使用できることです。 自動 ML プロセスに対してのみ、より強力な `ComputeTarget` を使用するように選択することもできます。 これは、`AutoMLConfig` オブジェクトの `run_configuration` パラメーターにより強力な `RunConfiguration` を割り当てるのと同じように簡単です。

### <a name="send-data-to-automlstep"></a>データを `AutoMLStep` に送信する

ML パイプラインでは、入力データは `Dataset` オブジェクトである必要があります。 パフォーマンスが最も高い方法は、`OutputTabularDatasetConfig` オブジェクトの形式で入力データを提供することです。 `prepped_data_path`、`prepped_data_path` オブジェクトなど、その型のオブジェクトを作成するには、`OutputFileDatasetConfig` で `read_delimited_files()` を使用します。

```python
# type(prepped_data) == OutputTabularDatasetConfig
prepped_data = prepped_data_path.read_delimited_files()
```

ワークスペースに登録されている `Dataset` オブジェクトを使用することもできます。

```python
prepped_data = Dataset.get_by_name(ws, 'Data_prepared')
```

2 つの手法の比較:

| 手法 | 利点と欠点 | 
|-|-|
|`OutputTabularDatasetConfig`| パフォーマンスが高い | 
|| `OutputFileDatasetConfig` からの自然なルート | 
|| パイプラインの実行後にデータが永続化されない |
|| [`OutputTabularDatasetConfig` 手法を示す Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb) |
| 登録済み `Dataset` | パフォーマンスが低い |
| | さまざまな方法で生成できる | 
| | データが永続化され、ワークスペース全体で表示される |
| | [登録済み `Dataset` 手法を示す Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/continuous-retraining/auto-ml-continuous-retraining.ipynb)


### <a name="specify-automated-ml-outputs"></a>自動 ML 出力を指定する

`AutoMLStep` の出力は、高パフォーマンス モデルとそのモデル自体の最終的なメトリック スコアです。 これらの出力を以降のパイプライン ステップで使用するには、それらを受け取るように `OutputFileDatasetConfig` オブジェクトを準備します。

```python
from azureml.pipeline.core import TrainingOutput

metrics_data = PipelineData(name='metrics_data',
                            datastore=datastore,
                            pipeline_output_name='metrics_output',
                            training_output=TrainingOutput(type='Metrics'))

model_data = PipelineData(name='best_model_data',
                          datastore=datastore,
                          pipeline_output_name='model_output',
                          training_output=TrainingOutput(type='Model'))
```

上記のスニペットでは、メトリックとモデル出力用に 2 つの `PipelineData` オブジェクトを作成します。 それぞれに名前が付けられ、前に取得した既定のデータストアに割り当てられ、`AutoMLStep` からの特定の `type` の `TrainingOutput` に関連付けられます。 これらの `PipelineData` オブジェクトに `pipeline_output_name` を割り当てるため、それらの値は、個々のパイプライン ステップからだけでなく、パイプライン全体から使用できるようになります。これについては、下記の「パイプラインの結果を確認する」セクションで説明します。 

### <a name="configure-and-create-the-automated-ml-pipeline-step"></a>自動 ML パイプライン ステップを構成および作成する

入力と出力が定義されたら、次は `AutoMLConfig` と `AutoMLStep` を作成します。 構成の詳細は、「[Python で自動 ML の実験を構成する](./how-to-configure-auto-train.md)」で説明されているように、タスクによって異なります。 次のスニペットでは、タイタニック号の生存者分類タスクについて、シンプルな構成を示しています。

```python
from azureml.train.automl import AutoMLConfig
from azureml.pipeline.steps import AutoMLStep

# Change iterations to a reasonable number (50) to get better accuracy
automl_settings = {
    "iteration_timeout_minutes" : 10,
    "iterations" : 2,
    "experiment_timeout_hours" : 0.25,
    "primary_metric" : 'AUC_weighted'
}

automl_config = AutoMLConfig(task = 'classification',
                             path = '.',
                             debug_log = 'automated_ml_errors.log',
                             compute_target = compute_target,
                             run_configuration = aml_run_config,
                             featurization = 'auto',
                             training_data = prepped_data,
                             label_column_name = 'Survived',
                             **automl_settings)

train_step = AutoMLStep(name='AutoML_Classification',
    automl_config=automl_config,
    passthru_automl_config=False,
    outputs=[metrics_data,model_data],
    enable_default_model_output=False,
    enable_default_metrics_output=False,
    allow_reuse=True)
```
このスニペットは、`AutoMLConfig` で一般的に使用される表現形式を示しています。 より流動的な (ハイパーパラメーターのような) 引数は個別の辞書で指定されますが、変更される可能性の低い値は `AutoMLConfig` コンストラクターで直接指定されます。 この場合、`automl_settings` では短い実行を指定します。実行は、2 回のみの反復後または 15 分後のどちらか早い方の時点で停止します。

`automl_settings` 辞書は、`AutoMLConfig` コンストラクターに kwargs として渡されます。 その他のパラメーターは複雑ではありません。

- この例では、`task`が `classification` に設定されています。 その他の有効な値は `regression` と `forecasting` です
- `path` と `debug_log` では、プロジェクトへのパスと、デバッグ情報が書き込まれるローカル ファイルを記述します 
- `compute_target` は以前に定義された `compute_target` です。この例では、低コストの CPU ベースのコンピューターです。 AutoML のディープ ラーニング機能を使用する場合は、コンピューティング ターゲットを GPU ベースに変更する必要があります
- `featurization` は `auto` に設定されます。 詳細については、自動 ML 構成ドキュメントの「[データの特徴付け](./how-to-configure-auto-train.md#data-featurization)」を参照してください 
- `label_column_name` は、予測対象の列を示します 
- `training_data` は、データ準備ステップの出力から作成された `OutputTabularDatasetConfig` オブジェクトに設定されます 

`AutoMLStep` 自体では `AutoMLConfig` を受け取り、メトリックおよびモデル データを保持するために出力として `PipelineData` オブジェクトが作成されます。 

>[!Important]
> `AutoMLStepRun` を使用している場合にのみ、`enable_default_model_output` と `enable_default_metrics_output` を `True` に設定する必要があります。

この例では、自動 ML プロセスで、`training_data` に対してクロス検証が実行されます。 `n_cross_validations` 引数を使用して、クロス検証の数を制御できます。 データ準備ステップの一部としてトレーニング データを既に分割している場合は、`validation_data` をその独自の `Dataset` に設定できます。

場合によっては、データ機能に `X` が、データ ラベルには `y` が使用されることがあります。 この手法は非推奨であり、入力には `training_data` を使用する必要があります。 

## <a name="register-the-model-generated-by-automated-ml"></a>自動 ML によって生成されたモデルを登録する 

単純な ML パイプラインの最後のステップでは、作成されたモデルを登録します。 ワークスペースのモデル レジストリにモデルを追加すると、ポータルで使用できるようになり、バージョン管理が可能になります。 モデルを登録するには、`AutoMLStep` の `model_data` 出力を受け取る別の `PythonScriptStep` を書き込みます。

### <a name="write-the-code-to-register-the-model"></a>モデルを登録するコードを書き込む

モデルは `Workspace` に登録されます。 おそらく、`Workspace.from_config()` を使用してローカル コンピューター上のワークスペースにログオンすることには慣れていると思いますが、実行中の ML パイプライン内からワークスペースを取得する方法は他にもあります。 `Run.get_context()` では、アクティブな `Run` を取得します。 この `run` オブジェクトでは、ここで使用される `Workspace` を含む、多くの重要なオブジェクトへのアクセスが提供されます。

```python
%%writefile register_model.py
from azureml.core.model import Model, Dataset
from azureml.core.run import Run, _OfflineRun
from azureml.core import Workspace
import argparse

parser = argparse.ArgumentParser()
parser.add_argument("--model_name", required=True)
parser.add_argument("--model_path", required=True)
args = parser.parse_args()

print(f"model_name : {args.model_name}")
print(f"model_path: {args.model_path}")

run = Run.get_context()
ws = Workspace.from_config() if type(run) == _OfflineRun else run.experiment.workspace

model = Model.register(workspace=ws,
                       model_path=args.model_path,
                       model_name=args.model_name)

print("Registered version {0} of model {1}".format(model.version, model.name))
```

### <a name="write-the-pythonscriptstep-code"></a>PythonScriptStep コードを書き込む

モデル登録 `PythonScriptStep` では、その引数の 1 つに `PipelineParameter` が使用されます。 パイプライン パラメーターはパイプラインの引数であり、実行の送信時に簡単に設定できます。 これらは、宣言すると通常の引数として渡されます。 

```python

from azureml.pipeline.core.graph import PipelineParameter

# The model name with which to register the trained model in the workspace.
model_name = PipelineParameter("model_name", default_value="TitanicSurvivalInitial")

register_step = PythonScriptStep(script_name="register_model.py",
                                       name="register_model",
                                       allow_reuse=False,
                                       arguments=["--model_name", model_name, "--model_path", model_data],
                                       inputs=[model_data],
                                       compute_target=compute_target,
                                       runconfig=aml_run_config)
```

## <a name="create-and-run-your-automated-ml-pipeline"></a>自動 ML パイプラインを作成して実行する

`AutoMLStep` を含むパイプラインを作成して実行することは、通常のパイプラインと何ら違いはありません。 

```python
from azureml.pipeline.core import Pipeline
from azureml.core import Experiment

pipeline = Pipeline(ws, [dataprep_step, train_step, register_step])

experiment = Experiment(workspace=ws, name='titanic_automl')

run = experiment.submit(pipeline, show_output=True)
run.wait_for_completion()
```

上記のコードでは、データ準備、自動 ML、およびモデル登録ステップを `Pipeline` オブジェクトに結合します。 その後、`Experiment` オブジェクトを作成します。 `Experiment` コンストラクターでは、名前が指定された実験が存在する場合はそれを取得するか、必要に応じて作成します。 `Pipeline` を `Experiment` に送信し、パイプラインを非同期に実行する `Run` オブジェクトを作成します。 `wait_for_completion()` 関数では、実行が完了するまでブロックします。

### <a name="examine-pipeline-results"></a>パイプラインの結果を確認する 

`run` が完了したら、`pipeline_output_name` が割り当てられている `PipelineData` オブジェクトを取得できます。 結果をダウンロードして読み込み、さらに処理することができます。  

```python
metrics_output_port = run.get_pipeline_output('metrics_output')
model_output_port = run.get_pipeline_output('model_output')

metrics_output_port.download('.', show_progress=True)
model_output_port.download('.', show_progress=True)
```

ダウンロードされたファイルは、`azureml/{run.id}/` サブディレクトリに書き込まれます。 メトリック ファイルは JSON 形式であり、確認のために Pandas データフレームに変換できます。

ローカル処理では、Pandas、Pickle、AzureML SDK などの関連パッケージのインストールが必要になる場合があります。 この例では、自動 ML によって検出される最適なモデルが XGBoost に依存する可能性があります。

```bash
!pip install xgboost==0.90
```

```python
import pandas as pd
import json

metrics_filename = metrics_output._path_on_datastore
# metrics_filename = path to downloaded file
with open(metrics_filename) as f:
   metrics_output_result = f.read()
   
deserialized_metrics_output = json.loads(metrics_output_result)
df = pd.DataFrame(deserialized_metrics_output)
df
```

上記のコード スニペットは、Azure データストア上の場所から読み込まれるメトリック ファイルを示しています。 また、コメントに示されているように、ダウンロードされたファイルから読み込むこともできます。 逆シリアル化して、Pandas DataFrame に変換したら、自動 ML ステップを反復するたびに詳細なメトリックを確認できます。

モデル ファイルは、`Model` オブジェクトに逆シリアル化でき、これを推論や詳細なメトリック分析などのために使用することができます。 

```python
import pickle

model_filename = model_output._path_on_datastore
# model_filename = path to downloaded file

with open(model_filename, "rb" ) as f:
    best_model = pickle.load(f)

# ... inferencing code not shown ...
```

既存のモデルの読み込みと操作の詳細については、「[Azure Machine Learning で既存のモデルを使用する](how-to-deploy-existing-model.md)」を参照してください。

### <a name="download-the-results-of-an-automated-ml-run"></a>自動 ML 実行の結果をダウンロードする

この記事に従っている場合は、インスタンス化された `run` オブジェクトが作成されます。 しかし、`Experiment` オブジェクトを使用して、完了した `Run` オブジェクトを `Workspace` から取得することもできます。

ワークスペースには、すべての実験と実行の完全なレコードが含まれています。 ポータルを使用して、実験の出力を見つけてダウンロードすることも、コードを使用することもできます。 過去の実行のレコードにアクセスするには、Azure Machine Learning を使用して、関心のある実行の ID を見つけます。 その ID では、`Workspace` と `Experiment` を使用して、特定の `run` を選択することができます。

```python
# Retrieved from Azure Machine Learning web UI
run_id = 'aaaaaaaa-bbbb-cccc-dddd-0123456789AB'
experiment = ws.experiments['titanic_automl']
run = next(run for run in ex.get_runs() if run.id == run_id)
```

上記のコードの文字列を、過去の実行の指定に変更する必要があります。 上記のスニペットでは、通常の `from_config()` を使用して、関連する `Workspace` に `ws` が割り当てられていることを前提としています。 目的の実験が直接取得されてから、コードでは、`run.id` 値を照合することによって目的の `Run` を見つけます。

`Run` オブジェクトが作成されたら、メトリックとモデルをダウンロードできます。 

```python
automl_run = next(r for r in run.get_children() if r.name == 'AutoML_Classification')
outputs = automl_run.get_outputs()
metrics = outputs['default_metrics_AutoML_Classification']
model = outputs['default_model_AutoML_Classification']

metrics.get_port_data_reference().download('.')
model.get_port_data_reference().download('.')
```

各 `Run` オブジェクトには、個々のパイプライン ステップの実行に関する情報を含む `StepRun` オブジェクトがあります。 `run` では、`AutoMLStep` の `StepRun` オブジェクトが検索されます。 メトリックとモデルは、その既定の名前を使用して取得されます。これは、`AutoMLStep` の `outputs` パラメーターに `PipelineData` オブジェクトを渡さない場合でも使用できます。 

最後に、実際のメトリックとモデルがローカル コンピューターにダウンロードされます。これについては、上記の「パイプラインの結果を確認する」セクションで説明しました。

## <a name="next-steps"></a>次の手順

- 回帰を使用してタクシー料金を予測する[パイプラインでの自動 ML の完全な例](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb)を示す、Jupyter Notebook を実行する
- [コードを書き込まずに自動 ML の実験を作成する](how-to-use-automated-ml-for-ml-models.md)
- さまざまな[自動 ML を示す Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) を探索する
- [エンドツーエンドの MLOps](./concept-model-management-and-deployment.md#automate-the-ml-lifecycle) へのパイプラインの統合について参照する、または [MLOps GitHub リポジトリ](https://github.com/Microsoft/MLOpspython)を調べる
