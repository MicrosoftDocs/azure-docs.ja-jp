---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3c825f042bb3e7fee5c00a8b34c12ca2d05f8d2e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92217957"
---
Azure Portal で目的の接続に移動することで、Resource Manager VPN ゲートウェイの接続の状態を確認できます。 以下に示した手順は、目的の接続に移動して接続を確認する方法の一例です。

1. [Azure Portal](https://portal.azure.com) で **[すべてのリソース]** をクリックし、仮想ネットワーク ゲートウェイに移動します。
1. 仮想ネットワーク ゲートウェイのブレードで、 **[接続]** をクリックします。 各接続の状態が確認できます。

   :::image type="content" source="./media/vpn-gateway-verify-connection-portal-rm-include/connections.png" alt-text="接続の表示" lightbox="./media/vpn-gateway-verify-connection-portal-rm-include/connections-expand.png":::

1. 確認する接続の名前をクリックします。 **[基本]** で、接続の詳しい情報を確認できます。 接続に成功していれば、 **[状態]** が "成功" と "接続済み" になります。

   :::image type="content" source="./media/vpn-gateway-verify-connection-portal-rm-include/essentials.png" alt-text="接続を確認する" lightbox="./media/vpn-gateway-verify-connection-portal-rm-include/essentials-expand.png":::
