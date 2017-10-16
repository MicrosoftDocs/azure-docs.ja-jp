---
title: "Azure サービスの仮想ネットワーク | Microsoft Docs"
description: "仮想ネットワークにリソースをデプロイすることの利点について説明します。 仮想ネットワークに置いたリソースは互いに通信できるほか、オンプレミスのリソースと通信できます。トラフィックがインターネットを通過することがありません。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial
ms.openlocfilehash: 2c3ffb8432fae41b376cc71bb600a0b1c490f345
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="virtual-network-integration-for-azure-services"></a>Azure サービスの仮想ネットワーク統合

Azure 仮想ネットワークに Azure サービスを統合することで、仮想ネットワークにデプロイされたサービスのインスタンスからのプライベート アクセスが可能になります。

次のオプションを使用して、Azure サービスと仮想ネットワークを統合することができます。
- サービスの専用インスタンスを仮想ネットワークに直接デプロイする。 サービスの専用インスタンスには、仮想ネットワーク内で、また、オンプレミス ネットワークからプライベート アクセスできます。
- サービス エンドポイント経由で仮想ネットワークをサービスまで延ばす。 サービス エンドポイントを使用することで、仮想ネットワークに個々のサービス リソースを結び付けることができます。
 
## <a name="deploy-azure-services-into-virtual-networks"></a>仮想ネットワークに Azure サービスをデプロイする

パブリック IP アドレスを利用することで、インターネット経由でほとんどの Azure リソースと通信できます。 [仮想ネットワーク](virtual-networks-overview.md)に Azure サービスをデプロイするとき、プライベート IP アドレスを利用してサービス リソースに非公開で通信できます。

![仮想ネットワークにデプロイされたサービス](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

仮想ネットワーク内にサービスをデプロイすると、次のことができるようになります。

- 仮想ネットワーク内のリソースは、プライベート IP アドレスを利用し、非公開で互いと通信できます。 たとえば、仮想ネットワーク内で、HDInsight と仮想マシンで実行されている SQL Server の間でデータを直接転送できます。
- オンプレミスのリソースは、[サイト間 VPN (VPN Gateway)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) または [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) とプライベート IP アドレスを利用し、仮想ネットワーク内のリソースにアクセスできます。
- 仮想ネットワーク内のリソースがプライベート IP アドレスで互いに通信できるように仮想ネットワークを[ピアリング](virtual-network-peering-overview.md)できます。
- 仮想ネットワーク内のサービス インスタンスは Azure サービスにより完全に管理され、インスタンスの正常性を監視し、負荷に基づいて必要な規模を与えます。
- サービス インスタンスは、仮想ネットワークの専用サブネットにデプロイされます。 ネットワークの着信および送信アクセスは、サービスで提供されるガイダンスに基づき、サブネットの[ネットワーク セキュリティ グループ](security-overview.md#network-security-groups)経由で開く必要があります。

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>仮想ネットワークにデプロイできるサービス

仮想ネットワークに直接デプロイされるサービスにはそれぞれ、経路指定とサブネットの通過を許可するトラフィックの種類に関して固有の要件があります。 詳細については、次を参照してください。 
 
- 仮想マシン: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Service Fabric](../service-fabric/service-fabric-patterns-networking.md?toc=%2fazure%2fvirtual-network%2ftoc.json#existingvnet)
- [仮想マシン スケール セット](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [App Service 環境](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [RedisCache](../redis-cache/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Application Gateway (内部)](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Container Service エンジン](../container-service/container-service-intro.md?toc=%2fazure%2fvirtual-network%2ftoc.json): Azure Container Service は既定の仮想ネットワークを作成します。 [Azure Container Service エンジン](https://github.com/Azure/acs-engine/tree/master/examples/vnet)と併用するカスタム仮想ネットワークを作成できます。
- [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json): 仮想ネットワーク (クラシック) のみ
- [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration): 仮想ネットワーク (クラシック) のみ
- [クラウド サービス](https://msdn.microsoft.com/library/azure/jj156091): 仮想ネットワーク (クラシック) のみ

[内部 Azure Load Balancer](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) をデプロイし、前述の一覧にある多くのリソースの負荷を分散できます。 場合によっては、リソースの作成時に、サービスがロード バランサーを自動的に作成し、デプロイします。

## <a name="service-endpoints-for-azure-services"></a>Azure サービスのサービス エンドポイント

一部の Azure サービスは仮想ネットワークでデプロイできません。 必要であれば、仮想ネットワークのサービス エンドポイントを有効にすることで、一部のサービス リソースへのアクセスを特定の仮想ネットワーク サブネットのみに制限できます。 仮想ネットワーク サービス エンドポイントの詳細については、[こちら](virtual-network-service-endpoints-overview.md)をご覧ください。

現時点では、サービス エンドポイントは以下のサービスでサポートされています。 
- **Azure Storage**: [Azure ストレージ アカウントを仮想ネットワークに結び付ける](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- **Azure SQL Database**: [Azure SQL Database を仮想ネットワークに結び付ける](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="virtual-network-integration-across-multiple-azure-services"></a>複数の Azure サービスにわたる仮想ネットワーク統合

仮想ネットワーク内のサブネットに Azure サービスをデプロイし、そのサブネットに重要なサービス リソースを結び付けることができます。 たとえば、仮想ネットワークに HDInsight をデプロイし、HDInsight サブネットにストレージ アカウントを結び付けることができます。





