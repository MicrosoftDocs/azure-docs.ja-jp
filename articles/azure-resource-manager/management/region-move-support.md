---
title: リージョン間の Azure リソースの移動のサポート
description: Azure リージョン間で移動できる Azure リソースの種類を一覧表示する
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 07/21/2020
ms.author: raynew
ms.openlocfilehash: 70f981f2763dd36f0f417faec6c81e168e9856e7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87040957"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>リージョン間の Azure リソースの移動のサポート

この記事では、Azure リソースの種類が別の Azure リージョンへの移動をサポートされているかどうかを確認します。 

リソース プロバイダーの名前空間に移動します。
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
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
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- | 
> | domainservices | いいえ | 
> | domainservices/replicasets | いいえ | 

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | tenants | いいえ |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | actionrules | いいえ | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | servers | いいえ |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | サービス (service) |  はい (テンプレートを使用) <br/><br/> [Azure API Management をリージョン間で移行する](../../api-management/api-management-howto-migrate.md)。 | 

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | configurationstores | いいえ | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | apiapps | はい (テンプレートを使用)<br/><br/> [App Service アプリを別のリージョンに移動する](../../app-service/manage-move-across-regions.md) | 
> | appidentities | いいえ | 
> | gateways | いいえ | 


## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | policyassignments | いいえ |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | automationaccounts | はい (テンプレートを使用) <br/><br/> [geo レプリケーションを使用する](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |  
> | automationaccounts/configurations | いいえ | 
> | automationaccounts/runbooks | いいえ | 



## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | b2cdirectories | いいえ | 

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | sqlserverregistrations | いいえ |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | registrations | いいえ | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | batchaccounts |  Batch アカウントを別のリージョンに直接移行することはできませんが、テンプレートを使用し、テンプレートをエクスポートしてそれを変更し、テンプレートを新しいリージョンにデプロイすることはできます。 <br/><br/> [リージョン間で Batch アカウントを移行する](../../batch/best-practices.md#moving-batch-accounts-across-regions)方法を参照してください。 |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | clusters | いいえ <br/><br/> Azure Batch AI サービスは[廃止](/previous-versions/azure/batch-ai/overview-what-happened-batch-ai)されました。
> | fileservers | いいえ | 
> | jobs | いいえ | 
> | workspaces | いいえ | 

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
> | watchers | いいえ | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | blueprintassignments | いいえ | 

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


## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | いいえ |
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
> | domainnames | クラシック サービスについては作業が予定されていません。
> | virtualmachines | いいえ | 



## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | networksecuritygroups | クラシック サービスについては作業が予定されていません。
> | reservedips | いいえ | 
> | virtualnetworks | いいえ | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | storageaccounts | はい |  


## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | accounts | いいえ | 
> | Cognitive Search | 手動の手順でサポートされています。<br/><br/> [Azure Cognitive Search サービスを別のリージョンに移行する](../../search/search-howto-move-across-regions.md)方法を参照してください。

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | availabilitysets | いいえ | 
> | diskencryptionsets | いいえ | 
> | disks | いいえ | 
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
> | virtualmachines | はい | 
> | virtualmachines/extensions | いいえ | 
> | virtualmachinescalesets | いいえ | 

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | containergroups | いいえ | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | containergroups | いいえ | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | registries | いいえ |  
> | registries/buildtasks | いいえ |  
> | registries/replications | いいえ | 
> | registries/tasks | いいえ |  
> | registries/webhooks | いいえ | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | containerservices | いいえ。<br/><br/> サービスは[廃止](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/)されました。
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
> | connectors | いいえ |  

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | hubs | いいえ |  

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
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

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | elasticpools | いいえ。 リソースは公開されていません。
> | elasticpools/iothubtenants | いいえ。 リソースは公開されていません。
> | iothubs | はい。 [詳細情報](../../iot-hub/iot-hub-how-to-clone.md)
> | provisioningservices | いいえ | 

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

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | databaseaccounts | いいえ | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | domains | いいえ | 

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
> | topics | いいえ | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | clusters | いいえ |  
> | namespaces | はい (テンプレートを使用)<br/><br/> [Event Hub 名前空間を別のリージョンに移動する](../../event-hubs/move-across-regions.md) | 

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | accounts | いいえ | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | hanainstances | いいえ | 
> | sapmonitors | いいえ |  

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

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | datamanagers |  いいえ | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | jobs |  いいえ | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | accounts | いいえ | 
> | actiongroups |  いいえ | 
> | activitylogalerts | いいえ | 
> | alertrules |  いいえ | 
> | autoscalesettings |  いいえ | 
> | components |  いいえ |  
> | guestdiagnosticsettings | いいえ | 
> | metricalerts | いいえ | 
> | notificationgroups | いいえ | 
> | notificationrules | いいえ | 
> | scheduledqueryrules |  いいえ | 
> | webtests |  いいえ | 
> | Workbooks |  いいえ |  


## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | checknameavailability |  いいえ。<br/><br/> IoT Central はリージョンではなく、地域で機能しています。
> | graph | いいえ

## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> |  iothub |  はい (ハブを複製) <br/><br/> [IoT ハブを別のリージョンに複製する](../../iot-hub/iot-hub-how-to-clone.md)

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | checknameavailability |  いいえ |  
> | graph |  いいえ | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | hsmpools | いいえ | 
> | vaults |  いいえ | 


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
> | accounts/workspaces | いいえ | 
> | accounts/workspaces/projects | いいえ | 
> | teamaccounts | いいえ | 
> | teamaccounts/workspaces | いいえ | 
> | teamaccounts/workspaces/projects | いいえ | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | accounts | いいえ | 

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | hostingaccounts | いいえ | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | workspaces | いいえ | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | userassignedidentities | いいえ | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | accounts |  いいえ。Azure Maps は地理空間サービスです。 

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | classicdevservices | クラシック サービスについては作業が予定されていません。 

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
> | projects | いいえ | 

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
> | connections |  いいえ | 
> | ddoscustompolicies |  いいえ | 
> | ddosprotectionplans | いいえ | 
> | dnszones |  いいえ | 
> | expressroutecircuits | いいえ | 
> | expressroutecrossconnections | いいえ | 
> | expressroutegateways | いいえ | 
> | expressrouteports | いいえ | 
> | frontdoors | いいえ | 
> | frontdoorwebapplicationfirewallpolicies | いいえ | 
> | loadbalancers | はい <br/><br/> 既存の構成をテンプレートとしてエクスポートし、そのテンプレートを新しいリージョンにデプロイすることができます。 [外部](../..//load-balancer/move-across-regions-external-load-balancer-portal.md)または[内部](../../load-balancer/move-across-regions-internal-load-balancer-portal.md)のロード バランサーを移動する方法をご覧ください。 |
> | localnetworkgateways |  いいえ | 
> | natgateways |  いいえ | 
> | networkintentpolicies |  いいえ | 
> | networkinterfaces | はい | 
> | networkprofiles | いいえ | 
> | networksecuritygroups | はい | 
> | networkwatchers |  いいえ |  
> | networkwatchers/connectionmonitors |  いいえ | 
> | networkwatchers/lenses |  いいえ | 
> | networkwatchers/pingmeshes |  いいえ | 
> | p2svpngateways | いいえ | 
> | privatednszones |  いいえ |  
> | privatednszones/virtualnetworklinks |  いいえ |  
> | privateendpoints | いいえ | 
> | privatelinkservices | いいえ | 
> | publicipaddresses | はい<br/><br/> 既存のパブリック IP アドレス構成をテンプレートとしてエクスポートし、そのテンプレートを新しいリージョンにデプロイすることができます。 パブリック IP アドレスの移動の[詳細をご覧ください](../../virtual-network/move-across-regions-publicip-portal.md)。 |
> | publicipprefixes | いいえ | 
> | routefilters | いいえ | 
> | routetables |  いいえ | 
> | serviceendpointpolicies |  いいえ | 
> | trafficmanagerprofiles |  いいえ | 
> | virtualhubs | いいえ | 
> | virtualnetworkgateways |  いいえ |  
> | virtualnetworks |  いいえ | 
> | virtualnetworktaps | いいえ | 
> | virtualwans | いいえ | 
> | vpngateways (仮想 WAN) | いいえ | 
> | vpnsites (仮想 WAN) | いいえ | 
> | webapplicationfirewallpolicies |  いいえ | 


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | namespaces |  いいえ | 
> | namespaces/notificationhubs |  いいえ |  

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | workspaces |  いいえ | 



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | managementconfigurations |  いいえ | 
> | views |  いいえ | 

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | peerings | いいえ | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | dashboards | いいえ | 

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | rootresources | いいえ | 

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

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | accounts | いいえ | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | vaults | いいえ。<br/><br/> Azure リージョン間で Azure Backup 用の Recovery Services コンテナーを移動することはできません。<br/><br/> Azure Site Recovery 用の Recovery Services コンテナーでは、[そのコンテナーを無効にして、ターゲット リージョンで再作成する](../../site-recovery/move-vaults-across-regions.md)ことができます。 | 


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

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | applications |  いいえ | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | flows |  いいえ |  
> | jobcollections |  いいえ | 

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | searchservices |  いいえ | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | iotsecuritysolutions |  いいえ | 
> | playbookconfigurations | いいえ | 

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

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | applications | いいえ | 
> | clusters |  いいえ | 
> | clusters/applications | いいえ | 
> | containergroups | いいえ | 
> | containergroupsets | いいえ | 
> | edgeclusters | いいえ | 
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

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | signalr |  いいえ |  

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | appliancedefinitions | いいえ | 
> | appliances | いいえ | 
> | applicationdefinitions | いいえ | 
> | applications | いいえ | 
> | jitrequests | いいえ | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | instancepools | いいえ | 
> | managedinstances | はい | 
> | managedinstances/databases | はい | 
> | servers | はい | 
> | servers/databases | はい | 
> | servers/elasticpools | はい | 
> | virtualclusters | はい | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  いいえ |  
> | sqlvirtualmachines |  いいえ |  

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | dwvm | いいえ | 

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
> | streamingjobs |  いいえ |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | 環境 | いいえ | 
> | environments/eventsources | いいえ | 
> | instances | いいえ | 
> | instances/environments | いいえ | 
> | instances/environments/eventsources | いいえ | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | providerregistrations | いいえ | 
> | resources | いいえ | 

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



## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | いいえ | 
> | dedicatedcloudservices | いいえ | 
> | virtualmachines | いいえ | 

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | certificates | いいえ | 
> | connectiongateways |  いいえ |  
> | connections |  いいえ |  
> | customapis |  いいえ | 
> | hostingenvironments | いいえ | 
> | serverfarms |  いいえ |  
> | sites |  いいえ | 
> | sites/premieraddons |  いいえ |  
> | sites/slots |  いいえ |  


## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | deviceservices | いいえ | 

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | リソースの種類 | リージョンの移動 | 
> | ------------- | ----------- |
> | applicationgroups | いいえ | 
> | hostpools | いいえ | 
> | workspaces | いいえ | 

## <a name="third-party-services"></a>サード パーティーのサービス

サード パーティのサービスは現在、移動操作をサポートしていません。
