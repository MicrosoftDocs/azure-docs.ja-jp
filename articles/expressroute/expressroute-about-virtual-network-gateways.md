---
title: "ExpressRoute 用の仮想ネットワーク ゲートウェイについて | Microsoft Docs"
description: "ExpressRoute 用の仮想ネットワーク ゲートウェイについて説明します。"
services: expressroute
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager, azure-service-management
ms.assetid: 7e0d9658-bc00-45b0-848f-f7a6da648635
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: b58a8f7f87a231bd44c9224e3c889c31336ee0b1
ms.lasthandoff: 03/21/2017


---
# <a name="about-virtual-network-gateways-for-expressroute"></a>ExpressRoute 用の仮想ネットワーク ゲートウェイについて
仮想ネットワーク ゲートウェイは、Azure 仮想ネットワークとオンプレミスの場所の間でネットワーク トラフィックの送信に使用されます。 ExpressRoute 接続を構成するときに、仮想ネットワーク ゲートウェイ、および仮想ネットワーク ゲートウェイの接続を作成して構成する必要があります。

仮想ネットワーク ゲートウェイを作成する場合、設定をいくつか指定します。 必須の設定の 1 つとして、ExpressRoute またはサイト間 VPN トラフィック用ゲートウェイのどちらを使用するかを指定します。 Resource Manager デプロイメント モデルでは、設定は "-GatewayType" です。

専用プライベート接続でネットワーク トラフィックを送信する場合、ゲートウェイの種類 "ExpressRoute" を使用します。 これは、ExpressRoute ゲートウェイとも呼ばれます。 パブリック インターネットでネットワーク トラフィックを暗号化して送信する場合、ゲートウェイの種類 "Vpn" を使用します。 これは、VPN ゲートウェイと呼ばれます。 サイト間接続、ポイント対サイト接続、VNet 間接続のすべてで、VPN ゲートウェイが使用されます。 

各仮想ネットワークに配置できる仮想ネットワーク ゲートウェイは、ゲートウェイの種類ごとに 1 つに限られています。 たとえば、-GatewayType が Vpn の仮想ネットワーク ゲートウェイと -GatewayType が ExpressRoute の仮想ネットワーク ゲートウェイをそれぞれ 1 つ配置できます。 この記事では、ExpressRoute の仮想ネットワーク ゲートウェイについて説明します。

## <a name="gwsku"></a>ゲートウェイの SKU
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

ゲートウェイをより強力なゲートウェイ SKU にアップグレードする場合、ほとんどの場合 "Resize-AzureRmVirtualNetworkGateway" PowerShell コマンドレットを使用できます。 これは、Standard および HighPerformance SKU へのアップグレードの場合でも機能します。 ただし、UltraPerformance SKU へのアップグレードでは、ゲートウェイを再作成する必要があります。

### <a name="aggthroughput"></a>ゲートウェイ SKU の予測される合計スループット
次の表は、ゲートウェイの種類、および予測される合計スループットを示したものです。 この表は、リソース マネージャーとクラシック デプロイ モデルの両方に適用されます。

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> アプリケーションのスループットは複数の要因によって異なります。これらの要因には、エンド ツー エンドの待機時間、アプリケーションが起動するトラフィック フローの数などがあります。 テーブルの数値は、アプリケーションが理想的な環境で理論上達成できる上限を表しています。 
> 
>

## <a name="resources"></a>REST API および PowerShell コマンドレット
仮想ネットワーク ゲートウェイの構成に対して REST API および PowerShell コマンドレットを使用する場合のテクニカル リソースおよび特定構文の要件については、次のページを参照してください。

| **クラシック** | **Resource Manager** |
| --- | --- |
| [PowerShell](https://msdn.microsoft.com/library/mt270335.aspx) |[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx) |
| [REST API](https://msdn.microsoft.com/library/jj154113.aspx) |[REST API](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>次のステップ
使用可能な接続構成の詳細については、 [ExpressRoute の概要](expressroute-introduction.md) に関するページを参照してください。 


