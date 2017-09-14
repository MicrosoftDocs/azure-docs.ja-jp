
---
title: "Azure Backup の FAQ | Microsoft Docs"
description: "一般的な質問への回答: Recovery Services コンテナーを含む Azure Backup の機能、バックアップの対象、しくみ、暗号化、制限。 "
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "バックアップと障害復旧; バックアップ サービス"
ms.assetid: 1011bdd6-7a64-434f-abd7-2783436668d7
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/21/2017
ms.author: markgal;arunak;trinadhk;
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: a765aeffbaa7fd94aa0ef8e3885c03e5b5098c6e
ms.contentlocale: ja-jp
ms.lasthandoff: 08/09/2017

---
# <a name="questions-about-the-azure-backup-service"></a>Azure Backup サービスについての質問
この記事では、Azure Backup の構成要素が理解しやすいよう、よく寄せられる質問とその回答を記載しています。 一部の回答は、より詳しい情報を扱った記事にリンクされています。 Azure Backup について質問するには、**[コメント]** (右側) をクリックします。 コメントは、この記事の下部に表示されます。 コメントするには、Livefyre アカウントが必要です。 また、 [ディスカッション フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)でも、Azure Backup サービスに関する質問を投稿できます。

この記事の各セクションの内容をひととおり確認するには、右側の「**この記事の内容**」にあるリンクをご利用ください。


## <a name="recovery-services-vault"></a>Recovery Services コンテナー

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription-br"></a>各 Azure サブスクリプションで作成できるコンテナーの数に制限はありますか。 <br/>
はい。 2016 年 9 月の時点では、サブスクリプションあたり 25 個の Recovery Services またはバックアップ コンテナーを作成できます。 各サブスクリプションでサポートされている Azure Backup のリージョンごとに、最大 25 個の Recovery Services コンテナーを作成できます。 コンテナーがさらに必要な場合は、追加のサブスクリプションを作成してください。

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault-br"></a>各コンテナーに登録できるサーバーやマシンの数に制限はありますか。 <br/>
はい。コンテナーにつき最大 50 のコンピューターを登録できます。 Azure IaaS 仮想マシンの場合、コンテナーあたりの VM の上限は 200 です。 さらにコンピューターを登録する必要がある場合は、別のコンテナーを作成してください。

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-one-servers-data-from-another-server-when-restoring-databr"></a>組織で所有しているコンテナーが 1 つの場合、データを復元する際に特定のサーバーのデータを別のサーバーから分離するには、どうすればよいですか。<br/>
同じコンテナーに登録されたサーバーはどれもが、 *同じパスフレーズを使用する*他のサーバーによってバックアップされたデータを復元できます。 サーバーのバックアップ データを組織内の他のサーバーから分離する必要がある場合は、これらのサーバーごとに指定したパスフレーズを使用します。 たとえば、人事部門のサーバーで特定の暗号化パスフレーズを使用し、経理部門のサーバーで 2 番目、ストレージ サーバーで 3 番目の暗号化パスフレーズを使用することができます。

### <a name="can-i-migrate-my-backup-data-or-vault-between-subscriptions-br"></a>サブスクリプション間でバックアップ データまたはコンテナーを "移行" することはできますか。 <br/>
いいえ。 コンテナーはサブスクリプション レベルで作成されるため、作成後に別のサブスクリプションに再割り当てすることはできません。

