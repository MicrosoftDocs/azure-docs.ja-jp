---
title: Azure Site Recovery サービスに関する一般的な質問
description: この記事では、Azure Site Recovery に関してよく寄せられる一般的な質問について説明します。
ms.topic: conceptual
ms.date: 7/14/2020
ms.author: raynew
ms.openlocfilehash: 9db91a15c0ee5c982f73f36a36f12b38b969a125
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99820198"
---
# <a name="general-questions-about-azure-site-recovery"></a>Azure Site Recovery に関する一般的な質問

この記事では、Azure Site Recovery に関してよく寄せられる質問をまとめています。 特定のシナリオについては、次の記事をご覧ください

- [Azure への Azure VM のディザスター リカバリーに関する質問](azure-to-azure-common-questions.md)
- [Azure への VMware VM のディザスター リカバリーに関する質問](vmware-azure-common-questions.md)
- [Azure への Hyper-V VM のディザスター リカバリーに関する質問](hyper-v-azure-common-questions.md)
 
## <a name="general"></a>全般

### <a name="what-does-site-recovery-do"></a>Site Recovery は何をするものですか。

Site Recovery は、リージョン間の Azure VM のレプリケーション、オンプレミスの仮想マシンと物理サーバーの Azure へのレプリケーション、およびオンプレミスのマシンのセカンダリ データセンターへのレプリケーションを調整および自動化することで、ビジネス継続性とディザスター リカバリー (BCDR) 戦略に貢献します。 [詳細については、こちらを参照してください](site-recovery-overview.md)。

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>Docker ディスクを備えた仮想マシンを保護できますか?

いいえ、これはサポートされていないシナリオです。

### <a name="what-does-site-recovery-do-to-ensure-data-integrity"></a>データの整合性を確保するために Site Recovery では何が行われますか。

データの整合性を確保するために Site Recovery ではさまざまな対策が講じられます。 HTTPS プロトコルを使用して、すべてのサービス間にセキュリティで保護された接続が確立されます。 これにより、すべてのマルウェアまたは外部エンティティによるデータの改ざんがなくなります。 もう 1 つの対策として、チェックサムを使用します。 ソースとターゲット間のデータ転送は、それらの間でのデータのチェックサムを計算することによって実行されます。 これにより、転送されるデータの整合性が保たれます。

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
Azure パック、クラウド プラットフォーム システム、および System Center ベース (Hyper 2012 以降) のデプロイメントがサポートされています。 [詳細情報](/previous-versions/azure/windows-server-azure-pack/dn850370(v=technet.10)) をご覧ください。

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>単一の Azure パックおよび単一の VMM サーバーのデプロイメントもサポートされますか。
はい。Hyper-V 仮想マシンを Azure にレプリケートできます。または、サービス プロバイダー サイト間でレプリケートできます。  サービス プロバイダー サイト間でレプリケートする場合は、Azure Runbook 統合が使用できないことに注意してください。

## <a name="pricing"></a>価格

### <a name="where-can-i-find-pricing-information"></a>価格情報はどこにありますか?
[Site Recovery 料金](https://azure.microsoft.com/pricing/details/site-recovery/)の詳細をご確認ください。


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Site Recovery の使用中は、どのようにして概算料金を計算できますか?

Site Recovery を使用している間は、[料金計算ツール](https://aka.ms/asr_pricing_calculator)を利用してコストを見積もることができます。

コストの詳細な見積もりのためには、[VMware](./site-recovery-deployment-planner.md) または [Hyper-V](https://aka.ms/asr-deployment-planner) に対してデプロイ プランナー ツールを実行し、[コスト見積もりレポート](./site-recovery-vmware-deployment-planner-cost-estimation.md)を使用します。


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>現在、VMware VM と物理サーバーをレプリケートするために、マネージド ディスクが使用されています。 マネージド ディスクには、キャッシュ ストレージ アカウントに対する追加料金はありますか?

いいえ、キャッシュに追加料金はありません。 Standard ストレージ アカウントにレプリケートする場合、このキャッシュ ストレージは同じターゲット ストレージ アカウントの一部になります。

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Azure Site Recovery のユーザーになって 1 か月以上が経過しました。 保護されたインスタンスはすべて、引き続き最初の 31 日間無料の対象になりますか?

はい。 保護されたインスタンスはすべて、最初の 31 日間は Azure Site Recovery の料金が発生しません。 たとえば、過去 6 か月間に 10 個のインスタンスを保護していて、11 番目のインスタンスを Azure Site Recovery に接続した場合、最初の 31 日間は 11 番目のインスタンスに対する変更はありません。 最初の 10 個のインスタンスは、既に 31 日間を超えて保護されているので、継続して Azure Site Recovery の料金が発生します。

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>最初の 31 日間に、その他の Azure 料金は発生しますか?

はい。Site Recovery では、保護されたインスタンスは最初の 31 日間無料ですが、Azure Storage、ストレージ トランザクション、およびデータ転送については料金が発生する場合があります。 また、復旧された仮想マシンも、Azure のコンピューティングの料金が発生する場合があります。


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>ディザスター リカバリー ドリル/テスト フェールオーバーの実行に関連するコストはありますか?

DR ドリルに対しては、別個のコストはかかりません。 テスト フェールオーバー後に VM が作成されると、以降はコンピューティングの料金がかかります。



## <a name="security"></a>Security

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Site Recovery サービスにレプリケーション データが送信されますか。
いいえ。Site Recovery は、レプリケートされたデータをインターセプトすることも、仮想マシンまたは物理サーバーでの実行内容に関するどのような情報を持つこともありません。
レプリケーション データは、オンプレミスの Hyper-V ホスト、VMware ハイパーバイザー、物理サーバーと Azure Storage またはセカンダリ サーバーとの間でやり取りされます。 Site Recovery には、これらのデータをインターセプトする能力はありません。 レプリケーションとフェールオーバーを調整するために必要なメタデータのみが、Site Recovery サービスに送信されます。  

Site Recovery は ISO 27001:2013、27018、HIPAA、DPA の認証を受けており、SOC2 および FedRAMP JAB の評価が進行中です。

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>コンプライアンスのため、オンプレミスのメタデータであっても、同じ地理的リージョン内に維持される必要があります。 Site Recovery は役立ちますか。
はい。 リージョンに Site Recovery コンテナーを作成すると、レプリケーションとフェールオーバーを有効にし、調整するために必要なすべてのメタデータが、そのリージョンの地理的境界内に維持されます。

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery はレプリケーションを暗号化しますか。
オンプレミスのサイト間で仮想マシンと物理サーバーをレプリケートする場合には、転送中の暗号化がサポートされます。 Azure に仮想マシンと物理サーバーをレプリケートする場合には、転送中の暗号化と [(Azure での) 保管データの暗号化](../storage/common/storage-service-encryption.md)の両方がサポートされます。

### <a name="does-azure-to-azure-site-recovery-use-tls-12-for-all-communications-across-microservices-of-azure"></a>Azure から Azure へのサイトの回復では、Azure のマイクロサービス間のすべての通信に TLS 1.2 が使用されますか。
はい。Azure から Azure へのサイトの回復のシナリオでは、TLS 1.2 プロトコルが既定で適用されます。 

### <a name="how-can-i-enforce-tls-12-on-vmware-to-azure-and-physical-server-to-azure-site-recovery-scenarios"></a>VMware から Azure へ、および物理サーバーから Azure へのサイトの回復のシナリオで TLS 1.2 を適用するにはどうすればよいですか。
レプリケートされたアイテムにインストールされているモビリティ エージェントは、TLS 1.2 でのみプロセス サーバーと通信します。 ただし、構成サーバーから Azure への通信と、プロセス サーバーから Azure への通信は、TLS 1.1 または 1.0 で行うことができます。 設定したすべての構成サーバーとプロセス サーバーに TLS 1.2 を適用するには、[ガイダンス](https://support.microsoft.com/en-us/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi)に従ってください。

### <a name="how-can-i-enforce-tls-12-on-hyperv-to-azure-site-recovery-scenarios"></a>HyperV から Azure へのサイトの回復のシナリオに TLS 1.2 を適用するにはどうすればよいですか。
Azure Site Recovery のマイクロサービス間のすべての通信は、TLS 1.2 プロトコルで発生します。 Site Recovery は、システム (OS) で構成されたセキュリティ プロバイダーを使用し、使用可能な最新の TLS プロトコルを使用します。 ユーザーは、レジストリで TLS 1.2 を明示的に有効にする必要があります。その後、Site Recovery は、サービスとの通信に TLS 1.2 を使用するようになります。 

### <a name="how-can-i-enforce-restricted-access-on-my-storage-accounts-which-are-accessed-by-site-recovery-service-for-readingwriting-replication-data"></a>レプリケーション データの読み取りと書き込みのために Site Recovery サービスからアクセスされるストレージ アカウントに対して、制限付きアクセスを適用するにはどうすればいいですか。
*[Identity]\(ID\)* 設定に移動して、Recovery Services コンテナーのマネージド ID を切り替えることができます。 コンテナーが Azure Active Directory に登録されたら、ストレージ アカウントに移動して、以下のロールの割り当てをコンテナーに指定することができます。

- Resource Manager ベースのストレージ アカウント (Standard タイプ):
  - [Contributor](../role-based-access-control/built-in-roles.md#contributor)
  - [ストレージ BLOB データ共同作成者](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Resource Manager ベースのストレージ アカウント (Premium タイプ):
  - [Contributor](../role-based-access-control/built-in-roles.md#contributor)
  - [ストレージ BLOB データ所有者](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- 従来のストレージ アカウント:
  - [Classic Storage Account Contributor](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [従来のストレージ アカウント キー オペレーターのサービス ロール](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

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

はい、Site Recovery では Azure リージョン間での Azure VM のディザスター リカバリーがサポートされます。 Azure VM のディザスター リカバリーに関する[一般的な質問を確認します](azure-to-azure-common-questions.md)。 同じ大陸の 2 つの Azure リージョン間でレプリケートしたい場合、Azure から Azure への DR オファリングを使用してください。 構成サーバーまたはプロセス サーバー、ExpressRoute 接続を設定する必要はありません。

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>VMware VM でディザスター リカバリーはサポートされますか?

Site Recovery では、オンプレミスの VMware VM のディザスター リカバリーをサポートします。 VMware VM のディザスター リカバリーに関する[一般的な質問を確認します](vmware-azure-common-questions.md)。

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>Hyper-V VM でディザスター リカバリーはサポートされますか?
はい、Site Recovery では、オンプレミスの Hyper-V VM のディザスター リカバリーをサポートします。 Hyper-V VM のディザスター リカバリーに関する[一般的な質問を確認します](hyper-v-azure-common-questions.md)。

### <a name="is-disaster-recovery-supported-for-physical-servers"></a>物理サーバーでディザスター リカバリーはサポートされますか?
はい、Site Recovery では、Windows および Linux を実行しているオンプレミスの物理サーバーの Azure またはセカンダリ サイトへのディザスター リカバリーをサポートします。 [Azure](vmware-physical-azure-support-matrix.md#replicated-machines) および[セカンダリ サイト](vmware-physical-secondary-support-matrix.md#replicated-vm-support)へのディザスター リカバリーについての要件を確認します。
フェールオーバー後、物理サーバーは Azure で VM として実行されることに注意してください。 Azure からオンプレミスの物理サーバーへのフェールバックは、現在サポートされていません。 VMware 仮想マシンにのみフェールバックできます。





## <a name="replication"></a>レプリケーション

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Azure にサイト間 VPN 経由でレプリケートできますか。
Azure Site Recovery は、パブリック エンドポイント経由で Azure ストレージ アカウントまたはマネージド ディスクにデータをレプリケートします。 レプリケーションは、サイト間 VPN 経由では実行されません。 

### <a name="why-cant-i-replicate-over-vpn"></a>VPN 経由でレプリケートできないのはなぜですか?

Azure にレプリケートする場合、レプリケーション トラフィックは Azure Storage のパブリック エンドポイントに到達します。 そのため、レプリケーションは、パブリック インターネットまたは ExpressRoute (Microsoft ピアリングまたは既存のパブリック ピアリング) 経由でのみ実行できます。

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>Riverbed SteelHeads をレプリケーションに使用できますか?

Microsoft のパートナーである Riverbed は、Azure Site Recovery の使用に関する詳細なガイダンスを提供しています。 Riverbed の[ソリューション ガイド](https://community.riverbed.com/s/article/DOC-4627)を確認してください。

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>ExpressRoute を使用して Azure に仮想マシンをレプリケートできますか。
はい。[ExpressRoute を使用して](concepts-expressroute-with-site-recovery.md)オンプレミスの仮想マシンを Azure にレプリケートできます。

- Azure Site Recovery は、パブリック エンドポイント経由で Azure Storage にデータをレプリケートします。 Site Recovery レプリケーションに ExpressRoute を使用するには、[Microsoft ピアリング](../expressroute/expressroute-circuit-peerings.md#microsoftpeering)を設定するか、既存の[パブリック ピアリング](../expressroute/about-public-peering.md) (新しい回線では非推奨) を使用する必要があります。
- Microsoft ピアリングは、レプリケーション用に推奨されるルーティング ドメインです。
- プライベート ピアリングを介したレプリケーションはサポートされていません。
- VMware マシンまたは物理マシンを保護する場合は、構成サーバーの[ネットワーク要件](vmware-azure-configuration-server-requirements.md#network-requirements)も満たしている必要があります。 Site Recovery レプリケーションを調整する場合、構成サーバーが特定の URL に接続する必要があります。 この接続には ExpressRoute は使用できません。
- 仮想マシンが Azure 仮想ネットワークにフェールオーバーされた後は、その Azure 仮想ネットワークでセットアップされた[プライベート ピアリング](../expressroute/expressroute-circuit-peerings.md#privatepeering)を使用して、それらのマシンにアクセスできます。


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>Azure にレプリケートする場合、どの種類のストレージ アカウントまたはマネージド ディスクが必要ですか。

ストレージ アカウントをターゲット ストレージとして使用することは、Azure Site Recovery ではサポートされていません。 マネージド ディスクは、マシンのターゲット ストレージとして使用することをお勧めします。 マネージド ディスクでは、データ回復性のために LRS 型のみがサポートされています。

### <a name="how-often-can-i-replicate-data"></a>どのくらいの頻度でデータをレプリケートできますか。
* **Hyper-V:** Hyper-V VM は 30 秒 (Premium Storage を除く)、5 分、または 15 分ごとにレプリケートできます。
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
* [Azure に Hyper-V VM をレプリケートするためのキャパシティ プランニング](./hyper-v-deployment-planner-overview.md)

### <a name="can-i-enable-replication-with-app-consistency-in-linux-servers"></a>Linux サーバーでアプリの整合性を使用してレプリケーションを有効にすることはできますか。 
はい。 Linux オペレーティング システム用の Azure Site Recovery では、アプリの整合性を保つためのアプリケーション カスタム スクリプトがサポートされています。 プリオプションとポストオプションを含むカスタム スクリプトが、アプリの整合性時に Azure Site Recovery の Mobility Agent によって使用されます。 これを有効にする手順は、次のとおりです。

1. マシンに root としてサインインします。
2. Azure Site Recovery Mobility Agent のインストール場所にディレクトリを変更します。 既定値は "/usr/local/ASR" です。<br>
    `# cd /usr/local/ASR`
3. インストール場所の下にある "VX/scripts" にディレクトリを変更します。<br>
    `# cd VX/scripts`
4. root ユーザーの実行アクセス許可を設定した "customscript.sh" という名前の bash シェル スクリプトを作成します。<br>
    a. このスクリプトでは、"--pre" と "--post" (二重ダッシュに注意してください) のコマンドライン オプションをサポートする必要があります。<br>
    b. プリオプションを使用してスクリプトを呼び出すときに、アプリケーションの入出力を凍結し、ポストオプションを使用して呼び出すときに、アプリケーションの入出力を凍結解除する必要があります。<br>
    c. サンプル テンプレート -<br>

    `# cat customscript.sh`<br>

```
    #!/bin/bash

    if [ $# -ne 1 ]; then
        echo "Usage: $0 [--pre | --post]"
        exit 1
    elif [ "$1" == "--pre" ]; then
        echo "Freezing app IO"
        exit 0
    elif [ "$1" == "--post" ]; then
        echo "Thawed app IO"
        exit 0
    fi
```

5. アプリの整合性を必要とするアプリケーションの pre と post の手順で、凍結と凍結解除の入力/出力コマンドを追加します。 これらを指定する別のスクリプトを追加し、pre と post オプションを使用して "customscript.sh" から呼び出すこともできます。

>[!Note]
>カスタム スクリプトをサポートするには、Site Recovery エージェントのバージョンは 9.24 以上である必要があります。

## <a name="replication-policy"></a>Replication policy

### <a name="what-is-a-replication-policy"></a>レプリケーション ポリシーとは何ですか?

レプリケーション ポリシーによって、復旧ポイントの保持履歴設定が定義されます。 このポリシーでは、アプリ整合性スナップショットの頻度も定義されます。 既定では、次のような既定の設定の新しいレプリケーション ポリシーが Azure Site Recovery で作成されます。

- 復旧ポイントの保持履歴は 24 時間。
- アプリ整合性スナップショットの頻度は 4 時間。

### <a name="what-is-a-crash-consistent-recovery-point"></a>クラッシュ整合性復旧ポイントとは何ですか?

クラッシュ整合性復旧ポイントには、スナップショットの作成中にサーバーから電源コードが引き抜かれたときのディスク上のデータが含まれます。 クラッシュ整合性復旧ポイントには、スナップショットの作成時にメモリに入っていたものは一切含まれません。

現在、ほとんどのアプリケーションは、クラッシュ整合性のスナップショットから十分に復旧できます。 クラッシュ整合性復旧ポイントは通常、データベースのないオペレーティング システムや、ファイル サーバー、DHCP サーバー、プリント サーバーなどのアプリケーションにとっては十分です。

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>クラッシュ整合性復旧ポイントはどのくらいの頻度で生成されますか?

Site Recovery では、5 分ごとにクラッシュ整合性復旧ポイントが作成されます。

### <a name="what-is-an-application-consistent-recovery-point"></a>アプリケーション整合性復旧ポイントとは何ですか?

アプリケーション整合性復旧ポイントは、アプリケーション整合性スナップショットから作成されます。 アプリケーション整合性復旧ポイントでは、クラッシュ整合性スナップショットと同じデータがキャプチャされますが、さらに、メモリに入っていたデータと処理中のすべてのトランザクションもキャプチャされます。

これらの追加コンテンツのため、アプリケーション整合性スナップショットは最も複雑となり、時間がかかります。 アプリケーション整合性の復旧ポイントは、SQL Server などのデータベース オペレーティング システムで推奨されます。

>[!Note]
>Windows マシンに 64 個を超えるボリュームがある場合、アプリケーション整合性復旧ポイントの作成に失敗します。

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>アプリケーション整合性復旧ポイントがアプリケーション パフォーマンスにもたらす影響について教えてください。

アプリケーション整合性復旧ポイントの場合、メモリに入っているデータと処理中のデータがすべてキャプチャされます。 復旧ポイントでそのデータがキャプチャされるため、アプリケーションを停止する目的で、ボリューム シャドウ コピー サービスなどのフレームワークが Windows で必要になります。 キャプチャ プロセスが頻繁に行われる場合、ワークロードが既にビジー状態であれば、パフォーマンスに影響が出ることがあります。 データベース以外のワークロードの場合、アプリ整合性復旧ポイントの頻度を低く設定しないことをお勧めします。 データベース ワークロードの場合であっても、1 時間で十分です。

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>アプリケーション整合性復旧ポイントが生成される最小の頻度はどのくらいですか?

Site Recovery では、アプリケーション整合性復旧ポイントを 1 時間という最小の頻度で作成できます。

### <a name="how-are-recovery-points-generated-and-saved"></a>復旧ポイントはどのように生成されて保存されますか?

Site Recovery で復旧ポイントを生成する方法を理解するため、レプリケーション ポリシーの例を見てみましょう。 このレプリケーション ポリシーの復旧ポイントには 24 時間の保持期間が設定されており、アプリ整合性スナップショットは 1 時間おきに作成されます。

Site Recovery では、5 分ごとにクラッシュ整合性復旧ポイントが作成されます。 この頻度は変更できません。 最後の 1 時間については、12 のクラッシュ整合性ポイントと 1 つのアプリ整合性ポイントから選択できます。 時間が経過すると、最後の 1 時間を超えた復旧ポイントは Site Recovery によってすべて取り除かれ、1 時間につき 1 つの復旧ポイントのみが保存されます。

次のスクリーンショットはこの例を示したものです。 スクリーンショットでは次のようになっています。

- 最後の 1 時間以内では、5 分ごとに復旧ポイントがあります。
- 最後の 1 時間を超えると、Site Recovery では復旧ポイントが 1 つだけ保持されます。

   ![生成された復旧ポイントの一覧](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>過去のどの時点まで遡って復旧できますか?

使用できる最も古い復旧ポイントは 72 時間です。

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>レプリケーション ポリシーを 24 時間に設定しています。 問題が発生し、Site Recovery で 24 時間以上復旧ポイントを生成できなくなった場合、どうなりますか? 以前の復旧ポイントはなくなりますか?

いいえ、以前のすべての復旧ポイントが Site Recovery によって保持されます。 復旧ポイントの保持期間に基づき、Site Recovery では、新しいポイントが生成された場合にのみ、最も古いポイントが置換されます。 問題のために、Site Recovery では新しい復旧ポイントを生成できません。 新しい復旧ポイントができるまで、保持期間に到達した後も古いポイントはすべて残ります。

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>VM でレプリケーションを有効にした後で、レプリケーション ポリシーを変更するにはどうしたらよいですか?

**[Site Recovery コンテナー]**  >  **[Site Recovery インフラストラクチャ]**  >  **[レプリケーション ポリシー]** の順に移動します。 編集するポリシーを選択し、変更内容を保存します。 変更は既存のすべてのレプリケーションにも適用されます。

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>すべての復旧ポイントが VM の完全なコピーですか、それとも差分ですか?

生成される最初の復旧ポイントには、完全なコピーがあります。 それ以降の復旧ポイントでは、差分変更が保持されます。

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>復旧ポイントの保持期間を長くすると、ストレージ コストは増えますか?

はい、保持期間を 24 時間から 72 時間に増やすと、Site Recovery により追加の 48 時間分の復旧ポイントが保存されます。 追加の時間により、ストレージ料金が発生します。 たとえば、1 つの復旧ポイントで 10 GB の差分変更があったとき、GB あたりのコストが 1 か月 $0.16 であるとします。 追加料金は 1 か月あたり $1.60 × 48 になります。


## <a name="failover"></a>[フェールオーバー]
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>Azure にフェールオーバーする場合、フェールオーバー後に Azure VM にどうしたらアクセスできますか?

Azure VM には、セキュリティで保護されたインターネット接続、サイト間 VPN、または Azure ExpressRoute 経由でアクセスできます。 接続するにはさまざまこと準備する必要があります。 [詳細については、こちらを参照してください](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)。


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Azure にフェールオーバーする場合、Azure はどのようにデータの回復力を確認しますか。
Azure は復元するように設計されています。 Site Recovery は、Azure SLA に従って、既にセカンダリ Azure データセンターにフェールオーバーする機能を備えています。 これが発生した場合には、お客様のメタデータとコンテナーは、お客様が選択したコンテナーと同じリージョン内に保持されます。  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>2 つのデータ センター間でレプリケートしている場合、プライマリ データセンターに予期しない障害が発生するとどうなりますか。
セカンダリ サイトから計画外のフェールオーバーをトリガーすることができます。 Site Recovery は、フェールオーバーを実行するためにプライマリ サイトからの接続を必要としません。

### <a name="is-failover-automatic"></a>フェールオーバーは自動で行われますか。
フェールオーバーは自動では行われません。 ポータルで 1 回クリックするだけでフェールオーバーを開始できます。または [Site Recovery PowerShell](/powershell/module/az.recoveryservices) を使用してフェールオーバーをトリガーすることもできます。 Site Recovery ポータルではフェールバックを簡単な操作で行えます。

自動化する場合は、オンプレミスの Orchestrator または Operations Manager を使用して仮想マシンのエラーを検出し、SDK を使用してフェールオーバーをトリガーします。

* リカバリー プランについての[詳細をお読みください](site-recovery-create-recovery-plans.md)。
* フェールオーバーについての[詳細をお読みください](site-recovery-failover.md)。
* VMware VMs と物理サーバーのフェールバックについては、[こちら](./vmware-azure-failback.md) を参照してください。

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>オンプレミスのホストが応答しない場合やクラッシュした場合は、別のホストにフェールバックできますか?
はい。alternate location recovery (別の場所への復旧) を使用すると、Azure から別のホストにフェールバックできます。

* [VMware 仮想マシン用](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [Hyper-V 仮想マシン用](hyper-v-azure-failback.md#fail-back-to-an-alternate-location)

### <a name="what-is-the-difference-between-complete-migration-commit-and-disable-replication"></a>[移行の完了]、[コミット]、[レプリケーションの無効化] の違いは何ですか。

ソースの場所からターゲットの場所へのマシンのフェールオーバーが完了したら、3 つのオプションを選択できます。 3 つの目的はそれぞれ異なります。

1.  **[移行の完了]** は、ソースの場所にはもう戻らないことを意味します。 ターゲット リージョンへの移行はこれで完了です。 [移行の完了] をクリックすると、内部的には [コミット]、[レプリケーションの無効化] の順にトリガーされます。 
2.  **[コミット]** は、これがレプリケーション プロセスの終了ではないことを意味します。 レプリケーション項目はすべての構成と共に保持されるため、後で **[再保護]** を実行して、マシンのレプリケーションをソース リージョンに戻すことができます。 
3.  **[レプリケーションの無効化]** を選択すると、レプリケーションが無効になり、関連するすべての構成が削除されます。 ターゲット リージョンの既存のマシンには影響しません。

## <a name="automation"></a>オートメーション

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>SDK を使用して Site Recovery のシナリオを自動化できますか。
はい。 Rest API、PowerShell、Azure SDK のいずれかを使用して、Site Recovery ワークフローを自動化することができます。 PowerShell を使用した Site Recovery のデプロイについて、現在サポートされているシナリオは次のとおりです。

* [VMM クラウドの Hyper-V VM を Azure PowerShell Resource Manager にレプリケートする](hyper-v-vmm-powershell-resource-manager.md)
* [VMM なしの Hyper-V VM を Azure PowerShell Resource Manager にレプリケートする](hyper-v-azure-powershell-resource-manager.md)
* [PowerShell Resource Manager を使用して Azure へ VMware をレプリケートする](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>コンポーネント/プロバイダーのアップグレード

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>Site Recovery のリリース ノートや更新プログラムのロールアップはどこで入手できますか?

新しい更新プログラムに関する[詳細を確認](site-recovery-whats-new.md)し、[ロールアップ情報を取得](service-updates-how-to.md)してください。

## <a name="next-steps"></a>次のステップ
* [Azure Site Recovery の概要](site-recovery-overview.md)