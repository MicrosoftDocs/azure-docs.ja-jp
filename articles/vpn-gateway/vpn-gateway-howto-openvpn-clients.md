---
title: Azure VPN Gateway 用に OpenVPN クライアントを構成する方法 | Microsoft Docs
description: オペレーティング システム クライアントである Windows、Linux、Mac で Azure VPN Gateway 用に OpenVPN を構成する方法について説明します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 4be903fa17ce95e96c82241249b421e1d794c80f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89435781"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Azure VPN Gateway 用に OpenVPN クライアントを構成する

この記事では、**OpenVPN &reg; プロトコル** クライアントの構成方法について説明します。

## <a name="before-you-begin"></a>開始する前に

VPN ゲートウェイ用に OpenVPN を構成する手順を完了していることを確認します。 詳細については、[Azure VPN Gateway 用に OpenVPN を構成する方法](vpn-gateway-howto-openvpn.md)に関するページを参照してください。

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>次のステップ

VPN クライアントが別の VNet のリソースにアクセスできるようにするには、[VNet 間](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)に関する記事に記載されている手順に従って、VNet 間接続を設定します。 ゲートウェイと接続で BGP を有効にします。そうしないと、トラフィックは流れません。

**"OpenVPN" は OpenVPN Inc. の商標です。**
