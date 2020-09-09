---
title: 自動機械学習の実験でクロス検証とデータの分割を構成する
titleSuffix: Azure Machine Learning
description: 自動機械学習の実験のためにクロス検証とデータセットの分割を構成する方法について説明します
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: cesardl
author: CESARDELATORRE
ms.reviewer: nibaccam
ms.date: 06/16/2020
ms.openlocfilehash: 900d5cd435a913c0859c862d176fd30130e0a079
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321498"
---
# <a name="configure-data-splits-and-cross-validation-in-automated-machine-learning"></a>自動機械学習の実験でデータの分割とクロス検証を構成する

この記事では、自動機械学習、AutoML、実験のトレーニング/検証データの分割とクロス検証を構成するための、さまざまなオプションについて説明します。

Azure Machine Learning では、AutoML を使用して複数の ML モデルを構築する場合、各子実行では、そのモデルの品質メトリック (精度や加重 ACU など) を計算することによって関連モデルを検証する必要があります。 これらのメトリックは、各モデルで行われた予測を、検証データの過去の観測からの実際のラベルと比較することによって計算されます。 

AutoML の実験では、モデルの検証が自動的に実行されます。 次のセクションでは、[Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py) で検証の設定をカスタマイズする方法について説明します。 

コードを使用しないエクスペリエンスの詳細については、[Azure Machine Learning Studio での自動機械学習の実験の作成](how-to-use-automated-ml-for-ml-models.md)に関するページを参照してください。 

> [!NOTE]
> 現時点でスタジオでは、トレーニング/検証データの分割とクロス検証のオプションがサポートされていますが、検証セットに個別のデータ ファイルを指定することはできません。 

## <a name="prerequisites"></a>前提条件

この記事では、以下が必要です。

* Azure Machine Learning ワークスペース。 ワークスペースを作成するには、[Azure Machine Learning ワークスペース](how-to-manage-workspace.md)の作成に関するページを参照してください。

* Azure Machine Learning SDK を使用した自動機械学習の実験の設定に関する知識。 [チュートリアル](tutorial-auto-train-models.md)または[ハウツー](how-to-configure-auto-train.md)に従って、自動化された機械学習実験の基本的な設計パターンについて確認してください。

* ML の概念としてのクロス検証とトレーニング/検証のデータの分割に関する知識。 概要については、以下を参照してください。

    * [機械学習におけるトレーニング、検証、およびテストのセットについて](https://towardsdatascience.com/train-validation-and-test-sets-72cb40cba9e7)

    * [クロス検証について](https://towardsdatascience.com/understanding-cross-validation-419dbd47e9bd)

## <a name="default--data-splits-and-cross-validation"></a>既定のデータの分割とクロス検証

[AutoMLConfig](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) オブジェクトを使用して、実験とトレーニングの設定を定義します。 次のコード スニペットでは、必須パラメーターのみが定義されていることに注意してください。つまり、`n_cross_validation` または `validation_ data` のパラメーターは**含まれていません**。

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'Class'
                            )
```

`validation_data` または `n_cross_validation` のいずれかのパラメーターを明示的に指定しない場合は、指定された 1 つのデータ セット `training_data` の行数に応じて、AutoML によって既定の方法が適用されます。

|トレーニング&nbsp;データ&nbsp;のサイズ| 検証の方法 |
|---|-----|
|**20,000&nbsp;行&nbsp;より&nbsp;多い**| トレーニング/検証データの分割が適用されます。 既定では、初期トレーニング データ セットの 10% が検証セットとして取得されます。 次に、その検証セットがメトリックの計算に使用されます。
|**20,000&nbsp;行&nbsp;より&nbsp;少ない**| クロス検証アプローチが適用されます。 フォールドの既定の数は行数によって異なります。 <br> **データセットが 1,000 行より少ない場合は**、10 個のフォールドが使用されます。 <br> **行が 1,000 から 20,000 の間の場合は**、3 つのフォールドが使用されます。

## <a name="provide-validation-data"></a>検証データを指定する

この場合は、1 つのデータ ファイルから始めて、トレーニング セットと検証セットに分割するか、検証セット用に個別のデータ ファイルを指定することができます。 どちらの方法でも、`AutoMLConfig` オブジェクトの `validation_data` パラメーターによって、検証セットとして使用するデータが割り当てられます。 このパラメーターは、[Azure Machine Learning データセット](how-to-create-register-datasets.md)または Pandas データフレームの形式のデータセットのみを受け入れます。   

次のコード例では、トレーニングと検証に使用する `dataset` 内の指定されたデータの一部を明示的に定義します。

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

training_data, validation_data = dataset.random_split(percentage=0.8, seed=1)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = training_data,
                             validation_data = validation_data,
                             label_column_name = 'Class'
                            )
```

## <a name="provide-validation-set-size"></a>検証セットのサイズを指定する

この場合、実験には 1 つのデータセットのみが指定されています。 つまり、`validation_data` パラメーターは**指定されず**、指定されたデータセットは `training_data` パラメーターに割り当てられます。  `AutoMLConfig` オブジェクトでは、検証用にトレーニング データの一部を提示するように `validation_size` パラメーターを設定できます。 これは、検証セットが指定された初期の `training_data` から AutoML によって分割されることを意味します。 この値は 0.0 より大きく 1.0 未満である必要があります (たとえば、0.2 は、データの 20% が検証データ用に提示されることを意味します)。

次のコード例をご覧ください。

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             validation_size = 0.2,
                             label_column_name = 'Class'
                            )
