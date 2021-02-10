---
title: Azure Monitor リソース ログでサポートされているサービスとカテゴリ
description: Azure Monitor のリファレンス - Azure リソース ログでサポートされているサービスとイベント スキーマについて説明します。
ms.subservice: logs
ms.topic: reference
ms.date: 01/29/2021
ms.openlocfilehash: 02488e1a3ff26acf9ff318a2d5c09115aaba8df9
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070748"
---
# <a name="supported-categories-for-azure-resource-logs"></a>Azure リソース ログでサポートされているカテゴリ

> [!NOTE]
> リソース ログは、以前は診断ログと呼ばれていました。 この名前は、Azure Monitor によって収集されたログの種類が、Azure リソースだけでなくそれ以外のものも含むように移行したため、2019 年 10 月に変更されました。

[Azure Monitor リソース ログ](./platform-logs-overview.md)は、Azure サービスによって出力されるログであり、そのサービスやリソースの操作が記述されます。 Azure Monitor を通じて使用できるすべてのリソース ログには、共通の上位スキーマが共有されます。各サービスが、独自のイベントに固有のプロパティを出力するための柔軟性も備わっています。

(`resourceId` プロパティで使用可能な) リソースの種類と `category` を組み合わせて、スキーマを一意に識別します。 サービス固有のフィールドを含むすべてのリソース ログに共通のスキーマがあり、それぞれのログ カテゴリに対して追加されています。 詳細については、[Azure リソース ログの共通のスキーマとサービス固有のスキーマ]()に関するページを参照してください


## <a name="costs"></a>コスト

Log Analytics、Azure Storage、またはイベント ハブにデータを送信し、格納することでコストが発生します。 これらの場所にデータを送り、そこに保管するために、場合によってはコストを支払う必要があります。  リソース ログは、これらの場所に送信できるデータの一種です。 

これらの場所に一部のカテゴリのリソース ログをエクスポートするとコストが追加で発生します。 そのようなログとエクスポート コストを下の表に示します。 この価格の詳細については、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」ページの「プラットフォーム ログ」セクションを参照してください。

## <a name="supported-log-categories-per-resource-type"></a>リソースの種類ごとのサポートされているログ カテゴリ

各リソースの種類で使用可能なログの種類の一覧を次に示します。 

一部のカテゴリは、特定の種類のリソースに対してのみサポートされています。 リソースが不足していると思われる場合は、リソース固有のドキュメントを参照してください。 たとえば、Microsoft.Sql/servers/databases カテゴリは、すべての種類のデータベースで使用できるわけではありません。 詳細については、[SQL Database の診断ログに関する情報](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md)を参照してください。 

何かが不足していると考えられる場合は、この記事の下部にある GitHub コメントを開くことができます。
## <a name="microsoftaaddomainservices"></a>Microsoft.AAD/domainServices

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AccountLogon|AccountLogon|いいえ|
|AccountManagement|AccountManagement|いいえ|
|DetailTracking|DetailTracking|いいえ|
|DirectoryServiceAccess|DirectoryServiceAccess|いいえ|
|LogonLogoff|LogonLogoff|いいえ|
|ObjectAccess|ObjectAccess|いいえ|
|PolicyChange|PolicyChange|いいえ|
|PrivilegeUse|PrivilegeUse|いいえ|
|SystemSecurity|SystemSecurity|いいえ|


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|エンジン|エンジン|いいえ|
|サービス|サービス|いいえ|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|GatewayLogs|ApiManagement Gateway に関連するログ|いいえ|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|HttpRequest|HTTP 要求|Yes|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|ApplicationConsole|アプリケーション コンソール|いいえ|
|SystemLogs|システム ログ|いいえ|


