---
title: トレーニングの実行中にメトリックを記録する
titleSuffix: Azure Machine Learning service
description: 実験を追跡し、メトリックを監視して、モデルの作成プロセスを拡張できます。 トレーニング スクリプトにログ記録を追加する方法、実験を送信する方法、実行中のジョブの進行状況を確認する方法、および実行の結果を表示する方法について説明します。
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 7ef3cfe1df792721db3fe3657c08f58ca82e3c91
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652316"
---
# <a name="log-metrics-during-training-runs-in-azure-machine-learning"></a>Azure Machine Learning 内でトレーニングの実行中にメトリックを記録する

Azure Machine Learning service では、実験を追跡し、メトリックを監視して、モデルの作成プロセスを拡張できます。 この記事では、トレーニング スクリプトへのログ記録の追加、実験の実行の送信、実行の監視、実行の結果の表示を行う方法について説明します。

## <a name="list-of-training-metrics"></a>トレーニング メトリックの一覧 

実験のトレーニング中に、次のメトリックを実行に追加できます。 実行で追跡できる内容の詳細な一覧については、[Run クラスのリファレンス ドキュメント](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)をご覧ください。

|Type| Python 関数 | メモ|
|----|:----|:----|
|スカラー値 |関数:<br>`run.log(name, value, description='')`<br><br>例:<br>run.log("accuracy", 0.95) |指定した名前で実行に数値または文字列値を記録します。 実行にメトリックを記録すると、メトリックは実験の実行レコードに格納されます。  同じメトリックを実行内で複数回記録でき、結果はそのメトリックのベクターと見なされます。|
|リスト|関数:<br>`run.log_list(name, value, description='')`<br><br>例:<br>run.log_list("accuracies", [0.6, 0.7, 0.87]) | 指定した名前で実行に値リストを記録します。|
|行|関数:<br>`run.log_row(name, description=None, **kwargs)`<br>例:<br>run.log_row("Y over X", x=1, y=0.4) | *log_row* を使用すると、kwargs で記述されているように、複数の列を含むメトリックが作成されます。 各名前付きパラメーターにより、指定した値の列が生成されます。  *log_row* を 1 回呼び出すと任意のタプルを記録でき、ループ内で複数回呼び出すと完全なテーブルを生成できます。|
|テーブル|関数:<br>`run.log_table(name, value, description='')`<br><br>例:<br>run.log_table("Y over X", {"x":[1, 2, 3], "y":[0.6, 0.7, 0.89]}) | 指定した名前で実行にディクショナリ オブジェクトを記録します。 |
|イメージ|関数:<br>`run.log_image(name, path=None, plot=None)`<br><br>例:<br>`run.log_image("ROC", plt)` | 実行レコードにイメージを記録します。 log_image を使用してイメージ ファイルに記録するか、または matplotlib を使用して実行にプロットします。  これらのイメージは実行レコードで表示して比較できます。|
|実行にタグを付ける|関数:<br>`run.tag(key, value=None)`<br><br>例:<br>run.tag("selected", "yes") | 文字列キーと省略可能な文字列値で実行にタグを付けます。|
|ファイルまたはディレクトリをアップロードする|関数:<br>`run.upload_file(name, path_or_stream)`<br> <br> 例:<br>run.upload_file("best_model.pkl", "./model.pkl") | 実行レコードにファイルをアップロードします。 実行は指定された出力ディレクトリ内のファイルを自動的にキャプチャします。ディレクトリの既定値は、ほとんどの実行種類で "./outputs" です。  追加のファイルをアップロードする必要がある場合、または出力ディレクトリが指定されていない場合にのみ、upload_file を使用します。 出力ディレクトリにアップロードされるように、名前に `outputs` を追加することをお勧めします。 `run.get_file_names()` を呼び出すことにより、この実行レコードに関連付けられているすべてのファイルの一覧を取得できます。|

> [!NOTE]
> スカラー、リスト、行、およびテーブルのメトリックの型には、float、integer、または string を使用できます。

## <a name="start-logging-metrics"></a>メトリックのログ記録を開始する

