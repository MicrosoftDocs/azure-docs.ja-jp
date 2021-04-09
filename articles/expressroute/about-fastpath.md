---
title: Azure ExpressRoute FastPath について
description: ゲートウェイをバイパスしてネットワーク トラフィックを送信するための Azure ExpressRoute FastPath について説明します
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: duau
ms.openlocfilehash: eefc42fb8e66e66c6388599df65c59ff642a6b59
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124110"
---
# <a name="about-expressroute-fastpath"></a>ExpressRoute FastPath について

ExpressRoute 仮想ネットワーク ゲートウェイの目的は、ネットワーク ルートを交換し、ネットワーク トラフィックをルーティングすることです。 FastPath の目的は、お使いのオンプレミス ネットワークと仮想ネットワークの間のデータ パスのパフォーマンスを向上させることです。 FastPath が有効になっていると、ゲートウェイはバイパスされ、ネットワーク トラフィックが仮想ネットワーク内の仮想マシンに直接送信されます。

## <a name="requirements"></a>必要条件

### <a name="circuits"></a>回線

FastPath はすべての ExpressRoute 回線で使用できます。

### <a name="gateways"></a>ゲートウェイ

FastPath を使うときも、仮想ネットワーク ゲートウェイとオンプレミス ネットワークの間でルートを交換する場合は、仮想ネットワーク ゲートウェイを作成する必要があります。 パフォーマンス情報やゲートウェイ SKU など、仮想ネットワーク ゲートウェイと ExpressRoute の詳細については、[ExpressRoute の仮想ネットワーク ゲートウェイ](expressroute-about-virtual-network-gateways.md)に関するページを参照してください。

FastPath を構成するには、仮想ネットワーク ゲートウェイを次のいずれかにする必要があります。

* Ultra Performance
* ErGw3AZ

> [!IMPORTANT]
> ExpressRoute 経由で IPv6 ベースのプライベート ピアリングと共に FastPath を使用する予定の場合は、**SKU** に対して ErGw3AZ を選択してください。 これは、ExpressRoute Direct を利用する回線でのみ利用できます。
> 
>

## <a name="limitations"></a>制限事項

FastPath ではほとんどの構成がサポートされていますが、次の機能はサポートされていません。

* ゲートウェイ サブネットの UDR: お使いの仮想ネットワークのゲートウェイ サブネットに UDR を適用する場合、ご自身のオンプレミス ネットワークからのネットワーク トラフィックは、引き続き仮想ネットワーク ゲートウェイに送信されます。

* VNet ピアリング: 他の仮想ネットワークが、ExpressRoute に接続されているものとピアリングされている場合、ご自身のオンプレミス ネットワークから他の仮想ネットワーク (いわゆる "スポーク" VNet) へのネットワーク トラフィックは、引き続き仮想ネットワーク ゲートウェイに送信されます。 回避策として、すべての仮想ネットワークを ExpressRoute 回線に直接接続します。

* Basic Load Balancer: 仮想ネットワークに Basic の内部ロード バランサーをデプロイする場合、または仮想ネットワークにデプロイする Azure PaaS サービスで Basic の内部ロード バランサーを使用する場合、オンプレミスのネットワークから Basic Load Balancer でホストされている仮想 IP へのネットワーク トラフィックが仮想ネットワーク ゲートウェイに送信されます。 この問題を解決するには、Basic Load Balancer から [Standard Load Balancer](../load-balancer/load-balancer-overview.md) にアップグレードします。

* Private Link: オンプレミス ネットワークから仮想ネットワークの[プライベート エンドポイント](../private-link/private-link-overview.md)に接続する場合、接続は仮想ネットワーク ゲートウェイを通過します。
 
## <a name="next-steps"></a>次のステップ

FastPath を有効にする方法については、[ExpressRoute への仮想ネットワークのリンク](expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)に関するページを参照してください。