## <a name="microsoftattestationattestationproviders"></a>Microsoft.Attestation/attestationProviders

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AuditEvent|AuditEvent メッセージ ログ カテゴリ。|いいえ|
|ERR|エラー メッセージ ログ カテゴリ。|いいえ|
|INF|情報メッセージ ログ カテゴリ。|いいえ|
|WRN|警告メッセージ ログ カテゴリ。|いいえ|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|DscNodeStatus|DSC ノードの状態|いいえ|
|JobLogs|ジョブ ログ|いいえ|
|JobStreams|ジョブ ストリーム|いいえ|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|ServiceLog|サービス ログ|いいえ|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|BaiClusterEvent|BaiClusterEvent|いいえ|
|BaiClusterNodeEvent|BaiClusterNodeEvent|いいえ|
|BaiJobEvent|BaiJobEvent|いいえ|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|BlockchainApplication|ブロックチェーン アプリケーション|いいえ|
|FabricOrderer|Fabric Orderer|いいえ|
|FabricPeer|Fabric Peer|いいえ|
|プロキシ|プロキシ|いいえ|


## <a name="microsoftblockchaincordamembers"></a>Microsoft.Blockchain/cordaMembers

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|BlockchainApplication|ブロックチェーン アプリケーション|いいえ|


## <a name="microsoftbotservicebotservices"></a>microsoft.botservice/botservices

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|BotRequest|チャネルからボットへの要求|いいえ|
|DependencyRequest|依存関係への要求|いいえ|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationfirewallpolicies

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|WebApplicationFirewallLogs|Web アプリケーション ファイアウォールのログ|いいえ|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/profiles

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AzureCdnAccessLog|Azure Cdn アクセス ログ|いいえ|
|FrontDoorAccessLog|FrontDoor アクセス ログ|Yes|
|FrontDoorHealthProbeLog|FrontDoor 正常性プローブ ログ|Yes|
|FrontDoorWebApplicationFirewallLog|FrontDoor WebApplicationFirewall ログ|Yes|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft.Cdn/profiles/endpoints

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|CoreAnalytics|エンドポイントのメトリック (帯域幅、エグレスなど) を取得します。|いいえ|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft.ClassicNetwork/networksecuritygroups

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|ネットワーク セキュリティ グループの規則フロー イベント|ネットワーク セキュリティ グループの規則フロー イベント|いいえ|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|Audit|[監査ログ]|いいえ|
|RequestResponse|要求と応答のログ|いいえ|
|Trace|トレース ログ|いいえ|


## <a name="microsoftcommunicationcommunicationservices"></a>Microsoft.Communication/CommunicationServices

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|ChatOperational|運用チャット ログ|いいえ|
|SMSOperational|運用 SMS ログ|いいえ|
|使用法|使用法レコード|いいえ|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|ContainerRegistryLoginEvents|ログイン イベント|いいえ|
|ContainerRegistryRepositoryEvents|RepositoryEvent ログ|いいえ|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|cluster-autoscaler|Kubernetes クラスター オートスケーラー|いいえ|
|guard|guard|いいえ|
|kube-apiserver|Kubernetes API サーバー|いいえ|
|kube-audit|Kubernetes の監査|いいえ|
|kube-audit-admin|Kubernetes 監査管理ログ|いいえ|
|kube-controller-manager|Kubernetes コントローラー マネージャー|いいえ|
|kube-scheduler|Kubernetes スケジューラ|いいえ|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/resourceproviders

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AuditLogs|MiniRP 呼び出しの監査ログ|いいえ|


## <a name="microsoftd365customerinsightsinstances"></a>Microsoft.D365CustomerInsights/instances

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|Audit|イベントを監査する|いいえ|
|運用時|操作イベント|いいえ|


## <a name="microsoftdatabricksworkspaces"></a>Microsoft.Databricks/workspaces

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|accounts|Databricks アカウント|いいえ|
|clusters|Databricks クラスター|いいえ|
|dbfs|Databricks ファイル システム|いいえ|
|instancePools|インスタンス プール|いいえ|
|jobs|Databricks ジョブ|いいえ|
|ノートブック|Databricks Notebook|いいえ|
|secrets|Databricks シークレット|いいえ|
|sqlPermissions|Databricks SQLPermissions|いいえ|
|ssh|Databricks SSH|いいえ|
|ワークスペース|Databricks ワークスペース|いいえ|


