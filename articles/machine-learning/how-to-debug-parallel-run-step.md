---
title: ParallelRunStep のトラブルシューティング
titleSuffix: Azure Machine Learning
description: 機械学習パイプラインで ParallelRunStep を使用してエラーが発生した場合のトラブルシューティング方法に関するヒント。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
ms.custom: troubleshooting
ms.reviewer: larryfr, vaidyas, laobri, tracych
ms.author: trmccorm
author: tmccrmck
ms.date: 09/23/2020
ms.openlocfilehash: b5511c8ecc33238e0409b5ee4c1c7a11adddeac5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102522157"
---
# <a name="troubleshooting-the-parallelrunstep"></a>ParallelRunStep のトラブルシューティング

この記事では、[Azure Machine Learning SDK](/python/api/overview/azure/ml/intro) の [ParallelRunStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep) クラスを使用してエラーが発生した場合のトラブルシューティングの方法について説明します。

パイプラインのトラブルシューティングに関する一般的なヒントについては、「[機械学習パイプラインのトラブルシューティング](how-to-debug-pipelines.md)」を参照してください。

## <a name="testing-scripts-locally"></a>スクリプトのローカルでのテスト

 ParallelRunStep は、ML パイプラインのステップとして実行されます。 最初のステップとして[ご使用のスクリプトをローカルでテストする](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines)ことをお勧めします。

##  <a name="script-requirements"></a>スクリプトの要件

`ParallelRunStep` スクリプトには、2 つの関数が "*含まれている必要があります*"。
- `init()`:この関数は、後で推論するためのコストのかかる準備、または一般的な準備を行うときに使用します。 たとえば、これを使って、モデルをグローバル オブジェクトに読み込みます。 この関数は、プロセスの開始時に 1 回だけ呼び出されます。
-  `run(mini_batch)`:この関数は、`mini_batch` インスタンスごとに実行されます。
    -  `mini_batch`: `ParallelRunStep` は run メソッドを呼び出して、そのメソッドに、リストまたは Pandas `DataFrame` のいずれかを引数として渡します。 mini_batch のエントリはそれぞれ、ファイル パス (入力が `FileDataset` の場合) または Pandas `DataFrame` (入力が `TabularDataset` の場合) になります。
    -  `response`: run() メソッドは、Pandas `DataFrame` または配列を返します。 append_row output_action の場合、これらの返される要素は、共通の出力ファイルに追加されます。 summary_only の場合、要素のコンテンツは無視されます。 すべての出力アクションについて、返される出力要素はそれぞれ、入力ミニバッチ内で成功した 1 つの入力要素の実行を示します。 入力を実行出力結果にマップできるだけの十分なデータが実行結果に含まれていることを確認してください。 実行の出力は出力ファイルに書き込まれますが、順序どおりの書き込みは保証されません。出力でいずれかのキーを使って、入力にマップする必要があります。

