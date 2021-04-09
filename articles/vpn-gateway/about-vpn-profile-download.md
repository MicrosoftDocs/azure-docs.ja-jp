---
title: Azure VPN Gateway:P2S VPN クライアント プロファイルについて
description: この記事を利用して、VPN クライアント プロファイルに必要な情報を確認できます。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/08/2021
ms.author: cherylmc
ms.openlocfilehash: 9bb66363d525648df08f32451842402ad1d0d93b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99979074"
---
# <a name="working-with-p2s-vpn-client-profile-files"></a>P2S VPN クライアント プロファイル ファイルの操作

このプロファイル ファイルには、VPN 接続の構成に必要な情報が含まれています。 この記事は、VPN クライアント プロファイルに必要な情報を取得して理解するのに役立ちます。

## <a name="generate-and-download-profile"></a>プロファイルを生成してダウンロードする

PowerShell または Azure Portal を使用してクライアント構成ファイルを生成することができます。 どちらの方法でも、同じ zip ファイルが返されます。

### <a name="portal"></a>ポータル

1. Azure Portal で、接続する仮想ネットワークの仮想ネットワーク ゲートウェイに移動します。
1. 仮想ネットワーク ゲートウェイ ページで、 **[ポイント対サイトの構成]** を選択します。
1. [ポイント対サイトの構成] ページの上部で **[VPN クライアントのダウンロード]** を選択します。 クライアント構成パッケージが生成されるまでに数分かかります。
1. お使いのブラウザーは、クライアント構成の zip ファイルが使用可能なことを示します。 ゲートウェイと同じ名前が付いています。 そのファイルを解凍して、フォルダーを表示します。

### <a name="powershell"></a>PowerShell

PowerShell を使用して生成するには、次の例を使用できます。

1. VPN クライアント構成ファイルを生成する際、"-AuthenticationMethod" の値は "EapTls" です。 次のコマンドを使用して、VPN クライアント構成ファイルを生成します。

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```

1. ブラウザーに URL をコピーして、zip ファイルをダウンロードし、ファイルを解凍してフォルダーを表示します。

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **OpenVPN フォルダー** には、*ovpn* プロファイルが含まれています。このプロファイルは、キーと証明書を含めるために変更する必要があります。 詳細については、「[Azure VPN Gateway 用に OpenVPN クライアントを構成する](vpn-gateway-howto-openvpn-clients.md#windows)」をご覧ください。 VPN ゲートウェイで Azure AD 認証が選択されている場合、このフォルダーは ZIP ファイルに存在しません。 代わりに、AzureVPN フォルダーに移動して、azurevpnconfig.xml を検索します。

## <a name="next-steps"></a>次のステップ

ポイント対サイトの詳細については、[ポイント対サイト](point-to-site-about.md)に関する記事をご覧ください。
