---
title: "システム正常性レポートを使用してトラブルシューティングを行う |Microsoft Docs"
description: "Azure Service Fabric のコンポーネントよって送信される正常性レポートと、クラスターやアプリケーションの問題をトラブルシューティングするための使い方について説明します。"
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: 
ms.assetid: 52574ea7-eb37-47e0-a20a-101539177625
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: oanapl
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: fb3b2ecd73acb0ea65af477bc6a5b887c117574c
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---
# <a name="use-system-health-reports-to-troubleshoot"></a>システム正常性レポートを使用したトラブルシューティング
Azure Service Fabric コンポーネントは、追加の設定なしでクラスター内のすべてのエンティティについてレポートします。 [正常性ストア](service-fabric-health-introduction.md#health-store) は、システム レポートに基づいてエンティティを作成および削除します。 さらに、エンティティの相互作用をキャプチャする階層で、それらを編成します。

> [!NOTE]
> 正常性に関する概念については、「 [Service Fabric の正常性モニタリングの概要](service-fabric-health-introduction.md)」を参照してください。
> 
> 

システム正常性レポートは、クラスターとアプリケーションの動作状況を可視化し、正常性の問題を警告します。 システム正常性レポートは、アプリケーションとサービスを対象に、エンティティが実装されて正しく動作していることを Service Fabric の観点から確認します。 レポートは、サービスのビジネス ロジックの正常性モニタリングやハングしたプロセスの検出を提供するものではありません。 ユーザー サービスでロジックに固有の情報を追加して正常性データを強化できます。

> [!NOTE]
> ウォッチドッグ正常性レポートは、システム コンポーネントでエンティティが作成された *後* にのみ表示できます。 エンティティが削除されると、正常性ストアは関連付けられているすべての正常性レポートを自動的に削除します。 エンティティのステートフルな永続化インスタンスが新しく作成される (たとえば、新しいサービス レプリカのインスタンスが作成される) 場合も同じことが当てはまります。 古いインスタンスに関連付けられているすべてのレポートが削除され、ストアからクリーンアップされます。
> 
> 

システム コンポーネント レポートはソース別に識別され、"**System.**" プレフィックスで 始まります。 ウォッチドッグのソースに同じプレフィックスを使用することはできません (無効なパラメーターを持つレポートが拒否されるため)。
いくつかのシステム レポートを確認し、何がレポートのトリガーになっているか、レポートに表示された問題を修正する方法を理解しましょう。

> [!NOTE]
> Service Fabric では、指定した条件に関するレポートが継続的に追加され、クラスターおよびアプリケーションの状況をより詳細に把握できます。 既存のレポートに詳細情報を追加して改善し、迅速に問題を解決することもできます。
> 
> 

## <a name="cluster-system-health-reports"></a>クラスター システム正常性レポート
クラスターの正常性エンティティが、正常性ストアに自動的に作成されます。 すべてが正常に動作している場合、システム レポートは作成されません。

### <a name="neighborhood-loss"></a>ネットワーク コンピューターの消失
**System.Federation** は、ネットワーク コンピューターの消失を検出するとエラーを報告します。 レポートのデータは個々のノードから収集され、ノード ID がプロパティ名に含まれます。 Service Fabric リング全体でネットワーク コンピューターの消失が 1 件あった場合、通常は 2 つのイベントを想定できます (問題の両側が報告されます)。 さらに多くのネットワーク コンピューターが消失している場合、イベントの数はもっと多くなります。

レポートは、グローバル リース タイムアウトを Time to Live として指定します。 レポートは、条件が有効な限り、TTL の半分の期間ごとに再送信されます。 イベントは、期限切れになると自動的に削除されます。 期限切れ時に削除する動作により、レポート ノードが停止した場合でも、レポートが正常性ストアから適切にクリーンアップされます。

* **SourceId**: System.Federation
* **Property**: **Neighborhood** で始まり、ノードの情報が含まれます
* **次のステップ**: ネットワーク コンピューターが消失した原因を調査します (たとえば、クラスター ノード間の通信をチェックします)。

## <a name="node-system-health-reports"></a>ノード システム正常性レポート
**System.FM**は Failover Manager サービスを表し、クラスター ノードに関する情報を管理する権限です。 どのノードにも、ノードの状態を示す System.FM からのレポートが 1 つあるはずです。 ノードの状態が削除されると、ノード エンティティは削除されます ( [RemoveNodeStateAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.clustermanagementclient.removenodestateasync)を参照)。

### <a name="node-updown"></a>ノードを上/下に移動
System.FM は、ノードがリングに参加する (稼動している) と、OK と報告します。 ノードがリングから外れる (アップグレードのため、または単に障害が発生しているため停止している) と、エラーを報告します。 正常性ストアによって構築された正常性の階層は、デプロイ済みエンティティに対して、System.FM ノード レポートに関連したアクションを実行します。 その階層では、ノードは、デプロイ済みのすべてのエンティティの仮想的な親ノードと見なされます。 そのノードにデプロイされたエンティティは、ノードが System.FM によって起動されたものとしてレポートされた場合、エンティティに関連付けられているインスタンスと同じインスタントと共に、クエリを通じて公開されます。 System.FM によってノードの停止または再起動 (新規インスタンス) が報告されると、正常性ストアは、停止したノードまたはノードの以前のインスタンスのみに存在している可能性のあるデプロイ済みエンティティを自動的にクリーンアップします。

* **SourceId**: System.FM
* **プロパティ**: State
* **次のステップ**: ノードがアップグレードのために停止している場合、アップグレード後に復帰する必要があります。 この例では、正常性状態が OK に切り替わる必要があります。 ノードが復帰しない場合、またはエラーが発生した場合は、さらに問題を調査する必要があります。

ノードの稼動を表す正常性状態 OK の System.FM イベントの例を次に示します。

```powershell
PS C:\> Get-ServiceFabricNodeHealth  _Node_0

NodeName              : _Node_0
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 8
                        SentAt                : 7/14/2017 4:54:51 PM
                        ReceivedAt            : 7/14/2017 4:55:14 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```


### <a name="certificate-expiration"></a>証明書の有効期限
**System.FabricNode** は、ノードで使用されている証明書の期限が近づくと警告を報告します。 ノードごとに、**Certificate_cluster**、**Certificate_server**、**Certificate_default_client** という 3 つの証明書があります。 有効期限が 2 週間以上先の場合は、レポートの正常性状態は OK になります。 有効期限が 2 週間以内の場合は、レポートの種類は警告になります。 これらのイベントの TTL は無制限で、イベントはノードがクラスターから切り離されると削除されます。

* **SourceId**: System.FabricNode
* **Property**: **Certificate** で始まり、証明書の種類に関する詳細が含まれます
* **次のステップ**: 有効期限が近い場合は、証明書を更新します。

### <a name="load-capacity-violation"></a>ロード容量違反
Service Fabric Load Balancer は、ノード容量違反を検出すると警告を報告します。

* **SourceId**: System.PLB
* **Property**: **Capacity** で始まります
* **次のステップ**: 提供されたメトリックを確認し、ノードの現在の容量を表示します。

## <a name="application-system-health-reports"></a>アプリケーション システム正常性レポート
**System.CM**は Cluster Manager サービスを表し、アプリケーションに関する情報を管理する権限です。

### <a name="state"></a>状態
System.CM は、アプリケーションが作成または更新されたときに OK を報告します。 アプリケーションが削除されると、ストアからアプリケーションを削除できるように、正常性ストアに通知します。

* **SourceId**: System.CM
* **プロパティ**: State
* **次のステップ**: アプリケーションが作成または更新されたら、Cluster Manager 正常性レポートを含める必要があります。 作成されなかった場合は、クエリを発行してアプリケーションの状態を確認します (例: Powershell コマンドレット **Get-ServiceFabricApplication -ApplicationName *applicationName***)。

**fabric:/WordCount** アプリケーションの State イベントの例を次に示します。

```powershell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/14/2017 4:55:10 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="service-system-health-reports"></a>サービス システム正常性レポート
**System.FM**は Failover Manager サービスを表し、サービスに関する情報を管理する権限です。

### <a name="state"></a>状態
System.FM は、サービスが作成されると OK を報告します。 サービスが削除されたら、正常性ストアからエンティティを削除します。

* **SourceId**: System.FM
* **プロパティ**: State

**fabric:/WordCount/WordCountWebService** サービスの State イベントの例を次に示します。

```powershell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountWebService -ExcludeHealthStatistics


ServiceName           : fabric:/WordCount/WordCountWebService
AggregatedHealthState : Ok
PartitionHealthStates : 
                        PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
                        AggregatedHealthState : Ok
                        
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 14
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="service-correlation-error"></a>サービス関連エラー
**System.PLB** で、別のサービスと関連付けられるサービスの更新によってアフィニティ チェーンが作成されたことが検出されると、エラーが報告されます。 このレポートは、更新が成功した場合にクリアされます。

* **SourceId**: System.PLB
* **プロパティ**: ServiceDescription
* **次のステップ**: 関連するサービスの説明を確認します。

## <a name="partition-system-health-reports"></a>パーティション システム正常性レポート
**System.FM**は Failover Manager サービスを表し、サービス パーティションに関する情報を管理する権限です。

### <a name="state"></a>状態
System.FM は、パーティションが作成されており、正常な場合に、OK を報告します。 パーティションが削除されると、正常性ストアからエンティティを削除します。

パーティションが最小レプリカ数を下回ると、エラーを報告します。 パーティションが最小レプリカ数を下回っていなくても、ターゲット レプリカ数を下回る場合は、警告を報告します。 パーティションがクォーラム損失の状態にあるとき、System.FM はエラーを報告します。

その他の重要なイベントとして、再構成に予想よりも時間がかかる場合と、ビルドに予想よりも時間がかかる場合の警告があります。 ビルドおよび再構成の予想される時間は、サービスのシナリオに基づいて構成可能です。 たとえば、サービスに SQL Database などのテラバイトの状態がある場合、状態が小量のサービスの場合よりもビルドに長い時間がかかります。

* **SourceId**: System.FM
* **プロパティ**: State
* **次のステップ**: 正常性状態が OK でない場合、一部のレプリカが正しく作成されていないか、開かれていないか、プライマリまたはセカンダリに昇格されていない可能性があります。 

説明にクォーラムの損失について記述されている場合、ダウンしたレプリカの詳細な正常性レポートを確認し、それらをバックアップしておくと、対象パーティションを再びオンラインに戻すのに役立ちます。

パーティションが[再構成](service-fabric-concepts-reconfiguration.md)処理でスタックしていることが説明に記述されている場合、プライマリ レプリカに関する正常性レポートに詳しい情報が示されています。

他の System.FM 正常性レポートでは、他のシステム コンポーネントのレプリカ、パーティション、サービスに関するレポートが含まれます。 

次の例では、これらのレポートの一部について取り上げます。 

正常なパーティションの例を示します。

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountWebService | Get-ServiceFabricPartitionHealth -ExcludeHealthStatistics -ReplicasFilter None

PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 70
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

ターゲット レプリカ数を下回るパーティションの状態の例を次に示します。 次のステップは、パーティションの説明を取得することです。その説明は、パーティションが構成された方法を示し、**MinReplicaSetSize** が 3 で **TargetReplicaSetSize** が 7 です。 次に、クラスター内のノード数 5 を取得します。 この場合、ターゲット レプリカ数は使用可能なノードの数よりも多いため、レプリカを 2 つ配置できません。

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None -ExcludeHealthStatistics


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                        
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 123
                        SentAt                : 7/14/2017 4:55:39 PM
                        ReceivedAt            : 7/14/2017 4:55:44 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/S Ready _Node_2 131444422260002646
                          N/S Ready _Node_4 131444422293113678
                          N/S Ready _Node_3 131444422293113679
                          N/S Ready _Node_1 131444422293118720
                          N/P Ready _Node_0 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5)
                        
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:55:44 PM, LastOk = 1/1/0001 12:00:00 AM
                        
                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131445250939703027
                        SentAt                : 7/14/2017 4:58:13 PM
                        ReceivedAt            : 7/14/2017 4:58:14 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        Secondary replica could not be placed due to the following constraints and properties:  
                        TargetReplicaSetSize: 7
                        Placement Constraint: N/A
                        Parent Service: N/A
                        
                        Constraint Elimination Sequence:
                        Existing Secondary Replicas eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        Existing Primary Replica eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.
                        
                        Nodes Eliminated By Constraints:
                        
                        Existing Secondary Replicas -- Nodes with Partition's Existing Secondary Replicas/Instances:
                        --
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        
                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None
                        
                        
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:56:14 PM, LastOk = 1/1/0001 12:00:00 AM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | select MinReplicaSetSize,TargetReplicaSetSize

