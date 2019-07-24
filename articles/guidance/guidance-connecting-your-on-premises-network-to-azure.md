---
title: オンプレミス ネットワークを Azure に接続する | Microsoft Docs
description: Azure、Office 365、Dynamics CRM Online などの Microsoft クラウド サービスへの接続に利用できるさまざまな方法について説明し、比較します。
services: ''
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: ''
ms.assetid: 294d39ad-40e0-476a-a362-57911b1172b7
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2016
ms.author: cherylmc
ms.openlocfilehash: 9361c0b2ee84d7459bd432f5a8ce13fc5fd85ef8
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58408361"
---
# <a name="connecting-your-on-premises-network-to-azure"></a>オンプレミス ネットワークを Azure に接続する
Microsoft では、複数の種類のクラウド サービスを提供しています。 パブリック インターネット経由ですべてのサービスに接続できますが、インターネット上の仮想プライベート ネットワーク (VPN) トンネルを使用するか、または Microsoft への直接のプライベート接続経由で、一部のサービスに接続することもできます。 この記事は、使用する Microsoft クラウド サービスの種類に基づいて、最もニーズを満たす接続オプションを決定するのに役立ちます。 ほとんどの組織では、以下に説明する複数の接続の種類を利用しています。

## <a name="connecting-over-the-public-internet"></a>パブリック インターネット経由での接続
この接続の種類では、次に示すように、インターネット経由で直接 Microsoft クラウド サービスにアクセスできます。

![インターネット](./media/guidance-connecting-your-on-premises-network-to-azure/internet.png "インターネット")

この接続は一般に、Microsoft クラウド サービスに接続するために使用する最初の種類です。 次の表に、この接続の種類の長所と短所を示します。

| **メリット** | **考慮事項** |
| --- | --- |
| すべてのクライアント デバイスがインターネット上のすべての IP アドレスとポートに無制限にアクセスできる限り、オンプレミス ネットワークへの変更は不要です。 |トラフィックは、多くの場合に HTTPS を使用して暗号化されますが、パブリック インターネットを経由するため転送中にインターセプトされる可能性があります。 |
| パブリック インターネットに公開されているすべての Microsoft クラウド サービスに接続できます。 |接続がインターネットを経由するため予期しない待ち時間。 |
| 既存のインターネット接続を使用します。 | |
| すべての接続デバイスの管理が必要ありません。 | |

既存のインターネット接続を使用するため、この接続には接続や帯域幅のコストがかかりません。

## <a name="connecting-with-a-point-to-site-connection"></a>ポイント対サイト接続による接続
この接続の種類では、次に示すように、インターネット上の SSTP (Secure Socket トンネリング プロトコル) トンネル経由で一部の Microsoft クラウド サービスにアクセスできます。

![P2S](./media/guidance-connecting-your-on-premises-network-to-azure/p2s.png "ポイント対サイト接続")

接続は、既存のインターネット接続経由で行われますが、Azure VPN Gateway の使用が必要です。 次の表に、この接続の種類の長所と短所を示します。

| **メリット** | **考慮事項** |
| --- | --- |
| すべてのクライアント デバイスがインターネット上のすべての IP アドレスとポートに無制限にアクセスできる限り、オンプレミス ネットワークへの変更は不要です。 |トラフィックは、IPSec を使用して暗号化されますが、パブリック インターネットを経由するため転送中にインターセプトされる可能性があります。 |
| 既存のインターネット接続を使用します。 |接続がインターネットを経由するため予期しない待ち時間。 |
| ゲートウェイあたり最大 200 Mb/秒のスループット。 |オンプレミス ネットワーク上の各デバイスと、各デバイスが接続する必要がある各ゲートウェイ間の個別の接続の作成と管理が必要です。 |
| Azure Virtual Machines および Azure Cloud Services など、Azure Virtual Network (VNet) に接続できる Azure サービスへの接続に使用できます。 |Azure VPN Gateway の最小限の継続的管理が必要です。 |
| Microsoft Office 365 または Dynamics CRM Online への接続には使用できません。 | |
| VNet に接続できない Azure サービスへの接続には使用できません。 | |

