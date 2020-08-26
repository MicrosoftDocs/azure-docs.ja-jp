---
title: Azure VPN Gateway 用に OpenVPN クライアントを構成する方法 | Microsoft Docs
description: オペレーティング システム クライアントである Windows、Linux、Mac で Azure VPN Gateway 用に OpenVPN を構成する方法について説明します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 92447a541726c1c11b7b10d6d52cf91cfc07f945
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88036863"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Azure VPN Gateway 用に OpenVPN クライアントを構成する

この記事では、**OpenVPN &reg; プロトコル** クライアントの構成方法について説明します。

## <a name="before-you-begin"></a>開始する前に

VPN ゲートウェイ用に OpenVPN を構成する手順を完了していることを確認します。 詳細については、[Azure VPN Gateway 用に OpenVPN を構成する方法](vpn-gateway-howto-openvpn.md)に関するページを参照してください。

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>次のステップ

VPN クライアントが別の VNet のリソースにアクセスできるようにするには、[VNet 間](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)に関する記事に記載されている手順に従って、VNet 間接続を設定します。 ゲートウェイと接続で BGP を有効にします。そうしないと、トラフィックは流れません。

**"OpenVPN" は OpenVPN Inc. の商標です。**