実験を追跡または監視する場合は、実行を送信するときにログ記録を開始するコードを追加する必要があります。 実行の送信をトリガーする方法を以下に示します。
* __Run.start_logging__ - トレーニング スクリプトにログ関数を追加し、指定した実験で対話型のログ セッションを開始します。 **start_logging** では、ノートブックなどのシナリオで使用するための対話型の実行が作成されます。 セッション中にログに記録されるすべてのメトリックは、実験の実行レコードに追加されます。
* __ScriptRunConfig__ - トレーニング スクリプトにログ関数を追加し、実行でスクリプト フォルダー全体を読み込みます。  **ScriptRunConfig** は、スクリプト実行の構成をセットアップするためのクラスです。 このオプションでは、監視コードを追加して、完了の通知を受け取るか、または監視するためのビジュアル ウィジェットを取得できます。

## <a name="set-up-the-workspace"></a>ワークスペースを設定する
ログを追加して実験を送信する前に、ワークスペースを設定する必要があります。

1. ワークスペースを読み込みます。 ワークスペース構成の設定について詳しくは、「[Azure Machine Learning service ワークスペースを作成する](setup-create-workspace.md#sdk)」の手順に従ってください。

   ```python
   from azureml.core import Experiment, Run, Workspace
   import azureml.core
  
   ws = Workspace(workspace_name = <<workspace_name>>,
               subscription_id = <<subscription_id>>,
               resource_group = <<resource_group>>)
   ```
  
## <a name="option-1-use-startlogging"></a>オプション 1:start_logging を使用する

**start_logging** では、ノートブックなどのシナリオで使用するための対話型の実行が作成されます。 セッション中にログに記録されるすべてのメトリックは、実験の実行レコードに追加されます。

次の例では、ローカルの Jupyter Notebook でローカルに単純な sklearn Ridge モデルをトレーニングします。 さまざまな環境への実験の送信について詳しくは、「[Azure Machine Learning サービスでモデルのトレーニング用のコンピューティング ターゲットを設定する](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets)」をご覧ください。

1. ローカルの Jupyter Notebook でトレーニング スクリプトを作成します。 

   ``` python
   # load diabetes dataset, a well-known small dataset that comes with scikit-learn
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from sklearn.externals import joblib

   X, y = load_diabetes(return_X_y = True)
   columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {
      "train":{"X": X_train, "y": y_train},        
      "test":{"X": X_test, "y": y_test}
   }
   reg = Ridge(alpha = 0.03)
   reg.fit(data['train']['X'], data['train']['y'])
   preds = reg.predict(data['test']['X'])
   print('Mean Squared Error is', mean_squared_error(preds, data['test']['y']))
   joblib.dump(value = reg, filename = 'model.pkl');
   ```

2. Azure Machine Learning service SDK を使用して実験の追跡を追加し、永続化されたモデルを実験の実行レコードにアップロードします。 次のコードでは、タグを追加し、ログを記録して、実験の実行にモデル ファイルをアップロードします。

   ```python
   # Get an experiment object from Azure Machine Learning
   experiment = Experiment(workspace = ws, name = "train-within-notebook")
  
   # Create a run object in the experiment
   run = experiment.start_logging()# Log the algorithm parameter alpha to the run
   run.log('alpha', 0.03)

   # Create, fit, and test the scikit-learn Ridge regression model
   regression_model = Ridge(alpha=0.03)
   regression_model.fit(data['train']['X'], data['train']['y'])
   preds = regression_model.predict(data['test']['X'])

   # Output the Mean Squared Error to the notebook and to the run
   print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
   run.log('mse', mean_squared_error(data['test']['y'], preds))

   # Save the model to the outputs directory for capture
   joblib.dump(value=regression_model, filename='outputs/model.pkl')

   # Take a snapshot of the directory containing this notebook
   run.take_snapshot('./')

   # Complete the run
   run.complete()
  
   ```

スクリプトが ```run.complete()``` で終了すると、実行は完了としてマークされます。  この関数は通常、対話型ノートブックのシナリオで使用されます。

## <a name="option-2-use-scriptrunconfig"></a>オプション 2:ScriptRunConfig を使用する

[**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) は、スクリプト実行の構成を設定するためのクラスです。 このオプションでは、監視コードを追加して、完了の通知を受け取るか、または監視するためのビジュアル ウィジェットを取得できます。

この例は、上記の基本的な sklearn Ridge モデルを拡張します。 単純なパラメーター スイープを行ってモデルのアルファ値をスイープし、実行でのメトリックとトレーニング済みモデルを実験にキャプチャします。 例は、ユーザー管理の環境に対してローカルに実行します。 

1. トレーニング スクリプト `train.py` を作成します。

   ```python
   # train.py

   import os
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from azureml.core.run import Run
   from sklearn.externals import joblib

   import numpy as np

   #os.makedirs('./outputs', exist_ok = True)

   X, y = load_diabetes(return_X_y = True)

   run = Run.get_context()

   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {"train": {"X": X_train, "y": y_train},
          "test": {"X": X_test, "y": y_test}}

   # list of numbers from 0.0 to 1.0 with a 0.05 interval
   alphas = mylib.get_alphas()

   for alpha in alphas:
      # Use Ridge algorithm to create a regression model
      reg = Ridge(alpha = alpha)
      reg.fit(data["train"]["X"], data["train"]["y"])

      preds = reg.predict(data["test"]["X"])
      mse = mean_squared_error(preds, data["test"]["y"])
      # log the alpha and mse values
      run.log('alpha', alpha)
      run.log('mse', mse)

      model_file_name = 'ridge_{0:.2f}.pkl'.format(alpha)
      # save model in the outputs folder so it automatically get uploaded
      with open(model_file_name, "wb") as file:
          joblib.dump(value = reg, filename = model_file_name)

      # upload the model file explicitly into artifacts 
      run.upload_file(name = model_file_name, path_or_stream = model_file_name)

      # register the model
      #run.register_model(file_name = model_file_name)

      print('alpha is {0:.2f}, and mse is {1:0.2f}'.format(alpha, mse))
  
   ```

2. `train.py` スクリプトは、Ridge モデルで使用するアルファ値の一覧を取得できる `mylib.py` を参照します。

   ```python
   # mylib.py
  
   import numpy as np

   def get_alphas():
      # list of numbers from 0.0 to 1.0 with a 0.05 interval
      return np.arange(0.0, 1.0, 0.05)
   ```

3. ユーザー管理のローカル環境を構成します。

   ```python
   from azureml.core.runconfig import RunConfiguration

   # Editing a run configuration property on-fly.
   run_config_user_managed = RunConfiguration()

   run_config_user_managed.environment.python.user_managed_dependencies = True

   # You can choose a specific Python environment by pointing to a Python path 
   #run_config.environment.python.interpreter_path = '/home/user/miniconda3/envs/sdk2/bin/python'
   ```

4. ```train.py``` スクリプトを送信して、ユーザー管理の環境内で実行します。 ```mylib.py``` ファイルも含めて、このスクリプト フォルダー全体がトレーニングのために送信されます。

   ```python
   from azureml.core import ScriptRunConfig
  
   experiment = Experiment(workspace=ws, name="train-on-local")
   src = ScriptRunConfig(source_directory = './', script = 'train.py', run_config = run_config_user_managed)
   run = experiment.submit(src)
   ```

## <a name="cancel-a-run"></a>実行をキャンセルする

実行の送信後、実験名と実行 ID を知っていれば、オブジェクト参照を失った場合でも、キャンセルできます。 


```python
from azureml.core import Experiment
exp = Experiment(ws, "my-experiment-name")

# if you don't know the run id, you can list all runs under an experiment
for r in exp.get_runs():  
    print(r.id, r.get_status())

# if you know the run id, you can "rehydrate" the run
from azureml.core import get_run
r = get_run(experiment=exp, run_id="my_run_id", rehydrate=True)
  
# check the returned run type and status
print(type(r), r.get_status())

# you can cancel a run if it hasn't completed or failed
if r.get_status() not in ['Complete', 'Failed']:
    r.cancel()
```
現在、ScriptRun および PipelineRun 型のみでキャンセル操作がサポートされています。

さらに、次のコマンドを使用して、CLI での実行を取り消すことができます。
```shell
az ml run cancel -r <run_id> -p <project_path>
```


## <a name="view-run-details"></a>実行の詳細を表示する

### <a name="monitor-run-with-jupyter-notebook-widgets"></a>Jupyter Notebook ウィジェットで実行を監視する
**ScriptRunConfig** メソッドを使用して実行を送信するときに、Jupyter Notebook ウィジェットを使用して実行の進行状況を見ることができます。 実行の送信と同様に、このウィジェットも非同期です。また、ジョブが完了するまで 10 秒から 15 秒ごとにライブ更新を提供します。

1. 実行が完了するのを待っている間、Jupyter ウィジェットを表示します。

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Jupyter Notebook ウィジェットのスクリーンショット](./media/how-to-track-experiments/widgets.PNG)