MinReplicaSetSize TargetReplicaSetSize
----------------- --------------------
                2                    7                        

PS C:\> @(Get-ServiceFabricNode).Count
5
```

次の例は、ユーザーが RunAsync メソッドでキャンセル トークンを考慮しないために、再構成処理でスタックしているパーティションの正常性を示しています。 プライマリ (P) とマークされているレプリカの正常性レポートを調査すると、問題の詳細を把握するのに役立ちます。

```powershell
PS C:\utilities\ServiceFabricExplorer\ClientPackage\lib> Get-ServiceFabricPartitionHealth 0e40fd81-284d-4be4-a665-13bc5a6607ec -ExcludeHealthStatistics 


PartitionId           : 0e40fd81-284d-4be4-a665-13bc5a6607ec
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                                               
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 7
                        SentAt                : 8/27/2017 3:43:09 AM
                        ReceivedAt            : 8/27/2017 3:43:32 AM
                        TTL                   : Infinite
                        Description           : Partition reconfiguration is taking longer than expected.
                        fabric:/app/test1 3 1 0e40fd81-284d-4be4-a665-13bc5a6607ec
                          P/S Ready Node1 131482789658160654
                          S/P Ready Node2 131482789688598467
                          S/S Ready Node3 131482789688598468
                          (Showing 3 out of 3 replicas. Total available replicas: 3)                        
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/27/2017 3:43:32 AM, LastError = 1/1/0001 12:00:00 AM
```
この正常性レポートには、再構成中のパーティションのレプリカの状態が示されています。 

```
  P/S Ready Node1 131482789658160654
  S/P Ready Node2 131482789688598467
  S/S Ready Node3 131482789688598468
