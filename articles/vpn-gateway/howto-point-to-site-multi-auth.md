---
title: 'P2S VPN と複数の認証の種類を使用して VNet に接続する: ポータル'
titleSuffix: Azure VPN Gateway
description: Azure portal で複数の認証の種類を使用して、P2S 経由で VNet に接続します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/22/2021
ms.author: cherylmc
ms.openlocfilehash: d405f4b10808b7d39c0d116f2c9006c85532b4f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101743845"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-multiple-authentication-types-azure-portal"></a>複数の認証の種類を使用して VNet へのポイント対サイト VPN 接続を構成する: Azure portal

この記事では、Windows、Linux、または macOS が実行されている個々のクライアントを Azure VNet に安全に接続する方法を紹介します。 ポイント対サイト VPN 接続は、自宅や会議室でのテレワークなど、リモートの場所から VNet に接続する場合に便利です。 VNet への接続を必要とするクライアントがごく少ない場合は、サイト対サイト VPN の代わりに P2S を使用することもできます。 ポイント対サイト接続に、VPN デバイスや公開 IP アドレスは必要ありません。 P2S により、SSTP (Secure Socket トンネリング プロトコル) または IKEv2 経由の VPN 接続が作成されます。 ポイント対サイト VPN について詳しくは、「[ポイント対サイト VPN について](point-to-site-about.md)」を参照してください。

:::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-diagram.png" alt-text="コンピューターから Azure VNet への接続 - ポイント対サイト接続の図":::

