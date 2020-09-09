---
title: P2S を使用したリモート作業:Azure VPN Gateway
description: このページでは、COVID-19 の世界的流行により、VPN Gateway を利用してリモートで作業できるようにする方法について説明します。
services: vpn-gateway
author: kumudD
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: alzam
ms.openlocfilehash: 302706b67900bd0dd82aabfa750bba48918b134a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86525366"
---
# <a name="remote-work-using-azure-vpn-gateway-point-to-site"></a>Azure VPN Gateway ポイント対サイトを使用したリモート作業

>[!NOTE]
>この記事では、Azure VPN Gateway、Azure、Microsoft ネットワーク、および Azure パートナー エコシステムを利用してリモートで作業し、COVID-19 の危機によって直面しているネットワークの問題を軽減する方法について説明します。
>

この記事では、COVID-19 の流行中に組織がユーザー向けにリモート アクセスを設定したり、容量を追加して既存のソリューションを補完したりするために使用できるオプションについて説明します。

Azure ポイント対サイトのソリューションはクラウド ベースであり、自宅で作業したいというユーザーの高まる要望に応えるために迅速にプロビジョニングできます。 簡単にスケールアップでき、増やした容量が不要になったら簡単かつ迅速に無効にできます。

## <a name="about-point-to-site-vpn"></a><a name="p2s"></a>ポイント対サイト VPN について

ポイント対サイト (P2S) VPN ゲートウェイ接続では、個々のクライアント コンピューターから仮想ネットワークへの、セキュリティで保護された接続を作成することができます。 P2S 接続は、クライアント コンピューターから接続を開始することによって確立されます。 このソリューションは、在宅勤務者が自宅や会議室など、遠隔地から Azure VNet またはオンプレミスのデータ センターに接続する場合に便利です。 この記事では、さまざまなシナリオに基づいてユーザーがリモートで作業できるようにする方法について説明します。

次の表は、クライアント オペレーティング システムと、それらに使用できる認証オプションを示しています。 既に使用中のクライアント OS に基づいて認証方法を選択すると便利です。 たとえば、接続する必要があるクライアント オペレーティング システムが混在している場合は、証明書ベースの認証を使用する OpenVPN を選択します。 また、ポイント対サイト VPN は、ルート ベースの VPN ゲートウェイでのみサポートされていることに注意してください。

![ポイント対サイト](./media/working-remotely-support/ostable.png "OS")

## <a name="scenario-1---users-need-access-to-resources-in-azure-only"></a><a name="scenario1"></a>シナリオ 1 - ユーザーが Azure のリソースにのみアクセスする必要がある

このシナリオでは、リモート ユーザーは、Azure 内のリソースにのみアクセスする必要があります。

![ポイント対サイト](./media/working-remotely-support/scenario1.png "シナリオ 1")

ユーザーが Azure リソースに安全に接続できるようにするために必要な手順は、大まかに次のとおりです。

1. Virtual Network ゲートウェイを作成します (存在しない場合)。
2. ゲートウェイでポイント対サイト VPN を構成します。

   * 証明書の認証の場合は、[このリンク](vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw)に従います。
   * OpenVPN の場合は、[このリンク](vpn-gateway-howto-openvpn.md)に従います。
   * Azure AD 認証の場合は、[このリンク](openvpn-azure-ad-tenant.md)に従います。
   * ポイント対サイト接続のトラブルシューティングの場合は、[このリンク](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)に従います。
3. VPN クライアント構成をダウンロードして配布します。
4. (証明書の認証を選択した場合) 証明書をクライアントに配布します。
5. Azure VPN に接続します。

## <a name="scenario-2---users-need-access-to-resources-in-azure-andor-on-prem-resources"></a><a name="scenario2"></a>シナリオ 2 - ユーザーが Azure またはオンプレミス (あるいは両方) のリソースにアクセスする必要がある

このシナリオでは、リモート ユーザーは、Azure とオンプレミスのデータ センター内のリソースにアクセスする必要があります。

![ポイント対サイト](./media/working-remotely-support/scenario2.png "シナリオ 2")

ユーザーが Azure リソースに安全に接続できるようにするために必要な手順は、大まかに次のとおりです。

1. Virtual Network ゲートウェイを作成します (存在しない場合)。
2. ゲートウェイでポイント対サイト VPN を構成します (上記の[シナリオ 1](#scenario1) を参照)。
3. BGP が有効になっている Azure Virtual Network ゲートウェイでサイト間トンネルを構成します。
4. Azure Virtual Network ゲートウェイに接続するようにオンプレミスのデバイスを構成します。
5. Azure portal からポイント対サイト プロファイルをダウンロードし、クライアントに配布します

サイト間 VPN トンネルを設定する方法については、[このリンク](vpn-gateway-howto-site-to-site-resource-manager-portal.md)を参照してください。

## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>ネイティブ Azure 証明書認証に関する FAQ

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>RADIUS 認証に関する FAQ

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>次の手順

* [P2S 接続の構成 - Azure AD 認証](openvpn-azure-ad-tenant.md)

* [P2S 接続の構成 - RADIUS 認証](point-to-site-how-to-radius-ps.md)

* [P2S 接続の構成 - Azure ネイティブ証明書認証](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" は OpenVPN Inc. の商標です。**