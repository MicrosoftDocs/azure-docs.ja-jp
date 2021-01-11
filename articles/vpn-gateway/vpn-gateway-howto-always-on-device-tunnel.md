---
title: Always-On VPN トンネルの構成
titleSuffix: Azure VPN Gateway
description: Windows 10 Always On と共にゲートウェイを使用し、Azure への永続的デバイス トンネルを確立し、構成する方法について説明します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: e64d2223740942fe35ae8a730303bee8893c2489
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88035605"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Always On VPN デバイス トンネルの構成

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>ゲートウェイを構成する

記事「[ポイント対サイト VPN 接続の構成](vpn-gateway-howto-point-to-site-resource-manager-portal.md)」を使用して、IKEv2 と証明書ベースの認証を使用するように VPN ゲートウェイを構成します。

## <a name="configure-the-device-tunnel"></a>デバイス トンネルの構成

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>プロファイルを削除するには

プロファイルを削除するには、次のコマンドを実行します。

![クリーンアップ](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>次のステップ

トラブルシューティングについては、[Azure ポイント対サイト接続の問題](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)のページを参照してください。
