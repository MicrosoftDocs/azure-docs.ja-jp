---
title: リソースでの Azure Resource Manager タグのサポート
description: タグをサポートしている Azure リソースの種類を示します。 すべての Azure サービスの詳細を提供します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 02/13/2019
ms.author: tomfitz
ms.openlocfilehash: dd6836ef4f859ce77c1a56095d32373d8e08f468
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2019
ms.locfileid: "56270388"
---
# <a name="tag-support-for-azure-resources"></a>Azure リソースでのタグのサポート
この記事では、リソースの種類が[タグ](resource-group-using-tags.md)をサポートしているかどうかについて説明します。

コンマ区切りの値のファイルと同じデータを取得するには、[tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv) をダウンロードします。

## <a name="microsoftaad"></a>Microsoft.AAD
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| DomainServices | はい | 
| DomainServices/oucontainer | いいえ  | 

## <a name="microsoftaadiam"></a>microsoft.aadiam
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| diagnosticSettings | いいえ  | 
| diagnosticSettingsCategories | いいえ  | 

## <a name="microsoftaddons"></a>Microsoft.Addons
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| supportProviders | いいえ  | 

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| aadsupportcases | いいえ  | 
| addsservices | いいえ  | 
| agents | いいえ  | 
| anonymousapiusers | いいえ  | 
| configuration | いいえ  | 
| logs | いいえ  | 
| reports | いいえ  | 
| services | いいえ  | 

## <a name="microsoftadvisor"></a>Microsoft.Advisor
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| configuration | いいえ  | 
| generateRecommendations | いいえ  | 
| recommendations | いいえ  | 
| suppressions | いいえ  | 

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| actionRules | いいえ  | 
| alerts | いいえ  | 
| alertsList | いいえ  | 
| alertsSummary | いいえ  | 
| alertsSummaryList | いいえ  | 
| smartDetectorAlertRules | いいえ  | 
| smartDetectorRuntimeEnvironments | いいえ  | 
| smartGroups | いいえ  | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| servers | はい | 

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| reportFeedback | いいえ  | 
| service | はい | 
| validateServiceName | いいえ  | 

## <a name="microsoftattestation"></a>Microsoft.Attestation
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| attestationProviders | いいえ  | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| classicAdministrators | いいえ  | 
| denyAssignments | いいえ  | 
| elevateAccess | いいえ  | 
| locks | いいえ  | 
| permissions | いいえ  | 
| policyAssignments | いいえ  | 
| policyDefinitions | いいえ  | 
| policySetDefinitions | いいえ  | 
| providerOperations | いいえ  | 
| roleAssignments | いいえ  | 
| roleDefinitions | いいえ  | 

## <a name="microsoftautomation"></a>Microsoft.Automation
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| automationAccounts | はい | 
| automationAccounts/configurations | はい | 
| automationAccounts/jobs | いいえ  | 
| automationAccounts/runbooks | はい | 
| automationAccounts/softwareUpdateConfigurations | いいえ  | 
| automationAccounts/webhooks | いいえ  | 

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| environments | いいえ  | 
| environments/accounts | いいえ  | 
| environments/accounts/namespaces | いいえ  | 
| environments/accounts/namespaces/configurations | いいえ  | 

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| b2cDirectories | はい | 

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| registrations | はい | 
| registrations/customerSubscriptions | いいえ  | 
| registrations/products | いいえ  | 

## <a name="microsoftbatch"></a>Microsoft.Batch
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| batchAccounts | はい | 

