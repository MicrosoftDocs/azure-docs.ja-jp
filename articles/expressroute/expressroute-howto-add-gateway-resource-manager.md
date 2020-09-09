---
title: 'Azure ExpressRoute: VNet へのゲートウェイの追加:PowerShell'
description: この記事では、ExpressRoute の作成済みの Resource Manager VNet に VNet ゲートウェイを追加する方法を説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/21/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: cfdab553ba7f6506f66e892da3f1e8ce01c6d8bb
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89396388"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>PowerShell を使用して ExpressRoute の仮想ネットワーク ゲートウェイを構成する
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [クラシック - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [ビデオ - Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

この記事では、既存の仮想ネットワーク (VNet) 用の VNet ゲートウェイを追加、サイズ変更、および削除する手順を説明します。 この構成の手順は、ExpressRoute 構成用に Resource Manager デプロイ モデルを使用して作成された VNet に適用されます。 詳細については、「[ExpressRoute 用の仮想ネットワーク ゲートウェイについて](expressroute-about-virtual-network-gateways.md)」をご覧ください。

## <a name="before-beginning"></a>作業を開始する前に

### <a name="working-with-powershell"></a>PowerShell での作業

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="configuration-reference-list"></a>構成参照一覧

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>次のステップ
VNet ゲートウェイを作成したので、ExpressRoute 回線に VNet をリンクできるようになりました。 「 [ExpressRoute 回線への仮想ネットワークのリンク](expressroute-howto-linkvnet-arm.md)」を参照してください。
