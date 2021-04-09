---
title: Azure Monitor ログを使用した Azure Service Fabric イベント分析
description: Azure Service Fabric クラスターの監視と診断での Azure Monitor ログを使用したイベントの視覚化と分析について説明します。
author: srrengar
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: srrengar
ms.openlocfilehash: 678e96c9b5611cd6d72abd7c4582691b55ffe479
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100581549"
---
# <a name="event-analysis-and-visualization-with-azure-monitor-logs"></a>Azure Monitor ログを使用したイベントの分析と視覚化
 Azure Monitor ログでは、クラウド内でホストされているアプリケーションとサービスからテレメトリが収集および分析され、それらの可用性とパフォーマンスを最大限に高めるために役立つ分析ツールが提供されます。 この記事では、Azure Monitor ログでクエリを実行して分析情報を取得し、クラスター内の処理をトラブルシューティングする方法について説明します。 次のような一般的な質問に対応します。

* 正常性イベントをどのようにトラブルシューティングすればいいか。
* ノードがダウンするタイミングを知るにはどうすればいいか。
* 自分のアプリケーションのサービスが起動または停止されたかを知るにはどうすればいいか。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview-of-the-log-analytics-workspace"></a>Log Analytics ワークスペースの概要

>[!NOTE] 
>クラスター作成時に診断ストレージが既定で有効になっている場合は、Log Analytics ワークスペースを診断ストレージから読み取るように設定する必要があります。

Azure Monitor ログでは、Azure ストレージ テーブルやエージェントなどの管理対象リソースからデータを収集し、中央レポジトリで管理します。 それらのデータは分析、アラート、および視覚化、さらにエクスポートに使用できます。 Azure Monitor ログでは、イベント、パフォーマンス データ、またはその他のカスタム データがサポートされます。 [診断の拡張機能を設定してイベントを集計する手順](service-fabric-diagnostics-event-aggregation-wad.md)および [Log Analytics ワークスペースを作成してストレージ内のイベントから読み取りを行う手順](service-fabric-diagnostics-oms-setup.md)を確認して、データが確実に Azure Monitor ログに送信されるようにしてください。

Azure Monitor ログによってデータが受信されたら、Azure に用意されている *管理ソリューション* (パッケージ済みのソリューションまたは運用ダッシュボード) を使用して、さまざまなシナリオに合わせて受信データを監視します。 *Service Fabric Analytics* ソリューションと *コンテナー* ソリューションが含まれており、これらは Service Fabric クラスターを使用する際の診断と監視に最も関連性の高いソリューションです。 この記事では、ワークスペースを使って作成される、Service Fabric Analytics ソリューションを使用する方法について説明します。

## <a name="access-the-service-fabric-analytics-solution"></a>Service Fabric Analytics ソリューションへのアクセス

[Azure Portal](https://portal.azure.com) で、Service Fabric Analytics ソリューションを作成したリソース グループに移動します。

リソース **ServiceFabric\<nameOfOMSWorkspace\>** を選択します。

`Summary` に、有効なソリューションごとのグラフ形式のタイルが表示されます (Service Fabric のタイルも含まれています)。 **[Service Fabric]** グラフをクリックして、Service Fabric Analytics ソリューションを継続します。

![Service Fabric ソリューション](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

次の画像には、Service Fabric Analytics ソリューションのホーム ページが示されています。 このホーム ページでは、クラスター内で行われている処理のスナップショット ビューが提供されます。

![Service Fabric Analytics ソリューションのホーム ページを示すスクリーンショット。](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

 クラスター作成時に診断を有効にした場合は、以下に対するイベントが表示されます。 

* [Service Fabric クラスター イベント](service-fabric-diagnostics-event-generation-operational.md)
* [Reliable Actors プログラミング モデル イベント](service-fabric-reliable-actors-diagnostics.md)
* [Reliable Services プログラミング モデル イベント](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>[診断の拡張機能の構成を更新する](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)ことで、すぐに利用できる Service Fabric イベントだけでなく、より詳細なシステム イベントが収集できます。

## <a name="view-service-fabric-events-including-actions-on-nodes"></a>ノードに対する操作を含む Service Fabric イベントを表示する

Service Fabric Analytics ページで、**Service Fabric Events** グラフをクリックします。

![Service Fabric ソリューションの操作チャネル](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

一覧内のイベントを表示するには、 **[一覧]** をクリックします。 ここで、収集済みのすべてのシステム イベントが表示されます。 参考までに、これらは Azure Storage アカウント内の **WADServiceFabricSystemEventsTable** に由来し、次に確認する Reliable Services イベントや Reliable Actors イベントも同様に、それぞれのテーブルに由来します。
    
![クエリの操作チャネル](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

または、左側にある虫眼鏡をクリックし、Kusto クエリ言語を使って検索対象を見つけることもできます。 たとえば、クラスター内のノードに対して行われたすべての操作を検索するは、次のクエリを使用できます。 使用されているイベント ID は、[操作チャネルのイベント リファレンス](service-fabric-diagnostics-event-generation-operational.md)で確認できます。

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

特定のノード (Computer) やシステム サービス (TaskName) など、より多くのフィールドに対してクエリを実行できます。

## <a name="view-service-fabric-reliable-service-and-actor-events"></a>Service Fabric Reliable Service および Actor イベントの表示

Service Fabric Analytics ページで、**Reliable Services** グラフをクリックします。

![Service Fabric ソリューションの Reliable Services](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

一覧内のイベントを表示するには、 **[一覧]** をクリックします。 ここで、Reliable Services のイベントを確認できます。 通常はデプロイおよびアップグレードで行われるサービス runasync の開始時点および完了時点に関する、さまざまなイベントを表示できます。 

![クエリの Reliable Services](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

Reliable Actor のイベントも、同様の方式で表示できます。 Reliable Actor により詳細なイベントを設定するには、診断の拡張機能の構成 (以下に示します) で `scheduledTransferKeywordFilter` を変更する必要があります。 これらの値の詳細については、「[Reliable Actors の診断とパフォーマンス監視](service-fabric-reliable-actors-diagnostics.md#keywords)」を参照してください。

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

Kusto クエリ言語は優れています。 実行可能なもう 1 つの重要なクエリでは、どのノードで大部分のイベントが生成されているかを検出します。 次のスクリーン ショットのクエリは、特定のサービスとノードで集計された Service Fabric の操作イベントを示しています。

![クエリのノードごとのイベント](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>次のステップ

* インフラストラクチャの監視、つまりパフォーマンス カウンターを有効にするために、[Log Analytics エージェントの追加](service-fabric-diagnostics-oms-agent.md)に関するページにアクセスしてください。 エージェントによって、パフォーマンス カウンターが収集され、既存のワークスペースに追加されます。
* オンプレミス クラスター向けに、Azure Monitor ログでは、データを Azure Monitor ログに送信するために使用できるゲートウェイ (HTTP 転送プロキシ) を提供されています。 詳細については、「[インターネットにアクセスできないコンピューターを Log Analytics ゲートウェイを使って Azure Monitor ログに接続する](../azure-monitor/agents/gateway.md)」を参照してください。
* 検出と診断に役立つ[自動アラート](../azure-monitor/alerts/alerts-overview.md)を構成します。
* Azure Monitor ログの一部として提供されている[ログ検索とクエリ](../azure-monitor/logs/log-query-overview.md)機能をよく理解します。
* Azure Monitor ログとそれが提供するサービスの詳しい概要について、[Azure Monitor ログとは何か](../azure-monitor/overview.md)に関するページで確認します。
