---
title: リソースでの Azure Resource Manager タグのサポート
description: タグをサポートしている Azure リソースの種類を示します。 すべての Azure サービスの詳細を提供します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 06/07/2019
ms.author: tomfitz
ms.openlocfilehash: 72bb11cd064c90c2bbe1e9e6452dcbf07fe37817
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304862"
---
# <a name="tag-support-for-azure-resources"></a>Azure リソースでのタグのサポート
この記事では、リソースの種類が[タグ](resource-group-using-tags.md)をサポートしているかどうかについて説明します。 「**タグのサポート**」というラベルが付けられた列は、リソースの種類にタグのプロパティがあるかどうかを示します。 「**コスト レポートのタグ**」というラベルが付けられた列は、リソースの種類がタグをコスト レポートに渡すかどうかを示します。

コンマ区切りの値のファイルと同じデータを取得するには、[tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv) をダウンロードします。

## <a name="microsoftaad"></a>Microsoft.AAD
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| DomainServices | はい | はい |
| DomainServices/oucontainer | いいえ | いいえ |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| diagnosticSettings | いいえ |  いいえ |
| diagnosticSettingsCategories | いいえ |  いいえ |

## <a name="microsoftaddons"></a>Microsoft.Addons
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| supportProviders | いいえ |  いいえ |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| aadsupportcases | いいえ |  いいえ |
| addsservices | いいえ |  いいえ |
| agents | いいえ |  いいえ |
| anonymousapiusers | いいえ |  いいえ |
| configuration | いいえ |  いいえ |
| logs | いいえ |  いいえ |
| reports | いいえ |  いいえ |
| services | いいえ |  いいえ |

## <a name="microsoftadvisor"></a>Microsoft.Advisor
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| configuration | いいえ |  いいえ |
| generateRecommendations | いいえ |  いいえ |
| recommendations | いいえ |  いいえ |
| suppressions | いいえ |  いいえ |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| actionRules | いいえ |  いいえ |
| alerts | いいえ |  いいえ |
| alertsList | いいえ |  いいえ |
| alertsSummary | いいえ |  いいえ |
| alertsSummaryList | いいえ |  いいえ |
| smartDetectorAlertRules | いいえ |  いいえ |
| smartDetectorRuntimeEnvironments | いいえ |  いいえ |
| smartGroups | いいえ |  いいえ |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| servers | はい | はい |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| reportFeedback | いいえ |  いいえ |
| service | はい | はい |
| validateServiceName | いいえ |  いいえ |

## <a name="microsoftattestation"></a>Microsoft.Attestation
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| attestationProviders | いいえ |  いいえ |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| classicAdministrators | いいえ |  いいえ |
| denyAssignments | いいえ |  いいえ |
| elevateAccess | いいえ |  いいえ |
| locks | いいえ |  いいえ |
| permissions | いいえ |  いいえ |
| policyAssignments | いいえ |  いいえ |
| policyDefinitions | いいえ |  いいえ |
| policySetDefinitions | いいえ |  いいえ |
| providerOperations | いいえ |  いいえ |
| roleAssignments | いいえ |  いいえ |
| roleDefinitions | いいえ |  いいえ |

## <a name="microsoftautomation"></a>Microsoft.Automation
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| automationAccounts | はい | はい |
| automationAccounts/configurations | はい | はい |
| automationAccounts/jobs | いいえ |  いいえ |
| automationAccounts/runbooks | はい | はい |
| automationAccounts/softwareUpdateConfigurations | いいえ | いいえ |
| automationAccounts/webhooks | いいえ |  いいえ |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| 環境 | いいえ |  いいえ |
| environments/accounts | いいえ |  いいえ |
| environments/accounts/namespaces | いいえ |  いいえ |
| environments/accounts/namespaces/configurations | いいえ |  いいえ |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| b2cDirectories | はい | いいえ |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| registrations | はい | はい |
| registrations/customerSubscriptions | いいえ |  いいえ |
| registrations/products | いいえ |  いいえ |

## <a name="microsoftbatch"></a>Microsoft.Batch
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| batchAccounts | はい | はい |

