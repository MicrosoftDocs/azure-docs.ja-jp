---
title: データセットでデータ ドリフトを検出する (プレビュー)
titleSuffix: Azure Machine Learning
description: Azure Learning でデータ ドリフト検出を設定する方法について説明します。 データセット監視 (プレビュー) を作成し、データ ドリフトを監視し、アラートを設定します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: copeters
author: lostmygithubaccount
ms.date: 06/25/2020
ms.topic: conceptual
ms.custom: how-to, data4ml, contperf-fy21q2
ms.openlocfilehash: 5a3d16445c5a4276f07f4ed502b9830a10c4ff72
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102518910"
---
# <a name="detect-data-drift-preview-on-datasets"></a>データセットでデータ ドリフトを検出する (プレビュー)

データ ドリフトを監視し、ドリフトが大きい場合のアラートを設定する方法について説明します。  

Azure Machine Learning データセット モニター (プレビュー) を使用すると、次のことを実行できます。
* **データのドリフトを分析** して、時間の経過と共にどのように変化するかを把握する。
* **モデル データを監視** して、トレーニング用データセットと供給データセットの違いを確認する。  [デプロイされたモデルからモデル データを収集すること](how-to-enable-data-collection.md)から始めます。
* **新しいデータを監視** して、ベースライン データセットとターゲット データセットの違いを確認する。
* **データの特徴をプロファイリング** して、時間の経過と共に統計的な特性がどのように変化するかを追跡する。
* **データ ドリフトに関するアラートを設定** して、潜在的な問題を早期に警告する。 
* 非常に多くのドリフトがデータに発生したと判断した場合に、**新しいバージョンのデータセットを作成する (how-to-version-track-datasets)** 。

モニターの作成には、[Azure Machine Learning のデータセット](how-to-create-register-datasets.md)が使用されます。 データセットには timestamp 列が含まれている必要があります。

データ ドリフト メトリックは、Python SDK または Azure Machine Learning Studio を使用して確認できます。  その他のメトリックと分析情報は、Azure Machine Learning ワークスペースに関連付けられている [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) リソースを通じて利用できます。

> [!IMPORTANT]
> データセットのデータ ドリフト検出は、現在パブリック プレビュー段階にあります。
> プレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

データセット モニターを作成して使用するには、以下が必要です。
* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。
* [Azure Machine Learning ワークスペース](how-to-manage-workspace.md)。
* [Azure Machine Learning SDK for Python がインストール済み](/python/api/overview/azure/ml/install) (これには azureml-datasets パッケージが含まれています)。
* データのファイル パス、ファイル名、または列にタイムスタンプが指定された構造化 (表形式) データ。

## <a name="what-is-data-drift"></a>データの誤差とは

データ ドリフトは、モデルの精度が時間の経過と共に低下する主な理由の 1 つです。  機械学習モデルの場合、データ ドリフトとは、モデルのパフォーマンスの低下につながるモデルの入力データの変更のことです。  データ ドリフトを監視すると、このようなモデルのパフォーマンスの問題を検出するために役立ちます。

データ ドリフトは、次のようなことが原因で発生します。

- 上流プロセスの変更 (センサーを交換したため測定単位がインチからセンチメートルに変更された場合など)。 
- データ品質の問題 (センサーが破損しているため読み取り値が常に 0 になっている場合など)。
- データの自然なドリフト (平均気温が季節と共に変化する場合など)。
- 特徴間の関係の変化 (共変量シフト)。 

Azure Machine Learning を使用すると、比較されるデータセットの複雑さを抽象化する単一のメトリックを計算することにより、ドリフト検出を簡略化することができます。  このようなデータセットには、数百単位の特徴と数万行が含まれている場合があります。 ドリフトが検出されたら、ドリフトを引き起こしている特徴までドリルダウンします。  次に、特徴レベルのメトリックを調べて、ドリフトの根本原因をデバッグし、特定します。

このトップ ダウン アプローチを使用すると、従来のルールベースの手法よりもデータの監視が簡単になります。 許可されたデータ範囲や許可された一意の値などのルールベースの手法では、時間がかかり、エラーが発生しやすくなります。

Azure Machine Learning では、データセット モニターを使用し、データ ドリフトを検出して警告します。
  
### <a name="dataset-monitors"></a>データセット モニター 

データセット モニターを使用すると、次のことができます。

