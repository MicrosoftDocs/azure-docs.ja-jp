---
title: 自動機械学習でのデータの分割とクロス検証
titleSuffix: Azure Machine Learning
description: 自動機械学習の実験のためにデータセットの分割とクロス検証を構成する方法について説明します
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, automl
ms.author: cesardl
author: CESARDELATORRE
ms.reviewer: nibaccam
ms.date: 02/23/2021
ms.openlocfilehash: 31d3dc2c2d8194541ba1fe7d0865e6c939d75f73
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501584"
---
# <a name="configure-data-splits-and-cross-validation-in-automated-machine-learning"></a>自動機械学習の実験でデータの分割とクロス検証を構成する

この記事では、自動機械学習 (自動 ML) の実験のために、トレーニング データと検証データの分割をクロス検証設定とあわせて構成するためのさまざまなオプションについて説明します。

Azure Machine Learning では、自動 ML を使用して複数の ML モデルを構築する場合、各子実行でそのモデルの品質メトリック (精度や加重 AUC など) を計算することによって関連モデルを検証する必要があります。 これらのメトリックは、各モデルで行われた予測を、検証データの過去の観測からの実際のラベルと比較することによって計算されます。 [検証の種類に基づいたメトリックの計算方法の詳細については、こちらを参照してください。](#metric-calculation-for-cross-validation-in-machine-learning) 

自動 ML の実験によって、モデルの検証が自動的に実行されます。 次のセクションでは、[Azure Machine Learning Python SDK](/python/api/overview/azure/ml/) で検証の設定をカスタマイズする方法について説明します。 

コードを使用しないエクスペリエンスの詳細については、[Azure Machine Learning Studio での自動機械学習の実験の作成](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment)に関するページを参照してください。 

> [!NOTE]
> 現時点でスタジオでは、トレーニングと検証データの分割とクロス検証オプションの両方がサポートされていますが、検証セットに個別のデータ ファイルを指定することはできません。 

## <a name="prerequisites"></a>前提条件

この記事では、以下が必要です。

* Azure Machine Learning ワークスペース。 ワークスペースを作成するには、[Azure Machine Learning ワークスペース](how-to-manage-workspace.md)の作成に関するページを参照してください。

* Azure Machine Learning SDK を使用した自動機械学習の実験の設定に関する知識。 [チュートリアル](tutorial-auto-train-models.md)または[方法](how-to-configure-auto-train.md)に従って、自動化された機械学習実験の基本的な設計パターンについて確認してください。

* 機械学習の概念としてのトレーニングと検証データの分割およびクロス検証に関する知識。 概要については、以下を参照してください。

    * [機械学習におけるトレーニング、検証、およびテスト データについて](https://towardsdatascience.com/train-validation-and-test-sets-72cb40cba9e7)

    * [機械学習でのクロス検証について](https://towardsdatascience.com/understanding-cross-validation-419dbd47e9bd) 

## <a name="default-data-splits-and-cross-validation-in-machine-learning"></a>機械学習での既定のデータ分割とクロス検証

[AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) オブジェクトを使用して、実験とトレーニングの設定を定義します。 次のコード スニペットでは、必須パラメーターのみが定義されていることに注意してください。つまり、`n_cross_validation` または `validation_ data` のパラメーターは **含まれていません**。

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

`validation_data` または `n_cross_validation` のいずれかのパラメーターを明示的に指定しない場合は、1 つのデータセット `training_data` で指定された行数に応じて、自動 ML によって既定の方法が適用されます。

|トレーニング&nbsp;データ&nbsp;のサイズ| 検証の方法 |
|---|-----|
|**20,000&nbsp;行&nbsp;より&nbsp;多い**| トレーニング/検証データの分割が適用されます。 既定では、初期トレーニング データ セットの 10% が検証セットとして取得されます。 次に、その検証セットがメトリックの計算に使用されます。
|**20,000&nbsp;行&nbsp;より&nbsp;少ない**| クロス検証アプローチが適用されます。 フォールドの既定の数は行数によって異なります。 <br> **データセットが 1,000 行より少ない場合は**、10 個のフォールドが使用されます。 <br> **行が 1,000 から 20,000 の間の場合は**、3 つのフォールドが使用されます。

## <a name="provide-validation-data"></a>検証データを指定する

この場合は、1 つのデータ ファイルから始めて、トレーニング データと検証データ セットに分割するか、検証セット用に個別のデータ ファイルを指定することができます。 どちらの方法でも、`AutoMLConfig` オブジェクトの `validation_data` パラメーターによって、検証セットとして使用するデータが割り当てられます。 このパラメーターは、[Azure Machine Learning データセット](how-to-create-register-datasets.md)または Pandas データフレームの形式のデータセットのみを受け入れます。   

> [!NOTE]
> `validation_size` パラメーターは予測のシナリオではサポートされていません。

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

この場合、実験には 1 つのデータセットのみが指定されています。 つまり、`validation_data` パラメーターは **指定されず**、指定されたデータセットは `training_data` パラメーターに割り当てられます。  

`AutoMLConfig` オブジェクトでは、検証用にトレーニング データの一部を提示するように `validation_size` パラメーターを設定できます。 これは、検証セットが、指定された初期の `training_data` から自動 ML によって分割されることを意味します。 この値は 0.0 より大きく 1.0 未満である必要があります (たとえば、0.2 は、データの 20% が検証データ用に提示されることを意味します)。

> [!NOTE]
> `validation_size` パラメーターは予測のシナリオではサポートされていません。 

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

## <a name="k-fold-cross-validation"></a>K フォールド クロス検証

k 分割交差検証を実行するには、`n_cross_validations` パラメーターを含めて、それを値に設定します。 このパラメーターは、同じフォールド数に基づいて、実行するクロス検証の回数を設定します。

> [!NOTE]
> `n_cross_validations` パラメーターは、ディープ ニューラル ネットワークを使用する分類のシナリオではサポートされていません。
 
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
## <a name="monte-carlo-cross-validation"></a>モンテカルロ クロス検証

モンテカルロ クロス検証を実行するには、`AutoMLConfig` オブジェクトに `validation_size` と `n_cross_validations` の両方のパラメーターを含めます。 

モンテカルロ クロス検証の場合、`validation_size` パラメーターで指定されたトレーニング データの一部が検証用に確保され、残りのデータがトレーニング用に割り当てられます。 このプロセスは `n_cross_validations` パラメーターに指定された値に基づいて繰り返されます。これにより、毎回、新しいトレーニングと検証の分割がランダムに生成されます。

> [!NOTE]
> モンテカルロ クロス検証は、予測のシナリオではサポートされていません。

次のコードでは、クロス検証用に 7 つのフォールドを使用し、検証用にトレーニング データの20% を使用することが定義されています。 そのため、7 つの異なるトレーニングがあり、各トレーニングには 80% のデータが使用され、各検証には、ホールドアウト フォールドが毎回異なる 20% のデータが使用されます。

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 7
                             validation_size = 0.2,
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


## <a name="metric-calculation-for-cross-validation-in-machine-learning"></a>機械学習でのクロス検証のメトリック計算

K 分割またはモンテカルロ クロス検証を使用すると、各検証のフォールドでメトリックが計算され、集計されます。 この集計操作は、スカラー メトリックの平均であり、グラフの合計です。 クロス検証中に計算されるメトリックは、すべてのフォールドに基づいているため、トレーニング セットのすべてのサンプルです。 [自動機械学習のメトリックの詳細については、こちらを参照してください。](how-to-understand-automated-ml.md)

カスタム検証セットまたは自動的に選択された検証セットを使用すると、モデルの評価メトリックは、トレーニング データではなく、その検証セットからのみ計算されます。

## <a name="next-steps"></a>次のステップ

* [不均衡データとオーバーフィットを防止します](concept-manage-ml-pitfalls.md)。
* [チュートリアル:自動機械学習を使用してタクシー料金を予測する - データの分割セクション](tutorial-auto-train-models.md#split-the-data-into-train-and-test-sets)。
* [時系列予測モデルを自動トレーニングする](how-to-auto-train-forecast.md)方法。
