---
title: Azure Availability Zones でのゾーン冗長仮想ネットワーク ゲートウェイについて | Microsoft Docs
description: Availability Zones での VPN Gateway と ExpressRoute ゲートウェイについて学習します。
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: cherylmc
ms.openlocfilehash: c13e7b507291d7671ac861fc7a8683c87be947a1
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896646"
---
# <a name="about-zone-redundant-virtual-network-gateways-in-azure-availability-zones"></a>Azure Availability Zones でのゾーン冗長仮想ネットワーク ゲートウェイについて

[Azure Availability Zones](../availability-zones/az-overview.md) に、VPN ゲートウェイと ExpressRoute ゲートウェイをデプロイできます。 これにより、仮想ネットワーク ゲートウェイに回復性、スケーラビリティ、高可用性が提供されます。 Azure Availability Zones にゲートウェイをデプロイすると、オンプレミス ネットワークの Azure への接続をゾーン レベルの障害から保護しながら、ゲートウェイを 1 つのリージョン内に物理的かつ論理的に分離できます。

### <a name="zrgw"></a>ゾーン冗長ゲートウェイ

複数の可用性ゾーンにわたって仮想ネットワーク ゲートウェイを自動的にデプロイするには、ゾーン冗長仮想ネットワーク ゲートウェイを使用します。 ゾーン冗長ゲートウェイでは、Azure 上のミッション クリティカルでスケーラブルなサービスへのアクセスにおいて、ゾーン回復性のメリットを活かすことができます。

<br>
<br>

![ゾーン冗長ゲートウェイの図](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zgw"></a>ゾーン ゲートウェイ

特定の 1 つのゾーンにゲートウェイをデプロイするには、ゾーン ゲートウェイを使用できます。 ゾーン ゲートウェイをデプロイすると、すべてのゲートウェイ インスタンスが同じ可用性ゾーンにデプロイされます。

<br>
<br>

![ゾーン ゲートウェイの図](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gwskus"></a>ゲートウェイの SKU

ゾーン冗長ゲートウェイとゾーン ゲートウェイは、新しいゲートウェイ SKU として使用できます。 Azure AZ リージョン内に新しい仮想ネットワーク ゲートウェイ SKU が追加されました。 これらの SKU は ExpressRoute および VPN Gateway の対応する既存の SKU と似ていますが、ゾーン冗長ゲートウェイとゾーン ゲートウェイに固有であるという点が異なります。 これらの SKU は、SKU 名に含まれる "AZ" で特定できます。

ゲートウェイ SKU の詳細については、[VPN ゲートウェイ SKU](vpn-gateway-about-vpngateways.md#gwsku) と [ExpressRoute ゲートウェイ SKU](../expressroute/expressroute-about-virtual-network-gateways.md#gwsku) に関する記事を参照してください。

## <a name="pipskus"></a>パブリック IP の SKU

ゾーン冗長ゲートウェイとゾーン ゲートウェイはいずれも、Azure パブリック IP リソースの *Standard* SKU に依存します。 Azure パブリック IP リソースの構成によって、デプロイするゲートウェイがゾーン冗長ゲートウェイになるかゾーン ゲートウェイになるかが決まります。 *Basic* SKU を使用してパブリック IP リソースを作成した場合、ゲートウェイはゾーン冗長性を持たず、ゲートウェイ リソースはリージョン固有となります。

### <a name="pipzrg"></a>ゾーン冗長ゲートウェイ

ゾーンを指定せずに **Standard** パブリック IP SKU を使用してパブリック IP アドレスを作成した場合、動作はゲートウェイが VPN ゲートウェイであるか ExpressRoute ゲートウェイであるかによって異なります。 

* VPN ゲートウェイの場合は、2 つのゲートウェイ インスタンスがこれらの 3 つのゾーンの 2 つにデプロイされ、ゾーン冗長性が提供されます。 
* ExpressRoute ゲートウェイの場合は、3 つ以上のインスタンスを作成できるため、ゲートウェイは 3 つのゾーンすべてにわたることができます。

### <a name="pipzg"></a>ゾーン ゲートウェイ

**Standard** パブリック IP SKU を使用してパブリック IP アドレスを作成し、ゾーン (1、2、または 3) を指定した場合、すべてのゲートウェイ インスタンスが同じゾーンにデプロイされます。

### <a name="piprg"></a>リージョン ゲートウェイ

**Basic** パブリック IP SKU を使用してパブリック IP アドレスを作成した場合、ゲートウェイはリージョン ゲートウェイとしてデプロイされ、ゲートウェイにはゾーン冗長性が組み込まれません。

## <a name="faq"></a>FAQ

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>これらの新しい SKU をデプロイすると何が変わりますか。

お客様の観点からは、ゾーン冗長性を備えたゲートウェイをデプロイできるようになります。 つまり、すべてのゲートウェイ インスタンスが複数の Azure Availability Zones にわたってデプロイされ、それぞれの Availability Zone が別々の障害ドメインおよび更新ドメインとなります。 これにより、ゲートウェイの信頼性、可用性、およびゾーン障害に対する回復性が向上します。

### <a name="can-i-use-the-azure-portal"></a>Azure Portal を使用することはできますか。

はい、Azure portal を使用して新しい SKU をデプロイできます。 ただし、これらの新しい SKU が表示されるのは、Azure Availability Zones がある Azure リージョンのみとなります。

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>新しい SKU を使用する場合、どのようなリージョンを利用できますか。

新しい SKU は、Azure Availability Zones がある Azure リージョン (米国中部、フランス中部、北ヨーロッパ、西ヨーロッパ、米国西部 2、米国東部、米国東部 2、東南アジア、東日本、英国南部) でご利用いただけます。 将来的には、他の Azure パブリック リージョンでもゾーン冗長ゲートウェイを利用できるようになる予定です。

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>既存の仮想ネットワーク ゲートウェイをゾーン冗長ゲートウェイまたはゾーン ゲートウェイに変更、移行、アップグレードすることはできますか。

既存の仮想ネットワーク ゲートウェイからゾーン冗長ゲートウェイまたはゾーン ゲートウェイへの移行は、現時点ではサポートされていません。 しかし、既存のゲートウェイを削除して、ゾーン冗長ゲートウェイまたはゾーン ゲートウェイを再作成することは可能です。

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>VPN ゲートウェイと Express Route ゲートウェイの両方を同じ仮想ネットワークにデプロイできますか。

同じ仮想ネットワーク内での VPN Gateway と Express Route ゲートウェイの共存がサポートされています。 ただし、ゲートウェイ サブネット用に、/27 IP アドレス範囲を予約する必要があります。

## <a name="next-steps"></a>次の手順

[ゾーン冗長仮想ネットワーク ゲートウェイの作成](create-zone-redundant-vnet-gateway.md)
