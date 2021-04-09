---
title: ExpressRoute:クラシックから Azure Resource Manager への回線の移行
description: Azure ExpressRoute 回線をクラシック デプロイ モデルから Azure Resource Manager デプロイ モデルに移行するときに起きることについて説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/15/2020
ms.author: duau
ms.openlocfilehash: dcba2e9de2b37e8c432f94781b3c4c369ad52395
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97807943"
---
# <a name="moving-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>クラシック デプロイ モデルから Resource Manager デプロイ モデルへの ExpressRoute 回線の移行
この記事では、Azure ExpressRoute 回線をクラシック デプロイ モデルから Azure Resource Manager デプロイ モデルに移行するときに起きることについて概説します。

クラシック デプロイ モデルでデプロイされた仮想ネットワークと Resource Manager デプロイ モデルでデプロイされた仮想ネットワークはどちらも、単一の ExpressRoute 回線を使用して接続できます。

![An ExpressRoute circuit that links to virtual networks across both deployment models](./media/expressroute-move/expressroute-move-1.png)

## <a name="expressroute-circuits-that-are-created-in-the-classic-deployment-model"></a>クラシック デプロイ モデルで作成された ExpressRoute 回線
クラシック デプロイ モデルで作成された ExpressRoute 回線は、最初に Resource Manager デプロイ モデルに移行する必要があります。 このようにした場合にのみ、クラシック デプロイ モデルと Resource Manager デプロイ モデルの両方への接続を有効にすることができます。 接続の移行中に接続が失われたり中断されたりすることはありません。 クラシック デプロイ モデルにおける回線と仮想ネットワークとの間のリンクは、同じサブスクリプション内であるかどうかに関係なくすべて保持されます。

移行が正常に完了すると、Resource Manager デプロイ モデルで作成された ExpressRoute 回線とまったく同じように動作します。 これで、Resource Manager デプロイ モデルの仮想ネットワークへの接続を作成することができます。

ExpressRoute 回線を Resource Manager デプロイ モデルに移行すると、Resource Manager デプロイ モデル以外では管理できなくなります。 ピアリングの管理、回線のプロパティの更新、回線の削除の操作は、Resource Manager デプロイ モデルからしか利用できません。 両方のデプロイ モデルへのアクセスを管理する方法の詳細については、次のセクションを参照してください。

回線を Resource Manager デプロイ モデルに移行するときに、接続プロバイダーが関わる必要はありません。

## <a name="expressroute-circuits-that-are-created-in-the-resource-manager-deployment-model"></a>Resource Manager デプロイ モデルで作成された ExpressRoute 回線
Resource Manager デプロイ モデルで作成された ExpressRoute 回線を両方のデプロイ モデルからアクセスできるように設定できます。 サブスクリプションのすべての ExpressRoute 回線は、両方のデプロイ モデルからアクセスできるように構成できます。

* Resource Manager デプロイ モデルで作成された ExpressRoute 回線は、既定でクラシック デプロイ モデルにアクセスできません。
* クラシック デプロイ モデルから Resource Manager デプロイ モデルに移行された ExpressRoute 回線は、既定で両方のデプロイ モデルからアクセスできます。
* ExpressRoute 回線は、Resource Manager デプロイ モデルで作成されたか、クラシック デプロイ モデルで作成されたかに関係なく、必ず Resource Manager デプロイ モデルにアクセスできます。 仮想ネットワークへの接続を作成するには、[仮想ネットワークをリンクする方法](expressroute-howto-linkvnet-arm.md)に関する手順に従います。
* クラシック デプロイ モデルへのアクセスは、ExpressRoute 回線の **allowClassicOperations** パラメーターを使用して制御します。

> [!IMPORTANT]
> [サービスの制限](../azure-resource-manager/management/azure-subscription-service-limits.md) に関するページに記載されているすべてのクォータが適用されます。 たとえば、Standard の回線は、クラシック デプロイ モデルと Resource Manager デプロイ モデルの両方にわたって最大で 10 個の仮想ネットワーク リンク/接続を持つことができます。
> 
> 

## <a name="controlling-access-to-the-classic-deployment-model"></a>クラシック デプロイ モデルへのアクセスの制御
ExpressRoute 回路が両方のデプロイ モデルの仮想ネットワークにリンクできるようにすることができます。 これを行うには、ExpressRoute 回線で **allowClassicOperations** パラメーターを設定します。

**allowClassicOperations** を TRUE に設定すると、両方のデプロイメント モデルから ExpressRoute 回線に仮想ネットワークをリンクできます。 
* クラシック デプロイ モデルの仮想ネットワークをリンクさせるには、[クラシック デプロイ モデルの仮想ネットワークをリンクする方法](expressroute-howto-linkvnet-classic.md)に関するページを参照してください。
* Resource Manager デプロイ モデルの仮想ネットワークをリンクさせるには、[Resource Manager デプロイ モデルの仮想ネットワークをリンクする方法](expressroute-howto-linkvnet-arm.md)に関するページを参照してください。

**allowClassicOperations** を FALSE に設定すると、クラシック デプロイ モデルから回線へのアクセスがブロックされます。 ただし、クラシック デプロイ モデル内でリンクされたすべての仮想ネットワークは保持されます。 ExpressRoute 回線は、クラシック デプロイ モデルで認識されません。

