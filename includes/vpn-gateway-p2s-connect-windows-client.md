---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.openlocfilehash: 15c29648e42ba190991d51188489883e29bee165
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93041515"
---
>[!NOTE]
>接続元の Windows クライアント コンピューターの管理者権限が必要です。
>

1. VNet に接続するには、クライアント コンピューターで VPN 設定に移動し、作成した VPN 接続を見つけます。 仮想ネットワークと同じ名前が付いています。 **[接続]** を選択します。 証明書を使用することを示すポップアップ メッセージが表示される場合があります。 **[続行]** を選択して、昇格された特権を使用します。

1. **[接続]** 状態ページで、 **[接続]** を選択して接続を開始します。 **[証明書の選択]** 画面が表示された場合は、表示されているクライアント証明書が接続に使用する証明書であることを確認します。 そうでない場合は、ドロップダウン矢印を使用して適切な証明書を選び、 **[OK]** を選択します。

   :::image type="content" source="./media/vpn-gateway-p2s-connect-windows-client/connection-status.png" alt-text="Windows コンピューターから接続する":::

1. 接続が確立されました。

   :::image type="content" source="./media/vpn-gateway-p2s-connect-windows-client/connected.png" alt-text="コンピューターから Azure VNet への接続 - ポイント対サイト接続の図":::