```

各レプリカの正常性レポートには、次の項目が含まれています。
- 前の構成ロール
- 現在の構成ロール
- [レプリカの状態](service-fabric-concepts-replica-lifecycle.md)
- レプリカが実行されているノード
- レプリカ ID

このような場合、上の例のようにプライマリとしてマークされているレプリカ (131482789658160654 および 131482789688598467) で始まる各レプリカの正常性を調査することによって、詳しく調べる必要があります。

### <a name="replica-constraint-violation"></a>レプリカ制約違反
**System.PLB** は、レプリカ制約違反を検出し、すべてのパーティション レプリカを配置できない場合、警告を報告します。 レポートの詳細には、レプリカを配置できなかった原因の制約とプロパティが示されます。

* **SourceId**: System.PLB
* **プロパティ**: **ReplicaConstraintViolation** で始まります

## <a name="replica-system-health-reports"></a>レプリカ システム正常性レポート
**System.RA**は、Reconfiguration Agent コンポーネントを表し、レプリカの状態を管理する権限です。

### <a name="state"></a>状態
**System.RA** は、レプリカが作成されていると OK を報告します。

* **SourceId**: System.RA
* **プロパティ**: State

正常なレプリカの例を示します。

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422293118721
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131445248920273536
                        SentAt                : 7/14/2017 4:54:52 PM
                        ReceivedAt            : 7/14/2017 4:55:13 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_0
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:13 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="replicaopenstatus-replicaclosestatus-replicachangerolestatus"></a>ReplicaOpenStatus、ReplicaCloseStatus、ReplicaChangeRoleStatus
このプロパティは、レプリカを開いたり、閉じたり、レプリカのロールを切り替えたりする場合に生じる警告またはエラーを示すために使用されます ([レプリカ ライフサイクル](service-fabric-concepts-replica-lifecycle.md) を参照)。 API 呼び出しからスローされる例外や、その時点のサービス ホスト プロセスのクラッシュといったエラーが考えられます。 C# コードからの API 呼び出しに起因するエラーの場合、Service Fabric によって正常性レポートに例外とスタックトレースが追加されます。

これらの正常性警告は、対象アクションを何回か (回数はポリシーによって異なります) ローカルに試行した後に生じます。 Service Fabric は、最大しきい値に達するまでアクションを試行し続けます。それでも解決しない場合、対象ノードでこのアクションをあきらめて、警告の原因となっている状況を修正するためのアクションを試行し、警告を解除します。 たとえば、レプリカをノードで開くことができない場合、Service Fabric は正常性警告を発生させます。 その後もレプリカで開くことができない場合、Service Fabric は、他のノードで同じ操作を試行するなど、自己修復アクションを実行します。 これにより、対象レプリカで生じている警告がクリアされます。 

* **SourceId**: System.RA
* **プロパティ**: **ReplicaOpenStatus**、**ReplicaCloseStatus**、**ReplicaChangeRoleStatus**
* **次のステップ**: サービス コードまたはクラッシュ ダンプを調べ、操作が失敗した理由を特定します。

次の例は、Open メソッドから `TargetInvocationException` がスローされているレプリカの正常性を示しています。 この説明には、障害点 (**IStatefulServiceReplica.Open**)、例外の種類 (**TargetInvocationException**)、およびスタックトレースが含まれています。

```powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 337cf1df-6cab-4825-99a9-7595090c0b1b -ReplicaOrInstanceId 131483509874784794


