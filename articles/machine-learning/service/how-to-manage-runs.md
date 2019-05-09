---
title: Python でのトレーニングの実行の開始、監視、およびキャンセル
titleSuffix: Azure Machine Learning service
description: 自分の機械学習の実験を、開始、状態設定、タグ付けおよび整理する方法を説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 04/05/2019
ms.openlocfilehash: c0c1c1353b12944fa913dfb0789192917b99f234
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2019
ms.locfileid: "59543595"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Python でのトレーニングの実行の開始、監視、およびキャンセル

[Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) には、自分のトレーニングおよび実験の実行を、監視、整理、管理するさまざまな方法があります。

この記事では、次のタスクの例を示します。

* 実行のパフォーマンスの監視。
* 実行のキャンセルまたは失敗。
* 子実行の作成。
* 実行のタグ付けおよび検索。

## <a name="prerequisites"></a>前提条件

次のものが必要です。

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning service](https://aka.ms/AMLFree) を今日からお試しいただけます。

* [Azure Machine Learning service ワークスペース](setup-create-workspace.md)。

* Azure Machine Learning SDK for Python (バージョン 1.0.21 以降)。 SDK の最新バージョンのインストールまたは最新バージョンへの更新を行うには、[SDK のインストールまたは更新](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)に関する記事を参照してください。

    お使いの Azure Machine Learning SDK のバージョンを確認するには、次のコードを使用します。

    ```Python
    print(azureml.core.VERSION)
    ```

## <a name="start-a-run-and-its-logging-process"></a>実行とそのロギング プロセスを開始する

お使いの実験を設定するには、[azureml.core](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py) パッケージから [Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)、[Experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)、[Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) および [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) クラスをインストールします。

```Python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

[`start_logging()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) メソッドを使用して、そのログ プロセスを開始および実行します。

```Python
notebook_run = exp.start_logging()

notebook_run.log(name="message", value="Hello from run!")
```

## <a name="monitor-the-status-of-a-run"></a>実行の状態を監視する

[`get_status()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--) メソッドを使用して実行の状態を取得します。

```Python
print(notebook_run.get_status())
```

実行の詳細をさらに取得するには、[`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--) メソッドを使用します。

```Python
notebook_run.get_details()
```

自分の実行が正常に終了したら、[`complete()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) メソッドを使用し、それを完了とマークします。

```Python
notebook_run.complete()
print(notebook_run.get_status())
```

Python の `with...as` パターンを使用している場合、実行が範囲外になると、実行によって自動的に実行が完了とマークされます。 ユーザーが手動で実行を完了とマークする必要はありません。

```Python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print("Is it still running?",notebook_run.get_status())

print("Has it completed?",notebook_run.get_status())
```

## <a name="cancel-or-fail-runs"></a>実行のキャンセルまたは失敗

 間違いに気付いた場合、または実行の完了に時間がかかりすぎる場合は、[`cancel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) メソッドを使用して、完了前にその実行を停止して、キャンセルとマークできます。

```Python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')

local_script_run = exp.submit(run_config)
print("Did the run start?",local_script_run.get_status())

local_script_run.cancel()
print("Did the run cancel?",local_script_run.get_status())
```

実行が終了したが、実行にエラー (正しくないトレーニング スクリプトが使用されたなど) が含まれている場合、[`fail()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#fail-error-details-none---set-status-true-) メソッドを使用して、それを失敗とマークできます。

```Python
local_script_run = exp.submit(run_config)
local_script_run.fail()

print(local_script_run.get_status())
```

## <a name="create-child-runs"></a>子実行の作成

異なるハイパーパラメーターの調整を繰り返すなど、関連する実行をグループ化する場合に、子実行を作成できます。

このコード例では、`hello_with_children.py` スクリプトで [`child_run()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) メソッドを使用して、渡された実行から 5 つの子実行のバッチを作成します。

```Python
!more hello_with_children.py
run_config = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_script_run = exp.submit(run_config)
local_script_run.wait_for_completion(show_output=True)
print(local_script_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> 子実行は、範囲外になると、自動的に完了とマークされます。

子実行は 1 つずつ実行することも可能ですが、作成されるたびにネットワーク呼び出しが行われるので、実行をバッチ送信するよりも効率が下がります。

特定の親の子実行をクエリするには、[`get_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) メソッドを使用します。

```Python
list(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>実行のタグ付けおよび検索

Azure Machine Learning service では、実行の整理にプロパティとタグを使用したり、自分の実行に対し重要な情報をクエリしたりできます。

### <a name="add-properties-and-tags"></a>プロパティとタグの追加

検索可能なメタデータを自分の実行に追加するには、[`add_properties()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) メソッドを使用します。 たとえば、次のコードでは実行に `"author"` プロパティが追加されます。

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

プロパティは変更不可であるため、プロパティによって監査目的の恒久的な記録が作成されます。 次のコード例は、前のコードで `"author"` プロパティ値として `"azureml-user"` が既に追加されているためエラーとなります。

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

プロパティとは異なり、タグは変更可能です。 自分の実験のコンシューマーに検索可能で意味のある情報を追加するには、[`tag()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) メソッドを使用します。

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

単純な文字列のタグを追加することもできます。 これらのタグがタグ辞書に表示されると、値は `None` になります。

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

### <a name="query-properties-and-tags"></a>クエリ プロパティおよびタグ

特定のプロパティとタグに一致する実行の一覧が返されるように、実験内の実行をクエリできます。

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

## <a name="example-notebooks"></a>サンプルの Notebook

次のノートブックは、この記事の概念を示しています。

* ロギング API の詳細については、[ロギング API ノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api/logging-api.ipynb)に関するページを参照してください。

* Azure Machine Learning SDK での実行の管理の詳細については、[実行の管理ノートブック](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/manage-runs)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

* 自分の実験のメトリックのログを記録する方法の詳細については、[トレーニングの実行中にメトリックを記録する](how-to-track-experiments.md)に関するページを参照してください。