---
title: Azure Virtual WAN の概要 | Microsoft Docs
description: Virtual WAN の自動化されたスケーラブルなブランチ間接続、利用可能なリージョン、パートナーについて説明します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 641ed73f507d35d7af548d0164ef8e80979be217
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96530478"
---
# <a name="what-is-azure-virtual-wan"></a>Azure Virtual WAN とは

Azure Virtual WAN は、ネットワーク、セキュリティ、ルーティングのさまざまな機能をまとめて、1 つの運用インターフェイスを提供するネットワーク サービスです。 これらの機能には、ブランチ接続 (SD-WAN や VPN CPE などの Virtual WAN パートナー デバイスからの接続自動化経由)、サイト間 VPN 接続、リモート ユーザー VPN (ポイント対サイト) 接続、プライベート (ExpressRoute) 接続、クラウド内接続 (仮想ネットワークの推移的な接続)、VPN ExpressRoute 相互接続、ルーティング、Azure Firewall、プライベート接続のための暗号化が含まれます。 Virtual WAN の利用を開始するために、これらのすべてのユース ケースを用意する必要はありません。 単純に 1 つのユース ケースから始めて、ネットワークの発展に合わせて調整することができます。

Virtual WAN アーキテクチャは、ブランチ (VPN または SD-WAN デバイス)、ユーザー (Azure VPN、OpenVPN、または IKEv2 クライアント)、ExpressRoute 回線、仮想ネットワーク向けにスケールとパフォーマンスが組み込まれたスケールスケーリングするハブ アンド スポーク アーキテクチャです。 これにより、[グローバル トランジット ネットワーク アーキテクチャ](virtual-wan-global-transit-network-architecture.md)が可能になります。つまり、クラウドでホストされたネットワーク "ハブ" によって、さまざまな種類の "スポーク" に分散されている可能性があるエンドポイント間の推移的な接続が可能になります。

Azure リージョンは、接続先として選択できるハブとして機能します。 Standard Virtual WAN ではすべてのハブがフル メッシュで接続されるため、ユーザーは Any-to-Any (任意のスポーク) 接続に Microsoft バックボーンを簡単に使用できます。 SD-WAN または VPN デバイスとのスポーク接続の場合、ユーザーは Azure Virtual WAN 内で手動で設定するか、Virtual WAN CPE (SD-WAN または VPN) パートナー ソリューションを使用して Azure への接続を設定することができます。 Azure Virtual WAN での接続の自動化 (デバイス情報を Azure にエクスポートし、Azure 構成をダウンロードして接続を確立する機能) をサポートするパートナーの一覧が提供されています。 詳細については、[Virtual WAN のパートナーと場所](virtual-wan-locations-partners.md)に関する記事を参照してください。

![Virtual WAN のダイアグラム](./media/virtual-wan-about/virtualwan1.png)

この記事では、Azure Virtual WAN のネットワーク接続について簡単に説明します。 Virtual WAN には次の利点があります。

* **ハブ アンド スポーク型の統合された接続ソリューション:** オンプレミス サイトと Azure ハブの間のサイト間の構成と接続を自動化します。
* **自動化されたスポークの設定と構成:** 仮想ネットワークとワークロードを Azure ハブにシームレスに接続します。
* **直感的なトラブルシューティング:** Azure 内でエンド ツー エンドのフローを確認し、この情報を使用して必要なアクションを実行できます。

## <a name="basic-and-standard-virtual-wans"></a><a name="basicstandard"></a>Basic および Standard の仮想 WAN

仮想 WAN には、次の 2 つの種類があります:Basic と Standard です。 各種類に対して使用できる構成を、次の表に示します。

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

仮想 WAN をアップグレードする手順については、「[Virtual WAN を Basic から Standard にアップグレードする](upgrade-virtual-wan.md)」をご覧ください。

## <a name="architecture"></a><a name="architecture"></a>アーキテクチャ