PartitionId           : 337cf1df-6cab-4825-99a9-7595090c0b1b
ReplicaId             : 131483509874784794
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaOpenStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaOpenStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483510001453159
                        SentAt                : 8/27/2017 11:43:20 PM
                        ReceivedAt            : 8/27/2017 11:43:21 PM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during open on _Node_0 API call: IStatefulServiceReplica.Open(); Error = System.Reflection.TargetInvocationException (-2146232828)
Exception has been thrown by the target of an invocation.
   at Microsoft.ServiceFabric.Replicator.RecoveryManager.d__31.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.LoggingReplicator.d__137.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.DynamicStateManager.d__109.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.TransactionalReplicator.d__79.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.StatefulServiceReplica.d__21.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Services.Runtime.StatefulServiceReplicaAdapter.d__0.MoveNext()

    For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/27/2017 11:43:21 PM, LastOk = 1/1/0001 12:00:00 AM                        
```

次の例は、終了中に継続的にクラッシュしているレプリカを示しています。

```Powershell
C:>Get-ServiceFabricReplicaHealth -PartitionId dcafb6b7-9446-425c-8b90-b3fdf3859e64 -ReplicaOrInstanceId 131483565548493142


PartitionId           : dcafb6b7-9446-425c-8b90-b3fdf3859e64
ReplicaId             : 131483565548493142
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaCloseStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaCloseStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483565611258984
                        SentAt                : 8/28/2017 1:16:01 AM
                        ReceivedAt            : 8/28/2017 1:16:03 AM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during close on _Node_1. The application 
                        host has crashed.
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 1:16:03 AM, LastOk = 1/1/0001 12:00:00 AM
```

### <a name="reconfiguration"></a>Reconfiguration
このプロパティは、[再構成](service-fabric-concepts-reconfiguration.md)を実行しているレプリカが、再構成の停止やスタックを検出する場合に使用されます。 この正常性レポートは、現在のロールがプライマリであるレプリカに関するものです (ただし、プライマリからアクティブなセカンダリに降格されているレプリカに対する、スワップ プライマリ再構成の場合は例外です)。

再構成は、次の理由によって停止することがあります

- ローカル レプリカ (再構成を実行しているのと同じレプリカ) におけるアクションが完了していない。 この場合、他のコンポーネント (System.RAP または System.RE) からこのレプリカに関する正常性レポートを調査すると、詳細な情報が得られることがあります。

- リモート レプリカでアクションが完了していない。 アクションが保留中のレプリカが、正常性レポートにリストされます。 こうしたリモート レプリカの正常性レポートを詳しく調査する必要があります。 対象ノードとリモート ノード間の通信に問題がある可能性もあります。

まれなケースとして、対象ノードとフェールオーバー マネージャー サービス間の通信などの問題が原因で再構成がスタックすることもあります。

* **SourceId**: System.RA
* **プロパティ**: **Reconfiguration**
* **次のステップ**: 正常性レポートの内容に応じて、ローカル レプリカまたはリモート レプリカを調査します。

次の例は、(サービスがキャンセル トークンを考慮しないことが原因で) ローカル レプリカで再構成がスタックしている正常性レポートを示しています。

```Powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 9a0cedee-464c-4603-abbc-1cf57c4454f3 -ReplicaOrInstanceId 131483600074836703


