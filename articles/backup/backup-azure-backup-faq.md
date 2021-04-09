---
title: よく寄せられる質問に対する回答
description: '一般的な質問への回答:Recovery Services コンテナーを含む Azure Backup の機能、バックアップの対象、しくみ、暗号化、制限。 '
ms.topic: conceptual
ms.date: 07/07/2019
ms.openlocfilehash: ac58cee66aa2a89efb7194a051801b068628d3bc
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467631"
---
# <a name="azure-backup---frequently-asked-questions"></a>Azure Backup - よく寄せられる質問

この記事では、Azure Backup サービスについてよく寄せられる質問への回答を示します。

## <a name="recovery-services-vault"></a>Recovery Services コンテナー

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription"></a>各 Azure サブスクリプションで作成できるコンテナーの数に制限はありますか。

はい。 サブスクリプションあたり、Azure Backup のサポートされているリージョンごとに最大 500 の Recovery Services コンテナーを作成できます。 コンテナーがさらに必要な場合は、追加のサブスクリプションを作成してください。

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault"></a>各コンテナーに登録できるサーバーやマシンの数に制限はありますか。

コンテナーあたり最大 1000 の Azure 仮想マシンを登録できます。 Microsoft Azure Backup エージェントを使用している場合は、コンテナーあたり最大 50 の MARS エージェントを登録できます。 1 つのコンテナーには、50 の MABS サーバーまたは DPM サーバーを登録できます。

### <a name="how-many-datasourcesitems-can-be-protected-in-a-vault"></a>コンテナーで保護できるデータソース/項目の数はいくつですか。

すべてのワークロード (IaaS VM、SQL、AFS など) にわたる最大 2000 のデータソース/項目をコンテナーで保護することができます。
たとえば、既に 500 台の VM と 400 個の Azure Files 共有がコンテナーで保護されている場合、そこで保護できる SQL データベースの数は最大 1100 個だけです。

### <a name="how-many-policies-can-i-create-per-vault"></a>コンテナーごとに作成できるポリシーはいくつですか。

作成できるポリシーはコンテナーあたり最大 200 に限られます。

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-data-from-different-servers-in-the-vault-when-restoring-data"></a>組織で所有しているコンテナーが 1 つの場合、データを復元する際にコンテナー内の他のサーバーのデータを分離するには、どうすればよいですか。

まとめて復旧するサーバー データには、バックアップをセットアップするときに、同じパスフレーズを使用してください。 1 つ以上の特定のサーバーを分離して復旧する場合は、該当するサーバー専用のパスフレーズを使用します。 たとえば、人事部門のサーバーで特定の暗号化パスフレーズを使用し、経理部門のサーバーで 2 番目、ストレージ サーバーで 3 番目の暗号化パスフレーズを使用することができます。

### <a name="can-i-move-my-vault-between-subscriptions"></a>サブスクリプション間でコンテナーを移動することはできますか。

はい。 Recovery Services コンテナーの移動については、こちらの[記事](backup-azure-move-recovery-services-vault.md)をご覧ください

### <a name="can-i-move-backup-data-to-another-vault"></a>バックアップ データを別のコンテナーに移動することはできますか。

いいえ。 コンテナーに保存されているバックアップ データを別のコンテナーに移行することはできません。

### <a name="can-i-change-the-storage-redundancy-setting-after-a-backup"></a>バックアップ後、ストレージの冗長設定を変更できますか。

既定では、ストレージ レプリケーションの種類は geo 冗長ストレージ (GRS) に設定されています。 バックアップを構成すると、変更オプションは無効になり、変更できなくなります。

![ストレージ レプリケーションの種類](./media/backup-azure-backup-faq/storage-replication-type.png)

