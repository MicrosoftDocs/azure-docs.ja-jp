---
title: ML の実験とメトリックをログに記録する
titleSuffix: Azure Machine Learning
description: Azure ML の実験を監視し、実行のメトリックを監視することでモデルの作成プロセスを強化します。 トレーニング スクリプトにログ記録を追加し、記録された実行結果を表示します。  run.log、Run.start_logging、ScriptRunConfig を使用します。
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/05/2019
ms.custom: seodec18
ms.openlocfilehash: 3de34c1da20df17fb5fb65cef28669fb73ff33a5
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978562"
---
# <a name="monitor-azure-ml-experiment-runs-and-metrics"></a>Azure ML の実験の実行とメトリックを監視する
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

実験を追跡し、実行のメトリックを監視することでモデルの作成プロセスを強化します。 この記事では、Azure Machine Learning におけるトレーニング スクリプトへのログ記録コードの追加、実験の実行の送信、実行の監視、結果の検査を行う方法について説明します。

> [!NOTE]
> Azure Machine Learning では、トレーニング中に、自動化された機械学習の実行や、トレーニング ジョブを実行する Docker コンテナーなど、他のソースから情報をログに記録することもできます。 これらのログについては記載されていません。 問題が発生し、Microsoft サポートに問い合わせた場合、サポートはトラブルシューティングの際にこれらのログを使用できる可能性があります。

> [!TIP]
> このドキュメントの情報は主に、モデルのトレーニング プロセスを監視したいデータ サイエンティストや開発者を対象としています。 Azure Machine Learning からリソース使用状況やイベント (クォータ、トレーニング実行の完了、モデル デプロイの完了など) を監視することに関心がある管理者の方は、「[Azure Machine Learning の監視](monitor-azure-machine-learning.md)」を参照してください。

## <a name="available-metrics-to-track"></a>追跡で使用できるメトリック

実験のトレーニング中に、次のメトリックを実行に追加できます。 実行で追跡できる内容の詳細な一覧については、[Run クラスのリファレンス ドキュメント](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)をご覧ください。

|種類| Python 関数 | メモ|
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

## <a name="choose-a-logging-option"></a>ログ記録オプションの選択

実験を追跡または監視する場合は、実行を送信するときにログ記録を開始するコードを追加する必要があります。 実行の送信をトリガーする方法を以下に示します。
* __Run.start_logging__ - トレーニング スクリプトにログ関数を追加し、指定した実験で対話型のログ セッションを開始します。 **start_logging** では、ノートブックなどのシナリオで使用するための対話型の実行が作成されます。 セッション中にログに記録されるすべてのメトリックは、実験の実行レコードに追加されます。
* __ScriptRunConfig__ - トレーニング スクリプトにログ関数を追加し、実行でスクリプト フォルダー全体を読み込みます。  **ScriptRunConfig** は、スクリプト実行の構成をセットアップするためのクラスです。 このオプションでは、監視コードを追加して、完了の通知を受け取るか、または監視するためのビジュアル ウィジェットを取得できます。

## <a name="set-up-the-workspace"></a>ワークスペースを設定する
ログを追加して実験を送信する前に、ワークスペースを設定する必要があります。

