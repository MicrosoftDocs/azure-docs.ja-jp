---
title: "PowerShell を使用して ExpressRoute 用の VNet ゲートウェイを構成する: クラシック: Azure | Microsoft Docs"
description: "ExpressRoute の構成に対して PowerShell を使用してクラシック デプロイ モデルの VNet 用の VNet ゲートウェイを構成します。"
documentationcenter: na
services: expressroute
author: charwen
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 85ee0bc1-55be-4760-bfb4-34d9f2c96f30
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: charwen
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: badec4e0b542db52f34db3c50f7d906ed6a5a377
ms.lasthandoff: 03/24/2017


---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell-classic"></a>PowerShell を使用して ExpressRoute の仮想ネットワーク ゲートウェイを構成する (クラシック)
> [!div class="op_single_selector"]
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [クラシック - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [ビデオ - Azure Portal](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

この記事では、既存の仮想ネットワーク (VNet) 用の VNet ゲートウェイを追加、サイズ変更、および削除する手順を説明します。 この構成の手順は、 **クラシック デプロイ モデル** を使用して作成され、ExpressRoute 構成で使用される VNet 専用です。 

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Azure のデプロイ モデルについて**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>作業を開始する前に
この構成に必要な Azure PowerShell コマンドレットがインストールされていることを確認します (1.0.2 以降)。 コマンドレットをインストールしていない場合は、構成手順を開始する前にインストールする必要があります。 Azure PowerShell のインストールの詳細については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)」を参照してください

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>次のステップ
VNet ゲートウェイを作成したので、ExpressRoute 回線に VNet をリンクできるようになりました。 「 [ExpressRoute 回線への仮想ネットワークのリンク](expressroute-howto-linkvnet-classic.md)」を参照してください。


