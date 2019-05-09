---
title: Azure Service Fabric の容量計画とスケーリングのベスト プラクティス | Microsoft Docs
description: Service Fabric クラスターとアプリケーションの計画とスケーリングに関するベスト プラクティス。
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 425154958e4c60902b56f320f714a011b9095830
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57997352"
---
# <a name="capacity-planning-and-scaling"></a>容量計画とスケーリング

Azure Service Fabric クラスターの作成前、またはクラスターをホストするコンピューティング リソースをスケールする前に、容量についての計画を立てることが重要です。 容量の計画に関する詳細については、[Service Fabric クラスターの容量計画](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)に関するページを参照してください。 Nodetype とクラスターの特性を考慮することに加えて、追加しようとしている VM の数にかかわらず、その運用環境で完了までの所要時間が 1 時間より長い場合はスケーリング操作を計画します。

## <a name="auto-scaling"></a>自動スケーリング
スケーリング操作は、Azure リソース テンプレートのデプロイを介して実行する必要があります。これは、[リソース構成はコードとして]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)扱うことがベスト プラクティスであるためです。そして、仮想マシン スケール セットの自動スケーリングを使用すると、バージョン管理される Resource Manager テンプレートが、仮想マシン スケール セットのインスタンス数を不正確に定義する結果となり、将来のデプロイで意図しないのスケーリング操作を発生させるリスクが高まります。一般に、自動スケーリングは以下の場合に使用してください。

* 適切な容量が宣言されている Resource Manager テンプレートをデプロイしても、実際のユース ケースがサポートされない。
  * 手動でのスケーリングに加えて、[Azure DevOps Services での Azure リソース グループのデプロイ プロジェクトを使用した継続的インテグレーションと配信パイプライン]( https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts)を構成できます。これは一般に、[Azure Monitor REST API](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough) からクエリされる、仮想マシンのパフォーマンス メトリックを活用するロジック アプリによってトリガーされます。こうして、任意の必要なメトリックに基づいて効果的に自動スケーリングするのと同時に、Azure Resource Manager の付加価値を最適化します。
* 一度に 1 つの仮想マシン スケール セット ノードのみを水平方向にスケールする必要がある。
  * 一度に 3 つ以上のノードをスケール アウトするには、[仮想マシン スケール セットを追加して Service Fabric クラスターをスケール アウト](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out)する必要があります。そして最も安全なのは、仮想マシン スケール セットのノードを一度に 1 つ水平方向にスケールインおよびスケール アウトすることです。
* Service Fabric クラスターの信頼性が Silver 以上で、自動スケーリング ルールを構成する任意のスケール セットの持続性が Silver 以上である。
  * 自動スケーリング ルールの容量の "最小" は、仮想マシン インスタンス 5 つ以上である必要があります。また、ノードの種類がプライマリの場合の信頼性レベルが "最小" 以上である必要があります。

> [!NOTE]
> Azure Service Fabric のステートフル サービス fabric:/InfastructureService/< NODE_TYPE_NAME > は、持続性が Silver 以上であるすべてのノードの種類で実行されます。これは、クラスターのノードの種類がどれであっても Azure での実行がサポートされる唯一のシステム サービスです。 

## <a name="vertical-scaling-considerations"></a>垂直方向のスケーリングに関する考慮事項

Azure Service Fabric で特定のノードの種類を[垂直方向にスケールする](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out)には、いくつかの手順が必要で、考慮すべきことがあります。 例: 
* クラスターは、スケーリングの前に正常になっている必要があります。 そうでないと、さらにクラスターを不安定にするだけになります。
* ステートフル サービスをホストするすべての Service Fabric クラスターの Nodetypes は、**持続性が Silver レベル以上**であることが必要です。

> [!NOTE]
> ステートフルな Service Fabric システム サービスをホストするプライマリ NodeType の持続性は、Silver レベル以上である必要があります。 Silver の持続性を有効にすると、アップグレード、ノードの追加や削除などのクラスター操作はより低速になります。これは、システムが、操作の速度よりもデータの安全性に向けて最適化を行うためです。

仮想マシン スケール セットを垂直方向にスケールすることは、破壊的な操作です。 代わりに、必要な SKU を使用して新しいスケール セットを追加することでクラスターを水平方向にスケールし、サービスを目的の SKU に移行して、安全な垂直方向のスケーリング操作を完了します。 仮想マシン スケール セットのリソース SKU を変更することは、それによってホストが再イメージ化され、ローカルで永続化されていた状態がすべて削除されるため、破壊的な操作です。

