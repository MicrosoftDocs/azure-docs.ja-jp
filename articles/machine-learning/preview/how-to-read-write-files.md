---
title: "大規模なデータ ファイルを読み書きする方法 | Microsoft Docs"
description: "Azure ML 実験で大規模なファイルを読み書きする方法"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/10/2017
ms.openlocfilehash: fb3158ef786ad73440a59c07b38476a98ced0768
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="persisting-changes-and-dealing-with-large-files"></a>変更の保持と大規模なファイルの処理

## <a name="execution-isolation-portability-and-reproducibility"></a>実行の分離性、移植性、および再現性
Azure ML 実験サービスでは、ローカル (ローカル コンピューターやローカル コンピューター上の Docker コンテナーなど) およびリモート(リモート マシンの Docker コンテナーや HDInsight クラスターなど) のさまざまな実行ターゲットを構成できます。 詳細については、[実験実行の構成](experiment-execution-configuration.md)に関する記事をご覧ください。 実行が行われる前には必ず、プロジェクト フォルダーがその計算ターゲットにコピーされます。 これは、ローカルの一時フォルダーがそのために使用されるローカル実行の場合でも同じです。 

この設計の目的は、実行の分離性、再現性、および移植性を保証することです。 同じスクリプトを 2 回、同じ計算ターゲットまたは異なる計算ターゲットで実行した場合、同じ結果が得られることが保証されます。 最初の実行によって行われた変更が、2 番目の実行に影響を与えるべきではありません。 この設計により、計算ターゲットを、完了後に実行されるジョブとのアフィニティがないステートレスな計算リソースとして扱うことができます。

## <a name="challenges"></a>課題
この設計には、移植性と再現性というメリットがある一方で、いくつかの固有の課題もあります。
### <a name="persisting-state-changes"></a>状態の変更の保持
スクリプトが計算コンテキストで変更された場合、その変更は次の実行では保持されず、クライアント コンピューターに自動的に反映されることもありません。 

具体的には、スクリプトによってサブフォルダーの作成やファイルの書き出しが行われた場合、そのようなフォルダーやファイルは、実行後のプロジェクト ディレクトリには含まれません。 それらは必ず、計算ターゲット環境の一時フォルダー内に残ります。 デバッグ目的で使用することはできますが、それらに対して依存関係を設定することはできません。

### <a name="dealing-with-large-files-in-project-folder"></a>プロジェクト フォルダー内の大規模なファイルの処理

プロジェクト フォルダーに大規模なファイルが含まれている場合、各実行の開始時にプロジェクト フォルダーがターゲット コンピューティング環境にコピーされるときに待ち時間が発生します。 実行がローカルで行われる場合でも、これは避ける必要がある不要なディスク トラフィックとなります。 そのために、現時点ではプロジェクトの最大サイズが 25 MB に制限されています。

## <a name="option-1-use-the-outputs-folder"></a>方法 1: outputs フォルダーを使用する
これは、スクリプトでファイルが生成され、それらのファイルが実験を実行するたびに変わると想定され、それらのファイルの履歴を保持したい場合に推奨される方法です。 一般的なユース ケースは、モデルのトレーニング、データセットの作成、またはモデル トレーニング実行の一部としてのグラフのイメージ ファイル形式でのプロットです。 また、これらの出力を複数の実行で比較したり、前の実行で生成された出力ファイル (モデルなど) を選択したり、それを後続のタスク (スコア付けなど) に使用したりもできます。

実際には、ルート ディレクトリに対して相対的な `outputs` という名前のフォルダーにファイルを書き込むことができます。 これは、実験サービスから特別な扱いを受ける特殊なフォルダーです。 モデル ファイル、データ ファイル、プロットされたイメージ ファイルなど、実行中にスクリプトによってこのフォルダー内に作成されたもの (まとめて "_アーティファクト_" と呼びます) はすべて、実行が完了すると実験アカウントに関連付けられた Azure Blob Storage アカウントにコピーされます。 これらは、実行履歴レコードの一部になります。

