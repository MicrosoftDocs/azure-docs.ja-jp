---
title: 多数のタスクを送信する - Azure Batch | Microsoft Docs
description: 1 つの Azure Batch ジョブで大量のタスクを効率的に送信する方法
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 08/24/2018
ms.author: lahugh
ms.custom: ''
ms.openlocfilehash: f91d47e1f57fb74575fbdad0a76386b53fb38b1f
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68322517"
---
# <a name="submit-a-large-number-of-tasks-to-a-batch-job"></a>1 つの Batch ジョブに多数のタスクを送信する

大規模な Azure Batch ワークロードを実行しているときに、1 つのジョブに数万、数十、またはそれ以上のタスクを送信したい場合があります。 

この記事では、スループットを大幅に向上して多数のタスクを 1 つの Batch ジョブに送信するためのガイダンスといくつかのコード例を示します。 タスクは送信された後に、Batch キューに格納され、ジョブに指定したプール上で処理されます。

## <a name="use-task-collections"></a>タスク コレクションを使用する

Batch API を使用すると、複数のタスクを 1 つの*コレクション*として 1 つのジョブに 1 つずつ効率的に追加できます。 多数のタスクを追加する場合は、適切なメソッドまたはオーバーロードを使用して、1 つのコレクションとしてタスクを追加するようにします。 一般的に、ジョブの入力ファイルまたはパラメーターのセットに対して反復処理を実行するときにタスクを定義することによって、タスク コレクションを構築します。

1 回の呼び出しで追加できるタスク コレクションの最大サイズは、使用する Batch API によって異なります。

* 次の Batch API では、コレクションが **100 タスク**に制限されています。 この制限は、タスクのサイズに応じて小さくなる可能性があります。たとえば、タスクに多数のリソース ファイルまたは環境変数がある場合などです。

    * [REST API](/rest/api/batchservice/task/addcollection)
    * [Python API](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python)
    * [Node.js API](/javascript/api/azure-batch/task?view=azure-node-latest)

  これらの API を使用する場合、タスクの追加が失敗した場合に、コレクションの制限を満たすように複数のタスクに分割し、エラーを処理して再試行するロジックを用意する必要があります。 タスク コレクションが大きすぎて追加できない場合、要求からエラーが生成されるので、より少ないタスクで再試行する必要があります。