[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) サービス、その[価格](https://azure.microsoft.com/pricing/details/vpn-gateway)、および送信データ転送の[料金](https://azure.microsoft.com/pricing/details/data-transfers)の詳細を確認してください。

## <a name="connecting-with-a-site-to-site-connection"></a>サイト対サイト接続による接続
この接続の種類では、次に示すように、インターネット上の IPSec トンネル経由で一部の Microsoft クラウド サービスにアクセスできます。

![S2S](./media/guidance-connecting-your-on-premises-network-to-azure/s2s.png "サイト対サイト接続")

接続は、既存のインターネット接続を介して行われますが、Azure VPN Gateway の使用が必要で、その関連料金と送信データ転送の料金がかかります。 次の表に、この接続の種類の長所と短所を示します。

| **メリット** | **考慮事項** |
| --- | --- |
| オンプレミス ネットワーク上のすべてのデバイスは、VNet に接続されている Azure サービスと通信できるため、デバイスごとに個々の接続を構成する必要はありません。 |トラフィックは、IPSec を使用して暗号化されますが、パブリック インターネットを経由するため転送中にインターセプトされる可能性があります。 |
| 既存のインターネット接続を使用します。 |接続がインターネットを経由するため予期しない待ち時間。 |
| Virtual Machines や Cloud Services などの VNet に接続できる Azure サービスへの接続に使用できます。 |オンプレミスの検証済み VPN デバイス*を構成し、管理する必要があります。 |
| ゲートウェイあたり最大 200 Mb/秒のスループット。 |Azure VPN Gateway の最小限の継続的管理が必要です。 |
| ユーザー定義のルートまたは Border Gateway Protocol (BGP)** を使用して、検査とログ記録のため、オンプレミス ネットワーク経由でクラウド仮想マシンから送信トラフィックを強制的に開始できます。 |Microsoft Office 365 または Dynamics CRM Online への接続には使用できません。 |
| VNet に接続できない Azure サービスへの接続には使用できません。 | |
| オンプレミス デバイスに戻る接続を開始するサービスを使用し、セキュリティ ポリシーでそれを必要とする場合は、オンプレミス ネットワークと Azure 間にファイアウォールが必要になることがあります。 | |

* *[検証済み VPN デバイス](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable)の一覧を表示します。
* **[ユーザー定義ルート](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)または [BGP](../vpn-gateway/vpn-gateway-bgp-overview.md) を使用して、Azure Vnet から、オンプレミス デバイスへのルーティングを強制する詳細を確認してください。

## <a name="connecting-with-a-dedicated-private-connection"></a>専用プライベート接続による接続
この接続の種類では、次に示すように、インターネットを経由しない、Microsoft への専用プライベート接続経由ですべての Microsoft クラウド サービスにアクセスします。

![ER](./media/guidance-connecting-your-on-premises-network-to-azure/er.png "ExpressRoute 接続")

接続には、ExpressRoute サービスの使用と接続プロバイダーへの接続が必要です。 次の表に、この接続の種類の長所と短所を示します。

| **メリット** | **考慮事項** |
| --- | --- |
| サービス プロバイダー経由の専用接続を使用するため、パブリック インターネット経由での転送中にトラフィックがインターセプトされる可能性はありません。 |オンプレミス ルーター管理が必要です。 |
| ExpressRoute 回線あたり最大 10 Gb/秒の帯域幅と各ゲートウェイへの最大 2 Gb/秒のスループット。 |接続プロバイダーへの専用接続が必要です。 |
| インターネットを経由しない Microsoft への専用接続であるため、予測可能な待ち時間。 |1 つまたは複数の Azure VPN Gateway の最小限の継続的管理が必要な場合があります (回線を VNet に接続する場合)。 |
| 必要に応じて、トラフィックを暗号化できますが、暗号化通信は必要ありません。 |オンプレミス デバイスに戻る接続を開始するクラウド サービスを使用する場合は、オンプレミス ネットワークと Azure 間にファイアウォールが必要になることがあります。 |
| すべての Microsoft クラウド サービスに直接接続できますが、いくつかの例外があります*。 |VNet に接続できないサービス用に、Microsoft データ センターに入るオンプレミス IP アドレスのネットワーク アドレス変換 (NAT) が必要です。** |
| BGP を使用した検査とログ記録のため、オンプレミス ネットワーク経由でクラウド仮想マシンから送信トラフィックを強制的に開始できます。 | |

* *ExpressRoute と共に使用できない [サービスの一覧](../expressroute/expressroute-faqs.md#supported-services)を表示します。 Azure サブスクリプションが Office 365 に接続することを許可されている必要があります。  詳細については、「[Office 365 向け Azure ExpressRoute](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd?ui=en-US&rs=en-US&ad=US&fromAR=1)」の記事を参照してください。
* **ExpressRoute [NAT](../expressroute/expressroute-nat.md) の要件の詳細を確認してください。

[ExpressRoute](../expressroute/expressroute-introduction.md)、それに関連する[料金](https://azure.microsoft.com/pricing/details/expressroute)、および[接続プロバイダー](../expressroute/expressroute-locations.md#locations)の詳細を確認してください。

## <a name="additional-considerations"></a>追加の考慮事項
* 上記のいくつかのオプションには、VNet 接続、ゲートウェイ接続、およびその他の条件に対し、サポートできるさまざまな上限があります。 Azure の[ネットワーク制限](../azure-subscription-service-limits.md#networking-limits)を確認し、それらのいずれかが、使用する接続の種類に影響を及ぼすかどうかを理解することをお勧めします。
* サイト対サイト VPN 接続から、ExpressRoute ゲートウェイと同じ VNet にゲートウェイを接続する場合、まず重要な制約について理解している必要があります。 詳細については、[ExpressRoute 接続とサイト間接続の共存の構成](../expressroute/expressroute-howto-coexist-resource-manager.md#limits-and-limitations)についての記事を参照してください。

## <a name="next-steps"></a>次の手順
以下のリソースでは、この記事で取り上げた接続の種類を実装する方法について説明します。

* [ポイント対サイト接続を実装する](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
* [サイト対サイト接続を実装する](guidance-hybrid-network-vpn.md)
* [専用プライベート接続を実装する](guidance-hybrid-network-expressroute.md)
* [高可用性のためのサイト対サイト接続による専用プライベート接続を実装する](guidance-hybrid-network-expressroute-vpn-failover.md)
