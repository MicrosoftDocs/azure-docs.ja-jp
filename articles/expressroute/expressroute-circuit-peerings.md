---
title: 'Azure ExpressRoute: 回線とピアリング'
description: このページでは、ExpressRoute 回線とルーティング ドメイン/ピアリングの概要を示します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: duau
ms.openlocfilehash: 87fed1d2ac4f5fa85c01d7af10bec10c1412744f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92202346"
---
# <a name="expressroute-circuits-and-peering"></a>ExpressRoute 回線とピアリング

ExpressRoute 回線は、オンプレミスのインフラストラクチャを接続プロバイダー経由で Microsoft に接続します。 この記事では、ExpressRoute 回線とルーティング ドメイン/ピアリングについて理解できます。 下の図は、お客様の WAN と Microsoft の接続を論理的に表現したものです。

![ExpressRoute 回線がオンプレミスのインフラストラクチャを接続プロバイダー経由でどのように Microsoft に接続するかを示す図。](./media/expressroute-circuit-peerings/expressroute-basic.png)

> [!IMPORTANT]
> Azure パブリック ピアリングは非推奨となり、新しい ExpressRoute 回線には使用できません。 新しい回線では、Microsoft ピアリングおよびプライベート ピアリングをサポートします。  
>

## <a name="expressroute-circuits"></a><a name="circuits"></a>ExpressRoute 回線

ExpressRoute 回線は、接続プロバイダー経由のオンプレミス インフラストラクチャと Microsoft クラウド サービス間の論理接続を表します。 複数の ExpressRoute 回線を注文することができます。 回線をそれぞれ同じリージョンや異なるリージョンに配置したり、異なる接続プロバイダーを経由して社内に接続したりすることができます。

ExpressRoute 回線は物理エンティティにはマップされません。 回線は、サービス キー (s キー) という標準 GUID によって一意に識別されます。 サービス キーは、Microsoft、接続プロバイダー、およびユーザー間で交換される情報のほんの一部です。 s キーは、セキュリティ目的のシークレットではありません。 ExpressRoute 回線と s キーは 1 対 1 で対応しています。

新しい ExpressRoute 回線には、2 つの独立したピアリングを含めることができます。プライベート ピアリングと Microsoft ピアリングです。 それに対して、既存の ExpressRoute 回線には 3 つのピアリングを含めることができます。Azure パブリック、Azure プライベート、および Microsoft です。 各ピアリングは独立した BGP セッションのペアであり、高可用性のために、それぞれ冗長に構成されます。 ExpressRoute 回線とルーティング ドメインは 1 対 N (1 <= N <= 3) で対応しています。 ExpressRoute 回線 は、回線ごとに 1 つ、2 つ、または 3 つすべてのピアリングを使用することができます。

各接続には固定帯域幅 (50 Mbps、100 Mbps、200 Mbps、500 Mbps、1 Gbps、10 Gbps) があり、接続プロバイダーとピアリング場所にマップされます。 選択する帯域幅は、すべての回線ピアリングにわたって共有されます。

### <a name="quotas-limits-and-limitations"></a><a name="quotas"></a>クォータ、制限、および制限事項

既定のクォータと制限は、すべての ExpressRoute 回線に適用されます。 クォータに関する最新情報については、「 [Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md) 」ページをご覧ください。

## <a name="expressroute-peering"></a><a name="routingdomains"></a>ExpressRoute ピアリング

ExpressRoute 回線には、複数のルーティング ドメイン/ピアリングが関連付けられます。Azure パブリック、Azure プライベート、および Microsoft です。 各ピアリングは、高可用性のために、ルーターのペアにまったく同様に (アクティブ/アクティブまたは負荷分散構成で) 構成されます。 Azure サービスは *Azure パブリック* および *Azure プライベート* として分類され、IP アドレス指定スキームを表します。

