---
title: 'Python スクリプトの実行: モジュール リファレンス'
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service で Python スクリプトの実行モジュールを使用し、Python コードを実行する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 4bd3433db92767f2d0d733ab71e4298fc5e618f8
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128820"
---
# <a name="execute-python-script-module"></a>Python スクリプトの実行モジュール

この記事では、Azure Machine Learning service のビジュアル インターフェイス (プレビュー) のモジュールについて説明します。

Python コードを実行するには、このモジュールを使用します。 Python のアーキテクチャと設計原則の詳細については、[次の記事](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts)を参照してください。

Python を使用すると、既存のモジュールでは現在サポートされていない次のようなタスクを実行できます。

+ `matplotlib` を使用してデータを可視化する
+ Python ライブラリを使用してワークスペース内のデータセットとモデルを列挙する
+ [データのインポート](./import-data.md) モジュールではサポートされていないソースからデータを読み取り、読み込み、操作する
+ 独自のディープ ラーニング コードを実行する 


Azure Machine Learning で使用されている Python の Anaconda ディストリビューションには、データ処理のための一般的なユーティリティが数多く含まれています。 ここでは、Anaconda バージョンを自動的に更新します。 最新のバージョンは次のとおりです。
 -  Python 3.6 の Anaconda 4.5 以上のディストリビューション 

プレインストールされているパッケージは次のとおりです。
-  asn1crypto==0.24.0
- attrs==19.1.0
- azure-common==1.1.18
- azure-storage-blob==1.5.0
- azure-storage-common==1.4.0
- certifi==2019.3.9
- cffi==1.12.2
- chardet==3.0.4
- cryptography==2.6.1
- distro==1.4.0
- idna==2.8
- jsonschema==3.0.1
- lightgbm==2.2.3
- more-itertools==6.0.0
- numpy==1.16.2
- pandas==0.24.2
- Pillow==6.0.0
- pip==19.0.3
- pyarrow==0.12.1
- pycparser==2.19
- pycryptodomex==3.7.3
- pyrsistent==0.14.11
- python-dateutil==2.8.0
- pytz==2018.9
- requests==2.21.0
- scikit-learn==0.20.3
- scipy==1.2.1
- setuptools==40.8.0
- six==1.12.0
- torch==1.0.1.post2
- torchvision==0.2.2.post3
- urllib3==1.24.1
- wheel==0.33.1 

 プレインストール一覧に含まれていない他のパッケージ (*scikit-misc* など) をインストールするには、スクリプトに次のコードを追加します。 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="how-to-use"></a>使用方法

**Python スクリプトの実行**モジュールには、出発点として利用できるサンプル Python コードが含まれています。 **Python スクリプトの実行**モジュールを構成するには、実行する Python コードと一連の入力を **[Python スクリプト]** ボックスに指定します。

1. **Python スクリプトの実行**モジュールを実験に追加します。

2. インターフェイスから、入力に使用するデータセットを追加して **Dataset1** に接続します。 Python スクリプトでは、このデータセットを **DataFrame1** として参照します。

    Python を使用してデータを生成したい場合や、Python コードを使用してデータを直接モジュールにインポートしたい場合、必ずしもデータセットを使用する必要はありません。

    このモジュールは、第 2 のデータセットを **Dataset2** で追加することができます。 Python スクリプトでは、第 2 のデータセットを DataFrame2 として参照します。

    Azure Machine Learning に格納されたデータセットは、このモジュールに読み込まれた時点で自動的に **pandas** data.frames に変換されます。

    ![Python 実行入力マップ](media/module/python-module.png)

4. 新しい Python パッケージまたはコードをインクルードするには、それらのカスタム リソースを含んだ ZIP ファイルを**スクリプト バンドル**で追加します。 **スクリプト バンドル**への入力は、あらかじめワークスペースにアップロードされた ZIP ファイルであることが必要です。 

    アップロード済みの ZIP アーカイブに格納されていれば、どのファイルでも実験の実行中に使用できます。 アーカイブにディレクトリ構造が含まれていても、その構造は維持されます。ただしその場合は、**src** というディレクトリをパスの先頭に追加する必要があります。

5. **[Python スクリプト]** ボックスに、有効な Python スクリプトを入力するか貼り付けます。

    **[Python スクリプト]** ボックスには、データへのアクセスと出力に使用するサンプル コードと共に、いくつかの指示がコメントとして事前に入力されています。 **このコードを編集するか置き換える必要があります。** 大文字と小文字の区別およびインデントに関する Python の規則に必ず従ってください。

    + スクリプトには、このモジュールのエントリ ポイントとして、`azureml_main` という名前の関数が含まれている必要があります。
    + エントリ ポイント関数には、最大 2 つの入力引数を含めることができます (`Param<dataframe1>` および `Param<dataframe2>`)。
    + 第 3 の入力ポートに接続された ZIP ファイルは解凍されて `.\Script Bundle` ディレクトリに格納されます。Python の `sys.path` には、このディレクトリも追加されます。 

    そのため、ZIP ファイルに `mymodule.py` が含まれている場合は、`import mymodule` を使用してインポートすることになります。

    + インターフェイスには 2 つのデータセットを返すことができます。このとき、データセットは `pandas.DataFrame` 型のシーケンスになっている必要があります。 その他の出力は Python コードで作成し、直接 Azure Storage に書き込むことができます。

6. 実験を実行します。Python スクリプトだけを実行する場合は、このモジュールを選択して **[Run selected]\(選択項目の実行\)** をクリックしてください。

    すべてのデータおよびコードが仮想マシンに読み込まれ、指定した Python 環境を使用して実行されます。

## <a name="results"></a>結果

埋め込み Python コードによって実行された計算の結果は pandas.DataFrame として返す必要があります。そうすることで自動的に Azure Machine Learning データセット形式に変換され、その結果を実験内の他のモジュールで使用できるようになります。

このモジュールからは、次の 2 つのデータセットが返されます。  
  
+ **Results Dataset 1**: Python スクリプトで最初に返される pandas データフレームによって定義されます。

+ **Result Dataset 2**: Python スクリプトで 2 番目に返される pandas データフレームによって定義されます。


## <a name="next-steps"></a>次の手順

Azure Machine Learning service で[使用できる一連のモジュール](module-reference.md)を参照してください。 