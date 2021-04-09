---
title: チュートリアル:"Hello world!" を実行する Python スクリプト
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 入門シリーズの第 2 部では、簡単な "Hello world" Python スクリプトをクラウドに送信する方法について説明します。 Python script to the cloud.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 02/11/2021
ms.custom: devx-track-python
ms.openlocfilehash: 4f2b01b7a04958c4bd1f97332b54a1ff4fc32356
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2021
ms.locfileid: "102522327"
---
# <a name="tutorial-run-a-hello-world-python-script-part-2-of-4"></a>チュートリアル:"Hello world!" Python スクリプトを実行する (パート 2/4) Python script (part 2 of 4)

このチュートリアルでは、Azure Machine Learning SDK for Python を使用して、Python の "Hello World" スクリプトを送信および実行する方法について説明します。  スクリプトを入手してください。

このチュートリアルは、"*4 部構成のチュートリアル シリーズのパート 2*" であり、Azure Machine Learning の基礎を学習し、Azure でジョブベースの機械学習タスクを実行します。 このチュートリアルは、[パート 1 のローカル マシンを Azure Machine Learning 用に設定する方法](tutorial-1st-experiment-sdk-setup-local.md)に関するページで完了した作業に基づいて構築されています。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
> * "Hello world!" Python スクリプトをローカルで作成して実行する。 Python script locally.
> * Azure Machine Learning に "Hello world" を送信する Python コントロール スクリプトを作成する。 to Azure Machine Learning.
> * コントロール スクリプトでの Azure Machine Learning の概念を理解する。
> * "Hello World" スクリプトを送信して実行する  スクリプトを入手してください。
> * クラウドでのコード出力を表示する。

## <a name="prerequisites"></a>前提条件

- まだ Azure Machine Learning ワークスペースがない場合は、[第 1 部](tutorial-1st-experiment-sdk-setup-local.md)を完了してください。

## <a name="create-and-run-a-python-script-locally"></a>Python スクリプトをローカルに作成して実行する

Azure Machine Learning コンピューティング クラスターで実行するコードを格納するために、`tutorial` ディレクトリの下に `src` という名前の新しいサブディレクトリを作成します。 `src` サブディレクトリで、`hello.py` Python スクリプトを作成します。

```python
# src/hello.py
print("Hello world!")
```

プロジェクト ディレクトリの構造は次のようになります。

:::image type="content" source="media/tutorial-1st-experiment-hello-world/directory-structure.png" alt-text="src サブディレクトリの hello.py を示すディレクトリ構造":::


### <a name="test-your-script-locally"></a><a name="test"></a>スクリプトをローカルでテストする

コードは、好みの IDE またはターミナルを使用してローカルで実行できます。 コードのローカル実行には、コードの対話型デバッグができるというメリットがあります。  アクティブになった *tutorial1* Conda 環境が表示されているウィンドウで、次の Python ファイルを実行します。

```bash
cd <path/to/tutorial>
python ./src/hello.py
```

