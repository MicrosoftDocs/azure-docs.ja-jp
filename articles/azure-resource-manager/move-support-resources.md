---
title: Azure リソースの種類別の移動操作のサポート
description: 新しいリソース グループまたはサブスクリプションに移動できる Azure リソースの種類を一覧表示します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 03/22/2019
ms.author: tomfitz
ms.openlocfilehash: d44b1bf778c7ec9551e2fd30f67083f8dded22d1
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438470"
---
# <a name="move-operation-support-for-resources"></a>リソースの操作のサポートの移動
この記事では、Azure リソースの種類は、移動操作をサポートしているかどうかを示します。 リソースの種類には、移動操作がサポートされていても、リソースが移動されることを妨げている条件がある可能性があります。 移動操作に影響する条件の詳細については、[新しいリソース グループまたはサブスクリプションへリソースを移動](resource-group-move-resources.md)を参照してください。

コンマ区切りの値のファイルと同じデータを取得するには、[move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv) をダウンロードします。

## <a name="microsoftaad"></a>Microsoft.AAD
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| domainservices | いいえ  | いいえ  |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| tenants | いいえ  | いいえ  |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| servers | はい | はい |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| service | はい | はい |

## <a name="microsoftappservice"></a>Microsoft.AppService
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| apiapps | いいえ  | いいえ  |
| appidentities | いいえ  | いいえ  |
| gateways | いいえ  | いいえ  |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| policyassignments | いいえ  | いいえ  |

## <a name="microsoftautomation"></a>Microsoft.Automation
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| automationaccounts | はい | はい |
| automationaccounts/configurations | はい | はい |
| automationaccounts/runbooks | はい | はい |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| b2cdirectories | はい | はい |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| registrations | はい | はい |

## <a name="microsoftbackup"></a>Microsoft.Backup
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| backupvault | いいえ  | いいえ  |

## <a name="microsoftbatch"></a>Microsoft.Batch
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| batchaccounts | はい | はい |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| clusters | いいえ  | いいえ  |
| fileservers | いいえ  | いいえ  |
| jobs | いいえ  | いいえ  |
| workspaces | いいえ  | いいえ  |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| mapapis | いいえ  | いいえ  |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| biztalk | はい | はい |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| blockchainmembers | いいえ  | いいえ  |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| blueprintassignments | いいえ  | いいえ  |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| botservices | はい | はい |

## <a name="microsoftcache"></a>Microsoft.Cache
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| redis | はい | はい |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| profiles | はい | はい |
| profiles/endpoints | はい | はい |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| certificateorders | はい | はい |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| domainnames | はい | いいえ  |
| virtualmachines | はい | いいえ  |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| networksecuritygroups | いいえ  | いいえ  |
| reservedips | いいえ  | いいえ  |
| virtualnetworks | いいえ  | いいえ  |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| storageaccounts | はい | いいえ  |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| accounts | はい | はい |

## <a name="microsoftcompute"></a>Microsoft.Compute
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| availabilitysets | はい | はい |
| disks | はい | はい |
| galleries | いいえ  | いいえ  |
| galleries/images | いいえ  | いいえ  |
| galleries/images/versions | いいえ  | いいえ  |
| images | はい | はい |
| proximityplacementgroups | いいえ  | いいえ  |
| restorepointcollections | いいえ  | いいえ  |
| sharedvmimages | いいえ  | いいえ  |
| sharedvmimages/versions | いいえ  | いいえ  |
| スナップショット | はい | はい |
| virtualmachines | はい | はい |
| virtualmachines/extensions | はい | はい |
| virtualmachinescalesets | はい | はい |

