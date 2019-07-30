---
title: 時系列予測モデルを自動トレーニングする
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service を使用して、自動化された機械学習で時系列予測回帰モデルをトレーニングする方法について説明します。
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 06/20/2019
ms.openlocfilehash: 34902aa23339b62920f918ae19b410a99e226a0e
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358799"
---
# <a name="auto-train-a-time-series-forecast-model"></a>時系列予測モデルを自動トレーニングする

この記事では、Azure Machine Learning service で自動化された機械学習を使用して、時系列予測回帰モデルをトレーニングする方法について説明します。 予測モデルの構成は、自動化された機械学習を使用した標準的な回帰モデルの設定に似ていますが、時系列データを操作するために特定の構成オプションと前処理の手順が存在します。 次の例では、以下の方法について説明します。

* 時系列モデリング用のデータを準備する
* [`AutoMLConfig`](/python/api/azureml-train-automl/azureml.train.automl.automlconfig) オブジェクトで特定の時系列パラメーターを構成する
* 時系列データで予測を実行する

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

自動化された ML を使用してテクニックとアプローチを組み合わせて、推奨される高品質な時系列予測を得ることができます。 自動化された時系列の実験は、多変量回帰問題として扱われます。 過去の時系列値は "ピボット" されて、他の予測因子とともにリグレッサーの追加ディメンションとなります。 

このアプローチには、従来の時系列手法と異なり、トレーニング中に複数のコンテキスト変数とその関係を自然に取り込めるという利点があります。 現実の予測アプリケーションでは、複数の要因が予測に影響を与える可能性があります。 たとえば、売上の予測では、履歴による傾向とのインタラクション、為替レート、および価格のすべてが、売上の結果に貢献します。 さらなるメリットは、回帰モデルのすべての最新の革新が予測にすぐに適用されることです。