## <a name="microsoftbilling"></a>Microsoft.Billing
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| billingAccounts | いいえ  | 
| billingAccounts/billingProfiles | いいえ  | 
| billingAccounts/billingProfiles/billingSubscriptions | いいえ  | 
| billingAccounts/billingProfiles/invoices | いいえ  | 
| billingAccounts/billingProfiles/invoices/pricesheet | いいえ  | 
| billingAccounts/billingProfiles/operationStatus | いいえ  | 
| billingAccounts/billingProfiles/paymentMethods | いいえ  | 
| billingAccounts/billingProfiles/policies | いいえ  | 
| billingAccounts/billingProfiles/pricesheet | いいえ  | 
| billingAccounts/billingProfiles/products | いいえ  | 
| billingAccounts/billingProfiles/transactions | いいえ  | 
| billingAccounts/billingSubscriptions | いいえ  | 
| billingAccounts/departments | いいえ  | 
| billingAccounts/eligibleOffers | いいえ  | 
| billingAccounts/enrollmentAccounts | いいえ  | 
| billingAccounts/invoices | いいえ  | 
| billingAccounts/invoiceSections | いいえ  | 
| billingAccounts/invoiceSections/billingSubscriptions | いいえ  | 
| billingAccounts/invoiceSections/billingSubscriptions/transfer | いいえ  | 
| billingAccounts/invoiceSections/importRequests | いいえ  | 
| billingAccounts/invoiceSections/initiateImportRequest | いいえ  | 
| billingAccounts/invoiceSections/initiateTransfer | いいえ  | 
| billingAccounts/invoiceSections/operationStatus | いいえ  | 
| billingAccounts/invoiceSections/products | いいえ  | 
| billingAccounts/invoiceSections/transfers | いいえ  | 
| billingAccounts/products | いいえ  | 
| billingAccounts/projects | いいえ  | 
| billingAccounts/projects/billingSubscriptions | いいえ  | 
| billingAccounts/projects/importRequests | いいえ  | 
| billingAccounts/projects/initiateImportRequest | いいえ  | 
| billingAccounts/projects/operationStatus | いいえ  | 
| billingAccounts/projects/products | いいえ  | 
| billingAccounts/transactions | いいえ  | 
| billingPeriods | いいえ  | 
| BillingPermissions | いいえ  | 
| billingProperty | いいえ  | 
| BillingRoleAssignments | いいえ  | 
| BillingRoleDefinitions | いいえ  | 
| CreateBillingRoleAssignment | いいえ  | 
| departments | いいえ  | 
| enrollmentAccounts | いいえ  | 
| importRequests | いいえ  | 
| importRequests/acceptImportRequest | いいえ  | 
| importRequests/declineImportRequest | いいえ  | 
| invoices | いいえ  | 
| transfers | いいえ  | 
| transfers/acceptTransfer | いいえ  | 
| transfers/declineTransfer | いいえ  | 
| transfers/operationStatus | いいえ  | 
| usagePlans | いいえ  | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| mapApis | はい | 
| updateCommunicationPreference | いいえ  | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| BizTalk | はい | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| blueprintAssignments | いいえ  | 
| blueprintAssignments/assignmentOperations | いいえ  | 
| blueprintAssignments/operations | いいえ  | 
| blueprints | いいえ  | 
| blueprints/artifacts | いいえ  | 
| blueprints/versions | いいえ  | 
| blueprints/versions/artifacts | いいえ  | 

## <a name="microsoftbotservice"></a>Microsoft.BotService
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| botServices | はい | 
| botServices/channels | いいえ  | 
| botServices/connections | いいえ  | 

## <a name="microsoftcache"></a>Microsoft.Cache
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| Redis | はい | 
| RedisConfigDefinition | いいえ  | 

## <a name="microsoftcapacity"></a>Microsoft.Capacity
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| appliedReservations | いいえ  | 
| calculatePrice | いいえ  | 
| catalogs | いいえ  | 
| commercialReservationOrders | いいえ  | 
| reservationOrders | いいえ  | 
| reservationOrders/calculateRefund | いいえ  | 
| reservationOrders/merge | いいえ  | 
| reservationOrders/reservations | いいえ  | 
| reservationOrders/reservations/revisions | いいえ  | 
| reservationOrders/return | いいえ  | 
| reservationOrders/split | いいえ  | 
| reservationOrders/swap | いいえ  | 
| reservations | いいえ  | 
| resources | いいえ  | 
| validateReservationOrder | いいえ  | 

## <a name="microsoftcdn"></a>Microsoft.Cdn
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| edgenodes | いいえ  | 
| profiles | はい | 
| profiles/endpoints | はい | 
| profiles/endpoints/customdomains | いいえ  | 
| profiles/endpoints/origins | いいえ  | 
| validateProbe | いいえ  | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| certificateOrders | はい | 
| certificateOrders/certificates | いいえ  | 
| validateCertificateRegistrationInformation | いいえ  | 

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| capabilities | いいえ  | 
| domainNames | いいえ  | 
| domainNames/capabilities | いいえ  | 
| domainNames/internalLoadBalancers | いいえ  | 
| domainNames/serviceCertificates | いいえ  | 
| domainNames/slots | いいえ  | 
| domainNames/slots/roles | いいえ  | 
| moveSubscriptionResources | いいえ  | 
| operatingSystemFamilies | いいえ  | 
| operatingSystems | いいえ  | 
| quotas | いいえ  | 
| resourceTypes | いいえ  | 
| validateSubscriptionMoveAvailability | いいえ  | 
| virtualMachines | いいえ  | 
| virtualMachines/diagnosticSettings | いいえ  | 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| classicInfrastructureResources | いいえ  | 

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| capabilities | いいえ  | 
| expressRouteCrossConnections | いいえ  | 
| expressRouteCrossConnections/peerings | いいえ  | 
| gatewaySupportedDevices | いいえ  | 
| networkSecurityGroups | いいえ  | 
| quotas | いいえ  | 
| reservedIps | いいえ  | 
| virtualNetworks | いいえ  | 
| virtualNetworks/remoteVirtualNetworkPeeringProxies | いいえ  | 
| virtualNetworks/virtualNetworkPeerings | いいえ  | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| capabilities | いいえ  | 
| disks | いいえ  | 
| images | いいえ  | 
| osImages | いいえ  | 
| osPlatformImages | いいえ  | 
| publicImages | いいえ  | 
| quotas | いいえ  | 
| storageAccounts | いいえ  | 
| storageAccounts/services | いいえ  | 
| storageAccounts/services/diagnosticSettings | いいえ  | 
| storageAccounts/vmImages | いいえ  | 
| vmImages | いいえ  | 

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| accounts | はい | 

