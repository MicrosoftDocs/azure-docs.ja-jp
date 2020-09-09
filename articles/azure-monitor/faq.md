---
title: Azure Monitor に関する FAQ | Microsoft Docs
description: Azure Monitor についてよくあるご質問とその回答です。
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/15/2020
ms.openlocfilehash: a78e1b9cc1d9ca8a815fdb586287983020232fd1
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2020
ms.locfileid: "88782942"
---
# <a name="azure-monitor-frequently-asked-questions"></a>Azure Monitor についてよくあるご質問

この Microsoft FAQ では、Azure Monitor についてよくあるご質問を紹介します。

## <a name="general"></a>全般

### <a name="what-is-azure-monitor"></a>Azure Monitor とは
[Azure Monitor](overview.md) は Azure のサービスであり、Azure、他のクラウド環境、またはオンプレミスのアプリケーションとサービスのパフォーマンスと可用性を監視する機能が提供されます。 Azure Monitor を使うと、複数のソースから共通のデータ プラットフォームにデータが収集され、傾向や異常を分析できるようになります。 Azure Monitor の豊富な機能を利用すると、アプリケーションに影響する可能性がある重大な状況を迅速に特定して対応することができます。

### <a name="whats-the-difference-between-azure-monitor-log-analytics-and-application-insights"></a>Azure Monitor、Log Analytics、Application Insights の違いは何ですか?
2018 年 9 月に、Azure Monitor、Log Analytics、Application Insights が 1 つのサービスに結合され、アプリケーションとそれらが依存するコンポーネントをエンドツーエンドで監視できるようになりました。 Log Analytics と Application Insights の機能は変更されていませんが、一部の機能は、新しい対象範囲がより適切に反映されるように、Azure Monitor にブランド変更されています。 Log Analytics のログデータ エンジンとクエリ言語は、Azure Monitor ログと呼ばれるようになっています。 [Azure Monitor の用語の更新](terminology.md)に関するページをご覧ください。

