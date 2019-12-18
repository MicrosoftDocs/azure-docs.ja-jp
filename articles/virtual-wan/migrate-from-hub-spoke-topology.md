---
title: Azure Virtual WAN に移行する
description: Azure Virtual WAN に移行する方法について説明します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: c50f2ad0a15e9793fd5a799d44bf34fc2b48e14b
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851266"
---
# <a name="migrate-to-azure-virtual-wan"></a>Azure Virtual WAN に移行する
Azure Virtual WAN を使用すると、企業はグローバルな接続を簡素化し、Microsoft グローバル ネットワークのスケールを活用できます。 このホワイトペーパーでは、お客様が管理する既存のハブアンドスポーク トポロジから、Microsoft が管理する仮想 WAN ハブを活用した設計への移行を希望する企業向けの技術情報について説明します。

「[グローバル トランジット ネットワーク アーキテクチャと Virtual WAN](virtual-wan-global-transit-network-architecture.md)」に関する記事では、クラウド中心の最新のエンタープライズ グローバル ネットワークを採用している企業で Azure Virtual WAN によって実現されたメリットに焦点を当てています。

![ハブアンドスポーク](./media/migrate-from-hub-spoke-topology/figure1.png)
**図 1:Azure Virtual WAN**

Azure 仮想データセンター (VDC) のハブアンドスポーク接続モデルは、Azure ネットワークの既定の推移的なルーティング動作を利用して、シンプルで拡張性の高いクラウド ネットワークを構築するために、数千の顧客によって採用されています。 Azure Virtual WAN はこれらの概念に基づいて構築されており、グローバル接続トポロジを実現する新しい機能を導入します。これにより、オンプレミスの場所と Azure の間だけでなく、お客様が Microsoft ネットワークのスケールを活用して、既存のグローバル ネットワークを拡張することが可能になります。

この記事では、既存のハイブリッド環境を Virtual WAN に移行する方法について説明します。

## <a name="scenario"></a>シナリオ

Contoso は、ヨーロッパとアジアの両方にオフィスを持つグローバルな金融機関です。 同社は既存のアプリケーションをオンプレミスのデータ センターから Azure に移行する予定であり、リージョン別にお客様が管理しているハイブリッド接続用のハブ仮想ネットワークなど、VDC アーキテクチャに基づく基礎設計を構築しています。 クラウドベース テクノロジへの移行の一環として、ネットワーク チームは、今後のビジネスの進展に向けて接続が最適化されていることを確認する任務を負っています。

図 2 は、複数の Azure リージョンへの接続を含む、既存のグローバル ネットワークの概要を示しています。

![Contoso の既存のネットワーク トポロジ](./media/migrate-from-hub-spoke-topology/figure2.png)
**図 2:Contoso の既存のネットワーク トポロジ**

既存のネットワーク トポロジからは、次のことを理解できます。
 
- ハブアンドスポーク トポロジは、共通のプライベート WAN に接続するために、ExpressRoute Premium 回線を含む複数のリージョンで使用されています。
- これらのサイトの中には、Microsoft クラウド内でホストされているアプリケーションに接続するために、VPN トンネルが Azure に直接接続されているものもある。

## <a name="requirements"></a>必要条件
ネットワーク チームは、Contoso のクラウドへの移行をサポート可能なグローバル ネットワーク モデルを提供する任務を負っており、コスト、スケール、およびパフォーマンスの分野で最適化する必要があります。 要約すると、次の要件を満たす必要があります。
- 本社 (HQ) とブランチ オフィスの両方に、クラウドでホストされているアプリケーションへの最適化されたパスを提供する。 
- VPN の終了に伴い既存のオンプレミス データ センター (DC) に依存しないようにし、以下の接続パスを維持したままにする。
    - **ブランチから VNet へ**:VPN に接続されたオフィスは、ローカルの Azure リージョンのクラウドに移行されたアプリケーションにアクセスできる必要があります。
    - **ブランチからハブ、ハブからハブ、ハブから VNet へ**:VPN に接続されたオフィスは、リモートの Azure リージョンのクラウドに移行されたアプリケーションにアクセスできる必要があります。 
    - **ブランチからブランチへ**:リージョンの VPN に接続されたオフィスは、互いに通信でき、ExpressRoute に接続された HQ/DC サイトと通信できる必要があります。 
    - **ブランチからハブ、ハブからハブ、ハブからブランチへ**:グローバルに分離された VPN に接続されたオフィスは、互いに通信でき、ExpressRoute に接続されたあらゆる HQ/DC サイトと通信できる必要があります。
    - **ブランチからインターネットへ**:接続されたサイトはインターネットと通信できる必要があり、このトラフィックはフィルター処理してログに記録する必要があります。
    - **VNet から VNet へ**:同じリージョン内のスポーク仮想ネットワークが相互に通信できる必要があります。
    - **VNet からハブ、ハブからハブ、ハブから VNet へ**:異なるリージョン内のスポーク仮想ネットワークが相互に通信できる必要があります。
