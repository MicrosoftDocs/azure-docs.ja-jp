---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/03/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 72ddd0b6cd6c3e12417d3698c403f89312b531f4
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53111638"
---
VNet 間接続の FAQ は VPN ゲートウェイ接続が対象となります。 VNet ピアリングについては、「[仮想ネットワーク ピアリング](../articles/virtual-network/virtual-network-peering-overview.md)」を参照してください。

### <a name="does-azure-charge-for-traffic-between-vnets"></a>Azure では VNet 間のトラフィックに対して料金が発生しますか。

VPN ゲートウェイ接続を使用している場合は、同じリージョン内の VNet 間トラフィックは双方向で無料です。 リージョンを越えて送信される VNet 間エグレス トラフィックには、ソース リージョンに基づき、アウトバウンド VNet 内データ転送料金が課せられます。 詳細については、[VPN Gateway の価格に関するページ](https://azure.microsoft.com/pricing/details/vpn-gateway/)を参照してください。 VPN ゲートウェイではなく VNet ピアリングを使用して VNet を接続している場合は、[Virtual Network の価格](https://azure.microsoft.com/pricing/details/virtual-network/)に関するページを参照してください。

### <a name="does-vnet-to-vnet-traffic-travel-across-the-internet"></a>VNet 間のトラフィックは、インターネット経由で送信されますか。

いいえ。 VNet 間のトラフィックは、インターネットではなく Microsoft Azure のバックボーンを経由して送信されます。

### <a name="can-i-establish-a-vnet-to-vnet-connection-across-azure-active-directory-aad-tenants"></a>Azure Active Directory (AAD) テナント間で VNet 間接続を確立できますか。

はい、Azure VPN ゲートウェイを使用する VNet 間接続は、AAD テナント間で動作します。

### <a name="is-vnet-to-vnet-traffic-secure"></a>VNet 間のトラフィックはセキュリティで保護されていますか。

はい、IPsec/IKE 暗号化で保護されます。

### <a name="do-i-need-a-vpn-device-to-connect-vnets-together"></a>VNet 同士を接続するには VPN デバイスが必要ですか。

いいえ。 複数の Azure 仮想ネットワークを接続するときに、VPN デバイスは必要ありません (クロスプレミス接続が必要な場合を除く)。

### <a name="do-my-vnets-need-to-be-in-the-same-region"></a>VNet は同じリージョンに属している必要がありますか。

いいえ。 仮想ネットワークが属している Azure リージョン (場所) は異なっていてもかまいません。

### <a name="if-the-vnets-arent-in-the-same-subscription-do-the-subscriptions-need-to-be-associated-with-the-same-active-directory-tenant"></a>VNet が同じサブスクリプションに存在しない場合、サブスクリプションが同じ Active Directory テナントに関連付けられている必要がありますか。

いいえ。

### <a name="can-i-use-vnet-to-vnet-to-connect-virtual-networks-in-separate-azure-instances"></a>別々の Azure インスタンスに存在する仮想ネットワークを VNet 間接続で接続することはできますか。 

いいえ。 VNet 間接続でサポートされるのは、同じ Azure インスタンス内の仮想ネットワークの接続だけです。 たとえば、グローバル Azure と中国/ドイツ/米国政府の Azure インスタンスとの間で接続を作成することはできません。 これらのシナリオについては、サイト間 VPN 接続の使用をご検討ください。

### <a name="can-i-use-vnet-to-vnet-along-with-multi-site-connections"></a>VNet 間接続はマルチサイト接続と併用できますか。

はい。 仮想ネットワーク接続は、マルチサイト VPN と同時に使用することができます。

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>1 つの仮想ネットワークから接続できるオンプレミス サイトと仮想ネットワークの数を教えてください。

「[ゲートウェイの要件](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#requirements)」の表を参照してください。

### <a name="can-i-use-vnet-to-vnet-to-connect-vms-or-cloud-services-outside-of-a-vnet"></a>VNet 間接続を使用して VNet の外部の VM やクラウド サービスを接続することはできますか。

いいえ。 VNet 間接続によって仮想ネットワークを接続できます。 仮想ネットワーク内に存在しない仮想マシンやクラウド サービスを接続することはできません。

### <a name="can-a-cloud-service-or-a-load-balancing-endpoint-span-vnets"></a>クラウド サービスや負荷分散エンドポイントは複数の VNet にまたがることができますか。

いいえ。 クラウド サービスや負荷分散エンドポイントは、仮にそれらが相互に接続されていたとしても、仮想ネットワークの境界を越えることはできません。

### <a name="can-i-use-a-policybased-vpn-type-for-vnet-to-vnet-or-multi-site-connections"></a>VNet 間接続やマルチサイト接続にポリシー ベースの VPN の種類を使用することはできますか。

いいえ。 VNet 間接続とマルチサイト接続には、VPN の種類がルート ベース (以前は "動的ルーティング" と呼ばれていました) である Azure VPN Gateway が必要です。

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>VPN の種類がルート ベースの VNet を VPN の種類がポリシー ベースの VNet に接続できますか。

いいえ、両方の仮想ネットワークでルート ベース (以前は "動的ルーティング" と呼ばれていました) の VPN を使用している必要があります。

### <a name="do-vpn-tunnels-share-bandwidth"></a>VPN トンネルは帯域幅を共有しますか。

はい。 仮想ネットワークのすべての VPN トンネルは、Azure VPN ゲートウェイ上の使用可能な帯域幅を共有し、Azure 内の同じ VPN ゲートウェイ アップタイム SLA を共有します。

### <a name="are-redundant-tunnels-supported"></a>冗長トンネルはサポートされますか。

1 つの仮想ネットワーク ゲートウェイがアクティブ/アクティブ構成になっている場合、仮想ネットワークのペア間の冗長トンネルがサポートされます。

### <a name="can-i-have-overlapping-address-spaces-for-vnet-to-vnet-configurations"></a>VNet 間接続の構成で、重複するアドレス空間を使用できますか。

いいえ。 重複する IP アドレス範囲は使用できません。

### <a name="can-there-be-overlapping-address-spaces-among-connected-virtual-networks-and-on-premises-local-sites"></a>接続されている仮想ネットワークとオンプレミスのローカル サイトで、重複するアドレス空間を使用できますか。

いいえ。 重複する IP アドレス範囲は使用できません。



