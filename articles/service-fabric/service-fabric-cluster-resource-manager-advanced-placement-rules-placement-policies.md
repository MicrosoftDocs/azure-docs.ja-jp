---
title: Service Fabric クラスター リソース マネージャー - 配置ポリシー
description: Service Fabric サービスの追加の配置ポリシーとルールの概要
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 263e45928642aa74d682fc490e424a24deeb8076
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98790683"
---
# <a name="placement-policies-for-service-fabric-services"></a>Service Fabric サービスの配置ポリシー
配置ポリシーは、特定のあまり一般的ではないシナリオでサーバーの配置の制御に使用できる追加の規則です。 このようなシナリオのいくつかの例は、次のとおりです。

- Service Fabric クラスターが複数のオンプレミス データセンターや Azure のリージョンなど、地理的に離れた場所に広がっている場合。
- 環境が地政学的または法的な管理の異なる領域に広がっている場合、またはポリシーの境界を設定する必要があるその他の場合
- 長距離、低速または信頼性の低いネットワーク リンクを使用するため、通信パフォーマンスまたは待機時間に関する考慮事項があります。
- 他のワークロードまたはユーザーとの近接性を考慮したベスト エフォートとして、一部のワークロードを併置しておく必要があります。
- 単一ノードにパーティションのステートレス インスタンスが複数必要な場合

このような要件の多くは、クラスターの障害ドメインとして表されるクラスターの物理レイアウトと一致しています。 

このようなシナリオに対応できる高度な配置ポリシーを次に示します。

1. 無効なドメイン
2. 必要なドメイン
3. 優先ドメイン
4. レプリカのパッキングを許可しない
5. ノードでの複数ステートレス インスタンスを許可

次の制御の多くはノード プロパティおよび配置の制約によって構成できますが、なかにはより複雑なものもあります。 簡素化するために、Service Fabric のクラスター リソース マネージャーでは追加の配置ポリシーが用意されています。 配置ポリシーは、指定されたサービス インスタンスごとに構成されます。 また、動的に更新することもできます。

## <a name="specifying-invalid-domains"></a>無効なドメインを指定する
**invalidDomain** 配置ポリシーでは、特定の障害ドメインが特定のサービスに対して無効になるように指定できます。 地政学的または企業のポリシー上の理由などにより、特定の地域で特定のサービスが実行されないようにできます。 無効なドメインは、別々のポリシーで複数指定できます。

<center>

![無効なドメインの例][Image1]
</center>

コード:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
invalidDomain.DomainName = "fd:/DCEast"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## <a name="specifying-required-domains"></a>必要なドメインを指定する
必要なドメインの配置ポリシーでは、サービスが指定したドメインにのみ存在する必要があります。 必要なドメインは、別々のポリシーで複数指定できます。

<center>

![必要なドメインの例][Image2]
</center>

コード:

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## <a name="specifying-a-preferred-domain-for-the-primary-replicas-of-a-stateful-service"></a>ステートフル サービスのプライマリ レプリカの優先ドメインを指定する
優先プライマリ ドメインでは、プライマリを配置する障害ドメインを指定します。 すべてが正常な場合、プライマリは最終的にこのドメインに配置されます。 ドメインまたはプライマリ レプリカでエラーがが発生した場合またはシャットダウンした場合、プライマリはその他の場所 (理想的には同じドメイン内) に移行されます。 この新しい場所が優先ドメインではない場合、クラスター リソース マネージャーによって可能な限り速やかに優先ドメインに戻されます。 元来、この設定は、ステートフル サービスでのみ有効です。 このポリシーは、Azure のリージョンまたは複数のデータセンターに分散しているものの、特定の場所に配置したいサービスがあるクラスターに最適です。 ユーザーまたは他のサービスに近い場所にプライマリを維持することで、待機時間が短くなります。既定でプライマリが処理する読み取りの場合は特に短くなります。

<center>

