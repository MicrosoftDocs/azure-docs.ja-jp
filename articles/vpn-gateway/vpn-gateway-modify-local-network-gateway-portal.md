---
title: 'VPN Gateway: ゲートウェイの IP アドレスの設定を変更する: Azure portal'
description: この記事では、Azure Portal を使用してローカル ネットワーク ゲートウェイの IP アドレスのプレフィックスを変更する手順について説明します
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/16/2020
ms.author: cherylmc
ms.openlocfilehash: 4ff4f1238764d7bdab6e74d29254a6388ea76d78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92143126"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Azure Portal を使用したローカル ネットワーク ゲートウェイの設定の変更

ローカル ネットワーク ゲートウェイの AddressPrefix または GatewayIPAddress の設定が変わることがあります。 この記事では、ローカル ネットワーク ゲートウェイの設定を変更する方法を説明します。 これらの設定は別の方法で変更することもできます。その場合は、次のボックスから別のオプションを選択してください。

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>

## <a name="local-network-gateway-configuration"></a><a name="configure-lng"></a>ローカル ネットワーク ゲートウェイ構成

下のスクリーンショットは、パブリック IP アドレス エンドポイントを使用したローカル ネットワーク ゲートウェイ リソースの **[構成]** ページを示しています。

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/settings.png" alt-text="IP アドレスの設定" lightbox="./media/vpn-gateway-modify-local-network-gateway-portal/settings-expand.png":::

次に、FQDN エンドポイントの構成ページを示します。

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/name.png" alt-text="FQDN の設定":::

## <a name="to-modify-the-gateway-ip-address-or-fqdn"></a><a name="ip"></a>ゲートウェイ IP アドレスまたは FQDN を変更するには

> [!NOTE]
> FQDN エンドポイントと IP アドレス エンドポイントの間のローカル ネットワーク ゲートウェイを変更することはできません。 このローカル ネットワーク ゲートウェイに関連付けられている接続をすべて削除し、新しいエンドポイント (IP アドレスまたは FQDN) で新規作成し、接続を再作成する必要があります。
>

接続先の VPN デバイスのパブリック IP アドレスが変更された場合は、以下の手順を使用して、ローカル ネットワーク ゲートウェイを変更します。

1. ローカル ネットワーク ゲートウェイ リソースの **[設定]** セクションで、 **[構成]** を選択します。
2. **[IP アドレス]** ボックスの IP アドレスを変更します。
3. **[保存]** を選択して設定を保存します。

接続先の VPN デバイスの FQDN (完全修飾ドメイン名) が変更された場合は、以下の手順を使用して、ローカル ネットワーク ゲートウェイを変更します。

1. ローカル ネットワーク ゲートウェイ リソースの **[設定]** セクションで、 **[構成]** を選択します。
2. **[FQDN]** ボックスでドメイン名を変更します。
3. **[保存]** を選択して設定を保存します。

## <a name="to-modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>IP アドレスのプレフィックスを変更するには

アドレス プレフィックスを追加するには:

1. ローカル ネットワーク ゲートウェイ リソースの **[設定]** セクションで、 **[構成]** を選択します。
2. *[その他のアドレス範囲の追加]* ボックスに、IP アドレス空間を追加します。
3. **[Save]\(保存\)** を選択して設定を保存します。

アドレス プレフィックスを削除するには:

1. ローカル ネットワーク ゲートウェイ リソースの **[設定]** セクションで、 **[構成]** を選択します。
2. 削除するプレフィックスが含まれる行の **[...]** を選択します。
3. **[削除]** を選択します。
4. **[Save]\(保存\)** を選択して設定を保存します。

## <a name="to-modify-bgp-settings"></a><a name="bgp"></a>BGP 設定を変更するには

BGP 設定を追加または更新するには:

1. ローカル ネットワーク ゲートウェイ リソースの **[設定]** セクションで、 **[構成]** を選択します。
2. このローカル ネットワーク ゲートウェイの BGP 構成を表示したり、更新したりするには、 **[BGP 設定の構成]** を選択します。
3. 対応するフィールドで自律システム番号か BGP ピア IP アドレスを追加するか更新します。
4. **[Save]\(保存\)** を選択して設定を保存します。

BGP 設定を削除するには:

1. ローカル ネットワーク ゲートウェイ リソースの **[設定]** セクションで、 **[構成]** を選択します。
2. 既存の BGP ASN と BGP ピア IP アドレスを削除するには、 **[BGP 設定の構成]** の選択を解除します
3. **[Save]\(保存\)** を選択して設定を保存します。

## <a name="next-steps"></a>次の手順

ゲートウェイの接続を確認することができます。 「 [Verify a gateway connection (ゲートウェイの接続を確認する)](vpn-gateway-verify-connection-resource-manager.md)」を参照してください。
