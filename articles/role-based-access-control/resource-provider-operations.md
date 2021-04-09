---
title: Azure リソース プロバイダーの操作
description: Azure リソース プロバイダーの操作を一覧表示します。
services: active-directory
ms.service: role-based-access-control
ms.topic: reference
ms.workload: identity
author: rolyon
ms.author: rolyon
ms.date: 02/25/2021
ms.custom: generated
ms.openlocfilehash: c91172ff110b950335c66f2c0ea9ac59c37e0a76
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2021
ms.locfileid: "102487853"
---
# <a name="azure-resource-provider-operations"></a>Azure リソース プロバイダーの操作

このセクションでは、組み込みロールで使用される、Azure リソース プロバイダーの操作を一覧表示します。 これらの操作を [Azure カスタム ロール](custom-roles.md)で使用することで、Azure のリソースに対する詳細なアクセス制御を実現できます。 リソース プロバイダー操作は常に進化しています。 最新の操作を取得するには、[Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) または [az provider operation list](/cli/azure/provider/operation#az-provider-operation-list) を使用してください。

操作の一覧を表示するには、次の表にあるリソース プロバイダーの名前をクリックします。

## <a name="all"></a>All

| 全般 |
| --- |
| [Microsoft.Addons](#microsoftaddons) |
| [Microsoft.Marketplace](#microsoftmarketplace) |
| [Microsoft.MarketplaceApps](#microsoftmarketplaceapps) |
| [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering) |
| [Microsoft.ResourceHealth](#microsoftresourcehealth) |
| [Microsoft.Support](#microsoftsupport) |
| **Compute** |
| [Microsoft.ClassicCompute](#microsoftclassiccompute) |
| [Microsoft.Compute](#microsoftcompute) |
| [Microsoft.ServiceFabric](#microsoftservicefabric) |
| **ネットワーク** |
| [Microsoft.Cdn](#microsoftcdn) |
| [Microsoft.ClassicNetwork](#microsoftclassicnetwork) |
| [Microsoft.Network](#microsoftnetwork) |
| **Storage** |
| [Microsoft.ClassicStorage](#microsoftclassicstorage) |
| [Microsoft.DataBox](#microsoftdatabox) |
| [Microsoft.DataShare](#microsoftdatashare) |
| [Microsoft.ImportExport](#microsoftimportexport) |
| [Microsoft.NetApp](#microsoftnetapp) |
| [Microsoft.Storage](#microsoftstorage) |
| [microsoft.storagesync](#microsoftstoragesync) |
| [Microsoft.StorSimple](#microsoftstorsimple) |
| **Web** |
| [Microsoft.AppPlatform](#microsoftappplatform) |
| [Microsoft.CertificateRegistration](#microsoftcertificateregistration) |
| [Microsoft.DomainRegistration](#microsoftdomainregistration) |
| [Microsoft.Maps](#microsoftmaps) |
| [Microsoft.Media](#microsoftmedia) |
| [Microsoft.Search](#microsoftsearch) |
| [Microsoft.SignalRService](#microsoftsignalrservice) |
| [microsoft.web](#microsoftweb) |
| **Containers** |
| [Microsoft.ContainerInstance](#microsoftcontainerinstance) |
| [Microsoft.ContainerRegistry](#microsoftcontainerregistry) |
| [Microsoft.ContainerService](#microsoftcontainerservice) |
| [Microsoft.DevSpaces](#microsoftdevspaces) |
| **データベース** |
| [Microsoft.Cache](#microsoftcache) |
| [Microsoft.DataFactory](#microsoftdatafactory) |
| [Microsoft.DataMigration](#microsoftdatamigration) |
| [Microsoft.DBforMariaDB](#microsoftdbformariadb) |
| [Microsoft.DBforMySQL](#microsoftdbformysql) |
| [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql) |
| [Microsoft.DocumentDB](#microsoftdocumentdb) |
| [Microsoft.Sql](#microsoftsql) |
| [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine) |
| **Analytics** |
| [Microsoft.AnalysisServices](#microsoftanalysisservices) |
| [Microsoft.Databricks](#microsoftdatabricks) |
| [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics) |
| [Microsoft.DataLakeStore](#microsoftdatalakestore) |
| [Microsoft.EventHub](#microsofteventhub) |
| [Microsoft.HDInsight](#microsofthdinsight) |
| [Microsoft.Kusto](#microsoftkusto) |
| [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated) |
| [Microsoft.Purview](#microsoftpurview) |
| [Microsoft.StreamAnalytics](#microsoftstreamanalytics) |
| [Microsoft.Synapse](#microsoftsynapse) |
| **ブロックチェーン** |
| [Microsoft.Blockchain](#microsoftblockchain) |
| **AI + 機械学習** |
| [Microsoft.BotService](#microsoftbotservice) |
| [Microsoft.CognitiveServices](#microsoftcognitiveservices) |
| [Microsoft.MachineLearning](#microsoftmachinelearning) |
| [Microsoft.MachineLearningServices](#microsoftmachinelearningservices) |
| **モノのインターネット** |
| [Microsoft.Devices](#microsoftdevices) |
| [Microsoft.IoTCentral](#microsoftiotcentral) |
| [Microsoft.NotificationHubs](#microsoftnotificationhubs) |
| [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights) |
| **複合現実** |
| [Microsoft.IoTSpaces](#microsoftiotspaces) |
| [Microsoft.MixedReality](#microsoftmixedreality) |
| **統合** |
| [Microsoft.ApiManagement](#microsoftapimanagement) |
| [Microsoft.AppConfiguration](#microsoftappconfiguration) |
| [Microsoft.AzureStack](#microsoftazurestack) |
| [Microsoft.DataBoxEdge](#microsoftdataboxedge) |
| [Microsoft.DataCatalog](#microsoftdatacatalog) |
| [Microsoft.EventGrid](#microsofteventgrid) |
| [Microsoft.Logic](#microsoftlogic) |
| [Microsoft.Relay](#microsoftrelay) |
| [Microsoft.ServiceBus](#microsoftservicebus) |
| **ID** |
| [Microsoft.AAD](#microsoftaad) |
| [microsoft.aadiam](#microsoftaadiam) |
| [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice) |
| [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory) |
| [Microsoft.ManagedIdentity](#microsoftmanagedidentity) |
| **Security** |
| [Microsoft.KeyVault](#microsoftkeyvault) |
| [Microsoft.Security](#microsoftsecurity) |
| [Microsoft.SecurityGraph](#microsoftsecuritygraph) |
| [Microsoft.SecurityInsights](#microsoftsecurityinsights) |
| **DevOps** |
| [Microsoft.DevTestLab](#microsoftdevtestlab) |
| [Microsoft.LabServices](#microsoftlabservices) |
| [Microsoft.VisualStudio](#microsoftvisualstudio) |
| **移行** |
| [Microsoft.Migrate](#microsoftmigrate) |
| [Microsoft.OffAzure](#microsoftoffazure) |
| **監視** |
| [Microsoft.AlertsManagement](#microsoftalertsmanagement) |
| [Microsoft.Insights](#microsoftinsights) |
| [Microsoft.OperationalInsights](#microsoftoperationalinsights) |
| [Microsoft.OperationsManagement](#microsoftoperationsmanagement) |
| [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor) |
| **管理 + ガバナンス** |
| [Microsoft.Advisor](#microsoftadvisor) |
| [Microsoft.Authorization](#microsoftauthorization) |
| [Microsoft.Automation](#microsoftautomation) |
| [Microsoft.Batch](#microsoftbatch) |
| [Microsoft.Billing](#microsoftbilling) |
| [Microsoft.Blueprint](#microsoftblueprint) |
| [Microsoft.Capacity](#microsoftcapacity) |
| [Microsoft.Commerce](#microsoftcommerce) |
| [Microsoft.Consumption](#microsoftconsumption) |
| [Microsoft.CostManagement](#microsoftcostmanagement) |
| [Microsoft.Features](#microsoftfeatures) |
| [Microsoft.GuestConfiguration](#microsoftguestconfiguration) |
| [Microsoft.HybridCompute](#microsofthybridcompute) |
| [Microsoft.Kubernetes](#microsoftkubernetes) |
| [Microsoft.ManagedServices](#microsoftmanagedservices) |
| [Microsoft.Management](#microsoftmanagement) |
| [Microsoft.PolicyInsights](#microsoftpolicyinsights) |
| [Microsoft.Portal](#microsoftportal) |
| [Microsoft.RecoveryServices](#microsoftrecoveryservices) |
| [Microsoft.Resources](#microsoftresources) |
| [Microsoft.Scheduler](#microsoftscheduler) |
| [Microsoft.Solutions](#microsoftsolutions) |
| [Microsoft.Subscription](#microsoftsubscription) |
| **Intune** |
| [Microsoft.Intune](#microsoftintune) |
| **その他** |
| [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization) |
| [Microsoft.DigitalTwins](#microsoftdigitaltwins) |
| [Microsoft.ServicesHub](#microsoftserviceshub) |


## <a name="general"></a>全般

### <a name="microsoftaddons"></a>Microsoft.Addons

Azure サービス: コア

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Addons/register/action | 指定したサブスクリプションを Microsoft.Addons に登録します。 |
> | Microsoft.Addons/operations/read | サポートされる RP 操作を取得します。 |
> | Microsoft.Addons/supportProviders/listsupportplaninfo/action | 指定したサブスクリプションの現在のサポート プラン情報の一覧を取得します。 |
> | Microsoft.Addons/supportProviders/supportPlanTypes/read | 指定された Canonical サポート プランの状態を取得します。 |
> | Microsoft.Addons/supportProviders/supportPlanTypes/write | 指定した Canonical サポート プランの種類を追加します。 |
> | Microsoft.Addons/supportProviders/supportPlanTypes/delete | 指定された Canonical サポート プランを削除します。 |

### <a name="microsoftmarketplace"></a>Microsoft.Marketplace

Azure サービス: コア

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Marketplace/register/action | Microsoft.Marketplace リソース プロバイダーをサブスクリプションに登録します。 |
> | Microsoft.Marketplace/privateStores/action | PrivateStore を更新します。 |
> | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | 契約を返します。 |
> | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | 署名済みのライセンス契約を受け入れます。 |
> | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | 構成を返します。 |
> | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | 構成を保存します。 |
> | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | イメージをエンド ユーザーの ACR にインポートします。 |
> | Microsoft.Marketplace/privateStores/write | PrivateStore を作成します。 |
> | Microsoft.Marketplace/privateStores/delete | PrivateStore を削除します。 |
> | Microsoft.Marketplace/privateStores/offers/action | PrivateStore でオファーを更新します。 |
> | Microsoft.Marketplace/privateStores/read | PrivateStores を読み取ります。 |
> | Microsoft.Marketplace/privateStores/requestApprovals/action | 要求の承認を更新します |
> | Microsoft.Marketplace/privateStores/adminRequestApprovals/read | すべての要求承認の詳細を読み取ります (管理者のみ) |
> | Microsoft.Marketplace/privateStores/adminRequestApprovals/write | 要求に関する決定によって管理者が要求を更新します |
> | Microsoft.Marketplace/privateStores/offers/write | PrivateStore でオファーを作成します。 |
> | Microsoft.Marketplace/privateStores/offers/delete | PrivateStore からオファーを削除します。 |
> | Microsoft.Marketplace/privateStores/offers/read | PrivateStore プランを読み取ります。 |
> | Microsoft.Marketplace/privateStores/queryNotificationsState/read | 通知状態の詳細を読み取ります (管理者のみ) |
> | Microsoft.Marketplace/privateStores/requestApprovals/read | 要求承認を読み取ります |
> | Microsoft.Marketplace/privateStores/requestApprovals/write | 要求の承認の作成 |
> | Microsoft.Marketplace/privateStores/RequestApprovals/offer/acknowledgeNotification/write | 通知を確認します (管理者のみ) |
> | Microsoft.Marketplace/privateStores/RequestApprovals/withdrawPlan/write | オファーの通知からプランを取り消します |

### <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

Azure サービス: コア

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.MarketplaceApps/ClassicDevServices/read | クラシック開発サービスに対して GET 操作を行います。 |
> | Microsoft.MarketplaceApps/ClassicDevServices/delete | クラシック開発サービス リソースに対して DELETE 操作を行います。 |
> | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | クラシック開発サービスのシングル サインオン URL を取得します。 |
> | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | クラシック開発サービス リソースの管理キーを取得します。 |
> | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | クラシック開発サービス リソースの管理キーを生成します。 |
> | Microsoft.MarketplaceApps/Operations/read | すべてのリソースの種類の操作を読み取ります。 |

### <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

Azure サービス: コア

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.MarketplaceOrdering/agreements/read | 指定されたサブスクリプションのすべての契約を返します。 |
> | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | 指定された Marketplace の項目の契約を返します。 |
> | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | 指定された Marketplace の項目の契約に署名します。 |
> | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | 指定された Marketplace の項目の契約を取り消します。 |
> | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | 指定された Marketplace 仮想マシン項目の契約を取得します。 |
> | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | 指定された Marketplace 仮想マシン項目の契約を署名またはキャンセルします。 |
> | Microsoft.MarketplaceOrdering/operations/read | API のすべての可能な操作を一覧表示します |

### <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

Azure サービス:[Azure Service Health](../service-health/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.ResourceHealth/register/action | Microsoft ResourceHealth にサブスクリプションを登録します。 |
> | Microsoft.ResourceHealth/unregister/action | Microsoft ResourceHealth のサブスクリプションを登録解除します |
> | Microsoft.Resourcehealth/healthevent/action | 指定されたリソースの正常性の状態の変更を表します。 |
> | Microsoft.ResourceHealth/AvailabilityStatuses/read | 指定されたスコープのすべてのリソースの利用状況を取得します。 |
> | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | 指定されたリソースの利用状況を取得します。 |
> | Microsoft.ResourceHealth/emergingissues/read | Azure サービスの新しい問題を取得します |
> | Microsoft.ResourceHealth/events/read | 指定されたサブスクリプションについてサービスの正常性イベントを取得します。 |
> | Microsoft.Resourcehealth/healthevent/Activated/action | 指定されたリソースの正常性の状態の変更を表します。 |
> | Microsoft.Resourcehealth/healthevent/Updated/action | 指定されたリソースの正常性の状態の変更を表します。 |
> | Microsoft.Resourcehealth/healthevent/Resolved/action | 指定されたリソースの正常性の状態の変更を表します。 |
> | Microsoft.Resourcehealth/healthevent/InProgress/action | 指定されたリソースの正常性の状態の変更を表します。 |
> | Microsoft.Resourcehealth/healthevent/Pending/action | 指定されたリソースの正常性の状態の変更を表します。 |
> | Microsoft.ResourceHealth/impactedResources/read | 指定されたサブスクリプションについて影響を受けるリソースを取得します。 |
> | Microsoft.ResourceHealth/metadata/read | メタデータを取得します |
> | Microsoft.ResourceHealth/Notifications/read | Azure Resource Manager 通知を受信します |
> | Microsoft.ResourceHealth/Operations/read | Microsoft ResourceHealth で利用できる操作を取得します |

### <a name="microsoftsupport"></a>Microsoft.Support

Azure サービス: コア

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Support/register/action | サポート リソース プロバイダーを登録します |
> | Microsoft.Support/checkNameAvailability/action | 名前が有効であり、リソースの種類で使用されていないことを確認します |
> | Microsoft.Support/operationresults/read | 非同期操作の結果を取得します |
> | Microsoft.Support/operations/read | Microsoft.Support リソース プロバイダーで使用できるすべての操作を一覧表示します |
> | Microsoft.Support/operationsstatus/read | 非同期操作の状態を取得します |
> | Microsoft.Support/services/read | サポートで使用可能なすべての Azure サービスを一覧表示します |
> | Microsoft.Support/services/problemClassifications/read | Azure サービスの 1 つまたはすべての問題分類を一覧表示します |
> | Microsoft.Support/supportTickets/read | 1 つまたはすべてのサポート チケットを一覧表示します |
> | Microsoft.Support/supportTickets/write | サポート チケットの作成と更新を許可します |
> | Microsoft.Support/supportTickets/communications/read | 1 つまたはすべてのサポート チケット コミュニケーションを一覧表示します |
> | Microsoft.Support/supportTickets/communications/write | サポート チケットに新しいコミュニケーションを追加します |

## <a name="compute"></a>Compute

### <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

Azure サービス:クラシック デプロイ モデルの仮想マシン

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.ClassicCompute/register/action | 従来のコンピューティングに登録します。 |
> | Microsoft.ClassicCompute/checkDomainNameAvailability/action | 指定されたドメイン名を使用できるかどうかを確認します。 |
> | Microsoft.ClassicCompute/moveSubscriptionResources/action | すべてのクラシック リソースを別のサブスクリプションに移動します。 |
> | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | 従来の移動操作でサブスクリプションが利用可能かどうかを検証します。 |
> | Microsoft.ClassicCompute/capabilities/read | 機能を表示します。 |
> | Microsoft.ClassicCompute/checkDomainNameAvailability/read | 特定のドメイン名の使用状況を取得します。 |
> | Microsoft.ClassicCompute/domainNames/read | リソースのドメイン名を返します。 |
> | Microsoft.ClassicCompute/domainNames/write | リソースのドメイン名を追加または変更します。 |
> | Microsoft.ClassicCompute/domainNames/delete | リソースのドメイン名を削除します。 |
> | Microsoft.ClassicCompute/domainNames/swap/action | ステージング スロットを運用スロットに切り替えます。 |
> | Microsoft.ClassicCompute/domainNames/active/write | アクティブなドメイン名を設定します。 |
> | Microsoft.ClassicCompute/domainNames/availabilitySets/read | リソースの可用性セットを表示します。 |
> | Microsoft.ClassicCompute/domainNames/capabilities/read | ドメイン名機能を表示します。 |
> | Microsoft.ClassicCompute/domainNames/deploymentslots/read | デプロイ スロットを表示します。 |
> | Microsoft.ClassicCompute/domainNames/deploymentslots/write | デプロイを作成または更新します。 |
> | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/read | ドメイン名のデプロイ スロットでロールを取得します |
> | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/roleinstances/read | ドメイン名のデプロイ スロットでロールのロール インスタンスを取得します |
> | Microsoft.ClassicCompute/domainNames/deploymentslots/state/read | デプロイ スロットの状態を取得します。 |
> | Microsoft.ClassicCompute/domainNames/deploymentslots/state/write | デプロイ スロットの状態を追加します。 |
> | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/read | ドメイン名でデプロイ スロットのアップグレード ドメインを取得します |
> | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/write | ドメイン名でデプロイ スロットのアップグレード ドメインを更新します |
> | Microsoft.ClassicCompute/domainNames/extensions/read | ドメイン名拡張子を返します。 |
> | Microsoft.ClassicCompute/domainNames/extensions/write | ドメイン名拡張子を追加します。 |
> | Microsoft.ClassicCompute/domainNames/extensions/delete | ドメイン名拡張子を削除します。 |
> | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | ドメイン名拡張子の操作の状態を読み取ります。 |
> | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | 内部ロード バランサーを取得します。 |
> | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | 新しい内部負荷分散を作成します。 |
> | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | 新しい内部負荷分散を削除します。 |
> | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | ドメイン名の内部ロード バランサーの操作の状態を読み取ります。 |
> | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | 負荷分散エンドポイント セットを取得します。 |
> | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/write | 負荷分散エンドポイント セットを追加します。 |
> | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | ドメイン名の負荷分散エンドポイント セットの操作の状態を読み取ります。 |
> | Microsoft.ClassicCompute/domainNames/operationstatuses/read | ドメイン名の操作状態を取得します。 |
> | Microsoft.ClassicCompute/domainNames/operationStatuses/read | ドメイン名拡張子の操作の状態を読み取ります。 |
> | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | 使用するサービス証明書を返します。 |
> | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | 使用するサービス証明書を追加または変更します。 |
> | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | 使用するサービス証明書を削除します。 |
> | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | ドメイン名のサービス証明書の操作の状態を読み取ります。 |
> | Microsoft.ClassicCompute/domainNames/slots/read | デプロイ スロットを表示します。 |
> | Microsoft.ClassicCompute/domainNames/slots/write | デプロイを作成または更新します。 |
> | Microsoft.ClassicCompute/domainNames/slots/delete | 指定されたデプロイ スロットを削除します。 |
> | Microsoft.ClassicCompute/domainNames/slots/start/action | デプロイ スロットを開始します。 |
> | Microsoft.ClassicCompute/domainNames/slots/stop/action | デプロイ スロットを中断します。 |
> | Microsoft.ClassicCompute/domainNames/slots/validateMigration/action | デプロイ スロットの移行を検証します。 |
> | Microsoft.ClassicCompute/domainNames/slots/prepareMigration/action | デプロイ スロットの移行を準備します。 |
> | Microsoft.ClassicCompute/domainNames/slots/commitMigration/action | デプロイ スロットの移行をコミットします。 |
> | Microsoft.ClassicCompute/domainNames/slots/abortMigration/action | デプロイ スロットの移行を中止します。 |
> | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | ドメイン名スロットの操作の状態を読み取ります。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/read | デプロイ スロットのロールを取得します。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/write | デプロイ スロットのロールを追加します。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | デプロイ スロット ロールの拡張子参照を返します。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | デプロイ スロット ロールの拡張子参照を追加または変更します。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | デプロイ スロット ロールの拡張子参照を削除します。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | ドメイン名スロット ロールの拡張子参照の操作の状態を読み取ります。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/metricdefinitions/read | ドメイン名のロール メトリックの定義を取得します。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/metrics/read | ドメイン名のロール メトリックを取得します。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/operationstatuses/read | ドメイン名のスロット ロールの操作状態を取得します。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read | 診断設定を取得します。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write | 診断設定を追加または変更します。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | メトリック定義を取得します。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/downloadremotedesktopconnectionfile/action | ドメイン名のスロット ロールのロール インスタンスのリモート デスクトップ接続ファイルをダウンロードします。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | ロール インスタンスを取得します。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | ロール インスタンスを再起動します。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | ロール インスタンスを再イメージ化します。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | ロール インスタンスを再構築します。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | ドメイン名のスロット ロールのロール インスタンスの操作状態を取得します。 |
> | Microsoft.ClassicCompute/domainNames/slots/roles/skus/read | デプロイ スロットのロールの SKU を取得します。 |
> | Microsoft.ClassicCompute/domainNames/slots/state/start/write | デプロイ スロットを停止状態に変更します。 |
> | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | デプロイ スロットを開始状態に変更します。 |
> | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | ドメインのウォーク アップグレードを行います。 |
> | Microsoft.ClassicCompute/operatingSystemFamilies/read | Microsoft Azure で利用できるゲスト オペレーティング システムのファミリと、それぞれのファミリで利用できるオペレーティング システムのバージョンを一覧表示します。 |
> | Microsoft.ClassicCompute/operatingSystems/read | Microsoft Azure で現在利用できるゲスト オペレーティング システムのバージョンを一覧表示します。 |
> | Microsoft.ClassicCompute/operations/read | 操作の一覧を取得します。 |
> | Microsoft.ClassicCompute/operationStatuses/read | リソースの操作の状態を読み取ります。 |
> | Microsoft.ClassicCompute/quotas/read | サブスクリプションのクォータを取得します。 |
> | Microsoft.ClassicCompute/resourceTypes/skus/read | サポートされているリソースの種類の SKU の一覧を取得します。 |
> | Microsoft.ClassicCompute/virtualMachines/read | 仮想マシンの一覧を取得します。 |
> | Microsoft.ClassicCompute/virtualMachines/write | 仮想マシンを追加または変更します。 |
> | Microsoft.ClassicCompute/virtualMachines/delete | 仮想マシンを削除します。 |
> | Microsoft.ClassicCompute/virtualMachines/capture/action | 仮想マシンをキャプチャします。 |
> | Microsoft.ClassicCompute/virtualMachines/start/action | 仮想マシンを開始します。 |
> | Microsoft.ClassicCompute/virtualMachines/redeploy/action | 仮想マシンを再デプロイします。 |
> | Microsoft.ClassicCompute/virtualMachines/performMaintenance/action | 仮想マシンに対してメンテナンスを実行します。 |
> | Microsoft.ClassicCompute/virtualMachines/restart/action | 仮想マシンを再起動します。 |
> | Microsoft.ClassicCompute/virtualMachines/stop/action | 仮想マシンを停止します。 |
> | Microsoft.ClassicCompute/virtualMachines/shutdown/action | 仮想マシンをシャットダウンします。 |
> | Microsoft.ClassicCompute/virtualMachines/attachDisk/action | 仮想マシンにデータ ディスクを接続します。 |
> | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | 仮想マシンからデータ ディスクを切断します。 |
> | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | 仮想マシンの RDP ファイルをダウンロードします。 |
> | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | 仮想マシンに関連付けられたネットワーク セキュリティ グループを取得します。 |
> | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | 仮想マシンに関連付けられたネットワーク セキュリティ グループを追加します。 |
> | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | 仮想マシンに関連付けられたネットワーク セキュリティ グループを削除します。 |
> | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | 仮想マシンに関連付けられたネットワーク セキュリティ グループの操作の状態を読み取ります。 |
> | Microsoft.ClassicCompute/virtualMachines/asyncOperations/read | 可能な非同期操作を取得します。 |
> | Microsoft.ClassicCompute/virtualMachines/diagnosticsettings/read | 仮想マシンの診断設定を取得します。 |
> | Microsoft.ClassicCompute/virtualMachines/disks/read | データ ディスクの一覧を取得します。 |
> | Microsoft.ClassicCompute/virtualMachines/extensions/read | 仮想マシン拡張機能を取得します。 |
> | Microsoft.ClassicCompute/virtualMachines/extensions/write | 仮想マシン拡張機能を追加します。 |
> | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | 仮想マシン拡張機能の操作の状態を読み取ります。 |
> | Microsoft.ClassicCompute/virtualMachines/metricdefinitions/read | 仮想マシンのメトリック定義を取得します。 |
> | Microsoft.ClassicCompute/virtualMachines/metrics/read | メトリックを取得します。 |
> | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | ネットワーク インターフェイスに関連付けられたネットワーク セキュリティ グループを取得します。 |
> | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | ネットワーク インターフェイスに関連付けられたネットワーク セキュリティ グループを追加します。 |
> | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | ネットワーク インターフェイスに関連付けられたネットワーク セキュリティ グループを削除します。 |
> | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | 仮想マシンに関連付けられたネットワーク セキュリティ グループの操作の状態を読み取ります。 |
> | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | 仮想マシンの操作の状態を読み取ります。 |
> | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | 診断設定を取得します。 |
> | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write | 診断設定を追加または変更します。 |
> | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | メトリック定義を取得します。 |

### <a name="microsoftcompute"></a>Microsoft.Compute

Azure サービス:[仮想マシン](../virtual-machines/index.yml)、 [仮想マシン スケール セット](../virtual-machine-scale-sets/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Compute/register/action | Microsoft.Compute リソース プロバイダーにサブスクリプションを登録します。 |
> | Microsoft.Compute/unregister/action | Microsoft.Compute リソース プロバイダーとのサブスクリプションの登録を解除します |
> | Microsoft.Compute/availabilitySets/read | 可用性セットのプロパティを取得します。 |
> | Microsoft.Compute/availabilitySets/write | 新しい可用性セットを作成するか、既存の可用性セットを更新します。 |
> | Microsoft.Compute/availabilitySets/delete | 可用性セットを削除します。 |
> | Microsoft.Compute/availabilitySets/vmSizes/read | 可用性セットの仮想マシンを作成または更新する際に使用できるサイズを一覧表示します。 |
> | Microsoft.Compute/capacityReservationGroups/read | 容量予約グループのプロパティを取得します |
> | Microsoft.Compute/capacityReservationGroups/write | 新しい容量予約グループを作成するか、既存の容量予約グループを更新します |
> | Microsoft.Compute/capacityReservationGroups/delete | 容量予約グループを削除します |
> | Microsoft.Compute/capacityReservationGroups/capacityReservations/read | 容量予約のプロパティを取得します |
> | Microsoft.Compute/capacityReservationGroups/capacityReservations/write | 新しい容量予約を作成するか、既存の容量予約を更新します |
> | Microsoft.Compute/capacityReservationGroups/capacityReservations/delete | 容量予約を削除します |
> | Microsoft.Compute/cloudServices/read | CloudService のプロパティを取得します。 |
> | Microsoft.Compute/cloudServices/write | 新しい CloudService を作成するか、既存のものを更新します。 |
> | Microsoft.Compute/cloudServices/delete | CloudService を削除します。 |
> | Microsoft.Compute/cloudServices/poweroff/action | CloudService の電源をオフにします。 |
> | Microsoft.Compute/cloudServices/start/action | CloudService を開始します。 |
> | Microsoft.Compute/cloudServices/restart/action | CloudService 内の 1 つ以上のロール インスタンスを再起動します。 |
> | Microsoft.Compute/cloudServices/reimage/action | CloudService 内のロール インスタンスのすべてのディスクを再構築します。 |
> | Microsoft.Compute/cloudServices/rebuild/action | CloudService 内のすべてのロール インスタンスを再イメージ化します。 |
> | Microsoft.Compute/cloudServices/delete/action | CloudService のロール インスタンスを削除します。 |
> | Microsoft.Compute/cloudServices/instanceView/read | CloudService の状態を取得します。 |
> | Microsoft.Compute/cloudServices/roleInstances/delete | CloudService から RoleInstance を削除します。 |
> | Microsoft.Compute/cloudServices/roleInstances/read | CloudService から RoleInstance を取得します。 |
> | Microsoft.Compute/cloudServices/roleInstances/restart/action | CloudService のロール インスタンスを再起動します |
> | Microsoft.Compute/cloudServices/roleInstances/reimage/action | CloudService のロール インスタンスを再イメージ化します。 |
> | Microsoft.Compute/cloudServices/roleInstances/rebuild/action | CloudService 内のすべてのディスクを再構築します。 |
> | Microsoft.Compute/cloudServices/roleInstances/instanceView/read | CloudService からロール インスタンスの状態を取得します。 |
> | Microsoft.Compute/cloudServices/roles/read | CloudService からロールを取得します。 |
> | Microsoft.Compute/cloudServices/roles/write | ロール内のインスタンスをスケーリングします |
> | Microsoft.Compute/cloudServices/updateDomains/read | CloudService 内のすべての更新ドメインの一覧を取得します。 |
> | Microsoft.Compute/diskAccesses/read | DiskAccess リソースのプロパティを取得します |
> | Microsoft.Compute/diskAccesses/write | 新しい DiskAccess リソースを作成するか、既存のものを更新します |
> | Microsoft.Compute/diskAccesses/delete | DiskAccess リソースを削除します |
> | Microsoft.Compute/diskAccesses/privateEndpointConnectionsApproval/action | プライベート エンドポイント接続を自動的に承認します |
> | Microsoft.Compute/diskAccesses/privateEndpointConnectionProxies/read | プライベート エンドポイント接続プロキシのプロパティを取得します |
> | Microsoft.Compute/diskAccesses/privateEndpointConnectionProxies/write | 新しいプライベート エンドポイント接続プロキシを作成します |
> | Microsoft.Compute/diskAccesses/privateEndpointConnectionProxies/delete | プライベート エンドポイント接続プロキシを削除します |
> | Microsoft.Compute/diskAccesses/privateEndpointConnectionProxies/validate/action | プライベート エンドポイント接続プロキシ オブジェクトを検証します |
> | Microsoft.Compute/diskAccesses/privateEndpointConnections/delete | プライベート エンドポイント接続を削除します |
> | Microsoft.Compute/diskAccesses/privateEndpointConnections/read | プライベート エンドポイント接続を取得します |
> | Microsoft.Compute/diskAccesses/privateEndpointConnections/write | プライベート エンドポイント接続を承認または拒否します |
> | Microsoft.Compute/diskEncryptionSets/read | ディスク暗号化セットのプロパティを取得します |
> | Microsoft.Compute/diskEncryptionSets/write | 新しいディスク暗号化セットを作成するか、既存のディスク暗号化セットを更新します |
> | Microsoft.Compute/diskEncryptionSets/delete | ディスク暗号化セットを削除します |
> | Microsoft.Compute/disks/read | ディスクのプロパティを取得します。 |
> | Microsoft.Compute/disks/write | 新しいディスクを作成するか、既存のディスクを更新します。 |
> | Microsoft.Compute/disks/delete | ディスクを削除します。 |
> | Microsoft.Compute/disks/beginGetAccess/action | BLOB へのアクセス用にディスクの SAS URI を取得します。 |
> | Microsoft.Compute/disks/endGetAccess/action | ディスクの SAS URI を取り消します。 |
> | Microsoft.Compute/galleries/read | ギャラリーのプロパティを取得します |
> | Microsoft.Compute/galleries/write | 新しいギャラリーを作成するか、既存のギャラリーを更新します |
> | Microsoft.Compute/galleries/delete | ギャラリーを削除します |
> | Microsoft.Compute/galleries/share/action | ギャラリーを別のスコープに共有します |
> | Microsoft.Compute/galleries/applications/read | ギャラリー アプリケーションのプロパティを取得します。 |
> | Microsoft.Compute/galleries/applications/write | 新しいギャラリー アプリケーションを作成するか、既存のギャラリー アプリケーションを更新します。 |
> | Microsoft.Compute/galleries/applications/delete | ギャラリー アプリケーションを削除します。 |
> | Microsoft.Compute/galleries/applications/versions/read | ギャラリー アプリケーションのバージョンのプロパティを取得します。 |
> | Microsoft.Compute/galleries/applications/versions/write | 新しいギャラリー アプリケーションのバージョンを作成するか、既存のギャラリー アプリケーションのバージョンを更新します。 |
> | Microsoft.Compute/galleries/applications/versions/delete | ギャラリー アプリケーションのバージョンを削除します。 |
> | Microsoft.Compute/galleries/images/read | ギャラリー イメージのプロパティを取得します |
> | Microsoft.Compute/galleries/images/write | 新しいギャラリー イメージを作成するか、既存のギャラリー イメージを更新します |
> | Microsoft.Compute/galleries/images/delete | ギャラリー イメージを削除します |
> | Microsoft.Compute/galleries/images/versions/read | ギャラリー イメージ バージョンのプロパティを取得します |
> | Microsoft.Compute/galleries/images/versions/write | 新しいギャラリー イメージ バージョンを作成するか、既存のギャラリー イメージ バージョンを更新します |
> | Microsoft.Compute/galleries/images/versions/delete | ギャラリー イメージ バージョンを削除します |
> | Microsoft.Compute/hostGroups/read | ホスト グループのプロパティを取得します |
> | Microsoft.Compute/hostGroups/write | 新しいホスト グループを作成するか、既存のホスト グループを更新します |
> | Microsoft.Compute/hostGroups/delete | ホスト グループを削除します |
> | Microsoft.Compute/hostGroups/hosts/read | ホストのプロパティを取得します |
> | Microsoft.Compute/hostGroups/hosts/write | 新しいホストを作成するか、既存のホストを更新します |
> | Microsoft.Compute/hostGroups/hosts/delete | ホストを削除します |
> | Microsoft.Compute/images/read | イメージのプロパティを取得します。 |
> | Microsoft.Compute/images/write | 新しいイメージを作成するか、既存のイメージを更新します。 |
> | Microsoft.Compute/images/delete | イメージを削除します。 |
> | Microsoft.Compute/locations/capsOperations/read | 非同期のキャップ操作の状態を取得します。 |
> | Microsoft.Compute/locations/diskOperations/read | 非同期のディスク操作の状態を取得します。 |
> | Microsoft.Compute/locations/logAnalytics/getRequestRateByInterval/action | 調整の診断を支援するために、時間間隔で要求の総数を表示するログを作成します。 |
> | Microsoft.Compute/locations/logAnalytics/getThrottledRequests/action | ResourceName、OperationName、または適用されたスロットル ポリシーでグループ化された、調整された要求の集計を表示するログを作成します。 |
> | Microsoft.Compute/locations/operations/read | 非同期操作の状態を取得します。 |
> | Microsoft.Compute/locations/privateEndpointConnectionProxyAzureAsyncOperation/read | 非同期のプライベート エンドポイント接続プロキシ操作の状態を取得します |
> | Microsoft.Compute/locations/privateEndpointConnectionProxyOperationResults/read | プライベート エンドポイント接続プロキシ操作の結果を取得します |
> | Microsoft.Compute/locations/publishers/read | パブリッシャーのプロパティを取得します。 |
> | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | プラットフォーム イメージ プランのプロパティを取得します。 |
> | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | プラットフォーム イメージ SKU のプロパティを取得します。 |
> | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | プラットフォーム イメージ バージョンのプロパティを取得します。 |
> | Microsoft.Compute/locations/publishers/artifacttypes/types/read | VMExtension 型のプロパティを取得します。 |
> | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | VMExtension バージョンのプロパティを取得します。 |
> | Microsoft.Compute/locations/runCommands/read | 場所で使用可能な実行コマンドを一覧表示します。 |
> | Microsoft.Compute/locations/usages/read | その場所のサブスクリプションのコンピューティング リソースについて、サービスの上限と現在の使用容量を取得します。 |
> | Microsoft.Compute/locations/vmSizes/read | その場所で使用できる仮想マシンのサイズを一覧表示します。 |
> | Microsoft.Compute/locations/vsmOperations/read | 仮想マシン ランタイム サービス拡張機能を使用した仮想マシン スケール セットの非同期操作の状態を取得します。 |
> | Microsoft.Compute/operations/read | Microsoft.Compute リソース プロバイダーで使用できる操作を一覧表示します。 |
> | Microsoft.Compute/proximityPlacementGroups/read | 近接通信配置グループのプロパティを取得します |
> | Microsoft.Compute/proximityPlacementGroups/write | 新しい近接通信配置グループを作成するか、既存のものを更新します |
> | Microsoft.Compute/proximityPlacementGroups/delete | 近接通信配置グループを削除します |
> | Microsoft.Compute/restorePointCollections/read | 復元ポイント コレクションのプロパティを取得します。 |
> | Microsoft.Compute/restorePointCollections/write | 新しい復元ポイント コレクションを作成するか、既存の復元ポイント コレクションを更新します。 |
> | Microsoft.Compute/restorePointCollections/delete | 復元ポイント コレクションと、コレクションに含まれている復元ポイントを削除します。 |
> | Microsoft.Compute/restorePointCollections/restorePoints/read | 復元ポイントのプロパティを取得します。 |
> | Microsoft.Compute/restorePointCollections/restorePoints/write | 新しい復元ポイントを作成します。 |
> | Microsoft.Compute/restorePointCollections/restorePoints/delete | 復元ポイントを削除します。 |
> | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | 復元ポイントのプロパティを BLOB SAS URI と共に取得します。 |
> | Microsoft.Compute/restorePointCollections/restorePoints/diskRestorePoints/read | 増分 DiskRestorePoint のプロパティを取得します |
> | Microsoft.Compute/restorePointCollections/restorePoints/diskRestorePoints/beginGetAccess/action | 増分 DiskRestorePoint の SAS URI を取得します |
> | Microsoft.Compute/restorePointCollections/restorePoints/diskRestorePoints/endGetAccess/action | 増分 DiskRestorePoint の SAS URI を取り消します |
> | Microsoft.Compute/sharedVMExtensions/read | 共有 VM 拡張機能のプロパティを取得します |
> | Microsoft.Compute/sharedVMExtensions/write | 共有 VM 拡張機能を新規作成するか、既存のものを更新します |
> | Microsoft.Compute/sharedVMExtensions/delete | 共有 VM 拡張機能を削除します |
> | Microsoft.Compute/sharedVMExtensions/versions/read | 共有 VM 拡張機能のバージョンのプロパティを取得します |
> | Microsoft.Compute/sharedVMExtensions/versions/write | 共有 VM 拡張機能のバージョンを新規作成するか、既存のものを更新します |
> | Microsoft.Compute/sharedVMExtensions/versions/delete | 共有 VM 拡張機能のバージョンを削除します |
> | Microsoft.Compute/sharedVMImages/read | SharedVMImage のプロパティを取得します。 |
> | Microsoft.Compute/sharedVMImages/write | 新しい SharedVMImage を作成するか、既存のものを更新します。 |
> | Microsoft.Compute/sharedVMImages/delete | SharedVMImage を削除します。 |
> | Microsoft.Compute/sharedVMImages/versions/read | SharedVMImageVersion のプロパティを取得します。 |
> | Microsoft.Compute/sharedVMImages/versions/write | 新しい SharedVMImageVersion を作成するか、既存のものを更新します。 |
> | Microsoft.Compute/sharedVMImages/versions/delete | SharedVMImageVersion を削除します。 |
> | Microsoft.Compute/sharedVMImages/versions/replicate/action | ターゲット リージョンに SharedVMImageVersion をレプリケートします。 |
> | Microsoft.Compute/skus/read | サブスクリプションで使用可能な Microsoft.Compute SKU の一覧を取得します。 |
> | Microsoft.Compute/snapshots/read | スナップショットのプロパティを取得します。 |
> | Microsoft.Compute/snapshots/write | 新しいスナップショットを作成するか、既存のスナップショットを更新します。 |
> | Microsoft.Compute/snapshots/delete | スナップショットを削除します。 |
> | Microsoft.Compute/snapshots/beginGetAccess/action | BLOB アクセス用のスナップショットの SAS URI を取得します。 |
> | Microsoft.Compute/snapshots/endGetAccess/action | スナップショットの SAS URI を取り消します。 |
> | Microsoft.Compute/sshPublicKeys/read | SSH 公開キーのプロパティを取得します |
> | Microsoft.Compute/sshPublicKeys/write | 新しい SSH 公開キーを作成するか、既存の SSH 公開キーを更新します |
> | Microsoft.Compute/sshPublicKeys/delete | SSH 公開キーを削除します |
> | Microsoft.Compute/sshPublicKeys/generateKeyPair/action | 新しい SSH 公開/秘密キーの組を生成します |
> | Microsoft.Compute/virtualMachines/read | 仮想マシンのプロパティを取得します。 |
> | Microsoft.Compute/virtualMachines/write | 新しい仮想マシンを作成するか、既存の仮想マシンを更新します。 |
> | Microsoft.Compute/virtualMachines/delete | 仮想マシンを削除します。 |
> | Microsoft.Compute/virtualMachines/start/action | 仮想マシンを起動します。 |
> | Microsoft.Compute/virtualMachines/powerOff/action | 仮想マシンを電源オフにします。 仮想マシンの料金は引き続き課金されます。 |
> | Microsoft.Compute/virtualMachines/reapply/action | 仮想マシンの現在のモデルを再適用します |
> | Microsoft.Compute/virtualMachines/redeploy/action | 仮想マシンを再デプロイします。 |
> | Microsoft.Compute/virtualMachines/restart/action | 仮想マシンを再起動します。 |
> | Microsoft.Compute/virtualMachines/retrieveBootDiagnosticsData/action | ブート診断ログ BLOB URI を取得します |
> | Microsoft.Compute/virtualMachines/deallocate/action | 仮想マシンを電源オフにし、コンピューティング リソースを解放します。 |
> | Microsoft.Compute/virtualMachines/generalize/action | 仮想マシンの状態を [一般化] に設定し、仮想マシンのキャプチャを準備します。 |
> | Microsoft.Compute/virtualMachines/capture/action | 仮想ハード ディスクをコピーして仮想マシンをキャプチャし、テンプレートを生成します。このテンプレートを使用して、類似する仮想マシンを作成できます。 |
> | Microsoft.Compute/virtualMachines/runCommand/action | 仮想マシンで定義済みのスクリプトを実行します。 |
> | Microsoft.Compute/virtualMachines/convertToManagedDisks/action | 仮想マシンの BLOB ベースのディスクをマネージド ディスクに変換します。 |
> | Microsoft.Compute/virtualMachines/performMaintenance/action | VM でメンテナンス操作を実行します。 |
> | Microsoft.Compute/virtualMachines/reimage/action | 差分ディスクを使用する仮想マシンを再イメージ化します。 |
> | Microsoft.Compute/virtualMachines/installPatches/action | ユーザーが指定したパラメーターに基づいて、仮想マシンに使用可能な OS 更新修正プログラムがインストールされます。 利用可能な修正プログラムの一覧を含む評価結果も、この操作の一部として更新されます。 |
> | Microsoft.Compute/virtualMachines/assessPatches/action | 仮想マシンが評価され、それに対して使用可能な OS 更新修正プログラムの一覧が見つけられます。 |
> | Microsoft.Compute/virtualMachines/cancelPatchInstallation/action | 仮想マシンで実行中の OS 更新修正プログラムのインストール操作がキャンセルされます。 |
> | Microsoft.Compute/virtualMachines/simulateEviction/action | スポット仮想マシンの削除をシミュレートします |
> | Microsoft.Compute/virtualMachines/extensions/read | 仮想マシン拡張機能のプロパティを取得します。 |
> | Microsoft.Compute/virtualMachines/extensions/write | 新しい仮想マシン拡張機能を作成するか、既存の仮想マシン拡張機能を更新します。 |
> | Microsoft.Compute/virtualMachines/extensions/delete | 仮想マシン拡張機能を削除します。 |
> | Microsoft.Compute/virtualMachines/instanceView/read | 仮想マシンとそのリソースの詳細なランタイムの状態を取得します。 |
> | Microsoft.Compute/virtualMachines/patchAssessmentResults/latest/read | 最新のパッチの評価操作に関する概要を取得します |
> | Microsoft.Compute/virtualMachines/patchAssessmentResults/latest/softwarePatches/read | 最後のパッチの評価操作中に評価されたパッチの一覧を取得します |
> | Microsoft.Compute/virtualMachines/patchInstallationResults/read | 最新のパッチのインストール操作の概要を取得します |
> | Microsoft.Compute/virtualMachines/patchInstallationResults/softwarePatches/read | 最後のパッチのインストール操作中にインストールが試行されたパッチの一覧を取得します |
> | Microsoft.Compute/virtualMachines/runCommands/read | 仮想マシンの実行コマンドのプロパティを取得します |
> | Microsoft.Compute/virtualMachines/runCommands/write | 新しい仮想マシンの実行コマンドを作成するか、既存のものを更新します |
> | Microsoft.Compute/virtualMachines/runCommands/delete | 仮想マシンの実行コマンドを削除します |
> | Microsoft.Compute/virtualMachines/vmSizes/read | 仮想マシンを更新する際に使用できるサイズを一覧表示します。 |
> | Microsoft.Compute/virtualMachineScaleSets/read | 仮想マシン スケール セットのプロパティを取得します。 |
> | Microsoft.Compute/virtualMachineScaleSets/write | 新しい仮想マシン スケール セットを作成するか、既存のものを更新します。 |
> | Microsoft.Compute/virtualMachineScaleSets/delete | 仮想マシン スケール セットを削除します。 |
> | Microsoft.Compute/virtualMachineScaleSets/delete/action | 仮想マシン スケール セットのインスタンスを削除します。 |
> | Microsoft.Compute/virtualMachineScaleSets/start/action | 仮想マシン スケール セットのインスタンスを起動します。 |
> | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | 仮想マシン スケール セットのインスタンスを電源オフにします。 |
> | Microsoft.Compute/virtualMachineScaleSets/restart/action | 仮想マシン スケール セットのインスタンスを再起動します。 |
> | Microsoft.Compute/virtualMachineScaleSets/deallocate/action | 仮想マシン スケール セットのインスタンスのコンピューティング リソースを電源オフにして解放します。  |
> | Microsoft.Compute/virtualMachineScaleSets/manualUpgrade/action | インスタンスを仮想マシン スケール セットの最新モデルに手動で更新します。 |
> | Microsoft.Compute/virtualMachineScaleSets/reimage/action | 仮想マシン スケール セットのインスタンスを再イメージ化します。 |
> | Microsoft.Compute/virtualMachineScaleSets/reimageAll/action | 仮想マシン スケール セットのインスタンスのすべてのディスク (OS ディスクとデータ ディスク) を再イメージ化します  |
> | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | 仮想マシン スケール セットのインスタンスを再デプロイします。 |
> | Microsoft.Compute/virtualMachineScaleSets/performMaintenance/action | 仮想マシン スケール セットのインスタンスで計画的なメンテナンスを実行します。 |
> | Microsoft.Compute/virtualMachineScaleSets/scale/action | 既存の仮想マシン スケール セットを、指定されたインスタンス数にスケールイン/スケールアウトできるかどうかを確認します。 |
> | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | サービス ファブリック仮想マシン スケール セットのプラットフォーム更新ドメインを手動で移動して、スタックしている保留中の更新を終了します。 |
> | Microsoft.Compute/virtualMachineScaleSets/osRollingUpgrade/action | ローリング アップグレードを開始して、すべての仮想マシン スケール セット インスタンスを最新の使用可能なプラットフォーム イメージ OS バージョンに移行します。 |
> | Microsoft.Compute/virtualMachineScaleSets/setOrchestrationServiceState/action | 操作の入力で指定されたアクションに基づいて、オーケストレーション サービスの状態を設定します。 |
> | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/action | 仮想マシン スケール セットのローリング アップグレードを取り消します。 |
> | Microsoft.Compute/virtualMachineScaleSets/extensions/read | 仮想マシン スケール セット拡張機能のプロパティを取得します。 |
> | Microsoft.Compute/virtualMachineScaleSets/extensions/write | 新しい仮想マシン スケール セット拡張機能を作成するか、既存のものを更新します。 |
> | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | 仮想マシン スケール セット拡張機能を削除します。 |
> | Microsoft.Compute/virtualMachineScaleSets/extensions/roles/read | 仮想マシン ランタイム サービス拡張機能を備えている仮想マシン スケール セットのロールのプロパティが取得されます |
> | Microsoft.Compute/virtualMachineScaleSets/extensions/roles/write | 仮想マシン ランタイム サービス拡張機能を備えている仮想マシン スケール セットの既存のロールのプロパティが更新されます |
> | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | 仮想マシン スケール セットのインスタンス ビューを取得します。 |
> | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | 仮想マシン スケール セットのすべてのネットワーク インターフェイスのプロパティを取得します。 |
> | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | 仮想マシン スケール セットの OS アップグレードの履歴を取得します。 |
> | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | 仮想マシン スケール セットのすべてのパブリック IP アドレスのプロパティを取得します。 |
> | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | 仮想マシン スケール セットの最新のローリング アップグレードの状態を取得します。 |
> | Microsoft.Compute/virtualMachineScaleSets/skus/read | 既存の仮想マシン スケール セットの有効な SKU を一覧表示します。 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | VM スケール セット内の仮想マシンのプロパティを取得します。 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | VM スケール セット内の仮想マシンのプロパティを更新します。 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | VM スケール セット内の特定の仮想マシンを削除します。 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | VM スケール セット内の仮想マシン インスタンスを起動します。 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | VM スケール セット内の仮想マシン インスタンスを電源オフにします。 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | VM スケール セット内の仮想マシン インスタンスを再起動します。 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | VM スケール セット内の仮想マシンのコンピューティング リソースを電源オフにして解放します。 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | 仮想マシン スケール セット内の仮想マシン インスタンスを再イメージ化します。 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimageAll/action | 仮想マシン スケール セット内の仮想マシン インスタンスのすべてのディスク (OS ディスクとデータ ディスク) を再イメージ化します。 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | 仮想マシン スケール セット内の仮想マシン インスタンスを再デプロイします。 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/retrieveBootDiagnosticsData/action | 仮想マシン スケール セット内の仮想マシン インスタンスのブート診断ログ BLOB URI を取得します |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | 仮想マシン スケール セットの仮想マシン インスタンスで計画的なメンテナンスを実行します。 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommand/action | 仮想マシン スケール セット内の仮想マシン インスタンスで、定義済みのスクリプトを実行します。 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/simulateEviction/action | 仮想マシン スケール セット内のスポット仮想マシンの削除をシミュレートします |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/extensions/read | 仮想マシン スケール セット内の仮想マシンの拡張機能のプロパティを取得します |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/extensions/write | 仮想マシン スケール セット内の仮想マシンの新しい拡張機能を作成するか、既存の拡張機能を更新します |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/extensions/delete | 仮想マシン スケール セット内の仮想マシンの拡張機能を削除します |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | VM スケール セット内の仮想マシンのインスタンス ビューを取得します。 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | 仮想マシン スケール セットを使って作成された仮想マシンの 1 つまたはすべてのネットワーク インターフェイスのプロパティを取得します。 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | 仮想マシン スケール セットを使って作成されたネットワーク インターフェイスの 1 つまたはすべての IP 構成のプロパティを取得します。 IP 構成は、プライベート IP を表します。 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | 仮想マシン スケール セットを使って作成されたパブリック IP アドレスのプロパティを取得します。 仮想マシン スケール セットは、ipconfiguration (プライベート IP) あたり最大で 1 つのパブリック IP を作成できます。 |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommands/read | 仮想マシン スケール セット内の仮想マシンの実行コマンドのプロパティを取得します |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommands/write | 仮想マシン スケール セット内の仮想マシンの新しい実行コマンドを作成するか、既存のものを更新します |
> | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommands/delete | 仮想マシン スケール セット内の仮想マシンの実行コマンドを削除します |
> | Microsoft.Compute/virtualMachineScaleSets/vmSizes/read | 仮想マシン スケール セットの仮想マシンを作成または更新する際に使用できるサイズを一覧表示します。 |
> | **DataAction** | **説明** |
> | Microsoft.Compute/virtualMachines/login/action | 仮想マシンに通常のユーザーとしてログインします。 |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Windows 管理者または Linux のルート ユーザーの権限で仮想マシンにログインします。 |

### <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

Azure サービス:[Service Fabric](../service-fabric/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.ServiceFabric/register/action | アクションを登録します。 |
> | Microsoft.ServiceFabric/clusters/read | クラスターを読み取ります。 |
> | Microsoft.ServiceFabric/clusters/write | クラスターを作成または更新します。 |
> | Microsoft.ServiceFabric/clusters/delete | クラスターを削除します。 |
> | Microsoft.ServiceFabric/clusters/applications/read | アプリケーションを読み取ります。 |
> | Microsoft.ServiceFabric/clusters/applications/write | アプリケーションを作成または更新します。 |
> | Microsoft.ServiceFabric/clusters/applications/delete | アプリケーションを削除します。 |
> | Microsoft.ServiceFabric/clusters/applications/services/read | サービスを読み取ります。 |
> | Microsoft.ServiceFabric/clusters/applications/services/write | サービスを作成または更新します。 |
> | Microsoft.ServiceFabric/clusters/applications/services/delete | サービスを削除します。 |
> | Microsoft.ServiceFabric/clusters/applications/services/partitions/read | パーティションを読み取ります。 |
> | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | レプリカを読み取ります。 |
> | Microsoft.ServiceFabric/clusters/applications/services/statuses/read | サービスの状態を読み取ります。 |
> | Microsoft.ServiceFabric/clusters/applicationTypes/read | アプリケーションの種類を読み取ります。 |
> | Microsoft.ServiceFabric/clusters/applicationTypes/write | アプリケーションの種類を作成または更新します。 |
> | Microsoft.ServiceFabric/clusters/applicationTypes/delete | アプリケーションの種類を削除します。 |
> | Microsoft.ServiceFabric/clusters/applicationTypes/versions/read | アプリケーションの種類のバージョンを読み取ります。 |
> | Microsoft.ServiceFabric/clusters/applicationTypes/versions/write | アプリケーションの種類のバージョンを作成または更新します。 |
> | Microsoft.ServiceFabric/clusters/applicationTypes/versions/delete | アプリケーションの種類のバージョンを削除します。 |
> | Microsoft.ServiceFabric/clusters/nodes/read | ノードを読み取ります。 |
> | Microsoft.ServiceFabric/clusters/statuses/read | クラスターの状態を読み取ります。 |
> | Microsoft.ServiceFabric/locations/clusterVersions/read | クラスターのバージョンを読み取ります。 |
> | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | 特定の環境のクラスターのバージョンを読み取ります。 |
> | Microsoft.ServiceFabric/locations/operationresults/read | 操作結果を読み取ります。 |
> | Microsoft.ServiceFabric/locations/operations/read | 場所別に操作を読み取ります。 |
> | Microsoft.ServiceFabric/operations/read | 使用可能な操作を読み取ります。 |

## <a name="networking"></a>ネットワーク

### <a name="microsoftcdn"></a>Microsoft.Cdn

Azure サービス:[Content Delivery Network](../cdn/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Cdn/register/action | CDN リソース プロバイダーにサブスクリプションを登録し、CDN プロファイルを作成できるようにします。 |
> | Microsoft.Cdn/CheckNameAvailability/action |  |
> | Microsoft.Cdn/ValidateProbe/action |  |
> | Microsoft.Cdn/CheckResourceUsage/action |  |
> | Microsoft.Cdn/cdnwebapplicationfirewallmanagedrulesets/read |  |
> | Microsoft.Cdn/cdnwebapplicationfirewallmanagedrulesets/write |  |
> | Microsoft.Cdn/cdnwebapplicationfirewallmanagedrulesets/delete |  |
> | Microsoft.Cdn/cdnwebapplicationfirewallpolicies/read |  |
> | Microsoft.Cdn/cdnwebapplicationfirewallpolicies/write |  |
> | Microsoft.Cdn/cdnwebapplicationfirewallpolicies/delete |  |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/edgenodes/write |  |
> | Microsoft.Cdn/edgenodes/delete |  |
> | Microsoft.Cdn/operationresults/read |  |
> | Microsoft.Cdn/operationresults/write |  |
> | Microsoft.Cdn/operationresults/delete |  |
> | Microsoft.Cdn/operationresults/cdnwebapplicationfirewallpolicyresults/read |  |
> | Microsoft.Cdn/operationresults/cdnwebapplicationfirewallpolicyresults/write |  |
> | Microsoft.Cdn/operationresults/cdnwebapplicationfirewallpolicyresults/delete |  |
> | Microsoft.Cdn/operationresults/profileresults/read |  |
> | Microsoft.Cdn/operationresults/profileresults/write |  |
> | Microsoft.Cdn/operationresults/profileresults/delete |  |
> | Microsoft.Cdn/operationresults/profileresults/CheckResourceUsage/action |  |
> | Microsoft.Cdn/operationresults/profileresults/GenerateSsoUri/action |  |
> | Microsoft.Cdn/operationresults/profileresults/GetSupportedOptimizationTypes/action |  |
> | Microsoft.Cdn/operationresults/profileresults/afdendpointresults/read |  |
> | Microsoft.Cdn/operationresults/profileresults/afdendpointresults/write |  |
> | Microsoft.Cdn/operationresults/profileresults/afdendpointresults/delete |  |
> | Microsoft.Cdn/operationresults/profileresults/afdendpointresults/Purge/action |  |
> | Microsoft.Cdn/operationresults/profileresults/afdendpointresults/Usages/action |  |
> | Microsoft.Cdn/operationresults/profileresults/afdendpointresults/ValidateCustomDomain/action |  |
> | Microsoft.Cdn/operationresults/profileresults/afdendpointresults/routeresults/read |  |
> | Microsoft.Cdn/operationresults/profileresults/afdendpointresults/routeresults/write |  |
> | Microsoft.Cdn/operationresults/profileresults/afdendpointresults/routeresults/delete |  |
> | Microsoft.Cdn/operationresults/profileresults/customdomainresults/read |  |
> | Microsoft.Cdn/operationresults/profileresults/customdomainresults/write |  |
> | Microsoft.Cdn/operationresults/profileresults/customdomainresults/delete |  |
> | Microsoft.Cdn/operationresults/profileresults/customdomainresults/RefreshValidationToken/action |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/read |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/write |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/delete |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/CheckResourceUsage/action |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/Start/action |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/Stop/action |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/Purge/action |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/Load/action |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/ValidateCustomDomain/action |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/read |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/write |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/delete |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/action |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/action |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/origingroupresults/read |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/origingroupresults/write |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/origingroupresults/delete |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/read |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/write |  |
> | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/delete |  |
> | Microsoft.Cdn/operationresults/profileresults/origingroupresults/read |  |
> | Microsoft.Cdn/operationresults/profileresults/origingroupresults/write |  |
> | Microsoft.Cdn/operationresults/profileresults/origingroupresults/delete |  |
> | Microsoft.Cdn/operationresults/profileresults/origingroupresults/Usages/action |  |
> | Microsoft.Cdn/operationresults/profileresults/origingroupresults/originresults/read |  |
> | Microsoft.Cdn/operationresults/profileresults/origingroupresults/originresults/write |  |
> | Microsoft.Cdn/operationresults/profileresults/origingroupresults/originresults/delete |  |
> | Microsoft.Cdn/operationresults/profileresults/rulesetresults/read |  |
> | Microsoft.Cdn/operationresults/profileresults/rulesetresults/write |  |
> | Microsoft.Cdn/operationresults/profileresults/rulesetresults/delete |  |
> | Microsoft.Cdn/operationresults/profileresults/rulesetresults/Usages/action |  |
> | Microsoft.Cdn/operationresults/profileresults/rulesetresults/ruleresults/read |  |
> | Microsoft.Cdn/operationresults/profileresults/rulesetresults/ruleresults/write |  |
> | Microsoft.Cdn/operationresults/profileresults/rulesetresults/ruleresults/delete |  |
> | Microsoft.Cdn/operationresults/profileresults/secretresults/read |  |
> | Microsoft.Cdn/operationresults/profileresults/secretresults/write |  |
> | Microsoft.Cdn/operationresults/profileresults/secretresults/delete |  |
> | Microsoft.Cdn/operationresults/profileresults/secretresults/ValidateSecret/action |  |
> | Microsoft.Cdn/operationresults/profileresults/securitypoliciesresults/read |  |
> | Microsoft.Cdn/operationresults/profileresults/securitypoliciesresults/write |  |
> | Microsoft.Cdn/operationresults/profileresults/securitypoliciesresults/delete |  |
> | Microsoft.Cdn/operations/read |  |
> | Microsoft.Cdn/profiles/read |  |
> | Microsoft.Cdn/profiles/write |  |
> | Microsoft.Cdn/profiles/delete |  |
> | Microsoft.Cdn/profiles/CheckResourceUsage/action |  |
> | Microsoft.Cdn/profiles/GenerateSsoUri/action |  |
> | Microsoft.Cdn/profiles/GetSupportedOptimizationTypes/action |  |
> | Microsoft.Cdn/profiles/afdendpoints/read |  |
> | Microsoft.Cdn/profiles/afdendpoints/write |  |
> | Microsoft.Cdn/profiles/afdendpoints/delete |  |
> | Microsoft.Cdn/profiles/afdendpoints/Purge/action |  |
> | Microsoft.Cdn/profiles/afdendpoints/Usages/action |  |
> | Microsoft.Cdn/profiles/afdendpoints/ValidateCustomDomain/action |  |
> | Microsoft.Cdn/profiles/afdendpoints/routes/read |  |
> | Microsoft.Cdn/profiles/afdendpoints/routes/write |  |
> | Microsoft.Cdn/profiles/afdendpoints/routes/delete |  |
> | Microsoft.Cdn/profiles/customdomains/read |  |
> | Microsoft.Cdn/profiles/customdomains/write |  |
> | Microsoft.Cdn/profiles/customdomains/delete |  |
> | Microsoft.Cdn/profiles/customdomains/RefreshValidationToken/action |  |
> | Microsoft.Cdn/profiles/endpoints/read |  |
> | Microsoft.Cdn/profiles/endpoints/write |  |
> | Microsoft.Cdn/profiles/endpoints/delete |  |
> | Microsoft.Cdn/profiles/endpoints/CheckResourceUsage/action |  |
> | Microsoft.Cdn/profiles/endpoints/Start/action |  |
> | Microsoft.Cdn/profiles/endpoints/Stop/action |  |
> | Microsoft.Cdn/profiles/endpoints/Purge/action |  |
> | Microsoft.Cdn/profiles/endpoints/Load/action |  |
> | Microsoft.Cdn/profiles/endpoints/ValidateCustomDomain/action |  |
> | Microsoft.Cdn/profiles/endpoints/customdomains/read |  |
> | Microsoft.Cdn/profiles/endpoints/customdomains/write |  |
> | Microsoft.Cdn/profiles/endpoints/customdomains/delete |  |
> | Microsoft.Cdn/profiles/endpoints/customdomains/DisableCustomHttps/action |  |
> | Microsoft.Cdn/profiles/endpoints/customdomains/EnableCustomHttps/action |  |
> | Microsoft.Cdn/profiles/endpoints/origingroups/read |  |
> | Microsoft.Cdn/profiles/endpoints/origingroups/write |  |
> | Microsoft.Cdn/profiles/endpoints/origingroups/delete |  |
> | Microsoft.Cdn/profiles/endpoints/origins/read |  |
> | Microsoft.Cdn/profiles/endpoints/origins/write |  |
> | Microsoft.Cdn/profiles/endpoints/origins/delete |  |
> | Microsoft.Cdn/profiles/origingroups/read |  |
> | Microsoft.Cdn/profiles/origingroups/write |  |
> | Microsoft.Cdn/profiles/origingroups/delete |  |
> | Microsoft.Cdn/profiles/origingroups/Usages/action |  |
> | Microsoft.Cdn/profiles/origingroups/origins/read |  |
> | Microsoft.Cdn/profiles/origingroups/origins/write |  |
> | Microsoft.Cdn/profiles/origingroups/origins/delete |  |
> | Microsoft.Cdn/profiles/rulesets/read |  |
> | Microsoft.Cdn/profiles/rulesets/write |  |
> | Microsoft.Cdn/profiles/rulesets/delete |  |
> | Microsoft.Cdn/profiles/rulesets/Usages/action |  |
> | Microsoft.Cdn/profiles/rulesets/rules/read |  |
> | Microsoft.Cdn/profiles/rulesets/rules/write |  |
> | Microsoft.Cdn/profiles/rulesets/rules/delete |  |
> | Microsoft.Cdn/profiles/secrets/read |  |
> | Microsoft.Cdn/profiles/secrets/write |  |
> | Microsoft.Cdn/profiles/secrets/delete |  |
> | Microsoft.Cdn/profiles/secrets/ValidateSecret/action |  |
> | Microsoft.Cdn/profiles/securitypolicies/read |  |
> | Microsoft.Cdn/profiles/securitypolicies/write |  |
> | Microsoft.Cdn/profiles/securitypolicies/delete |  |

### <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

Azure サービス:クラシック デプロイ モデルの仮想ネットワーク

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.ClassicNetwork/register/action | 従来のネットワークに登録します。 |
> | Microsoft.ClassicNetwork/expressroutecrossconnections/read | ExpressRoute 交差接続を取得します。 |
> | Microsoft.ClassicNetwork/expressroutecrossconnections/write | ExpressRoute 交差接続を追加します。 |
> | Microsoft.ClassicNetwork/expressroutecrossconnections/operationstatuses/read | ExpressRoute のクロス接続操作の状態を取得します。 |
> | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/read | ExpressRoute 交差接続のピアリングを取得します。 |
> | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/write | ExpressRoute 交差接続のピアリングを追加します。 |
> | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/delete | ExpressRoute 交差接続のピアリングを削除します。 |
> | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/operationstatuses/read | ExpressRoute 交差接続のピアリング操作の状態を取得します。 |
> | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | サポート対象デバイスの一覧を取得します。 |
> | Microsoft.ClassicNetwork/networkSecurityGroups/read | ネットワーク セキュリティ グループを取得します。 |
> | Microsoft.ClassicNetwork/networkSecurityGroups/write | 新しいネットワーク セキュリティ グループを追加します。 |
> | Microsoft.ClassicNetwork/networkSecurityGroups/delete | ネットワーク セキュリティ グループを削除します。 |
> | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | ネットワーク セキュリティ グループの操作の状態を読み取ります。 |
> | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | ネットワーク セキュリティ グループの診断設定を取得します。 |
> | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | ネットワーク セキュリティ グループの診断設定を作成または更新します。この操作は、Insights リソース プロバイダーによって補完されます。 |
> | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | ネットワーク セキュリティ グループのイベントを取得します。 |
> | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | セキュリティ規則を取得します。 |
> | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | セキュリティ規則を追加または更新します。 |
> | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | セキュリティ規則を削除します。 |
> | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | ネットワーク セキュリティ グループのセキュリティ規則の操作の状態を読み取ります。 |
> | Microsoft.ClassicNetwork/operations/read | 従来のネットワーク操作を取得します。 |
> | Microsoft.ClassicNetwork/quotas/read | サブスクリプションのクォータを取得します。 |
> | Microsoft.ClassicNetwork/reservedIps/read | 予約済み IP を取得します。 |
> | Microsoft.ClassicNetwork/reservedIps/write | 新しい予約済み IP を追加します。 |
> | Microsoft.ClassicNetwork/reservedIps/delete | 予約済み IP を削除します。 |
> | Microsoft.ClassicNetwork/reservedIps/link/action | 予約済み IP をリンクします。 |
> | Microsoft.ClassicNetwork/reservedIps/join/action | 予約済み IP を結合します。 |
> | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | 予約済み IP の操作の状態を読み取ります。 |
> | Microsoft.ClassicNetwork/virtualNetworks/read | 仮想ネットワークを取得します。 |
> | Microsoft.ClassicNetwork/virtualNetworks/write | 新しい仮想ネットワークを追加します。 |
> | Microsoft.ClassicNetwork/virtualNetworks/delete | 仮想ネットワークを削除します。 |
> | Microsoft.ClassicNetwork/virtualNetworks/peer/action | 仮想ネットワークを別の仮想ネットワークとピアリングします。 |
> | Microsoft.ClassicNetwork/virtualNetworks/join/action | 仮想ネットワークに参加します。 |
> | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | 指定された IP アドレスを仮想ネットワーク内で使用できるかどうかを確認します。 |
> | Microsoft.ClassicNetwork/virtualNetworks/validateMigration/action | 仮想ネットワークの移行を検証する |
> | Microsoft.ClassicNetwork/virtualNetworks/prepareMigration/action | 仮想ネットワークの移行を準備する |
> | Microsoft.ClassicNetwork/virtualNetworks/commitMigration/action | 仮想ネットワークの移行をコミットする |
> | Microsoft.ClassicNetwork/virtualNetworks/abortMigration/action | 仮想ネットワークの移行を中止する |
> | Microsoft.ClassicNetwork/virtualNetworks/capabilities/read | 機能を表示します。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/read | 仮想ネットワーク ゲートウェイを取得します。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/write | 仮想ネットワーク ゲートウェイを追加します。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/delete | 仮想ネットワーク ゲートウェイを削除します。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/startDiagnostics/action | 仮想ネットワーク ゲートウェイの診断を開始します。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/action | 仮想ネットワーク ゲートウェイの診断を停止します。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/action | ゲートウェイ診断をダウンロードします。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/action | 回線サービス キーを取得します。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/action | デバイス構成スクリプトをダウンロードします。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/listPackage/action | 仮想ネットワーク ゲートウェイ パッケージを一覧表示します。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | 失効したクライアント証明書を読み取ります。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | クライアント証明書を失効させます。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | クライアント証明書の失効を取り消します。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | クライアント ルート証明書を検索します。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | 新しいクライアント ルート証明書をアップロードします。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | 仮想ネットワーク ゲートウェイ クライアント証明書を削除します。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/download/action | 拇印によって証明書をダウンロードします。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | 仮想ネットワーク ゲートウェイ証明書パッケージを一覧表示します。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/read | 接続の一覧を取得します。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/connect/action | サイト間ゲートウェイ接続を行います。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/disconnect/action | サイト間ゲートウェイ接続を切断します。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/test/action | サイト間ゲートウェイ接続をテストします。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | 仮想ネットワーク ゲートウェイの操作の状態を読み取ります。 |
> | Microsoft.ClassicNetwork/virtualNetworks/gateways/packages/read | 仮想ネットワーク ゲートウェイ パッケージを取得します。 |
> | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | 仮想ネットワークの操作の状態を読み取ります。 |
> | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | リモート仮想ネットワーク ピアリング プロキシを取得します。 |
> | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | リモート仮想ネットワーク ピアリング プロキシを追加または更新します。 |
> | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | リモート仮想ネットワーク ピアリング プロキシを削除します。 |
> | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | サブネットに関連付けられたネットワーク セキュリティ グループを取得します。 |
> | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | サブネットに関連付けられたネットワーク セキュリティ グループを追加します。 |
> | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | サブネットに関連付けられたネットワーク セキュリティ グループを削除します。 |
> | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | 仮想ネットワーク サブネットに関連付けられたネットワーク セキュリティ グループの操作の状態を読み取ります。 |
> | Microsoft.ClassicNetwork/virtualNetworks/virtualNetworkPeerings/read | 仮想ネットワーク ピアリングを取得します。 |

### <a name="microsoftnetwork"></a>Microsoft.Network

Azure サービス:[Application Gateway](../application-gateway/index.yml)、[Azure Bastion](../bastion/index.yml)、[Azure DDoS Protection](../ddos-protection/ddos-protection-overview.md)、[Azure DNS](../dns/index.yml)、[Azure ExpressRoute](../expressroute/index.yml)、[Azure Firewall](../firewall/index.yml), [Azure Front Door Service](../frontdoor/index.yml)、[Azure Private Link](../private-link/index.yml)、[Load Balancer](../load-balancer/index.yml)、[Network Watcher](../network-watcher/index.yml)、[Traffic Manager](../traffic-manager/index.yml)、[Virtual Network](../virtual-network/index.yml)、[Virtual WAN](../virtual-wan/index.yml)、[VPN Gateway](../vpn-gateway/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Network/register/action | サブスクリプションを登録します。 |
> | Microsoft.Network/unregister/action | サブスクリプションの登録を解除します。 |
> | Microsoft.Network/checkTrafficManagerNameAvailability/action | Traffic Manager の相対 DNS 名を使用できるかどうかを確認します。 |
> | Microsoft.Network/checkFrontDoorNameAvailability/action | フロントドア名が使用可能かどうかを確認します |
> | Microsoft.Network/applicationGatewayAvailableRequestHeaders/read | Application Gateway の使用可能な要求ヘッダーを取得します。 |
> | Microsoft.Network/applicationGatewayAvailableResponseHeaders/read | Application Gateway の使用可能な応答ヘッダーを取得します。 |
> | Microsoft.Network/applicationGatewayAvailableServerVariables/read | Application Gateway の使用可能なサーバー変数を取得します。 |
> | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Application Gateway で使用可能な SSL オプションです。 |
> | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Application Gateway の SSL の定義済みポリシーです。 |
> | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | アプリケーション ゲートウェイの利用可能な Waf ルール セットを取得します。 |
> | Microsoft.Network/applicationGateways/read | アプリケーション ゲートウェイを取得します。 |
> | Microsoft.Network/applicationGateways/write | アプリケーション ゲートウェイを作成するか、アプリケーション ゲートウェイを更新します。 |
> | Microsoft.Network/applicationGateways/delete | アプリケーション ゲートウェイを削除します。 |
> | Microsoft.Network/applicationGateways/backendhealth/action | アプリケーション ゲートウェイのバックエンドの正常性を取得します。 |
> | Microsoft.Network/applicationGateways/getBackendHealthOnDemand/action | 特定の HTTP 設定とバックエンド プールに対するアプリケーション ゲートウェイ バックエンドの正常性をオンデマンドで取得します |
> | Microsoft.Network/applicationGateways/start/action | アプリケーション ゲートウェイを起動します。 |
> | Microsoft.Network/applicationGateways/stop/action | アプリケーション ゲートウェイを停止します。 |
> | Microsoft.Network/applicationGateways/restart/action | アプリケーション ゲートウェイを再起動します |
> | Microsoft.Network/applicationGateways/migrateV1ToV2/action | v1 sku から v2 sku に Application Gateway を移行します |
> | Microsoft.Network/applicationGateways/getMigrationStatus/action | V1 sku から V2 sku への Application Gateway の移行の状態を取得します |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | アプリケーション ゲートウェイのバックエンド アドレス プールを接続します。 警告不可能です。 |
> | Microsoft.Network/applicationGateways/privateEndpointConnections/read | Application Gateway PrivateEndpoint 接続を取得します |
> | Microsoft.Network/applicationGateways/privateEndpointConnections/write | Application Gateway PrivateEndpoint 接続を更新します |
> | Microsoft.Network/applicationGateways/privateEndpointConnections/delete | Application Gateway PrivateEndpoint 接続を削除します |
> | Microsoft.Network/applicationGateways/privateLinkConfigurations/read | Application Gateway プライベート リンク構成を取得します |
> | Microsoft.Network/applicationGateways/privateLinkResources/read | ApplicationGateway PrivateLink リソースを取得します |
> | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/read | Application Gateway WAF ポリシーを取得します。 |
> | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/write | Application Gateway WAF ポリシーを作成するか、Application Gateway WAF ポリシーを更新します。 |
> | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/delete | Application Gateway WAF ポリシーを削除します。 |
> | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | IP 構成をアプリケーション セキュリティ グループに結合します。 警告不可能です。 |
> | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | セキュリティ ルールをアプリケーション セキュリティ グループに結合します。 警告不可能です。 |
> | Microsoft.Network/applicationSecurityGroups/read | アプリケーション セキュリティ グループ ID を取得します。 |
> | Microsoft.Network/applicationSecurityGroups/write | アプリケーション セキュリティ グループを作成するか、既存のアプリケーション セキュリティ グループを更新します。 |
> | Microsoft.Network/applicationSecurityGroups/delete | アプリケーション セキュリティ グループを削除します。 |
> | Microsoft.Network/applicationSecurityGroups/listIpConfigurations/action | ApplicationSecurityGroup 内の IP 構成を一覧表示します |
> | Microsoft.Network/azureFirewallFqdnTags/read | Azure Firewall の FQDN タグを取得します |
> | Microsoft.Network/azurefirewalls/read | Azure Firewall を取得します |
> | Microsoft.Network/azurefirewalls/write | Azure Firewall を作成または更新します |
> | Microsoft.Network/azurefirewalls/delete | Azure Firewall を削除します |
> | Microsoft.Network/azureFirewalls/applicationRuleCollections/read | Azure Firewall ApplicationRuleCollection を取得します |
> | Microsoft.Network/azureFirewalls/applicationRuleCollections/write | Azure Firewall ApplicationRuleCollection を作成または更新します |
> | Microsoft.Network/azureFirewalls/applicationRuleCollections/delete | Azure Firewall ApplicationRuleCollection を削除します |
> | Microsoft.Network/azureFirewalls/natRuleCollections/read | Azure Firewall NatRuleCollection を取得します |
> | Microsoft.Network/azureFirewalls/natRuleCollections/write | Azure Firewall NatRuleCollection を作成または更新します |
> | Microsoft.Network/azureFirewalls/natRuleCollections/delete | Azure Firewall NatRuleCollection を削除します |
> | Microsoft.Network/azureFirewalls/networkRuleCollections/read | Azure Firewall NetworkRuleCollection を取得します |
> | Microsoft.Network/azureFirewalls/networkRuleCollections/write | Azure Firewall NetworkRuleCollection を作成または更新します |
> | Microsoft.Network/azureFirewalls/networkRuleCollections/delete | Azure Firewall NetworkRuleCollection を削除します |
> | Microsoft.Network/azureWebCategories/read | Azure の Web カテゴリを取得します |
> | Microsoft.Network/bastionHosts/read | 踏み台ホストを取得します |
> | Microsoft.Network/bastionHosts/write | 踏み台ホストを作成または更新します |
> | Microsoft.Network/bastionHosts/delete | 踏み台ホストを削除します |
> | Microsoft.Network/bastionHosts/getactivesessions/action | 踏み台ホストでアクティブなセッションを取得します |
> | Microsoft.Network/bastionHosts/disconnectactivesessions/action | 踏み台ホストで特定のアクティブなセッションを切断します |
> | Microsoft.Network/bastionHosts/getShareableLinks/action | URL が作成された場合に、踏み台サブネット内の指定された VM の共有可能な URL を返します |
> | Microsoft.Network/bastionHosts/createShareableLinks/action | 踏み台内の VM の共有可能な URL を作成し、URL を返します |
> | Microsoft.Network/bastionHosts/deleteShareableLinks/action | 踏み台内の指定された VM の共有可能な URL を削除します |
> | Microsoft.Network/bastionHosts/deleteShareableLinksByToken/action | 要塞内の指定されたトークンの共有可能な URL を削除します |
> | Microsoft.Network/bgpServiceCommunities/read | BGP サービス コミュニティを取得します。 |
> | Microsoft.Network/connections/read | VirtualNetworkGatewayConnection を取得します。 |
> | Microsoft.Network/connections/write | VirtualNetworkGatewayConnection を作成するか、既存の VirtualNetworkGatewayConnection を更新します。 |
> | Microsoft.Network/connections/delete | VirtualNetworkGatewayConnection を削除します。 |
> | Microsoft.Network/connections/sharedkey/action | VirtualNetworkGatewayConnection の SharedKey を取得します。 |
> | Microsoft.Network/connections/vpndeviceconfigurationscript/action | VirtualNetworkGatewayConnection の VPN デバイス構成を取得します。 |
> | Microsoft.Network/connections/revoke/action | Express Route の接続状態を失効としてマークします。 |
> | Microsoft.Network/connections/startpacketcapture/action | Virtual Network ゲートウェイ接続のパケット キャプチャを開始します。 |
> | Microsoft.Network/connections/stoppacketcapture/action | Virtual Network ゲートウェイ接続のパケット キャプチャを停止します。 |
> | Microsoft.Network/connections/getikesas/action | 接続の IKE セキュリティ アソシエーションを一覧表示します |
> | Microsoft.Network/connections/resetconnection/action | VNG の接続をリセットします |
> | Microsoft.Network/connections/sharedKey/read | VirtualNetworkGatewayConnection の SharedKey を取得します。 |
> | Microsoft.Network/connections/sharedKey/write | VirtualNetworkGatewayConnection の SharedKey を作成するか、既存の SharedKey を更新します。 |
> | Microsoft.Network/customIpPrefixes/read | カスタム IP プレフィックスの定義を取得します |
> | Microsoft.Network/customIpPrefixes/write | カスタム IP プレフィックスを作成するか、既存のカスタム IP プレフィックスを更新します |
> | Microsoft.Network/customIpPrefixes/delete | カスタム IP プレフィックスを削除します |
> | Microsoft.Network/ddosCustomPolicies/read | DDoS カスタム ポリシーの定義を取得します |
> | Microsoft.Network/ddosCustomPolicies/write | DDoS カスタム ポリシーを作成するか、既存の DDoS カスタム ポリシーを更新します |
> | Microsoft.Network/ddosCustomPolicies/delete | DDoS カスタム ポリシーを削除します |
> | Microsoft.Network/ddosProtectionPlans/read | DDoS Protection プランを取得します。 |
> | Microsoft.Network/ddosProtectionPlans/write | DDoS Protection プランを作成するか、DDoS Protection プランを更新します。  |
> | Microsoft.Network/ddosProtectionPlans/delete | DDoS Protection プランを削除します。 |
> | Microsoft.Network/ddosProtectionPlans/join/action | DDoS Protection プランを結合します。 警告不可能です。 |
> | Microsoft.Network/dnsoperationresults/read | DNS 操作の結果を取得します。 |
> | Microsoft.Network/dnsoperationstatuses/read | DNS 操作の状態を取得します。  |
> | Microsoft.Network/dnszones/read | DNS ゾーンを JSON 形式で取得します。 ゾーンのプロパティには、タグ、ETag、numberOfRecordSets、maxNumberOfRecordSets があります。 このコマンドでは、ゾーン内のレコード セットは取得されません。 |
> | Microsoft.Network/dnszones/write | リソース グループ内の DNS ゾーンを作成または更新します。  DNS ゾーン リソースのタグを更新する際に使用します。 このコマンドを使用して、ゾーン内のレコード セットを作成または更新することはできません。 |
> | Microsoft.Network/dnszones/delete | JSON 形式の DNS ゾーンを削除します。 ゾーンのプロパティには、タグ、ETag、numberOfRecordSets、maxNumberOfRecordSets があります。 |
> | Microsoft.Network/dnszones/A/read | "A" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、レコードの一覧と、TTL、タグ、および ETag が含まれます。 |
> | Microsoft.Network/dnszones/A/write | DNS ゾーン内の "A" タイプのレコード セットを作成または更新します。 レコード セットの現在のレコードが指定されたレコードに置き換えられます。 |
> | Microsoft.Network/dnszones/A/delete | 指定された名前の "A" タイプのレコード セットを DNS ゾーンから削除します。 |
> | Microsoft.Network/dnszones/AAAA/read | "AAAA" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、レコードの一覧と、TTL、タグ、および ETag が含まれます。 |
> | Microsoft.Network/dnszones/AAAA/write | DNS ゾーン内の "AAAA" タイプのレコード セットを作成または更新します。 レコード セットの現在のレコードが指定されたレコードに置き換えられます。 |
> | Microsoft.Network/dnszones/AAAA/delete | 指定された名前の "AAAA" タイプのレコード セットを DNS ゾーンから削除します。 |
> | Microsoft.Network/dnszones/all/read | 各タイプの DNS レコード セットを取得します。 |
> | Microsoft.Network/dnszones/CAA/read | "CAA" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、TTL、タグ、ETag が含まれます。 |
> | Microsoft.Network/dnszones/CAA/write | DNS ゾーン内の "CAA" タイプのレコード セットを作成または更新します。 レコード セットの現在のレコードが指定されたレコードに置き換えられます。 |
> | Microsoft.Network/dnszones/CAA/delete | 指定された名前の "CAA" タイプのレコード セットを DNS ゾーンから削除します。 |
> | Microsoft.Network/dnszones/CNAME/read | "CNAME" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、TTL、タグ、ETag が含まれます。 |
> | Microsoft.Network/dnszones/CNAME/write | DNS ゾーン内の "CNAME" タイプのレコード セットを作成または更新します。 レコード セットの現在のレコードが指定されたレコードに置き換えられます。 |
> | Microsoft.Network/dnszones/CNAME/delete | 指定された名前の "CNAME" タイプのレコード セットを DNS ゾーンから削除します。 |
> | Microsoft.Network/dnszones/MX/read | "MX" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、レコードの一覧と、TTL、タグ、および ETag が含まれます。 |
> | Microsoft.Network/dnszones/MX/write | DNS ゾーン内の "MX" タイプのレコード セットを作成または更新します。 レコード セットの現在のレコードが指定されたレコードに置き換えられます。 |
> | Microsoft.Network/dnszones/MX/delete | 指定された名前の "MX" タイプのレコード セットを DNS ゾーンから削除します。 |
> | Microsoft.Network/dnszones/NS/read | NS タイプの DNS レコード セットを取得します。 |
> | Microsoft.Network/dnszones/NS/write | NS タイプの DNS レコード セットを作成または更新します。 |
> | Microsoft.Network/dnszones/NS/delete | NS タイプの DNS レコード セットを削除します。 |
> | Microsoft.Network/dnszones/PTR/read | "PTR" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、レコードの一覧と、TTL、タグ、および ETag が含まれます。 |
> | Microsoft.Network/dnszones/PTR/write | DNS ゾーン内の "PTR" タイプのレコード セットを作成または更新します。 レコード セットの現在のレコードが指定されたレコードに置き換えられます。 |
> | Microsoft.Network/dnszones/PTR/delete | 指定された名前の "PTR" タイプのレコード セットを DNS ゾーンから削除します。 |
> | Microsoft.Network/dnszones/recordsets/read | 各タイプの DNS レコード セットを取得します。 |
> | Microsoft.Network/dnszones/SOA/read | SOA タイプの DNS レコード セットを取得します。 |
> | Microsoft.Network/dnszones/SOA/write | SOA タイプの DNS レコード セットを作成または更新します。 |
> | Microsoft.Network/dnszones/SRV/read | "SRV" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、レコードの一覧と、TTL、タグ、および ETag が含まれます。 |
> | Microsoft.Network/dnszones/SRV/write | SRV タイプのレコード セットを作成または更新します。 |
> | Microsoft.Network/dnszones/SRV/delete | 指定された名前の "SRV" タイプのレコード セットを DNS ゾーンから削除します。 |
> | Microsoft.Network/dnszones/TXT/read | "TXT" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、レコードの一覧と、TTL、タグ、および ETag が含まれます。 |
> | Microsoft.Network/dnszones/TXT/write | DNS ゾーン内の "TXT" タイプのレコード セットを作成または更新します。 レコード セットの現在のレコードが指定されたレコードに置き換えられます。 |
> | Microsoft.Network/dnszones/TXT/delete | 指定された名前の "TXT" タイプのレコード セットを DNS ゾーンから削除します。 |
> | Microsoft.Network/dscpConfiguration/write | DSCP 構成を書き込む操作。 |
> | Microsoft.Network/dscpConfiguration/read | DSCP 構成を書き込む操作。 |
> | Microsoft.Network/dscpConfiguration/join/action | DSCP 構成を結合します。 |
> | Microsoft.Network/expressRouteCircuits/read | ExpressRouteCircuit を取得します。 |
> | Microsoft.Network/expressRouteCircuits/write | ExpressRouteCircuit を作成するか、既存の ExpressRouteCircuit を更新します。 |
> | Microsoft.Network/expressRouteCircuits/join/action | Express Route 回線を結合します。 警告不可能です。 |
> | Microsoft.Network/expressRouteCircuits/delete | ExpressRouteCircuit を削除します。 |
> | Microsoft.Network/expressRouteCircuits/authorizations/read | ExpressRouteCircuit の承認を取得します。 |
> | Microsoft.Network/expressRouteCircuits/authorizations/write | ExpressRouteCircuit の承認を作成するか、既存の承認を更新します。 |
> | Microsoft.Network/expressRouteCircuits/authorizations/delete | ExpressRouteCircuit の承認を削除します。 |
> | Microsoft.Network/expressRouteCircuits/peerings/read | ExpressRouteCircuit のピアリングを取得します。 |
> | Microsoft.Network/expressRouteCircuits/peerings/write | ExpressRouteCircuit のピアリングを作成するか、既存のピアリングを更新します。 |
> | Microsoft.Network/expressRouteCircuits/peerings/delete | ExpressRouteCircuit のピアリングを削除します。 |
> | Microsoft.Network/expressRouteCircuits/peerings/arpTables/read | ExpressRouteCircuit のピアリングの ArpTable を取得します。 |
> | Microsoft.Network/expressRouteCircuits/peerings/connections/read | ExpressRouteCircuit の接続を取得します。 |
> | Microsoft.Network/expressRouteCircuits/peerings/connections/write | ExpressRouteCircuit の接続リソースを作成するか、既存の接続リソースを更新します。 |
> | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | ExpressRouteCircuit の接続を削除します。 |
> | Microsoft.Network/expressRouteCircuits/peerings/peerConnections/read | ピアの Express Route 回線の接続を取得します。 |
> | Microsoft.Network/expressRouteCircuits/peerings/routeTables/read | ExpressRouteCircuit のピアリングの RouteTable を取得します。 |
> | Microsoft.Network/expressRouteCircuits/peerings/routeTablesSummary/read | ExpressRouteCircuit のピアリングの RouteTable の概要を取得します。 |
> | Microsoft.Network/expressRouteCircuits/peerings/stats/read | ExpressRouteCircuit のピアリングの統計を取得します。 |
> | Microsoft.Network/expressRouteCircuits/stats/read | ExpressRouteCircuit の統計を取得します。 |
> | Microsoft.Network/expressRouteCrossConnections/read | ExpressRoute のクロス接続を取得します。 |
> | Microsoft.Network/expressRouteCrossConnections/join/action | ExpressRoute のクロス接続を結合します。 警告不可能です。 |
> | Microsoft.Network/expressRouteCrossConnections/peerings/read | ExpressRoute のクロス接続のピアリングを取得します。 |
> | Microsoft.Network/expressRouteCrossConnections/peerings/write | ExpressRoute のクロス接続ピアリングを作成するか、ExpressRoute の既存のクロス接続ピアリングを更新します。 |
> | Microsoft.Network/expressRouteCrossConnections/peerings/delete | ExpressRoute のクロス接続のピアリングを削除します。 |
> | Microsoft.Network/expressRouteCrossConnections/peerings/arpTables/read | ExpressRoute のクロス接続のピアリング ARP テーブルを取得します。 |
> | Microsoft.Network/expressRouteCrossConnections/peerings/routeTables/read | ExpressRoute のクロス接続のピアリング ルート テーブルを取得します。 |
> | Microsoft.Network/expressRouteCrossConnections/peerings/routeTableSummary/read | ExpressRoute のクロス接続のピアリング ルート テーブル概要を取得します。 |
> | Microsoft.Network/expressRouteGateways/read | Express Route ゲートウェイを取得します。 |
> | Microsoft.Network/expressRouteGateways/join/action | Express Route ゲートウェイを結合します。 警告不可能です。 |
> | Microsoft.Network/expressRouteGateways/expressRouteConnections/read | Express Route 接続を取得します。 |
> | Microsoft.Network/expressRouteGateways/expressRouteConnections/write | Express Route 接続を作成するか、Express Route の既存の接続を更新します。 |
> | Microsoft.Network/expressRouteGateways/expressRouteConnections/delete | Express Route 接続を削除します。 |
> | Microsoft.Network/expressRoutePorts/read | ExpressRoutePorts を取得します |
> | Microsoft.Network/expressRoutePorts/write | ExpressRoutePorts を作成または更新します |
> | Microsoft.Network/expressRoutePorts/join/action | Express Route ポートを結合します。 警告不可能です。 |
> | Microsoft.Network/expressRoutePorts/delete | ExpressRoutePorts を削除します |
> | Microsoft.Network/expressRoutePorts/generateloa/action | ExpressRoutePorts の LOA を生成します。 |
> | Microsoft.Network/expressRoutePorts/links/read | ExpressRouteLink を取得します |
> | Microsoft.Network/expressRoutePortsLocations/read | ExpressRoute ポートの場所を取得します |
> | Microsoft.Network/expressRouteServiceProviders/read | Express Route サービス プロバイダーを取得します。 |
> | Microsoft.Network/firewallPolicies/read | ファイアウォール ポリシーを取得します。 |
> | Microsoft.Network/firewallPolicies/write | ファイアウォール ポリシーを作成するか、既存のファイアウォール ポリシーを更新します。 |
> | Microsoft.Network/firewallPolicies/join/action | ファイアウォール ポリシーに参加します。 警告不可能です。 |
> | Microsoft.Network/firewallPolicies/certificates/action | ファイアウォール ポリシー証明書を生成します |
> | Microsoft.Network/firewallPolicies/delete | ファイアウォール ポリシーを削除します。 |
> | Microsoft.Network/firewallPolicies/ruleCollectionGroups/read | ファイアウォール ポリシー規則コレクション グループを取得します |
> | Microsoft.Network/firewallPolicies/ruleCollectionGroups/write | ファイアウォール ポリシー規則コレクション グループを作成するか、既存のファイアウォール ポリシー規則コレクション グループを更新します |
> | Microsoft.Network/firewallPolicies/ruleCollectionGroups/delete | ファイアウォール ポリシー規則コレクション グループを削除します |
> | Microsoft.Network/firewallPolicies/ruleGroups/read | ファイアウォール ポリシー規則グループを取得します。 |
> | Microsoft.Network/firewallPolicies/ruleGroups/write | ファイアウォール ポリシー規則グループを作成するか、既存のファイアウォール ポリシー規則グループを更新します。 |
> | Microsoft.Network/firewallPolicies/ruleGroups/delete | ファイアウォール ポリシー規則グループを削除します。 |
> | Microsoft.Network/frontDoors/read | フロントドアを取得します |
> | Microsoft.Network/frontDoors/write | フロントドアを作成または更新します |
> | Microsoft.Network/frontDoors/delete | フロントドアを削除します |
> | Microsoft.Network/frontDoors/purge/action | キャッシュされたコンテンツをフロントドアから消去します |
> | Microsoft.Network/frontDoors/validateCustomDomain/action | フロントドアのフロントエンド エンドポイントを検証します |
> | Microsoft.Network/frontDoors/backendPools/read | バックエンド プールを取得します |
> | Microsoft.Network/frontDoors/backendPools/write | バックエンド プールを作成または更新します |
> | Microsoft.Network/frontDoors/backendPools/delete | バックエンド プールを削除します |
> | Microsoft.Network/frontDoors/frontendEndpoints/read | フロントエンド エンドポイントを取得します |
> | Microsoft.Network/frontDoors/frontendEndpoints/write | フロントエンド エンドポイントを作成または更新します |
> | Microsoft.Network/frontDoors/frontendEndpoints/delete | フロントエンド エンドポイントを削除します |
> | Microsoft.Network/frontDoors/frontendEndpoints/enableHttps/action | フロントエンド エンドポイントでの HTTPS を有効にします |
> | Microsoft.Network/frontDoors/frontendEndpoints/disableHttps/action | フロントエンド エンドポイントでの HTTPS を無効にします |
> | Microsoft.Network/frontDoors/healthProbeSettings/read | 正常性プローブの設定を取得します |
> | Microsoft.Network/frontDoors/healthProbeSettings/write | 正常性プローブの設定を作成または更新します |
> | Microsoft.Network/frontDoors/healthProbeSettings/delete | 正常性プローブの設定を削除します |
> | Microsoft.Network/frontDoors/loadBalancingSettings/read | 負荷分散の設定を取得します |
> | Microsoft.Network/frontDoors/loadBalancingSettings/write | 負荷分散の設定を作成または更新します |
> | Microsoft.Network/frontDoors/loadBalancingSettings/delete | 負荷分散の設定を作成または更新します |
> | Microsoft.Network/frontDoors/routingRules/read | ルーティング規則を取得します |
> | Microsoft.Network/frontDoors/routingRules/write | ルーティング規則を作成または更新します |
> | Microsoft.Network/frontDoors/routingRules/delete | ルーティング規則を削除します |
> | Microsoft.Network/frontDoors/rulesEngines/read | ルール エンジンを取得します |
> | Microsoft.Network/frontDoors/rulesEngines/write | ルール エンジンを作成または更新します |
> | Microsoft.Network/frontDoors/rulesEngines/delete | ルール エンジンを削除します |
> | Microsoft.Network/frontDoorWebApplicationFirewallManagedRuleSets/read | Web アプリケーション ファイアウォールのマネージド ルール セットを取得します |
> | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/read | Web アプリケーション ファイアウォールのポリシーを取得します |
> | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/write | Web アプリケーション ファイアウォールのポリシーを作成または更新します |
> | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/delete | Web アプリケーション ファイアウォールのポリシーを削除します |
> | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/join/action | Web アプリケーション ファイアウォールのポリシーを統合します。 警告不可能です。 |
> | Microsoft.Network/ipAllocations/read | IpAllocation が取得されます |
> | Microsoft.Network/ipAllocations/write | IpAllocation が作成されるか、既存の IpAllocation が更新されます |
> | Microsoft.Network/ipAllocations/delete | IpAllocation が削除されます |
> | Microsoft.Network/ipGroups/read | IpGroup を取得します |
> | Microsoft.Network/ipGroups/write | IpGroup を作成するか、既存の IpGroup を更新します |
> | Microsoft.Network/ipGroups/validate/action | IpGroup を検証します |
> | Microsoft.Network/ipGroups/updateReferences/action | IpGroup 内の参照を更新します |
> | Microsoft.Network/ipGroups/join/action | IpGroup を結合します 警告不可能です。 |
> | Microsoft.Network/ipGroups/delete | IpGroup を削除します |
> | Microsoft.Network/loadBalancers/read | ロード バランサー定義を取得します。 |
> | Microsoft.Network/loadBalancers/write | ロード バランサーを作成するか、既存のロード バランサーを更新します。 |
> | Microsoft.Network/loadBalancers/delete | ロード バランサーを削除します。 |
> | Microsoft.Network/loadBalancers/backendAddressPools/read | ロード バランサーのバックエンド アドレス プールの定義を取得します。 |
> | Microsoft.Network/loadBalancers/backendAddressPools/write | ロード バランサーのバックエンド アドレス プールを作成するか、既存のロード バランサーのバックエンド アドレス プールを更新します |
> | Microsoft.Network/loadBalancers/backendAddressPools/delete | ロード バランサーのバックエンド アドレス プールを削除します |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | ロード バランサーのバックエンド アドレス プールを接続します。 警告不可能です。 |
> | Microsoft.Network/loadBalancers/backendAddressPools/backendPoolAddresses/read | ロード バランサーのバックエンド アドレス プールのバックエンド アドレスを一覧表示します |
> | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | ロード バランサーのフロントエンド IP 構成定義を取得します。 |
> | Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | ロード バランサーのフロントエンド IP 構成を結合します。 警告不可能です。 |
> | Microsoft.Network/loadBalancers/inboundNatPools/read | ロード バランサーの受信 NAT プールの定義を取得します。 |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | ロード バランサーの受信 NAT プールを接続します。 警告不可能です。 |
> | Microsoft.Network/loadBalancers/inboundNatRules/read | ロード バランサーの受信 NAT 規則の定義を取得します。 |
> | Microsoft.Network/loadBalancers/inboundNatRules/write | ロード バランサーの受信 NAT 規則を作成するか、既存の受信 NAT 規則を更新します。 |
> | Microsoft.Network/loadBalancers/inboundNatRules/delete | ロード バランサーの受信 NAT 規則を削除します。 |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | ロード バランサーのインバウンド NAT 規則を接続します。 警告不可能です。 |
> | Microsoft.Network/loadBalancers/loadBalancingRules/read | ロード バランサーの負荷分散規則の定義を取得します。 |
> | Microsoft.Network/loadBalancers/networkInterfaces/read | ロード バランサーの下のすべてのネットワーク インターフェイスへの参照を取得します。 |
> | Microsoft.Network/loadBalancers/outboundRules/read | ロード バランサーの送信規則の定義を取得します |
> | Microsoft.Network/loadBalancers/probes/read | ロード バランサー プローブを取得します。 |
> | Microsoft.Network/loadBalancers/probes/join/action | ロード バランサーのプローブの使用を許可します。 たとえば、このアクセス許可では、VM スケール セットの healthProbe プロパティでプローブを参照できます。 警告不可能です。 |
> | Microsoft.Network/loadBalancers/virtualMachines/read | ロード バランサーの下のすべての仮想マシンへの参照を取得します。 |
> | Microsoft.Network/localnetworkgateways/read | LocalNetworkGateway を取得します。 |
> | Microsoft.Network/localnetworkgateways/write | LocalNetworkGateway を作成するか、既存の LocalNetworkGateway を更新します。 |
> | Microsoft.Network/localnetworkgateways/delete | LocalNetworkGateway を削除します。 |
> | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | 高速ネットワークのサポートを確認します。 |
> | Microsoft.Network/locations/checkPrivateLinkServiceVisibility/action | プライベート リンク サービスの可視性を確認します |
> | Microsoft.Network/locations/bareMetalTenants/action | ベア メタル テナントを割り当てまたは検証します。 |
> | Microsoft.Network/locations/setLoadBalancerFrontendPublicIpAddresses/action | SetLoadBalancerFrontendPublicIpAddresses は、2 つのロード バランサーのフロントエンド IP 構成をターゲットにします。 IP 構成の Azure Resource Manager ID は、要求の本文で指定されます。 |
> | Microsoft.Network/locations/autoApprovedPrivateLinkServices/read | 自動承認のプライベート リンク サービスを取得します |
> | Microsoft.Network/locations/availableDelegations/read | 使用可能な委任を取得します。 |
> | Microsoft.Network/locations/availablePrivateEndpointTypes/read | 使用できるプライベート エンドポイント リソースを取得します |
> | Microsoft.Network/locations/availableServiceAliases/read | 使用可能なサービスの別名を取得します |
> | Microsoft.Network/locations/checkDnsNameAvailability/read | 指定された場所で DNS ラベルを使用できるかどうかを確認します。 |
> | Microsoft.Network/locations/operationResults/read | 非同期の POST 操作または DELETE 操作の結果を取得します。 |
> | Microsoft.Network/locations/operations/read | 非同期操作の状態を表す操作リソースを取得します。 |
> | Microsoft.Network/locations/serviceTags/read | サービス タグを取得します |
> | Microsoft.Network/locations/supportedVirtualMachineSizes/read | サポートされる仮想マシン サイズを取得します。 |
> | Microsoft.Network/locations/usages/read | リソースの使用状況メトリックを取得します。 |
> | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | 使用可能な仮想ネットワーク エンドポイント サービスの一覧を取得します。 |
> | Microsoft.Network/masterCustomIpPrefixes/read | マスター カスタム IP プレフィックスの定義を取得します |
> | Microsoft.Network/masterCustomIpPrefixes/write | マスター カスタム IP プレフィックスを作成するか、既存のマスター カスタム IP プレフィックスを更新します |
> | Microsoft.Network/masterCustomIpPrefixes/delete | マスター カスタム IP プレフィックスを削除します |
> | Microsoft.Network/natGateways/join/action | NAT Gateway を結合します |
> | Microsoft.Network/networkExperimentProfiles/read | Internet Analyzer プロファイルを取得します |
> | Microsoft.Network/networkExperimentProfiles/write | Internet Analyzer プロファイルを作成または更新します |
> | Microsoft.Network/networkExperimentProfiles/delete | Internet Analyzer プロファイルを削除します |
> | Microsoft.Network/networkExperimentProfiles/experiments/read | Internet Analyzer テストを取得します |
> | Microsoft.Network/networkExperimentProfiles/experiments/write | Internet Analyzer テストを作成または更新します |
> | Microsoft.Network/networkExperimentProfiles/experiments/delete | Internet Analyzer テストを削除します |
> | Microsoft.Network/networkExperimentProfiles/experiments/timeseries/action | Internet Analyzer テストの時系列を取得します |
> | Microsoft.Network/networkExperimentProfiles/experiments/latencyScorecard/action | Internet Analyzer テストの待機時間スコアカードを取得します |
> | Microsoft.Network/networkExperimentProfiles/preconfiguredEndpoints/read | Internet Analyzer プロファイルの事前構成済みのエンドポイントを取得します |
> | Microsoft.Network/networkIntentPolicies/read | ネットワーク インテント ポリシーの説明を取得します |
> | Microsoft.Network/networkIntentPolicies/write | ネットワーク インテント ポリシーを作成するか、既存のネットワーク インテント ポリシーを更新します |
> | Microsoft.Network/networkIntentPolicies/delete | ネットワーク インテント ポリシーを削除します |
> | Microsoft.Network/networkInterfaces/read | ネットワーク インターフェイスの定義を取得します。  |
> | Microsoft.Network/networkInterfaces/write | ネットワーク インターフェイスを作成するか、既存のネットワーク インターフェイスを更新します。  |
> | Microsoft.Network/networkInterfaces/join/action | 仮想マシンをネットワーク インターフェイスに接続します。 警告不可能です。 |
> | Microsoft.Network/networkInterfaces/delete | ネットワーク インターフェイスを削除します。 |
> | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | VM のネットワーク インターフェイスで構成されているルート テーブルを取得します。 |
> | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | VM のネットワーク インターフェイスで構成されているネットワーク セキュリティ グループを取得します。 |
> | Microsoft.Network/networkInterfaces/UpdateParentNicAttachmentOnElasticNic/action | エラスティック NIC に関連付けられている親 NIC が更新されます |
> | Microsoft.Network/networkInterfaces/ipconfigurations/read | ネットワーク インターフェイスの IP 構成定義を取得します。  |
> | Microsoft.Network/networkInterfaces/ipconfigurations/join/action | ネットワーク インターフェイスの IP 構成を結合します。 警告不可能です。 |
> | Microsoft.Network/networkInterfaces/loadBalancers/read | ネットワーク インターフェイスが含まれているすべてのロード バランサーを取得します。 |
> | Microsoft.Network/networkInterfaces/tapConfigurations/read | ネットワーク インターフェイスの TAP 構成を取得します。 |
> | Microsoft.Network/networkInterfaces/tapConfigurations/write | ネットワーク インターフェイスの TAP 構成を作成するか、または既存のネットワーク インターフェイスの TAP 構成を更新します。 |
> | Microsoft.Network/networkInterfaces/tapConfigurations/delete | ネットワーク インターフェイスの TAP 構成を削除します。 |
> | Microsoft.Network/networkProfiles/read | ネットワーク プロファイルを取得します |
> | Microsoft.Network/networkProfiles/write | ネットワーク プロファイルを作成または更新します |
> | Microsoft.Network/networkProfiles/delete | ネットワーク プロファイルを削除します |
> | Microsoft.Network/networkProfiles/setContainers/action | コンテナーを設定します |
> | Microsoft.Network/networkProfiles/removeContainers/action | コンテナーを削除します |
> | Microsoft.Network/networkProfiles/setNetworkInterfaces/action | コンテナーのネットワーク インターフェイスを設定します |
> | Microsoft.Network/networkSecurityGroups/read | ネットワーク セキュリティ グループの定義を取得します。 |
> | Microsoft.Network/networkSecurityGroups/write | ネットワーク セキュリティ グループを作成するか、既存のネットワーク セキュリティ グループを更新します。 |
> | Microsoft.Network/networkSecurityGroups/delete | ネットワーク セキュリティ グループを削除します。 |
> | Microsoft.Network/networkSecurityGroups/join/action | ネットワーク セキュリティ グループに参加します。 警告不可能です。 |
> | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | 既定のセキュリティ規則の定義を取得します。 |
> | Microsoft.Network/networkSecurityGroups/securityRules/read | セキュリティ規則の定義を取得します。 |
> | Microsoft.Network/networkSecurityGroups/securityRules/write | セキュリティ規則を作成するか、既存のセキュリティ規則を更新します。 |
> | Microsoft.Network/networkSecurityGroups/securityRules/delete | セキュリティ規則を削除します。 |
> | Microsoft.Network/networkWatchers/read | Network Watcher の定義を取得します。 |
> | Microsoft.Network/networkWatchers/write | Network Watcher を作成するか、既存の Network Watcher を更新します。 |
> | Microsoft.Network/networkWatchers/delete | Network Watcher を削除します。 |
> | Microsoft.Network/networkWatchers/configureFlowLog/action | ターゲット リソースのフロー ログを構成します。 |
> | Microsoft.Network/networkWatchers/ipFlowVerify/action | 特定の宛先との間で送受信されるパケットを許可するか拒否するかを返します。 |
> | Microsoft.Network/networkWatchers/nextHop/action | 指定されたターゲットと宛先 IP アドレスについて、次ホップの種類と次ホップの IP アドレスを返します。 |
> | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | リソースのフロー ログの状態を取得します。 |
> | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | 実行済みまたは現在実行中のトラブルシューティング操作のトラブルシューティングの結果を取得します。 |
> | Microsoft.Network/networkWatchers/securityGroupView/action | VM に適用されている構成済みの有効なネットワーク セキュリティ グループ規則を表示します。 |
> | Microsoft.Network/networkWatchers/networkConfigurationDiagnostic/action | ネットワーク構成の診断。 |
> | Microsoft.Network/networkWatchers/queryConnectionMonitors/action | 指定されたエンドポイント間の接続の監視を一括クエリします |
> | Microsoft.Network/networkWatchers/topology/action | リソース グループ内のリソースとその関係のネットワーク レベル ビューを取得します。 |
> | Microsoft.Network/networkWatchers/troubleshoot/action | Azure のネットワーク リソースでトラブルシューティングを開始します。 |
> | Microsoft.Network/networkWatchers/connectivityCheck/action | 仮想マシンから、別の VM や任意のリモート サーバーなどの指定されたエンドポイントまでの、直接 TCP 接続が確立されたかどうかを確認します。 |
> | Microsoft.Network/networkWatchers/azureReachabilityReport/action | 指定された場所から Azure リージョンまでのインターネット サービス プロバイダーの相対待機時間スコアを返します。 |
> | Microsoft.Network/networkWatchers/availableProvidersList/action | 指定された Azure リージョンで利用可能なすべてのインターネット サービス プロバイダーを返します。 |
> | Microsoft.Network/networkWatchers/connectionMonitors/start/action | 指定されたエンドポイント間の接続の監視を開始します。 |
> | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | 指定されたエンドポイント間の監視接続を停止/一時停止します。 |
> | Microsoft.Network/networkWatchers/connectionMonitors/query/action | 指定されたエンドポイント間の接続の監視をクエリします。 |
> | Microsoft.Network/networkWatchers/connectionMonitors/read | 接続モニターの詳細を取得します。 |
> | Microsoft.Network/networkWatchers/connectionMonitors/write | 接続モニターを作成します。 |
> | Microsoft.Network/networkWatchers/connectionMonitors/delete | 接続モニターを削除します。 |
> | Microsoft.Network/networkWatchers/flowLogs/read | フロー ログの詳細を取得します |
> | Microsoft.Network/networkWatchers/flowLogs/write | フロー ログを作成します |
> | Microsoft.Network/networkWatchers/flowLogs/delete | フロー ログを削除します |
> | Microsoft.Network/networkWatchers/lenses/start/action | 指定されたエンドポイントでネットワーク トラフィックの監視を開始します。 |
> | Microsoft.Network/networkWatchers/lenses/stop/action | 指定されたエンドポイントでのネットワーク トラフィックの監視を停止/一時停止します。 |
> | Microsoft.Network/networkWatchers/lenses/query/action | 指定されたエンドポイントでのネットワーク トラフィックの監視をクエリします。 |
> | Microsoft.Network/networkWatchers/lenses/read | レンズの詳細を取得します。 |
> | Microsoft.Network/networkWatchers/lenses/write | レンズを作成します。 |
> | Microsoft.Network/networkWatchers/lenses/delete | レンズを削除します。 |
> | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | パケット キャプチャ リソースのプロパティと状態に関する情報を取得します。 |
> | Microsoft.Network/networkWatchers/packetCaptures/stop/action | 実行中のパケット キャプチャ セッションを停止します。 |
> | Microsoft.Network/networkWatchers/packetCaptures/read | パケット キャプチャ定義を取得します。 |
> | Microsoft.Network/networkWatchers/packetCaptures/write | パケット キャプチャを作成します。 |
> | Microsoft.Network/networkWatchers/packetCaptures/delete | パケット キャプチャを削除します。 |
> | Microsoft.Network/networkWatchers/pingMeshes/start/action | 指定された VM 間の PingMesh を開始します。 |
> | Microsoft.Network/networkWatchers/pingMeshes/stop/action | 指定された VM 間の PingMesh を停止します。 |
> | Microsoft.Network/networkWatchers/pingMeshes/read | PingMesh の詳細を取得します。 |
> | Microsoft.Network/networkWatchers/pingMeshes/write | PingMesh を作成します。 |
> | Microsoft.Network/networkWatchers/pingMeshes/delete | PingMesh を削除します。 |
> | Microsoft.Network/operations/read | 使用可能な操作を取得します。 |
> | Microsoft.Network/p2sVpnGateways/read | P2SVpnGateway を取得します。 |
> | Microsoft.Network/p2sVpnGateways/write | P2SVpnGateway を設定します。 |
> | Microsoft.Network/p2sVpnGateways/delete | P2SVpnGateway を削除します。 |
> | microsoft.network/p2sVpnGateways/reset/action | P2SVpnGateway をリセットします |
> | Microsoft.Network/p2sVpnGateways/generatevpnprofile/action | P2SVpnGateway の VPN プロファイルを生成します |
> | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealth/action | P2SVpnGateway の P2S Vpn 接続の状態を取得します |
> | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealthdetailed/action | P2SVpnGateway の詳細な P2S Vpn 接続の正常性を取得します |
> | Microsoft.Network/p2sVpnGateways/disconnectp2svpnconnections/action | P2S VPN 接続を切断します |
> | Microsoft.Network/privateDnsOperationResults/read | プライベート DNS 操作の結果を取得します |
> | Microsoft.Network/privateDnsOperationStatuses/read | プライベート DNS 操作の状態を取得します |
> | Microsoft.Network/privateDnsZones/read | プライベート DNS ゾーンのプロパティを JSON 形式で取得します。 このコマンドでは、プライベート DNS ゾーンがリンクされている仮想ネットワーク、またはゾーン内のレコード セットは取得されません。 |
> | Microsoft.Network/privateDnsZones/write | リソース グループ内のプライベート DNS ゾーンを作成または更新します。 このコマンドを使用して、ゾーン内の仮想ネットワーク リンクまたはレコード セットを作成または更新することはできません。 |
> | Microsoft.Network/privateDnsZones/delete | プライベート DNS ゾーンを削除します。 |
> | Microsoft.Network/privateDnsZones/join/action | プライベート DNS ゾーンに参加します |
> | Microsoft.Network/privateDnsZones/A/read | プライベート DNS ゾーン内の "A" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、レコードの一覧と、TTL、タグ、および ETag が含まれます。 |
> | Microsoft.Network/privateDnsZones/A/write | プライベート DNS ゾーン内の "A" タイプのレコード セットを作成または更新します。 レコード セットの現在のレコードが指定されたレコードに置き換えられます。 |
> | Microsoft.Network/privateDnsZones/A/delete | 指定された名前の "A" タイプのレコード セットをプライベート DNS ゾーンから削除します。 |
> | Microsoft.Network/privateDnsZones/AAAA/read | プライベート DNS ゾーン内の "AAAA" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、レコードの一覧と、TTL、タグ、および ETag が含まれます。 |
> | Microsoft.Network/privateDnsZones/AAAA/write | プライベート DNS ゾーン内の "AAAA" タイプのレコード セットを作成または更新します。 レコード セットの現在のレコードが指定されたレコードに置き換えられます。 |
> | Microsoft.Network/privateDnsZones/AAAA/delete | 指定された名前の "AAAA" タイプのレコード セットをプライベート DNS ゾーンから削除します。 |
> | Microsoft.Network/privateDnsZones/ALL/read | 各タイプのプライベート DNS レコード セットを取得します |
> | Microsoft.Network/privateDnsZones/CNAME/read | プライベート DNS ゾーン内の "CNAME" タイプのレコード セットを JSON 形式で取得します。 |
> | Microsoft.Network/privateDnsZones/CNAME/write | プライベート DNS ゾーン内の "CNAME" タイプのレコード セットを作成または更新します。 |
> | Microsoft.Network/privateDnsZones/CNAME/delete | 指定された名前の "CNAME" タイプのレコード セットをプライベート DNS ゾーンから削除します。 |
> | Microsoft.Network/privateDnsZones/MX/read | プライベート DNS ゾーン内の "MX" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、レコードの一覧と、TTL、タグ、および ETag が含まれます。 |
> | Microsoft.Network/privateDnsZones/MX/write | プライベート DNS ゾーン内の "MX" タイプのレコード セットを作成または更新します。 レコード セットの現在のレコードが指定されたレコードに置き換えられます。 |
> | Microsoft.Network/privateDnsZones/MX/delete | 指定された名前の "MX" タイプのレコード セットをプライベート DNS ゾーンから削除します。 |
> | Microsoft.Network/privateDnsZones/PTR/read | プライベート DNS ゾーン内の "PTR" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、レコードの一覧と、TTL、タグ、および ETag が含まれます。 |
> | Microsoft.Network/privateDnsZones/PTR/write | プライベート DNS ゾーン内の "PTR" タイプのレコード セットを作成または更新します。 レコード セットの現在のレコードが指定されたレコードに置き換えられます。 |
> | Microsoft.Network/privateDnsZones/PTR/delete | 指定された名前の "PTR" タイプのレコード セットをプライベート DNS ゾーンから削除します。 |
> | Microsoft.Network/privateDnsZones/recordsets/read | 各タイプのプライベート DNS レコード セットを取得します |
> | Microsoft.Network/privateDnsZones/SOA/read | プライベート DNS ゾーン内の "SOA" タイプのレコード セットを JSON 形式で取得します。 |
> | Microsoft.Network/privateDnsZones/SOA/write | プライベート DNS ゾーン内の "SOA" タイプのレコード セットを更新します。 |
> | Microsoft.Network/privateDnsZones/SRV/read | プライベート DNS ゾーン内の "SRV" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、レコードの一覧と、TTL、タグ、および ETag が含まれます。 |
> | Microsoft.Network/privateDnsZones/SRV/write | プライベート DNS ゾーン内の "SRV" タイプのレコード セットを作成または更新します。 レコード セットの現在のレコードが指定されたレコードに置き換えられます。 |
> | Microsoft.Network/privateDnsZones/SRV/delete | 指定された名前の "SRV" タイプのレコード セットをプライベート DNS ゾーンから削除します。 |
> | Microsoft.Network/privateDnsZones/TXT/read | プライベート DNS ゾーン内の "TXT" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、レコードの一覧と、TTL、タグ、および ETag が含まれます。 |
> | Microsoft.Network/privateDnsZones/TXT/write | プライベート DNS ゾーン内の "TXT" タイプのレコード セットを作成または更新します。 レコード セットの現在のレコードが指定されたレコードに置き換えられます。 |
> | Microsoft.Network/privateDnsZones/TXT/delete | 指定された名前の "TXT" タイプのレコード セットをプライベート DNS ゾーンから削除します。 |
> | Microsoft.Network/privateDnsZones/virtualNetworkLinks/read | 仮想ネットワークのプロパティへのプライベート DNS ゾーンのリンクを JSON 形式で取得します。 |
> | Microsoft.Network/privateDnsZones/virtualNetworkLinks/write | 仮想ネットワークへのプライベート DNS ゾーンのリンクを作成または更新します。 |
> | Microsoft.Network/privateDnsZones/virtualNetworkLinks/delete | 仮想ネットワークへのプライベート DNS ゾーンのリンクを削除します |
> | Microsoft.Network/privateEndpointRedirectMaps/read | プライベート エンドポイントの RedirectMap を取得します |
> | Microsoft.Network/privateEndpointRedirectMaps/write | 新しいプライベート エンドポイントの RedirectMap を作成するか、既存のプライベート エンドポイントの RedirectMap を更新します |
> | Microsoft.Network/privateEndpoints/read | プライベート エンドポイント リソースを取得します |
> | Microsoft.Network/privateEndpoints/write | 新しいプライベート エンドポイントを作成するか、または既存のプライベート エンドポイントを更新します。 |
> | Microsoft.Network/privateEndpoints/delete | プライベート エンドポイント リソースを削除します。 |
> | Microsoft.Network/privateEndpoints/privateDnsZoneGroups/read | プライベート DNS ゾーン グループを取得します |
> | Microsoft.Network/privateEndpoints/privateDnsZoneGroups/write | プライベート DNS ゾーン グループを設定します |
> | Microsoft.Network/privateLinkServices/read | プライベート リンクのサービス リソースを取得します。 |
> | Microsoft.Network/privateLinkServices/write | 新しいプライベート リンク サービスを作成するか、または既存のプライベート リンク サービスを更新します。 |
> | Microsoft.Network/privateLinkServices/delete | プライベート リンクのサービス リソースを削除します。 |
> | Microsoft.Network/privateLinkServices/privateEndpointConnections/read | プライベート エンドポイント接続の定義を取得します。 |
> | Microsoft.Network/privateLinkServices/privateEndpointConnections/write | 新しいプライベート エンドポイント接続を作成するか、または既存のプライベート エンドポイント接続を更新します。 |
> | Microsoft.Network/privateLinkServices/privateEndpointConnections/delete | プライベート エンドポイント接続を削除します。 |
> | Microsoft.Network/publicIPAddresses/read | パブリック IP アドレス定義を取得します。 |
> | Microsoft.Network/publicIPAddresses/write | パブリック IP アドレスを作成するか、既存のパブリック IP アドレスを更新します。  |
> | Microsoft.Network/publicIPAddresses/delete | パブリック IP アドレスを削除します。 |
> | Microsoft.Network/publicIPAddresses/join/action | パブリック IP アドレスに接続します。 警告不可能です。 |
> | Microsoft.Network/publicIPPrefixes/read | パブリック IP プレフィックス定義を取得します |
> | Microsoft.Network/publicIPPrefixes/write | パブリック IP プレフィックスを作成するか、既存のパブリック IP プレフィックスを更新します |
> | Microsoft.Network/publicIPPrefixes/delete | パブリック IP プレフィックスを削除します |
> | Microsoft.Network/publicIPPrefixes/join/action | PublicIPPrefix を結合します。 警告不可能です。 |
> | Microsoft.Network/routeFilters/read | ルート フィルター定義を取得します。 |
> | Microsoft.Network/routeFilters/join/action | ルート フィルターを結合します。 警告不可能です。 |
> | Microsoft.Network/routeFilters/delete | ルート フィルター定義を削除します。 |
> | Microsoft.Network/routeFilters/write | ルート フィルターを作成するか、既存のルート フィルターを更新します。 |
> | Microsoft.Network/routeFilters/routeFilterRules/read | ルート フィルター規則の定義を取得します。 |
> | Microsoft.Network/routeFilters/routeFilterRules/write | ルート フィルター規則を作成するか、既存のルート フィルター規則を更新します。 |
> | Microsoft.Network/routeFilters/routeFilterRules/delete | ルート フィルター規則の定義を削除します。 |
> | Microsoft.Network/routeTables/read | ルート テーブルの定義を取得します。 |
> | Microsoft.Network/routeTables/write | ルート テーブルを作成するか、既存のルート テーブルを更新します。 |
> | Microsoft.Network/routeTables/delete | ルート テーブルの定義を削除します。 |
> | Microsoft.Network/routeTables/join/action | ルート テーブルを結合します。 警告不可能です。 |
> | Microsoft.Network/routeTables/routes/read | ルート定義を取得します。 |
> | Microsoft.Network/routeTables/routes/write | ルートを作成するか、既存のルートを更新します。 |
> | Microsoft.Network/routeTables/routes/delete | ルート定義を削除します。 |
> | Microsoft.Network/securityPartnerProviders/read | SecurityPartnerProvider を取得します |
> | Microsoft.Network/securityPartnerProviders/write | SecurityPartnerProvider を作成するか、既存の SecurityPartnerProvider を更新します |
> | Microsoft.Network/securityPartnerProviders/validate/action | SecurityPartnerProvider を検証します |
> | Microsoft.Network/securityPartnerProviders/updateReferences/action | SecurityPartnerProvider の参照を更新します |
> | Microsoft.Network/securityPartnerProviders/join/action | SecurityPartnerProvider を結合します 警告不可能です。 |
> | Microsoft.Network/securityPartnerProviders/delete | SecurityPartnerProvider を削除します |
> | Microsoft.Network/serviceEndpointPolicies/read | サービス エンドポイント ポリシーの説明を取得します。 |
> | Microsoft.Network/serviceEndpointPolicies/write | サービス エンドポイント ポリシーを作成するか、既存のサービス エンドポイント ポリシーを更新します。 |
> | Microsoft.Network/serviceEndpointPolicies/delete | サービス エンドポイント ポリシーを削除します。 |
> | Microsoft.Network/serviceEndpointPolicies/join/action | サービス エンドポイント ポリシーを結合します。 警告不可能です。 |
> | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | サブネットをサービス エンドポイント ポリシーに結合します。 警告不可能です。 |
> | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | サービス エンドポイント ポリシーの定義の説明を取得します。 |
> | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | サービス エンドポイント ポリシーの定義を作成するか、既存のサービス エンドポイント ポリシーの定義を更新します。 |
> | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | サービス エンドポイント ポリシーの定義を削除します。 |
> | Microsoft.Network/trafficManagerGeographicHierarchies/read | 地理的トラフィック ルーティング方法で使用できるリージョンを含む、Traffic Manager の地理的階層を取得します。 |
> | Microsoft.Network/trafficManagerProfiles/read | Traffic Manager プロファイル構成を取得します。 これには、DNS 設定、トラフィック ルーティング設定、エンドポイント監視設定、この Traffic Manager プロファイルによってルーティングされるエンドポイントの一覧が含まれます。 |
> | Microsoft.Network/trafficManagerProfiles/write | Traffic Manager プロファイルを作成するか、既存の Traffic Manager プロファイルの構成を変更します。<br>これには、プロファイルの有効化または無効化と、DNS 設定、トラフィック ルーティング設定、またはエンドポイント監視設定の変更が含まれます。<br>Traffic Manager プロファイルによってルーティングされるエンドポイントを追加、削除、有効化、または無効化できます。 |
> | Microsoft.Network/trafficManagerProfiles/delete | Traffic Manager プロファイルを削除します。 Traffic Manager プロファイルに関連付けられているすべての設定が失われ、プロファイルを使用してトラフィックをルーティングすることはできなくなります。 |
> | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Traffic Manager プロファイルに属する Azure エンドポイントを取得します (その Azure エンドポイントのすべてのプロパティなど)。 |
> | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | 既存の Traffic Manager プロファイルに新しい Azure エンドポイントを追加するか、その Traffic Manager プロファイルの既存の Azure エンドポイントのプロパティを更新します。 |
> | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | 既存の Traffic Manager プロファイルから Azure エンドポイントを削除します。 Traffic Manager は、削除された Azure エンドポイントへのトラフィックのルーティングを停止します。 |
> | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | Traffic Manager プロファイルに属する外部エンドポイントを取得します (その外部エンドポイントのすべてのプロパティなど)。 |
> | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | 既存の Traffic Manager プロファイルに新しい外部エンドポイントを追加するか、その Traffic Manager プロファイルの既存の外部エンドポイントのプロパティを更新します。 |
> | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | 既存の Traffic Manager プロファイルから外部エンドポイントを削除します。 Traffic Manager は、削除された外部エンドポイントへのトラフィックのルーティングを停止します。 |
> | Microsoft.Network/trafficManagerProfiles/heatMaps/read | 場所およびソース IP 別にクエリ数と待機時間のデータを含む指定された Traffic Manager プロファイルに対する Traffic Manager ヒート マップを取得します。 |
> | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | Traffic Manager プロファイルに属する入れ子になったエンドポイントを取得します (その入れ子になったエンドポイントのすべてのプロパティなど)。 |
> | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | 既存の Traffic Manager プロファイルに新しい入れ子になったエンドポイントを追加するか、その Traffic Manager プロファイルの既存の入れ子になったエンドポイントのプロパティを更新します。 |
> | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | 既存の Traffic Manager プロファイルから入れ子になったエンドポイントを削除します。 Traffic Manager は、削除された入れ子になったエンドポイントへのトラフィックのルーティングを停止します。 |
> | Microsoft.Network/trafficManagerUserMetricsKeys/read | リアルタイムのユーザー メトリック収集に使用されるサブスクリプション レベルのキーを取得します。 |
> | Microsoft.Network/trafficManagerUserMetricsKeys/write | リアルタイムのユーザー メトリック収集に使用されるサブスクリプション レベルのキーを新しく作成します。 |
> | Microsoft.Network/trafficManagerUserMetricsKeys/delete | リアルタイムのユーザー メトリック収集に使用されるサブスクリプション レベルのキーを削除します。 |
> | Microsoft.Network/virtualHubs/delete | 仮想ハブを削除します。 |
> | Microsoft.Network/virtualHubs/read | 仮想ハブを取得します。 |
> | Microsoft.Network/virtualHubs/write | 仮想ハブを作成または更新します。 |
> | Microsoft.Network/virtualHubs/effectiveRoutes/action | 仮想ハブで構成された有効なルートを取得します |
> | Microsoft.Network/virtualHubs/bgpConnections/read | 仮想ハブのハブ BGP 接続の子リソースを取得します |
> | Microsoft.Network/virtualHubs/bgpConnections/write | 仮想ハブのハブ BGP 接続の子リソースを作成または更新します |
> | Microsoft.Network/virtualHubs/bgpConnections/delete | 仮想ハブのハブ BGP 接続の子リソースを削除します |
> | Microsoft.Network/virtualHubs/bgpConnections/advertisedRoutes/action | VirtualRouter でアドバタイズされたルートを取得します |
> | Microsoft.Network/virtualHubs/bgpConnections/learnedRoutes/action | VirtualRouter で学習されたルートを取得します |
> | Microsoft.Network/virtualHubs/hubRouteTables/read | 仮想ハブのルート テーブルの子リソースを取得します |
> | Microsoft.Network/virtualHubs/hubRouteTables/write | 仮想ハブのルート テーブルの子リソースを作成または更新します |
> | Microsoft.Network/virtualHubs/hubRouteTables/delete | 仮想ハブのルート テーブルの子リソースを削除します |
> | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | HubVirtualNetworkConnection を取得します。 |
> | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | HubVirtualNetworkConnection を作成または更新します。 |
> | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | HubVirtualNetworkConnection を削除します。 |
> | Microsoft.Network/virtualHubs/ipConfigurations/read | 仮想ハブのハブ IpConfiguration の子リソースを取得します |
> | Microsoft.Network/virtualHubs/ipConfigurations/write | 仮想ハブのハブ IpConfiguration の子リソースを作成または更新します |
> | Microsoft.Network/virtualHubs/ipConfigurations/delete | 仮想ハブのハブ IpConfiguration の子リソースを削除します |
> | Microsoft.Network/virtualHubs/routeTables/read | VirtualHubRouteTableV2 を取得します |
> | Microsoft.Network/virtualHubs/routeTables/write | VirtualHubRouteTableV2 を作成または更新します |
> | Microsoft.Network/virtualHubs/routeTables/delete | VirtualHubRouteTableV2 を削除します |
> | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | サポートされている VPN デバイスを一覧表示します。 |
> | Microsoft.Network/virtualNetworkGateways/read | VirtualNetworkGateway を取得します。 |
> | Microsoft.Network/virtualNetworkGateways/write | VirtualNetworkGateway を作成または更新します。 |
> | Microsoft.Network/virtualNetworkGateways/delete | virtualNetworkGateway を削除します。 |
> | microsoft.network/virtualnetworkgateways/generatevpnclientpackage/action | virtualNetworkGateway の VpnClient パッケージを生成します。 |
> | microsoft.network/virtualnetworkgateways/generatevpnprofile/action | VirtualNetworkGateway の VpnProfile パッケージを生成します。 |
> | microsoft.network/virtualnetworkgateways/getvpnclientconnectionhealth/action | VirtualNetworkGateway の VPN クライアント別接続状態を取得します |
> | microsoft.network/virtualnetworkgateways/disconnectvirtualnetworkgatewayvpnconnections/action | 仮想ネットワーク ゲートウェイの VPN 接続を切断します |
> | microsoft.network/virtualnetworkgateways/getvpnprofilepackageurl/action | 事前生成済みの VPN クライアント プロファイル パッケージの URL を取得します。 |
> | microsoft.network/virtualnetworkgateways/setvpnclientipsecparameters/action | VirtualNetworkGateway P2S クライアントの Vpnclient Ipsec パラメーターを設定します。 |
> | microsoft.network/virtualnetworkgateways/getvpnclientipsecparameters/action | VirtualNetworkGateway P2S クライアントの Vpnclient Ipsec パラメーターを取得します。 |
> | microsoft.network/virtualnetworkgateways/resetvpnclientsharedkey/action | VirtualNetworkGateway P2S クライアント用の Vpnclient 共有キーをリセットします。 |
> | microsoft.network/virtualnetworkgateways/reset/action | virtualNetworkGateway をリセットします。 |
> | microsoft.network/virtualnetworkgateways/getadvertisedroutes/action | virtualNetworkGateway でアドバタイズされたルートを取得します。 |
> | microsoft.network/virtualnetworkgateways/getbgppeerstatus/action | virtualNetworkGateway の BGP ピアの状態を取得します。 |
> | microsoft.network/virtualnetworkgateways/getlearnedroutes/action | virtualnetworkgateway で学習されたルートを取得します。 |
> | microsoft.network/virtualnetworkgateways/startpacketcapture/action | Virtual Network ゲートウェイのパケット キャプチャを開始します。 |
> | microsoft.network/virtualnetworkgateways/stoppacketcapture/action | Virtual Network ゲートウェイのパケット キャプチャを停止します。 |
> | microsoft.network/virtualnetworkgateways/connections/read | VirtualNetworkGatewayConnection を取得します。 |
> | microsoft.network/virtualNetworkGateways/natRules/read | NAT ルール リソースを取得します |
> | microsoft.network/virtualNetworkGateways/natRules/write | NAT ルール リソースを設定します |
> | microsoft.network/virtualNetworkGateways/natRules/delete | NAT ルール リソースを削除します |
> | Microsoft.Network/virtualNetworks/read | 仮想ネットワークの定義を取得します。 |
> | Microsoft.Network/virtualNetworks/write | 仮想ネットワークを作成するか、既存の仮想ネットワークを更新します。 |
> | Microsoft.Network/virtualNetworks/delete | 仮想ネットワークを削除します。 |
> | Microsoft.Network/virtualNetworks/joinLoadBalancer/action | ロード バランサーを仮想ネットワークに結合します |
> | Microsoft.Network/virtualNetworks/peer/action | 仮想ネットワークを別の仮想ネットワークとピアリングします。 |
> | Microsoft.Network/virtualNetworks/join/action | 仮想ネットワークに参加します。 警告不可能です。 |
> | Microsoft.Network/virtualNetworks/BastionHosts/action | 仮想ネットワーク内の踏み台ホストの参照を取得します。 |
> | Microsoft.Network/virtualNetworks/bastionHosts/default/action | 仮想ネットワーク内の踏み台ホストの参照を取得します。 |
> | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | 指定された仮想ネットワークで IP アドレスを使用できるかどうかを確認します。 |
> | Microsoft.Network/virtualNetworks/privateDnsZoneLinks/read | 仮想ネットワークのプロパティへのプライベート DNS ゾーンのリンクを JSON 形式で取得します。 |
> | Microsoft.Network/virtualNetworks/subnets/read | 仮想ネットワーク サブネットの定義を取得します。 |
> | Microsoft.Network/virtualNetworks/subnets/write | 仮想ネットワーク サブネットを作成するか、既存の仮想ネットワーク サブネットを更新します。 |
> | Microsoft.Network/virtualNetworks/subnets/delete | 仮想ネットワーク サブネットを削除します。 |
> | Microsoft.Network/virtualNetworks/subnets/joinLoadBalancer/action | ロード バランサーを仮想ネットワーク サブネットに結合します |
> | Microsoft.Network/virtualNetworks/subnets/join/action | 仮想ネットワークに参加します。 警告不可能です。 |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | ストレージ アカウントや SQL Database などのリソースをサブネットに結合します。 警告不可能です。 |
> | Microsoft.Network/virtualNetworks/subnets/prepareNetworkPolicies/action | 必要なネットワーク ポリシーを適用してサブネットを準備します。 |
> | Microsoft.Network/virtualNetworks/subnets/unprepareNetworkPolicies/action | 適用されたネットワーク ポリシーを削除してサブネットの準備を解除します |
> | Microsoft.Network/virtualNetworks/subnets/contextualServiceEndpointPolicies/read | コンテキスト サービス エンドポイント ポリシーの定義を取得します |
> | Microsoft.Network/virtualNetworks/subnets/contextualServiceEndpointPolicies/write | コンテキスト サービス エンドポイント ポリシーを作成するか、既存のコンテキスト サービス エンドポイント ポリシーを更新します |
> | Microsoft.Network/virtualNetworks/subnets/contextualServiceEndpointPolicies/delete | コンテキスト サービス エンドポイント ポリシーの定義を削除します |
> | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | 仮想ネットワーク サブネットのすべての仮想マシンへの参照を取得します。 |
> | Microsoft.Network/virtualNetworks/usages/read | 仮想ネットワークの各サブネットの IP の使用状況を取得します。 |
> | Microsoft.Network/virtualNetworks/virtualMachines/read | 仮想ネットワークのすべての仮想マシンへの参照を取得します。 |
> | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | 仮想ネットワーク ピアリングの定義を取得します。 |
> | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | 仮想ネットワーク ピアリングを作成するか、既存の仮想ネットワーク ピアリングを更新します。 |
> | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | 仮想ネットワーク ピアリングを削除します。 |
> | Microsoft.Network/virtualNetworkTaps/read | 仮想ネットワーク タップを取得します。 |
> | Microsoft.Network/virtualNetworkTaps/join/action | 仮想ネットワーク タップに参加します。 警告不可能です。 |
> | Microsoft.Network/virtualNetworkTaps/delete | 仮想ネットワーク タップを削除します。 |
> | Microsoft.Network/virtualNetworkTaps/write | 仮想ネットワーク タップを作成または更新します。 |
> | Microsoft.Network/virtualRouters/read | VirtualRouter を取得します |
> | Microsoft.Network/virtualRouters/write | VirtualRouter を作成するか、既存の VirtualRouter を更新します |
> | Microsoft.Network/virtualRouters/delete | VirtualRouter を削除します |
> | Microsoft.Network/virtualRouters/join/action | VirtualRouter に参加します。 警告不可能です。 |
> | Microsoft.Network/virtualRouters/peerings/read | VirtualRouterPeering を取得します |
> | Microsoft.Network/virtualRouters/peerings/write | VirtualRouterPeering を作成するか、既存の VirtualRouterPeering を更新します |
> | Microsoft.Network/virtualRouters/peerings/delete | VirtualRouterPeering を削除します |
> | Microsoft.Network/virtualWans/delete | 仮想 WAN を削除します。 |
> | Microsoft.Network/virtualWans/read | 仮想 WAN を取得します。 |
> | Microsoft.Network/virtualWans/write | 仮想 WAN ハブを作成または更新します。 |
> | Microsoft.Network/virtualwans/vpnconfiguration/action | VPN 構成を取得します。 |
> | Microsoft.Network/virtualwans/vpnServerConfigurations/action | VirtualWanVpnServerConfigurations を取得します |
> | Microsoft.Network/virtualwans/generateVpnProfile/action | VirtualWanVpnServerConfiguration VpnProfile を生成します |
> | Microsoft.Network/virtualWans/p2sVpnServerConfigurations/read | 仮想 WAN の P2SVpnServerConfiguration を取得します |
> | Microsoft.network/virtualWans/p2sVpnServerConfigurations/write | 仮想 WAN P2SVpnServerConfiguration を作成するか、または既存の仮想 WAN P2SVpnServerConfiguration を更新します |
> | Microsoft.network/virtualWans/p2sVpnServerConfigurations/delete | 仮想 WAN の P2SVpnServerConfiguration を削除します |
> | Microsoft.Network/virtualwans/supportedSecurityProviders/read | サポートされている VirtualWan セキュリティ プロバイダーを取得します。 |
> | Microsoft.Network/virtualWans/virtualHubs/read | 仮想 WAN を参照するすべての仮想ハブを取得します。 |
> | Microsoft.Network/virtualWans/vpnSites/read | 仮想 WAN を参照するすべての VPN サイトを取得します。 |
> | Microsoft.Network/vpnGateways/read | VpnGateway を取得します。 |
> | Microsoft.Network/vpnGateways/write | VpnGateway を設定します。 |
> | Microsoft.Network/vpnGateways/delete | VpnGateway を削除します。 |
> | microsoft.network/vpngateways/reset/action | VpnGateway をリセットします |
> | microsoft.network/vpngateways/startpacketcapture/action | リソースに従って、VPN ゲートウェイのパケット キャプチャを開始します |
> | microsoft.network/vpngateways/stoppacketcapture/action | sasURL を使用して、VPN ゲートウェイのパケット キャプチャを停止します |
> | microsoft.network/vpngateways/listvpnconnectionshealth/action | VpnGateway 上のすべての接続または接続のサブセットの接続の正常性を取得します |
> | microsoft.network/vpnGateways/natRules/read | NAT ルール リソースを取得します |
> | microsoft.network/vpnGateways/natRules/write | NAT ルール リソースを設定します |
> | microsoft.network/vpnGateways/natRules/delete | NAT ルール リソースを削除します |
> | microsoft.network/vpnGateways/vpnConnections/read | VpnConnection を取得します。 |
> | microsoft.network/vpnGateways/vpnConnections/write | VpnConnection を設定します。 |
> | microsoft.network/vpnGateways/vpnConnections/delete | VpnConnection を削除します。 |
> | microsoft.network/vpnGateways/vpnConnections/startpacketcapture/action | VPN 接続で選択されたリンクのパケット キャプチャを開始します |
> | microsoft.network/vpnGateways/vpnConnections/stoppacketcapture/action | VPN 接続で選択されたリンクのパケット キャプチャを停止します |
> | microsoft.network/vpnGateways/vpnConnections/vpnLinkConnections/getikesas/action | VPN リンク接続の IKE セキュリティ アソシエーションを一覧表示します |
> | microsoft.network/vpnGateways/vpnConnections/vpnLinkConnections/resetconnection/action | vWAN の接続をリセットします |
> | microsoft.network/vpnGateways/vpnConnections/vpnLinkConnections/read | VPN リンク接続を取得します |
> | Microsoft.Network/vpnServerConfigurations/read | VpnServerConfiguration を取得します |
> | Microsoft.Network/vpnServerConfigurations/write | VpnServerConfiguration を作成または更新します |
> | Microsoft.Network/vpnServerConfigurations/delete | VpnServerConfiguration を削除します |
> | Microsoft.Network/vpnsites/read | VPN サイト リソースを取得します。 |
> | Microsoft.Network/vpnsites/write | VPN サイト リソースを作成または更新します。 |
> | Microsoft.Network/vpnsites/delete | VPN サイト リソースを削除します。 |
> | microsoft.network/vpnSites/vpnSiteLinks/read | VPN サイト リンクを取得します |

## <a name="storage"></a>ストレージ

### <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

Azure サービス:クラシック デプロイ モデルのストレージ

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.ClassicStorage/register/action | 従来のストレージに登録します。 |
> | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | ストレージ アカウントを使用できるかどうかを確認します。 |
> | Microsoft.ClassicStorage/capabilities/read | 機能を表示します。 |
> | Microsoft.ClassicStorage/checkStorageAccountAvailability/read | ストレージ アカウントの使用状況を取得します。 |
> | Microsoft.ClassicStorage/disks/read | ストレージ アカウント ディスクを返します。 |
> | Microsoft.ClassicStorage/images/read | イメージを返します。 |
> | Microsoft.ClassicStorage/images/operationstatuses/read | イメージの操作状態を取得します。 |
> | Microsoft.ClassicStorage/operations/read | 従来のストレージ操作を取得します |
> | Microsoft.ClassicStorage/osImages/read | オペレーティング システム イメージを返します。 |
> | Microsoft.ClassicStorage/osPlatformImages/read | オペレーティング システム プラットフォーム イメージを取得します。 |
> | Microsoft.ClassicStorage/publicImages/read | 仮想マシンのパブリック イメージを取得します。 |
> | Microsoft.ClassicStorage/quotas/read | サブスクリプションのクォータを取得します。 |
> | Microsoft.ClassicStorage/storageAccounts/read | 特定のアカウントのストレージ アカウントを返します。 |
> | Microsoft.ClassicStorage/storageAccounts/write | 新しいストレージ アカウントを追加します。 |
> | Microsoft.ClassicStorage/storageAccounts/delete | ストレージ アカウントを削除します。 |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | ストレージ アカウントのアクセス キーを一覧表示します。 |
> | Microsoft.ClassicStorage/storageAccounts/regenerateKey/action | ストレージ アカウントの既存のアクセス キーを再生成します。 |
> | Microsoft.ClassicStorage/storageAccounts/validateMigration/action | ストレージ アカウントの移行を検証します。 |
> | Microsoft.ClassicStorage/storageAccounts/prepareMigration/action | ストレージ アカウントの移行を準備します。 |
> | Microsoft.ClassicStorage/storageAccounts/commitMigration/action | ストレージ アカウントの移行をコミットします。 |
> | Microsoft.ClassicStorage/storageAccounts/abortMigration/action | ストレージ アカウントの移行を中止します。 |
> | Microsoft.ClassicStorage/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/read | 診断設定を取得します。 |
> | Microsoft.ClassicStorage/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/write | 診断設定を追加または変更します。 |
> | Microsoft.ClassicStorage/storageAccounts/blobServices/providers/Microsoft.Insights/metricDefinitions/read | メトリック定義を取得します。 |
> | Microsoft.ClassicStorage/storageAccounts/disks/read | ストレージ アカウント ディスクを返します。 |
> | Microsoft.ClassicStorage/storageAccounts/disks/write | ストレージ アカウント ディスクを追加します。 |
> | Microsoft.ClassicStorage/storageAccounts/disks/delete | 指定されたストレージ アカウント ディスクを削除します。 |
> | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | リソースの操作の状態を読み取ります。 |
> | Microsoft.ClassicStorage/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/read | 診断設定を取得します。 |
> | Microsoft.ClassicStorage/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/write | 診断設定を追加または変更します。 |
> | Microsoft.ClassicStorage/storageAccounts/fileServices/providers/Microsoft.Insights/metricDefinitions/read | メトリック定義を取得します。 |
> | Microsoft.ClassicStorage/storageAccounts/images/read | ストレージ アカウント イメージを返します。 (非推奨になりました。 'Microsoft.ClassicStorage/storageAccounts/vmImages' を使用してください。) |
> | Microsoft.ClassicStorage/storageAccounts/images/delete | 指定されたストレージ アカウント イメージを削除します。 (非推奨になりました。 'Microsoft.ClassicStorage/storageAccounts/vmImages' を使用してください。) |
> | Microsoft.ClassicStorage/storageAccounts/images/operationstatuses/read | ストレージ アカウント イメージの操作状態を返します。 |
> | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | リソースの操作の状態を読み取ります。 |
> | Microsoft.ClassicStorage/storageAccounts/osImages/read | ストレージ アカウント オペレーティング システム イメージを返します。 |
> | Microsoft.ClassicStorage/storageAccounts/osImages/write | 指定されたストレージ アカウント オペレーティング システム イメージを追加します。 |
> | Microsoft.ClassicStorage/storageAccounts/osImages/delete | 指定されたストレージ アカウント オペレーティング システム イメージを削除します。 |
> | Microsoft.ClassicStorage/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/read | 診断設定を取得します。 |
> | Microsoft.ClassicStorage/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/write | 診断設定を追加または変更します。 |
> | Microsoft.ClassicStorage/storageAccounts/providers/Microsoft.Insights/metricDefinitions/read | メトリック定義を取得します。 |
> | Microsoft.ClassicStorage/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/read | 診断設定を取得します。 |
> | Microsoft.ClassicStorage/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/write | 診断設定を追加または変更します。 |
> | Microsoft.ClassicStorage/storageAccounts/queueServices/providers/Microsoft.Insights/metricDefinitions/read | メトリック定義を取得します。 |
> | Microsoft.ClassicStorage/storageAccounts/services/read | 利用可能なサービスを取得します。 |
> | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | 診断設定を取得します。 |
> | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | 診断設定を追加または変更します。 |
> | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | メトリック定義を取得します。 |
> | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | メトリックを取得します。 |
> | Microsoft.ClassicStorage/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/read | 診断設定を取得します。 |
> | Microsoft.ClassicStorage/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/write | 診断設定を追加または変更します。 |
> | Microsoft.ClassicStorage/storageAccounts/tableServices/providers/Microsoft.Insights/metricDefinitions/read | メトリック定義を取得します。 |
> | Microsoft.ClassicStorage/storageAccounts/vmImages/read | 仮想マシン イメージを返します。 |
> | Microsoft.ClassicStorage/storageAccounts/vmImages/write | 指定された仮想マシン イメージを追加します。 |
> | Microsoft.ClassicStorage/storageAccounts/vmImages/delete | 指定された仮想マシン イメージを削除します。 |
> | Microsoft.ClassicStorage/storageAccounts/vmImages/operationstatuses/read | 指定された仮想マシン イメージの操作状態を取得します。 |
> | Microsoft.ClassicStorage/vmImages/read | 仮想マシン イメージを一覧表示します。 |

### <a name="microsoftdatabox"></a>Microsoft.DataBox

Azure サービス:[Azure Data Box](../databox/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.DataBox/jobs/read | 注文を一覧表示または取得します。 |
> | Microsoft.DataBox/jobs/delete | 注文を削除します。 |
> | Microsoft.DataBox/jobs/write | 注文を作成または更新します。 |
> | Microsoft.DataBox/locations/availableSkus/read | 利用可能な SKU を一覧表示または取得します。 |
> | Microsoft.DataBox/locations/operationResults/read | 操作結果を一覧表示または取得します。 |
> | Microsoft.DataBox/operations/read | 操作を一覧表示または取得します |
> | **DataAction** | **説明** |
> | Microsoft.DataBox/register/action | プロバイダー Microsoft.Databox を登録します |
> | Microsoft.DataBox/unregister/action | プロバイダー Microsoft.Databox を登録解除します |
> | Microsoft.DataBox/jobs/cancel/action | 処理中の注文を取り消します。 |
> | Microsoft.DataBox/jobs/bookShipmentPickUp/action | 返品の集配を予約できます。 |
> | Microsoft.DataBox/jobs/listCredentials/action | 注文に関連する暗号化されていない資格情報を一覧表示します。 |
> | Microsoft.DataBox/locations/validateInputs/action | このメソッドでは、すべての種類の検証が行われます。 |
> | Microsoft.DataBox/locations/validateAddress/action | 配送先住所を検証し、存在する場合には別の住所を指定します。 |
> | Microsoft.DataBox/locations/availableSkus/action | このメソッドは、使用可能な SKU の一覧を返します。 |
> | Microsoft.DataBox/locations/regionConfiguration/action | このメソッドでは、リージョンの構成が返されます。 |
> | Microsoft.DataBox/subscriptions/resourceGroups/moveResources/action | このメソッドは、リソースの移動を行います。 |
> | Microsoft.DataBox/subscriptions/resourceGroups/validateMoveResources/action | このメソッドは、リソースの移動が許可されているかどうかを検証します。 |

### <a name="microsoftdatashare"></a>Microsoft.DataShare

Azure サービス: [Azure Data Share](../data-share/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.DataShare/register/action | Data Share リソース プロバイダーのサブスクリプションを登録します。 |
> | Microsoft.DataShare/unregister/action | Data Share リソース プロバイダーのサブスクリプションを登録解除します。 |
> | Microsoft.DataShare/accounts/read | Data Share アカウントを読み取ります。 |
> | Microsoft.DataShare/accounts/write | Data Share アカウントを書き込みます。 |
> | Microsoft.DataShare/accounts/delete | Data Share アカウントを削除します。 |
> | Microsoft.DataShare/accounts/shares/read | Data Share 共有を読み取ります。 |
> | Microsoft.DataShare/accounts/shares/write | Data Share 共有を書き込みます。 |
> | Microsoft.DataShare/accounts/shares/delete | Data Share 共有を削除します。 |
> | Microsoft.DataShare/accounts/shares/listSynchronizations/action | Data Share ListSynchronization のアクションです。 |
> | Microsoft.DataShare/accounts/shares/listSynchronizationDetails/action | Data Share ListSynchronization 詳細のアクションです。 |
> | Microsoft.DataShare/accounts/shares/dataSets/read | データセットを読み取ります。 |
> | Microsoft.DataShare/accounts/shares/dataSets/write | Data Share データセットを作成します。 |
> | Microsoft.DataShare/accounts/shares/dataSets/delete | Data Share データセットを削除します。 |
> | Microsoft.DataShare/accounts/shares/invitations/read | Data Share の招待を読み取ります。 |
> | Microsoft.DataShare/accounts/shares/invitations/write | Data Share の招待を書き込みます。 |
> | Microsoft.DataShare/accounts/shares/invitations/delete | Data Share の招待を削除します。 |
> | Microsoft.DataShare/accounts/shares/operationResults/read | Data Share 共有を読み取ります。 |
> | Microsoft.DataShare/accounts/shares/providerShareSubscriptions/read | Data Share プロバイダーの ShareSubscription を読み取ります。 |
> | Microsoft.DataShare/accounts/shares/providerShareSubscriptions/revoke/action | Data Share サブスクリプションを取り消します。 |
> | Microsoft.DataShare/accounts/shares/providerShareSubscriptions/reinstate/action | Data Share サブスクリプションを復帰させます。 |
> | Microsoft.DataShare/accounts/shares/synchronizationSettings/read | Data Share の同期設定を読み取ります。 |
> | Microsoft.DataShare/accounts/shares/synchronizationSettings/write | Data Share の同期設定を書き込みます。 |
> | Microsoft.DataShare/accounts/shares/synchronizationSettings/delete | Data Share の同期設定を削除します。 |
> | Microsoft.DataShare/accounts/shareSubscriptions/cancelSynchronization/action | Data Share の同期をキャンセルします。 |
> | Microsoft.DataShare/accounts/shareSubscriptions/delete | Data Share 共有サブスクリプションを削除します。 |
> | Microsoft.DataShare/accounts/shareSubscriptions/listSourceShareSynchronizationSettings/action | Data Share ソース共有の SynchronizationSettings を一覧表示します。 |
> | Microsoft.DataShare/accounts/shareSubscriptions/listSynchronizationDetails/action | Data Share の同期の詳細を一覧表示します。 |
> | Microsoft.DataShare/accounts/shareSubscriptions/listSynchronizations/action | Data Share の同期を一覧表示します。 |
> | Microsoft.DataShare/accounts/shareSubscriptions/read | Data Share の ShareSubscription を読み取ります。 |
> | Microsoft.DataShare/accounts/shareSubscriptions/synchronize/action | Data Share の同期操作を初期化します。 |
> | Microsoft.DataShare/accounts/shareSubscriptions/write | Data Share の ShareSubscription を書き込みます。 |
> | Microsoft.DataShare/accounts/shareSubscriptions/consumerSourceDataSets/read | Data Share コンシューマー ソース データセットを読み取ります。 |
> | Microsoft.DataShare/accounts/shareSubscriptions/dataSetMappings/delete | Data Share DataSetMapping を削除します。 |
> | Microsoft.DataShare/accounts/shareSubscriptions/dataSetMappings/write | Data Share DataSetMapping を書き込みます。 |
> | Microsoft.DataShare/accounts/shareSubscriptions/dataSetMappings/read | Data Share DataSetMapping を読み取ります。 |
> | Microsoft.DataShare/accounts/shareSubscriptions/operationResults/read | Data Share ShareSubscription の長期操作の状態を読み取ります。 |
> | Microsoft.DataShare/accounts/shareSubscriptions/shareSubscriptionSynchronizations/read | Data Share 共有サブスクリプションの同期を読み取ります。 |
> | Microsoft.DataShare/accounts/shareSubscriptions/synchronizationOperationResults/read | Data Share の同期操作の結果を読み取ります。 |
> | Microsoft.DataShare/accounts/shareSubscriptions/triggers/read | Data Share トリガーを読み取ります。 |
> | Microsoft.DataShare/accounts/shareSubscriptions/triggers/write | Data Share トリガーを書き込みます。 |
> | Microsoft.DataShare/accounts/shareSubscriptions/triggers/delete | Data Share トリガーを削除します。 |
> | Microsoft.DataShare/listInvitations/read | テナント レベルで招待を読み取ります。 |
> | Microsoft.DataShare/locations/rejectInvitation/action | Data Share の招待を拒否します。 |
> | Microsoft.DataShare/locations/consumerInvitations/read | Data Share のコンシューマー招待を取得します。 |
> | Microsoft.DataShare/locations/operationResults/read | Data Share がサポートされている場所を読み取ります。 |
> | Microsoft.DataShare/operations/read | Data Share リソース プロバイダーで使用可能なすべての操作を読み取ります。 |

### <a name="microsoftimportexport"></a>Microsoft.ImportExport

Azure サービス:[Azure Import/Export](../import-export/storage-import-export-service.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.ImportExport/register/action | Import/Export リソース プロバイダーにサブスクリプションを登録し、インポート/エクスポート ジョブを作成できるようにします。 |
> | Microsoft.ImportExport/jobs/write | 指定されたパラメーターを使用してジョブを作成するか、指定されたジョブのプロパティまたはタグを更新します。 |
> | Microsoft.ImportExport/jobs/read | 指定されたジョブのプロパティを取得するか、ジョブの一覧を返します。 |
> | Microsoft.ImportExport/jobs/listBitLockerKeys/action | 指定されたジョブの BitLocker キーを取得します。 |
> | Microsoft.ImportExport/jobs/delete | 既存のジョブを削除します。 |
> | Microsoft.ImportExport/locations/read | 指定された場所のプロパティを取得するか、場所の一覧を返します。 |
> | Microsoft.ImportExport/operations/read | リソース プロバイダーでサポートされている操作を取得します。 |

### <a name="microsoftnetapp"></a>Microsoft.NetApp

Azure サービス:[Azure NetApp Files](../azure-netapp-files/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.NetApp/register/action | サブスクリプション登録アクション |
> | Microsoft.NetApp/unregister/action | Microsoft.NetApp リソース プロバイダーのサブスクリプションを登録解除します |
> | Microsoft.NetApp/locations/read | 利用可否の確認リソースを読み取ります。 |
> | Microsoft.NetApp/locations/checknameavailability/action | リソース名を使用できるかどうかを確認します |
> | Microsoft.NetApp/locations/checkfilepathavailability/action | ファイル パスが使用可能かどうかを確認します |
> | Microsoft.NetApp/locations/operationresults/read | 操作結果リソースを読み取ります。 |
> | Microsoft.NetApp/netAppAccounts/read | アカウント リソースを読み取ります。 |
> | Microsoft.NetApp/netAppAccounts/write | アカウント リソースを書き込みます。 |
> | Microsoft.NetApp/netAppAccounts/delete | アカウント リソースを削除します。 |
> | Microsoft.NetApp/netAppAccounts/accountBackups/read | アカウント バックアップ リソースが読み取られます。 |
> | Microsoft.NetApp/netAppAccounts/accountBackups/write | アカウント バックアップ リソースが書き込まれます。 |
> | Microsoft.NetApp/netAppAccounts/accountBackups/delete | アカウント バックアップ リソースが削除されます。 |
> | Microsoft.NetApp/netAppAccounts/backupPolicies/read | バックアップ ポリシー リソースを読み取ります。 |
> | Microsoft.NetApp/netAppAccounts/backupPolicies/write | バックアップ ポリシー リソースを書き込みます。 |
> | Microsoft.NetApp/netAppAccounts/backupPolicies/delete | バックアップ ポリシー リソースを削除します。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/read | プール リソースを読み取ります。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/write | プール リソースを書き込みます。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/delete | プール リソースを削除します。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/read | ボリューム リソースを読み取ります。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write | ボリューム リソースを書き込みます。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/delete | ボリューム リソースを削除します。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/Revert/action | ボリュームを特定のスナップショットに戻します |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/BreakReplication/action | ボリューム レプリケーション関係を中断します |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/ReplicationStatus/action | ボリューム レプリケーションの状態を読み取ります。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/ReInitializeReplication/action | 初期化されていないレプリケーションの再初期化を試行します |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/AuthorizeReplication/action | ソース ボリュームのレプリケーションを承認します |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/ResyncReplication/action | ターゲット ボリュームのレプリケーションを再同期します |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/DeleteReplication/action | ターゲット ボリュームのレプリケーションが削除されます |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/backups/read | バックアップ リソースを読み取ります。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/backups/write | バックアップ リソースを書き込みます。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/backups/delete | バックアップ リソースを削除します。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/BackupStatus/read |  |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/MountTargets/read | マウント ターゲット リソースを読み取ります。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/ReplicationStatus/read | ボリューム レプリケーションの状態を読み取ります。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/read | スナップショット リソースを読み取ります。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write | スナップショット リソースを書き込みます。 |
> | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete | スナップショット リソースを削除します。 |
> | Microsoft.NetApp/netAppAccounts/snapshotPolicies/read | スナップショット ポリシー リソースが読み取られます。 |
> | Microsoft.NetApp/netAppAccounts/snapshotPolicies/write | スナップショット ポリシー リソースが書き込まれます。 |
> | Microsoft.NetApp/netAppAccounts/snapshotPolicies/delete | スナップショット ポリシー リソースが削除されます。 |
> | Microsoft.NetApp/netAppAccounts/snapshotPolicies/Volumes/action | スナップショット ポリシーに接続されているボリュームが一覧表示されます |
> | Microsoft.NetApp/netAppAccounts/snapshotPolicies/ListVolumes/action | スナップショット ポリシーに接続されているボリュームが一覧表示されます |
> | Microsoft.NetApp/netAppAccounts/vaults/read | コンテナー リソースを読み取ります。 |
> | Microsoft.NetApp/netAppAccounts/volumeGroups/read | ボリューム グループ リソースを読み取ります。 |
> | Microsoft.NetApp/netAppAccounts/volumeGroups/write | ボリューム グループ リソースを書き込みます。 |
> | Microsoft.NetApp/netAppAccounts/volumeGroups/delete | ボリューム グループ リソースを削除します。 |
> | Microsoft.NetApp/Operations/read | 操作リソースを読み取ります。 |

### <a name="microsoftstorage"></a>Microsoft.Storage

Azure サービス:[Storage](../storage/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Storage/register/action | ストレージ リソース プロバイダーにサブスクリプションを登録し、ストレージ アカウントを作成できるようにします。 |
> | Microsoft.Storage/checknameavailability/read | アカウント名が有効であり、使用されていないことを確認します。 |
> | Microsoft.Storage/deletedAccounts/read |  |
> | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | 仮想ネットワークまたはサブネットを削除していることを Microsoft.Storage に通知します。 |
> | Microsoft.Storage/locations/checknameavailability/read | アカウント名が有効であり、使用されていないことを確認します。 |
> | Microsoft.Storage/locations/usages/read | 指定されたサブスクリプションのリソースの制限と現在の使用回数を返します。 |
> | Microsoft.Storage/operations/read | 非同期操作の状態をポーリングします。 |
> | Microsoft.Storage/resilienciesProgressions/read |  |
> | Microsoft.Storage/skus/read | Microsoft.Storage でサポートされている SKU を一覧表示します。 |
> | Microsoft.Storage/storageAccounts/updateInternalProperties/action |  |
> | Microsoft.Storage/storageAccounts/hnsonmigration/action | ユーザーはストレージ アカウントで進行中の Hns 移行を中止できます |
> | Microsoft.Storage/storageAccounts/hnsonmigration/action | ユーザーは hns アカウントの種類に移行できます |
> | Microsoft.Storage/storageAccounts/restoreBlobRanges/action | BLOB の範囲を指定した時間の状態に復元します |
> | Microsoft.Storage/storageAccounts/PrivateEndpointConnectionsApproval/action | プライベート エンドポイント接続を承認します |
> | Microsoft.Storage/storageAccounts/failover/action | 可用性に問題が生じる場合、お客様がフェールオーバーを制御できます |
> | Microsoft.Storage/storageAccounts/listkeys/action | 指定されたストレージ アカウントのアクセス キーを返します。 |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | 指定されたストレージ アカウントのアクセス キーを再生成します。 |
> | Microsoft.Storage/storageAccounts/revokeUserDelegationKeys/action | 指定されたストレージ アカウントのすべてのユーザーの委任キーを取り消します。 |
> | Microsoft.Storage/storageAccounts/delete | 既存のストレージ アカウントを削除します。 |
> | Microsoft.Storage/storageAccounts/read | ストレージ アカウントの一覧を返すか、指定されたストレージ アカウントのプロパティを取得します。 |
> | Microsoft.Storage/storageAccounts/listAccountSas/action | 指定されたストレージ アカウントのアカウント SAS トークンを返します。 |
> | Microsoft.Storage/storageAccounts/listServiceSas/action | 指定されたストレージ アカウントのサービス SAS トークンを返します。 |
> | Microsoft.Storage/storageAccounts/write | 指定されたパラメーターを使用してストレージ アカウントを作成するか、プロパティまたはタグを更新します。または、指定されたストレージ アカウントのカスタム ドメインを追加します。 |
> | Microsoft.Storage/storageAccounts/accountLocks/read |  |
> | Microsoft.Storage/storageAccounts/accountLocks/write |  |
> | Microsoft.Storage/storageAccounts/accountLocks/delete |  |
> | Microsoft.Storage/storageAccounts/blobServices/read | Blob service を一覧表示します |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Blob service のユーザーの委任キーを返します |
> | Microsoft.Storage/storageAccounts/blobServices/write | Blob service のプロパティの設定の結果を返します。 |
> | Microsoft.Storage/storageAccounts/blobServices/read | Blob service のプロパティまたは統計情報を返します。 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | BLOB コンテナーのパッチ結果を返します |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | コンテナーを削除した結果を返します。 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | コンテナーを返します |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | コンテナーの一覧を返します |
> | Microsoft.Storage/storageAccounts/blobServices/containers/lease/action | BLOB コンテナーのリースの結果を返します |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | BLOB コンテナーのプット結果を返します |
> | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | BLOB コンテナーの訴訟ホールドをクリアします。 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | BLOB コンテナーの訴訟ホールドを設定します。 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | BLOB コンテナーの不変ポリシーを拡張します。 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | BLOB コンテナーの不変ポリシーを削除します。 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | BLOB コンテナーの不変ポリシーを設定します。 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | BLOB コンテナーの不変ポリシーをロックします。 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | BLOB コンテナーの不変ポリシーを取得します。 |
> | Microsoft.Storage/storageAccounts/consumerDataSharePolicies/read |  |
> | Microsoft.Storage/storageAccounts/consumerDataSharePolicies/write |  |
> | Microsoft.Storage/storageAccounts/dataSharePolicies/delete |  |
> | Microsoft.Storage/storageAccounts/dataSharePolicies/read |  |
> | Microsoft.Storage/storageAccounts/dataSharePolicies/read |  |
> | Microsoft.Storage/storageAccounts/dataSharePolicies/write |  |
> | Microsoft.Storage/storageAccounts/encryptionScopes/read |  |
> | Microsoft.Storage/storageAccounts/encryptionScopes/read |  |
> | Microsoft.Storage/storageAccounts/encryptionScopes/write |  |
> | Microsoft.Storage/storageAccounts/encryptionScopes/write |  |
> | Microsoft.Storage/storageAccounts/fileServices/shares/action | ファイル共有を復元します |
> | Microsoft.Storage/storageAccounts/fileServices/read | ファイル サービスを一覧表示します |
> | Microsoft.Storage/storageAccounts/fileServices/write | ファイル サービスのプロパティを指定します |
> | Microsoft.Storage/storageAccounts/fileServices/read | ファイル サービスのプロパティを取得します |
> | Microsoft.Storage/storageAccounts/fileServices/shares/delete | ファイル共有を削除します |
> | Microsoft.Storage/storageAccounts/fileServices/shares/read | ファイル共有を取得します |
> | Microsoft.Storage/storageAccounts/fileServices/shares/read | ファイル共有を一覧表示します |
> | Microsoft.Storage/storageAccounts/fileServices/shares/write | ファイル共有を作成または更新します |
> | Microsoft.Storage/storageAccounts/inventoryPolicies/delete |  |
> | Microsoft.Storage/storageAccounts/inventoryPolicies/read |  |
> | Microsoft.Storage/storageAccounts/inventoryPolicies/read |  |
> | Microsoft.Storage/storageAccounts/inventoryPolicies/write |  |
> | Microsoft.Storage/storageAccounts/localUsers/delete | ローカル ユーザーを削除します |
> | Microsoft.Storage/storageAccounts/localusers/listKeys/action | ローカル ユーザー キーを一覧表示します |
> | Microsoft.Storage/storageAccounts/localusers/read | ローカル ユーザーを一覧表示します |
> | Microsoft.Storage/storageAccounts/localusers/read | ローカル ユーザーを取得します |
> | Microsoft.Storage/storageAccounts/localusers/write | ローカル ユーザーを作成または更新します |
> | Microsoft.Storage/storageAccounts/managementPolicies/delete | ストレージ アカウントの管理ポリシーを削除します |
> | Microsoft.Storage/storageAccounts/managementPolicies/read | ストレージ管理アカウントのポリシーを取得します |
> | Microsoft.Storage/storageAccounts/managementPolicies/write | ストレージ アカウントの管理ポリシーを配置します |
> | Microsoft.Storage/storageAccounts/objectReplicationPolicies/delete | オブジェクト レプリケーション ポリシーを削除します |
> | Microsoft.Storage/storageAccounts/objectReplicationPolicies/read | オブジェクト レプリケーション ポリシーを取得します |
> | Microsoft.Storage/storageAccounts/objectReplicationPolicies/read | オブジェクト レプリケーション ポリシーを一覧表示します |
> | Microsoft.Storage/storageAccounts/objectReplicationPolicies/write | オブジェクト レプリケーション ポリシーを作成または更新します |
> | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/read | プライベート エンドポイント接続プロキシを取得します |
> | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/delete | プライベート エンドポイント接続プロキシを削除します |
> | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/write | プライベート エンドポイント接続プロキシを設定します |
> | Microsoft.Storage/storageAccounts/privateEndpointConnections/read | プライベート エンドポイント接続を一覧表示します |
> | Microsoft.Storage/storageAccounts/privateEndpointConnections/delete | プライベート エンドポイント接続を削除します |
> | Microsoft.Storage/storageAccounts/privateEndpointConnections/read | プライベート エンドポイント接続を取得します |
> | Microsoft.Storage/storageAccounts/privateEndpointConnections/write | プライベート エンドポイント接続を設定します |
> | Microsoft.Storage/storageAccounts/privateLinkResources/read | StorageAccount groupids を取得します |
> | Microsoft.Storage/storageAccounts/queueServices/read |  |
> | Microsoft.Storage/storageAccounts/queueServices/write |  |
> | Microsoft.Storage/storageAccounts/queueServices/read | Queue サービスのプロパティを取得します |
> | Microsoft.Storage/storageAccounts/queueServices/read | Queue サービスのプロパティまたは統計情報を返します。 |
> | Microsoft.Storage/storageAccounts/queueServices/write | Queue サービスのプロパティを設定した結果を返します。 |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | キューまたはキューの一覧を返します。 |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write | キューの書き込みの結果を返します。 |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete | キューを削除した結果を返します。 |
> | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | ストレージ アカウントの診断設定を作成または更新します。 |
> | Microsoft.Storage/storageAccounts/tableServices/read |  |
> | Microsoft.Storage/storageAccounts/tableServices/read | Table service のプロパティを取得します |
> | Microsoft.Storage/storageAccounts/tableServices/write |  |
> | Microsoft.Storage/storageAccounts/tableServices/tables/delete |  |
> | Microsoft.Storage/storageAccounts/tableServices/tables/read |  |
> | Microsoft.Storage/storageAccounts/tableServices/tables/read |  |
> | Microsoft.Storage/storageAccounts/tableServices/tables/write |  |
> | Microsoft.Storage/usages/read | 指定されたサブスクリプションのリソースの制限と現在の使用回数を返します。 |
> | **DataAction** | **説明** |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | BLOB または BLOB の一覧を返します。 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | BLOB の書き込みの結果を返します。 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | BLOB を削除した結果を返します。 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteBlobVersion/action | BLOB バージョンを削除した結果を返します |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/permanentDelete/action |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action | BLOB コンテンツを追加した結果を返します。 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action | タグが一致するアカウントの BLOB の一覧のみを返します |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action | パス間で BLOB を移動します |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/manageOwnership/action | BLOB の所有権を変更します |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/modifyPermissions/action | BLOB のアクセス許可を変更します |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action | BLOB コマンドの結果を返します |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read | BLOB タグの読み取り結果を返します |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write | BLOB タグの書き込み結果を返します |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | ファイルまたはフォルダー、またはファイルまたはフォルダーの一覧を返します |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | ファイルの書き込みまたはフォルダーの作成の結果を返します |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete | ファイル/フォルダーの削除の結果を返します |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action | ファイル/フォルダーに対するアクセス許可の変更の結果を返します |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/actassuperuser/action | "ファイルを取得" 管理特権 |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | メッセージを返します。 |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | メッセージの書き込みの結果を返します。 |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | メッセージを削除した結果を返します。 |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | メッセージを追加した結果を返します。 |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | メッセージを処理した結果を返します。 |

### <a name="microsoftstoragesync"></a>microsoft.storagesync

Azure サービス:[Storage](../storage/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.storagesync/register/action | ストレージ同期プロバイダーのサブスクリプションを登録します。 |
> | microsoft.storagesync/unregister/action | ストレージ同期プロバイダーのサブスクリプションを登録解除します。 |
> | microsoft.storagesync/locations/checkNameAvailability/action | ストレージ同期サービス名が有効であり、使用されていないことを確認します。 |
> | microsoft.storagesync/locations/operationresults/read | 非同期操作の結果を取得します |
> | microsoft.storagesync/locations/operations/read | Azure 非同期操作の状態を取得します |
> | microsoft.storagesync/locations/workflows/operations/read | 非同期操作の状態を取得します。 |
> | microsoft.storagesync/operations/read | サポート対象の操作の一覧を取得します。 |
> | microsoft.storagesync/storageSyncServices/read | ストレージ同期サービスを読み取ります。 |
> | microsoft.storagesync/storageSyncServices/write | ストレージ同期サービスを作成または更新します。 |
> | microsoft.storagesync/storageSyncServices/delete | ストレージ同期サービスを削除します。 |
> | microsoft.storagesync/storageSyncServices/privateEndpointConnectionProxies/validate/action | プライベート エンドポイントの ConnectionProxies を確認します |
> | microsoft.storagesync/storageSyncServices/privateEndpointConnectionProxies/read | プライベート エンドポイントの ConnectionProxies を読み取ります |
> | microsoft.storagesync/storageSyncServices/privateEndpointConnectionProxies/write | プライベート エンドポイントの ConnectionProxies を作成または更新します |
> | microsoft.storagesync/storageSyncServices/privateEndpointConnectionProxies/delete | プライベート エンドポイントの ConnectionProxies を削除します |
> | microsoft.storagesync/storageSyncServices/privateEndpointConnections/read | プライベート エンドポイントの接続を読み取ります |
> | microsoft.storagesync/storageSyncServices/privateEndpointConnections/write | プライベート エンドポイントの接続を作成または更新します |
> | microsoft.storagesync/storageSyncServices/privateEndpointConnections/delete | プライベート エンドポイントの接続を削除します |
> | microsoft.storagesync/storageSyncServices/privateLinkResources/read | Private Link リソースを読み取ります |
> | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | ストレージ同期サービスで使用可能なメトリックを取得します。 |
> | microsoft.storagesync/storageSyncServices/registeredServers/read | 登録済みサーバーを読み取ります。 |
> | microsoft.storagesync/storageSyncServices/registeredServers/write | 登録済みサーバーを作成または更新します。 |
> | microsoft.storagesync/storageSyncServices/registeredServers/delete | 登録済みサーバーを削除します。 |
> | microsoft.storagesync/storageSyncServices/registeredServers/providers/Microsoft.Insights/metricDefinitions/read | 登録済みサーバーで使用可能なメトリックを取得します。 |
> | microsoft.storagesync/storageSyncServices/syncGroups/read | 同期グループを読み取ります。 |
> | microsoft.storagesync/storageSyncServices/syncGroups/write | 同期グループを作成または更新します。 |
> | microsoft.storagesync/storageSyncServices/syncGroups/delete | 同期グループを削除します。 |
> | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | クラウド エンドポイントを読み取ります。 |
> | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | クラウド エンドポイントを作成または更新します。 |
> | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | クラウド エンドポイントを削除します。 |
> | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | バックアップの前にこのアクションを呼び出します。 |
> | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | バックアップの後にこのアクションを呼び出します。 |
> | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | 復元の前にこのアクションを呼び出します。 |
> | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | 復元の後にこのアクションを呼び出します。 |
> | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | ハートビートを復元します。 |
> | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/triggerChangeDetection/action | クラウド エンドポイントのファイル共有に対する変更の検出をトリガーするには、このアクションを呼び出します |
> | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | 非同期のバックアップ/復元操作の状態を取得します |
> | microsoft.storagesync/storageSyncServices/syncGroups/providers/Microsoft.Insights/metricDefinitions/read | 同期グループで使用可能なメトリックを取得します。 |
> | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | サーバー エンドポイントを読み取ります。 |
> | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | サーバー エンドポイントを作成または更新します。 |
> | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | サーバー エンドポイントを削除します。 |
> | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | このアクションを呼び出して、サーバーにファイルを戻します。 |
> | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/providers/Microsoft.Insights/metricDefinitions/read | サーバー エンドポイントで使用可能なメトリックを取得します。 |
> | microsoft.storagesync/storageSyncServices/workflows/read | ワークフローを読み取ります |
> | microsoft.storagesync/storageSyncServices/workflows/operationresults/read | 非同期操作の状態を取得します。 |
> | microsoft.storagesync/storageSyncServices/workflows/operations/read | 非同期操作の状態を取得します。 |

### <a name="microsoftstorsimple"></a>Microsoft.StorSimple

Azure サービス:[StorSimple](../storsimple/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.StorSimple/register/action | プロバイダー Microsoft.StorSimple を登録します。 |
> | Microsoft.StorSimple/managers/clearAlerts/action | デバイス マネージャーに関連付けられているすべてのアラートをクリアします。 |
> | Microsoft.StorSimple/managers/getEncryptionKey/action | デバイス マネージャーの暗号化キーを取得します。 |
> | Microsoft.StorSimple/managers/read | デバイス マネージャーを一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/delete | デバイス マネージャーを削除します。 |
> | Microsoft.StorSimple/managers/write | デバイス マネージャーを作成または更新します。 |
> | Microsoft.StorSimple/managers/configureDevice/action | デバイスを構成します。 |
> | Microsoft.StorSimple/managers/migrateClassicToResourceManager/action | クラシックから Resource Manager に移行する |
> | Microsoft.StorSimple/managers/listActivationKey/action | StorSimple デバイス マネージャーのアクティブ化キーを取得します。 |
> | Microsoft.StorSimple/managers/regenerateActivationKey/action | 既存の StorSimple デバイス マネージャーのアクティブ化キーを再生成します。 |
> | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | StorSimple デバイス マネージャーの公開暗号化キーを一覧表示します。 |
> | Microsoft.StorSimple/managers/provisionCloudAppliance/action | 新しいクラウド アプライアンスを作成します。 |
> | Microsoft.StorSimple/Managers/write | "コンテナーの作成" 操作では、"コンテナー" 型の Azure リソースを作成します。 |
> | Microsoft.StorSimple/Managers/read | "コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。 |
> | Microsoft.StorSimple/Managers/delete | "コンテナーの削除" 操作では、指定された "コンテナー" 型の Azure リソースを削除します。 |
> | Microsoft.StorSimple/managers/accessControlRecords/read | アクセス制御レコードを一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/accessControlRecords/write | アクセス制御レコードを作成または更新します。 |
> | Microsoft.StorSimple/managers/accessControlRecords/delete | アクセス制御レコードを削除します。 |
> | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | 操作結果を一覧表示するか、取得します。 |
> | Microsoft.StorSimple/managers/alerts/read | アラートを一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/backups/read | バックアップ セットを一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/bandwidthSettings/read | 帯域幅設定を一覧表示します (8000 シリーズのみ)。 |
> | Microsoft.StorSimple/managers/bandwidthSettings/write | 新しい帯域幅設定を作成するか、帯域幅設定を更新します (8000 シリーズのみ)。 |
> | Microsoft.StorSimple/managers/bandwidthSettings/delete | 既存の帯域幅設定を削除します (8000 シリーズのみ)。 |
> | Microsoft.StorSimple/managers/bandwidthSettings/operationResults/read | 操作結果を一覧表示します。 |
> | Microsoft.StorSimple/managers/certificates/write | 証明書を作成または更新します。 |
> | Microsoft.StorSimple/Managers/certificates/write | "リソース証明書を更新" 操作では、リソース/コンテナー資格情報証明書を更新します。 |
> | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | クラウド アプライアンスでサポートされている構成を一覧表示します。 |
> | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | 構成済みの電子メール受信者にテスト アラート メールを送信します。 |
> | Microsoft.StorSimple/managers/devices/scanForUpdates/action | デバイスで更新プログラムをスキャンします。 |
> | Microsoft.StorSimple/managers/devices/download/action | デバイスの更新プログラムをダウンロードします。 |
> | Microsoft.StorSimple/managers/devices/install/action | デバイスに更新プログラムをインストールします。 |
> | Microsoft.StorSimple/managers/devices/read | デバイスを一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/devices/write | デバイスを作成または更新します。 |
> | Microsoft.StorSimple/managers/devices/delete | デバイスを削除します。 |
> | Microsoft.StorSimple/managers/devices/deactivate/action | デバイスを非アクティブ化します。 |
> | Microsoft.StorSimple/managers/devices/failover/action | デバイスをフェールオーバーします。 |
> | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | 既存のデバイスのサポート パッケージを発行します。 StorSimple サポート パッケージは、Microsoft サポートが StorSimple デバイスの問題を解決するときに役立つ、すべての関連するログを収集する使いやすいメカニズムです。 |
> | Microsoft.StorSimple/managers/devices/authorizeForServiceEncryptionKeyRollover/action | デバイスのサービス暗号化キー ロールオーバーに対して承認する |
> | Microsoft.StorSimple/managers/devices/installUpdates/action | デバイスに更新プログラムをインストールします (8000 シリーズのみ)。 |
> | Microsoft.StorSimple/managers/devices/listFailoverSets/action | 既存のデバイスのフェールオーバー セットを一覧表示します (8000 シリーズのみ)。 |
> | Microsoft.StorSimple/managers/devices/listFailoverTargets/action | デバイスのフェールオーバー ターゲットを一覧表示します (8000 シリーズのみ)。 |
> | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | デバイス マネージャーの公開暗号化キーを一覧表示します。 |
> | Microsoft.StorSimple/managers/devices/alertSettings/read | アラート設定を一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/devices/alertSettings/write | アラート設定を作成または更新します。 |
> | Microsoft.StorSimple/managers/devices/alertSettings/operationResults/read | 操作結果を一覧表示するか、取得します。 |
> | Microsoft.StorSimple/managers/devices/backupPolicies/write | 新しいバックアップ ポリシーを作成するか、バックアップ ポリシーを更新します (8000 シリーズのみ)。 |
> | Microsoft.StorSimple/managers/devices/backupPolicies/read | バックアップ ポリシーを一覧表示します (8000 シリーズのみ)。 |
> | Microsoft.StorSimple/managers/devices/backupPolicies/delete | 既存のバックアップ ポリシーを削除します (8000 シリーズのみ)。 |
> | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | 手動バックアップを実行して、ポリシーで保護されているすべてのボリュームのオンデマンド バックアップを作成します。 |
> | Microsoft.StorSimple/managers/devices/backupPolicies/operationResults/read | 操作結果を一覧表示します。 |
> | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | 新しいスケジュールを作成するか、スケジュールを更新します。 |
> | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | スケジュールを一覧表示します。 |
> | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | 既存のスケジュールを削除します。 |
> | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/operationResults/read | 操作結果を一覧表示します。 |
> | Microsoft.StorSimple/managers/devices/backups/read | バックアップ セットを一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/devices/backups/delete | バックアップ セットを削除します。 |
> | Microsoft.StorSimple/managers/devices/backups/restore/action | バックアップ セットからすべてのボリュームを復元します。 |
> | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | バックアップ要素を使用して、共有またはボリュームを複製します。 |
> | Microsoft.StorSimple/managers/devices/backups/elements/operationResults/read | 操作結果を一覧表示するか、取得します。 |
> | Microsoft.StorSimple/managers/devices/backups/operationResults/read | 操作結果を一覧表示するか、取得します。 |
> | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | バックアップ スケジュール グループを一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | バックアップ スケジュール グループを作成または更新します。 |
> | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | バックアップ スケジュール グループを削除します。 |
> | Microsoft.StorSimple/managers/devices/backupScheduleGroups/operationResults/read | 操作結果を一覧表示するか、取得します。 |
> | Microsoft.StorSimple/managers/devices/chapSettings/write | CHAP 設定を作成または更新します。 |
> | Microsoft.StorSimple/managers/devices/chapSettings/read | CHAP 設定を一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/devices/chapSettings/delete | CHAP 設定を削除します。 |
> | Microsoft.StorSimple/managers/devices/chapSettings/operationResults/read | 操作結果を一覧表示するか、取得します。 |
> | Microsoft.StorSimple/managers/devices/disks/read | ディスクを一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/devices/failover/operationResults/read | 操作結果を一覧表示するか、取得します。 |
> | Microsoft.StorSimple/managers/devices/failoverTargets/read | デバイスのフェールオーバー ターゲットを一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/devices/fileservers/read | ファイル サーバーを一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/devices/fileservers/write | ファイル サーバーを作成または更新します。 |
> | Microsoft.StorSimple/managers/devices/fileservers/delete | ファイル サーバーを削除します。 |
> | Microsoft.StorSimple/managers/devices/fileservers/backup/action | ファイル サーバーのバックアップを作成します。 |
> | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | メトリックを一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | メトリック定義を一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/devices/fileservers/operationResults/read | 操作結果を一覧表示するか、取得します。 |
> | Microsoft.StorSimple/managers/devices/fileservers/shares/write | 共有を作成または更新します。 |
> | Microsoft.StorSimple/managers/devices/fileservers/shares/read | 共有を一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | 共有を削除します。 |
> | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | メトリックを一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | メトリック定義を一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/devices/fileservers/shares/operationResults/read | 操作結果を一覧表示するか、取得します。 |
> | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | ハードウェア コンポーネント グループを一覧表示します。 |
> | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | ハードウェア コンポーネント グループのコントローラーの電源の状態を変更します。 |
> | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/operationResults/read | 操作結果を一覧表示します。 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/read | iSCSI サーバーを一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/write | iSCSI サーバーを作成または更新します。 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/delete | iSCSI サーバーを削除します。 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/backup/action | iSCSI サーバーのバックアップを作成します。 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/disks/read | ディスクを一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | ディスクを作成または更新します。 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | ディスクを削除します。 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | メトリックを一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | メトリック定義を一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/disks/operationResults/read | 操作結果を一覧表示するか、取得します。 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | メトリックを一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | メトリック定義を一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/devices/iscsiservers/operationResults/read | 操作結果を一覧表示するか、取得します。 |
> | Microsoft.StorSimple/managers/devices/jobs/read | ジョブを一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/devices/jobs/cancel/action | 実行中のジョブを取り消します。 |
> | Microsoft.StorSimple/managers/devices/jobs/operationResults/read | 操作結果を一覧表示します。 |
> | Microsoft.StorSimple/managers/devices/metrics/read | メトリックを一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | メトリック定義を一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | 移行のためにソース構成をインポートします。 |
> | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action | 移行プロセスの継続時間を推定するジョブを開始します。 |
> | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigration/action | ソース構成を使用して移行を開始します。 |
> | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigration/action | 移行が正常に完了したことを確認し、移行をコミットします。 |
> | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | 移行推定ジョブの状態を取得します。 |
> | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action | 移行の状態を取得します。 |
> | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | 移行の確認の状態を取得します。 |
> | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigrationStatus/read | 移行の確認の状態を一覧表示します。 |
> | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationEstimate/read | 移行見積もりを一覧表示します。 |
> | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationStatus/read | 移行状態を一覧表示します。 |
> | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/operationResults/read | 操作結果を一覧表示します。 |
> | Microsoft.StorSimple/managers/devices/networkSettings/read | ネットワーク設定を一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/devices/networkSettings/write | 新しいネットワーク設定を作成するか、ネットワーク設定を更新します。 |
> | Microsoft.StorSimple/managers/devices/networkSettings/operationResults/read | 操作結果を一覧表示します。 |
> | Microsoft.StorSimple/managers/devices/operationResults/read | 操作結果を一覧表示するか、取得します。 |
> | Microsoft.StorSimple/managers/devices/securitySettings/update/action | セキュリティ設定を更新します。 |
> | Microsoft.StorSimple/managers/devices/securitySettings/read | セキュリティ設定を一覧表示します。 |
> | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | デバイスのリモート管理証明書を同期します。 |
> | Microsoft.StorSimple/managers/devices/securitySettings/write | 新しいセキュリティ設定を作成するか、セキュリティ設定を更新します。 |
> | Microsoft.StorSimple/managers/devices/securitySettings/operationResults/read | 操作結果を一覧表示するか、取得します。 |
> | Microsoft.StorSimple/managers/devices/shares/read | 共有を一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/devices/timeSettings/read | 時刻設定を一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/devices/timeSettings/write | 新しい時刻設定を作成するか、時刻設定を更新します。 |
> | Microsoft.StorSimple/managers/devices/timeSettings/operationResults/read | 操作結果を一覧表示します。 |
> | Microsoft.StorSimple/managers/devices/updates/operationResults/read | 操作結果を一覧表示するか、取得します。 |
> | Microsoft.StorSimple/managers/devices/updateSummary/read | 更新の概要を一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/devices/volumeContainers/write | 新しいボリューム コンテナーを作成するか、ボリューム コンテナーを更新します (8000 シリーズのみ)。 |
> | Microsoft.StorSimple/managers/devices/volumeContainers/read | ボリューム コンテナーを一覧表示します (8000 シリーズのみ)。 |
> | Microsoft.StorSimple/managers/devices/volumeContainers/delete | 既存のボリューム コンテナーを削除します (8000 シリーズのみ)。 |
> | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | メトリックを一覧表示します。 |
> | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | メトリック定義を一覧表示します。 |
> | Microsoft.StorSimple/managers/devices/volumeContainers/operationResults/read | 操作結果を一覧表示します。 |
> | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | ボリュームを一覧表示します。 |
> | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | 新しいボリュームを作成するか、ボリュームを更新します。 |
> | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | 既存のボリュームを削除します。 |
> | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | メトリックを一覧表示します。 |
> | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | メトリック定義を一覧表示します。 |
> | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/operationResults/read | 操作結果を一覧表示します。 |
> | Microsoft.StorSimple/managers/devices/volumes/read | ボリュームを一覧表示します。 |
> | Microsoft.StorSimple/managers/encryptionSettings/read | 暗号化設定を一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/extendedInformation/read | 拡張資格情報コンテナーの情報を一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/extendedInformation/write | 拡張資格情報コンテナーの情報を作成または更新します。 |
> | Microsoft.StorSimple/managers/extendedInformation/delete | 拡張資格情報コンテナーの情報を削除します |
> | Microsoft.StorSimple/Managers/extendedInformation/read | "拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。 |
> | Microsoft.StorSimple/Managers/extendedInformation/write | "拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。 |
> | Microsoft.StorSimple/Managers/extendedInformation/delete | "拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。 |
> | Microsoft.StorSimple/managers/features/read | 機能を一覧表示します。 |
> | Microsoft.StorSimple/managers/fileservers/read | ファイル サーバーを一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/iscsiservers/read | iSCSI サーバーを一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/jobs/read | ジョブを一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/metrics/read | メトリックを一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/metricsDefinitions/read | メトリック定義を一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/migrationSourceConfigurations/read | 移行ソース構成を一覧表示します (8000 シリーズのみ)。 |
> | Microsoft.StorSimple/managers/operationResults/read | 操作結果を一覧表示するか、取得します。 |
> | Microsoft.StorSimple/managers/storageAccountCredentials/write | ストレージ アカウントの資格情報を作成または更新します。 |
> | Microsoft.StorSimple/managers/storageAccountCredentials/read | ストレージ アカウントの資格情報を一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/storageAccountCredentials/delete | ストレージ アカウントの資格情報を削除します。 |
> | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | 操作結果を一覧表示するか、取得します。 |
> | Microsoft.StorSimple/managers/storageDomains/read | ストレージ ドメインを一覧表示または取得します。 |
> | Microsoft.StorSimple/managers/storageDomains/write | ストレージ ドメインを作成または更新します。 |
> | Microsoft.StorSimple/managers/storageDomains/delete | ストレージ ドメインを削除します。 |
> | Microsoft.StorSimple/managers/storageDomains/operationResults/read | 操作結果を一覧表示するか、取得します。 |
> | Microsoft.StorSimple/operations/read | 操作を一覧表示または取得します。 |

## <a name="web"></a>Web

### <a name="microsoftappplatform"></a>Microsoft.AppPlatform

Azure サービス: [Azure Spring Cloud](../spring-cloud/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.AppPlatform/register/action | Microsoft.AppPlatform リソース プロバイダーにサブスクリプションを登録します |
> | Microsoft.AppPlatform/unregister/action | Microsoft.AppPlatform リソース プロバイダーからサブスクリプションの登録を解除します |
> | Microsoft.AppPlatform/locations/checkNameAvailability/action | リソース名の可用性を確認します |
> | Microsoft.AppPlatform/locations/operationResults/Spring/read | リソースの操作結果を読み取ります |
> | Microsoft.AppPlatform/locations/operationStatus/operationId/read | リソースの操作状態を読み取ります |
> | Microsoft.AppPlatform/operations/read | Microsoft Azure Spring Cloud の利用可能な操作を一覧表示します |
> | Microsoft.AppPlatform/skus/read | Microsoft Azure Spring Cloud の利用可能な SKU を一覧表示します |
> | Microsoft.AppPlatform/Spring/write | 特定の Azure Spring Cloud サービス インスタンスを作成または更新します |
> | Microsoft.AppPlatform/Spring/delete | 特定の Azure Spring Cloud サービス インスタンスを削除します |
> | Microsoft.AppPlatform/Spring/read | Azure Spring Cloud サービス インスタンスを取得します |
> | Microsoft.AppPlatform/Spring/enableTestEndpoint/action | 特定の Azure Spring Cloud サービス インスタンスのテスト エンドポイント機能を有効にします |
> | Microsoft.AppPlatform/Spring/disableTestEndpoint/action | 特定の Azure Spring Cloud サービス インスタンスのテスト エンドポイント機能を無効にします |
> | Microsoft.AppPlatform/Spring/listTestKeys/action | 特定の Azure Spring Cloud サービス インスタンスのテスト キーを一覧表示します |
> | Microsoft.AppPlatform/Spring/regenerateTestKey/action | 特定の Azure Spring Cloud サービス インスタンスのテスト キーを再生成します |
> | Microsoft.AppPlatform/Spring/apps/write | 特定の Azure Spring Cloud サービス インスタンスのアプリケーションを作成または更新します |
> | Microsoft.AppPlatform/Spring/apps/delete | 特定の Azure Spring Cloud サービス インスタンスのアプリケーションを削除します |
> | Microsoft.AppPlatform/Spring/apps/read | 特定の Azure Spring Cloud サービス インスタンスのアプリケーションを取得します |
> | Microsoft.AppPlatform/Spring/apps/getResourceUploadUrl/action | 特定の Microsoft Azure Spring Cloud アプリケーションのリソース アップロード URL を取得します |
> | Microsoft.AppPlatform/Spring/apps/validateDomain/action | 特定のアプリケーションのカスタム ドメインを検証します |
> | Microsoft.AppPlatform/Spring/apps/bindings/write | 特定のアプリケーションのバインドを作成または更新します |
> | Microsoft.AppPlatform/Spring/apps/bindings/delete | 特定のアプリケーションのバインドを削除します |
> | Microsoft.AppPlatform/Spring/apps/bindings/read | 特定のアプリケーションのバインドを取得します |
> | Microsoft.AppPlatform/Spring/apps/deployments/write | 特定のアプリケーションのデプロイを作成または更新します |
> | Microsoft.AppPlatform/Spring/apps/deployments/delete | 特定のアプリケーションのデプロイを削除します |
> | Microsoft.AppPlatform/Spring/apps/deployments/read | 特定のアプリケーションのデプロイを取得します |
> | Microsoft.AppPlatform/Spring/apps/deployments/start/action | 特定のアプリケーションのデプロイを開始します |
> | Microsoft.AppPlatform/Spring/apps/deployments/stop/action | 特定のアプリケーションのデプロイを停止します |
> | Microsoft.AppPlatform/Spring/apps/deployments/restart/action | 特定のアプリケーションのデプロイを再開します |
> | Microsoft.AppPlatform/Spring/apps/deployments/getLogFileUrl/action | 特定の Microsoft Azure Spring Cloud アプリケーションのデプロイのログ ファイルの URL を取得します |
> | Microsoft.AppPlatform/Spring/apps/deployments/skus/read | アプリケーションのデプロイの利用可能な SKU を一覧表示します |
> | Microsoft.AppPlatform/Spring/apps/domains/write | 特定のアプリケーションのカスタム ドメインを作成または更新します |
> | Microsoft.AppPlatform/Spring/apps/domains/delete | 特定のアプリケーションのカスタム ドメインを削除します |
> | Microsoft.AppPlatform/Spring/apps/domains/read | 特定のアプリケーションのカスタム ドメインを取得します |
> | Microsoft.AppPlatform/Spring/certificates/write | 特定の Azure Spring Cloud サービス インスタンスの証明書を作成または更新します |
> | Microsoft.AppPlatform/Spring/certificates/delete | 特定の Azure Spring Cloud サービス インスタンスの証明書を削除します |
> | Microsoft.AppPlatform/Spring/certificates/read | 特定の Azure Spring Cloud サービス インスタンスの証明書を取得します |
> | Microsoft.AppPlatform/Spring/configServers/read | 特定の Azure Spring Cloud サービス インスタンスの構成サーバーを取得します |
> | Microsoft.AppPlatform/Spring/configServers/write | 特定の Azure Spring Cloud サービス インスタンスの構成サーバーを作成または更新します |
> | Microsoft.AppPlatform/Spring/deployments/read | 特定の Azure Spring Cloud サービス インスタンスのデプロイを取得します |
> | Microsoft.AppPlatform/Spring/detectors/read | 特定の Azure Spring Cloud サービス インスタンスの検出機能を取得します |
> | Microsoft.AppPlatform/Spring/monitoringSettings/read | 特定の Azure Spring Cloud サービス インスタンスの監視設定を取得します |
> | Microsoft.AppPlatform/Spring/monitoringSettings/write | 特定の Azure Spring Cloud サービス インスタンスの監視設定を作成または更新します |
> | **DataAction** | **説明** |
> | Microsoft.AppPlatform/Spring/configService/read | 特定の Azure Spring Cloud サービス インスタンスの構成内容 (たとえば、application.yaml) を読み取ります |
> | Microsoft.AppPlatform/Spring/eurekaService/read | 特定の Azure Spring Cloud サービス インスタンスのユーザー アプリ登録情報を読み取ります |
> | Microsoft.AppPlatform/Spring/eurekaService/write | 特定の Azure Spring Cloud サービス インスタンスのユーザー アプリの登録情報を書き込みます |
> | Microsoft.AppPlatform/Spring/eurekaService/delete | 特定の Azure Spring Cloud サービス インスタンスのユーザー アプリの登録情報を削除します |
> | Microsoft.AppPlatform/Spring/logstreamService/read | 特定の Azure Spring Cloud サービス インスタンスのユーザー アプリのストリーミング ログを読み取ります |

### <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

Azure サービス:[App Service 証明書](../app-service/configure-ssl-certificate.md#import-an-app-service-certificate)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | サービス アプリ プリンシパルのサービス プリンシパルをプロビジョニングします。 |
> | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | 証明書購入オブジェクトを送信せずに検証します。 |
> | Microsoft.CertificateRegistration/register/action | Microsoft Certificates リソース プロバイダーをサブスクリプションに登録します。 |
> | Microsoft.CertificateRegistration/certificateOrders/Write | 新しい certificateOrder を追加するか、既存の certificateOrder を更新します。 |
> | Microsoft.CertificateRegistration/certificateOrders/Delete | 既存の AppServiceCertificate を削除します。 |
> | Microsoft.CertificateRegistration/certificateOrders/Read | CertificateOrders の一覧を取得します。 |
> | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | 既存の certificateorder を再発行します。 |
> | Microsoft.CertificateRegistration/certificateOrders/renew/Action | 既存の certificateorder を更新します。 |
> | Microsoft.CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | 証明書のアクションの一覧を取得します。 |
> | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | 証明書の電子メールの履歴を取得します。 |
> | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | 証明書の電子メールを再送信します。 |
> | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | ドメインの所有権を検証する |
> | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | 要求の電子メールを別の電子メール アドレスに再送信します。 |
> | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | 発行済みの App Service 証明書のサイト シールを取得します。 |
> | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | 新しい証明書を追加するか、既存の証明書を更新します。 |
> | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | 既存の証明書を削除します。 |
> | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | 証明書の一覧を取得します。 |
> | Microsoft.CertificateRegistration/operations/Read | アプリ サービス証明書登録のすべての操作を一覧表示します。 |

### <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

Azure サービス:[App Service](../app-service/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.DomainRegistration/generateSsoRequest/Action | ドメイン コントロール センターへのサインイン要求を生成します。 |
> | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | ドメイン購入オブジェクトを送信せずに検証します。 |
> | Microsoft.DomainRegistration/checkDomainAvailability/Action | ドメインを購入できるかどうかを確認します。 |
> | Microsoft.DomainRegistration/listDomainRecommendations/Action | キーワードに基づいて、ドメインの推奨事項の一覧を取得します。 |
> | Microsoft.DomainRegistration/register/action | Microsoft Domains リソース プロバイダーをサブスクリプションに登録します。 |
> | Microsoft.DomainRegistration/domains/Read | ドメインの一覧を取得します。 |
> | Microsoft.DomainRegistration/domains/Read | ドメインを取得します。 |
> | Microsoft.DomainRegistration/domains/Write | 新しいドメインを追加するか、既存のドメインを更新します。 |
> | Microsoft.DomainRegistration/domains/Delete | 既存のドメインを削除します。 |
> | Microsoft.DomainRegistration/domains/renew/Action | 既存のドメインを更新します。 |
> | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | 所有権の識別子を一覧表示します。 |
> | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | 所有権の識別子を取得します。 |
> | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | 識別子を作成または更新します。 |
> | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Delete | 所有権の識別子を削除します。 |
> | Microsoft.DomainRegistration/domains/operationresults/Read | ドメイン操作を取得します。 |
> | Microsoft.DomainRegistration/operations/Read | アプリ サービス ドメイン登録のすべての操作を一覧表示します。 |
> | Microsoft.DomainRegistration/topLevelDomains/Read | 最上位ドメインを取得します。 |
> | Microsoft.DomainRegistration/topLevelDomains/Read | 最上位ドメインを取得します。 |
> | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | 契約のアクションを一覧表示します。 |

### <a name="microsoftmaps"></a>Microsoft.Maps

Azure サービス:[Azure Maps](../azure-maps/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Maps/register/action | プロバイダーに登録します。 |
> | Microsoft.Maps/accounts/write | Maps アカウントを作成または更新します。 |
> | Microsoft.Maps/accounts/read | Maps アカウントを取得します。 |
> | Microsoft.Maps/accounts/delete | Maps アカウントを削除します。 |
> | Microsoft.Maps/accounts/listKeys/action | Maps アカウント キーを一覧表示します。 |
> | Microsoft.Maps/accounts/regenerateKey/action | Maps アカウントの新しい主または 2 次キーを生成します。 |
> | Microsoft.Maps/accounts/creators/write | 作成者を作成または更新します。 |
> | Microsoft.Maps/accounts/creators/read | 作成者を取得します。 |
> | Microsoft.Maps/accounts/creators/delete | 作成者を削除します。 |
> | Microsoft.Maps/accounts/eventGridFilters/delete | Event Grid のフィルターを削除します。 |
> | Microsoft.Maps/accounts/eventGridFilters/read | イベント グリッドのフィルターを取得します |
> | Microsoft.Maps/accounts/eventGridFilters/write | Event Grid のフィルターを作成または更新します。 |
> | Microsoft.Maps/accounts/privateAtlases/write | Private Atlas を作成または更新します。 |
> | Microsoft.Maps/accounts/privateAtlases/read | Private Atlas を取得します。 |
> | Microsoft.Maps/accounts/privateAtlases/delete | Private Atlas を削除します。 |
> | Microsoft.Maps/operations/read | プロバイダー操作を読み取ります |
> | **DataAction** | **説明** |
> | Microsoft.Maps/accounts/services/data/read | データのアップロード サービスおよび Private Atlas のデータの読み取りを許可します。 |
> | Microsoft.Maps/accounts/services/data/delete | データのアップロード サービスおよび Private Atlas のデータの削除を許可します |
> | Microsoft.Maps/accounts/services/data/write | データのアップロード サービスおよび Private Atlas のデータの書き込みまたは更新を許可します。 |
> | Microsoft.Maps/accounts/services/elevation/read | Elevation Service のデータの読み取りを許可します。 |
> | Microsoft.Maps/accounts/services/geolocation/read | 位置情報サービスのデータの読み取りを許可します。 |
> | Microsoft.Maps/accounts/services/mobility/read | モビリティ サービスのデータの読み取りを許可します。 |
> | Microsoft.Maps/accounts/services/render/read | レンダリング サービスのデータの読み取りを許可します。 |
> | Microsoft.Maps/accounts/services/route/read | ルート サービスのデータの読み取りを許可します。 |
> | Microsoft.Maps/accounts/services/search/read | 検索サービスのデータの読み取りを許可します。 |
> | Microsoft.Maps/accounts/services/spatial/read | 空間サービスのデータの読み取りを許可します。 |
> | Microsoft.Maps/accounts/services/spatial/write | イベントの発行など、空間サービスのデータの書き込みを許可します。 |
> | Microsoft.Maps/accounts/services/timezone/read | タイムゾーン サービスのデータの読み取りを許可します。 |
> | Microsoft.Maps/accounts/services/traffic/read | 交通サービスのデータの読み取りを許可します。 |
> | Microsoft.Maps/accounts/services/turnbyturn/read | TurnByTurn サービスのデータの読み取りを許可します。 |
> | Microsoft.Maps/accounts/services/weather/read | 天気サービスのデータの読み取りを許可します。 |

### <a name="microsoftmedia"></a>Microsoft.Media

Azure サービス:[Media Services](../media-services/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Media/register/action | Media Services リソース プロバイダーにサブスクリプションを登録し、Media Services アカウントを作成できるようにします。 |
> | Microsoft.Media/unregister/action | Media Services リソース プロバイダーへのサブスクリプションの登録を解除します。 |
> | Microsoft.Media/checknameavailability/action | Media Services のアカウント名が使用可能かどうかを確認します。 |
> | Microsoft.Media/locations/checkNameAvailability/action | Media Services のアカウント名が使用可能かどうかを確認します。 |
> | Microsoft.Media/mediaservices/read | Media Services アカウントを読み取ります。 |
> | Microsoft.Media/mediaservices/write | Media Services アカウントを作成または更新します。 |
> | Microsoft.Media/mediaservices/delete | Media Services アカウントを削除します。 |
> | Microsoft.Media/mediaservices/regenerateKey/action | Media Services の ACS キーを再生成します。 |
> | Microsoft.Media/mediaservices/listKeys/action | Media Services アカウントの ACS キーを一覧表示します。 |
> | Microsoft.Media/mediaservices/syncStorageKeys/action | アタッチされた Azure Storage アカウントのストレージ キーを同期します。 |
> | Microsoft.Media/mediaservices/listEdgePolicies/action | エッジ デバイスのポリシーを一覧表示します。 |
> | Microsoft.Media/mediaservices/accountfilters/read | アカウント フィルターを読み取ります。 |
> | Microsoft.Media/mediaservices/accountfilters/write | アカウント フィルターを作成または更新します。 |
> | Microsoft.Media/mediaservices/accountfilters/delete | アカウント フィルターを削除します。 |
> | Microsoft.Media/mediaservices/assets/read | アセンブリを読み取ります |
> | Microsoft.Media/mediaservices/assets/write | 資産を作成または更新します。 |
> | Microsoft.Media/mediaservices/assets/delete | 資産を削除します。 |
> | Microsoft.Media/mediaservices/assets/listContainerSas/action | 資産コンテナーの SAS URL の一覧を取得します。 |
> | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | 資産暗号化キーを取得します。 |
> | Microsoft.Media/mediaservices/assets/listStreamingLocators/action | 資産のストリーミング ロケーターの一覧を取得します。 |
> | Microsoft.Media/mediaservices/assets/assetfilters/read | 資産フィルターを読み取ります。 |
> | Microsoft.Media/mediaservices/assets/assetfilters/write | 資産フィルターを作成または更新します。 |
> | Microsoft.Media/mediaservices/assets/assetfilters/delete | 資産フィルターを削除します。 |
> | Microsoft.Media/mediaservices/contentKeyPolicies/read | コンテンツ キー ポリシーを読み取ります。 |
> | Microsoft.Media/mediaservices/contentKeyPolicies/write | コンテンツ キー ポリシーを作成または更新します。 |
> | Microsoft.Media/mediaservices/contentKeyPolicies/delete | コンテンツ キー ポリシーを削除します。 |
> | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | ポリシーのプロパティとシークレットを取得します。 |
> | Microsoft.Media/mediaservices/eventGridFilters/read | イベント グリッドのフィルターを読み取ります。 |
> | Microsoft.Media/mediaservices/eventGridFilters/write | イベント グリッドのフィルターを作成または更新します。 |
> | Microsoft.Media/mediaservices/eventGridFilters/delete | イベント グリッドのフィルターを削除します。 |
> | Microsoft.Media/mediaservices/liveEventOperations/read | ライブ イベント操作を読み取ります。 |
> | Microsoft.Media/mediaservices/liveEvents/read | ライブ イベントを読み取ります |
> | Microsoft.Media/mediaservices/liveEvents/write | ライブ イベントを作成または更新します。 |
> | Microsoft.Media/mediaservices/liveEvents/delete | ライブ イベントを削除します。 |
> | Microsoft.Media/mediaservices/liveEvents/start/action | ライブ イベント操作を開始します。 |
> | Microsoft.Media/mediaservices/liveEvents/stop/action | ライブ イベント操作を停止します。 |
> | Microsoft.Media/mediaservices/liveEvents/reset/action | ライブ イベント操作をリセットします。 |
> | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | ライブ出力を読み取ります |
> | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | ライブ出力を作成または更新します。 |
> | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | ライブ出力を削除します。 |
> | Microsoft.Media/mediaservices/liveEvents/privateEndpointConnectionProxies/read | ライブ イベントのプライベート エンドポイント接続プロキシを読み取ります |
> | Microsoft.Media/mediaservices/liveEvents/privateEndpointConnectionProxies/write | ライブ イベントのプライベート エンドポイント接続プロキシを作成します |
> | Microsoft.Media/mediaservices/liveEvents/privateEndpointConnectionProxies/delete | ライブ イベントのプライベート エンドポイント接続プロキシを削除します |
> | Microsoft.Media/mediaservices/liveEvents/privateEndpointConnectionProxies/validate/action | ライブ イベントのプライベート エンドポイント接続プロキシを検証します |
> | Microsoft.Media/mediaservices/liveEvents/privateEndpointConnections/read | ライブ イベントのプライベート エンドポイント接続を読み取ります |
> | Microsoft.Media/mediaservices/liveEvents/privateEndpointConnections/write | ライブ イベントのプライベート エンドポイント接続を作成します |
> | Microsoft.Media/mediaservices/liveEvents/privateEndpointConnections/delete | ライブ イベントのプライベート エンドポイント接続を削除します |
> | Microsoft.Media/mediaservices/liveEvents/privateLinkResources/read | ライブ イベントの Private Link リソースを読み取ります |
> | Microsoft.Media/mediaservices/liveOutputOperations/read | ライブ出力操作を読み取ります。 |
> | Microsoft.Media/mediaservices/mediaGraphs/read | 任意のメディア グラフを読み取ります |
> | Microsoft.Media/mediaservices/mediaGraphs/write | 任意のメディア グラフを作成または更新します |
> | Microsoft.Media/mediaservices/mediaGraphs/delete | 任意のメディア グラフを削除します |
> | Microsoft.Media/mediaservices/mediaGraphs/start/action | メディア グラフ操作を開始します |
> | Microsoft.Media/mediaservices/mediaGraphs/stop/action | 任意のメディア グラフ操作を停止します |
> | Microsoft.Media/mediaservices/privateEndpointConnectionOperations/read | プライベート エンドポイント接続操作を読み取ります |
> | Microsoft.Media/mediaservices/privateEndpointConnectionProxies/read | プライベート エンドポイント接続プロキシを読み取ります |
> | Microsoft.Media/mediaservices/privateEndpointConnectionProxies/write | プライベート エンドポイント接続プロキシを作成します |
> | Microsoft.Media/mediaservices/privateEndpointConnectionProxies/delete | プライベート エンドポイント接続プロキシを削除します |
> | Microsoft.Media/mediaservices/privateEndpointConnectionProxies/validate/action | プライベート エンドポイント接続プロキシを検証します |
> | Microsoft.Media/mediaservices/privateEndpointConnections/read | プライベート エンドポイント接続を読み取ります |
> | Microsoft.Media/mediaservices/privateEndpointConnections/write | プライベート エンドポイント接続を作成します |
> | Microsoft.Media/mediaservices/privateEndpointConnections/delete | プライベート エンドポイント接続を削除します |
> | Microsoft.Media/mediaservices/privateLinkResources/read | Private Link リソースを読み取ります |
> | Microsoft.Media/mediaservices/streamingEndpointOperations/read | ストリーミング エンドポイント操作を読み取ります。 |
> | Microsoft.Media/mediaservices/streamingEndpoints/read | ストリーミング エンドポイントを読み取ります。 |
> | Microsoft.Media/mediaservices/streamingEndpoints/write | ストリーミング エンドポイントを作成または更新します。 |
> | Microsoft.Media/mediaservices/streamingEndpoints/delete | ストリーミング エンドポイントを削除します。 |
> | Microsoft.Media/mediaservices/streamingEndpoints/start/action | ストリーミング エンドポイント操作を開始します。 |
> | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | ストリーミング エンドポイント操作を停止します。 |
> | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | ストリーミング エンドポイント操作をスケーリングします。 |
> | Microsoft.Media/mediaservices/streamingEndpoints/privateEndpointConnectionProxies/read | ストリーミング エンドポイントのプライベート エンドポイント接続プロキシを読み取ります |
> | Microsoft.Media/mediaservices/streamingEndpoints/privateEndpointConnectionProxies/write | ストリーミング エンドポイントのプライベート エンドポイント接続プロキシを作成します |
> | Microsoft.Media/mediaservices/streamingEndpoints/privateEndpointConnectionProxies/delete | ストリーミング エンドポイントのプライベート エンドポイント接続プロキシを削除します |
> | Microsoft.Media/mediaservices/streamingEndpoints/privateEndpointConnectionProxies/validate/action | ストリーミング エンドポイントのプライベート エンドポイント接続プロキシを検証します |
> | Microsoft.Media/mediaservices/streamingEndpoints/privateEndpointConnections/read | ストリーミング エンドポイントのプライベート エンドポイント接続を読み取ります |
> | Microsoft.Media/mediaservices/streamingEndpoints/privateEndpointConnections/write | ストリーミング エンドポイントのプライベート エンドポイント接続を作成します |
> | Microsoft.Media/mediaservices/streamingEndpoints/privateEndpointConnections/delete | ストリーミング エンドポイントのプライベート エンドポイント接続を削除します |
> | Microsoft.Media/mediaservices/streamingEndpoints/privateLinkResources/read | ストリーミング エンドポイントの Private Link リソースを読み取ります |
> | Microsoft.Media/mediaservices/streamingLocators/read | ストリーミング ロケーターを読み取ります。 |
> | Microsoft.Media/mediaservices/streamingLocators/write | ストリーミング ロケーターを作成または更新します。 |
> | Microsoft.Media/mediaservices/streamingLocators/delete | ストリーミング ロケーターを削除します。 |
> | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | コンテンツ キーの一覧を取得します。 |
> | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | パスの一覧を取得します。 |
> | Microsoft.Media/mediaservices/streamingPolicies/read | ストリーミング ポリシーを読み取ります。 |
> | Microsoft.Media/mediaservices/streamingPolicies/write | ストリーミング ポリシーを作成または更新します。 |
> | Microsoft.Media/mediaservices/streamingPolicies/delete | ストリーミング ポリシーを削除します。 |
> | Microsoft.Media/mediaservices/streamingPrivateEndpointConnectionOperations/read | ストリーミング プライベート エンドポイント接続の操作を読み取ります |
> | Microsoft.Media/mediaservices/streamingPrivateEndpointConnectionProxyOperations/read | ストリーミング プライベート エンドポイント接続プロキシの操作を読み取ります |
> | Microsoft.Media/mediaservices/transforms/read | 変換を読み取ります |
> | Microsoft.Media/mediaservices/transforms/write | 変換を作成または更新します。 |
> | Microsoft.Media/mediaservices/transforms/delete | 変換を削除します。 |
> | Microsoft.Media/mediaservices/transforms/jobs/read | ジョブを読み取ります |
> | Microsoft.Media/mediaservices/transforms/jobs/write | ジョブを作成または更新します。 |
> | Microsoft.Media/mediaservices/transforms/jobs/delete | ジョブを削除します。 |
> | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | ジョブを取り消します。 |
> | Microsoft.Media/operations/read | 使用可能な操作を取得します。 |
> | Microsoft.Media/videoAnalyzers/read | ビデオ アナライザー アカウントを読み取ります |
> | Microsoft.Media/videoAnalyzers/write | ビデオ アナライザー アカウントを書き込みます |
> | Microsoft.Media/videoAnalyzers/delete | ビデオ アナライザー アカウントを削除します |
> | Microsoft.Media/videoAnalyzers/syncStorageKeys/action | アタッチされたストレージ カウントのストレージキーを同期します |

### <a name="microsoftsearch"></a>Microsoft.Search

Azure サービス:[Azure Search](../search/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Search/register/action | 検索リソース プロバイダーにサブスクリプションを登録し、検索サービスを作成できるようにします。 |
> | Microsoft.Search/checkNameAvailability/action | サービス名を使用できるかどうかを確認します。 |
> | Microsoft.Search/operations/read | Microsoft.Search プロバイダーのすべての使用可能な操作の一覧を取得します。 |
> | Microsoft.Search/searchServices/write | 検索サービスを作成または更新します。 |
> | Microsoft.Search/searchServices/read | 検索サービスを読み取ります。 |
> | Microsoft.Search/searchServices/delete | 検索サービスを削除します。 |
> | Microsoft.Search/searchServices/start/action | 検索サービスを開始します。 |
> | Microsoft.Search/searchServices/stop/action | 検索サービスを停止します。 |
> | Microsoft.Search/searchServices/listAdminKeys/action | 管理者キーを読み取ります。 |
> | Microsoft.Search/searchServices/regenerateAdminKey/action | 管理者キーを再生成します。 |
> | Microsoft.Search/searchServices/listQueryKeys/action | 指定された Azure Search サービスのクエリ API キーの一覧を返します。 |
> | Microsoft.Search/searchServices/createQueryKey/action | クエリ キーを作成します。 |
> | Microsoft.Search/searchServices/deleteQueryKey/delete | クエリ キーを削除します。 |
> | Microsoft.Search/searchServices/privateEndpointConnectionProxies/validate/action | NRP 側からのプライベート エンドポイント接続作成呼び出しを検証します |
> | Microsoft.Search/searchServices/privateEndpointConnectionProxies/write | 指定されたパラメーターでプライベート エンドポイント接続プロキシを作成するか、指定されたプライベート エンドポイント接続プロキシのプロパティまたはタグを更新します |
> | Microsoft.Search/searchServices/privateEndpointConnectionProxies/read | プライベート エンドポイント接続プロキシの一覧を返すか、指定されたプライベート エンドポイント接続プロキシのプロパティを取得します |
> | Microsoft.Search/searchServices/privateEndpointConnectionProxies/delete | 既存のプライベート エンドポイント接続プロキシを削除します |
> | Microsoft.Search/searchServices/sharedPrivateLinkResources/write | 指定されたパラメーターを使用して新しい共有プライベート リンク リソースを作成するか、指定された共有プライベート リンク リソースのプロパティを更新します |
> | Microsoft.Search/searchServices/sharedPrivateLinkResources/read | 共有プライベート リンク リソースの一覧を返すか、指定された共有プライベート リンク リソースのプロパティを取得します |
> | Microsoft.Search/searchServices/sharedPrivateLinkResources/delete | 既存の共有プライベート リンク リソースを削除します |
> | Microsoft.Search/searchServices/sharedPrivateLinkResources/operationStatuses/read | 実行時間の長い共有プライベート リンク リソース操作の詳細を取得します |

### <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

Azure サービス:[Azure SignalR Service](../azure-signalr/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.SignalRService/register/action | "Microsoft.SignalRService" リソース プロバイダーをサブスクリプションに登録します。 |
> | Microsoft.SignalRService/unregister/action | "Microsoft.SignalRService" リソース プロバイダーのサブスクリプションの登録を解除します。 |
> | Microsoft.SignalRService/locations/checknameavailability/action | 新しい SignalR サービスで名前を使用できるかどうかを確認します。 |
> | Microsoft.SignalRService/locations/operationresults/signalr/read | 場所に基づく非同期操作の結果のクエリを実行します |
> | Microsoft.SignalRService/locations/operationStatuses/operationId/read | 場所に基づく非同期操作の状態のクエリを実行します |
> | Microsoft.SignalRService/locations/operationStatuses/signalr/read | 場所に基づく非同期操作の状態のクエリを実行します |
> | Microsoft.SignalRService/locations/usages/read | Azure SignalR Service のクォータ使用状況を取得します。 |
> | Microsoft.SignalRService/operationresults/read | プロバイダー レベルの非同期操作の結果のクエリを実行します |
> | Microsoft.SignalRService/operations/read | Azure SignalR Service の操作を一覧表示します。 |
> | Microsoft.SignalRService/operationstatus/read | プロバイダー レベルの非同期操作の状態のクエリを実行します |
> | Microsoft.SignalRService/SignalR/read | 管理ポータルで、または API を介して SignalR の設定と構成を表示します。 |
> | Microsoft.SignalRService/SignalR/write | 管理ポータルで、または API を介して SignalR の設定と構成を変更します。 |
> | Microsoft.SignalRService/SignalR/delete | SignalR Service 全体を削除します。 |
> | Microsoft.SignalRService/SignalR/listkeys/action | 管理ポータルで、または API を使用して SignalR のアクセス キーの値を表示します。 |
> | Microsoft.SignalRService/SignalR/regeneratekey/action | 管理ポータルで、または API を使用して SignalR のアクセス キーの値を変更します。 |
> | Microsoft.SignalRService/SignalR/restart/action | 管理ポータルで、または API を使用して Azure SignalR Service を再起動します。 特定のダウンタイムが発生します。 |
> | Microsoft.SignalRService/SignalR/eventGridFilters/read | 指定されたイベント グリッド フィルターのプロパティを取得するか、指定された SignalR のすべてのイベント グリッド フィルターを一覧表示します。 |
> | Microsoft.SignalRService/SignalR/eventGridFilters/write | 指定されたパラメーターで SignalR のイベント グリッド フィルターを作成または更新します。 |
> | Microsoft.SignalRService/SignalR/eventGridFilters/delete | SignalR からイベント グリッドのフィルターを削除します。 |
> | Microsoft.SignalRService/SignalR/privateEndpointConnectionProxies/validate/action | プライベート エンドポイント接続プロキシを検証します |
> | Microsoft.SignalRService/SignalR/privateEndpointConnectionProxies/write | プライベート エンドポイント接続プロキシを作成します |
> | Microsoft.SignalRService/SignalR/privateEndpointConnectionProxies/read | プライベート エンドポイント接続プロキシを読み取ります |
> | Microsoft.SignalRService/SignalR/privateEndpointConnectionProxies/delete | プライベート エンドポイント接続プロキシを削除します |
> | Microsoft.SignalRService/SignalR/privateEndpointConnections/write | プライベート エンドポイント接続を承認または拒否します |
> | Microsoft.SignalRService/SignalR/privateEndpointConnections/read | プライベート エンドポイント接続を読み取ります |
> | Microsoft.SignalRService/SignalR/privateLinkResources/read | すべての SignalR Private Link リソースを一覧表示します |
> | **DataAction** | **説明** |
> | Microsoft.SignalRService/SignalR/auth/accessKey/action | ClientToken に署名するための一時的な AccessKey を生成します。 |
> | Microsoft.SignalRService/SignalR/auth/clientToken/action | クライアント接続を開始するための ClientToken を生成します。 |
> | Microsoft.SignalRService/SignalR/clientConnection/send/action | クライアント接続にメッセージを直接送信します。 |
> | Microsoft.SignalRService/SignalR/clientConnection/read | クライアント接続の存在を確認します。 |
> | Microsoft.SignalRService/SignalR/clientConnection/write | クライアント接続を閉じます。 |
> | Microsoft.SignalRService/SignalR/group/send/action | メッセージをグループにブロードキャストします。 |
> | Microsoft.SignalRService/SignalR/group/read | グループの存在、またはグループ内のユーザーの存在を確認します。 |
> | Microsoft.SignalRService/SignalR/group/write | グループに参加するか、グループから脱退します。 |
> | Microsoft.SignalRService/SignalR/hub/send/action | ハブ内のすべてのクライアント接続にメッセージをブロードキャストします。 |
> | Microsoft.SignalRService/SignalR/serverConnection/write | サーバー接続を開始します。 |
> | Microsoft.SignalRService/SignalR/user/send/action | 複数のクライアント接続で構成されている可能性のあるユーザーにメッセージを送信します。 |
> | Microsoft.SignalRService/SignalR/user/read | ユーザーの存在を確認します。 |
> | Microsoft.SignalRService/SignalR/user/write |  |

### <a name="microsoftweb"></a>microsoft.web

Azure サービス:[App Service](../app-service/index.yml)、[Azure Functions](../azure-functions/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.web/unregister/action | サブスクリプションへの Microsoft.Web リソース プロバイダーの登録を解除します。 |
> | microsoft.web/validate/action | 検証します。 |
> | microsoft.web/register/action | Microsoft.Web リソース プロバイダーをサブスクリプションに登録します。 |
> | microsoft.web/verifyhostingenvironmentvnet/action | ホスティング環境の Vnet を検証します。 |
> | microsoft.web/apimanagementaccounts/apiacls/read | API Management アカウントの API ACL を取得します。 |
> | microsoft.web/apimanagementaccounts/apis/read | API Management アカウントの API を取得します。 |
> | microsoft.web/apimanagementaccounts/apis/delete | API Management アカウントの API を削除します。 |
> | microsoft.web/apimanagementaccounts/apis/write | API Management アカウントの API を更新します。 |
> | microsoft.web/apimanagementaccounts/apis/apiacls/delete | API Management アカウント API の API ACL を削除します。 |
> | microsoft.web/apimanagementaccounts/apis/apiacls/read | API Management アカウント API の API ACL を取得します。 |
> | microsoft.web/apimanagementaccounts/apis/apiacls/write | API Management アカウント API の API ACL を更新します。 |
> | microsoft.web/apimanagementaccounts/apis/connectionacls/read | API Management アカウントの API の Connectionacls を取得します。 |
> | microsoft.web/apimanagementaccounts/apis/connections/read | API Management アカウントの API 接続を取得します。 |
> | microsoft.web/apimanagementaccounts/apis/connections/confirmconsentcode/action | 同意コード API Management アカウントの API 接続を確認します。 |
> | microsoft.web/apimanagementaccounts/apis/connections/delete | API Management アカウントの API 接続を削除します。 |
> | microsoft.web/apimanagementaccounts/apis/connections/getconsentlinks/action | API Management アカウントの API 接続の同意リンクを取得します。 |
> | microsoft.web/apimanagementaccounts/apis/connections/write | API Management アカウントの API 接続を更新します。 |
> | microsoft.web/apimanagementaccounts/apis/connections/listconnectionkeys/action | 接続キー API Management アカウントの API 接続を一覧表示します。 |
> | microsoft.web/apimanagementaccounts/apis/connections/listsecrets/action | シークレット API Management アカウントの API 接続を一覧表示します。 |
> | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/delete | API Management アカウントの API 接続の Connectionacls を削除します。 |
> | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/read | API Management アカウントの API 接続の Connectionacls を取得します。 |
> | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/write | API Management アカウントの API 接続の Connectionacls を更新します。 |
> | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/delete | API Management アカウントの API のローカライズされた定義を削除します。 |
> | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/read | API Management アカウントの API のローカライズされた定義を取得します。 |
> | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/write | API Management アカウントの API のローカライズされた定義を更新します。 |
> | microsoft.web/apimanagementaccounts/connectionacls/read | API Management アカウントの Connectionacls を取得します。 |
> | microsoft.web/availablestacks/read | 使用可能なスタックを取得します。 |
> | Microsoft.Web/certificates/Read | 証明書の一覧を取得します。 |
> | Microsoft.Web/certificates/Write | 新しい証明書を追加するか、既存の証明書を更新します。 |
> | Microsoft.Web/certificates/Delete | 既存の証明書を削除します。 |
> | microsoft.web/checknameavailability/read | リソース名を使用できるかどうかを確認します。 |
> | microsoft.web/classicmobileservices/read | 従来の Mobile Services を取得します。 |
> | Microsoft.Web/connectionGateways/Read | 接続ゲートウェイの一覧を取得します。 |
> | Microsoft.Web/connectionGateways/Write | 接続ゲートウェイを作成または更新します。 |
> | Microsoft.Web/connectionGateways/Delete | 接続ゲートウェイを削除します。 |
> | Microsoft.Web/connectionGateways/Move/Action | 接続ゲートウェイを移動します。 |
> | Microsoft.Web/connectionGateways/Join/Action | 接続ゲートウェイを接続します。 |
> | Microsoft.Web/connectionGateways/Associate/Action | 接続ゲートウェイと関連付けます。 |
> | Microsoft.Web/connectionGateways/ListStatus/Action | 接続ゲートウェイの状態を一覧表示します。 |
> | Microsoft.Web/connections/Read | 接続の一覧を取得します。 |
> | Microsoft.Web/connections/Write | 接続を作成または更新します。 |
> | Microsoft.Web/connections/Delete | 接続を削除します。 |
> | Microsoft.Web/connections/Move/Action | 接続を移動します。 |
> | Microsoft.Web/connections/Join/Action | 接続に参加します。 |
> | microsoft.web/connections/confirmconsentcode/action | 接続の同意コードを確認します。 |
> | microsoft.web/connections/listconsentlinks/action | 接続の同意リンクを一覧表示します。 |
> | Microsoft.Web/customApis/Read | カスタム API の一覧を取得します。 |
> | Microsoft.Web/customApis/Write | カスタム API を作成または更新します。 |
> | Microsoft.Web/customApis/Delete | カスタム API を削除します。 |
> | Microsoft.Web/customApis/Move/Action | カスタム API を移動します。 |
> | Microsoft.Web/customApis/Join/Action | カスタム API を結合します。 |
> | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | WSDL から API の定義を抽出します。 |
> | Microsoft.Web/customApis/listWsdlInterfaces/Action | カスタム API の WSDL インターフェイスを一覧表示します。 |
> | Microsoft.Web/customhostnames/Read | サブスクリプションに割り当てられているカスタム ホスト名を取得します。 |
> | Microsoft.Web/customhostnames/sites/Read | カスタム ホスト名に割り当てられているサイトの名前を取得します。 |
> | Microsoft.Web/deletedSites/Read | 削除された Web アプリのプロパティを取得します。 |
> | microsoft.web/deploymentlocations/read | デプロイの場所を取得します。 |
> | Microsoft.Web/geoRegions/Read | 地理的リージョンの一覧を取得します。 |
> | Microsoft.Web/hostingEnvironments/Read | App Service Environment のプロパティを取得します。 |
> | Microsoft.Web/hostingEnvironments/Write | 新しい App Service Environment を作成するか、既存の App Service Environment を更新します。 |
> | Microsoft.Web/hostingEnvironments/Delete | App Service Environment を削除します。 |
> | Microsoft.Web/hostingEnvironments/Join/Action | App Service Environment に参加します |
> | Microsoft.Web/hostingEnvironments/reboot/Action | App Service Environment 内のすべてのマシンを再起動します。 |
> | Microsoft.Web/hostingEnvironments/PrivateEndpointConnectionsApproval/action | プライベート エンドポイント接続を承認します |
> | microsoft.web/hostingenvironments/resume/action | ホスティング環境を再開します。 |
> | microsoft.web/hostingenvironments/suspend/action | ホスティング環境を中断します。 |
> | microsoft.web/hostingenvironments/capacities/read | ホスティング環境の容量を取得します。 |
> | microsoft.web/hostingenvironments/detectors/read | ホスティング環境の検出機能を取得します。 |
> | microsoft.web/hostingenvironments/diagnostics/read | ホスティング環境の診断を取得します。 |
> | Microsoft.Web/hostingEnvironments/eventGridFilters/delete | ホスティング環境で Event Grid フィルターを削除します。 |
> | Microsoft.Web/hostingEnvironments/eventGridFilters/read | ホスティング環境で Event Grid フィルターを取得します。 |
> | Microsoft.Web/hostingEnvironments/eventGridFilters/write | ホスティング環境に Event Grid フィルターを配置します。 |
> | microsoft.web/hostingenvironments/health/read | App Service Environment の正常性の詳細を取得します。 |
> | microsoft.web/hostingenvironments/inboundnetworkdependenciesendpoints/read | すべての受信依存関係のネットワーク エンドポイントを取得します。 |
> | microsoft.web/hostingenvironments/metricdefinitions/read | ホスティング環境のメトリック定義を取得します。 |
> | Microsoft.Web/hostingEnvironments/multiRolePools/Read | App Service Environment のフロントエンド プールのプロパティを取得します。 |
> | Microsoft.Web/hostingEnvironments/multiRolePools/Write | App Service Environment に新しいフロントエンド プールを作成するか、既存のフロントエンド プールを更新します。 |
> | microsoft.web/hostingenvironments/multirolepools/metricdefinitions/read | ホスティング環境のマルチロール プールのメトリック定義を取得します。 |
> | microsoft.web/hostingenvironments/multirolepools/metrics/read | ホスティング環境のマルチロール プールのメトリックを取得します。 |
> | microsoft.web/hostingenvironments/multirolepools/skus/read | ホスティング環境のマルチロール プールの SKU を取得します。 |
> | microsoft.web/hostingenvironments/multirolepools/usages/read | ホスティング環境のマルチロール プールの使用状況を取得します。 |
> | microsoft.web/hostingenvironments/operations/read | ホスティング環境の操作を取得します。 |
> | microsoft.web/hostingenvironments/outboundnetworkdependenciesendpoints/read | すべての送信依存関係のネットワーク エンドポイントを取得します。 |
> | Microsoft.Web/hostingEnvironments/privateEndpointConnections/Write | プライベート エンドポイント接続を承認または拒否します。 |
> | Microsoft.Web/hostingEnvironments/privateEndpointConnections/Read | プライベート エンドポイント接続またはプライベート エンドポイント接続の一覧を取得します。 |
> | Microsoft.Web/hostingEnvironments/privateEndpointConnections/Delete | プライベート エンドポイント接続を削除します。 |
> | Microsoft.Web/hostingEnvironments/privateLinkResources/Read | Private Link リソースを取得します。 |
> | microsoft.web/hostingenvironments/serverfarms/read | ホスティング環境の App Service プランを取得します。 |
> | microsoft.web/hostingenvironments/sites/read | ホスティング環境の Web アプリを取得します。 |
> | microsoft.web/hostingenvironments/usages/read | ホスティング環境の使用状況を取得します。 |
> | Microsoft.Web/hostingEnvironments/workerPools/Read | App Service Environment のワーカー プールのプロパティを取得します。 |
> | Microsoft.Web/hostingEnvironments/workerPools/Write | App Service Environment に新しいワーカー プールを作成するか、既存のワーカー プールを更新します。 |
> | microsoft.web/hostingenvironments/workerpools/metricdefinitions/read | ホスティング環境のワーカー プールのメトリック定義を取得します。 |
> | microsoft.web/hostingenvironments/workerpools/metrics/read | ホスティング環境のワーカー プールのメトリックを取得します。 |
> | microsoft.web/hostingenvironments/workerpools/skus/read | ホスティング環境のワーカー プールの SKU を取得します。 |
> | microsoft.web/hostingenvironments/workerpools/usages/read | ホスティング環境のワーカー プールの使用状況を取得します。 |
> | microsoft.web/ishostingenvironmentnameavailable/read | ホスティング環境名を使用できるかどうかを取得します。 |
> | microsoft.web/ishostnameavailable/read | ホスト名を使用できるかどうかを確認します。 |
> | microsoft.web/isusernameavailable/read | ユーザー名を使用できるかどうかを確認します。 |
> | Microsoft.Web/kubeEnvironments/read | Kubernetes 環境のプロパティを取得します |
> | Microsoft.Web/kubeEnvironments/write | Kubernetes 環境を作成するか、既存の環境を更新します |
> | Microsoft.Web/kubeEnvironments/delete | Kubernetes 環境を削除します |
> | Microsoft.Web/kubeEnvironments/join/action | Kubernetes 環境に参加します |
> | Microsoft.Web/kubeEnvironments/operations/read | Kubernetes 環境の操作を取得します |
> | Microsoft.Web/listSitesAssignedToHostName/Read | ホスト名に割り当てられたサイトの名前を取得します。 |
> | microsoft.web/locations/extractapidefinitionfromwsdl/action | 場所の WSDL から API の定義を抽出します。 |
> | microsoft.web/locations/listwsdlinterfaces/action | 場所の WSDL インターフェイスを一覧表示します。 |
> | microsoft.web/locations/deleteVirtualNetworkOrSubnets/action | 場所の Vnet またはサブネット削除通知。 |
> | Microsoft.Web/locations/previewstaticsiteworkflowfile/action | 静的サイト ワークフロー ファイルをプレビューします |
> | microsoft.web/locations/apioperations/read | API 操作の場所を取得します。 |
> | microsoft.web/locations/connectiongatewayinstallations/read | 接続ゲートウェイのインストールの場所を取得します。 |
> | microsoft.web/locations/managedapis/read | マネージド API の場所を取得します。 |
> | Microsoft.Web/locations/managedapis/Join/Action | マネージド API を結合します。 |
> | microsoft.web/locations/managedapis/apioperations/read | 場所のマネージ API 操作を取得します。 |
> | microsoft.web/locations/operationResults/read | 操作を取得します。 |
> | microsoft.web/locations/operations/read | 操作を取得します。 |
> | microsoft.web/operations/read | 操作を取得します。 |
> | microsoft.web/publishingusers/read | 発行ユーザーを取得します。 |
> | microsoft.web/publishingusers/write | 発行ユーザーを更新します。 |
> | Microsoft.Web/recommendations/Read | サブスクリプションの推奨事項の一覧を取得します。 |
> | microsoft.web/resourcehealthmetadata/read | Resource Health のメタデータを取得します。 |
> | Microsoft.Web/serverfarms/Read | App Service プランのプロパティを取得します。 |
> | Microsoft.Web/serverfarms/Write | 新しい App Service プランを作成するか、既存の App Service プランを更新します。 |
> | Microsoft.Web/serverfarms/Delete | 既存の App Service プランの削除 |
> | Microsoft.Web/serverfarms/Join/Action | App Service プランに参加します |
> | Microsoft.Web/serverfarms/restartSites/Action | App Service プランのすべての Web アプリを再起動します。 |
> | microsoft.web/serverfarms/capabilities/read | App Service プランの機能を取得します。 |
> | Microsoft.Web/serverfarms/eventGridFilters/delete | サーバー ファームで Event Grid フィルターを削除します。 |
> | Microsoft.Web/serverfarms/eventGridFilters/read | サーバー ファームで Event Grid フィルターを取得します。 |
> | Microsoft.Web/serverfarms/eventGridFilters/write | サーバー ファームで Event Grid フィルターを取得します。 |
> | microsoft.web/serverfarms/firstpartyapps/settings/delete | App Service プランのファースト パーティ アプリの設定を削除します。 |
> | microsoft.web/serverfarms/firstpartyapps/settings/read | App Service プランのファースト パーティ アプリの設定を取得します。 |
> | microsoft.web/serverfarms/firstpartyapps/settings/write | App Service プランのファースト パーティ アプリの設定を更新します。 |
> | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/read | App Service プランのハイブリッド接続名前空間のリレーを取得します。 |
> | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/delete | App Service プランのハイブリッド接続名前空間のリレーを削除します。 |
> | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/sites/read | App Service プランのハイブリッド接続の名前空間のリレーの Web アプリを取得します。 |
> | microsoft.web/serverfarms/hybridconnectionplanlimits/read | App Service プランのハイブリッド接続プランの制限を取得します。 |
> | microsoft.web/serverfarms/hybridconnectionrelays/read | App Service プランのハイブリッド接続リレーを取得します。 |
> | microsoft.web/serverfarms/metricdefinitions/read | App Service プランのメトリック定義を取得します。 |
> | microsoft.web/serverfarms/metrics/read | App Service プランのメトリックを取得します。 |
> | microsoft.web/serverfarms/operationresults/read | App Service プランの操作の結果を取得します。 |
> | microsoft.web/serverfarms/sites/read | App Service プランの Web アプリを取得します。 |
> | microsoft.web/serverfarms/skus/read | App Service プランの SKU を取得します。 |
> | microsoft.web/serverfarms/usages/read | App Service プランの使用状況を取得します。 |
> | microsoft.web/serverfarms/virtualnetworkconnections/read | App Service プランの仮想ネットワーク接続を取得します。 |
> | microsoft.web/serverfarms/virtualnetworkconnections/gateways/write | App Service プランの仮想ネットワーク接続のゲートウェイを更新します。 |
> | microsoft.web/serverfarms/virtualnetworkconnections/routes/delete | App Service プランの仮想ネットワーク接続のルートを削除します。 |
> | microsoft.web/serverfarms/virtualnetworkconnections/routes/read | App Service プランの仮想ネットワーク接続のルートを取得します。 |
> | microsoft.web/serverfarms/virtualnetworkconnections/routes/write | App Service プランの仮想ネットワーク接続のルートを更新します。 |
> | microsoft.web/serverfarms/workers/reboot/action | App Service プランの worker を再起動します。 |
> | Microsoft.Web/sites/Read | Web アプリのプロパティを取得します。 |
> | Microsoft.Web/sites/Write | 新しい Web アプリを作成するか、既存の Web アプリを更新します。 |
> | Microsoft.Web/sites/Delete | 既存の Web アプリの削除 |
> | Microsoft.Web/sites/backup/Action | Web アプリの新しいバックアップを作成します。 |
> | Microsoft.Web/sites/publishxml/Action | Web アプリの発行プロファイル XML を取得します。 |
> | Microsoft.Web/sites/publish/Action | Web アプリを発行します。 |
> | Microsoft.Web/sites/restart/Action | Web アプリを再起動します。 |
> | Microsoft.Web/sites/start/Action | Web アプリを起動します。 |
> | Microsoft.Web/sites/stop/Action | Web アプリを停止します。 |
> | Microsoft.Web/sites/slotsswap/Action | Web アプリのデプロイ スロットを入れ替えます。 |
> | Microsoft.Web/sites/slotsdiffs/Action | Web アプリとスロットの構成の違いを取得します。 |
> | Microsoft.Web/sites/applySlotConfig/Action | ターゲット スロットの Web アプリ スロット構成を現在の Web アプリに適用します。 |
> | Microsoft.Web/sites/resetSlotConfig/Action | Web アプリの構成をリセットします。 |
> | Microsoft.Web/sites/PrivateEndpointConnectionsApproval/action | プライベート エンドポイント接続を承認します |
> | microsoft.web/sites/functions/action | Web アプリの関数。 |
> | microsoft.web/sites/listsyncfunctiontriggerstatus/action | 同期関数のトリガーの状態を一覧表示します。 |
> | microsoft.web/sites/networktrace/action | Web アプリのネットワーク トレース。 |
> | microsoft.web/sites/newpassword/action | Web アプリの Newpassword。 |
> | microsoft.web/sites/sync/action | Web アプリを同期します。 |
> | microsoft.web/sites/migratemysql/action | MySql の Web アプリを移行します。 |
> | microsoft.web/sites/recover/action | Web アプリを復旧します。 |
> | microsoft.web/sites/restoresnapshot/action | Web アプリのスナップショットを復元します。 |
> | microsoft.web/sites/restorefromdeletedapp/action | 削除されたアプリから Web アプリを復元します。 |
> | microsoft.web/sites/syncfunctiontriggers/action | 関数トリガーを同期します。 |
> | microsoft.web/sites/backups/action | Azure Storage 内の BLOB から復元できる既存のアプリのバックアップを検出します。 |
> | microsoft.web/sites/containerlogs/action | Web アプリの zip 圧縮されたコンテナーのログを取得します。 |
> | microsoft.web/sites/restorefrombackupblob/action | バックアップ BLOB から Web アプリを復元します。 |
> | microsoft.web/sites/analyzecustomhostname/read | カスタム ホスト名を分析します。 |
> | microsoft.web/sites/backup/read | Web アプリのバックアップを取得します。 |
> | microsoft.web/sites/backup/write | Web アプリのバックアップを更新します。 |
> | Microsoft.Web/sites/backups/Read | Web アプリのバックアップのプロパティを取得します。 |
> | microsoft.web/sites/backups/list/action | Web アプリのバックアップを一覧表示します。 |
> | microsoft.web/sites/backups/restore/action | Web アプリのバックアップを復元します。 |
> | microsoft.web/sites/backups/delete | Web アプリのバックアップを削除します。 |
> | microsoft.web/sites/backups/write | Web アプリのバックアップを更新します。 |
> | Microsoft.Web/sites/basicPublishingCredentialsPolicies/Read | Web アプリで許可されている発行方法を一覧表示します |
> | Microsoft.Web/sites/basicPublishingCredentialsPolicies/ftp/Read | FTP 発行資格情報が Web アプリに対して許可されているかどうかを取得します |
> | Microsoft.Web/sites/basicPublishingCredentialsPolicies/ftp/Write | FTP 発行資格情報が Web アプリに対して許可されているかどうかを更新します |
> | Microsoft.Web/sites/basicPublishingCredentialsPolicies/scm/Read | SCM 発行資格情報が Web アプリに対して許可されているかどうかを取得します |
> | Microsoft.Web/sites/basicPublishingCredentialsPolicies/scm/Write | SCM 発行資格情報が Web アプリに対して許可されているかどうかを更新します |
> | Microsoft.Web/sites/config/Read | Web アプリの構成設定を取得します。 |
> | Microsoft.Web/sites/config/list/Action | Web アプリのセキュリティに関する設定 (発行資格情報、アプリ設定、接続文字列など) を一覧表示します。 |
> | Microsoft.Web/sites/config/Write | Web アプリの構成設定を更新します。 |
> | microsoft.web/sites/config/delete | Web アプリの構成を削除します。 |
> | microsoft.web/sites/config/snapshots/read | Web アプリ構成のスナップショットを取得します。 |
> | microsoft.web/sites/containerlogs/download/action | Web アプリのコンテナー ログをダウンロードします。 |
> | microsoft.web/sites/continuouswebjobs/delete | Web アプリの継続的な Web ジョブを削除します。 |
> | microsoft.web/sites/continuouswebjobs/read | Web アプリの継続的な Web ジョブを取得します。 |
> | microsoft.web/sites/continuouswebjobs/start/action | Web アプリの継続的な Web ジョブを開始します。 |
> | microsoft.web/sites/continuouswebjobs/stop/action | Web アプリの継続的な Web ジョブを停止します。 |
> | microsoft.web/sites/deployments/delete | Web アプリのデプロイを削除します。 |
> | microsoft.web/sites/deployments/read | Web アプリのデプロイを取得します。 |
> | microsoft.web/sites/deployments/write | Web アプリのデプロイを更新します。 |
> | microsoft.web/sites/deployments/log/read | Web アプリのデプロイのログを取得します。 |
> | microsoft.web/sites/detectors/read | Web アプリの検出機能を取得します。 |
> | microsoft.web/sites/diagnostics/read | Web アプリ診断のカテゴリを取得します。 |
> | microsoft.web/sites/diagnostics/analyses/read | Web アプリ診断の分析を取得します。 |
> | microsoft.web/sites/diagnostics/analyses/execute/Action | Web アプリ診断の分析を実行します。 |
> | microsoft.web/sites/diagnostics/aspnetcore/read | ASP.NET Core アプリの Web アプリ診断を取得します。 |
> | microsoft.web/sites/diagnostics/autoheal/read | Web アプリ診断の Autoheal を取得します。 |
> | microsoft.web/sites/diagnostics/deployment/read | Web アプリ診断のデプロイを取得します。 |
> | microsoft.web/sites/diagnostics/deployments/read | Web アプリ診断のデプロイを取得します。 |
> | microsoft.web/sites/diagnostics/detectors/read | Web アプリ診断の検出機能を取得します。 |
> | microsoft.web/sites/diagnostics/detectors/execute/Action | Web アプリ診断の検出機能を実行します。 |
> | microsoft.web/sites/diagnostics/failedrequestsperuri/read | URI ごとに Web アプリ診断の失敗した要求を取得します。 |
> | microsoft.web/sites/diagnostics/frebanalysis/read | Web アプリの診断の FREB 分析を取得します。 |
> | microsoft.web/sites/diagnostics/loganalyzer/read | Web アプリ診断ログ アナライザーを取得します。 |
> | microsoft.web/sites/diagnostics/runtimeavailability/read | Web アプリの診断の実行時の可用性を取得します。 |
> | microsoft.web/sites/diagnostics/servicehealth/read | Web アプリの診断のサービス正常性を取得します。 |
> | microsoft.web/sites/diagnostics/sitecpuanalysis/read | Web アプリ診断のサイト CPU 分析を取得します。 |
> | microsoft.web/sites/diagnostics/sitecrashes/read | Web アプリ診断のサイト クラッシュを取得します。 |
> | microsoft.web/sites/diagnostics/sitelatency/read | Web アプリ診断のサイト待機時間を取得します。 |
> | microsoft.web/sites/diagnostics/sitememoryanalysis/read | Web アプリ診断のサイト メモリ分析を取得します。 |
> | microsoft.web/sites/diagnostics/siterestartsettingupdate/read | Web アプリ診断のサイト再起動設定更新を取得します。 |
> | microsoft.web/sites/diagnostics/siterestartuserinitiated/read | Web アプリ診断のサイト再起動ユーザー開始を取得します。 |
> | microsoft.web/sites/diagnostics/siteswap/read | Web アプリ診断のサイト スワップを取得します。 |
> | microsoft.web/sites/diagnostics/threadcount/read | Web アプリ診断のスレッド数を取得します。 |
> | microsoft.web/sites/diagnostics/workeravailability/read | Web アプリの診断の Workeravailability を取得します。 |
> | microsoft.web/sites/diagnostics/workerprocessrecycle/read | Web アプリの診断のワーカー プロセスのリサイクルを取得します。 |
> | microsoft.web/sites/domainownershipidentifiers/read | Web アプリのドメイン所有権識別子を取得します。 |
> | microsoft.web/sites/domainownershipidentifiers/write | Web アプリのドメイン所有権識別子を更新します。 |
> | Microsoft.Web/sites/eventGridFilters/delete | Web アプリから Event Grid フィルターを削除します。 |
> | Microsoft.Web/sites/eventGridFilters/read | Web アプリから Event Grid フィルターを取得します。 |
> | Microsoft.Web/sites/eventGridFilters/write | Web アプリに Event Grid フィルターを配置します。 |
> | microsoft.web/sites/extensions/delete | Web アプリのサイト拡張機能を削除します。 |
> | microsoft.web/sites/extensions/read | Web アプリのサイト拡張機能を取得します。 |
> | microsoft.web/sites/extensions/write | Web アプリのサイト拡張機能を更新します。 |
> | microsoft.web/sites/functions/delete | Web アプリの関数を削除します。 |
> | microsoft.web/sites/functions/listsecrets/action | 関数のシークレットを一覧表示します。 |
> | microsoft.web/sites/functions/listkeys/action | 関数キーを一覧表示します。 |
> | microsoft.web/sites/functions/read | Web アプリの関数を取得します。 |
> | microsoft.web/sites/functions/write | Web アプリの関数を更新します。 |
> | microsoft.web/sites/functions/keys/write | 関数キーを更新します。 |
> | microsoft.web/sites/functions/keys/delete | 関数キーを削除します。 |
> | microsoft.web/sites/functions/masterkey/read | Web アプリの関数のマスター キーを取得します。 |
> | microsoft.web/sites/functions/token/read | Web アプリの関数のトークンを取得します。 |
> | microsoft.web/sites/host/listkeys/action | Functions ホスト キーを一覧表示します。 |
> | microsoft.web/sites/host/sync/action | 関数トリガーを同期します。 |
> | microsoft.web/sites/host/listsyncstatus/action | 同期関数のトリガーの状態を一覧表示します。 |
> | microsoft.web/sites/host/functionkeys/write | Functions ホストの関数キーを更新します。 |
> | microsoft.web/sites/host/functionkeys/delete | Functions ホストの関数キーを削除します。 |
> | microsoft.web/sites/host/systemkeys/write | Functions ホストのシステム キーを更新します。 |
> | microsoft.web/sites/host/systemkeys/delete | Functions ホストのシステム キーを削除します。 |
> | microsoft.web/sites/hostnamebindings/delete | Web アプリのホスト名バインドを削除します。 |
> | microsoft.web/sites/hostnamebindings/read | Web アプリのホスト名バインドを取得します。 |
> | microsoft.web/sites/hostnamebindings/write | Web アプリのホスト名バインドを更新します。 |
> | Microsoft.Web/sites/hostruntime/host/action | トリガーの同期、関数の追加、関数の呼び出し、関数の削除など、Function App の実行時アクションを実行します。 |
> | microsoft.web/sites/hostruntime/functions/keys/read | Web アプリのホストランタイム関数のキーを取得します。 |
> | microsoft.web/sites/hostruntime/host/read | Web アプリのホストランタイムのホストを取得します。 |
> | Microsoft.Web/sites/hostruntime/host/_master/read | 管理操作用の Function App のマスター キーを取得します |
> | microsoft.web/sites/hybridconnection/delete | Web アプリのハイブリッド接続を削除します。 |
> | microsoft.web/sites/hybridconnection/read | Web アプリのハイブリッド接続を取得します。 |
> | microsoft.web/sites/hybridconnection/write | Web アプリのハイブリッド接続を更新します。 |
> | microsoft.web/sites/hybridconnectionnamespaces/relays/delete | Web アプリのハイブリッド接続名前空間リレーを削除します。 |
> | microsoft.web/sites/hybridconnectionnamespaces/relays/listkeys/action | Web アプリのハイブリッド接続名前空間リレーのキーを一覧表示します。 |
> | microsoft.web/sites/hybridconnectionnamespaces/relays/write | Web アプリのハイブリッド接続名前空間リレーを更新します。 |
> | microsoft.web/sites/hybridconnectionnamespaces/relays/read | Web アプリのハイブリッド接続名前空間リレーを取得します。 |
> | microsoft.web/sites/hybridconnectionrelays/read | Web アプリのハイブリッド接続リレーを取得します。 |
> | microsoft.web/sites/instances/read | Web アプリのインスタンスを取得します。 |
> | microsoft.web/sites/instances/deployments/read | Web アプリのインスタンスのデプロイを取得します。 |
> | microsoft.web/sites/instances/deployments/delete | Web アプリのインスタンスデプロイを削除します。 |
> | microsoft.web/sites/instances/extensions/read | Web アプリのインスタンス拡張機能を取得します。 |
> | microsoft.web/sites/instances/extensions/log/read | Web アプリのインスタンス拡張機能ログを取得します。 |
> | microsoft.web/sites/instances/extensions/processes/read | Web アプリのインスタンス拡張機能のプロセスを取得します。 |
> | microsoft.web/sites/instances/processes/delete | Web アプリのインスタンスのプロセスを削除します。 |
> | microsoft.web/sites/instances/processes/read | Web アプリのインスタンスのプロセスを取得します。 |
> | microsoft.web/sites/instances/processes/modules/read | Web アプリのインスタンスのプロセス モジュールを取得します。 |
> | microsoft.web/sites/instances/processes/threads/read | Web アプリのインスタンスのプロセス スレッドを取得します。 |
> | microsoft.web/sites/metricdefinitions/read | Web アプリのメトリック定義を取得します。 |
> | microsoft.web/sites/metrics/read | Web アプリのメトリックを取得します。 |
> | microsoft.web/sites/metricsdefinitions/read | Web アプリのメトリック定義を取得します。 |
> | microsoft.web/sites/migratemysql/read | Web アプリ移行 MySql を取得します。 |
> | microsoft.web/sites/networkConfig/read | App Service のネットワーク構成を取得します。 |
> | microsoft.web/sites/networkConfig/write | App Service のネットワーク構成を更新します。 |
> | microsoft.web/sites/networkConfig/delete | App Service のネットワーク構成を削除します。 |
> | microsoft.web/sites/networktraces/operationresults/read | Web Apps のネットワーク トレース操作の結果を取得します |
> | microsoft.web/sites/operationresults/read | Web アプリの操作の結果を取得します。 |
> | microsoft.web/sites/operations/read | Web アプリの操作を取得します。 |
> | microsoft.web/sites/perfcounters/read | Web アプリのパフォーマンス カウンターを取得します。 |
> | microsoft.web/sites/premieraddons/delete | Web アプリのプレミア アドオンを削除します。 |
> | microsoft.web/sites/premieraddons/read | Web アプリのプレミア アドオンを取得します。 |
> | microsoft.web/sites/premieraddons/write | Web アプリのプレミア アドオンを更新します。 |
> | microsoft.web/sites/privateaccess/read | プライベート サイト アクセスの有効化と、サイトにアクセスできる承認済み仮想ネットワークに関するデータを取得します。 |
> | Microsoft.Web/sites/privateEndpointConnections/Write | プライベート エンドポイント接続を承認または拒否します。 |
> | Microsoft.Web/sites/privateEndpointConnections/Read | プライベート エンドポイント接続またはプライベート エンドポイント接続の一覧を取得します。 |
> | Microsoft.Web/sites/privateEndpointConnections/Delete | プライベート エンドポイント接続を削除します。 |
> | Microsoft.Web/sites/privateLinkResources/Read | Private Link リソースを取得します。 |
> | microsoft.web/sites/processes/read | Web アプリのプロセスを取得します。 |
> | microsoft.web/sites/processes/modules/read | Web Apps のプロセス モジュールを取得します。 |
> | microsoft.web/sites/processes/threads/read | Web Apps のプロセス スレッドを取得します。 |
> | microsoft.web/sites/publiccertificates/delete | Web アプリのパブリック証明書を削除します。 |
> | microsoft.web/sites/publiccertificates/read | Web アプリのパブリック証明書を取得します。 |
> | microsoft.web/sites/publiccertificates/write | Web アプリのパブリック証明書を更新します。 |
> | microsoft.web/sites/publishxml/read | Web アプリの発行 XML を取得します。 |
> | microsoft.web/sites/recommendationhistory/read | Web アプリの推奨事項の履歴を取得します。 |
> | Microsoft.Web/sites/recommendations/Read | Web アプリの推奨事項の一覧を取得します。 |
> | microsoft.web/sites/recommendations/disable/action | Web アプリの推奨事項を無効にします。 |
> | microsoft.web/sites/resourcehealthmetadata/read | Web アプリの Resource Health のメタデータを取得します。 |
> | microsoft.web/sites/restore/read | Web アプリの復元を取得します。 |
> | microsoft.web/sites/restore/write | Web アプリを復元します。 |
> | microsoft.web/sites/siteextensions/delete | Web アプリのサイト拡張機能を削除します。 |
> | microsoft.web/sites/siteextensions/read | Web アプリのサイト拡張機能を取得します。 |
> | microsoft.web/sites/siteextensions/write | Web アプリのサイト拡張機能を更新します。 |
> | Microsoft.Web/sites/slots/Write | 新しい Web アプリ スロットを作成するか、既存の Web アプリ スロットを更新します。 |
> | Microsoft.Web/sites/slots/Delete | 既存の Web アプリ スロットを削除します。 |
> | Microsoft.Web/sites/slots/backup/Action | Web アプリ スロットの新しいバックアップを作成します。 |
> | Microsoft.Web/sites/slots/publishxml/Action | Web アプリ スロットの発行プロファイル XML を取得します。 |
> | Microsoft.Web/sites/slots/publish/Action | Web アプリ スロットを発行します。 |
> | Microsoft.Web/sites/slots/restart/Action | Web アプリ スロットを再起動します。 |
> | Microsoft.Web/sites/slots/start/Action | Web アプリ スロットを起動します。 |
> | Microsoft.Web/sites/slots/stop/Action | Web アプリ スロットを停止します。 |
> | Microsoft.Web/sites/slots/slotsswap/Action | Web アプリのデプロイ スロットを入れ替えます。 |
> | Microsoft.Web/sites/slots/slotsdiffs/Action | Web アプリとスロットの構成の違いを取得します。 |
> | Microsoft.Web/sites/slots/applySlotConfig/Action | ターゲット スロットの Web アプリ スロット構成を現在のスロットに適用します。 |
> | Microsoft.Web/sites/slots/resetSlotConfig/Action | Web アプリ スロット構成をリセットします。 |
> | Microsoft.Web/sites/slots/Read | Web アプリのデプロイ スロットのプロパティを取得します。 |
> | microsoft.web/sites/slots/newpassword/action | Web アプリとスロットの Newpassword。 |
> | microsoft.web/sites/slots/sync/action | Web アプリとスロットを同期します。 |
> | microsoft.web/sites/slots/networktrace/action | Web アプリ スロットのネットワーク トレースです。 |
> | microsoft.web/sites/slots/recover/action | Web アプリ スロットを復旧します。 |
> | microsoft.web/sites/slots/restoresnapshot/action | Web アプリ スロットのスナップショットを復元します。 |
> | microsoft.web/sites/slots/restorefromdeletedapp/action | 削除されたアプリから Web アプリ スロットを復元します。 |
> | microsoft.web/sites/slots/backups/action | Web Apps のスロット バックアップを検出します。 |
> | microsoft.web/sites/slots/containerlogs/action | Web アプリ スロットの zip 圧縮されたコンテナー ログを取得します。 |
> | microsoft.web/sites/slots/restorefrombackupblob/action | バックアップ BLOB から Web Apps スロットを復元します。 |
> | microsoft.web/sites/slots/analyzecustomhostname/read | Web アプリとスロットのカスタム ホスト名の分析を取得します。 |
> | microsoft.web/sites/slots/backup/write | Web アプリとスロットのバックアップを更新します。 |
> | microsoft.web/sites/slots/backup/read | Web アプリのスロット バックアップを取得します。 |
> | Microsoft.Web/sites/slots/backups/Read | Web アプリ スロットのバックアップのプロパティを取得します。 |
> | microsoft.web/sites/slots/backups/list/action | Web アプリとスロットのバックアップを一覧表示します。 |
> | microsoft.web/sites/slots/backups/restore/action | Web アプリとスロットのバックアップを復元します。 |
> | microsoft.web/sites/slots/backups/delete | Web アプリのスロット バックアップを削除します。 |
> | Microsoft.Web/sites/slots/basicPublishingCredentialsPolicies/Read | Web アプリ スロットに対して許可されている発行資格情報を一覧表示します |
> | Microsoft.Web/sites/slots/basicPublishingCredentialsPolicies/ftp/Read | FTP 発行資格情報が Web アプリ スロットに対して許可されているかどうかを取得します |
> | Microsoft.Web/sites/slots/basicPublishingCredentialsPolicies/ftp/Write | FTP 発行資格情報が Web アプリ スロットに対して許可されているかどうかを更新します |
> | Microsoft.Web/sites/slots/basicPublishingCredentialsPolicies/scm/Read | SCM 発行資格情報が Web アプリ スロットに対して許可されているかどうかを取得します |
> | Microsoft.Web/sites/slots/basicPublishingCredentialsPolicies/scm/Write | SCM 発行資格情報が Web アプリ スロットに対して許可されているかどうかを更新します |
> | Microsoft.Web/sites/slots/config/Read | Web アプリ スロットの構成設定を取得します。 |
> | Microsoft.Web/sites/slots/config/list/Action | Web アプリ スロットのセキュリティに関する設定 (発行資格情報、アプリ設定、接続文字列など) を一覧表示します。 |
> | Microsoft.Web/sites/slots/config/Write | Web アプリ スロットの構成設定を更新します。 |
> | microsoft.web/sites/slots/config/delete | Web アプリとスロットの構成を削除します。 |
> | microsoft.web/sites/slots/containerlogs/download/action | Web アプリ スロットのコンテナー ログをダウンロードします。 |
> | microsoft.web/sites/slots/continuouswebjobs/delete | Web アプリとスロットの継続的な Web ジョブを削除します。 |
> | microsoft.web/sites/slots/continuouswebjobs/read | Web アプリとスロットの継続的な Web ジョブを取得します。 |
> | microsoft.web/sites/slots/continuouswebjobs/start/action | Web アプリとスロットの継続的な Web ジョブを開始します。 |
> | microsoft.web/sites/slots/continuouswebjobs/stop/action | Web アプリとスロットの継続的な Web ジョブを停止します。 |
> | microsoft.web/sites/slots/deployments/delete | Web アプリとスロットのデプロイを削除します。 |
> | microsoft.web/sites/slots/deployments/read | Web アプリとスロットのデプロイを取得します。 |
> | microsoft.web/sites/slots/deployments/write | Web アプリとスロットのデプロイを更新します。 |
> | microsoft.web/sites/slots/deployments/log/read | Web アプリとスロットのデプロイのログを取得します。 |
> | microsoft.web/sites/slots/detectors/read | Web Apps スロット検出機能を取得します。 |
> | microsoft.web/sites/slots/diagnostics/read | Web アプリ スロット診断を取得します。 |
> | microsoft.web/sites/slots/diagnostics/analyses/read | Web アプリ スロット診断の分析を取得します。 |
> | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | Web アプリ スロット診断の分析を実行します。 |
> | microsoft.web/sites/slots/diagnostics/aspnetcore/read | ASP.NET Core アプリの Web アプリ スロット診断を取得します。 |
> | microsoft.web/sites/slots/diagnostics/autoheal/read | Web アプリ スロット診断の Autoheal を取得します。 |
> | microsoft.web/sites/slots/diagnostics/deployment/read | Web アプリ スロット診断のデプロイを取得します。 |
> | microsoft.web/sites/slots/diagnostics/deployments/read | Web アプリ スロット診断のデプロイを取得します。 |
> | microsoft.web/sites/slots/diagnostics/detectors/read | Web アプリ スロット診断の検出機能を取得します。 |
> | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | Web アプリ スロット診断の検出機能を実行します。 |
> | microsoft.web/sites/slots/diagnostics/frebanalysis/read | Web アプリ スロット診断の FREB 分析を取得します。 |
> | microsoft.web/sites/slots/diagnostics/loganalyzer/read | Web アプリ スロット診断ログ アナライザーを取得します。 |
> | microsoft.web/sites/slots/diagnostics/runtimeavailability/read | Web アプリ スロット診断の実行時の可用性を取得します。 |
> | microsoft.web/sites/slots/diagnostics/servicehealth/read | Web アプリ スロット診断の Service Health を取得します。 |
> | microsoft.web/sites/slots/diagnostics/sitecpuanalysis/read | Web アプリ スロット診断のサイト CPU 分析を取得します。 |
> | microsoft.web/sites/slots/diagnostics/sitecrashes/read | Web アプリ スロット診断のサイト クラッシュを取得します。 |
> | microsoft.web/sites/slots/diagnostics/sitelatency/read | Web アプリ スロット診断のサイト待機時間を取得します。 |
> | microsoft.web/sites/slots/diagnostics/sitememoryanalysis/read | Web アプリ スロット診断のサイト メモリ分析を取得します。 |
> | microsoft.web/sites/slots/diagnostics/siterestartsettingupdate/read | Web アプリ スロット診断のサイト再起動設定更新を取得します。 |
> | microsoft.web/sites/slots/diagnostics/siterestartuserinitiated/read | Web アプリ スロット診断のサイト再起動ユーザー開始を取得します。 |
> | microsoft.web/sites/slots/diagnostics/siteswap/read | Web アプリ スロット診断のサイト スワップを取得します。 |
> | microsoft.web/sites/slots/diagnostics/threadcount/read | Web アプリ スロット診断のスレッド数を取得します。 |
> | microsoft.web/sites/slots/diagnostics/workeravailability/read | Web アプリ スロット診断の Workeravailability を取得します。 |
> | microsoft.web/sites/slots/diagnostics/workerprocessrecycle/read | Web アプリ スロット診断のワーカー プロセス リサイクルを取得します。 |
> | microsoft.web/sites/slots/domainownershipidentifiers/read | Web アプリ スロットのドメイン所有権識別子を取得します。 |
> | microsoft.web/sites/slots/extensions/read | Web Apps スロット拡張機能が取得されます。 |
> | microsoft.web/sites/slots/extensions/write | Web Apps スロット拡張機能を更新します。 |
> | microsoft.web/sites/slots/functions/listkeys/action | 関数キーを一覧表示します。 |
> | microsoft.web/sites/slots/functions/read | Web Apps スロット関数を取得します。 |
> | microsoft.web/sites/slots/functions/listsecrets/action | Web アプリ スロットの関数のシークレットを一覧表示します。 |
> | microsoft.web/sites/slots/functions/keys/write | 関数キーを更新します。 |
> | microsoft.web/sites/slots/functions/keys/delete | 関数キーを削除します。 |
> | microsoft.web/sites/slots/host/listkeys/action | Functions ホスト キーを一覧表示します。 |
> | microsoft.web/sites/slots/host/sync/action | 関数トリガーを同期します。 |
> | microsoft.web/sites/slots/host/functionkeys/write | Functions ホストの関数キーを更新します。 |
> | microsoft.web/sites/slots/host/functionkeys/delete | Functions ホストの関数キーを削除します。 |
> | microsoft.web/sites/slots/host/systemkeys/write | Functions ホストのシステム キーを更新します。 |
> | microsoft.web/sites/slots/host/systemkeys/delete | Functions ホストのシステム キーを削除します。 |
> | microsoft.web/sites/slots/hostnamebindings/delete | Web アプリとスロットのホスト名バインドを削除します。 |
> | microsoft.web/sites/slots/hostnamebindings/read | Web アプリとスロットのホスト名バインドを取得します。 |
> | microsoft.web/sites/slots/hostnamebindings/write | Web アプリとスロットのホスト名バインドを更新します。 |
> | microsoft.web/sites/slots/hybridconnection/delete | Web アプリとスロットのハイブリッド接続を削除します。 |
> | microsoft.web/sites/slots/hybridconnection/read | Web アプリとスロットのハイブリッド接続を取得します。 |
> | microsoft.web/sites/slots/hybridconnection/write | Web アプリとスロットのハイブリッド接続を更新します。 |
> | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/delete | Web アプリ スロットのハイブリッド接続名前空間リレーを削除します。 |
> | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/write | Web アプリ スロットのハイブリッド接続名前空間リレーを更新します。 |
> | microsoft.web/sites/slots/hybridconnectionrelays/read | Web アプリ スロットのハイブリッド接続リレーを取得します。 |
> | microsoft.web/sites/slots/instances/read | Web アプリとスロットのインスタンスを取得します。 |
> | microsoft.web/sites/slots/instances/deployments/read | Web アプリとスロットのインスタンスのデプロイを取得します。 |
> | microsoft.web/sites/slots/instances/processes/read | Web アプリとスロットのインスタンスのプロセスを取得します。 |
> | microsoft.web/sites/slots/instances/processes/delete | Web アプリ スロットのインスタンス プロセスを削除します。 |
> | microsoft.web/sites/slots/metricdefinitions/read | Web アプリとスロットのメトリック定義を取得します。 |
> | microsoft.web/sites/slots/metrics/read | Web アプリとスロットのメトリックを取得します。 |
> | microsoft.web/sites/slots/migratemysql/read | Web アプリ スロットの移行 MySql を取得します。 |
> | microsoft.web/sites/slots/networktraces/operationresults/read | Web Apps スロットのネットワーク トレース操作の結果を取得します。 |
> | microsoft.web/sites/slots/operationresults/read | Web アプリとスロットの操作の結果を取得します。 |
> | microsoft.web/sites/slots/operations/read | Web アプリ スロットの操作を取得します。 |
> | microsoft.web/sites/slots/perfcounters/read | Web アプリ スロットのパフォーマンス カウンターを取得します。 |
> | microsoft.web/sites/slots/phplogging/read | Web アプリとスロットの Phplogging を取得します。 |
> | microsoft.web/sites/slots/premieraddons/delete | Web アプリとスロットのプレミア アドオンを削除します。 |
> | microsoft.web/sites/slots/premieraddons/read | Web アプリとスロットのプレミア アドオンを取得します。 |
> | microsoft.web/sites/slots/premieraddons/write | Web アプリとスロットのプレミア アドオンを更新します。 |
> | microsoft.web/sites/slots/processes/read | Web アプリ スロットのプロセスを取得します。 |
> | microsoft.web/sites/slots/publiccertificates/read | Web アプリ スロットのパブリック証明書を取得します。 |
> | microsoft.web/sites/slots/publiccertificates/write | Web アプリ スロットのパブリック証明書を作成または更新します。 |
> | microsoft.web/sites/slots/publiccertificates/delete | Web アプリ スロットのパブリック証明書を削除します。 |
> | microsoft.web/sites/slots/resourcehealthmetadata/read | Web アプリ スロットの Resource Health のメタデータを取得します。 |
> | microsoft.web/sites/slots/restore/read | Web アプリとスロットの復元を取得します。 |
> | microsoft.web/sites/slots/restore/write | Web アプリ スロットを復元します。 |
> | microsoft.web/sites/slots/siteextensions/delete | Web アプリ スロットのサイト拡張機能を削除します。 |
> | microsoft.web/sites/slots/siteextensions/read | Web アプリ スロットのサイト拡張機能を取得します。 |
> | microsoft.web/sites/slots/siteextensions/write | Web アプリ スロットのサイト拡張機能を更新します。 |
> | microsoft.web/sites/slots/snapshots/read | Web アプリ スロットのスナップショットを取得します。 |
> | Microsoft.Web/sites/slots/sourcecontrols/Read | Web アプリ スロットのソース管理の構成設定を取得します。 |
> | Microsoft.Web/sites/slots/sourcecontrols/Write | Web アプリ スロットのソース管理の構成設定を更新します。 |
> | Microsoft.Web/sites/slots/sourcecontrols/Delete | Web アプリ スロットのソース管理の構成設定を削除します。 |
> | microsoft.web/sites/slots/triggeredwebjobs/delete | Web アプリとスロットのトリガーされた Web ジョブを削除します。 |
> | microsoft.web/sites/slots/triggeredwebjobs/read | Web アプリとスロットのトリガーされた Web ジョブを取得します。 |
> | microsoft.web/sites/slots/triggeredwebjobs/run/action | Web アプリとスロットのトリガーされた Web ジョブを実行します。 |
> | microsoft.web/sites/slots/usages/read | Web アプリとスロットの使用状況を取得します。 |
> | microsoft.web/sites/slots/virtualnetworkconnections/delete | Web アプリとスロットの仮想ネットワーク接続を削除します。 |
> | microsoft.web/sites/slots/virtualnetworkconnections/read | Web アプリとスロットの仮想ネットワーク接続を取得します。 |
> | microsoft.web/sites/slots/virtualnetworkconnections/write | Web アプリとスロットの仮想ネットワーク接続を更新します。 |
> | microsoft.web/sites/slots/virtualnetworkconnections/gateways/write | Web アプリとスロットの仮想ネットワーク接続のゲートウェイを更新します。 |
> | microsoft.web/sites/slots/webjobs/read | Web アプリとスロットの Web ジョブを取得します。 |
> | microsoft.web/sites/snapshots/read | Web アプリのスナップショットを取得します。 |
> | Microsoft.Web/sites/sourcecontrols/Read | Web アプリのソース管理の構成設定を取得します。 |
> | Microsoft.Web/sites/sourcecontrols/Write | Web アプリのソース管理の構成設定を更新します。 |
> | Microsoft.Web/sites/sourcecontrols/Delete | Web アプリのソース管理の構成設定を削除します。 |
> | microsoft.web/sites/triggeredwebjobs/delete | Web アプリのトリガーされた Web ジョブを削除します。 |
> | microsoft.web/sites/triggeredwebjobs/read | Web アプリのトリガーされた Web ジョブを取得します。 |
> | microsoft.web/sites/triggeredwebjobs/run/action | Web アプリのトリガーされた Web ジョブを実行します。 |
> | microsoft.web/sites/triggeredwebjobs/history/read | Web アプリのトリガーされた Web ジョブの履歴を取得します。 |
> | microsoft.web/sites/usages/read | Web アプリの使用状況を取得します。 |
> | microsoft.web/sites/virtualnetworkconnections/delete | Web アプリの仮想ネットワーク接続を削除します。 |
> | microsoft.web/sites/virtualnetworkconnections/read | Web アプリの仮想ネットワーク接続を取得します。 |
> | microsoft.web/sites/virtualnetworkconnections/write | Web アプリの仮想ネットワーク接続を更新します。 |
> | microsoft.web/sites/virtualnetworkconnections/gateways/read | Web アプリの仮想ネットワーク接続のゲートウェイを取得します。 |
> | microsoft.web/sites/virtualnetworkconnections/gateways/write | Web アプリの仮想ネットワーク接続のゲートウェイを更新します。 |
> | microsoft.web/sites/webjobs/read | Web アプリの Web ジョブを取得します。 |
> | microsoft.web/skus/read | SKU を取得します。 |
> | microsoft.web/sourcecontrols/read | ソース管理を取得します。 |
> | microsoft.web/sourcecontrols/write | ソース管理を更新します。 |
> | Microsoft.Web/staticSites/Read | 静的サイトのプロパティを取得します |
> | Microsoft.Web/staticSites/Write | 新しい静的サイトを作成するか、既存のものを更新します |
> | Microsoft.Web/staticSites/Delete | 既存の静的サイトを削除します |
> | Microsoft.Web/staticSites/createinvitation/action | 一連のロールに対して静的サイト ユーザーの招待リンクを作成します |
> | Microsoft.Web/staticSites/listConfiguredRoles/action | 静的サイトに対して構成されているロールを一覧表示します。 |
> | Microsoft.Web/staticSites/listfunctionappsettings/Action | 静的サイトの関数アプリ設定を一覧表示します |
> | Microsoft.Web/staticSites/listappsettings/Action | 静的サイトのアプリの設定を一覧表示します |
> | Microsoft.Web/staticSites/detach/Action | 現在リンクされているリポジトリから静的サイトをデタッチします |
> | Microsoft.Web/staticSites/getuser/Action | 静的サイトのユーザーの情報を取得します |
> | Microsoft.Web/staticSites/listsecrets/action | 静的サイトのシークレットを一覧表示します |
> | Microsoft.Web/staticSites/resetapikey/Action | 静的サイトの API キーをリセットします |
> | Microsoft.Web/staticSites/zipdeploy/action | zip 圧縮したコンテンツから静的サイトを展開します |
> | Microsoft.Web/staticSites/authproviders/listusers/Action | 静的サイトのユーザーを一覧表示します |
> | Microsoft.Web/staticSites/authproviders/users/Delete | 静的サイトのユーザーを削除します |
> | Microsoft.Web/staticSites/authproviders/users/Write | 静的サイトのユーザーを更新します |
> | Microsoft.Web/staticSites/build/Read | 静的サイトのビルドを取得します |
> | Microsoft.Web/staticSites/build/Delete | 静的サイトのビルドを削除します |
> | Microsoft.Web/staticSites/builds/listfunctionappsettings/Action | 静的サイト ビルドの関数アプリ設定を一覧表示します |
> | Microsoft.Web/staticSites/builds/listappsettings/Action | 静的サイト ビルドのアプリの設定を一覧表示します |
> | Microsoft.Web/staticSites/builds/zipdeploy/action | zip 圧縮したコンテンツから静的サイト ビルドを展開します |
> | Microsoft.Web/staticSites/builds/config/Write | 静的サイト ビルドのアプリの設定を作成または更新します |
> | Microsoft.Web/staticSites/config/Write | 静的サイトのアプリの設定を作成または更新します |
> | Microsoft.Web/staticSites/customdomains/Write | 静的サイトのカスタム ドメインを作成します |
> | Microsoft.Web/staticSites/customdomains/Delete | 静的サイトのカスタム ドメインを削除します |
> | Microsoft.Web/staticSites/customdomains/Read | 静的サイトのカスタム ドメインを一覧表示します |
> | Microsoft.Web/staticSites/customdomains/validate/Action | カスタム ドメインを静的サイトに追加できることを確認します |
> | Microsoft.Web/staticSites/functions/Read | 静的サイトの関数を一覧表示します |
> | Microsoft.Web/staticSites/privateEndpointConnections/Write | 静的サイトのプライベート エンドポイント接続を承認または拒否します |
> | Microsoft.Web/staticSites/privateEndpointConnections/Read | 静的サイトのプライベート エンドポイント接続またはプライベート エンドポイント接続の一覧を取得します |
> | Microsoft.Web/staticSites/privateEndpointConnections/Delete | 静的サイトのプライベート エンドポイント接続を削除します |
> | Microsoft.Web/staticSites/privateLinkResources/Read | Private Link リソースを取得します |

## <a name="containers"></a>Containers

### <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

Azure サービス:[Container Instances](../container-instances/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.ContainerInstance/register/action | コンテナー インスタンス リソース プロバイダーのサブスクリプションを登録し、コンテナー グループの作成を有効にします。 |
> | Microsoft.ContainerInstance/containerGroups/read | すべてのコンテナー グループを取得します。 |
> | Microsoft.ContainerInstance/containerGroups/write | 特定のコンテナー グループを作成または更新します。 |
> | Microsoft.ContainerInstance/containerGroups/delete | 特定のコンテナー グループを削除します。 |
> | Microsoft.ContainerInstance/containerGroups/restart/action | 特定のコンテナー グループを再起動します。 |
> | Microsoft.ContainerInstance/containerGroups/stop/action | 特定のコンテナー グループを停止します。 コンピューティング リソースの割り当てが解除され、課金が停止されます。 |
> | Microsoft.ContainerInstance/containerGroups/start/action | 特定のコンテナー グループを起動します。 |
> | Microsoft.ContainerInstance/containerGroups/containers/exec/action | 特定のコンテナーに向けて実行します。 |
> | Microsoft.ContainerInstance/containerGroups/containers/attach/action | コンテナーの出力ストリームにアタッチします。 |
> | Microsoft.ContainerInstance/containerGroups/containers/buildlogs/read | 特定のコンテナーのビルド ログを取得します。 |
> | Microsoft.ContainerInstance/containerGroups/containers/logs/read | 特定のコンテナーのログを取得します。 |
> | Microsoft.ContainerInstance/containerGroups/detectors/read | コンテナー グループ検出機能を一覧表示します |
> | Microsoft.ContainerInstance/containerGroups/operationResults/read | 非同期操作の結果を取得します |
> | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | コンテナー グループの診断設定を取得します。 |
> | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | コンテナー グループの診断設定を作成または更新します。 |
> | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | コンテナー グループで使用可能なメトリックを取得します。 |
> | Microsoft.ContainerInstance/locations/deleteVirtualNetworkOrSubnets/action | 仮想ネットワークまたはサブネットを削除していることを Microsoft.ContainerInstance に通知します。 |
> | Microsoft.ContainerInstance/locations/cachedImages/read | リージョン内のサブスクリプションに対してキャッシュされたイメージを取得します。 |
> | Microsoft.ContainerInstance/locations/capabilities/read | リージョンの機能を取得します。 |
> | Microsoft.ContainerInstance/locations/operationResults/read | 非同期操作の結果を取得します |
> | Microsoft.ContainerInstance/locations/operations/read | Azure コンテナー インスタンス サービスの操作を一覧表示します。 |
> | Microsoft.ContainerInstance/locations/usages/read | 特定のリージョンの使用状況を取得します。 |
> | Microsoft.ContainerInstance/operations/read | Azure コンテナー インスタンス サービスの操作を一覧表示します。 |
> | Microsoft.ContainerInstance/serviceassociationlinks/delete | サブネット上の Azure コンテナー インスタンス リソースプロバイダーによって作成されたサービス関連付けリンクを削除します。 |

### <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

Azure サービス:[Container Registry](../container-registry/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.ContainerRegistry/register/action | コンテナー レジストリ リソース プロバイダーにサブスクリプションを登録し、コンテナー レジストリを作成できるようにします。 |
> | Microsoft.ContainerRegistry/checkNameAvailability/read | コンテナー レジストリ名が使用可能かどうかを確認します。 |
> | Microsoft.ContainerRegistry/locations/deleteVirtualNetworkOrSubnets/action | 仮想ネットワークまたはサブネットを削除していることを Microsoft.ContainerRegistry に通知します。 |
> | Microsoft.ContainerRegistry/locations/operationResults/read | 非同期操作の結果を取得します。 |
> | Microsoft.ContainerRegistry/operations/read | 使用可能なすべての Azure Container Registry REST API 操作を一覧表示します。 |
> | Microsoft.ContainerRegistry/registries/read | 指定されたコンテナー レジストリのプロパティを取得するか、指定されたリソース グループまたはサブスクリプションのすべてのコンテナー レジストリを一覧表示します。 |
> | Microsoft.ContainerRegistry/registries/write | 指定されたパラメーターでコンテナー レジストリを作成または更新します。 |
> | Microsoft.ContainerRegistry/registries/delete | コンテナー レジストリを削除します。 |
> | Microsoft.ContainerRegistry/registries/listCredentials/action | 指定されたコンテナー レジストリのログイン資格情報を一覧表示します。 |
> | Microsoft.ContainerRegistry/registries/regenerateCredential/action | 指定されたコンテナー レジストリのログイン資格情報の 1 つを再生成します。 |
> | Microsoft.ContainerRegistry/registries/generateCredentials/action | 指定されたコンテナー レジストリのトークンのキーを生成します。 |
> | Microsoft.ContainerRegistry/registries/importImage/action | 指定されたパラメーターを使用して、コンテナー レジストリにイメージをインポートします。 |
> | Microsoft.ContainerRegistry/registries/getBuildSourceUploadUrl/action | ユーザーがソースをアップロードできるアップロードの場所を取得します。 |
> | Microsoft.ContainerRegistry/registries/queueBuild/action | 要求パラメーターに基づいて新しいビルドを作成し、ビルド キューに追加します。 |
> | Microsoft.ContainerRegistry/registries/listBuildSourceUploadUrl/action | コンテナー レジストリのソース アップロード URL の場所を取得します。 |
> | Microsoft.ContainerRegistry/registries/scheduleRun/action | コンテナー レジストリに対する実行をスケジュールします。 |
> | Microsoft.ContainerRegistry/registries/agentpools/listQueueStatus/action | コンテナー レジストリの agentpool のすべてのキューの状態を一覧表示します。 |
> | Microsoft.ContainerRegistry/registries/artifacts/delete | コンテナー レジストリの成果物を削除します。 |
> | Microsoft.ContainerRegistry/registries/builds/read | 指定したビルドのプロパティを取得するか、指定したコンテナー レジストリのすべてのビルドを一覧表示します。 |
> | Microsoft.ContainerRegistry/registries/builds/write | 指定したパラメーターでコンテナー レジストリのビルドを更新します。 |
> | Microsoft.ContainerRegistry/registries/builds/getLogLink/action | ビルド ログをダウンロードするためのリンクを取得します。 |
> | Microsoft.ContainerRegistry/registries/builds/cancel/action | 既存のビルドを取り消します。 |
> | Microsoft.ContainerRegistry/registries/buildTasks/read | 指定したビルド タスクのプロパティを取得するか、指定したコンテナー レジストリのすべてのビルド タスクを一覧表示します。 |
> | Microsoft.ContainerRegistry/registries/buildTasks/write | 指定したパラメーターでコンテナー レジストリのビルド タスクを作成または更新します。 |
> | Microsoft.ContainerRegistry/registries/buildTasks/delete | コンテナー レジストリからビルド タスクを削除します。 |
> | Microsoft.ContainerRegistry/registries/buildTasks/listSourceRepositoryProperties/action | ビルド タスクのソース管理のプロパティを一覧表示します。 |
> | Microsoft.ContainerRegistry/registries/buildTasks/steps/read | 指定したビルド ステップのプロパティを取得するか、指定したビルド タスクのすべてのビルド ステップを一覧表示します。 |
> | Microsoft.ContainerRegistry/registries/buildTasks/steps/write | 指定したパラメーターでビルド タスクのビルド ステップを作成または更新します。 |
> | Microsoft.ContainerRegistry/registries/buildTasks/steps/delete | ビルド タスクからビルド ステップを削除します。 |
> | Microsoft.ContainerRegistry/registries/buildTasks/steps/listBuildArguments/action | シークレット引数を含むビルド ステップのビルド引数を一覧表示します。 |
> | Microsoft.ContainerRegistry/registries/eventGridFilters/read | 指定されたイベント グリッド フィルターのプロパティを取得するか、指定されたコンテナー レジストリのすべてのイベント グリッド フィルターを一覧表示します。 |
> | Microsoft.ContainerRegistry/registries/eventGridFilters/write | 指定されたパラメーターでコンテナー レジストリのイベント グリッド フィルターを作成または更新します。 |
> | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | コンテナー レジストリからイベント グリッド フィルターを削除します。 |
> | Microsoft.ContainerRegistry/registries/listPolicies/read | 指定されたコンテナー レジストリのポリシーを一覧表示します。 |
> | Microsoft.ContainerRegistry/registries/listUsages/read | 指定されたコンテナー レジストリのクォータ使用状況を一覧表示します。 |
> | Microsoft.ContainerRegistry/registries/metadata/read | コンテナー レジストリの特定のリポジトリのメタデータを取得します |
> | Microsoft.ContainerRegistry/registries/metadata/write | コンテナー レジストリのリポジトリのメタデータを更新します |
> | Microsoft.ContainerRegistry/registries/operationStatuses/read | レジストリの非同期操作の状態を取得します。 |
> | Microsoft.ContainerRegistry/registries/privateEndpointConnectionProxies/validate/action | プライベート エンドポイント接続プロキシが検証されます (NRP のみ) |
> | Microsoft.ContainerRegistry/registries/privateEndpointConnectionProxies/read | プライベート エンドポイント接続プロキシが取得されます (NRP のみ) |
> | Microsoft.ContainerRegistry/registries/privateEndpointConnectionProxies/write | プライベート エンドポイント接続プロキシが作成されます (NRP のみ) |
> | Microsoft.ContainerRegistry/registries/privateEndpointConnectionProxies/delete | プライベート エンドポイント接続プロキシが削除されます (NRP のみ) |
> | Microsoft.ContainerRegistry/registries/privateEndpointConnectionProxies/operationStatuses/read | プライベート エンドポイント接続プロキシの非同期操作の状態が取得されます |
> | Microsoft.ContainerRegistry/registries/pull/read | コンテナー レジストリからイメージをプルまたは取得します。 |
> | Microsoft.ContainerRegistry/registries/push/write | コンテナー レジストリにイメージをプッシュするか書き込みます。 |
> | Microsoft.ContainerRegistry/registries/quarantine/read | コンテナー レジストリから検疫済みのイメージをプルまたは取得します |
> | Microsoft.ContainerRegistry/registries/quarantine/write | 検疫済みイメージの検疫状態を書き込むか変更します |
> | Microsoft.ContainerRegistry/registries/replications/read | 指定されたレプリケーションのプロパティを取得するか、指定されたコンテナー レジストリのすべてのレプリケーションを一覧表示します。 |
> | Microsoft.ContainerRegistry/registries/replications/write | 指定されたパラメーターでコンテナー レジストリのレプリケーションを作成または更新します。 |
> | Microsoft.ContainerRegistry/registries/replications/delete | コンテナー レジストリからレプリケーションを削除します。 |
> | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | レプリケーションの非同期操作の状態を取得します。 |
> | Microsoft.ContainerRegistry/registries/runs/read | コンテナー レジストリに対する実行のプロパティを取得するか、実行を一覧表示します。 |
> | Microsoft.ContainerRegistry/registries/runs/write | 実行を更新します。 |
> | Microsoft.ContainerRegistry/registries/runs/listLogSasUrl/action | 実行のログ SAS URL を取得します。 |
> | Microsoft.ContainerRegistry/registries/runs/cancel/action | 既存の実行をキャンセルします。 |
> | Microsoft.ContainerRegistry/registries/scopeMaps/read | 指定したスコープ マップのプロパティを取得するか、指定したコンテナー レジストリのすべてのスコープ マップを一覧表示します。 |
> | Microsoft.ContainerRegistry/registries/scopeMaps/write | 指定されたパラメーターでコンテナー レジストリのスコープ マップを作成または更新します。 |
> | Microsoft.ContainerRegistry/registries/scopeMaps/delete | コンテナー レジストリからスコープ マップを削除します。 |
> | Microsoft.ContainerRegistry/registries/scopeMaps/operationStatuses/read | スコープ マップの非同期操作の状態を取得します。 |
> | Microsoft.ContainerRegistry/registries/sign/write | コンテナー レジストリのコンテンツの信頼メタデータをプッシュ/プルします。 |
> | Microsoft.ContainerRegistry/registries/taskruns/listDetails/action | コンテナー レジストリに対するタスク実行のすべての詳細を一覧表示します。 |
> | Microsoft.ContainerRegistry/registries/tasks/read | コンテナー レジストリに対するタスクを取得するか、すべてのタスクを一覧表示します。 |
> | Microsoft.ContainerRegistry/registries/tasks/write | コンテナー レジストリに対するタスクを作成または更新します。 |
> | Microsoft.ContainerRegistry/registries/tasks/delete | コンテナー レジストリに対するタスクを削除します。 |
> | Microsoft.ContainerRegistry/registries/tasks/listDetails/action | コンテナー レジストリに対するタスクの詳細を一覧表示します。 |
> | Microsoft.ContainerRegistry/registries/tokens/read | 指定したトークンのプロパティを取得するか、指定したコンテナー レジストリのすべてのトークンを一覧表示します。 |
> | Microsoft.ContainerRegistry/registries/tokens/write | 指定されたパラメーターでコンテナー レジストリのトークンを作成または更新します。 |
> | Microsoft.ContainerRegistry/registries/tokens/delete | コンテナー レジストリからトークンを削除します。 |
> | Microsoft.ContainerRegistry/registries/tokens/operationStatuses/read | トークンの非同期操作の状態を取得します。 |
> | Microsoft.ContainerRegistry/registries/updatePolicies/write | 指定されたコンテナー レジストリのポリシーを更新します。 |
> | Microsoft.ContainerRegistry/registries/webhooks/read | 指定された webhook のプロパティを取得するか、指定されたコンテナー レジストリのすべての webhook を一覧表示します。 |
> | Microsoft.ContainerRegistry/registries/webhooks/write | 指定されたパラメーターでコンテナー レジストリの webhook を作成または更新します。 |
> | Microsoft.ContainerRegistry/registries/webhooks/delete | コンテナー レジストリから webhook を削除します。 |
> | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | webhook のサービス URI とカスタム ヘッダーの構成を取得します。 |
> | Microsoft.ContainerRegistry/registries/webhooks/ping/action | webhook に送信される ping イベントをトリガーします。 |
> | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | 指定された webhook の最近のイベントを一覧表示します。 |
> | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | webhook の非同期操作の状態を取得します。 |

### <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

Azure サービス:[Azure Kubernetes Service (AKS)](../aks/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.ContainerService/register/action | Microsoft.ContainerService リソース プロバイダーにサブスクリプションを登録します。 |
> | Microsoft.ContainerService/unregister/action | Microsoft.ContainerService リソース プロバイダーからサブスクリプションを登録解除します。 |
> | Microsoft.ContainerService/containerServices/read | コンテナー サービスを取得します。 |
> | Microsoft.ContainerService/containerServices/write | 新しいコンテナー サービスを作成するか、既存のものを更新します。 |
> | Microsoft.ContainerService/containerServices/delete | コンテナー サービスを削除します。 |
> | Microsoft.ContainerService/locations/operationresults/read | 非同期操作結果の状態を取得します。 |
> | Microsoft.ContainerService/locations/operations/read | 非同期操作の状態を取得します。 |
> | Microsoft.ContainerService/locations/orchestrators/read | サポート対象のオーケストレーターを一覧表示します。 |
> | Microsoft.ContainerService/managedClusters/read | マネージド クラスターを取得します。 |
> | Microsoft.ContainerService/managedClusters/write | 新しいマネージド クラスターを作成するか、既存のものを更新します。 |
> | Microsoft.ContainerService/managedClusters/delete | マネージド クラスターを削除します。 |
> | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | 管理対象クラスターの clusterAdmin 資格情報を一覧表示します。 |
> | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | 管理対象クラスターの clusterUser 資格情報を一覧表示します。 |
> | Microsoft.ContainerService/managedClusters/listClusterMonitoringUserCredential/action | 管理対象クラスターの clusterMonitoringUser 資格情報を一覧表示します。 |
> | Microsoft.ContainerService/managedClusters/resetServicePrincipalProfile/action | マネージド クラスターのサービス プリンシパル プロファイルをリセットします。 |
> | Microsoft.ContainerService/managedClusters/resetAADProfile/action | マネージド クラスターの AAD プロファイルをリセットします。 |
> | Microsoft.ContainerService/managedClusters/rotateClusterCertificates/action | マネージド クラスターの証明書をローテーションします。 |
> | Microsoft.ContainerService/managedClusters/runCommand/action | マネージド Kubernetes サーバーに対してユーザーが発行したコマンドを実行します。 |
> | Microsoft.ContainerService/managedClusters/privateEndpointConnectionsApproval/action | ユーザーがプライベート エンドポイント接続の承認を許可されているかどうかを判断します。 |
> | Microsoft.ContainerService/managedClusters/accessProfiles/read | ロール名を指定してマネージド クラスターのアクセス プロファイルを取得します。 |
> | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | 資格情報の一覧の取得を使用し、ロール名を指定してマネージド クラスターのアクセス プロファイルを取得します。 |
> | Microsoft.ContainerService/managedClusters/agentPools/read | エージェント プールを取得します |
> | Microsoft.ContainerService/managedClusters/agentPools/write | 新しいエージェント プールを作成するか、既存のものを更新します |
> | Microsoft.ContainerService/managedClusters/agentPools/delete | エージェント プールを削除します |
> | Microsoft.ContainerService/managedClusters/agentPools/upgradeNodeImageVersion/write | エージェント プールのノード イメージ バージョンをアップグレードします |
> | Microsoft.ContainerService/managedClusters/agentPools/upgradeProfiles/read | エージェント プールのアップグレード プロファイルを取得します |
> | Microsoft.ContainerService/managedClusters/availableAgentPoolVersions/read | クラスターの使用可能なエージェント プールのバージョンを取得します。 |
> | Microsoft.ContainerService/managedClusters/commandResults/read | 以前に発行されたコマンドから結果を取得します。 |
> | Microsoft.ContainerService/managedClusters/detectors/read | 管理対象クラスター検出機能を取得します。 |
> | Microsoft.ContainerService/managedClusters/diagnosticsState/read | クラスターの診断状態を取得します |
> | Microsoft.ContainerService/managedClusters/maintenanceConfigurations/read | メンテナンス構成を取得します |
> | Microsoft.ContainerService/managedClusters/maintenanceConfigurations/write | 新しいメンテナンス構成を作成するか、既存のものを更新します |
> | Microsoft.ContainerService/managedClusters/maintenanceConfigurations/delete | メンテナンス構成を削除します |
> | Microsoft.ContainerService/managedClusters/privateEndpointConnections/read | プライベート エンドポイント接続を取得します |
> | Microsoft.ContainerService/managedClusters/privateEndpointConnections/write | プライベート エンドポイント接続を承認または拒否します |
> | Microsoft.ContainerService/managedClusters/privateEndpointConnections/delete | プライベート エンドポイント接続を削除します |
> | Microsoft.ContainerService/managedClusters/upgradeProfiles/read | クラスターのアップグレード プロファイルを取得します。 |
> | Microsoft.ContainerService/openShiftClusters/read | Open Shift クラスターを取得します |
> | Microsoft.ContainerService/openShiftClusters/write | 新しい Open Shift クラスターを作成するか、既存のものを更新します |
> | Microsoft.ContainerService/openShiftClusters/delete | Open Shift クラスターを削除します |
> | Microsoft.ContainerService/openShiftManagedClusters/read | Open Shift マネージド クラスターを取得します |
> | Microsoft.ContainerService/openShiftManagedClusters/write | 新しい Open Shift マネージド クラスターを作成するか、既存のものを更新します |
> | Microsoft.ContainerService/openShiftManagedClusters/delete | Open Shift マネージド クラスターを削除します |
> | Microsoft.ContainerService/operations/read | Microsoft.ContainerService リソース プロバイダーで使用できる操作を一覧表示します。 |
> | **DataAction** | **説明** |
> | Microsoft.ContainerService/managedClusters/admissionregistration.k8s.io/initializerconfigurations/read | initializerconfigurations を読み取ります |
> | Microsoft.ContainerService/managedClusters/admissionregistration.k8s.io/initializerconfigurations/write | initializerconfigurations を書き込みます |
> | Microsoft.ContainerService/managedClusters/admissionregistration.k8s.io/initializerconfigurations/delete | Deletes/DeletesCollection initializerconfigurations リソース |
> | Microsoft.ContainerService/managedClusters/admissionregistration.k8s.io/mutatingwebhookconfigurations/read | mutatingwebhookconfigurations を読み取ります |
> | Microsoft.ContainerService/managedClusters/admissionregistration.k8s.io/mutatingwebhookconfigurations/write | mutatingwebhookconfigurations を書き込みます |
> | Microsoft.ContainerService/managedClusters/admissionregistration.k8s.io/mutatingwebhookconfigurations/delete | mutatingwebhookconfigurations を削除します |
> | Microsoft.ContainerService/managedClusters/admissionregistration.k8s.io/validatingwebhookconfigurations/read | validatingwebhookconfigurations を読み取ります |
> | Microsoft.ContainerService/managedClusters/admissionregistration.k8s.io/validatingwebhookconfigurations/write | validatingwebhookconfigurations を書き込みます |
> | Microsoft.ContainerService/managedClusters/admissionregistration.k8s.io/validatingwebhookconfigurations/delete | validatingwebhookconfigurations を削除します |
> | Microsoft.ContainerService/managedClusters/api/read | api を読み取ります |
> | Microsoft.ContainerService/managedClusters/api/v1/read | api/v1 を読み取ります |
> | Microsoft.ContainerService/managedClusters/apiextensions.k8s.io/customresourcedefinitions/read | customresourcedefinitions を読み取ります |
> | Microsoft.ContainerService/managedClusters/apiextensions.k8s.io/customresourcedefinitions/write | customresourcedefinitions を書き込みます |
> | Microsoft.ContainerService/managedClusters/apiextensions.k8s.io/customresourcedefinitions/delete | customresourcedefinitions を削除します |
> | Microsoft.ContainerService/managedClusters/apiregistration.k8s.io/apiservices/read | apiservices を読み取ります |
> | Microsoft.ContainerService/managedClusters/apiregistration.k8s.io/apiservices/write | apiservices を書き込みます |
> | Microsoft.ContainerService/managedClusters/apiregistration.k8s.io/apiservices/delete | apiservices を削除します |
> | Microsoft.ContainerService/managedClusters/apis/read | apis を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/admissionregistration.k8s.io/read | admissionregistration.k8s.io を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/admissionregistration.k8s.io/v1/read | admissionregistration.k8s.io/v1 を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/admissionregistration.k8s.io/v1beta1/read | admissionregistration.k8s.io/v1beta1 を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/apiextensions.k8s.io/read | apiextensions.k8s.io を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/apiextensions.k8s.io/v1/read | apiextensions.k8s.io/v1 を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/apiextensions.k8s.io/v1beta1/read | apiextensions.k8s.io/v1beta1 を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/apiregistration.k8s.io/read | apiregistration.k8s.io を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/apiregistration.k8s.io/v1/read | apiregistration.k8s.io/v1 を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/apiregistration.k8s.io/v1beta1/read | apiregistration.k8s.io/v1beta1 を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/apps/read | apps を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/apps/v1/read | apps/v1 を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/apps/v1beta1/read | apps/v1beta1 を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/apps/v1beta2/read | apps/v1beta2 を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/authentication.k8s.io/read | authentication.k8s.io を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/authentication.k8s.io/v1/read | authentication.k8s.io/v1 を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/authentication.k8s.io/v1beta1/read | authentication.k8s.io/v1beta1 を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/authorization.k8s.io/read | authorization.k8s.io を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/authorization.k8s.io/v1/read | authorization.k8s.io/v1 を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/authorization.k8s.io/v1beta1/read | authorization.k8s.io/v1beta1 を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/autoscaling/read | 自動スケーリングを読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/autoscaling/v1/read | autoscaling/v1 を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/autoscaling/v2beta1/read | autoscaling/v2beta1 を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/autoscaling/v2beta2/read | autoscaling/v2beta2 を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/batch/read | バッチを読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/batch/v1/read | batch/v1 を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/batch/v1beta1/read | batch/v1beta1 を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/certificates.k8s.io/read | certificates.k8s.io を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/certificates.k8s.io/v1beta1/read | certificates.k8s.io/v1beta1 を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/coordination.k8s.io/read | coordination.k8s.io を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/coordination.k8s.io/v1/read | coordination/v1 を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/coordination.k8s.io/v1beta1/read | coordination.k8s.io/v1beta1 を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/events.k8s.io/read | events.k8s.io を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/events.k8s.io/v1beta1/read | events.k8s.io/v1beta1 を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/extensions/read | 拡張機能を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/extensions/v1beta1/read | extensions/v1beta1 を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/metrics.k8s.io/read | metrics.k8s.io を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/metrics.k8s.io/v1beta1/read | metrics.k8s.io/v1beta1 を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/networking.k8s.io/read | networking.k8s.io を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/networking.k8s.io/v1/read | networking/v1 を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/networking.k8s.io/v1beta1/read | networking.k8s.io/v1beta1 を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/node.k8s.io/read | node.k8s.io を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/node.k8s.io/v1beta1/read | node.k8s.io/v1beta1 を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/policy/read | ポリシーを読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/policy/v1beta1/read | policy/v1beta1 を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/rbac.authorization.k8s.io/read | rbac.authorization.k8s.io を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/rbac.authorization.k8s.io/v1/read | bac.authorization/v1 を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/rbac.authorization.k8s.io/v1beta1/read | rbac.authorization.k8s.io/v1beta1 を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/scheduling.k8s.io/read | scheduling.k8s.io を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/scheduling.k8s.io/v1/read | scheduling/v1 を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/scheduling.k8s.io/v1beta1/read | scheduling.k8s.io/v1beta1 を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/storage.k8s.io/read | storage.k8s.io を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/storage.k8s.io/v1/read | storage/v1 を読み取ります |
> | Microsoft.ContainerService/managedClusters/apis/storage.k8s.io/v1beta1/read | storage.k8s.io/v1beta1 を読み取ります |
> | Microsoft.ContainerService/managedClusters/apps/controllerrevisions/read | controllerrevisions を読み取ります |
> | Microsoft.ContainerService/managedClusters/apps/controllerrevisions/write | controllerrevisions を書き込みます |
> | Microsoft.ContainerService/managedClusters/apps/controllerrevisions/delete | controllerrevisions を削除します |
> | Microsoft.ContainerService/managedClusters/apps/daemonsets/read | デーモンセットを読み取ります |
> | Microsoft.ContainerService/managedClusters/apps/daemonsets/write | デーモンセットを書き込みます |
> | Microsoft.ContainerService/managedClusters/apps/daemonsets/delete | デーモンセットを削除します |
> | Microsoft.ContainerService/managedClusters/apps/deployments/read | デプロイを読み取ります |
> | Microsoft.ContainerService/managedClusters/apps/deployments/write | デプロイを書き込みます |
> | Microsoft.ContainerService/managedClusters/apps/deployments/delete | デプロイを削除します |
> | Microsoft.ContainerService/managedClusters/apps/replicasets/read | レプリカセットを読み取ります |
> | Microsoft.ContainerService/managedClusters/apps/replicasets/write | レプリカセットを書き込みます |
> | Microsoft.ContainerService/managedClusters/apps/replicasets/delete | レプリカセットを削除します |
> | Microsoft.ContainerService/managedClusters/apps/statefulsets/read | ステートフルセットを読み取ります |
> | Microsoft.ContainerService/managedClusters/apps/statefulsets/write | ステートフルセットを書き込みます |
> | Microsoft.ContainerService/managedClusters/apps/statefulsets/delete | ステートフルセットを削除します |
> | Microsoft.ContainerService/managedClusters/authentication.k8s.io/tokenreviews/write | tokenreviews を書き込みます |
> | Microsoft.ContainerService/managedClusters/authentication.k8s.io/userextras/impersonate/action | userextras を偽装します |
> | Microsoft.ContainerService/managedClusters/authorization.k8s.io/localsubjectaccessreviews/write | localsubjectaccessreviews を書き込みます |
> | Microsoft.ContainerService/managedClusters/authorization.k8s.io/selfsubjectaccessreviews/write | selfsubjectaccessreviews を書き込みます |
> | Microsoft.ContainerService/managedClusters/authorization.k8s.io/selfsubjectrulesreviews/write | selfsubjectrulesreviews を書き込みます |
> | Microsoft.ContainerService/managedClusters/authorization.k8s.io/subjectaccessreviews/write | subjectaccessreviews を書き込みます |
> | Microsoft.ContainerService/managedClusters/autoscaling/horizontalpodautoscalers/read | horizontalpodautoscalers を読み取ります |
> | Microsoft.ContainerService/managedClusters/autoscaling/horizontalpodautoscalers/write | horizontalpodautoscalers を書き込みます |
> | Microsoft.ContainerService/managedClusters/autoscaling/horizontalpodautoscalers/delete | horizontalpodautoscalers を削除します |
> | Microsoft.ContainerService/managedClusters/batch/cronjobs/read | cronjobs を読み取ります |
> | Microsoft.ContainerService/managedClusters/batch/cronjobs/write | cronjobs を書き込みます |
> | Microsoft.ContainerService/managedClusters/batch/cronjobs/delete | cronjobs を削除します |
> | Microsoft.ContainerService/managedClusters/batch/jobs/read | ジョブを読み取ります |
> | Microsoft.ContainerService/managedClusters/batch/jobs/write | ジョブを書き込みます |
> | Microsoft.ContainerService/managedClusters/batch/jobs/delete | ジョブを削除します |
> | Microsoft.ContainerService/managedClusters/bindings/write | バインディングを書き込みます |
> | Microsoft.ContainerService/managedClusters/certificates.k8s.io/certificatesigningrequests/read | certificatesigningrequests を読み取ります |
> | Microsoft.ContainerService/managedClusters/certificates.k8s.io/certificatesigningrequests/write | certificatesigningrequests を書き込みます |
> | Microsoft.ContainerService/managedClusters/certificates.k8s.io/certificatesigningrequests/delete | certificatesigningrequests を削除します |
> | Microsoft.ContainerService/managedClusters/componentstatuses/read | componentstatuses を読み取ります |
> | Microsoft.ContainerService/managedClusters/componentstatuses/write | componentstatuses を書き込みます |
> | Microsoft.ContainerService/managedClusters/componentstatuses/delete | componentstatuses を削除します |
> | Microsoft.ContainerService/managedClusters/configmaps/read | configmaps を読み取ります |
> | Microsoft.ContainerService/managedClusters/configmaps/write | configmaps を書き込みます |
> | Microsoft.ContainerService/managedClusters/configmaps/delete | configmaps を削除します |
> | Microsoft.ContainerService/managedClusters/coordination.k8s.io/leases/read | リースを読み取ります |
> | Microsoft.ContainerService/managedClusters/coordination.k8s.io/leases/write | リースを書き込みます |
> | Microsoft.ContainerService/managedClusters/coordination.k8s.io/leases/delete | リースを削除します |
> | Microsoft.ContainerService/managedClusters/endpoints/read | エンドポイントを読み取ります |
> | Microsoft.ContainerService/managedClusters/endpoints/write | エンドポイントを書き込みます |
> | Microsoft.ContainerService/managedClusters/endpoints/delete | エンドポイントを削除します |
> | Microsoft.ContainerService/managedClusters/events/read | イベントを読み取ります |
> | Microsoft.ContainerService/managedClusters/events/write | イベントを書き込みます |
> | Microsoft.ContainerService/managedClusters/events/delete | イベントを削除します |
> | Microsoft.ContainerService/managedClusters/events.k8s.io/events/read | イベントを読み取ります |
> | Microsoft.ContainerService/managedClusters/events.k8s.io/events/write | イベントを書き込みます |
> | Microsoft.ContainerService/managedClusters/events.k8s.io/events/delete | イベントを削除します |
> | Microsoft.ContainerService/managedClusters/extensions/daemonsets/read | デーモンセットを読み取ります |
> | Microsoft.ContainerService/managedClusters/extensions/daemonsets/write | デーモンセットを書き込みます |
> | Microsoft.ContainerService/managedClusters/extensions/daemonsets/delete | デーモンセットを削除します |
> | Microsoft.ContainerService/managedClusters/extensions/deployments/read | デプロイを読み取ります |
> | Microsoft.ContainerService/managedClusters/extensions/deployments/write | デプロイを書き込みます |
> | Microsoft.ContainerService/managedClusters/extensions/deployments/delete | デプロイを削除します |
> | Microsoft.ContainerService/managedClusters/extensions/ingresses/read | イングレスを読み取ります |
> | Microsoft.ContainerService/managedClusters/extensions/ingresses/write | イングレスを書き込みます |
> | Microsoft.ContainerService/managedClusters/extensions/ingresses/delete | イングレスを削除します |
> | Microsoft.ContainerService/managedClusters/extensions/networkpolicies/read | networkpolicies を読み取ります |
> | Microsoft.ContainerService/managedClusters/extensions/networkpolicies/write | networkpolicies を書き込みます |
> | Microsoft.ContainerService/managedClusters/extensions/networkpolicies/delete | networkpolicies を削除します |
> | Microsoft.ContainerService/managedClusters/extensions/podsecuritypolicies/read | podsecuritypolicies を読み取ります |
> | Microsoft.ContainerService/managedClusters/extensions/podsecuritypolicies/write | podsecuritypolicies を書き込みます |
> | Microsoft.ContainerService/managedClusters/extensions/podsecuritypolicies/delete | podsecuritypolicies を削除します |
> | Microsoft.ContainerService/managedClusters/extensions/replicasets/read | レプリカセットを読み取ります |
> | Microsoft.ContainerService/managedClusters/extensions/replicasets/write | レプリカセットを書き込みます |
> | Microsoft.ContainerService/managedClusters/extensions/replicasets/delete | レプリカセットを削除します |
> | Microsoft.ContainerService/managedClusters/groups/impersonate/action | グループを偽装します |
> | Microsoft.ContainerService/managedClusters/healthz/read | healthz を読み取ります |
> | Microsoft.ContainerService/managedClusters/healthz/autoregister-completion/read | autoregister-completion を読み取ります |
> | Microsoft.ContainerService/managedClusters/healthz/etcd/read | etcd を読み取ります |
> | Microsoft.ContainerService/managedClusters/healthz/log/read | ログを読み取ります |
> | Microsoft.ContainerService/managedClusters/healthz/ping/read | ping を読み取ります |
> | Microsoft.ContainerService/managedClusters/healthz/poststarthook/apiservice-openapi-controller/read | apiservice-openapi-controller を読み取ります |
> | Microsoft.ContainerService/managedClusters/healthz/poststarthook/apiservice-registration-controller/read | apiservice-registration-controller を読み取ります |
> | Microsoft.ContainerService/managedClusters/healthz/poststarthook/apiservice-status-available-controller/read | apiservice-status-available-controller を読み取ります |
> | Microsoft.ContainerService/managedClusters/healthz/poststarthook/bootstrap-controller/read | bootstrap-controller を読み取ります |
> | Microsoft.ContainerService/managedClusters/healthz/poststarthook/ca-registration/read | ca-registration を読み取ります |
> | Microsoft.ContainerService/managedClusters/healthz/poststarthook/crd-informer-synced/read | crd-informer-synced を読み取ります |
> | Microsoft.ContainerService/managedClusters/healthz/poststarthook/generic-apiserver-start-informers/read | generic-apiserver-start-informers を読み取ります |
> | Microsoft.ContainerService/managedClusters/healthz/poststarthook/kube-apiserver-autoregistration/read | kube-apiserver-autoregistration を読み取ります |
> | Microsoft.ContainerService/managedClusters/healthz/poststarthook/rbac/bootstrap-roles/read | bootstrap-roles を読み取ります |
> | Microsoft.ContainerService/managedClusters/healthz/poststarthook/scheduling/bootstrap-system-priority-classes/read | bootstrap-system-priority-classes を読み取ります |
> | Microsoft.ContainerService/managedClusters/healthz/poststarthook/start-apiextensions-controllers/read | start-apiextensions-controllers を読み取ります |
> | Microsoft.ContainerService/managedClusters/healthz/poststarthook/start-apiextensions-informers/read | start-apiextensions-informers を読み取ります |
> | Microsoft.ContainerService/managedClusters/healthz/poststarthook/start-kube-aggregator-informers/read | start-kube-aggregator-informers を読み取ります |
> | Microsoft.ContainerService/managedClusters/healthz/poststarthook/start-kube-apiserver-admission-initializer/read | start-kube-apiserver-admission-initializer を読み取ります |
> | Microsoft.ContainerService/managedClusters/limitranges/read | limitranges を読み取ります |
> | Microsoft.ContainerService/managedClusters/limitranges/write | limitranges を書き込みます |
> | Microsoft.ContainerService/managedClusters/limitranges/delete | limitranges を削除します |
> | Microsoft.ContainerService/managedClusters/livez/read | livez を読み取ります |
> | Microsoft.ContainerService/managedClusters/livez/autoregister-completion/read | autoregister-completion を読み取ります |
> | Microsoft.ContainerService/managedClusters/livez/etcd/read | etcd を読み取ります |
> | Microsoft.ContainerService/managedClusters/livez/log/read | ログを読み取ります |
> | Microsoft.ContainerService/managedClusters/livez/ping/read | ping を読み取ります |
> | Microsoft.ContainerService/managedClusters/livez/poststarthook/apiservice-openapi-controller/read | apiservice-openapi-controller を読み取ります |
> | Microsoft.ContainerService/managedClusters/livez/poststarthook/apiservice-registration-controller/read | apiservice-registration-controller を読み取ります |
> | Microsoft.ContainerService/managedClusters/livez/poststarthook/apiservice-status-available-controller/read | apiservice-status-available-controller を読み取ります |
> | Microsoft.ContainerService/managedClusters/livez/poststarthook/bootstrap-controller/read | bootstrap-controller を読み取ります |
> | Microsoft.ContainerService/managedClusters/livez/poststarthook/ca-registration/read | ca-registration を読み取ります |
> | Microsoft.ContainerService/managedClusters/livez/poststarthook/crd-informer-synced/read | crd-informer-synced を読み取ります |
> | Microsoft.ContainerService/managedClusters/livez/poststarthook/generic-apiserver-start-informers/read | generic-apiserver-start-informers を読み取ります |
> | Microsoft.ContainerService/managedClusters/livez/poststarthook/kube-apiserver-autoregistration/read | kube-apiserver-autoregistration を読み取ります |
> | Microsoft.ContainerService/managedClusters/livez/poststarthook/rbac/bootstrap-roles/read | bootstrap-roles を読み取ります |
> | Microsoft.ContainerService/managedClusters/livez/poststarthook/scheduling/bootstrap-system-priority-classes/read | bootstrap-system-priority-classes を読み取ります |
> | Microsoft.ContainerService/managedClusters/livez/poststarthook/start-apiextensions-controllers/read | start-apiextensions-controllers を読み取ります |
> | Microsoft.ContainerService/managedClusters/livez/poststarthook/start-apiextensions-informers/read | start-apiextensions-informers を読み取ります |
> | Microsoft.ContainerService/managedClusters/livez/poststarthook/start-kube-aggregator-informers/read | start-kube-aggregator-informers を読み取ります |
> | Microsoft.ContainerService/managedClusters/livez/poststarthook/start-kube-apiserver-admission-initializer/read | start-kube-apiserver-admission-initializer を読み取ります |
> | Microsoft.ContainerService/managedClusters/logs/read | ログを読み取ります |
> | Microsoft.ContainerService/managedClusters/metrics/read | メトリックを読み取ります |
> | Microsoft.ContainerService/managedClusters/metrics.k8s.io/nodes/read | ノードを読み取ります |
> | Microsoft.ContainerService/managedClusters/metrics.k8s.io/pods/read | ポッドを読み取ります |
> | Microsoft.ContainerService/managedClusters/namespaces/read | 名前空間を読み取ります |
> | Microsoft.ContainerService/managedClusters/namespaces/write | namespaces を書き込みます |
> | Microsoft.ContainerService/managedClusters/namespaces/delete | 名前空間を削除します |
> | Microsoft.ContainerService/managedClusters/networking.k8s.io/ingresses/read | イングレスを読み取ります |
> | Microsoft.ContainerService/managedClusters/networking.k8s.io/ingresses/write | イングレスを書き込みます |
> | Microsoft.ContainerService/managedClusters/networking.k8s.io/ingresses/delete | イングレスを削除します |
> | Microsoft.ContainerService/managedClusters/networking.k8s.io/networkpolicies/read | networkpolicies を読み取ります |
> | Microsoft.ContainerService/managedClusters/networking.k8s.io/networkpolicies/write | networkpolicies を書き込みます |
> | Microsoft.ContainerService/managedClusters/networking.k8s.io/networkpolicies/delete | networkpolicies を削除します |
> | Microsoft.ContainerService/managedClusters/node.k8s.io/runtimeclasses/read | runtimeclasses を読み取ります |
> | Microsoft.ContainerService/managedClusters/node.k8s.io/runtimeclasses/write | runtimeclasses を書き込みます |
> | Microsoft.ContainerService/managedClusters/node.k8s.io/runtimeclasses/delete | runtimeclasses を削除します |
> | Microsoft.ContainerService/managedClusters/nodes/read | ノードを読み取ります |
> | Microsoft.ContainerService/managedClusters/nodes/write | ノードを書き込みます |
> | Microsoft.ContainerService/managedClusters/nodes/delete | ノードを削除します |
> | Microsoft.ContainerService/managedClusters/openapi/v2/read | v2 を読み取ります |
> | Microsoft.ContainerService/managedClusters/persistentvolumeclaims/read | persistentvolumeclaims を読み取ります |
> | Microsoft.ContainerService/managedClusters/persistentvolumeclaims/write | persistentvolumeclaims を書き込みます |
> | Microsoft.ContainerService/managedClusters/persistentvolumeclaims/delete | persistentvolumeclaims を削除します |
> | Microsoft.ContainerService/managedClusters/persistentvolumes/read | persistentvolumes を読み取ります |
> | Microsoft.ContainerService/managedClusters/persistentvolumes/write | persistentvolumes を書き込みます |
> | Microsoft.ContainerService/managedClusters/persistentvolumes/delete | persistentvolumes を削除します |
> | Microsoft.ContainerService/managedClusters/pods/read | ポッドを読み取ります |
> | Microsoft.ContainerService/managedClusters/pods/write | ポッドを書き込みます |
> | Microsoft.ContainerService/managedClusters/pods/delete | ポッドを削除します |
> | Microsoft.ContainerService/managedClusters/podtemplates/read | podtemplates を読み取ります |
> | Microsoft.ContainerService/managedClusters/podtemplates/write | podtemplates を書き込みます |
> | Microsoft.ContainerService/managedClusters/podtemplates/delete | podtemplates を削除します |
> | Microsoft.ContainerService/managedClusters/policy/poddisruptionbudgets/read | poddisruptionbudgets を読み取ります |
> | Microsoft.ContainerService/managedClusters/policy/poddisruptionbudgets/write | poddisruptionbudgets を書き込みます |
> | Microsoft.ContainerService/managedClusters/policy/poddisruptionbudgets/delete | poddisruptionbudgets を削除します |
> | Microsoft.ContainerService/managedClusters/policy/podsecuritypolicies/read | podsecuritypolicies を読み取ります |
> | Microsoft.ContainerService/managedClusters/policy/podsecuritypolicies/write | podsecuritypolicies を書き込みます |
> | Microsoft.ContainerService/managedClusters/policy/podsecuritypolicies/delete | podsecuritypolicies を削除します |
> | Microsoft.ContainerService/managedClusters/policy/podsecuritypolicies/use/action | podsecuritypolicies でアクションを使用します |
> | Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/clusterrolebindings/read | clusterrolebindings を読み取ります |
> | Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/clusterrolebindings/write | clusterrolebindings を書き込みます |
> | Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/clusterrolebindings/delete | clusterrolebindings を削除します |
> | Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/clusterroles/read | clusterroles を読み取ります |
> | Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/clusterroles/write | clusterroles を書き込みます |
> | Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/clusterroles/delete | clusterroles を削除します |
> | Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/clusterroles/bind/action | clusterroles をバインドします |
> | Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/clusterroles/escalate/action | エスカレーションします |
> | Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/rolebindings/read | rolebindings を読み取ります |
> | Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/rolebindings/write | rolebindings を書き込みます |
> | Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/rolebindings/delete | rolebindings を削除します |
> | Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/roles/read | ロールを読み取ります |
> | Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/roles/write | ロールを書き込みます |
> | Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/roles/delete | ロールを削除します |
> | Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/roles/bind/action | ロールをバインドします |
> | Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/roles/escalate/action | ロールをエスカレーションします |
> | Microsoft.ContainerService/managedClusters/readyz/read | readyz を読み取ります |
> | Microsoft.ContainerService/managedClusters/readyz/autoregister-completion/read | autoregister-completion を読み取ります |
> | Microsoft.ContainerService/managedClusters/readyz/etcd/read | etcd を読み取ります |
> | Microsoft.ContainerService/managedClusters/readyz/log/read | ログを読み取ります |
> | Microsoft.ContainerService/managedClusters/readyz/ping/read | ping を読み取ります |
> | Microsoft.ContainerService/managedClusters/readyz/poststarthook/apiservice-openapi-controller/read | apiservice-openapi-controller を読み取ります |
> | Microsoft.ContainerService/managedClusters/readyz/poststarthook/apiservice-registration-controller/read | apiservice-registration-controller を読み取ります |
> | Microsoft.ContainerService/managedClusters/readyz/poststarthook/apiservice-status-available-controller/read | apiservice-status-available-controller を読み取ります |
> | Microsoft.ContainerService/managedClusters/readyz/poststarthook/bootstrap-controller/read | bootstrap-controller を読み取ります |
> | Microsoft.ContainerService/managedClusters/readyz/poststarthook/ca-registration/read | ca-registration を読み取ります |
> | Microsoft.ContainerService/managedClusters/readyz/poststarthook/crd-informer-synced/read | crd-informer-synced を読み取ります |
> | Microsoft.ContainerService/managedClusters/readyz/poststarthook/generic-apiserver-start-informers/read | generic-apiserver-start-informers を読み取ります |
> | Microsoft.ContainerService/managedClusters/readyz/poststarthook/kube-apiserver-autoregistration/read | kube-apiserver-autoregistration を読み取ります |
> | Microsoft.ContainerService/managedClusters/readyz/poststarthook/rbac/bootstrap-roles/read | bootstrap-roles を読み取ります |
> | Microsoft.ContainerService/managedClusters/readyz/poststarthook/scheduling/bootstrap-system-priority-classes/read | bootstrap-system-priority-classes を読み取ります |
> | Microsoft.ContainerService/managedClusters/readyz/poststarthook/start-apiextensions-controllers/read | start-apiextensions-controllers を読み取ります |
> | Microsoft.ContainerService/managedClusters/readyz/poststarthook/start-apiextensions-informers/read | start-apiextensions-informers を読み取ります |
> | Microsoft.ContainerService/managedClusters/readyz/poststarthook/start-kube-aggregator-informers/read | start-kube-aggregator-informers を読み取ります |
> | Microsoft.ContainerService/managedClusters/readyz/poststarthook/start-kube-apiserver-admission-initializer/read | start-kube-apiserver-admission-initializer を読み取ります |
> | Microsoft.ContainerService/managedClusters/readyz/shutdown/read | shutdown を読み取ります |
> | Microsoft.ContainerService/managedClusters/replicationcontrollers/read | replicationcontrollers を読み取ります |
> | Microsoft.ContainerService/managedClusters/replicationcontrollers/write | replicationcontrollers を書き込みます |
> | Microsoft.ContainerService/managedClusters/replicationcontrollers/delete | replicationcontrollers を削除します |
> | Microsoft.ContainerService/managedClusters/resetMetrics/read | resetMetrics を読み取ります |
> | Microsoft.ContainerService/managedClusters/resourcequotas/read | resourcequotas を読み取ります |
> | Microsoft.ContainerService/managedClusters/resourcequotas/write | resourcequotas を書き込みます |
> | Microsoft.ContainerService/managedClusters/resourcequotas/delete | resourcequotas を削除します |
> | Microsoft.ContainerService/managedClusters/scheduling.k8s.io/priorityclasses/read | priorityclasses を読み取ります |
> | Microsoft.ContainerService/managedClusters/scheduling.k8s.io/priorityclasses/write | priorityclasses を書き込みます |
> | Microsoft.ContainerService/managedClusters/scheduling.k8s.io/priorityclasses/delete | priorityclasses を削除します |
> | Microsoft.ContainerService/managedClusters/secrets/read | シークレットを読み取ります |
> | Microsoft.ContainerService/managedClusters/secrets/write | シークレットを書き込みます |
> | Microsoft.ContainerService/managedClusters/secrets/delete | シークレットを削除します |
> | Microsoft.ContainerService/managedClusters/serviceaccounts/read | serviceaccounts を読み取ります |
> | Microsoft.ContainerService/managedClusters/serviceaccounts/write | serviceaccounts を書き込みます |
> | Microsoft.ContainerService/managedClusters/serviceaccounts/delete | serviceaccounts を削除します |
> | Microsoft.ContainerService/managedClusters/serviceaccounts/impersonate/action | serviceaccounts を偽装します |
> | Microsoft.ContainerService/managedClusters/services/read | サービスを読み取ります |
> | Microsoft.ContainerService/managedClusters/services/write | サービスを書き込みます |
> | Microsoft.ContainerService/managedClusters/services/delete | サービスを削除します |
> | Microsoft.ContainerService/managedClusters/storage.k8s.io/csidrivers/read | csidrivers を読み取ります |
> | Microsoft.ContainerService/managedClusters/storage.k8s.io/csidrivers/write | csidrivers を書き込みます |
> | Microsoft.ContainerService/managedClusters/storage.k8s.io/csidrivers/delete | csidrivers を削除します |
> | Microsoft.ContainerService/managedClusters/storage.k8s.io/csinodes/read | csinodes を読み取ります |
> | Microsoft.ContainerService/managedClusters/storage.k8s.io/csinodes/write | csinodes を書き込みます |
> | Microsoft.ContainerService/managedClusters/storage.k8s.io/csinodes/delete | csinodes を削除します |
> | Microsoft.ContainerService/managedClusters/storage.k8s.io/storageclasses/read | storageclasses を読み取ります |
> | Microsoft.ContainerService/managedClusters/storage.k8s.io/storageclasses/write | storageclasses を書き込みます |
> | Microsoft.ContainerService/managedClusters/storage.k8s.io/storageclasses/delete | storageclasses を削除します |
> | Microsoft.ContainerService/managedClusters/storage.k8s.io/volumeattachments/read | volumeattachments を読み取ります |
> | Microsoft.ContainerService/managedClusters/storage.k8s.io/volumeattachments/write | volumeattachments を書き込みます |
> | Microsoft.ContainerService/managedClusters/storage.k8s.io/volumeattachments/delete | volumeattachments を削除します |
> | Microsoft.ContainerService/managedClusters/swagger-api/read | swagger-api を読み取ります |
> | Microsoft.ContainerService/managedClusters/swagger-ui/read | swagger-ui を読み取ります |
> | Microsoft.ContainerService/managedClusters/ui/read | UI を読み取ります |
> | Microsoft.ContainerService/managedClusters/users/impersonate/action | ユーザーを偽装します |
> | Microsoft.ContainerService/managedClusters/version/read | バージョンを読み取ります |

### <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

Azure サービス:[Azure Dev Spaces](../dev-spaces/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.DevSpaces/register/action | サブスクリプションに Microsoft Dev Spaces リソース プロバイダーを登録します |
> | Microsoft.DevSpaces/controllers/read | Azure Dev Spaces コントローラーのプロパティを読み取ります |
> | Microsoft.DevSpaces/controllers/write | Azure Dev Spaces コントローラーのプロパティを作成または更新します |
> | Microsoft.DevSpaces/controllers/delete | Azure Dev Spaces コントローラーとデータプレーン サービスを削除します |
> | Microsoft.DevSpaces/controllers/listConnectionDetails/action | Azure Dev Spaces コントローラーのインフラストラクチャの接続詳細を一覧表示します |
> | Microsoft.DevSpaces/locations/checkContainerHostMapping/action | コンテナー ホストの既存のコントローラーのマッピングを確認します |
> | Microsoft.DevSpaces/locations/checkContainerHostMapping/read | コンテナー ホストの既存のコントローラーのマッピングを確認します |
> | Microsoft.DevSpaces/locations/operationresults/read | 非同期操作の結果を読み取ります |
> | Microsoft.DevSpaces/operations/read | Microsoft Dev Spaces リソース プロバイダーでサポートされているすべての操作を一覧表示します |

## <a name="databases"></a>データベース

### <a name="microsoftcache"></a>Microsoft.Cache

Azure サービス:[Azure Cache for Redis](../azure-cache-for-redis/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Cache/checknameavailability/action | 新しい Redis Cache で名前を使用できるかどうかを確認します。 |
> | Microsoft.Cache/register/action | "Microsoft.Cache" リソース プロバイダーをサブスクリプションに登録します。 |
> | Microsoft.Cache/unregister/action | サブスクリプションへの "Microsoft.Cache" リソース プロバイダーの登録を解除します。 |
> | Microsoft.Cache/locations/asyncOperations/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.Cache/locations/operationResults/read | 前に "Location" ヘッダーがクライアントに返された、実行時間の長い操作の結果を取得します。 |
> | Microsoft.Cache/locations/operationsStatus/read | 前にクライアントに対して "AzureAsync" ヘッダーが返された長期操作の結果を取得します |
> | Microsoft.Cache/operations/read | "Microsoft.Cache" プロバイダーがサポートする操作を一覧表示します。 |
> | Microsoft.Cache/redis/write | 管理ポータルで Redis Cache の設定と構成を変更します。 |
> | Microsoft.Cache/redis/read | 管理ポータルで Redis Cache の設定と構成を表示します。 |
> | Microsoft.Cache/redis/delete | Redis Cache 全体を削除します。 |
> | Microsoft.Cache/redis/listKeys/action | 管理ポータルで Redis Cache のアクセス キーの値を表示します。 |
> | Microsoft.Cache/redis/regenerateKey/action | 管理ポータルで Redis Cache のアクセス キーの値を変更します。 |
> | Microsoft.Cache/redis/import/action | 指定された形式のデータを複数の BLOB から Redis にインポートします。 |
> | Microsoft.Cache/redis/export/action | 指定された形式のプレフィックス付きストレージ BLOB に Redis データをエクスポートします。 |
> | Microsoft.Cache/redis/forceReboot/action | キャッシュ インスタンスを強制的に再起動します。データが失われる可能性があります。 |
> | Microsoft.Cache/redis/stop/action | Azure Cache for Redis を停止します。データが失われるおそれがあります。 |
> | Microsoft.Cache/redis/start/action | Azure Cache for Redis を開始します |
> | Microsoft.Cache/redis/eventGridFilters/read | Redis Cache Event Grid フィルターを取得します |
> | Microsoft.Cache/redis/eventGridFilters/write | Redis Cache Event Grid フィルターを更新します |
> | Microsoft.Cache/redis/eventGridFilters/delete | Redis Cache Event Grid フィルターを削除します |
> | Microsoft.Cache/redis/firewallRules/read | Redis Cache の IP ファイアウォール規則を取得します。 |
> | Microsoft.Cache/redis/firewallRules/write | Redis Cache の IP ファイアウォール規則を編集します。 |
> | Microsoft.Cache/redis/firewallRules/delete | Redis Cache の IP ファイアウォール規則を削除します。 |
> | Microsoft.Cache/redis/linkedServers/read | Redis Cache に関連付けられているリンク サーバーを取得します。 |
> | Microsoft.Cache/redis/linkedServers/write | Redis Cache にリンク サーバーを追加します。 |
> | Microsoft.Cache/redis/linkedServers/delete | Redis Cache からリンク サーバーを削除します。 |
> | Microsoft.Cache/redis/metricDefinitions/read | Redis Cache の利用可能なメトリックを取得します。 |
> | Microsoft.Cache/redis/patchSchedules/read | Redis Cache のパッチ スケジュールを取得します。 |
> | Microsoft.Cache/redis/patchSchedules/write | Redis Cache のパッチ スケジュールを変更します。 |
> | Microsoft.Cache/redis/patchSchedules/delete | Redis Cache のパッチ スケジュールを削除します。 |
> | Microsoft.Cache/redis/privateEndpointConnectionProxies/validate/action | プライベート エンドポイント接続プロキシを検証します |
> | Microsoft.Cache/redis/privateEndpointConnectionProxies/read | プライベート エンドポイント接続プロキシを取得します |
> | Microsoft.Cache/redis/privateEndpointConnectionProxies/write | プライベート エンドポイント接続プロキシを作成します |
> | Microsoft.Cache/redis/privateEndpointConnectionProxies/delete | プライベート エンドポイント接続プロキシを削除します |
> | Microsoft.Cache/redis/privateEndpointConnections/read | プライベート エンドポイント接続が読み取られます |
> | Microsoft.Cache/redis/privateEndpointConnections/write | プライベート エンドポイント接続を書き込みます |
> | Microsoft.Cache/redis/privateEndpointConnections/delete | プライベート エンドポイント接続が削除されます |
> | Microsoft.Cache/redis/privateLinkResources/read | プライベート リンクの接続先にできる Redis サブリソースの 'groupId' を読み取ります |
> | Microsoft.Cache/redisEnterprise/delete | Redis Enterprise キャッシュ全体を削除します |
> | Microsoft.Cache/redisEnterprise/read | 管理ポータルで Redis Enterprise キャッシュの設定と構成を表示します |
> | Microsoft.Cache/redisEnterprise/write | 管理ポータルで Redis Enterprise キャッシュの設定と構成を変更します |
> | Microsoft.Cache/redisEnterprise/databases/delete | Redis Enterprise データベースとそのコンテンツを削除します |
> | Microsoft.Cache/redisEnterprise/databases/read | 管理ポータルで Redis Enterprise キャッシュ データベースの設定と構成を表示します |
> | Microsoft.Cache/redisEnterprise/databases/write | 管理ポータルで Redis Enterprise キャッシュ データベースの設定と構成を変更します |
> | Microsoft.Cache/redisEnterprise/databases/forceUnlink/action | Georeplica Redis Enterprise データベースをその Peer ノードから強制的にリンク解除します |
> | Microsoft.Cache/redisEnterprise/privateEndpointConnectionProxies/validate/action | プライベート エンドポイント接続プロキシを検証します |
> | Microsoft.Cache/redisEnterprise/privateEndpointConnectionProxies/read | プライベート エンドポイント接続プロキシを取得します |
> | Microsoft.Cache/redisEnterprise/privateEndpointConnectionProxies/write | プライベート エンドポイント接続プロキシを作成します |
> | Microsoft.Cache/redisEnterprise/privateEndpointConnectionProxies/delete | プライベート エンドポイント接続プロキシを削除します |
> | Microsoft.Cache/redisEnterprise/privateEndpointConnectionProxies/operationResults/read | 管理ポータルでプライベート エンドポイント接続操作の結果を表示します |
> | Microsoft.Cache/redisEnterprise/privateEndpointConnections/read | プライベート エンドポイント接続が読み取られます |
> | Microsoft.Cache/redisEnterprise/privateEndpointConnections/write | プライベート エンドポイント接続を書き込みます |
> | Microsoft.Cache/redisEnterprise/privateEndpointConnections/delete | プライベート エンドポイント接続が削除されます |
> | Microsoft.Cache/redisEnterprise/privateLinkResources/read | プライベート リンクの接続先にできる Redis サブリソースの 'groupId' を読み取ります |
> | Microsoft.Cache/redisEnterprise/providers/Microsoft.Insights/metricDefinitions/read | Redis Enterprise Cache で使用可能なメトリックを取得します |

### <a name="microsoftdatafactory"></a>Microsoft.DataFactory

Azure サービス:[Data Factory](../data-factory/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.DataFactory/register/action | データ ファクトリ リソース プロバイダーのサブスクリプションを登録します。 |
> | Microsoft.DataFactory/unregister/action | データ ファクトリ リソース プロバイダーへのサブスクリプションの登録を解除します。 |
> | Microsoft.DataFactory/checkazuredatafactorynameavailability/read | データ ファクトリ名が使用できるかどうかを確認します。 |
> | Microsoft.DataFactory/datafactories/read | データ ファクトリを読み取ります。 |
> | Microsoft.DataFactory/datafactories/write | データ ファクトリを作成または更新します。 |
> | Microsoft.DataFactory/datafactories/delete | データ ファクトリを削除します。 |
> | Microsoft.DataFactory/datafactories/activitywindows/read | 指定されたパラメーターを使用して、データ ファクトリのアクティビティ ウィンドウを読み取ります。 |
> | Microsoft.DataFactory/datafactories/datapipelines/read | パイプラインを読み取ります |
> | Microsoft.DataFactory/datafactories/datapipelines/delete | パイプラインを削除します。 |
> | Microsoft.DataFactory/datafactories/datapipelines/pause/action | パイプラインを一時停止します。 |
> | Microsoft.DataFactory/datafactories/datapipelines/resume/action | パイプラインを再開します。 |
> | Microsoft.DataFactory/datafactories/datapipelines/update/action | パイプラインを更新します。 |
> | Microsoft.DataFactory/datafactories/datapipelines/write | パイプラインを作成または更新します。 |
> | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | 指定されたパラメーターを使用して、パイプライン アクティビティのアクティビティ ウィンドウを読み取ります。 |
> | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | 指定されたパラメーターを使用して、パイプラインのアクティビティ ウィンドウを読み取ります。 |
> | Microsoft.DataFactory/datafactories/datasets/read | データセットを読み取ります。 |
> | Microsoft.DataFactory/datafactories/datasets/delete | データセットを削除します。 |
> | Microsoft.DataFactory/datafactories/datasets/write | データセットを作成または更新します。 |
> | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | 指定されたパラメーターを使用して、データセットのアクティビティ ウィンドウを読み取ります。 |
> | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | 指定された開始時刻の指定されたデータ セットについて、データ スライスの実行を読み取ります。 |
> | Microsoft.DataFactory/datafactories/datasets/slices/read | 指定された期間内のデータ スライスを取得します。 |
> | Microsoft.DataFactory/datafactories/datasets/slices/write | データ スライスの状態を更新します。 |
> | Microsoft.DataFactory/datafactories/gateways/read | ゲートウェイを読み取ります。 |
> | Microsoft.DataFactory/datafactories/gateways/write | ゲートウェイを作成または更新します。 |
> | Microsoft.DataFactory/datafactories/gateways/delete | ゲートウェイを削除します。 |
> | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | ゲートウェイの接続情報を読み取ります。 |
> | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | ゲートウェイの認証キーの一覧を取得します。 |
> | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | ゲートウェイの認証キーを再生成します。 |
> | Microsoft.DataFactory/datafactories/linkedServices/read | リンクされたサービスを読み取ります。 |
> | Microsoft.DataFactory/datafactories/linkedServices/delete | リンクされたサービスを削除します。 |
> | Microsoft.DataFactory/datafactories/linkedServices/write | リンクされたサービスを作成または更新します。 |
> | Microsoft.DataFactory/datafactories/runs/loginfo/read | ログを含む BLOB コンテナーの SAS URI を読み取ります。 |
> | Microsoft.DataFactory/datafactories/tables/read | データセットを読み取ります。 |
> | Microsoft.DataFactory/datafactories/tables/delete | データセットを削除します。 |
> | Microsoft.DataFactory/datafactories/tables/write | データセットを作成または更新します。 |
> | Microsoft.DataFactory/factories/read | データ ファクトリを読み取ります。 |
> | Microsoft.DataFactory/factories/write | データ ファクトリを作成または更新します。 |
> | Microsoft.DataFactory/factories/delete | データ ファクトリを削除します。 |
> | Microsoft.DataFactory/factories/createdataflowdebugsession/action | データ フローのデバッグ セッションを作成します。 |
> | Microsoft.DataFactory/factories/startdataflowdebugsession/action | データ フロー デバッグ セッションを開始します。 |
> | Microsoft.DataFactory/factories/addDataFlowToDebugSession/action | プレビュー用のデバッグ セッションにデータ フローを追加します。 |
> | Microsoft.DataFactory/factories/executeDataFlowDebugCommand/action | データ フローのデバッグ コマンドを実行します。 |
> | Microsoft.DataFactory/factories/deletedataflowdebugsession/action | データ フロー デバッグ セッションを削除します。 |
> | Microsoft.DataFactory/factories/querydataflowdebugsessions/action | データ フローのデバッグ セッションをクエリします。 |
> | Microsoft.DataFactory/factories/cancelpipelinerun/action | 実行 ID に指定されたパイプラインの実行を取り消します。 |
> | Microsoft.DataFactory/factories/cancelSandboxPipelineRun/action | パイプラインのデバッグ実行をキャンセルします。 |
> | Microsoft.DataFactory/factories/sandboxpipelineruns/action | デバッグ パイプライン実行を照会します。 |
> | Microsoft.DataFactory/factories/querytriggers/action | トリガーのクエリが実行されます。 |
> | Microsoft.DataFactory/factories/getFeatureValue/action | 特定の場所に対する露出調整機能の値を取得します。 |
> | Microsoft.DataFactory/factories/queryFeaturesValue/action | 機能の一覧に対する公開制御機能の値を取得します |
> | Microsoft.DataFactory/factories/getDataPlaneAccess/action | ADF DataPlane サービスへのアクセスを取得します。 |
> | Microsoft.DataFactory/factories/getGitHubAccessToken/action | GitHub アクセス トークンを取得します。 |
> | Microsoft.DataFactory/factories/querytriggerruns/action | トリガーの実行を照会します。 |
> | Microsoft.DataFactory/factories/querypipelineruns/action | パイプライン実行を照会します。 |
> | Microsoft.DataFactory/factories/querydebugpipelineruns/action | デバッグ パイプライン実行を照会します。 |
> | Microsoft.DataFactory/factories/dataflows/read | データ フローを読み取ります。 |
> | Microsoft.DataFactory/factories/dataflows/delete | データ フローを削除します。 |
> | Microsoft.DataFactory/factories/dataflows/write | データ フローを作成または更新します |
> | Microsoft.DataFactory/factories/datasets/read | データセットを読み取ります。 |
> | Microsoft.DataFactory/factories/datasets/delete | データセットを削除します。 |
> | Microsoft.DataFactory/factories/datasets/write | データセットを作成または更新します。 |
> | Microsoft.DataFactory/factories/debugpipelineruns/cancel/action | パイプラインのデバッグ実行をキャンセルします。 |
> | Microsoft.DataFactory/factories/getDataPlaneAccess/read | ADF DataPlane サービスへのアクセスを読み取ります。 |
> | Microsoft.DataFactory/factories/getFeatureValue/read | 特定の場所に対する露出調整機能の値を読み取ります。 |
> | Microsoft.DataFactory/factories/integrationruntimes/read | 統合ランタイムを読み取ります。 |
> | Microsoft.DataFactory/factories/integrationruntimes/write | 統合ランタイムを作成または更新します。 |
> | Microsoft.DataFactory/factories/integrationruntimes/delete | 統合ランタイムを削除します。 |
> | Microsoft.DataFactory/factories/integrationruntimes/start/action | 統合ランタイムを開始します。 |
> | Microsoft.DataFactory/factories/integrationruntimes/stop/action | 統合ランタイムを停止します。 |
> | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/action | 統合ランタイムの接続情報を読み取ります。 |
> | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/action | 統合ランタイムの認証キーの一覧を取得します。 |
> | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | 指定された統合ランタイムの資格情報を同期します。 |
> | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | 指定された統合ランタイムをアップグレードします。 |
> | Microsoft.DataFactory/factories/integrationruntimes/createexpressshirinstalllink/action | 自己ホスト型統合ランタイムの高速インストール リンクを作成します。 |
> | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | 指定された統合ランタイムの認証キーを再生成します。 |
> | Microsoft.DataFactory/factories/integrationruntimes/removelinks/action | 指定された Integration Runtime からリンクされた Integration Runtime の参照を削除します。 |
> | Microsoft.DataFactory/factories/integrationruntimes/linkedIntegrationRuntime/action | 指定された共有統合ランタイム上にリンクされた統合ランタイムの参照を作成します。 |
> | Microsoft.DataFactory/factories/integrationruntimes/getObjectMetadata/action | 指定された統合ランタイムの SSIS 統合ランタイム メタデータを取得します。 |
> | Microsoft.DataFactory/factories/integrationruntimes/refreshObjectMetadata/action | 指定された統合ランタイムの SSIS 統合ランタイム メタデータを更新します。 |
> | Microsoft.DataFactory/factories/integrationruntimes/enableInteractiveQuery/action | インタラクティブな編集セッションを有効にします。 |
> | Microsoft.DataFactory/factories/integrationruntimes/disableInteractiveQuery/action | インタラクティブな編集セッションを無効にします。 |
> | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | 統合ランタイムの状態を読み取ります。 |
> | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | 統合ランタイムの監視データを取得します。 |
> | Microsoft.DataFactory/factories/integrationruntimes/nodes/read | 指定された統合ランタイムのノードを読み取ります。 |
> | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | 指定された統合ランタイムのノードを削除します。 |
> | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | 自己ホスト型統合ランタイム ノードを更新します。 |
> | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | 統合ランタイムの指定されたノードの IP アドレスを返します。 |
> | Microsoft.DataFactory/factories/linkedServices/read | リンクされたサービスを読み取ります。 |
> | Microsoft.DataFactory/factories/linkedServices/delete | リンクされたサービスを削除します。 |
> | Microsoft.DataFactory/factories/linkedServices/write | リンクされたサービスを作成または更新します。 |
> | Microsoft.DataFactory/factories/managedVirtualNetworks/read | マネージド仮想ネットワークを読み取ります。 |
> | Microsoft.DataFactory/factories/managedVirtualNetworks/write | マネージド仮想ネットワークを作成または更新します。 |
> | Microsoft.DataFactory/factories/managedVirtualNetworks/managedPrivateEndpoints/read | マネージド プライベート エンドポイントを読み取ります。 |
> | Microsoft.DataFactory/factories/managedVirtualNetworks/managedPrivateEndpoints/write | マネージド プライベート エンドポイントを作成または更新します。 |
> | Microsoft.DataFactory/factories/managedVirtualNetworks/managedPrivateEndpoints/delete | マネージド プライベート エンドポイントを削除します。 |
> | Microsoft.DataFactory/factories/operationResults/read | 操作の結果を取得します。 |
> | Microsoft.DataFactory/factories/pipelineruns/read | パイプライン実行を読み取ります。 |
> | Microsoft.DataFactory/factories/pipelineruns/cancel/action | 実行 ID に指定されたパイプラインの実行を取り消します。 |
> | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/action | 指定されたパイプライン実行 ID のアクティビティの実行を照会します。 |
> | Microsoft.DataFactory/factories/pipelineruns/activityruns/read | 指定されたパイプライン実行 ID のアクティビティの実行を読み取ります。 |
> | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/read | 指定したパイプライン実行 ID のクエリ アクティビティ実行の結果を読み取ります。 |
> | Microsoft.DataFactory/factories/pipelines/read | パイプラインを読み取ります。 |
> | Microsoft.DataFactory/factories/pipelines/delete | パイプラインを削除します。 |
> | Microsoft.DataFactory/factories/pipelines/write | パイプラインを作成または更新します。 |
> | Microsoft.DataFactory/factories/pipelines/createrun/action | パイプラインの実行を作成します。 |
> | Microsoft.DataFactory/factories/pipelines/sandbox/action | パイプラインのデバッグ実行環境を作成します。 |
> | Microsoft.DataFactory/factories/pipelines/pipelineruns/read | パイプライン実行を読み取ります。 |
> | Microsoft.DataFactory/factories/pipelines/pipelineruns/activityruns/progress/read | アクティビティ実行の進行状況を取得します。 |
> | Microsoft.DataFactory/factories/pipelines/sandbox/create/action | パイプラインのデバッグ実行環境を作成します。 |
> | Microsoft.DataFactory/factories/pipelines/sandbox/run/action | パイプラインのデバッグ実行を作成します。 |
> | Microsoft.DataFactory/factories/privateEndpointConnectionProxies/read | プライベート エンドポイント接続プロキシを読み取ります。 |
> | Microsoft.DataFactory/factories/privateEndpointConnectionProxies/write | プライベート エンドポイント接続プロキシの作成または更新 |
> | Microsoft.DataFactory/factories/privateEndpointConnectionProxies/delete | プライベート エンドポイント接続プロキシが削除されます。 |
> | Microsoft.DataFactory/factories/privateEndpointConnectionProxies/validate/action | プライベート エンドポイント接続プロキシが検証されます |
> | Microsoft.DataFactory/factories/privateEndpointConnectionProxies/operationresults/read | プライベート エンドポイント接続プロキシの作成の結果を読み取ります。 |
> | Microsoft.DataFactory/factories/privateEndpointConnectionProxies/operationstatuses/read | プライベート エンドポイント接続プロキシの作成状態を読み取ります。 |
> | Microsoft.DataFactory/factories/queryFeaturesValue/read | 機能の一覧に対する公開制御機能の値を読み取ります。 |
> | Microsoft.DataFactory/factories/querypipelineruns/read | クエリ パイプライン実行の結果を読み取ります。 |
> | Microsoft.DataFactory/factories/querytriggerruns/read | トリガー実行の結果を読み取ります。 |
> | Microsoft.DataFactory/factories/sandboxpipelineruns/read | パイプラインのデバッグ実行情報を取得します。 |
> | Microsoft.DataFactory/factories/sandboxpipelineruns/sandboxActivityRuns/read | アクティビティのデバッグ実行情報を取得します。 |
> | Microsoft.DataFactory/factories/triggerruns/read | トリガーの実行を読み取ります。 |
> | Microsoft.DataFactory/factories/triggers/read | トリガーを読み取ります。 |
> | Microsoft.DataFactory/factories/triggers/write | トリガーを作成または更新します。 |
> | Microsoft.DataFactory/factories/triggers/delete | トリガーを削除します。 |
> | Microsoft.DataFactory/factories/triggers/subscribetoevents/action | イベントをサブスクライブします。 |
> | Microsoft.DataFactory/factories/triggers/geteventsubscriptionstatus/action | イベント サブスクリプションの状態。 |
> | Microsoft.DataFactory/factories/triggers/unsubscribefromevents/action | イベントのサブスクライブを解除します。 |
> | Microsoft.DataFactory/factories/triggers/querysubscriptionevents/action | サブスクリプション イベントのクエリを実行します。 |
> | Microsoft.DataFactory/factories/triggers/deletequeuedsubscriptionevents/action | キューに登録済みのサブスクリプション イベントを削除します。 |
> | Microsoft.DataFactory/factories/triggers/start/action | トリガーを開始します。 |
> | Microsoft.DataFactory/factories/triggers/stop/action | トリガーを停止します。 |
> | Microsoft.DataFactory/factories/triggers/triggerruns/read | トリガーの実行を読み取ります。 |
> | Microsoft.DataFactory/factories/triggers/triggerruns/cancel/action | 指定したトリガー実行 ID のトリガー実行を取り消します。 |
> | Microsoft.DataFactory/factories/triggers/triggerruns/rerun/action | 指定したトリガー実行 ID のトリガー実行を再実行します。 |
> | Microsoft.DataFactory/locations/configureFactoryRepo/action | ファクトリのリポジトリを構成します。 |
> | Microsoft.DataFactory/locations/getFeatureValue/action | 特定の場所に対する露出調整機能の値を取得します。 |
> | Microsoft.DataFactory/locations/getFeatureValue/read | 特定の場所に対する露出調整機能の値を読み取ります。 |
> | Microsoft.DataFactory/operations/read | Microsoft Data Factory プロバイダーのすべての操作を読み取ります。 |

### <a name="microsoftdatamigration"></a>Microsoft.DataMigration

Azure サービス:[Azure Database Migration Service](../dms/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.DataMigration/register/action | サブスクリプションを Azure Database Migration Service プロバイダーに登録します。 |
> | Microsoft.DataMigration/locations/operationResults/read | 202 (Accepted) 応答に関連した長時間実行操作の状態を取得します。 |
> | Microsoft.DataMigration/locations/operationStatuses/read | 202 (Accepted) 応答に関連した長時間実行操作の状態を取得します。 |
> | Microsoft.DataMigration/services/read | リソースに関する情報を読み取ります。 |
> | Microsoft.DataMigration/services/write | リソースとそのプロパティを作成または更新します。 |
> | Microsoft.DataMigration/services/delete | リソースおよびそのすべての子を削除します。 |
> | Microsoft.DataMigration/services/stop/action | DMS サービスを停止してコストを最小限に抑えます。 |
> | Microsoft.DataMigration/services/start/action | DMS サービスを開始して、移行をもう一度処理することができるようにします。 |
> | Microsoft.DataMigration/services/checkStatus/action | サービスが配置され実行されているかどうかを確認します。 |
> | Microsoft.DataMigration/services/configureWorker/action | サービスの使用可能なワーカーに対して DMS ワーカーを構成します |
> | Microsoft.DataMigration/services/addWorker/action | サービスの使用可能なワーカーに対して DMS ワーカーを追加します |
> | Microsoft.DataMigration/services/removeWorker/action | サービスの使用可能なワーカーに対して DMS ワーカーを削除します |
> | Microsoft.DataMigration/services/updateAgentConfig/action | 提供された値で DMS エージェントの構成を更新します。 |
> | Microsoft.DataMigration/services/getHybridDownloadLink/action | RP Blob Storage から DMS ワーカーのパッケージ ダウンロード リンクを取得します。 |
> | Microsoft.DataMigration/services/projects/read | リソースに関する情報を読み取ります。 |
> | Microsoft.DataMigration/services/projects/write | Azure Database Migration Service タスクのタスクを実行します。 |
> | Microsoft.DataMigration/services/projects/delete | リソースおよびそのすべての子を削除します。 |
> | Microsoft.DataMigration/services/projects/accessArtifacts/action | プロジェクト成果物の GET または PUT に使用できる URL を生成します。 |
> | Microsoft.DataMigration/services/projects/tasks/read | リソースに関する情報を読み取ります。 |
> | Microsoft.DataMigration/services/projects/tasks/write | Azure Database Migration Service タスクのタスクを実行します。 |
> | Microsoft.DataMigration/services/projects/tasks/delete | リソースおよびそのすべての子を削除します。 |
> | Microsoft.DataMigration/services/projects/tasks/cancel/action | タスクが現在実行されている場合、そのタスクを取り消します。 |
> | Microsoft.DataMigration/services/serviceTasks/read | リソースに関する情報を読み取ります。 |
> | Microsoft.DataMigration/services/serviceTasks/write | Azure Database Migration Service タスクのタスクを実行します。 |
> | Microsoft.DataMigration/services/serviceTasks/delete | リソースおよびそのすべての子を削除します。 |
> | Microsoft.DataMigration/services/serviceTasks/cancel/action | タスクが現在実行されている場合、そのタスクを取り消します。 |
> | Microsoft.DataMigration/services/slots/read | リソースに関する情報を読み取ります。 |
> | Microsoft.DataMigration/services/slots/write | リソースとそのプロパティを作成または更新します。 |
> | Microsoft.DataMigration/services/slots/delete | リソースおよびそのすべての子を削除します。 |
> | Microsoft.DataMigration/skus/read | DMS リソースでサポートされている SKU の一覧を取得します。 |

### <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

Azure サービス:[Azure Database for MariaDB](../mariadb/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.DBforMariaDB/privateEndpointConnectionsApproval/action | ユーザーがプライベート エンドポイント接続の承認を許可されているかどうかを判断します。 |
> | Microsoft.DBforMariaDB/register/action | MariaDB リソース プロバイダーを登録します |
> | Microsoft.DBforMariaDB/checkNameAvailability/action | 指定されたサブスクリプションの世界的なプロビジョニングに対して特定のサーバー名が使用可能かどうかを確認します。 |
> | Microsoft.DBforMariaDB/locations/administratorAzureAsyncOperation/read | MariaDB サーバー管理者の進行中の操作を取得します |
> | Microsoft.DBforMariaDB/locations/administratorOperationResults/read | MariaDB サーバー管理者の操作の結果を返します |
> | Microsoft.DBforMariaDB/locations/azureAsyncOperation/read | MariaDB サーバーの操作の結果を返します |
> | Microsoft.DBforMariaDB/locations/operationResults/read | ResourceGroup ベースの MariaDB サーバーの操作の結果を返します |
> | Microsoft.DBforMariaDB/locations/operationResults/read | MariaDB サーバーの操作の結果を返します |
> | Microsoft.DBforMariaDB/locations/performanceTiers/read | 使用可能なパフォーマンス レベルの一覧を返します。 |
> | Microsoft.DBforMariaDB/locations/privateEndpointConnectionAzureAsyncOperation/read | プライベート エンドポイント接続操作の結果を取得します |
> | Microsoft.DBforMariaDB/locations/privateEndpointConnectionOperationResults/read | プライベート エンドポイント接続操作の結果を取得します |
> | Microsoft.DBforMariaDB/locations/privateEndpointConnectionProxyAzureAsyncOperation/read | プライベート エンドポイント接続プロキシの操作の結果を取得します |
> | Microsoft.DBforMariaDB/locations/privateEndpointConnectionProxyOperationResults/read | プライベート エンドポイント接続プロキシの操作の結果を取得します |
> | Microsoft.DBforMariaDB/locations/securityAlertPoliciesAzureAsyncOperation/read | サーバーの脅威検出操作の結果の一覧を返します。 |
> | Microsoft.DBforMariaDB/locations/securityAlertPoliciesOperationResults/read | サーバーの脅威検出操作の結果の一覧を返します。 |
> | Microsoft.DBforMariaDB/locations/serverKeyAzureAsyncOperation/read | Data Encryption サーバー キーでの進行中の操作を取得します |
> | Microsoft.DBforMariaDB/locations/serverKeyOperationResults/read | Transparent Data Encryption サーバー キーでの進行中の操作を取得します |
> | Microsoft.DBforMariaDB/operations/read | MariaDB 操作の一覧を返します。 |
> | Microsoft.DBforMariaDB/performanceTiers/read | 使用可能なパフォーマンス レベルの一覧を返します。 |
> | Microsoft.DBforMariaDB/servers/start/action | 特定のサーバーを起動します。 |
> | Microsoft.DBforMariaDB/servers/stop/action | 特定のサーバーを停止します。 |
> | Microsoft.DBforMariaDB/servers/resetQueryPerformanceInsightData/action | Query Performance Insight のデータをリセットします |
> | Microsoft.DBforMariaDB/servers/queryTexts/action | クエリの一覧についてテキストを返します。 |
> | Microsoft.DBforMariaDB/servers/queryTexts/action | クエリのテキストを返します。 |
> | Microsoft.DBforMariaDB/servers/privateEndpointConnectionsApproval/action | ユーザーがプライベート エンドポイント接続の承認を許可されているかどうかを判断します。 |
> | Microsoft.DBforMariaDB/servers/read | サーバーの一覧を返すか、指定されたサーバーのプロパティを取得します。 |
> | Microsoft.DBforMariaDB/servers/write | 指定されたパラメーターでサーバーを作成するか、指定されたサーバーのプロパティまたはタグを更新します。 |
> | Microsoft.DBforMariaDB/servers/delete | 既存のサーバーを削除します。 |
> | Microsoft.DBforMariaDB/servers/restart/action | 特定のサーバーを再起動します。 |
> | Microsoft.DBforMariaDB/servers/updateConfigurations/action | 指定されたサーバーの構成を更新します。 |
> | Microsoft.DBforMariaDB/servers/administrators/read | MariaDB サーバーの管理者の一覧を取得します。 |
> | Microsoft.DBforMariaDB/servers/administrators/write | 指定されたパラメーターで MariaDB サーバーの管理者を作成または更新します。 |
> | Microsoft.DBforMariaDB/servers/administrators/delete | MariaDB サーバーの既存の管理者を削除します。 |
> | Microsoft.DBforMariaDB/servers/advisors/read | アドバイザーの一覧を返します |
> | Microsoft.DBforMariaDB/servers/advisors/read | アドバイザーを返します |
> | Microsoft.DBforMariaDB/servers/advisors/createRecommendedActionSession/action | 新しい推奨されるアクションのセクションを作成します |
> | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | 推奨されるアクションの一覧を返します |
> | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | 推奨されるアクションの一覧を返します |
> | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | 推奨されるアクションを返します |
> | Microsoft.DBforMariaDB/servers/configurations/read | サーバーの構成の一覧を返すか、指定された構成のプロパティを取得します。 |
> | Microsoft.DBforMariaDB/servers/configurations/write | 指定された構成の値を更新します。 |
> | Microsoft.DBforMariaDB/servers/databases/read | MariaDB データベースの一覧を返すか、指定されたデータベースのプロパティを取得します。 |
> | Microsoft.DBforMariaDB/servers/databases/write | 指定されたパラメーターで MariaDB データベースを作成するか、指定されたデータベースのプロパティを更新します。 |
> | Microsoft.DBforMariaDB/servers/databases/delete | 既存の MariaDB データベースを削除します。 |
> | Microsoft.DBforMariaDB/servers/firewallRules/read | サーバーのファイアウォール規則の一覧を返すか、指定されたファイアウォール規則のプロパティを取得します。 |
> | Microsoft.DBforMariaDB/servers/firewallRules/write | 指定されたパラメーターでファイアウォール規則を作成するか、既存の規則を更新します。 |
> | Microsoft.DBforMariaDB/servers/firewallRules/delete | 既存のファイアウォール規則を削除します。 |
> | Microsoft.DBforMariaDB/servers/keys/read | サーバー キーの一覧を返すか、指定されたサーバー キーのプロパティを取得します。 |
> | Microsoft.DBforMariaDB/servers/keys/write | 指定されたパラメーターでキーを作成するか、指定されたサーバー キーのプロパティまたはタグを更新します。 |
> | Microsoft.DBforMariaDB/servers/keys/delete | 既存のサーバー キーを削除します。 |
> | Microsoft.DBforMariaDB/servers/logFiles/read | MariaDB ログ ファイルの一覧を返します。 |
> | Microsoft.DBforMariaDB/servers/performanceTiers/read | 使用可能なパフォーマンス レベルの一覧を返します。 |
> | Microsoft.DBforMariaDB/servers/privateEndpointConnectionProxies/validate/action | NRP 側からのプライベート エンドポイント接続作成呼び出しを検証します |
> | Microsoft.DBforMariaDB/servers/privateEndpointConnectionProxies/read | プライベート エンドポイント接続プロキシの一覧を返すか、指定されたプライベート エンドポイント接続プロキシのプロパティを取得します。 |
> | Microsoft.DBforMariaDB/servers/privateEndpointConnectionProxies/write | 指定されたパラメーターでプライベート エンドポイント接続プロキシを作成するか、指定されたプライベート エンドポイント接続プロキシのプロパティまたはタグを更新します。 |
> | Microsoft.DBforMariaDB/servers/privateEndpointConnectionProxies/delete | 既存のプライベート エンドポイント接続プロキシを削除します |
> | Microsoft.DBforMariaDB/servers/privateEndpointConnections/read | プライベート エンドポイント接続の一覧を返すか、指定されたプライベート エンドポイント接続のプロパティを取得します。 |
> | Microsoft.DBforMariaDB/servers/privateEndpointConnections/delete | 既存のプライベート エンドポイント接続を削除します |
> | Microsoft.DBforMariaDB/servers/privateEndpointConnections/write | 既存のプライベート エンドポイント接続を承認または拒否します |
> | Microsoft.DBforMariaDB/servers/privateLinkResources/read | 対応する MariaDB サーバーのプライベート リンクのリソースを取得します。 |
> | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/logDefinitions/read | MariaDB サーバーの利用可能なログを取得します |
> | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/metricDefinitions/read | データベースの利用可能なメトリックの種類を返します。 |
> | Microsoft.DBforMariaDB/servers/recoverableServers/read | 復旧可能な MariaDB サーバーの情報を返します |
> | Microsoft.DBforMariaDB/servers/replicas/read | MariaDB サーバーの読み取りレプリカを取得します |
> | Microsoft.DBforMariaDB/servers/securityAlertPolicies/read | 指定されたサーバーで構成されているサーバーの脅威検出ポリシーの詳細を取得します。 |
> | Microsoft.DBforMariaDB/servers/securityAlertPolicies/write | 指定されたサーバーの脅威検出ポリシーを変更します。 |
> | Microsoft.DBforMariaDB/servers/securityAlertPolicies/read | 指定したサーバーに対して構成されているサーバー脅威検出ポリシーの一覧を取得します |
> | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | 上位のクエリのクエリ統計の一覧を返します。 |
> | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | クエリ統計情報を返します |
> | Microsoft.DBforMariaDB/servers/virtualNetworkRules/read | 仮想ネットワーク ルールの一覧を返すか、指定された仮想ネットワーク ルールのプロパティを取得します。 |
> | Microsoft.DBforMariaDB/servers/virtualNetworkRules/write | 指定されたパラメーターで仮想ネットワーク規則を作成するか、指定された仮想ネットワーク規則のプロパティまたはタグを更新します。 |
> | Microsoft.DBforMariaDB/servers/virtualNetworkRules/delete | 既存の仮想ネットワーク ルールを削除します。 |
> | Microsoft.DBforMariaDB/servers/waitStatistics/read | インスタンスの待機統計情報を返します |
> | Microsoft.DBforMariaDB/servers/waitStatistics/read | 待機統計情報を返します |

### <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

Azure サービス:[Azure Database for MySQL](../mysql/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.DBforMySQL/privateEndpointConnectionsApproval/action | ユーザーがプライベート エンドポイント接続の承認を許可されているかどうかを判断します。 |
> | Microsoft.DBforMySQL/register/action | MySQL リソース プロバイダーを登録します |
> | Microsoft.DBforMySQL/checkNameAvailability/action | 指定されたサブスクリプションの世界的なプロビジョニングに対して特定のサーバー名が使用可能かどうかを確認します。 |
> | Microsoft.DBforMySQL/flexibleServers/read | サーバーの一覧を返すか、指定されたサーバーのプロパティを取得します。 |
> | Microsoft.DBforMySQL/flexibleServers/write | 指定されたパラメーターでサーバーを作成するか、指定されたサーバーのプロパティまたはタグを更新します。 |
> | Microsoft.DBforMySQL/flexibleServers/delete | 既存のサーバーを削除します。 |
> | Microsoft.DBforMySQL/flexibleServers/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Microsoft.DBforMySQL/flexibleServers/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Microsoft.DBforMySQL/flexibleServers/providers/Microsoft.Insights/logDefinitions/read | MySQL サーバーの利用可能なログを取得します |
> | Microsoft.DBforMySQL/flexibleServers/providers/Microsoft.Insights/metricDefinitions/read | データベースの利用可能なメトリックの種類を返します。 |
> | Microsoft.DBforMySQL/locations/administratorAzureAsyncOperation/read | MySQL サーバー管理者の進行中の操作を取得します |
> | Microsoft.DBforMySQL/locations/administratorOperationResults/read | MySQL サーバー管理者の操作の結果を返します |
> | Microsoft.DBforMySQL/locations/azureAsyncOperation/read | MySQL サーバーの操作の結果を返します |
> | Microsoft.DBforMySQL/locations/operationResults/read | ResourceGroup ベースの MySQL サーバーの操作の結果を返します |
> | Microsoft.DBforMySQL/locations/operationResults/read | MySQL サーバーの操作の結果を返します |
> | Microsoft.DBforMySQL/locations/performanceTiers/read | 使用可能なパフォーマンス レベルの一覧を返します。 |
> | Microsoft.DBforMySQL/locations/privateEndpointConnectionAzureAsyncOperation/read | プライベート エンドポイント接続操作の結果を取得します |
> | Microsoft.DBforMySQL/locations/privateEndpointConnectionOperationResults/read | プライベート エンドポイント接続操作の結果を取得します |
> | Microsoft.DBforMySQL/locations/privateEndpointConnectionProxyAzureAsyncOperation/read | プライベート エンドポイント接続プロキシの操作の結果を取得します |
> | Microsoft.DBforMySQL/locations/privateEndpointConnectionProxyOperationResults/read | プライベート エンドポイント接続プロキシの操作の結果を取得します |
> | Microsoft.DBforMySQL/locations/securityAlertPoliciesAzureAsyncOperation/read | サーバーの脅威検出操作の結果の一覧を返します。 |
> | Microsoft.DBforMySQL/locations/securityAlertPoliciesOperationResults/read | サーバーの脅威検出操作の結果の一覧を返します。 |
> | Microsoft.DBforMySQL/locations/serverKeyAzureAsyncOperation/read | Transparent Data Encryption サーバー キーでの進行中の操作を取得します |
> | Microsoft.DBforMySQL/locations/serverKeyOperationResults/read | Data Encryption サーバー キーでの進行中の操作を取得します |
> | Microsoft.DBforMySQL/operations/read | MySQL 操作の一覧を返します。 |
> | Microsoft.DBforMySQL/performanceTiers/read | 使用可能なパフォーマンス レベルの一覧を返します。 |
> | Microsoft.DBforMySQL/servers/upgrade/action |  |
> | Microsoft.DBforMySQL/servers/start/action | 特定のサーバーを起動します。 |
> | Microsoft.DBforMySQL/servers/stop/action | 特定のサーバーを停止します。 |
> | Microsoft.DBforMySQL/servers/privateEndpointConnectionsApproval/action | ユーザーがプライベート エンドポイント接続の承認を許可されているかどうかを判断します。 |
> | Microsoft.DBforMySQL/servers/resetQueryPerformanceInsightData/action | Query Performance Insight のデータをリセットします |
> | Microsoft.DBforMySQL/servers/queryTexts/action | クエリの一覧についてテキストを返します。 |
> | Microsoft.DBforMySQL/servers/queryTexts/action | クエリのテキストを返します。 |
> | Microsoft.DBforMySQL/servers/read | サーバーの一覧を返すか、指定されたサーバーのプロパティを取得します。 |
> | Microsoft.DBforMySQL/servers/write | 指定されたパラメーターでサーバーを作成するか、指定されたサーバーのプロパティまたはタグを更新します。 |
> | Microsoft.DBforMySQL/servers/delete | 既存のサーバーを削除します。 |
> | Microsoft.DBforMySQL/servers/restart/action | 特定のサーバーを再起動します。 |
> | Microsoft.DBforMySQL/servers/updateConfigurations/action | 指定されたサーバーの構成を更新します。 |
> | Microsoft.DBforMySQL/servers/administrators/read | MySQL サーバーの管理者の一覧を取得します。 |
> | Microsoft.DBforMySQL/servers/administrators/write | 指定されたパラメーターで MySQL サーバーの管理者を作成または更新します。 |
> | Microsoft.DBforMySQL/servers/administrators/delete | MySQL サーバーの既存の管理者を削除します。 |
> | Microsoft.DBforMySQL/servers/advisors/read | アドバイザーの一覧を返します |
> | Microsoft.DBforMySQL/servers/advisors/read | アドバイザーを返します |
> | Microsoft.DBforMySQL/servers/advisors/createRecommendedActionSession/action | 新しい推奨されるアクションのセクションを作成します |
> | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | 推奨されるアクションの一覧を返します |
> | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | 推奨されるアクションの一覧を返します |
> | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | 推奨されるアクションを返します |
> | Microsoft.DBforMySQL/servers/configurations/read | サーバーの構成の一覧を返すか、指定された構成のプロパティを取得します。 |
> | Microsoft.DBforMySQL/servers/configurations/write | 指定された構成の値を更新します。 |
> | Microsoft.DBforMySQL/servers/databases/read | MySQL データベースの一覧を返すか、指定されたデータベースのプロパティを取得します。 |
> | Microsoft.DBforMySQL/servers/databases/write | 指定されたパラメーターで MySQL データベースを作成するか、指定されたデータベースのプロパティを更新します。 |
> | Microsoft.DBforMySQL/servers/databases/delete | 既存の MySQL データベースを削除します。 |
> | Microsoft.DBforMySQL/servers/exports/write |  |
> | Microsoft.DBforMySQL/servers/exports/read |  |
> | Microsoft.DBforMySQL/servers/exports/read |  |
> | Microsoft.DBforMySQL/servers/firewallRules/read | サーバーのファイアウォール規則の一覧を返すか、指定されたファイアウォール規則のプロパティを取得します。 |
> | Microsoft.DBforMySQL/servers/firewallRules/write | 指定されたパラメーターでファイアウォール規則を作成するか、既存の規則を更新します。 |
> | Microsoft.DBforMySQL/servers/firewallRules/delete | 既存のファイアウォール規則を削除します。 |
> | Microsoft.DBforMySQL/servers/keys/read | サーバー キーの一覧を返すか、指定されたサーバー キーのプロパティを取得します。 |
> | Microsoft.DBforMySQL/servers/keys/write | 指定されたパラメーターでキーを作成するか、指定されたサーバー キーのプロパティまたはタグを更新します。 |
> | Microsoft.DBforMySQL/servers/keys/delete | 既存のサーバー キーを削除します。 |
> | Microsoft.DBforMySQL/servers/logFiles/read | MySQL LogFiles の一覧を返します。 |
> | Microsoft.DBforMySQL/servers/performanceTiers/read | 使用可能なパフォーマンス レベルの一覧を返します。 |
> | Microsoft.DBforMySQL/servers/privateEndpointConnectionProxies/validate/action | NRP 側からのプライベート エンドポイント接続作成呼び出しを検証します |
> | Microsoft.DBforMySQL/servers/privateEndpointConnectionProxies/read | プライベート エンドポイント接続プロキシの一覧を返すか、指定されたプライベート エンドポイント接続プロキシのプロパティを取得します。 |
> | Microsoft.DBforMySQL/servers/privateEndpointConnectionProxies/write | 指定されたパラメーターでプライベート エンドポイント接続プロキシを作成するか、指定されたプライベート エンドポイント接続プロキシのプロパティまたはタグを更新します。 |
> | Microsoft.DBforMySQL/servers/privateEndpointConnectionProxies/delete | 既存のプライベート エンドポイント接続プロキシを削除します |
> | Microsoft.DBforMySQL/servers/privateEndpointConnections/read | プライベート エンドポイント接続の一覧を返すか、指定されたプライベート エンドポイント接続のプロパティを取得します。 |
> | Microsoft.DBforMySQL/servers/privateEndpointConnections/delete | 既存のプライベート エンドポイント接続を削除します |
> | Microsoft.DBforMySQL/servers/privateEndpointConnections/write | 既存のプライベート エンドポイント接続を承認または拒否します |
> | Microsoft.DBforMySQL/servers/privateLinkResources/read | 対応する MySQL サーバーのプライベート リンクのリソースを取得します。 |
> | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/logDefinitions/read | MySQL サーバーの利用可能なログを取得します |
> | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | データベースの利用可能なメトリックの種類を返します。 |
> | Microsoft.DBforMySQL/servers/recoverableServers/read | 復旧可能な MySQL サーバーの情報を返します |
> | Microsoft.DBforMySQL/servers/replicas/read | MySQL サーバーの読み取りレプリカを取得します |
> | Microsoft.DBforMySQL/servers/securityAlertPolicies/read | 指定されたサーバーで構成されているサーバーの脅威検出ポリシーの詳細を取得します。 |
> | Microsoft.DBforMySQL/servers/securityAlertPolicies/write | 指定されたサーバーの脅威検出ポリシーを変更します。 |
> | Microsoft.DBforMySQL/servers/securityAlertPolicies/read | 指定したサーバーに対して構成されているサーバー脅威検出ポリシーの一覧を取得します |
> | Microsoft.DBforMySQL/servers/topQueryStatistics/read | 上位のクエリのクエリ統計の一覧を返します。 |
> | Microsoft.DBforMySQL/servers/topQueryStatistics/read | クエリ統計情報を返します |
> | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | 仮想ネットワーク ルールの一覧を返すか、指定された仮想ネットワーク ルールのプロパティを取得します。 |
> | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | 指定されたパラメーターで仮想ネットワーク規則を作成するか、指定された仮想ネットワーク規則のプロパティまたはタグを更新します。 |
> | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | 既存の仮想ネットワーク ルールを削除します。 |
> | Microsoft.DBforMySQL/servers/waitStatistics/read | インスタンスの待機統計情報を返します |
> | Microsoft.DBforMySQL/servers/waitStatistics/read | 待機統計情報を返します |

### <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

Azure サービス:[Azure Database for PostgreSQL](../postgresql/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.DBforPostgreSQL/privateEndpointConnectionsApproval/action | ユーザーがプライベート エンドポイント接続の承認を許可されているかどうかを判断します。 |
> | Microsoft.DBforPostgreSQL/register/action | PostgreSQL リソース プロバイダーを登録します |
> | Microsoft.DBforPostgreSQL/checkNameAvailability/action | 指定されたサブスクリプションの世界的なプロビジョニングに対して特定のサーバー名が使用可能かどうかを確認します。 |
> | Microsoft.DBforPostgreSQL/flexibleServers/read | サーバーの一覧を返すか、指定されたサーバーのプロパティを取得します。 |
> | Microsoft.DBforPostgreSQL/flexibleServers/write | 指定されたパラメーターでサーバーを作成するか、指定されたサーバーのプロパティまたはタグを更新します。 |
> | Microsoft.DBforPostgreSQL/flexibleServers/delete | 既存のサーバーを削除します。 |
> | Microsoft.DBforPostgreSQL/flexibleServers/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Microsoft.DBforPostgreSQL/flexibleServers/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Microsoft.DBforPostgreSQL/flexibleServers/providers/Microsoft.Insights/logDefinitions/read | PostgreSQL サーバーの使用可能なログを取得します |
> | Microsoft.DBforPostgreSQL/flexibleServers/providers/Microsoft.Insights/metricDefinitions/read | データベースの利用可能なメトリックの種類を返します。 |
> | Microsoft.DBforPostgreSQL/locations/administratorAzureAsyncOperation/read | PostgreSQL サーバー管理者の進行中の操作を取得します |
> | Microsoft.DBforPostgreSQL/locations/administratorOperationResults/read | PostgreSQL サーバー管理者の操作の結果を返します |
> | Microsoft.DBforPostgreSQL/locations/azureAsyncOperation/read | PostgreSQL サーバーの操作の結果を返します |
> | Microsoft.DBforPostgreSQL/locations/operationResults/read | ResourceGroup ベースの PostgreSQL サーバーの操作の結果を返します |
> | Microsoft.DBforPostgreSQL/locations/operationResults/read | PostgreSQL サーバーの操作の結果を返します |
> | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | 使用可能なパフォーマンス レベルの一覧を返します。 |
> | Microsoft.DBforPostgreSQL/locations/privateEndpointConnectionAzureAsyncOperation/read | プライベート エンドポイント接続操作の結果を取得します |
> | Microsoft.DBforPostgreSQL/locations/privateEndpointConnectionOperationResults/read | プライベート エンドポイント接続操作の結果を取得します |
> | Microsoft.DBforPostgreSQL/locations/privateEndpointConnectionProxyAzureAsyncOperation/read | プライベート エンドポイント接続プロキシの操作の結果を取得します |
> | Microsoft.DBforPostgreSQL/locations/privateEndpointConnectionProxyOperationResults/read | プライベート エンドポイント接続プロキシの操作の結果を取得します |
> | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesAzureAsyncOperation/read | サーバーの脅威検出操作の結果の一覧を返します。 |
> | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesOperationResults/read | サーバーの脅威検出操作の結果の一覧を返します。 |
> | Microsoft.DBforPostgreSQL/locations/serverKeyAzureAsyncOperation/read | Data Encryption サーバー キーでの進行中の操作を取得します |
> | Microsoft.DBforPostgreSQL/locations/serverKeyOperationResults/read | Data Encryption サーバー キーでの進行中の操作を取得します |
> | Microsoft.DBforPostgreSQL/operations/read | PostgreSQL の操作の一覧を返します。 |
> | Microsoft.DBforPostgreSQL/performanceTiers/read | 使用可能なパフォーマンス レベルの一覧を返します。 |
> | Microsoft.DBforPostgreSQL/servers/queryTexts/action | クエリのテキストを返します。 |
> | Microsoft.DBforPostgreSQL/servers/resetQueryPerformanceInsightData/action | Query Performance Insight のデータをリセットします |
> | Microsoft.DBforPostgreSQL/servers/privateEndpointConnectionsApproval/action | ユーザーがプライベート エンドポイント接続の承認を許可されているかどうかを判断します。 |
> | Microsoft.DBforPostgreSQL/servers/read | サーバーの一覧を返すか、指定されたサーバーのプロパティを取得します。 |
> | Microsoft.DBforPostgreSQL/servers/write | 指定されたパラメーターでサーバーを作成するか、指定されたサーバーのプロパティまたはタグを更新します。 |
> | Microsoft.DBforPostgreSQL/servers/delete | 既存のサーバーを削除します。 |
> | Microsoft.DBforPostgreSQL/servers/restart/action | 特定のサーバーを再起動します。 |
> | Microsoft.DBforPostgreSQL/servers/updateConfigurations/action | 指定されたサーバーの構成を更新します。 |
> | Microsoft.DBforPostgreSQL/servers/administrators/read | PostgreSQL サーバーの管理者の一覧を取得します。 |
> | Microsoft.DBforPostgreSQL/servers/administrators/write | 指定されたパラメーターで PostgreSQL サーバーの管理者を作成または更新します。 |
> | Microsoft.DBforPostgreSQL/servers/administrators/delete | PostgreSQL サーバーの既存の管理者を削除します。 |
> | Microsoft.DBforPostgreSQL/servers/advisors/read | アドバイザーの一覧を返します |
> | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActionSessions/action | 推奨を行います |
> | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActions/read | 推奨されるアクションの一覧を返します |
> | Microsoft.DBforPostgreSQL/servers/configurations/read | サーバーの構成の一覧を返すか、指定された構成のプロパティを取得します。 |
> | Microsoft.DBforPostgreSQL/servers/configurations/write | 指定された構成の値を更新します。 |
> | Microsoft.DBforPostgreSQL/servers/databases/read | PostgreSQL データベースの一覧を返すか、指定されたデータベースのプロパティを取得します。 |
> | Microsoft.DBforPostgreSQL/servers/databases/write | 指定されたパラメーターで PostgreSQL データベースを作成するか、指定されたデータベースのプロパティを更新します。 |
> | Microsoft.DBforPostgreSQL/servers/databases/delete | 既存の PostgreSQL データベースを削除します。 |
> | Microsoft.DBforPostgreSQL/servers/firewallRules/read | サーバーのファイアウォール規則の一覧を返すか、指定されたファイアウォール規則のプロパティを取得します。 |
> | Microsoft.DBforPostgreSQL/servers/firewallRules/write | 指定されたパラメーターでファイアウォール規則を作成するか、既存の規則を更新します。 |
> | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | 既存のファイアウォール規則を削除します。 |
> | Microsoft.DBforPostgreSQL/servers/keys/read | サーバー キーの一覧を返すか、指定されたサーバー キーのプロパティを取得します。 |
> | Microsoft.DBforPostgreSQL/servers/keys/write | 指定されたパラメーターでキーを作成するか、指定されたサーバー キーのプロパティまたはタグを更新します。 |
> | Microsoft.DBforPostgreSQL/servers/keys/delete | 既存のサーバー キーを削除します。 |
> | Microsoft.DBforPostgreSQL/servers/logFiles/read | PostgreSQL ログ ファイルの一覧を返します。 |
> | Microsoft.DBforPostgreSQL/servers/performanceTiers/read | 使用可能なパフォーマンス レベルの一覧を返します。 |
> | Microsoft.DBforPostgreSQL/servers/privateEndpointConnectionProxies/validate/action | NRP 側からのプライベート エンドポイント接続作成呼び出しを検証します |
> | Microsoft.DBforPostgreSQL/servers/privateEndpointConnectionProxies/read | プライベート エンドポイント接続プロキシの一覧を返すか、指定されたプライベート エンドポイント接続プロキシのプロパティを取得します。 |
> | Microsoft.DBforPostgreSQL/servers/privateEndpointConnectionProxies/write | 指定されたパラメーターでプライベート エンドポイント接続プロキシを作成するか、指定されたプライベート エンドポイント接続プロキシのプロパティまたはタグを更新します。 |
> | Microsoft.DBforPostgreSQL/servers/privateEndpointConnectionProxies/delete | 既存のプライベート エンドポイント接続プロキシを削除します |
> | Microsoft.DBforPostgreSQL/servers/privateEndpointConnections/read | プライベート エンドポイント接続の一覧を返すか、指定されたプライベート エンドポイント接続のプロパティを取得します。 |
> | Microsoft.DBforPostgreSQL/servers/privateEndpointConnections/delete | 既存のプライベート エンドポイント接続を削除します |
> | Microsoft.DBforPostgreSQL/servers/privateEndpointConnections/write | 既存のプライベート エンドポイント接続を承認または拒否します |
> | Microsoft.DBforPostgreSQL/servers/privateLinkResources/read | 対応する PostgreSQL サーバーのプライベート リンクのリソースを取得します |
> | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | PostgreSQL サーバーの使用可能なログを取得します |
> | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | データベースの利用可能なメトリックの種類を返します。 |
> | Microsoft.DBforPostgreSQL/servers/queryTexts/read | クエリの一覧についてテキストを返します。 |
> | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | 復旧可能な PostgreSQL サーバーの情報を返します |
> | Microsoft.DBforPostgreSQL/servers/replicas/read | PostgreSQL サーバーの読み取りレプリカを取得します |
> | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | 指定されたサーバーで構成されているサーバーの脅威検出ポリシーの詳細を取得します。 |
> | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | 指定されたサーバーの脅威検出ポリシーを変更します。 |
> | Microsoft.DBforPostgreSQL/servers/topQueryStatistics/read | 上位のクエリのクエリ統計の一覧を返します。 |
> | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | 仮想ネットワーク ルールの一覧を返すか、指定された仮想ネットワーク ルールのプロパティを取得します。 |
> | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | 指定されたパラメーターで仮想ネットワーク規則を作成するか、指定された仮想ネットワーク規則のプロパティまたはタグを更新します。 |
> | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | 既存の仮想ネットワーク ルールを削除します。 |
> | Microsoft.DBforPostgreSQL/servers/waitStatistics/read | インスタンスの待機統計情報を返します |
> | Microsoft.DBforPostgreSQL/serversv2/read | サーバーの一覧を返すか、指定されたサーバーのプロパティを取得します。 |
> | Microsoft.DBforPostgreSQL/serversv2/write | 指定されたパラメーターでサーバーを作成するか、指定されたサーバーのプロパティまたはタグを更新します。 |
> | Microsoft.DBforPostgreSQL/serversv2/delete | 既存のサーバーを削除します。 |
> | Microsoft.DBforPostgreSQL/serversv2/updateConfigurations/action | 指定されたサーバーの構成を更新します。 |
> | Microsoft.DBforPostgreSQL/serversv2/configurations/read | サーバーの構成の一覧を返すか、指定された構成のプロパティを取得します。 |
> | Microsoft.DBforPostgreSQL/serversv2/configurations/write | 指定された構成の値を更新します。 |
> | Microsoft.DBforPostgreSQL/serversv2/firewallRules/read | サーバーのファイアウォール規則の一覧を返すか、指定されたファイアウォール規則のプロパティを取得します。 |
> | Microsoft.DBforPostgreSQL/serversv2/firewallRules/write | 指定されたパラメーターでファイアウォール規則を作成するか、既存の規則を更新します。 |
> | Microsoft.DBforPostgreSQL/serversv2/firewallRules/delete | 既存のファイアウォール規則を削除します。 |
> | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/logDefinitions/read | PostgreSQL サーバーの使用可能なログを取得します |
> | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/metricDefinitions/read | データベースの利用可能なメトリックの種類を返します。 |
> | Microsoft.DBforPostgreSQL/singleservers/read | サーバーの一覧を返すか、指定されたサーバーのプロパティを取得します。 |
> | Microsoft.DBforPostgreSQL/singleservers/write | 指定されたパラメーターでサーバーを作成するか、指定されたサーバーのプロパティまたはタグを更新します。 |
> | Microsoft.DBforPostgreSQL/singleservers/delete | 既存のサーバーを削除します。 |
> | Microsoft.DBforPostgreSQL/singleservers/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Microsoft.DBforPostgreSQL/singleservers/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Microsoft.DBforPostgreSQL/singleservers/providers/Microsoft.Insights/logDefinitions/read | PostgreSQL サーバーの使用可能なログを取得します |
> | Microsoft.DBforPostgreSQL/singleservers/providers/Microsoft.Insights/metricDefinitions/read | データベースの利用可能なメトリックの種類を返します。 |

### <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

Azure サービス:[Azure Cosmos DB](../cosmos-db/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.DocumentDB/register/action |  Microsoft DocumentDB リソース プロバイダーにサブスクリプションを登録します。 |
> | Microsoft.DocumentDB/cassandraClusters/read | マネージド Cassandra クラスターを読み取るか、すべてのマネージド Cassandra クラスターを一覧表示します |
> | Microsoft.DocumentDB/cassandraClusters/write | マネージド Cassandra クラスターを作成または更新します |
> | Microsoft.DocumentDB/cassandraClusters/delete | マネージド Cassandra クラスターを削除します |
> | Microsoft.DocumentDB/cassandraClusters/dataCenters/read | マネージド Cassandra クラスターのデータセンターを読み取るか、マネージド Cassandra クラスター内のすべてのデータセンターを一覧表示します |
> | Microsoft.DocumentDB/cassandraClusters/dataCenters/write | マネージド Cassandra クラスターのデータセンターを作成または更新します |
> | Microsoft.DocumentDB/cassandraClusters/dataCenters/delete | マネージド Cassandra クラスターのデータセンターを削除します |
> | Microsoft.DocumentDB/databaseAccountNames/read | 名前を使用できるかどうかを確認します。 |
> | Microsoft.DocumentDB/databaseAccounts/read | データベース アカウントを読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/write | データベース アカウントを更新します。 |
> | Microsoft.DocumentDB/databaseAccounts/listKeys/action | データベース アカウントのキーを一覧表示します。 |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | データベース アカウントの読み取り専用キーを読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | データベース アカウントのキーをローテーションします。 |
> | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | データベース アカウントの接続文字列を取得します。 |
> | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | データベース アカウントのリソース グループを変更します。 |
> | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | データベース アカウントのリージョンのフェールオーバーの優先度を変更します。 これは手動フェールオーバー操作を実行する際に使用します。 |
> | Microsoft.DocumentDB/databaseAccounts/offlineRegion/action | データベース アカウントのリージョンをオフラインにします。  |
> | Microsoft.DocumentDB/databaseAccounts/onlineRegion/action | データベース アカウントのリージョンをオンラインにします。 |
> | Microsoft.DocumentDB/databaseAccounts/delete | データベース アカウントを削除します。 |
> | Microsoft.DocumentDB/databaseAccounts/getBackupPolicy/action | データベース アカウントのバックアップ ポリシーを取得します。 |
> | Microsoft.DocumentDB/databaseAccounts/PrivateEndpointConnectionsApproval/action | データベース アカウントのプライベート エンドポイント接続を管理します |
> | Microsoft.DocumentDB/databaseAccounts/restore/action | 復元要求を送信します |
> | Microsoft.DocumentDB/databaseAccounts/backup/action | バックアップを構成するための要求を送信します |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/write | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) データベースを作成します。 API の種類 "sql"、"mongodb"、"gremlin" にのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/read | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) データベースを読み取るか、すべてのデータベースを一覧表示します。 API の種類 "sql"、"mongodb"、"gremlin" にのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/delete | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) データベースを削除します。 API の種類 "sql"、"mongodb"、"gremlin" にのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/write | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) コレクションを作成または更新します。 API の種類 "mongodb" にのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/read | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) コレクションを読み取るか、すべてのコレクションを一覧表示します。 API の種類 "mongodb" にのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/delete | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) コレクションを削除します。 API の種類 "mongodb" にのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/operationResults/read | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) 非同期操作の状態を読み取ります。 API の種類 "mongodb" にのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/write | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) コレクションのスループットを更新します。 API の種類 "mongodb" にのみ適用可能です。 設定の種類 "スループット" でのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/read | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) コレクションのスループットを読み取ります。 API の種類 "mongodb" にのみ適用可能です。 設定の種類 "スループット" でのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/operationResults/read | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) 非同期操作の状態を読み取ります。 API の種類 "mongodb" にのみ適用可能です。 設定の種類 "スループット" でのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/write | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) コンテナーを作成または更新します。 API の種類 "sql" にのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/read | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) コンテナーを読み取るか、すべてのコンテナーを一覧表示します。 API の種類 "sql" にのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/delete | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) コンテナーを削除します。 API の種類 "sql" にのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/operationResults/read | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) 非同期操作の状態を読み取ります。 API の種類 "sql" にのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/write | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) コンテナーのスループットを更新します。 API の種類 "sql" にのみ適用可能です。 設定の種類 "スループット" でのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/read | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) コンテナーのスループットを読み取ります。 API の種類 "sql" にのみ適用可能です。 設定の種類 "スループット" でのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/operationResults/read | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) 非同期操作の状態を読み取ります。 API の種類 "sql" にのみ適用可能です。 設定の種類 "スループット" でのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/write | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) グラフを作成または更新します。 API の種類 "gremlin" にのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/read | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) グラフを読み取るか、すべてのグラフを一覧表示します。 API の種類 "gremlin" にのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/delete | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) グラフを削除します。 API の種類 "gremlin" にのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/operationResults/read | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) 非同期操作の状態を読み取ります。 API の種類 "gremlin" にのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/write | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) グラフのスループットを更新します。 API の種類 "gremlin" にのみ適用可能です。 設定の種類 "スループット" でのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/read | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) グラフのスループットを読み取ります。 API の種類 "gremlin" にのみ適用可能です。 設定の種類 "スループット" でのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/operationResults/read | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) 非同期操作の状態を読み取ります。 API の種類 "gremlin" にのみ適用可能です。 設定の種類 "スループット" でのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/operationResults/read | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) 非同期操作の状態を読み取ります。 API の種類 "sql"、"mongodb"、"gremlin" にのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/write | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) データベースのスループットを更新します。 API の種類 "sql"、"mongodb"、"gremlin" にのみ適用可能です。 設定の種類 "スループット" でのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/read | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) データベースのスループットを読み取ります。 API の種類 "sql"、"mongodb"、"gremlin" にのみ適用可能です。 設定の種類 "スループット" でのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/operationResults/read | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) 非同期操作の状態を読み取ります。 API の種類 "sql"、"mongodb"、"gremlin" にのみ適用可能です。 設定の種類 "スループット" でのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/write | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) キースペースを作成します。 API の種類 "cassandra" にのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/read | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) キースペースを読み取るか、すべてのキースペースを一覧表示します。 API の種類 "cassandra" にのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/delete | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) キースペースを削除します。 API の種類 "cassandra" にのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/operationResults/read | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) 非同期操作の状態を読み取ります。 API の種類 "cassandra" にのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/write | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) キースペースのスループットを更新します。 API の種類 "cassandra" にのみ適用可能です。 設定の種類 "スループット" でのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/read | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) キースペースのスループットを読み取ります。 API の種類 "cassandra" にのみ適用可能です。 設定の種類 "スループット" でのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/operationResults/read | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) 非同期操作の状態を読み取ります。 API の種類 "cassandra" にのみ適用可能です。 設定の種類 "スループット" でのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/write | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) テーブルを作成または更新します。 API の種類 "cassandra" にのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/read | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) テーブルを読み取るか、すべてのテーブルを一覧表示します。 API の種類 "cassandra" にのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/delete | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) テーブルを削除します。 API の種類 "cassandra" にのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/operationResults/read | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) 非同期操作の状態を読み取ります。 API の種類 "cassandra" にのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/write | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) テーブルのスループットを更新します。 API の種類 "cassandra" にのみ適用可能です。 設定の種類 "スループット" でのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/read | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) テーブルのスループットを読み取ります。 API の種類 "cassandra" にのみ適用可能です。 設定の種類 "スループット" でのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/operationResults/read | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) 非同期操作の状態を読み取ります。 API の種類 "cassandra" にのみ適用可能です。 設定の種類 "スループット" でのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/tables/write | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) テーブルを作成または更新します。 API の種類 "table" にのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/tables/read | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) テーブルを読み取るか、すべてのテーブルを一覧表示します。 API の種類 "table" にのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/tables/delete | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) テーブルを削除します。 API の種類 "table" にのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/tables/operationResults/read | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) 非同期操作の状態を読み取ります。 API の種類 "table" にのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/write | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) テーブルのスループットを更新します。 API の種類 "table" にのみ適用可能です。 設定の種類 "スループット" でのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/read | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) テーブルのスループットを読み取ります。 API の種類 "table" にのみ適用可能です。 設定の種類 "スループット" でのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/operationResults/read | (非推奨になりました。 "/apis/" セグメントなしでリソース パスを使用してください) 非同期操作の状態を読み取ります。 API の種類 "table" にのみ適用可能です。 設定の種類 "スループット" でのみ適用可能です。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/write | Cassandra キースペースを作成します。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/read | Cassandra キースペースを読み取るか、すべての Cassandra キースペースを一覧表示します。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/delete | Cassandra キースペースを削除します。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/write | Cassandra テーブルを作成または更新します。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/read | Cassandra テーブルを読み取るか、すべての Cassandra テーブルを一覧表示します。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/delete | Cassandra テーブルを削除します。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/throughputSettings/write | Cassandra テーブルのスループットを更新します。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/throughputSettings/read | Cassandra テーブルのスループットを読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/throughputSettings/migrateToAutoscale/action | Cassandra テーブル プランを自動スケーリングに移行します。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/throughputSettings/migrateToManualThroughput/action | Cassandra テーブル プランを手動スループットに移行します。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/throughputSettings/migrateToAutoscale/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/throughputSettings/migrateToManualThroughput/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/tables/throughputSettings/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/throughputSettings/write | Cassandra キースペースのスループットを更新します。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/throughputSettings/read | Cassandra キースペースのスループットを読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/throughputSettings/migrateToAutoscale/action | Cassandra キースペース プランを自動スケーリングに移行します。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/throughputSettings/migrateToManualThroughput/action | Cassandra キースペース プランを手動スループットに移行します。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/throughputSettings/migrateToAutoscale/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/throughputSettings/migrateToManualThroughput/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces/throughputSettings/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | コレクションのメトリック定義を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | コレクションのメトリックを読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | データベース アカウントのパーティション キー レベルのメトリックを読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | コレクション内のデータベース アカウント パーティションを読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | データベース アカウントのパーティション レベルのメトリックを読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | データベース アカウントのパーティション レベルの使用状況を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | コレクションの使用状況を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | データベースのメトリック定義を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | データベースのメトリックを読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | データベースの使用状況を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/write | Gremlin データベースを作成します。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/read | Gremlin データベースを読み取るか、すべての Gremlin データベースを一覧表示します。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/delete | Gremlin データベースを削除します。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/graphs/write | Gremlin グラフを作成または更新します。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/graphs/read | Gremlin グラフを読み取るか、すべての Gremlin グラフを一覧表示します。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/graphs/delete | Gremlin グラフを削除します。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/graphs/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/graphs/throughputSettings/write | Gremlin グラフのスループットを更新します。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/graphs/throughputSettings/read | Gremlin グラフのスループットを読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/graphs/throughputSettings/migrateToAutoscale/action | Gremlin グラフ プランを自動スケーリングに移行します。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/graphs/throughputSettings/migrateToManualThroughput/action | Gremlin グラフ プランを手動スループットに移行します。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/graphs/throughputSettings/migrateToAutoscale/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/graphs/throughputSettings/migrateToManualThroughput/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/graphs/throughputSettings/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/throughputSettings/write | Gremlin データベースのスループットを更新します。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/throughputSettings/read | Gremlin データベースのスループットを読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/throughputSettings/migrateToAutoscale/action | Gremlin データベース プランを自動スケーリングに移行します。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/throughputSettings/migrateToManualThroughput/action | Gremlin データベース プランを手動スループットに移行します。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/throughputSettings/migrateToAutoscale/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/throughputSettings/migrateToManualThroughput/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/gremlinDatabases/throughputSettings/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | データベース アカウントのメトリック定義を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/metrics/read | データベース アカウントのメトリックを読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/write | MongoDB データベースを作成します。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/read | MongoDB データベースを読み取るか、すべての MongoDB データベースを一覧表示します。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/delete | MongoDB データベースを削除します。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collections/write | MongoDB コレクションを作成または更新します。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collections/read | MongoDB コレクションを読み取るか、すべての MongoDB コレクションを一覧表示します。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collections/delete | MongoDB コレクションを削除します。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collections/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collections/throughputSettings/write | MongoDB コレクションのスループットを更新します。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collections/throughputSettings/read | MongoDB コレクションのスループットを読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collections/throughputSettings/migrateToAutoscale/action | MongoDB コレクション プランを自動スケーリングに移行します。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collections/throughputSettings/migrateToManualThroughput/action | MongoDB コレクション プランを手動スループットに移行します。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collections/throughputSettings/migrateToAutoscale/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collections/throughputSettings/migrateToManualThroughput/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/collections/throughputSettings/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/throughputSettings/write | MongoDB データベースのスループットを更新します。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/throughputSettings/read | MongoDB データベースのスループットを読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/throughputSettings/migrateToAutoscale/action | MongoDB データベース プランを自動スケーリングに移行します。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/throughputSettings/migrateToManualThroughput/action | MongoDB データベース プランをに手動スループットに移行します。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/throughputSettings/migrateToAutoscale/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/throughputSettings/migrateToManualThroughput/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/mongodbDatabases/throughputSettings/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/notebookWorkspaces/write | ノートブック ワークスペースが作成または更新されます |
> | Microsoft.DocumentDB/databaseAccounts/notebookWorkspaces/read | ノートブック ワークスペースが読み取られます |
> | Microsoft.DocumentDB/databaseAccounts/notebookWorkspaces/delete | ノートブック ワークスペースが削除されます |
> | Microsoft.DocumentDB/databaseAccounts/notebookWorkspaces/listConnectionInfo/action | ノートブック ワークスペースの接続情報が一覧表示されます |
> | Microsoft.DocumentDB/databaseAccounts/notebookWorkspaces/operationResults/read | ノートブック ワークスペースに対する非同期操作の状態が読み取られます |
> | Microsoft.DocumentDB/databaseAccounts/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/percentile/read | レプリケーションの待機時間のパーセンタイルを読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | 待機時間メトリックを読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | 特定のソースおよびターゲット リージョンの待機時間メトリックを読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | 特定のターゲット リージョンの待機時間メトリックを読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/read | データベース アカウントのプライベート エンドポイント接続プロキシを読み取ります |
> | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/write | データベース アカウントのプライベート エンドポイント接続プロキシを作成または更新します |
> | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/delete | データベース アカウントのプライベート エンドポイント接続プロキシを削除します |
> | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/validate/action | データベース アカウントのプライベート エンドポイント接続プロキシを検証します |
> | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/operationResults/read | プライベート エンドポイント接続プロキシの非同期操作の状態を読み取ります |
> | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnections/read | プライベート エンドポイント接続を読み取るか、データベース アカウントのすべてのプライベート エンドポイント接続を一覧表示します |
> | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnections/write | データベース アカウントのプライベート エンドポイント接続を作成または更新します |
> | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnections/delete | データベース アカウントのプライベート エンドポイント接続を削除します |
> | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnections/operationResults/read | privateEndpointConnenctions 非同期操作の状態を読み取ります |
> | Microsoft.DocumentDB/databaseAccounts/privateLinkResources/read | プライベート リンク リソースを読み取るか、データベース アカウントのすべてのプライベート リンク リソースを一覧表示します |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | データベース アカウントの読み取り専用キーを読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | リージョンのコレクション メトリックを読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | リージョンのデータベース アカウントのパーティション キー レベルのメトリックを読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/read | コレクション内のリージョンのデータベース アカウント パーティションを読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | リージョンのデータベース アカウントのパーティション レベルのメトリックを読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | リージョンとデータベース アカウントのメトリックを読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/write | SQL データベースを作成します。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/read | SQL データベースを読み取るか、すべての SQL データベースを一覧表示します。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/delete | SQL データベースを削除します。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/write | SQL コンテナーを作成または更新します。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/read | SQL コンテナーを読み取るか、すべての SQL コンテナーを一覧表示します。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/delete | SQL コンテナーを削除します。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/storedProcedures/write | SQL ストアド プロシージャを作成または更新します。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/storedProcedures/read | SQL ストアド プロシージャを読み取るか、すべての SQL ストアド プロシージャを一覧表示します。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/storedProcedures/delete | SQL ストアド プロシージャを削除します。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/storedProcedures/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/throughputSettings/write | SQL コンテナーのスループットを更新します。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/throughputSettings/read | SQL コンテナーのスループットを読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/throughputSettings/migrateToAutoscale/action | SQL コンテナー プランを自動スケーリングに移行します。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/throughputSettings/migrateToManualThroughput/action | SQL データベースのスループット プランを手動スループットに移行します。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/throughputSettings/migrateToAutoscale/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/throughputSettings/migrateToManualThroughput/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/throughputSettings/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/triggers/write | SQL トリガーを作成または更新します。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/triggers/read | SQL トリガーを読み取るか、すべての SQL トリガーを一覧表示します。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/triggers/delete | SQL トリガーを削除します。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/triggers/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/userDefinedFunctions/write | SQL ユーザー定義関数を作成または更新します。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/userDefinedFunctions/read | SQL ユーザー定義関数を読み取るか、すべての SQL ユーザー定義関数を一覧表示します。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/userDefinedFunctions/delete | SQL ユーザー定義関数を削除します。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/userDefinedFunctions/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/write | SQL データベースのスループットを更新します。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/read | SQL データベースのスループットを読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/migrateToAutoscale/action | SQL データベース プランを自動スケーリングに移行します。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/migrateToManualThroughput/action | SQL データベースのスループット プランを手動スループットに移行します。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/migrateToAutoscale/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/migrateToManualThroughput/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/sqlRoleAssignments/read | SQL ロールの割り当てを読み取ります |
> | Microsoft.DocumentDB/databaseAccounts/sqlRoleAssignments/write | SQL ロールの割り当てを作成または更新します |
> | Microsoft.DocumentDB/databaseAccounts/sqlRoleAssignments/delete | SQL ロールの割り当てを削除します |
> | Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions/read | SQL ロールの定義を読み取ります |
> | Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions/write | SQL ロールの定義を作成または更新します |
> | Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions/delete | SQL ロールの定義を削除します |
> | Microsoft.DocumentDB/databaseAccounts/tables/write | テーブルを作成または更新します。 |
> | Microsoft.DocumentDB/databaseAccounts/tables/read | テーブルを読み取るか、すべてのテーブルを一覧表示します。 |
> | Microsoft.DocumentDB/databaseAccounts/tables/delete | テーブルを削除します。 |
> | Microsoft.DocumentDB/databaseAccounts/tables/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/tables/throughputSettings/write | テーブルのスループットを更新します。 |
> | Microsoft.DocumentDB/databaseAccounts/tables/throughputSettings/read | テーブルのスループットを読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/tables/throughputSettings/migrateToAutoscale/action | テーブル プランを自動スケーリングに移行します。 |
> | Microsoft.DocumentDB/databaseAccounts/tables/throughputSettings/migrateToManualThroughput/action | テーブル プランを手動スループットに移行します。 |
> | Microsoft.DocumentDB/databaseAccounts/tables/throughputSettings/migrateToAutoscale/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/tables/throughputSettings/migrateToManualThroughput/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/tables/throughputSettings/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/databaseAccounts/usages/read | データベース アカウントの使用状況を読み取ります。 |
> | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | 仮想ネットワークまたはサブネットが削除されていることを Microsoft.DocumentDB に通知します。 |
> | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | deleteVirtualNetworkOrSubnets 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/locations/operationsStatus/read | 非同期操作の状態を読み取ります |
> | Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read | 復元可能なデータベース アカウントを読み取るか、すべての復元可能なデータベースアカウントを一覧表示します |
> | Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action | 復元要求を送信します |
> | Microsoft.DocumentDB/operationResults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.DocumentDB/operations/read | Microsoft DocumentDB で使用可能な操作を読み取ります。  |

### <a name="microsoftsql"></a>Microsoft.Sql

Azure サービス:[Azure SQL Database](../azure-sql/database/index.yml)、[Azure SQL Managed Instance](../azure-sql/managed-instance/index.yml)、[Azure Synapse Analytics](../synapse-analytics/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Sql/checkNameAvailability/action | 指定されたサブスクリプションの世界的なプロビジョニングに対して特定のサーバー名が使用可能かどうかを確認します。 |
> | Microsoft.Sql/register/action | Microsoft SQL Database リソース プロバイダーにサブスクリプションを登録し、Microsoft SQL Database を作成できるようにします。 |
> | Microsoft.Sql/unregister/action | Microsoft SQL Database リソース プロバイダーのサブスクリプションを登録解除し、Microsoft SQL Database を作成できるようにします。 |
> | Microsoft.Sql/privateEndpointConnectionsApproval/action | ユーザーがプライベート エンドポイント接続の承認を許可されているかどうかを判断します。 |
> | Microsoft.Sql/instancePools/read | インスタンス プールを取得します。 |
> | Microsoft.Sql/instancePools/write | インスタンス プールを作成または更新します。 |
> | Microsoft.Sql/instancePools/delete | インスタンス プールを削除します。 |
> | Microsoft.Sql/instancePools/usages/read | インスタンス プールの使用状況情報を取得します |
> | Microsoft.Sql/locations/read | 指定されたサブスクリプションで使用可能な場所を取得します。 |
> | Microsoft.Sql/locations/administratorAzureAsyncOperation/read | マネージド インスタンスの Azure 非同期管理者操作の結果を取得します。 |
> | Microsoft.Sql/locations/administratorOperationResults/read | マネージド インスタンス管理者操作の結果を取得します。 |
> | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | 拡張サーバー BLOB 監査ポリシー設定操作の結果を取得します。 |
> | Microsoft.Sql/locations/auditingSettingsOperationResults/read | サーバー BLOB 監査ポリシーの設定操作の結果を取得します。 |
> | Microsoft.Sql/locations/capabilities/read | 指定された場所でのこのサブスクリプションの機能を取得します。 |
> | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | データベース操作の状態を取得します。 |
> | Microsoft.Sql/locations/databaseOperationResults/read | データベース操作の状態を取得します。 |
> | Microsoft.Sql/locations/deletedServerAsyncOperation/read | 削除されるサーバーでの進行中の操作を取得します。 |
> | Microsoft.Sql/locations/deletedServerOperationResults/read | 削除されるサーバーでの進行中の操作を取得します。 |
> | Microsoft.Sql/locations/deletedServers/read | 削除されるサーバーの一覧を返すか、指定された削除されるサーバーのプロパティを取得します。 |
> | Microsoft.Sql/locations/deletedServers/recover/action | 削除されたサーバーを復旧します。 |
> | Microsoft.Sql/locations/devOpsAuditingSettingsAzureAsyncOperation/read | サーバーの DevOps 監査ポリシー設定操作の結果を取得します |
> | Microsoft.Sql/locations/devOpsAuditingSettingsOperationResults/read | サーバーの DevOps 監査ポリシー設定操作の結果を取得します |
> | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | エラスティック プールの非同期操作に対する Azure 非同期操作を取得します。 |
> | Microsoft.Sql/locations/elasticPoolOperationResults/read | エラスティック プールの操作の結果を取得します。 |
> | Microsoft.Sql/locations/encryptionProtectorAzureAsyncOperation/read | Transparent Data Encryption での暗号化保護機能で進行中の操作を取得します |
> | Microsoft.Sql/locations/encryptionProtectorOperationResults/read | Transparent Data Encryption での暗号化保護機能で進行中の操作を取得します |
> | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | 拡張サーバー BLOB 監査ポリシー設定操作の結果を取得します。 |
> | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | 拡張サーバー BLOB 監査ポリシー設定操作の結果を取得します。 |
> | Microsoft.Sql/locations/firewallRulesAzureAsyncOperation/read | ファイアウォール規則操作の状態を取得します。 |
> | Microsoft.Sql/locations/firewallRulesOperationResults/read | ファイアウォール規則操作の状態を取得します。 |
> | Microsoft.Sql/locations/hybridLinkAzureAsyncOperation/read | Azure SQL Managed Instance での長期ハイブリッド リンクの非同期操作の状態を取得します。 |
> | Microsoft.Sql/locations/hybridLinkOperationResults/read | 長期ハイブリッド リンクの非同期操作の状態を取得します。 |
> | Microsoft.Sql/locations/instanceFailoverGroups/read | インスタンス フェールオーバー グループの一覧を返すか、指定されたインスタンス フェールオーバー グループのプロパティを取得します。 |
> | Microsoft.Sql/locations/instanceFailoverGroups/write | 指定されたパラメーターでインスタンス フェールオーバー グループを作成するか、指定されたインスタンス フェールオーバー グループのプロパティまたはタグを更新します。 |
> | Microsoft.Sql/locations/instanceFailoverGroups/delete | 既存のインスタンス フェールオーバー グループを削除します。 |
> | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | 既存のインスタンス フェールオーバー グループで計画されたフェールオーバーを実行します。 |
> | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | 既存のインスタンス フェールオーバー グループで強制フェールオーバーを実行します。 |
> | Microsoft.Sql/locations/instancePoolAzureAsyncOperation/read | インスタンス プール操作の状態を取得します |
> | Microsoft.Sql/locations/instancePoolOperationResults/read | インスタンス プール操作の結果を取得します |
> | Microsoft.Sql/locations/jobAgentAzureAsyncOperation/read | ジョブ エージェントの操作の状態を取得します。 |
> | Microsoft.Sql/locations/jobAgentOperationResults/read | ジョブ エージェントの操作の結果を取得します。 |
> | Microsoft.Sql/locations/longTermRetentionBackupAzureAsyncOperation/read | 長期的な保有期間のバックアップ操作の状態を取得します |
> | Microsoft.Sql/locations/longTermRetentionBackupOperationResults/read | 長期的な保有期間のバックアップ操作の状態を取得します |
> | Microsoft.Sql/locations/longTermRetentionBackups/read | 特定の場所にある各サーバー上の各データベースについて、長期保有バックアップを一覧表示します。 |
> | Microsoft.Sql/locations/longTermRetentionManagedInstanceBackupAzureAsyncOperation/read | マネージド インスタンスの長期的な保有期間のバックアップ操作の状態を取得します |
> | Microsoft.Sql/locations/longTermRetentionManagedInstanceBackupOperationResults/read | マネージド インスタンスの長期的な保有期間のバックアップ操作の状態を取得します |
> | Microsoft.Sql/locations/longTermRetentionManagedInstanceBackups/read | 特定の場所に対するマネージド インスタンス LTR バックアップの一覧が返されます  |
> | Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/read | マネージド インスタンス データベースの LTR バックアップの一覧が返されます |
> | Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete | マネージド インスタンス データベースの LTR バックアップが削除されます |
> | Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionManagedInstanceBackups/read | 特定のマネージド インスタンスに対するマネージド インスタンス LTR バックアップの一覧が返されます |
> | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | 特定のサーバー上の各データベースについて、長期保有バックアップを一覧表示します。 |
> | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/copy/action | 長期保有バックアップをコピーします |
> | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/update/action | 長期保有バックアップを更新します |
> | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | 特定のデータベースについて、長期保有バックアップを一覧表示します。 |
> | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | 長期保有バックアップを削除します。 |
> | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | 管理されたデータベースの復元操作を完了します。 |
> | Microsoft.Sql/locations/managedInstanceEncryptionProtectorAzureAsyncOperation/read | Transparent Data Encryption での暗号化マネージド インスタンス保護機能で進行中の操作を取得します |
> | Microsoft.Sql/locations/managedInstanceEncryptionProtectorOperationResults/read | Transparent Data Encryption での暗号化マネージド インスタンス保護機能で進行中の操作を取得します |
> | Microsoft.Sql/locations/managedInstanceKeyAzureAsyncOperation/read | Transparent Data Encryption のマネージド インスタンス キーで進行中の操作を取得します |
> | Microsoft.Sql/locations/managedInstanceKeyOperationResults/read | Transparent Data Encryption のマネージド インスタンス キーで進行中の操作を取得します |
> | Microsoft.Sql/locations/managedInstanceLongTermRetentionPolicyAzureAsyncOperation/read | マネージド データベースの長期保有ポリシー操作の状態を取得します |
> | Microsoft.Sql/locations/managedInstanceLongTermRetentionPolicyOperationResults/read | マネージド データベースの長期保有ポリシー操作の状態を取得します |
> | Microsoft.Sql/locations/managedInstancePrivateEndpointConnectionAzureAsyncOperation/read | プライベート エンドポイント接続操作の結果を取得します |
> | Microsoft.Sql/locations/managedInstancePrivateEndpointConnectionOperationResults/read | プライベート エンドポイント接続操作の結果を取得します |
> | Microsoft.Sql/locations/managedInstancePrivateEndpointConnectionProxyAzureAsyncOperation/read | プライベート エンドポイント接続プロキシの操作の結果を取得します |
> | Microsoft.Sql/locations/managedInstancePrivateEndpointConnectionProxyOperationResults/read | プライベート エンドポイント接続プロキシの操作の結果を取得します |
> | Microsoft.Sql/locations/managedShortTermRetentionPolicyOperationResults/read | 短期保有ポリシー操作の状態を取得します |
> | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | 管理されたデータベースの Transparent Data Encryption での進行中の操作を取得します。 |
> | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | 管理されたデータベースの Transparent Data Encryption での進行中の操作を取得します。 |
> | Microsoft.Sql/locations/operationsHealth/read | 場所にあるサービス操作の正常性状態を取得します |
> | Microsoft.Sql/locations/privateEndpointConnectionAzureAsyncOperation/read | プライベート エンドポイント接続操作の結果を取得します |
> | Microsoft.Sql/locations/privateEndpointConnectionOperationResults/read | プライベート エンドポイント接続操作の結果を取得します |
> | Microsoft.Sql/locations/privateEndpointConnectionProxyAzureAsyncOperation/read | プライベート エンドポイント接続プロキシの操作の結果を取得します |
> | Microsoft.Sql/locations/privateEndpointConnectionProxyOperationResults/read | プライベート エンドポイント接続プロキシの操作の結果を取得します |
> | Microsoft.Sql/locations/serverAdministratorAzureAsyncOperation/read | サーバーの Azure Active Directory 管理者の非同期操作の結果 |
> | Microsoft.Sql/locations/serverAdministratorOperationResults/read | サーバーの Azure Active Directory 管理者の操作の結果 |
> | Microsoft.Sql/locations/serverKeyAzureAsyncOperation/read | Transparent Data Encryption サーバー キーでの進行中の操作を取得します |
> | Microsoft.Sql/locations/serverKeyOperationResults/read | Transparent Data Encryption サーバー キーでの進行中の操作を取得します |
> | Microsoft.Sql/locations/serverTrustGroupAzureAsyncOperation/read | サーバー信頼グループの非同期操作の状態を取得します |
> | Microsoft.Sql/locations/serverTrustGroupOperationResults/read | サーバー信頼グループの操作結果を取得します |
> | Microsoft.Sql/locations/serverTrustGroups/write | 指定されたパラメーターを使用して、サーバー信頼グループを作成します |
> | Microsoft.Sql/locations/serverTrustGroups/delete | 既存の SQL Server 信頼グループを削除します |
> | Microsoft.Sql/locations/serverTrustGroups/read | 既存の SQL Server 信頼グループを返します |
> | Microsoft.Sql/locations/shortTermRetentionPolicyOperationResults/read | 短期保有ポリシー操作の状態を取得します |
> | Microsoft.Sql/locations/syncAgentOperationResults/read | 同期エージェント リソース操作の結果を取得します。 |
> | Microsoft.Sql/locations/syncDatabaseIds/read | 特定のリージョンおよびサブスクリプションの同期データベース ID を取得します。 |
> | Microsoft.Sql/locations/syncGroupOperationResults/read | 同期グループ リソース操作の結果を取得します。 |
> | Microsoft.Sql/locations/syncMemberOperationResults/read | 同期メンバー リソース操作の結果を取得します。 |
> | Microsoft.Sql/locations/timeZones/read | 場所別にマネージド インスタンスのタイム ゾーンの一覧を返します。 |
> | Microsoft.Sql/locations/transparentDataEncryptionAzureAsyncOperation/read | 論理データベースの Transparent Data Encryption での進行中の操作が取得されます |
> | Microsoft.Sql/locations/transparentDataEncryptionOperationResults/read | 論理データベースの Transparent Data Encryption での進行中の操作が取得されます |
> | Microsoft.Sql/locations/usages/read | ある場所でのこのサブスクリプションの使用状況メトリックのコレクションを取得します。 |
> | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | 指定された仮想ネットワーク規則の Azure 非同期操作の詳細を返します。  |
> | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | 指定された仮想ネットワーク規則の操作の詳細を返します。  |
> | Microsoft.Sql/managedInstances/tdeCertificates/action | TDE 証明書を作成/更新します |
> | Microsoft.Sql/managedInstances/read | マネージド インスタンスの一覧を返すか、指定されたマネージド インスタンスのプロパティを取得します。 |
> | Microsoft.Sql/managedInstances/write | 指定されたパラメーターでマネージド インスタンスを作成するか、指定されたマネージド インスタンスのプロパティまたはタグを更新します。 |
> | Microsoft.Sql/managedInstances/delete | 既存のマネージド インスタンスを削除します。 |
> | Microsoft.Sql/managedInstances/joinServerTrustGroup/action | ユーザーがマネージド サーバーをサーバー信頼グループに参加させることが許可されているかどうかを判断します |
> | Microsoft.Sql/managedInstances/failover/action | 顧客が開始したマネージド インスタンスのフェールオーバー。 |
> | Microsoft.Sql/managedInstances/administrators/read | Managed Instance の管理者の一覧を取得します。 |
> | Microsoft.Sql/managedInstances/administrators/write | 指定されたパラメーターでマネージド インスタンスの管理者を作成または更新します。 |
> | Microsoft.Sql/managedInstances/administrators/delete | マネージド インスタンスの既存の管理者を削除します。 |
> | Microsoft.Sql/managedInstances/azureADOnlyAuthentications/read | 特定のマネージド サーバーの Azure Active Directory のみの認証オブジェクトを読み取ります |
> | Microsoft.Sql/managedInstances/azureADOnlyAuthentications/write | 特定のマネージド サーバーの Azure Active Directory のみの認証オブジェクトを追加または更新します |
> | Microsoft.Sql/managedInstances/azureADOnlyAuthentications/delete | 特定のマネージド サーバーの Azure Active Directory のみの認証オブジェクトを削除します |
> | Microsoft.Sql/managedInstances/databases/read | 既存の管理されたデータベースを取得します。 |
> | Microsoft.Sql/managedInstances/databases/delete | 既存の管理されたデータベースを削除します。 |
> | Microsoft.Sql/managedInstances/databases/write | 新しいデータベースを作成するか、既存のデータベースを更新します。 |
> | Microsoft.Sql/managedInstances/databases/completeRestore/action | 管理されたデータベースの復元操作を完了します。 |
> | Microsoft.Sql/managedInstances/databases/backupLongTermRetentionPolicies/write | マネージド データベースの長期保有ポリシーを更新します |
> | Microsoft.Sql/managedInstances/databases/backupLongTermRetentionPolicies/read | マネージド データベースの長期保有ポリシーを取得します |
> | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/read | マネージド データベースの短期保持ポリシーを取得します |
> | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/write | マネージド データベースの短期保持ポリシーを更新します |
> | Microsoft.Sql/managedInstances/databases/columns/read | マネージド データベースの列の一覧を返します |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/read | 指定されたデータベースの機密ラベルを一覧表示します。 |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/write | 機密ラベルをバッチ更新します |
> | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/logDefinitions/read | マネージド インスタンス データベースで使用可能なログを取得します |
> | Microsoft.Sql/managedInstances/databases/queries/read | クエリ ID でクエリ テキストを取得します |
> | Microsoft.Sql/managedInstances/databases/queries/statistics/read | クエリ ID でクエリ実行の統計情報を取得します |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/read | 指定されたデータベースの機密ラベルを一覧表示します。 |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/write | 推薦される機密ラベルをバッチ更新します |
> | Microsoft.Sql/managedInstances/databases/restoreDetails/read | 復元中に、マネージド データベースの復元の詳細を返します。 |
> | Microsoft.Sql/managedInstances/databases/schemas/read | マネージド データベースのスキーマを取得します。 |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/read | マネージド データベースのテーブルを取得します |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/read | マネージド データベースの列を取得します |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/read | 指定された列の機密ラベルを取得します。 |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/write | 指定された列の機密ラベルを作成または更新します。 |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/delete | 指定された列の機密ラベルを削除します。 |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/disable/action | 指定された列に対する秘密度の推奨を無効にします。 |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/enable/action | 指定された列に対する秘密度の推奨を有効にします。 |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | 指定された管理されたデータベースのデータベース脅威検出ポリシーを変更します。 |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | 指定したサーバーに対して構成されているマネージド データベース脅威検出ポリシーの一覧を取得します |
> | Microsoft.Sql/managedInstances/databases/securityEvents/read | 管理されたデータベースのセキュリティ イベントを取得します。 |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/read | 指定されたデータベースの機密ラベルを一覧表示します。 |
> | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | 指定された管理されたデータベースでのデータベース Transparent Data Encryption の詳細を取得します。 |
> | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | 指定された管理されたデータベースのデータベース Transparent Data Encryption を変更します。 |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/write | 指定されたデータベースの脆弱性評価を変更します。 |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/delete | 指定されたデータベースの脆弱性評価を削除します。 |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | 指定されたデータベースの脆弱性評価ポリシーを取得します |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | 指定されたデータベースの脆弱性評価規則ベースラインを削除します。 |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/write | 指定されたデータベースの脆弱性評価規則ベースラインを変更します。 |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | 指定されたデータベースの脆弱性評価規則ベースラインを取得します。 |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | データベースの脆弱性評価スキャンを実行します。 |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | 既存のスキャン結果を人間が読める形式に変換します。 既に存在する場合は何も行われません。 |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | データベース脆弱性評価スキャン レコードの一覧を返すか、指定されたスキャン ID のスキャン レコードを取得します。 |
> | Microsoft.Sql/managedInstances/encryptionProtector/revalidate/action | 指定されたサーバー暗号化の保護機能のプロパティを更新します。 |
> | Microsoft.Sql/managedInstances/encryptionProtector/read | サーバー暗号化の保護機能の一覧を返すか、指定されたサーバー暗号化の保護機能のプロパティを取得します。 |
> | Microsoft.Sql/managedInstances/encryptionProtector/write | 指定されたサーバー暗号化の保護機能のプロパティを更新します。 |
> | Microsoft.Sql/managedInstances/hybridLink/read | ハイブリッド リンクの一覧を返します。または、指定された分散可用性グループのプロパティを取得します。 |
> | Microsoft.Sql/managedInstances/hybridLink/write | パラメーターが指定されたハイブリッド リンクを作成または更新します。 |
> | Microsoft.Sql/managedInstances/hybridLink/delete | 分散可用性グループが指定されたハイブリッド リンクを削除します。 |
> | Microsoft.Sql/managedInstances/inaccessibleManagedDatabases/read | マネージド インスタンス内のアクセスできないマネージド データベースの一覧を取得します |
> | Microsoft.Sql/managedInstances/keys/read | マネージド インスタンス キーの一覧を返すか、指定されたマネージド インスタンス キーのプロパティを取得します。 |
> | Microsoft.Sql/managedInstances/keys/write | 指定されたパラメーターでキーを作成するか、指定されたマネージド インスタンス キーのプロパティまたはタグを更新します。 |
> | Microsoft.Sql/managedInstances/keys/delete | 既存の Azure SQL Managed Instance キーを削除します。 |
> | Microsoft.Sql/managedInstances/metricDefinitions/read | マネージド インスタンスのメトリック定義を取得します。 |
> | Microsoft.Sql/managedInstances/metrics/read | Managed Instance のメトリックを取得します。 |
> | Microsoft.Sql/managedInstances/operations/read | マネージド インスタンス操作を取得します |
> | Microsoft.Sql/managedInstances/operations/cancel/action | Azure SQL マネージド インスタンスのまだ終了していない保留中非同期操作を取り消します。 |
> | Microsoft.Sql/managedInstances/privateEndpointConnectionProxies/read | プライベート エンドポイント接続プロキシの一覧を返すか、指定されたプライベート エンドポイント接続プロキシのプロパティを取得します。 |
> | Microsoft.Sql/managedInstances/privateEndpointConnectionProxies/write | 指定されたパラメーターでプライベート エンドポイント接続プロキシを作成するか、指定されたプライベート エンドポイント接続プロキシのプロパティまたはタグを更新します。 |
> | Microsoft.Sql/managedInstances/privateEndpointConnectionProxies/delete | 既存のプライベート エンドポイント接続プロキシを削除します |
> | Microsoft.Sql/managedInstances/privateEndpointConnectionProxies/validate/action | NRP 側からのプライベート エンドポイント接続作成呼び出しを検証します |
> | Microsoft.Sql/managedInstances/privateEndpointConnections/read | プライベート エンドポイント接続の一覧を返すか、指定されたプライベート エンドポイント接続のプロパティを取得します。 |
> | Microsoft.Sql/managedInstances/privateEndpointConnections/delete | 既存のプライベート エンドポイント接続を削除します |
> | Microsoft.Sql/managedInstances/privateEndpointConnections/write | 既存のプライベート エンドポイント接続を承認または拒否します |
> | Microsoft.Sql/managedInstances/privateLinkResources/read | 対応する SQL サーバーのプライベート リンクのリソースを取得します |
> | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/logDefinitions/read | マネージド インスタンスで使用可能なログを取得します |
> | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/metricDefinitions/read | マネージド インスタンスで使用可能なメトリックの種類を返します |
> | Microsoft.Sql/managedInstances/recoverableDatabases/read | 復元可能なマネージド データベースの一覧を返します |
> | Microsoft.Sql/managedInstances/restorableDroppedDatabases/read | 復元可能な削除されたマネージド データベースの一覧を返します。 |
> | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/read | ドロップされたマネージド データベースの短期保持ポリシーを取得します |
> | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/write | ドロップされたマネージド データベースの短期保持ポリシーを更新します |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/write | 指定された管理サーバーの管理サーバー脅威検出ポリシーを変更します。 |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/read | 指定したサーバーに対して構成されているマネージド サーバー脅威検出ポリシーの一覧を取得します |
> | Microsoft.Sql/managedInstances/serverTrustGroups/read | 既存の SQL Server 信頼グループをマネージド インスタンス名で返します |
> | Microsoft.Sql/managedInstances/topqueries/read | マネージド インスタンスのリソースを消費する上位のクエリを取得します |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/write | 特定のマネージド インスタンスの脆弱性評価を変更します |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/delete | 特定のマネージド インスタンスの脆弱性評価を削除します |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/read | 特定のマネージド インスタンスの脆弱性評価ポリシーを取得します |
> | Microsoft.Sql/operations/read | 使用可能な REST 操作を取得します。 |
> | Microsoft.Sql/restorableDroppedManagedInstances/read | 復元可能な削除されたマネージド インスタンスの一覧を返します。 |
> | Microsoft.Sql/servers/tdeCertificates/action | TDE 証明書を作成/更新します |
> | Microsoft.Sql/servers/import/action | 新しい Azure SQL Database をインポートします |
> | Microsoft.Sql/servers/read | サーバーの一覧を返すか、指定されたサーバーのプロパティを取得します。 |
> | Microsoft.Sql/servers/write | 指定されたパラメーターでサーバーを作成するか、指定されたサーバーのプロパティまたはタグを更新します。 |
> | Microsoft.Sql/servers/delete | 既存のサーバーを削除します。 |
> | Microsoft.Sql/servers/privateEndpointConnectionsApproval/action | ユーザーがプライベート エンドポイント接続の承認を許可されているかどうかを判断します。 |
> | Microsoft.Sql/servers/administratorOperationResults/read | サーバー管理者で進行中の操作を取得します。 |
> | Microsoft.Sql/servers/administrators/read | 特定の Azure Active Directory 管理者オブジェクトを取得します |
> | Microsoft.Sql/servers/administrators/write | 特定の Azure Active Directory 管理者オブジェクトを追加または更新します |
> | Microsoft.Sql/servers/administrators/delete | 特定の Azure Active Directory 管理者オブジェクトを削除します |
> | Microsoft.Sql/servers/advisors/read | サーバーで使用できる Advisor の一覧を返します。 |
> | Microsoft.Sql/servers/advisors/write | サーバー レベルでの Advisor の自動実行の状態を更新します。 |
> | Microsoft.Sql/servers/advisors/recommendedActions/read | サーバーの指定された Advisor の推奨アクションの一覧を返します。 |
> | Microsoft.Sql/servers/advisors/recommendedActions/write | サーバーに推奨アクションを適用します。 |
> | Microsoft.Sql/servers/auditingSettings/read | 指定されたサーバーで構成されているサーバー BLOB 監査ポリシーの詳細を取得します。 |
> | Microsoft.Sql/servers/auditingSettings/write | 指定されたサーバーのサーバー BLOB 監査を変更します。 |
> | Microsoft.Sql/servers/auditingSettings/operationResults/read | サーバー BLOB 監査ポリシーの設定操作の結果を取得します。 |
> | Microsoft.Sql/servers/automaticTuning/read | サーバーの自動調整の設定を返します。 |
> | Microsoft.Sql/servers/automaticTuning/write | サーバーの自動調整の設定を更新し、更新された設定を返します。 |
> | Microsoft.Sql/servers/azureADOnlyAuthentications/read | 特定のサーバーの Azure Active Directory のみの認証オブジェクトを読み取ります |
> | Microsoft.Sql/servers/azureADOnlyAuthentications/write | 特定のサーバーの Azure Active Directory 認証オブジェクトのみを追加または更新します |
> | Microsoft.Sql/servers/azureADOnlyAuthentications/delete | 特定のサーバーの Azure Active Directory のみの認証オブジェクトを削除します |
> | Microsoft.Sql/servers/communicationLinks/read | 指定されたサーバーの通信リンクの一覧を返します。 |
> | Microsoft.Sql/servers/communicationLinks/write | サーバー通信リンクを作成または更新します。 |
> | Microsoft.Sql/servers/communicationLinks/delete | 既存のサーバー通信リンクを削除します。 |
> | Microsoft.Sql/servers/connectionPolicies/read | 指定されたサーバーのサーバー接続ポリシーの一覧を返します。 |
> | Microsoft.Sql/servers/connectionPolicies/write | サーバー接続ポリシーを作成または更新します。 |
> | Microsoft.Sql/servers/databases/read | データベースの一覧を返すか、指定されたデータベースのプロパティを取得します。 |
> | Microsoft.Sql/servers/databases/write | 指定されたパラメーターでデータベースを作成するか、指定されたデータベースのプロパティまたはタグを更新します。 |
> | Microsoft.Sql/servers/databases/delete | 既存のデータベースを削除します。 |
> | Microsoft.Sql/servers/databases/pause/action | Azure SQL Datawarehouse データベースを一時停止します。 |
> | Microsoft.Sql/servers/databases/resume/action | Azure SQL Datawarehouse データベースを再開します。 |
> | Microsoft.Sql/servers/databases/export/action | Azure SQL Database をエクスポートします。 |
> | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | Azure SQL Datawarehouse データベースをアップグレードします。 |
> | Microsoft.Sql/servers/databases/move/action | 既存のデータベースの名前を変更します。 |
> | Microsoft.Sql/servers/databases/restorePoints/action | 新しい復元ポイントを作成します。 |
> | Microsoft.Sql/servers/databases/import/action | Azure SQL Database をインポートします |
> | Microsoft.Sql/servers/databases/failover/action | ユーザーが開始したデータベースのフェールオーバー。 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | データベースの脆弱性評価スキャンを実行します。 |
> | Microsoft.Sql/servers/databases/advisors/read | データベースで使用できる Advisor の一覧を返します。 |
> | Microsoft.Sql/servers/databases/advisors/write | データベース レベルでの Advisor の自動実行の状態を更新します。 |
> | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | データベースの指定された Advisor の推奨アクションの一覧を返します。 |
> | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | データベースに推奨アクションを適用します。 |
> | Microsoft.Sql/servers/databases/auditingSettings/read | 指定されたデータベースで構成されている BLOB 監査ポリシーの詳細を取得します。 |
> | Microsoft.Sql/servers/databases/auditingSettings/write | 指定されたデータベースの BLOB 監査ポリシーを変更します。 |
> | Microsoft.Sql/servers/databases/auditRecords/read | データベースの BLOB 監査レコードを取得します。 |
> | Microsoft.Sql/servers/databases/automaticTuning/read | データベースの自動調整の設定を返します。 |
> | Microsoft.Sql/servers/databases/automaticTuning/write | データベースの自動調整の設定を更新し、更新された設定を返します。 |
> | Microsoft.Sql/servers/databases/azureAsyncOperation/read | データベース操作の状態を取得します。 |
> | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | データベース バックアップ アーカイブ ポリシーを作成または更新します。 |
> | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | 指定されたデータベースのバックアップ アーカイブ ポリシーの一覧を返します。 |
> | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/read | データベースの短期保持ポリシーを取得します |
> | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/write | データベースの短期保持ポリシーを更新します |
> | Microsoft.Sql/servers/databases/columns/read | データベースの列の一覧を返します |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/read | 指定されたデータベースの機密ラベルを一覧表示します。 |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/write | 機密ラベルをバッチ更新します |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | データベースのデータ マスク ポリシーの一覧を返します。 |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | 指定されたデータベースのデータ マスク ポリシーを変更します。 |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | 指定されたデータベースで構成されているデータ マスク ポリシー ルールの詳細を取得します。 |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | 指定されたデータベースのデータ マスク ポリシー ルールを変更します。 |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | 指定されたデータベースのデータ マスク ポリシー ルールを削除します。 |
> | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | 選択したクエリ ID のデータ ウェアハウス分散クエリ情報を返します。 |
> | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | 選択したステップ ID のデータ ウェアハウス クエリの分散クエリ ステップ情報を返します。 |
> | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | 実行中のクエリと中断されたクエリを含む SQL Data Warehouse インスタンスのユーザー アクティビティを取得します |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | 指定されたデータベースで構成されている拡張 BLOB 監査ポリシーの詳細を取得します。 |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | 指定されたデータベースの拡張 BLOB 監査ポリシーを変更します。 |
> | Microsoft.Sql/servers/databases/extensions/write | データベース拡張機能の操作を実行します。 |
> | Microsoft.Sql/servers/databases/extensions/read | データベース拡張機能の操作を取得します。 |
> | Microsoft.Sql/servers/databases/extensions/importExtensionOperationResults/read | 進行中のインポート操作を取得します |
> | Microsoft.Sql/servers/databases/geoBackupPolicies/read | 指定されたデータベースの geo バックアップ ポリシーを取得します。 |
> | Microsoft.Sql/servers/databases/geoBackupPolicies/write | データベースの geo バックアップ ポリシーを作成または更新します。 |
> | Microsoft.Sql/servers/databases/importExportAzureAsyncOperation/read | 進行中のインポート/エクスポート操作を取得します。 |
> | Microsoft.Sql/servers/databases/importExportOperationResults/read | 進行中のインポート/エクスポート操作を取得します。 |
> | Microsoft.Sql/servers/databases/maintenanceWindowOptions/read | 選択したデータベースに使用可能なメンテナンス期間の一覧を取得します。 |
> | Microsoft.Sql/servers/databases/maintenanceWindows/read | 選択したデータベースのメンテナンス期間の設定を取得します。 |
> | Microsoft.Sql/servers/databases/maintenanceWindows/write | 選択したデータベースのメンテナンス期間の設定を行います。 |
> | Microsoft.Sql/servers/databases/metricDefinitions/read | データベースの利用可能なメトリックの種類を返します。 |
> | Microsoft.Sql/servers/databases/metrics/read | データベースのメトリックを返します。 |
> | Microsoft.Sql/servers/databases/operationResults/read | データベース操作の状態を取得します。 |
> | Microsoft.Sql/servers/databases/operations/cancel/action | Azure SQL Database のまだ終了していない保留中非同期操作を取り消します。 |
> | Microsoft.Sql/servers/databases/operations/read | データベースで実行された操作の一覧を返します。 |
> | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | データベースの利用可能なログを取得します。 |
> | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | データベースの利用可能なメトリックの種類を返します。 |
> | Microsoft.Sql/servers/databases/queryStore/read | データベースのクエリ ストアの設定の現在値を返します。 |
> | Microsoft.Sql/servers/databases/queryStore/write | データベースのクエリ ストア設定を更新します。 |
> | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | 指定されたパラメーターに対応するクエリ テキストのコレクションを返します。 |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/read | 指定されたデータベースの機密ラベルを一覧表示します。 |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/write | 推薦される機密ラベルをバッチ更新します |
> | Microsoft.Sql/servers/databases/replicationLinks/read | レプリケーション リンクの一覧を返すか、指定されたレプリケーション リンクのプロパティを取得します。 |
> | Microsoft.Sql/servers/databases/replicationLinks/failover/action | プライマリからすべての変更を同期した後にフェールオーバーします。このデータベースがレプリケーション関係のプライマリになり、リモート プライマリがセカンダリになります。 |
> | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | 即座にフェールオーバーします。このデータベースがレプリケーション関係のプライマリになり、リモート プライマリがセカンダリになります。データが失われる可能性があります。 |
> | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | リンクのレプリケーション モードを同期モードまたは非同期モードに更新します。 |
> | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | レプリケーション関係を強制的に終了するか、パートナーとの同期後に終了します。 |
> | Microsoft.Sql/servers/databases/restorePoints/read | データベースの復元ポイントを返します。 |
> | Microsoft.Sql/servers/databases/restorePoints/delete | データベースの復元ポイントを削除します。 |
> | Microsoft.Sql/servers/databases/schemas/read | データベースのスキーマを取得します。 |
> | Microsoft.Sql/servers/databases/schemas/tables/read | データベースのテーブルを取得します。 |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/read | データベースの列を取得します。 |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/enable/action | 指定された列に対する秘密度の推奨を有効にします。 |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/disable/action | 指定された列に対する秘密度の推奨を無効にします。 |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | 指定された列の機密ラベルを取得します。 |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | 指定された列の機密ラベルを作成または更新します。 |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | 指定された列の機密ラベルを削除します。 |
> | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | データベースのインデックス推奨事項の一覧を取得します。 |
> | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | インデックス推奨事項を適用します。 |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/write | 指定されたデータベースのデータベース脅威検出ポリシーを変更します |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/read | 指定したサーバーに対して構成されているデータベース脅威検出ポリシーの一覧を取得します |
> | Microsoft.Sql/servers/databases/securityMetrics/read | データベースのセキュリティ メトリックのコレクションを取得します。 |
> | Microsoft.Sql/servers/databases/sensitivityLabels/read | 指定されたデータベースの機密ラベルを一覧表示します。 |
> | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | パフォーマンスを向上させたり、コストを削減したりするために、クエリ実行の統計に基づいて、データベースのスケールアップまたはスケールダウンに関する提案を返します。 |
> | Microsoft.Sql/servers/databases/skus/read | データベースの使用可能な SKU のコレクションを取得します。 |
> | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | 同期ハブのデータベース スキーマを更新します。 |
> | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | 同期グループの同期を取り消します。 |
> | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | 同期グループの同期をトリガーします。 |
> | Microsoft.Sql/servers/databases/syncGroups/read | 同期グループの一覧を返すか、指定された同期グループのプロパティを取得します。 |
> | Microsoft.Sql/servers/databases/syncGroups/write | 指定されたパラメーターで同期グループを作成するか、指定され同期グループのプロパティを更新します。 |
> | Microsoft.Sql/servers/databases/syncGroups/delete | 既存の同期グループを削除します。 |
> | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | 同期ハブ データベース スキーマの一覧を返します。 |
> | Microsoft.Sql/servers/databases/syncGroups/logs/read | 同期グループのログの一覧を返します。 |
> | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | 同期ハブのスキーマ更新操作の結果を取得します。 |
> | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | 同期メンバーの一覧を返すか、指定された同期メンバーのプロパティを取得します。 |
> | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | 指定されたパラメーターで同期メンバーを作成するか、指定され同期メンバーのプロパティを更新します。 |
> | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | 既存の同期メンバーを削除します。 |
> | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | 同期メンバーのスキーマを更新します。 |
> | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | 同期メンバーのスキーマ更新操作の結果を取得します。 |
> | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | 同期メンバー データベース スキーマの一覧を返します。 |
> | Microsoft.Sql/servers/databases/topQueries/queryText/action | 選択したクエリ ID の Transact-SQL テキストを返します。 |
> | Microsoft.Sql/servers/databases/topQueries/read | 選択した期間の選択したクエリの集計されたランタイム統計を返します。 |
> | Microsoft.Sql/servers/databases/topQueries/statistics/read | 選択した期間の選択したクエリの集計されたランタイム統計を返します。 |
> | Microsoft.Sql/servers/databases/transparentDataEncryption/read | 指定されたマネージド データベースでの論理データベース Transparent Data Encryption の詳細を取得します |
> | Microsoft.Sql/servers/databases/transparentDataEncryption/write | 指定された論理データベースのデータベース Transparent Data Encryption を変更します。 |
> | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | Transparent Data Encryption での進行中の操作を取得します。 |
> | Microsoft.Sql/servers/databases/usages/read | Azure SQL Database の使用状況の情報を取得します。 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | 指定されたデータベースの脆弱性評価を変更します。 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | 指定されたデータベースの脆弱性評価を削除します。 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | 指定されたデータベースの脆弱性評価ポリシーを取得します |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | 指定されたデータベースの脆弱性評価規則ベースラインを削除します。 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | 指定されたデータベースの脆弱性評価規則ベースラインを変更します。 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | 指定されたデータベースの脆弱性評価規則ベースラインを取得します。 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | データベースの脆弱性評価スキャンを実行します。 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | データベース脆弱性評価スキャン レコードの一覧を返すか、指定されたスキャン ID のスキャン レコードを取得します。 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | 既存のスキャン結果を人間が読める形式に変換します。 既に存在する場合は何も行われません。 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | データベース脆弱性評価スキャンの実行操作の結果を取得します。 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | 指定されたデータベースで構成されている脆弱性評価の詳細を取得します。 |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | 指定されたデータベースの脆弱性評価を変更します。 |
> | Microsoft.Sql/servers/databases/workloadGroups/read | 選択したデータベースのワークロード グループを一覧表示します。 |
> | Microsoft.Sql/servers/databases/workloadGroups/write | 特定のワークロード グループのプロパティを設定します。 |
> | Microsoft.Sql/servers/databases/workloadGroups/delete | 特定のワークロード グループをドロップします。 |
> | Microsoft.Sql/servers/databases/workloadGroups/workloadClassifiers/read | 選択したデータベースのワークロード 分類子を一覧表示します。 |
> | Microsoft.Sql/servers/databases/workloadGroups/workloadClassifiers/write | 特定のワークロード分類子のプロパティを設定します。 |
> | Microsoft.Sql/servers/databases/workloadGroups/workloadClassifiers/delete | 特定のワークロード分類子をドロップします。 |
> | Microsoft.Sql/servers/devOpsAuditingSettings/read | 特定のサーバーで構成されているサーバー DevOps 監査ポリシーの詳細を取得します |
> | Microsoft.Sql/servers/devOpsAuditingSettings/write | 特定のサーバーに対するサーバー DevOps 監査ポリシーを変更します |
> | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | このサーバーを含むディザスター リカバリー構成のコレクションを取得します。 |
> | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | サーバーのディザスター リカバリー構成を変更します。 |
> | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | 指定されたサーバーの既存のディザスター リカバリー構成を削除します。 |
> | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | DisasterRecoveryConfiguration の構成をフェールオーバーします。 |
> | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | DisasterRecoveryConfiguration のフェールオーバーを強制します。 |
> | Microsoft.Sql/servers/elasticPoolEstimates/read | このサーバー用に既に作成されているエラスティック プール推定値の一覧を返します。 |
> | Microsoft.Sql/servers/elasticPoolEstimates/write | 提供されているデータベースの一覧用に、新しいエラスティック プール推定値を作成します。 |
> | Microsoft.Sql/servers/elasticPools/read | 指定されたサーバーのエラスティック プールの詳細を取得します。 |
> | Microsoft.Sql/servers/elasticPools/write | 新しいエラスティック プールを作成するか、既存のエラスティック プールのプロパティを変更します。 |
> | Microsoft.Sql/servers/elasticPools/delete | 既存のエラスティック プールを削除します。 |
> | Microsoft.Sql/servers/elasticPools/failover/action | ユーザーが開始したエラスティック プールのフェールオーバー。 |
> | Microsoft.Sql/servers/elasticPools/advisors/read | エラスティック プールで使用できる Advisor の一覧を返します。 |
> | Microsoft.Sql/servers/elasticPools/advisors/write | エラスティック プール レベルでの Advisor の自動実行の状態を更新します。 |
> | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | エラスティック プールの指定された Advisor の推奨アクションの一覧を返します。 |
> | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | エラスティック プールに推奨アクションを適用します。 |
> | Microsoft.Sql/servers/elasticPools/databases/read | エラスティック プールのデータベースの一覧を取得します。 |
> | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | 指定されたエラスティック データベース プールのアクティビティと詳細を取得します。 |
> | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | エラスティック データベース プールに含まれる指定されたデータベースのアクティビティと詳細を取得します。 |
> | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | エラスティック データベース プールの利用可能なメトリックの種類を返します。 |
> | Microsoft.Sql/servers/elasticPools/metrics/read | エラスティック データベース プールのメトリックを返します。 |
> | Microsoft.Sql/servers/elasticPools/operations/cancel/action | Azure SQL エラスティック プールのまだ終了していない保留中非同期操作を取り消します。 |
> | Microsoft.Sql/servers/elasticPools/operations/read | エラスティック プールで実行された操作の一覧を返します。 |
> | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | エラスティック データベース プールの利用可能なメトリックの種類を返します。 |
> | Microsoft.Sql/servers/elasticPools/skus/read | エラスティック プールの使用可能な SKU のコレクションを取得します。 |
> | Microsoft.Sql/servers/encryptionProtector/revalidate/action | 指定されたサーバー暗号化の保護機能のプロパティを更新します。 |
> | Microsoft.Sql/servers/encryptionProtector/read | サーバー暗号化の保護機能の一覧を返すか、指定されたサーバー暗号化の保護機能のプロパティを取得します。 |
> | Microsoft.Sql/servers/encryptionProtector/write | 指定されたサーバー暗号化の保護機能のプロパティを更新します。 |
> | Microsoft.Sql/servers/extendedAuditingSettings/read | 指定されたサーバーで構成されている拡張サーバー BLOB 監査ポリシーの詳細を取得します。 |
> | Microsoft.Sql/servers/extendedAuditingSettings/write | 指定されたサーバーの拡張サーバー BLOB 監査を変更します。 |
> | Microsoft.Sql/servers/failoverGroups/read | フェールオーバー グループの一覧を返すか、指定されたフェールオーバー グループのプロパティを取得します。 |
> | Microsoft.Sql/servers/failoverGroups/write | 指定されたパラメーターでフェールオーバー グループを作成するか、指定されたフェールオーバー グループのプロパティまたはタグを更新します。 |
> | Microsoft.Sql/servers/failoverGroups/delete | 既存のフェールオーバー グループを削除します。 |
> | Microsoft.Sql/servers/failoverGroups/failover/action | 既存のフェールオーバー グループで計画されたフェールオーバーを実行します。 |
> | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | 既存のフェールオーバー グループで強制フェールオーバーを実行します。 |
> | Microsoft.Sql/servers/firewallRules/write | 指定されたパラメーターでサーバー ファイアウォール規則を作成するか、指定された規則のプロパティを更新するか、新しいサーバー ファイアウォール規則ですべての既存規則を上書きします。 |
> | Microsoft.Sql/servers/firewallRules/read | サーバー ファイアウォール規則の一覧を返すか、指定されたサーバー ファイアウォール規則のプロパティを取得します。 |
> | Microsoft.Sql/servers/firewallRules/delete | 既存のサーバー ファイアウォール規則を削除します。 |
> | Microsoft.Sql/servers/importExportOperationResults/read | 進行中のインポート/エクスポート操作を取得します。 |
> | Microsoft.Sql/servers/inaccessibleDatabases/read | 論理サーバー内のアクセスできないデータベースの一覧を返します。 |
> | Microsoft.Sql/servers/jobAgents/read | Azure SQL DB のジョブ エージェントを取得します |
> | Microsoft.Sql/servers/jobAgents/write | Azure SQL DB のジョブ エージェントを作成または更新します |
> | Microsoft.Sql/servers/jobAgents/delete | Azure SQL DB のジョブ エージェントを削除します |
> | Microsoft.Sql/servers/keys/read | サーバー キーの一覧を返すか、指定されたサーバー キーのプロパティを取得します。 |
> | Microsoft.Sql/servers/keys/write | 指定されたパラメーターでキーを作成するか、指定されたサーバー キーのプロパティまたはタグを更新します。 |
> | Microsoft.Sql/servers/keys/delete | 既存のサーバー キーを削除します。 |
> | Microsoft.Sql/servers/operationResults/read | 進行中のサーバー操作を取得します。 |
> | Microsoft.Sql/servers/operations/read | サーバーで実行された操作の一覧を返します |
> | Microsoft.Sql/servers/privateEndpointConnectionProxies/updatePrivateEndpointProperties/action | NRP で、プライベート エンドポイント接続にプロパティをバックフィルするために使用されます |
> | Microsoft.Sql/servers/privateEndpointConnectionProxies/validate/action | NRP 側からのプライベート エンドポイント接続作成呼び出しを検証します |
> | Microsoft.Sql/servers/privateEndpointConnectionProxies/read | プライベート エンドポイント接続プロキシの一覧を返すか、指定されたプライベート エンドポイント接続プロキシのプロパティを取得します。 |
> | Microsoft.Sql/servers/privateEndpointConnectionProxies/write | 指定されたパラメーターでプライベート エンドポイント接続プロキシを作成するか、指定されたプライベート エンドポイント接続プロキシのプロパティまたはタグを更新します。 |
> | Microsoft.Sql/servers/privateEndpointConnectionProxies/delete | 既存のプライベート エンドポイント接続プロキシを削除します |
> | Microsoft.Sql/servers/privateEndpointConnections/read | プライベート エンドポイント接続の一覧を返すか、指定されたプライベート エンドポイント接続のプロパティを取得します。 |
> | Microsoft.Sql/servers/privateEndpointConnections/delete | 既存のプライベート エンドポイント接続を削除します |
> | Microsoft.Sql/servers/privateEndpointConnections/write | 既存のプライベート エンドポイント接続を承認または拒否します |
> | Microsoft.Sql/servers/privateLinkResources/read | 対応する SQL サーバーのプライベート リンクのリソースを取得します |
> | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | サーバーで使用可能なメトリックの種類を返します。 |
> | Microsoft.Sql/servers/recommendedElasticPools/read | リソース使用率の履歴に基づいて、コストを削減したり、パフォーマンスを向上させたりするために、エラスティック データベース プールの推奨事項を取得します。 |
> | Microsoft.Sql/servers/recommendedElasticPools/databases/read | 指定されたサーバーの推奨エラスティック データベース プールのメトリックを取得します。 |
> | Microsoft.Sql/servers/recoverableDatabases/read | この操作は、最新の既知の良好なバックアップ ポイントにデータベースを復元するために、ライブ データベースのディザスター リカバリーに使用します。 最新の良好なバックアップに関する情報が返されますが、データベースを実際に復元するわけではありません。 |
> | Microsoft.Sql/servers/replicationLinks/read | レプリケーション リンクの一覧を返すか、指定されたレプリケーション リンクのプロパティを取得します。 |
> | Microsoft.Sql/servers/restorableDroppedDatabases/read | 指定されたサーバーで破棄されたデータベースのうち、まだリテンション期間ポリシーの範囲内にあるデータベースの一覧を取得します。 |
> | Microsoft.Sql/servers/securityAlertPolicies/write | 指定されたサーバーの脅威検出ポリシーを変更します。 |
> | Microsoft.Sql/servers/securityAlertPolicies/read | 指定したサーバーに対して構成されているサーバー脅威検出ポリシーの一覧を取得します |
> | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | サーバーの脅威検出ポリシーの書き込み操作の結果を取得します。 |
> | Microsoft.Sql/servers/serviceObjectives/read | 指定されたサーバーで利用可能なサービス レベルの目標 (パフォーマンス レベルとも呼ばれます) の一覧を取得します。 |
> | Microsoft.Sql/servers/syncAgents/read | 同期エージェントの一覧を返すか、指定された同期エージェントのプロパティを取得します。 |
> | Microsoft.Sql/servers/syncAgents/write | 指定されたパラメーターで同期エージェントを作成するか、指定され同期エージェントのプロパティを更新します。 |
> | Microsoft.Sql/servers/syncAgents/delete | 既存の同期エージェントを削除します。 |
> | Microsoft.Sql/servers/syncAgents/generateKey/action | 同期エージェントの登録キーを生成します。 |
> | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | 同期エージェントのリンクされたデータベースの一覧を返します。 |
> | Microsoft.Sql/servers/usages/read | サーバーの DTU クォータと、サーバー内のすべてのデータベースによる現在の DTU 消費量を返します |
> | Microsoft.Sql/servers/virtualNetworkRules/read | 仮想ネットワーク ルールの一覧を返すか、指定された仮想ネットワーク ルールのプロパティを取得します。 |
> | Microsoft.Sql/servers/virtualNetworkRules/write | 指定されたパラメーターで仮想ネットワーク規則を作成するか、指定された仮想ネットワーク規則のプロパティまたはタグを更新します。 |
> | Microsoft.Sql/servers/virtualNetworkRules/delete | 既存の仮想ネットワーク ルールを削除します。 |
> | Microsoft.Sql/servers/vulnerabilityAssessments/write | 特定のサーバーの脆弱性評価を変更します |
> | Microsoft.Sql/servers/vulnerabilityAssessments/delete | 特定のサーバーの脆弱性評価を削除します |
> | Microsoft.Sql/servers/vulnerabilityAssessments/read | 指定されたサーバーの脆弱性評価ポリシーを取得します |
> | Microsoft.Sql/virtualClusters/updateManagedInstanceDnsServers/action | Azure SQL 仮想クラスターの DNS サーバーの構成を、仮想クラスターが配置されている Azure Virtual Network の構成と同期します。 |
> | Microsoft.Sql/virtualClusters/read | 仮想クラスターの一覧を返すか、指定された仮想クラスターのプロパティを取得します。 |
> | Microsoft.Sql/virtualClusters/write | 仮想クラスターのタグを更新します。 |
> | Microsoft.Sql/virtualClusters/delete | 既存の仮想クラスターを削除します。 |

### <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

Azure サービス:[Azure Virtual Machines における SQL Server](../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.SqlVirtualMachine/register/action | Microsoft.SqlVirtualMachine リソース プロバイダーにサブスクリプションを登録します |
> | Microsoft.SqlVirtualMachine/unregister/action | Microsoft.SqlVirtualMachine リソース プロバイダーにサブスクリプションを登録解除します |
> | Microsoft.SqlVirtualMachine/locations/registerSqlVmCandidate/action | SQL VM 候補を登録します |
> | Microsoft.SqlVirtualMachine/locations/availabilityGroupListenerOperationResults/read | 可用性グループ リスナー操作の結果を取得します |
> | Microsoft.SqlVirtualMachine/locations/sqlVirtualMachineGroupOperationResults/read | SQL 仮想マシン グループ操作の結果を取得します |
> | Microsoft.SqlVirtualMachine/locations/sqlVirtualMachineOperationResults/read | SQL 仮想マシン操作の結果を取得します |
> | Microsoft.SqlVirtualMachine/operations/read |  |
> | Microsoft.SqlVirtualMachine/sqlVirtualMachineGroups/read | SQL 仮想マシン グループの詳細を取得します |
> | Microsoft.SqlVirtualMachine/sqlVirtualMachineGroups/write | 新しい SQL 仮想マシン グループを作成するか、既存の仮想マシン グループのプロパティを変更します |
> | Microsoft.SqlVirtualMachine/sqlVirtualMachineGroups/delete | 既存の SQL 仮想マシン グループを削除します |
> | Microsoft.SqlVirtualMachine/sqlVirtualMachineGroups/availabilityGroupListeners/read | 指定された SQL 仮想マシン グループの SQL 可用性グループ リスナーの詳細を取得します |
> | Microsoft.SqlVirtualMachine/sqlVirtualMachineGroups/availabilityGroupListeners/write | SQL 可用性グループ リスナーのプロパティを新しく作成するか、既存の可用性グループ リスナーのプロパティを変更します |
> | Microsoft.SqlVirtualMachine/sqlVirtualMachineGroups/availabilityGroupListeners/delete | 既存の可用性グループ リスナーを削除します |
> | Microsoft.SqlVirtualMachine/sqlVirtualMachineGroups/sqlVirtualMachines/read | SQL 仮想マシンを特定の SQL 仮想マシン グループで一覧表示します |
> | Microsoft.SqlVirtualMachine/sqlVirtualMachines/read | SQL 仮想マシンの詳細を取得します |
> | Microsoft.SqlVirtualMachine/sqlVirtualMachines/write | 新しい SQL 仮想マシンを作成するか、既存の SQL 仮想マシンのプロパティを変更します |
> | Microsoft.SqlVirtualMachine/sqlVirtualMachines/delete | 既存の SQL 仮想マシンを削除します |

## <a name="analytics"></a>Analytics

### <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

Azure サービス:[Azure Analysis Services](../analysis-services/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.AnalysisServices/register/action | Analysis Services リソース プロバイダーを登録します。 |
> | Microsoft.AnalysisServices/locations/checkNameAvailability/action | 指定された分析サーバー名が有効であり、使用されていないことを確認します。 |
> | Microsoft.AnalysisServices/locations/operationresults/read | 指定された操作の結果の情報を取得します。 |
> | Microsoft.AnalysisServices/locations/operationstatuses/read | 指定された操作の状態の情報を取得します。 |
> | Microsoft.AnalysisServices/operations/read | 操作の情報を取得します。 |
> | Microsoft.AnalysisServices/servers/read | 指定された分析サーバーの情報を取得します。 |
> | Microsoft.AnalysisServices/servers/write | 指定された分析サーバーを作成または更新します。 |
> | Microsoft.AnalysisServices/servers/delete | 分析サーバーを削除します。 |
> | Microsoft.AnalysisServices/servers/suspend/action | 分析サーバーを中断します。 |
> | Microsoft.AnalysisServices/servers/resume/action | 分析サーバーを再開します。 |
> | Microsoft.AnalysisServices/servers/listGatewayStatus/action | サーバーに関連付けられているゲートウェイの状態を一覧表示します。 |
> | Microsoft.AnalysisServices/servers/skus/read | サーバーの利用可能な SKU 情報を取得します。 |
> | Microsoft.AnalysisServices/skus/read | SKU の情報を取得します。 |

### <a name="microsoftdatabricks"></a>Microsoft.Databricks

Azure サービス:[Azure Databricks](/azure/databricks/)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Databricks/register/action | Databricks に登録します。 |
> | Microsoft.Databricks/locations/getNetworkPolicies/action | NRP で使用されている場所に基づいてサブネットのネットワーク インテント ポリシーを取得します |
> | Microsoft.Databricks/locations/operationstatuses/read | リソースの操作の状態を読み取ります。 |
> | Microsoft.Databricks/operations/read | 操作の一覧を取得します。 |
> | Microsoft.Databricks/workspaces/read | Databricks ワークスペースの一覧を取得します。 |
> | Microsoft.Databricks/workspaces/write | Databricks ワークスペースを作成します。 |
> | Microsoft.Databricks/workspaces/delete | Databricks ワークスペースを削除します。 |
> | Microsoft.Databricks/workspaces/refreshPermissions/action | ワークスペースのアクセス許可を更新します。 |
> | Microsoft.Databricks/workspaces/migratePrivateLinkWorkspaces/action | 'requiredNsgRules' と 'enablePublicAccess' に基づいた新しい NIP テンプレートを適用します |
> | Microsoft.Databricks/workspaces/updateDenyAssignment/action | ワークスペースの管理対象リソース グループに対する拒否割り当ての拒否アクションを更新します。 |
> | Microsoft.Databricks/workspaces/refreshWorkspaces/action | URL など、新しい詳細情報でワークスペースを更新します |
> | Microsoft.Databricks/workspaces/dbWorkspaces/write | Databricks ワークスペースを初期化します (内部のみ) |
> | Microsoft.Databricks/workspaces/privateEndpointConnectionProxies/read | プライベート エンドポイント接続プロキシを取得します |
> | Microsoft.Databricks/workspaces/privateEndpointConnectionProxies/validate/action | プライベート エンドポイント接続プロキシを検証します |
> | Microsoft.Databricks/workspaces/privateEndpointConnectionProxies/write | プライベート エンドポイント接続プロキシを設定します |
> | Microsoft.Databricks/workspaces/privateEndpointConnectionProxies/delete | プライベート エンドポイント接続プロキシを削除します |
> | Microsoft.Databricks/workspaces/privateEndpointConnections/read | プライベート エンドポイント接続を一覧表示します |
> | Microsoft.Databricks/workspaces/privateEndpointConnections/write | プライベート エンドポイント接続を承認します |
> | Microsoft.Databricks/workspaces/privateEndpointConnections/delete | プライベート エンドポイント接続を削除する |
> | Microsoft.Databricks/workspaces/privateLinkResources/read | プライベート リンク リソースを一覧表示します |
> | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/read | Databricks ワークスペースの使用可能な診断設定を設定します |
> | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/write | 診断設定を追加または変更します。 |
> | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/logDefinitions/read | Databricks ワークスペースの使用可能なログ定義を取得します |
> | Microsoft.Databricks/workspaces/virtualNetworkPeerings/read | 仮想ネットワーク ピアリングを取得します。 |
> | Microsoft.Databricks/workspaces/virtualNetworkPeerings/write | 仮想ネットワーク ピアリングを追加または変更します。 |
> | Microsoft.Databricks/workspaces/virtualNetworkPeerings/delete | 仮想ネットワーク ピアリングを削除します。 |

### <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

Azure サービス:[Data Lake Analytics](../data-lake-analytics/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.DataLakeAnalytics/register/action | DataLakeAnalytics にサブスクリプションを登録します。 |
> | Microsoft.DataLakeAnalytics/accounts/read | 既存の DataLakeAnalytics アカウントに関する情報を取得します。 |
> | Microsoft.DataLakeAnalytics/accounts/write | DataLakeAnalytics アカウントを作成または更新します。 |
> | Microsoft.DataLakeAnalytics/accounts/delete | DataLakeAnalytics アカウントを削除します。 |
> | Microsoft.DataLakeAnalytics/accounts/transferAnalyticsUnits/action | DataLakeAnalytics アカウント間で SystemMaxAnalyticsUnits を転送します。 |
> | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | 他のユーザーによって送信されたジョブを取り消すアクセス許可を付与します。 |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | コンピューティング ポリシーに関する情報を取得します。 |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | コンピューティング ポリシーを作成または更新します。 |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | コンピューティング ポリシーを削除します。 |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | DataLakeAnalytics アカウントのリンクされた DataLakeStore アカウントに関する情報を取得します。 |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | DataLakeAnalytics アカウントのリンクされた DataLakeStore アカウントを作成または更新します。 |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | DataLakeAnalytics アカウントから DataLakeStore アカウントのリンクを解除します。 |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreGen2Accounts/read | DataLakeAnalytics アカウントのリンクされた DataLakeStoreGen2 アカウントに関する情報を取得します。 |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreGen2Accounts/write | DataLakeAnalytics アカウントのリンクされた DataLakeStoreGen2 アカウントを作成または更新します。 |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreGen2Accounts/delete | DataLakeAnalytics アカウントから DataLakeStoreGen2 アカウントのリンクを解除します。 |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | ファイアウォール規則に関する情報を取得します。 |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | ファイアウォール規則を作成または更新します。 |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | ファイアウォール規則を削除します。 |
> | Microsoft.DataLakeAnalytics/accounts/operationResults/read | DataLakeAnalytics アカウントの操作の結果を取得します。 |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | DataLakeAnalytics アカウントのリンクされたストレージ アカウントに関する情報を取得します。 |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | DataLakeAnalytics アカウントのリンクされたストレージ アカウントを作成または更新します。 |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | DataLakeAnalytics アカウントからストレージ アカウントをリンク解除します。 |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | DataLakeAnalytics アカウントのリンクされたストレージ アカウントのコンテナーを取得します。 |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | DataLakeAnalytics アカウントのリンクされたストレージ アカウントのストレージ コンテナーの SAS トークンを一覧表示します。 |
> | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/read | 仮想ネットワーク規則に関する情報を取得します。 |
> | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/write | 仮想ネットワーク規則を作成または更新します。 |
> | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/delete | 仮想ネットワーク規則を削除します。 |
> | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | DataLakeAnalytics アカウントの名前を使用できるかどうかを確認します。 |
> | Microsoft.DataLakeAnalytics/locations/capability/read | DataLakeAnalytics の使用に関するサブスクリプションの機能の情報を取得します。 |
> | Microsoft.DataLakeAnalytics/locations/operationResults/read | DataLakeAnalytics アカウントの操作の結果を取得します。 |
> | Microsoft.DataLakeAnalytics/locations/usages/read | DataLakeAnalytics の使用に関するサブスクリプションのクォータ使用状況の情報を取得します。 |
> | Microsoft.DataLakeAnalytics/operations/read | DataLakeAnalytics で使用可能な操作を取得します。 |

### <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

Azure サービス:[Azure Data Lake Store](../storage/blobs/data-lake-storage-introduction.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.DataLakeStore/register/action | DataLakeStore にサブスクリプションを登録します。 |
> | Microsoft.DataLakeStore/accounts/read | 既存の DataLakeStore アカウントに関する情報を取得します。 |
> | Microsoft.DataLakeStore/accounts/write | DataLakeStore アカウントを作成または更新します。 |
> | Microsoft.DataLakeStore/accounts/delete | DataLakeStore アカウントを削除します。 |
> | Microsoft.DataLakeStore/accounts/enableKeyVault/action | DataLakeStore アカウントの KeyVault を有効にします。 |
> | Microsoft.DataLakeStore/accounts/Superuser/action | Microsoft.Authorization/roleAssignments/write で許可されているときに、Data Lake Store でのスーパーユーザーを許可します。 |
> | Microsoft.DataLakeStore/accounts/cosmosCertMappings/read | Cosmos 証明書マッピングに関する情報を取得します。 |
> | Microsoft.DataLakeStore/accounts/cosmosCertMappings/write | Cosmos 証明書マッピングを作成または更新します。 |
> | Microsoft.DataLakeStore/accounts/cosmosCertMappings/delete | Cosmos 証明書マッピングを削除します。 |
> | Microsoft.DataLakeStore/accounts/eventGridFilters/read | EventGrid フィルターを取得します。 |
> | Microsoft.DataLakeStore/accounts/eventGridFilters/write | EventGrid のフィルターを作成または更新します。 |
> | Microsoft.DataLakeStore/accounts/eventGridFilters/delete | EventGrid フィルターを削除します。 |
> | Microsoft.DataLakeStore/accounts/firewallRules/read | ファイアウォール規則に関する情報を取得します。 |
> | Microsoft.DataLakeStore/accounts/firewallRules/write | ファイアウォール規則を作成または更新します。 |
> | Microsoft.DataLakeStore/accounts/firewallRules/delete | ファイアウォール規則を削除します。 |
> | Microsoft.DataLakeStore/accounts/mountpoints/read | マウント ポイントに関する情報を取得します。 |
> | Microsoft.DataLakeStore/accounts/operationResults/read | DataLakeStore アカウントの操作の結果を取得します。 |
> | Microsoft.DataLakeStore/accounts/shares/read | 共有に関する情報を取得します。 |
> | Microsoft.DataLakeStore/accounts/shares/write | 共有を作成または更新します。 |
> | Microsoft.DataLakeStore/accounts/shares/delete | 共有を削除します。 |
> | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | 信頼できる ID プロバイダーに関する情報を取得します。 |
> | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | 信頼できる ID プロバイダーを作成または更新します。 |
> | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | 信頼できる ID プロバイダーを削除します。 |
> | Microsoft.DataLakeStore/accounts/virtualNetworkRules/read | 仮想ネットワーク規則に関する情報を取得します。 |
> | Microsoft.DataLakeStore/accounts/virtualNetworkRules/write | 仮想ネットワーク規則を作成または更新します。 |
> | Microsoft.DataLakeStore/accounts/virtualNetworkRules/delete | 仮想ネットワーク規則を削除します。 |
> | Microsoft.DataLakeStore/locations/checkNameAvailability/action | DataLakeStore アカウントの名前を使用できるかどうかを確認します。 |
> | Microsoft.DataLakeStore/locations/capability/read | DataLakeStore の使用に関するサブスクリプションの機能の情報を取得します。 |
> | Microsoft.DataLakeStore/locations/operationResults/read | DataLakeStore アカウントの操作の結果を取得します。 |
> | Microsoft.DataLakeStore/locations/usages/read | DataLakeStore の使用に関するサブスクリプションのクォータ使用状況の情報を取得します。 |
> | Microsoft.DataLakeStore/operations/read | DataLakeStore で使用可能な操作を取得します。 |

### <a name="microsofteventhub"></a>Microsoft.EventHub

Azure サービス:[Event Hubs](../event-hubs/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.EventHub/checkNamespaceAvailability/action | 特定のサブスクリプションで名前空間を使用できるかどうかを確認します。 この API は非推奨です。代わりに CheckNameAvailability を使用してください。 |
> | Microsoft.EventHub/checkNameAvailability/action | 特定のサブスクリプションで名前空間を使用できるかどうかを確認します。 |
> | Microsoft.EventHub/register/action | EventHub リソース プロバイダーにサブスクリプションを登録し、EventHub リソースを作成できるようにします。 |
> | Microsoft.EventHub/unregister/action | EventHub リソース プロバイダーに登録します。 |
> | Microsoft.EventHub/availableClusterRegions/read | Azure リージョンで事前プロビジョニングされた利用可能なクラスターを一覧表示するための読み取り操作 |
> | Microsoft.EventHub/clusters/read | クラスター リソースの説明を取得します。 |
> | Microsoft.EventHub/clusters/write | 既存のクラスター リソースを削除または変更します。 |
> | Microsoft.EventHub/clusters/delete | 既存のクラスター リソースを削除します。 |
> | Microsoft.EventHub/clusters/namespaces/read | クラスター内の名前空間の名前空間 Azure Resource Manager ID を一覧表示します。 |
> | Microsoft.EventHub/clusters/operationresults/read | 非同期のクラスター操作の状態を取得します。 |
> | Microsoft.EventHub/clusters/providers/Microsoft.Insights/metricDefinitions/read | クラスターのメトリック リソースの説明一覧を取得します。 |
> | Microsoft.EventHub/locations/deleteVirtualNetworkOrSubnets/action | 指定した VNet について、 EventHub リソース プロバイダー内の VNet ルールを削除します |
> | Microsoft.EventHub/namespaces/write | 名前空間リソースを作成し、そのプロパティを更新します。 名前空間のタグとキャパシティは、更新できるプロパティです。 |
> | Microsoft.EventHub/namespaces/read | 名前空間リソースの説明の一覧を取得します。 |
> | Microsoft.EventHub/namespaces/Delete | 名前空間リソースを削除します。 |
> | Microsoft.EventHub/namespaces/authorizationRules/action | 名前空間の承認規則を更新します。 この API は非推奨です。 名前空間の承認規則を更新するには、代わりに PUT 呼び出しを使用してください。 この操作は、API バージョン 2017-04-01 ではサポートされていません。 |
> | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | ACS 名前空間を削除します。 |
> | Microsoft.EventHub/namespaces/privateEndpointConnectionsApproval/action | プライベート エンドポイント接続を承認します |
> | Microsoft.EventHub/namespaces/authorizationRules/read | 名前空間の承認規則の説明の一覧を取得します。 |
> | Microsoft.EventHub/namespaces/authorizationRules/write | 名前空間レベルの承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権、プライマリ キー、セカンダリ キーを更新できます。 |
> | Microsoft.EventHub/namespaces/authorizationRules/delete | 名前空間の承認規則を削除します。 名前空間の既定の承認規則は削除できません。  |
> | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | 名前空間への接続文字列を取得します。 |
> | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | リソースのプライマリ キーまたはセカンダリ キーを再生成します。 |
> | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | 指定されたサブスクリプションで名前空間別名を使用できるかどうかを確認します。 |
> | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | 名前空間に関連付けられているディザスター リカバリーの構成を作成または更新します。 |
> | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | 名前空間に関連付けられているディザスター リカバリーの構成を取得します。 |
> | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | 名前空間に関連付けられているディザスター リカバリー構成を削除します。 この操作は、プライマリ名前空間からのみ呼び出すことができます。 |
> | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | ディザスター リカバリーを無効にし、プライマリ名前空間からセカンダリ名前空間への変更の複製を停止します。 |
> | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | geo DR フェールオーバーを呼び出し、セカンダリ名前空間を指すように名前空間の別名を再構成します。 |
> | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | ディザスター リカバリーのプライマリ名前空間の承認規則を取得します。 |
> | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | ディザスター リカバリーのプライマリ名前空間の承認規則キーを取得します。 |
> | Microsoft.EventHub/namespaces/eventhubs/write | EventHub のプロパティを作成または更新します。 |
> | Microsoft.EventHub/namespaces/eventhubs/read | EventHub リソースの説明の一覧を取得します。 |
> | Microsoft.EventHub/namespaces/eventhubs/Delete | EventHub リソースを削除する操作。 |
> | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/action | EventHub を更新する操作です。 この操作は、API バージョン 2017-04-01 ではサポートされていません。 承認規則。 承認規則を更新するには、PUT 呼び出しを使用してください。 |
> | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/read |  EventHub の承認規則の一覧を取得します。 |
> | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/write | EventHub の承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権を更新できます。 |
> | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/delete | EventHub の承認規則を削除する操作。 |
> | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/listkeys/action | EventHub への接続文字列を取得します。 |
> | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | リソースのプライマリ キーまたはセカンダリ キーを再生成します。 |
> | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | ConsumerGroup のプロパティを作成または更新します。 |
> | Microsoft.EventHub/namespaces/eventHubs/consumergroups/read | ConsumerGroup リソースの説明の一覧を取得します。 |
> | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | ConsumerGroup リソースを削除する操作。 |
> | Microsoft.EventHub/namespaces/ipFilterRules/read | IP フィルター リソースを取得します |
> | Microsoft.EventHub/namespaces/ipFilterRules/write | IP フィルター リソースを作成します |
> | Microsoft.EventHub/namespaces/ipFilterRules/delete | IP フィルター リソースを削除します |
> | Microsoft.EventHub/namespaces/messagingPlan/read | 名前空間のメッセージング プランを取得します。<br>この API は非推奨です。<br>より新しい API バージョンでは、MessagingPlan リソースにより公開されるプロパティは (親) 名前空間リソースに移動されています。<br>この操作は、API バージョン 2017-04-01 ではサポートされていません。 |
> | Microsoft.EventHub/namespaces/messagingPlan/write | 名前空間のメッセージング プランを更新します。<br>この API は非推奨です。<br>より新しい API バージョンでは、MessagingPlan リソースにより公開されるプロパティは (親) 名前空間リソースに移動されています。<br>この操作は、API バージョン 2017-04-01 ではサポートされていません。 |
> | Microsoft.EventHub/namespaces/networkruleset/read | NetworkRuleSet リソースを取得します |
> | Microsoft.EventHub/namespaces/networkruleset/write | VNET ルール リソースを作成します |
> | Microsoft.EventHub/namespaces/networkruleset/delete | VNET ルール リソースを削除します |
> | Microsoft.EventHub/namespaces/networkrulesets/read | NetworkRuleSet リソースを取得します |
> | Microsoft.EventHub/namespaces/networkrulesets/write | VNET ルール リソースを作成します |
> | Microsoft.EventHub/namespaces/networkrulesets/delete | VNET ルール リソースを削除します |
> | Microsoft.EventHub/namespaces/operationresults/read | 名前空間の操作の状態を取得します。 |
> | Microsoft.EventHub/namespaces/privateEndpointConnectionProxies/validate/action | プライベート エンドポイント接続プロキシを検証します |
> | Microsoft.EventHub/namespaces/privateEndpointConnectionProxies/read | プライベート エンドポイント接続プロキシを取得します |
> | Microsoft.EventHub/namespaces/privateEndpointConnectionProxies/write | プライベート エンドポイント接続プロキシを作成します |
> | Microsoft.EventHub/namespaces/privateEndpointConnectionProxies/delete | プライベート エンドポイント接続プロキシを削除します |
> | Microsoft.EventHub/namespaces/privateEndpointConnectionProxies/operationstatus/read | 非同期のプライベート エンドポイント操作の状態を取得します |
> | Microsoft.EventHub/namespaces/privateEndpointConnections/read | プライベート エンドポイント接続を取得します |
> | Microsoft.EventHub/namespaces/privateEndpointConnections/write | プライベート エンドポイント接続を作成または更新します |
> | Microsoft.EventHub/namespaces/privateEndpointConnections/delete | プライベート エンドポイント接続を削除します |
> | Microsoft.EventHub/namespaces/privateEndpointConnections/operationstatus/read | 非同期のプライベート エンドポイント操作の状態を取得します |
> | Microsoft.EventHub/namespaces/privateLinkResources/read | プライベート エンドポイント接続をサポートするリソースの種類を取得します |
> | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | 名前空間の診断設定リソースの説明の一覧を取得します。 |
> | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | 名前空間の診断設定リソースの説明の一覧を取得します。 |
> | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/logDefinitions/read | 名前空間のログ リソースの説明の一覧を取得します。 |
> | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/metricDefinitions/read | 名前空間のメトリック リソースの説明の一覧を取得します。 |
> | Microsoft.EventHub/namespaces/schemagroups/write | SchemaGroup のプロパティを作成または更新します。 |
> | Microsoft.EventHub/namespaces/schemagroups/read | SchemaGroup リソースの説明の一覧を取得します |
> | Microsoft.EventHub/namespaces/schemagroups/delete | SchemaGroup リソースを削除する操作 |
> | Microsoft.EventHub/namespaces/virtualNetworkRules/read | VNET ルール リソースを取得します |
> | Microsoft.EventHub/namespaces/virtualNetworkRules/write | VNET ルール リソースを作成します |
> | Microsoft.EventHub/namespaces/virtualNetworkRules/delete | VNET ルール リソースを削除します |
> | Microsoft.EventHub/operations/read | 操作を取得します。 |
> | Microsoft.EventHub/sku/read | SKU リソースの説明の一覧を取得します。 |
> | Microsoft.EventHub/sku/regions/read | SkuRegions リソースの説明の一覧を取得します。 |
> | **DataAction** | **説明** |
> | Microsoft.EventHub/namespaces/messages/send/action | メッセージを送信する |
> | Microsoft.EventHub/namespaces/messages/receive/action | メッセージを受信する |
> | Microsoft.EventHub/namespaces/schemas/read | スキーマを取得する |
> | Microsoft.EventHub/namespaces/schemas/write | スキーマを書き込む |
> | Microsoft.EventHub/namespaces/schemas/delete | スキーマを削除する |

### <a name="microsofthdinsight"></a>Microsoft.HDInsight

Azure サービス:[HDInsight](../hdinsight/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.HDInsight/register/action | HDInsight リソース プロバイダーをサブスクリプションに登録します |
> | Microsoft.HDInsight/unregister/action | サブスクリプションの HDInsight リソース プロバイダーを登録解除します |
> | Microsoft.HDInsight/clusters/write | HDInsight クラスターを作成または更新します。 |
> | Microsoft.HDInsight/clusters/read | HDInsight クラスターの詳細を取得します。 |
> | Microsoft.HDInsight/clusters/delete | HDInsight クラスターを削除します。 |
> | Microsoft.HDInsight/clusters/changerdpsetting/action | HDInsight クラスターの RDP 設定を変更します。 |
> | Microsoft.HDInsight/clusters/getGatewaySettings/action | HDInsight クラスター向けのアプリケーションを取得します |
> | Microsoft.HDInsight/clusters/updateGatewaySettings/action | HDInsight クラスターのゲートウェイ設定を更新します |
> | Microsoft.HDInsight/clusters/configurations/action | HDInsight クラスターの構成を取得します。 |
> | Microsoft.HDInsight/clusters/applications/read | HDInsight クラスター向けのアプリケーションを取得します |
> | Microsoft.HDInsight/clusters/applications/write | HDInsight クラスター向けのアプリケーションを作成または更新します |
> | Microsoft.HDInsight/clusters/applications/delete | HDInsight クラスター向けのアプリケーションを削除します |
> | Microsoft.HDInsight/clusters/configurations/read | HDInsight クラスターの構成を取得します。 |
> | Microsoft.HDInsight/clusters/extensions/write | HDInsight クラスターのクラスター拡張機能を作成します |
> | Microsoft.HDInsight/clusters/extensions/read | HDInsight クラスターのクラスター拡張機能を取得します |
> | Microsoft.HDInsight/clusters/extensions/delete | HDInsight クラスターのクラスター拡張機能を削除します |
> | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/read | リソース HDInsight クラスターの診断設定を取得します。 |
> | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/write | リソース HDInsight クラスターの診断設定を作成または更新します。 |
> | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/metricDefinitions/read | HDInsight クラスターで使用可能なメトリックを取得します。 |
> | Microsoft.HDInsight/clusters/roles/resize/action | HDInsight クラスターのサイズを変更します。 |
> | Microsoft.HDInsight/locations/capabilities/read | サブスクリプション機能を取得します。 |
> | Microsoft.HDInsight/locations/checkNameAvailability/read | 名前を使用できるかどうかを確認します。 |

### <a name="microsoftkusto"></a>Microsoft.Kusto

Azure サービス:[Azure Data Explorer](/azure/data-explorer/)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Kusto/register/action | サブスクリプション登録アクション |
> | Microsoft.Kusto/Register/action | Kusto リソース プロバイダーのサブスクリプションを登録します。 |
> | Microsoft.Kusto/Unregister/action | Kusto リソース プロバイダーのサブスクリプションを登録解除します。 |
> | Microsoft.Kusto/Clusters/read | クラスター リソースを読み取ります。 |
> | Microsoft.Kusto/Clusters/write | クラスター リソースを書き込みます。 |
> | Microsoft.Kusto/Clusters/delete | クラスター リソースを削除します。 |
> | Microsoft.Kusto/Clusters/Start/action | クラスターを起動します。 |
> | Microsoft.Kusto/Clusters/Stop/action | クラスターを停止します。 |
> | Microsoft.Kusto/Clusters/Activate/action | クラスターを起動します。 |
> | Microsoft.Kusto/Clusters/Deactivate/action | クラスターを停止します。 |
> | Microsoft.Kusto/Clusters/CheckNameAvailability/action | クラスター名の可用性を確認します。 |
> | Microsoft.Kusto/Clusters/DetachFollowerDatabases/action | フォロワーのデータベースをデタッチします。 |
> | Microsoft.Kusto/Clusters/ListFollowerDatabases/action | フォロワーのデータベースを一覧表示します。 |
> | Microsoft.Kusto/Clusters/DiagnoseVirtualNetwork/action | サービスが依存している外部リソースのネットワーク接続状態を診断します。 |
> | Microsoft.Kusto/Clusters/ListLanguageExtensions/action | 言語拡張機能を一覧表示します。 |
> | Microsoft.Kusto/Clusters/AddLanguageExtensions/action | 言語拡張機能を追加します。 |
> | Microsoft.Kusto/Clusters/RemoveLanguageExtensions/action | 言語拡張機能を削除します。 |
> | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/read | アタッチされているデータベース構成リソースを読み取ります。 |
> | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/write | アタッチされているデータベース構成リソースを書き込みます。 |
> | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/delete | アタッチされているデータベース構成リソースを削除します。 |
> | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/write | スクリプト リソースを書き込みます。 |
> | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/delete | スクリプト リソースを削除します。 |
> | Microsoft.Kusto/Clusters/Databases/read | データベース リソースを読み取ります。 |
> | Microsoft.Kusto/Clusters/Databases/write | データベース リソースを書き込みます。 |
> | Microsoft.Kusto/Clusters/Databases/delete | データベース リソースを削除します。 |
> | Microsoft.Kusto/Clusters/Databases/ListPrincipals/action | データベース プリンシパルを一覧表示します。 |
> | Microsoft.Kusto/Clusters/Databases/AddPrincipals/action | データベース プリンシパルを追加します。 |
> | Microsoft.Kusto/Clusters/Databases/RemovePrincipals/action | データベース プリンシパルを削除します。 |
> | Microsoft.Kusto/Clusters/Databases/DataConnectionValidation/action | データベースのデータ接続を検証します。 |
> | Microsoft.Kusto/Clusters/Databases/CheckNameAvailability/action | 指定した種類の名前を使用できるかどうかを確認します。 |
> | Microsoft.Kusto/Clusters/Databases/EventHubConnectionValidation/action | データベースのイベント ハブ接続を検証します。 |
> | Microsoft.Kusto/Clusters/Databases/DataConnections/read | データ接続リソースを読み取ります。 |
> | Microsoft.Kusto/Clusters/Databases/DataConnections/write | データ接続リソースを書き込みます。 |
> | Microsoft.Kusto/Clusters/Databases/DataConnections/delete | データ接続リソースを削除します。 |
> | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | イベント ハブの接続リソースを読み取ります。 |
> | Microsoft.Kusto/Clusters/Databases/EventHubConnections/write | イベント ハブの接続リソースを書き込みます。 |
> | Microsoft.Kusto/Clusters/Databases/EventHubConnections/delete | イベント ハブの接続リソースを削除します。 |
> | Microsoft.Kusto/Clusters/Databases/PrincipalAssignments/read | データベース プリンシパルの割り当てリソースを読み取ります。 |
> | Microsoft.Kusto/Clusters/Databases/PrincipalAssignments/write | データベース プリンシパルの割り当てリソースを書き込みます。 |
> | Microsoft.Kusto/Clusters/Databases/PrincipalAssignments/delete | データベース プリンシパルの割り当てリソースを削除します。 |
> | Microsoft.Kusto/Clusters/Databases/Scripts/read | アタッチされているデータベース構成リソースを読み取ります。 |
> | Microsoft.Kusto/Clusters/DataConnections/read | クラスターのデータ接続リソースが読み取られます。 |
> | Microsoft.Kusto/Clusters/DataConnections/write | クラスターのデータ接続リソースが書き込まれます。 |
> | Microsoft.Kusto/Clusters/DataConnections/delete | クラスターのデータ接続リソースが削除されます。 |
> | Microsoft.Kusto/Clusters/PrincipalAssignments/read | クラスター プリンシパルの割り当てリソースを読み取ります。 |
> | Microsoft.Kusto/Clusters/PrincipalAssignments/write | クラスター プリンシパルの割り当てリソースを書き込みます。 |
> | Microsoft.Kusto/Clusters/PrincipalAssignments/delete | クラスター プリンシパルの割り当てリソースを削除します。 |
> | Microsoft.Kusto/Clusters/SKUs/read | クラスター SKU リソースを読み取ります。 |
> | Microsoft.Kusto/Locations/CheckNameAvailability/action | リソース名の可用性を確認します。 |
> | Microsoft.Kusto/Locations/GetNetworkPolicies/action | ネットワーク インテント ポリシーを取得します |
> | Microsoft.Kusto/locations/operationresults/read | 操作のリソースを読み取ります |
> | Microsoft.Kusto/Operations/read | 操作のリソースを読み取ります |
> | Microsoft.Kusto/SKUs/read | SKU リソースを読み取ります。 |

### <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

Azure サービス:[Power BI Embedded](/azure/power-bi-embedded/)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.PowerBIDedicated/register/action | Power BI 専用リソース プロバイダーを登録します。 |
> | Microsoft.PowerBIDedicated/autoScaleVCores/read | 指定された Power BI 自動スケール V-Core の情報を取得します。 |
> | Microsoft.PowerBIDedicated/autoScaleVCores/write | 指定された Power BI 自動スケール V-Core を作成または更新します。 |
> | Microsoft.PowerBIDedicated/autoScaleVCores/delete | Power BI 自動スケール V-Core を削除します。 |
> | Microsoft.PowerBIDedicated/capacities/read | 指定された Power BI 容量の情報を取得します。 |
> | Microsoft.PowerBIDedicated/capacities/write | 指定された Power BI 容量を作成または更新します。 |
> | Microsoft.PowerBIDedicated/capacities/delete | Power BI 容量を削除します。 |
> | Microsoft.PowerBIDedicated/capacities/suspend/action | 容量を一時停止します。 |
> | Microsoft.PowerBIDedicated/capacities/resume/action | 容量を再開します。 |
> | Microsoft.PowerBIDedicated/capacities/skus/read | 容量の使用可能な SKU 情報を取得します。 |
> | Microsoft.PowerBIDedicated/locations/checkNameAvailability/action | 指定された Power BI 容量の名前が有効であり使用されていないことを確認します。 |
> | Microsoft.PowerBIDedicated/locations/operationresults/read | 指定された操作の結果の情報を取得します。 |
> | Microsoft.PowerBIDedicated/locations/operationstatuses/read | 指定された操作の状態の情報を取得します。 |
> | Microsoft.PowerBIDedicated/operations/read | 操作の情報を取得します。 |
> | Microsoft.PowerBIDedicated/skus/read | SKU の情報を取得します。 |

### <a name="microsoftpurview"></a>Microsoft.Purview

Azure サービス:[Azure Purview](../purview/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Purview/register/action | Microsoft Purview プロバイダーのサブスクリプションを登録します。 |
> | Microsoft.Purview/unregister/action | Microsoft Purview プロバイダーのサブスクリプションを登録解除します。 |
> | Microsoft.Purview/setDefaultAccount/action | スコープの既定のアカウントを設定します。 |
> | Microsoft.Purview/accounts/read | Microsoft Purview プロバイダーのアカウント リソースを読み取ります。 |
> | Microsoft.Purview/accounts/write | Microsoft Purview プロバイダーのアカウント リソースを書き込みます。 |
> | Microsoft.Purview/accounts/delete | Microsoft Purview プロバイダーのアカウント リソースを削除します。 |
> | Microsoft.Purview/accounts/move/action | Microsoft Purview プロバイダーのアカウント リソースを移動します。 |
> | Microsoft.Purview/accounts/PrivateEndpointConnectionsApproval/action | プライベート エンドポイント接続を承認します。 |
> | Microsoft.Purview/accounts/privateEndpointConnectionProxies/read | アカウントのプライベート エンドポイント接続プロキシを読み取ります。 |
> | Microsoft.Purview/accounts/privateEndpointConnectionProxies/write | アカウントのプライベート エンドポイント接続プロキシを書き込みます。 |
> | Microsoft.Purview/accounts/privateEndpointConnectionProxies/delete | アカウントのプライベート エンドポイント接続プロキシを削除します。 |
> | Microsoft.Purview/accounts/privateEndpointConnectionProxies/validate/action | アカウントのプライベート エンドポイント接続プロキシを検証します。 |
> | Microsoft.Purview/accounts/privateEndpointConnectionProxies/operationResults/read | プライベート エンドポイント接続プロキシの非同期操作を監視します。 |
> | Microsoft.Purview/accounts/privateEndpointConnections/read | プライベート エンドポイント接続を読み取ります。 |
> | Microsoft.Purview/accounts/privateEndpointConnections/write | プライベート エンドポイント接続を作成または更新します。 |
> | Microsoft.Purview/accounts/privateEndpointConnections/delete | プライベート エンドポイント接続を削除します。 |
> | Microsoft.Purview/getDefaultAccount/read | スコープの既定のアカウントを取得します。 |
> | Microsoft.Purview/locations/operationResults/read | 非同期操作を監視します。 |
> | Microsoft.Purview/operations/read | Microsoft Purview プロバイダーの使用可能なすべての操作を読み取ります。 |
> | **DataAction** | **説明** |
> | Microsoft.Purview/accounts/data/read | データ オブジェクトを読み取ります。 |
> | Microsoft.Purview/accounts/data/write | データ オブジェクトを作成、更新、および削除します。 |
> | Microsoft.Purview/accounts/scan/read | データ ソースとスキャンを読み取ります。 |
> | Microsoft.Purview/accounts/scan/write | データソースの作成、更新、削除、およびスキャンの管理を行います。 |

### <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

Azure サービス:[Stream Analytics](../stream-analytics/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.StreamAnalytics/Register/action | Stream Analytics リソース プロバイダーにサブスクリプションを登録します。 |
> | Microsoft.StreamAnalytics/locations/quotas/Read | Stream Analytics サブスクリプションのクォータを読み取ります。 |
> | Microsoft.StreamAnalytics/operations/Read | Stream Analytics の操作を読み取ります。 |
> | Microsoft.StreamAnalytics/streamingjobs/Delete | Stream Analytics ジョブを削除します。 |
> | Microsoft.StreamAnalytics/streamingjobs/PublishEdgePackage/action | Stream Analytics ジョブのエッジ パッケージを発行します |
> | Microsoft.StreamAnalytics/streamingjobs/Read | Stream Analytics ジョブを読み取ります。 |
> | Microsoft.StreamAnalytics/streamingjobs/Scale/action | Stream Analytics ジョブをスケーリングします |
> | Microsoft.StreamAnalytics/streamingjobs/Start/action | Stream Analytics ジョブを開始します。 |
> | Microsoft.StreamAnalytics/streamingjobs/Stop/action | Stream Analytics ジョブを停止します。 |
> | Microsoft.StreamAnalytics/streamingjobs/Write | Stream Analytics ジョブを書き込みます。 |
> | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | Stream Analytics ジョブ関数を削除します。 |
> | Microsoft.StreamAnalytics/streamingjobs/functions/Read | Stream Analytics ジョブ関数を読み取ります。 |
> | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | Stream Analytics ジョブ関数の既定の定義を取得します。 |
> | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | Stream Analytics ジョブ関数をテストします。 |
> | Microsoft.StreamAnalytics/streamingjobs/functions/Write | Stream Analytics ジョブ関数を書き込みます。 |
> | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | Stream Analytics ジョブ関数の操作の結果を読み取ります。 |
> | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | Stream Analytics ジョブ入力を削除します。 |
> | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | Stream Analytics ジョブ入力を読み取ります。 |
> | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | Stream Analytics ジョブ入力をサンプリングします。 |
> | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | Stream Analytics ジョブ入力をテストします。 |
> | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | Stream Analytics ジョブ入力を書き込みます。 |
> | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | Stream Analytics ジョブ入力の操作の結果を読み取ります。 |
> | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | メトリック定義を読み取ります。 |
> | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | Stream Analytics ジョブの操作の結果を読み取ります。 |
> | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | Stream Analytics ジョブ出力を削除します。 |
> | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | Stream Analytics ジョブ出力を読み取ります。 |
> | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | Stream Analytics ジョブ出力をテストします。 |
> | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | Stream Analytics ジョブ出力を書き込みます。 |
> | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | Stream Analytics ジョブ出力の操作の結果を読み取ります。 |
> | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | 診断設定を読み取ります。 |
> | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | 診断設定を書き込みます。 |
> | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | streamingjobs の利用可能なログを取得します。 |
> | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | streamingjobs の利用可能なメトリックを取得します。 |
> | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | Stream Analytics ジョブ変換を削除します。 |
> | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | Stream Analytics ジョブ変換を読み取ります。 |
> | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | Stream Analytics ジョブ変換を書き込みます。 |

### <a name="microsoftsynapse"></a>Microsoft.Synapse

Azure サービス:[Azure Synapse Analytics](../synapse-analytics/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Synapse/checkNameAvailability/action | ワークスペース名を使用できるかどうかを調べます。 |
> | Microsoft.Synapse/register/action | Azure Synapse Analytics (ワークスペース) のリソース プロバイダーを登録し、ワークスペースを作成できるようにします。 |
> | Microsoft.Synapse/unregister/action | Azure Synapse Analytics (ワークスペース) のリソース プロバイダーの登録を解除し、ワークスペースを作成できないようにします。 |
> | Microsoft.Synapse/locations/operationResults/read | 非同期操作の結果を読み取ります。 |
> | Microsoft.Synapse/locations/operationStatuses/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.Synapse/operations/read | Azure Synapse Analytics リソース プロバイダーから使用可能な操作を読み取ります。 |
> | Microsoft.Synapse/privateLinkHubs/write | PrivateLinkHubs を作成します。 |
> | Microsoft.Synapse/privateLinkHubs/read | PrivateLinkHubs を読み取ります。 |
> | Microsoft.Synapse/privateLinkHubs/delete | PrivateLinkHubs を削除します。 |
> | Microsoft.Synapse/privateLinkHubs/privateEndpointConnections/write | PrivateLinkHub のプライベート エンドポイント接続を作成または更新します |
> | Microsoft.Synapse/privateLinkHubs/privateEndpointConnections/read | PrivateLinkHub のプライベート エンドポイント接続を読み取ります |
> | Microsoft.Synapse/privateLinkHubs/privateEndpointConnections/delete | PrivateLinkHub のプライベート エンドポイント接続を削除します |
> | Microsoft.Synapse/privateLinkHubs/privateLinkResources/read | Private Link リソースの一覧を取得します |
> | Microsoft.Synapse/resourceGroups/operationStatuses/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.Synapse/workspaces/replaceAllIpFirewallRules/action | ワークスペースのすべての IP ファイアウォール規則を置き換えます。 |
> | Microsoft.Synapse/workspaces/write | ワークスペースを作成または更新します。 |
> | Microsoft.Synapse/workspaces/read | ワークスペースを読み取ります。 |
> | Microsoft.Synapse/workspaces/delete | ワークスペースを削除します。 |
> | Microsoft.Synapse/workspaces/checkDefaultStorageAccountStatus/action | 既定のストレージ アカウントの状態を調べます。 |
> | Microsoft.Synapse/workspaces/administrators/write | ワークスペースに Active Directory 管理者を設定します |
> | Microsoft.Synapse/workspaces/administrators/read | ワークスペース Active Directory 管理者を取得します |
> | Microsoft.Synapse/workspaces/administrators/delete | ワークスペース Active Directory 管理者を削除します |
> | Microsoft.Synapse/workspaces/auditingSettings/write | SQL サーバーの監査設定を作成または更新します。 |
> | Microsoft.Synapse/workspaces/auditingSettings/read | 既定の SQL サーバーの監査設定を読み取ります。 |
> | Microsoft.Synapse/workspaces/bigDataPools/write | Spark プールを作成または更新します。 |
> | Microsoft.Synapse/workspaces/bigDataPools/read | Spark プールを読み取ります。 |
> | Microsoft.Synapse/workspaces/bigDataPools/delete | Spark プールを削除します。 |
> | Microsoft.Synapse/workspaces/extendedAuditingSettings/write | SQL サーバーの拡張監査設定を作成または更新します。 |
> | Microsoft.Synapse/workspaces/extendedAuditingSettings/read | 既定の SQL サーバーの拡張監査設定を読み取ります。 |
> | Microsoft.Synapse/workspaces/firewallRules/write | IP ファイアウォール規則を作成または更新します。 |
> | Microsoft.Synapse/workspaces/firewallRules/read | IP ファイアウォール規則を読み取ります |
> | Microsoft.Synapse/workspaces/firewallRules/delete | IP ファイアウォール規則を削除します。 |
> | Microsoft.Synapse/workspaces/integrationRuntimes/read | 統合ランタイムを取得します。 |
> | Microsoft.Synapse/workspaces/integrationruntimes/write | 統合ランタイムを作成または更新します。 |
> | Microsoft.Synapse/workspaces/integrationRuntimes/delete | 統合ランタイムを削除します |
> | Microsoft.Synapse/workspaces/integrationRuntimes/getStatus/action | 統合ランタイムの状態を取得します |
> | Microsoft.Synapse/workspaces/integrationRuntimes/createExpressSHIRInstallLink/action | 統合ランタイムのインストール リンクを作成します |
> | Microsoft.Synapse/workspaces/integrationRuntimes/start/action | 統合ランタイムを開始します |
> | Microsoft.Synapse/workspaces/integrationRuntimes/stop/action | 統合ランタイムを停止します |
> | Microsoft.Synapse/workspaces/integrationRuntimes/getConnectionInfo/action | 統合ランタイムの接続情報を取得します |
> | Microsoft.Synapse/workspaces/integrationRuntimes/regenerateAuthKey/action | 統合ランタイムの認証キーを再生成します |
> | Microsoft.Synapse/workspaces/integrationRuntimes/listAuthKeys/action | 統合ランタイムの認証キーの一覧を表示します |
> | Microsoft.Synapse/workspaces/integrationRuntimes/removeNode/action | 統合ランタイムのノードを削除します |
> | Microsoft.Synapse/workspaces/integrationRuntimes/monitoringData/action | 統合ランタイムの監視データを取得します |
> | Microsoft.Synapse/workspaces/integrationRuntimes/syncCredentials/action | 統合ランタイムの資格情報を同期します |
> | Microsoft.Synapse/workspaces/integrationRuntimes/upgrade/action | 統合ランタイムをアップグレードします |
> | Microsoft.Synapse/workspaces/integrationRuntimes/removeLinks/action | 統合ランタイムのリンクを削除します |
> | Microsoft.Synapse/workspaces/integrationRuntimes/enableInteractiveQuery/action | 統合ランタイムでの対話型クエリを有効にします |
> | Microsoft.Synapse/workspaces/integrationRuntimes/disableInteractiveQuery/action | 統合ランタイムでの対話型クエリを無効にします |
> | Microsoft.Synapse/workspaces/integrationRuntimes/refreshObjectMetadata/action | 統合ランタイムでオブジェクト メタデータを更新します |
> | Microsoft.Synapse/workspaces/integrationRuntimes/getObjectMetadata/action | 統合ランタイムでオブジェクト メタデータを取得します |
> | Microsoft.Synapse/workspaces/integrationRuntimes/nodes/read | 統合ランタイム ノードを取得します。 |
> | Microsoft.Synapse/workspaces/integrationRuntimes/nodes/delete | 統合ランタイム ノードを削除します。 |
> | Microsoft.Synapse/workspaces/integrationRuntimes/nodes/write | 統合ランタイム ノードにパッチを適用します。 |
> | Microsoft.Synapse/workspaces/integrationRuntimes/nodes/ipAddress/action | 統合ランタイムの IP アドレスを取得します |
> | Microsoft.Synapse/workspaces/keys/write | ワークスペース キーを作成または更新します |
> | Microsoft.Synapse/workspaces/keys/read | ワークスペース キーの定義を読み取ります。 |
> | Microsoft.Synapse/workspaces/keys/delete | ワークスペース キーを削除します。 |
> | Microsoft.Synapse/workspaces/libraries/read | ライブラリ成果物を読み取ります |
> | Microsoft.Synapse/workspaces/managedIdentitySqlControlSettings/write | ワークスペースでマネージド ID の SQL コントロールの設定を更新します |
> | Microsoft.Synapse/workspaces/managedIdentitySqlControlSettings/read | マネージド ID の SQL コントロールの設定を取得します |
> | Microsoft.Synapse/workspaces/operationResults/read | 非同期操作の結果を読み取ります。 |
> | Microsoft.Synapse/workspaces/operationStatuses/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.Synapse/workspaces/privateEndpointConnections/write | プライベート エンドポイント接続を作成または更新します |
> | Microsoft.Synapse/workspaces/privateEndpointConnections/read | プライベート エンドポイント接続を読み取ります |
> | Microsoft.Synapse/workspaces/privateEndpointConnections/delete | プライベート エンドポイント接続を削除します |
> | Microsoft.Synapse/workspaces/privateLinkResources/read | Private Link リソースの一覧を取得します |
> | Microsoft.Synapse/workspaces/recoverableSqlpools/read | SQL Analytics プールの geo バックアップを表すリソースである、復旧可能な SQL Analytics プールを取得します |
> | Microsoft.Synapse/workspaces/restorableDroppedSqlPools/read | 復元可能な削除された SQL プールを取得します |
> | Microsoft.Synapse/workspaces/scopePools/write | スコープ プールを作成または更新します。 |
> | Microsoft.Synapse/workspaces/scopePools/read | スコープ プールを読み取ります。 |
> | Microsoft.Synapse/workspaces/scopePools/delete | スコープ プールを削除します。 |
> | Microsoft.Synapse/workspaces/securityAlertPolicies/write | SQL サーバーのセキュリティ アラート ポリシーを作成または更新します。 |
> | Microsoft.Synapse/workspaces/securityAlertPolicies/read | 既定の SQL サーバーのセキュリティ アラート ポリシーを読み取ります。 |
> | Microsoft.Synapse/workspaces/sqlAdministrators/write | ワークスペースに Active Directory 管理者を設定します |
> | Microsoft.Synapse/workspaces/sqlAdministrators/read | ワークスペース Active Directory 管理者を取得します |
> | Microsoft.Synapse/workspaces/sqlAdministrators/delete | ワークスペース Active Directory 管理者を削除します |
> | Microsoft.Synapse/workspaces/sqlPools/write | SQL Analytics プールを作成または更新します。 |
> | Microsoft.Synapse/workspaces/sqlPools/read | SQL Analytics プールを読み取ります。 |
> | Microsoft.Synapse/workspaces/sqlPools/delete | SQL Analytics プールを削除します。 |
> | Microsoft.Synapse/workspaces/sqlPools/pause/action | SQL Analytics プールを一時停止します。 |
> | Microsoft.Synapse/workspaces/sqlPools/resume/action | SQL Analytics プールを再開します。 |
> | Microsoft.Synapse/workspaces/sqlPools/restorePoints/action | SQL Analytics プールの復元ポイントを作成します。 |
> | Microsoft.Synapse/workspaces/sqlPools/move/action | SQL Analytics プールの名前を変更します。 |
> | Microsoft.Synapse/workspaces/sqlPools/auditingSettings/read | SQL Analytics プールの監査設定を読み取ります。 |
> | Microsoft.Synapse/workspaces/sqlPools/auditingSettings/write | SQL Analytics プールの監査設定を作成または更新します。 |
> | Microsoft.Synapse/workspaces/sqlPools/auditRecords/read | SQL プールの BLOB 監査レコードを取得します |
> | Microsoft.Synapse/workspaces/sqlPools/columns/read | SQL Analytics プールの列の一覧を取得します |
> | Microsoft.Synapse/workspaces/sqlPools/connectionPolicies/read | SQL Analytics プールの接続ポリシーを読み取ります。 |
> | Microsoft.Synapse/workspaces/sqlPools/currentSensitivityLabels/read | SQL Analytics プールの現在の秘密度ラベルを読み取ります。 |
> | Microsoft.Synapse/workspaces/sqlPools/currentSensitivityLabels/write | 現在の秘密度ラベルをバッチ更新します |
> | Microsoft.Synapse/workspaces/sqlPools/dataMaskingPolicies/read | SQL Analytics プールのデータ マスキング ポリシーの一覧を取得します。 |
> | Microsoft.Synapse/workspaces/sqlPools/dataMaskingPolicies/write | SQL Analytics プールのデータ マスキング ポリシーを作成または更新します |
> | Microsoft.Synapse/workspaces/sqlPools/dataMaskingPolicies/rules/read | SQL Analytics プールのデータ マスキング ルールの一覧を取得します。 |
> | Microsoft.Synapse/workspaces/sqlPools/dataMaskingPolicies/rules/write | SQL Analytics プールのデータ マスキング ルールを作成または更新します。 |
> | Microsoft.Synapse/workspaces/sqlPools/dataWarehouseQueries/read | SQL Analytics プールのクエリを読み取ります。 |
> | Microsoft.Synapse/workspaces/sqlPools/dataWarehouseQueries/dataWarehouseQuerySteps/read | SQL Analytics プールのクエリ手順を読み取ります。 |
> | Microsoft.Synapse/workspaces/sqlPools/dataWarehouseQueries/Steps/read | SQL Analytics プールのクエリ手順を読み取ります。 |
> | Microsoft.Synapse/workspaces/sqlPools/dataWarehouseUserActivities/read | SQL Analytics プールのユーザー アクティビティを読み取ります。 |
> | Microsoft.Synapse/workspaces/sqlPools/extendedAuditingSettings/read | SQL Analytics プールの拡張監査設定を読み取ります。 |
> | Microsoft.Synapse/workspaces/sqlPools/extendedAuditingSettings/write | SQL Analytics プールの拡張監査設定を作成または更新します。 |
> | Microsoft.Synapse/workspaces/sqlPools/extensions/read | SQL Analytics プールの拡張機能を取得します |
> | Microsoft.Synapse/workspaces/sqlPools/extensions/write | 指定した SQL Analytics プールの拡張機能を変更します |
> | Microsoft.Synapse/workspaces/sqlPools/geoBackupPolicies/read | SQL Analytics プールの geo バックアップ ポリシーを読み取ります。 |
> | Microsoft.Synapse/workspaces/sqlPools/maintenanceWindowOptions/read | SQL Analytics プールのメンテナンス期間のオプションを読み取ります。 |
> | Microsoft.Synapse/workspaces/sqlPools/maintenanceWindows/read | SQL Analytics プールのメンテナンス期間を読み取ります。 |
> | Microsoft.Synapse/workspaces/sqlPools/maintenanceWindows/write | SQL Analytics プールのメンテナンス期間を読み取ります。 |
> | Microsoft.Synapse/workspaces/sqlPools/metadataSync/write | SQL Analytics プールのメタデータ同期の構成を作成または更新します |
> | Microsoft.Synapse/workspaces/sqlPools/metadataSync/read | SQL Analytics プールのメタデータ同期の構成を読み取ります |
> | Microsoft.Synapse/workspaces/sqlPools/operationResults/read | 非同期操作の結果を読み取ります。 |
> | Microsoft.Synapse/workspaces/sqlPools/operations/read | SQL Analytics プールの操作を読み取ります。 |
> | Microsoft.Synapse/workspaces/sqlPools/operationStatuses/read | 非同期操作の結果を読み取ります。 |
> | Microsoft.Synapse/workspaces/sqlPools/recommendedSensitivityLabels/read | SQL Analytics プールの推奨される秘密度ラベルを読み取ります。 |
> | Microsoft.Synapse/workspaces/sqlPools/recommendedSensitivityLabels/write | 推薦される機密ラベルをバッチ更新します |
> | Microsoft.Synapse/workspaces/sqlPools/replicationLinks/read | SQL Analytics プールのレプリケーション リンクを読み取ります。 |
> | Microsoft.Synapse/workspaces/sqlPools/restorePoints/read | SQL Analytics プールの復元ポイントを読み取ります。 |
> | Microsoft.Synapse/workspaces/sqlPools/restorePoints/delete | 復元ポイントを削除します。 |
> | Microsoft.Synapse/workspaces/sqlPools/schemas/read | SQL Analytics プールのスキーマを読み取ります。 |
> | Microsoft.Synapse/workspaces/sqlPools/schemas/tables/read | SQL Analytics プールのスキーマ テーブルを読み取ります。 |
> | Microsoft.Synapse/workspaces/sqlPools/schemas/tables/columns/read | SQL Analytics プールのスキーマ テーブルの列を読み取ります。 |
> | Microsoft.Synapse/workspaces/sqlPools/schemas/tables/columns/sensitivityLabels/read | 指定した列の秘密度ラベルを取得します。 |
> | Microsoft.Synapse/workspaces/sqlPools/schemas/tables/columns/sensitivityLabels/enable/action | SQL Analytics プールの秘密度ラベルを有効にします。 |
> | Microsoft.Synapse/workspaces/sqlPools/schemas/tables/columns/sensitivityLabels/disable/action | SQL Analytics プールの秘密度ラベルを無効にします。 |
> | Microsoft.Synapse/workspaces/sqlPools/schemas/tables/columns/sensitivityLabels/write | SQL Analytics プールの秘密度ラベルを作成または更新します。 |
> | Microsoft.Synapse/workspaces/sqlPools/schemas/tables/columns/sensitivityLabels/delete | SQL Analytics プールの秘密度ラベルを削除します。 |
> | Microsoft.Synapse/workspaces/sqlPools/securityAlertPolicies/read | SQL Analytics プールの脅威検出ポリシーを読み取ります。 |
> | Microsoft.Synapse/workspaces/sqlPools/securityAlertPolicies/write | SQL Analytics プールの脅威検出ポリシーを作成または更新します。 |
> | Microsoft.Synapse/workspaces/sqlPools/sensitivityLabels/read | 指定した列の秘密度ラベルを取得します。 |
> | Microsoft.Synapse/workspaces/sqlPools/transparentDataEncryption/read | SQL Analytics プールの Transparent Data Encryption の構成を読み取ります。 |
> | Microsoft.Synapse/workspaces/sqlPools/transparentDataEncryption/write | SQL Analytics プールの Transparent Data Encryption の構成を作成または更新します。 |
> | Microsoft.Synapse/workspaces/sqlPools/transparentDataEncryption/operationResults/read | SQL Analytics プールの Transparent Data Encryption の構成操作の結果を読み取ります。 |
> | Microsoft.Synapse/workspaces/sqlPools/usages/read | SQL Analytics プールの使用状況を読み取ります。 |
> | Microsoft.Synapse/workspaces/sqlPools/vulnerabilityAssessments/read | SQL Analytics プールの脆弱性評価を読み取ります。 |
> | Microsoft.Synapse/workspaces/sqlPools/vulnerabilityAssessments/write | SQL プールの脆弱性評価を作成または更新します |
> | Microsoft.Synapse/workspaces/sqlPools/vulnerabilityAssessments/delete | SQL Analytics プールの脆弱性評価を削除します。 |
> | Microsoft.Synapse/workspaces/sqlPools/vulnerabilityAssessments/rules/baselines/read | SQL Analytics プールの脆弱性評価ルールのベースラインを取得します。 |
> | Microsoft.Synapse/workspaces/sqlPools/vulnerabilityAssessments/rules/baselines/write | SQL Analytics プールの脆弱性評価ルールのベースラインを作成または更新します。 |
> | Microsoft.Synapse/workspaces/sqlPools/vulnerabilityAssessments/rules/baselines/delete | SQL Analytics プールの脆弱性評価ルールのベースラインを削除します。 |
> | Microsoft.Synapse/workspaces/sqlPools/vulnerabilityAssessments/scans/read | SQL Analytics プールの脆弱性評価スキャン レコードを読み取ります。 |
> | Microsoft.Synapse/workspaces/sqlPools/vulnerabilityAssessments/scans/initiateScan/action | SQL Analytics プールの脆弱性評価スキャン レコードを開始します。 |
> | Microsoft.Synapse/workspaces/sqlPools/vulnerabilityAssessments/scans/export/action | SQL Analytics プールの脆弱性評価スキャン レコードをエクスポートします。 |
> | Microsoft.Synapse/workspaces/sqlPools/workloadGroups/read | 選択した SQL プールのワークロード グループの一覧を表示します。 |
> | Microsoft.Synapse/workspaces/sqlPools/workloadGroups/write | 特定のワークロード グループのプロパティを設定します。 |
> | Microsoft.Synapse/workspaces/sqlPools/workloadGroups/delete | 特定のワークロード グループをドロップします。 |
> | Microsoft.Synapse/workspaces/sqlPools/workloadGroups/operationStatuses/read | SQL Analytics プールのワークロード グループの操作状態 |
> | Microsoft.Synapse/workspaces/sqlPools/workloadGroups/workloadClassifiers/read | 選択した SQL Analytics プールのワークロード分類子の一覧を表示します。 |
> | Microsoft.Synapse/workspaces/sqlPools/workloadGroups/workloadClassifiers/write | 特定のワークロード分類子のプロパティを設定します。 |
> | Microsoft.Synapse/workspaces/sqlPools/workloadGroups/workloadClassifiers/delete | 特定のワークロード分類子をドロップします。 |
> | Microsoft.Synapse/workspaces/sqlPools/workloadGroups/workloadClassifiers/operationResults/read | SQL Analytics プールのワークロード分類子の操作結果 |
> | Microsoft.Synapse/workspaces/sqlPools/workloadGroups/workloadClassifiers/operationStatuses/read | SQL Analytics プールのワークロード分類子の操作状態 |
> | Microsoft.Synapse/workspaces/sqlUsages/read | SQL Analytics プールで使用可能な使用制限を取得します |
> | Microsoft.Synapse/workspaces/vulnerabilityAssessments/write | SQL サーバーの脆弱性評価レポートを作成または更新します。 |
> | Microsoft.Synapse/workspaces/vulnerabilityAssessments/read | 既定の SQL Server の脆弱性評価レポートを読み取ります。 |
> | Microsoft.Synapse/workspaces/vulnerabilityAssessments/delete | SQL Server の脆弱性評価レポートを削除します。 |

## <a name="blockchain"></a>ブロックチェーン

### <a name="microsoftblockchain"></a>Microsoft.Blockchain

Azure サービス:[Azure Blockchain Service](../blockchain/workbench/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Blockchain/register/action | ブロックチェーン リソース プロバイダーのサブスクリプションを登録します。 |
> | Microsoft.Blockchain/blockchainMembers/read | 既存のブロックチェーン メンバーを取得または一覧表示します。 |
> | Microsoft.Blockchain/blockchainMembers/write | ブロックチェーン メンバーを作成または更新します。 |
> | Microsoft.Blockchain/blockchainMembers/delete | 既存のブロックチェーン メンバーを削除します。 |
> | Microsoft.Blockchain/blockchainMembers/listApiKeys/action | 既存のブロックチェーン メンバー API キーを取得または一覧表示します。 |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | 既存のブロックチェーン メンバーのトランザクション ノードを取得または一覧表示します。 |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/write | ブロックチェーン メンバーのトランザクション ノードを作成または更新します。 |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/delete | 既存のブロックチェーン メンバーのトランザクション ノードを削除します。 |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/listApiKeys/action | 既存のブロックチェーン メンバーのトランザクション ノード API キーを取得または一覧表示します。 |
> | Microsoft.Blockchain/cordaMembers/read | 既存のブロックチェーン Corda メンバーを取得または一覧表示します。 |
> | Microsoft.Blockchain/cordaMembers/write | ブロックチェーン Corda メンバーを作成または更新します。 |
> | Microsoft.Blockchain/cordaMembers/delete | 既存のブロックチェーン Corda メンバーを削除します。 |
> | Microsoft.Blockchain/locations/checkNameAvailability/action | リソース名が有効であり、使用されていないことを確認します。 |
> | Microsoft.Blockchain/locations/blockchainMemberOperationResults/read | ブロックチェーン メンバーの操作結果を取得します。 |
> | Microsoft.Blockchain/operations/read | Microsoft ブロックチェーン リソース プロバイダーのすべての操作を一覧表示します。 |
> | **DataAction** | **説明** |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | ブロックチェーン メンバーのトランザクション ノードに接続します。 |

## <a name="ai--machine-learning"></a>AI + 機械学習

### <a name="microsoftbotservice"></a>Microsoft.BotService

Azure サービス:[Azure Bot Service](/azure/bot-service/)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.BotService/checknameavailability/action | ボットの名前を使用できるかどうかが確認されます |
> | Microsoft.BotService/listauthserviceproviders/action | 認証サービス プロバイダーが一覧表示されます |
> | Microsoft.BotService/botServices/read | ボット サービスを読み取る |
> | Microsoft.BotService/botServices/write | ボット サービスを書き込む |
> | Microsoft.BotService/botServices/delete | ボット サービスを削除する |
> | Microsoft.BotService/botServices/channels/read | Bot Service チャネルが読み取られます |
> | Microsoft.BotService/botServices/channels/write | Bot Service チャネルが書き込まれます |
> | Microsoft.BotService/botServices/channels/delete | Bot Service チャネルが削除されます |
> | Microsoft.BotService/botServices/channels/listchannelwithkeys/action | シークレットが含まれる Botservice チャネルが一覧表示されます |
> | Microsoft.BotService/botServices/channels/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Microsoft.BotService/botServices/channels/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Microsoft.BotService/botServices/channels/providers/Microsoft.Insights/logDefinitions/read | &lt;リソースの名前&gt;に使用できるログを取得します |
> | Microsoft.BotService/botServices/channels/providers/Microsoft.Insights/metricDefinitions/read | リソースの診断設定を作成または更新します。 |
> | Microsoft.BotService/botServices/connections/read | Bot Service 接続が読み取られます |
> | Microsoft.BotService/botServices/connections/write | Bot Service 接続が書き込まれます |
> | Microsoft.BotService/botServices/connections/delete | Bot Service 接続が削除されます |
> | Microsoft.BotService/botServices/connections/listwithsecrets/write | Bot Service 接続リストが書き込まれます  |
> | Microsoft.BotService/botServices/connections/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Microsoft.BotService/botServices/connections/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Microsoft.BotService/botServices/connections/providers/Microsoft.Insights/logDefinitions/read | &lt;リソースの名前&gt;に使用できるログを取得します |
> | Microsoft.BotService/botServices/connections/providers/Microsoft.Insights/metricDefinitions/read | リソースの診断設定を作成または更新します。 |
> | Microsoft.BotService/botServices/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Microsoft.BotService/botServices/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Microsoft.BotService/botServices/providers/Microsoft.Insights/logDefinitions/read | &lt;リソースの名前&gt;に使用できるログを取得します |
> | Microsoft.BotService/botServices/providers/Microsoft.Insights/metricDefinitions/read | リソースの診断設定を作成または更新します。 |
> | Microsoft.BotService/checknameavailability/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Microsoft.BotService/checknameavailability/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Microsoft.BotService/checknameavailability/providers/Microsoft.Insights/logDefinitions/read | &lt;リソースの名前&gt;に使用できるログを取得します |
> | Microsoft.BotService/checknameavailability/providers/Microsoft.Insights/metricDefinitions/read | リソースの診断設定を作成または更新します。 |
> | Microsoft.BotService/listauthserviceproviders/providers/Microsoft.Insights/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Microsoft.BotService/listauthserviceproviders/providers/Microsoft.Insights/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Microsoft.BotService/listauthserviceproviders/providers/Microsoft.Insights/logDefinitions/read | &lt;リソースの名前&gt;に使用できるログを取得します |
> | Microsoft.BotService/listauthserviceproviders/providers/Microsoft.Insights/metricDefinitions/read | リソースの診断設定を作成または更新します。 |
> | Microsoft.BotService/locations/operationresults/read | 非同期操作の状態を読み取る |
> | Microsoft.BotService/Operations/read | すべてのリソースの種類に対して操作を読み取る |

### <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

Azure サービス:[Cognitive Services](../cognitive-services/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.CognitiveServices/register/action | サブスクリプション登録アクション |
> | Microsoft.CognitiveServices/register/action | Cognitive Services にサブスクリプションを登録します。 |
> | Microsoft.CognitiveServices/checkDomainAvailability/action | サブスクリプションで使用可能な SKU を読み取ります。 |
> | Microsoft.CognitiveServices/register/action | Cognitive Services にサブスクリプションを登録します。 |
> | Microsoft.CognitiveServices/register/action | Cognitive Services にサブスクリプションを登録します。 |
> | Microsoft.CognitiveServices/accounts/read | API アカウントを読み取ります。 |
> | Microsoft.CognitiveServices/accounts/write | API アカウントを書き込みます。 |
> | Microsoft.CognitiveServices/accounts/delete | API アカウントを削除します。 |
> | Microsoft.CognitiveServices/accounts/listKeys/action | キーを一覧表示します。 |
> | Microsoft.CognitiveServices/accounts/regenerateKey/action | キーを再生成します。 |
> | Microsoft.CognitiveServices/accounts/privateEndpointConnectionProxies/read | プライベート エンドポイントの接続を読み取ります。 |
> | Microsoft.CognitiveServices/accounts/privateEndpointConnectionProxies/write | プライベート エンドポイントの接続を書き込みます。 |
> | Microsoft.CognitiveServices/accounts/privateEndpointConnectionProxies/delete | プライベート エンドポイントの接続を削除します。 |
> | Microsoft.CognitiveServices/accounts/privateEndpointConnectionProxies/validate/action | プライベート エンドポイントの接続を検証します。 |
> | Microsoft.CognitiveServices/accounts/privateEndpointConnections/read | プライベート エンドポイントの接続を読み取ります。 |
> | Microsoft.CognitiveServices/accounts/privateEndpointConnections/write | プライベート エンドポイントの接続を書き込みます。 |
> | Microsoft.CognitiveServices/accounts/privateEndpointConnections/delete | プライベート エンドポイントの接続を削除します。 |
> | Microsoft.CognitiveServices/accounts/privateLinkResources/read | アカウントのプライベート リンク リソースを読み取ります。 |
> | Microsoft.CognitiveServices/accounts/skus/read | 既存のリソースの使用可能な SKU を読み取ります。 |
> | Microsoft.CognitiveServices/accounts/usages/read | 既存のリソースのクォータ使用率を取得します。 |
> | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | サブスクリプションで使用可能な SKU を読み取ります。 |
> | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | サブスクリプションで使用可能な SKU を読み取ります。 |
> | Microsoft.CognitiveServices/locations/deleteVirtualNetworkOrSubnets/action | VirtualNetworks または Subnets の削除に関する Microsoft.Network からの通知。 |
> | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | サブスクリプションで使用可能な SKU を読み取ります。 |
> | Microsoft.CognitiveServices/locations/deleteVirtualNetworkOrSubnets/action | VirtualNetworks または Subnets の削除に関する Microsoft.Network からの通知。 |
> | Microsoft.CognitiveServices/locations/operationresults/read | 非同期操作の状態を読み取ります。 |
> | Microsoft.CognitiveServices/Operations/read | 使用可能なすべての操作を一覧表示します。 |
> | **DataAction** | **説明** |
> | Microsoft.CognitiveServices/accounts/AnomalyDetector/timeseries/changepoint/detect/action | この操作により、系列全体を使用してモデルが生成され、各ポイントは同じモデルで検出されます。<br>このメソッドでは、特定のポイントの前後のポイントを使用して、これが傾向の変化点であるかどうかを判断します。<br>全体の検出によって、特定の時系列に含まれるすべての傾向の変化点が検出されます。 |
> | Microsoft.CognitiveServices/accounts/AnomalyDetector/timeseries/entire/detect/action | この操作により、系列全体を使用してモデルが生成され、各ポイントは同じモデルで検出されます。<br>このメソッドでは、特定のポイントの前後のポイントを使用して、異常であるかどうかを判断します。<br>全体を検出することで、時系列の全体的な状態をユーザーに提供することができます。 |
> | Microsoft.CognitiveServices/accounts/AnomalyDetector/timeseries/last/detect/action | この操作では、最新のポイント以前のポイントを使用してモデルを生成します。 このメソッドでは、履歴ポイントのみを使用して、ターゲット ポイントが異常であるかどうかを判断します。 検出された最新のポイントは、ビジネス メトリックのリアルタイム監視のシナリオと一致します。 |
> | Microsoft.CognitiveServices/accounts/Autosuggest/search/action | この操作は、特定のクエリまたは部分クエリの候補を提供します。 |
> | Microsoft.CognitiveServices/accounts/ComputerVision/analyze/action | この操作では､画像の内容に基づいて、さまざまな視覚的特徴のセットを抽出できます。  |
> | Microsoft.CognitiveServices/accounts/ComputerVision/describe/action | この操作は、完全な文を使用して、人間が判読できる言語でイメージ全体の説明を生成します。<br> 説明はコンテンツ タグのコレクションに基づいていますが、コンテンツ タグもこの操作によって返されます。<br>各画像に対して複数の説明が生成されることがあります。<br> 説明はその信頼度スコアの順に並んでいます。<br>すべての説明は英語です。 |
> | Microsoft.CognitiveServices/accounts/ComputerVision/generatethumbnail/action | この操作は、ユーザーが指定した幅と高さのサムネイル画像を生成します。<br> 既定では、サービスが画像を分析し、関心領域 (ROI) を特定し、ROI に基づいてスマート トリミング座標を生成します。<br> スマート トリミングは、指定する縦横比が入力画像の縦横比と異なるときに役立ちます。 |
> | Microsoft.CognitiveServices/accounts/ComputerVision/ocr/action | 光学式文字認識 (OCR) により画像内のテキストを検出し、認識した文字を抽出して、コンピューターで処理可能な文字ストリームに変換します。    |
> | Microsoft.CognitiveServices/accounts/ComputerVision/recognizetext/action | このインターフェイスを使用して、テキスト認識操作の結果を取得します。 テキスト認識インターフェイスを使用すると、"Operation-Location" というフィールドが応答に含まれます。 "Operation-Location" フィールドには、テキスト認識操作の結果の取得操作に使用する必要がある URL が含まれています。 |
> | Microsoft.CognitiveServices/accounts/ComputerVision/tag/action | この操作は、指定された画像のコンテンツに関連する単語すなわちタグのリストを生成します。<br>Computer Vision API は、画像内で検出された物体、生物、景色、または動作に基づくタグを返すことができます。<br>カテゴリとは異なり、タグは階層的分類システムで整理されていませんが、画像のコンテンツに対応しています。<br>タグにあいまいさを回避するためのヒントが含まれたり、タグによってコンテキストが提供されたりすることがあります。たとえば、"cello" (チェロ) というタグに "musical instrument" (楽器) というヒントが付く場合があります。<br>すべてのタグは英語です。 |
> | Microsoft.CognitiveServices/accounts/ComputerVision/areaofinterest/action | この操作は、画像の最も重要な部分を囲む境界ボックスを返します。 |
> | Microsoft.CognitiveServices/accounts/ComputerVision/detect/action | この操作は、指定した画像に対するオブジェクト検出を実行します。  |
> | Microsoft.CognitiveServices/accounts/ComputerVision/models/read | この操作は、Computer Vision API でサポートされているドメイン固有モデルの一覧を返します。  現時点では、この API によってサポートされるドメイン固有モデルは著名人認識機能とランドマーク認識機能です。 |
> | Microsoft.CognitiveServices/accounts/ComputerVision/models/analyze/action | この操作は、ドメイン固有モデルを適用することで、画像内のコンテンツを認識します。<br> Computer Vision API によってサポートされるドメイン固有モデルの一覧は、/models GET 要求を使用して取得できます。<br> 現時点で、この API によって提供されるドメイン固有モデルは著名人とランドマークです。 |
> | Microsoft.CognitiveServices/accounts/ComputerVision/read/analyze/action | このインターフェイスを使用して読み取り操作を実行し、テキストを多用するドキュメント用に最適化された最新の光学式文字認識 (OCR) アルゴリズムを使用します。<br>手書き、印刷、または組み合わせたドキュメントを処理できます。<br>読み取りインターフェイスを使用すると、'Operation-Location' というヘッダーが応答に含まれます。<br>'Operation-Location' ヘッダーには、OCR 結果にアクセスするための Get Read Result 操作に使用する必要がある URL が含まれています。* |
> | Microsoft.CognitiveServices/accounts/ComputerVision/read/analyzeresults/read | インターフェイスを使用して、読み取り操作の状態と OCR の結果を取得します。  'operationID' を含む URL が読み取り操作 'Operation-Location' 応答ヘッダーに返されます。* |
> | Microsoft.CognitiveServices/accounts/ComputerVision/read/core/asyncbatchanalyze/action | このインターフェイスを使用して、最新の光学式文字認識で Batch Read File 操作の結果を取得します。 |
> | Microsoft.CognitiveServices/accounts/ComputerVision/read/operations/read | このインターフェイスは、読み取り操作の OCR 結果を取得するために使用されます。 このインターフェイスの URL は、Batch Read File インターフェイスの <b>"Operation-Location"</b> フィールドから取得する必要があります。 |
> | Microsoft.CognitiveServices/accounts/ComputerVision/textoperations/read | このインターフェイスは、テキスト認識操作結果を取得するために使用されます。 このインターフェイスの URL は、テキスト認識インターフェイスから返される <b>"Operation-Location"</b> フィールドから取得する必要があります。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/imagelists/action | 画像一覧を作成します。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/action | 用語リストを作成します。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/imagelists/read | [Image Lists - Get Details - Image Lists - Get All]\(画像リスト - 詳細の取得 - 画像リスト - すべて取得\) |
> | Microsoft.CognitiveServices/accounts/ContentModerator/imagelists/delete | [Image Lists - Delete]\(画像リスト - 削除\) |
> | Microsoft.CognitiveServices/accounts/ContentModerator/imagelists/refreshindex/action | [Image Lists - Refresh Search Index]\(画像リスト - 検索インデックスを最新の情報に更新\) |
> | Microsoft.CognitiveServices/accounts/ContentModerator/imagelists/write | [Image Lists - Update Details]\(画像リスト - 詳細の更新\) |
> | Microsoft.CognitiveServices/accounts/ContentModerator/imagelists/images/write | 画像一覧に画像を追加します。 Image/Match API を使用する場合は、画像リストを使用して他の画像とのあいまい一致を行うことができます。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/imagelists/images/delete | 画像の一覧から画像を削除します。 Image/Match API を使用する場合は、画像リストを使用して他の画像とのあいまい一致を行うことができます。 一覧からすべての画像を削除します。 Image/Match API を使用する場合は、画像リストを使用して他の画像とのあいまい一致を行うことができます。* |
> | Microsoft.CognitiveServices/accounts/ContentModerator/imagelists/images/read | [Image - Get All Image IDs]\(画像 - すべての画像 ID を取得\) |
> | Microsoft.CognitiveServices/accounts/ContentModerator/processimage/evaluate/action | きわどい内容や成人向けコンテンツを含む画像の確率を返します。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/processimage/findfaces/action | 画像内の顔を検出します。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/processimage/match/action | あいまい一致により、画像をカスタム画像リストの 1 つに一致させます。 この API を使用して、カスタム画像リストを作成および管理できます。  |
> | Microsoft.CognitiveServices/accounts/ContentModerator/processimage/ocr/action | 画像内で見つかった指定した言語のテキストをすべて返します。 入力に言語が指定されていない場合は、既定で英語に設定されます。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/processtext/detectlanguage/action | この操作により、指定された入力コンテンツの言語が検出されます。 送信されたテキストを構成する、主要な言語の ISO 639-3 コードを返します。 110 言語以上をサポートしています。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/processtext/screen/action | この操作では、100 を超える言語の不適切な用語を検出し、カスタム ブラックリストおよび共有ブラックリストと照合します。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/teams/jobs/action | このエンドポイントにポストされた画像コンテンツに対して、ジョブ ID が返されます。  |
> | Microsoft.CognitiveServices/accounts/ContentModerator/teams/reviews/action | 作成されたレビューは、チームのレビュー担当者に対して表示されます。 レビュー担当者がレビューを完了すると、レビューの結果が指定された CallBackEndpoint に投稿されます (つまり、HTTP POST)。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/teams/jobs/read | ジョブ ID のジョブの詳細を取得します。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/teams/reviews/read | 渡されたレビュー ID のレビューの詳細を返します。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/teams/reviews/publish/action | ビデオ レビューは、最初は未公開状態で作成されます。つまり、チームのレビュー担当者がレビューを行うことはできません。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/teams/reviews/transcript/action | この API により、トランスクリプト ファイル (ビデオで話されているすべての単語のテキスト バージョン) がビデオ レビューに追加されます。 ファイルは、有効な WebVTT 形式である必要があります。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/teams/reviews/transcriptmoderationresult/action | この API は、ビデオ レビュー用のトランスクリプト画面のテキスト結果ファイルを追加します。 トランスクリプト画面テキストの結果ファイルは、Screen Text API の結果です。 トランスクリプト画面テキストの結果ファイルを生成するには、Screen Text API を使用して、トランスクリプト ファイルに不適切な表現が含まれていないかスクリーニングする必要があります。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/teams/reviews/accesskey/read | チームのレビュー コンテンツ アクセス キーを取得します。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/teams/reviews/frames/write | ビデオ レビュー用のフレームを追加するには、この方法を使用します。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/teams/reviews/frames/read | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/ContentModerator/teams/settings/templates/write | 指定されたテンプレートを作成または更新します |
> | Microsoft.CognitiveServices/accounts/ContentModerator/teams/settings/templates/delete | チーム内のテンプレートを削除します。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/teams/settings/templates/read | このチームでプロビジョニングされたレビュー用テンプレートの配列を返します。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/teams/workflows/write | 新しいワークフローを作成するか、既存の Web アプリを更新します。 |
> | Microsoft.CognitiveServices/accounts/ContentModerator/teams/workflows/read | チームで特定のワークフローの詳細を取得するチームで利用可能なすべてのワークフローを取得する * |
> | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/bulkupdate/action | [Term Lists - Bulk Update]\(用語リスト - 一括更新\) |
> | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/delete | [Term Lists - Delete]\(用語リスト - 削除\) |
> | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/read | [Term Lists - Get All - Term Lists - Get Details]\(用語リスト - すべて取得 - 用語リスト - 詳細を取得\) |
> | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/refreshindex/action | [Term Lists - Refresh Search Index]\(用語リスト - 検索インデックスを最新の情報に更新\) |
> | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/write | [Term Lists - Update Details]\(用語リスト - 詳細の更新\) |
> | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/terms/write | [Term - Add Term]\(用語 - 用語の追加\) |
> | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/terms/delete | [Term - Delete - Term - Delete All Terms]\(用語 - 削除 - 用語 - すべての用語を削除\) |
> | Microsoft.CognitiveServices/accounts/ContentModerator/termlists/terms/read | [Term - Get All Terms]\(用語 - すべての用語を取得\) |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/action | プロジェクトを作成します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/user/action | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/CustomVision/quota/action | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/action | プロジェクトを作成します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/user/action | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/quota/action | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/classify/iterations/image/action | 画像を分類し、結果を保存します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/classify/iterations/url/action | 画像の URL を分類し、結果を保存します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/classify/iterations/image/nostore/action | 結果を保存せずに画像を分類します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/classify/iterations/url/nostore/action | 結果を保存せずに画像の URL を分類します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/detect/iterations/image/action | 画像内のオブジェクトを検出し、結果を保存します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/detect/iterations/url/action | 画像の URL 内のオブジェクトを検出し、結果を保存します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/detect/iterations/image/nostore/action | 結果を保存せずに、画像内のオブジェクトを検出します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/detect/iterations/url/nostore/action | 結果を保存せずに、画像の URL 内のオブジェクトを検出します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/domains/read | 特定のドメインに関する情報を取得します。 使用可能なドメインの一覧を取得します。* |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/labelproposals/setting/action | ラベルの提案のプール サイズを設定します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/labelproposals/setting/read | このプロジェクトのラベルの提案のプール サイズを取得します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/project/migrate/action | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/action | この API は、multipart/form-data と application/octet-stream として本文コンテンツを受け入れます。 マルチパートを使用する場合 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/tags/action | プロジェクトのタグを作成します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/delete | 特定のプロジェクトを削除します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/read | 特定のプロジェクトを取得します。 自分のプロジェクトを取得します。* |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/train/action | トレーニングのためにプロジェクトをキューに置きます。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/write | 特定のプロジェクトを更新します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/import/action | プロジェクトをインポートします。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/export/read | プロジェクトをエクスポートします。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/regions/action | この API は、画像領域のバッチと、必要に応じてタグを使用して、既存の画像をリージョン情報で更新します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/files/action | この API は、ファイルのバッチと、必要に応じてタグを受け取り、画像を作成します。 画像は 64 枚、タグは 20 個の制限が設けられています。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/predictions/action | この API は、指定された予測画像から画像のバッチを作成します。 画像は 64 枚、タグは 20 個の制限が設けられています。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/urls/action | この API は、URL のバッチと、必要に応じてタグを受け取り、画像を作成します。 画像は 64 枚、タグは 20 個の制限が設けられています。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/tags/action | 一連の画像をタグのセットに関連付けます。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/delete | トレーニング画像のセットから画像を削除します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/regionproposals/action | この API は、リージョンの信頼度と共に、画像のリージョンの提案を取得します。 提案が見つからない場合は、空の配列が返されます。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/suggested/action | この API は、推奨タグ ID でフィルター処理されたタグなし画像をフェッチします。 画像が見つからない場合は、空の配列を返します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/id/read | この API は、指定されたタグの一連の画像と、必要に応じてイテレーションを返します。 イテレーションが指定されていない場合 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/regions/delete | 画像領域のセットを削除します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/suggested/count/action | この API は タグ ID を使用して、特定のしきい値について、提案されたタグごとにタグなし画像の数を取得します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/tagged/read | この API は、バッチ処理と範囲選択をサポートしています。 既定では、画像に一致する最初の 50 の画像のみが返されます。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/tagged/count/read | フィルター処理は、and/or の関係性に基づいています。 たとえば、指定されたタグ ID が "Dog" であり、 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/tags/delete | 一連の画像から一連のタグを削除します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/untagged/read | この API は、バッチ処理と範囲選択をサポートしています。 既定では、画像に一致する最初の 50 の画像のみが返されます。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/images/untagged/count/read | この API は、指定されたプロジェクトのタグがない画像と、必要に応じてイテレーションを返します。 イテレーションが指定されていない場合 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/iterations/delete | プロジェクトの特定のイテレーションを削除します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/iterations/export/action | トレーニングしたイテレーションをエクスポートします。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/iterations/read | 特定のイテレーションを取得します。 プロジェクトのイテレーションを取得します。* |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/iterations/publish/action | 特定のイテレーションを公開します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/iterations/write | 特定のイテレーションを更新します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/iterations/export/read | 特定のイテレーションのエクスポートの一覧を取得します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/iterations/performance/read | イテレーションに関する詳細なパフォーマンス情報を取得します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/iterations/performance/images/read | この API は、バッチ処理と範囲選択をサポートしています。 既定では、画像に一致する最初の 50 の画像のみが返されます。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/iterations/performance/images/count/read | フィルター処理は、and/or の関係性に基づいています。 たとえば、指定されたタグ ID が "Dog" であり、 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/iterations/publish/delete | 特定のイテレーションの公開を取り消します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/predictions/delete | 予測された画像とそれに関連する予測結果のセットを削除します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/predictions/query/action | 予測エンドポイントに送信された画像を取得します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/quicktest/image/action | 画像を簡単にテストします。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/quicktest/url/action | 画像の URL を簡単にテストします。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/tags/delete | プロジェクトからタグを削除します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/tags/read | 特定のタグに関する情報を取得します。 指定されたプロジェクトとイテレーションのタグを取得します。* |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/tags/write | タスクを更新します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/tagsandregions/suggestions/action | この API は、タグ付けされていないイメージの配列/バッチとタグの信頼度に対して、推奨されるタグと領域を取得します。 タグが見つからない場合は、空の配列を返します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/projects/train/advanced/action | PipelineConfiguration とトレーニングの種類を使用したトレーニングのためにプロジェクトをキューに置きます。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/quota/delete | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/quota/refresh/write | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/usage/prediction/user/read | Oxford ユーザーの予測リソースの使用状況を取得します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/usage/training/resource/tier/read | Azure ユーザーのトレーニング リソースの使用状況を取得します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/usage/training/user/read | Oxford ユーザーのトレーニング リソースの使用状況を取得します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/user/delete | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/user/state/write | ユーザーの状態を更新します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/user/tier/write | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/users/read | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/whitelist/delete | 特定の機能を持つ許可リストに登録されたユーザーを削除します |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/whitelist/read | 特定の機能を持つ許可リストに登録されたユーザーの一覧を取得します |
> | Microsoft.CognitiveServices/accounts/CustomVision.Prediction/whitelist/write | 特定の機能を持つ許可リストに登録されたユーザーを更新または作成します |
> | Microsoft.CognitiveServices/accounts/CustomVision/classify/iterations/image/action | 画像を分類し、結果を保存します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/classify/iterations/url/action | 画像の URL を分類し、結果を保存します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/classify/iterations/image/nostore/action | 結果を保存せずに画像を分類します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/classify/iterations/url/nostore/action | 結果を保存せずに画像の URL を分類します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/detect/iterations/image/action | 画像内のオブジェクトを検出し、結果を保存します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/detect/iterations/url/action | 画像の URL 内のオブジェクトを検出し、結果を保存します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/detect/iterations/image/nostore/action | 結果を保存せずに、画像内のオブジェクトを検出します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/detect/iterations/url/nostore/action | 結果を保存せずに、画像の URL 内のオブジェクトを検出します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/domains/read | 特定のドメインに関する情報を取得します。 使用可能なドメインの一覧を取得します。* |
> | Microsoft.CognitiveServices/accounts/CustomVision/labelproposals/setting/action | ラベルの提案のプール サイズを設定します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/labelproposals/setting/read | このプロジェクトのラベルの提案のプール サイズを取得します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/project/migrate/action | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/action | この API は、multipart/form-data と application/octet-stream として本文コンテンツを受け入れます。 マルチパートを使用する場合 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/tags/action | プロジェクトのタグを作成します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/delete | 特定のプロジェクトを削除します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/read | 特定のプロジェクトを取得します。 自分のプロジェクトを取得します。* |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/train/action | トレーニングのためにプロジェクトをキューに置きます。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/write | 特定のプロジェクトを更新します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/import/action | プロジェクトをインポートします。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/artifacts/read | Blob の成果物の相対パスに基づいて、Blob ストレージから成果物のコンテンツを取得します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read | プロジェクトをエクスポートします。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/regions/action | この API は、画像領域のバッチと、必要に応じてタグを使用して、既存の画像をリージョン情報で更新します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/files/action | この API は、ファイルのバッチと、必要に応じてタグを受け取り、画像を作成します。 画像は 64 枚、タグは 20 個の制限が設けられています。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/predictions/action | この API は、指定された予測画像から画像のバッチを作成します。 画像は 64 枚、タグは 20 個の制限が設けられています。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/urls/action | この API は、URL のバッチと、必要に応じてタグを受け取り、画像を作成します。 画像は 64 枚、タグは 20 個の制限が設けられています。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/tags/action | 一連の画像をタグのセットに関連付けます。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/delete | トレーニング画像のセットから画像を削除します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/regionproposals/action | この API は、リージョンの信頼度と共に、画像のリージョンの提案を取得します。 提案が見つからない場合は、空の配列が返されます。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/suggested/action | この API は、推奨タグ ID でフィルター処理されたタグなし画像をフェッチします。 画像が見つからない場合は、空の配列を返します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/read | この API は、バッチ処理と範囲選択をサポートしています。 既定では、画像に一致する最初の 50 の画像のみが返されます。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/metadata/action | この API では、画像 ID のバッチ、およびメタデータを受け入れて、画像を更新します。 画像は 64 個に制限されています。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/count/read | フィルター処理は、and/or の関係性に基づいています。 たとえば、指定されたタグ ID が "Dog" であり、 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/id/read | この API は、指定されたタグの一連の画像と、必要に応じてイテレーションを返します。 イテレーションが指定されていない場合 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/regions/delete | 画像領域のセットを削除します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/suggested/count/action | この API は タグ ID を使用して、特定のしきい値について、提案されたタグごとにタグなし画像の数を取得します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/tagged/read | この API は、バッチ処理と範囲選択をサポートしています。 既定では、画像に一致する最初の 50 の画像のみが返されます。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/tagged/count/read | フィルター処理は、and/or の関係性に基づいています。 たとえば、指定されたタグ ID が "Dog" であり、 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/tags/delete | 一連の画像から一連のタグを削除します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/untagged/read | この API は、バッチ処理と範囲選択をサポートしています。 既定では、画像に一致する最初の 50 の画像のみが返されます。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/images/untagged/count/read | この API は、指定されたプロジェクトのタグがない画像と、必要に応じてイテレーションを返します。 イテレーションが指定されていない場合 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/delete | プロジェクトの特定のイテレーションを削除します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/export/action | トレーニングしたイテレーションをエクスポートします。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/read | 特定のイテレーションを取得します。 プロジェクトのイテレーションを取得します。* |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/publish/action | 特定のイテレーションを公開します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/write | 特定のイテレーションを更新します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/export/read | 特定のイテレーションのエクスポートの一覧を取得します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/performance/read | イテレーションに関する詳細なパフォーマンス情報を取得します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/performance/images/read | この API は、バッチ処理と範囲選択をサポートしています。 既定では、画像に一致する最初の 50 の画像のみが返されます。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/performance/images/count/read | フィルター処理は、and/or の関係性に基づいています。 たとえば、指定されたタグ ID が "Dog" であり、 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/publish/delete | 特定のイテレーションの公開を取り消します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/predictions/delete | 予測された画像とそれに関連する予測結果のセットを削除します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/predictions/query/action | 予測エンドポイントに送信された画像を取得します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/quicktest/image/action | 画像を簡単にテストします。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/quicktest/url/action | 画像の URL を簡単にテストします。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/tags/delete | プロジェクトからタグを削除します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/tags/read | 特定のタグに関する情報を取得します。 指定されたプロジェクトとイテレーションのタグを取得します。* |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/tags/write | タスクを更新します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/tagsandregions/suggestions/action | この API は、タグ付けされていないイメージの配列/バッチとタグの信頼度に対して、推奨されるタグと領域を取得します。 タグが見つからない場合は、空の配列を返します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/projects/train/advanced/action | PipelineConfiguration とトレーニングの種類を使用したトレーニングのためにプロジェクトをキューに置きます。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/quota/delete | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/CustomVision/quota/refresh/write | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/CustomVision/usage/prediction/user/read | Oxford ユーザーの予測リソースの使用状況を取得します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/usage/training/resource/tier/read | Azure ユーザーのトレーニング リソースの使用状況を取得します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/usage/training/user/read | Oxford ユーザーのトレーニング リソースの使用状況を取得します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/user/delete | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/CustomVision/user/state/write | ユーザーの状態を更新します。 |
> | Microsoft.CognitiveServices/accounts/CustomVision/user/tier/write | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/CustomVision/users/read | *NotDefined* |
> | Microsoft.CognitiveServices/accounts/CustomVision/whitelist/delete | 特定の機能を持つ許可リストに登録されたユーザーを削除します |
> | Microsoft.CognitiveServices/accounts/CustomVision/whitelist/read | 特定の機能を持つ許可リストに登録されたユーザーの一覧を取得します |
> | Microsoft.CognitiveServices/accounts/CustomVision/whitelist/write | 特定の機能を持つ許可リストに登録されたユーザーを更新または作成します |
> | Microsoft.CognitiveServices/accounts/EntitySearch/search/action | 特定のクエリのエンティティを取得し、結果を格納します。 |
> | Microsoft.CognitiveServices/accounts/Face/detect/action | 画像内の人間の顔を検出して、顔を示す四角形を返します。必要に応じて、FaceId、ランドマーク、および属性も返します。 |
> | Microsoft.CognitiveServices/accounts/Face/findsimilars/action | 与えられたクエリの顔の faceId に対して、faceId 配列、顔リスト、または大規模顔リストから類似の顔を検索します。 faceId |
> | Microsoft.CognitiveServices/accounts/Face/group/action | 顔の類似性に基づいて候補の顔をグループ分けします。 |
> | Microsoft.CognitiveServices/accounts/Face/identify/action | 1 対多の識別により、人物グループまたは大規模人物グループから、特定のクエリの人物の顔に最も近い一致を見つけます。 |
> | Microsoft.CognitiveServices/accounts/Face/verify/action | 2 つの顔が同一人物に属しているか 1 つの顔が 1 人の人物に属しているかを確認します。 |
> | Microsoft.CognitiveServices/accounts/Face/facelists/write | ユーザーが指定した faceListId、name、および userData (オプション) を使用して空の顔リストを作成します。 最大 64 個の顔リストが許可されています name と userData 含む、顔リストの情報を更新します。 |
> | Microsoft.CognitiveServices/accounts/Face/facelists/delete | 指定された顔リストを削除します。 顔リスト内の関連する顔画像も削除されます。 |
> | Microsoft.CognitiveServices/accounts/Face/facelists/read | 顔リストの faceListId、name、userData、および顔リスト内の顔を取得します。 顔リストの faceListId、name、userData を一覧表示します。 |
> | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/write | 指定された顔リストに顔を追加します (最大 1,000 個)。 |
> | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/delete | 指定された faceListId と persisitedFaceId に基づいて顔リストから顔を削除します。 関連する顔画像も削除されます。 |
> | Microsoft.CognitiveServices/accounts/Face/largefacelists/write | ユーザー指定の largeFaceListId、name、および userData (オプション) を使用して空の大規模顔リストを作成します。 name や userData など、大規模顔リストの情報を更新します。 |
> | Microsoft.CognitiveServices/accounts/Face/largefacelists/delete | 指定された大規模顔リストを削除します。 大規模顔リスト内の関連する顔画像も削除されます。 |
> | Microsoft.CognitiveServices/accounts/Face/largefacelists/read | 大規模顔リストの largeFaceListId、name、userData を取得します。 大規模顔リストの情報 (largeFaceListId、name、および userData) を一覧表示します。 |
> | Microsoft.CognitiveServices/accounts/Face/largefacelists/train/action | 大規模顔リストのトレーニング タスクを送信します。 トレーニングは重要な手順です。使用できるのはトレーニング済みの大規模顔リストのみです。 |
> | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/write | 指定された大規模顔リストに顔を追加します (最大 1,000,000 個)。 persistedFaceId に基づいて、大規模顔リスト内の指定された顔の userData フィールドを更新します。 |
> | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/delete | 指定された largeFaceListId と persisitedFaceId に基づいて大規模顔リストから顔を削除します。 関連する顔画像も削除されます。 |
> | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/read | largeFaceListId と persistedFaceId に基づいて大規模顔リストで永続化されている顔を取得します。 指定された大規模顔リスト内の顔の persistedFaceId と userData を一覧表示します。 |
> | Microsoft.CognitiveServices/accounts/Face/largefacelists/training/read | 大規模顔リストのトレーニング状態が完了か実行中かをチェックします。 LargeFaceList トレーニングは非同期操作です。 |
> | Microsoft.CognitiveServices/accounts/Face/largepersongroups/write | ユーザー指定の largePersonGroupId、name、および userData (オプション) を使用して新しい大規模人物グループを作成します。 既存の大規模人物グループの name と userData を更新します。 プロパティが要求本文にない場合、プロパティは変更されません。 |
> | Microsoft.CognitiveServices/accounts/Face/largepersongroups/delete | 指定された personGroupId に基づいて、既存の大規模人物グループを削除します。 この大規模人物グループ内の永続化されているデータは削除されます。 |
> | Microsoft.CognitiveServices/accounts/Face/largepersongroups/read | name や userData など、大規模人物グループの情報を取得します。 この API では大規模人物グループの情報が返されます 既存のすべての大規模人物グループの largePesonGroupId、name、および userData をリストします。 |
> | Microsoft.CognitiveServices/accounts/Face/largepersongroups/train/action | 大規模人物グループのトレーニング タスクを送信します。 トレーニングは重要な手順です。使用できるのはトレーニング済みの大規模人物グループのみです。 |
> | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/action | 指定された大規模人物グループに新しい人物を作成します。 この人物に顔を追加するには、問い合わせてください。 |
> | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/delete | 大規模人物グループから既存の人物を削除します。 格納されているすべての人物データと人物エントリ内の顔画像が削除されます。 |
> | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/read | 人物の name と userData、および登録された人物の顔画像に対応する persistedFaceIds を取得します。 指定された大規模人物グループのすべての人物の情報 (personId、name、userData、persistedFaceIds など) を一覧表示します。 |
> | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/write | 人物の name または userData を更新します。 |
> | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/write | 顔識別または顔検証のために、人物の顔画像を大規模人物グループに追加します。 画像を扱います 人物の永続化された顔の userData フィールドを更新します。 |
> | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/delete | 大規模人物グループ内の人物の顔を削除します。 顔データとその顔エントリに関連する画像も削除されます。 |
> | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/read | 人物の顔の情報を取得します。 永続化されている人物の顔は、largePersonGroupId、personId、および persistedFaceId によって指定されます。 |
> | Microsoft.CognitiveServices/accounts/Face/largepersongroups/training/read | 大規模人物グループのトレーニング状態が完了か実行中かをチェックします。 LargePersonGroup トレーニングは非同期操作です。 |
> | Microsoft.CognitiveServices/accounts/Face/persongroups/write | 指定された personGroupId、name、およびユーザー提供の userData を使用して新しい人物グループを作成します。 既存の人物グループの name と userData を更新します。 プロパティが要求本文にない場合、プロパティは変更されません。 |
> | Microsoft.CognitiveServices/accounts/Face/persongroups/delete | 指定された personGroupId に基づいて、既存の人物グループを削除します。 この人物グループ内の永続化されているデータが削除されます。 |
> | Microsoft.CognitiveServices/accounts/Face/persongroups/read | 人物グループの name と userData を取得します。 この personGroup 下の人物の情報を取得するために使用します 人物グループの pesonGroupId、name、および userData をリストします。 |
> | Microsoft.CognitiveServices/accounts/Face/persongroups/train/action | 人物グループのトレーニング タスクを送信します。 トレーニングは重要な手順です。使用できるのはトレーニング済みの人物グループのみです。 |
> | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/action | 指定された人物グループに新しい人物を作成します。 この人物に顔を追加するには、問い合わせてください。 |
> | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/delete | 人物グループから既存の人物を削除します。 格納されているすべての人物データと人物エントリ内の顔画像が削除されます。 |
> | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/read | 人物の name と userData、および登録された人物の顔画像に対応する persistedFaceIds を取得します。 指定された人物グループのすべての人物の情報 (登録されている personId、name、userData、persistedFaceIds など) を一覧表示します。 |
> | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/write | 人物の name または userData を更新します。 |
> | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/write | 顔識別または顔検証のために、人物の顔画像を人物グループに追加します。 複数の画像を扱います 人物の永続化された顔の userData フィールドを更新します。 |
> | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/delete | 人物グループ内の人物の顔を削除します。 顔データとその顔エントリに関連する画像も削除されます。 |
> | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/read | 人物の顔の情報を取得します。 永続化されている人物の顔は、personGroupId、personId、および persistedFaceId によって指定されます。 |
> | Microsoft.CognitiveServices/accounts/Face/persongroups/training/read | 人物グループのトレーニング状態が完了か実行中かをチェックします。 PersonGroup トレーニングは、トリガーされる非同期操作です。 |
> | Microsoft.CognitiveServices/accounts/Face/snapshot/take/action | オブジェクトのスナップショットを取得します。 |
> | Microsoft.CognitiveServices/accounts/Face/snapshot/read | スナップショット操作の状態を取得します。 |
> | Microsoft.CognitiveServices/accounts/Face/snapshot/apply/action | ユーザー指定のオブジェクト ID を指定して、スナップショットを適用します。 |
> | Microsoft.CognitiveServices/accounts/Face/snapshot/delete | スナップショットを削除します。 |
> | Microsoft.CognitiveServices/accounts/Face/snapshot/write | スナップショットのプロパティを更新します。 |
> | Microsoft.CognitiveServices/accounts/Face/snapshots/read | すべてのユーザーのアクセス可能なスナップショットを情報と共に一覧表示します。* |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/train/action | カスタム モデルを作成してトレーニングします。<br>トレーニング要求には、外部からアクセスできる Azure Storage BLOB コンテナー URI (可能であれば Shared Access Signature URI) またはローカルにマウントされたドライブのデータ フォルダーへの有効なパスのいずれかのソース パラメーターを含める必要があります。<br>ローカル パスを指定する場合は、Linux または Unix のパス形式に従い、入力マウント構成をルートとする絶対パスにする必要があります。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/action | カスタム モデルを作成してトレーニングします。<br>要求には、外部からアクセスできる Azure Storage BLOB コンテナー URI (可能であれば Shared Access Signature URI) またはローカルにマウントされたドライブのデータ フォルダーへの有効なパスのいずれかのソース パラメーターを含める必要があります。<br>ローカル パスを指定する場合は、Linux/Unix のパス形式に従い、入力マウント構成設定の値をルートとする絶対パスを使用する必要があります。たとえば、"{Mounts:Input}" 構成設定値が "/input" の場合、有効なソース パスは "/input/contosodataset" になります。<br>トレーニング対象のすべてのデータは、その下のソース フォルダーまたはサブ フォルダーの下にあることが想定されます。<br>モデルは、次のコンテンツの種類であるドキュメントを使用してトレーニングされます - "application/pdf"、"image/jpeg"、"image/png"、"image/tiff"。<br>他のコンテンツの種類は無視されます。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/analyze/action | 指定されたドキュメントからキーと値のペアを抽出します。 入力ドキュメントは、サポートされているコンテンツの種類 ('application/pdf'、'image/jpeg'、または 'image/png') のいずれかである必要があります。 成功応答が JSON で返されます。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/delete | モデルの成果物を削除します。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/read | モデルに関する情報を取得します。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/copyauthorization/action | ターゲット Form Recognizer リソースでモデルをコピーするための承認ペイロードを生成します。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/copy/action | あるサブスクリプションから別のサブスクリプションにカスタム Form Recognizer モデルをコピーします。<br>`source=false` クエリ文字列でこの API を使用することによって、ターゲット エンドポイントから `modelId` トークンを取得することで、プロセスを開始します。<br>その後、要求本文で他のターゲット リソース情報と共に `modelId` の参照を渡します。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/analyze/action | 指定したドキュメントからキーと値のペア、テーブル、セマンティック値を抽出します。<br>入力ドキュメントは、サポートされているコンテンツの種類のいずれかである必要があります - "application/pdf"、"image/jpeg"、"image/png"、または "image/tiff"。<br>または、種類 "application/json" を使用して、分析するドキュメントの URL の場所を指定します。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/copy/action | カスタム Form Recognizer モデルをターゲット Form Recognizer リソースにコピーします。 この操作を呼び出す前に、まず、コピーするための承認を取得する必要があります  |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/delete | モデルを削除対象としてマークします。 モデル成果物は、48 時間以内に完全に削除されます。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/copyauthorization/action | モデル コピー操作に対する承認ペイロードを生成します。 この操作は、ターゲット Form Recognizer リソース エンドポイントに対して呼び出されます  |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/read | カスタム モデルに関する詳細情報を取得します。 すべてのカスタム モデルに関する情報を取得します |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/compose/action | 作成要求には、モデル ID の一覧が含まれます。 これによって、ラベル モデルまたは作成済みモデルでトレーニングされたすべてのモデルが検証されます。 組み合わされたモデルの数の限界が検証されます。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/analyzeresults/read | 現在の状態と、フォームの分析操作の結果を取得します。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/analyzeresults/read | 現在の状態と、フォームの分析操作の結果を取得します。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/copyresults/read | 現在の状態と、カスタム フォーム モデル コピー操作の結果を取得します。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/copyresults/read | 現在の状態と、カスタム フォーム モデル コピー操作の結果を取得します。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/custom/models/keys/read | モデルのキーを取得します。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/layout/analyze/action | 指定したドキュメントからテキストとレイアウトの情報を抽出します。<br>入力ドキュメントは、サポートされているコンテンツの種類のいずれかである必要があります - "application/pdf"、"image/jpeg"、"image/png"、または "image/tiff"。<br>または、種類 "application/json" を使用して、分析するドキュメントの URL の場所を指定します。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/layout/analyzeresults/read | 進行状況を追跡し、レイアウトの分析操作の結果を取得します |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/prebuilt/businesscard/analyze/action | 指定した名刺ドキュメントからフィールド テキストとセマンティック値を抽出します。  |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/prebuilt/businesscard/analyzeresults/read | 状態をクエリし、名刺分析操作の結果を取得します。 このインターフェイスの URL は、名刺分析応答の "Operation-Location" ヘッダーから取得できます。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/prebuilt/iddocument/analyze/action | 指定された ID ドキュメントからフィールド テキストとセマンティック値を抽出します。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/prebuilt/iddocument/analyzeresults/read | 状態をクエリし、Analyze ID 操作の結果を取得します。 このインターフェイスの URL は、Analyze ID 応答の 'Operation-Location' ヘッダーから取得できます。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/prebuilt/invoice/analyze/action | 指定された請求書ドキュメントからフィールド テキストとセマンティック値を抽出します。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/prebuilt/invoice/analyzeresults/read | 状態をクエリし、Analyze Invoice 操作の結果を取得します。 このインターフェイスの URL は、Analyze Invoice 応答の 'Operation-Location' ヘッダーから取得できます。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/prebuilt/receipt/asyncbatchanalyze/action | 指定された受信ドキュメントからフィールド テキストとセマンティック値を抽出します。 入力ドキュメントはサポートされているいずれかである必要があります |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/prebuilt/receipt/analyzeresults/read | 状態をクエリし、Analyze Receipt 操作の結果を取得します。 このインターフェイスの URL は、Analyze Receipt 応答の 'Operation-Location' ヘッダーから取得できます。 |
> | Microsoft.CognitiveServices/accounts/FormRecognizer/prebuilt/receipt/operations/read | 状態をクエリし、Analyze Receipt 操作の結果を取得します。 このインターフェイスの URL は、Analyze Receipt 応答の 'Operation-Location' ヘッダーから取得できます。 |
> | Microsoft.CognitiveServices/accounts/ImageSearch/details/action | 画像を含む Web ページなど、画像に関する分析情報を返します。 |
> | Microsoft.CognitiveServices/accounts/ImageSearch/search/action | 特定のクエリに関連する画像を取得します。 |
> | Microsoft.CognitiveServices/accounts/ImageSearch/trending/action | 現在の注目の画像を取得します。 |
> | Microsoft.CognitiveServices/accounts/ImmersiveReader/getcontentmodelforreader/action | イマーシブ リーダー セッションを作成します |
> | Microsoft.CognitiveServices/accounts/LUIS/predict/action | 指定されたクエリについて公開されたエンドポイント予測を取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/write | 新しい LUIS アプリを作成します。 アプリケーションの名前または説明を更新します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/azureaccounts/action | アプリケーションに Azure アカウントを割り当てます。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/delete | アプリケーションを削除します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/read | アプリケーション情報を取得します。 すべてのユーザー アプリケーションを一覧表示します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/import/action | アプリケーションを LUIS にインポートします。アプリケーションの JSON が要求本文に含まれている必要があります。 新しいアプリ ID を返します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/publish/action | アプリケーションの特定のバージョンを発行します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/querylogsasync/action | アプリケーションの過去 1 か月のクエリ ログのダウンロード要求を開始します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/azureaccounts/read | Azure Resource Manager トークンを使用して、ユーザーのアプリケーションに割り当てられた LUIS Azure アカウントを取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/azureaccounts/delete | Azure Resource Manager トークンを使用して、ユーザーの LUIS Azure アカウントを取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/cultures/read | サポートされている LUIS アプリケーション カルチャを取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/customprebuiltdomains/write | 事前構築済みのドメインとそのモデルを新しいアプリケーションとして追加します。 新しいアプリ ID を返します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/customprebuiltdomains/read | 特定のカルチャで使用可能なすべてのカスタム事前構築済みドメインを取得します。すべてのカルチャで使用可能なすべてのカスタム事前構築済みドメインを取得します |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/domains/read | 使用可能なアプリケーション ドメインを取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/endpoints/read | 使用可能なエンドポイント デプロイ リージョンと URL を返します |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/publishsettings/read | アプリケーションの発行設定を取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/publishsettings/write | アプリケーション発行設定を更新します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/querylogs/read | アプリケーションの過去 1 か月のクエリ ログを取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/querylogsasync/read | クエリ ログのダウンロード要求の状態を取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/settings/read | アプリケーションの設定を取得します |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/settings/write | アプリケーションの設定を更新します |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/usagescenarios/read | アプリケーションで使用可能な使用シナリオを取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/train/action | 指定した LUIS アプリケーションのバージョンのトレーニング要求を送信します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/clone/action | 選択したアプリケーション バージョンの現在のスナップショットに相当する、新しいアプリケーション バージョンを作成します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/delete | アプリケーション バージョンを削除します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/read | アプリケーション バージョン情報を取得します。 アプリケーション バージョンの一覧の情報を取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/import/action | 新しいバージョンを LUIS アプリケーションにインポートします。バージョンの JSON が、要求本文に含まれている必要があります。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/write | アプリケーション バージョンの名前または説明を更新します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/closedlists/write | リスト エンティティを LUIS アプリに追加します。 既存の closedlist にサブリストのバッチを追加します。* 閉じたリスト モデルを更新します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/closedlists/delete | 閉じたリスト エンティティをアプリケーションから削除します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/closedlists/read | 閉じたリスト モデルの情報を取得します。 closedlist モデルに関する情報を取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/closedlists/roles/write | 閉じたリスト エンティティ モデルのロールを追加します。閉じたリスト エンティティ モデルのロールを更新します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/closedlists/roles/delete | 閉じたリスト エンティティ モデルのロールを削除します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/closedlists/roles/read | 閉じたリスト エンティティ モデルのロールを取得します。 閉じたリスト エンティティ モデルのロールを取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/closedlists/sublists/write | 既存の閉じたリストにリストを追加します。閉じたリストのサブリストの 1 つを更新します |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/closedlists/sublists/delete | 指定したリスト エンティティのサブリストを削除します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/compositeentities/write | 複合エンティティ エクストラクターをアプリケーションに追加します。 複合エンティティ エクストラクターを更新します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/compositeentities/delete | 複合エンティティ エクストラクターをアプリケーション バージョンから削除します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/compositeentities/read | 複合エンティティ モデルに関する情報を取得します。 アプリケーション バージョンの複合エンティティ モデルに関する情報を取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/compositeentities/children/write | 既存の複合エンティティ モデルに 1 つの子を追加します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/compositeentities/children/delete | 複合エンティティ エクストラクターの子をアプリケーション バージョンから削除します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/compositeentities/roles/write | 複合エンティティ モデルのロールを追加します。 複合エンティティ モデルのロールを更新します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/compositeentities/roles/delete | 複合エンティティ モデルのロールを削除します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/compositeentities/roles/read | 複合エンティティ モデルのロールを取得します。 複合エンティティ モデルのロールを取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/customprebuiltdomains/write | カスタマイズ可能な事前構築済みドメインとそのすべてのモデルをこのアプリケーション バージョンに追加します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/customprebuiltdomains/delete | 事前構築済みドメインのモデルをアプリケーション バージョンから削除します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/customprebuiltentities/write | カスタムの事前構築済みドメイン エンティティ モデルをアプリケーション バージョンに追加します。 エンティティ ID で、[エンティティの削除](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c1f)を使用して、このエンティティを削除します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/customprebuiltentities/read | このアプリケーション バージョンのすべてのカスタムの事前構築済みドメイン エンティティ情報を取得します |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/customprebuiltentities/roles/write | カスタムの事前構築済みドメイン エンティティ モデルのロールを追加します。カスタムの事前構築済みドメイン エンティティ モデルのロールを更新します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/customprebuiltentities/roles/delete | カスタムの事前構築済みエンティティ モデルのロールを削除します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/customprebuiltentities/roles/read | カスタムの事前構築済みドメイン エンティティ モデルのロールを取得します。 カスタムの事前構築済みドメイン エンティティ モデルのロールを取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/customprebuiltintents/write | カスタムの事前構築済みドメイン意図モデルをアプリケーションに追加します。 意図 ID で、[意図の削除](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c1c)を使用して、この意図を削除します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/customprebuiltintents/read | このアプリケーション バージョンのカスタムの事前構築済み意図情報を取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/customprebuiltmodels/read | このアプリケーション バージョンのすべてのカスタムの事前構築済みドメイン モデルを取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/entities/write | シンプル エンティティ エクストラクターをアプリケーション バージョンに追加します。 エンティティ エクストラクターの名前を更新します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/entities/delete | シンプル エンティティ エクストラクターをアプリケーション バージョンから削除します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/entities/read | シンプル エンティティ モデルに関する情報を取得します。 アプリケーション バージョンのシンプル エンティティ モデルに関する情報を取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/entities/roles/write | シンプル エンティティ モデルのロールを追加します。シンプル エンティティ モデルのロールを更新します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/entities/roles/delete | シンプル エンティティ モデルのロールを削除します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/entities/roles/read | シンプル エンティティ モデルのロールを取得します。 シンプル エンティティ モデルのロールを取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/entities/suggest/read | エンティティ モデルの精度を向上させる例を提案します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/example/write | ラベル付きの例をアプリケーション バージョンに追加します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/examples/write | 重複しないラベル付きの例のバッチを、指定したアプリケーションに追加します。 バッチに階層構造の子エンティティを含めることはできません。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/examples/delete | 指定した ID を持つラベルを削除します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/examples/read | レビューするエンドポイントの例のサブセットを返します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/export/read | LUIS アプリケーション バージョンを JSON 形式にエクスポートします。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/features/read | すべてのアプリケーション バージョン機能を取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/hierarchicalentities/write | 階層構造エンティティ エクストラクターをアプリケーション バージョンに追加します。 階層構造エンティティ エクストラクター モデルの名前と子を更新します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/hierarchicalentities/delete | 階層構造エンティティ エクストラクターをアプリケーション バージョンから削除します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/hierarchicalentities/read | 階層構造エンティティ モデルに関する情報を取得します。 アプリケーション バージョンの階層構造エンティティ モデルに関する情報を取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/hierarchicalentities/children/write | 既存の階層構造エンティティ モデルに単一の子を作成します。 既存の階層構造エンティティ モデルの単一の子の名前を変更します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/hierarchicalentities/children/delete | 階層構造エンティティ エクストラクターの子をアプリケーション バージョンから削除します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/hierarchicalentities/children/read | 階層構造エンティティの子モデルに関する情報を取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/hierarchicalentities/roles/write | 階層構造エンティティ モデルのロールを追加します。階層構造エンティティ モデルのロールを更新します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/hierarchicalentities/roles/delete | 階層構造エンティティ モデルのロールを削除します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/hierarchicalentities/roles/read | 階層構造エンティティ モデルのロールを取得します。 階層構造エンティティ モデルのロールを取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/intents/write | 意図分類子をアプリケーション バージョンに追加します。 意図分類子の名前を更新します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/intents/delete | 意図分類子をアプリケーション バージョンから削除します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/intents/read | 意図モデルに関する情報を取得します。 アプリケーション バージョンの意図モデルに関する情報を取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/intents/patternrules/read | 特定の意図のパターンを取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/intents/suggest/read | 意図モデルの精度を向上させる例を提案します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/listprebuilts/read | アプリケーションのカルチャに基づいて、アプリケーションで使用可能なすべての事前構築済みエンティティを取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/models/read | アプリケーション バージョン モデルに関する情報を取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/patternanyentities/write | Pattern.any エンティティ エクストラクターをアプリケーション バージョンに追加します。 Pattern.any エンティティ エクストラクターを更新します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/patternanyentities/delete | Pattern.any エンティティ エクストラクターをアプリケーション バージョンから削除します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/patternanyentities/read | Pattern.any エンティティ モデルに関する情報を取得します。 アプリケーション バージョンの Pattern.any エンティティ モデルに関する情報を取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/patternanyentities/explicitlist/write | Pattern.any 明示的リストに項目を追加します。 Pattern.any エンティティ モデルの明示的リスト項目を更新します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/patternanyentities/explicitlist/delete | Pattern.any 明示的リストから項目を削除します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/patternanyentities/explicitlist/read | Pattern.any エンティティ モデルの明示的リストを取得します。 Pattern.any エンティティ モデルの明示的リスト項目を取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/patternanyentities/roles/write | Pattern.any エンティティ モデルのロールを追加します。Pattern.any エンティティ モデルのロールを更新します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/patternanyentities/roles/delete | Pattern.any エンティティ モデルのロールを削除します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/patternanyentities/roles/read | Pattern.any エンティティ モデルのロールを取得します。 Pattern.any エンティティ モデルのロールを取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/patternrule/write | 指定したアプリケーション バージョンにパターンを追加します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/patternrules/write | パターンのリストをアプリケーション バージョンに追加します。 アプリケーション バージョンのパターンを更新します。 アプリケーション バージョンのパターンのリストを更新します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/patternrules/delete | アプリケーション バージョンからパターンのリストを削除します。 アプリケーション バージョンからパターンを削除します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/patternrules/read | アプリケーション バージョンのパターンを取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/patterns/write | **この API は非推奨です。** |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/patterns/delete | **この API は非推奨です。** |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/patterns/read | **この API は非推奨です。** |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/phraselists/write | 新しい phraselist 機能を作成します。 phraselist 機能のフレーズ、状態、および名前を更新します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/phraselists/delete | phraselist 機能をアプリケーションから削除します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/phraselists/read | Phraselist 機能情報を取得します。 アプリケーション バージョンのすべての phraselist 機能を取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/prebuilts/write | 事前構築済みエンティティ エクストラクターのリストをアプリケーション バージョンに追加します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/prebuilts/delete | 事前構築済みエンティティ エクストラクターをアプリケーション バージョンから削除します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/prebuilts/read | 事前構築済みエンティティ モデルに関する情報を取得します。 アプリケーション バージョンの事前構築済みエンティティ モデルに関する情報を取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/prebuilts/roles/write | 事前構築済みエンティティ モデルのロールを追加します。事前構築済みエンティティ モデルのロールを更新します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/prebuilts/roles/delete | 事前構築済みエンティティ モデルのロールを削除します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/prebuilts/roles/read | 事前構築済みエンティティ モデルのロールを取得します。 事前構築済みエンティティ モデルのロールを取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/regexentities/write | 正規表現エンティティ エクストラクターをアプリケーション バージョンに追加します。 正規表現エンティティ モデルを更新します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/regexentities/delete | 正規表現エンティティ モデルをアプリケーション バージョンから削除します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/regexentities/read | 正規表現エンティティ モデルに関する情報を取得します。 アプリケーション バージョンの正規表現エンティティ モデルに関する情報を取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/regexentities/roles/write | 正規表現エンティティ モデルのロールを追加します。正規表現エンティティ モデルのロールを更新します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/regexentities/roles/delete | 正規表現エンティティ モデルのロールを削除します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/regexentities/roles/read | 正規表現エンティティ モデルのロールを取得します。 正規表現エンティティ モデルのロールを取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/settings/read | アプリケーション バージョン設定を取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/settings/write | アプリケーション バージョン設定を更新します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/suggest/delete | エンドポイント発話を削除しました。 この発話は、"エンドポイントの発話を確認する" リストにあります。 |
> | Microsoft.CognitiveServices/accounts/LUIS/apps/versions/train/read | 指定したアプリケーション バージョンのすべてのモデル (意図とエンティティ) のトレーニング状態を取得します。 トレーニング API を呼び出して、トレーニング状態を取得する前に、この API を呼び出して、LUIS アプリをトレーニングする必要があります。 |
> | Microsoft.CognitiveServices/accounts/LUIS/azureaccounts/read | Azure Resource Manager トークンを使用して、ユーザーの LUIS Azure アカウントを取得します。 |
> | Microsoft.CognitiveServices/accounts/LUIS/package/slot/gzip/read | 公開された LUIS アプリケーションを GZip としてパッケージ化します |
> | Microsoft.CognitiveServices/accounts/LUIS/package/versions/gzip/read | トレーニング済みの LUIS アプリケーションを GZip としてパッケージ化します |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/alert/anomaly/configurations/write | 異常アラート構成を作成または更新します |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/alert/anomaly/configurations/delete | 異常アラート構成を削除します |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/alert/anomaly/configurations/read | 単一の異常アラート構成をクエリします |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/alert/anomaly/configurations/alerts/query/action | 異常アラート構成でアラートをクエリします |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/alert/anomaly/configurations/alerts/anomalies/read | 特定のアラートで異常をクエリします |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/alert/anomaly/configurations/alerts/incidents/read | 特定のアラートでインシデントをクエリします |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/datafeeds/write | データ フィードを作成または更新します。 |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/datafeeds/delete | データ フィードを削除します |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/datafeeds/read | データ フィードをその ID で取得するか、すべてのデータ フィードを一覧表示します |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/datafeeds/ingestionprogress/read | データ フィードによって最後に成功したインジェスト ジョブのタイムスタンプを取得します |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/datafeeds/ingestionprogress/reset/action | データ フィードによってデータのインジェスト状態をリセットしてデータを埋め戻します |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/datafeeds/ingestionstatus/query/action | データ フィードによってデータのインジェスト状態を取得します |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/enrichment/anomalydetection/configurations/write | 異常検出構成を作成または更新します |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/enrichment/anomalydetection/configurations/delete | 異常検出構成を削除します |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/enrichment/anomalydetection/configurations/read | 単一の異常検出構成をクエリします |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/enrichment/anomalydetection/configurations/alert/anomaly/configurations/read | すべての異常アラート構成で、特定の異常検出構成をクエリします |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/enrichment/anomalydetection/configurations/anomalies/query/action | 異常検出構成で異常をクエリします |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/enrichment/anomalydetection/configurations/anomalies/dimension/query/action | 異常のディメンション値をクエリします |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/enrichment/anomalydetection/configurations/incidents/query/action | 異常検出構成でインシデントをクエリします |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/enrichment/anomalydetection/configurations/incidents/rootcause/read | インシデントの根本原因をクエリします |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/enrichment/anomalydetection/configurations/series/query/action | 異常検出によって強化された系列をクエリします |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/feedback/metric/write | 新しいメトリック フィードバックを作成します |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/feedback/metric/read | メトリック フィードバックを ID によって取得します |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/feedback/metric/query/action | 指定されたメトリックに関するフィードバックを一覧表示します |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/hooks/write | フックを作成または更新します |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/hooks/delete | フックを削除します |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/hooks/read | フックを ID によって取得するか、すべてのフックを一覧表示します |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/metrics/data/query/action | メトリックから時系列データを取得します |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/metrics/dimension/query/action | 特定のメトリックからディメンションを一覧表示します |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/metrics/enrichment/anomalydetection/configurations/read | すべての異常検出構成で特定のメトリックをクエリします |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/metrics/series/query/action | メトリックから系列 (ディメンションの組み合わせ) を一覧表示します |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/metrics/status/enrichment/anomalydetection/query/action | 異常検出状態をクエリします |
> | Microsoft.CognitiveServices/accounts/MetricsAdvisor/stats/latest/read | 最新の使用状況統計を取得します |
> | Microsoft.CognitiveServices/accounts/NewsSearch/categorysearch/action | 指定されたカテゴリのニュースを返します。 |
> | Microsoft.CognitiveServices/accounts/NewsSearch/search/action | 特定のクエリに関連するニュース記事を取得します。 |
> | Microsoft.CognitiveServices/accounts/NewsSearch/trendingtopics/action | Bing によって識別される注目のトピックを取得します。 これらは、Bing ホーム ページの下部にあるバナーに表示されるのと同じトピックです。 |
> | Microsoft.CognitiveServices/accounts/Personalizer/rank/action | パーソナル化ランク要求。 |
> | Microsoft.CognitiveServices/accounts/Personalizer/evaluations/action | 新しい評価を送信します。 |
> | Microsoft.CognitiveServices/accounts/Personalizer/configurations/client/action | クライアント構成を取得します。 |
> | Microsoft.CognitiveServices/accounts/Personalizer/configurations/policy/delete | 現在のポリシーを削除します。 |
> | Microsoft.CognitiveServices/accounts/Personalizer/configurations/policy/read | ポリシーの構成を取得します。 |
> | Microsoft.CognitiveServices/accounts/Personalizer/configurations/policy/write | ポリシーの構成を更新します。 |
> | Microsoft.CognitiveServices/accounts/Personalizer/configurations/service/read | サービスの構成を取得します。 |
> | Microsoft.CognitiveServices/accounts/Personalizer/configurations/service/write | サービスの構成を更新します。 |
> | Microsoft.CognitiveServices/accounts/Personalizer/evaluations/delete | ID に関連付けられている評価を削除します。 |
> | Microsoft.CognitiveServices/accounts/Personalizer/evaluations/read | ID に関連付けられている評価を取得します。 送信されたすべての評価を一覧表示します。* |
> | Microsoft.CognitiveServices/accounts/Personalizer/events/reward/action | 指定されたイベントの上位にランク付けされたアクションに割り当てる報酬を報告します。 |
> | Microsoft.CognitiveServices/accounts/Personalizer/events/activate/action | 指定されたイベントが実際にユーザーに表示されていることと、そのイベントに対する報酬が必要であることを報告します。 |
> | Microsoft.CognitiveServices/accounts/Personalizer/logs/delete | すべてのログを削除します。 |
> | Microsoft.CognitiveServices/accounts/Personalizer/logs/properties/read | ログのプロパティを取得します。 |
> | Microsoft.CognitiveServices/accounts/Personalizer/model/read | 現在のモデルを取得します。 |
> | Microsoft.CognitiveServices/accounts/Personalizer/model/delete | モデルをリセットします。 |
> | Microsoft.CognitiveServices/accounts/Personalizer/model/properties/read | モデルのプロパティを取得します。 |
> | Microsoft.CognitiveServices/accounts/Personalizer/status/read | 操作の状態を取得します。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker/root/action | QnA Maker |
> | Microsoft.CognitiveServices/accounts/QnAMaker.v2/root/action | QnA Maker |
> | Microsoft.CognitiveServices/accounts/QnAMaker.v2/alterations/read | ランタイムから変更をダウンロードします。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker.v2/alterations/write | 変更データを置換します。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointkeys/read | エンドポイント用のエンドポイント キーを取得します |
> | Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointkeys/refreshkeys/action | エンドポイント キーを再生成します。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointsettings/read | エンドポイントのエンドポイント設定を取得します |
> | Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointsettings/write | エンドポイントのエンドポイント設定を更新します。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/publish/action | ナレッジベースのテスト インデックス内のすべての変更を、その製品インデックスに発行します。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/delete | ナレッジベースとそのすべてのデータを削除します。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/read | ナレッジベースの一覧または特定のナレッジベースの詳細を取得します。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/write | ナレッジベースを変更する、またはナレッジベースの内容を置き換えるための非同期操作。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/generateanswer/action | ナレッジベースのクエリを実行するための GenerateAnswer 呼び出し。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/train/action | ナレッジベースに提案を追加する呼び出しをトレーニングします。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/create/write | 新しいナレッジベースを作成する非同期操作。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/download/read | ナレッジベースをダウンロードします。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker.v2/operations/read | 長期実行されている特定の操作の詳細を取得します。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker/alterations/read | ランタイムから変更をダウンロードします。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker/alterations/write | 変更データを置換します。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/read | エンドポイント用のエンドポイント キーを取得します |
> | Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/refreshkeys/action | エンドポイント キーを再生成します。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/read | エンドポイントのエンドポイント設定を取得します |
> | Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/write | エンドポイントのエンドポイント設定を更新します。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/publish/action | ナレッジベースのテスト インデックス内のすべての変更を、その製品インデックスに発行します。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/delete | ナレッジベースとそのすべてのデータを削除します。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/read | ナレッジベースの一覧または特定のナレッジベースの詳細を取得します。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/write | ナレッジベースを変更する、またはナレッジベースの内容を置き換えるための非同期操作。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/generateanswer/action | ナレッジベースのクエリを実行するための GenerateAnswer 呼び出し。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/train/action | ナレッジベースに提案を追加する呼び出しをトレーニングします。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/create/write | 新しいナレッジベースを作成する非同期操作。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/download/read | ナレッジベースをダウンロードします。 |
> | Microsoft.CognitiveServices/accounts/QnAMaker/operations/read | 長期実行されている特定の操作の詳細を取得します。 |
> | Microsoft.CognitiveServices/accounts/SpellCheck/spellcheck/action | GET または POST を使用してスペル チェック クエリの結果を取得します。 |
> | Microsoft.CognitiveServices/accounts/TextAnalytics/languages/action | この API は、検出した言語と 0 から 1 までの数値スコアを返します。 1 に近いスコアは、100% の確実性で正しい言語が特定されたことを示します。 合計で 120 種類の言語がサポートされています。 |
> | Microsoft.CognitiveServices/accounts/TextAnalytics/entities/action | この API は、指定されたドキュメント内の既知のエンティティと一般的な名前付きエンティティ (\"Person\"、\"Location\"、\"Organization\" など) を返します。 |
> | Microsoft.CognitiveServices/accounts/TextAnalytics/keyphrases/action | API は、入力テキストの要点を示す文字列のリストを返します。 |
> | Microsoft.CognitiveServices/accounts/TextAnalytics/sentiment/action | この API は 0 から 1 までの数値スコアを返します。<br>1 に近いスコアは正のセンチメントを表し、0 に近いスコアは負のセンチメントを表します。<br>スコア 0.5 はセンチメント不足を示します (例:<br>factoid ステートメントなど)。 |
> | Microsoft.CognitiveServices/accounts/TextAnalytics/analyze/action | 分析用のテキスト ドキュメントのコレクションを送信します。 実行する 1 つまたは複数の一意のタスクを指定します。 |
> | Microsoft.CognitiveServices/accounts/TextTranslation/detect/action | テキストの一部の言語を識別します。 |
> | Microsoft.CognitiveServices/accounts/TextTranslation/breaksentence/action | 文章内で文の境界の位置を識別します。 |
> | Microsoft.CognitiveServices/accounts/TextTranslation/translate/action | テキストを翻訳します。 |
> | Microsoft.CognitiveServices/accounts/TextTranslation/transliterate/action | ある言語のテキストを、あるスクリプトから別のスクリプトに変換します。 |
> | Microsoft.CognitiveServices/accounts/TextTranslation/dictionary/examples/action | 辞書内の用語がコンテキストで使用される方法を示す例を提供します。 この操作は、辞書検索と一緒に使用されます。 |
> | Microsoft.CognitiveServices/accounts/TextTranslation/dictionary/lookup/action | 単語や少数の慣用句に対し代替の翻訳を提供します。<br>各翻訳には品詞と、逆翻訳のリストが含まれます。<br>逆翻訳により、ユーザーはコンテキスト内の翻訳を理解することができます。<br>辞書の例操作では、さらにドリル ダウンすることで、各翻訳ペアの使用例を参照することができます。 |
> | Microsoft.CognitiveServices/accounts/TextTranslation/languages/read | Translator Text API の他の操作で現在サポートされている一連の言語を取得します。 |
> | Microsoft.CognitiveServices/accounts/VideoSearch/trending/action | 現在急上昇中の動画を取得します。 |
> | Microsoft.CognitiveServices/accounts/VideoSearch/details/action | 関連動画など、動画に関する分析情報を取得します。 |
> | Microsoft.CognitiveServices/accounts/VideoSearch/search/action | 特定のクエリに関連する動画を取得します。 |
> | Microsoft.CognitiveServices/accounts/VisualSearch/search/action | 指定された画像に関連するタグの一覧を返します |
> | Microsoft.CognitiveServices/accounts/WebSearch/search/action | 特定のクエリの Web、画像、ニュース、および動画の結果を取得します。 |

### <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

Azure サービス:[Machine Learning Studio (クラシック)](../machine-learning/classic/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.MachineLearning/register/action | Machine Learning Web サービス リソース プロバイダーにサブスクリプションを登録し、Web サービスを作成できるようにします。 |
> | Microsoft.MachineLearning/webServices/action | サポートされているリージョンのリージョン固有の Web サービス プロパティを作成します。 |
> | Microsoft.MachineLearning/commitmentPlans/read | Machine Learning コミットメント プランを読み取ります。 |
> | Microsoft.MachineLearning/commitmentPlans/write | Machine Learning コミットメント プランを作成または更新します。 |
> | Microsoft.MachineLearning/commitmentPlans/delete | Machine Learning コミットメント プランを削除します。 |
> | Microsoft.MachineLearning/commitmentPlans/join/action | Machine Learning コミットメント プランに加入します。 |
> | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | Machine Learning コミットメント プランの関連付けを読み取ります。 |
> | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | Machine Learning コミットメント プランの関連付けを移動します。 |
> | Microsoft.MachineLearning/locations/operationresults/read | Machine Learning 操作の結果を取得します。 |
> | Microsoft.MachineLearning/locations/operationsstatus/read | 実行中の Machine Learning 操作の状態を取得します。 |
> | Microsoft.MachineLearning/operations/read | Machine Learning 操作を取得します。 |
> | Microsoft.MachineLearning/skus/read | Machine Learning コミットメント プラン SKU を取得します。 |
> | Microsoft.MachineLearning/webServices/read | Machine Learning Web サービスを読み取ります。 |
> | Microsoft.MachineLearning/webServices/write | Machine Learning Web サービスを作成または更新します。 |
> | Microsoft.MachineLearning/webServices/delete | Machine Learning Web サービスを削除します。 |
> | Microsoft.MachineLearning/webServices/listkeys/read | Machine Learning Web サービスに対するキーを取得します。 |
> | Microsoft.MachineLearning/Workspaces/read | Machine Learning ワークスペースを読み取ります。 |
> | Microsoft.MachineLearning/Workspaces/write | Machine Learning ワークスペースを作成または更新します。 |
> | Microsoft.MachineLearning/Workspaces/delete | Machine Learning ワークスペースを削除します。 |
> | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | Machine Learning ワークスペースのキーを一覧表示します。 |
> | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | Machine Learning ワークスペース用に構成されたストレージ アカウントのキーを再同期します。 |

### <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

Azure サービス:[Machine Learning service](../machine-learning/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.MachineLearningServices/register/action | Machine Learning Services リソース プロバイダーのサブスクリプションを登録します。 |
> | Microsoft.MachineLearningServices/locations/updateQuotas/action | サブスクリプションまたはワークスペース レベルで各 VM ファミリのクォータを更新します。 |
> | Microsoft.MachineLearningServices/locations/quotas/read | VMFamily に基づいて現在割り当てられているワークスペースのクォータを取得します。 |
> | Microsoft.MachineLearningServices/locations/usages/read | サブスクリプション内の AML コンピューティング リソースの使用状況レポート |
> | Microsoft.MachineLearningServices/locations/vmsizes/read | サポートされる VM のサイズを取得します。 |
> | Microsoft.MachineLearningServices/operations/read | Machine Learning Services リソース プロバイダーの操作をすべて取得します |
> | Microsoft.MachineLearningServices/workspaces/checkComputeNameAvailability/action | Machine Learning Services ワークスペース内のバッチ計算エンドポイントの名前を確認します |
> | Microsoft.MachineLearningServices/workspaces/read | Machine Learning Services ワークスペースを取得します。 |
> | Microsoft.MachineLearningServices/workspaces/write | Machine Learning Services ワークスペースを作成または更新します。 |
> | Microsoft.MachineLearningServices/workspaces/delete | Machine Learning Services ワークスペースを削除します。 |
> | Microsoft.MachineLearningServices/workspaces/listKeys/action | Machine Learning Services ワークスペースのシークレットの一覧を取得します。 |
> | Microsoft.MachineLearningServices/workspaces/resynckeys/action | Machine Learning Services ワークスペースのシークレットを再同期します |
> | Microsoft.MachineLearningServices/workspaces/batchEndpoints/read | Machine Learning Services ワークスペース内のバッチ推論エンドポイントを取得します |
> | Microsoft.MachineLearningServices/workspaces/batchEndpoints/write | Machine Learning Services ワークスペース内のバッチ推論エンドポイントを作成または更新します |
> | Microsoft.MachineLearningServices/workspaces/batchEndpoints/delete | Machine Learning Services ワークスペース内のバッチ推論エンドポイントを削除します |
> | Microsoft.MachineLearningServices/workspaces/batchEndpoints/listKeys/action | Machine Learning Services ワークスペース内のバッチ推論エンドポイントのキーを一覧表示します |
> | Microsoft.MachineLearningServices/workspaces/batchEndpoints/checkNameAvailability/read | Machine Learning Services ワークスペース内のバッチ推論エンドポイントの名前を確認します |
> | Microsoft.MachineLearningServices/workspaces/batchEndpoints/deployments/read | Machine Learning Services ワークスペース内のバッチ推論エンドポイントでのデプロイを取得します |
> | Microsoft.MachineLearningServices/workspaces/batchEndpoints/deployments/write | Machine Learning Services ワークスペース内のバッチ推論エンドポイントでのデプロイを作成または更新します |
> | Microsoft.MachineLearningServices/workspaces/batchEndpoints/deployments/delete | Machine Learning Services ワークスペース内のバッチ推論エンドポイントでのデプロイを削除します |
> | Microsoft.MachineLearningServices/workspaces/batchEndpoints/deployments/checkNameAvailability/read | Machine Learning Services ワークスペース内のバッチ推論エンドポイントでのデプロイの名前を確認します |
> | Microsoft.MachineLearningServices/workspaces/batchEndpoints/deployments/jobs/read | Machine Learning Services ワークスペース内のバッチ推論のデプロイのジョブを読み取ります |
> | Microsoft.MachineLearningServices/workspaces/batchEndpoints/deployments/jobs/write | Machine Learning Services ワークスペース内のバッチ推論のデプロイのジョブを作成または更新します |
> | Microsoft.MachineLearningServices/workspaces/batchEndpoints/deployments/jobs/delete | Machine Learning Services ワークスペース内のバッチ推論のデプロイのジョブを削除します |
> | Microsoft.MachineLearningServices/workspaces/batchEndpoints/jobs/read | Machine Learning Services ワークスペース内のバッチ推論エンドポイントのジョブを読み取ります |
> | Microsoft.MachineLearningServices/workspaces/batchEndpoints/jobs/write | Machine Learning Services ワークスペース内のバッチ推論エンドポイントのジョブを作成または更新します |
> | Microsoft.MachineLearningServices/workspaces/batchEndpoints/jobs/delete | Machine Learning Services ワークスペース内のバッチ推論エンドポイントのジョブを削除します |
> | Microsoft.MachineLearningServices/workspaces/computes/read | Machine Learning Services ワークスペースのコンピューティング リソースを取得します。 |
> | Microsoft.MachineLearningServices/workspaces/computes/write | Machine Learning Services ワークスペースのコンピューティング リソースを作成または更新します。 |
> | Microsoft.MachineLearningServices/workspaces/computes/delete | Machine Learning Services ワークスペースのコンピューティング リソースを削除します。 |
> | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | Machine Learning Services ワークスペースのコンピューティング リソースについて、シークレットの一覧を取得します。 |
> | Microsoft.MachineLearningServices/workspaces/computes/listNodes/action | Machine Learning Services ワークスペースのコンピューティング リソースについて、ノードの一覧を取得します。 |
> | Microsoft.MachineLearningServices/workspaces/computes/start/action | Machine Learning Services ワークスペースでコンピューティング リソースが起動されます |
> | Microsoft.MachineLearningServices/workspaces/computes/stop/action | Machine Learning Services ワークスペースでコンピューティング リソースが停止されます |
> | Microsoft.MachineLearningServices/workspaces/computes/restart/action | Machine Learning Services ワークスペースでコンピューティング リソースが再起動されます |
> | Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action | Machine Learning Services ワークスペースのコンピューティング リソースにアクセスします |
> | Microsoft.MachineLearningServices/workspaces/connections/read | Machine Learning Services ワークスペースの接続を取得します |
> | Microsoft.MachineLearningServices/workspaces/connections/write | Machine Learning Services の接続を作成または更新します |
> | Microsoft.MachineLearningServices/workspaces/connections/delete | Machine Learning Services の接続を削除します |
> | Microsoft.MachineLearningServices/workspaces/datadriftdetectors/read | Machine Learning Services ワークスペース内のデータ ドリフト検出機能を取得します |
> | Microsoft.MachineLearningServices/workspaces/datadriftdetectors/write | Machine Learning Services ワークスペース内のデータ ドリフト検出機能を作成または更新します |
> | Microsoft.MachineLearningServices/workspaces/datadriftdetectors/delete | Machine Learning Services ワークスペース内のデータ ドリフト検出機能を削除します |
> | Microsoft.MachineLearningServices/workspaces/datasets/registered/read | Machine Learning Services ワークスペースに登録されているデータセットを取得します |
> | Microsoft.MachineLearningServices/workspaces/datasets/registered/write | Machine Learning Services ワークスペースに登録されているデータセットを作成または更新します |
> | Microsoft.MachineLearningServices/workspaces/datasets/registered/delete | Machine Learning Services ワークスペースに登録されているデータセットを削除します |
> | Microsoft.MachineLearningServices/workspaces/datasets/registered/preview/read | Machine Learning Services ワークスペースに登録されているデータセットのプレビューを取得します |
> | Microsoft.MachineLearningServices/workspaces/datasets/registered/profile/read | Machine Learning Services ワークスペースに登録されているデータセットのプロファイルを取得します |
> | Microsoft.MachineLearningServices/workspaces/datasets/registered/profile/write | Machine Learning Services ワークスペースに登録されているデータセット用のデータセット プロファイルを作成または更新します |
> | Microsoft.MachineLearningServices/workspaces/datasets/registered/schema/read | Machine Learning Services ワークスペースに登録されているデータセット用のデータセット スキーマを取得します |
> | Microsoft.MachineLearningServices/workspaces/datasets/unregistered/read | Machine Learning Services ワークスペースに登録されていないデータセットを取得します |
> | Microsoft.MachineLearningServices/workspaces/datasets/unregistered/write | Machine Learning Services ワークスペースに登録されていないデータセットを作成または更新します |
> | Microsoft.MachineLearningServices/workspaces/datasets/unregistered/delete | Machine Learning Services ワークスペースに登録されていないデータセットを削除します |
> | Microsoft.MachineLearningServices/workspaces/datasets/unregistered/preview/read | Machine Learning Services ワークスペースに登録されていないデータセットのプレビューを取得します |
> | Microsoft.MachineLearningServices/workspaces/datasets/unregistered/profile/read | Machine Learning Services ワークスペースに登録されていないデータセットのプロファイルを取得します |
> | Microsoft.MachineLearningServices/workspaces/datasets/unregistered/profile/write | Machine Learning Services ワークスペースに登録されていないデータセット用のデータセット プロファイルを作成または更新します |
> | Microsoft.MachineLearningServices/workspaces/datasets/unregistered/schema/read | Machine Learning Services ワークスペースに登録されていないデータセット用のデータセット スキーマを取得します |
> | Microsoft.MachineLearningServices/workspaces/datastores/read | Machine Learning Services ワークスペース内のデータストアを取得します |
> | Microsoft.MachineLearningServices/workspaces/datastores/write | Machine Learning Services ワークスペース内のデータストアを作成または更新します |
> | Microsoft.MachineLearningServices/workspaces/datastores/delete | Machine Learning Services ワークスペース内のデータストアを削除します |
> | Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/read | Machine Learning Services ワークスペースで公開されたパイプラインおよびパイプライン エンドポイントを取得します |
> | Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write | Machine Learning Services ワークスペースで公開されたパイプラインおよびパイプライン エンドポイントを作成または更新します |
> | Microsoft.MachineLearningServices/workspaces/environments/read | Machine Learning Services ワークスペース内の環境を取得します |
> | Microsoft.MachineLearningServices/workspaces/environments/readSecrets/action | シークレットを使用して Machine Learning Services ワークスペース内の環境を取得します |
> | Microsoft.MachineLearningServices/workspaces/environments/write | Machine Learning Services ワークスペース内の環境を作成または更新します |
> | Microsoft.MachineLearningServices/workspaces/environments/build/action | Machine Learning Services ワークスペース内に環境をビルドします |
> | Microsoft.MachineLearningServices/workspaces/eventGridFilters/read | 特定のワークスペースの Event Grid フィルターを取得します |
> | Microsoft.MachineLearningServices/workspaces/experiments/read | Machine Learning Services ワークスペース内の実験を取得します |
> | Microsoft.MachineLearningServices/workspaces/experiments/write | Machine Learning Services ワークスペース内の実験を作成または更新します。 |
> | Microsoft.MachineLearningServices/workspaces/experiments/delete | Machine Learning Services ワークスペース内の実験を削除します |
> | Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action | Machine Learning Services ワークスペース内のスクリプトの実行を作成または更新します |
> | Microsoft.MachineLearningServices/workspaces/experiments/runs/read | Machine Learning Services ワークスペース内の実行を取得します |
> | Microsoft.MachineLearningServices/workspaces/experiments/runs/write | Machine Learning Services ワークスペース内の実行を作成または更新します |
> | Microsoft.MachineLearningServices/workspaces/features/read | Machine Learning Services ワークスペースで有効なすべての機能が取得されます |
> | Microsoft.MachineLearningServices/workspaces/inferenceEndpoints/read | Machine Learning Services ワークスペース内の Inference 推論エンドポイントを取得します |
> | Microsoft.MachineLearningServices/workspaces/inferenceEndpoints/write | Machine Learning Services ワークスペース内の Inference 推論エンドポイントを作成または更新します |
> | Microsoft.MachineLearningServices/workspaces/inferenceEndpoints/delete | Machine Learning Services ワークスペース内の Inference 推論エンドポイントを削除します |
> | Microsoft.MachineLearningServices/workspaces/inferenceEndpoints/checkNameAvailability/read | Machine Learning Services ワークスペース内の Inference 推論エンドポイントの名前を確認します |
> | Microsoft.MachineLearningServices/workspaces/inferenceEndpoints/deployments/read | Machine Learning Services ワークスペース内の Inference 推論エンドポイントのデプロイを取得します |
> | Microsoft.MachineLearningServices/workspaces/inferenceEndpoints/deployments/write | Machine Learning Services ワークスペース内の Inference 推論エンドポイントのデプロイを作成または更新します |
> | Microsoft.MachineLearningServices/workspaces/inferenceEndpoints/deployments/delete | Machine Learning Services ワークスペース内の Inference 推論エンドポイントのデプロイを削除します |
> | Microsoft.MachineLearningServices/workspaces/inferenceEndpoints/deployments/checkNameAvailability/read | Machine Learning Services ワークスペース内の Inference 推論エンドポイントのデプロイの名前を確認します |
> | Microsoft.MachineLearningServices/workspaces/inferenceEndpoints/deployments/skus/read | Machine Learning Services ワークスペース内の Inference 推論エンドポイントのデプロイに対するスケール SKU 設定を取得します |
> | Microsoft.MachineLearningServices/workspaces/labeling/export/action | Machine Learning Services ワークスペースのラベル付けプロジェクトのラベルがエクスポートされます |
> | Microsoft.MachineLearningServices/workspaces/labeling/labels/read | Machine Learning Services ワークスペースのラベル付けプロジェクトのラベルが取得されます |
> | Microsoft.MachineLearningServices/workspaces/labeling/labels/write | Machine Learning Services ワークスペースのラベル付けプロジェクトのラベルが作成されます |
> | Microsoft.MachineLearningServices/workspaces/labeling/labels/reject/action | Machine Learning Services ワークスペースのラベル付けプロジェクトのラベルが拒否されます |
> | Microsoft.MachineLearningServices/workspaces/labeling/projects/read | Machine Learning Services ワークスペースのラベル付けプロジェクトが取得されます |
> | Microsoft.MachineLearningServices/workspaces/labeling/projects/write | Machine Learning Services ワークスペースのラベル付けプロジェクトが作成または更新されます |
> | Microsoft.MachineLearningServices/workspaces/labeling/projects/delete | Machine Learning Services ワークスペースのラベル付けプロジェクトが削除されます |
> | Microsoft.MachineLearningServices/workspaces/labeling/projects/summary/read | Machine Learning Services ワークスペースのラベル付けプロジェクトの概要が取得されます |
> | Microsoft.MachineLearningServices/workspaces/linkedServices/read | Machine Learning Services ワークスペースのすべてのリンク サービスを取得します |
> | Microsoft.MachineLearningServices/workspaces/linkedServices/write | Machine Learning Services ワークスペースのリンク サービスを作成または更新します |
> | Microsoft.MachineLearningServices/workspaces/linkedServices/delete | Machine Learning Services ワークスペースのリンク サービスを削除します |
> | Microsoft.MachineLearningServices/workspaces/metadata/artifacts/read | Machine Learning Services ワークスペース内の成果物を取得します |
> | Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write | Machine Learning Services ワークスペース内の成果物を作成または更新します |
> | Microsoft.MachineLearningServices/workspaces/metadata/artifacts/delete | Machine Learning Services ワークスペース内の成果物を削除します |
> | Microsoft.MachineLearningServices/workspaces/metadata/secrets/read | Machine Learning Services ワークスペース内のシークレットを取得します |
> | Microsoft.MachineLearningServices/workspaces/metadata/secrets/write | Machine Learning Services ワークスペース内のシークレットを作成または更新します |
> | Microsoft.MachineLearningServices/workspaces/metadata/secrets/delete | Machine Learning Services ワークスペース内のシークレットを削除します |
> | Microsoft.MachineLearningServices/workspaces/metadata/snapshots/read | Machine Learning Services ワークスペース内のスナップショットを取得します |
> | Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write | Machine Learning Services ワークスペース内のスナップショットを作成または更新します |
> | Microsoft.MachineLearningServices/workspaces/metadata/snapshots/delete | Machine Learning Services ワークスペース内のスナップショットを削除します |
> | Microsoft.MachineLearningServices/workspaces/metrics/resource/write | Machine Learning Services ワークスペースにリソース メトリックを作成します |
> | Microsoft.MachineLearningServices/workspaces/models/read | Machine Learning Services ワークスペース内のモデルを取得します |
> | Microsoft.MachineLearningServices/workspaces/models/write | Machine Learning Services ワークスペース内のモデルを作成または更新します |
> | Microsoft.MachineLearningServices/workspaces/models/delete | Machine Learning Services ワークスペース内のモデルを削除します |
> | Microsoft.MachineLearningServices/workspaces/models/package/action | Machine Learning Services ワークスペース内のモデルをパッケージします |
> | Microsoft.MachineLearningServices/workspaces/modules/read | Machine Learning Services ワークスペース内のモジュールを取得します |
> | Microsoft.MachineLearningServices/workspaces/modules/write | Machine Learning Services ワークスペース内のモジュールを作成または更新します |
> | Microsoft.MachineLearningServices/workspaces/notebooks/samples/read | サンプル ノートブックを取得します |
> | Microsoft.MachineLearningServices/workspaces/notebooks/storage/read | ワークスペースのノートブック ファイルを取得します |
> | Microsoft.MachineLearningServices/workspaces/notebooks/storage/write | ファイルをワークスペース ストレージに書き込みます |
> | Microsoft.MachineLearningServices/workspaces/notebooks/storage/delete | ワークスペース ストレージからファイルを削除します |
> | Microsoft.MachineLearningServices/workspaces/notebooks/vm/read | 特定のワークスペースの Notebook VM を取得します |
> | Microsoft.MachineLearningServices/workspaces/notebooks/vm/write | Notebook VM の状態を変更します |
> | Microsoft.MachineLearningServices/workspaces/notebooks/vm/delete | Notebook VM を削除します |
> | Microsoft.MachineLearningServices/workspaces/onlineEndpoints/read | Machine Learning Services ワークスペース内のオンライン推論エンドポイントを取得します |
> | Microsoft.MachineLearningServices/workspaces/onlineEndpoints/write | Machine Learning Services ワークスペース内のオンライン推論エンドポイントを作成または更新します |
> | Microsoft.MachineLearningServices/workspaces/onlineEndpoints/delete | Machine Learning Services ワークスペース内のオンライン推論エンドポイントを削除します |
> | Microsoft.MachineLearningServices/workspaces/onlineEndpoints/score/action | Machine Learning Services ワークスペース内のオンライン推論エンドポイントをスコア付けします |
> | Microsoft.MachineLearningServices/workspaces/onlineEndpoints/checkNameAvailability/read | Machine Learning Services ワークスペース内のオンライン推論エンドポイントの名前を確認します |
> | Microsoft.MachineLearningServices/workspaces/onlineEndpoints/deployments/read | Machine Learning Services ワークスペース内のオンライン推論エンドポイントのデプロイを取得します |
> | Microsoft.MachineLearningServices/workspaces/onlineEndpoints/deployments/write | Machine Learning Services ワークスペース内のオンライン推論エンドポイントのデプロイを作成または更新します |
> | Microsoft.MachineLearningServices/workspaces/onlineEndpoints/deployments/delete | Machine Learning Services ワークスペース内のオンライン推論エンドポイントのデプロイを削除します |
> | Microsoft.MachineLearningServices/workspaces/onlineEndpoints/deployments/checkNameAvailability/read | Machine Learning Services ワークスペース内のオンライン推論エンドポイントのデプロイの名前を確認します |
> | Microsoft.MachineLearningServices/workspaces/onlineEndpoints/deployments/skus/read | Machine Learning Services ワークスペース内のオンライン推論エンドポイントのデプロイに対するスケール SKU 設定を取得します |
> | Microsoft.MachineLearningServices/workspaces/pipelinedrafts/read | Machine Learning Services ワークスペース内のパイプライン ドラフトを取得します |
> | Microsoft.MachineLearningServices/workspaces/pipelinedrafts/write | Machine Learning Services ワークスペース内のパイプライン ドラフトを作成または更新します |
> | Microsoft.MachineLearningServices/workspaces/pipelinedrafts/delete | Machine Learning Services ワークスペース内のパイプライン ドラフトを削除します |
> | Microsoft.MachineLearningServices/workspaces/reports/read | Machine Learning Services ワークスペース内のカスタム レポートを取得します |
> | Microsoft.MachineLearningServices/workspaces/reports/write | Machine Learning Services ワークスペース内のカスタム レポートを作成または更新します |
> | Microsoft.MachineLearningServices/workspaces/reports/delete | Machine Learning Services ワークスペース内のカスタム レポートを削除します |
> | Microsoft.MachineLearningServices/workspaces/services/read | Machine Learning Services ワークスペース内のサービスを取得します |
> | Microsoft.MachineLearningServices/workspaces/services/aci/write | Machine Learning Services ワークスペース内の ACI サービスを作成または更新します |
> | Microsoft.MachineLearningServices/workspaces/services/aci/listkeys/action | Machine Learning Services ワークスペース内の ACI サービスのキーを一覧表示します |
> | Microsoft.MachineLearningServices/workspaces/services/aci/delete | Machine Learning Services ワークスペース内の ACI サービスを削除します |
> | Microsoft.MachineLearningServices/workspaces/services/aks/write | Machine Learning Services ワークスペース内の AKS サービスを作成または更新します |
> | Microsoft.MachineLearningServices/workspaces/services/aks/listkeys/action | Machine Learning Services ワークスペース内の AKS サービスのキーを一覧表示します |
> | Microsoft.MachineLearningServices/workspaces/services/aks/delete | Machine Learning Services ワークスペース内の AKS サービスを削除します |
> | Microsoft.MachineLearningServices/workspaces/services/aks/score/action | Machine Learning Services ワークスペース内の AKS サービスをスコア付けします |

## <a name="internet-of-things"></a>モノのインターネット

### <a name="microsoftdevices"></a>Microsoft.Devices

Azure サービス:[IoT Hub](../iot-hub/index.yml), [IoT Hub Device Provisioning Service](../iot-dps/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Devices/register/action | IotHub リソース プロバイダーにサブスクリプションを登録し、IotHub リソースを作成できるようにします。 |
> | Microsoft.Devices/checkNameAvailability/Action | IotHub 名を使用できるかどうかを確認します。 |
> | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | プロビジョニング サービス名を使用できるかどうかを確認します。 |
> | Microsoft.Devices/register/action | IotHub リソース プロバイダーにサブスクリプションを登録し、IotHub リソースを作成できるようにします。 |
> | Microsoft.Devices/checkNameAvailability/Action | IotHub 名を使用できるかどうかを確認します。 |
> | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | プロビジョニング サービス名を使用できるかどうかを確認します。 |
> | Microsoft.Devices/register/action | IotHub リソース プロバイダーにサブスクリプションを登録し、IotHub リソースを作成できるようにします。 |
> | Microsoft.Devices/Account/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Microsoft.Devices/Account/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Microsoft.Devices/Account/logDefinitions/read | IotHub サービスで使用可能なログ定義を取得します。 |
> | Microsoft.Devices/Account/metricDefinitions/read | IotHub サービスの利用可能なメトリックを取得します。 |
> | Microsoft.Devices/digitalTwins/Read | サブスクリプションに関連付けられている Digital Twins アカウントの一覧を取得します |
> | Microsoft.Devices/digitalTwins/Write | 新しい Digital Twins アカウントを作成します |
> | Microsoft.Devices/digitalTwins/Delete | 既存の Digital Twins アカウントとそのすべての子を削除します |
> | Microsoft.Devices/digitalTwins/operationresults/Read | Digital Twins アカウントに対する操作の状態を取得します |
> | Microsoft.Devices/digitalTwins/skus/Read | Digital Twins アカウントの有効な SKU の一覧を取得します |
> | Microsoft.Devices/ElasticPools/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Microsoft.Devices/ElasticPools/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Microsoft.Devices/elasticPools/eventGridFilters/Write | 新しい Elastic Pool Event Grid フィルターを作成するか、既存の Elastic Pool Event Grid フィルターを更新します |
> | Microsoft.Devices/elasticPools/eventGridFilters/Read | Elastic Pool Event Grid フィルターを取得します。 |
> | Microsoft.Devices/elasticPools/eventGridFilters/Delete | Elastic Pool Event Grid フィルターを削除します。 |
> | Microsoft.Devices/elasticPools/iotHubTenants/Write | IotHub テナントのリソースを作成または更新します。 |
> | Microsoft.Devices/elasticPools/iotHubTenants/Read | IotHub テナントのリソースを取得します。 |
> | Microsoft.Devices/elasticPools/iotHubTenants/Delete | IotHub テナントのリソースを削除します。 |
> | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | IotHub テナントのキーを取得します。 |
> | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | デバイスをエクスポートします。 |
> | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | デバイスをインポートします。 |
> | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Read | 証明書を取得します。 |
> | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Write | 証明書を作成または更新します。 |
> | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Delete | 証明書を削除します。 |
> | Microsoft.Devices/elasticPools/iotHubTenants/certificates/generateVerificationCode/Action | 確認コードを生成します。 |
> | Microsoft.Devices/elasticPools/iotHubTenants/certificates/verify/Action | 証明書リソースを確認します。 |
> | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | EventHub コンシューマー グループを作成します。 |
> | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | EventHub コンシューマー グループを取得します。 |
> | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | EventHub コンシューマー グループを削除します。 |
> | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | IotHub テナントの統計リソースを取得します |
> | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | IotHub テナントのキーを取得します。 |
> | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | 特定の IotHub で送信されたジョブの詳細を取得します。 |
> | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | IotHub サービスで使用可能なログ定義を取得します。 |
> | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | IotHub サービスの利用可能なメトリックを取得します。 |
> | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | クォータ メトリックを取得します。 |
> | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | 既存のすべてのルートに対してメッセージをテストします。 |
> | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | 指定されたテスト ルートに対してメッセージをテストします。 |
> | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | IotHub のすべてのルーティング エンドポイントの正常性を取得します。 |
> | Microsoft.Devices/elasticPools/iotHubTenants/securitySettings/Write | IoT テナント ハブの Azure Security Center 設定を更新します |
> | Microsoft.Devices/elasticPools/iotHubTenants/securitySettings/Read | IoT テナント ハブの Azure Security Center 設定を取得します |
> | Microsoft.Devices/elasticPools/iotHubTenants/securitySettings/operationResults/Read | IoT テナント ハブの SecuritySettings の非同期 PUT 操作の結果を取得します |
> | Microsoft.Devices/ElasticPools/metricDefinitions/read | IotHub サービスの利用可能なメトリックを取得します。 |
> | Microsoft.Devices/iotHubs/Read | IotHub リソースを取得します。 |
> | Microsoft.Devices/iotHubs/Write | IotHub リソースを作成または更新します。 |
> | Microsoft.Devices/iotHubs/Delete | IotHub リソースを削除します。 |
> | Microsoft.Devices/iotHubs/listkeys/Action | すべての IotHub キーを取得します。 |
> | Microsoft.Devices/iotHubs/exportDevices/Action | デバイスをエクスポートします。 |
> | Microsoft.Devices/iotHubs/importDevices/Action | デバイスをインポートします。 |
> | Microsoft.Devices/iotHubs/privateEndpointConnectionsApproval/Action | プライベート エンドポイント接続を承認または拒否します |
> | Microsoft.Devices/iotHubs/certificates/Read | 証明書を取得します。 |
> | Microsoft.Devices/iotHubs/certificates/Write | 証明書を作成または更新します。 |
> | Microsoft.Devices/iotHubs/certificates/Delete | 証明書を削除します。 |
> | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | 確認コードを生成します。 |
> | Microsoft.Devices/iotHubs/certificates/verify/Action | 証明書リソースを確認します。 |
> | Microsoft.Devices/iotHubs/certificates/Read | 証明書を取得します。 |
> | Microsoft.Devices/iotHubs/certificates/Write | 証明書を作成または更新します。 |
> | Microsoft.Devices/iotHubs/certificates/Delete | 証明書を削除します。 |
> | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | 確認コードを生成します。 |
> | Microsoft.Devices/iotHubs/certificates/verify/Action | 証明書リソースを確認します。 |
> | Microsoft.Devices/IotHubs/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Microsoft.Devices/IotHubs/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Microsoft.Devices/iotHubs/digitalTwinsLinks/Write | IotHub と Digital Twins サービスにリンクを設定します |
> | Microsoft.Devices/iotHubs/digitalTwinsLinks/Read | 現在リンクされている Digital Twins サービスに関する情報を取得します |
> | Microsoft.Devices/iotHubs/digitalTwinsLinks/Delete | Digital Twins サービスへのリンクを削除します |
> | Microsoft.Devices/iotHubs/eventGridFilters/Write | 新しい Event Grid フィルターを作成するか、既存の Event Grid フィルターを更新します。 |
> | Microsoft.Devices/iotHubs/eventGridFilters/Read | Event Grid フィルターを取得します。 |
> | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Event Grid フィルターを削除します。 |
> | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | EventHub コンシューマー グループを作成します。 |
> | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | EventHub コンシューマー グループを取得します。 |
> | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | EventHub コンシューマー グループを削除します。 |
> | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | 特定の名前の IotHub キーを取得します。 |
> | Microsoft.Devices/iotHubs/iotHubStats/Read | IotHub の統計を取得します。 |
> | Microsoft.Devices/iotHubs/jobs/Read | 特定の IotHub で送信されたジョブの詳細を取得します。 |
> | Microsoft.Devices/IotHubs/logDefinitions/read | IotHub サービスで使用可能なログ定義を取得します。 |
> | Microsoft.Devices/IotHubs/metricDefinitions/read | IotHub サービスの利用可能なメトリックを取得します。 |
> | Microsoft.Devices/iotHubs/operationresults/Read | 操作の結果を取得します (古い API)。 |
> | Microsoft.Devices/iotHubs/privateEndpointConnectionProxies/validate/Action | 作成時のプライベート エンドポイント接続プロキシの入力を検証します |
> | Microsoft.Devices/iotHubs/privateEndpointConnectionProxies/Read | 指定されたプライベート エンドポイント接続プロキシのプロパティを取得します |
> | Microsoft.Devices/iotHubs/privateEndpointConnectionProxies/Write | プライベート エンドポイント接続プロキシを作成または更新します |
> | Microsoft.Devices/iotHubs/privateEndpointConnectionProxies/Delete | 既存のプライベート エンドポイント接続プロキシを削除します |
> | Microsoft.Devices/iotHubs/privateEndpointConnectionProxies/operationResults/Read | プライベート エンドポイント接続プロキシの非同期操作の結果を取得します |
> | Microsoft.Devices/iotHubs/privateEndpointConnections/Read | 指定された IoT Hub のすべてのプライベート エンドポイント接続を取得します |
> | Microsoft.Devices/iotHubs/privateEndpointConnections/Delete | 既存のプライベート エンドポイント接続を削除します |
> | Microsoft.Devices/iotHubs/privateEndpointConnections/Write | プライベート エンドポイント接続を作成または更新します |
> | Microsoft.Devices/iotHubs/privateEndpointConnections/operationResults/Read | プライベート エンドポイント接続の非同期操作の結果を取得します |
> | Microsoft.Devices/iotHubs/privateLinkResources/Read | IotHub のプライベート リンク リソースを取得します |
> | Microsoft.Devices/iotHubs/quotaMetrics/Read | クォータ メトリックを取得します。 |
> | Microsoft.Devices/iotHubs/routing/$testall/Action | 既存のすべてのルートに対してメッセージをテストします。 |
> | Microsoft.Devices/iotHubs/routing/$testnew/Action | 指定されたテスト ルートに対してメッセージをテストします。 |
> | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | IotHub のすべてのルーティング エンドポイントの正常性を取得します。 |
> | Microsoft.Devices/iotHubs/securitySettings/Write | IoT ハブの Azure Security Center 設定を更新します |
> | Microsoft.Devices/iotHubs/securitySettings/Read | IoT ハブの Azure Security Center 設定を取得します |
> | Microsoft.Devices/iotHubs/securitySettings/operationResults/Read | IoT ハブの SecuritySettings の非同期 PUT 操作の結果を取得します |
> | Microsoft.Devices/iotHubs/skus/Read | 有効な IotHub SKU を取得します。 |
> | Microsoft.Devices/locations/operationresults/Read | 場所に基づく操作の結果を取得します。 |
> | Microsoft.Devices/locations/operationresults/Read | 場所に基づく操作の結果を取得します。 |
> | Microsoft.Devices/operationresults/Read | 操作の結果を取得します。 |
> | Microsoft.Devices/operationresults/Read | 操作の結果を取得します。 |
> | Microsoft.Devices/operations/Read | ResourceProvider のすべての操作を取得します。 |
> | Microsoft.Devices/operations/Read | ResourceProvider のすべての操作を取得します。 |
> | Microsoft.Devices/provisioningServices/Read | IotDps リソースを取得します。 |
> | Microsoft.Devices/provisioningServices/Write | IotDps リソースを作成します。 |
> | Microsoft.Devices/provisioningServices/Delete | IotDps リソースを削除します。 |
> | Microsoft.Devices/provisioningServices/listkeys/Action | すべての IotDps キーを取得します。 |
> | Microsoft.Devices/provisioningServices/privateEndpointConnectionsApproval/Action | プライベート エンドポイント接続を承認または拒否します |
> | Microsoft.Devices/provisioningServices/certificates/Read | 証明書を取得します。 |
> | Microsoft.Devices/provisioningServices/certificates/Write | 証明書を作成または更新します。 |
> | Microsoft.Devices/provisioningServices/certificates/Delete | 証明書を削除します。 |
> | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | 確認コードを生成します。 |
> | Microsoft.Devices/provisioningServices/certificates/verify/Action | 証明書リソースを確認します。 |
> | Microsoft.Devices/provisioningServices/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Microsoft.Devices/provisioningServices/diagnosticSettings/write | リソースの診断設定を作成または更新します。 |
> | Microsoft.Devices/provisioningServices/keys/listkeys/Action | キー名の IotDps キーを取得します。 |
> | Microsoft.Devices/provisioningServices/logDefinitions/read | プロビジョニング サービスで使用可能なログ定義を取得します。 |
> | Microsoft.Devices/provisioningServices/metricDefinitions/read | プロビジョニング サービスで使用可能なメトリックを取得します。 |
> | Microsoft.Devices/provisioningServices/operationresults/Read | DPS 操作の結果を取得します。 |
> | Microsoft.Devices/provisioningServices/privateEndpointConnectionProxies/validate/Action | 作成時のプライベート エンドポイント接続プロキシの入力を検証します |
> | Microsoft.Devices/provisioningServices/privateEndpointConnectionProxies/Read | 指定されたプライベート エンドポイント接続プロキシのプロパティを取得します |
> | Microsoft.Devices/provisioningServices/privateEndpointConnectionProxies/Write | プライベート エンドポイント接続プロキシを作成または更新します |
> | Microsoft.Devices/provisioningServices/privateEndpointConnectionProxies/Delete | 既存のプライベート エンドポイント接続プロキシを削除します |
> | Microsoft.Devices/provisioningServices/privateEndpointConnectionProxies/operationResults/Read | プライベート エンドポイント接続プロキシの非同期操作の結果を取得します |
> | Microsoft.Devices/provisioningServices/privateEndpointConnections/Read | 指定された IoT Hub のすべてのプライベート エンドポイント接続を取得します |
> | Microsoft.Devices/provisioningServices/privateEndpointConnections/Delete | 既存のプライベート エンドポイント接続を削除します |
> | Microsoft.Devices/provisioningServices/privateEndpointConnections/Write | プライベート エンドポイント接続を作成または更新します |
> | Microsoft.Devices/provisioningServices/privateEndpointConnections/operationResults/Read | プライベート エンドポイント接続の非同期操作の結果を取得します |
> | Microsoft.Devices/provisioningServices/privateLinkResources/Read | IotHub のプライベート リンク リソースを取得します |
> | Microsoft.Devices/provisioningServices/skus/Read | 有効な IotDps SKU を取得します。 |
> | Microsoft.Devices/usages/Read | このプロバイダーのサブスクリプションの使用状況の詳細を取得します。 |
> | Microsoft.Devices/usages/Read | このプロバイダーのサブスクリプションの使用状況の詳細を取得します。 |

### <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

Azure サービス:[IoT Central](../iot-central/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.IoTCentral/checkNameAvailability/action | IoT Central アプリケーションの名前が使用可能かどうかを確認します |
> | Microsoft.IoTCentral/checkSubdomainAvailability/action | IoT Central アプリケーションのサブドメインが使用可能かどうかを確認します |
> | Microsoft.IoTCentral/appTemplates/action | Azure IoT Central で使用可能なすべてのアプリケーション テンプレートを取得します |
> | Microsoft.IoTCentral/register/action | Azure IoT Central リソース プロバイダーのサブスクリプションを登録します |
> | Microsoft.IoTCentral/IoTApps/read | 単一の IoT Central アプリケーションを取得します |
> | Microsoft.IoTCentral/IoTApps/write | IoT Central アプリケーションを作成または更新します |
> | Microsoft.IoTCentral/IoTApps/delete | IoT Central アプリケーションを削除します |
> | Microsoft.IoTCentral/operations/read | IoT Central アプリケーションで使用可能なすべての操作を取得します |

### <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

Azure サービス:[Notification Hubs](../notification-hubs/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.NotificationHubs/register/action | NotificationHubs リソース プロバイダーにサブスクリプションを登録し、名前空間と NotificationHubs を作成できるようにします。 |
> | Microsoft.NotificationHubs/unregister/action | NotificationHubs リソース プロバイダーのサブスクリプションを登録解除し、名前空間と NotificationHubs を作成できるようにします。 |
> | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | 指定された名前空間リソース名を NotificationHub サービス内で使用できるかどうかを確認します。 |
> | Microsoft.NotificationHubs/Namespaces/write | 名前空間リソースを作成し、そのプロパティを更新します。 名前空間のタグとキャパシティは、更新できるプロパティです。 |
> | Microsoft.NotificationHubs/Namespaces/read | 名前空間リソースの説明の一覧を取得します。 |
> | Microsoft.NotificationHubs/Namespaces/Delete | 名前空間リソースを削除します。 |
> | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | 名前空間の承認規則の説明の一覧を取得します。 |
> | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | 指定された NotificationHub 名を名前空間内で使用できるかどうかを確認します。 |
> | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | 名前空間レベルの承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権、プライマリ キー、セカンダリ キーを更新できます。 |
> | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | 名前空間の承認規則の説明の一覧を取得します。 |
> | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | 名前空間の承認規則を削除します。 名前空間の既定の承認規則は削除できません。  |
> | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | 名前空間への接続文字列を取得します。 |
> | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | 名前空間の承認規則では、Primary/SecondaryKey を再生成します。再生成する必要があるキーを指定します。 |
> | Microsoft.NotificationHubs/namespaces/diagnosticSettings/read | 名前空間の診断設定リソースの説明の一覧を取得します。 |
> | Microsoft.NotificationHubs/namespaces/diagnosticSettings/write | 名前空間の診断設定リソースの説明の一覧を取得します。 |
> | Microsoft.NotificationHubs/namespaces/logDefinitions/read | 名前空間のログ リソースの説明の一覧を取得します。 |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Notification Hub を作成し、そのプロパティを更新します。 プロパティには、主に、PNS 資格情報、 承認規則、TTL が含まれます。 |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Notification Hub リソースの説明の一覧を取得します。 |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | Notification Hub リソースを削除します。 |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Notification Hub の承認規則の一覧を取得します。 |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Notification Hub の PNS の資格情報をすべて取得します。 これには、WNS、MPNS、APNS、GCM、Baidu の各資格情報が含まれます。 |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | テスト プッシュ通知を送信します。 |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Notification Hub の承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権、プライマリ キー、セカンダリ キーを更新できます。 |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Notification Hub の承認規則の一覧を取得します。 |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Notification Hub の承認規則を削除します。 |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Notification Hub への接続文字列を取得します。 |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Notification Hub の承認規則では、Primary/SecondaryKey を再生成します。再生成する必要があるキーを指定します。 |
> | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | 名前空間のメトリック リソースの説明の一覧を取得します。 |
> | Microsoft.NotificationHubs/operationResults/read | Notification Hubs プロバイダーの操作の結果を返します |
> | Microsoft.NotificationHubs/operations/read | Notification Hubs プロバイダーでサポートされている操作の一覧を返します |

### <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

Azure サービス:[Time Series Insights](../time-series-insights/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.TimeSeriesInsights/register/action | Time Series Insights リソース プロバイダーにサブスクリプションを登録し、Time Series Insights 環境を作成できるようにします。 |
> | Microsoft.TimeSeriesInsights/environments/read | 環境のプロパティを取得します。 |
> | Microsoft.TimeSeriesInsights/environments/write | 新しい環境を作成するか、既存の環境を更新します。 |
> | Microsoft.TimeSeriesInsights/environments/delete | 環境を削除します。 |
> | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | アクセス ポリシーのプロパティを取得します。 |
> | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | 環境に対する新しいアクセス ポリシーを作成するか、既存のアクセス ポリシーを更新します。 |
> | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | アクセス ポリシーを削除します。 |
> | Microsoft.TimeSeriesInsights/environments/eventsources/read | イベント ソースのプロパティを取得します。 |
> | Microsoft.TimeSeriesInsights/environments/eventsources/write | 環境に対する新しいイベント ソースを作成するか、既存のイベント ソースを更新します。 |
> | Microsoft.TimeSeriesInsights/environments/eventsources/delete | イベント ソースを削除します。 |
> | Microsoft.TimeSeriesInsights/environments/privateendpointConnections/read | プライベート エンドポイント接続のプロパティを取得します。 |
> | Microsoft.TimeSeriesInsights/environments/privateendpointConnections/write | 環境に新しいプライベート エンドポイント接続を作成するか、既存の接続を更新します。 |
> | Microsoft.TimeSeriesInsights/environments/privateendpointConnections/delete | プライベート エンドポイント接続を削除します。 |
> | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | 参照データ セットのプロパティを取得します。 |
> | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | 環境に対する新しい参照データ セットを作成するか、既存の参照データ セットを更新します。 |
> | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | 参照データ セットを削除します。 |
> | Microsoft.TimeSeriesInsights/environments/status/read | 環境の状態、およびそれに関連するイングレスなどの操作の状態を取得します。 |

## <a name="mixed-reality"></a>複合現実

### <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

Azure サービス:[Azure Digital Twins](../digital-twins/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.IoTSpaces/register/action | Microsoft.IoTSpaces Graph リソース プロバイダーのサブスクリプションを登録して、リソースの作成を有効にします |
> | Microsoft.IoTSpaces/Graph/write | Microsoft.IoTSpaces Graph リソースを作成します |
> | Microsoft.IoTSpaces/Graph/read | Microsoft.IoTSpaces Graph リソースを取得します |
> | Microsoft.IoTSpaces/Graph/delete | Microsoft.IoTSpaces Graph リソースを削除します |

### <a name="microsoftmixedreality"></a>Microsoft.MixedReality

Azure サービス:[Azure Spatial Anchors](../spatial-anchors/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.MixedReality/register/action | Mixed Reality リソース プロバイダーのサブスクリプションを登録します。 |
> | Microsoft.MixedReality/unregister/action | Mixed Reality リソース プロバイダーのサブスクリプションを登録解除します。 |
> | Microsoft.MixedReality/locations/checknameavailability/read | 名前を使用できるかどうかを確認します |
> | Microsoft.MixedReality/ObjectAnchorsAccounts/delete | Object Anchors アカウントを削除します |
> | Microsoft.MixedReality/ObjectAnchorsAccounts/listkeys/action | Object Anchors アカウントのキーを一覧表示します |
> | Microsoft.MixedReality/ObjectAnchorsAccounts/read | Object Anchors アカウントのプロパティを読み取ります |
> | Microsoft.MixedReality/ObjectAnchorsAccounts/regeneratekeys/action | Object Anchors アカウントのキーを再生成します |
> | Microsoft.MixedReality/ObjectAnchorsAccounts/write | Object Anchors アカウントのプロパティを更新します |
> | Microsoft.MixedReality/ObjectUnderstandingAccounts/delete | Object Understanding アカウントを削除します |
> | Microsoft.MixedReality/ObjectUnderstandingAccounts/listkeys/action | Object Understanding アカウントのキーを一覧表示します |
> | Microsoft.MixedReality/ObjectUnderstandingAccounts/read | Object Understanding アカウントのプロパティを読み取ります |
> | Microsoft.MixedReality/ObjectUnderstandingAccounts/regeneratekeys/action | Object Understanding アカウントのキーを再生成します |
> | Microsoft.MixedReality/ObjectUnderstandingAccounts/write | Object Understanding アカウントのプロパティを更新します |
> | Microsoft.MixedReality/operations/read | Microsoft Mixed Reality で利用可能な操作を一覧表示します |
> | Microsoft.MixedReality/RemoteRenderingAccounts/delete | Remote Rendering アカウントを削除します |
> | Microsoft.MixedReality/RemoteRenderingAccounts/listkeys/action | Remote Rendering アカウントのキーを一覧表示します |
> | Microsoft.MixedReality/RemoteRenderingAccounts/read | Remote Rendering アカウントのプロパティを読み取ります |
> | Microsoft.MixedReality/RemoteRenderingAccounts/regeneratekeys/action | Remote Rendering アカウントのキーを再生成します |
> | Microsoft.MixedReality/RemoteRenderingAccounts/write | Remote Rendering アカウントのプロパティを更新します |
> | Microsoft.MixedReality/RemoteRenderingAccounts/keys/read | Remote Rendering アカウントのキーを読み取ります |
> | Microsoft.MixedReality/remoteRenderingAccounts/providers/Microsoft.Insights/metricDefinitions/read | Microsoft.MixedReality/remoteRenderingAccounts の使用可能なメトリックが取得されます |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/listkeys/action | Spatial Anchors アカウントのキーを一覧表示します |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/read | Spatial Anchors アカウントのプロパティを読み取ります |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/regeneratekeys/action | Spatial Anchors アカウントのキーを再生成します |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/keys/read | Spatial Anchors アカウントのキーを読み取ります |
> | Microsoft.MixedReality/spatialAnchorsAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Microsoft.MixedReality/spatialAnchorsAccounts の診断設定を取得します |
> | Microsoft.MixedReality/spatialAnchorsAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Microsoft.MixedReality/spatialAnchorsAccounts の診断設定を作成または更新します |
> | Microsoft.MixedReality/spatialAnchorsAccounts/providers/Microsoft.Insights/metricDefinitions/read | Microsoft.MixedReality/spatialAnchorsAccounts の使用可能なメトリックを取得します |
> | **DataAction** | **説明** |
> | Microsoft.MixedReality/ObjectAnchorsAccounts/ingest/action | モデル インジェスト ジョブを作成します |
> | Microsoft.MixedReality/ObjectAnchorsAccounts/ingest/read | モデル インジェスト ジョブの状態を取得します |
> | Microsoft.MixedReality/ObjectUnderstandingAccounts/ingest/action | モデル インジェスト ジョブを作成します |
> | Microsoft.MixedReality/ObjectUnderstandingAccounts/ingest/read | モデル インジェスト ジョブの状態を取得します |
> | Microsoft.MixedReality/RemoteRenderingAccounts/convert/action | 資産の変換を開始します |
> | Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action | セッションを開始します |
> | Microsoft.MixedReality/RemoteRenderingAccounts/convert/read | 資産の変換プロパティを取得します |
> | Microsoft.MixedReality/RemoteRenderingAccounts/convert/delete | 資産の変換を停止します |
> | Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read | Remote Rendering インスペクターに接続します |
> | Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read | セッションのプロパティを取得します |
> | Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete | セッションを停止します |
> | Microsoft.MixedReality/RemoteRenderingAccounts/render/read | セッションに接続します |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | 空間アンカーを作成します。 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | 空間アンカーを削除します。 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | 空間アンカーのプロパティを更新します。 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | 近くにある空間アンカーを検出します。 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | 空間アンカーのプロパティを取得します。 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | 空間アンカーを探します。 |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Azure Spatial Anchors サービスの品質を改善するために診断データを送信します。 |

## <a name="integration"></a>統合

### <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

Azure サービス:[API Management](../api-management/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.ApiManagement/register/action | Microsoft.ApiManagement リソース プロバイダーにサブスクリプションを登録します。 |
> | Microsoft.ApiManagement/unregister/action | Microsoft.ApiManagement リソース プロバイダーへのサブスクリプションの登録を解除します。 |
> | Microsoft.ApiManagement/checkNameAvailability/read | 指定されたサービス名を使用できるかどうかを確認します。 |
> | Microsoft.ApiManagement/deletedservices/read | 削除された API Management サービスを取得します。このサービスは、論理的な削除期間内であれば復元できます |
> | Microsoft.ApiManagement/locations/deletedservices/delete | 復元するオプションを指定せずに、API Management サービスを削除します |
> | Microsoft.ApiManagement/operations/read | Microsoft.ApiManagement リソースで使用可能なすべての API 操作を読み取ります。 |
> | Microsoft.ApiManagement/reports/read | 期間、地理的地域、開発者、製品、API、操作、サブスクリプション、要求ごとに集計されたレポートを取得します。 |
> | Microsoft.ApiManagement/service/write | API Management サービスのインスタンスが作成または更新されます |
> | Microsoft.ApiManagement/service/read | API Management サービス インスタンスのメタデータの読み取り |
> | Microsoft.ApiManagement/service/delete | API Management サービス インスタンスを削除します。 |
> | Microsoft.ApiManagement/service/updatehostname/action | API Management サービスのカスタム ドメイン名を設定、更新、または削除します。 |
> | Microsoft.ApiManagement/service/updatecertificate/action | API Management サービスの TLS/SSL 証明書をアップロードします。 |
> | Microsoft.ApiManagement/service/backup/action | ユーザーが指定したストレージ アカウント内の指定されたコンテナーに API Management サービスをバックアップします。 |
> | Microsoft.ApiManagement/service/restore/action | ユーザーが指定したストレージ アカウント内の指定されたコンテナーからの API Management サービスの復元 |
> | Microsoft.ApiManagement/service/managedeployments/action | SKU/ユニット数を変更し、API Management サービスのリージョン デプロイを追加または削除します。 |
> | Microsoft.ApiManagement/service/getssotoken/action | API Management サービスの従来のポータルに管理者としてログインする際に使用できる SSO トークンを取得します。 |
> | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | 更新済みのネットワーク設定を選択するために、仮想ネットワークで実行されている Microsoft.ApiManagement リソースを更新します。 |
> | Microsoft.ApiManagement/service/users/action | 新しいユーザーを登録します。 |
> | Microsoft.ApiManagement/service/notifications/action | 指定されたユーザーに通知を送信します。 |
> | Microsoft.ApiManagement/service/gateways/action | ゲートウェイ構成を取得します。 またはゲートウェイのハートビートを更新します。 |
> | Microsoft.ApiManagement/service/apis/read | API Management サービス インスタンスのすべての API を一覧表示します。 または、識別子によって指定された API の詳細を取得します。 |
> | Microsoft.ApiManagement/service/apis/write | API Management サービス インスタンスの新しい API を作成するか、既存の指定された API を更新します。 または、API Management サービス インスタンスの指定された API を更新します。 |
> | Microsoft.ApiManagement/service/apis/delete | API Management サービス インスタンスの指定された API を削除します。 |
> | Microsoft.ApiManagement/service/apis/diagnostics/read | API のすべての診断を一覧表示します。 または、識別子によって指定された API の診断の詳細を取得します。 |
> | Microsoft.ApiManagement/service/apis/diagnostics/write | API の新しい診断を作成するか、既存のものを更新します。 または、識別子によって指定された API の診断の詳細を更新します。 |
> | Microsoft.ApiManagement/service/apis/diagnostics/delete | 指定された診断を API から削除します。 |
> | Microsoft.ApiManagement/service/apis/issues/read | 指定された API に関連付けられているすべての問題を一覧表示します。 または、識別子によって指定された API の問題の詳細を取得します。 |
> | Microsoft.ApiManagement/service/apis/issues/write | API の新しい問題を作成するか、既存のものを更新します。 または、API の既存の問題を更新します。 |
> | Microsoft.ApiManagement/service/apis/issues/delete | 指定された問題を API から削除します。 |
> | Microsoft.ApiManagement/service/apis/issues/attachments/read | 指定された API に関連付けられている問題のすべての添付ファイルを一覧表示します。 または、識別子によって指定された API の問題の添付ファイルの詳細を取得します。 |
> | Microsoft.ApiManagement/service/apis/issues/attachments/write | API の問題の新しい添付ファイルを作成するか、既存のものを更新します。 |
> | Microsoft.ApiManagement/service/apis/issues/attachments/delete | 指定されたコメントを問題から削除します。 |
> | Microsoft.ApiManagement/service/apis/issues/comments/read | 指定された API に関連付けられている問題のすべてのコメントを一覧表示します。 または、識別子によって指定された API の問題のコメントの詳細を取得します。 |
> | Microsoft.ApiManagement/service/apis/issues/comments/write | API の問題の新しいコメントを作成するか、既存のものを更新します。 |
> | Microsoft.ApiManagement/service/apis/issues/comments/delete | 指定されたコメントを問題から削除します。 |
> | Microsoft.ApiManagement/service/apis/operations/read | 指定された API の操作のコレクションを一覧表示します。 または、識別子によって指定された API 操作の詳細を取得します。 |
> | Microsoft.ApiManagement/service/apis/operations/write | API の新しい操作を作成するか、既存のものを更新します。 または、識別子によって指定された API の操作の詳細を更新します。 |
> | Microsoft.ApiManagement/service/apis/operations/delete | API の指定された操作を削除します。 |
> | Microsoft.ApiManagement/service/apis/operations/policies/read | API 操作レベルでポリシー構成の一覧を取得します。 または、API 操作レベルでポリシー構成を取得します。 |
> | Microsoft.ApiManagement/service/apis/operations/policies/write | API 操作レベルのポリシー構成を作成または更新します。 |
> | Microsoft.ApiManagement/service/apis/operations/policies/delete | API 操作におけるポリシー構成を削除します。 |
> | Microsoft.ApiManagement/service/apis/operations/policy/read | 操作レベルでポリシー構成を取得します |
> | Microsoft.ApiManagement/service/apis/operations/policy/write | 操作レベルでポリシー構成を作成します |
> | Microsoft.ApiManagement/service/apis/operations/policy/delete | 操作レベルでポリシー構成を削除します |
> | Microsoft.ApiManagement/service/apis/operations/tags/read | 操作に関連付けられているすべてのタグを一覧表示します。 または、操作に関連付けられているタグを取得します。 |
> | Microsoft.ApiManagement/service/apis/operations/tags/write | 操作にタグを割り当てます。 |
> | Microsoft.ApiManagement/service/apis/operations/tags/delete | 操作からタグをデタッチします。 |
> | Microsoft.ApiManagement/service/apis/operationsByTags/read | タグに関連付けられている操作のコレクションを一覧表示します。 |
> | Microsoft.ApiManagement/service/apis/policies/read | API レベルでポリシー構成を取得します。 または、API レベルでポリシー構成を取得します。 |
> | Microsoft.ApiManagement/service/apis/policies/write | API のポリシー構成を作成または更新します。 |
> | Microsoft.ApiManagement/service/apis/policies/delete | API のポリシー構成を削除します。 |
> | Microsoft.ApiManagement/service/apis/policy/read | API レベルでポリシー構成を取得します |
> | Microsoft.ApiManagement/service/apis/policy/write | API レベルでポリシー構成を作成します |
> | Microsoft.ApiManagement/service/apis/policy/delete | API レベルでポリシー構成を削除します |
> | Microsoft.ApiManagement/service/apis/products/read | API が含まれるすべての製品を一覧表示します。 |
> | Microsoft.ApiManagement/service/apis/releases/read | API のすべてのリリースを一覧表示します。<br>API リリースは、API リビジョンを最新にしたときに作成されます。<br>リリースは、以前のリビジョンにロールバックするためにも使用されます。<br>結果はページングされ、$top および $skip パラメーターによって制限することができます。<br>または、API リリースの詳細を返します。 |
> | Microsoft.ApiManagement/service/apis/releases/delete | API のすべてのリリースを削除するか、指定されたリリースを API から削除します。 |
> | Microsoft.ApiManagement/service/apis/releases/write | API の新しいリリースを作成します。 または、識別子によって指定された API のリリースの詳細を更新します。 |
> | Microsoft.ApiManagement/service/apis/revisions/read | API のすべてのリビジョンを一覧表示します。 |
> | Microsoft.ApiManagement/service/apis/revisions/delete | API のすべてのリビジョンを削除します。 |
> | Microsoft.ApiManagement/service/apis/schemas/read | API レベルでスキーマ構成を取得します。 または、API レベルでスキーマ構成を取得します。 |
> | Microsoft.ApiManagement/service/apis/schemas/write | API のスキーマ構成を作成または更新します。 |
> | Microsoft.ApiManagement/service/apis/schemas/delete | API のスキーマ構成を削除します。 |
> | Microsoft.ApiManagement/service/apis/tagDescriptions/read | API のスコープ内のすべてのタグの記述を一覧表示します。 Swagger に似たモデル - tagDescription は API レベルで定義されていますが、タグは API のスコープ内の操作またはタグの取得の記述に割り当てることができます |
> | Microsoft.ApiManagement/service/apis/tagDescriptions/write | API のスコープ内のタグの記述を作成または更新します。 |
> | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | API のタグの記述を削除します。 |
> | Microsoft.ApiManagement/service/apis/tags/read | API に関連付けられているすべてのタグを一覧表示します。 または、API に関連付けられているタグを取得します。 |
> | Microsoft.ApiManagement/service/apis/tags/write | API にタグを割り当てます。 |
> | Microsoft.ApiManagement/service/apis/tags/delete | API からタグをデタッチします。 |
> | Microsoft.ApiManagement/service/apisByTags/read | タグに関連付けられている API のコレクションを一覧表示します。 |
> | Microsoft.ApiManagement/service/apiVersionSets/read | 指定されたサービス インスタンスにおける API バージョン セットのコレクションを一覧表示します。 または、識別子によって指定された API バージョン セットの詳細を取得します。 |
> | Microsoft.ApiManagement/service/apiVersionSets/write | API バージョン セットを作成または更新します。 または、識別子によって指定された API バージョン セットの詳細を更新します。 |
> | Microsoft.ApiManagement/service/apiVersionSets/delete | 特定の API バージョン セットを削除します。 |
> | Microsoft.ApiManagement/service/apiVersionSets/versions/read | バージョン エンティティの一覧を取得します。 |
> | Microsoft.ApiManagement/service/authorizationServers/read | サービス インスタンス内で定義されている承認サーバーのコレクションを一覧表示します。 または、シークレットなしの承認サーバーの詳細を取得します。 |
> | Microsoft.ApiManagement/service/authorizationServers/write | 新しい承認サーバーを作成するか、既存の承認サーバーを更新します。 または、識別子によって指定された承認サーバーの詳細を更新します。 |
> | Microsoft.ApiManagement/service/authorizationServers/delete | 特定の承認サーバーのインスタンスを削除します。 |
> | Microsoft.ApiManagement/service/authorizationServers/listSecrets/action | 承認サーバーのシークレットを取得します。 |
> | Microsoft.ApiManagement/service/backends/read | 指定されたサービス インスタンスのバックエンドのコレクションを一覧表示します。 または、識別子によって指定されたバックエンドの詳細を取得します。 |
> | Microsoft.ApiManagement/service/backends/write | バックエンドを作成または更新します。 または、既存のバックエンドを更新します。 |
> | Microsoft.ApiManagement/service/backends/delete | 指定されたバックエンドを削除します。 |
> | Microsoft.ApiManagement/service/backends/reconnect/action | 指定されたタイムアウト後にバックエンドへの新しい接続を作成するように APIM プロキシに通知します。 タイムアウトが指定されていない場合は、2 分のタイムアウトが使用されます。 |
> | Microsoft.ApiManagement/service/caches/read | 指定されたサービス インスタンスのすべての外部キャッシュのコレクションを一覧表示します。 または、識別子によって指定されたキャッシュの詳細を取得します。 |
> | Microsoft.ApiManagement/service/caches/write | API Management インスタンスで使用される外部キャッシュを作成または更新します。 または、識別子によって指定されたキャッシュの詳細を更新します。 |
> | Microsoft.ApiManagement/service/caches/delete | 特定のキャッシュを削除します。 |
> | Microsoft.ApiManagement/service/certificates/read | 指定されたサービス インスタンスにおけるすべての証明書のコレクションを一覧表示します。 または、識別子によって指定された証明書の詳細を取得します。 |
> | Microsoft.ApiManagement/service/certificates/write | バックエンドでの認証に使用される証明書を作成または更新します。 |
> | Microsoft.ApiManagement/service/certificates/delete | 特定の証明書を削除します。 |
> | Microsoft.ApiManagement/service/contentTypes/read | コンテンツの種類の一覧を返します |
> | Microsoft.ApiManagement/service/contentTypes/contentItems/read | コンテンツ項目の一覧を返すか、コンテンツ項目の詳細を返します |
> | Microsoft.ApiManagement/service/contentTypes/contentItems/write | 新しいコンテンツ項目を作成するか、指定されたコンテンツ項目を更新します |
> | Microsoft.ApiManagement/service/contentTypes/contentItems/delete | 指定されたコンテンツ項目を削除します。 |
> | Microsoft.ApiManagement/service/diagnostics/read | API Management サービス インスタンスのすべての診断を一覧表示します。 または、識別子によって指定された診断の詳細を取得します。 |
> | Microsoft.ApiManagement/service/diagnostics/write | 新しい診断を作成するか、既存のものを更新します。 または、識別子によって指定された診断の詳細を更新します。 |
> | Microsoft.ApiManagement/service/diagnostics/delete | 指定された診断を削除します。 |
> | Microsoft.ApiManagement/service/gateways/read | サービス インスタンスに登録されているゲートウェイのコレクションを一覧表示します。 または、識別子によって指定されたゲートウェイの詳細を取得します。 |
> | Microsoft.ApiManagement/service/gateways/write | API Management インスタンスで使用されるゲートウェイを作成または更新します。 または、識別子によって指定されたゲートウェイの詳細を更新します。 |
> | Microsoft.ApiManagement/service/gateways/delete | 特定のゲートウェイを削除します。 |
> | Microsoft.ApiManagement/service/gateways/listKeys/action | ゲートウェイ キーを取得します。 |
> | Microsoft.ApiManagement/service/gateways/regenerateKey/action | 指定されたゲートウェイ キーを使用して作成されたすべてのトークンを無効化して、そのキーを再生成します。 |
> | Microsoft.ApiManagement/service/gateways/generateToken/action | ゲートウェイの共有アクセス承認トークンを取得します。 |
> | Microsoft.ApiManagement/service/gateways/apis/read | ゲートウェイに関連付けられている API のコレクションを一覧表示します。 |
> | Microsoft.ApiManagement/service/gateways/apis/write | 指定されたゲートウェイに API を追加します。 |
> | Microsoft.ApiManagement/service/gateways/apis/delete | 指定されたゲートウェイから指定された API を削除します。 |
> | Microsoft.ApiManagement/service/gateways/certificateAuthorities/read | ゲートウェイの CA リストを取得します。 または、割り当てられた証明機関の詳細を取得します。 |
> | Microsoft.ApiManagement/service/gateways/certificateAuthorities/write | 指定されたゲートウェイに API を追加します。 |
> | Microsoft.ApiManagement/service/gateways/certificateAuthorities/delete | ゲートウェイから証明機関の割り当てを解除します。 |
> | Microsoft.ApiManagement/service/gateways/hostnameConfigurations/read | 指定されたゲートウェイのホスト名構成のコレクションを一覧表示します。 |
> | Microsoft.ApiManagement/service/groups/read | サービス インスタンス内で定義されているグループのコレクションを一覧表示します。 または、識別子によって指定されたグループの詳細を取得します。 |
> | Microsoft.ApiManagement/service/groups/write | グループを作成または更新します。 または、識別子によって指定されたグループの詳細を更新します。 |
> | Microsoft.ApiManagement/service/groups/delete | API Management サービス インスタンスの特定のグループを削除します。 |
> | Microsoft.ApiManagement/service/groups/users/read | グループに関連付けられているユーザー エンティティのコレクションを一覧表示します。 |
> | Microsoft.ApiManagement/service/groups/users/write | 既存のユーザーを既存のグループに追加します。 |
> | Microsoft.ApiManagement/service/groups/users/delete | 既存のグループから既存のユーザーを削除します。 |
> | Microsoft.ApiManagement/service/identityProviders/read | 指定されたサービス インスタンスで構成されている ID プロバイダーのコレクションを一覧表示します。 または、シークレットなしの ID プロバイダーの構成の詳細を取得します。 |
> | Microsoft.ApiManagement/service/identityProviders/write | IdentityProvider 構成を作成または更新します。 または、既存の IdentityProvider 構成を更新します。 |
> | Microsoft.ApiManagement/service/identityProviders/delete | 指定された ID プロバイダーの構成を削除します。 |
> | Microsoft.ApiManagement/service/identityProviders/listSecrets/action | ID プロバイダーのシークレットを取得します。 |
> | Microsoft.ApiManagement/service/issues/read | 指定されたサービス インスタンスにおける問題のコレクションを一覧表示します。 または、API Management の問題の詳細を取得します |
> | Microsoft.ApiManagement/service/locations/networkstatus/read | その場所でサービスが依存しているリソースのネットワーク アクセスの状態を取得します。 |
> | Microsoft.ApiManagement/service/loggers/read | 指定されたサービス インスタンスにおけるロガーのコレクションを一覧表示します。 または、識別子によって指定されたロガーの詳細を取得します。 |
> | Microsoft.ApiManagement/service/loggers/write | ロガーを作成または更新します。 または、既存のロガーを更新します。 |
> | Microsoft.ApiManagement/service/loggers/delete | 指定されたロガーを削除します。 |
> | Microsoft.ApiManagement/service/namedValues/read | サービス インスタンス内で定義されている名前付きの値のコレクションを一覧表示します。 または、識別子によって指定された名前付きの値の詳細を取得します。 |
> | Microsoft.ApiManagement/service/namedValues/write | 名前付きの値を作成または更新します。 または、特定の名前付きの値を更新します。 |
> | Microsoft.ApiManagement/service/namedValues/delete | API Management サービス インスタンスから特定の名前付きの値を削除します。 |
> | Microsoft.ApiManagement/service/namedValues/listValue/action | 識別子によって指定された名前付きの値のシークレットを取得します。 |
> | Microsoft.ApiManagement/service/networkstatus/read | サービスが依存しているリソースのネットワーク アクセスの状態を取得します。 |
> | Microsoft.ApiManagement/service/notifications/read | サービス インスタンス内で定義されているプロパティのコレクションを一覧表示します。 または、識別子によって指定された通知の詳細を取得します。 |
> | Microsoft.ApiManagement/service/notifications/write | API Management 発行者通知を作成または更新します。 |
> | Microsoft.ApiManagement/service/notifications/recipientEmails/read | 通知をサブスクライブしている通知受信者のメールの一覧を取得します。 |
> | Microsoft.ApiManagement/service/notifications/recipientEmails/write | 通知の受信者の一覧にメール アドレスを追加します。 |
> | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | 通知の一覧からメールを削除します。 |
> | Microsoft.ApiManagement/service/notifications/recipientUsers/read | 通知をサブスクライブしている通知受信者ユーザーの一覧を取得します。 |
> | Microsoft.ApiManagement/service/notifications/recipientUsers/write | 通知の受信者の一覧に API Management ユーザーを追加します。 |
> | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | 通知の一覧から API Management ユーザーを削除します。 |
> | Microsoft.ApiManagement/service/openidConnectProviders/read | すべての OpenID Connect プロバイダーを一覧表示します。 または、シークレットなしの特定の OpenID Connect プロバイダーを取得します。 |
> | Microsoft.ApiManagement/service/openidConnectProviders/write | OpenID Connect プロバイダーを作成または更新します。 または、特定の OpenID Connect プロバイダーを更新します。 |
> | Microsoft.ApiManagement/service/openidConnectProviders/delete | API Management サービス インスタンスの特定の OpenID Connect プロバイダーを削除します。 |
> | Microsoft.ApiManagement/service/openidConnectProviders/listSecrets/action | 特定の OpenID Connect プロバイダーのシークレットを取得します。 |
> | Microsoft.ApiManagement/service/operationresults/read | 実行時間の長い操作の現在の状態を取得します。 |
> | Microsoft.ApiManagement/service/policies/read | API Management サービスのすべてのグローバル ポリシー定義を一覧表示します。 または、API Management サービスのグローバル ポリシー定義を取得します。 |
> | Microsoft.ApiManagement/service/policies/write | API Management サービスのグローバル ポリシー構成を作成または更新します。 |
> | Microsoft.ApiManagement/service/policies/delete | API Management サービスのグローバル ポリシー構成を削除します。 |
> | Microsoft.ApiManagement/service/policy/read | テナント レベルでポリシー構成を取得します |
> | Microsoft.ApiManagement/service/policy/write | テナント レベルでポリシー構成を作成します |
> | Microsoft.ApiManagement/service/policy/delete | テナント レベルでポリシー構成を削除します |
> | Microsoft.ApiManagement/service/policyDescriptions/read | すべてのポリシーの説明を一覧表示します。 |
> | Microsoft.ApiManagement/service/policySnippets/read | すべてのポリシーのスニペットを一覧表示します。 |
> | Microsoft.ApiManagement/service/portalRevisions/read | 開発者ポータルのリビジョンのコレクションを一覧表示します。 または、識別子によって指定された開発者ポータルのリビジョンを取得します。 |
> | Microsoft.ApiManagement/service/portalRevisions/write | 新しい開発者ポータルのリビジョンを作成します。 または、指定されたポータルのリビジョンの説明を更新するか、最新のものにします。 |
> | Microsoft.ApiManagement/service/portalSettings/read | ポータル設定のコレクションを一覧表示します。 または、ポータルのサインイン、サインアップ、あるいは委任の各設定を取得します。 |
> | Microsoft.ApiManagement/service/portalSettings/write | サインインの設定を更新します。 または、サインインの設定を作成または更新します。 または、サインアップ、サインアップ、または委任の設定を更新します。 または、委任の設定を作成または更新します。 |
> | Microsoft.ApiManagement/service/portalSettings/listSecrets/action | ポータルの委任設定の検証キーが取得されます。 または、メディア コンテンツ BLOB コンテナーの URI を取得します。 |
> | Microsoft.ApiManagement/service/privateLinkResources/read | Private Link グループ リソースを取得します |
> | Microsoft.ApiManagement/service/products/read | 指定されたサービス インスタンスにおける製品のコレクションを一覧表示します。 または、識別子によって指定された製品の詳細を取得します。 |
> | Microsoft.ApiManagement/service/products/write | 製品を作成または更新します。 または、既存の製品の詳細を更新します。 |
> | Microsoft.ApiManagement/service/products/delete | 製品を削除します。 |
> | Microsoft.ApiManagement/service/products/apis/read | 製品に関連付けられている API のコレクションを一覧表示します。 |
> | Microsoft.ApiManagement/service/products/apis/write | 指定された製品に API を追加します。 |
> | Microsoft.ApiManagement/service/products/apis/delete | 指定された製品から指定された API を削除します。 |
> | Microsoft.ApiManagement/service/products/groups/read | 指定された製品に関連付けられている開発者グループのコレクションを一覧表示します。 |
> | Microsoft.ApiManagement/service/products/groups/write | 指定された開発者グループと指定された製品の間の関連付けを追加します。 |
> | Microsoft.ApiManagement/service/products/groups/delete | 指定されたグループと製品の間の関連付けを削除します。 |
> | Microsoft.ApiManagement/service/products/policies/read | 製品レベルでポリシー構成を取得します。 または、製品レベルでポリシー構成を取得します。 |
> | Microsoft.ApiManagement/service/products/policies/write | 製品のポリシー構成を作成または更新します。 |
> | Microsoft.ApiManagement/service/products/policies/delete | 製品のポリシー構成を削除します。 |
> | Microsoft.ApiManagement/service/products/policy/read | 製品レベルでポリシー構成を取得します |
> | Microsoft.ApiManagement/service/products/policy/write | 製品レベルでポリシー構成を作成します |
> | Microsoft.ApiManagement/service/products/policy/delete | 製品レベルでポリシー構成を削除します |
> | Microsoft.ApiManagement/service/products/subscriptions/read | 指定された製品に対するサブスクリプションのコレクションを一覧表示します。 |
> | Microsoft.ApiManagement/service/products/tags/read | 製品に関連付けられているすべてのタグを一覧表示します。 または、製品に関連付けられているタグを取得します。 |
> | Microsoft.ApiManagement/service/products/tags/write | 製品にタグを割り当てます。 |
> | Microsoft.ApiManagement/service/products/tags/delete | 製品からタグをデタッチします。 |
> | Microsoft.ApiManagement/service/productsByTags/read | タグに関連付けられている製品のコレクションを一覧表示します。 |
> | Microsoft.ApiManagement/service/properties/read | サービス インスタンス内で定義されているプロパティのコレクションを一覧表示します。 または、識別子によって指定されたプロパティの詳細を取得します。 |
> | Microsoft.ApiManagement/service/properties/write | プロパティを作成または更新します。 または、特定のプロパティを更新します。 |
> | Microsoft.ApiManagement/service/properties/delete | API Management サービス インスタンスから特定のプロパティを削除します。 |
> | Microsoft.ApiManagement/service/properties/listSecrets/action | 識別子によって指定されたプロパティのシークレットを取得します。 |
> | Microsoft.ApiManagement/service/quotas/read | クォータの値を取得します。 |
> | Microsoft.ApiManagement/service/quotas/write | クォータ カウンターの現在の値を設定します。 |
> | Microsoft.ApiManagement/service/quotas/periods/read | 期間のクォータ カウンターの値を取得します。 |
> | Microsoft.ApiManagement/service/quotas/periods/write | クォータ カウンターの現在の値を設定します。 |
> | Microsoft.ApiManagement/service/regions/read | サービスが存在するすべての Azure リージョンを一覧表示します。 |
> | Microsoft.ApiManagement/service/reports/read | 期間、地理的地域、または開発者ごとに集計されたレポートを取得します。<br>または、製品ごとに集計されたレポートを取得します。<br>または、API、操作、サブスクリプションごとに集計されたレポートを取得します。<br>または、要求のレポート データを取得します。 |
> | Microsoft.ApiManagement/service/settings/read | テナント設定のコレクションを一覧表示します。 常に空です。 代わりに /settings/public を使用してください |
> | Microsoft.ApiManagement/service/subscriptions/read | API Management サービス インスタンスのすべてのサブスクリプションを一覧表示します。 または、指定されたサブスクリプション エンティティ (キーなし) を取得します。 |
> | Microsoft.ApiManagement/service/subscriptions/write | 指定された製品に対する指定されたユーザーのサブスクリプションを作成または更新します。 または、識別子によって指定されたサブスクリプションの詳細を更新します。 |
> | Microsoft.ApiManagement/service/subscriptions/delete | 指定されたサブスクリプションを削除します。 |
> | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | API Management サービス インスタンスの既存のサブスクリプションの主キーを再生成します。 |
> | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | API Management サービス インスタンスの既存のサブスクリプションのセカンダリ キーを再生成します。 |
> | Microsoft.ApiManagement/service/subscriptions/listSecrets/action | 指定されたサブスクリプション キーを取得します。 |
> | Microsoft.ApiManagement/service/tagResources/read | タグに関連付けられているリソースのコレクションを一覧表示します。 |
> | Microsoft.ApiManagement/service/tags/read | サービス インスタンス内で定義されているタグのコレクションを一覧表示します。 または、識別子によって指定されたタグの詳細を取得します。 |
> | Microsoft.ApiManagement/service/tags/write | タグを作成します。 または、識別子によって指定されたタグの詳細を更新します。 |
> | Microsoft.ApiManagement/service/tags/delete | API Management サービス インスタンスの特定のタグを削除します。 |
> | Microsoft.ApiManagement/service/templates/read | すべてのメール テンプレートまたは API Management メール テンプレートの詳細を取得します。 |
> | Microsoft.ApiManagement/service/templates/write | API Management メール テンプレートを作成または更新します。 |
> | Microsoft.ApiManagement/service/templates/delete | 既定の API Management メール テンプレートをリセットします。 |
> | Microsoft.ApiManagement/service/tenant/read | テナント アクセス設定のコレクションを一覧表示します。 または、API Management サービスのグローバル ポリシー定義を取得します。 または、テナント アクセス情報の詳細を取得します |
> | Microsoft.ApiManagement/service/tenant/write | テナントのポリシー構成を設定するか、テナントのアクセス情報の詳細を更新します |
> | Microsoft.ApiManagement/service/tenant/delete | テナントのポリシーの構成を削除します。 |
> | Microsoft.ApiManagement/service/tenant/listSecrets/action | テナント アクセス情報の詳細を取得します。 |
> | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | プライマリ アクセス キーを再生成します。 |
> | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | セカンダリ アクセス キーを再生成します。 |
> | Microsoft.ApiManagement/service/tenant/deploy/action | 指定された Git ブランチの変更をデータベース内の構成に適用するデプロイ タスクを実行します。 |
> | Microsoft.ApiManagement/service/tenant/save/action | レポジトリ内の指定されたブランチの構成のスナップショットを使用してコミットを作成します。 |
> | Microsoft.ApiManagement/service/tenant/validate/action | 指定された Git ブランチの変更を検証します。 |
> | Microsoft.ApiManagement/service/tenant/operationResults/read | 操作の結果の一覧を取得するか、特定の操作の結果を取得します。 |
> | Microsoft.ApiManagement/service/tenant/syncState/read | 前回の Git 同期の状態を取得します。 |
> | Microsoft.ApiManagement/service/users/read | 指定されたサービス インスタンスにおける登録ユーザーのコレクションを一覧表示します。 または、識別子によって指定されたユーザーの詳細を取得します。 |
> | Microsoft.ApiManagement/service/users/write | ユーザーを作成または更新します。 または、識別子によって指定されたユーザーの詳細を更新します。 |
> | Microsoft.ApiManagement/service/users/delete | 特定のユーザーを削除します。 |
> | Microsoft.ApiManagement/service/users/generateSsoUrl/action | 特定のユーザーが開発者ポータルにサインインするための認証トークンを含むリダイレクト URL を取得します。 |
> | Microsoft.ApiManagement/service/users/token/action | ユーザーの共有アクセス承認トークンを取得します。 |
> | Microsoft.ApiManagement/service/users/confirmations/send/action | 確認を送信します |
> | Microsoft.ApiManagement/service/users/groups/read | すべてのユーザー グループを一覧表示します。 |
> | Microsoft.ApiManagement/service/users/identities/read | すべてのユーザー ID を一覧表示します。 |
> | Microsoft.ApiManagement/service/users/keys/read | ユーザーに関連付けられたキーを取得します |
> | Microsoft.ApiManagement/service/users/subscriptions/read | 指定されたユーザーのサブスクリプションのコレクションを一覧表示します。 |

### <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

Azure サービス: コア

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.AppConfiguration/register/action | Microsoft App Configuration を使用するためにサブスクリプションを登録します。 |
> | Microsoft.AppConfiguration/unregister/action | Microsoft App Configuration の使用に関してサブスクリプションの登録を解除します。 |
> | Microsoft.AppConfiguration/checkNameAvailability/read | リソース名が使用可能かどうかを確認します。 |
> | Microsoft.AppConfiguration/configurationStores/read | 指定された構成ストアのプロパティを取得するか、指定されたリソース グループまたはサブスクリプションにあるすべての構成ストアを一覧表示します。 |
> | Microsoft.AppConfiguration/configurationStores/write | 指定されたパラメーターを使用して、構成ストアを作成または更新します。 |
> | Microsoft.AppConfiguration/configurationStores/delete | 構成ストアを削除します。 |
> | Microsoft.AppConfiguration/configurationStores/ListKeys/action | 指定された構成ストアの API キーを一覧表示します。 |
> | Microsoft.AppConfiguration/configurationStores/RegenerateKey/action | 指定された構成ストアの API キーを再生成します。 |
> | Microsoft.AppConfiguration/configurationStores/ListKeyValue/action | 指定された構成ストアのキー値を一覧表示します。 |
> | Microsoft.AppConfiguration/configurationStores/PrivateEndpointConnectionsApproval/action | 指定された構成ストアにあるプライベート エンドポイント接続が自動承認されます。 |
> | Microsoft.AppConfiguration/configurationStores/eventGridFilters/read | 指定された構成ストア イベント グリッド フィルターのプロパティを取得するか、指定された構成ストアにあるすべての構成ストア イベント グリッド フィルターを一覧表示します。 |
> | Microsoft.AppConfiguration/configurationStores/eventGridFilters/write | 指定されたパラメーターを使用して、構成ストア イベント グリッド フィルターを作成または更新します。 |
> | Microsoft.AppConfiguration/configurationStores/eventGridFilters/delete | 構成ストア イベント グリッド フィルターを削除します。 |
> | Microsoft.AppConfiguration/configurationStores/privateEndpointConnectionProxies/validate/action | 指定された構成ストアにあるプライベート エンドポイント接続プロキシを検証します。 |
> | Microsoft.AppConfiguration/configurationStores/privateEndpointConnectionProxies/read | 指定された構成ストアにあるプライベート エンドポイント接続プロキシを取得します。 |
> | Microsoft.AppConfiguration/configurationStores/privateEndpointConnectionProxies/write | 指定された構成ストアでプライベート エンドポイント接続プロキシを作成または更新します。 |
> | Microsoft.AppConfiguration/configurationStores/privateEndpointConnectionProxies/delete | 指定された構成ストアにあるプライベート エンドポイント接続プロキシを削除します。 |
> | Microsoft.AppConfiguration/configurationStores/privateEndpointConnections/read | 指定された構成ストアにあるプライベート エンドポイント接続が取得されるか、プライベート エンドポイント接続が一覧表示されます。 |
> | Microsoft.AppConfiguration/configurationStores/privateEndpointConnections/write | 指定された構成ストアにあるプライベート エンドポイント接続が承認または拒否されます。 |
> | Microsoft.AppConfiguration/configurationStores/privateEndpointConnections/delete | 指定された構成ストアにあるプライベート エンドポイント接続が削除されます。 |
> | Microsoft.AppConfiguration/configurationStores/privateLinkResources/read | 指定された構成ストアにあるすべての Private Link リソースが一覧表示されます。 |
> | Microsoft.AppConfiguration/configurationStores/providers/Microsoft.Insights/diagnosticSettings/read | 構成ストアのすべての診断設定値を読み取ります。 |
> | Microsoft.AppConfiguration/configurationStores/providers/Microsoft.Insights/diagnosticSettings/write | Microsoft App Configuration の診断設定を書き込むか上書きします。 |
> | Microsoft.AppConfiguration/configurationStores/providers/Microsoft.Insights/logDefinitions/read | Microsoft App Configuration のすべてのログ定義を取得します。 |
> | Microsoft.AppConfiguration/configurationStores/providers/Microsoft.Insights/metricDefinitions/read | Microsoft App Configuration のすべてのメトリック定義を取得します。 |
> | Microsoft.AppConfiguration/locations/operationsStatus/read | 操作の状態を取得します。 |
> | Microsoft.AppConfiguration/operations/read | Microsoft App Configuration でサポートされているすべての操作を一覧表示します。 |
> | **DataAction** | **説明** |
> | Microsoft.AppConfiguration/configurationStores/keyValues/read | 構成ストアからキー値を読み取ります。 |
> | Microsoft.AppConfiguration/configurationStores/keyValues/write | 構成ストアでキー値を作成または更新します。 |
> | Microsoft.AppConfiguration/configurationStores/keyValues/delete | 構成ストアから既存のキー値を削除します。 |

### <a name="microsoftazurestack"></a>Microsoft.AzureStack

Azure サービス: コア

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.AzureStack/register/action | サブスクリプション登録アクション |
> | Microsoft.AzureStack/register/action | Microsoft.AzureStack リソース プロバイダーにサブスクリプションを登録します。 |
> | Microsoft.AzureStack/cloudManifestFiles/read | クラウド マニフェスト ファイルを取得します |
> | Microsoft.AzureStack/edgeSubscriptions/read | Azure Stack Edge のサブスクリプションのプロパティを取得します |
> | Microsoft.AzureStack/edgeSubscriptions/write | Edge のサブスクリプションを作成または更新します |
> | Microsoft.AzureStack/edgeSubscriptions/delete | Edge のサブスクリプションを削除します |
> | Microsoft.AzureStack/edgeSubscriptions/edgeResourceGroups/action | Edge リソース グループの下にある、予想される Edge リソースの読み取りまたは書き込みを行います |
> | Microsoft.AzureStack/edgeSubscriptions/edgeProviders/action | 特定の Edge リソース プロバイダー名前空間の下にある、予想される Edge リソースの読み取りまたは書き込みを行います |
> | Microsoft.AzureStack/edgeSubscriptions/operations/action | 予想される Edge リソースに対する非同期操作の状態を取得または一覧表示します |
> | Microsoft.AzureStack/linkedSubscriptions/read | Azure Stack のリンクされたサブスクリプションのプロパティを取得します |
> | Microsoft.AzureStack/linkedSubscriptions/write | リンクされたサブスクリプションを作成または更新します |
> | Microsoft.AzureStack/linkedSubscriptions/delete | リンクされたサブスクリプションを削除します |
> | Microsoft.AzureStack/linkedSubscriptions/linkedResourceGroups/action | リンクされたリソース グループの下にある、予想されるリンクされたリソースの読み取りまたは書き込みを行います |
> | Microsoft.AzureStack/linkedSubscriptions/linkedProviders/action | 特定のリンクされたリソース プロバイダー名前空間の下にある、予想されるリンクされたリソースの読み取りまたは書き込みを行います |
> | Microsoft.AzureStack/linkedSubscriptions/operations/action | 予想されるリンクされたリソースに対する非同期操作の状態を取得または一覧表示します |
> | Microsoft.AzureStack/Operations/read | リソース プロバイダーの操作のプロパティを取得します。 |
> | Microsoft.AzureStack/registrations/read | Azure Stack の登録のプロパティを取得します |
> | Microsoft.AzureStack/registrations/write | Azure Stack の登録を作成または更新します。 |
> | Microsoft.AzureStack/registrations/delete | Azure Stack の登録を削除します。 |
> | Microsoft.AzureStack/registrations/getActivationKey/action | Azure Stack の最新のライセンス認証キーを取得します。 |
> | Microsoft.AzureStack/registrations/enableRemoteManagement/action | Azure Stack 登録に対して RemoteManagement を有効にします |
> | Microsoft.AzureStack/registrations/customerSubscriptions/read | Azure Stack の顧客サブスクリプションのプロパティを取得します。 |
> | Microsoft.AzureStack/registrations/customerSubscriptions/write | Azure Stack の顧客サブスクリプションを作成または更新します。 |
> | Microsoft.AzureStack/registrations/customerSubscriptions/delete | Azure Stack の顧客サブスクリプションを削除します。 |
> | Microsoft.AzureStack/registrations/products/read | Azure Stack Marketplace の製品のプロパティを取得します |
> | Microsoft.AzureStack/registrations/products/listDetails/action | Azure Stack Marketplace の製品の拡張詳細を取得します |
> | Microsoft.AzureStack/registrations/products/getProducts/action | Azure Stack Marketplace 製品の一覧を取得します。 |
> | Microsoft.AzureStack/registrations/products/getProduct/action | Azure Stack Marketplace 製品を取得します。 |
> | Microsoft.AzureStack/registrations/products/uploadProductLog/action | Azure Stack Marketplace の製品操作の状態とタイムスタンプを記録します |

### <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

Azure サービス:[Azure Stack Edge](../databox-online/azure-stack-edge-overview.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.DataBoxEdge/availableSkus/read | 利用可能な SKU を一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Data Box Edge デバイスを作成または更新します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Data Box Edge デバイスを一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/delete | Data Box Edge デバイスを削除します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Data Box Edge デバイスを一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Data Box Edge デバイスを一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Data Box Edge デバイスを作成または更新します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/action | リソースの拡張情報を取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/updateExtendedInformation/action | ArmApiDesc_action_updateExtendedInformation_dataBoxEdgeDevices |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/action | 更新プログラムをスキャンします |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/action | デバイスで更新プログラムをダウンロードします |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/installUpdates/action | デバイスに更新プログラムをインストールします |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/action | デバイス登録の証明書をアップロードします |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/generateCertificate/action | ArmApiDesc_action_generateCertificate_dataBoxEdgeDevices |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | アラートを一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | アラートを一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | 帯域幅のスケジュールを一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | 帯域幅のスケジュールを一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/write | 帯域幅のスケジュールを作成または更新します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/delete | 帯域幅のスケジュールを削除します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/operationResults/read | 操作結果を一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/jobs/read | ジョブを一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/networkSettings/read | デバイス ネットワークの設定を一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/nodes/read | ノードを一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationResults/read | 操作結果を一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationsStatus/read | 操作の状態を一覧表示するか、取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | 注文を一覧表示または取得します。 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | 注文を一覧表示または取得します。 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/write | 注文を作成または更新します。 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/delete | 注文を削除します。 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/listDCAccessCode/action | ArmApiDesc_action_listDCAccessCode_orders |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/operationResults/read | 操作結果を一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | ロールを一覧表示または取得します。 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | ロールを一覧表示または取得します。 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write | ロールを作成または更新します。 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/delete | ロールを削除します。 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/addons/read | ArmApiRes_addons を一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/addons/read | ArmApiRes_addons を一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/addons/write | ArmApiRes_addons を作成または更新します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/addons/delete | ArmApiRes_addons を削除します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/addons/operationResults/read | 操作結果を一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/monitoringConfig/write | 監視構成を作成または更新します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/monitoringConfig/delete | 監視構成を削除します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/monitoringConfig/read | 監視構成を一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/monitoringConfig/read | 監視構成を一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/monitoringConfig/operationResults/read | 操作結果を一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/operationResults/read | 操作結果を一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/update/action | セキュリティの設定を更新します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/operationResults/read | 操作結果を一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | 共有を一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | 共有を一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/write | 共有を作成または更新します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/refresh/action | クラウドのデータを使用して共有メタデータを更新します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/delete | 共有を削除します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/operationResults/read | 操作結果を一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/write | ストレージ アカウントの資格情報を作成または更新します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | ストレージ アカウントの資格情報を一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | ストレージ アカウントの資格情報を一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/delete | ストレージ アカウントの資格情報を削除します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/operationResults/read | 操作結果を一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccounts/read | ストレージ アカウントを一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccounts/read | ストレージ アカウントを一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccounts/write | ストレージ アカウントを作成または更新します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccounts/delete | ストレージ アカウントを削除します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccounts/containers/read | コンテナーを一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccounts/containers/read | コンテナーを一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccounts/containers/write | コンテナーを作成または更新します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccounts/containers/delete | コンテナーを削除します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccounts/containers/refresh/action | クラウドのデータでコンテナー メタデータを更新します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccounts/containers/operationResults/read | 操作結果を一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccounts/operationResults/read | 操作結果を一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | トリガーを一覧表示または取得します。 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | トリガーを一覧表示または取得します。 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/write | トリガーを作成または更新します。 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/delete | トリガーを削除します。 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/operationResults/read | 操作結果を一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/updateSummary/read | 更新の概要を一覧表示または取得します。 |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | 共有ユーザーを一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | 共有ユーザーを一覧表示または取得します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/write | 共有ユーザーを作成または更新します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/delete | 共有ユーザーを削除します |
> | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/operationResults/read | 操作結果を一覧表示または取得します |
> | Microsoft.DataBoxEdge/skus/read | SKU が一覧表示されるか取得されます |

### <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

Azure サービス:[Data Catalog](../data-catalog/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.DataCatalog/checkNameAvailability/action | テナントのカタログ名を使用できるかどうかを確認します。 |
> | Microsoft.DataCatalog/register/action | Microsoft.DataCatalog リソース プロバイダーにサブスクリプションを登録します。 |
> | Microsoft.DataCatalog/unregister/action | Microsoft.DataCatalog リソース プロバイダーにサブスクリプションを登録解除します。 |
> | Microsoft.DataCatalog/catalogs/read | サブスクリプションまたはリソース グループでカタログのプロパティを取得します。 |
> | Microsoft.DataCatalog/catalogs/write | カタログを作成するか、カタログのタグとプロパティを更新します。 |
> | Microsoft.DataCatalog/catalogs/delete | カタログを削除します。 |
> | Microsoft.DataCatalog/operations/read | Microsoft.DataCatalog リソース プロバイダーで使用できる操作を一覧表示します。 |

### <a name="microsofteventgrid"></a>Microsoft.EventGrid

Azure サービス:[Event Grid](../event-grid/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.EventGrid/register/action | EventGrid リソース プロバイダーのサブスクリプションを登録します。 |
> | Microsoft.EventGrid/unregister/action | EventGrid リソース プロバイダーのサブスクリプションを登録解除します。 |
> | Microsoft.EventGrid/domains/write | ドメインを作成または更新します |
> | Microsoft.EventGrid/domains/read | ドメインを読み取ります |
> | Microsoft.EventGrid/domains/delete | ドメインを削除します |
> | Microsoft.EventGrid/domains/listKeys/action | ドメインのキーを一覧表示します |
> | Microsoft.EventGrid/domains/regenerateKey/action | ドメインのキーを再生成します |
> | oreMicrosoft.EventGrid/domains/PrivateEndpointConnectionsApproval/action | ドメインの PrivateEndpointConnections を承認します |
> | Microsoft.EventGrid/domains/privateEndpointConnectionProxies/validate/action | ドメインの PrivateEndpointConnectionProxies を検証します |
> | Microsoft.EventGrid/domains/privateEndpointConnectionProxies/read | ドメインの PrivateEndpointConnectionProxies を読み取ります |
> | Microsoft.EventGrid/domains/privateEndpointConnectionProxies/write | ドメインの PrivateEndpointConnectionProxies を書き込みます |
> | Microsoft.EventGrid/domains/privateEndpointConnectionProxies/delete | ドメインの PrivateEndpointConnectionProxies を削除します |
> | Microsoft.EventGrid/domains/privateEndpointConnections/read | ドメインの PrivateEndpointConnections を読み取ります |
> | Microsoft.EventGrid/domains/privateEndpointConnections/write | ドメインの PrivateEndpointConnections を書き込みます |
> | Microsoft.EventGrid/domains/privateEndpointConnections/delete | ドメインの PrivateEndpointConnections を削除します |
> | Microsoft.EventGrid/domains/privateLinkResources/read | ドメインの PrivateLinkResources を取得または一覧表示します |
> | Microsoft.EventGrid/domains/providers/Microsoft.Insights/logDefinitions/read | 診断ログへのアクセスを許可します |
> | Microsoft.EventGrid/domains/providers/Microsoft.Insights/metricDefinitions/read | ドメインの使用可能なメトリックを取得します |
> | Microsoft.EventGrid/domains/topics/read | ドメインのトピックを読み取ります |
> | Microsoft.EventGrid/domains/topics/write | ドメイン トピックを作成または更新します |
> | Microsoft.EventGrid/domains/topics/delete | ドメイン トピックを削除します |
> | Microsoft.EventGrid/eventSubscriptions/write | eventSubscription を作成または更新します。 |
> | Microsoft.EventGrid/eventSubscriptions/read | eventSubscription を削除します。 |
> | Microsoft.EventGrid/eventSubscriptions/delete | eventSubscription を削除します。 |
> | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | イベント サブスクリプションの完全な URL を取得します。 |
> | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | イベント サブスクリプションの診断設定を取得します。 |
> | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | イベント サブスクリプションの診断設定を作成または更新します。 |
> | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | eventSubscription で使用可能なメトリックを取得します。 |
> | Microsoft.EventGrid/extensionTopics/read | extensionTopic を読み取ります。 |
> | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | トピックの診断設定を取得します。 |
> | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write | トピックの診断設定を作成または更新します。 |
> | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | トピックで使用可能なメトリックを取得します。 |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | リージョンのイベント サブスクリプションを一覧表示します |
> | Microsoft.EventGrid/locations/operationResults/read | リージョン操作の結果を読み取ります |
> | Microsoft.EventGrid/locations/operationsStatus/read | リージョン操作の状態を読み取ります |
> | Microsoft.EventGrid/locations/topictypes/eventSubscriptions/read | リージョンのイベント サブスクリプションを topictype ごとに一覧表示します |
> | Microsoft.EventGrid/operationResults/read | 操作の結果を読み取ります |
> | Microsoft.EventGrid/operations/read | EventGrid 操作を一覧表示します。 |
> | Microsoft.EventGrid/operationsStatus/read | 操作の状態を読み取ります |
> | Microsoft.EventGrid/partnerNamespaces/write | パートナー名前空間を作成または更新します |
> | Microsoft.EventGrid/partnerNamespaces/read | パートナー名前空間を読み取ります |
> | Microsoft.EventGrid/partnerNamespaces/delete | パートナー名前空間を削除します |
> | Microsoft.EventGrid/partnerNamespaces/listKeys/action | パートナー名前空間のキーを一覧表示します |
> | Microsoft.EventGrid/partnerNamespaces/regenerateKey/action | パートナー名前空間のキーを再生成します |
> | Microsoft.EventGrid/partnerNamespaces/eventChannels/read | イベント チャネルを読み取ります |
> | Microsoft.EventGrid/partnerNamespaces/eventChannels/write | イベント チャネルを作成または更新します |
> | Microsoft.EventGrid/partnerNamespaces/eventChannels/delete | イベント チャネルを削除します |
> | Microsoft.EventGrid/partnerNamespaces/providers/Microsoft.Insights/diagnosticSettings/read | パートナー名前空間の診断設定を取得します |
> | Microsoft.EventGrid/partnerNamespaces/providers/Microsoft.Insights/diagnosticSettings/write | パートナー名前空間の診断設定を作成または更新します |
> | Microsoft.EventGrid/partnerNamespaces/providers/Microsoft.Insights/logDefinitions/read | 診断ログへのアクセスを許可します |
> | Microsoft.EventGrid/partnerNamespaces/providers/Microsoft.Insights/metricDefinitions/read | パートナー名前空間で使用可能なメトリックを取得します |
> | Microsoft.EventGrid/partnerRegistrations/write | パートナー登録を成または更新します |
> | Microsoft.EventGrid/partnerRegistrations/read | パートナー登録を読み取ります |
> | Microsoft.EventGrid/partnerRegistrations/delete | パートナー登録を削除します |
> | Microsoft.EventGrid/partnerTopics/read | パートナー トピックを読み取ります |
> | Microsoft.EventGrid/partnerTopics/write | パートナー トピックを作成または更新します |
> | Microsoft.EventGrid/partnerTopics/delete | パートナー トピックを削除します |
> | Microsoft.EventGrid/partnerTopics/activate/action | パートナー トピックをアクティブ化します |
> | Microsoft.EventGrid/partnerTopics/deactivate/action | パートナー トピックを非アクティブ化します |
> | Microsoft.EventGrid/partnerTopics/providers/Microsoft.Insights/diagnosticSettings/read | パートナー トピックの診断設定を取得します |
> | Microsoft.EventGrid/partnerTopics/providers/Microsoft.Insights/diagnosticSettings/write | パートナー トピックの診断設定を作成または更新します |
> | Microsoft.EventGrid/partnerTopics/providers/Microsoft.Insights/logDefinitions/read | 診断ログへのアクセスを許可します |
> | Microsoft.EventGrid/partnerTopics/providers/Microsoft.Insights/metricDefinitions/read | パートナー トピックで使用可能なメトリックを取得します |
> | Microsoft.EventGrid/sku/read | イベント グリッド リソースの使用可能な Sku 定義を読み取ります |
> | Microsoft.EventGrid/systemTopics/read | システム トピックを読み取ります |
> | Microsoft.EventGrid/systemTopics/write | システム トピックを作成または更新します |
> | Microsoft.EventGrid/systemTopics/delete | システム トピックを削除します |
> | Microsoft.EventGrid/systemTopics/providers/Microsoft.Insights/diagnosticSettings/read | システム トピックの診断設定を取得します |
> | Microsoft.EventGrid/systemTopics/providers/Microsoft.Insights/diagnosticSettings/write | システム トピックの診断設定を作成または更新します |
> | Microsoft.EventGrid/systemTopics/providers/Microsoft.Insights/logDefinitions/read | 診断ログへのアクセスを許可します |
> | Microsoft.EventGrid/systemTopics/providers/Microsoft.Insights/metricDefinitions/read | システム トピックで使用可能なメトリックを取得します |
> | Microsoft.EventGrid/topics/write | トピックを作成または更新します。 |
> | Microsoft.EventGrid/topics/read | トピックを読み取ります。 |
> | Microsoft.EventGrid/topics/delete | トピックを削除する |
> | Microsoft.EventGrid/topics/listKeys/action | トピックのキーを一覧表示します |
> | Microsoft.EventGrid/topics/regenerateKey/action | トピックのキーを再生成します |
> | Microsoft.EventGrid/topics/PrivateEndpointConnectionsApproval/action | トピックの PrivateEndpointConnections を承認します |
> | Microsoft.EventGrid/topics/privateEndpointConnectionProxies/validate/action | トピックの PrivateEndpointConnectionProxies を検証します |
> | Microsoft.EventGrid/topics/privateEndpointConnectionProxies/read | トピックの PrivateEndpointConnectionProxies を読み取ります |
> | Microsoft.EventGrid/topics/privateEndpointConnectionProxies/write | トピックの PrivateEndpointConnectionProxies を書き込みます |
> | Microsoft.EventGrid/topics/privateEndpointConnectionProxies/delete | トピックの PrivateEndpointConnectionProxies を削除します |
> | Microsoft.EventGrid/topics/privateEndpointConnections/read | トピックの PrivateEndpointConnections を読み取ります |
> | Microsoft.EventGrid/topics/privateEndpointConnections/write | トピックの PrivateEndpointConnections を書き込みます |
> | Microsoft.EventGrid/topics/privateEndpointConnections/delete | トピックの PrivateEndpointConnections を削除します |
> | Microsoft.EventGrid/topics/privateLinkResources/read | トピックの PrivateLinkResources を読み取ります |
> | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | トピックの診断設定を取得します。 |
> | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/write | トピックの診断設定を作成または更新します。 |
> | Microsoft.EventGrid/topics/providers/Microsoft.Insights/logDefinitions/read | 診断ログへのアクセスを許可します |
> | Microsoft.EventGrid/topics/providers/Microsoft.Insights/metricDefinitions/read | トピックで使用可能なメトリックを取得します。 |
> | Microsoft.EventGrid/topictypes/read | topictype を読み取ります |
> | Microsoft.EventGrid/topictypes/eventSubscriptions/read | グローバル イベント サブスクリプションをトピックの種類ごとに一覧表示します |
> | Microsoft.EventGrid/topictypes/eventtypes/read | topictype でサポートされている eventtype を読み取ります |

### <a name="microsoftlogic"></a>Microsoft.Logic

Azure サービス:[Logic Apps](../logic-apps/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Logic/register/action | 指定されたサブスクリプションを Microsoft.Logic リソース プロバイダーに登録します。 |
> | Microsoft.Logic/integrationAccounts/read | 統合アカウントを読み取ります。 |
> | Microsoft.Logic/integrationAccounts/write | 統合アカウントを作成または更新します。 |
> | Microsoft.Logic/integrationAccounts/delete | 統合アカウントを削除します。 |
> | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | アクセス キーのシークレットを再生成します。 |
> | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | 統合アカウントのコールバック URL を取得します。 |
> | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | キー コンテナーのキーを取得します。 |
> | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | 統合アカウントでの追跡イベントを記録します。 |
> | Microsoft.Logic/integrationAccounts/join/action | 統合アカウントを結合します。 |
> | Microsoft.Logic/integrationAccounts/agreements/read | 統合アカウントの契約を読み取ります。 |
> | Microsoft.Logic/integrationAccounts/agreements/write | 統合アカウントの契約を作成または更新します。 |
> | Microsoft.Logic/integrationAccounts/agreements/delete | 統合アカウントの契約を削除します。 |
> | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | 統合アカウントの契約内容のコールバック URL を取得します。 |
> | Microsoft.Logic/integrationAccounts/assemblies/read | 統合アカウントのアセンブリを読み取ります。 |
> | Microsoft.Logic/integrationAccounts/assemblies/write | 統合アカウントのアセンブリを作成または更新します。 |
> | Microsoft.Logic/integrationAccounts/assemblies/delete | 統合アカウントのアセンブリを削除します。 |
> | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | 統合アカウントのアセンブリ内容のコールバック URL を取得します。 |
> | Microsoft.Logic/integrationAccounts/batchConfigurations/read | 統合アカウントのバッチ構成を読み取ります。 |
> | Microsoft.Logic/integrationAccounts/batchConfigurations/write | 統合アカウントのバッチ構成を作成または更新します。 |
> | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | 統合アカウントのバッチ構成を削除します。 |
> | Microsoft.Logic/integrationAccounts/certificates/read | 統合アカウントの証明書を読み取ります。 |
> | Microsoft.Logic/integrationAccounts/certificates/write | 統合アカウントの証明書を作成または更新します。 |
> | Microsoft.Logic/integrationAccounts/certificates/delete | 統合アカウントの証明書を削除します。 |
> | Microsoft.Logic/integrationAccounts/groups/read | 統合アカウントのグループを読み取ります。 |
> | Microsoft.Logic/integrationAccounts/groups/write | 統合アカウントのグループを作成または更新します。 |
> | Microsoft.Logic/integrationAccounts/groups/delete | 統合アカウントのグループを削除します。 |
> | Microsoft.Logic/integrationAccounts/maps/read | 統合アカウントのマップを読み取ります。 |
> | Microsoft.Logic/integrationAccounts/maps/write | 統合アカウントのマップを作成または更新します。 |
> | Microsoft.Logic/integrationAccounts/maps/delete | 統合アカウントのマップを削除します。 |
> | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | 統合アカウントのマップ内容のコールバック URL を取得します。 |
> | Microsoft.Logic/integrationAccounts/partners/read | 統合アカウントのパートナーを読み取ります。 |
> | Microsoft.Logic/integrationAccounts/partners/write | 統合アカウントのパートナーを作成または更新します。 |
> | Microsoft.Logic/integrationAccounts/partners/delete | 統合アカウントのパートナーを削除します。 |
> | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | 統合アカウントのパートナー内容のコールバック URL を取得します。 |
> | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | 統合アカウントのログ定義を読み取ります。 |
> | Microsoft.Logic/integrationAccounts/rosettaNetProcessConfigurations/read | 統合アカウントの RosettaNet プロセス構成を読み取ります。 |
> | Microsoft.Logic/integrationAccounts/rosettaNetProcessConfigurations/write | 統合アカウントの RosettaNet プロセス構成を作成または更新します。 |
> | Microsoft.Logic/integrationAccounts/rosettaNetProcessConfigurations/delete | 統合アカウントの RosettaNet プロセス構成を削除します。 |
> | Microsoft.Logic/integrationAccounts/schedules/read | 統合アカウントのスケジュールが読み取られます。 |
> | Microsoft.Logic/integrationAccounts/schedules/write | 統合アカウントのスケジュールが作成または更新されます。 |
> | Microsoft.Logic/integrationAccounts/schedules/delete | 統合アカウントのスケジュールが削除されます。 |
> | Microsoft.Logic/integrationAccounts/schemas/read | 統合アカウントのスキーマを読み取ります。 |
> | Microsoft.Logic/integrationAccounts/schemas/write | 統合アカウントのスキーマを作成または更新します。 |
> | Microsoft.Logic/integrationAccounts/schemas/delete | 統合アカウントのスキーマを削除します。 |
> | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | 統合アカウントのスキーマ内容のコールバック URL を取得します。 |
> | Microsoft.Logic/integrationAccounts/sessions/read | 統合アカウントのセッションを読み取ります。 |
> | Microsoft.Logic/integrationAccounts/sessions/write | 統合アカウントのセッションを作成または更新します。 |
> | Microsoft.Logic/integrationAccounts/sessions/delete | 統合アカウントのセッションを削除します。 |
> | Microsoft.Logic/integrationAccounts/usageConfigurations/read | 統合アカウントの使用構成を読み取ります。 |
> | Microsoft.Logic/integrationAccounts/usageConfigurations/write | 統合アカウントの使用構成を作成または更新します。 |
> | Microsoft.Logic/integrationAccounts/usageConfigurations/delete | 統合アカウントの使用構成を削除します。 |
> | Microsoft.Logic/integrationAccounts/usageConfigurations/listCallbackUrl/action | 統合アカウントの使用構成のコールバック URL を取得します。 |
> | Microsoft.Logic/integrationServiceEnvironments/read | 統合サービス環境を読み取ります。 |
> | Microsoft.Logic/integrationServiceEnvironments/write | 統合サービス環境を作成または更新します。 |
> | Microsoft.Logic/integrationServiceEnvironments/delete | 統合サービス環境を削除します。 |
> | Microsoft.Logic/integrationServiceEnvironments/join/action | 統合サービス環境に参加します。 |
> | Microsoft.Logic/integrationServiceEnvironments/availableManagedApis/read | 統合サービス環境で利用可能なマネージド API を読み取ります。 |
> | Microsoft.Logic/integrationServiceEnvironments/managedApis/read | 統合サービス環境のマネージド API を読み取ります。 |
> | Microsoft.Logic/integrationServiceEnvironments/managedApis/write | 統合サービス環境のマネージド API を作成または更新します。 |
> | Microsoft.Logic/integrationServiceEnvironments/managedApis/join/action | 統合サービス環境のマネージド API を結合します。 |
> | Microsoft.Logic/integrationServiceEnvironments/managedApis/apiOperations/read | 統合サービス環境のマネージド API 操作を読み取ります。 |
> | Microsoft.Logic/integrationServiceEnvironments/managedApis/operationStatuses/read | 統合サービス環境のマネージド API 操作の状態を読み取ります。 |
> | Microsoft.Logic/integrationServiceEnvironments/operationStatuses/read | 統合サービス環境の操作の状態を読み取ります。 |
> | Microsoft.Logic/integrationServiceEnvironments/providers/Microsoft.Insights/metricDefinitions/read | 統合サービス環境のメトリック定義を読み取ります。 |
> | Microsoft.Logic/locations/workflows/validate/action | ワークフローを検証します。 |
> | Microsoft.Logic/locations/workflows/recommendOperationGroups/action | ワークフロー推奨操作グループを取得します。 |
> | Microsoft.Logic/operations/read | 操作を取得します。 |
> | Microsoft.Logic/workflows/read | ワークフローを読み取ります。 |
> | Microsoft.Logic/workflows/write | ワークフローを作成または更新します。 |
> | Microsoft.Logic/workflows/delete | ワークフローを削除します。 |
> | Microsoft.Logic/workflows/run/action | ワークフローの実行を開始します。 |
> | Microsoft.Logic/workflows/disable/action | ワークフローを無効にします。 |
> | Microsoft.Logic/workflows/enable/action | ワークフローを有効にします。 |
> | Microsoft.Logic/workflows/suspend/action | ワークフローを一時停止します。 |
> | Microsoft.Logic/workflows/validate/action | ワークフローを検証します。 |
> | Microsoft.Logic/workflows/move/action | ワークフローを既存のサブスクリプション ID、リソース グループ、または名前から、別のサブスクリプション ID、リソース グループ、または名前に移動します。 |
> | Microsoft.Logic/workflows/listSwagger/action | ワークフローの Swagger 定義を取得します。 |
> | Microsoft.Logic/workflows/regenerateAccessKey/action | アクセス キーのシークレットを再生成します。 |
> | Microsoft.Logic/workflows/listCallbackUrl/action | ワークフローのコールバック URL を取得します。 |
> | Microsoft.Logic/workflows/accessKeys/read | アクセス キーを読み取ります。 |
> | Microsoft.Logic/workflows/accessKeys/write | アクセス キーを作成または更新します。 |
> | Microsoft.Logic/workflows/accessKeys/delete | アクセス キーを削除します。 |
> | Microsoft.Logic/workflows/accessKeys/list/action | アクセス キーのシークレットを一覧表示します。 |
> | Microsoft.Logic/workflows/accessKeys/regenerate/action | アクセス キーのシークレットを再生成します。 |
> | Microsoft.Logic/workflows/detectors/read | ワークフロー検出機能を読み取ります。 |
> | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | ワークフローの診断設定を読み取ります。 |
> | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/write | ワークフローの診断設定を作成または更新します。 |
> | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | ワークフローのログ定義を読み取ります。 |
> | Microsoft.Logic/workflows/providers/Microsoft.Insights/metricDefinitions/read | ワークフローのメトリック定義を読み取ります。 |
> | Microsoft.Logic/workflows/runs/read | ワークフロー実行を読み取ります。 |
> | Microsoft.Logic/workflows/runs/delete | ワークフローの実行を削除します。 |
> | Microsoft.Logic/workflows/runs/cancel/action | ワークフローの実行を取り消します。 |
> | Microsoft.Logic/workflows/runs/actions/read | ワークフロー実行アクションを読み取ります。 |
> | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | ワークフロー実行アクションの式のトレースを取得します。 |
> | Microsoft.Logic/workflows/runs/actions/repetitions/read | ワークフロー実行アクションの繰り返しを読み取ります。 |
> | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | ワークフロー実行アクションの繰り返し式のトレースを取得します。 |
> | Microsoft.Logic/workflows/runs/actions/repetitions/requestHistories/read | ワークフロー実行の繰り返しアクション要求の履歴を読み取ります。 |
> | Microsoft.Logic/workflows/runs/actions/requestHistories/read | ワークフロー実行アクション要求の履歴を読み取ります。 |
> | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | ワークフロー実行アクションのスコープの繰り返しを読み取ります。 |
> | Microsoft.Logic/workflows/runs/operations/read | ワークフロー実行操作の状態を読み取ります。 |
> | Microsoft.Logic/workflows/triggers/read | トリガーを読み取ります。 |
> | Microsoft.Logic/workflows/triggers/run/action | トリガーを実行します。 |
> | Microsoft.Logic/workflows/triggers/reset/action | トリガーをリセットします。 |
> | Microsoft.Logic/workflows/triggers/setState/action | トリガーの状態を設定します。 |
> | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | トリガーのコールバック URL を取得します。 |
> | Microsoft.Logic/workflows/triggers/histories/read | トリガーの履歴を読み取ります。 |
> | Microsoft.Logic/workflows/triggers/histories/resubmit/action | ワークフロー トリガーを再送信します。 |
> | Microsoft.Logic/workflows/versions/read | ワークフローのバージョンを読み取ります。 |
> | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | トリガーのコールバック URL を取得します。 |

### <a name="microsoftrelay"></a>Microsoft.Relay

Azure サービス:[Azure Relay](../azure-relay/relay-what-is-it.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Relay/checkNamespaceAvailability/action | 特定のサブスクリプションで名前空間を使用できるかどうかを確認します。 この API は非推奨です。代わりに CheckNameAvailability を使用してください。 |
> | Microsoft.Relay/checkNameAvailability/action | 特定のサブスクリプションで名前空間を使用できるかどうかを確認します。 |
> | Microsoft.Relay/register/action | Relay リソース プロバイダーにサブスクリプションを登録し、リレー リソースを作成できるようにします。 |
> | Microsoft.Relay/unregister/action | Relay リソース プロバイダーにサブスクリプションを登録し、リレー リソースを作成できるようにします。 |
> | Microsoft.Relay/namespaces/write | 名前空間リソースを作成し、そのプロパティを更新します。 名前空間のタグとキャパシティは、更新できるプロパティです。 |
> | Microsoft.Relay/namespaces/read | 名前空間リソースの説明の一覧を取得します。 |
> | Microsoft.Relay/namespaces/Delete | 名前空間リソースを削除します。 |
> | Microsoft.Relay/namespaces/authorizationRules/action | 名前空間の承認規則を更新します。 この API は非推奨です。 名前空間の承認規則を更新するには、代わりに PUT 呼び出しを使用してください。 この操作は、API バージョン 2017-04-01 ではサポートされていません。 |
> | Microsoft.Relay/namespaces/removeAcsNamepsace/action | ACS 名前空間を削除します。 |
> | Microsoft.Relay/namespaces/privateEndpointConnectionsApproval/action | プライベート エンドポイント接続を承認します |
> | Microsoft.Relay/namespaces/authorizationRules/read | 名前空間の承認規則の説明の一覧を取得します。 |
> | Microsoft.Relay/namespaces/authorizationRules/write | 名前空間レベルの承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権、プライマリ キー、セカンダリ キーを更新できます。 |
> | Microsoft.Relay/namespaces/authorizationRules/delete | 名前空間の承認規則を削除します。 名前空間の既定の承認規則は削除できません。  |
> | Microsoft.Relay/namespaces/authorizationRules/listkeys/action | 名前空間への接続文字列を取得します。 |
> | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | リソースのプライマリ キーまたはセカンダリ キーを再生成します。 |
> | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | 指定されたサブスクリプションで名前空間別名を使用できるかどうかを確認します。 |
> | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | 名前空間に関連付けられているディザスター リカバリーの構成を作成または更新します。 |
> | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | 名前空間に関連付けられているディザスター リカバリーの構成を取得します。 |
> | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | 名前空間に関連付けられているディザスター リカバリー構成を削除します。 この操作は、プライマリ名前空間からのみ呼び出すことができます。 |
> | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | ディザスター リカバリーを無効にし、プライマリ名前空間からセカンダリ名前空間への変更の複製を停止します。 |
> | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | geo DR フェールオーバーを呼び出し、セカンダリ名前空間を指すように名前空間の別名を再構成します。 |
> | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | ディザスター リカバリーのプライマリ名前空間の承認規則を取得します。 |
> | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | ディザスター リカバリーのプライマリ名前空間の承認規則キーを取得します。 |
> | Microsoft.Relay/namespaces/HybridConnections/write | HybridConnection プロパティを作成または更新します。 |
> | Microsoft.Relay/namespaces/HybridConnections/read | HybridConnection リソースの説明の一覧を取得します。 |
> | Microsoft.Relay/namespaces/HybridConnections/Delete | HybridConnection リソースを削除する操作。 |
> | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | HybridConnection を更新する操作です。 この操作は、API バージョン 2017-04-01 ではサポートされていません。 承認規則。 承認規則を更新するには、PUT 呼び出しを使用してください。 |
> | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  HybridConnection 承認規則の一覧を取得します。 |
> | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | HybridConnection の承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権を更新できます。 |
> | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | HybridConnection の承認規則を削除する操作。 |
> | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | HybridConnection への接続文字列を取得します。 |
> | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | リソースのプライマリ キーまたはセカンダリ キーを再生成します。 |
> | Microsoft.Relay/namespaces/messagingPlan/read | 名前空間のメッセージング プランを取得します。<br>この API は非推奨です。<br>より新しい API バージョンでは、MessagingPlan リソースにより公開されるプロパティは (親) 名前空間リソースに移動されています。<br>この操作は、API バージョン 2017-04-01 ではサポートされていません。 |
> | Microsoft.Relay/namespaces/messagingPlan/write | 名前空間のメッセージング プランを更新します。<br>この API は非推奨です。<br>より新しい API バージョンでは、MessagingPlan リソースにより公開されるプロパティは (親) 名前空間リソースに移動されています。<br>この操作は、API バージョン 2017-04-01 ではサポートされていません。 |
> | Microsoft.Relay/namespaces/networkrulesets/read | NetworkRuleSet リソースを取得します |
> | Microsoft.Relay/namespaces/networkrulesets/write | VNET ルール リソースを作成します |
> | Microsoft.Relay/namespaces/networkrulesets/delete | VNET ルール リソースを削除します |
> | Microsoft.Relay/namespaces/operationresults/read | 名前空間の操作の状態を取得します。 |
> | Microsoft.Relay/namespaces/privateEndpointConnectionProxies/validate/action | プライベート エンドポイント接続プロキシを検証します |
> | Microsoft.Relay/namespaces/privateEndpointConnectionProxies/read | プライベート エンドポイント接続プロキシを取得します |
> | Microsoft.Relay/namespaces/privateEndpointConnectionProxies/write | プライベート エンドポイント接続プロキシを作成します |
> | Microsoft.Relay/namespaces/privateEndpointConnectionProxies/delete | プライベート エンドポイント接続プロキシを削除します |
> | Microsoft.Relay/namespaces/privateEndpointConnectionProxies/operationstatus/read | 非同期のプライベート エンドポイント操作の状態を取得します |
> | Microsoft.Relay/namespaces/privateEndpointConnections/read | プライベート エンドポイント接続を取得します |
> | Microsoft.Relay/namespaces/privateEndpointConnections/write | プライベート エンドポイント接続を作成または更新します |
> | Microsoft.Relay/namespaces/privateEndpointConnections/delete | プライベート エンドポイント接続を削除します |
> | Microsoft.Relay/namespaces/privateEndpointConnections/operationstatus/read | 非同期のプライベート エンドポイント操作の状態を取得します |
> | Microsoft.Relay/namespaces/privateLinkResources/read | プライベート エンドポイント接続をサポートするリソースの種類を取得します |
> | Microsoft.Relay/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | 名前空間の診断設定リソースの説明の一覧を取得します。 |
> | Microsoft.Relay/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | 名前空間の診断設定リソースの説明の一覧を取得します。 |
> | Microsoft.Relay/namespaces/providers/Microsoft.Insights/logDefinitions/read | 名前空間のログ リソースの説明の一覧を取得します。 |
> | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | 名前空間のメトリック リソースの説明の一覧を取得します。 |
> | Microsoft.Relay/namespaces/WcfRelays/write | WcfRelay のプロパティを作成または更新します。 |
> | Microsoft.Relay/namespaces/WcfRelays/read | WcfRelay リソースの説明の一覧を取得します。 |
> | Microsoft.Relay/namespaces/WcfRelays/Delete | WcfRelay リソースを削除する操作。 |
> | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | WcfRelay を更新する操作です。 この操作は、API バージョン 2017-04-01 ではサポートされていません。 承認規則。 承認規則を更新するには、PUT 呼び出しを使用してください。 |
> | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  WcfRelay 承認規則の一覧を取得します。 |
> | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | WcfRelay の承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権を更新できます。 |
> | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | WcfRelay の承認規則を削除する操作。 |
> | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | WcfRelay への接続文字列を取得します。 |
> | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | リソースのプライマリ キーまたはセカンダリ キーを再生成します。 |
> | Microsoft.Relay/operations/read | 操作を取得します。 |

### <a name="microsoftservicebus"></a>Microsoft.ServiceBus

Azure サービス:[Service Bus](../service-bus-messaging/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.ServiceBus/checkNamespaceAvailability/action | 特定のサブスクリプションで名前空間を使用できるかどうかを確認します。 この API は非推奨です。代わりに CheckNameAvailability を使用してください。 |
> | Microsoft.ServiceBus/checkNameAvailability/action | 特定のサブスクリプションで名前空間を使用できるかどうかを確認します。 |
> | Microsoft.ServiceBus/register/action | ServiceBus リソース プロバイダーにサブスクリプションを登録し、ServiceBus リソースを作成できるようにします。 |
> | Microsoft.ServiceBus/unregister/action | ServiceBus リソース プロバイダーにサブスクリプションを登録し、ServiceBus リソースを作成できるようにします。 |
> | Microsoft.ServiceBus/locations/deleteVirtualNetworkOrSubnets/action | 指定した VNet について、ServiceBus リソース プロバイダー内の VNet ルールを削除します |
> | Microsoft.ServiceBus/namespaces/write | 名前空間リソースを作成し、そのプロパティを更新します。 名前空間のタグとキャパシティは、更新できるプロパティです。 |
> | Microsoft.ServiceBus/namespaces/read | 名前空間リソースの説明の一覧を取得します。 |
> | Microsoft.ServiceBus/namespaces/Delete | 名前空間リソースを削除します。 |
> | Microsoft.ServiceBus/namespaces/authorizationRules/action | 名前空間の承認規則を更新します。 この API は非推奨です。 名前空間の承認規則を更新するには、代わりに PUT 呼び出しを使用してください。 この操作は、API バージョン 2017-04-01 ではサポートされていません。 |
> | Microsoft.ServiceBus/namespaces/migrate/action | 名前空間の操作を移行します。 |
> | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | ACS 名前空間を削除します。 |
> | Microsoft.ServiceBus/namespaces/privateEndpointConnectionsApproval/action | プライベート エンドポイント接続を承認します |
> | Microsoft.ServiceBus/namespaces/authorizationRules/write | 名前空間レベルの承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権、プライマリ キー、セカンダリ キーを更新できます。 |
> | Microsoft.ServiceBus/namespaces/authorizationRules/read | 名前空間の承認規則の説明の一覧を取得します。 |
> | Microsoft.ServiceBus/namespaces/authorizationRules/delete | 名前空間の承認規則を削除します。 名前空間の既定の承認規則は削除できません。  |
> | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | 名前空間への接続文字列を取得します。 |
> | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | リソースのプライマリ キーまたはセカンダリ キーを再生成します。 |
> | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | 指定されたサブスクリプションで名前空間別名を使用できるかどうかを確認します。 |
> | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | 名前空間に関連付けられているディザスター リカバリーの構成を作成または更新します。 |
> | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | 名前空間に関連付けられているディザスター リカバリーの構成を取得します。 |
> | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | 名前空間に関連付けられているディザスター リカバリー構成を削除します。 この操作は、プライマリ名前空間からのみ呼び出すことができます。 |
> | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | ディザスター リカバリーを無効にし、プライマリ名前空間からセカンダリ名前空間への変更の複製を停止します。 |
> | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | geo DR フェールオーバーを呼び出し、セカンダリ名前空間を指すように名前空間の別名を再構成します。 |
> | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | ディザスター リカバリーのプライマリ名前空間の承認規則を取得します。 |
> | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | ディザスター リカバリーのプライマリ名前空間の承認規則キーを取得します。 |
> | Microsoft.ServiceBus/namespaces/eventGridFilters/write | 名前空間に関連付けられている Event Grid フィルターを作成または更新します。 |
> | Microsoft.ServiceBus/namespaces/eventGridFilters/read | 名前空間に関連付けられている Event Grid フィルターを取得します。 |
> | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | 名前空間に関連付けられている Event Grid フィルターを削除します。 |
> | Microsoft.ServiceBus/namespaces/eventhubs/read | EventHub リソースの説明の一覧を取得します。 |
> | Microsoft.ServiceBus/namespaces/ipFilterRules/read | IP フィルター リソースを取得します |
> | Microsoft.ServiceBus/namespaces/ipFilterRules/write | IP フィルター リソースを作成します |
> | Microsoft.ServiceBus/namespaces/ipFilterRules/delete | IP フィルター リソースを削除します |
> | Microsoft.ServiceBus/namespaces/messagingPlan/read | 名前空間のメッセージング プランを取得します。<br>この API は非推奨です。<br>より新しい API バージョンでは、MessagingPlan リソースにより公開されるプロパティは (親) 名前空間リソースに移動されています。<br>この操作は、API バージョン 2017-04-01 ではサポートされていません。 |
> | Microsoft.ServiceBus/namespaces/messagingPlan/write | 名前空間のメッセージング プランを更新します。<br>この API は非推奨です。<br>より新しい API バージョンでは、MessagingPlan リソースにより公開されるプロパティは (親) 名前空間リソースに移動されています。<br>この操作は、API バージョン 2017-04-01 ではサポートされていません。 |
> | Microsoft.ServiceBus/namespaces/migrationConfigurations/write | 移行構成を作成または更新します。 これにより、Standard から Premium 名前空間へのリソースの同期が開始します |
> | Microsoft.ServiceBus/namespaces/migrationConfigurations/read | 移行および保留中のレプリケーション操作の状態を示す移行構成を取得します |
> | Microsoft.ServiceBus/namespaces/migrationConfigurations/delete | 移行構成を削除します。 |
> | Microsoft.ServiceBus/namespaces/migrationConfigurations/revert/action | Standard から Premium 名前空間への移行を元に戻します |
> | Microsoft.ServiceBus/namespaces/migrationConfigurations/upgrade/action | Standard 名前空間に関連付けられている DNS を移行が完了した Premium 名前空間に割り当て、Standard から Premium 名前空間へ同期中のリソースを停止します |
> | Microsoft.ServiceBus/namespaces/networkruleset/read | NetworkRuleSet リソースを取得します |
> | Microsoft.ServiceBus/namespaces/networkruleset/write | VNET ルール リソースを作成します |
> | Microsoft.ServiceBus/namespaces/networkruleset/delete | VNET ルール リソースを削除します |
> | Microsoft.ServiceBus/namespaces/networkrulesets/read | NetworkRuleSet リソースを取得します |
> | Microsoft.ServiceBus/namespaces/networkrulesets/write | VNET ルール リソースを作成します |
> | Microsoft.ServiceBus/namespaces/networkrulesets/delete | VNET ルール リソースを削除します |
> | Microsoft.ServiceBus/namespaces/operationresults/read | 名前空間の操作の状態を取得します。 |
> | Microsoft.ServiceBus/namespaces/privateEndpointConnectionProxies/validate/action | プライベート エンドポイント接続プロキシを検証します |
> | Microsoft.ServiceBus/namespaces/privateEndpointConnectionProxies/read | プライベート エンドポイント接続プロキシを取得します |
> | Microsoft.ServiceBus/namespaces/privateEndpointConnectionProxies/write | プライベート エンドポイント接続プロキシを作成します |
> | Microsoft.ServiceBus/namespaces/privateEndpointConnectionProxies/delete | プライベート エンドポイント接続プロキシを削除します |
> | Microsoft.ServiceBus/namespaces/privateEndpointConnectionProxies/operationstatus/read | 非同期のプライベート エンドポイント操作の状態を取得します |
> | Microsoft.ServiceBus/namespaces/privateEndpointConnections/read | プライベート エンドポイント接続を取得します |
> | Microsoft.ServiceBus/namespaces/privateEndpointConnections/write | プライベート エンドポイント接続を作成または更新します |
> | Microsoft.ServiceBus/namespaces/privateEndpointConnections/delete | プライベート エンドポイント接続を削除します |
> | Microsoft.ServiceBus/namespaces/privateEndpointConnections/operationstatus/read | 非同期のプライベート エンドポイント操作の状態を取得します |
> | Microsoft.ServiceBus/namespaces/privateLinkResources/read | プライベート エンドポイント接続をサポートするリソースの種類を取得します |
> | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | 名前空間の診断設定リソースの説明の一覧を取得します。 |
> | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | 名前空間の診断設定リソースの説明の一覧を取得します。 |
> | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | 名前空間のログ リソースの説明の一覧を取得します。 |
> | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | 名前空間のメトリック リソースの説明の一覧を取得します。 |
> | Microsoft.ServiceBus/namespaces/queues/write | キューのプロパティを作成または更新します。 |
> | Microsoft.ServiceBus/namespaces/queues/read | キュー リソースの説明の一覧を取得します。 |
> | Microsoft.ServiceBus/namespaces/queues/Delete | キュー リソースを削除する操作。 |
> | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | キューを更新する操作です。 この操作は、API バージョン 2017-04-01 ではサポートされていません。 承認規則。 承認規則を更新するには、PUT 呼び出しを使用してください。 |
> | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | キューの承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権を更新できます。 |
> | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  キューの承認規則の一覧を取得します。 |
> | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | キューの承認規則を削除する操作。 |
> | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | キューへの接続文字列を取得します。 |
> | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | リソースのプライマリ キーまたはセカンダリ キーを再生成します。 |
> | Microsoft.ServiceBus/namespaces/skus/read | 名前空間でサポートされている SKU を一覧表示します。 |
> | Microsoft.ServiceBus/namespaces/topics/write | トピックのプロパティを作成または更新します。 |
> | Microsoft.ServiceBus/namespaces/topics/read | トピック リソースの説明の一覧を取得します。 |
> | Microsoft.ServiceBus/namespaces/topics/Delete | トピック リソースを削除する操作。 |
> | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | トピックを更新する操作です。 この操作は、API バージョン 2017-04-01 ではサポートされていません。 承認規則。 承認規則を更新するには、PUT 呼び出しを使用してください。 |
> | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | トピックの承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権を更新できます。 |
> | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  トピックの承認規則の一覧を取得します。 |
> | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | トピックの承認規則を削除する操作。 |
> | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | トピックへの接続文字列を取得します。 |
> | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | リソースのプライマリ キーまたはセカンダリ キーを再生成します。 |
> | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | TopicSubscription のプロパティを作成または更新します。 |
> | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | TopicSubscription リソースの説明の一覧を取得します。 |
> | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | TopicSubscription リソースを削除する操作。 |
> | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | 規則のプロパティを作成または更新します。 |
> | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | 規則リソースの説明の一覧を取得します。 |
> | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | 規則リソースを削除する操作。 |
> | Microsoft.ServiceBus/namespaces/virtualNetworkRules/read | VNET ルール リソースを取得します |
> | Microsoft.ServiceBus/namespaces/virtualNetworkRules/write | VNET ルール リソースを作成します |
> | Microsoft.ServiceBus/namespaces/virtualNetworkRules/delete | VNET ルール リソースを削除します |
> | Microsoft.ServiceBus/operations/read | 操作を取得します。 |
> | Microsoft.ServiceBus/sku/read | SKU リソースの説明の一覧を取得します。 |
> | Microsoft.ServiceBus/sku/regions/read | SkuRegions リソースの説明の一覧を取得します。 |
> | **DataAction** | **説明** |
> | Microsoft.ServiceBus/namespaces/messages/send/action | メッセージを送信する |
> | Microsoft.ServiceBus/namespaces/messages/receive/action | メッセージを受信する |

## <a name="identity"></a>ID

### <a name="microsoftaad"></a>Microsoft.AAD

Azure サービス:[Azure Active Directory Domain Services](../active-directory-domain-services/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.AAD/unregister/action | ドメイン サービスを登録解除します |
> | Microsoft.AAD/register/action | ドメイン サービスを登録する |
> | Microsoft.AAD/domainServices/read | ドメイン サービスを読み取ります |
> | Microsoft.AAD/domainServices/write | ドメイン サービスを書き込む |
> | Microsoft.AAD/domainServices/delete | ドメイン サービスを削除する |
> | Microsoft.AAD/domainServices/oucontainer/read | Ou コンテナーを読み取ります |
> | Microsoft.AAD/domainServices/oucontainer/write | Ou コンテナーを書き込みます |
> | Microsoft.AAD/domainServices/oucontainer/delete | Ou コンテナーを削除します |
> | Microsoft.AAD/locations/operationresults/read |  |
> | Microsoft.AAD/Operations/read |  |

### <a name="microsoftaadiam"></a>microsoft.aadiam

Azure サービス:Azure Active Directory

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.aadiam/azureADMetrics/read | Azure AD メトリックの定義を読み取ります |
> | microsoft.aadiam/azureADMetrics/write | Azure AD メトリックの定義を作成および更新します |
> | microsoft.aadiam/azureADMetrics/delete | Azure AD メトリックの定義を削除します |
> | microsoft.aadiam/diagnosticsettings/write | 診断設定を書き込みます。 |
> | microsoft.aadiam/diagnosticsettings/read | 診断設定を読み取ります。 |
> | microsoft.aadiam/diagnosticsettings/delete | 診断設定を削除します。 |
> | microsoft.aadiam/diagnosticsettingscategories/read | 診断設定のカテゴリを読み取ります。 |
> | microsoft.aadiam/metricDefinitions/read | テナントレベルのメトリック定義を読み取ります。 |
> | microsoft.aadiam/metrics/read | テナントレベルのメトリックを読み取ります。 |
> | microsoft.aadiam/privateLinkForAzureAD/read | Private Link ポリシーの定義を読み取ります。 |
> | microsoft.aadiam/privateLinkForAzureAD/write | Private Link のポリシー定義を作成および更新します |
> | microsoft.aadiam/privateLinkForAzureAD/delete | Private Link のポリシーの定義を削除します |
> | microsoft.aadiam/privateLinkForAzureAD/privateEndpointConnectionProxies/read | Private Link プロキシを読み取ります |
> | microsoft.aadiam/privateLinkForAzureAD/privateEndpointConnectionProxies/delete | Private Link プロキシを削除します |
> | microsoft.aadiam/privateLinkForAzureAD/privateEndpointConnectionProxies/validate/action | Private Link プロキシを検証します |
> | microsoft.aadiam/privateLinkForAzureAD/privateEndpointConnections/read | PrivateEndpointConnections を読み取ります |
> | microsoft.aadiam/privateLinkForAzureAD/privateEndpointConnections/write | PrivateEndpointConnections を作成および更新します |
> | microsoft.aadiam/privateLinkForAzureAD/privateEndpointConnections/delete | PrivateEndpointConnections を削除します |
> | microsoft.aadiam/privateLinkForAzureAD/privateLinkResources/read | PrivateLinkResources を読み取ります |
> | microsoft.aadiam/privateLinkForAzureAD/privateLinkResources/write | PrivateLinkResources を作成および更新します |
> | microsoft.aadiam/privateLinkForAzureAD/privateLinkResources/delete | PrivateLinkResources を削除します |

### <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

Azure サービス:[Azure Active Directory](../active-directory/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.ADHybridHealthService/configuration/action | テナント構成を更新します。 |
> | Microsoft.ADHybridHealthService/services/action | テナントのサービス インスタンスを更新します。 |
> | Microsoft.ADHybridHealthService/addsservices/action | テナントの新しいフォレストを作成します。 |
> | Microsoft.ADHybridHealthService/register/action | ADHybrid Health Service リソース プロバイダーを登録し、ADHybrid Health Service リソースの作成を有効にします。 |
> | Microsoft.ADHybridHealthService/unregister/action | ADHybrid Health Service リソース プロバイダーへのサブスクリプションの登録を解除します。 |
> | Microsoft.ADHybridHealthService/addsservices/write | テナントの ADDomainService インスタンスを作成または更新します。 |
> | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | サーバー インスタンスを追加します。 |
> | Microsoft.ADHybridHealthService/addsservices/read | 指定されたサービス名のサービスの詳細を取得します。 |
> | Microsoft.ADHybridHealthService/addsservices/delete | サービス、そのサーバー、および正常性データを削除します。 |
> | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | 指定されたサービス名のすべてのサーバーを取得します。 |
> | Microsoft.ADHybridHealthService/addsservices/alerts/read | alertid、アラートの発生日、アラートの最終検出日、アラートの説明、最終更新日、アラート レベル、アラートの状態、アラートのトラブルシューティングのリンクなど、フォレストのアラートに関する詳細を取得します。 |
> | Microsoft.ADHybridHealthService/addsservices/configuration/read | フォレストのサービス構成を取得します。 たとえば、フォレスト名、機能レベル、ドメイン名前付けマスター FSMO ロール、スキーマ マスター FSMO ロールなどです。 |
> | Microsoft.ADHybridHealthService/addsservices/dimensions/read | フォレストのドメインとサイトの詳細を取得します。 たとえば、正常性の状態、アクティブなアラート、解決済みのアラート、ドメインの機能レベルなどのプロパティ、フォレスト、インフラストラクチャ マスター、PDC、RID マスターなどです。  |
> | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | フォレストのユーザーの基本設定を取得します。<br>たとえば、ldapsuccessfulbinds、ntlmauthentications、kerberosauthentications、addsinsightsagentprivatebytes、ldapsearches などの MetricCounterName です。<br>UI グラフなどの設定。 |
> | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | フォレスト名、このフォレスト以下にあるドメイン数、サイト数、サイトの詳細など、特定のフォレストの概要を取得します。 |
> | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | 特定のサービスでサポートされているメトリックの一覧を取得します。<br>たとえば、ADFS サービスのエクストラネット アカウント ロックアウト、失敗した要求の総数、未処理のトークン要求 (プロキシ)、トークン要求/秒など。<br>ADDomainService の NTLM 認証/秒、LDAP 成功バインド数/秒、LDAP バインド時間、LDAP アクティブ スレッド数、Kerberos 認証数/秒、ADQ スレッド総数など。<br>実行プロファイルの待機時間、確立された TCP 接続、Insights エージェントのプライベート バイト、統計情報の Azure AD for ADSync サービスへのエクスポート。 |
> | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | サービスが指定されている場合、この API はメトリック情報を取得します。<br>たとえば、この API を使用して、以下のものに関連する情報を取得できます。ADFederation サービスのエクストラネット アカウント ロックアウト、失敗した要求の総数、未処理のトークン要求 (プロキシ)、トークン要求/秒など。<br>ADDomainService の NTLM 認証/秒、LDAP 成功バインド数/秒、LDAP バインド時間、LDAP アクティブ スレッド数、Kerberos 認証数/秒、ADQ スレッド総数など。<br>実行プロファイルの待機時間、確立された TCP 接続、Insights エージェントのプライベート バイト、統計情報の Azure AD for Sync サービスへのエクスポート。 |
> | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | この API は、Premium テナントのすべてのオンボード ADDomainServices の一覧を取得します。 |
> | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | 指定されたサービス名のすべてのサーバーについてレプリケーションの詳細を取得します。 |
> | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | ドメイン コントローラー数と、レプリケーション エラー数 (ある場合) を取得します。 |
> | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | 特定のフォレストについて、詳細なドメイン コントローラーの一覧とレプリケーションの詳細を取得します。 |
> | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | 特定のサービスとテナントのサーバーを削除します。 |
> | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | ADDomainService のサーバー登録時にこの API が呼び出され、新しいサーバーをオンボードするための資格情報が取得されます。 |
> | Microsoft.ADHybridHealthService/configuration/write | テナント構成を作成します。 |
> | Microsoft.ADHybridHealthService/configuration/read | テナント構成を読み取ります。 |
> | Microsoft.ADHybridHealthService/logs/read | テナントのエージェントのインストールおよび登録ログを取得します。 |
> | Microsoft.ADHybridHealthService/logs/contents/read | BLOB に格納されているエージェントのインストールおよび登録ログの内容を取得します。 |
> | Microsoft.ADHybridHealthService/operations/read | システムがサポートする操作の一覧を取得します。 |
> | Microsoft.ADHybridHealthService/reports/availabledeployments/read | 顧客のインシデントをサポートするために DevOps から使用される、使用可能なリージョンの一覧を取得します。 |
> | Microsoft.ADHybridHealthService/reports/badpassword/read | Active Directory フェデレーション サービス内のすべてのユーザーに関する無効なパスワード試行の一覧を取得します。 |
> | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | 指定されたテナントの 1 日の IPAddress あたりのユーザー名/パスワードの失敗した試行の頻度について、新しくエンキューされたレポート ジョブの状態と結果を含む BLOB SAS URI を取得します。 |
> | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | DevOps が承認したテナントの一覧を取得します。 通常、カスタマー サポートに使用されます。 |
> | Microsoft.ADHybridHealthService/reports/isdevops/read | DevOps が承認したテナントかどうかを示す値を取得します。 |
> | Microsoft.ADHybridHealthService/reports/selectdevopstenant/read | 選択されている DevOps テナントの userid (objectid) を更新します。 |
> | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | 指定されたテナントの選択されている展開を取得します。 |
> | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | テナント ID が指定されている場合、テナントの保存場所を取得します。 |
> | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | データのアクセス元の地域を取得します。 |
> | Microsoft.ADHybridHealthService/services/write | テナントにサービス インスタンスを作成します。 |
> | Microsoft.ADHybridHealthService/services/read | テナントのサービス インスタンスを読み取ります。 |
> | Microsoft.ADHybridHealthService/services/delete | テナントのサービス インスタンスを削除します。 |
> | Microsoft.ADHybridHealthService/services/servicemembers/action | サービスのサーバー インスタンスを作成または更新します。 |
> | Microsoft.ADHybridHealthService/services/alerts/read | サービスのアラートを読み取ります。 |
> | Microsoft.ADHybridHealthService/services/alerts/read | サービスのアラートを読み取ります。 |
> | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | 機能名が指定されている場合、その機能を使用するために必要なすべての要件をサービスが満たしているかどうかを検証します。 |
> | Microsoft.ADHybridHealthService/services/exporterrors/read | 指定された同期サービスのエクスポート エラーを取得します。 |
> | Microsoft.ADHybridHealthService/services/exportstatus/read | 指定されたサービスのエクスポート状態を取得します。 |
> | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | 指定されたサービスとサーバーに対するアラートのフィードバックを取得します。 |
> | Microsoft.ADHybridHealthService/services/ipAddressAggregates/read | サービスにアクセスしようとした無効な IP を読み取ります。 |
> | Microsoft.ADHybridHealthService/services/ipAddressAggregateSettings/read | 無効な IP のアラームしきい値を読み取ります。 |
> | Microsoft.ADHybridHealthService/services/ipAddressAggregateSettings/write | 無効な IP のアラームしきい値を書き込みます。 |
> | Microsoft.ADHybridHealthService/services/metricmetadata/read | 特定のサービスでサポートされているメトリックの一覧を取得します。<br>たとえば、ADFS サービスのエクストラネット アカウント ロックアウト、失敗した要求の総数、未処理のトークン要求 (プロキシ)、トークン要求/秒など。<br>ADDomainService の NTLM 認証/秒、LDAP 成功バインド数/秒、LDAP バインド時間、LDAP アクティブ スレッド数、Kerberos 認証数/秒、ADQ スレッド総数など。<br>実行プロファイルの待機時間、確立された TCP 接続、Insights エージェントのプライベート バイト、統計情報の Azure AD for ADSync サービスへのエクスポート。 |
> | Microsoft.ADHybridHealthService/services/metrics/groups/read | サービスが指定されている場合、この API はメトリック情報を取得します。<br>たとえば、この API を使用して、以下のものに関連する情報を取得できます。ADFederation サービスのエクストラネット アカウント ロックアウト、失敗した要求の総数、未処理のトークン要求 (プロキシ)、トークン要求/秒など。<br>ADDomainService の NTLM 認証/秒、LDAP 成功バインド数/秒、LDAP バインド時間、LDAP アクティブ スレッド数、Kerberos 認証数/秒、ADQ スレッド総数など。<br>実行プロファイルの待機時間、確立された TCP 接続、Insights エージェントのプライベート バイト、統計情報の Azure AD for Sync サービスへのエクスポート。 |
> | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | サービスが指定されている場合、この API はそのサービスのメトリックの平均を取得します。<br>たとえば、この API を使用して、以下のものに関連する情報を取得できます。ADFederation サービスのエクストラネット アカウント ロックアウト、失敗した要求の総数、未処理のトークン要求 (プロキシ)、トークン要求/秒など。<br>ADDomainService の NTLM 認証/秒、LDAP 成功バインド数/秒、LDAP バインド時間、LDAP アクティブ スレッド数、Kerberos 認証数/秒、ADQ スレッド総数など。<br>実行プロファイルの待機時間、確立された TCP 接続、Insights エージェントのプライベート バイト、統計情報の Azure AD for Sync サービスへのエクスポート。 |
> | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | サービスが指定されている場合、この API はそのサービスの集計ビューを取得します。<br>たとえば、この API を使用して、以下のものに関連する情報を取得できます。ADFederation サービスのエクストラネット アカウント ロックアウト、失敗した要求の総数、未処理のトークン要求 (プロキシ)、トークン要求/秒など。<br>ADDomainService の NTLM 認証/秒、LDAP 成功バインド数/秒、LDAP バインド時間、LDAP アクティブ スレッド数、Kerberos 認証数/秒、ADQ スレッド総数など。<br>実行プロファイルの待機時間、確立された TCP 接続、Insights エージェントのプライベート バイト、統計情報の Azure AD for Sync サービスへのエクスポート。 |
> | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | サービスの監視構成を追加または更新します。 |
> | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | 指定されたサービスの監視構成を取得します。 |
> | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | サービスの監視構成を追加または更新します。 |
> | Microsoft.ADHybridHealthService/services/premiumcheck/read | この API は、Premium テナントのすべてのオンボード サービスの一覧を取得します。 |
> | Microsoft.ADHybridHealthService/services/reports/generateBlobUri/action | 危険な IP レポートを生成し、それを指す URI を返します。 |
> | Microsoft.ADHybridHealthService/services/reports/blobUris/read | 過去 7 日間のすべての危険な IP レポートの URI を取得します。 |
> | Microsoft.ADHybridHealthService/services/reports/details/read | 過去 7 日間にパスワードの誤入力エラーが発生した上位 50 人のユーザーのレポートを取得します。 |
> | Microsoft.ADHybridHealthService/services/servicemembers/read | サービスのサーバー インスタンスを読み取ります。 |
> | Microsoft.ADHybridHealthService/services/servicemembers/delete | サービスのサーバー インスタンスを削除します。 |
> | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | サーバーのアラートを読み取ります。 |
> | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | サーバー登録時にこの API が呼び出され、新しいサーバーをオンボードするための資格情報が取得されます。 |
> | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | サーバーが指定されている場合、この API は、サーバーによってアップロードされているデータ型と各アップロードの最終時刻の一覧を取得します。 |
> | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | 指定された同期サービスの同期エクスポート エラーの詳細を取得します。 |
> | Microsoft.ADHybridHealthService/services/servicemembers/metrics/read | 指定したサービスおよびサービス メンバーのコネクタと実行プロファイルの一覧を取得します。 |
> | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | サービスが指定されている場合、この API はメトリック情報を取得します。<br>たとえば、この API を使用して、以下のものに関連する情報を取得できます。ADFederation サービスのエクストラネット アカウント ロックアウト、失敗した要求の総数、未処理のトークン要求 (プロキシ)、トークン要求/秒など。<br>ADDomainService の NTLM 認証/秒、LDAP 成功バインド数/秒、LDAP バインド時間、LDAP アクティブ スレッド数、Kerberos 認証数/秒、ADQ スレッド総数など。<br>実行プロファイルの待機時間、確立された TCP 接続、Insights エージェントのプライベート バイト、統計情報の Azure AD for Sync サービスへのエクスポート。 |
> | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | 指定されたテナントのサービス構成を取得します。 |
> | Microsoft.ADHybridHealthService/services/tenantwhitelisting/read | 指定されたテナントの機能の許可リスト登録状態を取得します。 |

### <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

Azure サービス:[Azure Active Directory B2C](../active-directory-b2c/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.AzureActiveDirectory/register/action | Microsoft.AzureActiveDirectory リソース プロバイダーにサブスクリプションを登録します。 |
> | Microsoft.AzureActiveDirectory/b2cDirectories/write | B2C ディレクトリ リソースの作成または更新 |
> | Microsoft.AzureActiveDirectory/b2cDirectories/read | B2C ディレクトリ リソースを表示します。 |
> | Microsoft.AzureActiveDirectory/b2cDirectories/delete | B2C ディレクトリ リソースを削除します。 |
> | Microsoft.AzureActiveDirectory/b2ctenants/read | ユーザーがメンバーであるすべての B2C テナントを一覧表示します |
> | Microsoft.AzureActiveDirectory/guestUsages/write | ゲストの使用状況リソースを作成または更新します |
> | Microsoft.AzureActiveDirectory/guestUsages/read | ゲストの使用状況リソースを表示します |
> | Microsoft.AzureActiveDirectory/guestUsages/delete | ゲストの使用状況リソースを削除します |
> | Microsoft.AzureActiveDirectory/operations/read | Microsoft.AzureActiveDirectory リソース プロバイダーで使用可能なすべての API 操作を読み取ります。 |

### <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

Azure サービス:[Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.ManagedIdentity/register/action | マネージド ID リソース プロバイダーのサブスクリプションを登録します。 |
> | Microsoft.ManagedIdentity/identities/read | 既存のシステム割り当て ID を取得します。 |
> | Microsoft.ManagedIdentity/operations/read | Microsoft.ManagedIdentity リソース プロバイダーで使用できる操作を一覧表示します |
> | Microsoft.ManagedIdentity/userAssignedIdentities/read | 既存のユーザー割り当て ID を取得します。 |
> | Microsoft.ManagedIdentity/userAssignedIdentities/write | 新しいユーザー割り当て ID を作成するか、既存のユーザー割り当て ID に関連付けられているタグを更新します。 |
> | Microsoft.ManagedIdentity/userAssignedIdentities/delete | 既存のユーザー割り当て ID を削除します。 |
> | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | 既存のユーザー割り当て ID をリソースに割り当てるための RBAC アクションです。 |

## <a name="security"></a>セキュリティ

### <a name="microsoftkeyvault"></a>Microsoft.KeyVault

Azure サービス:[Key Vault](../key-vault/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.KeyVault/register/action | サブスクリプションを登録します。 |
> | Microsoft.KeyVault/unregister/action | サブスクリプションの登録を解除します。 |
> | Microsoft.KeyVault/checkNameAvailability/read | Key Vault 名が有効であり、使用されていないことを確認します。 |
> | Microsoft.KeyVault/deletedVaults/read | 論理的に削除された Key Vault のプロパティを表示します。 |
> | Microsoft.KeyVault/hsmPools/read | HSM プールのプロパティを表示します。 |
> | Microsoft.KeyVault/hsmPools/write | 新しい HSM プールを作成するか、既存の HSM プールのプロパティを更新します。 |
> | Microsoft.KeyVault/hsmPools/delete | HSM プールを削除します。 |
> | Microsoft.KeyVault/hsmPools/joinVault/action | HSM プールにキー コンテナーを結合します。 |
> | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | 仮想ネットワークまたはサブネットが削除されていることを Microsoft.KeyVault に通知します。 |
> | Microsoft.KeyVault/locations/deletedVaults/read | 論理的に削除された Key Vault のプロパティを表示します。 |
> | Microsoft.KeyVault/locations/deletedVaults/purge/action | 論理的に削除された Key Vault を消去します。 |
> | Microsoft.KeyVault/locations/operationResults/read | 長時間実行された操作の結果を確認します。 |
> | Microsoft.KeyVault/managedHSMs/read | マネージド HSM のプロパティを表示します |
> | Microsoft.KeyVault/managedHSMs/write | 新しい Managed HSM を作成するか、既存のマネージド HSM のプロパティを更新します |
> | Microsoft.KeyVault/managedHSMs/delete | マネージド HSM を削除します |
> | Microsoft.KeyVault/operations/read | Microsoft.KeyVault リソース プロバイダーで使用できる操作を一覧表示します。 |
> | Microsoft.KeyVault/vaults/read | Key Vault のプロパティを表示します。 |
> | Microsoft.KeyVault/vaults/write | 新しい Key Vault を作成するか、既存の Key Vault のプロパティを更新します。 プロパティによっては、より多くのアクセス許可が必要な場合があります。 |
> | Microsoft.KeyVault/vaults/delete | Key Vault を削除します |
> | Microsoft.KeyVault/vaults/deploy/action | Azure リソースのデプロイ時に、Key Vault 内のシークレットにアクセスできるようにします。 |
> | Microsoft.KeyVault/vaults/accessPolicies/write | マージか置換によって既存のアクセス ポリシーを更新するか、Key Vault に新しいアクセス ポリシーを追加します。 |
> | Microsoft.KeyVault/vaults/eventGridFilters/read | Key Vault の EventGrid サブスクリプションが表示されていることを Microsoft.KeyVault に通知します |
> | Microsoft.KeyVault/vaults/eventGridFilters/write | Key Vault の新しい EventGrid サブスクリプションが作成されていることを Microsoft.KeyVault に通知します |
> | Microsoft.KeyVault/vaults/eventGridFilters/delete | Key Vault の EventGrid サブスクリプションが削除されていることを Microsoft.KeyVault に通知します |
> | Microsoft.KeyVault/vaults/keys/read | 指定されたコンテナー内のキーを一覧表示するか、指定されたキーの現在のバージョンを読み取ります。 |
> | Microsoft.KeyVault/vaults/keys/write | 新しいキーの最初のバージョンが存在しない場合、それを作成します。 既に存在する場合は、既存のキーが変更されずに返されます。 この API は、後続のバージョンを作成したり、既存のキーを更新したりしません。 |
> | Microsoft.KeyVault/vaults/keys/versions/read | 指定されたキーのバージョンを一覧表示するか、指定されたバージョンのキーを読み取ります。 |
> | Microsoft.KeyVault/vaults/secrets/read | シークレットの値ではなく、プロパティを表示します。 |
> | Microsoft.KeyVault/vaults/secrets/write | 新しいシークレットを作成するか、既存のシークレットの値を更新します。 |
> | **DataAction** | **説明** |
> | Microsoft.KeyVault/vaults/certificatecas/delete | 証明書の発行者を削除します |
> | Microsoft.KeyVault/vaults/certificatecas/read | 証明書の発行者を読み取ります |
> | Microsoft.KeyVault/vaults/certificatecas/write | 証明書の発行者を書き込みます |
> | Microsoft.KeyVault/vaults/certificatecontacts/write | 証明書の連絡先を管理します |
> | Microsoft.KeyVault/vaults/certificates/delete | 証明書を削除します。 すべてのバージョンが削除されます。 |
> | Microsoft.KeyVault/vaults/certificates/read | 指定された Key Vault 内の証明書を一覧表示するか、証明書に関する情報を取得します。 |
> | Microsoft.KeyVault/vaults/certificates/backup/action | 証明書のバックアップ ファイルを作成します。 このファイルは、同じサブスクリプションの Key Vault で証明書を復元するために使用できます。 制限が適用される場合があります。 |
> | Microsoft.KeyVault/vaults/certificates/purge/action | 証明書を消去して、回復不能にします。 |
> | Microsoft.KeyVault/vaults/certificates/update/action | 特定の証明書に関連付けられている、指定された属性を更新します。 |
> | Microsoft.KeyVault/vaults/certificates/create/action | 新しい証明書を作成します。 証明書が存在しない場合は、最初のバージョンが作成されます。 それ以外の場合は、新しいバージョンが作成されます。 |
> | Microsoft.KeyVault/vaults/certificates/import/action | 秘密キーを含む、既存の有効な証明書をインポートします。<br>インポートする証明書は、PFX または PEM 形式にすることができます。<br>証明書が Key Vault に存在しない場合は、指定されたコンテンツで最初のバージョンが作成されます。<br>それ以外の場合は、指定されたコンテンツで新しいバージョンが作成されます。 |
> | Microsoft.KeyVault/vaults/certificates/recover/action | 削除された証明書を回復させます。 この操作では、削除操作を取り消します。 この操作は、論理的な削除が有効になっている資格情報コンテナーで適用でき、保有期間中に発行する必要があります。 |
> | Microsoft.KeyVault/vaults/certificates/restore/action | Key Vault によって生成されたバックアップ ファイルから、証明書とそのすべてのバージョンを復元します。 |
> | Microsoft.KeyVault/vaults/keys/read | 指定された資格情報コンテナー内のキーを一覧表示するか、キーのプロパティおよび公開マテリアルを読み取ります。<br>非対称キーの場合、この操作では公開キーを公開し、署名の暗号化や検証などの公開キー アルゴリズムを実行する機能が含まれます。<br>秘密キーと対称キーが公開されることはありません。 |
> | Microsoft.KeyVault/vaults/keys/update/action | 特定のキーに関連付けられている、指定された属性を更新します。 |
> | Microsoft.KeyVault/vaults/keys/create/action | 新しいキーを作成します。 キーが存在しない場合は、最初のバージョンが作成されます。 それ以外の場合は、指定された値で新しいバージョンが作成されます。 |
> | Microsoft.KeyVault/vaults/keys/import/action | 外部で作成されたキーをインポートします。 キーが存在しない場合は、インポートされた素材で最初のバージョンが作成されます。 それ以外の場合は、インポートされた素材で新しいバージョンが作成されます。 |
> | Microsoft.KeyVault/vaults/keys/recover/action | 削除されたキーを回復させます。 この操作では、削除操作を取り消します。 この操作は、論理的な削除が有効になっている資格情報コンテナーで適用でき、保有期間中に発行する必要があります。 |
> | Microsoft.KeyVault/vaults/keys/restore/action | Key Vault によって生成されたバックアップ ファイルから、キーとそのすべてのバージョンを復元します。 |
> | Microsoft.KeyVault/vaults/keys/delete | キーを削除します。 すべてのバージョンが削除されます。 |
> | Microsoft.KeyVault/vaults/keys/backup/action | キーのバックアップ ファイルを作成します。 このファイルは、同じサブスクリプションの Key Vault でキーを復元するために使用できます。 制限が適用される場合があります。 |
> | Microsoft.KeyVault/vaults/keys/purge/action | キーを消去して、回復不能にします。 |
> | Microsoft.KeyVault/vaults/keys/encrypt/action | キーでプレーンテキストを暗号化します。 キーが非対称の場合は、この操作を読み取りアクセス権を持つプリンシパルで実行できることに注意してください。 |
> | Microsoft.KeyVault/vaults/keys/decrypt/action | キーで暗号化テキストの暗号化を解除します。 |
> | Microsoft.KeyVault/vaults/keys/wrap/action | Key Vault キーで対称キーをラップします。 Key Vault キーが非対称の場合は、この操作を読み取りアクセス権を持つプリンシパルで実行できることに注意してください。 |
> | Microsoft.KeyVault/vaults/keys/unwrap/action | Key Vault キーで対称キーをラップ解除します。 |
> | Microsoft.KeyVault/vaults/keys/sign/action | キーでメッセージ ダイジェスト (ハッシュ) に署名します。 |
> | Microsoft.KeyVault/vaults/keys/verify/action | キーでメッセージ ダイジェスト (ハッシュ) の署名を検証します。 キーが非対称の場合は、この操作を読み取りアクセス権を持つプリンシパルで実行できることに注意してください。 |
> | Microsoft.KeyVault/vaults/secrets/delete | シークレットを削除します。 すべてのバージョンが削除されます。 |
> | Microsoft.KeyVault/vaults/secrets/backup/action | シークレットのバックアップ ファイルを作成します。 このファイルは、同じサブスクリプションの Key Vault でシークレットを復元するために使用できます。 制限が適用される場合があります。 |
> | Microsoft.KeyVault/vaults/secrets/purge/action | シークレットを消去して回復不能にします。 |
> | Microsoft.KeyVault/vaults/secrets/update/action | 特定のシークレットに関連付けられている、指定された属性を更新します。 |
> | Microsoft.KeyVault/vaults/secrets/recover/action | 削除されたシークレットを回復させます。 この操作では、削除操作を取り消します。 この操作は、論理的な削除が有効になっている資格情報コンテナーで適用でき、保有期間中に発行する必要があります。 |
> | Microsoft.KeyVault/vaults/secrets/restore/action | Key Vault によって生成されたバックアップ ファイルから、シークレットとそのすべてのバージョンを復元します。 |
> | Microsoft.KeyVault/vaults/secrets/readMetadata/action | シークレットの値ではなく、プロパティを一覧表示または表示します。 |
> | Microsoft.KeyVault/vaults/secrets/getSecret/action | シークレットの値を取得します。 |
> | Microsoft.KeyVault/vaults/secrets/setSecret/action | シークレットの値を設定します。 シークレットが存在しない場合は、最初のバージョンが作成されます。 それ以外の場合は、指定された値で新しいバージョンが作成されます。 |
> | Microsoft.KeyVault/vaults/storageaccounts/read | マネージド ストレージ アカウントおよび SAS の定義を読み取ります。 |
> | Microsoft.KeyVault/vaults/storageaccounts/set/action | マネージド ストレージ アカウントの定義を作成または更新します。 |
> | Microsoft.KeyVault/vaults/storageaccounts/delete | マネージド ストレージ アカウントの定義を削除します。 |
> | Microsoft.KeyVault/vaults/storageaccounts/backup/action | マネージド ストレージ アカウントとその SAS (Shared Access Signature) の定義のバックアップ ファイルを作成します。 |
> | Microsoft.KeyVault/vaults/storageaccounts/purge/action | マネージド ストレージ アカウントまたは SAS (Shared Access Signature) の論理的に削除された定義を消去します。 |
> | Microsoft.KeyVault/vaults/storageaccounts/regeneratekey/action | マネージド ストレージ アカウントのアクセス キーを再生成します。 |
> | Microsoft.KeyVault/vaults/storageaccounts/recover/action | マネージド ストレージ アカウントまたは SAS (Shared Access Signature) の論理的に削除された定義を復旧します。 |
> | Microsoft.KeyVault/vaults/storageaccounts/restore/action | Key Vault によって生成されたバックアップ ファイルから、マネージド ストレージ アカウントとその SAS (Shared Access Signature) の定義を復元します。 |
> | Microsoft.KeyVault/vaults/storageaccounts/sas/set/action | マネージド ストレージ アカウントの SAS (Shared Access Signature) 定義を作成または更新します。 |
> | Microsoft.KeyVault/vaults/storageaccounts/sas/delete | マネージド ストレージ アカウントの SAS (Shared Access Signature) 定義を削除します。 |

### <a name="microsoftsecurity"></a>Microsoft.Security

Azure サービス:[セキュリティ センター](../security-center/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Security/register/action | Azure Security Center にサブスクリプションを登録します。 |
> | Microsoft.Security/unregister/action | Azure Security Center からサブスクリプションを登録解除します |
> | Microsoft.Security/adaptiveNetworkHardenings/read | Azure の保護されたリソースのアダプティブ ネットワークのセキュリティ強化機能のレコメンデーションを取得します |
> | Microsoft.Security/adaptiveNetworkHardenings/enforce/action | 特定のネットワーク セキュリティ グループに対応するセキュリティ規則を作成して、特定のトラフィックのセキュリティ強化規則を適用します |
> | Microsoft.Security/advancedThreatProtectionSettings/read | リソースの Advanced Threat Protection 設定を取得します。 |
> | Microsoft.Security/advancedThreatProtectionSettings/write | リソースの Advanced Threat Protection 設定を更新します。 |
> | Microsoft.Security/alerts/read | すべての使用可能なセキュリティ アラートを取得します。 |
> | Microsoft.Security/alertsSuppressionRules/read | すべての使用可能なセキュリティ アラートの抑制ルールを取得します |
> | Microsoft.Security/alertsSuppressionRules/write | 新しいセキュリティ アラートの抑制ルールを作成するか、既存のルールを更新します |
> | Microsoft.Security/alertsSuppressionRules/delete | セキュリティ アラートの抑制ルールを削除します |
> | Microsoft.Security/applicationWhitelistings/read | アプリケーションの許可リストを取得します |
> | Microsoft.Security/applicationWhitelistings/write | 新しいアプリケーションの許可リストを作成するか、既存のアプリケーションの許可リストを更新します |
> | Microsoft.Security/assessmentMetadata/read | サブスクリプションで利用可能なセキュリティ評価メタデータを取得します |
> | Microsoft.Security/assessmentMetadata/write | セキュリティ評価メタデータを作成または更新します |
> | Microsoft.Security/assessments/read | サブスクリプションで利用可能なセキュリティ評価を取得します |
> | Microsoft.Security/assessments/write | サブスクリプションで利用可能なセキュリティ評価を作成または更新します |
> | Microsoft.Security/automations/read | スコープの自動化を取得します |
> | Microsoft.Security/automations/write | スコープの自動化を作成または更新します |
> | Microsoft.Security/automations/delete | スコープの自動化を削除します |
> | Microsoft.Security/automations/validate/action | スコープの自動化モデルを検証します |
> | Microsoft.Security/autoProvisioningSettings/read | サブスクリプションのセキュリティ自動プロビジョニング設定を取得します |
> | Microsoft.Security/autoProvisioningSettings/write | サブスクリプションのセキュリティ自動プロビジョニング設定を作成または更新します |
> | Microsoft.Security/complianceResults/read | リソースのコンプライアンス結果を取得します。 |
> | Microsoft.Security/deviceSecurityGroups/write | IoT デバイス セキュリティ グループを作成または更新します |
> | Microsoft.Security/deviceSecurityGroups/delete | IoT デバイス セキュリティ グループを削除します |
> | Microsoft.Security/deviceSecurityGroups/read | IoT デバイス セキュリティ グループを取得します |
> | Microsoft.Security/informationProtectionPolicies/read | リソースの情報保護ポリシーを取得します |
> | Microsoft.Security/informationProtectionPolicies/write | リソースの情報保護ポリシーを更新します |
> | Microsoft.Security/iotDefenderSettings/read | IoT Defender の設定を取得します |
> | Microsoft.Security/iotDefenderSettings/write | IoT Defender の設定を作成または更新します |
> | Microsoft.Security/iotDefenderSettings/delete | IoT Defender の設定を削除します |
> | Microsoft.Security/iotDefenderSettings/PackageDownloads/action | ダウンロード可能な IoT Defender パッケージ情報を取得します |
> | Microsoft.Security/iotDefenderSettings/DownloadManagerActivation/action | サブスクリプション クォータ データを含むマネージャー アクティブ化ファイルをダウンロードします |
> | Microsoft.Security/iotSecuritySolutions/write | IoT セキュリティ ソリューションを作成または更新します |
> | Microsoft.Security/iotSecuritySolutions/delete | IoT セキュリティ ソリューションを削除します |
> | Microsoft.Security/iotSecuritySolutions/read | IoT セキュリティ ソリューションを取得します |
> | Microsoft.Security/iotSecuritySolutions/analyticsModels/read | IoT セキュリティ分析モデルを取得します |
> | Microsoft.Security/iotSecuritySolutions/analyticsModels/read | IoT アラートの種類を取得します |
> | Microsoft.Security/iotSecuritySolutions/analyticsModels/read | IoT アラートの種類を取得します |
> | Microsoft.Security/iotSecuritySolutions/analyticsModels/read | IoT アラートを取得します |
> | Microsoft.Security/iotSecuritySolutions/analyticsModels/read | IoT アラートを取得します |
> | Microsoft.Security/iotSecuritySolutions/analyticsModels/read | IoT 推奨事項の種類を取得します |
> | Microsoft.Security/iotSecuritySolutions/analyticsModels/read | IoT 推奨事項の種類を取得します |
> | Microsoft.Security/iotSecuritySolutions/analyticsModels/read | IoT 推奨事項を取得します |
> | Microsoft.Security/iotSecuritySolutions/analyticsModels/read | IoT 推奨事項を取得します |
> | Microsoft.Security/iotSecuritySolutions/analyticsModels/read | デバイスを取得します |
> | Microsoft.Security/iotSecuritySolutions/analyticsModels/aggregatedAlerts/read | IoT 集計アラートを取得します |
> | Microsoft.Security/iotSecuritySolutions/analyticsModels/aggregatedAlerts/dismiss/action | IoT 集計アラートを破棄します |
> | Microsoft.Security/iotSecuritySolutions/analyticsModels/aggregatedRecommendations/read | IoT の集計された推奨事項を取得します |
> | Microsoft.Security/iotSensors/read | IoT センサーを取得します |
> | Microsoft.Security/iotSensors/write | IoT センサーを作成または更新します |
> | Microsoft.Security/iotSensors/delete | IoT センサーを削除します |
> | Microsoft.Security/iotSensors/DownloadActivation/action | IoT センサーのアクティブ化ファイルをダウンロードします |
> | Microsoft.Security/iotSensors/TriggerTiPackageUpdate/action | 脅威インテリジェンス パッケージの更新をトリガーします |
> | Microsoft.Security/iotSensors/DownloadResetPassword/action | IoT センサーのパスワードのリセット ファイルをダウンロードします |
> | Microsoft.Security/iotSite/read | IoT サイトを取得します |
> | Microsoft.Security/iotSite/write | IoT サイトを作成または更新します |
> | Microsoft.Security/iotSite/delete | IoT サイトを削除します |
> | Microsoft.Security/locations/read | セキュリティ データの場所を取得します。 |
> | Microsoft.Security/locations/alerts/read | すべての使用可能なセキュリティ アラートを取得します。 |
> | Microsoft.Security/locations/alerts/dismiss/action | セキュリティ アラートを無視します |
> | Microsoft.Security/locations/alerts/activate/action | セキュリティ アラートをアクティブ化します。 |
> | Microsoft.Security/locations/alerts/resolve/action | セキュリティ アラートを解決します |
> | Microsoft.Security/locations/alerts/simulate/action | セキュリティ アラートをシミュレートします |
> | Microsoft.Security/locations/jitNetworkAccessPolicies/read | Just-In-Time ネットワーク アクセス ポリシーを取得します。 |
> | Microsoft.Security/locations/jitNetworkAccessPolicies/write | 新しい Just-In-Time ネットワーク アクセス ポリシーを作成するか、既存のポリシーを更新します。 |
> | Microsoft.Security/locations/jitNetworkAccessPolicies/delete | Just-In-Time ネットワーク アクセス ポリシーを削除します |
> | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | Just-In-Time ネットワーク アクセス ポリシー要求を開始します。 |
> | Microsoft.Security/locations/tasks/read | 利用可能なすべてのセキュリティ推奨事項を取得します。 |
> | Microsoft.Security/locations/tasks/start/action | セキュリティ推奨事項を開始します。 |
> | Microsoft.Security/locations/tasks/resolve/action | セキュリティ推奨事項を解決します。 |
> | Microsoft.Security/locations/tasks/activate/action | セキュリティ推奨事項をアクティブにします。 |
> | Microsoft.Security/locations/tasks/dismiss/action | セキュリティ推奨事項を無視します。 |
> | Microsoft.Security/policies/read | セキュリティ ポリシーを取得します。 |
> | Microsoft.Security/policies/write | セキュリティ ポリシーを更新します。 |
> | Microsoft.Security/pricings/read | スコープの価格設定を取得します。 |
> | Microsoft.Security/pricings/write | スコープの価格設定を更新します。 |
> | Microsoft.Security/pricings/delete | スコープの価格設定を削除します。 |
> | Microsoft.Security/secureScoreControlDefinitions/read | セキュア スコア コントロール定義を取得します |
> | Microsoft.Security/secureScoreControls/read | サブスクリプションの計算されたセキュア スコア コントロールを取得します |
> | Microsoft.Security/secureScores/read | サブスクリプションの計算されたセキュア スコアを取得します |
> | Microsoft.Security/secureScores/secureScoreControls/read | セキュア スコアを計算するために、計算されたセキュア スコア コントロールを取得します |
> | Microsoft.Security/securityContacts/read | セキュリティ連絡先を取得します。 |
> | Microsoft.Security/securityContacts/write | セキュリティ連絡先を更新します。 |
> | Microsoft.Security/securityContacts/delete | セキュリティ連絡先を削除します。 |
> | Microsoft.Security/securitySolutions/read | セキュリティ ソリューションを取得します。 |
> | Microsoft.Security/securitySolutions/write | 新しいセキュリティ ソリューションを作成するか、既存のセキュリティ ソリューションを更新します。 |
> | Microsoft.Security/securitySolutions/delete | セキュリティ ソリューションを削除します。 |
> | Microsoft.Security/securitySolutionsReferenceData/read | セキュリティ ソリューションの参照データを取得します。 |
> | Microsoft.Security/securityStatuses/read | Azure リソースのセキュリティ正常性状態を取得します。 |
> | Microsoft.Security/securityStatusesSummaries/read | スコープのセキュリティの状態の概要を取得します。 |
> | Microsoft.Security/settings/read | スコープの設定を取得します |
> | Microsoft.Security/settings/write | スコープの設定を更新します |
> | Microsoft.Security/sqlVulnerabilityAssessments/baselineRules/action | ルール結果の一覧をベースラインに追加します。 |
> | Microsoft.Security/sqlVulnerabilityAssessments/baselineRules/read | データベースのベースライン (ベースラインに追加されたすべてのルール) を返すか、指定されたルール ID のルール ベースラインの結果を取得します。 |
> | Microsoft.Security/sqlVulnerabilityAssessments/baselineRules/write | ルール ベースラインの結果を変更します。 |
> | Microsoft.Security/sqlVulnerabilityAssessments/baselineRules/delete | ベースラインからルールの結果を削除します。 |
> | Microsoft.Security/sqlVulnerabilityAssessments/scans/read | 脆弱性評価スキャン レコードの一覧を返すか、指定されたスキャン ID のスキャン レコードを取得します。 |
> | Microsoft.Security/sqlVulnerabilityAssessments/scans/scanResults/read | 脆弱性評価規則の結果の一覧を返すか、指定されたルール ID のルールの結果を取得します。 |
> | Microsoft.Security/tasks/read | 利用可能なすべてのセキュリティ推奨事項を取得します。 |
> | Microsoft.Security/webApplicationFirewalls/read | Web アプリケーション ファイアウォールを取得します。 |
> | Microsoft.Security/webApplicationFirewalls/write | 新しい Web アプリケーション ファイアウォールを作成するか、既存のWeb アプリケーション ファイアウォールを更新します。 |
> | Microsoft.Security/webApplicationFirewalls/delete | Web アプリケーション ファイアウォールを削除します。 |
> | Microsoft.Security/workspaceSettings/read | ワークスペースの設定を取得します。 |
> | Microsoft.Security/workspaceSettings/write | ワークスペースの設定を更新します。 |
> | Microsoft.Security/workspaceSettings/delete | ワークスペースの設定を削除します。 |
> | Microsoft.Security/workspaceSettings/connect/action | ワークスペースの設定の再接続の設定を変更します。 |

### <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

Azure サービス:Microsoft Monitoring Insights

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.SecurityGraph/diagnosticsettings/write | 診断設定を書き込みます。 |
> | Microsoft.SecurityGraph/diagnosticsettings/read | 診断設定を読み取ります。 |
> | Microsoft.SecurityGraph/diagnosticsettings/delete | 診断設定を削除します。 |
> | Microsoft.SecurityGraph/diagnosticsettingscategories/read | 診断設定のカテゴリを読み取ります。 |

### <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

Azure サービス:[Azure Sentinel](../sentinel/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.SecurityInsights/register/action | Azure Sentinel にサブスクリプションを登録します |
> | Microsoft.SecurityInsights/unregister/action | Azure Sentinel からサブスクリプションを登録解除します |
> | Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action | ユーザーの承認とライセンスを確認します |
> | Microsoft.SecurityInsights/Aggregations/read | 集計情報を取得します |
> | Microsoft.SecurityInsights/alertRules/read | アラート ルールを取得します |
> | Microsoft.SecurityInsights/alertRules/write | アラート ルールを更新します |
> | Microsoft.SecurityInsights/alertRules/delete | アラート ルールを削除します |
> | Microsoft.SecurityInsights/alertRules/actions/read | アラート ルールの応答アクションを取得します |
> | Microsoft.SecurityInsights/alertRules/actions/write | アラート ルールの応答アクションを更新します |
> | Microsoft.SecurityInsights/alertRules/actions/delete | アラート ルールの応答アクションを削除します |
> | Microsoft.SecurityInsights/automationRules/read | オートメーション ルールを取得します |
> | Microsoft.SecurityInsights/automationRules/write | オートメーション ルールを更新します |
> | Microsoft.SecurityInsights/automationRules/delete | オートメーション ルールを削除します |
> | Microsoft.SecurityInsights/Bookmarks/read | ブックマークを取得します |
> | Microsoft.SecurityInsights/Bookmarks/write | ブックマークを更新します |
> | Microsoft.SecurityInsights/Bookmarks/delete | ブックマークを削除します |
> | Microsoft.SecurityInsights/Bookmarks/expand/action | 特定の展開によってエンティティの関連エンティティを取得します |
> | Microsoft.SecurityInsights/bookmarks/relations/read | ブックマークの関係を取得します |
> | Microsoft.SecurityInsights/bookmarks/relations/write | ブックマークの関係を更新します |
> | Microsoft.SecurityInsights/bookmarks/relations/delete | ブックマークの関係を削除します |
> | Microsoft.SecurityInsights/cases/read | ケースを取得します |
> | Microsoft.SecurityInsights/cases/write | ケースを更新します |
> | Microsoft.SecurityInsights/cases/delete | ケースを削除します |
> | Microsoft.SecurityInsights/cases/comments/read | ケースコメントを取得します |
> | Microsoft.SecurityInsights/cases/comments/write | ケース コメントを作成します |
> | Microsoft.SecurityInsights/cases/investigations/read | ケースの調査を取得します |
> | Microsoft.SecurityInsights/cases/investigations/write | ケースのメタデータを更新します |
> | Microsoft.SecurityInsights/dataConnectors/read | データ コネクタを取得します |
> | Microsoft.SecurityInsights/dataConnectors/write | データ コネクタを更新します |
> | Microsoft.SecurityInsights/dataConnectors/delete | データ コネクタを削除します |
> | Microsoft.SecurityInsights/entities/read | Sentinel のエンティティ グラフを取得します |
> | Microsoft.SecurityInsights/entities/gettimeline/action | 特定の範囲のエンティティ タイムラインを取得します |
> | Microsoft.SecurityInsights/entities/getInsights/action | 特定の範囲のエンティティ分析情報を取得します |
> | Microsoft.SecurityInsights/entities/relations/read | エンティティと関連リソースの間の関係を取得します |
> | Microsoft.SecurityInsights/entities/relations/write | エンティティと関連リソースの間の関係を更新します |
> | Microsoft.SecurityInsights/entities/relations/delete | エンティティと関連リソースの間の関係を削除します |
> | Microsoft.SecurityInsights/entityQueries/read | エンティティの調査の展開を取得します |
> | Microsoft.SecurityInsights/incidents/read | インシデントが取得されます |
> | Microsoft.SecurityInsights/incidents/write | インシデントを更新します |
> | Microsoft.SecurityInsights/incidents/delete | インシデントが削除されます |
> | Microsoft.SecurityInsights/incidents/comments/read | インシデント コメントが取得されます |
> | Microsoft.SecurityInsights/incidents/comments/write | インシデントのコメントが作成されます |
> | Microsoft.SecurityInsights/incidents/comments/delete | インシデントのコメントを削除します |
> | Microsoft.SecurityInsights/incidents/relations/read | インシデントと関連リソースとの間の関係が取得されます |
> | Microsoft.SecurityInsights/incidents/relations/write | インシデントと関連リソースとの間の関係を更新します |
> | Microsoft.SecurityInsights/incidents/relations/delete | インシデントと関連リソースとの間の関係が削除されます |
> | Microsoft.SecurityInsights/officeConsents/read | Microsoft Office からの同意を取得します |
> | Microsoft.SecurityInsights/officeConsents/delete | Microsoft Office からの同意を削除します |
> | Microsoft.SecurityInsights/operations/read | 操作を取得します |
> | Microsoft.SecurityInsights/settings/read | 設定を取得します |
> | Microsoft.SecurityInsights/settings/write | 設定を更新します |
> | Microsoft.SecurityInsights/settings/delete | 設定を削除します |
> | Microsoft.SecurityInsights/threatintelligence/read | 脅威インテリジェンスを取得します |
> | Microsoft.SecurityInsights/threatintelligence/write | 脅威インテリジェンスを更新します |
> | Microsoft.SecurityInsights/threatintelligence/delete | 脅威インテリジェンスを削除します |
> | Microsoft.SecurityInsights/threatintelligence/query/action | 脅威インテリジェンスのクエリを実行します |
> | Microsoft.SecurityInsights/threatintelligence/metrics/action | 脅威インテリジェンス メトリックを収集します |
> | Microsoft.SecurityInsights/threatintelligence/bulkDelete/action | 脅威インテリジェンスを一括削除します |
> | Microsoft.SecurityInsights/threatintelligence/bulkTag/action | 脅威インテリジェンスに一括でタグを付けます |
> | Microsoft.SecurityInsights/threatintelligence/createIndicator/action | 脅威インテリジェンス インジケーターを作成します |
> | Microsoft.SecurityInsights/threatintelligence/queryIndicators/action | 脅威インテリジェンス インジケーターをクエリします |
> | Microsoft.SecurityInsights/threatintelligence/indicators/write | 脅威インテリジェンス インジケーターを更新します |
> | Microsoft.SecurityInsights/threatintelligence/indicators/delete | 脅威インテリジェンス インジケーターを削除します |
> | Microsoft.SecurityInsights/threatintelligence/indicators/query/action | 脅威インテリジェンス インジケーターをクエリします |
> | Microsoft.SecurityInsights/threatintelligence/indicators/metrics/action | 脅威インテリジェンス インジケーターのメトリックを取得します |
> | Microsoft.SecurityInsights/threatintelligence/indicators/bulkDelete/action | 脅威インテリジェンス インジケーターを一括で削除します |
> | Microsoft.SecurityInsights/threatintelligence/indicators/bulkTag/action | 脅威インテリジェンスに一括でタグを付けます |
> | Microsoft.SecurityInsights/threatintelligence/indicators/read | 脅威インテリジェンス インジケーターを取得します |
> | Microsoft.SecurityInsights/threatintelligence/indicators/appendTags/action | 脅威インテリジェンス インジケーターにタグを追加します |
> | Microsoft.SecurityInsights/threatintelligence/indicators/replaceTags/action | 脅威インテリジェンス インジケーターのタグを置換します |
> | Microsoft.SecurityInsights/threatintelligence/metrics/read | 脅威インテリジェンス メトリックを収集します |
> | Microsoft.SecurityInsights/Watchlists/read | ウォッチリストを取得します |
> | Microsoft.SecurityInsights/Watchlists/write | ウォッチリストを作成します |
> | Microsoft.SecurityInsights/Watchlists/delete | ウォッチリストを削除します |

## <a name="devops"></a>DevOps

### <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

Azure サービス:[Azure Lab Services](../lab-services/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.DevTestLab/register/action | サブスクリプションを登録します。 |
> | Microsoft.DevTestLab/labCenters/delete | ラボ センターを削除します。 |
> | Microsoft.DevTestLab/labCenters/read | ラボ センターを読み取ります。 |
> | Microsoft.DevTestLab/labCenters/write | ラボ センターを追加または変更します。 |
> | Microsoft.DevTestLab/labs/delete | ラボを削除します。 |
> | Microsoft.DevTestLab/labs/read | ラボを読み取ります。 |
> | Microsoft.DevTestLab/labs/write | ラボを追加または変更します。 |
> | Microsoft.DevTestLab/labs/ListVhds/action | カスタム イメージの作成に使用できるディスク イメージを一覧表示します。 |
> | Microsoft.DevTestLab/labs/GenerateUploadUri/action | カスタム ディスク イメージをラボにアップロードするための URI を生成します。 |
> | Microsoft.DevTestLab/labs/CreateEnvironment/action | ラボで仮想マシンを作成します。 |
> | Microsoft.DevTestLab/labs/ClaimAnyVm/action | ラボ内のランダムに要求可能な仮想マシンを要求します。 |
> | Microsoft.DevTestLab/labs/ExportResourceUsage/action | ラボのリソース使用量をストレージ アカウントにエクスポートします。 |
> | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | 別のラボに仮想マシンをインポートします。 |
> | Microsoft.DevTestLab/labs/EnsureCurrentUserProfile/action | 現在のユーザーがラボで有効なプロファイルを保有していることを確認します。 |
> | Microsoft.DevTestLab/labs/artifactSources/delete | アーティファクト ソースを削除します。 |
> | Microsoft.DevTestLab/labs/artifactSources/read | アーティファクト ソースを読み取ります。 |
> | Microsoft.DevTestLab/labs/artifactSources/write | アーティファクト ソースを追加または変更します。 |
> | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Azure Resource Manager テンプレートを読み取ります。 |
> | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | アーティファクトを読み取ります。 |
> | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | 特定のアーティファクトの Azure Resource Manager テンプレートを生成し、必要なファイルをストレージ アカウントにアップロードして、生成された成果物を検証します。 |
> | Microsoft.DevTestLab/labs/costs/read | コストを読み取ります。 |
> | Microsoft.DevTestLab/labs/costs/write | コストを追加または変更します。 |
> | Microsoft.DevTestLab/labs/customImages/delete | カスタム イメージを削除します。 |
> | Microsoft.DevTestLab/labs/customImages/read | カスタム イメージを読み取ります。 |
> | Microsoft.DevTestLab/labs/customImages/write | カスタム イメージを追加または変更します。 |
> | Microsoft.DevTestLab/labs/formulas/delete | 数式を削除します。 |
> | Microsoft.DevTestLab/labs/formulas/read | 数式を読み取ります。 |
> | Microsoft.DevTestLab/labs/formulas/write | 数式を追加または変更します。 |
> | Microsoft.DevTestLab/labs/galleryImages/read | ギャラリー イメージを読み取ります。 |
> | Microsoft.DevTestLab/labs/notificationChannels/delete | 通知チャネルを削除します。 |
> | Microsoft.DevTestLab/labs/notificationChannels/read | 通知チャネルを読み取ります。 |
> | Microsoft.DevTestLab/labs/notificationChannels/write | 通知チャネルを追加または変更します。 |
> | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | 指定されたチャンネルに通知を送信します。 |
> | Microsoft.DevTestLab/labs/policySets/read | ポリシー セットを読み取ります。 |
> | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | ラボ ポリシーを評価します。 |
> | Microsoft.DevTestLab/labs/policySets/policies/delete | ポリシーを削除します。 |
> | Microsoft.DevTestLab/labs/policySets/policies/read | ポリシーを読み取ります。 |
> | Microsoft.DevTestLab/labs/policySets/policies/write | ポリシーを追加または変更します。 |
> | Microsoft.DevTestLab/labs/schedules/delete | スケジュールを削除します。 |
> | Microsoft.DevTestLab/labs/schedules/read | スケジュールを読み取ります。 |
> | Microsoft.DevTestLab/labs/schedules/write | スケジュールを追加または変更します。 |
> | Microsoft.DevTestLab/labs/schedules/Execute/action | スケジュールを実行します。 |
> | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | 適用可能なすべてのスケジュールを一覧表示します。 |
> | Microsoft.DevTestLab/labs/serviceRunners/delete | サービス ランナーを削除します。 |
> | Microsoft.DevTestLab/labs/serviceRunners/read | サービス ランナーを読み取ります。 |
> | Microsoft.DevTestLab/labs/serviceRunners/write | サービス ランナーを追加または変更します。 |
> | Microsoft.DevTestLab/labs/sharedGalleries/delete | 共有ギャラリーを削除します。 |
> | Microsoft.DevTestLab/labs/sharedGalleries/read | 共有ギャラリーを読み取ります。 |
> | Microsoft.DevTestLab/labs/sharedGalleries/write | 共有ギャラリーを追加または変更します。 |
> | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/delete | 共有イメージを削除します。 |
> | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/read | 共有イメージを読み取ります。 |
> | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/write | 共有イメージを追加または変更します。 |
> | Microsoft.DevTestLab/labs/users/delete | ユーザー プロファイルを削除します。 |
> | Microsoft.DevTestLab/labs/users/read | ユーザー プロファイルを読み取ります。 |
> | Microsoft.DevTestLab/labs/users/write | ユーザー プロファイルを追加または変更します。 |
> | Microsoft.DevTestLab/labs/users/disks/delete | ディスクを削除します。 |
> | Microsoft.DevTestLab/labs/users/disks/read | ディスクを読み取ります。 |
> | Microsoft.DevTestLab/labs/users/disks/write | ディスクを追加または変更します。 |
> | Microsoft.DevTestLab/labs/users/disks/Attach/action | 仮想マシンにディスクを接続し、ディスクのリースを作成します。 |
> | Microsoft.DevTestLab/labs/users/disks/Detach/action | 仮想マシンに接続されているディスクを切断し、ディスクのリースを解除します。 |
> | Microsoft.DevTestLab/labs/users/environments/delete | 環境を削除します。 |
> | Microsoft.DevTestLab/labs/users/environments/read | 環境を読み取ります。 |
> | Microsoft.DevTestLab/labs/users/environments/write | 環境を追加または変更します。 |
> | Microsoft.DevTestLab/labs/users/secrets/delete | シークレットを削除します。 |
> | Microsoft.DevTestLab/labs/users/secrets/read | シークレットを読み取ります。 |
> | Microsoft.DevTestLab/labs/users/secrets/write | シークレットを追加または変更します。 |
> | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | サービス ファブリックを削除します。 |
> | Microsoft.DevTestLab/labs/users/serviceFabrics/read | サービス ファブリックを読み取ります。 |
> | Microsoft.DevTestLab/labs/users/serviceFabrics/write | サービス ファブリックを追加または変更します。 |
> | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | サービス ファブリックを開始します。 |
> | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | サービス ファブリックを停止します。 |
> | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | 存在する場合は、該当する開始/停止のスケジュールを一覧表示します。 |
> | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | スケジュールを削除します。 |
> | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | スケジュールを読み取ります。 |
> | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | スケジュールを追加または変更します。 |
> | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | スケジュールを実行します。 |
> | Microsoft.DevTestLab/labs/virtualMachines/delete | 仮想マシンを削除します。 |
> | Microsoft.DevTestLab/labs/virtualMachines/read | 仮想マシンを読み取ります。 |
> | Microsoft.DevTestLab/labs/virtualMachines/write | 仮想マシンを追加または変更します。 |
> | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | 新規または既存のデータ ディスクを仮想マシンに接続します。 |
> | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | 仮想マシンにアーティファクトを適用します。 |
> | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | 既存の仮想マシンの所有権を取得します。 |
> | Microsoft.DevTestLab/labs/virtualMachines/ClearArtifactResults/action | 仮想マシンの成果物の結果をクリアします。 |
> | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | 指定されたディスクを仮想マシンから切断します。 |
> | Microsoft.DevTestLab/labs/virtualMachines/GetRdpFileContents/action | 仮想マシンの RDP ファイルの内容を表す文字列を取得します。 |
> | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | 存在する場合は、該当する開始/停止のスケジュールを一覧表示します。 |
> | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | 仮想マシンを再配置します。 |
> | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | 仮想マシンのサイズを変更します。 |
> | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | 仮想マシンを再起動します。 |
> | Microsoft.DevTestLab/labs/virtualMachines/Start/action | 仮想マシンを起動します。 |
> | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | 仮想マシンの停止 |
> | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | 現在のユーザーが所有する仮想マシンに接続されているすべてのデータ ディスクを転送します。 |
> | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | 既存の仮想マシンの所有権を放棄します。 |
> | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | スケジュールを削除します。 |
> | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | スケジュールを読み取ります。 |
> | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | スケジュールを追加または変更します。 |
> | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | スケジュールを実行します。 |
> | Microsoft.DevTestLab/labs/virtualNetworks/delete | 仮想ネットワークを削除します。 |
> | Microsoft.DevTestLab/labs/virtualNetworks/read | 仮想ネットワークを読み取ります。 |
> | Microsoft.DevTestLab/labs/virtualNetworks/write | 仮想ネットワークを追加または変更します。 |
> | Microsoft.DevTestLab/labs/virtualNetworks/bastionHosts/delete | bastionhosts を削除します。 |
> | Microsoft.DevTestLab/labs/virtualNetworks/bastionHosts/read | bastionhosts を読み取ります。 |
> | Microsoft.DevTestLab/labs/virtualNetworks/bastionHosts/write | bastionhosts を追加または変更します。 |
> | Microsoft.DevTestLab/labs/vmPools/delete | 仮想マシン プールを削除します。 |
> | Microsoft.DevTestLab/labs/vmPools/read | 仮想マシン プールを読み取ります。 |
> | Microsoft.DevTestLab/labs/vmPools/write | 仮想マシン プールを追加または変更します。 |
> | Microsoft.DevTestLab/locations/operations/read | 操作を読み取ります。 |
> | Microsoft.DevTestLab/schedules/delete | スケジュールを削除します。 |
> | Microsoft.DevTestLab/schedules/read | スケジュールを読み取ります。 |
> | Microsoft.DevTestLab/schedules/write | スケジュールを追加または変更します。 |
> | Microsoft.DevTestLab/schedules/Execute/action | スケジュールを実行します。 |
> | Microsoft.DevTestLab/schedules/Retarget/action | スケジュールのターゲット リソース ID を更新します。 |

### <a name="microsoftlabservices"></a>Microsoft.LabServices

Azure サービス:[Azure Lab Services](../lab-services/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.LabServices/register/action | サブスクリプションを登録します。 |
> | Microsoft.LabServices/labAccounts/delete | ラボ アカウントを削除します。 |
> | Microsoft.LabServices/labAccounts/read | ラボ アカウントを読み取ります。 |
> | Microsoft.LabServices/labAccounts/write | ラボ アカウントを追加または変更します。 |
> | Microsoft.LabServices/labAccounts/CreateLab/action | ラボ アカウントにラボを作成します。 |
> | Microsoft.LabServices/labAccounts/GetRegionalAvailability/action | ラボ アカウントの下で構成されたサイズ カテゴリ別のリージョン別の提供状況を取得します。 |
> | Microsoft.LabServices/labAccounts/GetPricingAndAvailability/action | ラボ アカウントのサイズ、地域、およびオペレーティング システムの組み合わせの価格と可用性を取得します。 |
> | Microsoft.LabServices/labAccounts/GetRestrictionsAndUsage/action | このサブスクリプションのコアに関する制限と使用状況が取得されます |
> | Microsoft.LabServices/labAccounts/galleryImages/delete | ギャラリー イメージを削除します。 |
> | Microsoft.LabServices/labAccounts/galleryImages/read | ギャラリー イメージを読み取ります。 |
> | Microsoft.LabServices/labAccounts/galleryImages/write | ギャラリー イメージを追加または変更します。 |
> | Microsoft.LabServices/labAccounts/labs/delete | ラボを削除します。 |
> | Microsoft.LabServices/labAccounts/labs/read | ラボを読み取ります。 |
> | Microsoft.LabServices/labAccounts/labs/write | ラボを追加または変更します。 |
> | Microsoft.LabServices/labAccounts/labs/AddUsers/action | ラボへのユーザーの追加 |
> | Microsoft.LabServices/labAccounts/labs/SendEmail/action | ラボの登録リンクを含む電子メールを送信します |
> | Microsoft.LabServices/labAccounts/labs/GetLabPricingAndAvailability/action | このラボのラボ ユニットごとの価格と、このラボをスケールアップできるかどうかを示す可用性を取得します。 |
> | Microsoft.LabServices/labAccounts/labs/SyncUserList/action | AAD グループからユーザー リストに変更を同期します |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | 環境の設定を削除します。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | 環境の設定を読み取ります。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | 環境の設定を追加または変更します。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | ラボ/環境設定の現在の状態に基づいて環境設定に必要なリソースをプロビジョニング/プロビジョニング解除します。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | テンプレート内のすべてのリソースを開始してテンプレートを開始します。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | テンプレート内のすべてのリソースを停止してテンプレートを停止します。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/SaveImage/action | 現在のテンプレート イメージをラボ アカウントで共有ギャラリーに保存します |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/ResetPassword/action | テンプレート仮想マシンのパスワードをリセットします。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | 環境を削除します。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | 環境を読み取ります。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | 環境内のすべてのリソースを開始して環境を開始します。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | 環境内のすべてのリソースを停止して環境を停止します。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/ResetPassword/action | 特定の環境でのユーザー パスワードをリセットします |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/delete | スケジュールを削除します。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/read | スケジュールを読み取ります。 |
> | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/write | スケジュールを追加または変更します。 |
> | Microsoft.LabServices/labAccounts/labs/users/delete | ユーザーを削除します。 |
> | Microsoft.LabServices/labAccounts/labs/users/read | ユーザーを読み取ります。 |
> | Microsoft.LabServices/labAccounts/labs/users/write | ユーザーを追加または変更します。 |
> | Microsoft.LabServices/labAccounts/sharedGalleries/delete | 共有ギャラリーを削除します。 |
> | Microsoft.LabServices/labAccounts/sharedGalleries/read | 共有ギャラリーを読み取ります。 |
> | Microsoft.LabServices/labAccounts/sharedGalleries/write | 共有ギャラリーを追加または変更します。 |
> | Microsoft.LabServices/labAccounts/sharedImages/delete | 共有イメージを削除します。 |
> | Microsoft.LabServices/labAccounts/sharedImages/read | 共有イメージを読み取ります。 |
> | Microsoft.LabServices/labAccounts/sharedImages/write | 共有イメージを追加または変更します。 |
> | Microsoft.LabServices/locations/operations/read | 操作を読み取ります。 |
> | Microsoft.LabServices/users/Register/action | マネージド ラボにユーザーを登録します。 |
> | Microsoft.LabServices/users/ListAllEnvironments/action | ユーザーのすべての環境を一覧表示します |
> | Microsoft.LabServices/users/StartEnvironment/action | 環境内のすべてのリソースを開始して環境を開始します。 |
> | Microsoft.LabServices/users/StopEnvironment/action | 環境内のすべてのリソースを停止して環境を停止します。 |
> | Microsoft.LabServices/users/ResetPassword/action | 特定の環境でのユーザー パスワードをリセットします |
> | Microsoft.LabServices/users/UserSettings/action | 個人ユーザー設定を更新して返します。 |

### <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

Azure サービス:[Azure DevOps](/azure/devops/)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.VisualStudio/Register/Action | Microsoft.VisualStudio プロバイダーに Azure サブスクリプションを登録します。 |
> | Microsoft.VisualStudio/Account/Write | アカウントを設定します。 |
> | Microsoft.VisualStudio/Account/Delete | アカウントを削除します。 |
> | Microsoft.VisualStudio/Account/Read | アカウントを読み取ります。 |
> | Microsoft.VisualStudio/Account/Extension/Read | アカウント/拡張機能を読み取ります。 |
> | Microsoft.VisualStudio/Account/Project/Read | アカウント/プロジェクトを読み取ります。 |
> | Microsoft.VisualStudio/Account/Project/Write | アカウント/プロジェクトを設定します。 |
> | Microsoft.VisualStudio/Extension/Write | 拡張機能を設定します。 |
> | Microsoft.VisualStudio/Extension/Delete | 拡張機能を削除します。 |
> | Microsoft.VisualStudio/Extension/Read | 拡張機能を読み取ります。 |
> | Microsoft.VisualStudio/Project/Write | プロジェクトを設定します。 |
> | Microsoft.VisualStudio/Project/Delete | プロジェクトを削除します。 |
> | Microsoft.VisualStudio/Project/Read | プロジェクトを読み取ります。 |

## <a name="migrate"></a>移行

### <a name="microsoftmigrate"></a>Microsoft.Migrate

Azure サービス:[Azure Migrate](../migrate/migrate-services-overview.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Migrate/register/action | Microsoft.Migrate リソース プロバイダーにサブスクリプションを登録します |
> | Microsoft.Migrate/unregister/action | Microsoft.Migrate リソース プロバイダーのサブスクリプションを登録解除します |
> | Microsoft.Migrate/assessmentprojects/read | アセスメント プロジェクトのプロパティを取得します |
> | Microsoft.Migrate/assessmentprojects/write | 新しいアセスメント プロジェクトを作成するか、既存のアセスメント プロジェクトを更新します |
> | Microsoft.Migrate/assessmentprojects/delete | アセスメント プロジェクトを削除します |
> | Microsoft.Migrate/assessmentprojects/assessmentOptions/read | 指定された場所で利用できるアセスメント オプションを取得します |
> | Microsoft.Migrate/assessmentprojects/assessments/read | プロジェクトでの評価を一覧表示します |
> | Microsoft.Migrate/assessmentprojects/assessmentsSummary/read | 指定された場所で利用できる評価の概要を取得します |
> | Microsoft.Migrate/assessmentprojects/avsAssessmentOptions/read | 指定された場所で利用できる AVS 評価オプションを取得します |
> | Microsoft.Migrate/assessmentprojects/groups/read | グループのプロパティを取得します |
> | Microsoft.Migrate/assessmentprojects/groups/write | 新しいグループを作成するか、既存のグループを更新します |
> | Microsoft.Migrate/assessmentprojects/groups/delete | グループを削除します |
> | Microsoft.Migrate/assessmentprojects/groups/updateMachines/action | マシンを追加または削除することでグループを更新します |
> | Microsoft.Migrate/assessmentprojects/groups/assessments/read | 評価のプロパティを取得します |
> | Microsoft.Migrate/assessmentprojects/groups/assessments/write | 新しいアセスメントを作成するか、既存のアセスメントを更新します |
> | Microsoft.Migrate/assessmentprojects/groups/assessments/delete | アセスメントを削除します |
> | Microsoft.Migrate/assessmentprojects/groups/assessments/downloadurl/action | 評価レポートの URL をダウンロードします |
> | Microsoft.Migrate/assessmentprojects/groups/assessments/assessedmachines/read | 評価済みのマシンのプロパティを取得します |
> | Microsoft.Migrate/assessmentprojects/groups/avsAssessments/read | AVS 評価のプロパティを取得します |
> | Microsoft.Migrate/assessmentprojects/groups/avsAssessments/write | 新しい AVS 評価を作成するか、既存の AVS 評価を更新します |
> | Microsoft.Migrate/assessmentprojects/groups/avsAssessments/delete | AVS 評価を削除します |
> | Microsoft.Migrate/assessmentprojects/groups/avsAssessments/downloadurl/action | AVS 評価レポートの URL をダウンロードします |
> | Microsoft.Migrate/assessmentprojects/groups/avsAssessments/avsassessedmachines/read | AVS 評価済みマシンのプロパティを取得します |
> | Microsoft.Migrate/assessmentprojects/hypervcollectors/read | Hyper-V コレクターのプロパティを取得します |
> | Microsoft.Migrate/assessmentprojects/hypervcollectors/write | 新しい HyperV コレクターを作成するか、既存の HyperV コレクターを更新します |
> | Microsoft.Migrate/assessmentprojects/hypervcollectors/delete | HyperV コレクターを削除します |
> | Microsoft.Migrate/assessmentprojects/importcollectors/read | インポート コレクターのプロパティを取得します |
> | Microsoft.Migrate/assessmentprojects/importcollectors/write | 新しいインポート コレクターを作成するか、既存のインポート コレクターを更新します |
> | Microsoft.Migrate/assessmentprojects/importcollectors/delete | インポート コレクターを削除します。 |
> | Microsoft.Migrate/assessmentprojects/machines/read | マシンのプロパティを取得します |
> | Microsoft.Migrate/assessmentprojects/privateEndpointConnectionProxies/read | プライベート エンドポイント接続プロキシを取得します |
> | Microsoft.Migrate/assessmentprojects/privateEndpointConnectionProxies/validate/action | プライベート エンドポイント接続プロキシを検証します |
> | Microsoft.Migrate/assessmentprojects/privateEndpointConnectionProxies/write | プライベート エンドポイント接続プロキシを作成または更新します |
> | Microsoft.Migrate/assessmentprojects/privateEndpointConnectionProxies/delete | プライベート エンドポイント接続プロキシを削除します |
> | Microsoft.Migrate/assessmentprojects/privateEndpointConnections/read | プライベート エンドポイント接続を取得します |
> | Microsoft.Migrate/assessmentprojects/privateEndpointConnections/write | プライベート エンドポイント接続を更新します |
> | Microsoft.Migrate/assessmentprojects/privateEndpointConnections/delete | プライベート エンドポイント接続を削除します |
> | Microsoft.Migrate/assessmentprojects/privateLinkResources/read | Private Link リソースを取得します |
> | Microsoft.Migrate/assessmentprojects/servercollectors/read | サーバー コレクターのプロパティを取得します |
> | Microsoft.Migrate/assessmentprojects/servercollectors/write | 新しいサーバー コレクターを作成するか、既存のサーバー コレクターを更新します |
> | Microsoft.Migrate/assessmentprojects/vmwarecollectors/read | VMware コレクターのプロパティを取得します |
> | Microsoft.Migrate/assessmentprojects/vmwarecollectors/write | 新しい VMware コレクターを作成するか、既存の VMware コレクターを更新します |
> | Microsoft.Migrate/assessmentprojects/vmwarecollectors/delete | VMware コレクターを削除します |
> | Microsoft.Migrate/locations/checknameavailability/action | 指定された場所において、指定されたサブスクリプションでリソース名を使用できるかどうかを確認します |
> | Microsoft.Migrate/locations/assessmentOptions/read | 指定された場所で利用できるアセスメント オプションを取得します |
> | Microsoft.Migrate/locations/rmsOperationResults/read | サブスクリプション全体の場所に基づく操作の状態を取得します |
> | Microsoft.Migrate/migrateprojects/read | 移行プロジェクトのプロパティを取得します |
> | Microsoft.Migrate/migrateprojects/write | 新しい移行プロジェクトを作成するか、既存の移行プロジェクトを更新します |
> | Microsoft.Migrate/migrateprojects/delete | 移行プロジェクトを削除します |
> | Microsoft.Migrate/migrateprojects/registerTool/action | 移行プロジェクトにツールを登録します |
> | Microsoft.Migrate/migrateprojects/RefreshSummary/action | プロジェクトの移行の概要を更新します |
> | Microsoft.Migrate/migrateprojects/registrationDetails/action | ツール登録の詳細を提供します |
> | Microsoft.Migrate/migrateprojects/DatabaseInstances/read | データベース インスタンスのプロパティを取得します |
> | Microsoft.Migrate/migrateprojects/Databases/read | データベースのプロパティを取得します |
> | Microsoft.Migrate/migrateprojects/machines/read | マシンのプロパティを取得します |
> | Microsoft.Migrate/migrateprojects/MigrateEvents/read | 移行イベントのプロパティを取得します。 |
> | Microsoft.Migrate/migrateprojects/MigrateEvents/Delete | 移行イベントを削除します |
> | Microsoft.Migrate/migrateprojects/privateEndpointConnectionProxies/read | プライベート エンドポイント接続プロキシを取得します |
> | Microsoft.Migrate/migrateprojects/privateEndpointConnectionProxies/validate/action | プライベート エンドポイント接続プロキシを検証します |
> | Microsoft.Migrate/migrateprojects/privateEndpointConnectionProxies/write | プライベート エンドポイント接続プロキシを作成または更新します |
> | Microsoft.Migrate/migrateprojects/privateEndpointConnectionProxies/delete | プライベート エンドポイント接続プロキシを削除します |
> | Microsoft.Migrate/migrateprojects/privateEndpointConnections/read | プライベート エンドポイント接続を取得します |
> | Microsoft.Migrate/migrateprojects/privateEndpointConnections/write | プライベート エンドポイント接続を更新します |
> | Microsoft.Migrate/migrateprojects/privateEndpointConnections/delete | プライベート エンドポイント接続を削除します |
> | Microsoft.Migrate/migrateprojects/privateLinkResources/read | Private Link リソースを取得します |
> | Microsoft.Migrate/migrateprojects/solutions/read | 移行プロジェクト ソリューションのプロパティを取得します |
> | Microsoft.Migrate/migrateprojects/solutions/write | 新しい移行プロジェクト ソリューションを作成するか、既存の移行プロジェクト ソリューションを更新します |
> | Microsoft.Migrate/migrateprojects/solutions/Delete | 移行プロジェクト ソリューションを削除します |
> | Microsoft.Migrate/migrateprojects/solutions/getconfig/action | 移行プロジェクト ソリューション構成を取得します |
> | Microsoft.Migrate/migrateprojects/solutions/cleanupData/action | 移行プロジェクト ソリューション データをクリーンアップします |
> | Microsoft.Migrate/migrateprojects/VirtualDesktopUsers/read | 仮想デスクトップ ユーザーのプロパティを取得します |
> | Microsoft.Migrate/migrateprojects/WebServers/read | Web サーバーのプロパティを取得します |
> | Microsoft.Migrate/migrateprojects/WebSites/read | Web サイトのプロパティを取得します |
> | Microsoft.Migrate/moveCollections/read | 移動コレクションを取得します |
> | Microsoft.Migrate/moveCollections/write | 移動コレクションを作成または更新します |
> | Microsoft.Migrate/moveCollections/delete | 移動コレクションを削除します |
> | Microsoft.Migrate/moveCollections/resolveDependencies/action | 移動コレクション内の移動リソースの依存関係を計算、解決、検証します |
> | Microsoft.Migrate/moveCollections/prepare/action | 要求本文に含まれるリソースのセットの準備を開始します |
> | Microsoft.Migrate/moveCollections/initiateMove/action | 要求本文に含まれるリソースのセットを移動します |
> | Microsoft.Migrate/moveCollections/discard/action | 要求本文に含まれるリソースのセットを破棄します |
> | Microsoft.Migrate/moveCollections/commit/action | 要求本文に含まれるリソースのセットをコミットします |
> | Microsoft.Migrate/moveCollections/bulkRemove/action | 要求本文に含まれる移動リソースのセットを移動コレクションから削除します |
> | Microsoft.Migrate/moveCollections/moveResources/read | 移動コレクションからすべての移動リソースまたは 1 つの移動リソースを取得します |
> | Microsoft.Migrate/moveCollections/moveResources/write | 移動リソースを作成または更新します |
> | Microsoft.Migrate/moveCollections/moveResources/delete | 移動リソースを移動コレクションから削除します |
> | Microsoft.Migrate/moveCollections/operations/read | 操作の状態を取得します |
> | Microsoft.Migrate/moveCollections/requiredFor/read | クエリ パラメーターで渡されるリソースを使用するリソースを取得します |
> | Microsoft.Migrate/moveCollections/unresolvedDependencies/read | 移動コレクション内の未解決の依存関係の一覧を取得します |
> | Microsoft.Migrate/Operations/read | Microsoft.Migrate リソース プロバイダーで使用できる操作を一覧表示します |
> | Microsoft.Migrate/projects/read | プロジェクトのプロパティを取得します |
> | Microsoft.Migrate/projects/write | 新しいプロジェクトを作成するか、既存のプロジェクトを更新します |
> | Microsoft.Migrate/projects/delete | プロジェクトを削除します |
> | Microsoft.Migrate/projects/keys/action | プロジェクトの共有キーを取得します |
> | Microsoft.Migrate/projects/assessments/read | プロジェクトでの評価を一覧表示します |
> | Microsoft.Migrate/projects/groups/read | グループのプロパティを取得します |
> | Microsoft.Migrate/projects/groups/write | 新しいグループを作成するか、既存のグループを更新します |
> | Microsoft.Migrate/projects/groups/delete | グループを削除します |
> | Microsoft.Migrate/projects/groups/assessments/read | 評価のプロパティを取得します |
> | Microsoft.Migrate/projects/groups/assessments/write | 新しいアセスメントを作成するか、既存のアセスメントを更新します |
> | Microsoft.Migrate/projects/groups/assessments/delete | アセスメントを削除します |
> | Microsoft.Migrate/projects/groups/assessments/downloadurl/action | 評価レポートの URL をダウンロードします |
> | Microsoft.Migrate/projects/groups/assessments/assessedmachines/read | 評価済みのマシンのプロパティを取得します |
> | Microsoft.Migrate/projects/machines/read | マシンのプロパティを取得します |

### <a name="microsoftoffazure"></a>Microsoft.OffAzure

Azure サービス:[Azure Migrate](../migrate/migrate-services-overview.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.OffAzure/register/action | Microsoft.OffAzure リソース プロバイダーにサブスクリプションを登録します |
> | Microsoft.OffAzure/register/action | Microsoft.OffAzure リソース プロバイダーにサブスクリプションを登録します |
> | Microsoft.OffAzure/register/action | Microsoft.OffAzure リソース プロバイダーにサブスクリプションを登録します |
> | Microsoft.OffAzure/register/action | Microsoft.OffAzure リソース プロバイダーにサブスクリプションを登録します |
> | Microsoft.OffAzure/register/action | Microsoft.OffAzure リソース プロバイダーにサブスクリプションを登録します |
> | Microsoft.OffAzure/HyperVSites/read | Hyper-V サイトのプロパティを取得します。 |
> | Microsoft.OffAzure/HyperVSites/write | Hyper-V サイトを作成または更新します。 |
> | Microsoft.OffAzure/HyperVSites/delete | Hyper-V サイトを削除します。 |
> | Microsoft.OffAzure/HyperVSites/refresh/action | Hyper-V サイト内のオブジェクトを更新します。 |
> | Microsoft.OffAzure/HyperVSites/updateProperties/action | サイト内のマシンのプロパティを更新します |
> | Microsoft.OffAzure/HyperVSites/read | Hyper-V サイトのプロパティを取得します。 |
> | Microsoft.OffAzure/HyperVSites/write | Hyper-V サイトを作成または更新します。 |
> | Microsoft.OffAzure/HyperVSites/delete | Hyper-V サイトを削除します。 |
> | Microsoft.OffAzure/HyperVSites/refresh/action | Hyper-V サイト内のオブジェクトを更新します。 |
> | Microsoft.OffAzure/HyperVSites/updateProperties/action | サイト内のマシンのプロパティを更新します |
> | Microsoft.OffAzure/HyperVSites/read | Hyper-V サイトのプロパティを取得します。 |
> | Microsoft.OffAzure/HyperVSites/write | Hyper-V サイトを作成または更新します。 |
> | Microsoft.OffAzure/HyperVSites/delete | Hyper-V サイトを削除します。 |
> | Microsoft.OffAzure/HyperVSites/refresh/action | Hyper-V サイト内のオブジェクトを更新します。 |
> | Microsoft.OffAzure/HyperVSites/updateProperties/action | サイト内のマシンのプロパティを更新します |
> | Microsoft.OffAzure/HyperVSites/read | Hyper-V サイトのプロパティを取得します。 |
> | Microsoft.OffAzure/HyperVSites/write | Hyper-V サイトを作成または更新します。 |
> | Microsoft.OffAzure/HyperVSites/delete | Hyper-V サイトを削除します。 |
> | Microsoft.OffAzure/HyperVSites/refresh/action | Hyper-V サイト内のオブジェクトを更新します。 |
> | Microsoft.OffAzure/HyperVSites/updateProperties/action | サイト内のマシンのプロパティを更新します |
> | Microsoft.OffAzure/HyperVSites/read | Hyper-V サイトのプロパティを取得します。 |
> | Microsoft.OffAzure/HyperVSites/write | Hyper-V サイトを作成または更新します。 |
> | Microsoft.OffAzure/HyperVSites/delete | Hyper-V サイトを削除します。 |
> | Microsoft.OffAzure/HyperVSites/refresh/action | Hyper-V サイト内のオブジェクトを更新します。 |
> | Microsoft.OffAzure/HyperVSites/updateProperties/action | サイト内のマシンのプロパティを更新します |
> | Microsoft.OffAzure/HyperVSites/clusters/read | Hyper-V クラスターのプロパティを取得します。 |
> | Microsoft.OffAzure/HyperVSites/clusters/write | Hyper-V クラスターを作成または更新します。 |
> | Microsoft.OffAzure/HyperVSites/clusters/read | Hyper-V クラスターのプロパティを取得します。 |
> | Microsoft.OffAzure/HyperVSites/clusters/write | Hyper-V クラスターを作成または更新します。 |
> | Microsoft.OffAzure/HyperVSites/clusters/read | Hyper-V クラスターのプロパティを取得します。 |
> | Microsoft.OffAzure/HyperVSites/clusters/write | Hyper-V クラスターを作成または更新します。 |
> | Microsoft.OffAzure/HyperVSites/clusters/read | Hyper-V クラスターのプロパティを取得します。 |
> | Microsoft.OffAzure/HyperVSites/clusters/write | Hyper-V クラスターを作成または更新します。 |
> | Microsoft.OffAzure/HyperVSites/clusters/read | Hyper-V クラスターのプロパティを取得します。 |
> | Microsoft.OffAzure/HyperVSites/clusters/write | Hyper-V クラスターを作成または更新します。 |
> | Microsoft.OffAzure/HyperVSites/healthsummary/read | Hyper-V リソースの正常性の概要を取得します |
> | Microsoft.OffAzure/HyperVSites/healthsummary/read | Hyper-V リソースの正常性の概要を取得します |
> | Microsoft.OffAzure/HyperVSites/healthsummary/read | Hyper-V リソースの正常性の概要を取得します |
> | Microsoft.OffAzure/HyperVSites/healthsummary/read | Hyper-V リソースの正常性の概要を取得します |
> | Microsoft.OffAzure/HyperVSites/healthsummary/read | Hyper-V リソースの正常性の概要を取得します |
> | Microsoft.OffAzure/HyperVSites/hosts/read | Hyper-V ホストのプロパティを取得します。 |
> | Microsoft.OffAzure/HyperVSites/hosts/write | Hyper-V ホストを作成または更新します。 |
> | Microsoft.OffAzure/HyperVSites/hosts/read | Hyper-V ホストのプロパティを取得します。 |
> | Microsoft.OffAzure/HyperVSites/hosts/write | Hyper-V ホストを作成または更新します。 |
> | Microsoft.OffAzure/HyperVSites/hosts/read | Hyper-V ホストのプロパティを取得します。 |
> | Microsoft.OffAzure/HyperVSites/hosts/write | Hyper-V ホストを作成または更新します。 |
> | Microsoft.OffAzure/HyperVSites/hosts/read | Hyper-V ホストのプロパティを取得します。 |
> | Microsoft.OffAzure/HyperVSites/hosts/write | Hyper-V ホストを作成または更新します。 |
> | Microsoft.OffAzure/HyperVSites/hosts/read | Hyper-V ホストのプロパティを取得します。 |
> | Microsoft.OffAzure/HyperVSites/hosts/write | Hyper-V ホストを作成または更新します。 |
> | Microsoft.OffAzure/HyperVSites/jobs/read | Hyper-V ジョブのプロパティを取得します。 |
> | Microsoft.OffAzure/HyperVSites/jobs/read | Hyper-V ジョブのプロパティを取得します。 |
> | Microsoft.OffAzure/HyperVSites/jobs/read | Hyper-V ジョブのプロパティを取得します。 |
> | Microsoft.OffAzure/HyperVSites/jobs/read | Hyper-V ジョブのプロパティを取得します。 |
> | Microsoft.OffAzure/HyperVSites/jobs/read | Hyper-V ジョブのプロパティを取得します。 |
> | Microsoft.OffAzure/HyperVSites/machines/read | Hyper-V マシンのプロパティを取得します。 |
> | Microsoft.OffAzure/HyperVSites/machines/read | Hyper-V マシンのプロパティを取得します。 |
> | Microsoft.OffAzure/HyperVSites/machines/read | Hyper-V マシンのプロパティを取得します。 |
> | Microsoft.OffAzure/HyperVSites/machines/read | Hyper-V マシンのプロパティを取得します。 |
> | Microsoft.OffAzure/HyperVSites/machines/read | Hyper-V マシンのプロパティを取得します。 |
> | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Hyper-V 操作の状態のプロパティを取得します。 |
> | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Hyper-V 操作の状態のプロパティを取得します。 |
> | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Hyper-V 操作の状態のプロパティを取得します。 |
> | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Hyper-V 操作の状態のプロパティを取得します。 |
> | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Hyper-V 操作の状態のプロパティを取得します。 |
> | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Hyper-V の実行のプロパティをアカウントとして取得します。 |
> | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Hyper-V の実行のプロパティをアカウントとして取得します。 |
> | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Hyper-V の実行のプロパティをアカウントとして取得します。 |
> | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Hyper-V の実行のプロパティをアカウントとして取得します。 |
> | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Hyper-V の実行のプロパティをアカウントとして取得します。 |
> | Microsoft.OffAzure/HyperVSites/summary/read | Hyper-V サイトの概要が取得されます |
> | Microsoft.OffAzure/HyperVSites/summary/read | Hyper-V サイトの概要が取得されます |
> | Microsoft.OffAzure/HyperVSites/summary/read | Hyper-V サイトの概要が取得されます |
> | Microsoft.OffAzure/HyperVSites/summary/read | Hyper-V サイトの概要が取得されます |
> | Microsoft.OffAzure/HyperVSites/summary/read | Hyper-V サイトの概要が取得されます |
> | Microsoft.OffAzure/HyperVSites/usage/read | Hyper-V サイトの使用状況を取得します。 |
> | Microsoft.OffAzure/HyperVSites/usage/read | Hyper-V サイトの使用状況を取得します。 |
> | Microsoft.OffAzure/HyperVSites/usage/read | Hyper-V サイトの使用状況を取得します。 |
> | Microsoft.OffAzure/HyperVSites/usage/read | Hyper-V サイトの使用状況を取得します。 |
> | Microsoft.OffAzure/HyperVSites/usage/read | Hyper-V サイトの使用状況を取得します。 |
> | Microsoft.OffAzure/ImportSites/read | インポート サイトのプロパティを取得します |
> | Microsoft.OffAzure/ImportSites/write | インポート サイトを作成または更新します |
> | Microsoft.OffAzure/ImportSites/delete | インポート サイトを削除します |
> | Microsoft.OffAzure/ImportSites/importuri/action | マシンの CSV ファイルをインポートするための SAS URI を取得します。 |
> | Microsoft.OffAzure/ImportSites/exporturi/action | マシンの CSV ファイルをエクスポートするための SAS URI が取得されます。 |
> | Microsoft.OffAzure/ImportSites/read | インポート サイトのプロパティを取得します |
> | Microsoft.OffAzure/ImportSites/write | インポート サイトを作成または更新します |
> | Microsoft.OffAzure/ImportSites/delete | インポート サイトを削除します |
> | Microsoft.OffAzure/ImportSites/importuri/action | マシンの CSV ファイルをインポートするための SAS URI を取得します。 |
> | Microsoft.OffAzure/ImportSites/exporturi/action | マシンの CSV ファイルをエクスポートするための SAS URI が取得されます。 |
> | Microsoft.OffAzure/ImportSites/read | インポート サイトのプロパティを取得します |
> | Microsoft.OffAzure/ImportSites/write | インポート サイトを作成または更新します |
> | Microsoft.OffAzure/ImportSites/delete | インポート サイトを削除します |
> | Microsoft.OffAzure/ImportSites/importuri/action | マシンの CSV ファイルをインポートするための SAS URI を取得します。 |
> | Microsoft.OffAzure/ImportSites/exporturi/action | マシンの CSV ファイルをエクスポートするための SAS URI が取得されます。 |
> | Microsoft.OffAzure/ImportSites/read | インポート サイトのプロパティを取得します |
> | Microsoft.OffAzure/ImportSites/write | インポート サイトを作成または更新します |
> | Microsoft.OffAzure/ImportSites/delete | インポート サイトを削除します |
> | Microsoft.OffAzure/ImportSites/importuri/action | マシンの CSV ファイルをインポートするための SAS URI を取得します。 |
> | Microsoft.OffAzure/ImportSites/exporturi/action | マシンの CSV ファイルをエクスポートするための SAS URI が取得されます。 |
> | Microsoft.OffAzure/ImportSites/read | インポート サイトのプロパティを取得します |
> | Microsoft.OffAzure/ImportSites/write | インポート サイトを作成または更新します |
> | Microsoft.OffAzure/ImportSites/delete | インポート サイトを削除します |
> | Microsoft.OffAzure/ImportSites/importuri/action | マシンの CSV ファイルをインポートするための SAS URI を取得します。 |
> | Microsoft.OffAzure/ImportSites/exporturi/action | マシンの CSV ファイルをエクスポートするための SAS URI が取得されます。 |
> | Microsoft.OffAzure/ImportSites/jobs/read | インポート ジョブのプロパティを取得します |
> | Microsoft.OffAzure/ImportSites/jobs/read | インポート ジョブのプロパティを取得します |
> | Microsoft.OffAzure/ImportSites/jobs/read | インポート ジョブのプロパティを取得します |
> | Microsoft.OffAzure/ImportSites/jobs/read | インポート ジョブのプロパティを取得します |
> | Microsoft.OffAzure/ImportSites/jobs/read | インポート ジョブのプロパティを取得します |
> | Microsoft.OffAzure/ImportSites/machines/read | インポート マシンのプロパティを取得します |
> | Microsoft.OffAzure/ImportSites/machines/delete | インポート マシンを削除します |
> | Microsoft.OffAzure/ImportSites/machines/read | インポート マシンのプロパティを取得します |
> | Microsoft.OffAzure/ImportSites/machines/delete | インポート マシンを削除します |
> | Microsoft.OffAzure/ImportSites/machines/read | インポート マシンのプロパティを取得します |
> | Microsoft.OffAzure/ImportSites/machines/delete | インポート マシンを削除します |
> | Microsoft.OffAzure/ImportSites/machines/read | インポート マシンのプロパティを取得します |
> | Microsoft.OffAzure/ImportSites/machines/delete | インポート マシンを削除します |
> | Microsoft.OffAzure/ImportSites/machines/read | インポート マシンのプロパティを取得します |
> | Microsoft.OffAzure/ImportSites/machines/delete | インポート マシンを削除します |
> | Microsoft.OffAzure/MasterSites/operationsstatus/read | マスター サイト操作の状態のプロパティを取得します |
> | Microsoft.OffAzure/MasterSites/operationsstatus/read | マスター サイト操作の状態のプロパティを取得します |
> | Microsoft.OffAzure/MasterSites/operationsstatus/read | マスター サイト操作の状態のプロパティを取得します |
> | Microsoft.OffAzure/MasterSites/operationsstatus/read | マスター サイト操作の状態のプロパティを取得します |
> | Microsoft.OffAzure/MasterSites/operationsstatus/read | マスター サイト操作の状態のプロパティを取得します |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/read | プライベート エンドポイント接続プロキシを取得します |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/validate/action | プライベート エンドポイント接続プロキシを検証します |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/write | プライベート エンドポイント接続プロキシを作成または更新します |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/delete | プライベート エンドポイント接続プロキシを削除します |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/read | プライベート エンドポイント接続プロキシを取得します |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/validate/action | プライベート エンドポイント接続プロキシを検証します |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/write | プライベート エンドポイント接続プロキシを作成または更新します |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/delete | プライベート エンドポイント接続プロキシを削除します |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/read | プライベート エンドポイント接続プロキシを取得します |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/validate/action | プライベート エンドポイント接続プロキシを検証します |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/write | プライベート エンドポイント接続プロキシを作成または更新します |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/delete | プライベート エンドポイント接続プロキシを削除します |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/read | プライベート エンドポイント接続プロキシを取得します |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/validate/action | プライベート エンドポイント接続プロキシを検証します |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/write | プライベート エンドポイント接続プロキシを作成または更新します |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/delete | プライベート エンドポイント接続プロキシを削除します |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/read | プライベート エンドポイント接続プロキシを取得します |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/validate/action | プライベート エンドポイント接続プロキシを検証します |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/write | プライベート エンドポイント接続プロキシを作成または更新します |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnectionProxies/delete | プライベート エンドポイント接続プロキシを削除します |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnections/read | プライベート エンドポイント接続を取得します |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnections/write | プライベート エンドポイント接続を更新します |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnections/delete | プライベート エンドポイント接続を削除します |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnections/read | プライベート エンドポイント接続を取得します |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnections/write | プライベート エンドポイント接続を更新します |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnections/delete | プライベート エンドポイント接続を削除します |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnections/read | プライベート エンドポイント接続を取得します |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnections/write | プライベート エンドポイント接続を更新します |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnections/delete | プライベート エンドポイント接続を削除します |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnections/read | プライベート エンドポイント接続を取得します |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnections/write | プライベート エンドポイント接続を更新します |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnections/delete | プライベート エンドポイント接続を削除します |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnections/read | プライベート エンドポイント接続を取得します |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnections/write | プライベート エンドポイント接続を更新します |
> | Microsoft.OffAzure/MasterSites/privateEndpointConnections/delete | プライベート エンドポイント接続を削除します |
> | Microsoft.OffAzure/MasterSites/privateLinkResources/read | Private Link リソースを取得します |
> | Microsoft.OffAzure/MasterSites/privateLinkResources/read | Private Link リソースを取得します |
> | Microsoft.OffAzure/MasterSites/privateLinkResources/read | Private Link リソースを取得します |
> | Microsoft.OffAzure/MasterSites/privateLinkResources/read | Private Link リソースを取得します |
> | Microsoft.OffAzure/MasterSites/privateLinkResources/read | Private Link リソースを取得します |
> | Microsoft.OffAzure/Operations/read | 公開された操作を読み取ります。 |
> | Microsoft.OffAzure/ServerSites/read | Server サイトのプロパティを取得します |
> | Microsoft.OffAzure/ServerSites/write | Server サイトを作成または更新します |
> | Microsoft.OffAzure/ServerSites/delete | サーバー サイトを削除します |
> | Microsoft.OffAzure/ServerSites/refresh/action | Server サイト内のオブジェクトを更新します |
> | Microsoft.OffAzure/ServerSites/updateProperties/action | サイト内のマシンのプロパティを更新します |
> | Microsoft.OffAzure/ServerSites/read | Server サイトのプロパティを取得します |
> | Microsoft.OffAzure/ServerSites/write | Server サイトを作成または更新します |
> | Microsoft.OffAzure/ServerSites/delete | サーバー サイトを削除します |
> | Microsoft.OffAzure/ServerSites/refresh/action | Server サイト内のオブジェクトを更新します |
> | Microsoft.OffAzure/ServerSites/updateProperties/action | サイト内のマシンのプロパティを更新します |
> | Microsoft.OffAzure/ServerSites/read | Server サイトのプロパティを取得します |
> | Microsoft.OffAzure/ServerSites/write | Server サイトを作成または更新します |
> | Microsoft.OffAzure/ServerSites/delete | サーバー サイトを削除します |
> | Microsoft.OffAzure/ServerSites/refresh/action | Server サイト内のオブジェクトを更新します |
> | Microsoft.OffAzure/ServerSites/updateProperties/action | サイト内のマシンのプロパティを更新します |
> | Microsoft.OffAzure/ServerSites/read | Server サイトのプロパティを取得します |
> | Microsoft.OffAzure/ServerSites/write | Server サイトを作成または更新します |
> | Microsoft.OffAzure/ServerSites/delete | サーバー サイトを削除します |
> | Microsoft.OffAzure/ServerSites/refresh/action | Server サイト内のオブジェクトを更新します |
> | Microsoft.OffAzure/ServerSites/updateProperties/action | サイト内のマシンのプロパティを更新します |
> | Microsoft.OffAzure/ServerSites/read | Server サイトのプロパティを取得します |
> | Microsoft.OffAzure/ServerSites/write | Server サイトを作成または更新します |
> | Microsoft.OffAzure/ServerSites/delete | サーバー サイトを削除します |
> | Microsoft.OffAzure/ServerSites/refresh/action | Server サイト内のオブジェクトを更新します |
> | Microsoft.OffAzure/ServerSites/updateProperties/action | サイト内のマシンのプロパティを更新します |
> | Microsoft.OffAzure/ServerSites/jobs/read | Server ジョブのプロパティを取得します |
> | Microsoft.OffAzure/ServerSites/jobs/read | Server ジョブのプロパティを取得します |
> | Microsoft.OffAzure/ServerSites/jobs/read | Server ジョブのプロパティを取得します |
> | Microsoft.OffAzure/ServerSites/jobs/read | Server ジョブのプロパティを取得します |
> | Microsoft.OffAzure/ServerSites/jobs/read | Server ジョブのプロパティを取得します |
> | Microsoft.OffAzure/ServerSites/machines/read | Server マシンのプロパティを取得します |
> | Microsoft.OffAzure/ServerSites/machines/write | Server マシンのプロパティを書き込みます |
> | Microsoft.OffAzure/ServerSites/machines/read | Server マシンのプロパティを取得します |
> | Microsoft.OffAzure/ServerSites/machines/write | Server マシンのプロパティを書き込みます |
> | Microsoft.OffAzure/ServerSites/machines/read | Server マシンのプロパティを取得します |
> | Microsoft.OffAzure/ServerSites/machines/write | Server マシンのプロパティを書き込みます |
> | Microsoft.OffAzure/ServerSites/machines/read | Server マシンのプロパティを取得します |
> | Microsoft.OffAzure/ServerSites/machines/write | Server マシンのプロパティを書き込みます |
> | Microsoft.OffAzure/ServerSites/machines/read | Server マシンのプロパティを取得します |
> | Microsoft.OffAzure/ServerSites/machines/write | Server マシンのプロパティを書き込みます |
> | Microsoft.OffAzure/ServerSites/operationsstatus/read | Server 操作の状態のプロパティを取得します |
> | Microsoft.OffAzure/ServerSites/operationsstatus/read | Server 操作の状態のプロパティを取得します |
> | Microsoft.OffAzure/ServerSites/operationsstatus/read | Server 操作の状態のプロパティを取得します |
> | Microsoft.OffAzure/ServerSites/operationsstatus/read | Server 操作の状態のプロパティを取得します |
> | Microsoft.OffAzure/ServerSites/operationsstatus/read | Server 操作の状態のプロパティを取得します |
> | Microsoft.OffAzure/ServerSites/runasaccounts/read | Server の実行のプロパティをアカウントとして取得します |
> | Microsoft.OffAzure/ServerSites/runasaccounts/read | Server の実行のプロパティをアカウントとして取得します |
> | Microsoft.OffAzure/ServerSites/runasaccounts/read | Server の実行のプロパティをアカウントとして取得します |
> | Microsoft.OffAzure/ServerSites/runasaccounts/read | Server の実行のプロパティをアカウントとして取得します |
> | Microsoft.OffAzure/ServerSites/runasaccounts/read | Server の実行のプロパティをアカウントとして取得します |
> | Microsoft.OffAzure/ServerSites/summary/read | Server サイトの概要が取得されます |
> | Microsoft.OffAzure/ServerSites/summary/read | Server サイトの概要が取得されます |
> | Microsoft.OffAzure/ServerSites/summary/read | Server サイトの概要が取得されます |
> | Microsoft.OffAzure/ServerSites/summary/read | Server サイトの概要が取得されます |
> | Microsoft.OffAzure/ServerSites/summary/read | Server サイトの概要が取得されます |
> | Microsoft.OffAzure/ServerSites/usage/read | Server サイトの使用状況を取得します |
> | Microsoft.OffAzure/ServerSites/usage/read | Server サイトの使用状況を取得します |
> | Microsoft.OffAzure/ServerSites/usage/read | Server サイトの使用状況を取得します |
> | Microsoft.OffAzure/ServerSites/usage/read | Server サイトの使用状況を取得します |
> | Microsoft.OffAzure/ServerSites/usage/read | Server サイトの使用状況を取得します |
> | Microsoft.OffAzure/VMwareSites/read | VMware サイトのプロパティを取得します。 |
> | Microsoft.OffAzure/VMwareSites/write | VMware サイトを作成または更新します。 |
> | Microsoft.OffAzure/VMwareSites/delete | VMware サイトを削除します。 |
> | Microsoft.OffAzure/VMwareSites/refresh/action | VMware サイト内のオブジェクトを更新します。 |
> | Microsoft.OffAzure/VMwareSites/exportapplications/action | VMware アプリケーションとロールのデータを xls にエクスポートします |
> | Microsoft.OffAzure/VMwareSites/updateProperties/action | サイト内のマシンのプロパティを更新します |
> | Microsoft.OffAzure/VMwareSites/generateCoarseMap/action | マシンの一覧の粗いマップを生成します |
> | Microsoft.OffAzure/VMwareSites/generateDetailedMap/action | 詳細な VMware の粗いマップを生成します |
> | Microsoft.OffAzure/VMwareSites/clientGroupMembers/action | 選択されたクライアント グループのクライアント グループ メンバーを一覧表示します。 |
> | Microsoft.OffAzure/VMwareSites/serverGroupMembers/action | 選択されたサーバー グループのサーバー グループ メンバーを一覧表示します。 |
> | Microsoft.OffAzure/VMwareSites/getApplications/action | 選択されたマシンのアプリケーション情報の一覧を取得します |
> | Microsoft.OffAzure/VMwareSites/exportDependencies/action | 選択されたマシンの依存関係情報をエクスポートします |
> | Microsoft.OffAzure/VMwareSites/read | VMware サイトのプロパティを取得します。 |
> | Microsoft.OffAzure/VMwareSites/write | VMware サイトを作成または更新します。 |
> | Microsoft.OffAzure/VMwareSites/delete | VMware サイトを削除します。 |
> | Microsoft.OffAzure/VMwareSites/refresh/action | VMware サイト内のオブジェクトを更新します。 |
> | Microsoft.OffAzure/VMwareSites/exportapplications/action | VMware アプリケーションとロールのデータを xls にエクスポートします |
> | Microsoft.OffAzure/VMwareSites/updateProperties/action | サイト内のマシンのプロパティを更新します |
> | Microsoft.OffAzure/VMwareSites/generateCoarseMap/action | マシンの一覧の粗いマップを生成します |
> | Microsoft.OffAzure/VMwareSites/generateDetailedMap/action | 詳細な VMware の粗いマップを生成します |
> | Microsoft.OffAzure/VMwareSites/clientGroupMembers/action | 選択されたクライアント グループのクライアント グループ メンバーを一覧表示します。 |
> | Microsoft.OffAzure/VMwareSites/serverGroupMembers/action | 選択されたサーバー グループのサーバー グループ メンバーを一覧表示します。 |
> | Microsoft.OffAzure/VMwareSites/getApplications/action | 選択されたマシンのアプリケーション情報の一覧を取得します |
> | Microsoft.OffAzure/VMwareSites/exportDependencies/action | 選択されたマシンの依存関係情報をエクスポートします |
> | Microsoft.OffAzure/VMwareSites/read | VMware サイトのプロパティを取得します。 |
> | Microsoft.OffAzure/VMwareSites/write | VMware サイトを作成または更新します。 |
> | Microsoft.OffAzure/VMwareSites/delete | VMware サイトを削除します。 |
> | Microsoft.OffAzure/VMwareSites/refresh/action | VMware サイト内のオブジェクトを更新します。 |
> | Microsoft.OffAzure/VMwareSites/exportapplications/action | VMware アプリケーションとロールのデータを xls にエクスポートします |
> | Microsoft.OffAzure/VMwareSites/updateProperties/action | サイト内のマシンのプロパティを更新します |
> | Microsoft.OffAzure/VMwareSites/generateCoarseMap/action | マシンの一覧の粗いマップを生成します |
> | Microsoft.OffAzure/VMwareSites/generateDetailedMap/action | 詳細な VMware の粗いマップを生成します |
> | Microsoft.OffAzure/VMwareSites/clientGroupMembers/action | 選択されたクライアント グループのクライアント グループ メンバーを一覧表示します。 |
> | Microsoft.OffAzure/VMwareSites/serverGroupMembers/action | 選択されたサーバー グループのサーバー グループ メンバーを一覧表示します。 |
> | Microsoft.OffAzure/VMwareSites/getApplications/action | 選択されたマシンのアプリケーション情報の一覧を取得します |
> | Microsoft.OffAzure/VMwareSites/exportDependencies/action | 選択されたマシンの依存関係情報をエクスポートします |
> | Microsoft.OffAzure/VMwareSites/read | VMware サイトのプロパティを取得します。 |
> | Microsoft.OffAzure/VMwareSites/write | VMware サイトを作成または更新します。 |
> | Microsoft.OffAzure/VMwareSites/delete | VMware サイトを削除します。 |
> | Microsoft.OffAzure/VMwareSites/refresh/action | VMware サイト内のオブジェクトを更新します。 |
> | Microsoft.OffAzure/VMwareSites/exportapplications/action | VMware アプリケーションとロールのデータを xls にエクスポートします |
> | Microsoft.OffAzure/VMwareSites/updateProperties/action | サイト内のマシンのプロパティを更新します |
> | Microsoft.OffAzure/VMwareSites/generateCoarseMap/action | マシンの一覧の粗いマップを生成します |
> | Microsoft.OffAzure/VMwareSites/generateDetailedMap/action | 詳細な VMware の粗いマップを生成します |
> | Microsoft.OffAzure/VMwareSites/clientGroupMembers/action | 選択されたクライアント グループのクライアント グループ メンバーを一覧表示します。 |
> | Microsoft.OffAzure/VMwareSites/serverGroupMembers/action | 選択されたサーバー グループのサーバー グループ メンバーを一覧表示します。 |
> | Microsoft.OffAzure/VMwareSites/getApplications/action | 選択されたマシンのアプリケーション情報の一覧を取得します |
> | Microsoft.OffAzure/VMwareSites/exportDependencies/action | 選択されたマシンの依存関係情報をエクスポートします |
> | Microsoft.OffAzure/VMwareSites/read | VMware サイトのプロパティを取得します。 |
> | Microsoft.OffAzure/VMwareSites/write | VMware サイトを作成または更新します。 |
> | Microsoft.OffAzure/VMwareSites/delete | VMware サイトを削除します。 |
> | Microsoft.OffAzure/VMwareSites/refresh/action | VMware サイト内のオブジェクトを更新します。 |
> | Microsoft.OffAzure/VMwareSites/exportapplications/action | VMware アプリケーションとロールのデータを xls にエクスポートします |
> | Microsoft.OffAzure/VMwareSites/updateProperties/action | サイト内のマシンのプロパティを更新します |
> | Microsoft.OffAzure/VMwareSites/generateCoarseMap/action | マシンの一覧の粗いマップを生成します |
> | Microsoft.OffAzure/VMwareSites/generateDetailedMap/action | 詳細な VMware の粗いマップを生成します |
> | Microsoft.OffAzure/VMwareSites/clientGroupMembers/action | 選択されたクライアント グループのクライアント グループ メンバーを一覧表示します。 |
> | Microsoft.OffAzure/VMwareSites/serverGroupMembers/action | 選択されたサーバー グループのサーバー グループ メンバーを一覧表示します。 |
> | Microsoft.OffAzure/VMwareSites/getApplications/action | 選択されたマシンのアプリケーション情報の一覧を取得します |
> | Microsoft.OffAzure/VMwareSites/exportDependencies/action | 選択されたマシンの依存関係情報をエクスポートします |
> | Microsoft.OffAzure/VMwareSites/healthsummary/read | VMware リソースの正常性の概要を取得します |
> | Microsoft.OffAzure/VMwareSites/healthsummary/read | VMware リソースの正常性の概要を取得します |
> | Microsoft.OffAzure/VMwareSites/healthsummary/read | VMware リソースの正常性の概要を取得します |
> | Microsoft.OffAzure/VMwareSites/healthsummary/read | VMware リソースの正常性の概要を取得します |
> | Microsoft.OffAzure/VMwareSites/healthsummary/read | VMware リソースの正常性の概要を取得します |
> | Microsoft.OffAzure/VMwareSites/hosts/read | VMware ホストのプロパティを取得します |
> | Microsoft.OffAzure/VMwareSites/hosts/read | VMware ホストのプロパティを取得します |
> | Microsoft.OffAzure/VMwareSites/hosts/read | VMware ホストのプロパティを取得します |
> | Microsoft.OffAzure/VMwareSites/hosts/read | VMware ホストのプロパティを取得します |
> | Microsoft.OffAzure/VMwareSites/hosts/read | VMware ホストのプロパティを取得します |
> | Microsoft.OffAzure/VMwareSites/jobs/read | VMware ジョブのプロパティを取得します。 |
> | Microsoft.OffAzure/VMwareSites/jobs/read | VMware ジョブのプロパティを取得します。 |
> | Microsoft.OffAzure/VMwareSites/jobs/read | VMware ジョブのプロパティを取得します。 |
> | Microsoft.OffAzure/VMwareSites/jobs/read | VMware ジョブのプロパティを取得します。 |
> | Microsoft.OffAzure/VMwareSites/jobs/read | VMware ジョブのプロパティを取得します。 |
> | Microsoft.OffAzure/VMwareSites/machines/read | VMware マシンのプロパティを取得します。 |
> | Microsoft.OffAzure/VMwareSites/machines/stop/action | VMware マシンを停止します。 |
> | Microsoft.OffAzure/VMwareSites/machines/start/action | VMware マシンを起動します。 |
> | Microsoft.OffAzure/VMwareSites/machines/read | VMware マシンのプロパティを取得します。 |
> | Microsoft.OffAzure/VMwareSites/machines/stop/action | VMware マシンを停止します。 |
> | Microsoft.OffAzure/VMwareSites/machines/start/action | VMware マシンを起動します。 |
> | Microsoft.OffAzure/VMwareSites/machines/read | VMware マシンのプロパティを取得します。 |
> | Microsoft.OffAzure/VMwareSites/machines/stop/action | VMware マシンを停止します。 |
> | Microsoft.OffAzure/VMwareSites/machines/start/action | VMware マシンを起動します。 |
> | Microsoft.OffAzure/VMwareSites/machines/read | VMware マシンのプロパティを取得します。 |
> | Microsoft.OffAzure/VMwareSites/machines/stop/action | VMware マシンを停止します。 |
> | Microsoft.OffAzure/VMwareSites/machines/start/action | VMware マシンを起動します。 |
> | Microsoft.OffAzure/VMwareSites/machines/read | VMware マシンのプロパティを取得します。 |
> | Microsoft.OffAzure/VMwareSites/machines/stop/action | VMware マシンを停止します。 |
> | Microsoft.OffAzure/VMwareSites/machines/start/action | VMware マシンを起動します。 |
> | Microsoft.OffAzure/VMwareSites/machines/applications/read | VMware マシンのアプリケーションのプロパティを取得します |
> | Microsoft.OffAzure/VMwareSites/machines/applications/read | VMware マシンのアプリケーションのプロパティを取得します |
> | Microsoft.OffAzure/VMwareSites/machines/applications/read | VMware マシンのアプリケーションのプロパティを取得します |
> | Microsoft.OffAzure/VMwareSites/machines/applications/read | VMware マシンのアプリケーションのプロパティを取得します |
> | Microsoft.OffAzure/VMwareSites/machines/applications/read | VMware マシンのアプリケーションのプロパティを取得します |
> | Microsoft.OffAzure/VMwareSites/operationsstatus/read | VMware 操作の状態のプロパティを取得します。 |
> | Microsoft.OffAzure/VMwareSites/operationsstatus/read | VMware 操作の状態のプロパティを取得します。 |
> | Microsoft.OffAzure/VMwareSites/operationsstatus/read | VMware 操作の状態のプロパティを取得します。 |
> | Microsoft.OffAzure/VMwareSites/operationsstatus/read | VMware 操作の状態のプロパティを取得します。 |
> | Microsoft.OffAzure/VMwareSites/operationsstatus/read | VMware 操作の状態のプロパティを取得します。 |
> | Microsoft.OffAzure/VMwareSites/runasaccounts/read | VMware の実行のプロパティをアカウントとして取得します。 |
> | Microsoft.OffAzure/VMwareSites/runasaccounts/read | VMware の実行のプロパティをアカウントとして取得します。 |
> | Microsoft.OffAzure/VMwareSites/runasaccounts/read | VMware の実行のプロパティをアカウントとして取得します。 |
> | Microsoft.OffAzure/VMwareSites/runasaccounts/read | VMware の実行のプロパティをアカウントとして取得します。 |
> | Microsoft.OffAzure/VMwareSites/runasaccounts/read | VMware の実行のプロパティをアカウントとして取得します。 |
> | Microsoft.OffAzure/VMwareSites/summary/read | VMware サイトの概要が取得されます |
> | Microsoft.OffAzure/VMwareSites/summary/read | VMware サイトの概要が取得されます |
> | Microsoft.OffAzure/VMwareSites/summary/read | VMware サイトの概要が取得されます |
> | Microsoft.OffAzure/VMwareSites/summary/read | VMware サイトの概要が取得されます |
> | Microsoft.OffAzure/VMwareSites/summary/read | VMware サイトの概要が取得されます |
> | Microsoft.OffAzure/VMwareSites/usage/read | VMware サイトの使用状況を取得します。 |
> | Microsoft.OffAzure/VMwareSites/usage/read | VMware サイトの使用状況を取得します。 |
> | Microsoft.OffAzure/VMwareSites/usage/read | VMware サイトの使用状況を取得します。 |
> | Microsoft.OffAzure/VMwareSites/usage/read | VMware サイトの使用状況を取得します。 |
> | Microsoft.OffAzure/VMwareSites/usage/read | VMware サイトの使用状況を取得します。 |
> | Microsoft.OffAzure/VMwareSites/vcenters/read | VMware vCenter のプロパティを取得します。 |
> | Microsoft.OffAzure/VMwareSites/vcenters/write | VMware vCenter を作成または更新します。 |
> | Microsoft.OffAzure/VMwareSites/vcenters/read | VMware vCenter のプロパティを取得します。 |
> | Microsoft.OffAzure/VMwareSites/vcenters/write | VMware vCenter を作成または更新します。 |
> | Microsoft.OffAzure/VMwareSites/vcenters/read | VMware vCenter のプロパティを取得します。 |
> | Microsoft.OffAzure/VMwareSites/vcenters/write | VMware vCenter を作成または更新します。 |
> | Microsoft.OffAzure/VMwareSites/vcenters/read | VMware vCenter のプロパティを取得します。 |
> | Microsoft.OffAzure/VMwareSites/vcenters/write | VMware vCenter を作成または更新します。 |
> | Microsoft.OffAzure/VMwareSites/vcenters/read | VMware vCenter のプロパティを取得します。 |
> | Microsoft.OffAzure/VMwareSites/vcenters/write | VMware vCenter を作成または更新します。 |

## <a name="monitor"></a>モニター

### <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

Azure サービス:[Azure Monitor](../azure-monitor/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.AlertsManagement/register/action | サブスクリプション登録アクション |
> | Microsoft.AlertsManagement/register/action | Microsoft Alerts Management にサブスクリプションを登録します。 |
> | Microsoft.AlertsManagement/actionRules/read | 入力フィルターのすべてのアクション ルールを取得します。 |
> | Microsoft.AlertsManagement/actionRules/write | 特定のサブスクリプション内のアクション ルールを作成または更新します |
> | Microsoft.AlertsManagement/actionRules/delete | 特定のサブスクリプション内のアクション ルールを削除します。 |
> | Microsoft.AlertsManagement/alerts/read | 入力フィルターのすべてのアラートを取得します。 |
> | Microsoft.AlertsManagement/alerts/changestate/action | アラートの状態を変更します。 |
> | Microsoft.AlertsManagement/alerts/diagnostics/read | アラートのすべての診断を取得します |
> | Microsoft.AlertsManagement/alerts/history/read | アラートの履歴を取得します |
> | Microsoft.AlertsManagement/alertsList/read | サブスクリプション間での入力フィルターのアラートをすべて取得します |
> | Microsoft.AlertsManagement/alertsMetaData/read | 入力パラメーターのアラート メタデータを取得します。 |
> | Microsoft.AlertsManagement/alertsSummary/read | アラートの概要を取得します。 |
> | Microsoft.AlertsManagement/alertsSummaryList/read | サブスクリプション間でのアラートの概要を取得します |
> | Microsoft.AlertsManagement/Operations/read | 提供された操作を読み取ります。 |
> | Microsoft.AlertsManagement/resourceHealthAlertRules/write | 指定したサブスクリプション内の Resource Health アラート ルールを作成または更新します |
> | Microsoft.AlertsManagement/resourceHealthAlertRules/read | 入力フィルターのすべての Resource Health アラート ルールを取得します |
> | Microsoft.AlertsManagement/resourceHealthAlertRules/delete | 指定したサブスクリプション内の Resource Health アラート ルールを削除します |
> | Microsoft.AlertsManagement/smartDetectorAlertRules/write | 指定したサブスクリプション内の Smart Detector アラート規則を作成または更新します |
> | Microsoft.AlertsManagement/smartDetectorAlertRules/read | 入力フィルターのすべての Skills Manager アラート規則を取得します。 |
> | Microsoft.AlertsManagement/smartDetectorAlertRules/delete | 指定したサブスクリプション内の Smart Detector アラート規則を削除します |
> | Microsoft.AlertsManagement/smartGroups/read | 入力フィルターのすべてのスマート グループを取得します。 |
> | Microsoft.AlertsManagement/smartGroups/changestate/action | スマート グループの状態を変更します。 |
> | Microsoft.AlertsManagement/smartGroups/history/read | スマート グループの履歴を取得します |

### <a name="microsoftinsights"></a>Microsoft.Insights

Azure サービス:[Azure Monitor](../azure-monitor/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Insights/Metrics/Action | メトリックのアクション |
> | Microsoft.Insights/Register/Action | Microsoft Insights プロバイダーを登録します。 |
> | Microsoft.Insights/Unregister/Action | Microsoft Insights プロバイダーを登録します。 |
> | Microsoft.Insights/ListMigrationDate/Action | サブスクリプション移行の日付を取得します。 |
> | Microsoft.Insights/MigrateToNewpricingModel/Action | 新しい価格モデルにサブスクリプションを移行します。 |
> | Microsoft.Insights/RollbackToLegacyPricingModel/Action | 従来の価格モデルにサブスクリプションをロールバックします。 |
> | Microsoft.Insights/ActionGroups/Write | アクション グループを作成または更新します |
> | Microsoft.Insights/ActionGroups/Delete | アクション グループを削除します |
> | Microsoft.Insights/ActionGroups/Read | アクション グループを読み取ります |
> | Microsoft.Insights/ActivityLogAlerts/Write | アクティビティ ログ アラートを作成または更新します |
> | Microsoft.Insights/ActivityLogAlerts/Delete | アクティビティ ログ アラートを削除します |
> | Microsoft.Insights/ActivityLogAlerts/Read | アクティビティ ログ アラートを読み取ります |
> | Microsoft.Insights/ActivityLogAlerts/Activated/Action | アクティビティ ログ アラートがアクティブ化されました |
> | Microsoft.Insights/AlertRules/Write | クラシック メトリック アラートを作成または更新します |
> | Microsoft.Insights/AlertRules/Delete | クラシック メトリック アラートを削除します |
> | Microsoft.Insights/AlertRules/Read | クラシック メトリック アラートを読み取ります |
> | Microsoft.Insights/AlertRules/Activated/Action | クラシック メトリック アラートがアクティブ化されました |
> | Microsoft.Insights/AlertRules/Resolved/Action | クラシック メトリック アラートが解決されました |
> | Microsoft.Insights/AlertRules/Throttled/Action | クラシック メトリック アラート ルールが調整されました |
> | Microsoft.Insights/AlertRules/Incidents/Read | クラシック メトリック アラートのインシデントを読み取ります |
> | Microsoft.Insights/AutoscaleSettings/Write | 自動スケール設定を作成または更新します |
> | Microsoft.Insights/AutoscaleSettings/Delete | 自動スケール設定を削除します |
> | Microsoft.Insights/AutoscaleSettings/Read | 自動スケール設定を読み取ります |
> | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | 自動スケールのスケールアップが開始されました |
> | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | 自動スケールのスケールダウンが開始されました |
> | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | 自動スケールのスケールアップが完了しました |
> | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | 自動スケールのスケールダウンが完了しました |
> | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Read | リソースの診断設定を読み取ります |
> | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Write | リソースの診断設定を作成または更新します |
> | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/logDefinitions/Read | ログ定義を読み取ります。 |
> | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | メトリック定義を読み取ります。 |
> | Microsoft.Insights/Baseline/Read | メトリックのベースラインを読み取ります (プレビュー)。 |
> | Microsoft.Insights/CalculateBaseline/Read | メトリック値のベースラインを計算します (プレビュー)。 |
> | Microsoft.Insights/Components/AnalyticsTables/Action | Application Insights の分析テーブル アクションです。 |
> | Microsoft.Insights/Components/ApiKeys/Action | Application Insights の API キーを生成します。 |
> | Microsoft.Insights/Components/Purge/Action | Application Insights からデータを削除します。 |
> | Microsoft.Insights/Components/DailyCapReached/Action | Application Insights コンポーネントの日次上限に到達しました |
> | Microsoft.Insights/Components/DailyCapWarningThresholdReached/Action | Application Insights コンポーネントの日次上限の警告しきい値に到達しました |
> | Microsoft.Insights/Components/Write | Application Insights コンポーネントの構成を書き込みます。 |
> | Microsoft.Insights/Components/Delete | Application Insights コンポーネントの構成を削除します。 |
> | Microsoft.Insights/Components/Read | Application Insights コンポーネントの構成を読み取ります。 |
> | Microsoft.Insights/Components/ExportConfiguration/Action | Application Insights のエクスポート設定アクションです。 |
> | Microsoft.Insights/Components/Move/Action | Application Insights コンポーネントを別のリソース グループまたはサブスクリプションに移動します。 |
> | Microsoft.Insights/Components/AnalyticsItems/Delete | Application Insights の分析項目を削除します。 |
> | Microsoft.Insights/Components/AnalyticsItems/Read | Application Insights の分析項目を読み取ります。 |
> | Microsoft.Insights/Components/AnalyticsItems/Write | Application Insights の分析項目を書き込みます。 |
> | Microsoft.Insights/Components/AnalyticsTables/Delete | Application Insights の分析テーブル スキーマを削除します。 |
> | Microsoft.Insights/Components/AnalyticsTables/Read | Application Insights の分析テーブル スキーマを読み取ります。 |
> | Microsoft.Insights/Components/AnalyticsTables/Write | Application Insights の分析テーブル スキーマを書き込みます。 |
> | Microsoft.Insights/Components/Annotations/Delete | Application Insights の注釈を削除します。 |
> | Microsoft.Insights/Components/Annotations/Read | Application Insights の注釈を読み取ります。 |
> | Microsoft.Insights/Components/Annotations/Write | Application Insights の注釈を書き込みます。 |
> | Microsoft.Insights/Components/Api/Read | Application Insights コンポーネントのデータ API を読み取ります。 |
> | Microsoft.Insights/Components/ApiKeys/Delete | Application Insights の API キーを削除します。 |
> | Microsoft.Insights/Components/ApiKeys/Read | Application Insights の API キーを読み取ります。 |
> | Microsoft.Insights/Components/BillingPlanForComponent/Read | Application Insights コンポーネントの料金プランを読み取ります。 |
> | Microsoft.Insights/Components/CurrentBillingFeatures/Read | Application Insights コンポーネントの現在の料金機能を読み取ります。 |
> | Microsoft.Insights/Components/CurrentBillingFeatures/Write | Application Insights コンポーネントの現在の料金機能を書き込みます。 |
> | Microsoft.Insights/Components/DefaultWorkItemConfig/Read | Application Insights の既定の ALM 統合構成を読み取ります。 |
> | Microsoft.Insights/Components/Events/Read | OData クエリ形式を使用して、Application Insights からログを取得します。 |
> | Microsoft.Insights/Components/ExportConfiguration/Delete | Application Insights のエクスポート設定を削除します。 |
> | Microsoft.Insights/Components/ExportConfiguration/Read | Application Insights のエクスポート設定を読み取ります。 |
> | Microsoft.Insights/Components/ExportConfiguration/Write | Application Insights のエクスポート設定を書き込みます。 |
> | Microsoft.Insights/Components/ExtendQueries/Read | Application Insights コンポーネントの拡張クエリの結果を読み取ります。 |
> | Microsoft.Insights/Components/Favorites/Delete | Application Insights のお気に入りを削除します。 |
> | Microsoft.Insights/Components/Favorites/Read | Application Insights のお気に入りを読み取ります。 |
> | Microsoft.Insights/Components/Favorites/Write | Application Insights のお気に入りを書き込みます。 |
> | Microsoft.Insights/Components/FeatureCapabilities/Read | Application Insights コンポーネントの機能を読み取ります。 |
> | Microsoft.Insights/Components/GetAvailableBillingFeatures/Read | Application Insights コンポーネントで使用可能な請求機能を読み取ります。 |
> | Microsoft.Insights/Components/GetToken/Read | Application Insights コンポーネントのトークンを読み取ります。 |
> | Microsoft.Insights/Components/MetricDefinitions/Read | Application Insights コンポーネントのメトリック定義を読み取ります。 |
> | Microsoft.Insights/Components/Metrics/Read | Application Insights コンポーネントのメトリックを読み取ります。 |
> | Microsoft.Insights/Components/MyAnalyticsItems/Delete | Application Insights の個人分析項目を削除します。 |
> | Microsoft.Insights/Components/MyAnalyticsItems/Write | Application Insights の個人分析項目を書き込みます。 |
> | Microsoft.Insights/Components/MyAnalyticsItems/Read | Application Insights の個人分析項目を読み取ります。 |
> | Microsoft.Insights/Components/MyFavorites/Read | Application Insights の個人のお気に入りを読み取ります。 |
> | Microsoft.Insights/Components/Operations/Read | Application Insights での実行時間の長い操作の状態を取得します。 |
> | Microsoft.Insights/Components/PricingPlans/Read | Application Insights コンポーネントの価格プランを読み取ります。 |
> | Microsoft.Insights/Components/PricingPlans/Write | Application Insights コンポーネントの価格プランを書き込みます。 |
> | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | Application Insights のプロアクティブな検出構成を読み取ります。 |
> | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | Application Insights のプロアクティブな検出構成を書き込みます。 |
> | Microsoft.Insights/Components/providers/Microsoft.Insights/diagnosticSettings/Read | リソースの診断設定を読み取ります |
> | Microsoft.Insights/Components/providers/Microsoft.Insights/diagnosticSettings/Write | リソースの診断設定を作成または更新します |
> | Microsoft.Insights/Components/providers/Microsoft.Insights/logDefinitions/Read | ログ定義を読み取ります。 |
> | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | メトリック定義を読み取ります。 |
> | Microsoft.Insights/Components/Query/Read | Application Insights ログに対してクエリを実行します。 |
> | Microsoft.Insights/Components/QuotaStatus/Read | Application Insights コンポーネントのクォータの状態を読み取ります。 |
> | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | Application Insights の Web サイトの場所を読み取ります。 |
> | Microsoft.Insights/Components/Webtests/Read | Web テストの構成を読み取ります。 |
> | Microsoft.Insights/Components/WorkItemConfigs/Delete | Application Insights の ALM 統合構成を削除します。 |
> | Microsoft.Insights/Components/WorkItemConfigs/Read | Application Insights の ALM 統合構成を読み取ります。 |
> | Microsoft.Insights/Components/WorkItemConfigs/Write | Application Insights の ALM 統合構成を書き込みます。 |
> | Microsoft.Insights/DataCollectionRuleAssociations/Read | データ収集ルールを使用してリソースの関連付けを読み取ります |
> | Microsoft.Insights/DataCollectionRuleAssociations/Write | データ収集ルールを使用してリソースの関連付けを作成または更新します |
> | Microsoft.Insights/DataCollectionRuleAssociations/Delete | データ収集ルールを使用してリソースの関連付けを削除します |
> | Microsoft.Insights/DataCollectionRules/Read | データ収集ルールを読み取ります |
> | Microsoft.Insights/DataCollectionRules/Write | データ収集ルールを作成または更新します |
> | Microsoft.Insights/DataCollectionRules/Delete | データ収集ルールを削除します |
> | Microsoft.Insights/DiagnosticSettings/Write | リソースの診断設定を作成または更新します |
> | Microsoft.Insights/DiagnosticSettings/Delete | リソースの診断設定を削除します |
> | Microsoft.Insights/DiagnosticSettings/Read | リソースの診断設定を読み取ります |
> | Microsoft.Insights/DiagnosticSettingsCategories/Read | 診断設定のカテゴリを読み取ります |
> | Microsoft.Insights/EventCategories/Read | 利用可能なアクティビティ ログのイベント カテゴリを読み取ります |
> | Microsoft.Insights/eventtypes/digestevents/Read | イベントの種類の管理のダイジェストを読み取ります。 |
> | Microsoft.Insights/eventtypes/values/Read | アクティビティ ログのイベントを読み取ります |
> | Microsoft.Insights/ExtendedDiagnosticSettings/Write | ネットワーク フロー ログの診断設定を作成または更新します |
> | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | ネットワーク フロー ログの診断設定を削除します |
> | Microsoft.Insights/ExtendedDiagnosticSettings/Read | ネットワーク フロー ログの診断設定を読み取ります |
> | Microsoft.Insights/generateLiveToken/Read | Live Metrics のトークンを取得 |
> | Microsoft.Insights/ListMigrationDate/Read | サブスクリプション移行の日付を取得します。 |
> | Microsoft.Insights/LogDefinitions/Read | ログ定義を読み取ります。 |
> | Microsoft.Insights/LogProfiles/Write | アクティビティ ログのログ プロファイルを作成または更新します |
> | Microsoft.Insights/LogProfiles/Delete | アクティビティ ログのログ プロファイルを削除します |
> | Microsoft.Insights/LogProfiles/Read | アクティビティ ログのログ プロファイルを読み取ります |
> | Microsoft.Insights/Logs/Read | すべてのログからデータを読み取ります。 |
> | Microsoft.Insights/Logs/AADDomainServicesAccountLogon/Read | AADDomainServicesAccountLogon テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AADDomainServicesAccountManagement/Read | AADDomainServicesAccountManagement テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AADDomainServicesDirectoryServiceAccess/Read | AADDomainServicesDirectoryServiceAccess テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AADDomainServicesLogonLogoff/Read | AADDomainServicesLogonLogoff テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AADDomainServicesPolicyChange/Read | AADDomainServicesPolicyChange テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AADDomainServicesPrivilegeUse/Read | AADDomainServicesPrivilegeUse テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AADDomainServicesSystemSecurity/Read | AADDomainServicesSystemSecurity テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AADManagedIdentitySignInLogs/Read | AADManagedIdentitySignInLogs テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AADNonInteractiveUserSignInLogs/Read | AADNonInteractiveUserSignInLogs テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AADServicePrincipalSignInLogs/Read | AADServicePrincipalSignInLogs テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/ADAssessmentRecommendation/Read | ADAssessmentRecommendation テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/AddonAzureBackupAlerts/Read | AddonAzureBackupAlerts テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AddonAzureBackupJobs/Read | AddonAzureBackupJobs テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AddonAzureBackupPolicy/Read | AddonAzureBackupPolicy テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AddonAzureBackupProtectedInstance/Read | AddonAzureBackupProtectedInstance テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AddonAzureBackupStorage/Read | AddonAzureBackupStorage テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/ADFActivityRun/Read | ADFActivityRun テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/ADFPipelineRun/Read | ADFPipelineRun テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/ADFSSISIntegrationRuntimeLogs/Read | ADFSSISIntegrationRuntimeLogs テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/ADFSSISPackageEventMessageContext/Read | ADFSSISPackageEventMessageContext テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/ADFSSISPackageEventMessages/Read | ADFSSISPackageEventMessages テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/ADFSSISPackageExecutableStatistics/Read | ADFSSISPackageExecutableStatistics テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/ADFSSISPackageExecutionComponentPhases/Read | ADFSSISPackageExecutionComponentPhases テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/ADFSSISPackageExecutionDataStatistics/Read | ADFSSISPackageExecutionDataStatistics テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/ADFTriggerRun/Read | ADFTriggerRun テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/ADReplicationResult/Read | ADReplicationResult テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/ADSecurityAssessmentRecommendation/Read | ADSecurityAssessmentRecommendation テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/ADTDigitalTwinsOperation/Read | ADTDigitalTwinsOperation テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/ADTEventRoutesOperation/Read | ADTEventRoutesOperation テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/ADTModelsOperation/Read | ADTModelsOperation テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/ADTQueryOperation/Read | ADTQueryOperation テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AegDeliveryFailureLogs/Read | AegDeliveryFailureLogs テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AegPublishFailureLogs/Read | AegPublishFailureLogs テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/Alert/Read | Alert テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/AlertHistory/Read | AlertHistory テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/AmlComputeClusterEvent/Read | AmlComputeClusterEvent テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AmlComputeClusterNodeEvent/Read | AmlComputeClusterNodeEvent テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AmlComputeCpuGpuUtilization/Read | AmlComputeCpuGpuUtilization テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AmlComputeJobEvent/Read | AmlComputeJobEvent テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AmlRunStatusChangedEvent/Read | AmlRunStatusChangedEvent テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/ApiManagementGatewayLogs/Read | ApiManagementGatewayLogs テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AppAvailabilityResults/Read | AppAvailabilityResults テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AppBrowserTimings/Read | AppBrowserTimings テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AppCenterError/Read | AppCenterError テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AppDependencies/Read | AppDependencies テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AppEvents/Read | AppEvents テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AppExceptions/Read | AppExceptions テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/ApplicationInsights/Read | ApplicationInsights テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/AppMetrics/Read | AppMetrics テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AppPageViews/Read | AppPageViews テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AppPerformanceCounters/Read | AppPerformanceCounters テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AppPlatformLogsforSpring/Read | AppPlatformLogsforSpring テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AppPlatformSystemLogs/Read | AppPlatformSystemLogs テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AppRequests/Read | AppRequests テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AppServiceAntivirusScanLogs/Read | AppServiceAntivirusScanLogs テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AppServiceAppLogs/Read | AppServiceAppLogs テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AppServiceAuditLogs/Read | AppServiceAuditLogs テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AppServiceConsoleLogs/Read | AppServiceConsoleLogs テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AppServiceEnvironmentPlatformLogs/Read | AppServiceEnvironmentPlatformLogs テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AppServiceFileAuditLogs/Read | AppServiceFileAuditLogs テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AppServiceHTTPLogs/Read | AppServiceHTTPLogs テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AppServicePlatformLogs/Read | AppServicePlatformLogs テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AppSystemEvents/Read | AppSystemEvents テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AppTraces/Read | AppTraces テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AuditLogs/Read | AuditLogs テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AutoscaleEvaluationsLog/Read | AutoscaleEvaluationsLog テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/AutoscaleScaleActionsLog/Read | AutoscaleScaleActionsLog テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/AWSCloudTrail/Read | AWSCloudTrail テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/AzureActivity/Read | AzureActivity テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/AzureAssessmentRecommendation/Read | AzureAssessmentRecommendation テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AzureDevOpsAuditing/Read | AzureDevOpsAuditing テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AzureDiagnostics/Read | AzureDiagnostics テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/AzureMetrics/Read | AzureMetrics テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/BaiClusterEvent/Read | BaiClusterEvent テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/BaiClusterNodeEvent/Read | BaiClusterNodeEvent テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/BaiJobEvent/Read | BaiJobEvent テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/BehaviorAnalytics/Read | BehaviorAnalytics テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/BlockchainApplicationLog/Read | BlockchainApplicationLog テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/BlockchainProxyLog/Read | BlockchainProxyLog テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/BoundPort/Read | BoundPort テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/CommonSecurityLog/Read | CommonSecurityLog テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/ComputerGroup/Read | ComputerGroup テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/ConfigurationChange/Read | ConfigurationChange テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/ConfigurationData/Read | ConfigurationData テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/ContainerImageInventory/Read | ContainerImageInventory テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/ContainerInventory/Read | ContainerInventory テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/ContainerLog/Read | ContainerLog テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/ContainerNodeInventory/Read | ContainerNodeInventory テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/ContainerRegistryLoginEvents/Read | ContainerRegistryLoginEvents テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/ContainerRegistryRepositoryEvents/Read | ContainerRegistryRepositoryEvents テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/ContainerServiceLog/Read | ContainerServiceLog テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/CoreAzureBackup/Read | CoreAzureBackup テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/DatabricksAccounts/Read | DatabricksAccounts テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/DatabricksClusters/Read | DatabricksClusters テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/DatabricksDBFS/Read | DatabricksDBFS テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/DatabricksInstancePools/Read | DatabricksInstancePools テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/DatabricksJobs/Read | DatabricksJobs テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/DatabricksNotebook/Read | DatabricksNotebook テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/DatabricksSecrets/Read | DatabricksSecrets テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/DatabricksSQLPermissions/Read | DatabricksSQLPermissions テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/DatabricksSSH/Read | DatabricksSSH テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/DatabricksTables/Read | DatabricksTables テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/DatabricksWorkspace/Read | DatabricksWorkspace テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/DeviceAppCrash/Read | DeviceAppCrash テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/DeviceAppLaunch/Read | DeviceAppLaunch テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/DeviceCalendar/Read | DeviceCalendar テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/DeviceCleanup/Read | DeviceCleanup テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/DeviceConnectSession/Read | DeviceConnectSession テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/DeviceEtw/Read | DeviceEtw テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/DeviceHardwareHealth/Read | DeviceHardwareHealth テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/DeviceHealth/Read | DeviceHealth テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/DeviceHeartbeat/Read | DeviceHeartbeat テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/DeviceSkypeHeartbeat/Read | DeviceSkypeHeartbeat テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/DeviceSkypeSignIn/Read | DeviceSkypeSignIn テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/DeviceSleepState/Read | DeviceSleepState テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/DHAppFailure/Read | DHAppFailure テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/DHAppReliability/Read | DHAppReliability テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/DHCPActivity/Read | DHCPActivity テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/DHDriverReliability/Read | DHDriverReliability テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/DHLogonFailures/Read | DHLogonFailures テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/DHLogonMetrics/Read | DHLogonMetrics テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/DHOSCrashData/Read | DHOSCrashData テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/DHOSReliability/Read | DHOSReliability テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/DHWipAppLearning/Read | DHWipAppLearning テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/DnsEvents/Read | DnsEvents テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/DnsInventory/Read | DnsInventory テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/Dynamics365Activity/Read | Dynamics365Activity テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/ETWEvent/Read | ETWEvent テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/Event/Read | Event テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/ExchangeAssessmentRecommendation/Read | ExchangeAssessmentRecommendation テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/ExchangeOnlineAssessmentRecommendation/Read | ExchangeOnlineAssessmentRecommendation テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/FailedIngestion/Read | FailedIngestion テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/FunctionAppLogs/Read | FunctionAppLogs テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/Heartbeat/Read | Heartbeat テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/HuntingBookmark/Read | HuntingBookmark テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/IISAssessmentRecommendation/Read | IISAssessmentRecommendation テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/InboundConnection/Read | InboundConnection テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/InsightsMetrics/Read | InsightsMetrics テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/IntuneAuditLogs/Read | IntuneAuditLogs テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/IntuneDeviceComplianceOrg/Read | IntuneDeviceComplianceOrg テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/IntuneOperationalLogs/Read | IntuneOperationalLogs テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/IoTHubDistributedTracing/Read | IoTHubDistributedTracing テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/KubeEvents/Read | KubeEvents テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/KubeHealth/Read | KubeHealth テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/KubeMonAgentEvents/Read | KubeMonAgentEvents テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/KubeNodeInventory/Read | KubeNodeInventory テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/KubePodInventory/Read | KubePodInventory テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/KubeServices/Read | KubeServices テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/LinuxAuditLog/Read | LinuxAuditLog テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MAApplication/Read | MAApplication テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MAApplicationHealth/Read | MAApplicationHealth テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MAApplicationHealthAlternativeVersions/Read | MAApplicationHealthAlternativeVersions テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MAApplicationHealthIssues/Read | MAApplicationHealthIssues テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MAApplicationInstance/Read | MAApplicationInstance テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MAApplicationInstanceReadiness/Read | MAApplicationInstanceReadiness テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MAApplicationReadiness/Read | MAApplicationReadiness テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MADeploymentPlan/Read | MADeploymentPlan テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MADevice/Read | MADevice テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MADeviceNotEnrolled/Read | MADeviceNotEnrolled テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/MADeviceNRT/Read | MADeviceNRT テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/MADevicePnPHealth/Read | MADevicePnPHealth テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MADevicePnPHealthAlternativeVersions/Read | MADevicePnPHealthAlternativeVersions テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MADevicePnPHealthIssues/Read | MADevicePnPHealthIssues テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MADeviceReadiness/Read | MADeviceReadiness テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MADriverInstanceReadiness/Read | MADriverInstanceReadiness テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MADriverReadiness/Read | MADriverReadiness テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MAOfficeAddin/Read | MAOfficeAddin テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MAOfficeAddinEntityHealth/Read | MAOfficeAddinEntityHealth テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/MAOfficeAddinHealth/Read | MAOfficeAddinHealth テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MAOfficeAddinHealthEventNRT/Read | MAOfficeAddinHealthEventNRT テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/MAOfficeAddinHealthIssues/Read | MAOfficeAddinHealthIssues テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MAOfficeAddinInstance/Read | MAOfficeAddinInstance テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MAOfficeAddinInstanceReadiness/Read | MAOfficeAddinInstanceReadiness テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MAOfficeAddinReadiness/Read | MAOfficeAddinReadiness テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MAOfficeApp/Read | MAOfficeApp テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MAOfficeAppCrashesNRT/Read | MAOfficeAppCrashesNRT テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/MAOfficeAppHealth/Read | MAOfficeAppHealth テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MAOfficeAppInstance/Read | MAOfficeAppInstance テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MAOfficeAppInstanceHealth/Read | MAOfficeAppInstanceHealth テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/MAOfficeAppReadiness/Read | MAOfficeAppReadiness テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MAOfficeAppSessionsNRT/Read | MAOfficeAppSessionsNRT テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/MAOfficeBuildInfo/Read | MAOfficeBuildInfo テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MAOfficeCurrencyAssessment/Read | MAOfficeCurrencyAssessment テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MAOfficeCurrencyAssessmentDailyCounts/Read | MAOfficeCurrencyAssessmentDailyCounts テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MAOfficeDeploymentStatus/Read | MAOfficeDeploymentStatus テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MAOfficeDeploymentStatusNRT/Read | MAOfficeDeploymentStatusNRT テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/MAOfficeMacroErrorNRT/Read | MAOfficeMacroErrorNRT テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/MAOfficeMacroGlobalHealth/Read | MAOfficeMacroGlobalHealth テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/MAOfficeMacroHealth/Read | MAOfficeMacroHealth テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MAOfficeMacroHealthIssues/Read | MAOfficeMacroHealthIssues テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MAOfficeMacroIssueInstanceReadiness/Read | MAOfficeMacroIssueInstanceReadiness テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MAOfficeMacroIssueReadiness/Read | MAOfficeMacroIssueReadiness テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MAOfficeMacroSummary/Read | MAOfficeMacroSummary テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MAOfficeSuite/Read | MAOfficeSuite テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MAOfficeSuiteInstance/Read | MAOfficeSuiteInstance テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MAProposedPilotDevices/Read | MAProposedPilotDevices テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MAWindowsBuildInfo/Read | MAWindowsBuildInfo テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MAWindowsCurrencyAssessment/Read | MAWindowsCurrencyAssessment テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MAWindowsCurrencyAssessmentDailyCounts/Read | MAWindowsCurrencyAssessmentDailyCounts テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MAWindowsDeploymentStatus/Read | MAWindowsDeploymentStatus テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/MAWindowsDeploymentStatusNRT/Read | MAWindowsDeploymentStatusNRT テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/MAWindowsSysReqInstanceReadiness/Read | MAWindowsSysReqInstanceReadiness テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/McasShadowItReporting/Read | McasShadowItReporting テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/MicrosoftAzureBastionAuditLogs/Read | MicrosoftAzureBastionAuditLogs テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/MicrosoftDataShareReceivedSnapshotLog/Read | MicrosoftDataShareReceivedSnapshotLog テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/MicrosoftDataShareSentSnapshotLog/Read | MicrosoftDataShareSentSnapshotLog テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/MicrosoftDataShareShareLog/Read | MicrosoftDataShareShareLog テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/MicrosoftDynamicsTelemetryPerformanceLogs/Read | MicrosoftDynamicsTelemetryPerformanceLogs テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/MicrosoftDynamicsTelemetrySystemMetricsLogs/Read | MicrosoftDynamicsTelemetrySystemMetricsLogs テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/MicrosoftHealthcareApisAuditLogs/Read | MicrosoftHealthcareApisAuditLogs テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/NetworkMonitoring/Read | NetworkMonitoring テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/OfficeActivity/Read | OfficeActivity テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/Operation/Read | Operation テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/OutboundConnection/Read | OutboundConnection テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/Perf/Read | Perf テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/ProtectionStatus/Read | ProtectionStatus テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/ReservedAzureCommonFields/Read | ReservedAzureCommonFields テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/ReservedCommonFields/Read | ReservedCommonFields テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/SCCMAssessmentRecommendation/Read | SCCMAssessmentRecommendation テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/SCOMAssessmentRecommendation/Read | SCOMAssessmentRecommendation テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/SecurityAlert/Read | SecurityAlert テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/SecurityBaseline/Read | SecurityBaseline テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/SecurityBaselineSummary/Read | SecurityBaselineSummary テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/SecurityDetection/Read | SecurityDetection テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/SecurityEvent/Read | SecurityEvent テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/SecurityIncident/Read | SecurityIncident テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/SecurityIoTRawEvent/Read | SecurityIoTRawEvent テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/SecurityNestedRecommendation/Read | SecurityNestedRecommendation テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/SecurityRecommendation/Read | SecurityRecommendation テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/ServiceFabricOperationalEvent/Read | ServiceFabricOperationalEvent テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/ServiceFabricReliableActorEvent/Read | ServiceFabricReliableActorEvent テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/ServiceFabricReliableServiceEvent/Read | ServiceFabricReliableServiceEvent テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/SfBAssessmentRecommendation/Read | SfBAssessmentRecommendation テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/SfBOnlineAssessmentRecommendation/Read | SfBOnlineAssessmentRecommendation テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/SharePointOnlineAssessmentRecommendation/Read | SharePointOnlineAssessmentRecommendation テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/SignalRServiceDiagnosticLogs/Read | SignalRServiceDiagnosticLogs テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/SigninLogs/Read | SigninLogs テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/SPAssessmentRecommendation/Read | SPAssessmentRecommendation テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/SQLAssessmentRecommendation/Read | SQLAssessmentRecommendation テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/SqlDataClassification/Read | SqlDataClassification テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/SQLQueryPerformance/Read | SQLQueryPerformance テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/SqlVulnerabilityAssessmentResult/Read | SqlVulnerabilityAssessmentResult テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/StorageBlobLogs/Read | StorageBlobLogs テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/StorageFileLogs/Read | StorageFileLogs テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/StorageQueueLogs/Read | StorageQueueLogs テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/StorageTableLogs/Read | StorageTableLogs テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/SucceededIngestion/Read | SucceededIngestion テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/Syslog/Read | Syslog テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/SysmonEvent/Read | SysmonEvent テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/Tables.Custom/Read | カスタム ログからデータを読み取っています。 |
> | Microsoft.Insights/Logs/ThreatIntelligenceIndicator/Read | ThreatIntelligenceIndicator テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/TSIIngress/Read | TSIIngress テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/UAApp/Read | UAApp テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/UAComputer/Read | UAComputer テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/UAComputerRank/Read | UAComputerRank テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/UADriver/Read | UADriver テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/UADriverProblemCodes/Read | UADriverProblemCodes テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/UAFeedback/Read | UAFeedback テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/UAHardwareSecurity/Read | UAHardwareSecurity テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/UAIESiteDiscovery/Read | UAIESiteDiscovery テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/UAOfficeAddIn/Read | UAOfficeAddIn テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/UAProposedActionPlan/Read | UAProposedActionPlan テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/UASysReqIssue/Read | UASysReqIssue テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/UAUpgradedComputer/Read | UAUpgradedComputer テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/Update/Read | Update テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/UpdateRunProgress/Read | UpdateRunProgress テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/UpdateSummary/Read | UpdateSummary テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/Usage/Read | Usage テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/UserAccessAnalytics/Read | UserAccessAnalytics テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/UserPeerAnalytics/Read | UserPeerAnalytics テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/VMBoundPort/Read | VMBoundPort テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/VMComputer/Read | VMComputer テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/VMConnection/Read | VMConnection テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/VMProcess/Read | VMProcess テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/W3CIISLog/Read | W3CIISLog テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/WaaSDeploymentStatus/Read | WaaSDeploymentStatus テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/WaaSInsiderStatus/Read | WaaSInsiderStatus テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/WaaSUpdateStatus/Read | WaaSUpdateStatus テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/WDAVStatus/Read | WDAVStatus テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/WDAVThreat/Read | WDAVThreat テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/WindowsClientAssessmentRecommendation/Read | WindowsClientAssessmentRecommendation テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/WindowsEvent/Read | WindowsEvent テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/WindowsFirewall/Read | WindowsFirewall テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/WindowsServerAssessmentRecommendation/Read | WindowsServerAssessmentRecommendation テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/WireData/Read | WireData テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/WorkloadMonitoringPerf/Read | WorkloadMonitoringPerf テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/WUDOAggregatedStatus/Read | WUDOAggregatedStatus テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/WUDOStatus/Read | WUDOStatus テーブルからデータを読み取ります。 |
> | Microsoft.Insights/Logs/WVDCheckpoints/Read | WVDCheckpoints テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/WVDConnections/Read | WVDConnections テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/WVDErrors/Read | WVDErrors テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/WVDFeeds/Read | WVDFeeds テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/WVDHostRegistrations/Read | WVDHostRegistrations テーブルからデータを読み取ります |
> | Microsoft.Insights/Logs/WVDManagement/Read | WVDManagement テーブルからデータを読み取ります |
> | Microsoft.Insights/MetricAlerts/Write | メトリック アラートを作成または更新します |
> | Microsoft.Insights/MetricAlerts/Delete | メトリック アラートを削除します |
> | Microsoft.Insights/MetricAlerts/Read | メトリック アラートを読み取ります |
> | Microsoft.Insights/MetricAlerts/Status/Read | メトリック アラートの状態を読み取ります |
> | Microsoft.Insights/MetricBaselines/Read | メトリックのベースラインを読み取ります |
> | Microsoft.Insights/MetricDefinitions/Read | メトリック定義を読み取ります。 |
> | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | メトリック定義を読み取ります。 |
> | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | メトリック定義を読み取ります。 |
> | Microsoft.Insights/Metricnamespaces/Read | メトリックの名前空間を読み取ります |
> | Microsoft.Insights/Metrics/Read | メトリックを読み取ります。 |
> | Microsoft.Insights/Metrics/Microsoft.Insights/Read | メトリックを読み取ります。 |
> | Microsoft.Insights/Metrics/providers/Metrics/Read | メトリックを読み取ります。 |
> | Microsoft.Insights/myWorkbooks/Write | プライベート ブックを作成または更新されます |
> | Microsoft.Insights/myWorkbooks/Read | プライベート ブックが読み取られます |
> | Microsoft.Insights/MyWorkbooks/Read | プライベート ブックが読み取られます |
> | Microsoft.Insights/MyWorkbooks/Write | プライベート ブックを作成または更新します |
> | Microsoft.Insights/MyWorkbooks/Delete | プライベート ブックを削除します |
> | Microsoft.Insights/Operations/Read | 操作を読み取ります。 |
> | Microsoft.Insights/PrivateLinkScopeOperationStatuses/Read | プライベート リンク スコープ指定された操作状態を読み取ります |
> | Microsoft.Insights/PrivateLinkScopes/Read | プライベート リンク スコープが読み取られます |
> | Microsoft.Insights/PrivateLinkScopes/Write | プライベート リンク スコープが作成または更新されます |
> | Microsoft.Insights/PrivateLinkScopes/Delete | プライベート リンク スコープが削除されます |
> | Microsoft.Insights/PrivateLinkScopes/PrivateEndpointConnectionProxies/Read | プライベート エンドポイント接続プロキシが読み取られます |
> | Microsoft.Insights/PrivateLinkScopes/PrivateEndpointConnectionProxies/Write | プライベート エンドポイント接続プロキシが作成または更新されます |
> | Microsoft.Insights/PrivateLinkScopes/PrivateEndpointConnectionProxies/Delete | プライベート エンドポイント接続プロキシが削除されます |
> | Microsoft.Insights/PrivateLinkScopes/PrivateEndpointConnectionProxies/Validate/Action | プライベート エンドポイント接続プロキシが検証されます |
> | Microsoft.Insights/PrivateLinkScopes/PrivateEndpointConnections/Read | プライベート エンドポイント接続が読み取られます |
> | Microsoft.Insights/PrivateLinkScopes/PrivateEndpointConnections/Write | プライベート エンドポイント接続を作成または更新されます |
> | Microsoft.Insights/PrivateLinkScopes/PrivateEndpointConnections/Delete | プライベート エンドポイント接続が削除されます |
> | Microsoft.Insights/PrivateLinkScopes/PrivateLinkResources/Read | プライベート リンク リソースが読み取られます |
> | Microsoft.Insights/PrivateLinkScopes/ScopedResources/Read | プライベート リンク スコープ指定されたリソースが読み取られます |
> | Microsoft.Insights/PrivateLinkScopes/ScopedResources/Write | プライベート リンク スコープ指定されたリソースが作成または更新されます |
> | Microsoft.Insights/PrivateLinkScopes/ScopedResources/Delete | プライベート リンク スコープ指定されたリソースが削除されます |
> | Microsoft.Insights/ScheduledQueryRules/Write | スケジュール済みのクエリ ルールを書き込みます。 |
> | Microsoft.Insights/ScheduledQueryRules/Read | スケジュール済みのクエリ ルールを読み取ります。 |
> | Microsoft.Insights/ScheduledQueryRules/Delete | スケジュール済みのクエリ ルールを削除します。 |
> | Microsoft.Insights/Tenants/Register/Action | Microsoft Insights プロバイダーを初期化します |
> | Microsoft.Insights/topology/Read | トポロジを読み取ります |
> | Microsoft.Insights/transactions/Read | トランザクションを読み取ります |
> | Microsoft.Insights/Webtests/Write | Web テストの構成を書き込みます。 |
> | Microsoft.Insights/Webtests/Delete | Web テストの構成を削除します。 |
> | Microsoft.Insights/Webtests/Read | Web テストの構成を読み取ります。 |
> | Microsoft.Insights/Webtests/GetToken/Read | Web テストのトークンを読み取ります。 |
> | Microsoft.Insights/Webtests/MetricDefinitions/Read | Web テストのメトリック定義を読み取ります。 |
> | Microsoft.Insights/Webtests/Metrics/Read | Web テストのメトリックを読み取ります。 |
> | Microsoft.Insights/Workbooks/Write | ブックを作成または更新します |
> | Microsoft.Insights/Workbooks/Delete | ブックを削除します |
> | Microsoft.Insights/Workbooks/Read | ブックを読み取ります |
> | **DataAction** | **説明** |
> | Microsoft.Insights/DataCollectionRules/Data/Write | データ収集ルールにデータを送信します |
> | Microsoft.Insights/Metrics/Write | メトリックを書き込みます。 |

### <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

Azure サービス:[Azure Monitor](../azure-monitor/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.OperationalInsights/register/action | リソース プロバイダーにサブスクリプションを登録します。 |
> | microsoft.operationalinsights/register/action | サブスクリプションを登録します。 |
> | microsoft.operationalinsights/unregister/action | サブスクリプションの登録を解除します。 |
> | microsoft.operationalinsights/availableservicetiers/read | 利用可能なサービス レベルが取得されます。 |
> | Microsoft.OperationalInsights/clusters/read | クラスターを取得します |
> | Microsoft.OperationalInsights/clusters/write | クラスターを作成または更新します |
> | Microsoft.OperationalInsights/clusters/delete | クラスターを削除 |
> | Microsoft.OperationalInsights/deletedWorkspaces/read | 論理的な削除の期間ごとにワークスペースを一覧表示します。 |
> | Microsoft.OperationalInsights/linkTargets/read | 論理的な削除の期間ごとにワークスペースを一覧表示します。 |
> | microsoft.operationalinsights/locations/operationStatuses/read | Log Analytics Azure 非同期操作の状態が取得されます。 |
> | microsoft.operationalinsights/operations/read | 利用可能な OperationalInsights Rest API 操作をすべて表示します。 |
> | Microsoft.OperationalInsights/workspaces/write | 新しいワークスペースを作成するか、既存のワークスペースの顧客 ID を指定して既存のワークスペースにリンクします。 |
> | Microsoft.OperationalInsights/workspaces/read | 既存のワークスペースを取得します。 |
> | Microsoft.OperationalInsights/workspaces/delete | ワークスペースを削除します。 ワークスペースが、作成時に既存のワークスペースにリンクされていた場合、リンク先のワークスペースは削除されません。 |
> | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | ワークスペースの登録証明書を生成します。 この証明書を使用して、Microsoft System Center Operation Manager をワークスペースに接続します。 |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | ワークスペースの共有キーを取得します。 これらのキーを使用して、Microsoft Operational Insights エージェントをワークスペースに接続します。 |
> | Microsoft.OperationalInsights/workspaces/listKeys/action | ワークスペースの一覧キーを取得します。 これらのキーを使用して、Microsoft Operational Insights エージェントをワークスペースに接続します。 |
> | Microsoft.OperationalInsights/workspaces/search/action | 検索クエリを実行します。 |
> | Microsoft.OperationalInsights/workspaces/purge/action | ワークスペースから指定されたデータを削除します。 |
> | Microsoft.OperationalInsights/workspaces/regeneratesharedkey/action | 指定されたワークスペース共有キーを再生成します |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | 新しいエンジンを使用して検索します。 |
> | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | 検索スキーマ V2 を取得します。 |
> | Microsoft.OperationalInsights/workspaces/api/query/action | 新しいエンジンを使用して検索します。 |
> | Microsoft.OperationalInsights/workspaces/api/query/schema/read | 検索スキーマ V2 を取得します。 |
> | Microsoft.OperationalInsights/workspaces/availableservicetiers/read | ワークスペースで使用可能なすべてのサービス レベルの一覧。 |
> | Microsoft.OperationalInsights/workspaces/configurationScopes/read | 構成スコープを取得します。 |
> | Microsoft.OperationalInsights/workspaces/configurationScopes/write | 構成スコープを設定します。 |
> | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | 構成スコープを削除します。 |
> | microsoft.operationalinsights/workspaces/dataExports/read | 特定のデータ エクスポートが取得されます。 |
> | microsoft.operationalinsights/workspaces/dataExports/write | データ エクスポートが作成または更新されます。 |
> | microsoft.operationalinsights/workspaces/dataExports/delete | 特定のデータ エクスポートが削除されます。 |
> | Microsoft.OperationalInsights/workspaces/datasources/read | ワークスペースのデータソースを取得します。 |
> | Microsoft.OperationalInsights/workspaces/datasources/write | ワークスペースのデータソースを作成または更新します。 |
> | Microsoft.OperationalInsights/workspaces/datasources/delete | ワークスペースのデータソースを削除します。 |
> | Microsoft.OperationalInsights/workspaces/gateways/delete | ワークスペースに対して構成されたゲートウェイを削除します。 |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | 指定されたワークスペースで表示されるすべてのインテリジェンス パックを一覧表示します。また、そのワークスペースでパックが有効になっているか無効になっているかも示します。 |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | 指定されたワークスペースのインテリジェンス パックを有効にします。 |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | 指定されたワークスペースのインテリジェンス パックを無効にします。 |
> | Microsoft.OperationalInsights/workspaces/linkedServices/read | 指定されたワークスペースのリンクされたサービスを取得します。 |
> | Microsoft.OperationalInsights/workspaces/linkedServices/write | 指定されたワークスペースのリンクされたサービスを作成または更新します。 |
> | Microsoft.OperationalInsights/workspaces/linkedServices/delete | 指定されたワークスペースのリンクされたサービスを削除します。 |
> | Microsoft.OperationalInsights/workspaces/listKeys/read | ワークスペースの一覧キーを取得します。 これらのキーを使用して、Microsoft Operational Insights エージェントをワークスペースに接続します。 |
> | Microsoft.OperationalInsights/workspaces/managementGroups/read | このワークスペースに接続されている System Center Operations Manager 管理グループの名前とメタデータを取得します。 |
> | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | ワークスペースのメトリック定義を取得します。 |
> | Microsoft.OperationalInsights/workspaces/notificationSettings/read | ワークスペースのユーザーの通知設定を取得します。 |
> | Microsoft.OperationalInsights/workspaces/notificationSettings/write | ワークスペースのユーザーの通知設定を設定します。 |
> | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | ワークスペースのユーザーの通知設定を削除します。 |
> | microsoft.operationalinsights/workspaces/operations/read | OperationalInsights ワークスペース操作の状態を取得します。 |
> | Microsoft.OperationalInsights/workspaces/query/read | ワークスペース内のデータに対してクエリを実行します |
> | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountLogon/read | AADDomainServicesAccountLogon テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountManagement/read | AADDomainServicesAccountManagement テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesDirectoryServiceAccess/read | AADDomainServicesDirectoryServiceAccess テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesLogonLogoff/read | AADDomainServicesLogonLogoff テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPolicyChange/read | AADDomainServicesPolicyChange テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPrivilegeUse/read | AADDomainServicesPrivilegeUse テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesSystemSecurity/read | AADDomainServicesSystemSecurity テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AADManagedIdentitySignInLogs/read | AADManagedIdentitySignInLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AADNonInteractiveUserSignInLogs/read | AADNonInteractiveUserSignInLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AADProvisioningLogs/read | AADProvisioningLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AADServicePrincipalSignInLogs/read | AADServicePrincipalSignInLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/ABSBotRequests/read | ABSBotRequests テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/ABSChannelToBotRequests/read | ABSChannelToBotRequests テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/ABSDependenciesRequests/read | ABSDependenciesRequests テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/ACSBillingUsage/read | ACSBillingUsage テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/ACSChatIncomingOperations/read | ACSChatIncomingOperations テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/ACSSMSIncomingOperations/read | ACSSMSIncomingOperations テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/ADAssessmentRecommendation/read | ADAssessmentRecommendation テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/AddonAzureBackupAlerts/read | AddonAzureBackupAlerts テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AddonAzureBackupJobs/read | AddonAzureBackupJobs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AddonAzureBackupPolicy/read | AddonAzureBackupPolicy テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AddonAzureBackupProtectedInstance/read | AddonAzureBackupProtectedInstance テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AddonAzureBackupStorage/read | AddonAzureBackupStorage テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/ADFActivityRun/read | ADFActivityRun テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/ADFPipelineRun/read | ADFPipelineRun テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/ADFSandboxActivityRun/read | ADFSandboxActivityRun テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/ADFSandboxPipelineRun/read | ADFSandboxPipelineRun テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/ADFSSignInLogs/read | ADFSSigninLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/ADFSSISIntegrationRuntimeLogs/read | ADFSSISIntegrationRuntimeLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/ADFSSISPackageEventMessageContext/read | ADFSSISPackageEventMessageContext テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/ADFSSISPackageEventMessages/read | ADFSSISPackageEventMessages テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/ADFSSISPackageExecutableStatistics/read | ADFSSISPackageExecutableStatistics テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/ADFSSISPackageExecutionComponentPhases/read | ADFSSISPackageExecutionComponentPhases テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/ADFSSISPackageExecutionDataStatistics/read | ADFSSISPackageExecutionDataStatistics テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/ADFTriggerRun/read | ADFTriggerRun テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/ADReplicationResult/read | ADReplicationResult テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/ADSecurityAssessmentRecommendation/read | ADSecurityAssessmentRecommendation テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/ADTDigitalTwinsOperation/read | ADTDigitalTwinsOperation テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/ADTEventRoutesOperation/read | ADTEventRoutesOperation テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/ADTModelsOperation/read | ADTModelsOperation テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/ADTQueryOperation/read | ADTQueryOperation テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/ADXCommand/read | ADXCommand テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/ADXIngestionBatching/read | ADXIngestionBatching テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/ADXQuery/read | ADXQuery テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/ADXTableDetails/read | ADXTableDetails テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/ADXTableUsageStatistics/read | ADXTableUsageStatistics テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AegDeliveryFailureLogs/read | AegDeliveryFailureLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AegPublishFailureLogs/read | AegPublishFailureLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AEWAuditLogs/read | AEWAuditLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/Alert/read | Alert テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/AlertHistory/read | AlertHistory テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/AmlComputeClusterEvent/read | AmlComputeClusterEvent テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AmlComputeClusterNodeEvent/read | AmlComputeClusterNodeEvent テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AmlComputeCpuGpuUtilization/read | AmlComputeCpuGpuUtilization テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AmlComputeJobEvent/read | AmlComputeJobEvent テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AmlOnlineEndpointConsoleLog/read | AmlOnlineEndpointConsoleLog テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AmlRunStatusChangedEvent/read | AmlRunStatusChangedEvent テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/Anomalies/read | Anomalies テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/ApiManagementGatewayLogs/read | ApiManagementGatewayLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AppAvailabilityResults/read | AppAvailabilityResults テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AppBrowserTimings/read | AppBrowserTimings テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AppCenterError/read | AppCenterError テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AppDependencies/read | AppDependencies テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AppEvents/read | AppEvents テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AppExceptions/read | AppExceptions テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/ApplicationInsights/read | ApplicationInsights テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/AppMetrics/read | AppMetrics テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AppPageViews/read | AppPageViews テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AppPerformanceCounters/read | AppPerformanceCounters テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AppPlatformLogsforSpring/read | AppPlatformLogsforSpring テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AppPlatformSystemLogs/read | AppPlatformSystemLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AppRequests/read | AppRequests テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AppServiceAntivirusScanAuditLogs/read | AppServiceAntivirusScanAuditLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AppServiceAntivirusScanLogs/read | AppServiceAntivirusScanLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AppServiceAppLogs/read | AppServiceAppLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AppServiceAuditLogs/read | AppServiceAuditLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AppServiceConsoleLogs/read | AppServiceConsoleLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AppServiceEnvironmentPlatformLogs/read | AppServiceEnvironmentPlatformLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AppServiceFileAuditLogs/read | AppServiceFileAuditLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AppServiceHTTPLogs/read | AppServiceHTTPLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AppServiceIPSecAuditLogs/read | AppServiceIPSecAuditLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AppServicePlatformLogs/read | AppServicePlatformLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AppSystemEvents/read | AppSystemEvents テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AppTraces/read | AppTraces テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AuditLogs/read | AuditLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AutoscaleEvaluationsLog/read | AutoscaleEvaluationsLog テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/AutoscaleScaleActionsLog/read | AutoscaleScaleActionsLog テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/AWSCloudTrail/read | AWSCloudTrail テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/AzureActivity/read | AzureActivity テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/AzureAssessmentRecommendation/read | AzureAssessmentRecommendation テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AzureDevOpsAuditing/read | AzureDevOpsAuditing テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AzureDiagnostics/read | AzureDiagnostics テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AzureDiagnostics/read | AzureDiagnostics テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/AzureMetrics/read | AzureMetrics テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/BaiClusterEvent/read | BaiClusterEvent テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/BaiClusterNodeEvent/read | BaiClusterNodeEvent テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/BaiJobEvent/read | BaiJobEvent テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/BehaviorAnalytics/read | BehaviorAnalytics テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/BlockchainApplicationLog/read | BlockchainApplicationLog テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/BlockchainProxyLog/read | BlockchainProxyLog テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/BoundPort/read | BoundPort テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/CommonSecurityLog/read | CommonSecurityLog テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read | ComputerGroup テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/ConfigurationChange/read | ConfigurationChange テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/ConfigurationData/read | ConfigurationData テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/ContainerImageInventory/read | ContainerImageInventory テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/ContainerInventory/read | ContainerInventory テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/ContainerLog/read | ContainerLog テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/ContainerLogV2/read | ContainerLogV2 テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/ContainerNodeInventory/read | ContainerNodeInventory テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/ContainerRegistryLoginEvents/read | ContainerRegistryLoginEvents テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/ContainerRegistryRepositoryEvents/read | ContainerRegistryRepositoryEvents テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/ContainerServiceLog/read | ContainerServiceLog テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/CoreAzureBackup/read | CoreAzureBackup テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/DatabricksAccounts/read | DatabricksAccounts テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/DatabricksClusters/read | DatabricksClusters テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/DatabricksDBFS/read | DatabricksDBFS テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/DatabricksInstancePools/read | DatabricksInstancePools テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/DatabricksJobs/read | DatabricksJobs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/DatabricksNotebook/read | DatabricksNotebook テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/DatabricksSecrets/read | DatabricksSecrets テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/DatabricksSQLPermissions/read | DatabricksSQLPermissions テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/DatabricksSSH/read | DatabricksSSH テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/DatabricksTables/read | DatabricksTables テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/DatabricksWorkspace/read | DatabricksWorkspace テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/dependencies/read | dependencies テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/DeviceAppCrash/read | DeviceAppCrash テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceAppLaunch/read | DeviceAppLaunch テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceCalendar/read | DeviceCalendar テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceCleanup/read | DeviceCleanup テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceConnectSession/read | DeviceConnectSession テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceEtw/read | DeviceEtw テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceEvents/read | DeviceEvents テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/DeviceFileCertificateInfo/read | DeviceFileCertificateInfo テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/DeviceFileEvents/read | DeviceFileEvents テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/DeviceHardwareHealth/read | DeviceHardwareHealth テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceHealth/read | DeviceHealth テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceHeartbeat/read | DeviceHeartbeat テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceImageLoadEvents/read | DeviceImageLoadEvents テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/DeviceInfo/read | DeviceInfo テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/DeviceLogonEvents/read | DeviceLogonEvents テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/DeviceNetworkEvents/read | DeviceNetworkEvents テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/DeviceNetworkInfo/read | DeviceNetworkInfo テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/DeviceProcessEvents/read | DeviceProcessEvents テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/DeviceRegistryEvents/read | DeviceRegistryEvents テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeHeartbeat/read | DeviceSkypeHeartbeat テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeSignIn/read | DeviceSkypeSignIn テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/DeviceSleepState/read | DeviceSleepState テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/DHAppFailure/read | DHAppFailure テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/DHAppReliability/read | DHAppReliability テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/DHCPActivity/read | DHCPActivity テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/DHDriverReliability/read | DHDriverReliability テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/DHLogonFailures/read | DHLogonFailures テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/DHLogonMetrics/read | DHLogonMetrics テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/DHOSCrashData/read | DHOSCrashData テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/DHOSReliability/read | DHOSReliability テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/DHWipAppLearning/read | DHWipAppLearning テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/DnsEvents/read | DnsEvents テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/DnsInventory/read | DnsInventory テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/DynamicEventCollection/read | DynamicEventCollection テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/Dynamics365Activity/read | Dynamics365Activity テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/ETWEvent/read | ETWEvent テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/Event/read | Event テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/ExchangeAssessmentRecommendation/read | ExchangeAssessmentRecommendation テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/ExchangeOnlineAssessmentRecommendation/read | ExchangeOnlineAssessmentRecommendation テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/FailedIngestion/read | FailedIngestion テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/FunctionAppLogs/read | FunctionAppLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightAmbariClusterAlerts/read | HDInsightAmbariClusterAlerts テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightAmbariSystemMetrics/read | HDInsightAmbariSystemMetrics テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightGatewayAuditLogs/read | HDInsightGatewayAuditLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightHadoopAndYarnLogs/read | HDInsightHadoopAndYarnLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightHadoopAndYarnMetrics/read | HDInsightHadoopAndYarnMetrics テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightHBaseLogs/read | HDInsightHBaseLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightHBaseMetrics/read | HDInsightHBaseMetrics テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightHiveAndLLAPLogs/read | HDInsightHiveAndLLAPLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightHiveAndLLAPMetrics/read | HDInsightHiveAndLLAPMetrics テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightJupyterNotebookEvents/read | HDInsightJupyterNotebookEvents テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightKafkaLogs/read | HDInsightKafkaLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightKafkaMetrics/read | HDInsightKafkaMetrics テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightOozieLogs/read | HDInsightOozieLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightRangerAuditLogs/read | HDInsightRangerAuditLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightSecurityLogs/read | HDInsightSecurityLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightSparkApplicationEvents/read | HDInsightSparkApplicationEvents テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightSparkBlockManagerEvents/read | HDInsightSparkBlockManagerEvents テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightSparkEnvironmentEvents/read | HDInsightSparkEnvironmentEvents テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightSparkExecutorEvents/read | HDInsightSparkExecutorEvents テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightSparkExtraEvents/read | HDInsightSparkExtraEvents テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightSparkJobEvents/read | HDInsightSparkJobEvents テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightSparkLogs/read | HDInsightSparkLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightSparkSQLExecutionEvents/read | HDInsightSparkSQLExecutionEvents テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightSparkStageEvents/read | HDInsightSparkStageEvents テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightSparkStageTaskAccumulables/read | HDInsightSparkStageTaskAccumulables テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightSparkTaskEvents/read | HDInsightSparkTaskEvents テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightStormLogs/read | HDInsightStormLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightStormMetrics/read | HDInsightStormMetrics テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/HDInsightStormTopologyMetrics/read | HDInsightStormTopologyMetrics テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/HealthStateChangeEvent/read | HealthStateChangeEvent テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/Heartbeat/read | Heartbeat テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/HuntingBookmark/read | HuntingBookmark テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/IdentityInfo/read | IdentityInfo テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/IISAssessmentRecommendation/read | IISAssessmentRecommendation テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/InboundConnection/read | InboundConnection テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/InsightsMetrics/read | InsightsMetrics テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/IntuneAuditLogs/read | IntuneAuditLogs テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/IntuneDeviceComplianceOrg/read | IntuneDeviceComplianceOrg テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/IntuneDevices/read | IntuneDevices テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/IntuneOperationalLogs/read | IntuneOperationalLogs テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/IoTHubDistributedTracing/read | IoTHubDistributedTracing テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/KubeEvents/read | KubeEvents テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/KubeHealth/read | KubeHealth テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/KubeMonAgentEvents/read | KubeMonAgentEvents テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/KubeNodeInventory/read | KubeNodeInventory テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/KubePodInventory/read | KubePodInventory テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/KubePVInventory/read | KubePVInventory テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/KubeServices/read | KubeServices テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/LAQueryLogs/read | LAQueryLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/LinuxAuditLog/read | LinuxAuditLog テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MAApplication/read | MAApplication テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealth/read | MAApplicationHealth テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthAlternativeVersions/read | MAApplicationHealthAlternativeVersions テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthIssues/read | MAApplicationHealthIssues テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstance/read | MAApplicationInstance テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstanceReadiness/read | MAApplicationInstanceReadiness テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MAApplicationReadiness/read | MAApplicationReadiness テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MADeploymentPlan/read | MADeploymentPlan テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MADevice/read | MADevice テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MADeviceNotEnrolled/read | MADeviceNotEnrolled テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/MADeviceNRT/read | MADeviceNRT テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealth/read | MADevicePnPHealth テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthAlternativeVersions/read | MADevicePnPHealthAlternativeVersions テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthIssues/read | MADevicePnPHealthIssues テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MADeviceReadiness/read | MADeviceReadiness テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MADriverInstanceReadiness/read | MADriverInstanceReadiness テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MADriverReadiness/read | MADriverReadiness テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddin/read | MAOfficeAddin テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinEntityHealth/read | MAOfficeAddinEntityHealth テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealth/read | MAOfficeAddinHealth テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthEventNRT/read | MAOfficeAddinHealthEventNRT テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthIssues/read | MAOfficeAddinHealthIssues テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstance/read | MAOfficeAddinInstance テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstanceReadiness/read | MAOfficeAddinInstanceReadiness テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinReadiness/read | MAOfficeAddinReadiness テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeApp/read | MAOfficeApp テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppCrashesNRT/read | MAOfficeAppCrashesNRT テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppHealth/read | MAOfficeAppHealth テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstance/read | MAOfficeAppInstance テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstanceHealth/read | MAOfficeAppInstanceHealth テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppReadiness/read | MAOfficeAppReadiness テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppSessionsNRT/read | MAOfficeAppSessionsNRT テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeBuildInfo/read | MAOfficeBuildInfo テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessment/read | MAOfficeCurrencyAssessment テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessmentDailyCounts/read | MAOfficeCurrencyAssessmentDailyCounts テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatus/read | MAOfficeDeploymentStatus テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatusNRT/read | MAOfficeDeploymentStatusNRT テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroErrorNRT/read | MAOfficeMacroErrorNRT テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroGlobalHealth/read | MAOfficeMacroGlobalHealth テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealth/read | MAOfficeMacroHealth テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealthIssues/read | MAOfficeMacroHealthIssues テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueInstanceReadiness/read | MAOfficeMacroIssueInstanceReadiness テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueReadiness/read | MAOfficeMacroIssueReadiness テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroSummary/read | MAOfficeMacroSummary テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuite/read | MAOfficeSuite テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuiteInstance/read | MAOfficeSuiteInstance テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MAProposedPilotDevices/read | MAProposedPilotDevices テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MAWindowsBuildInfo/read | MAWindowsBuildInfo テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessment/read | MAWindowsCurrencyAssessment テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessmentDailyCounts/read | MAWindowsCurrencyAssessmentDailyCounts テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatus/read | MAWindowsDeploymentStatus テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatusNRT/read | MAWindowsDeploymentStatusNRT テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/MAWindowsSysReqInstanceReadiness/read | MAWindowsSysReqInstanceReadiness テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/McasShadowItReporting/read | McasShadowItReporting テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/MicrosoftAzureBastionAuditLogs/read | MicrosoftAzureBastionAuditLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/MicrosoftDataShareReceivedSnapshotLog/read | MicrosoftDataShareReceivedSnapshotLog テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/MicrosoftDataShareSentSnapshotLog/read | MicrosoftDataShareSentSnapshotLog テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/MicrosoftDataShareShareLog/read | MicrosoftDataShareShareLog テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/MicrosoftDynamicsTelemetryPerformanceLogs/read | MicrosoftDynamicsTelemetryPerformanceLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/MicrosoftDynamicsTelemetrySystemMetricsLogs/read | MicrosoftDynamicsTelemetrySystemMetricsLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/MicrosoftHealthcareApisAuditLogs/read | MicrosoftHealthcareApisAuditLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/NetworkMonitoring/read | NetworkMonitoring テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/NetworkSessions/read | NetworkSessions テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/NWConnectionMonitorDestinationListenerResult/read | NWConnectionMonitorDestinationListenerResult テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/NWConnectionMonitorDNSResult/read | NWConnectionMonitorDNSResult テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/NWConnectionMonitorPathResult/read | NWConnectionMonitorPathResult テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/NWConnectionMonitorTestResult/read | NWConnectionMonitorTestResult テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/OfficeActivity/read | OfficeActivity テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/Operation/read | Operation テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/OutboundConnection/read | OutboundConnection テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/Perf/read | Perf テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/PowerBIDatasetsTenant/read | PowerBIDatasetsTenant テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/PowerBIDatasetsTenantPreview/read | PowerBIDatasetsTenantPreview テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/PowerBIDatasetsWorkspace/read | PowerBIDatasetsWorkspace テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/PowerBIDatasetsWorkspacePreview/read | PowerBIDatasetsWorkspacePreview テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/ProtectionStatus/read | ProtectionStatus テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/requests/read | requests テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/SCCMAssessmentRecommendation/read | SCCMAssessmentRecommendation テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/SCOMAssessmentRecommendation/read | SCOMAssessmentRecommendation テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/SecureScoreControls/read | SecureScoreControls テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/SecureScores/read | SecureScores テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read | SecurityAlert テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read | SecurityBaseline テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/SecurityBaselineSummary/read | SecurityBaselineSummary テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/SecurityDetection/read | SecurityDetection テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/SecurityEvent/read | SecurityEvent テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/SecurityIncident/read | SecurityIncident テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/SecurityIoTRawEvent/read | SecurityIoTRawEvent テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/SecurityNestedRecommendation/read | SecurityNestedRecommendation テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/SecurityRecommendation/read | SecurityRecommendation テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/SecurityRegulatoryCompliance/read | SecurityRegulatoryCompliance テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/SentinelHealth/read | SentinelHealth テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/ServiceFabricOperationalEvent/read | ServiceFabricOperationalEvent テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableActorEvent/read | ServiceFabricReliableActorEvent テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableServiceEvent/read | ServiceFabricReliableServiceEvent テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/SfBAssessmentRecommendation/read | SfBAssessmentRecommendation テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/SfBOnlineAssessmentRecommendation/read | SfBOnlineAssessmentRecommendation テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/SharePointOnlineAssessmentRecommendation/read | SharePointOnlineAssessmentRecommendation テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/SignalRServiceDiagnosticLogs/read | SignalRServiceDiagnosticLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/SigninLogs/read | SigninLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/SPAssessmentRecommendation/read | SPAssessmentRecommendation テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/SQLAssessmentRecommendation/read | SQLAssessmentRecommendation テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/SqlDataClassification/read | SqlDataClassification テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/SQLQueryPerformance/read | SQLQueryPerformance テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/SqlVulnerabilityAssessmentResult/read | SqlVulnerabilityAssessmentResult テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/SqlVulnerabilityAssessmentScanStatus/read | SqlVulnerabilityAssessmentScanStatus テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/StorageBlobLogs/read | StorageBlobLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/StorageFileLogs/read | StorageFileLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/StorageQueueLogs/read | StorageQueueLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/StorageTableLogs/read | StorageTableLogs テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/SucceededIngestion/read | SucceededIngestion テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/SynapseBigDataPoolApplicationsEnded/read | SynapseBigDataPoolApplicationsEnded テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/SynapseBuiltinSqlPoolRequestsEnded/read | SynapseBuiltinSqlPoolRequestsEnded テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/SynapseGatewayApiRequests/read | SynapseGatewayApiRequests テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/SynapseGatewayEvents/read | SynapseGatewayEvents テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/SynapseIntegrationActivityRuns/read | SynapseIntegrationActivityRuns テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/SynapseIntegrationActivityRunsEnded/read | SynapseIntegrationActivityRunsEnded テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/SynapseIntegrationPipelineRuns/read | SynapseIntegrationPipelineRuns テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/SynapseIntegrationPipelineRunsEnded/read | SynapseIntegrationPipelineRunsEnded テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/SynapseIntegrationTriggerRuns/read | SynapseIntegrationTriggerRuns テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/SynapseIntegrationTriggerRunsEnded/read | SynapseIntegrationTriggerRunsEnded テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/SynapseRBACEvents/read | SynapseRBACEvents テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/SynapseRbacOperations/read | SynapseRbacOperations テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/SynapseSqlPoolDmsWorkers/read | SynapseSqlPoolDmsWorkers テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/SynapseSqlPoolExecRequests/read | SynapseSqlPoolExecRequests テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/SynapseSqlPoolRequestSteps/read | SynapseSqlPoolRequestSteps テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/SynapseSqlPoolSqlRequests/read | SynapseSqlPoolSqlRequests テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/SynapseSqlPoolWaits/read | SynapseSqlPoolWaits テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/Syslog/read | Syslog テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/SysmonEvent/read | SysmonEvent テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read | カスタム ログからデータを読み取っています。 |
> | Microsoft.OperationalInsights/workspaces/query/ThreatIntelligenceIndicator/read | ThreatIntelligenceIndicator テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/TSIIngress/read | TSIIngress テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/UAApp/read | UAApp テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/UAComputer/read | UAComputer テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/UAComputerRank/read | UAComputerRank テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/UADriver/read | UADriver テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/UADriverProblemCodes/read | UADriverProblemCodes テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/UAFeedback/read | UAFeedback テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/UAHardwareSecurity/read | UAHardwareSecurity テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/UAIESiteDiscovery/read | UAIESiteDiscovery テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/UAOfficeAddIn/read | UAOfficeAddIn テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/UAProposedActionPlan/read | UAProposedActionPlan テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/UASysReqIssue/read | UASysReqIssue テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/UAUpgradedComputer/read | UAUpgradedComputer テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/Update/read | Update テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read | UpdateRunProgress テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read | UpdateSummary テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/Usage/read | Usage テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/UserAccessAnalytics/read | UserAccessAnalytics テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/UserPeerAnalytics/read | UserPeerAnalytics テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/VMBoundPort/read | VMBoundPort テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/VMComputer/read | VMComputer テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/VMConnection/read | VMConnection テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/VMProcess/read | VMProcess テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/W3CIISLog/read | W3CIISLog テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/WaaSDeploymentStatus/read | WaaSDeploymentStatus テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/WaaSInsiderStatus/read | WaaSInsiderStatus テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/WaaSUpdateStatus/read | WaaSUpdateStatus テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/Watchlist/read | Watchlist テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/WDAVStatus/read | WDAVStatus テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/WDAVThreat/read | WDAVThreat テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/WindowsClientAssessmentRecommendation/read | WindowsClientAssessmentRecommendation テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/WindowsEvent/read | WindowsEvent テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/WindowsFirewall/read | WindowsFirewall テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/WindowsServerAssessmentRecommendation/read | WindowsServerAssessmentRecommendation テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/WireData/read | WireData テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/WorkloadMonitoringPerf/read | WorkloadMonitoringPerf テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/WUDOAggregatedStatus/read | WUDOAggregatedStatus テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/WUDOStatus/read | WUDOStatus テーブルからデータを読み取ります。 |
> | Microsoft.OperationalInsights/workspaces/query/WVDAgentHealthStatus/read | WVDAgentHealthStatus テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/WVDCheckpoints/read | WVDCheckpoints テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/WVDConnections/read | WVDConnections テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/WVDErrors/read | WVDErrors テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/WVDFeeds/read | WVDFeeds テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/WVDHostRegistrations/read | WVDHostRegistrations テーブルからデータを読み取ります |
> | Microsoft.OperationalInsights/workspaces/query/WVDManagement/read | WVDManagement テーブルからデータを読み取ります |
> | microsoft.operationalinsights/workspaces/rules/read | すべてのアラート ルールを取得します。 |
> | Microsoft.OperationalInsights/workspaces/savedSearches/read | 保存された検索クエリを取得します。 |
> | Microsoft.OperationalInsights/workspaces/savedSearches/write | 保存された検索クエリを作成します。 |
> | Microsoft.OperationalInsights/workspaces/savedSearches/delete | 保存された検索クエリを削除します。 |
> | microsoft.operationalinsights/workspaces/savedsearches/results/read | 保存されている検索結果を取得します。 非推奨 |
> | microsoft.operationalinsights/workspaces/savedsearches/schedules/read | スケジュールされた検索を取得します。 |
> | microsoft.operationalinsights/workspaces/savedsearches/schedules/delete | スケジュールされた検索を削除します。 |
> | microsoft.operationalinsights/workspaces/savedsearches/schedules/write | スケジュールされた検索を作成または更新します。 |
> | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/read | スケジュールされた検索アクションを取得します。 |
> | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/delete | スケジュールされた検索アクションを削除します。 |
> | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/write | スケジュールされた検索アクションを作成または更新します。 |
> | Microsoft.OperationalInsights/workspaces/schema/read | ワークスペースの検索スキーマを取得します。  検索スキーマには、公開されているフィールドとその種類が含まれます。 |
> | microsoft.operationalinsights/workspaces/scopedPrivateLinkProxies/read | スコープ指定された Private Link プロキシが取得されます。 |
> | microsoft.operationalinsights/workspaces/scopedPrivateLinkProxies/write | スコープ指定された Private Link プロキシが配置されます。 |
> | microsoft.operationalinsights/workspaces/scopedPrivateLinkProxies/delete | スコープ指定された Private Link プロキシが削除されます。 |
> | microsoft.operationalinsights/workspaces/search/read | 検索結果を取得します。 非推奨になりました。 |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read | ワークスペースの共有キーを取得します。 これらのキーを使用して、Microsoft Operational Insights エージェントをワークスペースに接続します。 |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | 新しいストレージ構成を作成します。 これらの構成を使用して、既存のストレージ アカウント内の場所からデータを取得します。 |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | ストレージ構成を取得します。 |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | ストレージ構成を削除します。 これにより、Microsoft Operational Insights はストレージ アカウントからデータを読み取らなくなります。 |
> | Microsoft.OperationalInsights/workspaces/upgradetranslationfailures/read | ワークスペースの検索アップグレード変換のエラー ログを取得します |
> | Microsoft.OperationalInsights/workspaces/usages/read | ワークスペースで読み取られたデータの量など、ワークスペースの使用状況データを取得します。 |

### <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

Azure サービス:[Azure Monitor](../azure-monitor/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.OperationsManagement/register/action | リソース プロバイダーにサブスクリプションを登録します。 |
> | Microsoft.OperationsManagement/managementAssociations/write | 新しい管理関連付けを作成します。 |
> | Microsoft.OperationsManagement/managementAssociations/read | 既存の管理関連付けを取得します。 |
> | Microsoft.OperationsManagement/managementAssociations/delete | 既存の管理関連付けを削除します。 |
> | Microsoft.OperationsManagement/managementConfigurations/write | 新しい管理構成を作成します。 |
> | Microsoft.OperationsManagement/managementConfigurations/read | 既存の管理構成を取得します。 |
> | Microsoft.OperationsManagement/managementConfigurations/delete | 既存の管理構成を削除します。 |
> | Microsoft.OperationsManagement/solutions/write | 新しい OMS ソリューションを作成します。 |
> | Microsoft.OperationsManagement/solutions/read | 既存の OMS ソリューションを取得します。 |
> | Microsoft.OperationsManagement/solutions/delete | 既存の OMS ソリューションを削除します。 |

### <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

Azure サービス:[Azure Monitor](../azure-monitor/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.WorkloadMonitor/register/action | Microsoft.WorkloadMonitor リソース プロバイダーにサブスクリプションを登録します |
> | Microsoft.WorkloadMonitor/unregister/action | Microsoft.WorkloadMonitor リソース プロバイダーからサブスクリプションを登録解除します |
> | Microsoft.WorkloadMonitor/monitors/read | モニターとその現在の正常性状態を取得します |
> | Microsoft.WorkloadMonitor/monitors/history/read | 特定のモニターの正常性の変化の履歴を取得します |
> | Microsoft.WorkloadMonitor/operations/read | サポート対象の操作の一覧を取得します |

## <a name="management--governance"></a>管理 + ガバナンス

### <a name="microsoftadvisor"></a>Microsoft.Advisor

Azure サービス:[Azure Advisor](../advisor/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Advisor/generateRecommendations/action | 推奨事項の生成の状態を取得します。 |
> | Microsoft.Advisor/register/action | Microsoft Advisor にサブスクリプションを登録します。 |
> | Microsoft.Advisor/unregister/action | Microsoft Advisor のサブスクリプションを登録解除します。 |
> | Microsoft.Advisor/configurations/read | 構成の取得 |
> | Microsoft.Advisor/configurations/write | 構成を作成/更新します。 |
> | Microsoft.Advisor/generateRecommendations/read | 推奨事項の生成の状態を取得します。 |
> | Microsoft.Advisor/metadata/read | [Get Metadata (メタデータの取得)] |
> | Microsoft.Advisor/operations/read | Microsoft Advisor の操作を取得します。 |
> | Microsoft.Advisor/recommendations/read | 推奨事項を読み取ります。 |
> | Microsoft.Advisor/recommendations/available/action | 新しい推奨事項は Microsoft Advisor で使用できます。 |
> | Microsoft.Advisor/recommendations/suppressions/read | 抑制を取得します。 |
> | Microsoft.Advisor/recommendations/suppressions/write | 抑制を作成または更新します。 |
> | Microsoft.Advisor/recommendations/suppressions/delete | 抑制を削除します。 |
> | Microsoft.Advisor/suppressions/read | 抑制を取得します。 |
> | Microsoft.Advisor/suppressions/write | 抑制を作成または更新します。 |
> | Microsoft.Advisor/suppressions/delete | 抑制を削除します。 |

### <a name="microsoftauthorization"></a>Microsoft.Authorization

Azure サービス:[Azure Policy](../governance/policy/overview.md)、[Azure RBAC](overview.md)、[Azure Resource Manager](../azure-resource-manager/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Authorization/elevateAccess/action | テナント スコープで、ユーザー アクセス管理者のアクセス権を呼び出し元に付与します。 |
> | Microsoft.Authorization/classicAdministrators/read | サブスクリプションの管理者を読み取ります。 カスタム ロールで NotAction として使用された場合は効果がありません。 |
> | Microsoft.Authorization/classicAdministrators/write | サブスクリプションの管理者を追加または変更しします。 |
> | Microsoft.Authorization/classicAdministrators/delete | サブスクリプションから管理者を削除します。 |
> | Microsoft.Authorization/classicAdministrators/operationstatuses/read | 管理者によるサブスクリプションの操作状態を取得します。 |
> | Microsoft.Authorization/denyAssignments/read | 拒否割り当てに関する情報を取得します。 |
> | Microsoft.Authorization/denyAssignments/write | 指定したスコープにおける拒否割り当てを作成します。 |
> | Microsoft.Authorization/denyAssignments/delete | 指定したスコープにおける拒否割り当てを削除します。 |
> | Microsoft.Authorization/locks/read | 指定されたスコープのロックを取得します。 |
> | Microsoft.Authorization/locks/write | 指定されたスコープのロックを追加します。 |
> | Microsoft.Authorization/locks/delete | 指定されたスコープのロックを削除します。 |
> | Microsoft.Authorization/operations/read | 操作の一覧を取得します。 |
> | Microsoft.Authorization/permissions/read | 指定されたスコープで呼び出し元が持つすべてのアクセス許可を一覧表示します。 |
> | Microsoft.Authorization/policies/audit/action | 'audit' 効果を含む Azure Policy 評価の結果として実行されるアクション |
> | Microsoft.Authorization/policies/auditIfNotExists/action | 'auditIfNotExists' 効果を含む Azure Policy の評価の結果として実行されるアクション |
> | Microsoft.Authorization/policies/deny/action | 'deny' 効果を含む Azure Policy 評価の結果として実行されるアクション |
> | Microsoft.Authorization/policies/deployIfNotExists/action | 'deployIfNotExists' 効果を含む Azure Policy の評価の結果として実行されるアクション |
> | Microsoft.Authorization/policyAssignments/read | ポリシー割り当てに関する情報を取得します。 |
> | Microsoft.Authorization/policyAssignments/write | 指定されたスコープのポリシー割り当てを作成します。 |
> | Microsoft.Authorization/policyAssignments/delete | 指定されたスコープのポリシー割り当てを削除します。 |
> | Microsoft.Authorization/policyAssignments/exempt/action | 指定されたスコープでのポリシーの割り当てを除外します。 |
> | Microsoft.Authorization/policyAssignments/privateLinkAssociations/read | プライベート リンクの関連付けに関する情報を取得します。 |
> | Microsoft.Authorization/policyAssignments/privateLinkAssociations/write | プライベート リンクの関連付けを作成または更新します。 |
> | Microsoft.Authorization/policyAssignments/privateLinkAssociations/delete | プライベート リンクの関連付けを削除します。 |
> | Microsoft.Authorization/policyAssignments/resourceManagementPrivateLinks/read | リソース管理のプライベート リンクに関する情報を取得します。 |
> | Microsoft.Authorization/policyAssignments/resourceManagementPrivateLinks/write | リソース管理のプライベート リンクを作成または更新します。 |
> | Microsoft.Authorization/policyAssignments/resourceManagementPrivateLinks/delete | リソース管理のプライベート リンクを削除します。 |
> | Microsoft.Authorization/policyAssignments/resourceManagementPrivateLinks/privateEndpointConnectionProxies/read | プライベート エンドポイント接続プロキシに関する情報を取得します。 |
> | Microsoft.Authorization/policyAssignments/resourceManagementPrivateLinks/privateEndpointConnectionProxies/write | プライベート エンドポイント接続プロキシを作成または更新します。 |
> | Microsoft.Authorization/policyAssignments/resourceManagementPrivateLinks/privateEndpointConnectionProxies/delete | プライベート エンドポイント接続プロキシを削除します。 |
> | Microsoft.Authorization/policyAssignments/resourceManagementPrivateLinks/privateEndpointConnectionProxies/validate/action | プライベート エンドポイント接続プロキシを検証します。 |
> | Microsoft.Authorization/policyAssignments/resourceManagementPrivateLinks/privateEndpointConnections/read | プライベート エンドポイント接続に関する情報を取得します。 |
> | Microsoft.Authorization/policyAssignments/resourceManagementPrivateLinks/privateEndpointConnections/write | プライベート エンドポイント接続を作成または更新します。 |
> | Microsoft.Authorization/policyAssignments/resourceManagementPrivateLinks/privateEndpointConnections/delete | プライベート エンドポイント接続を削除します。 |
> | Microsoft.Authorization/policyDefinitions/read | ポリシー定義に関する情報を取得します。 |
> | Microsoft.Authorization/policyDefinitions/write | カスタムのポリシー定義を作成します。 |
> | Microsoft.Authorization/policyDefinitions/delete | ポリシー定義を削除します。 |
> | Microsoft.Authorization/policyExemptions/read | ポリシー適用除外についての情報を取得します。 |
> | Microsoft.Authorization/policyExemptions/write | 指定されたスコープでのポリシー適用除外を作成します。 |
> | Microsoft.Authorization/policyExemptions/delete | 指定されたスコープでのポリシー適用除外を削除します。 |
> | Microsoft.Authorization/policySetDefinitions/read | ポリシー セットの定義に関する情報を取得します。 |
> | Microsoft.Authorization/policySetDefinitions/write | カスタム ポリシー セットの定義を作成します。 |
> | Microsoft.Authorization/policySetDefinitions/delete | ポリシー セットの定義を削除します。 |
> | Microsoft.Authorization/providerOperations/read | ロール定義で使用できる、すべてのリソース プロバイダーのための操作を取得します。 |
> | Microsoft.Authorization/roleAssignments/read | ロールの割り当てに関する情報を取得します。 |
> | Microsoft.Authorization/roleAssignments/write | 指定されたスコープのロールの割り当てを作成します。 |
> | Microsoft.Authorization/roleAssignments/delete | 指定したスコープにおけるロールの割り当てを削除します。 |
> | Microsoft.Authorization/roleDefinitions/read | ロール定義に関する情報を取得します。 |
> | Microsoft.Authorization/roleDefinitions/write | 指定されたアクセス許可と割り当て可能なスコープで、カスタムのロール定義を作成または更新します。 |
> | Microsoft.Authorization/roleDefinitions/delete | 指定されたカスタムのロール定義を削除します。 |

### <a name="microsoftautomation"></a>Microsoft.Automation

Azure サービス:[Automation](../automation/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Automation/register/action | Azure Automation にサブスクリプションを登録します。 |
> | Microsoft.Automation/automationAccounts/webhooks/action | Azure Automation Webhook の URI を生成します。 |
> | Microsoft.Automation/automationAccounts/read | Azure Automation アカウントを取得します。 |
> | Microsoft.Automation/automationAccounts/write | Azure Automation アカウントを作成または更新します。 |
> | Microsoft.Automation/automationAccounts/listKeys/action | Automation アカウントのキーを読み取ります |
> | Microsoft.Automation/automationAccounts/delete | Azure Automation アカウントを削除します。 |
> | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | Azure Automation DSC の登録情報を読み取ります。 |
> | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | Azure Automation DSC のキーを再生成する要求を書き込みます。 |
> | Microsoft.Automation/automationAccounts/certificates/getCount/action | 証明書数を読み取ります |
> | Microsoft.Automation/automationAccounts/certificates/read | Azure Automation 証明書資産を取得します。 |
> | Microsoft.Automation/automationAccounts/certificates/write | Azure Automation 証明書資産を作成または更新します。 |
> | Microsoft.Automation/automationAccounts/certificates/delete | Azure Automation 証明書資産を削除します。 |
> | Microsoft.Automation/automationAccounts/compilationjobs/write | Azure Automation DSC のコンパイルを書き込みます。 |
> | Microsoft.Automation/automationAccounts/compilationjobs/read | Azure Automation DSC のコンパイルを読み取ります。 |
> | Microsoft.Automation/automationAccounts/compilationjobs/write | Azure Automation DSC のコンパイルを書き込みます。 |
> | Microsoft.Automation/automationAccounts/compilationjobs/read | Azure Automation DSC のコンパイルを読み取ります。 |
> | Microsoft.Automation/automationAccounts/configurations/read | Azure Automation DSC のコンテンツを取得します。 |
> | Microsoft.Automation/automationAccounts/configurations/getCount/action | Azure Automation DSC のコンテンツの数を読み取ります。 |
> | Microsoft.Automation/automationAccounts/configurations/write | Azure Automation DSC のコンテンツを書き込みます。 |
> | Microsoft.Automation/automationAccounts/configurations/delete | Azure Automation DSC のコンテンツを削除します。 |
> | Microsoft.Automation/automationAccounts/configurations/content/read | 構成メディアの内容を読み取ります |
> | Microsoft.Automation/automationAccounts/connections/read | Azure Automation 接続資産を取得します。 |
> | Microsoft.Automation/automationAccounts/connections/getCount/action | 接続数を読み取ります |
> | Microsoft.Automation/automationAccounts/connections/write | Azure Automation 接続資産を作成または更新します。 |
> | Microsoft.Automation/automationAccounts/connections/delete | Azure Automation 接続資産を削除します。 |
> | Microsoft.Automation/automationAccounts/connectionTypes/read | Azure Automation 接続の種類の資産を取得します。 |
> | Microsoft.Automation/automationAccounts/connectionTypes/write | Azure Automation 接続の種類の資産を作成します。 |
> | Microsoft.Automation/automationAccounts/connectionTypes/delete | Azure Automation 接続の種類の資産を削除します。 |
> | Microsoft.Automation/automationAccounts/credentials/read | Azure Automation 資格情報資産を取得します。 |
> | Microsoft.Automation/automationAccounts/credentials/getCount/action | 資格情報数を読み取ります |
> | Microsoft.Automation/automationAccounts/credentials/write | Azure Automation 資格情報資産を作成または更新します。 |
> | Microsoft.Automation/automationAccounts/credentials/delete | Azure Automation 資格情報資産を削除します。 |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Hybrid Runbook Worker リソースを読み取ります。 |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | Hybrid Runbook Worker リソースを削除します。 |
> | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | ジョブ実行時に Azure Automation Runbook のコンテンツを取得します。 |
> | Microsoft.Automation/automationAccounts/jobs/read | Azure Automation ジョブを取得します。 |
> | Microsoft.Automation/automationAccounts/jobs/write | Azure Automation ジョブを作成します。 |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Azure Automation ジョブを停止します。 |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Azure Automation ジョブを中断します。 |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Azure Automation ジョブを再開します。 |
> | Microsoft.Automation/automationAccounts/jobs/output/read | ジョブの出力を取得します。 |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Azure Automation ジョブ ストリームを取得します。 |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Azure Automation ジョブ ストリームを取得します。 |
> | Microsoft.Automation/automationAccounts/jobSchedules/read | Azure Automation ジョブ スケジュールを取得します。 |
> | Microsoft.Automation/automationAccounts/jobSchedules/write | Azure Automation ジョブ スケジュールを作成します。 |
> | Microsoft.Automation/automationAccounts/jobSchedules/delete | Azure Automation ジョブ スケジュールを削除します。 |
> | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Automation アカウントにリンクされているワークスペースを取得します |
> | Microsoft.Automation/automationAccounts/modules/read | Azure Automation PowerShell モジュールを取得します。 |
> | Microsoft.Automation/automationAccounts/modules/getCount/action | Automation アカウント内の PowerShell モジュール数を取得します。 |
> | Microsoft.Automation/automationAccounts/modules/write | Azure Automation PowerShell モジュールを作成または更新します。 |
> | Microsoft.Automation/automationAccounts/modules/delete | Azure Automation PowerShell モジュールを削除します。 |
> | Microsoft.Automation/automationAccounts/modules/activities/read | Azure Automation のアクティビティを取得します。 |
> | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | Azure Automation DSC のノード構成の内容を読み取ります。 |
> | Microsoft.Automation/automationAccounts/nodeConfigurations/read | Azure Automation DSC のノード構成を読み取ります。 |
> | Microsoft.Automation/automationAccounts/nodeConfigurations/write | Azure Automation DSC のノード構成を書き込みます。 |
> | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | Azure Automation DSC のノード構成を削除します。 |
> | Microsoft.Automation/automationAccounts/nodecounts/read | 指定された種類のノード数の概要を読み取ります |
> | Microsoft.Automation/automationAccounts/nodes/read | Azure Automation DSC のノードを読み取ります。 |
> | Microsoft.Automation/automationAccounts/nodes/write | Azure Automation DSC ノードを作成または更新します。 |
> | Microsoft.Automation/automationAccounts/nodes/delete | Azure Automation DSC のノードを削除します。 |
> | Microsoft.Automation/automationAccounts/nodes/reports/read | Azure Automation DSC のレポートを読み取ります。 |
> | Microsoft.Automation/automationAccounts/nodes/reports/content/read | Azure Automation DSC のレポートの内容を読み取ります |
> | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | Azure Automation の TypeFields を取得します。 |
> | Microsoft.Automation/automationAccounts/privateEndpointConnectionProxies/read | Azure Automation プライベート エンドポイント接続プロキシを読み取ります |
> | Microsoft.Automation/automationAccounts/privateEndpointConnectionProxies/write | Azure Automation プライベート エンドポイント接続プロキシを作成します |
> | Microsoft.Automation/automationAccounts/privateEndpointConnectionProxies/validate/action | プライベート エンドポイント接続要求を検証します (groupId 検証) |
> | Microsoft.Automation/automationAccounts/privateEndpointConnectionProxies/delete | Azure Automation プライベート エンドポイント接続プロキシを削除します |
> | Microsoft.Automation/automationAccounts/privateEndpointConnectionProxies/operationResults/read | Azure Automation プライベート エンドポイント プロキシの操作結果を取得します。 |
> | Microsoft.Automation/automationAccounts/privateEndpointConnections/read | Azure Automation プライベート エンドポイント接続の状態を取得します |
> | Microsoft.Automation/automationAccounts/privateEndpointConnections/read | Azure Automation プライベート エンドポイント接続の状態を取得します |
> | Microsoft.Automation/automationAccounts/privateEndpointConnections/write | Azure Automation プライベート エンドポイント接続を承認または拒否します |
> | Microsoft.Automation/automationAccounts/privateEndpointConnections/delete | Azure Automation プライベート エンドポイント接続を削除します |
> | Microsoft.Automation/automationAccounts/privateLinkResources/read | プライベート エンドポイントのグループ情報を読み取ります |
> | Microsoft.Automation/automationAccounts/privateLinkResources/read | プライベート エンドポイントのグループ情報を読み取ります |
> | Microsoft.Automation/automationAccounts/python2Packages/read | Azure Automation Python 2 パッケージを取得します。 |
> | Microsoft.Automation/automationAccounts/python2Packages/write | Azure Automation Python 2 パッケージを作成または更新します。 |
> | Microsoft.Automation/automationAccounts/python2Packages/delete | Azure Automation Python 2 パッケージを削除します。 |
> | Microsoft.Automation/automationAccounts/python3Packages/read | Azure Automation Python 3 パッケージを取得します。 |
> | Microsoft.Automation/automationAccounts/python3Packages/write | Azure Automation Python 3 パッケージを作成または更新します。 |
> | Microsoft.Automation/automationAccounts/python3Packages/delete | Azure Automation Python 3 パッケージを削除します。 |
> | Microsoft.Automation/automationAccounts/runbooks/read | Azure Automation Runbook を取得します。 |
> | Microsoft.Automation/automationAccounts/runbooks/getCount/action | Azure Automation Runbook 数を取得します。 |
> | Microsoft.Automation/automationAccounts/runbooks/write | Azure Automation Runbook を作成または更新します。 |
> | Microsoft.Automation/automationAccounts/runbooks/delete | Azure Automation Runbook を削除します。 |
> | Microsoft.Automation/automationAccounts/runbooks/publish/action | Azure Automation Runbook の下書きを発行します。 |
> | Microsoft.Automation/automationAccounts/runbooks/content/read | Azure Automation Runbook のコンテンツを取得します。 |
> | Microsoft.Automation/automationAccounts/runbooks/draft/read | Azure Automation Runbook の下書きを取得します。 |
> | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | Azure Automation Runbook の下書きの編集を元に戻します。 |
> | Microsoft.Automation/automationAccounts/runbooks/draft/write | Azure Automation Runbook の下書きを作成します |
> | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | Azure Automation Runbook の下書きのコンテンツを作成します。 |
> | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | Azure Automation Runbook の下書きの操作結果を取得します。 |
> | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | Azure Automation Runbook の下書きのテスト ジョブを取得します。 |
> | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | Azure Automation Runbook の下書きのテスト ジョブを作成します。 |
> | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | Azure Automation Runbook の下書きのテスト ジョブを停止します。 |
> | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | Azure Automation Runbook の下書きのテスト ジョブを中断します。 |
> | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | Azure Automation Runbook の下書きのテスト ジョブを再開します。 |
> | Microsoft.Automation/automationAccounts/schedules/read | Azure Automation スケジュール資産を取得します。 |
> | Microsoft.Automation/automationAccounts/schedules/getCount/action | Azure Automation スケジュール数を取得します。 |
> | Microsoft.Automation/automationAccounts/schedules/write | Azure Automation スケジュール資産を作成または更新します。 |
> | Microsoft.Automation/automationAccounts/schedules/delete | Azure Automation スケジュール資産を削除します。 |
> | Microsoft.Automation/automationAccounts/softwareUpdateConfigurationMachineRuns/read | Azure Automation のソフトウェア更新構成のマシン実行を取得します |
> | Microsoft.Automation/automationAccounts/softwareUpdateConfigurationRuns/read | Azure Automation のソフトウェア更新構成の実行を取得します |
> | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Azure Automation のソフトウェア更新構成を作成または更新します |
> | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Azure Automation のソフトウェア更新構成を取得します |
> | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Azure Automation のソフトウェア更新構成を削除します |
> | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Azure Automation のソフトウェア更新構成を作成または更新します |
> | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Azure Automation のソフトウェア更新構成を取得します |
> | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Azure Automation のソフトウェア更新構成を削除します |
> | Microsoft.Automation/automationAccounts/statistics/read | Azure Automation の統計情報を取得します。 |
> | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | Azure Automation の更新プログラムの展開マシンを取得します。 |
> | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | Azure Automation の更新プログラムの管理パッチ ジョブを取得します。 |
> | Microsoft.Automation/automationAccounts/usages/read | Azure Automation の使用状況を取得します。 |
> | Microsoft.Automation/automationAccounts/variables/read | Azure Automation 変数資産を読み取ります。 |
> | Microsoft.Automation/automationAccounts/variables/write | Azure Automation 変数資産を作成または更新します。 |
> | Microsoft.Automation/automationAccounts/variables/delete | Azure Automation 変数資産を削除します。 |
> | Microsoft.Automation/automationAccounts/watchers/write | Azure Automation Watcher ジョブを作成します。 |
> | Microsoft.Automation/automationAccounts/watchers/read | Azure Automation Watcher ジョブを取得します。 |
> | Microsoft.Automation/automationAccounts/watchers/delete | Azure Automation Watcher ジョブを削除します。 |
> | Microsoft.Automation/automationAccounts/watchers/start/action | Azure Automation Watcher ジョブを開始します。 |
> | Microsoft.Automation/automationAccounts/watchers/stop/action | Azure Automation Watcher ジョブを停止します。 |
> | Microsoft.Automation/automationAccounts/watchers/streams/read | Azure Automation Watcher のジョブ ストリームを取得します。 |
> | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | Azure Automation Watcher のジョブ アクションを作成します。 |
> | Microsoft.Automation/automationAccounts/watchers/watcherActions/read | Azure Automation Watcher のジョブ アクションを取得します。 |
> | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | Azure Automation Watcher のジョブ アクションを削除します。 |
> | Microsoft.Automation/automationAccounts/webhooks/read | Azure Automation Webhook を読み取ります。 |
> | Microsoft.Automation/automationAccounts/webhooks/write | Azure Automation Webhook を作成または更新します。 |
> | Microsoft.Automation/automationAccounts/webhooks/delete | Azure Automation Webhook を削除します。  |
> | Microsoft.Automation/operations/read | Azure Automation リソースの使用可能な操作を取得します。 |

### <a name="microsoftbatch"></a>Microsoft.Batch

Azure サービス:[Batch](../batch/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Batch/register/action | Batch リソース プロバイダーにサブスクリプションを登録し、Batch アカウントを作成できるようにします。 |
> | Microsoft.Batch/unregister/action | Batch リソース プロバイダーのサブスクリプションを登録解除し、Batch アカウントを作成できないようにします。 |
> | Microsoft.Batch/batchAccounts/read | Batch アカウントを一覧表示するか、Batch アカウントのプロパティを取得します。 |
> | Microsoft.Batch/batchAccounts/write | 新しい Batch アカウントを作成するか、既存の Batch アカウントを更新します。 |
> | Microsoft.Batch/batchAccounts/delete | Batch アカウントを削除します。 |
> | Microsoft.Batch/batchAccounts/listkeys/action | Batch アカウントのアクセス キーを一覧表示します。 |
> | Microsoft.Batch/batchAccounts/regeneratekeys/action | Batch アカウントのアクセス キーを再生成します。 |
> | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | Batch アカウント用に構成された自動ストレージ アカウントのアクセス キーを同期します。 |
> | Microsoft.Batch/batchAccounts/applications/read | アプリケーションを一覧表示するか、アプリケーションのプロパティを取得します。 |
> | Microsoft.Batch/batchAccounts/applications/write | 新しいアプリケーションを作成するか、既存のアプリケーションを更新します。 |
> | Microsoft.Batch/batchAccounts/applications/delete | アプリケーションを削除します。 |
> | Microsoft.Batch/batchAccounts/applications/versions/read | アプリケーション パッケージのプロパティを取得します。 |
> | Microsoft.Batch/batchAccounts/applications/versions/write | 新しいアプリケーション パッケージを作成するか、既存のアプリケーション パッケージを更新します。 |
> | Microsoft.Batch/batchAccounts/applications/versions/delete | アプリケーション パッケージを削除します。 |
> | Microsoft.Batch/batchAccounts/applications/versions/activate/action | アプリケーション パッケージをアクティブにします。 |
> | Microsoft.Batch/batchAccounts/certificateOperationResults/read | Batch アカウントでの実行時間の長い証明書操作の結果を取得します。 |
> | Microsoft.Batch/batchAccounts/certificates/read | Batch アカウントの証明書を一覧表示するか、証明書のプロパティを取得します。 |
> | Microsoft.Batch/batchAccounts/certificates/write | Batch アカウントで新しい証明書を作成するか、既存の証明書を更新します。 |
> | Microsoft.Batch/batchAccounts/certificates/delete | Batch アカウントから証明書を削除します。 |
> | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | Batch アカウントでの失敗した証明書削除を取り消します |
> | Microsoft.Batch/batchAccounts/operationResults/read | Batch アカウントの実行時間の長い操作の結果を取得します。 |
> | Microsoft.Batch/batchAccounts/poolOperationResults/read | Batch アカウントでの実行時間の長いプール操作の結果を取得します。 |
> | Microsoft.Batch/batchAccounts/pools/read | Batch アカウントのプールを一覧表示するか、プールのプロパティを取得します。 |
> | Microsoft.Batch/batchAccounts/pools/write | Batch アカウントに新しいプールを作成するか、既存のプールを更新します。 |
> | Microsoft.Batch/batchAccounts/pools/delete | Batch アカウントからプールを削除します。 |
> | Microsoft.Batch/batchAccounts/pools/stopResize/action | Batch アカウント プールで実行中のサイズ変更操作を停止します。 |
> | Microsoft.Batch/batchAccounts/pools/disableAutoscale/action | Batch アカウント プールの自動スケーリングを無効にします。 |
> | Microsoft.Batch/batchAccounts/privateEndpointConnectionResults/read | 長時間実行された Batch アカウントのプライベート エンドポイント接続操作の結果を取得します |
> | Microsoft.Batch/batchAccounts/privateEndpointConnections/write | Batch アカウントの既存のプライベート エンドポイント接続を更新します |
> | Microsoft.Batch/batchAccounts/privateEndpointConnections/read | プライベート エンドポイント接続を取得するか、Batch アカウントのプライベート エンドポイント接続を一覧表示します |
> | Microsoft.Batch/batchAccounts/privateLinkResources/read | プライベート リンク リソースのプロパティを取得するか、Batch アカウントのプライベート リンク リソースを一覧表示します |
> | Microsoft.Batch/locations/checkNameAvailability/action | アカウント名が有効であり、使用されていないことを確認します。 |
> | Microsoft.Batch/locations/accountOperationResults/read | Batch アカウントの実行時間の長い操作の結果を取得します。 |
> | Microsoft.Batch/locations/quotas/read | 指定された Azure リージョンの指定されたサブスクリプションの Batch クォータを取得します。 |
> | Microsoft.Batch/operations/read | Microsoft.Batch リソース プロバイダーで使用できる操作を一覧表示します。 |
> | **DataAction** | **説明** |
> | Microsoft.Batch/batchAccounts/jobs/read | Batch アカウントのジョブを一覧表示するか、ジョブのプロパティを取得します。 |
> | Microsoft.Batch/batchAccounts/jobs/write | Batch アカウントに新しいジョブを作成するか、既存のジョブを更新します。 |
> | Microsoft.Batch/batchAccounts/jobs/delete | Batch アカウントからジョブを削除します。 |
> | Microsoft.Batch/batchAccounts/jobSchedules/read | Batch アカウントのジョブ スケジュールを一覧表示するか、ジョブ スケジュールのプロパティを取得します。 |
> | Microsoft.Batch/batchAccounts/jobSchedules/write | Batch アカウントに新しいジョブ スケジュールを作成するか、既存のジョブ スケジュールを更新します。 |
> | Microsoft.Batch/batchAccounts/jobSchedules/delete | Batch アカウントからジョブ スケジュールを削除します。 |

### <a name="microsoftbilling"></a>Microsoft.Billing

Azure サービス:[コストの管理と請求](../cost-management-billing/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Billing/billingAccounts/read |  |
> | Microsoft.Billing/billingAccounts/listInvoiceSectionsWithCreateSubscriptionPermission/action |  |
> | Microsoft.Billing/billingAccounts/write |  |
> | Microsoft.Billing/billingAccounts/billingProfiles/write |  |
> | Microsoft.Billing/billingAccounts/billingProfiles/read |  |
> | Microsoft.Billing/billingAccounts/billingProfiles/write |  |
> | Microsoft.Billing/billingAccounts/billingProfiles/invoiceSections/write |  |
> | Microsoft.Billing/billingAccounts/billingProfiles/invoiceSections/read |  |
> | Microsoft.Billing/billingAccounts/billingProfiles/invoiceSections/write |  |
> | Microsoft.Billing/billingAccounts/billingProfiles/invoiceSections/validateDeleteInvoiceSectionEligibility/write |  |
> | Microsoft.Billing/billingAccounts/billingProfiles/validateDeleteBillingProfileEligibility/write |  |
> | Microsoft.Billing/billingProperty/write |  |
> | Microsoft.Billing/operations/read |  |

### <a name="microsoftblueprint"></a>Microsoft.Blueprint

Azure サービス:[Azure Blueprint](../governance/blueprints/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Blueprint/register/action | Azure ブループリント リソース プロバイダーを登録します |
> | Microsoft.Blueprint/blueprintAssignments/read | 任意のブループリント アーティファクトを読み取ります |
> | Microsoft.Blueprint/blueprintAssignments/write | 任意のブループリント アーティファクトを作成または更新します |
> | Microsoft.Blueprint/blueprintAssignments/delete | 任意のブループリント アーティファクトを削除します |
> | Microsoft.Blueprint/blueprintAssignments/whoisblueprint/action | Azure Blueprints サービス プリンシパル オブジェクト ID を取得します。 |
> | Microsoft.Blueprint/blueprintAssignments/assignmentOperations/read | 任意のブループリント アーティファクトを読み取ります |
> | Microsoft.Blueprint/blueprints/read | 任意のブループリントを読み取ります |
> | Microsoft.Blueprint/blueprints/write | 任意のブループリントを作成または更新します |
> | Microsoft.Blueprint/blueprints/delete | 任意のブループリントを削除します |
> | Microsoft.Blueprint/blueprints/artifacts/read | 任意のブループリント アーティファクトを読み取ります |
> | Microsoft.Blueprint/blueprints/artifacts/write | 任意のブループリント アーティファクトを作成または更新します |
> | Microsoft.Blueprint/blueprints/artifacts/delete | 任意のブループリント アーティファクトを削除します |
> | Microsoft.Blueprint/blueprints/versions/read | 任意のブループリントを読み取ります |
> | Microsoft.Blueprint/blueprints/versions/write | 任意のブループリントを作成または更新します |
> | Microsoft.Blueprint/blueprints/versions/delete | 任意のブループリントを削除します |
> | Microsoft.Blueprint/blueprints/versions/artifacts/read | 任意のブループリント アーティファクトを読み取ります |

### <a name="microsoftcapacity"></a>Microsoft.Capacity

Azure サービス: コア

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Capacity/calculateprice/action | 予約価格を計算します。 |
> | Microsoft.Capacity/checkoffers/action | サブスクリプション オファーを確認します。 |
> | Microsoft.Capacity/checkscopes/action | サブスクリプションを確認します。 |
> | Microsoft.Capacity/validatereservationorder/action | 予約を検証します。 |
> | Microsoft.Capacity/reservationorders/action | 予約を更新します。 |
> | Microsoft.Capacity/register/action | キャパシティ リソース プロバイダーに登録し、キャパシティ リソースを作成できるようにします。 |
> | Microsoft.Capacity/unregister/action | テナントを登録解除します。 |
> | Microsoft.Capacity/calculateexchange/action | 新しい購入の取引額と価格を計算し、ポリシー エラーを返します。 |
> | Microsoft.Capacity/exchange/action | 任意の予約を交換します |
> | Microsoft.Capacity/listSkus/action | フィルターが適用された、制限なしの SKU を一覧表示します |
> | Microsoft.Capacity/appliedreservations/read | すべての予約を読み取ります。 |
> | Microsoft.Capacity/catalogs/read | 予約のカタログを読み取ります。 |
> | Microsoft.Capacity/commercialreservationorders/read | テナントで作成された予約注文を取得します。 |
> | Microsoft.Capacity/operations/read | 操作を読み取ります。 |
> | Microsoft.Capacity/reservationorders/changedirectory/action | 任意の予約のディレクトリを変更します |
> | Microsoft.Capacity/reservationorders/availablescopes/action | 使用可能なスコープを検索します。 |
> | Microsoft.Capacity/reservationorders/read | すべての予約を読み取ります。 |
> | Microsoft.Capacity/reservationorders/write | 予約を作成します。 |
> | Microsoft.Capacity/reservationorders/delete | 予約を削除します。 |
> | Microsoft.Capacity/reservationorders/reservations/action | 予約を更新します。 |
> | Microsoft.Capacity/reservationorders/return/action | 予約を返します。 |
> | Microsoft.Capacity/reservationorders/swap/action | 予約を入れ替えます。 |
> | Microsoft.Capacity/reservationorders/split/action | 予約を分割します。 |
> | Microsoft.Capacity/reservationorders/merge/action | 予約をマージします。 |
> | Microsoft.Capacity/reservationorders/calculaterefund/action | 新しい購入の払戻額と価格を計算し、ポリシー エラーを返します。 |
> | Microsoft.Capacity/reservationorders/mergeoperationresults/read | マージ操作をポーリングします |
> | Microsoft.Capacity/reservationorders/reservations/availablescopes/action | 使用可能なスコープを検索します。 |
> | Microsoft.Capacity/reservationorders/reservations/read | すべての予約を読み取ります。 |
> | Microsoft.Capacity/reservationorders/reservations/write | 予約を作成します。 |
> | Microsoft.Capacity/reservationorders/reservations/delete | 予約を削除します。 |
> | Microsoft.Capacity/reservationorders/reservations/archive/action | 期限切れや分割などの終了状態にある予約をアーカイブします。 |
> | Microsoft.Capacity/reservationorders/reservations/unarchive/action | 以前にアーカイブされた予約をアーカイブ解除します |
> | Microsoft.Capacity/reservationorders/reservations/revisions/read | すべての予約を読み取ります。 |
> | Microsoft.Capacity/reservationorders/splitoperationresults/read | 分割操作をポーリングします |
> | Microsoft.Capacity/resourceProviders/locations/serviceLimits/read | 指定されたリソースと場所の現在のサービスの制限またはクォータを取得します |
> | Microsoft.Capacity/resourceProviders/locations/serviceLimits/write | 指定されたリソースと場所のサービスの制限またはクォータを作成します |
> | Microsoft.Capacity/resourceProviders/locations/serviceLimitsRequests/read | 指定されたリソースと場所に関するサービスの制限の要求を取得します |
> | Microsoft.Capacity/tenants/register/action | テナントを登録します。 |

### <a name="microsoftcommerce"></a>Microsoft.Commerce

Azure サービス: コア

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Commerce/register/action | Microsoft.Commerce UsageAggregate のサブスクリプションを登録します。 |
> | Microsoft.Commerce/unregister/action | Microsoft.Commerce UsageAggregate のサブスクリプションを登録解除します。 |
> | Microsoft.Commerce/RateCard/read | 特定のサブスクリプションのオファー データ、リソース/測定メタデータ、料金を返します。 |
> | Microsoft.Commerce/UsageAggregates/read | サブスクリプションによる Microsoft Azure の利用状況を取得します。 結果には、特定の時間の範囲における利用状況データ、サブスクリプション、およびリソースに関連する情報の集計が含まれます。 |

### <a name="microsoftconsumption"></a>Microsoft.Consumption

Azure サービス:[Cost Management](../cost-management-billing/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Consumption/register/action | 従量課金プラン RP に登録します |
> | Microsoft.Consumption/aggregatedcost/read | 管理グループの AggregatedCost を一覧表示します。 |
> | Microsoft.Consumption/balances/read | 管理グループの請求期間の使用状況の概要を一覧表示します。 |
> | Microsoft.Consumption/budgets/read | サブスクリプションまたは管理グループごとに予算を一覧表示します。 |
> | Microsoft.Consumption/budgets/write | サブスクリプションまたは管理グループごとに予算を作成および更新します。 |
> | Microsoft.Consumption/budgets/delete | サブスクリプションまたは管理グループごとに予算を削除します。 |
> | Microsoft.Consumption/charges/read | 料金を一覧表示します |
> | Microsoft.Consumption/credits/read | クレジットを一覧表示します |
> | Microsoft.Consumption/events/read | イベントを一覧表示します |
> | Microsoft.Consumption/externalBillingAccounts/tags/read | EA およびサブスクリプションのタグを一覧表示します。 |
> | Microsoft.Consumption/externalSubscriptions/tags/read | EA およびサブスクリプションのタグを一覧表示します。 |
> | Microsoft.Consumption/forecasts/read | 予測を一覧表示します |
> | Microsoft.Consumption/lots/read | ロットを一覧表示します |
> | Microsoft.Consumption/marketplaces/read | EA および WebDirect サブスクリプションのスコープに対する Marketplace リソース使用状況の詳細を一覧表示します。 |
> | Microsoft.Consumption/operationresults/read | 操作結果を一覧表示します |
> | Microsoft.Consumption/operations/read | Microsoft.Consumption リソース プロバイダーによってサポートされるすべての操作を一覧表示します。 |
> | Microsoft.Consumption/operationstatus/read | 操作の状態を一覧表示します |
> | Microsoft.Consumption/pricesheets/read | サブスクリプションまたは管理グループの価格シート データを一覧表示します。 |
> | Microsoft.Consumption/reservationDetails/read | 予約注文または管理グループごとに予約インスタンスの使用状況の詳細を一覧表示します。 詳細データは、インスタンス別の日レベル別です。 |
> | Microsoft.Consumption/reservationRecommendations/read | サブスクリプションの予約インスタンスに関する 1 つのレコメンデーションまたは共有のレコメンデーションの一覧を取得します。 |
> | Microsoft.Consumption/reservationSummaries/read | 予約注文または管理グループごとに予約インスタンスの使用状況の概要を一覧表示します。 概要データは、月レベル別または日レベル別です。 |
> | Microsoft.Consumption/reservationTransactions/read | 管理グループ別に予約インスタンスのトランザクション履歴を一覧表示します。 |
> | Microsoft.Consumption/tags/read | EA およびサブスクリプションのタグを一覧表示します。 |
> | Microsoft.Consumption/tenants/register/action | Microsoft.Consumption のスコープのアクションをテナントごとに登録します。 |
> | Microsoft.Consumption/tenants/read | テナントを一覧表示します。 |
> | Microsoft.Consumption/terms/read | サブスクリプションまたは管理グループの条件を一覧表示します。 |
> | Microsoft.Consumption/usageDetails/read | EA および WebDirect サブスクリプションのスコープに対する使用状況の詳細を一覧表示します。 |

### <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

Azure サービス:[Cost Management](../cost-management-billing/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.CostManagement/query/action | スコープ別に使用状況データをクエリをします。 |
> | Microsoft.CostManagement/reports/action | スコープ別の使用状況データのレポートをスケジュールします。 |
> | Microsoft.CostManagement/exports/action | 指定されたエクスポートを実行します。 |
> | Microsoft.CostManagement/register/action | Microsoft.CostManagement のスコープに対するアクションをサブスクリプションごとに登録します。 |
> | Microsoft.CostManagement/views/action | ビューを作成します。 |
> | Microsoft.CostManagement/forecast/action | スコープ別に使用状況データを予測します。 |
> | Microsoft.CostManagement/alerts/write | アラートを更新する。 |
> | Microsoft.CostManagement/alerts/read | アラートを一覧表示します。 |
> | Microsoft.CostManagement/budgets/read | サブスクリプションまたは管理グループごとに予算を一覧表示します。 |
> | Microsoft.CostManagement/cloudConnectors/read | 認証済みユーザーの cloudConnector を一覧表示します。 |
> | Microsoft.CostManagement/cloudConnectors/write | 指定された cloudConnector を作成または更新します。 |
> | Microsoft.CostManagement/cloudConnectors/delete | 指定された cloudConnector を削除します。 |
> | Microsoft.CostManagement/dimensions/read | サポートされているすべてのディメンションをスコープ別に一覧表示します。 |
> | Microsoft.CostManagement/exports/read | エクスポートをスコープ別に一覧表示します。 |
> | Microsoft.CostManagement/exports/write | 指定されたエクスポートを作成または更新します。 |
> | Microsoft.CostManagement/exports/delete | 指定されたエクスポートを削除します。 |
> | Microsoft.CostManagement/exports/run/action | エクスポートを実行します。 |
> | Microsoft.CostManagement/externalBillingAccounts/read | 認証済みユーザーの externalBillingAccount を一覧表示します。 |
> | Microsoft.CostManagement/externalBillingAccounts/query/action | 外部 BillingAccounts の使用状況データをクエリします。 |
> | Microsoft.CostManagement/externalBillingAccounts/forecast/action | 外部 BillingAccounts の使用状況データを予測します。 |
> | Microsoft.CostManagement/externalBillingAccounts/dimensions/read | 外部 BillingAccounts でサポートされているディメンションを一覧表示します。 |
> | Microsoft.CostManagement/externalBillingAccounts/externalSubscriptions/read | 認証済みユーザーの externalBillingAccount 内の externalSubscription を一覧表示します。 |
> | Microsoft.CostManagement/externalBillingAccounts/forecast/read | 外部 BillingAccounts の使用状況データを予測します。 |
> | Microsoft.CostManagement/externalBillingAccounts/query/read | 外部 BillingAccounts の使用状況データをクエリします。 |
> | Microsoft.CostManagement/externalSubscriptions/read | 認証済みユーザーの externalSubscription を一覧表示します。 |
> | Microsoft.CostManagement/externalSubscriptions/write | externalSubscription の関連付けられている管理グループを更新します |
> | Microsoft.CostManagement/externalSubscriptions/query/action | 外部サブスクリプションの使用状況データをクエリします。 |
> | Microsoft.CostManagement/externalSubscriptions/forecast/action | 外部 BillingAccounts の使用状況データを予測します。 |
> | Microsoft.CostManagement/externalSubscriptions/dimensions/read | 外部サブスクリプションでサポートされているディメンションを一覧表示します。 |
> | Microsoft.CostManagement/externalSubscriptions/forecast/read | 外部 BillingAccounts の使用状況データを予測します。 |
> | Microsoft.CostManagement/externalSubscriptions/query/read | 外部サブスクリプションの使用状況データをクエリします。 |
> | Microsoft.CostManagement/forecast/read | スコープ別に使用状況データを予測します。 |
> | Microsoft.CostManagement/operations/read | Microsoft.CostManagement リソース プロバイダーによってサポートされるすべての操作を一覧表示します。 |
> | Microsoft.CostManagement/query/read | スコープ別に使用状況データをクエリをします。 |
> | Microsoft.CostManagement/reports/read | スコープ別の使用状況データのレポートをスケジュールします。 |
> | Microsoft.CostManagement/tenants/register/action | Microsoft.CostManagement のスコープに対するアクションをテナントごとに登録します。 |
> | Microsoft.CostManagement/views/read | 保存されているすべてのビューを一覧表示します。 |
> | Microsoft.CostManagement/views/delete | 保存されたビューを削除します。 |
> | Microsoft.CostManagement/views/write | ビューを更新します。 |

### <a name="microsoftfeatures"></a>Microsoft.Features

Azure サービス:[Azure Resource Manager](../azure-resource-manager/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Features/register/action | サブスクリプションの機能を登録します。 |
> | Microsoft.Features/featureProviders/subscriptionFeatureRegistrations/read | 指定されたリソース プロバイダーのサブスクリプションの機能の登録を取得します。 |
> | Microsoft.Features/featureProviders/subscriptionFeatureRegistrations/write | 指定されたリソース プロバイダーのサブスクリプションの機能の登録を追加します。 |
> | Microsoft.Features/featureProviders/subscriptionFeatureRegistrations/delete | 指定されたリソース プロバイダーのサブスクリプションの機能の登録を削除します。 |
> | Microsoft.Features/features/read | サブスクリプションの機能を取得します。 |
> | Microsoft.Features/operations/read | 操作の一覧を取得します。 |
> | Microsoft.Features/providers/features/read | 指定されたリソース プロバイダーのサブスクリプションの機能を取得します。 |
> | Microsoft.Features/providers/features/register/action | 指定されたリソース プロバイダーのサブスクリプションの機能を登録します。 |
> | Microsoft.Features/providers/features/unregister/action | 指定されたリソース プロバイダーのサブスクリプションの機能を登録解除します。 |
> | Microsoft.Features/subscriptionFeatureRegistrations/read | サブスクリプションの機能の登録を取得します。 |

### <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

Azure サービス:[Azure Policy](../governance/policy/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.GuestConfiguration/register/action | Microsoft.GuestConfiguration リソース プロバイダーのサブスクリプションを登録します。 |
> | Microsoft.GuestConfiguration/guestConfigurationAssignments/write | 新しいゲスト構成の割り当てを作成します。 |
> | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | ゲスト構成の割り当てを取得します。 |
> | Microsoft.GuestConfiguration/guestConfigurationAssignments/delete | ゲスト構成の割り当てを削除します。 |
> | Microsoft.GuestConfiguration/guestConfigurationAssignments/reports/read | ゲスト構成の割り当てレポートを取得します。 |
> | Microsoft.GuestConfiguration/operations/read | Microsoft.GuestConfiguration リソース プロバイダーの操作を取得します |

### <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

Azure サービス:[Azure Arc](../azure-arc/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.HybridCompute/register/action | Microsoft.HybridCompute リソース プロバイダーに対するサブスクリプションが登録されます |
> | Microsoft.HybridCompute/unregister/action | Microsoft.HybridCompute リソース プロバイダーに対するサブスクリプションが登録解除されます |
> | Microsoft.HybridCompute/locations/operationresults/read | Microsoft.HybridCompute リソース プロバイダーでの操作の状態が読み取られます |
> | Microsoft.HybridCompute/locations/operationstatus/read | Microsoft.HybridCompute リソース プロバイダーでの操作の状態が読み取られます |
> | Microsoft.HybridCompute/locations/updateCenterOperationResults/read | マシン上の更新センター操作の状態を読み取ります |
> | Microsoft.HybridCompute/machines/read | Azure Arc マシンを読み取ります |
> | Microsoft.HybridCompute/machines/write | Azure Arc マシンを書き込みます |
> | Microsoft.HybridCompute/machines/delete | Azure Arc マシンが削除されます |
> | Microsoft.HybridCompute/machines/assessPatches/action | Azure Arc マシンを評価して、不足しているソフトウェア パッチを取得します |
> | Microsoft.HybridCompute/machines/installPatches/action | Azure Arc マシンにパッチをインストールします |
> | Microsoft.HybridCompute/machines/extensions/read | Azure Arc 拡張機能が読み取られます |
> | Microsoft.HybridCompute/machines/extensions/write | Azure Arc 拡張機能をインストールまたは更新されます |
> | Microsoft.HybridCompute/machines/extensions/delete | Azure Arc 拡張機能が削除されます |
> | Microsoft.HybridCompute/machines/patchAssessmentResults/read | Azure Arc patchAssessmentResults を読み取ります |
> | Microsoft.HybridCompute/machines/patchAssessmentResults/softwarePatches/read | Azure Arc patchAssessmentResults/softwarePatches を読み取ります |
> | Microsoft.HybridCompute/machines/patchInstallationResults/read | Azure Arc patchInstallationResults を読み取ります |
> | Microsoft.HybridCompute/machines/patchInstallationResults/softwarePatches/read | Azure Arc patchInstallationResults/softwarePatches を読み取ります |
> | Microsoft.HybridCompute/operations/read | Azure Arc for servers に対するすべての操作が読み取られます |

### <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

Azure サービス:[Azure Arc 対応 Kubernetes](../azure-arc/kubernetes/overview.md)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Kubernetes/register/action | Microsoft.Kubernetes リソース プロバイダーにサブスクリプションを登録します |
> | Microsoft.Kubernetes/unregister/action | Microsoft.Kubernetes リソース プロバイダーからサブスクリプションを登録解除します |
> | Microsoft.Kubernetes/connectedClusters/Read | connectedClusters を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/Write | connectedClusters を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/Delete | connectedClusters を削除します |
> | Microsoft.Kubernetes/connectedClusters/listClusterUserCredentials/action | clusterUser 資格情報を一覧表示します |
> | Microsoft.Kubernetes/locations/operationstatuses/read | 操作状態を読み取ります |
> | Microsoft.Kubernetes/locations/operationstatuses/write | 操作の状態を書き込みます |
> | Microsoft.Kubernetes/operations/read | Microsoft.Kubernetes リソース プロバイダーで使用できる操作を一覧表示します |
> | Microsoft.Kubernetes/RegisteredSubscriptions/read | 登録済みサブスクリプションを読み取ります |
> | **DataAction** | **説明** |
> | Microsoft.Kubernetes/connectedClusters/admissionregistration.k8s.io/initializerconfigurations/read | initializerconfigurations を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/admissionregistration.k8s.io/initializerconfigurations/write | initializerconfigurations を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/admissionregistration.k8s.io/initializerconfigurations/delete | initializerconfigurations を削除します |
> | Microsoft.Kubernetes/connectedClusters/admissionregistration.k8s.io/mutatingwebhookconfigurations/read | mutatingwebhookconfigurations を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/admissionregistration.k8s.io/mutatingwebhookconfigurations/write | mutatingwebhookconfigurations を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/admissionregistration.k8s.io/mutatingwebhookconfigurations/delete | mutatingwebhookconfigurations を削除します |
> | Microsoft.Kubernetes/connectedClusters/admissionregistration.k8s.io/validatingwebhookconfigurations/read | validatingwebhookconfigurations を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/admissionregistration.k8s.io/validatingwebhookconfigurations/write | validatingwebhookconfigurations を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/admissionregistration.k8s.io/validatingwebhookconfigurations/delete | validatingwebhookconfigurations を削除します |
> | Microsoft.Kubernetes/connectedClusters/api/read | api を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/api/v1/read | api/v1 を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apiextensions.k8s.io/customresourcedefinitions/read | customresourcedefinitions を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apiextensions.k8s.io/customresourcedefinitions/write | customresourcedefinitions を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/apiextensions.k8s.io/customresourcedefinitions/delete | customresourcedefinitions を削除します |
> | Microsoft.Kubernetes/connectedClusters/apiregistration.k8s.io/apiservices/read | apiservices を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apiregistration.k8s.io/apiservices/write | apiservices を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/apiregistration.k8s.io/apiservices/delete | apiservices を削除します |
> | Microsoft.Kubernetes/connectedClusters/apis/read | apis を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/admissionregistration.k8s.io/read | admissionregistration.k8s.io を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/admissionregistration.k8s.io/v1/read | admissionregistration.k8s.io/v1 を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/admissionregistration.k8s.io/v1beta1/read | admissionregistration.k8s.io/v1beta1 を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/apiextensions.k8s.io/read | apiextensions.k8s.io を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/apiextensions.k8s.io/v1/read | apiextensions.k8s.io/v1 を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/apiextensions.k8s.io/v1beta1/read | apiextensions.k8s.io/v1beta1 を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/apiregistration.k8s.io/read | apiregistration.k8s.io を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/apiregistration.k8s.io/v1/read | apiregistration.k8s.io/v1 を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/apiregistration.k8s.io/v1beta1/read | apiregistration.k8s.io/v1beta1 を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/apps/read | apps を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/apps/v1beta1/read | apps/v1beta1 を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/apps/v1beta2/read | v1beta2 を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/authentication.k8s.io/read | authentication.k8s.io を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/authentication.k8s.io/v1/read | authentication.k8s.io/v1 を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/authentication.k8s.io/v1beta1/read | authentication.k8s.io/v1beta1 を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/authorization.k8s.io/read | authorization.k8s.io を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/authorization.k8s.io/v1/read | authorization.k8s.io/v1 を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/authorization.k8s.io/v1beta1/read | authorization.k8s.io/v1beta1 を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/autoscaling/read | 自動スケーリングを読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/autoscaling/v1/read | autoscaling/v1 を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/autoscaling/v2beta1/read | autoscaling/v2beta1 を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/autoscaling/v2beta2/read | autoscaling/v2beta2 を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/batch/read | バッチを読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/batch/v1/read | batch/v1 を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/batch/v1beta1/read | batch/v1beta1 を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/certificates.k8s.io/read | certificates.k8s.io を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/certificates.k8s.io/v1beta1/read | certificates.k8s.io/v1beta1 を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/coordination.k8s.io/read | coordination.k8s.io を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/coordination.k8s.io/v1/read | coordination/v1 を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/coordination.k8s.io/v1beta1/read | coordination.k8s.io/v1beta1 を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/events.k8s.io/read | events.k8s.io を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/events.k8s.io/v1beta1/read | events.k8s.io/v1beta1 を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/extensions/read | 拡張機能を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/extensions/v1beta1/read | extensions/v1beta1 を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/metrics.k8s.io/read | metrics.k8s.io を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/metrics.k8s.io/v1beta1/read | metrics.k8s.io/v1beta1 を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/networking.k8s.io/read | networking.k8s.io を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/networking.k8s.io/v1/read | networking/v1 を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/networking.k8s.io/v1beta1/read | networking.k8s.io/v1beta1 を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/node.k8s.io/read | node.k8s.io を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/node.k8s.io/v1beta1/read | node.k8s.io/v1beta1 を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/policy/read | ポリシーを読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/policy/v1beta1/read | policy/v1beta1 を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/rbac.authorization.k8s.io/read | rbac.authorization.k8s.io を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/rbac.authorization.k8s.io/v1/read | bac.authorization/v1 を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/rbac.authorization.k8s.io/v1beta1/read | rbac.authorization.k8s.io/v1beta1 を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/scheduling.k8s.io/read | scheduling.k8s.io を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/scheduling.k8s.io/v1/read | scheduling/v1 を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/scheduling.k8s.io/v1beta1/read | scheduling.k8s.io/v1beta1 を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/storage.k8s.io/read | storage.k8s.io を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/storage.k8s.io/v1/read | storage/v1 を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apis/storage.k8s.io/v1beta1/read | storage.k8s.io/v1beta1 を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apps/controllerrevisions/read | controllerrevisions を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apps/controllerrevisions/write | controllerrevisions を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/apps/controllerrevisions/delete | controllerrevisions を削除します |
> | Microsoft.Kubernetes/connectedClusters/apps/daemonsets/read | デーモンセットを読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apps/daemonsets/write | デーモンセットを書き込みます |
> | Microsoft.Kubernetes/connectedClusters/apps/daemonsets/delete | デーモンセットを削除します |
> | Microsoft.Kubernetes/connectedClusters/apps/deployments/read | デプロイを読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apps/deployments/write | デプロイを書き込みます |
> | Microsoft.Kubernetes/connectedClusters/apps/deployments/delete | デプロイを削除します |
> | Microsoft.Kubernetes/connectedClusters/apps/replicasets/read | レプリカセットを読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apps/replicasets/write | レプリカセットを書き込みます |
> | Microsoft.Kubernetes/connectedClusters/apps/replicasets/delete | レプリカセットを削除します |
> | Microsoft.Kubernetes/connectedClusters/apps/statefulsets/read | ステートフルセットを読み取ります |
> | Microsoft.Kubernetes/connectedClusters/apps/statefulsets/write | ステートフルセットを書き込みます |
> | Microsoft.Kubernetes/connectedClusters/apps/statefulsets/delete | ステートフルセットを削除します |
> | Microsoft.Kubernetes/connectedClusters/authentication.k8s.io/tokenreviews/write | tokenreviews を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/authentication.k8s.io/userextras/impersonate/action | userextras を偽装します |
> | Microsoft.Kubernetes/connectedClusters/authorization.k8s.io/localsubjectaccessreviews/write | localsubjectaccessreviews を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/authorization.k8s.io/selfsubjectaccessreviews/write | selfsubjectaccessreviews を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/authorization.k8s.io/selfsubjectrulesreviews/write | selfsubjectrulesreviews を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/authorization.k8s.io/subjectaccessreviews/write | subjectaccessreviews を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/autoscaling/horizontalpodautoscalers/read | horizontalpodautoscalers を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/autoscaling/horizontalpodautoscalers/write | horizontalpodautoscalers を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/autoscaling/horizontalpodautoscalers/delete | horizontalpodautoscalers を削除します |
> | Microsoft.Kubernetes/connectedClusters/batch/cronjobs/read | cronjobs を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/batch/cronjobs/write | cronjobs を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/batch/cronjobs/delete | cronjobs を削除します |
> | Microsoft.Kubernetes/connectedClusters/batch/jobs/read | ジョブを読み取ります |
> | Microsoft.Kubernetes/connectedClusters/batch/jobs/write | ジョブを書き込みます |
> | Microsoft.Kubernetes/connectedClusters/batch/jobs/delete | ジョブを削除します |
> | Microsoft.Kubernetes/connectedClusters/bindings/write | バインディングを書き込みます |
> | Microsoft.Kubernetes/connectedClusters/certificates.k8s.io/certificatesigningrequests/read | certificatesigningrequests を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/certificates.k8s.io/certificatesigningrequests/write | certificatesigningrequests を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/certificates.k8s.io/certificatesigningrequests/delete | certificatesigningrequests を削除します |
> | Microsoft.Kubernetes/connectedClusters/clusterconfig.azure.com/azureclusteridentityrequests/read | azureclusteridentityrequests を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/clusterconfig.azure.com/azureclusteridentityrequests/write | azureclusteridentityrequests を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/clusterconfig.azure.com/azureclusteridentityrequests/delete | azureclusteridentityrequests を削除します |
> | Microsoft.Kubernetes/connectedClusters/componentstatuses/read | componentstatuses を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/componentstatuses/write | componentstatuses を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/componentstatuses/delete | componentstatuses を削除します |
> | Microsoft.Kubernetes/connectedClusters/configmaps/read | configmaps を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/configmaps/write | configmaps を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/configmaps/delete | configmaps を削除します |
> | Microsoft.Kubernetes/connectedClusters/coordination.k8s.io/leases/read | リースを読み取ります |
> | Microsoft.Kubernetes/connectedClusters/coordination.k8s.io/leases/write | リースを書き込みます |
> | Microsoft.Kubernetes/connectedClusters/coordination.k8s.io/leases/delete | リースを削除します |
> | Microsoft.Kubernetes/connectedClusters/endpoints/read | エンドポイントを読み取ります |
> | Microsoft.Kubernetes/connectedClusters/endpoints/write | エンドポイントを書き込みます |
> | Microsoft.Kubernetes/connectedClusters/endpoints/delete | エンドポイントを削除します |
> | Microsoft.Kubernetes/connectedClusters/events/read | イベントを読み取ります |
> | Microsoft.Kubernetes/connectedClusters/events/write | イベントを書き込みます |
> | Microsoft.Kubernetes/connectedClusters/events/delete | イベントを削除します |
> | Microsoft.Kubernetes/connectedClusters/events.k8s.io/events/read | イベントを読み取ります |
> | Microsoft.Kubernetes/connectedClusters/events.k8s.io/events/write | イベントを書き込みます |
> | Microsoft.Kubernetes/connectedClusters/events.k8s.io/events/delete | イベントを削除します |
> | Microsoft.Kubernetes/connectedClusters/extensions/daemonsets/read | デーモンセットを読み取ります |
> | Microsoft.Kubernetes/connectedClusters/extensions/daemonsets/write | デーモンセットを書き込みます |
> | Microsoft.Kubernetes/connectedClusters/extensions/daemonsets/delete | デーモンセットを削除します |
> | Microsoft.Kubernetes/connectedClusters/extensions/deployments/read | デプロイを読み取ります |
> | Microsoft.Kubernetes/connectedClusters/extensions/deployments/write | デプロイを書き込みます |
> | Microsoft.Kubernetes/connectedClusters/extensions/deployments/delete | デプロイを削除します |
> | Microsoft.Kubernetes/connectedClusters/extensions/ingresses/read | イングレスを読み取ります |
> | Microsoft.Kubernetes/connectedClusters/extensions/ingresses/write | イングレスを書き込みます |
> | Microsoft.Kubernetes/connectedClusters/extensions/ingresses/delete | イングレスを削除します |
> | Microsoft.Kubernetes/connectedClusters/extensions/networkpolicies/read | networkpolicies を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/extensions/networkpolicies/write | networkpolicies を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/extensions/networkpolicies/delete | networkpolicies を削除します |
> | Microsoft.Kubernetes/connectedClusters/extensions/podsecuritypolicies/read | podsecuritypolicies を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/extensions/podsecuritypolicies/write | podsecuritypolicies を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/extensions/podsecuritypolicies/delete | podsecuritypolicies を削除します |
> | Microsoft.Kubernetes/connectedClusters/extensions/replicasets/read | レプリカセットを読み取ります |
> | Microsoft.Kubernetes/connectedClusters/extensions/replicasets/write | レプリカセットを書き込みます |
> | Microsoft.Kubernetes/connectedClusters/extensions/replicasets/delete | レプリカセットを削除します |
> | Microsoft.Kubernetes/connectedClusters/groups/impersonate/action | グループを偽装します |
> | Microsoft.Kubernetes/connectedClusters/healthz/read | healthz を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/healthz/autoregister-completion/read | autoregister-completion を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/healthz/etcd/read | etcd を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/healthz/log/read | ログを読み取ります |
> | Microsoft.Kubernetes/connectedClusters/healthz/ping/read | ping を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/healthz/poststarthook/apiservice-openapi-controller/read | apiservice-openapi-controller を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/healthz/poststarthook/apiservice-registration-controller/read | apiservice-registration-controller を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/healthz/poststarthook/apiservice-status-available-controller/read | apiservice-status-available-controller を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/healthz/poststarthook/bootstrap-controller/read | bootstrap-controller を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/healthz/poststarthook/ca-registration/read | ca-registration を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/healthz/poststarthook/crd-informer-synced/read | crd-informer-synced を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/healthz/poststarthook/generic-apiserver-start-informers/read | generic-apiserver-start-informers を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/healthz/poststarthook/kube-apiserver-autoregistration/read | kube-apiserver-autoregistration を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/healthz/poststarthook/rbac/bootstrap-roles/read | bootstrap-roles を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/healthz/poststarthook/scheduling/bootstrap-system-priority-classes/read | bootstrap-system-priority-classes を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/healthz/poststarthook/start-apiextensions-controllers/read | start-apiextensions-controllers を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/healthz/poststarthook/start-apiextensions-informers/read | start-apiextensions-informers を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/healthz/poststarthook/start-kube-aggregator-informers/read | start-kube-aggregator-informers を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/healthz/poststarthook/start-kube-apiserver-admission-initializer/read | start-kube-apiserver-admission-initializer を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/limitranges/read | limitranges を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/limitranges/write | limitranges を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/limitranges/delete | limitranges を削除します |
> | Microsoft.Kubernetes/connectedClusters/livez/read | livez を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/livez/autoregister-completion/read | autoregister-completion を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/livez/etcd/read | etcd を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/livez/log/read | ログを読み取ります |
> | Microsoft.Kubernetes/connectedClusters/livez/ping/read | ping を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/livez/poststarthook/apiservice-openapi-controller/read | apiservice-openapi-controller を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/livez/poststarthook/apiservice-registration-controller/read | apiservice-registration-controller を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/livez/poststarthook/apiservice-status-available-controller/read | apiservice-status-available-controller を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/livez/poststarthook/bootstrap-controller/read | bootstrap-controller を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/livez/poststarthook/ca-registration/read | ca-registration を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/livez/poststarthook/crd-informer-synced/read | crd-informer-synced を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/livez/poststarthook/generic-apiserver-start-informers/read | generic-apiserver-start-informers を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/livez/poststarthook/kube-apiserver-autoregistration/read | kube-apiserver-autoregistration を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/livez/poststarthook/rbac/bootstrap-roles/read | bootstrap-roles を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/livez/poststarthook/scheduling/bootstrap-system-priority-classes/read | bootstrap-system-priority-classes を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/livez/poststarthook/start-apiextensions-controllers/read | start-apiextensions-controllers を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/livez/poststarthook/start-apiextensions-informers/read | start-apiextensions-informers を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/livez/poststarthook/start-kube-aggregator-informers/read | start-kube-aggregator-informers を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/livez/poststarthook/start-kube-apiserver-admission-initializer/read | start-kube-apiserver-admission-initializer を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/logs/read | ログを読み取ります |
> | Microsoft.Kubernetes/connectedClusters/metrics/read | メトリックを読み取ります |
> | Microsoft.Kubernetes/connectedClusters/metrics.k8s.io/nodes/read | ノードを読み取ります |
> | Microsoft.Kubernetes/connectedClusters/metrics.k8s.io/pods/read | ポッドを読み取ります |
> | Microsoft.Kubernetes/connectedClusters/namespaces/read | 名前空間を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/namespaces/write | namespaces を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/namespaces/delete | 名前空間を削除します |
> | Microsoft.Kubernetes/connectedClusters/networking.k8s.io/ingresses/read | イングレスを読み取ります |
> | Microsoft.Kubernetes/connectedClusters/networking.k8s.io/ingresses/write | イングレスを書き込みます |
> | Microsoft.Kubernetes/connectedClusters/networking.k8s.io/ingresses/delete | イングレスを削除します |
> | Microsoft.Kubernetes/connectedClusters/networking.k8s.io/networkpolicies/read | networkpolicies を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/networking.k8s.io/networkpolicies/write | networkpolicies を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/networking.k8s.io/networkpolicies/delete | networkpolicies を削除します |
> | Microsoft.Kubernetes/connectedClusters/node.k8s.io/runtimeclasses/read | runtimeclasses を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/node.k8s.io/runtimeclasses/write | runtimeclasses を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/node.k8s.io/runtimeclasses/delete | runtimeclasses を削除します |
> | Microsoft.Kubernetes/connectedClusters/nodes/read | ノードを読み取ります |
> | Microsoft.Kubernetes/connectedClusters/nodes/write | ノードを書き込みます |
> | Microsoft.Kubernetes/connectedClusters/nodes/delete | ノードを削除します |
> | Microsoft.Kubernetes/connectedClusters/openapi/v2/read | v2 を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/persistentvolumeclaims/read | persistentvolumeclaims を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/persistentvolumeclaims/write | persistentvolumeclaims を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/persistentvolumeclaims/delete | persistentvolumeclaims を削除します |
> | Microsoft.Kubernetes/connectedClusters/persistentvolumes/read | persistentvolumes を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/persistentvolumes/write | persistentvolumes を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/persistentvolumes/delete | persistentvolumes を削除します |
> | Microsoft.Kubernetes/connectedClusters/pods/read | ポッドを読み取ります |
> | Microsoft.Kubernetes/connectedClusters/pods/write | ポッドを書き込みます |
> | Microsoft.Kubernetes/connectedClusters/pods/delete | ポッドを削除します |
> | Microsoft.Kubernetes/connectedClusters/podtemplates/read | podtemplates を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/podtemplates/write | podtemplates を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/podtemplates/delete | podtemplates を削除します |
> | Microsoft.Kubernetes/connectedClusters/policy/poddisruptionbudgets/read | poddisruptionbudgets を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/policy/poddisruptionbudgets/write | poddisruptionbudgets を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/policy/poddisruptionbudgets/delete | poddisruptionbudgets を削除します |
> | Microsoft.Kubernetes/connectedClusters/policy/podsecuritypolicies/read | podsecuritypolicies を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/policy/podsecuritypolicies/write | podsecuritypolicies を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/policy/podsecuritypolicies/delete | podsecuritypolicies を削除します |
> | Microsoft.Kubernetes/connectedClusters/policy/podsecuritypolicies/use/action | podsecuritypolicies でアクションを使用します |
> | Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/clusterrolebindings/read | clusterrolebindings を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/clusterrolebindings/write | clusterrolebindings を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/clusterrolebindings/delete | clusterrolebindings を削除します |
> | Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/clusterroles/read | clusterroles を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/clusterroles/write | clusterroles を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/clusterroles/delete | clusterroles を削除します |
> | Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/clusterroles/bind/action | clusterroles をバインドします |
> | Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/clusterroles/escalate/action | エスカレーションします |
> | Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/rolebindings/read | rolebindings を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/rolebindings/write | rolebindings を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/rolebindings/delete | rolebindings を削除します |
> | Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/roles/read | ロールを読み取ります |
> | Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/roles/write | ロールを書き込みます |
> | Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/roles/delete | ロールを削除します |
> | Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/roles/bind/action | ロールをバインドします |
> | Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/roles/escalate/action | ロールをエスカレーションします |
> | Microsoft.Kubernetes/connectedClusters/readyz/read | readyz を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/readyz/autoregister-completion/read | autoregister-completion を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/readyz/etcd/read | etcd を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/readyz/log/read | ログを読み取ります |
> | Microsoft.Kubernetes/connectedClusters/readyz/ping/read | ping を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/readyz/poststarthook/apiservice-openapi-controller/read | apiservice-openapi-controller を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/readyz/poststarthook/apiservice-registration-controller/read | apiservice-registration-controller を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/readyz/poststarthook/apiservice-status-available-controller/read | apiservice-status-available-controller を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/readyz/poststarthook/bootstrap-controller/read | bootstrap-controller を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/readyz/poststarthook/ca-registration/read | ca-registration を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/readyz/poststarthook/crd-informer-synced/read | crd-informer-synced を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/readyz/poststarthook/generic-apiserver-start-informers/read | generic-apiserver-start-informers を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/readyz/poststarthook/kube-apiserver-autoregistration/read | kube-apiserver-autoregistration を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/readyz/poststarthook/rbac/bootstrap-roles/read | bootstrap-roles を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/readyz/poststarthook/scheduling/bootstrap-system-priority-classes/read | bootstrap-system-priority-classes を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/readyz/poststarthook/start-apiextensions-controllers/read | start-apiextensions-controllers を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/readyz/poststarthook/start-apiextensions-informers/read | start-apiextensions-informers を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/readyz/poststarthook/start-kube-aggregator-informers/read | start-kube-aggregator-informers を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/readyz/poststarthook/start-kube-apiserver-admission-initializer/read | start-kube-apiserver-admission-initializer を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/readyz/shutdown/read | shutdown を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/replicationcontrollers/read | replicationcontrollers を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/replicationcontrollers/write | replicationcontrollers を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/replicationcontrollers/delete | replicationcontrollers を削除します |
> | Microsoft.Kubernetes/connectedClusters/resetMetrics/read | resetMetrics を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/resourcequotas/read | resourcequotas を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/resourcequotas/write | resourcequotas を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/resourcequotas/delete | resourcequotas を削除します |
> | Microsoft.Kubernetes/connectedClusters/scheduling.k8s.io/priorityclasses/read | priorityclasses を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/scheduling.k8s.io/priorityclasses/write | priorityclasses を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/scheduling.k8s.io/priorityclasses/delete | priorityclasses を削除します |
> | Microsoft.Kubernetes/connectedClusters/secrets/read | シークレットを読み取ります |
> | Microsoft.Kubernetes/connectedClusters/secrets/write | シークレットを書き込みます |
> | Microsoft.Kubernetes/connectedClusters/secrets/delete | シークレットを削除します |
> | Microsoft.Kubernetes/connectedClusters/serviceaccounts/read | serviceaccounts を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/serviceaccounts/write | serviceaccounts を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/serviceaccounts/delete | serviceaccounts を削除します |
> | Microsoft.Kubernetes/connectedClusters/serviceaccounts/impersonate/action | serviceaccounts を偽装します |
> | Microsoft.Kubernetes/connectedClusters/services/read | サービスを読み取ります |
> | Microsoft.Kubernetes/connectedClusters/services/write | サービスを書き込みます |
> | Microsoft.Kubernetes/connectedClusters/services/delete | サービスを削除します |
> | Microsoft.Kubernetes/connectedClusters/storage.k8s.io/csidrivers/read | csidrivers を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/storage.k8s.io/csidrivers/write | csidrivers を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/storage.k8s.io/csidrivers/delete | csidrivers を削除します |
> | Microsoft.Kubernetes/connectedClusters/storage.k8s.io/csinodes/read | csinodes を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/storage.k8s.io/csinodes/write | csinodes を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/storage.k8s.io/csinodes/delete | csinodes を削除します |
> | Microsoft.Kubernetes/connectedClusters/storage.k8s.io/storageclasses/read | storageclasses を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/storage.k8s.io/storageclasses/write | storageclasses を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/storage.k8s.io/storageclasses/delete | storageclasses を削除します |
> | Microsoft.Kubernetes/connectedClusters/storage.k8s.io/volumeattachments/read | volumeattachments を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/storage.k8s.io/volumeattachments/write | volumeattachments を書き込みます |
> | Microsoft.Kubernetes/connectedClusters/storage.k8s.io/volumeattachments/delete | volumeattachments を削除します |
> | Microsoft.Kubernetes/connectedClusters/swagger-api/read | swagger-api を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/swagger-ui/read | swagger-ui を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/ui/read | UI を読み取ります |
> | Microsoft.Kubernetes/connectedClusters/users/impersonate/action | ユーザーを偽装します |
> | Microsoft.Kubernetes/connectedClusters/version/read | バージョンを読み取ります |

### <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

Azure サービス:[Azure Lighthouse](../lighthouse/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.ManagedServices/register/action | マネージド サービスに登録します。 |
> | Microsoft.ManagedServices/unregister/action | マネージドサービスから登録解除します。 |
> | Microsoft.ManagedServices/marketplaceRegistrationDefinitions/read | マネージド サービスの登録定義の一覧を取得します。 |
> | Microsoft.ManagedServices/operations/read | マネージド サービスの操作一覧を取得します。 |
> | Microsoft.ManagedServices/operationStatuses/read | リソースの操作の状態を読み取ります。 |
> | Microsoft.ManagedServices/registrationAssignments/read | マネージド サービスの登録割り当ての一覧を取得します。 |
> | Microsoft.ManagedServices/registrationAssignments/write | マネージド サービスの登録割り当てを追加または変更します。 |
> | Microsoft.ManagedServices/registrationAssignments/delete | マネージド サービスの登録割り当てを削除します。 |
> | Microsoft.ManagedServices/registrationDefinitions/read | マネージド サービスの登録定義の一覧を取得します。 |
> | Microsoft.ManagedServices/registrationDefinitions/write | マネージド サービスの登録定義を追加または変更します。 |
> | Microsoft.ManagedServices/registrationDefinitions/delete | マネージド サービスの登録定義を削除します。 |

### <a name="microsoftmanagement"></a>Microsoft.Management

Azure サービス:[管理グループ](../governance/management-groups/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Management/checkNameAvailability/action | 指定された管理グループ名が有効で一意かどうかを確認します。 |
> | Microsoft.Management/getEntities/action | 認証済みユーザーのすべてのエンティティ (管理グループ、サブスクリプションなど) を一覧表示します。 |
> | Microsoft.Management/register/action | 指定したサブスクリプションを Microsoft.Management に登録します。 |
> | Microsoft.Management/managementGroups/read | 認証済みユーザーの管理グループを一覧表示します。 |
> | Microsoft.Management/managementGroups/write | 管理グループを作成または更新します。 |
> | Microsoft.Management/managementGroups/delete | 管理グループを削除します。 |
> | Microsoft.Management/managementGroups/descendants/read | 管理グループのすべての子孫 (管理グループ、サブスクリプション) を取得します。 |
> | Microsoft.Management/managementGroups/settings/read | 既存の管理グループ階層の設定が一覧表示されます。 |
> | Microsoft.Management/managementGroups/settings/write | 管理グループ階層の設定が作成または更新されます。 |
> | Microsoft.Management/managementGroups/settings/delete | 管理グループ階層の設定が削除されます。 |
> | Microsoft.Management/managementGroups/subscriptions/read | 指定された管理グループのサブスクリプションを一覧表示します。 |
> | Microsoft.Management/managementGroups/subscriptions/write | 既存のサブスクリプションと管理グループを関連付けます。 |
> | Microsoft.Management/managementGroups/subscriptions/delete | 管理グループからサブスクリプションの関連付けを解除します。 |

### <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

Azure サービス:[Azure Policy](../governance/policy/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.PolicyInsights/register/action | Microsoft Policy Insights リソース プロバイダーを登録して、それに対するアクションを有効にします。 |
> | Microsoft.PolicyInsights/unregister/action | Microsoft Policy Insights リソース プロバイダーの登録を解除します。 |
> | Microsoft.PolicyInsights/asyncOperationResults/read | 非同期操作の結果を取得します。 |
> | Microsoft.PolicyInsights/attestations/read | コンプライアンスの状態の構成証明を取得します。 |
> | Microsoft.PolicyInsights/attestations/write | コンプライアンスの状態の構成証明を作成または更新します。 |
> | Microsoft.PolicyInsights/attestations/delete | コンプライアンスの状態の構成証明を削除します。 |
> | Microsoft.PolicyInsights/checkPolicyRestrictions/read | ポリシーによってリソースに適用される制限の詳細を取得します。 |
> | Microsoft.PolicyInsights/eventGridFilters/read | 状態の変更通知を発行するスコープの追跡に使用する Event Grid フィルターを取得します。 |
> | Microsoft.PolicyInsights/eventGridFilters/write | Event Grid フィルターを作成または更新します。 |
> | Microsoft.PolicyInsights/eventGridFilters/delete | Event Grid フィルターを削除します。 |
> | Microsoft.PolicyInsights/operations/read | Microsoft.PolicyInsights 名前空間でサポートされている操作を取得します |
> | Microsoft.PolicyInsights/policyEvents/queryResults/action | ポリシー イベントに関する情報のクエリを実行します。 |
> | Microsoft.PolicyInsights/policyEvents/queryResults/read | ポリシー イベントに関する情報のクエリを実行します。 |
> | Microsoft.PolicyInsights/policyMetadata/read | ポリシー メタデータ リソースを取得します。 |
> | Microsoft.PolicyInsights/policyStates/queryResults/action | ポリシーの状態に関する情報のクエリを実行します。 |
> | Microsoft.PolicyInsights/policyStates/summarize/action | ポリシーの最新の状態に関する概要情報のクエリを実行します。 |
> | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | 選択したスコープの新たなコンプライアンス評価をトリガーします。 |
> | Microsoft.PolicyInsights/policyStates/queryResults/read | ポリシーの状態に関する情報のクエリを実行します。 |
> | Microsoft.PolicyInsights/policyStates/summarize/read | ポリシーの最新の状態に関する概要情報のクエリを実行します。 |
> | Microsoft.PolicyInsights/policyTrackedResources/queryResults/read | DeployIfNotExists ポリシーで必要なリソースに関する情報をクエリします。 |
> | Microsoft.PolicyInsights/remediations/read | ポリシーの修復を取得します。 |
> | Microsoft.PolicyInsights/remediations/write | Microsoft Policy の修復を作成または更新します。 |
> | Microsoft.PolicyInsights/remediations/delete | ポリシーの修復を削除します。 |
> | Microsoft.PolicyInsights/remediations/cancel/action | 進行中の Microsoft Policy の修復を取り消します。 |
> | Microsoft.PolicyInsights/remediations/listDeployments/read | ポリシーの修復で必要なデプロイを一覧表示します。 |
> | **DataAction** | **説明** |
> | Microsoft.PolicyInsights/checkDataPolicyCompliance/action | 指定されたコンポーネントのコンプライアンス状態をデータ ポリシーと照合します。 |
> | Microsoft.PolicyInsights/policyEvents/logDataEvents/action | リソース コンポーネントのポリシー イベントをログに記録します。 |

### <a name="microsoftportal"></a>Microsoft.Portal

Azure サービス:[Azure Portal](../azure-portal/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Portal/register/action | ポータルに登録します |
> | Microsoft.Portal/consoles/delete | Cloud Shell インスタンスを削除します。 |
> | Microsoft.Portal/consoles/write | Cloud Shell インスタンスを作成または更新します。 |
> | Microsoft.Portal/consoles/read | Cloud Shell インスタンスを読み取ります。 |
> | Microsoft.Portal/dashboards/read | サブスクリプションのダッシュボードを読み取ります。 |
> | Microsoft.Portal/dashboards/write | サブスクリプションのダッシュボードを追加または変更します。 |
> | Microsoft.Portal/dashboards/delete | サブスクリプションからダッシュボードを削除します。 |
> | Microsoft.Portal/tenantConfigurations/read | テナント構成を読み取ります |
> | Microsoft.Portal/tenantConfigurations/write | テナント構成を追加または更新します。 この操作を行うには、ユーザーはテナント管理者である必要があります。 |
> | Microsoft.Portal/tenantConfigurations/delete | テナント構成を削除します。 この操作を行うには、ユーザーはテナント管理者である必要があります。 |
> | Microsoft.Portal/usersettings/delete | Cloud Shell ユーザー設定を削除します。 |
> | Microsoft.Portal/usersettings/write | Cloud Shell ユーザー設定を作成または更新します。 |
> | Microsoft.Portal/usersettings/read | Cloud Shell ユーザー設定を読み取ります。 |

### <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

Azure サービス:[Site Recovery](../site-recovery/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.RecoveryServices/register/action | 指定されたリソース プロバイダーにサブスクリプションを登録します。 |
> | microsoft.recoveryservices/Locations/backupCrossRegionRestore/action | リージョンをまたがる復元をトリガーします。 |
> | microsoft.recoveryservices/Locations/backupCrrJob/action | Recovery Services コンテナーのセカンダリ リージョンにおけるリージョンをまたがる復元ジョブの詳細を取得します。 |
> | microsoft.recoveryservices/Locations/backupCrrJobs/action | Recovery Services コンテナーのセカンダリ リージョンにおけるリージョンをまたがる復元ジョブを一覧表示します。 |
> | microsoft.recoveryservices/Locations/backupPreValidateProtection/action |  |
> | microsoft.recoveryservices/Locations/backupStatus/action | Recovery Services コンテナーのバックアップの状態を確認します。 |
> | microsoft.recoveryservices/Locations/backupValidateFeatures/action | 機能を検証します。 |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp は、サービスによって使用される内部操作です。 |
> | Microsoft.RecoveryServices/locations/checkNameAvailability/action | リソース名の可用性がリソース名を使用できるかどうかを確認するための API であることを確認します |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp は、サービスによって使用される内部操作です。 |
> | microsoft.recoveryservices/Locations/backupAadProperties/read | リージョンをまたがる復元の 3 番目のリージョンにおける認証用 AAD プロパティを取得します。 |
> | microsoft.recoveryservices/Locations/backupCrrOperationResults/read | Recovery Services コンテナーに対する CRR 操作の結果を返します。 |
> | microsoft.recoveryservices/Locations/backupCrrOperationsStatus/read | Recovery Services コンテナーに対する CRR 操作の状態を返します。 |
> | microsoft.recoveryservices/Locations/backupProtectedItem/write | バックアップ保護項目を作成します。 |
> | microsoft.recoveryservices/Locations/backupProtectedItems/read | すべての保護された項目の一覧を返します。 |
> | Microsoft.RecoveryServices/locations/operationStatus/read | 特定の操作の操作の状態を取得します |
> | Microsoft.RecoveryServices/operations/read | リソース プロバイダーの操作の一覧を返します。 |
> | microsoft.recoveryservices/Vaults/backupJobsExport/action | ジョブをエクスポートします。 |
> | microsoft.recoveryservices/Vaults/backupSecurityPIN/action | Recovery Services コンテナーのセキュリティ PIN 情報を返します。 |
> | microsoft.recoveryservices/Vaults/backupValidateOperation/action | 保護された項目に対する操作を検証します |
> | Microsoft.RecoveryServices/Vaults/write | "コンテナーの作成" 操作では、"コンテナー" 型の Azure リソースを作成します。 |
> | Microsoft.RecoveryServices/Vaults/read | "コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。 |
> | Microsoft.RecoveryServices/Vaults/delete | "コンテナーの削除" 操作では、指定された "コンテナー" 型の Azure リソースを削除します。 |
> | microsoft.recoveryservices/Vaults/backupconfig/read | Recovery Services コンテナーの構成を返します。 |
> | microsoft.recoveryservices/Vaults/backupconfig/write | Recovery Services コンテナーの構成を更新します。 |
> | microsoft.recoveryservices/Vaults/backupEncryptionConfigs/read | バックアップ リソースの暗号化構成を取得します。 |
> | microsoft.recoveryservices/Vaults/backupEncryptionConfigs/write | バックアップ リソースの暗号化構成を更新します |
> | microsoft.recoveryservices/Vaults/backupEngines/read | コンテナーに登録されているすべてのバックアップ管理サーバーを返します。 |
> | microsoft.recoveryservices/Vaults/backupFabrics/refreshContainers/action | コンテナーの一覧を更新します。 |
> | microsoft.recoveryservices/Vaults/backupFabrics/backupProtectionIntent/delete | バックアップ保護の意図を削除します |
> | microsoft.recoveryservices/Vaults/backupFabrics/backupProtectionIntent/read | バックアップ保護の意図を取得します |
> | microsoft.recoveryservices/Vaults/backupFabrics/backupProtectionIntent/write | バックアップの保護インテントを作成します |
> | microsoft.recoveryservices/Vaults/backupFabrics/operationResults/read | 操作の状態を返します。 |
> | microsoft.recoveryservices/Vaults/backupFabrics/operationsStatus/read | 操作の状態を返します。 |
> | microsoft.recoveryservices/Vaults/backupFabrics/protectableContainers/read | すべての保護可能なコンテナーを取得します。 |
> | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/delete | 登録済みコンテナーを削除します。 |
> | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/inquire/action | コンテナー内のワークロードを照会します。 |
> | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/read | すべての登録済みコンテナーを返します。 |
> | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/write | 登録済みコンテナーを作成します。 |
> | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/items/read | コンテナー内のすべての項目を取得します。 |
> | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/operationResults/read | 保護コンテナーに対して実行された操作の結果を取得します。 |
> | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/operationsStatus/read | 保護コンテナーに対して実行された操作の状態を取得します。 |
> | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | 保護された項目のバックアップを実行します。 |
> | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | 保護された項目を削除します。 |
> | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/read | 保護された項目のオブジェクトの詳細を返します。 |
> | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPointsRecommendedForMove/action | 別の層への移動に推奨される復旧ポイントを取得します |
> | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/write | バックアップ保護項目を作成します。 |
> | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | 保護された項目に対して実行された操作の結果を取得します。 |
> | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | 保護された項目に対して実行された操作の状態を返します。 |
> | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/accessToken/action | リージョンをまたがる復元の AccessToken を取得します。 |
> | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/move/action | 復旧ポイントを別の層に移動します |
> | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | 保護された項目のインスタント項目回復をプロビジョニングします。 |
> | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | 保護された項目の復旧ポイントを取得します。 |
> | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | 保護された項目の復旧ポイントを復元します。 |
> | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | 保護された項目のインスタント項目回復を取り消します。 |
> | microsoft.recoveryservices/Vaults/backupJobs/cancel/action | ジョブを取り消します。 |
> | microsoft.recoveryservices/Vaults/backupJobs/read | すべてのジョブ オブジェクトを返します。 |
> | microsoft.recoveryservices/Vaults/backupJobs/operationResults/read | ジョブ操作の結果を返します。 |
> | microsoft.recoveryservices/Vaults/backupJobs/operationsStatus/read | ジョブ操作の状態を返します。 |
> | microsoft.recoveryservices/Vaults/backupOperationResults/read | Recovery Services コンテナーに対するバックアップ操作の結果を返します。 |
> | microsoft.recoveryservices/Vaults/backupOperations/read | Recovery Services コンテナーに対するバックアップ操作の状態を返します。 |
> | microsoft.recoveryservices/Vaults/backupOperationVaultGuardProxies/delete | "VaultGuard の削除" プロキシ操作は、指定された "VaultGuard プロキシ" 型の Azure リソースを削除します |
> | microsoft.recoveryservices/Vaults/backupOperationVaultGuardProxies/read | "VaultGuard の取得" 操作は、"VaultGuard プロキシ" 型の Azure リソースを表すオブジェクトを取得します |
> | microsoft.recoveryservices/Vaults/backupOperationVaultGuardProxies/read | リソースの VaultGuard プロキシの一覧を取得します |
> | microsoft.recoveryservices/Vaults/backupOperationVaultGuardProxies/unlockDelete/action | "VaultGuard の削除のロック解除" プロキシ操作は、次の重要な削除操作のロックを解除します |
> | microsoft.recoveryservices/Vaults/backupOperationVaultGuardProxies/write | "VaultGuard の作成" プロキシ操作は、"VaultGuard プロキシ" 型の Azure リソースを作成します |
> | microsoft.recoveryservices/Vaults/backupPolicies/delete | 保護ポリシーを削除します。 |
> | microsoft.recoveryservices/Vaults/backupPolicies/read | すべての保護ポリシーを返します。 |
> | microsoft.recoveryservices/Vaults/backupPolicies/write | 保護ポリシーを作成します。 |
> | microsoft.recoveryservices/Vaults/backupPolicies/operationResults/read | ポリシー操作の結果を取得します。 |
> | microsoft.recoveryservices/Vaults/backupPolicies/operations/read | ポリシー操作の状態を取得します。 |
> | microsoft.recoveryservices/Vaults/backupProtectableItems/read | すべての保護可能な項目の一覧を返します。 |
> | microsoft.recoveryservices/Vaults/backupProtectedItems/read | すべての保護された項目の一覧を返します。 |
> | microsoft.recoveryservices/Vaults/backupProtectionContainers/read | サブスクリプションに属するすべてのコンテナーを返します。 |
> | microsoft.recoveryservices/Vaults/backupProtectionIntents/read | すべてのバックアップ保護の意図を一覧表示します |
> | microsoft.recoveryservices/Vaults/backupstorageconfig/read | Recovery Services コンテナーのストレージ構成を返します。 |
> | microsoft.recoveryservices/Vaults/backupstorageconfig/write | Recovery Services コンテナーのストレージ構成を更新します。 |
> | microsoft.recoveryservices/Vaults/backupUsageSummaries/read | Recovery Services の保護された項目と保護されたサーバーの概要を返します。 |
> | Microsoft.RecoveryServices/Vaults/certificates/write | "リソース証明書を更新" 操作では、リソース/コンテナー資格情報証明書を更新します。 |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | "拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。 |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | "拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。 |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | "拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。 |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Recovery Services コンテナーのアラートを取得します。 |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | アラートを解決する。 |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | Recovery Services コンテナーの通知構成を取得します。 |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | Recovery Services コンテナーへのメール通知を構成します。 |
> | microsoft.recoveryservices/Vaults/privateEndpointConnectionProxies/delete | しばらくしてから操作をやり直してください。 問題が解決しない場合は、Microsoft サポートにお問い合わせください。 |
> | microsoft.recoveryservices/Vaults/privateEndpointConnectionProxies/read | すべての保護可能なコンテナーを取得します。 |
> | microsoft.recoveryservices/Vaults/privateEndpointConnectionProxies/validate/action | すべての保護可能なコンテナーを取得します。 |
> | microsoft.recoveryservices/Vaults/privateEndpointConnectionProxies/write | すべての保護可能なコンテナーを取得します。 |
> | microsoft.recoveryservices/Vaults/privateEndpointConnectionProxies/operationsStatus/read | すべての保護可能なコンテナーを取得します。 |
> | microsoft.recoveryservices/Vaults/privateEndpointConnections/delete | プライベート エンドポイント要求を削除します。 この呼び出しは、バックアップ管理者によって行われます。 |
> | microsoft.recoveryservices/Vaults/privateEndpointConnections/write | プライベート エンドポイント要求を承認または拒否します。 この呼び出しは、バックアップ管理者によって行われます。 |
> | microsoft.recoveryservices/Vaults/privateEndpointConnections/operationsStatus/read | プライベート エンドポイント接続の操作状態を返します。 |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | "サービス コンテナーを登録" 操作を使用すると、コンテナーを Recovery Services に登録できます。 |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | " コンテナーを取得" 操作を使用すると、リソースの登録済みコンテナーを取得できます。 |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | "コンテナーの登録を解除" 操作を使用すると、コンテナーの登録を解除できます。 |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | "操作結果を取得" 操作を使用すると、非同期で送信された操作の状態と結果を取得できます。 |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | アラート設定を読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | アラート設定を作成または更新します |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | イベントを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | ファブリックを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/write | ファブリックを作成または更新します |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | ファブリックを削除します。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | ファブリックの一貫性を確認します。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | ファブリックをすべて削除します |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | ファブリックの証明書を更新します。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | プロセス サーバー イメージをデプロイします。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | ゲートウェイを再関連付けします。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/migratetoaad/action | ファブリックを AAD に移行します |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/operationresults/read | リソースのファブリックでの非同期操作の結果を追跡します |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationLogicalNetworks/read | 論理ネットワークを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | ネットワークを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | ネットワーク マッピングを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | ネットワーク マッピングを作成または更新します |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | ネットワーク マッピングをすべて削除します |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | 保護コンテナーを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | 保護可能な項目を検出します。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | 保護コンテナーを作成または更新します |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | 保護コンテナーを削除します。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | 保護コンテナーを切り替えます。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/operationresults/read | リソースの保護コンテナーでの非同期操作の結果を追跡します |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/read | 移行項目を読み取ります。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/write | 移行項目を作成または更新します。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/delete | 移行項目を削除します。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/resync/action | 再同期します |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrate/action | 項目を移行します。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/action | 移行をテストします。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/action | 移行のクリーンアップをテストします。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/read | 移行の復旧ポイントを読み取ります。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/operationresults/read | リソースの移行項目での非同期操作の結果を追跡します |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | 保護可能な項目を読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | 保護された項目を読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | 保護された項目を作成または更新します |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | 保護された項目をすべて削除します |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | 保護された項目を削除します。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | 計画されたフェールオーバー。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | [フェールオーバー] |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | テスト フェールオーバー |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | テスト フェールオーバーのクリーンアップ。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | フェールオーバーのコミット。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | 保護された項目を再保護します。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | モビリティ サービスを更新します。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | レプリケーションを修復します。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | 復旧ポイントを適用します。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/action | フィードバックを送信します |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/addDisks/action | ディスクを追加します |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/removeDisks/action | ディスクを削除します |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ResolveHealthErrors/action |  |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCancel/action | フェールオーバーのキャンセル |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/operationresults/read | リソースの保護された項目での非同期操作の結果を追跡します |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | レプリケーションの復旧ポイントを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/read | ターゲット コンピューティング サイズを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | 保護コンテナー マッピングを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | 保護コンテナー マッピングを作成または更新します |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | 保護コンテナー マッピングを削除します。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | 保護コンテナー マッピングをすべて削除します |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/operationresults/read | リソースの保護コンテナー マッピングでの非同期操作の結果を追跡します |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Recovery Services プロバイダーを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | Recovery Services プロバイダーを作成または更新します |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | Recovery Services プロバイダーを削除します。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | Recovery Services プロバイダーをすべて削除します |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | プロバイダーを更新します。 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/operationresults/read | リソースの Recovery Services プロバイダーでの非同期操作の結果を追跡します |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | ストレージの分類を読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | ストレージ分類マッピングを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | ストレージ分類マッピングを作成または更新します |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | ストレージ分類マッピングをすべて削除します |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/operationresults/read | リソースのストレージ分類マッピングでの非同期操作の結果を追跡します |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | vCenter を読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | vCenter を作成または更新します |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | vCenter をすべて削除します |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/operationresults/read | リソースの vCenter での非同期操作の結果を追跡します |
> | Microsoft.RecoveryServices/vaults/replicationJobs/read | ジョブを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | ジョブを取り消します。 |
> | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | ジョブを再実行します。 |
> | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | ジョブを再開します。 |
> | Microsoft.RecoveryServices/vaults/replicationJobs/operationresults/read | リソースのジョブでの非同期操作の結果を追跡します |
> | Microsoft.RecoveryServices/vaults/replicationMigrationItems/read | 移行項目を読み取ります。 |
> | Microsoft.RecoveryServices/vaults/replicationNetworkMappings/read | ネットワーク マッピングを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationNetworks/read | ネットワークを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationOperationStatus/read | コンテナー レプリケーション操作の状態を読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | ポリシーを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/write | ポリシーを作成または更新します |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | ポリシーをすべて削除します |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/operationresults/read | リソースのポリシーでの非同期操作の結果を追跡します |
> | Microsoft.RecoveryServices/vaults/replicationProtectedItems/read | 保護された項目を読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationProtectionContainerMappings/read | 保護コンテナー マッピングを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationProtectionContainers/read | 保護コンテナーを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationProtectionIntents/read | サポート対象のオペレーティング システムを読み取ります  |
> | Microsoft.RecoveryServices/vaults/replicationProtectionIntents/write | を作成または更新します  |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | 復旧計画を読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | 復旧計画を作成または更新します |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | 復旧計画をすべて削除します |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | 計画されたフェールオーバーの復旧計画。 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | フェールオーバーの復旧計画。 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | テスト フェールオーバーの復旧計画。 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | テスト フェールオーバーのクリーンアップの復旧計画。 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | フェールオーバーのコミットの復旧計画。 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | 再保護の復旧計画。 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCancel/action | フェールオーバーの復旧計画を取り消します |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/operationresults/read | リソースの復旧計画での非同期操作の結果を追跡します |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryServicesProviders/read | Recovery Services プロバイダーを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationStorageClassificationMappings/read | ストレージ分類マッピングを読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationStorageClassifications/read | ストレージの分類を読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationSupportedOperatingSystems/read | サポート対象のオペレーティング システムを読み取ります  |
> | Microsoft.RecoveryServices/vaults/replicationSupportedRegionMappings/read | サポート対象のオペレーティング システムを読み取ります  |
> | Microsoft.RecoveryServices/vaults/replicationUsages/read | コンテナー レプリケーションの使用状況を読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationVaultHealth/read | コンテナー レプリケーションの正常性を読み取ります |
> | Microsoft.RecoveryServices/vaults/replicationVaultHealth/refresh/action | コンテナーの正常性を更新します |
> | Microsoft.RecoveryServices/vaults/replicationVaultHealth/operationresults/read | リソースのコンテナー レプリケーションの正常性での非同期操作の結果を追跡します |
> | Microsoft.RecoveryServices/vaults/replicationVaultSettings/read | サポート対象のオペレーティング システムを読み取ります  |
> | Microsoft.RecoveryServices/vaults/replicationVaultSettings/write | を作成または更新します  |
> | Microsoft.RecoveryServices/vaults/replicationvCenters/read | vCenter を読み取ります |
> | microsoft.recoveryservices/Vaults/usages/read | Recovery Services コンテナーの使用状況の詳細を返します。 |
> | Microsoft.RecoveryServices/vaults/usages/read | コンテナーの使用状況を読み取ります |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | "コンテナー トークン" 操作を使用すると、コンテナー レベルのバックエンド操作のコンテナー トークンを取得できます。 |

### <a name="microsoftresources"></a>Microsoft.Resources

Azure サービス:[Azure Resource Manager](../azure-resource-manager/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Resources/checkResourceName/action | リソース名の妥当性を確認します。 |
> | Microsoft.Resources/calculateTemplateHash/action | 指定されたテンプレートのハッシュを計算します。 |
> | Microsoft.Resources/checkPolicyCompliance/read | 指定されたリソースのコンプライアンス状態をリソース ポリシーと照合します。 |
> | Microsoft.Resources/deployments/read | デプロイを取得または一覧表示します。 |
> | Microsoft.Resources/deployments/write | デプロイを作成または更新します。 |
> | Microsoft.Resources/deployments/delete | デプロイを削除します。 |
> | Microsoft.Resources/deployments/cancel/action | デプロイを取り消します。 |
> | Microsoft.Resources/deployments/validate/action | デプロイを検証します。 |
> | Microsoft.Resources/deployments/whatIf/action | テンプレートのデプロイの変更を予測します。 |
> | Microsoft.Resources/deployments/exportTemplate/action | デプロイのテンプレートをエクスポートします |
> | Microsoft.Resources/deployments/operations/read | デプロイ操作を取得または一覧表示します。 |
> | Microsoft.Resources/deployments/operationstatuses/read | デプロイ操作の状態を取得または一覧表示します。 |
> | Microsoft.Resources/deploymentScripts/read | デプロイ スクリプトを取得または一覧表示します |
> | Microsoft.Resources/deploymentScripts/write | デプロイ スクリプトを作成または更新します |
> | Microsoft.Resources/deploymentScripts/delete | デプロイ スクリプトを削除します |
> | Microsoft.Resources/deploymentScripts/logs/read | デプロイ スクリプトのログを取得または一覧表示します |
> | Microsoft.Resources/links/read | リソース リンクを取得または一覧表示します。 |
> | Microsoft.Resources/links/write | リソース リンクを作成または更新します。 |
> | Microsoft.Resources/links/delete | リソース リンクを削除します。 |
> | Microsoft.Resources/marketplace/purchase/action | Marketplace からリソースを購入します。 |
> | Microsoft.Resources/providers/read | プロバイダーの一覧を取得します。 |
> | Microsoft.Resources/resources/read | フィルターに基づいてリソースの一覧を取得します。 |
> | Microsoft.Resources/subscriptions/read | サブスクリプションの一覧を取得します。 |
> | Microsoft.Resources/subscriptions/locations/read | サポートされる場所の一覧を取得します。 |
> | Microsoft.Resources/subscriptions/operationresults/read | サブスクリプション操作の結果を取得します。 |
> | Microsoft.Resources/subscriptions/providers/read | リソース プロバイダーを取得または一覧表示します。 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループを取得または一覧表示します。 |
> | Microsoft.Resources/subscriptions/resourceGroups/write | リソース グループを作成または更新します。 |
> | Microsoft.Resources/subscriptions/resourceGroups/delete | リソース グループとそのすべてのリソースを削除します。 |
> | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | リソース グループ間でリソースを移動します。 |
> | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | リソース グループ間でのリソースの移動を検証します。 |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | デプロイを取得または一覧表示します。 |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | デプロイを作成または更新します。 |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | デプロイ操作を取得または一覧表示します。 |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | デプロイ操作の状態を取得または一覧表示します。 |
> | Microsoft.Resources/subscriptions/resourcegroups/resources/read | リソース グループのリソースを取得します。 |
> | Microsoft.Resources/subscriptions/resources/read | サブスクリプションのリソースを取得します。 |
> | Microsoft.Resources/subscriptions/tagNames/read | サブスクリプション タグを取得または一覧表示します。 |
> | Microsoft.Resources/subscriptions/tagNames/write | サブスクリプション タグを追加します。 |
> | Microsoft.Resources/subscriptions/tagNames/delete | サブスクリプション タグを削除します。 |
> | Microsoft.Resources/subscriptions/tagNames/tagValues/read | サブスクリプション タグの値を取得または一覧表示します。 |
> | Microsoft.Resources/subscriptions/tagNames/tagValues/write | サブスクリプション タグの値を追加します。 |
> | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | サブスクリプション タグの値を削除します。 |
> | Microsoft.Resources/tags/read | リソースのすべてのタグを取得します。 |
> | Microsoft.Resources/tags/write | 既存のタグを新しいタグのセットと置換またはマージするか、既存のタグを削除することによって、リソースのタグを更新します。 |
> | Microsoft.Resources/tags/delete | リソースのすべてのタグを削除します。 |
> | Microsoft.Resources/templateSpecs/read | テンプレート スペックを取得または一覧表示します |
> | Microsoft.Resources/templateSpecs/write | テンプレート スペックを作成または更新します |
> | Microsoft.Resources/templateSpecs/delete | テンプレート スペックを削除します |
> | Microsoft.Resources/templateSpecs/versions/read | テンプレート スペックを取得または一覧表示します |
> | Microsoft.Resources/templateSpecs/versions/write | テンプレート スペックのバージョンを作成または更新します |
> | Microsoft.Resources/templateSpecs/versions/delete | テンプレート スペックのバージョンを削除します |
> | Microsoft.Resources/tenants/read | テナントの一覧を取得します。 |

### <a name="microsoftscheduler"></a>Microsoft.Scheduler

Azure サービス:[Scheduler](../scheduler/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Scheduler/jobcollections/read | ジョブ コレクションを取得します。 |
> | Microsoft.Scheduler/jobcollections/write | ジョブ コレクションを作成または更新します。 |
> | Microsoft.Scheduler/jobcollections/delete | ジョブ コレクションを削除します。 |
> | Microsoft.Scheduler/jobcollections/enable/action | ジョブ コレクションを有効にします。 |
> | Microsoft.Scheduler/jobcollections/disable/action | ジョブ コレクションを無効にします。 |
> | Microsoft.Scheduler/jobcollections/jobs/read | ジョブを取得します。 |
> | Microsoft.Scheduler/jobcollections/jobs/write | ジョブを作成または更新します。 |
> | Microsoft.Scheduler/jobcollections/jobs/delete | ジョブを削除します。 |
> | Microsoft.Scheduler/jobcollections/jobs/run/action | ジョブを実行します。 |
> | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | Scheduler ジョブに基づいてロジック アプリ定義を生成します。 |
> | Microsoft.Scheduler/jobcollections/jobs/jobhistories/read | ジョブ履歴を取得します。 |

### <a name="microsoftsolutions"></a>Microsoft.Solutions

Azure サービス:[Azure Managed Applications](../azure-resource-manager/managed-applications/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Solutions/register/action | ソリューションに登録します。 |
> | Microsoft.Solutions/unregister/action | ソリューションから登録を解除します。 |
> | Microsoft.Solutions/applicationDefinitions/read | アプリケーション定義の一覧を取得します。 |
> | Microsoft.Solutions/applicationDefinitions/write | アプリケーション定義を追加または変更します。 |
> | Microsoft.Solutions/applicationDefinitions/delete | アプリケーション定義を削除します。 |
> | Microsoft.Solutions/applicationDefinitions/applicationArtifacts/read | アプリケーション定義のアプリケーション アーティファクトを一覧表示します。 |
> | Microsoft.Solutions/applications/read | アプリケーションの一覧を取得します。 |
> | Microsoft.Solutions/applications/write | アプリケーションを作成します。 |
> | Microsoft.Solutions/applications/delete | アプリケーションを削除します。 |
> | Microsoft.Solutions/applications/refreshPermissions/action | アプリケーションのアクセス許可を更新します。 |
> | Microsoft.Solutions/applications/updateAccess/action | アプリケーション アクセスを更新します。 |
> | Microsoft.Solutions/applications/applicationArtifacts/read | アプリケーションの成果物を一覧表示します。 |
> | Microsoft.Solutions/jitRequests/read | JitRequests の一覧を取得します |
> | Microsoft.Solutions/jitRequests/write | JitRequest を作成します |
> | Microsoft.Solutions/jitRequests/delete | JitRequest を削除します |
> | Microsoft.Solutions/locations/operationStatuses/read | リソースの操作の状態を読み取ります。 |
> | Microsoft.Solutions/operations/read | 操作の一覧を取得します。 |

### <a name="microsoftsubscription"></a>Microsoft.Subscription

Azure サービス: コア

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Subscription/cancel/action | サブスクリプションを取り消します。 |
> | Microsoft.Subscription/rename/action | サブスクリプションの名前を変更します。 |
> | Microsoft.Subscription/enable/action | サブスクリプションを再アクティブ化します |
> | Microsoft.Subscription/CreateSubscription/action | Azure サブスクリプションを作成します。 |
> | Microsoft.Subscription/register/action | Microsoft.Subscription リソース プロバイダーにサブスクリプションを登録します。 |
> | Microsoft.Subscription/updateTenant/action | サブスクリプションのテナントを更新します。 |
> | Microsoft.Subscription/aliases/write | サブスクリプションの別名を作成します。 |
> | Microsoft.Subscription/aliases/read | サブスクリプションの別名を取得します |
> | Microsoft.Subscription/aliases/delete | サブスクリプションの別名を削除します |
> | Microsoft.Subscription/Subscriptions/write | Azure サブスクリプションを作成します。 |

## <a name="intune"></a>Intune

### <a name="microsoftintune"></a>Microsoft.Intune

Azure サービス:Microsoft Monitoring Insights

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.Intune/diagnosticsettings/write | 診断設定を書き込みます。 |
> | Microsoft.Intune/diagnosticsettings/read | 診断設定を読み取ります。 |
> | Microsoft.Intune/diagnosticsettings/delete | 診断設定を削除します。 |
> | Microsoft.Intune/diagnosticsettingscategories/read | 診断設定のカテゴリを読み取ります。 |

## <a name="other"></a>その他

### <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

Azure サービス:[Windows Virtual Desktop](../virtual-desktop/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.DesktopVirtualization/register/action | サブスクリプションを登録します |
> | Microsoft.DesktopVirtualization/applicationgroups/read | applicationgroups を読み取ります |
> | Microsoft.DesktopVirtualization/applicationgroups/write | applicationgroups を書き込みます |
> | Microsoft.DesktopVirtualization/applicationgroups/delete | applicationgroups を削除します |
> | Microsoft.DesktopVirtualization/applicationgroups/applications/read | applicationgroups/applications を読み取ります |
> | Microsoft.DesktopVirtualization/applicationgroups/applications/write | applicationgroups/applications を書き込みます |
> | Microsoft.DesktopVirtualization/applicationgroups/applications/delete | applicationgroups/applications を削除します |
> | Microsoft.DesktopVirtualization/applicationgroups/desktops/read | applicationgroups/desktops を読み取ります |
> | Microsoft.DesktopVirtualization/applicationgroups/desktops/write | applicationgroups/desktops を書き込みます |
> | Microsoft.DesktopVirtualization/applicationgroups/desktops/delete | applicationgroups/desktops を削除します |
> | Microsoft.DesktopVirtualization/applicationgroups/externaluserassignments/read |  |
> | Microsoft.DesktopVirtualization/applicationgroups/providers/Microsoft.Insights/diagnosticSettings/read | 診断設定を取得します |
> | Microsoft.DesktopVirtualization/applicationgroups/providers/Microsoft.Insights/diagnosticSettings/write | 診断設定を作成または更新します |
> | Microsoft.DesktopVirtualization/applicationgroups/providers/Microsoft.Insights/logDefinitions/read | 利用可能なログを取得します |
> | Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/read | スタート メニューの項目を読み取ります |
> | Microsoft.DesktopVirtualization/hostpools/read | Hostpools を読み取ります |
> | Microsoft.DesktopVirtualization/hostpools/write | hostpools を書き込みます |
> | Microsoft.DesktopVirtualization/hostpools/delete | hostpools を削除します |
> | Microsoft.DesktopVirtualization/hostpools/providers/Microsoft.Insights/diagnosticSettings/read | 診断設定を取得します |
> | Microsoft.DesktopVirtualization/hostpools/providers/Microsoft.Insights/diagnosticSettings/write | 診断設定を作成または更新します |
> | Microsoft.DesktopVirtualization/hostpools/providers/Microsoft.Insights/logDefinitions/read | 利用可能なログを取得します |
> | Microsoft.DesktopVirtualization/hostpools/sessionhosts/read | hostpools/sessionhosts を読み取ります |
> | Microsoft.DesktopVirtualization/hostpools/sessionhosts/write | hostpools/sessionhosts を書き込みます |
> | Microsoft.DesktopVirtualization/hostpools/sessionhosts/delete | hostpools/sessionhosts を削除します |
> | Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/read | hostpools/sessionhosts/usersessions を読み取ります |
> | Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/write | hostpools/sessionhosts/usersessions を書き込みます |
> | Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/delete | hostpools/sessionhosts/usersessions を削除します |
> | Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/disconnect/action | ユーザー セッションをセッション ホストから切断します |
> | Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/sendMessage/action | ユーザー セッションにメッセージを送信します |
> | Microsoft.DesktopVirtualization/workspaces/read | ワークスペースを読み取ります |
> | Microsoft.DesktopVirtualization/workspaces/write | ワークスペースを書き込みます |
> | Microsoft.DesktopVirtualization/workspaces/delete | ワークスペースを削除します |
> | Microsoft.DesktopVirtualization/workspaces/providers/Microsoft.Insights/diagnosticSettings/read | 診断設定を取得します |
> | Microsoft.DesktopVirtualization/workspaces/providers/Microsoft.Insights/diagnosticSettings/write | 診断設定を作成または更新します |
> | Microsoft.DesktopVirtualization/workspaces/providers/Microsoft.Insights/logDefinitions/read | 利用可能なログを取得します |
> | **DataAction** | **説明** |
> | Microsoft.DesktopVirtualization/applicationgroups/useapplications/action | ApplicationGroup を使用します。 |

### <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

Azure サービス:[Azure Digital Twins](../digital-twins/index.yml)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.DigitalTwins/register/action | Digital Twins リソース プロバイダーにサブスクリプションを登録し、Digital Twins インスタンスを作成できるようにします。 |
> | Microsoft.DigitalTwins/unregister/action | Digital Twins リソース プロバイダーに対するサブスクリプションの登録を解除します |
> | Microsoft.DigitalTwins/digitalTwinsInstances/read | Microsoft.DigitalTwins/digitalTwinsInstances リソースを読み取ります |
> | Microsoft.DigitalTwins/digitalTwinsInstances/write | Microsoft.DigitalTwins/digitalTwinsInstances リソースを作成または更新します |
> | Microsoft.DigitalTwins/digitalTwinsInstances/delete | Microsoft.DigitalTwins/digitalTwinsInstances リソースを削除します |
> | Microsoft.DigitalTwins/digitalTwinsInstances/diagnosticSettings/read | リソースの診断設定を取得します。 |
> | Microsoft.DigitalTwins/digitalTwinsInstances/diagnosticSettings/write | リソースの診断設定を設定します |
> | Microsoft.DigitalTwins/digitalTwinsInstances/endpoints/delete | Digital Twins リソースのエンドポイントを削除します |
> | Microsoft.DigitalTwins/digitalTwinsInstances/endpoints/read | Digital Twins リソースのエンドポイントを読み取ります |
> | Microsoft.DigitalTwins/digitalTwinsInstances/endpoints/write | Digital Twins リソースのエンドポイントを作成または更新します |
> | Microsoft.DigitalTwins/digitalTwinsInstances/logDefinitions/read | リソースの Azure Monitor のログ設定を取得します |
> | Microsoft.DigitalTwins/digitalTwinsInstances/metricDefinitions/read | リソースの Azure Monitor のメトリック設定を取得します |
> | Microsoft.DigitalTwins/digitalTwinsInstances/operationsResults/read | 操作の結果を読み取ります |
> | Microsoft.DigitalTwins/digitalTwinsInstances/privateEndpointConnectionProxies/validate/action | PrivateEndpointConnectionProxies リソースを検証します |
> | Microsoft.DigitalTwins/digitalTwinsInstances/privateEndpointConnectionProxies/read | PrivateEndpointConnectionProxies リソースを読み取ります |
> | Microsoft.DigitalTwins/digitalTwinsInstances/privateEndpointConnectionProxies/write | PrivateEndpointConnectionProxies リソースを書き込みます |
> | Microsoft.DigitalTwins/digitalTwinsInstances/privateEndpointConnectionProxies/delete | PrivateEndpointConnectionProxies リソースを削除します |
> | Microsoft.DigitalTwins/digitalTwinsInstances/privateEndpointConnectionProxies/operationResults/read | プライベート エンドポイント接続プロキシの非同期操作の結果を取得します |
> | Microsoft.DigitalTwins/digitalTwinsInstances/privateEndpointConnections/read | PrivateEndpointConnection リソースを読み取ります |
> | Microsoft.DigitalTwins/digitalTwinsInstances/privateEndpointConnections/write | PrivateEndpointConnection リソースを書き込みます |
> | Microsoft.DigitalTwins/digitalTwinsInstances/privateEndpointConnections/delete | PrivateEndpointConnection リソースを削除します |
> | Microsoft.DigitalTwins/digitalTwinsInstances/privateEndpointConnections/operationResults/read | プライベート エンドポイント接続の非同期操作の結果を取得します |
> | Microsoft.DigitalTwins/digitalTwinsInstances/privateLinkResources/read | Digital Twins の PrivateLinkResources を読み取ります |
> | Microsoft.DigitalTwins/locations/checkNameAvailability/action | Digital Twins リソース プロバイダーでのリソースの名前の可用性を確認します |
> | Microsoft.DigitalTwins/locations/operationsResults/read | 操作の結果を読み取ります |
> | Microsoft.DigitalTwins/operations/read | すべての操作を読み取ります |
> | **DataAction** | **説明** |
> | Microsoft.DigitalTwins/query/action | Digital Twins グラフのクエリを実行します |
> | Microsoft.DigitalTwins/digitaltwins/read | デジタル ツインを読み取ります |
> | Microsoft.DigitalTwins/digitaltwins/write | デジタル ツインを作成または更新します |
> | Microsoft.DigitalTwins/digitaltwins/delete | デジタル ツインを削除します |
> | Microsoft.DigitalTwins/digitaltwins/commands/action | デジタル ツインで任意のコマンドを呼び出します |
> | Microsoft.DigitalTwins/digitaltwins/relationships/read | デジタル ツインの関係を読み取ります |
> | Microsoft.DigitalTwins/digitaltwins/relationships/write | デジタル ツインの関係を作成または更新します |
> | Microsoft.DigitalTwins/digitaltwins/relationships/delete | デジタル ツインの関係を削除します |
> | Microsoft.DigitalTwins/eventroutes/read | イベント ルートを読み取ります |
> | Microsoft.DigitalTwins/eventroutes/delete | イベント ルートを削除します |
> | Microsoft.DigitalTwins/eventroutes/write | イベント ルートを作成または更新します |
> | Microsoft.DigitalTwins/models/read | モデルを読み取ります |
> | Microsoft.DigitalTwins/models/write | モデルを作成または更新します |
> | Microsoft.DigitalTwins/models/delete | モデルを削除します |

### <a name="microsoftserviceshub"></a>Microsoft.ServicesHub

Azure サービス:[サービス ハブ](/services-hub/)

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | Microsoft.ServicesHub/connectors/write | サービス ハブ コネクタを作成または更新します |
> | Microsoft.ServicesHub/connectors/read | サービス ハブ コネクタを表示または一覧表示します |
> | Microsoft.ServicesHub/connectors/delete | サービス ハブ コネクタを削除します |
> | Microsoft.ServicesHub/connectors/checkAssessmentEntitlement/action | 指定されたサービス ハブ ワークスペースの評価エンタイトルメントを一覧表示します |
> | Microsoft.ServicesHub/supportOfferingEntitlement/read | 指定されたサービス ハブ ワークスペースのサポート オファリングのエンタイトルメントを表示します |
> | Microsoft.ServicesHub/workspaces/read | 指定されたユーザーのサービス ハブ ワークスペースを一覧表示します |

## <a name="next-steps"></a>次のステップ

- [リソース プロバイダーとサービスの対応](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Azure 組み込みロール](built-in-roles.md)
- [クラウド導入フレームワーク:Azure でのリソース アクセス管理](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management)
