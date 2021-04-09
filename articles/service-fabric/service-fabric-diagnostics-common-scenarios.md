---
title: Azure Service Fabric 診断の一般的なシナリオ
description: Azure Service Fabric アプリケーション内の一般的な監視と診断のシナリオのトラブルシューティングについて説明します。
ms.topic: article
ms.date: 02/25/2019
ms.openlocfilehash: 3083348948ce97ae9d99098896878d79081a15f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100580486"
---
# <a name="diagnose-common-scenarios-with-service-fabric"></a>Service Fabric で一般的なシナリオを診断する

この記事では、Service Fabric での監視と診断の領域でユーザーが遭遇した一般的なシナリオについて説明します。 示されているシナリオには、Service Fabric の 3 つのレイヤーであるアプリケーション、クラスター、およびインフラストラクチャがすべて含まれています。 各ソリューションでは、それぞれのシナリオを完了するために Application Insights、Azure Monitor ログ、Azure 監視ツールが使用されます。 各ソリューションの手順では、Service Fabric のコンテキストで Application Insights と Azure Monitor ログを使用する方法の概要をユーザーに説明します。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-recommendations"></a>前提条件と推奨事項

この記事のソリューションでは、次のツールを使用します。 これらを設定および構成しておくことをお勧めします。

* [Service Fabric での Application Insights](service-fabric-tutorial-monitoring-aspnet.md)
* [クラスターでの Azure Diagnostics の有効化](service-fabric-diagnostics-event-aggregation-wad.md)
* [Log Analytics ワークスペースを設定する](service-fabric-diagnostics-oms-setup.md)
* [パフォーマンス カウンターを追跡するための Log Analytics エージェント](service-fabric-diagnostics-oms-agent.md)

## <a name="how-can-i-see-unhandled-exceptions-in-my-application"></a>アプリケーションのハンドルされない例外を表示するにはどうすればよいですか

1. アプリケーションが構成されている Application Insights リソースに移動します。
2. 左上の *[検索]* をクリックします。 次に、隣のパネルで [フィルター] をクリックします。

    ![AI の概要](media/service-fabric-diagnostics-common-scenarios/ai-search-filter.png)

3. さまざまな種類のイベント (トレース、要求、カスタム イベント) が表示されます。 [例外] をフィルターとして選択します。

    ![AI のフィルター一覧](media/service-fabric-diagnostics-common-scenarios/ai-filter-list.png)

    Service Fabric Application Insights SDK を使用している場合、一覧にある例外をクリックすると、サービス コンテキストなどの詳細を確認できます。

    ![AI の例外](media/service-fabric-diagnostics-common-scenarios/ai-exception.png)

## <a name="how-do-i-view-which-http-calls-are-used-in-my-services"></a>サービスで使用された HTTP 呼び出しを表示する方法を教えてください

1. 同じ Application Insights リソースで、例外ではなく "要求" でフィルターを行って、実行されたすべての要求を表示できます。
2. Service Fabric Application Insights SDK を使用している場合、互いに接続されたサービスの視覚表現と、成功/失敗した要求の数を確認できます。 左側の [アプリケーション マップ] をクリックします。

    ![AI の [アプリケーション マップ] ブレード](media/service-fabric-diagnostics-common-scenarios/app-map-blade.png) ![AI の [アプリケーション マップ]](media/service-fabric-diagnostics-common-scenarios/app-map-new.png)

    アプリケーション マップの詳細については、[アプリケーション マップのドキュメント](../azure-monitor/app/app-map.md)をご覧ください。

## <a name="how-do-i-create-an-alert-when-a-node-goes-down"></a>ノードがダウンした場合に通知されるアラートを作成する方法を教えてください

1. ノード イベントは Service Fabric クラスターによって追跡されます。 **ServiceFabric(NameofResourceGroup)** という名前の Service Fabric Analytics ソリューション リソースに移動します。
2. [概要] ブレードの下部にあるグラフをクリックします。

    ![Azure Monitor ログのソリューション](media/service-fabric-diagnostics-common-scenarios/oms-solution-azure-portal.png)

