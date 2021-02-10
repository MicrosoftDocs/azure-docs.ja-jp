---
title: リージョン間の Azure リソースの移動のサポート
description: Azure リージョン間で移動できる Azure リソースの種類を一覧表示する
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/25/2020
ms.author: raynew
ms.openlocfilehash: 49c5828e02bf96a536ff14f6b84e81f7adbe3090
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/26/2021
ms.locfileid: "98806902"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>リージョン間の Azure リソースの移動のサポート

この記事では、Azure リソースの種類が別の Azure リージョンへの移動をサポートされているかどうかを確認します。 

リソース プロバイダーの名前空間に移動します。
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
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
> - [Microsoft.ClassicSubscription](#microsoftclassicsubscription)
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
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataProtection](#microsoftdataprotection)
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
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.HybridNetwork](#microsofthybridnetwork)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedNetwork](#microsoftmanagednetwork)
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
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [microsoft.support](#microsoftsupport)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | domainservices | いいえ | 


## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | diagnosticsettings | いいえ |
> | diagnosticsettingscategories | いいえ |
> | privatelinkforazuread | いいえ |
> | tenants |  いいえ |

## <a name="microsoftaddons"></a>microsoft.Addons

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | supportproviders | いいえ |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
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
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | configuration | いいえ | 
> | generaterecommendations | いいえ |
> | metadata | いいえ |
> | recommendations | いいえ |
> | suppressions | いいえ | 

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | actionrules | いいえ | 
> | alerts | いいえ | 
> | alertslist | いいえ | 
> | alertsmetadata | いいえ | 
> | alertssummary | いいえ | 
> | alertssummarylist | いいえ | 
> | smartdetectoralertrules | いいえ | 
> | smartgroups | いいえ | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | servers | いいえ |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | reportfeedback | いいえ |
> | サービス (service) |  はい (テンプレートを使用) <br/><br/> [Azure API Management をリージョン間で移行する](../../api-management/api-management-howto-migrate.md)。 | 

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | configurationstores | いいえ | 
> | configurationstores / eventgridfilters | いいえ |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | spring | いいえ | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | apiapps | はい (テンプレートを使用)<br/><br/> [App Service アプリを別のリージョンに移動する](../../app-service/manage-move-across-regions.md) | 
> | appidentities | いいえ | 
> | gateways | いいえ | 

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | attestationproviders | いいえ | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | classicadministrators | いいえ | 
> | dataaliases | いいえ | 
> | denyassignments | いいえ | 
> | elevateaccess | いいえ | 
> | findorphanroleassignments | いいえ | 
> | locks | いいえ | 
> | 権限 | いいえ | 
> | policyassignments | いいえ | 
> | policydefinitions | いいえ | 
> | policysetdefinitions | いいえ | 
> | privatelinkassociations | いいえ | 
> | resourcemanagementprivatelinks | いいえ | 
> | roleassignments | いいえ | 
> | roleassignmentsusagemetrics | いいえ | 
> | roledefinitions | いいえ | 

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | automationaccounts | はい (テンプレートを使用) <br/><br/> [geo レプリケーションを使用する](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |  
> | automationaccounts/configurations | いいえ | 
> | automationaccounts/runbooks | いいえ | 

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | サブスクリプション |
> | ------------- | ----------- | 
> | privateclouds | いいえ | 


## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | b2cdirectories | いいえ | 
> | b2ctenants | いいえ | 

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | datacontrollers | いいえ | 
> | hybriddatamanagers | いいえ | 
> | postgresinstances | いいえ | 
> | sqlinstances | いいえ | 
> | sqlmanagedinstances | いいえ |
> | sqlserverinstances | いいえ | 
> | sqlserverregistrations | いいえ |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | cloudmanifestfiles | いいえ |
> | registrations | いいえ | 

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | clusters | いいえ | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | batchaccounts |  Batch アカウントを別のリージョンに直接移行することはできませんが、テンプレートを使用し、テンプレートをエクスポートしてそれを変更し、テンプレートを新しいリージョンにデプロイすることはできます。 <br/><br/> [リージョン間で Batch アカウントを移行する](../../batch/best-practices.md#moving-batch-accounts-across-regions)方法を参照してください。 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | billingaccounts | いいえ | 
> | billingperiods | いいえ | 
> | billingpermissions | いいえ | 
> | billingproperty | いいえ | 
> | billingroleassignments | いいえ | 
> | billingroledefinitions | いいえ | 
> | departments | いいえ | 
> | enrollmentaccounts | いいえ | 
> | invoices | いいえ | 
> | transfers | いいえ | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | mapapis | いいえ | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | biztalk | いいえ | 

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | blockchainmembers | いいえ <br/><br/> ブロックチェーン ネットワークでは、異なるリージョンにノードを持つことはできません。 
> | cordamembers | いいえ |
> | watchers | いいえ | 

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | tokenservices | いいえ |


## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | blueprintassignments | いいえ | 
> | blueprints | いいえ |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | botservices | いいえ | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | redis | いいえ | 
> | redisenterprise | いいえ | 

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | appliedreservations | いいえ | 
> | calculateexchange | いいえ | 
> | calculateprice | いいえ | 
> | calculatepurchaseprice | いいえ | 
> | catalogs | いいえ | 
> | commercialreservationorders | いいえ | 
> | exchange | いいえ |
> | reservationorders | いいえ | 
> | reservations | いいえ | 
> | resources | いいえ | 
> | validatereservationorder | いいえ | 

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | いいえ |
> | edgenodes | いいえ
> | profiles | いいえ | 
> | profiles/endpoints | いいえ | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | certificateorders | いいえ | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | capabilities | いいえ | 
> | domainnames | はい | いいえ |
> | quotas | いいえ | 
> | resourcetypes | いいえ |
> | validatesubscriptionmoveavailability | いいえ | 
> | virtualmachines | いいえ 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | classicinfrastructureresources | いいえ | 

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | capabilities | いいえ | 
> | expressroutecrossconnections | いいえ | 
> | expressroutecrossconnections / peerings | いいえ | 
> | gatewaysupporteddevices | いいえ | 
> | networksecuritygroups | いいえ |
> | quotas | いいえ |
> | reservedips | いいえ | 
> | virtualnetworks | いいえ | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | disks | いいえ | 
> | images | いいえ | 
> | osimages | いいえ | 
> | osplatformimages | いいえ | 
> | publicimages | いいえ | 
> | quotas | いいえ | 
> | storageaccounts | はい |  
> | vmimages | × |

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | operations | いいえ | 

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | accounts | いいえ | 
> | Cognitive Search | 手動の手順でサポートされています。<br/><br/> [Azure Cognitive Search サービスを別のリージョンに移行する](../../search/search-howto-move-across-regions.md)方法を参照してください。

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | ratecard | いいえ | 
> | usageaggregates | いいえ | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | availabilitysets | はい <br/><br/> 可用性セットを移動するには [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) を使用します。 | 
> | diskaccesses | いいえ |
> | diskencryptionsets | いいえ | 
> | disks | はい <br/><br/> Azure VM および関連ディスクを移動するには [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) を使用します。 | 
> | galleries | いいえ | 
> | galleries/images | いいえ | 
> | galleries/images/versions | いいえ | 
> | hostgroups | いいえ | 
> | hostgroups/hosts | いいえ | 
> | images | いいえ | 
> | proximityplacementgroups | いいえ | 
> | restorepointcollections | いいえ | 
> | sharedvmimages | いいえ | 
> | sharedvmimages/versions | いいえ | 
> | スナップショット | いいえ | 
> | sshpublickeys | いいえ |
> | virtualmachines | はい <br/><br/> Azure VM を移動するには [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) を使用します。 | 
> | virtualmachines/extensions | いいえ | 
> | virtualmachinescalesets | いいえ | 

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | aggregatedcost | いいえ | 
> | balances | いいえ | 
> | budgets | いいえ | 
> | charges | いいえ | 
> | costtags | いいえ | 
> | credits | いいえ | 
> | events | いいえ | 
> | forecasts | いいえ | 
> | lots | いいえ | 
> | marketplaces | いいえ | 
> | pricesheets | いいえ | 
> | products | いいえ | 
> | reservationdetails | いいえ | 
> | reservationrecommendationdetails | いいえ | 
> | reservationrecommendations | いいえ | 
> | reservationsummaries | いいえ | 
> | reservationtransactions | いいえ | 
> | tags | いいえ | 
> | tenants | いいえ | 
> | terms | いいえ | 
> | usagedetails | いいえ | 


## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | containergroups | いいえ | 
> | serviceassociationlinks | いいえ |


## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | registries | いいえ |  
> | registries / agentpools | いいえ | 
> | registries/buildtasks | いいえ |  
> | registries/replications | いいえ | 
> | registries/tasks | いいえ |  
> | registries/webhooks | いいえ | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | containerservices | いいえ |
> | managedclusters | いいえ | 
> | openshiftmanagedclusters | いいえ | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | applications | いいえ | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | accounts | いいえ | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | alerts | いいえ | 
> | billingaccounts | いいえ | 
> | budgets | いいえ | 
> | cloudconnectors | いいえ | 
> | connectors | いいえ | 
> | departments | いいえ | 
> | dimensions | いいえ | 
> | enrollmentaccounts | いいえ | 
> | exports | いいえ | 
> | externalbillingaccounts | いいえ | 
> | forecast | いいえ | 
> | query | いいえ | 
> | registrations | いいえ | 
> | reportconfigs | いいえ | 
> | reports | いいえ | 
> | settings | いいえ | 
> | showbackrules | いいえ | 
> | views | いいえ | 

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | hubs | いいえ |  

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | requests | いいえ | 

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | associations | いいえ |
> | resourceproviders | いいえ | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | jobs | いいえ | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | availableskus | いいえ |
> | databoxedgedevices | いいえ | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | workspaces | いいえ | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | catalogs | いいえ | 
> | datacatalogs | いいえ | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | connectionmanagers | いいえ | 

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | packages | いいえ | 
> | plans | いいえ | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | datafactories | いいえ | 
> | factories | いいえ |  

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | datalakeaccounts | いいえ | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | accounts | いいえ | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | accounts | いいえ | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | services | いいえ | 
> | services/projects | いいえ | 
> | slots | いいえ | 

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | ---------- |
> | backupvaults | いいえ | 

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | accounts | いいえ | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | servers | リージョン間の読み取りレプリカを使用して、既存のサーバーを移動できます。 [詳細については、こちらを参照してください](../../postgresql/howto-move-regions-portal.md)。<br/><br/> サービスが geo 冗長バックアップ ストレージを使用してプロビジョニングされている場合、geo リストアを使用して他のリージョンで復元できます。 [詳細については、こちらを参照してください](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage)。

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | servers | リージョン間の読み取りレプリカを使用して、既存のサーバーを移動できます。 [詳細については、こちらを参照してください](../../mysql/howto-move-regions-portal.md)。

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | servergroups | いいえ | 
> | servers | リージョン間の読み取りレプリカを使用して、既存のサーバーを移動できます。 [詳細については、こちらを参照してください](../../postgresql/howto-move-regions-portal.md)。
> | serversv2 | いいえ | 

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | artifactsources | いいえ | 
> | rollouts | いいえ |  
> | servicetopologies | いいえ | 
> | servicetopologies/services | いいえ |  
> | servicetopologies/services/serviceunits | いいえ | 
> | steps | いいえ | 


## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | applicationgroups | いいえ | 
> | workspaces | いいえ | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | elasticpools | いいえ。 リソースは公開されていません。
> | elasticpools/iothubtenants | いいえ。 リソースは公開されていません。
> | iothubs | はい。 [詳細情報](../../iot-hub/iot-hub-how-to-clone.md)
> | provisioningservices | いいえ | 

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | controllers | いいえ | 


## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | controllers | いいえ | 
> | AKS クラスター | いいえ<br/><br/> 別リージョンへの移行の[詳細をご覧ください](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region)。

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | labcenters | いいえ | 
> | labs | いいえ | 
> | labs/environments | いいえ |  
> | labs/servicerunners | いいえ | 
> | labs/virtualmachines | いいえ |  
> | schedules | いいえ |  

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | digitaltwinsinstances | はい。新しいリージョン内にリソースを再作成します。 [詳細情報](../../digital-twins/how-to-move-regions.md) |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | databaseaccounts | いいえ | 
> | databaseaccounts | いいえ | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | domains | いいえ | 
> | generatessorequest | いいえ | 
> | topleveldomains | いいえ | 
> | validatedomainregistrationinformation | いいえ |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | services | いいえ |  

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | domains | いいえ | 
> | eventsubscriptions | いいえ |
> | extensiontopics | いいえ | 
> | partnernamespaces | いいえ | 
> | partnerregistrations | いいえ | 
> | partnertopics | いいえ | 
> | systemtopics | いいえ | 
> | topics | いいえ | 
> | topictypes | いいえ | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | clusters | いいえ |  
> | namespaces | はい (テンプレートを使用)<br/><br/> [Event Hub 名前空間を別のリージョンに移動する](../../event-hubs/move-across-regions.md) | 
> | sku | いいえ |  

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | experimentworkspaces | いいえ | 

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | namespaces | いいえ | 

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | featureproviders | いいえ | 
> | features | いいえ | 
> | providers | いいえ | 
> | subscriptionfeatureregistrations | いいえ | 

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | accounts | いいえ | 

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | automanagedaccounts | いいえ | 
> | automanagedvmconfigurationprofiles | いいえ | 
> | guestconfigurationassignments | いいえ | 
> | software | いいえ | 
> | softwareupdateprofile | いいえ | 
> | softwareupdates | いいえ | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | hanainstances | いいえ | 
> | sapmonitors | いいえ |  

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | dedicatedhsms | いいえ | 


## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | clusters | いいえ | 

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | services | いいえ |  

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | machines | いいえ | 
> | machines / extensions | いいえ |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | datamanagers |  いいえ | 

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | devices | いいえ | 
> | vnfs | いいえ | 

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | components | いいえ | 
> | networkscopes | いいえ | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | jobs |  いいえ | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | accounts | いいえ。 [詳細については、こちらを参照してください](../../azure-monitor/faq.md#how-do-i-move-an-application-insights-resource-to-a-new-region)。
> | actiongroups |  いいえ | 
> | activitylogalerts | いいえ | 
> | alertrules |  いいえ | 
> | autoscalesettings |  いいえ | 
> | baseline | いいえ |
> | components |  いいえ |  
> | datacollectionrules | いいえ | 
> | diagnosticsettings | いいえ | 
> | diagnosticsettingscategories | いいえ | 
> | eventcategories | いいえ | 
> | eventtypes | いいえ | 
> | extendeddiagnosticsettings | いいえ | |
> | guestdiagnosticsettings | いいえ | 
> | listmigrationdate | いいえ | 
> | logdefinitions | いいえ | 
> | logprofiles | いいえ | 
> | logs | いいえ | いいえ |
> | metricalerts | いいえ | 
> | metricbaselines | いいえ | 
> | metricbatch | いいえ | 
> | metricdefinitions | いいえ | 
> | metricnamespaces | いいえ | 
> | metrics | いいえ | 
> | migratealertrules | いいえ |
> | migratetonewpricingmodel | いいえ | 
> | myworkbooks | いいえ |
> | notificationgroups | いいえ | 
> | privatelinkscopes | いいえ |
> | rollbacktolegacypricingmodel | いいえ |
> | scheduledqueryrules |  いいえ | 
> | トポロジ | いいえ |
> | トランザクション | いいえ |
> | vminsightsonboardingstatuses | いいえ |
> | webtests |  いいえ | 
> | webtests / gettestresultfile | いいえ |
> | Workbooks |  いいえ |  
> | workbooktemplates | いいえ |


## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | apptemplates | いいえ | 
> | iotapps | いいえ | 



## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> |  iothub |  はい (ハブを複製) <br/><br/> [IoT ハブを別のリージョンに複製する](../../iot-hub/iot-hub-how-to-clone.md)

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 |
> | ------------- | ----------- | 
> | graph | いいえ | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | deletedvaults | いいえ |
> | hsmpools | いいえ | 
> | managedhsms | いいえ |
> | vaults |  いいえ | 

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | connectedclusters | いいえ | 
> | registeredsubscriptions | いいえ | 

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | sourcecontrolconfigurations | いいえ | 

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | clusters |  いいえ |  

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | labaccounts | いいえ | 
> | users | いいえ | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | accounts | いいえ | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | accounts | いいえ。グローバル サービスです。

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | hostingenvironments | いいえ | 
> | integrationaccounts |  いいえ |  
> | integrationserviceenvironments | いいえ | 
> | integrationserviceenvironments / managedapis | いいえ |
> | isolatedenvironments | いいえ | 
> | workflows |  いいえ |  

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | commitmentplans |  いいえ | 
> | webservices |  いいえ | 
> | workspaces |  いいえ | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | operationalizationclusters |  いいえ | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | accounts | いいえ | 
> | teamaccounts | いいえ | 


## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | accounts | いいえ | 


## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | workspaces | いいえ | 

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | configurationassignments | はい。 [詳細情報](../../virtual-machines/move-region-maintenance-configuration.md) | 
> | maintenanceconfigurations | はい。 [詳細情報](../../virtual-machines/move-region-maintenance-configuration-resources.md) |
> | updates | いいえ | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | ID | いいえ | 
> | userassignedidentities | いいえ | 

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | managednetworks | いいえ | 
> | managednetworks / managednetworkgroups | いいえ |
> | managednetworks / managednetworkpeeringpolicies | いいえ | 
> | 通知 (notification) | いいえ | 

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | marketplaceregistrationdefinitions | いいえ | 
> | registrationassignments | いいえ |
> | registrationdefinitions | いいえ | 

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | getentities | いいえ | 
> | managementgroups | いいえ | 
> | managementgroups / settings | いいえ | 
> | resources | いいえ | 
> | starttenantbackfill | いいえ | 
> | tenantbackfillstatus | いいえ | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | accounts |  いいえ。Azure Maps は地理空間サービスです。 
> | accounts / privateatlases | いいえ

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | offers | いいえ | 
> | offertypes | いいえ | 
> | privategalleryitems | いいえ | 
> | privatestoreclient | いいえ | 
> | privatestores | いいえ | 
> | products | いいえ | 
> | publishers | いいえ | 
> | registrations | いいえ | 

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | classicdevservices | いいえ | 

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | agreements | いいえ | 
> | offertypes | いいえ | 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | mediaservices |  いいえ | 
> | mediaservices/liveevents |  いいえ | 
> | mediaservices/streamingendpoints |  いいえ | 

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | appclusters | いいえ | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | assessmentprojects | いいえ | 
> | migrateprojects | いいえ | 
> | movecollections | いいえ
> | projects | いいえ | 

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | いいえ | 
> | objectunderstandingaccounts | いいえ | 
> | remoterenderingaccounts | No | 
> | spatialanchorsaccounts | いいえ | 

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | netappaccounts | いいえ | 
> | netappaccounts/capacitypools | いいえ | 
> | netappaccounts/capacitypools/volumes | いいえ | 
> | netappaccounts/capacitypools/volumes/mounttargets | いいえ | 
> | netappaccounts/capacitypools/volumes/snapshots | いいえ | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | applicationgateways | いいえ |
> | applicationgatewaywebapplicationfirewallpolicies | いいえ | 
> | applicationsecuritygroups |  いいえ |  
> | azurefirewalls |  いいえ |  
> | bastionhosts | いいえ | 
> | bgpservicecommunities | いいえ |
> | connections |  いいえ | 
> | ddoscustompolicies |  いいえ | 
> | ddosprotectionplans | いいえ | 
> | dnszones |  いいえ | 
> | expressroutecircuits | いいえ | 
> | expressroutegateways | いいえ | 
> | expressrouteserviceproviders | いいえ | 
> | firewallpolicies | いいえ |
> | frontdoors | いいえ | 
> | ipallocations | いいえ |
> | ipgroups | いいえ |
> | loadbalancers | はい <br/><br/> 内部および外部のロード バランサーを移動するには [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) を使用します。 |
> | localnetworkgateways |  いいえ | 
> | natgateways |  いいえ | 
> | networkexperimentprofiles | いいえ |
> | networkintentpolicies |  いいえ | 
> | networkinterfaces | はい <br/><br/> NIC を移動するには [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) を使用します。 | 
> | networkprofiles | いいえ | 
> | networksecuritygroups | はい <br/><br/> ネットワーク セキュリティ グループ (NSG) を移動するには [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) を使用します。 | 
> | networkwatchers |  いいえ |  
> | networkwatchers/connectionmonitors |  いいえ | 
> | networkwatchers/flowlogs |  いいえ | 
> | networkwatchers/pingmeshes |  いいえ | 
> | p2svpngateways | いいえ | 
> | privatednszones |  いいえ |  
> | privatednszones/virtualnetworklinks | いいえ |> | privatednszonesinternal | いいえ |
> | privateendpointredirectmaps | いいえ |
> | privateendpoints | いいえ | 
> | privatelinkservices | いいえ | 
> | publicipaddresses | はい<br/><br/> パブリック IP アドレスを移動するには [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) を使用します。 |
> | publicipprefixes | いいえ | 
> | routefilters | いいえ | 
> | routetables |  いいえ | 
> | securitypartnerproviders | いいえ |
> | serviceendpointpolicies |  いいえ | 
> | trafficmanagergeographichierarchies | いいえ | 
> | trafficmanagerprofiles |  いいえ | 
> | trafficmanagerusermetricskeys | いいえ |
> | virtualhubs | いいえ | 
> | virtualnetworkgateways |  いいえ |  
> | virtualnetworks |  いいえ | 
> | virtualnetworktaps | いいえ | 
> | virtualwans | いいえ | 
> | vpngateways (仮想 WAN) | いいえ | 
> | vpnsites (仮想 WAN) | いいえ | 
> | vpnsites (仮想 WAN) | いいえ |


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | namespaces |  いいえ | 
> | namespaces/notificationhubs |  いいえ |  

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | osnamespaces | No | 

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | hypervsites | いいえ | 
> | importsites | いいえ | 
> | serversites | いいえ | 
> | vmwaresites | いいえ | 

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | clusters | いいえ | 
> | deletedworkspaces | いいえ | 
> | linktargets | いいえ | 
> | storageinsightconfigs | いいえ |
> | workspaces | いいえ |



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | managementassociations | いいえ |
> | managementconfigurations |  いいえ | 
> | solutions | いいえ |
> | views |  いいえ | 

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | legacypeerings | いいえ | 
> | peerasns | いいえ | 
> | peeringlocations | いいえ | 
> | peerings | いいえ | 
> | peeringservicecountries | いいえ | 
> | peeringservicelocations | いいえ | 
> | peeringserviceproviders | いいえ | 
> | peeringservices | いいえ | 

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | policyevents | いいえ | 
> | policystates | いいえ | 
> | policytrackedresources | いいえ | 
> | remediations | いいえ | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | consoles | いいえ |
> | dashboards | いいえ | 
> | usersettings | いいえ | 


## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | workspacecollections |  いいえ | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | capacities |  いいえ | 

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | accounts | いいえ | 

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | availableaccounts | いいえ | 
> | providerregistrations | いいえ | 
> | rollouts | いいえ | 

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | workspaces | いいえ | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | replicationeligibilityresults | いいえ |
> | vaults | いいえ。<br/><br/> Azure リージョン間で Azure Backup 用の Recovery Services コンテナーを移動することはできません。<br/><br/> Azure Site Recovery 用の Recovery Services コンテナーでは、[そのコンテナーを無効にして、ターゲット リージョンで再作成する](../../site-recovery/move-vaults-across-regions.md)ことができます。 | 

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | openshiftclusters | いいえ | 

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | namespaces |  いいえ | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | Query |  いいえ |  
> | resourcechangedetails | いいえ | 
> | resourcechanges | いいえ | 
> | resources | いいえ | 
> | resourceshistory | いいえ | 
> | subscriptionsstatus | いいえ | 

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | childresources | いいえ | 
> | emergingissues | いいえ | 
> | events | いいえ | 
> | metadata | いいえ | 
> | notifications | いいえ | 

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 |
> | ------------- | ----------- |
> | deploymentScripts |  はい<br/><br/>[Microsoft.Resources リソースを新しいリージョンに移動する](microsoft-resources-move-regions.md) |
> | templateSpecs |  はい<br/><br/>[Microsoft.Resources リソースを新しいリージョンに移動する](microsoft-resources-move-regions.md) |  

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | applications |  いいえ | 
> | saasresources | いいえ | 


## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | resourcehealthmetadata | いいえ |
> | searchservices |  いいえ | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | adaptivenetworkhardenings | いいえ | 
> | advancedthreatprotectionsettings | いいえ | 
> | alerts | いいえ | 
> | allowedconnections | いいえ | 
> | applicationwhitelistings | いいえ | 
> | assessmentmetadata | いいえ | 
> | assessments | いいえ | 
> | autodismissalertsrules | いいえ | 
> | automations | No | 
> | autoprovisioningsettings | いいえ |
> | complianceresults | いいえ | 
> | compliances | いいえ | 
> | datacollectionagents | いいえ | 
> | devicesecuritygroups | いいえ | 
> | discoveredsecuritysolutions | いいえ | 
> | externalsecuritysolutions | いいえ | 
> | informationprotectionpolicies | いいえ | 
> | iotsecuritysolutions | いいえ | 
> | iotsecuritysolutions / analyticsmodels | いいえ | 
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | いいえ | 
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | いいえ | 
> | jitnetworkaccesspolicies | いいえ | 
> | policies | いいえ | 
> | pricings | いいえ | 
> | regulatorycompliancestandards | いいえ | 
> | regulatorycompliancestandards / regulatorycompliancecontrols | いいえ | 
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | いいえ | 
> | securitycontacts | いいえ | 
> | securitysolutions | いいえ | 
> | securitysolutionsreferencedata | いいえ | 
> | securitystatuses | いいえ | 
> | securitystatusessummaries | いいえ | 
> | servervulnerabilityassessments | いいえ | 
> | settings | いいえ | 
> | subassessments | いいえ |
> | tasks | いいえ | 
> | topologies | いいえ | 
> | workspacesettings | いいえ | 

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | aggregations | いいえ | 
> | alertrules | いいえ | 
> | alertruletemplates | いいえ | 
> | automationrules | いいえ |
> | cases | いいえ | 
> | dataconnectors | いいえ | 
> | entities | いいえ | 
> | entityqueries | いいえ |
> | incidents | いいえ | 
> | officeconsents | いいえ | 
> | settings | いいえ | 
> | threatintelligence | いいえ | 

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | consoleservices | いいえ | 

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | gateways | いいえ | 
> | nodes | いいえ | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | namespaces |  いいえ | 
> | premiummessagingregions | いいえ | 
> | sku | いいえ | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | applications | いいえ | 
> | clusters |  いいえ |  
> | containergroups | いいえ | 
> | containergroupsets | いいえ | 
> | edgeclusters | いいえ | 
> | managedclusters | いいえ |
> | networks | いいえ | 
> | secretstores | いいえ | 
> | volumes | いいえ | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | applications |  いいえ | 
> | containergroups | いいえ | 
> | gateways |  いいえ | 
> | networks |  いいえ | 
> | secrets |  いいえ | 
> | volumes |  いいえ |  

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | rollouts | いいえ | 

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | signalr |  いいえ |  

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | hybridusebenefits | いいえ | 

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | appliancedefinitions | いいえ | 
> | appliances | いいえ | 
> | jitrequests | いいえ | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | instancepools | いいえ | 
> | locations | いいえ |
> | managedinstances | はい <br/><br/> マネージド インスタンスのリージョン間での移動に関する[詳細を参照してください](../../azure-sql/database/move-resources-across-regions.md)。 | 
> | managedinstances/databases | はい | 
> | servers | はい | 
> | servers/databases | はい <br/><br/> データベースのリージョン間での移動に関する[詳細を参照してください](../../azure-sql/database/move-resources-across-regions.md)。<br/><br/> Azure Resource Mover を使用した Azure SQL データベースの移動に関する[詳細を参照してください](../../resource-mover/tutorial-move-region-sql.md)。  | 
> | servers/elasticpools | はい <br/><br/> エラスティック プールのリージョン間での移動に関する[詳細を参照してください](../../azure-sql/database/move-resources-across-regions.md)。<br/><br/> Azure Resource Mover を使用した Azure SQL エラスティック プールを移動に関する[詳細を参照してください](../../resource-mover/tutorial-move-region-sql.md)。  | 
> | virtualclusters | はい | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  いいえ |  
> | sqlvirtualmachines |  いいえ |  


## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | storageaccounts | はい<br/><br/> [Azure ストレージ アカウントを別のリージョンに移動する](../../storage/common/storage-account-move.md) | 

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | caches | いいえ | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | storagesyncservices |  いいえ | 

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | storagesyncservices | いいえ | 

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | storagesyncservices | いいえ | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | managers | いいえ | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | clusters | いいえ |
> | streamingjobs |  いいえ |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | 環境 | いいえ | 
> | instances | いいえ | 

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | subscriptions | いいえ | 

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | services | いいえ | 
> | supporttickets | いいえ | 

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | workspaces | いいえ | 
> | workspaces / bigdatapools | いいえ | 
> | workspaces / sqlpools | いいえ | 


## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | 環境 |  いいえ | 
> | environments/eventsources |  いいえ |  
> | environments/referencedatasets |  いいえ | 

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | stores | いいえ | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | imagetemplates | いいえ | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | account |  いいえ | 
> | account / extension |  いいえ | 
> | account/project |  いいえ | 

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | arczones | いいえ | 
> | resourcepools | いいえ | 
> | vcenters | いいえ | 
> | virtualmachines | いいえ | 
> | virtualmachinetemplates | いいえ | 
> | virtualnetworks | いいえ | 

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | いいえ | 
> | dedicatedcloudservices | いいえ | 
> | virtualmachines | いいえ | 

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | devices | いいえ | 
> | vnfs | いいえ | 

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | accounts | いいえ | 
> | plans | いいえ | 
> | registeredsubscriptions | いいえ |


## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | availablestacks | いいえ | 
> | billingmeters | いいえ | 
> | certificates | いいえ | 
> | connectiongateways |  いいえ |  
> | connections |  いいえ |  
> | customapis |  いいえ | 
> | deletedsites | いいえ | 
> | deploymentlocations | いいえ | 
> | georegions | いいえ | 
> | hostingenvironments | いいえ | 
> | kubeenvironments | いいえ | 
> | publishingusers | いいえ |
> | recommendations | いいえ | 
> | resourcehealthmetadata | いいえ | 
> | runtimes | いいえ | 
> | serverfarms | いいえ |  
> | serverfarms / eventgridfilters | ×
> | sites |  いいえ | 
> | sites/premieraddons |  いいえ |  
> | sites/slots |  いいえ |  
> | sourcecontrols | いいえ |
> | staticsites | いいえ | 

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | multipleactivationkeys | いいえ |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | deviceservices | いいえ | 

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | workloads | いいえ | 

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | components | いいえ |
> | componentssummary | いいえ | 
> | monitorinstances | いいえ | 
> | monitorinstancessummary | いいえ | 
> | monitors | いいえ | 
## <a name="third-party-services"></a>サード パーティーのサービス

サード パーティのサービスは現在、移動操作をサポートしていません。

## <a name="next-steps"></a>次のステップ

Resource Mover サービスの[詳細を学習](../../resource-mover/overview.md)します。