```python
%%writefile digit_identification.py
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

推論スクリプトと同じディレクトリに他のファイルまたはフォルダーがある場合、現在の作業ディレクトリを特定することでそれらを参照することができます。

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

### <a name="parameters-for-parallelrunconfig"></a>ParallelRunConfig のパラメーター

`ParallelRunConfig` は、Azure Machine Learning パイプライン内にある `ParallelRunStep` インスタンスの主要な構成です。 これは、お使いのスクリプトをラップし、必要なパラメーターを構成するときに使用します。たとえば、次のようなエントリです。
- `entry_script`:複数のノードで並列で実行されるローカル ファイル パスとしてのユーザー スクリプト。 `source_directory` が存在する場合は、相対パスを使用します。 それ以外の場合は、マシンでアクセス可能な任意のパスを使用します。
- `mini_batch_size`:1 つの `run()` 呼び出しに渡されたミニバッチのサイズ (省略可能。既定値は、`FileDataset` の場合は `10` ファイルで、`TabularDataset` の場合は `1MB` です。)
    - `FileDataset` の場合、これはファイル数を示し、最小値は `1` です。 複数のファイルを 1 つのミニバッチに結合できます。
    - `TabularDataset` の場合は、データのサイズです。 サンプル値は、`1024`、`1024KB`、`10MB`、および `1GB` です。 推奨値は `1MB` です。 `TabularDataset` のミニバッチは、ファイル境界を超えません。 たとえば、さまざまなサイズの .csv ファイルがある場合、ファイルの最小サイズは 100 KB で、最大サイズは 10 MB です。 `mini_batch_size = 1MB` を設定すると、1 MB より小さいファイルは 1 つのミニバッチとして処理されます。 1 MB を超えるファイルは、複数のミニバッチに分割されます。
- `error_threshold`:処理中に無視する必要のあるエラーの数。`TabularDataset` の場合はレコード エラー数、`FileDataset` の場合はファイル エラー数を示します。 入力全体に対するエラーの数がこの値を超えると、ジョブは中止されます。 エラーのしきい値は入力全体を対象としています。`run()` メソッドに送信された個々のミニバッチを対象にしているものではありません。 範囲は `[-1, int.max]` です。 `-1` 部分は、処理中にすべてのエラーを無視することを示します。
- `output_action`:次のいずれかの値が、出力がどのように編成されるかを示しています。
    - `summary_only`:ユーザー スクリプトによって出力が格納されます。 `ParallelRunStep` では、エラーしきい値の計算にのみ出力が使用されます。
    - `append_row`:すべての入力について、出力フォルダーにファイルが 1 つだけ作成され、行で区切られたすべての出力が追加されます。
- `append_row_file_name`:append_row output_action の出力ファイル名をカスタマイズします (省略可能。既定値は `parallel_run_step.txt` です)。
- `source_directory`:コンピューティング ターゲットで実行されるすべてのファイルを含むフォルダーへのパス (省略可能)。
- `compute_target`:サポートされるのは `AmlCompute` のみです。
- `node_count`:ユーザー スクリプトの実行に使用されるコンピューティング ノードの数。
- `process_count_per_node`:ノードあたりのプロセスの数。 ベスト プラクティスとして、ノードあたりの GPU または CPU の数に設定します (省略可能。既定値は `1` です)。
- `environment`:Python 環境定義。 既存の Python 環境が使用されるように、または一時的な環境が設定されるように構成できます。 定義で、必要なアプリケーションの依存関係を設定することもできます (省略可能)。
- `logging_level`:ログの詳細。 値は詳細度が低い順に `WARNING`、`INFO`、`DEBUG` です。 (省略可能。既定値は `INFO` です)
- `run_invocation_timeout`:`run()` メソッド呼び出しのタイムアウト (秒単位)。 (省略可能、既定値は `60` です)
- `run_max_try`:ミニバッチに対する `run()` の最大試行回数。 例外がスローされた場合、`run()` は失敗します。`run_invocation_timeout` に到達した場合は何も返されません (省略可能。既定値は `3` です)。 

`mini_batch_size`、`node_count`、`process_count_per_node`、`logging_level`、`run_invocation_timeout`、`run_max_try` を `PipelineParameter` として指定すると、パイプラインの実行を再送信するときに、パラメーターの値を微調整できます。 この例では、`mini_batch_size` と `Process_count_per_node` に `PipelineParameter` を使用し、後で実行を再送信するときに、これらの値を変更します。 

### <a name="parameters-for-creating-the-parallelrunstep"></a>ParallelRunStep を作成するためのパラメーター

スクリプト、環境構成、およびパラメーターを使用して、ParallelRunStep を作成します。 お使いの推論スクリプトの実行の対象としてご自身のワークスペースに関連付けたコンピューティング ターゲットを指定します。 `ParallelRunStep` を使用して、バッチ推論パイプラインのステップを作成します。このステップでは、次のすべてのパラメーターが使用されます。
- `name`:ステップの名前。3 文字以上 32 文字以内で、一意の名前にする必要があります。また、正規表現 ^\[a-z\]([-a-z0-9]*[a-z0-9])?$ を使用できます。
- `parallel_run_config`:`ParallelRunConfig` オブジェクト (前述にて定義)。
- `inputs`:並列処理のためにパーティション分割される 1 つ以上の single 型の Azure Machine Learning データセット。
- `side_inputs`:パーティション分割する必要のないサイド入力として使用される 1 つ以上の参照データまたはデータセット。
- `output`: 出力データが補完されるディレクトリ パスを表す `OutputFileDatasetConfig` オブジェクト。
- `arguments`:ユーザー スクリプトに渡された引数の一覧。 それらを実際のエントリ スクリプト内で取得するには、unknown_args を使用します (省略可能)。
- `allow_reuse`:同じ設定/入力で実行されたときに、ステップで前の結果を再利用するかどうか。 このパラメーターが `False` の場合、パイプラインの実行中、このステップに対して必ず新しい実行が生成されます (省略可能。既定値は `True` です)。

```python
from azureml.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="predict-digits-mnist",
    parallel_run_config=parallel_run_config,
    inputs=[input_mnist_ds_consumption],
    output=output_dir,
    allow_reuse=True
)
```

## <a name="debugging-scripts-from-remote-context"></a>リモート コンテキストからのスクリプトのデバッグ

スコアリング スクリプトのローカルでのデバッグから実際のパイプラインでのデバッグに切り替えることは、大幅な変更であり、簡単ではありません。 ポータルでのログの検索については、[機械学習パイプラインのリモート コンテキストからのスクリプトのデバッグに関するセクション](how-to-debug-pipelines.md)を参照してください。 そのセクションの情報は、ParallelRunStep にも適用されます。

たとえば、ログファイル `70_driver_log.txt` には、ParallelRunStep コードを起動するコントローラーからの情報が含まれています。

ParallelRunStep ジョブには分散型の性質があるため、複数の異なるソースからのログが存在します。 ただし、概要情報を提供する、統合されたファイルが 2 つ作成されます。

- `~/logs/job_progress_overview.txt`:このファイルでは、これまでに作成されたミニバッチ (タスクとも呼ばれます) の数とこれまでに処理されたミニバッチの数に関する概要が示されます。 最後にはジョブの結果が示されます。 ジョブが失敗した場合は、エラー メッセージのほか、どこからトラブルシューティングすればよいかが示されます。

- `~/logs/sys/master_role.txt`:このファイルでは、実行中のジョブのプリンシパル ノード (オーケストレーターとも呼ばれます) が示されます。 タスクの作成、進行状況の監視、実行結果が含まれます。

EntryScript ヘルパーおよび PRINT ステートメントを使用したエントリ スクリプトから生成されたログは、次のファイルにあります。

- `~/logs/user/entry_script_log/<ip_address>/<process_name>.log.txt`:これらのファイルは、EntryScript ヘルパーを使用して entry_script から書き込まれたログです。

- `~/logs/user/stdout/<ip_address>/<process_name>.stdout.txt`:これらのファイルは、entry_script の stdout (PRINT ステートメントなど) のログです。

- `~/logs/user/stderr/<ip_address>/<process_name>.stderr.txt`:これらのファイルは、entry_script の stderr のログです。

スクリプトのエラーを簡潔に理解するために、次のものがあります。

- `~/logs/user/error.txt`:このファイルは、スクリプトのエラーの概要を作成しようとします。

スクリプトのエラーの詳細については、次のものがあります。

- `~/logs/user/error/`:エントリ スクリプトの読み込み中および実行中にスローされた例外の詳細なスタック トレースを格納します。

各ノードによってスコアリング スクリプトがどのように実行されたかを十分に理解する必要がある場合は、ノードごとの各プロセス ログを確認してください。 プロセス ログは、ワーカー ノード別にグループ化されて `sys/node` フォルダーにあります。

- `~/logs/sys/node/<ip_address>/<process_name>.txt`:このファイルは、各ミニバッチがワーカーによって収集または完了される際に、その詳細情報を提供します。 各ミニバッチについて、次の情報が記録されます。

    - ワーカー プロセスの IP アドレスと PID。 
    - 項目の合計数、正常に処理された項目数、および失敗した項目数。
    - 開始時刻、期間、処理時間、および実行メソッドの時間。

また、各ノードのリソース使用率の定期的チェックの結果を表示することもできます。 ログ ファイルとセットアップ ファイルは次のフォルダーにあります。

- `~/logs/perf`:秒単位でチェック間隔を変更するには、`--resource_monitor_interval` を設定します。 既定の間隔は `600` で、これは約 10 分です。 監視を停止するには、値を `0` に設定します。 各 `<ip_address>` フォルダーには次のものが含まれます。

    - `os/`:ノードで実行されているすべてのプロセスに関する情報。 1 回のチェックでオペレーティング システムのコマンドが実行され、その結果がファイルに保存されます。 Linux では、コマンドは `ps`です。 Windows では、`tasklist` を使用します。
        - `%Y%m%d%H`:サブフォルダー名は、time to hour です。
            - `processes_%M`:ファイルは、チェック時間の分で終了します。
    - `node_disk_usage.csv`:ノードの詳細なディスク使用量。
    - `node_resource_usage.csv`:ノードのリソース使用状況の概要。
    - `processes_resource_usage.csv`:各プロセスのリソース使用状況の概要。

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>リモート コンテキストからユーザー スクリプトのログを記録する方法

ParallelRunStep は、process_count_per_node に基づいて、1 つのノードで複数のプロセスを実行できます。 ノード上の各プロセスのログを整理し、PRINT および LOG ステートメントを組み合わせるには、次のように ParallelRunStep ロガーを使用することをお勧めします。 EntryScript からロガーを取得して、ポータルの **logs/user** フォルダーにログが表示されるようにします。

**ロガーを使用したサンプル エントリ スクリプト:**
```python
from azureml_user.parallel_run import EntryScript

