---
title: タスクの同時実行による Batch コンピューティング ノードの使用率の最大化
description: Azure Batch プール内の各ノードでタスクを並行実行し、使用する計算ノードの数を減らすことで、効率を高めて、コストを削減します。
ms.topic: how-to
ms.date: 10/08/2020
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 8bc9f03f05d52df6e400be5c57033ab2a38fa8eb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92102967"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>タスクの同時実行による Batch コンピューティング ノードの使用率の最大化

各ノードで複数のタスクを同時に実行することで、プール内の計算ノードの数を減らしつつ、リソースの使用率を最大限に高めることができます。

シナリオによっては、1 つのノードのリソースをすべて 1 つのタスクに割り当てた状態で最適に動作する場合がありますが、特定のワークロードでは、複数のタスクがそれらのリソースを共有するとジョブ時間が短縮され、コストが削減されることがあります。

- **データ転送を最小限に抑える** (データを共有できるタスクの場合)。 共有データを少数のノードにコピーし、各ノードでタスクを並列実行することにより、データ転送の費用を大幅に削減できます。 これが特に当てはまるのは、各ノードにコピーするデータを、リージョン間で転送する必要がある場合です。
- **メモリの使用率を最大化する** (大量のメモリが必要だが、短期間のみで、実行中の発生回数が一定しないタスクの場合)。 より多くのメモリを使用する少数の大きなコンピューティング ノードを使用することで、このようなスパイクを効率的に処理できます。 これらのノードには、各ノードで並列で実行されるタスクが割り当てられますが、各タスクは、ノードの豊富なメモリを異なるタイミングで利用できます。
- **ノード数の制限を緩和する** (プール内でノード間の通信が必要な場合)。 現時点では、ノード間通信用に構成されるプールは 50 のコンピューティング ノードに制限されています。 このようなプール内の各ノードがタスクを並列に実行できると、より多くのタスクが同時に実行できるようになります。
- **オンプレミスのコンピューティング クラスターをレプリケートする** (コンピューティング環境を初めて Azure に移行するときなど)。 現在のオンプレミスのソリューションがコンピューティング ノードごとに複数のタスクを実行している場合、ノード タスクの最大数を増やすことで、その構成をより厳密に反映できます。

## <a name="example-scenario"></a>サンプル シナリオ

例として、[Standard\_D1](../cloud-services/cloud-services-sizes-specs.md) ノードで十分な CPU とメモリの要件を持つタスク アプリケーションがあるとします。 ただし、必要な時間内にジョブを終えるには、そのようなノードが 1,000 個必要です。

1 個の CPU コアを持つ Standard\_D1 ノードを使用する代わりに、それぞれが 16 個のコアを持つ [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md) ノードを使用して、並列タスクの実行を有効にすることもできます。 つまり、使用する "*ノードの数は 16 分の 1*" で済むため、必要なのは 1,000 個のノードではなく 63 個のみになります。 大きなアプリケーション ファイルまたは参照データが各ノードに必要である場合、データのコピー先がわずか 63 個のノードで済むため、ジョブの実行時間と効率の面でも改善が期待できます。

## <a name="enable-parallel-task-execution"></a>並列タスク実行を有効にする

並列タスク実行のためのコンピューティング ノードの構成は、プール レベルで行います。 Batch .NET ライブラリを使用する場合は、プールを作成するときに [CloudPool.TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) プロパティを設定します。 Batch REST API を使用する場合は、プール作成時の要求本文に [taskSlotsPerNode](/rest/api/batchservice/pool/add) 要素を設定します。

> [!NOTE]
> `taskSlotsPerNode` 要素および [TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) プロパティは、プール作成時にのみ設定できます。 これらは、プールが既に作成された後で変更することはできません。

Azure Batch では、ノードあたりのタスク スロット数をノード コアの数の最大 4 倍まで設定できます。 たとえば、プールをノード サイズ "Large" (4 コア) で構成した場合、 `taskSlotsPerNode` は 16 に設定できます。 ただし、ノードのコア数には関係なく、ノードあたり 256 タスク スロットを超えることはできません。 各ノード サイズのコア数の詳細については、「 [Cloud Services のサイズ](../cloud-services/cloud-services-sizes-specs.md)」を参照してください。 サービスの制限の詳細については、「 [Azure Batch サービスのクォータと制限](batch-quota-limit.md)」を参照してください。

> [!TIP]
> プールの[自動スケールの数式](/rest/api/batchservice/pool/enableautoscale)を作成するときは、`taskSlotsPerNode` の値を考慮してください。 たとえば、 `$RunningTasks` を評価する式は、ノードあたりのタスク数の増加によって大きな影響を受ける可能性があります。 詳細については、[Azure Batch プール内の計算ノードの自動スケール](batch-automatic-scaling.md)に関する記事を参照してください。

## <a name="specify-task-distribution"></a>タスク分散を指定する

同時実行タスクを有効にする場合、プール内のノード間でタスクをどのように分散させるかを指定することが重要です。

