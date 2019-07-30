---
title: よくある質問 - Azure Site Recovery を使用した Hyper-V から Azure へのディザスター リカバリー | Microsoft Docs
description: この記事では、Azure Site Recovery サイトを使用したオンプレミス Hyper-V VM の Azure へのディザスター リカバリーを設定する場合によくある質問をまとめます。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.date: 06/30/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 36c109e083873e9c4ec63ebe34f5c5c0cfb6eeb1
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491821"
---
# <a name="common-questions---hyper-v-to-azure-disaster-recovery"></a>よくある質問 - Hyper-V から Azure へのディザスター リカバリー

この記事では、オンプレミスの Hyper-V VM を Azure にレプリケートするときによくある質問に回答します。 

## <a name="general"></a>全般

### <a name="how-is-site-recovery-priced"></a>Site Recovery の価格
詳しくは、「[Site Recovery の価格](https://azure.microsoft.com/pricing/details/site-recovery/)」をご覧ください。

### <a name="how-do-i-pay-for-azure-vms"></a>Azure VM の支払い方法
レプリケーションの間に、データは Azure ストレージにレプリケートされ、VM 変更の料金は発生しません。 Azure へのフェールオーバーを実行すると、Site Recovery は Azure IaaS 仮想マシンを自動的に作成します。 その後は、Azure で消費するコンピューティング リソースに対して課金されます。

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>General Purpose v2 ストレージ アカウントにレプリケートする場合、コストに差はありますか?

Azure Site Recovery のトランザクションは大量にあるため、通常は GPv2 ストレージ アカウントに対して発生するトランザクション コストが増加します。 差を見積もるには、[こちら](../storage/common/storage-account-upgrade.md#pricing-and-billing)をご覧ください。

## <a name="azure"></a>Azure

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>Site Recovery でレプリケーションを調整するには、Hyper-V で何が必要ですか。

Hyper-V ホスト サーバーに必要なものは、デプロイ シナリオによって異なります。 以下の Hyper-V に関する前提条件を参照してください。

* [Hyper-V VM を (VMM なしで) Azure にレプリケートする](site-recovery-hyper-v-site-to-azure.md)
* [Hyper-V VM を (VMM を使って) Azure にレプリケートする](site-recovery-vmm-to-azure.md)
* [Hyper-V VM をセカンダリ データセンターにレプリケートする](site-recovery-vmm-to-vmm.md)
* セカンダリ データセンターにレプリケートする場合は、「 [Hyper-V VM 用のサポートされているゲスト オペレーティング システム](https://technet.microsoft.com/library/mt126277.aspx)」をご覧ください。
* Azure にレプリケーションする場合、Site Recovery は [Azure でサポートされている](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)すべてのゲスト オペレーティング システムをサポートします。

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>クライアント オペレーティング システムで Hyper-V が実行されているときに VM を保護できますか。
いいえ。VM は、サポートされている Windows Server マシンで実行されている Hyper-V ホスト サーバーに配置されている必要があります。 クライアント コンピューターを保護する必要がある場合は、物理マシンとして [Azure](site-recovery-vmware-to-azure.md)、または[セカンダリ データセンター](site-recovery-vmware-to-vmware.md)にレプリケートできます。

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Hyper-V ホストを VMM クラウドに配置する必要がありますか。
セカンダリ データセンターにレプリケートする場合は、VMM クラウドの Hyper-V ホスト サーバーに Hyper-V VM を配置する必要があります。 Azure にレプリケートする場合は、VMM クラウド内にあるかどうかに関係なく、VM をレプリケートできます。 Azure への Hyper-V のレプリケーションについて詳しくは、[こちら](tutorial-hyper-v-to-azure.md)をご覧ください。


### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Hyper-V 第 2 世代仮想マシンを Azure にレプリケートできますか。
はい。 Site Recovery は、フェールオーバー中に第 2 世代から第 1 世代への変換を行います。 フェールバック時に、マシンは第 2 世代に変換し直されます。 詳細については、[こちら](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)を参照してください。


### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>VMM サーバーが 1 つしかない場合、VMM で Site Recovery をデプロイできますか。

はい。 VMM クラウド内の Hyper-V サーバー上にある VM を Azure にレプリケートすることも、同じサーバー上の VMM クラウド間でレプリケートすることもできます。 オンプレミス間のレプリケーションには、プライマリ サイトとセカンダリ サイトの両方に VMM サーバーを配置することをお勧めします。 

### <a name="what-do-i-need-in-azure"></a>Azure で必要なものは何ですか?
Azure サブスクリプション、Recovery Services コンテナー、ストレージ アカウント、仮想ネットワークが必要です。 コンテナー、ストレージ アカウント、ネットワークは、同じリージョンに存在する必要があります。

### <a name="what-azure-storage-account-do-i-need"></a>どの Azure ストレージ アカウントが必要ですか?
LRS または GRS ストレージ アカウントが必要です。 地域的障害が発生した場合やプライマリ リージョンが復旧できない場合にデータの復元性を確保できるように、GRS をお勧めします。 Premium Storage はサポートされています。

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Azure アカウントには VM を作成するアクセス許可が必要ですか?
サブスクリプション管理者の場合は、必要なレプリケーション アクセス許可を持っています。 サブスクリプション管理者ではない場合は、Site Recovery を構成するときに指定するリソース グループと仮想ネットワークに Azure VM を作成するアクセス許可と、選んだストレージ アカウントに書き込むアクセス許可が必要です。 [詳細情報](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)。

### <a name="is-replication-data-sent-to-site-recovery"></a>レプリケーション データは Site Recovery に送信されますか?
いいえ。Site Recovery は、レプリケートされたデータをインターセプトすることも、VM での実行内容に関するどのような情報を持つこともありません。 レプリケーション データは、Hyper-V ホストと Azure Storage の間で交換されます。 Site Recovery には、これらのデータをインターセプトする能力はありません。 レプリケーションとフェールオーバーを調整するために必要なメタデータのみが、Site Recovery サービスに送信されます。  

Site Recovery は ISO 27001:2013、27018、HIPAA、DPA の認証を受けており、SOC2 および FedRAMP JAB の評価が進行中です。

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-region"></a>オンプレミスのメタデータを地理的領域内に保つことができますか?
はい。 リージョンにコンテナーを作成するとき、Site Recovery によって使われるすべてのメタデータは、そのリージョンの地理的な境界内に維持されます。

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery はレプリケーションを暗号化しますか。
はい、転送中の暗号化と [Azure での暗号化](https://docs.microsoft.com/azure/storage/storage-service-encryption)の両方がサポートされています。


## <a name="deployment"></a>Deployment

### <a name="what-can-i-do-with-hyper-v-to-azure-replication"></a>Hyper-V から Azure へのレプリケーションによって何ができますか?

- **ディザスター リカバリー** : 完全なディザスター リカバリーを設定することができます。 このシナリオでは、オンプレミスの Hyper-V VM を Azure Storage にレプリケートします。
    - VM を Azure にレプリケートできます。 オンプレミスのインフラストラクチャが使用できなくなった場合は、Azure にフェールオーバーします。
    - フェールオーバーの際は、レプリケートされたデータを使って Azure VM が作成されます。 Azure VM 上のアプリやワークロードにアクセスできます。
    - オンプレミスのデータセンターが再び使用可能になったら、Azure からオンプレミスのサイトにフェールバックできます。
- **移行**: Site Recovery を使って、オンプレミスの Hyper-V VM を Azure Storage に移行できます。 その後、オンプレミスから Azure にフェールオーバーします。 フェールオーバーの後は、Azure VM で実行しているアプリとワークロードを利用できます。


### <a name="what-do-i-need-on-premises"></a>オンプレミスには何が必要ですか?

1 つ以上のスタンドアロン Hyper-V ホストまたはクラスター化された Hyper-V ホストで実行されている 1 つ以上の VM が必要です。 System Center Virtual Machine Manager (VMM) によって管理されているホスト上で実行されている VM をレプリケートすることもできます。
- VMM を実行していない場合は、Site Recovery のデプロイ中に、Hyper-V サイトに Hyper-V ホストとクラスターを収集します。 Site Recovery エージェント (Azure Site Recovery プロバイダーと Recovery Services エージェント) は個々の Hyper-V ホストにインストールします。
- Hyper-V ホストが VMM クラウドにある場合は、VMM 内でレプリケーションを調整します。 Site Recovery プロバイダーを VMM サーバーにインストールし、Recovery Services エージェントを各 Hyper-V ホストにインストールします。 VMM 論理/VM ネットワークと Azure VNet をマップします。
- Hyper-V から Azure へのアーキテクチャについて詳しくは、[こちら](hyper-v-azure-architecture.md)をご覧ください。

### <a name="can-i-replicate-vms-located-on-a-hyper-v-cluster"></a>Hyper-V クラスターにある VM をレプリケートできますか?

はい。Site Recovery では、クラスター化された Hyper-V ホストがサポートされます。 以下の点に注意してください。

- クラスターのすべてのノードを同じコンテナーに登録する必要があります。
- VMM を使用していない場合は、クラスター内のすべての Hyper-V ホストを同じ Hyper-V サイトに追加する必要があります。
- Azure Site Recovery プロバイダーと Recovery Services エージェントをクラスター内の各 Hyper-V ホストにインストールし、各ホストを Hyper-V サイトに追加します。
- クラスター上で特定の手順を実行する必要はありません。
- Hyper-V 向け Deployment Planner ツールを実行すると、実行中のノードと VM が実行されているノードからプロファイル データが収集されます。 このツールは、オフになっているノードからデータを収集することはできませんが、そのノードを追跡します。 ノードが起動して実行されると、ツールはそのノードからの VM プロファイル データの収集を開始します (VM がプロファイル VM リストに含まれ、ノード上で実行されている場合)。
- Site Recovery コンテナー内の Hyper-V ホスト上の VM を同じクラスター内の別の Hyper-V ホストまたはスタンドアロン ホストに移行する場合、その VM のレプリケーションは影響を受けません。 Hyper-V ホストは、[前提条件](hyper-v-azure-support-matrix.md#on-premises-servers)を満たし、Site Recovery コンテナー内に構成されている必要があります。 


### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>クライアント オペレーティング システムで Hyper-V が実行されているときに VM を保護できますか。
いいえ。VM は、サポートされている Windows Server マシンで実行されている Hyper-V ホスト サーバーに配置されている必要があります。 クライアント コンピューターを保護する必要がある場合は、Azure に[物理マシンとしてレプリケートする](physical-azure-disaster-recovery.md)ことができます。

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Hyper-V 第 2 世代仮想マシンを Azure にレプリケートできますか。
はい。 Site Recovery は、フェールオーバー中に第 2 世代から第 1 世代への変換を行います。 フェールバック時に、マシンは第 2 世代に変換し直されます。

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>SDK を使用して Site Recovery のシナリオを自動化できますか。
はい。 Rest API、PowerShell、Azure SDK のいずれかを使用して、Site Recovery ワークフローを自動化することができます。 現在サポートされている、PowerShell を使用して Hyper-V を Azure にレプリケートするためのシナリオは次のとおりです。

- [PowerShell を使用して、VMM を使用せずに Hyper-V をレプリケートする](hyper-v-azure-powershell-resource-manager.md)
- [PowerShell を使用して、VMM を使用して Hyper-V をレプリケートする](hyper-v-vmm-powershell-resource-manager.md)

## <a name="replication"></a>レプリケーション

### <a name="where-do-on-premises-vms-replicate-to"></a>オンプレミスの VM のレプリケート先はどこですか?
Azure ストレージにデータがレプリケートされます。 フェールオーバーを実行すると、Site Recovery はストレージ アカウントから Azure VM を自動的に作成します。

### <a name="what-apps-can-i-replicate"></a>どのようなアプリをレプリケートできますか?
[レプリケーション要件](hyper-v-azure-support-matrix.md#replicated-vms)に準拠する Hyper-V VM を実行しているすべてのアプリまたはワークロードをレプリケートできます。 また、アプリケーションに対応したレプリケーションもサポートしているため、アプリをインテリジェントな状態にフェールオーバーおよびフェールバックできます。 Site Recovery は、SharePoint、Exchange、Dynamics、SQL Server、Active Directory などの Microsoft アプリケーションと統合し、Oracle、SAP、IBM、Red Hat などの主要なベンダーと緊密に連携します。 [詳細情報](site-recovery-workload.md) を参照してください。

### <a name="whats-the-replication-process"></a>レプリケーションはどのようなプロセスで実行されますか?

1. 初期レプリケーションがトリガーされると、Hyper-V VM スナップショットが作成されます。
2. VM の仮想ハード ディスクは、すべてが Azure にコピーされるまで、1 つずつレプリケートされます。 VM サイズとネットワーク帯域幅によっては、しばらく時間がかかる場合があります。 ネットワーク帯域幅を拡張する方法について確認する。
3. 初期レプリケーションの進行中にディスクの変更が発生した場合、Hyper-V レプリカ レプリケーション トラッカーはそれらの変更を Hyper-V レプリケーション ログ (.hrl) として追跡します。 これらのログ ファイルは、ディスクと同じフォルダーに配置されます。 各ディスクには関連付けられた .hrl ファイルが存在し、これらはセカンダリ ストレージに送信されます。 初期レプリケーションの進行中は、スナップショットおよびログ ファイルによってディスク リソースが消費されます。
4. 初期レプリケーションが完了すると、VM スナップショットは削除されます。
5. ログのすべてのディスク変更が親ディスクに同期され、マージされます。
6. 初期レプリケーションが完了すると、仮想マシンでの保護の最終処理ジョブが実行されます。 VM が保護されるようにネットワークとその他のレプリケーション後の設定を構成します。
7. この段階で、VM の設定をチェックして、フェールオーバーできる状態であるかどうかを確認できます。 VM のディザスター リカバリーの訓練 (フェールオーバーのテスト) を実行して、期待どおりにフェールオーバーされることを確認できます。
8. 初期レプリケーション後は、レプリケーション ポリシーに従って差分レプリケーションが開始されます。
9. 変更は、.hrl ファイルに記録されます。 レプリケーション用に構成された各ディスクには、関連付けられた .hrl ファイルがあります。
10. ログは、お客様のストレージ アカウントに送信されます。 ログが Azure に送信される間、プライマリ ディスクでの変更は、同じフォルダー内の別のログ ファイルで追跡されます。
11. 初期レプリケーションと差分レプリケーションのどちらの場合も、実行中に Azure portal で VM を監視できます。

[ プロセス](hyper-v-azure-architecture.md#replication-process)の詳細を確認する。

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>サイト間 VPN で Azure にレプリケートできますか?

Site Recovery は、パブリック エンドポイントまたは ExpressRoute のパブリック ピアリングを使って、オンプレミスから Azure ストレージにデータをレプリケートします。 サイト間 VPN ネットワーク経由のレプリケーションはサポートされていません。

### <a name="can-i-replicate-to-azure-with-expressroute"></a>ExpressRoute で Azure にレプリケートできますか?

はい。ExpressRoute を使って Azure に VM をレプリケートできます。 Site Recovery はパブリック エンドポイントを使って Azure ストレージ アカウントにデータをレプリケートするので、Site Recovery のレプリケーション用に[パブリック ピアリング](../expressroute/expressroute-circuit-peerings.md#publicpeering)を設定する必要があります。 VM が Azure 仮想ネットワークにフェールオーバーした後は、[プライベート ピアリング](../expressroute/expressroute-circuit-peerings.md#privatepeering)を使ってアクセスできます。


### <a name="why-cant-i-replicate-over-vpn"></a>VPN 経由でレプリケートできないのはなぜですか?

Azure にレプリケートする場合、レプリケーション トラフィックは Azure Storage アカウントのパブリック エンドポイントに到達します。 そのため、ExpressRoute (パブリック ピアリング) を使ったパブリック インターネット経由でのみレプリケートすることができ、VPN は機能しません。 

### <a name="what-are-the-replicated-vm-requirements"></a>レプリケートされる VM にはどのような要件がありますか?

レプリケーションの場合、Hyper-V VM はサポートされているオペレーティング システムを実行している必要があります。 さらに、VM は Azure VM に対する要件を満たす必要があります。 サポート マトリックスについて詳しくは、[こちら](hyper-v-azure-support-matrix.md#replicated-vms)をご覧ください。

### <a name="how-often-can-i-replicate-to-azure"></a>どのくらいの頻度で Azure にレプリケートできますか?

Hyper-V VM は 30 秒 (Premium Storage を除く)、5 分、または 15 分ごとにレプリケートできます。

### <a name="can-i-extend-replication"></a>レプリケーションを拡張することはできますか?
拡張またはチェーン レプリケーションはサポートされていません。 [フィードバック フォーラム](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959)でこの機能を要求してください。

### <a name="can-i-do-an-offline-initial-replication"></a>オフラインの初期レプリケーションを行うことはできますか?
これはサポートされていません。 [フィードバック フォーラム](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)でこの機能を要求してください。

### <a name="can-i-exclude-disks"></a>ディスクを除外することはできますか?
はい、レプリケーションからディスクを除外できます。 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>ダイナミック ディスクを含む VM をレプリケートできますか?
ダイナミック ディスクをレプリケートすることができます。 オペレーティング システム ディスクはベーシック ディスクである必要があります。



## <a name="security"></a>セキュリティ

### <a name="what-access-does-site-recovery-need-to-hyper-v-hosts"></a>Site Recovery では、Hyper-V ホストに対してどのようなアクセスが必要ですか?

Site Recovery は、選択した VM をレプリケートするために Hyper-V ホストにアクセスできる必要があります。 Site Recovery により、Hyper-V ホストに次のものがインストールされます。

- VMM を実行していない場合は、Azure Site Recovery プロバイダーと Recovery Services エージェントが各ホストにインストールされます。
- VMM を実行している場合は、Recovery Services エージェントが各ホストにインストールされます。 プロバイダーは、VMM サーバー上で動作します。


### <a name="what-does-site-recovery-install-on-hyper-v-vms"></a>Site Recovery は Hyper-V VM に何をインストールしますか?

Site Recovery は、レプリケーションが有効になっている Hyper-V VM に対して何も明示的にインストールしません。




## <a name="failover-and-failback"></a>フェールオーバーとフェールバック


### <a name="how-do-i-fail-over-to-azure"></a>Azure へのフェールオーバーはどのように行いますか?

計画または計画外フェールオーバーをオンプレミスの Hyper-V VM から Azure に実行できます。
    - 予定されたフェールオーバーを実行する場合、データが決して失われないように、ソース側の VM はシャット ダウンされます。
    - プライマリ サイトにアクセスできない場合は、計画外フェールオーバーを実行できます。
    - 単一のマシンをフェールオーバーするか、複数のマシンのフェールオーバーを調整するための復旧計画を作成することができます。
    - フェールオーバーを実行します。 フェールオーバーの第 1 段階が完了すると、Azure に作成されたレプリカ VM が表示されるようになります。 必要に応じて、VM にパブリック IP アドレスを割り当てることができます。 その後、フェールオーバーをコミットして、レプリカの Azure VM からワークロードへのアクセスを開始します。
   

### <a name="how-do-i-access-azure-vms-after-failover"></a>フェールオーバー後にはどのようにして Azure VM にアクセスできますか?
フェールオーバー後、Azure VM には、セキュリティで保護されたインターネット接続、サイト間 VPN、または Azure ExpressRoute 経由でアクセスできます。 接続するにはさまざまこと準備する必要があります。 [詳細情報](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>フェールオーバーされたデータに回復力はありますか?
Azure は復元するように設計されています。 Site Recovery は、Azure SLA に従ってセカンダリ Azure データセンターにフェールオーバーする機能を備えています。 フェールオーバーが発生した場合、お客様のメタデータとコンテナーは、お客様が選択したコンテナーと同じリージョン内に保持されます。

### <a name="is-failover-automatic"></a>フェールオーバーは自動で行われますか。
[フェールオーバー](site-recovery-failover.md)は自動では行われません。 ポータルで 1 回クリックするだけでフェールオーバーを開始できます。または [PowerShell](/powershell/module/az.recoveryservices) を使ってフェールオーバーをトリガーすることもできます。

### <a name="how-do-i-fail-back"></a>フェールバックはどのように行いますか?

オンプレミスのインフラストラクチャが再起動すると、フェールバックを行えます。 フェールバックは 3 段階で発生します。

1. いくつかの異なるオプションを使用して、Azure からオンプレミス サイトへの計画的フェールオーバーを開始します。

    - ダウンタイムを最小化: このオプションを使用すると、フェールオーバーの前に Site Recovery によってデータが同期化されます。 変更されたデータ ブロックをチェックし、オンプレミス サイトにそれらのデータ ブロックをダウンロードします。また、Azure VM は稼働し続け、ダウンタイムを最小限に抑えます。 フェールオーバーが完了しなければならないことを手動で指定した場合は、Azure VM がシャット ダウンされ、最後の差分変更がすべてコピーされ、フェールオーバーが開始されます。
    - 完全ダウンロード: このオプションを使用すると、フェールオーバー中にデータが同期されます。 このオプションは、ディスク全体をダウンロードします。 チェックサムは計算されないため高速ですが、ダウンタイムが長くなります。 レプリカ Azure VM をしばらくの間稼働させていた場合や、オンプレミス VM が削除された場合は、このオプションを使用します。

2. 同じ VM または別の VM にフェールバックすることを選択できます。 VM が存在しない場合は Site Recovery が VM を作成するように指定できます。
3. 初期同期が完了したら、フェールオーバーを完了するよう選択します。 これが完了したら、オンプレミス VM にサインインして、すべてが期待どおりに動作していることを確認できます。 Azure Portal では、Azure VM が停止していることがわかります。
4. フェールオーバーをコミットして終了させ、オンプレミス VM からワークロードへのアクセスを再び開始します。
5. ワークロードがフェールバックされると、オンプレミス VM が再び Azure にレプリケートするように、レプリケーションの反転を有効にします。

### <a name="can-i-fail-back-to-a-different-location"></a>異なる場所にフェールバックすることはできますか?
はい、Azure にフェールオーバーした場合、元の場所が利用できないときは、別の場所にフェールバックすることができます。 [詳細情報](hyper-v-azure-failback.md#failback-to-an-alternate-location-in-hyper-v-environment)。
