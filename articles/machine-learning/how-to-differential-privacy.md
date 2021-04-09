---
title: 差分プライバシーの方法 - SmartNoise (プレビュー)
titleSuffix: Azure Machine Learning
description: SmartNoise オープンソース ライブラリを使用して、Azure Machine Learning モデルに差分プライバシーのベスト プラクティスを適用する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, responsible-ml
ms.author: slbird
author: slbird
ms.reviewer: luquinta
ms.date: 01/21/2020
ms.openlocfilehash: 62a002569696da4ef18e7bd967f027eb8247ef65
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98681406"
---
# <a name="use-differential-privacy-in-azure-machine-learning-preview"></a>Azure Machine Learning で差分プライバシーを使用する (プレビュー)

SmartNoise Python オープンソース ライブラリを使用して、Azure Machine Learning モデルに差分プライバシーのベスト プラクティスを適用する方法について説明します。

差分プライバシーは、ゴールド スタンダードなプライバシー定義です。 このプライバシー定義に準拠しているシステムでは、幅広いデータ再構成やデータ再識別攻撃 (補助情報を持つ敵対者からの攻撃など) への防御が強力に保証されます。 詳細については、「[差分プライバシーのしくみ](./concept-differential-privacy.md)」を参照してください。


## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。
- [Python 3](https://www.python.org/downloads/)

## <a name="install-smartnoise-python-libraries"></a>SmartNoise Python ライブラリをインストールする

### <a name="standalone-installation"></a>スタンドアロン インストール

ライブラリは分散型 Spark クラスターから動作するように設計されており、他のパッケージと同じようにインストールできます。

以下の手順は、`python` と `pip` コマンドが `python3` と `pip3` にマップされることを前提としています。

pip を使用して [SmartNoise Python パッケージ](https://pypi.org/project/opendp-smartnoise/)をインストールします。

`pip install opendp-smartnoise`

パッケージがインストールされたことを確認するには、Python プロンプトを起動し、次のように入力します。

```python
import opendp.smartnoise.core
import opendp.smartnoise.sql
```

インポートが成功すると、ライブラリがインストールされて使用できる状態になります。

### <a name="docker-image-installation"></a>Docker イメージのインストール

SmartNoise パッケージを Docker と併用することもできます。

Spark、Jupyter、サンプル コードを含む Docker コンテナー内でライブラリを使用するには、`opendp/smartnoise` イメージをプルします。

```sh
docker pull opendp/smartnoise:privacy
```

イメージをプルしたら、Jupyter サーバーを起動します。

```sh
docker run --rm -p 8989:8989 --name smartnoise-run opendp/smartnoise:privacy
```

これにより、`localhost` のポート `8989` でパスワード `pass@word99` を使用して Jupyter サーバーが開始されます。 上記のコマンド ラインを使用して `smartnoise-privacy` という名前のコンテナーを開始したと仮定すると、以下を実行して Jupyter サーバーで Bash ターミナルを開くことができます。

```sh
docker exec -it smartnoise-run bash
```

Docker インスタンスではシャットダウン時にすべての状態がクリアされるため、実行中のインスタンスで作成したノートブックはすべて失われます。 その対策として、コンテナー起動時にローカル フォルダーをコンテナーにバインド マウントできます。

```sh
docker run --rm -p 8989:8989 --name smartnoise-run --mount type=bind,source=/Users/your_name/my-notebooks,target=/home/privacy/my-notebooks opendp/smartnoise:privacy
```

*my-notebooks* フォルダーに作成したノートブックはすべて、ローカル ファイルシステムに格納されます。

## <a name="perform-data-analysis"></a>データ分析の実行

差分プライベート リリースを準備するには、データ ソースと統計を選択し、さらにプライバシー保護のレベルを示すいくつかのプライバシー パラメーターを選択する必要があります。  

このサンプルでは、市民の人口統計の匿名化された記録を表す、California Public Use Microdata (PUMS) を参照します。

```python
import os
import sys
import numpy as np
import opendp.smartnoise.core as sn

data_path = os.path.join('.', 'data', 'PUMS_california_demographics_1000', 'data.csv')
var_names = ["age", "sex", "educ", "race", "income", "married", "pid"]
```

この例では、年齢の平均と分散を計算します。  合計 1.0 の `epsilon` を使用します (epsilon はプライバシー パラメーターで、計算する 2 つの数量にプライバシー予算を振り分けます)。 詳細については、[プライバシー メトリック](concept-differential-privacy.md#differential-privacy-metrics)に関するページを参照してください。

```python
with sn.Analysis() as analysis:
    # load data
    data = sn.Dataset(path = data_path, column_names = var_names)

    # get mean of age
    age_mean = sn.dp_mean(data = sn.cast(data['age'], type="FLOAT"),
                          privacy_usage = {'epsilon': .65},
                          data_lower = 0.,
                          data_upper = 100.,
                          data_n = 1000
                         )
    # get variance of age
    age_var = sn.dp_variance(data = sn.cast(data['age'], type="FLOAT"),
                             privacy_usage = {'epsilon': .35},
                             data_lower = 0.,
                             data_upper = 100.,
                             data_n = 1000
                            )
analysis.release()

print("DP mean of age: {0}".format(age_mean.value))
print("DP variance of age: {0}".format(age_var.value))
print("Privacy usage: {0}".format(analysis.privacy_usage))
```

結果は次のようになります。

```text
DP mean of age: 44.55598845931517
DP variance of age: 231.79044646429134
Privacy usage: approximate {
  epsilon: 1.0
}
```

この例については、注意すべき重要な点がいくつかあります。  まず、`Analysis` オブジェクトはデータ処理グラフを表します。  この例では、平均と分散が同じソース ノードから計算されます。  ただし、入力と出力を任意の方法で組み合わせる、より複雑な式を組み込むことができます。

分析グラフには、年齢の下限と上限を指定するメタデータ `data_upper` と `data_lower` が含まれています。  これらの値は、ノイズを正確に調整して差分プライバシーを確保するために使用されます。  これらの値は、外れ値や欠損値の処理にも使用されます。

最後に、分析グラフでは費やしたプライバシー予算の合計が追跡されます。

ライブラリを使用して、複数のメカニズム、統計、ユーティリティ関数で、より複雑な分析グラフを作成できます。

| 統計    | メカニズム | Utilities  |
| ------------- |------------|------------|
| Count         | ガウス   | キャスト       |
| ヒストグラム     | 幾何  | クランプ   |
| 平均          | ラプラス    | デジタル化   |
| 分位点     |            | Assert     |
| SUM           |            | 補完 |
| 分散/共分散 |      | 変換  |

詳細については、[データ分析のノートブック](https://github.com/opendifferentialprivacy/smartnoise-samples/blob/master/analysis/basic_data_analysis.ipynb)を参照してください。

## <a name="approximate-utility-of-differentially-private-releases"></a>差分プライベート リリースのおおよその有用性

差分プライバシーはノイズを調整することで機能するため、プライバシー リスクによってリリースの有用性が変わる可能性があります。  一般的に、各個人を保護するために必要なノイズは、サンプル サイズが大きくなるにつれて無視できる程度になりますが、各個人を対象としたリリースの場合は、その結果を圧倒します。  アナリストは、リリースの精度情報を確認して、リリースの有用性を判断できます。

```python
with sn.Analysis() as analysis:
    # load data
    data = sn.Dataset(path = data_path, column_names = var_names)

    # get mean of age
    age_mean = sn.dp_mean(data = sn.cast(data['age'], type="FLOAT"),
                          privacy_usage = {'epsilon': .65},
                          data_lower = 0.,
                          data_upper = 100.,
                          data_n = 1000
                         )
analysis.release()

print("Age accuracy is: {0}".format(age_mean.get_accuracy(0.05)))
```

この演算の結果は次のようになります。

```text
Age accuracy is: 0.2995732273553991
```

この例では、上記のように平均を計算し、`get_accuracy` 関数を使用して 0.05 の `alpha` での精度を要求します。 0\.05 の `alpha` は 95% の区間を表します。この場合、リリース値は、95% の時間について報告される精度限界の間になります。  この例で報告されている精度は 0.3 です。つまり、リリース値は 95% の時間について幅 0.6 の区間内に収まります。  この値をエラー バーと見なすのは正しくありません。これは、リリース値が `alpha` で指定された比率での報告された精度範囲を外れ、範囲外の値がどちらの方向にも外れる可能性があるためです。

アナリストは、`alpha` の値を変えて `get_accuracy` を実行することで、追加のプライバシー コストを発生させずに信頼区間を狭めたり広げたりできます。

## <a name="generate-a-histogram"></a>ヒストグラムを生成する

組み込み関数 `dp_histogram` は、次の種類のデータについて差分プライベート ヒストグラムを作成します。

- 連続変数 (数値のセットをビンに分割する必要があります)
- ブール変数または二分変数 (値を 2 つだけ取ることができます)
- カテゴリ変数 (個々のカテゴリが文字列として列挙されます)

次に、連続変数ヒストグラムのビンを指定する `Analysis` の例を示します。

```python
income_edges = list(range(0, 100000, 10000))

with sn.Analysis() as analysis:
    data = sn.Dataset(path = data_path, column_names = var_names)

    income_histogram = sn.dp_histogram(
            sn.cast(data['income'], type='int', lower=0, upper=100),
            edges = income_edges,
            upper = 1000,
            null_value = 150,
            privacy_usage = {'epsilon': 0.5}
        )
```

各個人がヒストグラム ビン間でばらばらに分けられているため、ヒストグラムに多数のビンが含まれていても、プライバシー コストはヒストグラムごとに 1 回しか発生しません。

ヒストグラムの詳細については、[ヒストグラムのノートブック](https://github.com/opendifferentialprivacy/smartnoise-samples/blob/master/analysis/histograms.ipynb)を参照してください。

## <a name="generate-a-covariance-matrix"></a>共分散行列を生成する

SmartNoise では、`dp_covariance` 関数で 3 つの異なる機能を提供します。

- 2 つのベクトル間の共分散
- 行列の共分散行列
- 行列ペアの相互共分散行列

次に、スカラー共分散を計算する例を示します。

```python
with sn.Analysis() as analysis:
    wn_data = sn.Dataset(path = data_path, column_names = var_names)

    age_income_cov_scalar = sn.dp_covariance(
      left = sn.cast(wn_data['age'], 
      type = "FLOAT"), 
      right = sn.cast(wn_data['income'], 
      type = "FLOAT"), 
      privacy_usage = {'epsilon': 1.0},
      left_lower = 0., 
      left_upper = 100., 
      left_n = 1000, 
      right_lower = 0., 
      right_upper = 500_000.,
      right_n = 1000)
```

詳細については、[共分散のノートブック](
https://github.com/opendifferentialprivacy/smartnoise-samples/blob/master/analysis/covariance.ipynb)を参照してください。

## <a name="next-steps"></a>次の手順

- [SmartNoise サンプルのノートブック](https://github.com/opendifferentialprivacy/smartnoise-samples/tree/master/analysis)を調べます。