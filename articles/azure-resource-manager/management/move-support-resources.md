---
title: リソースの種類別の移動操作のサポート
description: 新しいリソース グループまたはサブスクリプションに移動できる Azure リソースの種類を一覧表示します。
ms.topic: conceptual
ms.date: 01/11/2021
ms.openlocfilehash: ae59bcc65f296a10c68b88f5f6a1ecbc923f8856
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2021
ms.locfileid: "103232598"
---
# <a name="move-operation-support-for-resources"></a>リソースの操作のサポートの移動

この記事では、Azure リソースの種類は、移動操作をサポートしているかどうかを示します。 また、リソースを移動するときに考慮すべき特別な条件に関する情報も提供します。

> [!IMPORTANT]
> ほとんどの場合、親リソースから独立して子リソースを移動することはできません。 子リソースのリソースの種類は、`<resource-provider-namespace>/<parent-resource>/<child-resource>` という形式です。 たとえば、`Microsoft.ServiceBus/namespaces/queues` は `Microsoft.ServiceBus/namespaces` の子リソースです。 親リソースを移動すると、子リソースもそれと一緒に自動的に移動されます。 この記事で子リソースが示されていない場合は、親リソースと共に移動されるものと見なすことができます。 親リソースで移動がサポートされていない場合、子リソースを移動することはできません。

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
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | domainservices | いいえ | いいえ |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | diagnosticsettings | いいえ | いいえ |
> | diagnosticsettingscategories | いいえ | いいえ |
> | privatelinkforazuread | はい | はい |
> | tenants | はい | はい |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
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
> | smartdetectoralertrules | はい | はい |
> | smartgroups | いいえ | いいえ |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | servers | はい | はい |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!IMPORTANT]
> 従量課金 SKU に設定されている API Management サービスは移動できません。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | reportfeedback | いいえ | いいえ |
> | サービス (service) | はい | はい |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | configurationstores | はい | はい |
> | configurationstores / eventgridfilters | いいえ | いいえ |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | spring | はい | はい |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!IMPORTANT]
> [App Service move guidance (App Service の移動のガイダンス)](./move-limitations/app-service-move-limitations.md) に関する記事をご覧ください。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | apiapps | いいえ | いいえ |
> | appidentities | いいえ | いいえ |
> | gateways | いいえ | いいえ |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | attestationproviders | はい | はい |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | classicadministrators | いいえ | いいえ |
> | dataaliases | いいえ | いいえ |
> | denyassignments | いいえ | いいえ |
> | elevateaccess | いいえ | いいえ |
> | findorphanroleassignments | いいえ | いいえ |
> | locks | いいえ | いいえ |
> | 権限 | いいえ | いいえ |
> | policyassignments | いいえ | いいえ |
> | policydefinitions | いいえ | いいえ |
> | policysetdefinitions | いいえ | いいえ |
> | privatelinkassociations | いいえ | いいえ |
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
> | automationaccounts | はい | はい |
> | automationaccounts/configurations | はい | はい |
> | automationaccounts/runbooks | はい | はい |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | privateclouds | はい | はい |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | はい | はい |
> | b2ctenants | いいえ | いいえ |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | datacontrollers | いいえ | いいえ |
> | hybriddatamanagers | いいえ | いいえ |
> | postgresinstances | いいえ | いいえ |
> | sqlinstances | いいえ | いいえ |
> | sqlmanagedinstances | いいえ | いいえ |
> | sqlserverinstances | いいえ | いいえ |
> | sqlserverregistrations | はい | はい |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | cloudmanifestfiles | いいえ | いいえ |
> | registrations | はい | はい |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | clusters | いいえ | いいえ |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | batchaccounts | はい | はい |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | billingaccounts | いいえ | いいえ |
> | billingperiods | いいえ | いいえ |
> | billingpermissions | いいえ | いいえ |
> | billingproperty | いいえ | いいえ |
> | billingroleassignments | いいえ | いいえ |
> | billingroledefinitions | いいえ | いいえ |
> | departments | いいえ | いいえ |
> | enrollmentaccounts | いいえ | いいえ |
> | invoices | いいえ | いいえ |
> | transfers | いいえ | いいえ |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | mapapis | いいえ | いいえ |

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
> | watchers | いいえ | いいえ |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | tokenservices | いいえ | いいえ |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | いいえ | いいえ |
> | blueprints | いいえ | いいえ |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | botservices | はい | はい |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!IMPORTANT]
> 仮想ネットワークを使用して Azure Cache for Redis インスタンスが構成されている場合、インスタンスを別のサブスクリプションに移動させることはできません。 「[ネットワーク リソースの移動ガイダンス](./move-limitations/networking-move-limitations.md)」を参照してください。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | redis | はい | はい |
> | redisenterprise | いいえ | いいえ |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | appliedreservations | いいえ | いいえ |
> | calculateexchange | いいえ | いいえ |
> | calculateprice | いいえ | いいえ |
> | calculatepurchaseprice | いいえ | いいえ |
> | catalogs | いいえ | いいえ |
> | commercialreservationorders | いいえ | いいえ |
> | exchange | いいえ | いいえ |
> | reservationorders | いいえ | いいえ |
> | reservations | いいえ | いいえ |
> | resources | いいえ | いいえ |
> | validatereservationorder | いいえ | いいえ |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallmanagedrulesets | いいえ | いいえ |
> | cdnwebapplicationfirewallpolicies | はい | はい |
> | edgenodes | いいえ | いいえ |
> | profiles | はい | はい |
> | profiles/endpoints | はい | はい |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!IMPORTANT]
> [App Service move guidance (App Service の移動のガイダンス)](./move-limitations/app-service-move-limitations.md) に関する記事をご覧ください。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | certificateorders | はい | はい |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!IMPORTANT]
> [Classic deployment move guidance (クラシック デプロイの移動のガイダンス)](./move-limitations/classic-model-move-limitations.md) に関する記事をご覧ください。 クラシック デプロイのリソースは、そのシナリオに固有の操作を使用して、サブスクリプション間で移動できます。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | capabilities | いいえ | いいえ |
> | domainnames | はい | いいえ |
> | quotas | いいえ | いいえ |
> | resourcetypes | いいえ | いいえ |
> | validatesubscriptionmoveavailability | いいえ | いいえ |
> | virtualmachines | はい | はい |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!IMPORTANT]
> [Classic deployment move guidance (クラシック デプロイの移動のガイダンス)](./move-limitations/classic-model-move-limitations.md) に関する記事をご覧ください。 クラシック デプロイのリソースは、そのシナリオに固有の操作を使用して、サブスクリプション間で移動できます。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | classicinfrastructureresources | いいえ | いいえ |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!IMPORTANT]
> [Classic deployment move guidance (クラシック デプロイの移動のガイダンス)](./move-limitations/classic-model-move-limitations.md) に関する記事をご覧ください。 クラシック デプロイのリソースは、そのシナリオに固有の操作を使用して、サブスクリプション間で移動できます。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | capabilities | いいえ | いいえ |
> | expressroutecrossconnections | いいえ | いいえ |
> | expressroutecrossconnections / peerings | いいえ | いいえ |
> | gatewaysupporteddevices | いいえ | いいえ |
> | networksecuritygroups | いいえ | いいえ |
> | quotas | いいえ | いいえ |
> | reservedips | いいえ | いいえ |
> | virtualnetworks | いいえ | いいえ |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!IMPORTANT]
> [Classic deployment move guidance (クラシック デプロイの移動のガイダンス)](./move-limitations/classic-model-move-limitations.md) に関する記事をご覧ください。 クラシック デプロイのリソースは、そのシナリオに固有の操作を使用して、サブスクリプション間で移動できます。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | disks | いいえ | いいえ |
> | images | いいえ | いいえ |
> | osimages | いいえ | いいえ |
> | osplatformimages | いいえ | いいえ |
> | publicimages | いいえ | いいえ |
> | quotas | いいえ | いいえ |
> | storageaccounts | はい | いいえ |
> | vmimages | いいえ | × |

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!IMPORTANT]
> [Classic deployment move guidance (クラシック デプロイの移動のガイダンス)](./move-limitations/classic-model-move-limitations.md) に関する記事をご覧ください。 クラシック デプロイのリソースは、そのシナリオに固有の操作を使用して、サブスクリプション間で移動できます。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | operations | いいえ | いいえ |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | accounts | はい | はい |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | ratecard | いいえ | いいえ |
> | usageaggregates | いいえ | いいえ |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!IMPORTANT]
> [Virtual Machines move guidance (仮想マシンの移動のガイダンス)](./move-limitations/virtual-machines-move-limitations.md) に関する記事をご覧ください。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | availabilitysets | はい | はい |
> | diskaccesses | いいえ | いいえ |
> | diskencryptionsets | いいえ | いいえ |
> | disks | はい | はい |
> | galleries | いいえ | いいえ |
> | galleries/images | いいえ | いいえ |
> | galleries/images/versions | いいえ | いいえ |
> | hostgroups | いいえ | いいえ |
> | hostgroups/hosts | いいえ | いいえ |
> | images | はい | はい |
> | proximityplacementgroups | はい | はい |
> | restorepointcollections | いいえ | いいえ |
> | restorepointcollections / restorepoints | いいえ | いいえ |
> | sharedvmextensions | いいえ | いいえ |
> | sharedvmimages | いいえ | いいえ |
> | sharedvmimages/versions | いいえ | いいえ |
> | スナップショット | はい | はい |
> | sshpublickeys | いいえ | いいえ |
> | virtualmachines | はい | はい |
> | virtualmachines/extensions | はい | はい |
> | virtualmachinescalesets | はい | はい |

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
> | serviceassociationlinks | いいえ | いいえ |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | registries | はい | はい |
> | registries / agentpools | はい | はい |
> | registries/buildtasks | はい | はい |
> | registries/replications | はい | はい |
> | registries/tasks | はい | はい |
> | registries/webhooks | はい | はい |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | containerservices | いいえ | いいえ |
> | managedclusters | いいえ | いいえ |
> | openshiftmanagedclusters | いいえ | いいえ |

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
> | forecast | いいえ | いいえ |
> | query | いいえ | いいえ |
> | registrations | いいえ | いいえ |
> | reportconfigs | いいえ | いいえ |
> | reports | いいえ | いいえ |
> | settings | いいえ | いいえ |
> | showbackrules | いいえ | いいえ |
> | views | いいえ | いいえ |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | hubs | いいえ | いいえ |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | requests | いいえ | いいえ |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | associations | いいえ | いいえ |
> | resourceproviders | はい | はい |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | jobs | いいえ | いいえ |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | availableskus | いいえ | いいえ |
> | databoxedgedevices | いいえ | いいえ |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | workspaces | いいえ | いいえ |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | catalogs | はい | はい |
> | datacatalogs | いいえ | いいえ |

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
> | datafactories | はい | はい |
> | factories | はい | はい |

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

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | accounts | はい | はい |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | services | いいえ | いいえ |
> | services/projects | いいえ | いいえ |
> | slots | いいえ | いいえ |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | backupvaults | いいえ | いいえ |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | accounts | はい | はい |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | servers | はい | はい |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | flexibleServers | はい | はい |
> | servers | はい | はい |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | flexibleServers | いいえ | いいえ |
> | servergroups | いいえ | いいえ |
> | servers | はい | はい |
> | serversv2 | はい | はい |
> | singleservers | はい | はい |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | artifactsources | はい | はい |
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
> | hostpools | はい | はい |
> | workspaces | はい | はい |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | elasticpools | いいえ | いいえ |
> | elasticpools/iothubtenants | いいえ | いいえ |
> | iothubs | はい | はい |
> | provisioningservices | はい | はい |

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

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | labcenters | いいえ | いいえ |
> | labs | はい | いいえ |
> | labs/environments | はい | はい |
> | labs/servicerunners | はい | はい |
> | labs/virtualmachines | はい | いいえ |
> | schedules | はい | はい |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | いいえ | いいえ |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | databaseaccountnames | いいえ | いいえ |
> | databaseaccounts | はい | はい |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | domains | はい | はい |
> | generatessorequest | いいえ | いいえ |
> | topleveldomains | いいえ | いいえ |
> | validatedomainregistrationinformation | いいえ | いいえ |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | services | はい | はい |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | domains | はい | はい |
> | eventsubscriptions | いいえ - 個別に移動できませんが、サブスクライブしたリソースで自動的に移動されます。 | いいえ - 個別に移動できませんが、サブスクライブしたリソースで自動的に移動されます。 |
> | extensiontopics | いいえ | いいえ |
> | partnernamespaces | はい | はい |
> | partnerregistrations | いいえ | いいえ |
> | partnertopics | はい | はい |
> | systemtopics | はい | はい |
> | topics | はい | はい |
> | topictypes | いいえ | いいえ |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | clusters | はい | はい |
> | namespaces | はい | はい |
> | sku | いいえ | いいえ |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | experimentworkspaces | いいえ | いいえ |

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
> | software | いいえ | いいえ |
> | softwareupdateprofile | いいえ | いいえ |
> | softwareupdates | いいえ | いいえ |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | hanainstances | いいえ | いいえ |
> | sapmonitors | いいえ | いいえ |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | dedicatedhsms | いいえ | いいえ |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!IMPORTANT]
> HDInsight クラスターは、新しいサブスクリプションまたはリソース グループに移動できます。 ただし、HDInsight クラスターにリンクされているネットワーク リソース (仮想ネットワーク、NIC、ロード バランサーなど) をサブスクリプション間で移動することはできません。 また、クラスターの仮想マシンに接続されている NIC を新しいリソース グループに移動することはできません。
>
> HDInsight クラスターを新しいサブスクリプションに移動するときは、まず、他のリソース (ストレージ アカウントなど) を移動します。 その後、HDInsight クラスターを単独で移動します。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | clusters | はい | はい |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | services | はい | はい |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | machines | はい | はい |
> | machines / extensions | はい | はい |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | datamanagers | はい | はい |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | devices | いいえ | いいえ |
> | vnfs | いいえ | いいえ |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | components | いいえ | いいえ |
> | networkscopes | いいえ | いいえ |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | jobs | はい | はい |

