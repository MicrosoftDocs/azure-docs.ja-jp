---
title: 自動 ML 実験のトラブルシューティング
titleSuffix: Azure Machine Learning
description: 自動機械学習の実験における問題のトラブルシューティングと解決方法について説明します。
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 03/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, automl, references_regions
ms.openlocfilehash: 28aac830326d60161f54d7ad5fa03326c1d66462
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563676"
---
# <a name="troubleshoot-automated-ml-experiments-in-python"></a>Python での自動 ML 実験のトラブルシューティング

このガイドでは、[Azure Machine Learning SDK](/python/api/overview/azure/ml/intro) を使用して、自動機械学習の実験の既知の問題を特定して解決する方法について説明します。

## <a name="version-dependencies"></a>バージョンの依存関係

**新しいパッケージ バージョンへの `AutoML` 依存関係には、互換性がありません**。 SDK バージョン 1.13.0 以降では、以前の `AutoML` パッケージでピン留めされた古いバージョンと、現在ピン留めされている新しいバージョンとの間に互換性がないため、モデルが以前の SDK に読み込まれません。

次のようなエラーが発生します。

* 次のような、モジュールが見つからないエラー

  `No module named 'sklearn.decomposition._truncated_svd`

* 次のような、インポート エラー

  `ImportError: cannot import name 'RollingOriginValidator'`,
* 次のような、属性エラー

  `AttributeError: 'SimpleImputer' object has no attribute 'add_indicator`

解決策は、お使いの `AutoML` SDK のトレーニング バージョンによって異なります。

* `AutoML` SDK トレーニングのバージョンが 1.13.0 より新しい場合、`pandas == 0.25.1` と `scikit-learn==0.22.1` が必要です。

    * バージョンが一致しない場合、次に示すように、scikit-learn と pandas を適切なバージョンにアップグレードします。

      ```bash
          pip install --upgrade pandas==0.25.1
          pip install --upgrade scikit-learn==0.22.1
      ```

* `AutoML` SDK トレーニングのバージョンが 1.12.0 以前の場合、`pandas == 0.23.4` と `sckit-learn==0.20.3` が必要です。
  * バージョンが一致しない場合、次に示すように、scikit-learn と pandas を適切なバージョンにダウングレードします。
  
    ```bash
      pip install --upgrade pandas==0.23.4
      pip install --upgrade scikit-learn==0.20.3
    ```

## <a name="setup"></a>セットアップ

バージョン 1.0.76 以降の `AutoML` パッケージでの変更により、新しいバージョンに更新する前に、以前のバージョンをアンインストールする必要があります。

* **`ImportError: cannot import name AutoMLConfig`**

    v1.0.76 より前の SDK バージョンから v1.0.76 以降にアップグレードした後でこのエラーが発生した場合は、`pip uninstall azureml-train automl` を実行してから `pip install azureml-train-automl` を実行してエラーを解決します。 これは、automl_setup.cmd スクリプトによって自動的に行われます。

