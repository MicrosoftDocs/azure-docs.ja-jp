---
title: "Azure Service Fabric のエンティティの正常性の集計を確認する方法 | Microsoft Docs"
description: "正常性クエリと一般クエリを通じて、Azure Service Fabric のエンティティの正常性の集計をクエリ、表示、評価する方法について説明します。"
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: 
ms.assetid: fa34c52d-3a74-4b90-b045-ad67afa43fe5
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2017
ms.author: oanapl
translationtype: Human Translation
ms.sourcegitcommit: 2573f300231ceb080343dad5c1c2aa65cc31c59e
ms.openlocfilehash: f62a7a8f594c67cac687159d72bfdb00a3355025


---
# <a name="view-service-fabric-health-reports"></a>Service Fabric の正常性レポートの確認
Azure Service Fabric に正常性エンティティで構成される[正常性モデル](service-fabric-health-introduction.md)が導入されます。ここでは、システム コンポーネントやウォッチドッグで監視しているローカルの状態についてのレポートを確認できます。 すべての正常性データは[正常性ストア](service-fabric-health-introduction.md#health-store)によって集計され、エンティティが正常であるかどうかが判断されます。

既定では、クラスターには、システム コンポーネントによって送信される正常性レポートが事前設定されます。 詳細については、「 [トラブルシューティングのためのシステム正常性レポートの使用](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)」を参照してください。

Service Fabric には、エンティティの正常性の集計を取得する方法が複数用意されています。

* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) またはその他の視覚化ツール
* 正常性クエリ (PowerShell、API、または REST を使用)
* 正常性をプロパティの 1 つとして取得するエンティティの一覧を返す一般クエリ (PowerShell、API、または REST を使用)

これらのオプションについて、5 つのノードがあるローカル クラスターを使用して説明しましょう。 **fabric:/System** アプリケーション (最初から使用可) の横にいくつかのアプリケーションがデプロイされています。 それらのアプリケーションのうちの 1 つが **fabric:/WordCount**です。 このアプリケーションには、7 のレプリカで構成されたステートフル サービスが含まれています。 ノードは 5 つのみであるため、システム コンポーネントからパーティションがターゲット数より少ないという警告が表示されます。

```xml
<Service Name="WordCountService">
    <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
      <UniformInt64Partition PartitionCount="1" LowKey="1" HighKey="26" />
    </StatefulService>
</Service>
```

## <a name="health-in-service-fabric-explorer"></a>Service Fabric Explorer での正常性
Service Fabric Explorer では、クラスターを視覚的に確認できます。 以下の図では、次の情報を確認できます。

* プロパティ **Availability** について **MyWatchdog** からエラー イベントが報告されているため、アプリケーション **fabric:/WordCount** が赤色 (エラー) になっています。
* そのサービスの 1 つである **fabric:/WordCount/WordCountService** が黄色 (警告) になっています。 サービスは 7 つのレプリカで構成されていますが、ノードは 5 つのみであるため、すべてを配置することはできません。 ここでは確認できませんが、システム レポートが原因でサービス パーティションが黄色になっています。 黄色のパーティションは黄色のサービスをトリガーします。
* 赤色のアプリケーションが原因でクラスターが赤色になっています。

評価にはクラスター マニフェストやアプリケーション マニフェストの既定のポリシーを使用します。 これらのポリシーは厳密なポリシーで、失敗は許容されません。

Service Fabric Explorer を使用したクラスターのビュー

