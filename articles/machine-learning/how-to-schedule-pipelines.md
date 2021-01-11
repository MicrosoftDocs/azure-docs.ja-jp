---
title: Azure Machine Learning パイプラインのスケジュールを設定する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning SDK for Python を使用して機械学習パイプラインのスケジュールを設定します。 スケジュールされたパイプラインを使用すると、データ処理、トレーニング、監視などの時間のかかる日常的なタスクを自動化できます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 11/12/2019
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 351d52128660c25c557c536f2835e0e71c0115f7
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87842962"
---
# <a name="schedule-machine-learning-pipelines-with-azure-machine-learning-sdk-for-python"></a>Azure Machine Learning SDK for Python を使用して機械学習パイプラインのスケジュールを設定する

この記事では、Azure で実行するパイプラインをプログラムでスケジュールする方法について説明します。 経過時間またはファイルシステムの変更に基づいてスケジュールを作成することを選択できます。 時間ベースのスケジュールを使用すると、データの誤差の監視などの日常的なタスクを行うことができます。 変更ベースのスケジュールを使用すると、新しいデータがアップロードされたり古いデータが編集されたりといった、不規則な変更や予期しない変更に対処できます。 スケジュールを作成する方法を説明した後、それらを取得して非アクティブ化する方法を説明します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://aka.ms/AMLFree)を作成してください。

* Azure Machine Learning SDK for Python がインストールされた Python 開発環境。 詳細については、「[Azure Machine Learning を使用してトレーニングとデプロイのための再利用可能な環境を作成および管理します](how-to-use-environments.md)」を参照してください。

* 公開されたパイプラインがある Machine Learning のワークスペース。 「[Azure Machine Learning SDK で機械学習パイプラインを作成して管理する](how-to-create-your-first-pipeline.md)」で組み込まれたものを使用できます。

## <a name="initialize-the-workspace--get-data"></a>ワークスペースを初期化してデータを取得する

パイプラインをスケジュールするには、ワークスペースへの参照、公開されたパイプラインの識別子、およびスケジュールを作成する実験の名前が必要です。 これらの値は次のコードを使用して取得できます。

```Python
import azureml.core
from azureml.core import Workspace
from azureml.pipeline.core import Pipeline, PublishedPipeline
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

experiments = Experiment.list(ws)
for experiment in experiments:
    print(experiment.name)

published_pipelines = PublishedPipeline.list(ws)
for published_pipeline in  published_pipelines:
    print(f"{published_pipeline.name},'{published_pipeline.id}'")

experiment_name = "MyExperiment" 
pipeline_id = "aaaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" 
```

## <a name="create-a-schedule"></a>スケジュールを作成する

パイプラインを定期的に実行するには、スケジュールを作成します。 `Schedule` によって、パイプライン、実験、およびトリガーが関連付けられます。 トリガーには、実行の待機時間を示す `ScheduleRecurrence`、または変更を監視するディレクトリを指定するデータストア パスを指定できます。 どちらの場合も、パイプライン識別子と、スケジュールを作成する実験の名前が必要になります。

Python ファイルの先頭に、`Schedule` クラスと `ScheduleRecurrence` クラスをインポートします。

```python

from azureml.pipeline.core.schedule import ScheduleRecurrence, Schedule
```

### <a name="create-a-time-based-schedule"></a>時刻ベースのスケジュールを作成する

`ScheduleRecurrence` コンストラクターには、次のいずれかの文字列である必要な `frequency` 引数があります。"Minute"、"Hour"、"Day"、"Week"、または "Month"。 また、スケジュールの開始から次の開始までの経過時間の `frequency` 単位を指定する整数の `interval` 引数も必要です。 省略可能な引数を使用すると、[ScheduleRecurrence SDK に関するドキュメント](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedulerecurrence?view=azure-ml-py)で詳しく説明されているように、開始時間をより具体的に指定できます。

15 分ごとに実行を開始する `Schedule` を作成します。

```python
recurrence = ScheduleRecurrence(frequency="Minute", interval=15)
recurring_schedule = Schedule.create(ws, name="MyRecurringSchedule", 
                            description="Based on time",
                            pipeline_id=pipeline_id, 
                            experiment_name=experiment_name, 
                            recurrence=recurrence)
```

### <a name="create-a-change-based-schedule"></a>変更ベースのスケジュールを作成する

ファイルの変更によってトリガーされるパイプラインは、時間ベースのスケジュールよりも効率的な場合があります。 たとえば、ファイルが変更されたとき、または新しいファイルがデータ ディレクトリに追加されたときに、前処理手順を実行することもできます。 データストアまたはデータストア内の特定のディレクトリ内の変更に対する変更を監視できます。 特定のディレクトリを監視する場合、そのディレクトリのサブディレクトリ内の変更は、実行をトリガー _しません_。

