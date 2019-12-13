---
title: Azure リソース ログでサポートされているサービスとスキーマ
description: Azure リソース ログでサポートされているサービスとイベント スキーマについて説明します。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: reference
ms.date: 10/22/2019
author: rboucher
ms.author: robb
ms.openlocfilehash: af47195a336739d604f0eb40ce6c5c54e15547cb
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894081"
---
# <a name="supported-services-schemas-and-categories-for-azure-resource-logs"></a>Azure リソース ログでサポートされているサービス、スキーマ、カテゴリ

> [!NOTE]
> リソース ログは、以前は診断ログと呼ばれていました。

[Azure Monitor リソース ログ](../../azure-monitor/platform/resource-logs-overview.md)は、Azure サービスによって出力されるログであり、そのサービスやリソースの操作が記述されます。 Azure Monitor を通じて使用できるすべてのリソース ログには、共通の上位スキーマが共有されます。各サービスが、独自のイベントに固有のプロパティを出力するための柔軟性も備わっています。

(`resourceId` プロパティで使用可能な) リソースの種類と `category` を組み合わせて、スキーマを一意に識別します。 この記事では、リソース ログの上位スキーマについて説明し、各サービスのスキーマへのリンクを示します。

## <a name="top-level-resource-logs-schema"></a>リソース ログの上位スキーマ