- Contoso ローミング ユーザー (ノート PC および電話のユーザー) が企業ネットワーク上にいないときに会社のリソースにアクセスできるようにします。

## <a name="azure-virtual-wan-architecture"></a>Azure Virtual WAN のアーキテクチャ

図 3 は、前のセクションで詳しく説明した要件を満たすために、Azure Virtual WAN を使用して更新されたターゲット トポロジの概要を示しています。

![Contoso の仮想 WAN アーキテクチャ](./media/migrate-from-hub-spoke-topology/figure3.png)
**図 3:Azure Virtual WAN のアーキテクチャ**

要約すると: 
- ヨーロッパの HQ は ExpressRoute に接続されたままで、ヨーロッパのオンプレミスの DC は Azure に完全に移行し、今後は使用停止になります。
- アジアの DC と HQ は、プライベート WAN に接続されたままです。 ローカル通信事業者ネットワークを増強し、グローバル接続を提供するために、Azure Virtual WAN が使用されるようになります 
- 西ヨーロッパと東南アジアの両方の Azure リージョンにデプロイされた Azure Virtual WAN ハブは、ExpressRoute 接続デバイスと VPN 接続デバイスの接続ハブを提供します。 
- ハブはさらに、グローバル メッシュ ネットワークへの OpenVPN 接続を使用する複数のクライアント タイプのローミング ユーザー向けの VPN 終端機能を提供します。これにより、Azure に移行されたアプリケーションだけでなく、オンプレミスに残るすべてのリソースにもアクセスできるようになります。 
- Azure Virtual WAN によって提供される仮想ネットワーク内のリソース向けのインターネット接続。 リモート サイト向けのインターネット接続も Azure Virtual WAN によって提供されます。 Office 365 などの SaaS サービスへのアクセスを最適化するために、パートナー統合によってサポートされるローカル インターネット ブレークアウト。

## <a name="migrate-to-azure-virtual-wan"></a>Azure Virtual WAN に移行する

このセクションでは、Azure Virtual WAN に移行するためのさまざまな手順について説明します。
 
### <a name="vdc-hub-and-spoke-single-region"></a>VDC ハブアンドスポークの単一リージョン

次の図は、Azure Virtual WAN をロールアウトする前の Contoso の単一リージョン トポロジを示しています。

![仮想 WAN ハブをデプロイする](./media/migrate-from-hub-spoke-topology/figure4.png)

 "**図 4:VDC ハブアンドスポークの単一リージョン - 手順 1**

仮想データ センター (VDC) のアプローチに従い、顧客が管理するハブ仮想ネットワークにはいくつかの機能ブロックが含まれています。
- Contoso が例として使用する共有サービス (複数のスポークによって要求される共通の機能) は、IaaS (サービスとしてのインフラストラクチャ) 仮想マシン上の IaaS Windows Server ドメイン コントローラーです。
- サードパーティのネットワーク仮想アプライアンスによって IP/ルーティング ファイアウォール サービスが提供され、スポーク間のレイヤー 3 IP ルーティングを実現します。 
- インターネット イングレス/エグレス サービス。これにはインバウンドの HTTP 要求のための Azure Application Gateway と、インターネット リソースへのフィルターされた発信アクセスのために仮想マシン上で実行されるサードパーティ プロキシ サービスが含まれます。
- オンプレミス ネットワークに接続するための ExpressRoute および VPN 仮想ネットワーク ゲートウェイ。

### <a name="deploy-virtual-wan-hubs"></a>仮想 WAN ハブをデプロイする

最初の手順では、各リージョンに Virtual WAN ハブをデプロイします。 次の記事で説明するように、VPN Gateway と Express Route ゲートウェイを使用して Virtual WAN ハブを設定します。  
- [チュートリアル:Azure Virtual WAN を使用してサイト間接続を作成する](virtual-wan-site-to-site-portal.md)
- [チュートリアル:Azure Virtual WAN を使用して ExpressRoute の関連付けを作成する](virtual-wan-expressroute-portal.md) 

