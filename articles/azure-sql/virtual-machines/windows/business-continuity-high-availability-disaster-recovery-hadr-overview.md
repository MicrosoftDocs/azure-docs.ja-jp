---
title: 高可用性、ディザスター リカバリー、ビジネス継続性
description: Always On 可用性グループ、フェールオーバー クラスター インスタンス、データベース ミラーリング、ログ配布、Azure Storage へのバックアップと復元など、Azure VM 上の SQL Server で使用できる高可用性、ディザスター リカバリー (HADR)、およびビジネス継続性のオプションについて学習します。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2020
ms.author: mathoma
ms.openlocfilehash: f4d870f458607ceb0f05812b5c0c066ce810448e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102508318"
---
# <a name="business-continuity-and-hadr-for-sql-server-on-azure-virtual-machines"></a>Azure Virtual Machines 上の SQL Server のビジネス継続性と HADR
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

ビジネス継続性とは、障害発生時にビジネスを継続し、復旧の計画を立て、データの高可用性を確保することを意味します。 Azure Virtual Machines 上の SQL Server は、高可用性とディザスター リカバリー (HADR) データベース ソリューションのコストを削減するのに役立ちます。 

ほとんどの SQL Server HADR ソリューションは、Azure のみとハイブリッドの両方のソリューションとして、仮想マシン (VM) でサポートされます。 Azure のみのソリューションでは、HADR システム全体が Azure で実行されます。 ハイブリッド構成では、ソリューションの一部が Azure で実行され、その他の部分が組織内のオンプレミスで実行されます。 Azure 環境は柔軟性が高いので、SQL Server データベース システムの予算や HADR 要件に応じて、部分的に、または完全に Azure に移動できます。

この記事では、Azure VM 上の SQL Server で使用できるビジネス継続性ソリューションを比較、対比します。 

## <a name="overview"></a>概要

サービス レベル アグリーメント (SLA) で求められる HADR 機能が、確実にデータベース システムに備わっているようにすることは、お客様の責任です。 Azure には、クラウド サービスのサービス復旧、仮想マシンの障害復旧検出などの高可用性メカニズムが用意されていますが、それだけで SLA を満たせることが保証されるわけではありません。 これらのメカニズムは仮想マシンの高可用性の保護に役立ちますが、VM 内で実行される SQL Server の可用性は保護されません。 

VM がオンラインで正常であっても、SQL Server インスタンスで障害が発生する可能性があります。 Azure によって提供される高可用性メカニズムでも、ソフトウェアまたはハードウェアの障害からの回復やオペレーティング システムのアップグレードなどのイベントのために、VM のダウンタイムが生じます。