## <a name="microsoftcommerce"></a>Microsoft.Commerce
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| RateCard | いいえ  | 
| UsageAggregates | いいえ  | 

## <a name="microsoftcompute"></a>Microsoft.Compute
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| availabilitySets | はい | 
| disks | はい | 
| images | はい | 
| restorePointCollections | はい | 
| restorePointCollections/restorePoints | いいえ  | 
| sharedVMImages | はい | 
| sharedVMImages/versions | はい | 
| snapshots | はい | 
| virtualMachines | はい | 
| virtualMachines/diagnosticSettings | いいえ  | 
| virtualMachines/extensions | はい | 
| virtualMachineScaleSets | はい | 
| virtualMachineScaleSets/extensions | いいえ  | 
| virtualMachineScaleSets/networkInterfaces | いいえ  | 
| virtualMachineScaleSets/publicIPAddresses | いいえ  | 
| virtualMachineScaleSets/virtualMachines | いいえ  | 
| virtualMachineScaleSets/virtualMachines/networkInterfaces | いいえ  | 

## <a name="microsoftconsumption"></a>Microsoft.Consumption
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| AggregatedCost | いいえ  | 
| Balances | いいえ  | 
| Budgets | いいえ  | 
| Charges | いいえ  | 
| CostTags | いいえ  | 
| credits | いいえ  | 
| events | いいえ  | 
| Forecasts | いいえ  | 
| lots | いいえ  | 
| Marketplaces | いいえ  | 
| Pricesheets | いいえ  | 
| products | いいえ  | 
| ReservationDetails | いいえ  | 
| ReservationRecommendations | いいえ  | 
| ReservationSummaries | いいえ  | 
| ReservationTransactions | いいえ  | 
| Tags | いいえ  | 
| Terms | いいえ  | 
| UsageDetails | いいえ  | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| containerGroups | はい | 
| serviceAssociationLinks | いいえ  | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| registries | はい | 
| registries/builds | いいえ  | 
| registries/builds/cancel | いいえ  | 
| registries/builds/getLogLink | いいえ  | 
| registries/buildTasks | はい | 
| registries/buildTasks/steps | いいえ  | 
| registries/eventGridFilters | いいえ  | 
| registries/getBuildSourceUploadUrl | いいえ  | 
| registries/GetCredentials | いいえ  | 
| registries/importImage | いいえ  | 
| registries/queueBuild | いいえ  | 
| registries/regenerateCredential | いいえ  | 
| registries/regenerateCredentials | いいえ  | 
| registries/replications | はい | 
| registries/runs | いいえ  | 
| registries/runs/cancel | いいえ  | 
| registries/scheduleRun | いいえ  | 
| registries/tasks | はい | 
| registries/updatePolicies | いいえ  | 
| registries/webhooks | はい | 
| registries/webhooks/getCallbackConfig | いいえ  | 
| registries/webhooks/ping | いいえ  | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| containerServices | はい | 
| managedClusters | はい | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| applications | はい | 
| updateCommunicationPreference | いいえ  | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| accounts | はい | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| Alerts | いいえ  | 
| BillingAccounts | いいえ  | 
| Connectors | はい | 
| Departments | いいえ  | 
| Dimensions | いいえ  | 
| EnrollmentAccounts | いいえ  | 
| Query | いいえ  | 
| registrations | いいえ  | 
| Reportconfigs | いいえ  | 
| Reports | いいえ  | 

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| hubs | はい | 
| hubs/authorizationPolicies | いいえ  | 
| hubs/connectors | いいえ  | 
| hubs/connectors/mappings | いいえ  | 
| hubs/interactions | いいえ  | 
| hubs/kpi | いいえ  | 
| hubs/links | いいえ  | 
| hubs/profiles | いいえ  | 
| hubs/roleAssignments | いいえ  | 
| hubs/roles | いいえ  | 
| hubs/suggestTypeSchema | いいえ  | 
| hubs/views | いいえ  | 
| hubs/widgetTypes | いいえ  | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| jobs | はい | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| DataBoxEdgeDevices | はい | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| workspaces | はい | 
| workspaces/virtualNetworkPeerings | いいえ  | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| catalogs | はい | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| connectionManagers | はい | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| dataFactories | はい | 
| dataFactories/diagnosticSettings | いいえ  | 
| dataFactorySchema | いいえ  | 
| factories | はい | 
| factories/integrationRuntimes | いいえ  | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| accounts | はい | 
| accounts/dataLakeStoreAccounts | いいえ  | 
| accounts/storageAccounts | いいえ  | 
| accounts/storageAccounts/containers | いいえ  | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| accounts | はい | 
| accounts/eventGridFilters | いいえ  | 
| accounts/firewallRules | いいえ  | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| services | はい | 
| services/projects | はい | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| servers | はい | 
| servers/recoverableServers | いいえ  | 
| servers/virtualNetworkRules | いいえ  | 

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| servers | はい | 
| servers/recoverableServers | いいえ  | 
| servers/virtualNetworkRules | いいえ  | 

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| servers | はい | 
| servers/advisors | いいえ  | 
| servers/queryTexts | いいえ  | 
| servers/recoverableServers | いいえ  | 
| servers/topQueryStatistics | いいえ  | 
| servers/virtualNetworkRules | いいえ  | 
| servers/waitStatistics | いいえ  | 

