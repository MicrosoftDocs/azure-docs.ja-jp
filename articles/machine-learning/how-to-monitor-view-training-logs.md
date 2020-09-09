---
title: ML 実行のログとメトリックを監視および表示する
titleSuffix: Azure Machine Learning
description: Azure ML の実験を監視し、実行のメトリックを表示することでモデルの作成プロセスを強化します。 ウィジェットと Studio ポータルを使用して、実行の状態を調べ、実行レコードを表示します。
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: d06186b2ce4d8bb9143663d41f03b9508e4bd00e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89005957"
---
# <a name="monitor-and-view-ml-run-logs-and-metrics"></a>ML 実行のログとメトリックを監視および表示する

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、Azure Machine Learning の実行を監視し、そのログを表示する方法について説明します。 ログを表示するには、まずそれを有効にする必要があります。 詳細については、「[Azure ML のトレーニングの実行でログ記録を有効にする](how-to-track-experiments.md)」を参照してください。

ログは、エラーや警告を診断したり、パラメーターやモデルの正確性などのパフォーマンス メトリックを追跡したりするのに役立ちます。 この記事では、次の方法を使用してログを表示する方法について説明します。

> [!div class="checklist"]
> * Studio で実行を監視する
> * Jupyter Notebook ウィジェットを使用して実行を監視する
> * 自動機械学習の実行を監視する
> * 完了時に出力ログを表示する
> * Studio で出力ログを表示する

実験の管理方法に関する一般的な情報については、「[トレーニングの実行の開始、監視、およびキャンセル](how-to-manage-runs.md)」を参照してください。

## <a name="monitor-runs-in-the-studio"></a>Studio で実行を監視する

ブラウザーから特定のコンピューティング先のための実行を監視するには、次の手順を使用します。

1. [Azure Machine Learning Studio](https://ml.azure.com/) で、ワークスペースを選択し、ページの左側から __[コンピューティング]__ を選択します。

1. __[Training Clusters] (トレーニング クラスター)__ を選択して、トレーニングに使用されるコンピューティング先の一覧を表示します。 次に、クラスターを選択します。

    ![トレーニング クラスターを選択する](./media/how-to-track-experiments/select-training-compute.png)

1. __[実行]__ を選択します。 このクラスターを使用する実行の一覧が表示されます。 特定の実行の詳細を表示するには、 __[実行]__ 列のリンクを使用します。 実験の詳細を表示するには、 __[実験]__ 列のリンクを使用します。

    ![トレーニング クラスターの実行を選択する](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > トレーニング コンピューティング先は共有リソースであるため、一度に複数の実行をキューに入れたりアクティブにしたりすることができます。
    > 
    > ある実行に子実行が含まれる場合があるため、1 つのトレーニング ジョブに複数のエントリが存在する可能性があります。

実行は、完了するとこのページには表示されなくなります。 完了した実行に関する情報を表示するには、スタジオの __[実験]__ セクションにアクセスし、実験と実行を選択します。 詳細については、[完了した実行のメトリックの表示](#view-the-experiment-in-the-web-portal)に関するセクションを参照してください。

## <a name="monitor-runs-using-the-jupyter-notebook-widget"></a>Jupyter Notebook ウィジェットを使用して実行を監視する

**ScriptRunConfig** メソッドを使用して実行を送信するときに、[Jupyter ウィジェット](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)を使用して実行の進行状況を確認できます。 実行の送信と同様に、このウィジェットも非同期です。また、ジョブが完了するまで 10 秒から 15 秒ごとにライブ更新を提供します。

実行が完了するのを待っている間、Jupyter ウィジェットを表示します。
    
```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

![Jupyter Notebook ウィジェットのスクリーンショット](./media/how-to-track-experiments/run-details-widget.png)

ワークスペース内の同じ表示へのリンクを取得することもできます。

```python
print(run.get_portal_url())
```

## <a name="monitor-automated-machine-learning-runs"></a>自動機械学習の実行を監視する

自動機械学習の実行の場合、前回の実行のグラフにアクセスするには、`<<experiment_name>>` を適切な実験名に置き換えます。

```python
from azureml.widgets import RunDetails
from azureml.core.run import Run

experiment = Experiment (workspace, <<experiment_name>>)
run_id = 'autoML_my_runID' #replace with run_ID
run = Run(experiment, run_id)
RunDetails(run).show()
```

![自動機械学習の場合の Jupyter Notebook ウィジェット](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)

## <a name="show-output-upon-completion"></a>完了時に出力を表示する

**ScriptRunConfig** を使用するときは、```run.wait_for_completion(show_output = True)``` を使用してモデルのトレーニングの完了を表示できます。 ```show_output``` フラグを指定すると、詳細な出力が提供されます。 詳細については、[ログ記録を有効にする方法](how-to-track-experiments.md#scriptrunconfig-logs)の ScriptRunConfig セクションを参照してください。

<a id="queryrunmetrics"></a>
## <a name="query-run-metrics"></a>実行のメトリックのクエリを行う

```run.get_metrics()``` を使用して、トレーニング済みモデルのメトリックを表示できます。 たとえば、上記の例と共にこれを使用すると、平均二乗誤差 (mse) の値が最小のモデルを探して最適なモデルを決定できます。

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-run-records-in-the-studio"></a>Studio で実行レコードを表示する

ログに記録されたメトリックを含む、完了した実行レコードを参照するには、[Azure Machine Learning Studio](https://ml.azure.com) を使用します。

**[実験]** タブに移動し、実験を選択します。 実験実行ダッシュボードでは、実行ごとに追跡されたメトリックとログを確認できます。 

特定の実行にドリルダウンしてその出力やログを表示したり、実験のスナップショットをダウンロードして他のユーザーと実験フォルダーを共有したりできます。

また、実行一覧テーブルを編集して、複数の実行を選択し、特定の実行に関してログに記録された直近の値や、最小値、最大値を表示することができます。 グラフをカスタマイズして、ログに記録されたメトリック値と集計を複数の実行にわたって比較します。

![Azure Machine Learning Studio での実行の詳細](media/how-to-track-experiments/experimentation-tab.gif)

### <a name="format-charts-in-the-studio"></a>Studio でのグラフの書式設定

ログ API で次のメソッドを使用して、Studio によるメトリックの視覚化を変更します。

|ログに記録される値|コード例| ポータルでの形式|
|----|----|----|
|数値の配列をログに記録します| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|単一変数の折れ線グラフ|
|(for ループ内からのように) 繰り返し使用される、同じメトリック名を持つ単一数値をログに記録します| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| 単一変数の折れ線グラフ|
|2 つの数値列が繰り返し含まれる 1 行をログに記録します|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|変数が 2 つの折れ線グラフ|
|2 つの数値列を含むテーブルをログに記録します|`run.log_table(name='Sine Wave', value=sines)`|変数が 2 つの折れ線グラフ|


## <a name="next-steps"></a>次のステップ

次の手順を試して、Azure Machine Learning を使用する方法を学習してください。

* [Azure Machine Learning デザイナー (プレビュー) で実験を追跡してログを有効にする](how-to-track-designer-experiments.md)方法を学習します。

* 最適なモデルを登録し、チュートリアルでデプロイする方法の例については、「[Azure Machine Learning で画像分類モデルをトレーニングする](tutorial-train-models-with-aml.md)」をご覧ください。

