---
title: Azure Service Fabric のサービスとコンテナーの自動スケーリング
description: Azure Service Fabric では、サービスとコンテナー用の自動スケーリング ポリシーを設定できます。
author: radicmilos
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: miradic
ms.custom: devx-track-csharp
ms.openlocfilehash: 060bb9dcdd504846c76ab4c782b2857fdddfa394
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91354804"
---
# <a name="introduction-to-auto-scaling"></a>自動スケーリングの概要
自動スケーリングは Service Fabric の追加機能であり、サービスによって報告される負荷またはリソースの使用量に基づいて、サービスを動的にスケーリングする機能です。 自動スケーリングは優れた柔軟性を提供し、必要に応じてサービスのインスタンスまたはパーティションを追加でプロビジョニングできます。 自動スケーリングは、プロセス全体が自動化された透過的なものであり、サービスのポリシーを設定した後は、サービス レベルの手動でのスケーリング操作は必要ありません。 自動スケーリングは、サービスの作成時に有効にできます。または、サービスを更新することでいつでも有効にできます。

自動スケーリングが役に立つ一般的なシナリオは、特定のサービスにかかる負荷が時間の経過と共に変化する場合です。 たとえば、ゲートウェイなどのサービスは、着信要求を処理するために必要なリソースの量に基づいてスケーリングできます。 その場合のスケーリング ルールがどのようになるかの例を見てみましょう。
* ゲートウェイのすべてのインスタンスが平均して 2 つ以上のコアを使用している場合は、インスタンスを 1 つ追加することで、ゲートウェイ サービスをスケールアウトします。 この操作を 1 時間ごとに行いますが、インスタンスの合計数が 7 つを超えることはありません。
* ゲートウェイのすべてのインスタンスが平均して 0.5 未満のコアを使用している場合は、インスタンスを 1 つ削除することで、サービスをスケールインします。 この操作を 1 時間ごとに行いますが、インスタンスの合計数が 3 つより少なくなることはありません。

自動スケーリングは、コンテナーと通常の Service Fabric サービスの両方でサポートされます。 自動スケーリングを使用するには、Service Fabric ランタイムのバージョン 6.2 以降が実行されている必要があります。 

この記事の残りの部分では、スケーリング ポリシーと自動スケーリングを有効または無効にする方法について説明し、この機能をどのように使用するかの例を示します。

## <a name="describing-auto-scaling"></a>自動スケーリングの記述
Service Fabric クラスター内の各サービスに対して、自動スケーリング ポリシーを定義できます。 それぞれのスケーリング ポリシーは、2 つの部分で構成されます。
* **スケーリング トリガー** は、サービスのスケーリングがいつ実行されるかを記述します。 トリガーに定義された条件が定期的にチェックされ、サービスをスケーリングする必要があるかどうかが決定されます。

* **スケーリング メカニズム** は、トリガーされたときにスケーリングをどのように実行するかを記述します。 メカニズムは、トリガーの条件を満たす場合にのみ適用されます。

現在サポートされているすべてのトリガーは、[論理負荷メトリック](service-fabric-cluster-resource-manager-metrics.md)、または CPU やメモリ使用量などの物理メトリックのいずれかで機能します。 どちらの場合でも、Service Fabric は、報告されるメトリックの負荷を監視し、定期的にトリガーを評価してスケーリングが必要かどうかを決定します。

自動スケーリング用に現在サポートされているメカニズムは 2 つあります。 1 つ目はステートレス サービスまたはコンテナー用であり、自動スケーリングは、[インスタンス](service-fabric-concepts-replica-lifecycle.md)を追加または削除することで実行されます。 ステートフル サービスとステートレス サービスでは、自動スケーリングは、サービスに対して指定された[パーティション](service-fabric-concepts-partitioning.md)を追加または削除することで実行することもできます。

> [!NOTE]
> 現時点では、サービスごとにスケーリング ポリシーが 1 つだけサポートされており、スケーリング ポリシーごとにスケーリング トリガーが 1 つだけサポートされています。

## <a name="average-partition-load-trigger-with-instance-based-scaling"></a>インスタンス ベースのスケーリングで使用するパーティションの平均負荷トリガー
トリガーの 1 つ目の種類は、ステートレス サービス パーティション内のインスタンスの負荷に基づいています。 メトリック負荷が平滑化されてパーティションのすべてのインスタンスの負荷が取得された後、これらの値がパーティションのすべてのインスタンスで平均化されます。 サービスがどの時点でスケーリングされるかを決定する 3 つの要素があります。

