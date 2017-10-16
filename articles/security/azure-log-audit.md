---
title: "Azure のログと監査 | Microsoft Docs"
description: "ログ データを使用して、アプリケーションに関する深い洞察を得る方法について説明します。"
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2017
ms.author: TomSh
ms.openlocfilehash: 9e5c929251259a86944121e504dc033bc99e3bc4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-logging-and-auditing"></a>Azure のログと監査
## <a name="introduction"></a>はじめに
### <a name="overview"></a>概要
Azure の現在の利用者と今後の利用予定者が、Azure プラットフォーム内やその周辺で提供されるさまざまなセキュリティ関連機能について理解し、使用できるように、Microsoft は一連のホワイト ペーパー、セキュリティ概要、ベスト プラクティス、およびチェックリストを作成しました。 このトピックの内容は広範囲かつ詳細であることから定期的に更新されています。 このドキュメントは、次の要約のセクションで概要を示した一連の内容の一部です。
### <a name="azure-platform"></a>Azure プラットフォーム
Azure は、オペレーティング システム、プログラミング言語、フレームワーク、ツール、データベース、デバイスにおいて最も幅広い選択肢をサポートするオープンで柔軟なクラウド サービス プラットフォームです。

たとえば、次のようなことができます。
-   Docker と統合した Linux コンテナーの実行。

-   JavaScript、Python、.NET、PHP、Java、および Node.js を使用したアプリの構築。

-   iOS、Android、および Windows デバイス用バックエンドの構築。

Azure パブリック クラウド サービスでは、何百万人もの開発者や IT プロフェッショナルから現在信頼が寄せられているのと同じテクノロジがサポートされています。

IT 資産を構築し、クラウド プロバイダーに移行したとしましょう。このとき、移行したアプリケーションやデータをどこまで保護できるかは、採用したプロバイダーがクラウド ベースの資産のセキュリティ管理のためにどのようなサービスと体制を用意しているかに応じて変わってきます。

Azure のインフラストラクチャでは、数百万の顧客を同時にホストできるように施設からアプリケーションまでが設計されており、ビジネスのセキュリティ ニーズを満たす信頼性の高い基盤となっています。 また、Azure には構成可能な幅広いセキュリティ オプションが用意されており、デプロイの独自の要件を満たすようにセキュリティをカスタマイズできます。 このドキュメントはこれらの要件を満たす上で役立ちます。

### <a name="abstract"></a>要約
セキュリティ関連のイベントの監査とログは、警告と並んで効果が高く、データ保護戦略において重要な構成要素です。 セキュリティのログやレポートは、疑わしいアクティビティの電子記録となり、外部からネットワークへの侵入または内部からの攻撃が試みられたこと、または成功したことを示すパターンを検出するために役立ちます。 監査機能を使うと、ユーザーのアクティビティの監視、規制へのコンプライアンスの文書化、フォレンジック分析などが可能になります。 警告機能によって、セキュリティ イベントが発生すると直ちに通知されます。

Microsoft Azure のサービスや製品が提供する構成可能なセキュリティ監査およびログのオプションは、セキュリティ ポリシーやセキュリティ メカニズムのギャップを識別してセキュリティ侵害を防止するための対策を講じる上で役立ちます。 Microsoft のサービスは、一元的な監視、ログ記録、継続的な可視性を提供する分析システムや、タイムリーな警告およびレポートなどのオプションをいくつか (場合によってはすべて) 提供することで、デバイスやサービスによって生成される大量の情報を管理するのに役立ちます。

Microsoft Azure のログ データは、分析やサード パーティー製の監査ソリューションとの統合を行うために、セキュリティ インシデントおよびイベント管理 (SIEM) システムにエクスポートできます。

このホワイトペーパーでは、Azure でホストされているサービスのセキュリティ ログの生成、収集、および分析の概要を示します。これは、お客様が Azure のデプロイにおけるセキュリティの洞察を得る上で役立ちます。 このホワイト ペーパーの範囲は、Azure で構築およびデプロイされたアプリケーションとサービスに限定されます。

> [!Note]
> ここに含まれる特定の推奨事項により、データ、ネットワーク、またはコンピューティング リソースの使用量が増え、ライセンスまたはサブスクリプション コストが増加する可能性があります。

## <a name="types-of-logs-in-azure"></a>Azure のログのタイプ
クラウド アプリケーションは、動的なパーツを多数使った複雑な構成になっています。 ログでは、アプリケーションを正常な状態で稼働させ続けるためのデータを取得できます。 また、潜在的な問題を防止したり、発生した問題をトラブルシューティングするのにも役立ちます。 さらに、ログ データを使用して、アプリケーションに関する深い洞察を得ることもできます。 そのような知識は、アプリケーションのパフォーマンスや保守容易性を向上させたり、手作業での介入が必要な操作を自動化したりするうえで役立ちます。

Azure サービスでは、すべてのサービスの広範なログ記録を作成します。 これらのログは、主に次のタイプに分類されます。
-   **コントロール/管理ログ**は、Azure Resource Manager の CREATE、UPDATE、および DELETE 操作の可視性を提供します。 [Azure アクティビティ ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)は、このタイプのログです。

-   **データ プレーン ログ**は、Azure リソース使用の一環として発生するイベントの可視性を提供します。 仮想マシンの Windows イベント システム ログ、セキュリティ ログ、アプリケーション ログや、Azure Monitor によって構成される[診断ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)は、このタイプのログです。


