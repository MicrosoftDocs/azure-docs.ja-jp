---
title: 'Python スクリプトの実行: モジュール リファレンス'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning で Python スクリプトの実行モジュールを使用し、Python コードを実行する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.custom: devx-track-python
author: likebupt
ms.author: keli19
ms.date: 07/27/2020
ms.openlocfilehash: e3e14001758cadc8df5af3c82cb4386659a59d6a
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87843727"
---
# <a name="execute-python-script-module"></a>Python スクリプトの実行モジュール

この記事では Azure Machine Learning デザイナー (プレビュー) 内の Python スクリプトの実行モジュールについて説明します。

Python コードを実行するには、このモジュールを使用します。 Python のアーキテクチャと設計原則の詳細については、[Azure Machine Learning デザイナーで Python コードを実行する方法](../how-to-designer-python.md)に関するページを参照してください。

Python を使用すると、既存のモジュールではサポートされない次のようなタスクを実行できます。

+ `matplotlib` を使用してデータを可視化する。
+ Python ライブラリを使用してワークスペース内のデータセットとモデルを列挙する。
+ [データのインポート](./import-data.md) モジュールではサポートされていないソースからデータを読み取り、読み込み、操作する。
+ 独自のディープ ラーニング コードを実行する。 

## <a name="supported-python-packages"></a>サポートされている Python パッケージ

Azure Machine Learning で使用されている Python の Anaconda ディストリビューションには、データ処理のための一般的なユーティリティが数多く含まれています。 ここでは、Anaconda バージョンを自動的に更新します。 現行バージョンは次のとおりです。
 -  Python 3.6 の Anaconda 4.5 以上のディストリビューション 

