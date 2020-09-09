---
title: リソースの種類別の移動操作のサポート
description: 新しいリソース グループまたはサブスクリプションに移動できる Azure リソースの種類を一覧表示します。
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 16197210326d73284a4a83edc7876e4faddded86
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87079511"
---
# <a name="move-operation-support-for-resources"></a>リソースの操作のサポートの移動

この記事では、Azure リソースの種類は、移動操作をサポートしているかどうかを示します。 また、リソースを移動するときに考慮すべき特別な条件に関する情報も提供します。

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
> - [Microsoft.ChangeAnalysis](#microsoftchangeanalysis)
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
> - [Microsoft.CostManagementExports](#microsoftcostmanagementexports)
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
> - [Microsoft.PowerPlatform](#microsoftpowerplatform)
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
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | domainservices | いいえ | いいえ |
> | domainservices / oucontainer | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / operationresults | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | diagnosticsettings | いいえ | いいえ |
> | diagnosticsettingscategories | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | privatelinkforazuread | はい | はい |
> | tenants | はい | はい |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | operationresults | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | supportproviders | いいえ | いいえ |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | aadsupportcases | いいえ | いいえ |
> | addsservices | いいえ | いいえ |
> | agents | いいえ | いいえ |
> | anonymousapiusers | いいえ | いいえ |
> | configuration | いいえ | いいえ |
> | logs | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | reports | いいえ | いいえ |
> | servicehealthmetrics | いいえ | いいえ |
> | services | いいえ | いいえ |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | configuration | いいえ | いいえ |
> | generaterecommendations | いいえ | いいえ |
> | metadata | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | recommendations | いいえ | いいえ |
> | suppressions | いいえ | いいえ |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | actionrules | はい | はい |
> | alerts | いいえ | いいえ |
> | alertslist | いいえ | いいえ |
> | alertsmetadata | いいえ | いいえ |
> | alertssummary | いいえ | いいえ |
> | alertssummarylist | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | smartdetectoralertrules | はい | はい |
> | smartgroups | いいえ | いいえ |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | locations | いいえ | いいえ |
> | locations / checknameavailability | いいえ | いいえ |
> | locations / operationresults | いいえ | いいえ |
> | locations / operationstatuses | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | servers | はい | はい |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!IMPORTANT]
> 従量課金 SKU に設定されている API Management サービスは移動できません。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | checkfeedbackrequired | いいえ | × |
> | checknameavailability | いいえ | × |
> | checkservicenameavailability | いいえ | × |
> | operations | いいえ | × |
> | reportfeedback | いいえ | × |
> | サービス (service) | はい | ○ |
> | validateservicename | いいえ | × |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | checknameavailability | いいえ | いいえ |
> | configurationstores | はい | はい |
> | configurationstores / eventgridfilters | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / operationsstatus | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | locations | いいえ | いいえ |
> | locations / checknameavailability | いいえ | いいえ |
> | locations / operationresults | いいえ | いいえ |
> | locations / operationstatus | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | spring | はい | はい |
> | spring / apps | いいえ | いいえ |
> | spring / apps / deployments | いいえ | いいえ |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!IMPORTANT]
> [App Service move guidance (App Service の移動のガイダンス)](./move-limitations/app-service-move-limitations.md) に関する記事をご覧ください。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | apiapps | いいえ | × |
> | appidentities | いいえ | × |
> | gateways | いいえ | いいえ |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | attestationproviders | はい | はい |
> | operations | いいえ | いいえ |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | checkaccess | いいえ | いいえ |
> | classicadministrators | いいえ | いいえ |
> | dataaliases | いいえ | いいえ |
> | denyassignments | いいえ | いいえ |
> | elevateaccess | いいえ | いいえ |
> | findorphanroleassignments | いいえ | いいえ |
> | locks | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | operationstatus | いいえ | いいえ |
> | 権限 | いいえ | いいえ |
> | policyassignments | いいえ | いいえ |
> | policydefinitions | いいえ | いいえ |
> | policysetdefinitions | いいえ | いいえ |
> | privatelinkassociations | いいえ | いいえ |
> | provideroperations | いいえ | いいえ |
> | resourcemanagementprivatelinks | いいえ | いいえ |
> | roleassignments | いいえ | いいえ |
> | roleassignmentsusagemetrics | いいえ | いいえ |
> | roledefinitions | いいえ | いいえ |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!IMPORTANT]
> Runbook は Automation アカウントと同じリソース グループに存在する必要があります。
>
> 詳しくは、「[Azure Automation アカウントを別のサブスクリプションに移動する](../../automation/how-to/move-account.md?toc=/azure/azure-resource-manager/toc.json)」をご覧ください。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | automationaccounts | ○ | はい |
> | automationaccounts/configurations | ○ | はい |
> | automationaccounts / jobs | × | いいえ |
> | automationaccounts / privateendpointconnectionproxies | × | いいえ |
> | automationaccounts / privateendpointconnections | × | いいえ |
> | automationaccounts / privatelinkresources | × | × |
> | automationaccounts/runbooks | ○ | ○ |
> | automationaccounts / softwareupdateconfigurations | × | いいえ |
> | automationaccounts / webhooks | × | いいえ |
> | operations | × | × |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | locations | いいえ | いいえ |
> | locations / checkquotaavailability | いいえ | いいえ |
> | locations / checktrialavailability | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | privateclouds | はい | はい |
> | privateclouds / clusters | いいえ | いいえ |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | はい | はい |
> | b2ctenants | いいえ | いいえ |
> | checknameavailability | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | datacontrollers | いいえ | いいえ |
> | hybriddatamanagers | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | postgresinstances | いいえ | いいえ |
> | sqlinstances | いいえ | いいえ |
> | sqlmanagedinstances | いいえ | いいえ |
> | sqlserverinstances | いいえ | いいえ |
> | sqlserverregistrations | はい | はい |
> | sqlserverregistrations / sqlservers | いいえ | いいえ |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | cloudmanifestfiles | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | registrations | はい | はい |
> | registrations / customersubscriptions | いいえ | いいえ |
> | registrations / products | いいえ | いいえ |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | clusters | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | batchaccounts | はい | はい |
> | locations | いいえ | いいえ |
> | locations / accountoperationresults | いいえ | いいえ |
> | locations / checknameavailability | いいえ | いいえ |
> | locations / quotas | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | billingaccounts | いいえ | いいえ |
> | billingaccounts / agreements | いいえ | いいえ |
> | billingaccounts / billingpermissions | いいえ | いいえ |
> | billingaccounts / billingprofiles | いいえ | いいえ |
> | billingaccounts / billingprofiles / availablebalance | いいえ | いいえ |
> | billingaccounts / billingprofiles / billingpermissions | いいえ | いいえ |
> | billingaccounts / billingprofiles / billingroleassignments | いいえ | いいえ |
> | billingaccounts / billingprofiles / billingroledefinitions | いいえ | いいえ |
> | billingaccounts / billingprofiles / billingsubscriptions | いいえ | いいえ |
> | billingaccounts / billingprofiles / createbillingroleassignment | いいえ | いいえ |
> | billingaccounts / billingprofiles / customers | いいえ | いいえ |
> | billingaccounts / billingprofiles / instructions | いいえ | いいえ |
> | billingaccounts / billingprofiles / invoices | いいえ | いいえ |
> | billingaccounts / billingprofiles / invoices / pricesheet | いいえ | いいえ |
> | billingaccounts / billingprofiles / invoices / transactions | いいえ | いいえ |
> | billingaccounts / billingprofiles / invoicesections | いいえ | いいえ |
> | billingaccounts / billingprofiles / invoicesections / billingpermissions | いいえ | いいえ |
> | billingaccounts / billingprofiles / invoicesections / billingroleassignments | いいえ | いいえ |
> | billingaccounts / billingprofiles / invoicesections / billingroledefinitions | いいえ | いいえ |
> | billingaccounts / billingprofiles / invoicesections / billingsubscriptions | いいえ | いいえ |
> | billingaccounts / billingprofiles / invoicesections / createbillingroleassignment | いいえ | いいえ |
> | billingaccounts / billingprofiles / invoicesections / initiatetransfer | いいえ | いいえ |
> | billingaccounts / billingprofiles / invoicesections / products | いいえ | いいえ |
> | billingaccounts / billingprofiles / invoicesections / products / transfer | いいえ | いいえ |
> | billingaccounts / billingprofiles / invoicesections / products / updateautorenew | いいえ | いいえ |
> | billingaccounts / billingprofiles / invoicesections / transactions | いいえ | いいえ |
> | billingaccounts / billingprofiles / invoicesections / transfers | いいえ | いいえ |
> | billingaccounts / billingprofiles / patchoperations | いいえ | いいえ |
> | billingaccounts / billingprofiles / paymentmethods | いいえ | いいえ |
> | billingaccounts / billingprofiles / policies | いいえ | いいえ |
> | billingaccounts / billingprofiles / pricesheet | いいえ | いいえ |
> | billingaccounts / billingprofiles / pricesheetdownloadoperations | いいえ | いいえ |
> | billingaccounts / billingprofiles / products | いいえ | いいえ |
> | billingaccounts / billingprofiles / transactions | いいえ | いいえ |
> | billingaccounts / billingroleassignments | いいえ | いいえ |
> | billingaccounts / billingroledefinitions | いいえ | いいえ |
> | billingaccounts / billingsubscriptions | いいえ | いいえ |
> | billingaccounts / billingsubscriptions / invoices | いいえ | いいえ |
> | billingaccounts / createbillingroleassignment | いいえ | いいえ |
> | billingaccounts / createinvoicesectionoperations | いいえ | いいえ |
> | billingaccounts / customers | いいえ | いいえ |
> | billingaccounts / customers / billingpermissions | いいえ | いいえ |
> | billingaccounts / customers / billingsubscriptions | いいえ | いいえ |
> | billingaccounts / customers / initiatetransfer | いいえ | いいえ |
> | billingaccounts / customers / policies | いいえ | いいえ |
> | billingaccounts / customers / products | いいえ | いいえ |
> | billingaccounts / customers / transactions | いいえ | いいえ |
> | billingaccounts / customers / transfers | いいえ | いいえ |
> | billingaccounts / departments | いいえ | いいえ |
> | billingaccounts / enrollmentaccounts | いいえ | いいえ |
> | billingaccounts / invoices | いいえ | いいえ |
> | billingaccounts / invoicesections | いいえ | いいえ |
> | billingaccounts / invoicesections / billingsubscriptionmoveoperations | いいえ | いいえ |
> | billingaccounts / invoicesections / billingsubscriptions | いいえ | いいえ |
> | billingaccounts / invoicesections / billingsubscriptions / transfer | いいえ | いいえ |
> | billingaccounts / invoicesections / elevate | いいえ | いいえ |
> | billingaccounts / invoicesections / initiatetransfer | いいえ | いいえ |
> | billingaccounts / invoicesections / patchoperations | いいえ | いいえ |
> | billingaccounts / invoicesections / productmoveoperations | いいえ | いいえ |
> | billingaccounts / invoicesections / products | いいえ | いいえ |
> | billingaccounts / invoicesections / products / transfer | いいえ | いいえ |
> | billingaccounts / invoicesections / products / updateautorenew | いいえ | いいえ |
> | billingaccounts / invoicesections / producttransfersresults | いいえ | いいえ |
> | billingaccounts / invoicesections / transactions | いいえ | いいえ |
> | billingaccounts / invoicesections / transfers | いいえ | いいえ |
> | billingaccounts / lineofcredit | いいえ | いいえ |
> | billingaccounts / listinvoicesectionswithcreatesubscriptionpermission | いいえ | いいえ |
> | billingaccounts / operationresults | いいえ | いいえ |
> | billingaccounts / patchoperations | いいえ | いいえ |
> | billingaccounts / paymentmethods | いいえ | いいえ |
> | billingaccounts / products | いいえ | いいえ |
> | billingaccounts / transactions | いいえ | いいえ |
> | billingperiods | いいえ | いいえ |
> | billingpermissions | いいえ | いいえ |
> | billingproperty | いいえ | いいえ |
> | billingroleassignments | いいえ | いいえ |
> | billingroledefinitions | いいえ | いいえ |
> | createbillingroleassignment | いいえ | いいえ |
> | departments | いいえ | いいえ |
> | enrollmentaccounts | いいえ | いいえ |
> | invoices | いいえ | いいえ |
> | operationresults | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | operationstatus | いいえ | いいえ |
> | transfers | いいえ | いいえ |
> | transfers / accepttransfer | いいえ | いいえ |
> | transfers / declinetransfer | いいえ | いいえ |
> | transfers / operationstatus | いいえ | いいえ |
> | transfers / validatetransfer | いいえ | いいえ |
> | validateaddress | いいえ | いいえ |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | listcommunicationpreference | いいえ | いいえ |
> | mapapis | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | updatecommunicationpreference | いいえ | いいえ |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | biztalk | いいえ | いいえ |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | いいえ | いいえ |
> | cordamembers | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / blockchainmemberoperationresults | いいえ | いいえ |
> | locations / checknameavailability | いいえ | いいえ |
> | locations / listconsortiums | いいえ | いいえ |
> | locations / watcheroperationresults | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | watchers | いいえ | いいえ |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | operations | いいえ | いいえ |
> | tokenservices | いいえ | いいえ |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | いいえ | いいえ |
> | blueprintassignments / assignmentoperations | いいえ | いいえ |
> | blueprintassignments / operations | いいえ | いいえ |
> | blueprints | いいえ | いいえ |
> | blueprints / artifacts | いいえ | いいえ |
> | blueprints / versions | いいえ | いいえ |
> | blueprints / versions / artifacts | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | botservices | はい | はい |
> | botservices / channels | いいえ | いいえ |
> | botservices / connections | いいえ | いいえ |
> | checknameavailability | いいえ | いいえ |
> | listauthserviceproviders | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!IMPORTANT]
> 仮想ネットワークを使用して Azure Cache for Redis インスタンスが構成されている場合、インスタンスを別のサブスクリプションに移動させることはできません。 「[ネットワーク リソースの移動ガイダンス](./move-limitations/networking-move-limitations.md)」を参照してください。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | checknameavailability | × | いいえ |
> | locations | × | いいえ |
> | locations / operationresults | いいえ | いいえ |
> | locations / operationsstatus | × | いいえ |
> | operations | × | × |
> | redis | はい | はい |
> | redis / eventgridfilters | いいえ | いいえ |
> | redis / privatelinkresources | × | いいえ |
> | redisenterprise | × | × |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | appliedreservations | いいえ | いいえ |
> | calculateexchange | いいえ | いいえ |
> | calculateprice | いいえ | いいえ |
> | calculatepurchaseprice | いいえ | いいえ |
> | catalogs | いいえ | いいえ |
> | checkoffers | いいえ | いいえ |
> | checkpurchasestatus | いいえ | いいえ |
> | checkscopes | いいえ | いいえ |
> | commercialreservationorders | いいえ | いいえ |
> | exchange | いいえ | いいえ |
> | listbenefits | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | placepurchaseorder | いいえ | いいえ |
> | reservationorders | いいえ | いいえ |
> | reservationorders / availablescopes | いいえ | いいえ |
> | reservationorders / calculaterefund | いいえ | いいえ |
> | reservationorders / merge | いいえ | いいえ |
> | reservationorders / reservations | いいえ | いいえ |
> | reservationorders / reservations / availablescopes | いいえ | いいえ |
> | reservationorders / reservations / revisions | いいえ | いいえ |
> | reservationorders / return | いいえ | いいえ |
> | reservationorders / split | いいえ | いいえ |
> | reservationorders / swap | いいえ | いいえ |
> | reservations | いいえ | いいえ |
> | resources | いいえ | いいえ |
> | validatereservationorder | いいえ | いいえ |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallmanagedrulesets | いいえ | いいえ |
> | cdnwebapplicationfirewallpolicies | はい | はい |
> | checknameavailability | いいえ | いいえ |
> | checkresourceusage | いいえ | いいえ |
> | edgenodes | いいえ | いいえ |
> | operationresults | いいえ | いいえ |
> | operationresults / profileresults | いいえ | いいえ |
> | operationresults / profileresults / endpointresults | いいえ | いいえ |
> | operationresults / profileresults / endpointresults / customdomainresults | いいえ | いいえ |
> | operationresults / profileresults / endpointresults / origingroupresults | いいえ | いいえ |
> | operationresults / profileresults / endpointresults / originresults | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | profiles | はい | はい |
> | profiles/endpoints | はい | はい |
> | profiles / endpoints / customdomains | いいえ | いいえ |
> | profiles / endpoints / origingroups | いいえ | いいえ |
> | profiles / endpoints / origins | いいえ | いいえ |
> | validateprobe | いいえ | いいえ |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!IMPORTANT]
> [App Service move guidance (App Service の移動のガイダンス)](./move-limitations/app-service-move-limitations.md) に関する記事をご覧ください。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | certificateorders | はい | ○ |
> | certificateorders / certificates | いいえ | × |
> | operations | いいえ | × |
> | validatecertificateregistrationinformation | いいえ | いいえ |

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | operations | いいえ | いいえ |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!IMPORTANT]
> [Classic deployment move guidance (クラシック デプロイの移動のガイダンス)](./move-limitations/classic-model-move-limitations.md) に関する記事をご覧ください。 クラシック デプロイのリソースは、そのシナリオに固有の操作を使用して、サブスクリプション間で移動できます。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | capabilities | × | いいえ |
> | checkdomainnameavailability | × | いいえ |
> | domainnames | はい | いいえ |
> | domainnames / capabilities | × | いいえ |
> | domainnames / internalloadbalancers | × | いいえ |
> | domainnames / servicecertificates | × | いいえ |
> | domainnames / slots | × | いいえ |
> | domainnames / slots / roles | × | いいえ |
> | domainnames / slots / roles / metricdefinitions | × | いいえ |
> | domainnames / slots / roles / metrics | × | いいえ |
> | movesubscriptionresources | × | いいえ |
> | operatingsystemfamilies | × | いいえ |
> | operatingsystems | × | いいえ |
> | operations | × | いいえ |
> | operationstatuses | × | いいえ |
> | quotas | × | いいえ |
> | resourcetypes | × | いいえ |
> | validatesubscriptionmoveavailability | × | × |
> | virtualmachines | はい | いいえ |
> | virtualmachines / diagnosticsettings | × | いいえ |
> | virtualmachines / metricdefinitions | × | いいえ |
> | virtualmachines / metrics | いいえ | × |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!IMPORTANT]
> [Classic deployment move guidance (クラシック デプロイの移動のガイダンス)](./move-limitations/classic-model-move-limitations.md) に関する記事をご覧ください。 クラシック デプロイのリソースは、そのシナリオに固有の操作を使用して、サブスクリプション間で移動できます。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | classicinfrastructureresources | × | × |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!IMPORTANT]
> [Classic deployment move guidance (クラシック デプロイの移動のガイダンス)](./move-limitations/classic-model-move-limitations.md) に関する記事をご覧ください。 クラシック デプロイのリソースは、そのシナリオに固有の操作を使用して、サブスクリプション間で移動できます。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | capabilities | × | いいえ |
> | expressroutecrossconnections | いいえ | いいえ |
> | expressroutecrossconnections / peerings | × | いいえ |
> | gatewaysupporteddevices | × | いいえ |
> | networksecuritygroups | × | いいえ |
> | operations | × | いいえ |
> | quotas | × | いいえ |
> | reservedips | × | いいえ |
> | virtualnetworks | × | いいえ |
> | virtualnetworks / remotevirtualnetworkpeeringproxies | × | いいえ |
> | virtualnetworks / virtualnetworkpeerings | × | いいえ |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!IMPORTANT]
> [Classic deployment move guidance (クラシック デプロイの移動のガイダンス)](./move-limitations/classic-model-move-limitations.md) に関する記事をご覧ください。 クラシック デプロイのリソースは、そのシナリオに固有の操作を使用して、サブスクリプション間で移動できます。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | capabilities | × | いいえ |
> | checkstorageaccountavailability | × | いいえ |
> | disks | × | いいえ |
> | images | × | いいえ |
> | operations | × | いいえ |
> | osimages | × | いいえ |
> | osplatformimages | × | いいえ |
> | publicimages | × | いいえ |
> | quotas | × | × |
> | storageaccounts | はい | いいえ |
> | storageaccounts / blobservices | × | いいえ |
> | storageaccounts / fileservices | × | いいえ |
> | storageaccounts / metricdefinitions | いいえ | いいえ |
> | storageaccounts / metrics | × | いいえ |
> | storageaccounts / queueservices | × | いいえ |
> | storageaccounts / services | × | いいえ |
> | storageaccounts / services / diagnosticsettings | × | いいえ |
> | storageaccounts / services / metricdefinitions | × | いいえ |
> | storageaccounts / services / metrics | × | いいえ |
> | storageaccounts / tableservices | × | いいえ |
> | storageaccounts / vmimages | × | いいえ |
> | vmimages | × | × |

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!IMPORTANT]
> [Classic deployment move guidance (クラシック デプロイの移動のガイダンス)](./move-limitations/classic-model-move-limitations.md) に関する記事をご覧ください。 クラシック デプロイのリソースは、そのシナリオに固有の操作を使用して、サブスクリプション間で移動できます。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | operations | いいえ | × |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | accounts | はい | はい |
> | checkdomainavailability | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / checkskuavailability | いいえ | いいえ |
> | locations / deletevirtualnetworkorsubnets | いいえ | いいえ |
> | locations / operationresults | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | operations | いいえ | いいえ |
> | ratecard | いいえ | いいえ |
> | usageaggregates | いいえ | いいえ |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!IMPORTANT]
> [Virtual Machines move guidance (仮想マシンの移動のガイダンス)](./move-limitations/virtual-machines-move-limitations.md) に関する記事をご覧ください。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | availabilitysets | はい | はい |
> | diskaccesses | いいえ | × |
> | diskencryptionsets | いいえ | × |
> | disks | はい | ○ |
> | galleries | いいえ | × |
> | galleries/images | いいえ | × |
> | galleries/images/versions | いいえ | × |
> | hostgroups | いいえ | × |
> | hostgroups/hosts | いいえ | × |
> | images | はい | はい |
> | locations | いいえ | × |
> | locations / artifactpublishers | いいえ | × |
> | locations / capsoperations | いいえ | × |
> | locations / diskoperations | いいえ | × |
> | locations / loganalytics | いいえ | × |
> | locations / operations | いいえ | × |
> | locations / publishers | いいえ | × |
> | locations / runcommands | いいえ | × |
> | locations / usages | いいえ | × |
> | locations / virtualmachines | いいえ | × |
> | locations / vmsizes | いいえ | × |
> | locations / vsmoperations | いいえ | × |
> | operations | いいえ | いいえ |
> | proximityplacementgroups | はい | ○ |
> | restorepointcollections | いいえ | いいえ |
> | restorepointcollections / restorepoints | いいえ | × |
> | sharedvmextensions | いいえ | × |
> | sharedvmimages | いいえ | × |
> | sharedvmimages/versions | いいえ | × |
> | スナップショット | はい | ○ |
> | sshpublickeys | いいえ | × |
> | virtualmachines | はい | はい |
> | virtualmachines/extensions | はい | ○ |
> | virtualmachines / metricdefinitions | いいえ | × |
> | virtualmachines / runcommands | いいえ | × |
> | virtualmachinescalesets | はい | ○ |
> | virtualmachinescalesets / extensions | いいえ | × |
> | virtualmachinescalesets / networkinterfaces | いいえ | × |
> | virtualmachinescalesets / publicipaddresses | いいえ | × |
> | virtualmachinescalesets / virtualmachines | いいえ | × |
> | virtualmachinescalesets / virtualmachines / networkinterfaces | いいえ | × |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | aggregatedcost | いいえ | いいえ |
> | balances | いいえ | いいえ |
> | budgets | いいえ | いいえ |
> | charges | いいえ | いいえ |
> | costtags | いいえ | いいえ |
> | credits | いいえ | いいえ |
> | events | いいえ | いいえ |
> | forecasts | いいえ | いいえ |
> | lots | いいえ | いいえ |
> | marketplaces | いいえ | いいえ |
> | operationresults | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | operationstatus | いいえ | いいえ |
> | pricesheets | いいえ | いいえ |
> | products | いいえ | いいえ |
> | reservationdetails | いいえ | いいえ |
> | reservationrecommendationdetails | いいえ | いいえ |
> | reservationrecommendations | いいえ | いいえ |
> | reservationsummaries | いいえ | いいえ |
> | reservationtransactions | いいえ | いいえ |
> | tags | いいえ | いいえ |
> | tenants | いいえ | いいえ |
> | terms | いいえ | いいえ |
> | usagedetails | いいえ | いいえ |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | containergroups | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / cachedimages | いいえ | いいえ |
> | locations / capabilities | いいえ | いいえ |
> | locations / deletevirtualnetworkorsubnets | いいえ | いいえ |
> | locations / operations | いいえ | いいえ |
> | locations / usages | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | serviceassociationlinks | いいえ | いいえ |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | checknameavailability | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / authorize | いいえ | いいえ |
> | locations / deletevirtualnetworkorsubnets | いいえ | いいえ |
> | locations / operationresults | いいえ | いいえ |
> | locations / setupauth | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | registries | はい | はい |
> | registries / agentpools | はい | はい |
> | registries / agentpools / listqueuestatus | いいえ | いいえ |
> | registries / builds | いいえ | いいえ |
> | registries / builds / cancel | いいえ | いいえ |
> | registries / builds / getloglink | いいえ | いいえ |
> | registries/buildtasks | はい | はい |
> | registries / buildtasks / listsourcerepositoryproperties | いいえ | いいえ |
> | registries / buildtasks / steps | いいえ | いいえ |
> | registries / buildtasks / steps / listbuildarguments | いいえ | いいえ |
> | registries / eventgridfilters | いいえ | いいえ |
> | registries / exportpipelines | いいえ | いいえ |
> | registries / generatecredentials | いいえ | いいえ |
> | registries / getbuildsourceuploadurl | いいえ | いいえ |
> | registries / getcredentials | いいえ | いいえ |
> | registries / importimage | いいえ | いいえ |
> | registries / importpipelines | いいえ | いいえ |
> | registries / listbuildsourceuploadurl | いいえ | いいえ |
> | registries / listcredentials | いいえ | いいえ |
> | registries / listpolicies | いいえ | いいえ |
> | registries / listusages | いいえ | いいえ |
> | registries / pipelineruns | いいえ | いいえ |
> | registries / privateendpointconnectionproxies | いいえ | いいえ |
> | registries / privateendpointconnectionproxies / validate | いいえ | いいえ |
> | registries / privateendpointconnections | いいえ | いいえ |
> | registries / privatelinkresources | いいえ | いいえ |
> | registries / queuebuild | いいえ | いいえ |
> | registries / regeneratecredential | いいえ | いいえ |
> | registries / regeneratecredentials | いいえ | いいえ |
> | registries/replications | はい | はい |
> | registries / runs | いいえ | いいえ |
> | registries / runs / cancel | いいえ | いいえ |
> | registries / runs / listlogsasurl | いいえ | いいえ |
> | registries / schedulerun | いいえ | いいえ |
> | registries / scopemaps | いいえ | いいえ |
> | registries/taskruns | いいえ | いいえ |
> | registries / taskruns / listdetails | いいえ | いいえ |
> | registries/tasks | はい | はい |
> | registries / tasks / listdetails | いいえ | いいえ |
> | registries / tokens | いいえ | いいえ |
> | registries / updatepolicies | いいえ | いいえ |
> | registries/webhooks | はい | はい |
> | registries / webhooks / getcallbackconfig | いいえ | いいえ |
> | registries / webhooks / listevents | いいえ | いいえ |
> | registries / webhooks / ping | いいえ | いいえ |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | containerservices | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / openshiftclusters | いいえ | いいえ |
> | locations / operationresults | いいえ | いいえ |
> | locations / operations | いいえ | いいえ |
> | locations / orchestrators | いいえ | いいえ |
> | managedclusters | いいえ | いいえ |
> | openshiftmanagedclusters | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | applications | いいえ | いいえ |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | accounts | いいえ | いいえ |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | alerts | いいえ | いいえ |
> | billingaccounts | いいえ | いいえ |
> | budgets | いいえ | いいえ |
> | cloudconnectors | いいえ | いいえ |
> | connectors | はい | はい |
> | departments | いいえ | いいえ |
> | dimensions | いいえ | いいえ |
> | enrollmentaccounts | いいえ | いいえ |
> | exports | いいえ | いいえ |
> | externalbillingaccounts | いいえ | いいえ |
> | externalbillingaccounts / alerts | いいえ | いいえ |
> | externalbillingaccounts / dimensions | いいえ | いいえ |
> | externalbillingaccounts / forecast | いいえ | いいえ |
> | externalbillingaccounts / query | いいえ | いいえ |
> | externalsubscriptions | いいえ | いいえ |
> | externalsubscriptions / alerts | いいえ | いいえ |
> | externalsubscriptions / dimensions | いいえ | いいえ |
> | externalsubscriptions / forecast | いいえ | いいえ |
> | externalsubscriptions / query | いいえ | いいえ |
> | forecast | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | query | いいえ | いいえ |
> | registrations | いいえ | いいえ |
> | reportconfigs | いいえ | いいえ |
> | reports | いいえ | いいえ |
> | settings | いいえ | いいえ |
> | showbackrules | いいえ | いいえ |
> | views | いいえ | いいえ |

