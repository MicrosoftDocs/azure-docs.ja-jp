---
title: Azure Monitor リソース ログでサポートされているサービスとカテゴリ
description: Azure Monitor のリファレンス - Azure リソース ログでサポートされているサービスとイベント スキーマについて説明します。
ms.subservice: logs
ms.topic: reference
ms.date: 06/03/2020
ms.openlocfilehash: 81f79b81c03e7996d7f6d45b002d8160740c3c14
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87318302"
---
# <a name="supported-categories-for-azure-resource-logs"></a>Azure リソース ログでサポートされているカテゴリ

> [!NOTE]
> リソース ログは、以前は診断ログと呼ばれていました。 この名前は、Azure Monitor によって収集されたログの種類が、Azure リソースだけでなくそれ以外のものも含むように移行したため、2019 年 10 月に変更されました。

[Azure Monitor リソース ログ](./platform-logs-overview.md)は、Azure サービスによって出力されるログであり、そのサービスやリソースの操作が記述されます。 Azure Monitor を通じて使用できるすべてのリソース ログには、共通の上位スキーマが共有されます。各サービスが、独自のイベントに固有のプロパティを出力するための柔軟性も備わっています。

(`resourceId` プロパティで使用可能な) リソースの種類と `category` を組み合わせて、スキーマを一意に識別します。 サービス固有のフィールドを含むすべてのリソース ログに共通のスキーマがあり、それぞれのログ カテゴリに対して追加されています。 詳細については、[Azure リソース ログの共通のスキーマとサービス固有のスキーマ]()に関するページを参照してください

## <a name="supported-log-categories-per-resource-type"></a>リソースの種類ごとのサポートされているログ カテゴリ

各リソースの種類で使用可能なログの種類の一覧を次に示します。 

一部のカテゴリは、特定の種類のリソースに対してのみサポートされています。 リソースが不足していると思われる場合は、リソース固有のドキュメントを参照してください。 たとえば、Microsoft.Sql/servers/databases カテゴリは、すべての種類のデータベースで使用できるわけではありません。 詳細については、[SQL Database の診断ログに関する情報](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md)を参照してください。 

まだ何かが不足している場合は、この記事の下部にある GitHub コメントを開くことができます。

## <a name="microsoftaadiamtenants"></a>microsoft.aadiam/tenants

|カテゴリ|カテゴリの表示名|
|---|---|
|サインイン|サインイン|


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|カテゴリ|カテゴリの表示名|
|---|---|
|エンジン|エンジン|
|サービス|サービス|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|カテゴリ|カテゴリの表示名|
|---|---|
|GatewayLogs|ApiManagement Gateway に関連するログ|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|カテゴリ|カテゴリの表示名|
|---|---|
|ApplicationConsole|アプリケーション コンソール|
|SystemLogs|システム ログ|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|カテゴリ|カテゴリの表示名|
|---|---|
|JobLogs|ジョブ ログ|
|JobStreams|ジョブ ストリーム|
|DscNodeStatus|DSC ノードの状態|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|カテゴリ|カテゴリの表示名|
|---|---|
|ServiceLog|サービス ログ|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces

|カテゴリ|カテゴリの表示名|
|---|---|
|BaiClusterEvent|BaiClusterEvent|
|BaiClusterNodeEvent|BaiClusterNodeEvent|
|BaiJobEvent|BaiJobEvent|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|カテゴリ|カテゴリの表示名|
|---|---|
|BlockchainApplication|ブロックチェーン アプリケーション|
|プロキシ|プロキシ|


## <a name="microsoftblockchaincordamembers"></a>Microsoft.Blockchain/cordaMembers

|カテゴリ|カテゴリの表示名|
|---|---|
|BlockchainApplication|ブロックチェーン アプリケーション|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationfirewallpolicies

|カテゴリ|カテゴリの表示名|
|---|---|
|WebApplicationFirewallLogs|Web アプリケーション ファイアウォールのログ|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/profiles

|カテゴリ|カテゴリの表示名|
|---|---|
|AzureCdnAccessLog|Azure Cdn アクセス ログ|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft.Cdn/profiles/endpoints