`outputs` フォルダーにモデルを格納する簡単な例を以下に示します。
```python
import os
import pickle

# m is a scikit-learn model. 
# we serialize it into a mode.plk file under the ./outputs folder.
with open(os.path.join('.', 'outputs', 'model.pkl'), 'wb') as f:    
    pickle.dump(m, f)
```
"_アーティファクト_" はダウンロードできます。そのためには、Azure ML Workbench で個別の実行に関する詳細ページの **[出力ファイル]** セクションに移動し、アーティファクトを選択して **[ダウンロード]** ボタンをクリックします。 または、CLI ウィンドウから `az ml asset download` コマンドを使用することもできます。

詳細な例については、_[Classifying Iris]\(アイリスの分類)\_ サンプル プロジェクトの `iris_sklearn.py` Python スクリプトをご覧ください。

## <a name="option-2-use-the-shared-folder"></a>方法 2: 共有フォルダーを使用する
複数の独立した実行でアクセスできる共有フォルダーを使用することは、各実行でこれらのファイルの履歴を保持する必要がない場合には以下のシナリオにとって最適な方法となります。 
- スクリプトがローカルのファイル (csv ファイルなど) やテキスト/イメージ ファイルのディレクトリからトレーニングまたはテスト データとしてデータを読み込む必要がある。 
- スクリプトが生データを処理し、テキスト/イメージ ファイルから、後続のトレーニング実行に使用される中間結果 (特徴を生成したトレーニング データなど) を書き出す。 
- スクリプトがモデルを吐き出し、後続のスコア付けスクリプトがそのモデルを取得して評価に使用する必要がある。 

重要な注意点は、この共有フォルダーが、選択した同じ計算ターゲットにローカルに配置されるという点です。 したがってこの方法は、この共有フォルダーを参照するすべてのスクリプト実行が同じ計算ターゲットで実行され、計算ターゲットが実行間でリサイクルされない場合にのみ機能します。

共有フォルダー機能を使用すると、環境変数 `AZUREML_NATIVE_SHARE_DIRECTORY` によって識別される特殊なフォルダーに対する読み書きを実行できます。 

### <a name="example"></a>例
この共有フォルダーを使用してテキスト ファイルの読み書きを実行するための Python コードの例を以下に示します。
```python
import os

# write to the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', 'wb') as f:
    f.write(“Hello World”)

# read from the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', 'r') as f:
    text = file.read()
```

詳細な例については、_[Classifying Iris]\(アイリスの分類)\_ サンプル プロジェクトの `iris_sklearn_shared_folder.py` ファイルをご覧ください。

この機能を使用する前に、`aml_config` フォルダー内のターゲット実行コンテキストを表す `.compute` ファイルでいくつかの単純な構成を設定する必要があります。 このフォルダーへの実際のパスは、選択するターゲットと構成する値によって異なります。

### <a name="configure-local-compute-context"></a>ローカルの計算コンテキストの構成

ローカルの計算コンテキストでこの機能を有効にするには、"_ローカル_" 環境を表す `.compute` ファイル (通常は `local.compute` という名前) に次の行を追加するだけです。
```
# local.runconfig
...
nativeSharedDirectory: ~/.azureml/share
...
```

`~/.azureml/share` は既定のベース フォルダーのパスです。 これは、スクリプト実行でアクセスできる任意のローカル絶対パスに変更可能です。 実験アカウント名、ワークスペース名、およびプロジェクト名がベース ディレクトリに自動的に追加され、共有ディレクトリの完全なパスになります。 たとえば、前述の既定値を使用した場合、ファイルの書き込み (および取得) は次のパスに対して実行されます。

```
# on Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# on macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

### <a name="configure-docker-compute-context-local-or-remote"></a>Docker 計算コンテキスト (ローカルまたはリモート) の構成
Docker 計算コンテキストでこの機能を有効にするには、ローカルまたはリモートの Docker _.compute_ ファイルに次の 2 行を追加する必要があります。

```
# docker.compute
...
sharedVolumes: true
nativeSharedDirectory: ~/.azureml/share
...
```
>[!IMPORTANT]
>`AZUREML_NATIVE_SHARE_DIRECTORY` 環境変数を使用して共有フォルダーにアクセスする場合は、`sharedVolume` を `true` に設定する必要があります。それ以外の場合、実行は失敗します。

Docker コンテナーで実行されるコードは、常にこの共有フォルダーを `/azureml-share/` と見なします。 Docker コンテナーから見たこのフォルダー パスは構成できません。 また、コードでこのフォルダー名への依存関係を設定しないでください。 代わりに、常に環境変数名 `AZUREML_NATIVE_SHARE_DIRECTORY` を使用してこのフォルダーを指します。 これは Docker ホスト コンピューター/計算コンテキスト上のローカル フォルダーにマップされます。 このローカル フォルダーの基本ディレクトリは、`.compute` ファイル内の `nativeSharedDirectory` 設定の構成可能な値です。 ホスト コンピューター上の共有フォルダーのローカル パスは、上記の既定値を使用する場合、次のようになります。
```
# Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/