[CloudPool.TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool) プロパティを使用することで、プール内のすべてのノードに対して均等にタスクを割り当てるように指定できます ("分散")。 または、1 つのノードにできる限り多くのタスクを割り当ててから、プール内の別のノードにタスクを割り当てるように指定することもできます ("圧縮")。

例として、前述の [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md) ノードのプールを [CloudPool.TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) 値 16 で構成した場合について考えます。 [ComputeNodeFillType](/dotnet/api/microsoft.azure.batch.common.computenodefilltype) に *Pack* を指定して [CloudPool.TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool) を構成した場合、各ノードの 16 コアすべての使用率が最大になり、[自動スケーリング プール](batch-automatic-scaling.md)が未使用ノード (タスクが割り当てられていないノード) をプールから削除できます。 これによってリソース使用率は最小になり、コストを節約できます。

## <a name="define-variable-slots-per-task"></a>タスクごとの可変スロットを定義する

タスクは、計算ノードでの実行に必要なスロットの数を指定する [CloudTask.RequiredSlots](/dotnet/api/microsoft.azure.batch.cloudtask.requiredslots) プロパティを使用して定義できます。 既定値は 1 です。 計算ノードでのリソースの使用量の比重がタスクごとに異なる場合には、可変タスク スロットを設定できます。 そうすることで、各計算ノードで、CPU やメモリなどのシステム リソースを圧迫しない妥当な数の同時実行タスクが実行されるようにすることができます。

たとえば、プロパティ `taskSlotsPerNode = 8` が指定されたプールの場合、マルチコアを必要とする CPU 負荷の高いタスクを `requiredSlots = 8` として送信できる一方で、その他のタスクは `requiredSlots = 1` に設定することができます。 この混合ワークロードがスケジュールされた場合、CPU 負荷の高いタスクはその計算ノード上で排他的に実行されますが、他のタスクは他のノードで同時に (一度に最大 8 個まで) 実行できます。 これにより、計算ノード間でワークロードのバランスを調整し、リソースの使用効率を向上させることができます。

> [!TIP]
> 可変タスク スロットを使用する場合、多くのスロットを必要とする大きなタスクを一時的にスケジュールできない可能性があります。これは、一部のノードにアイドル スロットが残っていたとしても、計算ノードで利用可能なスロットが不足しているためです。 これらのタスクに対するジョブの優先度を上げることで、ノードで利用可能なスロットを勝ち取る可能性を高めることができます。
>
> タスクの実行のスケジュールに失敗した場合には、Batch サービスによって [TaskScheduleFailEvent](batch-task-schedule-fail-event.md) が出力され、必要なスロットが利用可能になるまでスケジュールの再試行が継続されます。 そのイベントをリッスンして、潜在的なタスクのスケジュールの問題を検出し、それに応じて軽減策を実行できます。

> [!NOTE]
> タスクの `requiredSlots` をプールの `taskSlotsPerNode` よりも大きい値に指定しないでください。 これにより、タスクを実行できなくなります。 Batch サービスは現在、タスクの送信時にこの競合を検証していません。これは、ジョブの送信時にプールがバインドされていない可能性があるか、または無効化および再有効化によって別のプールに変更される可能性があるためです。

## <a name="batch-net-example"></a>Batch .NET の例

次の [Batch .NET](/dotnet/api/microsoft.azure.batch) API コード スニペットでは、ノードごとに複数のタスク スロットを持つプールを作成し、必要なスロットを指定してタスクを送信する方法を示します。

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>ノードごとに複数のタスク スロットを持つプールを作成する

このコード スニペットは、ノードあたり 4 つのタスク スロットが許可されている 4 つのノードを含むプールの作成要求を示しています。 ここでは、1 つのノードがタスクでいっぱいになってからプール内の別のノードにタスクを割り当てるタスク スケジュール ポリシーが指定されています。

Batch .NET API を使用したプールの追加方法の詳細については、「[BatchClient.PoolOperations.CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations)」を参照してください。

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

pool.TaskSlotsPerNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

### <a name="create-a-task-with-required-slots"></a>必要なスロットを指定したタスクを作成する

このコード スニペットでは、既定以外の `requiredSlots` を指定してタスクを作成します。 このタスクは、計算ノード上に利用可能な空きスロットが十分にある場合にのみ実行されます。

```csharp
CloudTask task = new CloudTask(taskId, taskCommandLine)
{
    RequiredSlots = 2
};
```

### <a name="list-compute-nodes-with-counts-for-running-tasks-and-slots"></a>コンピューティング ノードを、実行中のタスクとスロットの数とともに一覧表示する

このコード スニペットは、プール内のすべてのコンピューティング ノードを一覧表示し、実行中のタスクおよびノードあたりのタスク スロットの数を出力します。

