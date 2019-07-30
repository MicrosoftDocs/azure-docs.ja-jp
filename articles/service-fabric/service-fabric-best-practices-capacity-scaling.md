---
title: Azure Service Fabric の容量計画とスケーリング | Microsoft Docs
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
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: d4daa7ae9c7e58c1949dfbe4427a154c389100d4
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348378"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>Azure Service Fabric の容量計画とスケーリング

Azure Service Fabric クラスターの作成前、またはクラスターをホストするコンピューティング リソースをスケールする前に、容量についての計画を立てることが重要です。 容量の計画に関する詳細については、[Service Fabric クラスターの容量計画](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)に関するページを参照してください。 クラスター スケーラビリティのベスト プラクティスへの詳細な手引きについては、[Service Fabric のスケーラビリティに関する考慮事項](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations)に関するページを参照してください。

ノードの種類とクラスターの特性を考慮することに加えて、運用環境でのスケーリング操作が完了するまでに 1 時間以上かかることを考慮する必要があります。 この考慮事項は、追加する VM の数に関係なく当てはまります。

## <a name="autoscaling"></a>自動スケール
スケーリング操作は、Azure Resource Manager テンプレートを介して実行する必要があります。これは、[リソース構成をコードとして]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)扱うことがベスト プラクティスであるためです。 

仮想マシン スケール セットを介した自動スケーリングを使用すると、バージョン管理される Resource Manager テンプレートで、仮想マシン スケール セットのインスタンス数の定義が不正確になります。 不正確な定義により、今後のデプロイで意図しないスケーリング操作が発生するリスクが高まります。 一般的に、自動スケーリングは次の場合に使用してください。

* 適切な容量が宣言されている Resource Manager テンプレートをデプロイしても、実際のユース ケースがサポートされない。
     
   手動スケーリングに加えて、[Azure リソース グループのデプロイ プロジェクトを使用することで、Azure DevOps Services で継続的インテグレーションと配信パイプライン](https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts)を構成できます。 このパイプラインは、通常、[Azure Monitor REST API](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough) からクエリされる仮想マシンのパフォーマンス メトリックを使用するロジック アプリによってトリガーされます。 パイプラインでは、Resource Manager テンプレート用に最適化しながら、任意の必要なメトリックに基づいた効果的な自動スケーリングが行われます。
* 一度に 1 つの仮想マシン スケール セット ノードのみを水平方向にスケールする必要がある。
   
   一度に 3 つ以上のノードをスケールアウトするには、[仮想マシン スケール セットを追加することで、Service Fabric クラスターをスケールアウトする](virtual-machine-scale-set-scale-node-type-scale-out.md)必要があります。 仮想マシン スケール セットを水平方向に、一度に 1 ノードずつスケールインおよびスケールアウトするのが最も安全です。
* Service Fabric クラスターの信頼性が Silver 以上で、自動スケーリング ルールを構成する任意のスケールの持続性が Silver 以上である。
  
   自動スケーリング ルールの最小容量は、仮想マシン インスタンス 5 つ以上である必要があります。 また、ノードの種類がプライマリの場合、信頼性レベルが "最小" 以上である必要があります。

> [!NOTE]
> Service Fabric のステートフル サービス fabric:/System/InfastructureService/<NODE_TYPE_NAME> は、持続性が Silver 以上であるすべてのノードの種類で実行されます。 これは、クラスター ノードの種類がどれであっても Azure での実行がサポートされる唯一のシステム サービスです。

## <a name="vertical-scaling-considerations"></a>垂直方向のスケーリングに関する考慮事項

Azure Service Fabric で特定のノードの種類を[垂直方向にスケールする](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out)には、いくつかの手順が必要であり、考慮すべきことがあります。 例:

* クラスターは、スケーリングの前に正常になっている必要があります。 そうでない場合、クラスターがさらに不安定になってしまいます。
* ステートフル サービスをホストするすべての Service Fabric クラスター ノードの種類は、持続性が Silver レベル以上であることが必要です。

> [!NOTE]
> ステートフルな Service Fabric システム サービスをホストするプライマリ ノードの種類の持続性は、Silver レベル以上である必要があります。 Silver の持続性を有効にすると、アップグレード、ノードの追加や削除などのクラスター操作はより低速になります。これは、システムが、操作の速度よりもデータの安全性に向けて最適化を行うためです。

仮想マシン スケール セットを垂直方向にスケールすることは、破壊的な操作です。 代わりに、必要な SKU を使用して新しいスケール セットを追加することでクラスターを水平方向にスケールします。 その後、サービスを目的の SKU に移行して、安全な垂直方向のスケーリング操作を完了します。 仮想マシン スケール セットのリソース SKU を変更することは、それによってホストが再イメージ化され、ローカルで永続化されていた状態がすべて削除されるため、破壊的な操作です。

