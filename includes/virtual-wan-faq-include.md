---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 03/05/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b7f79bebce5a086b268f4fc1080c33517555fb39
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102431544"
---
### <a name="is-azure-virtual-wan-in-ga"></a>Azure Virtual WAN は GA ですか。

はい。Azure Virtual WAN は一般提供 (GA) の状態にあります。 ただし、Virtual WAN はいくつかの機能とシナリオから構成されています。 Microsoft がプレビュー タグを適用する機能またはシナリオが Virtual WAN 内にあります。 その場合、特定の機能またはシナリオ自体がプレビューになります。 特定のプレビュー機能を使用しない場合、通常の GA サポートが適用されます。 プレビュー サポートについて詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

### <a name="does-the-user-need-to-have-hub-and-spoke-with-sd-wanvpn-devices-to-use-azure-virtual-wan"></a>ユーザーは、Azure Virtual WAN を使用するために、SD-WAN/VPN デバイスを利用したハブとスポークを用意する必要がありますか。

Virtual WAN では、サイト/サイト間 VPN 接続、ユーザー/P2S 接続、ExpressRoute 接続、Virtual Network 接続、VPN ExpressRoute 相互接続、VNet 間の推移的な接続、一元化されたルーティング、Azure Firewall、Firewall Manager のセキュリティ、監視、ExpressRoute 暗号化など、数多くの機能が 1 つの画面に組み込まれています。 Virtual WAN の利用を開始するために、これらのユースケースをすべて用意する必要はありません。 1 つのユース ケースのみで利用を開始できます。

Virtual WAN アーキテクチャは、スケールとパフォーマンスが組み込まれたハブ アンド スポーク アーキテクチャで、ブランチ (VPN または SD-WAN デバイス)、ユーザー (Azure VPN クライアント、openVPN、または IKEv2 クライアント)、ExpressRoute 回線、仮想ネットワークが仮想ハブのスポークとして機能します。 Standard Virtual WAN ではすべてのハブがフル メッシュで接続されるため、ユーザーは Any-to-Any (任意のスポーク) 接続に Microsoft バックボーンを簡単に使用できます。 SD-WAN/VPN デバイスを利用したハブとスポークの場合、ユーザーは Azure Virtual WAN ポータル内で手動で設定するか、Virtual WAN パートナーのCPE (SD-WAN/VPN) を使用して Azure への接続を設定することができます。