## <a name="microsoftdevices"></a>Microsoft.Devices
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| IotHubs | はい | 
| IotHubs/eventGridFilters | いいえ  | 
| ProvisioningServices | はい | 
| usages | いいえ  | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| controllers | はい | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| labs | はい | 
| labs/serviceRunners | はい | 
| labs/virtualMachines | はい | 
| schedules | はい | 

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| databaseAccountNames | いいえ  | 
| databaseAccounts | はい | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| domains | はい | 
| domains/domainOwnershipIdentifiers | いいえ  | 
| generateSsoRequest | いいえ  | 
| topLevelDomains | いいえ  | 
| validateDomainRegistrationInformation | いいえ  | 

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| lcsprojects | いいえ  | 
| lcsprojects/clouddeployments | いいえ  | 
| lcsprojects/connectors | いいえ  | 

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| domains | はい | 
| domains/topics | いいえ  | 
| eventSubscriptions | いいえ  | 
| extensionTopics | いいえ  | 
| topics | はい | 
| topicTypes | いいえ  | 

## <a name="microsofteventhub"></a>Microsoft.EventHub
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| clusters | はい | 
| namespaces | はい | 
| namespaces/authorizationrules | いいえ  | 
| namespaces/disasterrecoveryconfigs | いいえ  | 
| namespaces/eventhubs | いいえ  | 
| namespaces/eventhubs/authorizationrules | いいえ  | 
| namespaces/eventhubs/consumergroups | いいえ  | 

## <a name="microsoftfeatures"></a>Microsoft.Features
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| features | いいえ  | 
| providers | いいえ  | 

## <a name="microsoftgallery"></a>Microsoft.Gallery
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| enroll | いいえ  | 
| galleryitems | いいえ  | 
| generateartifactaccessuri | いいえ  | 
| myareas | いいえ  | 
| myareas/areas | いいえ  | 
| myareas/areas/areas | いいえ  | 
| myareas/areas/areas/galleryitems | いいえ  | 
| myareas/areas/galleryitems | いいえ  | 
| myareas/galleryitems | いいえ  | 
| registrations | いいえ  | 
| resources | いいえ  | 
| retrieveresourcesbyid | いいえ  | 

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| guestConfigurationAssignments | いいえ  | 
| software | いいえ  | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| hanaInstances | はい | 

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| clusters | はい | 
| clusters/applications | いいえ  | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| jobs | はい | 

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| labelGroups | いいえ  | 
| labelGroups/labels | いいえ  | 
| labelGroups/labels/conditions | いいえ  | 
| labelGroups/labels/subLabels | いいえ  | 
| labelGroups/labels/subLabels/conditions | いいえ  | 