## <a name="microsoftinsights"></a>microsoft.insights

> [!IMPORTANT]
> 新しいサブスクリプションへの移動によって[サブスクリプション クォータ](azure-subscription-service-limits.md#azure-monitor-limits)を超えないようにします。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | actiongroups | はい | はい |
> | activitylogalerts | いいえ | いいえ |
> | alertrules | はい | はい |
> | autoscalesettings | はい | はい |
> | baseline | いいえ | いいえ |
> | components | はい | はい |
> | datacollectionrules | いいえ | いいえ |
> | diagnosticsettings | いいえ | いいえ |
> | diagnosticsettingscategories | いいえ | いいえ |
> | eventcategories | いいえ | いいえ |
> | eventtypes | いいえ | いいえ |
> | extendeddiagnosticsettings | いいえ | いいえ |
> | guestdiagnosticsettings | いいえ | いいえ |
> | listmigrationdate | いいえ | いいえ |
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
> | privatelinkscopes | いいえ | いいえ |
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
> | iotapps | はい | はい |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | graph | はい | はい |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!IMPORTANT]
> ディスクの暗号化に使用されるキー コンテナーは、同じサブスクリプション内のリソース グループに移動したり、サブスクリプション間で移動したりすることはできません。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | deletedvaults | いいえ | いいえ |
> | hsmpools | いいえ | いいえ |
> | managedhsms | いいえ | いいえ |
> | vaults | はい | はい |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | connectedclusters | はい | はい |
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

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | labaccounts | いいえ | いいえ |
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
> | workflows | はい | はい |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | commitmentplans | いいえ | いいえ |
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
> | teamaccounts | いいえ | いいえ |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | accounts | いいえ | いいえ |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | workspaces | いいえ | いいえ |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | configurationassignments | いいえ | いいえ |
> | maintenanceconfigurations | はい | はい |
> | updates | いいえ | いいえ |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | ID | いいえ | いいえ |
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
> | registrationassignments | いいえ | いいえ |
> | registrationdefinitions | いいえ | いいえ |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | getentities | いいえ | いいえ |
> | managementgroups | いいえ | いいえ |
> | managementgroups / settings | いいえ | いいえ |
> | resources | いいえ | いいえ |
> | starttenantbackfill | いいえ | いいえ |
> | tenantbackfillstatus | いいえ | いいえ |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | accounts | はい | はい |
> | accounts / privateatlases | はい | はい |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | offers | いいえ | いいえ |
> | offertypes | いいえ | いいえ |
> | privategalleryitems | いいえ | いいえ |
> | privatestoreclient | いいえ | いいえ |
> | privatestores | いいえ | いいえ |
> | products | いいえ | いいえ |
> | publishers | いいえ | いいえ |
> | registrations | いいえ | いいえ |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | classicdevservices | いいえ | いいえ |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | agreements | いいえ | いいえ |
> | offertypes | いいえ | いいえ |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | mediaservices | はい | はい |
> | mediaservices/liveevents | はい | はい |
> | mediaservices/streamingendpoints | はい | はい |

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
> | migrateprojects | いいえ | いいえ |
> | movecollections | いいえ | いいえ |
> | projects | いいえ | いいえ |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | いいえ | いいえ |
> | objectunderstandingaccounts | いいえ | いいえ |
> | remoterenderingaccounts | はい | はい |
> | spatialanchorsaccounts | はい | はい |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | netappaccounts | いいえ | いいえ |
> | netappaccounts/capacitypools | いいえ | いいえ |
> | netappaccounts/capacitypools/volumes | いいえ | いいえ |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!IMPORTANT]
> [ネットワークの移動ガイダンス](./move-limitations/networking-move-limitations.md)をご覧ください。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | applicationgateways | いいえ | いいえ |
> | applicationgatewaywebapplicationfirewallpolicies | いいえ | いいえ |
> | applicationsecuritygroups | はい | はい |
> | azurefirewalls | いいえ | いいえ |
> | bastionhosts | いいえ | いいえ |
> | bgpservicecommunities | いいえ | いいえ |
> | connections | はい | はい |
> | ddoscustompolicies | はい | はい |
> | ddosprotectionplans | いいえ | いいえ |
> | dnszones | はい | はい |
> | expressroutecircuits | いいえ | いいえ |
> | expressroutegateways | いいえ | いいえ |
> | expressrouteserviceproviders | いいえ | いいえ |
> | firewallpolicies | はい | はい |
> | frontdoors | いいえ | いいえ |
> | ipallocations | はい | はい |
> | ipgroups | はい | はい |
> | loadbalancers | はい - Basic SKU<br> はい - Standard SKU | はい - Basic SKU<br>いいえ - Standard SKU |
> | localnetworkgateways | はい | はい |
> | natgateways | いいえ | いいえ |
> | networkexperimentprofiles | いいえ | いいえ |
> | networkintentpolicies | はい | はい |
> | networkinterfaces | はい | はい |
> | networkprofiles | いいえ | いいえ |
> | networksecuritygroups | はい | はい |
> | networkwatchers | いいえ | いいえ |
> | networkwatchers/connectionmonitors | はい | いいえ |
> | networkwatchers/flowlogs | はい | いいえ |
> | networkwatchers/pingmeshes | はい | いいえ |
> | p2svpngateways | いいえ | いいえ |
> | privatednszones | はい | はい |
> | privatednszones/virtualnetworklinks | はい | はい |
> | privatednszonesinternal | いいえ | いいえ |
> | privateendpointredirectmaps | いいえ | いいえ |
> | privateendpoints | いいえ | いいえ |
> | privatelinkservices | いいえ | いいえ |
> | publicipaddresses | はい - Basic SKU<br>はい - Standard SKU | はい - Basic SKU<br>いいえ - Standard SKU |
> | publicipprefixes | はい | はい |
> | routefilters | いいえ | いいえ |
> | routetables | はい | はい |
> | securitypartnerproviders | はい | はい |
> | serviceendpointpolicies | はい | はい |
> | trafficmanagergeographichierarchies | いいえ | いいえ |
> | trafficmanagerprofiles | はい | はい |
> | trafficmanagerprofiles / heatmaps | いいえ | いいえ |
> | trafficmanagerusermetricskeys | いいえ | いいえ |
> | virtualhubs | いいえ | いいえ |
> | virtualnetworkgateways | はい | はい |
> | virtualnetworks | はい | はい |
> | virtualnetworktaps | いいえ | いいえ |
> | virtualrouters | はい | はい |
> | virtualwans | いいえ | いいえ |
> | vpngateways (仮想 WAN) | いいえ | いいえ |
> | vpnserverconfigurations | いいえ | いいえ |
> | vpnsites (仮想 WAN) | いいえ | いいえ |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | namespaces | はい | はい |
> | namespaces/notificationhubs | はい | はい |

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
> | storageinsightconfigs | いいえ | いいえ |
> | workspaces | はい | はい |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | managementassociations | いいえ | いいえ |
> | managementconfigurations | はい | はい |
> | solutions | はい | はい |
> | views | はい | はい |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | legacypeerings | いいえ | いいえ |
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
> | usersettings | いいえ | いいえ |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | workspacecollections | はい | はい |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | capacities | はい | はい |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | accounts | いいえ | いいえ |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | availableaccounts | いいえ | いいえ |
> | providerregistrations | いいえ | いいえ |
> | rollouts | いいえ | いいえ |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | workspaces | いいえ | いいえ |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!IMPORTANT]
> [Recovery Services の移動のガイダンス](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)に関する記事をご覧ください。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | replicationeligibilityresults | いいえ | いいえ |
> | vaults | はい | はい |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | openshiftclusters | いいえ | いいえ |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | namespaces | はい | はい |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
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
> | childresources | いいえ | いいえ |
> | emergingissues | いいえ | いいえ |
> | events | いいえ | いいえ |
> | metadata | いいえ | いいえ |
> | notifications | いいえ | いいえ |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | deployments | いいえ | いいえ |
> | deploymentscripts | いいえ | いいえ |
> | deploymentscripts / logs | いいえ | いいえ |
> | リンク | いいえ | いいえ |
> | providers | いいえ | いいえ |
> | resourcegroups | いいえ | いいえ |
> | resources | いいえ | いいえ |
> | subscriptions | いいえ | いいえ |
> | tags | いいえ | いいえ |
> | templatespecs | いいえ | いいえ |
> | templatespecs / versions | いいえ | いいえ |
> | tenants | いいえ | いいえ |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | applications | はい | いいえ |
> | saasresources | いいえ | いいえ |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!IMPORTANT]
> 1 回の操作で異なるリージョンにあるいくつかの Search リソースを一度に移動することはできません。 代わりに、別の操作で移動します。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
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
> | entities | いいえ | いいえ |
> | entityqueries | いいえ | いいえ |
> | incidents | いいえ | いいえ |
> | officeconsents | いいえ | いいえ |
> | settings | いいえ | いいえ |
> | threatintelligence | いいえ | いいえ |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | consoleservices | いいえ | いいえ |

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
> | namespaces | はい | はい |
> | premiummessagingregions | いいえ | いいえ |
> | sku | いいえ | いいえ |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | applications | いいえ | いいえ |
> | clusters | はい | はい |
> | containergroups | いいえ | いいえ |
> | containergroupsets | いいえ | いいえ |
> | edgeclusters | いいえ | いいえ |
> | managedclusters | いいえ | いいえ |
> | networks | いいえ | いいえ |
> | secretstores | いいえ | いいえ |
> | volumes | いいえ | いいえ |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | applications | はい | はい |
> | containergroups | いいえ | いいえ |
> | gateways | はい | はい |
> | networks | はい | はい |
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
> | signalr | はい | はい |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | いいえ | いいえ |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | いいえ | いいえ |
> | applications | いいえ | いいえ |
> | jitrequests | いいえ | いいえ |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!IMPORTANT]
> データベースとサーバーは同じリソース グループ内に存在する必要があります。 SQL Server を移動すると、そのデータベースもすべて移動されます。 この動作は、Azure SQL Database と Azure Synapse Analytics データベースに適用されます。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | instancepools | いいえ | いいえ |
> | locations | はい | はい |
> | managedinstances | いいえ | いいえ |
> | servers | はい | はい |
> | servers/databases | はい | はい |
> | servers / databases / backuplongtermretentionpolicies | はい | はい |
> | servers/elasticpools | はい | はい |
> | servers/jobaccounts | はい | はい |
> | servers/jobagents | はい | はい |
> | virtualclusters | はい | はい |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | はい | はい |
> | sqlvirtualmachines | はい | はい |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | storageaccounts | はい | はい |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | caches | いいえ | いいえ |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | はい | はい |

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

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!IMPORTANT]
> 実行中状態の Stream Analytics ジョブは移動できません。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | clusters | いいえ | いいえ |
> | streamingjobs | はい | ○ |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | 環境 | いいえ | いいえ |
> | instances | いいえ | いいえ |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | subscriptions | いいえ | いいえ |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | services | いいえ | いいえ |
> | supporttickets | いいえ | いいえ |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | workspaces | いいえ | いいえ |
> | workspaces / bigdatapools | いいえ | いいえ |
> | workspaces / sqlpools | いいえ | いいえ |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | 環境 | はい | はい |
> | environments/eventsources | はい | はい |
> | environments/referencedatasets | はい | はい |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | stores | はい | はい |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | imagetemplates | いいえ | いいえ |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!IMPORTANT]
> Azure DevOps のサブスクリプションを変更するには、[change the Azure subscription used for billing (課金に使用される Azure サブスクリプションの変更)](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json) に関する記事をご覧ください。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | account | いいえ | いいえ |
> | account / extension | いいえ | いいえ |
> | account/project | いいえ | いいえ |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | arczones | いいえ | いいえ |
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
> | virtualmachines | いいえ | いいえ |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | devices | いいえ | いいえ |
> | vnfs | いいえ | いいえ |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | accounts | いいえ | いいえ |
> | plans | いいえ | いいえ |
> | registeredsubscriptions | いいえ | いいえ |

