---
title: Azure Service Fabric の監視と診断の概要
description: Azure Service Fabric のクラスター、アプリケーション、およびサービスの監視と診断について説明します。
author: srrengar
ms.topic: conceptual
ms.date: 1/17/2019
ms.author: srrengar
ms.openlocfilehash: 25a50a2841a03929804be45be8012f9b5d0457ff
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "91357133"
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Azure Service Fabric での監視と診断

この記事では Azure Service Fabric の監視と診断の概要について説明します。 監視と診断は、あらゆるクラウド環境でアプリケーションやサービスを開発、テスト、およびデプロイするために非常に重要です。 たとえば、アプリケーションの使われ方、Service Fabric プラットフォームによって実行されたアクション、パフォーマンス カウンターでのリソースの使用状況、クラスターの全体的な正常性を追跡できます。 この情報を使って、問題の診断と修正を行い、再発を防ぐことができます。 以下のセクションでは、運用環境のワークロードで検討する必要のある、Service Fabric の各監視領域を簡単に説明します。 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="application-monitoring"></a>アプリケーションの監視
アプリケーションの監視は、アプリケーションの機能やコンポーネントがどのように使用されているかを追跡します。 お客様は、アプリケーションを監視して、ユーザーに影響が及ぶ問題を把握したいと考えます。 ビジネス ロジックはアプリケーションごとに固有なので、アプリケーションとそのサービスを開発するユーザーに、アプリケーションを監視する責任があります。 アプリケーションの監視は、次のシナリオで役立ちます。
* アプリケーションで発生しているトラフィックの量を知りたい。 - ユーザーのニーズを満たすために、またはアプリケーションの潜在的なボトルネックに対処するために、サービスを拡張する必要があるかどうかを判断できます。
* サービス間の呼び出しが成功して追跡されているかどうか知りたい。
* アプリケーションのユーザーによって実行されたアクションを知りたい。 - テレメトリを収集すると、将来機能を開発したり、アプリケーション エラーを適切に診断したりするときに参考になります
* アプリケーションでハンドルされない例外がスローされているか知りたい。 
* コンテナーの内部で実行されているサービス内で起きていることを知りたい。

アプリケーション監視のすばらしい点は、アプリケーションのコンテキスト内で動作するため、開発者は好みのツールやフレームワークを何でも使用できることです。 Azure Monitor - Application Insights でのアプリケーション監視のための Azure ソリューションに関して詳しくは、[Application Insights でのイベント分析](service-fabric-diagnostics-event-analysis-appinsights.md)に関する記事をご覧ください。
[.NET アプリケーション用にこれを設定する](service-fabric-tutorial-monitoring-aspnet.md)方法についてのチュートリアルもあります。 このチュートリアルでは、適切なツールをインストールする方法、アプリケーションでカスタム テレメトリを記述する例、Azure portal でのアプリケーションの診断とテレメトリの表示について説明されています。 


## <a name="platform-cluster-monitoring"></a>プラットフォーム (クラスター) の監視
アプリケーションから送られてくるテレメトリはユーザーが自分でコードを書くのでユーザーの制御下にありますが、Service Fabric プラットフォームからの診断はどうでしょうか。 Service Fabric の目標の 1 つは、ハードウェア障害に対するアプリケーションの回復力を維持することです。 この目標を実現するには、プラットフォームのシステム サービスの機能を利用します。この機能は、インフラストラクチャの問題を特定し、ワークロードをクラスター内の別のノードにすばやくフェールオーバーします。 しかし、このようなケースで、もしシステム サービス自体に問題があるとしたらどうなるでしょうか。 または、ワークロードのデプロイまたは移動でサービス配置のルールに違反したらどうなるでしょうか。 Service Fabric ではこれらやその他についての診断が提供され、ユーザーはクラスターで発生しているアクティビティについての情報を得られます。 クラスター監視のシナリオの例を次に示します。

Service Fabric は、標準で、包括的なイベントのセットを提供します。 これらの [Service Fabric イベント](service-fabric-diagnostics-events.md)には、EventStore または運用チャネル (プラットフォームによって公開されたイベント チャネル) を通してアクセスできます。 

* Service Fabric イベントのチャネル - Windows では、Service Fabric イベントは、稼働およびデータのチャネルとメッセージング チャネルの選択に使用される、一連の関連する `logLevelKeywordFilters` を持つ単一の ETW プロバイダーから提供されます。これが、発信される Service Fabric イベントを必要に応じて取り出してフィルター処理する方法となっています。 Linux では、Service Fabric イベントは LTTng 経由で到着し、1 つの Storage テーブルに格納されます。ここで、必要に応じてイベントをフィルター処理できます。 これらのチャネルには、選別され、構造化されたイベントが含まれ、これを使用してクラスターの状態をより詳細に把握できます。 クラスターの作成時には、既定で診断が有効になります。それにより、Azure Storage テーブルが作成され、後でクエリを実行できるように、そこにこれらのチャネルからのイベントが送信されます。 

