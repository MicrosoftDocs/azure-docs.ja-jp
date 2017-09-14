---
title: "コスト効率が高く優先順位の低い VM で Azure Batch ワークロードを実行する (プレビュー) | Microsoft Docs"
description: "優先順位の低い VM をプロビジョニングして Azure Batch ワークロードのコストを減らす方法について説明します。"
services: batch
author: mscurrell
manager: timlt
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 07/21/2017
ms.author: markscu
ms.translationtype: HT
ms.sourcegitcommit: 22aa82e5cbce5b00f733f72209318c901079b665
ms.openlocfilehash: 9bf0ac322020d8a8453011c3207c1930175db6d3
ms.contentlocale: ja-jp
ms.lasthandoff: 07/24/2017

---

# <a name="use-low-priority-vms-with-batch-preview"></a>優先順位の低い VM で Batch を使用する (プレビュー)

Azure Batch には Batch ワークロードのコストを減らす優先順位の低い仮想マシン (VM) が用意されています。 優先順位の低い VM は経済的であり、大量の計算能力を提供することで新しい種類の Batch ワークロードを作成できます。

優先順位の低い VM は Azure の余剰容量を活用します。 プールで優先順位の低い VM を指定すると、Azure Batch はこの余剰分を使用できる際には自動的に使用します。

優先順位の低い VM を使用するデメリットは、Azure に余剰容量がないとこれらの VM が別の VM によって割り込まれる可能性がある点です。 このため、優先順位の低い VM は特定の種類のワークロードに最適です。 優先順位の低い VM は、ジョブの完了時間に柔軟性があり、作業が多数の VM に分散されているバッチおよび非同期処理ワークロードに使用します。