|カテゴリ|カテゴリの表示名|
|---|---|
|CoreAnalytics|エンドポイントのメトリック (帯域幅、エグレスなど) を取得します。|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft.ClassicNetwork/networksecuritygroups

|カテゴリ|カテゴリの表示名|
|---|---|
|ネットワーク セキュリティ グループの規則フロー イベント|ネットワーク セキュリティ グループの規則フロー イベント|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|カテゴリ|カテゴリの表示名|
|---|---|
|Audit|[監査ログ]|
|RequestResponse|要求と応答のログ|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|カテゴリ|カテゴリの表示名|
|---|---|
|ContainerRegistryLoginEvents|ログイン イベント|
|ContainerRegistryRepositoryEvents|RepositoryEvent ログ|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|カテゴリ|カテゴリの表示名|
|---|---|
|cluster-autoscaler|Kubernetes クラスター オートスケーラー|
|kube-apiserver|Kubernetes API サーバー|
|kube-audit|Kubernetes の監査|
|kube-controller-manager|Kubernetes コントローラー マネージャー|
|kube-scheduler|Kubernetes スケジューラ|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/resourceproviders

|カテゴリ|カテゴリの表示名|
|---|---|
|AuditLogs|MiniRP 呼び出しの監査ログ|


## <a name="microsoftdatabricksworkspaces"></a>Microsoft.Databricks/workspaces

|カテゴリ|カテゴリの表示名|
|---|---|
|accounts|Databricks アカウント|
|clusters|Databricks クラスター|
|dbfs|Databricks ファイル システム|
|instancePools|インスタンス プール|
|jobs|Databricks ジョブ|
|ノートブック|Databricks Notebook|
|secrets|Databricks シークレット|
|sqlPermissions|Databricks SQLPermissions|
|ssh|Databricks SSH|
|ワークスペース|Databricks ワークスペース|


## <a name="microsoftdatacatalogdatacatalogs"></a>Microsoft.DataCatalog/datacatalogs

|カテゴリ|カテゴリの表示名|
|---|---|
|ScanStatusLogEvent|ScanStatus|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|カテゴリ|カテゴリの表示名|
|---|---|
|ActivityRuns|パイプライン アクティビティ実行ログ|
|PipelineRuns|パイプライン実行ログ|
|TriggerRuns|トリガー実行ログ|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|カテゴリ|カテゴリの表示名|
|---|---|
|Audit|[監査ログ]|
|Requests|要求ログ|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|カテゴリ|カテゴリの表示名|
|---|---|
|MySqlAuditLogs|MariaDB 監査ログ|
|MySqlSlowLogs|MariaDB サーバー ログ|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|カテゴリ|カテゴリの表示名|
|---|---|
|MySqlAuditLogs|MySQL 監査ログ|
|MySqlSlowLogs|MySQL サーバーのログ|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|カテゴリ|カテゴリの表示名|
|---|---|
|PostgreSQLLogs|PostgreSQL サーバー ログ|
|QueryStoreRuntimeStatistics|PostgreSQL クエリ ストアのランタイム統計|
|QueryStoreWaitStatistics|PostgreSQL クエリ ストアの待機統計|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|カテゴリ|カテゴリの表示名|
|---|---|
|PostgreSQLLogs|PostgreSQL サーバー ログ|


## <a name="microsoftdbforpostgresqlsingleservers"></a>Microsoft.DBforPostgreSQL/singleservers

|カテゴリ|カテゴリの表示名|
|---|---|
|PostgreSQLLogs|PostgreSQL サーバー ログ|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Microsoft.DesktopVirtualization/applicationgroups

|カテゴリ|カテゴリの表示名|
|---|---|
|Checkpoint|Checkpoint|
|エラー|エラー|
|管理|管理|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Microsoft.DesktopVirtualization/hostpools

|カテゴリ|カテゴリの表示名|
|---|---|
|Checkpoint|Checkpoint|
|Connection|Connection|
|エラー|エラー|
|HostRegistration|HostRegistration|
|管理|管理|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Microsoft.DesktopVirtualization/workspaces

