---
title: Azure Resource Manager のリソースの種類に応じた完全モードの削除
description: リソースの種類に応じて Azure Resource Manager テンプレートで完全モードがどのように処理されるかを示します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 04/24/2019
ms.author: tomfitz
ms.openlocfilehash: 5ac442c0ae1e397fd1e8b58fdbcf61eb8712046c
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302837"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>完全モード デプロイでの Azure リソースの削除
この記事では、リソースの種類に応じて、完全モードにデプロイされているテンプレート以外での削除の方法がどのように異なるかを説明します。

`Yes` とマークされたリソースの種類は、種類が完全モードでデプロイされているテンプレート内にない場合に削除されます。 

`No` とマークされたリソースの種類は、テンプレート内にない場合に自動的に削除されませんが、親リソースが削除されていると削除されます。 動作の詳細については、「[Azure Resource Manager のデプロイ モード](deployment-modes.md)」をご覧ください。


## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | DomainServices | はい | 
> | DomainServices/oucontainer | いいえ | 

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | diagnosticSettings | いいえ | 
> | diagnosticSettingsCategories | いいえ | 

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | supportProviders | いいえ | 

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | aadsupportcases | いいえ | 
> | addsservices | いいえ | 
> | agents | いいえ | 
> | anonymousapiusers | いいえ | 
> | configuration | いいえ | 
> | logs | いいえ | 
> | reports | いいえ | 
> | services | いいえ | 

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | configuration | いいえ | 
> | generateRecommendations | いいえ | 
> | recommendations | いいえ | 
> | suppressions | いいえ | 

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | actionRules | いいえ | 
> | alerts | いいえ | 
> | alertsList | いいえ | 
> | alertsSummary | いいえ | 
> | alertsSummaryList | いいえ | 
> | smartDetectorAlertRules | いいえ | 
> | smartDetectorRuntimeEnvironments | いいえ | 
> | smartGroups | いいえ | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | servers | はい | 

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | reportFeedback | いいえ | 
> | service | はい | 
> | validateServiceName | いいえ | 

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | attestationProviders | いいえ | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | classicAdministrators | いいえ | 
> | denyAssignments | いいえ | 
> | elevateAccess | いいえ | 
> | locks | いいえ | 
> | permissions | いいえ | 
> | policyAssignments | いいえ | 
> | policyDefinitions | いいえ | 
> | policySetDefinitions | いいえ | 
> | providerOperations | いいえ | 
> | roleAssignments | いいえ | 
> | roleDefinitions | いいえ | 

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | automationAccounts | はい | 
> | automationAccounts/configurations | はい | 
> | automationAccounts/jobs | いいえ | 
> | automationAccounts/runbooks | はい | 
> | automationAccounts/softwareUpdateConfigurations | いいえ | 
> | automationAccounts/webhooks | いいえ | 

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | 環境 | いいえ | 
> | environments/accounts | いいえ | 
> | environments/accounts/namespaces | いいえ | 
> | environments/accounts/namespaces/configurations | いいえ | 

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | b2cDirectories | はい | 

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | registrations | はい | 
> | registrations/customerSubscriptions | いいえ | 
> | registrations/products | いいえ | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | batchAccounts | はい | 

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | billingAccounts | いいえ | 
> | billingAccounts/billingProfiles | いいえ | 
> | billingAccounts/billingProfiles/billingSubscriptions | いいえ | 
> | billingAccounts/billingProfiles/invoices | いいえ | 
> | billingAccounts/billingProfiles/invoices/pricesheet | いいえ | 
> | billingAccounts/billingProfiles/operationStatus | いいえ | 
> | billingAccounts/billingProfiles/paymentMethods | いいえ | 
> | billingAccounts/billingProfiles/policies | いいえ | 
> | billingAccounts/billingProfiles/pricesheet | いいえ | 
> | billingAccounts/billingProfiles/products | いいえ | 
> | billingAccounts/billingProfiles/transactions | いいえ | 
> | billingAccounts/billingSubscriptions | いいえ | 
> | billingAccounts/departments | いいえ | 
> | billingAccounts/eligibleOffers | いいえ | 
> | billingAccounts/enrollmentAccounts | いいえ | 
> | billingAccounts/invoices | いいえ | 
> | billingAccounts/invoiceSections | いいえ | 
> | billingAccounts/invoiceSections/billingSubscriptions | いいえ | 
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | いいえ | 
> | billingAccounts/invoiceSections/importRequests | いいえ | 
> | billingAccounts/invoiceSections/initiateImportRequest | いいえ | 
> | billingAccounts/invoiceSections/initiateTransfer | いいえ | 
> | billingAccounts/invoiceSections/operationStatus | いいえ | 
> | billingAccounts/invoiceSections/products | いいえ | 
> | billingAccounts/invoiceSections/transfers | いいえ | 
> | billingAccounts/products | いいえ | 
> | billingAccounts/projects | いいえ | 
> | billingAccounts/projects/billingSubscriptions | いいえ | 
> | billingAccounts/projects/importRequests | いいえ | 
> | billingAccounts/projects/initiateImportRequest | いいえ | 
> | billingAccounts/projects/operationStatus | いいえ | 
> | billingAccounts/projects/products | いいえ | 
> | billingAccounts/transactions | いいえ | 
> | billingPeriods | いいえ | 
> | BillingPermissions | いいえ | 
> | billingProperty | いいえ | 
> | BillingRoleAssignments | いいえ | 
> | BillingRoleDefinitions | いいえ | 
> | CreateBillingRoleAssignment | いいえ | 
> | departments | いいえ | 
> | enrollmentAccounts | いいえ | 
> | importRequests | いいえ | 
> | importRequests/acceptImportRequest | いいえ | 
> | importRequests/declineImportRequest | いいえ | 
> | invoices | いいえ | 
> | transfers | いいえ | 
> | transfers/acceptTransfer | いいえ | 
> | transfers/declineTransfer | いいえ | 
> | transfers/operationStatus | いいえ | 
> | usagePlans | いいえ | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | mapApis | はい | 
> | updateCommunicationPreference | いいえ | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | BizTalk | はい | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | blueprintAssignments | いいえ | 
> | blueprintAssignments/assignmentOperations | いいえ | 
> | blueprintAssignments/operations | いいえ | 
> | blueprints | いいえ | 
> | blueprints/artifacts | いいえ | 
> | blueprints/versions | いいえ | 
> | blueprints/versions/artifacts | いいえ | 

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | botServices | はい | 
> | botServices/channels | いいえ | 
> | botServices/connections | いいえ | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | Redis | はい | 
> | RedisConfigDefinition | いいえ | 

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | appliedReservations | いいえ | 
> | calculatePrice | いいえ | 
> | catalogs | いいえ | 
> | commercialReservationOrders | いいえ | 
> | reservationOrders | いいえ | 
> | reservationOrders/calculateRefund | いいえ | 
> | reservationOrders/merge | いいえ | 
> | reservationOrders/reservations | いいえ | 
> | reservationOrders/reservations/revisions | いいえ | 
> | reservationOrders/return | いいえ | 
> | reservationOrders/split | いいえ | 
> | reservationOrders/swap | いいえ | 
> | reservations | いいえ | 
> | resources | いいえ | 
> | validateReservationOrder | いいえ | 

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | edgenodes | いいえ | 
> | profiles | はい | 
> | profiles/endpoints | はい | 
> | profiles/endpoints/customdomains | いいえ | 
> | profiles/endpoints/origins | いいえ | 
> | validateProbe | いいえ | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | certificateOrders | はい | 
> | certificateOrders/certificates | いいえ | 
> | validateCertificateRegistrationInformation | いいえ | 

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | capabilities | いいえ | 
> | domainNames | いいえ | 
> | domainNames/capabilities | いいえ | 
> | domainNames/internalLoadBalancers | いいえ | 
> | domainNames/serviceCertificates | いいえ | 
> | domainNames/slots | いいえ | 
> | domainNames/slots/roles | いいえ | 
> | moveSubscriptionResources | いいえ | 
> | operatingSystemFamilies | いいえ | 
> | operatingSystems | いいえ | 
> | quotas | いいえ | 
> | resourceTypes | いいえ | 
> | validateSubscriptionMoveAvailability | いいえ | 
> | virtualMachines | いいえ | 
> | virtualMachines/diagnosticSettings | いいえ | 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | classicInfrastructureResources | いいえ | 

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | capabilities | いいえ | 
> | expressRouteCrossConnections | いいえ | 
> | expressRouteCrossConnections/peerings | いいえ | 
> | gatewaySupportedDevices | いいえ | 
> | networkSecurityGroups | いいえ | 
> | quotas | いいえ | 
> | reservedIps | いいえ | 
> | virtualNetworks | いいえ | 
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | いいえ | 
> | virtualNetworks/virtualNetworkPeerings | いいえ | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | capabilities | いいえ | 
> | disks | いいえ | 
> | images | いいえ | 
> | osImages | いいえ | 
> | osPlatformImages | いいえ | 
> | publicImages | いいえ | 
> | quotas | いいえ | 
> | storageAccounts | いいえ | 
> | storageAccounts/services | いいえ | 
> | storageAccounts/services/diagnosticSettings | いいえ | 
> | storageAccounts/vmImages | いいえ | 
> | vmImages | いいえ | 

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | accounts | はい | 

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | RateCard | いいえ | 
> | UsageAggregates | いいえ | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | availabilitySets | はい | 
> | disks | はい | 
> | images | はい | 
> | restorePointCollections | はい | 
> | restorePointCollections/restorePoints | いいえ | 
> | sharedVMImages | はい | 
> | sharedVMImages/versions | はい | 
> | snapshots | はい | 
> | virtualMachines | はい | 
> | virtualMachines/diagnosticSettings | いいえ | 
> | virtualMachines/extensions | はい | 
> | virtualMachineScaleSets | はい | 
> | virtualMachineScaleSets/extensions | いいえ | 
> | virtualMachineScaleSets/networkInterfaces | いいえ | 
> | virtualMachineScaleSets/publicIPAddresses | いいえ | 
> | virtualMachineScaleSets/virtualMachines | いいえ | 
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | いいえ | 

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | AggregatedCost | いいえ | 
> | Balances | いいえ | 
> | Budgets | いいえ | 
> | Charges | いいえ | 
> | CostTags | いいえ | 
> | credits | いいえ | 
> | events | いいえ | 
> | Forecasts | いいえ | 
> | lots | いいえ | 
> | Marketplaces | いいえ | 
> | Pricesheets | いいえ | 
> | products | いいえ | 
> | ReservationDetails | いいえ | 
> | ReservationRecommendations | いいえ | 
> | ReservationSummaries | いいえ | 
> | ReservationTransactions | いいえ | 
> | Tags | いいえ | 
> | Terms | いいえ | 
> | UsageDetails | いいえ | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | containerGroups | はい | 
> | serviceAssociationLinks | いいえ | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | registries | はい | 
> | registries/builds | いいえ | 
> | registries/builds/cancel | いいえ | 
> | registries/builds/getLogLink | いいえ | 
> | registries/buildTasks | はい | 
> | registries/buildTasks/steps | いいえ | 
> | registries/eventGridFilters | いいえ | 
> | registries/getBuildSourceUploadUrl | いいえ | 
> | registries/GetCredentials | いいえ | 
> | registries/importImage | いいえ | 
> | registries/queueBuild | いいえ | 
> | registries/regenerateCredential | いいえ | 
> | registries/regenerateCredentials | いいえ | 
> | registries/replications | はい | 
> | registries/runs | いいえ | 
> | registries/runs/cancel | いいえ | 
> | registries/scheduleRun | いいえ | 
> | registries/tasks | はい | 
> | registries/updatePolicies | いいえ | 
> | registries/webhooks | はい | 
> | registries/webhooks/getCallbackConfig | いいえ | 
> | registries/webhooks/ping | いいえ | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | containerServices | はい | 
> | managedClusters | はい | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | applications | はい | 
> | updateCommunicationPreference | いいえ | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | accounts | はい | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | アラート | いいえ | 
> | BillingAccounts | いいえ | 
> | Connectors | はい | 
> | Departments | いいえ | 
> | Dimensions | いいえ | 
> | EnrollmentAccounts | いいえ | 
> | Query | いいえ | 
> | registrations | いいえ | 
> | Reportconfigs | いいえ | 
> | Reports | いいえ | 

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | hubs | はい | 
> | hubs/authorizationPolicies | いいえ | 
> | hubs/connectors | いいえ | 
> | hubs/connectors/mappings | いいえ | 
> | hubs/interactions | いいえ | 
> | hubs/kpi | いいえ | 
> | hubs/links | いいえ | 
> | hubs/profiles | いいえ | 
> | hubs/roleAssignments | いいえ | 
> | hubs/roles | いいえ | 
> | hubs/suggestTypeSchema | いいえ | 
> | hubs/views | いいえ | 
> | hubs/widgetTypes | いいえ | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | jobs | はい | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | はい | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | workspaces | はい | 
> | workspaces/virtualNetworkPeerings | いいえ | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | catalogs | はい | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | connectionManagers | はい | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | dataFactories | はい | 
> | dataFactories/diagnosticSettings | いいえ | 
> | dataFactorySchema | いいえ | 
> | factories | はい | 
> | factories/integrationRuntimes | いいえ | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | accounts | はい | 
> | accounts/dataLakeStoreAccounts | いいえ | 
> | accounts/storageAccounts | いいえ | 
> | accounts/storageAccounts/containers | いいえ | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | accounts | はい | 
> | accounts/eventGridFilters | いいえ | 
> | accounts/firewallRules | いいえ | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | services | はい | 
> | services/projects | はい | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | servers | はい | 
> | servers/recoverableServers | いいえ | 
> | servers/virtualNetworkRules | いいえ | 

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | servers | はい | 
> | servers/recoverableServers | いいえ | 
> | servers/virtualNetworkRules | いいえ | 

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | servers | はい | 
> | servers/advisors | いいえ | 
> | servers/queryTexts | いいえ | 
> | servers/recoverableServers | いいえ | 
> | servers/topQueryStatistics | いいえ | 
> | servers/virtualNetworkRules | いいえ | 
> | servers/waitStatistics | いいえ | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | IotHubs | はい | 
> | IotHubs/eventGridFilters | いいえ | 
> | ProvisioningServices | はい | 
> | usages | いいえ | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | controllers | はい | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | labs | はい | 
> | labs/serviceRunners | はい | 
> | labs/virtualMachines | はい | 
> | schedules | はい | 

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | databaseAccountNames | いいえ | 
> | databaseAccounts | はい | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | domains | はい | 
> | domains/domainOwnershipIdentifiers | いいえ | 
> | generateSsoRequest | いいえ | 
> | topLevelDomains | いいえ | 
> | validateDomainRegistrationInformation | いいえ | 

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | lcsprojects | いいえ | 
> | lcsprojects/clouddeployments | いいえ | 
> | lcsprojects/connectors | いいえ | 

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | domains | はい | 
> | domains/topics | いいえ | 
> | eventSubscriptions | いいえ | 
> | extensionTopics | いいえ | 
> | topics | はい | 
> | topicTypes | いいえ | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | clusters | はい | 
> | namespaces | はい | 
> | namespaces/authorizationrules | いいえ | 
> | namespaces/disasterrecoveryconfigs | いいえ | 
> | namespaces/eventhubs | いいえ | 
> | namespaces/eventhubs/authorizationrules | いいえ | 
> | namespaces/eventhubs/consumergroups | いいえ | 

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | features | いいえ | 
> | providers | いいえ | 

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | enroll | いいえ | 
> | galleryitems | いいえ | 
> | generateartifactaccessuri | いいえ | 
> | myareas | いいえ | 
> | myareas/areas | いいえ | 
> | myareas/areas/areas | いいえ | 
> | myareas/areas/areas/galleryitems | いいえ | 
> | myareas/areas/galleryitems | いいえ | 
> | myareas/galleryitems | いいえ | 
> | registrations | いいえ | 
> | resources | いいえ | 
> | retrieveresourcesbyid | いいえ | 

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | guestConfigurationAssignments | いいえ | 
> | software | いいえ | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | hanaInstances | はい | 

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | clusters | はい | 
> | clusters/applications | いいえ | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | jobs | はい | 

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | labelGroups | いいえ | 
> | labelGroups/labels | いいえ | 
> | labelGroups/labels/conditions | いいえ | 
> | labelGroups/labels/subLabels | いいえ | 
> | labelGroups/labels/subLabels/conditions | いいえ | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | actiongroups | はい | 
> | activityLogAlerts | はい | 
> | alertrules | はい | 
> | automatedExportSettings | いいえ | 
> | autoscalesettings | はい | 
> | baseline | いいえ | 
> | calculatebaseline | いいえ | 
> | components | はい | 
> | components/events | いいえ | 
> | components/pricingPlans | いいえ | 
> | components/query | いいえ | 
> | diagnosticSettings | いいえ | 
> | diagnosticSettingsCategories | いいえ | 
> | eventCategories | いいえ | 
> | eventtypes | いいえ | 
> | extendedDiagnosticSettings | いいえ | 
> | logDefinitions | いいえ | 
> | logprofiles | いいえ | 
> | logs | いいえ | 
> | metricAlerts | はい |
> | migrateToNewPricingModel | いいえ | 
> | myWorkbooks | いいえ | 
> | Query | いいえ | 
> | rollbackToLegacyPricingModel | いいえ | 
> | scheduledqueryrules | はい | 
> | vmInsightsOnboardingStatuses | いいえ | 
> | webtests | はい | 
> | Workbooks | はい | 

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | diagnosticSettings | いいえ | 
> | diagnosticSettingsCategories | いいえ | 

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | IoTApps | はい | 

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | Graph | はい | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | deletedVaults | いいえ | 
> | vaults | はい | 
> | vaults/accessPolicies | いいえ | 
> | vaults/secrets | いいえ | 

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | clusters | はい | 
> | clusters/databases | いいえ | 
> | clusters/databases/dataconnections | いいえ | 
> | clusters/databases/eventhubconnections | いいえ | 

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | labaccounts | はい | 
> | users | いいえ | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | accounts | はい | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | accounts | はい | 

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | logs | いいえ | 

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | integrationAccounts | はい | 
> | workflows | はい | 

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | commitmentPlans | はい | 
> | webServices | はい | 
> | Workspaces | はい | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | accounts | はい | 
> | accounts/workspaces | はい | 
> | accounts/workspaces/プロジェクト | はい | 
> | teamAccounts | はい | 
> | teamAccounts/workspaces | はい | 
> | teamAccounts/workspaces/projects | はい | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | accounts | はい | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | workspaces | はい | 
> | workspaces/computes | いいえ | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | Identities | いいえ | 
> | userAssignedIdentities | はい | 

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | getEntities | いいえ | 
> | managementGroups | いいえ | 
> | resources | いいえ | 
> | startTenantBackfill | いいえ | 
> | tenantBackfillStatus | いいえ | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | accounts | はい | 
> | accounts/eventGridFilters | いいえ | 

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | offers | いいえ | 
> | offerTypes | いいえ | 
> | offerTypes/publishers | いいえ | 
> | offerTypes/publishers/offers | いいえ | 
> | offerTypes/publishers/offers/plans | いいえ | 
> | offerTypes/publishers/offers/plans/agreements | いいえ | 
> | offerTypes/publishers/offers/plans/configs | いいえ | 
> | offerTypes/publishers/offers/plans/configs/importImage | いいえ | 
> | privategalleryitems | いいえ | 
> | products | いいえ | 

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | classicDevServices | はい | 
> | updateCommunicationPreference | いいえ | 

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | agreements | いいえ | 
> | offertypes | いいえ | 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | mediaservices | はい | 
> | mediaservices/accountFilters | いいえ | 
> | mediaservices/assets | いいえ | 
> | mediaservices/assets/assetFilters | いいえ | 
> | mediaservices/contentKeyPolicies | いいえ | 
> | mediaservices/eventGridFilters | いいえ | 
> | mediaservices/liveEventOperations | いいえ | 
> | mediaservices/liveEvents | はい | 
> | mediaservices/liveEvents/liveOutputs | いいえ | 
> | mediaservices/liveOutputOperations | いいえ | 
> | mediaservices/streamingEndpointOperations | いいえ | 
> | mediaservices/streamingEndpoints | はい | 
> | mediaservices/streamingLocators | いいえ | 
> | mediaservices/streamingPolicies | いいえ | 
> | mediaservices/transforms | いいえ | 
> | mediaservices/transforms/jobs | いいえ | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | projects | はい | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | applicationGateways | はい | 
> | applicationSecurityGroups | はい | 
> | azureFirewallFqdnTags | いいえ | 
> | azureFirewalls | はい | 
> | bgpServiceCommunities | いいえ | 
> | connections | はい | 
> | ddosCustomPolicies | はい | 
> | ddosProtectionPlans | はい | 
> | dnsOperationStatuses | いいえ | 
> | dnszones | はい | 
> | dnszones/A | いいえ | 
> | dnszones/AAAA | いいえ | 
> | dnszones/all | いいえ | 
> | dnszones/CAA | いいえ | 
> | dnszones/CNAME | いいえ | 
> | dnszones/MX | いいえ | 
> | dnszones/NS | いいえ | 
> | dnszones/PTR | いいえ | 
> | dnszones/recordsets | いいえ | 
> | dnszones/SOA | いいえ | 
> | dnszones/SRV | いいえ | 
> | dnszones/TXT | いいえ | 
> | expressRouteCircuits | はい | 
> | expressRouteServiceProviders | いいえ | 
> | frontdoors | はい | 
> | frontdoorWebApplicationFirewallPolicies | はい | 
> | getDnsResourceReference | いいえ | 
> | interfaceEndpoints | はい | 
> | internalNotify | いいえ | 
> | loadBalancers | はい | 
> | localNetworkGateways | はい | 
> | natGateways | はい | 
> | networkIntentPolicies | はい | 
> | networkInterfaces | はい | 
> | networkProfiles | はい | 
> | networkSecurityGroups | はい | 
> | networkWatchers | はい | 
> | networkWatchers/connectionMonitors | はい | 
> | networkWatchers/lenses | はい | 
> | networkWatchers/pingMeshes | はい | 
> | privateLinkServices | はい | 
> | publicIPAddresses | はい | 
> | publicIPPrefixes | はい | 
> | routeFilters | はい | 
> | routeTables | はい | 
> | serviceEndpointPolicies | はい | 
> | trafficManagerGeographicHierarchies | いいえ | 
> | trafficmanagerprofiles | はい | 
> | trafficmanagerprofiles/heatMaps | いいえ | 
> | virtualHubs | はい | 
> | virtualNetworkGateways | はい | 
> | virtualNetworks | はい | 
> | virtualNetworkTaps | はい | 
> | virtualWans | はい | 
> | vpnGateways | はい | 
> | vpnSites | はい | 
> | webApplicationFirewallPolicies | はい | 

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | namespaces | はい | 
> | namespaces/notificationHubs | はい | 

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | devices | いいえ | 
> | linkTargets | いいえ | 
> | storageInsightConfigs | いいえ | 
> | workspaces | はい | 
> | workspaces/dataSources | いいえ | 
> | workspaces/linkedServices | いいえ | 
> | workspaces/query | いいえ | 

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | managementassociations | いいえ | 
> | managementconfigurations | はい | 
> | solutions | はい | 
> | views | はい | 

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | policyEvents | いいえ | 
> | policyStates | いいえ | 
> | policyTrackedResources | いいえ | 
> | remediations | いいえ | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | consoles | いいえ | 
> | dashboards | はい | 
> | userSettings | いいえ | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | workspaceCollections | はい | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | capacities | はい | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | accounts | はい | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | backupProtectedItems | いいえ | 
> | vaults | はい | 

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | namespaces | はい | 
> | namespaces/authorizationrules | いいえ | 
> | namespaces/hybridconnections | いいえ | 
> | namespaces/hybridconnections/authorizationrules | いいえ | 
> | namespaces/wcfrelays | いいえ | 
> | namespaces/wcfrelays/authorizationrules | いいえ | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | resources | いいえ | 
> | subscriptionsStatus | いいえ | 

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | availabilityStatuses | いいえ | 
> | childAvailabilityStatuses | いいえ | 
> | childResources | いいえ | 
> | events | いいえ | 
> | impactedResources | いいえ | 
> | notifications | いいえ | 

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | deployments | いいえ | 
> | deployments/operations | いいえ | 
> | links | いいえ | 
> | notifyResourceJobs | いいえ | 
> | providers | いいえ | 
> | resourceGroups | いいえ | 
> | resources | いいえ | 
> | subscriptions | いいえ | 
> | subscriptions/providers | いいえ | 
> | subscriptions/resourceGroups | いいえ | 
> | subscriptions/resourcegroups/resources | いいえ | 
> | subscriptions/resources | いいえ | 
> | subscriptions/tagnames | いいえ | 
> | subscriptions/tagNames/tagValues | いいえ | 
> | tenants | いいえ | 

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | applications | はい | 
> | saasresources | いいえ | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | flows | はい | 
> | jobcollections | はい | 

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | resourceHealthMetadata | いいえ | 
> | searchServices | はい | 

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | advancedThreatProtectionSettings | いいえ | 
> | alerts | いいえ | 
> | allowedConnections | いいえ | 
> | appliances | いいえ | 
> | applicationWhitelistings | いいえ | 
> | AutoProvisioningSettings | いいえ | 
> | Compliances | いいえ | 
> | dataCollectionAgents | いいえ | 
> | discoveredSecuritySolutions | いいえ | 
> | externalSecuritySolutions | いいえ | 
> | InformationProtectionPolicies | いいえ | 
> | jitNetworkAccessPolicies | いいえ | 
> | monitoring | いいえ | 
> | monitoring/antimalware | いいえ | 
> | monitoring/baseline | いいえ | 
> | monitoring/patch | いいえ | 
> | policies | いいえ | 
> | pricings | いいえ | 
> | securityContacts | いいえ | 
> | securitySolutions | いいえ | 
> | securitySolutionsReferenceData | いいえ | 
> | securityStatus | いいえ | 
> | securityStatus/endpoints | いいえ | 
> | securityStatus/subnets | いいえ | 
> | securityStatus/virtualMachines | いいえ | 
> | securityStatuses | いいえ | 
> | securityStatusesSummaries | いいえ | 
> | settings | いいえ | 
> | tasks | いいえ | 
> | topologies | いいえ | 
> | workspaceSettings | いいえ | 

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | diagnosticSettings | いいえ | 
> | diagnosticSettingsCategories | いいえ | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | namespaces | はい | 
> | namespaces/authorizationrules | いいえ | 
> | namespaces/disasterrecoveryconfigs | いいえ | 
> | namespaces/eventgridfilters | いいえ | 
> | namespaces/queues | いいえ | 
> | namespaces/queues/authorizationrules | いいえ | 
> | namespaces/topics | いいえ | 
> | namespaces/topics/authorizationrules | いいえ | 
> | namespaces/topics/subscriptions | いいえ | 
> | namespaces/topics/subscriptions/rules | いいえ | 
> | premiumMessagingRegions | いいえ | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | clusters | はい | 
> | clusters/applications | いいえ | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | applications | はい | 
> | gateways | はい | 
> | networks | はい | 
> | secrets | はい | 
> | volumes | はい | 

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | SignalR | はい | 

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | applianceDefinitions | はい | 
> | appliances | はい | 
> | applicationDefinitions | はい | 
> | applications | はい | 
> | jitRequests | はい | 

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | managedInstances | はい |
> | managedInstances/databases | はい |
> | managedInstances/databases/backupShortTermRetentionPolicies | いいえ |
> | managedInstances/databases/schemas/tables/columns/sensitivityLabels | いいえ |
> | managedInstances/databases/vulnerabilityAssessments | いいえ |
> | managedInstances/databases/vulnerabilityAssessments/rules/baselines | いいえ |
> | managedInstances/encryptionProtector | いいえ |
> | managedInstances/keys | いいえ |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | いいえ |
> | managedInstances/vulnerabilityAssessments | いいえ |
> | servers | はい | 
> | servers/administrators | いいえ | 
> | servers/communicationLinks | いいえ | 
> | servers/databases | はい | 
> | servers/encryptionProtector | いいえ | 
> | servers/firewallRules | いいえ | 
> | servers/keys | いいえ | 
> | servers/restorableDroppedDatabases | いいえ | 
> | servers/serviceobjectives | いいえ | 
> | servers/tdeCertificates | いいえ | 


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | はい | 
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | いいえ | 
> | SqlVirtualMachines | はい | 

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | storageAccounts | はい | 
> | storageAccounts/blobServices | いいえ | 
> | storageAccounts/fileServices | いいえ | 
> | storageAccounts/queueServices | いいえ | 
> | storageAccounts/services | いいえ | 
> | storageAccounts/tableServices | いいえ | 
> | usages | いいえ | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | storageSyncServices | はい | 
> | storageSyncServices/registeredServers | いいえ | 
> | storageSyncServices/syncGroups | いいえ | 
> | storageSyncServices/syncGroups/cloudEndpoints | いいえ | 
> | storageSyncServices/syncGroups/serverEndpoints | いいえ | 
> | storageSyncServices/workflows | いいえ | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | managers | はい | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | streamingjobs | はい | 
> | streamingjobs/diagnosticSettings | いいえ | 

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | CreateSubscription | いいえ | 
> | SubscriptionDefinitions | いいえ | 
> | SubscriptionOperations | いいえ | 

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | supporttickets | いいえ | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | providerRegistrations | はい | 
> | resources | はい | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | 環境 | はい | 
> | environments/accessPolicies | いいえ | 
> | environments/eventsource | はい | 
> | environments/referenceDataSets | はい | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | account | はい | 
> | account/extension | はい | 
> | account/project | はい | 

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | apiManagementAccounts | いいえ | 
> | apiManagementAccounts/apiAcls | いいえ | 
> | apiManagementAccounts/apis | いいえ | 
> | apiManagementAccounts/apis/apiAcls | いいえ | 
> | apiManagementAccounts/apis/connectionAcls | いいえ | 
> | apiManagementAccounts/apis/connections | いいえ | 
> | apiManagementAccounts/apis/connections/connectionAcls | いいえ | 
> | apiManagementAccounts/apis/localizedDefinitions | いいえ | 
> | apiManagementAccounts/connectionAcls | いいえ | 
> | apiManagementAccounts/connections | いいえ | 
> | billingMeters | いいえ | 
> | certificates | はい | 
> | connectionGateways | はい | 
> | connections | はい | 
> | customApis | はい | 
> | deletedSites | いいえ | 
> | functions | いいえ | 
> | hostingEnvironments | はい | 
> | hostingEnvironments/multiRolePools | いいえ | 
> | hostingEnvironments/multiRolePools/instances | いいえ | 
> | hostingEnvironments/workerPools | いいえ | 
> | hostingEnvironments/workerPools/instances | いいえ | 
> | publishingUsers | いいえ | 
> | recommendations | いいえ | 
> | resourceHealthMetadata | いいえ | 
> | runtimes | いいえ | 
> | serverFarms | はい | 
> | serverFarms/workers | いいえ | 
> | sites | はい | 
> | sites/domainOwnershipIdentifiers | いいえ | 
> | sites/hostNameBindings | いいえ | 
> | sites/instances | いいえ | 
> | sites/instances/extensions | いいえ | 
> | sites/premieraddons | はい | 
> | sites/recommendations | いいえ | 
> | sites/resourceHealthMetadata | いいえ | 
> | sites/slots | はい | 
> | sites/slots/hostNameBindings | いいえ | 
> | sites/slots/instances | いいえ | 
> | sites/slots/instances/extensions | いいえ | 
> | sourceControls | いいえ | 
> | validate | いいえ | 
> | verifyHostingEnvironmentVnet | いいえ | 

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | diagnosticSettings | いいえ | 
> | diagnosticSettingsCategories | いいえ | 

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | DeviceServices | はい | 

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | components | いいえ | 
> | componentsSummary | いいえ | 
> | monitorInstances | いいえ | 
> | monitorInstancesSummary | いいえ | 
> | monitors | いいえ | 
> | notificationSettings | いいえ | 

## <a name="next-steps"></a>次の手順

コンマ区切りの値のファイルと同じデータを取得するには、[complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv) をダウンロードします。