---
title: Azure VMware Solution をオンプレミス環境に接続する
description: Azure VMware Solution をオンプレミス環境に接続する方法について説明します。
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 0b26dc4756cb37544c2b2f8c5a75df0ac1a9d629
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491794"
---
# <a name="connect-azure-vmware-solution-to-your-on-premises-environment"></a>Azure VMware Solution をオンプレミス環境に接続する

この記事では、[計画中に収集された情報](production-ready-deployment-steps.md)を引き続き使用して、Azure VMware Solution をオンプレミス環境に接続します。

開始する前に、Azure VMware Solution をオンプレミス環境に接続するための前提条件が 2 つあります。

- オンプレミス環境から Azure への ExpressRoute 回線 1 つ。
- ExpressRoute Global Reach ピアリングのための重複していない /29 CIDR ネットワーク アドレス ブロック。これは、[計画フェーズ](production-ready-deployment-steps.md)の一環として定義したものです。

>[!NOTE]
> VPN 経由での接続は可能ですが、このクイック スタート ドキュメントの範囲外です。

## <a name="establish-an-expressroute-global-reach-connection"></a>ExpressRoute Global Reach 接続を確立する

ExpressRoute Global Reach を使用して Azure VMware Solution プライベート クラウドへのオンプレミス接続を確立するには、「[ピアオンプレミス環境をプライベート クラウドにピアリングする](tutorial-expressroute-global-reach-private-cloud.md)」というチュートリアルに従ってください。

このチュートリアルにより、図に示されているような接続が実現します。

:::image type="content" source="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" alt-text="ExpressRoute Global Reach のオンプレミス ネットワーク接続の図。" lightbox="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" border="false":::

## <a name="verify-on-premises-network-connectivity"></a>オンプレミス ネットワークの接続性を検証する

この時点で、NSX-T ネットワーク セグメントと Azure VMware Solution 管理セグメントが、ExpressRoute によってどこに接続されているか、**オンプレミスのエッジ ルーター** に表示されるはずです。

>[!IMPORTANT]
>すべてのユーザーが異なる環境を使用しており、一部では、これらのルートをオンプレミス ネットワークに再び伝達できるようにする必要があります。  

一部の環境では、ExpressRoute 回線を保護するファイアウォールが配置されています。  ファイアウォールがなく、ルートの排除も行われない場合は、オンプレミス環境から、Azure VMware Solution の vCenter サーバーや [NSX-T セグメント上の VM](deploy-azure-vmware-solution.md#add-a-vm-on-the-nsx-t-network-segment) に対して ping を実行します。 さらに、NSX-T セグメントの VM からは、オンプレミス環境のリソースに到達できます。

## <a name="next-steps"></a>次の手順

次のセクションに進み、VMware HCX をデプロイして構成します

> [!div class="nextstepaction"]
> [VMware HCX をデプロイして構成する](tutorial-deploy-vmware-hcx.md)