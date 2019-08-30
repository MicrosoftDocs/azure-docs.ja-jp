---
title: '回帰モデルのチュートリアル: 自動化された ML'
titleSuffix: Azure Machine Learning service
description: 自動化された機械学習を使用して機械学習モデルを生成する方法について説明します。 Azure Machine Learning では、データの事前処理、アルゴリズムの選択、ハイパーパラメーターの選択をユーザーに代わって自動的に実行できます。 その後、最終的なモデルが Azure Machine Learning service によってデプロイされます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: nacharya1
ms.author: nilesha
ms.reviewer: trbye
ms.date: 08/11/2019
ms.custom: seodec18
ms.openlocfilehash: 49f46c09cfcfef2ab1e74ae7c08d9a54289293ac
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534824"
---
# <a name="tutorial-use-automated-machine-learning-to-build-your-regression-model"></a>チュートリアル:自動化された機械学習を使用して回帰モデルを構築する

このチュートリアルは、**2 部構成のチュートリアル シリーズのパート 2 です**。 前回のチュートリアルでは、[回帰モデル用の NYC タクシー データを準備](tutorial-data-prep.md)しました。

Azure Machine Learning service を使用してお客様のモデルの構築を開始する準備は整っています。 チュートリアルのこのパートでは、準備したデータを使用して、タクシー運賃の価格を予測する回帰モデルを自動的に生成します。 本サービスの自動化された機械学習機能を使用して、お客様の機械学習の目的と制約を定義します。 自動化された機械学習プロセスを開始します。 次に、アルゴリズムの選択とハイパーパラメーターの調整がお客様のために行われるようにします。 自動化された機械学習の手法では、アルゴリズムとハイパーパラメーターの組み合わせを多数イテレーションして、お客様の条件に基づき最高のモデルを見つけます。

![フロー図](./media/tutorial-auto-train-models/flow2.png)

このチュートリアルでは、以下のタスクについて学習します。

