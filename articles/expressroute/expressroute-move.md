---
title: "クラシックから Resource Manager への ExpressRoute 回線の移行 | Microsoft Docs"
description: "このページでは、クラシック デプロイメント モデルと Resource Manager デプロイメント モデルのブリッジについて知っておく必要がある情報を概説します。"
documentationcenter: na
services: expressroute
author: ganesr
manager: carmonm
editor: 
ms.assetid: bdf01217-1a98-4ec0-a08e-d84fd37f78af
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/02/2017
ms.author: ganesr
ms.openlocfilehash: 7f8386b518ada850fc03e23c5cae3b159b3b213e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="moving-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>クラシック デプロイメント モデルから Resource Manager デプロイメント モデルへの ExpressRoute 回線の移行
この記事では、Azure ExpressRoute 回線をクラシック デプロイメント モデルから Azure Resource Manager デプロイメント モデルに移行する意味について概説します。

クラシック デプロイメント モデルでデプロイされた仮想ネットワークと Resource Manager デプロイメント モデルでデプロイされた仮想ネットワークにはどちらも、単一の ExpressRoute 回線を使用して接続できます。 この両方のデプロイメント モデルにまたがって、その作成方法を問わず、ExpressRoute 回線を仮想ネットワークにリンクできるようになりました。

![An ExpressRoute circuit that links to virtual networks across both deployment models](./media/expressroute-move/expressroute-move-1.png)

## <a name="expressroute-circuits-that-are-created-in-the-classic-deployment-model"></a>クラシック デプロイメント モデルで作成された ExpressRoute 回線
クラシック デプロイメント モデルで作成された ExpressRoute 回線で、クラシック デプロイメント モデルと Resource Manager デプロイメント モデルの両方に接続できるようにするためには、最初に Resource Manager デプロイメント モデルに移行する必要があります。 接続の移行中に接続が失われたり中断が発生したりすることはありません。 クラシック デプロイメント モデルにおける回線と仮想ネットワークとの間のリンクは、(同じサブスクリプション内であるかどうかに関係なく) すべて保持されます。

移行が正常に完了すると、見た目、パフォーマンス、操作感は、Resource Manager デプロイメント モデルで作成された ExpressRoute 回線とまったく同じになります。 これで、Resource Manager デプロイメント モデルの仮想ネットワークへの接続を作成することができます。

ExpressRoute 回線を Resource Manager デプロイメント モデルに移行すると、ExpressRoute 回線のライフ サイクルの管理は Resource Manager デプロイメント モデルを使用してのみ行えるようになります。 これは、ピアリングの追加、更新、削除、回線のプロパティ (帯域幅、SKU、課金タイプなど) の更新、回線の削除などの操作を行うことができるのが Resource Manager デプロイメント モデルに限られることを意味します。 両方のデプロイメント モデルへのアクセスを管理する方法の詳細については、Resource Manager デプロイメント モデルで作成された回線に関する以下のセクションを参照してください。

移行にあたって接続プロバイダーの関与は必要ありません。

## <a name="expressroute-circuits-that-are-created-in-the-resource-manager-deployment-model"></a>Resource Manager デプロイメント モデルで作成された ExpressRoute 回線
Resource Manager デプロイメント モデルで作成された ExpressRoute 回線を両方のデプロイメント モデルからアクセスできるように設定できます。 サブスクリプションのすべての ExpressRoute 回線は、両方のデプロイ モデルからアクセスできるように設定できます。

