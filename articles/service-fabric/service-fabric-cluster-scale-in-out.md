---
title: Service Fabric クラスターをスケールインまたはスケールアウトする
description: ノードの種類/仮想マシン スケール セットごとに自動スケール ルールを設定することにより、需要に合わせて Service Fabric クラスターをスケールインまたはスケールアウトします。 Service Fabric クラスターのノードの追加または削除
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 6ee04c73b75d6b335e450ff816c51f0a3089b918
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94409962"
---
# <a name="scale-a-cluster-in-or-out"></a>クラスターをスケールインまたはスケールアウトする

> [!WARNING]
> スケーリングを行う前にこのセクションをお読みください

アプリケーションのワークロードのソースとなるコンピューティング リソースのスケーリングには、意図的な計画が必要になります。スケーリングには、運用環境においてほとんどの場合 1 時間以上の時間がかかり、ワークロードやビジネス コンテキストを理解する必要があります。実際、このアクティビティを実行した経験がない場合は、このドキュメントの残りの部分に進む前に、「[Service Fabric クラスターの容量計画に関する考慮事項](service-fabric-cluster-capacity.md)」を読んで理解することをお勧めします。 これは、意図しない LiveSite の問題を回避するための推奨事項です。また、実行することを決定した操作を非運用環境に対して適切にテストすることもお勧めします。 いつでも、[運用上の問題を報告したり、Azure の有料サポートを要求したりする](service-fabric-support.md#report-production-issues-or-request-paid-support-for-azure)ことができます。 この記事は、コンテキストが該当するこれらの操作の実行を担当するエンジニア向けに、スケーリング操作を説明します。ただし、どのリソース (CPU、ストレージ、メモリ) をスケーリングするか、どの方向にスケーリングするか (垂直、水平)、どの方法で操作するか (リソース テンプレート デプロイ、ポータル、PowerShell/CLI) など、どのような操作が実際のユース ケースに適しているかを自分で判断して理解する必要があります。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>自動スケール ルールを使用した、または手動による Service Fabric クラスターのスケールインとスケールアウト
仮想マシン スケール セットは、セットとして仮想マシンのコレクションをデプロイおよび管理するために使用できる Azure コンピューティング リソースです。 Service Fabric クラスターで定義されているすべてのノード タイプは、個別の仮想マシン スケール セットとしてセットアップされます。 各ノードの種類は、個別にスケールインまたはスケールアウトでき、さまざまなセットのポートを開き、異なる容量のメトリックスを持つことができます。 詳細については、[Service Fabric のノードの種類](service-fabric-cluster-nodetypes.md)に関するドキュメントを参照してください。 クラスター内の Service Fabric のノードの種類はバックエンドの仮想マシン スケール セットで構成されるため、ノードの種類/仮想マシン スケール セットごとに自動スケール ルールを設定する必要があります。

> [!NOTE]
> サブスクリプションに、このクラスターを構成する新しい VM を追加できるだけのコア数が割り当てられている必要があります。 現時点ではモデルの検証はないため、いずれかのクォータの制限に達した場合、デプロイ時のエラーが表示されます。 また、単一ノード タイプの場合、単純に VMSS あたり 100 ノードを超えることはできません。 場合によっては、目標のスケールを達成するために VMSS を追加する必要があります。また、自動スケーリングで VMSS を自動的に追加することはできません。 稼働中のクラスターに VMSS を追加することは困難な作業です。一般的に、ユーザーは作成時に適切なノード タイプをプロビジョニングして新しいクラスターをプロビジョニングする結果になります。そこで、[クラスター容量を計画](./service-fabric-cluster-capacity.md)します。 
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>スケールするノードの種類/仮想マシン スケール セットの選択
現在、ポータルを使用して仮想マシン スケール セットの自動スケール ルールを指定して Service Fabric クラスターを作成することはできないため、Azure PowerShell (1.0 以降) を使用して、ノードの種類を一覧表示し、それらに自動スケール ルールを追加します。

クラスターを構成する仮想マシン スケール セットの一覧を取得するには、次のコマンドレットを実行します。

```powershell
Get-AzResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzVmss -ResourceGroupName <RGname> -VMScaleSetName <virtual machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>ノードの種類/仮想マシン スケール セットの自動スケール ルールの設定
クラスターに複数のノードの種類がある場合は、スケールインまたはスケールアウトするノードの種類/仮想マシン スケール セットごとにこれを繰り返します。 自動スケールを設定する前に、必要なノードの数を考慮します。 ノードの種類がプライマリである場合に必要なノードの最小数は、選択した信頼性のレベルによって決まります。 信頼性のレベルの詳細については、 [こちら](service-fabric-cluster-capacity.md)を参照してください。

> [!NOTE]
> ノードの種類がプライマリである場合に前述の最小数未満にスケールインすると、クラスターが不安定になるか、停止することもあります。 これにより、アプリケーションおよびシステム サービスのデータが失われる可能性があります。
> 
> 

現在、自動スケール機能は、アプリケーションによって Service Fabric に報告されている場合がある負荷では駆動されません。 したがって現時点では、取得する自動スケールは、各仮想マシン スケール セット インスタンスによって出力されるパフォーマンス カウンターのみで駆動されます。  

仮想マシン スケール セットごとに自動スケールを設定するには、[この手順](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md)に従います。

> [!NOTE]
> スケールイン シナリオでは、ノードの種類の[耐久性レベル][durability]が Gold または Silver でない限り、適切なノードの名前を指定して、[Remove-ServiceFabricNodeState コマンドレット](/powershell/module/servicefabric/remove-servicefabricnodestate)を呼び出す必要があります。 Bronze の耐久性の場合、一度に 1 つを超えるノードのスケールインは推奨されません。
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>ノードの種類/仮想マシン スケール セットへの VM の手動の追加

スケールアウトする場合には、スケール セットに仮想マシン インスタンスを追加します。 これらのインスタンスは、Service Fabric で使用されるノードになります。 Service Fabric は、(スケールアウトによって) スケール セットにインスタンスがいつ追加されるかを認識し、自動的に反応します。 

> [!NOTE]
> VM の追加には時間がかかるため、すぐに追加されることを想定しないでください。 レプリカ/サービス インスタンスを配置するための VM 容量が使用可能になるまでの時間として 10 分以上を考慮した上で、容量の追加は十分な時間的余裕をもって計画してください。
> 

### <a name="add-vms-using-a-template"></a>テンプレートを使用した VM の追加
「[クイック スタート: テンプレート ギャラリー](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing)」のサンプル/手順に従って、各ノードの種類の VM 数を変更します。 

### <a name="add-vms-using-powershell-or-cli-commands"></a>PowerShell または CLI のコマンドを使用した VM の追加
次のコードでは、スケール セットを名前で取得し、スケール セットの **容量** を 1 ずつ増やします。

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzVmss -ResourceGroupName $scaleset.ResourceGroupName -VMScaleSetName $scaleset.Name -VirtualMachineScaleSet $scaleset
```

このコードでは容量を 6 に設定します。

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="manually-remove-vms-from-a-node-typevirtual-machine-scale-set"></a>ノードの種類/仮想マシン スケール セットからの VM の手動の削除
ノードの種類をスケールインするときは、スケール セットから VM インスタンスを削除します。 ノードの種類が Bronze の耐久性レベルの場合、Service Fabric は何が起こったかを認識せず、ノードが見つからないと報告します。 そのため、Service Fabric はクラスターに対して異常な状態を報告します。 そのような状態を避けるためには、クラスターからノードを明示的に削除し、ノード状態を削除する必要があります。

Service Fabric システム サービスは、クラスター内のプライマリ ノードの種類で実行されます。 プライマリのノードの種類をスケールインする際は、[信頼性レベル](service-fabric-cluster-capacity.md)の保証を下回るまでインスタンス数をスケールインしないでください。 
 
ステートフル サービスについては、可用性を維持し、サービスの状態を保持するために、所定の数のノードが常に稼働している必要があります。 少なくとも、パーティション/サービスのターゲット レプリカ セットと同じ数のノードが必要です。

### <a name="remove-the-service-fabric-node"></a>Service Fabric ノードの削除

ノード状態を手動で削除するための手順は、*Bronze* の耐久性のノードの種類にのみ適用されます。  持続性レベルが *Silver* および *Gold* の場合、これらの手順はプラットフォームによって自動的に実行されます。 耐久性の詳細については、[Service Fabric クラスターの容量計画][durability]に関する記事をご覧ください。

>[!NOTE]
> 持続性レベルが Gold または Silver である任意の仮想マシン スケール セットのノードを最小数である 5 つ保持します。 このしきい値を下回ってスケールインした場合は、クラスターがエラー状態になり、削除されたノードを手動でクリーンアップすることが必要になります。

クラスターのノードをアップグレード ドメインと障害ドメインに均等に分散させ続け、それによって均等な使用を実現するためには、最も最近作成されたノードが最初に削除されるようにする必要があります。 言い換えれば、ノードは作成とは逆の順序で削除される必要があります。 最も最近作成されたノードは、`virtual machine scale set InstanceId` プロパティの値が最大のノードです。 下のコード例では、最も最近作成されたノードが返されます。

```powershell
Get-ServiceFabricNode | Sort-Object NodeInstanceId -Descending | Select-Object -First 1
```

```shell
sfctl node list --query "sort_by(items[*], &name)[-1]"
```

Service Fabric クラスターは、このノードが削除されることを認識する必要があります。 以下の 3 つの手順を実行する必要があります。

1. ノードを無効にして、ノードがデータのレプリケートとならないようにします。  
PowerShell: `Disable-ServiceFabricNode`  
sfctl: `sfctl node disable`

2. ノードを停止して、Service Fabric ランタイムが完全にシャット ダウンされ、アプリが中断要求を取得できるようにします。  
PowerShell: `Start-ServiceFabricNodeTransition -Stop`  
sfctl: `sfctl node transition --node-transition-type Stop`

2. クラスターからノードを削除します。  
PowerShell: `Remove-ServiceFabricNodeState`  
sfctl: `sfctl node remove-state`

これら 3 つの手順がノードに適用されたら、ノードをスケール セットから削除できます。 [Bronze][durability] 以外の耐久性レベルを利用している場合には、スケール セット インスタンスが削除されるときに、これらの手順が自動的に行われます。

次のコード ブロックは、最後に作成されたノードを取得して、そのノードを無効化して停止し、クラスターから削除します。

```powershell
#### After you've connected.....
# Get the node that was created last
$node = Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1

# Node details for the disable/stop process
$nodename = $node.NodeName
$nodeid = $node.NodeInstanceId

$loopTimeout = 10

# Run disable logic
Disable-ServiceFabricNode -NodeName $nodename -Intent RemoveNode -TimeoutSec 300 -Force

$state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus

while (($state -ne [System.Fabric.Query.NodeStatus]::Disabled) -and ($loopTimeout -ne 0))
{
    Start-Sleep 5
    $loopTimeout -= 1
    $state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus
    Write-Host "Checking state... $state found"
}

# Exit if the node was unable to be disabled
if ($state -ne [System.Fabric.Query.NodeStatus]::Disabled)
{
    Write-Error "Disable failed with state $state"
}
else
{
    # Stop node
    $stopid = New-Guid
    Start-ServiceFabricNodeTransition -Stop -OperationId $stopid -NodeName $nodename -NodeInstanceId $nodeid -StopDurationInSeconds 300

    $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
    $loopTimeout = 10

    # Watch the transaction
    while (($state -eq [System.Fabric.TestCommandProgressState]::Running) -and ($loopTimeout -ne 0))
    {
        Start-Sleep 5
        $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
        Write-Host "Checking state... $state found"
    }

    if ($state -ne [System.Fabric.TestCommandProgressState]::Completed)
    {
        Write-Error "Stop transaction failed with $state"
    }
    else
    {
        # Remove the node from the cluster
        Remove-ServiceFabricNodeState -NodeName $nodename -TimeoutSec 300 -Force
    }
}
```

以下の **sfctl** コードでは、コマンド `sfctl node list --query "sort_by(items[*], &name)[-1].name"` を使用して、最後に作成されたノードの **node-name** 値を取得します。

```shell
# Inform the node that it is going to be removed
sfctl node disable --node-name _nt1vm_5 --deactivation-intent 4 -t 300

# Stop the node using a random guid as our operation id
sfctl node transition --node-instance-id 131541348482680775 --node-name _nt1vm_5 --node-transition-type Stop --operation-id c17bb4c5-9f6c-4eef-950f-3d03e1fef6fc --stop-duration-in-seconds 14400 -t 300

# Remove the node from the cluster
sfctl node remove-state --node-name _nt1vm_5
```

> [!TIP]
> 以下の **sfctl** クエリを使用して、各ステップの状態を確認します。
>
> **非アクティブ化状態を確認する**
> `sfctl node list --query "sort_by(items[*], &name)[-1].nodeDeactivationInfo"`
>
> **停止状態を確認する**
> `sfctl node list --query "sort_by(items[*], &name)[-1].isStopped"`
>

### <a name="scale-in-the-scale-set"></a>スケール セットのスケールイン

これで Service Fabric ノードがクラスターから削除されたので、仮想マシン スケール セットをスケールインできます。 次の例では、スケール セットの容量が 1 ずつ減少します。

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

このコードでは容量を 5 に設定します。

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Service Fabric Explorer で確認できる動作
クラスターをスケールアウトすると、Service Fabric Explorer に、クラスターの一部であるノード (仮想マシン スケール セットインスタンス) の数が反映されます。  ただし、クラスターをスケールインすると、適切なノードの名前を指定して [Remove-ServiceFabricNodeState cmd](/powershell/module/servicefabric/remove-servicefabricnodestate) を呼び出さない限り、削除されたノード/VM インスタンスが異常状態を示したまま表示されます。   

この動作についての説明を次に示します。

Service Fabric Explorer で示されているノードは、現在/過去におけるクラスターのノード数について、Service Fabric システム サービス (具体的には FM) が認識している内容が反映されたものです。 仮想マシン スケール セットをスケールインした場合、VM が削除された一方で、FM システム サービスはノード (削除された VM にマップされていたもの) が復元されると認識したままとなります。 そのため、(正常性状態がエラーまたは不明になる場合でも) Service Fabric Explorer はそのノードを表示し続けます。

VM が削除されたときに、ノードが削除されるようにするには、2 つのオプションを使用できます。

1. クラスターのノードの種類に Gold または Silver の耐久性レベルを選択します。これにより、インフラストラクチャの統合が提供されます。 これによって、スケールインしたときに、システム サービス (FM) の状態から自動的にノードが削除されます。
[耐久性レベルの詳細](service-fabric-cluster-capacity.md)に関するページをご覧ください

> [!NOTE]
> 持続性レベルが Gold または Silver である任意の仮想マシン スケール セットのノードを最小数である 5 つ保持します。 このしきい値を下回ってスケールインした場合は、クラスターがエラー状態になり、削除されたノードを手動でクリーンアップすることが必要になります。

2. VM インスタンスがスケールインされたら、[Remove-ServiceFabricNodeState コマンドレット](/powershell/module/servicefabric/remove-servicefabricnodestate)を呼び出す必要があります。

> [!NOTE]
> Service Fabric クラスターが可用性を維持し、状態を保持するには、一定数のノードが常にアップしている必要があります。これは、"維持クォーラム" と呼ばれます。 そのため、先に[状態の完全バックアップ](service-fabric-reliable-services-backup-restore.md)を実行していた場合を除き、クラスター内のすべてのコンピューターをシャットダウンするのは一般に安全ではありません。
> 
> 

## <a name="next-steps"></a>次のステップ
次を確認して、クラスター容量の計画、クラスターのアップグレード、およびサービスのパーティション分割についても学習してください。

* [クラスター容量の計画](service-fabric-cluster-capacity.md)
* [クラスターのアップグレード](service-fabric-cluster-upgrade.md)
* [最大のスケールに対応するためのパーティションのステートフル サービス](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-in-out/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-in-out/ClusterResources.png

[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