> [!div class="checklist"]
> * Python 環境を設定して SDK パッケージをインポートする。
> * Azure Machine Learning service ワークスペースを構成する。
> * 回帰モデルを自動トレーニングする。
> * カスタム パラメーターを使用してモデルをローカルで実行する。
> * 結果を調べる。

Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning service](https://aka.ms/AMLFree) を今日からお試しいただけます。

>[!NOTE]
> この記事のコードは、Azure Machine Learning SDK バージョン 1.0.39 を使用してテストされました。

## <a name="prerequisites"></a>前提条件

* パート 1 の[データ準備のチュートリアル](tutorial-data-prep.md)を完了してください。

* パート 1 を完了したら、同じノートブック サーバーを使用して、**tutorials/regression-part2-automated-ml.ipynb** ノートブックを開きます。

このチュートリアルは、独自の[ローカル環境](how-to-configure-environment.md#local)で使用する場合は、[GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) で入手することもできます。  Azure Machine Learning SDK の `matplotlib` と、`automl` および `notebooks` extras がインストールされていることを確認してください。

## <a name="start"></a>開発環境を設定する

Python Notebook で、開発作業に関するすべての設定を行うことができます。 設定の一環として次のことを行います。

* SDK のインストール
* Python パッケージをインポートする
* ワークスペースを構成する

### <a name="install-and-import-packages"></a>パッケージをインストールしてインポートする

独自の Python 環境でチュートリアルを実行している場合は、次を使用して必要なパッケージをインストールします。

```shell
pip install azureml-sdk[automl,notebooks] matplotlib
```

このチュートリアルで必要な Python パッケージをインポートします。

```python
import azureml.core
import pandas as pd
from azureml.core.workspace import Workspace
import logging
import os
```

### <a name="configure-workspace"></a>ワークスペースの構成

既存のワークスペースからワークスペース オブジェクトを作成します。 [ワークスペース](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)は、お客様の Azure サブスクリプションとリソースの情報を受け取るクラスです。 また、これにより、お客様のモデル実行を監視して追跡するためのクラウド リソースが作成されます。

`Workspace.from_config()` は、**config.json** ファイルを読み取り、詳細情報を `ws` という名前のオブジェクト内に読み込みます。  `ws` は、このチュートリアルの残りのコード全体で使用されています。

ワークスペース オブジェクトを用意したら、実験の名前を指定します。 ワークスペースが含まれるローカル ディレクトリを作成して登録します。 すべての実行履歴が、指定された実験と [Azure portal](https://portal.azure.com) に記録されます。


```python
ws = Workspace.from_config()
# choose a name for the run history container in the workspace
experiment_name = 'automated-ml-regression'
# project folder
project_folder = './automated-ml-regression'

output = {}
output['SDK version'] = azureml.core.VERSION
output['Subscription ID'] = ws.subscription_id
output['Workspace'] = ws.name
output['Resource Group'] = ws.resource_group
output['Location'] = ws.location
output['Project Directory'] = project_folder
pd.set_option('display.max_colwidth', -1)
pd.DataFrame(data=output, index=['']).T
```

## <a name="explore-data"></a>データを調査する

前のチュートリアルで作成されたデータ フロー オブジェクトを使用します。 要約すると、このチュートリアルのパート 1 では、NYC タクシー データを整理して、機械学習モデルで使用できるようにしました。 次に、データセットのさまざまな機能を使用して、自動化されたモデルで機能とタクシー乗車の料金との関係を構築できるようにします。 データ フローを開いて実行し、結果を確認します。


```python
import azureml.dataprep as dprep

file_path = os.path.join(os.getcwd(), "dflows.dprep")

dflow_prepared = dprep.Dataflow.open(file_path)
dflow_prepared.get_profile()
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
      <th>Error count</th>
      <th>Empty count</th>
      <th>0.1% quantile</th>
      <th>1% quantile</th>
      <th>5% quantile</th>
      <th>25% quantile</th>
      <th>50% quantile</th>
      <th>75% quantile</th>
      <th>95% quantile</th>
      <th>99% quantile</th>
      <th>99.9% quantile</th>
      <th>平均</th>
      <th>標準偏差</th>
      <th>variance</th>
      <th>傾斜</th>
      <th>尖度</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>vendor</th>
      <td>FieldType.STRING</td>
      <td>1</td>
      <td>VTS</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
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
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_weekday</th>
      <td>FieldType.STRING</td>
      <td>金曜日</td>
      <td>水曜日</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
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
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_hour</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>23</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>2.90047</td>
      <td>2.69355</td>
      <td>9.72889</td>
      <td>16</td>
      <td>19.3713</td>
      <td>22.6974</td>
      <td>23</td>
      <td>23</td>
      <td>14.2731</td>
      <td>6.59242</td>
      <td>43.46</td>
      <td>-0.693723</td>
      <td>-0.570403</td>
    </tr>
    <tr>
      <th>pickup_minute</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>4.99701</td>
      <td>4.95833</td>
      <td>14.1528</td>
      <td>29.3832</td>
      <td>44.6825</td>
      <td>56.4444</td>
      <td>58.9909</td>
      <td>59</td>
      <td>29.427</td>
      <td>17.4333</td>
      <td>303.921</td>
      <td>0.0120999</td>
      <td>-1.20981</td>
    </tr>
    <tr>
      <th>pickup_second</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5.28131</td>
      <td>5</td>
      <td>14.7832</td>
      <td>29.9293</td>
      <td>44.725</td>
      <td>56.7573</td>
      <td>59</td>
      <td>59</td>
      <td>29.7443</td>
      <td>17.3595</td>
      <td>301.351</td>
      <td>-0.0252399</td>
      <td>-1.19616</td>
    </tr>
    <tr>
      <th>dropoff_weekday</th>
      <td>FieldType.STRING</td>
      <td>金曜日</td>
      <td>水曜日</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
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
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>dropoff_hour</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>23</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>2.57153</td>
      <td>2</td>
      <td>9.58795</td>
      <td>15.9994</td>
      <td>19.6184</td>
      <td>22.8317</td>
      <td>23</td>
      <td>23</td>
      <td>14.2105</td>
      <td>6.71093</td>
      <td>45.0365</td>
      <td>-0.687292</td>
      <td>-0.61951</td>
    </tr>
    <tr>
      <th>dropoff_minute</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5.44383</td>
      <td>4.84694</td>
      <td>14.1036</td>
      <td>28.8365</td>
      <td>44.3102</td>
      <td>56.6892</td>
      <td>59</td>
      <td>59</td>
      <td>29.2907</td>
      <td>17.4108</td>
      <td>303.136</td>
      <td>0.0222514</td>
      <td>-1.2181</td>
    </tr>
    <tr>
      <th>dropoff_second</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5.07801</td>
      <td>5</td>
      <td>14.5751</td>
      <td>29.5972</td>
      <td>45.4649</td>
      <td>56.2729</td>
      <td>59</td>
      <td>59</td>
      <td>29.772</td>
      <td>17.5337</td>
      <td>307.429</td>
      <td>-0.0212575</td>
      <td>-1.226</td>
    </tr>
    <tr>
      <th>store_forward</th>
      <td>FieldType.STRING</td>
      <td>N</td>
      <td>Y</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
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
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.0781</td>
      <td>-73.7459</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.0578</td>
      <td>-73.9639</td>
      <td>-73.9656</td>
      <td>-73.9508</td>
      <td>-73.9255</td>
      <td>-73.8529</td>
      <td>-73.8302</td>
      <td>-73.8238</td>
      <td>-73.7697</td>
      <td>-73.9123</td>
      <td>0.0503757</td>
      <td>0.00253771</td>
      <td>0.352172</td>
      <td>-0.923743</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.5755</td>
      <td>40.8799</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.632</td>
      <td>40.7117</td>
      <td>40.7115</td>
      <td>40.7213</td>
      <td>40.7565</td>
      <td>40.8058</td>
      <td>40.8478</td>
      <td>40.8676</td>
      <td>40.8778</td>
      <td>40.7649</td>
      <td>0.0494674</td>
      <td>0.00244702</td>
      <td>0.205972</td>
      <td>-0.777945</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.0857</td>
      <td>-73.7209</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.0775</td>
      <td>-73.9875</td>
      <td>-73.9882</td>
      <td>-73.9638</td>
      <td>-73.935</td>
      <td>-73.8755</td>
      <td>-73.8125</td>
      <td>-73.7759</td>
      <td>-73.7327</td>
      <td>-73.9202</td>
      <td>0.0584627</td>
      <td>0.00341789</td>
      <td>0.623622</td>
      <td>-0.262603</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.5835</td>
      <td>40.8797</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.5973</td>
      <td>40.6928</td>
      <td>40.6911</td>
      <td>40.7226</td>
      <td>40.7567</td>
      <td>40.7918</td>
      <td>40.8495</td>
      <td>40.868</td>
      <td>40.8787</td>
      <td>40.7583</td>
      <td>0.0517399</td>
      <td>0.00267701</td>
      <td>0.0390404</td>
      <td>-0.203525</td>
    </tr>
    <tr>
      <th>passengers</th>
      <td>FieldType.DECIMAL</td>
      <td>1</td>
      <td>6</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>5</td>
      <td>5</td>
      <td>6</td>
      <td>6</td>
      <td>2.39249</td>
      <td>1.83197</td>
      <td>3.3561</td>
      <td>0.763144</td>
      <td>-1.23467</td>
    </tr>
    <tr>
      <th>distance</th>
      <td>FieldType.DECIMAL</td>
      <td>0.01</td>
      <td>32.34</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0108744</td>
      <td>0.743898</td>
      <td>0.738194</td>
      <td>1.243</td>
      <td>2.40168</td>
      <td>4.74478</td>
      <td>10.5136</td>
      <td>14.9011</td>
      <td>21.8035</td>
      <td>3.5447</td>
      <td>3.2943</td>
      <td>10.8524</td>
      <td>1.91556</td>
      <td>4.99898</td>
    </tr>
    <tr>
      <th>cost</th>
      <td>FieldType.DECIMAL</td>
      <td>0.1</td>
      <td>88</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>2.33837</td>
      <td>5.00491</td>
      <td>5</td>
      <td>6.93129</td>
      <td>10.524</td>
      <td>17.4811</td>
      <td>33.2343</td>
      <td>50.0093</td>
      <td>63.1753</td>
      <td>13.6843</td>
      <td>9.66571</td>
      <td>93.426</td>
      <td>1.78518</td>
      <td>4.13972</td>
    </tr>
  </tbody>
</table>

モデル作成の機能となるように `dflow_x` に列を追加して、実験用のデータを準備します。 予測値 **cost** になるように、`dflow_y` を定義します。

```python
dflow_X = dflow_prepared.keep_columns(
    ['pickup_weekday', 'pickup_hour', 'distance', 'passengers', 'vendor'])
dflow_y = dflow_prepared.keep_columns('cost')
```

### <a name="split-the-data-into-train-and-test-sets"></a>データをトレーニング セットとテスト セットに分割する

次に、`sklearn` ライブラリの `train_test_split` 関数を使用して、トレーニング セットとテスト セットにデータを分割します。 この関数は、モデル トレーニング用の x (**機能**) データ セットとテスト用の y (**予測する値**) データ セットに、データを分割します。 `test_size` パラメーターでは、テストに割り当てるデータの割合を決定します。 `random_state` パラメーターでは、お客様のトレーニングとテストの分割が常に決定論的になるように、ランダム ジェネレーターにシードを設定します。

```python
from sklearn.model_selection import train_test_split

x_df = dflow_X.to_pandas_dataframe()
y_df = dflow_y.to_pandas_dataframe()

x_train, x_test, y_train, y_test = train_test_split(
    x_df, y_df, test_size=0.2, random_state=223)
# flatten y_train to 1d array
y_train.values.flatten()
```

この手順の目的は、正しい精度を測定するために、モデルのトレーニングに使用されたことのないデータ ポイントで完成モデルをテストすることです。 つまり、適切にトレーニングされたモデルは、まだ見たことのないデータから正確な予測ができるはずです。 これで、お客様のモデルを自動的にトレーニングするために必要なパッケージとデータが揃いました。

## <a name="automatically-train-a-model"></a>自動的にモデルをトレーニングする

モデルを自動的にトレーニングするには、次の手順を実行します。
1. 実験の実行用の設定を定義する。 トレーニング データを構成にアタッチし、トレーニング プロセスを制御する設定を変更します。
1. モデル調整用の実験を送信する。 実験を送信した後、プロセスは定義された制約に従って、他の機械学習アルゴリズムとハイパー パラメーター設定を反復処理します。 精度メトリックを最適化することによって、最適なモデルが選択されます。

### <a name="define-settings-for-autogeneration-and-tuning"></a>自動生成と調整用の設定を定義する

自動生成と調整用の実験パラメーターとモデルの設定を定義します。 [設定](how-to-configure-auto-train.md)の完全な一覧を表示します。 これらの既定の設定で実験を送信するには約 10 分から 15 分かかりますが、実行時間を短くしたい場合は `iterations` または `iteration_timeout_minutes` を減らしてください。


|プロパティ| このチュートリアルの値 |説明|
|----|----|---|
|**iteration_timeout_minutes**|10|各イテレーションの分単位での時間制限。 合計実行時間を短縮するには、この値を減らします。|
|**iterations**|30|イテレーションの回数。 各イテレーションでは、新しい機械学習モデルがデータでトレーニングされます。 これは、合計実行時間に影響を与える主要な値です。|
|**primary_metric**| spearman_correlation | 最適化したいメトリック。 このメトリックに基づいて、最適なモデルが選択されます。|
|**preprocess**| True | **True** を使用すると、実験の入力データを前処理できます (欠損データの処理、テキストから数値への変換など)。|
|**verbosity**| logging.INFO | ログ記録のレベルを制御します。|
|**n_cross_validations**|5|検証データが指定されていない場合に実行される、クロス検証の分割の数。|



```python
automl_settings = {
    "iteration_timeout_minutes": 10,
    "iterations": 30,
    "primary_metric": 'spearman_correlation',
    "preprocess": True,
    "verbosity": logging.INFO,
    "n_cross_validations": 5
}
```

定義済みのトレーニング設定を `AutoMLConfig` オブジェクトへのパラメーターとして使用します。 さらに、トレーニング データとモデルの種類を指定します。モデルの種類は、ここでは `regression` です。

```python
from azureml.train.automl import AutoMLConfig

# local compute
automated_ml_config = AutoMLConfig(task='regression',
                                   debug_log='automated_ml_errors.log',
                                   path=project_folder,
                                   X=x_train.values,
                                   y=y_train.values.flatten(),
                                   **automl_settings)
```

> [!NOTE]
> 自動化された機械学習の前処理手順 (機能の正規化、欠損データの処理、テキストから数値への変換など) は、基になるモデルの一部になります。 モデルを予測に使用する場合、トレーニング中に適用されたのと同じ前処理手順が入力データに自動的に適用されます。

### <a name="train-the-automatic-regression-model"></a>自動回帰モデルをトレーニングする

ローカルで実験の実行を開始します。 定義済みの `automated_ml_config` オブジェクトを実験に渡します。 出力を `True` に設定して実験中の進行状況を表示します。


```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, experiment_name)
local_run = experiment.submit(automated_ml_config, show_output=True)
```

表示される出力は、実験の実行中に随時更新されます。 各イテレーションでは、モデルの種類、実行継続時間、およびトレーニングの精度が表示されます。 `BEST` フィールドでは、メトリックの種類に基づいて、最高の実行トレーニング スコアが追跡されます。

    Parent Run ID: AutoML_02778de3-3696-46e9-a71b-521c8fca0651
    *******************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE: A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    *******************************************************************************************

     ITERATION   PIPELINE                                       DURATION      METRIC      BEST
             0   MaxAbsScaler ExtremeRandomTrees                0:00:08       0.9447    0.9447
             1   StandardScalerWrapper GradientBoosting         0:00:09       0.9536    0.9536
             2   StandardScalerWrapper ExtremeRandomTrees       0:00:09       0.8580    0.9536
             3   StandardScalerWrapper RandomForest             0:00:08       0.9147    0.9536
             4   StandardScalerWrapper ExtremeRandomTrees       0:00:45       0.9398    0.9536
             5   MaxAbsScaler LightGBM                          0:00:08       0.9562    0.9562
             6   StandardScalerWrapper ExtremeRandomTrees       0:00:27       0.8282    0.9562
             7   StandardScalerWrapper LightGBM                 0:00:07       0.9421    0.9562
             8   MaxAbsScaler DecisionTree                      0:00:08       0.9526    0.9562
             9   MaxAbsScaler RandomForest                      0:00:09       0.9355    0.9562
            10   MaxAbsScaler SGD                               0:00:09       0.9602    0.9602
            11   MaxAbsScaler LightGBM                          0:00:09       0.9553    0.9602
            12   MaxAbsScaler DecisionTree                      0:00:07       0.9484    0.9602
            13   MaxAbsScaler LightGBM                          0:00:08       0.9540    0.9602
            14   MaxAbsScaler RandomForest                      0:00:10       0.9365    0.9602
            15   MaxAbsScaler SGD                               0:00:09       0.9602    0.9602
            16   StandardScalerWrapper ExtremeRandomTrees       0:00:49       0.9171    0.9602
            17   SparseNormalizer LightGBM                      0:00:08       0.9191    0.9602
            18   MaxAbsScaler DecisionTree                      0:00:08       0.9402    0.9602
            19   StandardScalerWrapper ElasticNet               0:00:08       0.9603    0.9603
            20   MaxAbsScaler DecisionTree                      0:00:08       0.9513    0.9603
            21   MaxAbsScaler SGD                               0:00:08       0.9603    0.9603
            22   MaxAbsScaler SGD                               0:00:10       0.9602    0.9603
            23   StandardScalerWrapper ElasticNet               0:00:09       0.9603    0.9603
            24   StandardScalerWrapper ElasticNet               0:00:09       0.9603    0.9603
            25   MaxAbsScaler SGD                               0:00:09       0.9603    0.9603
            26   TruncatedSVDWrapper ElasticNet                 0:00:09       0.9602    0.9603
            27   MaxAbsScaler SGD                               0:00:12       0.9413    0.9603
            28   StandardScalerWrapper ElasticNet               0:00:07       0.9603    0.9603
            29    Ensemble                                      0:00:38       0.9622    0.9622

## <a name="explore-the-results"></a>結果を検索する

Jupyter ウィジェットを使用するか、実験の履歴を検証して、自動トレーニングの結果を探索します。

### <a name="option-1-add-a-jupyter-widget-to-see-results"></a>方法 1: Jupyter ウィジェットを追加して結果を表示する

Jupyter ノートブックを使用する場合は、この [Jupyter ウィジェット](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)を使ってすべての結果のグラフおよびテーブルを参照します。


```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![Jupyter ウィジェット実行の詳細](./media/tutorial-auto-train-models/automl-dash-output.png)
![Jupyter ウィジェットのプロット](./media/tutorial-auto-train-models/automl-chart-output.png)

同じ結果がワークスペースに格納されます。  実行結果へのリンクを取得できます。

```
local_run.get_portal_url()
```


### <a name="option-2-get-and-examine-all-run-iterations-in-python"></a>オプション 2:Python ですべての実行イテレーションを取得して調査する

各実験の履歴を取得して、各イテレーション実行の個々のメトリックを調べることもできます。 個々のモデル実行ごとに RMSE (root_mean_squared_error) を調べると、ほとんどのイテレーションで、妥当なマージン (3 から 4 ドル) 以内でタクシーの公正なコストが予測されていることがわかります。

```python
children = list(local_run.get_children())
metricslist = {}
for run in children:
    properties = run.get_properties()
    metrics = {k: v for k, v in run.get_metrics().items()
               if isinstance(v, float)}
    metricslist[int(properties['iteration'])] = metrics

rundata = pd.DataFrame(metricslist).sort_index(1)
rundata
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
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
      <th>7</th>
      <th>8</th>
      <th>9</th>
      <th>...</th>
      <th>20</th>
      <th>21</th>
      <th>22</th>
      <th>23</th>
      <th>24</th>
      <th>25</th>
      <th>26</th>
      <th>27</th>
      <th>28</th>
      <th>29</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>explained_variance</th>
      <td>0.811037</td>
      <td>0.880553</td>
      <td>0.398582</td>
      <td>0.776040</td>
      <td>0.663869</td>
      <td>0.875911</td>
      <td>0.115632</td>
      <td>0.586905</td>
      <td>0.851911</td>
      <td>0.793964</td>
      <td>...</td>
      <td>0.850023</td>
      <td>0.883603</td>
      <td>0.883704</td>
      <td>0.880797</td>
      <td>0.881564</td>
      <td>0.883708</td>
      <td>0.881826</td>
      <td>0.585377</td>
      <td>0.883123</td>
      <td>0.886817</td>
    </tr>
    <tr>
      <th>mean_absolute_error</th>
      <td>2.189444</td>
      <td>1.500412</td>
      <td>5.480531</td>
      <td>2.626316</td>
      <td>2.973026</td>
      <td>1.550199</td>
      <td>6.383868</td>
      <td>4.414241</td>
      <td>1.743328</td>
      <td>2.294601</td>
      <td>...</td>
      <td>1.797402</td>
      <td>1.415815</td>
      <td>1.418167</td>
      <td>1.578617</td>
      <td>1.559427</td>
      <td>1.413042</td>
      <td>1.551698</td>
      <td>4.069196</td>
      <td>1.505795</td>
      <td>1.430957</td>
    </tr>
    <tr>
      <th>median_absolute_error</th>
      <td>1.438417</td>
      <td>0.850899</td>
      <td>4.579662</td>
      <td>1.765210</td>
      <td>1.594600</td>
      <td>0.869883</td>
      <td>4.266450</td>
      <td>3.627355</td>
      <td>0.954992</td>
      <td>1.361014</td>
      <td>...</td>
      <td>0.973634</td>
      <td>0.774814</td>
      <td>0.797269</td>
      <td>1.147234</td>
      <td>1.116424</td>
      <td>0.783958</td>
      <td>1.098464</td>
      <td>2.709027</td>
      <td>1.003728</td>
      <td>0.851724</td>
    </tr>
    <tr>
      <th>normalized_mean_absolute_error</th>
      <td>0.024908</td>
      <td>0.017070</td>
      <td>0.062350</td>
      <td>0.029878</td>
      <td>0.033823</td>
      <td>0.017636</td>
      <td>0.072626</td>
      <td>0.050219</td>
      <td>0.019833</td>
      <td>0.026105</td>
      <td>...</td>
      <td>0.020448</td>
      <td>0.016107</td>
      <td>0.016134</td>
      <td>0.017959</td>
      <td>0.017741</td>
      <td>0.016076</td>
      <td>0.017653</td>
      <td>0.046293</td>
      <td>0.017131</td>
      <td>0.016279</td>
    </tr>
    <tr>
      <th>normalized_median_absolute_error</th>
      <td>0.016364</td>
      <td>0.009680</td>
      <td>0.052101</td>
      <td>0.020082</td>
      <td>0.018141</td>
      <td>0.009896</td>
      <td>0.048538</td>
      <td>0.041267</td>
      <td>0.010865</td>
      <td>0.015484</td>
      <td>...</td>
      <td>0.011077</td>
      <td>0.008815</td>
      <td>0.009070</td>
      <td>0.013052</td>
      <td>0.012701</td>
      <td>0.008919</td>
      <td>0.012497</td>
      <td>0.030819</td>
      <td>0.011419</td>
      <td>0.009690</td>
    </tr>
    <tr>
      <th>normalized_root_mean_squared_error</th>
      <td>0.047968</td>
      <td>0.037882</td>
      <td>0.085572</td>
      <td>0.052282</td>
      <td>0.065809</td>
      <td>0.038664</td>
      <td>0.109401</td>
      <td>0.071104</td>
      <td>0.042294</td>
      <td>0.049967</td>
      <td>...</td>
      <td>0.042565</td>
      <td>0.037685</td>
      <td>0.037557</td>
      <td>0.037643</td>
      <td>0.037513</td>
      <td>0.037560</td>
      <td>0.037465</td>
      <td>0.072077</td>
      <td>0.037249</td>
      <td>0.036716</td>
    </tr>
    <tr>
      <th>normalized_root_mean_squared_log_error</th>
      <td>0.055353</td>
      <td>0.045000</td>
      <td>0.110219</td>
      <td>0.065633</td>
      <td>0.063589</td>
      <td>0.044412</td>
      <td>0.123433</td>
      <td>0.092312</td>
      <td>0.046130</td>
      <td>0.055243</td>
      <td>...</td>
      <td>0.046540</td>
      <td>0.041804</td>
      <td>0.041771</td>
      <td>0.045175</td>
      <td>0.044628</td>
      <td>0.041617</td>
      <td>0.044405</td>
      <td>0.079651</td>
      <td>0.042799</td>
      <td>0.041530</td>
    </tr>
    <tr>
      <th>r2_score</th>
      <td>0.810900</td>
      <td>0.880328</td>
      <td>0.398076</td>
      <td>0.775957</td>
      <td>0.642812</td>
      <td>0.875719</td>
      <td>0.021603</td>
      <td>0.586514</td>
      <td>0.851767</td>
      <td>0.793671</td>
      <td>...</td>
      <td>0.849809</td>
      <td>0.880142</td>
      <td>0.880952</td>
      <td>0.880586</td>
      <td>0.881347</td>
      <td>0.880887</td>
      <td>0.881613</td>
      <td>0.548121</td>
      <td>0.882883</td>
      <td>0.886321</td>
    </tr>
    <tr>
      <th>root_mean_squared_error</th>
      <td>4.216362</td>
      <td>3.329810</td>
      <td>7.521765</td>
      <td>4.595604</td>
      <td>5.784601</td>
      <td>3.398540</td>
      <td>9.616354</td>
      <td>6.250011</td>
      <td>3.717661</td>
      <td>4.392072</td>
      <td>...</td>
      <td>3.741447</td>
      <td>3.312533</td>
      <td>3.301242</td>
      <td>3.308795</td>
      <td>3.297389</td>
      <td>3.301485</td>
      <td>3.293182</td>
      <td>6.335581</td>
      <td>3.274209</td>
      <td>3.227365</td>
    </tr>
    <tr>
      <th>root_mean_squared_log_error</th>
      <td>0.243184</td>
      <td>0.197702</td>
      <td>0.484227</td>
      <td>0.288349</td>
      <td>0.279367</td>
      <td>0.195116</td>
      <td>0.542281</td>
      <td>0.405559</td>
      <td>0.202666</td>
      <td>0.242702</td>
      <td>...</td>
      <td>0.204464</td>
      <td>0.183658</td>
      <td>0.183514</td>
      <td>0.198468</td>
      <td>0.196067</td>
      <td>0.182836</td>
      <td>0.195087</td>
      <td>0.349935</td>
      <td>0.188031</td>
      <td>0.182455</td>
    </tr>
    <tr>
      <th>spearman_correlation</th>
      <td>0.944743</td>
      <td>0.953618</td>
      <td>0.857965</td>
      <td>0.914703</td>
      <td>0.939846</td>
      <td>0.956159</td>
      <td>0.828187</td>
      <td>0.942069</td>
      <td>0.952581</td>
      <td>0.935477</td>
      <td>...</td>
      <td>0.951287</td>
      <td>0.960335</td>
      <td>0.960195</td>
      <td>0.960279</td>
      <td>0.960288</td>
      <td>0.960323</td>
      <td>0.960161</td>
      <td>0.941254</td>
      <td>0.960293</td>
      <td>0.962158</td>
    </tr>
    <tr>
      <th>spearman_correlation_max</th>
      <td>0.944743</td>
      <td>0.953618</td>
      <td>0.953618</td>
      <td>0.953618</td>
      <td>0.953618</td>
      <td>0.956159</td>
      <td>0.956159</td>
      <td>0.956159</td>
      <td>0.956159</td>
      <td>0.956159</td>
      <td>...</td>
      <td>0.960303</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.962158</td>
    </tr>
  </tbody>
</table>
<p>12 行 × 30 列</p>
</div>

## <a name="retrieve-the-best-model"></a>最高のモデルを取得する

イテレーションから最適なパイプラインを選択します。 `automl_classifier` 上の `get_output` メソッドは、最適な実行と、最後の fit の呼び出しで適合したモデルを返します。 `get_output` 上のオーバーロードを使用して、ログ記録された任意のメトリックや特定のイテレーションに対する最適な実行と適合モデルを取得できます。

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

## <a name="test-the-best-model-accuracy"></a>最高のモデルの正確性をテストする

最高のモデルを使用して、テスト データ セット上で予測を実行し、タクシー料金を予測します。 関数 `predict` によって最高のモデルが使用され、`x_test` データセットから y (**交通費**) の値が予測されます。 `y_predict` から最初の 10 個の予測コスト値を出力します。

```python
y_predict = fitted_model.predict(x_test.values)
print(y_predict[:10])
```

散布図を作成して、実績コスト値と比較した予測コスト値を視覚化します。 次のコードでは、特徴 (`distance`) が X 軸、交通費 (`cost`) が Y 軸として使用されています。 個々の乗車距離値における予測コストの差異を比較するために、最初の 100 個の予測コスト値と実績コスト値は別個の系列として作成されます。 このプロットを観察すると、距離とコストの関係がほぼ線形であり、同じ乗車距離であれば、ほとんどの場合、予測コスト値が実績コスト値にきわめて近いことがわかります。

```python
%matplotlib inline

import matplotlib.pyplot as plt

fig = plt.figure(figsize=(14, 10))
ax1 = fig.add_subplot(111)

distance_vals = [x[4] for x in x_test.values]
y_actual = y_test.values.flatten().tolist()

ax1.scatter(distance_vals[:100], y_predict[:100],
            s=18, c='b', marker="s", label='Predicted')
ax1.scatter(distance_vals[:100], y_actual[:100],
            s=18, c='r', marker="o", label='Actual')

ax1.set_xlabel('distance (mi)')
ax1.set_title('Predicted and Actual Cost/Distance')
ax1.set_ylabel('Cost ($)')

plt.legend(loc='upper left', prop={'size': 12})
plt.rcParams.update({'font.size': 14})
plt.show()
```

![予測散布図](./media/tutorial-auto-train-models/automl-scatter-plot.png)

結果の `root mean squared error` を計算します。 `y_test` データフレームを使用します。 予測値と比較するために、これをリストに変換します。 関数 `mean_squared_error` によって 2 つの配列の値が受け取られ、それらの間の平均二乗誤差が計算されます。 結果の平方根を取ると、y 変数 (**コスト**) と同じ単位で誤差が得られます。 これは、タクシー料金の予測が実際の料金からどの程度離れているかを大まかに示します。

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

    3.2204936862688798

次のコードを実行し、完全な `y_actual` と `y_predict` データセットを使用して平均絶対誤差率 (MAPE) を計算します。 このメトリックでは、予測される各値と実際の値の間の絶対誤差が計算され、すべての差分が合計されます。 そしてその合計が、実際の値の合計に対する割合として表されます。

```python
sum_actuals = sum_errors = 0

for actual_val, predict_val in zip(y_actual, y_predict):
    abs_error = actual_val - predict_val
    if abs_error < 0:
        abs_error = abs_error * -1

    sum_errors = sum_errors + abs_error
    sum_actuals = sum_actuals + actual_val

mean_abs_percent_error = sum_errors / sum_actuals
print("Model MAPE:")
print(mean_abs_percent_error)
print()
print("Model Accuracy:")
print(1 - mean_abs_percent_error)
```

    Model MAPE:
    0.10545153869569586

    Model Accuracy:
    0.8945484613043041

最終的な予測精度メトリックから、モデルでのデータ セットの機能によるタクシー料金の予測はかなり良好で、誤差は 3 ドル以内であることがわかります。 従来の機械学習モデルの開発プロセスでは、リソースが大量に消費され、数十個のモデルを実行して結果を比較するために、その領域に関する膨大な知識と時間の投資が必要とされます。 自動化された機械学習の使用は、シナリオに合わせてさまざまなモデルを迅速にテストするための優れた方法です。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>次の手順

自動化された機械学習に関するこのチュートリアルでは、以下のタスクを学習しました。

> [!div class="checklist"]
> * 実験用のワークスペースと準備されたデータを構成しました。
> * カスタム パラメーターを使って、自動化された回帰モデルをローカルで使用してトレーニングしました。
> * トレーニング結果を調べて確認しました。

Azure Machine Learning を使って[モデルをデプロイ](tutorial-deploy-models-with-aml.md)してください。
