---
title: Operations Manager の既存のお客様向けの Azure Monitor
description: Operations Manager の既存のユーザーが、クラウドへの移行の一環として特定のワークロードの監視を Azure Monitor に移行する場合のガイダンス。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/11/2021
ms.openlocfilehash: 6d92b7c2f01a7e9ef12bc2bb422cfb6ed0076f73
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102039378"
---
# <a name="azure-monitor-for-existing-operations-manager-customers"></a>Operations Manager の既存のお客様向けの Azure Monitor
この記事では、[System Center Operations Manager](/system-center/scom/welcome) を現在使用しており、ビジネス アプリケーションやその他のリソースを Azure に移行する際に [Azure Monitor](overview.md) への移行を計画しているお客様に向けたガイダンスを提供します。 最終的な目標はクラウドへの全面的な移行であり、ビジネス面と IT 運用の要件について妥協することなく、可能な限り多くの Operations Manager 機能を Azure Monitor に置き換えると想定しています。 

この記事に記載されている特定の推奨事項は、Azure Monitor と Operations Manager に機能が追加されると変化します。 ただし、基本的な方法は一貫して残るでしょう。

> [!IMPORTANT]
> ここで説明するいくつかの Azure Monitor 機能を実装するにはコストがかかります。そのため、環境全体に展開する前にその価値を評価する必要があります。

## <a name="prerequisites"></a>前提条件
この記事は、[Operations Manager](/system-center/scom) を既に使用しており、[Azure Monitor](overview.md) について少なくとも基本的知識があることを前提としています。 これら 2 つの完全な比較については、「[クラウド監視ガイド:監視プラットフォームの概要](/azure/cloud-adoption-framework/manage/monitor/platform-overview)」を参照してください。 その記事では、ここに記載されている推奨事項の一部を理解する助けになる、これら 2 つの具体的な機能の違いが詳しく説明されています。 


## <a name="general-strategy"></a>全般的な方針
プラットフォームが根本的に異なっているため、Operations Manager から Azure Monitor にアセットを変換する移行ツールはありません。 代わりに、Operations Manager を使用し続けしている間に、移行によって[標準的な Azure Monitor 実装](deploy.md)を構成します。 Azure Monitor をカスタマイズして、さまざまなアプリケーションやコンポーネントの要件を満たす場合や、より多くの機能が追加された場合は、Operations Manager で、さまざまな管理パックおよびエージェントの、インベントリからの削除を開始できます。