|カテゴリ|カテゴリの表示名|
|---|---|
|Checkpoint|Checkpoint|
|エラー|エラー|
|フィード|フィード|
|管理|管理|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|カテゴリ|カテゴリの表示名|
|---|---|
|C2DCommands|C2D コマンド|
|C2DTwinOperations|C2D ツイン操作|
|構成|構成|
|接続|接続|
|D2CTwinOperations|D2CTwinOperations|
|DeviceIdentityOperations|デバイス ID の操作|
|DeviceStreams|デバイス ストリーム (プレビュー)|
|DeviceTelemetry|デバイス テレメトリ|
|DirectMethods|ダイレクト メソッド|
|DistributedTracing|分散トレース (プレビュー)|
|FileUploadOperations|ファイルのアップロード操作|
|JobsOperations|ジョブ操作|
|ルート|ルート|
|TwinQueries|ツイン クエリ|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|カテゴリ|カテゴリの表示名|
|---|---|
|DeviceOperations|デバイス操作|
|ServiceOperations|サービス操作|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|カテゴリ|カテゴリの表示名|
|---|---|
|CassandraRequests|CassandraRequests|
|ControlPlaneRequests|ControlPlaneRequests|
|DataPlaneRequests|DataPlaneRequests|
|MongoRequests|MongoRequests|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|
|PartitionKeyStatistics|PartitionKeyStatistics|
|QueryRuntimeStatistics|QueryRuntimeStatistics|


## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft.EnterpriseKnowledgeGraph/services

|カテゴリ|カテゴリの表示名|
|---|---|
|AuditEvent|AuditEvent ログ|
|DataIssue|DataIssue log|
|Requests|構成ログ|

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|カテゴリ|カテゴリの表示名|
|---|---|
|DeliveryFailures|配信エラーのログ|
|PublishFailures|発行エラーのログ|


## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|カテゴリ|カテゴリの表示名|
|---|---|
|DeliveryFailures|配信エラーのログ|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|カテゴリ|カテゴリの表示名|
|---|---|
|DeliveryFailures|配信エラーのログ|
|PublishFailures|発行エラーのログ|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|カテゴリ|カテゴリの表示名|
|---|---|
|ArchiveLogs|アーカイブ ログ|
|AutoScaleLogs|自動スケール ログ|
|CustomerManagedKeyUserLogs|カスタマー マネージド キーのログ|
|EventHubVNetConnectionEvent|VNet/IP フィルタリング接続ログ|
|KafkaCoordinatorLogs|Kafka コーディネーター ログ|
|KafkaUserErrorLogs|Kafka ユーザー エラー ログ|
|OperationalLogs|操作ログ|


## <a name="microsofthealthcareapisservices"></a>Microsoft.HealthcareApis/services

|カテゴリ|カテゴリの表示名|
|---|---|
|AuditLogs|監査ログ|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|カテゴリ|カテゴリの表示名|
|---|---|
|AutoscaleEvaluations|自動スケーリング検証|
|AutoscaleScaleActions|自動スケーリングのスケーリング操作|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|カテゴリ|カテゴリの表示名|
|---|---|
|AppAvailabilityResults|可用性の結果|
|AppBrowserTimings|ブラウザーのタイミング|
|AppDependencies|依存関係|
|AppEvents|events|
|AppExceptions|例外|
|AppMetrics|メトリック|
|AppPageViews|ページ ビュー|
|AppPerformanceCounters|パフォーマンス カウンター|
|AppRequests|Requests|
|AppSystemEvents|システム イベント|
|AppTraces|トレース|


## <a name="microsoftiotspacesgraph"></a>Microsoft.IoTSpaces/Graph

|カテゴリ|カテゴリの表示名|
|---|---|
|Audit|Audit|
|エグレス|エグレス|
|イングレス|イングレス|
|運用時|運用時|
|Trace|Trace|
|UserDefinedFunction|UserDefinedFunction|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|カテゴリ|カテゴリの表示名|
|---|---|
|AuditEvent|[監査ログ]|


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|カテゴリ|カテゴリの表示名|
|---|---|
|FailedIngestion|失敗した取り込み操作|
|SucceededIngestion|成功した取り込み操作|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft.Logic/integrationAccounts

