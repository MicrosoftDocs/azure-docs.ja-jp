---
title: Azure のログと監査 | Microsoft Docs
description: ログ データを使用して、アプリケーションに関する深い洞察を得る方法について説明します。
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/14/2019
ms.author: tomsh
ms.openlocfilehash: edadb369461bb3865dd6894c3329e7079fa9d13f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66752559"
---
# <a name="azure-logging-and-auditing"></a>Azure のログと監査

Azure にはさまざまな構成可能なセキュリティ監査およびログのオプションが用意されており、セキュリティ ポリシーとメカニズムのギャップを特定するのに役立ちます。 この記事では、Azure でホストされているサービスからのセキュリティ ログの生成、収集、分析について説明します。

> [!Note]
> この記事で紹介しているいくつかの推奨事項に従った結果、データ、ネットワーク、またはコンピューティング リソースの使用量が増加したり、ライセンスまたはサブスクリプションのコストが増加したりする場合があります。

## <a name="types-of-logs-in-azure"></a>Azure のログのタイプ

クラウド アプリケーションは、動的なパーツを多数使った複雑な構成になっています。 ログは、アプリケーションを稼働した状態にしておくために役立つデータを提供します。 ログは、これまでに発生した問題のトラブルシューティングや潜在的な問題の防止に役立ちます。 さらに、アプリケーションのパフォーマンスや保守容易性を向上させたり、手作業での介入が必要な操作を自動化したりするうえで役立ちます。

Azure のログは、次の種類に分類されます。
* **コントロール/管理ログ**: Azure Resource Manager の CREATE、UPDATE、DELETE 操作に関する情報を提供します。 詳細については、[Azure アクティビティ ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)に関するページを参照してください。

* **データ プレーン ログ**: Azure のリソース使用の一環として発生したイベントに関する情報を提供します。 この種類のログの例として、仮想マシンの Windows イベント システム ログ、セキュリティ ログ、アプリケーション ログや、Azure Monitor で構成される[診断ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)があります。

* **処理済みイベント**: ユーザーに代わって処理された分析済みのイベント/アラートに関する情報を提供します。 この種類の例として、[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) がサブスクリプションを処理して分析し、簡潔なセキュリティ アラートを提供する [Azure Security Center のアラート](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)があります。

次の表には、Azure で使用できる最も重要な種類のログを示します。