| 名前 | 必須/省略可能 | 説明 |
|---|---|---|
| time | 必須 | イベントのタイムスタンプ (UTC)。 |
| resourceId | 必須 | イベントを出力したリソースのリソース ID。 テナント サービスの場合、形式は /tenants/tenant-id/providers/provider-name です。 |
| tenantId | テナント ログで必須 | このイベントが関連付けられている Active Directory テナントのテナント ID。 このプロパティは、テナントレベルのログでのみ使用されます。リソースレベルのログには表示されません。 |
| operationName | 必須 | このイベントで表される操作の名前。 イベントが RBAC 操作を表す場合、これは RBAC 操作の名前になります (例: Microsoft.Storage/storageAccounts/blobServices/blobs/Read)。 実際の文書化されている Resource Manager 操作でない場合でも、通常は、Resource Manager 操作の形式でモデル化されます (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`)。 |
| operationVersion | 省略可能 | operationName が API を使用して実行された場合は、操作に関連付けられている api-version (例: `http://myservice.windowsazure.net/object?api-version=2016-06-01`)。 この操作に対応する API がない場合、バージョンは、操作に関連付けられているプロパティが今後、変更された場合、その操作のバージョンを表します。 |
| category | 必須 | イベントのログ カテゴリ。 カテゴリは細分化されており、これを使用して、特定のリソースのログを有効または無効にすることができます。 イベントのプロパティ BLOB 内に表示されるプロパティは、特定のログ カテゴリとリソースの種類内のものと同じです。 一般的なログ カテゴリは、"Audit"、"Operational"、"Execution"、"Request" です。 |
| resultType | 省略可能 | イベントの状態。 一般的な値は、Started、In Progress、Succeeded、Failed、Active、Resolved です。 |
| resultSignature | 省略可能 | イベントの副状態。 この操作が REST API 呼び出しに対応している場合、これは、対応する REST 呼び出しの HTTP 状態コードです。 |
| resultDescription | 省略可能 | この操作を説明する静的テキスト (例: "Get storage file")。 |
| durationMs | 省略可能 | 操作時間 (ミリ秒)。 |
| callerIpAddress | 省略可能 | 操作が、一般的に利用できる IP アドレスを持つエンティティからの API 呼び出しに対応している場合は、呼び出し元 IP アドレス。 |
| correlationId | 省略可能 | 関連するイベントのセットをグループ化するために使用される GUID。 通常、2 つのイベントの operationName は同じであるものの、状態が異なる (たとえば、 "Started" と "Succeeded") 場合、同じ関連付け ID が共有されます。 これは、イベント間の他のリレーションシップを表す場合もあります。 |
| identity | 省略可能 | 操作を実行したユーザーまたはアプリケーションの ID を説明する JSON BLOB。 通常、これにはアクティブ ディレクトリからの認証および要求 / JWT トークンが含まれます。 |
| Level | 省略可能 | イベントの重大度レベル。 Informational、Warning、Error、Critical のいずれかである必要があります。 |
| location | 省略可能 | イベントを出力するリソースの領域 (例: "East US"、"France South")。 |
| properties | 省略可能 | この特定のイベント カテゴリに関連する拡張プロパティ。 すべてのカスタム/一意のプロパティは、このスキーマの "Part B" 内に配置する必要があります。 |

## <a name="service-specific-schemas-for-resource-logs"></a>サービス固有のリソース ログのスキーマ
リソース診断ログのスキーマは、リソースとログ カテゴリによって異なります。 リソース ログを使用できるようにするすべてのサービスと、そのサービスおよびカテゴリ固有のスキーマへのリンク (使用可能な場合) を、次の一覧に示します。

| Service | スキーマとドキュメント |
| --- | --- |
| Azure Active Directory | [概要](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md)、[監査ログ スキーマ](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md)、および[サインイン スキーマ](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [API Management のリソース ログ](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Application Gateway |[Application Gateway のログ記録](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Azure Automation のログ分析](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch のログ記録](../../batch/batch-diagnostics.md) |
| Azure Database for MySQL | [Azure Database for MySQL の診断ログ](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Azure Database for PostgreSQL のログ](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Azure Data Explorer | [Azure Data Explorer のログ](../../data-explorer/using-diagnostic-logs.md) |
| Cognitive Services | [Azure Cognitive Services のログ記録](../../cognitive-services/diagnostic-logging.md) |
| Container Registry | [Azure Container Registry のログ記録](../../container-registry/container-registry-diagnostics-audit-logs.md) |
| Content Delivery Network | [CDN の Azure ログ](../../cdn/cdn-azure-diagnostic-logs.md) |
| Cosmos DB | [Azure Cosmos DB のログ](../../cosmos-db/logging.md) |
| Data Factory | [Azure Monitor を使用して、データ ファクトリを監視する](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Azure Data Lake Analytics のログへのアクセス](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Azure Data Lake Store のログへのアクセス](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Azure Event Hubs のログ](../../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | スキーマは使用できません。 |
| Azure Firewall | スキーマは使用できません。 |
| IoT Hub | [IoT Hub 操作](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Azure Key Vault のログ記録](../../key-vault/key-vault-logging.md) |
| Kubernetes Service |[Azure Kubernetes のログ記録](../../aks/view-master-logs.md#log-event-schema) |
| Load Balancer |[Azure Load Balancer のログ分析](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Logic Apps B2B カスタム追跡スキーマ](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| ネットワーク セキュリティ グループ |[ネットワーク セキュリティ グループ (NSG) のためのログ分析](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDOS Protection | [Azure DDoS Protection Standard を管理する](../../virtual-network/manage-ddos-protection.md) |
| Power BI 専用 | [Azure の Power BI Embedded のログ記録](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| 復旧サービス | [Azure Backup のデータ モデル](../../backup/backup-azure-reports-data-model.md)|
| Search |[検索トラフィックの分析の有効化と使用](../../search/search-traffic-analytics.md) |
| Service Bus |[Azure Service Bus のログ](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Azure SQL Database のログ記録](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[ジョブのログ](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Traffic Manager のログのスキーマ](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| 仮想ネットワーク | スキーマは使用できません。 |
| 仮想ネットワーク ゲートウェイ | スキーマは使用できません。 |

## <a name="supported-log-categories-per-resource-type"></a>リソースの種類ごとのサポートされているログ カテゴリ
|リソースの種類|カテゴリ|カテゴリの表示名|
|---|---|---|
|Microsoft.AAD/domainServices|SystemSecurity|SystemSecurity|
|Microsoft.AAD/domainServices|AccountManagement|AccountManagement|
|Microsoft.AAD/domainServices|LogonLogoff|LogonLogoff|
|Microsoft.AAD/domainServices|ObjectAccess|ObjectAccess|
|Microsoft.AAD/domainServices|PolicyChange|PolicyChange|
|Microsoft.AAD/domainServices|PrivilegeUse|PrivilegeUse|
|Microsoft.AAD/domainServices|DetailTracking|DetailTracking|
|Microsoft.AAD/domainServices|DirectoryServiceAccess|DirectoryServiceAccess|
|Microsoft.AAD/domainServices|AccountLogon|AccountLogon|
|microsoft.aadiam/tenants|Signin|サインイン|
|Microsoft.AnalysisServices/servers|Engine|Engine|
|Microsoft.AnalysisServices/servers|Service|Service|
|Microsoft.ApiManagement/service|GatewayLogs|ApiManagement Gateway に関連するログ|
|Microsoft.AppPlatform/Spring|ApplicationConsole|アプリケーション コンソール|
|Microsoft.Automation/automationAccounts|JobLogs|ジョブ ログ|
|Microsoft.Automation/automationAccounts|JobStreams|ジョブ ストリーム|
|Microsoft.Automation/automationAccounts|DscNodeStatus|DSC ノードの状態|
|Microsoft.Batch/batchAccounts|ServiceLog|サービス ログ|
|Microsoft.BatchAI/workspaces|BaiClusterEvent|BaiClusterEvent|
|Microsoft.BatchAI/workspaces|BaiClusterNodeEvent|BaiClusterNodeEvent|
|Microsoft.BatchAI/workspaces|BaiJobEvent|BaiJobEvent|
|Microsoft.Blockchain/blockchainMembers|BlockchainApplication|ブロックチェーン アプリケーション|
|Microsoft.Blockchain/blockchainMembers|プロキシ|プロキシ|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|エンドポイントのメトリック (帯域幅、エグレスなど) を取得します。|
|Microsoft.ClassicNetwork/networksecuritygroups|ネットワーク セキュリティ グループの規則フロー イベント|ネットワーク セキュリティ グループの規則フロー イベント|
|Microsoft.CognitiveServices/accounts|Audit|[監査ログ]|
|Microsoft.CognitiveServices/accounts|RequestResponse|要求と応答のログ|
|Microsoft.ContainerRegistry/registries|ContainerRegistryRepositoryEvents|RepositoryEvent ログ (プレビュー)|
|Microsoft.ContainerRegistry/registries|ContainerRegistryLoginEvents|ログイン イベント (プレビュー)|
|Microsoft.ContainerService/managedClusters|kube-apiserver|Kubernetes API サーバー|
|Microsoft.ContainerService/managedClusters|kube-controller-manager|Kubernetes コントローラー マネージャー|
|Microsoft.ContainerService/managedClusters|kube-scheduler|Kubernetes スケジューラ|
|Microsoft.ContainerService/managedClusters|kube-audit|Kubernetes の監査|
|Microsoft.ContainerService/managedClusters|cluster-autoscaler|Kubernetes クラスター オートスケーラー|
|Microsoft.Databricks/workspaces|dbfs|Databricks ファイル システム|
|Microsoft.Databricks/workspaces|clusters|Databricks クラスター|
|Microsoft.Databricks/workspaces|accounts|Databricks アカウント|
|Microsoft.Databricks/workspaces|jobs|Databricks ジョブ|
|Microsoft.Databricks/workspaces|notebook|Databricks Notebook|
|Microsoft.Databricks/workspaces|ssh|Databricks SSH|
|Microsoft.Databricks/workspaces|ワークスペース|Databricks ワークスペース|
|Microsoft.Databricks/workspaces|secrets|Databricks シークレット|
|Microsoft.Databricks/workspaces|sqlPermissions|Databricks SQLPermissions|
|Microsoft.Databricks/workspaces|instancePools|インスタンス プール|
|Microsoft.DataCatalog/datacatalogs|ScanStatusLogEvent|ScanStatus|
|Microsoft.DataFactory/factories|ActivityRuns|パイプライン アクティビティ実行ログ|
|Microsoft.DataFactory/factories|PipelineRuns|パイプライン実行ログ|
|Microsoft.DataFactory/factories|TriggerRuns|トリガー実行ログ|
|Microsoft.DataLakeAnalytics/accounts|Audit|[監査ログ]|
|Microsoft.DataLakeAnalytics/accounts|要求数|要求ログ|
|Microsoft.DataLakeStore/accounts|Audit|[監査ログ]|
|Microsoft.DataLakeStore/accounts|Requests|要求ログ|
|Microsoft.DataShare/accounts|共有|共有|
|Microsoft.DataShare/accounts|ShareSubscriptions|共有サブスクリプション|
|Microsoft.DataShare/accounts|SentShareSnapshots|送信された共有スナップショット|
|Microsoft.DataShare/accounts|ReceivedShareSnapshots|受信された共有スナップショット|
|Microsoft.DBforMySQL/servers|MySqlSlowLogs|MySQL サーバーのログ|
|Microsoft.DBforMySQL/servers|MySqlAuditLogs|MySQL 監査ログ|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|PostgreSQL サーバー ログ|
|Microsoft.DBforPostgreSQL/servers|QueryStoreRuntimeStatistics|PostgreSQL クエリ ストアのランタイム統計|
|Microsoft.DBforPostgreSQL/servers|QueryStoreWaitStatistics|PostgreSQL クエリ ストアの待機統計|
|Microsoft.DBforPostgreSQL/serversv2|PostgreSQLLogs|PostgreSQL サーバー ログ|
|Microsoft.DBforPostgreSQL/serversv2|QueryStoreRuntimeStatistics|PostgreSQL クエリ ストアのランタイム統計|
|Microsoft.DBforPostgreSQL/serversv2|QueryStoreWaitStatistics|PostgreSQL クエリ ストアの待機統計|
|Microsoft.DesktopVirtualization/workspaces|チェックポイント|チェックポイント|
|Microsoft.DesktopVirtualization/workspaces|Error|Error|
|Microsoft.DesktopVirtualization/workspaces|管理|管理|
|Microsoft.DesktopVirtualization/workspaces|フィード|フィード|
|Microsoft.DesktopVirtualization/applicationGroups|チェックポイント|チェックポイント|
|Microsoft.DesktopVirtualization/applicationGroups|Error|Error|
|Microsoft.DesktopVirtualization/applicationGroups|管理|管理|
|Microsoft.DesktopVirtualization/hostPools|チェックポイント|チェックポイント|
|Microsoft.DesktopVirtualization/hostPools|Error|Error|
|Microsoft.DesktopVirtualization/hostPools|管理|管理|
|Microsoft.DesktopVirtualization/hostPools|接続|接続|
|Microsoft.DesktopVirtualization/hostPools|HostRegistration|HostRegistration|
|Microsoft.Devices/IotHubs|Connections|Connections|
|Microsoft.Devices/IotHubs|DeviceTelemetry|デバイス テレメトリ|
|Microsoft.Devices/IotHubs|C2DCommands|C2D コマンド|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|デバイス ID の操作|
|Microsoft.Devices/IotHubs|FileUploadOperations|ファイルのアップロード操作|
|Microsoft.Devices/IotHubs|Routes|Routes|
|Microsoft.Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft.Devices/IotHubs|C2DTwinOperations|C2D ツイン操作|
|Microsoft.Devices/IotHubs|TwinQueries|ツイン クエリ|
|Microsoft.Devices/IotHubs|JobsOperations|ジョブ操作|
|Microsoft.Devices/IotHubs|DirectMethods|ダイレクト メソッド|
|Microsoft.Devices/IotHubs|DistributedTracing|分散トレース (プレビュー)|
|Microsoft.Devices/IotHubs|構成|構成|
|Microsoft.Devices/IotHubs|DeviceStreams|デバイス ストリーム (プレビュー)|
|Microsoft.Devices/provisioningServices|DeviceOperations|デバイス操作|
|Microsoft.Devices/provisioningServices|ServiceOperations|サービス操作|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft.DocumentDB/databaseAccounts|PartitionKeyStatistics|PartitionKeyStatistics|
|Microsoft.DocumentDB/databaseAccounts|ControlPlaneRequests|ControlPlaneRequests|
|Microsoft.EnterpriseKnowledgeGraph/services|AuditEvent|AuditEvent ログ|
|Microsoft.EnterpriseKnowledgeGraph/services|DataIssue|DataIssue log|
|Microsoft.EnterpriseKnowledgeGraph/services|Requests|構成ログ|
|Microsoft.EventHub/namespaces|ArchiveLogs|アーカイブ ログ|
|Microsoft.EventHub/namespaces|OperationalLogs|操作ログ|
|Microsoft.EventHub/namespaces|AutoScaleLogs|自動スケール ログ|
|Microsoft.EventHub/namespaces|KafkaCoordinatorLogs|Kafka コーディネーター ログ|
|Microsoft.EventHub/namespaces|KafkaUserErrorLogs|Kafka ユーザー エラー ログ|
|Microsoft.EventHub/namespaces|EventHubVNetConnectionEvent|VNet/IP フィルタリング接続ログ|
|Microsoft.EventHub/namespaces|CustomerManagedKeyUserLogs|顧客管理キーのログ|
|Microsoft.HealthcareApis/services|AuditLogs|監査ログ|
|Microsoft.Insights/AutoscaleSettings|AutoscaleEvaluations|自動スケーリング検証|
|Microsoft.Insights/AutoscaleSettings|AutoscaleScaleActions|自動スケーリングのスケーリング操作|
|Microsoft.IoTSpaces/Graph|Trace|Trace|
|Microsoft.IoTSpaces/Graph|運用時|運用時|
|Microsoft.IoTSpaces/Graph|Audit|Audit|
|Microsoft.IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft.IoTSpaces/Graph|イングレス|イングレス|
|Microsoft.IoTSpaces/Graph|エグレス|エグレス|
|Microsoft.KeyVault/vaults|AuditEvent|[監査ログ]|
|Microsoft.Kusto/Clusters|SucceededIngestion|成功した取り込み操作|
|Microsoft.Kusto/Clusters|FailedIngestion|失敗した取り込み操作|
|Microsoft.Logic/workflows|WorkflowRuntime|ワークフロー ランタイムの診断イベント|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|統合アカウント追跡イベント|
|Microsoft.MachineLearningServices/workspaces|AmlComputeClusterEvent|AmlComputeClusterEvent|
|Microsoft.MachineLearningServices/workspaces|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|Microsoft.MachineLearningServices/workspaces|AmlComputeJobEvent|AmlComputeJobEvent|
|Microsoft.Media/mediaservices|KeyDeliveryRequests|キー配信要求|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|ネットワーク セキュリティ グループ イベント|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|ネットワーク セキュリティ グループの規則数|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupFlowEvent|ネットワーク セキュリティ グループの規則フロー イベント|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|DDoS 保護通知|
|Microsoft.Network/publicIPAddresses|DDoSMitigationFlowLogs|DDoS 軽減策に関する意思決定のフロー ログ|
|Microsoft.Network/publicIPAddresses|DDoSMitigationReports|DDoS 軽減策のレポート|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|VM 保護アラート|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|アプリケーション ゲートウェイのアクセス ログ|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|アプリケーション ゲートウェイのパフォーマンス ログ|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|アプリケーション ゲートウェイのファイアウォール ログ|
|Microsoft.Network/azurefirewalls|AzureFirewallApplicationRule|Azure Firewall アプリケーション ルール|
|Microsoft.Network/azurefirewalls|AzureFirewallNetworkRule|Azure Firewall ネットワーク ルール|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|ゲートウェイ診断ログ|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|トンネル診断ログ|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|ルート診断ログ|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|IKE 診断ログ|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|P2S 診断ログ|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|Traffic Manager プローブの正常性結果イベント|
|Microsoft.Network/expressRouteCircuits|PeeringRouteLog|ピアリングのルート テーブルのログ|
|Microsoft.Network/vpnGateways|GatewayDiagnosticLog|ゲートウェイ診断ログ|
|Microsoft.Network/vpnGateways|TunnelDiagnosticLog|トンネル診断ログ|
|Microsoft.Network/vpnGateways|RouteDiagnosticLog|ルート診断ログ|
|Microsoft.Network/vpnGateways|IKEDiagnosticLog|IKE 診断ログ|
|Microsoft.Network/frontdoors|FrontdoorAccessLog|Frontdoor アクセス ログ|
|Microsoft.Network/frontdoors|FrontdoorWebApplicationFirewallLog|Frontdoor Web アプリケーション ファイアウォール ログ|
|Microsoft.Network/p2sVpnGateways|GatewayDiagnosticLog|ゲートウェイ診断ログ|
|Microsoft.Network/p2sVpnGateways|IKEDiagnosticLog|IKE 診断ログ|
|Microsoft.Network/p2sVpnGateways|P2SDiagnosticLog|P2S 診断ログ|
|Microsoft.Network/bastionHosts|BastionAuditLogs|Bastion 監査ログ|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|ロード バランサーのアラート イベント|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|ロード バランサーのプローブ正常性状態|
|Microsoft.PowerBIDedicated/capacities|Engine|Engine|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Azure Backup レポート データ|
|Microsoft.RecoveryServices/Vaults|CoreAzureBackup|コア Azure Backup データ|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupJobs|アドオン Azure Backup ジョブ データ|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupAlerts|アドオン Azure Backup アラート データ|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupPolicy|アドオン Azure Backup ポリシー データ|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupStorage|アドオン Azure Backup ストレージ データ|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupProtectedInstance|アドオン Azure Backup 保護されたインスタンス データ|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Azure Site Recovery ジョブ|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Azure Site Recovery イベント|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Azure Site Recovery レプリケートされた項目|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Azure Site Recovery レプリケーション状態|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery 回復ポイント|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Azure Site Recovery のレプリケーション データ アップロード速度|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery で保護されたディスクのデータ変更頻度|
|Microsoft.Search/searchServices|OperationLogs|[操作ログ]|
|Microsoft.ServiceBus/namespaces|OperationalLogs|操作ログ|
|Microsoft.Sql/servers/databases|SQLInsights|SQL Insights|
|Microsoft.Sql/servers/databases|AutomaticTuning|自動チューニング|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|クエリ ストアのランタイム統計|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|クエリ ストアの待機統計|
|Microsoft.Sql/servers/databases|Errors|Errors|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|データベースの待機統計|
|Microsoft.Sql/servers/databases|Timeouts|Timeouts|
|Microsoft.Sql/servers/databases|Blocks|Blocks|
|Microsoft.Sql/servers/databases|デッドロック|デッドロック|
|Microsoft.Sql/servers/databases|Audit|[監査ログ]|
|Microsoft.Sql/servers/databases|SQLSecurityAuditEvents|SQL セキュリティ監査イベント|
|Microsoft.Sql/servers/databases|DmsWorkers|DMS worker|
|Microsoft.Sql/servers/databases|ExecRequests|実行要求|
|Microsoft.Sql/servers/databases|RequestSteps|要求ステップ|
|Microsoft.Sql/servers/databases|SqlRequests|SQL 要求|
|Microsoft.Sql/servers/databases|Waits|待機|
|Microsoft.Sql/managedInstances|ResourceUsageStats|リソース使用統計|
|Microsoft.Sql/managedInstances|SQLSecurityAuditEvents|SQL セキュリティ監査イベント|
|Microsoft.Sql/managedInstances/databases|SQLInsights|SQL Insights|
|Microsoft.Sql/managedInstances/databases|QueryStoreRuntimeStatistics|クエリ ストアのランタイム統計|
|Microsoft.Sql/managedInstances/databases|QueryStoreWaitStatistics|クエリ ストアの待機統計|
|Microsoft.Sql/managedInstances/databases|Errors|Errors|
|Microsoft.Storage/storageAccounts/tableServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/tableServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/tableServices|StorageDelete|StorageDelete|
|Microsoft.Storage/storageAccounts/blobServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/blobServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/blobServices|StorageDelete|StorageDelete|
|Microsoft.Storage/storageAccounts/fileServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/fileServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/fileServices|StorageDelete|StorageDelete|
|Microsoft.Storage/storageAccounts/queueServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/queueServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/queueServices|StorageDelete|StorageDelete|
|Microsoft.StreamAnalytics/streamingjobs|Execution|実行|
|Microsoft.StreamAnalytics/streamingjobs|作成|Authoring|
|microsoft.web/hostingenvironments|AppServiceEnvironmentPlatformLogs|Azure App Service Environment のプラットフォーム ログ|
|microsoft.web/sites|FunctionAppLogs|関数アプリケーション ログ|
|microsoft.web/sites|AppServiceHTTPLogs|HTTP ログ|
|microsoft.web/sites|AppServiceConsoleLogs|App Service コンソール ログ|
|microsoft.web/sites|AppServiceAppLogs|App Service アプリケーション ログ|
|microsoft.web/sites|AppServiceFileAuditLogs|サイト コンテンツの変更に関する監査ログ|
|microsoft.web/sites|AppServiceAuditLogs|監査ログへのアクセス|
|microsoft.web/sites/slots|FunctionAppLogs|関数アプリケーション ログ|
|microsoft.web/sites/slots|AppServiceHTTPLogs|HTTP ログ|
|microsoft.web/sites/slots|AppServiceConsoleLogs|コンソール ログ|
|microsoft.web/sites/slots|AppServiceAppLogs|Application Logs|
|microsoft.web/sites/slots|AppServiceFileAuditLogs|サイト コンテンツの変更に関する監査ログ|
|microsoft.web/sites/slots|AppServiceAuditLogs|監査ログへのアクセス|

## <a name="next-steps"></a>次の手順

* [リソース ログの詳細を確認する](../../azure-monitor/platform/resource-logs-overview.md)
* [リソースのリソース ログを **Event Hubs** にストリーミングする](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [Azure Monitor REST API を使用してリソース ログの診断設定を変更する](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Log Analytics を使用した、Azure ストレージからのログの分析](../../azure-monitor/platform/collect-azure-metrics-logs.md)