> [!NOTE]
> Azure 仮想 WAN では、この記事で説明されているトラフィック パスの一部を有効にするために Standard SKU を使用する必要があります。


![仮想 WAN ハブをデプロイする](./media/migrate-from-hub-spoke-topology/figure5.png)
**図 5:VDC ハブアンドスポークから Virtual WAN への移行 - 手順 2**

### <a name="connect-remote-sites-expressroute-and-vpn-to-virtual-wan"></a>リモート サイト (ExpressRoute と VPN) を Virtual WAN に接続する

ここで、Virtual WAN ハブを既存の ExpressRoute 回線に接続し、インターネット経由で任意のリモート ブランチにサイト間 VPN をセットアップします。

> [!NOTE]
> 仮想 WAN ハブに接続するには、Express Route 回線を Premium SKU タイプにアップグレードする必要があります。


![リモート サイトを Virtual WAN に接続する](./media/migrate-from-hub-spoke-topology/figure6.png)
**図 6: VDC ハブアンドスポークから Virtual WAN への移行 - 手順 3**

この時点で、オンプレミスのネットワーク機器は、Virtual WAN で管理されているハブ VNet に割り当てられた IP アドレス空間を反映したルートの受信を開始します。 この段階で、VPN 接続されたリモート ブランチは、スポーク仮想ネットワーク内の既存のアプリケーションへの 2 つのパスを認識します。 これらのデバイスは、移行フェーズ中に対称ルーティングを確保するように、引き続き VDC ハブへのトンネルを使用するように構成する必要があります。

### <a name="test-hybrid-connectivity-via-virtual-wan"></a>Virtual WAN を使用してハイブリッド接続をテストする

マネージド仮想 WAN ハブを運用環境への接続に利用する前に、テスト スポークの仮想ネットワークと仮想 WAN VNet 接続を設定することをお勧めします。 次の手順に進む前に、このテスト環境への接続が ExpressRoute とサイト間 VPN 経由で動作することを確認してください。

![Virtual WAN を使用してハイブリッド接続をテストする](./media/migrate-from-hub-spoke-topology/figure7.png)
**図 7: VDC ハブアンドスポークから Virtual WAN への移行 - 手順 4**

### <a name="transition-connectivity-to-virtual-wan-hub"></a>仮想 WAN ハブへの接続を移行する


![Virtual WAN ハブへの接続を移行する](./media/migrate-from-hub-spoke-topology/figure8.png)
**図 8: VDC ハブアンドスポークから Virtual WAN への移行 - 手順 5**

**a**。 スポーク仮想ネットワークから古い VDC ハブへの既存のピアリング接続を削除します。 スポーク仮想ネットワーク内のアプリケーションへのアクセスは、手順 a ~ c が完了するまで使用できません。

**b**. VNet 接続を使用して、スポーク仮想ネットワークを Virtual WAN ハブに接続します。

**c**. スポーク仮想ネットワーク内で以前に使用されていたスポーク間通信用のすべてのユーザー定義ルート (UDR) を削除します。 このパスは、Virtual WAN ハブ内で使用可能な動的ルーティングによって有効になりました。

**d**. VDC ハブの既存の ExpressRoute および VPN ゲートウェイは、次の手順 (e) を可能にするために使用停止になりました。

**e**. 新しい VNet 接続を使用して、古い VDC ハブ (ハブ仮想ネットワーク) を Virtual WAN ハブに接続します。

### <a name="old-hub-becomes-shared-services-spoke"></a>古いハブが共有サービスのスポークになる

Azure ネットワークを再設計し、Virtual WAN ハブが新しいトポロジの中心点となるようにしました。

![古いハブが共有サービスのスポークになる](./media/migrate-from-hub-spoke-topology/figure9.png)
**図 9:VDC ハブアンドスポークから Virtual WAN への移行 - 手順 6**

Virtual WAN ハブはマネージド エンティティで、仮想マシンなどのカスタム リソースのデプロイが許可されていないため、共有サービス ブロックは、スポーク仮想ネットワークとして存在するようになり、Azure Application Gateway やネットワーク仮想化アプライアンスを経由したインターネット イングレスなどの機能をホストします。 共有サービス環境とバックエンド仮想マシン間のトラフィックは、Virtual WAN で管理されるハブを経由するようになりました。

