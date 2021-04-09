---
author: cherylmc
ms.author: cherylmc
ms.date: 02/23/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 567c0bb75c30a1f0ccdcde7ec1b0f04f5d6e54c5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048261"
---
[!INCLUDE [Portal feature rollout](virtual-wan-portal-feature-rollout.md)]

1. **[すべてのリソース]** に移動し、作成した仮想 WAN を選択して、左側のメニューから **[ユーザー VPN 構成]** を選択します。
1. **[ユーザー VPN 構成]** ページで、ページの上部にある **[+ ユーザー VPN 構成の作成]** を選択し、 **[新しいユーザー VPN 構成の作成]** ページを開きます。

   :::image type="content" source="media/virtual-wan-p2s-configuration/user-vpn.png" alt-text="[ユーザー VPN 構成] ページのスクリーンショット":::

1. **[基本]** タブの **[インスタンスの詳細]** で、VPN の構成に割り当てる **[名前]** を入力します。
1. **[トンネルの種類]** で、ドロップダウンから目的のトンネルの種類を選択します。 トンネルの種類のオプションは、 **[IKEv2 VPN]** 、 **[OpenVPN]** 、 **[OpenVpn and IkeV2]** です。
1. 選択したトンネルの種類に応じて次の手順を使用します。 すべての値を指定したら、 **[確認と作成]** 、 **[作成]** の順にクリックして構成を作成します。

   **IKEv2 VPN**

   * **要件:** トンネルの種類に **[IKEv2]** を選択すると、認証方法を選択するように指示するメッセージが表示されます。 IKEv2 の場合、指定できる認証方法は 1 つのみです。 Azure 証明書、Azure Active Directory、または RADIUS ベースの認証を選択できます。

   * **IPsec カスタム パラメーター:** IKE フェーズ 1 および IKE フェーズ 2 のパラメーターをカスタマイズするには、IPsec スイッチを **[カスタム]** に切り替えてパラメーター値を選択します。 カスタマイズできるパラメーターの詳細については、[カスタムの IPsec](../articles/virtual-wan/point-to-site-ipsec.md) に関する記事を参照してください。

     :::image type="content" source="media/virtual-wan-p2s-configuration/custom.png" alt-text="カスタムへの IPsec スイッチのスクリーンショット":::

   * **認証:** ページの下部にある **[次へ]** をクリックして認証方法に進むか、ページの上部にある適切なタブをクリックして、使用する認証メカニズムに移動します。 スイッチを **[はい]** に切り替えて方法を選択します。

     この例では、RADIUS 認証が選択されています。 RADIUS ベースの認証の場合、セカンダリ RADIUS サーバーの IP アドレスとサーバー シークレットを指定できます。

     :::image type="content" source="media/virtual-wan-p2s-configuration/ike-radius.png" alt-text="IKE のスクリーンショット。":::

   **OpenVPN**

   * **要件:** トンネルの種類に **[OpenVPN]** を選択すると、認証メカニズムを選択するように指示するメッセージが表示されます。 トンネルの種類として [OpenVPN] が選択されている場合は、複数の認証方法を指定できます。 Azure 証明書、Azure Active Directory、または RADIUS ベースの認証の任意のサブセットを選択できます。 RADIUS ベースの認証の場合、セカンダリ RADIUS サーバーの IP アドレスとサーバー シークレットを指定できます。

   * **認証:** ページの下部にある **[次へ]** をクリックして認証方法に進むか、ページの上部にある適切なタブをクリックして、使用する認証方法に移動します。
   選択する方法ごとに、スイッチを **[はい]** に切り替えて、適切な値を入力します。

     この例では、Azure Active Directory が選択されています。

     :::image type="content" source="media/virtual-wan-p2s-configuration/openvpn.png" alt-text="OpenVPN ページのスクリーンショット。":::