> [!div class="nextstepaction"]
> [スクリプトをローカルで実行しました](?success=run-local#control-script) [問題が発生しました](https://www.research.net/r/7C2NTH7?issue=run-local)

## <a name="create-a-control-script"></a><a name="control-script"></a> コントロール スクリプトを作成する

*コントロール スクリプト* を使用すると、`hello.py` スクリプトをクラウドで実行できます。 コントロール スクリプトを使用すると、機械学習コードを実行する方法と場所を制御できます。  

チュートリアル ディレクトリで、`03-run-hello.py` という名前の新しい Python ファイルを作成し、そのファイルに次のコードをコピーして貼り付けます。

```python
# tutorial/03-run-hello.py
from azureml.core import Workspace, Experiment, Environment, ScriptRunConfig

ws = Workspace.from_config()
experiment = Experiment(workspace=ws, name='day1-experiment-hello')

config = ScriptRunConfig(source_directory='./src', script='hello.py', compute_target='cpu-cluster')

run = experiment.submit(config)
aml_url = run.get_portal_url()
print(aml_url)
```

### <a name="understand-the-code"></a>コードの理解

ここでは、コントロール スクリプトのしくみについて説明します。

:::row:::
   :::column span="":::
      `ws = Workspace.from_config()`
   :::column-end:::
   :::column span="2":::
      [Workspace](/python/api/azureml-core/azureml.core.workspace.workspace) は Azure Machine Learning ワークスペースに接続して、Azure Machine Learning リソースと通信できるようにします。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `experiment =  Experiment( ... )`
   :::column-end:::
   :::column span="2":::
      [Experiment](/python/api/azureml-core/azureml.core.experiment.experiment) は、複数の実行を 1 つの名前で整理する簡単な方法を提供します。 後で実験を使用して、多数の実行の間でメトリックを簡単に比較する方法を確認できます。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig( ... )` 
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) は、`hello.py` コードをラップしてワークスペースに渡します。 名前に示されているように、このクラスを使用して、Azure Machine Learning で _スクリプト_ を _実行_ する方法を _構成_ できます。 スクリプトを実行するコンピューティング ターゲットも指定します。 このコードでは、ターゲットは[セットアップ チュートリアル](tutorial-1st-experiment-sdk-setup-local.md)で作成したコンピューティング クラスターです。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `run = experiment.submit(config)`
   :::column-end:::
   :::column span="2":::
       スクリプトを送信します。 この送信は[実行](/python/api/azureml-core/azureml.core.run%28class%29)と呼ばれます。 実行には、コードの 1 回の実行がカプセル化されています。 実行を使用して、スクリプトの進行状況を監視したり、出力をキャプチャしたり、結果を分析したり、メトリックを視覚化したりすることができます。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `aml_url = run.get_portal_url()` 
   :::column-end:::
   :::column span="2":::
        `run` オブジェクトは、コードの実行に対するハンドルを提供します。 Python スクリプトから出力された URL を使用して、進行状況を Azure Machine Learning スタジオから監視します。  
   :::column-end:::
:::row-end:::

> [!div class="nextstepaction"]
> [コントロール スクリプトを作成しました](?success=create-control-script#submit) [問題が発生しました](https://www.research.net/r/7C2NTH7?issue=create-control-script)

## <a name="submit-and-run-your-code-in-the-cloud"></a><a name="submit"></a> クラウドでコードを送信して実行する

コントロール スクリプトを実行します。そうすると、[セットアップ チュートリアル](tutorial-1st-experiment-sdk-setup-local.md)で作成したコンピューティング クラスター上で `hello.py` が順に実行されます。


```bash
python 03-run-hello.py
```

> [!TIP]
> このコードを実行すると、サブスクリプションへのアクセス権がないというエラーが表示される場合は、認証オプションの詳細について、「[ワークスペースに接続する](how-to-manage-workspace.md?tab=python#connect-multi-tenant)」セクションを参照してください。

> [!div class="nextstepaction"]
> [クラウドでコードを送信しました](?success=submit-to-cloud#monitor) [問題が発生しました](https://www.research.net/r/7C2NTH7?issue=submit-to-cloud)

## <a name="monitor-your-code-in-the-cloud-by-using-the-studio"></a><a name="monitor"></a>スタジオを使用してクラウドでコードを監視する

スクリプトからの出力には、`https://ml.azure.com/experiments/hello-world/runs/<run-id>?wsid=/subscriptions/<subscription-id>/resourcegroups/<resource-group>/workspaces/<workspace-name>` のようなスタジオへのリンクが含まれています。

リンク先に移動します。  最初は、状態が "**Preparing (準備中)** " と表示されます。  最初の実行は、完了までに 5 分から 10 分かかります。 これは、以下のことが行われるためです。

* クラウドで Docker イメージが構築されます
* コンピューティング クラスターのサイズが 0 ノードから 1 ノードに変更されます
* Docker イメージがコンピューティングにダウンロードされます。 

Docker イメージがコンピューティングにキャッシュされるため、それ以降の実行にかかる時間は大幅に短縮されます (約 15 秒)。 これをテストするには、最初の実行が完了した後に、以下のコードを再送信します。

ジョブが完了したら、 **[出力 + ログ]** タブに移動します。次のような `70_driver_log.txt` ファイルが表示されます。

```txt
 1: [2020-08-04T22:15:44.407305] Entering context manager injector.
 2: [context_manager_injector.py] Command line Options: Namespace(inject=['ProjectPythonPath:context_managers.ProjectPythonPath', 'RunHistory:context_managers.RunHistory', 'TrackUserError:context_managers.TrackUserError', 'UserExceptions:context_managers.UserExceptions'], invocation=['hello.py'])
 3: Starting the daemon thread to refresh tokens in background for process with pid = 31263
 4: Entering Run History Context Manager.
 5: Preparing to call script [ hello.py ] with arguments: []
 6: After variable expansion, calling script [ hello.py ] with arguments: []
 7:
 8: Hello world!
 9: Starting the daemon thread to refresh tokens in background for process with pid = 31263
10:
11:
12: The experiment completed successfully. Finalizing run...
13: Logging experiment finalizing status in history service.
14: [2020-08-04T22:15:46.541334] TimeoutHandler __init__
15: [2020-08-04T22:15:46.541396] TimeoutHandler __enter__
16: Cleaning up all outstanding Run operations, waiting 300.0 seconds
17: 1 items cleaning up...
18: Cleanup took 0.1812913417816162 seconds
19: [2020-08-04T22:15:47.040203] TimeoutHandler __exit__
```

8 行目には、"Hello world!" の出力が表示されます。

`70_driver_log.txt` ファイルには、実行からの標準出力が含まれます。 このファイルは、クラウドでのリモート実行をデバッグするときに役立ちます。

> [!div class="nextstepaction"]
> [スタジオでログを確認しました](?success=monitor-in-studio#next-steps) [問題が発生しました](https://www.research.net/r/7C2NTH7?issue=monitor-in-studio)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、単純な "Hello world!"  スクリプトを作成し、Azure で実行しました。 Azure Machine Learning ワークスペースに接続し、実験を作成して、`hello.py` コードをクラウドに送信する方法について説明しました。

次のチュートリアルでは、`print("Hello world!")` よりも興味深いものを実行して、これまでの学習をさらに発展させます。

> [!div class="nextstepaction"]
> [チュートリアル: モデルのトレーニング](tutorial-1st-experiment-sdk-train.md)

>[!NOTE] 
> チュートリアル シリーズをここで終了し、次の手順に進まない場合は、必ず[リソースをクリーンアップ](tutorial-1st-experiment-bring-data.md#clean-up-resources)してください。