### <a name="recovery-services-vaults-are-resource-manager-based-are-backup-vaults-classic-mode-still-supported-br"></a>Recovery Services コンテナーは Resource Manager に基づいています。 Backup コンテナー (クラシック モード) はまだサポートされていますか。 <br/>
既に[クラシック ポータル](https://manage.windowsazure.com)にあるバックアップ コンテナーは引き続きすべてサポートされます。 ただしクラシック ポータルを使って新しいバックアップ コンテナーをデプロイすることは今後できません。 将来的な機能強化は Recovery Services コンテナーに限定されるため、Microsoft では、すべてのデプロイに関して Recovery Services コンテナーの使用を推奨しています。 クラシック ポータルでバックアップ コンテナーを作成しようとすると、[Azure Portal](https://portal.azure.com) にリダイレクトされます。

### <a name="can-i-migrate-a-backup-vault-to-a-recovery-services-vault-br"></a>Backup コンテナーを Recovery Services コンテナーに移行することはできますか。 <br/>
残念ながら、Backup コンテナーの内容を Recovery Services コンテナーに移行することはできません。 この機能の追加に取り組んでいますが、現在は使用できません。

### <a name="i-backed-up-my-classic-vms-in-a-backup-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault"></a>クラシック VM をバックアップ コンテナーにバックアップしてあります。 クラシック モードから Resource Manager モードに VM を移行して、Recovery Services コンテナーで保護することはできますか。
バックアップ コンテナーのクラシック VM の回復ポイントは、VM をクラシックから Resource Manager モードに移行しても、Recovery Services コンテナーに自動的に移行されません。 VM バックアップを移行するには、次の手順に従います。

1. バックアップ コンテナーで、**[保護された項目]** タブに移動して VM を選択します。 [[保護の停止]](backup-azure-manage-vms-classic.md#stop-protecting-virtual-machines) をクリックします。 *[関連付けられたバックアップ データを削除します]* オプションは **オフ**のままにしておきます。
2. バックアップ/スナップショット拡張機能を VM から削除します。
3. 仮想マシンをクラシック モードから Resource Manager モードに移行します。 その仮想マシンに対応するストレージとネットワークの情報も Resource Manager モードに移行されていることを確認してください。
4. コンテナー ダッシュボードの上部にある **[バックアップ]** アクションを使用して、移行先の仮想マシンに Recovery Services コンテナーを作成し、バックアップを構成します。 Recovery Services コンテナーへの VM のバックアップについて詳しくは、[Recovery Services コンテナーを使用した Azure VM の保護](backup-azure-vms-first-look-arm.md)に関する記事を参照してください。

## <a name="azure-backup-agent"></a>Azure Backup エージェント
[Azure ファイル フォルダー バックアップに関する FAQ](backup-azure-file-folder-backup-faq.md) に一連の質問が掲載されています。

## <a name="azure-vm-backup"></a>Azure VM バックアップ
[Azure VM バックアップに関する FAQ](backup-azure-vm-backup-faq.md) に一連の質問が掲載されています。

## <a name="back-up-vmware-servers"></a>VMware サーバーのバックアップ

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>VMware vCenter サーバーを Azure にバックアップできますか。

はい。 Azure Backup Server を使用して、Azure に VMware vCenter および ESXi をバックアップすることができます。 サポートされている VMware バージョンについては、「[Azure Backup Server の保護マトリックス](backup-mabs-protection-matrix.md)」を参照してください。 詳細な手順については、「[Azure Backup Server を使用した VMware サーバーのバックアップ](backup-azure-backup-server-vmware.md)」を参照してください。


## <a name="azure-backup-server-and-system-center-data-protection-manager"></a>Azure Backup Server と System Center Data Protection Manager
### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server-br"></a>Azure Backup Server を使用して、物理サーバーのベア メタル回復 (BMR) バックアップを作成できますか。 <br/>
はい。

### <a name="can-i-register-my-dpm-server-to-multiple-vaults-br"></a>自分の DPM サーバーを複数のコンテナーに登録できますか。 <br/>
いいえ。 DPM または MABS サーバーは 1 つのコンテナーにしか登録できません。

### <a name="which-version-of-system-center-data-protection-manager-is-supported-br"></a>System Center Data Protection Manager は、どのバージョンがサポートされていますか。 <br/>
[最新版](http://aka.ms/azurebackup_agent)の Azure Backup エージェントを System Center Data Protection Manager (DPM) の最新の更新プログラムのロールアップ (UR) にインストールすることをお勧めします。 2016 年 8 月現在、更新プログラムのロールアップ 11 が最新の更新プログラムです。

### <a name="i-have-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-now-install-system-center-dpm-to-work-with-azure-backup-agent-to-protect-on-premises-applicationvm-workloads-to-azure-br"></a>ファイルとフォルダーを保護するために Azure Backup エージェントをインストールしました。 Azure に転送するオンプレミスのアプリケーション/VM ワークロードを保護するために、System Center DPM をインストールして Azure Backup エージェントと連携させることはできますか。 <br/>
System Center Data Protection Manager (DPM) と共に Azure Backup を使用するには、まず DPM をインストールしてから Azure Backup エージェントをインストールします。 この順序で Azure Backup のコンポーネントをインストールすることで、Azure Backup エージェントが DPM と連携します。 DPM をインストールする前に Azure Backup エージェントをインストールする方法は推奨されておらず、サポートもされていません。


## <a name="how-azure-backup-works"></a>Azure Backup の動作
### <a name="if-i-cancel-a-backup-job-once-it-has-started-is-the-transferred-backup-data-deleted-br"></a>バックアップを開始した後でバックアップ ジョブを取り消すと、転送されたバックアップ データは削除されますか。 <br/>
いいえ。 バックアップ ジョブを取り消す前にコンテナーに転送されたすべてのデータは、コンテナーに残ります。 Azure Backup では、チェックポイント メカニズムを使用して、バックアップ中に随時バックアップ データにチェックポイントを追加します。 バックアップ データにチェックポイントがあることで、次回のバックアップ処理でファイルの整合性を検証できます。 次のバックアップ ジョブは、これまでバックアップしたデータの増分になります。 増分バックアップでは、新しいデータまたは変更されたデータのみが転送され、帯域幅の使用状況が向上します。

Azure VM のバックアップ ジョブを取り消した場合、転送済みのデータは無視されます。 次のバックアップ ジョブでは、最後に成功したバックアップ ジョブから増分データが転送されます。

### <a name="are-there-limits-on-when-or-how-many-times-a-backup-job-can-be-scheduledbr"></a>バックアップ ジョブをスケジュール設定できる時刻や回数に制限はありますか。<br/>
はい。 Windows Server または Windows ワークステーションでは、1 日に最大 3 回バックアップ ジョブを実行できます。 System Center DPM では、1 日に最大 2 回バックアップ ジョブを実行できます。 IaaS VM では、1 日に 1 回バックアップ ジョブを実行できます。 Windows Server または Windows ワークステーションのスケジューリング ポリシーを使用して毎日または毎週のスケジュールを指定することができます。 System Center DPM を使用した場合、スケジュールには毎日、毎週、毎月、毎年を指定できます。

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-i-backed-upbr"></a>Recovery Services コンテナーに転送されたデータのサイズが、バックアップしたデータよりも小さいのはなぜでしょうか。<br/>
 Azure Backup エージェント、SCDPM、または Azure Backup Server からバックアップしたすべてのデータは、圧縮および暗号化されてから転送されます。 圧縮と暗号化が適用されると、バックアップ コンテナー内のデータは 30 ～ 40% 小さくなります。

## <a name="what-can-i-back-up"></a>バックアップできるデータについて
### <a name="which-operating-systems-do-azure-backup-support-br"></a>Azure Backup でサポートされるオペレーティング システムを教えてください。 <br/>
Azure Backup では、ファイルとフォルダーのほか、Azure Backup Server と System Center Data Protection Manager (DPM) を使用して保護されたワークロード アプリケーションのバックアップについて、次の一覧のオペレーティング システムがサポートされています。

| オペレーティング システム | プラットフォーム | SKU |
|:--- | --- |:--- |
| Windows 8 と最新 SP |64 ビット |Enterprise、Pro |
| Windows 7 と最新 SP |64 ビット |Ultimate、Enterprise、Professional、Home Premium、Home Basic、Starter |
| Windows 8.1 と最新 SP |64 ビット |Enterprise、Pro |
| Windows 10 |64 ビット |Enterprise、Pro、Home |
| Windows Server 2016 |64 ビット |Standard、Datacenter、Essentials |
| Windows Server 2012 R2 と最新 SP |64 ビット |Standard、Datacenter、Foundation |
| Windows Server 2012 と最新 SP |64 ビット |Datacenter、Foundation、Standard |
| Windows Storage Server 2016 と最新 SP |64 ビット |Standard、Workgroup | 
| Windows Storage Server 2012 R2 と最新 SP |64 ビット |Standard、Workgroup |
| Windows Storage Server 2012 と最新 SP |64 ビット |Standard、Workgroup |
| Windows Server 2012 R2 と最新 SP |64 ビット |Essential |
| Windows Server 2008 R2 SP1 |64 ビット |Standard、Enterprise、Datacenter、Foundation |
| Windows Server 2008 SP2 |64 ビット |Standard、Enterprise、Datacenter、Foundation |

**Azure VM バックアップのサポート:**

* **Linux**: Azure Backup は、 [Azure で承認されている一連のディストリビューション](../virtual-machines/linux/endorsed-distros.md) をサポートしています (Core OS Linux を除く)。  他の個人所有の Linux ディストリビューションも、仮想マシン上で VM エージェントが動作し、かつ Python がサポートされていれば使用できます。
* **Windows Server**: Windows Server 2008 R2 より前のバージョンはサポートされていません。


### <a name="is-there-a-limit-on-the-size-of-each-data-source-being-backed-up-br"></a>バックアップされる各データ ソースのサイズに制限はありますか。 <br/>
コンテナーにバックアップできるデータ量に制限はありません。 Azure Backup ではデータ ソースの最大サイズに制限がありますが、これらの上限は大きくなっています。 2015 年 8 月時点における、サポートされているオペレーティング システム別のデータ ソースの最大サイズは次のとおりです。

| No. | オペレーティング システム | データ ソースの最大サイズ |
|:---:|:--- |:--- |
| 1 |Windows Server 2012 またはそれ以降 |54,400 GB |
| 2 |Windows 8 以降 |54,400 GB |
| 3 |Windows Server 2008、Windows Server 2008 R2 |1,700 GB |
| 4 |Windows 7 |1,700 GB |

次の表では、各データ ソースのサイズが決定される方法について説明しています。

| データソース | 詳細 |
|:---:|:--- |
| ボリューム |サーバーまたはクライアント コンピューターの 1 つのボリュームからバックアップされるデータの量 |
| Hyper-V 仮想マシン |バックアップ対象の仮想マシンのすべての VHD のデータの合計 |
| Microsoft SQL Server データベース |バックアップ対象の 1 つの SQL データベースのサイズ |
| Microsoft SharePoint |バックアップ対象の SharePoint ファーム内のコンテンツと構成データベースの合計 |
| Microsoft Exchange |バックアップ対象の Exchange サーバー内のすべての Exchange データベースの合計 |
| BMR/システム状態 |バックアップ対象のコンピューターの BMR またはシステム状態の個々のコピー |

Azure VM バックアップでは、各データ ディスクのサイズを 1,023 GB 未満として、最大 16 台のデータ ディスクを個々の VM に割り当てることができます。 

## <a name="retention-policy-and-recovery-points"></a>保持ポリシーと復旧ポイント
### <a name="is-there-a-difference-between-the-retention-policy-for-dpm-and-windows-serverclient-that-is-on-windows-server-without-dpmbr"></a>DPM と Windows Server/クライアント (DPM がインストールされていない Windows Server) の保持ポリシーには違いがありますか。<br/>
いいえ。DPM と Windows Server/クライアントのどちらの場合でも、リテンション期間ポリシーに毎日、毎週、毎月、毎年を指定できます。

### <a name="can-i-configure-my-retention-policies-selectively--ie-configure-weekly-and-daily-but-not-yearly-and-monthlybr"></a>週単位と日単位を指定し、年単位と月単位を指定しないなど、保持ポリシーを選択的に構成することはできますか。<br/>
はい。Azure Backup の保有構造では、要件に合わせて保有ポリシーを柔軟に定義できます。

### <a name="can-i-schedule-a-backup-at-6pm-and-specify-retention-policies-at-a-different-timebr"></a>午後 6 時に "バックアップのスケジュールを設定" し、別の時刻に保持ポリシーを指定することはできますか。<br/>
いいえ。 保有ポリシーは、バックアップ ポイントにのみ適用できます。 次の画像では、午前 0 時と午後 6 時にバックアップが行われるように保持ポリシーが指定されています。 <br/>

![バックアップのスケジュールと保有期間](./media/backup-azure-backup-faq/Schedule.png)
<br/>

### <a name="if-a-backup-is-retained-for-a-long-duration-does-it-take-more-time-to-recover-an-older-data-point-br"></a>バックアップを長期にわたって保持した場合、データ ポイントが古いほど復元に時間がかかるのでしょうか。 <br/>
いいえ。最古のデータ ポイントも最新のデータ ポイントも復元に要する時間は同じです。 それぞれの回復ポイントは、完全なポイントと同じように動作します。

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storagebr"></a>それぞれの回復ポイントが完全なポイントと同じように機能する場合、それは課金対象のバックアップ ストレージの合計に影響するのでしょうか。<br/>
一般的に、回復ポイントのリテンション期間が長い製品では、バックアップ データが完全なポイントとして格納されます。 完全なポイントは、ストレージ効率は *悪く* なりますが、簡単かつ迅速に復元できます。 一方で増分コピーは、ストレージ効率は *良く* なりますが、データのチェーンを復元する必要があり、復旧時間に影響します。 Azure Backup のストレージ アーキテクチャを使用すると、高速に復元するためにデータの格納を最適化し、ストレージ コストを低く抑えることで、両方の長所を生かすことができます。 このようなデータ ストレージ手法を通じて、送受信の帯域幅が効率的に使用されています。 データ ストレージの量とデータ回復にかかる時間がどちらも最小限に抑えられます。 増分バックアップによる効率的なしくみの詳細については、[こちら](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/)を参照してください。

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-createdbr"></a>作成できる回復ポイント数に制限はありますか。<br/>
保護されているインスタンスごとに作成できる復旧ポイントは最大 9,999 個です。 保護されているインスタンスとは、データを Azure にバックアップするように構成されているコンピューター、サーバー (物理または仮想)、またはワークロードです。 詳細については、「[バックアップと保持](./backup-introduction-to-azure-backup.md#backup-and-retention)」および「[保護されているインスタンスとは](./backup-introduction-to-azure-backup.md#what-is-a-protected-instance)」をご覧ください。

### <a name="how-many-recoveries-can-i-perform-on-the-data-that-is-backed-up-to-azurebr"></a>Azure にバックアップされたデータで実行できる回復の回数は何回ですか。<br/>
Azure Backup からの回復の数に制限はありません。

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure-br"></a>データを復元している間に発生する Azure からのエグレス トラフィックには料金が発生するのでしょうか。 <br/>
いいえ。 回復は無料ですので、送信トラフィックに対しては課金されません。

## <a name="azure-backup-encryption"></a>Azure Backup の暗号化
### <a name="is-the-data-sent-to-azure-encrypted-br"></a>Azure に送信されるデータは暗号化されますか。 <br/>
はい。 データは、オンプレミスのサーバー / クライアント / SCDPM コンピューターで AES256 を使用して暗号化され、セキュリティで保護された HTTPS リンク経由で送信されます。

### <a name="is-the-backup-data-on-azure-encrypted-as-wellbr"></a>Azure 上のバックアップ データも暗号化されますか。<br/>
はい。 Azure に送信されるデータは、暗号化された状態で保存されます。 マイクロソフトは、どの時点でもバックアップ データの暗号化を解除しません。 Azure VM をバックアップする場合、Azure Backup では仮想マシンの暗号化が使用されます。 たとえば、Azure Disk Encryption や他の暗号化テクノロジを使用して VM が暗号化されている場合、Azure Backup はその暗号化を使用してデータをセキュリティ保護します。

### <a name="what-is-the-minimum-length-of-encryption-key-used-to-encrypt-backup-data-br"></a>バックアップ データの暗号化に使用される暗号化キーの最小の長さはどれくらいですか。 <br/>
Azure Backup エージェントを使用する場合、暗号化キーは 16 文字以上である必要があります。 Azure VM に関しては、Azure Key Vault で使用されるキーの長さに制限はありません。 

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-or-can-microsoft-recover-the-data-br"></a>暗号化キーを紛失した場合はどうなりますか? 自分でデータを回復できますか。または Microsoft でデータを回復してもらうことはできますか。 <br/>
バックアップ データの暗号化に使用されるキーは、お客様のオンプレミスにのみ存在します。 マイクロソフトは Azure にコピーを保持していませんし、キーにもアクセスできません。 お客様がキーを紛失した場合、マイクロソフトはバックアップ データを回復できません。

