---
title: ノートブック コードを Python スクリプトに変換する
titleSuffix: Azure Machine Learning
description: MLOpsPython コード テンプレートを使用して、機械学習の実験用ノートブックを運用環境に対応するコードに変換します。 その後、そのコードをテスト、デプロイ、および自動化できます。
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/30/2020
ms.custom: devx-track-python
ms.openlocfilehash: 3a6ce5860704e6fd16b79fc253650dd45ec743e7
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852618"
---
# <a name="tutorial-convert-ml-experiments-to-production-python-code"></a>チュートリアル:ML 実験を運用 Python コードに変換する

このチュートリアルでは、MLOpsPython コード テンプレートと Azure Machine Learning を使用して、Juptyer ノートブックを Python スクリプトに変換し、テストと自動化を容易にする方法について学習します。 通常、このプロセスは、Juptyer ノートブックから実験またはトレーニング コードを取得して Python スクリプトに変換するために使用されます。 これらのスクリプトは、運用環境でのテストおよび CI/CD 自動化に使用できます。 

機械学習プロジェクトでは、実際のデータセットを使用して Jupyter Notebook などのアジャイル ツールで仮説をテストする実験が必要です。 運用のためのモデルの準備ができたら、モデル コードを運用コード リポジトリに配置する必要があります。 場合によっては、モデル コードを Python スクリプトに変換して、運用コード リポジトリに配置する必要があります。 このチュートリアルでは、実験コードを Python スクリプトにエクスポートする方法について、推奨される方法を説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 不要なコードを消去する
> * Jupyter Notebook コードを関数にリファクタリングする
> * 関連タスク用の Python スクリプトを作成する
> * 単体テストを作成する

## <a name="prerequisites"></a>前提条件

- [MLOpsPython テンプレート](https://github.com/microsoft/MLOpsPython/generate)を生成し、`experimentation/Diabetes Ridge Regression Training.ipynb` と `experimentation/Diabetes Ridge Regression Scoring.ipynb` ノートブックを使用します。 これらのノートブックは、実験環境から運用に変換する例として使用されます。 これらのノートブックは、[https://github.com/microsoft/MLOpsPython/tree/master/experimentation](https://github.com/microsoft/MLOpsPython/tree/master/experimentation) にあります。
- `nbconvert`をインストールする。 [インストール](https://nbconvert.readthedocs.io/en/latest/install.html) ページの「__nbconvert のインストール__」セクションにあるインストール手順のみに従ってください。

## <a name="remove-all-nonessential-code"></a>不要なコードをすべて削除する

実験中に作成されるコードの中には、探索的な目的でのみ使用されるものもあります。 そのため、実験コードを運用コードに変換するための最初のステップは、この不要なコードを削除することです。 不要なコードを削除すると、コードの保守性も向上します。 このセクションでは、`experimentation/Diabetes Ridge Regression Training.ipynb` ノートブックからコードを削除します。 `X` と `y` の形状を出力するステートメントおよび `features.describe` を呼び出すセルはデータ探索のためのものであり、削除できます。 不要なコードを削除すると、`experimentation/Diabetes Ridge Regression Training.ipynb` はマークダウンのない次のコードのようになります。

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib
import pandas as pd

sample_data = load_diabetes()

df = pd.DataFrame(
    data=sample_data.data,
    columns=sample_data.feature_names)
df['Y'] = sample_data.target

X = df.drop('Y', axis=1).values
y = df['Y'].values

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=0)
data = {"train": {"X": X_train, "y": y_train},
        "test": {"X": X_test, "y": y_test}}

args = {
    "alpha": 0.5
}

reg_model = Ridge(**args)
reg.fit(data["train"]["X"], data["train"]["y"])

preds = reg_model.predict(data["test"]["X"])
mse = mean_squared_error(preds, y_test)
metrics = {"mse": mse}
print(metrics)

model_name = "sklearn_regression_model.pkl"
joblib.dump(value=reg, filename=model_name)
```

## <a name="refactor-code-into-functions"></a>コードを関数にリファクタリングする

次に、Jupyter コードを関数にリファクタリングする必要があります。 コードを関数にリファクタリングすると、単体テストが容易になり、コードの保守性が向上します。 このセクションでは、次のようにリファクタリングします。

- Diabetes Ridge 回帰トレーニング ノートブック (`experimentation/Diabetes Ridge Regression Training.ipynb`)
- Diabetes Ridge 回帰スコアリング ノートブック (`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Diabetes Ridge 回帰トレーニング ノートブックを関数にリファクタリングする

`experimentation/Diabetes Ridge Regression Training.ipynb` で、次の手順を実行します。

1. データ フレームをテスト データとトレーニング データに分割する `split_data` という関数を作成します。 この関数は、データフレーム `df` をパラメーターとして受け取って、`train` と `test` をキーとするディクショナリを返す必要があります。

    "*Split Data into Training and Validation Sets*" という見出しの下にあるコードを `split_data` 関数に移動し、`data` オブジェクトを返すように変更します。

1. パラメーター `data` および `args` を受け取って、トレーニング済みのモデルを返す、`train_model` という関数を作成します。

    "*Training Model on Training Set*" という見出しの下にあるコードを `train_model` 関数に移動し、`reg_model` オブジェクトを返すように変更します。 `args` ディクショナリは削除してください。この値は `args` パラメーターから渡されます。

1. `reg_model` および `data` をパラメーターとして受け取ってモデルを評価し、トレーニング済みのモデルのメトリックのディクショナリを返す `get_model_metrics` という関数を作成します。

    *Validate Model on Validation Set* という見出しの下のコードを `get_model_metrics` 関数に移動し、`metrics` オブジェクトを返すように変更します。

3 つの関数は次のようになっている必要があります。

```python
# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics
```

引き続き `experimentation/Diabetes Ridge Regression Training.ipynb` で、次の手順を実行します。

1. パラメーターを取らず、何も返さない、`main` という新しい関数を作成します。
1. "Load Data" という見出しの下にあるコードを `main` 関数に移動します。
1. 新しく作成した関数の呼び出しを `main` 関数に追加します。
    ```python
    # Split Data into Training and Validation Sets
    data = split_data(df)
    ```

    ```python
    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)
    ```

    ```python
    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)
    ```
1. "Save Model" という見出しの下にあるコードを `main` 関数に移動します。

`main` 関数は次のコードのようになります。

```python
def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)
```

この段階で、ノートブック内のコードは、1 つ目のセルの import ステートメントを除きすべて関数に移動されていると思います。

`main` 関数を呼び出すステートメントを追加します。

```python
main()
```

リファクタリング後、`experimentation/Diabetes Ridge Regression Training.ipynb` はマークダウンのない次のコードのようになります。

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)

