---
title: リソースでの Azure Resource Manager タグのサポート
description: タグをサポートしている Azure リソースの種類を示します。 すべての Azure サービスの詳細を提供します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/05/2019
ms.author: tomfitz
ms.openlocfilehash: 452230a263e623f53346fcdb8cf310e73f171aee
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848601"
---
# <a name="tag-support-for-azure-resources"></a>Azure リソースでのタグのサポート
この記事では、リソースの種類が[タグ](resource-group-using-tags.md)をサポートしているかどうかについて説明します。 「**タグのサポート**」というラベルが付けられた列は、リソースの種類にタグのプロパティがあるかどうかを示します。 「**コスト レポートのタグ**」というラベルが付けられた列は、リソースの種類がタグをコスト レポートに渡すかどうかを示します。

コンマ区切りの値のファイルと同じデータを取得するには、[tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv) をダウンロードします。

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
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | DomainServices | はい |はい |
> | DomainServices/oucontainer | いいえ |いいえ |
> | DomainServices/ReplicaSets | はい |はい |

## <a name="microsoftaaddomainservices"></a>Microsoft.AADDomainServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | domains | いいえ |いいえ |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | supportProviders | いいえ |いいえ |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | いいえ |いいえ |
> | addsservices | いいえ |いいえ |
> | agents | いいえ |いいえ |
> | anonymousapiusers | いいえ |いいえ |
> | configuration | いいえ |いいえ |
> | logs | いいえ |いいえ |
> | reports | いいえ |いいえ |
> | servicehealthmetrics | いいえ |いいえ |
> | services | いいえ |いいえ |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | configuration | いいえ |いいえ |
> | generateRecommendations | いいえ |いいえ |
> | metadata | いいえ |いいえ |
> | recommendations | いいえ |いいえ |
> | suppressions | いいえ |いいえ |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | actionRules | はい |はい |
> | alerts | いいえ |いいえ |
> | alertsList | いいえ |いいえ |
> | alertsMetaData | いいえ |いいえ |
> | alertsSummary | いいえ |いいえ |
> | alertsSummaryList | いいえ |いいえ |
> | feedback | いいえ |いいえ |
> | smartDetectorAlertRules | いいえ |いいえ |
> | smartDetectorRuntimeEnvironments | いいえ |いいえ |
> | smartGroups | いいえ |いいえ |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | servers | はい |はい |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | reportFeedback | いいえ |いいえ |
> | service | はい |はい |
> | validateServiceName | いいえ |いいえ |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | configurationStores | はい |はい |
> | configurationStores/eventGridFilters | いいえ |いいえ |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | attestationProviders | いいえ |いいえ |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | いいえ |いいえ |
> | dataAliases | いいえ |いいえ |
> | denyAssignments | いいえ |いいえ |
> | elevateAccess | いいえ |いいえ |
> | locks | いいえ |いいえ |
> | permissions | いいえ |いいえ |
> | policyAssignments | いいえ |いいえ |
> | policyDefinitions | いいえ |いいえ |
> | policySetDefinitions | いいえ |いいえ |
> | providerOperations | いいえ |いいえ |
> | roleAssignments | いいえ |いいえ |
> | roleDefinitions | いいえ |いいえ |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | automationAccounts | はい |はい |
> | automationAccounts/configurations | はい |はい |
> | automationAccounts/jobs | いいえ |いいえ |
> | automationAccounts/runbooks | はい |はい |
> | automationAccounts/softwareUpdateConfigurations | いいえ |いいえ |
> | automationAccounts/webhooks | いいえ |いいえ |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | configurationStores | はい |はい |
> | configurationStores/eventGridFilters | いいえ |いいえ |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | 環境 | いいえ |いいえ |
> | environments/accounts | いいえ |いいえ |
> | environments/accounts/namespaces | いいえ |いいえ |
> | environments/accounts/namespaces/configurations | いいえ |いいえ |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | はい |いいえ |
> | b2ctenants | いいえ |いいえ |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | sqlServerRegistrations | はい |はい |
> | sqlServerRegistrations/sqlServers | いいえ |いいえ |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | registrations | はい |はい |
> | registrations/customerSubscriptions | いいえ |いいえ |
> | registrations/products | いいえ |いいえ |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | batchAccounts | はい |はい |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | billingAccounts | いいえ |いいえ |
> | billingAccounts/agreements | いいえ |いいえ |
> | billingAccounts/billingProfiles | いいえ |いいえ |
> | billingAccounts/billingProfiles/billingSubscriptions | いいえ |いいえ |
> | billingAccounts/billingProfiles/invoices | いいえ |いいえ |
> | billingAccounts/billingProfiles/invoices/pricesheet | いいえ |いいえ |
> | billingAccounts/billingProfiles/invoiceSections | いいえ |いいえ |
> | billingAccounts/BillingProfiles/patchOperations | いいえ |いいえ |
> | billingAccounts/billingProfiles/paymentMethods | いいえ |いいえ |
> | billingAccounts/billingProfiles/policies | いいえ |いいえ |
> | billingAccounts/billingProfiles/pricesheet | いいえ |いいえ |
> | billingAccounts/billingProfiles/pricesheetDownloadOperations | いいえ |いいえ |
> | billingAccounts/billingProfiles/products | いいえ |いいえ |
> | billingAccounts/billingProfiles/transactions | いいえ |いいえ |
> | billingAccounts/billingSubscriptions | いいえ |いいえ |
> | billingAccounts/createInvoiceSectionOperations | いいえ |いいえ |
> | billingAccounts/customers | いいえ |いいえ |
> | billingAccounts/customers/billingSubscriptions | いいえ |いいえ |
> | billingAccounts/departments | いいえ |いいえ |
> | billingAccounts/enrollmentAccounts | いいえ |いいえ |
> | billingAccounts/invoices | いいえ |いいえ |
> | billingAccounts/invoiceSections | いいえ |いいえ |
> | billingAccounts/invoiceSections/billingSubscriptionMoveOperations | いいえ |いいえ |
> | billingAccounts/invoiceSections/billingSubscriptions | いいえ |いいえ |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | いいえ |いいえ |
> | billingAccounts/invoiceSections/elevate | いいえ |いいえ |
> | billingAccounts/invoiceSections/initiateTransfer | いいえ |いいえ |
> | billingAccounts/invoiceSections/patchOperations | いいえ |いいえ |
> | billingAccounts/invoiceSections/productMoveOperations | いいえ |いいえ |
> | billingAccounts/invoiceSections/products | いいえ |いいえ |
> | billingAccounts/invoiceSections/products/transfer | いいえ |いいえ |
> | billingAccounts/invoiceSections/products/updateAutoRenew | いいえ |いいえ |
> | billingAccounts/invoiceSections/transactions | いいえ |いいえ |
> | billingAccounts/invoiceSections/transfers | いいえ |いいえ |
> | billingAccounts/lineOfCredit | いいえ |いいえ |
> | billingAccounts/patchOperations | いいえ |いいえ |
> | billingAccounts/paymentMethods | いいえ |いいえ |
> | billingAccounts/products | いいえ |いいえ |
> | billingAccounts/transactions | いいえ |いいえ |
> | billingPeriods | いいえ |いいえ |
> | billingPermissions | いいえ |いいえ |
> | billingProperty | いいえ |いいえ |
> | billingRoleAssignments | いいえ |いいえ |
> | billingRoleDefinitions | いいえ |いいえ |
> | createBillingRoleAssignment | いいえ |いいえ |
> | departments | いいえ |いいえ |
> | enrollmentAccounts | いいえ |いいえ |
> | invoices | いいえ |いいえ |
> | transfers | いいえ |いいえ |
> | transfers/acceptTransfer | いいえ |いいえ |
> | transfers/declineTransfer | いいえ |いいえ |
> | transfers/operationStatus | いいえ |いいえ |
> | validateAddress | いいえ |いいえ |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | mapApis | はい |はい |
> | updateCommunicationPreference | いいえ |いいえ |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | BizTalk | はい |はい |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | はい |はい |
> | watchers | はい |はい |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | いいえ |いいえ |
> | blueprintAssignments/assignmentOperations | いいえ |いいえ |
> | blueprintAssignments/operations | いいえ |いいえ |
> | blueprints | いいえ |いいえ |
> | blueprints/artifacts | いいえ |いいえ |
> | blueprints/versions | いいえ |いいえ |
> | blueprints/versions/artifacts | いいえ |いいえ |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | botServices | はい |はい |
> | botServices/channels | いいえ |いいえ |
> | botServices/connections | いいえ |いいえ |
> | languages | いいえ |いいえ |
> | テンプレート | いいえ |いいえ |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | Redis | はい |はい |
> | RedisConfigDefinition | いいえ |いいえ |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | appliedReservations | いいえ |いいえ |
> | calculateExchange | いいえ |いいえ |
> | calculatePrice | いいえ |いいえ |
> | calculatePurchasePrice | いいえ |いいえ |
> | catalogs | いいえ |いいえ |
> | commercialReservationOrders | いいえ |いいえ |
> | exchange | いいえ |いいえ |
> | placePurchaseOrder | いいえ |いいえ |
> | reservationOrders | いいえ |いいえ |
> | reservationOrders/calculateRefund | いいえ |いいえ |
> | reservationOrders/merge | いいえ |いいえ |
> | reservationOrders/reservations | いいえ |いいえ |
> | reservationOrders/reservations/revisions | いいえ |いいえ |
> | reservationOrders/return | いいえ |いいえ |
> | reservationOrders/split | いいえ |いいえ |
> | reservationOrders/swap | いいえ |いいえ |
> | reservations | いいえ |いいえ |
> | resources | いいえ |いいえ |
> | validateReservationOrder | いいえ |いいえ |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | いいえ |いいえ |
> | CdnWebApplicationFirewallPolicies | はい |はい |
> | edgenodes | いいえ |いいえ |
> | profiles | はい |はい |
> | profiles/endpoints | はい |はい |
> | profiles/endpoints/customdomains | いいえ |いいえ |
> | profiles/endpoints/origins | いいえ |いいえ |
> | validateProbe | いいえ |いいえ |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | certificateOrders | はい |はい |
> | certificateOrders/certificates | いいえ |いいえ |
> | validateCertificateRegistrationInformation | いいえ |いいえ |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | capabilities | いいえ |いいえ |
> | domainNames | はい |はい |
> | domainNames/capabilities | いいえ |いいえ |
> | domainNames/internalLoadBalancers | いいえ |いいえ |
> | domainNames/serviceCertificates | いいえ |いいえ |
> | domainNames/slots | いいえ |いいえ |
> | domainNames/slots/roles | いいえ |いいえ |
> | domainNames/slots/roles/metricDefinitions | いいえ |いいえ |
> | domainNames/slots/roles/metrics | いいえ |いいえ |
> | moveSubscriptionResources | いいえ |いいえ |
> | operatingSystemFamilies | いいえ |いいえ |
> | operatingSystems | いいえ |いいえ |
> | quotas | いいえ |いいえ |
> | resourceTypes | いいえ |いいえ |
> | validateSubscriptionMoveAvailability | いいえ |いいえ |
> | virtualMachines | はい |はい |
> | virtualMachines/diagnosticSettings | いいえ |いいえ |
> | virtualMachines/metricDefinitions | いいえ |いいえ |
> | virtualMachines/metrics | いいえ |いいえ |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | いいえ |いいえ |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | capabilities | いいえ |いいえ |
> | expressRouteCrossConnections | いいえ |いいえ |
> | expressRouteCrossConnections/peerings | いいえ |いいえ |
> | gatewaySupportedDevices | いいえ |いいえ |
> | networkSecurityGroups | はい |はい |
> | quotas | いいえ |いいえ |
> | reservedIps | はい |はい |
> | virtualNetworks | はい |はい |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | いいえ |いいえ |
> | virtualNetworks/virtualNetworkPeerings | いいえ |いいえ |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | capabilities | いいえ |いいえ |
> | disks | いいえ |いいえ |
> | images | いいえ |いいえ |
> | osImages | いいえ |いいえ |
> | osPlatformImages | いいえ |いいえ |
> | publicImages | いいえ |いいえ |
> | quotas | いいえ |いいえ |
> | storageAccounts | はい |はい |
> | storageAccounts/metricDefinitions | いいえ |いいえ |
> | storageAccounts/metrics | いいえ |いいえ |
> | storageAccounts/services | いいえ |いいえ |
> | storageAccounts/services/diagnosticSettings | いいえ |いいえ |
> | storageAccounts/services/metricDefinitions | いいえ |いいえ |
> | storageAccounts/services/metrics | いいえ |いいえ |
> | storageAccounts/vmImages | いいえ |いいえ |
> | vmImages | いいえ |いいえ |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | accounts | はい |はい |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | RateCard | いいえ |いいえ |
> | UsageAggregates | いいえ |いいえ |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | availabilitySets | はい |はい |
> | diskEncryptionSets | はい |はい |
> | disks | はい |はい |
> | galleries | はい |はい |
> | galleries/applications | はい |はい |
> | galleries/applications/versions | はい |はい |
> | galleries/images | はい |はい |
> | galleries/images/versions | はい |はい |
> | hostGroups | はい |はい |
> | hostGroups/hosts | はい |はい |
> | images | はい |はい |
> | proximityPlacementGroups | はい |はい |
> | restorePointCollections | はい |はい |
> | restorePointCollections/restorePoints | いいえ |いいえ |
> | sharedVMImages | はい |はい |
> | sharedVMImages/versions | はい |はい |
> | snapshots | はい |はい |
> | virtualMachines | はい |はい |
> | virtualMachines/extensions | はい |はい |
> | virtualMachines/metricDefinitions | いいえ |いいえ |
> | virtualMachines/scriptJobs | いいえ |いいえ |
> | virtualMachines/softwareUpdateDeployments | いいえ |いいえ |
> | virtualMachineScaleSets | はい |はい |
> | virtualMachineScaleSets/extensions | いいえ |いいえ |
> | virtualMachineScaleSets/networkInterfaces | いいえ |いいえ |
> | virtualMachineScaleSets/publicIPAddresses | いいえ |いいえ |
> | virtualMachineScaleSets/virtualMachines | いいえ |いいえ |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | いいえ |いいえ |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | いいえ |いいえ |
> | Balances | いいえ |いいえ |
> | Budgets | いいえ |いいえ |
> | Charges | いいえ |いいえ |
> | CostTags | いいえ |いいえ |
> | credits | いいえ |いいえ |
> | events | いいえ |いいえ |
> | Forecasts | いいえ |いいえ |
> | lots | いいえ |いいえ |
> | Marketplaces | いいえ |いいえ |
> | Pricesheets | いいえ |いいえ |
> | products | いいえ |いいえ |
> | ReservationDetails | いいえ |いいえ |
> | ReservationRecommendations | いいえ |いいえ |
> | ReservationSummaries | いいえ |いいえ |
> | ReservationTransactions | いいえ |いいえ |
> | Tags | いいえ |いいえ |
> | tenants | いいえ |いいえ |
> | Terms | いいえ |いいえ |
> | UsageDetails | いいえ |いいえ |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | containerGroups | はい |はい |
> | serviceAssociationLinks | いいえ |いいえ |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | registries | はい |はい |
> | registries/builds | いいえ |いいえ |
> | registries/builds/cancel | いいえ |いいえ |
> | registries/builds/getLogLink | いいえ |いいえ |
> | registries/buildTasks | はい |はい |
> | registries/buildTasks/steps | いいえ |いいえ |
> | registries/eventGridFilters | いいえ |いいえ |
> | registries/getBuildSourceUploadUrl | いいえ |いいえ |
> | registries/GetCredentials | いいえ |いいえ |
> | registries/importImage | いいえ |いいえ |
> | registries/queueBuild | いいえ |いいえ |
> | registries/regenerateCredential | いいえ |いいえ |
> | registries/regenerateCredentials | いいえ |いいえ |
> | registries/replications | はい |はい |
> | registries/runs | いいえ |いいえ |
> | registries/runs/cancel | いいえ |いいえ |
> | registries/scheduleRun | いいえ |いいえ |
> | registries/tasks | はい |はい |
> | registries/updatePolicies | いいえ |いいえ |
> | registries/webhooks | はい |はい |
> | registries/webhooks/getCallbackConfig | いいえ |いいえ |
> | registries/webhooks/ping | いいえ |いいえ |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | containerServices | はい |はい |
> | managedClusters | はい |はい |
> | openShiftManagedClusters | はい |はい |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | applications | はい |はい |
> | updateCommunicationPreference | いいえ |いいえ |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | accounts | はい |はい |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | アラート | いいえ |いいえ |
> | BillingAccounts | いいえ |いいえ |
> | Budgets | いいえ |いいえ |
> | CloudConnectors | いいえ |いいえ |
> | Connectors | はい |はい |
> | Departments | いいえ |いいえ |
> | Dimensions | いいえ |いいえ |
> | EnrollmentAccounts | いいえ |いいえ |
> | Exports | いいえ |いいえ |
> | ExternalBillingAccounts | いいえ |いいえ |
> | ExternalBillingAccounts/Alerts | いいえ |いいえ |
> | ExternalBillingAccounts/Dimensions | いいえ |いいえ |
> | ExternalBillingAccounts/Forecast | いいえ |いいえ |
> | ExternalBillingAccounts/Query | いいえ |いいえ |
> | ExternalSubscriptions | いいえ |いいえ |
> | ExternalSubscriptions/Alerts | いいえ |いいえ |
> | ExternalSubscriptions/Dimensions | いいえ |いいえ |
> | ExternalSubscriptions/Forecast | いいえ |いいえ |
> | ExternalSubscriptions/Query | いいえ |いいえ |
> | Forecast | いいえ |いいえ |
> | Query | いいえ |いいえ |
> | registrations | いいえ |いいえ |
> | Reportconfigs | いいえ |いいえ |
> | Reports | いいえ |いいえ |
> | 設定 | いいえ |いいえ |
> | showbackRules | いいえ |いいえ |
> | ビュー | いいえ |いいえ |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | hubs | はい |はい |
> | hubs/authorizationPolicies | いいえ |いいえ |
> | hubs/connectors | いいえ |いいえ |
> | hubs/connectors/mappings | いいえ |いいえ |
> | hubs/interactions | いいえ |いいえ |
> | hubs/kpi | いいえ |いいえ |
> | hubs/links | いいえ |いいえ |
> | hubs/profiles | いいえ |いいえ |
> | hubs/roleAssignments | いいえ |いいえ |
> | hubs/roles | いいえ |いいえ |
> | hubs/suggestTypeSchema | いいえ |いいえ |
> | hubs/views | いいえ |いいえ |
> | hubs/widgetTypes | いいえ |いいえ |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | requests | いいえ |いいえ |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | associations | いいえ |いいえ |
> | resourceProviders | はい |はい |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | jobs | はい |はい |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | はい |はい |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | workspaces | はい |いいえ |
> | workspaces/virtualNetworkPeerings | いいえ |いいえ |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | catalogs | はい |はい |
> | datacatalogs | はい |はい |
> | datacatalogs/scantargets | いいえ |いいえ |
> | datacatalogs/scantargets/datasets | いいえ |いいえ |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | connectionManagers | はい |はい |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | dataFactories | はい |いいえ |
> | dataFactories/diagnosticSettings | いいえ |いいえ |
> | dataFactories/metricDefinitions | いいえ |いいえ |
> | dataFactorySchema | いいえ |いいえ |
> | factories | はい |いいえ |
> | factories/integrationRuntimes | いいえ |いいえ |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | accounts | はい |はい |
> | accounts/dataLakeStoreAccounts | いいえ |いいえ |
> | accounts/storageAccounts | いいえ |いいえ |
> | accounts/storageAccounts/containers | いいえ |いいえ |
> | accounts/transferAnalyticsUnits | いいえ |いいえ |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | accounts | はい |はい |
> | accounts/eventGridFilters | いいえ |いいえ |
> | accounts/firewallRules | いいえ |いいえ |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | services | いいえ |いいえ |
> | services/projects | いいえ |いいえ |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | accounts | はい |はい |
> | accounts/shares | いいえ |いいえ |
> | accounts/shares/datasets | いいえ |いいえ |
> | accounts/shares/invitations | いいえ |いいえ |
> | accounts/shares/providersharesubscriptions | いいえ |いいえ |
> | accounts/shares/synchronizationSettings | いいえ |いいえ |
> | accounts/sharesubscriptions | いいえ |いいえ |
> | accounts/sharesubscriptions/consumerSourceDataSets | いいえ |いいえ |
> | accounts/sharesubscriptions/datasetmappings | いいえ |いいえ |
> | accounts/sharesubscriptions/triggers | いいえ |いいえ |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | servers | はい |はい |
> | servers/advisors | いいえ |いいえ |
> | servers/queryTexts | いいえ |いいえ |
> | servers/recoverableServers | いいえ |いいえ |
> | servers/topQueryStatistics | いいえ |いいえ |
> | servers/virtualNetworkRules | いいえ |いいえ |
> | servers/waitStatistics | いいえ |いいえ |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | servers | はい |はい |
> | servers/advisors | いいえ |いいえ |
> | servers/queryTexts | いいえ |いいえ |
> | servers/recoverableServers | いいえ |いいえ |
> | servers/topQueryStatistics | いいえ |いいえ |
> | servers/virtualNetworkRules | いいえ |いいえ |
> | servers/waitStatistics | いいえ |いいえ |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | serverGroups | はい |はい |
> | servers | はい |はい |
> | servers/advisors | いいえ |いいえ |
> | servers/queryTexts | いいえ |いいえ |
> | servers/recoverableServers | いいえ |いいえ |
> | servers/topQueryStatistics | いいえ |いいえ |
> | servers/virtualNetworkRules | いいえ |いいえ |
> | servers/waitStatistics | いいえ |いいえ |
> | serversv2 | はい |はい |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | artifactSources | はい |はい |
> | rollouts | はい |はい |
> | serviceTopologies | はい |はい |
> | serviceTopologies/services | はい |はい |
> | serviceTopologies/services/serviceUnits | はい |はい |
> | steps | はい |はい |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | applicationgroups | はい |はい |
> | applicationgroups/applications | いいえ |いいえ |
> | applicationgroups/assignedusers | いいえ |いいえ |
> | applicationgroups/startmenuitems | いいえ |いいえ |
> | hostpools | はい |はい |
> | hostpools/sessionhosts | いいえ |いいえ |
> | hostpools/sessionhosts/usersessions | いいえ |いいえ |
> | hostpools/usersessions | いいえ |いいえ |
> | workspaces | はい |はい |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | ElasticPools | はい |はい |
> | ElasticPools/IotHubTenants | はい |はい |
> | IotHubs | はい |はい |
> | IotHubs/eventGridFilters | いいえ |いいえ |
> | ProvisioningServices | はい |はい |
> | usages | いいえ |いいえ |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | pipelines | はい |はい |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | controllers | はい |はい |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | labcenters | はい |はい |
> | labs | はい |はい |
> | labs/environments | はい |はい |
> | labs/serviceRunners | はい |はい |
> | labs/virtualMachines | はい |はい |
> | schedules | はい |はい |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | いいえ |いいえ |
> | databaseAccounts | はい |はい |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | domains | はい |はい |
> | domains/domainOwnershipIdentifiers | いいえ |いいえ |
> | generateSsoRequest | いいえ |いいえ |
> | topLevelDomains | いいえ |いいえ |
> | validateDomainRegistrationInformation | いいえ |いいえ |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | lcsprojects | いいえ |いいえ |
> | lcsprojects/clouddeployments | いいえ |いいえ |
> | lcsprojects/connectors | いいえ |いいえ |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | services | はい |はい |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | domains | はい |はい |
> | domains/topics | いいえ |いいえ |
> | eventSubscriptions | いいえ |いいえ |
> | extensionTopics | いいえ |いいえ |
> | topics | はい |はい |
> | topicTypes | いいえ |いいえ |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | clusters | はい |はい |
> | namespaces | はい |はい |
> | namespaces/authorizationrules | いいえ |いいえ |
> | namespaces/disasterrecoveryconfigs | いいえ |いいえ |
> | namespaces/eventhubs | いいえ |いいえ |
> | namespaces/eventhubs/authorizationrules | いいえ |いいえ |
> | namespaces/eventhubs/consumergroups | いいえ |いいえ |
> | namespaces/networkrulesets | いいえ |いいえ |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | features | いいえ |いいえ |
> | providers | いいえ |いいえ |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | enroll | いいえ |いいえ |
> | galleryitems | いいえ |いいえ |
> | generateartifactaccessuri | いいえ |いいえ |
> | myareas | いいえ |いいえ |
> | myareas/areas | いいえ |いいえ |
> | myareas/areas/areas | いいえ |いいえ |
> | myareas/areas/areas/galleryitems | いいえ |いいえ |
> | myareas/areas/galleryitems | いいえ |いいえ |
> | myareas/galleryitems | いいえ |いいえ |
> | registrations | いいえ |いいえ |
> | resources | いいえ |いいえ |
> | retrieveresourcesbyid | いいえ |いいえ |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | accounts | はい |はい |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | guestConfigurationAssignments | いいえ |いいえ |
> | software | いいえ |いいえ |
> | softwareUpdateProfile | いいえ |いいえ |
> | softwareUpdates | いいえ |いいえ |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | hanaInstances | はい |はい |
> | sapMonitors | はい |はい |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | はい |はい |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | clusters | はい |はい |
> | clusters/applications | いいえ |いいえ |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | services | はい |はい |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | machines | はい |はい |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | dataManagers | はい |はい |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | components | はい |はい |
> | networkScopes | はい |はい |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | jobs | はい |はい |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | いいえ |いいえ |
> | diagnosticSettingsCategories | いいえ |いいえ |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | appTemplates | いいえ |いいえ |
> | IoTApps | はい |はい |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | Graph | はい |はい |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | deletedVaults | いいえ |いいえ |
> | hsmPools | はい |はい |
> | vaults | はい |はい |
> | vaults/accessPolicies | いいえ |いいえ |
> | vaults/eventGridFilters | いいえ |いいえ |
> | vaults/secrets | いいえ |いいえ |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | clusters | はい |はい |
> | clusters/attacheddatabaseconfigurations | いいえ |いいえ |
> | clusters/databases | いいえ |いいえ |
> | clusters/databases/dataconnections | いいえ |いいえ |
> | clusters/databases/eventhubconnections | いいえ |いいえ |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | labaccounts | はい |はい |
> | users | いいえ |いいえ |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | はい |はい |
> | integrationAccounts | はい |はい |
> | integrationServiceEnvironments | はい |はい |
> | isolatedEnvironments | はい |はい |
> | workflows | はい |はい |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | はい |はい |
> | webServices | はい |はい |
> | Workspaces | はい |はい |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | workspaces | はい |はい |
> | workspaces/computes | いいえ |いいえ |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | Identities | いいえ |いいえ |
> | userAssignedIdentities | はい |はい |

