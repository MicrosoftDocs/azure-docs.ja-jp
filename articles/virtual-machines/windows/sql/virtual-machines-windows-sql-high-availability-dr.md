---
title: SQL Server の高可用性とディザスター リカバリー | Microsoft Docs
description: Azure Virtual Machines で実行されている SQL Server に対するさまざまな種類の HADR 戦略についての説明。
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2017
ms.author: mikeray
ms.openlocfilehash: 5f5fc4ecc0949f2f224c1d6a05742900a751ef45
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756246"
---
# <a name="high-availability-and-disaster-recovery-for-sql-server-in-azure-virtual-machines"></a>Azure 仮想マシンにおける SQL Server の高可用性とディザスター リカバリー

Microsoft Azure 仮想マシン (VM) と SQL Server を使用すると、高可用性ディザスター リカバリー (HADR) データベース ソリューションのコストを削減できます。 ほとんどの SQL Server HADR ソリューションでは、Azure のみのソリューションとしても、ハイブリッドのソリューションとしても、Azure 仮想マシンがサポートされています。 Azure のみのソリューションでは、HADR システム全体が Azure で実行されます。 ハイブリッド構成では、ソリューションの一部が Azure で実行され、その他の部分が組織内のオンプレミスで実行されます。 Azure 環境は柔軟性が高いので、SQL Server データベース システムの予算や HADR 要件に応じて、部分的に、または完全に Azure に移動できます。

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="understanding-the-need-for-an-hadr-solution"></a>HADR ソリューションの必要性
サービス レベル アグリーメント (SLA) が必要とする HADR 機能をデータベース システムが備えていることを確認するのは、管理者の責任です。 Azure には、クラウド サービスのサービス復旧、Virtual Machines のディザスター リカバリー検出などの高可用性メカニズムが用意されていますが、それだけで必要な SLA が満たされていることが保証されるわけではありません。 これらのメカニズムは VM の高可用性を保護しますが、VM 内で実行される SQL Server の高可用性は保護されません。 VM がオンラインで正常であっても、SQL Server インスタンスで障害が発生する可能性があります。 さらに、Azure の高可用性メカニズムがあっても、ソフトウェアまたはハードウェアの障害からの回復やオペレーティング システムのアップグレードなどのイベントのために、VM のダウンタイムが生じます。

