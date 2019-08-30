---
title: Azure Resource Manager のリソースの種類に応じた完全モードの削除
description: リソースの種類に応じて Azure Resource Manager テンプレートで完全モードがどのように処理されるかを示します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/04/2019
ms.author: tomfitz
ms.openlocfilehash: 7f804e61f77b745aa654852f4c1413a8f4bf53b4
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779946"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>完全モード デプロイでの Azure リソースの削除

この記事では、リソースの種類に応じて、完全モードにデプロイされているテンプレート以外での削除の方法がどのように異なるかを説明します。

**はい**とマークされたリソースの種類は、完全モードでデプロイされるテンプレート内にない場合、削除されます。

**いいえ**とマークされたリソースの種類は、テンプレート内になくても自動的に削除されることはありませんが、親リソースが削除された場合は削除されます。 動作の詳細については、「[Azure Resource Manager のデプロイ モード](deployment-modes.md)」をご覧ください。

リソース プロバイダーの名前空間に移動します。
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.AADDomainServices](#microsoftaaddomainservices)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.Azconfig](#microsoftazconfig)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedLab](#microsoftmanagedlab)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.RemoteApp](#microsoftremoteapp)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | DomainServices | はい |
> | DomainServices/oucontainer | いいえ |
> | DomainServices/ReplicaSets | はい |

## <a name="microsoftaaddomainservices"></a>Microsoft.AADDomainServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | domains | いいえ |

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
> | servicehealthmetrics | いいえ |
> | services | いいえ |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | configuration | いいえ |
> | generateRecommendations | いいえ |
> | metadata | いいえ |
> | recommendations | いいえ |
> | suppressions | いいえ |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | actionRules | はい |
> | alerts | いいえ |
> | alertsList | いいえ |
> | alertsMetaData | いいえ |
> | alertsSummary | いいえ |
> | alertsSummaryList | いいえ |
> | feedback | いいえ |
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

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | configurationStores | はい |
> | configurationStores/eventGridFilters | いいえ |

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
> | dataAliases | いいえ |
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

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | configurationStores | はい |
> | configurationStores/eventGridFilters | いいえ |

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
> | b2ctenants | いいえ |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | sqlServerRegistrations | はい |
> | sqlServerRegistrations/sqlServers | いいえ |

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
> | billingAccounts/agreements | いいえ |
> | billingAccounts/billingProfiles | いいえ |
> | billingAccounts/billingProfiles/billingSubscriptions | いいえ |
> | billingAccounts/billingProfiles/invoices | いいえ |
> | billingAccounts/billingProfiles/invoices/pricesheet | いいえ |
> | billingAccounts/billingProfiles/invoiceSections | いいえ |
> | billingAccounts/BillingProfiles/patchOperations | いいえ |
> | billingAccounts/billingProfiles/paymentMethods | いいえ |
> | billingAccounts/billingProfiles/policies | いいえ |
> | billingAccounts/billingProfiles/pricesheet | いいえ |
> | billingAccounts/billingProfiles/pricesheetDownloadOperations | いいえ |
> | billingAccounts/billingProfiles/products | いいえ |
> | billingAccounts/billingProfiles/transactions | いいえ |
> | billingAccounts/billingSubscriptions | いいえ |
> | billingAccounts/createInvoiceSectionOperations | いいえ |
> | billingAccounts/customers | いいえ |
> | billingAccounts/customers/billingSubscriptions | いいえ |
> | billingAccounts/departments | いいえ |
> | billingAccounts/enrollmentAccounts | いいえ |
> | billingAccounts/invoices | いいえ |
> | billingAccounts/invoiceSections | いいえ |
> | billingAccounts/invoiceSections/billingSubscriptionMoveOperations | いいえ |
> | billingAccounts/invoiceSections/billingSubscriptions | いいえ |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | いいえ |
> | billingAccounts/invoiceSections/elevate | いいえ |
> | billingAccounts/invoiceSections/initiateTransfer | いいえ |
> | billingAccounts/invoiceSections/patchOperations | いいえ |
> | billingAccounts/invoiceSections/productMoveOperations | いいえ |
> | billingAccounts/invoiceSections/products | いいえ |
> | billingAccounts/invoiceSections/products/transfer | いいえ |
> | billingAccounts/invoiceSections/products/updateAutoRenew | いいえ |
> | billingAccounts/invoiceSections/transactions | いいえ |
> | billingAccounts/invoiceSections/transfers | いいえ |
> | billingAccounts/lineOfCredit | いいえ |
> | billingAccounts/patchOperations | いいえ |
> | billingAccounts/paymentMethods | いいえ |
> | billingAccounts/products | いいえ |
> | billingAccounts/transactions | いいえ |
> | billingPeriods | いいえ |
> | billingPermissions | いいえ |
> | billingProperty | いいえ |
> | billingRoleAssignments | いいえ |
> | billingRoleDefinitions | いいえ |
> | createBillingRoleAssignment | いいえ |
> | departments | いいえ |
> | enrollmentAccounts | いいえ |
> | invoices | いいえ |
> | transfers | いいえ |
> | transfers/acceptTransfer | いいえ |
> | transfers/declineTransfer | いいえ |
> | transfers/operationStatus | いいえ |
> | validateAddress | いいえ |

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

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | blockchainMembers | はい |
> | watchers | はい |

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
> | languages | いいえ |
> | テンプレート | いいえ |

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
> | calculateExchange | いいえ |
> | calculatePrice | いいえ |
> | calculatePurchasePrice | いいえ |
> | catalogs | いいえ |
> | commercialReservationOrders | いいえ |
> | exchange | いいえ |
> | placePurchaseOrder | いいえ |
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
> | CdnWebApplicationFirewallManagedRuleSets | いいえ |
> | CdnWebApplicationFirewallPolicies | はい |
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
> | domainNames | はい |
> | domainNames/capabilities | いいえ |
> | domainNames/internalLoadBalancers | いいえ |
> | domainNames/serviceCertificates | いいえ |
> | domainNames/slots | いいえ |
> | domainNames/slots/roles | いいえ |
> | domainNames/slots/roles/metricDefinitions | いいえ |
> | domainNames/slots/roles/metrics | いいえ |
> | moveSubscriptionResources | いいえ |
> | operatingSystemFamilies | いいえ |
> | operatingSystems | いいえ |
> | quotas | いいえ |
> | resourceTypes | いいえ |
> | validateSubscriptionMoveAvailability | いいえ |
> | virtualMachines | はい |
> | virtualMachines/diagnosticSettings | いいえ |
> | virtualMachines/metricDefinitions | いいえ |
> | virtualMachines/metrics | いいえ |

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
> | networkSecurityGroups | はい |
> | quotas | いいえ |
> | reservedIps | はい |
> | virtualNetworks | はい |
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
> | storageAccounts | はい |
> | storageAccounts/metricDefinitions | いいえ |
> | storageAccounts/metrics | いいえ |
> | storageAccounts/services | いいえ |
> | storageAccounts/services/diagnosticSettings | いいえ |
> | storageAccounts/services/metricDefinitions | いいえ |
> | storageAccounts/services/metrics | いいえ |
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
> | diskEncryptionSets | はい |
> | disks | はい |
> | galleries | はい |
> | galleries/applications | はい |
> | galleries/applications/versions | はい |
> | galleries/images | はい |
> | galleries/images/versions | はい |
> | hostGroups | はい |
> | hostGroups/hosts | はい |
> | images | はい |
> | proximityPlacementGroups | はい |
> | restorePointCollections | はい |
> | restorePointCollections/restorePoints | いいえ |
> | sharedVMImages | はい |
> | sharedVMImages/versions | はい |
> | snapshots | はい |
> | virtualMachines | はい |
> | virtualMachines/extensions | はい |
> | virtualMachines/metricDefinitions | いいえ |
> | virtualMachines/scriptJobs | いいえ |
> | virtualMachines/softwareUpdateDeployments | いいえ |
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
> | tenants | いいえ |
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
> | openShiftManagedClusters | はい |

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
> | Budgets | いいえ |
> | CloudConnectors | いいえ |
> | Connectors | はい |
> | Departments | いいえ |
> | Dimensions | いいえ |
> | EnrollmentAccounts | いいえ |
> | Exports | いいえ |
> | ExternalBillingAccounts | いいえ |
> | ExternalBillingAccounts/Alerts | いいえ |
> | ExternalBillingAccounts/Dimensions | いいえ |
> | ExternalBillingAccounts/Forecast | いいえ |
> | ExternalBillingAccounts/Query | いいえ |
> | ExternalSubscriptions | いいえ |
> | ExternalSubscriptions/Alerts | いいえ |
> | ExternalSubscriptions/Dimensions | いいえ |
> | ExternalSubscriptions/Forecast | いいえ |
> | ExternalSubscriptions/Query | いいえ |
> | Forecast | いいえ |
> | Query | いいえ |
> | registrations | いいえ |
> | Reportconfigs | いいえ |
> | Reports | いいえ |
> | 設定 | いいえ |
> | showbackRules | いいえ |
> | ビュー | いいえ |

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

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | requests | いいえ |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | associations | いいえ |
> | resourceProviders | はい |

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
> | datacatalogs | はい |
> | datacatalogs/scantargets | いいえ |
> | datacatalogs/scantargets/datasets | いいえ |

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
> | dataFactories/metricDefinitions | いいえ |
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
> | accounts/transferAnalyticsUnits | いいえ |

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
> | slots | はい |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | accounts | はい |
> | accounts/shares | いいえ |
> | accounts/shares/datasets | いいえ |
> | accounts/shares/invitations | いいえ |
> | accounts/shares/providersharesubscriptions | いいえ |
> | accounts/shares/synchronizationSettings | いいえ |
> | accounts/sharesubscriptions | いいえ |
> | accounts/sharesubscriptions/consumerSourceDataSets | いいえ |
> | accounts/sharesubscriptions/datasetmappings | いいえ |
> | accounts/sharesubscriptions/triggers | いいえ |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

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

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

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

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | serverGroups | はい |
> | servers | はい |
> | servers/advisors | いいえ |
> | servers/queryTexts | いいえ |
> | servers/recoverableServers | いいえ |
> | servers/topQueryStatistics | いいえ |
> | servers/virtualNetworkRules | いいえ |
> | servers/waitStatistics | いいえ |
> | serversv2 | はい |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | artifactSources | はい |
> | rollouts | はい |
> | serviceTopologies | はい |
> | serviceTopologies/services | はい |
> | serviceTopologies/services/serviceUnits | はい |
> | steps | はい |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | applicationgroups | はい |
> | applicationgroups/applications | いいえ |
> | applicationgroups/assignedusers | いいえ |
> | applicationgroups/startmenuitems | いいえ |
> | hostpools | はい |
> | hostpools/sessionhosts | いいえ |
> | hostpools/sessionhosts/usersessions | いいえ |
> | hostpools/usersessions | いいえ |
> | workspaces | はい |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | ElasticPools | はい |
> | ElasticPools/IotHubTenants | はい |
> | IotHubs | はい |
> | IotHubs/eventGridFilters | いいえ |
> | ProvisioningServices | はい |
> | usages | いいえ |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | pipelines | はい |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | controllers | はい |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | labcenters | はい |
> | labs | はい |
> | labs/environments | はい |
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

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | services | はい |

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
> | namespaces/networkrulesets | いいえ |

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

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | accounts | はい |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | guestConfigurationAssignments | いいえ |
> | software | いいえ |
> | softwareUpdateProfile | いいえ |
> | softwareUpdates | いいえ |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | hanaInstances | はい |
> | sapMonitors | はい |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | dedicatedHSMs | はい |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | clusters | はい |
> | clusters/applications | いいえ |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | services | はい |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | machines | はい |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | dataManagers | はい |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | components | はい |
> | networkScopes | はい |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | jobs | はい |

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
> | appTemplates | いいえ |
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
> | hsmPools | はい |
> | vaults | はい |
> | vaults/accessPolicies | いいえ |
> | vaults/eventGridFilters | いいえ |
> | vaults/secrets | いいえ |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | clusters | はい |
> | clusters/attacheddatabaseconfigurations | いいえ |
> | clusters/databases | いいえ |
> | clusters/databases/dataconnections | いいえ |
> | clusters/databases/eventhubconnections | いいえ |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | labaccounts | はい |
> | users | いいえ |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | hostingEnvironments | はい |
> | integrationAccounts | はい |
> | integrationServiceEnvironments | はい |
> | isolatedEnvironments | はい |
> | workflows | はい |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | commitmentPlans | はい |
> | webServices | はい |
> | Workspaces | はい |

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

## <a name="microsoftmanagedlab"></a>Microsoft.ManagedLab

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | labaccounts | はい |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | いいえ |
> | registrationAssignments | いいえ |
> | registrationDefinitions | いいえ |

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
> | publishers | いいえ |
> | publishers/offers | いいえ |
> | publishers/offers/amendments | いいえ |

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

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | appClusters | はい |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | assessmentProjects | はい |
> | migrateprojects | はい |
> | projects | はい |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | remoteRenderingAccounts | はい |
> | spatialAnchorsAccounts | はい |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | netAppAccounts | はい |
> | netAppAccounts/capacityPools | はい |
> | netAppAccounts/capacityPools/volumes | はい |
> | netAppAccounts/capacityPools/volumes/mountTargets | はい |
> | netAppAccounts/capacityPools/volumes/snapshots | はい |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | applicationGateways | はい |
> | applicationGatewayWebApplicationFirewallPolicies | はい |
> | applicationSecurityGroups | はい |
> | azureFirewallFqdnTags | いいえ |
> | azureFirewalls | はい |
> | bastionHosts | はい |
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
> | expressRouteCrossConnections | はい |
> | expressRouteGateways | はい |
> | expressRoutePorts | はい |
> | expressRouteServiceProviders | いいえ |
> | firewallPolicies | はい |
> | frontdoors | はい |
> | frontdoorWebApplicationFirewallManagedRuleSets | いいえ |
> | frontdoorWebApplicationFirewallPolicies | はい |
> | getDnsResourceReference | いいえ |
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
> | p2sVpnGateways | はい |
> | privateDnsOperationStatuses | いいえ |
> | privateDnsZones | はい |
> | privateDnsZones/A | いいえ |
> | privateDnsZones/AAAA | いいえ |
> | privateDnsZones/all | いいえ |
> | privateDnsZones/CNAME | いいえ |
> | privateDnsZones/MX | いいえ |
> | privateDnsZones/PTR | いいえ |
> | privateDnsZones/SOA | いいえ |
> | privateDnsZones/SRV | いいえ |
> | privateDnsZones/TXT | いいえ |
> | privateDnsZones/virtualNetworkLinks | はい |
> | privateEndpoints | はい |
> | privateLinkServices | はい |
> | publicIPAddresses | はい |
> | publicIPPrefixes | はい |
> | routeFilters | はい |
> | routeTables | はい |
> | secureGateways | はい |
> | serviceEndpointPolicies | はい |
> | trafficManagerGeographicHierarchies | いいえ |
> | trafficmanagerprofiles | はい |
> | trafficmanagerprofiles/heatMaps | いいえ |
> | trafficManagerUserMetricsKeys | いいえ |
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

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | HyperVSites | はい |
> | ImportSites | はい |
> | ServerSites | はい |
> | VMwareSites | はい |

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

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | legacyPeerings | いいえ |
> | peerAsns | いいえ |
> | peerings | はい |

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

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | accounts | いいえ |
> | collections | はい |
> | collections/applications | いいえ |
> | collections/securityprincipals | いいえ |
> | templateImages | いいえ |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | Query | はい |
> | resourceChangeDetails | いいえ |
> | resourceChanges | いいえ |
> | resources | いいえ |
> | resourcesHistory | いいえ |
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
> | metadata | いいえ |
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
> | tags | いいえ |
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
> | adaptiveNetworkHardenings | いいえ |
> | advancedThreatProtectionSettings | いいえ |
> | alerts | いいえ |
> | allowedConnections | いいえ |
> | applicationWhitelistings | いいえ |
> | assessmentMetadata | いいえ |
> | assessments | いいえ |
> | AutoProvisioningSettings | いいえ |
> | Compliances | いいえ |
> | dataCollectionAgents | いいえ |
> | deviceSecurityGroups | いいえ |
> | discoveredSecuritySolutions | いいえ |
> | externalSecuritySolutions | いいえ |
> | InformationProtectionPolicies | いいえ |
> | iotSecuritySolutions | はい |
> | iotSecuritySolutions/analyticsModels | いいえ |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | いいえ |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | いいえ |
> | jitNetworkAccessPolicies | いいえ |
> | playbookConfigurations | はい |
> | policies | いいえ |
> | pricings | いいえ |
> | regulatoryComplianceStandards | いいえ |
> | regulatoryComplianceStandards/regulatoryComplianceControls | いいえ |
> | regulatoryComplianceStandards/regulatoryComplianceControls/regulatoryComplianceAssessments | いいえ |
> | securityContacts | いいえ |
> | securitySolutions | いいえ |
> | securitySolutionsReferenceData | いいえ |
> | securityStatuses | いいえ |
> | securityStatusesSummaries | いいえ |
> | serverVulnerabilityAssessments | いいえ |
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

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | aggregations | いいえ |
> | alertRules | いいえ |
> | bookmarks | いいえ |
> | cases | いいえ |
> | dataConnectors | いいえ |
> | entities | いいえ |
> | entityQueries | いいえ |
> | officeConsents | いいえ |
> | settings | いいえ |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | namespaces | はい |
> | namespaces/authorizationrules | いいえ |
> | namespaces/disasterrecoveryconfigs | いいえ |
> | namespaces/eventgridfilters | いいえ |
> | namespaces/networkrulesets | いいえ |
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
> | applications | はい |
> | clusters | はい |
> | clusters/applications | いいえ |
> | containerGroups | はい |
> | containerGroupSets | はい |
> | edgeclusters | はい |
> | edgeclusters/applications | いいえ |
> | networks | はい |
> | secretstores | はい |
> | secretstores/certificates | いいえ |
> | secretstores/secrets | いいえ |
> | volumes | はい |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | applications | はい |
> | containerGroups | はい |
> | gateways | はい |
> | networks | はい |
> | secrets | はい |
> | volumes | はい |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | providerRegistrations | いいえ |
> | providerRegistrations/resourceTypeRegistrations | いいえ |
> | rollouts | はい |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | SignalR | はい |
> | SignalR/eventGridFilters | いいえ |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | SiteRecoveryVault | はい |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | hybridUseBenefits | いいえ |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
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
> | storageAccounts/services/metricDefinitions | いいえ |
> | storageAccounts/tableServices | いいえ |
> | usages | いいえ |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | caches | はい |
> | caches/storageTargets | いいえ |
> | usageModels | いいえ |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | replicationGroups | いいえ |

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

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | storageSyncServices | はい |
> | storageSyncServices/registeredServers | いいえ |
> | storageSyncServices/syncGroups | いいえ |
> | storageSyncServices/syncGroups/cloudEndpoints | いいえ |
> | storageSyncServices/syncGroups/serverEndpoints | いいえ |
> | storageSyncServices/workflows | いいえ |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

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

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | cancel | いいえ |
> | CreateSubscription | いいえ |
> | rename | いいえ |
> | SubscriptionDefinitions | いいえ |
> | SubscriptionOperations | いいえ |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | 環境 | はい |
> | environments/accessPolicies | いいえ |
> | environments/eventsource | はい |
> | environments/referenceDataSets | はい |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | リソースの種類 | 完全モードの削除 |
> | ------------- | ----------- |
> | dedicatedCloudNodes | はい |
> | dedicatedCloudServices | はい |
> | virtualMachines | はい |

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
> | hostingEnvironments/workerPools | いいえ |
> | publishingUsers | いいえ |
> | recommendations | いいえ |
> | resourceHealthMetadata | いいえ |
> | runtimes | いいえ |
> | serverFarms | はい |
> | serverFarms/eventGridFilters | いいえ |
> | sites | はい |
> | sites/config  | いいえ |
> | sites/eventGridFilters | いいえ |
> | sites/hostNameBindings | いいえ |
> | sites/networkConfig | いいえ |
> | sites/premieraddons | はい |
> | sites/slots | はい |
> | sites/slots/eventGridFilters | いいえ |
> | sites/slots/hostNameBindings | いいえ |
> | sites/slots/networkConfig | いいえ |
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