* "_負荷のしきい値の下限_" は、サービスがどの時点で **スケールイン** されるかを決定します。 パーティションのすべてのインスタンスの平均負荷がこの値よりも小さい場合、サービスはスケールインされます。
* "_負荷のしきい値の上限_" は、サービスがどの時点で **スケールアウト** されるかを決定します。パーティションのすべてのインスタンスの平均負荷がこの値よりも大きい場合、サービスはスケールアウトされます。
* "_スケーリング間隔_" は、トリガーをチェックする頻度を決定します。 トリガーがチェックされ、スケーリングが必要な場合は、メカニズムが適用されます。 スケーリングが必要でない場合は、アクションは行われません。 どちらの場合も、トリガーは、スケール間隔が経過するまで再度チェックされることはありません。

このトリガーは、ステートレス サービス (ステートレス コンテナーまたは Service Fabric サービス) でのみ使用できます。 サービスに複数のパーティションがある場合、トリガーはパーティションごとに個別に評価され、パーティションごとに指定されたメカニズムが個別に適用されます。 そのため、このような場合は、サービスのパーティションの負荷に基づいて、一部をスケールアウト、一部をスケールイン、一部をスケーリングしないという操作を同時に行うことができます。

このトリガーで使用できる唯一のメカニズムは、PartitionInstanceCountScaleMechanism です。 このメカニズムを適用する方法を決定する 3 つの要素があります。
* "_スケールの増分_" は、メカニズムがトリガーされたときに追加または削除されるインスタンスの数を決定します。
* "_最大インスタンス数_" は、スケーリングの上限を定義します。 パーティションのインスタンス数がこの制限に達すると、負荷に関係なくサービスはスケールアウトされなくなります。 この制限は、値 -1 を指定することで省略できます。その場合、サービスは可能な限りスケールアウトされます (上限はクラスターで使用可能なノードの数になります)。
* "_最小インスタンス数_" は、スケーリングの下限を定義します。 パーティションのインスタンス数がこの制限に達すると、負荷に関係なくサービスはスケールインされなくなります。

## <a name="setting-auto-scaling-policy-for-instance-based-scaling"></a>インスタンス ベースのスケーリングのための自動スケーリング ポリシーの設定

### <a name="using-application-manifest"></a>アプリケーション マニフェストの使用
``` xml
<LoadMetrics>
<LoadMetric Name="MetricB" Weight="High"/>
</LoadMetrics>
<ServiceScalingPolicies>
<ScalingPolicy>
    <AveragePartitionLoadScalingTrigger MetricName="MetricB" LowerLoadThreshold="1" UpperLoadThreshold="2" ScaleIntervalInSeconds="100"/>
    <InstanceCountScalingMechanism MinInstanceCount="3" MaxInstanceCount="4" ScaleIncrement="1"/>
</ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>C# API の使用
```csharp
FabricClient fabricClient = new FabricClient();
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//set up the rest of the ServiceDescription
AveragePartitionLoadScalingTrigger trigger = new AveragePartitionLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new PartitionInstanceCountScaleMechanism();
mechanism.MaxInstanceCount = 3;
mechanism.MinInstanceCount = 1;
mechanism.ScaleIncrement = 1;
trigger.MetricName = "servicefabric:/_CpuCores";
trigger.ScaleInterval = TimeSpan.FromMinutes(20);
trigger.LowerLoadThreshold = 1.0;
trigger.UpperLoadThreshold = 2.0;
ScalingPolicyDescription policy = new ScalingPolicyDescription(mechanism, trigger);
serviceDescription.ScalingPolicies.Add(policy);
//as we are using scaling on a resource this must be exclusive service
//also resource monitor service needs to be enabled
serviceDescription.ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```
### <a name="using-powershell"></a>PowerShell の使用
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.PartitionInstanceCountScaleMechanism
$mechanism.MinInstanceCount = 1
$mechanism.MaxInstanceCount = 6
$mechanism.ScaleIncrement = 2
$trigger = New-Object -TypeName System.Fabric.Description.AveragePartitionLoadScalingTrigger
$trigger.MetricName = "servicefabric:/_CpuCores"
$trigger.LowerLoadThreshold = 0.3
$trigger.UpperLoadThreshold = 0.8
$trigger.ScaleInterval = New-TimeSpan -Minutes 10
$scalingpolicy = New-Object -TypeName System.Fabric.Description.ScalingPolicyDescription
$scalingpolicy.ScalingMechanism = $mechanism
$scalingpolicy.ScalingTrigger = $trigger
$scalingpolicies = New-Object 'System.Collections.Generic.List[System.Fabric.Description.ScalingPolicyDescription]'
$scalingpolicies.Add($scalingpolicy)
#as we are using scaling on a resource this must be exclusive service
#also resource monitor service needs to be enabled
Update-ServiceFabricService -Stateless -ServiceName "fabric:/AppName/ServiceName" -ScalingPolicies $scalingpolicies
```