## <a name="microsoftbilling"></a>Microsoft.Billing
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| billingAccounts | いいえ |  いいえ |
| billingAccounts/billingProfiles | いいえ |  いいえ |
| billingAccounts/billingProfiles/billingSubscriptions | いいえ |  いいえ |
| billingAccounts/billingProfiles/invoices | いいえ |  いいえ |
| billingAccounts/billingProfiles/invoices/pricesheet | いいえ |  いいえ |
| billingAccounts/billingProfiles/operationStatus | いいえ |  いいえ |
| billingAccounts/billingProfiles/paymentMethods | いいえ |  いいえ |
| billingAccounts/billingProfiles/policies | いいえ |  いいえ |
| billingAccounts/billingProfiles/pricesheet | いいえ |  いいえ |
| billingAccounts/billingProfiles/products | いいえ |  いいえ |
| billingAccounts/billingProfiles/transactions | いいえ |  いいえ |
| billingAccounts/billingSubscriptions | いいえ |  いいえ |
| billingAccounts/departments | いいえ |  いいえ |
| billingAccounts/eligibleOffers | いいえ |  いいえ |
| billingAccounts/enrollmentAccounts | いいえ |  いいえ |
| billingAccounts/invoices | いいえ |  いいえ |
| billingAccounts/invoiceSections | いいえ |  いいえ |
| billingAccounts/invoiceSections/billingSubscriptions | いいえ |  いいえ |
| billingAccounts/invoiceSections/billingSubscriptions/transfer | いいえ |  いいえ |
| billingAccounts/invoiceSections/importRequests | いいえ |  いいえ |
| billingAccounts/invoiceSections/initiateImportRequest | いいえ |  いいえ |
| billingAccounts/invoiceSections/initiateTransfer | いいえ |  いいえ |
| billingAccounts/invoiceSections/operationStatus | いいえ |  いいえ |
| billingAccounts/invoiceSections/products | いいえ |  いいえ |
| billingAccounts/invoiceSections/transfers | いいえ |  いいえ |
| billingAccounts/products | いいえ |  いいえ |
| billingAccounts/projects | いいえ |  いいえ |
| billingAccounts/projects/billingSubscriptions | いいえ |  いいえ |
| billingAccounts/projects/importRequests | いいえ |  いいえ |
| billingAccounts/projects/initiateImportRequest | いいえ |  いいえ |
| billingAccounts/projects/operationStatus | いいえ |  いいえ |
| billingAccounts/projects/products | いいえ |  いいえ |
| billingAccounts/transactions | いいえ |  いいえ |
| billingPeriods | いいえ |  いいえ |
| BillingPermissions | いいえ |  いいえ |
| billingProperty | いいえ |  いいえ |
| BillingRoleAssignments | いいえ |  いいえ |
| BillingRoleDefinitions | いいえ |  いいえ |
| CreateBillingRoleAssignment | いいえ |  いいえ |
| departments | いいえ |  いいえ |
| enrollmentAccounts | いいえ |  いいえ |
| importRequests | いいえ |  いいえ |
| importRequests/acceptImportRequest | いいえ |  いいえ |
| importRequests/declineImportRequest | いいえ |  いいえ |
| invoices | いいえ |  いいえ |
| transfers | いいえ |  いいえ |
| transfers/acceptTransfer | いいえ |  いいえ |
| transfers/declineTransfer | いいえ |  いいえ |
| transfers/operationStatus | いいえ |  いいえ |
| usagePlans | いいえ |  いいえ |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| mapApis | はい | はい |
| updateCommunicationPreference | いいえ |  いいえ |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| BizTalk | はい | はい |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| blueprintAssignments | いいえ |  いいえ |
| blueprintAssignments/assignmentOperations | いいえ |  いいえ |
| blueprintAssignments/operations | いいえ |  いいえ |
| blueprints | いいえ |  いいえ |
| blueprints/artifacts | いいえ |  いいえ |
| blueprints/versions | いいえ |  いいえ |
| blueprints/versions/artifacts | いいえ |  いいえ |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| botServices | はい | はい |
| botServices/channels | いいえ |  いいえ |
| botServices/connections | いいえ |  いいえ |

## <a name="microsoftcache"></a>Microsoft.Cache
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| Redis | はい | はい |
| RedisConfigDefinition | いいえ |  いいえ |

## <a name="microsoftcapacity"></a>Microsoft.Capacity
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| appliedReservations | いいえ |  いいえ |
| calculatePrice | いいえ |  いいえ |
| catalogs | いいえ |  いいえ |
| commercialReservationOrders | いいえ |  いいえ |
| reservationOrders | いいえ |  いいえ |
| reservationOrders/calculateRefund | いいえ |  いいえ |
| reservationOrders/merge | いいえ |  いいえ |
| reservationOrders/reservations | いいえ |  いいえ |
| reservationOrders/reservations/revisions | いいえ |  いいえ |
| reservationOrders/return | いいえ |  いいえ |
| reservationOrders/split | いいえ |  いいえ |
| reservationOrders/swap | いいえ |  いいえ |
| reservations | いいえ |  いいえ |
| resources | いいえ |  いいえ |
| validateReservationOrder | いいえ |  いいえ |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| edgenodes | いいえ |  いいえ |
| profiles | はい | はい |
| profiles/endpoints | はい | はい |
| profiles/endpoints/customdomains | いいえ |  いいえ |
| profiles/endpoints/origins | いいえ |  いいえ |
| validateProbe | いいえ |  いいえ |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| certificateOrders | はい | はい |
| certificateOrders/certificates | いいえ |  いいえ |
| validateCertificateRegistrationInformation | いいえ |  いいえ |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| capabilities | いいえ |  いいえ |
| domainNames | いいえ |  いいえ |
| domainNames/capabilities | いいえ |  いいえ |
| domainNames/internalLoadBalancers | いいえ |  いいえ |
| domainNames/serviceCertificates | いいえ |  いいえ |
| domainNames/slots | いいえ |  いいえ |
| domainNames/slots/roles | いいえ |  いいえ |
| moveSubscriptionResources | いいえ |  いいえ |
| operatingSystemFamilies | いいえ |  いいえ |
| operatingSystems | いいえ |  いいえ |
| quotas | いいえ |  いいえ |
| resourceTypes | いいえ |  いいえ |
| validateSubscriptionMoveAvailability | いいえ |  いいえ |
| virtualMachines | いいえ |  いいえ |
| virtualMachines/diagnosticSettings | いいえ |  いいえ |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| classicInfrastructureResources | いいえ |  いいえ |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| capabilities | いいえ |  いいえ |
| expressRouteCrossConnections | いいえ |  いいえ |
| expressRouteCrossConnections/peerings | いいえ |  いいえ |
| gatewaySupportedDevices | いいえ |  いいえ |
| networkSecurityGroups | いいえ |  いいえ |
| quotas | いいえ |  いいえ |
| reservedIps | いいえ |  いいえ |
| virtualNetworks | いいえ |  いいえ |
| virtualNetworks/remoteVirtualNetworkPeeringProxies | いいえ |  いいえ |
| virtualNetworks/virtualNetworkPeerings | いいえ |  いいえ |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| capabilities | いいえ |  いいえ |
| disks | いいえ |  いいえ |
| images | いいえ |  いいえ |
| osImages | いいえ |  いいえ |
| osPlatformImages | いいえ |  いいえ |
| publicImages | いいえ |  いいえ |
| quotas | いいえ |  いいえ |
| storageAccounts | いいえ |  いいえ |
| storageAccounts/services | いいえ |  いいえ |
| storageAccounts/services/diagnosticSettings | いいえ |  いいえ |
| storageAccounts/vmImages | いいえ |  いいえ |
| vmImages | いいえ |  いいえ |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| accounts | はい | はい |