将来の予測をどこまで行うか (予測期間) の他に、ラグなども[構成](#config)できます。 自動化された ML では、データセットと予測期間内のすべての項目について、単一ではあるがしばしば内部的に分岐するモデルが学習されます。 したがって、モデルのパラメーターを見積もるために多くのデータを使用でき、目に見えない系列の一般化が可能になります。 

トレーニング データから抽出されたフィーチャーが重要な役割を果たします。 さらに、自動化された ML では、標準的な前処理手順が実行され、追加の時系列フィーチャーが生成されて季節的影響がキャプチャされ、予測の精度が最大化されます。 

## <a name="prerequisites"></a>前提条件

* Azure Machine Learning ワークスペース。 ワークスペースを作成するには、「[Create an Azure Machine Learning service workspace](setup-create-workspace.md)」 (Azure Machine Learning サービス ワークスペースの作成) を参照してください。
* この記事では、自動化された機械学習実験の設定に基本的に精通していることを前提としています。 [チュートリアル](tutorial-auto-train-models.md)または[ハウツー](how-to-configure-auto-train.md)に従って、自動化された機械学習実験の基本的な設計パターンについて確認してください。

## <a name="preparing-data"></a>データの準備

自動化された機械学習内での予測回帰タスクの種類と回帰タスクの種類の最も重要な違いは、有効な時系列を表す機能がデータに含まれるという点です。 通常の時系列には、明確に定義され一貫した頻度があり、連続した期間のすべてのサンプル ポイントで値があります。 ファイル `sample.csv` の次のスナップショットを見てください。

    day_datetime,store,sales_quantity,week_of_year
    9/3/2018,A,2000,36
    9/3/2018,B,600,36
    9/4/2018,A,2300,36
    9/4/2018,B,550,36
    9/5/2018,A,2100,36
    9/5/2018,B,650,36
    9/6/2018,A,2400,36
    9/6/2018,B,700,36
    9/7/2018,A,2450,36
    9/7/2018,B,650,36

このデータ セットは、A と B の 2 つの異なる店舗を持つある企業の日々の売上データの単純な例です。さらに、モデルで週単位の季節性を検出できるようにする `week_of_year` の機能もあります。 フィールド `day_datetime` は、1 日 1 回の頻度でクリーンな時系列を表し、フィールド `sales_quantity` は、予測を実行するためのターゲット列になります。 Pandas データフレームにデータを読み取ってから `to_datetime` 関数を使用して、時系列が `datetime` 型であることを確認します。

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

この場合、データは既に時間フィールド `day_datetime` により昇順で並べ替えられています。 ただし、実験を設定するときに、有効な時系列を構築するために、目的の時間列が昇順で並べ替えられていることを確認してください。 データには 1,000 個のレコードが含まれていると想定し、データ内を結果が予測できるように分割してトレーニングおよびテスト用データ セットを作成してください。 この場合、ターゲット フィールド `sales_quantity` を分割して、予測トレーニングおよびテスト セットを作成します。

```python
X_train = data.iloc[:950]
X_test = data.iloc[-50:]

y_train = X_train.pop("sales_quantity").values
y_test = X_test.pop("sales_quantity").values
```

> [!NOTE]
> 将来の値を予測するためにモデルをトレーニングするときは、目的のトレーニングで使用されるすべての機能が、意図した期間の予測を実行するときに使用できることを確認してください。 たとえば、需要の予測を作成するときに、現在の株価の機能を含めれば、トレーニングの精度を大幅に高めることができます。 ただし、長期間にわたり予測する予定の場合、将来の時系列ポイントに応じた将来の株価を正確に予測できない可能性があり、モデルの精度が低下することがあります。

<a name="config"></a>
## <a name="configure-and-run-experiment"></a>実験を構成および実行する

予測タスクの場合は、自動化された機械学習は、時系列データに固有の前処理および推定手順を使用します。 次の前処理手順が実行されます。

* 時系列のサンプル頻度 (たとえば、1 時間ごと、毎日、毎週) を検出し、存在しない時間ポイントについて新しいレコードを作成して時系列を連続にします。
* ターゲット列 (前方フィルにより) および機能列 (メジアン列値を使用) の欠損値を補完します
* さまざまな系列で一定の効果を可能にするグレインベースの機能を作成します
* 季節のパターンの学習を支援する時間ベースの機能を作成します
* カテゴリ変数を数量にエンコードします

`AutoMLConfig` オブジェクトは、自動化された機械学習タスクに必要な設定とデータを定義します。 回帰の問題と同様に、タスクの種類、イテレーションの数、トレーニング データ、クロス検証の数など、標準的なトレーニング パラメーターを定義します。 予測タスクの場合は、実験に影響を与える追加パラメーターを設定する必要があります。 次の表に、各パラメーターとその使用法を示します。

| Param | 説明 | 必須 |
|-------|-------|-------|
|`time_column_name`|時系列の構築とその頻度の推定に使用される入力データで、datetime 列を指定するために使用されます。|✓|
|`grain_column_names`|入力データ内の個々の系列グループを定義する名前。 グレインが定義されていない場合、データ セットは 1 つの時系列であると見なされます。||
|`max_horizon`|時系列頻度を単位にした目的の最大予測期間を定義します。 単位は、月ごとや週ごとなどの予測を実行する必要があるトレーニング データの時間間隔に基づきます。|✓|
|`target_lags`|モデルのトレーニング前にターゲット値を前方に *n* 期間ずらします。||
|`target_rolling_window_size`|予測値の生成に使用する *n* 履歴期間 (トレーニング セットのサイズ以下)。 省略した場合、*n* はトレーニング セットの全体のサイズになります。||

ディクショナリ オブジェクトとして時系列設定を作成します。 `time_column_name` をデータ セットの `day_datetime` フィールドに設定します。 ストア A と B 用の **2 つの個別の時系列グループ**がデータに対して作成されるように `grain_column_names` パラメーターを定義します。最後に、テスト セット全体に対して予測を行うために `max_horizon` を 50 に設定します。 `target_rolling_window_size` で予測ウィンドウを 10 期間に設定し、`target_lags` パラメーターでターゲット値を前方に 2 期間ずらします。

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": 50,
    "target_lags": 2,
    "target_rolling_window_size": 10
}
```

次に標準的な `AutoMLConfig` オブジェクトを作成し、`forecasting` タスクの種類を指定して、実験を送信します。 モデルの終了後、最適な実行イテレーションを取得します。

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             iterations=10,
                             X=X_train,
                             y=y_train,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **time_series_settings)

ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```

