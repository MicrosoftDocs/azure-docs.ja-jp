---
title: SSTP から OpenVPN または IKEv2 に移行する | Azure VPN Gateway
description: この記事は、SSTP の 128 というコンカレント接続制限を克服する方法を理解するのに役立ちます。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: e2fa265e580bc0e752498284ed50e398b59423fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97657143"
---
# <a name="transition-to-openvpn-protocol-or-ikev2-from-sstp"></a>SSTP から OpenVPN プロトコルまたは IKEv2 に移行する

ポイント対サイト (P2S) VPN ゲートウェイ接続では、個々のクライアント コンピューターから仮想ネットワークへの、セキュリティで保護された接続を作成することができます。 P2S 接続は、クライアント コンピューターから接続を開始することによって確立されます。 この記事は、Resource Manager デプロイ モデルに適用され、OpenVPN プロトコルまたは IKEv2 に移行することによって、SSTP の 128 というコンカレント接続制限を克服する方法について説明します。

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>P2S で使用されるプロトコル

ポイント対サイト VPN では、次のいずれかのプロトコルを使用できます。

* **OpenVPN&reg; プロトコル**。これは、SSL/TLS ベースの VPN プロトコルです。 SSL VPN ソリューションはファイアウォールを通過できます。これは、ほとんどのファイアウォールで開かれている TCP ポート 443 アウトバウンドが SSL で使用されるためです。 OpenVPN は、Android、iOS (バージョン 11.0 以上)、Windows、Linux、および Mac デバイス (OSX バージョン 10.13 以上) から接続する際に使用できます。

* **Secure Socket トンネリング プロトコル (SSTP)** 。これは、SSL ベースの独自の VPN プロトコルです。 SSL VPN ソリューションはファイアウォールを通過できます。これは、ほとんどのファイアウォールで開かれている TCP ポート 443 アウトバウンドが SSL で使用されるためです。 SSTP は、Windows デバイスでのみサポートされます。 Azure では、SSTP を備えたすべてのバージョンの Windows (Windows 7 以降) がサポートされています。 **SSTP は、ゲートウェイ SKU に関係なく、最大 128 のコンカレント接続をサポートします**。

* IKEv2 VPN。これは、標準ベースの IPsec VPN ソリューションです。 IKEv2 VPN は、Mac デバイス (OSX バージョン 10.11 以上) から接続する際に使用できます。


>[!NOTE]
>P2S 用 IKEv2 および OpenVPN は、Resource Manager デプロイ モデルでのみ使用できます。 これらは、クラシック デプロイ モデルでは使用できません。 Basic ゲートウェイ SKU では、IKEv2 と OpenVPN プロトコルはサポートされません。 Basic SKU を使用している場合は、運用 SKU 仮想ネットワーク ゲートウェイを削除して再作成する必要があります。
>

## <a name="migrating-from-sstp-to-ikev2-or-openvpn"></a>SSTP から IKEv2 または OpenVPN に移行する

VPN ゲートウェイへの 128 を超える同時 P2S 接続をサポートが必要なのに、SSTP を使用している場合があります。 このような場合は、IKEv2 または OpenVPN プロトコルに移行する必要があります。

### <a name="option-1---add-ikev2-in-addition-to-sstp-on-the-gateway"></a>オプション 1 - ゲートウェイで SSTP だけでなく IKEv2 を追加する

これが最も簡単なオプションです。 SSTP と IKEv2 は同じゲートウェイ上に共存できるため、コンカレント接続の数を増やすことができます。 既存のゲートウェイで IKEv2 を有効にし、クライアントを再ダウンロードするだけです。

既存の SSTP VPN ゲートウェイに IKEv2 を追加しても、既存のクライアントには影響しません。また、IKEv2 を小さなバッチで使用するように構成したり、新しいクライアントで IKEv2 を使用するように構成したりすることができます。 Windows クライアントが SSTP と IKEv2 の両方用に構成されている場合は、まず IKEV2 を使用して接続が試行され、それが失敗した場合は SSTP にフォールバックされます。

**IKEv2 は標準以外の UDP ポートを使用するので、ユーザーのファイアウォールでこれらのポートがブロックされないようにする必要があります。使用されるポートは、UDP 500 および 4500 です。**

既存のゲートウェイに IKEv2 を追加するには、portal の [Virtual Network ゲートウェイ] の下にある [ポイント対サイトの構成] タブに移動し、ドロップダウン ボックスから **[IKEv2 と SSTP (SSL)]** を選択します。

![[トンネルの種類] ドロップダウンが開かれ、[IKEv2 と SSTP (SSL)] が選択されている、[ポイント対サイトの構成] ページのスクリーンショット。](./media/ikev2-openvpn-from-sstp/sstptoikev2.png "IKEv2")


### <a name="option-2---remove-sstp-and-enable-openvpn-on-the-gateway"></a>オプション 2 - SSTP を削除し、ゲートウェイで OpenVPN を有効にする