## <a name="microsoftcommerce"></a>Microsoft.Commerce
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| RateCard | いいえ |  いいえ |
| UsageAggregates | いいえ |  いいえ |

## <a name="microsoftcompute"></a>Microsoft.Compute
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| availabilitySets | はい | はい |
| disks | はい | はい |
| images | はい | はい |
| restorePointCollections | はい | はい |
| restorePointCollections/restorePoints | いいえ |  いいえ |
| sharedVMImages | はい | はい |
| sharedVMImages/versions | はい | はい |
| snapshots | はい | はい |
| virtualMachines | はい | はい |
| virtualMachines/diagnosticSettings | いいえ |  いいえ |
| virtualMachines/extensions | はい | はい |
| virtualMachineScaleSets | はい | はい |
| virtualMachineScaleSets/extensions | いいえ |  いいえ |
| virtualMachineScaleSets/networkInterfaces | いいえ |  いいえ |
| virtualMachineScaleSets/publicIPAddresses | いいえ |  いいえ |
| virtualMachineScaleSets/virtualMachines | いいえ |  いいえ |
| virtualMachineScaleSets/virtualMachines/networkInterfaces | いいえ |  いいえ |

## <a name="microsoftconsumption"></a>Microsoft.Consumption
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| AggregatedCost | いいえ |  いいえ |
| Balances | いいえ |  いいえ |
| Budgets | いいえ |  いいえ |
| Charges | いいえ |  いいえ |
| CostTags | いいえ |  いいえ |
| credits | いいえ |  いいえ |
| events | いいえ |  いいえ |
| Forecasts | いいえ |  いいえ |
| lots | いいえ |  いいえ |
| Marketplaces | いいえ |  いいえ |
| Pricesheets | いいえ |  いいえ |
| products | いいえ |  いいえ |
| ReservationDetails | いいえ |  いいえ |
| ReservationRecommendations | いいえ |  いいえ |
| ReservationSummaries | いいえ |  いいえ |
| ReservationTransactions | いいえ |  いいえ |
| Tags | いいえ |  いいえ |
| Terms | いいえ |  いいえ |
| UsageDetails | いいえ |  いいえ |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| containerGroups | はい | はい |
| serviceAssociationLinks | いいえ |  いいえ |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| registries | はい | はい |
| registries/builds | いいえ |  いいえ |
| registries/builds/cancel | いいえ |  いいえ |
| registries/builds/getLogLink | いいえ |  いいえ |
| registries/buildTasks | はい | はい |
| registries/buildTasks/steps | いいえ |  いいえ |
| registries/eventGridFilters | いいえ |  いいえ |
| registries/getBuildSourceUploadUrl | いいえ |  いいえ |
| registries/GetCredentials | いいえ |  いいえ |
| registries/importImage | いいえ |  いいえ |
| registries/queueBuild | いいえ |  いいえ |
| registries/regenerateCredential | いいえ |  いいえ |
| registries/regenerateCredentials | いいえ |  いいえ |
| registries/replications | はい | はい |
| registries/runs | いいえ |  いいえ |
| registries/runs/cancel | いいえ |  いいえ |
| registries/scheduleRun | いいえ |  いいえ |
| registries/tasks | はい | はい |
| registries/updatePolicies | いいえ |  いいえ |
| registries/webhooks | はい | はい |
| registries/webhooks/getCallbackConfig | いいえ |  いいえ |
| registries/webhooks/ping | いいえ |  いいえ |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| containerServices | はい | はい |
| managedClusters | はい | はい |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| applications | はい | はい |
| updateCommunicationPreference | いいえ |  いいえ |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| accounts | はい | はい |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| アラート | いいえ |  いいえ |
| BillingAccounts | いいえ |  いいえ |
| Connectors | はい | はい |
| Departments | いいえ |  いいえ |
| Dimensions | いいえ |  いいえ |
| EnrollmentAccounts | いいえ |  いいえ |
| Query | いいえ |  いいえ |
| registrations | いいえ |  いいえ |
| Reportconfigs | いいえ |  いいえ |
| Reports | いいえ |  いいえ |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| hubs | はい | はい |
| hubs/authorizationPolicies | いいえ |  いいえ |
| hubs/connectors | いいえ |  いいえ |
| hubs/connectors/mappings | いいえ |  いいえ |
| hubs/interactions | いいえ |  いいえ |
| hubs/kpi | いいえ |  いいえ |
| hubs/links | いいえ |  いいえ |
| hubs/profiles | いいえ |  いいえ |
| hubs/roleAssignments | いいえ |  いいえ |
| hubs/roles | いいえ |  いいえ |
| hubs/suggestTypeSchema | いいえ |  いいえ |
| hubs/views | いいえ |  いいえ |
| hubs/widgetTypes | いいえ |  いいえ |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| jobs | はい | はい |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| DataBoxEdgeDevices | はい | はい |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| workspaces | はい | いいえ |
| workspaces/virtualNetworkPeerings | いいえ |  いいえ |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| catalogs | はい | はい |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| connectionManagers | はい | はい |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| dataFactories | はい | いいえ |
| dataFactories/diagnosticSettings | いいえ |  いいえ |
| dataFactorySchema | いいえ |  いいえ |
| factories | はい | いいえ |
| factories/integrationRuntimes | いいえ |  いいえ |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| accounts | はい | はい |
| accounts/dataLakeStoreAccounts | いいえ |  いいえ |
| accounts/storageAccounts | いいえ |  いいえ |
| accounts/storageAccounts/containers | いいえ |  いいえ |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| accounts | はい | はい |
| accounts/eventGridFilters | いいえ |  いいえ |
| accounts/firewallRules | いいえ |  いいえ |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| services | はい | はい |
| services/projects | はい | はい |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| servers | はい | はい |
| servers/recoverableServers | いいえ |  いいえ |
| servers/virtualNetworkRules | いいえ |  いいえ |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| servers | はい | はい |
| servers/recoverableServers | いいえ |  いいえ |
| servers/virtualNetworkRules | いいえ |  いいえ |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| servers | はい | はい |
| servers/advisors | いいえ |  いいえ |
| servers/queryTexts | いいえ |  いいえ |
| servers/recoverableServers | いいえ |  いいえ |
| servers/topQueryStatistics | いいえ |  いいえ |
| servers/virtualNetworkRules | いいえ |  いいえ |
| servers/waitStatistics | いいえ |  いいえ |

