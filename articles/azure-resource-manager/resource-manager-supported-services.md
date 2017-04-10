---
title: "Resource Manager でサポートされるサービス | Microsoft Docs"
description: "リソース マネージャーをサポートするリソース プロバイダー、そのスキーマと利用可能な API バージョン、およびリソースをホストできるリージョンについて説明します。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 3c7a6fe4-371a-40da-9ebe-b574f583305b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 7dc5143086e3a73e0536408a41468b8cdd40bd12
ms.lasthandoff: 03/24/2017


---
# <a name="resource-manager-providers-regions-api-versions-and-schemas"></a>リソース マネージャーのプロバイダー、リージョン、API のバージョン、およびスキーマ
このトピックでは、Azure Resource Manager をサポートするリソース プロバイダーの一覧を示します。

リソースをデプロイするときは、リソースをサポートしているリージョンとリソースで利用できる API バージョンも知っておく必要があります。 「[サポートされているリージョン](#supported-regions)」セクションでは、サブスクリプションおよびリソースで有効なリージョンを確認する方法が示されています。 「 [サポートされる API バージョン](#supported-api-versions) 」セクションでは、利用できる API バージョンを決定する方法について紹介します。

Azure ポータルとクラシック ポータルでサポートされているサービスについては、 [Azure ポータルで利用できるサービスの表](https://azure.microsoft.com/features/azure-portal/availability/)を参照してください。 リソースの移動をサポートするサーバーを確認する方法については、「 [新しいリソース グループまたはサブスクリプションへのリソースの移動](resource-group-move-resources.md)」を参照してください。

次の表では、各 Microsoft サービスで Resource Manager によるデプロイと管理がサポートされるかどうかを示します。 Resource Manager をサポートするサード パーティのリソース プロバイダーも多数存在します。 すべてのリソース プロバイダーを確認する方法については、「[リソース プロバイダーと種類](#resource-providers-and-types)」セクションを参照してください。

## <a name="compute"></a>計算
| サービス | リソース マネージャーが有効 | REST API | テンプレートの形式 |
| --- | --- | --- | --- |
| Batch
 |あり |[Batch REST](/rest/api/batchservice) |[バッチのリソース](/azure/templates/microsoft.batch/batchaccounts) |
| Container Registry |はい |[Container Registry の REST](/rest/api/containerregistry) |[Container Registry のリソース](/azure/templates/microsoft.containerregistry/registries) |
| Container Service |はい |[コンテナー サービスの REST](/rest/api/compute/containerservices) |[コンテナー サービスのリソース](/azure/templates/microsoft.containerservice/containerservices) |
| Dynamics Lifecycle Services |あり | | |
| スケール セット |あり |[スケール セットの REST](/rest/api/compute/virtualmachinescalesets) |[スケール セットのリソース](/azure/templates/microsoft.compute/virtualmachinescalesets) |
| Service Fabric |あり |[Service Fabric Rest](/rest/api/servicefabric) | [Service Fabric のリソース](/azure/templates/microsoft.servicefabric/clusters) |
| Virtual Machines |あり |[VM REST](/rest/api/compute/virtualmachines) |[VM のリソース](/azure/templates/microsoft.compute/virtualmachines) |
| Virtual Machines (クラシック) |制限あり |- |- |
| リモート アプリ |いいえ |- |- |
| Cloud Services (クラシック) |制限あり (下記参照) |- |- |

「Virtual Machines (クラシック)」とは、リソース マネージャー デプロイ モデルではなくクラシック デプロイ モデルを使用してデプロイされたリソースのことです。 一般に、このようなリソースはリソース マネージャーの操作をサポートしていませんが、有効になっている操作がいくつかあります。 これらのデプロイメント モデルの詳細については、「 [リソース マネージャー デプロイと従来のデプロイを理解する](resource-manager-deployment-model.md)」を参照してください。 

Cloud Services (クラシック) は、他のクラシック リソースと共に使用できます。 ただし、クラシック リソースは、Resource Manager のすべての機能を利用するわけではないため、今後のソリューションに適したオプションではありません。 代わりに、アプリケーション インフラストラクチャを変更して、Microsoft.Compute、Microsoft.Storage、Microsoft.Network の各名前空間のリソースを使用することを検討してください。

## <a name="networking"></a>ネットワーク
| サービス | リソース マネージャーが有効 | REST API | テンプレートの形式 |
| --- | --- | --- | --- |
| Application Gateway |あり |[Application Gateway REST](https://msdn.microsoft.com/library/azure/mt684939.aspx) | [Application Gateway のリソース](/azure/templates/microsoft.network/applicationgateways) |
| DNS |あり |[DNS REST](/rest/api/dns) |[DNS のリソース](/azure/templates/microsoft.network/dnszones) |
| ExpressRoute |あり |[ExpressRoute REST](https://msdn.microsoft.com/library/azure/mt586720.aspx) | [ExpressRoute のリソース](/azure/templates/microsoft.network/expressroutecircuits) |
| Load Balancer |あり |[Load Balancer REST](https://msdn.microsoft.com/library/azure/mt163651.aspx) |[ロード バランサーのリソース](/azure/templates/microsoft.network/loadbalancers) |
| Traffic Manager |あり |[Traffic Manager REST](https://msdn.microsoft.com/library/azure/mt163667.aspx) |[Traffic Manager のリソース](/azure/templates/microsoft.network/trafficmanagerprofiles) |
| Virtual Networks |あり |[Virtual Network REST](https://msdn.microsoft.com/library/azure/mt163650.aspx) |[Virtual Network のリソース](/azure/templates/microsoft.network/virtualnetworks) |
| ネットワーク ゲートウェイ |はい |[ネットワーク ゲートウェイ REST](https://msdn.microsoft.com/library/azure/mt163859.aspx) | [接続のリソース](/azure/templates/microsoft.network/connections) <br /> [ローカル ネットワーク ゲートウェイのリソース](/azure/templates/microsoft.network/localnetworkgateways) <br /> [Virtual Network ゲートウェイのリソース](/azure/templates/microsoft.network/virtualnetworkgateways) |

## <a name="storage"></a>Storage
| サービス | リソース マネージャーが有効 | REST API | テンプレートの形式 |
| --- | --- | --- | --- | --- |
| Import Export | はい | [Import Export REST](/rest/api/storageimportexport/) | [Import Export のリソース](/azure/templates/microsoft.importexport/jobs) |
| Storage |あり |[Storage REST](/rest/api/storagerp) |[Storage のリソース](/azure/templates/microsoft.storage/storageaccounts) |
| StorSimple |はい | | |

## <a name="databases"></a>データベース
| サービス | リソース マネージャーが有効 | REST API | テンプレートの形式 |
| --- | --- | --- | --- | --- |
| DocumentDB |あり |[DocumentDB REST](/rest/api/documentdbresourceprovider) |[DocumentDB のリソース](/azure/templates/microsoft.documentdb/databaseaccounts) |
| Redis Cache |はい | [Redis Cache REST](/rest/api/redis) |[Redis のリソース](/azure/templates/microsoft.cache/redis) |
| SQL Database |あり |[SQL Database REST](/rest/api/sql) |[SQL Database のリソース](/azure/templates/microsoft.sql/servers) |
| SQL Data Warehouse |あり | | |

## <a name="web--mobile"></a>Web とモバイル
| サービス | リソース マネージャーが有効 | REST API | テンプレートの形式 |
| --- | --- | --- | --- |
| API Apps |はい | [App Service REST](/rest/api/appservice) |[Web のリソース](/azure/templates/microsoft.web/sites) |
| API Management |あり |[API Management REST](/rest/api/apimanagement) |[API Management のリソース](/azure/templates/microsoft.apimanagement/service) |
| 証明書登録 | はい | [証明書登録 REST](/rest/api/appservice/appservicecertificateorders) | [証明書登録のリソース](/azure/templates/microsoft.certificateregistration/certificateorders)  |
| Content Moderator |はい | | | |
| ドメイン登録 | はい | [ドメイン登録](/rest/api/appservice/domains) | [ドメイン登録のリソース](/azure/templates/microsoft.domainregistration/domains)  |
| Function App |はい | [Function App REST](/rest/api/appservice) | [Web のリソース](/azure/templates/microsoft.web/sites) |
| Logic Apps |はい |[Logic Apps REST](/rest/api/logic) |[Logic App のリソース](/azure/templates/microsoft.logic/workflows) |
| Mobile Apps |はい | [App Service REST](/rest/api/appservice) | [Web のリソース](/azure/templates/microsoft.web/sites) |
| Mobile Engagement |はい |[Mobile Engagement REST](https://msdn.microsoft.com/library/azure/mt683754.aspx) | |
| Search |あり |[Search REST](/rest/api/searchservice) | [Search のリソース](/azure/templates/microsoft.search/searchservices) |
| Web Apps |はい | [Web Apps REST](/rest/api/appservice/webapps) | [Web のリソース](/azure/templates/microsoft.web/sites) |

## <a name="intelligence--analytics"></a>インテリジェンス + 分析
| サービス | リソース マネージャーが有効 | REST API | テンプレートの形式 | 
| --- | --- | --- | --- |
| Analysis Services | はい | [Analysis Services REST](/rest/api/analysisservices) | [Analysis Services のリソース](/azure/templates/microsoft.analysisservices/servers) |
| Cognitive Services |はい | [Cognitive Services REST](/rest/api/cognitiveservices) |[Cognitive Services のリソース](/azure/templates/microsoft.cognitiveservices/accounts) |
| Data Catalog |あり |[Data Catalog REST](/rest/api/datacatalog) |[Data Catalog スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.DataCatalog.json) |
| Data Factory |あり |[Data Factory REST](/rest/api/datafactory) | |
| Data Lake Analytics |あり | [Data Lake REST](/rest/api/datalakeanalytics) |[Data Lake Analytics のリソース](/azure/templates/microsoft.datalakeanalytics/accounts) |
| Data Lake Store |あり |[Data Lake Store REST](/rest/api/datalakestore) |[Data Lake Store のリソース](/azure/templates/microsoft.datalakestore/accounts) |
| HDInsights |あり |[HDInsights REST](/rest/api/hdinsight) | |
| Machine Learning |あり |[Machine Learning REST](/rest/api/machinelearning) |[Machine Learning のリソース](/azure/templates/microsoft.machinelearning/commitmentplans) |
| Stream Analytics |あり |[Steam Analytics REST](/rest/api/streamanalytics) | |
| Power BI |あり |[Power BI Embedded REST](/rest/api/powerbiembedded) |[Power BI のリソース](/azure/templates/microsoft.powerbi/workspacecollections) |


## <a name="internet-of-things"></a>モノのインターネット
| サービス | リソース マネージャーが有効 | REST API | テンプレートの形式 |
| --- | --- | --- | --- |
| イベント ハブ |あり |[Event Hubs REST](/rest/api/eventhub) |[Event Hub のリソース](/azure/templates/microsoft.eventhub/namespaces) |
| IoTHubs |あり |[IoT Hub REST](/rest/api/iothub) |[IoT Hub のリソース](/azure/templates/microsoft.devices/iothubs) |
| Notification Hubs |あり |[Notification Hubs REST](/rest/api/notificationhubs) |[Notification Hubs のリソース](/azure/templates/microsoft.notificationhubs/namespaces) |

## <a name="media--cdn"></a>メディアと CDN
| サービス | リソース マネージャーが有効 | REST API | テンプレートの形式 |
| --- | --- | --- | --- |
| CDN |あり |[CDN REST](/rest/api/cdn) |[CDN のリソース](/azure/templates/microsoft.cdn/profiles) |
| メディア サービス |あり |[Media Services REST](/rest/api/media) |[メディアのリソース](/azure/templates/microsoft.media/mediaservices) |

## <a name="enterprise-integration"></a>エンタープライズ統合
| サービス | リソース マネージャーが有効 | REST API | テンプレートの形式 |
| --- | --- | --- | --- |
| BizTalk Services |はい | |[BizTalk スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) |
| リレー | はい |  | [リレーのリソース](/azure/templates/microsoft.relay/namespaces) |
| Service Bus |はい |[Service Bus REST](/rest/api/servicebus) |[Service Bus のリソース](/azure/templates/microsoft.servicebus/namespaces) |

## <a name="identity--access-management"></a>ID 管理とアクセス管理
Azure Active Directory はリソース マネージャーと連携して、サブスクリプションのロールベースのアクセス制御を可能にします。 ロールベースのアクセス制御と Active Directory の使用方法については、「 [Azure Active Directory リソースへのアクセスをロールの割り当てによって管理する](../active-directory/role-based-access-control-configure.md)」を参照してください。

## <a name="developer-services"></a>開発者サービス
| サービス | リソース マネージャーが有効 | REST API | テンプレートの形式 |
| --- | --- | --- | --- |
| 監視 |はい |[Monitor REST](/rest/api/monitor) |[Insights のリソース](/azure/templates/microsoft.insights/alertrules) |
| Bing Maps |あり | | |
| DevTest Labs |はい | [DevTest REST](/rest/api/dtl) |[DevTest Lab のリソース](/azure/templates/microsoft.devtestlab/labs) |
| Visual Studio アカウント |はい | |[Visual Studio スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) |

## <a name="management-and-security"></a>管理とセキュリティ
| サービス | リソース マネージャーが有効 | REST API | テンプレートの形式 |
| --- | --- | --- | --- |
| Advisor | はい | [Advisor REST](/rest/api/advisor/) | - |
| Automation |あり |[Automation REST](https://msdn.microsoft.com/library/azure/mt662285.aspx) |[Automation リソース](/azure/templates/microsoft.automation/automationaccounts) |
| 課金 | はい | [課金 REST](/rest/api/billing/) | - |
| Key Vault |あり |[Key Vault](/rest/api/keyvault) |[Key Vault のリソース](/azure/templates/microsoft.keyvault/vaults) |
| Operational Insights |はい | | |
| Recovery Service |はい |[Recovery Services REST](/rest/api/recoveryservices) |[Recovery Services のリソース](/azure/templates/microsoft.recoveryservices/vaults) |
| Scheduler |あり |[Scheduler REST](/rest/api/scheduler) |[Scheduler のリソース](/azure/templates/microsoft.scheduler/jobcollections) |
| セキュリティ |はい |[Security REST](https://msdn.microsoft.com/library/azure/mt704034.aspx) | |
| Server Management | はい | [Server Management REST](/rest/api/servermanagement/) | [Server Management のリソース](/azure/templates/microsoft.servermanagement/gateways) |

## <a name="resource-manager"></a>リソース マネージャー
| 機能 | リソース マネージャーが有効 | REST API | テンプレートの形式 |
| --- | --- | --- | --- | --- |
| 承認 |はい |[Authorization REST](/rest/api/authorization) |[Authorization のリソース](/azure/templates/microsoft.authorization/locks) |
| リソース |はい |[Resources REST](/rest/api/resources) |[デプロイのリソース](/azure/templates/microsoft.resources/deployments) |

## <a name="resource-providers-and-types"></a>リソース プロバイダーと種類
リソースをデプロイするときに、リソース プロバイダーと種類に関する情報を取得しなければならないケースは少なくありません。 この情報は、REST API、Azure PowerShell、Azure CLI のいずれかを使って取得できます。

リソース プロバイダーを使用するには、そのリソース プロバイダーをアカウントに登録する必要があります。 既定では、多くのリソース プロバイダーが自動的に登録されます。ただし、一部のリソース プロバイダーは手動で登録することが必要な場合があります。 次の例は、リソース プロバイダーの登録状態を取得し、必要に応じてリソース プロバイダーを登録する方法を示しています。

### <a name="portal"></a>ポータル
サブスクリプション ブレードで **[リソース プロバイダー]** を選択すると、サポートされているリソース プロバイダーの一覧を簡単に確認できます。 サブスクリプションをリソース プロバイダーに登録するには、**[登録]** リンクを選択します。
   
![リソース プロバイダーの一覧](./media/resource-manager-supported-services/view-resource-providers.png)

### <a name="rest-api"></a>REST API
使用可能なすべてのリソース プロバイダーとその種類、場所、API バージョン、登録状態を取得するには、 [すべてのリソース プロバイダーの一覧表示](https://docs.microsoft.com/rest/api/resources/providers#Providers_List)操作を使用します。 リソース プロバイダーを登録する必要がある場合は、「 [リソース プロバイダーへのサブスクリプションの登録](https://docs.microsoft.com/rest/api/resources/providers#Providers_Register)」をご覧ください。

### <a name="powershell"></a>PowerShell
使用可能なすべてのリソース プロバイダーを取得する方法を次の例に示します。

```powershell
Get-AzureRmResourceProvider -ListAvailable
```


次の例では、特定のリソース プロバイダーのリソースの種類を取得しています。

```powershell
(Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes
```

リソース プロバイダーを登録するには、名前空間を指定します。

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ApiManagement
```

### <a name="azure-cli"></a>Azure CLI
使用可能なすべてのリソース プロバイダーを取得する方法を次の例に示します。

```azurecli
az provider list
```

特定のリソース プロバイダーの情報は、次のコマンドで表示できます。

```azurecli
az provider show --namespace Microsoft.Web
```

リソース プロバイダーを登録するには、名前空間を指定します。

```azurecli
az provider register --namespace Microsoft.ServiceBus
```

## <a name="supported-regions"></a>サポートされているリージョン
通常、リソースをデプロイするときはリソースのリージョンを指定する必要があります。 リソース マネージャーはすべてのリージョンでサポートされていますが、デプロイするリソースはすべてのリージョンではサポートされていない場合があります。 さらに、サブスクリプションでの制限により、リソースをサポートする一部のリージョンを使用できない場合があります。 これらの制限事項は、本国での税金に関する問題のため、またはサブスクリプション管理者によって設けられた、使用を特定のリージョンに制限するポリシーの結果である場合があります。 

すべての Azure サービスでサポートされている全リージョンの一覧については、[リージョン別のサービス](https://azure.microsoft.com/regions/#services)に関するページを参照してください。 ただし、この一覧には、ご利用のサブスクリプションでサポートされていないリージョンも含まれている可能性があります。 ご利用のサブスクリプションでサポートされている、特定のリソースの種類に対応したリージョンは、Portal、REST API、PowerShell、または Azure CLI を使用して確認できます。

### <a name="portal"></a>ポータル
次の手順で、サポートされているリージョンをリソースの種類別に確認できます。

1. **[その他のサービス]** > **[リソース エクスプローラー]** を選択します。
   
    ![リソース エクスプローラー](./media/resource-manager-supported-services/select-resource-explorer.png)
2. **[プロバイダー]** ノードを開きます。
   
    ![プロバイダーの選択](./media/resource-manager-supported-services/select-providers.png)
3. リソース プロバイダーを選択し、サポートされているリージョンと API バージョンを確認します。
   
    ![プロバイダーの表示](./media/resource-manager-supported-services/view-provider.png)

### <a name="rest-api"></a>REST API
ご利用のサブスクリプションの特定のリソースの種類で使用可能なリージョンを確認するには、 [すべてのリソース プロバイダーの一覧表示](https://docs.microsoft.com/rest/api/resources/providers#Providers_List) 操作を利用します。 

### <a name="powershell"></a>PowerShell
次の例では、Web サイトでサポートされるリージョンを取得する方法を示します。

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

### <a name="azure-cli"></a>Azure CLI
次の例では、Web サイトでサポートされる場所を取得する方法を示します。

```azurecli
az provider show --namespace Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```


## <a name="supported-api-versions"></a>サポートされる API バージョン
テンプレートをデプロイするとき、各リソースの作成に使用する API バージョンを指定する必要があります。 API バージョンは、リリース プロバイダーがリリースする REST API のバージョンに一致します。 リソース プロバイダーは、新しい機能を有効にすると、REST API の新しいバージョンをリリースします。 そのため、テンプレートで指定した API のバージョンにより、テンプレートで指定できるプロパティが変わります。 通常、テンプレートを作成するときは、最新の API バージョンを選択します。 既存のテンプレートの場合、以前の API バージョンの使用を続けるか、テンプレートを最新版に更新して新しい機能を最大限に活用するか決定できます。

### <a name="portal"></a>ポータル
サポートされている API バージョンは、サポートされているリージョンと同様の方法 (前の説明のとおり) で確認できます。

### <a name="rest-api"></a>REST API
リソースの種類で使用可能な API バージョンを確認するには、 [すべてのリソース プロバイダーの一覧表示](https://docs.microsoft.com/rest/api/resources/providers#Providers_List) 操作を利用します。 

### <a name="powershell"></a>PowerShell
次の例では、特定のリソースの種類で利用できる API バージョンを取得する方法を示します。

```powershell
    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

次のように出力されます。

```powershell
2015-08-01
2015-07-01
2015-06-01
2015-05-01
2015-04-01
2015-02-01
2014-11-01
2014-06-01
2014-04-01-preview
2014-04-01
```

### <a name="azure-cli"></a>Azure CLI
次のコマンドを使用して、リソース プロバイダーで使用可能な API バージョンを取得します。

```azurecli
az provider show --namespace Microsoft.Web --query "resourceTypes[?resourceType=='sites'].apiVersions"
```

## <a name="next-steps"></a>次のステップ
* リソース マネージャーのテンプレートの作成の詳細については、 [Azure リソース マネージャーのテンプレートの作成](resource-group-authoring-templates.md)に関するページを参照してください。
* リソースをデプロイする方法を確認するには、「 [Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy.md)」を参照してください。