3. さまざまなメトリックを示す多数のグラフとタイルが表示されます。 いずれかのグラフをクリックすると、[ログ検索] に移動します。 ここで、クラスター イベントまたはパフォーマンス カウンターを照会できます。
4. 次のクエリを入力します。 これらのイベント ID は、[ノード イベントのリファレンス](service-fabric-diagnostics-event-generation-operational.md#application-events)で確認できます。

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID >= 25622 and EventID <= 25626
    ```

5. 上部の [新しいアラート ルール] をクリックします。これで、このクエリに基づいてイベントが到着した場合にいつも、選択した通信手段でアラートが届きます。

    ![Azure Monitor ログの新しいアラート](media/service-fabric-diagnostics-common-scenarios/oms-create-alert.png)

## <a name="how-can-i-be-alerted-of-application-upgrade-rollbacks"></a>アプリケーション アップグレードのロールバックに関するアラートを受け取るには、どうすればよいですか

1. 前と同じ [ログ検索] ウィンドウで、アップグレードのロールバックに関する次のクエリを入力します。 これらのイベント ID は、[アプリケーション イベントのリファレンス](service-fabric-diagnostics-event-generation-operational.md#application-events)で確認できます。

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID == 29623 or EventID == 29624
    ```

2. 上部の [新しいアラート ルール] をクリックします。これで、このクエリに基づいてイベントが到着した場合にいつもアラートが届きます。

## <a name="how-do-i-see-container-metrics"></a>コンテナー メトリックの確認方法を教えてください

すべてのグラフと同じビューに、コンテナーのパフォーマンスに関するタイルがいくつか表示されます。 これらのタイルの作成には、Log Analytics エージェントと[コンテナー監視ソリューション](service-fabric-diagnostics-oms-containers.md)が必要です。

![Log Analytics のコンテナー メトリック](media/service-fabric-diagnostics-common-scenarios/containermetrics.png)

>[!NOTE]
>コンテナーの **内部** からテレメトリをインストルメント化するには、[コンテナー用の Application Insights NuGet パッケージ](https://github.com/Microsoft/ApplicationInsights-servicefabric#microsoftapplicationinsightsservicefabric--for-service-fabric-lift-and-shift-scenarios)を追加する必要があります。

## <a name="how-can-i-monitor-performance-counters"></a>パフォーマンス カウンターを監視するにはどうすればよいですか

1. Log Analytics エージェントをクラスターに追加したら、追跡したい特定のパフォーマンス カウンターを追加する必要があります。ポータルでソリューションのページから Log Analytics ワークスペースのページに移動します。ワークスペースのタブは左側のメニューにあります。

    ![Log Analytics ワークスペース タブ](media/service-fabric-diagnostics-common-scenarios/workspacetab.png)

2. ワークスペースのページが表示されたら、同じく左側のメニューにある [詳細設定] をクリックします。

    ![Log Analytics の [詳細設定]](media/service-fabric-diagnostics-common-scenarios/advancedsettingsoms.png)

3. [データ]、[Windows パフォーマンス カウンター] (Linux マシンの場合は [データ]、[Linux パフォーマンス カウンター]) の順にクリックして、Log Analytics エージェント経由でノードから特定のカウンターを収集することを開始します。 追加するカウンターの形式の例を次に示します。

   * `.NET CLR Memory(<ProcessNameHere>)\\# Total committed Bytes`
   * `Processor(_Total)\\% Processor Time`

     このクイック スタートでは、VotingData と VotingWeb というプロセス名が使用されます。そのため、これらのカウンターの追跡は次のようになります。

   * `.NET CLR Memory(VotingData)\\# Total committed Bytes`
   * `.NET CLR Memory(VotingWeb)\\# Total committed Bytes`

     ![Log Analytics のパフォーマンス カウンター](media/service-fabric-diagnostics-common-scenarios/omsperfcounters.png)

4. これで、インフラストラクチャで処理されているワークロードと、リソース使用率に基づいて設定された関連アラートを確認できるようになります。 たとえば、プロセッサ使用率の合計が 90% を上回るか、または 5% 未満になったときに通知されるアラートを設定したい場合があります。 この場合に使用するカウンター名は "% Processor Time" です。 これは、次のクエリのアラート ルールを作成することで実行できます。

    ```kusto
    Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" | where CounterValue >= 90 or CounterValue <= 5.
    ```

## <a name="how-do-i-track-performance-of-my-reliable-services-and-actors"></a>Reliable Services と Reliable Actors のパフォーマンスを追跡する方法を教えてください

アプリケーションで Reliable Services または Reliable Actors のパフォーマンスを追跡するには、Service Fabric アクター、Service Fabric アクター メソッド、Service Fabric サービス、Service Fabric サービス メソッドのカウンターも収集する必要があります。 信頼できるサービスと、収集する必要があるアクター パフォーマンス カウンターの例を次に示します

>[!NOTE]
>現在、Service Fabric のパフォーマンス カウンターは Log Analytics エージェントでは収集できませんが、[他の診断ソリューション](service-fabric-diagnostics-partners.md)では収集できます

* `Service Fabric Service(*)\\Average milliseconds per request`
* `Service Fabric Service Method(*)\\Invocations/Sec`
* `Service Fabric Actor(*)\\Average milliseconds per request`
* `Service Fabric Actor Method(*)\\Invocations/Sec`

[Reliable Services](service-fabric-reliable-serviceremoting-diagnostics.md) と [Reliable Actors](service-fabric-reliable-actors-diagnostics.md) に関するパフォーマンス カウンターの完全な一覧を確認するには、それぞれのリンクをご利用ください。

## <a name="next-steps"></a>次のステップ

* [一般的なコード パッケージのアクティブ化エラーを参照する](./service-fabric-diagnostics-code-package-errors.md)
* [AI のアラートを設定して](../azure-monitor/alerts/alerts-log.md)、パフォーマンスまたは使用状況の変化について通知を受けます
* [Application Insights のスマート検出](../azure-monitor/app/proactive-diagnostics.md)は、 AI に送信されるテレメトリのプロアクティブ分析を実行し、潜在的なパフォーマンスの問題を警告します
* 検出と診断に役立つ Azure Monitor ログの[アラート](../azure-monitor/alerts/alerts-overview.md)についてさらに学習します。
* オンプレミス クラスター向けに、Azure Monitor ログでは、データを Azure Monitor ログに送信するために使用できるゲートウェイ (HTTP 転送プロキシ) を提供されています。 詳細については、「[インターネットにアクセスできないコンピューターを Log Analytics ゲートウェイを使って Azure Monitor ログに接続する](../azure-monitor/agents/gateway.md)」を参照してください
* Azure Monitor ログの一部として提供されている[ログ検索とクエリ](../azure-monitor/logs/log-query-overview.md)機能の詳細を確認します
* Azure Monitor ログとそれが提供するサービスの詳しい概要について、[Azure Monitor ログとは何か](../azure-monitor/overview.md)に関するページで確認します