## <a name="microsoftdevices"></a>Microsoft.Devices
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| IotHubs | はい | はい |
| IotHubs/eventGridFilters | いいえ |  いいえ |
| ProvisioningServices | はい | はい |
| usages | いいえ |  いいえ |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| controllers | はい | はい |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| labs | はい | はい |
| labs/serviceRunners | はい | はい |
| labs/virtualMachines | はい | はい |
| schedules | はい | はい |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| databaseAccountNames | いいえ |  いいえ |
| databaseAccounts | はい | はい |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| domains | はい | はい |
| domains/domainOwnershipIdentifiers | いいえ |  いいえ |
| generateSsoRequest | いいえ |  いいえ |
| topLevelDomains | いいえ |  いいえ |
| validateDomainRegistrationInformation | いいえ |  いいえ |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| lcsprojects | いいえ |  いいえ |
| lcsprojects/clouddeployments | いいえ |  いいえ |
| lcsprojects/connectors | いいえ |  いいえ |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| domains | はい | いいえ |
| domains/topics | いいえ |  いいえ |
| eventSubscriptions | いいえ |  いいえ |
| extensionTopics | いいえ |  いいえ |
| topics | はい | いいえ |
| topicTypes | いいえ |  いいえ |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| clusters | はい | いいえ |
| namespaces | はい | いいえ |
| namespaces/authorizationrules | いいえ |  いいえ |
| namespaces/disasterrecoveryconfigs | いいえ |  いいえ |
| namespaces/eventhubs | いいえ |  いいえ |
| namespaces/eventhubs/authorizationrules | いいえ |  いいえ |
| namespaces/eventhubs/consumergroups | いいえ |  いいえ |

## <a name="microsoftfeatures"></a>Microsoft.Features
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| features | いいえ |  いいえ |
| providers | いいえ |  いいえ |

## <a name="microsoftgallery"></a>Microsoft.Gallery
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| enroll | いいえ |  いいえ |
| galleryitems | いいえ |  いいえ |
| generateartifactaccessuri | いいえ |  いいえ |
| myareas | いいえ |  いいえ |
| myareas/areas | いいえ |  いいえ |
| myareas/areas/areas | いいえ |  いいえ |
| myareas/areas/areas/galleryitems | いいえ |  いいえ |
| myareas/areas/galleryitems | いいえ |  いいえ |
| myareas/galleryitems | いいえ |  いいえ |
| registrations | いいえ |  いいえ |
| resources | いいえ |  いいえ |
| retrieveresourcesbyid | いいえ |  いいえ |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| guestConfigurationAssignments | いいえ |  いいえ |
| software | いいえ |  いいえ |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| hanaInstances | はい |  はい |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| clusters | はい | はい |
| clusters/applications | いいえ |  いいえ |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| jobs | はい | はい |

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| labelGroups | いいえ |  いいえ |
| labelGroups/labels | いいえ |  いいえ |
| labelGroups/labels/conditions | いいえ |  いいえ |
| labelGroups/labels/subLabels | いいえ |  いいえ |
| labelGroups/labels/subLabels/conditions | いいえ |  いいえ |