def init():
    """ Initialize the node."""
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug("This will show up in files under logs/user on the Azure portal.")


def run(mini_batch):
    """ Accept and return the list back."""
    # This class is in singleton pattern and will return same instance as the one in init()
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug(f"{__file__}: {mini_batch}.")
    ...

    return mini_batch
```

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>ファイルや、参照テーブルを含むファイルなどのサイド入力を担当者全員に渡す方法はありますか。

ユーザーは、ParalleRunStep の side_inputs パラメーターを使用して、参照データをスクリプトに渡すことができます。 side_inputs として提供されるすべてのデータセットは、各ワーカー ノードにマウントされます。 ユーザーは引数を渡すことによって、マウントの場所を取得できます。

参照データを含む[データセット](/python/api/azureml-core/azureml.core.dataset.dataset)を作成し、これをワークスペースに登録します。 これを `ParallelRunStep` の `side_inputs` パラメーターに渡します。 また、`arguments` セクションにそのパスを追加して、マウントされたパスに簡単にアクセスすることもできます。

```python
label_config = label_ds.as_named_input("labels_input")
batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
)
```

その後、次のように、推論スクリプト (init() メソッドなど) でアクセスできるようになります。

```python
parser = argparse.ArgumentParser()
parser.add_argument('--labels_dir', dest="labels_dir", required=True)
args, _ = parser.parse_known_args()

