---
title: Azure Site Recovery を使用した Azure VM のディザスター リカバリーについてよく寄せられる質問
description: この記事では、Azure Site Recovery を使用し、Azure VM をディザスター リカバリーすることに関連してよく寄せられる質問に回答します。
author: sideeksh
manager: rochakm
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: 7bc8427a51a9931ca82155232569767f12a8e266
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2020
ms.locfileid: "87534024"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>一般的な質問:Azure から Azure へのディザスター リカバリー

この記事では、[Azure Site Recovery](site-recovery-overview.md) を使用し、Azure VM を別の Azure リージョンにディザスター リカバリーすることに関連してよく寄せられる質問に回答します。

## <a name="general"></a>全般

### <a name="how-is-site-recovery-priced"></a>Site Recovery の価格

詳しくは、[VM 向け Azure Site Recovery の価格](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)に関するページをご覧ください。

### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Azure Site Recovery の Free レベルの課金はどのように行われますか?

Azure Site Recovery で保護されるすべてのインスタンスは、保護を開始してから 31 日間は無料になります。 その期間を過ぎると、[Azure 仮想マシン向けの Azure Site Recovery の価格](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)に関するページに記載されている価格で各インスタンスが保護されます。

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>最初の 31 日間に、その他の Azure 料金は発生しますか?