## <a name="microsoftcostmanagementexports"></a>Microsoft.CostManagementExports

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | operations | いいえ | いいえ |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | hubs | いいえ | いいえ |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | operations | いいえ | いいえ |
> | requests | いいえ | いいえ |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | associations | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / operationstatuses | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | resourceproviders | はい | はい |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | jobs | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / availableskus | いいえ | いいえ |
> | locations / checknameavailability | いいえ | いいえ |
> | locations / operationresults | いいえ | いいえ |
> | locations / regionconfiguration | いいえ | いいえ |
> | locations / validateaddress | いいえ | いいえ |
> | locations / validateinputs | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | availableskus | いいえ | いいえ |
> | databoxedgedevices | はい | はい |
> | databoxedgedevices / checknameavailability | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | locations | いいえ | いいえ |
> | locations / getnetworkpolicies | いいえ | いいえ |
> | locations / operationstatuses | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | workspaces | いいえ | いいえ |
> | workspaces / dbworkspaces | いいえ | いいえ |
> | workspaces / virtualnetworkpeerings | いいえ | いいえ |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | catalogs | はい | はい |
> | checknameavailability | いいえ | いいえ |
> | datacatalogs | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / jobs | いいえ | いいえ |
> | locations / operationresults | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | いいえ | いいえ |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | packages | いいえ | いいえ |
> | plans | いいえ | いいえ |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | checkazuredatafactorynameavailability | いいえ | いいえ |
> | checkdatafactorynameavailability | いいえ | いいえ |
> | datafactories | はい | はい |
> | datafactories / diagnosticsettings | いいえ | いいえ |
> | datafactories / metricdefinitions | いいえ | いいえ |
> | datafactoryschema | いいえ | いいえ |
> | factories | はい | はい |
> | factories / integrationruntimes | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / configurefactoryrepo | いいえ | いいえ |
> | locations / getfeaturevalue | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | いいえ | いいえ |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | accounts | はい | はい |
> | accounts / datalakestoreaccounts | いいえ | いいえ |
> | accounts / storageaccounts | いいえ | いいえ |
> | accounts / storageaccounts / containers | いいえ | いいえ |
> | accounts / storageaccounts / containers / listsastokens | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / capability | いいえ | いいえ |
> | locations / checknameavailability | いいえ | いいえ |
> | locations / operationresults | いいえ | いいえ |
> | locations / usages | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | accounts | はい | はい |
> | accounts / eventgridfilters | いいえ | いいえ |
> | accounts / firewallrules | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / capability | いいえ | いいえ |
> | locations / checknameavailability | いいえ | いいえ |
> | locations / deletevirtualnetworkorsubnets | いいえ | いいえ |
> | locations / operationresults | いいえ | いいえ |
> | locations / usages | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | locations | いいえ | いいえ |
> | locations / checknameavailability | いいえ | いいえ |
> | locations / operationresults | いいえ | いいえ |
> | locations / operationstatuses | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | services | いいえ | いいえ |
> | services/projects | いいえ | いいえ |
> | slots | いいえ | いいえ |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | backupvaults | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | accounts | はい | はい |
> | accounts / shares | いいえ | いいえ |
> | accounts / shares / datasets | いいえ | いいえ |
> | accounts / shares / invitations | いいえ | いいえ |
> | accounts / shares / providersharesubscriptions | いいえ | いいえ |
> | accounts / shares / synchronizationsettings | いいえ | いいえ |
> | accounts / sharesubscriptions | いいえ | いいえ |
> | accounts / sharesubscriptions / consumersourcedatasets | いいえ | いいえ |
> | accounts / sharesubscriptions / datasetmappings | いいえ | いいえ |
> | accounts / sharesubscriptions / triggers | いいえ | いいえ |
> | listinvitations | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / consumerinvitations | いいえ | いいえ |
> | locations / operationresults | いいえ | いいえ |
> | locations / rejectinvitation | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | checknameavailability | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / azureasyncoperation | いいえ | いいえ |
> | locations / operationresults | いいえ | いいえ |
> | locations / performancetiers | いいえ | いいえ |
> | locations / privateendpointconnectionazureasyncoperation | いいえ | いいえ |
> | locations / privateendpointconnectionoperationresults | いいえ | いいえ |
> | locations / privateendpointconnectionproxyazureasyncoperation | いいえ | いいえ |
> | locations / privateendpointconnectionproxyoperationresults | いいえ | いいえ |
> | locations / recommendedactionsessionsazureasyncoperation | いいえ | いいえ |
> | locations / recommendedactionsessionsoperationresults | いいえ | いいえ |
> | locations / securityalertpoliciesazureasyncoperation | いいえ | いいえ |
> | locations / securityalertpoliciesoperationresults | いいえ | いいえ |
> | locations / serverkeyazureasyncoperation | いいえ | いいえ |
> | locations / serverkeyoperationresults | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | servers | はい | はい |
> | servers / advisors | いいえ | いいえ |
> | servers / privateendpointconnectionproxies | いいえ | いいえ |
> | servers / privateendpointconnections | いいえ | いいえ |
> | servers / privatelinkresources | いいえ | いいえ |
> | servers / querytexts | いいえ | いいえ |
> | servers / recoverableservers | いいえ | いいえ |
> | servers / topquerystatistics | いいえ | いいえ |
> | servers / virtualnetworkrules | いいえ | いいえ |
> | servers / waitstatistics | いいえ | いいえ |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | checknameavailability | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / administratorazureasyncoperation | いいえ | いいえ |
> | locations / administratoroperationresults | いいえ | いいえ |
> | locations / azureasyncoperation | いいえ | いいえ |
> | locations / operationresults | いいえ | いいえ |
> | locations / performancetiers | いいえ | いいえ |
> | locations / privateendpointconnectionazureasyncoperation | いいえ | いいえ |
> | locations / privateendpointconnectionoperationresults | いいえ | いいえ |
> | locations / privateendpointconnectionproxyazureasyncoperation | いいえ | いいえ |
> | locations / privateendpointconnectionproxyoperationresults | いいえ | いいえ |
> | locations / recommendedactionsessionsazureasyncoperation | いいえ | いいえ |
> | locations / recommendedactionsessionsoperationresults | いいえ | いいえ |
> | locations / securityalertpoliciesazureasyncoperation | いいえ | いいえ |
> | locations / securityalertpoliciesoperationresults | いいえ | いいえ |
> | locations / serverkeyazureasyncoperation | いいえ | いいえ |
> | locations / serverkeyoperationresults | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | servers | はい | はい |
> | servers / advisors | いいえ | いいえ |
> | servers / keys | いいえ | いいえ |
> | servers / privateendpointconnectionproxies | いいえ | いいえ |
> | servers / privateendpointconnections | いいえ | いいえ |
> | servers / privatelinkresources | いいえ | いいえ |
> | servers / querytexts | いいえ | いいえ |
> | servers / recoverableservers | いいえ | いいえ |
> | servers / topquerystatistics | いいえ | いいえ |
> | servers / virtualnetworkrules | いいえ | いいえ |
> | servers / waitstatistics | いいえ | いいえ |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | checknameavailability | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / administratorazureasyncoperation | いいえ | いいえ |
> | locations / administratoroperationresults | いいえ | いいえ |
> | locations / azureasyncoperation | いいえ | いいえ |
> | locations / operationresults | いいえ | いいえ |
> | locations / performancetiers | いいえ | いいえ |
> | locations / privateendpointconnectionazureasyncoperation | いいえ | いいえ |
> | locations / privateendpointconnectionoperationresults | いいえ | いいえ |
> | locations / privateendpointconnectionproxyazureasyncoperation | いいえ | いいえ |
> | locations / privateendpointconnectionproxyoperationresults | いいえ | いいえ |
> | locations / recommendedactionsessionsazureasyncoperation | いいえ | いいえ |
> | locations / recommendedactionsessionsoperationresults | いいえ | いいえ |
> | locations / securityalertpoliciesazureasyncoperation | いいえ | いいえ |
> | locations / securityalertpoliciesoperationresults | いいえ | いいえ |
> | locations / serverkeyazureasyncoperation | いいえ | いいえ |
> | locations / serverkeyoperationresults | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | servergroups | いいえ | いいえ |
> | servers | はい | はい |
> | servers / advisors | いいえ | いいえ |
> | servers / keys | いいえ | いいえ |
> | servers / privateendpointconnectionproxies | いいえ | いいえ |
> | servers / privateendpointconnections | いいえ | いいえ |
> | servers / privatelinkresources | いいえ | いいえ |
> | servers / querytexts | いいえ | いいえ |
> | servers / recoverableservers | いいえ | いいえ |
> | servers / topquerystatistics | いいえ | いいえ |
> | servers / virtualnetworkrules | いいえ | いいえ |
> | servers / waitstatistics | いいえ | いいえ |
> | serversv2 | はい | はい |
> | singleservers | はい | はい |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | artifactsources | はい | はい |
> | operationresults | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | rollouts | はい | はい |
> | servicetopologies | はい | はい |
> | servicetopologies/services | はい | はい |
> | servicetopologies/services/serviceunits | はい | はい |
> | steps | はい | はい |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | applicationgroups | はい | はい |
> | applicationgroups / applications | いいえ | いいえ |
> | applicationgroups / desktops | いいえ | いいえ |
> | applicationgroups / startmenuitems | いいえ | いいえ |
> | hostpools | はい | はい |
> | hostpools / sessionhosts | いいえ | いいえ |
> | hostpools / sessionhosts / usersessions | いいえ | いいえ |
> | hostpools / usersessions | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | workspaces | はい | はい |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | checknameavailability | いいえ | いいえ |
> | checkprovisioningservicenameavailability | いいえ | いいえ |
> | elasticpools | いいえ | いいえ |
> | elasticpools/iothubtenants | いいえ | いいえ |
> | iothubs | はい | はい |
> | iothubs / eventgridfilters | いいえ | いいえ |
> | iothubs / securitysettings | いいえ | いいえ |
> | operationresults | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | provisioningservices | はい | はい |
> | usages | いいえ | いいえ |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | pipelines | はい | はい |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | controllers | はい | はい |
> | controllers / listconnectiondetails | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / checkcontainerhostmapping | いいえ | いいえ |
> | locations / operationresults | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | labcenters | いいえ | いいえ |
> | labs | はい | いいえ |
> | labs/environments | はい | はい |
> | labs/servicerunners | はい | はい |
> | labs/virtualmachines | はい | いいえ |
> | locations | いいえ | いいえ |
> | locations / operations | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | schedules | はい | はい |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | いいえ | いいえ |
> | digitaltwinsinstances / operationresults | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | databaseaccountnames | いいえ | いいえ |
> | databaseaccounts | はい | はい |
> | locations | いいえ | いいえ |
> | locations / deletevirtualnetworkorsubnets | いいえ | いいえ |
> | locations / operationresults | いいえ | いいえ |
> | locations / operationsstatus | いいえ | いいえ |
> | operationresults | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | checkdomainavailability | いいえ | いいえ |
> | domains | はい | はい |
> | domains / domainownershipidentifiers | いいえ | いいえ |
> | generatessorequest | いいえ | いいえ |
> | listdomainrecommendations | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | topleveldomains | いいえ | いいえ |
> | validatedomainregistrationinformation | いいえ | いいえ |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | locations | いいえ | いいえ |
> | locations / operationresults | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | services | はい | はい |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | domains | はい | はい |
> | domains / topics | いいえ | いいえ |
> | eventsubscriptions | いいえ - 個別に移動できませんが、サブスクライブしたリソースで自動的に移動されます。 | いいえ - 個別に移動できませんが、サブスクライブしたリソースで自動的に移動されます。 |
> | extensiontopics | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / eventsubscriptions | いいえ | いいえ |
> | locations / operationresults | いいえ | いいえ |
> | locations / operationsstatus | いいえ | いいえ |
> | locations / topictypes | いいえ | いいえ |
> | operationresults | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | operationsstatus | いいえ | いいえ |
> | partnernamespaces | はい | はい |
> | partnernamespaces / eventchannels | いいえ | いいえ |
> | partnerregistrations | いいえ | いいえ |
> | partnertopics | はい | はい |
> | partnertopics / eventsubscriptions | いいえ | いいえ |
> | systemtopics | はい | はい |
> | systemtopics / eventsubscriptions | いいえ | いいえ |
> | topics | はい | はい |
> | topictypes | いいえ | いいえ |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | availableclusterregions | いいえ | いいえ |
> | checknameavailability | いいえ | いいえ |
> | checknamespaceavailability | いいえ | いいえ |
> | clusters | はい | はい |
> | locations | いいえ | いいえ |
> | locations / deletevirtualnetworkorsubnets | いいえ | いいえ |
> | namespaces | はい | はい |
> | namespaces / authorizationrules | いいえ | いいえ |
> | namespaces / disasterrecoveryconfigs | いいえ | いいえ |
> | namespaces / disasterrecoveryconfigs / checknameavailability | いいえ | いいえ |
> | namespaces / eventhubs | いいえ | いいえ |
> | namespaces / eventhubs / authorizationrules | いいえ | いいえ |
> | namespaces / eventhubs / consumergroups | いいえ | いいえ |
> | namespaces / networkrulesets | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | sku | いいえ | いいえ |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | experimentworkspaces | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / operations | いいえ | いいえ |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | namespaces | はい | はい |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | featureproviders | いいえ | いいえ |
> | features | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | providers | いいえ | いいえ |
> | subscriptionfeatureregistrations | いいえ | いいえ |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | accounts | いいえ | いいえ |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | automanagedaccounts | いいえ | いいえ |
> | automanagedvmconfigurationprofiles | いいえ | いいえ |
> | guestconfigurationassignments | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | software | いいえ | いいえ |
> | softwareupdateprofile | いいえ | いいえ |
> | softwareupdates | いいえ | いいえ |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | hanainstances | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / operations | いいえ | いいえ |
> | locations / operationsstatus | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | sapmonitors | はい | はい |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | dedicatedhsms | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!IMPORTANT]
> HDInsight クラスターは、新しいサブスクリプションまたはリソース グループに移動できます。 ただし、HDInsight クラスターにリンクされているネットワーク リソース (仮想ネットワーク、NIC、ロード バランサーなど) をサブスクリプション間で移動することはできません。 また、クラスターの仮想マシンに接続されている NIC を新しいリソース グループに移動することはできません。
>
> HDInsight クラスターを新しいサブスクリプションに移動するときは、まず、他のリソース (ストレージ アカウントなど) を移動します。 その後、HDInsight クラスターを単独で移動します。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | clusters | はい | ○ |
> | clusters/applications | × | いいえ |
> | clusters / operationresults | × | いいえ |
> | locations | × | いいえ |
> | locations / azureasyncoperations | × | いいえ |
> | locations / billingspecs | × | いいえ |
> | locations / capabilities | × | いいえ |
> | locations / operationresults | いいえ | いいえ |
> | locations / usages | × | いいえ |
> | locations / validatecreaterequest | いいえ | × |
> | operations | × | × |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | checknameavailability | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / operationresults | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | services | はい | はい |
> | services / privateendpointconnections | いいえ | いいえ |
> | services / privatelinkresources | いいえ | いいえ |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | locations | いいえ | いいえ |
> | locations / operationresults | いいえ | いいえ |
> | locations / operationstatus | いいえ | いいえ |
> | machines | はい | はい |
> | machines / extensions | はい | はい |
> | operations | いいえ | いいえ |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | datamanagers | はい | はい |
> | operations | いいえ | いいえ |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | devices | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / operationstatuses | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | vnfs | いいえ | いいえ |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | components | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | networkscopes | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | jobs | はい | はい |
> | locations | いいえ | いいえ |
> | locations / operationresults | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftinsights"></a>microsoft.insights