| ログのカテゴリ | ログのタイプ | 使用法 | 統合 |
| ------------ | -------- | ------ | ----------- |
|[アクティビティ ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|Azure Resource Manager リソースのコントロールプレーン イベント|   サブスクリプションのリソースに対して実行された操作に関する分析情報を提供します。|    Rest API、[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|[Azure Diagnostics ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|サブスクリプションの Azure Resource Manager リソースの操作に関してよく使用されるデータ|    リソース自体が実行した操作に関する分析情報を提供します。| Azure Monitor、[Stream](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|[Azure AD レポート](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)|ログとレポート | ユーザーのサインイン アクティビティと、ユーザーおよびグループの管理に関するシステム アクティビティの情報を報告します。|[Graph API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart)|
|[仮想マシンとクラウド サービス](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm)|Windows イベント ログ サービスと Linux Syslog|  仮想マシンのシステム データとログ データを取り込み、そのデータを任意のストレージ アカウントに転送します。|   Azure Monitor の Windows (Microsoft Azure Diagnostics ([WAD](https://docs.microsoft.com/azure/azure-diagnostics)) ストレージを使用) と Linux|
|[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|ストレージ ログ (ストレージ アカウントのメトリック データの提供)|トレース要求に関する分析情報を提供し、使用傾向を分析して、ストレージ アカウントの問題を診断します。|   REST API または[クライアント ライブラリ](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[ネットワーク セキュリティ グループ (NSG) のフロー ログ](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|JSON 形式 (送信および受信のフローをルールごとに表示)|ネットワーク セキュリティ グループを介したイングレスおよびエグレス IP トラフィックに関する情報を表示します。|[Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)|
|[Application Insight](https://docs.microsoft.com/azure/application-insights/app-insights-overview)|ログ、例外、カスタム診断|   複数のプラットフォームの Web 開発者向けにアプリケーション パフォーマンス管理 (APM) サービスを提供します。| REST API、[Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|データの処理/セキュリティ アラート|    Azure Security Center のアラート、Azure Monitor のログ アラート|    セキュリティに関する情報と警告を提供します。|  REST API、JSON|

### <a name="activity-logs"></a>アクティビティ ログ

[Azure アクティビティ ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)は、サブスクリプション内のリソースに対して実行された操作に関する分析情報を提供します。 アクティビティ ログは、サブスクリプションの[コントロール プレーン イベント](https://driftboatdave.com/2016/10/13/azure-auditing-options-for-your-custom-reporting-needs/)を報告するため、以前は "監査ログ" または "操作ログ" と呼ばれていました。 

アクティビティ ログは、書き込み操作 (つまり、PUT、POST、DELETE) について "いつだれが何を" 行ったのかを確認する際に役立ちます。 また、アクティビティ ログは、操作の状態やその他の関連するプロパティを把握する際にも役立ちます。 アクティビティ ログには、読み取り (GET) 操作は含まれません。

この記事では、PUT、POST、DELETE は、リソースのアクティビティ ログに含まれている書き込み操作すべてを表しています。 たとえば、アクティビティ ログを使用すると、問題のトラブルシューティング時にエラーを探したり、組織のユーザーがどのようにリソースを変更したかを監視したりできます。

![アクティビティ ログの図](./media/azure-log-audit/azure-log-audit-fig1.png)

Azure portal、[Azure CLI](https://docs.microsoft.com/azure/storage/storage-azure-cli)、PowerShell コマンドレット、[Azure Monitor REST API](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough) を使用して、アクティビティ ログからイベントを取得できます。 アクティビティ ログには、90 日間のデータ保持期間があります。

アクティビティ ログ イベントの統合シナリオ:

* [アクティビティ ログ イベントでトリガーされる電子メール アラートまたは Webhook アラートを作成する](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-auditlog-to-webhook-email)。

* サード パーティーのサービスや PowerBI などのカスタム分析ソリューションで取り込むために、[アクティビティ ログをイベント ハブにストリーム配信する](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs)。

* [PowerBI コンテンツ パック](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)を使用して、アクティビティ ログを PowerBI で分析する。

* [アーカイブや手動での検査に使用するためにアクティビティ ログをストレージ アカウントに保存する](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-activity-log)。 ログ プロファイルを使用して、保持期間 (日数) を指定できます。

* Azure Portal でアクティビティ ログを照会して表示する。

* PowerShell コマンドレット、Azure CLI、または REST API を使用してアクティビティ ログを照会する。

* ログ プロファイルを含むアクティビティ ログを [Azure Monitor ログ](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)にエクスポートする。

ログを出力するサブスクリプションとは別のサブスクリプションで、ストレージ アカウントまたは[イベント ハブ 名前空間](https://docs.microsoft.com/azure/event-hubs/event-hubs-resource-manager-namespace-event-hub-enable-archive)を使用できます。 設定を構成するユーザーは、両方のサブスクリプションに対して適切な[ロールベースのアクセス制御 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) アクセス権限を持っている必要があります。

### <a name="azure-diagnostics-logs"></a>Azure Diagnostics ログ

Azure Diagnostics ログはリソースによって出力され、そのリソースの操作に関してよく使用される豊富なデータを提供します。 これらのログの内容は、リソースの種類によって異なります。 たとえば、[Windows イベント システム ログ](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events)は VM の診断ログのカテゴリであり、[BLOB、テーブル、キューのログ](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account)はストレージ アカウントの診断ログのカテゴリです。 診断ログは、サブスクリプションのリソースに対して実行された操作に関する分析情報を提供するアクティビティ ログとは異なります。

![Azure Diagnostics ログの図](./media/azure-log-audit/azure-log-audit-fig2.png)

Azure Diagnostics ログには、Azure portal、PowerShell、Azure CLI、REST API など、複数の構成オプションがあります。

**統合シナリオ**

* 監査や手動での検査に使用するために[ストレージ アカウント](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs)に保存する。 診断設定を使用して保持期間 (日数) を指定できます。

* サード パーティのサービスや [PowerBI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/) などのカスタム分析ソリューションで取り込むために[イベント ハブにストリーム配信する](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs)。

* [Azure Monitor ログ](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)を使用して分析する。

**サポートされるサービス、診断ログ用スキーマ、リソースの種類ごとのサポートされるログ カテゴリ**


| Service | スキーマとドキュメント | リソースの種類 | Category |
| ------- | ------------- | ------------- | -------- |
|Azure Load Balancer| [Load Balancer 用の Azure Monitor のログ (プレビュー)](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)|Microsoft.Network/loadBalancers<br>Microsoft.Network/loadBalancers|    LoadBalancerAlertEvent<br>LoadBalancerProbeHealthStatus|
|ネットワーク セキュリティ グループ|[ネットワーク セキュリティ グループの Azure Monitor ログ](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)|Microsoft.Network/networksecuritygroups<br>Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent<br>NetworkSecurityGroupRuleCounter|
|Azure Application Gateway|[Application Gateway の診断ログ](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)|Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog<br>ApplicationGatewayPerformanceLog<br>ApplicationGatewayFirewallLog|
|Azure Key Vault|[Key Vault ログ](https://docs.microsoft.com/azure/key-vault/key-vault-logging)|Microsoft.KeyVault/vaults|AuditEvent|
|Azure Search|[検索トラフィックの分析の有効化と使用](https://docs.microsoft.com/azure/search/search-traffic-analytics)|Microsoft.Search/searchServices|OperationLogs|
|Azure Data Lake Store|[Data Lake Store の診断ログへのアクセス](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs)|Microsoft.DataLakeStore/accounts<br>Microsoft.DataLakeStore/accounts|Audit<br>Requests|
|Azure Data Lake Analytics|[Data Lake Analytics の診断ログへのアクセス](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)|Microsoft.DataLakeAnalytics/accounts<br>Microsoft.DataLakeAnalytics/accounts|Audit<br>Requests|
|Azure Logic Apps|[Logic Apps B2B カスタム追跡スキーマ](https://docs.microsoft.com/azure/logic-apps/logic-apps-track-integration-account-custom-tracking-schema)|Microsoft.Logic/workflows<br>Microsoft.Logic/integrationAccounts|WorkflowRuntime<br>IntegrationAccountTrackingEvents|
|Azure Batch|[Azure Batch の診断ログ](https://docs.microsoft.com/azure/batch/batch-diagnostics)|Microsoft.Batch/batchAccounts|ServiceLog|
|Azure Automation|[Azure Automation 用の Azure Monitor ログ](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|Microsoft.Automation/automationAccounts<br>Microsoft.Automation/automationAccounts|JobLogs<br>JobStreams|
|Azure Event Hubs|[Event Hubs の診断ログ](https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs)|Microsoft.EventHub/namespaces<br>Microsoft.EventHub/namespaces|ArchiveLogs<br>OperationalLogs|
|Azure Stream Analytics|[ジョブの診断ログ](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)|Microsoft.StreamAnalytics/streamingjobs<br>Microsoft.StreamAnalytics/streamingjobs|Execution<br>Authoring|
|Azure Service Bus|[Service Bus の診断ログ](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-diagnostic-logs)|Microsoft.ServiceBus/namespaces|OperationalLogs|

### <a name="azure-active-directory-reporting"></a>Azure Active Directory レポート

Azure Active Directory (Azure AD) には、ユーザーのディレクトリに関するセキュリティ レポート、アクティビティ レポート、監査レポートが含まれています。 [Azure AD の監査レポート](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide)は、ユーザーの Azure AD インスタンスで発生した特権アクションを特定するのに役立ちます。 特権アクションには、昇格の変更 (例: ロールの作成、パスワードのリセット)、ポリシー構成の変更 (例: パスワード ポリシー)、ディレクトリ構成の変更 (例: ドメインのフェデレーション設定の変更) などがあります。

このレポートでは、イベント名、アクションを実行したユーザー、変更によって影響を受けた対象リソース、日時 (UTC) に関する監査レコードが提供されます。 ユーザーは、[監査ログの表示](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)に関するページに説明されているように、[Azure portal](https://portal.azure.com/) を使用して Azure AD に対する監査イベントのリストを取得できます。 

含まれているレポートを次の表に示します。

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
||パスワード リセット アクティビティ||

これらのレポートのデータは、セキュリティ情報イベント管理 (SIEM) システム、監査、ビジネス インテリジェンス ツールなどのアプリケーションに役立ちます。 Azure AD レポート API は、一連の REST ベースの API を使用してプログラムによってデータにアクセスできるようにします。 これらの [API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) は、さまざまなプログラミング言語とツールから呼び出すことができます。

Azure AD の監査レポートのイベントは、180 日間保持されます。

> [!Note]
> レポートの保持の詳細については、[Azure AD レポートの保持ポリシー](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention)に関するページを参照してください。

監査イベントの保持期間を長くしたい場合は、Reporting API を使用して、定期的に[監査イベント](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events)を別のデータ ストアにプルしてください。

### <a name="virtual-machine-logs-that-use-azure-diagnostics"></a>Azure Diagnostics を使用する仮想マシン ログ

[Azure Diagnostics](https://docs.microsoft.com/azure/azure-diagnostics) は、デプロイされたアプリケーションで診断データを収集できるようにする Azure 内の機能です。 いくつかのソースのいずれかで診断拡張機能を使用することができます。 現在サポートされているのは、[Azure Cloud Service の Web ロールと worker ロール](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me)です。

![Azure Diagnostics を使用する仮想マシン ログ](./media/azure-log-audit/azure-log-audit-fig3.png)

### <a name="azure-virtual-machineslearnpathsdeploy-a-website-with-azure-virtual-machines-that-are-running-microsoft-windows-and-service-fabrichttpsdocsmicrosoftcomazureservice-fabricservice-fabric-overview"></a>Microsoft Windows および [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) が稼動している [Azure 仮想マシン](/learn/paths/deploy-a-website-with-azure-virtual-machines/)

仮想マシンで Azure Diagnostics を有効にするには、次のいずれかを実行します。

* [Visual Studio を使用して Azure 仮想マシンをトレースする](https://docs.microsoft.com/azure/vs-azure-tools-debug-cloud-services-virtual-machines)

* [Azure 仮想マシンの Azure Diagnostics をリモートで設定する](https://docs.microsoft.com/azure/virtual-machines-dotnet-diagnostics)

* [PowerShell を使用して Azure 仮想マシンで診断を設定する](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

* [Azure Resource Manager テンプレートを使用して、監視および診断を含む Windows 仮想マシンを作成する](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="storage-analytics"></a>Storage Analytics

[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) では、ストレージ アカウントのメトリック データをログに記録して提供します。 このデータを使用して、要求のトレース、使用傾向の分析、ストレージ アカウントの問題の診断を行うことができます。 Storage Analytics のログは、[Azure BLOB、Azure キュー、Azure テーブルの各ストレージ サービス](https://docs.microsoft.com/azure/storage/storage-introduction)で使用できます。 Storage Analytics は、ストレージ サービスに対する要求の成功と失敗についての詳細な情報をログに記録します。

この情報を使用すると、個々の要求を監視したり、ストレージ サービスに関する問題を診断したりできます。 要求は、ベスト エフォートでログに記録されます。 ログ エントリが作成されるのは、サービス エンドポイントに対して行われた要求がある場合に限られます。 たとえば、ストレージ アカウントの BLOB エンドポイントにはアクティビティが存在するが、テーブル エンドポイントまたはキュー エンドポイントには存在しない場合、BLOB ストレージ サービスに関連したログだけが作成されます。

Storage Analytics を使用するには、監視するサービスごとに個別に有効にします。 これは、[Azure portal](https://portal.azure.com/) で有効にすることができます。 詳細については、「[Azure portal でのストレージ アカウントの監視](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account)」を参照してください。 また、プログラムから REST API またはクライアント ライブラリを使用して有効にすることもできます。 サービスごとに Storage Analytics を個別に有効にするには、Set Service Properties 操作を使用します。

集計データは、既知の BLOB (ログの場合) と既知のテーブル (メトリックの場合) に格納されます。集計データには、Blob Storage サービスと Table Storage サービスの API を使用してアクセスできます。

ストレージ アカウントの合計の制限とは別に、Storage Analytics には、格納されたデータの量に関して 20 テラバイト (TB) の制限があります。 すべてのログは、ストレージ アカウントに対して Storage Analytics を有効にしたときに自動的に作成される $logs という名前のコンテナー内の[ブロック BLOB](https://docs.microsoft.com/azure/storage/storage-analytics) に格納されます。

> [!Note]
> * 課金ポリシーとデータ保有ポリシーの詳細については、「[Storage Analytics and Billing (Storage Analytics と課金)](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing)」を参照してください。
> * ストレージ アカウントの制限の詳細については、「[Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](https://docs.microsoft.com/azure/storage/storage-scalability-targets)」を参照してください。

Storage Analytics では、次の種類の認証済み要求と匿名要求が記録されます。

| 認証済み  | 匿名|
| :------------- | :-------------|
| 成功した要求 | 成功した要求 |
|失敗した要求 (タイムアウト、スロットル、ネットワーク、承認などに関する各種エラー) | Shared Access Signature を使用した要求 (失敗した要求と成功した要求を含む) |
| Shared Access Signature を使用した要求 (失敗した要求と成功した要求を含む) |クライアントとサーバーの両方のタイムアウト エラー |
|   データの分析要求 |    エラー コード 304 (変更されていません) で失敗した GET 要求 |
| Storage Analytics そのものによる要求 (ログの作成/削除など) は記録されません。 ログに記録されるデータの全一覧については、「[Storage Analytics logged operations and status messages (Storage Analytics によって記録される操作やステータス メッセージ)](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages)」および「[Storage Analytics log format (Storage Analytics のログの形式)](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format)」を参照してください。 | その他の失敗した匿名要求は一切記録されません。 ログに記録されるデータの全一覧については、「[Storage Analytics logged operations and status messages (Storage Analytics によって記録される操作やステータス メッセージ)](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages)」および「[Storage Analytics log format (Storage Analytics のログの形式)](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format)」を参照してください。 |

### <a name="azure-networking-logs"></a>Azure のネットワーク ログ

Azure でのネットワーク ログおよび監視が指す意味は広く、大まかに次の 2 つのカテゴリを対象としています。

* [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview):シナリオベースのネットワーク監視は、Network Watcher の機能を使用して実現できます。 このサービスには、パケット キャプチャ、次のホップ、IP フロー検証、セキュリティ グループ ビュー、NSG フロー ログなどが搭載されています。 シナリオ レベルの監視では、個別のネットワーク リソースの監視とは対照的に、ネットワーク リソースを隅から隅まで確認できます。

* [リソース監視](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview):リソース レベルの監視は、診断ログ、メトリック、トラブルシューティング、リソース正常性という 4 つの機能で構成されています。 これらの機能はすべて、ネットワーク リソース レベルで構築されています。

![Azure のネットワーク ログ](./media/azure-log-audit/azure-log-audit-fig4.png)

Network Watcher は地域サービスであり、ネットワーク シナリオ レベルで Azure 内と Azure 間の状態を監視して診断できます。 Network Watcher に搭載されているネットワークの診断および監視ツールは、Azure 内のネットワークを把握、診断し、分析情報を得る際に役立ちます。

### <a name="network-security-group-flow-logging"></a>ネットワーク セキュリティ グループのフロー ログ

[NSG のフロー ログ](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)は、NSG を介したイングレスおよびエグレス IP トラフィックに関する情報の表示に使用できる、Network Watcher の機能です。 これらのフロー ログは JSON 形式で記述され、以下を示します。
* ルールごとの送信フローと受信フロー。
* フローの適用先の NIC。
* フローに関する 5 タプル情報: 送信元または宛先の IP、送信元または宛先ポート、プロトコル。
* トラフィックが許可されたか、拒否されたか。

フロー ログは NSG を対象としていますが、その他のログと同じようには表示されません。 フロー ログは、ストレージ アカウント内でのみ保存されます。

その他のログで見られる同様の保持ポリシーがフロー ログに適用されます。 ログには保持期間ポリシーがあり、1 日 から 365 日まで設定できます。 リテンション期間ポリシーが設定されていない場合、ログは無期限に保持されます。

**診断ログ**

定期イベントおよび自然発生イベントがネットワーク リソースにより作成され、ストレージ アカウント内に記録されるか、イベント ハブまたは Azure Monitor ログへ送信されます。 このログから、リソースの正常性に関する分析情報が得られます。 これらは、Power BI や Azure Monitor ログなどのツールで確認できます。 診断ログを確認する方法については、[Azure Monitor ログ](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics)に関するページを参照してください。

![[診断ログ]](./media/azure-log-audit/azure-log-audit-fig5.png)

診断ログは、[Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)、[ネットワーク セキュリティ グループ](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)、ルート、[Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics) で利用できます。

Network Watcher には、診断ログ ビューが用意されています。 このビューには、診断ログをサポートするすべてのネットワーク リソースが表示されます。 このビューから、ネットワーク リソースの有効化および無効化を手軽に素早く行うことができます。


Network Watcher には現在、前述のログ機能に加えて、次の機能があります。
- [トポロジ](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview):リソース グループ内のネットワーク リソース間のさまざまな相互接続および関連付けを表示するネットワーク レベルのビューが用意されています。

- [可変パケット キャプチャ](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview):仮想マシンで送受信されるパケット データをキャプチャします。 詳細なフィルター オプションときめ細やかなコントロール (時間やサイズの制限の設定など) により、多様性が備わっています。 パケット データは、 *.cap* ファイル形式で BLOB ストアまたはローカル ディスクに保管できます。

- [IP フロー検証](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview):フロー情報の 5 タプル パケット パラメーター (つまり、宛先 IP、送信元 IP、宛先ポート、送信元ポート、プロトコル) に基づいてパケットが許可されたか拒否されたかを確認します。 パケットがセキュリティ グループにより拒否された場合、そのパケットを拒否した規則とグループが返されます。

- [次ホップ](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview):Azure ネットワーク ファブリックにおけるルーティング対象パケットの次ホップを特定します。これにより、誤って構成されたユーザー定義のルートを診断できます。

- [セキュリティ グループ ビュー](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview):VM に適用されている有効な適用セキュリティ規則を確認できます。

- [Virtual Network ゲートウェイと接続のトラブルシューティング](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest):仮想ネットワーク ゲートウェイと接続のトラブルシューティングに役立ちます。

- [ネットワーク サブスクリプションの制限](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview):ネットワーク リソースの使用状況を制限と照らし合わせて確認できます。

### <a name="application-insights"></a>Application Insights

[Azure Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) は、複数のプラットフォームの Web 開発者向けの拡張可能な APM サービスです。 実行中の Web アプリケーションを監視するには、これを使用します。 パフォーマンスに異常があると、自動的に検出されます。 組み込まれている強力な分析ツールを使えば、問題を診断し、ユーザーがアプリを使用して実行している操作を把握できます。

Application Insights は、パフォーマンスと使いやすさを継続的に改善できるように設計されています。

オンプレミスまたはクラウドのどちらでホストされているかに関係なく、.NET、Node.js、Java EE などのさまざまなプラットフォーム上のアプリに役立ちます。 DevOps プロセスと統合され、さまざまな開発ツールとの接続ポイントを備えています。

![Application Insights の図](./media/azure-log-audit/azure-log-audit-fig6.png)

Application Insights は、開発チーム用のツールであり、アプリのパフォーマンスや使用状況を把握できるように支援します。 以下を監視します。

* **要求率、応答時間、および失敗率**:最も人気の高いページ、時刻、およびユーザーの場所がわかります。 最もパフォーマンスの高いページを確認します。 要求が増えると応答時間と失敗率が高くなる場合は、リソースに問題がある可能性があります。

* **依存率、応答時間、および失敗率**:外部サービスによって動作速度が遅くなっているかどうかがわかります。

* **例外**:集計された統計を分析します。または特定のインスタンスを選択し、スタック トレースと関連する要求を調べます。 サーバーとブラウザーの両方の例外が報告されます。

* **ページ ビューと読み込みのパフォーマンス**:ユーザーのブラウザーからレポートを取得します。

* **AJAX 呼び出し**:Web ページのレート、応答時間、およびエラー率を取得します。

* **ユーザー数とセッション数**。

* **パフォーマンス カウンター**:Windows または Linux サーバー マシンからデータ (CPU、メモリ、ネットワーク使用率など) を取得します。

* **ホスト診断**:Docker または Azure からデータを取得します。

* **診断トレース ログ**:トレース イベントを要求に関連付けることができるように、アプリからデータを取得します。

* **カスタム イベントとメトリック**:販売された品目や勝利したゲームなどのビジネス イベントを追跡するためにクライアントまたはサーバーのコード内に書き込んだデータを取得します。

次の表は、統合シナリオとその説明の一覧です。

| 統合シナリオ | 説明 |
| --------------------- | :---------- |
|[アプリケーション マップ](https://docs.microsoft.com/azure/application-insights/app-insights-app-map)|アプリのコンポーネントを、主要なメトリックとアラートと共に表示します。|
|[インスタンス データの診断の検索](https://docs.microsoft.com/azure/application-insights/app-insights-diagnostic-search)| 要求、例外、依存関係の呼び出し、ログ トレースおよびページ ビューなどのイベントを検索およびフィルター処理します。|
|[集計データのメトリックス エクスプローラー](https://docs.microsoft.com/azure/azure-monitor/app/metrics-explorer)|要求、失敗、および例外の比率、応答時間、ページの読み込み時間などの集計データを調査、フィルター処理、およびセグメント分割します。|
|[ダッシュボード](https://docs.microsoft.com/azure/azure-monitor/app/overview-dashboard)|複数のリソースからのデータをマッシュアップし、他のユーザーと共有します。 複数コンポーネントのアプリケーションと、チーム ルームでの継続的な表示に最適です。|
|[Live Metrics Stream](https://docs.microsoft.com/azure/azure-monitor/app/live-stream)|新しいビルドをデプロイする場合、このほぼリアルタイムのパフォーマンス インジケーターを監視し、すべてが期待どおりに動作することを確認します。|
|[Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)|この強力なクエリ言語を使用して、アプリのパフォーマンスと使用状況に関する難しい質問に回答します。|
|[自動および手動のアラート](https://docs.microsoft.com/azure/application-insights/app-insights-alerts)|アプリのテレメトリの通常パターンに対して自動アラートを適応し、通常とは異なるパターンがある場合にそのアラートがトリガーされます。 カスタムまたは標準のメトリックスの特定レベルでアラートを設定することもできます。|
|[Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)|パフォーマンス データをコードで表示します。 スタック トレースからコードに移動します。|
|[Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)|使用状況のメトリックをその他のビジネス インテリジェンスと統合します。|
|[REST API](https://dev.applicationinsights.io/)|メトリックと生データのクエリを実行するコードを記述します。|
|[連続エクスポート](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)|生データが届いたらストレージに一括エクスポートします。|

### <a name="azure-security-center-alerts"></a>Azure Security Center のアラート

Azure Security Center の脅威検出は、Azure のリソース、ネットワーク、接続されているパートナー ソリューションからセキュリティ情報を自動的に収集することによって機能します。 この情報を分析し、ときには複数の情報源から得た情報との関連性を探りながら、脅威を特定します。 Security Center では、セキュリティの警告に優先順位が、脅威に対処するための推奨事項と共に割り当てられます。 詳細については、[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)に関するページを参照してください。

![Azure Security Center の図](./media/azure-log-audit/azure-log-audit-fig7.png)

Security Center には、シグネチャ ベースの手法とは比較にならない高度なセキュリティ分析が採用されています。 これは、大規模なデータおよび[機械学習](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/)テクノロジに比較的進歩をもたらし、クラウド ファブリック全体でイベントを評価します。 このように、手作業に頼った手法や攻撃の進化を予測する手法では特定できない脅威も検出できます。 こうしたセキュリティ分析の例を次に示します。

* **統合された脅威インテリジェンス**:グローバルな脅威インテリジェンスを適用して、既知の有害因子を探します。情報源としては、Microsoft 製品とサービス、Microsoft Digital Crimes Unit (DCU)、Microsoft Security Response Center (MSRC)、外部フィードがあります。

* **行動分析**:既知のパターンを適用することによって悪質な行動を検出します。

* **異常検出**:統計プロファイルを使用して、過去のベースラインを構築します。 確立された基準からの逸脱に、攻撃ベクトルとの一致が疑われる場合、警告が生成されます。

セキュリティ操作およびインシデント対応チームの多くが、SIEM ソリューションを、セキュリティ警告のトリアージと調査の開始点として使用します。 Azure Log Integration を使用すると、Security Center のアラートや、Azure Diagnostics および Azure 監査ログによって収集された仮想マシンのセキュリティ イベントを Azure Monitor ログまたは SIEM ソリューションとほぼリアルタイムで同期させることができます。

## <a name="azure-monitor-logs"></a>Azure Monitor ログ

Azure Monitor ログは Azure のサービスであり、ご利用のクラウドおよびオンプレミスの環境内にあるリソースによって生成されたデータを収集して分析するのに役立ちます。 統合検索とカスタム ダッシュボードによるリアルタイムの分析情報が得られるため、物理的な場所に関係なくすべてのワークロードおよびサーバーの多数のレコードをただちに分析できます。

![Azure Monitor ログの図](./media/azure-log-audit/azure-log-audit-fig8.png)

Azure Monitor ログの核となる機能は、Azure でホストされている Log Analytics ワークスペースです。 Azure Monitor ログは、データ ソースを構成してソリューションをサブスクリプションに追加することで、接続されているソースからワークスペースのデータを収集します。 データ ソースとソリューションはそれぞれ、異なるレコードの種類を作成します。また、レコードの種類それぞれには独自のプロパティ セットが用意されています。 ただし、ソースとソリューションは、引き続き、ワークスペースに対するクエリでまとめて分析できます。 この機能により、同じツールと方法を使用して、さまざまなソースによって収集された各種データを操作できます。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

接続先のソースは、Azure Monitor ログによって収集されるデータを生成するコンピューターとその他のリソースです。 ソースには、直接接続している [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) および [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) のコンピューターにインストールされているエージェント、または[接続されている System Center Operations Manager 管理グループ](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents)のエージェントを含めることができます。 また、Azure Monitor ログは [Azure ストレージ アカウント](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage)からもデータを収集できます。

[データ ソース](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources)は、接続先の各ソースから収集されるさまざまな種類のデータです。 ソースには、[IIS ログ](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs)や[カスタム テキスト ログ](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-custom-logs)などのソースに加えて、[Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) および Linux エージェントからのイベントと[パフォーマンス データ](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters)が含まれます。 収集するデータ ソースをそれぞれ構成すると、構成は接続されているソースごとに自動的に提供されます。

[Azure サービスのログとメトリックを収集する](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage)方法は 4 つあります。

* Azure Diagnostics から Azure Monitor ログに直接 (次の表では "**診断**")

* Azure Diagnostics から Azure Storage 経由で Azure Monitor ログに (次の表では "**Storage**")

* Azure サービスのコネクタ (次の表では "**コネクタ**")

* データを収集して Azure Monitor ログに投稿するためのスクリプト (次の表では空白セル、表に記載されていないサービス用)

| Service | リソースの種類 | ログ | メトリック | 解決策 |
| :------ | :------------ | :--- | :------ | :------- |
|Azure Application Gateway| Microsoft.Network/<br>applicationGateways|  診断|診断|    [Azure Application](https://docs.microsoft.com/azure/azure-monitor/insights/azure-networking-analytics) [Gateway 分析](https://docs.microsoft.com/azure/azure-monitor/insights/azure-networking-analytics#azure-application-gateway-analytics-solution-in-azure-monitor)|
|Application Insights||     コネクタ|  コネクタ|  [Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) [コネクタ (プレビュー)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)|
|Azure Automation アカウント| Microsoft.Automation/<br>AutomationAccounts|    診断||       [詳細情報](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|
|Azure Batch アカウント|  Microsoft.Batch/<br>batchAccounts|  診断|    診断||
|従来のクラウド サービス||       Storage||       [詳細情報](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage-iis-table)|
|Cognitive Services|    Microsoft.CognitiveServices/<br>accounts|       診断|||
|Azure Data Lake Analytics| Microsoft.DataLakeAnalytics/<br>accounts|   診断|||
|Azure Data Lake Store| Microsoft.DataLakeStore/<br>accounts|   診断|||
|Azure イベント ハブの名前空間| Microsoft.EventHub/<br>namespaces|  診断|    診断||
|Azure IoT Hub| Microsoft.Devices/<br>IotHubs||     診断||
|Azure Key Vault|   Microsoft.KeyVault/<br>vaults|  診断  || [Key Vault Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault)|
|Azure Load Balancer|   Microsoft.Network/<br>loadBalancers|    診断|||
|Azure Logic Apps|  Microsoft.Logic/<br>workflows|  診断|    診断||
||Microsoft.Logic/<br>integrationAccounts||||
|ネットワーク セキュリティ グループ|   Microsoft.Network/<br>networksecuritygroups|診断||   [Azure ネットワーク セキュリティ グループ分析](https://docs.microsoft.com/azure/azure-monitor/insights/azure-networking-analytics#azure-application-gateway-and-network-security-group-analytics)|
|Recovery コンテナー|   Microsoft.RecoveryServices/<br>vaults|||[Azure Recovery Services Analytics (プレビュー)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
|Search サービス|   Microsoft.Search/<br>searchServices|    診断|    診断||
|Service Bus 名前空間| Microsoft.ServiceBus/<br>namespaces|    診断|診断|    [Service Bus Analytics (プレビュー)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
|Service Fabric||       Storage||    [Service Fabric Analytics (プレビュー)](https://docs.microsoft.com/azure/log-analytics/log-analytics-service-fabric)|
|SQL (v12)| Microsoft.Sql/<br>servers/<br>databases||       診断||
||Microsoft.Sql/<br>servers/<br>elasticPools||||
|Storage|||         スクリプト| [Azure Storage Analytics (プレビュー)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution)|
|Azure Virtual Machines|    Microsoft.Compute/<br>virtualMachines|  拡張機能|  拡張機能||
||||診断||
|仮想マシン スケール セット|    Microsoft.Compute/<br>virtualMachines    ||診断||
||Microsoft.Compute/<br>virtualMachineScaleSets/<br>virtualMachines||||
|Web サーバー ファーム|Microsoft.Web/<br>serverfarms||   診断
|Websites|  Microsoft.Web/<br>sites ||      診断|    [詳細情報](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webappazure-oms-monitoring)|
||Microsoft.Web/<br>sites/<br>slots||||


## <a name="log-integration-with-on-premises-siem-systems"></a>オンプレミスの SIEM システムを使用した Log Integration

Azure Log Integration を使用して、Azure リソースからの未加工のログをオンプレミスの SIEM システム (セキュリティ情報/イベント管理システム) に統合できます。 AzLog ダウンロードは 2018 年 6 月 27 日に無効になりました。 今後必要な対応のガイダンスについては、[Azure 監視を使って SIEM ツールと統合する](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)方法に関する投稿を確認してください。

![Log Integration の図](./media/azure-log-audit/azure-log-audit-fig9.png)

Log Integration では、Windows 仮想マシン、Azure アクティビティ ログ、Azure Security Center アラート、Azure リソース プロバイダー ログから Azure Diagnostics を収集します。 この統合により、オンプレミスかクラウド内化に関係なく、すべての資産に対して統合されたダッシュボードが提供されます。これにより、セキュリティ イベントの集計、関連付け、分析を実行し、アラートを生成できます。

Log Integration では現在、Azure アクティビティ ログ、Windows 仮想マシンからの Windows イベント ログと Azure サブスクリプション、Azure Security Center アラート、Azure Diagnostics ログ、Azure AD 監査ログの統合をサポートしています。

| ログのタイプ | Azure Monitor ログによる JSON (Splunk、ArcSight、IBM QRadar) のサポート |
| :------- | :-------------------------------------------------------- |
|Azure AD 監査ログ|   はい|
|アクティビティ ログ| はい|
|Security Center のアラート |はい|
|診断ログ (リソース ログ)|  はい|
|VM ログ|   はい (JSON 経由ではなく転送されたイベントの場合)|

[Azure Log Integration の使用](https://docs.microsoft.com/azure/security/security-azure-log-integration-get-started):このチュートリアルでは、Azure Log Integration のインストールに加え、Azure Storage のログ、Azure アクティビティ ログ、Azure Security Center アラート、Azure AD 監査ログの統合について説明します。

SIEM の統合シナリオ:

* [パートナーの構成手順](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/):このブログ投稿では、Splunk、HP ArcSight、IBM QRadar などのパートナー ソリューションを使用できるように、Azure Log Integration を構成する方法について説明します。

* [Azure Log Integration の FAQ](https://docs.microsoft.com/azure/security/security-azure-log-integration-faq):この記事は、Azure ログ統合について寄せられる質問とその回答です。

* [Security Center アラートと Azure Log Integration の統合](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration):この記事では、Security Center アラート、Azure Diagnostics ログで収集された仮想マシンのセキュリティ イベント、Azure 監査ログを Azure Monitor ログまたは SIEM ソリューションと同期させる方法について説明します。

## <a name="next-steps"></a>次の手順

- [監査とログ記録](https://docs.microsoft.com/azure/security/security-management-and-monitoring-overview):可視性の維持と、タイムリーなセキュリティ アラートへの迅速な対応により、データを保護します。

- [Azure 内でのセキュリティ ログと監査ログの収集](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/):Azure インスタンスで適切なセキュリティ ログと監査ログが収集されるようにこれらの設定を適用します。

- [サイト コレクションの監査設定を構成する](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US):サイト コレクションの管理者の場合、個々のユーザーのアクションの履歴と特定の期間内に行われたアクションの履歴を取得します。 

- [Office 365 セキュリティ/コンプライアンス センターで監査ログを検索する](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US):Office 365 セキュリティ/コンプライアンス センターを使用して統合監査ログを検索し、Office 365 の組織のユーザーと管理者のアクティビティを表示できます。


