---
title: 概念 - ネットワークの相互接続性
description: Azure VMware Solution におけるネットワークと相互接続性の重要な側面とユース ケースについて説明します。
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 3420f6aa61ced7632175f3e12edda9de72639517
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750566"
---
# <a name="azure-vmware-solution-preview-networking-and-interconnectivity-concepts"></a>Azure VMware Solution プレビューにおけるネットワークと相互接続性の概念

Azure VMware Solution (AVS) は、オンプレミスおよび Azure ベースの環境またはリソースからユーザーやアプリケーションがアクセスできる VMware プライベート クラウド環境を提供します。 Azure ExpressRoute 接続や VPN 接続などのサービスによって接続が可能になります。 これらのサービスでは、サービスを有効にするための特定のネットワーク アドレス範囲とファイアウォール ポートが必要になります。  

プライベート クラウドをデプロイすると、管理、プロビジョニング、および vMotion 用のプライベート ネットワークが作成されます。 それらは、vCenter および NSX-T Manager へのアクセス、および仮想マシンの vMotion またはデプロイに使用されます。 Azure の VNet またはオンプレミス環境から、それらのすべてのプライベート ネットワークにアクセスできます。 ExpressRoute Global Reach は、プライベート クラウドをオンプレミス環境に接続するために使用されます。この接続には、お使いのサブスクリプションに ExpressRoute 回路を備えた VNet が必要です。

また、プライベート クラウドをデプロイすると、運用ネットワーク上の VM がそれらを使用できるように、インターネットおよび Azure サービスへのアクセスがプロビジョニングされて提供されます。  既定では、新しいプライベート クラウドのインターネット アクセスは無効になっており、いつでも有効または無効にすることができます。

相互接続性に関する有益な観点は、2 種類の AVS プライベート クラウドの実装について検討することです。

1. [**Azure のみの基本的な相互接続性**](#azure-virtual-network-interconnectivity)では、Azure の仮想ネットワークを 1 つだけ使用してプライベート クラウドを管理および使用できます。 この実装は、オンプレミス環境からのアクセスを必要としない、AVS の評価または実装に最適です。

1. [**オンプレミスからプライベート クラウドへの完全な相互接続性**](#on-premises-interconnectivity)では、Azure のみの基本的な実装を拡張して、オンプレミスと AVS プライベート クラウドの間の相互接続性が含まれるようにします。
 
この記事では、要件や制限事項など、ネットワークと相互接続性を確立するいくつかの重要な概念について説明します。 また、2 種類の AVS プライベート クラウドの相互接続性の実装についても詳しく説明します。 この記事では、AVS で正しく動作するようネットワークを構成する上で把握しておく必要のある情報について説明します。

## <a name="avs-private-cloud-use-cases"></a>AVS プライベート クラウドのユース ケース

AVS プライベート クラウドのユース ケースには、以下が含まれます。
- クラウド内の新しい VMware VM ワークロード
- クラウドへの VM ワークロードのバースト (オンプレミスから AVS のみ)
- クラウドへの VM ワークロードの移行 (オンプレミスから AVS のみ)
- ディザスター リカバリー (AVS から AVS、またはオンプレミスから AVS)
- Azure サービスの使用

> [!TIP]
> AVS サービスのすべてのユース ケースは、オンプレミスからプライベート クラウドへの接続で有効になります。

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

- [ネットワーク接続に関する考慮事項と要件](tutorial-network-checklist.md)について詳しく説明します。 
- [プライベート クラウド ストレージの概念](concepts-storage.md)について説明します。


<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->

