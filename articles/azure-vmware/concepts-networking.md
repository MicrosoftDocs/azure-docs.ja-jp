---
title: 概念 - ネットワークの相互接続性
description: Azure VMware Solution におけるネットワークと相互接続性の重要な側面とユース ケースについて説明します。
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: eb701a7fca2be08d70e603734b9c4d8e266959cd
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2021
ms.locfileid: "99475422"
---
# <a name="azure-vmware-solution-networking-and-interconnectivity-concepts"></a>Azure VMware Solution におけるネットワークと相互接続性の概念

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

相互接続性に関する有益な観点は、2 種類の Azure VMware Solution プライベート クラウドの実装について検討することです。

1. [**Azure のみの基本的な相互接続性**](#azure-virtual-network-interconnectivity)では、Azure の仮想ネットワークを 1 つだけ使用してプライベート クラウドを管理および使用できます。 この実装は、Azure VMware Solution の評価、またはオンプレミス環境からのアクセスを必要としない実装に最適です。

1. [**オンプレミスからプライベート クラウドへの完全な相互接続性**](#on-premises-interconnectivity)では、Azure のみの基本的な実装を拡張して、オンプレミスと Azure VMware Solution プライベート クラウドの間の相互接続性が含まれるようにします。
 
この記事では、要件や制限事項など、ネットワークと相互接続性を確立するいくつかの重要な概念について説明します。 また、2 種類の Azure VMware Solution プライベート クラウドの相互接続性の実装についても詳しく説明します。 この記事では、Azure VMware Solution で適切に動作するようにネットワークを構成するうえで把握しておく必要のある情報について説明します。

## <a name="azure-vmware-solution-private-cloud-use-cases"></a>Azure VMware Solution プライベート クラウドのユース ケース

Azure VMware Solution プライベート クラウドには、次のようなユースケースがあります。
- クラウド内の新しい VMware VM ワークロード
- クラウドへの VM ワークロードのバースト (オンプレミスから Azure VMware Solution のみ)
- クラウドへの VM ワークロードの移行 (オンプレミスから Azure VMware Solution のみ)
- ディザスター リカバリー (Azure VMware Solution から Azure VMware Solution、またはオンプレミスから Azure VMware Solution)
- Azure サービスの使用

> [!TIP]
> Azure VMware Solution サービスのすべてのユース ケースは、オンプレミスからプライベート クラウドへの接続で有効になります。

## <a name="azure-virtual-network-interconnectivity"></a>Azure 仮想ネットワークの相互接続性

仮想ネットワークからプライベート クラウドへの実装では、Azure VMware Solution プライベート クラウドの管理、プライベート クラウドのワークロードの使用、ExpressRoute 接続経由での Azure サービスへのアクセスが可能になります。 

下の図は、プライベート クラウドのデプロイ時に確立される、基本的なネットワークの相互接続性を示しています。 これは、Azure 内の仮想ネットワークとプライベート クラウド間の ExpressRoute ベースの論理ネットワークを示しています。 相互接続性により、主要なユース ケースのうち次の 3 つが実現します。
* オンプレミス システムからではなく Azure サブスクリプション内の VM からアクセス可能な vCenter Server と NSX-T Manager への受信アクセス。 
* VM から Azure サービスへの発信アクセス。 
* プライベート クラウドを実行するワークロードの受信アクセスと消費。

:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="仮想ネットワークからプライベート クラウドへの基本的な接続" border="false":::

## <a name="on-premises-interconnectivity"></a>オンプレミスの相互接続性

仮想ネットワークおよびオンプレミスからプライベート クラウドへの完全な実装では、オンプレミス環境から Azure VMware Solution のプライベート クラウドにアクセスできます。 この実装は、前のセクションで説明した基本的な実装を拡張したものです。 この実装では、基本的な実装と同様に ExpressRoute 回線が必要ですが、これはオンプレミス環境から Azure 内のプライベート クラウドに接続するために使用されます。 

下の図は、オンプレミスからプライベート クラウドへの相互接続性を示しています。これにより、次のユース ケースが実現されます。
* ホット/コールド Cross-vCenter vMotion
* オンプレミスから Azure VMware Solution プライベート クラウドへの管理アクセス

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="仮想ネットワークおよびオンプレミスからプライベート クラウドへの完全な接続" border="false":::

プライベート クラウドへの完全な相互接続性を実現するには、ExpressRoute Global Reach を有効にしてから、Azure portal で Global Reach の承認キーとプライベート ピアリング ID を要求します。 この承認キーとピアリング ID を使用して、サブスクリプション内の ExpressRoute 回線と新しいプライベート クラウド用の ExpressRoute 回線との間の Global Reach を確立します。 リンクされると、2 つの ExpressRoute 回線によって、オンプレミス環境からプライベート クラウドへとネットワーク トラフィックがルーティングされます。  承認キーとピアリング ID を要求して使用する手順については、[プライベート クラウドへの ExpressRoute Global Reach ピアリングの作成に関するチュートリアル](tutorial-expressroute-global-reach-private-cloud.md)を参照してください。

## <a name="next-steps"></a>次のステップ 

これらのネットワークと相互接続性の概念について理解したので、次のことについて学習します。

- [Azure VMware Solution のストレージの概念](concepts-storage.md)。
- [Azure VMware Solution の ID の概念](concepts-identity.md)

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->