ファイルに対応する `Schedule` を作成するには、[Schedule.create](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedule?view=azure-ml-py#create-workspace--name--pipeline-id--experiment-name--recurrence-none--description-none--pipeline-parameters-none--wait-for-provisioning-false--wait-timeout-3600--datastore-none--polling-interval-5--data-path-parameter-name-none--continue-on-step-failure-none--path-on-datastore-none---workflow-provider-none---service-endpoint-none-) の呼び出しに `datastore` パラメーターを設定する必要があります。 フォルダーを監視するには、`path_on_datastore` 引数を設定します。

`polling_interval` 引数を使用すると、データストアが変更されたかどうかを確認する頻度を分単位で指定できます。

パイプラインが [DataPath](https://docs.microsoft.com/python/api/azureml-core/azureml.data.datapath.datapath?view=azure-ml-py) [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py) を使用して構築されている場合は、`data_path_parameter_name` 引数を設定することによって、その変数を変更されたファイルの名前に設定できます。

```python
datastore = Datastore(workspace=ws, name="workspaceblobstore")

reactive_schedule = Schedule.create(ws, name="MyReactiveSchedule", description="Based on input file change.",
                            pipeline_id=pipeline_id, experiment_name=experiment_name, datastore=datastore, data_path_parameter_name="input_data")
```

### <a name="optional-arguments-when-creating-a-schedule"></a>スケジュールを作成するときの省略可能な引数

前に説明した引数に加えて、`status` 引数を `"Disabled"` に設定して、非アクティブなスケジュールを作成することもできます。 最後に、`continue_on_step_failure` を使用すると、パイプラインの既定のエラー動作をオーバーライドするブール値を渡すことができます。

### <a name="use-azure-logic-apps-for-more-complex-workflows"></a>より複雑なワークフローに Azure Logic Apps を使用する

Azure Logic Apps は、Azure Machine Learning パイプラインよりも複雑なワークフローをサポートしており、はるかに広範に統合されています。 詳細については、「[ロジック アプリから Machine Learning パイプラインの実行をトリガーする](how-to-trigger-published-pipeline.md)」を参照してください。

## <a name="view-your-scheduled-pipelines"></a>スケジュールされたパイプラインを表示する

Web ブラウザーで Azure Machine Learning に移動します。 ナビゲーション パネルの **[エンドポイント]** セクションで、 **[Pipeline endpoints]\(パイプラインのエンドポイント\)** を選択します。 これにより、ワークスペースで発行されたパイプラインの一覧が表示されます。

![AML の [パイプライン] ページ](./media/how-to-schedule-pipelines/scheduled-pipelines.png)

このページでは、ワークスペース内のすべてのパイプラインに関する概要情報 (名前、説明、状態など) を確認できます。 パイプラインをクリックすると、詳細を詳しく見ることができます。 結果のページには、パイプラインの詳細が表示され、個々の実行について確認することもできます。

## <a name="deactivate-the-pipeline"></a>パイプラインを非アクティブ化する

発行されているがスケジュールされていない `Pipeline` がある場合は、次の方法で無効にできます。

```python
pipeline = PublishedPipeline.get(ws, id=pipeline_id)
pipeline.disable()
```

パイプラインがスケジュールされている場合は、まずスケジュールを取り消す必要があります。 ポータルから、または次のように実行して、スケジュールの識別子を取得します。

```python
ss = Schedule.list(ws)
for s in ss:
    print(s)
```

無効にする `schedule_id` がある場合は、次のように実行します。

```python
def stop_by_schedule_id(ws, schedule_id):
    s = next(s for s in Schedule.list(ws) if s.id == schedule_id)
    s.disable()
    return s

stop_by_schedule_id(ws, schedule_id)
```

その後 `Schedule.list(ws)` を再度実行すると、空の一覧が表示されます。

## <a name="next-steps"></a>次のステップ

この記事では、Python 用の Azure Machine Learning SDK を使用して、2 つの異なる方法でパイプラインをスケジュールしています。 一方のスケジュールは、経過時間に基づいて繰り返されます。 もう一方のスケジュールは、指定された `Datastore` で、またはそのストアのディレクトリ内でファイルが変更された場合に実行されます。 ポータルを使用して、パイプラインと個々の実行を確認する方法について説明しました。 最後に、パイプラインが実行を停止するようにスケジュールを無効にする方法を学習しました。

詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [バッチ スコアリングに Azure Machine Learning パイプラインを使用する](tutorial-pipeline-batch-scoring-classification.md)

* [パイプライン](concept-ml-pipelines.md)に関する詳細情報
* [Jupyter を使用した Azure Machine Learning の探索](samples-notebooks.md)に関する詳細情報