## <a name="microsoftinsights"></a>microsoft.insights
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| actiongroups | はい | 
| activityLogAlerts | はい | 
| alertrules | はい | 
| automatedExportSettings | いいえ  | 
| autoscalesettings | はい | 
| baseline | いいえ  | 
| calculatebaseline | いいえ  | 
| components | はい | 
| components/events | いいえ  | 
| components/pricingPlans | いいえ  | 
| components/query | いいえ  | 
| diagnosticSettings | いいえ  | 
| diagnosticSettingsCategories | いいえ  | 
| eventCategories | いいえ  | 
| eventtypes | いいえ  | 
| extendedDiagnosticSettings | いいえ  | 
| logDefinitions | いいえ  | 
| logprofiles | いいえ  | 
| logs | いいえ  | 
| metricAlerts | はい |
| migrateToNewPricingModel | いいえ  | 
| myWorkbooks | いいえ  | 
| Query | いいえ  | 
| rollbackToLegacyPricingModel | いいえ  | 
| scheduledqueryrules | はい | 
| vmInsightsOnboardingStatuses | いいえ  | 
| webtests | はい | 
| Workbooks | はい | 

## <a name="microsoftintune"></a>Microsoft.Intune
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| diagnosticSettings | いいえ  | 
| diagnosticSettingsCategories | いいえ  | 

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| IoTApps | はい | 

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| Graph | はい | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| deletedVaults | いいえ  | 
| vaults | はい | 
| vaults/accessPolicies | いいえ  | 
| vaults/secrets | いいえ  | 

## <a name="microsoftkusto"></a>Microsoft.Kusto
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| clusters | はい | 
| clusters/databases | いいえ  | 
| clusters/databases/dataconnections | いいえ  | 
| clusters/databases/eventhubconnections | いいえ  | 

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| labaccounts | はい | 
| users | いいえ  | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| accounts | はい | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| accounts | はい | 

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| logs | いいえ  | 

## <a name="microsoftlogic"></a>Microsoft.Logic
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| integrationAccounts | はい | 
| workflows | はい | 

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| commitmentPlans | はい | 
| webServices | はい | 
| Workspaces | はい | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| accounts | はい | 
| accounts/workspaces | はい | 
| accounts/workspaces/プロジェクト | はい | 
| teamAccounts | はい | 
| teamAccounts/workspaces | はい | 
| teamAccounts/workspaces/projects | はい | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| accounts | はい | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| workspaces | はい | 
| workspaces/computes | いいえ  | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| Identities | いいえ  | 
| userAssignedIdentities | はい | 

## <a name="microsoftmanagement"></a>Microsoft.Management
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| getEntities | いいえ  | 
| managementGroups | いいえ  | 
| resources | いいえ  | 
| startTenantBackfill | いいえ  | 
| tenantBackfillStatus | いいえ  | 

## <a name="microsoftmaps"></a>Microsoft.Maps
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| accounts | はい | 
| accounts/eventGridFilters | いいえ  | 

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| offers | いいえ  | 
| offerTypes | いいえ  | 
| offerTypes/publishers | いいえ  | 
| offerTypes/publishers/offers | いいえ  | 
| offerTypes/publishers/offers/plans | いいえ  | 
| offerTypes/publishers/offers/plans/agreements | いいえ  | 
| offerTypes/publishers/offers/plans/configs | いいえ  | 
| offerTypes/publishers/offers/plans/configs/importImage | いいえ  | 
| privategalleryitems | いいえ  | 
| products | いいえ  | 

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| classicDevServices | はい | 
| updateCommunicationPreference | いいえ  | 

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| agreements | いいえ  | 
| offertypes | いいえ  | 

## <a name="microsoftmedia"></a>Microsoft.Media
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| mediaservices | はい | 
| mediaservices/accountFilters | いいえ  | 
| mediaservices/assets | いいえ  | 
| mediaservices/assets/assetFilters | いいえ  | 
| mediaservices/contentKeyPolicies | いいえ  | 
| mediaservices/eventGridFilters | いいえ  | 
| mediaservices/liveEventOperations | いいえ  | 
| mediaservices/liveEvents | はい | 
| mediaservices/liveEvents/liveOutputs | いいえ  | 
| mediaservices/liveOutputOperations | いいえ  | 
| mediaservices/streamingEndpointOperations | いいえ  | 
| mediaservices/streamingEndpoints | はい | 
| mediaservices/streamingLocators | いいえ  | 
| mediaservices/streamingPolicies | いいえ  | 
| mediaservices/transforms | いいえ  | 
| mediaservices/transforms/jobs | いいえ  | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| projects | はい | 

