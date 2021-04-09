---
title: Python でのトレーニングの実行の開始、監視、およびキャンセル
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Python SDK を使用して、機械学習実験の実行を開始し、状態を監視し、管理する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 977498abb17fe592cef344f407a662d3b79749b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102634788"
---
# <a name="start-monitor-and-track-runs"></a>実行の開始、監視および追跡 

[Azure Machine Learning SDK for Python](/python/api/overview/azure/ml/intro)、[Machine Learning CLI](reference-azure-machine-learning-cli.md)、および [Azure Machine Learning Studio](https://ml.azure.com) には、自分のトレーニングおよび実験の実行を、監視、整理、管理するさまざまな方法があります。

この記事では、次のタスクの例を示します。

* 実行のパフォーマンスの監視。
* 電子メール通知による実行状態の監視。
* 実行のタグ付けおよび検索。
* 実行の説明の追加。 
* 検索の実行。 
* 実行のキャンセルまたは失敗。
* 子実行の作成。
 

> [!TIP]
> Azure Machine Learning service および関連する Azure サービスの監視の詳細については、[Azure Machine Learning を監視する方法](monitor-azure-machine-learning.md)に関する記事を参照してください。
> Web サービスまたは IoT Edge モジュールとしてデプロイされたモデルの監視の詳細については、[モデル データの収集](how-to-enable-data-collection.md)および [Application Insights での監視](how-to-enable-app-insights.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

次のものが必要です。

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。

* [Azure Machine Learning ワークスペース](how-to-manage-workspace.md)。

* Azure Machine Learning SDK for Python (バージョン 1.0.21 以降)。 SDK の最新バージョンのインストールまたは最新バージョンへの更新を行うには、[SDK のインストールまたは更新](/python/api/overview/azure/ml/install)に関する記事を参照してください。

    お使いの Azure Machine Learning SDK のバージョンを確認するには、次のコードを使用します。

    ```python
    print(azureml.core.VERSION)
    ```

* [Azure CLI](/cli/azure/?preserve-view=true&view=azure-cli-latest) と [Azure Machine Learning 用 CLI 拡張機能](reference-azure-machine-learning-cli.md)。


## <a name="monitor-run-performance"></a>実行のパフォーマンスの監視

* 実行とそのロギング プロセスを開始する

    # <a name="python"></a>[Python](#tab/python)
    
    1. お使いの実験を設定するには、[azureml.core](/python/api/azureml-core/azureml.core) パッケージから [Workspace](/python/api/azureml-core/azureml.core.workspace.workspace)、[Experiment](/python/api/azureml-core/azureml.core.experiment.experiment)、[Run](/python/api/azureml-core/azureml.core.run%28class%29) および [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) クラスをインストールします。
    
        ```python
        import azureml.core
        from azureml.core import Workspace, Experiment, Run
        from azureml.core import ScriptRunConfig
        
        ws = Workspace.from_config()
        exp = Experiment(workspace=ws, name="explore-runs")
        ```
    
    1. [`start_logging()`](/python/api/azureml-core/azureml.core.experiment%28class%29#start-logging--args----kwargs-) メソッドを使用して、そのログ プロセスを開始および実行します。
    
        ```python
        notebook_run = exp.start_logging()
        notebook_run.log(name="message", value="Hello from run!")
        ```
        
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    実験の実行を開始するには、次の手順を使用します。
    
    1. シェルまたはコマンド プロンプトから Azure CLI を使用して、Azure サブスクリプションに対して認証します。
    
        ```azurecli-interactive
        az login
        ```
        
        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 
    
    1. ワークスペースの構成を、トレーニング スクリプトを含むフォルダーにアタッチします。 `myworkspace` は、ご利用の Azure Machine Learning ワークスペースに置き換えます。 `myresourcegroup` は、ワークスペースが含まれている Azure リソース グループに置き換えます。
    
        ```azurecli-interactive
        az ml folder attach -w myworkspace -g myresourcegroup
        ```
    
        このコマンドでは、サンプルの runconfig および conda 環境ファイルを含む `.azureml` サブディレクトリを作成します。 これには、Azure Machine Learning ワークスペースとの通信に使用される `config.json` ファイルも含まれています。
    
        詳しくは、「[az ml folder attach](/cli/azure/ext/azure-cli-ml/ml/folder?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach)」をご覧ください。
    
    2. 実行を開始するには、次のコマンドを使用します。 このコマンドを使用する場合は、-c パラメーターに対して runconfig ファイルの名前 (ファイル システムが表示されている場合、\*.runconfig の前のテキスト) を指定します。
    
        ```azurecli-interactive
        az ml run submit-script -c sklearn -e testexperiment train.py
        ```
    
        > [!TIP]
        > `az ml folder attach` コマンドで、2 つのサンプル runconfig ファイルを含む `.azureml` サブディレクトリが作成されました。
        >
        > プログラムで実行構成オブジェクトを作成する Python スクリプトがある場合は、[RunConfig.save()](/python/api/azureml-core/azureml.core.runconfiguration#save-path-none--name-none--separate-environment-yaml-false-) を使用してそれを runconfig ファイルとして保存できます。
        >
        > runconfig ファイルのその他の例については、[https://github.com/MicrosoftDocs/pipelines-azureml/](https://github.com/MicrosoftDocs/pipelines-azureml/) を参照してください。
    
        詳しくは、「[az ml run submit-script](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script)」をご覧ください。

    # <a name="studio"></a>[スタジオ](#tab/azure-studio)

    Azure Machine Learning デザイナーでモデルをトレーニングする例については、「[チュートリアル: デザイナーを使用して自動車の価格を予測する](tutorial-designer-automobile-price-train-score.md)」を参照してください。

    ---

* 実行の状態を監視する

    # <a name="python"></a>[Python](#tab/python)
    
    * [`get_status()`](/python/api/azureml-core/azureml.core.run%28class%29#get-status--) メソッドを使用して実行の状態を取得します。
    
        ```python
        print(notebook_run.get_status())
        ```
    
    * 実行 ID、実行時間、および実行に関する追加の詳細を取得するには、[`get_details()`](/python/api/azureml-core/azureml.core.workspace.workspace#get-details--) メソッドを使用します。
    
        ```python
        print(notebook_run.get_details())
        ```
    
    * 自分の実行が正常に終了したら、[`complete()`](/python/api/azureml-core/azureml.core.run%28class%29#complete--set-status-true-) メソッドを使用し、それを完了とマークします。
    
        ```python
        notebook_run.complete()
        print(notebook_run.get_status())
        ```
    
    * Python の `with...as` 設計パターンを使用している場合、実行が範囲外になると、実行によって自動的に実行が完了とマークされます。 ユーザーが手動で実行を完了とマークする必要はありません。
        
        ```python
        with exp.start_logging() as notebook_run:
            notebook_run.log(name="message", value="Hello from run!")
            print(notebook_run.get_status())
        
        print(notebook_run.get_status())
        ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    * 実験の実行の一覧を表示するには、次のコマンドを使用します。 `experiment` は、実験の名前に置き換えます。
    
        ```azurecli-interactive
        az ml run list --experiment-name experiment
        ```
    
        このコマンドで、この実験の実行に関する情報を一覧表示する JSON ドキュメントが返されます。
    
        詳しくは、「[az ml experiment list](/cli/azure/ext/azure-cli-ml/ml/experiment?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list)」をご覧ください。
    
    * 特定の実行に関する情報を表示するには、次のコマンドを使用します。 `runid` は、実行の ID に置き換えます。
    
        ```azurecli-interactive
        az ml run show -r runid
        ```
    
        このコマンドで、その実行に関する情報を一覧表示する JSON ドキュメントが返されます。
    
        詳しくは、「[az ml run show](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show)」をご覧ください。
    
    
    # <a name="studio"></a>[スタジオ](#tab/azure-studio)
    
    自分の実行をスタジオで表示するには、次のようにします。 
    
    1. **[実験]** タブに移動します。
    
    1. 実験のすべての実行を表示するには、 **[All experiments]** \(すべての実験\) を選択し、ワークスペースで送信したすべての実行を表示するには、 **[すべての実行]** を選択します。
    
        **[すべての実行]** ページでは、タグ、実験、コンピューティング ターゲットなどで実行の一覧をフィルター処理し、自分の作業を整理したり、範囲を絞り込んだりすることができます。  
    
    1. このページでは、比較する実行を選択したり、グラフを追加したり、フィルターを適用して、カスタマイズすることが可能です。 これらの変更は **カスタム ビュー** として保存できるので、簡単に作業に戻ることができます。 ワークスペースのアクセス許可を持つユーザーは、カスタム ビューを編集または表示できます。 また、 **[共有ビュー]** を選択すると、カスタム ビューをチームメンバーと共有して、連携を強化できます。   
    
        :::image type="content" source="media/how-to-manage-runs/custom-views.gif" alt-text="スクリーンショット: カスタム ビューを作成する":::
    
    1. 実行ログを表示するには、特定の実行を選択し、自分の実行の診断ログとエラー ログを **[Outputs + logs]** \(出力 + ログ\) タブから確認します。
    
    ---

## <a name="monitor-the-run-status-by-email-notification"></a>電子メール通知による実行状態の監視

1. [Azure portal](https://ms.portal.azure.com/)の左側のナビゲーション バーで、 **[監視]** タブを選択します。 

1. **[診断設定]** を選択し、 **[+ 診断設定の追加]** を選択します。

    ![電子メール通知の診断設定のスクリーンショット](./media/how-to-manage-runs/diagnostic-setting.png)

1. [診断設定] の 
    1. **[カテゴリの詳細]** で、 **[AmlRunStatusChangedEvent]** を選択します。 
    1. **[宛先の詳細]** で、 **[Log Analytics ワークスペースに送信する]** を選択し、 **[サブスクリプション]** と **[Log Analytics ワークスペース]** を指定します。 

    > [!NOTE]
    > **Azure Log Analytics ワークスペース** は、**Azure Machine Learning service ワークスペース** とは異なる種類の Azure Resource です。 そのリストにオプションがない場合は、[Log Analytics ワークスペースを作成](https://docs.microsoft.com/azure/azure-monitor/logs/quick-create-workspace)することができます。 
    
    ![電子メール通知を保存する場所](./media/how-to-manage-runs/log-location.png)

1. **[ログ]** タブで、**新しい警告ルール** を追加します。 

    ![新しいアラート ルール](./media/how-to-manage-runs/new-alert-rule.png)

1. [Azure Monitor を使用してログ アラートを作成および管理する方法](https://docs.microsoft.com/azure/azure-monitor/alerts/alerts-log)に関するページを参照してください。

## <a name="run-description"></a>実行の説明 

実行に実行の説明を追加し、その実行に関するコンテキストおよび情報をより多く指定できます。 また、実行の一覧でこれらの説明を検索し、実行の一覧の列として実行の説明を追加することもできます。 

自分の実行の **[実行の詳細]** ページに移動し、編集または鉛筆アイコンを選択して、自分の実行の説明を追加、編集または削除します。 お使いの既存のカスタム ビューまたは新しいカスタム ビューに変更を保存すると、この変更を実行の一覧に保存できます。 実行の説明には、次に示すように、イメージを埋め込んだり、ディープ リンクを設定したりすることができる Markdown 形式がサポートされています。

:::image type="content" source="media/how-to-manage-runs/run-description.gif" alt-text="スクリーンショット: 実行の説明を作成する"::: 

## <a name="tag-and-find-runs"></a>実行のタグ付けおよび検索

Azure Machine Learning では、実行の整理にプロパティとタグを使用したり、自分の実行に対し重要な情報をクエリしたりできます。

* プロパティとタグの追加

    # <a name="python"></a>[Python](#tab/python)
    
    検索可能なメタデータを自分の実行に追加するには、[`add_properties()`](/python/api/azureml-core/azureml.core.run%28class%29#add-properties-properties-) メソッドを使用します。 たとえば、次のコードでは実行に `"author"` プロパティが追加されます。
    
    ```Python
    local_run.add_properties({"author":"azureml-user"})
    print(local_run.get_properties())
    ```
    
    プロパティは変更不可であるため、プロパティによって監査目的の恒久的な記録が作成されます。 次のコード例は、前のコードで `"author"` プロパティ値として `"azureml-user"` が既に追加されているためエラーとなります。
    
    ```Python
    try:
        local_run.add_properties({"author":"different-user"})
    except Exception as e:
        print(e)
    ```
    
    プロパティとは異なり、タグは変更可能です。 自分の実験のコンシューマーに検索可能で意味のある情報を追加するには、[`tag()`](/python/api/azureml-core/azureml.core.run%28class%29#tag-key--value-none-) メソッドを使用します。
    
    ```Python
    local_run.tag("quality", "great run")
    print(local_run.get_tags())
    
    local_run.tag("quality", "fantastic run")
    print(local_run.get_tags())
    ```
    
    単純な文字列のタグを追加することもできます。 これらのタグがタグ辞書にキーとして表示されると、値は `None` になります。
    
    ```Python
    local_run.tag("worth another look")
    print(local_run.get_tags())
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    > [!NOTE]
    > CLI の使用では、タグの追加または更新のみが可能です。
    
    タグの追加または更新を行うには、次のコマンドを使用します。
    
    ```azurecli-interactive
    az ml run update -r runid --add-tag quality='fantastic run'
    ```
    
    詳しくは、「[az ml run update](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update)」をご覧ください。
    
    # <a name="studio"></a>[スタジオ](#tab/azure-studio)
    
    スタジオから実行のタグを追加、編集、または削除できます。 自分の実行の **[実行の詳細]** ページに移動し、編集または鉛筆アイコンを選択して、自分の実行を追加、編集または削除します。 実行の一覧ページから、これらのタグを検索したり、フィルター処理したりすることもできます。
    
    :::image type="content" source="media/how-to-manage-runs/run-tags.gif" alt-text="スクリーンショット: 実行タグを追加、編集、または削除する":::
    
    ---

* クエリ プロパティおよびタグ

    特定のプロパティとタグに一致する実行の一覧が返されるように、実験内の実行をクエリできます。

    # <a name="python"></a>[Python](#tab/python)
    
    ```Python
    list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
    list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    Azure CLI では、プロパティとタグに基づいて実行をフィルター処理するために使用できる [JMESPath](http://jmespath.org) クエリがサポートされています。 Azure CLI で JMESPath クエリを使用するには、`--query` パラメーターでそれを指定します。 次の例は、プロパティとタグを使用したクエリのいくつかを示しています。
    
    ```azurecli-interactive
    # list runs where the author property = 'azureml-user'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user']
    # list runs where the tag contains a key that starts with 'worth another look'
    az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
    # list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
    ```
    
    Azure CLI 結果のクエリ実行の詳細については、「[Azure CLI コマンドの出力のクエリ](/cli/azure/query-azure-cli?preserve-view=true&view=azure-cli-latest)」を参照してください。
    
    # <a name="studio"></a>[スタジオ](#tab/azure-studio)
    
    特定の実行を検索するには、 **[すべての実行]** リストに移動します。 ここでは、次の 2 つの選択肢があります。
    
    1. **[フィルターの追加]** ボタンを使用して、タグへのフィルター適用を選択し、実行に割り当てられたタグで実行をフィルター処理します。 <br><br>
    OR
    
    1. 検索バーを使用して、実行状態、説明、実験名、送信者名などの実行メタデータを検索することにより、実行をすばやく見つけます。 
    
## <a name="cancel-or-fail-runs"></a>実行のキャンセルまたは失敗

間違いに気付いた場合、または実行の完了に時間がかかりすぎる場合は、実行をキャンセルできます。

# <a name="python"></a>[Python](#tab/python)

SDK を使用して実行をキャンセルするには、[`cancel()`](/python/api/azureml-core/azureml.core.run%28class%29#cancel--) メソッドを使用します。

```python
src = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_run = exp.submit(src)
print(local_run.get_status())

local_run.cancel()
print(local_run.get_status())
```

実行が終了したが、実行にエラー (正しくないトレーニング スクリプトが使用されたなど) が含まれている場合、[`fail()`](/python/api/azureml-core/azureml.core.run%28class%29#fail-error-details-none--error-code-none---set-status-true-) メソッドを使用して、それを失敗とマークできます。

```python
local_run = exp.submit(src)
local_run.fail()
print(local_run.get_status())
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

CLI を使用して実行をキャンセルするには、次のコマンドを使用します。 `runid` は、実行の ID に置き換えます

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

詳しくは、「[az ml run cancel](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel)」をご覧ください。

# <a name="studio"></a>[スタジオ](#tab/azure-studio)

Studio で実行をキャンセルするには、次の手順を使用します。

1. **[実験]** または **[パイプライン]** セクションで、実行中のパイプラインに移動します。 

1. キャンセルするパイプラインの実行番号を選択します。

1. ツール バーで、 **[キャンセル]** を選択します

---

## <a name="create-child-runs"></a>子実行の作成

異なるハイパーパラメーターの調整を繰り返すなど、関連する実行をグループ化する場合に、子実行を作成できます。

> [!NOTE]
> 子実行は、SDK を使用してのみ作成できます。

このコード例では、`hello_with_children.py` スクリプトで [`child_run()`](/python/api/azureml-core/azureml.core.run%28class%29#child-run-name-none--run-id-none--outputs-none-) メソッドを使用して、渡された実行から 5 つの子実行のバッチを作成します。

```python
!more hello_with_children.py
src = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_run = exp.submit(src)
local_run.wait_for_completion(show_output=True)
print(local_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> 子実行は、範囲外になると、自動的に完了とマークされます。

多数の子実行を効率よく作成するには、[`create_children()`](/python/api/azureml-core/azureml.core.run.run#create-children-count-none--tag-key-none--tag-values-none-) メソッドを使用します。 実行を作成するたびにネットワーク呼び出しが行われるため、実行のバッチを作成した方が、1 つずつ作成するよりも効率的です。

### <a name="submit-child-runs"></a>子実行を送信する

親実行から子実行を送信することもできます。 そうすることで、親実行と子実行の階層を作成できます。 親のない子実行を作成することはできません。親実行で子実行が起動されただけであっても、階層を作成する必要があります。 すべての実行の状態は独立しています。1 つ以上の子実行が取り消されたか失敗した場合でも、親は正常に `"Completed"` の状態になります。  

子実行で、親実行と異なる実行構成を使用することを望む場合があります。 たとえば、子に GPU ベースの構成を使用しながら、親に対して非力な CPU ベースの構成を使用できます。 他の一般的な目的は、各子に異なる引数とデータを渡すことです。 子実行をカスタマイズするには、子実行の `ScriptRunConfig` オブジェクトを作成します。 下のコードにより、次のことが行われます。

- `"gpu-cluster"` という名前のコンピューティング リソースを、ワークスペース `ws` から取得します
- 子 `ScriptRunConfig` オブジェクトに渡される異なる引数値を繰り返します
- カスタム コンピューティング リソースと引数を使用して、新しい子実行を作成して送信します
- すべての子実行が完了するまでブロックします

```python
# parent.py
# This script controls the launching of child scripts
from azureml.core import Run, ScriptRunConfig

compute_target = ws.compute_targets["gpu-cluster"]

run = Run.get_context()

child_args = ['Apple', 'Banana', 'Orange']
for arg in child_args: 
    run.log('Status', f'Launching {arg}')
    child_config = ScriptRunConfig(source_directory=".", script='child.py', arguments=['--fruit', arg], compute_target=compute_target)
    # Starts the run asynchronously
    run.submit_child(child_config)

# Experiment will "complete" successfully at this point. 
# Instead of returning immediately, block until child runs complete

for child in run.get_children():
    child.wait_for_completion()
```

同じ構成、引数、および入力を使用して多数の子実行を効率的に作成するには、[`create_children()`](/python/api/azureml-core/azureml.core.run.run#create-children-count-none--tag-key-none--tag-values-none-) メソッドを使用します。 実行を作成するたびにネットワーク呼び出しが行われるため、実行のバッチを作成した方が、1 つずつ作成するよりも効率的です。

次のようにすれば、子実行内から親実行 ID を確認できます。

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>子実行を照会する

特定の親の子実行をクエリするには、[`get_children()`](/python/api/azureml-core/azureml.core.run%28class%29#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) メソッドを使用します。 ``recursive = True`` 引数を指定すると、入れ子になった子と孫のツリーを照会できます。

```python
print(parent_run.get_children())
```

### <a name="log-to-parent-or-root-run"></a>親またはルートの実行にログを記録する

`Run.parent` フィールドを使用すると、現在の子実行を開始した実行にアクセスできます。 一般的なユースケースは、ログ結果を 1 か所に統合する場合です。 子実行は非同期に実行され、子実行が完了するまで待機する親の機能を超えた順序付けや同期の保証はないことに注意してください。

```python
# in child (or even grandchild) run

def root_run(self : Run) -> Run :
    if self.parent is None : 
        return self
    return root_run(self.parent)

current_child_run = Run.get_context()
root_run(current_child_run).log("MyMetric", f"Data from child run {current_child_run.id}")

```

## <a name="example-notebooks"></a>サンプルの Notebook

次のノートブックは、この記事の概念を示しています。

* ロギング API の詳細については、[ロギング API ノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb)に関するページを参照してください。

* Azure Machine Learning SDK での実行の管理の詳細については、[実行の管理ノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* 自分の実験のメトリックのログを記録する方法の詳細については、[トレーニングの実行中にメトリックを記録する](how-to-track-experiments.md)に関するページを参照してください。
* Azure Machine Learning からリソースとログを監視する方法については、「[Azure Machine Learning の監視](monitor-azure-machine-learning.md)」を参照してください。