* データセット内の新しいデータに対してデータ ドリフトを検出して警告する。
* 履歴データのドラフトを分析する。
* 時系列で新しいデータをプロファイルする。

データ ドリフトに関するアルゴリズムは、データの変化を総合的に測定するだけでなく、どの特徴に詳細な調査が必要であるかがわかるようになっています。 データセット モニターでは、`timeseries` データセット内の新しいデータをプロファイリングすることによって、ほかにも多数のメトリックが生成されます。 

[Azure Application Insights](../azure-monitor/app/app-insights-overview.md) を使用することで、モニターによって生成されるすべてのメトリックについてカスタム アラートを設定できます。 データセット モニターを使用すると、データの問題を迅速に検出し、考えられる原因を特定することによって問題のデバッグ時間を短縮できます。  

概念的には、Azure Machine Learning でデータセット モニターを設定するシナリオは主に 3 つあります。

シナリオ | 説明
---|---
トレーニング データのドラフトに対してモデルのサービス データを監視する | 供給データがトレーニング データからドリフトすると、モデルの精度が低下するので、このシナリオの結果は、代用品を監視してモデルの精度を調べたものと解釈できます。
時系列データセットを監視して、以前の期間からのドリフトを調べる。 | このシナリオはより一般的なものであり、モデル構築の上流または下流に関係するデータセットを監視するために使用できます。  対象のデータセットには timestamp 列が必要です。 ベースライン データセットは、ターゲットのデータセットと共通の特徴を持つ表形式のデータセットです。
過去のデータに対して分析を実行する。 | このシナリオは、履歴データを解釈し、データセット モニターを設定する際の意思決定に反映させるために使用できます。

データセット モニターは、次の Azure サービスによって変わります。

|Azure サービス  |説明  |
|---------|---------|
| *データセット* | ドリフトでは、Machine Learning データセットを使用してトレーニング データが取得され、モデルのトレーニングのためにデータが比較されます。  データのプロファイル生成を使用して、最小値、最大値、個別値、個別値カウントなどのレポートされたメトリックの一部を生成します。 |
| *Azureml のパイプラインとコンピューティング* | ドリフト計算ジョブは azureml パイプラインでホストされます。  このジョブは、オンデマンドまたはスケジュールによってトリガーされ、ドリフト モニターの作成時に構成されたコンピューティングで実行されます。
| *Application Insights*| ドリフトによって、機械学習ワークスペースに属する Application Insights にメトリックが送信されます。
| *Azure Blob Storage*| ドリフトによって、メトリックが JSON 形式で Azure BLOB Storage に送信されます。

### <a name="baseline-and-target-datasets"></a>ベースライン データセットとターゲット データセット 

[Azure Machine Learning データセット](how-to-create-register-datasets.md)でデータ ドリフトを監視します。 データセット モニターを作成するときには、次の情報を参照します。
* ベースライン データセット - 通常はモデルのトレーニング データセット。
* ターゲット データセット - 通常はモデルの入力データ - 時間の経過に沿ってベースライン データセットと比較されます。 この比較は、ターゲット データセットにはタイムスタンプ列が指定されている必要があるということを意味します。

モニターは、ベースライン データセットとターゲット データセットを比較します。

## <a name="create-target-dataset"></a>ターゲット データセットを作成する