![Azure パブリック、Azure プライベート、および Microsoft ピアリングが ExpressRoute 回線でどのように構成されているかを示す図。](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="azure-private-peering"></a><a name="privatepeering"></a>Azure プライベート ピアリング

Azure Compute Services、つまり、仮想ネットワーク内にデプロイされる仮想マシン (IaaS) とクラウド サービス (PaaS) には、プライベート ピアリング ドメイン経由で接続できます。 プライベート ピアリング ドメインは、お客様のコア ネットワークを Microsoft Azure に信頼できる方法で拡張したものと言えます。 コア ネットワークと Azure Virtual Network (VNET) の間に双方向接続を設定できます。 このピアリングにより、仮想マシンとクラウド サービスにプライベート IP アドレスで直接接続できます。  

プライベート ピアリング ドメインには複数の仮想ネットワークを接続できます。 上限と制限事項については、 [FAQ のページ](expressroute-faqs.md) を参照してください。 制限に関する最新情報については、「 [Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md) 」ページをご覧ください。  ルーティング構成の詳細については、 [ルーティング](expressroute-routing.md) に関するページを参照してください。

### <a name="microsoft-peering"></a><a name="microsoftpeering"></a>Microsoft ピアリング

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

Microsoft オンライン サービス (Microsoft 365 および Azure PaaS サービス) への接続は、Microsoft ピアリング経由で行われます。 Microsoft ピアリング ルーティング ドメイン経由で、ご使用の WAN と Microsoft クラウド サービスの双方向接続を実現できます。 お客様または接続プロバイダーが所有するパブリック IP アドレスのみを使用して Microsoft クラウド サービスに接続する必要があり、定義されてするすべての規則を遵守する必要があります。 詳細については、[ExpressRoute の前提条件](expressroute-prerequisites.md)に関するページを参照してください。

サポートされるサービス、料金、および構成の詳細については、 [FAQ のページ](expressroute-faqs.md) を参照してください。 Microsoft ピアリングのサポートを提供している接続プロバイダーの一覧については、 [ExpressRoute の場所](expressroute-locations.md) に関するページを参照してください。

## <a name="peering-comparison"></a><a name="peeringcompare"></a>ピアリングの比較

次の表では、3 つのピアリングを比較しています。

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

ExpressRoute 回線の一部として 1 つ以上のルーティング ドメインを有効にすることができます。 すべてのルーティング ドメインを 1 つのルーティング ドメインに取り込みたい場合は、同じ VPN 上に配置するように選択できます。 また、上図に似た別のルーティング ドメイン上に配置することもできます。 推奨構成は、プライベート ピアリングをコア ネットワークに直接接続し、パブリック ピアリングと Microsoft ピアリングのリンクを DMZ に接続する構成です。

各ピアリングには、個別の BGP セッションが必要です (ピアリングの種類ごとに 1 つのペア)。 BGP セッションのペアによって、高可用性リンクが実現されます。 レイヤー 2 接続プロバイダーを経由して接続している場合、ルーティングの構成と管理はお客様の責任となります。 詳細については、ExpressRoute を設定する [ワークフロー](expressroute-workflows.md) を参照してください。

## <a name="expressroute-health"></a><a name="health"></a>ExpressRoute の正常性

[Network Performance Monitor](../networking/network-monitoring-overview.md) (NPM) を使用して、ExpressRoute 回線の可用性、VNet の接続、および帯域幅の使用状況を監視できます。

NPM は、Azure プライベート ピアリングと Microsoft ピアリングの正常性を監視します。 詳細については、こちらの[ブログ](https://azure.microsoft.com/blog/monitoring-of-azure-expressroute-in-preview/)を参照してください。

## <a name="next-steps"></a>次のステップ

* サービス プロバイダーを検索します。 [ExpressRoute のサービス プロバイダーと提供地域](expressroute-locations.md)に関するページをご覧ください。
* すべての前提条件を満たしていることを確認します。 「 [Azure ExpressRoute の前提条件](expressroute-prerequisites.md)」を参照してください。
* ExpressRoute 接続を構成します。
  * [ExpressRoute 回線の作成と管理](expressroute-howto-circuit-portal-resource-manager.md)
  * [ExpressRoute 回線用のルーティング (ピアリング) の構成](expressroute-howto-routing-portal-resource-manager.md)