---
title: Azure Monitor のドキュメントの最新情報
description: 毎月更新される Azure Monitor のドキュメントに対する重要な更新。
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 07/08/2020
ms.openlocfilehash: 10d4cb8160d0bf9111903b193574e9e820ded221
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87827743"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Azure Monitor のドキュメントの最新情報

この記事では、Azure Monitor に関して新たに執筆された記事または大幅に更新された記事を一覧で紹介しています。 毎月第 1 週目に更新され、前月から更新された記事が掲載されます。

## <a name="july-2020"></a>2020 年 7 月

### <a name="general"></a>全般
- [Azure Monitor をデプロイする](deploy-scale.md) - Azure Monitor for VMs オンボード コンテンツの再構築。
- [Azure Private Link を使用して、ネットワークを Azure Monitor に安全に接続する](platform/private-link-security.md) - 制限に関してセクションを追加しました。

### <a name="alerts"></a>警告
- [Azure Monitor アラートのアクション ルール](platform/alerts-action-rules.md) - CLI プロセスを追加しました。
- [Azure portal でのアクション グループの作成および管理](platform/action-groups.md) - UI の変更を反映して更新しました。
- [Azure Monitor Log Analytics の保存済みクエリ](log-query/saved-queries.md) - 新しい記事。
- [Azure Monitor のログ アラートのトラブルシューティング](platform/alerts-troubleshoot-log.md) - アラート ルール クォータに関するセクションを追加しました。
- [Azure メトリック警告のトラブルシューティング](platform/alerts-troubleshoot-metric.md) - まだ生成されていないカスタム メトリックに対するアラート ルールに関するセクションを追加しました。
- [Azure Monitor でのメトリック アラートのしくみを理解する](platform/alerts-metric-overview.md) - 集計の粒度を選択するための推奨事項を追加しました。

### <a name="application-insights"></a>Application Insights
- [Azure Web アプリ拡張機能のリリース ノート - Application Insights](app/web-app-extension-release-notes.md) - 新しい記事。
- [Application Insights リソース用の Resource Manager テンプレートのサンプル](samples/resource-manager-app-resource.md) - 新しい記事。
- [Azure Application Insights Profiler に関する問題のトラブルシューティング](app/profiler-troubleshooting.md) - ASP.NET Core アプリのプロファイラーを Azure App Service で実行するバグに関する注意を追加しました。 

### <a name="containers"></a>Containers
- [Azure Monitor for containers からのログ アラート](insights/container-insights-log-alerts.md) - 新しい記事。
- [Azure Monitor for containers からのメトリック アラート](insights/container-insights-metric-alerts.md) - 新しい記事。

### <a name="logs"></a>ログ
- [Azure Monitor のカスタマー マネージド キー](platform/customer-managed-keys.md) - エラー メッセージとクエリの CMK 構成のセクションを追加しました。
- [Azure Monitor HTTP データ コレクター API](platform/data-collector-api.md) - Python 3 のサンプルを追加しました。
- [Azure Monitor でログ クエリを最適化する](log-query/query-optimization.md) - サブクエリを使用するときの複数のデータ スキャンの回避に関するセクションを追加しました。
- [チュートリアル:Log Analytics クエリの使用方法](log-query/get-started-portal.md) - ビデオを追加しました。

### <a name="platform-logs"></a>プラットフォーム ログ
- [プラットフォーム ログとメトリックを異なる宛先に送信するための診断設定を作成する](platform/diagnostic-settings.md) - ビデオを追加しました。
- [Azure Monitor 用の Resource Manager テンプレートのサンプル](samples/resource-manager-samples.md) - ログの送信先の種類を使用する ARM サンプルを追加しました。 

### <a name="solutions"></a>ソリューション
- [Azure Monitor での監視ソリューション](insights/solutions.md) - CLI プロセスを追加しました。
- [Azure の Office 365 管理ソリューション](insights/solution-office-365.md) - 提供終了日を変更しました。

### <a name="virtual-machines"></a>仮想マシン

Azure Monitor for VMs コンテンツの再構築による新規および更新された記事

- [Azure Monitor for VMs とは?](insights/vminsights-overview.md)
- [Azure Monitor for VMs 用に Log Analytics ワークスペースを構成する](insights/vminsights-configure-workspace.md)
- [Linux コンピューターを Azure Monitor に接続する](platform/agent-linux.md)
- [ハイブリッド環境で Azure Monitor を有効にする](insights/vminsights-enable-hybrid.md)
- [Azure portal で 1 つの仮想マシンまたは仮想マシン スケール セットで Azure Monitor を有効にする](insights/vminsights-enable-portal.md)
- [Azure Policy を使用して Azure Monitor for VMs を有効にする](insights/vminsights-enable-at-scale-policy.md)
- [Azure Monitor for VMs の有効化の概要](insights/vminsights-enable-overview.md)
- [PowerShell を使用して Azure Monitor for VMs を有効にする](insights/vminsights-enable-powershell.md)
- [Resource Manager テンプレートを使用して Azure Monitor for VMs を有効にする](insights/vminsights-enable-resource-manager.md)
- [PowerShell またはテンプレートを使用して Azure Monitor for VMs を有効にする](insights/vminsights-enable-at-scale-powershell.md)


### <a name="visualizations"></a>視覚化
- [Log Analytics ダッシュボードの視覚化のアップグレード](log-query/dashboard-upgrade.md) - 更新間隔を更新しました。
- [Azure Monitor からのデータを視覚化する](visualizations.md) - ビデオを追加しました。


## <a name="june-2020"></a>2020 年 6 月

### <a name="general"></a>全般
- [Azure Monitor をデプロイする](deploy-scale.md) - 新しい記事。
- [Azure Monitor のカスタマー マネージド キー](platform/customer-managed-keys.md) - billingtype プロパティを更新しました。 PowerShell コマンドを追加しました。