-   **処理済みイベント**は、ユーザーに代わって処理された分析済みのイベント/アラートに関する情報を提供します。 [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) がユーザーのサブスクリプションを処理して分析し、簡潔なセキュリティ アラートを提供する [Azure Security Center アラート](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)は、このタイプのログです。

次の表は、Azure で使用できる最も重要なタイプのログの一覧です。

| ログのカテゴリ | ログの種類 | 使用法 | 統合 |
| ------------ | -------- | ------ | ----------- |
|[アクティビティ ログ](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|Azure Resource Manager リソースのコントロールプレーン イベント| サブスクリプションのリソースに対して実行された操作を調査できます。| Rest API & [Azure Monitor](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|[Azure 診断ログ](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|サブスクリプションの Azure Resource Manager リソースの操作に関する豊富なデータ|   リソース自体が実行した操作を調査できます。| Azure Monitor、[Stream](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|[AAD レポート](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-azure-portal)|ログとレポート|ユーザーのサインイン アクティビティ情報と、ユーザーおよびグループ管理に関するシステム アクティビティ情報|[Graph API](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-graph-api-quickstart)|
|[Virtual Machine と Cloud Services](https://docs.microsoft.com/en-us/azure/cloud-services/cloud-services-dotnet-diagnostics-storage)|Windows のイベント ログと Linux の Syslog|  仮想マシンのシステム データとログ データを取り込み、そのデータを任意のストレージ アカウントに転送します。|   [WAD](https://docs.microsoft.com/en-us/azure/azure-diagnostics) (Windows Azure 診断ストレージ) を使用する Windows と Azure Monitor の Linux|
|[Storage Analytics](https://docs.microsoft.com/en-us/rest/api/storageservices/fileservices/storage-analytics)|ストレージ ログと、ストレージ アカウントのメトリック データの提供|要求のトレース、使用傾向の分析、ストレージ アカウントの問題の診断に対する洞察が得られます。|  REST API または[クライアント ライブラリ](https://msdn.microsoft.com/en-us/library/azure/mt347887.aspx)|
|[NSG (ネットワーク セキュリティ グループ) のフロー ログ](https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|JSON 形式、送信および受信のフローをルールごとに表示|ネットワーク セキュリティ グループを介した IP トラフィックの送信と受信に関する情報を表示します。|[Network Watcher](https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-monitoring-overview)|
|[Application Insight](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-overview)|ログ、例外、カスタム診断|  複数のプラットフォームの Web 開発者向けのアプリケーション パフォーマンス管理 (APM) サービスです。| REST API、[Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-azure-and-power-bi/)|
|データの処理/セキュリティ アラート| Azure Security Center アラート、OMS アラート| セキュリティに関する情報と警告。|   REST API、JSON|

### <a name="activity-log"></a>アクティビティ ログ
[Azure アクティビティ ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)では、サブスクリプションのリソースに対して実行された操作を調査できます。 アクティビティ ログではサブスクリプションの[コントロールプレーン イベント](https://driftboatdave.com/2016/10/13/azure-auditing-options-for-your-custom-reporting-needs/)が報告されるため、以前は "監査ログ" または "操作ログ" と呼ばれていました。 アクティビティ ログを使用すると、サブスクリプションのリソースに対して発生する書き込み操作 (PUT、POST、DELETE) すべてについて、"いつ誰が何を" 行ったのかを確認できます。 さらに、操作の状態など、重要性の大きなプロパティを確認することもできます。 アクティビティ ログには、読み取り (GET) 操作は含まれません。

ここで、PUT、POST、DELETE は、リソースのアクティビティ ログに含まれているあらゆる書き込み操作を参照します。 たとえば、アクティビティ ログを使用して、トラブルシューティングを行うときにエラーを探したり、組織のユーザーがどのようにリソースを変更したかを監視したりできます。

![アクティビティ ログ](./media/azure-log-audit/azure-log-audit-fig1.png)


Azure Portal、[CLI](https://docs.microsoft.com/azure/storage/storage-azure-cli)、PowerShell コマンドレット、[Azure Monitor REST API](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough) を使用して、アクティビティ ログからイベントを取得できます。 アクティビティ ログには、19 日間のデータ保持期間があります。

統合シナリオ
-   [アクティビティ ログ イベントをトリガーする電子メール アラートまたは Webhook アラートを作成する。](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-auditlog-to-webhook-email)

-   サード パーティーのサービスや PowerBI などのカスタム分析ソリューションで取り込むために、[アクティビティ ログを Event Hubs にストリーミングする](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs)。

-   [PowerBI コンテンツ パック](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)を使用して、アクティビティ ログを PowerBI で分析する。

-   [アーカイブや手動での検査に使用するためにアクティビティ ログをストレージ アカウントに保存する。](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-activity-log) ログ プロファイルを使用して、保持期間 (日数) を指定できます。

-   Azure Portal でアクティビティ ログを照会して表示する。

-   PowerShell コマンドレット、CLI、または REST API を使用してアクティビティ ログを照会する。

-   ログ プロファイルを使用して、アクティビティ ログを [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) にエクスポートする。

ログを出力するサブスクリプションとは別のサブスクリプションで、ストレージ アカウントまたは [Event Hub 名前空間](https://docs.microsoft.com/azure/event-hubs/event-hubs-resource-manager-namespace-event-hub-enable-archive)を使用できます。 設定を構成するユーザーは、両方のサブスクリプションに対して適切な [RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) へのアクセス権を持っている必要があります。
### <a name="azure-diagnostic-logs"></a>Azure 診断ログ
Azure 診断ログはリソースによって出力され、そのリソースの操作に関する豊富なデータを提供します。 これらのログの内容は、リソースのタイプによって異なります (たとえば、[Windows イベント システム ログ](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events)は、VM の診断ログのカテゴリの 1 つであり、[BLOB ログ、Table ログ、Queue ログ](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account)は、ストレージ アカウントの診断ログのカテゴリです)。また、診断ログは、サブスクリプションのリソースに対して実行された操作に関する洞察が得られるアクティビティ ログとは異なります。

![Azure 診断ログ](./media/azure-log-audit/azure-log-audit-fig2.png)

Azure 診断ログは、PowerShell、コマンド ライン インターフェイス (CLI)、および REST API を使用して、複数の構成オプション (Azure Portal) を提供します。

**統合シナリオ**
-   監査や手動での検査に使用するために診断ログを [ストレージ アカウント](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) に保存する。 診断設定を使用して、保持期間 (日数) を指定できます。

-   サード パーティーのサービスや [PowerBI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/) などのカスタム分析ソリューションで取り込むために、[診断ログを Event Hubs にストリーミングする](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs)。

-   診断ログを [OMS Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) で分析する。

**サポートされるサービス、診断ログ用スキーマ、リソース タイプごとのサポートされるログ カテゴリ**


| サービス | スキーマとドキュメント | リソースの種類 | カテゴリ |
| ------- | ------------- | ------------- | -------- |
|Load Balancer| [Azure Load Balancer のログ分析 (プレビュー)](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-monitor-log)|Microsoft.Network/loadBalancers|  LoadBalancerAlertEvent|
|||Microsoft.Network/loadBalancers| LoadBalancerProbeHealthStatus
|ネットワーク セキュリティ グループ|[ネットワーク セキュリティ グループ (NSG) のためのログ分析](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-nsg-manage-log)|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|
|||Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|
|Application Gateway|[Application Gateway の診断ログ](https://docs.microsoft.com/en-us/azure/application-gateway/application-gateway-diagnostics)|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|
|||Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|
|||Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|
|Key Vault|[Azure Key Vault のログ記録](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-logging)|Microsoft.KeyVault/vaults|AuditEvent|
|Azure Search|[検索トラフィックの分析の有効化と使用](https://docs.microsoft.com/en-us/azure/search/search-traffic-analytics)|Microsoft.Search/searchServices|OperationLogs|
|Data Lake Store|[Azure Data Lake Store の診断ログへのアクセス](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-diagnostic-logs)|Microsoft.DataLakeStore/accounts|Audit|
|Data Lake Analytics|[Azure Data Lake Analytics の診断ログへのアクセス](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)|Microsoft.DataLakeAnalytics/accounts|Audit|
|||Microsoft.DataLakeAnalytics/accounts|要求|
|||Microsoft.DataLakeStore/accounts|要求|
|Logic Apps|[Logic Apps B2B カスタム追跡スキーマ](https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-track-integration-account-custom-tracking-schema)|Microsoft.Logic/workflows|WorkflowRuntime|
|||Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|
|Azure Batch|[Azure Batch 診断ログ](https://docs.microsoft.com/en-us/azure/batch/batch-diagnostics)|Microsoft.Batch/batchAccounts|ServiceLog|
|Azure Automation|[Azure Automation のログ分析](https://docs.microsoft.com/en-us/azure/automation/automation-manage-send-joblogs-log-analytics)|Microsoft.Automation/automationAccounts|JobLogs|
|||Microsoft.Automation/automationAccounts|JobStreams|
|Event Hubs|[Azure Event Hubs の診断ログ](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-diagnostic-logs)|Microsoft.EventHub/namespaces|ArchiveLogs|
|||Microsoft.EventHub/namespaces|OperationalLogs|
|Stream Analytics|[ジョブの診断ログ](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-job-diagnostic-logs)|Microsoft.StreamAnalytics/streamingjobs|実行|
|||Microsoft.StreamAnalytics/streamingjobs|作成|
|Service Bus|[Azure Service Bus の診断ログ](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-diagnostic-logs)|Microsoft.ServiceBus/namespaces|OperationalLogs|

### <a name="azure-active-directory-reporting"></a>Azure Active Directory レポート
Azure Active Directory (Azure AD) には、ディレクトリに関するセキュリティ レポート、アクティビティ レポート、および監査レポートが含まれています。 [Azure Active Directory 監査レポート](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide)を利用すると、Azure Active Directory で発生した特権アクションを識別できます。 特権アクションとしては、昇格の変更 (例: ロールの作成、パスワードのリセット)、ポリシー構成の変更 (例: パスワード ポリシー)、ディレクトリ構成の変更 (例: ドメインのフェデレーション設定の変更) などがあります。

レポートでは、イベント名、アクションを実行したアクター、変更によって影響を受けた対象リソース、日時 (UTC) に関する監査レコードが提供されます。 [監査ログの表示](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)に関するページに説明されているように、[Azure Portal](https://portal.azure.com/) を使用して Azure Active Directory に対する監査イベントのリストを取得できます。 含まれているレポートの一覧を次に示します。

| セキュリティ レポート | アクティビティ レポート | 監査レポート |
| :--------------- | :--------------- | :------------ |
|不明なソースからのサインイン| アプリケーションの使用状況: 概要| ディレクトリ監査レポート|
|複数のエラー後のサインイン|  アプリケーションの使用状況: 詳細||
|複数の地域からのサインイン|    アプリケーション ダッシュボード||
|不審なアクティビティのある IP アドレスからのサインイン|   アカウント プロビジョニング エラー||
|不規則なサインイン アクティビティ|    個々のユーザー デバイス||
|感染している可能性があるデバイスからのサインイン|   個々のユーザー アクティビティ||
|異常なサインイン アクティビティがあるユーザー| グループのアクティビティ レポート||
||パスワード リセット登録アクティビティ レポート||
||パスワード リセット アクティビティ|||

これらのレポートのデータは、SIEM システム、監査、ビジネス インテリジェンス ツールなどのアプリケーションに役立ちます。 Azure AD レポート API は、一連の REST ベースの API を使用してプログラムによってデータにアクセスできるようにします。 これらの [API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) は、さまざまなプログラミング言語とツールから呼び出すことができます。

Azure AD の監査レポートのイベントは、180 日間保持されます。

> [!Note]
> レポートの保持期間の詳細については、「[Azure Active Directory レポートの保持ポリシー](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention)」を参照してください。

監査イベントを長期間保存する場合は、Reporting API を使用して[監査イベント](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events)を定期的に別のデータ ストアにプルできます。

### <a name="virtual-machine-logs-using-azure-diagnostics"></a>Azure 診断を使用した仮想マシン ログ
[Azure 診断](https://docs.microsoft.com/azure/azure-diagnostics)は、デプロイされたアプリケーションで診断データを収集できるようにする Azure 内の機能です。 さまざまなソースで診断拡張機能を使用することができます。 現在サポートされているのは、[Azure Cloud Service の Web ロールおよび Worker ロール](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me)、

![Azure 診断を使用した仮想マシン ログ](./media/azure-log-audit/azure-log-audit-fig3.png)

Microsoft Windows および [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) が稼動している [Azure Virtual Machines](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/) です。

仮想マシンで Azure 診断を有効にするには、以下の方法があります。

-   Visual Studio の使用 ([Visual Studio を使用した Azure Virtual Machines のトレース](https://docs.microsoft.com/azure/vs-azure-tools-debug-cloud-services-virtual-machines)に関するページを参照)

-   [Azure Virtual Machine での Azure 診断のリモート設定](https://docs.microsoft.com/azure/virtual-machines-dotnet-diagnostics)

-   [PowerShell を使用した Azure Virtual Machines での診断の設定](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

-   [Azure Resource Manager テンプレートを使用して監視および診断を含む Windows 仮想マシンを登録する](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="storage-analytics"></a>Storage Analytics
[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) では、ログが記録され、ストレージ アカウントのメトリック データを得ることができます。 このデータを使用して、要求のトレース、使用傾向の分析、ストレージ アカウントの問題の診断を行うことができます。 Storage Analytics Logging は、[BLOB、Queue、Table の各サービス](https://docs.microsoft.com/azure/storage/storage-introduction)で使用できます。 Storage Analytics は、ストレージ サービスに対する要求の成功と失敗についての詳細な情報をログに記録します。

この情報を使って個々の要求を監視したり、ストレージ サービスに関する問題を診断したりできます。 要求は、ベスト エフォートでログに記録されます。 ログ エントリが作成されるのは、サービス エンドポイントに対して行われた要求がある場合に限られます。 たとえば、ストレージ アカウントの BLOB エンドポイントにはアクティビティが存在するが、Table エンドポイントや Queue エンドポイントにはアクティビティが存在しない場合、Blob service に関連したログだけが作成されます。

Storage Analytics を使用するには、監視するサービスごとに Storage Analytics を個別に有効にする必要があります。 Storage Analytics は [Azure Portal](https://portal.azure.com/) で有効にできます。詳細については、「[Azure Portal でのストレージ アカウントの監視](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account)」を参照してください。 また、プログラムから REST API またはクライアント ライブラリを使用して有効にすることもできます。 サービスごとに Storage Analytics を個別に有効にするには、Set Service Properties 操作を使用します。

集計データは、既知の BLOB (ログの場合) と既知のテーブル (メトリックの場合) に格納されます。集計データには、Blob service と Table service の API を使用してアクセスできます。

ストレージ アカウントの合計の制限とは別に、Storage Analytics には、格納されたデータの量に関して 20 TB (テラバイト) の制限があります。 すべてのログは、Storage Analytics をストレージ アカウントに対して有効にしたときに自動的に作成される $logs という名前のコンテナー内の[ブロック BLOB](https://docs.microsoft.com/azure/storage/storage-analytics) に格納されます。

> [!Note]
> 課金ポリシーとデータ保持ポリシーの詳細については、「[Storage Analytics and Billing (Storage Analytics と課金)](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing)」を参照してください。
>
> [!Note]
> ストレージ アカウントの制限の詳細については、「[Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](https://docs.microsoft.com/azure/storage/storage-scalability-targets)」を参照してください。

次のタイプの認証済み要求と匿名要求が記録されます。



| 認証済み  | 匿名|
| :------------- | :-------------|
| 成功した要求 | 成功した要求 |
|失敗した要求 (タイムアウト、スロットル、ネットワーク、承認などに関する各種エラー) | Shared Access Signature (SAS) を使用した要求 (失敗した要求と成功した要求を含む) |
| Shared Access Signature (SAS) を使用した要求 (失敗した要求と成功した要求を含む) |クライアントとサーバーの両方のタイムアウト エラー |
|   データの分析要求 |    エラー コード 304 (変更されていません) で失敗した GET 要求 |
| Storage Analytics そのものによる要求 (ログの作成/削除など) は記録されません。 ログに記録されるデータの一覧については、「[Storage Analytics によって記録される操作およびステータス メッセージ](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages)」および「[Storage Analytics のログの形式](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format)」をご覧ください。 | その他の失敗した匿名要求は一切記録されません。 ログに記録されるデータの全一覧については、「[Storage Analytics によって記録される操作やステータス メッセージ](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages)」および「[Storage Analytics のログの形式](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format)」を参照してください。 |

### <a name="azure-networking-logs"></a>Azure のネットワーク ログ
Azure でのネットワーク ログおよび監視が指す意味は広く、大まかに次の 2 つのカテゴリを対象としています。

-   [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher) - シナリオベースのネットワーク監視は、Network Watcher の機能を使用して実現できます。 このサービスには、パケット キャプチャ、次のホップ、IP フロー検証、セキュリティ グループ ビュー、NSG フロー ログなどが搭載されています。 シナリオ レベルの監視では、個別のネットワーク リソースの監視とは対照的に、ネットワーク リソースを隅から隅まで確認できます。

-   [リソース監視](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-resource-level-monitoring) - リソース レベルの監視は、診断ログ、メトリック、トラブルシューティング、リソース正常性の 4 つの機能で構成されています。 これらの機能はすべて、ネットワーク リソース レベルで構築されています。

![Azure のネットワーク ログ](./media/azure-log-audit/azure-log-audit-fig4.png)

Network Watcher は地域サービスであり、ネットワーク シナリオ レベルで Azure 内と Azure 間の状態を監視して診断できます。 Network Watcher に搭載されているネットワークの診断および監視ツールを使用して、Azure 内のネットワークを把握および診断し、洞察を得ることができます。

**NSG フロー ログ** - ネットワーク セキュリティ グループのフロー ログにより、そのグループのセキュリティ規則で許可または拒否されるトラフィックに関係するログを記録できます。 これらのフローのログは JSON 形式で記述され、ルールごとの送信フローと受信フロー、フローが適用される NIC、フローに関する 5 タプル情報 (送信元/宛先 IP、送信元/宛先ポート、プロトコル)、トラフィックが許可されているか拒否されているかが示されます。

### <a name="network-security-group-flow-logging"></a>ネットワーク セキュリティ グループのフロー ログ

[ネットワーク セキュリティ グループのフローのログ](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)は、ネットワーク セキュリティ グループを使用した受信および送信 IP トラフィックに関する情報を表示できる Network Watcher の機能です。 これらのフローのログは JSON 形式で記述され、ルールごとの送信フローと受信フロー、フローが適用される NIC、フローに関する 5 タプル情報 (送信元/宛先 IP、送信元/宛先ポート、プロトコル)、トラフィックが許可されているか拒否されているかが示されます。

フロー ログはネットワーク セキュリティ グループを対象としていますが、その他のログと同じようには表示されません。 フロー ログは、ストレージ アカウント内でのみ保存されます。

その他のログで見られる同様のリテンション期間ポリシーが、フロー ログに適用されます。 ログにはリテンション期間ポリシーがあり、1 日 から 365 日まで設定できます。 リテンション期間ポリシーが設定されていない場合、ログは無期限に保持されます。

**診断ログ**

定期イベントおよび自然発生イベントがネットワーク リソースにより作成され、ストレージ アカウント内に記録されるか、イベント ハブまたは Log Analytics へ送信されます。 こうしたログから、リソースの正常性に関する詳細が得られます。 これらのログは、Power BI および Log Analytics などのツールで確認できます。 診断ログを確認する方法については、[Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics) に関するページを参照してください。

![診断ログ](./media/azure-log-audit/azure-log-audit-fig5.png)

診断ログは、[ロード バランサー](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)、[ネットワーク セキュリティ グループ](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)、ルーティング、および [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics) で利用できます。

Network Watcher には診断ログ ビューが用意されています。 このビューには、診断ログをサポートするすべてのネットワーク リソースが表示されます。 このビューから、ネットワーク リソースの有効化および無効化を手軽に素早く行うことができます。


これらのログ機能に加えて、Network Watcher には現在、次のような機能があります。
- [トポロジ](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) - リソース グループ内のネットワーク リソース間のさまざまな相互接続および関係をネットワーク レベルで確認できます。

- [可変パケット キャプチャ](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) - 仮想マシンで送受信されるパケット データをキャプチャします。 時間やサイズの制限などを設定できる詳細なフィルター オプションときめ細やかなコントロールにより、多様なキャプチャを行うことができます。パケット データは、.cap 形式で BLOB ストアまたはローカル ディスクに保管できます。

-   [IP フロー検証](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) - フロー情報の 5 タプル パケット パラメーター (宛先 IP、送信元 IP、宛先ポート、送信元ポート、プロトコル) に基づいてパケットが許可されたか拒否されたかを確認します。 パケットがセキュリティ グループにより拒否された場合、そのパケットを拒否した規則とグループが返されます。

-   [次のホップ](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) - Azure Network Fabric におけるルーティング対象パケットの次のホップを特定します。これにより、誤って構成されたユーザー定義のルーティングがあるかどうかを診断できます。

-   [セキュリティ グループ ビュー](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) - VM に適用された有効な適用セキュリティ規則を確認できます。

-   [仮想ネットワーク ゲートウェイと接続のトラブルシューティング](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) - 仮想ネットワーク ゲートウェイと接続に関する問題をトラブルシューティングできます。

-   [ネットワーク サブスクリプションの制限](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-subscription-limits) - ネットワーク リソースの使用状況を制限と照らし合わせて確認できます。

### <a name="application-insight"></a>Application Insight

[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) は、複数のプラットフォームで使用できる Web 開発者向けの拡張可能なアプリケーション パフォーマンス管理 (APM) サービスです。 このサービスを使用して、実行中の Web アプリケーションを監視することができます。 パフォーマンスに異常があると、自動的に検出されます。 組み込まれている強力な分析ツールを使えば、問題を診断し、ユーザーがアプリを使用して実行している操作を把握できます。

 Application Insights は、パフォーマンスやユーザビリティを継続的に向上させるうえで役立つように設計されています。

 オンプレミスまたはクラウドでホストされている .NET、Node.js、J2EE などのさまざまなプラットフォーム上のアプリで機能します。 devOps プロセスと統合され、さまざまな開発ツールへの接続ポイントを備えています。

![Application Insight](./media/azure-log-audit/azure-log-audit-fig6.png)

Application Insights は、開発チーム用のツールであり、アプリのパフォーマンスや使用状況を把握できるように支援します。 以下を監視します。

-   **要求レート、応答時間、およびエラー率**: 最も人気のあるページがどの時間帯にどの場所のユーザーからアクセスされているかを調べます。 最もパフォーマンスの高いページを確認します。 要求が多いときに、応答時間と失敗率が高くなる場合は、おそらくリソースに問題があります。

-   **依存率、応答時間、およびエラー率**: 外部サービスによって応答が遅くなっているかどうかを調べます。

-   **例外**: 集計された統計を分析します。または特定のインスタンスを選択し、スタック トレースと関連する要求を調べます。 サーバーとブラウザーの両方の例外が報告されます。

-   **ページ ビューと読み込みのパフォーマンス**: ユーザーのブラウザーから報告されます。

-   Web ページからの **AJAX 呼び出し**: レート、応答時間、およびエラー率。

-   **ユーザー数とセッション数**。

-   Windows または Linux サーバー コンピューターの CPU、メモリ、ネットワーク使用率などの**パフォーマンス カウンター**。

-   Docker または Azure の**ホスト診断**。

-   アプリの**診断トレース ログ**: これにより、トレース イベントを要求に関連付けることができます。

-   販売された品目や勝利したゲームなどのビジネス イベントを追跡するためにクライアントまたはサーバーのコード内に書き込んだ**カスタム イベントとメトリック**。

**統合シナリオと説明:**

| 統合シナリオ | Description |
| --------------------- | :---------- |
|[アプリケーション マップ](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-app-map)|アプリのコンポーネントを、主要なメトリックとアラートと共に表示します。||
|[インスタンス データの診断検索](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-diagnostic-search)| 要求、例外、依存関係の呼び出し、ログ トレースおよびページ ビューなどのイベントを検索およびフィルター処理します。||
|[集計データのメトリックス エクスプローラー](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-metrics-explorer)|要求、失敗、および例外の比率、応答時間、ページの読み込み時間などの集計データを調査、フィルター処理、およびセグメント分割します。||
|[ダッシュボード](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-dashboards#dashboards)|複数のリソースからのデータをマッシュアップし、他のユーザーと共有します。 複数コンポーネントのアプリケーションと、チーム ルームでの継続的な表示に最適です。||
|[Live Metrics Stream](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-live-stream)|新しいビルドをデプロイする場合、このほぼリアルタイムのパフォーマンス インジケーターを監視し、すべてが期待どおりに動作することを確認します。||
|[Analytics](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-analytics)|この強力なクエリ言語を使用して、アプリのパフォーマンスと使用状況に関する難しい質問に回答します。||
|[自動および手動のアラート](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-alerts)|アプリのテレメトリの通常パターンに対して自動アラートを適応し、通常とは異なるパターンがある場合にアラートをトリガーします。 カスタムまたは標準のメトリックスの特定レベルでアラートを設定することもできます。||
|[Visual Studio](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-visual-studio)|パフォーマンス データをコードで確認します。 スタック トレースからコードに移動します。||
|[Power BI](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-export-power-bi)|使用状況のメトリックをその他のビジネス インテリジェンスと統合します。||
|[REST API](https://dev.applicationinsights.io/)|メトリックと生データのクエリを実行するコードを記述します。||
|[連続エクスポート](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-export-telemetry)|生データが届いたらストレージに一括エクスポートします。||

### <a name="azure-security-center-alerts"></a>Azure Security Center のアラート
[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) は、真の脅威を検出し、誤検知を減らすために、Azure のリソースやネットワークのほか、接続されているパートナー ソリューション (ファイアウォールやエンドポイント保護ソリューションなど) から、自動的にログ データを収集、分析、統合します。 Security Center には、優先順位の付いたセキュリティの警告の一覧が表示されます。また、すぐに問題を調査する必要がある情報や、攻撃を受けたものを修復する方法についての推奨事項も表示されます。

Security Center の脅威検出は、Azure のリソースやネットワーク、接続されているパートナー ソリューションからセキュリティ情報を自動的に収集することによって機能します。 この情報を分析し、ときには複数の情報源から得た情報との関連性を探りながら、脅威を特定します。 Security Center では、セキュリティの警告に優先順位が、脅威に対処するための推奨事項と共に割り当てられます。

![[Azure Security Center] \(Azure Security Center)](./media/azure-log-audit/azure-log-audit-fig7.png)

Security Center には、シグネチャ ベースの手法とは比較にならない高度なセキュリティ分析が採用されています。 大量のデータや[機械学習](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/)における革新的テクノロジを適用し、クラウド ファブリック全体にわたってイベントが評価されるので、手作業に頼った手法や攻撃の進化を予測する手法では特定できない脅威でも検出することができます。 こうしたセキュリティ分析の例を次に示します。

-   **統合された脅威インテリジェンス**: グローバルな脅威インテリジェンスを適用して、既知の有害因子を探します。情報源としては、Microsoft 製品とサービス、Microsoft Digital Crimes Unit (DCU)、Microsoft Security Response Center (MSRC)、外部フィードがあります。

-   **行動分析**: 既知のパターンを適用することによって悪質な行動を検出します。

-   **異常検出**: 統計プロファイルを使用して、過去の基準を構築します。 確立された基準からの逸脱に、攻撃ベクトルとの一致が疑われる場合、警告が生成されます。


セキュリティ操作およびインシデント対応チームの多くが、セキュリティ情報イベント管理 (SIEM) ソリューションを、セキュリティ警告のトリアージと調査の開始点として使用します。 Azure ログ統合により、お客様は、Security Center の警告を、Azure 診断および Azure 監査ログによって収集された仮想マシンのセキュリティ イベントと共に、ログ分析または SIEM ソリューションとほぼリアルタイムで同期させることができます。


## <a name="log-analytics"></a>Log Analytics

Log Analytics は、[Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) のサービスであり、クラウドおよびオンプレミスの環境内にあるリソースで生成されたデータを収集して分析するのに役立ちます。 統合検索とカスタム ダッシュボードによるリアルタイムの洞察が得られるため、物理的な場所に関係なくワークロードおよびサーバー全体の無数のレコードをただちに分析できます。

![Log Analytics](./media/azure-log-audit/azure-log-audit-fig8.png)

Log Analytics の核となる機能は、Azure クラウドでホストされている OMS リポジトリです。 データは、データ ソースを構成してソリューションをサブスクリプションに追加することによって接続されているソースからリポジトリに収集されます。 データ ソースとソリューションは、独自のプロパティのセットを持つさまざまなレコードの種類をそれぞれ作成しますが、リポジトリへのクエリでまとめて分析することもできます。 これにより、同じツールと方法を使用して、異なるソースによって収集されたさまざまな種類のデータを操作できます。

接続先のソースは、Log Analytics によって収集されたデータを生成するコンピューターとその他のリソースです。 これには、直接接続している [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) および [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) のコンピューターにインストールされているエージェント、または[接続されている System Center Operations Manager 管理グループ](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents)のエージェントを含めることができます。 Log Analytics は [Azure Storage](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) からのデータも収集できます。

[データソース](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources) は接続されている各ソースから収集されたさまざまな種類のデータです。 これには、[IIS ログ](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs)や[カスタム テキスト ログ](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-custom-logs)などのソースに加えて、[Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) および Linux エージェントからのイベントと[パフォーマンス データ](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters)が含まれます。 収集するデータ ソースをそれぞれ構成すると、構成は接続されているソースごとに自動的に提供されます。

[Azure サービスのログとメトリックを収集する](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage)方法は 4 種類あります。
1.  Azure 診断から Log Analytics に直接 (次の表の "診断")

2.  Azure 診断から Azure storage 経由で Log Analytics に (次の表の "ストレージ")

3.  Azure サービスのコネクタ (次の表の "コネクタ")

4.  スクリプトでデータを収集して Log Analytics に投稿 (次の表の空白セルと、表に記載されていないサービス用)

| サービス | リソースの種類 | ログ | メトリック | 解決策 |
| :------ | :------------ | :--- | :------ | :------- |
|アプリケーション ゲートウェイ|  Microsoft.Network/<br>applicationGateways|  診断|診断|    [Azure Application](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics) [Gateway 分析](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics)|
|Application Insights||     コネクタ|  コネクタ|  [Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) [コネクタ (プレビュー)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)|
|Automation アカウント|   Microsoft.Automation/<br>AutomationAccounts|    診断||       [詳細情報](https://docs.microsoft.com/en-us/azure/automation/automation-manage-send-joblogs-log-analytics)|
|Batch アカウント|    Microsoft.Batch/<br>batchAccounts|  診断|    診断||
|従来のクラウド サービス||       Storage||       [詳細情報](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-storage-iis-table)|
|Cognitive Services|    Microsoft.CognitiveServices/<br>accounts|       診断|||
|Data Lake Analytics|   Microsoft.DataLakeAnalytics/<br>accounts|   診断|||
|Data Lake Store|   Microsoft.DataLakeStore/<br>accounts|   診断|||
|Event Hub 名前空間|   Microsoft.EventHub/<br>namespaces|  診断|    診断||
|IoT Hub|  Microsoft.Devices/<br>IotHubs||     診断||
|Key Vault| Microsoft.KeyVault/<br>vaults|  診断  || [KeyVault 分析](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-key-vault)|
|ロード バランサー|    Microsoft.Network/<br>loadBalancers|    診断|||
|Logic Apps|    Microsoft.Logic/<br>workflows|  診断|    診断||
||Microsoft.Logic/<br>integrationAccounts||||
|ネットワーク セキュリティ グループ|   Microsoft.Network/<br>networksecuritygroups|診断||   [Azure ネットワーク セキュリティ グループ分析](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-networking-analytics#azure-network-security-group-analytics-solution-in-log-analytics)|
|Recovery コンテナー|   Microsoft.RecoveryServices/<br>vaults|||[Azure Recovery Services Analytics (プレビュー)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
|Search サービス|   Microsoft.Search/<br>searchServices|    診断|    診断||
|Service Bus 名前空間| Microsoft.ServiceBus/<br>namespaces|    診断|診断|    [Service Bus Analytics (プレビュー)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
|Service Fabric||       Storage||    [Service Fabric Analytics (プレビュー)](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-service-fabric)|
|SQL (v12)| Microsoft.Sql/<br>servers/<br>データベース||       診断||
||Microsoft.Sql/<br>servers/<br>elasticPools||||
|Storage|||         スクリプト| [Azure Storage Analytics (プレビュー)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution)|
|Virtual Machines|  Microsoft.Compute/<br>virtualMachines|  内線番号|  内線番号||
||||診断||
|仮想マシン スケール セット|   Microsoft.Compute/<br>virtualMachines    ||診断||
||Microsoft.Compute/<br>virtualMachineScaleSets/<br>virtualMachines||||
|Web サーバー ファーム|Microsoft.Web/<br>serverfarms||   診断
|Web サイト| Microsoft.Web/<br>sites ||      診断|    [詳細情報](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webappazure-oms-monitoring)|
||Microsoft.Web/<br>sites/<br>slots|||||


## <a name="log-integration-with-on-premises-siem-systems"></a>オンプレミスの SIEM システムとのログ統合
[Azure ログ統合](https://www.microsoft.com/download/details.aspx?id=53324)を使用すると、未加工のログを、Azure リソースからオンプレミスの**セキュリティ情報イベント管理 (SIEM) システム**に統合できます。

![ログ統合](./media/azure-log-audit/azure-log-audit-fig9.png)

Azure ログ統合では、Windows (WAD) 仮想マシン、Azure アクティビティ ログ、Azure Security Center アラート、および Azure リソース プロバイダーのログから Azure 診断を収集します。 この統合は、すべての資産に対してオンプレミスまたはクラウドの統合ダッシュボードを提供します。これによりセキュリティ イベントの集計、関連付け、分析を実行し、警告を生成できます。



現在のところ Azure ログ統合は、Azure アクティビティ ログ、Azure サブスクリプション内の Windows 仮想マシンからの Windows イベント ログ、Azure Security Center アラート、Azure 診断ログ、および Azure Active Directory 監査ログの統合をサポートしています。

| ログのタイプ | JSON (Splunk、ArcSight、Qradar) をサポートする Log Analytics |
| :------- | :-------------------------------------------------------- |
|AAD 監査ログ|    ○|
|アクティビティ ログ| はい|
|ASC アラート |はい|
|診断ログ (リソース ログ)|  はい|
|VM ログ|   はい (JSON 経由ではなく転送されたイベントの場合)|


次の表では、ログのカテゴリと SIEM 統合の詳細について説明します。

[Azure ログ統合の使用](https://docs.microsoft.com/azure/security/security-azure-log-integration-get-started) – このチュートリアルでは、Azure ログ統合のインストールと、Azure WAD ストレージのログ、Azure アクティビティ ログ、Azure Security Center アラート、および Azure Active Directory の監査ログの統合について説明します。

統合シナリオ

-   [パートナーの構成手順](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – このブログ投稿では、Splunk、HP ArcSight、IBM QRadar などのパートナー ソリューションを使用できるように、Azure ログ統合を構成する方法について説明します。

-   [Azure ログ統合のよく寄せられる質問 (FAQ)](https://docs.microsoft.com/azure/security/security-azure-log-integration-faq) – この FAQ は、Azure ログ統合について寄せられる質問とその回答です。

-   [Azure ログ統合への Security Center の警告の統合](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration) – このドキュメントでは、Azure Security Center の警告を、Azure 診断および Azure 監査ログによって収集された仮想マシンのセキュリティ イベントとともに、ログ分析または SIEM ソリューションと同期させる方法について説明します。

## <a name="next-steps"></a>次のステップ

- [監査とログ記録](https://www.microsoft.com/trustcenter/security/auditingandlogging)

可視性の維持と、タイムリーなセキュリティ アラートへの迅速な対応により、データを保護します。

- [Azure 内でのセキュリティ ログと監査ログの収集](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/)

Azure のインスタンスが適切なセキュリティおよび監査ログを収集していることを確認するために使用する必要がある設定について説明します。

- [サイト コレクションの監査設定の構成](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US)

サイト コレクション管理者として、特定のユーザーが行ったアクションの履歴を取得したり、特定の期間に行われたアクションの履歴を取得したりすることができます。 

- [Office 365 のセキュリティ センターとコンプライアンス センターでの監査ログの検索](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US)

Office 365 のセキュリティ センターとコンプライアンス センターを使用して統合監査ログを検索し、Office 365 の組織のユーザーと管理者のアクティビティを表示できます。