1. ワークスペースを読み込みます。 ワークスペースの構成の設定について詳しくは、[ワークスペース構成ファイル](how-to-configure-environment.md#workspace)についての記事を参照してください。

   ```python
   from azureml.core import Experiment, Run, Workspace
   import azureml.core
  
   ws = Workspace.from_config()
   ```
  
## <a name="option-1-use-start_logging"></a>オプション 1:start_logging を使用する

**start_logging** では、ノートブックなどのシナリオで使用するための対話型の実行が作成されます。 セッション中にログに記録されるすべてのメトリックは、実験の実行レコードに追加されます。

次の例では、ローカルの Jupyter Notebook でローカルに単純な sklearn Ridge モデルをトレーニングします。 さまざまな環境に実験を送信する方法の詳細については、[Azure Machine Learning でモデル トレーニング用のコンピューティング先を設定する](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets)方法に関するページをご覧ください。

1. ローカルの Jupyter Notebook でトレーニング スクリプトを作成します。 

   ```python
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

2. Azure Machine Learning SDK を使用して実験の追跡を追加し、永続化されたモデルを実験の実行レコードにアップロードします。 次のコードでは、タグを追加し、ログを記録して、実験の実行にモデル ファイルをアップロードします。

   ```python
    # Get an experiment object from Azure Machine Learning
    experiment = Experiment(workspace=ws, name="train-within-notebook")
    
    # Create a run object in the experiment
    run =  experiment.start_logging()
    # Log the algorithm parameter alpha to the run
    run.log('alpha', 0.03)
    
    # Create, fit, and test the scikit-learn Ridge regression model
    regression_model = Ridge(alpha=0.03)
    regression_model.fit(data['train']['X'], data['train']['y'])
    preds = regression_model.predict(data['test']['X'])
    
    # Output the Mean Squared Error to the notebook and to the run
    print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
    run.log('mse', mean_squared_error(data['test']['y'], preds))
    
    # Save the model to the outputs directory for capture
    model_file_name = 'outputs/model.pkl'
    
    joblib.dump(value = regression_model, filename = model_file_name)
    
    # upload the model file explicitly into artifacts 
    run.upload_file(name = model_file_name, path_or_stream = model_file_name)
    
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
   from azureml.core import Environment
    
   # Editing a run configuration property on-fly.
   user_managed_env = Environment("user-managed-env")
    
   user_managed_env.python.user_managed_dependencies = True
    
   # You can choose a specific Python environment by pointing to a Python path 
   #user_managed_env.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
   ```

4. ```train.py``` スクリプトを送信して、ユーザー管理の環境内で実行します。 ```mylib.py``` ファイルも含めて、このスクリプト フォルダー全体がトレーニングのために送信されます。

   ```python
   from azureml.core import ScriptRunConfig
    
   exp = Experiment(workspace=ws, name="train-on-local")
   src = ScriptRunConfig(source_directory='./', script='train.py')
   src.run_config.environment = user_managed_env
   run = exp.submit(src)
   ```

## <a name="manage-a-run"></a>実行の管理

[トレーニングの実行の開始、監視、キャンセル](how-to-manage-runs.md)に関する記事では、実験の管理方法に関する特定の Azure Machine Learning ワークフローについて紹介しています。

## <a name="view-run-details"></a>実行の詳細を表示する

### <a name="view-activequeued-runs-from-the-browser"></a>ブラウザーからアクティブな、またはキューに入れられた実行を表示する

モデルをトレーニングするために使用されるコンピューティング先は共有リソースです。 そのため、特定の時点でキューに入れられた、またはアクティブな複数の実行が含まれている可能性があります。 ブラウザーから特定のコンピューティング先のための実行を表示するには、次の手順を使用します。

1. [Azure Machine Learning Studio](https://ml.azure.com/) から、ワークスペースを選択し、ページの左側から __[コンピューティング]__ を選択します。

1. __[Training Clusters] (トレーニング クラスター)__ を選択して、トレーニングに使用されるコンピューティング先の一覧を表示します。 次に、クラスターを選択します。

    ![トレーニング クラスターを選択する](./media/how-to-track-experiments/select-training-compute.png)

1. __[実行]__ を選択します。 このクラスターを使用する実行の一覧が表示されます。 特定の実行の詳細を表示するには、 __[実行]__ 列のリンクを使用します。 実験の詳細を表示するには、 __[実験]__ 列のリンクを使用します。

    ![トレーニング クラスターの実行を選択する](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > ある実行に子実行が含まれる場合があるため、1 つのトレーニング ジョブに複数のエントリが存在する可能性があります。

実行は、完了するとこのページには表示されなくなります。 完了した実行に関する情報を表示するには、スタジオの __[実験]__ セクションにアクセスし、実験と実行を選択します。 詳細については、「[実行のメトリックのクエリを行う](#queryrunmetrics)」のセクションを参照してください。

### <a name="monitor-run-with-jupyter-notebook-widget"></a>Jupyter Notebook ウィジェットで実行を監視する
**ScriptRunConfig** メソッドを使用して実行を送信するときに、[Jupyter ウィジェット](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)を使用して実行の進行状況を見ることができます。 実行の送信と同様に、このウィジェットも非同期です。また、ジョブが完了するまで 10 秒から 15 秒ごとにライブ更新を提供します。

1. 実行が完了するのを待っている間、Jupyter ウィジェットを表示します。

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Jupyter Notebook ウィジェットのスクリーンショット](./media/how-to-track-experiments/run-details-widget.png)

   ワークスペース内の同じ表示へのリンクを取得することもできます。

   ```python
   print(run.get_portal_url())
   ```

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


パイプラインをさらに詳しく見るには、調べるパイプラインをテーブルでクリックすると、Azure Machine Learning Studio のポップアップにグラフがレンダリングされます。

### <a name="get-log-results-upon-completion"></a>完了時にログの結果を取得する

モデルのトレーニングと監視はバックグラウンドで行われるので、待機中に他のタスクを実行できます。 また、モデルのトレーニングが完了するまで待ってから、さらにコードを実行することもできます。 **ScriptRunConfig** を使用するときは、```run.wait_for_completion(show_output = True)``` を使用してモデルのトレーニングの完了を表示できます。 ```show_output``` フラグを指定すると、詳細な出力が提供されます。 

<a id="queryrunmetrics"></a>

### <a name="query-run-metrics"></a>実行のメトリックのクエリを行う

```run.get_metrics()``` を使用して、トレーニング済みモデルのメトリックを表示できます。 上の例でログに記録されたすべてのメトリックを取得して、最適なモデルを決定できます。

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-your-workspace-in-azure-machine-learning-studiohttpsmlazurecom"></a>[Azure Machine Learning Studio](https://ml.azure.com) でワークスペースの実験を表示する

実験の実行が完了したら、記録された実験の実行レコードを参照できます。 [Azure Machine Learning Studio](https://ml.azure.com) から履歴にアクセスできます。

[実験] タブに移動し、実験を選択します。 実験実行ダッシュボードが表示されます。ここで、実行ごとにログに記録された、追跡対象のメトリックとグラフを確認できます。 この場合は、MSE とアルファ値を記録しました。

  ![Azure Machine Learning Studio での実行の詳細](./media/how-to-track-experiments/experiment-dashboard.png)

また、特定の実行にドリルダウンしてその出力やログを表示したり、送信した実験のスナップショットをダウンロードして他のユーザーと実験フォルダーを共有したりすることもできます。

### <a name="viewing-charts-in-run-details"></a>実行の詳細でのグラフの表示

実行中にさまざまな種類のメトリックを記録し、Azure Machine Learning Studio でグラフとして表示するために、ロギング APIを使用するさまざまな方法があります。

|ログに記録される値|コード例| ポータルでの表示|
|----|----|----|
|数値の配列をログに記録します| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|単一変数の折れ線グラフ|
|(for ループ内からのように) 繰り返し使用される、同じメトリック名を持つ単一数値をログに記録します| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| 単一変数の折れ線グラフ|
|2 つの数値列が繰り返し含まれる 1 行をログに記録します|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|変数が 2 つの折れ線グラフ|
|2 つの数値列を含むテーブルをログに記録します|`run.log_table(name='Sine Wave', value=sines)`|変数が 2 つの折れ線グラフ|


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
