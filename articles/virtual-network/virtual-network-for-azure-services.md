---
title: Azure サービス用の仮想ネットワーク
titlesuffix: Azure Virtual Network
description: 仮想ネットワークにリソースをデプロイすることの利点について説明します。 仮想ネットワークに置いたリソースは互いに通信できるほか、オンプレミスのリソースと通信できます。トラフィックがインターネットを通過することがありません。
services: virtual-network
documentationcenter: na
author: malopMSFT
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: malop;kumud
ms.openlocfilehash: c9b2f7244731be67628776b032e041457900353c
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58886422"
---
# <a name="virtual-network-integration-for-azure-services"></a>Azure サービスの仮想ネットワーク統合

Azure 仮想ネットワークに Azure サービスを統合すると、仮想ネットワーク内の仮想マシンまたはコンピューティング リソースからサービスにプライベート アクセスできるようになります。
次のオプションを使用して、仮想ネットワークで Azure サービスを統合することができます。
- サービスの専用インスタンスを仮想ネットワークにデプロイする。 サービスは、仮想ネットワーク内で、また、オンプレミス ネットワークからプライベート アクセスできます。
- サービス エンドポイント経由で仮想ネットワークをサービスまで延ばす。 サービス エンドポイントを使用することで、仮想ネットワークに個々のサービス リソースを結び付けることができます。

仮想ネットワークに複数の Azure サービスを統合するには、上記のパターンの 1 つ以上を組み合わせることができます。 たとえば、仮想ネットワークに HDInsight をデプロイし、サービス エンドポイントを介して HDInsight サブネットにストレージ アカウントを結び付けることができます。
 
## <a name="deploy-azure-services-into-virtual-networks"></a>仮想ネットワークに Azure サービスをデプロイする

[仮想ネットワーク](virtual-networks-overview.md)に専用の Azure サービスをデプロイすると、プライベート IP アドレスを利用してサービス リソースに非公開で通信できます。

![仮想ネットワークにデプロイされたサービス](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

仮想ネットワーク内にサービスをデプロイすると、次のことができるようになります。

- 仮想ネットワーク内のリソースは、プライベート IP アドレスを利用し、非公開で互いと通信できます。 たとえば、仮想ネットワーク内で、HDInsight と仮想マシンで実行されている SQL Server の間でデータを直接転送できます。
- オンプレミスのリソースは、[サイト間 VPN (VPN Gateway)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) または [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) とプライベート IP アドレスを利用し、仮想ネットワーク内のリソースにアクセスできます。
- 仮想ネットワーク内のリソースがプライベート IP アドレスで互いに通信できるように仮想ネットワークを[ピアリング](virtual-network-peering-overview.md)できます。
- 仮想ネットワーク内のサービス インスタンスは Azure サービスにより完全に管理され、インスタンスの正常性を監視し、負荷に基づいて必要な規模を与えます。
- サービス インスタンスは、仮想ネットワークのサブネットにデプロイされます。 ネットワークの着信および送信アクセスは、サービスで提供されるガイダンスに基づき、サブネットの[ネットワーク セキュリティ グループ](security-overview.md#network-security-groups)経由で開く必要があります。
- 場合によっては、サブネットが特定のサービスをホストできる明示的な識別子として、[委任されたサブネット](virtual-network-manage-subnet.md#add-a-subnet)がサービスで必要になることがあります。 サブネットの委任では、サブネットにサービス固有のリソースを作成するための明示的なアクセス許可がサービスに与えられます。

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>仮想ネットワークにデプロイできるサービス

|Category|Service|
|-|-|
| Compute | 仮想マシン:[Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[仮想マシン スケール セット](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[クラウド サービス](https://msdn.microsoft.com/library/azure/jj156091):仮想ネットワーク (クラシック) のみ<br/> [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)  |
| ネットワーク | [Application Gateway - WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[ネットワーク仮想アプライアンス](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn) 
|データ|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure SQL Database Managed Instance](../sql-database/sql-database-managed-instance-connectivity-architecture.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
Analytics | [Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |
| ID | [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |
| Containers | [Azure Kubernetes Service (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Container Instance (ACI)](https://www.aka.ms/acivnet)<br/>[Azure Container Service エンジン](https://github.com/Azure/acs-engine)と Azure Virtual Network CNI [プラグイン](https://github.com/Azure/acs-engine/tree/master/examples/vnet)|
| Web | [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[App Service 環境](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|
| ホストされている | [Azure の専用 HSM](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|
|||



## <a name="service-endpoints-for-azure-services"></a>Azure サービスのサービス エンドポイント

一部の Azure サービスは仮想ネットワークでデプロイできません。 必要であれば、仮想ネットワークのサービス エンドポイントを有効にすることで、一部のサービス リソースへのアクセスを特定の仮想ネットワーク サブネットのみに制限できます。  [仮想ネットワーク サービス エンドポイント](virtual-network-service-endpoints-overview.md)と、エンドポイントを有効にできるサービスの詳細について確認してください。