## <a name="average-service-load-trigger-with-partition-based-scaling"></a>パーティション ベースのスケーリングで使用するサービスの平均負荷トリガー
2 つ目のトリガーは、1 つのサービスのすべてのパーティションの負荷に基づいています。 最初にメトリック負荷が平滑化されて、パーティションのすべてのレプリカまたはインスタンスの負荷が取得されます。 ステートフル サービスでは、パーティションの負荷はプライマリ レプリカの負荷であるとみなされ、ステートレス サービスでは、パーティションの負荷は、パーティションのすべてのインスタンスの平均負荷であるとみなされます。 これらの値がサービスのすべてのパーティションで平均化され、その値を使用して自動スケーリングがトリガーされます。 前のメカニズムと同じように、サービスがどの時点でスケーリングされるかを決定する 3 つの要素があります。

* "_負荷のしきい値の下限_" は、サービスがどの時点で **スケールイン** されるかを決定します。 サービスのすべてのパーティションの平均負荷がこの値よりも小さい場合、サービスはスケールインされます。
* "_負荷のしきい値の上限_" は、サービスがどの時点で **スケールアウト** されるかを決定します。サービスのすべてのパーティションの平均負荷がこの値よりも大きい場合、サービスはスケールアウトされます。
* "_スケーリング間隔_" は、トリガーをチェックする頻度を決定します。 トリガーがチェックされ、スケーリングが必要な場合は、メカニズムが適用されます。 スケーリングが必要でない場合は、アクションは行われません。 どちらの場合も、トリガーは、スケール間隔が経過するまで再度チェックされることはありません。

このトリガーは、ステートフル サービスとステートレス サービスの両方で使用できます。 このトリガーで使用できる唯一のメカニズムは、AddRemoveIncrementalNamedPartitionScalingMechanism です。 サービスがスケールアウトされるときは新しいパーティションが追加され、サービスがスケールインされるときは既存のパーティションのいずれかが削除されます。 サービスの作成または更新時にチェックされる制限があります。これらの条件が満たされない場合、サービスの作成または更新は失敗します。
* サービスでは名前付きパーティション スキームを使用する必要があります。
* パーティション名は連続した整数にする必要があります。例: "0"、"1"、...
* 最初のパーティション名は "0" にする必要があります。

たとえば、サービスの初回の作成が 3 つのパーティションで行われる場合、パーティション名として有効な名前は "0"、"1"、および "2" のみです。

実行される実際の自動スケーリング操作も、この名前スキームに従います。
* サービスの現在のパーティションの名前が "0"、"1"、および "2" のときにスケールアウトする場合は、"3" という名前のパーティションが追加されます。
* サービスの現在のパーティションの名前が "0"、"1"、および "2" のときにスケールインする場合は、"2" という名前のパーティションが削除されます。

インスタンスの追加または削除によるスケーリングを使用するメカニズムと同じように、このメカニズムの適用方法を決定する 3 つのパラメーターがあります。
* "_スケールの増分_" は、メカニズムがトリガーされたときに追加または削除されるパーティションの数を決定します。
* "_最大パーティション数_" は、スケーリングの上限を定義します。 サービスのパーティション数がこの制限に達すると、負荷に関係なくサービスはスケールアウトされなくなります。 この制限は、値 -1 を指定することで省略できます。その場合、サービスは可能な限りスケールアウトされます (上限はクラスターの実際の容量になります)。
* "_最小インスタンス数_" は、スケーリングの下限を定義します。 サービスのパーティション数がこの制限に達すると、負荷に関係なくサービスはスケールインされなくなります。

> [!WARNING] 
> AddRemoveIncrementalNamedPartitionScalingMechanism がステートフル サービスで使用される場合、Service Fabric によって、**通知や警告なしで** パーティションの追加または削除が行われます。 スケーリング メカニズムがトリガーされている場合、データの再分割は実行されません。 スケールアウト操作では、新しいパーティションは空になり、スケールイン操作では、**パーティションはその中に含まれるすべてのデータと共に削除されます**。