## <a name="microsoftmanagedlab"></a>Microsoft.ManagedLab

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | labaccounts | はい |はい |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | いいえ |いいえ |
> | registrationAssignments | いいえ |いいえ |
> | registrationDefinitions | いいえ |いいえ |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | getEntities | いいえ |いいえ |
> | managementGroups | いいえ |いいえ |
> | resources | いいえ |いいえ |
> | startTenantBackfill | いいえ |いいえ |
> | tenantBackfillStatus | いいえ |いいえ |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | accounts | はい |はい |
> | accounts/eventGridFilters | いいえ |いいえ |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | offers | いいえ |いいえ |
> | offerTypes | いいえ |いいえ |
> | offerTypes/publishers | いいえ |いいえ |
> | offerTypes/publishers/offers | いいえ |いいえ |
> | offerTypes/publishers/offers/plans | いいえ |いいえ |
> | offerTypes/publishers/offers/plans/agreements | いいえ |いいえ |
> | offerTypes/publishers/offers/plans/configs | いいえ |いいえ |
> | offerTypes/publishers/offers/plans/configs/importImage | いいえ |いいえ |
> | privategalleryitems | いいえ |いいえ |
> | products | いいえ |いいえ |
> | publishers | いいえ |いいえ |
> | publishers/offers | いいえ |いいえ |
> | publishers/offers/amendments | いいえ |いいえ |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | classicDevServices | はい |はい |
> | updateCommunicationPreference | いいえ |いいえ |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | agreements | いいえ |いいえ |
> | offertypes | いいえ |いいえ |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | mediaservices | はい |はい |
> | mediaservices/accountFilters | いいえ |いいえ |
> | mediaservices/assets | いいえ |いいえ |
> | mediaservices/assets/assetFilters | いいえ |いいえ |
> | mediaservices/contentKeyPolicies | いいえ |いいえ |
> | mediaservices/eventGridFilters | いいえ |いいえ |
> | mediaservices/liveEventOperations | いいえ |いいえ |
> | mediaservices/liveEvents | はい |はい |
> | mediaservices/liveEvents/liveOutputs | いいえ |いいえ |
> | mediaservices/liveOutputOperations | いいえ |いいえ |
> | mediaservices/streamingEndpointOperations | いいえ |いいえ |
> | mediaservices/streamingEndpoints | はい |はい |
> | mediaservices/streamingLocators | いいえ |いいえ |
> | mediaservices/streamingPolicies | いいえ |いいえ |
> | mediaservices/transforms | いいえ |いいえ |
> | mediaservices/transforms/jobs | いいえ |いいえ |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | appClusters | はい |はい |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | はい |はい |
> | migrateprojects | はい |はい |
> | projects | はい |はい |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | remoteRenderingAccounts | はい |はい |
> | spatialAnchorsAccounts | はい |はい |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | はい |はい |
> | netAppAccounts/capacityPools | はい |はい |
> | netAppAccounts/capacityPools/volumes | はい |はい |
> | netAppAccounts/capacityPools/volumes/mountTargets | はい |はい |
> | netAppAccounts/capacityPools/volumes/snapshots | はい |はい |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | applicationGateways | はい |はい |
> | applicationGatewayWebApplicationFirewallPolicies | はい |はい |
> | applicationSecurityGroups | はい |はい |
> | azureFirewallFqdnTags | いいえ |いいえ |
> | azureFirewalls | はい |はい |
> | bastionHosts | はい |はい |
> | bgpServiceCommunities | いいえ |いいえ |
> | connections | はい |はい |
> | ddosCustomPolicies | はい |はい |
> | ddosProtectionPlans | はい |はい |
> | dnsOperationStatuses | いいえ |いいえ |
> | dnszones | はい |はい |
> | dnszones/A | いいえ |いいえ |
> | dnszones/AAAA | いいえ |いいえ |
> | dnszones/all | いいえ |いいえ |
> | dnszones/CAA | いいえ |いいえ |
> | dnszones/CNAME | いいえ |いいえ |
> | dnszones/MX | いいえ |いいえ |
> | dnszones/NS | いいえ |いいえ |
> | dnszones/PTR | いいえ |いいえ |
> | dnszones/recordsets | いいえ |いいえ |
> | dnszones/SOA | いいえ |いいえ |
> | dnszones/SRV | いいえ |いいえ |
> | dnszones/TXT | いいえ |いいえ |
> | expressRouteCircuits | はい |はい |
> | expressRouteCrossConnections | はい |はい |
> | expressRouteGateways | はい |はい |
> | expressRoutePorts | はい |はい |
> | expressRouteServiceProviders | いいえ |いいえ |
> | firewallPolicies | はい |はい |
> | frontdoors | はい。ただし、制限あり ([下記の注](#frontdoor)を参照) |はい |
> | frontdoorWebApplicationFirewallManagedRuleSets | はい。ただし、制限あり ([下記の注](#frontdoor)を参照) |いいえ |
> | frontdoorWebApplicationFirewallPolicies | はい。ただし、制限あり ([下記の注](#frontdoor)を参照) |はい |
> | getDnsResourceReference | いいえ |いいえ |
> | internalNotify | いいえ |いいえ |
> | loadBalancers | はい |いいえ |
> | localNetworkGateways | はい |はい |
> | natGateways | はい |はい |
> | networkIntentPolicies | はい |はい |
> | networkInterfaces | はい |はい |
> | networkProfiles | はい |はい |
> | networkSecurityGroups | はい |はい |
> | networkWatchers | はい |いいえ |
> | networkWatchers/connectionMonitors | はい |いいえ |
> | networkWatchers/lenses | はい |いいえ |
> | networkWatchers/pingMeshes | はい |いいえ |
> | p2sVpnGateways | はい |はい |
> | privateDnsOperationStatuses | いいえ |いいえ |
> | privateDnsZones | はい |はい |
> | privateDnsZones/A | いいえ |いいえ |
> | privateDnsZones/AAAA | いいえ |いいえ |
> | privateDnsZones/all | いいえ |いいえ |
> | privateDnsZones/CNAME | いいえ |いいえ |
> | privateDnsZones/MX | いいえ |いいえ |
> | privateDnsZones/PTR | いいえ |いいえ |
> | privateDnsZones/SOA | いいえ |いいえ |
> | privateDnsZones/SRV | いいえ |いいえ |
> | privateDnsZones/TXT | いいえ |いいえ |
> | privateDnsZones/virtualNetworkLinks | はい |はい |
> | privateEndpoints | はい |はい |
> | privateLinkServices | はい |はい |
> | publicIPAddresses | はい |はい |
> | publicIPPrefixes | はい |はい |
> | routeFilters | はい |はい |
> | routeTables | はい |はい |
> | secureGateways | はい |はい |
> | serviceEndpointPolicies | はい |はい |
> | trafficManagerGeographicHierarchies | いいえ |いいえ |
> | trafficmanagerprofiles | はい |はい |
> | trafficmanagerprofiles/heatMaps | いいえ |いいえ |
> | trafficManagerUserMetricsKeys | いいえ |いいえ |
> | virtualHubs | はい |はい |
> | virtualNetworkGateways | はい |はい |
> | virtualNetworks | はい |はい |
> | virtualNetworkTaps | はい |はい |
> | virtualWans | はい |はい |
> | vpnGateways | はい |いいえ |
> | vpnSites | はい |はい |
> | webApplicationFirewallPolicies | はい |はい |

<a id="frontdoor" />

> [!NOTE]
> Azure Front Door Service の場合は、リソースの作成時にタグを適用できますが、タグの更新や追加は現在サポートされていません。

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | namespaces | はい |いいえ |
> | namespaces/notificationHubs | はい |いいえ |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | HyperVSites | はい |はい |
> | ImportSites | はい |はい |
> | ServerSites | はい |はい |
> | VMwareSites | はい |はい |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | devices | いいえ |いいえ |
> | linkTargets | いいえ |いいえ |
> | storageInsightConfigs | いいえ |いいえ |
> | workspaces | はい |はい |
> | workspaces/dataSources | いいえ |いいえ |
> | workspaces/linkedServices | いいえ |いいえ |
> | workspaces/query | いいえ |いいえ |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | managementassociations | いいえ |いいえ |
> | managementconfigurations | はい |はい |
> | solutions | はい |はい |
> | views | はい |はい |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | いいえ |いいえ |
> | peerAsns | いいえ |いいえ |
> | peerings | はい |はい |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | policyEvents | いいえ |いいえ |
> | policyStates | いいえ |いいえ |
> | policyTrackedResources | いいえ |いいえ |
> | remediations | いいえ |いいえ |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | consoles | いいえ |いいえ |
> | dashboards | はい |はい |
> | userSettings | いいえ |いいえ |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | はい |はい |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | capacities | はい |はい |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | いいえ |いいえ |
> | vaults | はい |はい |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | namespaces | はい |はい |
> | namespaces/authorizationrules | いいえ |いいえ |
> | namespaces/hybridconnections | いいえ |いいえ |
> | namespaces/hybridconnections/authorizationrules | いいえ |いいえ |
> | namespaces/wcfrelays | いいえ |いいえ |
> | namespaces/wcfrelays/authorizationrules | いいえ |いいえ |

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | accounts | いいえ |いいえ |
> | collections | はい |はい |
> | collections/applications | いいえ |いいえ |
> | collections/securityprincipals | いいえ |いいえ |
> | templateImages | いいえ |いいえ |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | Query | はい |はい |
> | resourceChangeDetails | いいえ |いいえ |
> | resourceChanges | いいえ |いいえ |
> | resources | いいえ |いいえ |
> | resourcesHistory | いいえ |いいえ |
> | subscriptionsStatus | いいえ |いいえ |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | いいえ |いいえ |
> | childAvailabilityStatuses | いいえ |いいえ |
> | childResources | いいえ |いいえ |
> | events | いいえ |いいえ |
> | impactedResources | いいえ |いいえ |
> | metadata | いいえ |いいえ |
> | notifications | いいえ |いいえ |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | deployments | いいえ |いいえ |
> | deployments/operations | いいえ |いいえ |
> | links | いいえ |いいえ |
> | notifyResourceJobs | いいえ |いいえ |
> | providers | いいえ |いいえ |
> | resourceGroups | いいえ |いいえ |
> | resources | いいえ |いいえ |
> | subscriptions | いいえ |いいえ |
> | subscriptions/providers | いいえ |いいえ |
> | subscriptions/resourceGroups | いいえ |いいえ |
> | subscriptions/resourcegroups/resources | いいえ |いいえ |
> | subscriptions/resources | いいえ |いいえ |
> | subscriptions/tagnames | いいえ |いいえ |
> | subscriptions/tagNames/tagValues | いいえ |いいえ |
> | tags | いいえ |いいえ |
> | tenants | いいえ |いいえ |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | applications | はい |はい |
> | saasresources | いいえ |いいえ |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | flows | はい |はい |
> | jobcollections | はい |はい |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | いいえ |いいえ |
> | searchServices | はい |はい |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | いいえ |いいえ |
> | advancedThreatProtectionSettings | いいえ |いいえ |
> | alerts | いいえ |いいえ |
> | allowedConnections | いいえ |いいえ |
> | applicationWhitelistings | いいえ |いいえ |
> | assessmentMetadata | いいえ |いいえ |
> | assessments | いいえ |いいえ |
> | AutoProvisioningSettings | いいえ |いいえ |
> | Compliances | いいえ |いいえ |
> | dataCollectionAgents | いいえ |いいえ |
> | deviceSecurityGroups | いいえ |いいえ |
> | discoveredSecuritySolutions | いいえ |いいえ |
> | externalSecuritySolutions | いいえ |いいえ |
> | InformationProtectionPolicies | いいえ |いいえ |
> | iotSecuritySolutions | はい |はい |
> | iotSecuritySolutions/analyticsModels | いいえ |いいえ |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | いいえ |いいえ |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | いいえ |いいえ |
> | jitNetworkAccessPolicies | いいえ |いいえ |
> | playbookConfigurations | はい |はい |
> | policies | いいえ |いいえ |
> | pricings | いいえ |いいえ |
> | regulatoryComplianceStandards | いいえ |いいえ |
> | regulatoryComplianceStandards/regulatoryComplianceControls | いいえ |いいえ |
> | regulatoryComplianceStandards/regulatoryComplianceControls/regulatoryComplianceAssessments | いいえ |いいえ |
> | securityContacts | いいえ |いいえ |
> | securitySolutions | いいえ |いいえ |
> | securitySolutionsReferenceData | いいえ |いいえ |
> | securityStatuses | いいえ |いいえ |
> | securityStatusesSummaries | いいえ |いいえ |
> | serverVulnerabilityAssessments | いいえ |いいえ |
> | settings | いいえ |いいえ |
> | tasks | いいえ |いいえ |
> | topologies | いいえ |いいえ |
> | workspaceSettings | いいえ |いいえ |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | いいえ |いいえ |
> | diagnosticSettingsCategories | いいえ |いいえ |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | aggregations | いいえ |いいえ |
> | alertRules | いいえ |いいえ |
> | bookmarks | いいえ |いいえ |
> | cases | いいえ |いいえ |
> | dataConnectors | いいえ |いいえ |
> | entities | いいえ |いいえ |
> | entityQueries | いいえ |いいえ |
> | officeConsents | いいえ |いいえ |
> | settings | いいえ |いいえ |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | namespaces | はい |いいえ |
> | namespaces/authorizationrules | いいえ |いいえ |
> | namespaces/disasterrecoveryconfigs | いいえ |いいえ |
> | namespaces/eventgridfilters | いいえ |いいえ |
> | namespaces/networkrulesets | いいえ |いいえ |
> | namespaces/queues | いいえ |いいえ |
> | namespaces/queues/authorizationrules | いいえ |いいえ |
> | namespaces/topics | いいえ |いいえ |
> | namespaces/topics/authorizationrules | いいえ |いいえ |
> | namespaces/topics/subscriptions | いいえ |いいえ |
> | namespaces/topics/subscriptions/rules | いいえ |いいえ |
> | premiumMessagingRegions | いいえ |いいえ |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | applications | はい |はい |
> | clusters | はい |はい |
> | clusters/applications | いいえ |いいえ |
> | containerGroups | はい |はい |
> | containerGroupSets | はい |はい |
> | edgeclusters | はい |はい |
> | edgeclusters/applications | いいえ |いいえ |
> | networks | はい |はい |
> | secretstores | はい |はい |
> | secretstores/certificates | いいえ |いいえ |
> | secretstores/secrets | いいえ |いいえ |
> | volumes | はい |はい |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | applications | はい |はい |
> | containerGroups | はい |はい |
> | gateways | はい |はい |
> | networks | はい |はい |
> | secrets | はい |はい |
> | volumes | はい |はい |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | いいえ |いいえ |
> | providerRegistrations/resourceTypeRegistrations | いいえ |いいえ |
> | rollouts | はい |はい |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | SignalR | はい |はい |
> | SignalR/eventGridFilters | いいえ |いいえ |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | はい |はい |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | いいえ |いいえ |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | はい |はい |
> | applications | はい |はい |
> | jitRequests | はい |はい |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | managedInstances | はい | はい |
> | managedInstances/databases | はい ([下記の「注」](#sqlnote)を参照) | はい |
> | managedInstances/databases/backupShortTermRetentionPolicies | いいえ | いいえ |
> | managedInstances/databases/schemas/tables/columns/sensitivityLabels | いいえ | いいえ |
> | managedInstances/databases/vulnerabilityAssessments | いいえ | いいえ |
> | managedInstances/databases/vulnerabilityAssessments/rules/baselines | いいえ | いいえ |
> | managedInstances/encryptionProtector | いいえ | いいえ |
> | managedInstances/keys | いいえ | いいえ |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | いいえ | いいえ |
> | managedInstances/vulnerabilityAssessments | いいえ | いいえ |
> | servers | はい | はい |
> | servers/administrators | いいえ | いいえ |
> | servers/communicationLinks | いいえ | いいえ |
> | servers/databases | はい ([下記の「注」](#sqlnote)を参照) | はい |
> | servers/encryptionProtector | いいえ | いいえ |
> | servers/firewallRules | いいえ | いいえ |
> | servers/keys | いいえ | いいえ |
> | servers/restorableDroppedDatabases | いいえ | いいえ |
> | servers/serviceobjectives | いいえ | いいえ |
> | servers/tdeCertificates | いいえ | いいえ |

<a id="sqlnote" />

> [!NOTE]
> マスター データベースではタグはサポートされませんが、Azure SQL Data Warehouse データベースを含むその他のデータベースではタグがサポートされます。 Azure SQL Data Warehouse データベースは (一時停止ではなく) アクティブ状態である必要があります。

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | はい |はい |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | いいえ |いいえ |
> | SqlVirtualMachines | はい |はい |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | storageAccounts | はい |はい |
> | storageAccounts/blobServices | いいえ |いいえ |
> | storageAccounts/fileServices | いいえ |いいえ |
> | storageAccounts/queueServices | いいえ |いいえ |
> | storageAccounts/services | いいえ |いいえ |
> | storageAccounts/services/metricDefinitions | いいえ |いいえ |
> | storageAccounts/tableServices | いいえ |いいえ |
> | usages | いいえ |いいえ |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | caches | はい |はい |
> | caches/storageTargets | いいえ |いいえ |
> | usageModels | いいえ |いいえ |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | replicationGroups | いいえ |いいえ |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | はい |はい |
> | storageSyncServices/registeredServers | いいえ |いいえ |
> | storageSyncServices/syncGroups | いいえ |いいえ |
> | storageSyncServices/syncGroups/cloudEndpoints | いいえ |いいえ |
> | storageSyncServices/syncGroups/serverEndpoints | いいえ |いいえ |
> | storageSyncServices/workflows | いいえ |いいえ |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | はい |はい |
> | storageSyncServices/registeredServers | いいえ |いいえ |
> | storageSyncServices/syncGroups | いいえ |いいえ |
> | storageSyncServices/syncGroups/cloudEndpoints | いいえ |いいえ |
> | storageSyncServices/syncGroups/serverEndpoints | いいえ |いいえ |
> | storageSyncServices/workflows | いいえ |いいえ |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | はい |はい |
> | storageSyncServices/registeredServers | いいえ |いいえ |
> | storageSyncServices/syncGroups | いいえ |いいえ |
> | storageSyncServices/syncGroups/cloudEndpoints | いいえ |いいえ |
> | storageSyncServices/syncGroups/serverEndpoints | いいえ |いいえ |
> | storageSyncServices/workflows | いいえ |いいえ |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | managers | はい |はい |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | streamingjobs | はい (下記の「注」を参照) |はい |

> [!NOTE]
> streamingjobs が実行されている場合、タグを追加することはできません。 タグを追加するにはリソースを停止します。

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | cancel | いいえ |いいえ |
> | CreateSubscription | いいえ |いいえ |
> | rename | いいえ |いいえ |
> | SubscriptionDefinitions | いいえ |いいえ |
> | SubscriptionOperations | いいえ |いいえ |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | 環境 | はい |いいえ |
> | environments/accessPolicies | いいえ |いいえ |
> | environments/eventsource | はい |いいえ |
> | environments/referenceDataSets | はい |いいえ |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | はい |はい |
> | dedicatedCloudServices | はい |はい |
> | virtualMachines | はい |はい |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | いいえ |いいえ |
> | apiManagementAccounts/apiAcls | いいえ |いいえ |
> | apiManagementAccounts/apis | いいえ |いいえ |
> | apiManagementAccounts/apis/apiAcls | いいえ |いいえ |
> | apiManagementAccounts/apis/connectionAcls | いいえ |いいえ |
> | apiManagementAccounts/apis/connections | いいえ |いいえ |
> | apiManagementAccounts/apis/connections/connectionAcls | いいえ |いいえ |
> | apiManagementAccounts/apis/localizedDefinitions | いいえ |いいえ |
> | apiManagementAccounts/connectionAcls | いいえ |いいえ |
> | apiManagementAccounts/connections | いいえ |いいえ |
> | billingMeters | いいえ |いいえ |
> | certificates | はい |はい |
> | connectionGateways | はい |はい |
> | connections | はい |はい |
> | customApis | はい |はい |
> | deletedSites | いいえ |いいえ |
> | functions | いいえ |いいえ |
> | hostingEnvironments | はい |はい |
> | hostingEnvironments/multiRolePools | いいえ |いいえ |
> | hostingEnvironments/workerPools | いいえ |いいえ |
> | publishingUsers | いいえ |いいえ |
> | recommendations | いいえ |いいえ |
> | resourceHealthMetadata | いいえ |いいえ |
> | runtimes | いいえ |いいえ |
> | serverFarms | はい |はい |
> | serverFarms/eventGridFilters | いいえ |いいえ |
> | sites | はい |はい |
> | sites/config  | いいえ | いいえ |
> | sites/eventGridFilters | いいえ |いいえ |
> | sites/hostNameBindings | いいえ |いいえ |
> | sites/networkConfig | いいえ |いいえ |
> | sites/premieraddons | はい |はい |
> | sites/slots | はい |はい |
> | sites/slots/eventGridFilters | いいえ |いいえ |
> | sites/slots/hostNameBindings | いいえ |いいえ |
> | sites/slots/networkConfig | いいえ |いいえ |
> | sourceControls | いいえ |いいえ |
> | validate | いいえ |いいえ |
> | verifyHostingEnvironmentVnet | いいえ |いいえ |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | いいえ |いいえ |
> | diagnosticSettingsCategories | いいえ |いいえ |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | DeviceServices | はい |はい |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | リソースの種類 | タグのサポート | コスト レポートのタグ |
> | ------------- | ----------- | ----------- |
> | components | いいえ |いいえ |
> | componentsSummary | いいえ |いいえ |
> | monitorInstances | いいえ |いいえ |
> | monitorInstancesSummary | いいえ |いいえ |
> | monitors | いいえ |いいえ |
> | notificationSettings | いいえ |いいえ |

## <a name="next-steps"></a>次の手順

リソースにタグを適用する方法については、「[タグを使用した Azure リソースの整理](resource-group-using-tags.md)」をご覧ください。
