---
title: ML 実行のログとメトリックを監視および表示する
titleSuffix: Azure Machine Learning
description: ML の実験を監視し、Jupyter ウィジェットと Azure Machine Learning スタジオを使用して実行のメトリックを表示します。
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 47531da9c1e508281a57074df7aa10ffffe78810
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102518740"
---
# <a name="monitor-and-view-ml-run-logs-and-metrics"></a>ML 実行のログとメトリックを監視および表示する

Azure Machine Learning の実行を監視し、そのログを表示する方法について説明します。 

実験を実行すると、ログとメトリックがストリーミングされます。  また、独自のものを追加することもできます。  方法については、「[Azure ML のトレーニングの実行でログ記録を有効にする](how-to-track-experiments.md)」をご覧ください。

ログは、実行のエラーと警告の診断に役立ちます。 パラメーターやモデルの精度などのパフォーマンス メトリックは、実行の追跡と監視に役立ちます。

この記事では、次の方法を使用してログを表示する方法について説明します。

> [!div class="checklist"]
> * Studio で実行を監視する
> * Jupyter Notebook ウィジェットを使用して実行を監視する
> * 自動機械学習の実行を監視する
> * 完了時に出力ログを表示する
> * Studio で出力ログを表示する

実験の管理方法に関する一般的な情報については、「[トレーニングの実行の開始、監視、およびキャンセル](how-to-manage-runs.md)」を参照してください。

## <a name="monitor-runs-using-the-jupyter-notebook-widget"></a>Jupyter Notebook ウィジェットを使用して実行を監視する

**ScriptRunConfig** メソッドを使用して実行を送信するときに、[Jupyter ウィジェット](/python/api/azureml-widgets/azureml.widgets)を使用して実行の進行状況を確認できます。 実行の送信と同様に、このウィジェットも非同期です。また、ジョブが完了するまで 10 秒から 15 秒ごとにライブ更新を提供します。

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

