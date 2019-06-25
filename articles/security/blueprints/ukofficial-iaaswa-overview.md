---
title: 'Azure のセキュリティとコンプライアンスのブループリント: UK OFFICIAL のための 3 層 IaaS Web アプリケーション'
description: 'Azure のセキュリティとコンプライアンスのブループリント: UK OFFICIAL のための 3 層 IaaS Web アプリケーション'
services: security
author: jomolesk
ms.assetid: 9c32e836-0564-4906-9e15-f070d2707e63
ms.service: security
ms.topic: article
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 13ea2b68027c81bca7b43cef62cf7039aa0ea8dd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60609489"
---
# <a name="azure-security-and-compliance-blueprint---three-tier-iaas-web-application-for-uk-official"></a>Azure のセキュリティとコンプライアンスのブループリント: UK OFFICIAL のための 3 層 IaaS Web アプリケーション

## <a name="overview"></a>概要

 この記事では、イギリスで OFFICIAL として分類される多くのワークロードの処理に適したアーキテクチャに基づく Microsoft Azure 3 層 Web を提供するためのガイダンスと自動化スクリプトを提供します。

 コードとしてのインフラストラクチャ アプローチを使用して、一連の [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) テンプレートは、英国立サーバー セキュリティ センター (NCSC) の 14 の[クラウド セキュリティに関する原則](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles)と Center for Internet Security (CIS) の[重要なセキュリティ コントロール](https://www.cisecurity.org/critical-controls.cfm)に準拠する環境をデプロイします。

 NCSC は、顧客がそのクラウド セキュリティに関する原則を使用してサービスのセキュリティ プロパティを評価し、顧客とサプライヤー間での責任分担の理解に役立てるよう推奨しています。 Microsoft では、責任の分担を理解できるように、これらの各原則に関する情報を提供しています。

 このアーキテクチャおよび対応する Azure Resource Manager テンプレートは、Microsoft ホワイト ペーパー「[14 Cloud Security Controls for UK cloud Using Microsoft Azure (Microsoft Azure を使用した英国クラウド向けの 14 のクラウド セキュリティ コントロール)」](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1)でサポートされています。 このホワイトペーパーは、Azure サービスが英国 NCSC の 14 のクラウド セキュリティに関する原則に準拠する方法をカタログ化しているので、組織はグローバルに、また英国で Microsoft Azure クラウドのクラウドベース サービスを使用してコンプライアンス義務を果たす能力を迅速に実現できます。

 このテンプレートは、ワークロードのインフラストラクチャをデプロイします。 アプリケーション コードとビジネス層およびデータ層のサポート ソフトウェアをインストールし、構成する必要があります。 詳しいデプロイ手順については、[こちら](https://aka.ms/ukwebappblueprintrepo)をご覧ください。

 Azure サブスクリプションをお持ちでない場合は、「[Azure を使ってみる](https://azure.microsoft.com/get-started/)」ですばやく簡単にサインアップできます。

## <a name="architecture-diagram-and-components"></a>アーキテクチャ ダイアグラムとコンポーネント

 Azure テンプレートは、Azure クラウド環境で UK-OFFICIAL ワークロードをサポートする 3 層 Web アプリケーション アーキテクチャを提供します。 このアーキテクチャは、オンプレミスのネットワークを Azure に拡張するセキュリティ保護されたハイブリッド環境を提供するので、企業ユーザーまたはインターネットから Web ベース ワークロードに安全にアクセスできます。

![UK OFFICIAL のための 3 層 IaaS Web アプリケーションの参照アーキテクチャ図](images/ukofficial-iaaswa-architecture.png?raw=true "UK OFFICIAL のための 3 層 IaaS Web アプリケーションの参照アーキテクチャ図")

 このソリューションでは、次の Azure サービスを使用します。 デプロイ アーキテクチャについて詳しくは、「[デプロイ アーキテクチャ](#deployment-architecture)」のセクションをご覧ください。

(1) /16 仮想ネットワーク - 運用 VNet
- (3) /24 サブネット - 3 層 (Web、Biz、データ)
- (1) /27 サブネット - ADDS
- (1) /27 サブネット - ゲートウェイ サブネット
- (1) /29 サブネット - Application Gateway サブネット
- 既定の (Azure で提供される) DNS を使用
- 管理 VNet へのピアリングが有効
- トラフィック フローを管理するためのネットワーク セキュリティ グループ (NSG)

(1) /24 仮想ネットワーク - 管理 VNet
- (1) /27 サブネット
- (2) ADDS DNS と (1) Azure DNS のエントリを使用
- 運用 VNet へのピアリングが有効
- トラフィック フローを管理するためのネットワーク セキュリティ グループ (NSG)

(1) Application Gateway
- WAF - 有効
- WAF モード - 防止
- ルール セット:OWASP 3.0
- ポート 80 の HTTP リスナー
- NSG を通じて規制される接続性/トラフィック
- パブリック IP アドレス エンドポイントが定義済み (Azure)

(1) VPN - ルートベースのサイト間 IPSec VPN トンネル
- パブリック IP アドレス エンドポイントが定義済み (Azure)
- NSG を通じて規制される接続性/トラフィック
- (1) ローカル ネットワーク ゲートウェイ (オンプレミスのエンドポイント)
- (1) Azure ネットワーク ゲートウェイ (Azure エンドポイント)

(9) 仮想マシン - すべての VM が Azure IaaS マルウェア対策 DSC 設定でデプロイ

- (2) Active Directory Domain Services ドメイン コントローラー (Windows Server 2012 R2)
  - (2) DNS サーバー ロール - VM あたり 1 つ
  - (2) 運用 VNet に接続されている NIC - VM あたり 1 つ
  - どちらもテンプレートで定義されているドメインに参加
    - デプロイの一部として作成されるドメイン


- (1) ジャンプボックス (踏み台ホスト) 管理 VM
  - パブリック IP アドレスを持つ管理 VNet に 1 枚の NIC
    - 特定のソースへのトラフィック (入力/出力) の制限に NSG を使用
  - ドメイン不参加


- (2) Web 層の VM
  - (2) IIS サーバー ロール - VM あたり 1 つ
  - (2) 運用 VNet に接続されている NIC - VM あたり 1 つ
  - ドメイン不参加


- (2) Biz 層の VM
  - (2) 運用 VNet に接続されている NIC - VM あたり 1 つ
  - ドメイン不参加


- (2) データ層の VM
  - (2) 運用 VNet に接続されている NIC - VM あたり 1 つ
  - ドメイン不参加

可用性セット
- (1) Active Directory ドメイン コントローラー VM セット - 2 VM
- (1) Web 層の VM セット - 2 VM
- (1) Biz 層の VM セット - 2 VM
- (1) データ層の VM セット - 2 VM

Load Balancer
- (1) Web 層のロード バランサー
- (1) Biz 層のロード バランサー
- (1) データ層のロード バランサー

Storage
- (14) 合計ストレージ アカウント
  - Active Directory ドメイン コントローラー可用性セット
    - (2) プライマリ ローカル冗長ストレージ (LRS) アカウント - VM ごとに 1 つ  
    - (1) ADDS 可用性セット用の診断ローカル冗長ストレージ (LRS) アカウント
  - 管理ジャンプボックス VM
    - (1) ジャンプボックス VM 用のプライマリ ローカル冗長ストレージ (LRS) アカウント
    - (1) ジャンプボックス VM 用の診断ローカル冗長ストレージ (LRS) アカウント
  - Web 層の VM
    - (2) プライマリ ローカル冗長ストレージ (LRS) アカウント - VM ごとに 1 つ  
    - (1) Web 層可用性セット用の診断ローカル冗長ストレージ (LRS) アカウント
  - Biz 層の VM
    - (2) プライマリ ローカル冗長ストレージ (LRS) アカウント - VM ごとに 1 つ  
    - (1) Biz 層可用性セット用の診断ローカル冗長ストレージ (LRS) アカウント
  - データ層の VM
    - (2) プライマリ ローカル冗長ストレージ (LRS) アカウント - VM ごとに 1 つ  
    - (1) データ層可用性セット用の診断ローカル冗長ストレージ (LRS) アカウント

### <a name="deployment-architecture"></a>デプロイ アーキテクチャ:

**オンプレミスのネットワーク**: 組織内に実装されているプライベートなローカル エリア ネットワーク。

**運用 VNet**: 運用 [VNet](https://docs.microsoft.com/azure/Virtual-Network/virtual-networks-overview) (仮想ネットワーク) は、Azure で実行されているアプリケーションとその他の運用リソースをホストします。 各 VNet には、ネットワーク トラフィックの分離と管理に使用される複数のサブネットが含まれることがあります。

**Web 層**: 受信 HTTP 要求を処理します。 この層を通じて応答が返されます。

**ビジネス層**: システムのビジネス プロセスとその他の機能ロジックを実装します。

**データベース層**: 高可用性を実現するために [SQL Server Always On 可用性グループ](https://msdn.microsoft.com/library/hh510230.aspx)を使用して永続的なデータ ストレージを提供します。 顧客は [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) を PaaS の代替として使用できます。

**ゲートウェイ**: [VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) は、オンプレミスのネットワーク内のルーターと運用 VNet 間の接続性を提供します。

**インターネット ゲートウェイとパブリック IP アドレス**: インターネット ゲートウェイは、インターネットを通じてユーザーにアプリケーション サービスを公開します。 これらのサービスにアクセスするトラフィックは、レイヤー 7 のルーティングと負荷分散の機能を Web アプリケーション ファイアウォール (WAF) 保護と共に提供する [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) を使用して保護されます。

**管理 VNet**: この [VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) には、運用 VNet で実行されているワークロードの管理および監視機能を実装するリソースが含まれます。

**ジャンプボックス**: [踏み台ホスト](https://en.wikipedia.org/wiki/Bastion_host)とも呼ばれ、運用 VNet で VM に接続するために管理者が使用する、ネットワーク上の安全な VM です。 jumpbox の NSG は、セーフ リストにあるパブリック IP アドレスからのリモート トラフィックのみを許可します。 リモート デスクトップ (RDP) トラフィックを許可するには、トラフィックのソースが NSG で定義されている必要があります。 運用リソースの管理は、セキュリティで保護されたジャンプボックス VM を使用して RDP を介して行われます。

**ユーザー定義ルート**: [ユーザー定義ルート](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)は、Azure VNet 内の IP トラフィックのフローを定義するために使用されます。

**ネットワーク ピアリング VNet** : 運用 VNet と管理 VNet は [VNet ピアリング](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)を使用して接続されます。
これらの VNet は別々のリソースとして管理されますが、これらの仮想マシンのすべての接続性の目的では 1 つとして表示されます。 これらのネットワークは、プライベート IP アドレスを使用して相互に直接通信します。 VNet ピアリングは、同じ Azure リージョン内の VNet が対象です。

**ネットワーク セキュリティ グループ**: [NSG](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) には、VNet 内のトラフィックを許可または拒否するアクセス制御リストが含まれます。 NSG を使用して、サブネットまたは個々の VM レベルでトラフィックを保護できます。

**Active Directory Domain Services (AD DS)** : このアーキテクチャでは、専用 [Active Directory Domain Services](https://technet.microsoft.com/library/hh831484.aspx) デプロイが提供されます。

**ログ記録と監査**: [Azure アクティビティ ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)には、操作を開始したユーザー、操作の発生時期、操作の状態、操作の調査に役立つ可能性のあるその他のプロパティの値など、サブスクリプション内のリソースに対して行われた操作がキャプチャされます。 Azure アクティビティ ログは、サブスクリプションのすべてのアクションをキャプチャする Azure プラットフォーム サービスです。 必要に応じてログをアーカイブまたはエクスポートできます。

**ネットワーク監視およびアラート**: [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) は、VNet 内のネットワーク トラフィックに対するネットワーク パケット キャプチャ、フロー ログ記録、トポロジ ツール、および診断を提供するプラットフォーム サービスです。

## <a name="guidance-and-recommendations"></a>ガイダンスと推奨事項

### <a name="business-continuity"></a>ビジネス継続性

**高可用性**: サーバー ワークロードは、Azure の仮想マシンの高可用性を確保するために[可用性セット](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)にグループ化されます。 このような構成により、計画済み、または計画外メンテナンス イベント中に、少なくとも 1 つの仮想マシンが利用可能となり、99.95% の Azure SLA を満たします。

### <a name="logging-and-audit"></a>ログ記録と監査

**監視**:[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) は、すべての Azure リソースのアクティビティ ログ、メトリック、および診断ログを監視するための単一ソースを提供するプラットフォーム サービスです。 Azure Monitor は、Azure のリソースのメトリックとログを視覚化、クエリ、ルーティング、アーカイブし、そのメトリックとログに対してアクションを実行するように構成できます。 リソース ベースのアクセス制御を使用して監査証跡をセキュリティで保護し、ユーザーがログを変更する権限を持たないようにすることをお勧めします。

**アクティビティ ログ**: [Azure アクティビティ ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)を構成して、サブスクリプションのリソースに対して実行された操作に関する分析情報が得られるようにします。

**診断ログ**: [診断ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)は、リソースによって出力されるすべてのログです。 これらのログには、Windows イベント システム ログ、BLOB、テーブル、キューのログが含まれることがあります。

**ファイアウォール ログ**: Application Gateway は、完全な診断ログとアクセス ログを提供します。 WAF が有効になっているアプリケーション ゲートウェイに対してファイアウォールのログを使用できます。

**ログ アーカイブ**: ログ データ ストレージは、アーカイブのために定義済みのリテンション期間にわたって、一元管理された Azure ストレージ アカウントに書き込むように構成できます。 Azure Monitor のログを使用するかサード パーティ製 SIEM システムを使用してログを処理することができます。

### <a name="identity"></a>ID

**Active Directory Domain Services**: このアーキテクチャでは、Azure に Active Directory Domain Services デプロイが提供されます。 Azure に Active Directory を実装する場合の具体的な推奨事項については、以下の記事をご覧ください。

[Active Directory Domain Services (AD DS) を Azure に拡張する](https://docs.microsoft.com/azure/guidance/guidance-identity-adds-extend-domain)。

[Azure 仮想マシンでの Windows Server Active Directory のデプロイ ガイドライン](https://msdn.microsoft.com/library/azure/jj156090.aspx)。

**Active Directory 統合**: 専用 AD DS のアーキテクチャの代わりに、お客様は [Azure Active Directory](https://docs.microsoft.com/azure/guidance/guidance-ra-identity) 統合または[オンプレミスのフォレストに参加している Azure 内の Active Directory](https://docs.microsoft.com/azure/guidance/guidance-ra-identity) を使用できます。

### <a name="security"></a>セキュリティ

**管理セキュリティ**: このブループリントにより、管理者は信頼されたソースからの RDP を使用して管理 VNet およびジャンプボックスに接続できます。 管理 VNetのネットワーク トラフィックは、NSG を使用して制御されます。 ポート 3389 へのアクセスは、ジャンプボックスを含むサブネットにアクセスできる信頼される IP 範囲からのトラフィックに制限されます。

お客様は、管理 VNet とジャンプボックスに接続するときに、[強化されたセキュリティ管理モデル](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/securing-privileged-access)を使用して環境をセキュリティで保護することも検討できます。 セキュリティを強化するために、[特権アクセス ワークステーション](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations#what-is-a-privileged-access-workstation-paw)と RDGateway 構成を使用することをお勧めします。 ネットワーク仮想アプライアンスとパブリック/プライベート DMZ を使用すると、セキュリティがさらに強化されます。

**ネットワークのセキュリティ保護**: [ネットワーク セキュリティ グループ](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) は、正しく構成されていないか無効になっているゲートウェイをバイパスするインバウンド トラフィックに対して第 2 レベルの保護を提供するために、各サブネットに対してお勧めします。 例 - [NSG をデプロイするための Resource Manager テンプレート](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/networkSecurityGroups)。

**パブリック エンドポイントのセキュリティ保護**: インターネット ゲートウェイは、インターネットを通じてユーザーにアプリケーション サービスを公開します。 これらのサービスにアクセスするトラフィックは、Web アプリケーション ファイアウォールと HTTPS プロトコル管理を提供する [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) を使用して保護されます。

**IP 範囲**: アーキテクチャの IP 範囲は推奨範囲です。 お客様自身の環境を検討し、適切な範囲を使用することをお勧めします。

**ハイブリッド接続**: クラウド ベースのワークロードは、Azure VPN Gateway を使用して、IPSEC VPN を介してオンプレミスのデータセンターに接続されます。 お客様は、適切な VPN Gateway を使用して Azure に接続する必要があります。 例 - [VPN Gateway Resource Manager テンプレート](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/vpn-gateway-vpn-connection)。 ビッグ データが必要な大規模でミッション クリティカルなワークロードを実行しているお客様は、Microsoft クラウド サービスへのプライベート ネットワーク接続のために [ExpressRoute](https://docs.microsoft.com/azure/guidance/guidance-hybrid-network-expressroute) を使用したハイブリッド ネットワーク アーキテクチャを検討できます。

**懸念事項の分離**: この参照アーキテクチャは、VNet を管理操作用とビジネス操作用に分離します。 分離された VNet とサブネットにより、「[Microsoft クラウド サービスとネットワーク セキュリティ](https://docs.microsoft.com/azure/best-practices-network-security)」のベスト プラクティスに従ってネットワーク セグメント間に NSG を使用して、トラフィックの受信と送信の制限などのトラフィック管理を実行できます。

**リソース管理**: VM、VNet、ロード バランサーなどの Azure リソースは、[Azure リソース グループ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)にグループ化することで管理されます。 リソース ベースのアクセス制御ロールを各リソース グループに割り当てて、許可されているユーザーのみにアクセスを制限することができます。

**アクセス制御の制限**: [ロールベースのアクセス制御](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) (RBAC) を使用して、アプリケーション内のリソースを管理します。[カスタム ロール](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)を使用すると、RBAC では DevOps が各階層に対して実行できる操作を制限できます。 権限を付与する場合は、[最小限の特権の原則](https://msdn.microsoft.com/library/hdb58b2f(v=vs.110).aspx#Anchor_1)を使用します。 構成の変更がすべて計画されたものであることを確認するため、すべての管理操作をログに記録し、定期的な監査を実行します。

**インターネット アクセス**: この参照アーキテクチャでは、[Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) をインターネット接続ゲートウェイおよびロード バランサーとして利用します。 一部のお客様は、[Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) の代わりにサード パーティ製のネットワーク仮想アプライアンスを使用してネットワーク セキュリティのレイヤーを追加することも検討できます。

**Azure Security Center**:[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) では、サブスクリプション内のリソースのセキュリティの状態を一元的に表示することができ、リソースの侵害を防ぐうえで役立つ推奨事項が提供されます。 よりきめ細かいポリシーを有効にするためにも使用できます。 たとえば、特定のリソース グループにポリシーを適用できるので、企業はリスクへの対策を調整できます。 Azure サブスクリプションで Azure Security Center を有効にすることをお勧めします。

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>NCSC クラウド セキュリティに関する原則のコンプライアンス ドキュメント

Crown Commercial Service (政府による商業および調達アクティビティの向上に努める機関) は、Microsoft の対象となるエンタープライズ クラウド サービスの分類を OFFICIAL レベルのすべてのサービスをカバーする G-Cloud v6 に更新しました。 Azure と G-Cloud について詳しくは、[Azure UK G-Cloud セキュリティ評価概要](https://www.microsoft.com/en-us/trustcenter/compliance/uk-g-cloud)に関するページをご覧ください。

このブループリントは、NCSC [クラウド セキュリティに関する原則](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles)で説明されている 14 のクラウド セキュリティに関する原則に準拠し、UK-OFFICIAL として分類されたワークロードをサポートする環境の実現を支援します。

[お客様の責任マトリックス](https://aka.ms/ukofficial-crm) (Excel ブック) には 14 のクラウド セキュリティに関する原則がすべて示されており、マトリックスは、各原則 (または原則のサブパート) について、原則の実装の責任を追うのが Microsoft、お客様、または双方の共同責任のいずれかであるかを示します。

[原則実装マトリックス](https://aka.ms/ukofficial-iaaswa-pim) (Excel ブック) には、14 のクラウド セキュリティに関する原則がすべて示されており、マトリックスは、お客様の責任マトリックスでお客様の責任と指定された各原則 (または原則のサブパート) について、1) Blueprint Automation が原則を実行しているかどうか、および 2) 実装が原則要件に準拠する方法の説明を示します。

さらに Cloud Security Alliance (CSA) は、クラウド プロバイダーの評価でお客様をサポートし、クラウド サービスへの移行前に回答する必要がある質問を識別するために、Cloud Control Matrix を発行しました。 これに対し、Microsoft Azure は CSA Consensus Assessment Initiative Questionnaire ([CSA CAIQ](https://www.microsoft.com/en-us/TrustCenter/Compliance/CSA)) に回答し、Microsoft が提示された原則に対処する方法を説明しました。

## <a name="deploy-the-solution"></a>ソリューションのデプロイ

デプロイ ユーザーがこの Blueprint Automation のデプロイに使用できる方法は 2 つあります。 最初の方法は PowerShell スクリプトを使用し、2 番目の方法は Azure Portal を使用して参照アーキテクチャをデプロイします。 詳しいデプロイ手順については、[こちら](https://aka.ms/ukofficial-iaaswa-repo)をご覧ください。

## <a name="disclaimer"></a>免責事項

 - このドキュメントは、参考目的でのみ使用してください。 マイクロソフトは、本文書の情報に対して、明示的、黙示的、法的であるかを問わず、一切の保証を行わないものとします。 このドキュメントは "現状のまま" 提供されます。 このドキュメントに記載された情報と見解は、URL やその他のインターネット Web サイトの参照も含め、予告なく変更する可能性があります。 このドキュメントをお読みになったお客様は、自己責任でドキュメントをご利用ください。
 - このドキュメントは、Microsoft 製品に含まれる知的財産に対するいかなる法的権利もお客様に提供するものではありません。
 - お客様の社内での参照目的に限り、このドキュメントを複製し使用することができます。
 - このドキュメントに記載されている特定の推奨事項に従った結果、Azure でのデータ、ネットワーク、コンピューティング リソースの使用量が増加したり、ライセンス コストやサブスクリプション コストが増加したりする場合があります。
 - このアーキテクチャは、特定の要件に合わせて調整するための基礎として使用されることを目的としており、そのまま運用環境に適用しないでください。
 - このドキュメントは、参照資料として作成されており、お客様が特定のコンプライアンス要件や規制に適合するためのすべての手段を定義するために使用されるべきではありません。 お客様は、承認された顧客実装について、組織の法務課からのアドバイスを受けてください。
