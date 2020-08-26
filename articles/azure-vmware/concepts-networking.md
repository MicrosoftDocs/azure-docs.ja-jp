---
title: 概念 - ネットワークの相互接続性
description: Azure VMware Solution (AVS) でのネットワークと相互接続性の主要な側面とユース ケースについて説明します。
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 6f1f1f5a089781f1f7e882c9c8692f0c845ae485
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88214100"
---
# <a name="azure-vmware-solution-avs-preview-networking-and-interconnectivity-concepts"></a>Azure VMware Solution (AVS) Preview のネットワークと相互接続性の概念

Azure VMware Solution (AVS) プライベート クラウドとオンプレミス環境または Azure の仮想ネットワークとの間におけるネットワークの相互接続性により、プライベート クラウドへのアクセスと使用が可能になります。 この記事では、ネットワークと相互接続性の基礎となる、いくつかの重要な概念について説明します。

相互接続性に関する有益な観点は、2 種類の AVS プライベート クラウドの実装について検討することです。

1. [**Azure のみの基本的な相互接続性**](#azure-virtual-network-interconnectivity)では、Azure の仮想ネットワークを 1 つだけ使用してプライベート クラウドを管理および使用できます。 この実装は、オンプレミス環境からのアクセスを必要としない、AVS の評価または実装に最適です。

1. [**オンプレミスからプライベート クラウドへの完全な相互接続性**](#on-premises-interconnectivity)では、Azure のみの基本的な実装を拡張して、オンプレミスと AVS プライベート クラウドの間の相互接続性が含まれるようにします。
 
2 種類の AVS プライベート クラウド相互接続性の実装とその要件の詳細について、以下のセクションで説明します。

## <a name="avs-private-cloud-use-cases"></a>AVS プライベート クラウドのユース ケース

AVS プライベート クラウドのユース ケースには、以下が含まれます。
- クラウド内の新しい VMware VM ワークロード
- クラウドへの VM ワークロードのバースト (オンプレミスから AVS のみ)
- クラウドへの VM ワークロードの移行 (オンプレミスから AVS のみ)
- ディザスター リカバリー (AVS から AVS、またはオンプレミスから AVS)
- Azure サービスの使用

 AVS サービスのすべてのユース ケースは、オンプレミスからプライベート クラウドへの接続で有効になります。 

## <a name="virtual-network-and-expressroute-circuit-requirements"></a>仮想ネットワークと ExpressRoute 回線の要件
 
サブスクリプション内の仮想ネットワークから接続を作成するときに、ExpressRoute 回線がピアリングによって確立され、Azure portal で要求した承認キーとピアリング ID が使用されます。 ピアリングは、プライベート クラウドと仮想ネットワークの間のプライベートな 1 対 1 の接続です。

> [!NOTE] 
> ExpressRoute 回線は、プライベート クラウドのデプロイには含まれません。 オンプレミスの ExpressRoute 回線については、このドキュメントでは取り扱いません。 プライベート クラウドへのオンプレミス接続が必要な場合は、既存の ExpressRoute 回線のいずれかを使用するか、Azure portal で購入することができます。

プライベート クラウドをデプロイすると、vCenter および NSX-T Manager の IP アドレスが提供されます。 これらの管理インターフェイスにアクセスするには、ご使用のサブスクリプション内の仮想ネットワークに追加のリソースを作成する必要があります。 これらのリソースを作成し、ExpressRoute プライベート ピアリングを確立する手順については、チュートリアルを参照してください。

プライベート クラウドの論理ネットワークには、事前にプロビジョニングされた NSX-T が付属しています。 Tier-0 ゲートウェイと Tier-1 ゲートウェイが事前にプロビジョニングされています。 セグメントを作成し、それを既存の Tier-1 ゲートウェイにアタッチすることも、自分が定義する新しい Tier-1 ゲートウェイにアタッチすることもできます。 NSX-T 論理ネットワーク コンポーネントは、ワークロード間の East-West 接続を提供し、インターネットおよび Azure サービスへの North-South 接続も提供します。 

## <a name="routing-and-subnet-requirements"></a>ルーティングとサブネットの要件

ルーティングは Border Gateway Protocol (BGP) ベースであり、プライベート クラウドのデプロイごとに既定で自動的にプロビジョニングされ、有効になります。 AVS プライベート クラウドでは、次の表に示すように、サブネットの CIDR ネットワーク アドレス ブロックのプレフィックス長が /22 以上であるプライベート クラウド ネットワーク アドレス空間を計画する必要があります。 アドレス ブロックは、サブスクリプションとオンプレミス ネットワークにある他の仮想ネットワークで使用されているアドレス ブロックと重複しないようにする必要があります。 このアドレス ブロック内では、管理、プロビジョニング、vMotion ネットワークが自動的にプロビジョニングされます。

`/22` CIDR ネットワーク アドレス ブロックの例: `10.10.0.0/22`

サブネット:

| ネットワークの使用法             | Subnet | 例        |
| ------------------------- | ------ | -------------- |
| プライベート クラウドの管理  | `/24`  | `10.10.0.0/24` |
| vMotion ネットワーク           | `/24`  | `10.10.1.0/24` |
| VM ワークロード              | `/24`  | `10.10.2.0/24` |
| ExpressRoute ピアリング      | `/24`  | `10.10.3.8/30` |


## <a name="azure-virtual-network-interconnectivity"></a>Azure 仮想ネットワークの相互接続性

仮想ネットワークからプライベート クラウドへの実装では、AVS プライベート クラウドの管理、プライベート クラウドのワークロードの使用、ExpressRoute 接続経由での Azure サービスへのアクセスが可能になります。 

下の図は、プライベート クラウドのデプロイ時に確立される、基本的なネットワークの相互接続性を示しています。 これは、Azure 内の仮想ネットワークとプライベート クラウド間の ExpressRoute ベースの論理ネットワークを示しています。 相互接続性により、主要なユース ケースのうち次の 3 つが実現します。
* オンプレミス システムからではなく Azure サブスクリプション内の VM からアクセス可能な vCenter Server と NSX-T Manager への受信アクセス。 
* VM から Azure サービスへの発信アクセス。 
* プライベート クラウドを実行するワークロードの受信アクセスと消費。

:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="仮想ネットワークからプライベート クラウドへの基本的な接続" border="false":::

## <a name="on-premises-interconnectivity"></a>オンプレミスの相互接続性

仮想ネットワークおよびオンプレミスからプライベート クラウドへの完全な実装では、オンプレミス環境から AVS プライベート クラウドにアクセスできます。 この実装は、前のセクションで説明した基本的な実装を拡張したものです。 この実装では、基本的な実装と同様に ExpressRoute 回線が必要ですが、これはオンプレミス環境から Azure 内のプライベート クラウドに接続するために使用されます。 

下の図は、オンプレミスからプライベート クラウドへの相互接続性を示しています。これにより、次のユース ケースが実現されます。
* ホット/コールド Cross-vCenter vMotion
* オンプレミスから AVS プライベート クラウドへの管理アクセス

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="仮想ネットワークおよびオンプレミスからプライベート クラウドへの完全な接続" border="false":::

プライベート クラウドへの完全な相互接続性を実現するには、ExpressRoute Global Reach を有効にしてから、Azure portal で Global Reach の承認キーとプライベート ピアリング ID を要求します。 この承認キーとピアリング ID を使用して、サブスクリプション内の ExpressRoute 回線と新しいプライベート クラウド用の ExpressRoute 回線との間の Global Reach を確立します。 リンクされると、2 つの ExpressRoute 回線によって、オンプレミス環境からプライベート クラウドへとネットワーク トラフィックがルーティングされます。  承認キーとピアリング ID を要求して使用する手順については、[プライベート クラウドへの ExpressRoute Global Reach ピアリングの作成に関するチュートリアル](tutorial-expressroute-global-reach-private-cloud.md)を参照してください。


## <a name="next-steps"></a>次のステップ 

次のステップでは、[プライベート クラウド ストレージの概念](concepts-storage.md)について説明します。

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->
