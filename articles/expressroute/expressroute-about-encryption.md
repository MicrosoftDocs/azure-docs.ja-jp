---
title: 'Azure ExpressRoute: 暗号化とは'
description: ExpressRoute の暗号化について説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: duau
ms.openlocfilehash: 693d2304324bdfcac298b3e20ddd0d882a16533c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92899870"
---
# <a name="expressroute-encryption"></a>ExpressRoute の暗号化
 
ExpressRoute では、お使いのネットワークと Microsoft のネットワークの間を行き来するデータの機密性と整合性を確保するために、暗号化テクノロジがいくつかサポートされています。

## <a name="point-to-point-encryption-by-macsec-faq"></a>MACsec によるポイントツーポイント暗号化に関する FAQ
MACsec は [IEEE 標準](https://1.ieee802.org/security/802-1ae/)です。 MAC (メディア アクセス コントロール) レベルまたはネットワーク レイヤー 2 でデータが暗号化されます。 [ExpressRoute Direct](expressroute-erdirect-about.md) 経由で Microsoft に接続するとき、MACsec を利用し、お使いのネットワーク デバイスと Microsoft のネットワーク デバイスの間の物理リンクを暗号化できます。 既定では、MACsec は ExpressRoute Direct ポートで無効になっています。 暗号化用に自分の MACsec キーを持ち込み、それを [Azure Key Vault](../key-vault/general/overview.md) に格納します。 キーを交換するタイミングを決定します。 以下でその他のよくあるご質問をご覧ください。
### <a name="can-i-enable-macsec-on-my-expressroute-circuit-provisioned-by-an-expressroute-provider"></a>ExpressRoute プロバイダーからプロビジョニングされた自分の ExpressRoute 回線で MACsec を有効にできますか。
いいえ。 MACsec では、あるエンティティ (すなわち、顧客) が所有するキーによって物理リンク上のあらゆるトラフィックが暗号化されます。 そのため、ExpressRoute Direct 上でのみ利用できます。
### <a name="can-i-encrypt-some-of-the-expressroute-circuits-on-my-expressroute-direct-ports-and-leave-other-circuits-on-the-same-ports-unencrypted"></a>ExpressRoute 回線の一部を自分の ExpressRoute Direct ポートで暗号化し、他の回線を同じポートで暗号化しないままにすることができますか。 
いいえ。 MACsec を有効にすると、BGP データ トラフィックや顧客データ トラフィックなど、すべてのネットワーク制御トラフィックが暗号化されます。 
### <a name="when-i-enabledisable-macsec-or-update-macsec-key-will-my-on-premises-network-lose-connectivity-to-microsoft-over-expressroute"></a>MACsec の有効/無効を設定したり、MACsec キーを更新したりすると、自分のオンプレミス ネットワークでは、ExpressRoute 経由での Microsoft への接続が失われますか。
はい。 MACsec 構成については、事前共有キー モードだけがサポートされます。 つまり、お使いのデバイスと Microsoft のデバイスの両方でキーを更新する必要があります (Microsoft の API 経由で)。 この変更はアトミックではなく、両者の間でキーが一致しない場合、接続が失われます。 構成変更のために保守管理期間を予定することを強くお勧めします。 ダウンタイムを最小限に抑えるために、他のリンクにネットワーク トラフィックを切り替えたら、ExpressRoute Direct の 1 つのリンクで構成を更新することをお勧めします。  
### <a name="will-traffic-continue-to-flow-if-theres-a-mismatch-in-macsec-key-between-my-devices-and-microsofts"></a>自分のデバイスと Microsoft のデバイスの間で MACsec キーが一致しない場合、トラフィックは引き続き流れますか。
いいえ。 MACsec が構成されているとき、キーの不一致が発生した場合、Microsoft への接続が失われます。 つまり、暗号化されていない接続にフォールバックし、データを露出させることがありません。 
### <a name="will-enabling-macsec-on-expressroute-direct-degrade-network-performance"></a>ExpressRoute Direct で MACsec を有効にすると、ネットワーク パフォーマンスが低下しますか。
MACsec の暗号化と復号は、Microsoft が使用するルーターのハードウェアで行われます。 Microsoft 側では、パフォーマンスに影響はありません。 しかしながら、お使いのデバイスのネットワーク ベンダーに問い合わせ、MACsec にパフォーマンス上の影響が出るかどうかを確認してください。
### <a name="which-cipher-suites-are-supported-for-encryption"></a>暗号化にはどの暗号スイートがサポートされていますか。
[Extended Packet Numbering](https://1.ieee802.org/security/802-1aebw/) バージョンの AES-128 および AES-256 のみサポートされています。 また、デバイスの MACsec 構成で [Secure Channel Identifier (SCI)](https://wikipedia.org/wiki/IEEE_802.1AE) を無効にする必要があります。 

## <a name="end-to-end-encryption-by-ipsec-faq"></a>IPsec によるエンドツーエンドの暗号化に関してよくあるご質問
IPsec は [IETF 標準](https://tools.ietf.org/html/rfc6071)です。 インターネット プロトコル (IP) レベルまたはネットワーク レイヤー 3 でデータを暗号化します。 お使いのオンプレミス ネットワークと Azure でお使いの仮想ネットワーク (VNET) の間でエンドツーエンドの接続を暗号化する目的で IPsec を利用できます。 以下でその他のよくあるご質問をご覧ください。
### <a name="can-i-enable-ipsec-in-addition-to-macsec-on-my-expressroute-direct-ports"></a>自分の ExpressRoute Direct ポートでは、MACsec に加えて IPsec を有効にできますか。
はい。 MACsec の場合、ユーザーと Microsoft との間の物理的な接続がセキュリティで保護されます。 IPsec の場合、ユーザーと Azure でお使いの仮想ネットワークの間のエンドツーエンド接続がセキュリティで保護されます。 いずれも個別に有効にすることができます。 
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-over-azure-private-peering"></a>Azure VPN ゲートウェイを利用し、Azure プライベート ピアリング経由で IPsec トンネルを設定できます。
はい。 Azure Virtual WAN を採用する場合は、[次の手順](../virtual-wan/vpn-over-expressroute.md)に従って、エンドツーエンド接続を暗号化することができます。 通常の Azure VNET を使用している場合は、[こちらの手順](../vpn-gateway/site-to-site-vpn-private-peering.md)に従って、Azure VPN ゲートウェイとオンプレミスの VPN ゲートウェイの間に IPsec トンネルを確立することができます。
### <a name="what-is-the-throughput-i-will-get-after-enabling-ipsec-on-my-expressroute-connection"></a>自分の ExpressRoute 接続で IPsec を有効にすると、どのようなスループットが得られますか。
Azure VPN ゲートウェイが使用されている場合、[こちらでパフォーマンス数値](../vpn-gateway/vpn-gateway-about-vpngateways.md)をご確認ください。 サードパーティ製の VPN ゲートウェイが使用されている場合、パフォーマンス数値についてはベンダーにお問い合わせください。

## <a name="next-steps"></a>次のステップ
MACsec 構成に関する詳細については、[MACsec の構成](expressroute-howto-macsec.md)に関するページを参照してください。

IPsec 構成に関する詳細については、[IPsec の構成](site-to-site-vpn-over-microsoft-peering.md)に関するページを参照してください。