Service Fabric の [ノード プロパティと配置の制約](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints)は、アプリケーション サービスをホストする場所を決定するためにクラスターによって使用されます。 プライマリのノードの種類を垂直方向にスケールするときには、仮想マシン スケール セットの Service Fabric 拡張機能内にある `"nodeTypeRef"` に対して、同じプロパティ値を宣言します。 Resource Manager テンプレートの次のスニペットは、スケールする対象となる、新たにプロビジョニングしたスケール セットと同じ値を指定して宣言するプロパティを示しています。このスニペットは、クラスターの一時的なステートフルとしてのみサポートされます。

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> 同じ `nodeTypeRef` プロパティの値を使用する複数のスケール セットのあるクラスターが、垂直方向のスケーリング操作を正常に完了するために必要な時間より長く実行されたままにならないようにします。
> 運用環境での変更を試みる前に、必ずテスト環境で操作を検証してください。 既定では、Service Fabric クラスターのシステム サービスには、プライマリ nodetype のみが対象となる配置の制約があります。

ノードのプロパティと、宣言されている配置の制約を使用し、一度に VM インスタンス 1 つずつ、以下の手順を実行します。 これにより、他の場所に新しいレプリカを作成するときに、削除する VM インスタンスでシステム サービス (およびステートフル サービス) を正常にシャットダウンできます。
1. 削除しようとしているノードを無効にする意図 "RemoveNode" を指定して、PowerShell から `Disable-ServiceFabricNode` を実行します。 最も数が多いノードの種類を削除します。 たとえば、6 個のノードを含むクラスターがある場合は、仮想マシン インスタンス "MyNodeType_5" を削除します。
2. `Get-ServiceFabricNode` を実行し、ノードが無効に移行したことを確認します。 無効になっていない場合は、無効になるまで待ちます。 これはノードごとに数時間かかる場合があります。 ノードが無効に移行するまで作業を進めないでください。
3. そのノードの種類である VM の数を 1 つ減らします。 これで 最上位の VM インスタンスが削除されます。
4. 必要に応じて手順 1. ～ 3. を繰り返します。ただし、プライマリ ノードの種類のインスタンス数を、信頼性レベルで保証するよりも少ない数にスケールダウンしないでください。 推奨されるインスタンスの一覧については、「[Service Fabric クラスターの容量計画](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)」を参照してください。

## <a name="horizontal-scaling"></a>水平スケーリング

Service Fabric での水平スケーリングは、[手動で](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down)、または[プログラムで](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling)行うことができます。

> [!NOTE]
> 持続性が Silver または Gold であるノードの種類をスケールする場合、スケーリングは低速になります。

### <a name="scaling-out"></a>スケールアウト

特定の仮想マシン スケール セットのインスタンス数を増やすことで、Service Fabric クラスターをスケールアウトします。 AzureClient と、目的のスケール セットの ID を使用することでプログラムによってスケールアウトし、容量を増やすことができます。

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

手動でスケールアウトするには、目的の[仮想マシン スケール セット](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) リソースの SKU プロパティで容量を更新します。
```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>スケールイン

スケールインでは、スケールアウトよりも多くのことを考慮する必要があります。例: 
* Service Fabric システム サービスは、クラスター内のプライマリのノードの種類で実行されます。 信頼性レベルでの保証内容よりもインスタンス数が少なくならないように、シャットダウンしたり、そのノードの種類のインスタンス数をスケールダウンしたりしてはいけません。 
* ステートフル サービスについては、可用性を保ち、サービスの状態を維持するために、一定数のノードが常に稼働している必要があります。 少なくとも、パーティション/サービスのターゲット レプリカ セットと同数のノードが必要です。

手動でスケールインするには、次の手順に従います。

1. 削除しようとしているノードを無効にする意図 "RemoveNode" を指定して、PowerShell から `Disable-ServiceFabricNode` を実行します。 最も数が多いノードの種類を削除します。 たとえば、6 個のノードを含むクラスターがある場合は、仮想マシン インスタンス "MyNodeType_5" を削除します。
2. `Get-ServiceFabricNode` を実行し、ノードが無効に移行したことを確認します。 無効になっていない場合は、無効になるまで待ちます。 これはノードごとに数時間かかる場合があります。 ノードが無効に移行するまで作業を進めないでください。
3. そのノードの種類である VM の数を 1 つ減らします。 これで 最上位の VM インスタンスが削除されます。
4. 必要に応じて手順 1. ～ 3. を繰り返します。ただし、プライマリ ノードの種類のインスタンス数を、信頼性レベルで保証するよりも少ない数にスケールダウンしないでください。 推奨されるインスタンスの一覧については、「[Service Fabric クラスターの容量計画](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)」を参照してください。

手動でスケールインするには、目的の[仮想マシン スケール セット](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) リソースの SKU プロパティで容量を更新します。

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

1. 手順 1. ~ 3. を繰り返して、必要な容量をプロビジョニングします。 プライマリのノードの種類のインスタンス数は、信頼性レベルの保証を下回るまでスケールダウンしないでください。 信頼性レベルと、そのレベルで必要なインスタンス数の詳細については、「[Service Fabric クラスターの容量計画](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)」を参照してください。

プログラムでのスケーリングのためには、シャット ダウンのためにノードを準備する必要があります。 それには、最上位のインスタンス ノードである削除するノードを見つけて、それを非アクティブ化する必要があります。 例: 

```c#
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n =>
        {
            var instanceIdIndex = n.NodeName.LastIndexOf("_");
            var instanceIdString = n.NodeName.Substring(instanceIdIndex + 1);
            return int.Parse(instanceIdString);
        })
        .FirstOrDefault();
