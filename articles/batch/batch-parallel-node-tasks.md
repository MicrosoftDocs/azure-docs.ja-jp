---
title: タスクの並列実行によるコンピューティング リソースの効率的な使用 - Azure Batch | Microsoft Docs
description: Azure Batch プール内の各ノードで同時実行タスクを実行し、使用するコンピューティング ノードの数を減らすことで、効率を高めて、コストを削減します。
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 538a067c-1f6e-44eb-a92b-8d51c33d3e1a
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/17/2019
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f45c35e6d9fb611ebf73c4eab8b517d8575b8e82
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094939"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>タスクの同時実行による Batch コンピューティング ノードの使用率の最大化 

Azure Batch プールでは、各コンピューティング ノードで複数のタスクを同時に実行することによって、リソースの使用率を最大限に高めつつ、プールに必要なノード数を減らすことができます。 ワークロードによっては、ジョブの時間が短縮され、コストを削減できます。

単一のタスクにすべてのノードのリソースを割り当てることでメリットがあるシナリオもありますが、複数のタスクでリソースを共有することにメリットがある状況もあります。

* **データ転送を最小限に抑える** 。 このシナリオでは、共有データを少数のノードにコピーし、各ノードでタスクを並列実行することにより、データ転送の費用を大幅に削減できます。 これが特に当てはまるのは、各ノードにコピーするデータを、リージョン間で転送する必要がある場合です。
* **メモリの使用率を最大化する** 。 より多くのメモリを使用する少数の大きなコンピューティング ノードを使用することで、このようなスパイクを効率的に処理できます。 これらのノードには、各ノードで並列で実行されるタスクが割り当てられますが、各タスクは、ノードの豊富なメモリを異なるタイミングで利用できます。
* **ノード数の制限を緩和する** 。 現時点では、ノード間通信用に構成されるプールは 50 のコンピューティング ノードに制限されています。 このようなプール内の各ノードがタスクを並列に実行できると、より多くのタスクが同時に実行できるようになります。
* 初めてコンピューティング環境を Azure に移行するときなどに、**オンプレミスのコンピューティング クラスターをレプリケートする**。 現在のオンプレミスのソリューションがコンピューティング ノードごとに複数のタスクを実行している場合、ノード タスクの最大数を増やすことで、その構成をより厳密に反映できます。

## <a name="example-scenario"></a>サンプル シナリオ
並列タスク実行の利点を示す例として、タスク アプリケーションの CPU とメモリの要件を満たすノードのサイズが [Standard\_D1](../cloud-services/cloud-services-sizes-specs.md) である場合について考えてみます。 ただし、必要な時間内にジョブを終えるには、そのようなサイズのノードが 1,000 個必要です。

1 個の CPU コアを持つ Standard\_D1 ノードを使用する代わりに、それぞれが 16 個のコアを持つ [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md) ノードを使用して、並列タスクの実行を有効にすることもできます。 この場合、" *ノードの数は 16 分の 1* " で済むため、必要なのは 1,000 ノードではなく 63 ノードのみになります。 加えて、大きなアプリケーション ファイルまたは参照データが各ノードに必要である場合、データのコピー先がわずか 63 ノードで済むため、ジョブの実行時間と効率の面でも改善が期待できます。

## <a name="enable-parallel-task-execution"></a>並列タスク実行を有効にする
並列タスク実行のためのコンピューティング ノードの構成は、プール レベルで行います。 Batch .NET ライブラリを使用する場合は、プールを作成するときに [CloudPool.MaxTasksPerComputeNode][maxtasks_net] プロパティを設定します。 Batch REST API を使用する場合は、プール作成時の要求本文に [maxTasksPerNode][rest_addpool] 要素を設定します。

Azure Batch では、ノードあたりのタスク数をコア ノードの数の最大 4 倍まで設定できます。 たとえば、プールをノード サイズ "Large" (4 コア) で構成した場合、 `maxTasksPerNode` は 16 に設定できます。 ただし、ノードのコア数には関係なく、ノードあたり 256 タスクを超えることはできません。 各ノード サイズのコア数の詳細については、「 [Cloud Services のサイズ](../cloud-services/cloud-services-sizes-specs.md)」を参照してください。 サービスの制限の詳細については、「 [Azure Batch サービスのクォータと制限](batch-quota-limit.md)」を参照してください。

> [!TIP]
> プールの[自動スケールの数式][enable_autoscaling]を作成するときは、`maxTasksPerNode` の値を考慮してください。 たとえば、 `$RunningTasks` を評価する式は、ノードあたりのタスク数の増加によって大きな影響を受ける可能性があります。 詳細については、「 [Azure Batch プール内のコンピューティング ノードの自動スケール](batch-automatic-scaling.md) 」を参照してください。
>
>

