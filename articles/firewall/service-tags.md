---
title: Azure Firewall サービス タグの概要
description: この記事は、Azure Firewall サービス タグの概要です。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 2/25/2019
ms.author: victorh
ms.openlocfilehash: 1d03d896de947fcc938619c52a3690962a0d2d6c
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805622"
---
# <a name="azure-firewall-service-tags"></a>Azure Firewall サービス タグ

サービス タグは IP アドレス プレフィックスのグループを表し、セキュリティ規則の作成の複雑さを最小限に抑えるのに役立ちます。 独自のサービス タグを作成したり、タグに含まれる IP アドレスを指定したりすることはできません。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

Azure Firewall サービス タグは、ネットワーク ルールの宛先フィールドで使用できます。 特定の IP アドレスの代わりにそれらを使用できます。

## <a name="supported-service-tags"></a>サポートされるサービス タグ

次のサービス タグを Azure ファイアウォール ネットワーク ルール内で使うことができます。

* **AzureCloud** (Resource Manager のみ):このタグは、すべての[データセンターのパブリック IP アドレス](https://www.microsoft.com/download/details.aspx?id=41653)を含む Azure の IP アドレス空間を表します。 値として *AzureCloud* を指定した場合、Azure パブリック IP アドレスへのトラフィックが許可または拒否されます。 特定の[リージョン](https://azure.microsoft.com/regions)の AzureCloud に対するアクセスのみを許可する場合は、リージョンを指定することができます。 たとえば、米国東部リージョンの Azure AzureCloud へのアクセスのみを許可する場合は、サービス タグとして *AzureCloud.EastUS* と指定できます。 
* **AzureTrafficManager** (Resource Manager のみ):このタグは、Azure Traffic Manager プローブ IP アドレスに対する IP アドレス空間を表します。 Traffic Manager プローブ IP アドレスについて詳しくは、[Azure Traffic Manager の FAQ](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs) に関するページをご覧ください。 
* **Storage** (Resource Manager のみ):このタグは、Azure Storage サービスの IP アドレス空間を表します。 値として *Storage* を指定した場合、ストレージへのトラフィックが許可または拒否されます。 特定の[リージョン](https://azure.microsoft.com/regions)のストレージに対するアクセスのみを許可する場合は、リージョンを指定することができます。 たとえば、米国東部リージョンの Azure Storage へのアクセスのみを許可する場合は、サービス タグとして *Storage.EastUS* と指定できます。 タグはサービスだけを表し、サービスの特定のインスタンスは表しません。 たとえば、このタグは Azure Storage サービスを表しますが、特定の Azure Storage アカウントは表しません。
* **Sql** (Resource Manager のみ):このタグは、Azure SQL Database サービスおよび Azure SQL Data Warehouse サービスのアドレス プレフィックスを表します。 値として *Sql* を指定した場合、SQL へのトラフィックが許可または拒否されます。 特定の[リージョン](https://azure.microsoft.com/regions)の SQL へのアクセスのみを許可する場合は、リージョンを指定することができます。 たとえば、米国東部リージョンの Azure SQL Database へのアクセスのみを許可する場合は、サービス タグとして *Sql.EastUS* と指定できます。 タグはサービスだけを表し、サービスの特定のインスタンスは表しません。 たとえば、このタグは Azure SQL Database サービスを表しますが、特定の SQL データベースや SQL サーバーは表しません。
* **AzureCosmosDB** (Resource Manager のみ):このタグは、Azure Cosmos Database サービスのアドレス プレフィックスを表します。 値として *AzureCosmosDB* を指定した場合、AzureCosmosDB へのトラフィックが許可または拒否されます。 特定の[リージョン](https://azure.microsoft.com/regions)の AzureCosmosDB へのアクセスのみを許可する場合は、AzureCosmosDB.[リージョン名] の形式で、リージョンを指定できます。
* **AzureKeyVault** (Resource Manager のみ):このタグは、Azure KeyVault サービスのアドレス プレフィックスを表します。 値として *AzureKeyVault* を指定した場合、AzureKeyVault へのトラフィックが許可または拒否されます。 特定の[リージョン](https://azure.microsoft.com/regions)の AzureKeyVault へのアクセスのみを許可する場合は、AzureKeyVault.[リージョン名] の形式で、リージョンを指定できます。
* **EventHub** (Resource Manager のみ):このタグは、Azure EventHub サービスのアドレス プレフィックスを表します。 値として *EventHub* を指定した場合、EventHub へのトラフィックが許可または拒否されます。 特定の[リージョン](https://azure.microsoft.com/regions)の EventHub へのアクセスのみを許可する場合は、EventHub.[リージョン名] の形式で、リージョンを指定できます。 
* **ServiceBus** (Resource Manager のみ):このタグは、Azure ServiceBus サービスのアドレス プレフィックスを表します。 値として *ServiceBus* を指定した場合、ServiceBus へのトラフィックが許可または拒否されます。 特定の[リージョン](https://azure.microsoft.com/regions)の ServiceBus へのアクセスのみを許可する場合は、ServiceBus.[リージョン名] の形式で、リージョンを指定できます。
* **MicrosoftContainerRegistry** (Resource Manager のみ):このタグは、Microsoft Container Registry サービスのアドレス プレフィックスを表します。 値として *MicrosoftContainerRegistry* を指定した場合、MicrosoftContainerRegistry へのトラフィックが許可または拒否されます。 特定の[リージョン](https://azure.microsoft.com/regions)の MicrosoftContainerRegistry へのアクセスのみを許可する場合は、MicrosoftContainerRegistry.[リージョン名] の形式で、リージョンを指定できます。
* **AzureContainerRegistry** (Resource Manager のみ):このタグは、Azure Container Registry サービスのアドレス プレフィックスを表します。 値として *AzureContainerRegistry* を指定した場合、AzureContainerRegistry へのトラフィックが許可または拒否されます。 特定の[リージョン](https://azure.microsoft.com/regions)の AzureContainerRegistry へのアクセスのみを許可する場合は、AzureContainerRegistry.[リージョン名] の形式で、リージョンを指定できます。 
* **AppService** (Resource Manager のみ):このタグは、Azure AppService サービスのアドレス プレフィックスを表します。 値として *AppService* を指定した場合、AppService へのトラフィックが許可または拒否されます。 特定の[リージョン](https://azure.microsoft.com/regions)の AppService へのアクセスのみを許可する場合は、AppService.[リージョン名] の形式で、リージョンを指定できます。 
* **AppServiceManagement** (Resource Manager のみ):このタグは、Azure AppService Management サービスのアドレス プレフィックスを表します。 値として *AppServiceManagement* を指定した場合、AppServiceManagement へのトラフィックが許可または拒否されます。 
* **ApiManagement** (Resource Manager のみ):このタグは、Azure API Management サービスのアドレス プレフィックスを表します。 値として *ApiManagement* を指定した場合、ApiManagement へのトラフィックが許可または拒否されます。  
* **AzureConnectors** (Resource Manager のみ):このタグは、Azure Connectors サービスのアドレス プレフィックスを表します。 値として *AzureConnectors* を指定した場合、AzureConnectors へのトラフィックが許可または拒否されます。 特定の[リージョン](https://azure.microsoft.com/regions)の AzureConnectors へのアクセスのみを許可する場合は、AzureConnectors.[リージョン名] の形式で、リージョンを指定できます。
* **AzureDataLake** (Resource Manager のみ):このタグは、Azure Data Lake サービスのアドレス プレフィックスを表します。 値として *AzureDataLake* を指定した場合、AzureDataLake へのトラフィックが許可または拒否されます。
* **AzureActiveDirectory** (Resource Manager のみ):このタグは、AzureActiveDirectory サービスのアドレス プレフィックスを表します。 値として *AzureActiveDirectory* を指定した場合、AzureActiveDirectory へのトラフィックが許可または拒否されます。
* **AzureMonitor** (Resource Manager のみ): このタグは、AzureMonitor サービスのアドレス プレフィックスを表します。 値として *AzureMonitor* を指定した場合、AzureMonitor へのトラフィックが許可または拒否されます。
* **ServiceFabric** (Resource Manager のみ): このタグは、ServiceFabric サービスのアドレス プレフィックスを表します。 値として *ServiceFabric* を指定した場合、ServiceFabric へのトラフィックが許可または拒否されます。
* **AzureMachineLearning** (Resource Manager のみ): このタグは、AzureMachineLearning サービスのアドレス プレフィックスを表します。 値として *AzureMachineLearning* を指定した場合、AzureMachineLearning へのトラフィックが許可または拒否されます。

## <a name="next-steps"></a>次の手順

Azure Firewall ルールの詳細については、「[Azure Firewall ルール処理ロジック](rule-processing.md)」を参照してください。