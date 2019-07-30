---
title: '書き込み: Data Prep Python SDK'
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning Data Prep SDK を使用してデータを書き込む方法について説明します。 データ フローの任意の時点でデータを書き込むことができます。また、サポートされている任意の場所 (ローカル ファイル システム、Azure Blob Storage、Azure Data Lake Storage) のファイルに書き込むことができます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: jmartens
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 6753be5613b10b64936cddaafbb9859aad837b02
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358626"
---
# <a name="write-and-configure-data--with-the-azure-machine-learning-data-prep-sdk"></a>Azure Machine Learning Data Prep SDK でデータの書き込みと構成を行う

この記事では、[Azure Machine Learning Data Prep Python SDK](https://aka.ms/data-prep-sdk) を使用してデータを書き込むさまざまな方法と、そのデータを、[Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) を使用した実験用に構成する方法について説明します。  出力データは、データ フローのどの時点でも書き込むことができます。 書き込みは、結果のデータ フローへのステップとして追加され、これらのステップはデータ フローが実行されるたびに実行されます。 データは複数のパーティション ファイルに書き込まれ、並列書き込みが可能です。

> [!Important]
> 新しいソリューションを構築する場合は、データの変換、データのスナップショットの作成、およびバージョン管理されたデータセット定義の格納に [Azure Machine Learning Datasets](how-to-explore-prepare-data.md) (プレビュー) をお試しください。 Datasets は、次のバージョンのデータ準備 SDK であり、AI ソリューションでデータセットを管理するための拡張機能が提供されます。
> `azureml-datasets` パッケージを使用してデータセットを作成するのではなく、`azureml-dataprep` パッケージを使用し、変換を使用してデータフローを作成した場合、後でスナップショットまたはバージョン管理されたデータセットを使用することはできません。

パイプライン内に存在する書き込みのステップ数に制限はないため、書き込みステップを簡単に追加して、トラブルシューティングやその他のパイプライン用に中間結果を取得することができます。

書き込みステップを実行するたびに、データ フローの全データのプルが実行されます。 たとえば、3 つの書き込みステップがあるデータ フローの場合、データ セット内の各レコードの読み取りと処理が 3 回行われます。

## <a name="supported-data-types-and-location"></a>サポートされているデータ型と場所

次のファイル形式がサポートされています
-   区切りファイル (CSV や TSV など)
-   Parquet ファイル

Azure Machine Learning Data Prep Python SDK を使用して、次の場所にデータを書き込むことができます。
+ ローカル ファイル システム
+ Azure Blob Storage
+ Azure Data Lake Storage

## <a name="spark-considerations"></a>Spark に関する考慮事項

Spark でデータ フローを実行する場合は、空のフォルダーに書き込む必要があります。 既存フォルダーへの書き込みを実行しようとすると失敗します。 ターゲット フォルダーが空であることを確認するか、実行ごとに別のターゲットの場所を使用してください。そうしないと、書き込みは失敗します。

## <a name="monitoring-write-operations"></a>書き込み操作の監視

作業を容易にするために、書き込みが完了すると、SUCCESS という名前のセンチネル ファイルが生成されます。 このファイルにより、パイプライン全体が完了するのを待つことなく、中間書き込みが完了したときを特定できます。

## <a name="example-write-code"></a>書き込みコードの例

この例では、`auto_read_file()` を使用してまずデータ フローにデータを読み込みます。 このデータをさまざまな形式で再利用します。

```python
import azureml.dataprep as dprep
t = dprep.auto_read_file('./data/fixed_width_file.txt')
t = t.to_number('Column3')
t.head(5)
```

出力例:

| | Column1 | Column2 | Column3 | Column4 | Column5 | Column6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000.0 | 99999.0 | なし | NO | NO | ENRS | NaN | NaN | (NaN) |   
|1| 10003.0 | 99999.0 | なし | NO | NO | ENSO | NaN | NaN | (NaN) |   
|2| 10010.0 | 99999.0 | なし | NO | JN | ENJA | 70933.0 | -8667.0 | 90.0 |
|3| 10013.0 | 99999.0 | なし | NO | NO |      | (NaN) | NaN | (NaN) |
|4| 10014.0 | 99999.0 | なし | NO | NO | ENSO | 59783.0 | 5350.0 |  500.0|

### <a name="delimited-file-example"></a>区切りファイルの例

次のコードは、区切り記号入りファイルにデータを書き込むために [`write_to_csv()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#write-to-csv-directory-path--datadestination--separator--str--------na--str----na---error--str----error------azureml-dataprep-api-dataflow-dataflow) 関数を使用します。

```python
# Create a new data flow using `write_to_csv`
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'))

# Run the data flow to begin the write operation.
write_t.run_local()

written_files = dprep.read_csv('./test_out/part-*')
written_files.head(5)
```

出力例:

| | Column1 | Column2 | Column3 | Column4 | Column5 | Column6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000.0 | 99999.0 | ERROR | NO | NO | ENRS | NaN    | NaN | (NaN) |   
|1| 10003.0 | 99999.0 | ERROR | NO | NO | ENSO |    NaN | NaN | (NaN) |   
|2| 10010.0 | 99999.0 | ERROR | NO | JN | ENJA |    70933.0 | -8667.0 | 90.0 |
|3| 10013.0 | 99999.0 | ERROR | NO | NO |     | (NaN) | NaN | (NaN) |
|4| 10014.0 | 99999.0 | ERROR | NO | NO | ENSO |    59783.0 | 5350.0 |  500.0|

上記の出力では、正しく解析されなかった数値があるため、数値列にエラーがいくつか表示されます。 CSV に書き込まれると、既定でこれらの null 値は "ERROR" という文字列に置き換えられます。

書き込みの呼び出しの一部としてパラメーターを追加し、null 値を表すために使用する文字列を指定することができます。

```python
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'),
                         error='BadData',
                         na='NA')
write_t.run_local()
written_files = dprep.read_csv('./test_out/part-*')
written_files.head(5)
```

上記のコードでは、次の出力が生成されます。

| | Column1 | Column2 | Column3 | Column4 | Column5 | Column6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000.0 | 99999.0 | BadData | NO | NO | ENRS | NaN  | NaN | (NaN) |   
|1| 10003.0 | 99999.0 | BadData | NO | NO | ENSO |  NaN | NaN | (NaN) |   
|2| 10010.0 | 99999.0 | BadData | NO | JN | ENJA |  70933.0 | -8667.0 | 90.0 |
|3| 10013.0 | 99999.0 | BadData | NO | NO |   | (NaN) | NaN | (NaN) |
|4| 10014.0 | 99999.0 | BadData | NO | NO | ENSO |  59783.0 | 5350.0 |  500.0|

### <a name="parquet-file-example"></a>Parquet ファイルの例

`write_to_csv()` と同様に、[`write_to_parquet()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#write-to-parquet-file-path--typing-union--datadestination--nonetype----none--directory-path--typing-union--datadestination--nonetype----none--single-file--bool---false--error--str----error---row-groups--int---0-----azureml-dataprep-api-dataflow-dataflow) 関数では、データ フローの実行時に実行される書き込みの Parquet ステップを含む新しいデータ フローが返されます。

```python
write_parquet_t = t.write_to_parquet(directory_path=dprep.LocalFileOutput('./test_parquet_out/'),
                                     error='MiscreantData')
```

データ フローを実行して書き込み操作を実行します。

```python
write_parquet_t.run_local()

written_parquet_files = dprep.read_parquet_file('./test_parquet_out/part-*')
written_parquet_files.head(5)
```

上記のコードでは、次の出力が生成されます。

|   | Column1 | Column2 | Column3 | Column4 | Column5 | Column6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |-------- |
|0| 10000.0 | 99999.0 | MiscreantData | NO | NO | ENRS | MiscreantData | MiscreantData | MiscreantData |
|1| 10003.0 | 99999.0 | MiscreantData | NO | NO | ENSO | MiscreantData | MiscreantData | MiscreantData |   
|2| 10010.0 | 99999.0 | MiscreantData | NO| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0 | 99999.0 | MiscreantData | NO| NO| |   MiscreantData|    MiscreantData|    MiscreantData|
|4| 10014.0 | 99999.0 | MiscreantData | NO| NO| ENSO|   59783.0|    5350.0| 500.0|

## <a name="configure-data-for-automated-machine-learning-training"></a>自動機械学習トレーニング用のデータを構成する

自動機械学習トレーニングへの準備として、新しく書き込まれたデータ ファイルを [`AutoMLConfig`](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#automlconfig) オブジェクトに渡します。 

次のコード例では、データフローを Pandas データフレームに変換してから、自動機械学習トレーニングのためにトレーニングとテスト データセットに分割する方法を示します。

```Python
from azureml.train.automl import AutoMLConfig
from sklearn.model_selection import train_test_split

dflow = dprep.auto_read_file(path="")
X_dflow = dflow.keep_columns([feature_1,feature_2, feature_3])
y_dflow = dflow.keep_columns("target")

X_df = X_dflow.to_pandas_dataframe()
y_df = y_dflow.to_pandas_dataframe()

X_train, X_test, y_train, y_test = train_test_split(X_df, y_df, test_size=0.2, random_state=223)

# flatten y_train to 1d array
y_train.values.flatten()

#configure 
automated_ml_config = AutoMLConfig(task = 'regression',
                               X = X_train.values,  
                   y = y_train.values.flatten(),
                   iterations = 30,
                       Primary_metric = "AUC_weighted",
                       n_cross_validation = 5
                       )

```

前の例のようなデータ準備の中間ステップが不要な場合は、データフローを `AutoMLConfig` に直接渡すことができます。

```Python
automated_ml_config = AutoMLConfig(task = 'regression', 
                   X = X_dflow,   
                   y = y_dflow, 
                   iterations = 30, 
                   Primary_metric = "AUC_weighted",
                   n_cross_validation = 5
                   )
```

## <a name="next-steps"></a>次の手順
* 設計パターンと使用例については、SDK の[概要](https://aka.ms/data-prep-sdk)を参照してください 
* 回帰モデルの例については、自動機械学習の[チュートリアル](tutorial-auto-train-models.md)を参照してください