labels_path = args.labels_dir
```

### <a name="how-to-use-input-datasets-with-service-principal-authentication"></a>サービス プリンシパルの認証での入力データセットの使用方法

ユーザーは、ワークスペースで使用されるサービス プリンシパルの認証で入力データセットを渡すことができます。 ParallelRunStep でこのようなデータセットを使用する場合、ParallelRunStep 構成を構築するためにデータセットが登録されている必要があります。

```python
service_principal = ServicePrincipalAuthentication(
    tenant_id="***",
    service_principal_id="***",
    service_principal_password="***")
 
ws = Workspace(
    subscription_id="***",
    resource_group="***",
    workspace_name="***",
    auth=service_principal
    )
 
default_blob_store = ws.get_default_datastore() # or Datastore(ws, '***datastore-name***') 
ds = Dataset.File.from_files(default_blob_store, '**path***')
registered_ds = ds.register(ws, '***dataset-name***', create_new_version=True)
```

## <a name="next-steps"></a>次のステップ

* [Azure Machine Learning パイプラインを示す Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines) に関するページを参照してください。

* [azureml-pipeline-steps](/python/api/azureml-pipeline-steps/azureml.pipeline.steps) パッケージについては、SDK リファレンスを参照してください。 ParallelRunStep クラスのリファレンス [ドキュメント](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep)を参照してください。

* ParallelRunStep でパイプラインを使用するには、[高度なチュートリアル](tutorial-pipeline-batch-scoring-classification.md)に従ってください。 このチュートリアルでは、別のファイルをサイド入力として渡す方法について説明しています。