仮想 WAN のアーキテクチャと Virtual WAN への移行方法については、次の記事を参照してください。

* [仮想 WAN のアーキテクチャ](migrate-from-hub-spoke-topology.md)
* [グローバル転送ネットワーク アーキテクチャ](virtual-wan-global-transit-network-architecture.md)

## <a name="virtual-wan-resources"></a><a name="resources"></a>Virtual WAN リソース

エンドツーエンドの仮想 WAN を構成するには、次のリソースを作成します。

* **virtualWAN:** virtualWAN リソースは、Azure ネットワークの仮想オーバーレイを表し、複数のリソースのコレクションです。 これには、仮想 WAN 内に配置するすべての仮想ハブへのリンクが含まれます。 Virtual WAN リソースは相互に分離されており、共通のハブを含むことはできません。 Virtual WAN 上の仮想ハブは互いに通信しません。

* **ハブ:** 仮想ハブは、Microsoft のマネージド仮想ネットワークです。 ハブには、接続を可能にするためのさまざまなサービス エンドポイントが含まれています。 オンプレミス ネットワーク (vpnsite) から、仮想ハブ内の VPN Gateway に接続したり、ExpressRoute 回線を仮想ハブに接続したり、またはモバイル ユーザーを仮想ハブ内のポイント対サイト ゲートウェイに接続したりすることもできます。 ハブは、リージョン内のネットワークのコアです。 1 つの Azure リージョンに配置できるハブは 1 つのみです。

  ハブ ゲートウェイは、ExpressRoute および VPN Gateway に使用する仮想ネットワーク ゲートウェイと同じではありません。 たとえば、Virtual WAN を使用する場合は、オンプレミス サイトから直接 VNet にサイト間接続を作成しません。 代わりに、ハブへのサイト間接続を作成します。 トラフィックは、常にハブ ゲートウェイを通過します。 これは、VNet には独自の仮想ネットワーク ゲートウェイが必要ないことを意味します。 Virtual WAN により、VNet は、仮想ハブと仮想ハブ ゲートウェイを介して簡単にスケーリングできます。

* **ハブ仮想ネットワーク接続:** ハブ仮想ネットワーク接続リソースは、ハブを仮想ネットワークにシームレスに接続するために使用します。

* **ハブ間接続** ハブはすべて、仮想 WAN 内で相互に接続されています。 これは、ローカル ハブに接続されているブランチ、ユーザー、または VNet が、接続されたハブのフル メッシュ アーキテクチャを使用して、別のブランチまたは VNet と通信できることを意味します。 また、ハブ間の VNet と同様に、ハブ間接続されたフレームワークを使用して、仮想ハブを通じて転送するハブ内の VNet を接続することもできます。

* **ハブのルート テーブル:** 仮想ハブのルートを作成して、そのルートを仮想ハブのルート テーブルに適用することができます。 仮想ハブのルート テーブルには、複数のルートを適用できます。

**追加の Virtual WAN リソース**

* **サイト:** このリソースは、サイト間接続にのみ使用されます。 サイト リソースは **vpnsite** です。 これは、オンプレミスの VPN デバイスとその設定を表します。 Virtual WAN パートナーと連携することで、この情報を Azure に自動的にエクスポートする組み込みのソリューションが得られます。

## <a name="types-of-connectivity"></a><a name="connectivity"></a>接続の種類

Virtual WAN では、次の種類の接続を使用できます。サイト間 VPN、ユーザー VPN (ポイント対サイト)、および ExpressRoute です。

### <a name="site-to-site-vpn-connections"></a><a name="s2s"></a>サイト間 VPN 接続

サイト間 IPsec または IKE (IKEv2) 接続を使用して、Azure 内のリソースに接続することができます。 詳細については、[Virtual WAN を使用したサイト間接続の作成](virtual-wan-site-to-site-portal.md)に関するページを参照してください。 