main()
```

### <a name="refactor-diabetes-ridge-regression-scoring-notebook-into-functions"></a>Diabetes Ridge 回帰スコアリング ノートブックを関数にリファクタリングする

`experimentation/Diabetes Ridge Regression Scoring.ipynb` で、次の手順を実行します。

1. パラメーターを取らず、何も返さない、`init` という新しい関数を作成します。
1. "Load Model" という見出しの下にあるコードを `init` 関数にコピーします。

`init` 関数は次のコードのようになります。

```python
def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

`init` 関数が作成されたら、"Load Model" という見出しの下にあるすべてのコードを、次のように `init` の単一の呼び出しに置き換えます。

```python
init()
```

`experimentation/Diabetes Ridge Regression Scoring.ipynb` で、次の手順を実行します。

1. `run` という新しい関数を作成します。これは、パラメーターとして `raw_data` と `request_headers` を取り、次のように結果のディクショナリを返します。

    ```python
    {"result": result.tolist()}
    ```

1. "Prepare Data" および "Score Data" という見出しの下にあるコードを `run` 関数にコピーします。

    `run` 関数は次のコードのようになります (変数 `raw_data` と `request_headers` を設定するステートメントを忘れずに削除してください。これらは、後で `run` 関数が呼び出されたときに使用されます)。

    ```python
    def run(raw_data, request_headers):
        data = json.loads(raw_data)["data"]
        data = numpy.array(data)
        result = model.predict(data)

        return {"result": result.tolist()}
    ```

`run` 関数が作成されたら、"Prepare Data" および "Score Data" という見出しの下にあるすべてのコードを次のコードに置き換えます。

```python
raw_data = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(raw_data, request_header)
print("Test result: ", prediction)
```

前のコードでは、変数 `raw_data` と `request_header` を設定し、`raw_data` と `request_header` で関数 `run` を呼び出し、予測を出力します。

リファクタリング後、`experimentation/Diabetes Ridge Regression Scoring.ipynb` はマークダウンのない次のコードのようになります。

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, {})
print("Test result: ", prediction)
```

## <a name="combine-related-functions-in-python-files"></a>Python ファイルで関連する関数を結合する

3 番目に、コードの再利用を容易にするために、関連する関数を Python ファイルにマージする必要があります。 このセクションでは、次のノートブック用の Python ファイルを作成します。

- Diabetes Ridge 回帰トレーニング ノートブック (`experimentation/Diabetes Ridge Regression Training.ipynb`)
- Diabetes Ridge 回帰スコアリング ノートブック (`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="create-python-file-for-the-diabetes-ridge-regression-training-notebook"></a>Diabetes Ridge 回帰トレーニング ノートブック用の Python ファイルを作成する

