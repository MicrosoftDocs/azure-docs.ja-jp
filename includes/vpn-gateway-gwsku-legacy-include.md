---
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: cherylmc
ms.openlocfilehash: 622a2f69c2e7b82f2df10d6120ee2dd466961915
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "77211423"
---
レガシ (古い) VPN ゲートウェイ SKU には以下があります。

* 既定 (Basic)
* Standard
* HighPerformance

VPN Gateway では、UltraPerformance ゲートウェイ SKU が使用されません。 UltraPerformance SKU については、[ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md) のドキュメントを参照してください。

レガシ SKU を操作する場合は、以下を考慮してください。

* PolicyBased VPN タイプを使用する場合には、Basic SKU を使用する必要があります。 PolicyBased VPN (旧称静的ルーティング) は、その他の SKU ではサポートされていません。
* BGP は、Basic SKU ではサポートされていません。
* ExpressRoute と VPN Gateway が共存する構成は、Basic SKU ではサポートされていません。
* アクティブ/アクティブ S2S VPN ゲートウェイ接続は、HighPerformance SKU のみで構成できます。