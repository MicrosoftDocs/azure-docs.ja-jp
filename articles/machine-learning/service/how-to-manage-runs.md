---
title: Python でのトレーニングの実行の開始、監視、およびキャンセル
titleSuffix: Azure Machine Learning
description: 自分の機械学習の実験を、開始、状態設定、タグ付けおよび整理する方法を説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2019
ms.openlocfilehash: 6615b5c277577ee2238434591c61362885f2fec6
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002742"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Python でのトレーニングの実行の開始、監視、およびキャンセル

[Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) と [Machine Learning CLI](reference-azure-machine-learning-cli.md) には、自分のトレーニングおよび実験の実行を、監視、整理、管理するさまざまな方法があります。

この記事では、次のタスクの例を示します。

* 実行のパフォーマンスの監視。
* 実行のキャンセルまたは失敗。
* 子実行の作成。
* 実行のタグ付けおよび検索。

## <a name="prerequisites"></a>前提条件

次のものが必要です。

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。

* [Azure Machine Learning ワークスペース](how-to-manage-workspace.md)。

* Azure Machine Learning SDK for Python (バージョン 1.0.21 以降)。 SDK の最新バージョンのインストールまたは最新バージョンへの更新を行うには、[SDK のインストールまたは更新](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)に関する記事を参照してください。

    お使いの Azure Machine Learning SDK のバージョンを確認するには、次のコードを使用します。

    ```python
    print(azureml.core.VERSION)
    ```

* [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) と [Azure Machine Learning 用 CLI 拡張機能](reference-azure-machine-learning-cli.md)。

## <a name="start-a-run-and-its-logging-process"></a>実行とそのロギング プロセスを開始する

### <a name="using-the-sdk"></a>SDK を使用する

お使いの実験を設定するには、[azureml.core](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py) パッケージから [Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)、[Experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)、[Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) および [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) クラスをインストールします。

```python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

[`start_logging()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) メソッドを使用して、そのログ プロセスを開始および実行します。

```python
notebook_run = exp.start_logging()
notebook_run.log(name="message", value="Hello from run!")
```

### <a name="using-the-cli"></a>CLI の使用

実験の実行を開始するには、次の手順を使用します。

1. シェルまたはコマンド プロンプトから Azure CLI を使用して、Azure サブスクリプションに対して認証します。

    ```azurecli-interactive
    az login
    ```

1. ワークスペースの構成を、トレーニング スクリプトを含むフォルダーにアタッチします。 `myworkspace` は、ご利用の Azure Machine Learning ワークスペースに置き換えます。 `myresourcegroup` は、ワークスペースが含まれている Azure リソース グループに置き換えます。

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    このコマンドでは、サンプルの runconfig および conda 環境ファイルを含む `.azureml` サブディレクトリを作成します。 これには、Azure Machine Learning ワークスペースとの通信に使用される `config.json` ファイルも含まれています。

    詳しくは、「[az ml folder attach](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach)」をご覧ください。

2. 実行を開始するには、次のコマンドを使用します。 このコマンドを使用する場合は、-c パラメーターに対して runconfig ファイルの名前 (ファイル システムが表示されている場合、\*.runconfig の前のテキスト) を指定します。

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > `az ml folder attach` コマンドで、2 つのサンプル runconfig ファイルを含む `.azureml` サブディレクトリが作成されました。
    >
    > プログラムで実行構成オブジェクトを作成する Python スクリプトがある場合は、[RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) を使用してそれを runconfig ファイルとして保存できます。
    >
    > runconfig ファイルのその他の例については、[https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml) を参照してください。

    詳しくは、「[az ml run submit-script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script)」をご覧ください。

## <a name="monitor-the-status-of-a-run"></a>実行の状態を監視する

### <a name="using-the-sdk"></a>SDK を使用する

[`get_status()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--) メソッドを使用して実行の状態を取得します。

```python
print(notebook_run.get_status())
```

実行 ID、実行時間、および実行に関する追加の詳細を取得するには、[`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--) メソッドを使用します。

```python
print(notebook_run.get_details())
```

自分の実行が正常に終了したら、[`complete()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) メソッドを使用し、それを完了とマークします。

```python
notebook_run.complete()
print(notebook_run.get_status())
```

Python の `with...as` 設計パターンを使用している場合、実行が範囲外になると、実行によって自動的に実行が完了とマークされます。 ユーザーが手動で実行を完了とマークする必要はありません。

```python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print(notebook_run.get_status())

print(notebook_run.get_status())
```

### <a name="using-the-cli"></a>CLI の使用

1. 実験の実行の一覧を表示するには、次のコマンドを使用します。 `experiment` は、実験の名前に置き換えます。

    ```azurecli-interactive
    az ml run list --experiment-name experiment
    ```

    このコマンドで、この実験の実行に関する情報を一覧表示する JSON ドキュメントが返されます。

    詳しくは、「[az ml experiment list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list)」をご覧ください。

2. 特定の実行に関する情報を表示するには、次のコマンドを使用します。 `runid` は、実行の ID に置き換えます。

    ```azurecli-interactive
    az ml run show -r runid
    ```

    このコマンドで、その実行に関する情報を一覧表示する JSON ドキュメントが返されます。

    詳しくは、「[az ml run show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show)」をご覧ください。

## <a name="cancel-or-fail-runs"></a>実行のキャンセルまたは失敗

間違いに気付いた場合、または実行の完了に時間がかかりすぎる場合は、実行をキャンセルできます。

### <a name="using-the-sdk"></a>SDK を使用する

SDK を使用して実行をキャンセルするには、[`cancel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) メソッドを使用します。