ポイント対サイト VPN の詳細については、「[ポイント対サイト VPN について](point-to-site-about.md)」を参照してください。 Azure PowerShell を使用してこの構成を作成するには、[Azure PowerShell を使用したポイント対サイト VPN の構成](vpn-gateway-howto-point-to-site-rm-ps.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションを持っていることを確認します。 Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)を有効にするか、[無料アカウント](https://azure.microsoft.com/pricing/free-trial)にサインアップしてください。

同じ VPN ゲートウェイ上で複数の認証の種類を使用することは、OpenVPN トンネルの種類でのみサポートされています。

### <a name="example-values"></a><a name="example"></a>値の例

次の値を使用して、テスト環境を作成できます。また、この値を参考にしながら、この記事の例を確認していくこともできます。

* **VNet 名:** VNet1
* **[アドレス空間]:** 10.1.0.0/16<br>この例では、1 つのアドレス空間のみを使用します。 VNet には、複数のアドレス空間を使用することができます。
* **サブネット名:** FrontEnd
* **サブネットのアドレス範囲:** 10.1.0.0/24
* **サブスクリプション:** サブスクリプションが複数ある場合は、適切なサブスクリプションを使用していることを確認します。
* **[リソース グループ]:** TestRG1
* **[場所]:** 米国東部
* **GatewaySubnet:** 10.1.255.0/27<br>
* **仮想ネットワーク ゲートウェイ名:** VNet1GW
* **ゲートウェイの種類:** VPN
* **VPN の種類:** ルート ベース
* **パブリック IP アドレス名:** VNet1GWpip
* **[接続の種類]** : ポイント対サイト
* **クライアント アドレス プール:** 172.16.201.0/24<br>このポイント対サイト接続を利用して VNet に接続する VPN クライアントは、クライアント アドレス プールから IP アドレスを受け取ります。

## <a name="create-a-virtual-network"></a><a name="createvnet"></a>仮想ネットワークの作成

最初に Azure サブスクリプションを持っていることを確認します。 Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)を有効にするか、[無料アカウント](https://azure.microsoft.com/pricing/free-trial)にサインアップしてください。

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="virtual-network-gateway"></a><a name="creategw"></a>仮想ネットワーク ゲートウェイ

この手順では、VNet の仮想ネットワーク ゲートウェイを作成します。 選択したゲートウェイ SKU によっては、ゲートウェイの作成に 45 分以上かかる場合も少なくありません。

>[!NOTE]
>Basic ゲートウェイ SKU では、OpenVPN トンネルの種類はサポートされていません。
>

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="client-address-pool"></a><a name="addresspool"></a>クライアント アドレス プール

クライアント アドレス プールとは、指定するプライベート IP アドレスの範囲です。 ポイント対サイト VPN 経由で接続するクライアントは、この範囲内の IP アドレスを動的に受け取ります。 接続元であるオンプレミスの場所、または接続先とする VNet と重複しないプライベート IP アドレス範囲を使用してください。 複数のプロトコルを構成するとき、SSTP がプロトコルの 1 つの場合、構成後のアドレス プールは構成されるプロトコル間で均等に分割されます。

1. 仮想ネットワーク ゲートウェイが作成されたら、[仮想ネットワーク ゲートウェイ] ページの **[設定]** セクションに移動します。 **[設定]** で、 **[ポイント対サイトの構成]** を選択します。 **[今すぐ構成]** を選択して、構成ページを開きます。

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png" alt-text="ポイント対サイト構成ページのスクリーンショット。" lightbox="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png":::
1. **[ポイント対サイトの構成]** ページでは、さまざまな設定を構成できます。 **[アドレス プール]** ボックスに、使用するプライベート IP アドレス範囲を追加します。 VPN クライアントには、指定した範囲から動的に IP アドレスが割り当てられます。 最小のサブネット マスクは、アクティブ/パッシブ構成の場合は 29 ビット、アクティブ/アクティブ構成の場合は 28 ビットです。

   :::image type="content" source="./media/howto-point-to-site-multi-auth/address.jpg" alt-text="アドレス プールのスクリーンショット。":::

1. 次のセクションに進み、認証とトンネルの種類を構成します。

## <a name="authentication-and-tunnel-types"></a><a name="type"></a>認証とトンネルの種類

このセクションでは、認証の種類とトンネルの種類を構成します。 **[ポイント対サイトの構成]** ページで、**トンネルの種類** も **認証の種類** も表示されない場合、お使いのゲートウェイで使用されているのは Basic SKU です。 Basic SKU では、IKEv2 と RADIUS 認証はサポートされません。 これらの設定を使用する場合は、ゲートウェイを削除し、別のゲートウェイ SKU を使って再作成する必要があります。

   :::image type="content" source="./media/howto-point-to-site-multi-auth/multiauth.jpg" alt-text="認証の種類のスクリーンショット。":::

### <a name="tunnel-type"></a><a name="tunneltype"></a>トンネルの種類

**[ポイント対サイトの構成]** ページで、トンネルの種類として **[OpenVPN (SSL)]** を選択します。

### <a name="authentication-type"></a><a name="authenticationtype"></a>認証の種類

**[認証の種類]** で、目的の種類を選択します。 オプションは次のとおりです。

* Azure 証明書
* RADIUS
* Azure Active Directory

選択した認証の種類に応じて、入力する必要があるさまざまな構成設定フィールドが表示されます。 必要な情報を入力し、ページの上部にある **[保存]** を選択して、すべての構成設定を保存してください。

認証の詳細については、以下を参照してください。

* [Azure 証明書](vpn-gateway-howto-point-to-site-resource-manager-portal.md#type)
* [RADIUS](point-to-site-how-to-radius-ps.md)
* [Azure Active Directory](openvpn-azure-ad-tenant.md)

## <a name="vpn-client-configuration-package"></a><a name="clientconfig"></a>VPN クライアント構成パッケージ

VPN クライアントをクライアント構成設定で構成する必要があります。 VPN クライアント構成パッケージには、P2S 接続を使って VNet に接続するために VPN クライアントを構成する設定が記載されたファイルが含まれています。

VPN クライアント構成ファイルを生成してインストールする手順については、構成に関連する以下の記事を参照してください。

* [ネイティブ Azure 証明書認証の P2S 構成のための VPN クライアント構成ファイルを作成およびインストールする](point-to-site-vpn-client-configuration-azure-cert.md)。
* [Azure Active Directory 認証: P2S OpenVPN プロトコル接続用に VPN クライアントを構成する](openvpn-azure-ad-client.md)。

## <a name="point-to-site-faq"></a><a name="faq"></a>ポイント対サイトに関する FAQ

このセクションでは、ポイント対サイト構成に関連する FAQ 情報を掲載しています。 また、VPN Gateway に関する詳細については、「[VPN Gateway に関する FAQ](vpn-gateway-vpn-faq.md)」をご覧ください。

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>次のステップ

接続が完成したら、仮想ネットワークに仮想マシンを追加することができます。 詳細については、[Virtual Machines](../index.yml) に関するページを参照してください。 ネットワークと仮想マシンの詳細については、「[Azure と Linux の VM ネットワークの概要](../virtual-machines/network-overview.md)」を参照してください。

P2S のトラブルシューティング情報については、[Azure ポイント対サイト接続のトラブルシューティング](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)に関するページを参照してください。
