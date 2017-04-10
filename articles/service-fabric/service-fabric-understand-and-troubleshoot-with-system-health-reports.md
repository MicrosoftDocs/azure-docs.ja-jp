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
ms.date: 01/12/2017
ms.author: oanapl
translationtype: Human Translation
ms.sourcegitcommit: d20b8d5848d1a11326c60d998099571a4ab8056e
ms.openlocfilehash: 0306b8c38a7dd86dff56f6cc7bb9eab7e0428762


---
# <a name="use-system-health-reports-to-troubleshoot"></a>システム正常性レポートを使用したトラブルシューティング
Azure Service Fabric コンポーネントは、追加の設定なしでクラスター内のすべてのエンティティについてレポートします。 [正常性ストア](service-fabric-health-introduction.md#health-store) は、システム レポートに基づいてエンティティを作成および削除します。 さらに、エンティティの相互作用をキャプチャする階層で、それらを編成します。

> [!NOTE]
> 正常性に関する概念については、「 [Service Fabric の正常性モニタリングの概要](service-fabric-health-introduction.md)」を参照してください。
> 
> 

システム正常性レポートは、クラスターとアプリケーションの動作状況を可視化し、正常性の問題を警告します。 システム正常性レポートは、アプリケーションとサービスを対象に、エンティティが実装されて正しく動作していることを Service Fabric の観点から確認します。 レポートは、サービスのビジネス ロジックの正常性モニタリングやハングしたプロセスの検出を提供するものではありません。 ユーザー サービスでロジックに固有の情報を追加して正常性データを強化できます。

> [!NOTE]
> ウォッチドッグ正常性レポートは、システム コンポーネントでエンティティが作成された *後* にのみ表示できます。 エンティティが削除されると、正常性ストアは関連付けられているすべての正常性レポートを自動的に削除します。 エンティティの新しいインスタンスが作成される (たとえば、新しいサービス レプリカのインスタンスが作成される) 場合も同じことが当てはまります。 古いインスタンスに関連付けられているすべてのレポートが削除され、ストアからクリーンアップされます。
> 
> 

システム コンポーネント レポートはソース別に識別され、"**System.**" プレフィックスで 始まります。 ウォッチドッグのソースに同じプレフィックスを使用することはできません (無効なパラメーターを持つレポートが拒否されるため)。
いくつかのシステム レポートを確認し、何がレポートのトリガーになっているか、レポートに表示された問題を修正する方法を理解しましょう。

> [!NOTE]
> Service Fabric では、指定した条件に関するレポートが継続的に追加され、クラスターおよびアプリケーションの状況をより詳細に把握できます。
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
**System.FM**は Failover Manager サービスを表し、クラスター ノードに関する情報を管理する権限です。 どのノードにも、ノードの状態を示す System.FM からのレポートが 1 つあるはずです。 ノードの状態が削除されると、ノード エンティティは削除されます ( [RemoveNodeStateAsync](https://msdn.microsoft.com/library/azure/mt161348.aspx)を参照)。

### <a name="node-updown"></a>ノードを上/下に移動
System.FM は、ノードがリングに参加する (稼動している) と、OK と報告します。 ノードがリングから外れる (アップグレードのため、または単に障害が発生しているため停止している) と、エラーを報告します。 正常性ストアによって構築された正常性の階層は、デプロイ済みエンティティに対して、System.FM ノード レポートに関連したアクションを実行します。 その階層では、ノードは、デプロイ済みのすべてのエンティティの仮想的な親ノードと見なされます。 そのノードにデプロイされたエンティティは、ノードが System.FM によって起動されたものとしてレポートされた場合、エンティティに関連付けられているインスタンスと同じインスタントと共に、クエリを通じて公開されます。 System.FM によってノードの停止または再起動 (新規インスタンス) が報告されると、正常性ストアは、停止したノードまたはノードの以前のインスタンスのみに存在している可能性のあるデプロイ済みエンティティを自動的にクリーンアップします。

* **SourceId**: System.FM
* **プロパティ**: State
* **次のステップ**: ノードがアップグレードのために停止している場合、アップグレード後に復帰する必要があります。 この例では、正常性状態が OK に切り替わる必要があります。 ノードが復帰しない場合、またはエラーが発生した場合は、さらに問題を調査する必要があります。

ノードの稼動を表す正常性状態 OK の System.FM イベントの例を次に示します。

```powershell

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 2
                        SentAt                : 4/24/2015 5:27:33 PM
                        ReceivedAt            : 4/24/2015 5:28:50 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 5:28:50 PM

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
* **次のステップ**: アプリケーションが作成されたら、Cluster Manager 正常性レポートを含める必要があります。 作成されなかった場合は、クエリを発行してアプリケーションの状態を確認します (例: Powershell コマンドレット **Get-ServiceFabricApplication -ApplicationName *applicationName***)。

**fabric:/WordCount** アプリケーションの State イベントの例を次に示します。

```powershell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesFilter None -DeployedApplicationsFilter None

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 82
                                  SentAt                : 4/24/2015 6:12:51 PM
                                  ReceivedAt            : 4/24/2015 6:12:51 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/24/2015 6:12:51 PM
```

## <a name="service-system-health-reports"></a>サービス システム正常性レポート
**System.FM**は Failover Manager サービスを表し、サービスに関する情報を管理する権限です。

### <a name="state"></a>状態
System.FM は、サービスが作成されると OK を報告します。 サービスが削除されたら、正常性ストアからエンティティを削除します。

* **SourceId**: System.FM
* **プロパティ**: State

**fabric:/WordCount/WordCountService**サービスの State イベントの例を次に示します。

```powershell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountService

ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Ok
PartitionHealthStates :
                        PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:01 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:01 PM
```

### <a name="unplaced-replicas-violation"></a>未配置レプリカ違反
**System.PLB** は、1 つ以上のサービス レプリカの位置を見つけられなかった場合に警告を報告します。 レポートは有効期限になると削除されます。

* **SourceId**: System.FM
* **プロパティ**: State
* **次のステップ**: サービスの制約と配置の現在の状態を確認します。

以下に違反の例を示します。この例では、5 つのノードを持つクラスターに 7 つのターゲット レプリカでサービスが構成されています。

```xml
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountService


ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB',
                        Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                        ConsiderWarningAsError=false.

PartitionHealthStates :
                        PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
                        AggregatedHealthState : Warning

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 10
                        SentAt                : 3/22/2016 7:56:53 PM
                        ReceivedAt            : 3/22/2016 7:57:18 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b
                        HealthState           : Warning
                        SequenceNumber        : 131032232425505477
                        SentAt                : 3/23/2016 4:14:02 PM
                        ReceivedAt            : 3/23/2016 4:14:03 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        fabric:/WordCount/WordCountService Secondary Partition a1f83a35-d6bf-4d39-b90d-28d15f39599b could not be placed, possibly,
                        due to the following constraints and properties:  
                        Placement Constraint: N/A
                        Depended Service: N/A

                        Constraint Elimination Sequence:
                        ReplicaExclusionStatic eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        ReplicaExclusionDynamic eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.

                        Nodes Eliminated By Constraints:

                        ReplicaExclusionStatic:
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status:
                        None/None

                        ReplicaExclusionDynamic:
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status:
                        None/None


                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 3/22/2016 7:57:48 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="partition-system-health-reports"></a>パーティション システム正常性レポート
**System.FM**は Failover Manager サービスを表し、サービス パーティションに関する情報を管理する権限です。

### <a name="state"></a>状態
System.FM は、パーティションが作成されており、正常な場合に、OK を報告します。 パーティションが削除されると、正常性ストアからエンティティを削除します。

パーティションが最小レプリカ数を下回ると、エラーを報告します。 パーティションが最小レプリカ数を下回っていなくても、ターゲット レプリカ数を下回る場合は、警告を報告します。 パーティションがクォーラム損失の状態にあるとき、System.FM はエラーを報告します。

その他の重要なイベントとして、再構成に予想よりも時間がかかる場合と、ビルドに予想よりも時間がかかる場合の警告があります。 ビルドおよび再構成の予想される時間は、サービスのシナリオに基づいて構成可能です。 たとえば、サービスに SQL Database などのテラバイトの状態がある場合、状態が小量のサービスの場合よりもビルドに長い時間がかかります。

* **SourceId**: System.FM
* **プロパティ**: State
* **次のステップ**: 正常性状態が OK でない場合、一部のレプリカが正しく作成されていないか、開かれていないか、プライマリまたはセカンダリに昇格されていない可能性があります。 多くの場合、根本的な原因は、ロールを開くか変更する実装でのサービスのバグです。

正常なパーティションの例を示します。

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/StatelessPiApplication/StatelessPiService | Get-ServiceFabricPartitionHealth
PartitionId           : 29da484c-2c08-40c5-b5d9-03774af9a9bf
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 38
                        SentAt                : 4/24/2015 6:33:10 PM
                        ReceivedAt            : 4/24/2015 6:33:31 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:33:31 PM
```

ターゲット レプリカ数を下回るパーティションの状態の例を次に示します。 次のステップは、パーティションの説明を取得することです。その説明は、パーティションが構成された方法を示し、**MinReplicaSetSize** が 2 で **TargetReplicaSetSize** が 7 です。 次に、クラスター内のノード数 5 を取得します。 したがって、この例では 2 つのレプリカを配置できません。

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None

PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 37
                        SentAt                : 4/24/2015 6:13:12 PM
                        ReceivedAt            : 4/24/2015 6:13:31 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 4/24/2015 6:13:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService

PartitionId            : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
PartitionKind          : Int64Range
PartitionLowKey        : 1
PartitionHighKey       : 26
PartitionStatus        : Ready
LastQuorumLossDuration : 00:00:00
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 7
HealthState            : Warning
DataLossNumber         : 130743727710830900
ConfigurationNumber    : 8589934592


PS C:\> @(Get-ServiceFabricNode).Count
5
```

### <a name="replica-constraint-violation"></a>レプリカ制約違反
**System.PLB** は、レプリカ制約違反を検出し、パーティションのレプリカを配置できない場合、警告を報告します。

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
PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
ReplicaId             : 130743727717237310
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743727718018580
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:02 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:02 PM
```

### <a name="replica-open-status"></a>開いている状態のレプリカ
この正常性レポートの説明には、API 呼び出しが行われたときの開始時刻 (世界協定時刻) が含まれます。

**System.RA** は、レプリカを開くために、構成されている期間 (既定値: 30 分) よりも長い時間がかかる場合、警告を報告します。 API がサービスの可用性に影響する場合、レポートは大幅に短時間で発行されます (間隔は構成可能で既定値は 30 秒)。 計測された時間には、レプリケーターを開く処理とサービスを開く処理にかかる時間が含まれます。 プロパティは、開く処理が完了すると OK に変わります。

* **SourceId**: System.RA
* **プロパティ**サービスの State イベントの例を次に示します。 **ReplicaOpenStatus**
* **次のステップ**: 正常性の状態が OK でない場合は、レプリカを開く処理が予想よりも長くかかる原因を調査します。

### <a name="slow-service-api-call"></a>低速のサービス API 呼び出し
**System.RAP** と **System.Replicator** は、ユーザー サービス コードの呼び出しにかかる時間が構成された時間よりも長い場合、警告を報告します。 呼び出しが完了すると、警告はクリアされます。

* **SourceId**: System.RAP または System.Replicator
* **プロパティ**: 低速の API の名前。 説明には、API が保留中であった時間についての詳細が示されます。
* **次のステップ**: 呼び出しに予想よりも長くかかる原因を調査します。

次の例は、クォーラム損失の状態にあるパーティションと、理由を解明するために実行した調査のステップを示します。 レプリカの 1 つの正常性状態が警告になっているため、そのレプリカの正常性を取得します。 サービス操作に予想よりも長くかかることを示しています (System.RAP によって報告されたイベント)。 この情報を受け取った後、次のステップはサービス コードを確認し、調査することです。 この場合、ステートフル サービスの **RunAsync** 実装は、未処理の例外をスローします。 レプリカはリサイクルされるため、警告状態のレプリカが 1 つもない場合もあります。 正常性状態の取得を再試行し、レプリカ ID の違いを探します。 場合によっては、再試行により手掛かりが得られることがあります。

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful | Get-ServiceFabricPartitionHealth

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
AggregatedHealthState : Error
UnhealthyEvaluations  :
                        Error event: SourceId='System.FM', Property='State'.

ReplicaHealthStates   :
                        ReplicaId             : 130743748372546446
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746168084332
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746195428808
                        AggregatedHealthState : Warning

                        ReplicaId             : 130743746195428807
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Error
                        SequenceNumber        : 182
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:31 PM
                        TTL                   : Infinite
                        Description           : Partition is in quorum loss.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Warning->Error = 4/24/2015 6:51:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful

PartitionId            : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
PartitionKind          : Int64Range
PartitionLowKey        : -9223372036854775808
PartitionHighKey       : 9223372036854775807
PartitionStatus        : InQuorumLoss
LastQuorumLossDuration : 00:00:13
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 3
HealthState            : Error
DataLossNumber         : 130743746152927699
ConfigurationNumber    : 227633266688

PS C:\> Get-ServiceFabricReplica 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

ReplicaId           : 130743746195428808
ReplicaAddress      : PartitionId: 72a0fb3e-53ec-44f2-9983-2f272aca3e38, ReplicaId: 130743746195428808
ReplicaRole         : Primary
NodeName            : Node.3
ReplicaStatus       : Ready
LastInBuildDuration : 00:00:01
HealthState         : Warning

PS C:\> Get-ServiceFabricReplicaHealth 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
ReplicaId             : 130743746195428808
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.RAP', Property='ServiceOpenOperationDuration', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743756170185892
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:33 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 7:00:33 PM

                        SourceId              : System.RAP
                        Property              : ServiceOpenOperationDuration
                        HealthState           : Warning
                        SequenceNumber        : 130743756399407044
                        SentAt                : 4/24/2015 7:00:39 PM
                        ReceivedAt            : 4/24/2015 7:00:59 PM
                        TTL                   : Infinite
                        Description           : Start Time (UTC): 2015-04-24 19:00:17.019
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/24/2015 7:00:59 PM
```

障害のあるアプリケーションをデバッガーで起動すると、診断イベント ウィンドウに RunAsync からスローされた例外が表示されます。

![Visual Studio 2015 診断イベント: fabric:/HelloWorldStatefulApplication の RunAsync エラー][1]

Visual Studio 2015 診断イベント: **fabric:/HelloWorldStatefulApplication**の RunAsync エラー。

[1]: ./media/service-fabric-understand-and-troubleshoot-with-system-health-reports/servicefabric-health-vs-runasync-exception.png


### <a name="replication-queue-full"></a>レプリケーション キュー満杯
**System.Replicator** は、レプリケーション キューが満杯の場合、警告を報告します。 プライマリでこの状態が発生する原因は、通常、1 つまたは複数のセカンダリ レプリカで、処理の確認に時間がかかることです。 セカンダリでこの状態が発生する原因は、通常、サービスでの操作の適用に時間がかかることです。 キューが満杯でなくなると、警告はクリアされます。

* **SourceId**: System.Replicator
* **Property**: レプリカのロールに応じて **PrimaryReplicationQueueStatus** または **SecondaryReplicationQueueStatus**

### <a name="slow-naming-operations"></a>名前付け操作が遅い
**System.NamingService** は、名前付け操作にかかる時間が許容範囲を超える場合に、そのプライマリ レプリカの正常性を報告します。 名前付け操作の例として、[CreateServiceAsync](https://msdn.microsoft.com/library/azure/mt124028.aspx) または [DeleteServiceAsync](https://msdn.microsoft.com/library/azure/mt124029.aspx) があります。 FabricClient には、[サービス管理メソッド](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.aspx)や[プロパティ管理メソッド](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.propertymanagementclient.aspx)など、その他多くのメソッドが見つかります。

> [!NOTE]
> ネーム サービスは、サービス名を解決してクラスター内の場所に対応付けて、ユーザーによるサービス名とプロパティの管理を可能にします。 これは、Service Fabric のパーティション分割型の永続化されたサービスです。 パーティションの 1 つは、Service Fabric のすべての名前とサービスに関するメタデータを含む Authority Owner を表します。 Service Fabric の名前は、Name Owner パーティションという各種パーティションにマップされるため、サービスは拡張可能です。 詳細については、 [ネーム サービス](service-fabric-architecture.md)に関するページを参照してください。
> 
> 

名前付け操作に予想以上の時間がかかると、" *操作を実行するネーム サービス パーティションのプライマリ レプリカ*" に関する警告のレポートでフラグが設定されます。 操作が正常に完了すると、警告はクリアされます。 操作がエラーで終了した場合は、正常性レポートにエラーの詳細が含まれます。

* **SourceId**: System.NamingService
* **Property**: プレフィックス **Duration_** で始まり、時間がかかっている操作とその操作が適用されている Service Fabric の名前を識別します。 たとえば、fabric:/MyApp/MyService という名前のサービスの作成に時間がかかる場合、プロパティは Duration_AOCreateService.fabric:/MyApp/MyService になります。 AO は、この名前と操作の名前付けパーティションの役割を指します。
* **次のステップ**: 名前付け操作に失敗した原因を確認します。 各操作の根本原因は異なる場合があります。 たとえば、サービス コード内のユーザー バグによってアプリケーション ホストがノードでクラッシュしたままになっていることが原因で、ノードではサービスの削除が停止することがあります。

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
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName Node.1 -ApplicationName fabric:/WordCount

ApplicationName                    : fabric:/WordCount
NodeName                           : Node.1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCountServicePkg
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 130743727751144415
                                     SentAt                : 4/24/2015 6:12:55 PM
                                     ReceivedAt            : 4/24/2015 6:13:03 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : ->Ok = 4/24/2015 6:13:03 PM
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
* **Property**: プレフィックス **CodePackageActivation** を使用し、**CodePackageActivation:*CodePackageName*:*SetupEntryPoint/EntryPoint*** として、コード パッケージの名前とエントリ ポイントを含みます (**CodePackageActivation:Code:SetupEntryPoint** など)

### <a name="service-type-registration"></a>サービスの種類の登録
**System.Hosting** は、サービスの種類が正常に登録されていると、OK を報告します。 (**ServiceTypeRegistrationTimeout** を使用して構成されている) 時間内に登録が行われなかった場合は、エラーを報告します。 ランタイムが閉じられたために、サービスの種類がノードから登録解除された場合には、 Hosting は警告を報告します。

* **SourceId**: System.Hosting
* **Property**: プレフィックス **ServiceTypeRegistration** を使用し、サービスの種類の名前を含みます (**ServiceTypeRegistration:FileStoreServiceType** など)

正常なデプロイ済みサービス パッケージの例を次に示します。

```powershell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName Node.1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCountServicePkg
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 130743727751456915
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 130743727751613185
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCountServiceType
                        HealthState           : Ok
                        SequenceNumber        : 130743727753644473
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM
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




<!--HONumber=Jan17_HO2-->


