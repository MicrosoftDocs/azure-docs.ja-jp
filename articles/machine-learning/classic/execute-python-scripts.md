---
title: 'ML Studio (classic): Python スクリプトを実行する - Azure'
description: Python スクリプトの実行モジュールを使用して、Machine Learning Studio (クラシック) の実験や Web サービスで Python コードを使用する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: devx-track-python, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: 374589212b230e6b3ce0abcadbad8aa3eb7271fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "100519985"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio (クラシック) で Python Machine Learning スクリプトを実行する

**適用対象:** ![適用対象: ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (classic)   ![適用対象外: ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Python は、多くのデータ サイエンティストにとって貴重なツールです。 これは、データの探索、特徴の抽出、モデルのトレーニングと検証、デプロイなど、一般的な機械学習ワークフローのすべての段階で使用されます。

この記事では、Python スクリプトの実行モジュールを使用して、Azure Machine Learning Studio (クラシック) の実験や Web サービスで Python コードを使用する方法について説明します。

## <a name="using-the-execute-python-script-module"></a>Python スクリプトの実行モジュールの使用

Studio (クラシック) での Python への主要なインターフェイスは、[Python スクリプトの実行][execute-python-script]モジュールを経由します。 [R スクリプトの実行][execute-r-script]モジュールと同様に、最大 3 つの入力が受け付けられて、最大 2 つの出力が生成されます。 Python コードは、`azureml_main` という特別に指定されたエントリポイント関数によってパラメーター ボックスに入力されます。

![Python スクリプトの実行モジュール](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![モジュール パラメーター ボックス内の Python のサンプル コード](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>入力パラメーター

Python モジュールへの入力は、Pandas DataFrames として公開されます。 `azureml_main` 関数は、最大 2 つの省略可能な Pandas DataFrames をパラメーターとして受け取ります。

入力ポートと関数パラメーター間のマッピングは位置指定されます。

- 最初に接続された入力ポートは、関数の最初のパラメーターにマッピングされます。
- 2 番目の入力は (接続されている場合)、関数の 2 番目のパラメーターにマッピングされます。
- 3 番目の入力は、[追加の Python モジュールをインポート](#import-modules)するために使用されます。

入力ポートが `azureml_main` 関数のパラメーターにマッピングされる方法の詳細なセマンティクスを、以下に示します。

![入力ポートの構成と結果の Python 署名の表](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>出力の戻り値

`azureml_main` 関数は、Python [シーケンス](https://docs.python.org/2/c-api/sequence.html) (タプル、リスト、NumPy 配列など) 内にパッケージ化された 1 つの Pandas DataFrame を返す必要があります。 このシーケンスの最初の要素は、モジュールの最初の出力ポートに返されます。 モジュールの 2 番目の出力ポートは[視覚化](#visualizations)のために使用され、戻り値を必要としません。 このスキームを次に示します。

![パラメーターに入力ポートをマッピングし、出力ポートに値を返す](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>入力と出力のデータ型の変換

Studio データセットは Panda DataFrames と同じではありません。 そのため、Studio (クラシック) の入力データセットは Pandas DataFrame に変換され、出力の DataFrame は元の Studio (クラシック) データセットに変換されます。 この変換プロセス中には、次の変換も実行されます。

 **Python データ型** | **Studio 変換手順** |
| --- | --- |
| 文字列と数値| 現状のまま変換される |
| Pandas 'NA' | '欠損値’ として変換される |
| インデックス ベクター | サポートされていません* |
| 文字列以外の列名 | 列名で `str` を呼び出す |
| 重複する列名 | (1)、(2)、(3) などの数値サフィックスを追加します。

**Python 関数内のすべての入力データ フレームには必ず、0 から、行の数から 1 を引いた数までの範囲の、64 ビットの数値インデックスがあります*

## <a name="importing-existing-python-script-modules"></a><a id="import-modules"></a>既存の Python スクリプト モジュールをインポートする

Python の実行に使用されるバックエンドは、広く使用されている科学的な Python ディストリビューションである [Anaconda](https://www.anaconda.com/distribution/) に基づいています。 データ中心のワークロードで最もよく使用される Python パッケージが 200 個近く付属しています。 Studio (クラシック) は、外部ライブラリのインストールと管理のための Pip や Conda などのパッケージ管理システムの使用を、現時点ではサポートしていません。  追加のライブラリを組み込む必要がある場合は、ガイドとして次のシナリオを使用してください。

一般的な使用例は、既存の Python スクリプトを Studio (クラシック) の実験に組み込むことです。 [Python スクリプトの実行][execute-python-script]モジュールは、3 番目の入力ポートでの Python モジュールを含む zip ファイルを受け取ります。 このファイルは実行時に実行フレームワークによって解凍され、その内容が Python インタープリターのライブラリ パスに追加されます。 `azureml_main` エントリ ポイント関数がこれらのモジュールを直接インポートできます。 

たとえば、単純な "Hello, World" 関数を含む Hello.py ファイルについて考えてみます。

![Hello.py ファイル内のユーザー定義関数](./media/execute-python-scripts/figure4.png)

次に、Hello.py を含む Hello.zip ファイルを作成します。

![ユーザー定義の Python コードを含む zip ファイル](./media/execute-python-scripts/figure5.png)

この zip ファイルをデータセットとして Studio (クラシック) にアップロードします。 次にそれを、次の図に示すように、**Python スクリプトの実行** モジュールの 3 番目の入力ポートに追加することで、Hello.zip ファイルで Python コードを使用する実験を作成し、実行します。

![Hello.zip を Python スクリプトの実行モジュールへの入力として使用した実験のサンプル](./media/execute-python-scripts/figure6a.png)

![zip ファイルとしてアップロードされたユーザー定義の Python コード](./media/execute-python-scripts/figure6b.png)

モジュール出力は、zip ファイルがパッケージ解除されたこと、および関数 `print_hello` が実行されたことを示しています。

![ユーザー定義関数を示しているモジュール出力](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>Azure Storage BLOB へのアクセス

Azure BLOB Storage アカウントに保存されているデータには、次の手順を使用してアクセスできます。

1. [Python 用 Azure Blob Storage パッケージ](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip)をローカルでダウンロードします。
1. この zip ファイルを Studio (クラシック) ワークスペースにデータセットとしてアップロードします。
1. `protocol='http'` を使用して BlobService オブジェクトを作成する

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. ストレージの **[構成]** 設定タブで **[安全な転送が必須]** を無効にする

![Azure portal で [安全な転送が必須] を無効にする](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>Python スクリプトの操作

スコア付け実験で使用されるすべての [Python スクリプトの実行][execute-python-script]モジュールは、Web サービスとして公開された場合に呼び出されます。 たとえば、次の図は、1 つの Python 式を評価するコードを含む、スコア付け実験を示しています。

![Web サービスの Studio ワークスペース](./media/execute-python-scripts/figure3a.png)

![Python Pandas 式](./media/execute-python-scripts/python-script-with-python-pandas.png)

この実験から作成される Web サービスは、以下の操作を実行します。

1. Python 式を入力として (文字列として) 受け取る
1. Python 式を Python インタープリターに送信する
1. 式と評価結果の両方を含むテーブルを返す。

## <a name="working-with-visualizations"></a><a id="visualizations"></a>視覚化の操作

MatplotLib を使用して作成されたプロットは、[Python スクリプトの実行][execute-python-script]によって返されます。 ただし、R を使用している場合、プロットは自動的にリダイレクトされません。このため、ユーザーはすべてのプロットを PNG ファイルに明示的に保存する必要があります。

MatplotLib からイメージを生成するには、次の手順を実行する必要があります。

1. 既定の Qt ベース レンダラーから "AGG" にバックエンドを切り替える。
1. 新しい figure オブジェクトを作成する。
1. 軸を取得し、そこにすべてのプロットを生成する。
1. PNG ファイルに図を保存する。

次の図に、Pandas の scatter_matrix 関数を使用して散布図マトリックスを作成するプロセスを示します。

![イメージに MatplotLib の図を保存するコード](./media/execute-python-scripts/figure-v1-8.png)

![Python スクリプトの実行モジュールで [視覚化] をクリックして、図を表示する](./media/execute-python-scripts/figure-v2-9a.png)

![Python コードを使用したサンプル実験のプロットの視覚化](./media/execute-python-scripts/figure-v2-9b.png)

さまざまなイメージに保存することで複数の図を返すことができます。 Studio (クラシック) ランタイムは、すべてのイメージを取得して、視覚化のために連結します。

## <a name="advanced-examples"></a>高度な例

Studio (クラシック) にインストールされた Anaconda 環境には、NumPy、SciPy、Scikits-Learn などの共通のパッケージが含まれています。 これらのパッケージは、機械学習パイプラインでのデータ処理のために効果的に使用できます。

たとえば、次の実験とスクリプトは、データセットの特徴の重要度スコアを計算する、Scikits-Learn のアンサンブル学習者の使い方を示しています。 このスコアは、別のモデルに供給する前に、監視対象の特徴の選択を実行するために使用できます。

以下に、重要度スコアを計算し、そのスコアに基づいて特徴を順位付けるために使用される Python 関数を示します。

![スコアによって特徴を順位付ける関数](./media/execute-python-scripts/figure8.png)

次の実験では、Azure Machine Learning Studio (classic) の "ピマ インディアン糖尿病" データセットにおける特徴の重要度スコアを計算して返します。

![Python を使用したピマ インディアン糖尿病の特徴の順位付けの実験](./media/execute-python-scripts/figure9a.png)

![Python スクリプトの実行モジュールの出力の視覚化](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>制限事項

[Python スクリプトの実行][execute-python-script]モジュールには、現在、次のような制限があります。

### <a name="sandboxed-execution"></a>セキュリティで保護された実行

Python ランタイムは、現在セキュリティで保護されているため、永続的な方法でのネットワークやローカル ファイル システムへのアクセスを許可しません。 ローカルに保存されているすべてのファイルは分離され、モジュールが終了すると削除されます。 Python コードは、現在のディレクトリとそのサブディレクトリを除く、実行中のコンピューターのほとんどのディレクトリにアクセスできません。

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>高度な開発とデバッグ サポートの欠如

Python モジュールは、現在、Intellisense やデバッグなどの IDE 機能をサポートしていません。 また、実行時にモジュールが失敗する場合は、Python の完全なスタック トレースを使用できます。 ただし、モジュールの出力ログで参照する必要があります。 現在のところ、IPython などの環境で Python スクリプトの開発とデバッグを行い、そのコードをモジュールにインポートすることをお勧めしています。

### <a name="single-data-frame-output"></a>1 つのデータ フレームの出力

Python のエントリ ポイントは、1 つのデータ フレームのみを出力として返すことができます。 トレーニング済みのモデルなどの任意の Python オブジェクトを元の Studio (クラシック) ランタイムに直接返すことは現在できません。 同じ制限がある [R スクリプトの実行][execute-r-script]と同様に、多くの場合、バイト配列にオブジェクトを変換され、データ フレーム内のその部分を返されます。

### <a name="inability-to-customize-python-installation"></a>Python のインストールをカスタマイズできない

現時点では、カスタムの Python モジュールを追加する唯一の方法は、前に説明した zip ファイルのメカニズムを使用することです。 これは、小さなモジュールに適していますが、大きなモジュール (特にネイティブ DLL を使用するモジュール) や大量のモジュールでは、使用が面倒です。

## <a name="next-steps"></a>次のステップ

詳細については、 [Python デベロッパー センター](https://azure.microsoft.com/develop/python/)を参照してください。

<!-- Module References -->
[execute-python-script]: /azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script