|カテゴリ|カテゴリの表示名|
|---|---|
|IntegrationAccountTrackingEvents|統合アカウント追跡イベント|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|カテゴリ|カテゴリの表示名|
|---|---|
|WorkflowRuntime|ワークフロー ランタイムの診断イベント|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|カテゴリ|カテゴリの表示名|
|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|
|AmlComputeJobEvent|AmlComputeJobEvent|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|カテゴリ|カテゴリの表示名|
|---|---|
|KeyDeliveryRequests|キー配信要求|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|カテゴリ|カテゴリの表示名|
|---|---|
|ApplicationGatewayAccessLog|アプリケーション ゲートウェイのアクセス ログ|
|ApplicationGatewayFirewallLog|アプリケーション ゲートウェイのファイアウォール ログ|
|ApplicationGatewayPerformanceLog|アプリケーション ゲートウェイのパフォーマンス ログ|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|カテゴリ|カテゴリの表示名|
|---|---|
|AzureFirewallApplicationRule|Azure Firewall アプリケーション ルール|
|AzureFirewallNetworkRule|Azure Firewall ネットワーク ルール|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft.Network/bastionHosts

|カテゴリ|カテゴリの表示名|
|---|---|
|BastionAuditLogs|Bastion 監査ログ|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|カテゴリ|カテゴリの表示名|
|---|---|
|PeeringRouteLog|ピアリングのルート テーブルのログ|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|カテゴリ|カテゴリの表示名|
|---|---|
|FrontdoorAccessLog|Frontdoor アクセス ログ|
|FrontdoorWebApplicationFirewallLog|Frontdoor Web アプリケーション ファイアウォール ログ|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|カテゴリ|カテゴリの表示名|
|---|---|
|LoadBalancerAlertEvent|ロード バランサーのアラート イベント|
|LoadBalancerProbeHealthStatus|ロード バランサーのプローブ正常性状態|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft.Network/networksecuritygroups

|カテゴリ|カテゴリの表示名|
|---|---|
|NetworkSecurityGroupEvent|ネットワーク セキュリティ グループ イベント|
|NetworkSecurityGroupFlowEvent|ネットワーク セキュリティ グループの規則フロー イベント|
|NetworkSecurityGroupRuleCounter|ネットワーク セキュリティ グループの規則数|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|カテゴリ|カテゴリの表示名|
|---|---|
|DDoSMitigationFlowLogs|DDoS 軽減策に関する意思決定のフロー ログ|
|DDoSMitigationReports|DDoS 軽減策のレポート|
|DDoSProtectionNotifications|DDoS 保護通知|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|カテゴリ|カテゴリの表示名|
|---|---|
|ProbeHealthStatusEvents|Traffic Manager プローブの正常性結果イベント|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|カテゴリ|カテゴリの表示名|
|---|---|
|GatewayDiagnosticLog|ゲートウェイ診断ログ|
|IKEDiagnosticLog|IKE 診断ログ|
|P2SDiagnosticLog|P2S 診断ログ|
|RouteDiagnosticLog|ルート診断ログ|
|TunnelDiagnosticLog|トンネル診断ログ|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|カテゴリ|カテゴリの表示名|
|---|---|
|VMProtectionAlerts|VM 保護アラート|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|カテゴリ|カテゴリの表示名|
|---|---|
|エンジン|エンジン|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft.RecoveryServices/Vaults

|カテゴリ|カテゴリの表示名|
|---|---|
|AddonAzureBackupAlerts|アドオン Azure Backup アラート データ|
|AddonAzureBackupJobs|アドオン Azure Backup ジョブ データ|
|AddonAzureBackupPolicy|アドオン Azure Backup ポリシー データ|
|AddonAzureBackupProtectedInstance|アドオン Azure Backup 保護されたインスタンス データ|
|AddonAzureBackupStorage|アドオン Azure Backup ストレージ データ|
|AzureBackupReport|Azure Backup レポート データ|
|AzureSiteRecoveryEvents|Azure Site Recovery イベント|
|AzureSiteRecoveryJobs|Azure Site Recovery ジョブ|
|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery で保護されたディスクのデータ変更頻度|
|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery 回復ポイント|
|AzureSiteRecoveryReplicatedItems|Azure Site Recovery レプリケートされた項目|
|AzureSiteRecoveryReplicationDataUploadRate|Azure Site Recovery のレプリケーション データ アップロード速度|
|AzureSiteRecoveryReplicationStats|Azure Site Recovery レプリケーション状態|
|CoreAzureBackup|コア Azure Backup データ|


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|カテゴリ|カテゴリの表示名|
|---|---|
|HybridConnectionsEvent|HybridConnections イベント|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|カテゴリ|カテゴリの表示名|
|---|---|
|OperationLogs|[操作ログ]|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|カテゴリ|カテゴリの表示名|
|---|---|
|OperationalLogs|操作ログ|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|カテゴリ|カテゴリの表示名|
|---|---|
|AllLogs|Azure SignalR Service ログ。|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|カテゴリ|カテゴリの表示名|
|---|---|
|DevOpsOperationsAudit|Devops 操作の監査ログ|
|ResourceUsageStats|リソース使用統計|
|SQLSecurityAuditEvents|SQL セキュリティ監査イベント|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft.Sql/managedInstances/databases

