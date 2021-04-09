---
title: 自動機械学習による特徴量化
titleSuffix: Azure Machine Learning
description: Azure Machine Learning でのデータの特徴量化設定と、自動 ML の実験に合わせてこれらの特徴をカスタマイズする方法について説明します。
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to,automl,contperf-fy21q2
ms.date: 12/18/2020
ms.openlocfilehash: c90ef9fe49a87c18c7f4f55175bafaebfd31d722
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98610303"
---
# <a name="data-featurization-in-automated-machine-learning"></a>自動機械学習でのデータの特徴量化

Azure Machine Learning でのデータの特徴量化設定と、[自動機械学習の実験](concept-automated-ml.md)に合わせてこれらの特徴をカスタマイズする方法について説明します。

## <a name="feature-engineering-and-featurization"></a>特徴エンジニアリングと特徴量化

トレーニング データは行と列で構成されています。 各行は観測またはレコードで、各行の列は各レコードを表す特徴です。 通常は、予測モデルを作成するために選択されるのは、データ内のパターンを最もよく特徴付ける特徴です。

生のデータ フィールドの多くはモデルのトレーニングに直接使用できますが、多くの場合、データ内のパターンをより適切に識別する情報を提供する追加の (エンジニアリングされた) 特徴を作成する必要があります。 このプロセスは **特徴エンジニアリング** と呼ばれます。データのドメイン知識を活用して特徴を作成し、次にそれが、機械学習アルゴリズムの学習向上に寄与します。 

Azure Machine Learning では、特徴エンジニアリングを容易にするために、データのスケーリングと正規化の手法が適用されます。 自動 ML の実験では、これらの手法と特徴エンジニアリングをまとめて "**特徴量化**" と呼んでいます。

## <a name="prerequisites"></a>前提条件

この記事は、自動 ML の実験を構成する方法についての知識が既にある読者を対象としています。 構成については、次の記事を参照してください。

- コード ファーストのエクスペリエンスについて: [Python 用 Azure Machine Learning SDK を使用して自動 ML の実験を構成する](how-to-configure-auto-train.md)。
- 少量のコードまたはコードなしのエクスペリエンスについて: [Azure Machine Learning Studio を使用して自動機械学習モデルを作成、確認、デプロイする](how-to-use-automated-ml-for-ml-models.md)。

## <a name="configure-featurization"></a>特徴量化を構成する