優先順位の低い VM は、専用 VM よりも大幅にコストを抑えることができます。 料金について詳しくは、「[Batch の価格](https://azure.microsoft.com/pricing/details/batch/)」をご覧ください。

優先順位の低い VM に関する追加の説明については、ブログ投稿の告知「[Batch computing at a fraction of the price](https://azure.microsoft.com/blog/announcing-public-preview-of-azure-batch-low-priority-vms/)」 (わずかな価格でのバッチ コンピューティング) をご覧ください。

> [!IMPORTANT]
> 優先順位の低い VM は現在プレビューの段階であり、Batch で実行されるワークロードでのみ使用できます。 
>
>

## <a name="use-cases-for-low-priority-vms"></a>優先順位の低い VM の使用事例

優先順位の低い VM のこの特性から、この VM を使用できるワークロードと使用できないワークロードはどれでしょうか。 一般的に、ジョブが多数の並列タスクに分割されていたり、スケールアウトされた多数のジョブが多数の VM に分散されていたりするバッチ処理ワークロードに適しています。

-   Azure では余剰容量の使用を最大化するために、適切なジョブをスケールアウトできます。

-   VM は使用できない場合や割り込まれる場合があり、その結果ジョブに使用できる容量が減り、タスクの中断や再実行につながることがあります。 このため、ジョブの実行にかかる時間には柔軟性が必要です。

-   時間のかかるジョブが中断されると影響がより大きくなります。 長時間実行されるタスクについては、チェックポイント処理を実装して実行時に進行状況を保存すると、中断の影響をかなり抑えることができます。 実行時間の短いタスクは中断の影響がはるかに少ないため、優先順位の低い VM に最適です。

-   複数の VM を使用する実行時間の長い MPI ジョブは、優先順位の低い VM の使用が適していません。1 つの VM が割り込まれるとジョブ全体を再実行する必要があるためです。

優先順位の低い VM の使用が適しているバッチ処理の使用事例は次のとおりです。

-   **開発とテスト**: 特に、大規模なソリューションを開発する場合に大幅な節約を実現できます。 あらゆる種類のテストでメリットがありますが、特に大規模なロード テストや回帰テストに最適です。

-   **オンデマンドでの容量の補足**: 優先順位の低い VM は通常の専用 VM の補足に使用できます。使用できるときは、ジョブをスケールし、より早く完了することでコストを削減できます。使用できないときは、専用 VM のベースラインを使用できます。

-   **柔軟なジョブ実行時間**: ジョブの実行に要する時間に柔軟性があれば、容量の低下に対応できます。ただし、多くの場合、優先順位の低い VM を追加することでジョブの実行は速くなり、コストを削減できます。

Batch プールは、ジョブ実行時間の柔軟性によって、いくつかの方法で優先順位の低い VM を使用するように構成できます。

-   優先順位の低い VM はプールでのみ使用でき、Batch は使用できるときに割り込まれた容量を回復します。 優先順位の低い VM のみが使用されるため、これがジョブを実行する上でコストが最もかからない方法です。

-   優先順位の低い VM は専用 VM の固定ベースラインと組み合わせて使用できます。 専用 VM の数を固定することにより、ジョブの進行を維持するための容量が常に確保されます。

-   専用 VM と優先順位の低い VM の組み合わせは動的に変更できます。このため、使用できるときはコストの低い VM のみを使用します。ただし、ジョブの進行を維持できる最小限の容量を確保するために、必要に応じてフルプライスの専用 VM をスケールアップします。

## <a name="batch-support-for-low-priority-vms"></a>優先順位の低い VM に対する Batch のサポート

Azure Batch には、優先順位の低い VM を使用してメリットを享受するためのいくつかの機能が用意されています。

-   Batch プールには専用 VM と優先順位の低い VM の両方を入れることができます。 各種類の VM の数はプールを作成するときに指定するか、既存のプールで明示的なサイズ変更操作または自動スケールを使用することでいつでも変更できます。 ジョブやタスクの送信は変更しないでそのままにし、プール内の VM の種類に関連付ける必要はありません。 また、コストを可能な限り抑えるためにすべてのジョブを優先順位の低い VM で実行する一方で、ジョブの実行を維持するために、容量が最小しきい値を下回った場合には専用 VM を使用するようにプールを設定できます。

-   Batch プールは優先順位の低い VM のターゲット数を自動的にシークします。 VM が割り込まれた場合、Batch は失われた容量に対して置き換えを試行し、ターゲットに戻します。

-   タスクが中断されたときには、Batch はタスクを検出してもう一度実行するように自動的にキューに入れます。

-   優先順位の低い VM には専用 VM とは異なるコア クォータが存在します。 
    優先順位の低い VM はコストがより低いため、クォータが専用コストよりも高くなります。 詳しくは、「[Batch サービスのクォータと制限](batch-quota-limit.md#resource-quotas)」をご覧ください。    

> [!NOTE]
> 優先順位の低い VM は、プールの割り当てモードが[ユーザー サブスクリプション](batch-account-create-portal.md#user-subscription-mode)になっている Batch アカウントでは、現在サポートされていません。
>
>

## <a name="create-and-update-pools"></a>プールの作成と更新

Batch プールには、専用 VM と優先順位の低い VM の両方を入れることができます (計算ノードとも呼ばれます)。 専用 VM と優先順位の低い VM の両方の計算ノードのターゲット数を設定できます。 ノードのターゲット数はプールに入れておく VM の数を指定します。

たとえば、Azure クラウド サービス VM を使用して専用 VM のターゲット数が 5、優先順位の低い VM のターゲット数が 20 のプールを作成するには、次のようにします。

```csharp
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: "cspool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4") // WS 2012 R2
);
```

Azure Virtual Machines (ここでは Linux VM) を使用して専用 VM のターゲット数が 5、優先順位の低い VM のターゲット数が 20 のプールを作成するには、次のようにします。

```csharp
ImageReference imageRef = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "16.04.0-LTS",
    version: "latest");

// Create the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration("batch.node.ubuntu 16.04", imageRef);

pool = batchClient.PoolOperations.CreatePool(
    poolId: "vmpool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard\_D2\_v2",
    virtualMachineConfiguration: virtualMachineConfiguration);
```

専用 VM と優先順位の低い VM の両方の現在のノード数を取得するには、次のようにします。

```csharp
int? numDedicated = pool1.CurrentDedicatedComputeNodes;
int? numLowPri = pool1.CurrentLowPriorityComputeNodes;
```

プールのノードには、そのノードが専用 VM であるか優先順位の低い VM であるかを示すプロパティが用意されています。

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

プール内の 1 つ以上のノードが割り込まれても、プール上のノードの一覧表示操作は引き続きこれらのノードを返し、優先順位の低いノードの現在の数は変わりませんが、これらのノードの状態は**割り込み**に設定されます。 バッチは代替 VM の検索を試行し、成功するとノードの状態が**作成中**から**開始**に移行します。その後新しいノードと同じようにタスクを実行できるようになります。

## <a name="scale-a-pool-containing-low-priority-vms"></a>優先順位の低い VM が入ったプールのスケール

専用 VM のみで構成されるプールと同じように、サイズ変更メソッドを呼び出すか自動スケールを使用して、優先順位の低い VM が入ったプールをスケールできます。

プールのサイズ変更操作は 2 番目のオプション パラメーターを取り、**targetLowPriorityNodes** の値を更新します。

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

プールの自動スケール式は、優先順位の低い VM を次のようにサポートします。

-   サービス定義変数 **$TargetLowPriorityNodes** の値を取得または設定できます。

-   サービス定義変数 **$CurrentLowPriorityNodes** の値を取得できます。

-   サービス定義変数 **$PreemptedNodeCount** の値を取得できます。 
    この変数は割り込み状態にあるノードの数を返し、使用できない割り込みノードの数に応じて、専用ノードの数をスケールアップまたはスケールダウンできます。

## <a name="jobs-and-tasks"></a>ジョブとタスク

優先順位の低いノードに対して、ジョブやタスクに必要なサポートはごくわずかです。必要なサポートは次のとおりです。

-   ジョブの JobManagerTask プロパティに新しいプロパティ **AllowLowPriorityNode** があります。 
    このプロパティが true の場合、専用ノードまたは優先順位の低いノードのいずれかでジョブ マネージャー タスクをスケジュールできます。 このプロパティが false の場合、専用のノードに対してのみジョブ マネージャー タスクをスケジュールできます。

-   タスク アプリケーションに対して[環境変数](batch-compute-node-environment-variables.md)が有効になっているため、優先順位の低いノードと専用ノードのどちらで実行されているかを判別できます。 環境変数は、AZ_BATCH_NODE_IS_DEDICATED です。

## <a name="handling-preemption"></a>割り込みの処理

VM では割り込みが発生することがあり、これが発生すると、Batch は次のように処理します。

-   割り込まれた VM の状態が**割り込み**に更新されます。
-   タスクが割り込まれたノードの VM で実行されていた場合、それらのタスクは再度キューに入れられもう一度実行されます。
-   その VM は事実上削除され、VM のローカルに保存されているすべてのデータが削除されます。
-   プールは引き続き使用可能な優先順位の低いノードのターゲット数に達するよう試行します。 代替の容量が見つかると、ノードは ID を保持しつつ再初期化され、**作成中**状態、**開始**状態を経て、その後タスクをスケジュールできるようになります。
-   割り込みの数は Azure Portal のメトリックとして使用できます。

## <a name="metrics"></a>メトリック

新しいメトリックは、優先順位の低いノードについて [Azure Portal](https://portal.azure.com) で使用可能です。 これらのメトリックは次のとおりです。

- Low-Priority Node Count (優先順位の低いノードの数)
- Low-Priority Core Count (優先順位の低いコアの数) 
- Preempted Node Count (割り込まれたノードの数)

Azure Portal でメトリックを表示するには、次の手順を実行します。

1. ポータル内の Batch アカウントに移動し、Batch アカウントの設定を表示します。
2. **[監視]** セクションから **[メトリック]** を選択します。
3. **[利用可能なメトリック]** 一覧から目的のメトリックを選択します。

![優先順位の低いノードのメトリック](media/batch-low-pri-vms/low-pri-metrics.png)

## <a name="next-steps"></a>次のステップ

* Batch を使用するための準備を担当する方は、「 [開発者向け Batch 機能の概要](batch-api-basics.md)」で重要な情報をご確認ください。 この記事には、Batch アプリケーションを構築するときに使用できる多数の API 機能、プール、ノード、ジョブ、タスクなど、Batch サービスのリソースに関する詳しい情報が記載されています。
* Batch ソリューションの構築に使用できる [Batch API とツール](batch-apis-tools.md)について学習します。