## <a name="microsoftdatacollaborationworkspaces"></a>Microsoft.DataCollaboration/workspaces

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|CollaborationAudit|コラボレーションの監査|Yes|
|DataAssets|データ資産|いいえ|
|パイプライン|パイプライン|いいえ|
|Proposals|Proposals|いいえ|
|スクリプト|スクリプト|いいえ|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|ActivityRuns|パイプライン アクティビティ実行ログ|いいえ|
|PipelineRuns|パイプライン実行ログ|いいえ|
|SSISIntegrationRuntimeLogs|SSIS 統合ランタイム ログ|いいえ|
|SSISPackageEventMessageContext|SSIS パッケージ イベント メッセージ コンテキスト|いいえ|
|SSISPackageEventMessages|SSIS パッケージ イベント メッセージ|いいえ|
|SSISPackageExecutableStatistics|SSIS パッケージ実行可能ファイル統計|いいえ|
|SSISPackageExecutionComponentPhases|SSIS パッケージ実行コンポーネント フェーズ|いいえ|
|SSISPackageExecutionDataStatistics|SSIS パッケージ実行データ統計|いいえ|
|TriggerRuns|トリガー実行ログ|いいえ|


## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|Audit|[監査ログ]|いいえ|
|Requests|要求ログ|いいえ|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|Audit|[監査ログ]|いいえ|
|Requests|要求ログ|いいえ|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|ReceivedShareSnapshots|受信された共有スナップショット|いいえ|
|SentShareSnapshots|送信された共有スナップショット|いいえ|
|共有|共有|いいえ|
|ShareSubscriptions|共有サブスクリプション|いいえ|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|MySqlAuditLogs|MariaDB 監査ログ|いいえ|
|MySqlSlowLogs|MariaDB サーバー ログ|いいえ|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft.DBforMySQL/flexibleServers

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|MySqlAuditLogs|MySQL 監査ログ|いいえ|
|MySqlSlowLogs|MySQL Slow ログ|いいえ|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|MySqlAuditLogs|MySQL 監査ログ|いいえ|
|MySqlSlowLogs|MySQL サーバーのログ|いいえ|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft.DBforPostgreSQL/flexibleServers

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|PostgreSQLLogs|PostgreSQL サーバー ログ|いいえ|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|PostgreSQLLogs|PostgreSQL サーバー ログ|いいえ|
|QueryStoreRuntimeStatistics|PostgreSQL クエリ ストアのランタイム統計|いいえ|
|QueryStoreWaitStatistics|PostgreSQL クエリ ストアの待機統計|いいえ|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|PostgreSQLLogs|PostgreSQL サーバー ログ|いいえ|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Microsoft.DesktopVirtualization/applicationgroups

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|Checkpoint|Checkpoint|いいえ|
|エラー|エラー|いいえ|
|管理|管理|いいえ|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Microsoft.DesktopVirtualization/hostpools

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AgentHealthStatus|AgentHealthStatus|いいえ|
|Checkpoint|Checkpoint|いいえ|
|Connection|Connection|いいえ|
|エラー|エラー|いいえ|
|HostRegistration|HostRegistration|いいえ|
|管理|管理|いいえ|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Microsoft.DesktopVirtualization/workspaces

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|Checkpoint|Checkpoint|いいえ|
|エラー|エラー|いいえ|
|フィード|フィード|いいえ|
|管理|管理|いいえ|


## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft.Devices/ElasticPools/IotHubTenants

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|C2DCommands|C2D コマンド|いいえ|
|C2DTwinOperations|C2D ツイン操作|いいえ|
|構成|構成|いいえ|
|接続|接続|いいえ|
|D2CTwinOperations|D2CTwinOperations|いいえ|
|DeviceIdentityOperations|デバイス ID の操作|いいえ|
|DeviceStreams|デバイス ストリーム (プレビュー)|いいえ|
|DeviceTelemetry|デバイス テレメトリ|いいえ|
|DirectMethods|ダイレクト メソッド|いいえ|
|DistributedTracing|分散トレース (プレビュー)|いいえ|
|FileUploadOperations|ファイルのアップロード操作|いいえ|
|JobsOperations|ジョブ操作|いいえ|
|ルート|ルート|いいえ|
|TwinQueries|ツイン クエリ|いいえ|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|C2DCommands|C2D コマンド|いいえ|
|C2DTwinOperations|C2D ツイン操作|いいえ|
|構成|構成|いいえ|
|接続|接続|いいえ|
|D2CTwinOperations|D2CTwinOperations|いいえ|
|DeviceIdentityOperations|デバイス ID の操作|いいえ|
|DeviceStreams|デバイス ストリーム (プレビュー)|いいえ|
|DeviceTelemetry|デバイス テレメトリ|いいえ|
|DirectMethods|ダイレクト メソッド|いいえ|
|DistributedTracing|分散トレース (プレビュー)|いいえ|
|FileUploadOperations|ファイルのアップロード操作|いいえ|
|JobsOperations|ジョブ操作|いいえ|
|ルート|ルート|いいえ|
|TwinQueries|ツイン クエリ|いいえ|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|DeviceOperations|デバイス操作|いいえ|
|ServiceOperations|サービス操作|いいえ|


## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft.DigitalTwins/digitalTwinsInstances

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|DigitalTwinsOperation|DigitalTwinsOperation|いいえ|
|EventRoutesOperation|EventRoutesOperation|いいえ|
|ModelsOperation|ModelsOperation|いいえ|
|QueryOperation|QueryOperation|いいえ|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|CassandraRequests|CassandraRequests|いいえ|
|ControlPlaneRequests|ControlPlaneRequests|いいえ|
|DataPlaneRequests|DataPlaneRequests|いいえ|
|GremlinRequests|GremlinRequests|いいえ|
|MongoRequests|MongoRequests|いいえ|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|いいえ|
|PartitionKeyStatistics|PartitionKeyStatistics|いいえ|
|QueryRuntimeStatistics|QueryRuntimeStatistics|いいえ|


## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|DeliveryFailures|配信エラーのログ|いいえ|
|PublishFailures|発行エラーのログ|いいえ|


## <a name="microsofteventgridpartnernamespaces"></a>Microsoft.EventGrid/partnerNamespaces

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|DeliveryFailures|配信エラーのログ|いいえ|
|PublishFailures|発行エラーのログ|いいえ|


## <a name="microsofteventgridpartnertopics"></a>Microsoft.EventGrid/partnerTopics

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|DeliveryFailures|配信エラーのログ|いいえ|


## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|DeliveryFailures|配信エラーのログ|いいえ|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|DeliveryFailures|配信エラーのログ|いいえ|
|PublishFailures|発行エラーのログ|いいえ|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|ArchiveLogs|アーカイブ ログ|いいえ|
|AutoScaleLogs|自動スケール ログ|いいえ|
|CustomerManagedKeyUserLogs|顧客管理キーのログ|いいえ|
|EventHubVNetConnectionEvent|VNet/IP フィルタリング接続ログ|いいえ|
|KafkaCoordinatorLogs|Kafka コーディネーター ログ|いいえ|
|KafkaUserErrorLogs|Kafka ユーザー エラー ログ|いいえ|
|OperationalLogs|操作ログ|いいえ|


## <a name="microsoftexperimentationexperimentworkspaces"></a>microsoft.experimentation/experimentWorkspaces

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|要求|要求|いいえ|


## <a name="microsofthealthcareapisservices"></a>Microsoft.HealthcareApis/services

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AuditLogs|監査ログ|いいえ|


## <a name="microsoftinsightsautoscalesettings"></a>microsoft.insights/autoscalesettings

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AutoscaleEvaluations|自動スケーリング検証|いいえ|
|AutoscaleScaleActions|自動スケーリングのスケーリング操作|いいえ|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AppAvailabilityResults|可用性の結果|いいえ|
|AppBrowserTimings|ブラウザーのタイミング|いいえ|
|AppDependencies|依存関係|いいえ|
|AppEvents|events|いいえ|
|AppExceptions|例外|いいえ|
|AppMetrics|メトリック|いいえ|
|AppPageViews|ページ ビュー|いいえ|
|AppPerformanceCounters|パフォーマンス カウンター|いいえ|
|AppRequests|Requests|いいえ|
|AppSystemEvents|システム イベント|いいえ|
|AppTraces|トレース|いいえ|