* **automl_setup が失敗する**

  * Windows の場合は、Anaconda プロンプトから automl_setup を実行します。 [Miniconda をインストールします](https://docs.conda.io/en/latest/miniconda.html)。

  * Conda 64 ビット バージョン 4.4.10 以降がインストールされていることを確認します。 ビットは、`conda info` コマンドを使用して確認できます。 `platform` は、Windows の場合は `win-64`、Mac の場合は `osx-64` にする必要があります。 バージョンは、コマンド `conda -V` を使用して確認できます。 以前のバージョンがインストールされている場合は、`conda update conda` コマンドを使用して更新できます。 実行して 32 ビットを確認します 

  * Conda がインストールされていることを確認します。 

  * Linux - `gcc: error trying to exec 'cc1plus'`

    1. `gcc: error trying to exec 'cc1plus': execvp: No such file or directory` エラーが発生した場合は、Linux ディストリビューション用の GCC ビルド ツールをインストールします。 たとえば、Ubuntu では、コマンド `sudo apt-get install build-essential` を使用します。

    1. 新しい名前を最初のパラメーターとして automl_setup に渡して、新しい Conda 環境を作成します。 `conda env list` を使用して既存の Conda 環境を表示し、`conda env remove -n <environmentname>` を使用してそれらを削除します。

* **automl_setup_linux が失敗する**:Ubuntu Linux で automl_setup_linus.sh がエラーで失敗する場合: `unable to execute 'gcc': No such file or directory`

  1. 送信ポート 53 および 80 が有効になっていることを確認します。 Azure の仮想マシンでこれを行うには、Azure portal でその VM を選択し、 **[ネットワーク]** をクリックします。
  1. `sudo apt-get update` コマンドを実行します
  1. `sudo apt-get install build-essential --fix-missing` コマンドを実行します
  1. `automl_setup_linux.sh` をもう一度実行します

* **configuration.ipynb が失敗する**:

  * ローカルの Conda の場合は、最初に `automl_setup` が正常に実行されていることを確認します。
  * subscription_id が正しいことを確認します。 Azure portal で [すべてのサービス]、[サブスクリプション] の順に選択して、subscription_id を見つけます。 subscription_id 値に文字 "<" と ">" を含めることはできません。 たとえば、`subscription_id = "12345678-90ab-1234-5678-1234567890abcd"` は有効な形式です。
  * 共同作成者または所有者がサブスクリプションにアクセスできることを確認します。
  * リージョンがサポートされているリージョン (`eastus2`、`eastus`、`westcentralus`、`southeastasia`、`westeurope`、`australiaeast`、`westus2`、`southcentralus`) のいずれかであることを確認します。
  * Azure portal を使用してリージョンにアクセスできることを確認します。
  
* **workspace.from_config が失敗する**:

  呼び出し `ws = Workspace.from_config()` が失敗する場合:

  1. configuration.ipynb ノートブックが正常に実行されていることを確認します。
  1. ノートブックが、`configuration.ipynb` が実行されたフォルダーの配下ではないフォルダーから実行されている場合は、フォルダー aml_config と、それに含まれているファイル config.json を新しいフォルダーにコピーします。 Workspace.from_config により、ノートブック フォルダーまたはその親フォルダーの config.json が読み取られます。
  1. 新しいサブスクリプション、リソース グループ、ワークスペース、またはリージョンが使用されている場合は、もう一度 `configuration.ipynb` ノートブックを実行してください。 指定されたサブスクリプションの指定されたリソース グループにワークスペースが既に存在する場合にのみ、config.json を直接変更することができます。
  1. リージョンを変更する場合は、ワークスペース、リソース グループ、またはサブスクリプションを変更します。 指定されたリージョンが異なる場合でも、ワークスペースが既に存在する場合は、`Workspace.create` によりワークスペースが作成または更新されることはありません。

## <a name="tensorflow"></a>TensorFlow

SDK のバージョン 1.5.0 以降の自動機械学習では、TensorFlow モデルは既定ではインストールされません。 自動 ML 実験で TensorFlow をインストールして使用するには、`CondaDependencies` を使用して `tensorflow==1.12.0` をインストールします。

```python
  from azureml.core.runconfig import RunConfiguration
  from azureml.core.conda_dependencies import CondaDependencies
  run_config = RunConfiguration()
  run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['tensorflow==1.12.0'])
```

## <a name="numpy-failures"></a>Numpy の失敗

* **`import numpy` が Windows で失敗する**:一部の Windows 環境で、Python の最新バージョン 3.6.8 を使用した NumPy の読み込みでエラーが発生することがあります。 この問題が発生した場合は、Python バージョン 3.6.7 を試してください。

* **`import numpy` が失敗する**:自動 ML Conda 環境で TensorFlow のバージョンを確認します。 サポートされているバージョンは、1.13 未満です。 バージョンが 1.13 以降の場合は、環境から TensorFlow をアンインストールします。

TensorFlow のバージョンを確認し、アンインストールするには、次のようにします。

  1. コマンド シェルを起動し、自動 ML パッケージがインストールされている Conda 環境をアクティブにします。
  1. `pip freeze` を入力して `tensorflow`を探します。見つかった場合は、表示されるバージョンは 1.13 未満になるはずです。
  1. 表示されているバージョンがサポート対象のバージョンでない場合は、コマンド シェルで `pip uninstall tensorflow` を入力し、確認のために「y」を入力します。

## `jwt.exceptions.DecodeError`

正確なエラー メッセージ: `jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()`。

SDK のバージョンが 1.17.0 以前の場合、サポートされていないバージョンの PyJWT がインストールされる可能性があります。 自動 ML Conda 環境の PyJWT バージョンが、サポートされているバージョンであることを確認します。 つまり、バージョン 2.0.0 より前の PyJWT です。

PyJWT のバージョンは次のようにして確認できます。

1. コマンド シェルを起動し、自動 ML パッケージがインストールされている Conda 環境をアクティブにします。

1. `pip freeze` を入力して `PyJWT`を探します。見つかった場合は、表示されるバージョンは 2.0.0 未満になるはずです。

表示されているバージョンがサポートされているバージョンでない場合:

1. 最新バージョンの 自動 ML SDK へのアップグレードを検討してください: `pip install -U azureml-sdk[automl]`

1. それが実行できない場合は、次のように環境から PyJWT をアンインストールし、適切なバージョンをインストールします。

    1. コマンド シェルで `pip uninstall PyJWT` を実行し、確認のために「`y`」を入力します。
    1. `pip install 'PyJWT<2.0.0'` を使用してインストールします。
  
## <a name="databricks"></a>Databricks
[Databricks を使用して自動 ML 実験を構成する方法](how-to-configure-databricks-automl-environment.md#troubleshooting)に関する記事を参照してください。

## <a name="forecasting-r2-score-is-always-zero"></a>R2 スコアの予測が常にゼロになる

 この問題は、指定されたトレーニング データに、最後の `n_cv_splits` + `forecasting_horizon` データポイントと同じ値を含む時系列がある場合に発生します。

時系列でこのパターンが想定される場合は、プライマリ メトリックを **正規化された二乗平均平方根誤差** に切り替えることができます。

## <a name="failed-deployment"></a>失敗したデプロイ

 SDK のバージョンが 1.18.0 以前の場合、デプロイ用に作成された基本イメージが、次のエラーで失敗することがあります: `ImportError: cannot import name cached_property from werkzeug`。

  この問題を回避するには、次の手順を実行します。

  1. モデル パッケージをダウンロードする
  1. パッケージの解凍
  1. 解凍した資産を使用してデプロイする

## <a name="azure-functions-application"></a>Azure Functions アプリケーション
  
  自動化された ML では現在、Azure Functions アプリケーションがサポートされていません。 

## <a name="sample-notebook-failures"></a>サンプル ノートブックの失敗

 プロパティ、メソッド、またはライブラリが存在しないというエラーでサンプル ノートブックが失敗する場合:

* Jupyter Notebook で正しいカーネルが選択されていることを確認します。 カーネルがノートブック ページの右上に表示されます。 既定値は *azure_automl* です。 カーネルはノートブックの一部として保存されます。 新しい Conda 環境に切り替える場合は、ノートブックで新しいカーネルを選択する必要があります。

  * Azure Notebooks の場合は、Python 3.6 にする必要があります。
  * ローカルの Conda 環境の場合は、automl_setup で指定した Conda 環境名にする必要があります。

* ノートブックが、使用している SDK のバージョンに対応していることを確認するには、次のようにします。
  * Jupyter Notebook のセルで `azureml.core.VERSION` を実行して SDK のバージョンを確認します。
  * 以前のバージョンのサンプル ノートブックは、こちらの手段に従って GitHub からダウンロードできます。
    1. `Branch` ボタンを選択します
    1. `Tags` タブに移動します
    1. バージョンを選択します

## <a name="next-steps"></a>次のステップ

+ [自動機械学習を使用して回帰モデルをトレーニングする方法](tutorial-auto-train-models.md)または[リモート リソースに対して自動機械学習を使用してトレーニングする方法](how-to-auto-train-remote.md)についてさらに詳しく学習する。

+ [モデルをデプロイする方法と場所](how-to-deploy-and-where.md)についてさらに詳しく学習する。