コマンド プロンプトで、`nbconvert` パッケージと `experimentation/Diabetes Ridge Regression Training.ipynb` のパスを使用する次のステートメントを実行して、ノートブックを実行可能スクリプトに変換します。

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Training.ipynb" –output train
```

ノートブックが `train.py` に変換されたら、不要なコメントをすべて削除します。 ファイルの最後にある `main()` の呼び出しは、次のコードのように条件付き呼び出しに置き換えます。

```python
if __name__ == '__main__':
    main()
```

`train.py` ファイルは次のコードのようになります。

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)

if __name__ == '__main__':
    main()
```

これで、ターミナルから `python train.py` を実行して `train.py` を呼び出すことができるようになりました。
`train.py` 内の関数を他のファイルから呼び出すこともできます。

MLOpsPython リポジトリの `diabetes_regression/training` ディレクトリにある `train_aml.py` ファイルは、Azure Machine Learning 実験の実行コンテキストで `train.py` に定義されている関数を呼び出します。 これらの関数を、このガイドで後述している単体テストで呼び出すこともできます。

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Diabetes Ridge 回帰スコアリング ノートブック用の Python ファイルを作成する

コマンド プロンプトで、`nbconvert` パッケージと `experimentation/Diabetes Ridge Regression Scoring.ipynb` のパスを使用する次のステートメントを実行して、ノートブックを実行可能スクリプトに変換します。

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Scoring.ipynb" –output score
```

ノートブックが `score.py` に変換されたら、不要なコメントをすべて削除します。 `score.py` ファイルは次のコードのようになります。

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, request_header)
print("Test result: ", prediction)
```

`model` 変数は、スクリプトのどこからでもアクセスできるようグローバルにする必要があります。 `init` 関数の先頭に次のステートメントを追加します。

```python
global model
```

前のステートメントを追加すると、`init` 関数は次のコードのようになります。

```python
def init():
    global model

    # load the model from file into a global object
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

## <a name="create-unit-tests-for-each-python-file"></a>各 Python ファイルの単体テストを作成する

4 番目に、Python 関数の単体テストを作成します。 単体テストには、機能的な退化からコードを保護し、メンテナンス性を高める効果があります。 このセクションでは、`train.py` の関数に対して単体テストを作成します。

`train.py` には複数の関数が存在しますが、このチュートリアルでは Pytest フレームワークを使用して `train_model` 関数の単体テストを 1 つだけ作成します。 Pytest は、唯一の Python 単体テスト フレームワークではありませんが、とても一般的に使用されているものの 1 つです。 詳細については、[Pytest](https://pytest.org) をご覧ください。

単体テストには通常、次の 3 つの主要なアクションが含まれています。

- オブジェクトを配置する (必要なオブジェクトの作成と設定)
- オブジェクトを操作する
- 予期されるものに対してアサートを行う

単体テストでは、一部のデータと引数をハードコーディングした状態で `train_model` を呼び出し、結果として得られるトレーニング済みのモデルを使用して予測を行います。その予測と期待される値とを比較することで、`train_model` が正しく機能することを検証します。

```python
import numpy as np
from code.training.train import train_model


def test_train_model():
    # Arrange
    X_train = np.array([1, 2, 3, 4, 5, 6]).reshape(-1, 1)
    y_train = np.array([10, 9, 8, 8, 6, 5])
    data = {"train": {"X": X_train, "y": y_train}}

    # Act
    reg_model = train_model(data, {"alpha": 1.2})

    # Assert
    preds = reg_model.predict([[1], [2]])
    np.testing.assert_almost_equal(preds, [9.93939393939394, 9.03030303030303])
```

## <a name="next-steps"></a>次のステップ

ここでは、実験コードから運用環境のコードへの変換方法について説明しました。さらに詳しい情報と次のステップについては、次のリンクを参照してください。

+ [MLOpsPython](https://github.com/microsoft/MLOpsPython/blob/master/docs/custom_model.md): Azure Pipelines と Azure Machine Learning を使用して独自のモデルをトレーニング、評価、デプロイする CI/CD パイプラインを作成します。
+ [Azure ML の実験の実行とメトリックを監視する](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments)
+ [ML Web サービス エンドポイントからのデータを監視および収集する](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)
