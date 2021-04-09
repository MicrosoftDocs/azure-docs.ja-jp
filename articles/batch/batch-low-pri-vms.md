---
title: コスト効率が高く優先順位の低い VM でワークロードを実行する
description: 優先順位の低い VM をプロビジョニングして Azure Batch ワークロードのコストを減らす方法について説明します。
author: mscurrell
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: seodec18
ms.openlocfilehash: cafc7216e8112640f823ecee1aea055ab78b3fc6
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098470"
---
# <a name="use-low-priority-vms-with-batch"></a>Batch で優先順位の低い VM を使用する

Azure Batch には Batch ワークロードのコストを減らす優先順位の低い仮想マシン (VM) が用意されています。 優先順位の低い VM は、非常に低いコストで大量のコンピューティング能力を使用できるようにすることで、新しい種類の Batch ワークロードを可能にします。

優先順位の低い VM は Azure の余剰容量を活用します。 プールで優先順位の低い VM を指定すると、Azure Batch は使用できる場合にこの余剰分を使用します。

優先順位の低い VM を使用するデメリットは、利用可能な容量によっては、これらの VM が常に割り当てられるわけではないという点、またはいつでも割り込まれる可能性があるという点です。 このため、優先順位の低い VM は、ジョブの完了時間に柔軟性があり、作業が多数の VM に分散されているバッチおよび非同期処理ワークロードに最も適しています。