|カテゴリ|カテゴリの表示名|
|---|---|
|エラー|エラー|
|QueryStoreRuntimeStatistics|クエリ ストアのランタイム統計|
|QueryStoreWaitStatistics|クエリ ストアの待機統計|
|SQLInsights|SQL Insights|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|カテゴリ|カテゴリの表示名|
|---|---|
|AutomaticTuning|自動チューニング|
|Blocks|Blocks|
|DatabaseWaitStatistics|データベースの待機統計|
|デッドロック|デッドロック|
|DevOpsOperationsAudit|Devops 操作の監査ログ|
|DmsWorkers|DMS worker|
|エラー|エラー|
|ExecRequests|実行要求|
|QueryStoreRuntimeStatistics|クエリ ストアのランタイム統計|
|QueryStoreWaitStatistics|クエリ ストアの待機統計|
|RequestSteps|要求ステップ|
|SQLInsights|SQL Insights|
|SqlRequests|SQL 要求|
|SQLSecurityAuditEvents|SQL セキュリティ監査イベント|
|Timeouts|Timeouts|
|待機|待機|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|カテゴリ|カテゴリの表示名|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|カテゴリ|カテゴリの表示名|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|カテゴリ|カテゴリの表示名|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|カテゴリ|カテゴリの表示名|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|カテゴリ|カテゴリの表示名|
|---|---|
|Authoring|Authoring|
|実行|実行|


## <a name="microsoftwebhostingenvironments"></a>microsoft.web/hostingenvironments

|カテゴリ|カテゴリの表示名|
|---|---|
|AppServiceEnvironmentPlatformLogs|Azure App Service Environment のプラットフォーム ログ|


## <a name="microsoftwebsites"></a>microsoft.web/sites

|カテゴリ|カテゴリの表示名|
|---|---|
|AppServiceAppLogs|App Service アプリケーション ログ|
|AppServiceAuditLogs|監査ログへのアクセス|
|AppServiceConsoleLogs|App Service コンソール ログ|
|AppServiceFileAuditLogs|サイト コンテンツの変更に関する監査ログ|
|AppServiceHTTPLogs|HTTP ログ|
|FunctionAppLogs|関数アプリケーション ログ|
|ScanLogs|ウイルス対策スキャンのログ|


## <a name="microsoftwebsitesslots"></a>microsoft.web/sites/slots


|カテゴリ|カテゴリの表示名|
|---|---|
|AppServiceAppLogs|App Service アプリケーション ログ|
|AppServiceAuditLogs|監査ログへのアクセス|
|AppServiceConsoleLogs|App Service コンソール ログ|
|AppServiceFileAuditLogs|サイト コンテンツの変更に関する監査ログ|
|AppServiceHTTPLogs|HTTP ログ|
|FunctionAppLogs|関数アプリケーション ログ|
|ScanLogs|ウイルス対策スキャンのログ|


## <a name="next-steps"></a>次の手順

* [リソース ログの詳細を確認する](./platform-logs-overview.md)
* [リソースのリソース ログを **Event Hubs** にストリーミングする](./resource-logs.md#send-to-azure-event-hubs)
* [Azure Monitor REST API を使用してリソース ログの診断設定を変更する](/rest/api/monitor/diagnosticsettings)
* [Log Analytics を使用した、Azure ストレージからのログの分析](./resource-logs.md#send-to-log-analytics-workspace)