* EventStore - EventStore は Service Fabric Explorer および REST API で使用可能な Service Fabric プラットフォーム イベントを提供するプラットフォームによって提供される機能です。 ノード、サービス、アプリケーション、クエリなどの各エンティティに対してクラスター内で行われていることの時刻に基づくスナップショット ビューを表示できます。 また、EventStore について詳しくは、「[EventStore の概要](service-fabric-diagnostics-eventstore.md)」をご覧ください。    

![[ノード] ペインの [イベント] タブに NodeDown イベントなどのいくつかのイベントが表示さているスクリーンショット。](media/service-fabric-diagnostics-overview/eventstore.png)

提供される診断は、既定のイベントの包括的なセットの形式になっています。 これらの [Service Fabric イベント](service-fabric-diagnostics-events.md)では、ノード、アプリケーション、サービス、パーティションなどのさまざまなエンティティ上で、プラットフォームによって実行されたアクションが示されています。上記の最後のシナリオでは、ノードがダウンした場合、プラットフォームは `NodeDown` イベントを生成し、ユーザーは自分が選択した監視ツールによってすぐに通知されます。 他の一般的な例としては、フェールオーバーの間の `ApplicationUpgradeRollbackStarted` や `PartitionReconfigured` などがあります。 **同じイベントを、Windows と Linux 両方のクラスターで利用できます。**

イベントは、Windows でも Linux でも標準チャンネル経由で送信され、これらをサポートする任意の監視ツールで読み取ることができます。 Azure Monitor ソリューションは Azure Monitor ログです。 [Azure Monitor ログの統合](service-fabric-diagnostics-event-analysis-oms.md)に関する詳細をご覧ください。これには、クラスター用のカスタム操作ダッシュボードと、アラートの作成に使用できるサンプル クエリが含まれています。 クラスター監視の概念について詳しくは、「[プラットフォーム レベルのイベントとログの生成](service-fabric-diagnostics-event-generation-infra.md)」をご覧ください。

### <a name="health-monitoring"></a>正常性の監視
Service Fabric プラットフォームには正常性モデルが組み込まれており、これがクラスター内のエンティティの状態に関する拡張可能な正常性レポートを提供します。 各ノード、アプリケーション、サービス、パーティション、レプリカ、またはインスタンスには、継続的に更新可能な正常性状態が存在します。 正常性状態は、"OK"、"警告"、"エラー" のいずれかになります。 Service Fabric のイベントはさまざまなエンティティに対してクラスターによって実行された動詞として、正常性は各エンティティの形容詞として、考えることができます。 特定のエンティティの正常性が変化するたびに、イベントも生成されます。 これにより、他のイベントと同様に、任意の監視ツールで正常性イベントに対するクエリとアラートを設定できます。 

さらに、ユーザーがエンティティの正常性をオーバーライドすることさえできるようになっています。 アプリケーションのアップグレード時に検証テストが失敗する場合、ユーザーは Health API を使用して Service Fabric の正常性に書き込んで、アプリケーションが正常ではないことを示すことができ、Service Fabric は自動的にアップグレードのロールバックを行います。 正常性モデルについて詳しくは、「[Service Fabric の正常性モニタリングの概要](service-fabric-health-introduction.md)」をご覧ください

![SFX 正常性ダッシュボード](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)