はい。 Azure Site Recovery では、最初の 31 日間、インスタンスは無料で保護されますが、Azure Storage、ストレージ トランザクション、データ転送については料金が発生する場合があります。 復旧された仮想マシンからも、Azure のコンピューティングの料金が発生する場合があります。 価格の完全な詳細は、「[Azure Site Recovery の価格](https://azure.microsoft.com/pricing/details/site-recovery)」でご覧ください。

### <a name="what-are-the-best-practices-for-azure-virtual-machines-disaster-recovery"></a>Azure Virtual Machines ディザスター リカバリーのベスト プラクティスは何ですか?

1. [Azure から Azure へのアーキテクチャを理解する](azure-to-azure-architecture.md)
1. [サポートされている構成とサポートされていない構成を確認する](azure-to-azure-support-matrix.md)
1. [Azure VM のディザスター リカバリーを設定する](azure-to-azure-how-to-enable-replication.md)
1. [テスト フェールオーバーの実行](azure-to-azure-tutorial-dr-drill.md)
1. [プライマリ リージョンにフェールオーバーおよびフェールバックする](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-ensured-in-the-target-region"></a>ターゲット リージョンでは、容量はどのように確保されますか?

十分なインフラストラクチャ容量が確保されるよう、Site Recovery チームと Azure 容量管理チームが計画を立てます。 フェールオーバーを開始するとき、Site Recovery によって保護される VM インスタンスがターゲット リージョンにデプロイされるよう、チームも支援します。

## <a name="replication"></a>レプリケーション

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>Azure Disk Encryption が有効になっている VM をレプリケートできますか?

はい。 Site Recovery では、Azure Disk Encryption が有効になっている VM のディザスター リカバリーがサポートされています。 レプリケーションを有効にすると、Azure では、必要なすべてのディスク暗号化キーとシークレットがユーザー コンテキストでソース リージョンからターゲット リージョンにコピーされます。 自分に適切なアクセス許可がない場合、セキュリティ管理者がスクリプトを利用し、キーとシークレットをコピーできます。

- Site Recovery では、Windows を実行している Azure VM の Azure Disk Encryption がサポートされています。
- Site Recovery では、Azure Disk Encryption バージョン 0.1 がサポートされています。これには Azure Active Directory (Azure AD) を必要とするスキーマがあります。 Site Recovery では、Azure AD を必要としないバージョン 1.1 もサポートされています。 [Azure ディスク暗号化の拡張機能スキーマの詳細を確認してください](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema)。
  - Azure Disk Encryption バージョン 1.1 については、Windows VM と共にマネージド ディスクを使用する必要があります。
  - 暗号化された VM のレプリケーションの有効化について、[詳細を確認します](azure-to-azure-how-to-enable-replication-ade-vms.md)。

### <a name="can-i-select-an-automation-account-from-a-different-resource-group"></a>別のリソース グループから Automation アカウントを選択できますか?

これは現時点ではポータルではサポートされていませんが、Powershell を使用して別のリソース グループから Automation アカウントを選択できます。

### <a name="after-specifying-an-automation-account-that-is-in-a-different-resource-group-than-the-vault-am-i-permitted-to-delete-the-runbook-if-there-is-no-other-vault-to-specify"></a>コンテナーとは異なるリソース グループに含まれる Automation アカウントを指定した後、他に指定するコンテナーがない場合に Runbook を削除することはできますか?

作成されたカスタム Runbook はツールであり、同じものが不要になった場合は削除しても問題はありません。

### <a name="can-i-replicate-vms-to-another-subscription"></a>別のサブスクリプションに VM をレプリケートできますか?

はい、同じ Azure AD テナント内で別のサブスクリプションに Azure VM をレプリケートできます。

レプリケーション時に別のサブスクリプションを選択することで、[サブスクリプション全体で](https://azure.microsoft.com/blog/cross-subscription-dr)ディザスター リカバリーを構成します。

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>ゾーンにピン留めされた Azure VM を別のリージョンにレプリケートできますか?

はい、[ゾーンにピン留めされた VM](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) を別のリージョンにレプリケートすることができます。

### <a name="can-i-replicate-vms-in-a-region-that-has-zones-from-non-zone-to-zonal-configuration"></a>複数のゾーンがあるリージョン内で、非ゾーンからゾーン構成に VM をレプリケートできますか?

いいえ、これは現在サポートされていません。 回避策として、ASR を使用して、別のリージョンのゾーン構成に VM をレプリケートしてから、レプリケーションを無効にできます。 次に、そのリージョンから元のリージョンへのレプリケーションを再度有効にし、フェールオーバーのゾーン構成を選択します。

### <a name="can-i-exclude-disks"></a>ディスクを除外することはできますか?

はい、PowerShell を使用して保護時にディスクを除外できます。 詳細については、[レプリケーションからディスクを除外する方法](azure-to-azure-exclude-disks.md)に関するページを参照してください。

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>レプリケートされた VM に新しいディスクを追加して、それらのレプリケーションを有効にすることはできますか?

はい。Azure VM とマネージド ディスクの組み合わせに対しては、レプリケートされた VM に新しいディスクを追加し、それらのレプリケーションを有効にできます。 レプリケーションが有効になっている Azure VM に新しいディスクを追加すると、その VM のレプリケーション正常性に警告が表示されます。 その警告は、VM 上の 1 つまたは複数のディスクに保護を適用できることを示しています。 追加されたディスクのレプリケートを有効にすることができます。

- 追加されたディスクの保護を有効にすると、初回のレプリケーション後に警告は表示されなくなります。
- ディスクのレプリケートを有効にしない場合、この警告を無視できます。
- ディスクが追加され、レプリケーションが有効になっている VM をフェールオーバーする場合、レプリケーション ポイントがあります。 レプリケーション ポイントによって、回復できるディスクが示されます。

たとえば、ある VM にディスクが 1 つあるとき、新しいディスクを追加するとします。 ディスクを追加するより前にレプリケーション ポイントが作成されていた可能性があります。 このレプリケーション ポイントからは、それが "2 ディスク中 1" から構成されていることが示されます。

Site Recovery では、レプリケートされた VM からディスクを "ホット リムーブ" できません。 VM ディスクを削除した場合は、VM のレプリケーションを無効にしてから再度有効にする必要があります。

### <a name="how-often-can-i-replicate-to-azure"></a>どのくらいの頻度で Azure にレプリケートできますか?

Azure VM を別の Azure リージョンにレプリケートするときは、レプリケーションは継続的に行われます。 詳しくは、[Azure から Azure へのレプリケーション アーキテクチャ](./azure-to-azure-architecture.md#replication-process)に関する記事をご覧ください。

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-functionality-to-migrate-vms"></a>リージョン内で仮想マシンをレプリケートできますか? VM を移行するためにこの機能が必要です。

Azure 間のディスク回復ソリューションを使用し、リージョン内で VM をレプリケートすることはできません。

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>VM インスタンスを任意の Azure リージョンにレプリケートできますか?

Site Recovery を使用することで、同じ地理クラスター内の 2 つのリージョン間で VM をレプリケートして復旧できます。 地理クラスターは、データの遅延と主権を念頭に置きながら定義されます。 詳しくは、Site Recovery の[リージョンのサポート マトリックス](./azure-to-azure-support-matrix.md#region-support)をご覧ください。

### <a name="does-site-recovery-require-internet-connectivity"></a>Site Recovery にはインターネット接続が必要ですか?

いいえ、Site Recovery にはインターネット接続は必要ありません。 ただし、「[Azure VM ディザスター リカバリーのネットワークについて](./azure-to-azure-about-networking.md#outbound-connectivity-for-urls)」で説明されているように、Site Recovery の URL と IP 範囲にアクセスする必要があります。

### <a name="can-i-replicate-an-application-that-has-a-separate-resource-group-for-separate-tiers"></a>階層ごとにリソース グループが分かれているアプリケーションをレプリケートできますか?

はい、アプリケーションをレプリケートし、同時に、リソース グループ別にディザスター リカバリー構成を保持することは可能です。

ご利用のアプリケーションでたとえば、各層のアプリケーション、データベース、Web が別々のリソース グループに入っている場合、すべての層を保護するには[レプリケーション ウィザード](./azure-to-azure-how-to-enable-replication.md#enable-replication)を 3 回選択する必要があります。 Site Recovery によって、これら 3 つの層が 3 つの異なるリソース グループにレプリケートされます。

### <a name="can-i-move-storage-accounts-across-resource-groups"></a>リソース グループとの間でストレージ アカウントを移動できますか?

いいえ、これはサポートされていないシナリオです。 ただし、ストレージ アカウントを別のリソース グループに誤って移動し、元のリソース グループを削除した場合は、古いリソース グループと同じ名前で新しいリソース グループを作成し、そのストレージ アカウントをこのリソース グループに移動できます。

## <a name="replication-policy"></a>Replication policy

### <a name="what-is-a-replication-policy"></a>レプリケーション ポリシーとは何ですか?

レプリケーション ポリシーによって、復旧ポイントの保持履歴設定が定義されます。 このポリシーでは、アプリ整合性スナップショットの頻度も定義されます。 既定では、次のような既定の設定の新しいレプリケーション ポリシーが Azure Site Recovery で作成されます。

- 復旧ポイントの保持履歴は 24 時間。
- アプリ整合性スナップショットの頻度は 4 時間。

[レプリケーション設定についてはこちらをご覧ください](./azure-to-azure-tutorial-enable-replication.md#configure-replication-settings)。

### <a name="what-is-a-crash-consistent-recovery-point"></a>クラッシュ整合性復旧ポイントとは何ですか?

クラッシュ整合性復旧ポイントには、スナップショットの作成中にサーバーから電源コードが引き抜かれたときのディスク上のデータが含まれます。 クラッシュ整合性復旧ポイントには、スナップショットの作成時にメモリに入っていたものは一切含まれません。

現在、ほとんどのアプリケーションは、クラッシュ整合性のスナップショットから十分に復旧できます。 クラッシュ整合性復旧ポイントは通常、データベースのないオペレーティング システムや、ファイル サーバー、DHCP サーバー、プリント サーバーなどのアプリケーションにとっては十分です。

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>クラッシュ整合性復旧ポイントはどのくらいの頻度で生成されますか?

Site Recovery では、5 分ごとにクラッシュ整合性復旧ポイントが作成されます。

### <a name="what-is-an-application-consistent-recovery-point"></a>アプリケーション整合性復旧ポイントとは何ですか?

アプリケーション整合性復旧ポイントは、アプリケーション整合性スナップショットから作成されます。 アプリケーション整合性復旧ポイントでは、クラッシュ整合性スナップショットと同じデータがキャプチャされますが、さらに、メモリに入っていたデータと処理中のすべてのトランザクションもキャプチャされます。

これらの追加コンテンツのため、アプリケーション整合性スナップショットは最も複雑となり、時間がかかります。 アプリケーション整合性の復旧ポイントは、SQL Server などのデータベース オペレーティング システムで推奨されます。

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

### <a name="can-i-enable-replication-with-app-consistency-in-linux-servers"></a>Linux サーバーでアプリの整合性を使用してレプリケーションを有効にすることはできますか?

はい。 Linux オペレーティング システム用の Azure Site Recovery では、アプリの整合性を保つためのアプリケーション カスタム スクリプトがサポートされています。 アプリの整合性を保つときに、Azure Site Recovery モビリティ エージェントによって、プリオプションとポストオプションを含むカスタム スクリプトが使用されます。 [詳細情報](./site-recovery-faq.md#can-i-enable-replication-with-app-consistency-in-linux-servers)

## <a name="multi-vm-consistency"></a>マルチ VM 整合性

### <a name="what-is-multi-vm-consistency"></a>マルチ VM 整合性とは何ですか?

マルチ VM 整合性によって、レプリケートされたすべての仮想マシン間で復旧ポイントに整合性が確保されます。

Site Recovery には **マルチ VM 整合性**オプションがあります。このオプションにより、すべてのマシンのレプリケーション グループが作成されます。

仮想マシンをフェールオーバーすると、仮想マシン間でクラッシュ整合性復旧ポイントとアプリ整合性復旧ポイントが共有されます。

[マルチ VM 整合性の有効化](./azure-to-azure-tutorial-enable-replication.md#enable-replication-for-a-vm)に関するチュートリアルをご覧ください。

### <a name="can-i-fail-over-a-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>マルチ VM 整合性レプリケーション グループ内の単一の仮想マシンをフェールオーバーできますか?

**[マルチ VM 整合性]** オプションを選択すると、アプリケーションがグループ内のすべての仮想マシンに対して依存関係を持つことが示されます。 単一の仮想マシンのフェールオーバーは許可されません。

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>マルチ VM 整合性レプリケーション グループの一部として何個の仮想マシンをレプリケートできますか?

レプリケーション グループ内の 16 個の仮想マシンをまとめてレプリケートできます。

### <a name="when-should-i-enable-multi-vm-consistency"></a>どのようなときにマルチ VM 整合性を有効にする必要がありますか?

マルチ VM 整合性では CPU が集中的に消費されるので、これを有効にすると、ワークロードのパフォーマンスに影響する場合があります。 複数のマシンが同じワークロードを実行しているとき、複数のマシン間に整合性を持たせる必要がある場合にのみ、マルチ VM 整合性を使用してください。 たとえば、2 個の SQL Server インスタンスと 2 個の Web サーバーがアプリケーション内にある場合、SQL Server インスタンスに対してのみマルチ VM 整合性を有効にする必要があります。

### <a name="can-you-add-an-already-replicating-vm-to-a-replication-group"></a>既にレプリケートしている VM をレプリケーション グループに追加できますか?
レプリケーションを有効にしている間、新しいレプリケーション グループに VM を追加することができます。 また、レプリケーションを有効にしている間、既存のレプリケーション グループに VM を追加することもできます。 ただし、既にレプリケートしている VM は新しいレプリケーション グループにも、既存のレプリケーション グループにも追加することはできません。
 
## <a name="failover"></a>[フェールオーバー]


### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Azure VM のターゲット リージョンでは、容量はどのように確保されますか?

十分なインフラストラクチャ容量が確保されるよう、Site Recovery チームと Azure 容量管理チームが計画を立てます。 フェールオーバーを開始するとき、Site Recovery によって保護される VM インスタンスがターゲット リージョンにデプロイされるよう、チームも支援します。

### <a name="is-failover-automatic"></a>フェールオーバーは自動で行われますか。

フェールオーバーは自動では行われません。 ポータルで 1 回クリックするだけでフェールオーバーを開始できます。または [PowerShell](azure-to-azure-powershell.md) を使用してフェールオーバーをトリガーすることもできます。

### <a name="can-i-keep-a-public-ip-address-after-a-failover"></a>フェールオーバー後にパブリック IP アドレスを保持できますか?

運用アプリケーションのパブリック IP アドレスは、フェールオーバー後に保持できません。

フェールオーバー プロセスの一環としてワークロードを起動するとき、Azure パブリック IP リソースをワークロードに割り当てる必要があります。 Azure パブリック IP リソースは、ターゲット リージョンで使用できる必要があります。 Azure パブリック IP リソースは手動で割り当てるか、復旧計画で自動化できます。 「[フェールオーバー後にパブリック IP アドレスを設定する](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan)」方法を参照してください。

### <a name="can-i-keep-a-private-ip-address-during-a-failover"></a>フェールオーバー中、プライベート IP アドレスを保持できますか?

はい、プライベート IP アドレスを保持することができます。 既定では、Azure VM のディザスター リカバリーを有効にすると、Site Recovery によって、ソース リソースの設定に基づいてターゲット リソースが作成されます。 Azure VM が静的 IP アドレスで構成されている場合、Site Recovery では、使用中でなければ同じ IP アドレスをターゲット VM にプロビジョニングしようとします。
フェールオーバー時の IP アドレスの保持に関する[こちらの](site-recovery-retain-ip-azure-vm-failover.md)記事を参照してください。

### <a name="after-a-failover-why-is-the-server-assigned-a-new-ip-address"></a>フェールオーバー後、サーバーに新しい IP アドレスが割り当てられるのはなぜですか?

Site Recovery では、フェールオーバー時に IP アドレスの指定が試みられます。 別の仮想マシンがそのアドレスを取得している場合、Site Recovery では次の使用可能な IP アドレスがターゲットとして設定されます。

仮想ネットワークのネットワーク マッピングと IP アドレス指定の設定に関する[こちらの](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms)記事を参照してください。

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>**最新 (最低 RPO)** の復旧ポイントとは何ですか?

**[最新 (最低 RPO)]** オプションではまず、Site Recovery に送信されているすべてのデータが処理されます。 サービスでデータが処理された後、VM にフェールオーバーする前に、VM ごとに復旧ポイントが作成されます。 このオプションは、最も低い目標復旧ポイント (RPO) を提供します。 フェールオーバー後に作成された VM には、フェールオーバーのトリガー時に Site Recovery にレプリケートされたすべてのデータが含まれます。

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>**最新 (最低 RPO)** の復旧ポイントは、フェールオーバー RTO に影響しますか?

はい。 Site Recovery では、フェールオーバー前に保留中のすべてのデータが処理されるので、このオプションは他のオプションに比べて目標復旧時間 (RTO) が高くなります。

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>復旧ポイントの **[最後に処理があった時点]** オプションは何を意味しますか?

**[最後に処理があった時点]** オプションでは、計画内のすべての VM が、Site Recovery によって処理された最新の復旧ポイントにフェールオーバーされます。 特定の VM の最新の復旧ポイントを参照するには、VM 設定の **[Latest Recovery Points] (最新の回復ポイント)** を確認します。 このオプションを使用すると、未処理のデータの処理に時間がかからないため、RTO を低くできます。

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>プライマリ リージョンで予期しない停止が発生した場合はどうなりますか?

停止後、フェールオーバーをトリガーできます。 Site Recovery では、フェールオーバーを実行するためにプライマリ リージョンからの接続を必要としません。

### <a name="what-is-an-rto-of-a-vm-failover"></a>VM フェールオーバーの RTO とは何ですか?

Site Recovery の [RTO SLA は 2 時間](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/)です。 ただし、ほとんどの場合、Site Recovery によって数分以内に仮想マシンがフェールオーバーされます。 VM を起動するまでにかかった時間を示すフェールオーバー ジョブに移動することで、RTO を計算できます。 復旧計画の RTO については、次のセクションを参照してください。

## <a name="recovery-plans"></a>復旧計画

### <a name="what-is-a-recovery-plan"></a>復旧計画とは何ですか?

Site Recovery での復旧計画は、VM のフェールオーバーの復旧を調整します。 これは、復旧を常に正確で、繰り返し可能、さらに自動化されるようにするのに役立ちます。 復旧計画は次のニーズに対応します。

- 一緒にフェールオーバーする仮想マシンのグループを定義する
- アプリケーションが正確に動作するように、仮想マシン間の依存関係を定義する
- 仮想マシンのフェールオーバー以外のタスクを実行するため、カスタムの手動操作と共に復旧を自動化する

復旧計画の作成の詳細については[こちら](site-recovery-create-recovery-plans.md)を参照してください。

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>復旧計画ではシーケンス処理はどのように実行されますか?

復旧計画では、シーケンス処理を実行するために複数のグループを作成できます。 すべてのグループが一度にフェールオーバーされます。 同じグループに属する仮想マシンがまとめてフェールオーバーされ、続いて別のグループがフェールオーバーされます。 復旧計画を使用してアプリケーションをモデル化する方法については、「[復旧計画について](recovery-plan-overview.md#model-apps)」をご覧ください。

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>復旧計画の RTO を検索するにはどうしたらよいですか?

復旧計画の RTO を確認するには、復旧計画をテスト フェールオーバーして、**Site Recovery ジョブ**に移動します。
次の例では、**SAPTestRecoveryPlan** というジョブを見てください。 このジョブでは、すべての仮想マシンをフェールオーバーし、指定のアクションを行うために 8 分 59 秒かかりました。

![Site Recovery ジョブの一覧](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>復旧計画に Automation Runbook を追加できますか?

はい、Azure Automation Runbooks を復旧計画に組み込むことができます。 Azure Automation Runbook の追加については[こちら](site-recovery-runbook-automation.md)をご覧ください。

## <a name="reprotection-and-failback"></a>再保護とフェールバック

### <a name="i-failed-over-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>プライマリ リージョンからディザスター リカバリー リージョンにフェールオーバーしました。 DR リージョンの VM は自動的に保護されますか?

いいえ。 あるリージョンから別のリージョンに Azure VM を[フェールオーバー](./azure-to-azure-tutorial-failover-failback.md)すると、その VM は DR リージョン内で保護されていない状態で起動されます。 プライマリ リージョンに VM をフェールバックするには、セカンダリ リージョン内の VM を[再保護](./azure-to-azure-how-to-reprotect.md)する必要があります。

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>再保護時に、Site Recovery ではセカンダリ リージョンからプライマリ リージョンに完全なデータがレプリケートされますか?

状況によって異なります。 ソース リージョンの VM が存在している場合、ソース ディスクとターゲット ディスクの間の変更のみが同期されます。 Site Recovery では、ディスクを比較することで差分が計算された後、データが転送されます。 通常、このプロセスには数時間かかります。 再保護中に行われることについて詳しくは、[プライマリ リージョンに対してフェールオーバーされた Azure VM インスタンスを再保護する](./azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection)方法に関するページをご覧ください。

### <a name="how-much-time-does-it-take-to-fail-back"></a>フェールバックにはどのくらい時間がかかりますか?

再保護後のフェールオーバーには、プライマリ リージョンからセカンダリ リージョンにフェールオーバーするときと大体同じ時間がかかります。

## <a name="capacity"></a><a name="capacity"></a>容量

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Azure VM のターゲット リージョンでは、容量はどのように確保されますか?

十分なインフラストラクチャ容量が確保されるよう、Site Recovery チームと Azure 容量管理チームが計画を立てます。 フェールオーバーを開始するとき、Site Recovery によって保護される VM インスタンスがターゲット リージョンにデプロイされるよう、チームも支援します。

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
