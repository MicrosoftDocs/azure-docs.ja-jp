---
title: "Service Fabric クラスター リソース マネージャー - 配置ポリシー | Microsoft Docs"
description: "Service Fabric サービスの追加の配置ポリシーとルールの概要"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 5c2d19c6-dd40-4c4b-abd3-5c5ec0abed38
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 7fa35de8b99132ad1c10229a9bb2231f11fdbaa0
ms.lasthandoff: 01/07/2017


---
# <a name="placement-policies-for-service-fabric-services"></a>Service Fabric サービスの配置ポリシー
一部のまれなシナリオでは、異なる追加ルールを多く構成する必要があります。 このようなシナリオのいくつかの例は、次のとおりです。
* Service Fabric のクラスターが複数のオンプレミス データセンターや Azure のリージョンなど、地理的に離れた場所に広がっている場合。
* 環境が地政学的な管理の異なる領域に広がっている場合 (もしくは法的、政策的な境界について考慮しなければならない場合)。
* 長距離を移動するクラスター内の通信や、転送速度が遅く信頼性の低いネットワークを経由する通信のため、実際のパフォーマンス/待機時間に関して考慮すべき事項がある。

このような状況では、特定の地域では特定のサービスを常に実行するか、または決して実行しないことが重要な場合があります。 同様に、特定の地域にはプライマリを配置して、エンドユーザーの待機時間を最小限に抑えることも重要です。

高度な配置ポリシーは次のとおりです。

1. 無効なドメイン
2. 必要なドメイン
3. 優先ドメイン
4. レプリカのパッキングを許可しない

次の制御の多くはノード プロパティおよび配置の制約によって構成できますが、なかにはより複雑なものもあります。 簡素化するために、Service Fabric のクラスター リソース マネージャーでは追加の配置ポリシーが用意されています。 他の配置の制約と同様に、配置ポリシーを名前付きサービス インスタンスごとに構成して動的に更新できます。

## <a name="specifying-invalid-domains"></a>無効なドメインを指定する
invalidDomain 配置ポリシーでは、特定の障害ドメインがこのワークロードに対して無効になるように指定できます。 地政学的または企業のポリシー上の理由などにより、特定の地域で特定のサービスが実行されないようにできます。 無効なドメインは、別々のポリシーで複数指定できます。

<center>
![無効なドメインの例][Image1]
</center>

コード:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
invalidDomain.DomainName = "fd:/DCEast"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## <a name="specifying-required-domains"></a>必要なドメインを指定する
required domain 配置ポリシーでは、指定したドメインにサービスのすべてのステートフル レプリカまたはステートレス サービス インスタンスが存在している必要があります。 必要なドメインは、別々のポリシーで複数指定できます。

<center>
![必要なドメインの例][Image2]
</center>

コード:

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## <a name="specifying-a-preferred-domain-for-the-primary-replicas"></a>プライマリ レプリカの優先ドメインを指定する
優先プライマリ ドメインは変わったコントロールであり、プライマリが存在する必要がある障害ドメインの選択が許可されます (選択が可能な場合)。 すべてが正常な場合、プライマリは最終的にこのドメインに配置されます。 何らかの理由によりドメインまたはプライマリ レプリカで障害が発生した場合、またはシャットダウンした場合、プライマリは別の場所に移行します。 この新しい場所が優先ドメインではない場合、クラスター リソース マネージャーによって可能な限り速やかに優先ドメインに戻されます。 元来、この設定は、ステートフル サービスでのみ有効です。 このポリシーは、Azure のリージョンまたは複数のデータセンターに分散しているものの、特定の場所にプライマリ レプリカを配置することを希望するクラスターに最適です。 プライマリをユーザーの近くに保つことで、特に読み取りにおいて待機時間の短縮に役立ちます。

<center>
![優先プライマリ ドメインとフェールオーバー][Image3]
</center>

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replicas-to-be-distributed-among-all-domains-and-disallowing-packing"></a>すべてのドメイン間で配信することとパッキングを許可しないことをレプリカに要求する
レプリカは_通常_クラスターが正常なドメイン間で配信されますが、特定のパーティションのレプリカが、1 つのドメインに一時的にパッキングされるという結果になる場合があります。 たとえば、3 つの障害ドメイン (0、1、および&2;) でクラスターに&9; 個のノードが、サービスに&3; つのレプリカがあり、 障害ドメイン&1; および&2; のこれらのレプリカに使用したノードが停止したと仮定します。 通常、クラスター リソース マネージャーによって、これらの同じ障害ドメインの他のノードが優先されます。 この場合、容量の問題でこれらのドメイン内の他のいずれのノードも有効ではなかったと仮定します。 クラスター リソース マネージャーがこれらのレプリカの交換を構築した場合、障害ドメイン&0; のノードを選択する必要がありますが、 _これ_によって、障害ドメインの制約が違反される状況が発生します。 また、レプリカ セット全体が失われる可能性も高まります (障害ドメイン 0 が完全に失われた場合)。 制約と制約の優先順位の概要については、[このトピック](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities)をご覧ください。

`The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: FaultDomain` のような正常性の警告を受け取ったことがある場合は、この状況、または似た状況が既に発生していることになります。 これはまれなことで、発生しても、ノードが戻ってくるため、通常このような状況は一過性のものです。 ノードが長時間停止し、クラスター リソース マネージャーが交換を構築する必要があっても、ほとんどの場合は、最適な障害ドメインで他のノードが利用できます。

少数のドメインにパッキングされる場合も、常に目標とする数のレプリカを持つ必要があるワークロードもあります。 このようなワークロードでは全体で同時発生する永続的なドメイン障害がないと見込まれ、ほとんどの場合はローカル状態を回復できます。 他のワークロードでは、正確性やデータ損失のリスクを冒すよりも、先にダウンタイムが発生することを選びます。 ほとんどの実稼働ワークロードは&4; つ以上のレプリカ、4 つ以上の障害ドメイン、および障害ドメインごとに多数の有効なノードを使って実行されるため、既定ではドメイン配信が不要です。 これで、結果としてドメインに複数のレプリカが一時的にパッキングされることになっても、通常の負荷分散とフェールオーバーによってこれらのケースが対処されます。

特定のワークロードに対してこのようなパッキングを無効にする場合は、サービスで "RequireDomainDistribution" ポリシーを指定します。 このポリシーが設定されると、クラスター リソース マネージャーにより、同じパーティションのレプリカが複数同じ障害またはアップグレード ドメインに存在することが許可されないよう保証されます。

コード:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

では、これらの構成を地理的に分散していないクラスターのサービスに使用することはできるでしょうか。 もちろん、できます。 しかし、あまり利点はありません。 必要な、無効な、および優先ドメインの構成は、実際に地理的に離れた場所に分散しているクラスターを実行していない場合は、避ける必要があります。 特定のワークロードを単一のラックで実行したり、ローカル クラスターのセグメントを他のものより優先することは意味がありません。 異なるハードウェアの構成を複数のドメインや、通常の配置の制約とノードのプロパティを通じて処理されるドメインに分散する必要があります。

## <a name="next-steps"></a>次のステップ
* サービスの構成に利用できるその他のオプションの詳細については、[サービスの構成についての学習](service-fabric-cluster-resource-manager-configure-services.md)に関する記事を参照してください。

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png