アプリケーションのサービスをホストする場所を決定するために、クラスターで Service Fabric の[ノード プロパティと配置の制約](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints)が使用されます。 プライマリ ノードの種類を垂直方向にスケーリングする場合、`"nodeTypeRef"` に対して同一のプロパティ値を宣言します。 仮想マシン スケール セットの Service Fabric 拡張機能でこれらの値を見つけることができます。 

Resource Manager テンプレートの次のスニペットに、宣言するプロパティが示されています。 これは、スケールする対象となる、新たにプロビジョニングされたスケール セットと同じ値を持ち、クラスターの一時的なステートフル サービスとしてのみサポートされます。

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> 同じ `nodeTypeRef` プロパティの値を使用する複数のスケール セットのあるクラスターが、垂直方向のスケーリング操作を正常に完了するために必要な時間より長く実行されたままにならないようにします。
>
> 運用環境に対する変更を試行する前に、必ずテスト環境で操作を検証してください。 既定では、Service Fabric クラスターのシステム サービスには、ターゲットのプライマリ ノードの種類に対してのみ配置の制約があります。

ノードのプロパティと、宣言されている配置の制約を使用し、一度に VM インスタンス 1 つずつ、以下の手順を実行します。 これにより、他の場所に新しいレプリカを作成するときに、削除する VM インスタンスでシステム サービス (およびステートフル サービス) を正常にシャットダウンできます。

