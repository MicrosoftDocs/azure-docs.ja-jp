---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 04/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 19ad4e39ca4e402c37b2cfa69c7c306b6e5a2766
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444223"
---
1. 仮想ネットワーク ゲートウェイのページに移動してそれを開きます。 移動方法は複数あります。 **[TestVNet1]、[概要]、[接続されているデバイス]、[VNet1GW]** の順に選択することで、ゲートウェイ 'VNet1GW' に移動することができます。
2. VNet1GW 用のページで、**[接続]** をクリックします。 [接続] ページの上部にある **[+追加]** をクリックして **[接続の追加]** ページを開きます。

   ![Create Site-to-Site connection](./media/vpn-gateway-add-site-to-site-connection-portal-include/configure-site-to-site-connection.png)
3. **[接続の追加]** ページで、接続の値を構成します。

   - **[名前]:** 接続に名前を付けます。
   - **[接続の種類]**: **[サイト対サイト (IPsec)]** を選択します。
   - **[仮想ネットワーク ゲートウェイ]**:このゲートウェイから接続するため、この値は固定されています。
   - **[ローカル ネットワーク ゲートウェイ]:****[ローカル ネットワーク ゲートウェイを選択する]** をクリックし、使用するローカル ネットワーク ゲートウェイを選択します。
   - **[共有キー]:** この値は、ローカルのオンプレミス VPN デバイスで使用している値と一致させる必要があります。 この例では "abc123" を使用していますが、より複雑な値を使用することもできます (推奨)。 重要なのは、ここで指定する値は、VPN デバイスを構成する際に指定する値と同じにする必要があることです。
   - **[サブスクリプション]**、**[リソース グループ]**、**[場所]** の残りの値は固定されています。

4. **[OK]** をクリックして、接続を作成します。 画面に *"接続を作成しています"* というメッセージが点滅表示されます。
5. 仮想ネットワーク ゲートウェイの **[接続]** ページで接続を確認できます。 状態は、*[不明]* から *[接続中]* へ、その後 *[成功]* に変わります。