PartitionId           : 9a0cedee-464c-4603-abbc-1cf57c4454f3
ReplicaId             : 131483600074836703
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483600309264482
                        SentAt                : 8/28/2017 2:13:50 AM
                        ReceivedAt            : 8/28/2017 2:13:57 AM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from the local replica
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 2:13:57 AM, LastOk = 1/1/0001 12:00:00 AM
```

次の例は、2 つのリモート レプリカからの応答を待機しているために再構成がスタックしている正常性レポートを示しています (現在のプライマリが含まれるパーティションには 3 つのレプリカがあります)。 

```Powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId  579d50c6-d670-4d25-af70-d706e4bc19a2 -ReplicaOrInstanceId 131483956274977415


PartitionId           : 579d50c6-d670-4d25-af70-d706e4bc19a2
ReplicaId             : 131483956274977415
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483960376212469
                        SentAt                : 8/28/2017 12:13:57 PM
                        ReceivedAt            : 8/28/2017 12:14:07 PM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from 2 replicas
                        
                        Pending Replicas: 
                        P/I Down 40 131483956244554282
                        S/S Down 20 131483956274972403
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:07:37 PM, LastOk = 1/1/0001 12:00:00 AM
```

この正常性レポートは、2 つのレプリカからの応答を待機しているために、再構成がスタックしていることを示しています。 

```
    P/I Down 40 131483956244554282
    S/S Down 20 131483956274972403
