---
title: Always-On VPN トンネルの構成
titleSuffix: Azure Virtual WAN
description: Virtual WAN に Always On VPN デバイス トンネルを構成する手順
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: e814487cb4dab9c8c19daab2ea3bb81391d4a98f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90983693"
---
# <a name="configure-an-always-on-vpn-device-tunnel-for-virtual-wan"></a>Virtual WAN に Always On VPN デバイス トンネルを構成する

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>前提条件

ポイント対サイト構成を作成し、仮想ハブの割り当てを編集する必要があります。 手順については、次のセクションを参照してください。

* [P2S の構成を作成する](virtual-wan-point-to-site-portal.md#p2sconfig)
* [P2S ゲートウェイを使用してハブを作成する](virtual-wan-point-to-site-portal.md#hub)

## <a name="configure-the-device-tunnel"></a>デバイス トンネルの構成

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>プロファイルを削除するには

プロファイルを削除するには、次のコマンドを実行します。

![Remove-VpnConnection -Name MachineCertTest コマンドを実行している PowerShell ウィンドウを示すスクリーンショット。](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>次のステップ

Virtual WAN の詳細については、[FAQ](virtual-wan-faq.md) を参照してください。