## <a name="microsoftiotspacesgraph"></a>Microsoft.IoTSpaces/Graph

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|Audit|Audit|いいえ|
|エグレス|エグレス|いいえ|
|イングレス|イングレス|いいえ|
|運用時|運用時|いいえ|
|Trace|Trace|いいえ|
|UserDefinedFunction|UserDefinedFunction|いいえ|


## <a name="microsoftkeyvaultmanagedhsms"></a>microsoft.keyvault/managedhsms

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AuditEvent|監査イベント|いいえ|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AuditEvent|[監査ログ]|いいえ|


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|コマンド|コマンド|いいえ|
|FailedIngestion|失敗した取り込み操作|いいえ|
|IngestionBatching|インジェスト バッチ処理|いいえ|
|クエリ|クエリ|いいえ|
|SucceededIngestion|成功した取り込み操作|いいえ|
|TableDetails|テーブル詳細|いいえ|
|TableUsageStatistics|テーブルの使用状況に関する統計情報|いいえ|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft.Logic/integrationAccounts

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|IntegrationAccountTrackingEvents|統合アカウント追跡イベント|いいえ|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|WorkflowRuntime|ワークフロー ランタイムの診断イベント|いいえ|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|いいえ|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|いいえ|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|いいえ|
|AmlComputeJobEvent|AmlComputeJobEvent|いいえ|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|いいえ|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|KeyDeliveryRequests|キー配信要求|いいえ|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|ApplicationGatewayAccessLog|アプリケーション ゲートウェイのアクセス ログ|いいえ|
|ApplicationGatewayFirewallLog|アプリケーション ゲートウェイのファイアウォール ログ|いいえ|
|ApplicationGatewayPerformanceLog|アプリケーション ゲートウェイのパフォーマンス ログ|いいえ|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AzureFirewallApplicationRule|Azure Firewall アプリケーション ルール|いいえ|
|AzureFirewallDnsProxy|Azure Firewall DNS プロキシ|いいえ|
|AzureFirewallNetworkRule|Azure Firewall ネットワーク ルール|いいえ|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft.Network/bastionHosts

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|BastionAuditLogs|Bastion 監査ログ|いいえ|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|PeeringRouteLog|ピアリングのルート テーブルのログ|いいえ|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|FrontdoorAccessLog|Frontdoor アクセス ログ|いいえ|
|FrontdoorWebApplicationFirewallLog|Frontdoor Web アプリケーション ファイアウォール ログ|いいえ|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|LoadBalancerAlertEvent|ロード バランサーのアラート イベント|いいえ|
|LoadBalancerProbeHealthStatus|ロード バランサーのプローブ正常性状態|いいえ|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft.Network/networksecuritygroups

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|NetworkSecurityGroupEvent|ネットワーク セキュリティ グループ イベント|いいえ|
|NetworkSecurityGroupFlowEvent|ネットワーク セキュリティ グループの規則フロー イベント|いいえ|
|NetworkSecurityGroupRuleCounter|ネットワーク セキュリティ グループの規則数|いいえ|


## <a name="microsoftnetworkp2svpngateways"></a>Microsoft.Network/p2sVpnGateways

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|GatewayDiagnosticLog|ゲートウェイ診断ログ|いいえ|
|IKEDiagnosticLog|IKE 診断ログ|いいえ|
|P2SDiagnosticLog|P2S 診断ログ|いいえ|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|DDoSMitigationFlowLogs|DDoS 軽減策に関する意思決定のフロー ログ|いいえ|
|DDoSMitigationReports|DDoS 軽減策のレポート|いいえ|
|DDoSProtectionNotifications|DDoS 保護通知|いいえ|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|ProbeHealthStatusEvents|Traffic Manager プローブの正常性結果イベント|いいえ|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|GatewayDiagnosticLog|ゲートウェイ診断ログ|いいえ|
|IKEDiagnosticLog|IKE 診断ログ|いいえ|
|P2SDiagnosticLog|P2S 診断ログ|いいえ|
|RouteDiagnosticLog|ルート診断ログ|いいえ|
|TunnelDiagnosticLog|トンネル診断ログ|いいえ|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|VMProtectionAlerts|VM 保護アラート|いいえ|