2. **[自動化された機械学習の実行の場合]** 前回の実行のグラフにアクセスするには。 `<<experiment_name>>` を適切な実験名に置き換えます。

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![自動機械学習の場合の Jupyter Notebook ウィジェット](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


パイプラインをさらに詳しく見るには、調べるパイプラインをテーブルでクリックすると、Azure portal のポップアップにグラフがレンダリングされます。

### <a name="get-log-results-upon-completion"></a>完了時にログの結果を取得する

モデルのトレーニングと監視はバックグラウンドで行われるので、待機中に他のタスクを実行できます。 また、モデルのトレーニングが完了するまで待ってから、さらにコードを実行することもできます。 **ScriptRunConfig** を使用するときは、```run.wait_for_completion(show_output = True)``` を使用してモデルのトレーニングの完了を表示できます。 ```show_output``` フラグを指定すると、詳細な出力が提供されます。 
  
### <a name="query-run-metrics"></a>実行のメトリックのクエリを行う

```run.get_metrics()``` を使用して、トレーニング済みモデルのメトリックを表示できます。 上の例でログに記録されたすべてのメトリックを取得して、最適なモデルを決定できます。

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-the-azure-portal"></a>Azure portal で実験を表示する

実験の実行が完了したら、記録された実験の実行レコードを参照できます。 履歴には 2 つの方法でアクセスできます。

* ```print(run.get_portal_url())``` で実行に対する URL を直接取得します
* 実行の名前を送信することで、実行の詳細を表示します (この場合は ```run```)。 この方法では、実験名、ID、種類、状態、詳細ページ、Azure portal へのリンク、およびドキュメントへのリンクが示されます。

実行へのリンクでは、Azure portal 内の実行詳細ページに直接移動します。 ここでは、任意のプロパティ、追跡対象のメトリック、イメージ、および実験に記録されたグラフを見ることができます。 この場合は、MSE とアルファ値を記録しました。

  ![Azure portal での実行の詳細](./media/how-to-track-experiments/run-details-page-web.PNG)

また、実行に対する出力またはログを表示したり、送信した実験のスナップショットをダウンロードして他のユーザーと実験フォルダーを共有したりすることもできます。

### <a name="viewing-charts-in-run-details"></a>実行の詳細でのグラフの表示

実行中にさまざまな種類のメトリックを記録し、Azure portal でグラフとして表示するために、ロギング APIを使用するさまざまな方法があります。 

|ログに記録される値|コード例| ポータルでの表示|
|----|----|----|
|数値の配列をログに記録します| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|単一変数の折れ線グラフ|
|(for ループ内からのように) 繰り返し使用される、同じメトリック名を持つ単一数値をログに記録します| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| 単一変数の折れ線グラフ|
|2 つの数値列が繰り返し含まれる 1 行をログに記録します|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|変数が 2 つの折れ線グラフ|
|2 つの数値列を含むテーブルをログに記録します|`run.log_table(name='Sine Wave', value=sines)`|変数が 2 つの折れ線グラフ|

<a name="auto"></a>
## <a name="understanding-automated-ml-charts"></a>自動 ML グラフの説明

ノートブックで自動 ML ジョブを送信した後は、機械学習サービス ワークスペースで実行履歴を確認できます。 

各項目の詳細情報
+ [分類モデルのグラフと曲線](#classification)
+ [回帰モデルのチャートとグラフ](#regression)
+ [モデル説明能力](#model-explain-ability-and-feature-importance)


### <a name="view-the-run-charts"></a>実行のグラフを表示する

1. ワークスペースに移動します。 

1. ワークスペースの左側のパネルで **[実験]** を選択します。

   ![実験メニューのスクリーンショット](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_menu.PNG)

1. 興味のある実験を選択します。

   ![実験リスト](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_list.PNG)

1. テーブルで実行番号を選択します。

   ![実験の実行](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_run.PNG)

1. テーブルで、さらに調べるモデルの繰り返し番号を選択します。

   ![実験モデル](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_model.PNG)



### <a name="classification"></a>分類

Azure Machine Learning の自動機械学習機能を使って作成されたすべての分類モデルについて、次のグラフを見ることができます。 
+ [混同行列](#confusion-matrix)
+ [精度/再現率グラフ](#precision-recall-chart)
+ [受信者操作特性 (ROC)](#roc)
+ [リフト曲線](#lift-curve)
+ [ゲイン曲線](#gains-curve)
+ [調整プロット](#calibration-plot)

#### <a name="confusion-matrix"></a>混同行列

混同行列は、分類モデルのパフォーマンスの記述に使用します。 各行には true クラスのインスタンスが表示され、各列は予測されたクラスのインスタンスを表します。 混同行列では、特定のモデルで正しく分類されたラベルと、間違って分類ラベルが示されます。

分類問題の場合、Azure Machine Learning では作成された各モデルに対して混同行列が自動的に提供されます。 自動 ML では、混同行列ごとに、正しく分類されたラベルは緑で、間違って分類されたラベルは赤で示されます。 円のサイズは、そのビンのサンプルの数を表します。 さらに、各予測ラベルと各 true ラベルの頻度の数が、隣の棒グラフで提供されます。 

例 1:精度の低い分類モデル![精度の低い分類モデル](./media/how-to-track-experiments/azure-machine-learning-auto-ml-confusion_matrix1.PNG)

例 2:精度の高い分類モデル (理想的)![精度の高い分類モデル](./media/how-to-track-experiments/azure-machine-learning-auto-ml-confusion_matrix2.PNG)


#### <a name="precision-recall-chart"></a>精度/再現率グラフ

このグラフでは、各モデルの精度/再現率曲線を比較して、特定のビジネスの問題に対して精度と再現率の間の関係が許容できるモデルを特定できます。 このグラフでは、マクロ平均精度/再現率、ミクロ平均精度/再現率、およびモデルのすべてのクラスに関連付けられた精度/再現率が示されます。

精度という用語は、分類子がすべてのインスタンスに正しくラベルを付ける能力を表します。 再現率は、分類子が特定のラベルのすべてのインスタンスを見つける能力を表します。 精度/再現率曲線は、これら 2 つの概念の間の関係を示します。 理想的なモデルでは、100% の精度と 100% の再現率になります。

例 1:精度と再現率が低い分類モデル![精度と再現率が低い分類モデル](./media/how-to-track-experiments/azure-machine-learning-auto-ml-precision_recall1.PNG)

例 2:精度と再現率がほぼ 100% の分類モデル (理想的)![精度と再現率がほぼ 100% の分類モデル](./media/how-to-track-experiments/azure-machine-learning-auto-ml-precision_recall2.PNG)

#### <a name="roc"></a>ROC

受信者操作特性 (ROC) は、特定のモデルについて正しく分類されたラベルと間違って分類されたラベルを対比したプロットです。 ROC 曲線は、誤検知のラベルが示されないため、高バイアスのデータセットでモデルをトレーニングするときは、あまり役に立たないことがあります。

例 1:正しいラベルが少なく間違ったラベルが多い分類モデル![正しいラベルが少なく間違ったラベルが多い分類モデル](./media/how-to-track-experiments/azure-machine-learning-auto-ml-roc1.PNG)

例 2:正しいラベルが多く間違ったラベルが少ない分類モデル![正しいラベルが多く間違ったラベルが少ない分類モデル](./media/how-to-track-experiments/azure-machine-learning-auto-ml-roc2.PNG)

#### <a name="lift-curve"></a>リフト曲線

Azure Machine Learning で自動的に作成されたモデルのリフトとベースラインを比較して、その特定のモデルでの値のゲインを確認できます。

リフト チャートは、分類モデルのパフォーマンスの評価に使用されます。 モデルを使用しない場合と比較して、モデルを使用するとどの程度の向上が予想されるかがわかります。 

例 1:ランダムに選択されたモデルよりパフォーマンスが悪いモデル![ランダムに選択されたモデルより悪い分類モデル](./media/how-to-track-experiments/azure-machine-learning-auto-ml-lift_curve1.PNG)

例 2:ランダムに選択されたモデルよりパフォーマンスがよいモデル![パフォーマンスがよい分類モデル](./media/how-to-track-experiments/azure-machine-learning-auto-ml-lift_curve2.PNG)

#### <a name="gains-curve"></a>ゲイン曲線

ゲイン チャートでは、データの各部分によって分類モデルのパフォーマンスが評価されます。 データ セットの各パーセンタイルについて、ランダムに選択されたモデルと比較してパフォーマンスがどの程度向上すると予想されるかがわかります。

モデルからの望ましいゲインに対応するパーセンテージを使用して分類カットオフを選択するには、累積ゲイン チャートを使用します。 この情報では、付随するリフト チャートの結果を調べる別の方法が提供されます。

例 1:ゲインが最小限の分類モデル![ゲインが最小限の分類モデル](./media/how-to-track-experiments/azure-machine-learning-auto-ml-gains_curve1.PNG)

例 2:ゲインが大きい分類モデル![ゲインが大きい分類モデル](./media/how-to-track-experiments/azure-machine-learning-auto-ml-gains_curve2.PNG)

#### <a name="calibration-plot"></a>調整プロット

すべての分類の問題について、マイクロ平均、マクロ平均、および特定の予測モデルの各クラスに対する調整ラインを確認できます。 

調整プロットは、予測モデルの信頼度を示すために使用されます。 これを行うため、予測される確率と実際の確率の間の関係が示されます。"確率" は、特定のインスタンスがいくつかのラベルに属する可能性を表します。 適切に調整されたモデルは y=x の線と一致し、予測の信頼性は妥当です。 過剰信頼モデルは y=0 の線と一致し、予測確率はありますが、実際の確率はありません。

例 1:適切に調整されたモデル![適切に調整されたモデル](./media/how-to-track-experiments/azure-machine-learning-auto-ml-calib_curve1.PNG)

例 2:過剰に信頼されたモデル![過剰に信頼されたモデル](./media/how-to-track-experiments/azure-machine-learning-auto-ml-calib_curve2.PNG)

### <a name="regression"></a>回帰
Azure Machine Learning の自動機械学習機能を使って作成されたすべての回帰モデルについて、次のグラフを見ることができます。 
+ [予測とTrue](#pvt)
+ [残差のヒストグラム](#histo)

<a name="pvt"></a>

#### <a name="predicted-vs-true"></a>予測とTrue

予測とTrue では、回帰問題に対する予測値とそれに相関する True 値の間の関係が示されます。 このグラフを使用してモデルのパフォーマンスを測定できます。予測値が y=x の線に近いほど、予測モデルの精度が高いことを示します。

各実行の後で、各回帰モデルの予測と True のグラフを表示できます。 データのプライバシーを保護するため、値はビンにまとめられており、各ビンのサイズはグラフ領域の下部に棒グラフとして示されます。 許容誤差を示す明るい網掛け領域で、予測モデルをモデルの理想値と比較できます。

例 1:予測の精度が低い回帰モデル![予測の精度が低い回帰モデル](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression1.PNG)

例 2:予測の精度が高い回帰モデル![予測の精度が高い回帰モデル](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression2.PNG)

<a name="histo"></a>

#### <a name="histogram-of-residuals"></a>残差のヒストグラム

残差は、観察された y から予測された y を引いた値を表します。 低バイアスでの許容誤差を示すため、残差のヒストグラムは 0 を中心とするベル曲線として成形する必要があります。 

例 1:エラーにバイアスがある回帰モデル![エラーにバイアスがある SA 回帰モデル](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression3.PNG)

例 2:エラーがより均等に分布している回帰モデル![エラーがより均等に分布している回帰モデル](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression4.PNG)

### <a name="model-explain-ability-and-feature-importance"></a>モデル説明能力と特徴の重要度

特徴の重要度では、モデルの構築における各特徴の価値を示すスコアが提供されます。 モデル全体および予測モデルの各クラスについて、特徴の重要度のスコアを確認できます。 特徴ごとに、各クラスおよび全体との重要度の比較を確認できます。

![特徴説明能力](./media/how-to-track-experiments/azure-machine-learning-auto-ml-feature_explain1.PNG)

## <a name="example-notebooks"></a>サンプルの Notebook
次の Notebook は、この記事の概念を示しています。
* [how-to-use-azureml/training/train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/training/logging-api/logging-api.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>次の手順

次の手順を試して、Azure Machine Learning SDK for Python を使用する方法を学習してください。

* 最適なモデルを登録し、チュートリアルでデプロイする方法の例については、「[Azure Machine Learning で画像分類モデルをトレーニングする](tutorial-train-models-with-aml.md)」をご覧ください。

* [Azure Machine Learning で PyTorch モデルをトレーニングする](how-to-train-pytorch.md)方法を学習してください。
