---
title: Azure Service Fabric でのノード タイプの削除 | Microsoft Docs
description: Azure で実行されている Service Fabric クラスターからノード タイプを削除する方法について説明します。
author: inputoutputcode
manager: sridmad
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: chrpap
ms.openlocfilehash: ede999bee9ce1a4a9dd10652a2c52a840d5b24be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88163579"
---
# <a name="how-to-remove-a-service-fabric-node-type"></a>Service Fabric ノード タイプを削除する方法
この記事では、クラスターから既存のノード タイプを削除することで、Azure Service Fabric クラスターをスケーリングする方法について説明します。 Service Fabric クラスターは、ネットワークで接続された一連の仮想マシンまたは物理マシンで、マイクロサービスがデプロイおよび管理されます。 クラスターに属しているコンピューターまたは VM を "ノード" と呼びます。 仮想マシン スケール セットは、セットとして仮想マシンのコレクションをデプロイおよび管理するために使用する Azure コンピューティング リソースです。 Azure クラスターで定義されているすべてのノードの種類は、[異なるスケール セットとしてセットアップされます](service-fabric-cluster-nodetypes.md)。 その後は、ノードの種類ごとに個別に管理できます。 Service Fabric クラスターを作成した後は、ノード タイプ (仮想マシン スケール セット) とそのノードすべてを削除することで、クラスターを水平方向にスケーリングできます。  クラスターは、クラスターでワークロードを実行中であっても、いつでもスケーリングできます。  クラスターをスケーリングすると、アプリケーションも自動的にスケーリングされます。

> [!WARNING]
> 運用環境のクラスターからノード タイプを削除するためにこの方法を使用することは、頻繁に使用する場合は推奨されません。 ノード タイプの背後にある仮想マシン スケール セット リソースが削除されるため、危険なコマンドです。 

## <a name="durability-characteristics"></a>耐久性の特性
Remove-AzServiceFabricNodeType の使用時には、速度よりも安全性が優先されます。 次に示す理由から、ノード タイプはシルバーまたはゴールドの[耐久性レベル](./service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)にする必要があります。
- ブロンズでは、状態情報の保存に関する保証が一切提供されしません。
- シルバーとゴールドの耐久性では、スケール セットに対するすべての変更がトラップされます。
- ゴールドでは、スケール セットの下にある Azure の更新に対する制御も与えられます。

Service Fabric では、データが失われないように、基になっている変更や更新が "調整" されます。 ただし、ブロンズの持続性を持つノード タイプを削除すると、状態情報が失われる可能性があります。 プライマリ ノード タイプを削除しようとしていて、アプリケーションがステートレスである場合は、ブロンズで問題ありません。 運用環境でステートフル ワークロードを実行する場合は、最小構成をシルバーにする必要があります。 同様に、運用環境であれば、プライマリ ノード タイプは常にシルバーまたはゴールドにする必要があります。

### <a name="more-about-bronze-durability"></a>ブロンズの耐久性に関する詳細

ブロンズであるノード タイプを削除すると、そのノード タイプに含まれるすべてのノードが直ちに停止します。 Service Fabric では、ブロンズ ノードのスケール セットの更新は一切トラップされないため、すべての VM が直ちに停止します。 それらのノードに何かステートフルなものがあった場合、データは失われます。 ステートレスであった場合でも、Service Fabric 内にあるノードはすべてリングに参加しているため、近隣全体が失われる可能性があり、そのために、クラスター自体が不安定になることがあります。

## <a name="remove-a-node-type"></a>ノード タイプを削除する

1. 処理を開始する前に、次の前提条件に対処してください。

    - クラスターが正常である。
    - ノード タイプが削除された後も依然として十分な容量がある。例: 必要なレプリカ数を配置するためのノードの数。

2. ノード タイプを使用するための配置制約があるすべてのサービスを、そのノード タイプから移動します。

    - ノード タイプを参照しなくなるようにアプリケーション/サービス マニフェストを変更します。
    - 変更内容をデプロイします。

    その後、次のことを検証します。
    - 上記で変更したすべてのサービスが、そのノード タイプに属しているノードで実行されなくなっている。
    - すべてのサービスが正常である。

3. node-type を非プライマリとしてマークを解除します (非プライマリ ノード タイプの場合はスキップします)

    - デプロイに使用する Azure Resource Manager テンプレートを見つけます。
    - [Service Fabric] セクション内で、ノード タイプに関連するセクションを探します。
    - IsPrimary プロパティを false に変更します。 ** このタスクにおいて、ノード タイプに関連するセクションを削除しないでください。
    - 変更した Azure Resource Manager テンプレートをデプロイします。 ** クラスター構成によっては、この手順にしばらく時間がかかることがあります。
    
    その後、次のことを検証します。
    - ポータルの [Service Fabric] セクションで、クラスターの準備ができていることが示されている。
    - クラスターが正常である。
    - ノード タイプに属しているどのノードもシード ノードとしてマークされていない。