## <a name="supported-operations-in-the-classic-deployment-model"></a>クラシック デプロイ モデルでサポートされる操作
**allowClassicOperations** が TRUE に設定されている場合は、ExpressRoute 回線で次の従来の操作がサポートされます。

* ExpressRoute 回線の情報の取得
* クラシック仮想ネットワークへの仮想ネットワーク リンクの作成/更新/取得/削除
* 仮想ネットワーク リンクでのサブスクリプションの境界を越えた接続に必要な承認の作成/更新/取得/削除

ただし、**allowClassicOperations** が TRUE に設定されていても、次に示す従来の操作は実行できません。

* Azure プライベート ピアリング、Azure パブリック ピアリング、Microsoft ピアリングに使用するボーダー ゲートウェイ プロトコル (BGP) ピアリングの作成/更新/取得/削除
* ExpressRoute 回線の削除

## <a name="communication-between-the-classic-and-the-resource-manager-deployment-models"></a>クラシック デプロイ モデルと Resource Manager デプロイ モデルとの間の通信
ExpressRoute 回線は、クラシック デプロイ モデルと Resource Manager デプロイ モデルとの間の橋渡し的な役割を果たします。 両方のデプロイ モデルの仮想ネットワーク間のトラフィックは、両方の仮想ネットワークが同じ回線にリンクされている場合、ExpressRoute 回線を通過できます。

合計スループットは、仮想ネットワーク ゲートウェイのスループット容量によって制限されます。 トラフィックは、接続プロバイダーのネットワークや組織のネットワークを通過しません。 仮想ネットワーク間のトラフィック フローは、完全に Microsoft ネットワーク内で完結します。

## <a name="access-to-azure-public-and-microsoft-peering-resources"></a>Azure パブリックおよび Microsoft ピアリング リソースへのアクセス
通常、Azure パブリック ピアリングおよび Microsoft ピアリングからアクセスできるリソースに対して、中断することなくアクセスできます。  

## <a name="whats-supported"></a>サポートされる操作
ここでは、ExpressRoute 回線でサポートされる操作について説明します。

* クラシック デプロイ モデルでデプロイされた仮想ネットワークと Resource Manager デプロイ モデルでデプロイされた仮想ネットワークには、単一の ExpressRoute 回線を使用してアクセスできます。
* クラシック デプロイ モデルから Resource Manager デプロイ モデルに ExpressRoute 回線を移行することができます。 移行が完了すると、Resource Manager デプロイ モデルで作成された ExpressRoute 回線と同じように動作し続けます。
* 移行できるのは ExpressRoute 回線のみです。 この操作で回線リンク、仮想ネットワーク、VPN ゲートウェイを移行することはできません。
* ExpressRoute 回線を Resource Manager デプロイ モデルに移行すると、ExpressRoute 回線のライフ サイクルの管理は Resource Manager デプロイ モデルを使用してのみ行えるようになります。 これは、ピアリングの追加、更新、削除、回線のプロパティ (帯域幅、SKU、課金タイプなど) の更新、回線の削除などの操作を行うことができるのが Resource Manager デプロイ モデルに限られることを意味します。
* ExpressRoute 回線は、クラシック デプロイ モデルと Resource Manager デプロイ モデルとの間の橋渡し的な役割を果たします。 クラシック仮想ネットワーク内の仮想マシンと Resource Manager 仮想ネットワーク内の仮想マシンとの間のトラフィックは、両方の仮想ネットワークが同じ ExpressRoute 回線にリンクされている場合、ExpressRoute を介して通信できます。
* サブスクリプションの境界を越えた接続は、クラシック デプロイ モデルと Resource Manager デプロイ モデルの両方でサポートされます。
* ExpressRoute 回線をクラシック モデルから Azure Resource Manager モデルに移動した後、[その ExpressRoute 回線にリンクされている仮想ネットワークを移行する](expressroute-migration-classic-resource-manager.md)ことができます。

## <a name="whats-not-supported"></a>サポートされていないもの
ここでは、ExpressRoute 回線でサポートされない操作について説明します。

* クラシック デプロイ モデルからの ExpressRoute 回線のライフ サイクルの管理。
* クラシック デプロイ モデルでの Azure ロールベースのアクセス制御 (Azure RBAC)。 クラシック デプロイ モデルの回線に対して Azure RBAC 制御を行うことはできません。 回線に対する仮想ネットワークのリンクとリンク解除は、サブスクリプションの管理者/共同管理者が実施できます。

## <a name="configuration"></a>構成
「[クラシック デプロイ モデルから Resource Manager デプロイ モデルへの ExpressRoute 回線の移行](expressroute-howto-move-arm.md)」に説明されている手順を参照してください。

## <a name="next-steps"></a>次のステップ
* [ExpressRoute 回線にリンクされている仮想ネットワークをクラシック モデルから Azure Resource Manager モデルに移行します](expressroute-migration-classic-resource-manager.md)
* ワークフロー情報については、「 [ExpressRoute 回線のプロビジョニング ワークフローと回線の状態](expressroute-workflows.md)」を参照してください。
* ExpressRoute 接続を構成します。
  
  * [ExpressRoute 回線の作成](expressroute-howto-circuit-arm.md)
  * [ルーティングの構成](expressroute-howto-routing-arm.md)
  * [ExpressRoute 回線への仮想ネットワークのリンク](expressroute-howto-linkvnet-arm.md)

