---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/22/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c46c11ead645b93d7710d1e11636037e4dcaf8e7
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444563"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Azure 仮想ネットワーク ゲートウェイ (VPN Gateway) と Azure Virtual WAN vpngateway の違いは何ですか。

Virtual WAN は、大規模なサイト間接続を提供し、スループット、スケーラビリティ、使いやすさを考慮して構築されています。 Virtual WAN 接続の ExpressRoute は、現在プレビューの段階です。 CPE 支店のデバイスは自動的にプロビジョニングされ、Azure Virtual WAN に接続されます。 これらのデバイスは、拡大を続ける SD-WAN および VPN パートナーのエコシステムから利用できます。 [推奨されるパートナーの一覧](https://go.microsoft.com/fwlink/p/?linkid=2019615)を参照してください。

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Azure Virtual WAN への支店接続とは

2 つのアクティブ/アクティブ IPsec トンネルで構成された、支店のデバイスから Azure Virtual WAN への接続。

### <a name="which-device-providers-virtual-wan-partners-are-supported-at-launch-time"></a>ローンチ段階でどのデバイス プロバイダー (Virtual WAN パートナー) がサポートされていますか。

現時点で、多くのパートナーが、完全に自動化された Virtual WAN エクスペリエンスをサポートしています。 詳細については、[Virtual WAN パートナー](https://go.microsoft.com/fwlink/p/?linkid=2019615)に関するページを参照してください。 

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Virtual WAN パートナーの自動化手順はどのようになっていますか。

パートナーの自動化手順については、[Virtual WAN パートナーの自動化](../articles/virtual-wan/virtual-wan-configure-automation-providers.md)に関するページを参照してください。

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>推奨パートナー デバイスを使用する必要がありますか。

いいえ。 Azure の IKEv2/IKEv1 IPsec サポートのための要件に準拠する、任意の VPN 対応デバイスを使用できます。

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Virtual WAN パートナーはどのように Azure Virtual WAN との接続を自動化しますか。

通常、ソフトウェア定義の接続ソリューションでは、コントローラーまたはデバイス プロビジョニング センターを使用してブランチ デバイスを管理します。 コントローラーは、Azure API を使用して、Azure Virtual WAN への接続を自動化できます。 詳細については、Virtual WAN パートナーの自動化に関するページを参照してください。

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>Virtual WAN によって既存の接続機能は変更されますか。   

既存の Azure 接続機能に変更はありません。

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Virtual WAN 用に使用できる新しい Resource Manager リソースはありますか。
  
はい。Virtual WAN では、新しい Resource Manager リソースが導入されます。 詳細については、[概要](https://go.microsoft.com/fwlink/p/?LinkId=2004389)に関するページを参照してください。

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>1 つのハブにはいくつの VPN デバイスを接続できますか。

1 つの仮想ハブにつき、最大で 1,000 件の接続がサポートされます。 各接続は、アクティブ/アクティブ構成の 2 つのトンネルで構成されます。 トンネルは、Azure Virtual Hub vpngateway で終了します。

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>オンプレミスの VPN デバイスは、複数のハブに接続できますか。

はい。 開始時のトラフィック フローは、オンプレミス デバイスから、最も近い Microsoft エッジへ、次に仮想ハブへとなります。

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Azure Virtual WAN ではグローバル VNet ピアリングがサポートされていますか。 

 いいえ。

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>仮想ハブに接続されているスポーク VNet は相互に通信できますか。

はい。 スポーク VNet は、仮想ネットワーク ピアリングを介して直接通信できます。 ただし、ハブを通じて推移的に通信する VNet はサポートされていません。 詳細については、「[仮想ネットワーク ピアリング](../articles/virtual-network/virtual-network-peering-overview.md)」を参照してください。

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Azure Virtual WAN で (NVA VNet 内に) 好みのネットワーク仮想アプライアンスをデプロイして使用できますか。

はい、お好みのネットワーク仮想アプライアンス (NVA) の VNet を Azure Virtual WAN に接続できます。 最初に、ハブ仮想ネットワーク接続を使用して、ネットワーク仮想アプライアンスの VNet をハブに接続します。 次に、仮想アプライアンスを指す次ホップを指定して仮想ハブ ルートを作成します。 仮想ハブのルート テーブルには、複数のルートを適用できます。 NVA の VNet に接続されているすべてのスポークの VNet ルートが確実にオンプレミス システムに伝達されるように、それらのスポークを追加で仮想ハブに接続する必要があります。

### <a name="can-an-nva-vnet-have-a-virtual-network-gateway"></a>NVA VNet に仮想ネットワーク ゲートウェイを配置できますか。

いいえ。 仮想ハブに接続されている場合、NVA VNet に仮想ネットワーク ゲートウェイを配置することはできません。 

### <a name="is-there-support-for-bgp"></a>BGP のサポートはありますか。

はい。BGP はサポートされています。 VPN サイトを作成するときに、BGP パラメーターをそこに指定することができます。 これは、そのサイトの Azure で作成されたすべての接続が BGP に対して有効になることを意味します。 さらに、NVA を含む VNet がある場合と、この NVA VNet が仮想 WAN ハブにアタッチされている場合、確実に NVA VNet からのルートが適切にアドバタイズされるように、NVA VNet にアタッチされているスポークで BGP を無効にする必要があります。 また、これらのスポーク VNet を仮想ハブ VNet に接続し、スポーク Vnet ルートがオンプレミス システムに確実に伝達されるようにします。

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>仮想ハブで UDR を使用してトラフィックを転送できますか。

はい、仮想ハブのルート テーブルを使用して、VNet にトラフィックを転送できます。

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Virtual WAN のライセンス情報や価格情報はありますか。
 
はい。 [価格](https://azure.microsoft.com/pricing/details/virtual-wan/)に関するページを参照してください。

### <a name="how-do-i-calculate-price-of-a-hub"></a>ハブの価格はどのように計算しますか。
 
ハブのサービスは課金対象となります。 たとえば、Azure Virtual WAN に接続するために必要な 10 個の支店またはオンプレミス デバイスは、ハブの VPN エンドポイントへの接続を意味します。 たとえば、1 スケール ユニットが 500 Mbps の VPN である場合、これは $0.361/時で課金されます。 各接続は $0.08/時で課金されます。 10 接続の場合、サービス/時の課金の合計は $0.361 + $.8/時となります。 Azure を離れるトラフィックに対するデータの料金が適用されます。 

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>ローンチ パートナーの一覧に記載されていない新しいパートナーが一覧に記載されるには、どうすればよいですか。

azurevirtualwan@microsoft.com に電子メールを送信する。 理想的なパートナーは、IKEv1 または IKEv2 IPsec 接続用にプロビジョニングできるデバイスを持つパートナーです。

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>使用しているデバイスが Virtual WAN パートナー リストにない場合はどうなりますか。 Azure Virtual WAN VPN に引き続き接続することはできますか。

はい。デバイスで IPsec IKEv1 または IKEv2 がサポートされている限り接続できます。 Virtual WAN パートナーによって、デバイスから Azure VPN エンドポイントへの接続が自動化されます。 これは '支店情報のアップロード'、'IPsec と構成'、'接続' などの手順が自動化されることを意味します。ご利用のデバイスが Virtual WAN パートナーのエコシステムからのものでない場合、手動で Azure の構成を取得し、デバイスを更新して IPsec 接続を設定するといった面倒な作業が必要になります。 

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Resource Manager テンプレートを使用して Azure Virtual WAN を構築することはできますか。

1 つのハブを持つ 1 つの Virtual WAN と、1 つの vpnsite から成るシンプルな構成は、[Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network)を使用して作成できます。 仮想 WAN は、主として REST またはポータルによって駆動されるサービスです。

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Virtual WAN では、支店間接続を行うことができますか?

はい。VPN 用または VPN から ExpressRoute 用の Virtual WAN では、支店間接続が利用可能です。 サイト間 VPN は GA である一方、ExpressRoute は、現在プレビューの段階です。

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>支店間トラフィックは、Azure Virtual WAN を横断しますか。

はい。

### <a name="how-is-virtual-wan-different-from-the-existing-azure-virtual-network-gateway"></a>Virtual WAN と既存の Azure Virtual Network Gateway は、どんな点が違いますか。

Virtual Network Gateway VPN は、トンネル数が 30 に制限されています。 大規模な VPN の場合、接続するには、Virtual WAN を使用する必要があります。 すべてのリージョンを対象にしたハブ内では、最大 1,000 個の支社接続を 20 Gbps で接続できます。 接続は、オンプレミス VPN デバイスから仮想ハブへのアクティブ/アクティブ型トンネルです。 リージョンごとに 1 つのハブを持つことができます。これは、ハブ全体で 1,000 を超える支店を接続できることを意味します。

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Virtual WAN では SD-WAN デバイスがどのようにサポートされますか。

Virtual WAN パートナーによって、Azure VPN エンドポイントへの IPsec 接続が自動化されます。 Virtual WAN パートナーが SD-WAN プロバイダーである場合、SD-WAN コントローラーで Azure VPN エンドポイントに対する自動化および IPsec 接続が管理されることを意味します。 SD-WAN デバイスに、専用の SD-WAN 機能の Azure VPN ではなく、独自のエンドポイントが必要である場合、SD-WAN エンドポイントを Azure VNet にデプロイし、Azure Virtual WAN と共存させることができます。

### <a name="does-this-virtual-wan-require-expressroute-from-each-site"></a>この Virtual WAN では、各サイトからの ExpressRoute が必要ですか。

いいえ。この Virtual WAN では、各サイトからの ExpressRoute は不要です。 デバイスから Azure Virtual WAN ハブへのインターネット リンク経由で標準 IPsec サイト間接続が使用されます。 サイトは、ExpressRoute 回線を使用してプロバイダーのネットワークに接続される場合があります。 仮想ハブ (プレビュー中) 内で ExpressRoute を使用して接続されているサイトの場合、サイトでは VPN と ExpressRoute の間に、支店間のトラフィック フローを持つことができます。 

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Azure Virtual WAN を使用する場合、ネットワーク スループットに制限はありますか。

支店の数は、ハブ/リージョンあたり 1,000 接続に制限されています。ハブ内では合計 20 Gbp までです。

### <a name="how-many-vpn-connections-does-a-virtual-wan-hub-support"></a>Virtual WAN ハブでサポートされる VPN 接続の数を教えてください。

Azure Virtual WAN ハブでは、最大で 1,000 件のサイト間接続と 10,000 件のポイント対サイト接続を同時にサポートできます。

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>VPN トンネルおよび接続の VPN スループットの合計を教えてください。

ハブの VPN スループットの合計は、選択されたスケール ユニットに基づき、最大で 20 Gbps です。 スループットはすべての既存の接続によって共有されます。

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Virtual WAN では、オンプレミス デバイスで複数の ISP を並行して利用できますか、それとも常に単一の VPN トンネルですか。

Virtual WAN VPN への接続は常に、支店で利用可能なリンクを使用する、アクティブ/アクティブ トンネルとなります (同じハブ/リージョン内の回復性の場合)。 このリンクは、オンプレミス支店の ISP リンクである可能性があります。 Virtual WAN では、並列で複数の ISP を設定するための特別なロジックは提供されません。支店で ISP 全体のフェールオーバーを管理するのは、完全に支店を中心としたネットワーク操作となります。 好きな SD-WAN ソリューションを使用して、支店でパスを選択することができます。

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Azure バックボーン上では、トラフィックはどのようにルーティングされますか。

トラフィックは、支店のデバイス -> ISP -> Microsoft Edge -> Microsoft DC (ハブ VNet) -> Microsoft Edge -> ISP -> 支店のデバイスというパターンに従います

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>このモデルでは、各サイトでどのようなものが必要ですか。 インターネット接続は必要ですか。

はい。 IPsec をサポートするインターネット接続および物理デバイスが必要です。統合[パートナー](https://go.microsoft.com/fwlink/p/?linkid=2019615)が提供するものをお勧めします。 オプションで、好みのデバイスから、構成と Azure への接続を手動で管理できます。