```

それぞれのレプリカに関して、次の情報が提供されます。
- 前の構成ロール
- 現在の構成ロール
- [レプリカの状態](service-fabric-concepts-replica-lifecycle.md)
- ノード ID
- レプリカ ID

再構成のブロックを解除するには、次の条件が必要です。
- **down** レプリカの実行 
- **inbuild** レプリカがビルドを完了し、準備完了状態に遷移

### <a name="slow-service-api-call"></a>低速のサービス API 呼び出し
**System.RAP** と **System.Replicator** は、ユーザー サービス コードの呼び出しにかかる時間が構成された時間よりも長い場合、警告を報告します。 呼び出しが完了すると、警告はクリアされます。

* **SourceId**: System.RAP または System.Replicator
* **プロパティ**: 低速の API の名前。 説明には、API が保留中であった時間についての詳細が示されます。
* **次のステップ**: 呼び出しに予想よりも長くかかる原因を調査します。

次の例は、RunAsync のキャンセル トークンを考慮しない Reliable services に関する、System.RAP からの正常性イベントを示しています。

```powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 5f6060fb-096f-45e4-8c3d-c26444d8dd10 -ReplicaOrInstanceId 131483966141404693


PartitionId           : 5f6060fb-096f-45e4-8c3d-c26444d8dd10
ReplicaId             : 131483966141404693
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          :                         
                        SourceId              : System.RAP
                        Property              : IStatefulServiceReplica.ChangeRole(S)Duration
                        HealthState           : Warning
                        SequenceNumber        : 131483966663476570
                        SentAt                : 8/28/2017 12:24:26 PM
                        ReceivedAt            : 8/28/2017 12:24:56 PM
                        TTL                   : Infinite
                        Description           : The api IStatefulServiceReplica.ChangeRole(S) on _Node_1 is stuck. Start Time (UTC): 2017-08-28 12:23:56.347.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:24:56 PM, LastOk = 1/1/0001 12:00:00 AM
                        
