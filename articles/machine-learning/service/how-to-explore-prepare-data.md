---
title: データを探索して変換する (Dataset クラス)
titleSuffix: Azure Machine Learning service
description: 概要統計情報を使ってデータを探索し、データの消去、変換、および特徴エンジニアリングを使用してデータを準備する
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/23/2019
ms.openlocfilehash: 80137c7f1ecebab4d2da0c4b7ba0ca9292dad22e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443965"
---
# <a name="explore-and-prepare-data-with-the-dataset-class-preview"></a>Dataset クラス (プレビュー) でデータを探索して準備する

[Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) の azureml-datasets パッケージを使用して、データを探索して準備する方法について説明します。 [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) クラス (プレビュー) では、サンプリング、概要統計情報およびインテリジェントな変換などの機能を提供することで、データを探索して準備することができます。 変換の手順は、拡張性の高い方法でさまざまなスキーマの複数の大きなファイルを処理する機能を使用して、[Dataset 定義](how-to-manage-dataset-definitions.md)に保存されます。

> [!Important]
> 一部の Dataset クラス (プレビュー) は [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) パッケージ (GA) に依存しています。 変換関数を GA の [Data Prep 関数](how-to-transform-data.md)で直接実行することはできますが、新しいソリューションを構築する場合は、この記事で説明されている Dataset パッケージ ラッパーをお勧めします。 Azure Machine Learning Datasets (プレビュー) ではデータを変換するだけでなく、[データのスナップショットを作成し](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py)、[バージョン管理されたデータセット定義](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py)を格納することもできます。 Datasets は、次のバージョンの Data Prep SDK であり、AI ソリューションでデータセットを管理するための拡張機能が提供されます。

## <a name="prerequisites"></a>前提条件

