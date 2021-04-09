---
title: 概念 - ネットワークの相互接続性
description: Azure VMware Solution におけるネットワークと相互接続性の重要な側面とユース ケースについて説明します。
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 4c964151c49e2fea56031dd24bacf4655753a18d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491811"
---
# <a name="azure-vmware-solution-networking-and-interconnectivity-concepts"></a>Azure VMware Solution におけるネットワークと相互接続性の概念

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Azure VMware Solution プライベート クラウドにおける相互接続性には、次の 2 つの方法があります。

- [**Azure のみの基本的な相互接続性**](#azure-virtual-network-interconnectivity)では、Azure の仮想ネットワークを 1 つだけ使用してプライベート クラウドを管理および使用できます。 この実装は、Azure VMware Solution の評価、またはオンプレミス環境からのアクセスを必要としない実装に最適です。

- [**オンプレミスからプライベート クラウドへの完全な相互接続性**](#on-premises-interconnectivity)では、Azure のみの基本的な実装を拡張して、オンプレミスと Azure VMware Solution プライベート クラウドの間の相互接続性が含まれるようにします。
 
この記事では、要件や制限事項など、ネットワークと相互接続性を確立する重要な概念について説明します。 この記事では、Azure VMware Solution で動作するようにネットワークを構成するうえで把握しておく必要のある情報について説明します。

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

Azure 仮想ネットワークを、Azure VMware Solution のプライベート クラウド実装と相互接続できます。 自分の Azure VMware Solution プライベート クラウドを管理し、プライベート クラウドでワークロードを使用して、他の Azure サービスにアクセスできます。

下の図は、プライベート クラウドのデプロイ時に確立される、基本的なネットワークの相互接続性を示しています。 これは、Azure 内の仮想ネットワークとプライベート クラウド間の論理ネットワークを示しています。 この接続は、Azure VMware Solution サービスの一部であるバックエンド ExpressRoute を介して確立されます。 相互接続性により、次の主要なユース ケースが実現します。

- Azure サブスクリプション内の VM からアクセス可能な vCenter Server と NSX-T Manager への受信アクセス。
- プライベート クラウド上の VM から Azure サービスへの発信アクセス。
- プライベート クラウドで実行中のワークロードの受信アクセス。


:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="仮想ネットワークからプライベート クラウドへの基本的な接続" border="false":::

## <a name="on-premises-interconnectivity"></a>オンプレミスの相互接続性

完全に相互接続されたシナリオでは、Azure VMware Solution に Azure 仮想ネットワークとオンプレミスからアクセスできます。 この実装は、前のセクションで説明した基本的な実装を拡張したものです。 オンプレミスから Azure のAzure VMware Solution プライベート クラウドに接続するには、ExpressRoute 回線が必要です。

下の図は、オンプレミスからプライベート クラウドへの相互接続性を示しています。これにより、次のユース ケースが実現されます。

- オンプレミスと Azure VMware Solution の間のホット/コールド vCenter vMotion。
- オンプレミスから Azure VMware Solution プライベート クラウドへの管理アクセス。

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="仮想ネットワークおよびオンプレミスからプライベート クラウドへの完全な接続" border="false":::

プライベート クラウドへの完全な相互接続性を実現するには、ExpressRoute Global Reach を有効にしてから、Azure portal で Global Reach の承認キーとプライベート ピアリング ID を要求する必要があります。 この承認キーとピアリング ID を使用して、サブスクリプション内の ExpressRoute 回線とプライベート クラウド用の ExpressRoute 回線との間の Global Reach を確立します。 リンクされると、2 つの ExpressRoute 回線によって、オンプレミス環境からプライベート クラウドへとネットワーク トラフィックがルーティングされます。 手順については、[プライベート クラウドへの ExpressRoute Global Reach ピアリングの作成に関するチュートリアル](tutorial-expressroute-global-reach-private-cloud.md)を参照してください。

## <a name="limitations"></a>制限事項
[!INCLUDE [azure-vmware-solutions-limits](includes/azure-vmware-solutions-limits.md)]

## <a name="next-steps"></a>次のステップ 

Azure VMware Solution のネットワークと相互接続性の概念について理解したら、次の事項の学習に進むことができます。

- [Azure VMware Solution のストレージの概念](concepts-storage.md)。
- [Azure VMware Solution の ID の概念](concepts-identity.md)
- [Azure VMware Solution リソースを有効にする方法](enable-azure-vmware-solution.md)

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md
[concepts-storage]: ./concepts-storage.md