## <a name="microsoftcontainer"></a>Microsoft.Container
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| containergroups | いいえ  | いいえ  |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| containergroups | いいえ  | いいえ  |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| registries | はい | はい |
| registries/buildtasks | はい | はい |
| registries/replications | いいえ  | いいえ  |
| registries/tasks | はい | はい |
| registries/webhooks | はい | はい |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| containerservices | いいえ  | いいえ  |
| managedclusters | いいえ  | いいえ  |
| openshiftmanagedclusters | いいえ  | いいえ  |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| applications | はい | はい |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| accounts | いいえ  | いいえ  |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| connectors | はい | はい |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| hubs | はい | はい |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| jobs | いいえ  | いいえ  |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| databoxedgedevices | いいえ  | いいえ  |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| workspaces | いいえ  | いいえ  |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| catalogs | はい | はい |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| connectionmanagers | いいえ  | いいえ  |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| packages | いいえ  | いいえ  |
| plans | いいえ  | いいえ  |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| datafactories | はい | はい |
| factories | はい | はい |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| datalakeaccounts | いいえ  | いいえ  |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| accounts | はい | はい |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| accounts | はい | はい |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| services | いいえ  | いいえ  |
| services/projects | いいえ  | いいえ  |
| slots | いいえ  | いいえ  |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| servers | はい | はい |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| servers | はい | はい |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| servergroups | いいえ  | いいえ  |
| servers | はい | はい |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| artifactsources | いいえ  | いいえ  |
| rollouts | いいえ  | いいえ  |
| servicetopologies | いいえ  | いいえ  |
| servicetopologies/services | いいえ  | いいえ  |
| servicetopologies/services/serviceunits | いいえ  | いいえ  |
| steps | いいえ  | いいえ  |

## <a name="microsoftdevices"></a>Microsoft.Devices
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| elasticpools | いいえ  | いいえ  |
| elasticpools/iothubtenants | いいえ  | いいえ  |
| iothubs | はい | はい |
| provisioningservices | はい | はい |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| controllers | いいえ  | いいえ  |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| labcenters | いいえ  | いいえ  |
| labs | はい | いいえ  |
| labs/servicerunners | はい | はい |
| labs/virtualmachines | はい | いいえ  |
| schedules | いいえ  | いいえ  |

## <a name="microsoftdns"></a>microsoft.dns
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| dnszones | いいえ  | いいえ  |
| dnszones/a | いいえ  | いいえ  |
| dnszones/aaaa | いいえ  | いいえ  |
| dnszones/cname | いいえ  | いいえ  |
| dnszones/mx | いいえ  | いいえ  |
| dnszones/ptr | いいえ  | いいえ  |
| dnszones/srv | いいえ  | いいえ  |
| dnszones/txt | いいえ  | いいえ  |
| trafficmanagerprofiles | いいえ  | いいえ  |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| databaseaccounts | はい | はい |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| domains | はい | はい |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| domains | はい | はい |
| topics | はい | はい |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| clusters | はい | はい |
| namespaces | はい | はい |

## <a name="microsoftgenomics"></a>Microsoft.Genomics
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| accounts | いいえ  | いいえ  |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| hanainstances | はい | はい |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| clusters | はい | はい |

## <a name="microsofthybriddata"></a>Microsoft.HybridData
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| datamanagers | はい | はい |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| jobs | はい | はい |

## <a name="microsoftinsights"></a>microsoft.insights
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| accounts | いいえ  | いいえ  |
| actiongroups | はい | はい |
| activitylogalerts | いいえ  | いいえ  |
| alertrules | はい | はい |
| autoscalesettings | はい | はい |
| components | はい | はい |
| guestdiagnosticsettings | いいえ  | いいえ  |
| metricalerts | いいえ  | いいえ  |
| notificationgroups | いいえ  | いいえ  |
| notificationrules | いいえ  | いいえ  |
| scheduledqueryrules | いいえ  | いいえ  |
| webtests | はい | はい |
| Workbooks | はい | はい |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| iotapps | はい | はい |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| checknameavailability | はい | はい |
| graph | はい | はい |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| hsmpools | いいえ  | いいえ  |
| vaults | はい | はい |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| clusters | はい | はい |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| labaccounts | いいえ  | いいえ  |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| accounts | はい | はい |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| accounts | はい | はい |

