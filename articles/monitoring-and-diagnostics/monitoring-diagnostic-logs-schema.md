---
title: "Azure 診断ログでサポートされているサービスとスキーマ | Microsoft Docs"
description: "Azure 診断ログでサポートされているサービスとイベント スキーマについて説明します。"
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: fe8887df-b0e6-46f8-b2c0-11994d28e44f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: johnkem
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: aa4fa6e0310b2725005dfa34e3225c89fb4282d6
ms.contentlocale: ja-jp
ms.lasthandoff: 08/24/2017

---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Azure 診断ログでサポートされているサービス、スキーマ、カテゴリ

[Azure リソース診断ログ](monitoring-overview-of-diagnostic-logs.md)は、Azure リソースから出力されるログであり、そのリソースの操作を説明します。 これらのログは、リソースの種類ごとに固有です。 この記事では、サポートされる一連のサービスと、各サービスで出力されるイベントのイベント スキーマについて説明します。 また、この記事にはリソースの種類ごとに使用可能なログ カテゴリの完全な一覧も含まれています。

## <a name="supported-services-and-schemas-for-resource-diagnostic-logs"></a>リソース診断ログでサポートされているサービスとスキーマ
リソース診断ログのスキーマは、リソースとログ カテゴリによって異なります。   

| サービス | スキーマとドキュメント |
| --- | --- |
| API Management | スキーマは使用できません。 |
| Application Gateway |[Application Gateway の診断ログ](../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Azure Automation のログ分析](../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch 診断ログ](../batch/batch-diagnostics.md) |
| Customer Insights | スキーマは使用できません。 |
| Content Delivery Network | スキーマは使用できません。 |
| Cosmos DB | スキーマは使用できません。 |
| Data Lake Analytics |[Azure Data Lake Analytics の診断ログへのアクセス](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Azure Data Lake Store の診断ログへのアクセス](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Azure Event Hubs の診断ログ](../event-hubs/event-hubs-diagnostic-logs.md) |
| Key Vault |[Azure Key Vault のログ記録](../key-vault/key-vault-logging.md) |
| Load Balancer |[Azure Load Balancer のログ分析](../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Logic Apps B2B カスタム追跡スキーマ](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| ネットワーク セキュリティ グループ |[ネットワーク セキュリティ グループ (NSG) のためのログ分析](../virtual-network/virtual-network-nsg-manage-log.md) |
| 復旧サービス | スキーマは使用できません。|
| Search |[検索トラフィックの分析の有効化と使用](../search/search-traffic-analytics.md) |
| Server Management | スキーマは使用できません。 |
| Service Bus |[Azure Service Bus の診断ログ](../service-bus-messaging/service-bus-diagnostic-logs.md) |
| Stream Analytics |[ジョブの診断ログ](../stream-analytics/stream-analytics-job-diagnostic-logs.md) |

## <a name="supported-log-categories-per-resource-type"></a>リソースの種類ごとのサポートされているログ カテゴリ
|リソースの種類|カテゴリ|カテゴリの表示名|
|---|---|---|
|Microsoft.ApiManagement/service|GatewayLogs|ApiManagement Gateway に関連するログ|
|Microsoft.Automation/automationAccounts|JobLogs|ジョブ ログ|
|Microsoft.Automation/automationAccounts|JobStreams|ジョブ ストリーム|
|Microsoft.Automation/automationAccounts|DscNodeStatus|DSC ノードの状態|
|Microsoft.Batch/batchAccounts|ServiceLog|サービス ログ|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|エンドポイントのメトリック (帯域幅、エグレスなど) を取得します。|
|Microsoft.CustomerInsights/hubs|AuditEvents|AuditEvents|
|Microsoft.DataLakeAnalytics/accounts|Audit|Audit Logs|
|Microsoft.DataLakeAnalytics/accounts|要求数|要求ログ|
|Microsoft.DataLakeStore/accounts|Audit|Audit Logs|
|Microsoft.DataLakeStore/accounts|要求数|要求ログ|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.EventHub/namespaces|ArchiveLogs|アーカイブ ログ|
|Microsoft.EventHub/namespaces|OperationalLogs|操作ログ|
|Microsoft.EventHub/namespaces|AutoScaleLogs|自動スケール ログ|
|Microsoft.KeyVault/vaults|AuditEvent|Audit Logs|
|Microsoft.Logic/workflows|WorkflowRuntime|ワークフロー ランタイムの診断イベント|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|統合アカウント追跡イベント|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|ネットワーク セキュリティ グループ イベント|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|ネットワーク セキュリティ グループの規則数|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|ロード バランサーのアラート イベント|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|ロード バランサーのプローブ正常性状態|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|アプリケーション ゲートウェイのアクセス ログ|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|アプリケーション ゲートウェイのパフォーマンス ログ|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|アプリケーション ゲートウェイのファイアウォール ログ|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Azure Backup レポート データ|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Azure Site Recovery ジョブ|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Azure Site Recovery イベント|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Azure Site Recovery レプリケートされた項目|
|Microsoft.Search/searchServices|OperationLogs|操作ログ|
|Microsoft.ServiceBus/namespaces|OperationalLogs|操作ログ|
|Microsoft.StreamAnalytics/streamingjobs|実行|実行|
|Microsoft.StreamAnalytics/streamingjobs|作成|作成|

## <a name="next-steps"></a>次のステップ

* [診断ログの詳細の確認](monitoring-overview-of-diagnostic-logs.md)
* [リソース診断ログを **Event Hubs** にストリーミングする](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Azure Monitor REST API を使用してリソース診断設定を変更する](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Log Analytics を使用した、Azure ストレージからのログの分析](../log-analytics/log-analytics-azure-storage.md)