Azure の geo 冗長ストレージ (GRS) は、geo レプリケーションと呼ばれる機能と共に実装されます。 GRS は、ご利用のデータベースに適したディザスター リカバリー ソリューションではない可能性があります。 geo レプリケーションではデータを非同期的に送信するため、障害が発生したときに最新の更新が失われる場合があります。 geo レプリケーションの制限事項の詳細については、「[geo レプリケーション サポート](#geo-replication-support)」セクションを参照してください。

## <a name="deployment-architectures"></a>デプロイ アーキテクチャ
Azure では、ビジネス継続性のための以下の SQL Server テクノロジがサポートされます。

* [Always On 可用性グループ](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)
* [Always On フェールオーバー クラスター インスタンス (FCI)](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
* [ログ配布](/sql/database-engine/log-shipping/about-log-shipping-sql-server)
* [Azure Blob Storage を使った SQL Server のバックアップと復元](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
* [データベース ミラーリング](/sql/database-engine/database-mirroring/database-mirroring-sql-server) - SQL Server 2016 では非推奨

高可用性とディザスター リカバリーの両方の機能を持つ SQL Server ソリューションを実装するために、テクノロジを組み合わせることができます。 使用するテクノロジによっては、ハイブリッド デプロイで、Azure 仮想ネットワークを使った VPN トンネルが必要になる場合があります。 以下の各セクションでは、デプロイ アーキテクチャの例をいくつか示します。

## <a name="azure-only-high-availability-solutions"></a>Azure のみ:高可用性ソリューション

SQL Server の高可用性ソリューションは、Always On 可用性グループを使用して、データベース レベルで実現することができます。 また、Always On フェールオーバー クラスター インスタンスを使用して、インスタンス レベルで高可用性ソリューションを作成することもできます。 保護を強化するために、フェールオーバー クラスター インスタンスで可用性グループを作成し、両方のレベルで冗長性を持たせることもできます。 

| テクノロジ | アーキテクチャの例 |
| --- | --- |
| **可用性グループ** |同じリージョンの Azure VM で実行している可用性レプリカによって、高い可用性が実現します。 Windows フェールオーバー クラスタリングには Active Directory ドメインが必要であるため、ドメイン コントローラー VM を構成する必要があります。<br/><br/> 冗長性と可用性を高めるために、[可用性グループの概要](availability-group-overview.md)に関するドキュメントに従って、Azure VM を異なる[可用性ゾーン](../../../availability-zones/az-overview.md)にデプロイすることができます。 可用性グループ内の SQL Server VM が可用性ゾーンにデプロイされている場合は、[Azure SQL VM CLI](./availability-group-az-commandline-configure.md) および [Azure クイックスタート テンプレート](availability-group-quickstart-template-configure.md)に関する記事に記載されているように、[Azure Standard Load Balancer](../../../load-balancer/load-balancer-overview.md) をリスナーに使用します。<br/> !["プライマリ レプリカ"、"セカンダリ レプリカ"、および "ファイル共有監視" で作成された "WSFC クラスター" の上の "ドメイン コントローラー" を示す図。](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-ha-always-on.png)<br/>詳細については、「[Azure Virtual Machines での AlwaysOn 可用性グループの自動構成: Resource Manager](./availability-group-quickstart-template-configure.md)」を参照してください。 |
| **フェールオーバー クラスター インスタンス** |フェールオーバー クラスター インスタンスは SQL Server VM でサポートされています。 FCI 機能には共有ストレージが必要であるため、次の 5 つのソリューションは Azure VM 上の SQL Server で機能します。 <br/><br/> - Windows Server 2019 用の [Azure 共有ディスク](failover-cluster-instance-azure-shared-disks-manually-configure.md)の使用。 共有マネージド ディスクは、マネージド ディスクを複数の仮想マシンに同時に接続できるようにする Azure 製品です。 クラスター内の VM では、SCSI の永続的な予約 (SCSI PR) を使用するクラスター化アプリケーションによって選択された予約に基づいて、接続されたディスクに対して読み取りまたは書き込みを行うことができます。 SCSI PR は、オンプレミスの記憶域ネットワーク (SAN) 上で実行されているアプリケーションによって使用される、業界標準のストレージ ソリューションです。 マネージド ディスクで SCSI PR を有効にすると、これらのアプリケーションを Azure にそのまま移行することができます。 <br/><br/>- Windows Server 2016 以降用のソフトウェアベースの仮想 SAN を提供するための、[記憶域スペース ダイレクト \(S2D\)](failover-cluster-instance-storage-spaces-direct-manually-configure.md) の使用。<br/><br/>- Windows Server 2012 以降用の [Premium ファイル共有](failover-cluster-instance-premium-file-share-manually-configure.md)の使用。 Premium ファイル共有は、SSD によってバックアップされ、待機時間が常に短く、FCI との使用が完全にサポートされています。<br/><br/>- クラスタリングのためにパートナー ソリューションでサポートされているストレージの使用。 SIOS DataKeeper を使用する具体的な例については、[フェールオーバー クラスタリングと SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/) に関するブログ エントリを参照してください。<br/><br/>- Azure ExpressRoute を介したリモート iSCSI ターゲット用の共有ブロック記憶域の使用。 たとえば、NetApp Private Storage (NPS) は、ExpressRoute と Equinix を使用して iSCSI ターゲットを Azure VM に公開します。<br/><br/>Microsoft パートナーの共有ストレージとデータ レプリケーション ソリューションの場合は、フェールオーバーでのデータ アクセスに関する問題について、ベンダーにお問い合わせください。<br/><br/>||

## <a name="azure-only-disaster-recovery-solutions"></a>Azure のみ: ディザスター リカバリー ソリューション
可用性グループ、データベース ミラーリング、またはストレージ BLOB によるバックアップと復元を使用して、Azure 内の SQL Server データベースのディザスター リカバリー ソリューションを実現することができます。

| テクノロジ | アーキテクチャの例 |
| --- | --- |
| **可用性グループ** |可用性レプリカが、障害復旧のために、Azure VM の複数のデータセンターで実行されます。 この複数のリージョンにわたるソリューションは、完全なサイトの機能停止の場合の保護に役立ちます。 <br/> !["プライマリ レプリカ" と "セカンダリ レプリカ" が "非同期コミット" によって接続されている 2 つのリージョンを示す図。](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-alwayson.png)<br/>リージョン内では、すべてのレプリカが同じクラウド サービスおよび同じ仮想ネットワーク内にある必要があります。 各リージョンには個別の仮想ネットワークがあるため、これらのソリューションには仮想ネットワーク間の接続が必要です。 詳細については、[Azure portal を使用したネットワーク間接続の構成](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)に関するページを参照してください。 詳細については、[さまざまな Azure リージョンに存在する SQL Server Always On 可用性グループの構成](availability-group-manually-configure-multiple-regions.md)に関するページを参照してください。|
| **データベース ミラーリング** |プリンシパルとミラーとサーバーが、ディザスター リカバリーのために、異なるデータセンターで実行されます。 サーバー証明書を使用して、それらをデプロイする必要があります。 SQL Server データベース ミラーリングは、Azure VM 上の SQL Server 2008 や SQL Server 2008 R2 ではサポートされていません。 <br/>!["ハイ パフォーマンス" の別のリージョンのミラーに接続された 1 つのリージョンの "プリンシパル" を示す図。](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-dbmirroring.png) |
| **Azure Blob Storage を使用したバックアップと復元** |運用データベースが、ディザスター リカバリーのために、別のデータセンターの BLOB ストレージに直接バックアップされます。<br/>![1 つのリージョンの "データベース" を、別のリージョンの "Blob Storage" にバックアップすることを示す図。](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-backup-restore.png)<br/>詳細については、「[Azure VM における SQL Server のバックアップと復元](../../../azure-sql/virtual-machines/windows/backup-restore.md)」を参照してください。 |
| **Azure Site Recovery を使用する Azure への SQL Server のレプリケートおよびフェールオーバー** |ある Azure データセンターの運用 SQL Server インスタンスが、ディザスター リカバリーのために別の Azure データセンターの Azure Storage に直接レプリケートされます。<br/>![ディザスター リカバリーのために別のデータセンターの "ASR レプリケーション" を使用する、1 つの Azure データセンターにある "データベース" を表示する図。 ](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-standalone-sqlserver-asr.png)<br/>詳細については、「[SQL Server ディザスター リカバリーおよび Azure Site Recovery を使用した SQL Server の保護](../../../site-recovery/site-recovery-sql.md)」を参照してください。 |


## <a name="hybrid-it-disaster-recovery-solutions"></a>ハイブリッド IT: ディザスター リカバリー ソリューション
可用性グループ、データベース ミラーリング、ログ配布、および Azure Blob Storage によるバックアップと復元を使用して、ハイブリッド IT 環境内の SQL Server データベースのディザスター リカバリー ソリューションを実現することができます。

| テクノロジ | サンプル アーキテクチャ |
| --- | --- |
| **可用性グループ** |クロスサイト障害復旧のために、いくつかの可用性レプリカが Azure VM で実行され、その他のレプリカがオンプレミスで実行されます。 実稼働サイトは、オンプレミスに置くことも、Azure データセンターに置くこともできます。<br/>![可用性グループ](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-alwayson.png)<br/>すべての可用性レプリカは同じフェールオーバー クラスターに存在する必要があるため、クラスターは両方のネットワークにまたがっている必要があります (マルチサブネット フェールオーバー クラスター)。 この構成には、Azure とオンプレミス ネットワークの間の VPN 接続が必要です。<br/><br/>データベースのディザスター リカバリーを成功させるには、ディザスター リカバリー サイトにレプリカ ドメイン コントローラーもインストールする必要があります。|
| **データベース ミラーリング** |一方のパートナーを Azure VM で実行し、もう一方をオンプレミスで実行して、サーバー証明書を使用したクロスサイトでのディザスター リカバリーを行います。 パートナーは、同じ Active Directory ドメイン内にある必要がなく、VPN 接続も必要ありません。<br/>![データベース ミラーリング](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-dbmirroring.png)<br/>もう 1 つのデータベース ミラーリング シナリオとして、クロスサイト障害復旧のために、1 つのパートナーを Azure VM で実行し、もう 1 つを同じ Active Directory ドメイン内のオンプレミスで実行するという形式があります。 [Azure Virtual Network とオンプレミス ネットワークの間の VPN 接続](../../../vpn-gateway/tutorial-site-to-site-portal.md)が必要です。<br/><br/>データベースのディザスター リカバリーを成功させるには、ディザスター リカバリー サイトにレプリカ ドメイン コントローラーもインストールする必要があります。 SQL Server データベース ミラーリングは、Azure VM 上の SQL Server 2008 や SQL Server 2008 R2 ではサポートされていません。 |
| **ログ配布** |クロスサイト障害復旧のために、1 つのサーバーが Azure VM で実行され、もう 1 つがオンプレミスで実行されます。 ログ配布は Windows ファイル共有に依存しているため、Azure Virtual Network とオンプレミス ネットワークの間の VPN 接続が必要です。<br/>![ログ配布](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-log-shipping.png)<br/>データベースのディザスター リカバリーを成功させるには、ディザスター リカバリー サイトにレプリカ ドメイン コントローラーもインストールする必要があります。 |
| **Azure Blob Storage を使用したバックアップと復元** |オンプレミスの運用データベースが、ディザスター リカバリーのために、Azure Blob Storage に直接バックアップされます。<br/>![バックアップと復元](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-backup-restore.png)<br/>詳細については、[Azure Virtual Machines における SQL Server のバックアップと復元](../../../azure-sql/virtual-machines/windows/backup-restore.md)に関するページを参照してください。 |
| **Azure Site Recovery を使用する Azure への SQL Server のレプリケートおよびフェールオーバー** |オンプレミスの運用 SQL Server インスタンスが、ディザスター リカバリーのために Azure Storage に直接レプリケートされます。<br/>![Azure Site Recovery を使用してレプリケートする](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-standalone-sqlserver-asr.png)<br/>詳細については、「[SQL Server ディザスター リカバリーおよび Azure Site Recovery を使用した SQL Server の保護](../../../site-recovery/site-recovery-sql.md)」を参照してください。 |


## <a name="free-dr-replica-in-azure"></a>Azure での無料 DR レプリカ

[ソフトウェア アシュアランス](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)を所有している場合は、パッシブ ディザスター リカバリー インスタンスの追加のライセンス コストを発生させることなく、SQL Server でハイブリッド ディザスター リカバリー (DR) プランを実装できます。

たとえば、3 つのレプリカすべてが Azure 内でホストされている場合は、2 つの無料パッシブ セカンダリを使用できます。 

![すべてが Azure 内の場合、2 つの無料パッシブ](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/failover-with-primary-in-azure.png)

または、1 つのライセンスされたプライマリ (オンプレミス)、1 つの HA 用無料パッシブと 1 つの DR 用無料パッシブ (オンプレミス)、および 1 つの DR 用無料パッシブ (Azure 内) を使用して、ハイブリッド フェールオーバー環境を構成することもできます。

![環境が、1 つのオンプレミスのプライマリ レプリカを使用したハイブリッドのときの 3 つの無料パッシブ](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-with-primary-on-prem.png)

詳細については、[製品ライセンス条項](https://www.microsoft.com/licensing/product-licensing/products)に関するページを参照してください。 

このベネフィットを有効にするには、[SQL Server の仮想マシン リソース](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource)に移動します。 **[設定]** の下にある **[構成]** を選択してから、 **[SQL Server ライセンス]** で **[ディザスター リカバリー]** オプションを選びます。 この SQL Server VM がパッシブ レプリカとして使用されることを確認するためのチェック ボックスをオンにし、 **[適用]** を選択して設定を保存します。 

![Azure でディザスター リカバリー レプリカを構成する](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/dr-replica-in-portal.png)


## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Azure での SQL Server HADR の重要な考慮事項
Azure の VM、ストレージ、およびネットワークには、オンプレミスの非仮想化 IT インフラストラクチャとは異なる動作特性があります。 Azure での HADR SQL Server ソリューションの実装を成功させるには、これらの違いを理解し、それに対応したソリューションを設計する必要があります。

### <a name="high-availability-nodes-in-an-availability-set"></a>可用性セット内の高可用性ノード
Azure の可用性セットを使用すると、高可用性ノードを別個の障害ドメインと更新ドメインに配置できます。 Azure プラットフォームによって、可用性セット内の各仮想マシンに更新ドメインと障害ドメインが割り当てられます。 データセンター内のこの構成により、計画的または計画外のメンテナンス イベント中に、少なくとも 1 つの仮想マシンが利用可能であり、99.95% の Azure SLA が満たされることが保証されます。 

高可用性の設定を構成するには、参加するすべての SQL Server 仮想マシンを同じ可用性セットに配置して、メンテナンス イベント中のアプリケーションまたはデータの損失を回避します。 同じクラウド サービス上にあるノードのみが同じ可用性セットに属することができます。 詳細については、[仮想マシンの可用性の管理](../../../virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページを参照してください。

### <a name="high-availability-nodes-in-an-availability-zone"></a>可用性ゾーン内の高可用性ノード
可用性ゾーンは、Azure リージョン内の一意の物理的な場所です。 それぞれのゾーンは、独立した電源、冷却手段、ネットワークを備えた 1 つまたは複数のデータセンターで構成されています。 リージョン内での可用性ゾーンの物理的な分離は、少なくとも 1 つの仮想マシンが利用可能であり、99.99% の Azure SLA が満たされることを保証することで、データセンターで障害が発生した場合にアプリケーションとデータを保護するのに役立ちます。 

高可用性を構成するには、参加する SQL Server 仮想マシンをリージョン内の可用性ゾーンに分散させて配置します。 可用性ゾーン間のネットワーク間転送については、追加料金が発生します。 詳細については、[可用性ゾーン](../../../availability-zones/az-overview.md)に関するページをご覧ください。 


### <a name="failover-cluster-behavior-in-azure-networking"></a>Azure ネットワークでのフェールオーバー クラスターの動作
Azure の RFC に準拠していない DHCP サービスにより、特定のフェールオーバー クラスター構成の作成に失敗する可能性があります。 この失敗は、クラスター ネットワーク名に重複する IP アドレス (クラスター ノードの 1 つと同じ IP アドレスなど) が割り当てられていることが原因で発生します。 これは、Windows フェールオーバー クラスター機能に依存する、可用性グループを使用するときに問題になります。

2 ノード クラスターを作成し、オンラインにするシナリオを考えてみましょう。

1. クラスターがオンラインになると、NODE1 によって、クラスター ネットワーク名のために動的に割り当てられた IP アドレスが要求されます。
2. DHCP サービスでは要求が NODE1 自体からのものであることが認識されるため、DHCP サービスで NODE1 自体の IP アドレス以外の IP アドレスは提供されません。
3. NODE1 とフェールオーバー クラスターのネットワーク名の両方に重複するアドレスが割り当てられていることが Windows によって検出されると、既定のクラスター グループはオンラインになることができません。
4. 既定のクラスター グループは NODE2 に移動されます。 NODE2 によって、NODE1 の IP アドレスはクラスター IP アドレスとして処理され、既定のクラスター グループがオンラインになります。
5. NODE2 では、NODE1 との接続を確立しようとするときに、NODE1 の IP アドレスがそれ自体に解決されるため、NODE1 宛てのパケットは NODE2 から送信されません。 NODE2 では NODE1 との接続を確立できず、クォーラムが失われ、クラスターがシャットダウンされます。
6. NODE1 では NODE2 にパケットを送信できますが、NODE2 は応答できません。 NODE1 はクォーラムを失い、クラスターをシャットダウンします。

このシナリオは、クラスター ネットワーク名をオンラインにするために、クラスター ネットワーク名に未使用の静的 IP アドレスを割り当てることによって回避できます。 たとえば、169.254.1.1 のようなリンク ローカル IP アドレスを使用できます。 このプロセスを簡略化するには、[可用性グループのための Azure での Windows フェールオーバー クラスターの構成](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx)に関するページを参照してください。

詳細については、「[Azure Virtual Machines での AlwaysOn 可用性グループの自動構成: Resource Manager](./availability-group-quickstart-template-configure.md)」を参照してください。

### <a name="support-for-availability-group-listeners"></a>可用性グループ リスナーのサポート
可用性グループ リスナーは、Windows Server 2012 以降を実行している Azure VM でサポートされています。 このサポートは、可用性グループ ノードである Azure VM 上で有効になっている負荷分散エンドポイントを使用して実現されます。 Azure で実行されているクライアント アプリケーションと、オンプレミスで実行されているものの両方に対してリスナーを動作させるには、特別な構成手順に従う必要があります。

リスナーを設定するための主なオプションには、外部 (パブリック) と内部の 2 つがあります。 外部 (パブリック) リスナーでは、インターネットに接続されているロード バランサーを使用し、インターネット経由でアクセスできるパブリック仮想 IP と関連付けられます。 内部リスナーでは、内部ロード バランサーを使用し、同じ仮想ネットワーク内のクライアントのみをサポートします。 どちらのタイプのロード バランサーの場合でも、Direct Server Return を有効にする必要があります。 

可用性グループが複数の Azure サブネットにまたがっている場合 (複数の Azure リージョンにわたるデプロイなど)、クライアント接続文字列には `MultisubnetFailover=True` を含める必要があります。 これにより、別のサブネット内のレプリカへのパラレル接続が試行されます。 リスナーの設定手順については、[Azure での可用性グループの ILB リスナーの構成](availability-group-listener-powershell-configure.md)に関するページを参照してください。


この場合でも、サービス インスタンスに直接接続することで、各可用性レプリカに個別に接続できます。 また、可用性グループはデータベース ミラーリング クライアントとの下位互換性があるため、レプリカがデータベース ミラーリングと同様に、次のように構成されていれば、データベース ミラーリング パートナーのように可用性レプリカに接続できます。

* 1 つのプライマリ レプリカと 1 つのセカンダリ レプリカがある。
* セカンダリ レプリカが読み取り不可として構成されている ( **[読み取り可能なセカンダリ]** オプションが **[いいえ]** に設定されている)。

ADO.NET または SQL Server Native Client を使用する、このデータベース ミラーリングと同様の構成に対応するクライアント接続文字列の例を以下に示します。

```console
Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;
```

クライアント接続の詳細については、以下を参照してください。

* [SQL Server Native Client での接続文字列キーワードの使用](/sql/relational-databases/native-client/applications/using-connection-string-keywords-with-sql-server-native-client)
* [データベース ミラーリング セッションへのクライアントの接続 (SQL Server)](/sql/database-engine/database-mirroring/connect-clients-to-a-database-mirroring-session-sql-server)
* [ハイブリッド IT での可用性グループ リスナーへの接続](/archive/blogs/sqlalwayson/connecting-to-availability-group-listener-in-hybrid-it)
* [可用性グループ リスナー、クライアント接続、およびアプリケーションのフェールオーバー (SQL Server)](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover)
* [可用性グループでのデータベース ミラーリング接続文字列の使用](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover)

### <a name="network-latency-in-hybrid-it"></a>ハイブリッド IT でのネットワーク待ち時間
オンプレミス ネットワークと Azure 間に長いネットワーク待ち時間が生じる期間がある可能性があることを前提として、HADR ソリューションをデプロイします。 レプリカを Azure にデプロイする場合、同期モードでは同期コミットではなく、非同期コミットを使用します。 オンプレミスと Azure の両方にデータベース ミラーリング サーバーをデプロイする場合は、高い安全性モードではなく、高いパフォーマンス モードを使用します。

### <a name="geo-replication-support"></a>geo レプリケーション サポート
Azure ディスク内の geo レプリケーションでは、同じデータベースのデータ ファイルとログ ファイルを別個のディスクに格納することはできません。 GRS は、各ディスク上の変更を個別に非同期的にレプリケートします。 このメカニズムでは、1 つのディスク内の geo レプリケートされたコピーでの書き込み順序は保証されますが、複数のディスクでの geo レプリケートされた各コピーでは保証されません。 データ ファイルとログ ファイルを個別のディスクに格納するようにデータベースを構成すると、障害発生後の復旧されたディスクには、ログ ファイルより新しいデータ ファイルのコピーが含まれる可能性があります。その場合、SQL Server の先書きログとトランザクションの ACID プロパティ (原子性、一貫性、分離性、持続性) が破損します。 

ストレージ アカウントの geo レプリケーションを無効にするオプションがない場合は、データベースのすべてのデータおよびログ ファイルを同じディスクに保持します。 データベースのサイズのために複数のディスクを使用する必要がある場合は、データの冗長性を確保するために、前述の一覧表示されたディザスター リカバリー ソリューションのいずれかをデプロイします。

## <a name="next-steps"></a>次のステップ

[可用性グループ](availability-group-overview.md)と[フェールオーバー クラスター インスタンス](failover-cluster-instance-overview.md)のどちらが、ビジネスに最適なビジネス継続性ソリューションであるかを判断します。 その後、高可用性とディザスター リカバリー用に環境を構成するための[ベスト プラクティス](hadr-cluster-best-practices.md)を確認します。