---
title: 'VPN Gateway: ゲートウェイの IP アドレスの設定を変更する: Azure CLI'
description: この記事では、Azure CLI を使用してローカル ネットワーク ゲートウェイの IP アドレスのプレフィックスを変更する手順について説明します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: d5656b60b3c94720ad0a5952f8f6524f90dc6c17
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89392631"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-cli"></a>Azure CLI を使用したローカル ネットワーク ゲートウェイの設定の変更

ローカル ネットワーク ゲートウェイのアドレス プレフィックスまたはゲートウェイ IP アドレスの設定が変わることがあります。 この記事では、ローカル ネットワーク ゲートウェイの設定を変更する方法を説明します。 これらの設定は別の方法で変更することもできます。その場合は、次のボックスから別のオプションを選択してください。

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before-you-begin"></a><a name="before"></a>開始する前に

最新バージョンの CLI コマンド (2.0 以降) をインストールします。 CLI コマンドのインストール方法については、「[Azure CLI 2.0 のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)」をご覧ください。

[!INCLUDE [CLI-login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>IP アドレスのプレフィックスを変更する

[!INCLUDE [modify-prefix](../../includes/vpn-gateway-modify-ip-prefix-cli-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>ゲートウェイの IP アドレスを変更する

[!INCLUDE [modify-gateway-IP](../../includes/vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

## <a name="next-steps"></a>次のステップ

ゲートウェイの接続を確認することができます。 「 [Verify a gateway connection (ゲートウェイの接続を確認する)](vpn-gateway-verify-connection-resource-manager.md)」を参照してください。