優先度の低い VM は、専用の VM と比較して大幅な割引価格で提供されます。 料金について詳しくは、「[Batch の価格](https://azure.microsoft.com/pricing/details/batch/)」をご覧ください。

> [!NOTE]
> [単一インスタンス VM](../virtual-machines/spot-vms.md) と [VM スケールセット](../virtual-machine-scale-sets/use-spot.md)に[スポット VM](https://azure.microsoft.com/pricing/spot/) を使用できるようになりました。 スポット VM は優先順位の低い VM の進化版ですが、その価格の差はさまざまで、オプションの最大価格をスポット VM の割り当て時に設定することもできます。
>
>Azure Batch プールでは、将来、新しいバージョンの [Batch API とツール](./batch-apis-tools.md)とともに、スポット VM のサポートを開始する予定です。 スポット VM のサポートが開始されると、優先順位の低い VM は非推奨になりますが、スポット VM への移行に十分な時間を確保するため、API とツールの現在のバージョンを使用して、少なくとも 12 か月間は引き続きサポートされます。
>
> スポット VM は、仮想マシン構成プールでのみサポートされます。 スポット VM を使用するには、すべてのクラウド サービス構成プールを[仮想マシン構成プールに移行する](batch-pool-cloud-service-to-virtual-machine-configuration.md)必要があります。

## <a name="batch-support-for-low-priority-vms"></a>優先順位の低い VM に対する Batch のサポート

Azure Batch には、優先順位の低い VM を使用してメリットを享受するためのいくつかの機能が用意されています。

- Batch プールには専用 VM と優先順位の低い VM の両方を入れることができます。 各種類の VM の数はプールを作成するときに指定するか、既存のプールで明示的なサイズ変更操作または自動スケールを使用することでいつでも変更できます。 ジョブやタスクの送信は、プール内の VM の種類に関係なく、変更しないままにできます。 また、コストを可能な限り抑えるためにすべてのジョブを優先順位の低い VM で実行する一方で、ジョブの実行を維持するために、容量が最小しきい値を下回った場合には専用 VM を使用するようにプールを構成できます。
- Batch プールは優先順位の低い VM のターゲット数を自動的にシークします。 VM が割り込まれたり、利用できなくなったりした場合、Batch は失われた容量に対して置き換えを試行し、ターゲットに戻します。
- タスクが中断された場合、Batch は再度実行するタスクを検出して自動的にキューに再登録します。
- 優先順位の低い VM には、専用 VM とは異なる別個の vCPU クォータがあります。 優先順位の低い VM はコストがより低いため、クォータは専用 VM のクォータより高くなります。 詳しくは、「[Batch サービスのクォータと制限](batch-quota-limit.md#resource-quotas)」をご覧ください。

> [!NOTE]
> 優先順位の低い VM は、[ユーザー サブスクリプション モード](accounts.md)で作成された Batch アカウントでは、現在サポートされていません。

## <a name="considerations-and-use-cases"></a>考慮事項とユース ケース

多くの Batch ワークロードは、優先順位の低い VM に適しています。 ジョブが多数の並列タスクに分割されている場合や、スケールアウトされた多数のジョブが多数の VM に分散されている場合にそれらを使用することを検討してください。

優先順位の低い VM の使用が適しているバッチ処理の使用事例は次のとおりです。

- **開発とテスト**:特に、大規模なソリューションを開発する場合に大幅な節約を実現できます。 あらゆる種類のテストでメリットがありますが、特に大規模なロード テストや回帰テストに最適です。
- **オンデマンドでの容量の補足**:優先順位の低い VM を使用して、通常の専用 VM を補うことができます。 利用可能な場合は、ジョブをスケーリングして短時間で完了することで、コストを削減できます。利用できない場合は、専用 VM のベースラインを引き続き利用できます。
- **柔軟なジョブ実行時間**:ジョブの実行に要する時間に柔軟性があれば、容量の低下に対応できます。ただし、多くの場合、優先順位の低い VM を追加することでジョブの実行は速くなり、コストを削減できます。

Batch プールは、いくつかの方法で優先順位の低い VM を使用するように構成できます。

- プールは、優先順位の低い VM のみを使用できます。 この場合、Batch は、使用可能な場合は割り込まれた容量を回復します。 この構成は、ジョブを実行する上でコストが最もかからない方法です。
- 優先順位の低い VM は専用 VM の固定ベースラインと組み合わせて使用できます。 専用 VM の数を固定することにより、ジョブの進行を維持するための容量が常に確保されます。
- プールは、専用 VM と優先順位の低い VM の動的な組み合わせを使用できます。このため、使用できるときは低コストの優先順位の低い VM のみを使用します。ただし、必要に応じてフルプライスの専用 VM をスケールアップします。 この構成は、ジョブの進行を維持できる最小限の容量を確保します。

優先順位の低い VM の使用を計画するときは、次の点に注意してください。

- Azure では余剰容量の使用を最大化するために、適切なジョブをスケールアウトできます。
- VM は使用できない場合や割り込まれる場合があり、その結果ジョブに使用できる容量が減り、タスクの中断や再実行につながることがあります。
- 実行時間の短いタスクは、優先順位の低い VM で最適に動作する傾向があります。 時間のかかるジョブが中断されると影響がより大きくなります。 長時間実行されるタスクについては、チェックポイント処理を実装して実行時に進行状況を保存すると、この影響が軽減される可能性があります。 
- 1 つの VM が割り込まれるとジョブ全体を再実行する可能性があるため、複数の VM を使用する実行時間の長い MPI ジョブは、優先順位の低い VM の使用は適していません。
- 優先順位の低いノードは、[ネットワーク セキュリティ グループ （NSG） 規則](batch-virtual-network.md#network-security-groups-specifying-subnet-level-rules)が正しく構成されていない場合、使用不可とマークされることがあります。

## <a name="create-and-manage-pools-with-low-priority-vms"></a>優先順位の低い VM でプールを作成して管理する

Batch プールには、専用 VM と優先順位の低い VM の両方を入れることができます (計算ノードとも呼ばれます)。 専用 VM と優先順位の低い VM の両方の計算ノードのターゲット数を設定できます。 ノードのターゲット数はプールに入れておく VM の数を指定します。

たとえば、Azure Virtual Machines (ここでは Linux VM) を使用して専用 VM のターゲット数が 5、優先順位の低い VM のターゲット数が 20 のプールを作成するには、次のようにします。

```csharp
ImageReference imageRef = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "16.04-LTS",
    version: "latest");

// Create the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration("batch.node.ubuntu 16.04", imageRef);

pool = batchClient.PoolOperations.CreatePool(
    poolId: "vmpool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
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

VM は、割り込まれる場合があります。 このようなことが起こった場合は、割り込まれたノードの VM で実行されていたタスクが再度キューに入れられて、もう一度実行されます。

仮想マシン構成プールの場合、Batch によって次の処理も行われます。

- 割り込まれた VM の状態が **割り込み** に更新されます。 
- その VM は事実上削除され、VM のローカルに保存されているすべてのデータが削除されます。
- プールのノード一覧表示操作でも、割り込まれたノードが返されます。
- プールは引き続き使用可能な優先順位の低いノードのターゲット数に達するよう試行します。 代替の容量が見つかると、ノードは ID を保持しつつ再初期化され、**作成中** 状態、**開始** 状態を経て、その後タスクをスケジュールできるようになります。
- 割り込みの数は Azure Portal のメトリックとして使用できます。

## <a name="scale-pools-containing-low-priority-vms"></a>優先順位の低い VM が入ったプールのスケール

専用 VM のみで構成されるプールと同じように、サイズ変更メソッドを呼び出すか自動スケールを使用して、優先順位の低い VM が入ったプールをスケールできます。

プールのサイズ変更操作は 2 番目のオプション パラメーターを取り、**targetLowPriorityNodes** の値を更新します。

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

プールの自動スケール式は、優先順位の低い VM を次のようにサポートします。

- サービス定義変数 **$TargetLowPriorityNodes** の値を取得または設定できます。
- サービス定義変数 **$CurrentLowPriorityNodes** の値を取得できます。
- サービス定義変数 **$PreemptedNodeCount** の値を取得できます。 この変数は割り込み状態にあるノードの数を返し、使用できない割り込みノードの数に応じて、専用ノードの数をスケールアップまたはスケールダウンできます。

## <a name="configure-jobs-and-tasks"></a>ジョブとタスクを構成する

優先順位の低いノードに対して、ジョブとタスクに必要な追加構成はわずかです。 以下の点にご注意ください。

- ジョブの JobManagerTask プロパティには **AllowLowPriorityNode** プロパティがあります。 このプロパティが true の場合、専用ノードまたは優先順位の低いノードのいずれかでジョブ マネージャー タスクをスケジュールできます。 これが false の場合、ジョブ マネージャー タスクは専用のノードに対してのみスケジュールされます。
- タスク アプリケーションに対して AZ_BATCH_NODE_IS_DEDICATED [環境変数](batch-compute-node-environment-variables.md)が有効になっているため、優先順位の低いノードと専用ノードのどちらで実行されているかを判別できます。

## <a name="view-metrics-for-low-priority-vms"></a>優先順位の低い VM のメトリックを表示する

新しいメトリックは、優先順位の低いノードについて [Azure Portal](https://portal.azure.com) で使用可能です。 これらのメトリックは次のとおりです。

- Low-Priority Node Count (優先順位の低いノードの数)
- Low-Priority Core Count (優先順位の低いコアの数)
- Preempted Node Count (割り込まれたノードの数)

Azure portal でこれらのメトリックを表示するには

1. Azure Portal の Batch アカウントに移動します。
2. **[監視]** セクションから **[メトリック]** を選択します。
3. **[メトリック]** 一覧から目的のメトリックを選択します。

## <a name="next-steps"></a>次のステップ

- [Batch サービスのワークフローと主要なリソース](batch-service-workflow-features.md) (プール、ノード、ジョブ、タスクなど) について学習します。
- Batch ソリューションの構築に使用できる [Batch API とツール](batch-apis-tools.md)について学習します。
- 優先順位の低い VM からスポット VM への移行の計画を開始しましょう。 優先順位の低い VM を **クラウド サービス構成** プールと共に使用する場合は、代わりに [**仮想マシンの構成** プール](nodes-and-pools.md#configurations)に移行することを計画してください。