SSTP と OpenVPN は両方とも TLS ベースのプロトコルであるため、同じゲートウェイ上に共存することはできません。 SSTP から OpenVPN に移行する場合は、ゲートウェイで SSTP を無効にし、OpenVPN を有効にする必要があります。 この操作を行うと、クライアントで新しいプロファイルが構成されるまで、既存のクライアントが VPN ゲートウェイへの接続を失います。

必要に応じて、IKEv2 と共に OpenVPN を有効にすることができます。 OpenVPN は TLS ベースで、標準の TCP 443 ポートを使用します。 OpenVPN に切り替えるには、portal の [Virtual Network ゲートウェイ] の下にある [ポイント対サイトの構成] タブに移動し、ドロップダウン ボックスから **[OpenVPN (SSL)]** または **[IKEv2 と SSTP (SSL)]** を選択します。

![ポイント対サイト](./media/ikev2-openvpn-from-sstp/sstptoopenvpn.png "OpenVPN")

ゲートウェイが構成されると、既存のクライアントは、[OpenVPN クライアントを展開および構成する](./vpn-gateway-howto-openvpn-clients.md)まで接続できなくなります。

Windows 10 を使用している場合は、[Windows 用の Azure VPN クライアント](./openvpn-azure-ad-client.md#to-download-the-azure-vpn-client)を使用することもできます。


## <a name="frequently-asked-questions"></a>よく寄せられる質問
### <a name="what-are-the-client-configuration-requirements"></a>クライアントの構成要件について

>[!NOTE]
>Windows クライアントの場合、クライアント デバイスから Azure に VPN 接続を開始するには、クライアント デバイスで管理権限が必要です。
>

ユーザーは、P2S 用の Windows デバイスと Mac デバイスでネイティブ VPN クライアントを使用します。 これらのネイティブ クライアントが Azure に接続するために必要な設定が含まれた VPN クライアント構成 zip ファイルは、Azure に用意されています。

* Windows デバイスの場合、VPN クライアント構成は、ユーザーがデバイスにインストールするインストーラー パッケージで構成されています。
* Mac デバイスの場合、これは、ユーザーがデバイスにインストールする mobileconfig ファイルで構成されています。

zip ファイルでは、Azure 側のいくつかの重要な設定の値も指定されており、この値を使用することで、これらのデバイス用に独自のプロファイルを作成できます。 このような値には、VPN ゲートウェイ アドレス、構成されたトンネルの種類、ルート、ゲートウェイ検証用のルート証明書などがあります。

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

### <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>P2S VPN がサポートされるゲートウェイ SKU の種類

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* ゲートウェイ SKU の推奨事項については、[VPN Gateway の設定](vpn-gateway-about-vpn-gateway-settings.md#gwsku)に関するページを参照してください。

>[!NOTE]
>Basic SKU では、IKEv2 と RADIUS 認証はサポートされません。
>

### <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>P2S 用に VPN ゲートウェイではどの IKE/IPsec ポリシーが構成されていますか。


**IKEv2**

| **暗号化** | **整合性** | **PRF** | **DH グループ** |
|--|--|--|--|
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_ECP256 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA384 | SHA384 | GROUP_24 |
| AES256 | SHA384 | SHA384 | GROUP_14 |
| AES256 | SHA384 | SHA384 | GROUP_ECP384 |
| AES256 | SHA384 | SHA384 | GROUP_ECP256 |
| AES256 | SHA256 | SHA256 | GROUP_24 |
| AES256 | SHA256 | SHA256 | GROUP_14 |
| AES256 | SHA256 | SHA256 | GROUP_ECP384 |
| AES256 | SHA256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA256 | SHA256 | GROUP_2 |

**IPsec**

| **暗号化** | **整合性** | **PFS グループ** |
|--|--|--|
| GCM_AES256 | GCM_AES256 | GROUP_NONE |
| GCM_AES256 | GCM_AES256 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | GROUP_ECP256 |
| AES256 | SHA256 | GROUP_NONE |
| AES256 | SHA256 | GROUP_24 |
| AES256 | SHA256 | GROUP_14 |
| AES256 | SHA256 | GROUP_ECP384 |
| AES256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA1 | GROUP_NONE |

### <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>P2S 用に VPN ゲートウェイではどの TLS ポリシーが構成されていますか。
**TLS**

|**ポリシー** |
|---| 
|TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 |
|TLS_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_RSA_WITH_AES_256_CBC_SHA256 |

### <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>P2S 接続の構成方法

P2S 構成で必要な手順には、特有のものが非常に多くあります。 次の記事では、P2S 構成の詳細な手順と、VPN クライアント デバイスの構成方法のリンクをご紹介します。

* [P2S 接続の構成 - RADIUS 認証](point-to-site-how-to-radius-ps.md)

* [P2S 接続の構成 - Azure ネイティブ証明書認証](vpn-gateway-howto-point-to-site-rm-ps.md)

* [OpenVPN の構成](vpn-gateway-howto-openvpn.md)

## <a name="next-steps"></a>次のステップ

* [P2S 接続の構成 - RADIUS 認証](point-to-site-how-to-radius-ps.md)

* [P2S 接続の構成 - Azure ネイティブ証明書認証](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" は OpenVPN Inc. の商標です。**