### <a name="what-does-azure-monitor-cost"></a>Azure Monitor のコストはどれくらいですか?
メトリックやアクティビティ ログの収集など、自動的に有効になる Azure Monitor の機能は、無料で提供されます。 ログ クエリやアラートなどの他の機能については、関連コストが発生します。 詳細な価格については、[Azure Monitor の価格に関するページ](https://azure.microsoft.com/pricing/details/monitor/)をご覧ください。

### <a name="how-do-i-enable-azure-monitor"></a>Azure Monitor を有効にするにはどうすればよいですか?
Azure Monitor は新しい Azure サブスクリプションを作成した時点で有効になり、[アクティビティ ログ](./platform/platform-logs-overview.md)とプラットフォーム [メトリック](platform/data-platform-metrics.md)が自動的に収集されます。 Azure リソースの動作に関するさらに詳細な情報を収集するには[診断設定](platform/diagnostic-settings.md)を作成し、特定のサービスについて収集されたデータについて追加の分析を提供するには[監視ソリューション](insights/solutions.md)と[分析情報](insights/insights-overview.md)を追加します。 

### <a name="how-do-i-access-azure-monitor"></a>Azure Monitor にアクセスするにはどうすればよいですか?
Azure Monitor のすべての機能とデータには、Azure portal の **[モニター]** メニューからアクセスします。 さまざまな Azure サービスのメニューの **[監視]** セクションでは、特定のリソースに対してフィルター処理されたデータを使用して、同じツールにアクセスできます。 Azure Monitor データには、CLI、PowerShell、REST API を使用したさまざまなシナリオでもアクセスできます。

### <a name="is-there-an-on-premises-version-of-azure-monitor"></a>オンプレミス バージョンの Azure Monitor はありますか?
いいえ。 Azure Monitor は大量のデータを処理して格納するスケーラブルなクラウド サービスですが、Azure Monitor ではオンプレミスまたは他のクラウドのリソースを監視することができます。

### <a name="can-azure-monitor-monitor-on-premises-resources"></a>Azure Monitor でオンプレミスのリソースを監視することはできますか?
はい。Azure リソースから監視データを収集するだけでなく、Azure Monitor では、他のクラウドやオンプレミスの仮想マシンやアプリケーションからデータを収集することができます。 「[Azure Monitor で使用する監視データのソース](platform/data-sources.md)」をご覧ください。

### <a name="does-azure-monitor-integrate-with-system-center-operations-manager"></a>Azure Monitor と System Center Operations Manager を統合することはできますか?
System Center Operations Manager の既存の管理グループを Azure Monitor に接続して、エージェントからのデータを Azure Monitor ログに収集することができます。 これにより、ログ クエリとソリューションを使用して、エージェントから収集されたデータを分析することができます。 また、Azure Monitor にデータを直接送信するように、既存の System Center Operations Manager エージェントを構成することもできます。 「[Operations Manager を Azure Monitor に接続する](platform/om-agents.md)」を参照してください。

### <a name="what-ip-addresses-does-azure-monitor-use"></a>Azure Monitor ではどのような IP アドレスが使用されますか?
エージェントと他の外部リソースが Azure Monitor にアクセスするために必要な IP アドレスとポートの一覧については、「[Application Insights および Log Analytics によって使用される IP アドレス](app/ip-addresses.md)」をご覧ください。 

## <a name="monitoring-data"></a>データの監視

### <a name="where-does-azure-monitor-get-its-data"></a>Azure Monitor はどこでデータを取得しますか?
Azure Monitor では、Azure Platform とリソース、カスタム アプリケーション、仮想マシン上で実行されているエージェントからのログやメトリックなど、さまざまなソースのデータが収集されます。 Azure Security Center や Network Watcher などの他のサービスでは、データが Log Analytics ワークスペースに収集されるので、Azure Monitor のデータで分析できます。 また、ログまたはメトリックの REST API を使用して、Azure Monitor にカスタム データを送信することもできます。 「[Azure Monitor で使用する監視データのソース](platform/data-sources.md)」をご覧ください。

### <a name="what-data-is-collected-by-azure-monitor"></a>Azure Monitor ではどのようなデータが収集されますか? 
Azure Monitor では、さまざまなソースからのデータが[ログ](platform/data-platform-logs.md)または[メトリック](platform/data-platform-metrics.md)に収集されます。 各データの種類には、独自の相対的利点があり、それぞれによって Azure Monitor の特定の機能セットがサポートされます。 Azure サブスクリプションごとに 1 つのメトリック データベースがありますが、複数の Log Analytics ワークスペースを作成し、要件に応じてログを収集することができます。 「[Azure Monitor データ プラットフォーム](platform/data-platform.md)」をご覧ください。

### <a name="is-there-a-maximum-amount-of-data-that-i-can-collect-in-azure-monitor"></a>Azure Monitor で収集できるデータに最大量はありますか?
収集できるメトリック データの量に制限はありませんが、このデータは最大 93 日間保持されます。 「[メトリックの保有期間](platform/data-platform-metrics.md#retention-of-metrics)」をご覧ください。 収集できるログ データの量に制限はありませんが、Log Analytics ワークスペースに対して選択した価格レベルによって影響を受ける可能性があります。 [価格の詳細](https://azure.microsoft.com/pricing/details/monitor/)を参照してください。

### <a name="how-do-i-access-data-collected-by-azure-monitor"></a>Azure Monitor によって収集されたデータにアクセスするにはどうすればよいですか?
分析情報とソリューションにより、Azure Monitor に格納されているデータを操作するためのカスタム エクスペリエンスが提供されます。 Kusto クエリ言語 (KQL) で記述たログ クエリを使用して、ログ データを直接操作できます。 Azure portal では、Log Analytics を使用してクエリを記述して実行し、対話形式でデータを分析できます。 Azure portal では、メトリックス エクスプローラーを使用してメトリックを分析します。 [Azure Monitor でログ データを分析する](log-query/log-query-overview.md)方法に関するページ、および「[Azure メトリックス エクスプローラーの概要](platform/metrics-getting-started.md)」のページをご覧ください。

## <a name="solutions-and-insights"></a>ソリューションと分析情報

### <a name="what-is-an-insight-in-azure-monitor"></a>Azure Monitor での分析情報とはどのようなものですか?
分析情報では、特定の Azure サービスに対するカスタマイズされた監視エクスペリエンスが提供されます。 Azure Monitor の他の機能と同じメトリックとログが使用されますが、追加データを収集し、Azure portal で独自のエクスペリエンスを提供することができます。 [Azure Monitor での分析情報](insights/insights-overview.md)に関する記事をご覧ください。

Azure portal で分析情報を表示するには、 **[モニター]** メニューの **[分析情報]** セクション、またはサービスのメニューの **[監視]** セクションを使用します。

### <a name="what-is-a-solution-in-azure-monitor"></a>Azure Monitor でのソリューションとは何ですか?
監視ソリューションは、Azure Monitor の機能に基づいて特定のアプリケーションまたはサービスを監視するためのロジックのパッケージ化されたセットです。 Azure Monitor のログ データが収集され、Azure portal の一般的なエクスペリエンスを使用して、分析のためのログ クエリとビューが提供されます。 「[Azure Monitor での監視ソリューション](insights/solutions.md)」をご覧ください。

Azure portal でソリューションを表示するには、 **[モニター]** メニューの **[分析情報]** セクションで **[詳細]** をクリックします。 **[追加]** をクリックして、ワークスペースに新しいソリューションを追加します。

## <a name="logs"></a>ログ

### <a name="whats-the-difference-between-azure-monitor-logs-and-azure-data-explorer"></a>Azure Monitor ログと Azure Data Explorer の違いは何ですか?
Azure Data Explorer は、ログと利用統計情報データのための高速で拡張性に優れたデータ探索サービスです。 Azure Monitor ログは、Azure Data Explorer を基にして構築されており、若干の違いはありますが同じ Kusto クエリ言語 (KQL) を使用します。 「[Azure Monitor ログ クエリ言語の違い](log-query/data-explorer-difference.md)」をご覧ください。

### <a name="how-do-i-retrieve-log-data"></a>ログ データはどのようにして取得しますか?
すべてのデータは、Kusto クエリ言語 (KQL) で記述したログ クエリを使用して、Log Analytics ワークスペースから取得します。 独自のクエリを記述したり、特定のアプリケーションまたはサービス用のログ クエリが含まれるソリューションや分析情報を使用したりできます。 「[Azure Monitor のログ クエリの概要](log-query/log-query-overview.md)」をご覧ください。

### <a name="can-i-delete-data-from-a-log-analytics-workspace"></a>Log Analytics ワークスペースのデータは削除できますか?
データは、ワークスペースの[保有期間](platform/manage-cost-storage.md#change-the-data-retention-period)に従って削除されます。 プライバシーやコンプライアンス上の理由から、特定のデータを削除することが可能です。 詳細については、「[プライベート データをエクスポートして削除する方法](platform/personal-data-mgmt.md#how-to-export-and-delete-private-data)」を参照してください。


### <a name="what-is-a-log-analytics-workspace"></a>Log Analytics ワークスペースとはどのようなものですか?
Azure Monitor によって収集されたすべてのログ データは、Log Analytics ワークスペースに保存されます。 ワークスペースは、基本的に、さまざまなソースからログ データが収集されるコンテナーです。 すべての監視データに対して 1 つの Log Analytics ワークスペースを使用する場合や、複数のワークスペースが必要な場合があります。 「[Azure Monitor ログのデプロイの設計](platform/design-logs-deployment.md)」をご覧ください。

### <a name="can-you-move-an-existing-log-analytics-workspace-to-another-azure-subscription"></a>既存の Log Analytics ワークスペースを別の Azure サブスクリプションに移動できますか?
リソース グループ間またはサブスクリプション間でワークスペースを移動することはできますが、別のリージョンに移動することはできません。 「[Log Analytics ワークスペースを別のサブスクリプションまたはリソース グループに移動する](platform/move-workspace.md)」をご覧ください。

### <a name="why-cant-i-see-query-explorer-and-save-buttons-in-log-analytics"></a>Log Analytics でクエリ エクスプローラーと [保存] ボタンが表示されないのはなぜですか?

**クエリ エクスプローラー**、 **[保存]** および **[新しいアラート ルール]** ボタンは、[クエリ スコープ](log-query/scope.md)が特定のリソースに設定されている場合は使用できません。 アラートを作成し、クエリを保存または読み込むには、Log Analytics のスコープをワークスペースに指定する必要があります。 ワークスペース コンテキストで Log Analytics を開くには、 **[Azure Monitor]** メニューの **[ログ]** を選択します。 最後に使用されたワークスペースが選択されますが、その他のワークスペースを選択することはできます。 「[Azure Monitor Log Analytics のログ クエリのスコープと時間範囲](log-query/scope.md)」を参照してください

### <a name="why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-when-opening-log-analytics-from-a-vm"></a>エラーが発生する理由:VM から Log Analytics を開くときに、"このサブスクリプションのリソース プロバイダー 'Microsoft.Insights' を登録してこのクエリを有効にしてください" というエラーが表示されるのはなぜですか? 
多くのリソース プロバイダーは自動的に登録されますが、一部のリソース プロバイダーは手動で登録することが必要な場合があります。 登録の範囲は常にサブスクリプションです。 詳細については、「[リソース プロバイダーと種類](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)」を参照してください。

### <a name="why-am-i-am-getting-no-access-error-message-when-opening-log-analytics-from-a-vm"></a>VM から Log Analytics を開くときに、アクセス権なしというエラー メッセージが表示されるのはなぜですか? 
VM ログを表示するには、VM ログを格納するワークスペースに対する読み取りアクセス許可が付与される必要があります。 このような場合、管理者が Azure でのアクセス許可を付与する必要があります。

## <a name="metrics"></a>メトリック

### <a name="why-are-metrics-from-the-guest-os-of-my-azure-virtual-machine-not-showing-up-in-metrics-explorer"></a>Azure 仮想マシンのゲスト OS のメトリックがメトリックス エクスプローラーに表示されないのはなぜですか?
[プラットフォーム メトリック](insights/monitor-azure-resource.md#monitoring-data)は、Azure リソースについて自動的に収集されます。 ただし、仮想マシンのゲスト OS からメトリックを収集するには、いくつかの構成を実行する必要があります。 Windows VM の場合は、「[Install and configure Windows Azure Diagnostics 拡張機能 (WAD) のインストールと構成](platform/diagnostics-extension-windows-install.md)」の説明に従って、診断拡張機能をインストールし、Azure Monitor シンクを構成します。 Linux の場合は、「[Linux VM のカスタム メトリックを InfluxData Telegraf エージェントを使用して収集する](platform/collect-custom-metrics-linux-telegraf.md)」の説明に従って、Telegraf エージェントをインストールします。

## <a name="alerts"></a>警告

### <a name="what-is-an-alert-in-azure-monitor"></a>Azure Monitor でのアラートとはどのようなものですか?
アラートは、監視データで重要な状態が見つかると事前に通知します。 管理者は、その通知を見て、システムのユーザーが問題に気付く前に問題を識別して対処できます。 アラートには、次のように複数の種類があります。

- メトリック - メトリックの値が、しきい値を超えました。
- ログ クエリ - ログ クエリの結果が、定義された条件と一致しました。
- アクティビティ ログ - アクティビティ ログのイベントが、定義された条件と一致しました。
- Web テスト - 可用性テストの結果が、定義された条件と一致しました。


「[Microsoft Azure のアラートの概要](platform/alerts-overview.md)」をご覧ください。


### <a name="what-is-an-action-group"></a>アクション グループとはどのようなものですか?
アクション グループとは、アラートによってトリガーできる通知とアクションのコレクションです。 複数のアラートで 1 つのアクション グループを使用して、通知とアクションの共通セットを利用できます。 「[Azure portal でのアクション グループの作成および管理](platform/action-groups.md)」をご覧ください。


### <a name="what-is-an-action-rule"></a>アクション ルールとはどのようなものですか?
アクション ルールを使用すると、特定の条件に一致する一連のアラートの動作を変更できます。 これにより、メンテナンス期間中にアラート アクションを無効にするなどの要件を実行できます。 また、アラート ルールにアラートを直接適用するのではなく、一連のアラートにアクション グループを適用することもできます。 [アクション ルール](platform/alerts-action-rules.md)に関するページをご覧ください。

## <a name="agents"></a>エージェント

### <a name="does-azure-monitor-require-an-agent"></a>Azure Monitor ではエージェントは必要ですか?
エージェントは、仮想マシン内のオペレーティング システムおよびワークロードからデータを収集するためにのみ必要です。 仮想マシンは、Azure、別のクラウド環境、またはオンプレミスのどこに存在していてもかまいません。 「[Azure Monitor エージェントの概要](platform/agents-overview.md)」をご覧ください。


### <a name="whats-the-difference-between-the-azure-monitor-agents"></a>Azure Monitor エージェントの間にはどのような違いがありますか?
Azure 診断拡張機能は Azure Virtual Machines 用であり、データは Azure Monitor メトリック、Azure Storage、Azure Event Hubs に収集されます。 Log Analytics エージェントは、Azure、別のクラウド環境、またはオンプレミスの仮想マシン用であり、データは Azure Monitor ログに収集されます。 Dependency Agent には、Log Analytics エージェントと、収集されるプロセスの詳細と依存関係が必要です。 「[Azure Monitor エージェントの概要](platform/agents-overview.md)」をご覧ください。


### <a name="does-my-agent-traffic-use-my-expressroute-connection"></a>エージェントのトラフィックでは、ExpressRoute の接続が使用されますか?
Azure Monitor へのトラフィックでは、Microsoft ピアリングの ExpressRoute 回線が使用されます。 さまざまな種類の ExpressRoute トラフィックについては、[ExpressRoute のドキュメント](../expressroute/expressroute-faqs.md#supported-services)をご覧ください。 

### <a name="how-can-i-confirm-that-the-log-analytics-agent-is-able-to-communicate-with-azure-monitor"></a>Log Analytics エージェントが Azure Monitor と通信できることを確認するにはどうすればよいですか?
エージェント コンピューターのコントロール パネルで、 **[セキュリティ設定]** 、**[Microsoft Monitoring Agent] の順に選択します。 **[Azure Log Analytics (OMS)]** タブで、緑色のチェック マーク アイコンは、エージェントが Azure Monitor と通信できることを示します。 黄色の警告アイコンは、エージェントに問題があることを示します。 一般的な原因の 1 つは、**Microsoft Monitoring Agent** サービスが停止したことです。 サービス コントロール マネージャーを使用してサービスを再開します。

### <a name="how-do-i-stop-the-log-analytics-agent-from-communicating-with-azure-monitor"></a>Log Analytics エージェントと Azure Monitor の通信を停止するにはどうすればよいですか?
Log Analytics に直接接続されているエージェントの場合は、[コントロール パネル] を開き、 **[システムとセキュリティ]** 、 **[Microsoft Monitoring Agent]** の順に選択します。 **[Azure Log Analytics (OMS)]** タブで、一覧に表示されているすべてのワークスペースを削除します。 System Center Operations Manager では、Log Analytics マネージド コンピューターの一覧からコンピューターを削除します。 Operations Manager はエージェントの構成を更新して、Log Analytics に報告しなくなるようにします。 

### <a name="how-much-data-is-sent-per-agent"></a>エージェントごとにどれくらいのデータが送信されますか?
エージェントごとに送信されるデータ量は、次のものによって異なります。

* 有効にしているソリューション
* 収集されているログとパフォーマンス カウンターの数
* ログ内のデータ量

「[Azure Monitor ログで使用量とコストを管理する](platform/manage-cost-storage.md)」をご覧ください。

WireData エージェントを実行できるコンピューターの場合、どれくらいのデータが送信されているかを確認するには次のクエリを使用します。

```Kusto
WireData
| where ProcessName == "C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe" 
| where Direction == "Outbound" 
| summarize sum(TotalBytes) by Computer 
```

### <a name="how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-azure-monitor"></a>データを Azure Monitor に送信するときに、どれくらいのネットワーク帯域幅が Microsoft Management Agent (MMA) によって使用されますか?
帯域幅は、送信されるデータ量に関する機能です。 データは、ネットワーク経由で送信されるときに圧縮されます。


### <a name="how-can-i-be-notified-when-data-collection-from-the-log-analytics-agent-stops"></a>Log Analytics エージェントからのデータ収集が停止したときに通知を受け取るにはどうすればよいですか?

データ収集が停止したときに通知を受けるには、[新しいログ アラートの作成](platform/alerts-metric.md)に関する記事で説明されている手順を使用します。 アラート ルールの次の設定を使用します。

- **[アラートの条件を定義します]** : リソース ターゲットとして Log Analytics ワークスペースを指定します。
- **[アラートの条件]** 
   - **[シグナル名]** : "*カスタム ログ検索*"
   - **[検索クエリ]** : `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **[アラート ロジック]** : **[基準]** : "*結果の数*"、 **[条件]** : "*より大きい*"、 **[しきい値]** : *0*
   - **[評価基準]** : **[期間 (分単位)]** : *30*、 **[頻度 (分単位)]** : *10*
- **[アラートの詳細を定義します]** 
   - **Name**:"*データ収集が停止した*"
   - **[重大度]** :*警告*

既存または新規の[アクション グループ](platform/action-groups.md)を指定して、ログ アラートが条件に一致する場合に、ハートビートが 15 分以上なければ通知が送られるようにします。


### <a name="what-are-the-firewall-requirements-for-azure-monitor-agents"></a>Azure Monitor エージェントにはファイアウォールに関するどのような要件がありますか?
ファイアウォールの要件について詳しくは、「[ネットワーク ファイアウォールの要件](platform/log-analytics-agent.md#network-requirements)」をご覧ください。


## <a name="visualizations"></a>視覚化

### <a name="why-cant-i-see-view-designer"></a>ビュー デザイナーを表示できないのはなぜですか?

ビュー デザイナーは、 Log Analytics ワークスペース内で共同作成者以上のアクセス許可が割り当てられているユーザーのみが使用できます。

## <a name="application-insights"></a>Application Insights

### <a name="configuration-problems"></a>構成の問題
*次のセットアップで問題が発生しています。*

* [.NET アプリ](app/asp-net-troubleshoot-no-data.md)
* [既に実行中のアプリの監視](app/monitor-performance-live-website-now.md#troubleshoot)
* [Azure Diagnostics](platform/diagnostics-extension-to-application-insights.md)
* [Java Web アプリ](app/java-troubleshoot.md)

*サーバーからデータを取得できません。*

* [ファイアウォール例外の設定](app/ip-addresses.md)
* [ASP.NET サーバーのセットアップ](app/monitor-performance-live-website-now.md)
* [Java サーバーのセットアップ](app/java-agent.md)

"*デプロイする必要がある Application Insights リソースの数。* "

* [Application Insights のデプロイを設計する方法:1 つまたは多数の Application Insights リソース](app/separate-resources.md)

### <a name="can-i-use-application-insights-with-"></a>Application Insights と共に使用できるもの

* [Azure VM または Azure 仮想マシン スケール セットの IIS サーバー上の Web アプリ](app/azure-vm-vmss-apps.md)
* [オンプレミスの IIS サーバーまたは VM 内で動作する Web アプリ](app/asp-net.md)
* [Java Web アプリ](app/java-get-started.md)
* [Node.js アプリ](app/nodejs.md)
* [Azure で動作する Web アプリ](app/azure-web-apps.md)
* [Azure の Cloud Services](app/cloudservices.md)
* [Docker で実行中のアプリ サーバー](app/docker.md)
* [単一ページの Web アプリ](app/javascript.md)
* [SharePoint](app/sharepoint.md)
* [Windows デスクトップ アプリ](app/windows-desktop.md)
* [その他のプラットフォーム](app/platforms.md)

### <a name="is-it-free"></a>これは無料ですか。

はい、試験段階用です。 Basic 価格プランでは、アプリケーションは毎月無料で一定のデータ許容量を送信できます。 無料許容量は、開発や、少数のユーザー向けにアプリを発行するのに十分な大きさです。 指定した以上のデータ量が処理されることを防ぐために上限を設定できます。

上限を超える量のテレメトリは GB 単位で課金されます。 課金額を制限する方法について、[こちら](app/pricing.md)でいくつかのヒントを紹介しています。

Enterprise プランでは、テレメトリを送信した Web サーバー ノードごとに、1 日単位で料金が請求されます。 このプランは、連続エクスポートを大規模に使用する場合に適しています。

[価格プランを参照してください](https://azure.microsoft.com/pricing/details/application-insights/)。

### <a name="how-much-does-it-cost"></a>コストはどれくらいですか。

* Application Insights リソースで **[使用量と推定コスト] ページ**を開きます。 最近の利用状況のグラフが表示されます。 必要に応じて、データ量の上限を設定できます。
* [Azure の [課金] ブレード](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview)を開き、リソース全体での請求額を確認します。

### <a name="what-does-application-insights-modify-in-my-project"></a><a name="q14"></a>Application Insights によってどのような変更がプロジェクトに加えられますか?
詳細は、プロジェクトの種類によって異なります。 Web アプリケーションの場合:

* 次のファイルがプロジェクトに追加されます。
  * ApplicationInsights.config
  * ai.js
* これらの NuGet パッケージをインストールします。
  * *Application Insights API* - コア API
  * *Application Insights API for Web Applications* - サーバーからテレメトリを送信するために使用されます
  * *Application Insights API for JavaScript Applications* - クライアントからテレメトリを送信するために使用されます
* パッケージには、これらのアセンブリが含まれます。
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* 次の項目を挿入します。
  * web.config
  * packages.config
* (新しいプロジェクトのみ。[Application Insights を既存のプロジェクトに追加している場合][start]は、手動でこの操作を行う必要があります)。これらを Application Insights リソース ID で初期化するためのスニペットを、クライアントとサーバーのコードに挿入します。 たとえば、MVC アプリでは、Views/Shared/\_Layout.cshtml マスター ページにコードが挿入されます。

### <a name="how-do-i-upgrade-from-older-sdk-versions"></a>以前のバージョンの SDK からアップグレードする方法。
お使いのアプリケーションに適切な SDK については、「 [リリース ノート](app/release-notes.md) 」をご覧ください。

### <a name="how-can-i-change-which-azure-resource-my-project-sends-data-to"></a><a name="update"></a>自分のプロジェクトがデータを送信する Azure のリソースを変更するにはどうすればいいですか?
ソリューション エクスプローラーで、 `ApplicationInsights.config` を右クリックし、 **[Application Insights の更新]** を選択します。 Azure の既存または新規のリソースにデータを送信できます。 更新ウィザードでは、サーバー SDK のデータの送信先を決定する、ApplicationInsights.config のインストルメンテーション キーを変更します。 [すべて更新] を選択解除している場合を除き、Web ページ内のキーが表示される場所でもキーが変更されます。

### <a name="can-i-use-providersmicrosoftinsights-componentsapiversions0-in-my-azure-resource-manager-deployments"></a>Azure Resource Manager のデプロイで `providers('Microsoft.Insights', 'components').apiVersions[0]` を使用できますか?

API バージョンの設定に、この方法を使用することは推奨されません。 最新バージョンは、破壊的変更を含んでいる可能性があるプレビュー リリースを表す場合があります。 新しいプレビュー以外のリリースでも、API バージョンが必ずしも既存のテンプレートと下位互換性を持っているとは限りません。場合によっては、API バージョンがすべてのサブスクリプションで使用できない可能性もあります。

### <a name="what-is-status-monitor"></a>Status Monitor とは何ですか?

IIS Web サーバーで Web アプリ内の Application Insights を構成するために使用できるデスクトップ アプリです。 テレメトリは収集されないので、アプリの構成中以外は停止しておくことができます。 

[詳細については、こちらを参照してください](app/monitor-performance-live-website-now.md#questions)。

### <a name="what-telemetry-is-collected-by-application-insights"></a>Application Insights では、どのようなテレメトリが収集されますか?

サーバーの Web アプリから:

* HTTP 要求
* [依存関係](app/asp-net-dependencies.md)。 SQL Database の呼び出し、外部サービスの HTTP 呼び出し、Azure Cosmos DB、テーブル、Blob Storage、およびキューの呼び出し。 
* [例外](app/asp-net-exceptions.md)とスタック トレース。
* [パフォーマンス カウンター](app/performance-counters.md) - [Status Monitor](app/monitor-performance-live-website-now.md)、[Azure monitoring for App Services](app/azure-web-apps.md)、[Azure monitoring for VM または仮想マシン スケール セット](app/azure-vm-vmss-apps.md)、または[Application Insights collectd ライター](app/java-collectd.md)を使用している場合。
* コード化する[カスタム イベントとカスタム メトリック](app/api-custom-events-metrics.md)。
* [トレース ログ](app/asp-net-trace-logs.md) (適切なコレクターを構成する場合)。

[クライアントの Web ページ](app/javascript.md)から:

* [ページ ビュー数](app/usage-overview.md)
* [AJAX 呼び出し](app/asp-net-dependencies.md)。実行中のスクリプトから行われる要求。
* ページ ビューの読み込みデータ
* ユーザー数とセッション数
* [認証されたユーザー ID](app/api-custom-events-metrics.md#authenticated-users)

その他のソースから (構成する場合):

* [Azure Diagnostics](platform/diagnostics-extension-to-application-insights.md)
* [Analytics へのインポート](platform/data-collector-api.md)
* [Log Analytics](platform/data-collector-api.md)
* [Logstash](platform/data-collector-api.md)

### <a name="can-i-filter-out-or-modify-some-telemetry"></a>一部のテレメトリを除外または変更することはできますか?

はい。サーバーで以下のようなものを記述できます。

* 選択されたテレメトリ項目に対して、それがアプリから送信される前にフィルター処理やプロパティの追加を行うテレメトリ プロセッサ。
* テレメトリの全項目にプロパティを追加するテレメトリ初期化子。

ASP.NET の場合は[こちら](app/api-filtering-sampling.md)、Java の場合は[こちら](app/java-filter-telemetry.md)で詳細を確認してください。

### <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>市区町村や国や地域などの geo ロケーション データはどのように計算されますか?

Web クライアントの IP アドレス (IPv4 または IPv6) の検索に [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/) を使用しています。

* ブラウザー テレメトリ:送信者の IP アドレスを収集します。
* サーバー テレメトリ:Application Insights モジュールでクライアントの IP アドレスが収集されます。 `X-Forwarded-For` が設定されている場合は収集されません。
* Application Insights で IP アドレスと geo ロケーション データが収集される方法の詳細については、こちらの[記事](./app/ip-collection.md)を参照してください。


別のヘッダーから IP アドレスを取得するように `ClientIpHeaderTelemetryInitializer` を構成できます。 たとえば一部のシステムでは、プロキシ、ロード バランサー、または CDN によって IP アドレスが `X-Originating-IP` に移動されます。 [詳細については、こちらを参照してください](https://apmtips.com/posts/2016-07-05-client-ip-address/)。

[Power BI を使用する](app/export-power-bi.md )と、要求テレメトリを地図上に表示できます。


### <a name="how-long-is-data-retained-in-the-portal-is-it-secure"></a><a name="data"></a>ポータルでのデータ保持期間はどのくらいですか? セキュリティで保護されていますか?
[データの保持とプライバシー][data]に関するページをご覧ください。

### <a name="what-happens-to-application-insights-telemetry-when-a-server-or-device-loses-connection-with-azure"></a>サーバーまたはデバイスが Azure との接続を失った場合、Application insights のテレメトリはどうなりますか?

Web SDK を含むすべての SDK には、"信頼できるトランスポート" または "堅牢なトランスポート" が含まれています。 サーバーまたはデバイスが Azure との接続を失った場合、テレメトリは[ローカルにファイル システム](./app/data-retention-privacy.md#does-the-sdk-create-temporary-local-storage) (サーバー SDK) に、または HTML5 セッション ストレージ (Web SDK) に格納されます。 SDK は、インジェスト サービスが "古い" と見なすまで (ログの場合は 48 時間、メトリックの場合は 30 分)、このテレメトリの送信を定期的に再試行します。 古いテレメトリは削除されます。 ローカル ストレージがいっぱいになった場合など、場合によっては再試行は行われません。


### <a name="could-personal-data-be-sent-in-the-telemetry"></a>テレメトリで個人データが送信されることはありますか?

コードでそのようなデータを送信する場合は可能性があります。 また、スタック トレース内の変数に個人データが含まれている場合にも、送信される可能性があります。 その個人データの処理が適切に行われるように、開発チームでリスク評価を実施する必要があります。 [こちら](app/data-retention-privacy.md)から、データ リテンション期間とプライバシーについての詳細を確認してください。

geo ロケーション属性の検索後、クライアント Web アドレスの**すべて**のオクテットは常に 0 に設定されます。

### <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>Web ページのソースに自分のインストルメンテーション キーが表示されます。 

* 監視ソリューションにおいてはよくあることです。
* iKey を使用してデータを盗むことはできません。
* データ スキューやアラートのトリガーに使用される可能性はあります。
* これまでに、お客様がそのような問題に遭遇したという事例は報告されていません。

以下のような方法で対処できます。

* クライアント データとサーバー データに 2 つの独立したインストルメンテーション キー (別々の Application Insights リソース) を使用します。 または
* サーバーで実行するためのプロキシを記述し、Web クライアントからそのプロキシを介してデータを送信します。

### <a name="how-do-i-see-post-data-in-diagnostic-search"></a><a name="post"></a>診断検索で POST データを表示する方法を教えてください。
POST データは自動ではログに記録されませんが、TrackTrace 呼び出しを使用してメッセージ パラメーターにデータを格納できます。 文字列プロパティの制限よりもサイズ制限は大きいですが、フィルター処理には使用できません。

### <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Application Insights リソースは、単一リソースと複数リソースのどちらを使用すべきですか?

1 つのビジネス システム内のすべてのコンポーネントまたはロールに対しては、単一リソースを使用します。 開発、テスト、およびリリースのバージョン、および独立したアプリケーションに対しては、複数のリソースを使用します。

* [こちら](app/separate-resources.md)の説明を参照してください
* [例 - worker ロールと web ロールを使用したクラウド サービス](app/cloudservices.md)

### <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>インストルメンテーション キーを動的に変更するには、どうすればよいですか?

* [こちら](app/separate-resources.md)で説明しています
* [例 - worker ロールと web ロールを使用したクラウド サービス](app/cloudservices.md)

### <a name="what-are-the-user-and-session-counts"></a>ユーザー数とセッション数とは何ですか?

* JavaScript SDK では、Web クライアントにユーザー Cookie を設定することで戻ってきたユーザーを識別し、セッション Cookie を設定することでグループ アクティビティを識別します。
* クライアント側のスクリプトがない場合は、[サーバーで Cookie を設定](https://apmtips.com/posts/2016-07-09-tracking-users-in-api-apps/)できます。
* 1 人の実在するユーザーが、複数の異なるブラウザーや、プライベート/シークレット ブラウズ、または複数のコンピューターでサイトを利用した場合、それらは複数のユーザーとしてカウントされます。
* 複数のコンピューターやブラウザー間でログイン済みのユーザーを識別するには、[setAuthenticatedUserContext()](app/api-custom-events-metrics.md#authenticated-users) の呼び出しを追加します。

### <a name="have-i-enabled-everything-in-application-insights"></a><a name="q17"></a> Application Insights の機能をすべて有効にしているでしょうか?
| 表示内容 | 表示方法 | 用途 |
| --- | --- | --- |
| 可用性グラフ |[Web テスト](app/monitor-web-app-availability.md) |Web アプリが稼働しているか確認する |
| サーバー アプリ パフォーマンス: 応答時間、... |[Application Insights をプロジェクトに追加する](app/asp-net.md)か、[AI Status Monitor をサーバーにインストールする](app/monitor-performance-live-website-now.md) (または独自のコードを記述して[依存関係を追跡する](app/api-custom-events-metrics.md#trackdependency)) |パフォーマンスの問題を検出する |
| 依存関係テレメトリ |[AI Status Monitor をサーバーにインストールする](app/monitor-performance-live-website-now.md) |データベースや、その他の外部コンポーネントの問題を診断する |
| 例外からスタック トレースを取得する |[コード内に TrackException 呼び出しを挿入する](app/asp-net-exceptions.md) (自動で報告されるものもある) |例外を検出して診断する |
| ログ トレースの検索 |[ログ アダプターを追加する](app/asp-net-trace-logs.md) |例外、パフォーマンスの問題を診断する |
| クライアントの利用状況の基本情報: ページ ビュー、セッション、... |[Web ページの JavaScript の初期化子](app/javascript.md) |Usage analytics |
| クライアントのカスタム メトリック |[Web ページでの呼び出しの追跡](app/api-custom-events-metrics.md) |ユーザー エクスペリエンスを向上させる |
| サーバーのカスタム メトリック |[サーバーでの呼び出しの追跡](app/api-custom-events-metrics.md) |Business intelligence |

### <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>検索グラフとメトリック グラフで数が等しくないのはなぜですか?

[サンプリング](app/sampling.md)により、アプリからポータルに実際に送信されたテレメトリ項目 (要求、カスタム イベントなど) の数が減少します。 検索グラフには、実際に受信した項目の数が表示されます。 イベント数を表示するメトリック グラフには、発生した元のイベントの数が表示されます。 

送信される各項目には、その項目が表す元のイベントの数を示す `itemCount` プロパティが含まれています。 Analytics で次のクエリを実行すると、サンプリング操作を確認できます。

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


### <a name="automation"></a>オートメーション

#### <a name="configuring-application-insights"></a>Application Insights の構成

Azure Resource Monitor を使用して [PowerShell スクリプトを記述する](app/powershell.md)ことにより、以下の操作を実行できます。

* Application Insights リソースの作成と更新。
* 価格プランの設定。
* インストルメンテーション キーの取得。
* メトリック アラートの追加。
* 可用性テストの追加。

メトリックス エクスプローラー レポートや連続エクスポートの設定はできません。

#### <a name="querying-the-telemetry"></a>テレメトリに対するクエリの実行

[REST API](https://dev.applicationinsights.io/) を使用して [Analytics](./log-query/log-query-overview.md) クエリを実行します。

### <a name="how-can-i-set-an-alert-on-an-event"></a>イベントにアラートを設定するには、どうすればよいですか?

Azure アラートはメトリックにのみ設定できます。 イベントが発生するたびにしきい値を超える、カスタム メトリックを作成してください。 その後、メトリックにアラートを設定します。 メトリックがしきい値を超えると、超えたときの方向がどちらであっても通知が届くことになります。初期値がしきい値より高くても低くても、最初にしきい値を超えるまで通知は届きません。常に数分の待ち時間が発生します。

### <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Azure Web アプリと Application Insights の間でデータ転送料金は発生しますか?

* Application Insights の収集エンドポイントがあるデータ センターに Azure Web アプリがホストされている場合、料金はかかりません。 
* ホスト データ センターに収集エンドポイントがない場合は、アプリのテレメトリに対して [Azure の送信料金](https://azure.microsoft.com/pricing/details/bandwidth/)が発生します。

これは、Application Insights リソースがホストされている場所ではなく、 単に Microsoft のエンドポイントの割り当てによって決まります。

### <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>テレメトリを Application Insights ポータルに送信できますか?

Microsoft の SDK と [SDK API](app/api-custom-events-metrics.md) を使用することをお勧めします。 各種[プラットフォーム](app/platforms.md)向けに異なる SDK が用意されています。 これらの SDK では、バッファリング、圧縮、調整、再試行などを処理します。 ただし、[取り込みスキーマ](https://github.com/microsoft/ApplicationInsights-dotnet/tree/master/BASE/Schema/PublicSchema)と[エンドポイント プロトコル](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md)はパブリックです。

### <a name="can-i-monitor-an-intranet-web-server"></a>イントラネット Web サーバーを監視できますか?

はい。ただし、ファイアウォールの例外またはプロキシによるリダイレクトを使用して、Microsoft のサービスへのトラフィックを許可する必要があります。
- QuickPulse `https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider `https://dc.services.visualstudio.com:443` 
- TelemetryChannel `https://dc.services.visualstudio.com:443` 


[ここ](app/ip-addresses.md)でサービスと IP アドレスのすべての一覧を確認できます。

#### <a name="firewall-exception"></a>ファイアウォールの例外

ご利用の Web サーバーから Microsoft のエンドポイントへの利用統計情報の送信を許可します。 

#### <a name="gateway-redirect"></a>ゲートウェイのリダイレクト

構成内のエンドポイントを上書きすることによって、ご利用のサーバーからのトラフィックをイントラネット上のゲートウェイにルーティングします。 これらの "Endpoint" プロパティが config に存在しない場合、これらのクラスは、次の ApplicationInsights.config の例に示されているように、既定値を使用します。 

ご利用のゲートウェイは、Microsoft のエンドポイントのベース アドレスにトラフィックをルーティングする必要があります。 構成内の既定値を `http://<your.gateway.address>/<relative path>` に置き換えてください。


##### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>既定のエンドポイントを使用する ApplicationInsights.config の例:
```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://rt.services.visualstudio.com/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.services.visualstudio.com/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

> [!NOTE]
> ApplicationIdProvider は v2.6.0 以降で使用できます。



#### <a name="proxy-passthrough"></a>プロキシのパススルー

プロキシのパススルーは、マシン レベルとアプリケーション レベルのどちらかのプロキシを構成することで実現できます。
詳細については、[DefaultProxy](/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings) に関する dotnet の記事を参照してください。
 
 Web.config の例:
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

### <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>イントラネット サーバーで可用性 Web テストを実行できますか?

Microsoft の [Web テスト](app/monitor-web-app-availability.md)は、世界中に分布する Points of Presence で実行されます。 以下に示す 2 つのソリューションがあります。

* ファイアウォール ドア - [頻繁に変更される多数の Web テスト エージェント](app/ip-addresses.md)からのサーバーへの要求を許可します。
* イントラネット内部からサーバーに定期的な要求を送信する独自のコードを記述します。 Visual Studio Web テストを実行して、このコードをテストすることができます。 テストの結果は TrackAvailability() API を使用して Application Insights に送信できます。

### <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>テレメトリの収集にはどれくらいの時間がかかりますか?

Application Insights のほとんどのデータは、待ち時間が 5 分未満となっています。 一部のデータ (通常は、大きなログ ファイル) については、それよりも時間がかかることがあります。 詳細については、「[Application Insights の SLA](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/)」を参照してください。



<!--Link references-->

[data]: app/data-retention-privacy.md
[platforms]: app/platforms.md
[start]: app/app-insights-overview.md
[windows]: app/app-insights-windows-get-started.md

### <a name="http-502-and-503-responses-are-not-always-captured-by-application-insights"></a>HTTP 502 および 503 の応答は Application Insights によって常にキャプチャされるわけではありません

"502 無効なゲートウェイです" エラーと "503 サービスを利用できません" エラーは、Application Insights によって常にキャプチャされるわけではありません。 クライアント側の JavaScript が監視に使用されている場合にのみ、これは予期される動作となります。監視 JavaScript スニペットが表示されている HTML ヘッダーを含むページの前でエラー応答が返されるためです。 

サーバー側の監視が有効になっているサーバーから 502 または 503 の応答が送信された場合は、Application Insights SDK によってエラーが収集されます。 

ただし、アプリケーションの Web サーバーでサーバー側の監視が有効になっていても、Application Insights によって 502 エラーまたは 503 エラーがキャプチャされない場合もあります。 多くの最新の Web サーバーでは、クライアントが直接通信することはできませんが、代わりにリバース プロキシなどのソリューションを使用して、クライアントとフロントエンド Web サーバーの間で情報をやり取りすることはできます。 

このシナリオでは、リバース プロキシ レイヤーでの問題により、502 または 503 の応答がクライアントに返されますが、Application Insights によってすぐにキャプチャされることはありません。 このレイヤーでの問題を検出するには、リバース プロキシから Log Analytics にログを転送し、502/503 の応答を確認するためのカスタム ルールを作成する必要があります。 502 エラーと 503 エラーの一般的な原因の詳細については、Azure App Service の ["502 無効なゲートウェイです" と "503 サービスを利用できません" のトラブルシューティングに関する記事](../app-service/troubleshoot-http-502-http-503.md)を参照してください。     

## <a name="azure-monitor-for-containers"></a>コンテナーに対する Azure Monitor

この Microsoft FAQ では、コンテナーの Azure Monitor についてよく寄せられる質問を紹介します。 このソリューションについてほかに質問がある場合は、[ディスカッション フォーラム](https://feedback.azure.com/forums/34192--general-feedback)にアクセスして質問を投稿してください。 よく寄せられる質問については、すばやく簡単に見つけることができるように、この記事に追加していきます。

### <a name="health-feature-is-in-private-preview"></a>正常性機能 (プライベート プレビュー)

機能を追加するための一連の変更を行い、お客様からのフィードバックに対処する予定です。 正常性機能は、2020 年 6 月末にプライベート プレビューに移行します。詳細については、[Azure 更新プログラムに関するお知らせ](https://azure.microsoft.com/updates/ci-health-limited-preview/)を参照してください。

### <a name="what-does-other-processes-represent-under-the-node-view"></a>ノード ビューで *[その他のプロセス]* は何を表していますか?

**[その他のプロセス]** は、ノードのリソース使用率が高い根本原因を明確に理解するために役立ちます。 これによって、コンテナー化されたプロセスとコンテナー化されていないプロセスで使用率を区別できます。

これらの **[その他のプロセス]** とは何ですか? 

これらは、ノードで実行されるコンテナー化されていないプロセスです。  

これはどのようにして計算しますか?

**その他のプロセス** = *CAdvisor からの合計使用量* - *コンテナー化されたプロセスからの使用量*

**[その他のプロセス]** には、次のものが含まれます。

- 自己管理型またはマネージド Kubernetes のコンテナー化されていないプロセス 

- コンテナーの実行時プロセス  

- kubelet  

- ノードで実行されているシステム プロセス 

- ノード ハードウェアまたは VM 上で実行されている Kubernetes 以外の他のワークロード 

### <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>ContainerLog テーブルのクエリを実行したとき、Image プロパティと Name プロパティの値が出力されません。

エージェント バージョン ciprod12042019 以降では、ログ データの収集にかかるコストを最小限に抑えるため、既定では、これら 2 つのプロパティはすべてのログ行に出力されません。 これらのプロパティとその値を含めるようにテーブルをクエリする 2 つの方法があります。

#### <a name="option-1"></a>方法 1 

他のテーブルを結合して、これらのプロパティ値を結果に含めます。

ContainerID プロパティに結合することによって ```ContainerInventory``` テーブルの Image プロパティと ImageTag プロパティを含めるようにクエリを変更します。 ContainerID プロパティに結合することによって、KubepodInventory テーブルの ContaineName フィールドから (以前に ```ContainerLog``` テーブルにあったときのように) Name プロパティを含めることができます。こちらの方法をお勧めします。

次の例は、結合を使用してこれらのフィールド値を取得する方法を説明するサンプルの詳細なクエリです。

```
//lets say we are querying an hour worth of logs
let startTime = ago(1h);
let endTime = now();
//below gets the latest Image & ImageTag for every containerID, during the time window
let ContainerInv = ContainerInventory | where TimeGenerated >= startTime and TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID, Image, ImageTag | project-away TimeGenerated | project ContainerID1=ContainerID, Image1=Image ,ImageTag1=ImageTag;
//below gets the latest Name for every containerID, during the time window
let KubePodInv  = KubePodInventory | where ContainerID != "" | where TimeGenerated >= startTime | where TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID2 = ContainerID, Name1=ContainerName | project ContainerID2 , Name1;
//now join the above 2 to get a 'jointed table' that has name, image & imagetag. Outer left is safer in-case there are no kubepod records are if they are latent
let ContainerData = ContainerInv | join kind=leftouter (KubePodInv) on $left.ContainerID1 == $right.ContainerID2;
//now join ContainerLog table with the 'jointed table' above and project-away redundant fields/columns and rename columns that were re-written
//Outer left is safer so you dont lose logs even if we cannot find container metadata for loglines (due to latency, time skew between data types etc...)
ContainerLog
| where TimeGenerated >= startTime and TimeGenerated < endTime 
| join kind= leftouter (
   ContainerData
) on $left.ContainerID == $right.ContainerID2 | project-away ContainerID1, ContainerID2, Name, Image, ImageTag | project-rename Name = Name1, Image=Image1, ImageTag=ImageTag1 

```

#### <a name="option-2"></a>方法 2

すべてのコンテナー ログ行について、これらのプロパティの収集を再び有効にします。

クエリの変更を伴うため最初の方法が難しい場合、```log_collection_settings.enrich_container_logs```データ収集の構成設定[に関するページの説明に従い、エージェントの ConfigMap で ](insights/container-insights-agent-config.md)設定を有効にすることによって、これらのフィールドの収集を再び有効にすることができます。

> [!NOTE]
> 2 番目の方法は、50 を超えるノードを持つ大規模なクラスターでは推奨されません。このエンリッチメントを実行するために、クラスター内のすべてのノードから API サーバー呼び出しが生成されるからです。 この方法を使用すると、収集されるすべてのログ行のデータ サイズも増加します。

### <a name="can-i-view-metrics-collected-in-grafana"></a>Grafana で収集されたメトリックを表示できますか?

Azure Monitor for Containers では、Grafana ダッシュボードの Log Analytics ワークスペースに格納されているメトリックの表示をサポートしています。 Grafana の[ダッシュボード リポジトリ](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker)からダウンロードできるテンプレートが用意されています。これを使って作業を開始し、監視対象クラスターから追加データのクエリを実行して、カスタム Grafana ダッシュボードで視覚化する方法を学習できます。 

### <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>Azure Monitor for containers で AKS エンジンのクラスターを監視できますか?

Azure Monitor for containers は、Azure をホストとする AKS エンジン (旧称 ACS エンジン) クラスターにデプロイされたコンテナー ワークロードの監視をサポートしています。 このシナリオでの監視を有効にするために必要な手順の概要および詳細については、[AKS エンジンへの Azure Monitor for containers の使用](https://github.com/microsoft/OMS-docker/tree/aks-engine)に関するページを参照してください。

### <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Log Analytics ワークスペースにデータが表示されないのはなぜですか

Log Analytics ワークスペースで、毎日特定の時間にデータが表示されない場合は、既定の 500 MB の制限に達したか、または毎日収集するデータ量を制御するために指定された 1 日の上限に達している可能性があります。 その日の上限に達すると、データ収集が停止し、次の日にしか再開されません。 データ利用状況を確認して、予期される利用パターンに基づいて別の価格レベルに更新するには、[ログ データの使用量とコスト](platform/manage-cost-storage.md)に関する記事をご覧ください。 

### <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>ContainerInventory テーブルではどのようなコンテナーの状態が指定されますか

ContainerInventory テーブルには、停止中と実行中両方のコンテナーに関する情報が含まれています。 テーブルの値は、エージェント内のワークフローによって設定されます。このワークフローでは、Docker に対してすべてのコンテナー (実行中と停止) のクエリが実行され、そのデータが Log Analytics ワークスペースに転送されます。
 
### <a name="how-do-i-resolve-missing-subscription-registration-error"></a>"*サブスクリプション登録がない*" エラーを解決するにはどうすればよいですか

"**Microsoft.OperationsManagement へのサブスクリプション登録がない**" というエラーが表示される場合は、ワークスペースが定義されているサブスクリプションでリソース プロバイダー **Microsoft.OperationsManagement** を登録することで解決できます。 これを行う方法に関するドキュメントは、[こちら](../azure-resource-manager/templates/error-register-resource-provider.md)にあります。

### <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>RBAC 対応の AKS クラスターはサポートされていますか

コンテナー監視ソリューションでは RBAC がサポートされていませんが、Azure Monitor for Containers ではサポートされています。 これらのクラスターのデータが示されるブレードでは、ソリューションの詳細ページに正しい情報が表示されない場合があります。

### <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Helm を使用して kube システム名前空間内のコンテナーのログ収集を有効にするにはどうすればよいですか

Kube システム名前空間内のコンテナーからのログ収集は、既定では無効になっています。 omsagent で環境変数を設定することにより、ログ収集を有効にすることができます。 詳しくは、[コンテナーの Azure Monitor](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers)に関する GitHub のページをご覧ください。 

### <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>omsagent を最新リリースのバージョンに更新するにはどうすればよいですか

エージェントをアップグレードする方法については、[エージェントの管理](insights/container-insights-manage-agent.md)に関する記事をご覧ください。

### <a name="how-do-i-enable-multi-line-logging"></a>複数行のログ記録を有効にするにはどうすればよいですか

現在、コンテナーの Azure Monitor では複数行のログ記録はサポートされていませんが、利用可能な回避策があります。 JSON 形式で書き込むようにすべてのサービスを構成することができ、Docker/Moby ではそれが単一行として書き込まれます。

たとえば、サンプルの node.js アプリケーションに対する次の例で示すように、JSON オブジェクトとしてログをラップすることができます。

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

このデータは、クエリを実行すると、Azure Monitor のログに次の例のように表示されます。

```
LogEntry : ({"Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

問題の詳細については、こちらの [GitHub リンク](https://github.com/moby/moby/issues/22920)をご覧ください。

### <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>ライブ ログを有効にしたときの Azure AD のエラーを解決するにはどうすればよいですか 

次のエラーがに表示される場合があります。**要求で指定されている応答 URL が、アプリケーションに関して構成されている応答 URL と一致しません ('<application ID\>'** )。 それを解決するためのソリューションについては、[Azure Monitor for containers を使用して、コンテナー データをリアルタイムで表示する方法](insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication)に関する記事をご覧ください。 

### <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>オンボード後にクラスターをアップグレードできないのはなぜですか

AKS クラスターに対して Azure Monitor for containers を有効にした後、クラスターがデータを送信していた Log Analytics ワークスペースを削除する場合、クラスターをアップグレードしようとすると失敗します。 これを回避するには、監視を無効にしてから、サブスクリプション内の別の有効なワークスペースを参照して、監視を再度有効にする必要があります。 クラスターのアップグレードをもう一度実行しようとすると、正常に処理され、完了するはずです。  

### <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>エージェントに対して、どのポートとドメインを開くまたはホワイトリスト登録する必要がありますか

Azure、Azure US Government、および Azure China 21Vianet クラウドでコンテナー化されたエージェントに必要なプロキシとファイアウォールの構成情報については、「[ネットワーク ファイアウォールの要件](insights/container-insights-onboard.md#network-firewall-requirements)」をご覧ください。

## <a name="azure-monitor-for-vms"></a>VM に対する Azure Monitor
この Microsoft FAQ では、Azure Monitor for VMs についてよく寄せられる質問を紹介します。 このソリューションについてほかに質問がある場合は、[ディスカッション フォーラム](https://feedback.azure.com/forums/34192--general-feedback)にアクセスして質問を投稿してください。 よく寄せられる質問については、すばやく簡単に見つけることができるように、この記事に追加していきます。

### <a name="can-i-onboard-to-an-existing-workspace"></a>既存のワークスペースにオンボードすることはできますか?
仮想マシンが Log Analytics ワークスペースに既に接続されている場合、ワークスペースが[サポートされているリージョン](insights/vminsights-configure-workspace.md#supported-regions)のいずれかにあれば、Azure Monitor for VMs にオンボードするときにそのワークスペースを引き続き使用できます。


### <a name="can-i-onboard-to-a-new-workspace"></a>新しいワークスペースにオンボードすることはできますか? 
現在、VM が既存の Log Analytics ワークスペースに接続されていない場合は、データを保存するために新しいワークスペースを作成する必要があります。 Azure portal を使用して Azure Monitor for VMs で単一の Azure VM を構成すると、新しい既定のワークスペースが自動的に作成されます。

スクリプト ベースのメソッドを使用する場合、これらの手順は、[Azure PowerShell または Resource Manager テンプレートを使用した Azure Monitor for VMs の有効化](./insights/vminsights-enable-powershell.md)に関する記事で説明されています。 

### <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>VM が既に既存のワークスペースに報告している場合はどうすればよいですか?
仮想マシンから既にデータを収集している場合、既存の Log Analytics ワークスペースにデータを報告するように仮想マシンを構成済みである可能性があります。  そのワークスペースがサポートされているリージョンのいずれかにあれば、その既存のワークスペースに対して Azure Monitor for VMs を有効にすることができます。  既に使用しているワークスペースがサポートされているリージョンにない場合、現時点では Azure Monitor for VMs にオンボードすることはできません。  Microsoft では、その他のリージョンのサポートに積極的に取り組んでいます。


### <a name="why-did-my-vm-fail-to-onboard"></a>VM のオンボードに失敗したのはなぜですか?
Azure portal から Azure VM をオンボードすると、次の手順が実行されます。

* 既定の Log Analytics ワークスペースが作成されます (該当するオプションが選択されている場合)。
* Log Analytics エージェントが必要と判断されると、VM 拡張機能を使用して Azure VM にインストールされます。  
* Azure Monitor for VMs Map Dependency エージェントが必要と判断されると、拡張機能を使用して Azure VM にインストールされます。 

オンボード プロセス中、上記の各手順で状態がチェックされ、ポータルで通知の状態が返されます。 ワークスペースとエージェントのインストールの構成には、通常 5 から 10 分かかります。 監視データがポータルに表示されるまでに、さらに 5 から 10 分かかります。  

オンボードを開始したときに、VM をオンボードする必要があることを示すメッセージが表示された場合は、VM がプロセスを完了するまでに最大 30 分かかります。 


### <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>パフォーマンス グラフに VM のデータが表示されません
パフォーマンス グラフは、*InsightsMetrics* テーブルに格納されているデータを使用するように更新されました。  これらのグラフのデータを表示するには、新しい VM Insights ソリューションを使用するようにアップグレードする必要があります。  追加情報については、[一般提供についての FAQ](insights/vminsights-ga-release-faq.md) に関する記事を参照してください。

ディスク テーブルまたは一部のパフォーマンス グラフにパフォーマンス データが表示されない場合、ワークスペースでパフォーマンス カウンターが構成されていない可能性があります。 これを解決するには、こちらの [PowerShell スクリプト](./insights/vminsights-enable-powershell.md)を実行してください。


### <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Azure Monitor for VMs のマップ機能は Service Map とどのように異なるのですか?
Azure Monitor for VMs のマップ機能は Service Map に基づいていますが、次の点が異なります。

* マップ ビューには、VM のブレード、および [Azure Monitor] の [Azure Monitor for VMs]\(Azure Monitor for VMs\) からアクセスできます。
* マップ内の接続がクリック可能になっており、選択した接続のサイド パネルに接続メトリック データのビューが表示されます。
* より複雑なマップのサポートを強化するために、マップの作成に使用される新しい API が用意されています。
* 監視対象の VM がクライアント グループ ノードに含まれるようになりました。グループ内の監視対象の仮想マシンと監視対象外の仮想マシンの割合がドーナツ グラフに示されます。  また、グループを展開したときに、これを使用してマシンの一覧をフィルター処理することもできます。
* 監視対象の仮想マシンがサーバー ポート グループ ノードに含まれるようになりました。グループ内の監視対象のマシンと監視対象外のマシンの割合がドーナツ グラフに示されます。  また、グループを展開したときに、これを使用してマシンの一覧をフィルター処理することもできます。
* Application Insights のアプリ マップとの一貫性を向上させるために、マップのスタイルが更新されました。
* サイド パネルが更新されましたが、Service Map でサポートされていた Update Management、Change Tracking、Security、およびService Desk との統合は含まれていません。 
* マップするグループとマシンを選択するオプションが更新され、サブスクリプション、リソース グループ、Azure 仮想マシン スケール セット、クラウド サービスがサポートされるようになりました。
* Azure Monitor for VMs のマップ機能では、新しい Service Map コンピューター グループを作成することはできません。  

### <a name="why-do-my-performance-charts-show-dotted-lines"></a>パフォーマンス グラフに点線が表示されるのはなぜですか?
これはいくつかの理由で発生する可能性があります。  表示するデータ収集にギャップがある場合、点線が表示されます。  有効になっているパフォーマンス カウンターのデータ サンプリング頻度を変更したときに (既定の設定では、60 秒ごとにデータが収集されます)、グラフの時間範囲を狭め、サンプリング頻度がグラフで使用されるバケット サイズよりも少ない場合 (たとえば、サンプリング頻度が 10分 ごとで、グラフの各バケットが 5 分の場合)、グラフに点線が表示されます。  この場合、表示する時間範囲を広げると、グラフの線が点線ではなく実線で表示されます。

### <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Azure Monitor for VMs でグループはサポートされていますか?
はい、Dependency エージェントをインストールすると、VM から情報が収集され、サブスクリプション、リソース グループ、仮想マシン スケール セット、およびクラウド サービスに基づいてグループが表示されます。  Service Map を使用し、マシン グループを作成している場合も、これらが表示されます。  表示中のワークスペースでコンピューター グループを作成している場合は、それらもグループ フィルターに表示されます。 

### <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>集計パフォーマンス グラフで 95 百分位線を引き上げているものについて詳細を確認するにはどうすればよいですか?
既定では、選択されたメトリックで 95 パーセンタイルの最大値を持つ VM を示すためにリストが並べ替えられます。ただし、5 パーセンタイルの最小値を持つマシンが表示される、使用可能なメモリのグラフを除きます。  グラフをクリックすると、適切なメトリックが選択された **[Top N List]\(上位 N のリスト\)** ビューが開きます。

### <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>マップ機能では、VNet 間およびサブネット間で重複する IP はどのように処理されますか?
サブネット間および VNet 間で VM または Azure 仮想マシン スケール セットの IP 範囲が重複している場合、Azure Monitor for VMs に間違った情報が表示されることがあります。 これは既知の問題であり、このエクスペリエンスを改善するためのオプションを検討中です。

### <a name="does-map-feature-support-ipv6"></a>マップ機能で IPv6 はサポートされていますか?
現在、マップ機能では IPv4 のみがサポートされています。IPv6 のサポートは検討中です。 IPv6 内でトンネリングされる IPv4 もサポートされています。

### <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>リソース グループや他の大規模なグループのマップを読み込んだときに、マップを表示しづらくなります
大規模で複雑な構成に対応できるようにマップを改善しましたが、マップに多数のノード、接続、クラスターとして機能するノードが含まれている場合があることがわかりました。  Microsoft では、スケーラビリティを向上させるサポートの継続的な強化に取り組んでいます。   

### <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>[パフォーマンス] タブのネットワーク グラフと Azure VM の概要ページのネットワーク グラフが異なるのはなぜですか?

Azure VM の概要ページには、ゲスト VM でのアクティビティのホストの測定に基づいてグラフが表示されます。  Azure VM の概要のネットワーク グラフでは、課金対象となるネットワーク トラフィックのみが表示されます。  これには、仮想ネットワーク間トラフィックは含まれません。  Azure Monitor for VMs に表示されるデータとグラフは、ゲスト VM のデータに基づいており、ネットワーク グラフには、仮想ネットワーク間も含め、その VM に対する受信および送信のすべての TCP/IP トラフィックが表示されます。

### <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>VMConnection に格納されているデータの応答時間はどのように測定されて、接続パネルとブックに表示されるのですか?

応答時間は概算です。 アプリケーションのコードがインストルメント化されていないため、要求がいつ開始され、応答をいつ受信したのかは、実際にはわかりません。 その代わり、接続上で送信されるデータと、その接続で返されるデータが監視されます。 それらの送信と受信は、エージェントによって追跡され、ペアリングが試みられます。つまり、一連の送信とそれに続く一連の受信が要求/応答のペアとして解釈されます。 その操作の間隔が応答時間となります。 そこにはネットワーク待ち時間とサーバーの処理時間が含まれます。

要求/応答ベースのプロトコル、つまり接続上で単一の要求を送信して単一の応答を受信するプロトコルでは、この概算がうまく機能します。 HTTP(S) (パイプライン処理を伴わないもの) はそれに該当しますが、他のプロトコルでは十分に機能しません。

### <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Log Analytics の無料プランを利用している場合、機能の制限はありますか?
*無料*の価格レベルを使った Log Analytics ワークスペースで Azure Monitor を構成した場合、Azure Monitor for VMs Map 機能では、ワークスペースに接続できるマシンが 5 つに制限されます。 無料のワークスペースに VM が 5 つ接続されている場合、いずれかの VM を切断した後に新しい VM を接続すると、新しい VM は監視されず、マップ ページにも反映されません。  

この条件下では、VM を開いて左側のウィンドウから **[Insights]\(インサイト\)** を選択すると、機能が既に VM にインストール済みであっても、 **[今すぐ試す]** オプションが表示されます。  ただし、その VM が Azure Monitor for VMs にオンボードされていない場合には、オプションは表示されません。 


## <a name="next-steps"></a>次のステップ
質問の回答がここで見つからない場合は、次のフォーラムで他の質問と回答を参照できます。

- [Log Analytics](/answers/topics/azure-monitor.html)
- [Application Insights](/answers/topics/azure-monitor.html)

Azure Monitor に関する一般的なフィードバックについては、[フィードバック フォーラム](https://feedback.azure.com/forums/34192--general-feedback)にアクセスしてください。