この種類の接続には、VPN デバイスまたは Virtual WAN パートナー デバイスが必要です。 Virtual WAN パートナーは、デバイス情報を Azure にエクスポートし、Azure 構成をダウンロードして、Azure Virtual WAN ハブへの接続を確立できる、接続の自動化を提供しています。 利用可能なパートナーと場所の一覧については、[Virtual WAN のパートナーと場所](virtual-wan-locations-partners.md)に関する記事を参照してください。 VPN または SD-WAN デバイス プロバイダーが前述のリンク先に記載されていない場合は、[Virtual WAN を使用したサイト間接続の作成](virtual-wan-site-to-site-portal.md)に関するページの手順を行って簡単に接続を設定できます。

### <a name="user-vpn-point-to-site-connections"></a><a name="uservpn"></a>ユーザー VPN (ポイント対サイト) 接続

IPsec/IKE (IKEv2) 接続または OpenVPN 接続を使用して、Azure 内のリソースに接続できます。 この種類の接続を使うには、クライアント コンピューター上で VPN クライアントを構成する必要があります。 詳細については、[ポイント対サイト接続の作成](virtual-wan-point-to-site-portal.md)に関する記事をご覧ください。

### <a name="expressroute-connections"></a><a name="er"></a>ExpressRoute 接続
ExpressRoute を使用すると、プライベート接続を介してオンプレミス ネットワークを Azure に接続できます。 接続を作成するには、[Virtual WAN を使用した ExpressRoute 接続の作成](virtual-wan-expressroute-portal.md)に関するページを参照してください。

### <a name="hub-to-vnet-connections"></a><a name="hub"></a>ハブから VNet への接続