* 次の API は、大規模なタスク コレクションをサポートしており、送信側クライアントの RAM の空きによってのみ制限されます。 これらの API は、タスクの追加が失敗した場合、低レベルな API に対してタスク コレクションを "チャンク" に分割して処理し、再試行します。

    * [.NET API](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet)
    * [Java API](/java/api/com.microsoft.azure.batch.protocol.tasks.addcollectionasync?view=azure-java-stable)
    * Batch CLI テンプレートを使用した [Azure Batch CLI 拡張機能](batch-cli-templates.md)
    * [Python SDK 拡張機能](https://pypi.org/project/azure-batch-extensions/)

## <a name="increase-throughput-of-task-submission"></a>タスク送信のスループットを向上する

多数のタスク コレクションをジョブに追加するには時間がかかる場合があります。たとえば、.NET API を使用して 20,000 件のタスクを追加するには、最大で 1 分かかることがあります。 Batch API とワークロードに応じて、次のうち 1 つまたは複数を変更して、タスクのスループットを向上することができます。

* **タスク サイズ**: 大きなタスクの追加には、小さなタスクの追加よりも時間がかかります。 コレクション内の各タスクのサイズを縮小するには、タスクのコマンド ラインを簡素化する、環境変数の数を減らす、またはタスク実行の要件をより効率的に処理するという方法があります。 たとえば、多数のリソース ファイルを使用する代わりに、プール上の[開始タスク](batch-api-basics.md#start-task)を使用してタスクの依存関係をインストールするか、[アプリケーション パッケージ](batch-application-packages.md)または [Docker コンテナー](batch-docker-container-workloads.md)を使用します。

* **同時実行操作数**: Batch API に応じて、Batch クライアントの同時実行操作の最大数を増やすことでスループットを向上します。 この設定の構成には、.NET API の [BatchClientParallelOptions.MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) プロパティ、または Batch Python SDK 拡張機能の [ TaskOperations.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python) などのメソッドの `threads` パラメーターを使用します (このプロパティは、ネイティブの Batch Python SDK では使用できません)。このプロパティは既定では 1 に設定されていますが、操作のスループットを向上するには高く設定してください。 ネットワーク帯域幅を使用し、CPU のパフォーマンスがいくらか低下しますが、スループットは向上します。 タスクのスループットは、`MaxDegreeOfParallelism` または `threads` の最大 100 倍まで増加します。 実用の面では、同時実行操作数を 100 未満に設定することをお勧めします。 
 
  Azure Batch CLI 拡張機能と Batch テンプレートを使用すると、使用できるコア数に基づいて同時実行操作数が自動的に増えますが、このプロパティは CLI で構成できません。 

* **HTTP 接続の制限**: 多数のタスクを追加するときに、同時実行されている HTTP 接続が多数の場合、Batch クライアントのパフォーマンスが低下する可能性があります。 一部の API では、HTTP 接続の数は制限されています。 たとえば、.NET API を使用して開発する場合、[ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) プロパティは既定で 2 に設定されています。 この値は、同時実行操作数に近い値または大きい値まで増やすことをお勧めします。

## <a name="example-batch-net"></a>例:Batch .NET

次の C# スニペットは、Batch .NET API を使用して多数のタスクを追加するときに構成する設定を示しています。

タスクのスループットを向上するには、[BatchClient](/dotnet/api/microsoft.azure.batch.batchclient?view=azure-dotnet) の [MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) プロパティの値を増やします。 例:

```csharp
BatchClientParallelOptions parallelOptions = new BatchClientParallelOptions()
  {
    MaxDegreeOfParallelism = 15
  };
...
```
[AddTaskAsync](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync?view=azure-dotnet) または [AddTask](/dotnet/api/microsoft.azure.batch.cloudjob.addtask?view=azure-dotnet
) メソッドの適切なオーバーロードを使用して、ジョブにタスク コレクションを追加します。 例:

```csharp
// Add a list of tasks as a collection
List<CloudTask> tasksToAdd = new List<CloudTask>(); // Populate with your tasks
...
await batchClient.JobOperations.AddTaskAsync(jobId, tasksToAdd, parallelOptions);
```


## <a name="example-batch-cli-extension"></a>例:Batch CLI 拡張機能

Azure Batch CLI 拡張機能と [Batch CLI テンプレート](batch-cli-templates.md)を使用して、[タスク ファクトリ](https://github.com/Azure/azure-batch-cli-extensions/blob/master/doc/taskFactories.md)を含むジョブ テンプレート JSON ファイルを作成します。 タスク ファクトリは、1 つのタスク定義からジョブに関連するタスクのコレクションを構成します。  

多数のタスク (この場合は 250,000) がある 1 次元のパラメーターによるスイープ ジョブのサンプル ジョブ テンプレートを次に示します。 タスクのコマンド ラインは単純な `echo` コマンドです。

```json
{
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime":"PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```
テンプレートを使用してジョブを実行するには、[Azure Batch CLI テンプレートの使用とファイル転送](batch-cli-templates.md)に関するページを参照してください。

## <a name="example-batch-python-sdk-extension"></a>例:Batch Python SDK 拡張機能

Azure Batch Python SDK 拡張機能を使用するには、まず Python SDK と拡張機能をインストールします。

```
pip install azure-batch
pip install azure-batch-extensions
```

SDK 拡張機能を使用する `BatchExtensionsClient` を設定します。

```python

client = batch.BatchExtensionsClient(
    base_url=BATCH_ACCOUNT_URL, resource_group=RESOURCE_GROUP_NAME, batch_account=BATCH_ACCOUNT_NAME)
...
```

ジョブに追加するタスクのコレクションを作成します。 例:


```python
tasks = list()
# Populate the list with your tasks
...
```

[task.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations?view=azure-python) を使用してタスク コレクションを追加します。 `threads` パラメーターを設定して同時実行操作数を増やします。

```python
try:
    client.task.add_collection(job_id, threads=100)
except Exception as e:
    raise e
```

Batch Python SDK 拡張機能は、タスク ファクトリの JSON 仕様を使用してジョブにタスク パラメーターを追加する操作もサポートしています。 たとえば、前述の Batch CLI テンプレートの例のようなパラメーターによるスイープのジョブ パラメーターを構成します。

```python
parameter_sweep = {
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime": "PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
...
job_json = client.job.expand_template(parameter_sweep)
job_parameter = client.job.jobparameter_from_json(job_json)
```

ジョブ パラメーターをジョブに追加します。 `threads` パラメーターを設定して同時実行操作数を増やします。

```python
try:
    client.job.add(job_parameter, threads=50)
except Exception as e:
    raise e
```

## <a name="next-steps"></a>次の手順

* [Batch CLI テンプレート](batch-cli-templates.md)に関するページで Azure Batch CLI 拡張機能の使用について学びます。
* [Batch Python SDK 拡張機能](https://pypi.org/project/azure-batch-extensions/)の詳細について学びます。