## <a name="microsoftnetwork"></a>Microsoft.Network
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| applicationGateways | はい | 
| applicationSecurityGroups | はい | 
| azureFirewallFqdnTags | いいえ  | 
| azureFirewalls | はい | 
| bgpServiceCommunities | いいえ  | 
| connections | はい | 
| ddosCustomPolicies | はい | 
| ddosProtectionPlans | はい | 
| dnsOperationStatuses | いいえ  | 
| dnszones | はい | 
| dnszones/A | いいえ  | 
| dnszones/AAAA | いいえ  | 
| dnszones/all | いいえ  | 
| dnszones/CAA | いいえ  | 
| dnszones/CNAME | いいえ  | 
| dnszones/MX | いいえ  | 
| dnszones/NS | いいえ  | 
| dnszones/PTR | いいえ  | 
| dnszones/recordsets | いいえ  | 
| dnszones/SOA | いいえ  | 
| dnszones/SRV | いいえ  | 
| dnszones/TXT | いいえ  | 
| expressRouteCircuits | はい | 
| expressRouteServiceProviders | いいえ  | 
| frontdoors | はい | 
| frontdoorWebApplicationFirewallPolicies | はい | 
| getDnsResourceReference | いいえ  | 
| interfaceEndpoints | はい | 
| internalNotify | いいえ  | 
| loadBalancers | はい | 
| localNetworkGateways | はい | 
| natGateways | はい | 
| networkIntentPolicies | はい | 
| networkInterfaces | はい | 
| networkProfiles | はい | 
| networkSecurityGroups | はい | 
| networkWatchers | はい | 
| networkWatchers/connectionMonitors | はい | 
| networkWatchers/lenses | はい | 
| networkWatchers/pingMeshes | はい | 
| privateLinkServices | はい | 
| publicIPAddresses | はい | 
| publicIPPrefixes | はい | 
| routeFilters | はい | 
| routeTables | はい | 
| serviceEndpointPolicies | はい | 
| trafficManagerGeographicHierarchies | いいえ  | 
| trafficmanagerprofiles | はい | 
| trafficmanagerprofiles/heatMaps | いいえ  | 
| virtualHubs | はい | 
| virtualNetworkGateways | はい | 
| virtualNetworks | はい | 
| virtualNetworkTaps | はい | 
| virtualWans | はい | 
| vpnGateways | はい | 
| vpnSites | はい | 
| webApplicationFirewallPolicies | はい | 

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| namespaces | はい | 
| namespaces/notificationHubs | はい | 

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| devices | いいえ  | 
| linkTargets | いいえ  | 
| storageInsightConfigs | いいえ  | 
| workspaces | はい | 
| workspaces/dataSources | いいえ  | 
| workspaces/linkedServices | いいえ  | 
| workspaces/query | いいえ  | 

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| managementassociations | いいえ  | 
| managementconfigurations | はい | 
| solutions | はい | 
| views | はい | 

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| policyEvents | いいえ  | 
| policyStates | いいえ  | 
| policyTrackedResources | いいえ  | 
| remediations | いいえ  | 

## <a name="microsoftportal"></a>Microsoft.Portal
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| consoles | いいえ  | 
| dashboards | はい | 
| userSettings | いいえ  | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| workspaceCollections | はい | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| capacities | はい | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| accounts | はい | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| backupProtectedItems | いいえ  | 
| vaults | はい | 

## <a name="microsoftrelay"></a>Microsoft.Relay
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| namespaces | はい | 
| namespaces/authorizationrules | いいえ  | 
| namespaces/hybridconnections | いいえ  | 
| namespaces/hybridconnections/authorizationrules | いいえ  | 
| namespaces/wcfrelays | いいえ  | 
| namespaces/wcfrelays/authorizationrules | いいえ  | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| resources | いいえ  | 
| subscriptionsStatus | いいえ  | 

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| availabilityStatuses | いいえ  | 
| childAvailabilityStatuses | いいえ  | 
| childResources | いいえ  | 
| events | いいえ  | 
| impactedResources | いいえ  | 
| notifications | いいえ  | 

## <a name="microsoftresources"></a>Microsoft.Resources
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| deployments | いいえ  | 
| deployments/operations | いいえ  | 
| links | いいえ  | 
| notifyResourceJobs | いいえ  | 
| providers | いいえ  | 
| resourceGroups | いいえ  | 
| resources | いいえ  | 
| subscriptions | いいえ  | 
| subscriptions/providers | いいえ  | 
| subscriptions/resourceGroups | いいえ  | 
| subscriptions/resourcegroups/resources | いいえ  | 
| subscriptions/resources | いいえ  | 
| subscriptions/tagnames | いいえ  | 
| subscriptions/tagNames/tagValues | いいえ  | 
| tenants | いいえ  | 