## <a name="setting-auto-scaling-policy-for-partition-based-scaling"></a>パーティション ベースのスケーリングのための自動スケーリング ポリシーの設定

### <a name="using-application-manifest"></a>アプリケーション マニフェストの使用
``` xml
<NamedPartition>
    <Partition Name="0" />
</NamedPartition>
<ServiceScalingPolicies>
    <ScalingPolicy>
        <AverageServiceLoadScalingTrigger MetricName="servicefabric:/_MemoryInMB" LowerLoadThreshold="300" UpperLoadThreshold="500" ScaleIntervalInSeconds="600"/>
        <AddRemoveIncrementalNamedPartitionScalingMechanism MinPartitionCount="1" MaxPartitionCount="3" ScaleIncrement="1"/>
    </ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>C# API の使用
```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceUpdateDescription serviceUpdate = new StatefulServiceUpdateDescription();
AveragePartitionLoadScalingTrigger trigger = new AverageServiceLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new AddRemoveIncrementalNamedPartitionScalingMechanism();
mechanism.MaxPartitionCount = 4;
mechanism.MinPartitionCount = 1;
mechanism.ScaleIncrement = 1;
//expecting that the service already has metric NumberOfConnections
trigger.MetricName = "NumberOfConnections";
trigger.ScaleInterval = TimeSpan.FromMinutes(15);
trigger.LowerLoadThreshold = 10000;
trigger.UpperLoadThreshold = 20000;
ScalingPolicyDescription policy = new ScalingPolicyDescription(mechanism, trigger);
serviceUpdate.ScalingPolicies = new List<ScalingPolicyDescription>;
serviceUpdate.ScalingPolicies.Add(policy);
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), serviceUpdate);
```
### <a name="using-powershell"></a>PowerShell の使用
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.AddRemoveIncrementalNamedPartitionScalingMechanism
$mechanism.MinPartitionCount = 1
$mechanism.MaxPartitionCount = 3
$mechanism.ScaleIncrement = 2
$trigger = New-Object -TypeName System.Fabric.Description.AverageServiceLoadScalingTrigger
$trigger.MetricName = "servicefabric:/_MemoryInMB"
$trigger.LowerLoadThreshold = 5000
$trigger.UpperLoadThreshold = 10000
$trigger.ScaleInterval = New-TimeSpan -Minutes 25
$scalingpolicy = New-Object -TypeName System.Fabric.Description.ScalingPolicyDescription
$scalingpolicy.ScalingMechanism = $mechanism
$scalingpolicy.ScalingTrigger = $trigger
$scalingpolicies = New-Object 'System.Collections.Generic.List[System.Fabric.Description.ScalingPolicyDescription]'
$scalingpolicies.Add($scalingpolicy)
#as we are using scaling on a resource this must be exclusive service
#also resource monitor service needs to be enabled
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -TargetReplicaSetSize 3 -MinReplicaSetSize 2 -HasPersistedState true -PartitionNames @("0","1") -ServicePackageActivationMode ExclusiveProcess -ScalingPolicies $scalingpolicies
```

## <a name="auto-scaling-based-on-resources"></a>リソースに基づく自動スケーリング

実際のリソースに基づいてスケーリングするためにリソース モニター サービスを有効にするには、次のコードを使用します。

``` json
"fabricSettings": [
...      
],
"addonFeatures": [
    "ResourceMonitorService"
],
```
実際の物理リソースを表す 2 つのメトリックがあります。 1 つは実際の CPU 使用量を表す servicefabric:/_CpuCores であり (0.5 はコアの半分を表します)、他方はメモリ使用量を MB 単位で表わす servicefabric:/_MemoryInMB です。
ResourceMonitorService は、ユーザー サービスの CPU とメモリの使用量を追跡します。 このサービスは、潜在的な短時間持続するスパイクに対応するために、加重移動平均を適用します。 リソースの監視は、Windows ではコンテナー化されたアプリケーションとコンテナー化されていないアプリケーションの両方でサポートされ、Linux ではコンテナー化されたアプリケーションでサポートされます。 リソース ベースの自動スケーリングは、[占有プロセス モデル](service-fabric-hosting-model.md#exclusive-process-model)でアクティブ化されるサービスに対してのみ有効にできます。

## <a name="next-steps"></a>次のステップ
[アプリケーションのスケーラビリティ](service-fabric-concepts-scalability.md)について学習します。
