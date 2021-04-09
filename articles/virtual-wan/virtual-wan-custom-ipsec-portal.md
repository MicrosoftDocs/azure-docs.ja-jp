---
title: Azure Virtual WAN のカスタム IPsec ポリシーを構成する:ポータル | Microsoft Docs
description: ポータルを使用して Azure Virtual WAN のカスタム IPsec ポリシーを構成する方法について説明します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: 07eae453441ea8bff81d7cdb60f9c46c08a22829
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91851177"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>ポータルを使用して Virtual WAN のカスタム IPsec ポリシーを構成する

Azure portal で Virtual WAN VPN 接続用のカスタム IPsec ポリシーを構成できます。 カスタム ポリシーは、両側 (オンプレミスと Azure VPN ゲートウェイ) で、IKE フェーズ 1 および IKE フェーズ 2 に同じ設定を使用する場合に便利です。

## <a name="working-with-custom-policies"></a>カスタム ポリシーの使用

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>ポリシーを構成する

1. **仮想ハブを見つけます**。 ブラウザーから [Azure ポータル](https://aka.ms/azurevirtualwanpreviewfeatures) に移動し、Azure アカウントでサインインします。 Virtual WAN リソースに移動し、お使いの VPN サイトが接続されている仮想ハブを見つけます。
2. **VPN サイトを選択します**。 ハブの概要ページで、 **[VPN (サイトからサイトへ)]** をクリックし、カスタム IPsec ポリシーを設定する VPN サイトを選択します。

   ![select](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **VPN 接続を編集します**。 **コンテキスト メニュー** ( **...** ) から、 **[VPN 接続を編集する]** を選択します。

   ![編集](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **設定を構成します**。 **[VPN 接続を編集する]** ページで、IPsec の設定を既定値からカスタムに変更し、IPsec ポリシーをカスタマイズします。 **[Save]\(保存\)** を選択して設定を保存します。

   ![構成して保存する](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>次のステップ

Virtual WAN の詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください。