
---
title: "Azure Backup の FAQ | Microsoft Docs"
description: "一般的な質問への回答: サービスの動作、Azure Backup エージェント、Recovery Services コンテナー、バックアップとリテンション期間の制限。"
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
ms.topic: get-started-article
ms.date: 3/10/2017
ms.author: markgal;giridham;arunak;trinadhk;
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 87d500d886feb2dbd61d15d5a980ab2c4018c2f2
ms.lasthandoff: 04/03/2017


---
# <a name="questions-about-the-azure-backup-service"></a>Azure Backup サービスについての質問
この記事では、Azure Backup の構成要素が理解しやすいよう、よく寄せられる質問とその回答をセクションごとに記載しています。 一部の回答は、より詳しい情報を扱った記事にリンクされています。 Azure Backup について質問するには、**[コメント]** (右側) をクリックします。 コメントは、この記事の下部に表示されます。 コメントするには、Livefyre アカウントが必要です。 また、 [ディスカッション フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)でも、Azure Backup サービスに関する質問を投稿できます。

この記事の各セクションの内容をひととおり確認するには、右側の「**この記事の内容**」にあるリンクをご利用ください。


## <a name="recovery-services-vault"></a>Recovery Services コンテナー

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription-br"></a>各 Azure サブスクリプションで作成できるコンテナーの数に制限はありますか。 <br/>
はい。 2016 年 9 月の時点では、サブスクリプションあたり 25 個の Recovery Services またはバックアップ コンテナーを作成できます。 各サブスクリプションでサポートされている Azure Backup のリージョンごとに、最大 25 個の Recovery Services コンテナーを作成できます。 コンテナーがさらに必要な場合は、追加のサブスクリプションを作成してください。

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault-br"></a>各コンテナーに登録できるサーバーやマシンの数に制限はありますか。 <br/>
はい。コンテナーにつき最大 50 のコンピューターを登録できます。 Azure IaaS 仮想マシンの場合、コンテナーあたりの VM の上限は 200 です。 さらにコンピューターを登録する必要がある場合は、別のコンテナーを作成してください。

### <a name="how-do-i-register-my-server-to-another-datacenterbr"></a>別のデータ センターにサーバーを登録する方法を教えてください。<br/>
バックアップ データは登録されているコンテナーのデータセンターに送信されます。 データセンターを変更する最も簡単な方法は、エージェントをアンインストールしてから再インストールし、変更先のデータセンターに属している新しいコンテナーに登録する方法です。

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-one-servers-data-from-another-server-when-restoring-databr"></a>組織で所有しているコンテナーが 1 つの場合、データを復元する際に特定のサーバーのデータを別のサーバーから分離するには、どうすればよいですか。<br/>
同じコンテナーに登録されたサーバーはどれもが、 *同じパスフレーズを使用する*他のサーバーによってバックアップされたデータを復元できます。 サーバーのバックアップ データを組織内の他のサーバーから分離する必要がある場合は、これらのサーバーごとに指定したパスフレーズを使用します。 たとえば、人事部門のサーバーで特定の暗号化パスフレーズを使用し、経理部門のサーバーで 2 番目、ストレージ サーバーで 3 番目の暗号化パスフレーズを使用することができます。

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder-br"></a>キャッシュ フォルダーの最小サイズ要件を教えてください。 <br/>
キャッシュ フォルダーのサイズによって、バックアップするデータ量が決まります。 キャッシュ フォルダーは、データの格納に必要なスペースの 5% に設定する必要があります。

### <a name="can-i-migrate-my-backup-data-or-vault-between-subscriptions-br"></a>サブスクリプション間でバックアップ データまたはコンテナーを "移行" することはできますか。 <br/>
いいえ。 コンテナーはサブスクリプション レベルで作成されるため、作成後に別のサブスクリプションに再割り当てすることはできません。

