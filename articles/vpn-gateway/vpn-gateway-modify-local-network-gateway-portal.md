---
title: 'VPN Gateway: ゲートウェイの IP アドレスの設定を変更する: Azure portal'
description: この記事では、Azure Portal を使用してローカル ネットワーク ゲートウェイの IP アドレスのプレフィックスを変更する手順について説明します
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 57288d49fdfa193e9ebebe5f2ce4d24327997980
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89392478"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Azure Portal を使用したローカル ネットワーク ゲートウェイの設定の変更

ローカル ネットワーク ゲートウェイの AddressPrefix または GatewayIPAddress の設定が変わることがあります。 この記事では、ローカル ネットワーク ゲートウェイの設定を変更する方法を説明します。 これらの設定は別の方法で変更することもできます。その場合は、次のボックスから別のオプションを選択してください。

接続を削除する前に、定義された PSK を取得するために、接続デバイスの構成をダウンロードしてしておくことをお勧めします。 こうすると、もう一方の側で再定義する必要はありません。

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>


## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>IP アドレスのプレフィックスを変更する

IP アドレス プレフィックスを変更するときに従う手順は、ローカル ネットワーク ゲートウェイ接続が存在するかどうかによって異なります。

[!INCLUDE [modify prefix](../../includes/vpn-gateway-modify-ip-prefix-portal-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>ゲートウェイの IP アドレスを変更する

接続先の VPN デバイスのパブリック IP アドレスが変更された場合には、ローカル ネットワーク ゲートウェイを変更してアドレスの変更を反映する必要があります。 パブリック IP アドレスを変更するときに従う手順は、ローカル ネットワーク ゲートウェイ接続が存在するかどうかによって異なります。

[!INCLUDE [modify gateway IP](../../includes/vpn-gateway-modify-lng-gateway-ip-portal-include.md)]

## <a name="next-steps"></a>次のステップ

ゲートウェイの接続を確認することができます。 「 [Verify a gateway connection (ゲートウェイの接続を確認する)](vpn-gateway-verify-connection-resource-manager.md)」を参照してください。