## <a name="microsoftinsights"></a>microsoft.insights
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| actiongroups | はい | はい |
| activityLogAlerts | はい | はい |
| alertrules | はい | はい |
| automatedExportSettings | いいえ |  いいえ |
| autoscalesettings | はい | はい |
| baseline | いいえ |  いいえ |
| calculatebaseline | いいえ |  いいえ |
| components | はい | はい |
| components/events | いいえ |  いいえ |
| components/pricingPlans | いいえ |  いいえ |
| components/query | いいえ |  いいえ |
| diagnosticSettings | いいえ |  いいえ |
| diagnosticSettingsCategories | いいえ |  いいえ |
| eventCategories | いいえ |  いいえ |
| eventtypes | いいえ |  いいえ |
| extendedDiagnosticSettings | いいえ |  いいえ |
| logDefinitions | いいえ |  いいえ |
| logprofiles | いいえ |  いいえ |
| logs | いいえ |  いいえ |
| metricAlerts | はい | はい |
| migrateToNewPricingModel | いいえ |  いいえ |
| myWorkbooks | いいえ |  いいえ |
| Query | いいえ |  いいえ |
| rollbackToLegacyPricingModel | いいえ |  いいえ |
| scheduledqueryrules | はい | はい |
| vmInsightsOnboardingStatuses | いいえ |  いいえ |
| webtests | はい | はい |
| Workbooks | はい | はい |

## <a name="microsoftintune"></a>Microsoft.Intune
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| diagnosticSettings | いいえ |  いいえ |
| diagnosticSettingsCategories | いいえ |  いいえ |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| IoTApps | はい | はい |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| Graph | はい | はい |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| deletedVaults | いいえ |  いいえ |
| vaults | はい | はい |
| vaults/accessPolicies | いいえ |  いいえ |
| vaults/secrets | いいえ |  いいえ |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| clusters | はい | はい |
| clusters/databases | いいえ |  いいえ |
| clusters/databases/dataconnections | いいえ |  いいえ |
| clusters/databases/eventhubconnections | いいえ |  いいえ |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| labaccounts | はい | はい |
| users | いいえ |  いいえ |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| accounts | はい | はい |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| accounts | はい | はい |

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| logs | いいえ |  いいえ |

## <a name="microsoftlogic"></a>Microsoft.Logic
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| integrationAccounts | はい | はい |
| workflows | はい | はい |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| commitmentPlans | はい | はい |
| webServices | はい | はい |
| Workspaces | はい | はい |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| accounts | はい | はい |
| accounts/workspaces | はい | はい |
| accounts/workspaces/プロジェクト | はい | はい |
| teamAccounts | はい | はい |
| teamAccounts/workspaces | はい | はい |
| teamAccounts/workspaces/projects | はい | はい |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| accounts | はい | はい |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| workspaces | はい | はい |
| workspaces/computes | いいえ |  いいえ |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| Identities | いいえ |  いいえ |
| userAssignedIdentities | はい | 

## <a name="microsoftmanagement"></a>Microsoft.Management
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| getEntities | いいえ |  いいえ |
| managementGroups | いいえ |  いいえ |
| resources | いいえ |  いいえ |
| startTenantBackfill | いいえ |  いいえ |
| tenantBackfillStatus | いいえ |  いいえ |

## <a name="microsoftmaps"></a>Microsoft.Maps
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| accounts | はい | はい |
| accounts/eventGridFilters | いいえ |  いいえ |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| offers | いいえ |  いいえ |
| offerTypes | いいえ |  いいえ |
| offerTypes/publishers | いいえ |  いいえ |
| offerTypes/publishers/offers | いいえ |  いいえ |
| offerTypes/publishers/offers/plans | いいえ |  いいえ |
| offerTypes/publishers/offers/plans/agreements | いいえ |  いいえ |
| offerTypes/publishers/offers/plans/configs | いいえ |  いいえ |
| offerTypes/publishers/offers/plans/configs/importImage | いいえ |  いいえ |
| privategalleryitems | いいえ |  いいえ |
| products | いいえ |  いいえ |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| classicDevServices | はい | はい |
| updateCommunicationPreference | いいえ |  いいえ |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| agreements | いいえ |  いいえ |
| offertypes | いいえ |  いいえ |

## <a name="microsoftmedia"></a>Microsoft.Media
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| mediaservices | はい | はい |
| mediaservices/accountFilters | いいえ |  いいえ |
| mediaservices/assets | いいえ |  いいえ |
| mediaservices/assets/assetFilters | いいえ |  いいえ |
| mediaservices/contentKeyPolicies | いいえ |  いいえ |
| mediaservices/eventGridFilters | いいえ |  いいえ |
| mediaservices/liveEventOperations | いいえ |  いいえ |
| mediaservices/liveEvents | はい | はい |
| mediaservices/liveEvents/liveOutputs | いいえ |  いいえ |
| mediaservices/liveOutputOperations | いいえ |  いいえ |
| mediaservices/streamingEndpointOperations | いいえ |  いいえ |
| mediaservices/streamingEndpoints | はい | はい |
| mediaservices/streamingLocators | いいえ |  いいえ |
| mediaservices/streamingPolicies | いいえ |  いいえ |
| mediaservices/transforms | いいえ |  いいえ |
| mediaservices/transforms/jobs | いいえ |  いいえ |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| projects | はい | はい |