## <a name="microsoftnetworkvpngateways"></a>Microsoft.Network/vpnGateways

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|GatewayDiagnosticLog|ゲートウェイ診断ログ|いいえ|
|IKEDiagnosticLog|IKE 診断ログ|いいえ|
|RouteDiagnosticLog|ルート診断ログ|いいえ|
|TunnelDiagnosticLog|トンネル診断ログ|いいえ|


## <a name="microsoftnotificationhubsnamespaces"></a>Microsoft.NotificationHubs/namespaces

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|OperationalLogs|操作ログ|いいえ|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|Audit|[監査ログ]|いいえ|


## <a name="microsoftpowerbitenants"></a>Microsoft.PowerBI/tenants

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|エンジン|エンジン|いいえ|


## <a name="microsoftpowerbitenantsworkspaces"></a>Microsoft.PowerBI/tenants/workspaces

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|エンジン|エンジン|いいえ|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|エンジン|エンジン|いいえ|


## <a name="microsoftprojectbabylonaccounts"></a>Microsoft.ProjectBabylon/accounts

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|ScanStatusLogEvent|ScanStatus|いいえ|


## <a name="microsoftpurviewaccounts"></a>microsoft.purview/accounts

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|ScanStatusLogEvent|ScanStatus|いいえ|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft.RecoveryServices/Vaults

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AddonAzureBackupAlerts|アドオン Azure Backup アラート データ|いいえ|
|AddonAzureBackupJobs|アドオン Azure Backup ジョブ データ|いいえ|
|AddonAzureBackupPolicy|アドオン Azure Backup ポリシー データ|いいえ|
|AddonAzureBackupProtectedInstance|アドオン Azure Backup 保護されたインスタンス データ|いいえ|
|AddonAzureBackupStorage|アドオン Azure Backup ストレージ データ|いいえ|
|AzureBackupReport|Azure Backup レポート データ|いいえ|
|AzureSiteRecoveryEvents|Azure Site Recovery イベント|いいえ|
|AzureSiteRecoveryJobs|Azure Site Recovery ジョブ|いいえ|
|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery で保護されたディスクのデータ変更頻度|いいえ|
|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery 回復ポイント|いいえ|
|AzureSiteRecoveryReplicatedItems|Azure Site Recovery レプリケートされた項目|いいえ|
|AzureSiteRecoveryReplicationDataUploadRate|Azure Site Recovery のレプリケーション データ アップロード速度|いいえ|
|AzureSiteRecoveryReplicationStats|Azure Site Recovery レプリケーション状態|いいえ|
|CoreAzureBackup|コア Azure Backup データ|いいえ|


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|HybridConnectionsEvent|HybridConnections イベント|いいえ|
|HybridConnectionsLogs|HybridConnectionsLogs|いいえ|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|OperationLogs|[操作ログ]|いいえ|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|OperationalLogs|操作ログ|いいえ|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AllLogs|Azure SignalR Service ログ。|いいえ|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|DevOpsOperationsAudit|Devops 操作の監査ログ|いいえ|
|ResourceUsageStats|リソース使用統計|いいえ|
|SQLSecurityAuditEvents|SQL セキュリティ監査イベント|いいえ|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft.Sql/managedInstances/databases

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|エラー|エラー|いいえ|
|QueryStoreRuntimeStatistics|クエリ ストアのランタイム統計|いいえ|
|QueryStoreWaitStatistics|クエリ ストアの待機統計|いいえ|
|SQLInsights|SQL Insights|いいえ|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AutomaticTuning|自動チューニング|いいえ|
|Blocks|Blocks|いいえ|
|DatabaseWaitStatistics|データベースの待機統計|いいえ|
|デッドロック|デッドロック|いいえ|
|DevOpsOperationsAudit|Devops 操作の監査ログ|いいえ|
|DmsWorkers|DMS worker|いいえ|
|エラー|エラー|いいえ|
|ExecRequests|実行要求|いいえ|
|QueryStoreRuntimeStatistics|クエリ ストアのランタイム統計|いいえ|
|QueryStoreWaitStatistics|クエリ ストアの待機統計|いいえ|
|RequestSteps|要求ステップ|いいえ|
|SQLInsights|SQL Insights|いいえ|
|SqlRequests|SQL 要求|いいえ|
|SQLSecurityAuditEvents|SQL セキュリティ監査イベント|いいえ|
|Timeouts|Timeouts|いいえ|
|待機|待機|いいえ|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|Authoring|Authoring|いいえ|
|実行|実行|いいえ|


## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse ワークスペース

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|BuiltinSqlReqsEnded|組み込みの SQL プール要求終了|いいえ|
|GatewayApiRequests|Synapse Gateway API 要求|いいえ|
|SQLSecurityAuditEvents|SQL セキュリティ監査イベント|いいえ|
|SynapseRbacOperations|Synapse RBAC 操作|いいえ|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft.Synapse/workspaces/bigDataPools

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|BigDataPoolAppsEnded|ビッグ データ プール アプリケーション終了|いいえ|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft.Synapse/workspaces/sqlPools

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|DmsWorkers|DMS worker|いいえ|
|ExecRequests|実行要求|いいえ|
|RequestSteps|要求ステップ|いいえ|
|SqlRequests|SQL 要求|いいえ|
|SQLSecurityAuditEvents|SQL セキュリティ監査イベント|いいえ|
|待機|待機|いいえ|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|イングレス|イングレス|いいえ|
|管理|管理|いいえ|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|イングレス|イングレス|いいえ|
|管理|管理|いいえ|


## <a name="microsoftwebhostingenvironments"></a>microsoft.web/hostingenvironments

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AppServiceEnvironmentPlatformLogs|Azure App Service Environment のプラットフォーム ログ|いいえ|


## <a name="microsoftwebsites"></a>microsoft.web/sites

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|ウイルス対策監査ログのレポート|いいえ|
|AppServiceAppLogs|App Service アプリケーション ログ|いいえ|
|AppServiceAuditLogs|監査ログへのアクセス|いいえ|
|AppServiceConsoleLogs|App Service コンソール ログ|いいえ|
|AppServiceFileAuditLogs|サイト コンテンツの変更に関する監査ログ|いいえ|
|AppServiceHTTPLogs|HTTP ログ|いいえ|
|AppServiceIPSecAuditLogs|IPSecurity 監査ログ|いいえ|
|AppServicePlatformLogs|App Service プラットフォーム ログ|いいえ|
|FunctionAppLogs|関数アプリケーション ログ|いいえ|


## <a name="microsoftwebsitesslots"></a>microsoft.web/sites/slots

|カテゴリ|カテゴリの表示名|エクスポートのコスト|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|ウイルス対策監査ログのレポート|いいえ|
|AppServiceAppLogs|App Service アプリケーション ログ|いいえ|
|AppServiceAuditLogs|監査ログへのアクセス|いいえ|
|AppServiceConsoleLogs|App Service コンソール ログ|いいえ|
|AppServiceFileAuditLogs|サイト コンテンツの変更に関する監査ログ|いいえ|
|AppServiceHTTPLogs|HTTP ログ|いいえ|
|AppServiceIPSecAuditLogs|IPSecurity 監査ログ|いいえ|
|AppServicePlatformLogs|App Service プラットフォーム ログ|いいえ|
|FunctionAppLogs|関数アプリケーション ログ|いいえ|



## <a name="next-steps"></a>次の手順

* [リソース ログの詳細を確認する](./platform-logs-overview.md)
* [リソースのリソース ログを **Event Hubs** にストリーミングする](./resource-logs.md#send-to-azure-event-hubs)
* [Azure Monitor REST API を使用してリソース ログの診断設定を変更する](/rest/api/monitor/diagnosticsettings)
* [Log Analytics を使用した、Azure ストレージからのログの分析](./resource-logs.md#send-to-log-analytics-workspace)