既にバックアップを構成していて、GRS から LRS に移行する必要がある場合は、[バックアップの構成後に GRS から LRS に変更する方法](backup-create-rs-vault.md#how-to-change-from-grs-to-lrs-after-configuring-backup)に関する記事を参照してください。

### <a name="can-i-do-an-item-level-restore-ilr-for-vms-backed-up-to-a-recovery-services-vault"></a>Recovery Services コンテナーにバックアップした VM でアイテム レベルの復元 (ILR) を行うことはできますか。

- ILR は、Azure VM バックアップによってバックアップされている Azure VM に対してサポートされています。 詳しくは、[こちらの記事](backup-azure-restore-files-from-vm.md)をご覧ください
- ILR は、Azure Backup Server (MABS) または System Center DPM によってバックアップされているオンプレミスの VM のオンライン復旧ポイントに対してはサポートされていません。

### <a name="how-can-i-move-data-from-the-recovery-services-vault-to-on-premises"></a>Recovery Services コンテナーからオンプレミスにデータを移動するにはどうすればよいですか。

Data Box を使用して Recovery Services コンテナーからオンプレミスにデータを直接エクスポートすることはサポートされていません。 データはストレージ アカウントに復元する必要があります。その後、[Data Box](../databox/data-box-overview.md) または [Import/Export](../import-export/storage-import-export-service.md) を使ってオンプレミスに移動できます。

### <a name="what-is-the-difference-between-a-geo-redundant-storage-grs-vault-with-and-without-the-cross-region-restore-crr-capability-enabled"></a>geo 冗長ストレージ (GRS) コンテナーで、リージョンをまたがる復元 (CRR) 機能が有効になっている場合とそうでない場合では、どのような違いがありますか。

[CRR](azure-backup-glossary.md#cross-region-restore-crr) 機能が有効になっていない [GRS](azure-backup-glossary.md#grs) コンテナーの場合、Azure によってプライマリ リージョンでの障害が宣言されるまで、セカンダリ リージョンのデータにはアクセスできません。 このような場合、セカンダリ リージョンから復元が行われます。 CRR が有効になっている場合、プライマリ リージョンが稼働している場合でも、セカンダリ リージョンでの復元をトリガーできます。

### <a name="can-i-move-a-subscription-that-contains-a-vault-to-a-different-azure-active-directory"></a>コンテナーが含まれるサブスクリプションを別の Azure Active Directory に移動できますか。

はい。 (コンテナーが含まれる) サブスクリプションを別の Azure Active Directory (AD) に移動するには、[サブスクリプションを別のディレクトリに移動する](../role-based-access-control/transfer-subscription.md)方法に関するページを参照してください。

>[!IMPORTANT]
>サブスクリプションの移動後、必ず次のアクションを実行してください。<ul><li>ロールベースのアクセス制御とカスタム役割は移動できません。 新しい Azure AD で、アクセス許可とロールを再作成する必要があります。</li><li>コンテナーのマネージド ID (MI) は無効にし、再び有効にすることで再作成する必要があります。 また、MI アクセス許可を昇格させ、再作成する必要があります。</li><li>[プライベート エンドポイント](private-endpoints.md#before-you-start)や[カスタマー マネージド キー](encryption-at-rest-with-cmk.md#before-you-start)など、MI を活用する機能がコンテナーで使用されている場合、その機能を再構成する必要があります。</li></ul>

## <a name="azure-backup-agent"></a>Azure Backup エージェント

### <a name="where-can-i-find-common-questions-about-the-azure-backup-agent-for-azure-vm-backup"></a>Azure VM のバックアップに使用される Azure Backup エージェントについてよく寄せられる質問は、どこで確認できますか。

- Azure VM 上で実行されるエージェントについては、この [FAQ](backup-azure-vm-backup-faq.yml) をご覧ください。
- Azure ファイル フォルダーのバックアップに使用されるエージェントについては、こちらの [FAQ](backup-azure-file-folder-backup-faq.md) をご覧ください。

## <a name="general-backup"></a>一般的なバックアップ

### <a name="are-there-limits-on-backup-scheduling"></a>バックアップのスケジュール設定に制限はありますか。

はい。

- Windows Server または Windows マシンをバックアップできるのは、1 日 3 回までです。 スケジューリング ポリシーは、毎日または毎週のスケジュールに設定できます。
- DPM をバックアップできるのは、1 日 2 回までです。 スケジューリング ポリシーは、毎日、毎週、毎月、および毎年のスケジュールに設定できます。
- Azure VM のバックアップは 1 日 1 回に限られます。

### <a name="what-operating-systems-are-supported-for-backup"></a>バックアップでサポートされているオペレーティング システムはどれですか。

ファイルとフォルダーのほか、Azure Backup Server と DPM を使用して保護されたアプリのバックアップについて、Azure Backup では次のオペレーティング システムをサポートしています。

**OS** | **SKU** | **詳細**
--- | --- | ---
ワークステーション | |
Windows 10 64 ビット | Enterprise、Pro、Home | マシンで最新の Service Pack および更新プログラムが実行されている必要があります。
Windows 8.1 64 ビット | Enterprise、Pro | マシンで最新の Service Pack および更新プログラムが実行されている必要があります。
Windows 8 64 ビット | Enterprise、Pro | マシンで最新の Service Pack および更新プログラムが実行されている必要があります。
Windows 7 64 ビット | Ultimate、Enterprise、Professional、Home Premium、Home Basic、Starter | マシンで最新の Service Pack および更新プログラムが実行されている必要があります。
サーバー | |
Windows Server 2019 64 ビット | Standard、Datacenter、Essentials | 最新の Service Pack/更新プログラムが適用されていること。
Windows Server 2016 64 ビット | Standard、Datacenter、Essentials | 最新の Service Pack/更新プログラムが適用されていること。
Windows Server 2012 R2 64 ビット | Standard、Datacenter、Foundation | 最新の Service Pack/更新プログラムが適用されていること。
Windows Server 2012 64 ビット | Datacenter、Foundation、Standard | 最新の Service Pack/更新プログラムが適用されていること。
Windows Storage Server 2016 64 ビット | Standard、Workgroup | 最新の Service Pack/更新プログラムが適用されていること。
Windows Storage Server 2012 R2 64 ビット | Standard、Workgroup、Essential | 最新の Service Pack/更新プログラムが適用されていること。
Windows Storage Server 2012 64 ビット | Standard、Workgroup | 最新の Service Pack/更新プログラムが適用されていること。
Windows Server 2008 R2 SP1 64 ビット | Standard、Enterprise、Datacenter、Foundation | 最新の更新プログラムが適用されていること。
Windows Server 2008 64 ビット | Standard、Enterprise、Datacenter | 最新の更新プログラムが適用されていること。

Azure Backup は 32 ビットのオペレーティング システムをサポートしていません。

Azure VM Linux のバックアップについては、Azure Backup は [Azure で承認されている一連のディストリビューション](../virtual-machines/linux/endorsed-distros.md)をサポートしています。ただし、Core OS Linux と 32 ビットオペレーティング システムは除きます。 他の個人所有の Linux ディストリビューションは、VM 上で VM エージェントが動作し、かつ Python がサポートされていれば使用できます。

### <a name="are-there-size-limits-for-data-backup"></a>データのバックアップにサイズ制限はありますか。

次のサイズ制限が適用されます。

OS/マシン | データ ソースのサイズ制限
--- | ---
Windows 8 以降 | 54,400 GB
Windows 7 |1,700 GB
Windows Server 2012 またはそれ以降 | 54,400 GB
Windows Server 2008、Windows Server 2008 R2 | 1,700 GB
Azure VM | [Azure VM バックアップのサポート マトリックス](./backup-support-matrix-iaas.md#vm-storage-support)を確認してください

### <a name="how-is-the-data-source-size-determined"></a>データ ソースのサイズはどのように決定されますか。

次の表では、各データ ソースのサイズが決定される方法について説明しています。

**データ ソース** | **詳細**
--- | ---
ボリューム |バックアップ対象の VM の 1 つのボリュームからバックアップされるデータの量。
SQL Server データベース |バックアップされる単一データベースのサイズ。
SharePoint | バックアップ対象の SharePoint ファーム内のコンテンツと構成データベースの合計。
Exchange |バックアップ対象の Exchange サーバー内のすべての Exchange データベースの合計。
BMR/システム状態 |バックアップ対象のコンピューターの BMR またはシステム状態の個々のコピー。

### <a name="is-there-a-limit-on-the-amount-of-data-backed-up-using-a-recovery-services-vault"></a>Recovery Services コンテナーを使用してバックアップできるデータ量に制限はありますか。

Recovery Services コンテナーを使用してバックアップできる合計データ量に制限はありません。 個々のデータ ソース (Azure VM 以外) は、最大 54,400 GB のサイズにすることができます。 制限の詳細については、[サポート マトリックスのコンテナーの制限に関するセクション](./backup-support-matrix.md#vault-support)を参照してください。

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-selected-for-backup"></a>Recovery Services コンテナーに転送されたデータのサイズが、バックアップ対象として選択したデータよりも小さいのはなぜでしょうか。

Azure Backup エージェント、DPM、または Azure Backup Server からバックアップしたデータは、圧縮および暗号化されてから転送されます。 圧縮と暗号化が適用されると、コンテナー内のデータのサイズは 30 から 40% 小さくなります。

### <a name="can-i-delete-individual-files-from-a-recovery-point-in-the-vault"></a>コンテナー内の復旧ポイントから個々のファイルを削除できますか。

いいえ。Azure Backup では、保存されているバックアップからの個々の項目の削除または消去はサポートしていません。

### <a name="if-i-cancel-a-backup-job-after-it-starts-is-the-transferred-backup-data-deleted"></a>バックアップを開始した後でバックアップ ジョブを取り消すと、転送されたバックアップ データは削除されますか。

いいえ。 バックアップ ジョブを取り消す前にコンテナーに転送されたすべてのデータは、コンテナー内に残ります。

- Azure Backup では、チェックポイント メカニズムを使用して、バックアップ中に随時バックアップ データにチェックポイントを追加します。
- バックアップ データにチェックポイントがあることで、次回のバックアップ処理でファイルの整合性を検証できます。
- 次のバックアップ ジョブは、これまでバックアップしたデータの増分になります。 増分バックアップでは、新しいデータまたは変更されたデータのみが転送され、帯域幅の使用状況が向上します。

Azure VM のバックアップ ジョブを取り消した場合、転送済みのデータは無視されます。 次のバックアップ ジョブでは、最後に成功したバックアップ ジョブから増分データが転送されます。

## <a name="retention-and-recovery"></a>保有期間と復元

### <a name="are-the-retention-policies-for-dpm-and-windows-machines-without-dpm-the-same"></a>DPM での保有ポリシーと DPM を使用しない Windows マシンでの保有ポリシーは同じですか。

はい。どちらとも、保有ポリシーとして毎日、毎週、毎月、毎年を指定できます。

### <a name="can-i-customize-retention-policies"></a>保有ポリシーをカスタマイズできますか。

はい、ポリシーをカスタマイズして使用できます。 たとえば、保有期間の要件を毎週および毎日として構成し、毎年や毎月の保有期間の要件を構成しないこともできます。

### <a name="can-i-use-different-times-for-backup-scheduling-and-retention-policies"></a>バックアップ スケジュールと保有ポリシーを別々の期間に使用できますか。

いいえ。 保有ポリシーは、バックアップ ポイントにのみ適用できます。 一例として、この図には午前 12 時と午後 6 時に作成されたバックアップの保有ポリシーが示されています。

![バックアップのスケジュールと保有期間](./media/backup-azure-backup-faq/Schedule.png)

### <a name="if-a-backup-is-kept-for-a-long-time-does-it-take-more-time-to-recover-an-older-data-point"></a>バックアップを長期にわたって保持した場合、データ ポイントが古いほど復元に時間がかかるのでしょうか。

いいえ。 最古のデータ ポイントも最新のデータ ポイントも復元に要する時間は同じです。 それぞれの回復ポイントは、完全なポイントと同じように動作します。

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storage"></a>それぞれの回復ポイントが完全なポイントと同じように機能する場合、それは課金対象のバックアップ ストレージの合計に影響するのでしょうか。

一般的に、回復ポイントのリテンション期間が長い製品では、バックアップ データが完全なポイントとして格納されます。

- 完全なポイントは、ストレージ効率は *悪く* なりますが、簡単かつ迅速に復元できます。
- 一方、増分コピーはストレージ効率が *向上* しますが、データのチェーンを復元する必要があり、復旧時間に影響します。

Azure Backup のストレージ アーキテクチャを使用すると、高速に復元するためにデータの格納を最適化し、ストレージ コストを低く抑えることで、両方の長所を生かすことができます。 これにより、送受信の帯域幅が効率的に使用されるようになります。 データ ストレージの量とデータ回復にかかる時間がどちらも最小限に抑えられます。 詳細については、[増分バックアップ](backup-architecture.md#backup-types)を参照してください。

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-created"></a>作成できる回復ポイント数に制限はありますか。

保護されているインスタンスごとに作成できる復旧ポイントは最大 9,999 個です。 保護されたインスタンスとは、Azure へのバックアップを行うコンピューター、サーバー (物理または仮想)、またはワークロードです。

- 詳細については、[バックアップと保有期間](./backup-support-matrix.md)を参照してください。

### <a name="how-many-times-can-i-recover-data-thats-backed-up-to-azure"></a>Azure にバックアップされたデータを回復できる回数に制限はありますか。

Azure Backup からの回復の数に制限はありません。

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure"></a>データを復元している間に発生する Azure からのエグレス トラフィックには料金が発生するのでしょうか。

いいえ。 データ回復は無料ですので、送信トラフィックに対しては課金されません。

### <a name="what-happens-when-i-change-my-backup-policy"></a>バックアップ ポリシーを変更した場合どうなりますか。

新しいポリシーが適用されると、新しいポリシーのスケジュールとリテンション期間が適用されます。

- リテンション期間が延長された場合、既にある復旧ポイントは、新しいポリシーに従って保存するようにマーキングされます。
- リテンション期間が短縮された場合、次回のクリーンアップ ジョブで排除対象としてマーキングされて、その後削除されます。

### <a name="how-long-is-data-retained-when-stopping-backups-but-selecting-the-option-to-retain-backup-data"></a>バックアップを停止したもののバックアップ データを保持するオプションを選択した場合、データはどのくらいの期間にわたって保持されますか。

バックアップが停止し、データが保持されている場合、データ排除用の既存のポリシー ルールが中断し、管理者が削除するまでデータは無期限に保持されます。

## <a name="encryption"></a>暗号化

### <a name="is-the-data-sent-to-azure-encrypted"></a>Azure に送信されるデータは暗号化されますか。

はい。 データはオンプレミスのマシン上で AES256 を使用して暗号化されます。 データは、セキュリティで保護された HTTPS リンク上で送信されます。 クラウド内で送信されたデータは、ストレージと復旧サービス間の HTTPS リンクのみで保護されています。 iSCSI プロトコルは、復旧サービスとユーザー マシン間で送信されるデータをセキュリティで保護します。 iSCSI チャネルの保護には、セキュリティで保護されたトンネリングが使用されます。

### <a name="is-the-backup-data-on-azure-encrypted-as-well"></a>Azure 上のバックアップ データも暗号化されますか。

はい。 Azure 内ではデータが暗号化されて保存されます。

- オンプレミスのバックアップでは、Azure にバックアップする際に指定するパスフレーズを使用して保存時の暗号化が行われます。
- Azure VM の場合、データは Storage Service Encryption (SSE) を使用して暗号化された上で保存されます。

マイクロソフトは、どの時点でもバックアップ データの暗号化を解除しません。

### <a name="what-is-the-minimum-length-of-the-encryption-key-used-to-encrypt-backup-data"></a>バックアップ データの暗号化に使用される暗号化キーの最小の長さはどれくらいですか。

Microsoft Azure Recovery Services (MARS) エージェントによって使用される暗号化キーは、16 文字以上のパス フレーズから派生します。 Azure VM の場合、Azure Key Vault で使用されるキーの長さに制限はありません。

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-can-microsoft-recover-the-data"></a>暗号化キーを紛失した場合はどうなりますか? データを回復できますか。 マイクロソフトがデータを回復することはできますか。

バックアップ データの暗号化に使用されるキーは、ユーザーのサイトにだけ存在します。 マイクロソフトは Azure にコピーを保持していませんし、キーにもアクセスできません。 ユーザーがキーを紛失した場合、マイクロソフトはバックアップ データを回復できません。

## <a name="next-steps"></a>次のステップ

その他のよく寄せられる質問をお読みください。

- Azure VM バックアップについて[よく寄せられる質問](backup-azure-vm-backup-faq.yml)
- Azure Backup エージェントについて[よく寄せられる質問](backup-azure-file-folder-backup-faq.md)