```

プロパティとテキストには、スタックする可能性がある API が示されます。 スタックした API によって、次のステップも異なります。 通常、*IStatefulServiceReplica* または *IStatelessServiceInstance* はサービス コードのバグです。 次のセクションでは、[Reliable services モデル](service-fabric-reliable-services-lifecycle.md)にどのように変換されるかを取り上げます。

- **IStatefulServiceReplica.Open**: これは、`CreateServiceInstanceListeners` または `ICommunicationListener.OpenAsync` に対する呼び出し、あるいは上書きされた `OnOpenAsync` がスタックしているかどうかを示します。

- **IStatefulServiceReplica.Close** および **IStatefulServiceReplica.Abort**: 最も一般的なのは、`RunAsync` に渡されたキャンセル トークンを考慮しないサービスです。 または、`ICommunicationListener.CloseAsync` あるいは上書きされた `OnCloseAsync` がスタックしている場合にも生じます。

- **IStatefulServiceReplica.ChangeRole(S)** および **IStatefulServiceReplica.ChangeRole(N)**: 最も一般的なのは、`RunAsync` に渡されたキャンセル トークンを考慮しないサービスです。

- **IStatefulServiceReplica.ChangeRole(P)**: 最も一般的なのは、`RunAsync` からタスクが戻されないサービスです。

**IReplicator** インターフェイス上の他の API 呼び出しもスタックする可能性があります。 For example:

- **IReplicator.CatchupReplicaSet**: 十分な数の実行中のレプリカがないこと (パーティションのレプリカのレプリカ状態を確認するか、スタック再構成の System.FM 正常性レポートを確認して判別できます)、またはレプリカが操作を認識できないことを示します。 Powershell コマンドレット `Get-ServiceFabricDeployedReplicaDetail` を使用すると、すべてのレプリカの進行状況を判断できます。 プライマリの `CommittedSequenceNumber` の後ろにある `LastAppliedReplicationSequenceNumber` が含まれるレプリカに問題があります。

- **IReplicator.BuildReplica (<Remote ReplicaId>)**: これは、ビルド プロセスに問題があることを示します ([レプリカ ライフサイクル](service-fabric-concepts-replica-lifecycle.md)を参照)。 レプリカ アドレスが正しく構成されていないことが原因の可能性があります ([こちら](service-fabric-reliable-services-configuration.md)と[こちら](service-fabric-service-manifest-resources.md)を参照)。 リモート ノードに問題があることもあります。

### <a name="replication-queue-full"></a>レプリケーション キュー満杯
**System.Replicator** は、レプリケーション キューが満杯の場合、警告を報告します。 プライマリでレプリケーション キューが満杯になる原因は、通常、1 つまたは複数のセカンダリ レプリカで、処理の確認に時間がかかることです。 セカンダリでこの状態が発生する原因は、通常、サービスでの操作の適用に時間がかかることです。 キューが満杯でなくなると、警告はクリアされます。

* **SourceId**: System.Replicator
* **Property**: レプリカのロールに応じて **PrimaryReplicationQueueStatus** または **SecondaryReplicationQueueStatus**

### <a name="slow-naming-operations"></a>名前付け操作が遅い
**System.NamingService** は、名前付け操作にかかる時間が許容範囲を超える場合に、そのプライマリ レプリカの正常性を報告します。 名前付け操作の例として、[CreateServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) または [DeleteServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync) があります。 FabricClient には、[サービス管理メソッド](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient)や[プロパティ管理メソッド](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.propertymanagementclient)など、その他多くのメソッドが見つかります。

> [!NOTE]
> ネーム サービスは、サービス名を解決してクラスター内の場所に対応付けて、ユーザーによるサービス名とプロパティの管理を可能にします。 これは、Service Fabric のパーティション分割型の永続化されたサービスです。 パーティションの 1 つは、Service Fabric のすべての名前とサービスに関するメタデータを含む Authority Owner を表します。 Service Fabric の名前は、Name Owner パーティションという各種パーティションにマップされるため、サービスは拡張可能です。 詳細については、 [ネーム サービス](service-fabric-architecture.md)に関するページを参照してください。
> 
> 

名前付け操作に予想以上の時間がかかると、" *操作を実行するネーム サービス パーティションのプライマリ レプリカ*" に関する警告のレポートでフラグが設定されます。 操作が正常に完了すると、警告はクリアされます。 操作がエラーで終了した場合は、正常性レポートにエラーの詳細が含まれます。

* **SourceId**: System.NamingService
* **Property**: プレフィックス **Duration_** で始まり、時間がかかっている操作とその操作が適用されている Service Fabric の名前を識別します。 たとえば、fabric:/MyApp/MyService という名前のサービスの作成に時間がかかる場合、プロパティは Duration_AOCreateService.fabric:/MyApp/MyService になります。 AO は、この名前と操作の名前付けパーティションの役割を指します。
* **次のステップ**: 名前付け操作に失敗した原因を確認します。 各操作の根本原因は異なる場合があります。 たとえば、サービス コード内のユーザー バグによってアプリケーション ホストがノードでクラッシュしたままになっていることが原因で、サービスの削除がスタックすることがあります。

サービスの作成操作の例を次に示します。 この操作には、構成された期間よりも長い時間がかかりました。 AO は再試行し、作業を NO に送信します。 NO は、タイムアウトにより最後の操作を完了しました。 この場合、同じレプリカが AO と NO の両方の役割でプライマリになります。

```powershell
PartitionId           : 00000000-0000-0000-0000-000000001000
ReplicaId             : 131064359253133577
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.NamingService', Property='Duration_AOCreateService.fabric:/MyApp/MyService', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131064359308715535
                        SentAt                : 4/29/2016 8:38:50 PM
                        ReceivedAt            : 4/29/2016 8:39:08 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 4/29/2016 8:39:08 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_AOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064359526778775
                        SentAt                : 4/29/2016 8:39:12 PM
                        ReceivedAt            : 4/29/2016 8:39:38 PM
                        TTL                   : 00:05:00
                        Description           : The AOCreateService started at 2016-04-29 20:39:08.677 is taking longer than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_NOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064360657607311
                        SentAt                : 4/29/2016 8:41:05 PM
                        ReceivedAt            : 4/29/2016 8:41:08 PM
                        TTL                   : 00:00:15
                        Description           : The NOCreateService started at 2016-04-29 20:39:08.689 completed with FABRIC_E_TIMEOUT in more than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="deployedapplication-system-health-reports"></a>DeployedApplication システム正常性レポート
