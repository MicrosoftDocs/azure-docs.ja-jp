---
title: VMware Solution by CloudSimple - Azure ネットワーク接続
description: Azure 仮想ネットワークを CloudSimple 領域ネットワークに接続する方法について説明します。
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cfd4d65b07cf255ac2b60d6bf8376723a997374e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "77025080"
---
# <a name="azure-network-connections-overview"></a>Azure ネットワーク接続の概要

リージョンで CloudSimple サービスを作成し、ノードを作成すると、次のことができます。

* Azure ExpressRoute 回線を要求して、そのリージョンの CloudSimple ネットワークに接続します。
* Azure ExpressRoute を使用して、CloudSimple リージョン ネットワークを Azure 仮想ネットワークまたはオンプレミス ネットワークに接続します。
* プライベート クラウド環境から、Azure サブスクリプションまたはオンプレミス ネットワークで実行されているサービスへのアクセスを提供します。

ExpressRoute 接続は高帯域幅で、待機時間は短くなります。

## <a name="benefits"></a>メリット

Azure のネットワーク接続により以下をすることができます。

* プライベート クラウド上の仮想マシンのバックアップ ターゲットとして Azure を使用します。
* プライベート クラウドの vSAN データストアを暗号化する Azure サブスクリプション内の KMS サーバーを展開します。
* ハイブリッド アプリケーションを使用します。アプリケーションの Web 層は、アプリケーションおよびデータベースの層がプライベート クラウドで実行する間にパブリック クラウドで実行します。

## <a name="azure-virtual-network-connection"></a>Azure 仮想ネットワーク接続

ExpressRoute を使用して、プライベート クラウドを Azure リソースに接続できます。  ExpressRoute 接続を使用すると、プライベート クラウドから Azure サブスクリプションで実行されているリソースにアクセスできます。  この接続により、プライベート クラウド ネットワークを Azure 仮想ネットワークに拡張できます。  CloudSimple ネットワークからのルートは、BGP 経由で Azure 仮想ネットワークと交換されます。  仮想ネットワーク ピアリングを構成した場合は、すべてのピアリングされた仮想ネットワークに CloudSimple ネットワークからアクセスできるようになります。

![仮想ネットワークへの Azure ExpressRoute 接続](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>ExpressRoute のオンプレミス ネットワークへの接続。

CloudSimple リージョンに、既存の Azure ExpressRoute 回線を接続できます。 ExpressRoute Global Reach 機能により、2 つの回線を相互に接続します。  オンプレミスと CloudSimple ExpressRoute 回線の間に接続が確立されます。  この接続により、オンプレミス ネットワークをプライベート クラウド ネットワークに拡張できます。 CloudSimple ネットワークからのルートは、BGP 経由でオンプレミス ネットワークと交換されます。

![オンプレミスの ExpressRoute 接続 - Global Reach](media/cloudsimple-global-reach-connection.png)

## <a name="connection-to-on-premises-network-and-azure-virtual-network"></a>オンプレミス ネットワークと Azure 仮想ネットワークへの接続

CloudSimple ネットワークからのオンプレミス ネットワークと Azure 仮想ネットワークへの接続は共存できます。  この接続では、BGP を使用して、オンプレミス ネットワーク、Azure 仮想ネットワーク、および CloudSimple ネットワーク間のルートを交換します。  Global Reach 接続が存在する場合に、CloudSimple ネットワークを Azure 仮想ネットワークに接続すると、Azure 仮想ネットワーク ルートがオンプレミス ネットワークに表示されます。  Azure ではエッジ ルーター間でルート交換が発生します。

![Azure 仮想ネットワーク接続を使用したオンプレミスの ExpressRoute 接続](media/cloudsimple-global-reach-and-vnet-connection.png)

### <a name="important-considerations"></a>重要な考慮事項

オンプレミス ネットワークから、および Azure 仮想ネットワークから CloudSimple ネットワークに接続することで、すべてのネットワーク間でのルート交換が可能になります。

* Azure 仮想ネットワークは、オンプレミス ネットワークと CloudSimple ネットワークの両方から表示できます。
* オンプレミス ネットワークから Azure 仮想ネットワークに接続している場合は、Global Reach を使用して CloudSimple ネットワークに接続することで、CloudSimple ネットワークから仮想ネットワークにアクセスできるようになります。
* サブネット アドレスは、接続されているどのネットワーク間でも重複することは **できません**。
* CloudSimple では、ExpressRoute 接続に既定のルートがアドバタイズ **されません**
* オンプレミス ルーターで既定のルートをアドバタイズする場合、CloudSimple ネットワークと Azure 仮想ネットワークからのトラフィックでは、アドバタイズされた既定のルートが使用されます。  そのため、パブリック IP アドレスを使用して、Azure 上の仮想マシンにアクセスすることはできません。

## <a name="next-steps"></a>次のステップ

* [ExpressRoute を使用して Azure 仮想ネットワークを CloudSimple に接続する](virtual-network-connection.md)
* [ExpressRoute を使用してオンプレミスから CloudSimple に接続する](on-premises-connection.md)