完全な一覧については、「[プレインストールされている Python パッケージ](#preinstalled-python-packages)」セクションを参照してください。

プレインストール一覧に含まれていないパッケージ (*scikit-misc* など) をインストールするには、スクリプトに次のコードを追加します。 

```python
import os
os.system(f"pip install scikit-misc")
```

特に推論の場合は、次のコードを使用してパッケージをインストールした方がパフォーマンスは高くなります。
```python
import importlib.util
package_name = 'scikit-misc'
spec = importlib.util.find_spec(package_name)
if spec is None:
    import os
    os.system(f"pip install scikit-misc")
```

> [!NOTE]
> プレインストール一覧に含まれていないパッケージを必要とする Python スクリプトの実行モジュールがパイプラインに複数含まれている場合は、それぞれのモジュールにそれらのパッケージをインストールしてください。

## <a name="upload-files"></a>ファイルをアップロードする
Python スクリプトの実行モジュールでは、[Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#upload-file-name--path-or-stream-) を使用したファイルのアップロードがサポートされています。

次の例は、Python スクリプトの実行モジュールでイメージ ファイルをアップロードする方法を示しています。

```Python

# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Imports up here can be used to
import pandas as pd

# The entry point function must have two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):

    # Execution logic goes here
    print(f'Input pandas.DataFrame #1: {dataframe1}')

    from matplotlib import pyplot as plt
    plt.plot([1, 2, 3, 4])
    plt.ylabel('some numbers')
    img_file = "line.png"
    plt.savefig(img_file)

    from azureml.core import Run
    run = Run.get_context(allow_offline=True)
    run.upload_file(f"graphics/{img_file}", img_file)

    # Return value must be of a sequence of pandas.DataFrame
    # For example:
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
}
```

パイプラインの実行が終了したら、モジュールの右側のパネルでイメージをプレビューできます

> [!div class="mx-imgBorder"]
> ![アップロードされた画像のプレビュー](media/module/upload-image-in-python-script.png)

## <a name="how-to-configure-execute-python-script"></a>Execute Python Script を構成する方法

Python スクリプトの実行モジュールには、出発点として利用できるサンプル Python コードが含まれています。 Python スクリプトの実行モジュールを構成するには、実行する Python コードと一連の入力を **[Python スクリプト]** ボックスに指定します。

1. **Python スクリプトの実行**モジュールをパイプラインに追加します。

2. デザイナーから、入力に使用するデータセットを追加して **Dataset1** に接続します。 Python スクリプトでは、このデータセットを **DataFrame1** として参照します。

    データセットの使用は任意です。 Python を使用してデータを生成したい場合に使用します。データを直接モジュールにインポートしたい場合は Python コードを使用してください。

    このモジュールは、第 2 のデータセットを **Dataset2** で追加することができます。 Python スクリプトでは、第 2 のデータセットを **DataFrame2** として参照します。

    Azure Machine Learning に格納されたデータセットは、このモジュールに読み込まれた時点で自動的に pandas data.frames に変換されます。

    ![Python 実行入力マップ](media/module/python-module.png)

4. 新しい Python パッケージまたはコードをインクルードするには、それらのカスタム リソースを含む ZIP ファイルを**スクリプト バンドル**で追加します。 **スクリプト バンドル**への入力は、ファイルの種類をデータセットとしてワークスペースにアップロードされた ZIP ファイルであることが必要です。 データセットのアップロードは、 **[データセット]** 資産ページで行うことができます。 デザイナー作成ページの左側のモジュール　ツリーにある **[My datasets]\(マイ データセット\)** リストから、データセット モジュールをドラッグしてください。 

    アップロード済みの ZIP アーカイブに格納されていれば、どのファイルでもパイプラインの実行中に使用できます。 アーカイブにディレクトリ構造が含まれていても、その構造は維持されます。ただしその場合は、**src** というディレクトリをパスの先頭に追加する必要があります。

5. **[Python スクリプト]** ボックスに、有効な Python スクリプトを入力するか貼り付けます。

    > [!NOTE]
    >  スクリプトを記述するときは注意が必要です。 宣言されていない変数やインポートされていないモジュールまたは関数の使用など、構文エラーがないことをご確認ください。 事前にインストールされているモジュールの一覧にも特別な注意を払ってください。 一覧表示されていないモジュールをインポートするには、対応するパッケージを次のようなスクリプトでインストールします。
    >  ``` Python
    > import os
    > os.system(f"pip install scikit-misc")
    > ```
    
    **[Python スクリプト]** ボックスには、データへのアクセスと出力に使用するサンプル コードと共に、いくつかの指示がコメントとして事前に入力されています。 このコードを編集するか置き換える必要があります。 大文字と小文字の区別およびインデントに関する Python の規則に従ってください。

    + スクリプトには、このモジュールのエントリ ポイントとして、`azureml_main` という名前の関数が含まれている必要があります。
    + `Param<dataframe1>` と `Param<dataframe2>` の 2 つの入力引数がスクリプトで使用されていない場合でも、エントリ ポイント関数にはこれらの引数が必要です。
    + 第 3 の入力ポートに接続された ZIP ファイルは解凍されて `.\Script Bundle` ディレクトリに格納されます。Python の `sys.path` には、このディレクトリも追加されます。 

    ZIP ファイルに `mymodule.py` が含まれている場合は、`import mymodule` を使用してインポートすることになります。

    デザイナーに対しては 2 つのデータセットを返すことができます。このとき、データセットは `pandas.DataFrame` 型のシーケンスになっている必要があります。 その他の出力は Python コードで作成し、直接 Azure Storage に書き込むことができます。

6. パイプラインを送信します。Python スクリプトだけを実行する場合は、このモジュールを選択して **[選択項目の実行]** を選択してください。

    すべてのデータおよびコードが仮想マシンに読み込まれ、指定した Python 環境を使用して実行されます。

## <a name="results"></a>結果

埋め込み Python コードによる計算の結果は `pandas.DataFrame` として返す必要があります。そうすることで自動的に Azure Machine Learning データセット形式に変換されます。 その結果は、パイプライン内の他のモジュールで使用することができます。

このモジュールからは、次の 2 つのデータセットが返されます。  
  
+ **Results Dataset 1**: Python スクリプトで最初に返される pandas データフレームによって定義されます。

+ **Result Dataset 2**: Python スクリプトで 2 番目に返される pandas データフレームによって定義されます。

## <a name="preinstalled-python-packages"></a>プレインストールされている Python パッケージ
プレインストールされているパッケージは次のとおりです。
-    adal==1.2.2
-    applicationinsights==0.11.9
-    attrs==19.3.0
-    azure-common==1.1.25
-    azure-core==1.3.0
-    azure-graphrbac==0.61.1
-    azure-identity==1.3.0
-    azure-mgmt-authorization==0.60.0
-    azure-mgmt-containerregistry==2.8.0
-    azure-mgmt-keyvault==2.2.0
-    azure-mgmt-resource==8.0.1
-    azure-mgmt-storage==8.0.0
-    azure-storage-blob==1.5.0
-    azure-storage-common==1.4.2
-    azureml-core==1.1.5.5
-    azureml-dataprep-native==14.1.0
-    azureml-dataprep==1.3.5
-    azureml-defaults==1.1.5.1
-    azureml-designer-classic-modules==0.0.118
-    azureml-designer-core==0.0.31
-    azureml-designer-internal==0.0.18
-    azureml-model-management-sdk==1.0.1b6.post1
-    azureml-pipeline-core==1.1.5
-    azureml-telemetry==1.1.5.3
-    backports.tempfile==1.0
-    backports.weakref==1.0.post1
-    boto3==1.12.29
-    botocore==1.15.29
-    cachetools==4.0.0
-    certifi==2019.11.28
-    cffi==1.12.3
-    chardet==3.0.4
-    click==7.1.1
-    cloudpickle==1.3.0
-    configparser==3.7.4
-    contextlib2==0.6.0.post1
-    cryptography==2.8
-    cycler==0.10.0
-    dill==0.3.1.1
-    distro==1.4.0
-    docker==4.2.0
-    docutils==0.15.2
-    dotnetcore2==2.1.13
-    flask==1.0.3
-    fusepy==3.0.1
-    gensim==3.8.1
-    google-api-core==1.16.0
-    google-auth==1.12.0
-    google-cloud-core==1.3.0
-    google-cloud-storage==1.26.0
-    google-resumable-media==0.5.0
-    googleapis-common-protos==1.51.0
-    gunicorn==19.9.0
-    idna==2.9
-    imbalanced-learn==0.4.3
-    isodate==0.6.0
-    itsdangerous==1.1.0
-    jeepney==0.4.3
-    jinja2==2.11.1
-    jmespath==0.9.5
-    joblib==0.14.0
-    json-logging-py==0.2
-    jsonpickle==1.3
-    jsonschema==3.0.1
-    kiwisolver==1.1.0
-    liac-arff==2.4.0
-    lightgbm==2.2.3
-    markupsafe==1.1.1
-    matplotlib==3.1.3
-    more-itertools==6.0.0
-    msal-extensions==0.1.3
-    msal==1.1.0
-    msrest==0.6.11
-    msrestazure==0.6.3
-    ndg-httpsclient==0.5.1
-    nimbusml==1.6.1
-    numpy==1.18.2
-    oauthlib==3.1.0
-    pandas==0.25.3
-    pathspec==0.7.0
-    pip==20.0.2
-    portalocker==1.6.0
-    protobuf==3.11.3
-    pyarrow==0.16.0
-    pyasn1-modules==0.2.8
-    pyasn1==0.4.8
-    pycparser==2.20
-    pycryptodomex==3.7.3
-    pyjwt==1.7.1
-    pyopenssl==19.1.0
-    pyparsing==2.4.6
-    pyrsistent==0.16.0
-    python-dateutil==2.8.1
-    pytz==2019.3
-    requests-oauthlib==1.3.0
-    requests==2.23.0
-    rsa==4.0
-    ruamel.yaml==0.15.89
-    s3transfer==0.3.3
-    scikit-learn==0.22.2
-    scipy==1.4.1
-    secretstorage==3.1.2
-    setuptools==46.1.1.post20200323
-    six==1.14.0
-    smart-open==1.10.0
-    urllib3==1.25.8
-    websocket-client==0.57.0
-    werkzeug==0.16.1
-    wheel==0.34.2

## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。 