## <a name="microsoftlogic"></a>Microsoft.Logic
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| hostingenvironments | いいえ  | いいえ  |
| integrationaccounts | はい | はい |
| integrationserviceenvironments | いいえ  | いいえ  |
| isolatedenvironments | いいえ  | いいえ  |
| workflows | はい | はい |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| commitmentplans | はい | はい |
| webservices | はい | いいえ  |
| workspaces | はい | はい |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| operationalizationclusters | はい | はい |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| accounts | はい | はい |
| accounts/workspaces | はい | はい |
| accounts/workspaces/プロジェクト | はい | はい |
| teamaccounts | はい | はい |
| teamaccounts/workspaces | はい | はい |
| teamaccounts/workspaces/projects | はい | はい |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| accounts | はい | はい |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| hostingaccounts | いいえ  | いいえ  |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| workspaces | いいえ  | いいえ  |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| userassignedidentities | はい | はい |

## <a name="microsoftmaps"></a>Microsoft.Maps
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| accounts | はい | はい |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| classicdevservices | いいえ  | いいえ  |

## <a name="microsoftmedia"></a>Microsoft.Media
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| mediaservices | はい | はい |
| mediaservices/liveevents | はい | はい |
| mediaservices/streamingendpoint | はい | はい |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| assessmentprojects | いいえ  | いいえ  |
| migrateprojects | いいえ  | いいえ  |
| projects | いいえ  | いいえ  |

## <a name="microsoftnetapp"></a>Microsoft.NetApp
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| netappaccounts | いいえ  | いいえ  |
| netappaccounts/capacitypools | いいえ  | いいえ  |
| netappaccounts/capacitypools/volumes | いいえ  | いいえ  |
| netappaccounts/capacitypools/volumes/mounttargets | いいえ  | いいえ  |
| netappaccounts/capacitypools/volumes/snapshots | いいえ  | いいえ  |

## <a name="microsoftnetwork"></a>Microsoft.Network
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| applicationgateways | いいえ  | いいえ  |
| applicationsecuritygroups | はい | はい |
| azurefirewalls | はい | はい |
| bastionhosts | いいえ  | いいえ  |
| connections | はい | はい |
| ddoscustompolicies | はい | はい |
| ddosprotectionplans | いいえ  | いいえ  |
| dnszones | はい | はい |
| expressroutecircuits | いいえ  | いいえ  |
| expressroutecrossconnections | いいえ  | いいえ  |
| expressroutegateways | いいえ  | いいえ  |
| expressrouteports | いいえ  | いいえ  |
| frontdoors | はい | はい |
| frontdoorwebapplicationfirewallpolicies | はい | はい |
| interfaceendpoints | いいえ  | いいえ  |
| loadbalancers | はい | はい |
| localnetworkgateways | はい | はい |
| natgateways | はい | はい |
| networkintentpolicies | はい | はい |
| networkinterfaces | はい | はい |
| networkprofiles | いいえ  | いいえ  |
| networksecuritygroups | はい | はい |
| networkwatchers | はい | はい |
| networkwatchers/connectionmonitors | はい | はい |
| networkwatchers/lenses | はい | はい |
| networkwatchers/pingmeshes | はい | はい |
| p2svpngateways | いいえ  | いいえ  |
| privatelinkservices | いいえ  | いいえ  |
| publicipaddresses | はい | はい |
| publicipprefixes | はい | はい |
| routefilters | いいえ  | いいえ  |
| routetables | はい | はい |
| securegateways | いいえ  | いいえ  |
| serviceendpointpolicies | はい | はい |
| trafficmanagerprofiles | はい | はい |
| virtualhubs | いいえ  | いいえ  |
| virtualnetworkgateways | はい | はい |
| virtualnetworks | はい | はい |
| virtualnetworktaps | いいえ  | いいえ  |
| virtualwans | いいえ  | いいえ  |
| vpngateways | いいえ  | いいえ  |
| vpnsites | はい | はい |
| webapplicationfirewallpolicies | はい | はい |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| namespaces | はい | はい |
| namespaces/notificationhubs | はい | はい |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| workspaces | はい | はい |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| managementconfigurations | はい | はい |
| solutions | はい | はい |
| views | はい | はい |