ターゲット データセットには、データ内の列またはファイルのパス パターンから派生した仮想列のいずれかにタイムスタンプ列を指定することにより、`timeseries` 特性が設定されている必要があります。 [Python SDK](#sdk-dataset) または [Azure Machine Learning Studio](#studio-dataset) を使用して、タイムスタンプを持つデータセットを作成します。 `timeseries` 特性をデータセットに追加するには、"タイムスタンプ" を表す列を指定する必要があります。 データが "{yyyy/MM/dd}" などの時刻情報を含むフォルダー構造に分割されている場合は、パス パターン設定を使用して仮想列を作成して、"パーティションのタイムスタンプ" として設定し、時系列機能の重要度を向上させます。

# <a name="python"></a>[Python](#tab/python)
<a name="sdk-dataset"></a>

[`Dataset`](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) クラスの [`with_timestamp_columns()`](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) メソッドによって、データセットのタイムスタンプ列が定義されます。

```python 
from azureml.core import Workspace, Dataset, Datastore

# get workspace object
ws = Workspace.from_config()

# get datastore object 
dstore = Datastore.get(ws, 'your datastore name')

# specify datastore paths
dstore_paths = [(dstore, 'weather/*/*/*/*/data.parquet')]

# specify partition format
partition_format = 'weather/{state}/{date:yyyy/MM/dd}/data.parquet'

# create the Tabular dataset with 'state' and 'date' as virtual columns 
dset = Dataset.Tabular.from_parquet_files(path=dstore_paths, partition_format=partition_format)

# assign the timestamp attribute to a real or virtual column in the dataset
dset = dset.with_timestamp_columns('date')

# register the dataset as the target dataset
dset = dset.register(ws, 'target')
```

> [!TIP]
> データセットの `timeseries` 特性を使用する完全な例については、[ノートブックの例](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb)または [Datasets SDK のドキュメント](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-)を参照してください。

# <a name="studio"></a>[スタジオ](#tab/azure-studio)

<a name="studio-dataset"></a>

Azure Machine Learning Studio を使用してデータセットを作成する場合は、データへのパスにタイムスタンプ情報が含まれていることを確認し、データが存在するすべてのサブフォルダーを含め、パーティション形式を設定します。

次の例では、サブフォルダー *NoaaIsdFlorida/2019* のすべてのデータが取得され、パーティション形式でタイムスタンプの年、月、日が指定されています。

[![パーティション形式](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

**スキーマ** 設定では、指定されたデータセットの仮想または実際の列からタイムスタンプ列を指定します。

:::image type="content" source="media/how-to-monitor-datasets/timestamp.png" alt-text="タイムスタンプを設定する":::

この場合のように、データが日付でパーティション分割されている場合は、partition_timestamp も指定できます。  こうすることで、より効率的に日付を処理できます。

:::image type="content" source="media/how-to-monitor-datasets/timeseries-partitiontimestamp.png" alt-text="パーティションのタイムスタンプ":::

---

## <a name="create-dataset-monitor"></a>データセット モニターを作成する

新しいデータセットのデータ ドリフトを検出して警告するデータセット モニターを作成します。  [Python SDK](#sdk-monitor) または [Azure Machine Learning Studio](#studio-monitor) のいずれかを使用します。

# <a name="python"></a>[Python](#tab/python)
<a name="sdk-monitor"></a> 詳細については、[データ ドリフトに関する Python SDK リファレンス ドキュメント](/python/api/azureml-datadrift/azureml.datadrift)を参照してください。 

次の例では、Python SDK を使用してデータセット モニターを作成する方法を示します。

```python
from azureml.core import Workspace, Dataset
from azureml.datadrift import DataDriftDetector
from datetime import datetime

# get the workspace object
ws = Workspace.from_config()

# get the target dataset
dset = Dataset.get_by_name(ws, 'target')

# set the baseline dataset
baseline = target.time_before(datetime(2019, 2, 1))

# set up feature list
features = ['latitude', 'longitude', 'elevation', 'windAngle', 'windSpeed', 'temperature', 'snowDepth', 'stationName', 'countryOrRegion']

# set up data drift detector
monitor = DataDriftDetector.create_from_datasets(ws, 'drift-monitor', baseline, target, 
                                                      compute_target='cpu-cluster', 
                                                      frequency='Week', 
                                                      feature_list=None, 
                                                      drift_threshold=.6, 
                                                      latency=24)

# get data drift detector by name
monitor = DataDriftDetector.get_by_name(ws, 'drift-monitor')

# update data drift detector
monitor = monitor.update(feature_list=features)

# run a backfill for January through May
backfill1 = monitor.backfill(datetime(2019, 1, 1), datetime(2019, 5, 1))

# run a backfill for May through today
backfill1 = monitor.backfill(datetime(2019, 5, 1), datetime.today())

# disable the pipeline schedule for the data drift detector
monitor = monitor.disable_schedule()

# enable the pipeline schedule for the data drift detector
monitor = monitor.enable_schedule()
```

> [!TIP]
> `timeseries` データセットとデータ ドリフト検出機能を設定する完全な例については、[サンプルのノートブック](https://aka.ms/datadrift-notebook)を参照してください。


# <a name="studio"></a>[スタジオ](#tab/azure-studio)
<a name="studio-monitor"></a>

1. [スタジオのホームページ](https://ml.azure.com)に移動します。
1. 左側の **[データセット]** タブを選択します。 
1. **データセット モニター** を選択します。
   ![モニターの一覧](./media/how-to-monitor-datasets/monitor-list.png)

1. **[+ モニターの作成]** をクリックし、 **[次へ]** をクリックしてウィザードの手順を続行します。  

:::image type="content" source="media/how-to-monitor-datasets/wizard.png" alt-text="[モニターの作成] ウィザード":::

* **ターゲット データセットを選択します**。  ターゲット データセットは、データ ドリフトの分析対象となる、タイムライン列が指定された表形式のデータセットです。 ターゲット データセットは、ベースライン データセットと共通の特徴を備え、かつ、新しいデータが追加される `timeseries` データセットである必要があります。 ターゲット データセット内の履歴データを分析することも、新しいデータを監視することもできます。

* **ベースライン データセットを選択します。**  時間の経過に沿ってターゲット データセットと比較するベースラインとして使用される表形式のデータセットを選択します。  ベースライン データセットには、ターゲット データセットと共通の特徴が含まれている必要があります。  ターゲット データセットのスライスを使用する時間範囲を選択するか、ベースラインとして使用する別のデータセットを指定します。

* **モニターの設定**。  以下は、スケジュールされたデータセット モニター パイプラインを作成する場合の設定です。 

    | 設定 | 説明 | ヒント | 変更可能 | 
    | ------- | ----------- | ---- | ------- |
    | 名前 | データセット モニターの名前。 | | いいえ |
    | 特徴 | 時間の経過に沿ってデータ ドリフトが分析される特徴の一覧。 | 概念ドリフトが測定されるように、モデルで出力される特徴に設定します。 時間の経過と共に自然にドリフトする特徴 (月、年、インデックスなど) は含めないでください。 特徴の一覧を調整したら、既存のデータ ドリフト モニターをバックフィルできます。 | はい | 
    | コンピューティング ターゲット | データセット モニター ジョブを実行する Azure Machine Learning コンピューティング ターゲット。 | | はい | 
    | 有効化 | データセット モニター パイプラインのスケジュールを有効または無効にします | バックフィル設定を使用して履歴データを分析する場合は、スケジュールを無効にします。 これは、データセット モニターの作成後に有効にできます。 | はい | 
    | 頻度 | バックフィルを実行する場合にパイプライン ジョブをスケジュールし、履歴データを分析するために使用される頻度。 オプションには、毎日、毎週、毎月があります。 | 実行ごとに、頻度に従ってターゲット データセットのデータが比較されます。 <li>毎日:ターゲット データセットの直近の丸 1 日をベースラインと比較します <li>毎週:ターゲット データセットの直近の 1 週間 (月曜日から日曜日) をベースラインと比較します <li>毎月:ターゲット データセットの直近の 1 か月間をベースラインと比較します | いいえ | 
    | Latency | データがデータセットに到達するのにかかる時間 (時間単位)。 たとえば、データが、データセットをカプセル化している SQL DB に到達するのに 3 日かかる場合は、待ち時間を 72 に設定します。 | データセット モニターの作成後に変更することはできません | いいえ | 
    | メール アドレス | データ ドリフトのしきい値違反 (パーセンテージ単位) に基づいてアラートを送信するメール アドレス。 | メールは Azure Monitor 経由で送信されます。 | はい | 
    | Threshold | メール アラートが送信される、データ ドリフトのしきい値 (パーセンテージ単位)。 | さらに、Application Insights リソースに関連付けられているワークスペース内の他のさまざまなメトリックについても、アラートやイベントを設定できます。 | はい |

ウィザードを完了すると、結果のデータセット モニターが一覧に表示されます。 それを選択して、このモニターの詳細ページに移動します。

---

## <a name="understand-data-drift-results"></a>データ ドリフトの結果の概要

このセクションでは、Azure Studio の **[データセット]**  /  **[データセット モニター]** ページに表示されるデータセットの監視結果について説明します。  このページでは、設定を更新するだけでなく、特定の期間の既存のデータを分析することができます。  

データ ドリフトの規模に関する最上位レベルの分析情報と、さらに調査すべき特徴の見所から始めます。

:::image type="content" source="media/how-to-monitor-datasets/drift-overview.png" alt-text="ドリフトの概要":::


| メトリック | 説明 | 
| ------ | ----------- | 
| データ ドリフトの大きさ | 時間の経過と共に生じるベースラインとターゲットのデータセット間のドリフトのパーセンテージ。 0 から 100 までの範囲で示され、0 はデータセットが同一であることを表し、100 は Azure Machine Learning データ ドリフト モデルによって 2 つのデータセットが完全に識別可能であることを表します。 この大きさを生成するために使用されている機械学習の手法が原因で、測定されたパーセンテージの数値にはノイズの混入が想定されます。 | 
| ドリフトが発生している主な特徴 | データセット内の機能のうち最も多くドリフトが発生し、ドリフトの規模指標に最も関与しているものを示します。 共変量シフトがあるため、基盤となる特徴の分布が特徴として比較的高い重要度になるように変更する必要は必ずしもありません。 |
| Threshold | データ ドリフトの規模が設定されたしきい値を超えると、アラートがトリガーされます。 これは、モニターの設定で構成できます。 | 

### <a name="drift-magnitude-trend"></a>ドリフトの規模の傾向

指定した期間内のデータセットとターゲット データセットの違いを確認します。  100% に近いほど、2 つのデータセットの違いは大きくなります。

:::image type="content" source="media/how-to-monitor-datasets/drift-magnitude.png" alt-text="ドリフトの規模の傾向":::

### <a name="drift-magnitude-by-features"></a>特徴ごとのドリフトの規模

このセクションには、選択した特徴の分布の経時的変化に対する特徴レベルの分析情報と、その他の統計情報が含まれています。

ターゲット データセットも時間の経過に沿ってプロファイリングされます。 各特徴のベースライン分布間の統計的な距離は、ターゲット データセットの時間の経過と比較されます。  概念的には、これはデータ ドリフトの規模と似ています。  ただし、この統計的な距離は、すべての特徴ではなく、個々の特徴に対するものです。 最小値、最大値、平均値も取得できます。

Azure Machine Learning Studio で、グラフ内のバーをクリックすると、その日付の特徴レベルの詳細が表示されます。 既定では、同じ特徴について、ベースライン データセットの分布と最近の実行時の分布が表示されます。

:::image type="content" source="media/how-to-monitor-datasets/drift-by-feature.gif" alt-text="特徴ごとのドリフトの規模":::

これらのメトリックは、Python SDK で、`DataDriftDetector` オブジェクトに `get_metrics()` メソッドを実行して取得することもできます。

### <a name="feature-details"></a>特徴の詳細

最後に、下にスクロールして、個々の特徴の詳細を確認します。  グラフの上にあるプルダウンを使用して特徴を選択し、詳細を確認するメトリックを選択します。

:::image type="content" source="media/how-to-monitor-datasets/numeric-feature.gif" alt-text="数値の特徴グラフと比較":::

グラフのメトリックは、特徴の種類によって異なります。

* 数値の特徴

    | メトリック | 説明 |  
    | ------ | ----------- |  
    | ワッサースタイン距離 | ベースライン分布をターゲット分布に変換するための最小限の作業量。 |
    | 平均値 | 特徴量の平均値。 |
    | 最小値 | 特徴量の最小値。 |
    | 最大値 | 特徴量の最大値。 |

* カテゴリ別の特徴
    
    | メトリック | 説明 |  
    | ------ | ----------- |  
    | ユークリッド距離     |  カテゴリ列に対して計算されます。  ユークリッド距離は 2 つのベクトルに対して計算され、2 つのデータセットからの同じカテゴリ列の経験的分布から生成されます。  0 は、経験的分布に差がないことを示します。    0 から外れるほど、この列のドリフトは大きくなります。  傾向は、このメトリックの時系列プロットから観察でき、ドリフトが生じている特徴を明らかにするために役立ちます。  |
    | 一意の値の数 | 特徴の一意の値 (カーディナリティ) の数。 |

このグラフで、1 つの日付を選択して、表示された特徴のターゲットとこの日付の間の特徴の分布を比較します。 数値の特徴の場合、これは 2 つの確率分布を示します。  特徴が数値の場合、横棒グラフが表示されます。

:::image type="content" source="media/how-to-monitor-datasets/select-date-to-compare.gif" alt-text="ターゲットと比較する日付を選択する":::

## <a name="metrics-alerts-and-events"></a>メトリック、アラート、イベント

メトリックは、Machine Learning ワークスペースに関連付けられている [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) リソースで照会できます。 カスタム アラート ルールの設定や、アクション (メール、SMS、プッシュ、音声、Azure 関数など) をトリガーするためのアクション グループの設定など、Application Insights のすべての機能にアクセスすることができます。 詳細については、Application Insights の包括的ドキュメントを参照してください。 

最初に、[Azure portal](https://portal.azure.com) に移動し、ワークスペースの **[概要]** ページを選択します。  関連付けられている Application Insights リソースが右端に表示されます。

[![Azure portal の概要](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

左側のペインの [監査] から [ログ (Analytics)] を選択します。

![Application Insights の概要](./media/how-to-monitor-datasets/ai-overview.png)

データセット モニターのメトリックは、`customMetrics` として格納されます。 データセット モニターの設定後、クエリを記述して実行すれば、それらを表示できます。

[![Log Analytics のクエリ](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

アラート ルールを設定するためのメトリックを確認したら、新しいアラート ルールを作成できます。

![新しいアラート ルール](./media/how-to-monitor-datasets/alert-rule.png)

既存のアクション グループを使用するか、または、新しいアクション グループを作成して、設定した条件が満たされたときに実行されるアクションを定義することができます。

![新しいアクション グループ](./media/how-to-monitor-datasets/action-group.png)


## <a name="troubleshooting"></a>トラブルシューティング

データ ドリフト モニターに関する制限事項と既知の問題

* 履歴データ分析時の時間範囲は、モニターの頻度設定の 31 間隔までに制限されます。 
* 特徴一覧が指定されていない (すべての特徴を使用する) 場合、200 の特徴に制限されます。
* コンピューティング サイズは、データを処理できる十分な大きさにする必要があります。
* 特定のモニターの実行について、開始日と終了日の範囲に該当するデータがデータセットに存在することを確認します。
* データセット モニターは、50 行以上を含むデータセットでのみ機能します。
* データセット内の列、つまり特徴は、次の表の条件に基づいてカテゴリまたは数値として分類されます。 特徴がこれらの条件を満たしていない場合 (たとえば、string 型の列に一意の値が 100 個以上含まれる場合)、その特徴はデータ ドリフト アルゴリズムから削除されますが、プロファイリングは引き続き行われます。 

    | 特徴の種類 | データ型 | 条件 | 制限事項 | 
    | ------------ | --------- | --------- | ----------- |
    | Categorical | string、bool、int、float | 特徴内の一意の値の数は、100 個未満であり、かつ行数の 5% 未満であること。 | null 値は独自のカテゴリとして扱われます。 | 
    | 数値 | int、float | 特徴内の値は数値データ型で、カテゴリの特徴の条件を満たしていません。 | 値の数の 15% を超える null が含まれる場合、その特徴は削除されます。 | 

* データ ドリフト モニターを作成したが、Azure Machine Learning Studio の **[データセット モニター]** ページにデータが表示されない場合は、次を試してください。

    1. ページの一番上で正しい日付範囲が選択されているかどうかを確認します。  
    1. **[データセット モニター]** タブで、実験リンクを選択し、実行状態を確認します。  このリンクはテーブルの右端にあります。
    1. 実行が正常に完了したら、生成されているメトリックの数や警告メッセージがあるかどうかをドライバー ログで確認します。  実験をクリックしたら、 **[出力 + ログ]** タブでドライバー ログを見つけます。

* SDK の `backfill()` 関数で予期された出力が生成されない場合は、認証の問題が原因である可能性があります。  この関数に渡す計算を作成するときに、`Run.get_context().experiment.workspace.compute_targets` を使用しないでください。  代わりに、次のような [ServicePrincipalAuthentication](/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication) を使用して、その `backfill()` 関数に渡す計算を作成します。 

  ```python
   auth = ServicePrincipalAuthentication(
          tenant_id=tenant_id,
          service_principal_id=app_id,
          service_principal_password=client_secret
          )
   ws = Workspace.get("xxx", auth=auth, subscription_id="xxx", resource_group="xxx")
   compute = ws.compute_targets.get("xxx")
   ```

* モデル データ コレクターからは、BLOB ストレージ アカウントにデータが到着するまでに最大で 10 分 (通常は 10 分未満) かかることがあります。 スクリプトまたは Notebook で、次のセルが実行されるように、10 分間待機します。

    ```python
    import time
    time.sleep(600)
    ```

## <a name="next-steps"></a>次のステップ

* [Azure Machine Learning Studio](https://ml.azure.com) または [Python ノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb)に移動して、データセット モニターを設定する。
* [Azure Kubernetes Service にデプロイされたモデル](./how-to-enable-data-collection.md)でデータ ドリフトを設定する方法を確認する。
* [Event Grid](how-to-use-event-grid.md) を使用してデータセット ドリフト モニターを設定する。