自動機械学習におけるあらゆる実験では、[自動スケーリングと正規化の手法](#featurization)が既定でデータに適用されます。 これらの手法は、さまざまなスケールの特徴に反応する "*特定*" のアルゴリズムを支援する特徴量化の一種です。 "*欠損値の補完*"、"*エンコード*"、"*変換*" などの特徴量化も追加で有効にできます。

> [!NOTE]
> 自動機械学習の特徴量化の手順 (機能の正規化、欠損データの処理、テキストから数値への変換など) は、基になるモデルの一部になります。 このモデルを予測に使用する場合、トレーニング中に適用されたのと同じ特徴量化の手順が入力データに自動的に適用されます。

Python SDK を使用して構成した実験では、特徴量化の設定を有効または無効にできるほか、実験に使用する特徴量化手順をさらに指定することもできます。 Azure Machine Learning Studio を使用している場合は、[特徴量化を有効にする手順](how-to-use-automated-ml-for-ml-models.md#customize-featurization)に関する記事を参照してください。

次の表は、[AutoMLConfig クラス](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)で `featurization` に使用できる設定を示したものです。

|特徴量化の構成 | 説明|
------------- | ------------- |
|`"featurization": 'auto'`| 前処理の一環として、[データ ガードレール](#data-guardrails)と[特徴量化](#featurization)の手順が自動的に実行されることを示します。 これは、既定の設定です。|
|`"featurization": 'off'`| 特徴量化手順が自動的に行われないことを指定します。|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| カスタマイズした特徴量化手順を使用することを指定します。 [特徴付けをカスタマイズする方法の詳細](#customize-featurization)。|

<a name="featurization"></a>

## <a name="automatic-featurization"></a>自動特徴付け

以下の表は、データに対して自動的に適用される手法の一覧です。 これらの手法は、SDK またはスタジオを使用して構成された実験に適用されます。 この動作を無効にするには、`AutoMLConfig` オブジェクトで `"featurization": 'off'` を設定します。

> [!NOTE]
> AutoML で作成されたモデルを [ONNX モデル](concept-onnx.md)にエクスポートする予定がある場合、アスタリスク ("*") で示された特徴量化オプションのみが ONNX 形式でサポートされます。 [モデルの ONNX への変換](how-to-use-automl-onnx-model-dotnet.md)の詳細についてご確認ください。

|特徴量化&nbsp;ステップ| 説明 |
| ------------- | ------------- |
|**高カーディナリティまたは差異なしの特徴の削除*** |これらの特徴をトレーニング セットと検証セットから削除します。 これには、まったく値が存在しない特徴、すべての行の値が同じである特徴、高いカーディナリティ (ハッシュ、ID、GUID など) の特徴に適用します。|
|**欠損値の補完*** |数値特徴の場合、その列の平均値で補完します。<br/><br/>カテゴリ特徴の場合、出現回数が最も多い値で補完します。|
|**その他の特徴の生成** _ |DateTime の特徴:年、月、日、曜日、年の通算日、四半期、年の通算週、時間、分、秒。<br><br> "_予測タスクの場合*"、次の追加の DateTime 機能が作成されます: ISO の年、半期、カレンダーの月の文字列、週、曜日の文字列、四半期の日、年の日、午前/午後 (時間が正午 (午後 12 時) より前の場合は 0、それ以外の場合は 1)、午前/午後の文字列、時間 (12 時間ベース)<br/><br/>テキストの特徴:ユニグラム、バイグラム、トライグラムに基づく期間の頻度。 詳細については、[BERT を使用してこれを実行する方法](#bert-integration)に関する記事を参照してください。|
|**変換とエンコード***|一意の値がほとんどない数値特徴を、カテゴリ特徴に変換します。<br/><br/>カーディナリティの低いカテゴリ特徴の場合、ワンホット エンコードが使用されます。 カーディナリティが高いカテゴリ特徴の場合、ワンホット ハッシュ エンコードが使用されます。|
|**ワードの埋め込み**|事前トレーニングされたモデルを使用してテキスト トークンのベクトルをセンテンス ベクトルに変換するテキスト特徴抽出器です。 ドキュメント内の各ワードの埋め込みベクトルは、ドキュメント特徴ベクトルを生成するために他のものと集約されます。|
|**クラスターの距離**|k-means クラスタリング モデルを、すべての数値列を対象にトレーニングします。 *k* 個の新しい特徴 (クラスターごとに 1 つの新しい数値特徴) が生成されます。各クラスターの重心までの各サンプルの距離が含まれます。|

## <a name="data-guardrails"></a>データ ガードレール

"*データ ガードレール*" は、データの潜在的な問題 (欠損値、[クラスの不均衡](concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data)など) を特定するのに役立ちます。 また、改善された結果を得るために是正措置を取るのにも役立ちます。

データ ガードレールは次の場合に適用されます。

- **SDK 実験の場合**: `AutoMLConfig` オブジェクトでパラメーター `"featurization": 'auto'` または `validation=auto` が指定されているとき。
- **Studio 実験の場合**: 自動特徴量化が有効になっているとき。

実験に対するデータ ガードレールは、次のようにして確認できます。

- SDK を使用して実験を送信するときに、`show_output=True` を設定します。

- Studio で、自動 ML 実行の **[Data guardrails]\(データ ガードレール\)** タブを確認します。

### <a name="data-guardrail-states"></a>データ ガードレールの状態

データ ガードレールには、3 つの状態のいずれかが表示されます。

|State| 説明 |
|----|---- |
|**成功**| データの問題は検出されませんでした。ユーザーの操作は不要です。 |
|**完了**| データに変更が適用されました。 AutoML で行われた修正アクションをレビューし、変更が予想される結果と一致することを確認することをお勧めします。 |
|**通知済み**| データの問題が検出されましたが、修復できませんでした。 修正して問題を解決することをお勧めします。|

### <a name="supported-data-guardrails"></a>サポートされているデータ ガードレール

次の表では、現在サポートされているデータ ガードレールと、実験を送信するときに表示される可能性がある関連する状態について説明します。

ガードレール|Status|トリガー&nbsp;の&nbsp;条件
---|---|---
**欠損特徴量値の補完** |Passed <br><br><br> 完了| トレーニング データで、不足している機能の値が検出されませんでした。 [不足している値の補完](./how-to-use-automated-ml-for-ml-models.md#customize-featurization)の詳細を確認してください。 <br><br> 不足している特徴の値が、トレーニング データで検出され、補完されました。
**高カーディナリティの特徴量の処理** |Passed <br><br><br> 完了| 入力が分析され、高カーディナリティの特徴は検出されませんでした。 <br><br> 高カーディナリティの特徴が入力で検出され、処理されました。
**検証分割処理** |完了| 検証構成は `'auto'` に設定されており、トレーニング データには "*20,000 行未満*" が含まれていました。 <br> トレーニング済みモデルの各イテレーションは、クロス検証を使用して検証されました。 [検証データ](./how-to-configure-auto-train.md#training-validation-and-test-data)の詳細を確認してください。 <br><br> 検証構成は `'auto'` に設定されており、トレーニング データには "*20,000 行超*" が含まれていました。 <br> 入力データはトレーニング データセットと検証データセットに分割され、モデルが検証されます。
**クラス均衡の検出** |Passed <br><br><br><br>通知済み <br><br><br>完了 | 入力が分析され、すべてのクラスがトレーニング データ内で均衡が取られます。 サンプルの数と比率によって測定され、データセット内で各クラスに適正な表現がある場合、データセットは均衡が取れていると見なされます。 <br><br> 入力で、不均衡なクラスが検出されました。 モデルのバイアスを修正するには、バランスの問題を修正します。 [不均衡なデータ](./concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data)の詳細を確認してください。<br><br> 入力で不均衡なクラスが検出され、スイープ ロジックによって分散の適用が決定されました。
**メモリの問題の検出** |Passed <br><br><br><br> 完了 |<br> 選択された値 (期間、ラグ、ローリング ウィンドウ) が分析され、潜在的なメモリ不足の問題は検出されませんでした。 時系列[予測構成](./how-to-auto-train-forecast.md#configuration-settings)の詳細を確認してください。 <br><br><br>選択された値 (期間、ラグ、ローリング ウィンドウ) が分析され、実験でメモリが不足する可能性があります。 ラグまたはローリング ウィンドウの構成がオフになっています。
**頻度の検出** |Passed <br><br><br><br> 完了 |<br> 時系列が分析され、すべてのデータ ポイントが検出済みの頻度でアラインされます。 <br> <br> 時系列が分析され、検出された頻度にアラインしないデータ ポイントが検出されました。 このようなデータ ポイントはデータセットから削除されました。 [時系列予測のデータの準備](./how-to-auto-train-forecast.md#preparing-data)の詳細を確認してください。

## <a name="customize-featurization"></a>特徴量化のカスタマイズ

ML モデルのトレーニングに使用されたデータと特徴から適切な予測が得られるよう、特徴量化の設定をカスタマイズすることができます。

特徴量化をカスタマイズするには、`AutoMLConfig` オブジェクト内で `"featurization": FeaturizationConfig` を指定します。 Azure Machine Learning Studio を実験に使用している場合、[方法に関する記事](how-to-use-automated-ml-for-ml-models.md#customize-featurization)を参照してください。 予測のタスクの種類用に特徴量化をカスタマイズするには、[予測の方法](how-to-auto-train-forecast.md#customize-featurization)に関する記事を参照してください。

サポートされるカスタマイズは次のとおりです。

|カスタマイズ|定義|
|--|--|
|**列の目的の更新**|指定した列の自動検出された特徴の種類をオーバーライドします。|
|**トランスフォーマー パラメーターの更新** |指定したトランスフォーマーのパラメーターを更新します。 現在、*Imputer* (平均値、最頻値、中央値) と *HashOneHotEncoder* がサポートされています。|
|**列の削除** |特徴量化から削除する列を指定します。|
|**ブロック トランスフォーマー**| 特徴量化プロセスで使用されるブロック トランスフォーマーを指定します。|

>[!NOTE]
> **列の削除** 機能は、SDK バージョン 1.19 の時点で非推奨になっています。 データセットの列は、自動 ML 実験で使用する前に、データ クレンジングの一部として削除してください。 

次の API 呼び出しを使用して、`FeaturizationConfig` オブジェクトを作成します。

```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```

## <a name="featurization-transparency"></a>特徴量化の透過性

すべての AutoML モデルには、特徴量化が自動的に適用されます。  特徴量化には、自動化された特徴エンジニアリング (`"featurization": 'auto'` の場合) およびスケーリングと正規化が含まれます。それにより、選択したアルゴリズムとそのハイパーパラメーター値が影響を受けます。 AutoML によって、モデルに適用された内容を確実に把握するためのさまざまな方法がサポートされています。

次の予測例を考えてみましょう。

+ 次の 4 つの入力特徴があります。A (数値)、B (数値)、C (数値)、D (DateTime)。
+ 数値の特徴 C は、すべて一意の値を持つ ID 列であるためドロップされます。
+ 数値の特徴 A と B には欠損値があるため、平均値で補完されます。
+ DateTime の特徴 D は、11 の異なるエンジニアリングされた特徴に特徴付けされます。

この情報を取得するには、自動 ML 実験の実行からの `fitted_model` 出力を使用します。

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```
### <a name="automated-feature-engineering"></a>自動化された特徴エンジニア リング 
`get_engineered_feature_names()` からはエンジニアリングされた特徴の名前の一覧が返されます。

  >[!Note]
  >task='forecasting' に 'timeseriestransformer' を使用するか、'regression' または 'classification' タスクに 'datatransformer' を使用します。

  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

このリストには、すべてのエンジニアリングされた特徴の名前が含まれます。 

  ```
  ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

`get_featurization_summary()` からは、すべての入力特徴について、特徴量化の概要が返されます。

  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

出力

  ```
  [{'RawFeatureName': 'A',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'B',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'C',
    'TypeDetected': 'Numeric',
    'Dropped': 'Yes',
    'EngineeredFeatureCount': 0,
    'Tranformations': []},
   {'RawFeatureName': 'D',
    'TypeDetected': 'DateTime',
    'Dropped': 'No',
    'EngineeredFeatureCount': 11,
    'Tranformations': ['DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime']}]
  ```

   |出力|定義|
   |----|--------|
   |RawFeatureName|指定されたデータセットの入力特徴/列の名前。|
   |TypeDetected|検出された入力特徴のデータ型。|
   |Dropped|入力特徴がドロップされたか、または使用されたかを示す。|
   |EngineeringFeatureCount|自動化された特徴エンジニアリングの変換によって生成された特徴の数。|
   |変換|エンジニアリングされた特徴を生成するために入力特徴に適用される変換の一覧。|

### <a name="scaling-and-normalization"></a>スケーリングと正規化

パイプラインのスケーリングと正規化および選択したアルゴリズムとハイパーパラメーター値を理解するには、`fitted_model.steps` を使用します。 

次のサンプル出力は、選択した実行の `fitted_model.steps` を実行したものです。

```
[('RobustScaler', 
  RobustScaler(copy=True, 
  quantile_range=[10, 90], 
  with_centering=True, 
  with_scaling=True)), 

  ('LogisticRegression', 
  LogisticRegression(C=0.18420699693267145, class_weight='balanced', 
  dual=False, 
  fit_intercept=True, 
  intercept_scaling=1, 
  max_iter=100, 
  multi_class='multinomial', 
  n_jobs=1, penalty='l2', 
  random_state=None, 
  solver='newton-cg', 
  tol=0.0001, 
  verbose=0, 
  warm_start=False))
```

詳細情報を取得するには、次のヘルパー関数を使用します。 

```python
from pprint import pprint

def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(
                e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0] + ' - ')
        else:
            pprint(step[1].get_params())
            print()

print_model(model)
```

このヘルパー関数によって、特定のアルゴリズムとして `LogisticRegression with RobustScalar` を使用している特定の実行に対して、次の出力が返されます。

```
RobustScaler
{'copy': True,
'quantile_range': [10, 90],
'with_centering': True,
'with_scaling': True}

LogisticRegression
{'C': 0.18420699693267145,
'class_weight': 'balanced',
'dual': False,
'fit_intercept': True,
'intercept_scaling': 1,
'max_iter': 100,
'multi_class': 'multinomial',
'n_jobs': 1,
'penalty': 'l2',
'random_state': None,
'solver': 'newton-cg',
'tol': 0.0001,
'verbose': 0,
'warm_start': False}
```

### <a name="predict-class-probability"></a>クラスの確率を予測する

自動 ML を使用して生成されたモデルにはすべて、オープンソースの元のクラスの機能をミラー化するラッパー オブジェクトがあります。 自動 ML によって返されるほとんどの分類モデル ラッパー オブジェクトは、`predict_proba()` 関数を実装しています。これは特徴 (X 値) の配列に似た、または疎行列のデータ サンプルを受け入れ、各サンプルの n 次元配列と、それぞれのクラスの確率を返します。

上記と同じ呼び出しを使用して最適な実行済みの適合モデルを取得したことを前提として、適合モデルから直接 `predict_proba()` を呼び出して、モデルの種類に応じて適切な形式で `X_test` サンプルを指定することができます。

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

基になるモデルが `predict_proba()` 関数をサポートしていない場合、または形式が正しくない場合は、モデル クラス固有の例外がスローされます。 さまざまな種類のモデルに対してこの関数を実装する方法の例については、[RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) と [XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html) のリファレンス ドキュメントを参照してください。

<a name="bert-integration"></a>

## <a name="bert-integration-in-automated-ml"></a>自動 ML での BERT 統合

[BERT](https://techcommunity.microsoft.com/t5/azure-ai/how-bert-is-integrated-into-azure-automated-machine-learning/ba-p/1194657) は、AutoML の特徴量化層で使用されます。 この層では、列にフリー テキストやその他の種類のデータ (タイムスタンプや単純な数値など) が含まれている場合は、それに応じて特徴量化が適用されます。

BERT の場合、モデルはユーザー指定のラベルを使用して微調整およびトレーニングされます。 ここから、ドキュメント埋め込みが、タイムスタンプベースの特徴 (曜日) などの他の特徴と同様に、特徴として出力されます。 


### <a name="steps-to-invoke-bert"></a>BERT を呼び出す手順

BERT を呼び出すには、automl_settings に `enable_dnn: True` を設定し、GPU コンピューティング (`vm_size = "STANDARD_NC6"`、またはそれ以上の GPU) を使用します。 CPU コンピューティングを使用した場合、AutoML によって、BERT ではなく BiLSTM DNN 特徴抽出器が有効になります。

BERT の場合は、AutoML で次の手順が行われます。 

1. **すべてのテキスト列の前処理とトークン化**。 たとえば、"StringCast" トランスフォーマーは、最終的なモデルの特徴量化の概要内にあります。 モデルの特徴量化の概要を生成する方法の例については、[こちらのノートブック](https://towardsdatascience.com/automated-text-classification-using-machine-learning-3df4f4f9570b)を参照してください。

2. **すべてのテキスト列を 1 つのテキスト列に連結** します。そのため、最終的なモデル内では `StringConcatTransformer` になります。 

    BERT の実装では、トレーニング サンプルのテキストの長さの合計は 128 トークンに制限されています。 つまり、すべてのテキスト列を連結した長さは、最大で 128 トークンになることが理想です。 複数の列がある場合は、この条件が満たされるように各列を切り詰める必要があります。 それ以外の場合、長さが 128 トークンを超える連結列については、BERT のトークナイザー層により、この入力は 128 トークンに切り詰められます。

3. **特徴量スイープの一部として、AutoML で BERT がデータ サンプルのベースライン (bag-of-words 特徴量) と比較されます。** この比較では、BERT によって精度が向上するかどうかが判断されます。 BERT のパフォーマンスがベースラインよりも高い場合、AutoML でデータ全体に対して BERT がテキスト特徴量化に使用されます。 その場合は、最終的なモデルに `PretrainedTextDNNTransformer` が表示されます。

BERT は通常、他の特徴抽出器よりも長く実行されます。 パフォーマンスを向上させるには、RDMA 機能に "STANDARD_NC24r" または "STANDARD_NC24rs_V3" を使用することをお勧めします。 

AutoML によって、可能な場合、BERT トレーニングが複数のノードに分散されます (最大 8 ノードまで)。 これは、`AutoMLConfig` オブジェクトで `max_concurrent_iterations` パラメーターを 1 より大きく設定することによって、行うことができます。 

## <a name="supported-languages-for-bert-in-automl"></a>autoML で BERT に対してサポートされている言語 

autoML では現在約 100 の言語がサポートされており、データセットの言語に応じて、適切な BERT モデルが選択されます。 ドイツ語のデータの場合は、ドイツ語の BERT モデルが使用されます。 英語の場合は、英語の BERT モデルが使用されます。 その他のすべての言語では、多言語 BERT モデルが使用されます。

次のコードでは、データセットの言語が `deu` ([ISO 分類](https://iso639-3.sil.org/code/deu)でドイツ語を示す 3 文字の言語コード) に指定されているため、ドイツ語の BERT モデルがトリガーされます。

```python
from azureml.automl.core.featurization import FeaturizationConfig

featurization_config = FeaturizationConfig(dataset_language='deu')

automl_settings = {
    "experiment_timeout_minutes": 120,
    "primary_metric": 'accuracy', 
# All other settings you want to use 
    "featurization": featurization_config,
    
  "enable_dnn": True, # This enables BERT DNN featurizer
    "enable_voting_ensemble": False,
    "enable_stack_ensemble": False
}
```

## <a name="next-steps"></a>次のステップ

* 自動 ML の実験を設定する方法を確認します。

    * コード ファーストのエクスペリエンスについて: [Azure Machine Learning SDK を使用して自動 ML の実験を構成する](how-to-configure-auto-train.md)。
    * 少量のコードまたはコードなしのエクスペリエンスについて: [Azure Machine Learning Studio で自動 ML 実験を作成する](how-to-use-automated-ml-for-ml-models.md)。

* [モデルをデプロイする方法と場所](how-to-deploy-and-where.md)についてさらに詳しく学習する。

* [自動機械学習を使用して回帰モデルをトレーニングする方法](tutorial-auto-train-models.md)または[リモート リソースに対して自動機械学習を使用してトレーニングする方法](how-to-auto-train-remote.md)についてさらに詳しく確認します。