## <a name="microsoftsaas"></a>Microsoft.SaaS
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| applications | はい | 
| saasresources | いいえ  | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| flows | はい | 
| jobcollections | はい | 

## <a name="microsoftsearch"></a>Microsoft.Search
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| resourceHealthMetadata | いいえ  | 
| searchServices | はい | 

## <a name="microsoftsecurity"></a>Microsoft.Security
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| advancedThreatProtectionSettings | いいえ  | 
| alerts | いいえ  | 
| allowedConnections | いいえ  | 
| appliances | いいえ  | 
| applicationWhitelistings | いいえ  | 
| AutoProvisioningSettings | いいえ  | 
| Compliances | いいえ  | 
| dataCollectionAgents | いいえ  | 
| discoveredSecuritySolutions | いいえ  | 
| externalSecuritySolutions | いいえ  | 
| InformationProtectionPolicies | いいえ  | 
| jitNetworkAccessPolicies | いいえ  | 
| monitoring | いいえ  | 
| monitoring/antimalware | いいえ  | 
| monitoring/baseline | いいえ  | 
| monitoring/patch | いいえ  | 
| policies | いいえ  | 
| pricings | いいえ  | 
| securityContacts | いいえ  | 
| securitySolutions | いいえ  | 
| securitySolutionsReferenceData | いいえ  | 
| securityStatus | いいえ  | 
| securityStatus/endpoints | いいえ  | 
| securityStatus/subnets | いいえ  | 
| securityStatus/virtualMachines | いいえ  | 
| securityStatuses | いいえ  | 
| securityStatusesSummaries | いいえ  | 
| settings | いいえ  | 
| tasks | いいえ  | 
| topologies | いいえ  | 
| workspaceSettings | いいえ  | 

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| diagnosticSettings | いいえ  | 
| diagnosticSettingsCategories | いいえ  | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| namespaces | はい | 
| namespaces/authorizationrules | いいえ  | 
| namespaces/disasterrecoveryconfigs | いいえ  | 
| namespaces/eventgridfilters | いいえ  | 
| namespaces/queues | いいえ  | 
| namespaces/queues/authorizationrules | いいえ  | 
| namespaces/topics | いいえ  | 
| namespaces/topics/authorizationrules | いいえ  | 
| namespaces/topics/subscriptions | いいえ  | 
| namespaces/topics/subscriptions/rules | いいえ  | 
| premiumMessagingRegions | いいえ  | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| clusters | はい | 
| clusters/applications | いいえ  | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| applications | はい | 
| gateways | はい | 
| networks | はい | 
| secrets | はい | 
| volumes | はい | 

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| SignalR | はい | 

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| applianceDefinitions | はい | 
| appliances | はい | 
| applicationDefinitions | はい | 
| applications | はい | 
| jitRequests | はい | 

## <a name="microsoftsql"></a>Microsoft.SQL
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| managedInstances | はい |
| managedInstances/databases | はい (下記の「注」を参照) |
| managedInstances/databases/backupShortTermRetentionPolicies | いいえ  |
| managedInstances/databases/schemas/tables/columns/sensitivityLabels | いいえ  |
| managedInstances/databases/vulnerabilityAssessments | いいえ  |
| managedInstances/databases/vulnerabilityAssessments/rules/baselines | いいえ  |
| managedInstances/encryptionProtector | いいえ  |
| managedInstances/keys | いいえ  |
| managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | いいえ  |
| managedInstances/vulnerabilityAssessments | いいえ  |
| servers | はい | 
| servers/administrators | いいえ  | 
| servers/communicationLinks | いいえ  | 
| servers/databases | はい (下記の「注」を参照) | 
| servers/encryptionProtector | いいえ  | 
| servers/firewallRules | いいえ  | 
| servers/keys | いいえ  | 
| servers/restorableDroppedDatabases | いいえ  | 
| servers/serviceobjectives | いいえ  | 
| servers/tdeCertificates | いいえ  | 

> [!NOTE]
> マスター データベースではタグはサポートされませんが、Azure SQL Data Warehouse データベースを含むその他のデータベースではタグがサポートされます。 Azure SQL Data Warehouse データベースは (一時停止ではなく) アクティブ状態である必要があります。


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| SqlVirtualMachineGroups | はい | 
| SqlVirtualMachineGroups/AvailabilityGroupListeners | いいえ  | 
| SqlVirtualMachines | はい | 

