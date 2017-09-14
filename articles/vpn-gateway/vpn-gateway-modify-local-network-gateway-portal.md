---
title: "ローカル ネットワーク ゲートウェイの IP アドレスのプレフィックスと VPN ゲートウェイの IP アドレスの変更 | Azure| Portal | Microsoft Docs"
description: "この記事では、Azure Portal を使用してローカル ネットワーク ゲートウェイの IP アドレスのプレフィックスを変更する手順について説明します"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: cherylmc
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: bdd6f90fe97408bd45a72adf58bfdc190207de46
ms.contentlocale: ja-jp
ms.lasthandoff: 08/16/2017

---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Azure Portal を使用したローカル ネットワーク ゲートウェイの設定の変更

ローカル ネットワーク ゲートウェイの AddressPrefix または GatewayIPAddress の設定が変わることがあります。 この記事では、ローカル ネットワーク ゲートウェイの設定を変更する方法を説明します。 これらの設定は別の方法で変更することもできます。その場合は、次のボックスから別のオプションを選択してください。

> [!div class="op_single_selector"]
> * [Azure ポータル](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>


## <a name="ipaddprefix"></a>IP アドレスのプレフィックスを変更する

IP アドレス プレフィックスを変更するときに従う手順は、ローカル ネットワーク ゲートウェイ接続が存在するかどうかによって異なります。

[!INCLUDE [modify prefix](../../includes/vpn-gateway-modify-ip-prefix-portal-include.md)]

## <a name="gwip"></a>ゲートウェイの IP アドレスを変更する

接続先の VPN デバイスのパブリック IP アドレスが変更された場合には、ローカル ネットワーク ゲートウェイを変更してアドレスの変更を反映する必要があります。 パブリック IP アドレスを変更するときに従う手順は、ローカル ネットワーク ゲートウェイ接続が存在するかどうかによって異なります。

[!INCLUDE [modify gateway IP](../../includes/vpn-gateway-modify-lng-gateway-ip-portal-include.md)]

## <a name="next-steps"></a>次のステップ

ゲートウェイの接続を確認することができます。 「 [Verify a gateway connection (ゲートウェイの接続を確認する)](vpn-gateway-verify-connection-resource-manager.md)」を参照してください。