### <a name="recovery-services-vaults-are-resource-manager-based-are-backup-vaults-classic-mode-still-supported-br"></a>Recovery Services コンテナーは Resource Manager に基づいています。 Backup コンテナー (クラシック モード) はまだサポートされていますか。 <br/>
既に[クラシック ポータル](https://manage.windowsazure.com)にあるバックアップ コンテナーは引き続きすべてサポートされます。 ただしクラシック ポータルを使って新しいバックアップ コンテナーをデプロイすることは今後できません。 将来的な機能強化は Recovery Services コンテナーに限定されるため、Microsoft では、すべてのデプロイに関して Recovery Services コンテナーの使用を推奨しています。 クラシック ポータルでバックアップ コンテナーを作成しようとすると、[Azure Portal](https://portal.azure.com) にリダイレクトされます。

### <a name="can-i-migrate-a-backup-vault-to-a-recovery-services-vault-br"></a>Backup コンテナーを Recovery Services コンテナーに移行することはできますか。 <br/>
残念ながら、Backup コンテナーの内容を Recovery Services コンテナーに移行することはできません。 この機能の追加に取り組んでいますが、現在は使用できません。

### <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>Recovery Services コンテナーでサポートされるのはクラシック VM と Resource Manager ベースの VM のどちらですか。 <br/>
Recovery Services コンテナーでは両方のモデルがサポートされています。  (クラシック ポータルで作成された) クラシック VM または (Azure Portal で作成された) Resource Manager VM を、Recovery Services コンテナーにバックアップできます。

### <a name="i-backed-up-my-classic-vms-in-a-backup-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault"></a>クラシック VM をバックアップ コンテナーにバックアップしてあります。 クラシック モードから Resource Manager モードに VM を移行して、Recovery Services コンテナーで保護することはできますか。
バックアップ コンテナーのクラシック VM の回復ポイントは、VM をクラシックから Resource Manager モードに移行しても、Recovery Services コンテナーに自動的に移行されません。 VM バックアップを移行するには、次の手順に従います。

1. バックアップ コンテナーで、**[保護された項目]** タブに移動して VM を選択します。 [[保護の停止]](backup-azure-manage-vms-classic.md#stop-protecting-virtual-machines) をクリックします。 *[関連付けられたバックアップ データを削除します]* オプションは **オフ**のままにしておきます。
2. 仮想マシンをクラシック モードから Resource Manager モードに移行します。 その仮想マシンに対応するストレージとネットワークの情報も Resource Manager モードに移行されていることを確認してください。
3. コンテナー ダッシュボードの上部にある **[バックアップ]** アクションを使用して、移行先の仮想マシンに Recovery Services コンテナーを作成し、バックアップを構成します。 Recovery Services コンテナーへの VM のバックアップについて詳しくは、[Recovery Services コンテナーを使用した Azure VM の保護](backup-azure-vms-first-look-arm.md)に関する記事を参照してください。



## <a name="azure-backup-agent"></a>Azure Backup エージェント

### <a name="where-can-i-download-the-latest-azure-backup-agent-br"></a>最新の Azure Backup エージェントはどこでダウンロードできますか。 <br/>
Windows Server、System Center DPM、または Windows クライアントをバックアップするための最新のエージェントは、 [こちら](http://aka.ms/azurebackup_agent)からダウンロードできます。 仮想マシンをバックアップする場合は、VM エージェントを使用してください (適切な拡張機能が自動的にインストールされます)。 VM エージェントは、Azure ギャラリーから作成された仮想マシン内に既に存在しています。

### <a name="when-configuring-the-azure-backup-agent-i-am-prompted-to-enter-the-vault-credentials-do-vault-credentials-expire"></a>Azure Backup エージェントを構成するときに、コンテナー資格情報の入力が求められます。 コンテナー資格情報には有効期限がありますか。
はい。コンテナー資格情報は 48 時間後に有効期限が切れます。 ファイルの有効期限が切れた場合は、Azure Portal にログインし、コンテナーからコンテナー資格情報ファイルをダウンロードしてください。

### <a name="what-happens-if-i-rename-a-windows-server-that-is-backing-up-data-to-azurebr"></a>Azure にデータをバックアップしている Windows サーバーの名前を変更するとどうなりますか。<br/>
サーバーの名前を変更すると、現在構成されているすべてのバックアップが停止します。
バックアップ コンテナーにサーバーの新しい名前を登録します。 コンテナーに新しい名前を登録すると、最初のバックアップ操作は "*完全*" バックアップになります。 以前のサーバー名でコンテナーにバックアップしたデータを回復する必要がある場合は、**データの回復**ウィザードで [**[別のサーバー]**](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) オプションを使用します。

### <a name="what-types-of-drives-can-i-back-up-files-and-folders-from-br"></a>ファイルとフォルダーのバックアップ元として、どのような種類のドライブを使用できますか。 <br/>
次のドライブやボリュームはバックアップできません。

* リムーバブル メディア: バックアップ項目のソースはすべて、固定として認識される必要があります。
* 読み取り専用ボリューム: ボリューム シャドウ コピー サービス (VSS) が機能するには、ボリュームが書き込み可能である必要があります。
* オフライン ボリューム: VSS が機能するには、ボリュームがオンラインである必要があります。
* ネットワーク共有: オンライン バックアップを使用してバックアップするボリュームは、サーバーに対してローカルである必要があります。
* Bitlocker で保護されているボリューム: バックアップを行う前に、ボリュームのロックを解除する必要があります。
* ファイル システムの識別: サポートされているファイル システムは NTFS だけです。

### <a name="what-file-and-folder-types-can-i-back-up-from-my-serverbr"></a>サーバーからバックアップできるのはどのような種類のファイルとフォルダーですか。<br/>
次の種類がサポートされています。

* 暗号化
* 圧縮
* スパース
* 圧縮 + スパース
* ハード リンク: サポートされていません。スキップされます。
* 再解析ポイント: サポートされていません。スキップされます。
* 暗号化 + スパース: サポートされていません。スキップされます。
* 圧縮されたストリーム: サポートされていません。スキップされます。
* スパース ストリーム: サポートされていません。スキップされます。

### <a name="what-is-the-maximum-file-path-length-that-can-be-specified-in-backup-policy-using-azure-backup-agent-br"></a>Azure Backup エージェントを使用するバックアップ ポリシーに指定できるファイル パスの最大長はどれくらいですか。 <br/>
Azure Backup エージェントでは NTFS が使用されています。 [ファイル パスの長さの指定は、Windows API による制限を受けます](https://msdn.microsoft.com/library/aa365247.aspx#fully_qualified_vs._relative_paths)。 保護するファイルのファイル パスが、Windows API で許容されている長さを超える場合は、親フォルダーまたはディスク ドライブをバックアップしてください。  

### <a name="what-characters-are-allowed-in-file-path-of-azure-backup-policy-using-azure-backup-agent-br"></a>Azure Backup エージェントを使用する Azure Backup ポリシーのファイル パスには、どのような文字を使用できますか。 <br>
 Azure Backup エージェントでは NTFS が使用されています。 そのため、ファイルの指定では [NTFS でサポートされている文字](https://msdn.microsoft.com/library/aa365247.aspx#naming_conventions) を使用できます。  

### <a name="how-do-i-change-the-cache-location-specified-for-the-azure-backup-agentbr"></a>Azure Backup エージェント用に指定されたキャッシュの場所を変更する方法を教えてください。<br/>
キャッシュの場所は、以下の手順で変更します。

* 管理者特権でのコマンド プロンプトで次のコマンドを実行して Backup エンジンを停止します。

```PS C:\> Net stop obengine```
* ファイルを移動するのではなく、 空き容量に余裕のある別のドライブにキャッシュ領域フォルダーをコピーします。 元のキャッシュ領域は、バックアップが新しいキャッシュ領域で正常に動作していることを確認した後で削除できます。
* 次のレジストリ エントリを、新しいキャッシュ領域のフォルダーへのパスで更新します。<br/>

| レジストリ パス | レジストリ キー | 値 |
| --- | --- | --- |
| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*新しいキャッシュ フォルダーの場所* |
| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*新しいキャッシュ フォルダーの場所* |

* 管理者特権でのコマンド プロンプトで次のコマンドを実行して Backup エンジンを再起動します。

```PS C:\> Net start obengine```

バックアップが新しいキャッシュ場所に正常に作成されたら、元のキャッシュ フォルダーを削除できます。

### <a name="where-can-i-put-the-cache-folder-for-the-azure-backup-agent-to-work-as-expectedbr"></a>適切に動作させるためには、Azure Backup エージェントのキャッシュ フォルダーをどこに設定すればよいですか。<br/>
次の場所へのキャッシュ フォルダーの設定はお勧めしません。

* ネットワーク共有またはリムーバブル メディア: キャッシュ フォルダーは、オンライン バックアップを使用したバックアップが必要なサーバーに対してローカルにする必要があります。 ネットワーク上の場所や USB ドライブなどのリムーバブル メディアはサポートされていません。
* オフライン ボリューム: キャッシュ フォルダーは、Azure Backup エージェントを使用したバックアップのために、オンラインにしておく必要があります。

### <a name="are-there-any-attributes-of-the-cache-folder-that-are-not-supportedbr"></a>キャッシュ フォルダーの属性としてサポートされていないものはありますか。<br/>
キャッシュ フォルダーでは、次の各属性またはそれらの組み合わせはサポートされていません。

* 暗号化
* 重複除去
* 圧縮
* スパース
* 再解析ポイント

キャッシュ フォルダーとメタデータ VHD には Azure Backup エージェントに必要な属性はありません。


## <a name="virtual-machines"></a>仮想マシン

### <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-already-backed-by-the-azure-backup-service-using-the-vm-extension-br"></a>VM 拡張機能を使用して Azure Backup サービスで既にバックアップされている Azure VM に Azure Backup エージェントをインストールすることはできますか。 <br/>
そして、 Azure Backup では、VM 拡張機能を使用している Azure VM には VM レベルのバックアップを提供します。 ゲスト Windows OS 上のファイルとフォルダーを保護するには、ゲスト Windows OS に Azure Backup エージェントをインストールします。

### <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-to-back-up-files-and-folders-present-on-temporary-storage-provided-by-the-azure-vm-br"></a>Azure VM に Azure Backup エージェントをインストールし、Azure VM で提供される一時ストレージ上のファイルとフォルダーをバックアップすることはできますか。 <br/>
はい。 ゲスト Windows OS に Azure Backup エージェントをインストールして、ファイルやフォルダーを一時的なストレージにバックアップすることはできますが、 一時的なストレージのデータが消去されるとバックアップ ジョブは失敗します。 また、一時的なストレージのデータが削除された場合、復元先に指定できるのは非揮発性ストレージのみになります。


## <a name="azure-backup-server-and-data-protection-manager"></a>Azure Backup Server と Data Protection Manager

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server-br"></a>Azure Backup Server を使用して、物理サーバーのベア メタル回復 (BMR) バックアップを作成できますか。 <br/>
はい。

### <a name="which-version-of-system-center-data-protection-manager-is-supported-br"></a>System Center Data Protection Manager は、どのバージョンがサポートされていますか。 <br/>
[最新版](http://aka.ms/azurebackup_agent)の Azure Backup エージェントを System Center Data Protection Manager (DPM) の最新の更新プログラムのロールアップ (UR) にインストールすることをお勧めします。 2016 年 8 月現在、更新プログラムのロールアップ 11 が最新の更新プログラムです。

### <a name="i-have-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-now-install-system-center-dpm-to-work-with-azure-backup-agent-to-protect-on-premises-applicationvm-workloads-to-azure-br"></a>ファイルとフォルダーを保護するために Azure Backup エージェントをインストールしました。 Azure に転送するオンプレミスのアプリケーション/VM ワークロードを保護するために、System Center DPM をインストールして Azure Backup エージェントと連携させることはできますか。 <br/>
System Center Data Protection Manager (DPM) と共に Azure Backup を使用するには、まず DPM をインストールしてから Azure Backup エージェントをインストールします。 この順序で Azure Backup のコンポーネントをインストールすることで、Azure Backup エージェントが DPM と連携します。 DPM をインストールする前に Azure Backup エージェントをインストールする方法は推奨されておらず、サポートもされていません。


## <a name="how-azure-backup-works"></a>Azure Backup の動作

### <a name="does-the-azure-backup-agent-work-on-a-server-that-uses-windows-server-2012-deduplication-br"></a>Windows Server 2012 の重複除去を使用するサーバーでは、Azure Backup エージェントは動作しますか。 <br/>
はい。 エージェント サービスは、バックアップ操作を準備するときに、重複除去されたデータを通常のデータに変換します。 そのデータをバックアップ用に最適化し、暗号化してから、オンライン バックアップ サービスに送信します。

### <a name="if-i-cancel-a-backup-job-once-it-has-started-is-the-transferred-backup-data-deleted-br"></a>バックアップを開始した後でバックアップ ジョブを取り消すと、転送されたバックアップ データは削除されますか。 <br/>
いいえ。 バックアップ ジョブを取り消す前にコンテナーに転送されたすべてのデータは、コンテナーに残ります。 Azure Backup では、チェックポイント メカニズムを使用して、バックアップ中に随時バックアップ データにチェックポイントを追加します。 バックアップ データにチェックポイントがあることで、次回のバックアップ処理でファイルの整合性を検証できます。 次のバックアップ ジョブは、これまでバックアップしたデータの増分になります。 増分バックアップでは、新しいデータまたは変更されたデータのみが転送され、帯域幅の使用状況が向上します。

Azure VM のバックアップ ジョブを取り消した場合、転送済みのデータは無視されます。 次のバックアップ ジョブでは、最後に成功したバックアップ ジョブから増分データが転送されます。

### <a name="if-a-backup-job-fails-can-i-configure-the-backup-service-to-send-e-mail-br"></a>バックアップ ジョブが失敗した場合にメールを送信するように Backup サービスを構成できますか。 <br/>
はい、Backup サービスには、PowerShell スクリプトで使用できるイベント ベースのアラートがいくつかあります。 詳細については、[通知の構成](backup-azure-monitor-vms.md#configure-notifications)に関するページをご覧ください。

### <a name="are-there-limits-on-when-or-how-many-times-a-backup-job-can-be-scheduledbr"></a>バックアップ ジョブをスケジュール設定できる時刻や回数に制限はありますか。<br/>
はい。 Windows Server または Windows ワークステーションでは、1 日に最大 3 回バックアップ ジョブを実行できます。 System Center DPM では、1 日に最大 2 回バックアップ ジョブを実行できます。 IaaS VM では、1 日に 1 回バックアップ ジョブを実行できます。 Windows Server または Windows ワークステーションのスケジューリング ポリシーを使用して毎日または毎週のスケジュールを指定することができます。 System Center DPM を使用した場合、スケジュールには毎日、毎週、毎月、毎年を指定できます。

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-i-backed-upbr"></a>Recovery Services コンテナーに転送されたデータのサイズが、バックアップしたデータよりも小さいのはなぜでしょうか。<br/>
 Azure Backup エージェント、SCDPM、または Azure Backup Server からバックアップしたすべてのデータは、圧縮および暗号化されてから転送されます。 圧縮と暗号化が適用されると、バックアップ コンテナー内のデータは 30 ～ 40% 小さくなります。

 ### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-by-the-backup-servicebr"></a>Backup サービスによって使用される帯域幅の量を調整する方法はありますか。<br/>
  はい。帯域幅の調整には、Backup エージェントの **[プロパティの変更]** オプションを使用します。 帯域幅の量と、帯域幅を使用する時間を調整できます。 具体的な手順については、「**[ネットワーク調整を有効にするには](backup-configure-vault.md#enable-network-throttling)**」を参照してください。



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



## <a name="retention-policy-and-recovery-points"></a>保持ポリシーと復旧ポイント

### <a name="is-there-a-difference-between-the-retention-policy-for-dpm-and-windows-serverclient-that-is-on-windows-server-without-dpmbr"></a>DPM と Windows Server/クライアント (DPM がインストールされていない Windows Server) の保持ポリシーには違いがありますか。<br/>
いいえ。DPM と Windows Server/クライアントのどちらの場合でも、リテンション期間ポリシーに毎日、毎週、毎月、毎年を指定できます。

### <a name="can-i-configure-my-retention-policies-selectively--ie-configure-weekly-and-daily-but-not-yearly-and-monthlybr"></a>週単位と日単位を指定し、年単位と月単位を指定しないなど、保持ポリシーを選択的に構成することはできますか。<br/>
はい。Azure Backup の保有構造では、要件に合わせて保有ポリシーを柔軟に定義できます。

### <a name="can-i-schedule-a-backup-at-6pm-and-specify-retention-policies-at-a-different-timebr"></a>午後 6 時に "バックアップのスケジュールを設定" し、別の時刻に保持ポリシーを指定することはできますか。<br/>
いいえ。 保有ポリシーは、バックアップ ポイントにのみ適用できます。 次の画像では、午前 0 時と午後 6 時にバックアップが行われるように保持ポリシーが指定されています。 <br/>

![バックアップのスケジュールと保有期間](./media/backup-azure-backup-faq/Schedule.png)
<br/>

### <a name="is-an-incremental-copy-transferred-for-the-retention-policies-scheduled-br"></a>スケジュールが設定されている保持ポリシーでは増分コピーは転送されますか。 <br/>
いいえ。増分コピーはバックアップ スケジュール ページに記載されている時刻に基づいて送信されます。 保持できるポイントは、保有ポリシーに基づいて決定されます。

### <a name="if-a-backup-is-retained-for-a-long-duration-does-it-take-more-time-to-recover-an-older-data-point-br"></a>バックアップを長期にわたって保持した場合、データ ポイントが古いほど復元に時間がかかるのでしょうか。 <br/>
いいえ。最古のデータ ポイントも最新のデータ ポイントも復元に要する時間は同じです。 それぞれの回復ポイントは、完全なポイントと同じように動作します。

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storagebr"></a>それぞれの回復ポイントが完全なポイントと同じように機能する場合、それは課金対象のバックアップ ストレージの合計に影響するのでしょうか。<br/>
一般的に、回復ポイントのリテンション期間が長い製品では、バックアップ データが完全なポイントとして格納されます。 完全なポイントは、ストレージ効率は *悪く* なりますが、簡単かつ迅速に復元できます。 一方で増分コピーは、ストレージ効率は *良く* なりますが、データのチェーンを復元する必要があり、復旧時間に影響します。 Azure Backup のストレージ アーキテクチャを使用すると、高速に復元するためにデータの格納を最適化し、ストレージ コストを低く抑えることで、両方の長所を生かすことができます。 このようなデータ ストレージ手法を通じて、送受信の帯域幅が効率的に使用されています。 データ ストレージの量とデータ回復にかかる時間がどちらも最小限に抑えられます。 増分バックアップによる効率的な保存のしくみの詳細については、 [こちら](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/) を参照してください。

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-createdbr"></a>作成できる回復ポイント数に制限はありますか。<br/>
保護されているインスタンスごとに作成できる復旧ポイントは最大 9,999 個です。 保護されているインスタンスとは、データを Azure にバックアップするように構成されているコンピューター、サーバー (物理または仮想)、またはワークロードです。 バックアップ資格情報コンテナーごとの保護されているインスタンスの数に制限はありません。 詳細については、「[バックアップと保持](./backup-introduction-to-azure-backup.md#backup-and-retention)」および「[保護されているインスタンスとは](./backup-introduction-to-azure-backup.md#what-is-a-protected-instance)」をご覧ください。

### <a name="how-many-recoveries-can-i-perform-on-the-data-that-is-backed-up-to-azurebr"></a>Azure にバックアップされたデータで実行できる回復の回数は何回ですか。<br/>
Azure Backup からの回復の数に制限はありません。

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure-br"></a>データを復元している間に発生する Azure からのエグレス トラフィックには料金が発生するのでしょうか。 <br/>
いいえ。 回復は無料ですので、送信トラフィックに対しては課金されません。

### <a name="i-receive-the-warning-azure-backups-have-not-been-configured-for-this-server-even-though-i-configured-a-backup-policy-br"></a>バックアップ ポリシーを設定してあるのに、"このサーバーに対して Azure Backups は構成されていません" という警告が表示されるのはなぜですか。 <br/>
ローカル サーバーに保存されているバックアップ スケジュールの設定がバックアップ コンテナーに格納されている設定と異なる場合、このような警告が表示されることがあります。 サーバーまたは設定が既知の正常な状態に復旧されると、バックアップ スケジュールの同期が失われることがあります。 この警告が表示された場合は、 [バックアップ ポリシーを再構成](backup-azure-manage-windows-server.md) した後、 **[今すぐバックアップ]** を実行して、ローカル サーバーと Azure を再同期してください。



## <a name="azure-backup-encryption"></a>Azure Backup の暗号化

### <a name="is-the-data-sent-to-azure-encrypted-br"></a>Azure に送信されるデータは暗号化されますか。 <br/>
はい。 データは、オンプレミスのサーバー / クライアント / SCDPM コンピューターで AES256 を使用して暗号化され、セキュリティで保護された HTTPS リンク経由で送信されます。

### <a name="is-the-backup-data-on-azure-encrypted-as-wellbr"></a>Azure 上のバックアップ データも暗号化されますか。<br/>
はい。 Azure に送信されるデータは、暗号化された状態で保存されます。 マイクロソフトは、どの時点でもバックアップ データの暗号化を解除しません。 Azure VM をバックアップする場合、Azure Backup では仮想マシンの暗号化が使用されます。 たとえば、Azure Disk Encryption や他の暗号化テクノロジを使用して VM が暗号化されている場合、Azure Backup はその暗号化を使用してデータをセキュリティ保護します。

### <a name="what-is-the-minimum-length-of-encryption-key-used-to-encrypt-backup-data-br"></a>バックアップ データの暗号化に使用される暗号化キーの最小の長さはどれくらいですか。 <br/>
暗号化キーは 16 文字以上にする必要があります。

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-or-can-microsoft-recover-the-data-br"></a>暗号化キーを紛失した場合はどうなりますか? 自分でデータを回復できますか。または Microsoft でデータを回復してもらうことはできますか。 <br/>
バックアップ データの暗号化に使用されるキーは、お客様のオンプレミスにのみ存在します。 マイクロソフトは Azure にコピーを保持していませんし、キーにもアクセスできません。 お客様がキーを紛失した場合、マイクロソフトはバックアップ データを回復できません。