### <a name="agents"></a>エージェント
- [Log Analytics エージェントの概要](platform/log-analytics-agent.md) - Python 2 要件を追加しました。

### <a name="alerts"></a>警告
- [ターゲット リソースが別の Azure リージョンに移動されるときにアラート ルールまたはアクション ルールを更新する方法](platform/alerts-resource-move.md) - 新しい記事。
- [Azure メトリック警告のトラブルシューティング](platform/alerts-troubleshoot-metric.md) - 新しい記事。
- [Azure Monitor におけるログ アラートのトラブルシューティング](platform/alerts-troubleshoot-metric.md) - 新しい記事。
  
### <a name="application-insights"></a>Application Insights
- [JavaScript Web アプリのための Azure Application Insights](app/javascript.md) - JavaScript SDK セクションを更新。 読み込みエラーを報告するようにスニペットを更新しました。
- [BYOS (Bring Your Own Storage) を Profiler および Visual Studio スナップショット デバッガー用に構成する](app/profiler-bring-your-own-storage.md) - 新しい記事。
- [OpenCensus Python を使用した Azure Application Insights における受信要求の追跡](app/opencensus-python-request.md) - OpenCensus のログと構成を更新しました。
- [Azure Application Insights を使用してライブ ASP.NET Web アプリを監視する](app/monitor-performance-live-website-now.md) - Status Monitor v1 が非推奨になる日付を更新しました。
- [Azure Application Insights を使用して Node.js サービスを監視する](app/nodejs.md) - 以前のバージョンからの移行と SDK 構成含む複数の更新
- [Azure Monitor を使用して Python アプリを監視する (プレビュー)](app/opencensus-python.md) - Azure Monitor エクスポーターの構成に関するセクションを追加しました。
- [コードを変更せずにアプリを監視する - Azure Monitor Application Insights の自動インストルメンテーション](app/codeless-overview.md) - 新しい記事。
- [JavaScript Web アプリの SDK 読み込みエラーのトラブルシューティング](app/javascript-sdk-load-failure.md) - 新しい記事。

### <a name="containers"></a>Containers
- [お使いのハイブリッド Kubernetes クラスターの監視を停止する方法](insights/container-insights-optout-hybrid.md) - Arc 対応 Kubernetes に関するセクションを追加しました。
- [Azure Arc 対応 Kubernetes クラスターに Azure Monitor for containers を構成する](insights/container-insights-enable-arc-enabled-clusters.md) - 新しい記事。
- [Azure Monitor for containers を使用して Azure Red Hat OpenShift v4.x を構成する](insights/container-insights-azure-redhat4-setup.md) - 前提条件を更新しました。
- [コンテナー用 Azure Monitor のライブ データ (プレビュー) の設定](insights/container-insights-livedata-setup.md) - Azure US Government で利用できない機能に関する注を削除しました。

### <a name="insights"></a>洞察
- [FAQ - Azure の Network Performance Monitor ソリューション](insights/network-performance-monitor-faq.md) - ExpressRoute モニターの FAQ を追加しました。

### <a name="logs"></a>ログ
- [Azure Log Analytics ワークスペースの削除と復旧](platform/delete-workspace.md) - PowerShell コマンドを追加しました。 トラブルシューティングを更新しました。
- [Azure Monitor で Log Analytics ワークスペースを管理する](platform/manage-access.md) - RBAC セクションに許可されないテーブルの例を追加しました。
- [Azure Monitor ログの使用量とコストを管理する](platform/manage-cost-storage.md) - データ サイズの計算に関する詳細を追加しました。 データ ボリューム アラートの構成について更新しました。 Azure Sentinel によって収集されるセキュリティ データの詳細。 データ上限の明確化。
- [Azure Logic Apps と Power Automate で Azure Monitor ログを使用する](platform/logicapp-flow-connector.md) - コネクタの制限を追加しました。

### <a name="metrics"></a>メトリック
- [Azure Monitor でサポートされているメトリック (リソースの種類別)](platform/metrics-supported.md) - SQL Server のメトリックを更新しました。


### <a name="platform-logs"></a>プラットフォーム ログ

- [診断設定用の Resource Manager テンプレートのサンプル](samples/resource-manager-diagnostic-settings.md) - アクティビティ ログの診断設定を修正します。
- [Azure portal を使用して Azure アクティビティ ログを Log Analytics ワークスペースに送信する](learn/quick-collect-activity-log-portal.md) - 新しい記事。
- [Azure Resource Manager テンプレートを使用して Azure アクティビティ ログを Log Analytics ワークスペースに送信する](learn/quick-collect-activity-log-arm.md) - 新しい記事。

プラットフォーム ログ コンテンツの再構築と統合による新規および更新された記事