## <a name="microsoftnetwork"></a>Microsoft.Network
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| applicationGateways | はい | いいえ |
| applicationSecurityGroups | はい | はい |
| azureFirewallFqdnTags | いいえ |  いいえ |
| azureFirewalls | はい | いいえ |
| bgpServiceCommunities | いいえ |  いいえ |
| connections | はい | はい |
| ddosCustomPolicies | はい | はい |
| ddosProtectionPlans | はい | はい |
| dnsOperationStatuses | いいえ |  いいえ |
| dnszones | はい | はい |
| dnszones/A | いいえ |  いいえ |
| dnszones/AAAA | いいえ |  いいえ |
| dnszones/all | いいえ |  いいえ |
| dnszones/CAA | いいえ |  いいえ |
| dnszones/CNAME | いいえ |  いいえ |
| dnszones/MX | いいえ |  いいえ |
| dnszones/NS | いいえ |  いいえ |
| dnszones/PTR | いいえ |  いいえ |
| dnszones/recordsets | いいえ |  いいえ |
| dnszones/SOA | いいえ |  いいえ |
| dnszones/SRV | いいえ |  いいえ |
| dnszones/TXT | いいえ |  いいえ |
| expressRouteCircuits | はい  | いいえ |
| expressRouteServiceProviders | いいえ |  いいえ |
| frontdoors | はい。ただし、制限あり ([下記の注](#frontdoor)を参照) | はい |
| frontdoorWebApplicationFirewallPolicies | はい。ただし、制限あり ([下記の注](#frontdoor)を参照) | はい |
| getDnsResourceReference | いいえ |  いいえ |
| interfaceEndpoints | はい | はい |
| internalNotify | いいえ |  いいえ |
| loadBalancers | はい | いいえ |
| localNetworkGateways | はい | はい |
| natGateways | はい | はい |
| networkIntentPolicies | はい | はい |
| networkInterfaces | はい | はい |
| networkProfiles | はい | はい |
| networkSecurityGroups | はい | はい |
| networkWatchers | はい | いいえ |
| networkWatchers/connectionMonitors | はい | いいえ |
| networkWatchers/lenses | はい | いいえ |
| networkWatchers/pingMeshes | はい | いいえ |
| privateLinkServices | はい | はい |
| publicIPAddresses | はい | はい |
| publicIPPrefixes | はい | はい |
| routeFilters | はい | はい |
| routeTables | はい | はい |
| serviceEndpointPolicies | はい | はい |
| trafficManagerGeographicHierarchies | いいえ |  いいえ |
| trafficmanagerprofiles | はい | はい |
| trafficmanagerprofiles/heatMaps | いいえ |  いいえ |
| virtualHubs | はい | はい |
| virtualNetworkGateways | はい | はい |
| virtualNetworks | はい | はい |
| virtualNetworks/subnets | いいえ |  いいえ |
| virtualNetworkTaps | はい | はい |
| virtualWans | はい | はい |
| vpnGateways | はい | いいえ |
| vpnSites | はい | はい |
| webApplicationFirewallPolicies | はい | はい |

<a id="frontdoor" />

Azure Front Door Service の場合は、リソースの作成時にタグを適用できますが、タグの更新や追加は現在サポートされていません。

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| namespaces | はい | いいえ |
| namespaces/notificationHubs | はい | いいえ |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| devices | いいえ |  いいえ |
| linkTargets | いいえ |  いいえ |
| storageInsightConfigs | いいえ |  いいえ |
| workspaces | はい | はい |
| workspaces/dataSources | いいえ |  いいえ |
| workspaces/linkedServices | いいえ |  いいえ |
| workspaces/query | いいえ |  いいえ |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| managementassociations | いいえ |  いいえ |
| managementconfigurations | はい | はい |
| solutions | はい | はい |
| views | はい | はい |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| policyEvents | いいえ |  いいえ |
| policyStates | いいえ |  いいえ |
| policyTrackedResources | いいえ |  いいえ |
| remediations | いいえ |  いいえ |

## <a name="microsoftportal"></a>Microsoft.Portal
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| consoles | いいえ |  いいえ |
| dashboards | はい | はい |
| userSettings | いいえ |  いいえ |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| workspaceCollections | はい | はい |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| capacities | はい | はい |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| accounts | はい | はい |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| backupProtectedItems | いいえ |  いいえ |
| vaults | はい | はい |

## <a name="microsoftrelay"></a>Microsoft.Relay
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| namespaces | はい | はい |
| namespaces/authorizationrules | いいえ |  いいえ |
| namespaces/hybridconnections | いいえ |  いいえ |
| namespaces/hybridconnections/authorizationrules | いいえ |  いいえ |
| namespaces/wcfrelays | いいえ |  いいえ |
| namespaces/wcfrelays/authorizationrules | いいえ |  いいえ |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| resources | いいえ |  いいえ |
| subscriptionsStatus | いいえ |  いいえ |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| availabilityStatuses | いいえ |  いいえ |
| childAvailabilityStatuses | いいえ |  いいえ |
| childResources | いいえ |  いいえ |
| events | いいえ |  いいえ |
| impactedResources | いいえ |  いいえ |
| notifications | いいえ |  いいえ |

## <a name="microsoftresources"></a>Microsoft.Resources
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| deployments | いいえ |  いいえ |
| deployments/operations | いいえ |  いいえ |
| links | いいえ |  いいえ |
| notifyResourceJobs | いいえ |  いいえ |
| providers | いいえ |  いいえ |
| resourceGroups | いいえ |  いいえ |
| resources | いいえ |  いいえ |
| subscriptions | いいえ |  いいえ |
| subscriptions/providers | いいえ |  いいえ |
| subscriptions/resourceGroups | いいえ |  いいえ |
| subscriptions/resourcegroups/resources | いいえ |  いいえ |
| subscriptions/resources | いいえ |  いいえ |
| subscriptions/tagnames | いいえ |  いいえ |
| subscriptions/tagNames/tagValues | いいえ |  いいえ |
| tenants | いいえ |  いいえ |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| applications | はい | はい |
| saasresources | いいえ |  いいえ |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| flows | はい | はい |
| jobcollections | はい | はい |

## <a name="microsoftsearch"></a>Microsoft.Search
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| resourceHealthMetadata | いいえ |  いいえ |
| searchServices | はい | はい |

## <a name="microsoftsecurity"></a>Microsoft.Security
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| advancedThreatProtectionSettings | いいえ |  いいえ |
| alerts | いいえ |  いいえ |
| allowedConnections | いいえ |  いいえ |
| appliances | いいえ |  いいえ |
| applicationWhitelistings | いいえ |  いいえ |
| AutoProvisioningSettings | いいえ |  いいえ |
| Compliances | いいえ |  いいえ |
| dataCollectionAgents | いいえ |  いいえ |
| discoveredSecuritySolutions | いいえ |  いいえ |
| externalSecuritySolutions | いいえ |  いいえ |
| InformationProtectionPolicies | いいえ |  いいえ |
| jitNetworkAccessPolicies | いいえ |  いいえ |
| monitoring | いいえ |  いいえ |
| monitoring/antimalware | いいえ |  いいえ |
| monitoring/baseline | いいえ |  いいえ |
| monitoring/patch | いいえ |  いいえ |
| policies | いいえ |  いいえ |
| pricings | いいえ |  いいえ |
| securityContacts | いいえ |  いいえ |
| securitySolutions | いいえ |  いいえ |
| securitySolutionsReferenceData | いいえ |  いいえ |
| securityStatus | いいえ |  いいえ |
| securityStatus/endpoints | いいえ |  いいえ |
| securityStatus/subnets | いいえ |  いいえ |
| securityStatus/virtualMachines | いいえ |  いいえ |
| securityStatuses | いいえ |  いいえ |
| securityStatusesSummaries | いいえ |  いいえ |
| settings | いいえ |  いいえ |
| tasks | いいえ |  いいえ |
| topologies | いいえ |  いいえ |
| workspaceSettings | いいえ |  いいえ |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| diagnosticSettings | いいえ |  いいえ |
| diagnosticSettingsCategories | いいえ |  いいえ |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| namespaces | はい | いいえ |
| namespaces/authorizationrules | いいえ |  いいえ |
| namespaces/disasterrecoveryconfigs | いいえ |  いいえ |
| namespaces/eventgridfilters | いいえ |  いいえ |
| namespaces/queues | いいえ |  いいえ |
| namespaces/queues/authorizationrules | いいえ |  いいえ |
| namespaces/topics | いいえ |  いいえ |
| namespaces/topics/authorizationrules | いいえ |  いいえ |
| namespaces/topics/subscriptions | いいえ |  いいえ |
| namespaces/topics/subscriptions/rules | いいえ |  いいえ |
| premiumMessagingRegions | いいえ |  いいえ |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| clusters | はい | はい |
| clusters/applications | いいえ |  いいえ |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| applications | はい | はい |
| gateways | はい | はい |
| networks | はい | はい |
| secrets | はい | はい |
| volumes | はい | はい |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| SignalR | はい | はい |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| applianceDefinitions | はい | はい |
| appliances | はい | はい |
| applicationDefinitions | はい | はい |
| applications | はい | はい |
| jitRequests | はい | はい |

## <a name="microsoftsql"></a>Microsoft.SQL
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| managedInstances | はい | はい |
| managedInstances/databases | はい (下記の「注」を参照) | はい |
| managedInstances/databases/backupShortTermRetentionPolicies | いいえ | いいえ |
| managedInstances/databases/schemas/tables/columns/sensitivityLabels | いいえ | いいえ |
| managedInstances/databases/vulnerabilityAssessments | いいえ | いいえ |
| managedInstances/databases/vulnerabilityAssessments/rules/baselines | いいえ | いいえ |
| managedInstances/encryptionProtector | いいえ | いいえ |
| managedInstances/keys | いいえ | いいえ |
| managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | いいえ | いいえ |
| managedInstances/vulnerabilityAssessments | いいえ | いいえ |
| servers | はい | はい |
| servers/administrators | いいえ |  いいえ |
| servers/communicationLinks | いいえ |  いいえ |
| servers/databases | はい (下記の「注」を参照) | はい |
| servers/encryptionProtector | いいえ |  いいえ |
| servers/firewallRules | いいえ |  いいえ |
| servers/keys | いいえ |  いいえ |
| servers/restorableDroppedDatabases | いいえ |  いいえ |
| servers/serviceobjectives | いいえ |  いいえ |
| servers/tdeCertificates | いいえ |  いいえ |

> [!NOTE]
> マスター データベースではタグはサポートされませんが、Azure SQL Data Warehouse データベースを含むその他のデータベースではタグがサポートされます。 Azure SQL Data Warehouse データベースは (一時停止ではなく) アクティブ状態である必要があります。


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| SqlVirtualMachineGroups | はい | はい |
| SqlVirtualMachineGroups/AvailabilityGroupListeners | いいえ |  いいえ |
| SqlVirtualMachines | はい | はい |

## <a name="microsoftstorage"></a>Microsoft.Storage
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| storageAccounts | はい | はい |
| storageAccounts/blobServices | いいえ |  いいえ |
| storageAccounts/fileServices | いいえ |  いいえ |
| storageAccounts/queueServices | いいえ |  いいえ |
| storageAccounts/services | いいえ |  いいえ |
| storageAccounts/tableServices | いいえ |  いいえ |
| usages | いいえ |  いいえ |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| storageSyncServices | はい | はい |
| storageSyncServices/registeredServers | いいえ |  いいえ |
| storageSyncServices/syncGroups | いいえ |  いいえ |
| storageSyncServices/syncGroups/cloudEndpoints | いいえ |  いいえ |
| storageSyncServices/syncGroups/serverEndpoints | いいえ |  いいえ |
| storageSyncServices/workflows | いいえ |  いいえ |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| managers | はい | はい |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| streamingjobs | はい (下記の「注」を参照) | はい |
| streamingjobs/diagnosticSettings | いいえ |  いいえ |

> [!NOTE]
> streamingjobs が実行されている場合、タグを追加することはできません。 タグを追加するにはリソースを停止します。

## <a name="microsoftsubscription"></a>Microsoft.Subscription
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| CreateSubscription | いいえ |  いいえ |
| SubscriptionDefinitions | いいえ |  いいえ |
| SubscriptionOperations | いいえ |  いいえ |

## <a name="microsoftsupport"></a>microsoft.support
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| supporttickets | いいえ |  いいえ |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| providerRegistrations | はい | はい |
| resources | はい | はい |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| 環境 | はい | いいえ |
| environments/accessPolicies | いいえ |  いいえ |
| environments/eventsource | はい | いいえ |
| environments/referenceDataSets | はい | いいえ |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| account | はい | はい |
| account/extension | はい | はい |
| account/project | はい | はい |

## <a name="microsoftweb"></a>Microsoft.Web
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| apiManagementAccounts | いいえ |  いいえ |
| apiManagementAccounts/apiAcls | いいえ |  いいえ |
| apiManagementAccounts/apis | いいえ |  いいえ |
| apiManagementAccounts/apis/apiAcls | いいえ |  いいえ |
| apiManagementAccounts/apis/connectionAcls | いいえ |  いいえ |
| apiManagementAccounts/apis/connections | いいえ |  いいえ |
| apiManagementAccounts/apis/connections/connectionAcls | いいえ |  いいえ |
| apiManagementAccounts/apis/localizedDefinitions | いいえ |  いいえ |
| apiManagementAccounts/connectionAcls | いいえ |  いいえ |
| apiManagementAccounts/connections | いいえ |  いいえ |
| billingMeters | いいえ |  いいえ |
| certificates | はい | はい |
| connectionGateways | はい | はい |
| connections | はい | はい |
| customApis | はい | はい |
| deletedSites | いいえ |  いいえ |
| functions | いいえ |  いいえ |
| hostingEnvironments | はい | はい |
| hostingEnvironments/multiRolePools | いいえ |  いいえ |
| hostingEnvironments/multiRolePools/instances | いいえ |  いいえ |
| hostingEnvironments/workerPools | いいえ |  いいえ |
| hostingEnvironments/workerPools/instances | いいえ |  いいえ |
| publishingUsers | いいえ |  いいえ |
| recommendations | いいえ |  いいえ |
| resourceHealthMetadata | いいえ |  いいえ |
| runtimes | いいえ |  いいえ |
| serverFarms | はい | はい |
| serverFarms/workers | いいえ |  いいえ |
| sites | はい | はい |
| sites/domainOwnershipIdentifiers | いいえ |  いいえ |
| sites/hostNameBindings | いいえ |  いいえ |
| sites/instances | いいえ |  いいえ |
| sites/instances/extensions | いいえ |  いいえ |
| sites/premieraddons | はい | はい |
| sites/recommendations | いいえ |  いいえ |
| sites/resourceHealthMetadata | いいえ |  いいえ |
| sites/slots | はい | はい |
| sites/slots/hostNameBindings | いいえ |  いいえ |
| sites/slots/instances | いいえ |  いいえ |
| sites/slots/instances/extensions | いいえ |  いいえ |
| sourceControls | いいえ |  いいえ |
| validate | いいえ |  いいえ |
| verifyHostingEnvironmentVnet | いいえ |  いいえ |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| diagnosticSettings | いいえ |  いいえ |
| diagnosticSettingsCategories | いいえ |  いいえ |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| DeviceServices | はい | はい |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor
| リソースの種類 | タグのサポート | コスト レポートのタグ |
| ------------- | ----------- | ----------- |
| components | いいえ |  いいえ |
| componentsSummary | いいえ |  いいえ |
| monitorInstances | いいえ |  いいえ |
| monitorInstancesSummary | いいえ |  いいえ |
| monitors | いいえ |  いいえ |
| notificationSettings | いいえ |  いいえ |

## <a name="next-steps"></a>次の手順
リソースにタグを適用する方法については、「[タグを使用した Azure リソースの整理](resource-group-using-tags.md)」をご覧ください。