Virtual WAN パートナーによって、デバイス情報を Azure にエクスポートし、Azure 構成をダウンロードして、Azure Virtual WAN ハブへの接続を確立できる、接続の自動化が提供されます。 ポイント対サイト (ユーザー VPN) 接続の場合、[Azure VPN クライアント](https://go.microsoft.com/fwlink/?linkid=2117554)、OpenVPN、または IKEv2 クライアントがサポートされています。

### <a name="can-you-disable-fully-meshed-hubs-in-a-virtual-wan"></a>Virtual WAN でフル メッシュ ハブを無効にすることはできますか。

Virtual WAN には、次の 2 種類があります。Basic と Standard です。 Basic Virtual WAN では、ハブはメッシュされません。 Standard Virtual WAN では、ハブはメッシュされ、仮想 WAN が最初に設定されるときに自動的に接続されます。 ユーザーは特に何もする必要はありません。 また、ユーザーはフル メッシュ ハブを取得する機能を無効または有効にする必要もありません。 Virtual WAN には、任意のスポーク (VNet、VPN、または ExpressRoute) 間のトラフィックを操作するためのルーティング オプションが多数用意されています。 これにより、フル メッシュ ハブを簡単に利用でき、ニーズに応じてトラフィックをルーティングする柔軟性も得られます。

### <a name="how-are-availability-zones-and-resiliency-handled-in-virtual-wan"></a>Virtual WAN では Availability Zones と回復性はどのように処理されますか。

Virtual WAN は、ハブ内で使用可能になったハブとサービスのコレクションです。 ユーザーは、必要な数だけ Virtual WAN を利用できます。 Virtual WAN ハブには、VPN や ExpressRoute などの複数のサービスがあります。Availability Zones リージョンで Availability Zones がサポートされている場合、これらのサービス (Azure Firewall を除く) はそれぞれそのリージョンにデプロイされます。 ハブでの初期デプロイ後にリージョンが可用性ゾーンになった場合、ユーザーはゲートウェイを再作成できます。これにより、可用性ゾーンのデプロイがトリガーされます。 すべてのゲートウェイがアクティブ/アクティブとしてハブにプロビジョニングされます。これは、ハブ内に回復性が組み込まれていることを意味します。 ユーザーは、複数のリージョンにわたって回復性が必要な場合、複数のハブに接続できます。

Virtual WAN の概念はグローバルですが、実際の Virtual WAN リソースは Resource Manager ベースであり、リージョンでデプロイされます。 仮想 WAN リージョン自体に問題がある場合、その仮想 WAN 内のすべてのハブは引き続きそのまま機能しますが、ユーザーは、仮想 WAN リージョンが使用可能になるまで新しいハブを作成することはできません。

### <a name="what-client-does-the-azure-virtual-wan-user-vpn-point-to-site-support"></a>Azure Virtual WAN ユーザー VPN (ポイント対サイト) ではどのクライアントがサポートされていますか。

Virtual WAN では [Azure VPN クライアント](https://go.microsoft.com/fwlink/?linkid=2117554)、OpenVPN クライアント、または任意の IKEv2 クライアントがサポートされています。 Azure VPN クライアントでは、Azure AD 認証がサポートされています。少なくとも Windows 10 クライアント OS バージョン 17763.0 以降が必要です。  OpenVPN クライアントでは、証明書ベースの認証をサポートできます。 ゲートウェイで証明書ベースの認証が選択されると、ご利用のデバイスにダウンロードできる .ovpn* ファイルが表示されます。 IKEv2 では、証明書と RADIUS の両方の認証がサポートされます。 

### <a name="for-user-vpn-point-to-site--why-is-the-p2s-client-pool-split-into-two-routes"></a>ユーザー VPN (ポイント対サイト) の場合、P2S クライアント プールが 2 つのルートに分割されるのはなぜですか。

各ゲートウェイには 2 つのインスタンスがあり、接続されたクライアントに対して各ゲートウェイ インスタンスが個別にクライアント IP を割り当てることができ、仮想ネットワークからのトラフィックを適切なゲート ウェイ インスタンスに戻るようルーティングしてゲートウェイ インスタンス間のホップを避けるために分割が行われます。

### <a name="how-do-i-add-dns-servers-for-p2s-clients"></a>P2S クライアント用の DNS サーバーを追加するにはどうすればよいですか。

P2S クライアント用の DNS サーバーを追加するには、2つのオプションがあります。 クライアントではなく、ゲートウェイにカスタム DNS サーバーを追加する場合は、最初の方法が優先されます。

1. 次の PowerShell スクリプトを使用して、カスタム DNS サーバーを追加します。 ご利用の環境に合わせて値を置き換えてください。

   ```powershell
   // Define variables
   $rgName = "testRG1"
   $virtualHubName = "virtualHub1"
   $P2SvpnGatewayName = "testP2SVpnGateway1"
   $vpnClientAddressSpaces = 
   $vpnServerConfiguration1Name = "vpnServerConfig1"
   $vpnClientAddressSpaces = New-Object string[] 2
   $vpnClientAddressSpaces[0] = "192.168.2.0/24"
   $vpnClientAddressSpaces[1] = "192.168.3.0/24"
   $customDnsServers = New-Object string[] 2
   $customDnsServers[0] = "7.7.7.7"
   $customDnsServers[1] = "8.8.8.8"
   $virtualHub = $virtualHub = Get-AzVirtualHub -ResourceGroupName $rgName -Name $virtualHubName
   $vpnServerConfig1 = Get-AzVpnServerConfiguration -ResourceGroupName $rgName -Name $vpnServerConfiguration1Name

   // Specify custom dns servers for P2SVpnGateway VirtualHub while creating gateway
   createdP2SVpnGateway = New-AzP2sVpnGateway -ResourceGroupName $rgname -Name $P2SvpnGatewayName -VirtualHub $virtualHub -VpnGatewayScaleUnit 1 -VpnClientAddressPool $vpnClientAddressSpaces -VpnServerConfiguration $vpnServerConfig1 -CustomDnsServer $customDnsServers

   // Specify custom dns servers for P2SVpnGateway VirtualHub while updating existing gateway
   $P2SVpnGateway = Get-AzP2sVpnGateway -ResourceGroupName $rgName -Name $P2SvpnGatewayName
   $updatedP2SVpnGateway = Update-AzP2sVpnGateway -ResourceGroupName $rgName -Name $P2SvpnGatewayName  -CustomDnsServer $customDnsServers 

   // Re-generate Vpn profile either from PS/Portal for Vpn clients to have the specified dns servers
   ```
2. または、Windows 10 向けの Azure VPN クライアントを使用している場合は、ダウンロードしたプロファイル XML ファイルを変更して、 **\<dnsservers>\<dnsserver> \</dnsserver>\</dnsservers>** タグを追加してからインポートできます。

   ```powershell
      <azvpnprofile>
      <clientconfig>

          <dnsservers>
              <dnsserver>x.x.x.x</dnsserver>
              <dnsserver>y.y.y.y</dnsserver>
          </dnsservers>
    
      </clientconfig>
      </azvpnprofile>
   ```

### <a name="for-user-vpn-point-to-site--how-many-clients-are-supported"></a>ユーザー VPN (ポイント対サイト) の場合、サポートされているクライアントの数はいくつですか。

各ユーザー VPN P2S ゲートウェイには、2 つのインスタンスがあります。 各インスタンスでサポートされる接続数の上限は、スケール ユニットによって異なります。 スケール ユニット 1 から 3 では、500 接続がサポートされます。スケール ユニット 4 から 6 では、1,000 接続がサポートされます。スケール ユニット 7 から 12 では、5,000 接続がサポートされます。スケール ユニット 13 から 18 では最大 10,000 接続がサポートされます。

たとえば、ユーザーが 1 スケール ユニットを選択したとします。 各スケール ユニットはデプロイされた 1 つのアクティブ/アクティブ ゲートウェイを意味し、(この例では 2 つある) インスタンスのそれぞれで最大 500 接続がサポートされます。 ゲートウェイあたり 500 接続 * 2 を取得できますが、このスケール ユニットで 500 ではなく、1,000 を計画することを意味するわけではありません。 推奨される接続数を超えた場合は、余分な 500 の接続が中断される可能性があるため、インスタンスの処理が必要になることがあります。 また、スケール ユニットをスケールアップまたはスケールダウンする場合や、VPN ゲートウェイのポイント対サイト構成を変更する場合に備えて、必ずダウンタイムを計画してください。

### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Azure 仮想ネットワーク ゲートウェイ (VPN ゲートウェイ) と Azure Virtual WAN VPN ゲートウェイの違いは何ですか。

Virtual WAN は、大規模なサイト間接続を提供し、スループット、スケーラビリティ、使いやすさを考慮して構築されています。 Virtual WAN の VPN ゲートウェイにサイトを接続するとき、そのゲートウェイは、"VPN" タイプのゲートウェイを使用する通常の仮想ネットワーク ゲートウェイとは異なります。 同様に、ExpressRoute 回線を Virtual WAN ハブに接続するとき、ExpressRoute ゲートウェイには、"ExpressRoute" タイプのゲートウェイを使用する通常の仮想ネットワーク ゲートウェイとは異なるリソースが使用されます。 

Virtual WAN は、VPN と ExpressRoute のどちらについても、最大 20 Gbps の総スループットをサポートします。 また、CPE 支店デバイス パートナーのエコシステムとの接続に関して、Virtual WAN は自動化に対応しています。 CPE 支店デバイスには、自動的にプロビジョニングして Azure Virtual WAN に接続する自動化機能が組み込まれています。 これらのデバイスは、拡大を続ける SD-WAN および VPN パートナーのエコシステムから利用できます。 [推奨されるパートナーの一覧](../articles/virtual-wan/virtual-wan-locations-partners.md)を参照してください。

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>Virtual WAN と Azure 仮想ネットワーク ゲートウェイは、どんな点が違いますか。

仮想ネットワーク ゲートウェイの VPN は、トンネル数が 30 に制限されています。 大規模な VPN の場合、接続するには、Virtual WAN を使用する必要があります。 各リージョン (仮想ハブ) につき最大 1,000 個の支店接続を、ハブあたり合計 20 Gbps で接続できます。 接続は、オンプレミス VPN デバイスから仮想ハブへのアクティブ/アクティブ型トンネルです。 リージョンごとに 1 つのハブを持つことができます。これは、ハブ全体で 1,000 を超える支店を接続できることを意味します。

### <a name="what-is-a-virtual-wan-gateway-scale-unit"></a>Virtual WAN ゲートウェイ スケール ユニットとは何ですか。

スケール ユニットは、仮想ハブにおけるゲートウェイの総スループットを選択するために定義された単位です。 VPN の 1 スケール ユニットは 500 Mbps です。 ExpressRoute の 1 スケール ユニットは 2 Gbps です。 例:VPN の 10 スケール ユニットは 5 Gbps (= 500 Mbps * 10) を意味します。

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>どのデバイス プロバイダー (Virtual WAN パートナー) がサポートされていますか。

現時点で、多くのパートナーが、完全に自動化された Virtual WAN エクスペリエンスをサポートしています。 詳細については、[Virtual WAN パートナー](../articles/virtual-wan/virtual-wan-locations-partners.md)に関するページを参照してください。

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Virtual WAN パートナーの自動化手順はどのようになっていますか。

パートナーの自動化手順については、[Virtual WAN パートナーの自動化](../articles/virtual-wan/virtual-wan-configure-automation-providers.md)に関するページを参照してください。

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>推奨パートナー デバイスを使用する必要がありますか。

いいえ。 Azure の IKEv2/IKEv1 IPsec サポートのための要件に準拠する、任意の VPN 対応デバイスを使用できます。 また、Virtual WAN には、Azure Virtual WAN への接続を自動化して大規模な IPsec VPN 接続の設定を容易にする、CPE パートナー ソリューションがあります。

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Virtual WAN パートナーはどのように Azure Virtual WAN との接続を自動化しますか。

通常、ソフトウェア定義の接続ソリューションでは、コントローラーまたはデバイス プロビジョニング センターを使用してブランチ デバイスを管理します。 コントローラーは、Azure API を使用して、Azure Virtual WAN への接続を自動化できます。 自動化には、支店情報のアップロードや、Azure の構成のダウンロード、Azure 仮想ハブに対する IPsec トンネルの設定、支店デバイスから Azure Virtual WAN への接続の自動設定が含まれます。 何百もの支店が存在する場合は、Virtual WAN CPE パートナーを使用して接続するのが簡単です。オンボーディング エクスペリエンスにより、大規模な IPsec 接続の設定、構成、管理が不要となるためです。 詳細については、「[Virtual WAN partner automation](../articles/virtual-wan/virtual-wan-configure-automation-providers.md)」 (Virtual WAN パートナーの自動化) を参照してください。

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>使用しているデバイスが Virtual WAN パートナー リストにない場合はどうなりますか。 Azure Virtual WAN VPN に引き続き接続することはできますか。

はい。デバイスで IPsec IKEv1 または IKEv2 がサポートされている限り接続できます。 Virtual WAN パートナーによって、デバイスから Azure VPN エンドポイントへの接続が自動化されます。 これは、"ブランチ情報のアップロード"、"IPsec と構成"、"接続" などの手順が自動化されることを意味します。 ご利用のデバイスが Virtual WAN パートナーのエコシステムからのものでない場合、手動で Azure の構成を取得し、デバイスを更新して IPsec 接続を設定するなどの面倒な作業が必要になります。

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>ローンチ パートナーの一覧に記載されていない新しいパートナーが一覧に記載されるには、どうすればよいですか。

すべての仮想 WAN API はオープン API です。 [Virtual WAN パートナーの自動化](../articles/virtual-wan/virtual-wan-configure-automation-providers.md)に関するドキュメントを参照して、技術的実現可能性を評価することができます。 理想的なパートナーは、IKEv1 または IKEv2 IPsec 接続用にプロビジョニングできるデバイスを持つパートナーです。 前述の自動化ガイドラインに基づいて、会社での CPE デバイスの自動化作業が完了したら、azurevirtualwan@microsoft.com にご連絡いただき、こちら (「[パートナー経由の接続]( ../articles/virtual-wan/virtual-wan-locations-partners.md#partners)」) に一覧表示されるようにしてください。 特定の会社のソリューションが Virtual WAN パートナーとして一覧表示されるようにしたいとお考えのお客様は、azurevirtualwan@microsoft.com に電子メールを送信し、会社の連絡先を Virtual WAN にしてください。

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Virtual WAN では SD-WAN デバイスがどのようにサポートされますか。

Virtual WAN パートナーによって、Azure VPN エンドポイントへの IPsec 接続が自動化されます。 Virtual WAN パートナーが SD-WAN プロバイダーである場合、SD-WAN コントローラーで Azure VPN エンドポイントに対する自動化および IPsec 接続が管理されることを意味します。 SD-WAN デバイスに、専用の SD-WAN 機能の Azure VPN ではなく、独自のエンドポイントが必要である場合、SD-WAN エンドポイントを Azure VNet にデプロイし、Azure Virtual WAN と共存させることができます。

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>1 つのハブにはいくつの VPN デバイスを接続できますか。

1 つの仮想ハブにつき、最大で 1,000 件の接続がサポートされます。 各接続は 4 つのリンクから成り、各リンク接続は、アクティブ/アクティブ構成の 2 つのトンネルをサポートします。 トンネルは、Azure 仮想ハブの VPN ゲートウェイで終了します。 リンクは、ブランチまたは VPN デバイスの物理的な ISP リンクを表します。

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Azure Virtual WAN への支店接続とは

ブランチまたは VPN デバイスから Azure Virtual WAN への接続は、仮想ハブ内の VPN サイトと Azure VPN Gateway を仮想的に接続する VPN 接続です。

### <a name="what-happens-if-the-on-premises-vpn-device-only-has-1-tunnel-to-an-azure-virtual-wan-vpn-gateway"></a>オンプレミスの VPN デバイスに Azure Virtual WAN VPN ゲートウェイへのトンネルが 1 つしかない場合はどうなりますか。

Azure Virtual WAN 接続は、2 つのトンネルから成ります。 Virtual WAN VPN ゲートウェイは、アクティブ/アクティブ モードで仮想ハブにデプロイされます。つまり、オンプレミス デバイスから各インスタンスを終点とするトンネルが別々に存在します。 これは、すべてのユーザーに推奨されます。 ただし、Virtual WAN VPN ゲートウェイ インスタンス 1 つに対してユーザーがトンネルを 1 つしか確保していない場合、なんらかの理由 (メンテナンス、パッチなど) でゲートウェイ インスタンスがオフラインになると、トンネルはセカンダリ アクティブ インスタンスに移動され、再接続が生じることがあります。 BGP セッションは、インスタンス間を移動しません。

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>オンプレミスの VPN デバイスを複数のハブに接続できますか。

はい。 開始時のトラフィック フローは、オンプレミス デバイスから、最も近い Microsoft ネットワーク エッジへ、次に仮想ハブへとなります。

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Virtual WAN 用に使用できる新しい Resource Manager リソースはありますか。
  
はい。Virtual WAN には新しい Resource Manager リソースがあります。 詳細については、[概要](../articles/virtual-wan/virtual-wan-about.md)に関するページを参照してください。

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Azure Virtual WAN で (NVA VNet 内に) 好みのネットワーク仮想アプライアンスをデプロイして使用できますか。

はい、お好みのネットワーク仮想アプライアンス (NVA) の VNet を Azure Virtual WAN に接続できます。

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>仮想ハブ内にネットワーク仮想アプライアンスを作成することはできますか。

ネットワーク仮想アプライアンス (NVA) を仮想ハブ内にデプロイすることはできません。 しかし、仮想ハブに接続されているスポーク VNet で作成し、ニーズに応じてトラフィックを転送するための適切なルーティングを有効にすることができます。

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>スポーク VNet に仮想ネットワーク ゲートウェイを配置することはできますか。

いいえ。 仮想ハブに接続されている場合、スポーク VNet に仮想ネットワーク ゲートウェイを配置することはできません。

### <a name="is-there-support-for-bgp-in-vpn-connectivity"></a>VPN 接続で BGP はサポートされていますか。

はい。BGP はサポートされています。 VPN サイトを作成するときに、BGP パラメーターをそこに指定することができます。 これは、そのサイトの Azure で作成されたすべての接続が BGP に対して有効になることを意味します。

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Virtual WAN のライセンス情報や価格情報はありますか。

はい。 [価格](https://azure.microsoft.com/pricing/details/virtual-wan/)に関するページを参照してください。

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Resource Manager テンプレートを使用して Azure Virtual WAN を構築することはできますか。

1 つのハブを持つ 1 つの Virtual WAN と、1 つの vpnsite から成るシンプルな構成は、[クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network)を使用して作成できます。 Virtual WAN は、主として REST またはポータルによって駆動されるサービスです。

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other-v2v-transit"></a>仮想ハブに接続されているスポーク VNet は相互に通信 (V2V 転送) できますか。

はい。 Standard Virtual WAN では、VNet が接続されている Virtual WAN ハブを介して、VNet 間の推移的な接続がサポートされています。 Virtual WAN の用語では、これらのパスを、VNet が単一のリージョン内の Virtual WAN ハブに接続されている場合は "ローカル Virtual WAN VNet 転送" と呼び、VNet が 2 つ以上のリージョンにまたがる複数の Virtual WAN ハブを通じて接続されている場合は "グローバル Virtual WAN VNet 転送" と呼びます。

一部のシナリオでは、ローカルまたはグローバルの Virtual WAN VNet 転送に加えて、[仮想ネットワーク ピアリング](../articles/virtual-network/virtual-network-peering-overview.md)を使用して、スポーク VNet を相互に直接ピアリングすることもできます。 この場合、VNet ピアリングは、Virtual WAN ハブを介した推移的な接続よりも優先されます。

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Virtual WAN では、支店間接続を行うことができますか?

はい。Virtual WAN では、支店間接続が利用可能です。 ブランチは、概念的には VPN サイト、ExpressRoute 回線、またはポイント対サイト (ユーザー VPN) ユーザーに適用されます。 ブランチ間の有効化は既定で有効になっており、WAN の **[構成]** 設定で確認できます。 これにより、VPN ブランチまたはユーザーが他の VPN ブランチに接続できるようになります。また、VPN および ExpressRoute ユーザーの間での転送接続性が有効になります。

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>支店間トラフィックは、Azure Virtual WAN を横断しますか。

はい。 支店間トラフィックは、Azure Virtual WAN を横断します。

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>Virtual WAN では、各サイトからの ExpressRoute が必要ですか。

いいえ。 この Virtual WAN では、各サイトからの ExpressRoute は不要です。 サイトは、ExpressRoute 回線を使用してプロバイダーのネットワークに接続される場合があります。 仮想ハブへの ExpressRoute および同じハブへの IPsec VPN を使用して接続されているサイトの場合、仮想ハブで VPN および ExpressRoute ユーザー間の転送接続性が提供されます。

### <a name="is-there-a-network-throughput-or-connection-limit-when-using-azure-virtual-wan"></a>Azure Virtual WAN を使用する場合、ネットワーク スループットまたは接続に制限はありますか。

ネットワーク スループットは、仮想 WAN ハブのサービスごとにあります。 仮想 WAN は必要な数だけ使用できますが、各仮想 WAN ではリージョンごとに 1 つのハブが許可されます。 各ハブでは、VPN の総スループットは最大 20 Gbps、ExpressRoute の総スループットは最大 20 Gbps、ユーザー VPN (ポイント対サイト) VPN の総スループットは最大 20 Gbps となります。 仮想ハブのルーターでは、VNet 対 VNet のトラフィック フローに対して最大 50 Gbps がサポートされ、単一の仮想ハブに接続されているすべての VNet で合計 2,000 の VM ワークロードが想定されます。

VPN サイトでは、ハブに接続するときに、複数の接続を使用します。 Virtual WAN では、仮想ハブあたり最大 1,000 の接続または 2,000 の IPsec トンネルがサポートされます。 リモート ユーザーは、仮想ハブに接続するときに、P2S VPN ゲートウェイに接続します。これにより、仮想ハブの P2S VPN ゲートウェイに対して選択されたスケール ユニット (帯域幅) に応じて、最大 10,000 のユーザーがサポートされます。

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>VPN トンネルおよび接続の VPN スループットの合計を教えてください。

ハブの VPN スループットの合計は、VPN ゲートウェイの選択されたスケール ユニットに基づき、最大で 20 Gbps となります。 スループットはすべての既存の接続によって共有されます。 接続内の各トンネルは最大 1 Gbps をサポートできます。

### <a name="can-i-use-nat-t-on-my-vpn-connections"></a>VPN 接続で NAT-T を使用できますか。

はい。NAT トラバーサル (NAT-T) がサポートされています。 Virtual WAN VPN ゲートウェイでは、NAT に類似する機能が IPsec トンネルとの間の内部パケットに対して実行されることはありません。 この構成では、オンプレミスのデバイスによって IPsec トンネルが開始されるようにします。

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-portal-how-do-i-configure-that"></a>仮想ハブの 20 Gbps の設定がポータルに表示されません。 どのように構成すればよいですか。

ポータルでハブ内の VPN ゲートウェイに移動し、スケール ユニットをクリックして適切な設定に変更します。

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Virtual WAN では、オンプレミス デバイスで複数の ISP を並行して利用できますか、それとも常に単一の VPN トンネルとなりますか。

オンプレミスのデバイス ソリューションでは、トラフィック ポリシーを適用して、Azure Virtual WAN ハブ (仮想ハブの VPN ゲートウェイ) への複数のトンネルにまたがるトラフィックを操作できます。

### <a name="what-is-global-transit-architecture"></a>グローバル トランジット アーキテクチャとは何ですか。

グローバル トランジット アーキテクチャについては、「[グローバル トランジット ネットワーク アーキテクチャと Virtual WAN](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md)」を参照してください。

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Azure バックボーン上では、トラフィックはどのようにルーティングされますか。

トラフィックは、支店のデバイス -> ISP -> Microsoft ネットワーク エッジ -> Microsoft DC (ハブ VNet) -> Microsoft ネットワーク エッジ -> ISP -> 支店のデバイスというパターンに従います

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>このモデルでは、各サイトでどのようなものが必要ですか。 インターネット接続は必要ですか。

はい。 IPsec をサポートするインターネット接続および物理デバイスが必要です。統合 [Virtual WAN パートナー](../articles/virtual-wan/virtual-wan-locations-partners.md)が提供するものをお勧めします。 必要に応じて、好みのデバイスから、構成と Azure への接続を手動で管理できます。

### <a name="how-do-i-enable-default-route-00000-for-a-connection-vpn-expressroute-or-virtual-network"></a>接続 (VPN、ExpressRoute、または Virtual Network) の既定のルート (0.0.0.0/0) を有効にするにはどうすればよいですか。

仮想ハブは、仮想ネットワーク、サイト間 VPN、または ExpressRoute 接続に対し、学習した既定のルートを伝達することができます (対応するフラグが、その接続で "有効" になっている場合)。 このフラグは、ユーザーが仮想ネットワーク接続、VPN 接続、または ExpressRoute 接続を編集するときに表示されます。 サイトまたは ExpressRoute 回線がハブに接続されると、このフラグは既定で無効になります。 VNet を仮想ハブに接続するための仮想ネットワーク接続が追加されたときは、既定で有効になります。

既定のルートの起点は Virtual WAN ハブではありません。Virtual WAN ハブにファイアウォールをデプロイした結果としてそのハブが既定のルートを既に学習している場合、または接続されている別のサイトで強制トンネリングが有効な場合に、既定のルートが伝達されます。 既定のルートはハブ間で伝達されません。

### <a name="how-does-the-virtual-hub-in-a-virtual-wan-select-the-best-path-for-a-route-from-multiple-hubs"></a>仮想 WAN 内の仮想ハブは、どのようにして複数のハブからのルートに最適なパスを選択するのですか。

仮想ハブが複数のリモート ハブからの同じルートを学習する場合、次の順序で決定されます。

1. プレフィックスの最長一致。
1. インターハブ上のローカル ルート (仮想ハブからは 65520-65520 がインターハブ AS に対して割り当てられます)。
1. BGP 経由の静的ルート: これは、仮想ハブ ルーターによって行われる決定に対するコンテキストにあります。 しかし、決定を行うのが VPN ゲートウェイであり、サイトで BGP 経由のルートをアドバタイズする場合、または静的アドレス プレフィックスを指定する場合は、静的ルートが BGP ルートよりも優先されることがあります。
1. VPN 経由の ExpressRoute (ER): コンテキストがローカル ハブの場合、VPN よりも ER が優先されます。 ExpressRoute 回線間の転送接続は、Global Reach 経由でのみ使用できます。 そのため、ExpressRoute 回線が 1 つのハブに接続されており、VPN 接続を使用して異なるハブに接続されている別の ExpressRoute 回線があるシナリオの場合、ハブ間のシナリオでは VPN が優先される可能性があります。
1. AS パスの長さ。

### <a name="does-the-virtual-wan-hub-allow-connectivity-between-expressroute-circuits"></a>Virtual WAN ハブで ExpressRoute 回線間の接続性は許可されますか。

ER 間の転送は常に Global Reach 経由で行われます。 仮想ハブ ゲートウェイは、DC または Azure リージョンにデプロイされます。 2 つの ExpressRoute 回線が Global Reach 経由で接続されている場合、トラフィックがエッジ ルーターから仮想ハブ DC まで到達する必要はありません。

### <a name="is-there-a-concept-of-weight-in-azure-virtual-wan-expressroute-circuits-or-vpn-connections"></a>Azure Virtual WAN ExpressRoute 回線または VPN 接続に重みの概念はありますか。

複数の ExpressRoute 回線が仮想ハブに接続されている場合、接続のルーティングの重みによって、仮想ハブ内の ExpressRoute で一方の回線をもう一方より優先するメカニズムが提供されます。 VPN 接続に重みを設定するメカニズムはありません。 Azure では常に、1 つのハブ内の VPN 接続より ExpressRoute 接続が優先されます。

### <a name="does-virtual-wan-prefer-expressroute-over-vpn-for-traffic-egressing-azure"></a>Virtual WAN では、Azure から送信されるトラフィックに対して、VPN より ExpressRoute が優先されますか。

はい。 Virtual WAN では、Azure から送信されるトラフィックに対して、VPN より ExpressRoute が優先されます。

### <a name="when-a-virtual-wan-hub-has-an-expressroute-circuit-and-a-vpn-site-connected-to-it-what-would-cause-a-vpn-connection-route-to-be-preferred-over-expressroute"></a>Virtual WAN ハブに ExpressRoute 回線と VPN サイトが接続されている場合、VPN 接続ルートが ExpressRoute より優先される原因は何ですか。

ExpressRoute 回線が仮想ハブに接続されている場合、Microsoft エッジ ルーターは、オンプレミスと Azure の間の通信での最初のノードになります。 これらのエッジ ルーターは、Virtual WAN ExpressRoute ゲートウェイと通信します。次に、Virtual WAN のあらゆるゲートウェイの間のルートをすべて制御する仮想ハブ ルーターからルートを学習します。 Microsoft エッジ ルーターは、オンプレミスから学習したルートより高い優先順位である仮想ハブ ExpressRoute ルートを処理します。

なんらかの理由により、仮想ハブがルートを学習するためのプライマリ メディアに VPN 接続がなった場合は (ExpressRoute と VPN の間のフェールオーバー シナリオなど)、VPN サイトの AS パスが長い場合を除き、仮想ハブは引き続き ExpressRoute ゲートウェイと VPN 学習ルートを共有します。 これにより、Microsoft エッジ ルーターでは、オンプレミス ルートより VPN ルートが優先されます。

### <a name="when-two-hubs-hub-1-and-2-are-connected-and-there-is-an-expressroute-circuit-connected-as-a-bow-tie-to-both-the-hubs-what-is-the-path-for-a-vnet-connected-to-hub-1-to-reach-a-vnet-connected-in-hub-2"></a>2 つのハブ (ハブ 1 と 2) が接続されていて、両方のハブに対して (蝶ネクタイのような形で) 接続されている ExpressRoute 回線がある場合、ハブ 2 に接続されている VNet に到達するために、ハブ 1 に接続されている VNet のパスはどのようなものですか。

現在の動作では、VNet 対 VNet 接続の場合、ハブ間よりも ExpressRoute 回線パスが優先されます。 しかし、これは仮想 WAN セットアップではお勧めできません。 Virtual WAN チームは、ExpressRoute パスよりハブ間を優先できるようにするための修正に取り組んでいます。 複数の ExpressRoute 回線 (異なるプロバイダー) を 1 つのハブに接続し、リージョン間のトラフィック フローに対して Virtual WAN によって提供されるハブ間接続を使用することをお勧めします。

### <a name="can-hubs-be-created-in-different-resource-group-in-virtual-wan"></a>ハブは、Virtual WAN 内の別のリソース グループに作成できますか。

はい。 このオプションは、現在、PowerShell を介してのみ使用可能です。 Virtual WAN ポータルでは、ハブが仮想 WAN リソース自体と同じリソース グループに含まれている必要があります。

### <a name="is-there-support-for-ipv6-in-virtual-wan"></a>Virtual WAN に IPv6 のサポートはありますか。

IPv6 は、Virtual WAN ハブとそのゲートウェイではサポートされていません。 IPv4 および IPv6 をサポートする VNet があり、その VNet を Virtual WAN に接続する必要がある場合、このシナリオは現在サポートされていません。

Azure Firewall 経由のインターネット ブレークアウトを使用したポイント対サイトのユーザー VPN シナリオでは、おそらく、仮想 WAN ハブに対してトラフィックを強制するために、クライアント デバイスの IPv6 接続性を無効にする必要があります。 最近のデバイスでは、IPv6 アドレスが既定で使用されるためです。

### <a name="what-is-the-recommended-api-version-to-be-used-by-scripts-automating-various-virtual-wan-functionalities"></a>さまざまな Virtual WAN 機能を自動化するスクリプトで使用される、推奨される API バージョンは何ですか。

最小バージョンとして 05-01-2020 (2020 年 5 月 1 日) が必要です。

### <a name="are-there-any-virtual-wan-limits"></a>Virtual WAN には制限がありますか。

サブスクリプションとサービスの制限に関するページの「[Virtual WAN の制限](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#virtual-wan-limits)」のセクションを参照してください。

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Basic タイプの Virtual WAN と Standard タイプの Virtual WAN の違いは何ですか。

「[Basic および Standard の仮想 WAN](../articles/virtual-wan/virtual-wan-about.md#basicstandard)」を参照してください。 価格については、[価格](https://azure.microsoft.com/pricing/details/virtual-wan/)ページを参照してください。

### <a name="does-virtual-wan-store-customer-data"></a>Virtual WAN に顧客データは格納されますか?

いいえ。 Virtual WAN にお客様のデータは一切格納されません。

### <a name="are-there-any-managed-service-providers-that-can-manage-virtual-wan-for-users-as-a-service"></a>ユーザーに代わって Virtual WAN をサービスとして管理できるマネージド サービス プロバイダーはありますか?

はい。 Azure Marketplace から利用できるマネージド サービス プロバイダー (MSP) ソリューションの一覧については、「[Azure ネットワーク MSP パートナーによる Azure Marketplace のオファー](../articles/networking/networking-partners-msp.md#msp)」を参照してください。

### <a name="how-does-virtual-wan-hub-routing-differ-from-azure-route-server-in-a-vnet"></a>Virtual WAN ハブのルーティングは、VNet の Azure Route Server とどう違うのですか?

Azure Route Server は、Border Gateway Protocol (BGP) ピアリング サービスを提供しており、NVA (Network Virtual Appliance) は、このピアリング サービスを使用して、DIY ハブ VNet のルート サーバーからルートを学習することができます。 VNet 対 VNet トランジット ルーティング、カスタム ルーティング、カスタム ルートの関連付けと伝播、ゼロタッチのフル メッシュ型ハブ サービス、そして ExpressRoute、サイト VPN、リモート ユーザー P2S VPN、大規模 P2S VPN、セキュア ハブ (Azure Firewall) 機能の接続サービスなど、さまざまな機能が Virtual WAN のルーティングには備わっています。 NVA と Azure Route Server との間で BGP ピアリングを確立すると、NVA からの IP アドレスを仮想ネットワークにアドバタイズすることができます。 トランジット ルーティング、カスタム ルーティングなど、先進のあらゆるルーティング機能に、Virtual WAN ルーティングを使用することができます。