> [!IMPORTANT]
> 新しいサブスクリプションへの移動によって[サブスクリプション クォータ](azure-subscription-service-limits.md#azure-monitor-limits)を超えないようにします。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | actiongroups | はい | ○ |
> | activitylogalerts | いいえ | × |
> | alertrules | はい | ○ |
> | autoscalesettings | はい | ○ |
> | baseline | いいえ | × |
> | calculatebaseline | いいえ | × |
> | components | はい | はい |
> | components / events | いいえ | × |
> | components / linkedstorageaccounts | いいえ | いいえ |
> | components / metadata | いいえ | × |
> | components / metrics | いいえ | × |
> | components / pricingplans | いいえ | × |
> | components / query | いいえ | × |
> | datacollectionrules | いいえ | × |
> | diagnosticsettings | いいえ | いいえ |
> | diagnosticsettingscategories | いいえ | × |
> | eventcategories | いいえ | × |
> | eventtypes | いいえ | いいえ |
> | extendeddiagnosticsettings | いいえ | いいえ |
> | guestdiagnosticsettings | いいえ | いいえ |
> | listmigrationdate | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / operationresults | いいえ | いいえ |
> | logdefinitions | いいえ | いいえ |
> | logprofiles | いいえ | いいえ |
> | logs | いいえ | いいえ |
> | metricalerts | いいえ | いいえ |
> | metricbaselines | いいえ | いいえ |
> | metricbatch | いいえ | いいえ |
> | metricdefinitions | いいえ | いいえ |
> | metricnamespaces | いいえ | いいえ |
> | metrics | いいえ | いいえ |
> | migratealertrules | いいえ | いいえ |
> | migratetonewpricingmodel | いいえ | いいえ |
> | myworkbooks | いいえ | いいえ |
> | notificationgroups | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | privatelinkscopeoperationstatuses | いいえ | いいえ |
> | privatelinkscopes | いいえ | いいえ |
> | privatelinkscopes / privateendpointconnectionproxies | いいえ | いいえ |
> | privatelinkscopes / privateendpointconnections | いいえ | いいえ |
> | privatelinkscopes / scopedresources | いいえ | いいえ |
> | rollbacktolegacypricingmodel | いいえ | いいえ |
> | scheduledqueryrules | はい | はい |
> | トポロジ | いいえ | いいえ |
> | トランザクション | いいえ | いいえ |
> | vminsightsonboardingstatuses | いいえ | いいえ |
> | webtests | はい | はい |
> | webtests / gettestresultfile | いいえ | いいえ |
> | Workbooks | はい | はい |
> | workbooktemplates | はい | はい |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | apptemplates | いいえ | いいえ |
> | checknameavailability | いいえ | いいえ |
> | checksubdomainavailability | いいえ | いいえ |
> | iotapps | はい | はい |
> | operations | いいえ | いいえ |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | checknameavailability | はい | はい |
> | graph | はい | はい |
> | operations | いいえ | いいえ |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!IMPORTANT]
> ディスクの暗号化に使用されるキー コンテナーは、同じサブスクリプション内のリソース グループに移動したり、サブスクリプション間で移動したりすることはできません。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | checknameavailability | いいえ | いいえ |
> | deletedvaults | いいえ | いいえ |
> | hsmpools | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / deletedvaults | いいえ | いいえ |
> | locations / deletevirtualnetworkorsubnets | いいえ | いいえ |
> | locations / operationresults | いいえ | いいえ |
> | managedhsms | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | vaults | はい | はい |
> | vaults / accesspolicies | いいえ | いいえ |
> | vaults / eventgridfilters | いいえ | いいえ |
> | vaults / secrets | いいえ | いいえ |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | connectedclusters | はい | はい |
> | locations | いいえ | いいえ |
> | locations / operationstatuses | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | registeredsubscriptions | いいえ | いいえ |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | sourcecontrolconfigurations | いいえ | いいえ |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | clusters | はい | はい |
> | clusters / attacheddatabaseconfigurations | いいえ | いいえ |
> | clusters / databases | いいえ | いいえ |
> | clusters / databases / dataconnections | いいえ | いいえ |
> | clusters / databases / eventhubconnections | いいえ | いいえ |
> | clusters / databases / principalassignments | いいえ | いいえ |
> | clusters / principalassignments | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / checknameavailability | いいえ | いいえ |
> | locations / operationresults | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | labaccounts | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / operations | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | users | いいえ | いいえ |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | accounts | いいえ | いいえ |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | accounts | いいえ | いいえ |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | いいえ | いいえ |
> | integrationaccounts | はい | はい |
> | integrationserviceenvironments | はい | いいえ |
> | integrationserviceenvironments / managedapis | はい | いいえ |
> | isolatedenvironments | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / workflows | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | workflows | はい | はい |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | commitmentplans | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / operations | いいえ | いいえ |
> | locations / operationsstatus | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | webservices | はい | いいえ |
> | workspaces | はい | はい |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | いいえ | いいえ |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | accounts | いいえ | いいえ |
> | accounts/workspaces | いいえ | いいえ |
> | accounts/workspaces/projects | いいえ | いいえ |
> | teamaccounts | いいえ | いいえ |
> | teamaccounts/workspaces | いいえ | いいえ |
> | teamaccounts/workspaces/projects | いいえ | いいえ |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | accounts | いいえ | いいえ |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | locations | いいえ | いいえ |
> | locations / computeoperationsstatus | いいえ | いいえ |
> | locations / quotas | いいえ | いいえ |
> | locations / updatequotas | いいえ | いいえ |
> | locations / usages | いいえ | いいえ |
> | locations / vmsizes | いいえ | いいえ |
> | locations / workspaceoperationsstatus | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | workspaces | いいえ | いいえ |
> | workspaces / computes | いいえ | いいえ |
> | workspaces / eventgridfilters | いいえ | いいえ |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | applyupdates | いいえ | いいえ |
> | configurationassignments | いいえ | いいえ |
> | maintenanceconfigurations | はい | はい |
> | updates | いいえ | いいえ |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | ID | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | userassignedidentities | いいえ | いいえ |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | managednetworks | いいえ | いいえ |
> | managednetworks / managednetworkgroups | いいえ | いいえ |
> | managednetworks / managednetworkpeeringpolicies | いいえ | いいえ |
> | 通知 (notification) | いいえ | いいえ |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | marketplaceregistrationdefinitions | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | operationstatuses | いいえ | いいえ |
> | registrationassignments | いいえ | いいえ |
> | registrationdefinitions | いいえ | いいえ |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | checknameavailability | いいえ | いいえ |
> | getentities | いいえ | いいえ |
> | managementgroups | いいえ | いいえ |
> | managementgroups / settings | いいえ | いいえ |
> | operationresults | いいえ | いいえ |
> | operationresults / asyncoperation | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | resources | いいえ | いいえ |
> | starttenantbackfill | いいえ | いいえ |
> | tenantbackfillstatus | いいえ | いいえ |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | accounts | はい | はい |
> | accounts / eventgridfilters | いいえ | いいえ |
> | accounts / privateatlases | はい | はい |
> | operations | いいえ | いいえ |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | listavailableoffers | いいえ | いいえ |
> | offers | いいえ | いいえ |
> | offertypes | いいえ | いいえ |
> | offertypes / publishers | いいえ | いいえ |
> | offertypes / publishers / offers | いいえ | いいえ |
> | offertypes / publishers / offers / plans | いいえ | いいえ |
> | offertypes / publishers / offers / plans / agreements | いいえ | いいえ |
> | offertypes / publishers / offers / plans / configs | いいえ | いいえ |
> | offertypes / publishers / offers / plans / configs / importimage | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | privategalleryitems | いいえ | いいえ |
> | privatestoreclient | いいえ | いいえ |
> | privatestores | いいえ | いいえ |
> | privatestores / offers | いいえ | いいえ |
> | products | いいえ | いいえ |
> | publishers | いいえ | いいえ |
> | publishers / offers | いいえ | いいえ |
> | publishers / offers / amendments | いいえ | いいえ |
> | registrations | いいえ | いいえ |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | classicdevservices | いいえ | いいえ |
> | listcommunicationpreference | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | updatecommunicationpreference | いいえ | いいえ |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | agreements | いいえ | いいえ |
> | offertypes | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | checknameavailability | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / checknameavailability | いいえ | いいえ |
> | mediaservices | はい | はい |
> | mediaservices / accountfilters | いいえ | いいえ |
> | mediaservices / assets | いいえ | いいえ |
> | mediaservices / assets / assetfilters | いいえ | いいえ |
> | mediaservices / contentkeypolicies | いいえ | いいえ |
> | mediaservices / eventgridfilters | いいえ | いいえ |
> | mediaservices / liveeventoperations | いいえ | いいえ |
> | mediaservices/liveevents | はい | はい |
> | mediaservices / liveevents / liveoutputs | いいえ | いいえ |
> | mediaservices / liveoutputoperations | いいえ | いいえ |
> | mediaservices / streamingendpointoperations | いいえ | いいえ |
> | mediaservices/streamingendpoints | はい | はい |
> | mediaservices / streaminglocators | いいえ | いいえ |
> | mediaservices / streamingpolicies | いいえ | いいえ |
> | mediaservices / transforms | いいえ | いいえ |
> | mediaservices / transforms / jobs | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | appclusters | いいえ | いいえ |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / assessmentoptions | いいえ | いいえ |
> | locations / checknameavailability | いいえ | いいえ |
> | migrateprojects | いいえ | いいえ |
> | movecollections | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | projects | いいえ | いいえ |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / checknameavailability | いいえ | いいえ |
> | objectunderstandingaccounts | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | remoterenderingaccounts | はい | はい |
> | spatialanchorsaccounts | はい | はい |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | netappaccounts | いいえ | いいえ |
> | netappaccounts/backuppolicies | いいえ | いいえ |
> | netappaccounts/capacitypools | いいえ | いいえ |
> | netappaccounts/capacitypools/volumes | いいえ | いいえ |
> | netappaccounts/capacitypools/volumes/mounttargets | いいえ | いいえ |
> | netappaccounts/capacitypools/volumes/snapshots | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!IMPORTANT]
> [ネットワークの移動ガイダンス](./move-limitations/networking-move-limitations.md)をご覧ください。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | applicationgatewayavailablerequestheaders | いいえ | いいえ |
> | applicationgatewayavailableresponseheaders | いいえ | いいえ |
> | applicationgatewayavailableservervariables | いいえ | いいえ |
> | applicationgatewayavailablessloptions | いいえ | いいえ |
> | applicationgatewayavailablewafrulesets | いいえ | いいえ |
> | applicationgateways | いいえ | いいえ |
> | applicationgatewaywebapplicationfirewallpolicies | いいえ | いいえ |
> | applicationsecuritygroups | はい | はい |
> | azurefirewallfqdntags | いいえ | いいえ |
> | azurefirewalls | いいえ | いいえ |
> | bastionhosts | いいえ | いいえ |
> | bgpservicecommunities | いいえ | いいえ |
> | checkfrontdoornameavailability | いいえ | いいえ |
> | checktrafficmanagernameavailability | いいえ | いいえ |
> | connections | はい | はい |
> | ddoscustompolicies | はい | はい |
> | ddosprotectionplans | いいえ | いいえ |
> | dnsoperationresults | いいえ | いいえ |
> | dnsoperationstatuses | いいえ | いいえ |
> | dnszones | はい | はい |
> | dnszones/a | いいえ | いいえ |
> | dnszones/aaaa | いいえ | いいえ |
> | dnszones / all | いいえ | いいえ |
> | dnszones / caa | いいえ | いいえ |
> | dnszones/cname | いいえ | いいえ |
> | dnszones/mx | いいえ | いいえ |
> | dnszones / ns | いいえ | いいえ |
> | dnszones/ptr | いいえ | いいえ |
> | dnszones / recordsets | いいえ | いいえ |
> | dnszones / soa | いいえ | いいえ |
> | dnszones/srv | いいえ | いいえ |
> | dnszones/txt | いいえ | いいえ |
> | expressroutecircuits | いいえ | いいえ |
> | expressroutegateways | いいえ | いいえ |
> | expressrouteserviceproviders | いいえ | いいえ |
> | firewallpolicies | はい | はい |
> | frontdooroperationresults | いいえ | いいえ |
> | frontdoors | いいえ | いいえ |
> | frontdoors / frontendendpoints | いいえ | いいえ |
> | frontdoorwebapplicationfirewallmanagedrulesets | いいえ | いいえ |
> | frontdoorwebapplicationfirewallpolicies | いいえ | いいえ |
> | getdnsresourcereference | いいえ | いいえ |
> | internalnotify | いいえ | いいえ |
> | ipallocations | はい | はい |
> | ipgroups | はい | はい |
> | loadbalancers | はい - Basic SKU<br>いいえ - Standard SKU | はい - Basic SKU<br>いいえ - Standard SKU |
> | localnetworkgateways | はい | はい |
> | locations | いいえ | いいえ |
> | locations / autoapprovedprivatelinkservices | いいえ | × |
> | locations / availabledelegations | いいえ | × |
> | locations / availableprivateendpointtypes | いいえ | × |
> | locations / availableservicealiases | いいえ | × |
> | locations / baremetaltenants | いいえ | × |
> | locations / batchnotifyprivateendpointsforresourcemove | いいえ | × |
> | locations / batchvalidateprivateendpointsforresourcemove | いいえ | × |
> | locations / checkacceleratednetworkingsupport | いいえ | × |
> | locations / checkdnsnameavailability | いいえ | × |
> | locations / checkprivatelinkservicevisibility | いいえ | × |
> | locations / commitinternalazurenetworkmanagerconfiguration | いいえ | × |
> | locations / effectiveresourceownership | いいえ | × |
> | locations / nfvoperationresults | いいえ | × |
> | locations / nfvoperations | いいえ | いいえ |
> | locations / operationresults | いいえ | × |
> | locations / operations | いいえ | × |
> | locations / servicetags | いいえ | × |
> | locations / setresourceownership | いいえ | × |
> | locations / supportedvirtualmachinesizes | いいえ | × |
> | locations / usages | いいえ | × |
> | locations / validateresourceownership | いいえ | × |
> | locations / virtualnetworkavailableendpointservices | いいえ | × |
> | natgateways | いいえ | × |
> | networkexperimentprofiles | いいえ | × |
> | networkintentpolicies | はい | ○ |
> | networkinterfaces | はい | ○ |
> | networkprofiles | いいえ | × |
> | networksecuritygroups | はい | はい |
> | networkwatchers | はい | いいえ |
> | networkwatchers/connectionmonitors | ○ | いいえ |
> | networkwatchers/flowlogs | ○ | いいえ |
> | networkwatchers/pingmeshes | はい | いいえ |
> | operations | いいえ | × |
> | p2svpngateways | いいえ | × |
> | privatednsoperationresults | いいえ | × |
> | privatednsoperationstatuses | いいえ | × |
> | privatednszones | はい | ○ |
> | privatednszones / a | いいえ | × |
> | privatednszones / aaaa | いいえ | × |
> | privatednszones / all | いいえ | × |
> | privatednszones / cname | いいえ | × |
> | privatednszones / mx | いいえ | × |
> | privatednszones / ptr | いいえ | × |
> | privatednszones / soa | いいえ | × |
> | privatednszones / srv | いいえ | × |
> | privatednszones / txt | いいえ | × |
> | privatednszones/virtualnetworklinks | はい | はい |
> | privatednszonesinternal | いいえ | × |
> | privateendpointredirectmaps | いいえ | × |
> | privateendpoints | はい | ○ |
> | privatelinkservices | いいえ | × |
> | publicipaddresses | はい - Basic SKU<br>いいえ - Standard SKU | はい - Basic SKU<br>いいえ - Standard SKU |
> | publicipprefixes | はい | ○ |
> | routefilters | いいえ | × |
> | routetables | はい | ○ |
> | securitypartnerproviders | はい | ○ |
> | serviceendpointpolicies | はい | ○ |
> | trafficmanagergeographichierarchies | いいえ | × |
> | trafficmanagerprofiles | はい | ○ |
> | trafficmanagerprofiles / heatmaps | いいえ | × |
> | trafficmanagerusermetricskeys | いいえ | いいえ |
> | virtualhubs | いいえ | × |
> | virtualnetworkgateways | はい | ○ |
> | virtualnetworks | はい | ○ |
> | virtualnetworktaps | いいえ | × |
> | virtualrouters | はい | はい |
> | virtualwans | いいえ | × |
> | vpngateways (仮想 WAN) | いいえ | × |
> | vpnserverconfigurations | いいえ | × |
> | vpnsites (仮想 WAN) | いいえ | × |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | checknameavailability | いいえ | いいえ |
> | checknamespaceavailability | いいえ | いいえ |
> | namespaces | はい | はい |
> | namespaces/notificationhubs | はい | はい |
> | operationresults | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | osnamespaces | はい | はい |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | hypervsites | いいえ | いいえ |
> | importsites | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | serversites | いいえ | いいえ |
> | vmwaresites | いいえ | いいえ |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!IMPORTANT]
> 新しいサブスクリプションへの移行によって、[サブスクリプション クォータ](azure-subscription-service-limits.md#azure-monitor-limits)が超えることがないようにします。
>
> Automation アカウントがリンクされているワークスペースは移動できません。 移動操作を開始する前に、リンクされている Automation アカウントは必ず解除してください。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | clusters | いいえ | いいえ |
> | deletedworkspaces | いいえ | いいえ |
> | linktargets | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / operationstatuses | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | storageinsightconfigs | いいえ | × |
> | workspaces | はい | はい |
> | workspaces / datasources | いいえ | いいえ |
> | workspaces / linkedservices | いいえ | いいえ |
> | workspaces / linkedstorageaccounts | いいえ | いいえ |
> | workspaces / metadata | いいえ | いいえ |
> | workspaces / query | いいえ | いいえ |
> | workspaces / scopedprivatelinkproxies | いいえ | × |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | managementassociations | いいえ | いいえ |
> | managementconfigurations | はい | はい |
> | operations | いいえ | いいえ |
> | solutions | はい | はい |
> | views | はい | はい |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | checkserviceprovideravailability | いいえ | いいえ |
> | legacypeerings | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | peerasns | いいえ | いいえ |
> | peeringlocations | いいえ | いいえ |
> | peerings | いいえ | いいえ |
> | peeringservicecountries | いいえ | いいえ |
> | peeringservicelocations | いいえ | いいえ |
> | peeringserviceproviders | いいえ | いいえ |
> | peeringservices | いいえ | いいえ |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | asyncoperationresults | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | policyevents | いいえ | いいえ |
> | policystates | いいえ | いいえ |
> | policytrackedresources | いいえ | いいえ |
> | remediations | いいえ | いいえ |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | consoles | いいえ | いいえ |
> | dashboards | はい | はい |
> | locations | いいえ | いいえ |
> | locations / consoles | いいえ | いいえ |
> | locations / usersettings | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | usersettings | いいえ | いいえ |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | locations | いいえ | いいえ |
> | locations / checknameavailability | いいえ | いいえ |
> | workspacecollections | はい | はい |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | capacities | はい | はい |
> | locations | いいえ | いいえ |
> | locations / checknameavailability | いいえ | いいえ |
> | locations / operationresults | いいえ | いいえ |
> | locations / operationstatuses | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftpowerplatform"></a>Microsoft.PowerPlatform

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | operations | いいえ | いいえ |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | accounts | いいえ | いいえ |
> | checknameavailability | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | availableaccounts | いいえ | いいえ |
> | providerregistrations | いいえ | いいえ |
> | providerregistrations / resourcetyperegistrations | いいえ | いいえ |
> | rollouts | いいえ | いいえ |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | locations | いいえ | いいえ |
> | locations / operationstatuses | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | workspaces | いいえ | いいえ |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!IMPORTANT]
> [Recovery Services の移動のガイダンス](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)に関する記事をご覧ください。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems | いいえ | × |
> | locations | いいえ | × |
> | locations / allocatedstamp | いいえ | × |
> | locations / allocatestamp | いいえ | × |
> | locations / backupaadproperties | いいえ | × |
> | locations / backupcrossregionrestore | いいえ | × |
> | locations / backupcrrjob | いいえ | × |
> | locations / backupcrrjobs | いいえ | × |
> | locations / backupcrroperationresults | いいえ | × |
> | locations / backupcrroperationsstatus | いいえ | × |
> | locations / backupprevalidateprotection | いいえ | いいえ |
> | locations / backupstatus | いいえ | × |
> | locations / backupvalidatefeatures | いいえ | × |
> | locations / checknameavailability | いいえ | × |
> | operations | いいえ | × |
> | replicationeligibilityresults | いいえ | × |
> | vaults | はい | はい |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | locations | いいえ | いいえ |
> | locations / operationresults | いいえ | いいえ |
> | locations / operationsstatus | いいえ | いいえ |
> | openshiftclusters | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | checknameavailability | いいえ | いいえ |
> | namespaces | はい | はい |
> | namespaces / authorizationrules | いいえ | いいえ |
> | namespaces / hybridconnections | いいえ | いいえ |
> | namespaces / hybridconnections / authorizationrules | いいえ | いいえ |
> | namespaces / privateendpointconnections | いいえ | いいえ |
> | namespaces / wcfrelays | いいえ | いいえ |
> | namespaces / wcfrelays / authorizationrules | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | operations | いいえ | いいえ |
> | Query | はい | はい |
> | resourcechangedetails | いいえ | いいえ |
> | resourcechanges | いいえ | いいえ |
> | resources | いいえ | いいえ |
> | resourceshistory | いいえ | いいえ |
> | subscriptionsstatus | いいえ | いいえ |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | availabilitystatuses | いいえ | いいえ |
> | childavailabilitystatuses | いいえ | いいえ |
> | childresources | いいえ | いいえ |
> | emergingissues | いいえ | いいえ |
> | events | いいえ | いいえ |
> | metadata | いいえ | いいえ |
> | notifications | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | calculatetemplatehash | いいえ | いいえ |
> | checkpolicycompliance | いいえ | いいえ |
> | checkresourcename | いいえ | いいえ |
> | deployments | いいえ | いいえ |
> | deployments / operations | いいえ | いいえ |
> | deploymentscripts | いいえ | いいえ |
> | deploymentscripts / logs | いいえ | いいえ |
> | リンク | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / deploymentscriptoperationresults | いいえ | いいえ |
> | notifyresourcejobs | いいえ | いいえ |
> | operationresults | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | providers | いいえ | いいえ |
> | resourcegroups | いいえ | いいえ |
> | resources | いいえ | いいえ |
> | subscriptions | いいえ | いいえ |
> | subscriptions / locations | いいえ | いいえ |
> | subscriptions / operationresults | いいえ | いいえ |
> | subscriptions / providers | いいえ | いいえ |
> | subscriptions / resourcegroups | いいえ | いいえ |
> | subscriptions / resourcegroups / resources | いいえ | いいえ |
> | subscriptions / resources | いいえ | いいえ |
> | subscriptions / tagnames | いいえ | いいえ |
> | subscriptions / tagnames / tagvalues | いいえ | いいえ |
> | tags | いいえ | いいえ |
> | templatespecs | いいえ | いいえ |
> | templatespecs / versions | いいえ | いいえ |
> | tenants | いいえ | いいえ |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | applications | はい | いいえ |
> | checkmoderneligibility | いいえ | いいえ |
> | checknameavailability | いいえ | いいえ |
> | operationresults | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | saasresources | いいえ | いいえ |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!IMPORTANT]
> 1 回の操作で異なるリージョンにあるいくつかの Search リソースを一度に移動することはできません。 代わりに、別の操作で移動します。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | checknameavailability | × | いいえ |
> | checkservicenameavailability | × | いいえ |
> | operations | × | いいえ |
> | resourcehealthmetadata | いいえ | いいえ |
> | searchservices | はい | はい |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | いいえ | いいえ |
> | advancedthreatprotectionsettings | いいえ | いいえ |
> | alerts | いいえ | いいえ |
> | allowedconnections | いいえ | いいえ |
> | applicationwhitelistings | いいえ | いいえ |
> | assessmentmetadata | いいえ | いいえ |
> | assessments | いいえ | いいえ |
> | autodismissalertsrules | いいえ | いいえ |
> | automations | はい | はい |
> | autoprovisioningsettings | いいえ | いいえ |
> | complianceresults | いいえ | いいえ |
> | compliances | いいえ | いいえ |
> | datacollectionagents | いいえ | いいえ |
> | devicesecuritygroups | いいえ | いいえ |
> | discoveredsecuritysolutions | いいえ | いいえ |
> | externalsecuritysolutions | いいえ | いいえ |
> | informationprotectionpolicies | いいえ | いいえ |
> | iotsecuritysolutions | はい | はい |
> | iotsecuritysolutions / analyticsmodels | いいえ | いいえ |
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | いいえ | いいえ |
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | いいえ | いいえ |
> | jitnetworkaccesspolicies | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / alerts | いいえ | いいえ |
> | locations / allowedconnections | いいえ | いいえ |
> | locations / applicationwhitelistings | いいえ | いいえ |
> | locations / discoveredsecuritysolutions | いいえ | いいえ |
> | locations / externalsecuritysolutions | いいえ | いいえ |
> | locations / jitnetworkaccesspolicies | いいえ | いいえ |
> | locations / securitysolutions | いいえ | いいえ |
> | locations / securitysolutionsreferencedata | いいえ | いいえ |
> | locations / tasks | いいえ | いいえ |
> | locations / topologies | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | policies | いいえ | いいえ |
> | pricings | いいえ | いいえ |
> | regulatorycompliancestandards | いいえ | いいえ |
> | regulatorycompliancestandards / regulatorycompliancecontrols | いいえ | いいえ |
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | いいえ | いいえ |
> | securitycontacts | いいえ | いいえ |
> | securitysolutions | いいえ | いいえ |
> | securitysolutionsreferencedata | いいえ | いいえ |
> | securitystatuses | いいえ | いいえ |
> | securitystatusessummaries | いいえ | いいえ |
> | servervulnerabilityassessments | いいえ | いいえ |
> | settings | いいえ | いいえ |
> | subassessments | いいえ | いいえ |
> | tasks | いいえ | いいえ |
> | topologies | いいえ | いいえ |
> | workspacesettings | いいえ | いいえ |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | aggregations | いいえ | いいえ |
> | alertrules | いいえ | いいえ |
> | alertruletemplates | いいえ | いいえ |
> | automationrules | いいえ | いいえ |
> | bookmarks | いいえ | いいえ |
> | cases | いいえ | いいえ |
> | dataconnectors | いいえ | いいえ |
> | dataconnectorscheckrequirements | いいえ | いいえ |
> | entities | いいえ | いいえ |
> | entityqueries | いいえ | いいえ |
> | incidents | いいえ | いいえ |
> | officeconsents | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | settings | いいえ | いいえ |
> | threatintelligence | いいえ | いいえ |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | consoleservices | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / consoleservices | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | gateways | いいえ | いいえ |
> | nodes | いいえ | いいえ |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | checknameavailability | いいえ | いいえ |
> | checknamespaceavailability | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / deletevirtualnetworkorsubnets | いいえ | いいえ |
> | namespaces | はい | はい |
> | namespaces / authorizationrules | いいえ | いいえ |
> | namespaces / disasterrecoveryconfigs | いいえ | いいえ |
> | namespaces / disasterrecoveryconfigs / checknameavailability | いいえ | いいえ |
> | namespaces / eventgridfilters | いいえ | いいえ |
> | namespaces / networkrulesets | いいえ | いいえ |
> | namespaces / queues | いいえ | いいえ |
> | namespaces / queues / authorizationrules | いいえ | いいえ |
> | namespaces / topics | いいえ | いいえ |
> | namespaces / topics / authorizationrules | いいえ | いいえ |
> | namespaces / topics / subscriptions | いいえ | いいえ |
> | namespaces / topics / subscriptions / rules | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | premiummessagingregions | いいえ | いいえ |
> | sku | いいえ | いいえ |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | applications | いいえ | いいえ |
> | clusters | はい | はい |
> | clusters/applications | いいえ | いいえ |
> | containergroups | いいえ | いいえ |
> | containergroupsets | いいえ | いいえ |
> | edgeclusters | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / clusterversions | いいえ | いいえ |
> | locations / environments | いいえ | いいえ |
> | locations / operationresults | いいえ | いいえ |
> | locations / operations | いいえ | いいえ |
> | managedclusters | いいえ | いいえ |
> | networks | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | secretstores | いいえ | いいえ |
> | volumes | いいえ | いいえ |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | applications | はい | はい |
> | containergroups | いいえ | いいえ |
> | gateways | はい | はい |
> | locations | いいえ | いいえ |
> | locations / applicationoperations | いいえ | いいえ |
> | locations / gatewayoperations | いいえ | いいえ |
> | locations / networkoperations | いいえ | いいえ |
> | locations / secretoperations | いいえ | いいえ |
> | locations / volumeoperations | いいえ | いいえ |
> | networks | はい | はい |
> | operations | いいえ | いいえ |
> | secrets | はい | はい |
> | volumes | はい | はい |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | rollouts | いいえ | いいえ |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | locations | いいえ | いいえ |
> | locations / checknameavailability | いいえ | いいえ |
> | locations / operationresults | いいえ | いいえ |
> | locations / operationstatuses | いいえ | いいえ |
> | locations / usages | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | signalr | はい | はい |
> | signalr / eventgridfilters | いいえ | いいえ |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | いいえ | いいえ |
> | applications | いいえ | いいえ |
> | jitrequests | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / operationstatuses | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!IMPORTANT]
> データベースとサーバーは同じリソース グループ内に存在する必要があります。 SQL Server を移動すると、そのデータベースもすべて移動されます。 この動作は、Azure SQL Database と Azure Synapse Analytics データベースに適用されます。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | checknameavailability | いいえ | いいえ |
> | instancepools | × | いいえ |
> | locations | はい | ○ |
> | locations / administratorazureasyncoperation | いいえ | いいえ |
> | locations / administratoroperationresults | いいえ | いいえ |
> | locations / auditingsettingsazureasyncoperation | いいえ | いいえ |
> | locations / auditingsettingsoperationresults | いいえ | いいえ |
> | locations / capabilities | いいえ | いいえ |
> | locations / databaseazureasyncoperation | いいえ | いいえ |
> | locations / databaseoperationresults | いいえ | いいえ |
> | locations / databaserestoreazureasyncoperation | いいえ | いいえ |
> | locations / deletevirtualnetworkorsubnets | いいえ | いいえ |
> | locations / deletevirtualnetworkorsubnetsazureasyncoperation | いいえ | いいえ |
> | locations / deletevirtualnetworkorsubnetsoperationresults | いいえ | いいえ |
> | locations / dnsaliasasyncoperation | いいえ | いいえ |
> | locations / dnsaliasoperationresults | いいえ | いいえ |
> | locations / elasticpoolazureasyncoperation | いいえ | いいえ |
> | locations / elasticpooloperationresults | いいえ | いいえ |
> | locations / encryptionprotectorazureasyncoperation | いいえ | いいえ |
> | locations / encryptionprotectoroperationresults | いいえ | いいえ |
> | locations / extendedauditingsettingsazureasyncoperation | いいえ | いいえ |
> | locations / extendedauditingsettingsoperationresults | いいえ | いいえ |
> | locations / failovergroupazureasyncoperation | いいえ | いいえ |
> | locations / failovergroupoperationresults | いいえ | いいえ |
> | locations / firewallrulesazureasyncoperation | いいえ | いいえ |
> | locations / firewallrulesoperationresults | いいえ | いいえ |
> | locations / instancefailovergroupazureasyncoperation | いいえ | いいえ |
> | locations / instancefailovergroupoperationresults | いいえ | いいえ |
> | locations / instancefailovergroups | いいえ | いいえ |
> | locations / instancepoolazureasyncoperation | いいえ | いいえ |
> | locations / instancepooloperationresults | いいえ | いいえ |
> | locations / jobagentazureasyncoperation | いいえ | いいえ |
> | locations / jobagentoperationresults | いいえ | いいえ |
> | locations / longtermretentionbackupazureasyncoperation | いいえ | いいえ |
> | locations / longtermretentionbackupoperationresults | いいえ | いいえ |
> | locations / longtermretentionbackups | いいえ | いいえ |
> | locations / longtermretentionmanagedinstancebackupazureasyncoperation | いいえ | いいえ |
> | locations / longtermretentionmanagedinstancebackupoperationresults | いいえ | いいえ |
> | locations / longtermretentionmanagedinstancebackups | いいえ | いいえ |
> | locations / longtermretentionmanagedinstances | いいえ | いいえ |
> | locations / longtermretentionpolicyazureasyncoperation | いいえ | いいえ |
> | locations / longtermretentionpolicyoperationresults | いいえ | いいえ |
> | locations / longtermretentionservers | いいえ | いいえ |
> | locations / manageddatabaseazureasyncoperation | いいえ | いいえ |
> | locations / manageddatabasecompleterestoreazureasyncoperation | いいえ | いいえ |
> | locations / manageddatabasecompleterestoreoperationresults | いいえ | いいえ |
> | locations / manageddatabaseoperationresults | いいえ | いいえ |
> | locations / manageddatabaserestoreazureasyncoperation | いいえ | いいえ |
> | locations / manageddatabaserestoreoperationresults | いいえ | いいえ |
> | locations / managedinstanceazureasyncoperation | いいえ | いいえ |
> | locations / managedinstanceencryptionprotectorazureasyncoperation | いいえ | いいえ |
> | locations / managedinstanceencryptionprotectoroperationresults | いいえ | いいえ |
> | locations / managedinstancekeyazureasyncoperation | いいえ | いいえ |
> | locations / managedinstancekeyoperationresults | いいえ | いいえ |
> | locations / managedinstancelongtermretentionpolicyazureasyncoperation | いいえ | いいえ |
> | locations / managedinstancelongtermretentionpolicyoperationresults | いいえ | いいえ |
> | locations / managedinstanceoperationresults | いいえ | いいえ |
> | locations / managedinstancetdecertazureasyncoperation | いいえ | いいえ |
> | locations / managedinstancetdecertoperationresults | いいえ | いいえ |
> | locations / managedserversecurityalertpoliciesazureasyncoperation | いいえ | いいえ |
> | locations / managedserversecurityalertpoliciesoperationresults | いいえ | いいえ |
> | locations / managedshorttermretentionpolicyazureasyncoperation | いいえ | いいえ |
> | locations / managedshorttermretentionpolicyoperationresults | いいえ | いいえ |
> | locations / notifyazureasyncoperation | いいえ | いいえ |
> | locations / privateendpointconnectionazureasyncoperation | いいえ | いいえ |
> | locations / privateendpointconnectionoperationresults | いいえ | いいえ |
> | locations / privateendpointconnectionproxyazureasyncoperation | いいえ | いいえ |
> | locations / privateendpointconnectionproxyoperationresults | いいえ | いいえ |
> | locations / securityalertpoliciesazureasyncoperation | いいえ | いいえ |
> | locations / securityalertpoliciesoperationresults | いいえ | いいえ |
> | locations / serveradministratorazureasyncoperation | いいえ | いいえ |
> | locations / serveradministratoroperationresults | いいえ | いいえ |
> | locations / serverazureasyncoperation | いいえ | いいえ |
> | locations / serverkeyazureasyncoperation | いいえ | いいえ |
> | locations / serverkeyoperationresults | いいえ | いいえ |
> | locations / serveroperationresults | いいえ | いいえ |
> | locations / shorttermretentionpolicyazureasyncoperation | いいえ | いいえ |
> | locations / shorttermretentionpolicyoperationresults | いいえ | いいえ |
> | locations / syncagentoperationresults | いいえ | いいえ |
> | locations / syncdatabaseids | いいえ | いいえ |
> | locations / syncgroupoperationresults | いいえ | いいえ |
> | locations / syncmemberoperationresults | いいえ | いいえ |
> | locations / tdecertazureasyncoperation | いいえ | いいえ |
> | locations / tdecertoperationresults | いいえ | いいえ |
> | locations / usages | いいえ | いいえ |
> | locations / virtualclusterazureasyncoperation | いいえ | いいえ |
> | locations / virtualclusteroperationresults | いいえ | いいえ |
> | locations / virtualnetworkrulesazureasyncoperation | いいえ | いいえ |
> | locations / virtualnetworkrulesoperationresults | いいえ | いいえ |
> | locations / vulnerabilityassessmentscanazureasyncoperation | いいえ | いいえ |
> | locations / vulnerabilityassessmentscanoperationresults | いいえ | いいえ |
> | managedinstances | いいえ | いいえ |
> | managedinstances / administrators | いいえ | いいえ |
> | managedinstances/databases | いいえ | いいえ |
> | managedinstances / databases / backuplongtermretentionpolicies | いいえ | いいえ |
> | managedinstances / databases / vulnerabilityassessments | いいえ | いいえ |
> | managedinstances / metricdefinitions | いいえ | いいえ |
> | managedinstances / metrics | いいえ | いいえ |
> | managedinstances / recoverabledatabases | いいえ | いいえ |
> | managedinstances / tdecertificates | いいえ | いいえ |
> | managedinstances / vulnerabilityassessments | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | servers | はい | ○ |
> | servers / administratoroperationresults | いいえ | いいえ |
> | servers / administrators | いいえ | いいえ |
> | servers / advisors | いいえ | いいえ |
> | servers / aggregateddatabasemetrics | いいえ | いいえ |
> | servers / auditingpolicies | いいえ | いいえ |
> | servers / auditingsettings | いいえ | いいえ |
> | servers / automatictuning | いいえ | いいえ |
> | servers / communicationlinks | いいえ | いいえ |
> | servers / connectionpolicies | × | × |
> | servers/databases | はい | ○ |
> | servers / databases / advisors | いいえ | いいえ |
> | servers / databases / auditingpolicies | いいえ | いいえ |
> | servers / databases / auditingsettings | いいえ | いいえ |
> | servers / databases / auditrecords | いいえ | いいえ |
> | servers / databases / automatictuning | いいえ | いいえ |
> | servers / databases / backuplongtermretentionpolicies | いいえ | いいえ |
> | servers / databases / backupshorttermretentionpolicies | いいえ | いいえ |
> | servers / databases / connectionpolicies | いいえ | いいえ |
> | servers / databases / datamaskingpolicies | いいえ | いいえ |
> | servers / databases / datamaskingpolicies / rules | いいえ | いいえ |
> | servers / databases / extensions | いいえ | いいえ |
> | servers / databases / geobackuppolicies | いいえ | いいえ |
> | servers / databases / metricdefinitions | いいえ | いいえ |
> | servers / databases / metrics | いいえ | いいえ |
> | servers / databases / recommendedsensitivitylabels | いいえ | いいえ |
> | servers / databases / securityalertpolicies | いいえ | いいえ |
> | servers / databases / syncgroups | いいえ | いいえ |
> | servers / databases / syncgroups / syncmembers | いいえ | いいえ |
> | servers / databases / topqueries | いいえ | いいえ |
> | servers / databases / topqueries / querytext | いいえ | いいえ |
> | servers / databases / transparentdataencryption | いいえ | いいえ |
> | servers / databases / vulnerabilityassessment | いいえ | いいえ |
> | servers / databases / vulnerabilityassessments | いいえ | いいえ |
> | servers / databases / vulnerabilityassessmentscans | いいえ | いいえ |
> | servers / databases / vulnerabilityassessmentsettings | いいえ | いいえ |
> | servers / databases / workloadgroups | いいえ | いいえ |
> | servers / databasesecuritypolicies | いいえ | いいえ |
> | servers / disasterrecoveryconfiguration | いいえ | いいえ |
> | servers / dnsaliases | いいえ | いいえ |
> | servers / elasticpoolestimates | いいえ | いいえ |
> | servers/elasticpools | はい | はい |
> | servers / elasticpools / advisors | いいえ | いいえ |
> | servers / elasticpools / metricdefinitions | いいえ | いいえ |
> | servers / elasticpools / metrics | いいえ | いいえ |
> | servers / encryptionprotector | いいえ | いいえ |
> | servers / extendedauditingsettings | いいえ | いいえ |
> | servers / failovergroups | いいえ | いいえ |
> | servers / import | いいえ | いいえ |
> | servers / importexportoperationresults | × | × |
> | servers/jobaccounts | はい | はい |
> | servers/jobagents | ○ | ○ |
> | servers / jobagents / jobs | いいえ | いいえ |
> | servers / jobagents / jobs / executions | いいえ | いいえ |
> | servers / jobagents / jobs / steps | いいえ | いいえ |
> | servers / keys | いいえ | いいえ |
> | servers / operationresults | いいえ | いいえ |
> | servers / recommendedelasticpools | いいえ | いいえ |
> | servers / recoverabledatabases | いいえ | いいえ |
> | servers / restorabledroppeddatabases | いいえ | いいえ |
> | servers / securityalertpolicies | いいえ | いいえ |
> | servers / serviceobjectives | いいえ | いいえ |
> | servers / syncagents | いいえ | いいえ |
> | servers / tdecertificates | いいえ | いいえ |
> | servers / usages | いいえ | いいえ |
> | servers / virtualnetworkrules | いいえ | いいえ |
> | servers / vulnerabilityassessments | いいえ | × |
> | virtualclusters | はい | はい |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | locations | いいえ | いいえ |
> | locations / availabilitygrouplisteneroperationresults | いいえ | いいえ |
> | locations / operationtypes | いいえ | いいえ |
> | locations / sqlvirtualmachinegroupoperationresults | いいえ | いいえ |
> | locations / sqlvirtualmachineoperationresults | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | sqlvirtualmachinegroups | はい | はい |
> | sqlvirtualmachinegroups / availabilitygrouplisteners | いいえ | いいえ |
> | sqlvirtualmachines | はい | はい |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | checknameavailability | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / asyncoperations | いいえ | いいえ |
> | locations / checknameavailability | いいえ | いいえ |
> | locations / deletevirtualnetworkorsubnets | いいえ | いいえ |
> | locations / usages | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | storageaccounts | はい | はい |
> | storageaccounts / blobservices | いいえ | いいえ |
> | storageaccounts / fileservices | いいえ | いいえ |
> | storageaccounts / listaccountsas | いいえ | いいえ |
> | storageaccounts / listservicesas | いいえ | いいえ |
> | storageaccounts / queueservices | いいえ | いいえ |
> | storageaccounts / services | いいえ | いいえ |
> | storageaccounts / services / metricdefinitions | いいえ | いいえ |
> | storageaccounts / tableservices | いいえ | いいえ |
> | usages | いいえ | いいえ |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | caches | いいえ | いいえ |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | locations | いいえ | いいえ |
> | locations / checknameavailability | いいえ | いいえ |
> | locations / operationresults | いいえ | いいえ |
> | locations / operations | いいえ | いいえ |
> | locations / workflows | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | storagesyncservices | はい | はい |
> | storagesyncservices / registeredservers | いいえ | いいえ |
> | storagesyncservices / syncgroups | いいえ | いいえ |
> | storagesyncservices / syncgroups / cloudendpoints | いいえ | いいえ |
> | storagesyncservices / syncgroups / serverendpoints | いいえ | いいえ |
> | storagesyncservices / workflows | いいえ | いいえ |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | いいえ | いいえ |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | いいえ | いいえ |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | managers | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!IMPORTANT]
> 実行中状態の Stream Analytics ジョブは移動できません。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | clusters | いいえ | × |
> | locations | いいえ | × |
> | locations / quotas | いいえ | × |
> | operations | いいえ | × |
> | streamingjobs | はい | ○ |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | 環境 | いいえ | いいえ |
> | environments/eventsources | いいえ | いいえ |
> | instances | いいえ | いいえ |
> | instances/environments | いいえ | いいえ |
> | instances/environments/eventsources | いいえ | いいえ |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | cancel | いいえ | いいえ |
> | createsubscription | いいえ | いいえ |
> | 有効化 (enable) | いいえ | いいえ |
> | operationresults | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | rename | いいえ | いいえ |
> | subscriptiondefinitions | いいえ | いいえ |
> | subscriptionoperations | いいえ | いいえ |
> | subscriptions | いいえ | いいえ |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | checknameavailability | いいえ | いいえ |
> | operationresults | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | operationsstatus | いいえ | いいえ |
> | services | いいえ | いいえ |
> | services / problemclassifications | いいえ | いいえ |
> | supporttickets | いいえ | いいえ |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | checknameavailability | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | workspaces | はい | はい |
> | workspaces / bigdatapools | はい | はい |
> | workspaces / operationresults | いいえ | いいえ |
> | workspaces / operationstatuses | いいえ | いいえ |
> | workspaces / sqlpools | はい | はい |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | 環境 | はい | はい |
> | environments / accesspolicies | いいえ | いいえ |
> | environments/eventsources | はい | はい |
> | environments/referencedatasets | はい | はい |
> | operations | いいえ | いいえ |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | stores | はい | はい |
> | stores / accesspolicies | いいえ | いいえ |
> | stores / services | いいえ | いいえ |
> | stores / services / tokens | いいえ | いいえ |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | imagetemplates | いいえ | いいえ |
> | imagetemplates / runoutputs | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / operations | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!IMPORTANT]
> Azure DevOps のサブスクリプションを変更するには、[change the Azure subscription used for billing (課金に使用される Azure サブスクリプションの変更)](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json) に関する記事をご覧ください。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | account | いいえ | × |
> | account / extension | いいえ | × |
> | account/project | いいえ | × |
> | checknameavailability | いいえ | × |
> | operations | いいえ | いいえ |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | arczones | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / operationstatuses | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | resourcepools | いいえ | いいえ |
> | vcenters | いいえ | いいえ |
> | virtualmachines | いいえ | いいえ |
> | virtualmachinetemplates | いいえ | いいえ |
> | virtualnetworks | いいえ | いいえ |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | いいえ | いいえ |
> | dedicatedcloudservices | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / availabilities | いいえ | いいえ |
> | locations / operationresults | いいえ | いいえ |
> | locations / privateclouds | いいえ | いいえ |
> | locations / privateclouds / resourcepools | いいえ | いいえ |
> | locations / privateclouds / virtualmachinetemplates | いいえ | いいえ |
> | locations / privateclouds / virtualnetworks | いいえ | いいえ |
> | locations / usages | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | virtualmachines | いいえ | いいえ |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | devices | いいえ | いいえ |
> | locations | いいえ | いいえ |
> | locations / operationstatuses | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | vnfs | いいえ | いいえ |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | accounts | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | plans | いいえ | いいえ |
> | registeredsubscriptions | いいえ | いいえ |