- [Azure リソース ログをストレージ アカウントにアーカイブする](./platform/resource-logs.md#send-to-azure-storage)
- [Azure アクティビティ ログのイベント スキーマ](platform/activity-log-schema.md)
- [Azure アクティビティ ログ](platform/activity-log.md)
- [Azure Monitor CLI のサンプル](samples/cli-samples.md)
- [Azure Monitor PowerShell のサンプル](samples/powershell-samples.md)
- [Azure 監視 REST API のチュートリアル](platform/rest-api-walkthrough.md)
- [Azure リソース ログでサポートされているサービスとスキーマ](./platform/resource-logs-schema.md)
- [Azure リソース ログ](platform/resource-logs.md)
- [Azure Monitor での Azure アクティビティ ログの収集と分析](./platform/activity-log.md)
- [Log Analytics ワークスペースで Azure リソース ログを収集する](./platform/resource-logs.md#send-to-log-analytics-workspace)
- [プラットフォーム ログとメトリックを異なる宛先に送信するための診断設定を作成する](platform/diagnostic-settings.md)
- [Azure アクティビティ ログをエクスポートする](./platform/activity-log.md#legacy-collection-methods)
- [Azure プラットフォーム ログの概要](platform/platform-logs-overview.md)
- [Azure プラットフォーム ログをイベント ハブにストリーム配信する](./platform/resource-logs.md#send-to-azure-event-hubs)
- [Azure Monitor で Azure アクティビティ ログ イベントを表示する](./platform/activity-log.md#view-the-activity-log)

### <a name="virtual-machines"></a>仮想マシン
- [Azure portal で Azure Monitor for VMs を有効にする](./insights/vminsights-enable-portal.md) - Azure Arc を含めるように更新しました。
- [Azure Monitor for VMs の有効化の概要](insights/vminsights-enable-overview.md) - Azure Arc を含めるように更新しました。
- [Azure Monitor for VMs とは?](insights/vminsights-overview.md) - Azure Arc を含めるように更新しました。


### <a name="visualizations"></a>視覚化
- [Azure Monitor ブックのデータ ソース](platform/workbooks-data-sources.md) - アラートとカスタム エンドポイントのセクションを追加しました。
- [Azure Monitor のブックベースの分析情報のトラブルシューティング](insights/troubleshoot-workbooks.md) - 新しい記事。
- [Log Analytics ダッシュボードの視覚化のアップグレード](log-query/dashboard-upgrade.md) - 新しい記事。



## <a name="may-2020"></a>2020 年 5 月

### <a name="general"></a>全般

- [Azure Monitor についてよくあるご質問](faq.md) - メトリックのセクションを追加しました。
- [Azure Monitor のカスタマー マネージド キー](platform/customer-managed-keys.md) - 一般提供に向けたさまざまな変更。
- [Azure Monitor 用の Azure Policy 組み込みポリシー定義](samples/policy-samples.md) - 新しい記事。
- [ログ取り込み用の顧客所有のストレージ アカウント](platform/private-storage.md) - 新しい記事。
- [Azure Monitor ログで使用量とコストを管理する](platform/manage-cost-storage.md) - クラスターの比例課金を追加しました。
- [Azure Private Link を使用して、ネットワークを Azure Monitor に安全に接続する](platform/private-link-security.md) - 新しい記事。


#### <a name="new-resource-manager-template-samples"></a>新しい Resource Manager テンプレートのサンプル 
- [Azure Monitor 用の Resource Manager テンプレートのサンプル](samples/resource-manager-samples.md)
- [アクション グループ用の Resource Manager テンプレートのサンプル](samples/resource-manager-action-groups.md)
- [エージェント用の Resource Manager テンプレートのサンプル](samples/resource-manager-agent.md)
- [Azure Monitor for containers 用の Resource Manager テンプレートのサンプル](samples/resource-manager-container-insights.md)
- [Azure Monitor for VMs 用の Resource Manager テンプレートのサンプル](samples/resource-manager-vminsights.md)
- [診断設定用の Resource Manager テンプレートのサンプル](samples/resource-manager-diagnostic-settings.md)
- [Log Analytics ワークスペース用の Resource Manager テンプレートのサンプル](samples/resource-manager-workspace.md)
- [ログ クエリ用の Resource Manager テンプレートのサンプル](samples/resource-manager-log-queries.md)
- [ログ クエリ アラート ルール用の Resource Manager テンプレートのサンプル](samples/resource-manager-alerts-log.md)
- [メトリック アラート ルール用の Resource Manager テンプレートのサンプル](samples/resource-manager-alerts-metric.md)
- [ブック用の Resource Manager テンプレートのサンプル](samples/resource-manager-workbooks.md)

### <a name="agents"></a>エージェント
- [Windows Azure Diagnostics 拡張機能 (WAD) のインストールと構成](platform/diagnostics-extension-windows-install.md) - 診断構成の詳細を追加しました。
- [Log Analytics エージェントの概要](platform/log-analytics-agent.md) - サポートされている Linux バージョンを追加しました。

### <a name="application-insights"></a>Application Insights

- [Azure Monitor Application Insights で Azure Functions を監視する](app/monitor-functions.md) - 新しい記事。
- [Application Insights を使用して Node.js サービスとアプリを監視する](app/nodejs.md) - 以前のバージョンからの移行に関する新しいセクションを含む一般的な更新。
- [Application Insights および Log Analytics によって使用される IP アドレス](app/ip-addresses.md) - Webhook と米国政府用の IP アドレスを追加しました。
- [Application Insights を使用した Azure Kubernetes Service (AKS) 上のアプリケーションの監視 - Azure Monitor](app/kubernetes-codeless.md) - 新しい記事。
- [データが存在しない場合のトラブルシューティング - Application Insights for .NET](app/asp-net-troubleshoot-no-data.md) - dotnet-trace を使用したログの収集に関するセクションを追加しました。
- [Azure Monitor でアプリケーション変更分析を使用して Web アプリの問題を見つける](app/change-analysis.md) - 変更分析機能の複数の更新。

#### <a name="new-and-updated-articles-for-preview-of-workspace-based-applications"></a>ワークスペースベースのアプリケーションのプレビューに関する新規および更新された記事
- [Azure Monitor Application Insights のワークスペースベースのリソース スキーマ](app/apm-tables.md)
- [Azure Monitor Application Insights の新しいワークスペース ベースのリソースを作成する](app/create-workspace-resource.md)
- [Azure Monitor ログ クエリでの app() 式](log-query/app-expression.md)
- [Azure Monitor Log Analytics のログ クエリのスコープ](log-query/scope.md)
- [Azure Monitor でのリソース間のクエリ](log-query/cross-workspace-query.md)
- [Azure Monitor ログ レコードの標準プロパティ](platform/log-standard-properties.md)
- [Azure Monitor Logs の構造](log-query/logs-structure.md)





### <a name="containers"></a>Containers
- [Azure Monitor for containers を有効にする方法](insights/container-insights-onboard.md) - ファイアウォール構成テーブルを更新しました。
- [メトリック用に Azure Monitor for containers を更新する方法](insights/container-insights-update-metrics.md) - メトリックを収集するためにマネージド ID を使用する方法を更新しました。
- [Azure Monitor for containers の監視コストについて](insights/container-insights-cost.md) - 新しい記事。
- [Azure Monitor for containers のライブ データ (プレビュー) の設定](insights/container-insights-livedata-setup.md) - 新しいクラスターの役割のバインドのサポート。

### <a name="insights"></a>洞察
- [Azure Monitor for Azure Cache for Redis (プレビュー)](insights/redis-cache-insights-overview.md) - 新しい記事。
- [Azure Monitor for Key Vault (プレビュー) によるキー コンテナーの監視](./insights/key-vault-insights-overview.md) - 新しい記事。

### <a name="logs"></a>ログ
- [PowerShell を使用して Log Analytics を作成および構成する](platform/powershell-workspace-configuration.md) - トラブルシューティング セクションを追加しました。
- [Azure portal で Log Analytics ワークスペースを作成する](learn/quick-create-workspace.md) - トラブルシューティング セクションを追加しました。
- [Azure CLI を使用して Log Analytics ワークスペースを作成する](learn/quick-create-workspace-cli.md) - トラブルシューティング セクションを追加しました。
- [Azure Log Analytics ワークスペースの削除と復旧](platform/delete-workspace.md) - 削除されたワークスペースの復旧に関する情報を更新しました。
- [Azure Monitor ログ クエリにおける関数](log-query/functions.md) - 他の関数が含まれていない関数についての注を削除しました。
- [Azure Monitor Logs の構造](log-query/logs-structure.md) - Application Insights テーブルのプロパティの説明をわかりやすくしました。
- [Azure Logic Apps と Power Automate で Azure Monitor ログを使用する](platform/logicapp-flow-connector.md) - 制限セクションを追加しました。
- [PowerShell を使用して Log Analytics ワークスペースを作成および構成する](platform/powershell-workspace-configuration.md) - トラブルシューティング セクションを追加しました。


### <a name="metrics"></a>メトリック
- [Azure Monitor でサポートされているメトリック (リソースの種類別)](platform/metrics-supported.md) - ゲスト メトリックとメトリック ルーティングを明確にしました。 

### <a name="solutions"></a>ソリューション
- [Azure Monitor で Active Directory の環境を最適化する](insights/ad-assessment.md) - サポートされているバージョンに Windows Server 2019 を追加しました。
- [Azure Monitor で SQL Server の環境を最適化する](insights/sql-assessment.md) - サポートされている SQL Server のバージョンに追加しました。


### <a name="virtual-machines"></a>仮想マシン
- [Azure Monitor for VMs の有効化の概要](insights/vminsights-enable-overview.md) - サポートされている Ubuntu Server のバージョンに追加しました。 Log Analytics ワークスペースにサポートされるリージョンを追加しました。
- [Azure Monitor for VMs を使用してパフォーマンスをグラフ化する方法](insights/vminsights-performance.md) - 使用できないメトリックについて制限事項セクションに追加しました。

### <a name="visualizations"></a>視覚化
- [Azure Monitor ブックと Azure Resource Manager テンプレート](platform/workbooks-automate.md) - ブック テンプレートをデプロイするための Resource Manager を追加しました。
- [Azure Monitor のブック グループ](platform/workbooks-groups.md) - 新しい記事。
- [Azure Monitor ブック - JSONPath を使用して JSON データを変換する](platform/workbooks-jsonpath.md) - 新しい記事。


## <a name="april-2020"></a>2020 年 4 月

### <a name="general"></a>全般

- [Azure Monitor のカスタマー マネージド キー](platform/customer-managed-keys.md) - 非同期操作に関するセクションを追加しました
- [Azure Monitor で Log Analytics ワークスペースを管理する](platform/manage-access.md) - カスタム ログのセクションを更新しました。

### <a name="alerts"></a>警告

- [Azure Monitor アラートのアクション ルール](platform/alerts-action-rules.md) - ビデオを追加しました。
- [Azure でのアラートと通知監視の概要](platform/alerts-overview.md) - ビデオを追加しました。

### <a name="application-insights"></a>Application Insights

- [Azure Application Insights のアプリケーション マップ](app/app-map.md) - java エージェントのクラウド ロール名の構成を追加しました。
- [Azure Application Insights .NET エージェント API リファレンス](app/status-monitor-v2-api-reference.md) - API リファレンスを統合しました。
- [Application Insights および Log Analytics によって使用される IP アドレス](app/ip-addresses.md) - App Insights と Log Analytics の API、アクショングループ webhook、Azure US Government の IP アドレスを更新しました。
- [任意の場所の Java アプリケーションの監視](app/java-standalone-config.md) - 新しい記事
- [任意の場所の Java アプリケーションを監視する](app/java-in-process-agent.md) - 新しい記事。
- [任意の環境で実行されている Java アプリケーションを監視する](app/java-standalone-arguments.md) - 新しい記事。
- [オンプレミスで実行されている Java アプリケーションの監視](app/java-on-premises.md) - 新しい記事。
- [Visual Studio での Application Insights の削除](app/remove-application-insights.md) - 新しい記事。
- [Azure Application Insights でのテレメトリ サンプリング](app/sampling.md) - Python の fixed-rate サンプルを修正。

### <a name="containers"></a>Containers

- [Azure Monitor for containers を使用して Azure Red Hat OpenShift v4.x を構成する](insights/container-insights-azure-redhat4-setup.md) - 新しい記事。
- [ServiceNow の同期に関する問題を手動で修正する方法](platform/itsmc-resync-servicenow.md) - 新しい記事。
- [お使いの Azure および Red Hat OpenShift v4 クラスターの監視を停止する方法](insights/container-insights-optout-openshift-v4.md) - 新しい記事。
- [お使いの Azure Red Hat OpenShift v3 クラスターの監視を停止する方法](insights/container-insights-optout-openshift-v3.md) - 新しい記事。
- [お使いのハイブリッド Kubernetes クラスターの監視を停止する方法](insights/container-insights-optout-hybrid.md) - 新しい記事。

### <a name="insights"></a>洞察

- [Azure Monitor for Key Vault (プレビュー) による Azure キー コンテナーの監視](insights/key-vault-insights-overview.md) - 新しい記事。

### <a name="logs"></a>ログ

- [Azure Monitor サービスの制限](service-limits.md) - ユーザー クエリの調整を追加しました。
- [Azure Monitor ログの使用量とコストを管理する](platform/manage-cost-storage.md) - ログ クラスターの課金を追加しました。 Kusto クエリが追加されました。これにより、ノードごとのレガシ価格レベルを使用するお客様は、GB 単位または容量予約のレベルに移行する必要があるかどうかを判断できます。

### <a name="metrics"></a>メトリック

- [Azure メトリックス エクスプローラーの高度な機能](platform/metrics-charts.md) - 集計のセクションを追加しました。

### <a name="workbooks"></a>Workbooks

- [Azure Monitor ブックと Azure Resource Manager テンプレート](platform/workbooks-automate.md) - ブック テンプレートをデプロイするための Resource Manager テンプレートを追加しました。

## <a name="march-2020"></a>2020 年 3 月

### <a name="general"></a>全般

- [Azure Monitor の概要](overview.md) - Azure Monitor の概要ビデオを追加しました。
- [Azure Monitor のカスタマー マネージド キーの構成](platform/customer-managed-keys.md) - 全般的な更新。
- [Azure Monitor データ参照](/azure/azure-monitor/reference/) - 新しいサイト。

### <a name="alerts"></a>警告

- [Azure Monitor でアクティビティ ログ アラートを作成、表示、管理する](platform/alerts-activity-log.md) -Resource Manager テンプレートの説明を追加しました。
- [Azure Monitor でのメトリック アラートのしくみを理解する](platform/alerts-metric-overview.md) - 政府機関のサポート向けに更新されました。
- [Azure Monitor のアラートと通知のトラブルシューティング](platform/alerts-troubleshoot.md) - 新しい記事。

### <a name="application-insights"></a>Application Insights

- [PowerShell での Azure Application Insights の自動化](app/powershell.md) - ARMClient の例を追加しました。
- [Application Insights からのテレメトリの連続エクスポート](app/export-telemetry.md) - エクスポート構造の詳細を含むテーブルを追加します。
- [Azure App Service で .NET アプリでスナップショット デバッガーを有効にする](app/snapshot-debugger-appservice.md) Resource Manager テンプレートの例を追加しました。
- [Azure Application Insights の使用量とコストを管理する](app/pricing.md) - データ上限アラートに関する情報を追加しました。
- [Azure Monitor を使用して Python アプリケーションを監視する (プレビュー)](app/opencensus-python.md) - 標準メトリックを追加しました。
- [JavaScript アプリケーションのソース マップのサポート - Azure Monitor Application Insights](app/source-map-support.md) - 新しい記事。

### <a name="containers"></a>Containers

- [Azure Monitor に関する FAQ](faq.md) - コンテナーに対する Azure Monitor について更新しました。
- [Azure Monitor for containers で GPU の監視を構成する](insights/container-insights-gpu-monitoring.md) - 新しい記事。

### <a name="insights"></a>洞察

- [Azure の Office 365 管理ソリューション](insights/solution-office-365.md) - 非推奨となる日が更新されました。

### <a name="logs"></a>ログ

- [Azure Monitor でログ クエリを最適化する](log-query/query-optimization.md) - XML および JSON 解析のための CPU 状態について追加しました。
- [Azure Log Analytics ワークスペースの削除と復旧](platform/delete-workspace.md) - トラブルシューティングを追加しました。
- [Azure Logic Apps と Power Automate で Azure Monitor ログを使用する](platform/logicapp-flow-connector.md) - 新しい Azure Monitor コネクタ向けに更新しました。

### <a name="metrics"></a>メトリック

- [Azure portal でのディスク メトリックの非推奨](platform/portal-disk-metrics-deprecation.md) - 新しい記事。
- [チュートリアル - Azure Monitor でのメトリック グラフの作成](learn/tutorial-metrics-explorer.md) - ビデオを追加しました。

### <a name="platform-logs"></a>プラットフォーム ログ

- [Azure Monitor での Azure アクティビティ ログの収集と分析](./platform/activity-log.md) - 診断設定を使用したアクティビティ ログの収集をより適切に説明するために書き直しました。

### <a name="virtual-machines"></a>仮想マシン

- [Azure Monitor を使用して Azure 仮想マシンを監視する](insights/monitor-vm-azure.md) - 新しい記事。
- [クイック スタート: Azure Monitor を使用して Azure 仮想マシンを監視する](learn/quick-monitor-azure-vm.md) - Azure Monitor for VMs を追加して更新しました。
- [Azure Monitor for VMs からのアラートを作成する方法](insights/vminsights-alerts.md) - 新しい記事。
- [Azure Monitor for VMs の有効化の概要](insights/vminsights-enable-overview.md) - エージェントのダウンロード リンクを更新しました。

Azure Monitor for VMs の一般提供に関する全般的な更新

- [Azure Monitor for VMs とは?](insights/vminsights-overview.md)
- [Azure Monitor for VMs についてよく寄せられる質問](insights/vminsights-ga-release-faq.md) 
- [Azure Policy を使用して Azure Monitor for VMs を有効にする](./insights/vminsights-enable-policy.md) 
- [Azure Monitor for VMs を使用してパフォーマンスをグラフ化する方法](insights/vminsights-performance.md)
- [Azure Monitor for VMs からログを照会する方法](insights/vminsights-log-search.md)
- [Azure Monitor for VMs を使用してアプリの依存関係を表示する](insights/vminsights-maps.md) 

### <a name="visualizations"></a>視覚化

- [Azure Monitor からのデータを視覚化する](visualizations.md) - ビュー デザイナーの計画されている非推奨について記載するために更新しました。

## <a name="february-2020"></a>2020 年 2 月

### <a name="agents"></a>エージェント

診断拡張機能のコンテンツの書き直しの一環としての複数の更新。

- [Azure Monitor エージェントの概要](platform/agents-overview.md) - 各エージェントの固有の機能をより明確にするために表を再構築しました。
- [Azure Diagnostics 拡張機能の概要](platform/diagnostics-extension-overview.md) - 完全な書き直し。
- [Azure Monitor で IIS 用に Blob Storage を使用し、イベント用にテーブル ストレージを使用する](platform/diagnostics-extension-logs.md) - 更新と明確化のための全体的な書き直し。
- [Windows Azure Diagnostics 拡張機能 (WAD) のインストールと構成](platform/diagnostics-extension-windows-install.md) - 新しい記事。 
- [Windows Diagnostics 拡張機能のスキーマ](platform/diagnostics-extension-schema-windows.md) - 再構成。
- [Windows Azure Diagnostics 拡張機能から Azure Event Hubs にデータを送信する](platform/diagnostics-extension-stream-event-hubs.md) - 完全な書き直しおよび更新。
- [Azure Storage への診断データの保存と表示](../cloud-services/diagnostics-extension-to-storage.md) - 完全な書き直しおよび更新。
- [Windows 用の仮想マシン拡張機能](../virtual-machines/extensions/oms-windows.md) - Log Analytics エージェントとの関係をより明確にします。
- [Linux 用の Azure Monitor 仮想マシン拡張機能](../virtual-machines/extensions/oms-linux.md) - Log Analytics エージェントとの関係をより明確にします。

### <a name="application-insights"></a>Application Insights

- [Azure Application Insights の接続文字列](app/sdk-connection-string.md) - 新しい記事。

### <a name="insights-and-solutions"></a>分析情報とソリューション

#### <a name="azure-monitor-for-containers"></a>Azure Monitor for Containers

- [Azure Active Directory と Azure Kubernetes Service を統合する](../aks/azure-ad-integration-cli.md) - コンテナーの Azure Monitor をサポートするために RBAC 対応クラスターをサポートするクライアント アプリケーションを作成するためのメモを追加しました。

#### <a name="azure-monitor-for-vms"></a>VM に対する Azure Monitor

- [Azure Monitor for VMs (GA) についてよく寄せられる質問](insights/vminsights-ga-release-faq.md) - パフォーマンス データの保存方法の変更。

#### <a name="office-365"></a>Office 365

- [Azure の Office 365 管理ソリューション](insights/solution-office-365.md) - 非推奨となる日が更新されました。


### <a name="logs"></a>ログ

- [Azure Monitor でログ クエリを最適化する](log-query/query-optimization.md) - 新しい記事。
- [Azure Monitor ログの使用量とコストを管理する](platform/manage-cost-storage.md) - 使用量を理解するのに役立つサンプル クエリを改良しました。

### <a name="metrics"></a>メトリック

- [診断設定を使用してエクスポート可能な Azure Monitor プラットフォーム メトリック](platform/metrics-supported-export-diagnostic-settings.md) - null 値およびゼロ値に対する動作の変更に関するセクションを追加しました。

### <a name="visualizations"></a>視覚化

ビュー デザイナーからブックへの変換ガイドに関する複数の新しい記事。

- [Azure Monitor ビュー デザイナーからブックへの移行ガイド](platform/view-designer-conversion-overview.md) - 新しい記事。
- [Azure Monitor ビュー デザイナーからブックへの変換オプション](platform/view-designer-conversion-options.md) - 新しい記事。
- [Azure Monitor ビュー デザイナーからブック タイルへの変換](platform/view-designer-conversion-tiles.md) - 新しい記事。
- [Azure Monitor ビュー デザイナーからブックへの変換の概要とアクセス](platform/view-designer-conversion-access.md) - 新しい記事。
- [Azure Monitor ビュー デザイナーからブックへの変換の一般的なタスク](platform/view-designer-conversion-tasks.md) - 新しい記事。
- [Azure Monitor ビュー デザイナーからブックへの変換の例](platform/view-designer-conversion-examples.md) - 新しい記事。

## <a name="january-2020"></a>2020 年 1 月

### <a name="general"></a>全般

- [Azure Monitor によって監視される内容](monitor-reference.md) - 新しい記事。

### <a name="agents"></a>エージェント

- [Azure Log Analytics エージェントを使用してログ データを収集する](platform/log-analytics-agent.md) - ネットワーク ファイアウォールの要件に関する表が更新されています。

### <a name="alerts"></a>警告

- [Azure portal でのアクション グループの作成および管理](platform/action-groups.md) - 不要になった v2 関数用の設定を削除しました。
- [Resource Manager テンプレートでのメトリック アラートの作成](platform/alerts-metric-create-templates.md) - *ignoreDataBefore* パラメーターの例を追加しました。  複数条件のルールに関する制約を追加しました。
- [Log Analytics のアラート REST API の使用](platform/api-alerts.md) - JSON の例を修正しました。

### <a name="application-insights"></a>Application Insights

- [Application Insights および Log Analytics によって使用される IP アドレス](app/ip-addresses.md) - Azure ネットワーク セキュリティ グループを使用してトラフィックを許可するための受信ポート規則の追加方法に関して、「可用性テスト」セクションを更新しました。
- [Azure Application Insights Profiler に関する問題のトラブルシューティング](app/profiler-troubleshooting.md) - 一般的なトラブルシューティングを更新しました。
- [Azure Application Insights におけるテレメトリ サンプリング](app/sampling.md) - お客様からのフィードバックに基づいて読みやすさを改善するために更新、改編しました。

### <a name="data-security"></a>データのセキュリティ

- [Azure Monitor のカスタマー マネージド キーの構成](platform/customer-managed-keys.md) - 新しい記事。

### <a name="insights-and-solutions"></a>分析情報とソリューション

#### <a name="azure-monitor-for-containers"></a>Azure Monitor for Containers

- [Azure Monitor for containers エージェントのデータ収集を構成する](insights/container-insights-agent-config.md) - Azure Red Hat OpenShift におけるエージェントのアップグレードに関する詳しい情報を追加しました。また、エージェントのアップグレード方法を識別するための補足情報を追加しました。
- [Azure Monitor for containers におけるパフォーマンス アラートを作成する](insights/container-insights-alerts.md) - ワークスペースコンテキストのアラートを使用して、ワークスペースに格納されているパフォーマンス データに関するアラートを作成する方法についての情報を修正し、手順を更新しました。
- [Azure Monitor for containers での Kubernetes の監視](insights/container-insights-analyze.md) - Windows Kubernetes クラスターのサポートに関する概要記事と分析記事の両方を更新しました。
- [Azure Red Hat OpenShift クラスターに Azure Monitor for containers を構成する](insights/container-insights-azure-redhat-setup.md) - Azure Red Hat OpenShift におけるエージェントのアップグレードに関する詳しい情報を追加しました。また、エージェントのアップグレード方法を識別するための補足情報を追加しました。
- [ハイブリッド Kubernetes クラスターに Azure Monitor for containers を構成する](insights/container-insights-hybrid-setup.md) - Kubelet の cAdvisor を使用した secure port:10250 のサポート追加を反映するために更新しました。
- [Azure Monitor for containers エージェントを管理する方法](insights/container-insights-manage-agent.md) - Azure Red Hat OpenShift と他の種類の Kubernetes クラスターとを比較して、メトリック スクレーピングの動作と構成に関する詳細情報を更新しました。
- [Azure Monitor for containers の Prometheus 統合を構成する](insights/container-insights-prometheus-integration.md) - Azure Red Hat OpenShift と他の種類の Kubernetes クラスターとを比較して、メトリック スクレーピングの動作と構成に関する詳細情報を更新しました。
- [メトリックのための Azure Monitor for containers の更新方法](insights/container-insights-update-metrics.md) - Azure Red Hat OpenShift と他の種類の Kubernetes クラスターとを比較して、メトリック スクレーピングの動作と構成に関する詳細情報を更新しました。

#### <a name="azure-monitor-for-vms"></a>VM に対する Azure Monitor

- [Azure Monitor for VMs (GA) についてよく寄せられる質問](insights/vminsights-ga-release-faq.md) - ワークスペースとエージェントの新バージョンへのアップグレードに関する情報を追加しました。

#### <a name="office-365"></a>Office 365

- [Azure の Office 365 管理ソリューション](insights/solution-office-365.md) - Azure Sentinel における Office 365 ソリューションへの移行に関する詳細および FAQ を追加しました。 オンボーディング セクションを削除しました。

### <a name="logs"></a>ログ

- [Azure Monitor で Log Analytics ワークスペースを管理する](platform/manage-access.md) - 「非アクション」を更新しました。
- [Azure Monitor ログの使用量とコストを管理する](platform/manage-cost-storage.md) - 「価格モデル」セクションにデータ量の計算についての説明を追加しました。
- [Azure Resource Manager テンプレートを使用して Log Analytics ワークスペースの作成と構成を行う](platform/template-workspace-configuration.md) - 新しい価格レベルでテンプレートを更新しました。

### <a name="platform-logs"></a>プラットフォーム ログ

- [診断設定を使用して Azure アクティビティ ログを収集する - Azure Monitor](./platform/activity-log.md) - 変更されたプロパティについての情報を追加しました。
- [Azure アクティビティ ログをエクスポートする](./platform/activity-log.md#legacy-collection-methods) - UI の変更に合わせて更新しました。 

## <a name="december-2019"></a>2019 年 12 月

### <a name="agents"></a>エージェント

- [Linux コンピューターを Azure Monitor に接続する](platform/agent-linux.md) - 新しい記事。

### <a name="alerts"></a>警告

- [Resource Manager テンプレートでのメトリック アラートの作成](platform/alerts-metric-create-templates.md) - カスタム メトリックの例を追加しました。
- [Azure Monitor で動的しきい値を使用したアラートを作成する](platform/alerts-dynamic-thresholds.md) - 動的しきい値のグラフの解釈に関するセクションを追加しました。
- [Azure でのアラートと通知監視の概要](platform/alerts-overview.md) - Resource Graph クエリを更新しました。
- [Azure Monitor のメトリック アラートでサポートされるリソース](platform/alerts-metric-near-real-time.md) - サポートされるメトリックとディメンションについて更新しました。
- [従来の Log Analytics アラート API から新しい Azure Alerts API に切り替える](platform/alerts-log-api-switch.md) - 変更されたアラート名についての注意を追加しました。
- [Azure Monitor でのメトリック アラートのしくみを理解する](platform/alerts-metric-overview.md) - 大規模な監視でサポートされるリソースの種類を追加しました。

### <a name="application-insights"></a>Application Insights

- [ワーカー サービス アプリ (非 HTTP アプリ) 向け Application Insights](app/worker-service.md) - C# コードに既定のログ レベルを追加しました。 パッケージの参照バージョンを更新しました。
- [ApplicationInsights.config リファレンス - Azure](app/configuration-with-applicationinsights-config.md) - サンプル コードを更新しました。
- [PowerShell での Azure Application Insights の自動化](app/powershell.md) - Resource Manager テンプレートへの更新。
- [Azure Monitor Application Insights NuGet パッケージ](app/nuget.md) - パッケージのバージョンを更新しました。
- [新しい Azure Application Insights リソースの作成](app/create-new-resource.md) - グローバルに一意の名前についての注意を追加しました。
- [Live Metrics Stream による診断 - Azure Application Insights](app/live-stream.md) - ASP.NET Core SDK バージョンの要件を更新しました。
- [Application Insights のイベント カウンター](app/eventcounters.md) - customMetrics のカテゴリとテーブルを更新しました。
- [Azure Application Insights を使用した Java トレース ログの探索](app/java-trace-logs.md) - Java エージェントのログのしきい値の構成を追加しました。
- [Application Insights および Log Analytics によって使用される IP アドレス](app/ip-addresses.md) - Live Metrics Stream の IP アドレスを更新しました。
- [Azure App Services のパフォーマンスを監視する](app/azure-web-apps.md) - ASP.NET Core 3.0 のサポートを追加しました。 
- [Azure Monitor を使用して Python アプリを監視する (プレビュー)](app/opencensus-python.md) - Azure Monitor スキーマに対する OpenCensus Python のスキーマ マッピングに関する説明を追加しました。
- [Azure Application Insights のリリース ノート](app/release-notes.md) - 以前のリリースに関する注意を追加しました。
- [Azure Application Insights におけるテレメトリ チャネル](app/telemetry-channels.md) - 長時間にわたる接続喪失時に破棄されるデータの期間を更新しました。
- [Azure Application Insights におけるテレメトリ サンプリング](app/sampling.md) - カスタム TelemetryInitializer のコード スニペットを修正しました。
- [Java Web プロジェクトでの Application Insights のトラブルシューティング](app/java-troubleshoot.md) - JDK 9 で依存関係の収集が未サポートであることに関する記述を削除しました。

### <a name="insights-and-solutions"></a>分析情報とソリューション

- [Azure Monitor for containers についてよく寄せられる質問](./faq.md) - Image フィールドと Name フィールドに関する質問を追加しました。
- [Azure Monitor の Azure SQL Analytics ソリューション](insights/azure-sql.md) - 「データベース待機」の「マネージド インスタンスのサポート」を更新しました。
- [Azure Monitor for containers エージェントのデータ収集を構成する](insights/container-insights-agent-config.md) - enrich_container_logs の設定を追加しました。
- [ハイブリッド Kubernetes クラスターに Azure Monitor for containers を構成する](insights/container-insights-hybrid-setup.md) - トラブルシューティング セクションを追加しました。
- [Azure Monitor で Active Directory のレプリケーション状態を監視する](insights/ad-replication-status.md) - .NET Framework の前提条件を更新しました。
- [Azure の Network Performance Monitor ソリューション](insights/network-performance-monitor.md) - サポートされるリージョンを追加しました。
- [Azure Monitor で Active Directory 環境を最適化する](insights/ad-assessment.md) - .NET Framework の前提条件を更新しました。
- [Azure Monitor で SQL Server 環境を最適化する](insights/sql-assessment.md) - .NET Framework の前提条件を更新しました。
- [Azure Log Analytics で System Center Operations Manager 環境を最適化する](insights/scom-assessment.md) - .NET Framework の前提条件を更新しました。
- [Azure Log Analytics の IT Service Management Connector とのサポートされている接続](platform/itsmc-connections.md) - 前提条件のクライアント ID とクライアント シークレットにニューヨークを追加しました。

### <a name="logs"></a>ログ

- [Azure Log Analytics ワークスペースの削除と復旧](platform/delete-workspace.md) - PowerShell メソッドを追加しました。
- [Azure Monitor ログのデプロイの設計](platform/design-logs-deployment.md) - ワークスペースのインジェスト率を増やしました。

### <a name="metrics"></a>メトリック

- [診断設定を使用してエクスポート可能な Azure Monitor プラットフォーム メトリック](platform/metrics-supported-export-diagnostic-settings.md) - 新しい記事。

### <a name="platform-logs"></a>プラットフォーム ログ

プラットフォーム ログのコンテンツ再編成の一環として、診断設定を使用してアクティビティ ログを構成するための新機能に基づいて複数の記事を更新しました。

- [Azure リソース ログをストレージ アカウントにアーカイブする](./platform/resource-logs.md#send-to-azure-storage)
- [Azure アクティビティ ログのイベント スキーマ](platform/activity-log-schema.md)
- [Azure Monitor サービスの制限](service-limits.md)
- [Log Analytics ワークスペースで Azure アクティビティ ログを収集して分析する](./platform/activity-log.md)
- [診断設定を使用して Azure アクティビティ ログを収集する (プレビュー) - Azure Monitor](./platform/activity-log.md)
- [Azure テナントにまたがる Log Analytics ワークスペースに Azure アクティビティ ログを収集する](platform/activity-log-collect-tenants.md)
- [Log Analytics ワークスペースで Azure リソース ログを収集する](./platform/resource-logs.md#send-to-log-analytics-workspace)
- [Azure で Resource Manager テンプレートを使用して診断設定を作成する](platform/diagnostic-settings-template.md)
- [Azure でログとメトリックを収集するための診断設定を作成する](platform/diagnostic-settings.md)
- [Azure アクティビティ ログをエクスポートする](./platform/activity-log.md#legacy-collection-methods)
- [Azure プラットフォーム ログの概要](platform/platform-logs-overview.md)
- [イベント ハブへの Azure 監視データのストリーム配信](platform/stream-monitoring-data-event-hubs.md)
- [Azure プラットフォーム ログをイベント ハブにストリーム配信する](./platform/resource-logs.md#send-to-azure-event-hubs)

### <a name="quickstarts-and-tutorials"></a>クイック スタートとチュートリアル

- [Azure Monitor でのメトリック グラフの作成](learn/tutorial-metrics-explorer.md) - 新しい記事。
- [Azure リソースからリソース ログを収集し、Azure Monitor で分析する](learn/tutorial-resource-logs.md) - 新しい記事。
- [Azure Monitor を使用して Azure リソースを監視する](learn/quick-monitor-azure-resource.md) - 新しい記事。
   
## <a name="next-steps"></a>次のステップ

- Azure Monitor のドキュメント作成に協力していただける方は、[Docs 共同作成者ガイド](/contribute/)に関するページを参照してください。