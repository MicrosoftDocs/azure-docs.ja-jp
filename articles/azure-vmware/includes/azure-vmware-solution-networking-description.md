---
title: Azure VMware Solution のネットワークと接続
description: Azure VMware Solution のネットワークと接続の説明。
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: 96dd93f1db5dc3ddcbb883313e19c6aed8a256da
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462586"
---
<!-- Used in introduction.md and concepts-networking.md -->

Azure VMware Solution は、オンプレミスおよび Azure ベースのリソースからアクセスできるプライベート クラウド環境を提供します。 Azure ExpressRoute、VPN 接続、Azure Virtual WAN などのサービスによって接続が可能になります。 これらのサービスでは、サービスを有効にするための特定のネットワーク アドレス範囲とファイアウォール ポートが必要になります。

プライベート クラウドをデプロイすると、管理、プロビジョニング、および vMotion 用のプライベート ネットワークが作成されます。 これらのプライベート ネットワークを使用して、vCenter と NSX-T Manager、および仮想マシンの vMotion またはデプロイにアクセスします。  

ExpressRoute Global Reach は、プライベート クラウドをオンプレミス環境に接続するために使用されます。 この接続には、サブスクリプション内にオンプレミスへの ExpressRoute 回線がある仮想ネットワークが必要です。

プライベート クラウドにデプロイされた仮想マシン (VM) は、Azure Virtual WAN パブリック IP 機能を使用してインターネットからアクセスできます。  新しいプライベート クラウドのインターネット アクセスは、既定で無効になっています。 詳細については、「[Azure VMware Solution でパブリック IP 機能を使用する方法](../public-ip-usage.md)」を参照してください。

