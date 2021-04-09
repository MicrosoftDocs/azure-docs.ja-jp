---
title: Azure Site Recovery を使用した Azure VM のディザスター リカバリーについてよく寄せられる質問
description: この記事では、Azure Site Recovery を使用し、Azure VM をディザスター リカバリーすることに関連してよく寄せられる質問に回答します。
author: sideeksh
manager: rochakm
ms.date: 11/03/2019
ms.topic: conceptual
ms.openlocfilehash: d4fa7348df647cf699ebd55dd6415a79454ab5f1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397952"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>一般的な質問:Azure から Azure へのディザスター リカバリー

この記事では、[Azure Site Recovery](site-recovery-overview.md) サービスを使用した Azure VM の別の Azure リージョンへのディザスター リカバリーについてよく寄せられる質問に回答します。

## <a name="general"></a>全般

### <a name="how-is-site-recovery-priced"></a>Site Recovery の価格

Azure VM のディザスター リカバリーの[コスト](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)について学習します。

### <a name="how-does-the-free-tier-work"></a>Free レベルのしくみを教えてください。

Site Recovery で保護されるすべてのインスタンスは、保護を開始してから 31 日間は無料になります。 この期間が経過すると、各インスタンスは[料金の詳細](https://azure.microsoft.com/pricing/details/site-recovery/)にまとめられた料金で保護されます。 [Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/?service=site-recovery)を使用してコストを見積もることができます。

### <a name="do-i-incur-other-azure-charges-in-the-first-31-days"></a>最初の 31 日間に、その他の Azure 料金は発生しますか?

はい。 Azure Site Recovery では、最初の 31 日間、インスタンスは無料で保護されますが、Azure Storage、ストレージ トランザクション、データ転送については料金が発生する場合があります。 復旧された VM からも、Azure のコンピューティングの料金が発生する場合があります。 G

### <a name="how-do-i-get-started-with-azure-vm-disaster-recovery"></a>Azure VM ディザスター リカバリーの使用を開始するにはどうすればよいですか?

1. Azure VM ディザスター リカバリー アーキテクチャを[理解する](azure-to-azure-architecture.md)。
2. サポート要件を[確認する](azure-to-azure-support-matrix.md)。
3. Azure VM のディザスター リカバリーを[設定する](azure-to-azure-how-to-enable-replication.md)。
4. テスト フェールオーバーで[ディザスター リカバリーのテストを実行する](azure-to-azure-tutorial-dr-drill.md)。
5. セカンダリ Azure リージョンへの[完全フェールオーバーを実行する](azure-to-azure-tutorial-failover-failback.md)。
6. セカンダリ リージョンからプライマリ リージョンに[フェールバックする](azure-to-azure-tutorial-failback.md)。

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>ターゲット リージョンの容量を確保するにはどうすればよいですか?

十分なインフラストラクチャ容量が確保されるよう、Site Recovery チームと Azure 容量管理チームが計画を立てます。 フェールオーバーを開始するときに、これらのチームは、Site Recovery によって保護される VM インスタンスがターゲット リージョンにデプロイされるようにするための支援も行います。

## <a name="replication"></a>レプリケーション

### <a name="can-i-replicate-vms-with-disk-encryption"></a>ディスク暗号化が有効になっている VM をレプリケートできますか?

はい。 Site Recovery では、Azure Disk Encryption (ADE) が有効になっている VM のディザスター リカバリーがサポートされています。 レプリケーションを有効にすると、Azure では、必要なすべてのディスク暗号化キーとシークレットがユーザー コンテキストでソース リージョンからターゲット リージョンにコピーされます。 自分に必要なアクセス許可がない場合、セキュリティ管理者がスクリプトを利用し、キーとシークレットをコピーできます。

- Site Recovery では、Windows を実行している Azure VM で ADE がサポートされています。
- Site Recovery は次をサポートします。
    - ADE バージョン 0.1。これには Azure Active Directory (Azure AD) を必要とするスキーマがあります。
    - ADE バージョン 1.1。Azure AD は必要ありません。 バージョン 1.1 の場合、Windows Azure VM にはマネージド ディスクが必要です。
    - [詳細については、こちらを参照してください](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema)。 拡張機能のスキーマについて。

暗号化された VM のレプリケーションの有効化について、[詳細を確認します](azure-to-azure-how-to-enable-replication-ade-vms.md)。

### <a name="can-i-select-an-automation-account-from-a-different-resource-group"></a>別のリソース グループから Automation アカウントを選択できますか?

レプリケートされた Azure VM で実行されている Mobility Service 拡張機能の更新を Site Recovery が管理できるようにすると、Azure Automation アカウントを使用して (Azure サービスによって使用される) グローバル Runbook がデプロイされます。 Site Recovery が作成する Automation アカウントを使用するか、既存の Automation アカウントを使用するように選択することができます。 

現在、ポータルでは、コンテナーと同じリソース グループ内の Automation アカウントのみを選択できます。 PowerShell を使って、別のリソース グループから Automation アカウントを選択できます。 [詳細については、こちらを参照してください](azure-to-azure-autoupdate.md#enable-automatic-updates)。

### <a name="if-i-use-a-customer-automation-account-thats-not-in-the-vault-resource-group-can-i-delete-the-default-runbook"></a>コンテナー リソース グループに含まれていない顧客の Automation アカウントを使用している場合、既定の Runbook を削除できますか?

はい、不要な場合は削除できます。 

### <a name="can-i-replicate-vms-to-another-subscription"></a>別のサブスクリプションに VM をレプリケートできますか?

はい、同じ Azure AD テナント内で任意のサブスクリプションに Azure VM をレプリケートできます。 VM のディザスター リカバリーを有効にした場合、既定では、表示されるターゲット サブスクリプションはソース VM のものになります。 ターゲット サブスクリプションを変更でき、その他の設定 (リソース グループ、仮想ネットワークなど) は、選択したサブスクリプションから自動的に設定されます。

### <a name="can-i-replicate-vms-in-an-availability-zone-to-another-region"></a>可用性ゾーン内の VM を別のリージョンにレプリケートすることはできますか?

はい、可用性ゾーン内の VM を別の Azure リージョンにレプリケートすることができます。 ターゲット VM は、可用性セット内で、またはターゲット リージョンでサポートされている場合は可用性ゾーン内で、1 つのインスタンスとしてデプロイできます。 

### <a name="can-i-replicate-non-zone-vms-to-a-zone-within-the-same-region"></a>非ゾーンの VM を同じリージョン内のゾーンにレプリケートすることはできますか? 

これは、ポータルではサポートされていません。 これを行うには、REST API/PowerShell を使用できます。

### <a name="can-i-replicate-zoned-vms-to-a-different-zone-in-the-same-region"></a>ゾーン VM を同じリージョンの別のゾーンにレプリケートすることはできますか?

このサポートは、いくつかのリージョンに制限されています。 [詳細については、こちらを参照してください](azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)。

### <a name="can-i-exclude-disks-from-replication"></a>レプリケーションからディスクを除外できますか?

はい、PowerShell を使用して、レプリケーションを設定するときにディスクを除外できます。 [詳細については、こちらを参照してください](azure-to-azure-exclude-disks.md)。

### <a name="can-i-replicate-new-disks-added-to-replicated-vms"></a>レプリケートされた VM に追加された新しいディスクをレプリケートできますか?

マネージド ディスクがあるレプリケートされた VM では、新しいディスクを追加し、それらに対するレプリケーションを有効にすることができます。 新しいディスクを追加すると、レプリケートされた VM に、VM 上の 1 つ以上のディスクで保護が使用できることを示す警告メッセージが表示されます。 

- 追加されたディスクのレプリケーションを有効にすると、初回のレプリケーション後に警告は表示されなくなります。
- ディスクのレプリケーションを有効にしない場合、この警告を無視できます。
- ディスクが追加された VM をフェールオーバーする場合、レプリケーション ポイントには回復に使用できるディスクが表示されます。 たとえば、ディスクが 1 つある VM に 2 つ目のディスクを追加する場合、追加する前に作成されたレプリケーション ポイントでは、"1/2 台のディスク" と表示されます。

Site Recovery では、レプリケートされた VM からディスクを "ホット リムーブ" できません。 VM ディスクを削除する場合は、VM のレプリケーションを無効にしてから再度有効にする必要があります。

### <a name="how-often-can-i-replicate-to-azure"></a>どのくらいの頻度で Azure にレプリケートできますか?

Azure VM を別の Azure リージョンにレプリケートするときは、レプリケーションは継続的に行われます。 レプリケーションのしくみについては、[こちらを参照してください](./azure-to-azure-architecture.md#replication-process)。

### <a name="can-i-replicate-virtual-machines-within-a-region"></a>リージョン内で仮想マシンをレプリケートできますか? 

Site Recovery を使用して、リージョン内のディスクをレプリケートすることはできません。

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>VM インスタンスを任意の Azure リージョンにレプリケートできますか?

同じ地理クラスター内の 2 つのリージョン間で VM をレプリケートして、復旧できます。 地理クラスターは、データの遅延と主権を念頭に置きながら定義されます。 リージョンのサポートについては、[こちらを参照してください。](./azure-to-azure-support-matrix.md#region-support)

### <a name="does-site-recovery-need-internet-connectivity"></a>Site Recovery にはインターネット接続が必要ですか?

いいえ、ただし、VM には Site Recovery の URL と IP 範囲へのアクセスが必要です。 [詳細については、こちらを参照してください](./azure-to-azure-about-networking.md#outbound-connectivity-for-urls)。

### <a name="can-i-replicate-an-application-tiered-across-resource-groups"></a>リソース グループ間で階層化されたアプリケーションをレプリケートできますか?

はい、アプリをレプリケートし、別のリソース グループにディザスター リカバリー構成を保持することは可能です。

たとえば、異なるリソース グループにアプリが 3 つの層 (アプリケーション/データベース/Web) を持つ場合、すべての層を保護するためにレプリケーションを 3 回有効にする必要があります。 Site Recovery によって、3 つの層が 3 つの異なるリソース グループにレプリケートされます。

### <a name="can-i-move-storage-accounts-across-resource-groups"></a>リソース グループとの間でストレージ アカウントを移動できますか?

いいえ、これはサポートされていません。 ストレージ アカウントを別のリソース グループに誤って移動し、元のリソース グループを削除した場合は、古いリソース グループと同じ名前で新しいリソース グループを作成し、そのストレージ アカウントをこのリソース グループに移動できます。

## <a name="replication-policy"></a>Replication policy

### <a name="what-is-a-replication-policy"></a>レプリケーション ポリシーとは何ですか?

レプリケーション ポリシーでは、復旧ポイントの保持履歴と、アプリ整合性スナップショットの頻度が定義されています。  Site Recovery では、次のように既定のレプリケーション ポリシーが作成されます。

- 復旧ポイントは 24 時間保持されます。
- アプリ整合性スナップショットを 4 時間ごとに取得します。

レプリケーション設定については、[こちらを参照してください](azure-to-azure-how-to-enable-replication.md#customize-target-resources)。

### <a name="whats-a-crash-consistent-recovery-point"></a>クラッシュ整合性復旧ポイントとは何ですか?

クラッシュ整合性復旧ポイントには、スナップショットの作成中にサーバーから電源コードが引き抜かれたときのディスク上のデータが含まれます。 これには、スナップショットの作成時にメモリ内にあったものは一切含まれません。

現在、ほとんどのアプリでは、クラッシュ整合性スナップショットから十分に復旧できます。 クラッシュ整合性復旧ポイントは通常、データベースのないオペレーティング システムや、ファイル サーバー、DHCP サーバー、プリント サーバーなどのアプリにとっては十分です。

Site Recovery では、5 分ごとにクラッシュ整合性復旧ポイントが自動的に作成されます。

### <a name="whats-an-application-consistent-recovery-point"></a>アプリケーション整合性復旧ポイントとは何ですか?

アプリ整合性復旧ポイントは、アプリ整合性スナップショットから作成されます。 ここでは、クラッシュ整合性スナップショットと同じデータがキャプチャされ、メモリ内のデータと処理中のすべてのトランザクションが追加でキャプチャされます。

追加コンテンツのため、アプリ整合性スナップショットは最も複雑となり、時間がかかります。 アプリ整合性の復旧ポイントは、データベース オペレーティング システムや、SQL Server などのアプリで推奨されます。 Windows の場合、アプリ整合性スナップショットでは、ボリューム シャドウ コピー サービス (VSS) が使用されます。

### <a name="do-app-consistent-recovery-points-impact-performance"></a>アプリ整合性復旧ポイントはパフォーマンスに影響しますか?

 アプリ整合性復旧ポイントではメモリとプロセス内のすべてのデータがキャプチャされるため、キャプチャが頻繁になる場合は、ワークロードが既にビジー状態のときにパフォーマンスに影響を与える可能性があります。 データベース以外のワークロードについては、あまり頻繁にはキャプチャしないことをお勧めします。 データベース ワークロードの場合でも、1 時間で十分です。

### <a name="whats-the-minimum-frequency-for-generating-app-consistent-recovery-points"></a>アプリ整合性復旧ポイントを生成するための最小間隔はどれくらいですか?

Site Recovery では、アプリ整合性復旧ポイントを最小間隔の 1 時間で作成できます。

### <a name="can-i-enable-app-consistent-replication-for-linux-vms"></a>Linux VM でアプリ整合性レプリケーションを有効にすることはできますか?

はい。 Linux 用モビリティ エージェントは、アプリ整合性のためのカスタム スクリプトをサポートしています。 エージェントでは、pre オプションと post オプションを使用したカスタム スクリプトが使用されます。 [詳細情報](site-recovery-faq.md#can-i-enable-replication-with-app-consistency-in-linux-servers)

### <a name="how-are-recovery-points-generated-and-saved"></a>復旧ポイントはどのように生成されて保存されますか?

Site Recovery で復旧ポイントを生成する方法を理解するため、例を使ってみましょう。 

- レプリケーション ポリシーでは、復旧ポイントが 24 時間保持され、アプリ整合性の頻度スナップショットが 1 時間ごとに取得されます。
- Site Recovery では、5 分ごとにクラッシュ整合性復旧ポイントが作成されます。 この頻度は変更できません。
- Site Recovery では、1 時間後に復旧ポイントが取り除かれて、1 時間に 1 つの復旧ポイントが保存されます。

そのため、最後の 1 時間については、次の図に示すように、12 のクラッシュ整合性ポイントと 1 つのアプリ整合性ポイントから選択できます。

   ![生成された復旧ポイントの一覧](./media/azure-to-azure-common-questions/recovery-points.png)

### <a name="how-far-back-can-i-recover"></a>過去のどの時点まで遡って復旧できますか?

使用できる最も古い復旧ポイントは 72 時間です。

### <a name="what-happens-if-site-recovery-cant-generate-recovery-points-for-more-than-24-hours"></a>Site Recovery が 24 時間以上復旧ポイントを生成できない場合はどうなりますか? 

レプリケーション ポリシーが 24 時間で、Site Recovery で 24 時間を超えて復旧ポイントを生成できない場合、古い復旧ポイントが残ります。 Site Recovery は、新しいポイントが生成された場合、最も古いポイントのみを置き換えます。 新しい復旧ポイントができるまで、リテンション期間に到達した後も古いポイントはすべて残ります。

### <a name="can-i-change-the-replication-policy-after-replication-is-enabled"></a>レプリケーションを有効にした後で、レプリケーション ポリシーを変更できますか?

はい。 コンテナー > **[Site Recovery のインフラストラクチャ]**  >  **[レプリケーション ポリシー]** で、ポリシーを選択して編集します。 変更は既存のポリシーにも適用されます。

### <a name="are-all-recovery-points-a-complete-vm-copy"></a>すべての復旧ポイントが VM の完全なコピーですか?

生成される最初の復旧ポイントには、完全なコピーがあります。 それ以降の復旧ポイントでは、差分変更が保持されます。

### <a name="do-increases-in-recovery-point-retention-increase-storage-costs"></a>復旧ポイントのリテンション期間の増加によってストレージ コストが増加しますか?

はい。 たとえば、リテンション期間を 24 時間から 72 時間に増やした場合、Site Recovery により追加の 48 時間分の復旧ポイントが保存されます。 追加された時間によって、ストレージの変更が発生します。 単なる例として、1 つの復旧ポイントに 10 GB の差分変更が含まれ、GB あたりのコストが 0.16 ドル/月の場合、1 か月あたり 1.60 ドル * 48 の追加料金が発生します。

## <a name="multi-vm-consistency"></a>マルチ VM 整合性

### <a name="what-is-multi-vm-consistency"></a>マルチ VM 整合性とは何ですか?

マルチ VM 整合性によって、レプリケートされた仮想マシン間で復旧ポイントに整合性が確保されます。

- マルチ VM 整合性を有効にすると、Site Recovery によってオプションが有効なすべてのマシンのレプリケーション グループが作成されます。 
- レプリケーション グループのマシンをフェールオーバーすると、それらのマシン間でクラッシュ整合性復旧ポイントとアプリ整合性復旧ポイントが共有されます。

マルチ VM 整合性を有効にする方法については、[こちらを参照してください](azure-to-azure-tutorial-enable-replication.md#enable-replication)。

### <a name="can-i-fail-over-a-single-vm-in-a-replication-group"></a>レプリケーション グループ内の 1 つの VM をフェールオーバーできますか?

いいえ。 マルチ VM 整合性を有効にすると、アプリがレプリケーション グループ内のすべての VM に依存関係を持つと推測され、単一の VM フェールオーバーは許可されません。

### <a name="how-many-vm-can-i-replicate-together-in-a-group"></a>1 つのグループでまとめてレプリケートできる VM の数はいくつですか?

レプリケーション グループ内の 16 個の VM をまとめてレプリケートできます。

### <a name="when-should-i-enable-multi-vm-consistency"></a>どのようなときにマルチ VM 整合性を有効にする必要がありますか?

マルチ VM 整合性では CPU が集中的に消費されるので、これを有効にすると、ワークロードのパフォーマンスに影響する場合があります。 VM が同じワークロードを実行していて、複数のマシン間に整合性を持たせる必要がある場合にのみ有効にしてください。 たとえば、2 個の SQL Server インスタンスと 2 個の Web サーバーがアプリケーション内にある場合、SQL Server インスタンスに対してのみマルチ VM 整合性を有効にします。

### <a name="can-i-add-a-replicating-vm-to-a-replication-group"></a>レプリケートされている VM をレプリケーション グループに追加できますか?

VM のレプリケーションを有効にすると、新しいレプリケーション グループまたは既存のグループに追加できます。 既にレプリケートされている VM をグループに追加することはできません。 
 
## <a name="failover"></a>[フェールオーバー]

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>ターゲット リージョンの容量を確保するにはどうすればよいですか?

十分なインフラストラクチャ容量が確保されるよう、Site Recovery チームと Azure 容量管理チームが計画を立てます。 フェールオーバーを開始するとき、Site Recovery によって保護される VM インスタンスがターゲット リージョンにデプロイできるよう、チームも支援します。

### <a name="is-failover-automatic"></a>フェールオーバーは自動で行われますか。

フェールオーバーは自動では行われません。 ポータルで 1 回クリックするだけでフェールオーバーを開始できます。または [PowerShell](azure-to-azure-powershell.md) を使用してフェールオーバーをトリガーすることもできます。

### <a name="can-i-keep-a-public-ip-address-after--failover"></a>フェールオーバー後にパブリック IP アドレスを保持できますか?

運用アプリのパブリック IP アドレスは、フェールオーバー後に保持できません。

フェールオーバー プロセスの一環としてワークロードを起動するとき、Azure パブリック IP アドレス リソースをワークロードに割り当てる必要があります。 リソースはターゲット リージョンで使用可能である必要があります。 Azure パブリック IP アドレス リソースは手動で割り当てるか、復旧計画で自動化できます。 フェールオーバー後にパブリック IP アドレスを設定する方法については、[こちらを参照してください](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan)。

### <a name="can-i-keep-a-private-ip-address-after-failover"></a>フェールオーバー後にプライベート IP アドレスを保持できますか?

はい。 既定では、Azure VM のディザスター リカバリーを有効にすると、Site Recovery によって、ソース リソースの設定に基づいてターゲット リソースが作成されます。 Azure VM が静的 IP アドレスで構成されている場合、Site Recovery は、使用中でなければ同じ IP アドレスをターゲット VM にプロビジョニングしようとします。
フェールオーバー後の IP アドレスの保持については、[こちらを参照してください](site-recovery-retain-ip-azure-vm-failover.md)。

### <a name="why-is-a-vm-assigned-a-new-ip-address-after-failover"></a>フェールオーバー後に VM に新しい IP アドレスが割り当てられるのはなぜですか?

Site Recovery では、フェールオーバー時に IP アドレスの指定が試みられます。 別の VM がそのアドレスを使っている場合、Site Recovery では次の使用可能な IP アドレスがターゲットとして設定されます。

仮想ネットワークのネットワーク マッピングと IP アドレス指定の設定については、[こちらを参照してください](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms)。

### <a name="whats-the-latest-recovery-point"></a>"*最新*" の復旧ポイントとは何ですか?

"*最新 (最低 RPO)* " の復旧ポイント オプションでは、次の処理が行われます。

1. 最初に、Site Recovery に送信されたすべてのデータが処理されます。
2. サービスでデータが処理された後、VM にフェールオーバーする前に、VM ごとに復旧ポイントが作成されます。 このオプションは、最も低い目標復旧ポイント (RPO) を提供します。
3. フェールオーバー後に作成された VM には、フェールオーバーのトリガー時に Site Recovery にレプリケートされたすべてのデータが含まれます。

### <a name="do-latest-recovery-points-impact-failover-rto"></a>"*最新*" の復旧ポイントは、フェールオーバー RTO に影響しますか?

はい。 Site Recovery では、フェールオーバー前に保留中のすべてのデータが処理されるので、このオプションは他のオプションよりも目標復旧時間 (RTO) が高くなります。

### <a name="whats-the-latest-processed-recovery-option"></a>"*最後に処理があった時点*" の復旧オプションとは何ですか?

"*最後に処理があった時点*" オプションでは、次のことが行われます。

1. Site Recovery によって最後に処理された復旧ポイントに、すべての VM をフェールオーバーします。 このオプションを使用すると、未処理のデータの処理に時間がかからないため、RTO を低くできます。

### <a name="what-if-theres-an-unexpected-outage-in-the-primary-region"></a>プライマリ リージョンで予期しない障害が発生した場合はどうすればよいですか?

フェールオーバーを開始できます。 Site Recovery では、フェールオーバーを実行するためにプライマリ リージョンからの接続を必要としません。

### <a name="what-is-the-rto-of-a-vm-failover"></a>VM フェールオーバーの RTO とは何ですか?

Site Recovery の RTO SLA は [2 時間](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/)です。 ほとんどの場合、Site Recovery によって数分以内に VM がフェールオーバーされます。 RTO を計算するにはフェールオーバー ジョブを確認します。ここでは、VM を起動するまでにかかった時間が示されます。 

## <a name="recovery-plans"></a>復旧計画

### <a name="whats-a-recovery-plan"></a>復旧計画とは何ですか?

Site Recovery での[復旧計画](site-recovery-create-recovery-plans.md)は、VM のフェールオーバーと復旧を調整します。 これは、復旧を常に正確で、繰り返し可能、さらに自動化されるようにするのに役立ちます。 その後、次の処理を実行します。

- 一緒にフェールオーバーする VM のグループを定義します。
- アプリケーションが正確に起動するように、VM 間の依存関係を定義します。
- VM フェールオーバー以外のタスクに対するカスタム手動アクションのオプションを使用して、復旧を自動化します。 


### <a name="how-does-sequencing-work"></a>シーケンス処理のしくみを教えてください。

復旧計画では、シーケンス処理のために VM の複数のグループを作成できます。 グループは一度に 1 つずつフェールオーバーされるので、同じグループに含まれる VM は一緒にフェールオーバーされます。 [詳細については、こちらを参照してください](recovery-plan-overview.md#model-apps)。

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>復旧計画の RTO を検索するにはどうしたらよいですか?

復旧計画の RTO を確認するには、復旧計画のテスト フェールオーバーを実行します。 **Site Recovery ジョブ** で、テスト フェールオーバーの期間を確認します。 このスクリーンショットの例では、**SAPTestRecoveryPlan** テスト フェールオーバー ジョブに 8 分 59 秒かかりました。

![RTO のテスト フェールオーバーの期間を示すジョブの一覧](./media/azure-to-azure-common-questions/recovery-plan-rto.png)

### <a name="can-i-add-automation-runbooks-to-recovery-plans"></a>復旧計画に Automation Runbook を追加できますか?

はい。 [詳細については、こちらを参照してください](site-recovery-runbook-automation.md)。

## <a name="reprotection-and-failback"></a>再保護とフェールバック

### <a name="after-failover-are-vms-in-the-secondary-region-protected-automatically"></a>フェールオーバー後、セカンダリ リージョンの VM は自動的に保護されますか? 

いいえ。 あるリージョンから別のリージョンに Azure VM をフェールオーバーすると、その VM はターゲット ディザスター リカバリー リージョン内で保護されていない状態で起動されます。 セカンダリ リージョン内の VM を[再保護](./azure-to-azure-how-to-reprotect.md)するには、プライマリ リージョンに戻ってレプリケーションを有効にします。

### <a name="when-i-reprotect-is-all-data-replicated-from-the-secondary-region-to-primary"></a>再保護すると、セカンダリ リージョンからプライマリにすべてのデータがレプリケートされますか? 

状況により異なります。ソース リージョンの VM が存在している場合、ソース ディスクとターゲット ディスクの間の変更のみが同期されます。 Site Recovery では、ディスクを変更点と比較した後、データが転送されます。 通常、このプロセスには数時間かかります。 [詳細については、こちらを参照してください](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection)。

### <a name="how-long-does-it-take-fail-back"></a>フェールバックにはどのくらいの時間がかかりますか?

再保護後のフェールオーバーには、プライマリ リージョンからセカンダリ リージョンにフェールオーバーするときと大体同じ時間がかかります。

## <a name="capacity"></a><a name="capacity"></a>容量

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>ターゲット リージョンの容量を確保するにはどうすればよいですか?

十分なインフラストラクチャ容量が確保されるよう、Site Recovery チームと Azure 容量管理チームが計画を立てます。 フェールオーバーを開始するとき、Site Recovery によって保護される VM インスタンスがターゲット リージョンにデプロイできるよう、チームも支援します。

### <a name="does-site-recovery-work-with-reserved-instances"></a>Site Recovery は予約インスタンスと連携しますか?

はい、ディザスター リカバリー リージョンで[予約 Azure VM](https://azure.microsoft.com/pricing/reserved-vm-instances/) を購入することができます。Site Recovery のフェールオーバー操作でその VM が使用されます。 追加の構成は必要ありません。

## <a name="security"></a>セキュリティ

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Site Recovery サービスにレプリケーション データが送信されますか。

いいえ、Site Recovery はレプリケートされたデータをインターセプトすることも、VM 上の実行内容に関するどのような情報を保持することもありません。 レプリケーションとフェールオーバーを調整するために必要なメタデータのみが、Site Recovery サービスに送信されます。

Site Recovery は ISO 27001:2013、27018、HIPAA、DPA 認定です。 このサービスは現在、SOC2 と FedRAMP JAB の評価を判定されている最中です。

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery はレプリケーションを暗号化しますか。

はい、転送中の暗号化と [Azure に保存中の暗号化](../storage/common/storage-service-encryption.md)の両方がサポートされています。

## <a name="next-steps"></a>次のステップ

- [Azure 間サポートの要件を確認します](azure-to-azure-support-matrix.md)。
- [Azure 間レプリケーションを設定します](azure-to-azure-tutorial-enable-replication.md)。
- この記事の内容について質問がある場合は、[Azure Recovery Services に関する Microsoft Q&A 質問ページ](/answers/topics/azure-site-recovery.html)に投稿してください。
