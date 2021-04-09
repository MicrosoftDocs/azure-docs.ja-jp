---
title: EventStore API を使用したクラスター イベントのクエリ
description: Azure Service Fabric EventStore API を使用してプラットフォーム イベントのクエリを実行する方法について説明します
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.custom: devx-track-csharp
ms.openlocfilehash: 6bed26227542cbf3ffc13ecc018aef9e659d026e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791630"
---
# <a name="query-eventstore-apis-for-cluster-events"></a>クラスター イベントに対して EventStore API のクエリを実行する

この記事では、Service Fabric バージョン 6.2 以降で使用可能な EventStore API のクエリを実行する方法について説明します。EventStore サービスについて詳しくは、「[EventStore service overview](service-fabric-diagnostics-eventstore.md)」(EventStore サービスの概要) をご覧ください。 現時点では、EventStore サービスでは、(クラスターの診断データ保有ポリシーに基づいて) 過去 7 日間のデータのみにアクセスできます。

>[!NOTE]
>Service Fabric バージョン 6.4 の時点で、EventStore API は、Azure 上で実行されている Windows クラスターに対してのみの一般提供 (GA) です。

EventStore API は、REST エンドポイントを使用して直接アクセスするか、プログラムでアクセスできます。 クエリによっては、適切なデータを収集するために必要なパラメーターがいくつかあります。 通常、次のパラメーターが含まれます。
* `api-version`: 使用している EventStore API のバージョン
* `StartTimeUtc`: 参照する期間の開始を定義します
* `EndTimeUtc`: 期間の終わり

これらのパラメーターに加えて、次のような省略可能なパラメーターを使用できます。
* `timeout`: 要求操作の実行に対する既定の 60 秒のタイムアウトをオーバーライドします
* `eventstypesfilter`: 特定のイベントの種類をフィルター処理するオプションを提供します
* `ExcludeAnalysisEvents`: '解析' イベントを返しません。 既定では、EventStore クエリによって可能な場合 "解析" イベントが返されます。 解析イベントは、通常の Service Fabric イベント以外の追加のコンテキストや情報を含み、より詳しい情報を提供するより強力な稼働チャネル イベントです。
* `SkipCorrelationLookup`: クラスター内の潜在的な相関イベントを探しません。 既定では、EventStore はクラスター全体でイベントを関連付けようとし、可能な場合はイベントどうしをリンクします。 

クラスター内の各エンティティに対してイベントのクエリを実行できます。 すべての種類のエンティティに対してイベントのクエリを実行することもできます。 たとえば、特定のノードまたはクラスター内のすべてのノードに対してイベントのクエリを実行できます。 イベントのクエリを実行できる現在のエンティティ セット (およびクエリの構成) は次のとおりです。
* クラスター: `/EventsStore/Cluster/Events`
* 複数ノード: `/EventsStore/Nodes/Events`
* ノード: `/EventsStore/Nodes/<NodeName>/$/Events`
* 複数アプリケーション: `/EventsStore/Applications/Events`
* アプリケーション: `/EventsStore/Applications/<AppName>/$/Events`
* 複数サービス: `/EventsStore/Services/Events`
* サービス: `/EventsStore/Services/<ServiceName>/$/Events`
* 複数パーティション: `/EventsStore/Partitions/Events`
* パーティション: `/EventsStore/Partitions/<PartitionID>/$/Events`
* 複数レプリカ: `/EventsStore/Partitions/<PartitionID>/$/Replicas/Events`
* レプリカ: `/EventsStore/Partitions/<PartitionID>/$/Replicas/<ReplicaID>/$/Events`

>[!NOTE]
>アプリケーションまたはサービス名を参照する場合、クエリに "fabric:/" プレフィックスを含める必要はありません。 さらに、アプリケーションまたはサービス名に "/" が含まれる場合は、"~" に切り替えてクエリの動作を維持します。 たとえば、アプリケーションが "fabric:/App1/FrontendApp" と表示される場合、アプリ固有のクエリは `/EventsStore/Applications/App1~FrontendApp/$/Events` と構成されます。
>さらに、サービスの今日の正常性レポートが対応するアプリケーションに表示される場合は、適切なアプリケーション エンティティに対して `DeployedServiceHealthReportCreated` イベントのクエリを実行します。 

## <a name="query-the-eventstore-via-rest-api-endpoints"></a>REST API エンドポイントを介した EventStore のクエリ

`<your cluster address>/EventsStore/<entity>/Events/` に対して `GET` 要求を実行して、REST エンドポイントを介して EventStore のクエリを直接実行できます。

たとえば、`2018-04-03T18:00:00Z` と `2018-04-04T18:00:00Z` の間のすべてのクラスター イベントのクエリを実行するには、要求は次のようになります。

```
Method: GET 
URL: http://mycluster:19080/EventsStore/Cluster/Events?api-version=6.4&StartTimeUtc=2018-04-03T18:00:00Z&EndTimeUtc=2018-04-04T18:00:00Z
```

これによって、イベントや JSON で返される一覧が返されることはありません。