## <a name="distribution-of-tasks"></a>タスクの分散
プール内のコンピューティング ノードが複数のタスクを同時に実行できる場合、プール内の各ノードへのタスクの分散方法を指定することが重要です。

[CloudPool.TaskSchedulingPolicy][task_schedule] プロパティを使用することで、プール内のすべてのノードに対して均等にタスクを割り当てるように指定できます ("分散")。 または、1 つのノードにできる限り多くのタスクを割り当ててから、プール内の別のノードにタスクを割り当てるように指定することもできます ("圧縮")。

この機能の有効性を示す例として、前述の [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md) ノードのプールを [CloudPool.MaxTasksPerComputeNode][maxtasks_net] 値 16 で構成した場合について考えます。 [ComputeNodeFillType][fill_type] に *Pack* を指定して [CloudPool.TaskSchedulingPolicy][task_schedule] を構成した場合、各ノードの 16 コアすべての使用率が最大になり、[自動スケーリング プール](batch-automatic-scaling.md)が未使用ノード (タスクが割り当てられていないノード) をプールから削除できます。 これによってリソース使用率は最小になり、コストを節約できます。

## <a name="batch-net-example"></a>Batch .NET の例
次の [Batch .NET][api_net] API コード スニペットは、ノードごとの最大タスク数が 4 である 4 個のノードを含むプールの作成要求を示しています。 ここでは、1 つのノードがタスクでいっぱいになってからプール内の別のノードにタスクを割り当てるタスク スケジュール ポリシーが指定されています。 Batch .NET API を使用したプールの追加方法の詳細については、[BatchClient.PoolOperations.CreatePool][poolcreate_net] に関するページを参照してください。

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

pool.MaxTasksPerComputeNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

## <a name="batch-rest-example"></a>Batch REST の例
次の [Batch REST][api_rest] API スニペットには、ノードごとの最大タスク数が 4 である 2 個の大きいノードを含むプールの作成要求を示します。 REST API を使用したプールの追加方法の詳細については、[アカウントへのプールの追加][rest_addpool]に関するページを参照してください。

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  }
  "targetDedicatedComputeNodes":2,
  "maxTasksPerNode":4,
  "enableInterNodeCommunication":true,
}
```

> [!NOTE]
> `maxTasksPerNode` 要素および [MaxTasksPerComputeNode][maxtasks_net] プロパティは、プール作成時にのみ設定できます。 これらは、プール作成後に変更することはできません。
>
>

## <a name="code-sample"></a>サンプル コード
[CloudPool.MaxTasksPerComputeNode][maxtasks_net] プロパティの使用方法は、GitHub の [ParallelNodeTasks][parallel_tasks_sample] プロジェクトで紹介されています。

この C# コンソール アプリケーションでは、[Batch .NET][api_net] ライブラリを使用して、1 つ以上のコンピューティング ノードを含むプールを作成します。 さらに、これらのノードで構成可能な数のタスクを実行して、可変負荷をシミュレートします。 アプリケーションの出力には、各タスクを実行したノードが示されます。 また、ジョブのパラメーターと実行時間の概要も出力されます。 サンプル アプリケーションの 2 つの異なる実行からの出力の概要部分を以下に示します。

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

サンプル アプリケーションの 1 回目の実行では、プール内の 1 つのノードと、ノードごとに 1 タスクという既定の設定が使用され、ジョブの実行時間は 30 分を超えていることがわかります。

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

2 回目の実行では、ジョブの実行時間が大幅に短縮されています。 これは、ノードごとのタスク数を 4 にしてプールが構成されているためであり、並列タスク実行によってほぼ 4 分の 1 の時間でジョブを完了できます。

> [!NOTE]
> 上記のジョブ期間には、プールの作成時間は含まれません。 上記の各ジョブは事前に作成されたプールに送信されており、そのプールのコンピューティング ノードは送信の時点で *アイドル* 状態でした。
>
>

## <a name="next-steps"></a>次の手順
### <a name="batch-explorer-heat-map"></a>Batch Explorer のヒート マップ
[Batch Explorer][batch_labs] は、Azure Batch アプリケーションの作成、デバッグ、および監視を支援する、豊富な機能を備えた無料のスタンドアロン クライアント ツールです。 Batch Explorer には、タスクの実行を視覚化する "*ヒート マップ*" 機能が含まれます。 [ParallelTasks][parallel_tasks_sample] サンプル アプリケーションを実行すると、ヒート マップ機能を使用し、各ノードでの並列タスクの実行を簡単に視覚化できます。


[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_labs]: https://azure.github.io/BatchExplorer/
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[rest_addpool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