```

削除するノードを識別したら、上記のコードで使用したのと同じ `FabricClient` インスタンス (この場合は `client`) とノード インスタンス名 (この場合は `instanceIdString`) を使用して、それを非アクティブ化して削除します。

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shutdown
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement VMSS capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply();
```

> [!NOTE]
> クラスターをスケール ダウンすると、削除されたノード/VM のインスタンスが Service Fabric Explorer に異常な状態で表示されます。 この動作の説明については、「[Service Fabric Explorer で確認できる動作](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer)」をご覧ください。
> 
> 次のようにすることができます。
> * 適切なノード名で [Remove-ServiceFabricNodeState cmd](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) を呼び出します。
> * クラスターに [service fabric autoscale helper application](https://github.com/Azure/service-fabric-autoscale-helper/) をデプロイします。これによってスケール ダウンされたノードが Service Fabric Explorer から確実にクリアされます。

## <a name="reliability-levels"></a>信頼性レベル

[信頼性レベル](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)は Service Fabric クラスター リソースのプロパティなので、個々の NodeTypes に異なる構成を適用することはできません。 これが、クラスターのシステム サービスのレプリケーション係数を制御しており、クラスター リソース レベルでの設定となっています。 信頼性レベルによって、プライマリのノードの種類に必要な最小限のノード数が決まります。 信頼性レベルは、以下のプランから選ぶことができます。
* Platinum - ターゲットのレプリカ セット数が 7、シード ノード数 9 でシステム サービスを実行します。
* Gold - ターゲットのレプリカ セット数が 7、シード ノード数 7 でシステム サービスを実行します。
* Silver - ターゲットのレプリカ セット数が 5、シード ノード数 5 でシステム サービスを実行します。
* Bronze - ターゲットのレプリカ セット数が 3、シード ノード数 3 でシステム サービスを実行します。

推奨される最小限の信頼性レベルは Silver です。

信頼性レベルは、[Microsoft.ServiceFabric/clusters resource](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters) のプロパティ セクションで、次のように設定されています。

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>持続性レベル

> [!WARNING]
> Bronze の持続性で実行されているノード タイプには "_特権が与えられません_"。 つまり、ステートレス ワークロードに影響を与えるインフラストラクチャ ジョブは、停止または延期されません。これによりワークロードに影響を及ぼす場合があります。 Bronze の持続性は、ステートレス ワークロードを実行するノードの種類に対してのみ使用します。 運用環境のワークロードについては、状態の一貫性を確保するために Silver 以上を実行します。 [容量計画に関するドキュメント](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)のガイダンスに基づいて、適切な信頼性を選択してください。

持続性レベルは 2 つのリソースで設定する必要があります。 次の[仮想マシン スケール セット リソース](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)の拡張機能プロファイルと、

```json
"extensionProfile": {
    "extensions":          {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
        "properties": {
            "settings": {
                "durabilityLevel": "Bronze"
            }
        }
    }
}
```

[Microsoft.ServiceFabric/clusters リソース](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters)内の `nodeTypes` の下です 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>次の手順

* Windows Server を実行している VM またはコンピューター上にクラスターを作成する:[Windows Server 用の Service Fabric クラスターの作成](service-fabric-cluster-creation-for-windows-server.md)
* Linux を実行している VM またはコンピューター上にクラスターを作成する:[Linux クラスターの作成](service-fabric-cluster-creation-via-portal.md)
* [Service Fabric のサポート オプション](service-fabric-support.md)について学びます。

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