* Resource Manager デプロイメント モデルで作成された ExpressRoute 回線は、既定でクラシック デプロイメント モデルにアクセスできません。
* クラシック デプロイメント モデルから Resource Manager デプロイメント モデルに移行された ExpressRoute 回線は、既定で両方のデプロイメント モデルからアクセスできます。
* ExpressRoute 回線は、作成されたのが Resource Manager デプロイメント モデルであるかクラシック デプロイメント モデルであるかに関係なく、必ず Resource Manager デプロイメント モデルにアクセスできます。 これは、 [仮想ネットワークをリンクする方法](expressroute-howto-linkvnet-arm.md)に従うことで、Resource Manager デプロイメント モデルで作成された仮想ネットワークへの接続を作成できることを意味します。
* クラシック デプロイメント モデルへのアクセスは、ExpressRoute 回線の **allowClassicOperations** パラメーターを使用して制御します。

> [!IMPORTANT]
> [サービスの制限](../azure-subscription-service-limits.md) に関するページに記載されているすべてのクォータが適用されます。 たとえば、Standard の回線は、クラシック デプロイメント モデルと Resource Manager デプロイメント モデルの両方にわたって最大で 10 個の仮想ネットワーク リンク/接続を持つことができます。
> 
> 

## <a name="controlling-access-to-the-classic-deployment-model"></a>クラシック デプロイメント モデルへのアクセスの制御
ExpressRoute 回線の **allowClassicOperations** パラメーターを設定すると、単一の ExpressRoute 回線で両方のデプロイメント モデルの仮想ネットワークにリンクできるようになります。

**allowClassicOperations** を TRUE に設定すると、両方のデプロイメント モデルから ExpressRoute 回線に仮想ネットワークをリンクできます。 クラシック デプロイメント モデルの仮想ネットワークにリンクするには、 [クラシック デプロイメント モデルの仮想ネットワークをリンクする方法](expressroute-howto-linkvnet-classic.md)に関するガイダンスに従ってください。 Resource Manager デプロイメント モデルの仮想ネットワークにリンクするには、 [Resource Manager デプロイメント モデルの仮想ネットワークをリンクする方法](expressroute-howto-linkvnet-arm.md)に関するガイダンスに従ってください。

**allowClassicOperations** を FALSE に設定すると、クラシック デプロイメント モデルから回線へのアクセスがブロックされます。 ただし、クラシック デプロイメント モデルのすべての仮想ネットワーク リンクは保持されます。 この場合、ExpressRoute 回線は、クラシック デプロイメント モデルで認識されません。

## <a name="supported-operations-in-the-classic-deployment-model"></a>クラシック デプロイ モデルでサポートされる操作
**allowClassicOperations** が TRUE に設定されている場合は、ExpressRoute 回線で次の従来の操作がサポートされます。

* ExpressRoute 回線の情報の取得
* クラシック仮想ネットワークへの仮想ネットワーク リンクの作成/更新/取得/削除
* 仮想ネットワーク リンクでのサブスクリプションの境界を越えた接続に必要な承認の作成/更新/取得/削除

**allowClassicOperations** が TRUE に設定されている場合でも、次の従来の操作を実行することはできません。

* Azure プライベート ピアリング、Azure パブリック ピアリング、Microsoft ピアリングに使用するボーダー ゲートウェイ プロトコル (BGP) ピアリングの作成/更新/取得/削除
* ExpressRoute 回線の削除

## <a name="communication-between-the-classic-and-the-resource-manager-deployment-models"></a>クラシック デプロイメント モデルと Resource Manager デプロイメント モデルとの間の通信
ExpressRoute 回線は、クラシック デプロイメント モデルと Resource Manager デプロイメント モデルとの間の橋渡し的な役割を果たします。 クラシック デプロイメント モデルの仮想ネットワーク内の仮想マシンと Resource Manager デプロイメント モデルの仮想ネットワーク内の仮想マシンとの間のトラフィックは、両方の仮想ネットワークが同じ ExpressRoute 回線にリンクされている場合は ExpressRoute を通過します。

合計スループットは、仮想ネットワーク ゲートウェイのスループット容量によって制限されます。 トラフィックは、接続プロバイダーのネットワークや組織のネットワークを通過しません。 仮想ネットワーク間のトラフィック フローは、完全に Microsoft ネットワーク内で完結します。