```json
Response: 200
Body:
[
  {
    "Kind": "ClusterUpgradeStart",
    "CurrentClusterVersion": "0.0.0.0:",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeType": "Rolling",
    "RollingUpgradeMode": "UnmonitoredAuto",
    "FailureAction": "Manual",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:18:59.4313064Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(0 1 2)",
    "UpgradeDomainElapsedTimeInMs": "78.5288",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:19:59.5729953Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(3 4)",
    "UpgradeDomainElapsedTimeInMs": "0",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.6271949Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeComplete",
    "TargetClusterVersion": "6.2:1.0",
    "OverallUpgradeElapsedTimeInMs": "120196.5212",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.8134457Z",
    "HasCorrelatedEvents": false
  }
]
```

ご覧のように、`2018-04-03T18:00:00Z` と `2018-04-04T18:00:00Z` の間では、このクラスターは、初回起動時に `"CurrentClusterVersion": "0.0.0.0:"` から `"TargetClusterVersion": "6.2:1.0"` への最初のアップグレードを `"OverallUpgradeElapsedTimeInMs": "120196.5212"` で正常に完了しました。

## <a name="query-the-eventstore-programmatically"></a>プログラムによる EventStore のクエリ

[Service Fabric クライアント ライブラリ](/dotnet/api/overview/azure/service-fabric#client-library)を介して EventStore のクエリをプログラムで実行することもできます。

Service Fabric クライアントを設定したら、次のように EventStore にアクセスしてイベントのクエリを実行できます: `sfhttpClient.EventStore.<request>`

`GetClusterEventListAsync` 関数を使用して、`2018-04-03T18:00:00Z` と `2018-04-04T18:00:00Z` の間のすべてのクラスター イベントを要求する例を次に示します。

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

## <a name="sample-scenarios-and-queries"></a>サンプルのシナリオとクエリ

クラスターの状態を理解するために、イベント ストア REST API を呼び出す方法の例をいくつか示します。

*クラスターのアップグレード:*

過去 1 週間のクラスターが最後に正常にアップグレードされた時刻またはアップグレードが試行された時刻を表示するには、EventStore で "ClusterUpgradeCompleted" イベントのクエリを実行して、クラスターに対して最近完了したアップグレードのクエリを API に対して実行できます: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ClusterUpgradeCompleted`

*クラスターのアップグレードの問題:*

同様に、最近のクラスター アップグレードに問題があった場合は、クラスター エンティティに対してすべてのイベントのクエリを実行できます。 アップグレードの開始などのさまざまなイベントと、アップグレードが正常に開始された各 UD が表示されます。 ロールバックが開始された時点のイベントと、対応する正常性イベントも表示されます。 これに使用するクエリを次に示します: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*ノードの状態の変更:*

過去数日間のノードの状態の変化 (ノードが起動またはダウンしたときや、(プラットフォーム、chaos サービスまたはユーザー入力により) アクティブ化または非アクティブ化されたとき) を表示するには、次のクエリを使用します: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Nodes/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*アプリケーション イベント:*

最近のアプリケーションのデプロイとアップグレードを追跡することもできます。 次のクエリを使用して、ご利用のクラスター内のアプリケーション イベントをすべて表示します: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*アプリケーションの正常性の履歴:*

アプリケーション ライフサイクル イベントを表示するだけでなく、特定のアプリケーションの正常性に関する履歴データを表示することもできます。 これは、データの収集対象のアプリケーション名を指定して行うことができます。 このクエリを使用して、すべてのアプリケーションの正常性イベントを取得します: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myApp/$/Events?api-version=6.4&starttimeutc=2018-03-24T17:01:51Z&endtimeutc=2018-03-29T17:02:51Z&EventsTypesFilter=ApplicationNewHealthReport`。 期限が切れた (Time to Live (TTL) を経過した) 正常性イベントを含める場合は、クエリの末尾に `,ApplicationHealthReportExpired` を追加して、2 種類のイベントにフィルターを適用します。

*"myApp" のすべてのサービスの正常性の履歴:*

現時点では、サービスの正常性レポート イベントは、対応するアプリケーション エンティティの下に `DeployedServicePackageNewHealthReport` イベントとして表示されます。 サービスが "App1" に対してどのように動作しているかを確認するには、次のクエリを使用します: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myapp/$/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=DeployedServicePackageNewHealthReport`

*パーティションの再構成:*

クラスター内で発生したパーティションの移動をすべて確認するには、`PartitionReconfigured` イベントのクエリを実行します。 これにより、クラスター内の問題を診断するときに、特定の時点でどのようなワークロードがどのノードで実行されたかを把握できます。 これを行うクエリのサンプルを次に示します: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Partitions/Events?api-version=6.4&starttimeutc=2018-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=PartitionReconfigured`

*Chaos サービス:*

クラスター レベルで公開されている Chaos サービスがいつ開始または停止されたかに関するイベントがあります。 Chaos サービスの最近の使用状況を確認するには、次のクエリを使用します: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ChaosStarted,ChaosStopped`