## <a name="microsoftweb"></a>Microsoft.Web

> [!IMPORTANT]
> [App Service move guidance (App Service の移動のガイダンス)](./move-limitations/app-service-move-limitations.md) に関する記事をご覧ください。

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | availablestacks | いいえ | いいえ |
> | billingmeters | いいえ | いいえ |
> | certificates | いいえ | はい |
> | connectiongateways | はい | はい |
> | connections | はい | はい |
> | customapis | はい | はい |
> | deletedsites | いいえ | いいえ |
> | deploymentlocations | いいえ | いいえ |
> | georegions | いいえ | いいえ |
> | hostingenvironments | いいえ | いいえ |
> | kubeenvironments | はい | はい |
> | publishingusers | いいえ | いいえ |
> | recommendations | いいえ | いいえ |
> | resourcehealthmetadata | いいえ | いいえ |
> | runtimes | いいえ | いいえ |
> | serverfarms | はい | はい |
> | serverfarms / eventgridfilters | いいえ | いいえ |
> | sites | はい | はい |
> | sites/premieraddons | はい | はい |
> | sites/slots | はい | はい |
> | sourcecontrols | いいえ | いいえ |
> | staticsites | いいえ | いいえ |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | multipleactivationkeys | いいえ | いいえ |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | deviceservices | いいえ | いいえ |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
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

## <a name="third-party-services"></a>サード パーティーのサービス

サード パーティのサービスは現在、移動操作をサポートしていません。

## <a name="next-steps"></a>次のステップ

リソースの移動のコマンドについては、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](move-resource-group-and-subscription.md)」を参照してください。

コンマ区切りの値のファイルと同じデータを取得するには、[move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv) をダウンロードします。
