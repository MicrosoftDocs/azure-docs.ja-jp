---
title: "ローカル ネットワーク ゲートウェイの IP アドレスのプレフィックスと VPN ゲートウェイの IP アドレスの変更| Azure| PowerShell| Microsoft Docs"
description: "この記事では、PowerShell を使用してローカル ネットワーク ゲートウェイの IP アドレスのプレフィックスを変更する手順について説明します"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 8c7db48f-d09a-44e7-836f-1fb6930389df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: cherylmc
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 2a095b96a8c352abeca72640d37c0d629b447763
ms.contentlocale: ja-jp
ms.lasthandoff: 08/16/2017

---
# <a name="modify-local-network-gateway-settings-using-powershell"></a>PowerShell を使用したローカル ネットワーク ゲートウェイの設定の変更

ローカル ネットワーク ゲートウェイの AddressPrefix または GatewayIPAddress の設定が変わることがあります。 この記事では、ローカル ネットワーク ゲートウェイの設定を変更する方法を説明します。 これらの設定は別の方法で変更することもできます。その場合は、次のボックスから別のオプションを選択してください。

> [!div class="op_single_selector"]
> * [Azure ポータル](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before"></a>開始する前に

Azure Resource Manager PowerShell コマンドレットの最新版をインストールしてください。 PowerShell コマンドレットのインストールの詳細については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs) 」を参照してください。

## <a name="ipaddprefix"></a>IP アドレスのプレフィックスを変更する

[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="gwip"></a>ゲートウェイの IP アドレスを変更する

[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>次のステップ

ゲートウェイの接続を確認することができます。 「 [Verify a gateway connection (ゲートウェイの接続を確認する)](vpn-gateway-verify-connection-resource-manager.md)」を参照してください。
