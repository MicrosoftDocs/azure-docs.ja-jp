---
title: Azure 内で Service Fabric クラスターを監視する | Microsoft Docs
description: このチュートリアルでは、クラスターの監視方法を学習するために、Service Fabric イベントの表示、EventStore API シリーズのクエリの実行、パフォーマンス カウンターの監視、および正常性レポートの表示を行います。
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: srrengar
ms.custom: mvc
ms.openlocfilehash: 63069dcdebf19c64d7bcde298fa234622a6d9a2b
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385282"
---
# <a name="tutorial-monitor-a-service-fabric-cluster-in-azure"></a>チュートリアル:Azure 内で Service Fabric クラスターを監視する

監視と診断は、あらゆるクラウド環境でアプリケーションやサービスを開発、テスト、およびデプロイするために非常に重要です。 このチュートリアルは、シリーズの第 2 部です。イベント、パフォーマンス カウンター、および正常性レポートを使用して Service Fabric クラスターを監視し、診断する方法を説明します。   詳細については、[クラスターの監視](service-fabric-diagnostics-overview.md#platform-cluster-monitoring)と[インフラストラクチャの監視](service-fabric-diagnostics-overview.md#infrastructure-performance-monitoring)に関する概要を参照してください。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Service Fabric イベントの表示
> * クラスター イベントに対して EventStore API のクエリを実行する
> * インフラストラクチャの監視/パフォーマンス カウンターの収集を行う
> * クラスターの正常性レポートを表示する

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * テンプレートを使用して Azure にセキュリティで保護された [Windows クラスター](service-fabric-tutorial-create-vnet-and-windows-cluster.md)を作成する
> * クラスターを監視する
> * [クラスターをスケールインまたはスケールアウトする](service-fabric-tutorial-scale-cluster.md)
> * [クラスターのランタイムをアップグレードする](service-fabric-tutorial-upgrade-cluster.md)
> * [クラスターの削除](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* Azure サブスクリプションを持っていない場合は[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成する
* [Azure Powershell](https://docs.microsoft.com/powershell/azure/install-Az-ps) または [Azure CLI](/cli/azure/install-azure-cli) をインストールする。
* セキュリティで保護された [Windows クラスター](service-fabric-tutorial-create-vnet-and-windows-cluster.md)を作成する 
* クラスターに対して[診断の収集](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configurediagnostics_anchor)を構成する
* クラスター内で [EventStore サービス](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureeventstore_anchor)を有効にする
* クラスターに対して [Azure Monitor ログと Log Analytics エージェント](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureloganalytics_anchor)を構成する

## <a name="view-service-fabric-events-using-azure-monitor-logs"></a>Azure Monitor ログを使用して Service Fabric イベントを表示する

Azure Monitor ログでは、クラウド内でホストされているアプリケーションとサービスからテレメトリが収集および分析され、可用性とパフォーマンスを最大限に高めることができるように分析ツールが提供されます。 Azure Monitor ログ内でクエリを実行して、分析情報を取得したり、クラスター内の処理のトラブルシューティングを行ったりできます。

Service Fabric Analytics ソリューションにアクセスするには、[Azure portal](https://portal.azure.com) に移動し、Service Fabric Analytics ソリューションを作成したリソース グループを選択します。

リソースの **[ServiceFabric(mysfomsworkspace)]** を選択します。

**[概要]** に、有効になっているソリューションごとのグラフ形式のタイルが表示されます (Service Fabric のタイルも含まれています)。 **[Service Fabric]** グラフをクリックして、Service Fabric Analytics ソリューションを継続します。

![Service Fabric ソリューション](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-summary.png)

次の画像には、Service Fabric Analytics ソリューションのホーム ページが示されています。 このホーム ページでは、クラスター内で行われている処理のスナップショット ビューが提供されます。

![Service Fabric ソリューション](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-solution.png)

 クラスター作成時に診断を有効にした場合は、以下に対するイベントが表示されます。 

* [Service Fabric クラスター イベント](service-fabric-diagnostics-event-generation-operational.md)
* [Reliable Actors プログラミング モデル イベント](service-fabric-reliable-actors-diagnostics.md)
* [Reliable Services プログラミング モデル イベント](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>[診断の拡張機能の構成を更新する](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)ことで、すぐに利用できる Service Fabric イベントだけでなく、より詳細なシステム イベントが収集できます。

### <a name="view-service-fabric-events-including-actions-on-nodes"></a>ノードに対する操作を含む Service Fabric イベントを表示する

Service Fabric Analytics ページで、**クラスター イベント**のグラフをクリックします。  収集済みのすべてのシステム イベントのログが表示されます。 参考までに、これらは Azure ストレージ アカウント内の **WADServiceFabricSystemEventsTable** に由来し、次に確認する Reliable Services イベントや Reliable Actors イベントも同様に、それぞれのテーブルに由来します。
    
![クエリの操作チャネル](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-events.png)

クエリでは、Kusto クエリ言語を使用します。これを変更して、探しているものを絞り込むことができます。 たとえば、クラスター内のノードに対して行われたすべての操作を検索するは、次のクエリを使用できます。 使用されているイベント ID は、[操作チャネルのイベント リファレンス](service-fabric-diagnostics-event-generation-operational.md)で確認できます。

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Kusto クエリ言語は優れています。 他にも便利なクエリをいくつか紹介します。

エイリアス ServiceFabricEvent を使用してクエリを関数として保存することで、*ServiceFabricEvent* ルックアップ テーブルをユーザー定義関数として作成します。

```kusto
let ServiceFabricEvent = datatable(EventId: int, EventName: string)
[
    ...
    18603, 'NodeUpOperational',
    18604, 'NodeDownOperational',
    ...
];
ServiceFabricEvent
```

過去 1 時間に記録された操作イベントを返します。
```kusto
ServiceFabricOperationalEvent
| where TimeGenerated > ago(1h)
| join kind=leftouter ServiceFabricEvent on EventId
| project EventId, EventName, TaskName, Computer, ApplicationName, EventMessage, TimeGenerated
| sort by TimeGenerated
```

EventId == 18604 および EventName == 'NodeDownOperational' の操作イベントを返します。
```kusto
ServiceFabricOperationalEvent
| where EventId == 18604
| project EventId, EventName = 'NodeDownOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
```

EventId == 18604 および EventName == 'NodeUpOperational' の操作イベントを返します。
```kusto
ServiceFabricOperationalEvent
| where EventId == 18603
| project EventId, EventName = 'NodeUpOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
``` 
 
HealthState == 3 (エラー) の正常性レポートを返し、EventMessage フィールドから追加のプロパティを抽出します。

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| extend HealthStateId = extract(@"HealthState=(\S+) ", 1, EventMessage, typeof(int))
| where TaskName == 'HM' and HealthStateId == 3
| extend SourceId = extract(@"SourceId=(\S+) ", 1, EventMessage, typeof(string)),
         Property = extract(@"Property=(\S+) ", 1, EventMessage, typeof(string)),
         HealthState = case(HealthStateId == 0, 'Invalid', HealthStateId == 1, 'Ok', HealthStateId == 2, 'Warning', HealthStateId == 3, 'Error', 'Unknown'),
         TTL = extract(@"TTL=(\S+) ", 1, EventMessage, typeof(string)),
         SequenceNumber = extract(@"SequenceNumber=(\S+) ", 1, EventMessage, typeof(string)),
         Description = extract(@"Description='([\S\s, ^']+)' ", 1, EventMessage, typeof(string)),
         RemoveWhenExpired = extract(@"RemoveWhenExpired=(\S+) ", 1, EventMessage, typeof(bool)),
         SourceUTCTimestamp = extract(@"SourceUTCTimestamp=(\S+)", 1, EventMessage, typeof(datetime)),
         ApplicationName = extract(@"ApplicationName=(\S+) ", 1, EventMessage, typeof(string)),
         ServiceManifest = extract(@"ServiceManifest=(\S+) ", 1, EventMessage, typeof(string)),
         InstanceId = extract(@"InstanceId=(\S+) ", 1, EventMessage, typeof(string)),
         ServicePackageActivationId = extract(@"ServicePackageActivationId=(\S+) ", 1, EventMessage, typeof(string)),
         NodeName = extract(@"NodeName=(\S+) ", 1, EventMessage, typeof(string)),
         Partition = extract(@"Partition=(\S+) ", 1, EventMessage, typeof(string)),
         StatelessInstance = extract(@"StatelessInstance=(\S+) ", 1, EventMessage, typeof(string)),
         StatefulReplica = extract(@"StatefulReplica=(\S+) ", 1, EventMessage, typeof(string))
```

EventId != 17523 のイベントの時間グラフを返します。

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| where EventId != 17523
| summarize Count = count() by Timestamp = bin(TimeGenerated, 1h), strcat(tostring(EventId), " - ", case(EventName != "", EventName, "Unknown"))
| render timechart 
```

特定のサービスとノードで集計された Service Fabric の操作イベントを取得します。

```kusto
ServiceFabricOperationalEvent
| where ApplicationName  != "" and ServiceName != ""
| summarize AggregatedValue = count() by ApplicationName, ServiceName, Computer 
```

クロスリソース クエリを使用して、EventId/EventName 別に Service Fabric イベントの数を表示します。

```kusto
app('PlunkoServiceFabricCluster').traces
| where customDimensions.ProviderName == 'Microsoft-ServiceFabric'
| extend EventId = toint(customDimensions.EventId), TaskName = tostring(customDimensions.TaskName)
| where EventId != 17523
| join kind=leftouter ServiceFabricEvent on EventId
| extend EventName = case(EventName != '', EventName, 'Undocumented')
| summarize ["Event Count"]= count() by bin(timestamp, 30m), EventName = strcat(tostring(EventId), " - ", EventName)
| render timechart
```

### <a name="view-service-fabric-application-events"></a>Service Fabric アプリケーション イベントを表示する

クラスター上に展開されている Reliable Services アプリケーションと Reliable Actors アプリケーションのイベントを表示できます。  Service Fabric Analytics ページで、**アプリケーション イベント**のグラフをクリックします。

次のクエリを実行すると、Reliable Services アプリケーションのイベントが表示されます。
```kusto
ServiceFabricReliableServiceEvent
| sort by TimeGenerated desc
```

通常はデプロイおよびアップグレードで行われるサービス runasync の開始時点および完了時点に関する、さまざまなイベントを表示できます。

![Service Fabric ソリューションの Reliable Services](media/service-fabric-tutorial-monitor-cluster/oms-reliable-services-events-selection.png)

ServiceName == "fabric:/Watchdog/WatchdogService" を使用して、Reliable Services のイベントを検索することもできます。

```kusto
ServiceFabricReliableServiceEvent
| where ServiceName == "fabric:/Watchdog/WatchdogService"
| project TimeGenerated, EventMessage
| order by TimeGenerated desc  
```
 
Reliable Actors のイベントも、同様の方式で表示できます。

```kusto
ServiceFabricReliableActorEvent
| sort by TimeGenerated desc
```
Reliable Actors に対してより詳細なイベントを構成するには、クラスター テンプレート内にある診断の拡張機能の構成で `scheduledTransferKeywordFilter` を変更する必要があります。 これらの値の詳細については、「[Reliable Actors の診断とパフォーマンス監視](service-fabric-reliable-actors-diagnostics.md#keywords)」を参照してください。

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

## <a name="view-performance-counters-with-azure-monitor-logs"></a>Azure Monitor ログを使用してパフォーマンス カウンターを表示する
パフォーマンス カウンターを表示するには、[Azure portal](https://portal.azure.com) に移動し、Service Fabric Analytics ソリューションを作成したリソース グループに移動します。 

リソースの **[ServiceFabric(mysfomsworkspace)]** 、 **[Log Analytics ワークスペース]** 、 **[詳細設定]** の順に選択します。

**[データ]** をクリックし、 **[Windows パフォーマンス カウンター]** をクリックします。 選択して有効にできる既定のカウンターの一覧があり、収集の間隔を設定することもできます。 収集する[追加のパフォーマンス カウンター](service-fabric-diagnostics-event-generation-perf.md)を追加することもできます。 正しい形式については、この[記事](/windows/desktop/PerfCtrs/specifying-a-counter-path)を参照してください。 **[保存]** をクリックし、 **[OK]** をクリックします。

[詳細設定] ブレードを閉じ、 **[全般]** という見出しの下にある **[ワークスペースの概要]** を選択します。 有効なソリューションごとにグラフ形式のタイルがあります (Service Fabric のタイルも含まれています)。 **[Service Fabric]** グラフをクリックして、Service Fabric Analytics ソリューションを継続します。

これらは、操作チャネルと Reliable Services のイベントのグラフ形式のタイルです。 選択したカウンターのデータ フローのグラフィック表現が、 **[Node Metrics]\(ノード メトリック\)** の下に表示されます。 

**[Container Metric]\(コンテナー メトリック\)** グラフを選択すると、詳細が追加表示されます。 また、Kusto クエリ言語を使用して、イベントをクラスタリングし、ノード、パフォーマンス カウンター名および値をフィルタリングするために、同様にパフォーマンス カウンター データに対してクエリを実行できます。

## <a name="query-the-eventstore-service"></a>EventStore サービスに対してクエリを実行する
[EventStore サービス](service-fabric-diagnostics-eventstore.md)では、任意の時点でのクラスターまたはワークロードの状態を理解する手段が提供されます。 EventStore は、クラスターからのイベントを保持するステートフルな Service Fabric サービスです。 イベントは、[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)、REST、および API シリーズを介して公開されます。 EventStore では、クラスターに対してクエリが直接実行されて、クラスター内の任意のエンティティに関する診断データが取得されます。EventStore で使用できるイベントの完全な一覧を確認するには、「[Service Fabric イベント](service-fabric-diagnostics-event-generation-operational.md)」を参照してください。

[Service Fabric クライアント ライブラリ](/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library)を使用して EventStore API シリーズのクエリをプログラムで実行することもできます。

GetClusterEventListAsync 関数を使用して、2018-04-03T18:00:00Z と 2018-04-04T18:00:00Z の間のすべてのクラスター イベントを要求する例を示します。

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

2018 年 9 月のクラスターの正常性と、すべてのノード イベントに対してクエリが実行され、それらが出力される別の例を次に示します。

```csharp
const int timeoutSecs = 60;
var clusterUrl = new Uri(@"http://localhost:19080"); // This example is for a Local cluster
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl);

var clusterHealth = sfhttpClient.Cluster.GetClusterHealthAsync().GetAwaiter().GetResult();
Console.WriteLine("Cluster Health: {0}", clusterHealth.AggregatedHealthState.Value.ToString());


Console.WriteLine("Querying for node events...");
var nodesEvents = sfhttpClient.EventsStore.GetNodesEventListAsync(
    "2018-09-01T00:00:00Z",
    "2018-09-30T23:59:59Z",
    timeoutSecs,
    "NodeDown,NodeUp")
    .GetAwaiter()
    .GetResult()
    .ToList();
Console.WriteLine("Result Count: {0}", nodesEvents.Count());

foreach (var nodeEvent in nodesEvents)
{
    Console.Write("Node event happened at {0}, Node name: {1} ", nodeEvent.TimeStamp, nodeEvent.NodeName);
    if (nodeEvent is NodeDownEvent)
    {
        var nodeDownEvent = nodeEvent as NodeDownEvent;
        Console.WriteLine("(Node is down, and it was last up at {0})", nodeDownEvent.LastNodeUpAt);
    }
    else if (nodeEvent is NodeUpEvent)
    {
        var nodeUpEvent = nodeEvent as NodeUpEvent;
        Console.WriteLine("(Node is up, and it was last down at {0})", nodeUpEvent.LastNodeDownAt);
    }
}
```


## <a name="monitor-cluster-health"></a>クラスターの正常性を監視する
Service Fabric には、正常性エンティティを使用する[正常性モデル](service-fabric-health-introduction.md)が導入されています。ここでは、システム コンポーネントやウォッチドッグで監視しているローカルの状態についてのレポートを確認できます。 すべての正常性データは[正常性ストア](service-fabric-health-introduction.md#health-store)によって集計され、エンティティが正常であるかどうかが判断されます。

クラスターには、システム コンポーネントから送信された正常性レポートが自動的に設定されます。 詳細については、「 [トラブルシューティングのためのシステム正常性レポートの使用](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)」を参照してください。

Service Fabric では、サポート対象の各 [エンティティ型](service-fabric-health-introduction.md#health-entities-and-hierarchy)について正常性クエリが公開されています。 アクセスには、API ([FabricClient.HealthManager](/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet) 上メソッドを使用)、PowerShell コマンドレット、および REST を使用できます。 これらのクエリは、正常性状態の集計、エンティティの正常性イベント、子の正常性状態 (該当する場合)、異常評価 (エンティティが正常でない場合)、子の正常性統計情報 (該当する場合) といったエンティティの正常性についての完全な情報を返します。

### <a name="get-cluster-health"></a>クラスターの正常性の取得
[Get-ServiceFabricClusterHealth コマンドレット](/powershell/module/servicefabric/get-servicefabricclusterhealth)は、クラスター エンティティの正常性を返し、アプリケーションとノード (クラスターの子) の正常性状態が含まれます。  まず、[Connect-ServiceFabricCluster コマンドレット](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)を使用してクラスターに接続します。

クラスターの状態 (11 個のノード、システム アプリケーション、fabric:/Voting) は上記のように構成されています。

次の例では、既定の正常性ポリシーを使用してクラスターの正常性を取得します。 11 個のノードは正常ですが、クラスターの集計された正常性状態はエラーです。これは fabric:/Voting アプリケーションがエラー状態であるためです。 異常性の評価には、正常性の集計をトリガーした条件の詳細が表示されます。

```powershell
Get-ServiceFabricClusterHealth

AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          100% (1/1) applications are unhealthy. The evaluation tolerates 0% unhealthy applications.
                          
                          Application 'fabric:/Voting' is in Error.
                          
                            33% (1/3) deployed applications are unhealthy. The evaluation tolerates 0% unhealthy deployed applications.
                          
                            Deployed application on node '_nt2vm_3' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '8723eb73-9b83-406b-9de3-172142ba15f3' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376195593305'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                          
NodeHealthStates        : 
                          NodeName              : _nt2vm_3
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_4
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_2
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_3
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_1
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_2
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_1
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt3vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_4
                          AggregatedHealthState : Ok
                          
ApplicationHealthStates : 
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok
                          
                          ApplicationName       : fabric:/Voting
                          AggregatedHealthState : Error
                          
HealthEvents            : None
HealthStatistics        : 
                          Node                  : 11 Ok, 0 Warning, 0 Error
                          Replica               : 4 Ok, 0 Warning, 0 Error
                          Partition             : 2 Ok, 0 Warning, 0 Error
                          Service               : 2 Ok, 0 Warning, 0 Error
                          DeployedServicePackage : 3 Ok, 1 Warning, 1 Error
                          DeployedApplication   : 1 Ok, 1 Warning, 1 Error
                          Application           : 0 Ok, 0 Warning, 1 Error
```

次の例では、カスタム アプリケーション ポリシーを使用してクラスターの正常性を取得します。 結果はフィルター処理され、エラーまたは警告状態のアプリケーションとノードのみを取得します。 この例では、すべて正常であったため、ノードは返されません。 アプリケーションのフィルターが考慮されるのは fabric:/Voting アプリケーションに対してのみです。 カスタム ポリシーでは fabric:/Voting アプリケーションに対する警告をエラーと認識するよう指定しているため、アプリケーションとクラスターはエラーとして評価されます。

```powershell
$appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/Voting"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error" -ExcludeHealthStatistics

AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          100% (1/1) applications are unhealthy. The evaluation tolerates 0% unhealthy applications.
                          
                          Application 'fabric:/Voting' is in Error.
                          
                            100% (5/5) deployed applications are unhealthy. The evaluation tolerates 0% unhealthy deployed applications.
                          
                            Deployed application on node '_nt2vm_3' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '8723eb73-9b83-406b-9de3-172142ba15f3' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376195593305'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_2' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '2466f2f9-d5fd-410c-a6a4-5b1e00630cca' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376486201388'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_4' is in Error.
                          
                                100% (1/1) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '5faa5201-eede-400a-865f-07f7f886aa32' is in Error.
                          
                                    'System.Hosting' reported Warning for property 'CodePackageActivation:Code:SetupEntryPoint:131959376207396204'. The evaluation treats 
                          Warning as Error.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_0' is in Error.
                          
                                100% (1/1) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '204f1783-f774-4f3a-b371-d9983afaf059' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959375885791093'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt3vm_0' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '2533ae95-2d2a-4f8b-beef-41e13e4c0081' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376108346272'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1                         
                          
NodeHealthStates        : None
ApplicationHealthStates : 
                          ApplicationName       : fabric:/Voting
                          AggregatedHealthState : Error
                          
HealthEvents            : None
```

### <a name="get-node-health"></a>ノードの正常性の取得
[Get-ServiceFabricNodeHealth コマンドレット](/powershell/module/servicefabric/get-servicefabricnodehealth)は、ノード エンティティの正常性を返し、ノードでレポートされた正常性のイベントが含まれます。 まず、[Connect-ServiceFabricCluster コマンドレット](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)を使用してクラスターに接続します。 次の例では、既定の正常性ポリシーを使用して特定のノードの正常性を取得します。

```powershell
Get-ServiceFabricNodeHealth _nt1vm_3
```

次の例では、クラスター内のすべてのノードの正常性を取得します。
```powershell
Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize
```

### <a name="get-system-service-health"></a>システム サービスの正常性の取得 

システム サービスの正常性の集計を取得します。

```powershell
Get-ServiceFabricService -ApplicationName fabric:/System | Get-ServiceFabricServiceHealth | select ServiceName, AggregatedHealthState | ft -AutoSize
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Service Fabric イベントの表示
> * クラスター イベントに対して EventStore API のクエリを実行する
> * インフラストラクチャの監視/パフォーマンス カウンターの収集を行う
> * クラスターの正常性レポートを表示する

次に、以下のチュートリアルに進んで、クラスターをスケーリングする方法について学習します。
> [!div class="nextstepaction"]
> [クラスターを拡張する](service-fabric-tutorial-scale-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