### <a name="watchdogs"></a>ウォッチドッグ
一般に、ウォッチドッグとは、複数のサービスにわたって正常性と負荷を監視し、エンドポイントに ping を送信し、クラスター内のあらゆるものの正常性を報告することができる個別のサービスを指します。 これは、単一サービスのビューに基づいて検出されないエラーを防ぐのに役立ちます。 またウォッチドッグは、ユーザー操作なしで修復アクションを実行するコードをホストする場所としても適しています。たとえば、特定の時間間隔でストレージ内のログ ファイル クリーンアップを行うなどです。 ウォッチドッグ サービスの実装サンプルは、[ここ](https://github.com/Azure-Samples/service-fabric-watchdog-service)で見つけることができます。

## <a name="infrastructure-performance-monitoring"></a>インフラストラクチャ (パフォーマンス) の監視
これまではアプリケーションとプラットフォームでの診断について説明してきましたが、ハードウェアが期待どおりに機能していることを知るにはどうすればよいでしょう。 基盤となるインフラストラクチャを監視することは、クラスターやリソース使用率の状態を把握する上で重要です。 システム パフォーマンスの測定は、ワークロードに応じて影響を受ける可能性がある多くの要因に依存します。 これらの要因は通常、パフォーマンス カウンターによって測定されます。 これらのパフォーマンス カウンターは、オペレーティング システム、.NET フレームワーク、Service Fabric プラットフォーム自体など、さまざまなソースから送られてきます。 次のようなシナリオで役に立ちます

* ハードウェアが効率的に利用されているか知りたい。 CPU 90% または CPU 10% のどちらで、ハードウェアを使いたいですか。 これは、クラスターをスケーリングするとき、またはアプリケーションのプロセスを最適化するときに役に立ちます。
* インフラストラクチャの問題を事前に予測したい。 多くの問題では、発生前にパフォーマンスの急激な変化 (低下) があるため、ネットワーク I/O や CPU 使用率などのパフォーマンス カウンターを使用して、問題を事前に予測および診断できます。

インフラストラクチャ レベルで収集されるパフォーマンス カウンターの一覧は、「[パフォーマンス メトリック](service-fabric-diagnostics-event-generation-perf.md)」でご覧いただけます。 

Service Fabric では、Reliable Services および Reliable Actors の各プログラミング モデル向けのパフォーマンス カウンター セットも提供されています。 これらのモデルのいずれかを使用している場合、アクターが上下に適切にスピンするようにしたり、Reliable Services の要求が迅速に処理されるようにしたりするのに役立つ情報を、これらのパフォーマンス カウンターで提供できます。 詳細については、[Reliable Services のリモート処理の監視](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters)および [Reliable Actors のパフォーマンスの監視](service-fabric-reliable-actors-diagnostics.md#performance-counters)に関する記事を参照してください。 

これらを収集するための Azure Monitor ソリューションは、プラットフォーム レベルの監視と同様に Azure Monitor ログです。 [Log Analytics エージェント](service-fabric-diagnostics-oms-agent.md)を使用して適切なパフォーマンス カウンターを収集し、それらを Azure Monitor ログで表示する必要があります。

## <a name="recommended-setup"></a>推奨される設定
監視の各領域とシナリオの例を説明したので、ここでは上記のすべての領域を監視するために必要な Azure 監視ツールと設定の概要を説明します。 

* [Application Insights](service-fabric-tutorial-monitoring-aspnet.md) によるアプリケーションの監視
* [診断エージェント](service-fabric-diagnostics-event-aggregation-wad.md)と [Azure Monitor ログ](service-fabric-diagnostics-oms-setup.md)によるクラスターの監視
* [Azure Monitor ログ](service-fabric-diagnostics-oms-agent.md)によるインフラストラクチャーの監視

[こちら](service-fabric-diagnostics-oms-setup.md#deploy-azure-monitor-logs-with-azure-resource-manager)にあるサンプルの ARM テンプレートを使用および変更して、すべての必要なリソースとエージェントのデプロイを自動化することもできます。 

## <a name="other-logging-solutions"></a>その他のログ記録ソリューション

推奨した 2 つのソリューション、[Azure Monitor ログ](service-fabric-diagnostics-event-analysis-oms.md)と [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) には Service Fabric との統合が組み込まれていますが、多くのイベントは ETW プロバイダーを介して書き出され、他のロギング ソリューションでも拡張可能です。 また、[Elastic Stack](https://www.elastic.co/products) (特に、オフライン環境でクラスターの実行を検討している場合)、[Dynatrace](https://www.dynatrace.com/)、またはその他の好みのプラットフォームを検討する必要もあります。 使用可能な統合されたパートナーの一覧は[こちら](service-fabric-diagnostics-partners.md)をご覧ください。

プラットフォーム選択の重要なポイントは、ユーザー インターフェイスの使いやすさ、クエリの機能、使用できるカスタム視覚化とダッシュボード、監視エクスペリエンスを強化するために提供される追加ツールなどです。 

## <a name="next-steps"></a>次のステップ

* アプリケーションのインストルメント化の開始については、[アプリケーション レベルのイベントとログの生成](service-fabric-diagnostics-event-generation-app.md)に関するページを参照してください。
* アプリケーションの Application Insights を設定する手順をについては、「[Service Fabric での ASP.NET Core アプリケーションの監視と診断](service-fabric-tutorial-monitoring-aspnet.md)」をご覧ください。
* Service Fabric が提供するプラットフォームおよびイベント監視の詳細については、[プラットフォーム レベルのイベントとログの生成](service-fabric-diagnostics-event-generation-infra.md)に関するページを参照してください。
* 「[クラスター用に Azure Monitor ログを設定する](service-fabric-diagnostics-oms-setup.md)」で、Azure Monitor ログと Service Fabric との統合を構成します。
* コンテナーを監視するために Azure Monitor ログを設定する方法については、「[Azure Service Fabric での Windows コンテナーの監視と診断](service-fabric-tutorial-monitoring-wincontainers.md)」をご覧ください。
* Service Fabric での診断の問題と解決策の例については、[一般的なシナリオの診断](service-fabric-diagnostics-common-scenarios.md)に関する記事をご覧ください
* Service Fabric と統合する他の診断製品については、[Service Fabric 診断パートナー](service-fabric-diagnostics-partners.md)に関する記事をご覧ください
* Azure リソースの一般的な監視の推奨事項については、[監視と診断のベスト プラクティス](/azure/architecture/best-practices/monitoring)に関するページを参照してください。 
