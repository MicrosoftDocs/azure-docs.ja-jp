---
title: ParallelRunStep のデバッグとトラブルシューティング
titleSuffix: Azure Machine Learning
description: Azure Machine Learning SDK for Python で ParallelRunStep の機械学習パイプラインのデバッグとトラブルシューティングを行います。 パイプラインを使用した開発によくある落とし穴と、リモートからの実行前および実行中にスクリプトをデバッグする際に役立つヒントについて説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: troubleshooting
ms.reviewer: jmartens, larryfr, vaidyas, laobri, tracych
ms.author: trmccorm
author: tmccrmck
ms.date: 07/16/2020
ms.openlocfilehash: 0b914059567e9a513b605ce733407465b625a909
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950019"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>ParallelRunStep のデバッグとトラブルシューティング
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、[Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) の [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) クラスのデバッグとトラブルシューティングを行う方法について説明します。

## <a name="testing-scripts-locally"></a>スクリプトのローカルでのテスト

機械学習パイプラインについては、「[スクリプトのローカルでのテスト](how-to-debug-pipelines.md#debug-scripts-locally)」セクションを参照してください。 ParallelRunStep は、ML パイプラインのステップとして実行されるため、どちらにも同じ回答が当てはまります。

## <a name="debugging-scripts-from-remote-context"></a>リモート コンテキストからのスクリプトのデバッグ

スコアリング スクリプトのローカルでのデバッグから実際のパイプラインでのデバッグに切り替えることは、大幅な変更であり、簡単ではありません。 ポータルでのログの検索については、[機械学習パイプラインのリモート コンテキストからのスクリプトのデバッグに関するセクション](how-to-debug-pipelines.md#finding-and-reading-pipeline-log-files)を参照してください。 そのセクションの情報は、ParallelRunStep にも適用されます。

たとえば、ログファイル `70_driver_log.txt` には、ParallelRunStep コードを起動するコントローラーからの情報が含まれています。

ParallelRunStep ジョブには分散型の性質があるため、複数の異なるソースからのログが存在します。 ただし、概要情報を提供する、統合されたファイルが 2 つ作成されます。

- `~/logs/overview.txt`:このファイルでは、これまでに作成されたミニバッチ (タスクとも呼ばれます) の数とこれまでに処理されたミニバッチの数に関する概要が示されます。 最後にはジョブの結果が示されます。 ジョブが失敗した場合は、エラー メッセージのほか、どこからトラブルシューティングすればよいかが示されます。

- `~/logs/sys/master.txt`:このファイルでは、実行中のジョブのプリンシパル ノード (オーケストレーターとも呼ばれます) が示されます。 タスクの作成、進行状況の監視、実行結果が含まれます。

EntryScript ヘルパーおよび PRINT ステートメントを使用したエントリ スクリプトから生成されたログは、次のファイルにあります。

- `~/logs/user/<ip_address>/<node_name>.log.txt`:これらのファイルは、EntryScript ヘルパーを使用して entry_script から書き込まれたログです。 また、entry_script からの PRINT ステートメント (stdout) も含まれています。

スクリプトのエラーを簡潔に理解するために、次のものがあります。

- `~/logs/user/error.txt`:このファイルは、スクリプトのエラーの概要を作成しようとします。

スクリプトのエラーの詳細については、次のものがあります。

- `~/logs/user/error/`:スローされたすべてのエラーと完全なスタック トレースがノード別に整理されて含まれています。

各ノードによってスコアリング スクリプトがどのように実行されたかを十分に理解する必要がある場合は、ノードごとの各プロセス ログを確認してください。 プロセス ログは、ワーカー ノード別にグループ化されて `sys/node` フォルダーにあります。

- `~/logs/sys/node/<node_name>.txt`:このファイルは、各ミニバッチがワーカーによって収集または完了される際に、その詳細情報を提供します。 各ミニバッチについて、次の情報が記録されます。

    - ワーカー プロセスの IP アドレスと PID。 
    - 項目の合計数、正常に処理された項目数、および失敗した項目数。
    - 開始時刻、期間、処理時間、および実行メソッドの時間。

各ワーカーのプロセスのリソース使用量に関する情報も確認できます。 この情報は、CSV 形式で `~/logs/sys/perf/overview.csv` にあります。 各プロセスに関する情報は、`~logs/sys/processes.csv` で参照できます。

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

参照データを含む[データセット](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)を作成し、これをワークスペースに登録します。 これを `ParallelRunStep` の `side_inputs` パラメーターに渡します。 また、`arguments` セクションにそのパスを追加して、マウントされたパスに簡単にアクセスすることもできます。

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

## <a name="next-steps"></a>次のステップ

* [azureml-pipeline-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) パッケージについては、SDK リファレンスを参照してください。 ParallelRunStep クラスのリファレンス [ドキュメント](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep?view=azure-ml-py)を参照してください。

* ParallelRunStep でパイプラインを使用するには、[高度なチュートリアル](tutorial-pipeline-batch-scoring-classification.md)に従ってください。 このチュートリアルでは、別のファイルをサイド入力として渡す方法について説明しています。 
