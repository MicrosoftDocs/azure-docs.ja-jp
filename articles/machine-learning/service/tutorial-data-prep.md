---
title: '回帰モデルのチュートリアル: データを準備する'
titleSuffix: Azure Machine Learning service
description: このチュートリアルの最初の部分では、Azure Machine Learning SDK を使用して回帰モデリングのために Python でデータを準備する方法を説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: MayMSFT
ms.author: sihhu
ms.reviewer: trbye
ms.date: 07/16/2019
ms.custom: seodec18
ms.openlocfilehash: 6692f64dc7e7fa2799f9095af39171a2ddc0e76d
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360906"
---
# <a name="tutorial-prepare-data-for-regression-modeling"></a>チュートリアル:回帰モデリングのためにデータを準備する

このチュートリアルでは、[Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) からの[データ準備パッケージ](https://aka.ms/data-prep-sdk)を使用して、回帰モデリングのためのデータを準備する方法について説明します。 さまざまな変換を実行して、2 つの異なる NYC タクシー データ セットをフィルター処理して結合します。

このチュートリアルは、**2 部構成のチュートリアル シリーズのパート 1 です**。 このチュートリアル シリーズを完了すると、データの特徴についてモデルをトレーニングして、タクシー移動のコストを予測できます。 これらの特徴には、乗車日時、乗客数、乗車場所が含まれます。

このチュートリアルでは、次のことを行いました。

> [!div class="checklist"]
> * Python 環境を設定してパッケージをインポートする。
> * フィールド名が異なる 2 つのデータセットを読み込む。
> * データをクレンジングして異常を除く。
> * インテリジェント変換を使用してデータを変換し、新しい特徴を作成する。
> * 回帰モデルで使用するためにデータフロー オブジェクトを保存する。

## <a name="prerequisites"></a>前提条件

「[開発環境を設定する](#start)」にスキップしてノートブックの手順を読むか、以下の手順に従ってノートブックを入手し、Azure Notebooks または独自のノートブック サーバーで実行します。 ノートブックを実行するには、以下のものが必要です。

* 以下のものがインストールされている Python 3.6 ノートブック サーバー。
    * Azure Machine Learning SDK からの `azureml-dataprep` パッケージ
* チュートリアル ノートブック

* [ワークスペース内のクラウド ノートブック サーバー](#azure)を使用する 
* [独自のノートブック サーバー](#server)を使用する

### <a name="azure"></a>ワークスペース内のクラウド ノートブックサーバーを使用する

独自のクラウドベースのノートブック サーバーで簡単に開始できます。 このクラウド リソースを作成すると、Azure Machine Learning SDK for Python が既にインストールされて構成されています。

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

* ノートブックの Web ページを起動したら、**tutorials/regression-part1-data-prep.ipynb** ノートブックを実行します。

### <a name="server"></a>独自の Jupyter Notebook サーバーを使用する

次の手順を使用して、コンピューターにローカルの Jupyter Notebook サーバーを作成します。  手順を完了したら、**tutorials/regression-part1-data-prep.ipynb** ノートブックを実行します。

1. [Azure Machine Learning Python のクイック スタート](setup-create-workspace.md#sdk)にあるインストール手順を完了して、Miniconda 環境を作成し、SDK をインストールします。  「**ワークスペースを作成する**」セクションはスキップしてもかまいませんが、このチュートリアル シリーズの[パート 2](tutorial-auto-train-models.md) で必要になります。
1. SDK をインストールすると、`azureml-dataprep` パッケージが自動的にインストールされます。
1. [GitHub リポジトリ](https://aka.ms/aml-notebooks)を複製します。

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. 複製したディレクトリから、Notebook サーバーを起動します。

    ```shell
    jupyter notebook
    ```

## <a name="start"></a>開発環境を設定する

Python Notebook で、開発作業に関するすべての設定を行うことができます。 設定の一環として次のことを行います。

* SDK のインストール
* Python パッケージをインポートする

### <a name="install-and-import-packages"></a>パッケージをインストールしてインポートする

必要なパッケージがまだない場合は、次のコマンドを使用してそれらをインストールします。

```shell
pip install "azureml-dataprep[pandas]>=1.1.0,<1.2.0"
```

パッケージをインポートします。

```python
import azureml.dataprep as dprep
```

> [!IMPORTANT]
> 必ず、最新の azureml.dataprep パッケージ バージョンをインストールしてください。 バージョン番号が 1.1.0 より低いと、このチュートリアルは正しく機能しません。

## <a name="load-data"></a>データを読み込む

2 つの異なる NYC タクシー データ セットをデータフロー オブジェクトにダウンロードします。 これらのデータセットには、若干異なるフィールドがあります。 `auto_read_file()` メソッドによって、入力ファイルの種類が自動的に認識されます。

```python
from IPython.display import display
dataset_root = "https://dprepdata.blob.core.windows.net/demo"

green_path = "/".join([dataset_root, "green-small/*"])
yellow_path = "/".join([dataset_root, "yellow-small/*"])

green_df_raw = dprep.read_csv(
    path=green_path, header=dprep.PromoteHeadersMode.GROUPED)
# auto_read_file automatically identifies and parses the file type, which is useful when you don't know the file type.
yellow_df_raw = dprep.auto_read_file(path=yellow_path)

display(green_df_raw.head(5))
display(yellow_df_raw.head(5))
```

> [!Note]
> この同じ例の URL は完全な URL ではありません。 代わりに、BLOB の demo フォルダーが参照されます。 一部のデータの完全な URL は、 https://dprepdata.blob.core.windows.net/demo/green-small/green_tripdata_2013-08.csv です

`Dataflow` オブジェクトはデータフレームに類似しており、データに対して遅延評価される一連の不変の操作を表します。 操作は、異なる変換を呼び出して、利用可能なメソッドをフィルター処理することで追加できます。 `Dataflow` に対する操作の追加の結果は常に、新しい `Dataflow` オブジェクトです。

## <a name="cleanse-data"></a>データをクレンジングする

ここでは、すべてのデータフローに適用されるショートカット変換をいくつかの変数に設定します。 `drop_if_all_null` 変数は、すべてのフィールドが null のレコードを削除するために使用されます。 `useful_columns` 変数は、各データフローに保持されている列の説明の配列を保持します。

```python
all_columns = dprep.ColumnSelector(term=".*", use_regex=True)
drop_if_all_null = [all_columns, dprep.ColumnRelationship(
    dprep.ColumnRelationship.ALL)]
useful_columns = [
    "cost", "distance", "dropoff_datetime", "dropoff_latitude", "dropoff_longitude",
    "passengers", "pickup_datetime", "pickup_latitude", "pickup_longitude", "store_forward", "vendor"
]
```

まず最初にグリーン タクシーのデータを処理して、イエロー タクシーのデータと結合できる有効な形式にします。 作成したショートカット変換変数を使用して、`replace_na()`、`drop_nulls()`、`keep_columns()` 関数を呼び出します。 さらに、データフレーム内のすべての列の名前を、`useful_columns` 変数の名前と一致するように変更します。


```python
green_df = (green_df_raw
            .replace_na(columns=all_columns)
            .drop_nulls(*drop_if_all_null)
            .rename_columns(column_pairs={
                "VendorID": "vendor",
                "lpep_pickup_datetime": "pickup_datetime",
                "Lpep_dropoff_datetime": "dropoff_datetime",
                "lpep_dropoff_datetime": "dropoff_datetime",
                "Store_and_fwd_flag": "store_forward",
                "store_and_fwd_flag": "store_forward",
                "Pickup_longitude": "pickup_longitude",
                "Pickup_latitude": "pickup_latitude",
                "Dropoff_longitude": "dropoff_longitude",
                "Dropoff_latitude": "dropoff_latitude",
                "Passenger_count": "passengers",
                "Fare_amount": "cost",
                "Trip_distance": "distance"
            })
            .keep_columns(columns=useful_columns))
green_df.head(5)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendor</th>
      <th>pickup_datetime</th>
      <th>dropoff_datetime</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>passengers</th>
      <th>distance</th>
      <th>cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2013-08-01 08:14:37</td>
      <td>2013-08-01 09:09:06</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>21.25</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2013-08-01 09:13:00</td>
      <td>2013-08-01 11:38:00</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>.00</td>
      <td>74.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2013-08-01 09:48:00</td>
      <td>2013-08-01 09:49:00</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2013-08-01 10:38:35</td>
      <td>2013-08-01 10:38:51</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>3.25</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2013-08-01 11:51:45</td>
      <td>2013-08-01 12:03:52</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>8.5</td>
    </tr>
  </tbody>
</table>
</div>

イエロー タクシーのデータで、同じ変換手順を実行します。 これらの関数によって、データ セットから null データが確実に削除されます。これにより、機械学習モデルの精度が向上します。

```python
yellow_df = (yellow_df_raw
             .replace_na(columns=all_columns)
             .drop_nulls(*drop_if_all_null)
             .rename_columns(column_pairs={
                 "vendor_name": "vendor",
                 "VendorID": "vendor",
                 "vendor_id": "vendor",
                 "Trip_Pickup_DateTime": "pickup_datetime",
                 "tpep_pickup_datetime": "pickup_datetime",
                 "Trip_Dropoff_DateTime": "dropoff_datetime",
                 "tpep_dropoff_datetime": "dropoff_datetime",
                 "store_and_forward": "store_forward",
                 "store_and_fwd_flag": "store_forward",
                 "Start_Lon": "pickup_longitude",
                 "Start_Lat": "pickup_latitude",
                 "End_Lon": "dropoff_longitude",
                 "End_Lat": "dropoff_latitude",
                 "Passenger_Count": "passengers",
                 "passenger_count": "passengers",
                 "Fare_Amt": "cost",
                 "fare_amount": "cost",
                 "Trip_Distance": "distance",
                 "trip_distance": "distance"
             })
             .keep_columns(columns=useful_columns))
yellow_df.head(5)
```

グリーン タクシーのデータで `append_rows()` 関数を呼び出して、イエロー タクシーのデータを追加します。 新しく結合されたデータフレームが作成されます。

```python
combined_df = green_df.append_rows([yellow_df])
```

### <a name="convert-types-and-filter"></a>型を変換してフィルター処理する

乗車と降車の座標に関する概要の統計を調べて、データがどのように分散しているかを確認します。 まず、`TypeConverter` オブジェクトを定義して、緯度と経度のフィールドを 10 進型に変更します。 次に、`keep_columns()` 関数を呼び出して、緯度と経度のフィールドのみに出力を制限し、`get_profile()` 関数を呼び出します。 これらの関数呼び出しによって、緯度と経度のフィールドのみを示す、データフローの圧縮されたビューが作成されます。これにより、欠落している座標または範囲外の座標を評価するのが容易になります。


```python
decimal_type = dprep.TypeConverter(data_type=dprep.FieldType.DECIMAL)
combined_df = combined_df.set_column_types(type_conversions={
    "pickup_longitude": decimal_type,
    "pickup_latitude": decimal_type,
    "dropoff_longitude": decimal_type,
    "dropoff_latitude": decimal_type
})
combined_df.keep_columns(columns=[
    "pickup_longitude", "pickup_latitude",
    "dropoff_longitude", "dropoff_latitude"
]).get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Type</th>
      <th>Min</th>
      <th>max</th>
      <th>Count</th>
      <th>Missing Count</th>
      <th>Not Missing Count</th>
      <th>Percent missing</th>
      <th>Error Count</th>
      <th>Empty count</th>
      <th>0.1% Quantile</th>
      <th>1% Quantile</th>
      <th>5% Quantile</th>
      <th>25% Quantile</th>
      <th>50% Quantile</th>
      <th>75% Quantile</th>
      <th>95% Quantile</th>
      <th>99% Quantile</th>
      <th>99.9% Quantile</th>
      <th>標準偏差</th>
      <th>Mean</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-115.179337</td>
      <td>0.000000</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-88.114046</td>
      <td>-73.961840</td>
      <td>-73.961964</td>
      <td>-73.947693</td>
      <td>-73.922097</td>
      <td>-73.846670</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>18.792672</td>
      <td>-68.833579</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>0.000000</td>
      <td>40.919121</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>40.682889</td>
      <td>40.675541</td>
      <td>40.721075</td>
      <td>40.756159</td>
      <td>40.803909</td>
      <td>40.849406</td>
      <td>40.870681</td>
      <td>40.891244</td>
      <td>10.345967</td>
      <td>37.936742</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-115.179337</td>
      <td>0.000000</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-87.699611</td>
      <td>-73.984734</td>
      <td>-73.985777</td>
      <td>-73.956250</td>
      <td>-73.928948</td>
      <td>-73.866208</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>18.696526</td>
      <td>-68.896978</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>0.000000</td>
      <td>41.008934</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>40.662763</td>
      <td>40.654851</td>
      <td>40.717821</td>
      <td>40.756534</td>
      <td>40.784688</td>
      <td>40.852437</td>
      <td>40.879289</td>
      <td>40.937291</td>
      <td>10.290780</td>
      <td>37.963774</td>
    </tr>
  </tbody>
</table>



概要の統計出力から、欠落している座標やニューヨーク市に存在しない座標があることがわかります (これは主観的な分析により判断されます)。 市外の場所を指す座標をフィルターで除外します。 列のフィルターのコマンドを `filter()` 関数内で組み合わせ、各フィールドの下限と上限を定義します。 その後、もう一度 `get_profile()` 関数を呼び出して、変換を確認します。


```python
latlong_filtered_df = (combined_df
                       .drop_nulls(
                           columns=["pickup_longitude", "pickup_latitude",
                                    "dropoff_longitude", "dropoff_latitude"],
                           column_relationship=dprep.ColumnRelationship(
                               dprep.ColumnRelationship.ANY)
                       )
                       .filter(dprep.f_and(
                           dprep.col("pickup_longitude") <= -73.72,
                           dprep.col("pickup_longitude") >= -74.09,
                           dprep.col("pickup_latitude") <= 40.88,
                           dprep.col("pickup_latitude") >= 40.53,
                           dprep.col("dropoff_longitude") <= -73.72,
                           dprep.col("dropoff_longitude") >= -74.09,
                           dprep.col("dropoff_latitude") <= 40.88,
                           dprep.col("dropoff_latitude") >= 40.53
                       )))
latlong_filtered_df.keep_columns(columns=[
    "pickup_longitude", "pickup_latitude",
    "dropoff_longitude", "dropoff_latitude"
]).get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Type</th>
      <th>Min</th>
      <th>max</th>
      <th>Count</th>
      <th>Missing Count</th>
      <th>Not Missing Count</th>
      <th>Percent missing</th>
      <th>Error Count</th>
      <th>Empty count</th>
      <th>0.1% Quantile</th>
      <th>1% Quantile</th>
      <th>5% Quantile</th>
      <th>25% Quantile</th>
      <th>50% Quantile</th>
      <th>75% Quantile</th>
      <th>95% Quantile</th>
      <th>99% Quantile</th>
      <th>99.9% Quantile</th>
      <th>標準偏差</th>
      <th>Mean</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.078156</td>
      <td>-73.736481</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.076314</td>
      <td>-73.962542</td>
      <td>-73.962893</td>
      <td>-73.948975</td>
      <td>-73.927856</td>
      <td>-73.866662</td>
      <td>-73.830438</td>
      <td>-73.823160</td>
      <td>-73.769750</td>
      <td>0.048711</td>
      <td>-73.913865</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.575485</td>
      <td>40.879852</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.632884</td>
      <td>40.713105</td>
      <td>40.711600</td>
      <td>40.721403</td>
      <td>40.758142</td>
      <td>40.805145</td>
      <td>40.848855</td>
      <td>40.867567</td>
      <td>40.877690</td>
      <td>0.048348</td>
      <td>40.765226</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.085747</td>
      <td>-73.720871</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.078828</td>
      <td>-73.985650</td>
      <td>-73.985813</td>
      <td>-73.959041</td>
      <td>-73.936681</td>
      <td>-73.884846</td>
      <td>-73.815507</td>
      <td>-73.776697</td>
      <td>-73.733471</td>
      <td>0.055961</td>
      <td>-73.920718</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.583530</td>
      <td>40.879734</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.597741</td>
      <td>40.695376</td>
      <td>40.695115</td>
      <td>40.727549</td>
      <td>40.758160</td>
      <td>40.788378</td>
      <td>40.850372</td>
      <td>40.867968</td>
      <td>40.878586</td>
      <td>0.050462</td>
      <td>40.759487</td>
    </tr>
  </tbody>
</table>

### <a name="split-and-rename-columns"></a>列を分割して名前を変更する

`store_forward` 列のデータ プロファイルを見てください。 このフィールドは、タクシーが移動後にサーバーに接続できなかったことが理由で移動データがメモリに格納され、後で接続されたときにそれがサーバーに転送された場合に `Y` になるブール値フラグです。


```python
latlong_filtered_df.keep_columns(columns='store_forward').get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Type</th>
      <th>Min</th>
      <th>max</th>
      <th>Count</th>
      <th>Missing Count</th>
      <th>Not Missing Count</th>
      <th>Percent missing</th>
      <th>Error Count</th>
      <th>Empty count</th>
      <th>0.1% Quantile</th>
      <th>1% Quantile</th>
      <th>5% Quantile</th>
      <th>25% Quantile</th>
      <th>50% Quantile</th>
      <th>75% Quantile</th>
      <th>95% Quantile</th>
      <th>99% Quantile</th>
      <th>99.9% Quantile</th>
      <th>標準偏差</th>
      <th>Mean</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>store_forward</th>
      <td>FieldType.STRING</td>
      <td>N</td>
      <td>Y</td>
      <td>7059.0</td>
      <td>99.0</td>
      <td>6960.0</td>
      <td>0.014025</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>



`store_forward` 列のデータ プロファイル出力では、データの整合性がなく、欠落値や null 値があることが示されていることに注意してください。 `replace()` 関数と `fill_nulls()` 関数を使用して、これらの値を文字列 "N" に置換します。


```python
replaced_stfor_vals_df = latlong_filtered_df.replace(
    columns="store_forward", find="0", replace_with="N").fill_nulls("store_forward", "N")
```

`distance` フィールドに対して `replace` 関数を実行します。 この関数により、間違って `.00` と示されている距離の値の書式が変更されるほか、null 値があればゼロで埋められます。 `distance` フィールドは数値形式に変換します。 これらの不適切なデータ ポイントは、タクシーのデータ収集システム内の異常であると考えられます。


```python
replaced_distance_vals_df = replaced_stfor_vals_df.replace(
    columns="distance", find=".00", replace_with=0).fill_nulls("distance", 0)
replaced_distance_vals_df = replaced_distance_vals_df.to_number(["distance"])
```

乗車と降車の日時の値を、それぞれの日付と時刻の列に分割します。 分割を行うには `split_column_by_example()` 関数を使用します。 このケースでは、`split_column_by_example()` 関数の省略可能な `example` パラメーターが省略されます。 そのため、この関数では、分割位置がデータに基づいて自動的に判別されます。


```python
time_split_df = (replaced_distance_vals_df
                 .split_column_by_example(source_column="pickup_datetime")
                 .split_column_by_example(source_column="dropoff_datetime"))
time_split_df.head(5)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendor</th>
      <th>pickup_datetime</th>
      <th>pickup_datetime_1</th>
      <th>pickup_datetime_2</th>
      <th>dropoff_datetime</th>
      <th>dropoff_datetime_1</th>
      <th>dropoff_datetime_2</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>passengers</th>
      <th>distance</th>
      <th>cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2013-08-01 17:22:00</td>
      <td>2013-08-01</td>
      <td>17:22:00</td>
      <td>2013-08-01 17:22:00</td>
      <td>2013-08-01</td>
      <td>17:22:00</td>
      <td>N</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2013-08-01 17:24:00</td>
      <td>2013-08-01</td>
      <td>17:24:00</td>
      <td>2013-08-01 17:25:00</td>
      <td>2013-08-01</td>
      <td>17:25:00</td>
      <td>N</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2013-08-06 06:51:19</td>
      <td>2013-08-06</td>
      <td>06:51:19</td>
      <td>2013-08-06 06:51:36</td>
      <td>2013-08-06</td>
      <td>06:51:36</td>
      <td>N</td>
      <td>-73.937721</td>
      <td>40.758404</td>
      <td>-73.937721</td>
      <td>40.758369</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2013-08-06 13:26:34</td>
      <td>2013-08-06</td>
      <td>13:26:34</td>
      <td>2013-08-06 13:26:57</td>
      <td>2013-08-06</td>
      <td>13:26:57</td>
      <td>N</td>
      <td>-73.937691</td>
      <td>40.758419</td>
      <td>-73.937790</td>
      <td>40.758358</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2013-08-06 13:27:53</td>
      <td>2013-08-06</td>
      <td>13:27:53</td>
      <td>2013-08-06 13:28:08</td>
      <td>2013-08-06</td>
      <td>13:28:08</td>
      <td>N</td>
      <td>-73.937805</td>
      <td>40.758396</td>
      <td>-73.937775</td>
      <td>40.758450</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
  </tbody>
</table>
</div>

`split_column_by_example()` 関数によって生成された列名を、わかりやすい名前に変更します。

```python
renamed_col_df = (time_split_df
                  .rename_columns(column_pairs={
                      "pickup_datetime_1": "pickup_date",
                      "pickup_datetime_2": "pickup_time",
                      "dropoff_datetime_1": "dropoff_date",
                      "dropoff_datetime_2": "dropoff_time"
                  }))
renamed_col_df.head(5)
```

`get_profile()` 関数を呼び出して、すべてのクレンジング手順後の概要の統計情報全体を表示します。

```python
renamed_col_df.get_profile()
```

## <a name="transform-data"></a>データを変換する

乗車と降車の日付をさらに曜日、日、月の値に分割します。 曜日の値を取得するには、`derive_column_by_example()` 関数を使用します。 この関数は、入力データと優先される出力を定義するサンプル オブジェクトの配列のパラメーターを取得します。 この関数によって、優先される変換が自動的に判別されます。 乗車と降車の時刻の列については、example パラメーターは使用せずに `split_column_by_example()` 関数を使用して、時刻を時、分、秒に分割します。

新しい機能を生成した後は、新しく生成された機能が優先されるため、`drop_columns()` 関数を使用して元のフィールドを削除します。 残りのフィールド名を、わかりやすい説明に変更します。

この方法でデータを変換して新しい時間ベースの特徴を作成すると、機械学習モデルの精度が向上します。 たとえば、曜日について新しい特徴を生成することは、その曜日とタクシー料金の価格との間に関係を確立するのに役に立ちます。この価格は多くの場合、需要が多いことが理由で一定の曜日で高くなっています。


```python
transformed_features_df = (renamed_col_df
                           .derive_column_by_example(
                               source_columns="pickup_date",
                               new_column_name="pickup_weekday",
                               example_data=[
                                   ("2009-01-04", "Sunday"), ("2013-08-22", "Thursday")]
                           )
                           .derive_column_by_example(
                               source_columns="dropoff_date",
                               new_column_name="dropoff_weekday",
                               example_data=[
                                   ("2013-08-22", "Thursday"), ("2013-11-03", "Sunday")]
                           )

                           .split_column_by_example(source_column="pickup_time")
                           .split_column_by_example(source_column="dropoff_time")
                           # The following two calls to split_column_by_example reference the column names generated from the previous two calls.
                           .split_column_by_example(source_column="pickup_time_1")
                           .split_column_by_example(source_column="dropoff_time_1")
                           .drop_columns(columns=[
                               "pickup_date", "pickup_time", "dropoff_date", "dropoff_time",
                               "pickup_date_1", "dropoff_date_1", "pickup_time_1", "dropoff_time_1"
                           ])

                           .rename_columns(column_pairs={
                               "pickup_date_2": "pickup_month",
                               "pickup_date_3": "pickup_monthday",
                               "pickup_time_1_1": "pickup_hour",
                               "pickup_time_1_2": "pickup_minute",
                               "pickup_time_2": "pickup_second",
                               "dropoff_date_2": "dropoff_month",
                               "dropoff_date_3": "dropoff_monthday",
                               "dropoff_time_1_1": "dropoff_hour",
                               "dropoff_time_1_2": "dropoff_minute",
                               "dropoff_time_2": "dropoff_second"
                           }))

transformed_features_df.head(5)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendor</th>
      <th>pickup_datetime</th>
      <th>pickup_weekday</th>
      <th>pickup_hour</th>
      <th>pickup_minute</th>
      <th>pickup_second</th>
      <th>dropoff_datetime</th>
      <th>dropoff_weekday</th>
      <th>dropoff_hour</th>
      <th>dropoff_minute</th>
      <th>dropoff_second</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>passengers</th>
      <th>distance</th>
      <th>cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2013-08-01 17:22:00</td>
      <td>Thursday</td>
      <td>17</td>
      <td>22</td>
      <td>00</td>
      <td>2013-08-01 17:22:00</td>
      <td>Thursday</td>
      <td>17</td>
      <td>22</td>
      <td>00</td>
      <td>N</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2013-08-01 17:24:00</td>
      <td>Thursday</td>
      <td>17</td>
      <td>24</td>
      <td>00</td>
      <td>2013-08-01 17:25:00</td>
      <td>Thursday</td>
      <td>17</td>
      <td>25</td>
      <td>00</td>
      <td>N</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2013-08-06 06:51:19</td>
      <td>Tuesday</td>
      <td>06</td>
      <td>51</td>
      <td>19</td>
      <td>2013-08-06 06:51:36</td>
      <td>Tuesday</td>
      <td>06</td>
      <td>51</td>
      <td>36</td>
      <td>N</td>
      <td>-73.937721</td>
      <td>40.758404</td>
      <td>-73.937721</td>
      <td>40.758369</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2013-08-06 13:26:34</td>
      <td>Tuesday</td>
      <td>13</td>
      <td>26</td>
      <td>34</td>
      <td>2013-08-06 13:26:57</td>
      <td>Tuesday</td>
      <td>13</td>
      <td>26</td>
      <td>57</td>
      <td>N</td>
      <td>-73.937691</td>
      <td>40.758419</td>
      <td>-73.937790</td>
      <td>40.758358</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2013-08-06 13:27:53</td>
      <td>Tuesday</td>
      <td>13</td>
      <td>27</td>
      <td>53</td>
      <td>2013-08-06 13:28:08</td>
      <td>Tuesday</td>
      <td>13</td>
      <td>28</td>
      <td>08</td>
      <td>N</td>
      <td>-73.937805</td>
      <td>40.758396</td>
      <td>-73.937775</td>
      <td>40.758450</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
  </tbody>
</table>
</div>

このデータでは、派生した変換から生成された乗車と降車の日付と時刻の構成要素が正しいことが示されていることに注意してください。 `pickup_datetime` 列と `dropoff_datetime` 列は必要なくなったため削除します (時間、分、秒など、より細かい時間の特徴の方がモデルのトレーニングに役立ちます)。


```python
processed_df = transformed_features_df.drop_columns(
    columns=["pickup_datetime", "dropoff_datetime"])
```

型の推定機能を使用して、各フィールドのデータ型を自動的に調べ、推定結果を表示します。


```python
type_infer = processed_df.builders.set_column_types()
type_infer.learn()
type_infer
```

`type_infer` の結果の出力は次のようになります。

    Column types conversion candidates:
    'pickup_weekday': [FieldType.STRING],
    'pickup_hour': [FieldType.DECIMAL],
    'pickup_minute': [FieldType.DECIMAL],
    'pickup_second': [FieldType.DECIMAL],
    'dropoff_hour': [FieldType.DECIMAL],
    'dropoff_minute': [FieldType.DECIMAL],
    'dropoff_second': [FieldType.DECIMAL],
    'store_forward': [FieldType.STRING],
    'pickup_longitude': [FieldType.DECIMAL],
    'dropoff_longitude': [FieldType.DECIMAL],
    'passengers': [FieldType.DECIMAL],
    'distance': [FieldType.DECIMAL],
    'vendor': [FieldType.STRING],
    'dropoff_weekday': [FieldType.STRING],
    'pickup_latitude': [FieldType.DECIMAL],
    'dropoff_latitude': [FieldType.DECIMAL],
    'cost': [FieldType.DECIMAL]

推定結果は、データに基づいた正しい内容のようです。 ここで、データフローに型の変換を適用します。


```python
type_converted_df = type_infer.to_dataflow()
type_converted_df.get_profile()
```

データフローをパッケージ化する前に、データ セットに対して最終的なフィルターを 2 つ実行します。 不適切にキャプチャされたデータ ポイントを排除するために、`cost` 変数と `distance` 変数の値両方がゼロより大きいレコードという条件でデータフローをフィルター処理します。 コストまたは距離がゼロのデータ ポイントが、予測の精度を狂わせる主な外れ値を表すので、この手順によって、機械学習モデルの精度が大幅に向上します。

```python
final_df = type_converted_df.filter(dprep.col("distance") > 0)
final_df = final_df.filter(dprep.col("cost") > 0)
```

これで、データフロー オブジェクトが完全に変換され、機械学習モデルで使用するための準備が整いました。 SDK にはオブジェクトのシリアル化機能が含まれており、次のコードに示されるように使用されます。

```python
import os

file_path = os.path.join(os.getcwd(), "dflows.dprep")
final_df.save(file_path)
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

チュートリアルの第 2 部に進むには、現在のディレクトリにある **dflows.dprep** ファイルが必要です。

第 2 部に進む予定がない場合は、現在のディレクトリにある **dflows.dprep** ファイルを削除します。 実行場所がローカルの場合でも [Azure Notebooks](https://notebooks.azure.com/) の場合でも、このファイルは削除してください。

## <a name="next-steps"></a>次の手順

このチュートリアルの第 1 部では次を行いました。

> [!div class="checklist"]
> * 開発環境を設定する。
> * データ セットを読み込んでクレンジングする。
> * スマート変換を使用して例に基づいてロジックを予測する。
> * 機械学習トレーニング用にデータセットをマージしてパッケージ化する。

これで、チュートリアルの第 2 部でトレーニング データを使用する準備ができました。

> [!div class="nextstepaction"]
> [チュートリアル (第 2 部): 回帰モデルをトレーニングする](tutorial-auto-train-models.md)