Azure 仮想ネットワークを仮想ハブに接続することができます。 詳細については、[VNet のハブへの接続](virtual-wan-site-to-site-portal.md#vnet)に関するセクションを参照してください。

### <a name="transit-connectivity"></a><a name="transit"></a>トランジット接続

#### <a name="transit-connectivity-between-vnets"></a><a name="transit-vnet"></a>VNet 間のトランジット接続

Virtual WAN では、VNet 間のトランジット接続が可能です。 VNet では、仮想ネットワーク接続を介して仮想ハブに接続します。 すべての仮想ハブにはルーターが存在するため、**Standard Virtual WAN** の VNet 間でトランジット接続が有効になります。 このルーターは、仮想ハブが最初に作成されたときにインスタンス化されます。

ルーターには 4 つのルーティング状態があります。プロビジョニング済み、プロビジョニング中、失敗、または、なしです。 Azure portal で [仮想ハブ] ページに移動すると、 **[Routing status]\(ルーティングの状態\)** が表示されます。

* **なし** 状態は、仮想ハブでルーターがプロビジョニングされなかったことを示します。 これは、Virtual WAN の種類が *Basic* の場合、またはサービスが利用可能になる前に仮想ハブがデプロイされた場合に発生する可能性があります。
* **失敗** 状態は、インスタンス化中の失敗を示します。 ルーターをインスタンス化またはリセットするには、Azure portal の仮想ハブの [概要] ページに移動し、 **[Reset Router]\(ルーターのリセット\)** オプションを見つけます。

各仮想ハブ ルーターは、最大 50 Gbps の集約スループットをサポートしています。 仮想ネットワーク接続間の接続では、単一の仮想ハブに接続されているすべての VNet 全体で合計 2,000 VM のワークロードを想定しています。

#### <a name="transit-connectivity-between-vpn-and-expressroute"></a><a name="transit-er"></a>VPN と ExpressRoute 間のトランジット接続

Virtual WAN では、VPN と ExpressRoute 間のトランジット接続が可能です。 これは、VPN 接続サイトまたはリモート ユーザーが ExpressRoute 接続サイトと通信できることを意味します。 また、**ブランチ間フラグ** が有効であり、VPN と ExpressRoute 接続で BGP がサポートされていることが暗黙的に想定されています。 このフラグは、Azure portal の Azure Virtual WAN 設定にあります。 すべてのルート管理は仮想ハブ ルーターによって提供されます。これにより、仮想ネットワーク間のトランジット接続も可能になります。

### <a name="custom-routing"></a><a name="routing"></a>カスタム ルーティング

Virtual WAN には、高度なルーティング機能強化があります。 カスタム ルート テーブルの設定、ルートの関連付けと伝達による仮想ネットワーク ルーティングの最適化、ラベルを使用したルート テーブルの論理的なグループ化、多数のネットワーク仮想アプライアンス (NVA) または共有サービスのルーティング シナリオを簡素化する機能があります。

### <a name="global-vnet-peering"></a><a name="global"></a>グローバル VNET ピアリング

グローバル VNet ピアリングには、異なるリージョンにある 2 つの VNet を接続するメカニズムが用意されています。 Virtual WAN では、仮想ネットワーク接続によって VNet が仮想ハブと接続されます。 ユーザーはグローバル VNet ピアリングを明示的に設定する必要がありません。 同じリージョンの仮想ハブに接続された VNet には、VNet ピアリング料金が発生します。 別のリージョンの仮想ハブに接続された VNet には、グローバル VNet ピアリング料金が発生します。

### <a name="expressroute-traffic-encryption"></a><a name="encryption"></a>ExpressRoute トラフィックの暗号化

Azure Virtual WAN には、ExpressRoute トラフィックを暗号化する機能があります。 この手法により、パブリック インターネットを経由したり、パブリック IP アドレスを使用したりすることなく、ExpressRoute を経由してオンプレミス ネットワークと Azure 仮想ネットワークの間で暗号化されたトランジットを提供できます。 詳細については、[Virtual WAN 向けの ExpressRoute 経由の IPsec](vpn-over-expressroute.md)に関するページを参照してください。

## <a name="locations"></a><a name="locations"></a>場所

場所の情報については、[Virtual WAN のパートナーと場所](virtual-wan-locations-partners.md)に関する記事を参照してください。

## <a name="route-tables-for-basic-and-standard-virtual-wans"></a><a name="route"></a>Basic および Standard 仮想 WAN のルート テーブル

ルート テーブルに、関連付けと伝達の機能が備わりました。 既存のルート テーブルは、これらの機能を持たないルート テーブルです。 ハブ ルーティングに既存のルートがあり、新しい機能を使用する場合は、以下を考慮してください。

* **仮想ハブに既存のルートがある Standard Virtual WAN のお客様**:Azure portal のハブの [ルーティング] セクションにルートが既にある場合、まずそれらを削除してから、新しいルート テーブルを作成する必要があります (Azure portal のハブの [ルート テーブル] セクションにあります)。 Virtual WAN 内のすべてのハブに対して削除手順を実行することを強くお勧めします。

* **仮想ハブに既存のルートがある Basic Virtual WAN のお客様**:Azure portal のハブの [ルーティング] セクションにルートが既にある場合、まずそれらを削除してから、お使いの Basic Virtual WAN を Standard Virtual WAN に **アップグレード** します。 「[Virtual WAN を Basic から Standard にアップグレードする](upgrade-virtual-wan.md)」を参照してください。 Virtual WAN 内のすべてのハブに対して削除手順を実行することを強くお勧めします。

## <a name="faq"></a><a name="faq"></a>FAQ

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="whats-new"></a><a name="new"></a>新機能

RSS フィードを購読し、[Azure 更新情報](https://azure.microsoft.com/updates/?category=networking&query=VIRTUAL%20WAN)ページで、最新の Virtual WAN 機能の更新を確認します。

## <a name="next-steps"></a>次のステップ

[Virtual WAN を使用してサイト間接続を作成する](virtual-wan-site-to-site-portal.md)
