---
title: Azure Service Fabric でのノード タイプの削除 | Microsoft Docs
description: Azure で実行されている Service Fabric クラスターからノード タイプを削除する方法について説明します。
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/14/2019
ms.author: aljo
ms.openlocfilehash: 779051135a994574cb2bed7bfc4879270ec1d8fa
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443027"
---
# <a name="remove-a-service-fabric-node-type"></a>Service Fabric ノード タイプを削除する
この記事では、クラスターから既存のノード タイプを削除することで、Azure Service Fabric クラスターをスケーリングする方法について説明します。 Service Fabric クラスターは、ネットワークで接続された一連の仮想マシンまたは物理マシンで、マイクロサービスがデプロイおよび管理されます。 クラスターに属しているコンピューターまたは VM を "ノード" と呼びます。 仮想マシン スケール セットは、セットとして仮想マシンのコレクションをデプロイおよび管理するために使用する Azure コンピューティング リソースです。 Azure クラスターで定義されているすべてのノードの種類は、[異なるスケール セットとしてセットアップされます](service-fabric-cluster-nodetypes.md)。 その後は、ノードの種類ごとに個別に管理できます。 Service Fabric クラスターを作成した後は、ノード タイプ (仮想マシン スケール セット) とそのノードすべてを削除することで、クラスターを水平方向にスケーリングできます。  クラスターは、クラスターでワークロードを実行中であっても、いつでもスケーリングできます。  クラスターをスケーリングすると、アプリケーションも自動的にスケーリングされます。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Remove-AzServiceFabricNodeType](https://docs.microsoft.com/powershell/module/az.servicefabric/remove-azservicefabricnodetype) を使用して Service Fabric ノード タイプを削除します。

Remove-AzServiceFabricNodeType が呼び出されると、次の 3 つの操作が行われます。
1.  ノード タイプの背後にある仮想マシン スケール セットが削除されます。
2.  ノード タイプがクラスターから削除されます。
3.  そのノード タイプに含まれる各ノードについて、そのノード全体の状態がシステムから削除されます。 そのノード上にサービスが存在する場合、それらのサービスは最初に別ノードに移動されます。 クラスター マネージャーで、レプリカ/サービス用のノードを見つけられない場合、操作は遅延またはブロックされます。

> [!WARNING]
> 運用環境のクラスターからノード タイプを削除するために Remove-AzServiceFabricNodeType を使用することは、頻繁に使用する方法としては推奨されません。 ノード タイプの背後にある仮想マシン スケール セット リソースが削除されるため、危険なコマンドです。 

## <a name="durability-characteristics"></a>耐久性の特性
Remove-AzServiceFabricNodeType の使用時には、速度よりも安全性が優先されます。 次に示す理由から、ノード タイプはシルバーまたはゴールドの[耐久性レベル](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)にする必要があります。
- ブロンズでは、状態情報の保存に関する保証が一切提供されしません。
- シルバーとゴールドの耐久性では、スケール セットに対するすべての変更がトラップされます。
- ゴールドでは、スケール セットの下にある Azure の更新に対する制御も与えられます。

Service Fabric では、データが失われないように、基になっている変更や更新が "調整" されます。 ただし、ブロンズの耐久性を持つノードを削除すると、状態情報が失われる可能性があります。 プライマリ ノード タイプを削除しようとしていて、アプリケーションがステートレスである場合は、ブロンズで問題ありません。 運用環境でステートフル ワークロードを実行する場合は、最小構成をシルバーにする必要があります。 同様に、運用環境であれば、プライマリ ノード タイプは常にシルバーまたはゴールドにする必要があります。

### <a name="more-about-bronze-durability"></a>ブロンズの耐久性に関する詳細

ブロンズであるノード タイプを削除すると、そのノード タイプに含まれるすべてのノードが直ちに停止します。 Service Fabric では、ブロンズ ノードのスケール セットの更新は一切トラップされないため、すべての VM が直ちに停止します。 それらのノードに何かステートフルなものがあった場合、データは失われます。 ステートレスであった場合でも、Service Fabric 内にあるノードはすべてリングに参加しているため、近隣全体が失われる可能性があり、そのために、クラスター自体が不安定になることがあります。

## <a name="recommended-node-type-removal-process"></a>推奨されるノード タイプの削除プロセス

ノード タイプを削除するには、[Remove-AzServiceFabricNodeType](/powershell/module/az.servicefabric/remove-azservicefabricnodetype) コマンドレットを実行します。  コマンドレットが完了するまでに、一定の時間がかかります。  すべての VM が削除されると ("ダウン" と示されます)、fabric:/System/InfrastructureService/[ノード タイプ名] でエラー状態が示されます。

```powershell
$groupname = "mynodetype"
$nodetype = "nt2vm"
$clustername = "mytestcluster"

Remove-AzServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

Connect-ServiceFabricCluster -ConnectionEndpoint mytestcluster.eastus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <thumbprint> `
          -FindType FindByThumbprint -FindValue <thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

その後、クラスター リソースを更新してノードの種類を削除できます。 ARM テンプレート デプロイを使用するか、[Azure Resource Manager](https://resources.azure.com) によってクラスター リソースを編集することができます。 これにより、クラスターのアップグレードが開始され、エラー状態の fabric:/System/InfrastructureService/[ノード タイプ名] サービスが削除されます。

Service Fabric Explorer でノードは "ダウン" と表示されます。 削除する各ノードで [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) を実行します。


```powershell
$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="next-steps"></a>次の手順
- クラスターの[耐久性の特性](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)についてさらに学習します。
- [ノード タイプと Virtual Machine Scale Sets](service-fabric-cluster-nodetypes.md) についてさらに学習します。
- [Service Fabric クラスターのスケーリング](service-fabric-cluster-scaling.md)についてさらに学習します。