## <a name="microsoftstorage"></a>Microsoft.Storage
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| storageAccounts | はい | 
| storageAccounts/blobServices | いいえ  | 
| storageAccounts/fileServices | いいえ  | 
| storageAccounts/queueServices | いいえ  | 
| storageAccounts/services | いいえ  | 
| storageAccounts/tableServices | いいえ  | 
| usages | いいえ  | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| storageSyncServices | はい | 
| storageSyncServices/registeredServers | いいえ  | 
| storageSyncServices/syncGroups | いいえ  | 
| storageSyncServices/syncGroups/cloudEndpoints | いいえ  | 
| storageSyncServices/syncGroups/serverEndpoints | いいえ  | 
| storageSyncServices/workflows | いいえ  | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| managers | はい | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| streamingjobs | はい (下記の「注」を参照) | 
| streamingjobs/diagnosticSettings | いいえ  | 

> [!NOTE]
> streamingjobs が実行されている場合、タグを追加することはできません。 タグを追加するにはリソースを停止します。

## <a name="microsoftsubscription"></a>Microsoft.Subscription
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| CreateSubscription | いいえ  | 
| SubscriptionDefinitions | いいえ  | 
| SubscriptionOperations | いいえ  | 

## <a name="microsoftsupport"></a>microsoft.support
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| supporttickets | いいえ  | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| providerRegistrations | はい | 
| resources | はい | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| environments | はい | 
| environments/accessPolicies | いいえ  | 
| environments/eventsource | はい | 
| environments/referenceDataSets | はい | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| account | はい | 
| account/extension | はい | 
| account/project | はい | 

## <a name="microsoftweb"></a>Microsoft.Web
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| apiManagementAccounts | いいえ  | 
| apiManagementAccounts/apiAcls | いいえ  | 
| apiManagementAccounts/apis | いいえ  | 
| apiManagementAccounts/apis/apiAcls | いいえ  | 
| apiManagementAccounts/apis/connectionAcls | いいえ  | 
| apiManagementAccounts/apis/connections | いいえ  | 
| apiManagementAccounts/apis/connections/connectionAcls | いいえ  | 
| apiManagementAccounts/apis/localizedDefinitions | いいえ  | 
| apiManagementAccounts/connectionAcls | いいえ  | 
| apiManagementAccounts/connections | いいえ  | 
| billingMeters | いいえ  | 
| certificates | はい | 
| connectionGateways | はい | 
| connections | はい | 
| customApis | はい | 
| deletedSites | いいえ  | 
| functions | いいえ  | 
| hostingEnvironments | はい | 
| hostingEnvironments/multiRolePools | いいえ  | 
| hostingEnvironments/multiRolePools/instances | いいえ  | 
| hostingEnvironments/workerPools | いいえ  | 
| hostingEnvironments/workerPools/instances | いいえ  | 
| publishingUsers | いいえ  | 
| recommendations | いいえ  | 
| resourceHealthMetadata | いいえ  | 
| runtimes | いいえ  | 
| serverFarms | はい | 
| serverFarms/workers | いいえ  | 
| sites | はい | 
| sites/domainOwnershipIdentifiers | いいえ  | 
| sites/hostNameBindings | いいえ  | 
| sites/instances | いいえ  | 
| sites/instances/extensions | いいえ  | 
| sites/premieraddons | はい | 
| sites/recommendations | いいえ  | 
| sites/resourceHealthMetadata | いいえ  | 
| sites/slots | はい | 
| sites/slots/hostNameBindings | いいえ  | 
| sites/slots/instances | いいえ  | 
| sites/slots/instances/extensions | いいえ  | 
| sourceControls | いいえ  | 
| validate | いいえ  | 
| verifyHostingEnvironmentVnet | いいえ  | 

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| diagnosticSettings | いいえ  | 
| diagnosticSettingsCategories | いいえ  | 

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| DeviceServices | はい | 

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor
| リソースの種類 | タグのサポート |
| ------------- | ----------- |
| components | いいえ  | 
| componentsSummary | いいえ  | 
| monitorInstances | いいえ  | 
| monitorInstancesSummary | いいえ  | 
| monitors | いいえ  | 
| notificationSettings | いいえ  | 

## <a name="next-steps"></a>次の手順
リソースにタグを適用する方法については、「[タグを使用した Azure リソースの整理](resource-group-using-tags.md)」をご覧ください。