```

## <a name="set-the-number-of-cross-validations"></a>クロス検証の数を設定する

クロス検証を実行するには、`n_cross_validations` パラメーターを含めて、それを値に設定します。 このパラメーターは、同じフォールド数に基づいて、実行するクロス検証の回数を設定します。

次のコードでは、クロス検証のための 5 つのフォールドが定義されています。 そのため、5 つの異なるトレーニングがあり、各トレーニングには 5 分の 4 のデータが使用され、各検証には、ホールドアウト フォールドが毎回異なる 5 分の 1 のデータが使用されます。

その結果、メトリックは、5 つの検証メトリックの平均値を使用して計算されます。

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 5
                             label_column_name = 'Class'
                            )
```

## <a name="specify-custom-cross-validation-data-folds"></a>カスタムのクロス検証データ フォールドを指定する

独自のクロス検証 (CV) データ フォールドを指定することもできます。 分割して検証に使用する列を指定するため、これはより高度なシナリオと見なされます。  トレーニング データにカスタムの CV 分割列を含め、`cv_split_column_names` パラメーターで列名を入力して列を指定します。 各列は 1 つのクロス検証分割を表し、整数値 1 または 0 が入力されます。ここで、1 は行がトレーニングに使用されることを示し、0 は行が検証に使用されることを示します。

次のコード スニペットには、2 つの CV 分割列 'cv1' と 'cv2' がある銀行のマーケティング データが含まれています。

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_with_cv.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'y',
                             cv_split_column_names = ['cv1', 'cv2']
                            )
```

> [!NOTE]
> `cv_split_column_names` を `training_data` および `label_column_name` と共に使用するには、Azure Machine Learning Python SDK バージョン 1.6.0 以降をアップグレードしてください。 以前のバージョンの SDK の場合は、`cv_splits_indices` の使用を参照してください。ただし、`X` と `y` のデータセット入力のみで使用されていることに注意してください。 

## <a name="next-steps"></a>次のステップ

* [不均衡データとオーバーフィットを防止します](concept-manage-ml-pitfalls.md)。
* [チュートリアル:自動機械学習を使用してタクシー料金を予測する - データの分割セクション](tutorial-auto-train-models.md#split-the-data-into-train-and-test-sets)。
* [時系列予測モデルを自動トレーニングする](how-to-auto-train-forecast.md)方法。