## <a name="microsoftweb"></a>Microsoft.Web

> [!IMPORTANT]
> [App Service move guidance (App Service の移動のガイダンス)](./move-limitations/app-service-move-limitations.md) に関する記事をご覧ください。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | availablestacks | いいえ | × |
> | billingmeters | いいえ | × |
> | certificates | いいえ | [はい] |
> | checknameavailability | いいえ | × |
> | connectiongateways | はい | ○ |
> | connections | はい | ○ |
> | customapis | はい | ○ |
> | deletedsites | いいえ | × |
> | deploymentlocations | いいえ | × |
> | georegions | いいえ | × |
> | hostingenvironments | いいえ | × |
> | hostingenvironments / eventgridfilters | いいえ | × |
> | hostingenvironments / multirolepools | いいえ | × |
> | hostingenvironments / workerpools | いいえ | × |
> | ishostingenvironmentnameavailable | いいえ | × |
> | ishostnameavailable | いいえ | いいえ |
> | isusernameavailable | いいえ | × |
> | kubeenvironments | はい | ○ |
> | listsitesassignedtohostname | いいえ | × |
> | locations | いいえ | いいえ |
> | locations / apioperations | いいえ | いいえ |
> | locations / connectiongatewayinstallations | いいえ | いいえ |
> | locations / deletedsites | いいえ | いいえ |
> | locations / deletevirtualnetworkorsubnets | いいえ | いいえ |
> | locations / extractapidefinitionfromwsdl | いいえ | いいえ |
> | locations / getnetworkpolicies | いいえ | いいえ |
> | locations / listwsdlinterfaces | いいえ | いいえ |
> | locations / managedapis | いいえ | いいえ |
> | locations / operationresults | いいえ | いいえ |
> | locations / operations | いいえ | いいえ |
> | locations / runtimes | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | publishingusers | いいえ | いいえ |
> | recommendations | いいえ | いいえ |
> | resourcehealthmetadata | いいえ | いいえ |
> | runtimes | いいえ | いいえ |
> | serverfarms | はい | はい |
> | serverfarms / eventgridfilters | いいえ | いいえ |
> | sites | はい | はい |
> | sites / eventgridfilters | いいえ | いいえ |
> | sites / hostnamebindings | いいえ | いいえ |
> | sites / networkconfig | いいえ | いいえ |
> | sites/premieraddons | はい | はい |
> | sites/slots | はい | はい |
> | sites / slots / eventgridfilters | いいえ | いいえ |
> | sites / slots / hostnamebindings | いいえ | いいえ |
> | sites / slots / networkconfig | いいえ | いいえ |
> | sourcecontrols | いいえ | いいえ |
> | staticsites | いいえ | いいえ |
> | validate | いいえ | いいえ |
> | verifyhostingenvironmentvnet | いいえ | いいえ |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | locations | いいえ | いいえ |
> | locations / operationstatuses | いいえ | いいえ |
> | multipleactivationkeys | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | deviceservices | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | locations | いいえ | いいえ |
> | locations / operationstatuses | いいえ | いいえ |
> | operations | いいえ | いいえ |
> | workloads | いいえ | いいえ |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | components | いいえ | いいえ |
> | componentssummary | いいえ | いいえ |
> | monitorinstances | いいえ | いいえ |
> | monitorinstancessummary | いいえ | いいえ |
> | monitors | いいえ | いいえ |
> | notificationsettings | いいえ | いいえ |
> | operations | いいえ | いいえ |

## <a name="third-party-services"></a>サード パーティーのサービス

サード パーティのサービスは現在、移動操作をサポートしていません。

## <a name="next-steps"></a>次のステップ
リソースの移動のコマンドについては、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](move-resource-group-and-subscription.md)」を参照してください。

コンマ区切りの値のファイルと同じデータを取得するには、[move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv) をダウンロードします。