データを探索して準備するには、以下のものが必要です。

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning service](https://aka.ms/AMLFree) を今日からお試しいただけます。

* Azure Machine Learning ワークスペース。 「[Create an Azure Machine Learning service workspace](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace)」(Azure Machine Learning service ワークスペースを作成する) を参照してください。

* Azure Machine Learning SDK for Python (バージョン 1.0.21 以降)。azureml-datasets パッケージが含まれています。 SDK の最新バージョンのインストールまたは最新バージョンへの更新を行うには、[SDK のインストールまたは更新](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)に関する記事を参照してください。

* Azure Machine Learning Data Prep SDK。 最新バージョンのインストールまたは更新については、[Data Prep SDK のインストールまたは更新](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py#install)に関する記事を参照してください。

* サンプル ファイル ([crime.csv](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv) と [city.json](https://dprepdata.blob.core.windows.net/dataset-sample-files/city.json)) をダウンロードして、例に従います。

## <a name="sampling"></a>サンプリング

データのサンプルを取得し、データのアーキテクチャとコンテンツを早いうちに把握しておきます。 この時点では、Dataset クラスの [`sample()`](https://docs.microsoft.com//python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#sample-sample-strategy--arguments-) メソッドで上位 N 個、簡単なランダム、および階層サンプリング戦略がサポートされます。

```Python
from azureml.core.dataset import Dataset
import random

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')

# create seed for Simple Random and Stratified sampling
seed = random.randint(0, 4294967295)
```

### <a name="top-n-sample"></a>上位 N 個のサンプル

上位 N 個のサンプリングでは、Dataset の最初の n 個のレコードがサンプルとなります。 これは、データ レコードの外観を想像してみる場合や、データ内のフィールドを見てみるだけの場合に便利です。

```Python
top_n_sample_dataset = dataset.sample('top_n', {'n': 5})
top_n_sample_dataset.to_pandas_dataframe()
```

||id|事件番号|Date|ブロック|IUCR|プライマリ タイプ|...|
-|--|-----------|----|-----|----|------------|---
0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH ST|1153|DECEPTIVE PRACTICE|...
1|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|THEFT|...
2|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO AVE|1154|DECEPTIVE PRACTICE|...
3|10519591|HZ261534|4/15/2016 9:00|113XX S PRAIRIE AVE|1120|DECEPTIVE PRACTICE|...
4|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|THEFT|...

### <a name="simple-random-sample"></a>簡単なランダム サンプル

簡単なランダム サンプリングでは、データ母集団のすべてのメンバーが、サンプルの一部として選択される可能性が等しくなります。 `simple_random` サンプル戦略では、Dataset からのレコードが指定された確率に基づいて選択され、変更後の Dataset が返されます。 シード パラメーターは省略可能です。

```Python
simple_random_sample_dataset = dataset.sample('simple_random', {'probability':0.3, 'seed': seed})
simple_random_sample_dataset.to_pandas_dataframe()
```

||id|事件番号|Date|ブロック|IUCR|プライマリ タイプ|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|THEFT|...
1|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO AVE|1154|DECEPTIVE PRACTICE|...
2|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|THEFT|...

### <a name="stratified-sample"></a>階層サンプル

階層サンプルでは確実に、サンプルで母集団の特定のグループが表されます。 `stratified` サンプル戦略では、類似性に基づいて、母集団が階層 (つまり、サブグループ) に分割され、レコードは、`fractions` パラメーターによって示される階層重みに従って、各階層からランダムに選択されます。

次の例では、指定された列で各レコードをグループ化し、`fractions` で階層 X 重み情報に基づいて、そのレコードを含めます。 階層が指定されていないか、レコードをグループ化できない場合、サンプリングする既定の重みは 0 になります。

```Python
# take 50% of records with `Primary Type` as `THEFT` and 20% of records with `Primary Type` as `DECEPTIVE PRACTICE` into sample Dataset
fractions = {}
fractions[('THEFT',)] = 0.5
fractions[('DECEPTIVE PRACTICE',)] = 0.2

sample_dataset = dataset.sample('stratified', {'columns': ['Primary Type'], 'fractions': fractions, 'seed': seed})

sample_dataset.to_pandas_dataframe()
```

||id|事件番号|Date|ブロック|IUCR|プライマリ タイプ|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|THEFT|...
1|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|THEFT|...
2|10535059|HZ278872|4/15/2016 4:30|004XX S KILBOURN AVE|810|THEFT|...

## <a name="explore-with-summary-statistics"></a>概要統計情報を使用して探索する

 [`get_profile()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-) メソッドを使用して、異常、欠損値、エラーの数を検出します。 この関数ではデータのプロファイルと概要統計情報を取得します。これは、適用する必要なデータ準備操作を決定するのに役立ちます。

```Python
dataset.get_profile()
```

||Type|Min|max|Count|Missing Count|Not Missing Count|Percent missing|Error Count|Empty count|0.1% Quantile|1% Quantile|5% Quantile|25% Quantile|50% Quantile|75% Quantile|95% Quantile|99% Quantile|99.9% Quantile|平均|標準偏差|variance|傾斜|尖度
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
id|FieldType.INTEGER|1.04986e+07|1.05351e+07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986e+07|1.04992e+07|1.04986e+07|1.05166e+07|1.05209e+07|1.05259e+07|1.05351e+07|1.05351e+07|1.05351e+07|1.05195e+07|12302.7|1.51358e+08|-0.495701|-1.02814
事件番号|FieldType.STRING|HZ239907|HZ278872|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Date|FieldType.DATE|2016-04-04 23:56:00+00:00|2016-04-15 17:00:00+00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
ブロック|FieldType.STRING|004XX S KILBOURN AVE|113XX S PRAIRIE AVE|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
IUCR|FieldType.INTEGER|810|1154|10.0|0.0|10.0|0.0|0.0|0.0|810|850|810|890|1136|1153|1154|1154|1154|1058.5|137.285|18847.2|-0.785501|-1.3543
プライマリ タイプ|FieldType.STRING|DECEPTIVE PRACTICE|THEFT|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
説明|FieldType.STRING|BOGUS CHECK|OVER $500|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
場所の説明|FieldType.STRING||SCHOOL, PUBLIC, BUILDING|10.0|0.0|10.0|0.0|0.0|1.0||||||||||||||
逮捕|FieldType.BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
国内|FieldType.BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Beat|FieldType.INTEGER|531|2433|10.0|0.0|10.0|0.0|0.0|0.0|531|531|531|614|1318.5|1911|2433|2433|2433|1371.1|692.094|478994|0.105418|-1.60684
District|FieldType.INTEGER|5|24|10.0|0.0|10.0|0.0|0.0|0.0|5|5|5|6|13|19|24|24|24|13.5|6.94822|48.2778|0.0930109|-1.62325
区|FieldType.INTEGER|1|48|10.0|0.0|10.0|0.0|0.0|0.0|1|5|1|9|22.5|40|48|48|48|24.5|16.2635|264.5|0.173723|-1.51271
コミュニティ エリア|FieldType.INTEGER|4|77|10.0|0.0|10.0|0.0|0.0|0.0|4|8.5|4|24|37.5|71|77|77|77|41.2|26.6366|709.511|0.112157|-1.73379
FBI コード|FieldType.INTEGER|6|11|10.0|0.0|10.0|0.0|0.0|0.0|6|6|6|6|11|11|11|11|11|9.4|2.36643|5.6|-0.702685|-1.59582
X 座標|FieldType.INTEGER|1.16309e+06|1.18336e+06|10.0|7.0|3.0|0.7|0.0|0.0|1.16309e+06|1.16309e+06|1.16309e+06|1.16401e+06|1.16678e+06|1.17921e+06|1.18336e+06|1.18336e+06|1.18336e+06|1.17108e+06|10793.5|1.165e+08|0.335126|-2.33333
Y 座標|FieldType.INTEGER|1.8315e+06|1.908e+06|10.0|7.0|3.0|0.7|0.0|0.0|1.8315e+06|1.8315e+06|1.8315e+06|1.83614e+06|1.85005e+06|1.89352e+06|1.908e+06|1.908e+06|1.908e+06|1.86319e+06|39905.2|1.59243e+09|0.293465|-2.33333
年|FieldType.INTEGER|2016|2016|10.0|0.0|10.0|0.0|0.0|0.0|2016|2016|2016|2016|2016|2016|2016|2016|2016|2016|0|0|(NaN)|(NaN)
更新日|FieldType.DATE|2016-05-11 15:48:00+00:00|2016-05-27 15:45:00+00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Latitude|FieldType.DECIMAL|41.6928|41.9032|10.0|7.0|3.0|0.7|0.0|0.0|41.6928|41.6928|41.6928|41.7057|41.7441|41.8634|41.9032|41.9032|41.9032|41.78|0.109695|0.012033|0.292478|-2.33333
Longitude|FieldType.DECIMAL|-87.6764|-87.6043|10.0|7.0|3.0|0.7|0.0|0.0|-87.6764|-87.6764|-87.6764|-87.6734|-87.6645|-87.6194|-87.6043|-87.6043|-87.6043|-87.6484|0.0386264|0.001492|0.344429|-2.33333
Location|FieldType.STRING||(41.903206037, -87.676361925)|10.0|0.0|10.0|0.0|0.0|7.0||||||||||||||

## <a name="impute-missing-values"></a>欠損値の補完

Datasets では、null 値、NaN、コンテンツがない値が欠損値と見なされます。 これらは機械学習モデルのパフォーマンスに影響する可能性があります。また、これらにより、結果が無効になる可能性もあります。 補完は欠損値に対処する 1 つの一般的な方法であり、単に削除できない欠損値レコードの割合が高い場合に役立ちます。

前のセクションで生成された Dataset プロファイルから、`Latitude` と `Longitude` の列の欠損値の割合が高いことがわかります。 この例では、これら 2 つの列の欠損値の平均値を計算し、補完します。

最初に、[`get_definition()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-definition-version-id-none-) を使用して Dataset の最新の定義を取得し、[`keep_columns()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#keep-columns-columns--multicolumnselection--validate-column-exists--bool---false-----azureml-dataprep-api-dataflow-dataflow) を使用してデータを減らし、対処する列のみを表示するようにします。

```Python
from azureml.core.dataset import Dataset
import azureml.dataprep as dprep

# get the latest definition of Dataset
ds_def = dataset.get_definition()

# keep useful columns for this example
ds_def = ds_def.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
ds_def.head(3)
```

||id|逮捕| Latitude|Longitude|
-|---------|-----|---------|----------|
|0|10498554|False|41.692834|-87.604319|
|1|10516598|False| 41.744107 |-87.664494|
|2|10519196|False| (NaN)|(NaN)|

次に、[`summarize()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow-summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow) 関数を使って、緯度列の `MEAN` 値を確認します。 この関数は、集計レベルを指定するための `group_by_columns` パラメーターで、列の配列を受け取ります。 `summary_columns` パラメーターでは、現在の列名、新しい計算フィールド名、実行する `SummaryFunction` を指定する、`SummaryColumnsValue` 関数を受け入れます。

```Python
lat_mean = ds_def.summarize(group_by_columns = ['Arrest'],
                            summary_columns = [dprep.SummaryColumnsValue(column_id = 'Latitude',
                                                                         summary_column_name = 'Latitude_MEAN',
                                                                         summary_function = dprep.SummaryFunction.MEAN)])
lat_mean = lat_mean.filter(lat_mean['Arrest'] == False)
lat_mean.head(1)
```

||逮捕|Latitude_MEAN|
--|-----|--------|
|0|False|41.780049|

補完する値を確認したら、[`ImputeMissingValuesBuilder`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py) を使用して、計算された `MIN`、`MAX`、`MEAN` 値、または `CUSTOM` 値で列を補完する、修正された式を学習します。 `group_by_columns` を指定すると、グループ別に算出された `MIN`、 `MAX`、および `MEAN` でグループごとに欠損値が補完されます｡

[`ImputeColumnArguments`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputecolumnarguments?view=azure-dataprep-pyfunction) では、column_id 文字列と、補完の種類を指定する `ReplaceValueFunction` を受け入れます。 経度の欠損値の場合は、外部のナレッジに基づき -87 で補完します。

```Python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id = 'Latitude',
                                          impute_function = dprep.ReplaceValueFunction.MEAN)

# impute with custom value -87
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=-87)
```

[`Builders`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py) クラス関数 [`impute_missing_values()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py#impute-missing-values-impute-columns--typing-list-azureml-dataprep-api-builders-imputecolumnarguments----none--group-by-columns--typing-union-typing-list-str---nonetype----none-----azureml-dataprep-api-builders-imputemissingvaluesbuilder) を使って、補完の手順を `ImputeMissingValuesBuilder` オブジェクトに連結できます。 `impute_columns` パラメーターは、`ImputeColumnArguments` オブジェクトの配列を受け取ります。

```Python
# get instance of ImputeMissingValuesBuilder
impute_builder = ds_def.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])
```

[`learn()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#learn------none) 関数を呼び出して補完の手順を格納し、[`to_dataflow()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#to-dataflow------azureml-dataprep-api-dataflow-dataflow) を使ってデータフロー オブジェクトにそれらを適用します。

```Python
impute_builder.learn()
ds_def = impute_builder.to_dataflow()
ds_def.head(3)
```

次の出力テーブルに示されているように、欠損緯度は `Arrest==False` グループの `MEAN` 値で補間されており、欠損経度は -87 で補完されています。

||id|逮捕|Latitude|Longitude
-|---------|-----|---------|----------
0|10498554|False|41.692834|-87.604319
1|10516598|False|41.744107|-87.664494
2|10519196|False|41.780049|-87.000000

[`update_definition()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#update-definition-definition--definition-update-message-) を使用して、Dataset 定義を更新し、実行された変換手順を保持します。

```Python
dataset = dataset.update_definition(ds_def, 'Impute Missing')
dataset.head(3)
```

||id|逮捕|Latitude|Longitude
-|---------|-----|---------|----------
0|10498554|False|41.692834|-87.604319
1|10516598|False|41.744107|-87.664494
2|10519196|False|41.780049|-87.000000

## <a name="create-assertion-rules"></a>アサーション規則を作成する

多くの場合、データの消去と準備中に操作するデータは、運用環境で必要な合計データのサブセットに過ぎません。 その結果、消去の一部として行われる想定のいくつかが false になる可能性があります。 たとえば、継続的に更新するデータ セットでは、最初に特定の範囲内の数値のみが含まれていた列に、以降の実行で広い範囲の値が含まれる可能性があります。 これらのエラーにより、多くの場合、パイプラインが切断されるか、データが不適切なものになります。

データセットは、パイプラインの実行時に評価されるデータに対するアサーションの作成をサポートしています。 これらのアサーションを使用して、データに関する想定が引き続き正確であることを確認し、そうでない場合は、適宜、エラーを処理できます。

たとえば、Dataset 内の `Latitude` と `Longitude` の値を特定の数値範囲に制限したい場合は、[`assert_value()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#assert-value-columns--multicolumnselection--expression--azureml-dataprep-api-expressions-expression--policy--azureml-dataprep-api-engineapi-typedefinitions-assertpolicy----assertpolicy-errorvalue--1---error-code--str----assertionfailed------azureml-dataprep-api-dataflow-dataflow) メソッドを使用して確実に、常にそうなるように指定できます。

```Python
from azureml.dataprep import value
from azureml.core.dataset import Dataset

# get the latest definition of the Dataset
ds_def = dataset.get_definition()

# set assertion rules for `Latitude` and `Longitude` columns
ds_def = ds_def.assert_value('Latitude', (value <= 90) & (value >= -90), error_code='InvalidLatitude')
ds_def = ds_def.assert_value('Longitude', (value <= 180) & (value >= -87), error_code='InvalidLongitude')

ds_def.get_profile()
```

||Type|Min|max|Count|Missing Count|Not Missing Count|Percent missing|Error Count|Empty count|0.1% Quantile|1% Quantile|5% Quantile|25% Quantile|50% Quantile|75% Quantile|95% Quantile|99% Quantile|99.9% Quantile|平均|標準偏差|variance|傾斜|尖度
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
id|FieldType.INTEGER|1.04986e+07|1.05351e+07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986e+07|1.04992e+07|1.04986e+07|1.05166e+07|1.05209e+07|1.05259e+07|1.05351e+07|1.05351e+07|1.05351e+07|1.05195e+07|12302.7|1.51358e+08|-0.495701|-1.02814
逮捕|FieldType.BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Latitude|FieldType.DECIMAL|41.6928|41.9032|10.0|0.0|10.0|0.0|0.0|0.0|41.6928|41.7185|41.6928|41.78|41.78|41.78|41.9032|41.9032|41.9032|41.78|0.0517107|0.002674|0.837593|1.05
Longitude|FieldType.INTEGER|-87|-87|10.0|0.0|10.0|0.0|3.0|0.0|-87|-87|-87|-87|-87|-87|-87|-87|-87|-87|0|0|(NaN)|(NaN)

プロファイルから、`Longitude` 列の `Error Count` が 3 であることがわかります。 次のコードでは、Dataset をフィルター処理し、エラーを取得して、アサーションが失敗する原因となる値を確認します。 ここでは、コードを調整し、データを適切にクレンジングします。

```Python
from azureml.dataprep import col

ds_error = ds_def.filter(col('Longitude').is_error())
error = ds_error.head(10)['Longitude'][0]

print(error.originalValue)
```

    -87.60431945

## <a name="derive-columns-by-example"></a>例を使用して列を派生させる

Datasets のより高度なツールの 1 つは、目的の結果の例を使用して列を派生させる機能です。 これにより、SDK に列を提供して、目的の変換を実現するコードを生成できます。

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')
dataset.head(3)
```

||id|事件番号|Date|ブロック|...|
-|---------|-----|---------|----|---
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|...
1|10516598|HZ258664|2016-04-15 17:00:00|082XX S MARSHFIELD AVE|...
2|10519196|HZ261252|2016-04-15 10:00:00|104XX S SACRAMENTO AVE|...

たとえば、日付と時刻の形式を '2016-04-04 10PM-12AM' に変換する必要があるとします。 [`derive_column_by_example()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#derive-column-by-example-source-columns--sourcecolumns--new-column-name--str--example-data--exampledata-----azureml-dataprep-api-dataflow-dataflow) 引数では、 *(元の出力, 目的の出力)* .という形式で、`example_data` パラメーターの目的の出力の例を示します。

次のコードでは、目的の出力の 2 つの例 ("2016-04-04 23:56:00", "2016-04-04 10PM-12AM") と ("2016-04-15 17:00:00", "2016-04-15 4PM-6PM") を示します。

```Python
ds_def = dataset.get_definition()
ds_def = ds_def.derive_column_by_example(
        source_columns = "Date",
        new_column_name = "Date_Time_Range",
        example_data = [("2016-04-04 23:56:00", "2016-04-04 10PM-12AM"), ("2016-04-15 17:00:00", "2016-04-15 4PM-6PM")]
    )
ds_def.keep_columns(['ID','Date','Date_Time_Range']).head(3)
```

次の表の新しい列である Date_Time_Range に、指定された形式のレコードが含まれていることに注目してください。

||id|Date|Date_Time_Range
-|--------|-----|----
0|10498554|2016-04-04 23:56:00|2016-04-04 10PM-12AM
1|10516598|2016-04-15 17:00:00|2016-04-15 4PM-6PM
2|10519196|2016-04-15 10:00:00|2016-04-15 10AM-12PM

```Python
# update Dataset definition to keep the transformation steps performed.
dataset = dataset.update_definition(ds_def, 'Derive Date_Time_Range')
```

## <a name="fuzzy-groupings"></a>あいまいグループ化

さまざまなソースからデータを収集するときに、同じエンティティのスペル、大文字と小文字、または省略形が異なる場合があります。 そのような場合は、SDK のあいまいグループ化 (つまり、テキスト クラスタリング) 機能を使用し、自動的に標準化して調整します。

たとえば、`inspections.business.city` 列に、いくつかの形式の "San Francisco" という市区町村名が含まれているとします。

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local json file
dataset = Dataset.auto_read_files('./data/city.json')
dataset.head(5)
```

||inspections.business.business_id|inspections.business_name|inspections.business.address|inspections.business.city|...|
-|-----|-------------------------|------------|--|---
0|16162|Quick-N-Ezee Indian Foods|3861 24th St|SF|...
1|67565|King of Thai Noodles Cafe|1541 TARAVAL St|SAN FRANCISCO|...
2|67565|King of Thai Noodles Cafe|1541 TARAVAL St|SAN FRANCISCO|...
3|68701|Grindz|832 Clement St|SF|...
4|69186|Premier Catering & Events, Inc.|1255 22nd St|S.F.|...

[`fuzzy_group_column()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#fuzzy-group-column-source-column--str--new-column-name--str--similarity-threshold--float---0-8--similarity-score-column-name--str---none-----azureml-dataprep-api-dataflow-dataflow) メソッドを使用して、自動検出された正規形式の "San Francisco" を含む列を追加してみましょう。 引数の `source_column` と `new_column_name` が必要です。 以下のオプションもあります。

* 新しい列として `similarity_score_column_name` を作成する。これには、元の値と正規値のすべてのペア間の類似性スコアが表示されます。

* `similarity_threshold` を指定する。これは、まとめてグループ化する値の最小類似性スコアです。

```Python
# get the latest Dataset definition
ds_def = dataset.get_definition()
ds_def = ds_def.fuzzy_group_column(source_column='inspections.business.city',
                                       new_column_name='city_grouped',
                                       similarity_threshold=0.8,
                                       similarity_score_column_name='similarity_score')
ds_def.head(5)
```

||inspections.business.business_id|inspections.business_name|inspections.business.address|inspections.business.city|city_grouped|similarity_score|...|
-|-----|-------------------------|------------|--|---|---|---
0|16162|Quick-N-Ezee Indian Foods|3861 24th St|SF|San Francisco|0.814806|...
1|67565|King of Thai Noodles Cafe|1541 TARAVAL St|SAN FRANCISCO|San Francisco|1.000000|...
2|67565|King of Thai Noodles Cafe|1541 TARAVAL St|SAN FRANCISCO|San Francisco|1.000000|...
3|68701|Grindz|832 Clement St|SF|San Francisco|0.814806|...
4|69186|Premier Catering & Events, Inc.|1255 22nd St|S.F.|San Francisco|0.814806|...

結果として得られる Dataset 定義では、データの "San Francisco" を表すさまざまなバリエーションがすべて、同じ文字列の "San Francisco" に標準化されています。

`update_definition()` を使用して、このあいまいグループ化の手順を最新の Dataset 定義に保存します。

```Python
dataset = dataset.update_definition(ds_def, 'fuzzy grouping')
```

## <a name="next-steps"></a>次の手順

* 回帰モデルの例については、自動機械学習の[チュートリアル](tutorial-auto-train-models.md)を参照してください。

* 設計パターンと使用例については、SDK の [概要](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)を参照してください。

* Datasets の使用例については、[サンプル ノートブック](https://aka.ms/dataset-tutorial)を参照してください。