![Service Fabric Explorer を使用したクラスターのビュー。][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [!NOTE]
> [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)の詳細をご覧ください。
>
>

## <a name="health-queries"></a>正常性クエリ
Service Fabric では、サポート対象の各 [エンティティ型](service-fabric-health-introduction.md#health-entities-and-hierarchy)について正常性クエリが公開されています。 API ( **FabricClient.HealthManager**にあるメソッド)、PowerShell コマンドレット、および REST を使用してアクセスできます。 これらのクエリは、正常性状態の集計、エンティティの正常性イベント、子の正常性状態 (該当する場合)、エンティティが正常でない場合の異常評価、といったエンティティの正常性についての完全な情報を返します。

> [!NOTE]
> 正常性エンティティは、正常性ストアにすべてが設定されると返されます。 エンティティはアクティブな (削除されていない) 状態で、システム レポートを含む必要があります。 階層チェーン上の親エンティティも、システム レポートを含む必要があります。 これらの条件が満たされていないと、正常性クエリでは例外が返され、エンティティが返されない理由が表示されます。
>
>

正常性クエリは、エンティティの識別子を渡す必要があります。エンティティの識別子はエンティティの型によって異なります。 正常性クエリには、オプションで正常性ポリシーのパラメーターを指定できます。 正常性ポリシーを指定しないと、クラスター マニフェストやアプリケーション マニフェストの[正常性ポリシー](service-fabric-health-introduction.md#health-policies)が評価に使用されます。 正常性クエリにはフィルターも適用できます。適用すると、指定したフィルターに該当する子やイベントのみが返されます。

> [!NOTE]
> 出力フィルターはサーバー側に適用されるため、応答されるメッセージのサイズが減少します。 フィルターをクライアント側に適用するよりも、返されたデータの数を制限するために出力フィルターを使用することをお勧めします。
>
>

エンティティの正常性に含まれているものは、次のとおりです。

* エンティティの正常性状態の集計。 エンティティの正常性レポート、子の正常性状態 (該当する場合)、および正常性ポリシーに基づいて、正常性ストアによって計算されます。 [エンティティの正常性評価](service-fabric-health-introduction.md#health-evaluation)の詳細をご覧ください。  
* エンティティの正常性イベント。
* 子が存在するエンティティについては、すべての子の正常性状態のコレクション。 正常性状態にはエンティティの識別子と正常性状態の集計が含まれます。 子の完全な正常性を取得するには、子のエンティティ型に対して正常性クエリを呼び出し、子の識別子を渡します。
* エンティティが正常でない場合は、エンティティの状態をトリガーしたレポートを示す異常性の評価。

## <a name="get-cluster-health"></a>クラスターの正常性の取得
クラスター エンティティの正常性を返し、アプリケーションとノード (クラスターの子) の正常性状態が含まれます。 次の内容を入力します。

* [省略可能] ノードとクラスターのイベントを評価するために使用されるクラスターの正常性ポリシー。
* [省略可能] アプリケーション マニフェストのポリシーを上書きするために使用するアプリケーションの正常性ポリシー マップと正常性ポリシー。
* [省略可能] 結果で返す対象のエントリ (たとえば、エラーのみ、警告とエラーの両方) を指定する、イベント、ノード、およびアプリケーションのフィルター。 すべてのイベント、ノード、およびアプリケーションは、フィルターに関係なく、エンティティで集計された正常性の評価に使用されます。

### <a name="api"></a>API
クラスターの正常性を取得するには、 `FabricClient` を作成し、 [HealthManager](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getclusterhealthasync.aspx) で **GetClusterHealthAsync**メソッドを呼び出します。

次の呼び出しでは、クラスターの正常性を取得します。

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

次のコードは、カスタム クラスターの正常性ポリシーとノードおよびアプリケーションのフィルターを使用して、クラスターの正常性を取得します。 入力情報が含まれる [ClusterHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.clusterhealthquerydescription.aspx)が作成されます。

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error | HealthStateFilter.Warning
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
};

ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
クラスターの正常性を取得するコマンドレットは [Get-ServiceFabricClusterHealth](https://msdn.microsoft.com/library/mt125850.aspx)です。 まず、 [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) コマンドレットを使用してクラスターに接続します。

クラスターの状態 (5 つのノード、システム アプリケーション、fabric:/WordCount) は上記のように構成されています。

次のコマンドレットは、既定の正常性ポリシーを使用してクラスターの正常性を取得します。 fabric:/WordCount アプリケーションが警告になっているため、正常性状態の集計は警告になります。 異常性の評価には、正常性の集計をトリガーした条件の詳細が表示されます。

```xml
PS C:\> Get-ServiceFabricClusterHealth

AggregatedHealthState   : Warning
UnhealthyEvaluations    :
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.

                              Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                              Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                  Unhealthy event: SourceId='System.PLB',
                          Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                          ConsiderWarningAsError=false.


NodeHealthStates        :
                          NodeName              : _Node_2
                          AggregatedHealthState : Ok

                          NodeName              : _Node_0
                          AggregatedHealthState : Ok

                          NodeName              : _Node_1
                          AggregatedHealthState : Ok

                          NodeName              : _Node_3
                          AggregatedHealthState : Ok

                          NodeName              : _Node_4
                          AggregatedHealthState : Ok

ApplicationHealthStates :
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning

HealthEvents            : None
```

次の PowerShell コマンドレットは、カスタム アプリケーション ポリシーを使用してクラスターの正常性を取得します。 結果はフィルター処理され、エラーまたは警告状態のアプリケーションとノードのみを取得します。 すべて正常であったため、結果としてノードは返されません。 アプリケーションのフィルターが考慮されるのは fabric:/WordCount に対してのみです。 カスタム ポリシーでは fabric:/WordCount アプリケーションに対する警告をエラーと認識するため、アプリケーションとクラスターはエラーとして評価されます。

```powershell
PS c:\> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error"


AggregatedHealthState   : Error
UnhealthyEvaluations    :
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.

                              Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                              Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                  Unhealthy event: SourceId='System.PLB',
                          Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                          ConsiderWarningAsError=true.


NodeHealthStates        : None
ApplicationHealthStates :
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error

HealthEvents            : None

```

### <a name="rest"></a>REST ()
クラスターの正常性を取得するには、[GET 要求](https://msdn.microsoft.com/library/azure/dn707669.aspx)または [POST 要求](https://msdn.microsoft.com/library/azure/dn707696.aspx)を使用できます。これらの要求の本文には、正常性ポリシーが記載されています。

## <a name="get-node-health"></a>ノードの正常性の取得
ノード エンティティの正常性を返し、ノードで報告された正常性のイベントが含まれます。 次の内容を入力します。

* [必須] ノードを識別するノード名。
* [省略可能] 正常性の評価に使用するクラスターの正常性ポリシーの設定。
* [省略可能] 結果で返す対象のエントリ (たとえば、エラーのみ、警告とエラーの両方) を指定するイベントのフィルター。 すべてのイベントは、フィルターに関係なく、エンティティで集計された正常性の評価に使用されます。

### <a name="api"></a>API
API を使用してノードの正常性を取得するには、 `FabricClient` を作成し、HealthManager で [GetNodeHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getnodehealthasync.aspx) メソッドを呼び出します。

次のコードは、指定したノード名のノードの正常性を取得します。

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

次のコードは、指定したノード名のノードの正常性を取得し、 [NodeHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.nodehealthquerydescription.aspx)を通じてイベント フィルターとカスタム ポリシーを渡します。

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
ノードの正常性を取得するコマンドレットは [Get-ServiceFabricNodeHealth](https://msdn.microsoft.com/library/mt125937.aspx)です。 まず、 [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) コマンドレットを使用してクラスターに接続します。
次のコマンドレットは、既定の正常性ポリシーを使用してノードの正常性を取得します。

```powershell
PS C:\> Get-ServiceFabricNodeHealth _Node_1


NodeName              : _Node_1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 6
                        SentAt                : 3/22/2016 7:47:56 PM
                        ReceivedAt            : 3/22/2016 7:48:19 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:48:19 PM, LastWarning = 1/1/0001 12:00:00 AM
```

次のコマンドレットは、クラスター内のすべてのノードの正常性を取得します。

```powershell
PS C:\> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
_Node_2                     Ok
_Node_0                     Ok
_Node_1                     Ok
_Node_3                     Ok
_Node_4                     Ok
```

### <a name="rest"></a>REST ()
ノードの正常性を取得するには、[GET 要求](https://msdn.microsoft.com/library/azure/dn707650.aspx)または [POST 要求](https://msdn.microsoft.com/library/azure/dn707665.aspx)を使用できます。これらの要求の本文には、正常性ポリシーが記載されています。

## <a name="get-application-health"></a>アプリケーションの正常性の取得
アプリケーション エンティティの正常性を返します。 デプロイされたアプリケーションとサービスの子の正常性状態が含まれます。 次の内容を入力します。

* [必須] アプリケーションを識別するアプリケーション名 (URI)。
* [省略可能] アプリケーション マニフェストのポリシーを上書きするために使用するアプリケーションの正常性ポリシー。
* [省略可能] 結果で返す対象のエントリ (たとえば、エラーのみ、警告とエラーの両方) を指定する、イベント、サービス、およびデプロイされたアプリケーションのフィルター。 すべてのイベント、サービス、およびデプロイされたアプリケーションは、フィルターに関係なく、エンティティで集計された正常性の評価に使用されます。

### <a name="api"></a>API
アプリケーションの正常性を取得するには、 `FabricClient` を作成し、HealthManager で [GetApplicationHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getapplicationhealthasync.aspx) メソッドを呼び出します。

次のコードは、指定のアプリケーション名 (URI) のアプリケーションの正常性を取得します。

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

次のコードは、 [ApplicationHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.applicationhealthquerydescription.aspx)を通じて指定したフィルターとカスタム ポリシーを使用して、指定したアプリケーション名 (URI) のアプリケーションの正常性を取得します。

```csharp
HealthStateFilter warningAndErrors = HealthStateFilter.Error | HealthStateFilter.Warning;
var serviceTypePolicy = new ServiceTypeHealthPolicy()
{
    MaxPercentUnhealthyPartitionsPerService = 0,
    MaxPercentUnhealthyReplicasPerPartition = 5,
    MaxPercentUnhealthyServices = 0,
};
var policy = new ApplicationHealthPolicy()
{
    ConsiderWarningAsError = false,
    DefaultServiceTypeHealthPolicy = serviceTypePolicy,
    MaxPercentUnhealthyDeployedApplications = 0,
};

var queryDescription = new ApplicationHealthQueryDescription(applicationName)
{
    HealthPolicy = policy,
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
};

ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
アプリケーションの正常性を取得するコマンドレットは [Get-ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx)です。 まず、 [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) コマンドレットを使用してクラスターに接続します。

次のコマンドレットは、 **fabric:/WordCount** アプリケーションの正常性を返します。

```powershell
PS c:\>
PS C:\WINDOWS\system32>  Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                      Unhealthy event: SourceId='System.PLB',
                                  Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                                  ConsiderWarningAsError=false.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Warning

                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 360
                                  SentAt                : 3/22/2016 7:56:53 PM
                                  ReceivedAt            : 3/22/2016 7:56:53 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 7:56:53 PM, LastWarning = 1/1/0001 12:00:00 AM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Ok
                                  SequenceNumber        : 131031545225930951
                                  SentAt                : 3/22/2016 9:08:42 PM
                                  ReceivedAt            : 3/22/2016 9:08:42 PM
                                  TTL                   : Infinite
                                  Description           : Availability checked successfully, latency ok
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 8:55:39 PM, LastWarning = 1/1/0001 12:00:00 AM
```

次の PowerShell コマンドレットは、カスタム ポリシーを渡します。 また、子とイベントをフィルター処理します。

```powershell
PS C:\> Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesFilter Error -EventsFilter Error -DeployedApplicationsFilter Error

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy event: SourceId='System.PLB',
                                  Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                                  ConsiderWarningAsError=true.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

DeployedApplicationHealthStates : None
HealthEvents                    : None
```

### <a name="rest"></a>REST ()
アプリケーションの正常性を取得するには、[GET 要求](https://msdn.microsoft.com/library/azure/dn707681.aspx)または [POST 要求](https://msdn.microsoft.com/library/azure/dn707643.aspx)を使用できます。これらの要求の本文には、正常性ポリシーが記載されています。

## <a name="get-service-health"></a>サービスの正常性の取得
サービス エンティティの正常性を返します。 パーティションの正常性状態が含まれます。 次の内容を入力します。

* [必須] サービスを識別するサービス名 (URI)
* [省略可能] アプリケーション マニフェストのポリシーを上書きするために使用するアプリケーションの正常性ポリシー。
* [省略可能] 結果で返す対象のエントリ (たとえば、エラーのみ、警告とエラーの両方) を指定するイベントおよびパーティションのフィルター。 すべてのイベントおよびパーティションは、フィルターに関係なく、エンティティで集計された正常性の評価に使用されます。

### <a name="api"></a>API
API を使用してサービスの正常性を取得するには、 `FabricClient` を作成し、HealthManager で [GetServiceHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getservicehealthasync.aspx) メソッドを呼び出します。

次の例は、指定のサービス名 (URI) のサービスの正常性を取得します。

```charp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

次のコードは、 [ServiceHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.servicehealthquerydescription.aspx)を通じてフィルターとカスタム ポリシーを指定し、指定したサービス名 (URI) のサービスの正常性を取得します。

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
サービスの正常性を取得するコマンドレットは [Get-ServiceFabricServiceHealth](https://msdn.microsoft.com/library/mt125984.aspx)です。 まず、 [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) コマンドレットを使用してクラスターに接続します。

次のコマンドレットは、既定の正常性ポリシーを使用してサービスの正常性を取得します。

```powershell
PS C:\> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCountService


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
                        SequenceNumber        : 131031547693687021
                        SentAt                : 3/22/2016 9:12:49 PM
                        ReceivedAt            : 3/22/2016 9:12:49 PM
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
```

### <a name="rest"></a>REST ()
サービスの正常性を取得するには、[GET 要求](https://msdn.microsoft.com/library/azure/dn707609.aspx)または [POST 要求](https://msdn.microsoft.com/library/azure/dn707646.aspx)を使用できます。これらの要求の本文には、正常性ポリシーが記載されています。

## <a name="get-partition-health"></a>パーティションの正常性の取得
パーティション エンティティの正常性を返します。 レプリカの正常性状態が含まれます。 次の内容を入力します。

* [必須] パーティションを識別するパーティション ID (GUID)。
* [省略可能] アプリケーション マニフェストのポリシーを上書きするために使用するアプリケーションの正常性ポリシー。
* [省略可能] 結果で返す対象のエントリ (たとえば、エラーのみ、警告とエラーの両方) を指定するイベントおよびレプリカのフィルター。 すべてのイベントおよびレプリカは、フィルターに関係なく、エンティティで集計された正常性の評価に使用されます。

### <a name="api"></a>API
API を使用してパーティションの正常性を取得するには、 `FabricClient` を作成し、HealthManager で [GetPartitionHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getpartitionhealthasync.aspx) メソッドを呼び出します。 省略可能なパラメーターを指定するには、 [PartitionHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.partitionhealthquerydescription.aspx)を作成します。

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### <a name="powershell"></a>PowerShell
パーティションの正常性を取得するコマンドレットは [Get-ServiceFabricPartitionHealth](https://msdn.microsoft.com/library/mt125869.aspx)です。 まず、 [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) コマンドレットを使用してクラスターに接続します。

次のコマンドレットは、 **fabric:/WordCount/WordCountService** サービスの全パーティションの正常性を取得します。

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth


PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 131031502143040223
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844060
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844059
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844061
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844058
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 76
                        SentAt                : 3/22/2016 7:57:26 PM
                        ReceivedAt            : 3/22/2016 7:57:48 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 3/22/2016 7:57:48 PM, LastOk = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST ()
パーティションの正常性を取得するには、[GET 要求](https://msdn.microsoft.com/library/azure/dn707683.aspx)または [POST 要求](https://msdn.microsoft.com/library/azure/dn707680.aspx)を使用できます。これらの要求の本文には、正常性ポリシーが記載されています。

## <a name="get-replica-health"></a>レプリカの正常性の取得
ステートフル サービス レプリカまたはステートレス サービス インスタンスの正常性を返します。 次の内容を入力します。

* [必須] レプリカを識別するパーティション ID (GUID) とレプリカ ID。
* [省略可能] アプリケーション マニフェストのポリシーを上書きするために使用するアプリケーションの正常性ポリシーのパラメーター。
* [省略可能] 結果で返す対象のエントリ (たとえば、エラーのみ、警告とエラーの両方) を指定するイベントのフィルター。 すべてのイベントは、フィルターに関係なく、エンティティで集計された正常性の評価に使用されます。

### <a name="api"></a>API
API を使用してレプリカの正常性を取得するには、 `FabricClient` を作成し、HealthManager で [GetReplicaHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getreplicahealthasync.aspx) メソッドを呼び出します。 詳細なパラメーターを指定するには、 [ReplicaHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.replicahealthquerydescription.aspx)を使用します。

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### <a name="powershell"></a>PowerShell
レプリカの正常性を取得するコマンドレットは [Get-ServiceFabricReplicaHealth](https://msdn.microsoft.com/library/mt125808.aspx)です。 まず、 [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) コマンドレットを使用してクラスターに接続します。

次のコマンドレットは、サービスのすべてのパーティションのプライマリ レプリカの正常性を取得します。

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth


PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
ReplicaId             : 131031502143040223
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131031502145556748
                        SentAt                : 3/22/2016 7:56:54 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST ()
レプリカの正常性を取得するには、[GET 要求](https://msdn.microsoft.com/library/azure/dn707673.aspx)または [POST 要求](https://msdn.microsoft.com/library/azure/dn707641.aspx)を使用できます。これらの要求の本文には、正常性ポリシーが記載されています。

## <a name="get-deployed-application-health"></a>デプロイされたアプリケーションの正常性の取得
ノード エンティティにデプロイされたアプリケーションの正常性を返します。 デプロイされたサービス パッケージの正常性状態が含まれます。 次の内容を入力します。

* [必須] デプロイされたアプリケーションを識別するアプリケーション名 (URI) とノード名 (文字列)。
* [省略可能] アプリケーション マニフェストのポリシーを上書きするために使用するアプリケーションの正常性ポリシー。
* [省略可能] 結果で返す対象のエントリ (たとえば、エラーのみ、警告とエラーの両方) を指定する、イベントおよびデプロイされたサービス パッケージのフィルター。 すべてのイベントおよびデプロイされたサービス パッケージは、フィルターに関係なく、エンティティで集計された正常性の評価に使用されます。

### <a name="api"></a>API
API を使用してノードにデプロイされたアプリケーションの正常性を取得するには、 `FabricClient` を作成し、HealthManager で [GetDeployedApplicationHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync.aspx) メソッドを呼び出します。 省略可能なパラメーターを指定するには、 [DeployedApplicationHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.deployedapplicationhealthquerydescription.aspx)を使用します。

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### <a name="powershell"></a>PowerShell
デプロイされたアプリケーションの正常性を取得するコマンドレットは [Get-ServiceFabricDeployedApplicationHealth](https://msdn.microsoft.com/library/mt163523.aspx)です。 まず、 [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) コマンドレットを使用してクラスターに接続します。 アプリケーションのデプロイ先を探すには、 [Get-ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx) を実行して、デプロイされたアプリケーションの子を確認します。

次のコマンドレットは、**_Node_2** にデプロイされた **fabric:/WordCount** アプリケーションの正常性を取得します。

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName _Node_2


ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_2
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCountServicePkg
                                     NodeName              : _Node_2
                                     AggregatedHealthState : Ok

                                     ServiceManifestName   : WordCountWebServicePkg
                                     NodeName              : _Node_2
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131031502143710698
                                     SentAt                : 3/22/2016 7:56:54 PM
                                     ReceivedAt            : 3/22/2016 7:57:12 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST ()
デプロイされたアプリケーションの正常性を取得するには、[GET 要求](https://msdn.microsoft.com/library/azure/dn707644.aspx)または [POST 要求](https://msdn.microsoft.com/library/azure/dn707688.aspx)を使用できます。これらの要求の本文には、正常性ポリシーが記載されています。

## <a name="get-deployed-service-package-health"></a>デプロイされたサービス パッケージの正常性の取得
デプロイされたサービス パッケージ エンティティの正常性を返します。 次の内容を入力します。

* [必須] デプロイされたサービス パッケージを識別するアプリケーション名 (URI)、ノード名 (文字列)、サービス マニフェスト名 (文字列)。
* [省略可能] アプリケーション マニフェストのポリシーを上書きするために使用するアプリケーションの正常性ポリシー。
* [省略可能] 結果で返す対象のエントリ (たとえば、エラーのみ、警告とエラーの両方) を指定するイベントのフィルター。 すべてのイベントは、フィルターに関係なく、エンティティで集計された正常性の評価に使用されます。

### <a name="api"></a>API
API を使用してデプロイされたサービス パッケージの正常性を取得するには、 `FabricClient` を作成し、HealthManager で [GetDeployedServicePackageHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync.aspx) メソッドを呼び出します。 省略可能なパラメーターを指定するには、 [DeployedServicePackageHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.deployedservicepackagehealthquerydescription.aspx)を使用します。

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### <a name="powershell"></a>PowerShell
デプロイされたサービス パッケージの正常性を取得するコマンドレットは [Get-ServiceFabricDeployedServicePackageHealth](https://msdn.microsoft.com/library/mt163525.aspx)です。 まず、 [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) コマンドレットを使用してクラスターに接続します。 アプリケーションのデプロイ先を確認するには、 [Get-ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx) を実行して、デプロイされたアプリケーションを確認します。 アプリケーション内のサービス パッケージを確認するには、 [Get-ServiceFabricDeployedApplicationHealth](https://msdn.microsoft.com/library/mt163523.aspx) の出力で、デプロイされたサービス パッケージの子を確認します。

次のコマンドレットは、**_Node_2** にデプロイされた **fabric:/WordCount** アプリケーションの **WordCountServicePkg** サービス パッケージの正常性を取得します。 エンティティには、アクティブ化に成功したサービス パッケージとエントリー ポイント、および登録に成功したサービス型に関する **System.Hosting** のレポートが含まれています。

```powershell
PS C:\> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName _Node_2 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCountServicePkg


ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCountServicePkg
NodeName              : _Node_2
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 131031502301306211
                        SentAt                : 3/22/2016 7:57:10 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 131031502301568982
                        SentAt                : 3/22/2016 7:57:10 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCountServiceType
                        HealthState           : Ok
                        SequenceNumber        : 131031502314788519
                        SentAt                : 3/22/2016 7:57:11 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST ()
デプロイされたサービス パッケージの正常性を取得するには、[GET 要求](https://msdn.microsoft.com/library/azure/dn707677.aspx)または [POST 要求](https://msdn.microsoft.com/library/azure/dn707689.aspx)を使用できます。これらの要求の本文には、正常性ポリシーが記載されています。

## <a name="health-chunk-queries"></a>正常性チャンク クエリ
正常性チャンク クエリは、入力フィルターごとにクラスターの複数レベルの子を (再帰的に) 返すことができます。 このクエリでサポートされている高度なフィルターでは、一意の識別子または他のグループ識別子や正常性状態で識別して、どの子を返すかを柔軟に記述できます。 最初のレベルの子が常に含まれている正常性コマンドとは異なり、既定ではどの子も含まれていません。

[正常性クエリ](service-fabric-view-entities-aggregated-health.md#health-queries)は、要求されたフィルターごとに、指定されたエンティティの最初のレベルの子のみを返します。 子の子を取得するには、対象のエンティティごとに追加の正常性 API を呼び出す必要があります。 同様に、特定のエンティティの正常性を取得するには、目的の各エンティティごとに正常性 API を呼び出す必要があります。 チャンク クエリの高度なフィルターを使用することで、対象とする複数の項目を 1 つのクエリで要求できます。これにより、メッセージ サイズとメッセージ数を最小限に抑えられます。

チャンク クエリの真価は、一度の呼び出しでより多くのクラスター エンティティの正常性状態を取得できるところにあります (要求されたルートを起点とするすべてのクラスター エンティティの状態を取得可能です)。 次のような複雑な正常性クエリを記述できます。

* エラーがあるアプリケーションのみを返す。その際、これらのアプリケーションについて警告やエラーがあるすべてのサービスを含める。 また、返されるサービスについて、すべてのパーティションを含める。
* 名前で指定された 4 つのアプリケーションの正常性のみを返す。
* 目的のアプリケーションの種類に属するアプリケーションの正常性のみを返す。
* ノードにデプロイされたすべてのエンティティを返す。 すべてのアプリケーション、指定のノードにデプロイされたすべてのアプリケーション、そのノードにデプロイされたすべてのサービス パッケージが返されます。
* エラーがあるすべてのレプリカを返す。 すべてのアプリケーション、サービス、パーティションのほか、エラーのあるレプリカが返されます。
* すべてのアプリケーションを返す。 指定したサービスについて、すべてのパーティションを含める。

現時点では、正常性チャンク クエリはクラスター エンティティについてのみ公開されています。 このクエリが返すクラスターの正常性チャンクには、次のものが含まれます。

* クラスターの正常性状態の集計。
* 入力フィルターに該当するノードの正常性状態チャンクの一覧。
* 入力フィルターに該当するアプリケーションの正常性状態チャンクの一覧。 アプリケーションの各正常性状態チャンクには、入力フィルターの条件に該当するすべてのサービスが列挙されたチャンクの一覧のほか、フィルターに該当するすべてのデプロイ済みアプリケーションが列挙されたチャンクの一覧が含まれています。 サービスとデプロイ済みアプリケーションの子についても同様です。 このように、クラスター内のすべてのエンティティは、要求に応じて階層的な方法で返すことができます。

### <a name="cluster-health-chunk-query"></a>クラスターの正常性チャンク クエリ
クラスター エンティティの正常性を返します。これには、要求された子の階層的な正常性状態チャンクが含まれます。 次の内容を入力します。

* [省略可能] ノードとクラスターのイベントを評価するために使用されるクラスターの正常性ポリシー。
* [省略可能] アプリケーション マニフェストのポリシーを上書きするために使用するアプリケーションの正常性ポリシー マップと正常性ポリシー。
* [省略可能] 結果で返される対象のエントリを指定するノードとアプリケーションのフィルター。 フィルターは、複数のエンティティのうちの 1 つのエンティティまたはグループのみか、そのレベルのすべてのエンティティに適用されます。 フィルターの一覧には、一般的なフィルターを 1 つだけ含めることができるほか、特定の識別子に対応するフィルターを含めて、クエリによって返されるエンティティを絞り込むこともできます。 空の場合、既定では子は返されません。
  フィルターの詳細については、[NodeHealthStateFilter](https://msdn.microsoft.com/library/azure/system.fabric.health.nodehealthstatefilter.aspx) と [ApplicationHealthStateFilter](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthstatefilter.aspx) に関するページを参照してください。 アプリケーションのフィルターでは、子に対する高度なフィルターを再帰的に指定できます。

チャンクの結果には、フィルターに該当する子が含まれます。

現時点では、チャンク クエリは異常性の評価またはエンティティ イベントを返しません。 この追加情報は、既存のクラスター正常性クエリを使用して取得できます。

### <a name="api"></a>API
クラスターの正常性チャンクを取得するには、 `FabricClient` を作成し、 [HealthManager](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync.aspx) で **GetClusterHealthChunkAsync**メソッドを呼び出します。 [ClusterHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.clusterhealthchunkquerydescription.aspx) を渡して、正常性ポリシーと高度なフィルターを記述できます。

次のコードは、高度なフィルターを使用してクラスターの正常性チャンクを取得します。

```csharp
var queryDescription = new ClusterHealthChunkQueryDescription();
queryDescription.ApplicationFilters.Add(new ApplicationHealthStateFilter()
    {
        // Return applications only if they are in error
        HealthStateFilter = HealthStateFilter.Error
    });

// Return all replicas
var wordCountServiceReplicaFilter = new ReplicaHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };

// Return all replicas and all partitions
var wordCountServicePartitionFilter = new PartitionHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };
wordCountServicePartitionFilter.ReplicaFilters.Add(wordCountServiceReplicaFilter);

// For specific service, return all partitions and all replicas
var wordCountServiceFilter = new ServiceHealthStateFilter()
{
    ServiceNameFilter = new Uri("fabric:/WordCount/WordCountService"),
};
wordCountServiceFilter.PartitionFilters.Add(wordCountServicePartitionFilter);

// Application filter: for specific application, return no services except the ones of interest
var wordCountApplicationFilter = new ApplicationHealthStateFilter()
    {
        // Always return fabric:/WordCount application
        ApplicationNameFilter = new Uri("fabric:/WordCount"),
    };
wordCountApplicationFilter.ServiceFilters.Add(wordCountServiceFilter);

queryDescription.ApplicationFilters.Add(wordCountApplicationFilter);

var result = await fabricClient.HealthManager.GetClusterHealthChunkAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
クラスターの正常性を取得するコマンドレットは [Get-ServiceFabricClusterChunkHealth](https://msdn.microsoft.com/library/mt644772.aspx)です。 まず、 [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) コマンドレットを使用してクラスターに接続します。

次のコードは、常に返される特定のノードを除き、状態がエラーのノードのみを取得します。

```xml
PS C:\> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$nodeFilter1 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{HealthStateFilter=$errorFilter}
$nodeFilter2 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{NodeNameFilter="_Node_1";HealthStateFilter=$allFilter}
# Create node filter list that will be passed in the cmdlet
$nodeFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.NodeHealthStateFilter]
$nodeFilters.Add($nodeFilter1)
$nodeFilters.Add($nodeFilter2)

Get-ServiceFabricClusterHealthChunk -NodeFilters $nodeFilters

HealthState                  : Error
NodeHealthStateChunks        :
                               TotalCount            : 1

                               NodeName              : _Node_1
                               HealthState           : Ok

ApplicationHealthStateChunks : None
```

次のコマンドレットは、アプリケーションのフィルターを使用してクラスターのチャンクを取得します。

```xml
$errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

# All replicas
$replicaFilter = New-Object System.Fabric.Health.ReplicaHealthStateFilter -Property @{HealthStateFilter=$allFilter}

# All partitions
$partitionFilter = New-Object System.Fabric.Health.PartitionHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$partitionFilter.ReplicaFilters.Add($replicaFilter)

# For WordCountService, return all partitions and all replicas
$svcFilter1 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{ServiceNameFilter="fabric:/WordCount/WordCountService"}
$svcFilter1.PartitionFilters.Add($partitionFilter)

$svcFilter2 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{HealthStateFilter=$errorFilter}

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{ApplicationNameFilter="fabric:/WordCount"}
$appFilter.ServiceFilters.Add($svcFilter1)
$appFilter.ServiceFilters.Add($svcFilter2)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)

Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters

HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks :
                               TotalCount            : 1

                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               ServiceHealthStateChunks :
                                   TotalCount            : 1

                                   ServiceName           : fabric:/WordCount/WordCountService
                                   HealthState           : Error
                                   PartitionHealthStateChunks :
                                       TotalCount            : 1

                                       PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
                                       HealthState           : Error
                                       ReplicaHealthStateChunks :
                                           TotalCount            : 5

                                           ReplicaOrInstanceId   : 131031502143040223
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844060
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844059
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844061
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844058
                                           HealthState           : Error
```

次のコマンドレットは、ノードにデプロイされたすべてのエンティティを返します。

```xml
$errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$dspFilter = New-Object System.Fabric.Health.DeployedServicePackageHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$daFilter =  New-Object System.Fabric.Health.DeployedApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter;NodeNameFilter="_Node_2"}
$daFilter.DeployedServicePackageFilters.Add($dspFilter)

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$appFilter.DeployedApplicationFilters.Add($daFilter)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)
Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks :
                               TotalCount            : 2

                               ApplicationName       : fabric:/System
                               HealthState           : Ok
                               DeployedApplicationHealthStateChunks :
                                   TotalCount            : 1

                                   NodeName              : _Node_2
                                   HealthState           : Ok
                                   DeployedServicePackageHealthStateChunks :
                                       TotalCount            : 1

                                       ServiceManifestName   : FAS
                                       HealthState           : Ok



                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               DeployedApplicationHealthStateChunks :
                                   TotalCount            : 1

                                   NodeName              : _Node_2
                                   HealthState           : Ok
                                   DeployedServicePackageHealthStateChunks :
                                       TotalCount            : 2

                                       ServiceManifestName   : WordCountServicePkg
                                       HealthState           : Ok

                                       ServiceManifestName   : WordCountWebServicePkg
                                       HealthState           : Ok
```

### <a name="rest"></a>REST ()
クラスターの正常性チャンクを取得するには、[GET 要求](https://msdn.microsoft.com/library/azure/mt656722.aspx)または [POST 要求](https://msdn.microsoft.com/library/azure/mt656721.aspx)を使用できます。これらの要求の本文には、正常性ポリシーと高度なフィルターが記載されています。

## <a name="general-queries"></a>一般クエリ
一般クエリは、指定した型の Service Fabric エンティティの一覧を返します。 API ( **FabricClient.QueryManager**のメソッド)、PowerShell コマンドレット、REST を通じて公開されます。 これらのクエリは複数のコンポーネントからのサブクエリを集計します。 そのうちの 1 つが [正常性ストア](service-fabric-health-introduction.md#health-store)で、各クエリ結果の正常性状態の集計を設定します。  

> [!NOTE]
> 一般クエリはエンティティの正常性状態の集計を返し、正常性に関する詳細なデータは含まれません。 エンティティが正常でない場合は、正常性クエリを実行してイベント、子の正常性状態、異常性の評価などの正常性に関するすべての情報を取得できます。
>
>

エンティティについて一般クエリで不明な正常性状態が返された場合、エンティティに関するすべてのデータが正常性ストアに揃っていない可能性があります。 また、正常性ストアに対するサブクエリが失敗した可能性もあります (たとえば、通信エラーがあったか、正常性ストアが制限されていた)。 引き続きエンティティに対して正常性クエリを実行します。 サブクエリで一時的なエラーが発生していた (ネットワークの問題など) 場合、このフォローアップ クエリは成功することがあります。 また、エンティティが公開されなかった理由の詳細を正常性ストアから確認することもできます。

エンティティの **HealthState** が含まれるクエリは次のとおりです。

* ノード一覧: クラスター内のノードの一覧を返します (ページング)。
  * API: [FabricClient.QueryClient.GetNodeListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getnodelistasync.aspx)
  * PowerShell: Get-ServiceFabricNode
* アプリケーション一覧: クラスター内のアプリケーションの一覧を返します (ページング)。
  * API: [FabricClient.QueryClient.GetApplicationListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getapplicationlistasync.aspx)
  * PowerShell: Get-ServiceFabricApplication
* サービス一覧: アプリケーション内のサービスの一覧を返します (ページング)。
  * API: [FabricClient.QueryClient.GetServiceListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getservicelistasync.aspx)
  * PowerShell: Get-ServiceFabricService
* パーティション一覧: サービス内のパーティションの一覧を返します (ページング)。
  * API: [FabricClient.QueryClient.GetPartitionListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getpartitionlistasync.aspx)
  * PowerShell: Get-ServiceFabricPartition
* レプリカ一覧: パーティション内のレプリカの一覧を返します (ページング)。
  * API: [FabricClient.QueryClient.GetReplicaListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getreplicalistasync.aspx)
  * PowerShell: Get-ServiceFabricReplica
* デプロイ済みアプリケーション一覧: ノード上にデプロイされたアプリケーションの一覧を返します。
  * API: [FabricClient.QueryClient.GetDeployedApplicationListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync.aspx)
  * PowerShell: Get-ServiceFabricDeployedApplication
* デプロイ済みサービス パッケージ一覧: デプロイされたアプリケーション内のサービス パッケージの一覧を返します。
  * API: [FabricClient.QueryClient.GetDeployedServicePackageListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync.aspx)
  * PowerShell: Get-ServiceFabricDeployedApplication

> [!NOTE]
> 一部のクエリはページングされた結果を返します。 これらのクエリの戻り値は、[PagedList<T>](https://msdn.microsoft.com/library/azure/mt280056.aspx) から派生した一覧です。 結果がメッセージに収まらない場合、1 ページのみが返されます。また、列挙が停止した場所を追跡する ContinuationToken も返されます。 同じクエリの呼び出しを続け、次の結果を得るために前のクエリから継続トークンを渡す必要があります。
>
>

### <a name="examples"></a>例
次のコードは、クラスター内の正常でないアプリケーションを取得します。

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

次のコマンドレットは、fabric:/WordCount アプリケーションの詳細を取得します。 正常性状態は警告になっています。

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "WordCountWebService_InstanceCount" = "1";
                         "_WFDebugParams_" = "[{"ServiceManifestName":"WordCountWebServicePkg","CodePackageName":"Code","EntryPointType":"Main","Debug
                         ExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {74f7e5d5-71a9-47e2-a8cd-1878ec4734f1} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"},{"ServiceManifestName":"WordCountServicePkg","CodeP
                         ackageName":"Code","EntryPointType":"Main","DebugExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {2ab462e6-e0d1-4fda-a844-972f561fe751} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"}]" }
```

次のコマンドレットは、正常性状態が警告になっているサービスを取得します。

```powershell
PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Warning"}


ServiceName            : fabric:/WordCount/WordCountService
ServiceKind            : Stateful
ServiceTypeName        : WordCountServiceType
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Warning
```

## <a name="cluster-and-application-upgrades"></a>クラスターとアプリケーションのアップグレード
Service Fabric はクラスターとアプリケーションのアップグレードを監視して正常性を確認し、正常な状態の維持に努めます。 構成された正常性ポリシーを使用して評価したときにエンティティに異常が見つかった場合、アップグレードではアップグレード固有のポリシーを適用して次のアクションが決定されます。 ユーザーが操作 (エラー状態の修正やポリシーの変更など) できるようにアップグレードが一時停止されるか、前の正常なバージョンに自動的にロールバックされる場合があります。

" *クラスター* " のアップグレード中には、クラスターのアップグレード ステータスを取得できます。 アップグレード ステータスには、クラスター内の異常個所を示す異常性の評価が含まれます。 正常性の問題によりアップグレードがロールバックされると、アップグレード ステータスに最後に問題が発生した理由が記憶されます。 この情報は、アップグレードがロールバックまたは停止された後で、管理者が問題点を調査する際に役立ちます。

同様に、" *アプリケーション* " のアップグレード中には、アプリケーションのアップグレード ステータスに異常性の評価が記録されます。

次のコードは、修正された fabric:/WordCount アプリケーションのアップグレード ステータスを示します。 ウォッチドッグによってレプリカの 1 つでエラーが報告されています。 正常性チェックは考慮されないため、アップグレードはロールバックされます。

```powershell
PS C:\> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

ApplicationName               : fabric:/WordCount
ApplicationTypeName           : WordCount
TargetApplicationTypeVersion  : 1.0.0.0
ApplicationParameters         : {}
StartTimestampUtc             : 4/21/2015 5:23:26 PM
FailureTimestampUtc           : 4/21/2015 5:23:37 PM
FailureReason                 : HealthCheck
UpgradeState                  : RollingBackInProgress
UpgradeDuration               : 00:00:23
CurrentUpgradeDomainDuration  : 00:00:00
CurrentUpgradeDomainProgress  : UD1

                                NodeName            : _Node_1
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_2
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                      Unhealthy partition: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b', AggregatedHealthState='Error'.

                                          Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                          Unhealthy replica: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b',
                                  ReplicaOrInstanceId='131031502346844058', AggregatedHealthState='Error'.

                                              Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

詳細については、「 [Service Fabric アプリケーションのアップグレード](service-fabric-application-upgrade.md)」をご覧ください。

## <a name="use-health-evaluations-to-troubleshoot"></a>正常性の評価を使用したトラブルシューティング
クラスターやアプリケーションで問題が発生した場合は必ず、そのクラスターやアプリケーションの正常性を確認して問題を特定します。 異常性の評価では、現在の異常な状態をトリガーした原因の詳細を確認できます。 必要に応じて、異常が発生している子エンティティにまで掘り下げて根本原因を特定できます。

> [!NOTE]
> 異常性の評価には、エンティティが現在の正常性状態であると評価された第一の理由が示されます。 この状態をトリガーする複数のイベントが他に存在する場合がありますが、それらのイベントは評価には反映されません。 より詳しい情報を得るには、正常性エンティティを詳しく調べ、クラスター内の異常性レポートをすべて確認します。
>
>

## <a name="next-steps"></a>次のステップ
[システム正常性レポートを使用したトラブルシューティング](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Service Fabric のカスタム正常性レポートの追加](service-fabric-report-health.md)

[サービス正常性のレポートとチェックの方法](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[ローカルでのサービスの監視と診断](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric アプリケーションのアップグレード](service-fabric-application-upgrade.md)



<!--HONumber=Jan17_HO2-->