```python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_script_run = exp.submit(run_config)
print(local_script_run.get_status())

local_script_run.cancel()
print(local_script_run.get_status())
```

実行が終了したが、実行にエラー (正しくないトレーニング スクリプトが使用されたなど) が含まれている場合、[`fail()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)#fail-error-details-none--error-code-none---set-status-true-) メソッドを使用して、それを失敗とマークできます。

```python
local_script_run = exp.submit(run_config)
local_script_run.fail()
print(local_script_run.get_status())
```

### <a name="using-the-cli"></a>CLI の使用

CLI を使用して実行をキャンセルするには、次のコマンドを使用します。 `runid` は、実行の ID に置き換えます

```azurecli-interactive
az ml run cancel -r runid
```

詳しくは、「[az ml run cancel](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel)」をご覧ください。

## <a name="create-child-runs"></a>子実行の作成

異なるハイパーパラメーターの調整を繰り返すなど、関連する実行をグループ化する場合に、子実行を作成できます。

> [!NOTE]
> 子実行は、SDK を使用してのみ作成できます。

このコード例では、`hello_with_children.py` スクリプトで [`child_run()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) メソッドを使用して、渡された実行から 5 つの子実行のバッチを作成します。

```python
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

多数の子実行を効率よく作成するには、[`create_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#create-children-count-none--tag-key-none--tag-values-none-) メソッドを使用します。 実行を作成するたびにネットワーク呼び出しが行われるため、実行のバッチを作成した方が、1 つずつ作成するよりも効率的です。

### <a name="submit-child-runs"></a>子実行を送信する

親実行から子実行を送信することもできます。 そうすることで、親実行と子実行の階層を作成することができます。子実行はそれぞれ異なるコンピューティング先で実行され、共通の親実行 ID で関連付けられます。

親実行内から子実行を送信するには、["submit_child()"](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#submit-child-config--tags-none----kwargs-) メソッドを使用します。 これを親実行スクリプト内で行うには、実行コンテキストを取得し、そのコンテキスト インスタンスの "submit_child" メソッドを使用して子実行を送信します。

```python
## In parent run script
parent_run = Run.get_context()
child_run_config = ScriptRunConfig(source_directory='.', script='child_script.py')
parent_run.submit_child(child_run_config)
```

次のようにすれば、子実行内から親実行 ID を確認できます。

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>子実行を照会する

特定の親の子実行をクエリするには、[`get_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) メソッドを使用します。 "recursive = True" 引数を指定すると、入れ子になった子と孫のツリーを照会できます。

```python
print(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>実行のタグ付けおよび検索

Azure Machine Learning では、実行の整理にプロパティとタグを使用したり、自分の実行に対し重要な情報をクエリしたりできます。

### <a name="add-properties-and-tags"></a>プロパティとタグの追加

#### <a name="using-the-sdk"></a>SDK を使用する

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

単純な文字列のタグを追加することもできます。 これらのタグがタグ辞書にキーとして表示されると、値は `None` になります。

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

#### <a name="using-the-cli"></a>CLI の使用

> [!NOTE]
> CLI の使用では、タグの追加または更新のみが可能です。

タグの追加または更新を行うには、次のコマンドを使用します。

```azurecli-interactive
az ml run update -r runid --add-tag quality='fantastic run'
```

詳しくは、「[az ml run update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update)」をご覧ください。

### <a name="query-properties-and-tags"></a>クエリ プロパティおよびタグ

特定のプロパティとタグに一致する実行の一覧が返されるように、実験内の実行をクエリできます。

#### <a name="using-the-sdk"></a>SDK を使用する

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

#### <a name="using-the-cli"></a>CLI の使用

Azure CLI では、プロパティとタグに基づいて実行をフィルター処理するために使用できる [JMESPath](http://jmespath.org) クエリがサポートされています。 Azure CLI で JMESPath クエリを使用するには、`--query` パラメーターでそれを指定します。 次の例は、プロパティとタグを使用した基本的なクエリを示しています。

```azurecli-interactive
# list runs where the author property = 'azureml-user'
az ml run list --experiment-name experiment [?properties.author=='azureml-user']
# list runs where the tag contains a key that starts with 'worth another look'
az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
# list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
```

Azure CLI 結果のクエリ実行の詳細については、「[Azure CLI コマンドの出力のクエリ](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest)」を参照してください。

## <a name="example-notebooks"></a>サンプルの Notebook

次のノートブックは、この記事の概念を示しています。

* ロギング API の詳細については、[ロギング API ノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api/logging-api.ipynb)に関するページを参照してください。

* Azure Machine Learning SDK での実行の管理の詳細については、[実行の管理ノートブック](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/manage-runs)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

* 自分の実験のメトリックのログを記録する方法の詳細については、[トレーニングの実行中にメトリックを記録する](how-to-track-experiments.md)に関するページを参照してください。