## <a name="access-to-azure-public-and-microsoft-peering-resources"></a>Azure パブリックおよび Microsoft ピアリング リソースへのアクセス
通常、Azure パブリック ピアリングおよび Microsoft ピアリングからアクセスできるリソースに対して、中断することなくアクセスできます。  

## <a name="whats-supported"></a>サポートされる操作
ここでは、ExpressRoute 回線でサポートされる操作について説明します。

* クラシック デプロイメント モデルでデプロイされた仮想ネットワークと Resource Manager デプロイメント モデルでデプロイされた仮想ネットワークには、単一の ExpressRoute 回線を使用してアクセスできます。
* クラシック デプロイメント モデルから Resource Manager デプロイメント モデルに ExpressRoute 回線を移行することができます。 移行が完了すると、見た目、操作感、パフォーマンスは、Resource Manager デプロイメント モデルで作成された ExpressRoute 回線と同じになります。
* 移行できるのは ExpressRoute 回線のみです。 この操作で回線リンク、仮想ネットワーク、VPN ゲートウェイを移行することはできません。
* ExpressRoute 回線を Resource Manager デプロイメント モデルに移行すると、ExpressRoute 回線のライフ サイクルの管理は Resource Manager デプロイメント モデルを使用してのみ行えるようになります。 これは、ピアリングの追加、更新、削除、回線のプロパティ (帯域幅、SKU、課金タイプなど) の更新、回線の削除などの操作を行うことができるのが Resource Manager デプロイメント モデルに限られることを意味します。
* ExpressRoute 回線は、クラシック デプロイメント モデルと Resource Manager デプロイメント モデルとの間の橋渡し的な役割を果たします。 クラシック デプロイメント モデルの仮想ネットワーク内の仮想マシンと Resource Manager デプロイメント モデルの仮想ネットワーク内の仮想マシンとの間のトラフィックは、両方の仮想ネットワークが同じ ExpressRoute 回線にリンクされている場合は ExpressRoute を通過します。
* サブスクリプションの境界を越えた接続は、クラシック デプロイメント モデルと Resource Manager デプロイメント モデルの両方でサポートされます。
* ExpressRoute 回線をクラシック モデルから Azure Resource Manager モデルに移動した後、[その ExpressRoute 回線にリンクされている仮想ネットワークを移行する](expressroute-migration-classic-resource-manager.md)ことができます。

## <a name="whats-not-supported"></a>サポートされていないもの
ここでは、ExpressRoute 回線でサポートされない操作について説明します。

* クラシック デプロイメント モデルからの ExpressRoute 回線のライフ サイクルの管理。
* クラシック デプロイメント モデルでのロールベースのアクセス制御 (RBAC)。 クラシック デプロイメント モデルの回線に対して RBAC 制御を行うことはできません。 回線に対する仮想ネットワークのリンクとリンク解除は、サブスクリプションの管理者/共同管理者が実施できます。

## <a name="configuration"></a>構成
「 [クラシック デプロイメント モデルから Resource Manager デプロイメント モデルへの ExpressRoute 回線の移行](expressroute-howto-move-arm.md)」に説明されている手順を参照してください。

## <a name="next-steps"></a>次のステップ
* [ExpressRoute 回線にリンクされている仮想ネットワークをクラシック モデルから Azure Resource Manager モデルに移行します](expressroute-migration-classic-resource-manager.md)
* ワークフロー情報については、「 [ExpressRoute 回線のプロビジョニング ワークフローと回線の状態](expressroute-workflows.md)」を参照してください。
* ExpressRoute 接続を構成します。
  
  * [ExpressRoute 回線の作成](expressroute-howto-circuit-arm.md)
  * [ルーティングの構成](expressroute-howto-routing-arm.md)
  * [ExpressRoute 回線への仮想ネットワークのリンク](expressroute-howto-linkvnet-arm.md)