この記事で推奨される一般的な方針は、[クラウド監視ガイド](/azure/cloud-adoption-framework/manage/monitor/)と同じです。このガイドでは、クラウドへの段階的移行を行えるようする[ハイブリッド クラウド監視](/azure/cloud-adoption-framework/manage/monitor/cloud-models-monitor-overview#hybrid-cloud-monitoring)の方針が勧められています。 一部の機能が重複する可能性もありますが、この方針では、新しいプラットフォームに慣れながら既存のビジネス プロセスを維持できます。 Operations Manager の機能から離れるのは、Azure Monitor に置き換え可能なときだけです。 複数の監視ツールを使用すると複雑さが増しますが、オンプレミスまたは他のクラウド内に存在する可能性があるサーバー ソフトウェアやインフラストラクチャ コンポーネントを監視する Operations Manager の機能を維持しながら、次世代のクラウド ワークロードを監視する Azure Monitor の機能を活用できます。 


## <a name="components-to-monitor"></a>監視するコンポーネント
それぞれ別個の監視方針を決定するために、監視する必要があるさまざまな種類のワークロードを分類することが役立ちます。 「[クラウド監視ガイド:監視戦略を策定する](/azure/cloud-adoption-framework/strategy/monitoring-strategy#high-level-modeling)」には、従来のエンタープライズ アプリケーションからクラウドの最新アプリケーションに移行するときに監視が必要になる、環境内のさまざまなレイヤーの詳細な内訳が示されています。

クラウド以前には、Operations Manager を使用してすべてのレイヤーを監視していました。 サービスとしてのインフラストラクチャ (IaaS) を使用して移行を開始するときは、仮想マシンについては引き続き Operations Manager を使用しますが、クラウド リソースについては Azure Monitor の使用を開始します。 その後、サービスとしてのプラットフォーム (PaaS) を使用して、最新アプリケーションに移行するときには、Azure Monitor により焦点を合わせて、Operations Manager 機能のインベントリからの削除を開始できます。


![クラウド モデル](/azure/cloud-adoption-framework/strategy/media/monitoring-strategy/cloud-models.png)

これらのレイヤーは、以下のカテゴリに単純化することができます。これらのカテゴリについては、この記事の残りの部分で説明します。 環境内のすべての監視ワークロードが、これらのカテゴリのいずれかにぴたりと該当するとは限りませんが、それぞれが、適用する一般的推奨事項の特定カテゴリに十分に近いものである必要があります。

**ビジネス アプリケーション。** 実際のビジネスに固有の機能を提供するアプリケーション。 これらは内部または外部のものである可能性があり、多くの場合、カスタム コードを使用して内部で開発されています。 レガシ アプリケーションは、通常、Windows または Linux のいずれかを実行している仮想マシンまたは物理マシンでホストされますが、より新しいアプリケーションは、Azure Web Apps や Azure Functions などの Azure のアプリケーション サービスに基づくことになります。

**Azure サービス。** クラウドに移行したビジネス アプリケーションをサポートする、Azure 内のリソース。 これには、Azure Storage、Azure SQL、Azure IoT などのサービスが含まれます。 これには、他の Azure サービスと同様に監視されるために Azure 仮想マシンも含まれますが、それらの仮想マシンのゲスト オペレーティング システムで実行されているアプリケーションとソフトウェアについては、ホストを越えての監視が必要になります。

**サーバー ソフトウェア。** ビジネス アプリケーションや、ビジネスに一般的な機能を提供するパッケージ アプリケーションをサポートする仮想マシンおよび物理マシンで実行されるソフトウェア。 例には、インターネット インフォメーション サーバー (IIS)、SQL Server、Exchange、SharePoint が含まれます。 これには、仮想マシンおよび物理マシン上の Windows または Linux オペレーティング システムも含まれます。

**ローカル インフラストラクチャ。** 監視を必要とするオンプレミス環境に固有のコンポーネント。 これには、物理サーバー、ストレージ、ネットワーク コンポーネントなどのリソースが含まれます。 これらは、クラウドに移行すると仮想化されるコンポーネントです。

## <a name="sample-walkthrough"></a>サンプルのチュートリアル
以下は、Operations Manager から Azure Monitor への移行に関する架空のチュートリアルです。 これは、実際の移行の複雑さを完全に表現することを意図したものではありませんが、少なくとも基本的な手順と順序が示されています。 下のセクションで、これらの手順のそれぞれについて詳しく説明します。

いずれかのコンポーネントを Azure に移行する前の環境は、オンプレミスに配置されているか、マネージド サービス プロバイダーによって配置された、仮想マシンや物理マシンに基づいています。 これは、Operations Manager に頼って、物理サーバーやネットワークなど、環境内のビジネス アプリケーション、サーバー ソフトウェア、およびその他のインフラストラクチャ コンポーネントを監視しています。 IIS、SQL Server、さまざまなベンダー ソフトウェアなどのサーバー ソフトウェアには、標準の管理パックを使用し、特定の要件に合わせてこれらの管理パックに調整を加えます。 既存の管理パックで監視できないビジネス アプリケーションやその他のコンポーネントには、カスタム管理パックを作成し、ビジネス プロセスをサポートするように Operations Manager を構成します。

Azure への移行は IaaS を使用して始まり、ビジネス アプリケーションをサポートする仮想マシンを Azure に移行します。 これらのアプリケーションと、それらが依存するサーバー ソフトウェアの監視要件は変わらないため、既存の管理パックを使用して、これらのサーバーで引き続き Operations Manager を使用します。 

Azure サブスクリプションを作成するとすぐに、Azure のサービスに対して Azure Monitor が有効になります。 プラットフォーム メトリックとアクティビティ ログは、自動的に収集されます。また、リソース ログが収集されるように構成し、ログ クエリを使用して、利用できるすべてのテレメトリを対話的に分析できるようにします。 仮想マシンで VM insights を有効にして、環境全体にわたって監視データをまとめて分析し、マシンとプロセスとの間の関係を検出します。 オンプレミスの物理マシンと仮想マシンで Azure Arc 対応サーバーを有効にして、それらのマシンまで Azure Monitor の使用を拡張します。 

お使いのビジネス アプリケーションごとに Application Insights を有効にします。 これは、各アプリケーションのさまざまなコンポーネントを識別し、使用状況とパフォーマンスのデータの収集を開始して、発生したすべてのエラーをコードで識別します。 可用性テストを作成して、外部アプリケーションを事前にテストし、パフォーマンスや可用性の問題があればアラートを受け取ります。 Application Insights では Operations Manager にない強力な機能が提供されますが、ビジネス アプリケーション用に開発したカスタム管理パックの利用を続行します。これは、管理パックに、Azure Monitor がまだ対応していない監視シナリオが含まれるためです。 

Azure Monitor に慣れるにつれて、いくつかの管理パックの機能を置き換えることができるアラート ルールの作成を開始し、新しい監視プラットフォームを使用するためのビジネス プロセスの開発を開始します。 これで、Operations Manager 管理グループからマシンと管理パックの削除を開始できます。 重要なサーバー ソフトウェアとオンプレミスのインフラストラクチャについては、引き続き管理パックを使用しますが、追加の機能をインベントリから削除できるようになる Azure Monitor の新機能を待ち続けます。

## <a name="monitor-azure-services"></a>Azure サービスを監視する
Azure サービスでは、テレメトリを収集するために実際には Azure Monitor が必要であり、Azure サブスクリプションを作成した時点でそれが有効にされます。 サブスクリプションに対して[アクティビティ ログ](essentials/activity-log.md)が自動的に収集され、作成したすべての Azure リソースから[プラットフォーム メトリック](essentials/data-platform-metrics.md)が自動的に収集されます。 [メトリックス エクスプローラー](essentials/metrics-getting-started.md)の使用をすぐに開始できます。これは、オペレーション コンソールのパフォーマンス ビューに似ていますが、対話的な分析と、データの[高度な集計](essentials/metrics-charts.md)が可能です。 値がしきい値を超えたときに通知されるように[メトリック アラートを作成する](alerts/alerts-metric.md)か、表示のために [Azure ダッシュボードにグラフを追加します](essentials/metrics-charts.md#pinning-to-dashboards)。

[![メトリックス エクスプローラー](media/azure-monitor-operations-manager/metrics-explorer.png)](media/azure-monitor-operations-manager/metrics-explorer.png#lightbox)

各 Azure リソースの[診断設定を作成](essentials/diagnostic-settings.md)して、メトリックと[リソース ログ](essentials/resource-logs.md)を送信します。これらからは、各リソースの内部操作に関する詳細情報が、Log Analytics ワークスペースに提供されます。 これで、リソースについて利用できるすべてのテレメトリが得られ、[Log Analytics](logs/log-analytics-overview.md) を使用して、Operations Manager には同等の機能がない高度なクエリ言語を使用したログとパフォーマンス データの対話形式での分析が可能になります。 また、[ログ クエリ アラート](alerts/alerts-log-query.md)を作成できます。このアラートでは、複雑なロジックを使用してアラート発生の状態を判断し、複数のリソースにわたってデータを関連付けることができます。

[![Logs Analytics](media/azure-monitor-operations-manager/log-analytics.png)](media/azure-monitor-operations-manager/log-analytics.png#lightbox)

Azure Monitor の[分析情報](monitor-reference.md)は、特定の Azure サービスに固有の監視機能を提供するという点で、管理パックに似ています。 分析情報は、現在のところネットワーク、ストレージ、コンテナーなどのいくつかのサービスで利用でき、その他のサービスは継続的に追加されています。

[![分析情報の例](media/azure-monitor-operations-manager/insight.png)](media/azure-monitor-operations-manager/insight.png#lightbox)


分析情報は、メトリックとログ クエリを内容豊富な対話型レポートに結合する、Azure Monitor の[ブック](visualize/workbooks-overview.md)に基づいています。 オペレーション コンソールでカスタムのビューやレポートを作成する方法と同様に、複数のサービスからのデータを結合する独自のブックを作成します。

### <a name="azure-management-pack"></a>Azure 管理パック
[Azure 管理パック](https://www.microsoft.com/download/details.aspx?id=50013)を使用すると、Operations Manager で、特定の監視シナリオのセットに基づいて Azure リソースを検出し、それらの正常性を監視できます。 この管理パックでは、Azure の各リソースに対して追加の構成を実行する必要がありますが、Azure Monitor に焦点を合わせたビジネス プロセスを開発するまでは、オペレーション コンソールで Azure リソースをある程度表示できると便利な場合があります。

[![Azure 管理パック](media/azure-monitor-operations-manager/operations-console.png)](media/azure-monitor-operations-manager/operations-console.png#lightbox)

 オペレーション コンソールで一定の Azure リソースを表示したい場合に Azure 管理パックを使用し、いくつかの基本的なアラートを既存のプロセスと統合することも選択できます。 これは実際には、Azure Monitor によって収集されたデータを使用します。 ただし、Azure リソースの長期的で完全な監視については、Azure Monitor に頼る必要があります。 


## <a name="monitor-server-software-and-local-infrastructure"></a>サーバー ソフトウェアとローカル インフラストラクチャを監視する
マシンをクラウドに移動するときに、マシンのソフトウェアの監視要件は変化しません。 それらは仮想化されるため、物理コンポーネントを監視する必要はなくなりますが、ゲスト オペレーティング システムとそのワークロードには、環境とは無関係に同じ要件があります。

[VM insights](vm/vminsights-overview.md) は、仮想マシンと、そのゲスト オペレーティング システムおよびワークロードを監視するための、Azure Monitor の主要機能です。 Operations Manager と同様に、VM insights により、エージェントを使用して、仮想マシンのゲスト オペレーティング システムからデータが収集されます。 これは、分析とアラートのために一般に管理パックによって使用されるのと同じパフォーマンスおよびイベントのデータです。 ただし、これらのマシンで実行されているビジネス アプリケーションやサーバー ソフトウェアの問題を識別し、アラートを発行するための既存のルールは存在しません。 検出された問題を事前に通知するには、独自のアラート ルールを作成する必要があります。

[![VM insights のパフォーマンス](media/azure-monitor-operations-manager/vm-insights-performance.png)](media/azure-monitor-operations-manager/vm-insights-performance.png#lightbox)

Azure Monitor は、仮想マシンで実行されているさまざまなアプリケーションとサービスの正常性を測定するものでもありません。 メトリック アラートは、値がしきい値を下回ると自動的に解決されますが、Azure Monitor には、現在のところ、マシンで実行されているアプリケーションとサービスの正常性基準を定義する機能がなく、関連するコンポーネントの正常性をグループ化するための正常性ロールアップも提供されていません。

> [!NOTE]
> 新しい [VM insights のゲスト正常性機能](vm/vminsights-health-overview.md)は、現在パブリック プレビューの段階にあり、一連のパフォーマンス メトリックの正常性状態に基づいてアラートが生成されます。 ただしこれは、仮想マシンで実行されているアプリケーションやその他のワークロードではなく、ゲスト オペレーティング システムに関連する、特定のセットのパフォーマンス カウンターに限られています。
> 
> [![VM insights のゲストの正常性](media/azure-monitor-operations-manager/vm-insights-guest-health.png)](media/azure-monitor-operations-manager/vm-insights-guest-health.png#lightbox)

ハイブリッド環境でコンピューターのソフトウェアを監視するには、通常、各コンピューターの要件と、Azure Monitor 関連の運用プロセス開発の成熟度に応じて、VM insights と Operations Manager を組み合わせて使用します。 Microsoft Management Agent (Azure Monitor では Log Analytics エージェントと呼ばれます) は両方のプラットフォームで使用されるので、1 台のマシンが両方から同時に監視されるようにできます。

> [!NOTE]
> 今後、VM insights は、現在パブリック プレビューの段階にある [Azure Monitor エージェント](agents/azure-monitor-agent-overview.md)に移行する予定です。 両方のプラットフォームで引き続き同じ仮想マシンを監視できるように、Microsoft Monitoring Agent との互換性が保たれる予定です。

Azure Monitor ではまだ提供できない機能については、引き続き Operations Manager を使用します。 これには、IIS、SQL Server、Exchange などの重要なサーバー ソフトウェア用の管理パックが含まれます。 Azure Monitor ではアクセスできない、オンプレミスのインフラストラクチャ用に開発されたカスタム管理パックがある可能性もあります。 また、運用プロセスに緊密に統合されている場合は、Azure Monitor やその他の Azure サービスで拡張または置き換えが可能なサービス操作の最新化に移行できるようになるまで、引き続き Operations Manager を使用します。 

すぐに Operations Manager を置き換えない場合であっても、Azure Monitor fo Vm を使用して現在の監視を強化します。 Azure Monitor 固有の機能の例には、以下が含まれます。

- 仮想マシンとその外部依存先との間の関係を検出し、監視します。
- 対話型のグラフやブック内の複数の仮想マシンにわたって集計されたパフォーマンス データを表示します。
- [ログ クエリ](logs/log-query-overview.md)を使用して、他の Azure リソースのデータを取得し、仮想マシンからのテレメトリを対話形式で分析します。
- 複数の仮想マシンにわたる複雑なロジックに基づいて、[ログ アラート ルール](alerts/alerts-log-query.md)を作成します。

[![VM insights のマップ](media/azure-monitor-operations-manager/vm-insights-map.png)](media/azure-monitor-operations-manager/vm-insights-map.png#lightbox)

VM insights を使用すると、Azure Virtual Machines に加えて、オンプレミスのマシンや、[Azure Arc 対応サーバー](../azure-arc/servers/overview.md)を使用する他のクラウド内のマシンを、監視することができます。 Arc 対応サーバーを使用すると、Azure の外部 (企業ネットワークや Azure 以外のクラウド プロバイダー) でホストされている Windows と Linux のマシンを、ネイティブの Azure 仮想マシンと同じように管理することができます。



## <a name="monitor-business-applications"></a>ビジネス アプリケーションを監視する
一般に、Operations Manager で、各仮想マシンにインストールされるエージェントを利用してビジネス アプリケーションを監視するには、カスタム管理パックが必要です。 Azure Monitor の Application Insights では、Azure、その他のクラウド、オンプレミスのどこにあるかを問わず、Web ベースのアプリケーションの監視が行われます。そのためこれは、Azure に移行されたかどうかにかかわらず、すべてのアプリケーションに対して使用できます。

Operations Manager でのビジネス アプリケーションの監視が、[.NET アプリのパフォーマンス テンプレート]()によって提供される機能に限られる場合は、機能を失うことなく Application Insights に移行できる可能性が非常に高くなります。 実際、Application Insights には、以下のように非常に多くの追加機能が含まれる予定です。

- アプリケーション コンポーネントを自動的に検出して監視します。
- アプリケーションの使用状況とパフォーマンスに関する詳細なデータ (応答時間、失敗率、要求率など) を収集します。
- ページ ビューや読み込みパフォーマンスなどのブラウザー データを収集します。
- 例外を検出し、スタック トレースと関連する要求の詳細を表示します。
- [分散トレース](app/distributed-tracing.md)や[スマート検出](app/proactive-diagnostics.md)などの機能を使用して、高度な分析を実行します。
- [メトリックス エクスプローラー](essentials/metrics-getting-started.md)を使用して、パフォーマンス データを対話形式で分析します。
- [ログ クエリ](logs/log-query-overview.md)を使用して、収集されたテレメトリを、Azure サービスおよび VM insights 用に収集されたデータと一緒に、対話形式で分析します。

[![Application Insights](media/azure-monitor-operations-manager/application-insights.png)](media/azure-monitor-operations-manager/application-insights.png#lightbox)

ただし特定のシナリオでは、必要な機能を実現できるまで、Application Insights に加えて Operations Manager の使用を続ける必要がある場合があります。 Operations Manager を使い続ける必要がある場合の例には、以下が含まれます。

-  アプリケーションの可用性と応答性を監視してアラートを送信できるようにする[可用性テスト](app/monitor-web-app-availability.md)を行うには、Web テスト エージェントの IP アドレスからの受信要求が必要です。 このようなアクセスがポリシーで許可されない場合は、Operations Manager で [Web アプリケーションの可用性モニター](/system-center/scom/web-application-availability-monitoring-template)を使い続けることが必要な可能性があります。
- Operations Manager では、可用性テストのポーリング間隔を設定できます。多くのお客様は、60 ～ 120 秒ごとに調査を行っています。 Application Insights の最小ポーリング間隔は 5 分のため、一部のお客様にとっては長すぎる可能性があります。
- Operations Manager では、アプリケーションによって生成されたイベントを収集し、ローカル エージェントでスクリプトを実行することで、膨大な量の監視が実行されます。 Application Insights では、これらは標準オプションではないため、ビジネス要件を満たすためにカスタムの作業が必要になる場合があります。 これには、Log Analytics ワークスペースに格納されているイベント データを使用したカスタム アラート ルールと、[ハイブリッド runbook worker](../automation/automation-hybrid-runbook-worker.md) を使用して仮想マシンのゲストで起動されるスクリプトが含まれる場合があります。
- アプリケーションが記述されている言語によっては、[Application Insights で使用できるインストルメンテーション](app/platforms.md)が限られる場合があります。

このガイドの他のセクションの基本的方針に従って、ビジネス アプリケーションについては引き続き Operations Manager を使用しますが、Application Insights によって提供される追加の機能を活用します。 重要な機能を Azure Monitor に置き換えることができるため、インベントリからのカスタム管理パックの削除を開始できます。


## <a name="next-steps"></a>次の手順

- Azure Monitor と System Center Operations Manager の詳細な比較や、ハイブリッド監視環境の設計と実装の詳細については、[クラウド監視ガイド](/azure/cloud-adoption-framework/manage/monitor/)を参照してください。
- [Azure Monitor での Azure リソースの監視](essentials/monitor-azure-resource.md)の詳細を確認します。
- [Azure Monitor での Azure 仮想マシンの監視](vm/monitor-vm-azure.md)の詳細を確認します。
- [VM insights](vm/vminsights-overview.md) の詳細を確認します。
- [Application Insights](app/app-insights-overview.md) の詳細を確認します。