**System.Hosting** は、デプロイ済みのエンティティでの権限です。

### <a name="activation"></a>アクティブ化
System.Hosting は、アプリケーションがノードで正常にアクティブ化されていると OK を報告します。 それ以外の場合、エラーを報告します。

* **SourceId**: System.Hosting
* **プロパティ**: ロールアウト バージョンを含むアクティブ化
* **次のステップ**: アプリケーションが正常でない場合、アクティブ化が失敗した原因を調査します。

アクティブ化の成功例を次に示します。

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ExcludeHealthStatistics

ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_1
                                     AggregatedHealthState : Ok
                                     
HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131445249083836329
                                     SentAt                : 7/14/2017 4:55:08 PM
                                     ReceivedAt            : 7/14/2017 4:55:14 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>ダウンロード
**System.Hosting** は、アプリケーション パッケージのダウンロードが失敗した場合、エラーを報告します。

* **SourceId**: System.Hosting
* **Property**: **Download:*RolloutVersion***
* **次のステップ**: ノードでダウンロードが失敗した原因を調査します。

## <a name="deployedservicepackage-system-health-reports"></a>DeployedServicePackage システム正常性レポート
**System.Hosting** は、デプロイ済みのエンティティでの権限です。

### <a name="service-package-activation"></a>サービス パッケージのアクティブ化
System.Hosting は、ノードでのサービス パッケージのアクティブ化が成功すると、OK を報告します。 それ以外の場合、エラーを報告します。

* **SourceId**: System.Hosting
* **プロパティ**: Activation
* **次のステップ**: アクティブ化が失敗した原因を調査します。

### <a name="code-package-activation"></a>コード パッケージのアクティブ化
**System.Hosting** は、各コード パッケージのアクティブ化が成功すると、OK を報告します。 アクティブ化に失敗した場合は、構成されているとおりに警告を報告します。 **CodePackage** がアクティブ化に失敗したか、構成されている **CodePackageHealthErrorThreshold** より大きいエラーで終了した場合、Hosting はエラーを報告します。 サービス パッケージに複数のコード パッケージが含まれている場合、コード パッケージごとにアクティブ化レポートが生成されます。

* **SourceId**: System.Hosting
* **Property**: プレフィックス **CodePackageActivation** を使用し、**CodePackageActivation:*CodePackageName*:*SetupEntryPoint/EntryPoint* として、コード パッケージの名前とエントリ ポイントを含みます** (**CodePackageActivation:Code:SetupEntryPoint** など)

### <a name="service-type-registration"></a>サービスの種類の登録
**System.Hosting** は、サービスの種類が正常に登録されていると、OK を報告します。 (**ServiceTypeRegistrationTimeout** を使用して構成されている) 時間内に登録が行われなかった場合は、エラーを報告します。 ランタイムが終了すると、サービスの種類はノードの登録から削除され、Hosting から警告が報告されます。

* **SourceId**: System.Hosting
* **Property**: プレフィックス **ServiceTypeRegistration** を使用し、サービスの種類の名前を含みます (**ServiceTypeRegistration:FileStoreServiceType** など)

正常なデプロイ済みサービス パッケージの例を次に示します。

```powershell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_1
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131445249084026346
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131445249084306362
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131445249088096842
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>ダウンロード
**System.Hosting** は、サービス パッケージのダウンロードが失敗すると、エラーを報告します。

* **SourceId**: System.Hosting
* **Property**: **Download:*RolloutVersion***
* **次のステップ**: ノードでダウンロードが失敗した原因を調査します。

### <a name="upgrade-validation"></a>アップグレードの検証
**System.Hosting** は、アップグレード中に検証が失敗した場合、またはノードでアップグレードが失敗した場合、エラーを報告します。

* **SourceId**: System.Hosting
* **Property**: プレフィックス **FabricUpgradeValidation** を使用し、アップグレード バージョンを含みます
* **説明**: 発生したエラーが参照されます。

## <a name="next-steps"></a>次のステップ
[Service Fabric の正常性レポートの確認](service-fabric-view-entities-aggregated-health.md)

[サービス正常性のレポートとチェックの方法](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[ローカルでのサービスの監視と診断](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric アプリケーションのアップグレード](service-fabric-application-upgrade.md)