> [!NOTE]
> クロス検証 (CV) 手順の場合、時系列データは、正規 K 分割クロス検証方法の基本的な統計的想定に違反する可能性があるため、自動化された機械学習が、元のローリング検証手順を実装して、時系列データに対しクロス検証分割を作成します。 この手順を使用するには、`AutoMLConfig` オブジェクトで `n_cross_validations` パラメーターを指定します。 検証を迂回して、`X_valid` および `y_valid` パラメーターを使った独自の検証セットを使用できます。

### <a name="view-feature-engineering-summary"></a>特徴エンジニアリングの概要を確認する

自動化された機械学習の時系列タスクの種類では、特徴エンジニアリング プロセスの詳細を確認できます。 次のコードは、個々の生の特徴と次の属性を表示します。

* 生の特徴名
* この生の特徴から形成されたエンジニアリング済み特徴の数
* 検出された種類
* 特徴がドロップされたかどうか
* 生の特徴に対する特徴変換の一覧

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>最適モデルによる予測

最適モデルのイテレーションを使用して、テスト データ セットの値を予測します。

```python
y_predict = fitted_model.predict(X_test)
y_actual = y_test.flatten()
```

または、`predict()` ではなく `forecast()` 関数を使用することもできます。これにより、いつ予測を開始するかを指定できます。 次の例では、最初に `y_pred` のすべての値を `NaN` に置き換えます。 `predict()` を使用すると通常そうなるように、このケースでは、予測の始まりはトレーニング データの最後になります。 ただし、`y_pred` の後半部分のみを `NaN` に置き換えた場合、この関数では前半の数値を変更しないまま、後半の `NaN` の値を予測します。 この関数は、予測値と調整された特徴の両方を返します。

`forecast()` 関数の `forecast_destination` パラメーターを使用して、指定した日付までの値を予測することもできます。

```python
y_query = y_test.copy().astype(np.float)
y_query.fill(np.nan)
y_fcst, X_trans = fitted_pipeline.forecast(
    X_test, y_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

`y_test` の実際の値と `y_pred` の予測値との RMSE (二乗平均平方根誤差) を計算します。

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

モデル全体の精度は判別しているので、最も現実的な次の手順は、モデルを使用して不明な将来の値を予測することです。 テスト セット `X_test` と同じ形式ですが将来の日時を使用してデータ セットを提供するだけで、結果の予測セットは時系列手順ごとに予測された値になります。 データ セット内の最後の時系列レコードは 2018 年 12 月 31 日のものだったとします。 次の日 (または予測する必要のある数の期間、< = `max_horizon`) の需要を予測するには、2019 年 1 月 1 日の店舗ごとに 1 つの時系列レコードを作成します。

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

必要な手順を繰り返して、この将来のデータをデータフレームに読み込んで、`best_run.predict(X_test)` を実行して将来の値を予測します。

> [!NOTE]
> `max_horizon` を超える数の期間について値を予測することはできません。 現在の期間を超えて将来の値を予測するには、さらに期間を長くしてモデルを再度トレーニングする必要があります。

## <a name="next-steps"></a>次の手順

* [チュートリアル](tutorial-auto-train-models.md)に従って、自動化された機械学習で実験を作成する方法について学習します。
* [Azure Machine Learning SDK for Python](https://aka.ms/aml-sdk) に関するリファレンス ドキュメントを確認します。