### <a name="optimize-on-premises-connectivity-to-fully-utilize-virtual-wan"></a>オンプレミスの接続を最適化して Virtual WAN を完全に活用する

この段階では、Contoso は Microsoft Cloud へのビジネス アプリケーションの移行をほぼ完了し、オンプレミスの DC 内にいくつかのレガシ アプリケーションだけが残っています。

![オンプレミスの接続を最適化して Virtual WAN を完全に活用する](./media/migrate-from-hub-spoke-topology/figure10.png)
**図 10: VDC ハブアンドスポークから Virtual WAN への移行 - 手順 7**

 Contoso は、Azure Virtual WAN の機能を十分に活用するために、レガシのオンプレミス VPN 接続の使用を停止することを決定します。 HQ ネットワークまたは DC ネットワークにアクセスし続けるすべてのブランチは、Azure Virtual WAN の組み込みのトランジット ルーティングを使用して Microsoft グローバル ネットワークを経由することができます。 

> [!NOTE]
> ExpressRoute Global Reach は、Microsoft のバックボーンを利用してお客様の既存のプライベート WAN を補完することを希望するお客様が選択できる別の方法です。

## <a name="end-state-architecture-and-traffic-paths"></a>終了状態のアーキテクチャおよびトラフィック パス


![終了状態のアーキテクチャおよびトラフィック パス](./media/migrate-from-hub-spoke-topology/figure11.png)
**図 11:2 つのリージョンの Virtual WAN**

このセクションでは、トラフィック フローの例をいくつか見ることで、このトポロジが元の要件をどのように満たすかを要約して説明します。

### <a name="path-1"></a>パス 1

パス 1 では、アジアのサイト間 VPN 接続ブランチから、東南アジア リージョンの Azure VNet へのトラフィック フローについて説明します。

トラフィックは次のようにルーティングされます。
- アジア ブランチは、回復性のある S2S BGP 対応トンネルを介して、東南アジアの仮想 WAN ハブに接続されます。
- アジアの仮想 WAN ハブは、接続された VNet に対してトラフィックをローカルにルーティングします。

![フロー 1](./media/migrate-from-hub-spoke-topology/flow1.png)

### <a name="path-2"></a>パス 2
パス 2 では、ExpressRoute で接続されたヨーロッパ HQ から、東南アジア リージョンの Azure VNet へのトラフィック フローについて説明します。

トラフィックは次のようにルーティングされます。
- ヨーロッパの HQ は、Premium ExpressRoute 回線経由で西ヨーロッパの Virtual WAN ハブに接続されます。
- Virtual WAN のハブからハブへのグローバル接続により、リモート リージョンで接続されている VNet へのトラフィックの転送が可能になります。

![フロー 2](./media/migrate-from-hub-spoke-topology/flow2.png)

### <a name="path-3"></a>パス 3
パス 3 では、プライベート WAN に接続されたアジアのオンプレミス DC からヨーロッパのサイト間接続されたブランチへのトラフィック フローについて説明します。

トラフィックは次のようにルーティングされます。
- アジア DC はローカル プライベート WAN の通信事業者に接続されています。
- プライベート WAN でローカルに終了する ExpressRoute 回線は、東南アジアの Virtual WAN ハブに接続します。
- Virtual WAN のハブからハブへのグローバル接続により、トラフィックの転送が可能になります。

![フロー 3](./media/migrate-from-hub-spoke-topology/flow3.png)


### <a name="path-4"></a>パス 4
パス 4 では、東南アジア リージョンの Azure VNet から、西ヨーロッパ リージョンの Azure VNet へのトラフィック フローについて説明します。

トラフィックは次のようにルーティングされます。
- Virtual WAN のハブからハブへのグローバル接続により、ユーザーが追加構成することなく、接続されているすべての Azure VNet のネイティブ転送が可能になります。

![フロー 4](./media/migrate-from-hub-spoke-topology/flow4.png)

### <a name="path-5"></a>パス 5
パス 5 では、ローミング VPN (P2S) ユーザーから西ヨーロッパ リージョンの Azure VNet へのトラフィック フローについて説明します。

トラフィックは次のようにルーティングされます。
- ノート PC およびモバイル デバイスのユーザーは、OpenVPN クライアントを使用して、西ヨーロッパの P2S VPN ゲートウェイに透過的に接続します。
- 西ヨーロッパの仮想 WAN ハブは、接続された VNet に対してトラフィックをローカルにルーティングします。

![フロー 5](./media/migrate-from-hub-spoke-topology/flow5.png)

