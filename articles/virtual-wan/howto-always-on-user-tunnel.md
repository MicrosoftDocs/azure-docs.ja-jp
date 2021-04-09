---
title: Always-On VPN ユーザー トンネルの構成
titleSuffix: Azure Virtual WAN
description: この記事では、Virtual WAN に対して Always On VPN ユーザー トンネルを構成する方法について説明します
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: e83ca64d2b0e50ec02007a3cd878e6bf034d0961
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91313588"
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
