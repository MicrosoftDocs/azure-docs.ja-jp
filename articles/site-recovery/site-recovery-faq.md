---
title: Azure Site Recovery:よく寄せられる質問 | Microsoft Docs
description: この記事では、Azure Site Recovery に関してよく寄せられる質問について説明します。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 6/27/2019
ms.author: raynew
ms.openlocfilehash: a9c7aa2be945e4fbaa65bdd2a145d576422c5539
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491770"
---
# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>Azure Site Recovery: よく寄せられる質問 (FAQ)
この記事では、Azure Site Recovery に関してよく寄せられる質問をまとめています。</br>
ASR シナリオごとに固有のクエリについては、シナリオ固有のよく寄せられる質問を参照してください。<br>

- [Azure への Azure VM のディザスター リカバリー](azure-to-azure-common-questions.md)
- [Azure への VMware VM のディザスター リカバリー](vmware-azure-common-questions.md)
- [Azure への Hyper-V VM のディザスター リカバリー](hyper-v-azure-common-questions.md)
 
## <a name="general"></a>全般

### <a name="what-does-site-recovery-do"></a>Site Recovery は何をするものですか。
Site Recovery は、リージョン間の Azure VM のレプリケーション、オンプレミスの仮想マシンと物理サーバーの Azure へのレプリケーション、およびオンプレミスのマシンのセカンダリ データセンターへのレプリケーションを調整および自動化することで、ビジネス継続性とディザスター リカバリー (BCDR) 戦略に貢献します。 [詳細情報](site-recovery-overview.md)。

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>Docker ディスクを備えた仮想マシンを保護できますか?

いいえ、これはサポートされていないシナリオです。

## <a name="service-providers"></a>サービス プロバイダー

### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>当社はサービス プロバイダーですが、 Site Recovery は、専用および共有のインフラストラクチャ モデルで動作しますか。
はい。Site Recovery は、専用および共有のインフラストラクチャ モデルの両方をサポートしています。

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>サービス プロバイダーの場合、テナントの ID は Site Recovery サービスと共有されますか。
いいえ。 テナント ID は匿名のままです。 テナントは Site Recovery ポータルにアクセスする必要はありません。 ポータルを操作するのは、サービス プロバイダーの管理者だけです。

### <a name="will-tenant-application-data-ever-go-to-azure"></a>テナントのアプリケーション データは Azure に送信されますか。
サービス プロバイダーが所有するサイト間でレプリケートするときに、アプリケーション データが Azure に送信されることはありません。 データは転送中に暗号化され、サービス プロバイダーのサイト間で直接レプリケートされます。

Azure にレプリケートする場合、アプリケーション データは Site Recovery サービスではなく、Azure ストレージに送信されます。 データは、転送中に暗号化され、暗号化された状態のまま Azure に残ります。

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>テナントに Azure サービスの請求書が届きますか。
いいえ。 Azure の請求関係は、サービス プロバイダーとの直接的な関係です。 サービス プロバイダーは、そのテナントに対して固有の請求書を生成する必要があります。

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Azure にレプリケートする場合、仮想マシンを Azure で常時実行する必要がありますか。
いいえ、データはご使用のサブスクリプション内の Azure Storage にレプリケートされます。 テスト フェールオーバー (障害復旧訓練) または実際のフェールオーバーを実行すると、Site Recovery により、サブスクリプションで仮想マシンが自動的に作成されます。

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Azure にレプリケートする際に、テナント レベルでの隔離は保証されますか。
はい。

