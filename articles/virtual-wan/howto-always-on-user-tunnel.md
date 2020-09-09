---
title: Always-On VPN ユーザー トンネルの構成
titleSuffix: Azure Virtual WAN
description: この記事では、Virtual WAN に対して Always On VPN ユーザー トンネルを構成する方法について説明します
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 06/22/2020
ms.author: cherylmc
ms.openlocfilehash: 03f67053a5a199c8c64efb05d2b6a65ad6707650
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564048"
---
# <a name="configure-an-always-on-vpn-user-tunnel-for-virtual-wan"></a>Virtual WAN に Always On VPN ユーザー トンネルを構成する

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>前提条件

ポイント対サイト構成を作成し、仮想ハブの割り当てを編集する必要があります。 手順については、次のセクションを参照してください。

* [P2S の構成を作成する](virtual-wan-point-to-site-portal.md#p2sconfig)
* [P2S ゲートウェイを使用してハブを作成する](virtual-wan-point-to-site-portal.md#hub)

## <a name="configure-a-user-tunnel"></a>ユーザー トンネルを構成する

[!INCLUDE [user tunnel](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>プロファイルを削除するには

プロファイルを削除するには、次の手順に従います。

1. 次のコマンドを実行します。

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. 接続を切断し、 **[Connect automatically]\(自動的に接続する\)** チェック ボックスをオフにします。

   ![クリーンアップ](./media/howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>次のステップ

Virtual WAN の詳細については、[FAQ](virtual-wan-faq.md) を参照してください。