4. ノード タイプで各ノードを無効にします。

    PowerShell を使用してクラスターに接続し、次の手順を実行します。
    
    ```powershell
    $nodeType = "" # specify the name of node type
    $nodes = Get-ServiceFabricNode
    
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
      }
    }
    ```

    - ブロンズの持続性の場合は、すべてのノードが無効状態になるまで待ちます
    - シルバーおよびゴールドの持続性の場合は、一部のノードが無効になり、残りは無効化中の状態になります。 無効化中の状態のノードの詳細タブを確認し、インフラストラクチャ サービス パーティションのクォーラムを確保しているときにそれらがすべて停止している場合は、続行しても安全です。

5. ノード タイプのデータを停止します。

    PowerShell を使用してクラスターに接続し、次の手順を実行します。
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
      }
    }
    ```
    
    ノード タイプのすべてのノードが「ダウン」とマークされされるまで待ちます。

6. 元の仮想マシン スケール セットでノードの割り当てを解除する
    
    スケール セットがデプロイされた Azure サブスクリプションにログインし、仮想マシン スケール セットを削除します。 

    ```powershell
    $scaleSetName="myscaleset"
    $scaleSetResourceType="Microsoft.Compute/virtualMachineScaleSets"
    
    Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
    ```

    
7. ノード タイプのデータを削除します。

    PowerShell を使用してクラスターに接続し、次の手順を実行します。
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
      }
    }
    ```

    すべてのノードがクラスターから削除されるまで待ちます。 ノードは SFX に表示されません。

8. [Service Fabric] セクションからノード タイプを削除します。

    - デプロイに使用する Azure Resource Manager テンプレートを見つけます。
    - [Service Fabric] セクション内で、ノード タイプに関連するセクションを探します。
    - そのノード タイプに対応するセクションを削除します。
    - シルバー以上の持続性クラスターの場合のみ、テンプレートのクラスター リソースを更新し、次に示すようにクラスター リソース `properties` の下に `applicationDeltaHealthPolicies` を追加して、fabric:/System のアプリケーションの正常性を無視するように正常性ポリシーを構成します。 以下のポリシーでは既存のエラーが無視されますが、新しい正常性エラーは許可されません。 
 
 
     ```json
    "upgradeDescription":  
    { 
      "forceRestart": false, 
      "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
      "healthCheckWaitDuration": "00:05:00", 
      "healthCheckStableDuration": "00:05:00", 
      "healthCheckRetryTimeout": "00:45:00", 
      "upgradeTimeout": "12:00:00", 
      "upgradeDomainTimeout": "02:00:00", 
      "healthPolicy": { 
        "maxPercentUnhealthyNodes": 100, 
        "maxPercentUnhealthyApplications": 100 
      }, 
      "deltaHealthPolicy":  
      { 
        "maxPercentDeltaUnhealthyNodes": 0, 
        "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
        "maxPercentDeltaUnhealthyApplications": 0, 
        "applicationDeltaHealthPolicies":  
        { 
            "fabric:/System":  
            { 
                "defaultServiceTypeDeltaHealthPolicy":  
                { 
                        "maxPercentDeltaUnhealthyServices": 0 
                } 
            } 
        } 
      } 
    },
    ```

    - 変更した Azure Resource Manager テンプレートをデプロイします。 ** この手順にはしばらく時間がかかります (通常は最大 2 時間)。 このアップグレードでは、InfrastructureService に対する設定が変更されるため、ノードの再起動が必要になります。 この場合、`forceRestart` は無視されます。 
    パラメーター `upgradeReplicaSetCheckTimeout` には、パーティションが (まだ安全な状態でない場合に) 安全な状態になるまで Service Fabric が待機する最大時間を指定します。 ノード上のすべてのパーティションが安全性チェックに合格すると、Service Fabric はそのノードでアップグレードを進めます。
    パラメーター `upgradeTimeout` の値は 6 時間に短縮できますが、最大の安全確保のために 12 時間とする必要があります。

    その後、次のことを検証します。
    - ポータルで Service Fabric リソースが準備完了と表示される。

9. ARM テンプレートからノード タイプに関連するリソースへのすべての参照を削除します。

    - デプロイに使用する Azure Resource Manager テンプレートを見つけます。
    - テンプレートから、ノード タイプに関連する仮想マシン スケール セットとその他のリソースを削除します。
    - 変更内容をデプロイします。

    その後、以下を実行します。
    - デプロイが完了するまで待ちます。
    
10. 使用されなくなったノード タイプに関連するリソースを削除します。 サンプルのロード バランサーとパブリック IP。 

    - これらのリソースを削除するには、手順 6 と同じ PowerShell コマンドを使用し、特定のリソースの種類と API バージョンを指定します。 

> [!Note]
> 同じロード バランサーと IP がノード タイプ間で再使用される場合、この手順は任意です。

## <a name="next-steps"></a>次のステップ
- クラスターの[耐久性の特性](./service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)についてさらに学習します。
- [ノード タイプと Virtual Machine Scale Sets](service-fabric-cluster-nodetypes.md) についてさらに学習します。
- [Service Fabric クラスターのスケーリング](service-fabric-cluster-scaling.md)についてさらに学習します。