```csharp
ODATADetailLevel nodeDetail = new ODATADetailLevel(selectClause: "id,runningTasksCount,runningTaskSlotsCount");
IPagedEnumerable<ComputeNode> nodes = batchClient.PoolOperations.ListComputeNodes(poolId, nodeDetail);

await nodes.ForEachAsync(node =>
{
    Console.WriteLine(node.Id + " :");
    Console.WriteLine($"RunningTasks = {node.RunningTasksCount}, RunningTaskSlots = {node.RunningTaskSlotsCount}");

}).ConfigureAwait(continueOnCapturedContext: false);
```

### <a name="list-task-counts-for-the-job"></a>ジョブのタスク数を一覧表示する

このコード スニペットは、ジョブのタスク数を取得します。その中には、タスク状態ごとのタスクとタスク スロットの両方が含まれます。

```csharp
TaskCountsResult result = await batchClient.JobOperations.GetJobTaskCountsAsync(jobId);

Console.WriteLine("\t\tActive\tRunning\tCompleted");
Console.WriteLine($"TaskCounts:\t{result.TaskCounts.Active}\t{result.TaskCounts.Running}\t{result.TaskCounts.Completed}");
Console.WriteLine($"TaskSlotCounts:\t{result.TaskSlotCounts.Active}\t{result.TaskSlotCounts.Running}\t{result.TaskSlotCounts.Completed}");
```

## <a name="batch-rest-example"></a>Batch REST の例

次の [Batch REST](/rest/api/batchservice/) API コード スニペットは、ノードごとに複数のタスク スロットを持つプールを作成し、必要なスロットを指定してタスクを送信する方法を示します。

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>ノードごとに複数のタスク スロットを持つプールを作成する

このスニペットは、ノードごとの最大タスク数が 4 である 2 個の大きいノードを含むプールの作成要求を示します。

REST API を使用したプールの追加方法の詳細については、「[Add a pool to an account (アカウントにプールを追加する)](/rest/api/batchservice/pool/add)」を参照してください。

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  },
  "targetDedicatedComputeNodes":2,
  "taskSlotsPerNode":4,
  "enableInterNodeCommunication":true,
}
```

### <a name="create-a-task-with-required-slots"></a>必要なスロットを指定したタスクを作成する

このスニペットは、既定以外の `requiredSlots` を指定してタスクを追加する要求を示しています。 このタスクは、計算ノード上に利用可能な空きスロットが十分にある場合にのみ実行されます。

```json
{
  "id": "taskId",
  "commandLine": "bash -c 'echo hello'",
  "userIdentity": {
    "autoUser": {
      "scope": "task",
      "elevationLevel": "nonadmin"
    }
  },
  "requiredSLots": 2
}
```

## <a name="code-sample-on-github"></a>GitHub 上のサンプル コード

[CloudPool.TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) プロパティの使用方法は、GitHub の [ParallelTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) プロジェクトで紹介されています。

この C# コンソール アプリケーションでは、[Batch .NET](/dotnet/api/microsoft.azure.batch) ライブラリを使用して、1 つ以上のコンピューティング ノードを含むプールを作成します。 さらに、これらのノードで構成可能な数のタスクを実行して、可変負荷をシミュレートします。 アプリケーションの出力には、各タスクを実行したノードが示されます。 また、ジョブのパラメーターと実行時間の概要も出力されます。

例として、ParallelTasks サンプル アプリケーションの 2 つの異なる実行からの出力の概要部分を下に示します。 ここに示されているジョブ期間にはプールの作成時間が含まれていません。これは、送信時に "*アイドル*" 状態だった計算ノードを持つ以前に作成されたプールに対して各ジョブが送信されたためです。

サンプル アプリケーションの 1 回目の実行では、プール内の 1 つのノードと、ノードごとに 1 タスクという既定の設定が使用され、ジョブの実行時間は 30 分を超えていることがわかります。

```
Nodes: 1
Node size: large
Task slots per node: 1
Max slots per task: 1
Tasks: 32
Duration: 00:30:01.4638023
```

2 回目の実行では、ジョブの実行時間が大幅に短縮されています。 これは、ノードごとのタスク数を 4 にしてプールが構成されているためであり、並列タスク実行によってほぼ 4 分の 1 の時間でジョブを完了できます。

```
Nodes: 1
Node size: large
Task slots per node: 4
Max slots per task: 1
Tasks: 32
Duration: 00:08:48.2423500
```

## <a name="next-steps"></a>次の手順

- [Batch Explorer](https://azure.github.io/BatchExplorer/) のヒート マップを試してください。 Batch Explorer は、Azure Batch アプリケーションの作成、デバッグ、および監視を支援する、豊富な機能を備えた無料のスタンドアロン クライアント ツールです。 [ParallelTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) サンプル アプリケーションを実行しているときに、Batch Explorer のヒート マップ機能を使用すると、各ノードでの並列タスクの実行を簡単に視覚化できます。
- [GitHub の Azure Batch サンプル](https://github.com/Azure/azure-batch-samples)を探索してください。
- [Batch タスクの依存関係](batch-task-dependencies.md)の詳細を確認してください。