![優先プライマリ ドメインとフェールオーバー][Image3]
</center>

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(primaryDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replica-distribution-and-disallowing-packing"></a>レプリカの分散を必須にしてパッキングを許可しない
クラスターが正常に動作している場合、_通常_、レプリカは障害ドメインとアップグレード ドメイン全体に分散されています。 ただし、指定されたパーティションの複数のレプリカが、1 つのドメインに一時的にパッキングされる結果になる場合があります。 たとえば、fd:/0、fd:/1、fd:/2 という 3 つの障害ドメイン内に 9 つのノードがあるクラスターがあるとします。 また、サービスには 3 つのレプリカがあります。 この環境で fd:/1 と fd:/2 のレプリカに使用されていたノードが停止したとします。 通常、クラスター リソース マネージャーによって、これらの同じ障害ドメインの他のノードが優先されます。 この場合、容量の問題でこれらのドメイン内の他のいずれのノードも有効ではなかったと仮定します。 Cluster Resource Manager でそれらのレプリカの置き換えを構築する場合、fd:/0 のノードを選択する必要があります。 ただし、_その選択_ を実行すると、障害ドメインの制約違反になる状況が生じます。 複数のレプリカをパッキングすると、レプリカ セット全体が停止したり、失われたりする可能性が高まります。 

> [!NOTE]
> 制約と制約の優先順位の概要については、[このトピック](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities)をご覧ください。
>

"`The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: FaultDomain`" などの正常性メッセージを受け取ったことがある場合は、この状況、または似た状況が既に発生していることになります。 通常、一時的にパッキングされるのは 1 つまたは 2 つのレプリカのみです。 特定のドメイン内のレプリカのクォーラムよりも少ない限りは問題ありません。 パッキングはまれなことで、発生しても、ノードが戻ってくるため、通常このような状況は一過性のものです。 ノードが長時間停止し、クラスター リソース マネージャーが交換を構築する必要があっても、ほとんどの場合は、最適な障害ドメインで他のノードが利用できます。

少数のドメインにパッキングされる場合でも、常に目標とする数のレプリカを持つことを優先するワークロードもあります。 このようなワークロードでは全体で同時発生する永続的なドメイン障害がないと見込まれ、ほとんどの場合はローカル状態を回復できます。 他のワークロードでは、正確性やデータ損失のリスクを冒すよりも、先にダウンタイムが発生することを選びます。 ほとんどの実稼働ワークロードは、4 つ以上のレプリカ、4 つ以上の障害ドメイン、および障害ドメインごとに多数の有効なノードを使って実行されます。 そのため、既定の動作ではドメインのパッキングが許可されています。 この既定の動作により、一時的なドメインのパッキングが生じるような極端な場合でも、通常の分散とフェールオーバーで処理できます。

特定のワークロードに対してこのようなパッキングを無効にする場合は、サービスで `RequireDomainDistribution` ポリシーを指定します。 このポリシーが設定されると、Cluster Resource Manager により、同じパーティションの 2 つのレプリカが同じ障害またはアップグレード ドメイン内で実行されないよう保証されます。

コード:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

では、これらの構成を地理的に分散していないクラスターのサービスに使用することはできるでしょうか。 使用できますが、あまり利点はありません。 シナリオで必要な場合を除き、必須のドメイン、無効なドメイン、および優先されるドメインの構成は避けることをお勧めします。 特定のワークロードを単一のラックで実行したり、ローカル クラスターのセグメントを他のものより優先することは意味がありません。 異なるハードウェアの構成を複数のドメインや、通常の配置の制約とノードのプロパティを通じて処理される障害ドメインに分散する必要があります。

## <a name="placement-of-multiple-stateless-instances-of-a-partition-on-single-node"></a>パーティションの複数ステートレス インスタンスの単一ノードへの配置
**AllowMultipleStatelessInstancesOnNode** 配置ポリシーを使うと、単一ノードにパーティションの複数ステートレス インスタンスを配置できます。 既定では、単一パーティションの複数インスタンスを 1 つのノードに配置することはできません。 -1 サービスを使用している場合でも、特定の名前付きサービスについて、クラスター内のノード数を超えてインスタンスの数をスケーリングすることはできません。 この配置ポリシーによってこの制限が削除されて、InstanceCount をノード数よりも多く指定できるようになります。

"`The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: ReplicaExclusion`" などの正常性メッセージを受け取ったことがある場合は、この状況、または似た状況が既に発生していることになります。 

サービスで `AllowMultipleStatelessInstancesOnNode` ポリシーを指定することによって、クラスター内のノード数を超える InstanceCount を設定できます。

コード:

```csharp
ServicePlacementAllowMultipleStatelessInstancesOnNodePolicyDescription allowMultipleInstances = new ServicePlacementAllowMultipleStatelessInstancesOnNodePolicyDescription();
serviceDescription.PlacementPolicies.Add(allowMultipleInstances);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName -Stateless –PartitionSchemeSingleton –PlacementPolicy @(“AllowMultipleStatelessInstancesOnNode”) -InstanceCount 10 -ServicePackageActivationMode ExclusiveProcess 
```

> [!NOTE]
> 配置ポリシーは現在プレビュー段階であり、`EnableUnsupportedPreviewFeatures` クラスター設定の背後にあります。 これは現在プレビュー機能であるため、プレビュー構成を設定すると、クラスターでアップグレードが実行されなくなります。 つまり、この機能を試すには、新しいクラスターの作成が必要になります。
>

> [!NOTE]
> 現時点で、このポリシーは、ExclusiveProcess [サービス パッケージ アクティブ化モード](/dotnet/api/system.fabric.description.servicepackageactivationmode)のステートレス サービスでのみサポートされています。
>

> [!WARNING]
> 静的ポート エンドポイントと共に使う場合、ポリシーはサポートされません。 両方を組み合わせて使うと、同じノード上で複数のインスタンスが同じポートにバインドしようとして、起動できなくなるような異常なクラスターになる可能性があります。 
>

> [!NOTE]
> この配置ポリシーで [MinInstanceCount](/dotnet/api/system.fabric.description.statelessservicedescription.mininstancecount) の値を大きくすると、アプリケーションのアップグレードが停止する可能性があります。 たとえば、5 ノードのクラスターがあり、InstanceCount=10 と設定されている場合、各ノードに 2 つのインスタンスが存在します。 MinInstanceCount=9 を設定した場合、試行されたアプリのアップグレードが停止する可能性があります。MinInstanceCount=8 の場合は、これを回避できます。
>

## <a name="next-steps"></a>次のステップ
- サービスの構成の詳細については、[サービスの構成についての学習](service-fabric-cluster-resource-manager-configure-services.md)に関する記事を参照してください。

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png