# Ubuntu Linux
/home/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

>[!TIP]
>ローカル ディスク上の共有フォルダーのパスが、ローカル計算コンテキストとローカル Docker 計算コンテキストの間で同じであることに注意してください。 これは、ローカル実行とローカル Docker 実行の間でファイルを共有できることを意味します。

これらのフォルダーに入力データを直接配置して、そのマシン上のローカル実行または Docker 実行に取得させることができます。 また、ローカル実行または Docker 実行からこのフォルダーにファイルを書き込んで、ファイルがそのフォルダー内で実行ライフサイクルを超えて保持されるようにすることもできます。

Azure ML 実行サービスの構成ファイルの詳細については、[実行構成ファイル](experiment-execution-configuration-reference.md)に関する記事をご覧ください。

>[!NOTE]
>`AZUREML_NATIVE_SHARE_DIRECTORY` 環境変数は、HDInsight 計算コンテキストではサポートされていません。 ただし、アタッチされた BLOB ストレージに対する読み書きを行うための絶対 WASB パスを明示的に使用することで、同じ結果を簡単に得ることができます。

## <a name="option-3-use-external-durable-storage"></a>方法 3: 外部の永続ストレージを使用する

もちろん、外部の永続ストアを使用して実行時の状態を保持することもできます。 この方法は以下のシナリオで役に立ちます。
- 入力データが、ターゲット計算環境からアクセスできる永続ストレージに既に格納されている。
- これらのファイルを実行履歴レコードに含める必要はない。
- これらのファイルを、異なるコンピューティング環境にわたる複数の実行で共有する必要がある。
- これらのファイルを、計算コンテキスト自体を超えて保持する必要がある。

そのような例の 1 つが、Python/PySpark コードからの Azure BLOB ストレージの使用です。

Python コードから Azure BLOB ストレージを使用する簡単な例を次に示します。
```python
from azure.storage.blob import BlockBlobService
import glob
import os

ACCOUNT_NAME = "<your blob storage account name>"
ACCOUNT_KEY = "<account key>"
CONTAINER_NAME = "<container name>"

blob_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

## Create a new container if necessary, or use an existing one
my_service.create_container(CONTAINER_NAME, fail_on_exist=False, public_access=PublicAccess.Container)

# df is a pandas DataFrame
df.to_csv('mydata.csv', sep='\t', index=False)

# Export the mydata.csv file to blob storage.
for name in glob.iglob('mydata.csv'):
    blob_service.create_blob_from_path(CONTAINER_NAME, 'single_file.csv', name)
```

また、任意の Azure Blob Storage を HDI Spark ランタイムにアタッチするための簡単な例を次に示します。
```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)
 
attach_storage_container(spark, "<storage account name>", "<storage key>”)
```

## <a name="conclusion"></a>まとめ
Azure ML はプロジェクト フォルダー全体をターゲット計算コンテキストにコピーすることでスクリプトを実行するため、大規模な入力、出力、および中間ファイルには特に注意する必要があります。 大規模なファイルのトランザクションには、特殊な `outputs` フォルダー、`AZUREML_NATIVE_SHARE_DIRECTORY` 環境変数を使用してアクセスできる共有フォルダー、または外部の永続ストレージを使用できます。 

## <a name="next-steps"></a>次のステップ
- [実験実行の構成](experiment-execution-configuration-reference.md)を確認します。
- [Classifying Iris](tutorial-classifying-iris-part-1.md) チュートリアル プロジェクトで、`outputs` フォルダーを使用してトレーニング済みモデルを保持する方法を確認する。