## <a name="microsoftportal"></a>Microsoft.Portal
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| dashboards | はい | はい |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| rootresources | いいえ  | いいえ  |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| workspacecollections | はい | はい |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| capacities | はい | はい |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| accounts | いいえ  | いいえ  |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| vaults | はい | はい |

## <a name="microsoftrelay"></a>Microsoft.Relay
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| namespaces | はい | はい |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| applications | はい | いいえ  |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| flows | はい | はい |
| jobcollections | はい | はい |

## <a name="microsoftsearch"></a>Microsoft.Search
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| searchservices | はい | はい |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| gateways | いいえ  | いいえ  |
| nodes | いいえ  | いいえ  |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| namespaces | はい | はい |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| applications | いいえ  | いいえ  |
| clusters | はい | はい |
| containergroups | いいえ  | いいえ  |
| containergroupsets | いいえ  | いいえ  |
| edgeclusters | いいえ  | いいえ  |
| networks | いいえ  | いいえ  |
| secretstores | いいえ  | いいえ  |
| volumes | いいえ  | いいえ  |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| applications | はい | はい |
| containergroups | いいえ  | いいえ  |
| gateways | はい | はい |
| networks | はい | はい |
| secrets | はい | はい |
| volumes | はい | はい |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| signalr | はい | はい |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| siterecoveryvault | いいえ  | いいえ  |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| appliancedefinitions | いいえ  | いいえ  |
| appliances | いいえ  | いいえ  |
| applicationdefinitions | いいえ  | いいえ  |
| applications | いいえ  | いいえ  |
| jitrequests | いいえ  | いいえ  |

## <a name="microsoftsql"></a>Microsoft.Sql
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| managedinstances | はい | はい |
| managedinstances/データベース | はい | はい |
| servers | はい | はい |
| servers/databases | はい | はい |
| servers/elasticpools | はい | はい |
| servers/jobaccounts | いいえ  | いいえ  |
| servers/jobagents | いいえ  | いいえ  |
| virtualclusters | はい | はい |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| sqlvirtualmachinegroups | はい | はい |
| sqlvirtualmachines | はい | はい |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| dwvm | いいえ  | いいえ  |

## <a name="microsoftstorage"></a>Microsoft.Storage
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| storageaccounts | はい | はい |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| storagesyncservices | はい | はい |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| storagesyncservices | いいえ  | いいえ  |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| storagesyncservices | いいえ  | いいえ  |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| managers | いいえ  | いいえ  |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| streamingjobs | はい | はい |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| 環境 | いいえ  | いいえ  |
| environments/eventsource | いいえ  | いいえ  |
| instances | いいえ  | いいえ  |
| instances/environments | いいえ  | いいえ  |
| instances/environments/eventsources | いいえ  | いいえ  |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| providerregistrations | いいえ  | いいえ  |
| resources | いいえ  | いいえ  |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| 環境 | はい | はい |
| environments/eventsource | はい | はい |
| environments/referencedatasets | はい | はい |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| imagetemplates | いいえ  | いいえ  |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| account | はい | はい |
| account/extension | はい | はい |
| account/project | はい | はい |

## <a name="microsoftweb"></a>Microsoft.Web
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| certificates | いいえ  | はい |
| connectiongateways | はい | はい |
| connections | はい | はい |
| customapis | はい | はい |
| hostingenvironments | いいえ  | いいえ  |
| serverfarms | はい | はい |
| sites | はい | はい |
| sites/premieraddons | はい | はい |
| sites/slots | はい | はい |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| リソースの種類 | リソース グループ | サブスクリプション |
| ------------- | ----------- | ---------- |
| deviceservices | はい | はい |

## <a name="third-party-services"></a>サード パーティーのサービス

サード パーティのサービスは現在、移動操作をサポートしていません。

## <a name="next-steps"></a>次の手順
リソースの移動のコマンドについては、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](resource-group-move-resources.md)」を参照してください。
