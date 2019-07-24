---
title: 'Service Fabric クラスター Resource Manager: 移動コスト | Microsoft Docs'
description: Service Fabric サービスの移動コストの概要
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: f022f258-7bc0-4db4-aa85-8c6c8344da32
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 1bd049e6f929b6c3247ca1842412d5527605e643
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58669981"
---
# <a name="service-movement-cost"></a>サービスの移動コスト
クラスターに対してどのような変更を行うかを決定するときに Service Fabric Cluster Resource Manager が考慮する要因は、それらの変更のコストです。 "コスト" の概念は、クラスターをどの程度向上させることができるかとのトレードオフです。 コストは、均衡化、デフラグ、およびその他の要件に対応するためにサービスを移動するときに考慮されます。 その目標は、中断が最も少ない方法またはコストがかからない方法で要件を満たすことです。 

サービスの移動には、最低でも CPU 時間とネットワーク帯域幅のコストがかかります。 ステートフル サービスでは、サービスの状態をコピーする必要があり、メモリとディスクがさらに消費されます。 Azure Service Fabric Cluster Resource Manager が見つけ出すソリューションのコストを最小限に抑えることによって、クラスターのリソースが不必要に費やされることがないことを保証できます。 一方でクラスター内のリソースの割り当てを大幅に改善するソリューションを無視することは避けたいものです。

Cluster Resource Manager は、クラスターを管理するときに、コストを 2 つの方法で計算し、それらを制限します。 最初のメカニズムは、実行するすべての移動の回数を単純にカウントします。 2 つのソリューションがほぼ同じバランス (スコア) で生成される場合、Cluster Resource Manager は、コスト (移動の合計数) が小さいほうを優先します。

この戦略は効果的に機能します。 ただし、既定の負荷や静的な負荷の場合と同様に、すべての移動が等しい複雑なシステムではそうはいきません。 一部のコストがはるかに高くなる可能性があります。

## <a name="setting-move-costs"></a>移動コストの設定 
サービスの作成時に、サービスの既定の移動コストを指定できます。

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -DefaultMoveCost Medium
```

C#: 

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
//set up the rest of the ServiceDescription
serviceDescription.DefaultMoveCost = MoveCost.Medium;
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

サービスの作成後に、サービスの MoveCost を動的に指定するか更新することもできます。 

PowerShell: 

```posh
Update-ServiceFabricService -Stateful -ServiceName "fabric:/AppName/ServiceName" -DefaultMoveCost High
```

C#:

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.DefaultMoveCost = MoveCost.High;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), updateDescription);
```

## <a name="dynamically-specifying-move-cost-on-a-per-replica-basis"></a>レプリカごとの動的な移動コストの指定

上記のすべてのスニペットは、サービスの外部からサービス全体の MoveCost を一度に指定するためのものです。 ただし、移動コストは、特定のサービス オブジェクトの移動コストがその有効期間中に変化する場合に最も役に立ちます。 サービス自体が特定の時点でかかる移動コストを最も把握している可能性が高いため、実行時に個々の移動コストをレポートするためのサービス向けの API があります。 

C#:

```csharp
this.Partition.ReportMoveCost(MoveCost.Medium);
```

## <a name="impact-of-move-cost"></a>移動コストの影響
MoveCost には 4 つのレベルがあります: Zero、Low、Medium、High。 Zero を除き、MoveCost のレベルは相対的なものです。 移動コストが Zero の場合、移動にコストはかからず、ソリューションのスコアに対してカウントされません。 移動コストを High に設定しても、レプリカが 1 つの場所に維持されると保証されるわけでは*ありません*。

<center>

![移動するレプリカを選択する際の要因としての移動コスト][Image1]
</center>

移動コストは、同等のバランスを最も簡単に維持したまま全体的な中断が最小限になるソリューションを見つけるのに役立ちます。 サービスのコストの概念はさまざまなものに関係する可能性があります。 最も一般的なものは次のとおりです。

- サービスが移動する必要のある状態またはデータの量。
- クライアント切断のコスト。 通常、プライマリ レプリカの移動は、セカンダリ レプリカの移動よりもコストが高くなります。
- 実行中の操作を中断するコスト。 一部のデータ ストア レベル操作またはクライアント コールの応答で実行される操作にはコストがかかります。 特定の時点以降では、必要でなければ処理を中止しない方が適切です。 操作の実行中に移動コストを高くすると、そのサービス オブジェクトが移動される可能性は低くなります。 操作が完了したら、コストを通常の設定に戻します。

## <a name="enabling-move-cost-in-your-cluster"></a>クラスターでの移動コストの有効化
細かい MoveCosts を考慮するためには、MoveCost をクラスターで有効にする必要があります。 この設定がない場合は、移動をカウントする既定のモードを使用して MoveCost が計算され、MoveCost レポートは無視されます。


ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="UseMoveCostReports" Value="true" />
        </Section>
```

スタンドアロン デプロイの ClusterConfig.json 経由または Azure でホストされたクラスターの Template.json 経由:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "UseMoveCostReports",
          "value": "true"
      }
    ]
  }
]
```

## <a name="next-steps"></a>次の手順
- Service Fabric クラスター リソース マネージャーは、メトリックを使用して、クラスターの利用量と容量を管理します。 メトリックの詳細とその構成方法については、「 [Service Fabric のリソース使用量と負荷をメトリックで管理する](service-fabric-cluster-resource-manager-metrics.md)」を参照してください。
- クラスター リソース マネージャーでクラスターの負荷を管理し、分散するしくみについては、「 [Service Fabric クラスターの均衡をとる](service-fabric-cluster-resource-manager-balancing.md)」を参照してください。

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png