1. 削除しようとしているノードを無効にする意図 `RemoveNode` を指定して、PowerShell から `Disable-ServiceFabricNode` を実行します。 最も数が多いノードの種類を削除します。 たとえば、6 個のノードを含むクラスターがある場合は、仮想マシン インスタンス "MyNodeType_5" を削除します。
2. `Get-ServiceFabricNode` を実行し、ノードが無効に移行したことを確認します。 無効になっていない場合は、無効になるまで待ちます。 これはノードごとに数時間かかる場合があります。 ノードが無効に移行するまで作業を進めないでください。
3. そのノードの種類で VM の数を 1 つ減らします。 これで 最上位の VM インスタンスが削除されます。
4. 必要に応じて手順 1. ～ 3. を繰り返します。ただし、プライマリ ノードの種類のインスタンス数を、信頼性レベルで保証するよりも少ない数にスケールダウンしないでください。 推奨されるインスタンスの一覧については、「[Service Fabric クラスターの容量計画](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)」を参照してください。
5. すべての VM が削除されると ("ダウン" と示されます)、fabric:/System/InfrastructureService/[ノード名] でエラー状態が示されます。 その後、クラスター リソースを更新してノードの種類を削除できます。 ARM テンプレート デプロイを使用するか、[Azure Resource Manager](https://resources.azure.com) によってクラスター リソースを編集することができます。 これにより、クラスターのアップグレードが開始され、エラー状態の fabric:/System/InfrastructureService/[ノードの種類] サービスが削除されます。
 6. その後、必要に応じて VMScaleSet を削除できますが、Service Fabric Explorer ビューではノードが "ダウン" と表示されます。 最後の手順は、`Remove-ServiceFabricNodeState` コマンドを使用してこれらをクリーンアップすることです。

## <a name="horizontal-scaling"></a>水平スケーリング

[手動](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down)または[プログラム](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling)で水平スケーリングを実行できます。

> [!NOTE]
> 持続性が Silver または Gold であるノードの種類をスケールする場合、スケーリングは低速になります。

### <a name="scaling-out"></a>スケールアウト

特定の仮想マシン スケール セットのインスタンス数を増やすことで、Service Fabric クラスターをスケールアウトします。 `AzureClient` と目的のスケール セットの ID を使用することでプログラムでスケールアウトし、容量を増やすことができます。

```csharp
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

* Service Fabric システム サービスは、クラスター内のプライマリ ノードの種類で実行されます。 信頼性レベルでの保証内容よりもインスタンス数が少なくならないように、シャットダウンしたり、そのノードの種類のインスタンス数をスケールダウンしたりしてはいけません。 
* ステートフル サービスについては、可用性を保ち、サービスの状態を維持するために、一定数のノードが常に稼働している必要があります。 少なくとも、パーティションまたはサービスのターゲット レプリカ セットと同数のノードが必要です。

手動でスケールインするには、次の手順に従います。

1. 削除しようとしているノードを無効にする意図 `RemoveNode` を指定して、PowerShell から `Disable-ServiceFabricNode` を実行します。 最も数が多いノードの種類を削除します。 たとえば、6 個のノードを含むクラスターがある場合は、仮想マシン インスタンス "MyNodeType_5" を削除します。
2. `Get-ServiceFabricNode` を実行し、ノードが無効に移行したことを確認します。 無効になっていない場合は、無効になるまで待ちます。 これはノードごとに数時間かかる場合があります。 ノードが無効に移行するまで作業を進めないでください。
3. そのノードの種類で VM の数を 1 つ減らします。 これで 最上位の VM インスタンスが削除されます。
4. 必要に応じて、手順 1 から 3 を繰り返して、必要な容量をプロビジョニングします。 プライマリのノードの種類のインスタンス数は、信頼性レベルの保証を下回るまでスケールダウンしないでください。 推奨されるインスタンスの一覧については、「[Service Fabric クラスターの容量計画](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)」を参照してください。

手動でスケールインするには、目的の[仮想マシン スケール セット](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) リソースの SKU プロパティで容量を更新します。

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

プログラムでのスケーリングのためには、シャット ダウンのためにノードを準備する必要があります。 削除するノード (最上位のインスタンス ノード) を見つけます。 例:

```csharp
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

上記のコードで使用したのと同じ `FabricClient` インスタンス (この場合は `client`) とノード インスタンス (この場合は `instanceIdString`) を使用して、ノードを非アクティブ化し、削除します。

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shut down
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement virtual machine scale set capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply();
```

> [!NOTE]
> クラスターをスケールダウンすると、削除されたノード/VM のインスタンスが Service Fabric Explorer に異常状態で表示されます。 この動作の説明については、「[Service Fabric Explorer で確認できる動作](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer)」をご覧ください。 次のようにすることができます。
> * 適切なノード名で [Remove-ServiceFabricNodeState コマンド](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps)を呼び出します。
> * クラスターに [Service Fabric 自動スケーリング ヘルパー アプリケーション](https://github.com/Azure/service-fabric-autoscale-helper/)をデプロイします。 このアプリケーションにより、スケールダウンされたノードが Service Fabric Explorer から確実にクリアされます。

## <a name="reliability-levels"></a>信頼性レベル

[信頼性レベル](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)は、Service Fabric クラスター リソースのプロパティです。 ノードの種類ごとに異なる構成はできません。 これが、クラスターのシステム サービスのレプリケーション係数を制御しており、クラスター リソース レベルでの設定となっています。 

信頼性レベルによって、プライマリのノードの種類に必要な最小限のノード数が決まります。 信頼性レベルは、以下のプランから選ぶことができます。

* Platinum:ターゲットのレプリカ セット数が 7、シード ノード数が 9 でシステム サービスを実行します。
* Gold:ターゲットのレプリカ セット数が 7、シード ノード数が 7 でシステム サービスを実行します。
* Silver:ターゲットのレプリカ セット数が 5、シード ノード数が 5 でシステム サービスを実行します。
* Bronze:ターゲットのレプリカ セット数が 3、シード ノード数が 3 でシステム サービスを実行します。

推奨される最小限の信頼性レベルは Silver です。

信頼性レベルは、[Microsoft.ServiceFabric/clusters resource](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters) のプロパティ セクションで、次のように設定されています。

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>持続性レベル

> [!WARNING]
> Bronze の持続性で実行されているノード タイプには "_特権が与えられません_"。 ステートレス ワークロードに影響するインフラストラクチャ ジョブは停止または延期されません。これはワークロードに影響する可能性があります。 
>
> Bronze の持続性は、ステートレス ワークロードを実行するノードの種類に対してのみ使用します。 運用環境のワークロードについては、状態の一貫性を確保するために Silver 以上を実行します。 [容量計画に関するドキュメント](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)のガイダンスに基づいて、適切な信頼性を選択してください。

持続性レベルは 2 つのリソースで設定する必要があります。 1 つは、[仮想マシン スケール セット リソース](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)の拡張機能プロファイルです。

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

もう 1 つのリソースは、[Microsoft.ServiceFabric/clusters リソース](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters)の `nodeTypes` の下です。 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>次の手順

* Windows Server を実行している VM またはコンピューター上にクラスターを作成する:[Windows Server 用の Service Fabric クラスターの作成](service-fabric-cluster-creation-for-windows-server.md)。
* Linux を実行している VM またはコンピューター上にクラスターを作成する:[Linux クラスターの作成](service-fabric-cluster-creation-via-portal.md)。
* [Service Fabric のサポート オプション](service-fabric-support.md)について学びます。

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