また、Azure の geo 冗長ストレージ (GRS) は、geo レプリケーションと呼ばれる機能によって実装されており、データベースにとって適切なディザスター リカバリー ソリューションにはならない場合があります。 geo レプリケーションでは、データを非同期的に送信するため、障害が発生すると、最新の更新が失われる場合があります。 geo レプリケーションの制限事項の詳細については、「 [別々のディスクのデータとログ ファイルでサポートされていない geo レプリケーション](#geo-replication-support) 」セクションを参照してください。

## <a name="hadr-deployment-architectures"></a>HADR デプロイ アーキテクチャ
Azure でサポートされている SQL Server HADR テクノロジは、次のとおりです。

* [AlwaysOn 可用性グループ](https://technet.microsoft.com/library/hh510230.aspx)
* [AlwaysOn フェールオーバー クラスター インスタンス](https://technet.microsoft.com/library/ms189134.aspx)
* [ログ配布](https://technet.microsoft.com/library/ms187103.aspx)
* [Azure Blob Storage サービスを使用した SQL Server のバックアップと復元](https://msdn.microsoft.com/library/jj919148.aspx)
* [データベース ミラーリング](https://technet.microsoft.com/library/ms189852.aspx) - SQL Server 2016 では非推奨

高可用性とディザスター リカバリーの両方の機能を持つ SQL Server ソリューションを実装するために、テクノロジを組み合わせることができます。 使用するテクノロジによっては、ハイブリッド デプロイで VPN トンネルと Azure Virtual Network が必要になる場合があります。 以下の各セクションで、デプロイ アーキテクチャのいくつかの例を示します。

## <a name="azure-only-high-availability-solutions"></a>Azure のみ:高可用性ソリューション

SQL Server の高可用性ソリューションは、AlwaysOn 可用性グループ (可用性グループと呼ばれる) を使用してデータベース レベルにするができます。 また、AlwaysOn フェールオーバー クラスター インスタンス (フェールオーバー クラスター インスタンス) を使用して、インスタンス レベルで高可用性ソリューションを作成することもできます。 冗長性を追加するには、フェールオーバー クラスター インスタンスで可用性グループを作成することで、両方のレベルで冗長性を持たせることもできます。 

| テクノロジ | サンプル アーキテクチャ |
| --- | --- |
| **可用性グループ** |同じリージョンの Azure VM で実行している可用性レプリカによって、高い可用性が実現します。  Windows フェールオーバー クラスタリングには Active Directory ドメインが必要であるため、ドメイン コントローラー VM を構成する必要があります。<br/><br/> 冗長性と可用性を高めるために、[可用性グループの概要](virtual-machines-windows-portal-sql-availability-group-overview.md)に関するドキュメントに従って、Azure VM を異なる[可用性ゾーン](../../../availability-zones/az-overview.md)にデプロイすることができます。 可用性グループ内の SQL Server VM が可用性ゾーンにデプロイされている場合は、[Azure SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) & [Azure クイックスタート テンプレート](virtual-machines-windows-sql-availability-group-quickstart-template.md)に記載されているように [Standard Load Balancer](../../../load-balancer/load-balancer-standard-overview.md) をリスナーに使用します。<br/> ![可用性グループ](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-ha-always-on.png)<br/>詳細については、「[Azure Virtual Machines での AlwaysOn 可用性グループの自動構成: Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)」を参照してください。 |
| **フェールオーバー クラスター インスタンス** |共有記憶域を必要とするフェールオーバー クラスター インスタンス (FCI) は、4 つの異なる方法で作成できます。<br/><br/>1.接続されたストレージを使用する Azure VM で実行される 2 ノード フェールオーバー クラスター。このストレージは [Windows Server 2016 Storage Spaces Direct \(S2D\)](virtual-machines-windows-portal-sql-create-failover-cluster.md) を使用して、ソフトウェア ベースの仮想 SAN を提供します。<br/><br/> 2.[Premium ファイル共有](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md)を使用して Azure VM で実行される 2 ノード フェールオーバー クラスター。 Premium ファイル共有は、SSD によってバックアップされる、フェールオーバー クラスター インスタンスとの使用が完全にサポートされている継続的に待機時間の短いファイル共有です。<br/><br/>3.サード パーティのクラスタリング ソリューションでサポートされるストレージを使用する Azure VM で実行される 2 ノード フェールオーバー クラスター。 SIOS DataKeeper を使用する具体的な例については、[フェールオーバー クラスタリングとサード パーティ製ソフトウェアの SIOS DataKeeper を使用したファイル共有の高可用性](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/)に関するページを参照してください。<br/><br/>4.ExpressRoute を介してリモート iSCSI ターゲット共有ブロック記憶域を使用する Azure VM で実行される 2 ノード フェールオーバー クラスター。 たとえば、NetApp Private Storage (NPS) は、ExpressRoute と Equinix を使用して iSCSI ターゲットを Azure VM に公開します。<br/><br/>サードパーティの共有記憶域とデータ レプリケーション ソリューションの場合は、フェールオーバーでのデータ アクセスに関する問題について、ベンダーに問い合わせてください。<br/><br/>|

## <a name="azure-only-disaster-recovery-solutions"></a>Azure のみ:ディザスター リカバリー ソリューション
Azure 内の SQL Server データベースのディザスター リカバリー ソリューションを実現するには、可用性グループ、データベース ミラーリング、またはストレージ BLOB によるバックアップと復元を使用します。

| テクノロジ | サンプル アーキテクチャ |
| --- | --- |
| **可用性グループ** |可用性レプリカが、障害復旧のために、Azure VM の複数のデータセンターで実行されます。 この複数のリージョンにわたるソリューションでは、完全なサイトの機能停止の場合にも保護できます。 <br/> ![可用性グループ](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-alwayson.png)<br/>リージョン内で、すべてのレプリカが同じクラウド サービスおよび同じ VNet 内にある必要があります。 各リージョンは個別の VNet を持つため、これらのソリューションには VNet と VNet 間の接続が必要です。 詳細については、「[Azure Portal を使用した VNet 間接続の構成](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)」をご覧ください。 詳細については、「[さまざまな地域に存在する Azure Virtual Machines に Always On 可用性グループを構成する](virtual-machines-windows-portal-sql-availability-group-dr.md)」を参照してください。|
| **データベース ミラーリング** |プリンシパルとミラーとサーバーが、ディザスター リカバリーのために、異なるデータセンターで実行されます。 サーバー証明書を使ってデプロイする必要があります。 SQL Server データベース ミラーリングは、Azure VM 上の SQL Server 2008 および SQL Server 2008 R2 ではサポートされていません。 <br/>![データベース ミラーリング](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-dbmirroring.png) |
| **Azure BLOB ストレージ サービスを使用したバックアップと復元** |実稼働データベースが、ディザスター リカバリーのために、別のデータセンターの BLOB ストレージに直接バックアップされます。<br/>![バックアップと復元](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-backup-restore.png)<br/>詳細については、「[Azure Virtual Machines における SQL Server のバックアップと復元](virtual-machines-windows-sql-backup-recovery.md)」を参照してください。 |
| **Azure Site Recovery を使用する Azure への SQL Server のレプリケートおよびフェールオーバー** |ある Azure データセンターの実稼働 SQL Server が、ディザスター リカバリーのために別の Azure データセンターの Azure Storage に直接レプリケートされます。<br/>![Azure Site Recovery を使用してレプリケートする](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-standalone-sqlserver-asr.png)<br/>詳細については、「[SQL Server ディザスター リカバリーおよび Azure Site Recovery を使用した SQL Server の保護](../../../site-recovery/site-recovery-sql.md)」を参照してください。 |


## <a name="hybrid-it-disaster-recovery-solutions"></a>ハイブリッド IT: ディザスター リカバリー ソリューション
ハイブリッド IT 環境内の SQL Server データベースのディザスター リカバリー ソリューションを実現するには、可用性グループ、データベース ミラーリング、ログ配布、および Azure BLOB ストレージによるバックアップと復元を使用します。

| テクノロジ | サンプル アーキテクチャ |
| --- | --- |
| **可用性グループ** |クロスサイト障害復旧のために、いくつかの可用性レプリカが Azure VM で実行され、その他のレプリカがオンプレミスで実行されます。 実稼働サイトは、オンプレミスに置くことも、Azure データセンターに置くこともできます。<br/>![可用性グループ](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-alwayson.png)<br/>すべての可用性レプリカは同じフェールオーバー クラスターに存在する必要があるため、クラスターは両方のネットワークにまたがっている必要があります (マルチサブネット フェールオーバー クラスター)。 この構成には、Azure とオンプレミス ネットワークの間の VPN 接続が必要です。<br/><br/>データベースのディザスター リカバリーを成功させるには、ディザスター リカバリー サイトにレプリカ ドメイン コントローラーもインストールする必要があります。<br/><br/>SSMS のレプリカの追加ウィザードを使用して、既存の AlwaysOn 可用性グループに Azure レプリカを追加できます。 詳しくは、「Tutorial: Extend your Always On Availability Group to Azure (チュートリアル: Always On 可用性グループを Azure に拡張する)」をご覧ください。 |
| **データベース ミラーリング** |クロスサイトでのディザスター リカバリーのために、1 つのサーバーが Azure VM で実行され、もう 1 つがオンプレミスで実行されます。 パートナーは、同じ Active Directory ドメイン内にある必要がなく、VPN 接続も必要ありません。<br/>![データベース ミラーリング](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-dbmirroring.png)<br/>もう 1 つのデータベース ミラーリング シナリオとして、クロスサイト障害復旧のために、1 つのパートナーを Azure VM で実行し、もう 1 つを同じ Active Directory ドメイン内のオンプレミスで実行するという形式があります。 [Azure Virtual Network とオンプレミス ネットワークの間の VPN 接続](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)が必要です。<br/><br/>データベースのディザスター リカバリーを成功させるには、ディザスター リカバリー サイトにレプリカ ドメイン コントローラーもインストールする必要があります。 SQL Server データベース ミラーリングは、Azure VM 上の SQL Server 2008 および SQL Server 2008 R2 ではサポートされていません。 |
| **ログ配布** |クロスサイト障害復旧のために、1 つのサーバーが Azure VM で実行され、もう 1 つがオンプレミスで実行されます。 ログ配布は Windows ファイル共有に依存しているため、Azure Virtual Network とオンプレミス ネットワークの間の VPN 接続が必要です。<br/>![ログ配布](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-log-shipping.png)<br/>データベースのディザスター リカバリーを成功させるには、ディザスター リカバリー サイトにレプリカ ドメイン コントローラーもインストールする必要があります。 |
| **Azure BLOB ストレージ サービスを使用したバックアップと復元** |オンプレミスの実稼働データベースが、ディザスター リカバリーのために、Azure Blob Storage に直接バックアップされます。<br/>![バックアップと復元](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-backup-restore.png)<br/>詳細については、「[Azure Virtual Machines における SQL Server のバックアップと復元](virtual-machines-windows-sql-backup-recovery.md)」を参照してください。 |
| **Azure Site Recovery を使用する Azure への SQL Server のレプリケートおよびフェールオーバー** |オンプレミスの実稼働 SQL Server が、ディザスター リカバリーのために Azure Storage に直接レプリケートされます。<br/>![Azure Site Recovery を使用してレプリケートする](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-standalone-sqlserver-asr.png)<br/>詳細については、「[SQL Server ディザスター リカバリーおよび Azure Site Recovery を使用した SQL Server の保護](../../../site-recovery/site-recovery-sql.md)」を参照してください。 |

## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Azure での SQL Server HADR の重要な考慮事項
Azure の VM、ストレージ、およびネットワークには、オンプレミスの非仮想化 IT インフラストラクチャとは異なる動作特性があります。 Azure での HADR SQL Server ソリューションの実装を成功させるには、これらの違いを理解し、それに対応したソリューションを設計する必要があります。

### <a name="high-availability-nodes-in-an-availability-set"></a>可用性セットでの高可用性ノード
Azure の可用性セットを使用すると、高可用性ノードを別個の障害ドメイン (FD) と更新ドメイン (UD) に配置できます。 基盤となる Azure プラットフォームにより、可用性セット内の各仮想マシンに更新ドメインと障害ドメインが割り当てられます。 データセンター内のこのような構成により、計画的または計画外のメンテナンス イベント中に、少なくとも 1 つの仮想マシンが利用可能となり、99.95% の Azure SLA を満たします。 高可用性の設定を構成するには、参加するすべての SQL Virtual Machines を同じ可用性セットに配置して、メンテナンス イベント中のアプリケーションまたはデータの損失を回避します。 同じクラウド サービス上にあるノードのみが同じ可用性セットに属することができます。 詳細については、「 [Virtual Machines の可用性管理](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。

### <a name="high-availability-nodes-in-an-availability-zone"></a>可用性ゾーンでの高可用性ノード
Availability Zones は、Azure リージョン内の一意の物理的な場所です。 それぞれのゾーンは、独立した電源、冷却手段、ネットワークを備えた 1 つまたは複数のデータセンターで構成されています。 可用性ゾーンをリージョン内で物理的に分離して、少なくとも 1 つの仮想マシンが確実に利用できるようにし、99.99% Azure SLA を確実に満足することで、データセンターで障害が発生してもアプリケーションとデータは保護されます。 高可用性を構成するには、参加する SQL Virtual Machines をリージョン内の利用可能な Availability Zones に分散させて配置します。 可用性ゾーン VM 間データ転送の追加料金がかかります。 詳細については、[可用性ゾーン](/azure/availability-zones/az-overview)に関するページをご覧ください。 


### <a name="failover-cluster-behavior-in-azure-networking"></a>Azure ネットワークでのフェールオーバー クラスターの動作
Azure の RFC に準拠していない DHCP サービスが原因で、特定のフェールオーバー クラスター構成の作成が失敗する場合があります。これは、クラスター ネットワーク名に重複する IP アドレス (クラスター ノードのいずれかと同じ IP アドレスなど) が割り当てられているためです。 これは、Windows フェールオーバー クラスター機能に依存する、可用性グループを実装するときに問題になります。

2 ノード クラスターを作成し、オンラインにするシナリオを考えてみましょう。

1. クラスターがオンラインになると、NODE1 がクラスター ネットワーク名のために動的に割り当てられた IP アドレスを要求します。
2. DHCP サービスは、要求が NODE1 自体から送られてきていると認識するため、DHCP サービスからは NODE1 自体の IP アドレス以外の IP アドレスは取得できません。
3. NODE1 とフェールオーバー クラスター ネットワーク名の両方に重複するアドレスが割り当てられていることが Windows によって検出されると、既定のクラスター グループはオンラインになることができません。
4. 既定のクラスター グループは、NODE2 に移動されます。NODE1 の IP アドレスはクラスター IP アドレスとして処理され、既定のクラスター グループがオンラインになります。
5. NODE2 が NODE1 との接続を確立しようとするときに、NODE1 宛てのパケットは、NODE1 の IP アドレスが NODE2 の IP アドレスに解決されるため、NODE2 から送信されません。 NODE2 が NODE1 との接続を確立できず、クォーラムを失って、クラスターがシャットダウンされます。
6. 一方、NODE1 は NODE2 にパケットを送信できますが、NODE2 は応答できません。 NODE1 はクォーラムを失い、クラスターをシャットダウンします。

このシナリオは、クラスター ネットワーク名をオンラインにするために、クラスター ネットワーク名にリンク ローカル IP アドレス (169.254.1.1 など) のような未使用の静的 IP アドレスを割り当てることによって回避できます。 このプロセスを簡略化するには、[可用性グループのための Azure での Windows フェールオーバー クラスターの構成](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx)に関するページを参照してください。

詳細については、「[Azure Virtual Machines での AlwaysOn 可用性グループの自動構成: Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)」を参照してください。

### <a name="availability-group-listener-support"></a>可用性グループ リスナーのサポート
可用性グループ リスナーは、Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2、および Windows Server 2016 を実行している Azure VM でサポートされます。 このサポートは、可用性グループ ノードである Azure VM 上で有効になっている負荷分散エンドポイントを使用して実現されます。 Azure で実行されているクライアント アプリケーションと、オンプレミスで実行されているクライアント アプリケーションの両方に対してリスナーを動作させるには、特別な構成手順に従う必要があります。

リスナーを設定するための主なオプションには、外部 (パブリック) と内部の 2 つがあります。 外部 (パブリック) リスナーは、インターネットに接続されているロード バランサーを使用し、インターネット経由でアクセスできるパブリック仮想 IP (VIP) と関連付けられます。 内部リスナーは、内部ロード バランサーを使用し、同じ Virtual Network 内のクライアントのみをサポートします。 どちらのタイプのロード バランサーの場合でも、Direct Server Return を有効にする必要があります。 

可用性グループが複数の Azure サブネットにまたがっている場合 (たとえば、複数の Azure リージョンにわたるデプロイメント)、クライアント接続文字列には "**MultisubnetFailover=True**" を含める必要があります。 これにより、別のサブネット内のレプリカへのパラレル接続が試行されます。 リスナーの設定方法については、次を参照してください。

* [Azure での可用性グループの ILB リスナーの構成](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md)
* [Azure での可用性グループの外部リスナーの構成](../sqlclassic/virtual-machines-windows-classic-ps-sql-ext-listener.md)

この場合でも、サービス インスタンスに直接接続することで、各可用性レプリカに個別に接続できます。 また、可用性グループはデータベース ミラーリング クライアントとの下位互換性があるため、レプリカがデータベース ミラーリングと同様に、次のように構成されていれば、データベース ミラーリング パートナーのように可用性レプリカに接続できます。

* 1 つのプライマリ レプリカと 1 つのセカンダリ レプリカ
* セカンダリ レプリカが読み取り不可として構成されている ( **[読み取り可能なセカンダリ]** オプションが **[いいえ]** に設定されている)

ADO.NET または SQL Server Native Client を使用する、このデータベース ミラーリングと同様の構成に対応するクライアント接続文字列の例を、次に示します。

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

クライアント接続の詳細については、以下を参照してください。

* [SQL Server Native Client での接続文字列キーワードの使用](https://msdn.microsoft.com/library/ms130822.aspx)
* [データベース ミラーリング セッションへのクライアントの接続 (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
* [ハイブリッド IT での可用性グループ リスナーへの接続](https://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
* [可用性グループ リスナー、クライアント接続、およびアプリケーションのフェールオーバー (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
* [可用性グループでのデータベース ミラーリング接続文字列の使用](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>ハイブリッド IT でのネットワーク待ち時間
オンプレミス ネットワークと Azure 間に長いネットワーク待ち時間が生じる期間があることを前提にして、HADR ソリューションをデプロイする必要があります。 レプリカを Azure にデプロイする際は、同期モードで、同期コミットではなく非同期コミットを使用してください。 オンプレミスと Azure の両方にデータベース ミラーリング サーバーをデプロイする際は、高い安全性モードではなく高いパフォーマンス モードを使用してください。

### <a name="geo-replication-support"></a>geo レプリケーション サポート
Azure ディスク内の geo レプリケーションでは、同じデータベースのデータ ファイルとログ ファイルを別個のディスクに格納することはできません。 GRS は、各ディスク上の変更を個別に非同期的にレプリケートします。 このメカニズムでは、1 つのディスク内の geo レプリケートされたコピーでの書き込み順序は保証されますが、複数のディスクでの geo レプリケートされた各コピーでは保証されません。 データ ファイルとログ ファイルを個別のディスクに格納するようにデータベースを構成すると、障害発生後の復旧されたディスクには、ログ ファイルより新しいデータ ファイルのコピーが含まれる可能性があります。その場合、SQL Server の先書きログとトランザクションの ACID プロパティが破損します。 ストレージ アカウントの geo レプリケーションを無効にするオプションがない場合は、特定のデータベースのすべてのデータ ファイルとログ ファイルを同じディスクに置く必要があります。 データベースのサイズのために複数のディスクを使用する必要がある場合は、データの冗長性を確保するために、前に示したディザスター リカバリー ソリューションのいずれかをデプロイする必要があります。

## <a name="next-steps"></a>次の手順
Azure 仮想マシンと SQL Server を作成する必要がある場合は、「 [Azure での SQL Server 仮想マシンのプロビジョニング](virtual-machines-windows-portal-sql-server-provision.md)」を参照してください。

Azure VM で実行されている SQL Server のパフォーマンスを最大限まで高めるには、「 [Azure Virtual Machines における SQL Server のパフォーマンスに関するベスト プラクティス](virtual-machines-windows-sql-performance.md)」のガイダンスをご覧ください。

Azure VM での SQL Server の実行に関するその他のトピックについては、「 [Azure Virtual Machines における SQL Server](virtual-machines-windows-sql-server-iaas-overview.md)」を参照してください。

### <a name="other-resources"></a>その他のリソース
* [Azure での新しい Active Directory フォレストのインストール](../../../active-directory/active-directory-new-forest-virtual-machine.md)
* [Azure VM での可用性グループのフェールオーバー クラスターの作成](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)

