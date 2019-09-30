---
title: ExpressRoute 用の仮想ネットワーク ゲートウェイについて - Azure | Microsoft Docs
description: ExpressRoute 用の仮想ネットワーク ゲートウェイについて説明します。 この記事には、ゲートウェイの SKU と種類に関する情報が含まれます。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: 5b74e387c6bee58acbbb7bae320a9bc72a4dda1c
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376288"
---
# <a name="expressroute-virtual-network-gateway-and-fastpath"></a>ExpressRoute 仮想ネットワーク ゲートウェイと FastPath
お使いの Azure 仮想ネットワークとオンプレミス ネットワークを ExpressRoute 経由で接続するには、最初に仮想ネットワーク ゲートウェイを作成する必要があります。 仮想ネットワーク ゲートウェイには 2 つの目的があります。1 つはネットワーク間で IP ルートを交換すること、もう 1 つはネットワーク トラフィックをルーティングすることです。 この記事では、ゲートウェイの種類、ゲートウェイ SKU、および SKU の推定パフォーマンスについて説明します。 また、パフォーマンスを向上させるために、お使いのオンプレミス ネットワークからのネットワーク トラフィックが仮想ネットワーク ゲートウェイをバイパスできるようにする機能、ExpressRoute [FastPath](#fastpath) についても説明します。

## <a name="gateway-types"></a>ゲートウェイの種類

仮想ネットワーク ゲートウェイを作成する場合、設定をいくつか指定する必要があります。 必須の設定の 1 つである '-GatewayType' は、ゲートウェイを ExpressRoute と VPN トラフィックのどちらに使用するかを指定します。 ゲートウェイには、次の 2 種類があります。

* **Vpn** - パブリック インターネットでトラフィックを暗号化して送信するには、ゲートウェイの種類 "Vpn" を使用します。 これは、VPN ゲートウェイとも呼ばれます。 サイト間接続、ポイント対サイト接続、VNet 間接続のすべてで、VPN ゲートウェイが使用されます。

* **ExpressRoute** - プライベート接続でネットワーク トラフィックを送信するには、ゲートウェイの種類 'ExpressRoute' を使用します。 これは ExpressRoute ゲートウェイとも呼ばれ、ExpressRoute の構成時に使用するゲートウェイの種類です。

各仮想ネットワークに配置できる仮想ネットワーク ゲートウェイは、ゲートウェイの種類ごとに 1 つに限られています。 たとえば、-GatewayType が Vpn の仮想ネットワーク ゲートウェイと -GatewayType が ExpressRoute の仮想ネットワーク ゲートウェイをそれぞれ 1 つ配置できます。

## <a name="gwsku"></a>ゲートウェイの SKU
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

ゲートウェイをより強力なゲートウェイ SKU にアップグレードする場合、ほとんどの場合 "Resize-AzVirtualNetworkGateway" PowerShell コマンドレットを使用できます。 これは、Standard および HighPerformance SKU へのアップグレードの場合でも機能します。 ただし、UltraPerformance SKU へのアップグレードでは、ゲートウェイを再作成する必要があります。 ゲートウェイの再作成によりダウンタイムが発生します。

### <a name="aggthroughput"></a>ゲートウェイ SKU の推定パフォーマンス
次の表は、ゲートウェイの種類と、予測されるパフォーマンスを示したものです。 この表は、リソース マネージャーとクラシック デプロイ モデルの両方に適用されます。

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> アプリケーションのパフォーマンスは複数の要因によって異なります。これらの要因には、エンド ツー エンドの待機時間、アプリケーションが起動するトラフィック フローの数などがあります。 テーブルの数値は、アプリケーションが理想的な環境で理論上達成できる上限を表しています。
>
>

### <a name="zrgw"></a>ゾーン冗長ゲートウェイ SKU

Azure Availability Zones に、ExpressRoute ゲートウェイをデプロイすることもできます。 これにより、ゲートウェイは異なる Availability Zones に物理的かつ論理的に分離され、オンプレミス ネットワークの Azure への接続がゾーン レベルの障害から保護されます。

![ゾーン冗長 ExpressRoute ゲートウェイ](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

ゾーン冗長ゲートウェイでは、ExpressRoute ゲートウェイに特定の新しいゲートウェイ SKU が使用されます。

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

新しいゲートウェイ SKU では、ニーズに最も適したその他のデプロイ オプションもサポートされます。 新しいゲートウェイ SKU を使用して仮想ネットワーク ゲートウェイを作成する場合、特定のゾーン内にゲートウェイをデプロイするオプションもあります。 これは、ゾーン ゲートウェイと呼ばれます。 ゾーン ゲートウェイをデプロイすると、すべてのゲートウェイ インスタンスが同じ可用性ゾーンにデプロイされます。

## <a name="fastpath"></a>FastPath
ExpressRoute 仮想ネットワーク ゲートウェイの目的は、ネットワーク ルートを交換し、ネットワーク トラフィックをルーティングすることです。 FastPath の目的は、お使いのオンプレミス ネットワークと仮想ネットワークの間のデータ パスのパフォーマンスを向上させることです。 FastPath が有効になっていると、ゲートウェイはバイパスされ、ネットワーク トラフィックが仮想ネットワーク内の仮想マシンに直接送信されます。 

FastPath は [ExpressRoute Direct](expressroute-erdirect-about.md) でのみ使用できます。 つまり、この機能は、ExpressRoute Direct ポートで作成された ExpressRoute 回線に[お使いの仮想ネットワークを接続](expressroute-howto-linkvnet-arm.md)する場合にのみ有効にできます。 FastPath を使うときも、仮想ネットワーク ゲートウェイとオンプレミス ネットワークの間でルートを交換する場合は、仮想ネットワーク ゲートウェイを作成する必要があります。 仮想ネットワーク ゲートウェイは、Ultra Performance または ErGw3AZ のいずれかである必要があります。

FastPath では、次の機能はサポートされていません。
* ゲートウェイ サブネットの UDR: お使いの仮想ネットワークのゲートウェイ サブネットに UDR を適用した場合、ご自身のオンプレミス ネットワークからのネットワーク トラフィックは、引き続き仮想ネットワーク ゲートウェイに送信されます。
* VNet ピアリング: 他の仮想ネットワークが、ExpressRoute に接続されているものとピアリングされている場合、ご自身のオンプレミス ネットワークから他の仮想ネットワーク (いわゆる "スポーク" VNet) へのネットワーク トラフィックは、引き続き仮想ネットワーク ゲートウェイに送信されます。 回避策として、すべての仮想ネットワークを ExpressRoute 回線に直接接続します。
* Basic Load Balancer: 仮想ネットワークに Basic の内部ロード バランサーをデプロイする場合、または仮想ネットワークにデプロイする Azure PaaS サービスで Basic の内部ロード バランサーを使用する場合、オンプレミスのネットワークから Basic Load Balancer でホストされている仮想 IP へのネットワーク トラフィックが仮想ネットワーク ゲートウェイに送信されます。 この問題を解決するには、Basic Load Balancer から [Standard Load Balancer](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-overview) にアップグレードします。 
 
## <a name="resources"></a>REST API および PowerShell コマンドレット
仮想ネットワーク ゲートウェイの構成に対して REST API および PowerShell コマンドレットを使用する場合のテクニカル リソースおよび特定構文の要件については、次のページを参照してください。

| **クラシック** | **Resource Manager** |
| --- | --- |
| [PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#azure) |[PowerShell](https://docs.microsoft.com/powershell/module/az.network#networking) |
| [REST API](https://msdn.microsoft.com/library/jj154113.aspx) |[REST API](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>次の手順
使用可能な接続構成の詳細については、 [ExpressRoute の概要](expressroute-introduction.md) に関するページを参照してください。

ExpressRoute ゲートウェイの作成の詳細については、[ExpressRoute 用の仮想ネットワーク ゲートウェイの作成](expressroute-howto-add-gateway-resource-manager.md)に関するページを参照してください。

ゾーン冗長ゲートウェイの構成について詳しくは、[ゾーン冗長仮想ネットワーク ゲートウェイの作成](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md)に関する記事をご覧ください。

FastPath を有効にする方法の詳細については、[ExpressRoute への仮想ネットワークの接続](expressroute-howto-linkvnet-arm.md)に関するページをご覧ください。 