**ScriptRunConfig** を使用するときは、```run.wait_for_completion(show_output = True)``` を使用してモデルのトレーニングの完了を表示できます。 ```show_output``` フラグを指定すると、詳細な出力が提供されます。 詳細については、[ログ記録を有効にする方法](how-to-track-experiments.md#scriptrun-logs)の ScriptRunConfig セクションを参照してください。

<a id="queryrunmetrics"></a>

## <a name="view-run-metrics"></a>実行メトリックを表示する

## <a name="via-the-sdk"></a>SDK を使用
```run.get_metrics()``` を使用して、トレーニング済みモデルのメトリックを表示できます。 次の例を見てください。 

```python
from azureml.core import Run
run = Run.get_context()
run.log('metric-name', metric_value)

metrics = run.get_metrics()
# metrics is of type Dict[str, List[float]] mapping mertic names
# to a list of the values for that metric in the given run.

metrics.get('metric-name')
# list of metrics in the order they were recorded
```

<a name="view-the-experiment-in-the-web-portal"></a>

## <a name="view-run-records-in-the-studio"></a>Studio で実行レコードを表示する

ログに記録されたメトリックを含む、完了した実行レコードを参照するには、[Azure Machine Learning Studio](https://ml.azure.com) を使用します。

**[実験]** タブに移動します。複数の実験にわたってワークスペース内のすべての実行を表示するには、 **[すべての実行]** タブを選択します。上部のメニュー バーに実験フィルターを適用することで、特定の実験の実行をドリルダウンできます。

個々の実験ビューで、 **[すべての実験の]** タブを選択します。実験実行ダッシュボードでは、実行ごとに追跡されたメトリックとログを確認できます。 

また、実行一覧テーブルを編集して、複数の実行を選択し、特定の実行に関してログに記録された直近の値や、最小値、最大値を表示することができます。 グラフをカスタマイズして、ログに記録されたメトリック値と集計を複数の実行にわたって比較します。 

![Azure Machine Learning Studio での実行の詳細](media/how-to-track-experiments/experimentation-tab.gif)

### <a name="view-log-files-for-a-run"></a>実行のログ ファイルを表示する 

ログ ファイルは、Azure ML ワークロードをデバッグするための必須リソースです。 特定の実行にドリルダウンして、そのログと出力を表示します。  

1. **[実験]** タブに移動します。
1. 特定の実行の runID を選択します。
1. ページの上部にある **[出力とログ]** を選択します。

:::image type="content" source="media/how-to-monitor-view-training-logs/view-logs.png" alt-text="実行の [出力とログ] セクションのスクリーンショット":::

次の表は、このセクションに表示されるフォルダー内のログ ファイルの内容を示しています。

> [!NOTE]
> 実行ごとにすべてのファイルが表示されるとは限りません。 たとえば、20_image_build_log*.txt は、新しいイメージが作成されたとき (環境を変更した場合など) にのみ表示されます。

#### <a name="azureml-logs-folder"></a>`azureml-logs` フォルダー

|ファイル  |説明  |
|---------|---------|
|20_image_build_log.txt     | トレーニング環境用 Docker イメージ ビルド ログ (省略可能、実行ごとに 1 つ)。 環境を更新する場合にのみ適用されます。 それ以外の場合、AML ではキャッシュされたイメージを再利用します。 成功した場合は、対応するイメージのイメージ レジストリの詳細が含められます。         |
|55_azureml-execution-<node_id>.txt     | ホスト ツールの stdout/stderr ログ (ノードごとに 1 つ)。 イメージをコンピューティング ターゲットにプルします。 このログは、コンピューティング リソースをセキュリティで保護した後にのみ表示されることに注意してください。         |
|65_job_prep-<node_id>.txt     |   ジョブ準備スクリプトの stdout/stderr ログ (ノードごとに 1 つ)。 コンピューティング ターゲットとデータストア (要求された場合) にコードをダウンロードします。       |
|70_driver_log(_x).txt      |  AML コントロール スクリプトとカスタマー トレーニング スクリプトからの stdout/stderr ログ (プロセスごとに 1 つ)。 **これは、スクリプトからの標準出力です。ここに、コードのログ (print ステートメントなど) が表示されます。** ほとんどの場合は、ここでログを監視します。       |
|70_mpi_log.txt     |   MPI フレームワーク ログ (省略可能、実行ごとに 1 つ)。 MPI 実行の場合のみ。   |
|75_job_post-<node_id>.txt     |  ジョブ リリース スクリプトの stdout/stderr ログ (ノードごとに 1 つ)。 ログを送信し、コンピューティング リソースを解放して Azure に戻します。        |
|process_info.json      |   どのプロセスがどのノードで実行されているかを示します。  |
|process_status.json      | プロセスが開始されていないか、実行中か、完了しているかというプロセスの状態を示します。         |

#### <a name="logs--azureml-folder"></a>`logs > azureml` フォルダー

|ファイル  |説明  |
|---------|---------|
|110_azureml.log      |         |
|job_prep_azureml.log     |   ジョブ準備のためのシステム ログ        |
|job_release_azureml.log     | ジョブ解放のためのシステム ログ        |

#### <a name="logs--azureml--sidecar--node_id-folder"></a>`logs > azureml > sidecar > node_id` フォルダー

サイドカーが有効になっている場合、ジョブ準備とジョブ解放のスクリプトが、サイドカー コンテナー内で実行されます。  ノードごとに 1 つのフォルダーがあります。 

|ファイル  |説明  |
|---------|---------|
|start_cms.txt     |  サイドカー コンテナーの開始時に開始されるプロセスのログ       |
|prep_cmd.txt      |   `job_prep.py` の実行時に入力された ContextManagers のログ (この一部が `azureml-logs/65-job_prep` にストリーミングされます)       |
|release_cmd.txt     |  `job_release.py` の実行時に終了した ComtextManagers のログ        |

#### <a name="other-folders"></a>その他のフォルダー

マルチコンピューティング クラスターでのジョブのトレーニングでは、ノード IP ごとにログが存在します。 各ノードの構造は、単一ノードのジョブと同じです。 execution ログ、stderr ログ、stdout ログ全般に対して 1 つのログ フォルダーが追加されています。

Azure Machine Learning では、AutoML やトレーニング ジョブを実行する Docker コンテナーなど、トレーニング中にさまざまなソースからの情報がログに記録すされます。 これらのログの多くについては、ドキュメントに記載されていません。 問題が発生し、Microsoft サポートに問い合わせた場合、サポートはトラブルシューティングの際にこれらのログを使用できる可能性があります。

## <a name="monitor-a-compute-cluster"></a>コンピューティング クラスターを監視する

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


## <a name="next-steps"></a>次のステップ

次の手順を試して、Azure Machine Learning を使用する方法を学習してください。

* [Azure Machine Learning デザイナーで実験を追跡してログを有効にする](how-to-track-designer-experiments.md)方法を学習します。

* 最適なモデルを登録し、チュートリアルでデプロイする方法の例については、「[Azure Machine Learning で画像分類モデルをトレーニングする](tutorial-train-models-with-aml.md)」をご覧ください。