## <a name="security-and-policy-control-via-azure-firewall"></a>Azure Firewall によるセキュリティとポリシーの制御

Contoso は、このドキュメントで既に説明した要件に従って、すべてのブランチと VNet の間の接続を検証しました。 セキュリティ制御とネットワーク分離の要件を満たすためには、ハブ ネットワーク経由で引き続きトラフィックを分離し、ログに記録する必要があります。この機能は以前はネットワーク仮想アプライアンス (NVA) によって実行されていました。 また、Contoso では、既存のプロキシ サービスを使用停止にし、発信インターネット フィルター処理のためにネイティブ Azure サービスを使用したいと考えています。 

![Azure Firewall によるセキュリティとポリシーの制御](./media/migrate-from-hub-spoke-topology/figure12.png)
**図 12:仮想 WAN での Azure Firewall (セキュリティ保護付き仮想ハブ)**

次の大まかな手順では、Azure Firewall を仮想 WAN ハブに導入し、ポリシー制御の統合ポイントを有効にする必要があります。 このプロセスと、セキュリティ保護付き仮想ハブの概念については、[こちら](https://go.microsoft.com/fwlink/?linkid=2107683)に詳しく説明されています。
- Azure Firewall ポリシーを作成する。
- ファイアウォール ポリシーを Azure Virtual WAN ハブとリンクする。
- 上記の手順では、既存の Virtual WAN ハブをセキュリティ保護付き仮想ハブとして機能させることができ、必要な Azure Firewall リソースがデプロイされます。

> [!NOTE]
> Azure Firewall が標準の仮想 WAN ハブにデプロイされている場合 (SKU:Standard):V2V、B2V、V2I、B2I の FW ポリシーは、Azure FW がデプロイされている特定のハブ (セキュリティ保護付きハブ) に接続されている Vnet およびブランチから送信されたトラフィックにのみ適用されます。 同じ仮想 WAN 内の他の仮想 WAN ハブに接続されているリモートの Vnet およびブランチから発信されるトラフィックは、これらのリモート ブランチおよび Vnet が仮想 WAN のハブ間リンクを介して相互接続されていたとしても、ファイアウォールの対象になりません。 ハブ間のファイアウォールのサポートは、Azure Virtual WAN と Firewall Manager のロードマップにあります。

次のパスは、Azure のセキュリティ保護付き仮想ハブを利用して有効になっている接続パスを示しています。

### <a name="path-6"></a>パス 6
パス 6 では、同じリージョン内での VNet 間のセキュリティで保護されたトラフィック フローについて説明します。

トラフィックは次のようにルーティングされます。
- 同じセキュリティ保護付き仮想ハブに接続されている仮想ネットワークは、Azure Firewall 経由でトラフィックをルーティングするようになりました。
- Azure Firewall はポリシーをこれらのフローに適用できます。

![フロー 6](./media/migrate-from-hub-spoke-topology/flow6.png)

### <a name="path-7"></a>パス 7
パス 7 では、Azure VNet からインターネットまたはサードパーティのセキュリティ サービスへのトラフィック フローについて説明します。

トラフィックは次のようにルーティングされます。
- セキュリティで保護された仮想ハブに接続されている仮想ネットワークは、インターネット アクセスの中央点としてセキュリティで保護されたハブを使用して、インターネット上のパブリックな宛先にトラフィックを送信できます。
- このトラフィックは、Azure Firewall FQDN 規則を使用してローカルでフィルター処理するか、または検査のためにサードパーティのセキュリティ サービスに送信することができます。

![フロー 7](./media/migrate-from-hub-spoke-topology/flow7.png)

### <a name="path-8"></a>パス 8
パス 8 では、ブランチとインターネットの間、またはサードパーティのセキュリティ サービスからのトラフィック フローについて説明します。

トラフィックは次のようにルーティングされます。
- セキュリティで保護された仮想ハブに接続されているブランチは、インターネット アクセスの中央点としてセキュリティで保護されたハブを使用して、インターネット上のパブリックな宛先にトラフィックを送信できます。
- このトラフィックは、Azure Firewall FQDN 規則を使用してローカルでフィルター処理するか、または検査のためにサードパーティのセキュリティ サービスに送信することができます。

![フロー 8](./media/migrate-from-hub-spoke-topology/flow8.png) 

## <a name="next-steps"></a>次の手順
[Azure Virtual WAN](virtual-wan-about.md) の詳細を理解する