### <a name="what-platforms-do-you-currently-support"></a>現在、どのプラットフォームがサポートされていますか。
Azure パック、クラウド プラットフォーム システム、および System Center ベース (Hyper 2012 以降) のデプロイメントがサポートされています。 [詳細情報](https://technet.microsoft.com/library/dn850370.aspx) をご覧ください。

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>単一の Azure パックおよび単一の VMM サーバーのデプロイメントもサポートされますか。
はい。Hyper-V 仮想マシンを Azure にレプリケートできます。または、サービス プロバイダー サイト間でレプリケートできます。  サービス プロバイダー サイト間でレプリケートする場合は、Azure Runbook 統合が使用できないことに注意してください。

## <a name="pricing"></a>価格

### <a name="where-can-i-find-pricing-information"></a>価格情報はどこにありますか?
[Site Recovery 料金](https://azure.microsoft.com/pricing/details/site-recovery/)の詳細をご確認ください。


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Site Recovery の使用中は、どのようにして概算料金を計算できますか?

Site Recovery を使用している間は、[料金計算ツール](https://aka.ms/asr_pricing_calculator)を利用してコストを見積もることができます。

コストの詳細な見積もりのためには、[VMware](https://aka.ms/siterecovery_deployment_planner) または [Hyper-V](https://aka.ms/asr-deployment-planner) に対してデプロイ プランナー ツールを実行し、[コスト見積もりレポート](https://aka.ms/asr_DP_costreport)を使用します。


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>現在、VMware VM と物理サーバーをレプリケートするために、マネージド ディスクが使用されています。 マネージド ディスクには、キャッシュ ストレージ アカウントに対する追加料金はありますか?

いいえ、キャッシュに追加料金はありません。 Standard ストレージ アカウントにレプリケートする場合、このキャッシュ ストレージは同じターゲット ストレージ アカウントの一部になります。

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Azure Site Recovery のユーザーになって 1 か月以上が経過しました。 保護されたインスタンスはすべて、引き続き最初の 31 日間無料の対象になりますか?

はい。 保護されたインスタンスはすべて、最初の 31 日間は Azure Site Recovery の料金が発生しません。 たとえば、過去 6 か月間に 10 個のインスタンスを保護していて、11 番目のインスタンスを Azure Site Recovery に接続した場合、最初の 31 日間は 11 番目のインスタンスに対する変更はありません。 最初の 10 個のインスタンスは、既に 31 日間を超えて保護されているので、継続して Azure Site Recovery の料金が発生します。

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>最初の 31 日間に、その他の Azure 料金は発生しますか?

はい。Site Recovery では、保護されたインスタンスは最初の 31 日間無料ですが、Azure Storage、ストレージ トランザクション、およびデータ転送については料金が発生する場合があります。 また、復旧された仮想マシンも、Azure のコンピューティングの料金が発生する場合があります。


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>ディザスター リカバリー ドリル/テスト フェールオーバーの実行に関連するコストはありますか?

DR ドリルに対しては、別個のコストはかかりません。 テスト フェールオーバー後に VM が作成されると、以降はコンピューティングの料金がかかります。



## <a name="security"></a>セキュリティ

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Site Recovery サービスにレプリケーション データが送信されますか。
いいえ。Site Recovery は、レプリケートされたデータをインターセプトすることも、仮想マシンまたは物理サーバーでの実行内容に関するどのような情報を持つこともありません。
レプリケーション データは、オンプレミスの Hyper-V ホスト、VMware ハイパーバイザー、物理サーバーと Azure Storage またはセカンダリ サーバーとの間でやり取りされます。 Site Recovery には、これらのデータをインターセプトする能力はありません。 レプリケーションとフェールオーバーを調整するために必要なメタデータのみが、Site Recovery サービスに送信されます。  

Site Recovery は ISO 27001:2013、27018、HIPAA、DPA の認証を受けており、SOC2 および FedRAMP JAB の評価が進行中です。

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>コンプライアンスのため、オンプレミスのメタデータであっても、同じ地理的リージョン内に維持される必要があります。 Site Recovery は役立ちますか。
はい。 リージョンに Site Recovery コンテナーを作成すると、レプリケーションとフェールオーバーを有効にし、調整するために必要なすべてのメタデータが、そのリージョンの地理的境界内に維持されます。

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery はレプリケーションを暗号化しますか。
オンプレミスのサイト間で仮想マシンと物理サーバーをレプリケートする場合には、転送中の暗号化がサポートされます。 Azure に仮想マシンと物理サーバーをレプリケートする場合には、転送中の暗号化と [(Azure での) 保管データの暗号化](https://docs.microsoft.com/azure/storage/storage-service-encryption)の両方がサポートされます。




## <a name="disaster-recovery"></a>障害復旧

### <a name="what-can-site-recovery-protect"></a>Site Recovery が保護できるものは何ですか。
* **Azure VM**: Site Recovery は、サポート対象の Azure VM で実行されているすべてのワークロードをレプリケートできます
* **Hyper-V 仮想マシン**:Site Recovery は、Hyper-V VM で実行されているすべてのワークロードを保護できます。
* **物理サーバー**:Site Recovery は、Windows または Linux が実行されている物理サーバーを保護できます。
* **VMware 仮想マシン**:Site Recovery は、VMware VM で実行されているすべてのワークロードを保護できます。

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Site Recovery で保護できるワークロードは何ですか。
Site Recovery を使用すると、サポートされている VM または物理サーバーで実行されているほとんどのワークロードを保護できます。 また、アプリケーションに対応したレプリケーションもサポートしているため、アプリをインテリジェントな状態に復元できます。 Site Recovery は、SharePoint、Exchange、Dynamics、SQL Server、Active Directory などの Microsoft アプリケーションと統合し、Oracle、SAP、IBM、Red Hat などの主要なベンダーと緊密に連携します。 [詳細情報](site-recovery-workload.md) を参照してください。

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Site Recovery を使用してブランチ オフィスのディザスター リカバリーを管理できますか。
はい。 ブランチ オフィスでのレプリケーションとフェールオーバーを調整するために Site Recovery を使用すると、1 か所ですべてのブランチ オフィスのワークロードの統一されたオーケストレーションとビューが得られます。 ブランチ オフィスに出向くことなく、フェールオーバーを容易に実行し、本社からすべてのブランチのディザスター リカバリーを管理できます。


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>Azure VM でディザスター リカバリーはサポートされますか?

はい、Site Recovery では Azure リージョン間での Azure VM のディザスター リカバリーがサポートされます。 Azure VM のディザスター リカバリーに関する[一般的な質問を確認します](azure-to-azure-common-questions.md)。

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>VMware VM でディザスター リカバリーはサポートされますか?

Site Recovery では、オンプレミスの VMware VM のディザスター リカバリーをサポートします。 VMware VM のディザスター リカバリーに関する[一般的な質問を確認します](vmware-azure-common-questions.md)。

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>Hyper-V VM でディザスター リカバリーはサポートされますか?
はい、Site Recovery では、オンプレミスの Hyper-V VM のディザスター リカバリーをサポートします。 Hyper-V VM のディザスター リカバリーに関する[一般的な質問を確認します](hyper-v-azure-common-questions.md)。

## <a name="is-disaster-recovery-supported-for-physical-servers"></a>物理サーバーでディザスター リカバリーはサポートされますか?
はい、Site Recovery では、Windows および Linux を実行しているオンプレミスの物理サーバーの Azure またはセカンダリ サイトへのディザスター リカバリーをサポートします。 [Azure](vmware-physical-azure-support-matrix.md#replicated-machines) および[セカンダリ サイト](vmware-physical-secondary-support-matrix.md#replicated-vm-support)へのディザスター リカバリーについての要件を確認します。
フェールオーバー後、物理サーバーは Azure で VM として実行されることに注意してください。 Azure からオンプレミスの物理サーバーへのフェールバックは、現在サポートされていません。 VMware 仮想マシンにのみフェールバックできます。





## <a name="replication"></a>レプリケーション

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Azure にサイト間 VPN 経由でレプリケートできますか。
Azure Site Recovery は、パブリック エンドポイント経由で Azure ストレージ アカウントまたはマネージド ディスクにデータをレプリケートします。 レプリケーションは、サイト間 VPN 経由では実行されません。 

### <a name="why-cant-i-replicate-over-vpn"></a>VPN 経由でレプリケートできないのはなぜですか?

Azure にレプリケートする場合、レプリケーション トラフィックは Azure Storage のパブリック エンドポイントに到達します。 そのため、レプリケーションは、ExpressRoute (Microsoft ピアリングまたは既存のパブリック ピアリング) を使ったパブリック インターネット経由でのみ実行することができ、VPN は機能しません。

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>Riverbed SteelHeads をレプリケーションに使用できますか?

Microsoft のパートナーである Riverbed は、Azure Site Recovery の使用に関する詳細なガイダンスを提供しています。 Riverbed の[ソリューション ガイド](https://community.riverbed.com/s/article/DOC-4627)を確認してください。

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>ExpressRoute を使用して Azure に仮想マシンをレプリケートできますか。
はい。[ExpressRoute を使用して](concepts-expressroute-with-site-recovery.md)オンプレミスの仮想マシンを Azure にレプリケートできます。

- Azure Site Recovery は、パブリック エンドポイント経由で Azure Storage にデータをレプリケートします。 Site Recovery レプリケーションに ExpressRoute を使用するには、[Microsoft ピアリング](../expressroute/expressroute-circuit-peerings.md#microsoftpeering)を設定するか、既存の[パブリック ピアリング](../expressroute/expressroute-circuit-peerings.md#publicpeering) (新しい回線では非推奨) を使用する必要があります。
- Microsoft ピアリングは、レプリケーション用に推奨されるルーティング ドメインです。
- プライベート ピアリングを介したレプリケーションはサポートされていません。
- VMware マシンまたは物理マシンを保護する場合は、構成サーバーの[ネットワーク要件](vmware-azure-configuration-server-requirements.md#network-requirements)も満たしている必要があります。 Site Recovery レプリケーションを調整する場合、構成サーバーが特定の URL に接続する必要があります。 この接続には ExpressRoute は使用できません。
- 仮想マシンが Azure 仮想ネットワークにフェールオーバーされた後は、その Azure 仮想ネットワークでセットアップされた[プライベート ピアリング](../expressroute/expressroute-circuit-peerings.md#privatepeering)を使用して、それらのマシンにアクセスできます。


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>Azure にレプリケートする場合、どの種類のストレージ アカウントまたはマネージド ディスクが必要ですか。

LRS または GRS ストレージが必要です。 地域的障害が発生した場合やプライマリ リージョンが復旧できない場合にデータの復元性を確保できるように、GRS をお勧めします。 アカウントは、Recovery Services コンテナーと同じリージョンにある必要があります。 Azure Portal に Site Recovery をデプロイする場合、VMware VM、Hyper-V VM、および物理サーバーのレプリケーションで Premium Storage がサポートされます。 マネージド ディスクでは、LRS のみをサポートしています。

### <a name="how-often-can-i-replicate-data"></a>どのくらいの頻度でデータをレプリケートできますか。
* **Hyper-V:** Hyper-V VM では 5 分、または 30 秒ごとにレプリケートできます (Premium Storage を除く)。
* **Azure VM、VMware VM、物理サーバー:** レプリケーションの頻度はここでは関係ありません。 レプリケーションは継続的です。

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>既存の復旧サイトから第 3 のサイトにレプリケーションを拡張することができますか。
拡張またはチェーン レプリケーションはサポートされていません。 [フィードバック フォーラム](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959)でこの機能を要求してください。

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Azure に初めてレプリケートする際に、オフライン レプリケーションを行うことができますか。
これはサポートされていません。 [フィードバック フォーラム](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)でこの機能を要求してください。

### <a name="can-i-exclude-specific-disks-from-replication"></a>レプリケーションから特定のディスクを除外することはできますか。
これは、Azure Portal を使用して Azure に VMware VM と Hyper-V VM をレプリケートする場合にサポートされます。

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>ダイナミック ディスクを持つ仮想マシンをレプリケートできますか。
Hyper-V 仮想マシンをレプリケートする場合、および Azure に VMware VM と物理マシンをレプリケートする場合、ダイナミック ディスクがサポートされます。 オペレーティング システム ディスクはベーシック ディスクである必要があります。


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>レプリケーション トラフィックに割り当てられた帯域幅を調整できますか?
はい。 帯域幅の調整の詳細については、次の記事を参照してください。

* [VMware VM と物理サーバーをレプリケートするためのキャパシティ プランニング](site-recovery-plan-capacity-vmware.md)
* [Azure に Hyper-V VM をレプリケートするためのキャパシティ プランニング](site-recovery-capacity-planning-for-hyper-v-replication.md)



## <a name="failover"></a>フェールオーバー
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>Azure にフェールオーバーする場合、フェールオーバー後に Azure VM にどうしたらアクセスできますか?

Azure VM には、セキュリティで保護されたインターネット接続、サイト間 VPN、または Azure ExpressRoute 経由でアクセスできます。 接続するにはさまざまこと準備する必要があります。 [詳細情報](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)。


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Azure にフェールオーバーする場合、Azure はどのようにデータの回復力を確認しますか。
Azure は復元するように設計されています。 Site Recovery は、Azure SLA に従って、既にセカンダリ Azure データセンターにフェールオーバーする機能を備えています。 これが発生した場合には、お客様のメタデータとコンテナーは、お客様が選択したコンテナーと同じリージョン内に保持されます。  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>2 つのデータ センター間でレプリケートしている場合、プライマリ データセンターに予期しない障害が発生するとどうなりますか。
セカンダリ サイトから計画外のフェールオーバーをトリガーすることができます。 Site Recovery は、フェールオーバーを実行するためにプライマリ サイトからの接続を必要としません。

### <a name="is-failover-automatic"></a>フェールオーバーは自動で行われますか。
フェールオーバーは自動では行われません。 ポータルで 1 回クリックするだけでフェールオーバーを開始できます。または [Site Recovery PowerShell](/powershell/module/az.recoveryservices) を使用してフェールオーバーをトリガーすることもできます。 Site Recovery ポータルではフェールバックを簡単な操作で行えます。

自動化する場合は、オンプレミスの Orchestrator または Operations Manager を使用して仮想マシンのエラーを検出し、SDK を使用してフェールオーバーをトリガーします。

* リカバリー プランについての[詳細をお読みください](site-recovery-create-recovery-plans.md)。
* フェールオーバーについての[詳細をお読みください](site-recovery-failover.md)。
* VMware VMs と物理サーバーのフェールバックについては、[こちら](site-recovery-failback-azure-to-vmware.md) を参照してください。

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>オンプレミスのホストが応答しない場合やクラッシュした場合は、別のホストにフェールバックできますか?
はい。alternate location recovery (別の場所への復旧) を使用すると、Azure から別のホストにフェールバックできます。

* [VMware 仮想マシン用](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [Hyper-V 仮想マシン用](hyper-v-azure-failback.md#perform-failback)

## <a name="automation"></a>Automation

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>SDK を使用して Site Recovery のシナリオを自動化できますか。
はい。 Rest API、PowerShell、Azure SDK のいずれかを使用して、Site Recovery ワークフローを自動化することができます。 PowerShell を使用した Site Recovery のデプロイについて、現在サポートされているシナリオは次のとおりです。

* [VMM クラウドの Hyper-V VM を Azure PowerShell Resource Manager にレプリケートする](hyper-v-vmm-powershell-resource-manager.md)
* [VMM なしの Hyper-V VM を Azure PowerShell Resource Manager にレプリケートする](hyper-v-azure-powershell-resource-manager.md)
* [PowerShell Resource Manager を使用して Azure へ VMware をレプリケートする](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>コンポーネント/プロバイダーのアップグレード

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>Site Recovery のリリース ノートや更新プログラムのロールアップはどこで入手できますか?

新しい更新プログラムに関する[詳細](site-recovery-whats-new.md)を確認し、[ロールアップ情報を取得](service-updates-how-to.md)します。

## <a name="next-steps"></a>次の手順
* [Azure Site Recovery の概要](site-recovery-overview.md)

